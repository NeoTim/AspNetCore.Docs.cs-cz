---
<span data-ttu-id="fa060-101">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="fa060-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fa060-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fa060-102">'Blazor'</span></span>
- <span data-ttu-id="fa060-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fa060-103">'Identity'</span></span>
- <span data-ttu-id="fa060-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fa060-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="fa060-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fa060-105">'Razor'</span></span>
- <span data-ttu-id="fa060-106">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="fa060-106">'SignalR' uid:</span></span> 

---
# <a name="host-aspnet-core-on-windows-with-iis"></a><span data-ttu-id="fa060-107">Hostování ASP.NET Core ve Windows se službou IIS</span><span class="sxs-lookup"><span data-stu-id="fa060-107">Host ASP.NET Core on Windows with IIS</span></span>

<!-- 

    NOTE FOR 5.0
    
    When making the 5.0 version of this topic, remove the Hosting Bundle
    direct download section from the (new) <5.0 & >2.2 version and modify 
    the text and heading for the *Earlier versions of the installer* 
    section. See the 2.2 version for an example.
    
-->

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="fa060-108">Kurz týkající se publikování ASP.NET Core aplikace na server služby IIS najdete v tématu <xref:tutorials/publish-to-iis> .</span><span class="sxs-lookup"><span data-stu-id="fa060-108">For a tutorial experience on publishing an ASP.NET Core app to an IIS server, see <xref:tutorials/publish-to-iis>.</span></span>

[<span data-ttu-id="fa060-109">Instalace hostující sady .NET Core</span><span class="sxs-lookup"><span data-stu-id="fa060-109">Install the .NET Core Hosting Bundle</span></span>](#install-the-net-core-hosting-bundle)

## <a name="supported-operating-systems"></a><span data-ttu-id="fa060-110">Podporované operační systémy</span><span class="sxs-lookup"><span data-stu-id="fa060-110">Supported operating systems</span></span>

<span data-ttu-id="fa060-111">Podporovány jsou následující operační systémy:</span><span class="sxs-lookup"><span data-stu-id="fa060-111">The following operating systems are supported:</span></span>

* <span data-ttu-id="fa060-112">Windows 7 nebo novější</span><span class="sxs-lookup"><span data-stu-id="fa060-112">Windows 7 or later</span></span>
* <span data-ttu-id="fa060-113">Windows Server 2012 R2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="fa060-113">Windows Server 2012 R2 or later</span></span>

<span data-ttu-id="fa060-114">[Http. sys Server](xref:fundamentals/servers/httpsys) (dříve označovaný jako weblisten) nefunguje v konfiguraci reverzního proxy serveru se službou IIS.</span><span class="sxs-lookup"><span data-stu-id="fa060-114">[HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener) doesn't work in a reverse proxy configuration with IIS.</span></span> <span data-ttu-id="fa060-115">Použijte [Server Kestrel](xref:fundamentals/servers/kestrel).</span><span class="sxs-lookup"><span data-stu-id="fa060-115">Use the [Kestrel server](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="fa060-116">Informace o hostování v Azure najdete v tématu <xref:host-and-deploy/azure-apps/index> .</span><span class="sxs-lookup"><span data-stu-id="fa060-116">For information on hosting in Azure, see <xref:host-and-deploy/azure-apps/index>.</span></span>

<span data-ttu-id="fa060-117">Pokyny k řešení potíží najdete v tématu <xref:test/troubleshoot> .</span><span class="sxs-lookup"><span data-stu-id="fa060-117">For troubleshooting guidance, see <xref:test/troubleshoot>.</span></span>

## <a name="supported-platforms"></a><span data-ttu-id="fa060-118">Podporované platformy</span><span class="sxs-lookup"><span data-stu-id="fa060-118">Supported platforms</span></span>

<span data-ttu-id="fa060-119">Podporují se aplikace publikované pro nasazení 32 (x86) nebo 64-bit (x64).</span><span class="sxs-lookup"><span data-stu-id="fa060-119">Apps published for 32-bit (x86) or 64-bit (x64) deployment are supported.</span></span> <span data-ttu-id="fa060-120">Nasazení 32 aplikace s 32 (x86) .NET Core SDK, pokud aplikace:</span><span class="sxs-lookup"><span data-stu-id="fa060-120">Deploy a 32-bit app with a 32-bit (x86) .NET Core SDK unless the app:</span></span>

* <span data-ttu-id="fa060-121">Vyžaduje větší dostupný adresní prostor virtuální paměti pro 64 aplikaci.</span><span class="sxs-lookup"><span data-stu-id="fa060-121">Requires the larger virtual memory address space available to a 64-bit app.</span></span>
* <span data-ttu-id="fa060-122">Vyžaduje větší velikost zásobníku služby IIS.</span><span class="sxs-lookup"><span data-stu-id="fa060-122">Requires the larger IIS stack size.</span></span>
* <span data-ttu-id="fa060-123">Má 64 nativní závislosti.</span><span class="sxs-lookup"><span data-stu-id="fa060-123">Has 64-bit native dependencies.</span></span>

<span data-ttu-id="fa060-124">Aplikace publikované pro 32 (x86) musí mít pro své fondy aplikací IIS povolené 32 bitů.</span><span class="sxs-lookup"><span data-stu-id="fa060-124">Apps published for 32-bit (x86) must have 32-bit enabled for their IIS Application Pools.</span></span> <span data-ttu-id="fa060-125">Další informace najdete v části [Vytvoření webu služby IIS](#create-the-iis-site) .</span><span class="sxs-lookup"><span data-stu-id="fa060-125">For more information, see the [Create the IIS site](#create-the-iis-site) section.</span></span>

<span data-ttu-id="fa060-126">K publikování 64 aplikace použijte 64 .NET Core SDK (x64).</span><span class="sxs-lookup"><span data-stu-id="fa060-126">Use a 64-bit (x64) .NET Core SDK to publish a 64-bit app.</span></span> <span data-ttu-id="fa060-127">V hostitelském systému musí být nainstalován 64 modul runtime.</span><span class="sxs-lookup"><span data-stu-id="fa060-127">A 64-bit runtime must be present on the host system.</span></span>

## <a name="hosting-models"></a><span data-ttu-id="fa060-128">Modely hostingu</span><span class="sxs-lookup"><span data-stu-id="fa060-128">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="fa060-129">Model hostování v procesu</span><span class="sxs-lookup"><span data-stu-id="fa060-129">In-process hosting model</span></span>

<span data-ttu-id="fa060-130">Při použití hostování v rámci procesu ASP.NET Core aplikace běží ve stejném procesu jako jeho pracovní proces služby IIS.</span><span class="sxs-lookup"><span data-stu-id="fa060-130">Using in-process hosting, an ASP.NET Core app runs in the same process as its IIS worker process.</span></span> <span data-ttu-id="fa060-131">Hostování v rámci procesů poskytují lepší výkon než hostování mimo procesy, protože požadavky nejsou proxy serverem přes adaptér zpětné smyčky, síťové rozhraní, které vrátí odchozí síťový provoz zpátky do stejného počítače.</span><span class="sxs-lookup"><span data-stu-id="fa060-131">In-process hosting provides improved performance over out-of-process hosting because requests aren't proxied over the loopback adapter, a network interface that returns outgoing network traffic back to the same machine.</span></span> <span data-ttu-id="fa060-132">Služba IIS zpracovává správu procesů pomocí [aktivační služby procesů systému Windows (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span><span class="sxs-lookup"><span data-stu-id="fa060-132">IIS handles process management with the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="fa060-133">[Modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module):</span><span class="sxs-lookup"><span data-stu-id="fa060-133">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module):</span></span>

* <span data-ttu-id="fa060-134">Provede inicializaci aplikace.</span><span class="sxs-lookup"><span data-stu-id="fa060-134">Performs app initialization.</span></span>
  * <span data-ttu-id="fa060-135">Načte [CoreCLR](/dotnet/standard/glossary#coreclr).</span><span class="sxs-lookup"><span data-stu-id="fa060-135">Loads the [CoreCLR](/dotnet/standard/glossary#coreclr).</span></span>
  * <span data-ttu-id="fa060-136">Volání `Program.Main` .</span><span class="sxs-lookup"><span data-stu-id="fa060-136">Calls `Program.Main`.</span></span>
* <span data-ttu-id="fa060-137">Zpracovává životnost nativního požadavku služby IIS.</span><span class="sxs-lookup"><span data-stu-id="fa060-137">Handles the lifetime of the IIS native request.</span></span>

<span data-ttu-id="fa060-138">Následující diagram znázorňuje vztah mezi službou IIS, modulem ASP.NET Core a aplikací hostovanou v procesu:</span><span class="sxs-lookup"><span data-stu-id="fa060-138">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted in-process:</span></span>

![ASP.NET Core modul ve scénáři hostování v rámci procesu](index/_static/ancm-inprocess.png)

1. <span data-ttu-id="fa060-140">Požadavek přijde z webu do ovladače HTTP. sys v režimu jádra.</span><span class="sxs-lookup"><span data-stu-id="fa060-140">A request arrives from the web to the kernel-mode HTTP.sys driver.</span></span>
1. <span data-ttu-id="fa060-141">Ovladač směruje nativní požadavek na IIS na konfigurovaném portu webu, obvykle 80 (HTTP) nebo 443 (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="fa060-141">The driver routes the native request to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span>
1. <span data-ttu-id="fa060-142">Modul ASP.NET Core obdrží nativní požadavek a předá ho k serveru HTTP služby IIS ( `IISHttpServer` ).</span><span class="sxs-lookup"><span data-stu-id="fa060-142">The ASP.NET Core Module receives the native request and passes it to IIS HTTP Server (`IISHttpServer`).</span></span> <span data-ttu-id="fa060-143">HTTP server IIS je vnitroprocesové implementace v rámci procesového serveru pro službu IIS, která převádí požadavek z nativního na spravovanou.</span><span class="sxs-lookup"><span data-stu-id="fa060-143">IIS HTTP Server is an in-process server implementation for IIS that converts the request from native to managed.</span></span>

<span data-ttu-id="fa060-144">Poté, co server HTTP služby IIS zpracuje požadavek:</span><span class="sxs-lookup"><span data-stu-id="fa060-144">After the IIS HTTP Server processes the request:</span></span>

1. <span data-ttu-id="fa060-145">Požadavek se odešle do kanálu middleware ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="fa060-145">The request is sent to the ASP.NET Core middleware pipeline.</span></span>
1. <span data-ttu-id="fa060-146">Kanál middlewaru zpracovává požadavek a předá ho jako `HttpContext` instanci do logiky aplikace.</span><span class="sxs-lookup"><span data-stu-id="fa060-146">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span>
1. <span data-ttu-id="fa060-147">Odpověď aplikace se předává zpět službě IIS prostřednictvím serveru IIS HTTP.</span><span class="sxs-lookup"><span data-stu-id="fa060-147">The app's response is passed back to IIS through IIS HTTP Server.</span></span>
1. <span data-ttu-id="fa060-148">Služba IIS odešle odpověď klientovi, který žádost inicioval.</span><span class="sxs-lookup"><span data-stu-id="fa060-148">IIS sends the response to the client that initiated the request.</span></span>

<span data-ttu-id="fa060-149">Hostování v procesu je výslovný souhlas pro existující aplikace.</span><span class="sxs-lookup"><span data-stu-id="fa060-149">In-process hosting is opt-in for existing apps.</span></span> <span data-ttu-id="fa060-150">Webové šablony ASP.NET Core používají model hostování v rámci procesu.</span><span class="sxs-lookup"><span data-stu-id="fa060-150">The ASP.NET Core web templates use the in-process hosting model.</span></span>

<span data-ttu-id="fa060-151">`CreateDefaultBuilder`přidá <xref:Microsoft.AspNetCore.Hosting.Server.IServer> instanci voláním <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> metody pro spuštění [CoreCLR](/dotnet/standard/glossary#coreclr) a hostování aplikace uvnitř pracovního procesu služby IIS (*W3wp. exe* nebo *IISExpress. exe*).</span><span class="sxs-lookup"><span data-stu-id="fa060-151">`CreateDefaultBuilder` adds an <xref:Microsoft.AspNetCore.Hosting.Server.IServer> instance by calling the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> method to boot the [CoreCLR](/dotnet/standard/glossary#coreclr) and host the app inside of the IIS worker process (*w3wp.exe* or *iisexpress.exe*).</span></span> <span data-ttu-id="fa060-152">Testy výkonu označují, že hostování aplikace .NET Core v rámci procesu přináší výrazně vyšší propustnost žádostí v porovnání s hostováním aplikace mimo proces a požadavky na proxy server do [Kestrel](xref:fundamentals/servers/kestrel).</span><span class="sxs-lookup"><span data-stu-id="fa060-152">Performance tests indicate that hosting a .NET Core app in-process delivers significantly higher request throughput compared to hosting the app out-of-process and proxying requests to [Kestrel](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="fa060-153">Aplikace publikované jako spustitelný soubor s jedním souborem nejde načíst pomocí modelu hostování v rámci procesu.</span><span class="sxs-lookup"><span data-stu-id="fa060-153">Apps published as a single file executable can't be loaded by the in-process hosting model.</span></span>

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="fa060-154">Model hostování mimo proces</span><span class="sxs-lookup"><span data-stu-id="fa060-154">Out-of-process hosting model</span></span>

<span data-ttu-id="fa060-155">Vzhledem k tomu, že ASP.NET Core aplikace běží v procesu odděleném od pracovního procesu služby IIS, modul ASP.NET Core zpracovává správu procesů.</span><span class="sxs-lookup"><span data-stu-id="fa060-155">Because ASP.NET Core apps run in a process separate from the IIS worker process, the ASP.NET Core Module handles process management.</span></span> <span data-ttu-id="fa060-156">Modul spustí proces pro aplikaci ASP.NET Core, když první požadavek dorazí a restartuje aplikaci, pokud se ukončí nebo dojde k chybě.</span><span class="sxs-lookup"><span data-stu-id="fa060-156">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="fa060-157">To je v podstatě stejné chování jako u aplikací, které běží v procesu, které jsou spravovány službou was [(Windows Process Activation Service)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span><span class="sxs-lookup"><span data-stu-id="fa060-157">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="fa060-158">Následující diagram znázorňuje vztah mezi službou IIS, modulem ASP.NET Core a aplikací hostovanou mimo proces:</span><span class="sxs-lookup"><span data-stu-id="fa060-158">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted out-of-process:</span></span>

![ASP.NET Core modul ve scénáři hostování mimo proces](index/_static/ancm-outofprocess.png)

1. <span data-ttu-id="fa060-160">Požadavky přicházející z webu do ovladače HTTP. sys v režimu jádra.</span><span class="sxs-lookup"><span data-stu-id="fa060-160">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span>
1. <span data-ttu-id="fa060-161">Ovladač směruje požadavky do služby IIS na konfigurovaném portu webu.</span><span class="sxs-lookup"><span data-stu-id="fa060-161">The driver routes the requests to IIS on the website's configured port.</span></span> <span data-ttu-id="fa060-162">Nakonfigurovaný port je obvykle 80 (HTTP) nebo 443 (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="fa060-162">The configured port is usually 80 (HTTP) or 443 (HTTPS).</span></span>
1. <span data-ttu-id="fa060-163">Modul předá požadavky do Kestrel na náhodném portu pro aplikaci.</span><span class="sxs-lookup"><span data-stu-id="fa060-163">The module forwards the requests to Kestrel on a random port for the app.</span></span> <span data-ttu-id="fa060-164">Náhodný port není 80 nebo 443.</span><span class="sxs-lookup"><span data-stu-id="fa060-164">The random port isn't 80 or 443.</span></span>

<!-- make this a bullet list -->
<span data-ttu-id="fa060-165">Modul ASP.NET Core Určuje port prostřednictvím proměnné prostředí při spuštění.</span><span class="sxs-lookup"><span data-stu-id="fa060-165">The ASP.NET Core Module specifies the port via an environment variable at startup.</span></span> <span data-ttu-id="fa060-166"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*>Rozšíření nakonfiguruje server tak, aby naslouchal `http://localhost:{PORT}` .</span><span class="sxs-lookup"><span data-stu-id="fa060-166">The <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> extension configures the server to listen on `http://localhost:{PORT}`.</span></span> <span data-ttu-id="fa060-167">Budou provedeny další kontroly a požadavky, které nepocházejí z modulu, jsou odmítnuty.</span><span class="sxs-lookup"><span data-stu-id="fa060-167">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="fa060-168">Modul nepodporuje předávání HTTPS.</span><span class="sxs-lookup"><span data-stu-id="fa060-168">The module doesn't support HTTPS forwarding.</span></span> <span data-ttu-id="fa060-169">Požadavky jsou předávány přes protokol HTTP i v případě, že jsou přijímány službou IIS prostřednictvím protokolu HTTPS.</span><span class="sxs-lookup"><span data-stu-id="fa060-169">Requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="fa060-170">Jakmile Kestrel požadavek z modulu přijme, požadavek se přepošle do kanálu middleware ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="fa060-170">After Kestrel picks up the request from the module, the request is forwarded into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="fa060-171">Kanál middlewaru zpracovává požadavek a předá ho jako `HttpContext` instanci do logiky aplikace.</span><span class="sxs-lookup"><span data-stu-id="fa060-171">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="fa060-172">Middleware přidaný integrací služby IIS: aktualizace schématu, vzdálené IP adresy a pathbase pro přesměrování požadavku do Kestrel.</span><span class="sxs-lookup"><span data-stu-id="fa060-172">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="fa060-173">Odpověď aplikace se předává zpátky do služby IIS, která je předává zpátky klientovi HTTP, který žádost inicioval.</span><span class="sxs-lookup"><span data-stu-id="fa060-173">The app's response is passed back to IIS, which forwards it back to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="fa060-174">Pokyny ke konfiguraci ASP.NET Core modulu najdete v tématu <xref:host-and-deploy/aspnet-core-module> .</span><span class="sxs-lookup"><span data-stu-id="fa060-174">For ASP.NET Core Module configuration guidance, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="fa060-175">Další informace o hostování najdete v tématu [hostitel v ASP.NET Core](xref:fundamentals/index#host).</span><span class="sxs-lookup"><span data-stu-id="fa060-175">For more information on hosting, see [Host in ASP.NET Core](xref:fundamentals/index#host).</span></span>

## <a name="application-configuration"></a><span data-ttu-id="fa060-176">Konfigurace aplikací</span><span class="sxs-lookup"><span data-stu-id="fa060-176">Application configuration</span></span>

### <a name="enable-the-iisintegration-components"></a><span data-ttu-id="fa060-177">Povolit komponenty IISIntegration</span><span class="sxs-lookup"><span data-stu-id="fa060-177">Enable the IISIntegration components</span></span>

<span data-ttu-id="fa060-178">Při sestavování hostitele v `CreateHostBuilder` (*program.cs*) zavolejte <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> na Povolit integraci služby IIS:</span><span class="sxs-lookup"><span data-stu-id="fa060-178">When building a host in `CreateHostBuilder` (*Program.cs*), call <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> to enable IIS integration:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        ...
```

<span data-ttu-id="fa060-179">Další informace o `CreateDefaultBuilder` naleznete v tématu <xref:fundamentals/host/generic-host#default-builder-settings> .</span><span class="sxs-lookup"><span data-stu-id="fa060-179">For more information on `CreateDefaultBuilder`, see <xref:fundamentals/host/generic-host#default-builder-settings>.</span></span>

### <a name="iis-options"></a><span data-ttu-id="fa060-180">Možnosti služby IIS</span><span class="sxs-lookup"><span data-stu-id="fa060-180">IIS options</span></span>

<span data-ttu-id="fa060-181">**Model hostování v procesu**</span><span class="sxs-lookup"><span data-stu-id="fa060-181">**In-process hosting model**</span></span>

<span data-ttu-id="fa060-182">Pokud chcete nakonfigurovat možnosti serveru IIS, zahrňte do nástroje konfiguraci služby pro <xref:Microsoft.AspNetCore.Builder.IISServerOptions> <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*> .</span><span class="sxs-lookup"><span data-stu-id="fa060-182">To configure IIS Server options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISServerOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>.</span></span> <span data-ttu-id="fa060-183">Následující příklad zakáže AutomaticAuthentication:</span><span class="sxs-lookup"><span data-stu-id="fa060-183">The following example disables AutomaticAuthentication:</span></span>

```csharp
services.Configure<IISServerOptions>(options => 
{
    options.AutomaticAuthentication = false;
});
```

| <span data-ttu-id="fa060-184">Možnost</span><span class="sxs-lookup"><span data-stu-id="fa060-184">Option</span></span>                         | <span data-ttu-id="fa060-185">Výchozí</span><span class="sxs-lookup"><span data-stu-id="fa060-185">Default</span></span> | <span data-ttu-id="fa060-186">Nastavení</span><span class="sxs-lookup"><span data-stu-id="fa060-186">Setting</span></span> |
| ---
<span data-ttu-id="fa060-187">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="fa060-187">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fa060-188">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fa060-188">'Blazor'</span></span>
- <span data-ttu-id="fa060-189">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fa060-189">'Identity'</span></span>
- <span data-ttu-id="fa060-190">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fa060-190">'Let's Encrypt'</span></span>
- <span data-ttu-id="fa060-191">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fa060-191">'Razor'</span></span>
- <span data-ttu-id="fa060-192">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="fa060-192">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fa060-193">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="fa060-193">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fa060-194">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fa060-194">'Blazor'</span></span>
- <span data-ttu-id="fa060-195">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fa060-195">'Identity'</span></span>
- <span data-ttu-id="fa060-196">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fa060-196">'Let's Encrypt'</span></span>
- <span data-ttu-id="fa060-197">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fa060-197">'Razor'</span></span>
- <span data-ttu-id="fa060-198">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="fa060-198">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fa060-199">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="fa060-199">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fa060-200">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fa060-200">'Blazor'</span></span>
- <span data-ttu-id="fa060-201">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fa060-201">'Identity'</span></span>
- <span data-ttu-id="fa060-202">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fa060-202">'Let's Encrypt'</span></span>
- <span data-ttu-id="fa060-203">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fa060-203">'Razor'</span></span>
- <span data-ttu-id="fa060-204">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="fa060-204">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fa060-205">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="fa060-205">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fa060-206">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fa060-206">'Blazor'</span></span>
- <span data-ttu-id="fa060-207">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fa060-207">'Identity'</span></span>
- <span data-ttu-id="fa060-208">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fa060-208">'Let's Encrypt'</span></span>
- <span data-ttu-id="fa060-209">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fa060-209">'Razor'</span></span>
- <span data-ttu-id="fa060-210">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="fa060-210">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fa060-211">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="fa060-211">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fa060-212">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fa060-212">'Blazor'</span></span>
- <span data-ttu-id="fa060-213">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fa060-213">'Identity'</span></span>
- <span data-ttu-id="fa060-214">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fa060-214">'Let's Encrypt'</span></span>
- <span data-ttu-id="fa060-215">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fa060-215">'Razor'</span></span>
- <span data-ttu-id="fa060-216">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="fa060-216">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fa060-217">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="fa060-217">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fa060-218">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fa060-218">'Blazor'</span></span>
- <span data-ttu-id="fa060-219">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fa060-219">'Identity'</span></span>
- <span data-ttu-id="fa060-220">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fa060-220">'Let's Encrypt'</span></span>
- <span data-ttu-id="fa060-221">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fa060-221">'Razor'</span></span>
- <span data-ttu-id="fa060-222">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="fa060-222">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fa060-223">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="fa060-223">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fa060-224">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fa060-224">'Blazor'</span></span>
- <span data-ttu-id="fa060-225">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fa060-225">'Identity'</span></span>
- <span data-ttu-id="fa060-226">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fa060-226">'Let's Encrypt'</span></span>
- <span data-ttu-id="fa060-227">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fa060-227">'Razor'</span></span>
- <span data-ttu-id="fa060-228">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="fa060-228">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fa060-229">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="fa060-229">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fa060-230">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fa060-230">'Blazor'</span></span>
- <span data-ttu-id="fa060-231">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fa060-231">'Identity'</span></span>
- <span data-ttu-id="fa060-232">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fa060-232">'Let's Encrypt'</span></span>
- <span data-ttu-id="fa060-233">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fa060-233">'Razor'</span></span>
- <span data-ttu-id="fa060-234">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="fa060-234">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fa060-235">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="fa060-235">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fa060-236">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fa060-236">'Blazor'</span></span>
- <span data-ttu-id="fa060-237">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fa060-237">'Identity'</span></span>
- <span data-ttu-id="fa060-238">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fa060-238">'Let's Encrypt'</span></span>
- <span data-ttu-id="fa060-239">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fa060-239">'Razor'</span></span>
- <span data-ttu-id="fa060-240">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="fa060-240">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fa060-241">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="fa060-241">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fa060-242">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fa060-242">'Blazor'</span></span>
- <span data-ttu-id="fa060-243">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fa060-243">'Identity'</span></span>
- <span data-ttu-id="fa060-244">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fa060-244">'Let's Encrypt'</span></span>
- <span data-ttu-id="fa060-245">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fa060-245">'Razor'</span></span>
- <span data-ttu-id="fa060-246">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="fa060-246">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fa060-247">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="fa060-247">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fa060-248">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fa060-248">'Blazor'</span></span>
- <span data-ttu-id="fa060-249">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fa060-249">'Identity'</span></span>
- <span data-ttu-id="fa060-250">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fa060-250">'Let's Encrypt'</span></span>
- <span data-ttu-id="fa060-251">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fa060-251">'Razor'</span></span>
- <span data-ttu-id="fa060-252">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="fa060-252">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fa060-253">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="fa060-253">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fa060-254">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fa060-254">'Blazor'</span></span>
- <span data-ttu-id="fa060-255">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fa060-255">'Identity'</span></span>
- <span data-ttu-id="fa060-256">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fa060-256">'Let's Encrypt'</span></span>
- <span data-ttu-id="fa060-257">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fa060-257">'Razor'</span></span>
- <span data-ttu-id="fa060-258">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="fa060-258">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fa060-259">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="fa060-259">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fa060-260">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fa060-260">'Blazor'</span></span>
- <span data-ttu-id="fa060-261">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fa060-261">'Identity'</span></span>
- <span data-ttu-id="fa060-262">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fa060-262">'Let's Encrypt'</span></span>
- <span data-ttu-id="fa060-263">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fa060-263">'Razor'</span></span>
- <span data-ttu-id="fa060-264">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="fa060-264">'SignalR' uid:</span></span> 

<span data-ttu-id="fa060-265">--------------- | :-----: | ---Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="fa060-265">--------------- | :-----: | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fa060-266">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fa060-266">'Blazor'</span></span>
- <span data-ttu-id="fa060-267">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fa060-267">'Identity'</span></span>
- <span data-ttu-id="fa060-268">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fa060-268">'Let's Encrypt'</span></span>
- <span data-ttu-id="fa060-269">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fa060-269">'Razor'</span></span>
- <span data-ttu-id="fa060-270">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="fa060-270">'SignalR' uid:</span></span> 

<span data-ttu-id="fa060-271">---- | | `AutomaticAuthentication`      | `true`  | Pokud `true` Server IIS nastaví `HttpContext.User` ověřený [ověřováním systému Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="fa060-271">---- | | `AutomaticAuthentication`      | `true`  | If `true`, IIS Server sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="fa060-272">Pokud `false` Server poskytuje pouze identitu pro `HttpContext.User` a reaguje na výzvy, pokud je explicitně vyžádala `AuthenticationScheme` .</span><span class="sxs-lookup"><span data-stu-id="fa060-272">If `false`, the server only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="fa060-273">Aby mohla služba fungovat, musí být ve službě IIS povolená možnost ověřování systému Windows `AutomaticAuthentication` .</span><span class="sxs-lookup"><span data-stu-id="fa060-273">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="fa060-274">Další informace najdete v tématu [ověřování systému Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="fa060-274">For more information, see [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="fa060-275">| | `AuthenticationDisplayName`    | `null`  | Nastaví zobrazovaný název, který se zobrazí uživatelům na přihlašovacích stránkách.</span><span class="sxs-lookup"><span data-stu-id="fa060-275">| | `AuthenticationDisplayName`    | `null`  | Sets the display name shown to users on login pages.</span></span> <span data-ttu-id="fa060-276">| | `AllowSynchronousIO`           | `false` | Zda je povolena synchronní I/O pro `HttpContext.Request` a `HttpContext.Response` .</span><span class="sxs-lookup"><span data-stu-id="fa060-276">| | `AllowSynchronousIO`           | `false` | Whether synchronous I/O is allowed for the `HttpContext.Request` and the `HttpContext.Response`.</span></span> <span data-ttu-id="fa060-277">| | `MaxRequestBodySize`           | `30000000`  | Získá nebo nastaví velikost textu maximálního požadavku pro `HttpRequest` .</span><span class="sxs-lookup"><span data-stu-id="fa060-277">| | `MaxRequestBodySize`           | `30000000`  | Gets or sets the max request body size for the `HttpRequest`.</span></span> <span data-ttu-id="fa060-278">Všimněte si, že samotná služba IIS má limit, `maxAllowedContentLength` který bude zpracován před `MaxRequestBodySize` nastavením v `IISServerOptions` .</span><span class="sxs-lookup"><span data-stu-id="fa060-278">Note that IIS itself has the limit `maxAllowedContentLength` which will be processed before the `MaxRequestBodySize` set in the `IISServerOptions`.</span></span> <span data-ttu-id="fa060-279">Změna `MaxRequestBodySize` neovlivní `maxAllowedContentLength` .</span><span class="sxs-lookup"><span data-stu-id="fa060-279">Changing the `MaxRequestBodySize` won't affect the `maxAllowedContentLength`.</span></span> <span data-ttu-id="fa060-280">Chcete-li zvýšit `maxAllowedContentLength` , přidejte položku do *souboru Web. config* a nastavte `maxAllowedContentLength` ji na vyšší hodnotu.</span><span class="sxs-lookup"><span data-stu-id="fa060-280">To increase `maxAllowedContentLength`, add an entry in the *web.config* to set `maxAllowedContentLength` to a higher value.</span></span> <span data-ttu-id="fa060-281">Další podrobnosti najdete v tématu [Konfigurace](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/#configuration).</span><span class="sxs-lookup"><span data-stu-id="fa060-281">For more details, see [Configuration](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/#configuration).</span></span> |

<span data-ttu-id="fa060-282">**Model hostování mimo proces**</span><span class="sxs-lookup"><span data-stu-id="fa060-282">**Out-of-process hosting model**</span></span>

<span data-ttu-id="fa060-283">Pokud chcete nakonfigurovat možnosti služby IIS, zahrňte do nástroje konfiguraci služby pro <xref:Microsoft.AspNetCore.Builder.IISOptions> <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*> .</span><span class="sxs-lookup"><span data-stu-id="fa060-283">To configure IIS options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>.</span></span> <span data-ttu-id="fa060-284">Následující příklad zabrání aplikaci v naplnění `HttpContext.Connection.ClientCertificate` :</span><span class="sxs-lookup"><span data-stu-id="fa060-284">The following example prevents the app from populating `HttpContext.Connection.ClientCertificate`:</span></span>

```csharp
services.Configure<IISOptions>(options => 
{
    options.ForwardClientCertificate = false;
});
```

| <span data-ttu-id="fa060-285">Možnost</span><span class="sxs-lookup"><span data-stu-id="fa060-285">Option</span></span>                         | <span data-ttu-id="fa060-286">Výchozí</span><span class="sxs-lookup"><span data-stu-id="fa060-286">Default</span></span> | <span data-ttu-id="fa060-287">Nastavení</span><span class="sxs-lookup"><span data-stu-id="fa060-287">Setting</span></span> |
| ---
<span data-ttu-id="fa060-288">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="fa060-288">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fa060-289">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fa060-289">'Blazor'</span></span>
- <span data-ttu-id="fa060-290">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fa060-290">'Identity'</span></span>
- <span data-ttu-id="fa060-291">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fa060-291">'Let's Encrypt'</span></span>
- <span data-ttu-id="fa060-292">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fa060-292">'Razor'</span></span>
- <span data-ttu-id="fa060-293">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="fa060-293">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fa060-294">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="fa060-294">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fa060-295">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fa060-295">'Blazor'</span></span>
- <span data-ttu-id="fa060-296">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fa060-296">'Identity'</span></span>
- <span data-ttu-id="fa060-297">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fa060-297">'Let's Encrypt'</span></span>
- <span data-ttu-id="fa060-298">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fa060-298">'Razor'</span></span>
- <span data-ttu-id="fa060-299">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="fa060-299">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fa060-300">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="fa060-300">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fa060-301">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fa060-301">'Blazor'</span></span>
- <span data-ttu-id="fa060-302">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fa060-302">'Identity'</span></span>
- <span data-ttu-id="fa060-303">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fa060-303">'Let's Encrypt'</span></span>
- <span data-ttu-id="fa060-304">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fa060-304">'Razor'</span></span>
- <span data-ttu-id="fa060-305">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="fa060-305">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fa060-306">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="fa060-306">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fa060-307">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fa060-307">'Blazor'</span></span>
- <span data-ttu-id="fa060-308">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fa060-308">'Identity'</span></span>
- <span data-ttu-id="fa060-309">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fa060-309">'Let's Encrypt'</span></span>
- <span data-ttu-id="fa060-310">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fa060-310">'Razor'</span></span>
- <span data-ttu-id="fa060-311">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="fa060-311">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fa060-312">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="fa060-312">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fa060-313">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fa060-313">'Blazor'</span></span>
- <span data-ttu-id="fa060-314">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fa060-314">'Identity'</span></span>
- <span data-ttu-id="fa060-315">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fa060-315">'Let's Encrypt'</span></span>
- <span data-ttu-id="fa060-316">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fa060-316">'Razor'</span></span>
- <span data-ttu-id="fa060-317">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="fa060-317">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fa060-318">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="fa060-318">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fa060-319">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fa060-319">'Blazor'</span></span>
- <span data-ttu-id="fa060-320">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fa060-320">'Identity'</span></span>
- <span data-ttu-id="fa060-321">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fa060-321">'Let's Encrypt'</span></span>
- <span data-ttu-id="fa060-322">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fa060-322">'Razor'</span></span>
- <span data-ttu-id="fa060-323">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="fa060-323">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fa060-324">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="fa060-324">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fa060-325">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fa060-325">'Blazor'</span></span>
- <span data-ttu-id="fa060-326">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fa060-326">'Identity'</span></span>
- <span data-ttu-id="fa060-327">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fa060-327">'Let's Encrypt'</span></span>
- <span data-ttu-id="fa060-328">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fa060-328">'Razor'</span></span>
- <span data-ttu-id="fa060-329">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="fa060-329">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fa060-330">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="fa060-330">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fa060-331">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fa060-331">'Blazor'</span></span>
- <span data-ttu-id="fa060-332">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fa060-332">'Identity'</span></span>
- <span data-ttu-id="fa060-333">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fa060-333">'Let's Encrypt'</span></span>
- <span data-ttu-id="fa060-334">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fa060-334">'Razor'</span></span>
- <span data-ttu-id="fa060-335">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="fa060-335">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fa060-336">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="fa060-336">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fa060-337">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fa060-337">'Blazor'</span></span>
- <span data-ttu-id="fa060-338">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fa060-338">'Identity'</span></span>
- <span data-ttu-id="fa060-339">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fa060-339">'Let's Encrypt'</span></span>
- <span data-ttu-id="fa060-340">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fa060-340">'Razor'</span></span>
- <span data-ttu-id="fa060-341">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="fa060-341">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fa060-342">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="fa060-342">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fa060-343">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fa060-343">'Blazor'</span></span>
- <span data-ttu-id="fa060-344">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fa060-344">'Identity'</span></span>
- <span data-ttu-id="fa060-345">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fa060-345">'Let's Encrypt'</span></span>
- <span data-ttu-id="fa060-346">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fa060-346">'Razor'</span></span>
- <span data-ttu-id="fa060-347">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="fa060-347">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fa060-348">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="fa060-348">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fa060-349">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fa060-349">'Blazor'</span></span>
- <span data-ttu-id="fa060-350">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fa060-350">'Identity'</span></span>
- <span data-ttu-id="fa060-351">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fa060-351">'Let's Encrypt'</span></span>
- <span data-ttu-id="fa060-352">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fa060-352">'Razor'</span></span>
- <span data-ttu-id="fa060-353">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="fa060-353">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fa060-354">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="fa060-354">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fa060-355">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fa060-355">'Blazor'</span></span>
- <span data-ttu-id="fa060-356">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fa060-356">'Identity'</span></span>
- <span data-ttu-id="fa060-357">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fa060-357">'Let's Encrypt'</span></span>
- <span data-ttu-id="fa060-358">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fa060-358">'Razor'</span></span>
- <span data-ttu-id="fa060-359">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="fa060-359">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fa060-360">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="fa060-360">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fa060-361">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fa060-361">'Blazor'</span></span>
- <span data-ttu-id="fa060-362">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fa060-362">'Identity'</span></span>
- <span data-ttu-id="fa060-363">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fa060-363">'Let's Encrypt'</span></span>
- <span data-ttu-id="fa060-364">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fa060-364">'Razor'</span></span>
- <span data-ttu-id="fa060-365">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="fa060-365">'SignalR' uid:</span></span> 

<span data-ttu-id="fa060-366">--------------- | :-----: | ---Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="fa060-366">--------------- | :-----: | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fa060-367">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fa060-367">'Blazor'</span></span>
- <span data-ttu-id="fa060-368">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fa060-368">'Identity'</span></span>
- <span data-ttu-id="fa060-369">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fa060-369">'Let's Encrypt'</span></span>
- <span data-ttu-id="fa060-370">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fa060-370">'Razor'</span></span>
- <span data-ttu-id="fa060-371">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="fa060-371">'SignalR' uid:</span></span> 

<span data-ttu-id="fa060-372">---- | | `AutomaticAuthentication`      | `true`  | Pokud `true` [Služba IIS Integration middleware](#enable-the-iisintegration-components) nastaví ověření `HttpContext.User` [ověřováním systému Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="fa060-372">---- | | `AutomaticAuthentication`      | `true`  | If `true`, [IIS Integration Middleware](#enable-the-iisintegration-components) sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="fa060-373">`false`V případě, middleware poskytuje pouze identitu pro `HttpContext.User` a reaguje na výzvy, pokud je explicitně vyžádala `AuthenticationScheme` .</span><span class="sxs-lookup"><span data-stu-id="fa060-373">If `false`, the middleware only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="fa060-374">Aby mohla služba fungovat, musí být ve službě IIS povolená možnost ověřování systému Windows `AutomaticAuthentication` .</span><span class="sxs-lookup"><span data-stu-id="fa060-374">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="fa060-375">Další informace najdete v tématu [ověřování systému Windows](xref:security/authentication/windowsauth) .</span><span class="sxs-lookup"><span data-stu-id="fa060-375">For more information, see the [Windows Authentication](xref:security/authentication/windowsauth) topic.</span></span> <span data-ttu-id="fa060-376">| | `AuthenticationDisplayName`    | `null`  | Nastaví zobrazovaný název, který se zobrazí uživatelům na přihlašovacích stránkách.</span><span class="sxs-lookup"><span data-stu-id="fa060-376">| | `AuthenticationDisplayName`    | `null`  | Sets the display name shown to users on login pages.</span></span> <span data-ttu-id="fa060-377">| | `ForwardClientCertificate`     | `true`  | Pokud `true` je k `MS-ASPNETCORE-CLIENTCERT` dispozici hlavička žádosti, `HttpContext.Connection.ClientCertificate` je naplněna hodnota.</span><span class="sxs-lookup"><span data-stu-id="fa060-377">| | `ForwardClientCertificate`     | `true`  | If `true` and the `MS-ASPNETCORE-CLIENTCERT` request header is present, the `HttpContext.Connection.ClientCertificate` is populated.</span></span> |

### <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="fa060-378">Scénáře proxy serveru a nástroje pro vyrovnávání zatížení</span><span class="sxs-lookup"><span data-stu-id="fa060-378">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="fa060-379">[Middleware pro integraci služby IIS](#enable-the-iisintegration-components) a modul ASP.NET Core jsou nakonfigurované pro přeposílání:</span><span class="sxs-lookup"><span data-stu-id="fa060-379">The [IIS Integration Middleware](#enable-the-iisintegration-components) and the ASP.NET Core Module are configured to forward the:</span></span>

* <span data-ttu-id="fa060-380">Schéma (HTTP/HTTPS).</span><span class="sxs-lookup"><span data-stu-id="fa060-380">Scheme (HTTP/HTTPS).</span></span>
* <span data-ttu-id="fa060-381">Vzdálená IP adresa, ze které pochází požadavek.</span><span class="sxs-lookup"><span data-stu-id="fa060-381">Remote IP address where the request originated.</span></span>

<span data-ttu-id="fa060-382">Middleware pro [integraci služby IIS](#enable-the-iisintegration-components) konfiguruje middleware předávaných hlaviček.</span><span class="sxs-lookup"><span data-stu-id="fa060-382">The [IIS Integration Middleware](#enable-the-iisintegration-components) configures Forwarded Headers Middleware.</span></span>

<span data-ttu-id="fa060-383">Pro aplikace hostované za dalšími proxy servery a nástroji pro vyrovnávání zatížení může být vyžadována další konfigurace.</span><span class="sxs-lookup"><span data-stu-id="fa060-383">Additional configuration might be required for apps hosted behind additional proxy servers and load balancers.</span></span> <span data-ttu-id="fa060-384">Další informace najdete v tématu [konfigurace ASP.NET Core pro práci se servery proxy a nástroji pro vyrovnávání zatížení](xref:host-and-deploy/proxy-load-balancer).</span><span class="sxs-lookup"><span data-stu-id="fa060-384">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

### <a name="webconfig-file"></a><span data-ttu-id="fa060-385">soubor Web. config</span><span class="sxs-lookup"><span data-stu-id="fa060-385">web.config file</span></span>

<span data-ttu-id="fa060-386">Soubor *Web. config* konfiguruje [modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="fa060-386">The *web.config* file configures the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="fa060-387">Vytváření, transformace a publikování souboru *Web. config* je zpracováno nástrojem MSBuild cíl ( `_TransformWebConfig` ) při publikování projektu.</span><span class="sxs-lookup"><span data-stu-id="fa060-387">Creating, transforming, and publishing the *web.config* file is handled by an MSBuild target (`_TransformWebConfig`) when the project is published.</span></span> <span data-ttu-id="fa060-388">Tento cíl je k dispozici v cíle webové sady SDK ( `Microsoft.NET.Sdk.Web` ).</span><span class="sxs-lookup"><span data-stu-id="fa060-388">This target is present in the Web SDK targets (`Microsoft.NET.Sdk.Web`).</span></span> <span data-ttu-id="fa060-389">Sada SDK je nastavena v horní části souboru projektu:</span><span class="sxs-lookup"><span data-stu-id="fa060-389">The SDK is set at the top of the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="fa060-390">Pokud soubor *Web. config* není v projektu přítomen, je vytvořen soubor se správným *processPath* a *argumenty* pro konfiguraci modulu ASP.NET Core a přesunutí do [publikovaného výstupu](xref:host-and-deploy/directory-structure).</span><span class="sxs-lookup"><span data-stu-id="fa060-390">If a *web.config* file isn't present in the project, the file is created with the correct *processPath* and *arguments* to configure the ASP.NET Core Module and moved to [published output](xref:host-and-deploy/directory-structure).</span></span>

<span data-ttu-id="fa060-391">Pokud je soubor *Web. config* přítomen v projektu, soubor je transformován se správným *processPath* a *argumenty* pro konfiguraci modulu ASP.NET Core a přesunutí do publikovaného výstupu.</span><span class="sxs-lookup"><span data-stu-id="fa060-391">If a *web.config* file is present in the project, the file is transformed with the correct *processPath* and *arguments* to configure the ASP.NET Core Module and moved to published output.</span></span> <span data-ttu-id="fa060-392">Transformace nemění nastavení konfigurace služby IIS v souboru.</span><span class="sxs-lookup"><span data-stu-id="fa060-392">The transformation doesn't modify IIS configuration settings in the file.</span></span>

<span data-ttu-id="fa060-393">Soubor *Web. config* může poskytovat další nastavení konfigurace služby IIS, která ovládají aktivní moduly IIS.</span><span class="sxs-lookup"><span data-stu-id="fa060-393">The *web.config* file may provide additional IIS configuration settings that control active IIS modules.</span></span> <span data-ttu-id="fa060-394">Informace o modulech služby IIS, které jsou schopné zpracovávat požadavky s ASP.NET Core aplikacemi, najdete v tématu [IIS modules](xref:host-and-deploy/iis/modules) .</span><span class="sxs-lookup"><span data-stu-id="fa060-394">For information on IIS modules that are capable of processing requests with ASP.NET Core apps, see the [IIS modules](xref:host-and-deploy/iis/modules) topic.</span></span>

<span data-ttu-id="fa060-395">Chcete-li webové sadě SDK zabránit ve transformaci souboru *Web. config* , použijte **\<IsTransformWebConfigDisabled>** vlastnost v souboru projektu:</span><span class="sxs-lookup"><span data-stu-id="fa060-395">To prevent the Web SDK from transforming the *web.config* file, use the **\<IsTransformWebConfigDisabled>** property in the project file:</span></span>

```xml
<PropertyGroup>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

<span data-ttu-id="fa060-396">Při zakazování webové sady SDK z transformace souboru by měl vývojář *processPath* a *argumenty* ručně nastavit.</span><span class="sxs-lookup"><span data-stu-id="fa060-396">When disabling the Web SDK from transforming the file, the *processPath* and *arguments* should be manually set by the developer.</span></span> <span data-ttu-id="fa060-397">Další informace naleznete v tématu <xref:host-and-deploy/aspnet-core-module>.</span><span class="sxs-lookup"><span data-stu-id="fa060-397">For more information, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

### <a name="webconfig-file-location"></a><span data-ttu-id="fa060-398">umístění souboru Web. config</span><span class="sxs-lookup"><span data-stu-id="fa060-398">web.config file location</span></span>

<span data-ttu-id="fa060-399">Aby bylo možné správně nastavit [modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module) , musí být soubor *Web. config* přítomen v [kořenové cestě obsahu](xref:fundamentals/index#content-root) (obvykle v základní cestě aplikace) nasazené aplikace.</span><span class="sxs-lookup"><span data-stu-id="fa060-399">In order to set up the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) correctly, the *web.config* file must be present at the [content root](xref:fundamentals/index#content-root) path (typically the app base path) of the deployed app.</span></span> <span data-ttu-id="fa060-400">Toto je stejné umístění jako fyzická cesta k webu poskytované službě IIS.</span><span class="sxs-lookup"><span data-stu-id="fa060-400">This is the same location as the website physical path provided to IIS.</span></span> <span data-ttu-id="fa060-401">Soubor *Web. config* je vyžadován v kořenu aplikace, aby bylo možné publikovat více aplikací pomocí nasazení webu.</span><span class="sxs-lookup"><span data-stu-id="fa060-401">The *web.config* file is required at the root of the app to enable the publishing of multiple apps using Web Deploy.</span></span>

<span data-ttu-id="fa060-402">Citlivé soubory existují na fyzické cestě aplikace, jako je například \* \<assembly> . runtimeconfig. JSON*, \* \<assembly> . XML* (dokumentační komentáře XML) a \* \<assembly> . DEPS. JSON\*.</span><span class="sxs-lookup"><span data-stu-id="fa060-402">Sensitive files exist on the app's physical path, such as *\<assembly>.runtimeconfig.json*, *\<assembly>.xml* (XML Documentation comments), and *\<assembly>.deps.json*.</span></span> <span data-ttu-id="fa060-403">Když je přítomen soubor *Web. config* a lokalita se spouští normálně, služba IIS tyto citlivé soubory po vyžádání neobsluhuje.</span><span class="sxs-lookup"><span data-stu-id="fa060-403">When the *web.config* file is present and the site starts normally, IIS doesn't serve these sensitive files if they're requested.</span></span> <span data-ttu-id="fa060-404">Pokud soubor *Web. config* chybí, je nesprávně pojmenovaný nebo nemůže nakonfigurovat lokalitu pro normální spuštění, služba IIS může obsluhovat citlivé soubory veřejně.</span><span class="sxs-lookup"><span data-stu-id="fa060-404">If the *web.config* file is missing, incorrectly named, or unable to configure the site for normal startup, IIS may serve sensitive files publicly.</span></span>

<span data-ttu-id="fa060-405">**Soubor *Web. config* musí být současně přítomen v nasazení, správně pojmenován a může nakonfigurovat lokalitu pro normální spuštění. Nikdy neodstraňujte soubor *Web. config* z produkčního nasazení.**</span><span class="sxs-lookup"><span data-stu-id="fa060-405">**The *web.config* file must be present in the deployment at all times, correctly named, and able to configure the site for normal start up. Never remove the *web.config* file from a production deployment.**</span></span>

### <a name="transform-webconfig"></a><span data-ttu-id="fa060-406">Transformace souboru web.config</span><span class="sxs-lookup"><span data-stu-id="fa060-406">Transform web.config</span></span>

<span data-ttu-id="fa060-407">Pokud potřebujete transformovat *Web. config* při publikování, přečtěte si téma <xref:host-and-deploy/iis/transform-webconfig> .</span><span class="sxs-lookup"><span data-stu-id="fa060-407">If you need to transform *web.config* on publish, see <xref:host-and-deploy/iis/transform-webconfig>.</span></span> <span data-ttu-id="fa060-408">Může být nutné transformovat *Web. config* při publikování a nastavit proměnné prostředí na základě konfigurace, profilu nebo prostředí.</span><span class="sxs-lookup"><span data-stu-id="fa060-408">You might need to transform *web.config* on publish to set environment variables based on the configuration, profile, or environment.</span></span>

## <a name="iis-configuration"></a><span data-ttu-id="fa060-409">Konfigurace služby IIS</span><span class="sxs-lookup"><span data-stu-id="fa060-409">IIS configuration</span></span>

<span data-ttu-id="fa060-410">**Operační systémy Windows Server**</span><span class="sxs-lookup"><span data-stu-id="fa060-410">**Windows Server operating systems**</span></span>

<span data-ttu-id="fa060-411">Povolte roli serveru **webový server (IIS)** a vytvořte služby rolí.</span><span class="sxs-lookup"><span data-stu-id="fa060-411">Enable the **Web Server (IIS)** server role and establish role services.</span></span>

1. <span data-ttu-id="fa060-412">Použijte průvodce **přidáním rolí a funkcí** z nabídky **Správa** nebo odkazu v **Správce serveru**.</span><span class="sxs-lookup"><span data-stu-id="fa060-412">Use the **Add Roles and Features** wizard from the **Manage** menu or the link in **Server Manager**.</span></span> <span data-ttu-id="fa060-413">V kroku **role serveru** zaškrtněte políčko **webový server (IIS)**.</span><span class="sxs-lookup"><span data-stu-id="fa060-413">On the **Server Roles** step, check the box for **Web Server (IIS)**.</span></span>

   ![V kroku vybrat role serveru je vybraná role Webový server IIS.](index/_static/server-roles-ws2016.png)

1. <span data-ttu-id="fa060-415">Po kroku **funkce** se krok **služby rolí** načte pro webový server (IIS).</span><span class="sxs-lookup"><span data-stu-id="fa060-415">After the **Features** step, the **Role services** step loads for Web Server (IIS).</span></span> <span data-ttu-id="fa060-416">Vyberte požadované služby role IIS nebo přijměte výchozí poskytnuté služby rolí.</span><span class="sxs-lookup"><span data-stu-id="fa060-416">Select the IIS role services desired or accept the default role services provided.</span></span>

   ![Výchozí služby rolí se vyberou v kroku vybrat služby rolí.](index/_static/role-services-ws2016.png)

   <span data-ttu-id="fa060-418">**Ověřování systému Windows (volitelné)**</span><span class="sxs-lookup"><span data-stu-id="fa060-418">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="fa060-419">Chcete-li povolit ověřování systému Windows, rozbalte následující uzly: zabezpečení **webového serveru**  >  **Security**.</span><span class="sxs-lookup"><span data-stu-id="fa060-419">To enable Windows Authentication, expand the following nodes: **Web Server** > **Security**.</span></span> <span data-ttu-id="fa060-420">Vyberte funkci **ověřování systému Windows** .</span><span class="sxs-lookup"><span data-stu-id="fa060-420">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="fa060-421">Další informace najdete v tématu [ověřování \<windowsAuthentication> systému Windows](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) a [Konfigurace ověřování systému Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="fa060-421">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="fa060-422">**WebSockets (volitelné)**</span><span class="sxs-lookup"><span data-stu-id="fa060-422">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="fa060-423">Objekty WebSocket jsou podporované ASP.NET Core 1,1 nebo novějším.</span><span class="sxs-lookup"><span data-stu-id="fa060-423">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="fa060-424">Chcete-li povolit objekty WebSockets, rozbalte následující **Web Server**uzly:  >  **vývoj aplikací**webového serveru.</span><span class="sxs-lookup"><span data-stu-id="fa060-424">To enable WebSockets, expand the following nodes: **Web Server** > **Application Development**.</span></span> <span data-ttu-id="fa060-425">Vyberte funkci **protokolu WebSocket** .</span><span class="sxs-lookup"><span data-stu-id="fa060-425">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="fa060-426">Další informace najdete v tématu [objekty WebSockets](xref:fundamentals/websockets).</span><span class="sxs-lookup"><span data-stu-id="fa060-426">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="fa060-427">Chcete-li nainstalovat roli a služby webového serveru, postupujte podle kroků pro **potvrzení** .</span><span class="sxs-lookup"><span data-stu-id="fa060-427">Proceed through the **Confirmation** step to install the web server role and services.</span></span> <span data-ttu-id="fa060-428">Po instalaci role **webový server (IIS)** není nutné restartovat server nebo službu IIS.</span><span class="sxs-lookup"><span data-stu-id="fa060-428">A server/IIS restart isn't required after installing the **Web Server (IIS)** role.</span></span>

<span data-ttu-id="fa060-429">**Desktopové operační systémy Windows**</span><span class="sxs-lookup"><span data-stu-id="fa060-429">**Windows desktop operating systems**</span></span>

<span data-ttu-id="fa060-430">Povolte **konzolu pro správu služby IIS** a **webové služby**.</span><span class="sxs-lookup"><span data-stu-id="fa060-430">Enable the **IIS Management Console** and **World Wide Web Services**.</span></span>

1. <span data-ttu-id="fa060-431">Přejděte na **Ovládací panely** > **programy** programy > **a funkce** > **zapnout nebo vypnout funkce systému Windows** (levá strana obrazovky).</span><span class="sxs-lookup"><span data-stu-id="fa060-431">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>

1. <span data-ttu-id="fa060-432">Otevřete uzel **Internetová informační služba** .</span><span class="sxs-lookup"><span data-stu-id="fa060-432">Open the **Internet Information Services** node.</span></span> <span data-ttu-id="fa060-433">Otevřete uzel **Nástroje webové správy** .</span><span class="sxs-lookup"><span data-stu-id="fa060-433">Open the **Web Management Tools** node.</span></span>

1. <span data-ttu-id="fa060-434">Zaškrtněte políčko pro **konzolu pro správu služby IIS**.</span><span class="sxs-lookup"><span data-stu-id="fa060-434">Check the box for **IIS Management Console**.</span></span>

1. <span data-ttu-id="fa060-435">Zaškrtněte políčko u **webových služeb**.</span><span class="sxs-lookup"><span data-stu-id="fa060-435">Check the box for **World Wide Web Services**.</span></span>

1. <span data-ttu-id="fa060-436">Přijměte výchozí funkce pro **webové služby** nebo upravte funkce služby IIS.</span><span class="sxs-lookup"><span data-stu-id="fa060-436">Accept the default features for **World Wide Web Services** or customize the IIS features.</span></span>

   <span data-ttu-id="fa060-437">**Ověřování systému Windows (volitelné)**</span><span class="sxs-lookup"><span data-stu-id="fa060-437">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="fa060-438">Chcete-li povolit ověřování systému Windows, rozbalte následující uzly: zabezpečení **webové služby**  >  **Security**.</span><span class="sxs-lookup"><span data-stu-id="fa060-438">To enable Windows Authentication, expand the following nodes: **World Wide Web Services** > **Security**.</span></span> <span data-ttu-id="fa060-439">Vyberte funkci **ověřování systému Windows** .</span><span class="sxs-lookup"><span data-stu-id="fa060-439">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="fa060-440">Další informace najdete v tématu [ověřování \<windowsAuthentication> systému Windows](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) a [Konfigurace ověřování systému Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="fa060-440">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="fa060-441">**WebSockets (volitelné)**</span><span class="sxs-lookup"><span data-stu-id="fa060-441">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="fa060-442">Objekty WebSocket jsou podporované ASP.NET Core 1,1 nebo novějším.</span><span class="sxs-lookup"><span data-stu-id="fa060-442">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="fa060-443">Chcete-li povolit objekty WebSockets, rozbalte následující uzly: funkce pro vývoj aplikací v **rámci webové služby**  >  **Application Development Features**.</span><span class="sxs-lookup"><span data-stu-id="fa060-443">To enable WebSockets, expand the following nodes: **World Wide Web Services** > **Application Development Features**.</span></span> <span data-ttu-id="fa060-444">Vyberte funkci **protokolu WebSocket** .</span><span class="sxs-lookup"><span data-stu-id="fa060-444">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="fa060-445">Další informace najdete v tématu [objekty WebSockets](xref:fundamentals/websockets).</span><span class="sxs-lookup"><span data-stu-id="fa060-445">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="fa060-446">Pokud instalace služby IIS vyžaduje restart, restartujte systém.</span><span class="sxs-lookup"><span data-stu-id="fa060-446">If the IIS installation requires a restart, restart the system.</span></span>

![V rámci funkcí systému Windows jsou vybrána Konzola pro správu služby IIS a webové služby.](index/_static/windows-features-win10.png)

## <a name="install-the-net-core-hosting-bundle"></a><span data-ttu-id="fa060-448">Instalace hostující sady .NET Core</span><span class="sxs-lookup"><span data-stu-id="fa060-448">Install the .NET Core Hosting Bundle</span></span>

<span data-ttu-id="fa060-449">Nainstalujte *hostující sadu .NET Core* do hostitelského systému.</span><span class="sxs-lookup"><span data-stu-id="fa060-449">Install the *.NET Core Hosting Bundle* on the hosting system.</span></span> <span data-ttu-id="fa060-450">Svazek nainstaluje modul runtime .NET Core, knihovnu .NET Core a [modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="fa060-450">The bundle installs the .NET Core Runtime, .NET Core Library, and the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="fa060-451">Modul umožňuje, aby aplikace ASP.NET Core běžely za službou IIS.</span><span class="sxs-lookup"><span data-stu-id="fa060-451">The module allows ASP.NET Core apps to run behind IIS.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="fa060-452">Pokud je hostující sada nainstalována před službou IIS, je nutné opravit instalaci sady prostředků.</span><span class="sxs-lookup"><span data-stu-id="fa060-452">If the Hosting Bundle is installed before IIS, the bundle installation must be repaired.</span></span> <span data-ttu-id="fa060-453">Spusťte znovu instalační program hostující sady po instalaci služby IIS.</span><span class="sxs-lookup"><span data-stu-id="fa060-453">Run the Hosting Bundle installer again after installing IIS.</span></span>
>
> <span data-ttu-id="fa060-454">Pokud se hostující sada nainstaluje po instalaci 64 (x64) verze .NET Core, můžou se zdát, že sady SDK chybí ([nezjistily se žádné sady .NET Core SDK](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span><span class="sxs-lookup"><span data-stu-id="fa060-454">If the Hosting Bundle is installed after installing the 64-bit (x64) version of .NET Core, SDKs might appear to be missing ([No .NET Core SDKs were detected](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span></span> <span data-ttu-id="fa060-455">Chcete-li tento problém vyřešit, přečtěte si téma <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle> .</span><span class="sxs-lookup"><span data-stu-id="fa060-455">To resolve the problem, see <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>.</span></span>

### <a name="direct-download-current-version"></a><span data-ttu-id="fa060-456">Přímé stažení (aktuální verze)</span><span class="sxs-lookup"><span data-stu-id="fa060-456">Direct download (current version)</span></span>

<span data-ttu-id="fa060-457">Stáhněte instalační program pomocí následujícího odkazu:</span><span class="sxs-lookup"><span data-stu-id="fa060-457">Download the installer using the following link:</span></span>

[<span data-ttu-id="fa060-458">Aktuální instalační program sady hostujících sad .NET Core (přímé stahování)</span><span class="sxs-lookup"><span data-stu-id="fa060-458">Current .NET Core Hosting Bundle installer (direct download)</span></span>](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

### <a name="earlier-versions-of-the-installer"></a><span data-ttu-id="fa060-459">Starší verze instalačního programu</span><span class="sxs-lookup"><span data-stu-id="fa060-459">Earlier versions of the installer</span></span>

<span data-ttu-id="fa060-460">Chcete-li získat starší verzi instalačního programu:</span><span class="sxs-lookup"><span data-stu-id="fa060-460">To obtain an earlier version of the installer:</span></span>

1. <span data-ttu-id="fa060-461">Přejděte na stránku [stáhnout jádro .NET Core](https://dotnet.microsoft.com/download/dotnet-core) .</span><span class="sxs-lookup"><span data-stu-id="fa060-461">Navigate to the [Download .NET Core](https://dotnet.microsoft.com/download/dotnet-core) page.</span></span>
1. <span data-ttu-id="fa060-462">Vyberte požadovanou verzi .NET Core.</span><span class="sxs-lookup"><span data-stu-id="fa060-462">Select the desired .NET Core version.</span></span>
1. <span data-ttu-id="fa060-463">Ve sloupci **Spustit aplikace – modul runtime** vyhledejte řádek požadované verze modulu runtime .NET Core.</span><span class="sxs-lookup"><span data-stu-id="fa060-463">In the **Run apps - Runtime** column, find the row of the .NET Core runtime version desired.</span></span>
1. <span data-ttu-id="fa060-464">Stáhněte instalační program pomocí odkazu **hostující sada** .</span><span class="sxs-lookup"><span data-stu-id="fa060-464">Download the installer using the **Hosting Bundle** link.</span></span>

> [!WARNING]
> <span data-ttu-id="fa060-465">Některé instalační programy obsahují verze vydaných verzí, které dosáhly svého konce životnosti (konce řádku) a které už nejsou podporovány společností Microsoft.</span><span class="sxs-lookup"><span data-stu-id="fa060-465">Some installers contain release versions that have reached their end of life (EOL) and are no longer supported by Microsoft.</span></span> <span data-ttu-id="fa060-466">Další informace najdete v tématu [zásady podpory](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span><span class="sxs-lookup"><span data-stu-id="fa060-466">For more information, see the [support policy](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span></span>

### <a name="install-the-hosting-bundle"></a><span data-ttu-id="fa060-467">Instalace hostitelského balíčku</span><span class="sxs-lookup"><span data-stu-id="fa060-467">Install the Hosting Bundle</span></span>

1. <span data-ttu-id="fa060-468">Spusťte instalační program na serveru.</span><span class="sxs-lookup"><span data-stu-id="fa060-468">Run the installer on the server.</span></span> <span data-ttu-id="fa060-469">Při spuštění instalačního programu z příkazového prostředí správce jsou k dispozici následující parametry:</span><span class="sxs-lookup"><span data-stu-id="fa060-469">The following parameters are available when running the installer from an administrator command shell:</span></span>

   * <span data-ttu-id="fa060-470">`OPT_NO_ANCM=1`: Přeskočit instalaci modulu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="fa060-470">`OPT_NO_ANCM=1`: Skip installing the ASP.NET Core Module.</span></span>
   * <span data-ttu-id="fa060-471">`OPT_NO_RUNTIME=1`: Přeskočí instalace modulu runtime .NET Core.</span><span class="sxs-lookup"><span data-stu-id="fa060-471">`OPT_NO_RUNTIME=1`: Skip installing the .NET Core runtime.</span></span> <span data-ttu-id="fa060-472">Používá se, když Server hostuje jenom [samostatná nasazení (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span><span class="sxs-lookup"><span data-stu-id="fa060-472">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="fa060-473">`OPT_NO_SHAREDFX=1`: Přeskočí instalace sdíleného rozhraní ASP.NET (ASP.NET Runtime).</span><span class="sxs-lookup"><span data-stu-id="fa060-473">`OPT_NO_SHAREDFX=1`: Skip installing the ASP.NET Shared Framework (ASP.NET runtime).</span></span> <span data-ttu-id="fa060-474">Používá se, když Server hostuje jenom [samostatná nasazení (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span><span class="sxs-lookup"><span data-stu-id="fa060-474">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="fa060-475">`OPT_NO_X86=1`: Přeskočí instalace běhových prostředí x86.</span><span class="sxs-lookup"><span data-stu-id="fa060-475">`OPT_NO_X86=1`: Skip installing x86 runtimes.</span></span> <span data-ttu-id="fa060-476">Tento parametr použijte, pokud víte, že nebudete hostovat 32 aplikace.</span><span class="sxs-lookup"><span data-stu-id="fa060-476">Use this parameter when you know that you won't be hosting 32-bit apps.</span></span> <span data-ttu-id="fa060-477">Pokud existuje možnost, že v budoucnosti budete hostovat jak 32, tak i 64 aplikace, tento parametr nepoužívejte a nainstalujete oba moduly runtime.</span><span class="sxs-lookup"><span data-stu-id="fa060-477">If there's any chance that you will host both 32-bit and 64-bit apps in the future, don't use this parameter and install both runtimes.</span></span>
   * <span data-ttu-id="fa060-478">`OPT_NO_SHARED_CONFIG_CHECK=1`: Pokud je sdílená konfigurace (*ApplicationHost. config*) ve stejném počítači jako instalace služby IIS, zakažte kontrolu použití sdílené konfigurace služby IIS.</span><span class="sxs-lookup"><span data-stu-id="fa060-478">`OPT_NO_SHARED_CONFIG_CHECK=1`: Disable the check for using an IIS Shared Configuration when the shared configuration (*applicationHost.config*) is on the same machine as the IIS installation.</span></span> <span data-ttu-id="fa060-479">*K dispozici jenom pro ASP.NET Core 2,2 nebo novější instalační programy hostujících prostředků.*</span><span class="sxs-lookup"><span data-stu-id="fa060-479">*Only available for ASP.NET Core 2.2 or later Hosting Bundler installers.*</span></span> <span data-ttu-id="fa060-480">Další informace naleznete v tématu <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.</span><span class="sxs-lookup"><span data-stu-id="fa060-480">For more information, see <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.</span></span>
1. <span data-ttu-id="fa060-481">Restartujte systém nebo v příkazovém prostředí spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="fa060-481">Restart the system or execute the following commands in a command shell:</span></span>

   ```console
   net stop was /y
   net start w3svc
   ```
   <span data-ttu-id="fa060-482">Restartování služby IIS zabere změny v systémové cestě, což je proměnná prostředí vytvořená instalačním programem.</span><span class="sxs-lookup"><span data-stu-id="fa060-482">Restarting IIS picks up a change to the system PATH, which is an environment variable, made by the installer.</span></span>

<span data-ttu-id="fa060-483">ASP.NET Core nesplňuje chování při přeposílání pro vydání oprav pro balíčky sdílených rozhraní.</span><span class="sxs-lookup"><span data-stu-id="fa060-483">ASP.NET Core doesn't adopt roll-forward behavior for patch releases of shared framework packages.</span></span> <span data-ttu-id="fa060-484">Po upgradu sdíleného rozhraní instalací nové hostitelské sady restartujte systém nebo v příkazovém prostředí spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="fa060-484">After upgrading the shared framework by installing a new hosting bundle, restart the system or execute the following commands in a command shell:</span></span>

```console
net stop was /y
net start w3svc
```

> [!NOTE]
> <span data-ttu-id="fa060-485">Informace o sdílené konfiguraci služby IIS najdete v tématu [modul ASP.NET Core se sdílenou konfigurací služby IIS](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).</span><span class="sxs-lookup"><span data-stu-id="fa060-485">For information on IIS Shared Configuration, see [ASP.NET Core Module with IIS Shared Configuration](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).</span></span>

## <a name="install-web-deploy-when-publishing-with-visual-studio"></a><span data-ttu-id="fa060-486">Při publikování v aplikaci Visual Studio nainstalovat Nasazení webu</span><span class="sxs-lookup"><span data-stu-id="fa060-486">Install Web Deploy when publishing with Visual Studio</span></span>

<span data-ttu-id="fa060-487">Při nasazování aplikací na servery s [nasazení webu](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later)nainstalujte na server nejnovější verzi nasazení webu.</span><span class="sxs-lookup"><span data-stu-id="fa060-487">When deploying apps to servers with [Web Deploy](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later), install the latest version of Web Deploy on the server.</span></span> <span data-ttu-id="fa060-488">Chcete-li nainstalovat Nasazení webu, použijte [instalační program webové platformy (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) nebo si instalační program Získejte přímo z webu [služby Stažení softwaru](https://www.microsoft.com/download/details.aspx?id=43717).</span><span class="sxs-lookup"><span data-stu-id="fa060-488">To install Web Deploy, use the [Web Platform Installer (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) or obtain an installer directly from the [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=43717).</span></span> <span data-ttu-id="fa060-489">Upřednostňovanou metodou je použití WebPI.</span><span class="sxs-lookup"><span data-stu-id="fa060-489">The preferred method is to use WebPI.</span></span> <span data-ttu-id="fa060-490">WebPI nabízí samostatnou instalaci a konfiguraci pro poskytovatele hostingu.</span><span class="sxs-lookup"><span data-stu-id="fa060-490">WebPI offers a standalone setup and a configuration for hosting providers.</span></span>

## <a name="create-the-iis-site"></a><span data-ttu-id="fa060-491">Vytvoření webu služby IIS</span><span class="sxs-lookup"><span data-stu-id="fa060-491">Create the IIS site</span></span>

1. <span data-ttu-id="fa060-492">V hostitelském systému vytvořte složku, která bude obsahovat publikované složky a soubory aplikace.</span><span class="sxs-lookup"><span data-stu-id="fa060-492">On the hosting system, create a folder to contain the app's published folders and files.</span></span> <span data-ttu-id="fa060-493">V následujícím kroku je jako fyzická cesta k aplikaci služba IIS poskytována jako cesta k této složce.</span><span class="sxs-lookup"><span data-stu-id="fa060-493">In a following step, the folder's path is provided to IIS as the physical path to the app.</span></span> <span data-ttu-id="fa060-494">Další informace o složce nasazení aplikace a rozložení souborů naleznete v tématu <xref:host-and-deploy/directory-structure> .</span><span class="sxs-lookup"><span data-stu-id="fa060-494">For more information on an app's deployment folder and file layout, see <xref:host-and-deploy/directory-structure>.</span></span>

1. <span data-ttu-id="fa060-495">Ve Správci služby IIS otevřete uzel serveru na panelu **připojení** .</span><span class="sxs-lookup"><span data-stu-id="fa060-495">In IIS Manager, open the server's node in the **Connections** panel.</span></span> <span data-ttu-id="fa060-496">Klikněte pravým tlačítkem na složku **weby** .</span><span class="sxs-lookup"><span data-stu-id="fa060-496">Right-click the **Sites** folder.</span></span> <span data-ttu-id="fa060-497">V místní nabídce vyberte **Přidat web** .</span><span class="sxs-lookup"><span data-stu-id="fa060-497">Select **Add Website** from the contextual menu.</span></span>

1. <span data-ttu-id="fa060-498">Zadejte **název lokality** a nastavte **fyzickou cestu** ke složce pro nasazení aplikace.</span><span class="sxs-lookup"><span data-stu-id="fa060-498">Provide a **Site name** and set the **Physical path** to the app's deployment folder.</span></span> <span data-ttu-id="fa060-499">Zadejte konfiguraci **vazby** a vytvořte web výběrem **OK**:</span><span class="sxs-lookup"><span data-stu-id="fa060-499">Provide the **Binding** configuration and create the website by selecting **OK**:</span></span>

   ![Zadejte název lokality, fyzickou cestu a název hostitele v kroku přidat web.](index/_static/add-website-ws2016.png)

   > [!WARNING]
   > <span data-ttu-id="fa060-501">Nelze použít vazby zástupných znaků na nejvyšší úrovni ( `http://*:80/` a `http://+:80` ). **not**</span><span class="sxs-lookup"><span data-stu-id="fa060-501">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="fa060-502">Vazby zástupných znaků nejvyšší úrovně můžou aplikaci otevřít pro slabá místa zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="fa060-502">Top-level wildcard bindings can open up your app to security vulnerabilities.</span></span> <span data-ttu-id="fa060-503">To platí pro silné i slabé zástupné znaky.</span><span class="sxs-lookup"><span data-stu-id="fa060-503">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="fa060-504">Místo zástupných znaků použijte explicitní názvy hostitelů.</span><span class="sxs-lookup"><span data-stu-id="fa060-504">Use explicit host names rather than wildcards.</span></span> <span data-ttu-id="fa060-505">Vazba zástupných znaků subdomény (například `*.mysub.com` ) nemá toto bezpečnostní riziko, pokud ovládáte celou nadřazenou doménu (na rozdíl od `*.com` , která je zranitelná).</span><span class="sxs-lookup"><span data-stu-id="fa060-505">Subdomain wildcard binding (for example, `*.mysub.com`) doesn't have this security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="fa060-506">Další informace najdete v [části rfc7230 část-5,4](https://tools.ietf.org/html/rfc7230#section-5.4) .</span><span class="sxs-lookup"><span data-stu-id="fa060-506">See [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) for more information.</span></span>

1. <span data-ttu-id="fa060-507">V uzlu serveru vyberte **fondy aplikací**.</span><span class="sxs-lookup"><span data-stu-id="fa060-507">Under the server's node, select **Application Pools**.</span></span>

1. <span data-ttu-id="fa060-508">Klikněte pravým tlačítkem myši na fond aplikací webu a v místní nabídce vyberte **základní nastavení** .</span><span class="sxs-lookup"><span data-stu-id="fa060-508">Right-click the site's app pool and select **Basic Settings** from the contextual menu.</span></span>

1. <span data-ttu-id="fa060-509">V okně **Upravit fond aplikací** nastavte **verzi .NET CLR** na **bez spravovaného kódu**:</span><span class="sxs-lookup"><span data-stu-id="fa060-509">In the **Edit Application Pool** window, set the **.NET CLR version** to **No Managed Code**:</span></span>

   ![Pro verzi .NET CLR nenastavte žádný spravovaný kód.](index/_static/edit-apppool-ws2016.png)

    <span data-ttu-id="fa060-511">ASP.NET Core běží v samostatném procesu a spravuje modul runtime.</span><span class="sxs-lookup"><span data-stu-id="fa060-511">ASP.NET Core runs in a separate process and manages the runtime.</span></span> <span data-ttu-id="fa060-512">ASP.NET Core nespoléhá na načítání CLR desktopu (.NET CLR).</span><span class="sxs-lookup"><span data-stu-id="fa060-512">ASP.NET Core doesn't rely on loading the desktop CLR (.NET CLR).</span></span> <span data-ttu-id="fa060-513">Základní modul CLR (Common Language Runtime) pro .NET Core se spouští k hostování aplikace v pracovním procesu.</span><span class="sxs-lookup"><span data-stu-id="fa060-513">The Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process.</span></span> <span data-ttu-id="fa060-514">Nastavení **verze .NET CLR** na **žádný spravovaný kód** není volitelné, ale doporučuje se.</span><span class="sxs-lookup"><span data-stu-id="fa060-514">Setting the **.NET CLR version** to **No Managed Code** is optional but recommended.</span></span>

1. <span data-ttu-id="fa060-515">*ASP.NET Core 2,2 nebo novější*:</span><span class="sxs-lookup"><span data-stu-id="fa060-515">*ASP.NET Core 2.2 or later*:</span></span>

   * <span data-ttu-id="fa060-516">V případě [nasazeného samostatného nasazení](/dotnet/core/deploying/#self-contained-deployments-scd) s 32 (x86) publikovaného s 32 sadou SDK, která používá [model hostování v rámci procesu](#in-process-hosting-model), povolte fond aplikací pro 32-bit.</span><span class="sxs-lookup"><span data-stu-id="fa060-516">For a 32-bit (x86) [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) published with a 32-bit SDK that uses the [in-process hosting model](#in-process-hosting-model), enable the Application Pool for 32-bit.</span></span> <span data-ttu-id="fa060-517">Ve Správci služby IIS přejděte na postranní panel **připojení** na **fondy aplikací** .</span><span class="sxs-lookup"><span data-stu-id="fa060-517">In IIS Manager, navigate to **Application Pools** in the **Connections** sidebar.</span></span> <span data-ttu-id="fa060-518">Vyberte fond aplikací aplikace.</span><span class="sxs-lookup"><span data-stu-id="fa060-518">Select the app's Application Pool.</span></span> <span data-ttu-id="fa060-519">Na bočním panelu **Akce** vyberte **Upřesnit nastavení**.</span><span class="sxs-lookup"><span data-stu-id="fa060-519">In the **Actions** sidebar, select **Advanced Settings**.</span></span> <span data-ttu-id="fa060-520">Nastavte **možnost povolit 32 aplikace** na `True` .</span><span class="sxs-lookup"><span data-stu-id="fa060-520">Set **Enable 32-Bit Applications** to `True`.</span></span> 

   * <span data-ttu-id="fa060-521">Pro 64 (x64) samostatné [nasazení](/dotnet/core/deploying/#self-contained-deployments-scd) , které používá [model hostování v rámci procesu](#in-process-hosting-model), zakažte fond aplikací pro procesy 32 (x86).</span><span class="sxs-lookup"><span data-stu-id="fa060-521">For a 64-bit (x64) [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) that uses the [in-process hosting model](#in-process-hosting-model), disable the app pool for 32-bit (x86) processes.</span></span> <span data-ttu-id="fa060-522">Ve Správci služby IIS přejděte na postranní panel **připojení** na **fondy aplikací** .</span><span class="sxs-lookup"><span data-stu-id="fa060-522">In IIS Manager, navigate to **Application Pools** in the **Connections** sidebar.</span></span> <span data-ttu-id="fa060-523">Vyberte fond aplikací aplikace.</span><span class="sxs-lookup"><span data-stu-id="fa060-523">Select the app's Application Pool.</span></span> <span data-ttu-id="fa060-524">Na bočním panelu **Akce** vyberte **Upřesnit nastavení**.</span><span class="sxs-lookup"><span data-stu-id="fa060-524">In the **Actions** sidebar, select **Advanced Settings**.</span></span> <span data-ttu-id="fa060-525">Nastavte **možnost povolit 32 aplikace** na `False` .</span><span class="sxs-lookup"><span data-stu-id="fa060-525">Set **Enable 32-Bit Applications** to `False`.</span></span> 

1. <span data-ttu-id="fa060-526">Potvrďte, že identita modelu procesu má správná oprávnění.</span><span class="sxs-lookup"><span data-stu-id="fa060-526">Confirm the process model identity has the proper permissions.</span></span>

   <span data-ttu-id="fa060-527">Pokud se výchozí identita fondu aplikací (**modelu procesu**  >  **Identity** ) změní z **ApplicationPoolIdentity** na jinou identitu, ověřte, že Nová identita má požadovaná oprávnění pro přístup ke složce, databázi a dalším požadovaným prostředkům aplikace.</span><span class="sxs-lookup"><span data-stu-id="fa060-527">If the default identity of the app pool (**Process Model** > **Identity**) is changed from **ApplicationPoolIdentity** to another identity, verify that the new identity has the required permissions to access the app's folder, database, and other required resources.</span></span> <span data-ttu-id="fa060-528">Například fond aplikací vyžaduje přístup pro čtení a zápis ke složkám, kde aplikace čte a zapisuje soubory.</span><span class="sxs-lookup"><span data-stu-id="fa060-528">For example, the app pool requires read and write access to folders where the app reads and writes files.</span></span>

<span data-ttu-id="fa060-529">**Konfigurace ověřování systému Windows (volitelné)**</span><span class="sxs-lookup"><span data-stu-id="fa060-529">**Windows Authentication configuration (Optional)**</span></span>  
<span data-ttu-id="fa060-530">Další informace najdete v tématu [Konfigurace ověřování systému Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="fa060-530">For more information, see [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

## <a name="deploy-the-app"></a><span data-ttu-id="fa060-531">Nasazení aplikace</span><span class="sxs-lookup"><span data-stu-id="fa060-531">Deploy the app</span></span>

<span data-ttu-id="fa060-532">Nasaďte aplikaci do složky **fyzické cesty** služby IIS, která byla navázána v části [Vytvoření webu služby IIS](#create-the-iis-site) .</span><span class="sxs-lookup"><span data-stu-id="fa060-532">Deploy the app to the IIS **Physical path** folder that was established in the [Create the IIS site](#create-the-iis-site) section.</span></span> <span data-ttu-id="fa060-533">[Nasazení webu](/iis/publish/using-web-deploy/introduction-to-web-deploy) je doporučeným mechanismem pro nasazení, ale existuje několik možností pro přesun aplikace ze složky *publikování* projektu do složky pro nasazení hostitelského systému.</span><span class="sxs-lookup"><span data-stu-id="fa060-533">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) is the recommended mechanism for deployment, but several options exist for moving the app from the project's *publish* folder to the hosting system's deployment folder.</span></span>

### <a name="web-deploy-with-visual-studio"></a><span data-ttu-id="fa060-534">Nasazení webu se sadou Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fa060-534">Web Deploy with Visual Studio</span></span>

<span data-ttu-id="fa060-535">Informace o tom, jak vytvořit profil publikování pro použití s Nasazení webu, najdete v tématu [publikační profily sady Visual Studio pro ASP.NET Core nasazení aplikací](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) .</span><span class="sxs-lookup"><span data-stu-id="fa060-535">See the [Visual Studio publish profiles for ASP.NET Core app deployment](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) topic to learn how to create a publish profile for use with Web Deploy.</span></span> <span data-ttu-id="fa060-536">Pokud poskytovatel hostingu poskytuje profil publikování nebo podporu pro jeho vytvoření, Stáhněte si jeho profil a importujte ho pomocí dialogového okna pro **publikování** sady Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="fa060-536">If the hosting provider provides a Publish Profile or support for creating one, download their profile and import it using the Visual Studio **Publish** dialog:</span></span>

![Stránka publikovat dialog](index/_static/pub-dialog.png)

### <a name="web-deploy-outside-of-visual-studio"></a><span data-ttu-id="fa060-538">Nasazení webu mimo Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fa060-538">Web Deploy outside of Visual Studio</span></span>

<span data-ttu-id="fa060-539">[Nasazení webu](/iis/publish/using-web-deploy/introduction-to-web-deploy) lze použít také mimo sadu Visual Studio z příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="fa060-539">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) can also be used outside of Visual Studio from the command line.</span></span> <span data-ttu-id="fa060-540">Další informace najdete v tématu [Nástroj pro nasazení webu](/iis/publish/using-web-deploy/use-the-web-deployment-tool).</span><span class="sxs-lookup"><span data-stu-id="fa060-540">For more information, see [Web Deployment Tool](/iis/publish/using-web-deploy/use-the-web-deployment-tool).</span></span>

### <a name="alternatives-to-web-deploy"></a><span data-ttu-id="fa060-541">Alternativy k Nasazení webu</span><span class="sxs-lookup"><span data-stu-id="fa060-541">Alternatives to Web Deploy</span></span>

<span data-ttu-id="fa060-542">K přesunu aplikace do hostitelského systému, jako je ruční kopírování, [xcopy](/windows-server/administration/windows-commands/xcopy), [Robocopy](/windows-server/administration/windows-commands/robocopy)nebo [PowerShell](/powershell/), použijte libovolný z několika způsobů.</span><span class="sxs-lookup"><span data-stu-id="fa060-542">Use any of several methods to move the app to the hosting system, such as manual copy, [Xcopy](/windows-server/administration/windows-commands/xcopy), [Robocopy](/windows-server/administration/windows-commands/robocopy), or [PowerShell](/powershell/).</span></span>

<span data-ttu-id="fa060-543">Další informace o nasazení ASP.NET Core do služby IIS najdete v části [prostředky nasazení pro správce služby IIS](#deployment-resources-for-iis-administrators) .</span><span class="sxs-lookup"><span data-stu-id="fa060-543">For more information on ASP.NET Core deployment to IIS, see the [Deployment resources for IIS administrators](#deployment-resources-for-iis-administrators) section.</span></span>

## <a name="browse-the-website"></a><span data-ttu-id="fa060-544">Procházet web</span><span class="sxs-lookup"><span data-stu-id="fa060-544">Browse the website</span></span>

<span data-ttu-id="fa060-545">Po nasazení aplikace do hostitelského systému vytvořte žádost jednomu z veřejných koncových bodů aplikace.</span><span class="sxs-lookup"><span data-stu-id="fa060-545">After the app is deployed to the hosting system, make a request to one of the app's public endpoints.</span></span>

<span data-ttu-id="fa060-546">V následujícím příkladu je lokalita svázána s **názvem hostitele** služby IIS `www.mysite.com` na **portu** `80` .</span><span class="sxs-lookup"><span data-stu-id="fa060-546">In the following example, the site is bound to an IIS **Host name** of `www.mysite.com` on **Port** `80`.</span></span> <span data-ttu-id="fa060-547">Odeslala se žádost `http://www.mysite.com` :</span><span class="sxs-lookup"><span data-stu-id="fa060-547">A request is made to `http://www.mysite.com`:</span></span>

![Prohlížeč Microsoft Edge načetl spouštěcí stránku služby IIS.](index/_static/browsewebsite.png)

## <a name="locked-deployment-files"></a><span data-ttu-id="fa060-549">Uzamčené soubory nasazení</span><span class="sxs-lookup"><span data-stu-id="fa060-549">Locked deployment files</span></span>

<span data-ttu-id="fa060-550">Soubory ve složce pro nasazení jsou zamčené, když je aplikace spuštěná.</span><span class="sxs-lookup"><span data-stu-id="fa060-550">Files in the deployment folder are locked when the app is running.</span></span> <span data-ttu-id="fa060-551">Uzamčené soubory nejde během nasazování přepsat.</span><span class="sxs-lookup"><span data-stu-id="fa060-551">Locked files can't be overwritten during deployment.</span></span> <span data-ttu-id="fa060-552">Chcete-li uvolnit uzamčené soubory v nasazení, zastavte fond aplikací pomocí **jednoho** z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="fa060-552">To release locked files in a deployment, stop the app pool using **one** of the following approaches:</span></span>

* <span data-ttu-id="fa060-553">V souboru projektu použijte Nasazení webu a odkaz `Microsoft.NET.Sdk.Web` .</span><span class="sxs-lookup"><span data-stu-id="fa060-553">Use Web Deploy and reference `Microsoft.NET.Sdk.Web` in the project file.</span></span> <span data-ttu-id="fa060-554">Soubor *App_offline. htm* se umístí do kořenové složky adresáře webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="fa060-554">An *app_offline.htm* file is placed at the root of the web app directory.</span></span> <span data-ttu-id="fa060-555">Když je soubor přítomen, modul ASP.NET Core aplikaci korektně ukončí a během nasazování zachová soubor *App_offline. htm* .</span><span class="sxs-lookup"><span data-stu-id="fa060-555">When the file is present, the ASP.NET Core Module gracefully shuts down the app and serves the *app_offline.htm* file during the deployment.</span></span> <span data-ttu-id="fa060-556">Další informace najdete v referenčních informacích k [konfiguraci modulu ASP.NET Core](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).</span><span class="sxs-lookup"><span data-stu-id="fa060-556">For more information, see the [ASP.NET Core Module configuration reference](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).</span></span>
* <span data-ttu-id="fa060-557">Ručně Zastavte fond aplikací ve Správci služby IIS na serveru.</span><span class="sxs-lookup"><span data-stu-id="fa060-557">Manually stop the app pool in the IIS Manager on the server.</span></span>
* <span data-ttu-id="fa060-558">Použití PowerShellu k vyřazení *App_offline. htm* (vyžaduje PowerShell 5 nebo novější):</span><span class="sxs-lookup"><span data-stu-id="fa060-558">Use PowerShell to drop *app_offline.htm* (requires PowerShell 5 or later):</span></span>

  ```powershell
  $pathToApp = 'PATH_TO_APP'

  # Stop the AppPool
  New-Item -Path $pathToApp app_offline.htm

  # Provide script commands here to deploy the app

  # Restart the AppPool
  Remove-Item -Path $pathToApp app_offline.htm

  ```

## <a name="data-protection"></a><span data-ttu-id="fa060-559">Ochrana dat</span><span class="sxs-lookup"><span data-stu-id="fa060-559">Data protection</span></span>

<span data-ttu-id="fa060-560">[Sada ASP.NET Core Data Protection Stack](xref:security/data-protection/introduction) je používána několika ASP.NET Core [middlewary](xref:fundamentals/middleware/index), včetně middlewaru používaného při ověřování.</span><span class="sxs-lookup"><span data-stu-id="fa060-560">The [ASP.NET Core Data Protection stack](xref:security/data-protection/introduction) is used by several ASP.NET Core [middlewares](xref:fundamentals/middleware/index), including middleware used in authentication.</span></span> <span data-ttu-id="fa060-561">I v případě, že rozhraní API ochrany dat není voláno uživatelským kódem, je třeba nakonfigurovat ochranu dat pomocí skriptu nasazení nebo v uživatelském kódu, aby bylo možné vytvořit trvalé úložiště kryptografických [klíčů](xref:security/data-protection/implementation/key-management).</span><span class="sxs-lookup"><span data-stu-id="fa060-561">Even if Data Protection APIs aren't called by user code, data protection should be configured with a deployment script or in user code to create a persistent cryptographic [key store](xref:security/data-protection/implementation/key-management).</span></span> <span data-ttu-id="fa060-562">Pokud ochrana dat není nakonfigurovaná, klíče se uchovávají v paměti a při restartování aplikace se zahodí.</span><span class="sxs-lookup"><span data-stu-id="fa060-562">If data protection isn't configured, the keys are held in memory and discarded when the app restarts.</span></span>

<span data-ttu-id="fa060-563">Pokud se klíčového prstence při restartu aplikace uloží do paměti:</span><span class="sxs-lookup"><span data-stu-id="fa060-563">If the key ring is stored in memory when the app restarts:</span></span>

* <span data-ttu-id="fa060-564">Všechny ověřovací tokeny založené na souborech cookie jsou neověřené.</span><span class="sxs-lookup"><span data-stu-id="fa060-564">All cookie-based authentication tokens are invalidated.</span></span> 
* <span data-ttu-id="fa060-565">Uživatelé se musí znovu přihlásit na svůj další požadavek.</span><span class="sxs-lookup"><span data-stu-id="fa060-565">Users are required to sign in again on their next request.</span></span> 
* <span data-ttu-id="fa060-566">Data chráněná pomocí Key ringu už nebude možné dešifrovat.</span><span class="sxs-lookup"><span data-stu-id="fa060-566">Any data protected with the key ring can no longer be decrypted.</span></span> <span data-ttu-id="fa060-567">To může zahrnovat [CSRF tokeny](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) a [ASP.NET Core soubory cookie TempData MVC](xref:fundamentals/app-state#tempdata).</span><span class="sxs-lookup"><span data-stu-id="fa060-567">This may include [CSRF tokens](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) and [ASP.NET Core MVC TempData cookies](xref:fundamentals/app-state#tempdata).</span></span>

<span data-ttu-id="fa060-568">Pokud chcete v rámci služby IIS nakonfigurovat ochranu dat a zachovat přitom klíčového prstence, použijte **některý** z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="fa060-568">To configure data protection under IIS to persist the key ring, use **one** of the following approaches:</span></span>

* <span data-ttu-id="fa060-569">**Vytvoření klíčů registru ochrany dat**</span><span class="sxs-lookup"><span data-stu-id="fa060-569">**Create Data Protection Registry Keys**</span></span>

  <span data-ttu-id="fa060-570">Klíče ochrany dat, které používá aplikace ASP.NET Core, jsou uložené v registru externě pro aplikace.</span><span class="sxs-lookup"><span data-stu-id="fa060-570">Data protection keys used by ASP.NET Core apps are stored in the registry external to the apps.</span></span> <span data-ttu-id="fa060-571">Pokud chcete zachovat klíče pro danou aplikaci, vytvořte klíče registru pro fond aplikací.</span><span class="sxs-lookup"><span data-stu-id="fa060-571">To persist the keys for a given app, create registry keys for the app pool.</span></span>

  <span data-ttu-id="fa060-572">Pro samostatnou instalaci služby IIS, která není součástí webfarmu, se dá [skript PowerShellu pro ochranu dat provision-AutoGenKeys. ps1](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) použít pro každý fond aplikací, který se používá v aplikaci ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="fa060-572">For standalone, non-webfarm IIS installations, the [Data Protection Provision-AutoGenKeys.ps1 PowerShell script](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) can be used for each app pool used with an ASP.NET Core app.</span></span> <span data-ttu-id="fa060-573">Tento skript vytvoří klíč registru v registru HKLM, který je přístupný jenom pro účet pracovního procesu fondu aplikací aplikace.</span><span class="sxs-lookup"><span data-stu-id="fa060-573">This script creates a registry key in the HKLM registry that's accessible only to the worker process account of the app's app pool.</span></span> <span data-ttu-id="fa060-574">Klíče jsou v klidovém stavu zašifrované pomocí rozhraní DPAPI s klíčem celého počítače.</span><span class="sxs-lookup"><span data-stu-id="fa060-574">Keys are encrypted at rest using DPAPI with a machine-wide key.</span></span>

  <span data-ttu-id="fa060-575">Ve scénářích webové farmy může být aplikace nakonfigurovaná tak, aby používala cestu UNC k uložení svého prstence s klíčem pro ochranu dat.</span><span class="sxs-lookup"><span data-stu-id="fa060-575">In web farm scenarios, an app can be configured to use a UNC path to store its data protection key ring.</span></span> <span data-ttu-id="fa060-576">Ve výchozím nastavení nejsou klíče ochrany dat šifrované.</span><span class="sxs-lookup"><span data-stu-id="fa060-576">By default, the data protection keys aren't encrypted.</span></span> <span data-ttu-id="fa060-577">Ujistěte se, že oprávnění k souborům pro sdílenou síťovou složku jsou omezená na účet systému Windows, pod kterým je aplikace spuštěná.</span><span class="sxs-lookup"><span data-stu-id="fa060-577">Ensure that the file permissions for the network share are limited to the Windows account the app runs under.</span></span> <span data-ttu-id="fa060-578">K ochraně neaktivních klíčů je možné použít certifikát x509.</span><span class="sxs-lookup"><span data-stu-id="fa060-578">An X509 certificate can be used to protect keys at rest.</span></span> <span data-ttu-id="fa060-579">Vezměte v úvahu mechanismus, který umožní uživatelům odeslat certifikáty: Umístěte certifikáty do důvěryhodného úložiště certifikátů uživatele a ujistěte se, že jsou k dispozici na všech počítačích, kde je aplikace uživatele spuštěna.</span><span class="sxs-lookup"><span data-stu-id="fa060-579">Consider a mechanism to allow users to upload certificates: Place certificates into the user's trusted certificate store and ensure they're available on all machines where the user's app runs.</span></span> <span data-ttu-id="fa060-580">Podrobnosti najdete v tématu [Konfigurace ochrany ASP.NET Core dat](xref:security/data-protection/configuration/overview) .</span><span class="sxs-lookup"><span data-stu-id="fa060-580">See [Configure ASP.NET Core Data Protection](xref:security/data-protection/configuration/overview) for details.</span></span>

* <span data-ttu-id="fa060-581">**Konfigurace fondu aplikací služby IIS pro načtení profilu uživatele**</span><span class="sxs-lookup"><span data-stu-id="fa060-581">**Configure the IIS Application Pool to load the user profile**</span></span>

  <span data-ttu-id="fa060-582">Toto nastavení se nachází v části **model procesu** v části **Rozšířená nastavení** fondu aplikací.</span><span class="sxs-lookup"><span data-stu-id="fa060-582">This setting is in the **Process Model** section under the **Advanced Settings** for the app pool.</span></span> <span data-ttu-id="fa060-583">Nastavte **načíst profil uživatele** na `True` .</span><span class="sxs-lookup"><span data-stu-id="fa060-583">Set **Load User Profile** to `True`.</span></span> <span data-ttu-id="fa060-584">Pokud je nastaveno na `True` , klíče jsou uloženy v adresáři profilu uživatele a chráněny pomocí rozhraní DPAPI s klíčem specifickým pro uživatelský účet.</span><span class="sxs-lookup"><span data-stu-id="fa060-584">When set to `True`, keys are stored in the user profile directory and protected using DPAPI with a key specific to the user account.</span></span> <span data-ttu-id="fa060-585">Klíče jsou uchovány ve složce *% localappdata%/ASP.NET/DataProtection-Keys* .</span><span class="sxs-lookup"><span data-stu-id="fa060-585">Keys are persisted to the *%LOCALAPPDATA%/ASP.NET/DataProtection-Keys* folder.</span></span>

  <span data-ttu-id="fa060-586">Musí být povolený i [atribut setProfileEnvironment](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) fondu aplikací.</span><span class="sxs-lookup"><span data-stu-id="fa060-586">The app pool's [setProfileEnvironment attribute](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) must also be enabled.</span></span> <span data-ttu-id="fa060-587">Výchozí hodnota `setProfileEnvironment` je `true` .</span><span class="sxs-lookup"><span data-stu-id="fa060-587">The default value of `setProfileEnvironment` is `true`.</span></span> <span data-ttu-id="fa060-588">V některých scénářích (například operační systém Windows) `setProfileEnvironment` je nastavena na `false` .</span><span class="sxs-lookup"><span data-stu-id="fa060-588">In some scenarios (for example, Windows OS), `setProfileEnvironment` is set to `false`.</span></span> <span data-ttu-id="fa060-589">Pokud se klíče neukládají v adresáři profilu uživatele podle očekávání:</span><span class="sxs-lookup"><span data-stu-id="fa060-589">If keys aren't stored in the user profile directory as expected:</span></span>

  1. <span data-ttu-id="fa060-590">Přejděte do složky *% windir%/system32/Inetsrv/config* .</span><span class="sxs-lookup"><span data-stu-id="fa060-590">Navigate to the *%windir%/system32/inetsrv/config* folder.</span></span>
  1. <span data-ttu-id="fa060-591">Otevřete soubor *ApplicationHost. config* .</span><span class="sxs-lookup"><span data-stu-id="fa060-591">Open the *applicationHost.config* file.</span></span>
  1. <span data-ttu-id="fa060-592">Vyhledejte element `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>`.</span><span class="sxs-lookup"><span data-stu-id="fa060-592">Locate the `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` element.</span></span>
  1. <span data-ttu-id="fa060-593">Potvrďte, že `setProfileEnvironment` atribut není přítomen, přičemž výchozí hodnota je `true` , nebo explicitně nastavte hodnotu atributu na `true` .</span><span class="sxs-lookup"><span data-stu-id="fa060-593">Confirm that the `setProfileEnvironment` attribute isn't present, which defaults the value to `true`, or explicitly set the attribute's value to `true`.</span></span>

* <span data-ttu-id="fa060-594">**Použití systému souborů jako úložiště pro Key Ring**</span><span class="sxs-lookup"><span data-stu-id="fa060-594">**Use the file system as a key ring store**</span></span>

  <span data-ttu-id="fa060-595">Upravte kód aplikace tak, aby [používal systém souborů jako úložiště ve službě Key Ring](xref:security/data-protection/configuration/overview).</span><span class="sxs-lookup"><span data-stu-id="fa060-595">Adjust the app code to [use the file system as a key ring store](xref:security/data-protection/configuration/overview).</span></span> <span data-ttu-id="fa060-596">K ochraně služby Key Ring použijte certifikát x509 a ujistěte se, že certifikát je důvěryhodný certifikát.</span><span class="sxs-lookup"><span data-stu-id="fa060-596">Use an X509 certificate to protect the key ring and ensure the certificate is a trusted certificate.</span></span> <span data-ttu-id="fa060-597">Pokud je certifikát podepsaný svým držitelem, umístěte certifikát do důvěryhodného kořenového úložiště.</span><span class="sxs-lookup"><span data-stu-id="fa060-597">If the certificate is self-signed, place the certificate in the Trusted Root store.</span></span>

  <span data-ttu-id="fa060-598">Při použití služby IIS ve webové farmě:</span><span class="sxs-lookup"><span data-stu-id="fa060-598">When using IIS in a web farm:</span></span>

  * <span data-ttu-id="fa060-599">Použijte sdílenou složku, ke které mají přístup všechny počítače.</span><span class="sxs-lookup"><span data-stu-id="fa060-599">Use a file share that all machines can access.</span></span>
  * <span data-ttu-id="fa060-600">Nasaďte do každého počítače certifikát x509.</span><span class="sxs-lookup"><span data-stu-id="fa060-600">Deploy an X509 certificate to each machine.</span></span> <span data-ttu-id="fa060-601">Konfigurace [ochrany dat v kódu](xref:security/data-protection/configuration/overview).</span><span class="sxs-lookup"><span data-stu-id="fa060-601">Configure [data protection in code](xref:security/data-protection/configuration/overview).</span></span>

* <span data-ttu-id="fa060-602">**Nastavení zásad pro ochranu dat na úrovni počítače**</span><span class="sxs-lookup"><span data-stu-id="fa060-602">**Set a machine-wide policy for data protection**</span></span>

  <span data-ttu-id="fa060-603">Systém ochrany dat má omezená podpora pro nastavení výchozích [zásad](xref:security/data-protection/configuration/machine-wide-policy) pro všechny aplikace, které využívají rozhraní API pro ochranu dat.</span><span class="sxs-lookup"><span data-stu-id="fa060-603">The data protection system has limited support for setting a default [machine-wide policy](xref:security/data-protection/configuration/machine-wide-policy) for all apps that consume the Data Protection APIs.</span></span> <span data-ttu-id="fa060-604">Další informace naleznete v tématu <xref:security/data-protection/introduction>.</span><span class="sxs-lookup"><span data-stu-id="fa060-604">For more information, see <xref:security/data-protection/introduction>.</span></span>

## <a name="virtual-directories"></a><span data-ttu-id="fa060-605">Virtuální adresáře</span><span class="sxs-lookup"><span data-stu-id="fa060-605">Virtual Directories</span></span>

<span data-ttu-id="fa060-606">Aplikace ASP.NET Core nepodporují [virtuální adresáře služby IIS](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) .</span><span class="sxs-lookup"><span data-stu-id="fa060-606">[IIS Virtual Directories](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) aren't supported with ASP.NET Core apps.</span></span> <span data-ttu-id="fa060-607">Aplikace může být hostována jako [dílčí aplikace](#sub-applications).</span><span class="sxs-lookup"><span data-stu-id="fa060-607">An app can be hosted as a [sub-application](#sub-applications).</span></span>

## <a name="sub-applications"></a><span data-ttu-id="fa060-608">Dílčí aplikace</span><span class="sxs-lookup"><span data-stu-id="fa060-608">Sub-applications</span></span>

<span data-ttu-id="fa060-609">Aplikace ASP.NET Core může být hostovaná jako [podaplikace služby IIS (dílčí aplikace)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications).</span><span class="sxs-lookup"><span data-stu-id="fa060-609">An ASP.NET Core app can be hosted as an [IIS sub-application (sub-app)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications).</span></span> <span data-ttu-id="fa060-610">Cesta k dílčí aplikaci se stala součástí adresy URL kořenové aplikace.</span><span class="sxs-lookup"><span data-stu-id="fa060-610">The sub-app's path becomes part of the root app's URL.</span></span>

<span data-ttu-id="fa060-611">Propojení statických prostředků v rámci dílčí aplikace by měla používat notaci vlnovku ( `~/` ).</span><span class="sxs-lookup"><span data-stu-id="fa060-611">Static asset links within the sub-app should use tilde-slash (`~/`) notation.</span></span> <span data-ttu-id="fa060-612">Znak tildy vlnovkou spustí [pomocnou nápovědu](xref:mvc/views/tag-helpers/intro) k předřazení pathbase dílčí aplikace na vykreslený relativní odkaz.</span><span class="sxs-lookup"><span data-stu-id="fa060-612">Tilde-slash notation triggers a [Tag Helper](xref:mvc/views/tag-helpers/intro) to prepend the sub-app's pathbase to the rendered relative link.</span></span> <span data-ttu-id="fa060-613">V případě dílčí aplikace na `/subapp_path` je obrázek propojený s objektem `src="~/image.png"` vykreslen jako `src="/subapp_path/image.png"` .</span><span class="sxs-lookup"><span data-stu-id="fa060-613">For a sub-app at `/subapp_path`, an image linked with `src="~/image.png"` is rendered as `src="/subapp_path/image.png"`.</span></span> <span data-ttu-id="fa060-614">Middleware statických souborů kořenové aplikace nezpracovává požadavek na statický soubor.</span><span class="sxs-lookup"><span data-stu-id="fa060-614">The root app's Static File Middleware doesn't process the static file request.</span></span> <span data-ttu-id="fa060-615">Požadavek zpracovává middleware statických souborů v dílčí aplikaci.</span><span class="sxs-lookup"><span data-stu-id="fa060-615">The request is processed by the sub-app's Static File Middleware.</span></span>

<span data-ttu-id="fa060-616">Pokud je atribut statického prostředku `src` nastavený na absolutní cestu (například `src="/image.png"` ), odkaz se vykreslí bez pathbase dílčí aplikace.</span><span class="sxs-lookup"><span data-stu-id="fa060-616">If a static asset's `src` attribute is set to an absolute path (for example, `src="/image.png"`), the link is rendered without the sub-app's pathbase.</span></span> <span data-ttu-id="fa060-617">Middleware statických souborů v kořenové aplikaci se pokusí o poskytování assetu z [kořenového adresáře webu](xref:fundamentals/index#web-root)kořenové aplikace, což má za následek *404 – nenalezené* odpovědi, pokud není k dispozici statický prostředek z kořenové aplikace.</span><span class="sxs-lookup"><span data-stu-id="fa060-617">The root app's Static File Middleware attempts to serve the asset from the root app's [web root](xref:fundamentals/index#web-root), which results in a *404 - Not Found* response unless the static asset is available from the root app.</span></span>

<span data-ttu-id="fa060-618">Hostování aplikace ASP.NET Core jako dílčí aplikace v jiné aplikaci ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="fa060-618">To host an ASP.NET Core app as a sub-app under another ASP.NET Core app:</span></span>

1. <span data-ttu-id="fa060-619">Vytvořte fond aplikací pro dílčí aplikaci.</span><span class="sxs-lookup"><span data-stu-id="fa060-619">Establish an app pool for the sub-app.</span></span> <span data-ttu-id="fa060-620">Nastavte **verzi .NET CLR** na **žádný spravovaný kód** , protože základní modul CLR (Common Language Runtime) pro .NET Core se spouští k hostování aplikace v pracovním procesu, ne CLR Desktop (.NET CLR).</span><span class="sxs-lookup"><span data-stu-id="fa060-620">Set the **.NET CLR Version** to **No Managed Code** because the Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process, not the desktop CLR (.NET CLR).</span></span>

1. <span data-ttu-id="fa060-621">Přidejte kořenovou lokalitu ve Správci služby IIS pomocí dílčí aplikace do složky v kořenové lokalitě.</span><span class="sxs-lookup"><span data-stu-id="fa060-621">Add the root site in IIS Manager with the sub-app in a folder under the root site.</span></span>

1. <span data-ttu-id="fa060-622">Klikněte pravým tlačítkem na složku dílčí aplikace ve Správci služby IIS a vyberte **převést na aplikaci**.</span><span class="sxs-lookup"><span data-stu-id="fa060-622">Right-click the sub-app folder in IIS Manager and select **Convert to Application**.</span></span>

1. <span data-ttu-id="fa060-623">V dialogovém okně **Přidat aplikaci** použijte pro **fond aplikací** tlačítko **Vybrat** , abyste přiřadili fond aplikací, který jste vytvořili pro dílčí aplikaci.</span><span class="sxs-lookup"><span data-stu-id="fa060-623">In the **Add Application** dialog, use the **Select** button for the **Application Pool** to assign the app pool that you created for the sub-app.</span></span> <span data-ttu-id="fa060-624">Vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="fa060-624">Select **OK**.</span></span>

<span data-ttu-id="fa060-625">Přiřazení samostatného fondu aplikací k dílčí aplikaci je požadavek při použití modelu hostování v rámci procesu.</span><span class="sxs-lookup"><span data-stu-id="fa060-625">The assignment of a separate app pool to the sub-app is a requirement when using the in-process hosting model.</span></span>

<span data-ttu-id="fa060-626">Další informace o modelu hostování v rámci procesu a konfiguraci modulu ASP.NET Core naleznete v tématu <xref:host-and-deploy/aspnet-core-module> .</span><span class="sxs-lookup"><span data-stu-id="fa060-626">For more information on the in-process hosting model and configuring the ASP.NET Core Module, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

## <a name="configuration-of-iis-with-webconfig"></a><span data-ttu-id="fa060-627">Konfigurace služby IIS pomocí souboru Web. config</span><span class="sxs-lookup"><span data-stu-id="fa060-627">Configuration of IIS with web.config</span></span>

<span data-ttu-id="fa060-628">Konfigurace služby IIS má vliv na `<system.webServer>` část souboru *Web. config* pro scénáře služby IIS, které jsou funkční pro ASP.NET Core aplikací s modulem ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="fa060-628">IIS configuration is influenced by the `<system.webServer>` section of *web.config* for IIS scenarios that are functional for ASP.NET Core apps with the ASP.NET Core Module.</span></span> <span data-ttu-id="fa060-629">Například konfigurace služby IIS je funkční pro dynamickou kompresi.</span><span class="sxs-lookup"><span data-stu-id="fa060-629">For example, IIS configuration is functional for dynamic compression.</span></span> <span data-ttu-id="fa060-630">Pokud je služba IIS nakonfigurovaná na úrovni serveru, aby používala dynamickou kompresi, `<urlCompression>` element v souboru *Web. config* aplikace ho může zakázat pro ASP.NET Core aplikaci.</span><span class="sxs-lookup"><span data-stu-id="fa060-630">If IIS is configured at the server level to use dynamic compression, the `<urlCompression>` element in the app's *web.config* file can disable it for an ASP.NET Core app.</span></span>

<span data-ttu-id="fa060-631">Další informace najdete v následujících tématech:</span><span class="sxs-lookup"><span data-stu-id="fa060-631">For more information, see the following topics:</span></span>

* [<span data-ttu-id="fa060-632">Odkaz na konfiguraci pro\<system.webServer></span><span class="sxs-lookup"><span data-stu-id="fa060-632">Configuration reference for \<system.webServer></span></span>](/iis/configuration/system.webServer/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>

<span data-ttu-id="fa060-633">Informace o nastavení proměnných prostředí pro jednotlivé aplikace spuštěné v izolovaných fondech aplikací (podporované pro IIS 10,0 nebo novější) najdete v části *příkaz Appcmd. exe* tématu [proměnné \<environmentVariables> prostředí](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) v referenční dokumentaci služby IIS.</span><span class="sxs-lookup"><span data-stu-id="fa060-633">To set environment variables for individual apps running in isolated app pools (supported for IIS 10.0 or later), see the *AppCmd.exe command* section of the [Environment Variables \<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) topic in the IIS reference documentation.</span></span>

## <a name="configuration-sections-of-webconfig"></a><span data-ttu-id="fa060-634">Konfigurační oddíly souboru Web. config</span><span class="sxs-lookup"><span data-stu-id="fa060-634">Configuration sections of web.config</span></span>

<span data-ttu-id="fa060-635">Konfigurační oddíly aplikací ASP.NET 4. x v *souboru Web. config* nepoužívá aplikace ASP.NET Core pro konfiguraci:</span><span class="sxs-lookup"><span data-stu-id="fa060-635">Configuration sections of ASP.NET 4.x apps in *web.config* aren't used by ASP.NET Core apps for configuration:</span></span>

* `<system.web>`
* `<appSettings>`
* `<connectionStrings>`
* `<location>`

<span data-ttu-id="fa060-636">Aplikace ASP.NET Core jsou nakonfigurovány pomocí jiných poskytovatelů konfigurace.</span><span class="sxs-lookup"><span data-stu-id="fa060-636">ASP.NET Core apps are configured using other configuration providers.</span></span> <span data-ttu-id="fa060-637">Další informace najdete v tématu [Konfigurace](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="fa060-637">For more information, see [Configuration](xref:fundamentals/configuration/index).</span></span>

## <a name="application-pools"></a><span data-ttu-id="fa060-638">Fondy aplikací</span><span class="sxs-lookup"><span data-stu-id="fa060-638">Application Pools</span></span>

<span data-ttu-id="fa060-639">Izolaci fondu aplikací určuje model hostování:</span><span class="sxs-lookup"><span data-stu-id="fa060-639">App pool isolation is determined by the hosting model:</span></span>

* <span data-ttu-id="fa060-640">Hostování v procesu: aplikace se vyžadují ke spuštění v samostatných fondech aplikací.</span><span class="sxs-lookup"><span data-stu-id="fa060-640">In-process hosting: Apps are required to run in separate app pools.</span></span>
* <span data-ttu-id="fa060-641">Hostování mimo procesy: aplikace doporučujeme izolovat od sebe navzájem tak, že spustíte každou aplikaci ve svém vlastním fondu aplikací.</span><span class="sxs-lookup"><span data-stu-id="fa060-641">Out-of-process hosting: We recommend isolating the apps from each other by running each app in its own app pool.</span></span>

<span data-ttu-id="fa060-642">Dialogové okno **Přidat web** do služby IIS je ve výchozím nastavení nastaveno na jeden fond aplikací na aplikaci.</span><span class="sxs-lookup"><span data-stu-id="fa060-642">The IIS **Add Website** dialog defaults to a single app pool per app.</span></span> <span data-ttu-id="fa060-643">Když je zadaný **název lokality** , text se automaticky přenese do textového pole **fondu aplikací** .</span><span class="sxs-lookup"><span data-stu-id="fa060-643">When a **Site name** is provided, the text is automatically transferred to the **Application pool** textbox.</span></span> <span data-ttu-id="fa060-644">Po přidání webu se vytvoří nový fond aplikací s názvem lokality.</span><span class="sxs-lookup"><span data-stu-id="fa060-644">A new app pool is created using the site name when the site is added.</span></span>

## <a name="application-pool-identity"></a><span data-ttu-id="fa060-645">Fond aplikacíIdentity</span><span class="sxs-lookup"><span data-stu-id="fa060-645">Application Pool Identity</span></span>

<span data-ttu-id="fa060-646">Účet identity fondu aplikací umožňuje aplikaci běžet v rámci jedinečného účtu, aniž by bylo nutné vytvářet a spravovat domény nebo místní účty.</span><span class="sxs-lookup"><span data-stu-id="fa060-646">An app pool identity account allows an app to run under a unique account without having to create and manage domains or local accounts.</span></span> <span data-ttu-id="fa060-647">Ve službě IIS 8,0 nebo novější služba pracovní proces správce služby IIS (WAS) vytvoří virtuální účet s názvem nového fondu aplikací a ve výchozím nastavení spustí pracovní procesy fondu aplikací v rámci tohoto účtu.</span><span class="sxs-lookup"><span data-stu-id="fa060-647">On IIS 8.0 or later, the IIS Admin Worker Process (WAS) creates a virtual account with the name of the new app pool and runs the app pool's worker processes under this account by default.</span></span> <span data-ttu-id="fa060-648">V konzole pro správu služby IIS v části **Upřesnit nastavení** fondu aplikací zajistěte, aby byl **Identity** nastaven na použití **ApplicationPoolIdentity**:</span><span class="sxs-lookup"><span data-stu-id="fa060-648">In the IIS Management Console under **Advanced Settings** for the app pool, ensure that the **Identity** is set to use **ApplicationPoolIdentity**:</span></span>

![Dialogové okno Upřesnit nastavení fondu aplikací](index/_static/apppool-identity.png)

<span data-ttu-id="fa060-650">Proces správy služby IIS vytvoří v systému zabezpečení systému Windows zabezpečený identifikátor s názvem fondu aplikací.</span><span class="sxs-lookup"><span data-stu-id="fa060-650">The IIS management process creates a secure identifier with the name of the app pool in the Windows Security System.</span></span> <span data-ttu-id="fa060-651">Prostředky je možné zabezpečit pomocí této identity.</span><span class="sxs-lookup"><span data-stu-id="fa060-651">Resources can be secured using this identity.</span></span> <span data-ttu-id="fa060-652">Tato identita ale není skutečným uživatelským účtem a nezobrazuje se v konzole pro správu uživatelů Windows.</span><span class="sxs-lookup"><span data-stu-id="fa060-652">However, this identity isn't a real user account and doesn't show up in the Windows User Management Console.</span></span>

<span data-ttu-id="fa060-653">Pokud pracovní proces služby IIS vyžaduje zvýšený přístup k aplikaci, upravte seznam Access Control (ACL) pro adresář obsahující aplikaci:</span><span class="sxs-lookup"><span data-stu-id="fa060-653">If the IIS worker process requires elevated access to the app, modify the Access Control List (ACL) for the directory containing the app:</span></span>

1. <span data-ttu-id="fa060-654">Otevřete Průzkumníka Windows a přejděte do adresáře.</span><span class="sxs-lookup"><span data-stu-id="fa060-654">Open Windows Explorer and navigate to the directory.</span></span>

1. <span data-ttu-id="fa060-655">Klikněte pravým tlačítkem na adresář a vyberte **vlastnosti**.</span><span class="sxs-lookup"><span data-stu-id="fa060-655">Right-click on the directory and select **Properties**.</span></span>

1. <span data-ttu-id="fa060-656">Na kartě **zabezpečení** vyberte tlačítko **Upravit** a pak klikněte na tlačítko **Přidat** .</span><span class="sxs-lookup"><span data-stu-id="fa060-656">Under the **Security** tab, select the **Edit** button and then the **Add** button.</span></span>

1. <span data-ttu-id="fa060-657">Vyberte tlačítko **umístění** a ujistěte se, že je vybraný systém.</span><span class="sxs-lookup"><span data-stu-id="fa060-657">Select the **Locations** button and make sure the system is selected.</span></span>

1. <span data-ttu-id="fa060-658">Do pole **Zadejte názvy objektů k výběru** oblasti zadejte \*\* \\<app_pool_name služby IIS AppPool>\*\* .</span><span class="sxs-lookup"><span data-stu-id="fa060-658">Enter **IIS AppPool\\<app_pool_name>** in **Enter the object names to select** area.</span></span> <span data-ttu-id="fa060-659">Vyberte tlačítko pro **kontrolu názvů** .</span><span class="sxs-lookup"><span data-stu-id="fa060-659">Select the **Check Names** button.</span></span> <span data-ttu-id="fa060-660">Pro službu *DefaultAppPool* ověřte názvy pomocí **služby IIS AppPool\DefaultAppPool**.</span><span class="sxs-lookup"><span data-stu-id="fa060-660">For the *DefaultAppPool* check the names using **IIS AppPool\DefaultAppPool**.</span></span> <span data-ttu-id="fa060-661">Když je vybráno tlačítko pro **kontrolu názvů** , hodnota **DefaultAppPool** je uvedena v oblasti názvy objektů.</span><span class="sxs-lookup"><span data-stu-id="fa060-661">When the **Check Names** button is selected, a value of **DefaultAppPool** is indicated in the object names area.</span></span> <span data-ttu-id="fa060-662">Není možné zadat název fondu aplikací přímo do oblasti názvy objektů.</span><span class="sxs-lookup"><span data-stu-id="fa060-662">It isn't possible to enter the app pool name directly into the object names area.</span></span> <span data-ttu-id="fa060-663">Při kontrole názvu objektu použijte \*\* \\<APP_POOL_NAME>formátu IIS AppPool\*\* .</span><span class="sxs-lookup"><span data-stu-id="fa060-663">Use the **IIS AppPool\\<app_pool_name>** format when checking for the object name.</span></span>

   ![Dialogová okna pro výběr uživatelů nebo skupin pro složku aplikace: název fondu aplikací "DefaultAppPool" se připojí k "rozhraní IIS AppPool" \" v oblasti názvy objektů před výběrem "kontrolovat jména".](index/_static/select-users-or-groups-1.png)

1. <span data-ttu-id="fa060-665">Vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="fa060-665">Select **OK**.</span></span>

   ![Dialogová okna pro výběr uživatelů nebo skupin pro složku aplikace: po výběru možnosti kontrolovat názvy se v oblasti názvy objektů zobrazí název objektu DefaultAppPool.](index/_static/select-users-or-groups-2.png)

1. <span data-ttu-id="fa060-667">&amp;Ve výchozím nastavení by měla být udělena oprávnění ke čtení.</span><span class="sxs-lookup"><span data-stu-id="fa060-667">Read &amp; execute permissions should be granted by default.</span></span> <span data-ttu-id="fa060-668">V případě potřeby zadejte další oprávnění.</span><span class="sxs-lookup"><span data-stu-id="fa060-668">Provide additional permissions as needed.</span></span>

<span data-ttu-id="fa060-669">Přístup se dá taky udělit na příkazovém řádku pomocí nástroje **Icacls** .</span><span class="sxs-lookup"><span data-stu-id="fa060-669">Access can also be granted at a command prompt using the **ICACLS** tool.</span></span> <span data-ttu-id="fa060-670">Pomocí příkazu *DefaultAppPool* jako příkladu se používá následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="fa060-670">Using the *DefaultAppPool* as an example, the following command is used:</span></span>

```console
ICACLS C:\sites\MyWebApp /grant "IIS AppPool\DefaultAppPool":F
```

<span data-ttu-id="fa060-671">Další informace najdete v tématu [Icacls](/windows-server/administration/windows-commands/icacls) .</span><span class="sxs-lookup"><span data-stu-id="fa060-671">For more information, see the [icacls](/windows-server/administration/windows-commands/icacls) topic.</span></span>

## <a name="http2-support"></a><span data-ttu-id="fa060-672">Podpora HTTP/2</span><span class="sxs-lookup"><span data-stu-id="fa060-672">HTTP/2 support</span></span>

<span data-ttu-id="fa060-673">[Http/2](https://httpwg.org/specs/rfc7540.html) se podporuje s ASP.NET Core v následujících scénářích nasazení služby IIS:</span><span class="sxs-lookup"><span data-stu-id="fa060-673">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is supported with ASP.NET Core in the following IIS deployment scenarios:</span></span>

* <span data-ttu-id="fa060-674">V procesu</span><span class="sxs-lookup"><span data-stu-id="fa060-674">In-process</span></span>
  * <span data-ttu-id="fa060-675">Windows Server 2016/Windows 10 nebo novější; IIS 10 nebo novější</span><span class="sxs-lookup"><span data-stu-id="fa060-675">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="fa060-676">Připojení TLS 1,2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="fa060-676">TLS 1.2 or later connection</span></span>
* <span data-ttu-id="fa060-677">Mimo proces</span><span class="sxs-lookup"><span data-stu-id="fa060-677">Out-of-process</span></span>
  * <span data-ttu-id="fa060-678">Windows Server 2016/Windows 10 nebo novější; IIS 10 nebo novější</span><span class="sxs-lookup"><span data-stu-id="fa060-678">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="fa060-679">Veřejná připojení hraničních serverů používají protokol HTTP/2, ale připojení reverzního proxy [serveru Kestrel](xref:fundamentals/servers/kestrel) používá protokol HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="fa060-679">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to the [Kestrel server](xref:fundamentals/servers/kestrel) uses HTTP/1.1.</span></span>
  * <span data-ttu-id="fa060-680">Připojení TLS 1,2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="fa060-680">TLS 1.2 or later connection</span></span>

<span data-ttu-id="fa060-681">Pro nasazení v rámci procesu, když je vytvořeno připojení HTTP/2, sestavy [HttpRequest. Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) `HTTP/2` .</span><span class="sxs-lookup"><span data-stu-id="fa060-681">For an in-process deployment when an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span> <span data-ttu-id="fa060-682">Pro nasazení mimo proces, když je vytvořeno připojení HTTP/2, sestavy [HttpRequest. Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) `HTTP/1.1` .</span><span class="sxs-lookup"><span data-stu-id="fa060-682">For an out-of-process deployment when an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/1.1`.</span></span>

<span data-ttu-id="fa060-683">Další informace o modelech hostování v procesu a mimoprocesové procesy naleznete v tématu <xref:host-and-deploy/aspnet-core-module> .</span><span class="sxs-lookup"><span data-stu-id="fa060-683">For more information on the in-process and out-of-process hosting models, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="fa060-684">HTTP/2 je ve výchozím nastavení povolené.</span><span class="sxs-lookup"><span data-stu-id="fa060-684">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="fa060-685">Pokud není navázáno připojení HTTP/2, připojení se vrátí k HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="fa060-685">Connections fall back to HTTP/1.1 if an HTTP/2 connection isn't established.</span></span> <span data-ttu-id="fa060-686">Další informace o konfiguraci HTTP/2 v nasazeních služby IIS najdete v tématu [http/2 ve službě IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).</span><span class="sxs-lookup"><span data-stu-id="fa060-686">For more information on HTTP/2 configuration with IIS deployments, see [HTTP/2 on IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).</span></span>

## <a name="cors-preflight-requests"></a><span data-ttu-id="fa060-687">Požadavky na kontrolu před výstupem CORS</span><span class="sxs-lookup"><span data-stu-id="fa060-687">CORS preflight requests</span></span>

<span data-ttu-id="fa060-688">*Tato část platí jenom pro ASP.NET Core aplikace, které cílí na .NET Framework.*</span><span class="sxs-lookup"><span data-stu-id="fa060-688">*This section only applies to ASP.NET Core apps that target the .NET Framework.*</span></span>

<span data-ttu-id="fa060-689">Pro ASP.NET Core aplikaci, která cílí na .NET Framework, požadavky na možnosti nejsou ve výchozím nastavení ve službě IIS předány do aplikace.</span><span class="sxs-lookup"><span data-stu-id="fa060-689">For an ASP.NET Core app that targets the .NET Framework, OPTIONS requests aren't passed to the app by default in IIS.</span></span> <span data-ttu-id="fa060-690">Informace o tom, jak nakonfigurovat obslužné rutiny IIS aplikace v *souboru Web. config* , aby předávala požadavky na možnosti, najdete v tématu [Povolení žádostí o více zdrojů v ASP.NET webovém rozhraní API 2: jak CORS funguje](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works).</span><span class="sxs-lookup"><span data-stu-id="fa060-690">To learn how to configure the app's IIS handlers in *web.config* to pass OPTIONS requests, see [Enable cross-origin requests in ASP.NET Web API 2: How CORS Works](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works).</span></span>

## <a name="application-initialization-module-and-idle-timeout"></a><span data-ttu-id="fa060-691">Modul inicializace aplikace a časový limit nečinnosti</span><span class="sxs-lookup"><span data-stu-id="fa060-691">Application Initialization Module and Idle Timeout</span></span>

<span data-ttu-id="fa060-692">Při hostování ve službě IIS pomocí modulu ASP.NET Core verze 2:</span><span class="sxs-lookup"><span data-stu-id="fa060-692">When hosted in IIS by the ASP.NET Core Module version 2:</span></span>

* <span data-ttu-id="fa060-693">[Modul inicializace aplikace](#application-initialization-module): hostovaný [v procesu](#in-process-hosting-model) nebo [mimoprocesové](#out-of-process-hosting-model) aplikaci aplikace je možné nakonfigurovat tak, aby se automaticky spouštěla v pracovním procesu nebo na restartování serveru.</span><span class="sxs-lookup"><span data-stu-id="fa060-693">[Application Initialization Module](#application-initialization-module): App's hosted [in-process](#in-process-hosting-model) or [out-of-process](#out-of-process-hosting-model) can be configured to start automatically on a worker process restart or server restart.</span></span>
* <span data-ttu-id="fa060-694">[Časový limit nečinnosti](#idle-timeout): hostovaný [v procesu](#in-process-hosting-model) aplikace se dá nakonfigurovat tak, aby nevypršel časový limit v období nečinnosti.</span><span class="sxs-lookup"><span data-stu-id="fa060-694">[Idle Timeout](#idle-timeout): App's hosted [in-process](#in-process-hosting-model) can be configured not to timeout during periods of inactivity.</span></span>

### <a name="application-initialization-module"></a><span data-ttu-id="fa060-695">Modul inicializace aplikace</span><span class="sxs-lookup"><span data-stu-id="fa060-695">Application Initialization Module</span></span>

<span data-ttu-id="fa060-696">*Platí pro aplikace hostované v procesu a mimo proces.*</span><span class="sxs-lookup"><span data-stu-id="fa060-696">*Applies to apps hosted in-process and out-of-process.*</span></span>

<span data-ttu-id="fa060-697">[Inicializace aplikace IIS](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) je funkce služby IIS, která do aplikace pošle požadavek HTTP, když se spustí nebo recykluje fond aplikací.</span><span class="sxs-lookup"><span data-stu-id="fa060-697">[IIS Application Initialization](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) is an IIS feature that sends an HTTP request to the app when the app pool starts or is recycled.</span></span> <span data-ttu-id="fa060-698">Požadavek spustí spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="fa060-698">The request triggers the app to start.</span></span> <span data-ttu-id="fa060-699">Služba IIS ve výchozím nastavení vydá požadavek na kořenovou adresu URL aplikace ( `/` ) k inicializaci aplikace (Další informace o konfiguraci najdete v [dalších zdrojích](#application-initialization-module-and-idle-timeout-additional-resources) ).</span><span class="sxs-lookup"><span data-stu-id="fa060-699">By default, IIS issues a request to the app's root URL (`/`) to initialize the app (see the [additional resources](#application-initialization-module-and-idle-timeout-additional-resources) for more details on configuration).</span></span>

<span data-ttu-id="fa060-700">Potvrďte, že je povolená funkce role inicializace aplikace služby IIS:</span><span class="sxs-lookup"><span data-stu-id="fa060-700">Confirm that the IIS Application Initialization role feature in enabled:</span></span>

<span data-ttu-id="fa060-701">V systémech Windows 7 nebo novějších stolních počítačích při používání služby IIS v místním prostředí:</span><span class="sxs-lookup"><span data-stu-id="fa060-701">On Windows 7 or later desktop systems when using IIS locally:</span></span>

1. <span data-ttu-id="fa060-702">Přejděte na **Ovládací panely** > **programy** programy > **a funkce** > **zapnout nebo vypnout funkce systému Windows** (levá strana obrazovky).</span><span class="sxs-lookup"><span data-stu-id="fa060-702">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="fa060-703">Otevřete **Internetová informační služba** > **World Wide Web Services** > **funkce pro vývoj aplikací**webové služby.</span><span class="sxs-lookup"><span data-stu-id="fa060-703">Open **Internet Information Services** > **World Wide Web Services** > **Application Development Features**.</span></span>
1. <span data-ttu-id="fa060-704">Zaškrtněte políčko pro **inicializaci aplikace**.</span><span class="sxs-lookup"><span data-stu-id="fa060-704">Select the check box for **Application Initialization**.</span></span>

<span data-ttu-id="fa060-705">V systému Windows Server 2008 R2 nebo novějším:</span><span class="sxs-lookup"><span data-stu-id="fa060-705">On Windows Server 2008 R2 or later:</span></span>

1. <span data-ttu-id="fa060-706">Otevřete **Průvodce přidáním rolí a funkcí**.</span><span class="sxs-lookup"><span data-stu-id="fa060-706">Open the **Add Roles and Features Wizard**.</span></span>
1. <span data-ttu-id="fa060-707">Na panelu **Vybrat služby rolí** otevřete uzel **vývoj aplikací** .</span><span class="sxs-lookup"><span data-stu-id="fa060-707">In the **Select role services** panel, open the **Application Development** node.</span></span>
1. <span data-ttu-id="fa060-708">Zaškrtněte políčko pro **inicializaci aplikace**.</span><span class="sxs-lookup"><span data-stu-id="fa060-708">Select the check box for **Application Initialization**.</span></span>

<span data-ttu-id="fa060-709">Pomocí některého z následujících přístupů povolte modul inicializace aplikace pro danou lokalitu:</span><span class="sxs-lookup"><span data-stu-id="fa060-709">Use either of the following approaches to enable the Application Initialization Module for the site:</span></span>

* <span data-ttu-id="fa060-710">Pomocí Správce služby IIS:</span><span class="sxs-lookup"><span data-stu-id="fa060-710">Using IIS Manager:</span></span>

  1. <span data-ttu-id="fa060-711">Na panelu **připojení** vyberte **fondy aplikací** .</span><span class="sxs-lookup"><span data-stu-id="fa060-711">Select **Application Pools** in the **Connections** panel.</span></span>
  1. <span data-ttu-id="fa060-712">V seznamu klikněte pravým tlačítkem na fond aplikací aplikace a vyberte **Upřesnit nastavení**.</span><span class="sxs-lookup"><span data-stu-id="fa060-712">Right-click the app's app pool in the list and select **Advanced Settings**.</span></span>
  1. <span data-ttu-id="fa060-713">Výchozí **režim spuštění** je **OnDemand**.</span><span class="sxs-lookup"><span data-stu-id="fa060-713">The default **Start Mode** is **OnDemand**.</span></span> <span data-ttu-id="fa060-714">Nastavte **režim Start** na **AlwaysRunning**.</span><span class="sxs-lookup"><span data-stu-id="fa060-714">Set the **Start Mode** to **AlwaysRunning**.</span></span> <span data-ttu-id="fa060-715">Vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="fa060-715">Select **OK**.</span></span>
  1. <span data-ttu-id="fa060-716">Otevřete uzel **lokality** na panelu **připojení** .</span><span class="sxs-lookup"><span data-stu-id="fa060-716">Open the **Sites** node in the **Connections** panel.</span></span>
  1. <span data-ttu-id="fa060-717">Klikněte pravým tlačítkem na aplikaci a vyberte **Spravovat** > **Rozšířená nastavení**webu.</span><span class="sxs-lookup"><span data-stu-id="fa060-717">Right-click the app and select **Manage Website** > **Advanced Settings**.</span></span>
  1. <span data-ttu-id="fa060-718">Výchozí nastavení pro **Povolení přednačtení** je **false**.</span><span class="sxs-lookup"><span data-stu-id="fa060-718">The default **Preload Enabled** setting is **False**.</span></span> <span data-ttu-id="fa060-719">Nastavte **předběžné načtení povoleno** na **hodnotu true**.</span><span class="sxs-lookup"><span data-stu-id="fa060-719">Set **Preload Enabled** to **True**.</span></span> <span data-ttu-id="fa060-720">Vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="fa060-720">Select **OK**.</span></span>

* <span data-ttu-id="fa060-721">Pomocí souboru *Web. config*přidejte `<applicationInitialization>` element s `doAppInitAfterRestart` nastavením do `true` `<system.webServer>` prvků v souboru *Web. config* aplikace:</span><span class="sxs-lookup"><span data-stu-id="fa060-721">Using *web.config*, add the `<applicationInitialization>` element with `doAppInitAfterRestart` set to `true` to the `<system.webServer>` elements in the app's *web.config* file:</span></span>

  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <configuration>
    <location path="." inheritInChildApplications="false">
      <system.webServer>
        <applicationInitialization doAppInitAfterRestart="true" />
      </system.webServer>
    </location>
  </configuration>
  ```

### <a name="idle-timeout"></a><span data-ttu-id="fa060-722">Časový limit nečinnosti</span><span class="sxs-lookup"><span data-stu-id="fa060-722">Idle Timeout</span></span>

<span data-ttu-id="fa060-723">*Platí jenom pro aplikace hostované v procesu.*</span><span class="sxs-lookup"><span data-stu-id="fa060-723">*Only applies to apps hosted in-process.*</span></span>

<span data-ttu-id="fa060-724">Pokud chcete zabránit volnoběhu aplikace, nastavte časový limit nečinnosti fondu aplikací pomocí Správce služby IIS:</span><span class="sxs-lookup"><span data-stu-id="fa060-724">To prevent the app from idling, set the app pool's idle timeout using IIS Manager:</span></span>

1. <span data-ttu-id="fa060-725">Na panelu **připojení** vyberte **fondy aplikací** .</span><span class="sxs-lookup"><span data-stu-id="fa060-725">Select **Application Pools** in the **Connections** panel.</span></span>
1. <span data-ttu-id="fa060-726">V seznamu klikněte pravým tlačítkem na fond aplikací aplikace a vyberte **Upřesnit nastavení**.</span><span class="sxs-lookup"><span data-stu-id="fa060-726">Right-click the app's app pool in the list and select **Advanced Settings**.</span></span>
1. <span data-ttu-id="fa060-727">Výchozí **časový limit nečinnosti (minuty)** je **20** minut.</span><span class="sxs-lookup"><span data-stu-id="fa060-727">The default **Idle Time-out (minutes)** is **20** minutes.</span></span> <span data-ttu-id="fa060-728">Nastavte **časový limit nečinnosti (minuty)** na **0** (nula).</span><span class="sxs-lookup"><span data-stu-id="fa060-728">Set the **Idle Time-out (minutes)** to **0** (zero).</span></span> <span data-ttu-id="fa060-729">Vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="fa060-729">Select **OK**.</span></span>
1. <span data-ttu-id="fa060-730">Recyklujte pracovní proces.</span><span class="sxs-lookup"><span data-stu-id="fa060-730">Recycle the worker process.</span></span>

<span data-ttu-id="fa060-731">Chcete-li zabránit aplikacím hostovaným v [procesu](#out-of-process-hosting-model) vypršení časového limitu, použijte některý z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="fa060-731">To prevent apps hosted [out-of-process](#out-of-process-hosting-model) from timing out, use either of the following approaches:</span></span>

* <span data-ttu-id="fa060-732">Pomocí příkazu otestujete aplikaci z externí služby, aby byla spuštěná.</span><span class="sxs-lookup"><span data-stu-id="fa060-732">Ping the app from an external service in order to keep it running.</span></span>
* <span data-ttu-id="fa060-733">Pokud aplikace hostuje jenom služby na pozadí, vyhněte se hostování služby IIS a použití [služby Windows k hostování aplikace ASP.NET Core](xref:host-and-deploy/windows-service).</span><span class="sxs-lookup"><span data-stu-id="fa060-733">If the app only hosts background services, avoid IIS hosting and use a [Windows Service to host the ASP.NET Core app](xref:host-and-deploy/windows-service).</span></span>

### <a name="application-initialization-module-and-idle-timeout-additional-resources"></a><span data-ttu-id="fa060-734">Modul inicializace aplikace a další prostředky časového limitu nečinnosti</span><span class="sxs-lookup"><span data-stu-id="fa060-734">Application Initialization Module and Idle Timeout additional resources</span></span>

* [<span data-ttu-id="fa060-735">Inicializace aplikace IIS 8,0</span><span class="sxs-lookup"><span data-stu-id="fa060-735">IIS 8.0 Application Initialization</span></span>](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization)
* <span data-ttu-id="fa060-736">[Inicializace \<applicationInitialization> aplikace ](/iis/configuration/system.webserver/applicationinitialization/)</span><span class="sxs-lookup"><span data-stu-id="fa060-736">[Application Initialization \<applicationInitialization>](/iis/configuration/system.webserver/applicationinitialization/).</span></span>
* <span data-ttu-id="fa060-737">[Nastavení modelu procesu pro \<processModel> fond aplikací ](/iis/configuration/system.applicationhost/applicationpools/add/processmodel).</span><span class="sxs-lookup"><span data-stu-id="fa060-737">[Process Model Settings for an Application Pool \<processModel>](/iis/configuration/system.applicationhost/applicationpools/add/processmodel).</span></span>

## <a name="deployment-resources-for-iis-administrators"></a><span data-ttu-id="fa060-738">Prostředky nasazení pro správce služby IIS</span><span class="sxs-lookup"><span data-stu-id="fa060-738">Deployment resources for IIS administrators</span></span>

* [<span data-ttu-id="fa060-739">Dokumentace ke službě IIS</span><span class="sxs-lookup"><span data-stu-id="fa060-739">IIS documentation</span></span>](/iis)
* [<span data-ttu-id="fa060-740">Začínáme ve službě IIS pomocí Správce služby IIS</span><span class="sxs-lookup"><span data-stu-id="fa060-740">Getting Started with the IIS Manager in IIS</span></span>](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* [<span data-ttu-id="fa060-741">Nasazení aplikace .NET Core</span><span class="sxs-lookup"><span data-stu-id="fa060-741">.NET Core application deployment</span></span>](/dotnet/core/deploying/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:host-and-deploy/iis/modules>
* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

## <a name="additional-resources"></a><span data-ttu-id="fa060-742">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="fa060-742">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:index>
* [<span data-ttu-id="fa060-743">Oficiální web Microsoft IIS</span><span class="sxs-lookup"><span data-stu-id="fa060-743">The Official Microsoft IIS Site</span></span>](https://www.iis.net/)
* [<span data-ttu-id="fa060-744">Knihovna technických obsahu pro Windows Server</span><span class="sxs-lookup"><span data-stu-id="fa060-744">Windows Server technical content library</span></span>](/windows-server/windows-server)
* [<span data-ttu-id="fa060-745">HTTP/2 ve službě IIS</span><span class="sxs-lookup"><span data-stu-id="fa060-745">HTTP/2 on IIS</span></span>](/iis/get-started/whats-new-in-iis-10/http2-on-iis)
* <xref:host-and-deploy/iis/transform-webconfig>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="fa060-746">Kurz týkající se publikování ASP.NET Core aplikace na server služby IIS najdete v tématu <xref:tutorials/publish-to-iis> .</span><span class="sxs-lookup"><span data-stu-id="fa060-746">For a tutorial experience on publishing an ASP.NET Core app to an IIS server, see <xref:tutorials/publish-to-iis>.</span></span>

[<span data-ttu-id="fa060-747">Instalace hostující sady .NET Core</span><span class="sxs-lookup"><span data-stu-id="fa060-747">Install the .NET Core Hosting Bundle</span></span>](#install-the-net-core-hosting-bundle)

## <a name="supported-operating-systems"></a><span data-ttu-id="fa060-748">Podporované operační systémy</span><span class="sxs-lookup"><span data-stu-id="fa060-748">Supported operating systems</span></span>

<span data-ttu-id="fa060-749">Podporovány jsou následující operační systémy:</span><span class="sxs-lookup"><span data-stu-id="fa060-749">The following operating systems are supported:</span></span>

* <span data-ttu-id="fa060-750">Windows 7 nebo novější</span><span class="sxs-lookup"><span data-stu-id="fa060-750">Windows 7 or later</span></span>
* <span data-ttu-id="fa060-751">Windows Server 2008 R2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="fa060-751">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="fa060-752">[Http. sys Server](xref:fundamentals/servers/httpsys) (dříve označovaný jako weblisten) nefunguje v konfiguraci reverzního proxy serveru se službou IIS.</span><span class="sxs-lookup"><span data-stu-id="fa060-752">[HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener) doesn't work in a reverse proxy configuration with IIS.</span></span> <span data-ttu-id="fa060-753">Použijte [Server Kestrel](xref:fundamentals/servers/kestrel).</span><span class="sxs-lookup"><span data-stu-id="fa060-753">Use the [Kestrel server](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="fa060-754">Informace o hostování v Azure najdete v tématu <xref:host-and-deploy/azure-apps/index> .</span><span class="sxs-lookup"><span data-stu-id="fa060-754">For information on hosting in Azure, see <xref:host-and-deploy/azure-apps/index>.</span></span>

<span data-ttu-id="fa060-755">Pokyny k řešení potíží najdete v tématu <xref:test/troubleshoot> .</span><span class="sxs-lookup"><span data-stu-id="fa060-755">For troubleshooting guidance, see <xref:test/troubleshoot>.</span></span>

## <a name="supported-platforms"></a><span data-ttu-id="fa060-756">Podporované platformy</span><span class="sxs-lookup"><span data-stu-id="fa060-756">Supported platforms</span></span>

<span data-ttu-id="fa060-757">Podporují se aplikace publikované pro nasazení 32 (x86) nebo 64-bit (x64).</span><span class="sxs-lookup"><span data-stu-id="fa060-757">Apps published for 32-bit (x86) or 64-bit (x64) deployment are supported.</span></span> <span data-ttu-id="fa060-758">Nasazení 32 aplikace s 32 (x86) .NET Core SDK, pokud aplikace:</span><span class="sxs-lookup"><span data-stu-id="fa060-758">Deploy a 32-bit app with a 32-bit (x86) .NET Core SDK unless the app:</span></span>

* <span data-ttu-id="fa060-759">Vyžaduje větší dostupný adresní prostor virtuální paměti pro 64 aplikaci.</span><span class="sxs-lookup"><span data-stu-id="fa060-759">Requires the larger virtual memory address space available to a 64-bit app.</span></span>
* <span data-ttu-id="fa060-760">Vyžaduje větší velikost zásobníku služby IIS.</span><span class="sxs-lookup"><span data-stu-id="fa060-760">Requires the larger IIS stack size.</span></span>
* <span data-ttu-id="fa060-761">Má 64 nativní závislosti.</span><span class="sxs-lookup"><span data-stu-id="fa060-761">Has 64-bit native dependencies.</span></span>

<span data-ttu-id="fa060-762">K publikování 64 aplikace použijte 64 .NET Core SDK (x64).</span><span class="sxs-lookup"><span data-stu-id="fa060-762">Use a 64-bit (x64) .NET Core SDK to publish a 64-bit app.</span></span> <span data-ttu-id="fa060-763">V hostitelském systému musí být nainstalován 64 modul runtime.</span><span class="sxs-lookup"><span data-stu-id="fa060-763">A 64-bit runtime must be present on the host system.</span></span>

## <a name="hosting-models"></a><span data-ttu-id="fa060-764">Modely hostingu</span><span class="sxs-lookup"><span data-stu-id="fa060-764">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="fa060-765">Model hostování v procesu</span><span class="sxs-lookup"><span data-stu-id="fa060-765">In-process hosting model</span></span>

<span data-ttu-id="fa060-766">Při použití hostování v rámci procesu ASP.NET Core aplikace běží ve stejném procesu jako jeho pracovní proces služby IIS.</span><span class="sxs-lookup"><span data-stu-id="fa060-766">Using in-process hosting, an ASP.NET Core app runs in the same process as its IIS worker process.</span></span> <span data-ttu-id="fa060-767">Hostování v rámci procesů poskytují lepší výkon než hostování mimo procesy z těchto důvodů:</span><span class="sxs-lookup"><span data-stu-id="fa060-767">In-process hosting provides improved performance over out-of-process hosting because:</span></span>

* <span data-ttu-id="fa060-768">Žádosti nejsou proxy serverem přes adaptér zpětné smyčky.</span><span class="sxs-lookup"><span data-stu-id="fa060-768">Requests aren't proxied over the loopback adapter.</span></span> <span data-ttu-id="fa060-769">Adaptér zpětné smyčky je síťové rozhraní, které vrátí odchozí síťový provoz zpátky do stejného počítače.</span><span class="sxs-lookup"><span data-stu-id="fa060-769">A loopback adapter is a network interface that returns outgoing network traffic back to the same machine.</span></span>

<span data-ttu-id="fa060-770">Služba IIS zpracovává správu procesů pomocí [aktivační služby procesů systému Windows (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span><span class="sxs-lookup"><span data-stu-id="fa060-770">IIS handles process management with the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="fa060-771">[Modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module):</span><span class="sxs-lookup"><span data-stu-id="fa060-771">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module):</span></span>

* <span data-ttu-id="fa060-772">Provede inicializaci aplikace.</span><span class="sxs-lookup"><span data-stu-id="fa060-772">Performs app initialization.</span></span>
  * <span data-ttu-id="fa060-773">Načte [CoreCLR](/dotnet/standard/glossary#coreclr).</span><span class="sxs-lookup"><span data-stu-id="fa060-773">Loads the [CoreCLR](/dotnet/standard/glossary#coreclr).</span></span>
  * <span data-ttu-id="fa060-774">Volání `Program.Main` .</span><span class="sxs-lookup"><span data-stu-id="fa060-774">Calls `Program.Main`.</span></span>
* <span data-ttu-id="fa060-775">Zpracovává životnost nativního požadavku služby IIS.</span><span class="sxs-lookup"><span data-stu-id="fa060-775">Handles the lifetime of the IIS native request.</span></span>

<span data-ttu-id="fa060-776">Model hostování v rámci procesu není podporován pro aplikace ASP.NET Core, které cílí na .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="fa060-776">The in-process hosting model isn't supported for ASP.NET Core apps that target the .NET Framework.</span></span>

<span data-ttu-id="fa060-777">Následující diagram znázorňuje vztah mezi službou IIS, modulem ASP.NET Core a aplikací hostovanou v procesu:</span><span class="sxs-lookup"><span data-stu-id="fa060-777">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted in-process:</span></span>

![ASP.NET Core modul ve scénáři hostování v rámci procesu](index/_static/ancm-inprocess.png)

<span data-ttu-id="fa060-779">Požadavek přijde z webu do ovladače HTTP. sys v režimu jádra.</span><span class="sxs-lookup"><span data-stu-id="fa060-779">A request arrives from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="fa060-780">Ovladač směruje nativní požadavek na IIS na konfigurovaném portu webu, obvykle 80 (HTTP) nebo 443 (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="fa060-780">The driver routes the native request to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="fa060-781">Modul ASP.NET Core obdrží nativní požadavek a předá ho k serveru HTTP služby IIS ( `IISHttpServer` ).</span><span class="sxs-lookup"><span data-stu-id="fa060-781">The ASP.NET Core Module receives the native request and passes it to IIS HTTP Server (`IISHttpServer`).</span></span> <span data-ttu-id="fa060-782">HTTP server IIS je vnitroprocesové implementace v rámci procesového serveru pro službu IIS, která převádí požadavek z nativního na spravovanou.</span><span class="sxs-lookup"><span data-stu-id="fa060-782">IIS HTTP Server is an in-process server implementation for IIS that converts the request from native to managed.</span></span>

<span data-ttu-id="fa060-783">Poté, co server HTTP služby IIS požadavek zpracuje, je požadavek vložen do kanálu middleware ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="fa060-783">After the IIS HTTP Server processes the request, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="fa060-784">Kanál middlewaru zpracovává požadavek a předá ho jako `HttpContext` instanci do logiky aplikace.</span><span class="sxs-lookup"><span data-stu-id="fa060-784">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="fa060-785">Odpověď aplikace se předává zpět službě IIS prostřednictvím serveru IIS HTTP.</span><span class="sxs-lookup"><span data-stu-id="fa060-785">The app's response is passed back to IIS through IIS HTTP Server.</span></span> <span data-ttu-id="fa060-786">Služba IIS odešle odpověď klientovi, který žádost inicioval.</span><span class="sxs-lookup"><span data-stu-id="fa060-786">IIS sends the response to the client that initiated the request.</span></span>

<span data-ttu-id="fa060-787">Vnitroprocesové hostování v rámci procesu je výslovný souhlas pro existující aplikace, ale [dotnet nové](/dotnet/core/tools/dotnet-new) šablony jsou výchozí pro všechny scénáře hostování v rámci procesu pro všechny služby IIS a IIS Express.</span><span class="sxs-lookup"><span data-stu-id="fa060-787">In-process hosting is opt-in for existing apps, but [dotnet new](/dotnet/core/tools/dotnet-new) templates default to the in-process hosting model for all IIS and IIS Express scenarios.</span></span>

<span data-ttu-id="fa060-788">`CreateDefaultBuilder`přidá <xref:Microsoft.AspNetCore.Hosting.Server.IServer> instanci voláním <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> metody pro spuštění [CoreCLR](/dotnet/standard/glossary#coreclr) a hostování aplikace uvnitř pracovního procesu služby IIS (*W3wp. exe* nebo *IISExpress. exe*).</span><span class="sxs-lookup"><span data-stu-id="fa060-788">`CreateDefaultBuilder` adds an <xref:Microsoft.AspNetCore.Hosting.Server.IServer> instance by calling the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> method to boot the [CoreCLR](/dotnet/standard/glossary#coreclr) and host the app inside of the IIS worker process (*w3wp.exe* or *iisexpress.exe*).</span></span> <span data-ttu-id="fa060-789">Testy výkonu označují, že hostování aplikace .NET Core v rámci procesu přináší výrazně vyšší propustnost žádostí v porovnání s hostováním aplikací mimo proces a požadavky na proxy serveru na [Kestrel](xref:fundamentals/servers/kestrel) Server.</span><span class="sxs-lookup"><span data-stu-id="fa060-789">Performance tests indicate that hosting a .NET Core app in-process delivers significantly higher request throughput compared to hosting the app out-of-process and proxying requests to [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span>

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="fa060-790">Model hostování mimo proces</span><span class="sxs-lookup"><span data-stu-id="fa060-790">Out-of-process hosting model</span></span>

<span data-ttu-id="fa060-791">Vzhledem k tomu, že ASP.NET Core aplikace běží v procesu odděleném od pracovního procesu služby IIS, modul ASP.NET Core zpracovává správu procesů.</span><span class="sxs-lookup"><span data-stu-id="fa060-791">Because ASP.NET Core apps run in a process separate from the IIS worker process, the ASP.NET Core Module handles process management.</span></span> <span data-ttu-id="fa060-792">Modul spustí proces pro aplikaci ASP.NET Core, když první požadavek dorazí a restartuje aplikaci, pokud se ukončí nebo dojde k chybě.</span><span class="sxs-lookup"><span data-stu-id="fa060-792">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="fa060-793">To je v podstatě stejné chování jako u aplikací, které běží v procesu, které jsou spravovány službou was [(Windows Process Activation Service)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span><span class="sxs-lookup"><span data-stu-id="fa060-793">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="fa060-794">Následující diagram znázorňuje vztah mezi službou IIS, modulem ASP.NET Core a aplikací hostovanou mimo proces:</span><span class="sxs-lookup"><span data-stu-id="fa060-794">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted out-of-process:</span></span>

![ASP.NET Core modul ve scénáři hostování mimo proces](index/_static/ancm-outofprocess.png)

<span data-ttu-id="fa060-796">Požadavky přicházející z webu do ovladače HTTP. sys v režimu jádra.</span><span class="sxs-lookup"><span data-stu-id="fa060-796">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="fa060-797">Ovladač směruje požadavky do služby IIS na konfigurovaném portu webu, obvykle 80 (HTTP) nebo 443 (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="fa060-797">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="fa060-798">Modul předá požadavky do Kestrel na náhodném portu pro aplikaci, což není port 80 nebo 443.</span><span class="sxs-lookup"><span data-stu-id="fa060-798">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="fa060-799">Modul Určuje port přes proměnnou prostředí při spuštění a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> rozšíření nakonfiguruje server tak, aby naslouchal `http://localhost:{PORT}` .</span><span class="sxs-lookup"><span data-stu-id="fa060-799">The module specifies the port via an environment variable at startup, and the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> extension configures the server to listen on `http://localhost:{PORT}`.</span></span> <span data-ttu-id="fa060-800">Budou provedeny další kontroly a požadavky, které nepocházejí z modulu, jsou odmítnuty.</span><span class="sxs-lookup"><span data-stu-id="fa060-800">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="fa060-801">Modul nepodporuje předávání HTTPS, takže požadavky se předávají přes protokol HTTP i v případě, že je služba IIS prostřednictvím protokolu HTTPS přijímá.</span><span class="sxs-lookup"><span data-stu-id="fa060-801">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="fa060-802">Po Kestrel žádosti z modulu se požadavek odešle do kanálu middlewaru ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="fa060-802">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="fa060-803">Kanál middlewaru zpracovává požadavek a předá ho jako `HttpContext` instanci do logiky aplikace.</span><span class="sxs-lookup"><span data-stu-id="fa060-803">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="fa060-804">Middleware přidaný integrací služby IIS: aktualizace schématu, vzdálené IP adresy a pathbase pro přesměrování požadavku do Kestrel.</span><span class="sxs-lookup"><span data-stu-id="fa060-804">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="fa060-805">Odpověď aplikace se předává zpátky do služby IIS, která ji přenáší zpátky do klienta HTTP, který žádost inicioval.</span><span class="sxs-lookup"><span data-stu-id="fa060-805">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="fa060-806">Pokyny ke konfiguraci ASP.NET Core modulu najdete v tématu <xref:host-and-deploy/aspnet-core-module> .</span><span class="sxs-lookup"><span data-stu-id="fa060-806">For ASP.NET Core Module configuration guidance, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="fa060-807">Další informace o hostování najdete v tématu [hostitel v ASP.NET Core](xref:fundamentals/index#host).</span><span class="sxs-lookup"><span data-stu-id="fa060-807">For more information on hosting, see [Host in ASP.NET Core](xref:fundamentals/index#host).</span></span>

## <a name="application-configuration"></a><span data-ttu-id="fa060-808">Konfigurace aplikací</span><span class="sxs-lookup"><span data-stu-id="fa060-808">Application configuration</span></span>

### <a name="enable-the-iisintegration-components"></a><span data-ttu-id="fa060-809">Povolit komponenty IISIntegration</span><span class="sxs-lookup"><span data-stu-id="fa060-809">Enable the IISIntegration components</span></span>

<span data-ttu-id="fa060-810">Při sestavování hostitele v `CreateWebHostBuilder` (*program.cs*) zavolejte <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> na Povolit integraci služby IIS:</span><span class="sxs-lookup"><span data-stu-id="fa060-810">When building a host in `CreateWebHostBuilder` (*Program.cs*), call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> to enable IIS integration:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        ...
```

<span data-ttu-id="fa060-811">Další informace o `CreateDefaultBuilder` naleznete v tématu <xref:fundamentals/host/web-host#set-up-a-host> .</span><span class="sxs-lookup"><span data-stu-id="fa060-811">For more information on `CreateDefaultBuilder`, see <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

### <a name="iis-options"></a><span data-ttu-id="fa060-812">Možnosti služby IIS</span><span class="sxs-lookup"><span data-stu-id="fa060-812">IIS options</span></span>

<span data-ttu-id="fa060-813">**Model hostování v procesu**</span><span class="sxs-lookup"><span data-stu-id="fa060-813">**In-process hosting model**</span></span>

<span data-ttu-id="fa060-814">Pokud chcete nakonfigurovat možnosti serveru IIS, zahrňte do nástroje konfiguraci služby pro <xref:Microsoft.AspNetCore.Builder.IISServerOptions> <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*> .</span><span class="sxs-lookup"><span data-stu-id="fa060-814">To configure IIS Server options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISServerOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>.</span></span> <span data-ttu-id="fa060-815">Následující příklad zakáže AutomaticAuthentication:</span><span class="sxs-lookup"><span data-stu-id="fa060-815">The following example disables AutomaticAuthentication:</span></span>

```csharp
services.Configure<IISServerOptions>(options => 
{
    options.AutomaticAuthentication = false;
});
```

| <span data-ttu-id="fa060-816">Možnost</span><span class="sxs-lookup"><span data-stu-id="fa060-816">Option</span></span>                         | <span data-ttu-id="fa060-817">Výchozí</span><span class="sxs-lookup"><span data-stu-id="fa060-817">Default</span></span> | <span data-ttu-id="fa060-818">Nastavení</span><span class="sxs-lookup"><span data-stu-id="fa060-818">Setting</span></span> |
| ---
<span data-ttu-id="fa060-819">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="fa060-819">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fa060-820">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fa060-820">'Blazor'</span></span>
- <span data-ttu-id="fa060-821">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fa060-821">'Identity'</span></span>
- <span data-ttu-id="fa060-822">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fa060-822">'Let's Encrypt'</span></span>
- <span data-ttu-id="fa060-823">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fa060-823">'Razor'</span></span>
- <span data-ttu-id="fa060-824">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="fa060-824">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fa060-825">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="fa060-825">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fa060-826">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fa060-826">'Blazor'</span></span>
- <span data-ttu-id="fa060-827">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fa060-827">'Identity'</span></span>
- <span data-ttu-id="fa060-828">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fa060-828">'Let's Encrypt'</span></span>
- <span data-ttu-id="fa060-829">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fa060-829">'Razor'</span></span>
- <span data-ttu-id="fa060-830">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="fa060-830">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fa060-831">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="fa060-831">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fa060-832">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fa060-832">'Blazor'</span></span>
- <span data-ttu-id="fa060-833">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fa060-833">'Identity'</span></span>
- <span data-ttu-id="fa060-834">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fa060-834">'Let's Encrypt'</span></span>
- <span data-ttu-id="fa060-835">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fa060-835">'Razor'</span></span>
- <span data-ttu-id="fa060-836">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="fa060-836">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fa060-837">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="fa060-837">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fa060-838">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fa060-838">'Blazor'</span></span>
- <span data-ttu-id="fa060-839">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fa060-839">'Identity'</span></span>
- <span data-ttu-id="fa060-840">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fa060-840">'Let's Encrypt'</span></span>
- <span data-ttu-id="fa060-841">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fa060-841">'Razor'</span></span>
- <span data-ttu-id="fa060-842">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="fa060-842">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fa060-843">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="fa060-843">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fa060-844">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fa060-844">'Blazor'</span></span>
- <span data-ttu-id="fa060-845">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fa060-845">'Identity'</span></span>
- <span data-ttu-id="fa060-846">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fa060-846">'Let's Encrypt'</span></span>
- <span data-ttu-id="fa060-847">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fa060-847">'Razor'</span></span>
- <span data-ttu-id="fa060-848">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="fa060-848">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fa060-849">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="fa060-849">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fa060-850">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fa060-850">'Blazor'</span></span>
- <span data-ttu-id="fa060-851">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fa060-851">'Identity'</span></span>
- <span data-ttu-id="fa060-852">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fa060-852">'Let's Encrypt'</span></span>
- <span data-ttu-id="fa060-853">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fa060-853">'Razor'</span></span>
- <span data-ttu-id="fa060-854">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="fa060-854">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fa060-855">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="fa060-855">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fa060-856">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fa060-856">'Blazor'</span></span>
- <span data-ttu-id="fa060-857">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fa060-857">'Identity'</span></span>
- <span data-ttu-id="fa060-858">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fa060-858">'Let's Encrypt'</span></span>
- <span data-ttu-id="fa060-859">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fa060-859">'Razor'</span></span>
- <span data-ttu-id="fa060-860">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="fa060-860">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fa060-861">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="fa060-861">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fa060-862">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fa060-862">'Blazor'</span></span>
- <span data-ttu-id="fa060-863">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fa060-863">'Identity'</span></span>
- <span data-ttu-id="fa060-864">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fa060-864">'Let's Encrypt'</span></span>
- <span data-ttu-id="fa060-865">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fa060-865">'Razor'</span></span>
- <span data-ttu-id="fa060-866">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="fa060-866">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fa060-867">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="fa060-867">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fa060-868">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fa060-868">'Blazor'</span></span>
- <span data-ttu-id="fa060-869">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fa060-869">'Identity'</span></span>
- <span data-ttu-id="fa060-870">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fa060-870">'Let's Encrypt'</span></span>
- <span data-ttu-id="fa060-871">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fa060-871">'Razor'</span></span>
- <span data-ttu-id="fa060-872">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="fa060-872">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fa060-873">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="fa060-873">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fa060-874">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fa060-874">'Blazor'</span></span>
- <span data-ttu-id="fa060-875">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fa060-875">'Identity'</span></span>
- <span data-ttu-id="fa060-876">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fa060-876">'Let's Encrypt'</span></span>
- <span data-ttu-id="fa060-877">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fa060-877">'Razor'</span></span>
- <span data-ttu-id="fa060-878">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="fa060-878">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fa060-879">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="fa060-879">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fa060-880">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fa060-880">'Blazor'</span></span>
- <span data-ttu-id="fa060-881">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fa060-881">'Identity'</span></span>
- <span data-ttu-id="fa060-882">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fa060-882">'Let's Encrypt'</span></span>
- <span data-ttu-id="fa060-883">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fa060-883">'Razor'</span></span>
- <span data-ttu-id="fa060-884">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="fa060-884">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fa060-885">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="fa060-885">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fa060-886">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fa060-886">'Blazor'</span></span>
- <span data-ttu-id="fa060-887">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fa060-887">'Identity'</span></span>
- <span data-ttu-id="fa060-888">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fa060-888">'Let's Encrypt'</span></span>
- <span data-ttu-id="fa060-889">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fa060-889">'Razor'</span></span>
- <span data-ttu-id="fa060-890">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="fa060-890">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fa060-891">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="fa060-891">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fa060-892">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fa060-892">'Blazor'</span></span>
- <span data-ttu-id="fa060-893">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fa060-893">'Identity'</span></span>
- <span data-ttu-id="fa060-894">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fa060-894">'Let's Encrypt'</span></span>
- <span data-ttu-id="fa060-895">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fa060-895">'Razor'</span></span>
- <span data-ttu-id="fa060-896">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="fa060-896">'SignalR' uid:</span></span> 

<span data-ttu-id="fa060-897">--------------- | :-----: | ---Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="fa060-897">--------------- | :-----: | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fa060-898">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fa060-898">'Blazor'</span></span>
- <span data-ttu-id="fa060-899">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fa060-899">'Identity'</span></span>
- <span data-ttu-id="fa060-900">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fa060-900">'Let's Encrypt'</span></span>
- <span data-ttu-id="fa060-901">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fa060-901">'Razor'</span></span>
- <span data-ttu-id="fa060-902">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="fa060-902">'SignalR' uid:</span></span> 

<span data-ttu-id="fa060-903">---- | | `AutomaticAuthentication`      | `true`  | Pokud `true` Server IIS nastaví `HttpContext.User` ověřený [ověřováním systému Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="fa060-903">---- | | `AutomaticAuthentication`      | `true`  | If `true`, IIS Server sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="fa060-904">Pokud `false` Server poskytuje pouze identitu pro `HttpContext.User` a reaguje na výzvy, pokud je explicitně vyžádala `AuthenticationScheme` .</span><span class="sxs-lookup"><span data-stu-id="fa060-904">If `false`, the server only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="fa060-905">Aby mohla služba fungovat, musí být ve službě IIS povolená možnost ověřování systému Windows `AutomaticAuthentication` .</span><span class="sxs-lookup"><span data-stu-id="fa060-905">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="fa060-906">Další informace najdete v tématu [ověřování systému Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="fa060-906">For more information, see [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="fa060-907">| | `AuthenticationDisplayName`    | `null`  | Nastaví zobrazovaný název, který se zobrazí uživatelům na přihlašovacích stránkách.</span><span class="sxs-lookup"><span data-stu-id="fa060-907">| | `AuthenticationDisplayName`    | `null`  | Sets the display name shown to users on login pages.</span></span> |

<span data-ttu-id="fa060-908">**Model hostování mimo proces**</span><span class="sxs-lookup"><span data-stu-id="fa060-908">**Out-of-process hosting model**</span></span>

<span data-ttu-id="fa060-909">Pokud chcete nakonfigurovat možnosti služby IIS, zahrňte do nástroje konfiguraci služby pro <xref:Microsoft.AspNetCore.Builder.IISOptions> <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*> .</span><span class="sxs-lookup"><span data-stu-id="fa060-909">To configure IIS options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>.</span></span> <span data-ttu-id="fa060-910">Následující příklad zabrání aplikaci v naplnění `HttpContext.Connection.ClientCertificate` :</span><span class="sxs-lookup"><span data-stu-id="fa060-910">The following example prevents the app from populating `HttpContext.Connection.ClientCertificate`:</span></span>

```csharp
services.Configure<IISOptions>(options => 
{
    options.ForwardClientCertificate = false;
});
```

| <span data-ttu-id="fa060-911">Možnost</span><span class="sxs-lookup"><span data-stu-id="fa060-911">Option</span></span>                         | <span data-ttu-id="fa060-912">Výchozí</span><span class="sxs-lookup"><span data-stu-id="fa060-912">Default</span></span> | <span data-ttu-id="fa060-913">Nastavení</span><span class="sxs-lookup"><span data-stu-id="fa060-913">Setting</span></span> |
| ---
<span data-ttu-id="fa060-914">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="fa060-914">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fa060-915">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fa060-915">'Blazor'</span></span>
- <span data-ttu-id="fa060-916">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fa060-916">'Identity'</span></span>
- <span data-ttu-id="fa060-917">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fa060-917">'Let's Encrypt'</span></span>
- <span data-ttu-id="fa060-918">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fa060-918">'Razor'</span></span>
- <span data-ttu-id="fa060-919">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="fa060-919">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fa060-920">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="fa060-920">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fa060-921">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fa060-921">'Blazor'</span></span>
- <span data-ttu-id="fa060-922">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fa060-922">'Identity'</span></span>
- <span data-ttu-id="fa060-923">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fa060-923">'Let's Encrypt'</span></span>
- <span data-ttu-id="fa060-924">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fa060-924">'Razor'</span></span>
- <span data-ttu-id="fa060-925">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="fa060-925">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fa060-926">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="fa060-926">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fa060-927">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fa060-927">'Blazor'</span></span>
- <span data-ttu-id="fa060-928">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fa060-928">'Identity'</span></span>
- <span data-ttu-id="fa060-929">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fa060-929">'Let's Encrypt'</span></span>
- <span data-ttu-id="fa060-930">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fa060-930">'Razor'</span></span>
- <span data-ttu-id="fa060-931">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="fa060-931">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fa060-932">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="fa060-932">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fa060-933">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fa060-933">'Blazor'</span></span>
- <span data-ttu-id="fa060-934">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fa060-934">'Identity'</span></span>
- <span data-ttu-id="fa060-935">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fa060-935">'Let's Encrypt'</span></span>
- <span data-ttu-id="fa060-936">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fa060-936">'Razor'</span></span>
- <span data-ttu-id="fa060-937">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="fa060-937">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fa060-938">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="fa060-938">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fa060-939">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fa060-939">'Blazor'</span></span>
- <span data-ttu-id="fa060-940">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fa060-940">'Identity'</span></span>
- <span data-ttu-id="fa060-941">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fa060-941">'Let's Encrypt'</span></span>
- <span data-ttu-id="fa060-942">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fa060-942">'Razor'</span></span>
- <span data-ttu-id="fa060-943">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="fa060-943">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fa060-944">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="fa060-944">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fa060-945">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fa060-945">'Blazor'</span></span>
- <span data-ttu-id="fa060-946">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fa060-946">'Identity'</span></span>
- <span data-ttu-id="fa060-947">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fa060-947">'Let's Encrypt'</span></span>
- <span data-ttu-id="fa060-948">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fa060-948">'Razor'</span></span>
- <span data-ttu-id="fa060-949">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="fa060-949">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fa060-950">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="fa060-950">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fa060-951">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fa060-951">'Blazor'</span></span>
- <span data-ttu-id="fa060-952">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fa060-952">'Identity'</span></span>
- <span data-ttu-id="fa060-953">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fa060-953">'Let's Encrypt'</span></span>
- <span data-ttu-id="fa060-954">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fa060-954">'Razor'</span></span>
- <span data-ttu-id="fa060-955">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="fa060-955">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fa060-956">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="fa060-956">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fa060-957">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fa060-957">'Blazor'</span></span>
- <span data-ttu-id="fa060-958">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fa060-958">'Identity'</span></span>
- <span data-ttu-id="fa060-959">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fa060-959">'Let's Encrypt'</span></span>
- <span data-ttu-id="fa060-960">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fa060-960">'Razor'</span></span>
- <span data-ttu-id="fa060-961">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="fa060-961">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fa060-962">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="fa060-962">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fa060-963">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fa060-963">'Blazor'</span></span>
- <span data-ttu-id="fa060-964">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fa060-964">'Identity'</span></span>
- <span data-ttu-id="fa060-965">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fa060-965">'Let's Encrypt'</span></span>
- <span data-ttu-id="fa060-966">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fa060-966">'Razor'</span></span>
- <span data-ttu-id="fa060-967">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="fa060-967">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fa060-968">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="fa060-968">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fa060-969">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fa060-969">'Blazor'</span></span>
- <span data-ttu-id="fa060-970">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fa060-970">'Identity'</span></span>
- <span data-ttu-id="fa060-971">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fa060-971">'Let's Encrypt'</span></span>
- <span data-ttu-id="fa060-972">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fa060-972">'Razor'</span></span>
- <span data-ttu-id="fa060-973">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="fa060-973">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fa060-974">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="fa060-974">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fa060-975">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fa060-975">'Blazor'</span></span>
- <span data-ttu-id="fa060-976">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fa060-976">'Identity'</span></span>
- <span data-ttu-id="fa060-977">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fa060-977">'Let's Encrypt'</span></span>
- <span data-ttu-id="fa060-978">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fa060-978">'Razor'</span></span>
- <span data-ttu-id="fa060-979">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="fa060-979">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fa060-980">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="fa060-980">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fa060-981">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fa060-981">'Blazor'</span></span>
- <span data-ttu-id="fa060-982">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fa060-982">'Identity'</span></span>
- <span data-ttu-id="fa060-983">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fa060-983">'Let's Encrypt'</span></span>
- <span data-ttu-id="fa060-984">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fa060-984">'Razor'</span></span>
- <span data-ttu-id="fa060-985">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="fa060-985">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fa060-986">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="fa060-986">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fa060-987">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fa060-987">'Blazor'</span></span>
- <span data-ttu-id="fa060-988">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fa060-988">'Identity'</span></span>
- <span data-ttu-id="fa060-989">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fa060-989">'Let's Encrypt'</span></span>
- <span data-ttu-id="fa060-990">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fa060-990">'Razor'</span></span>
- <span data-ttu-id="fa060-991">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="fa060-991">'SignalR' uid:</span></span> 

<span data-ttu-id="fa060-992">--------------- | :-----: | ---Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="fa060-992">--------------- | :-----: | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fa060-993">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fa060-993">'Blazor'</span></span>
- <span data-ttu-id="fa060-994">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fa060-994">'Identity'</span></span>
- <span data-ttu-id="fa060-995">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fa060-995">'Let's Encrypt'</span></span>
- <span data-ttu-id="fa060-996">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fa060-996">'Razor'</span></span>
- <span data-ttu-id="fa060-997">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="fa060-997">'SignalR' uid:</span></span> 

<span data-ttu-id="fa060-998">---- | | `AutomaticAuthentication`      | `true`  | Pokud `true` [Služba IIS Integration middleware](#enable-the-iisintegration-components) nastaví ověření `HttpContext.User` [ověřováním systému Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="fa060-998">---- | | `AutomaticAuthentication`      | `true`  | If `true`, [IIS Integration Middleware](#enable-the-iisintegration-components) sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="fa060-999">`false`V případě, middleware poskytuje pouze identitu pro `HttpContext.User` a reaguje na výzvy, pokud je explicitně vyžádala `AuthenticationScheme` .</span><span class="sxs-lookup"><span data-stu-id="fa060-999">If `false`, the middleware only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="fa060-1000">Aby mohla služba fungovat, musí být ve službě IIS povolená možnost ověřování systému Windows `AutomaticAuthentication` .</span><span class="sxs-lookup"><span data-stu-id="fa060-1000">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="fa060-1001">Další informace najdete v tématu [ověřování systému Windows](xref:security/authentication/windowsauth) .</span><span class="sxs-lookup"><span data-stu-id="fa060-1001">For more information, see the [Windows Authentication](xref:security/authentication/windowsauth) topic.</span></span> <span data-ttu-id="fa060-1002">| | `AuthenticationDisplayName`    | `null`  | Nastaví zobrazovaný název, který se zobrazí uživatelům na přihlašovacích stránkách.</span><span class="sxs-lookup"><span data-stu-id="fa060-1002">| | `AuthenticationDisplayName`    | `null`  | Sets the display name shown to users on login pages.</span></span> <span data-ttu-id="fa060-1003">| | `ForwardClientCertificate`     | `true`  | Pokud `true` je k `MS-ASPNETCORE-CLIENTCERT` dispozici hlavička žádosti, `HttpContext.Connection.ClientCertificate` je naplněna hodnota.</span><span class="sxs-lookup"><span data-stu-id="fa060-1003">| | `ForwardClientCertificate`     | `true`  | If `true` and the `MS-ASPNETCORE-CLIENTCERT` request header is present, the `HttpContext.Connection.ClientCertificate` is populated.</span></span> |

### <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="fa060-1004">Scénáře proxy serveru a nástroje pro vyrovnávání zatížení</span><span class="sxs-lookup"><span data-stu-id="fa060-1004">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="fa060-1005">[Služba IIS Integration middleware](#enable-the-iisintegration-components), která konfiguruje middleware předávaných hlaviček, a modul ASP.NET Core je nakonfigurován tak, aby přenesl schéma (http/https) a vzdálenou IP adresu, kam pochází požadavek.</span><span class="sxs-lookup"><span data-stu-id="fa060-1005">The [IIS Integration Middleware](#enable-the-iisintegration-components), which configures Forwarded Headers Middleware, and the ASP.NET Core Module are configured to forward the scheme (HTTP/HTTPS) and the remote IP address where the request originated.</span></span> <span data-ttu-id="fa060-1006">Pro aplikace hostované za dalšími proxy servery a nástroji pro vyrovnávání zatížení může být vyžadována další konfigurace.</span><span class="sxs-lookup"><span data-stu-id="fa060-1006">Additional configuration might be required for apps hosted behind additional proxy servers and load balancers.</span></span> <span data-ttu-id="fa060-1007">Další informace najdete v tématu [konfigurace ASP.NET Core pro práci se servery proxy a nástroji pro vyrovnávání zatížení](xref:host-and-deploy/proxy-load-balancer).</span><span class="sxs-lookup"><span data-stu-id="fa060-1007">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

### <a name="webconfig-file"></a><span data-ttu-id="fa060-1008">soubor Web. config</span><span class="sxs-lookup"><span data-stu-id="fa060-1008">web.config file</span></span>

<span data-ttu-id="fa060-1009">Soubor *Web. config* konfiguruje [modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="fa060-1009">The *web.config* file configures the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="fa060-1010">Vytváření, transformace a publikování souboru *Web. config* je zpracováno nástrojem MSBuild cíl ( `_TransformWebConfig` ) při publikování projektu.</span><span class="sxs-lookup"><span data-stu-id="fa060-1010">Creating, transforming, and publishing the *web.config* file is handled by an MSBuild target (`_TransformWebConfig`) when the project is published.</span></span> <span data-ttu-id="fa060-1011">Tento cíl je k dispozici v cíle webové sady SDK ( `Microsoft.NET.Sdk.Web` ).</span><span class="sxs-lookup"><span data-stu-id="fa060-1011">This target is present in the Web SDK targets (`Microsoft.NET.Sdk.Web`).</span></span> <span data-ttu-id="fa060-1012">Sada SDK je nastavena v horní části souboru projektu:</span><span class="sxs-lookup"><span data-stu-id="fa060-1012">The SDK is set at the top of the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="fa060-1013">Pokud soubor *Web. config* není v projektu přítomen, je vytvořen soubor se správným *processPath* a *argumenty* pro konfiguraci modulu ASP.NET Core a přesunutí do [publikovaného výstupu](xref:host-and-deploy/directory-structure).</span><span class="sxs-lookup"><span data-stu-id="fa060-1013">If a *web.config* file isn't present in the project, the file is created with the correct *processPath* and *arguments* to configure the ASP.NET Core Module and moved to [published output](xref:host-and-deploy/directory-structure).</span></span>

<span data-ttu-id="fa060-1014">Pokud je soubor *Web. config* přítomen v projektu, soubor je transformován se správným *processPath* a *argumenty* pro konfiguraci modulu ASP.NET Core a přesunutí do publikovaného výstupu.</span><span class="sxs-lookup"><span data-stu-id="fa060-1014">If a *web.config* file is present in the project, the file is transformed with the correct *processPath* and *arguments* to configure the ASP.NET Core Module and moved to published output.</span></span> <span data-ttu-id="fa060-1015">Transformace nemění nastavení konfigurace služby IIS v souboru.</span><span class="sxs-lookup"><span data-stu-id="fa060-1015">The transformation doesn't modify IIS configuration settings in the file.</span></span>

<span data-ttu-id="fa060-1016">Soubor *Web. config* může poskytovat další nastavení konfigurace služby IIS, která ovládají aktivní moduly IIS.</span><span class="sxs-lookup"><span data-stu-id="fa060-1016">The *web.config* file may provide additional IIS configuration settings that control active IIS modules.</span></span> <span data-ttu-id="fa060-1017">Informace o modulech služby IIS, které jsou schopné zpracovávat požadavky s ASP.NET Core aplikacemi, najdete v tématu [IIS modules](xref:host-and-deploy/iis/modules) .</span><span class="sxs-lookup"><span data-stu-id="fa060-1017">For information on IIS modules that are capable of processing requests with ASP.NET Core apps, see the [IIS modules](xref:host-and-deploy/iis/modules) topic.</span></span>

<span data-ttu-id="fa060-1018">Chcete-li webové sadě SDK zabránit ve transformaci souboru *Web. config* , použijte **\<IsTransformWebConfigDisabled>** vlastnost v souboru projektu:</span><span class="sxs-lookup"><span data-stu-id="fa060-1018">To prevent the Web SDK from transforming the *web.config* file, use the **\<IsTransformWebConfigDisabled>** property in the project file:</span></span>

```xml
<PropertyGroup>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

<span data-ttu-id="fa060-1019">Při zakazování webové sady SDK z transformace souboru by měl vývojář *processPath* a *argumenty* ručně nastavit.</span><span class="sxs-lookup"><span data-stu-id="fa060-1019">When disabling the Web SDK from transforming the file, the *processPath* and *arguments* should be manually set by the developer.</span></span> <span data-ttu-id="fa060-1020">Další informace naleznete v tématu <xref:host-and-deploy/aspnet-core-module>.</span><span class="sxs-lookup"><span data-stu-id="fa060-1020">For more information, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

### <a name="webconfig-file-location"></a><span data-ttu-id="fa060-1021">umístění souboru Web. config</span><span class="sxs-lookup"><span data-stu-id="fa060-1021">web.config file location</span></span>

<span data-ttu-id="fa060-1022">Aby bylo možné správně nastavit [modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module) , musí být soubor *Web. config* přítomen v [kořenové cestě obsahu](xref:fundamentals/index#content-root) (obvykle v základní cestě aplikace) nasazené aplikace.</span><span class="sxs-lookup"><span data-stu-id="fa060-1022">In order to set up the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) correctly, the *web.config* file must be present at the [content root](xref:fundamentals/index#content-root) path (typically the app base path) of the deployed app.</span></span> <span data-ttu-id="fa060-1023">Toto je stejné umístění jako fyzická cesta k webu poskytované službě IIS.</span><span class="sxs-lookup"><span data-stu-id="fa060-1023">This is the same location as the website physical path provided to IIS.</span></span> <span data-ttu-id="fa060-1024">Soubor *Web. config* je vyžadován v kořenu aplikace, aby bylo možné publikovat více aplikací pomocí nasazení webu.</span><span class="sxs-lookup"><span data-stu-id="fa060-1024">The *web.config* file is required at the root of the app to enable the publishing of multiple apps using Web Deploy.</span></span>

<span data-ttu-id="fa060-1025">Citlivé soubory existují na fyzické cestě aplikace, jako je například \* \<assembly> . runtimeconfig. JSON*, \* \<assembly> . XML* (dokumentační komentáře XML) a \* \<assembly> . DEPS. JSON\*.</span><span class="sxs-lookup"><span data-stu-id="fa060-1025">Sensitive files exist on the app's physical path, such as *\<assembly>.runtimeconfig.json*, *\<assembly>.xml* (XML Documentation comments), and *\<assembly>.deps.json*.</span></span> <span data-ttu-id="fa060-1026">Když je přítomen soubor *Web. config* a lokalita se spouští normálně, služba IIS tyto citlivé soubory po vyžádání neobsluhuje.</span><span class="sxs-lookup"><span data-stu-id="fa060-1026">When the *web.config* file is present and the site starts normally, IIS doesn't serve these sensitive files if they're requested.</span></span> <span data-ttu-id="fa060-1027">Pokud soubor *Web. config* chybí, je nesprávně pojmenovaný nebo nemůže nakonfigurovat lokalitu pro normální spuštění, služba IIS může obsluhovat citlivé soubory veřejně.</span><span class="sxs-lookup"><span data-stu-id="fa060-1027">If the *web.config* file is missing, incorrectly named, or unable to configure the site for normal startup, IIS may serve sensitive files publicly.</span></span>

<span data-ttu-id="fa060-1028">**Soubor *Web. config* musí být současně přítomen v nasazení, správně pojmenován a může nakonfigurovat lokalitu pro normální spuštění. Nikdy neodstraňujte soubor *Web. config* z produkčního nasazení.**</span><span class="sxs-lookup"><span data-stu-id="fa060-1028">**The *web.config* file must be present in the deployment at all times, correctly named, and able to configure the site for normal start up. Never remove the *web.config* file from a production deployment.**</span></span>

### <a name="transform-webconfig"></a><span data-ttu-id="fa060-1029">Transformace souboru web.config</span><span class="sxs-lookup"><span data-stu-id="fa060-1029">Transform web.config</span></span>

<span data-ttu-id="fa060-1030">Pokud potřebujete transformovat *Web. config* při publikování (například nastavit proměnné prostředí na základě konfigurace, profilu nebo prostředí), přečtěte si téma <xref:host-and-deploy/iis/transform-webconfig> .</span><span class="sxs-lookup"><span data-stu-id="fa060-1030">If you need to transform *web.config* on publish (for example, set environment variables based on the configuration, profile, or environment), see <xref:host-and-deploy/iis/transform-webconfig>.</span></span>

## <a name="iis-configuration"></a><span data-ttu-id="fa060-1031">Konfigurace služby IIS</span><span class="sxs-lookup"><span data-stu-id="fa060-1031">IIS configuration</span></span>

<span data-ttu-id="fa060-1032">**Operační systémy Windows Server**</span><span class="sxs-lookup"><span data-stu-id="fa060-1032">**Windows Server operating systems**</span></span>

<span data-ttu-id="fa060-1033">Povolte roli serveru **webový server (IIS)** a vytvořte služby rolí.</span><span class="sxs-lookup"><span data-stu-id="fa060-1033">Enable the **Web Server (IIS)** server role and establish role services.</span></span>

1. <span data-ttu-id="fa060-1034">Použijte průvodce **přidáním rolí a funkcí** z nabídky **Správa** nebo odkazu v **Správce serveru**.</span><span class="sxs-lookup"><span data-stu-id="fa060-1034">Use the **Add Roles and Features** wizard from the **Manage** menu or the link in **Server Manager**.</span></span> <span data-ttu-id="fa060-1035">V kroku **role serveru** zaškrtněte políčko **webový server (IIS)**.</span><span class="sxs-lookup"><span data-stu-id="fa060-1035">On the **Server Roles** step, check the box for **Web Server (IIS)**.</span></span>

   ![V kroku vybrat role serveru je vybraná role Webový server IIS.](index/_static/server-roles-ws2016.png)

1. <span data-ttu-id="fa060-1037">Po kroku **funkce** se krok **služby rolí** načte pro webový server (IIS).</span><span class="sxs-lookup"><span data-stu-id="fa060-1037">After the **Features** step, the **Role services** step loads for Web Server (IIS).</span></span> <span data-ttu-id="fa060-1038">Vyberte požadované služby role IIS nebo přijměte výchozí poskytnuté služby rolí.</span><span class="sxs-lookup"><span data-stu-id="fa060-1038">Select the IIS role services desired or accept the default role services provided.</span></span>

   ![Výchozí služby rolí se vyberou v kroku vybrat služby rolí.](index/_static/role-services-ws2016.png)

   <span data-ttu-id="fa060-1040">**Ověřování systému Windows (volitelné)**</span><span class="sxs-lookup"><span data-stu-id="fa060-1040">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="fa060-1041">Chcete-li povolit ověřování systému Windows, rozbalte následující uzly: zabezpečení **webového serveru**  >  **Security**.</span><span class="sxs-lookup"><span data-stu-id="fa060-1041">To enable Windows Authentication, expand the following nodes: **Web Server** > **Security**.</span></span> <span data-ttu-id="fa060-1042">Vyberte funkci **ověřování systému Windows** .</span><span class="sxs-lookup"><span data-stu-id="fa060-1042">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="fa060-1043">Další informace najdete v tématu [ověřování \<windowsAuthentication> systému Windows](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) a [Konfigurace ověřování systému Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="fa060-1043">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="fa060-1044">**WebSockets (volitelné)**</span><span class="sxs-lookup"><span data-stu-id="fa060-1044">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="fa060-1045">Objekty WebSocket jsou podporované ASP.NET Core 1,1 nebo novějším.</span><span class="sxs-lookup"><span data-stu-id="fa060-1045">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="fa060-1046">Chcete-li povolit objekty WebSockets, rozbalte následující **Web Server**uzly:  >  **vývoj aplikací**webového serveru.</span><span class="sxs-lookup"><span data-stu-id="fa060-1046">To enable WebSockets, expand the following nodes: **Web Server** > **Application Development**.</span></span> <span data-ttu-id="fa060-1047">Vyberte funkci **protokolu WebSocket** .</span><span class="sxs-lookup"><span data-stu-id="fa060-1047">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="fa060-1048">Další informace najdete v tématu [objekty WebSockets](xref:fundamentals/websockets).</span><span class="sxs-lookup"><span data-stu-id="fa060-1048">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="fa060-1049">Chcete-li nainstalovat roli a služby webového serveru, postupujte podle kroků pro **potvrzení** .</span><span class="sxs-lookup"><span data-stu-id="fa060-1049">Proceed through the **Confirmation** step to install the web server role and services.</span></span> <span data-ttu-id="fa060-1050">Po instalaci role **webový server (IIS)** není nutné restartovat server nebo službu IIS.</span><span class="sxs-lookup"><span data-stu-id="fa060-1050">A server/IIS restart isn't required after installing the **Web Server (IIS)** role.</span></span>

<span data-ttu-id="fa060-1051">**Desktopové operační systémy Windows**</span><span class="sxs-lookup"><span data-stu-id="fa060-1051">**Windows desktop operating systems**</span></span>

<span data-ttu-id="fa060-1052">Povolte **konzolu pro správu služby IIS** a **webové služby**.</span><span class="sxs-lookup"><span data-stu-id="fa060-1052">Enable the **IIS Management Console** and **World Wide Web Services**.</span></span>

1. <span data-ttu-id="fa060-1053">Přejděte na **Ovládací panely** > **programy** programy > **a funkce** > **zapnout nebo vypnout funkce systému Windows** (levá strana obrazovky).</span><span class="sxs-lookup"><span data-stu-id="fa060-1053">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>

1. <span data-ttu-id="fa060-1054">Otevřete uzel **Internetová informační služba** .</span><span class="sxs-lookup"><span data-stu-id="fa060-1054">Open the **Internet Information Services** node.</span></span> <span data-ttu-id="fa060-1055">Otevřete uzel **Nástroje webové správy** .</span><span class="sxs-lookup"><span data-stu-id="fa060-1055">Open the **Web Management Tools** node.</span></span>

1. <span data-ttu-id="fa060-1056">Zaškrtněte políčko pro **konzolu pro správu služby IIS**.</span><span class="sxs-lookup"><span data-stu-id="fa060-1056">Check the box for **IIS Management Console**.</span></span>

1. <span data-ttu-id="fa060-1057">Zaškrtněte políčko u **webových služeb**.</span><span class="sxs-lookup"><span data-stu-id="fa060-1057">Check the box for **World Wide Web Services**.</span></span>

1. <span data-ttu-id="fa060-1058">Přijměte výchozí funkce pro **webové služby** nebo upravte funkce služby IIS.</span><span class="sxs-lookup"><span data-stu-id="fa060-1058">Accept the default features for **World Wide Web Services** or customize the IIS features.</span></span>

   <span data-ttu-id="fa060-1059">**Ověřování systému Windows (volitelné)**</span><span class="sxs-lookup"><span data-stu-id="fa060-1059">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="fa060-1060">Chcete-li povolit ověřování systému Windows, rozbalte následující uzly: zabezpečení **webové služby**  >  **Security**.</span><span class="sxs-lookup"><span data-stu-id="fa060-1060">To enable Windows Authentication, expand the following nodes: **World Wide Web Services** > **Security**.</span></span> <span data-ttu-id="fa060-1061">Vyberte funkci **ověřování systému Windows** .</span><span class="sxs-lookup"><span data-stu-id="fa060-1061">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="fa060-1062">Další informace najdete v tématu [ověřování \<windowsAuthentication> systému Windows](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) a [Konfigurace ověřování systému Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="fa060-1062">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="fa060-1063">**WebSockets (volitelné)**</span><span class="sxs-lookup"><span data-stu-id="fa060-1063">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="fa060-1064">Objekty WebSocket jsou podporované ASP.NET Core 1,1 nebo novějším.</span><span class="sxs-lookup"><span data-stu-id="fa060-1064">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="fa060-1065">Chcete-li povolit objekty WebSockets, rozbalte následující uzly: funkce pro vývoj aplikací v **rámci webové služby**  >  **Application Development Features**.</span><span class="sxs-lookup"><span data-stu-id="fa060-1065">To enable WebSockets, expand the following nodes: **World Wide Web Services** > **Application Development Features**.</span></span> <span data-ttu-id="fa060-1066">Vyberte funkci **protokolu WebSocket** .</span><span class="sxs-lookup"><span data-stu-id="fa060-1066">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="fa060-1067">Další informace najdete v tématu [objekty WebSockets](xref:fundamentals/websockets).</span><span class="sxs-lookup"><span data-stu-id="fa060-1067">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="fa060-1068">Pokud instalace služby IIS vyžaduje restart, restartujte systém.</span><span class="sxs-lookup"><span data-stu-id="fa060-1068">If the IIS installation requires a restart, restart the system.</span></span>

![V rámci funkcí systému Windows jsou vybrána Konzola pro správu služby IIS a webové služby.](index/_static/windows-features-win10.png)

## <a name="install-the-net-core-hosting-bundle"></a><span data-ttu-id="fa060-1070">Instalace hostující sady .NET Core</span><span class="sxs-lookup"><span data-stu-id="fa060-1070">Install the .NET Core Hosting Bundle</span></span>

<span data-ttu-id="fa060-1071">Nainstalujte *hostující sadu .NET Core* do hostitelského systému.</span><span class="sxs-lookup"><span data-stu-id="fa060-1071">Install the *.NET Core Hosting Bundle* on the hosting system.</span></span> <span data-ttu-id="fa060-1072">Svazek nainstaluje modul runtime .NET Core, knihovnu .NET Core a [modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="fa060-1072">The bundle installs the .NET Core Runtime, .NET Core Library, and the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="fa060-1073">Modul umožňuje, aby aplikace ASP.NET Core běžely za službou IIS.</span><span class="sxs-lookup"><span data-stu-id="fa060-1073">The module allows ASP.NET Core apps to run behind IIS.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="fa060-1074">Pokud je hostující sada nainstalována před službou IIS, je nutné opravit instalaci sady prostředků.</span><span class="sxs-lookup"><span data-stu-id="fa060-1074">If the Hosting Bundle is installed before IIS, the bundle installation must be repaired.</span></span> <span data-ttu-id="fa060-1075">Spusťte znovu instalační program hostující sady po instalaci služby IIS.</span><span class="sxs-lookup"><span data-stu-id="fa060-1075">Run the Hosting Bundle installer again after installing IIS.</span></span>
>
> <span data-ttu-id="fa060-1076">Pokud se hostující sada nainstaluje po instalaci 64 (x64) verze .NET Core, můžou se zdát, že sady SDK chybí ([nezjistily se žádné sady .NET Core SDK](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span><span class="sxs-lookup"><span data-stu-id="fa060-1076">If the Hosting Bundle is installed after installing the 64-bit (x64) version of .NET Core, SDKs might appear to be missing ([No .NET Core SDKs were detected](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span></span> <span data-ttu-id="fa060-1077">Chcete-li tento problém vyřešit, přečtěte si téma <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle> .</span><span class="sxs-lookup"><span data-stu-id="fa060-1077">To resolve the problem, see <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>.</span></span>

### <a name="download"></a><span data-ttu-id="fa060-1078">Stáhnout</span><span class="sxs-lookup"><span data-stu-id="fa060-1078">Download</span></span>

1. <span data-ttu-id="fa060-1079">Přejděte na stránku [stáhnout jádro .NET Core](https://dotnet.microsoft.com/download/dotnet-core) .</span><span class="sxs-lookup"><span data-stu-id="fa060-1079">Navigate to the [Download .NET Core](https://dotnet.microsoft.com/download/dotnet-core) page.</span></span>
1. <span data-ttu-id="fa060-1080">Vyberte požadovanou verzi .NET Core.</span><span class="sxs-lookup"><span data-stu-id="fa060-1080">Select the desired .NET Core version.</span></span>
1. <span data-ttu-id="fa060-1081">Ve sloupci **Spustit aplikace – modul runtime** vyhledejte řádek požadované verze modulu runtime .NET Core.</span><span class="sxs-lookup"><span data-stu-id="fa060-1081">In the **Run apps - Runtime** column, find the row of the .NET Core runtime version desired.</span></span>
1. <span data-ttu-id="fa060-1082">Stáhněte instalační program pomocí odkazu **hostující sada** .</span><span class="sxs-lookup"><span data-stu-id="fa060-1082">Download the installer using the **Hosting Bundle** link.</span></span>

> [!WARNING]
> <span data-ttu-id="fa060-1083">Některé instalační programy obsahují verze vydaných verzí, které dosáhly svého konce životnosti (konce řádku) a které už nejsou podporovány společností Microsoft.</span><span class="sxs-lookup"><span data-stu-id="fa060-1083">Some installers contain release versions that have reached their end of life (EOL) and are no longer supported by Microsoft.</span></span> <span data-ttu-id="fa060-1084">Další informace najdete v tématu [zásady podpory](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span><span class="sxs-lookup"><span data-stu-id="fa060-1084">For more information, see the [support policy](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span></span>

### <a name="install-the-hosting-bundle"></a><span data-ttu-id="fa060-1085">Instalace hostitelského balíčku</span><span class="sxs-lookup"><span data-stu-id="fa060-1085">Install the Hosting Bundle</span></span>

1. <span data-ttu-id="fa060-1086">Spusťte instalační program na serveru.</span><span class="sxs-lookup"><span data-stu-id="fa060-1086">Run the installer on the server.</span></span> <span data-ttu-id="fa060-1087">Při spuštění instalačního programu z příkazového prostředí správce jsou k dispozici následující parametry:</span><span class="sxs-lookup"><span data-stu-id="fa060-1087">The following parameters are available when running the installer from an administrator command shell:</span></span>

   * <span data-ttu-id="fa060-1088">`OPT_NO_ANCM=1`: Přeskočit instalaci modulu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="fa060-1088">`OPT_NO_ANCM=1`: Skip installing the ASP.NET Core Module.</span></span>
   * <span data-ttu-id="fa060-1089">`OPT_NO_RUNTIME=1`: Přeskočí instalace modulu runtime .NET Core.</span><span class="sxs-lookup"><span data-stu-id="fa060-1089">`OPT_NO_RUNTIME=1`: Skip installing the .NET Core runtime.</span></span> <span data-ttu-id="fa060-1090">Používá se, když Server hostuje jenom [samostatná nasazení (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span><span class="sxs-lookup"><span data-stu-id="fa060-1090">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="fa060-1091">`OPT_NO_SHAREDFX=1`: Přeskočí instalace sdíleného rozhraní ASP.NET (ASP.NET Runtime).</span><span class="sxs-lookup"><span data-stu-id="fa060-1091">`OPT_NO_SHAREDFX=1`: Skip installing the ASP.NET Shared Framework (ASP.NET runtime).</span></span> <span data-ttu-id="fa060-1092">Používá se, když Server hostuje jenom [samostatná nasazení (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span><span class="sxs-lookup"><span data-stu-id="fa060-1092">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="fa060-1093">`OPT_NO_X86=1`: Přeskočí instalace běhových prostředí x86.</span><span class="sxs-lookup"><span data-stu-id="fa060-1093">`OPT_NO_X86=1`: Skip installing x86 runtimes.</span></span> <span data-ttu-id="fa060-1094">Tento parametr použijte, pokud víte, že nebudete hostovat 32 aplikace.</span><span class="sxs-lookup"><span data-stu-id="fa060-1094">Use this parameter when you know that you won't be hosting 32-bit apps.</span></span> <span data-ttu-id="fa060-1095">Pokud existuje možnost, že v budoucnosti budete hostovat jak 32, tak i 64 aplikace, tento parametr nepoužívejte a nainstalujete oba moduly runtime.</span><span class="sxs-lookup"><span data-stu-id="fa060-1095">If there's any chance that you will host both 32-bit and 64-bit apps in the future, don't use this parameter and install both runtimes.</span></span>
   * <span data-ttu-id="fa060-1096">`OPT_NO_SHARED_CONFIG_CHECK=1`: Pokud je sdílená konfigurace (*ApplicationHost. config*) ve stejném počítači jako instalace služby IIS, zakažte kontrolu použití sdílené konfigurace služby IIS.</span><span class="sxs-lookup"><span data-stu-id="fa060-1096">`OPT_NO_SHARED_CONFIG_CHECK=1`: Disable the check for using an IIS Shared Configuration when the shared configuration (*applicationHost.config*) is on the same machine as the IIS installation.</span></span> <span data-ttu-id="fa060-1097">*K dispozici jenom pro ASP.NET Core 2,2 nebo novější instalační programy hostujících prostředků.*</span><span class="sxs-lookup"><span data-stu-id="fa060-1097">*Only available for ASP.NET Core 2.2 or later Hosting Bundler installers.*</span></span> <span data-ttu-id="fa060-1098">Další informace naleznete v tématu <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.</span><span class="sxs-lookup"><span data-stu-id="fa060-1098">For more information, see <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.</span></span>
1. <span data-ttu-id="fa060-1099">Restartujte systém nebo v příkazovém prostředí spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="fa060-1099">Restart the system or execute the following commands in a command shell:</span></span>

   ```console
   net stop was /y
   net start w3svc
   ```
   <span data-ttu-id="fa060-1100">Restartování služby IIS zabere změny v systémové cestě, což je proměnná prostředí vytvořená instalačním programem.</span><span class="sxs-lookup"><span data-stu-id="fa060-1100">Restarting IIS picks up a change to the system PATH, which is an environment variable, made by the installer.</span></span>

<span data-ttu-id="fa060-1101">Při instalaci hostujícího balíčku není nutné ručně zastavit jednotlivé weby ve službě IIS.</span><span class="sxs-lookup"><span data-stu-id="fa060-1101">It isn't necessary to manually stop individual sites in IIS when installing the Hosting Bundle.</span></span> <span data-ttu-id="fa060-1102">Hostované aplikace (weby IIS) se restartují po restartování služby IIS.</span><span class="sxs-lookup"><span data-stu-id="fa060-1102">Hosted apps (IIS sites) restart when IIS restarts.</span></span> <span data-ttu-id="fa060-1103">Aplikace se po přijetí první žádosti spustí znovu, včetně [modulu inicializace aplikace](#application-initialization-module-and-idle-timeout).</span><span class="sxs-lookup"><span data-stu-id="fa060-1103">Apps start up again when they receive their first request, including from the [Application Initialization Module](#application-initialization-module-and-idle-timeout).</span></span>

<span data-ttu-id="fa060-1104">ASP.NET Core přijímá postup při přeposílání pro vydání oprav pro balíčky sdílených balíčků rozhraní.</span><span class="sxs-lookup"><span data-stu-id="fa060-1104">ASP.NET Core adopts roll-forward behavior for patch releases of shared framework packages.</span></span> <span data-ttu-id="fa060-1105">Když se aplikace hostované službou IIS restartují se službou IIS, aplikace při přijetí jejich prvního požadavku načtou nejnovější verze oprav jejich odkazovaných balíčků.</span><span class="sxs-lookup"><span data-stu-id="fa060-1105">When apps hosted by IIS restart with IIS, the apps load with the latest patch releases of their referenced packages when they receive their first request.</span></span> <span data-ttu-id="fa060-1106">Pokud se služba IIS nerestartuje, aplikace se restartují a projeví se chování při přeposílání, když se jejich pracovní procesy recyklují a obdrží první požadavek.</span><span class="sxs-lookup"><span data-stu-id="fa060-1106">If IIS isn't restarted, apps restart and exhibit roll-forward behavior when their worker processes are recycled and they receive their first request.</span></span>

> [!NOTE]
> <span data-ttu-id="fa060-1107">Informace o sdílené konfiguraci služby IIS najdete v tématu [modul ASP.NET Core se sdílenou konfigurací služby IIS](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).</span><span class="sxs-lookup"><span data-stu-id="fa060-1107">For information on IIS Shared Configuration, see [ASP.NET Core Module with IIS Shared Configuration](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).</span></span>

## <a name="install-web-deploy-when-publishing-with-visual-studio"></a><span data-ttu-id="fa060-1108">Při publikování v aplikaci Visual Studio nainstalovat Nasazení webu</span><span class="sxs-lookup"><span data-stu-id="fa060-1108">Install Web Deploy when publishing with Visual Studio</span></span>

<span data-ttu-id="fa060-1109">Při nasazování aplikací na servery s [nasazení webu](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later)nainstalujte na server nejnovější verzi nasazení webu.</span><span class="sxs-lookup"><span data-stu-id="fa060-1109">When deploying apps to servers with [Web Deploy](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later), install the latest version of Web Deploy on the server.</span></span> <span data-ttu-id="fa060-1110">Chcete-li nainstalovat Nasazení webu, použijte [instalační program webové platformy (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) nebo si instalační program Získejte přímo z webu [služby Stažení softwaru](https://www.microsoft.com/download/details.aspx?id=43717).</span><span class="sxs-lookup"><span data-stu-id="fa060-1110">To install Web Deploy, use the [Web Platform Installer (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) or obtain an installer directly from the [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=43717).</span></span> <span data-ttu-id="fa060-1111">Upřednostňovanou metodou je použití WebPI.</span><span class="sxs-lookup"><span data-stu-id="fa060-1111">The preferred method is to use WebPI.</span></span> <span data-ttu-id="fa060-1112">WebPI nabízí samostatnou instalaci a konfiguraci pro poskytovatele hostingu.</span><span class="sxs-lookup"><span data-stu-id="fa060-1112">WebPI offers a standalone setup and a configuration for hosting providers.</span></span>

## <a name="create-the-iis-site"></a><span data-ttu-id="fa060-1113">Vytvoření webu služby IIS</span><span class="sxs-lookup"><span data-stu-id="fa060-1113">Create the IIS site</span></span>

1. <span data-ttu-id="fa060-1114">V hostitelském systému vytvořte složku, která bude obsahovat publikované složky a soubory aplikace.</span><span class="sxs-lookup"><span data-stu-id="fa060-1114">On the hosting system, create a folder to contain the app's published folders and files.</span></span> <span data-ttu-id="fa060-1115">V následujícím kroku je jako fyzická cesta k aplikaci služba IIS poskytována jako cesta k této složce.</span><span class="sxs-lookup"><span data-stu-id="fa060-1115">In a following step, the folder's path is provided to IIS as the physical path to the app.</span></span> <span data-ttu-id="fa060-1116">Další informace o složce nasazení aplikace a rozložení souborů naleznete v tématu <xref:host-and-deploy/directory-structure> .</span><span class="sxs-lookup"><span data-stu-id="fa060-1116">For more information on an app's deployment folder and file layout, see <xref:host-and-deploy/directory-structure>.</span></span>

1. <span data-ttu-id="fa060-1117">Ve Správci služby IIS otevřete uzel serveru na panelu **připojení** .</span><span class="sxs-lookup"><span data-stu-id="fa060-1117">In IIS Manager, open the server's node in the **Connections** panel.</span></span> <span data-ttu-id="fa060-1118">Klikněte pravým tlačítkem na složku **weby** .</span><span class="sxs-lookup"><span data-stu-id="fa060-1118">Right-click the **Sites** folder.</span></span> <span data-ttu-id="fa060-1119">V místní nabídce vyberte **Přidat web** .</span><span class="sxs-lookup"><span data-stu-id="fa060-1119">Select **Add Website** from the contextual menu.</span></span>

1. <span data-ttu-id="fa060-1120">Zadejte **název lokality** a nastavte **fyzickou cestu** ke složce pro nasazení aplikace.</span><span class="sxs-lookup"><span data-stu-id="fa060-1120">Provide a **Site name** and set the **Physical path** to the app's deployment folder.</span></span> <span data-ttu-id="fa060-1121">Zadejte konfiguraci **vazby** a vytvořte web výběrem **OK**:</span><span class="sxs-lookup"><span data-stu-id="fa060-1121">Provide the **Binding** configuration and create the website by selecting **OK**:</span></span>

   ![Zadejte název lokality, fyzickou cestu a název hostitele v kroku přidat web.](index/_static/add-website-ws2016.png)

   > [!WARNING]
   > <span data-ttu-id="fa060-1123">Nelze použít vazby zástupných znaků na nejvyšší úrovni ( `http://*:80/` a `http://+:80` ). **not**</span><span class="sxs-lookup"><span data-stu-id="fa060-1123">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="fa060-1124">Vazby zástupných znaků nejvyšší úrovně můžou aplikaci otevřít pro slabá místa zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="fa060-1124">Top-level wildcard bindings can open up your app to security vulnerabilities.</span></span> <span data-ttu-id="fa060-1125">To platí pro silné i slabé zástupné znaky.</span><span class="sxs-lookup"><span data-stu-id="fa060-1125">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="fa060-1126">Místo zástupných znaků použijte explicitní názvy hostitelů.</span><span class="sxs-lookup"><span data-stu-id="fa060-1126">Use explicit host names rather than wildcards.</span></span> <span data-ttu-id="fa060-1127">Vazba zástupných znaků subdomény (například `*.mysub.com` ) nemá toto bezpečnostní riziko, pokud ovládáte celou nadřazenou doménu (na rozdíl od `*.com` , která je zranitelná).</span><span class="sxs-lookup"><span data-stu-id="fa060-1127">Subdomain wildcard binding (for example, `*.mysub.com`) doesn't have this security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="fa060-1128">Další informace najdete v [části rfc7230 část-5,4](https://tools.ietf.org/html/rfc7230#section-5.4) .</span><span class="sxs-lookup"><span data-stu-id="fa060-1128">See [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) for more information.</span></span>

1. <span data-ttu-id="fa060-1129">V uzlu serveru vyberte **fondy aplikací**.</span><span class="sxs-lookup"><span data-stu-id="fa060-1129">Under the server's node, select **Application Pools**.</span></span>

1. <span data-ttu-id="fa060-1130">Klikněte pravým tlačítkem myši na fond aplikací webu a v místní nabídce vyberte **základní nastavení** .</span><span class="sxs-lookup"><span data-stu-id="fa060-1130">Right-click the site's app pool and select **Basic Settings** from the contextual menu.</span></span>

1. <span data-ttu-id="fa060-1131">V okně **Upravit fond aplikací** nastavte **verzi .NET CLR** na **bez spravovaného kódu**:</span><span class="sxs-lookup"><span data-stu-id="fa060-1131">In the **Edit Application Pool** window, set the **.NET CLR version** to **No Managed Code**:</span></span>

   ![Pro verzi .NET CLR nenastavte žádný spravovaný kód.](index/_static/edit-apppool-ws2016.png)

    <span data-ttu-id="fa060-1133">ASP.NET Core běží v samostatném procesu a spravuje modul runtime.</span><span class="sxs-lookup"><span data-stu-id="fa060-1133">ASP.NET Core runs in a separate process and manages the runtime.</span></span> <span data-ttu-id="fa060-1134">ASP.NET Core nespoléhá na načítání CLR desktopových aplikací (.NET CLR) &mdash; , modul CLR (Common Language Runtime) pro .NET Core se spouští k hostování aplikace v pracovním procesu.</span><span class="sxs-lookup"><span data-stu-id="fa060-1134">ASP.NET Core doesn't rely on loading the desktop CLR (.NET CLR)&mdash;the Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process.</span></span> <span data-ttu-id="fa060-1135">Nastavení **verze .NET CLR** na **žádný spravovaný kód** není volitelné, ale doporučuje se.</span><span class="sxs-lookup"><span data-stu-id="fa060-1135">Setting the **.NET CLR version** to **No Managed Code** is optional but recommended.</span></span>

1. <span data-ttu-id="fa060-1136">*ASP.NET Core 2,2 nebo novější*: 64 u samostatného [nasazeného nasazení](/dotnet/core/deploying/#self-contained-deployments-scd) (x64), které používá [model hostování v rámci procesu](#in-process-hosting-model), zakažte fond aplikací pro procesy 32 (x86).</span><span class="sxs-lookup"><span data-stu-id="fa060-1136">*ASP.NET Core 2.2 or later*: For a 64-bit (x64) [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) that uses the [in-process hosting model](#in-process-hosting-model), disable the app pool for 32-bit (x86) processes.</span></span>

   <span data-ttu-id="fa060-1137">V bočním panelu **Akce** Správce služby IIS > **fondy aplikací**vyberte možnost **nastavit výchozí hodnoty fondu aplikací** nebo **Upřesnit nastavení**.</span><span class="sxs-lookup"><span data-stu-id="fa060-1137">In the **Actions** sidebar of IIS Manager > **Application Pools**, select **Set Application Pool Defaults** or **Advanced Settings**.</span></span> <span data-ttu-id="fa060-1138">Vyhledejte **možnost povolit 32 – bitové aplikace** a nastavte hodnotu na `False` .</span><span class="sxs-lookup"><span data-stu-id="fa060-1138">Locate **Enable 32-Bit Applications** and set the value to `False`.</span></span> <span data-ttu-id="fa060-1139">Toto nastavení nemá vliv na aplikace nasazené pro [hostování mimo proces](xref:host-and-deploy/aspnet-core-module#out-of-process-hosting-model).</span><span class="sxs-lookup"><span data-stu-id="fa060-1139">This setting doesn't affect apps deployed for [out-of-process hosting](xref:host-and-deploy/aspnet-core-module#out-of-process-hosting-model).</span></span>

1. <span data-ttu-id="fa060-1140">Potvrďte, že identita modelu procesu má správná oprávnění.</span><span class="sxs-lookup"><span data-stu-id="fa060-1140">Confirm the process model identity has the proper permissions.</span></span>

   <span data-ttu-id="fa060-1141">Pokud se výchozí identita fondu aplikací (**modelu procesu**  >  **Identity** ) změní z **ApplicationPoolIdentity** na jinou identitu, ověřte, že Nová identita má požadovaná oprávnění pro přístup ke složce, databázi a dalším požadovaným prostředkům aplikace.</span><span class="sxs-lookup"><span data-stu-id="fa060-1141">If the default identity of the app pool (**Process Model** > **Identity**) is changed from **ApplicationPoolIdentity** to another identity, verify that the new identity has the required permissions to access the app's folder, database, and other required resources.</span></span> <span data-ttu-id="fa060-1142">Například fond aplikací vyžaduje přístup pro čtení a zápis ke složkám, kde aplikace čte a zapisuje soubory.</span><span class="sxs-lookup"><span data-stu-id="fa060-1142">For example, the app pool requires read and write access to folders where the app reads and writes files.</span></span>

<span data-ttu-id="fa060-1143">**Konfigurace ověřování systému Windows (volitelné)**</span><span class="sxs-lookup"><span data-stu-id="fa060-1143">**Windows Authentication configuration (Optional)**</span></span>  
<span data-ttu-id="fa060-1144">Další informace najdete v tématu [Konfigurace ověřování systému Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="fa060-1144">For more information, see [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

## <a name="deploy-the-app"></a><span data-ttu-id="fa060-1145">Nasazení aplikace</span><span class="sxs-lookup"><span data-stu-id="fa060-1145">Deploy the app</span></span>

<span data-ttu-id="fa060-1146">Nasaďte aplikaci do složky **fyzické cesty** služby IIS, která byla navázána v části [Vytvoření webu služby IIS](#create-the-iis-site) .</span><span class="sxs-lookup"><span data-stu-id="fa060-1146">Deploy the app to the IIS **Physical path** folder that was established in the [Create the IIS site](#create-the-iis-site) section.</span></span> <span data-ttu-id="fa060-1147">[Nasazení webu](/iis/publish/using-web-deploy/introduction-to-web-deploy) je doporučeným mechanismem pro nasazení, ale existuje několik možností pro přesun aplikace ze složky *publikování* projektu do složky pro nasazení hostitelského systému.</span><span class="sxs-lookup"><span data-stu-id="fa060-1147">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) is the recommended mechanism for deployment, but several options exist for moving the app from the project's *publish* folder to the hosting system's deployment folder.</span></span>

### <a name="web-deploy-with-visual-studio"></a><span data-ttu-id="fa060-1148">Nasazení webu se sadou Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fa060-1148">Web Deploy with Visual Studio</span></span>

<span data-ttu-id="fa060-1149">Informace o tom, jak vytvořit profil publikování pro použití s Nasazení webu, najdete v tématu [publikační profily sady Visual Studio pro ASP.NET Core nasazení aplikací](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) .</span><span class="sxs-lookup"><span data-stu-id="fa060-1149">See the [Visual Studio publish profiles for ASP.NET Core app deployment](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) topic to learn how to create a publish profile for use with Web Deploy.</span></span> <span data-ttu-id="fa060-1150">Pokud poskytovatel hostingu poskytuje profil publikování nebo podporu pro jeho vytvoření, Stáhněte si jeho profil a importujte ho pomocí dialogového okna pro **publikování** sady Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="fa060-1150">If the hosting provider provides a Publish Profile or support for creating one, download their profile and import it using the Visual Studio **Publish** dialog:</span></span>

![Stránka publikovat dialog](index/_static/pub-dialog.png)

### <a name="web-deploy-outside-of-visual-studio"></a><span data-ttu-id="fa060-1152">Nasazení webu mimo Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fa060-1152">Web Deploy outside of Visual Studio</span></span>

<span data-ttu-id="fa060-1153">[Nasazení webu](/iis/publish/using-web-deploy/introduction-to-web-deploy) lze použít také mimo sadu Visual Studio z příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="fa060-1153">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) can also be used outside of Visual Studio from the command line.</span></span> <span data-ttu-id="fa060-1154">Další informace najdete v tématu [Nástroj pro nasazení webu](/iis/publish/using-web-deploy/use-the-web-deployment-tool).</span><span class="sxs-lookup"><span data-stu-id="fa060-1154">For more information, see [Web Deployment Tool](/iis/publish/using-web-deploy/use-the-web-deployment-tool).</span></span>

### <a name="alternatives-to-web-deploy"></a><span data-ttu-id="fa060-1155">Alternativy k Nasazení webu</span><span class="sxs-lookup"><span data-stu-id="fa060-1155">Alternatives to Web Deploy</span></span>

<span data-ttu-id="fa060-1156">K přesunu aplikace do hostitelského systému, jako je ruční kopírování, [xcopy](/windows-server/administration/windows-commands/xcopy), [Robocopy](/windows-server/administration/windows-commands/robocopy)nebo [PowerShell](/powershell/), použijte libovolný z několika způsobů.</span><span class="sxs-lookup"><span data-stu-id="fa060-1156">Use any of several methods to move the app to the hosting system, such as manual copy, [Xcopy](/windows-server/administration/windows-commands/xcopy), [Robocopy](/windows-server/administration/windows-commands/robocopy), or [PowerShell](/powershell/).</span></span>

<span data-ttu-id="fa060-1157">Další informace o nasazení ASP.NET Core do služby IIS najdete v části [prostředky nasazení pro správce služby IIS](#deployment-resources-for-iis-administrators) .</span><span class="sxs-lookup"><span data-stu-id="fa060-1157">For more information on ASP.NET Core deployment to IIS, see the [Deployment resources for IIS administrators](#deployment-resources-for-iis-administrators) section.</span></span>

## <a name="browse-the-website"></a><span data-ttu-id="fa060-1158">Procházet web</span><span class="sxs-lookup"><span data-stu-id="fa060-1158">Browse the website</span></span>

<span data-ttu-id="fa060-1159">Po nasazení aplikace do hostitelského systému vytvořte žádost jednomu z veřejných koncových bodů aplikace.</span><span class="sxs-lookup"><span data-stu-id="fa060-1159">After the app is deployed to the hosting system, make a request to one of the app's public endpoints.</span></span>

<span data-ttu-id="fa060-1160">V následujícím příkladu je lokalita svázána s **názvem hostitele** služby IIS `www.mysite.com` na **portu** `80` .</span><span class="sxs-lookup"><span data-stu-id="fa060-1160">In the following example, the site is bound to an IIS **Host name** of `www.mysite.com` on **Port** `80`.</span></span> <span data-ttu-id="fa060-1161">Odeslala se žádost `http://www.mysite.com` :</span><span class="sxs-lookup"><span data-stu-id="fa060-1161">A request is made to `http://www.mysite.com`:</span></span>

![Prohlížeč Microsoft Edge načetl spouštěcí stránku služby IIS.](index/_static/browsewebsite.png)

## <a name="locked-deployment-files"></a><span data-ttu-id="fa060-1163">Uzamčené soubory nasazení</span><span class="sxs-lookup"><span data-stu-id="fa060-1163">Locked deployment files</span></span>

<span data-ttu-id="fa060-1164">Soubory ve složce pro nasazení jsou zamčené, když je aplikace spuštěná.</span><span class="sxs-lookup"><span data-stu-id="fa060-1164">Files in the deployment folder are locked when the app is running.</span></span> <span data-ttu-id="fa060-1165">Uzamčené soubory nejde během nasazování přepsat.</span><span class="sxs-lookup"><span data-stu-id="fa060-1165">Locked files can't be overwritten during deployment.</span></span> <span data-ttu-id="fa060-1166">Chcete-li uvolnit uzamčené soubory v nasazení, zastavte fond aplikací pomocí **jednoho** z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="fa060-1166">To release locked files in a deployment, stop the app pool using **one** of the following approaches:</span></span>

* <span data-ttu-id="fa060-1167">V souboru projektu použijte Nasazení webu a odkaz `Microsoft.NET.Sdk.Web` .</span><span class="sxs-lookup"><span data-stu-id="fa060-1167">Use Web Deploy and reference `Microsoft.NET.Sdk.Web` in the project file.</span></span> <span data-ttu-id="fa060-1168">Soubor *App_offline. htm* se umístí do kořenové složky adresáře webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="fa060-1168">An *app_offline.htm* file is placed at the root of the web app directory.</span></span> <span data-ttu-id="fa060-1169">Když je soubor přítomen, modul ASP.NET Core aplikaci korektně ukončí a během nasazování zachová soubor *App_offline. htm* .</span><span class="sxs-lookup"><span data-stu-id="fa060-1169">When the file is present, the ASP.NET Core Module gracefully shuts down the app and serves the *app_offline.htm* file during the deployment.</span></span> <span data-ttu-id="fa060-1170">Další informace najdete v referenčních informacích k [konfiguraci modulu ASP.NET Core](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).</span><span class="sxs-lookup"><span data-stu-id="fa060-1170">For more information, see the [ASP.NET Core Module configuration reference](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).</span></span>
* <span data-ttu-id="fa060-1171">Ručně Zastavte fond aplikací ve Správci služby IIS na serveru.</span><span class="sxs-lookup"><span data-stu-id="fa060-1171">Manually stop the app pool in the IIS Manager on the server.</span></span>
* <span data-ttu-id="fa060-1172">Použití PowerShellu k vyřazení *App_offline. htm* (vyžaduje PowerShell 5 nebo novější):</span><span class="sxs-lookup"><span data-stu-id="fa060-1172">Use PowerShell to drop *app_offline.htm* (requires PowerShell 5 or later):</span></span>

  ```powershell
  $pathToApp = 'PATH_TO_APP'

  # Stop the AppPool
  New-Item -Path $pathToApp app_offline.htm

  # Provide script commands here to deploy the app

  # Restart the AppPool
  Remove-Item -Path $pathToApp app_offline.htm

  ```

## <a name="data-protection"></a><span data-ttu-id="fa060-1173">Ochrana dat</span><span class="sxs-lookup"><span data-stu-id="fa060-1173">Data protection</span></span>

<span data-ttu-id="fa060-1174">[Sada ASP.NET Core Data Protection Stack](xref:security/data-protection/introduction) je používána několika ASP.NET Core [middlewary](xref:fundamentals/middleware/index), včetně middlewaru používaného při ověřování.</span><span class="sxs-lookup"><span data-stu-id="fa060-1174">The [ASP.NET Core Data Protection stack](xref:security/data-protection/introduction) is used by several ASP.NET Core [middlewares](xref:fundamentals/middleware/index), including middleware used in authentication.</span></span> <span data-ttu-id="fa060-1175">I v případě, že rozhraní API ochrany dat není voláno uživatelským kódem, je třeba nakonfigurovat ochranu dat pomocí skriptu nasazení nebo v uživatelském kódu, aby bylo možné vytvořit trvalé úložiště kryptografických [klíčů](xref:security/data-protection/implementation/key-management).</span><span class="sxs-lookup"><span data-stu-id="fa060-1175">Even if Data Protection APIs aren't called by user code, data protection should be configured with a deployment script or in user code to create a persistent cryptographic [key store](xref:security/data-protection/implementation/key-management).</span></span> <span data-ttu-id="fa060-1176">Pokud ochrana dat není nakonfigurovaná, klíče se uchovávají v paměti a při restartování aplikace se zahodí.</span><span class="sxs-lookup"><span data-stu-id="fa060-1176">If data protection isn't configured, the keys are held in memory and discarded when the app restarts.</span></span>

<span data-ttu-id="fa060-1177">Pokud se klíčového prstence při restartu aplikace uloží do paměti:</span><span class="sxs-lookup"><span data-stu-id="fa060-1177">If the key ring is stored in memory when the app restarts:</span></span>

* <span data-ttu-id="fa060-1178">Všechny ověřovací tokeny založené na souborech cookie jsou neověřené.</span><span class="sxs-lookup"><span data-stu-id="fa060-1178">All cookie-based authentication tokens are invalidated.</span></span> 
* <span data-ttu-id="fa060-1179">Uživatelé se musí znovu přihlásit na svůj další požadavek.</span><span class="sxs-lookup"><span data-stu-id="fa060-1179">Users are required to sign in again on their next request.</span></span> 
* <span data-ttu-id="fa060-1180">Data chráněná pomocí Key ringu už nebude možné dešifrovat.</span><span class="sxs-lookup"><span data-stu-id="fa060-1180">Any data protected with the key ring can no longer be decrypted.</span></span> <span data-ttu-id="fa060-1181">To může zahrnovat [CSRF tokeny](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) a [ASP.NET Core soubory cookie TempData MVC](xref:fundamentals/app-state#tempdata).</span><span class="sxs-lookup"><span data-stu-id="fa060-1181">This may include [CSRF tokens](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) and [ASP.NET Core MVC TempData cookies](xref:fundamentals/app-state#tempdata).</span></span>

<span data-ttu-id="fa060-1182">Pokud chcete v rámci služby IIS nakonfigurovat ochranu dat a zachovat přitom klíčového prstence, použijte **některý** z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="fa060-1182">To configure data protection under IIS to persist the key ring, use **one** of the following approaches:</span></span>

* <span data-ttu-id="fa060-1183">**Vytvoření klíčů registru ochrany dat**</span><span class="sxs-lookup"><span data-stu-id="fa060-1183">**Create Data Protection Registry Keys**</span></span>

  <span data-ttu-id="fa060-1184">Klíče ochrany dat, které používá aplikace ASP.NET Core, jsou uložené v registru externě pro aplikace.</span><span class="sxs-lookup"><span data-stu-id="fa060-1184">Data protection keys used by ASP.NET Core apps are stored in the registry external to the apps.</span></span> <span data-ttu-id="fa060-1185">Pokud chcete zachovat klíče pro danou aplikaci, vytvořte klíče registru pro fond aplikací.</span><span class="sxs-lookup"><span data-stu-id="fa060-1185">To persist the keys for a given app, create registry keys for the app pool.</span></span>

  <span data-ttu-id="fa060-1186">Pro samostatnou instalaci služby IIS, která není součástí webfarmu, se dá [skript PowerShellu pro ochranu dat provision-AutoGenKeys. ps1](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) použít pro každý fond aplikací, který se používá v aplikaci ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="fa060-1186">For standalone, non-webfarm IIS installations, the [Data Protection Provision-AutoGenKeys.ps1 PowerShell script](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) can be used for each app pool used with an ASP.NET Core app.</span></span> <span data-ttu-id="fa060-1187">Tento skript vytvoří klíč registru v registru HKLM, který je přístupný jenom pro účet pracovního procesu fondu aplikací aplikace.</span><span class="sxs-lookup"><span data-stu-id="fa060-1187">This script creates a registry key in the HKLM registry that's accessible only to the worker process account of the app's app pool.</span></span> <span data-ttu-id="fa060-1188">Klíče jsou v klidovém stavu zašifrované pomocí rozhraní DPAPI s klíčem celého počítače.</span><span class="sxs-lookup"><span data-stu-id="fa060-1188">Keys are encrypted at rest using DPAPI with a machine-wide key.</span></span>

  <span data-ttu-id="fa060-1189">Ve scénářích webové farmy může být aplikace nakonfigurovaná tak, aby používala cestu UNC k uložení svého prstence s klíčem pro ochranu dat.</span><span class="sxs-lookup"><span data-stu-id="fa060-1189">In web farm scenarios, an app can be configured to use a UNC path to store its data protection key ring.</span></span> <span data-ttu-id="fa060-1190">Ve výchozím nastavení nejsou klíče ochrany dat šifrované.</span><span class="sxs-lookup"><span data-stu-id="fa060-1190">By default, the data protection keys aren't encrypted.</span></span> <span data-ttu-id="fa060-1191">Ujistěte se, že oprávnění k souborům pro sdílenou síťovou složku jsou omezená na účet systému Windows, pod kterým je aplikace spuštěná.</span><span class="sxs-lookup"><span data-stu-id="fa060-1191">Ensure that the file permissions for the network share are limited to the Windows account the app runs under.</span></span> <span data-ttu-id="fa060-1192">K ochraně neaktivních klíčů je možné použít certifikát x509.</span><span class="sxs-lookup"><span data-stu-id="fa060-1192">An X509 certificate can be used to protect keys at rest.</span></span> <span data-ttu-id="fa060-1193">Vezměte v úvahu mechanismus, který umožní uživatelům odeslat certifikáty: Umístěte certifikáty do důvěryhodného úložiště certifikátů uživatele a ujistěte se, že jsou k dispozici na všech počítačích, kde je aplikace uživatele spuštěna.</span><span class="sxs-lookup"><span data-stu-id="fa060-1193">Consider a mechanism to allow users to upload certificates: Place certificates into the user's trusted certificate store and ensure they're available on all machines where the user's app runs.</span></span> <span data-ttu-id="fa060-1194">Podrobnosti najdete v tématu [Konfigurace ochrany ASP.NET Core dat](xref:security/data-protection/configuration/overview) .</span><span class="sxs-lookup"><span data-stu-id="fa060-1194">See [Configure ASP.NET Core Data Protection](xref:security/data-protection/configuration/overview) for details.</span></span>

* <span data-ttu-id="fa060-1195">**Konfigurace fondu aplikací služby IIS pro načtení profilu uživatele**</span><span class="sxs-lookup"><span data-stu-id="fa060-1195">**Configure the IIS Application Pool to load the user profile**</span></span>

  <span data-ttu-id="fa060-1196">Toto nastavení se nachází v části **model procesu** v části **Rozšířená nastavení** fondu aplikací.</span><span class="sxs-lookup"><span data-stu-id="fa060-1196">This setting is in the **Process Model** section under the **Advanced Settings** for the app pool.</span></span> <span data-ttu-id="fa060-1197">Nastavte **načíst profil uživatele** na `True` .</span><span class="sxs-lookup"><span data-stu-id="fa060-1197">Set **Load User Profile** to `True`.</span></span> <span data-ttu-id="fa060-1198">Pokud je nastaveno na `True` , klíče jsou uloženy v adresáři profilu uživatele a chráněny pomocí rozhraní DPAPI s klíčem specifickým pro uživatelský účet.</span><span class="sxs-lookup"><span data-stu-id="fa060-1198">When set to `True`, keys are stored in the user profile directory and protected using DPAPI with a key specific to the user account.</span></span> <span data-ttu-id="fa060-1199">Klíče jsou uchovány ve složce *% localappdata%/ASP.NET/DataProtection-Keys* .</span><span class="sxs-lookup"><span data-stu-id="fa060-1199">Keys are persisted to the *%LOCALAPPDATA%/ASP.NET/DataProtection-Keys* folder.</span></span>

  <span data-ttu-id="fa060-1200">Musí být povolený i [atribut setProfileEnvironment](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) fondu aplikací.</span><span class="sxs-lookup"><span data-stu-id="fa060-1200">The app pool's [setProfileEnvironment attribute](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) must also be enabled.</span></span> <span data-ttu-id="fa060-1201">Výchozí hodnota `setProfileEnvironment` je `true` .</span><span class="sxs-lookup"><span data-stu-id="fa060-1201">The default value of `setProfileEnvironment` is `true`.</span></span> <span data-ttu-id="fa060-1202">V některých scénářích (například operační systém Windows) `setProfileEnvironment` je nastavena na `false` .</span><span class="sxs-lookup"><span data-stu-id="fa060-1202">In some scenarios (for example, Windows OS), `setProfileEnvironment` is set to `false`.</span></span> <span data-ttu-id="fa060-1203">Pokud se klíče neukládají v adresáři profilu uživatele podle očekávání:</span><span class="sxs-lookup"><span data-stu-id="fa060-1203">If keys aren't stored in the user profile directory as expected:</span></span>

  1. <span data-ttu-id="fa060-1204">Přejděte do složky *% windir%/system32/Inetsrv/config* .</span><span class="sxs-lookup"><span data-stu-id="fa060-1204">Navigate to the *%windir%/system32/inetsrv/config* folder.</span></span>
  1. <span data-ttu-id="fa060-1205">Otevřete soubor *ApplicationHost. config* .</span><span class="sxs-lookup"><span data-stu-id="fa060-1205">Open the *applicationHost.config* file.</span></span>
  1. <span data-ttu-id="fa060-1206">Vyhledejte element `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>`.</span><span class="sxs-lookup"><span data-stu-id="fa060-1206">Locate the `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` element.</span></span>
  1. <span data-ttu-id="fa060-1207">Potvrďte, že `setProfileEnvironment` atribut není přítomen, přičemž výchozí hodnota je `true` , nebo explicitně nastavte hodnotu atributu na `true` .</span><span class="sxs-lookup"><span data-stu-id="fa060-1207">Confirm that the `setProfileEnvironment` attribute isn't present, which defaults the value to `true`, or explicitly set the attribute's value to `true`.</span></span>

* <span data-ttu-id="fa060-1208">**Použití systému souborů jako úložiště pro Key Ring**</span><span class="sxs-lookup"><span data-stu-id="fa060-1208">**Use the file system as a key ring store**</span></span>

  <span data-ttu-id="fa060-1209">Upravte kód aplikace tak, aby [používal systém souborů jako úložiště ve službě Key Ring](xref:security/data-protection/configuration/overview).</span><span class="sxs-lookup"><span data-stu-id="fa060-1209">Adjust the app code to [use the file system as a key ring store](xref:security/data-protection/configuration/overview).</span></span> <span data-ttu-id="fa060-1210">K ochraně služby Key Ring použijte certifikát x509 a ujistěte se, že certifikát je důvěryhodný certifikát.</span><span class="sxs-lookup"><span data-stu-id="fa060-1210">Use an X509 certificate to protect the key ring and ensure the certificate is a trusted certificate.</span></span> <span data-ttu-id="fa060-1211">Pokud je certifikát podepsaný svým držitelem, umístěte certifikát do důvěryhodného kořenového úložiště.</span><span class="sxs-lookup"><span data-stu-id="fa060-1211">If the certificate is self-signed, place the certificate in the Trusted Root store.</span></span>

  <span data-ttu-id="fa060-1212">Při použití služby IIS ve webové farmě:</span><span class="sxs-lookup"><span data-stu-id="fa060-1212">When using IIS in a web farm:</span></span>

  * <span data-ttu-id="fa060-1213">Použijte sdílenou složku, ke které mají přístup všechny počítače.</span><span class="sxs-lookup"><span data-stu-id="fa060-1213">Use a file share that all machines can access.</span></span>
  * <span data-ttu-id="fa060-1214">Nasaďte do každého počítače certifikát x509.</span><span class="sxs-lookup"><span data-stu-id="fa060-1214">Deploy an X509 certificate to each machine.</span></span> <span data-ttu-id="fa060-1215">Konfigurace [ochrany dat v kódu](xref:security/data-protection/configuration/overview).</span><span class="sxs-lookup"><span data-stu-id="fa060-1215">Configure [data protection in code](xref:security/data-protection/configuration/overview).</span></span>

* <span data-ttu-id="fa060-1216">**Nastavení zásad pro ochranu dat na úrovni počítače**</span><span class="sxs-lookup"><span data-stu-id="fa060-1216">**Set a machine-wide policy for data protection**</span></span>

  <span data-ttu-id="fa060-1217">Systém ochrany dat má omezená podpora pro nastavení výchozích [zásad](xref:security/data-protection/configuration/machine-wide-policy) pro všechny aplikace, které využívají rozhraní API pro ochranu dat.</span><span class="sxs-lookup"><span data-stu-id="fa060-1217">The data protection system has limited support for setting a default [machine-wide policy](xref:security/data-protection/configuration/machine-wide-policy) for all apps that consume the Data Protection APIs.</span></span> <span data-ttu-id="fa060-1218">Další informace naleznete v tématu <xref:security/data-protection/introduction>.</span><span class="sxs-lookup"><span data-stu-id="fa060-1218">For more information, see <xref:security/data-protection/introduction>.</span></span>

## <a name="virtual-directories"></a><span data-ttu-id="fa060-1219">Virtuální adresáře</span><span class="sxs-lookup"><span data-stu-id="fa060-1219">Virtual Directories</span></span>

<span data-ttu-id="fa060-1220">Aplikace ASP.NET Core nepodporují [virtuální adresáře služby IIS](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) .</span><span class="sxs-lookup"><span data-stu-id="fa060-1220">[IIS Virtual Directories](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) aren't supported with ASP.NET Core apps.</span></span> <span data-ttu-id="fa060-1221">Aplikace může být hostována jako [dílčí aplikace](#sub-applications).</span><span class="sxs-lookup"><span data-stu-id="fa060-1221">An app can be hosted as a [sub-application](#sub-applications).</span></span>

## <a name="sub-applications"></a><span data-ttu-id="fa060-1222">Dílčí aplikace</span><span class="sxs-lookup"><span data-stu-id="fa060-1222">Sub-applications</span></span>

<span data-ttu-id="fa060-1223">Aplikace ASP.NET Core může být hostovaná jako [podaplikace služby IIS (dílčí aplikace)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications).</span><span class="sxs-lookup"><span data-stu-id="fa060-1223">An ASP.NET Core app can be hosted as an [IIS sub-application (sub-app)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications).</span></span> <span data-ttu-id="fa060-1224">Cesta k dílčí aplikaci se stala součástí adresy URL kořenové aplikace.</span><span class="sxs-lookup"><span data-stu-id="fa060-1224">The sub-app's path becomes part of the root app's URL.</span></span>

<span data-ttu-id="fa060-1225">Propojení statických prostředků v rámci dílčí aplikace by měla používat notaci vlnovku ( `~/` ).</span><span class="sxs-lookup"><span data-stu-id="fa060-1225">Static asset links within the sub-app should use tilde-slash (`~/`) notation.</span></span> <span data-ttu-id="fa060-1226">Znak tildy vlnovkou spustí [pomocnou nápovědu](xref:mvc/views/tag-helpers/intro) k předřazení pathbase dílčí aplikace na vykreslený relativní odkaz.</span><span class="sxs-lookup"><span data-stu-id="fa060-1226">Tilde-slash notation triggers a [Tag Helper](xref:mvc/views/tag-helpers/intro) to prepend the sub-app's pathbase to the rendered relative link.</span></span> <span data-ttu-id="fa060-1227">V případě dílčí aplikace na `/subapp_path` je obrázek propojený s objektem `src="~/image.png"` vykreslen jako `src="/subapp_path/image.png"` .</span><span class="sxs-lookup"><span data-stu-id="fa060-1227">For a sub-app at `/subapp_path`, an image linked with `src="~/image.png"` is rendered as `src="/subapp_path/image.png"`.</span></span> <span data-ttu-id="fa060-1228">Middleware statických souborů kořenové aplikace nezpracovává požadavek na statický soubor.</span><span class="sxs-lookup"><span data-stu-id="fa060-1228">The root app's Static File Middleware doesn't process the static file request.</span></span> <span data-ttu-id="fa060-1229">Požadavek zpracovává middleware statických souborů v dílčí aplikaci.</span><span class="sxs-lookup"><span data-stu-id="fa060-1229">The request is processed by the sub-app's Static File Middleware.</span></span>

<span data-ttu-id="fa060-1230">Pokud je atribut statického prostředku `src` nastavený na absolutní cestu (například `src="/image.png"` ), odkaz se vykreslí bez pathbase dílčí aplikace.</span><span class="sxs-lookup"><span data-stu-id="fa060-1230">If a static asset's `src` attribute is set to an absolute path (for example, `src="/image.png"`), the link is rendered without the sub-app's pathbase.</span></span> <span data-ttu-id="fa060-1231">Middleware statických souborů v kořenové aplikaci se pokusí o poskytování assetu z [kořenového adresáře webu](xref:fundamentals/index#web-root)kořenové aplikace, což má za následek *404 – nenalezené* odpovědi, pokud není k dispozici statický prostředek z kořenové aplikace.</span><span class="sxs-lookup"><span data-stu-id="fa060-1231">The root app's Static File Middleware attempts to serve the asset from the root app's [web root](xref:fundamentals/index#web-root), which results in a *404 - Not Found* response unless the static asset is available from the root app.</span></span>

<span data-ttu-id="fa060-1232">Hostování aplikace ASP.NET Core jako dílčí aplikace v jiné aplikaci ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="fa060-1232">To host an ASP.NET Core app as a sub-app under another ASP.NET Core app:</span></span>

1. <span data-ttu-id="fa060-1233">Vytvořte fond aplikací pro dílčí aplikaci.</span><span class="sxs-lookup"><span data-stu-id="fa060-1233">Establish an app pool for the sub-app.</span></span> <span data-ttu-id="fa060-1234">Nastavte **verzi .NET CLR** na **žádný spravovaný kód** , protože základní modul CLR (Common Language Runtime) pro .NET Core se spouští k hostování aplikace v pracovním procesu, ne CLR Desktop (.NET CLR).</span><span class="sxs-lookup"><span data-stu-id="fa060-1234">Set the **.NET CLR Version** to **No Managed Code** because the Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process, not the desktop CLR (.NET CLR).</span></span>

1. <span data-ttu-id="fa060-1235">Přidejte kořenovou lokalitu ve Správci služby IIS pomocí dílčí aplikace do složky v kořenové lokalitě.</span><span class="sxs-lookup"><span data-stu-id="fa060-1235">Add the root site in IIS Manager with the sub-app in a folder under the root site.</span></span>

1. <span data-ttu-id="fa060-1236">Klikněte pravým tlačítkem na složku dílčí aplikace ve Správci služby IIS a vyberte **převést na aplikaci**.</span><span class="sxs-lookup"><span data-stu-id="fa060-1236">Right-click the sub-app folder in IIS Manager and select **Convert to Application**.</span></span>

1. <span data-ttu-id="fa060-1237">V dialogovém okně **Přidat aplikaci** použijte pro **fond aplikací** tlačítko **Vybrat** , abyste přiřadili fond aplikací, který jste vytvořili pro dílčí aplikaci.</span><span class="sxs-lookup"><span data-stu-id="fa060-1237">In the **Add Application** dialog, use the **Select** button for the **Application Pool** to assign the app pool that you created for the sub-app.</span></span> <span data-ttu-id="fa060-1238">Vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="fa060-1238">Select **OK**.</span></span>

<span data-ttu-id="fa060-1239">Přiřazení samostatného fondu aplikací k dílčí aplikaci je požadavek při použití modelu hostování v rámci procesu.</span><span class="sxs-lookup"><span data-stu-id="fa060-1239">The assignment of a separate app pool to the sub-app is a requirement when using the in-process hosting model.</span></span>

<span data-ttu-id="fa060-1240">Další informace o modelu hostování v rámci procesu a konfiguraci modulu ASP.NET Core naleznete v tématu <xref:host-and-deploy/aspnet-core-module> .</span><span class="sxs-lookup"><span data-stu-id="fa060-1240">For more information on the in-process hosting model and configuring the ASP.NET Core Module, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

## <a name="configuration-of-iis-with-webconfig"></a><span data-ttu-id="fa060-1241">Konfigurace služby IIS pomocí souboru Web. config</span><span class="sxs-lookup"><span data-stu-id="fa060-1241">Configuration of IIS with web.config</span></span>

<span data-ttu-id="fa060-1242">Konfigurace služby IIS má vliv na `<system.webServer>` část souboru *Web. config* pro scénáře služby IIS, které jsou funkční pro ASP.NET Core aplikací s modulem ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="fa060-1242">IIS configuration is influenced by the `<system.webServer>` section of *web.config* for IIS scenarios that are functional for ASP.NET Core apps with the ASP.NET Core Module.</span></span> <span data-ttu-id="fa060-1243">Například konfigurace služby IIS je funkční pro dynamickou kompresi.</span><span class="sxs-lookup"><span data-stu-id="fa060-1243">For example, IIS configuration is functional for dynamic compression.</span></span> <span data-ttu-id="fa060-1244">Pokud je služba IIS nakonfigurovaná na úrovni serveru, aby používala dynamickou kompresi, `<urlCompression>` element v souboru *Web. config* aplikace ho může zakázat pro ASP.NET Core aplikaci.</span><span class="sxs-lookup"><span data-stu-id="fa060-1244">If IIS is configured at the server level to use dynamic compression, the `<urlCompression>` element in the app's *web.config* file can disable it for an ASP.NET Core app.</span></span>

<span data-ttu-id="fa060-1245">Další informace najdete v následujících tématech:</span><span class="sxs-lookup"><span data-stu-id="fa060-1245">For more information, see the following topics:</span></span>

* [<span data-ttu-id="fa060-1246">Odkaz na konfiguraci pro\<system.webServer></span><span class="sxs-lookup"><span data-stu-id="fa060-1246">Configuration reference for \<system.webServer></span></span>](/iis/configuration/system.webServer/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>

<span data-ttu-id="fa060-1247">Informace o nastavení proměnných prostředí pro jednotlivé aplikace spuštěné v izolovaných fondech aplikací (podporované pro IIS 10,0 nebo novější) najdete v části *příkaz Appcmd. exe* tématu [proměnné \<environmentVariables> prostředí](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) v referenční dokumentaci služby IIS.</span><span class="sxs-lookup"><span data-stu-id="fa060-1247">To set environment variables for individual apps running in isolated app pools (supported for IIS 10.0 or later), see the *AppCmd.exe command* section of the [Environment Variables \<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) topic in the IIS reference documentation.</span></span>

## <a name="configuration-sections-of-webconfig"></a><span data-ttu-id="fa060-1248">Konfigurační oddíly souboru Web. config</span><span class="sxs-lookup"><span data-stu-id="fa060-1248">Configuration sections of web.config</span></span>

<span data-ttu-id="fa060-1249">Konfigurační oddíly aplikací ASP.NET 4. x v *souboru Web. config* nepoužívá aplikace ASP.NET Core pro konfiguraci:</span><span class="sxs-lookup"><span data-stu-id="fa060-1249">Configuration sections of ASP.NET 4.x apps in *web.config* aren't used by ASP.NET Core apps for configuration:</span></span>

* `<system.web>`
* `<appSettings>`
* `<connectionStrings>`
* `<location>`

<span data-ttu-id="fa060-1250">Aplikace ASP.NET Core jsou nakonfigurovány pomocí jiných poskytovatelů konfigurace.</span><span class="sxs-lookup"><span data-stu-id="fa060-1250">ASP.NET Core apps are configured using other configuration providers.</span></span> <span data-ttu-id="fa060-1251">Další informace najdete v tématu [Konfigurace](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="fa060-1251">For more information, see [Configuration](xref:fundamentals/configuration/index).</span></span>

## <a name="application-pools"></a><span data-ttu-id="fa060-1252">Fondy aplikací</span><span class="sxs-lookup"><span data-stu-id="fa060-1252">Application Pools</span></span>

<span data-ttu-id="fa060-1253">Izolaci fondu aplikací určuje model hostování:</span><span class="sxs-lookup"><span data-stu-id="fa060-1253">App pool isolation is determined by the hosting model:</span></span>

* <span data-ttu-id="fa060-1254">Hostování v procesu: aplikace se vyžadují ke spuštění v samostatných fondech aplikací.</span><span class="sxs-lookup"><span data-stu-id="fa060-1254">In-process hosting: Apps are required to run in separate app pools.</span></span>
* <span data-ttu-id="fa060-1255">Hostování mimo procesy: aplikace doporučujeme izolovat od sebe navzájem tak, že spustíte každou aplikaci ve svém vlastním fondu aplikací.</span><span class="sxs-lookup"><span data-stu-id="fa060-1255">Out-of-process hosting: We recommend isolating the apps from each other by running each app in its own app pool.</span></span>

<span data-ttu-id="fa060-1256">Dialogové okno **Přidat web** do služby IIS je ve výchozím nastavení nastaveno na jeden fond aplikací na aplikaci.</span><span class="sxs-lookup"><span data-stu-id="fa060-1256">The IIS **Add Website** dialog defaults to a single app pool per app.</span></span> <span data-ttu-id="fa060-1257">Když je zadaný **název lokality** , text se automaticky přenese do textového pole **fondu aplikací** .</span><span class="sxs-lookup"><span data-stu-id="fa060-1257">When a **Site name** is provided, the text is automatically transferred to the **Application pool** textbox.</span></span> <span data-ttu-id="fa060-1258">Po přidání webu se vytvoří nový fond aplikací s názvem lokality.</span><span class="sxs-lookup"><span data-stu-id="fa060-1258">A new app pool is created using the site name when the site is added.</span></span>

## <a name="application-pool-identity"></a><span data-ttu-id="fa060-1259">Fond aplikacíIdentity</span><span class="sxs-lookup"><span data-stu-id="fa060-1259">Application Pool Identity</span></span>

<span data-ttu-id="fa060-1260">Účet identity fondu aplikací umožňuje aplikaci běžet v rámci jedinečného účtu, aniž by bylo nutné vytvářet a spravovat domény nebo místní účty.</span><span class="sxs-lookup"><span data-stu-id="fa060-1260">An app pool identity account allows an app to run under a unique account without having to create and manage domains or local accounts.</span></span> <span data-ttu-id="fa060-1261">Ve službě IIS 8,0 nebo novější služba pracovní proces správce služby IIS (WAS) vytvoří virtuální účet s názvem nového fondu aplikací a ve výchozím nastavení spustí pracovní procesy fondu aplikací v rámci tohoto účtu.</span><span class="sxs-lookup"><span data-stu-id="fa060-1261">On IIS 8.0 or later, the IIS Admin Worker Process (WAS) creates a virtual account with the name of the new app pool and runs the app pool's worker processes under this account by default.</span></span> <span data-ttu-id="fa060-1262">V konzole pro správu služby IIS v části **Upřesnit nastavení** fondu aplikací zajistěte, aby byl **Identity** nastaven na použití **ApplicationPoolIdentity**:</span><span class="sxs-lookup"><span data-stu-id="fa060-1262">In the IIS Management Console under **Advanced Settings** for the app pool, ensure that the **Identity** is set to use **ApplicationPoolIdentity**:</span></span>

![Dialogové okno Upřesnit nastavení fondu aplikací](index/_static/apppool-identity.png)

<span data-ttu-id="fa060-1264">Proces správy služby IIS vytvoří v systému zabezpečení systému Windows zabezpečený identifikátor s názvem fondu aplikací.</span><span class="sxs-lookup"><span data-stu-id="fa060-1264">The IIS management process creates a secure identifier with the name of the app pool in the Windows Security System.</span></span> <span data-ttu-id="fa060-1265">Prostředky je možné zabezpečit pomocí této identity.</span><span class="sxs-lookup"><span data-stu-id="fa060-1265">Resources can be secured using this identity.</span></span> <span data-ttu-id="fa060-1266">Tato identita ale není skutečným uživatelským účtem a nezobrazuje se v konzole pro správu uživatelů Windows.</span><span class="sxs-lookup"><span data-stu-id="fa060-1266">However, this identity isn't a real user account and doesn't show up in the Windows User Management Console.</span></span>

<span data-ttu-id="fa060-1267">Pokud pracovní proces služby IIS vyžaduje zvýšený přístup k aplikaci, upravte seznam Access Control (ACL) pro adresář obsahující aplikaci:</span><span class="sxs-lookup"><span data-stu-id="fa060-1267">If the IIS worker process requires elevated access to the app, modify the Access Control List (ACL) for the directory containing the app:</span></span>

1. <span data-ttu-id="fa060-1268">Otevřete Průzkumníka Windows a přejděte do adresáře.</span><span class="sxs-lookup"><span data-stu-id="fa060-1268">Open Windows Explorer and navigate to the directory.</span></span>

1. <span data-ttu-id="fa060-1269">Klikněte pravým tlačítkem na adresář a vyberte **vlastnosti**.</span><span class="sxs-lookup"><span data-stu-id="fa060-1269">Right-click on the directory and select **Properties**.</span></span>

1. <span data-ttu-id="fa060-1270">Na kartě **zabezpečení** vyberte tlačítko **Upravit** a pak klikněte na tlačítko **Přidat** .</span><span class="sxs-lookup"><span data-stu-id="fa060-1270">Under the **Security** tab, select the **Edit** button and then the **Add** button.</span></span>

1. <span data-ttu-id="fa060-1271">Vyberte tlačítko **umístění** a ujistěte se, že je vybraný systém.</span><span class="sxs-lookup"><span data-stu-id="fa060-1271">Select the **Locations** button and make sure the system is selected.</span></span>

1. <span data-ttu-id="fa060-1272">Do pole **Zadejte názvy objektů k výběru** oblasti zadejte \*\* \\<app_pool_name služby IIS AppPool>\*\* .</span><span class="sxs-lookup"><span data-stu-id="fa060-1272">Enter **IIS AppPool\\<app_pool_name>** in **Enter the object names to select** area.</span></span> <span data-ttu-id="fa060-1273">Vyberte tlačítko pro **kontrolu názvů** .</span><span class="sxs-lookup"><span data-stu-id="fa060-1273">Select the **Check Names** button.</span></span> <span data-ttu-id="fa060-1274">Pro službu *DefaultAppPool* ověřte názvy pomocí **služby IIS AppPool\DefaultAppPool**.</span><span class="sxs-lookup"><span data-stu-id="fa060-1274">For the *DefaultAppPool* check the names using **IIS AppPool\DefaultAppPool**.</span></span> <span data-ttu-id="fa060-1275">Když je vybráno tlačítko pro **kontrolu názvů** , hodnota **DefaultAppPool** je uvedena v oblasti názvy objektů.</span><span class="sxs-lookup"><span data-stu-id="fa060-1275">When the **Check Names** button is selected, a value of **DefaultAppPool** is indicated in the object names area.</span></span> <span data-ttu-id="fa060-1276">Není možné zadat název fondu aplikací přímo do oblasti názvy objektů.</span><span class="sxs-lookup"><span data-stu-id="fa060-1276">It isn't possible to enter the app pool name directly into the object names area.</span></span> <span data-ttu-id="fa060-1277">Při kontrole názvu objektu použijte \*\* \\<APP_POOL_NAME>formátu IIS AppPool\*\* .</span><span class="sxs-lookup"><span data-stu-id="fa060-1277">Use the **IIS AppPool\\<app_pool_name>** format when checking for the object name.</span></span>

   ![Dialogová okna pro výběr uživatelů nebo skupin pro složku aplikace: název fondu aplikací "DefaultAppPool" se připojí k "rozhraní IIS AppPool" \" v oblasti názvy objektů před výběrem "kontrolovat jména".](index/_static/select-users-or-groups-1.png)

1. <span data-ttu-id="fa060-1279">Vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="fa060-1279">Select **OK**.</span></span>

   ![Dialogová okna pro výběr uživatelů nebo skupin pro složku aplikace: po výběru možnosti kontrolovat názvy se v oblasti názvy objektů zobrazí název objektu DefaultAppPool.](index/_static/select-users-or-groups-2.png)

1. <span data-ttu-id="fa060-1281">&amp;Ve výchozím nastavení by měla být udělena oprávnění ke čtení.</span><span class="sxs-lookup"><span data-stu-id="fa060-1281">Read &amp; execute permissions should be granted by default.</span></span> <span data-ttu-id="fa060-1282">V případě potřeby zadejte další oprávnění.</span><span class="sxs-lookup"><span data-stu-id="fa060-1282">Provide additional permissions as needed.</span></span>

<span data-ttu-id="fa060-1283">Přístup se dá taky udělit na příkazovém řádku pomocí nástroje **Icacls** .</span><span class="sxs-lookup"><span data-stu-id="fa060-1283">Access can also be granted at a command prompt using the **ICACLS** tool.</span></span> <span data-ttu-id="fa060-1284">Pomocí příkazu *DefaultAppPool* jako příkladu se používá následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="fa060-1284">Using the *DefaultAppPool* as an example, the following command is used:</span></span>

```console
ICACLS C:\sites\MyWebApp /grant "IIS AppPool\DefaultAppPool":F
```

<span data-ttu-id="fa060-1285">Další informace najdete v tématu [Icacls](/windows-server/administration/windows-commands/icacls) .</span><span class="sxs-lookup"><span data-stu-id="fa060-1285">For more information, see the [icacls](/windows-server/administration/windows-commands/icacls) topic.</span></span>

## <a name="http2-support"></a><span data-ttu-id="fa060-1286">Podpora HTTP/2</span><span class="sxs-lookup"><span data-stu-id="fa060-1286">HTTP/2 support</span></span>

<span data-ttu-id="fa060-1287">[Http/2](https://httpwg.org/specs/rfc7540.html) se podporuje s ASP.NET Core v následujících scénářích nasazení služby IIS:</span><span class="sxs-lookup"><span data-stu-id="fa060-1287">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is supported with ASP.NET Core in the following IIS deployment scenarios:</span></span>

* <span data-ttu-id="fa060-1288">V procesu</span><span class="sxs-lookup"><span data-stu-id="fa060-1288">In-process</span></span>
  * <span data-ttu-id="fa060-1289">Windows Server 2016/Windows 10 nebo novější; IIS 10 nebo novější</span><span class="sxs-lookup"><span data-stu-id="fa060-1289">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="fa060-1290">Připojení TLS 1,2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="fa060-1290">TLS 1.2 or later connection</span></span>
* <span data-ttu-id="fa060-1291">Mimo proces</span><span class="sxs-lookup"><span data-stu-id="fa060-1291">Out-of-process</span></span>
  * <span data-ttu-id="fa060-1292">Windows Server 2016/Windows 10 nebo novější; IIS 10 nebo novější</span><span class="sxs-lookup"><span data-stu-id="fa060-1292">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="fa060-1293">Veřejná připojení hraničních serverů používají protokol HTTP/2, ale připojení reverzního proxy [serveru Kestrel](xref:fundamentals/servers/kestrel) používá protokol HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="fa060-1293">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to the [Kestrel server](xref:fundamentals/servers/kestrel) uses HTTP/1.1.</span></span>
  * <span data-ttu-id="fa060-1294">Připojení TLS 1,2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="fa060-1294">TLS 1.2 or later connection</span></span>

<span data-ttu-id="fa060-1295">Pro nasazení v rámci procesu, když je vytvořeno připojení HTTP/2, sestavy [HttpRequest. Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) `HTTP/2` .</span><span class="sxs-lookup"><span data-stu-id="fa060-1295">For an in-process deployment when an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span> <span data-ttu-id="fa060-1296">Pro nasazení mimo proces, když je vytvořeno připojení HTTP/2, sestavy [HttpRequest. Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) `HTTP/1.1` .</span><span class="sxs-lookup"><span data-stu-id="fa060-1296">For an out-of-process deployment when an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/1.1`.</span></span>

<span data-ttu-id="fa060-1297">Další informace o modelech hostování v procesu a mimoprocesové procesy naleznete v tématu <xref:host-and-deploy/aspnet-core-module> .</span><span class="sxs-lookup"><span data-stu-id="fa060-1297">For more information on the in-process and out-of-process hosting models, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="fa060-1298">HTTP/2 je ve výchozím nastavení povolené.</span><span class="sxs-lookup"><span data-stu-id="fa060-1298">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="fa060-1299">Pokud není navázáno připojení HTTP/2, připojení se vrátí k HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="fa060-1299">Connections fall back to HTTP/1.1 if an HTTP/2 connection isn't established.</span></span> <span data-ttu-id="fa060-1300">Další informace o konfiguraci HTTP/2 v nasazeních služby IIS najdete v tématu [http/2 ve službě IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).</span><span class="sxs-lookup"><span data-stu-id="fa060-1300">For more information on HTTP/2 configuration with IIS deployments, see [HTTP/2 on IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).</span></span>

## <a name="cors-preflight-requests"></a><span data-ttu-id="fa060-1301">Požadavky na kontrolu před výstupem CORS</span><span class="sxs-lookup"><span data-stu-id="fa060-1301">CORS preflight requests</span></span>

<span data-ttu-id="fa060-1302">*Tato část platí jenom pro ASP.NET Core aplikace, které cílí na .NET Framework.*</span><span class="sxs-lookup"><span data-stu-id="fa060-1302">*This section only applies to ASP.NET Core apps that target the .NET Framework.*</span></span>

<span data-ttu-id="fa060-1303">Pro ASP.NET Core aplikaci, která cílí na .NET Framework, požadavky na možnosti nejsou ve výchozím nastavení ve službě IIS předány do aplikace.</span><span class="sxs-lookup"><span data-stu-id="fa060-1303">For an ASP.NET Core app that targets the .NET Framework, OPTIONS requests aren't passed to the app by default in IIS.</span></span> <span data-ttu-id="fa060-1304">Informace o tom, jak nakonfigurovat obslužné rutiny IIS aplikace v *souboru Web. config* , aby předávala požadavky na možnosti, najdete v tématu [Povolení žádostí o více zdrojů v ASP.NET webovém rozhraní API 2: jak CORS funguje](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works).</span><span class="sxs-lookup"><span data-stu-id="fa060-1304">To learn how to configure the app's IIS handlers in *web.config* to pass OPTIONS requests, see [Enable cross-origin requests in ASP.NET Web API 2: How CORS Works](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works).</span></span>

## <a name="application-initialization-module-and-idle-timeout"></a><span data-ttu-id="fa060-1305">Modul inicializace aplikace a časový limit nečinnosti</span><span class="sxs-lookup"><span data-stu-id="fa060-1305">Application Initialization Module and Idle Timeout</span></span>

<span data-ttu-id="fa060-1306">Při hostování ve službě IIS pomocí modulu ASP.NET Core verze 2:</span><span class="sxs-lookup"><span data-stu-id="fa060-1306">When hosted in IIS by the ASP.NET Core Module version 2:</span></span>

* <span data-ttu-id="fa060-1307">[Modul inicializace aplikace](#application-initialization-module): hostovaný [v procesu](#in-process-hosting-model) nebo [mimoprocesové](#out-of-process-hosting-model) aplikaci aplikace je možné nakonfigurovat tak, aby se automaticky spouštěla v pracovním procesu nebo na restartování serveru.</span><span class="sxs-lookup"><span data-stu-id="fa060-1307">[Application Initialization Module](#application-initialization-module): App's hosted [in-process](#in-process-hosting-model) or [out-of-process](#out-of-process-hosting-model) can be configured to start automatically on a worker process restart or server restart.</span></span>
* <span data-ttu-id="fa060-1308">[Časový limit nečinnosti](#idle-timeout): hostovaný [v procesu](#in-process-hosting-model) aplikace se dá nakonfigurovat tak, aby nevypršel časový limit v období nečinnosti.</span><span class="sxs-lookup"><span data-stu-id="fa060-1308">[Idle Timeout](#idle-timeout): App's hosted [in-process](#in-process-hosting-model) can be configured not to timeout during periods of inactivity.</span></span>

### <a name="application-initialization-module"></a><span data-ttu-id="fa060-1309">Modul inicializace aplikace</span><span class="sxs-lookup"><span data-stu-id="fa060-1309">Application Initialization Module</span></span>

<span data-ttu-id="fa060-1310">*Platí pro aplikace hostované v procesu a mimo proces.*</span><span class="sxs-lookup"><span data-stu-id="fa060-1310">*Applies to apps hosted in-process and out-of-process.*</span></span>

<span data-ttu-id="fa060-1311">[Inicializace aplikace IIS](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) je funkce služby IIS, která do aplikace pošle požadavek HTTP, když se spustí nebo recykluje fond aplikací.</span><span class="sxs-lookup"><span data-stu-id="fa060-1311">[IIS Application Initialization](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) is an IIS feature that sends an HTTP request to the app when the app pool starts or is recycled.</span></span> <span data-ttu-id="fa060-1312">Požadavek spustí spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="fa060-1312">The request triggers the app to start.</span></span> <span data-ttu-id="fa060-1313">Služba IIS ve výchozím nastavení vydá požadavek na kořenovou adresu URL aplikace ( `/` ) k inicializaci aplikace (Další informace o konfiguraci najdete v [dalších zdrojích](#application-initialization-module-and-idle-timeout-additional-resources) ).</span><span class="sxs-lookup"><span data-stu-id="fa060-1313">By default, IIS issues a request to the app's root URL (`/`) to initialize the app (see the [additional resources](#application-initialization-module-and-idle-timeout-additional-resources) for more details on configuration).</span></span>

<span data-ttu-id="fa060-1314">Potvrďte, že je povolená funkce role inicializace aplikace služby IIS:</span><span class="sxs-lookup"><span data-stu-id="fa060-1314">Confirm that the IIS Application Initialization role feature in enabled:</span></span>

<span data-ttu-id="fa060-1315">V systémech Windows 7 nebo novějších stolních počítačích při používání služby IIS v místním prostředí:</span><span class="sxs-lookup"><span data-stu-id="fa060-1315">On Windows 7 or later desktop systems when using IIS locally:</span></span>

1. <span data-ttu-id="fa060-1316">Přejděte na **Ovládací panely** > **programy** programy > **a funkce** > **zapnout nebo vypnout funkce systému Windows** (levá strana obrazovky).</span><span class="sxs-lookup"><span data-stu-id="fa060-1316">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="fa060-1317">Otevřete **Internetová informační služba** > **World Wide Web Services** > **funkce pro vývoj aplikací**webové služby.</span><span class="sxs-lookup"><span data-stu-id="fa060-1317">Open **Internet Information Services** > **World Wide Web Services** > **Application Development Features**.</span></span>
1. <span data-ttu-id="fa060-1318">Zaškrtněte políčko pro **inicializaci aplikace**.</span><span class="sxs-lookup"><span data-stu-id="fa060-1318">Select the check box for **Application Initialization**.</span></span>

<span data-ttu-id="fa060-1319">V systému Windows Server 2008 R2 nebo novějším:</span><span class="sxs-lookup"><span data-stu-id="fa060-1319">On Windows Server 2008 R2 or later:</span></span>

1. <span data-ttu-id="fa060-1320">Otevřete **Průvodce přidáním rolí a funkcí**.</span><span class="sxs-lookup"><span data-stu-id="fa060-1320">Open the **Add Roles and Features Wizard**.</span></span>
1. <span data-ttu-id="fa060-1321">Na panelu **Vybrat služby rolí** otevřete uzel **vývoj aplikací** .</span><span class="sxs-lookup"><span data-stu-id="fa060-1321">In the **Select role services** panel, open the **Application Development** node.</span></span>
1. <span data-ttu-id="fa060-1322">Zaškrtněte políčko pro **inicializaci aplikace**.</span><span class="sxs-lookup"><span data-stu-id="fa060-1322">Select the check box for **Application Initialization**.</span></span>

<span data-ttu-id="fa060-1323">Pomocí některého z následujících přístupů povolte modul inicializace aplikace pro danou lokalitu:</span><span class="sxs-lookup"><span data-stu-id="fa060-1323">Use either of the following approaches to enable the Application Initialization Module for the site:</span></span>

* <span data-ttu-id="fa060-1324">Pomocí Správce služby IIS:</span><span class="sxs-lookup"><span data-stu-id="fa060-1324">Using IIS Manager:</span></span>

  1. <span data-ttu-id="fa060-1325">Na panelu **připojení** vyberte **fondy aplikací** .</span><span class="sxs-lookup"><span data-stu-id="fa060-1325">Select **Application Pools** in the **Connections** panel.</span></span>
  1. <span data-ttu-id="fa060-1326">V seznamu klikněte pravým tlačítkem na fond aplikací aplikace a vyberte **Upřesnit nastavení**.</span><span class="sxs-lookup"><span data-stu-id="fa060-1326">Right-click the app's app pool in the list and select **Advanced Settings**.</span></span>
  1. <span data-ttu-id="fa060-1327">Výchozí **režim spuštění** je **OnDemand**.</span><span class="sxs-lookup"><span data-stu-id="fa060-1327">The default **Start Mode** is **OnDemand**.</span></span> <span data-ttu-id="fa060-1328">Nastavte **režim Start** na **AlwaysRunning**.</span><span class="sxs-lookup"><span data-stu-id="fa060-1328">Set the **Start Mode** to **AlwaysRunning**.</span></span> <span data-ttu-id="fa060-1329">Vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="fa060-1329">Select **OK**.</span></span>
  1. <span data-ttu-id="fa060-1330">Otevřete uzel **lokality** na panelu **připojení** .</span><span class="sxs-lookup"><span data-stu-id="fa060-1330">Open the **Sites** node in the **Connections** panel.</span></span>
  1. <span data-ttu-id="fa060-1331">Klikněte pravým tlačítkem na aplikaci a vyberte **Spravovat** > **Rozšířená nastavení**webu.</span><span class="sxs-lookup"><span data-stu-id="fa060-1331">Right-click the app and select **Manage Website** > **Advanced Settings**.</span></span>
  1. <span data-ttu-id="fa060-1332">Výchozí nastavení pro **Povolení přednačtení** je **false**.</span><span class="sxs-lookup"><span data-stu-id="fa060-1332">The default **Preload Enabled** setting is **False**.</span></span> <span data-ttu-id="fa060-1333">Nastavte **předběžné načtení povoleno** na **hodnotu true**.</span><span class="sxs-lookup"><span data-stu-id="fa060-1333">Set **Preload Enabled** to **True**.</span></span> <span data-ttu-id="fa060-1334">Vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="fa060-1334">Select **OK**.</span></span>

* <span data-ttu-id="fa060-1335">Pomocí souboru *Web. config*přidejte `<applicationInitialization>` element s `doAppInitAfterRestart` nastavením do `true` `<system.webServer>` prvků v souboru *Web. config* aplikace:</span><span class="sxs-lookup"><span data-stu-id="fa060-1335">Using *web.config*, add the `<applicationInitialization>` element with `doAppInitAfterRestart` set to `true` to the `<system.webServer>` elements in the app's *web.config* file:</span></span>

  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <configuration>
    <location path="." inheritInChildApplications="false">
      <system.webServer>
        <applicationInitialization doAppInitAfterRestart="true" />
      </system.webServer>
    </location>
  </configuration>
  ```

### <a name="idle-timeout"></a><span data-ttu-id="fa060-1336">Časový limit nečinnosti</span><span class="sxs-lookup"><span data-stu-id="fa060-1336">Idle Timeout</span></span>

<span data-ttu-id="fa060-1337">*Platí jenom pro aplikace hostované v procesu.*</span><span class="sxs-lookup"><span data-stu-id="fa060-1337">*Only applies to apps hosted in-process.*</span></span>

<span data-ttu-id="fa060-1338">Pokud chcete zabránit volnoběhu aplikace, nastavte časový limit nečinnosti fondu aplikací pomocí Správce služby IIS:</span><span class="sxs-lookup"><span data-stu-id="fa060-1338">To prevent the app from idling, set the app pool's idle timeout using IIS Manager:</span></span>

1. <span data-ttu-id="fa060-1339">Na panelu **připojení** vyberte **fondy aplikací** .</span><span class="sxs-lookup"><span data-stu-id="fa060-1339">Select **Application Pools** in the **Connections** panel.</span></span>
1. <span data-ttu-id="fa060-1340">V seznamu klikněte pravým tlačítkem na fond aplikací aplikace a vyberte **Upřesnit nastavení**.</span><span class="sxs-lookup"><span data-stu-id="fa060-1340">Right-click the app's app pool in the list and select **Advanced Settings**.</span></span>
1. <span data-ttu-id="fa060-1341">Výchozí **časový limit nečinnosti (minuty)** je **20** minut.</span><span class="sxs-lookup"><span data-stu-id="fa060-1341">The default **Idle Time-out (minutes)** is **20** minutes.</span></span> <span data-ttu-id="fa060-1342">Nastavte **časový limit nečinnosti (minuty)** na **0** (nula).</span><span class="sxs-lookup"><span data-stu-id="fa060-1342">Set the **Idle Time-out (minutes)** to **0** (zero).</span></span> <span data-ttu-id="fa060-1343">Vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="fa060-1343">Select **OK**.</span></span>
1. <span data-ttu-id="fa060-1344">Recyklujte pracovní proces.</span><span class="sxs-lookup"><span data-stu-id="fa060-1344">Recycle the worker process.</span></span>

<span data-ttu-id="fa060-1345">Chcete-li zabránit aplikacím hostovaným v [procesu](#out-of-process-hosting-model) vypršení časového limitu, použijte některý z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="fa060-1345">To prevent apps hosted [out-of-process](#out-of-process-hosting-model) from timing out, use either of the following approaches:</span></span>

* <span data-ttu-id="fa060-1346">Pomocí příkazu otestujete aplikaci z externí služby, aby byla spuštěná.</span><span class="sxs-lookup"><span data-stu-id="fa060-1346">Ping the app from an external service in order to keep it running.</span></span>
* <span data-ttu-id="fa060-1347">Pokud aplikace hostuje jenom služby na pozadí, vyhněte se hostování služby IIS a použití [služby Windows k hostování aplikace ASP.NET Core](xref:host-and-deploy/windows-service).</span><span class="sxs-lookup"><span data-stu-id="fa060-1347">If the app only hosts background services, avoid IIS hosting and use a [Windows Service to host the ASP.NET Core app](xref:host-and-deploy/windows-service).</span></span>

### <a name="application-initialization-module-and-idle-timeout-additional-resources"></a><span data-ttu-id="fa060-1348">Modul inicializace aplikace a další prostředky časového limitu nečinnosti</span><span class="sxs-lookup"><span data-stu-id="fa060-1348">Application Initialization Module and Idle Timeout additional resources</span></span>

* [<span data-ttu-id="fa060-1349">Inicializace aplikace IIS 8,0</span><span class="sxs-lookup"><span data-stu-id="fa060-1349">IIS 8.0 Application Initialization</span></span>](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization)
* <span data-ttu-id="fa060-1350">[Inicializace \<applicationInitialization> aplikace ](/iis/configuration/system.webserver/applicationinitialization/)</span><span class="sxs-lookup"><span data-stu-id="fa060-1350">[Application Initialization \<applicationInitialization>](/iis/configuration/system.webserver/applicationinitialization/).</span></span>
* <span data-ttu-id="fa060-1351">[Nastavení modelu procesu pro \<processModel> fond aplikací ](/iis/configuration/system.applicationhost/applicationpools/add/processmodel).</span><span class="sxs-lookup"><span data-stu-id="fa060-1351">[Process Model Settings for an Application Pool \<processModel>](/iis/configuration/system.applicationhost/applicationpools/add/processmodel).</span></span>

## <a name="deployment-resources-for-iis-administrators"></a><span data-ttu-id="fa060-1352">Prostředky nasazení pro správce služby IIS</span><span class="sxs-lookup"><span data-stu-id="fa060-1352">Deployment resources for IIS administrators</span></span>

* [<span data-ttu-id="fa060-1353">Dokumentace ke službě IIS</span><span class="sxs-lookup"><span data-stu-id="fa060-1353">IIS documentation</span></span>](/iis)
* [<span data-ttu-id="fa060-1354">Začínáme ve službě IIS pomocí Správce služby IIS</span><span class="sxs-lookup"><span data-stu-id="fa060-1354">Getting Started with the IIS Manager in IIS</span></span>](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* [<span data-ttu-id="fa060-1355">Nasazení aplikace .NET Core</span><span class="sxs-lookup"><span data-stu-id="fa060-1355">.NET Core application deployment</span></span>](/dotnet/core/deploying/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:host-and-deploy/iis/modules>
* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

## <a name="additional-resources"></a><span data-ttu-id="fa060-1356">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="fa060-1356">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:index>
* [<span data-ttu-id="fa060-1357">Oficiální web Microsoft IIS</span><span class="sxs-lookup"><span data-stu-id="fa060-1357">The Official Microsoft IIS Site</span></span>](https://www.iis.net/)
* [<span data-ttu-id="fa060-1358">Knihovna technických obsahu pro Windows Server</span><span class="sxs-lookup"><span data-stu-id="fa060-1358">Windows Server technical content library</span></span>](/windows-server/windows-server)
* [<span data-ttu-id="fa060-1359">HTTP/2 ve službě IIS</span><span class="sxs-lookup"><span data-stu-id="fa060-1359">HTTP/2 on IIS</span></span>](/iis/get-started/whats-new-in-iis-10/http2-on-iis)
* <xref:host-and-deploy/iis/transform-webconfig>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="fa060-1360">Kurz týkající se publikování ASP.NET Core aplikace na server služby IIS najdete v tématu <xref:tutorials/publish-to-iis> .</span><span class="sxs-lookup"><span data-stu-id="fa060-1360">For a tutorial experience on publishing an ASP.NET Core app to an IIS server, see <xref:tutorials/publish-to-iis>.</span></span>

[<span data-ttu-id="fa060-1361">Instalace hostující sady .NET Core</span><span class="sxs-lookup"><span data-stu-id="fa060-1361">Install the .NET Core Hosting Bundle</span></span>](#install-the-net-core-hosting-bundle)

## <a name="supported-operating-systems"></a><span data-ttu-id="fa060-1362">Podporované operační systémy</span><span class="sxs-lookup"><span data-stu-id="fa060-1362">Supported operating systems</span></span>

<span data-ttu-id="fa060-1363">Podporovány jsou následující operační systémy:</span><span class="sxs-lookup"><span data-stu-id="fa060-1363">The following operating systems are supported:</span></span>

* <span data-ttu-id="fa060-1364">Windows 7 nebo novější</span><span class="sxs-lookup"><span data-stu-id="fa060-1364">Windows 7 or later</span></span>
* <span data-ttu-id="fa060-1365">Windows Server 2008 R2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="fa060-1365">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="fa060-1366">[Http. sys Server](xref:fundamentals/servers/httpsys) (dříve označovaný jako weblisten) nefunguje v konfiguraci reverzního proxy serveru se službou IIS.</span><span class="sxs-lookup"><span data-stu-id="fa060-1366">[HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener) doesn't work in a reverse proxy configuration with IIS.</span></span> <span data-ttu-id="fa060-1367">Použijte [Server Kestrel](xref:fundamentals/servers/kestrel).</span><span class="sxs-lookup"><span data-stu-id="fa060-1367">Use the [Kestrel server](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="fa060-1368">Informace o hostování v Azure najdete v tématu <xref:host-and-deploy/azure-apps/index> .</span><span class="sxs-lookup"><span data-stu-id="fa060-1368">For information on hosting in Azure, see <xref:host-and-deploy/azure-apps/index>.</span></span>

<span data-ttu-id="fa060-1369">Pokyny k řešení potíží najdete v tématu <xref:test/troubleshoot> .</span><span class="sxs-lookup"><span data-stu-id="fa060-1369">For troubleshooting guidance, see <xref:test/troubleshoot>.</span></span>

## <a name="supported-platforms"></a><span data-ttu-id="fa060-1370">Podporované platformy</span><span class="sxs-lookup"><span data-stu-id="fa060-1370">Supported platforms</span></span>

<span data-ttu-id="fa060-1371">Podporují se aplikace publikované pro nasazení 32 (x86) nebo 64-bit (x64).</span><span class="sxs-lookup"><span data-stu-id="fa060-1371">Apps published for 32-bit (x86) or 64-bit (x64) deployment are supported.</span></span> <span data-ttu-id="fa060-1372">Nasazení 32 aplikace s 32 (x86) .NET Core SDK, pokud aplikace:</span><span class="sxs-lookup"><span data-stu-id="fa060-1372">Deploy a 32-bit app with a 32-bit (x86) .NET Core SDK unless the app:</span></span>

* <span data-ttu-id="fa060-1373">Vyžaduje větší dostupný adresní prostor virtuální paměti pro 64 aplikaci.</span><span class="sxs-lookup"><span data-stu-id="fa060-1373">Requires the larger virtual memory address space available to a 64-bit app.</span></span>
* <span data-ttu-id="fa060-1374">Vyžaduje větší velikost zásobníku služby IIS.</span><span class="sxs-lookup"><span data-stu-id="fa060-1374">Requires the larger IIS stack size.</span></span>
* <span data-ttu-id="fa060-1375">Má 64 nativní závislosti.</span><span class="sxs-lookup"><span data-stu-id="fa060-1375">Has 64-bit native dependencies.</span></span>

<span data-ttu-id="fa060-1376">K publikování 64 aplikace použijte 64 .NET Core SDK (x64).</span><span class="sxs-lookup"><span data-stu-id="fa060-1376">Use a 64-bit (x64) .NET Core SDK to publish a 64-bit app.</span></span> <span data-ttu-id="fa060-1377">V hostitelském systému musí být nainstalován 64 modul runtime.</span><span class="sxs-lookup"><span data-stu-id="fa060-1377">A 64-bit runtime must be present on the host system.</span></span>

<span data-ttu-id="fa060-1378">ASP.NET Core se dodává se [serverem Kestrel](xref:fundamentals/servers/kestrel), výchozím serverem HTTP pro různé platformy.</span><span class="sxs-lookup"><span data-stu-id="fa060-1378">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), a default, cross-platform HTTP server.</span></span>

<span data-ttu-id="fa060-1379">Při použití [služby IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) nebo [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)aplikace běží v procesu odděleně od pracovního procesu služby IIS (*mimo proces*) se [serverem Kestrel](xref:fundamentals/servers/index#kestrel).</span><span class="sxs-lookup"><span data-stu-id="fa060-1379">When using [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), the app runs in a process separate from the IIS worker process (*out-of-process*) with the [Kestrel server](xref:fundamentals/servers/index#kestrel).</span></span>

<span data-ttu-id="fa060-1380">Vzhledem k tomu, že ASP.NET Core aplikace běží v procesu odděleném od pracovního procesu služby IIS, modul zpracovává správu procesů.</span><span class="sxs-lookup"><span data-stu-id="fa060-1380">Because ASP.NET Core apps run in a process separate from the IIS worker process, the module handles process management.</span></span> <span data-ttu-id="fa060-1381">Modul spustí proces pro aplikaci ASP.NET Core, když první požadavek dorazí a restartuje aplikaci, pokud se ukončí nebo dojde k chybě.</span><span class="sxs-lookup"><span data-stu-id="fa060-1381">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="fa060-1382">To je v podstatě stejné chování jako u aplikací, které běží v procesu, které jsou spravovány službou was [(Windows Process Activation Service)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span><span class="sxs-lookup"><span data-stu-id="fa060-1382">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="fa060-1383">Následující diagram znázorňuje vztah mezi službou IIS, modulem ASP.NET Core a aplikací hostovanou mimo proces:</span><span class="sxs-lookup"><span data-stu-id="fa060-1383">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted out-of-process:</span></span>

![Modul ASP.NET Core](index/_static/ancm-outofprocess.png)

<span data-ttu-id="fa060-1385">Požadavky přicházející z webu do ovladače HTTP. sys v režimu jádra.</span><span class="sxs-lookup"><span data-stu-id="fa060-1385">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="fa060-1386">Ovladač směruje požadavky do služby IIS na konfigurovaném portu webu, obvykle 80 (HTTP) nebo 443 (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="fa060-1386">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="fa060-1387">Modul předá požadavky do Kestrel na náhodném portu pro aplikaci, což není port 80 nebo 443.</span><span class="sxs-lookup"><span data-stu-id="fa060-1387">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="fa060-1388">Modul Určuje port prostřednictvím proměnné prostředí při spuštění a [Služba IIS Integration middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) nakonfiguruje server, na kterém má naslouchat `http://localhost:{port}` .</span><span class="sxs-lookup"><span data-stu-id="fa060-1388">The module specifies the port via an environment variable at startup, and the [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) configures the server to listen on `http://localhost:{port}`.</span></span> <span data-ttu-id="fa060-1389">Budou provedeny další kontroly a požadavky, které nepocházejí z modulu, jsou odmítnuty.</span><span class="sxs-lookup"><span data-stu-id="fa060-1389">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="fa060-1390">Modul nepodporuje předávání HTTPS, takže požadavky se předávají přes protokol HTTP i v případě, že je služba IIS prostřednictvím protokolu HTTPS přijímá.</span><span class="sxs-lookup"><span data-stu-id="fa060-1390">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="fa060-1391">Po Kestrel žádosti z modulu se požadavek odešle do kanálu middlewaru ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="fa060-1391">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="fa060-1392">Kanál middlewaru zpracovává požadavek a předá ho jako `HttpContext` instanci do logiky aplikace.</span><span class="sxs-lookup"><span data-stu-id="fa060-1392">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="fa060-1393">Middleware přidaný integrací služby IIS: aktualizace schématu, vzdálené IP adresy a pathbase pro přesměrování požadavku do Kestrel.</span><span class="sxs-lookup"><span data-stu-id="fa060-1393">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="fa060-1394">Odpověď aplikace se předává zpátky do služby IIS, která ji přenáší zpátky do klienta HTTP, který žádost inicioval.</span><span class="sxs-lookup"><span data-stu-id="fa060-1394">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="fa060-1395">`CreateDefaultBuilder`nakonfiguruje server [Kestrel](xref:fundamentals/servers/kestrel) jako webový server a povolí integraci služby IIS konfigurací základní cesty a portu pro [modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="fa060-1395">`CreateDefaultBuilder` configures [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and enables IIS Integration by configuring the base path and port for the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span>

<span data-ttu-id="fa060-1396">Modul ASP.NET Core generuje dynamický port, který se přiřadí back-end procesu.</span><span class="sxs-lookup"><span data-stu-id="fa060-1396">The ASP.NET Core Module generates a dynamic port to assign to the backend process.</span></span> <span data-ttu-id="fa060-1397">`CreateDefaultBuilder`volá <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> metodu.</span><span class="sxs-lookup"><span data-stu-id="fa060-1397">`CreateDefaultBuilder` calls the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> method.</span></span> <span data-ttu-id="fa060-1398">`UseIISIntegration`Konfiguruje Kestrel k naslouchání na dynamickém portu na IP adrese místního hostitele ( `127.0.0.1` ).</span><span class="sxs-lookup"><span data-stu-id="fa060-1398">`UseIISIntegration` configures Kestrel to listen on the dynamic port at the localhost IP address (`127.0.0.1`).</span></span> <span data-ttu-id="fa060-1399">Pokud je dynamický port 1234, Kestrel naslouchá na `127.0.0.1:1234` .</span><span class="sxs-lookup"><span data-stu-id="fa060-1399">If the dynamic port is 1234, Kestrel listens at `127.0.0.1:1234`.</span></span> <span data-ttu-id="fa060-1400">Tato konfigurace nahrazuje jiné konfigurace adres URL, které poskytl:</span><span class="sxs-lookup"><span data-stu-id="fa060-1400">This configuration replaces other URL configurations provided by:</span></span>

* `UseUrls`
* [<span data-ttu-id="fa060-1401">Rozhraní API pro naslouchání Kestrel</span><span class="sxs-lookup"><span data-stu-id="fa060-1401">Kestrel's Listen API</span></span>](xref:fundamentals/servers/kestrel#endpoint-configuration)
* <span data-ttu-id="fa060-1402">[Konfigurace](xref:fundamentals/configuration/index) (nebo [příkazového řádku – možnost adresy URL](xref:fundamentals/host/web-host#override-configuration))</span><span class="sxs-lookup"><span data-stu-id="fa060-1402">[Configuration](xref:fundamentals/configuration/index) (or [command-line --urls option](xref:fundamentals/host/web-host#override-configuration))</span></span>

<span data-ttu-id="fa060-1403">`UseUrls` `Listen` Při použití modulu se nevyžadují volání rozhraní API nebo Kestrel.</span><span class="sxs-lookup"><span data-stu-id="fa060-1403">Calls to `UseUrls` or Kestrel's `Listen` API aren't required when using the module.</span></span> <span data-ttu-id="fa060-1404">Pokud `UseUrls` `Listen` je volána nebo, Kestrel naslouchá na portu určeném pouze při spuštění aplikace bez služby IIS.</span><span class="sxs-lookup"><span data-stu-id="fa060-1404">If `UseUrls` or `Listen` is called, Kestrel listens on the port specified only when running the app without IIS.</span></span>

<span data-ttu-id="fa060-1405">Pokyny ke konfiguraci ASP.NET Core modulu najdete v tématu <xref:host-and-deploy/aspnet-core-module> .</span><span class="sxs-lookup"><span data-stu-id="fa060-1405">For ASP.NET Core Module configuration guidance, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="fa060-1406">Další informace o hostování najdete v tématu [hostitel v ASP.NET Core](xref:fundamentals/index#host).</span><span class="sxs-lookup"><span data-stu-id="fa060-1406">For more information on hosting, see [Host in ASP.NET Core](xref:fundamentals/index#host).</span></span>

## <a name="application-configuration"></a><span data-ttu-id="fa060-1407">Konfigurace aplikací</span><span class="sxs-lookup"><span data-stu-id="fa060-1407">Application configuration</span></span>

### <a name="enable-the-iisintegration-components"></a><span data-ttu-id="fa060-1408">Povolit komponenty IISIntegration</span><span class="sxs-lookup"><span data-stu-id="fa060-1408">Enable the IISIntegration components</span></span>

<span data-ttu-id="fa060-1409">Při sestavování hostitele v `CreateWebHostBuilder` (*program.cs*) zavolejte <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> na Povolit integraci služby IIS:</span><span class="sxs-lookup"><span data-stu-id="fa060-1409">When building a host in `CreateWebHostBuilder` (*Program.cs*), call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> to enable IIS integration:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        ...
```

<span data-ttu-id="fa060-1410">Další informace o `CreateDefaultBuilder` naleznete v tématu <xref:fundamentals/host/web-host#set-up-a-host> .</span><span class="sxs-lookup"><span data-stu-id="fa060-1410">For more information on `CreateDefaultBuilder`, see <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

### <a name="iis-options"></a><span data-ttu-id="fa060-1411">Možnosti služby IIS</span><span class="sxs-lookup"><span data-stu-id="fa060-1411">IIS options</span></span>

| <span data-ttu-id="fa060-1412">Možnost</span><span class="sxs-lookup"><span data-stu-id="fa060-1412">Option</span></span>                         | <span data-ttu-id="fa060-1413">Výchozí</span><span class="sxs-lookup"><span data-stu-id="fa060-1413">Default</span></span> | <span data-ttu-id="fa060-1414">Nastavení</span><span class="sxs-lookup"><span data-stu-id="fa060-1414">Setting</span></span> |
| ---
<span data-ttu-id="fa060-1415">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="fa060-1415">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fa060-1416">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fa060-1416">'Blazor'</span></span>
- <span data-ttu-id="fa060-1417">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fa060-1417">'Identity'</span></span>
- <span data-ttu-id="fa060-1418">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fa060-1418">'Let's Encrypt'</span></span>
- <span data-ttu-id="fa060-1419">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fa060-1419">'Razor'</span></span>
- <span data-ttu-id="fa060-1420">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="fa060-1420">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fa060-1421">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="fa060-1421">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fa060-1422">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fa060-1422">'Blazor'</span></span>
- <span data-ttu-id="fa060-1423">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fa060-1423">'Identity'</span></span>
- <span data-ttu-id="fa060-1424">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fa060-1424">'Let's Encrypt'</span></span>
- <span data-ttu-id="fa060-1425">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fa060-1425">'Razor'</span></span>
- <span data-ttu-id="fa060-1426">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="fa060-1426">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fa060-1427">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="fa060-1427">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fa060-1428">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fa060-1428">'Blazor'</span></span>
- <span data-ttu-id="fa060-1429">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fa060-1429">'Identity'</span></span>
- <span data-ttu-id="fa060-1430">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fa060-1430">'Let's Encrypt'</span></span>
- <span data-ttu-id="fa060-1431">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fa060-1431">'Razor'</span></span>
- <span data-ttu-id="fa060-1432">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="fa060-1432">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fa060-1433">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="fa060-1433">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fa060-1434">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fa060-1434">'Blazor'</span></span>
- <span data-ttu-id="fa060-1435">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fa060-1435">'Identity'</span></span>
- <span data-ttu-id="fa060-1436">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fa060-1436">'Let's Encrypt'</span></span>
- <span data-ttu-id="fa060-1437">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fa060-1437">'Razor'</span></span>
- <span data-ttu-id="fa060-1438">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="fa060-1438">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fa060-1439">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="fa060-1439">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fa060-1440">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fa060-1440">'Blazor'</span></span>
- <span data-ttu-id="fa060-1441">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fa060-1441">'Identity'</span></span>
- <span data-ttu-id="fa060-1442">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fa060-1442">'Let's Encrypt'</span></span>
- <span data-ttu-id="fa060-1443">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fa060-1443">'Razor'</span></span>
- <span data-ttu-id="fa060-1444">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="fa060-1444">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fa060-1445">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="fa060-1445">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fa060-1446">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fa060-1446">'Blazor'</span></span>
- <span data-ttu-id="fa060-1447">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fa060-1447">'Identity'</span></span>
- <span data-ttu-id="fa060-1448">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fa060-1448">'Let's Encrypt'</span></span>
- <span data-ttu-id="fa060-1449">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fa060-1449">'Razor'</span></span>
- <span data-ttu-id="fa060-1450">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="fa060-1450">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fa060-1451">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="fa060-1451">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fa060-1452">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fa060-1452">'Blazor'</span></span>
- <span data-ttu-id="fa060-1453">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fa060-1453">'Identity'</span></span>
- <span data-ttu-id="fa060-1454">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fa060-1454">'Let's Encrypt'</span></span>
- <span data-ttu-id="fa060-1455">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fa060-1455">'Razor'</span></span>
- <span data-ttu-id="fa060-1456">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="fa060-1456">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fa060-1457">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="fa060-1457">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fa060-1458">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fa060-1458">'Blazor'</span></span>
- <span data-ttu-id="fa060-1459">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fa060-1459">'Identity'</span></span>
- <span data-ttu-id="fa060-1460">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fa060-1460">'Let's Encrypt'</span></span>
- <span data-ttu-id="fa060-1461">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fa060-1461">'Razor'</span></span>
- <span data-ttu-id="fa060-1462">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="fa060-1462">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fa060-1463">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="fa060-1463">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fa060-1464">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fa060-1464">'Blazor'</span></span>
- <span data-ttu-id="fa060-1465">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fa060-1465">'Identity'</span></span>
- <span data-ttu-id="fa060-1466">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fa060-1466">'Let's Encrypt'</span></span>
- <span data-ttu-id="fa060-1467">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fa060-1467">'Razor'</span></span>
- <span data-ttu-id="fa060-1468">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="fa060-1468">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fa060-1469">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="fa060-1469">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fa060-1470">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fa060-1470">'Blazor'</span></span>
- <span data-ttu-id="fa060-1471">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fa060-1471">'Identity'</span></span>
- <span data-ttu-id="fa060-1472">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fa060-1472">'Let's Encrypt'</span></span>
- <span data-ttu-id="fa060-1473">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fa060-1473">'Razor'</span></span>
- <span data-ttu-id="fa060-1474">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="fa060-1474">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fa060-1475">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="fa060-1475">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fa060-1476">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fa060-1476">'Blazor'</span></span>
- <span data-ttu-id="fa060-1477">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fa060-1477">'Identity'</span></span>
- <span data-ttu-id="fa060-1478">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fa060-1478">'Let's Encrypt'</span></span>
- <span data-ttu-id="fa060-1479">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fa060-1479">'Razor'</span></span>
- <span data-ttu-id="fa060-1480">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="fa060-1480">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fa060-1481">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="fa060-1481">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fa060-1482">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fa060-1482">'Blazor'</span></span>
- <span data-ttu-id="fa060-1483">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fa060-1483">'Identity'</span></span>
- <span data-ttu-id="fa060-1484">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fa060-1484">'Let's Encrypt'</span></span>
- <span data-ttu-id="fa060-1485">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fa060-1485">'Razor'</span></span>
- <span data-ttu-id="fa060-1486">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="fa060-1486">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fa060-1487">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="fa060-1487">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fa060-1488">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fa060-1488">'Blazor'</span></span>
- <span data-ttu-id="fa060-1489">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fa060-1489">'Identity'</span></span>
- <span data-ttu-id="fa060-1490">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fa060-1490">'Let's Encrypt'</span></span>
- <span data-ttu-id="fa060-1491">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fa060-1491">'Razor'</span></span>
- <span data-ttu-id="fa060-1492">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="fa060-1492">'SignalR' uid:</span></span> 

<span data-ttu-id="fa060-1493">--------------- | :-----: | ---Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="fa060-1493">--------------- | :-----: | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fa060-1494">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fa060-1494">'Blazor'</span></span>
- <span data-ttu-id="fa060-1495">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fa060-1495">'Identity'</span></span>
- <span data-ttu-id="fa060-1496">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fa060-1496">'Let's Encrypt'</span></span>
- <span data-ttu-id="fa060-1497">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fa060-1497">'Razor'</span></span>
- <span data-ttu-id="fa060-1498">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="fa060-1498">'SignalR' uid:</span></span> 

<span data-ttu-id="fa060-1499">---- | | `AutomaticAuthentication`      | `true`  | Pokud `true` Server IIS nastaví `HttpContext.User` ověřený [ověřováním systému Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="fa060-1499">---- | | `AutomaticAuthentication`      | `true`  | If `true`, IIS Server sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="fa060-1500">Pokud `false` Server poskytuje pouze identitu pro `HttpContext.User` a reaguje na výzvy, pokud je explicitně vyžádala `AuthenticationScheme` .</span><span class="sxs-lookup"><span data-stu-id="fa060-1500">If `false`, the server only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="fa060-1501">Aby mohla služba fungovat, musí být ve službě IIS povolená možnost ověřování systému Windows `AutomaticAuthentication` .</span><span class="sxs-lookup"><span data-stu-id="fa060-1501">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="fa060-1502">Další informace najdete v tématu [ověřování systému Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="fa060-1502">For more information, see [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="fa060-1503">| | `AuthenticationDisplayName`    | `null`  | Nastaví zobrazovaný název, který se zobrazí uživatelům na přihlašovacích stránkách.</span><span class="sxs-lookup"><span data-stu-id="fa060-1503">| | `AuthenticationDisplayName`    | `null`  | Sets the display name shown to users on login pages.</span></span> |

<span data-ttu-id="fa060-1504">Pokud chcete nakonfigurovat možnosti služby IIS, zahrňte do nástroje konfiguraci služby pro <xref:Microsoft.AspNetCore.Builder.IISOptions> <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*> .</span><span class="sxs-lookup"><span data-stu-id="fa060-1504">To configure IIS options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>.</span></span> <span data-ttu-id="fa060-1505">Následující příklad zabrání aplikaci v naplnění `HttpContext.Connection.ClientCertificate` :</span><span class="sxs-lookup"><span data-stu-id="fa060-1505">The following example prevents the app from populating `HttpContext.Connection.ClientCertificate`:</span></span>

```csharp
services.Configure<IISOptions>(options => 
{
    options.ForwardClientCertificate = false;
});
```

| <span data-ttu-id="fa060-1506">Možnost</span><span class="sxs-lookup"><span data-stu-id="fa060-1506">Option</span></span>                         | <span data-ttu-id="fa060-1507">Výchozí</span><span class="sxs-lookup"><span data-stu-id="fa060-1507">Default</span></span> | <span data-ttu-id="fa060-1508">Nastavení</span><span class="sxs-lookup"><span data-stu-id="fa060-1508">Setting</span></span> |
| ---
<span data-ttu-id="fa060-1509">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="fa060-1509">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fa060-1510">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fa060-1510">'Blazor'</span></span>
- <span data-ttu-id="fa060-1511">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fa060-1511">'Identity'</span></span>
- <span data-ttu-id="fa060-1512">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fa060-1512">'Let's Encrypt'</span></span>
- <span data-ttu-id="fa060-1513">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fa060-1513">'Razor'</span></span>
- <span data-ttu-id="fa060-1514">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="fa060-1514">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fa060-1515">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="fa060-1515">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fa060-1516">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fa060-1516">'Blazor'</span></span>
- <span data-ttu-id="fa060-1517">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fa060-1517">'Identity'</span></span>
- <span data-ttu-id="fa060-1518">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fa060-1518">'Let's Encrypt'</span></span>
- <span data-ttu-id="fa060-1519">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fa060-1519">'Razor'</span></span>
- <span data-ttu-id="fa060-1520">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="fa060-1520">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fa060-1521">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="fa060-1521">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fa060-1522">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fa060-1522">'Blazor'</span></span>
- <span data-ttu-id="fa060-1523">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fa060-1523">'Identity'</span></span>
- <span data-ttu-id="fa060-1524">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fa060-1524">'Let's Encrypt'</span></span>
- <span data-ttu-id="fa060-1525">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fa060-1525">'Razor'</span></span>
- <span data-ttu-id="fa060-1526">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="fa060-1526">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fa060-1527">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="fa060-1527">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fa060-1528">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fa060-1528">'Blazor'</span></span>
- <span data-ttu-id="fa060-1529">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fa060-1529">'Identity'</span></span>
- <span data-ttu-id="fa060-1530">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fa060-1530">'Let's Encrypt'</span></span>
- <span data-ttu-id="fa060-1531">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fa060-1531">'Razor'</span></span>
- <span data-ttu-id="fa060-1532">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="fa060-1532">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fa060-1533">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="fa060-1533">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fa060-1534">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fa060-1534">'Blazor'</span></span>
- <span data-ttu-id="fa060-1535">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fa060-1535">'Identity'</span></span>
- <span data-ttu-id="fa060-1536">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fa060-1536">'Let's Encrypt'</span></span>
- <span data-ttu-id="fa060-1537">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fa060-1537">'Razor'</span></span>
- <span data-ttu-id="fa060-1538">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="fa060-1538">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fa060-1539">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="fa060-1539">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fa060-1540">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fa060-1540">'Blazor'</span></span>
- <span data-ttu-id="fa060-1541">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fa060-1541">'Identity'</span></span>
- <span data-ttu-id="fa060-1542">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fa060-1542">'Let's Encrypt'</span></span>
- <span data-ttu-id="fa060-1543">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fa060-1543">'Razor'</span></span>
- <span data-ttu-id="fa060-1544">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="fa060-1544">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fa060-1545">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="fa060-1545">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fa060-1546">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fa060-1546">'Blazor'</span></span>
- <span data-ttu-id="fa060-1547">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fa060-1547">'Identity'</span></span>
- <span data-ttu-id="fa060-1548">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fa060-1548">'Let's Encrypt'</span></span>
- <span data-ttu-id="fa060-1549">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fa060-1549">'Razor'</span></span>
- <span data-ttu-id="fa060-1550">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="fa060-1550">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fa060-1551">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="fa060-1551">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fa060-1552">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fa060-1552">'Blazor'</span></span>
- <span data-ttu-id="fa060-1553">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fa060-1553">'Identity'</span></span>
- <span data-ttu-id="fa060-1554">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fa060-1554">'Let's Encrypt'</span></span>
- <span data-ttu-id="fa060-1555">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fa060-1555">'Razor'</span></span>
- <span data-ttu-id="fa060-1556">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="fa060-1556">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fa060-1557">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="fa060-1557">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fa060-1558">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fa060-1558">'Blazor'</span></span>
- <span data-ttu-id="fa060-1559">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fa060-1559">'Identity'</span></span>
- <span data-ttu-id="fa060-1560">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fa060-1560">'Let's Encrypt'</span></span>
- <span data-ttu-id="fa060-1561">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fa060-1561">'Razor'</span></span>
- <span data-ttu-id="fa060-1562">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="fa060-1562">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fa060-1563">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="fa060-1563">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fa060-1564">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fa060-1564">'Blazor'</span></span>
- <span data-ttu-id="fa060-1565">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fa060-1565">'Identity'</span></span>
- <span data-ttu-id="fa060-1566">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fa060-1566">'Let's Encrypt'</span></span>
- <span data-ttu-id="fa060-1567">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fa060-1567">'Razor'</span></span>
- <span data-ttu-id="fa060-1568">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="fa060-1568">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fa060-1569">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="fa060-1569">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fa060-1570">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fa060-1570">'Blazor'</span></span>
- <span data-ttu-id="fa060-1571">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fa060-1571">'Identity'</span></span>
- <span data-ttu-id="fa060-1572">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fa060-1572">'Let's Encrypt'</span></span>
- <span data-ttu-id="fa060-1573">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fa060-1573">'Razor'</span></span>
- <span data-ttu-id="fa060-1574">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="fa060-1574">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fa060-1575">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="fa060-1575">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fa060-1576">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fa060-1576">'Blazor'</span></span>
- <span data-ttu-id="fa060-1577">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fa060-1577">'Identity'</span></span>
- <span data-ttu-id="fa060-1578">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fa060-1578">'Let's Encrypt'</span></span>
- <span data-ttu-id="fa060-1579">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fa060-1579">'Razor'</span></span>
- <span data-ttu-id="fa060-1580">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="fa060-1580">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fa060-1581">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="fa060-1581">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fa060-1582">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fa060-1582">'Blazor'</span></span>
- <span data-ttu-id="fa060-1583">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fa060-1583">'Identity'</span></span>
- <span data-ttu-id="fa060-1584">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fa060-1584">'Let's Encrypt'</span></span>
- <span data-ttu-id="fa060-1585">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fa060-1585">'Razor'</span></span>
- <span data-ttu-id="fa060-1586">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="fa060-1586">'SignalR' uid:</span></span> 

<span data-ttu-id="fa060-1587">--------------- | :-----: | ---Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="fa060-1587">--------------- | :-----: | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fa060-1588">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fa060-1588">'Blazor'</span></span>
- <span data-ttu-id="fa060-1589">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fa060-1589">'Identity'</span></span>
- <span data-ttu-id="fa060-1590">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fa060-1590">'Let's Encrypt'</span></span>
- <span data-ttu-id="fa060-1591">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fa060-1591">'Razor'</span></span>
- <span data-ttu-id="fa060-1592">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="fa060-1592">'SignalR' uid:</span></span> 

<span data-ttu-id="fa060-1593">---- | | `AutomaticAuthentication`      | `true`  | Pokud `true` [Služba IIS Integration middleware](#enable-the-iisintegration-components) nastaví ověření `HttpContext.User` [ověřováním systému Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="fa060-1593">---- | | `AutomaticAuthentication`      | `true`  | If `true`, [IIS Integration Middleware](#enable-the-iisintegration-components) sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="fa060-1594">`false`V případě, middleware poskytuje pouze identitu pro `HttpContext.User` a reaguje na výzvy, pokud je explicitně vyžádala `AuthenticationScheme` .</span><span class="sxs-lookup"><span data-stu-id="fa060-1594">If `false`, the middleware only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="fa060-1595">Aby mohla služba fungovat, musí být ve službě IIS povolená možnost ověřování systému Windows `AutomaticAuthentication` .</span><span class="sxs-lookup"><span data-stu-id="fa060-1595">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="fa060-1596">Další informace najdete v tématu [ověřování systému Windows](xref:security/authentication/windowsauth) .</span><span class="sxs-lookup"><span data-stu-id="fa060-1596">For more information, see the [Windows Authentication](xref:security/authentication/windowsauth) topic.</span></span> <span data-ttu-id="fa060-1597">| | `AuthenticationDisplayName`    | `null`  | Nastaví zobrazovaný název, který se zobrazí uživatelům na přihlašovacích stránkách.</span><span class="sxs-lookup"><span data-stu-id="fa060-1597">| | `AuthenticationDisplayName`    | `null`  | Sets the display name shown to users on login pages.</span></span> <span data-ttu-id="fa060-1598">| | `ForwardClientCertificate`     | `true`  | Pokud `true` je k `MS-ASPNETCORE-CLIENTCERT` dispozici hlavička žádosti, `HttpContext.Connection.ClientCertificate` je naplněna hodnota.</span><span class="sxs-lookup"><span data-stu-id="fa060-1598">| | `ForwardClientCertificate`     | `true`  | If `true` and the `MS-ASPNETCORE-CLIENTCERT` request header is present, the `HttpContext.Connection.ClientCertificate` is populated.</span></span> |

### <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="fa060-1599">Scénáře proxy serveru a nástroje pro vyrovnávání zatížení</span><span class="sxs-lookup"><span data-stu-id="fa060-1599">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="fa060-1600">[Služba IIS Integration middleware](#enable-the-iisintegration-components), která konfiguruje middleware předávaných hlaviček, a modul ASP.NET Core je nakonfigurován tak, aby přenesl schéma (http/https) a vzdálenou IP adresu, kam pochází požadavek.</span><span class="sxs-lookup"><span data-stu-id="fa060-1600">The [IIS Integration Middleware](#enable-the-iisintegration-components), which configures Forwarded Headers Middleware, and the ASP.NET Core Module are configured to forward the scheme (HTTP/HTTPS) and the remote IP address where the request originated.</span></span> <span data-ttu-id="fa060-1601">Pro aplikace hostované za dalšími proxy servery a nástroji pro vyrovnávání zatížení může být vyžadována další konfigurace.</span><span class="sxs-lookup"><span data-stu-id="fa060-1601">Additional configuration might be required for apps hosted behind additional proxy servers and load balancers.</span></span> <span data-ttu-id="fa060-1602">Další informace najdete v tématu [konfigurace ASP.NET Core pro práci se servery proxy a nástroji pro vyrovnávání zatížení](xref:host-and-deploy/proxy-load-balancer).</span><span class="sxs-lookup"><span data-stu-id="fa060-1602">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

### <a name="webconfig-file"></a><span data-ttu-id="fa060-1603">soubor Web. config</span><span class="sxs-lookup"><span data-stu-id="fa060-1603">web.config file</span></span>

<span data-ttu-id="fa060-1604">Soubor *Web. config* konfiguruje [modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="fa060-1604">The *web.config* file configures the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="fa060-1605">Vytváření, transformace a publikování souboru *Web. config* je zpracováno nástrojem MSBuild cíl ( `_TransformWebConfig` ) při publikování projektu.</span><span class="sxs-lookup"><span data-stu-id="fa060-1605">Creating, transforming, and publishing the *web.config* file is handled by an MSBuild target (`_TransformWebConfig`) when the project is published.</span></span> <span data-ttu-id="fa060-1606">Tento cíl je k dispozici v cíle webové sady SDK ( `Microsoft.NET.Sdk.Web` ).</span><span class="sxs-lookup"><span data-stu-id="fa060-1606">This target is present in the Web SDK targets (`Microsoft.NET.Sdk.Web`).</span></span> <span data-ttu-id="fa060-1607">Sada SDK je nastavena v horní části souboru projektu:</span><span class="sxs-lookup"><span data-stu-id="fa060-1607">The SDK is set at the top of the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="fa060-1608">Pokud soubor *Web. config* není v projektu přítomen, je vytvořen soubor se správným *processPath* a *argumenty* pro konfiguraci modulu ASP.NET Core a přesunutí do [publikovaného výstupu](xref:host-and-deploy/directory-structure).</span><span class="sxs-lookup"><span data-stu-id="fa060-1608">If a *web.config* file isn't present in the project, the file is created with the correct *processPath* and *arguments* to configure the ASP.NET Core Module and moved to [published output](xref:host-and-deploy/directory-structure).</span></span>

<span data-ttu-id="fa060-1609">Pokud je soubor *Web. config* přítomen v projektu, soubor je transformován se správným *processPath* a *argumenty* pro konfiguraci modulu ASP.NET Core a přesunutí do publikovaného výstupu.</span><span class="sxs-lookup"><span data-stu-id="fa060-1609">If a *web.config* file is present in the project, the file is transformed with the correct *processPath* and *arguments* to configure the ASP.NET Core Module and moved to published output.</span></span> <span data-ttu-id="fa060-1610">Transformace nemění nastavení konfigurace služby IIS v souboru.</span><span class="sxs-lookup"><span data-stu-id="fa060-1610">The transformation doesn't modify IIS configuration settings in the file.</span></span>

<span data-ttu-id="fa060-1611">Soubor *Web. config* může poskytovat další nastavení konfigurace služby IIS, která ovládají aktivní moduly IIS.</span><span class="sxs-lookup"><span data-stu-id="fa060-1611">The *web.config* file may provide additional IIS configuration settings that control active IIS modules.</span></span> <span data-ttu-id="fa060-1612">Informace o modulech služby IIS, které jsou schopné zpracovávat požadavky s ASP.NET Core aplikacemi, najdete v tématu [IIS modules](xref:host-and-deploy/iis/modules) .</span><span class="sxs-lookup"><span data-stu-id="fa060-1612">For information on IIS modules that are capable of processing requests with ASP.NET Core apps, see the [IIS modules](xref:host-and-deploy/iis/modules) topic.</span></span>

<span data-ttu-id="fa060-1613">Chcete-li webové sadě SDK zabránit ve transformaci souboru *Web. config* , použijte **\<IsTransformWebConfigDisabled>** vlastnost v souboru projektu:</span><span class="sxs-lookup"><span data-stu-id="fa060-1613">To prevent the Web SDK from transforming the *web.config* file, use the **\<IsTransformWebConfigDisabled>** property in the project file:</span></span>

```xml
<PropertyGroup>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

<span data-ttu-id="fa060-1614">Při zakazování webové sady SDK z transformace souboru by měl vývojář *processPath* a *argumenty* ručně nastavit.</span><span class="sxs-lookup"><span data-stu-id="fa060-1614">When disabling the Web SDK from transforming the file, the *processPath* and *arguments* should be manually set by the developer.</span></span> <span data-ttu-id="fa060-1615">Další informace naleznete v tématu <xref:host-and-deploy/aspnet-core-module>.</span><span class="sxs-lookup"><span data-stu-id="fa060-1615">For more information, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

### <a name="webconfig-file-location"></a><span data-ttu-id="fa060-1616">umístění souboru Web. config</span><span class="sxs-lookup"><span data-stu-id="fa060-1616">web.config file location</span></span>

<span data-ttu-id="fa060-1617">Aby bylo možné správně nastavit [modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module) , musí být soubor *Web. config* přítomen v [kořenové cestě obsahu](xref:fundamentals/index#content-root) (obvykle v základní cestě aplikace) nasazené aplikace.</span><span class="sxs-lookup"><span data-stu-id="fa060-1617">In order to set up the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) correctly, the *web.config* file must be present at the [content root](xref:fundamentals/index#content-root) path (typically the app base path) of the deployed app.</span></span> <span data-ttu-id="fa060-1618">Toto je stejné umístění jako fyzická cesta k webu poskytované službě IIS.</span><span class="sxs-lookup"><span data-stu-id="fa060-1618">This is the same location as the website physical path provided to IIS.</span></span> <span data-ttu-id="fa060-1619">Soubor *Web. config* je vyžadován v kořenu aplikace, aby bylo možné publikovat více aplikací pomocí nasazení webu.</span><span class="sxs-lookup"><span data-stu-id="fa060-1619">The *web.config* file is required at the root of the app to enable the publishing of multiple apps using Web Deploy.</span></span>

<span data-ttu-id="fa060-1620">Citlivé soubory existují na fyzické cestě aplikace, jako je například \* \<assembly> . runtimeconfig. JSON*, \* \<assembly> . XML* (dokumentační komentáře XML) a \* \<assembly> . DEPS. JSON\*.</span><span class="sxs-lookup"><span data-stu-id="fa060-1620">Sensitive files exist on the app's physical path, such as *\<assembly>.runtimeconfig.json*, *\<assembly>.xml* (XML Documentation comments), and *\<assembly>.deps.json*.</span></span> <span data-ttu-id="fa060-1621">Když je přítomen soubor *Web. config* a lokalita se spouští normálně, služba IIS tyto citlivé soubory po vyžádání neobsluhuje.</span><span class="sxs-lookup"><span data-stu-id="fa060-1621">When the *web.config* file is present and the site starts normally, IIS doesn't serve these sensitive files if they're requested.</span></span> <span data-ttu-id="fa060-1622">Pokud soubor *Web. config* chybí, je nesprávně pojmenovaný nebo nemůže nakonfigurovat lokalitu pro normální spuštění, služba IIS může obsluhovat citlivé soubory veřejně.</span><span class="sxs-lookup"><span data-stu-id="fa060-1622">If the *web.config* file is missing, incorrectly named, or unable to configure the site for normal startup, IIS may serve sensitive files publicly.</span></span>

<span data-ttu-id="fa060-1623">**Soubor *Web. config* musí být současně přítomen v nasazení, správně pojmenován a může nakonfigurovat lokalitu pro normální spuštění. Nikdy neodstraňujte soubor *Web. config* z produkčního nasazení.**</span><span class="sxs-lookup"><span data-stu-id="fa060-1623">**The *web.config* file must be present in the deployment at all times, correctly named, and able to configure the site for normal start up. Never remove the *web.config* file from a production deployment.**</span></span>

### <a name="transform-webconfig"></a><span data-ttu-id="fa060-1624">Transformace souboru web.config</span><span class="sxs-lookup"><span data-stu-id="fa060-1624">Transform web.config</span></span>

<span data-ttu-id="fa060-1625">Pokud potřebujete transformovat *Web. config* při publikování (například nastavit proměnné prostředí na základě konfigurace, profilu nebo prostředí), přečtěte si téma <xref:host-and-deploy/iis/transform-webconfig> .</span><span class="sxs-lookup"><span data-stu-id="fa060-1625">If you need to transform *web.config* on publish (for example, set environment variables based on the configuration, profile, or environment), see <xref:host-and-deploy/iis/transform-webconfig>.</span></span>

## <a name="iis-configuration"></a><span data-ttu-id="fa060-1626">Konfigurace služby IIS</span><span class="sxs-lookup"><span data-stu-id="fa060-1626">IIS configuration</span></span>

<span data-ttu-id="fa060-1627">**Operační systémy Windows Server**</span><span class="sxs-lookup"><span data-stu-id="fa060-1627">**Windows Server operating systems**</span></span>

<span data-ttu-id="fa060-1628">Povolte roli serveru **webový server (IIS)** a vytvořte služby rolí.</span><span class="sxs-lookup"><span data-stu-id="fa060-1628">Enable the **Web Server (IIS)** server role and establish role services.</span></span>

1. <span data-ttu-id="fa060-1629">Použijte průvodce **přidáním rolí a funkcí** z nabídky **Správa** nebo odkazu v **Správce serveru**.</span><span class="sxs-lookup"><span data-stu-id="fa060-1629">Use the **Add Roles and Features** wizard from the **Manage** menu or the link in **Server Manager**.</span></span> <span data-ttu-id="fa060-1630">V kroku **role serveru** zaškrtněte políčko **webový server (IIS)**.</span><span class="sxs-lookup"><span data-stu-id="fa060-1630">On the **Server Roles** step, check the box for **Web Server (IIS)**.</span></span>

   ![V kroku vybrat role serveru je vybraná role Webový server IIS.](index/_static/server-roles-ws2016.png)

1. <span data-ttu-id="fa060-1632">Po kroku **funkce** se krok **služby rolí** načte pro webový server (IIS).</span><span class="sxs-lookup"><span data-stu-id="fa060-1632">After the **Features** step, the **Role services** step loads for Web Server (IIS).</span></span> <span data-ttu-id="fa060-1633">Vyberte požadované služby role IIS nebo přijměte výchozí poskytnuté služby rolí.</span><span class="sxs-lookup"><span data-stu-id="fa060-1633">Select the IIS role services desired or accept the default role services provided.</span></span>

   ![Výchozí služby rolí se vyberou v kroku vybrat služby rolí.](index/_static/role-services-ws2016.png)

   <span data-ttu-id="fa060-1635">**Ověřování systému Windows (volitelné)**</span><span class="sxs-lookup"><span data-stu-id="fa060-1635">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="fa060-1636">Chcete-li povolit ověřování systému Windows, rozbalte následující uzly: zabezpečení **webového serveru**  >  **Security**.</span><span class="sxs-lookup"><span data-stu-id="fa060-1636">To enable Windows Authentication, expand the following nodes: **Web Server** > **Security**.</span></span> <span data-ttu-id="fa060-1637">Vyberte funkci **ověřování systému Windows** .</span><span class="sxs-lookup"><span data-stu-id="fa060-1637">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="fa060-1638">Další informace najdete v tématu [ověřování \<windowsAuthentication> systému Windows](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) a [Konfigurace ověřování systému Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="fa060-1638">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="fa060-1639">**WebSockets (volitelné)**</span><span class="sxs-lookup"><span data-stu-id="fa060-1639">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="fa060-1640">Objekty WebSocket jsou podporované ASP.NET Core 1,1 nebo novějším.</span><span class="sxs-lookup"><span data-stu-id="fa060-1640">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="fa060-1641">Chcete-li povolit objekty WebSockets, rozbalte následující **Web Server**uzly:  >  **vývoj aplikací**webového serveru.</span><span class="sxs-lookup"><span data-stu-id="fa060-1641">To enable WebSockets, expand the following nodes: **Web Server** > **Application Development**.</span></span> <span data-ttu-id="fa060-1642">Vyberte funkci **protokolu WebSocket** .</span><span class="sxs-lookup"><span data-stu-id="fa060-1642">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="fa060-1643">Další informace najdete v tématu [objekty WebSockets](xref:fundamentals/websockets).</span><span class="sxs-lookup"><span data-stu-id="fa060-1643">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="fa060-1644">Chcete-li nainstalovat roli a služby webového serveru, postupujte podle kroků pro **potvrzení** .</span><span class="sxs-lookup"><span data-stu-id="fa060-1644">Proceed through the **Confirmation** step to install the web server role and services.</span></span> <span data-ttu-id="fa060-1645">Po instalaci role **webový server (IIS)** není nutné restartovat server nebo službu IIS.</span><span class="sxs-lookup"><span data-stu-id="fa060-1645">A server/IIS restart isn't required after installing the **Web Server (IIS)** role.</span></span>

<span data-ttu-id="fa060-1646">**Desktopové operační systémy Windows**</span><span class="sxs-lookup"><span data-stu-id="fa060-1646">**Windows desktop operating systems**</span></span>

<span data-ttu-id="fa060-1647">Povolte **konzolu pro správu služby IIS** a **webové služby**.</span><span class="sxs-lookup"><span data-stu-id="fa060-1647">Enable the **IIS Management Console** and **World Wide Web Services**.</span></span>

1. <span data-ttu-id="fa060-1648">Přejděte na **Ovládací panely** > **programy** programy > **a funkce** > **zapnout nebo vypnout funkce systému Windows** (levá strana obrazovky).</span><span class="sxs-lookup"><span data-stu-id="fa060-1648">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>

1. <span data-ttu-id="fa060-1649">Otevřete uzel **Internetová informační služba** .</span><span class="sxs-lookup"><span data-stu-id="fa060-1649">Open the **Internet Information Services** node.</span></span> <span data-ttu-id="fa060-1650">Otevřete uzel **Nástroje webové správy** .</span><span class="sxs-lookup"><span data-stu-id="fa060-1650">Open the **Web Management Tools** node.</span></span>

1. <span data-ttu-id="fa060-1651">Zaškrtněte políčko pro **konzolu pro správu služby IIS**.</span><span class="sxs-lookup"><span data-stu-id="fa060-1651">Check the box for **IIS Management Console**.</span></span>

1. <span data-ttu-id="fa060-1652">Zaškrtněte políčko u **webových služeb**.</span><span class="sxs-lookup"><span data-stu-id="fa060-1652">Check the box for **World Wide Web Services**.</span></span>

1. <span data-ttu-id="fa060-1653">Přijměte výchozí funkce pro **webové služby** nebo upravte funkce služby IIS.</span><span class="sxs-lookup"><span data-stu-id="fa060-1653">Accept the default features for **World Wide Web Services** or customize the IIS features.</span></span>

   <span data-ttu-id="fa060-1654">**Ověřování systému Windows (volitelné)**</span><span class="sxs-lookup"><span data-stu-id="fa060-1654">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="fa060-1655">Chcete-li povolit ověřování systému Windows, rozbalte následující uzly: zabezpečení **webové služby**  >  **Security**.</span><span class="sxs-lookup"><span data-stu-id="fa060-1655">To enable Windows Authentication, expand the following nodes: **World Wide Web Services** > **Security**.</span></span> <span data-ttu-id="fa060-1656">Vyberte funkci **ověřování systému Windows** .</span><span class="sxs-lookup"><span data-stu-id="fa060-1656">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="fa060-1657">Další informace najdete v tématu [ověřování \<windowsAuthentication> systému Windows](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) a [Konfigurace ověřování systému Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="fa060-1657">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="fa060-1658">**WebSockets (volitelné)**</span><span class="sxs-lookup"><span data-stu-id="fa060-1658">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="fa060-1659">Objekty WebSocket jsou podporované ASP.NET Core 1,1 nebo novějším.</span><span class="sxs-lookup"><span data-stu-id="fa060-1659">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="fa060-1660">Chcete-li povolit objekty WebSockets, rozbalte následující uzly: funkce pro vývoj aplikací v **rámci webové služby**  >  **Application Development Features**.</span><span class="sxs-lookup"><span data-stu-id="fa060-1660">To enable WebSockets, expand the following nodes: **World Wide Web Services** > **Application Development Features**.</span></span> <span data-ttu-id="fa060-1661">Vyberte funkci **protokolu WebSocket** .</span><span class="sxs-lookup"><span data-stu-id="fa060-1661">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="fa060-1662">Další informace najdete v tématu [objekty WebSockets](xref:fundamentals/websockets).</span><span class="sxs-lookup"><span data-stu-id="fa060-1662">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="fa060-1663">Pokud instalace služby IIS vyžaduje restart, restartujte systém.</span><span class="sxs-lookup"><span data-stu-id="fa060-1663">If the IIS installation requires a restart, restart the system.</span></span>

![V rámci funkcí systému Windows jsou vybrána Konzola pro správu služby IIS a webové služby.](index/_static/windows-features-win10.png)

## <a name="install-the-net-core-hosting-bundle"></a><span data-ttu-id="fa060-1665">Instalace hostující sady .NET Core</span><span class="sxs-lookup"><span data-stu-id="fa060-1665">Install the .NET Core Hosting Bundle</span></span>

<span data-ttu-id="fa060-1666">Nainstalujte *hostující sadu .NET Core* do hostitelského systému.</span><span class="sxs-lookup"><span data-stu-id="fa060-1666">Install the *.NET Core Hosting Bundle* on the hosting system.</span></span> <span data-ttu-id="fa060-1667">Svazek nainstaluje modul runtime .NET Core, knihovnu .NET Core a [modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="fa060-1667">The bundle installs the .NET Core Runtime, .NET Core Library, and the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="fa060-1668">Modul umožňuje, aby aplikace ASP.NET Core běžely za službou IIS.</span><span class="sxs-lookup"><span data-stu-id="fa060-1668">The module allows ASP.NET Core apps to run behind IIS.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="fa060-1669">Pokud je hostující sada nainstalována před službou IIS, je nutné opravit instalaci sady prostředků.</span><span class="sxs-lookup"><span data-stu-id="fa060-1669">If the Hosting Bundle is installed before IIS, the bundle installation must be repaired.</span></span> <span data-ttu-id="fa060-1670">Spusťte znovu instalační program hostující sady po instalaci služby IIS.</span><span class="sxs-lookup"><span data-stu-id="fa060-1670">Run the Hosting Bundle installer again after installing IIS.</span></span>
>
> <span data-ttu-id="fa060-1671">Pokud se hostující sada nainstaluje po instalaci 64 (x64) verze .NET Core, můžou se zdát, že sady SDK chybí ([nezjistily se žádné sady .NET Core SDK](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span><span class="sxs-lookup"><span data-stu-id="fa060-1671">If the Hosting Bundle is installed after installing the 64-bit (x64) version of .NET Core, SDKs might appear to be missing ([No .NET Core SDKs were detected](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span></span> <span data-ttu-id="fa060-1672">Chcete-li tento problém vyřešit, přečtěte si téma <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle> .</span><span class="sxs-lookup"><span data-stu-id="fa060-1672">To resolve the problem, see <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>.</span></span>

### <a name="download"></a><span data-ttu-id="fa060-1673">Stáhnout</span><span class="sxs-lookup"><span data-stu-id="fa060-1673">Download</span></span>

1. <span data-ttu-id="fa060-1674">Přejděte na stránku [stáhnout jádro .NET Core](https://dotnet.microsoft.com/download/dotnet-core) .</span><span class="sxs-lookup"><span data-stu-id="fa060-1674">Navigate to the [Download .NET Core](https://dotnet.microsoft.com/download/dotnet-core) page.</span></span>
1. <span data-ttu-id="fa060-1675">Vyberte požadovanou verzi .NET Core.</span><span class="sxs-lookup"><span data-stu-id="fa060-1675">Select the desired .NET Core version.</span></span>
1. <span data-ttu-id="fa060-1676">Ve sloupci **Spustit aplikace – modul runtime** vyhledejte řádek požadované verze modulu runtime .NET Core.</span><span class="sxs-lookup"><span data-stu-id="fa060-1676">In the **Run apps - Runtime** column, find the row of the .NET Core runtime version desired.</span></span>
1. <span data-ttu-id="fa060-1677">Stáhněte instalační program pomocí odkazu **hostující sada** .</span><span class="sxs-lookup"><span data-stu-id="fa060-1677">Download the installer using the **Hosting Bundle** link.</span></span>

> [!WARNING]
> <span data-ttu-id="fa060-1678">Některé instalační programy obsahují verze vydaných verzí, které dosáhly svého konce životnosti (konce řádku) a které už nejsou podporovány společností Microsoft.</span><span class="sxs-lookup"><span data-stu-id="fa060-1678">Some installers contain release versions that have reached their end of life (EOL) and are no longer supported by Microsoft.</span></span> <span data-ttu-id="fa060-1679">Další informace najdete v tématu [zásady podpory](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span><span class="sxs-lookup"><span data-stu-id="fa060-1679">For more information, see the [support policy](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span></span>

### <a name="install-the-hosting-bundle"></a><span data-ttu-id="fa060-1680">Instalace hostitelského balíčku</span><span class="sxs-lookup"><span data-stu-id="fa060-1680">Install the Hosting Bundle</span></span>

1. <span data-ttu-id="fa060-1681">Spusťte instalační program na serveru.</span><span class="sxs-lookup"><span data-stu-id="fa060-1681">Run the installer on the server.</span></span> <span data-ttu-id="fa060-1682">Při spuštění instalačního programu z příkazového prostředí správce jsou k dispozici následující parametry:</span><span class="sxs-lookup"><span data-stu-id="fa060-1682">The following parameters are available when running the installer from an administrator command shell:</span></span>

   * <span data-ttu-id="fa060-1683">`OPT_NO_ANCM=1`: Přeskočit instalaci modulu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="fa060-1683">`OPT_NO_ANCM=1`: Skip installing the ASP.NET Core Module.</span></span>
   * <span data-ttu-id="fa060-1684">`OPT_NO_RUNTIME=1`: Přeskočí instalace modulu runtime .NET Core.</span><span class="sxs-lookup"><span data-stu-id="fa060-1684">`OPT_NO_RUNTIME=1`: Skip installing the .NET Core runtime.</span></span> <span data-ttu-id="fa060-1685">Používá se, když Server hostuje jenom [samostatná nasazení (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span><span class="sxs-lookup"><span data-stu-id="fa060-1685">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="fa060-1686">`OPT_NO_SHAREDFX=1`: Přeskočí instalace sdíleného rozhraní ASP.NET (ASP.NET Runtime).</span><span class="sxs-lookup"><span data-stu-id="fa060-1686">`OPT_NO_SHAREDFX=1`: Skip installing the ASP.NET Shared Framework (ASP.NET runtime).</span></span> <span data-ttu-id="fa060-1687">Používá se, když Server hostuje jenom [samostatná nasazení (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span><span class="sxs-lookup"><span data-stu-id="fa060-1687">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="fa060-1688">`OPT_NO_X86=1`: Přeskočí instalace běhových prostředí x86.</span><span class="sxs-lookup"><span data-stu-id="fa060-1688">`OPT_NO_X86=1`: Skip installing x86 runtimes.</span></span> <span data-ttu-id="fa060-1689">Tento parametr použijte, pokud víte, že nebudete hostovat 32 aplikace.</span><span class="sxs-lookup"><span data-stu-id="fa060-1689">Use this parameter when you know that you won't be hosting 32-bit apps.</span></span> <span data-ttu-id="fa060-1690">Pokud existuje možnost, že v budoucnosti budete hostovat jak 32, tak i 64 aplikace, tento parametr nepoužívejte a nainstalujete oba moduly runtime.</span><span class="sxs-lookup"><span data-stu-id="fa060-1690">If there's any chance that you will host both 32-bit and 64-bit apps in the future, don't use this parameter and install both runtimes.</span></span>
   * <span data-ttu-id="fa060-1691">`OPT_NO_SHARED_CONFIG_CHECK=1`: Pokud je sdílená konfigurace (*ApplicationHost. config*) ve stejném počítači jako instalace služby IIS, zakažte kontrolu použití sdílené konfigurace služby IIS.</span><span class="sxs-lookup"><span data-stu-id="fa060-1691">`OPT_NO_SHARED_CONFIG_CHECK=1`: Disable the check for using an IIS Shared Configuration when the shared configuration (*applicationHost.config*) is on the same machine as the IIS installation.</span></span> <span data-ttu-id="fa060-1692">*K dispozici jenom pro ASP.NET Core 2,2 nebo novější instalační programy hostujících prostředků.*</span><span class="sxs-lookup"><span data-stu-id="fa060-1692">*Only available for ASP.NET Core 2.2 or later Hosting Bundler installers.*</span></span> <span data-ttu-id="fa060-1693">Další informace naleznete v tématu <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.</span><span class="sxs-lookup"><span data-stu-id="fa060-1693">For more information, see <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.</span></span>
1. <span data-ttu-id="fa060-1694">Restartujte systém nebo v příkazovém prostředí spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="fa060-1694">Restart the system or execute the following commands in a command shell:</span></span>

   ```console
   net stop was /y
   net start w3svc
   ```
   <span data-ttu-id="fa060-1695">Restartování služby IIS zabere změny v systémové cestě, což je proměnná prostředí vytvořená instalačním programem.</span><span class="sxs-lookup"><span data-stu-id="fa060-1695">Restarting IIS picks up a change to the system PATH, which is an environment variable, made by the installer.</span></span>

<span data-ttu-id="fa060-1696">Při instalaci hostujícího balíčku není nutné ručně zastavit jednotlivé weby ve službě IIS.</span><span class="sxs-lookup"><span data-stu-id="fa060-1696">It isn't necessary to manually stop individual sites in IIS when installing the Hosting Bundle.</span></span> <span data-ttu-id="fa060-1697">Hostované aplikace (weby IIS) se restartují po restartování služby IIS.</span><span class="sxs-lookup"><span data-stu-id="fa060-1697">Hosted apps (IIS sites) restart when IIS restarts.</span></span> <span data-ttu-id="fa060-1698">Aplikace se po přijetí první žádosti spustí znovu, včetně [modulu inicializace aplikace](#application-initialization-module-and-idle-timeout).</span><span class="sxs-lookup"><span data-stu-id="fa060-1698">Apps start up again when they receive their first request, including from the [Application Initialization Module](#application-initialization-module-and-idle-timeout).</span></span>

<span data-ttu-id="fa060-1699">ASP.NET Core přijímá postup při přeposílání pro vydání oprav pro balíčky sdílených balíčků rozhraní.</span><span class="sxs-lookup"><span data-stu-id="fa060-1699">ASP.NET Core adopts roll-forward behavior for patch releases of shared framework packages.</span></span> <span data-ttu-id="fa060-1700">Když se aplikace hostované službou IIS restartují se službou IIS, aplikace při přijetí jejich prvního požadavku načtou nejnovější verze oprav jejich odkazovaných balíčků.</span><span class="sxs-lookup"><span data-stu-id="fa060-1700">When apps hosted by IIS restart with IIS, the apps load with the latest patch releases of their referenced packages when they receive their first request.</span></span> <span data-ttu-id="fa060-1701">Pokud se služba IIS nerestartuje, aplikace se restartují a projeví se chování při přeposílání, když se jejich pracovní procesy recyklují a obdrží první požadavek.</span><span class="sxs-lookup"><span data-stu-id="fa060-1701">If IIS isn't restarted, apps restart and exhibit roll-forward behavior when their worker processes are recycled and they receive their first request.</span></span>

> [!NOTE]
> <span data-ttu-id="fa060-1702">Informace o sdílené konfiguraci služby IIS najdete v tématu [modul ASP.NET Core se sdílenou konfigurací služby IIS](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).</span><span class="sxs-lookup"><span data-stu-id="fa060-1702">For information on IIS Shared Configuration, see [ASP.NET Core Module with IIS Shared Configuration](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).</span></span>

## <a name="install-web-deploy-when-publishing-with-visual-studio"></a><span data-ttu-id="fa060-1703">Při publikování v aplikaci Visual Studio nainstalovat Nasazení webu</span><span class="sxs-lookup"><span data-stu-id="fa060-1703">Install Web Deploy when publishing with Visual Studio</span></span>

<span data-ttu-id="fa060-1704">Při nasazování aplikací na servery s [nasazení webu](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later)nainstalujte na server nejnovější verzi nasazení webu.</span><span class="sxs-lookup"><span data-stu-id="fa060-1704">When deploying apps to servers with [Web Deploy](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later), install the latest version of Web Deploy on the server.</span></span> <span data-ttu-id="fa060-1705">Chcete-li nainstalovat Nasazení webu, použijte [instalační program webové platformy (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) nebo si instalační program Získejte přímo z webu [služby Stažení softwaru](https://www.microsoft.com/download/details.aspx?id=43717).</span><span class="sxs-lookup"><span data-stu-id="fa060-1705">To install Web Deploy, use the [Web Platform Installer (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) or obtain an installer directly from the [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=43717).</span></span> <span data-ttu-id="fa060-1706">Upřednostňovanou metodou je použití WebPI.</span><span class="sxs-lookup"><span data-stu-id="fa060-1706">The preferred method is to use WebPI.</span></span> <span data-ttu-id="fa060-1707">WebPI nabízí samostatnou instalaci a konfiguraci pro poskytovatele hostingu.</span><span class="sxs-lookup"><span data-stu-id="fa060-1707">WebPI offers a standalone setup and a configuration for hosting providers.</span></span>

## <a name="create-the-iis-site"></a><span data-ttu-id="fa060-1708">Vytvoření webu služby IIS</span><span class="sxs-lookup"><span data-stu-id="fa060-1708">Create the IIS site</span></span>

1. <span data-ttu-id="fa060-1709">V hostitelském systému vytvořte složku, která bude obsahovat publikované složky a soubory aplikace.</span><span class="sxs-lookup"><span data-stu-id="fa060-1709">On the hosting system, create a folder to contain the app's published folders and files.</span></span> <span data-ttu-id="fa060-1710">V následujícím kroku je jako fyzická cesta k aplikaci služba IIS poskytována jako cesta k této složce.</span><span class="sxs-lookup"><span data-stu-id="fa060-1710">In a following step, the folder's path is provided to IIS as the physical path to the app.</span></span> <span data-ttu-id="fa060-1711">Další informace o složce nasazení aplikace a rozložení souborů naleznete v tématu <xref:host-and-deploy/directory-structure> .</span><span class="sxs-lookup"><span data-stu-id="fa060-1711">For more information on an app's deployment folder and file layout, see <xref:host-and-deploy/directory-structure>.</span></span>

1. <span data-ttu-id="fa060-1712">Ve Správci služby IIS otevřete uzel serveru na panelu **připojení** .</span><span class="sxs-lookup"><span data-stu-id="fa060-1712">In IIS Manager, open the server's node in the **Connections** panel.</span></span> <span data-ttu-id="fa060-1713">Klikněte pravým tlačítkem na složku **weby** .</span><span class="sxs-lookup"><span data-stu-id="fa060-1713">Right-click the **Sites** folder.</span></span> <span data-ttu-id="fa060-1714">V místní nabídce vyberte **Přidat web** .</span><span class="sxs-lookup"><span data-stu-id="fa060-1714">Select **Add Website** from the contextual menu.</span></span>

1. <span data-ttu-id="fa060-1715">Zadejte **název lokality** a nastavte **fyzickou cestu** ke složce pro nasazení aplikace.</span><span class="sxs-lookup"><span data-stu-id="fa060-1715">Provide a **Site name** and set the **Physical path** to the app's deployment folder.</span></span> <span data-ttu-id="fa060-1716">Zadejte konfiguraci **vazby** a vytvořte web výběrem **OK**:</span><span class="sxs-lookup"><span data-stu-id="fa060-1716">Provide the **Binding** configuration and create the website by selecting **OK**:</span></span>

   ![Zadejte název lokality, fyzickou cestu a název hostitele v kroku přidat web.](index/_static/add-website-ws2016.png)

   > [!WARNING]
   > <span data-ttu-id="fa060-1718">Nelze použít vazby zástupných znaků na nejvyšší úrovni ( `http://*:80/` a `http://+:80` ). **not**</span><span class="sxs-lookup"><span data-stu-id="fa060-1718">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="fa060-1719">Vazby zástupných znaků nejvyšší úrovně můžou aplikaci otevřít pro slabá místa zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="fa060-1719">Top-level wildcard bindings can open up your app to security vulnerabilities.</span></span> <span data-ttu-id="fa060-1720">To platí pro silné i slabé zástupné znaky.</span><span class="sxs-lookup"><span data-stu-id="fa060-1720">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="fa060-1721">Místo zástupných znaků použijte explicitní názvy hostitelů.</span><span class="sxs-lookup"><span data-stu-id="fa060-1721">Use explicit host names rather than wildcards.</span></span> <span data-ttu-id="fa060-1722">Vazba zástupných znaků subdomény (například `*.mysub.com` ) nemá toto bezpečnostní riziko, pokud ovládáte celou nadřazenou doménu (na rozdíl od `*.com` , která je zranitelná).</span><span class="sxs-lookup"><span data-stu-id="fa060-1722">Subdomain wildcard binding (for example, `*.mysub.com`) doesn't have this security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="fa060-1723">Další informace najdete v [části rfc7230 část-5,4](https://tools.ietf.org/html/rfc7230#section-5.4) .</span><span class="sxs-lookup"><span data-stu-id="fa060-1723">See [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) for more information.</span></span>

1. <span data-ttu-id="fa060-1724">V uzlu serveru vyberte **fondy aplikací**.</span><span class="sxs-lookup"><span data-stu-id="fa060-1724">Under the server's node, select **Application Pools**.</span></span>

1. <span data-ttu-id="fa060-1725">Klikněte pravým tlačítkem myši na fond aplikací webu a v místní nabídce vyberte **základní nastavení** .</span><span class="sxs-lookup"><span data-stu-id="fa060-1725">Right-click the site's app pool and select **Basic Settings** from the contextual menu.</span></span>

1. <span data-ttu-id="fa060-1726">V okně **Upravit fond aplikací** nastavte **verzi .NET CLR** na **bez spravovaného kódu**:</span><span class="sxs-lookup"><span data-stu-id="fa060-1726">In the **Edit Application Pool** window, set the **.NET CLR version** to **No Managed Code**:</span></span>

   ![Pro verzi .NET CLR nenastavte žádný spravovaný kód.](index/_static/edit-apppool-ws2016.png)

    <span data-ttu-id="fa060-1728">ASP.NET Core běží v samostatném procesu a spravuje modul runtime.</span><span class="sxs-lookup"><span data-stu-id="fa060-1728">ASP.NET Core runs in a separate process and manages the runtime.</span></span> <span data-ttu-id="fa060-1729">ASP.NET Core nespoléhá na načítání CLR desktopových aplikací (.NET CLR) &mdash; , modul CLR (Common Language Runtime) pro .NET Core se spouští k hostování aplikace v pracovním procesu.</span><span class="sxs-lookup"><span data-stu-id="fa060-1729">ASP.NET Core doesn't rely on loading the desktop CLR (.NET CLR)&mdash;the Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process.</span></span> <span data-ttu-id="fa060-1730">Nastavení **verze .NET CLR** na **žádný spravovaný kód** není volitelné, ale doporučuje se.</span><span class="sxs-lookup"><span data-stu-id="fa060-1730">Setting the **.NET CLR version** to **No Managed Code** is optional but recommended.</span></span>

1. <span data-ttu-id="fa060-1731">*ASP.NET Core 2,2 nebo novější*: 64 u samostatného [nasazeného nasazení](/dotnet/core/deploying/#self-contained-deployments-scd) (x64), které používá [model hostování v rámci procesu](#in-process-hosting-model), zakažte fond aplikací pro procesy 32 (x86).</span><span class="sxs-lookup"><span data-stu-id="fa060-1731">*ASP.NET Core 2.2 or later*: For a 64-bit (x64) [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) that uses the [in-process hosting model](#in-process-hosting-model), disable the app pool for 32-bit (x86) processes.</span></span>

   <span data-ttu-id="fa060-1732">V bočním panelu **Akce** Správce služby IIS > **fondy aplikací**vyberte možnost **nastavit výchozí hodnoty fondu aplikací** nebo **Upřesnit nastavení**.</span><span class="sxs-lookup"><span data-stu-id="fa060-1732">In the **Actions** sidebar of IIS Manager > **Application Pools**, select **Set Application Pool Defaults** or **Advanced Settings**.</span></span> <span data-ttu-id="fa060-1733">Vyhledejte **možnost povolit 32 – bitové aplikace** a nastavte hodnotu na `False` .</span><span class="sxs-lookup"><span data-stu-id="fa060-1733">Locate **Enable 32-Bit Applications** and set the value to `False`.</span></span> <span data-ttu-id="fa060-1734">Toto nastavení nemá vliv na aplikace nasazené pro [hostování mimo proces](xref:host-and-deploy/aspnet-core-module#out-of-process-hosting-model).</span><span class="sxs-lookup"><span data-stu-id="fa060-1734">This setting doesn't affect apps deployed for [out-of-process hosting](xref:host-and-deploy/aspnet-core-module#out-of-process-hosting-model).</span></span>

1. <span data-ttu-id="fa060-1735">Potvrďte, že identita modelu procesu má správná oprávnění.</span><span class="sxs-lookup"><span data-stu-id="fa060-1735">Confirm the process model identity has the proper permissions.</span></span>

   <span data-ttu-id="fa060-1736">Pokud se výchozí identita fondu aplikací (**modelu procesu**  >  **Identity** ) změní z **ApplicationPoolIdentity** na jinou identitu, ověřte, že Nová identita má požadovaná oprávnění pro přístup ke složce, databázi a dalším požadovaným prostředkům aplikace.</span><span class="sxs-lookup"><span data-stu-id="fa060-1736">If the default identity of the app pool (**Process Model** > **Identity**) is changed from **ApplicationPoolIdentity** to another identity, verify that the new identity has the required permissions to access the app's folder, database, and other required resources.</span></span> <span data-ttu-id="fa060-1737">Například fond aplikací vyžaduje přístup pro čtení a zápis ke složkám, kde aplikace čte a zapisuje soubory.</span><span class="sxs-lookup"><span data-stu-id="fa060-1737">For example, the app pool requires read and write access to folders where the app reads and writes files.</span></span>

<span data-ttu-id="fa060-1738">**Konfigurace ověřování systému Windows (volitelné)**</span><span class="sxs-lookup"><span data-stu-id="fa060-1738">**Windows Authentication configuration (Optional)**</span></span>  
<span data-ttu-id="fa060-1739">Další informace najdete v tématu [Konfigurace ověřování systému Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="fa060-1739">For more information, see [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

## <a name="deploy-the-app"></a><span data-ttu-id="fa060-1740">Nasazení aplikace</span><span class="sxs-lookup"><span data-stu-id="fa060-1740">Deploy the app</span></span>

<span data-ttu-id="fa060-1741">Nasaďte aplikaci do složky **fyzické cesty** služby IIS, která byla navázána v části [Vytvoření webu služby IIS](#create-the-iis-site) .</span><span class="sxs-lookup"><span data-stu-id="fa060-1741">Deploy the app to the IIS **Physical path** folder that was established in the [Create the IIS site](#create-the-iis-site) section.</span></span> <span data-ttu-id="fa060-1742">[Nasazení webu](/iis/publish/using-web-deploy/introduction-to-web-deploy) je doporučeným mechanismem pro nasazení, ale existuje několik možností pro přesun aplikace ze složky *publikování* projektu do složky pro nasazení hostitelského systému.</span><span class="sxs-lookup"><span data-stu-id="fa060-1742">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) is the recommended mechanism for deployment, but several options exist for moving the app from the project's *publish* folder to the hosting system's deployment folder.</span></span>

### <a name="web-deploy-with-visual-studio"></a><span data-ttu-id="fa060-1743">Nasazení webu se sadou Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fa060-1743">Web Deploy with Visual Studio</span></span>

<span data-ttu-id="fa060-1744">Informace o tom, jak vytvořit profil publikování pro použití s Nasazení webu, najdete v tématu [publikační profily sady Visual Studio pro ASP.NET Core nasazení aplikací](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) .</span><span class="sxs-lookup"><span data-stu-id="fa060-1744">See the [Visual Studio publish profiles for ASP.NET Core app deployment](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) topic to learn how to create a publish profile for use with Web Deploy.</span></span> <span data-ttu-id="fa060-1745">Pokud poskytovatel hostingu poskytuje profil publikování nebo podporu pro jeho vytvoření, Stáhněte si jeho profil a importujte ho pomocí dialogového okna pro **publikování** sady Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="fa060-1745">If the hosting provider provides a Publish Profile or support for creating one, download their profile and import it using the Visual Studio **Publish** dialog:</span></span>

![Stránka publikovat dialog](index/_static/pub-dialog.png)

### <a name="web-deploy-outside-of-visual-studio"></a><span data-ttu-id="fa060-1747">Nasazení webu mimo Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fa060-1747">Web Deploy outside of Visual Studio</span></span>

<span data-ttu-id="fa060-1748">[Nasazení webu](/iis/publish/using-web-deploy/introduction-to-web-deploy) lze použít také mimo sadu Visual Studio z příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="fa060-1748">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) can also be used outside of Visual Studio from the command line.</span></span> <span data-ttu-id="fa060-1749">Další informace najdete v tématu [Nástroj pro nasazení webu](/iis/publish/using-web-deploy/use-the-web-deployment-tool).</span><span class="sxs-lookup"><span data-stu-id="fa060-1749">For more information, see [Web Deployment Tool](/iis/publish/using-web-deploy/use-the-web-deployment-tool).</span></span>

### <a name="alternatives-to-web-deploy"></a><span data-ttu-id="fa060-1750">Alternativy k Nasazení webu</span><span class="sxs-lookup"><span data-stu-id="fa060-1750">Alternatives to Web Deploy</span></span>

<span data-ttu-id="fa060-1751">K přesunu aplikace do hostitelského systému, jako je ruční kopírování, [xcopy](/windows-server/administration/windows-commands/xcopy), [Robocopy](/windows-server/administration/windows-commands/robocopy)nebo [PowerShell](/powershell/), použijte libovolný z několika způsobů.</span><span class="sxs-lookup"><span data-stu-id="fa060-1751">Use any of several methods to move the app to the hosting system, such as manual copy, [Xcopy](/windows-server/administration/windows-commands/xcopy), [Robocopy](/windows-server/administration/windows-commands/robocopy), or [PowerShell](/powershell/).</span></span>

<span data-ttu-id="fa060-1752">Další informace o nasazení ASP.NET Core do služby IIS najdete v části [prostředky nasazení pro správce služby IIS](#deployment-resources-for-iis-administrators) .</span><span class="sxs-lookup"><span data-stu-id="fa060-1752">For more information on ASP.NET Core deployment to IIS, see the [Deployment resources for IIS administrators](#deployment-resources-for-iis-administrators) section.</span></span>

## <a name="browse-the-website"></a><span data-ttu-id="fa060-1753">Procházet web</span><span class="sxs-lookup"><span data-stu-id="fa060-1753">Browse the website</span></span>

<span data-ttu-id="fa060-1754">Po nasazení aplikace do hostitelského systému vytvořte žádost jednomu z veřejných koncových bodů aplikace.</span><span class="sxs-lookup"><span data-stu-id="fa060-1754">After the app is deployed to the hosting system, make a request to one of the app's public endpoints.</span></span>

<span data-ttu-id="fa060-1755">V následujícím příkladu je lokalita svázána s **názvem hostitele** služby IIS `www.mysite.com` na **portu** `80` .</span><span class="sxs-lookup"><span data-stu-id="fa060-1755">In the following example, the site is bound to an IIS **Host name** of `www.mysite.com` on **Port** `80`.</span></span> <span data-ttu-id="fa060-1756">Odeslala se žádost `http://www.mysite.com` :</span><span class="sxs-lookup"><span data-stu-id="fa060-1756">A request is made to `http://www.mysite.com`:</span></span>

![Prohlížeč Microsoft Edge načetl spouštěcí stránku služby IIS.](index/_static/browsewebsite.png)

## <a name="locked-deployment-files"></a><span data-ttu-id="fa060-1758">Uzamčené soubory nasazení</span><span class="sxs-lookup"><span data-stu-id="fa060-1758">Locked deployment files</span></span>

<span data-ttu-id="fa060-1759">Soubory ve složce pro nasazení jsou zamčené, když je aplikace spuštěná.</span><span class="sxs-lookup"><span data-stu-id="fa060-1759">Files in the deployment folder are locked when the app is running.</span></span> <span data-ttu-id="fa060-1760">Uzamčené soubory nejde během nasazování přepsat.</span><span class="sxs-lookup"><span data-stu-id="fa060-1760">Locked files can't be overwritten during deployment.</span></span> <span data-ttu-id="fa060-1761">Chcete-li uvolnit uzamčené soubory v nasazení, zastavte fond aplikací pomocí **jednoho** z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="fa060-1761">To release locked files in a deployment, stop the app pool using **one** of the following approaches:</span></span>

* <span data-ttu-id="fa060-1762">V souboru projektu použijte Nasazení webu a odkaz `Microsoft.NET.Sdk.Web` .</span><span class="sxs-lookup"><span data-stu-id="fa060-1762">Use Web Deploy and reference `Microsoft.NET.Sdk.Web` in the project file.</span></span> <span data-ttu-id="fa060-1763">Soubor *App_offline. htm* se umístí do kořenové složky adresáře webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="fa060-1763">An *app_offline.htm* file is placed at the root of the web app directory.</span></span> <span data-ttu-id="fa060-1764">Když je soubor přítomen, modul ASP.NET Core aplikaci korektně ukončí a během nasazování zachová soubor *App_offline. htm* .</span><span class="sxs-lookup"><span data-stu-id="fa060-1764">When the file is present, the ASP.NET Core Module gracefully shuts down the app and serves the *app_offline.htm* file during the deployment.</span></span> <span data-ttu-id="fa060-1765">Další informace najdete v referenčních informacích k [konfiguraci modulu ASP.NET Core](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).</span><span class="sxs-lookup"><span data-stu-id="fa060-1765">For more information, see the [ASP.NET Core Module configuration reference](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).</span></span>
* <span data-ttu-id="fa060-1766">Ručně Zastavte fond aplikací ve Správci služby IIS na serveru.</span><span class="sxs-lookup"><span data-stu-id="fa060-1766">Manually stop the app pool in the IIS Manager on the server.</span></span>
* <span data-ttu-id="fa060-1767">Použití PowerShellu k vyřazení *App_offline. htm* (vyžaduje PowerShell 5 nebo novější):</span><span class="sxs-lookup"><span data-stu-id="fa060-1767">Use PowerShell to drop *app_offline.htm* (requires PowerShell 5 or later):</span></span>

  ```powershell
  $pathToApp = 'PATH_TO_APP'

  # Stop the AppPool
  New-Item -Path $pathToApp app_offline.htm

  # Provide script commands here to deploy the app

  # Restart the AppPool
  Remove-Item -Path $pathToApp app_offline.htm

  ```

## <a name="data-protection"></a><span data-ttu-id="fa060-1768">Ochrana dat</span><span class="sxs-lookup"><span data-stu-id="fa060-1768">Data protection</span></span>

<span data-ttu-id="fa060-1769">[Sada ASP.NET Core Data Protection Stack](xref:security/data-protection/introduction) je používána několika ASP.NET Core [middlewary](xref:fundamentals/middleware/index), včetně middlewaru používaného při ověřování.</span><span class="sxs-lookup"><span data-stu-id="fa060-1769">The [ASP.NET Core Data Protection stack](xref:security/data-protection/introduction) is used by several ASP.NET Core [middlewares](xref:fundamentals/middleware/index), including middleware used in authentication.</span></span> <span data-ttu-id="fa060-1770">I v případě, že rozhraní API ochrany dat není voláno uživatelským kódem, je třeba nakonfigurovat ochranu dat pomocí skriptu nasazení nebo v uživatelském kódu, aby bylo možné vytvořit trvalé úložiště kryptografických [klíčů](xref:security/data-protection/implementation/key-management).</span><span class="sxs-lookup"><span data-stu-id="fa060-1770">Even if Data Protection APIs aren't called by user code, data protection should be configured with a deployment script or in user code to create a persistent cryptographic [key store](xref:security/data-protection/implementation/key-management).</span></span> <span data-ttu-id="fa060-1771">Pokud ochrana dat není nakonfigurovaná, klíče se uchovávají v paměti a při restartování aplikace se zahodí.</span><span class="sxs-lookup"><span data-stu-id="fa060-1771">If data protection isn't configured, the keys are held in memory and discarded when the app restarts.</span></span>

<span data-ttu-id="fa060-1772">Pokud se klíčového prstence při restartu aplikace uloží do paměti:</span><span class="sxs-lookup"><span data-stu-id="fa060-1772">If the key ring is stored in memory when the app restarts:</span></span>

* <span data-ttu-id="fa060-1773">Všechny ověřovací tokeny založené na souborech cookie jsou neověřené.</span><span class="sxs-lookup"><span data-stu-id="fa060-1773">All cookie-based authentication tokens are invalidated.</span></span> 
* <span data-ttu-id="fa060-1774">Uživatelé se musí znovu přihlásit na svůj další požadavek.</span><span class="sxs-lookup"><span data-stu-id="fa060-1774">Users are required to sign in again on their next request.</span></span> 
* <span data-ttu-id="fa060-1775">Data chráněná pomocí Key ringu už nebude možné dešifrovat.</span><span class="sxs-lookup"><span data-stu-id="fa060-1775">Any data protected with the key ring can no longer be decrypted.</span></span> <span data-ttu-id="fa060-1776">To může zahrnovat [CSRF tokeny](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) a [ASP.NET Core soubory cookie TempData MVC](xref:fundamentals/app-state#tempdata).</span><span class="sxs-lookup"><span data-stu-id="fa060-1776">This may include [CSRF tokens](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) and [ASP.NET Core MVC TempData cookies](xref:fundamentals/app-state#tempdata).</span></span>

<span data-ttu-id="fa060-1777">Pokud chcete v rámci služby IIS nakonfigurovat ochranu dat a zachovat přitom klíčového prstence, použijte **některý** z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="fa060-1777">To configure data protection under IIS to persist the key ring, use **one** of the following approaches:</span></span>

* <span data-ttu-id="fa060-1778">**Vytvoření klíčů registru ochrany dat**</span><span class="sxs-lookup"><span data-stu-id="fa060-1778">**Create Data Protection Registry Keys**</span></span>

  <span data-ttu-id="fa060-1779">Klíče ochrany dat, které používá aplikace ASP.NET Core, jsou uložené v registru externě pro aplikace.</span><span class="sxs-lookup"><span data-stu-id="fa060-1779">Data protection keys used by ASP.NET Core apps are stored in the registry external to the apps.</span></span> <span data-ttu-id="fa060-1780">Pokud chcete zachovat klíče pro danou aplikaci, vytvořte klíče registru pro fond aplikací.</span><span class="sxs-lookup"><span data-stu-id="fa060-1780">To persist the keys for a given app, create registry keys for the app pool.</span></span>

  <span data-ttu-id="fa060-1781">Pro samostatnou instalaci služby IIS, která není součástí webfarmu, se dá [skript PowerShellu pro ochranu dat provision-AutoGenKeys. ps1](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) použít pro každý fond aplikací, který se používá v aplikaci ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="fa060-1781">For standalone, non-webfarm IIS installations, the [Data Protection Provision-AutoGenKeys.ps1 PowerShell script](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) can be used for each app pool used with an ASP.NET Core app.</span></span> <span data-ttu-id="fa060-1782">Tento skript vytvoří klíč registru v registru HKLM, který je přístupný jenom pro účet pracovního procesu fondu aplikací aplikace.</span><span class="sxs-lookup"><span data-stu-id="fa060-1782">This script creates a registry key in the HKLM registry that's accessible only to the worker process account of the app's app pool.</span></span> <span data-ttu-id="fa060-1783">Klíče jsou v klidovém stavu zašifrované pomocí rozhraní DPAPI s klíčem celého počítače.</span><span class="sxs-lookup"><span data-stu-id="fa060-1783">Keys are encrypted at rest using DPAPI with a machine-wide key.</span></span>

  <span data-ttu-id="fa060-1784">Ve scénářích webové farmy může být aplikace nakonfigurovaná tak, aby používala cestu UNC k uložení svého prstence s klíčem pro ochranu dat.</span><span class="sxs-lookup"><span data-stu-id="fa060-1784">In web farm scenarios, an app can be configured to use a UNC path to store its data protection key ring.</span></span> <span data-ttu-id="fa060-1785">Ve výchozím nastavení nejsou klíče ochrany dat šifrované.</span><span class="sxs-lookup"><span data-stu-id="fa060-1785">By default, the data protection keys aren't encrypted.</span></span> <span data-ttu-id="fa060-1786">Ujistěte se, že oprávnění k souborům pro sdílenou síťovou složku jsou omezená na účet systému Windows, pod kterým je aplikace spuštěná.</span><span class="sxs-lookup"><span data-stu-id="fa060-1786">Ensure that the file permissions for the network share are limited to the Windows account the app runs under.</span></span> <span data-ttu-id="fa060-1787">K ochraně neaktivních klíčů je možné použít certifikát x509.</span><span class="sxs-lookup"><span data-stu-id="fa060-1787">An X509 certificate can be used to protect keys at rest.</span></span> <span data-ttu-id="fa060-1788">Vezměte v úvahu mechanismus, který umožní uživatelům odeslat certifikáty: Umístěte certifikáty do důvěryhodného úložiště certifikátů uživatele a ujistěte se, že jsou k dispozici na všech počítačích, kde je aplikace uživatele spuštěna.</span><span class="sxs-lookup"><span data-stu-id="fa060-1788">Consider a mechanism to allow users to upload certificates: Place certificates into the user's trusted certificate store and ensure they're available on all machines where the user's app runs.</span></span> <span data-ttu-id="fa060-1789">Podrobnosti najdete v tématu [Konfigurace ochrany ASP.NET Core dat](xref:security/data-protection/configuration/overview) .</span><span class="sxs-lookup"><span data-stu-id="fa060-1789">See [Configure ASP.NET Core Data Protection](xref:security/data-protection/configuration/overview) for details.</span></span>

* <span data-ttu-id="fa060-1790">**Konfigurace fondu aplikací služby IIS pro načtení profilu uživatele**</span><span class="sxs-lookup"><span data-stu-id="fa060-1790">**Configure the IIS Application Pool to load the user profile**</span></span>

  <span data-ttu-id="fa060-1791">Toto nastavení se nachází v části **model procesu** v části **Rozšířená nastavení** fondu aplikací.</span><span class="sxs-lookup"><span data-stu-id="fa060-1791">This setting is in the **Process Model** section under the **Advanced Settings** for the app pool.</span></span> <span data-ttu-id="fa060-1792">Nastavte **načíst profil uživatele** na `True` .</span><span class="sxs-lookup"><span data-stu-id="fa060-1792">Set **Load User Profile** to `True`.</span></span> <span data-ttu-id="fa060-1793">Pokud je nastaveno na `True` , klíče jsou uloženy v adresáři profilu uživatele a chráněny pomocí rozhraní DPAPI s klíčem specifickým pro uživatelský účet.</span><span class="sxs-lookup"><span data-stu-id="fa060-1793">When set to `True`, keys are stored in the user profile directory and protected using DPAPI with a key specific to the user account.</span></span> <span data-ttu-id="fa060-1794">Klíče jsou uchovány ve složce *% localappdata%/ASP.NET/DataProtection-Keys* .</span><span class="sxs-lookup"><span data-stu-id="fa060-1794">Keys are persisted to the *%LOCALAPPDATA%/ASP.NET/DataProtection-Keys* folder.</span></span>

  <span data-ttu-id="fa060-1795">Musí být povolený i [atribut setProfileEnvironment](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) fondu aplikací.</span><span class="sxs-lookup"><span data-stu-id="fa060-1795">The app pool's [setProfileEnvironment attribute](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) must also be enabled.</span></span> <span data-ttu-id="fa060-1796">Výchozí hodnota `setProfileEnvironment` je `true` .</span><span class="sxs-lookup"><span data-stu-id="fa060-1796">The default value of `setProfileEnvironment` is `true`.</span></span> <span data-ttu-id="fa060-1797">V některých scénářích (například operační systém Windows) `setProfileEnvironment` je nastavena na `false` .</span><span class="sxs-lookup"><span data-stu-id="fa060-1797">In some scenarios (for example, Windows OS), `setProfileEnvironment` is set to `false`.</span></span> <span data-ttu-id="fa060-1798">Pokud se klíče neukládají v adresáři profilu uživatele podle očekávání:</span><span class="sxs-lookup"><span data-stu-id="fa060-1798">If keys aren't stored in the user profile directory as expected:</span></span>

  1. <span data-ttu-id="fa060-1799">Přejděte do složky *% windir%/system32/Inetsrv/config* .</span><span class="sxs-lookup"><span data-stu-id="fa060-1799">Navigate to the *%windir%/system32/inetsrv/config* folder.</span></span>
  1. <span data-ttu-id="fa060-1800">Otevřete soubor *ApplicationHost. config* .</span><span class="sxs-lookup"><span data-stu-id="fa060-1800">Open the *applicationHost.config* file.</span></span>
  1. <span data-ttu-id="fa060-1801">Vyhledejte element `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>`.</span><span class="sxs-lookup"><span data-stu-id="fa060-1801">Locate the `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` element.</span></span>
  1. <span data-ttu-id="fa060-1802">Potvrďte, že `setProfileEnvironment` atribut není přítomen, přičemž výchozí hodnota je `true` , nebo explicitně nastavte hodnotu atributu na `true` .</span><span class="sxs-lookup"><span data-stu-id="fa060-1802">Confirm that the `setProfileEnvironment` attribute isn't present, which defaults the value to `true`, or explicitly set the attribute's value to `true`.</span></span>

* <span data-ttu-id="fa060-1803">**Použití systému souborů jako úložiště pro Key Ring**</span><span class="sxs-lookup"><span data-stu-id="fa060-1803">**Use the file system as a key ring store**</span></span>

  <span data-ttu-id="fa060-1804">Upravte kód aplikace tak, aby [používal systém souborů jako úložiště ve službě Key Ring](xref:security/data-protection/configuration/overview).</span><span class="sxs-lookup"><span data-stu-id="fa060-1804">Adjust the app code to [use the file system as a key ring store](xref:security/data-protection/configuration/overview).</span></span> <span data-ttu-id="fa060-1805">K ochraně služby Key Ring použijte certifikát x509 a ujistěte se, že certifikát je důvěryhodný certifikát.</span><span class="sxs-lookup"><span data-stu-id="fa060-1805">Use an X509 certificate to protect the key ring and ensure the certificate is a trusted certificate.</span></span> <span data-ttu-id="fa060-1806">Pokud je certifikát podepsaný svým držitelem, umístěte certifikát do důvěryhodného kořenového úložiště.</span><span class="sxs-lookup"><span data-stu-id="fa060-1806">If the certificate is self-signed, place the certificate in the Trusted Root store.</span></span>

  <span data-ttu-id="fa060-1807">Při použití služby IIS ve webové farmě:</span><span class="sxs-lookup"><span data-stu-id="fa060-1807">When using IIS in a web farm:</span></span>

  * <span data-ttu-id="fa060-1808">Použijte sdílenou složku, ke které mají přístup všechny počítače.</span><span class="sxs-lookup"><span data-stu-id="fa060-1808">Use a file share that all machines can access.</span></span>
  * <span data-ttu-id="fa060-1809">Nasaďte do každého počítače certifikát x509.</span><span class="sxs-lookup"><span data-stu-id="fa060-1809">Deploy an X509 certificate to each machine.</span></span> <span data-ttu-id="fa060-1810">Konfigurace [ochrany dat v kódu](xref:security/data-protection/configuration/overview).</span><span class="sxs-lookup"><span data-stu-id="fa060-1810">Configure [data protection in code](xref:security/data-protection/configuration/overview).</span></span>

* <span data-ttu-id="fa060-1811">**Nastavení zásad pro ochranu dat na úrovni počítače**</span><span class="sxs-lookup"><span data-stu-id="fa060-1811">**Set a machine-wide policy for data protection**</span></span>

  <span data-ttu-id="fa060-1812">Systém ochrany dat má omezená podpora pro nastavení výchozích [zásad](xref:security/data-protection/configuration/machine-wide-policy) pro všechny aplikace, které využívají rozhraní API pro ochranu dat.</span><span class="sxs-lookup"><span data-stu-id="fa060-1812">The data protection system has limited support for setting a default [machine-wide policy](xref:security/data-protection/configuration/machine-wide-policy) for all apps that consume the Data Protection APIs.</span></span> <span data-ttu-id="fa060-1813">Další informace naleznete v tématu <xref:security/data-protection/introduction>.</span><span class="sxs-lookup"><span data-stu-id="fa060-1813">For more information, see <xref:security/data-protection/introduction>.</span></span>

## <a name="virtual-directories"></a><span data-ttu-id="fa060-1814">Virtuální adresáře</span><span class="sxs-lookup"><span data-stu-id="fa060-1814">Virtual Directories</span></span>

<span data-ttu-id="fa060-1815">Aplikace ASP.NET Core nepodporují [virtuální adresáře služby IIS](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) .</span><span class="sxs-lookup"><span data-stu-id="fa060-1815">[IIS Virtual Directories](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) aren't supported with ASP.NET Core apps.</span></span> <span data-ttu-id="fa060-1816">Aplikace může být hostována jako [dílčí aplikace](#sub-applications).</span><span class="sxs-lookup"><span data-stu-id="fa060-1816">An app can be hosted as a [sub-application](#sub-applications).</span></span>

## <a name="sub-applications"></a><span data-ttu-id="fa060-1817">Dílčí aplikace</span><span class="sxs-lookup"><span data-stu-id="fa060-1817">Sub-applications</span></span>

<span data-ttu-id="fa060-1818">Aplikace ASP.NET Core může být hostovaná jako [podaplikace služby IIS (dílčí aplikace)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications).</span><span class="sxs-lookup"><span data-stu-id="fa060-1818">An ASP.NET Core app can be hosted as an [IIS sub-application (sub-app)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications).</span></span> <span data-ttu-id="fa060-1819">Cesta k dílčí aplikaci se stala součástí adresy URL kořenové aplikace.</span><span class="sxs-lookup"><span data-stu-id="fa060-1819">The sub-app's path becomes part of the root app's URL.</span></span>

<span data-ttu-id="fa060-1820">Dílčí aplikace by neměla obsahovat modul ASP.NET Core jako obslužnou rutinu.</span><span class="sxs-lookup"><span data-stu-id="fa060-1820">A sub-app shouldn't include the ASP.NET Core Module as a handler.</span></span> <span data-ttu-id="fa060-1821">Pokud se modul přidá jako obslužná rutina v souboru *Web. config* dílčí aplikace 500,19, při pokusu o procházení dílčí aplikace se obdrží *interní chyba serveru* odkazující na chybný konfigurační soubor.</span><span class="sxs-lookup"><span data-stu-id="fa060-1821">If the module is added as a handler in a sub-app's *web.config* file, a *500.19 Internal Server Error* referencing the faulty config file is received when attempting to browse the sub-app.</span></span>

<span data-ttu-id="fa060-1822">Následující příklad ukazuje publikovaný soubor *Web. config* pro ASP.NET Core dílčí aplikaci:</span><span class="sxs-lookup"><span data-stu-id="fa060-1822">The following example shows a published *web.config* file for an ASP.NET Core sub-app:</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <aspNetCore processPath="dotnet" 
      arguments=".\MyApp.dll" 
      stdoutLogEnabled="false" 
      stdoutLogFile=".\logs\stdout" />
  </system.webServer>
</configuration>
```

<span data-ttu-id="fa060-1823">Když se v aplikaci ASP.NET Core hostuje podaplikace Core non-ASP.NET, explicitně se odebere zděděná obslužná rutina v souboru *Web. config* dílčí aplikace:</span><span class="sxs-lookup"><span data-stu-id="fa060-1823">When hosting a non-ASP.NET Core sub-app underneath an ASP.NET Core app, explicitly remove the inherited handler in the sub-app's *web.config* file:</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <handlers>
      <remove name="aspNetCore" />
    </handlers>
    <aspNetCore processPath="dotnet" 
      arguments=".\MyApp.dll" 
      stdoutLogEnabled="false" 
      stdoutLogFile=".\logs\stdout" />
  </system.webServer>
</configuration>
```

<span data-ttu-id="fa060-1824">Propojení statických prostředků v rámci dílčí aplikace by měla používat notaci vlnovku ( `~/` ).</span><span class="sxs-lookup"><span data-stu-id="fa060-1824">Static asset links within the sub-app should use tilde-slash (`~/`) notation.</span></span> <span data-ttu-id="fa060-1825">Znak tildy vlnovkou spustí [pomocnou nápovědu](xref:mvc/views/tag-helpers/intro) k předřazení pathbase dílčí aplikace na vykreslený relativní odkaz.</span><span class="sxs-lookup"><span data-stu-id="fa060-1825">Tilde-slash notation triggers a [Tag Helper](xref:mvc/views/tag-helpers/intro) to prepend the sub-app's pathbase to the rendered relative link.</span></span> <span data-ttu-id="fa060-1826">V případě dílčí aplikace na `/subapp_path` je obrázek propojený s objektem `src="~/image.png"` vykreslen jako `src="/subapp_path/image.png"` .</span><span class="sxs-lookup"><span data-stu-id="fa060-1826">For a sub-app at `/subapp_path`, an image linked with `src="~/image.png"` is rendered as `src="/subapp_path/image.png"`.</span></span> <span data-ttu-id="fa060-1827">Middleware statických souborů kořenové aplikace nezpracovává požadavek na statický soubor.</span><span class="sxs-lookup"><span data-stu-id="fa060-1827">The root app's Static File Middleware doesn't process the static file request.</span></span> <span data-ttu-id="fa060-1828">Požadavek zpracovává middleware statických souborů v dílčí aplikaci.</span><span class="sxs-lookup"><span data-stu-id="fa060-1828">The request is processed by the sub-app's Static File Middleware.</span></span>

<span data-ttu-id="fa060-1829">Pokud je atribut statického prostředku `src` nastavený na absolutní cestu (například `src="/image.png"` ), odkaz se vykreslí bez pathbase dílčí aplikace.</span><span class="sxs-lookup"><span data-stu-id="fa060-1829">If a static asset's `src` attribute is set to an absolute path (for example, `src="/image.png"`), the link is rendered without the sub-app's pathbase.</span></span> <span data-ttu-id="fa060-1830">Middleware statických souborů v kořenové aplikaci se pokusí o poskytování assetu z [kořenového adresáře webu](xref:fundamentals/index#web-root)kořenové aplikace, což má za následek *404 – nenalezené* odpovědi, pokud není k dispozici statický prostředek z kořenové aplikace.</span><span class="sxs-lookup"><span data-stu-id="fa060-1830">The root app's Static File Middleware attempts to serve the asset from the root app's [web root](xref:fundamentals/index#web-root), which results in a *404 - Not Found* response unless the static asset is available from the root app.</span></span>

<span data-ttu-id="fa060-1831">Hostování aplikace ASP.NET Core jako dílčí aplikace v jiné aplikaci ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="fa060-1831">To host an ASP.NET Core app as a sub-app under another ASP.NET Core app:</span></span>

1. <span data-ttu-id="fa060-1832">Vytvořte fond aplikací pro dílčí aplikaci.</span><span class="sxs-lookup"><span data-stu-id="fa060-1832">Establish an app pool for the sub-app.</span></span> <span data-ttu-id="fa060-1833">Nastavte **verzi .NET CLR** na **žádný spravovaný kód** , protože základní modul CLR (Common Language Runtime) pro .NET Core se spouští k hostování aplikace v pracovním procesu, ne CLR Desktop (.NET CLR).</span><span class="sxs-lookup"><span data-stu-id="fa060-1833">Set the **.NET CLR Version** to **No Managed Code** because the Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process, not the desktop CLR (.NET CLR).</span></span>

1. <span data-ttu-id="fa060-1834">Přidejte kořenovou lokalitu ve Správci služby IIS pomocí dílčí aplikace do složky v kořenové lokalitě.</span><span class="sxs-lookup"><span data-stu-id="fa060-1834">Add the root site in IIS Manager with the sub-app in a folder under the root site.</span></span>

1. <span data-ttu-id="fa060-1835">Klikněte pravým tlačítkem na složku dílčí aplikace ve Správci služby IIS a vyberte **převést na aplikaci**.</span><span class="sxs-lookup"><span data-stu-id="fa060-1835">Right-click the sub-app folder in IIS Manager and select **Convert to Application**.</span></span>

1. <span data-ttu-id="fa060-1836">V dialogovém okně **Přidat aplikaci** použijte pro **fond aplikací** tlačítko **Vybrat** , abyste přiřadili fond aplikací, který jste vytvořili pro dílčí aplikaci.</span><span class="sxs-lookup"><span data-stu-id="fa060-1836">In the **Add Application** dialog, use the **Select** button for the **Application Pool** to assign the app pool that you created for the sub-app.</span></span> <span data-ttu-id="fa060-1837">Vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="fa060-1837">Select **OK**.</span></span>

<span data-ttu-id="fa060-1838">Přiřazení samostatného fondu aplikací k dílčí aplikaci je požadavek při použití modelu hostování v rámci procesu.</span><span class="sxs-lookup"><span data-stu-id="fa060-1838">The assignment of a separate app pool to the sub-app is a requirement when using the in-process hosting model.</span></span>

<span data-ttu-id="fa060-1839">Další informace o modelu hostování v rámci procesu a konfiguraci modulu ASP.NET Core naleznete v tématu <xref:host-and-deploy/aspnet-core-module> .</span><span class="sxs-lookup"><span data-stu-id="fa060-1839">For more information on the in-process hosting model and configuring the ASP.NET Core Module, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

## <a name="configuration-of-iis-with-webconfig"></a><span data-ttu-id="fa060-1840">Konfigurace služby IIS pomocí souboru Web. config</span><span class="sxs-lookup"><span data-stu-id="fa060-1840">Configuration of IIS with web.config</span></span>

<span data-ttu-id="fa060-1841">Konfigurace služby IIS má vliv na `<system.webServer>` část souboru *Web. config* pro scénáře služby IIS, které jsou funkční pro ASP.NET Core aplikací s modulem ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="fa060-1841">IIS configuration is influenced by the `<system.webServer>` section of *web.config* for IIS scenarios that are functional for ASP.NET Core apps with the ASP.NET Core Module.</span></span> <span data-ttu-id="fa060-1842">Například konfigurace služby IIS je funkční pro dynamickou kompresi.</span><span class="sxs-lookup"><span data-stu-id="fa060-1842">For example, IIS configuration is functional for dynamic compression.</span></span> <span data-ttu-id="fa060-1843">Pokud je služba IIS nakonfigurovaná na úrovni serveru, aby používala dynamickou kompresi, `<urlCompression>` element v souboru *Web. config* aplikace ho může zakázat pro ASP.NET Core aplikaci.</span><span class="sxs-lookup"><span data-stu-id="fa060-1843">If IIS is configured at the server level to use dynamic compression, the `<urlCompression>` element in the app's *web.config* file can disable it for an ASP.NET Core app.</span></span>

<span data-ttu-id="fa060-1844">Další informace najdete v následujících tématech:</span><span class="sxs-lookup"><span data-stu-id="fa060-1844">For more information, see the following topics:</span></span>

* [<span data-ttu-id="fa060-1845">Odkaz na konfiguraci pro\<system.webServer></span><span class="sxs-lookup"><span data-stu-id="fa060-1845">Configuration reference for \<system.webServer></span></span>](/iis/configuration/system.webServer/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>

<span data-ttu-id="fa060-1846">Informace o nastavení proměnných prostředí pro jednotlivé aplikace spuštěné v izolovaných fondech aplikací (podporované pro IIS 10,0 nebo novější) najdete v části *příkaz Appcmd. exe* tématu [proměnné \<environmentVariables> prostředí](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) v referenční dokumentaci služby IIS.</span><span class="sxs-lookup"><span data-stu-id="fa060-1846">To set environment variables for individual apps running in isolated app pools (supported for IIS 10.0 or later), see the *AppCmd.exe command* section of the [Environment Variables \<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) topic in the IIS reference documentation.</span></span>

## <a name="configuration-sections-of-webconfig"></a><span data-ttu-id="fa060-1847">Konfigurační oddíly souboru Web. config</span><span class="sxs-lookup"><span data-stu-id="fa060-1847">Configuration sections of web.config</span></span>

<span data-ttu-id="fa060-1848">Konfigurační oddíly aplikací ASP.NET 4. x v *souboru Web. config* nepoužívá aplikace ASP.NET Core pro konfiguraci:</span><span class="sxs-lookup"><span data-stu-id="fa060-1848">Configuration sections of ASP.NET 4.x apps in *web.config* aren't used by ASP.NET Core apps for configuration:</span></span>

* `<system.web>`
* `<appSettings>`
* `<connectionStrings>`
* `<location>`

<span data-ttu-id="fa060-1849">Aplikace ASP.NET Core jsou nakonfigurovány pomocí jiných poskytovatelů konfigurace.</span><span class="sxs-lookup"><span data-stu-id="fa060-1849">ASP.NET Core apps are configured using other configuration providers.</span></span> <span data-ttu-id="fa060-1850">Další informace najdete v tématu [Konfigurace](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="fa060-1850">For more information, see [Configuration](xref:fundamentals/configuration/index).</span></span>

## <a name="application-pools"></a><span data-ttu-id="fa060-1851">Fondy aplikací</span><span class="sxs-lookup"><span data-stu-id="fa060-1851">Application Pools</span></span>

<span data-ttu-id="fa060-1852">Při hostování více webů na serveru doporučujeme vzájemně izolovat aplikace tím, že spustíte každou aplikaci ve svém vlastním fondu aplikací.</span><span class="sxs-lookup"><span data-stu-id="fa060-1852">When hosting multiple websites on a server, we recommend isolating the apps from each other by running each app in its own app pool.</span></span> <span data-ttu-id="fa060-1853">Do této konfigurace se nastaví dialogové okno **Přidat web** do služby IIS.</span><span class="sxs-lookup"><span data-stu-id="fa060-1853">The IIS **Add Website** dialog defaults to this configuration.</span></span> <span data-ttu-id="fa060-1854">Když je zadaný **název lokality** , text se automaticky přenese do textového pole **fondu aplikací** .</span><span class="sxs-lookup"><span data-stu-id="fa060-1854">When a **Site name** is provided, the text is automatically transferred to the **Application pool** textbox.</span></span> <span data-ttu-id="fa060-1855">Po přidání webu se vytvoří nový fond aplikací s názvem lokality.</span><span class="sxs-lookup"><span data-stu-id="fa060-1855">A new app pool is created using the site name when the site is added.</span></span>

## <a name="application-pool-identity"></a><span data-ttu-id="fa060-1856">Fond aplikacíIdentity</span><span class="sxs-lookup"><span data-stu-id="fa060-1856">Application Pool Identity</span></span>

<span data-ttu-id="fa060-1857">Účet identity fondu aplikací umožňuje aplikaci běžet v rámci jedinečného účtu, aniž by bylo nutné vytvářet a spravovat domény nebo místní účty.</span><span class="sxs-lookup"><span data-stu-id="fa060-1857">An app pool identity account allows an app to run under a unique account without having to create and manage domains or local accounts.</span></span> <span data-ttu-id="fa060-1858">Ve službě IIS 8,0 nebo novější služba pracovní proces správce služby IIS (WAS) vytvoří virtuální účet s názvem nového fondu aplikací a ve výchozím nastavení spustí pracovní procesy fondu aplikací v rámci tohoto účtu.</span><span class="sxs-lookup"><span data-stu-id="fa060-1858">On IIS 8.0 or later, the IIS Admin Worker Process (WAS) creates a virtual account with the name of the new app pool and runs the app pool's worker processes under this account by default.</span></span> <span data-ttu-id="fa060-1859">V konzole pro správu služby IIS v části **Upřesnit nastavení** fondu aplikací zajistěte, aby byl **Identity** nastaven na použití **ApplicationPoolIdentity**:</span><span class="sxs-lookup"><span data-stu-id="fa060-1859">In the IIS Management Console under **Advanced Settings** for the app pool, ensure that the **Identity** is set to use **ApplicationPoolIdentity**:</span></span>

![Dialogové okno Upřesnit nastavení fondu aplikací](index/_static/apppool-identity.png)

<span data-ttu-id="fa060-1861">Proces správy služby IIS vytvoří v systému zabezpečení systému Windows zabezpečený identifikátor s názvem fondu aplikací.</span><span class="sxs-lookup"><span data-stu-id="fa060-1861">The IIS management process creates a secure identifier with the name of the app pool in the Windows Security System.</span></span> <span data-ttu-id="fa060-1862">Prostředky je možné zabezpečit pomocí této identity.</span><span class="sxs-lookup"><span data-stu-id="fa060-1862">Resources can be secured using this identity.</span></span> <span data-ttu-id="fa060-1863">Tato identita ale není skutečným uživatelským účtem a nezobrazuje se v konzole pro správu uživatelů Windows.</span><span class="sxs-lookup"><span data-stu-id="fa060-1863">However, this identity isn't a real user account and doesn't show up in the Windows User Management Console.</span></span>

<span data-ttu-id="fa060-1864">Pokud pracovní proces služby IIS vyžaduje zvýšený přístup k aplikaci, upravte seznam Access Control (ACL) pro adresář obsahující aplikaci:</span><span class="sxs-lookup"><span data-stu-id="fa060-1864">If the IIS worker process requires elevated access to the app, modify the Access Control List (ACL) for the directory containing the app:</span></span>

1. <span data-ttu-id="fa060-1865">Otevřete Průzkumníka Windows a přejděte do adresáře.</span><span class="sxs-lookup"><span data-stu-id="fa060-1865">Open Windows Explorer and navigate to the directory.</span></span>

1. <span data-ttu-id="fa060-1866">Klikněte pravým tlačítkem na adresář a vyberte **vlastnosti**.</span><span class="sxs-lookup"><span data-stu-id="fa060-1866">Right-click on the directory and select **Properties**.</span></span>

1. <span data-ttu-id="fa060-1867">Na kartě **zabezpečení** vyberte tlačítko **Upravit** a pak klikněte na tlačítko **Přidat** .</span><span class="sxs-lookup"><span data-stu-id="fa060-1867">Under the **Security** tab, select the **Edit** button and then the **Add** button.</span></span>

1. <span data-ttu-id="fa060-1868">Vyberte tlačítko **umístění** a ujistěte se, že je vybraný systém.</span><span class="sxs-lookup"><span data-stu-id="fa060-1868">Select the **Locations** button and make sure the system is selected.</span></span>

1. <span data-ttu-id="fa060-1869">Do pole **Zadejte názvy objektů k výběru** oblasti zadejte \*\* \\<app_pool_name služby IIS AppPool>\*\* .</span><span class="sxs-lookup"><span data-stu-id="fa060-1869">Enter **IIS AppPool\\<app_pool_name>** in **Enter the object names to select** area.</span></span> <span data-ttu-id="fa060-1870">Vyberte tlačítko pro **kontrolu názvů** .</span><span class="sxs-lookup"><span data-stu-id="fa060-1870">Select the **Check Names** button.</span></span> <span data-ttu-id="fa060-1871">Pro službu *DefaultAppPool* ověřte názvy pomocí **služby IIS AppPool\DefaultAppPool**.</span><span class="sxs-lookup"><span data-stu-id="fa060-1871">For the *DefaultAppPool* check the names using **IIS AppPool\DefaultAppPool**.</span></span> <span data-ttu-id="fa060-1872">Když je vybráno tlačítko pro **kontrolu názvů** , hodnota **DefaultAppPool** je uvedena v oblasti názvy objektů.</span><span class="sxs-lookup"><span data-stu-id="fa060-1872">When the **Check Names** button is selected, a value of **DefaultAppPool** is indicated in the object names area.</span></span> <span data-ttu-id="fa060-1873">Není možné zadat název fondu aplikací přímo do oblasti názvy objektů.</span><span class="sxs-lookup"><span data-stu-id="fa060-1873">It isn't possible to enter the app pool name directly into the object names area.</span></span> <span data-ttu-id="fa060-1874">Při kontrole názvu objektu použijte \*\* \\<APP_POOL_NAME>formátu IIS AppPool\*\* .</span><span class="sxs-lookup"><span data-stu-id="fa060-1874">Use the **IIS AppPool\\<app_pool_name>** format when checking for the object name.</span></span>

   ![Dialogová okna pro výběr uživatelů nebo skupin pro složku aplikace: název fondu aplikací "DefaultAppPool" se připojí k "rozhraní IIS AppPool" \" v oblasti názvy objektů před výběrem "kontrolovat jména".](index/_static/select-users-or-groups-1.png)

1. <span data-ttu-id="fa060-1876">Vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="fa060-1876">Select **OK**.</span></span>

   ![Dialogová okna pro výběr uživatelů nebo skupin pro složku aplikace: po výběru možnosti kontrolovat názvy se v oblasti názvy objektů zobrazí název objektu DefaultAppPool.](index/_static/select-users-or-groups-2.png)

1. <span data-ttu-id="fa060-1878">&amp;Ve výchozím nastavení by měla být udělena oprávnění ke čtení.</span><span class="sxs-lookup"><span data-stu-id="fa060-1878">Read &amp; execute permissions should be granted by default.</span></span> <span data-ttu-id="fa060-1879">V případě potřeby zadejte další oprávnění.</span><span class="sxs-lookup"><span data-stu-id="fa060-1879">Provide additional permissions as needed.</span></span>

<span data-ttu-id="fa060-1880">Přístup se dá taky udělit na příkazovém řádku pomocí nástroje **Icacls** .</span><span class="sxs-lookup"><span data-stu-id="fa060-1880">Access can also be granted at a command prompt using the **ICACLS** tool.</span></span> <span data-ttu-id="fa060-1881">Pomocí příkazu *DefaultAppPool* jako příkladu se používá následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="fa060-1881">Using the *DefaultAppPool* as an example, the following command is used:</span></span>

```console
ICACLS C:\sites\MyWebApp /grant "IIS AppPool\DefaultAppPool":F
```

<span data-ttu-id="fa060-1882">Další informace najdete v tématu [Icacls](/windows-server/administration/windows-commands/icacls) .</span><span class="sxs-lookup"><span data-stu-id="fa060-1882">For more information, see the [icacls](/windows-server/administration/windows-commands/icacls) topic.</span></span>

## <a name="http2-support"></a><span data-ttu-id="fa060-1883">Podpora HTTP/2</span><span class="sxs-lookup"><span data-stu-id="fa060-1883">HTTP/2 support</span></span>

<span data-ttu-id="fa060-1884">[Http/2](https://httpwg.org/specs/rfc7540.html) se podporuje pro nasazení mimo procesy, která splňují následující základní požadavky:</span><span class="sxs-lookup"><span data-stu-id="fa060-1884">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is supported for out-of-process deployments that meet the following base requirements:</span></span>

* <span data-ttu-id="fa060-1885">Windows Server 2016/Windows 10 nebo novější; IIS 10 nebo novější</span><span class="sxs-lookup"><span data-stu-id="fa060-1885">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
* <span data-ttu-id="fa060-1886">Veřejná připojení hraničních serverů používají protokol HTTP/2, ale připojení reverzního proxy [serveru Kestrel](xref:fundamentals/servers/kestrel) používá protokol HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="fa060-1886">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to the [Kestrel server](xref:fundamentals/servers/kestrel) uses HTTP/1.1.</span></span>
* <span data-ttu-id="fa060-1887">Cílová architektura: neplatí pro nasazení mimo procesy, protože připojení HTTP/2 je zpracováváno výhradně službou IIS.</span><span class="sxs-lookup"><span data-stu-id="fa060-1887">Target framework: Not applicable to out-of-process deployments, since the HTTP/2 connection is handled entirely by IIS.</span></span>
* <span data-ttu-id="fa060-1888">Připojení TLS 1,2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="fa060-1888">TLS 1.2 or later connection</span></span>

<span data-ttu-id="fa060-1889">Pokud je vytvořeno připojení HTTP/2, sestavy [HttpRequest. Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) `HTTP/1.1` .</span><span class="sxs-lookup"><span data-stu-id="fa060-1889">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/1.1`.</span></span>

<span data-ttu-id="fa060-1890">HTTP/2 je ve výchozím nastavení povolené.</span><span class="sxs-lookup"><span data-stu-id="fa060-1890">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="fa060-1891">Pokud není navázáno připojení HTTP/2, připojení se vrátí k HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="fa060-1891">Connections fall back to HTTP/1.1 if an HTTP/2 connection isn't established.</span></span> <span data-ttu-id="fa060-1892">Další informace o konfiguraci HTTP/2 v nasazeních služby IIS najdete v tématu [http/2 ve službě IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).</span><span class="sxs-lookup"><span data-stu-id="fa060-1892">For more information on HTTP/2 configuration with IIS deployments, see [HTTP/2 on IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).</span></span>

## <a name="cors-preflight-requests"></a><span data-ttu-id="fa060-1893">Požadavky na kontrolu před výstupem CORS</span><span class="sxs-lookup"><span data-stu-id="fa060-1893">CORS preflight requests</span></span>

<span data-ttu-id="fa060-1894">*Tato část platí jenom pro ASP.NET Core aplikace, které cílí na .NET Framework.*</span><span class="sxs-lookup"><span data-stu-id="fa060-1894">*This section only applies to ASP.NET Core apps that target the .NET Framework.*</span></span>

<span data-ttu-id="fa060-1895">Pro ASP.NET Core aplikaci, která cílí na .NET Framework, požadavky na možnosti nejsou ve výchozím nastavení ve službě IIS předány do aplikace.</span><span class="sxs-lookup"><span data-stu-id="fa060-1895">For an ASP.NET Core app that targets the .NET Framework, OPTIONS requests aren't passed to the app by default in IIS.</span></span> <span data-ttu-id="fa060-1896">Informace o tom, jak nakonfigurovat obslužné rutiny IIS aplikace v *souboru Web. config* , aby předávala požadavky na možnosti, najdete v tématu [Povolení žádostí o více zdrojů v ASP.NET webovém rozhraní API 2: jak CORS funguje](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works).</span><span class="sxs-lookup"><span data-stu-id="fa060-1896">To learn how to configure the app's IIS handlers in *web.config* to pass OPTIONS requests, see [Enable cross-origin requests in ASP.NET Web API 2: How CORS Works](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works).</span></span>

## <a name="deployment-resources-for-iis-administrators"></a><span data-ttu-id="fa060-1897">Prostředky nasazení pro správce služby IIS</span><span class="sxs-lookup"><span data-stu-id="fa060-1897">Deployment resources for IIS administrators</span></span>

* [<span data-ttu-id="fa060-1898">Dokumentace ke službě IIS</span><span class="sxs-lookup"><span data-stu-id="fa060-1898">IIS documentation</span></span>](/iis)
* [<span data-ttu-id="fa060-1899">Začínáme ve službě IIS pomocí Správce služby IIS</span><span class="sxs-lookup"><span data-stu-id="fa060-1899">Getting Started with the IIS Manager in IIS</span></span>](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* [<span data-ttu-id="fa060-1900">Nasazení aplikace .NET Core</span><span class="sxs-lookup"><span data-stu-id="fa060-1900">.NET Core application deployment</span></span>](/dotnet/core/deploying/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:host-and-deploy/iis/modules>
* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

## <a name="additional-resources"></a><span data-ttu-id="fa060-1901">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="fa060-1901">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:index>
* [<span data-ttu-id="fa060-1902">Oficiální web Microsoft IIS</span><span class="sxs-lookup"><span data-stu-id="fa060-1902">The Official Microsoft IIS Site</span></span>](https://www.iis.net/)
* [<span data-ttu-id="fa060-1903">Knihovna technických obsahu pro Windows Server</span><span class="sxs-lookup"><span data-stu-id="fa060-1903">Windows Server technical content library</span></span>](/windows-server/windows-server)
* [<span data-ttu-id="fa060-1904">HTTP/2 ve službě IIS</span><span class="sxs-lookup"><span data-stu-id="fa060-1904">HTTP/2 on IIS</span></span>](/iis/get-started/whats-new-in-iis-10/http2-on-iis)
* <xref:host-and-deploy/iis/transform-webconfig>

::: moniker-end
