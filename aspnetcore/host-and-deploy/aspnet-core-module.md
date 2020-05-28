---
<span data-ttu-id="ae9a6-101">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ae9a6-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ae9a6-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ae9a6-102">'Blazor'</span></span>
- <span data-ttu-id="ae9a6-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ae9a6-103">'Identity'</span></span>
- <span data-ttu-id="ae9a6-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ae9a6-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="ae9a6-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ae9a6-105">'Razor'</span></span>
- <span data-ttu-id="ae9a6-106">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="ae9a6-106">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-module"></a><span data-ttu-id="ae9a6-107">Modul ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ae9a6-107">ASP.NET Core Module</span></span>

<span data-ttu-id="ae9a6-108">Díky [Dykstra](https://github.com/tdykstra), [Rick Strahl](https://github.com/RickStrahl), [Novák Rossův](https://github.com/Tratcher), [Rick Anderson](https://twitter.com/RickAndMSFT), [sourabh Shirhatti](https://twitter.com/sshirhatti)a [Justin](https://github.com/jkotalik) Kotalik</span><span class="sxs-lookup"><span data-stu-id="ae9a6-108">By [Tom Dykstra](https://github.com/tdykstra), [Rick Strahl](https://github.com/RickStrahl), [Chris Ross](https://github.com/Tratcher), [Rick Anderson](https://twitter.com/RickAndMSFT), [Sourabh Shirhatti](https://twitter.com/sshirhatti), and [Justin Kotalik](https://github.com/jkotalik)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="ae9a6-109">Modul ASP.NET Core je nativním modulem služby IIS, který se připojuje k kanálu IIS na jednu z těchto:</span><span class="sxs-lookup"><span data-stu-id="ae9a6-109">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to either:</span></span>

* <span data-ttu-id="ae9a6-110">Hostování aplikace ASP.NET Core uvnitř pracovního procesu služby IIS ( `w3wp.exe` ), který se nazývá [model hostování v rámci procesu](#in-process-hosting-model).</span><span class="sxs-lookup"><span data-stu-id="ae9a6-110">Host an ASP.NET Core app inside of the IIS worker process (`w3wp.exe`), called the [in-process hosting model](#in-process-hosting-model).</span></span>
* <span data-ttu-id="ae9a6-111">Dopředné webové požadavky do back-endu ASP.NET Core aplikace, na které běží [Server Kestrel](xref:fundamentals/servers/kestrel), se nazývá [model hostování mimo proces](#out-of-process-hosting-model).</span><span class="sxs-lookup"><span data-stu-id="ae9a6-111">Forward web requests to a backend ASP.NET Core app running the [Kestrel server](xref:fundamentals/servers/kestrel), called the [out-of-process hosting model](#out-of-process-hosting-model).</span></span>

<span data-ttu-id="ae9a6-112">Podporované verze systému Windows:</span><span class="sxs-lookup"><span data-stu-id="ae9a6-112">Supported Windows versions:</span></span>

* <span data-ttu-id="ae9a6-113">Windows 7 nebo novější</span><span class="sxs-lookup"><span data-stu-id="ae9a6-113">Windows 7 or later</span></span>
* <span data-ttu-id="ae9a6-114">Windows Server 2008 R2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="ae9a6-114">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="ae9a6-115">Při hostování v procesu používá modul implementaci vnitroprocesového serveru pro službu IIS nazvanou IIS HTTP Server ( `IISHttpServer` ).</span><span class="sxs-lookup"><span data-stu-id="ae9a6-115">When hosting in-process, the module uses an in-process server implementation for IIS, called IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="ae9a6-116">Při hostování mimo proces funguje modul pouze s Kestrel.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-116">When hosting out-of-process, the module only works with Kestrel.</span></span> <span data-ttu-id="ae9a6-117">Modul nefunguje s [ovladačem HTTP. sys](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="ae9a6-117">The module doesn't function with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

## <a name="hosting-models"></a><span data-ttu-id="ae9a6-118">Modely hostingu</span><span class="sxs-lookup"><span data-stu-id="ae9a6-118">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="ae9a6-119">Model hostování v procesu</span><span class="sxs-lookup"><span data-stu-id="ae9a6-119">In-process hosting model</span></span>

<span data-ttu-id="ae9a6-120">ASP.NET Core aplikace jako výchozí pro model hostování v rámci procesu.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-120">ASP.NET Core apps default to the in-process hosting model.</span></span>

<span data-ttu-id="ae9a6-121">Následující vlastnosti platí při hostování v procesu:</span><span class="sxs-lookup"><span data-stu-id="ae9a6-121">The following characteristics apply when hosting in-process:</span></span>

* <span data-ttu-id="ae9a6-122">`IISHttpServer`Místo serveru [Kestrel](xref:fundamentals/servers/kestrel) se používá server HTTP služby IIS ().</span><span class="sxs-lookup"><span data-stu-id="ae9a6-122">IIS HTTP Server (`IISHttpServer`) is used instead of [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span> <span data-ttu-id="ae9a6-123">Pro vnitroprocesové [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) volání <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> :</span><span class="sxs-lookup"><span data-stu-id="ae9a6-123">For in-process, [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> to:</span></span>

  * <span data-ttu-id="ae9a6-124">Zaregistrujte `IISHttpServer` .</span><span class="sxs-lookup"><span data-stu-id="ae9a6-124">Register the `IISHttpServer`.</span></span>
  * <span data-ttu-id="ae9a6-125">Nakonfigurujte port a základní cestu, na kterých má Server naslouchat při spuštění za modulem ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-125">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
  * <span data-ttu-id="ae9a6-126">Nakonfigurujte hostitele tak, aby zachytával chyby při spuštění.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-126">Configure the host to capture startup errors.</span></span>

* <span data-ttu-id="ae9a6-127">[Atribut RequestTimeout](#attributes-of-the-aspnetcore-element) se nevztahuje na hostování v rámci procesu.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-127">The [requestTimeout attribute](#attributes-of-the-aspnetcore-element) doesn't apply to in-process hosting.</span></span>

* <span data-ttu-id="ae9a6-128">Sdílení fondu aplikací mezi aplikacemi není podporováno.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-128">Sharing an app pool among apps isn't supported.</span></span> <span data-ttu-id="ae9a6-129">Použijte jeden fond aplikací na aplikaci.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-129">Use one app pool per app.</span></span>

* <span data-ttu-id="ae9a6-130">Při použití [nasazení webu](/iis/publish/using-web-deploy/introduction-to-web-deploy) nebo ručního umístění [souboru App_offline. htm v nasazení](xref:host-and-deploy/iis/index#locked-deployment-files)se aplikace nemusí vypnout okamžitě, pokud dojde k otevřenému připojení.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-130">When using [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) or manually placing an [app_offline.htm file in the deployment](xref:host-and-deploy/iis/index#locked-deployment-files), the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="ae9a6-131">Připojení k protokolu WebSocket může například zpozdit ukončení aplikace.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-131">For example, a websocket connection may delay app shut down.</span></span>

* <span data-ttu-id="ae9a6-132">Architektura (bitová verze) aplikace a nainstalovaného modulu runtime (x64 nebo x86) se musí shodovat s architekturou fondu aplikací.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-132">The architecture (bitness) of the app and installed runtime (x64 or x86) must match the architecture of the app pool.</span></span>

* <span data-ttu-id="ae9a6-133">Zjistila se odpojení klienta.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-133">Client disconnects are detected.</span></span> <span data-ttu-id="ae9a6-134">Token zrušení [HttpContext. RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) je zrušený, když se klient odpojí.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-134">The [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) cancellation token is cancelled when the client disconnects.</span></span>

* <span data-ttu-id="ae9a6-135">V ASP.NET Core 2.2.1 nebo dřívější <xref:System.IO.Directory.GetCurrentDirectory*> vrátí pracovní adresář procesu spuštěného službou IIS místo adresáře aplikace (například *C:\Windows\System32\inetsrv* pro *W3wp. exe*).</span><span class="sxs-lookup"><span data-stu-id="ae9a6-135">In ASP.NET Core 2.2.1 or earlier, <xref:System.IO.Directory.GetCurrentDirectory*> returns the worker directory of the process started by IIS rather than the app's directory (for example, *C:\Windows\System32\inetsrv* for *w3wp.exe*).</span></span>

  <span data-ttu-id="ae9a6-136">Vzorový kód, který nastaví aktuální adresář aplikace, najdete v tématu [Třída CurrentDirectoryHelpers](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/3.x/CurrentDirectoryHelpers.cs).</span><span class="sxs-lookup"><span data-stu-id="ae9a6-136">For sample code that sets the app's current directory, see the [CurrentDirectoryHelpers class](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/3.x/CurrentDirectoryHelpers.cs).</span></span> <span data-ttu-id="ae9a6-137">Zavolejte `SetCurrentDirectory` metodu.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-137">Call the `SetCurrentDirectory` method.</span></span> <span data-ttu-id="ae9a6-138">Následná volání pro <xref:System.IO.Directory.GetCurrentDirectory*> poskytnutí adresáře aplikace.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-138">Subsequent calls to <xref:System.IO.Directory.GetCurrentDirectory*> provide the app's directory.</span></span>

* <span data-ttu-id="ae9a6-139">Při hostování v procesu <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> není volána interně pro inicializaci uživatele.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-139">When hosting in-process, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span> <span data-ttu-id="ae9a6-140">Proto <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementace používaná k transformaci deklarací po každém ověření není ve výchozím nastavení aktivována.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-140">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="ae9a6-141">Při transformaci deklarací s <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementací zavolejte <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> na přidat ověřovací služby:</span><span class="sxs-lookup"><span data-stu-id="ae9a6-141">When transforming claims with an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation, call <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> to add authentication services:</span></span>

  ```csharp
  public void ConfigureServices(IServiceCollection services)
  {
      services.AddTransient<IClaimsTransformation, ClaimsTransformer>();
      services.AddAuthentication(IISServerDefaults.AuthenticationScheme);
  }

  public void Configure(IApplicationBuilder app)
  {
      app.UseAuthentication();
  }
  ```
  
  * <span data-ttu-id="ae9a6-142">[Nasazení webového balíčku (v jednom souboru)](/aspnet/web-forms/overview/deployment/web-deployment-in-the-enterprise/deploying-web-packages) nejsou podporována.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-142">[Web Package (single-file) deployments](/aspnet/web-forms/overview/deployment/web-deployment-in-the-enterprise/deploying-web-packages) aren't supported.</span></span>

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="ae9a6-143">Model hostování mimo proces</span><span class="sxs-lookup"><span data-stu-id="ae9a6-143">Out-of-process hosting model</span></span>

<span data-ttu-id="ae9a6-144">Chcete-li nakonfigurovat aplikaci pro hostování mimo procesy, nastavte hodnotu `<AspNetCoreHostingModel>` vlastnosti na `OutOfProcess` soubor projektu (*. csproj*):</span><span class="sxs-lookup"><span data-stu-id="ae9a6-144">To configure an app for out-of-process hosting, set the value of the `<AspNetCoreHostingModel>` property to `OutOfProcess` in the project file (*.csproj*):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="ae9a6-145">Hostování v rámci procesu je nastaveno na `InProcess` , což je výchozí hodnota.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-145">In-process hosting is set with `InProcess`, which is the default value.</span></span>

<span data-ttu-id="ae9a6-146">Hodnota rozlišuje `<AspNetCoreHostingModel>` malá a velká písmena, takže `inprocess` a `outofprocess` jsou platné hodnoty.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-146">The value of `<AspNetCoreHostingModel>` is case insensitive, so `inprocess` and `outofprocess` are valid values.</span></span>

<span data-ttu-id="ae9a6-147">[Kestrel](xref:fundamentals/servers/kestrel) Místo http serveru IIS () se používá server Kestrel `IISHttpServer` .</span><span class="sxs-lookup"><span data-stu-id="ae9a6-147">[Kestrel](xref:fundamentals/servers/kestrel) server is used instead of IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="ae9a6-148">Pro mimoprocesové [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) volání <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> do:</span><span class="sxs-lookup"><span data-stu-id="ae9a6-148">For out-of-process, [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> to:</span></span>

* <span data-ttu-id="ae9a6-149">Nakonfigurujte port a základní cestu, na kterých má Server naslouchat při spuštění za modulem ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-149">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
* <span data-ttu-id="ae9a6-150">Nakonfigurujte hostitele tak, aby zachytával chyby při spuštění.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-150">Configure the host to capture startup errors.</span></span>

### <a name="hosting-model-changes"></a><span data-ttu-id="ae9a6-151">Změny modelu hostování</span><span class="sxs-lookup"><span data-stu-id="ae9a6-151">Hosting model changes</span></span>

<span data-ttu-id="ae9a6-152">Pokud `hostingModel` je nastavení změněno v souboru *Web. config* (vysvětleno v části [Konfigurace pomocí Web. config](#configuration-with-webconfig) ), modul recykluje pracovní proces pro službu IIS.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-152">If the `hostingModel` setting is changed in the *web.config* file (explained in the [Configuration with web.config](#configuration-with-webconfig) section), the module recycles the worker process for IIS.</span></span>

<span data-ttu-id="ae9a6-153">V případě IIS Express modul neprovádí recyklaci pracovního procesu, ale místo toho aktivuje bezproblémové vypnutí aktuálního procesu IIS Express.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-153">For IIS Express, the module doesn't recycle the worker process but instead triggers a graceful shutdown of the current IIS Express process.</span></span> <span data-ttu-id="ae9a6-154">Další požadavek na aplikaci vytvoří nový proces IIS Express.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-154">The next request to the app spawns a new IIS Express process.</span></span>

### <a name="process-name"></a><span data-ttu-id="ae9a6-155">Název procesu</span><span class="sxs-lookup"><span data-stu-id="ae9a6-155">Process name</span></span>

<span data-ttu-id="ae9a6-156">`Process.GetCurrentProcess().ProcessName`sestavy `w3wp` / `iisexpress` (v procesu) nebo `dotnet` (mimo proces).</span><span class="sxs-lookup"><span data-stu-id="ae9a6-156">`Process.GetCurrentProcess().ProcessName` reports `w3wp`/`iisexpress` (in-process) or `dotnet` (out-of-process).</span></span>

<span data-ttu-id="ae9a6-157">Mnoho nativních modulů, jako je ověřování systému Windows, zůstane aktivní.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-157">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="ae9a6-158">Další informace o aktivních modulech služby IIS pomocí modulu ASP.NET Core najdete v tématu <xref:host-and-deploy/iis/modules> .</span><span class="sxs-lookup"><span data-stu-id="ae9a6-158">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="ae9a6-159">Modul ASP.NET Core může také:</span><span class="sxs-lookup"><span data-stu-id="ae9a6-159">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="ae9a6-160">Nastavte proměnné prostředí pro pracovní proces.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-160">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="ae9a6-161">Protokoluje výstup stdout do úložiště souborů pro řešení problémů při spuštění.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-161">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="ae9a6-162">Předejte tokeny ověřování systému Windows.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-162">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="ae9a6-163">Jak nainstalovat a používat modul ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ae9a6-163">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="ae9a6-164">Pokyny k instalaci modulu ASP.NET Core najdete v tématu [instalace hostující sady .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span><span class="sxs-lookup"><span data-stu-id="ae9a6-164">For instructions on how to install the ASP.NET Core Module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="ae9a6-165">Konfigurace pomocí souboru Web. config</span><span class="sxs-lookup"><span data-stu-id="ae9a6-165">Configuration with web.config</span></span>

<span data-ttu-id="ae9a6-166">Modul ASP.NET Core je nakonfigurován s `aspNetCore` částí `system.webServer` uzlu v souboru *Web. config* webu.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-166">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="ae9a6-167">Následující soubor *Web. config* je publikován pro [nasazení závislé na rozhraní](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) a nakonfiguruje modul ASP.NET Core pro zpracování požadavků lokality:</span><span class="sxs-lookup"><span data-stu-id="ae9a6-167">The following *web.config* file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <location path="." inheritInChildApplications="false">
    <system.webServer>
      <handlers>
        <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModuleV2" resourceType="Unspecified" />
      </handlers>
      <aspNetCore processPath="dotnet"
                  arguments=".\MyApp.dll"
                  stdoutLogEnabled="false"
                  stdoutLogFile=".\logs\stdout"
                  hostingModel="inprocess" />
    </system.webServer>
  </location>
</configuration>
```

<span data-ttu-id="ae9a6-168">Následující soubor *Web. config* je publikován pro [samostatně uzavřené nasazení](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span><span class="sxs-lookup"><span data-stu-id="ae9a6-168">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <location path="." inheritInChildApplications="false">
    <system.webServer>
      <handlers>
        <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModuleV2" resourceType="Unspecified" />
      </handlers>
      <aspNetCore processPath=".\MyApp.exe"
                  stdoutLogEnabled="false"
                  stdoutLogFile=".\logs\stdout"
                  hostingModel="inprocess" />
    </system.webServer>
  </location>
</configuration>
```

<span data-ttu-id="ae9a6-169"><xref:System.Configuration.SectionInformation.InheritInChildApplications*>Vlastnost je nastavena na hodnotu `false` , která označuje, že nastavení zadaná v rámci [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) elementu nejsou děděna aplikacemi, které jsou umístěny v podadresáři aplikace.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-169">The <xref:System.Configuration.SectionInformation.InheritInChildApplications*> property is set to `false` to indicate that the settings specified within the [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) element aren't inherited by apps that reside in a subdirectory of the app.</span></span>

<span data-ttu-id="ae9a6-170">Když je aplikace nasazená na [Azure App Service](https://azure.microsoft.com/services/app-service/), `stdoutLogFile` cesta je nastavená na `\\?\%home%\LogFiles\stdout` .</span><span class="sxs-lookup"><span data-stu-id="ae9a6-170">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="ae9a6-171">Cesta ukládá protokoly stdout do složky *soubory protokolů* , což je umístění, které služba automaticky vytvořila.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-171">The path saves stdout logs to the *LogFiles* folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="ae9a6-172">Informace o konfiguraci dílčí aplikace IIS najdete v tématu <xref:host-and-deploy/iis/index#sub-applications> .</span><span class="sxs-lookup"><span data-stu-id="ae9a6-172">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="ae9a6-173">Atributy elementu aspNetCore</span><span class="sxs-lookup"><span data-stu-id="ae9a6-173">Attributes of the aspNetCore element</span></span>

| <span data-ttu-id="ae9a6-174">Atribut</span><span class="sxs-lookup"><span data-stu-id="ae9a6-174">Attribute</span></span> | <span data-ttu-id="ae9a6-175">Description</span><span class="sxs-lookup"><span data-stu-id="ae9a6-175">Description</span></span> | <span data-ttu-id="ae9a6-176">Výchozí</span><span class="sxs-lookup"><span data-stu-id="ae9a6-176">Default</span></span> |
| ---
<span data-ttu-id="ae9a6-177">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ae9a6-177">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ae9a6-178">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ae9a6-178">'Blazor'</span></span>
- <span data-ttu-id="ae9a6-179">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ae9a6-179">'Identity'</span></span>
- <span data-ttu-id="ae9a6-180">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ae9a6-180">'Let's Encrypt'</span></span>
- <span data-ttu-id="ae9a6-181">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ae9a6-181">'Razor'</span></span>
- <span data-ttu-id="ae9a6-182">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="ae9a6-182">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ae9a6-183">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ae9a6-183">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ae9a6-184">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ae9a6-184">'Blazor'</span></span>
- <span data-ttu-id="ae9a6-185">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ae9a6-185">'Identity'</span></span>
- <span data-ttu-id="ae9a6-186">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ae9a6-186">'Let's Encrypt'</span></span>
- <span data-ttu-id="ae9a6-187">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ae9a6-187">'Razor'</span></span>
- <span data-ttu-id="ae9a6-188">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="ae9a6-188">'SignalR' uid:</span></span> 

<span data-ttu-id="ae9a6-189">----- | ---Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ae9a6-189">----- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ae9a6-190">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ae9a6-190">'Blazor'</span></span>
- <span data-ttu-id="ae9a6-191">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ae9a6-191">'Identity'</span></span>
- <span data-ttu-id="ae9a6-192">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ae9a6-192">'Let's Encrypt'</span></span>
- <span data-ttu-id="ae9a6-193">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ae9a6-193">'Razor'</span></span>
- <span data-ttu-id="ae9a6-194">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="ae9a6-194">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ae9a6-195">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ae9a6-195">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ae9a6-196">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ae9a6-196">'Blazor'</span></span>
- <span data-ttu-id="ae9a6-197">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ae9a6-197">'Identity'</span></span>
- <span data-ttu-id="ae9a6-198">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ae9a6-198">'Let's Encrypt'</span></span>
- <span data-ttu-id="ae9a6-199">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ae9a6-199">'Razor'</span></span>
- <span data-ttu-id="ae9a6-200">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="ae9a6-200">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ae9a6-201">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ae9a6-201">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ae9a6-202">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ae9a6-202">'Blazor'</span></span>
- <span data-ttu-id="ae9a6-203">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ae9a6-203">'Identity'</span></span>
- <span data-ttu-id="ae9a6-204">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ae9a6-204">'Let's Encrypt'</span></span>
- <span data-ttu-id="ae9a6-205">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ae9a6-205">'Razor'</span></span>
- <span data-ttu-id="ae9a6-206">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="ae9a6-206">'SignalR' uid:</span></span> 

<span data-ttu-id="ae9a6-207">------ | :-----: | | `arguments` | </span><span class="sxs-lookup"><span data-stu-id="ae9a6-207">------ | :-----: | | `arguments` | </span></span><p><span data-ttu-id="ae9a6-208">Volitelný řetězcový atribut.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-208">Optional string attribute.</span></span></p><p><span data-ttu-id="ae9a6-209">Argumenty ke spustitelnému souboru určenému v **processPath**.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-209">Arguments to the executable specified in **processPath**.</span></span></p> <span data-ttu-id="ae9a6-210">| | | `disableStartUpErrorPage` | </span><span class="sxs-lookup"><span data-stu-id="ae9a6-210">| | | `disableStartUpErrorPage` | </span></span><p><span data-ttu-id="ae9a6-211">Volitelný atribut typu Boolean.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-211">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="ae9a6-212">Pokud je nastaveno na true, stránka **502,5 – selhání procesu** se potlačí a stavová stránka 502 konfigurovaná v *souboru Web. config* má přednost.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-212">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> <span data-ttu-id="ae9a6-213">| `false` | | `forwardWindowsAuthToken` | </span><span class="sxs-lookup"><span data-stu-id="ae9a6-213">| `false` | | `forwardWindowsAuthToken` | </span></span><p><span data-ttu-id="ae9a6-214">Volitelný atribut typu Boolean.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-214">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="ae9a6-215">Pokud je nastaveno na true, token se přepošle podřízenému procesu, který naslouchá na% ASPNETCORE_PORT% jako záhlaví MS-ASPNETCORE-WINAUTHTOKEN na žádost.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-215">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="ae9a6-216">Je zodpovědností za tento proces volání CloseHandle na tento token na žádost.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-216">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> <span data-ttu-id="ae9a6-217">| `true` | | `hostingModel` | </span><span class="sxs-lookup"><span data-stu-id="ae9a6-217">| `true` | | `hostingModel` | </span></span><p><span data-ttu-id="ae9a6-218">Volitelný řetězcový atribut.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-218">Optional string attribute.</span></span></p><p><span data-ttu-id="ae9a6-219">Určuje model hostování jako vnitroprocesové ( `InProcess` / `inprocess` ) nebo mimo proces ( `OutOfProcess` / `outofprocess` ).</span><span class="sxs-lookup"><span data-stu-id="ae9a6-219">Specifies the hosting model as in-process (`InProcess`/`inprocess`) or out-of-process (`OutOfProcess`/`outofprocess`).</span></span></p> | `InProcess`<br><span data-ttu-id="ae9a6-220">`inprocess` | | `processesPerApplication` | </span><span class="sxs-lookup"><span data-stu-id="ae9a6-220">`inprocess` | | `processesPerApplication` | </span></span><p><span data-ttu-id="ae9a6-221">Volitelný celočíselný atribut</span><span class="sxs-lookup"><span data-stu-id="ae9a6-221">Optional integer attribute.</span></span></p><p><span data-ttu-id="ae9a6-222">Určuje počet instancí procesu určeného v nastavení **processPath** , které lze v rámci aplikace vystavit.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-222">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="ae9a6-223">&dagger;Pro hostování v rámci procesu je hodnota omezená na `1` .</span><span class="sxs-lookup"><span data-stu-id="ae9a6-223">&dagger;For in-process hosting, the value is limited to `1`.</span></span></p><p><span data-ttu-id="ae9a6-224">Nastavení `processesPerApplication` se nedoporučuje.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-224">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="ae9a6-225">Tento atribut bude v budoucí verzi odebrán.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-225">This attribute will be removed in a future release.</span></span></p> <span data-ttu-id="ae9a6-226">| Výchozí`1`</span><span class="sxs-lookup"><span data-stu-id="ae9a6-226">| Default: `1`</span></span><br><span data-ttu-id="ae9a6-227">Dlouhé`1`</span><span class="sxs-lookup"><span data-stu-id="ae9a6-227">Min: `1`</span></span><br><span data-ttu-id="ae9a6-228">Max: `100` &dagger; | |`processPath` | </span><span class="sxs-lookup"><span data-stu-id="ae9a6-228">Max: `100`&dagger; | | `processPath` | </span></span><p><span data-ttu-id="ae9a6-229">Povinný atribut řetězce.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-229">Required string attribute.</span></span></p><p><span data-ttu-id="ae9a6-230">Cesta ke spustitelnému souboru, který spouští proces naslouchající na požadavky HTTP.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-230">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="ae9a6-231">Jsou podporovány relativní cesty.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-231">Relative paths are supported.</span></span> <span data-ttu-id="ae9a6-232">Pokud cesta začíná `.` , bude tato cesta považována za relativní vzhledem k kořenu webu.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-232">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> <span data-ttu-id="ae9a6-233">| | | `rapidFailsPerMinute` | </span><span class="sxs-lookup"><span data-stu-id="ae9a6-233">| | | `rapidFailsPerMinute` | </span></span><p><span data-ttu-id="ae9a6-234">Volitelný celočíselný atribut</span><span class="sxs-lookup"><span data-stu-id="ae9a6-234">Optional integer attribute.</span></span></p><p><span data-ttu-id="ae9a6-235">Určuje, kolikrát je povoleno selhání procesu určeného v **processPath** za minutu.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-235">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="ae9a6-236">Pokud je tento limit překročen, modul ukončí spuštění procesu po zbytek minuty.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-236">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p><p><span data-ttu-id="ae9a6-237">Nepodporováno v hostování v rámci procesu.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-237">Not supported with in-process hosting.</span></span></p> <span data-ttu-id="ae9a6-238">| Výchozí`10`</span><span class="sxs-lookup"><span data-stu-id="ae9a6-238">| Default: `10`</span></span><br><span data-ttu-id="ae9a6-239">Dlouhé`0`</span><span class="sxs-lookup"><span data-stu-id="ae9a6-239">Min: `0`</span></span><br><span data-ttu-id="ae9a6-240">Max: `100` | |`requestTimeout` | </span><span class="sxs-lookup"><span data-stu-id="ae9a6-240">Max: `100` | | `requestTimeout` | </span></span><p><span data-ttu-id="ae9a6-241">Volitelný atribut TimeSpan.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-241">Optional timespan attribute.</span></span></p><p><span data-ttu-id="ae9a6-242">Určuje dobu, po kterou modul ASP.NET Core čeká na odpověď od procesu, který naslouchá na% ASPNETCORE_PORT%.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-242">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="ae9a6-243">Ve verzích modulu ASP.NET Core, které byly dodávány s vydáním ASP.NET Core 2,1 nebo vyšší, `requestTimeout` je určena v hodinách, minutách a sekundách.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-243">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p><p><span data-ttu-id="ae9a6-244">Neplatí pro hostování v procesu.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-244">Doesn't apply to in-process hosting.</span></span> <span data-ttu-id="ae9a6-245">Pro hostování v rámci procesu modul čeká, až aplikace zpracuje požadavek.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-245">For in-process hosting, the module waits for the app to process the request.</span></span></p><p><span data-ttu-id="ae9a6-246">Platné hodnoty segmentů minut a sekund řetězce jsou v rozsahu 0-59.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-246">Valid values for minutes and seconds segments of the string are in the range 0-59.</span></span> <span data-ttu-id="ae9a6-247">Při použití hodnoty **60** v hodnotě minut nebo sekund dojde k chybě systému *500 – interní chyba serveru*.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-247">Use of **60** in the value for minutes or seconds results in a *500 - Internal Server Error*.</span></span></p> <span data-ttu-id="ae9a6-248">| Výchozí`00:02:00`</span><span class="sxs-lookup"><span data-stu-id="ae9a6-248">| Default: `00:02:00`</span></span><br><span data-ttu-id="ae9a6-249">Dlouhé`00:00:00`</span><span class="sxs-lookup"><span data-stu-id="ae9a6-249">Min: `00:00:00`</span></span><br><span data-ttu-id="ae9a6-250">Max: `360:00:00` | |`shutdownTimeLimit` | </span><span class="sxs-lookup"><span data-stu-id="ae9a6-250">Max: `360:00:00` | | `shutdownTimeLimit` | </span></span><p><span data-ttu-id="ae9a6-251">Volitelný celočíselný atribut</span><span class="sxs-lookup"><span data-stu-id="ae9a6-251">Optional integer attribute.</span></span></p><p><span data-ttu-id="ae9a6-252">Doba v sekundách, po kterou modul čeká na řádné vypnutí spustitelného souboru, když se zjistí soubor *App_offline. htm* .</span><span class="sxs-lookup"><span data-stu-id="ae9a6-252">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> <span data-ttu-id="ae9a6-253">| Výchozí`10`</span><span class="sxs-lookup"><span data-stu-id="ae9a6-253">| Default: `10`</span></span><br><span data-ttu-id="ae9a6-254">Dlouhé`0`</span><span class="sxs-lookup"><span data-stu-id="ae9a6-254">Min: `0`</span></span><br><span data-ttu-id="ae9a6-255">Max: `600` | |`startupTimeLimit` | </span><span class="sxs-lookup"><span data-stu-id="ae9a6-255">Max: `600` | | `startupTimeLimit` | </span></span><p><span data-ttu-id="ae9a6-256">Volitelný celočíselný atribut</span><span class="sxs-lookup"><span data-stu-id="ae9a6-256">Optional integer attribute.</span></span></p><p><span data-ttu-id="ae9a6-257">Doba v sekundách, kterou modul počká, než spustitelný soubor spustí proces, který na portu naslouchá.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-257">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="ae9a6-258">Pokud je tento časový limit překročen, modul proces ukončuje.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-258">If this time limit is exceeded, the module kills the process.</span></span> <span data-ttu-id="ae9a6-259">Modul se pokusí znovu spustit proces, když obdrží novou žádost, a pokračuje v pokusu o restartování procesu u dalších příchozích požadavků, pokud se nepodaří spustit **rapidFailsPerMinute** počet pokusů během poslední minuty.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-259">The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="ae9a6-260">Hodnota 0 (nula **) není považována za** nekonečný časový limit.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-260">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> <span data-ttu-id="ae9a6-261">| Výchozí`120`</span><span class="sxs-lookup"><span data-stu-id="ae9a6-261">| Default: `120`</span></span><br><span data-ttu-id="ae9a6-262">Dlouhé`0`</span><span class="sxs-lookup"><span data-stu-id="ae9a6-262">Min: `0`</span></span><br><span data-ttu-id="ae9a6-263">Max: `3600` | |`stdoutLogEnabled` | </span><span class="sxs-lookup"><span data-stu-id="ae9a6-263">Max: `3600` | | `stdoutLogEnabled` | </span></span><p><span data-ttu-id="ae9a6-264">Volitelný atribut typu Boolean.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-264">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="ae9a6-265">Je-li nastavena hodnota true, **stdout** a **stderr** pro proces zadaný v **processPath** budou přesměrovány do souboru zadaného v **stdoutLogFile**.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-265">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> <span data-ttu-id="ae9a6-266">| `false` | | `stdoutLogFile` | </span><span class="sxs-lookup"><span data-stu-id="ae9a6-266">| `false` | | `stdoutLogFile` | </span></span><p><span data-ttu-id="ae9a6-267">Volitelný řetězcový atribut.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-267">Optional string attribute.</span></span></p><p><span data-ttu-id="ae9a6-268">Určuje relativní nebo absolutní cestu k souboru, pro který je **stdout** a **stderr** z procesu určeného v **processPath** protokolováno.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-268">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="ae9a6-269">Relativní cesty jsou relativní vzhledem k kořenu webu.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-269">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="ae9a6-270">Každá cesta začínající řetězcem `.` je relativní vzhledem k kořenu webu a všechny ostatní cesty se považují za absolutní cesty.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-270">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="ae9a6-271">Při vytvoření souboru protokolu se v modulu vytvoří všechny složky, které jsou v cestě zadané.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-271">Any folders provided in the path are created by the module when the log file is created.</span></span> <span data-ttu-id="ae9a6-272">Pomocí oddělovačů podtržítka se do posledního segmentu cesty **stdoutLogFile** přidá časové razítko, ID procesu a Přípona souboru (*. log*).</span><span class="sxs-lookup"><span data-stu-id="ae9a6-272">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="ae9a6-273">Pokud `.\logs\stdout` je zadán jako hodnota, je ukázkový protokol stdout uložen jako *stdout_20180205194132_1934. log* ve složce *logs* při uložení na 2/5/2018 v 19:41:32 s ID procesu 1934.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-273">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

### <a name="set-environment-variables"></a><span data-ttu-id="ae9a6-274">Nastavení proměnných prostředí</span><span class="sxs-lookup"><span data-stu-id="ae9a6-274">Set environment variables</span></span>

<span data-ttu-id="ae9a6-275">Proměnné prostředí lze zadat pro proces v `processPath` atributu.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-275">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="ae9a6-276">Zadejte proměnnou prostředí s `<environmentVariable>` podřízeným elementem `<environmentVariables>` elementu kolekce.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-276">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span> <span data-ttu-id="ae9a6-277">Proměnné prostředí nastavené v této části mají přednost před proměnnými prostředí systému.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-277">Environment variables set in this section take precedence over system environment variables.</span></span>

<span data-ttu-id="ae9a6-278">Následující příklad nastaví dvě proměnné prostředí v *souboru Web. config*. `ASPNETCORE_ENVIRONMENT`nakonfiguruje prostředí aplikace na `Development` .</span><span class="sxs-lookup"><span data-stu-id="ae9a6-278">The following example sets two environment variables in *web.config*. `ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="ae9a6-279">Vývojář může tuto hodnotu dočasně nastavit v souboru *Web. config* , aby bylo možné vynutit, aby se [Stránka s výjimkou vývojářů](xref:fundamentals/error-handling) načetla při ladění výjimky aplikace.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-279">A developer may temporarily set this value in the *web.config* file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="ae9a6-280">`CONFIG_DIR`je příkladem uživatelsky definované proměnné prostředí, kde vývojář napsal kód, který přečte hodnotu při spuštění, aby vytvořil cestu pro načtení konfiguračního souboru aplikace.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-280">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile=".\logs\stdout"
      hostingModel="inprocess">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
    <environmentVariable name="CONFIG_DIR" value="f:\application_config" />
  </environmentVariables>
</aspNetCore>
```

> [!NOTE]
> <span data-ttu-id="ae9a6-281">Alternativou k nastavení prostředí přímo v souboru *Web. config* je zahrnutí `<EnvironmentName>` vlastnosti do [profilu publikování (. pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) nebo souboru projektu.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-281">An alternative to setting the environment directly in *web.config* is to include the `<EnvironmentName>` property in the [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) or project file.</span></span> <span data-ttu-id="ae9a6-282">Tento přístup nastaví prostředí v *souboru Web. config* při publikování projektu:</span><span class="sxs-lookup"><span data-stu-id="ae9a6-282">This approach sets the environment in *web.config* when the project is published:</span></span>
>
> ```xml
> <PropertyGroup>
>   <EnvironmentName>Development</EnvironmentName>
> </PropertyGroup>
> ```

> [!WARNING]
> <span data-ttu-id="ae9a6-283">Nastavte pouze `ASPNETCORE_ENVIRONMENT` proměnnou prostředí na `Development` pracovní a testovací servery, které nejsou přístupné nedůvěryhodným sítím, jako je například Internet.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-283">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## <a name="app_offlinehtm"></a><span data-ttu-id="ae9a6-284">app_offline. htm</span><span class="sxs-lookup"><span data-stu-id="ae9a6-284">app_offline.htm</span></span>

<span data-ttu-id="ae9a6-285">Pokud se v kořenovém adresáři aplikace zjistí soubor s názvem *App_offline. htm* , modul ASP.NET Core se pokusí aplikaci řádně vypnout a zastavit zpracování příchozích požadavků.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-285">If a file with the name *app_offline.htm* is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="ae9a6-286">Pokud aplikace běží i po uplynutí počtu sekund definovaného v `shutdownTimeLimit` , modul ASP.NET Core ukončuje běžící proces.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-286">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="ae9a6-287">Když je přítomen soubor *App_offline. htm* , ASP.NET Core modul reaguje na požadavky odesláním zpět obsahu souboru *App_offline. htm* .</span><span class="sxs-lookup"><span data-stu-id="ae9a6-287">While the *app_offline.htm* file is present, the ASP.NET Core Module responds to requests by sending back the contents of the *app_offline.htm* file.</span></span> <span data-ttu-id="ae9a6-288">Po odebrání souboru *App_offline. htm* aplikace spustí další požadavek.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-288">When the *app_offline.htm* file is removed, the next request starts the app.</span></span>

<span data-ttu-id="ae9a6-289">Při použití nezpracovaného modelu hostování se aplikace nemusí okamžitě vypnout, pokud dojde k otevřenému připojení.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-289">When using the out-of-process hosting model, the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="ae9a6-290">Připojení k protokolu WebSocket může například zpozdit ukončení aplikace.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-290">For example, a websocket connection may delay app shut down.</span></span>

## <a name="start-up-error-page"></a><span data-ttu-id="ae9a6-291">Spouštěcí chybová stránka</span><span class="sxs-lookup"><span data-stu-id="ae9a6-291">Start-up error page</span></span>

<span data-ttu-id="ae9a6-292">Hostování v procesu i mimo procesy vytváří vlastní chybové stránky, když se nepodaří aplikaci spustit.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-292">Both in-process and out-of-process hosting produce custom error pages when they fail to start the app.</span></span>

<span data-ttu-id="ae9a6-293">Pokud modul ASP.NET Core nenalezne obslužnou rutinu žádosti v procesu nebo mimo proces 500,0, zobrazí se stránka s kódem stavu *selhání načtení obslužné rutiny v* procesu nebo mimo proces.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-293">If the ASP.NET Core Module fails to find either the in-process or out-of-process request handler, a *500.0 - In-Process/Out-Of-Process Handler Load Failure* status code page appears.</span></span>

<span data-ttu-id="ae9a6-294">Pro vnitroprocesové hostování v případě, že se modul ASP.NET Core nepodaří spustit aplikaci, zobrazí se stránka s kódem stavu *chyby při spuštění 500,30* .</span><span class="sxs-lookup"><span data-stu-id="ae9a6-294">For in-process hosting if the ASP.NET Core Module fails to start the app, a *500.30 - Start Failure* status code page appears.</span></span>

<span data-ttu-id="ae9a6-295">Pro hostování mimo procesy, pokud se ASP.NET Core modulu nespustí back-end proces, nebo dojde k selhání procesu back-endu, ale nenaslouchá na konfigurovaném portu, zobrazí se stránka s kódem stavu *selhání procesu 502,5* .</span><span class="sxs-lookup"><span data-stu-id="ae9a6-295">For out-of-process hosting if the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span>

<span data-ttu-id="ae9a6-296">Chcete-li tuto stránku potlačit a vrátit se na výchozí stavovou stránku služby IIS 5xx, použijte `disableStartUpErrorPage` atribut.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-296">To suppress this page and revert to the default IIS 5xx status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="ae9a6-297">Další informace o konfiguraci vlastních chybových zpráv najdete v tématu [chyby \<httpErrors> protokolu HTTP ](/iis/configuration/system.webServer/httpErrors/).</span><span class="sxs-lookup"><span data-stu-id="ae9a6-297">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

## <a name="log-creation-and-redirection"></a><span data-ttu-id="ae9a6-298">Vytvoření a přesměrování protokolu</span><span class="sxs-lookup"><span data-stu-id="ae9a6-298">Log creation and redirection</span></span>

<span data-ttu-id="ae9a6-299">Modul ASP.NET Core přesměruje výstup konzoly stdout a stderr na disk, pokud `stdoutLogEnabled` `stdoutLogFile` `aspNetCore` jsou nastaveny atributy a elementu.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-299">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="ae9a6-300">`stdoutLogFile`Při vytvoření souboru protokolu jsou všechny složky v této cestě vytvářeny modulem.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-300">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="ae9a6-301">Fond aplikací musí mít oprávnění k zápisu do umístění, kam se protokoly zapisují (použijte `IIS AppPool\<app_pool_name>` k poskytnutí oprávnění k zápisu).</span><span class="sxs-lookup"><span data-stu-id="ae9a6-301">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="ae9a6-302">Protokoly nejsou otočeny, pokud nedochází k recyklování/restartování procesu.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-302">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="ae9a6-303">Je zodpovědností hostitele omezit místo na disku, které používají protokoly.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-303">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="ae9a6-304">Použití protokolu stdout se doporučuje pouze při řešení potíží se spouštěním aplikací při hostování služby IIS nebo při použití [podpory v době vývoje služby IIS se sadou Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), nikoli při ladění místně a při spuštění aplikace s IIS Express.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-304">Using the stdout log is only recommended for troubleshooting app startup issues when hosting on IIS or when using [development-time support for IIS with Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), not while debugging locally and running the app with IIS Express.</span></span>

<span data-ttu-id="ae9a6-305">Nepoužívejte protokol stdout pro obecné účely protokolování aplikace.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-305">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="ae9a6-306">Pro rutinu protokolování v aplikaci ASP.NET Core použijte knihovnu protokolování, která omezuje velikost souboru protokolu a otočí protokoly.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-306">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="ae9a6-307">Další informace najdete v tématu [Zprostředkovatelé protokolování třetích stran](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="ae9a6-307">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="ae9a6-308">Časové razítko a Přípona souboru se automaticky přidají při vytvoření souboru protokolu.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-308">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="ae9a6-309">Název souboru protokolu se skládá připojením časového razítka, ID procesu a přípony souboru (*. log*) k poslednímu segmentu `stdoutLogFile` cesty (obvykle *stdout*), oddělený podtržítky.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-309">The log file name is composed by appending the timestamp, process ID, and file extension (*.log*) to the last segment of the `stdoutLogFile` path (typically *stdout*) delimited by underscores.</span></span> <span data-ttu-id="ae9a6-310">Pokud `stdoutLogFile` cesta končí na *stdout*, protokol pro aplikaci s číslem PID 1934 vytvořeným v 2/5/2018 v 19:42:32 má název souboru *stdout_20180205194132_1934. log*.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-310">If the `stdoutLogFile` path ends with *stdout*, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name *stdout_20180205194132_1934.log*.</span></span>

<span data-ttu-id="ae9a6-311">Pokud `stdoutLogEnabled` je hodnota false, chyby, ke kterým dochází při spuštění aplikace, se zaznamenávají a generují do protokolu událostí až do 30 kB.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-311">If `stdoutLogEnabled` is false, errors that occur on app startup are captured and emitted to the event log up to 30 KB.</span></span> <span data-ttu-id="ae9a6-312">Po spuštění budou všechny další protokoly zahozeny.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-312">After startup, all additional logs are discarded.</span></span>

<span data-ttu-id="ae9a6-313">Následující vzorový `aspNetCore` prvek nakonfiguruje protokolování stdout na relativní cestě `.\log\` .</span><span class="sxs-lookup"><span data-stu-id="ae9a6-313">The following sample `aspNetCore` element configures stdout logging at the relative path `.\log\`.</span></span> <span data-ttu-id="ae9a6-314">Potvrďte, že identita uživatele fondu aplikací má oprávnění k zápisu do zadané cesty.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-314">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout"
    hostingModel="inprocess">
</aspNetCore>
```

<span data-ttu-id="ae9a6-315">Když publikujete aplikaci pro nasazení Azure App Service, sada web SDK nastaví `stdoutLogFile` hodnotu na `\\?\%home%\LogFiles\stdout` .</span><span class="sxs-lookup"><span data-stu-id="ae9a6-315">When publishing an app for Azure App Service deployment, the Web SDK sets the `stdoutLogFile` value to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="ae9a6-316">`%home`Proměnná prostředí je předdefinovaná pro aplikace hostované v Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-316">The `%home` environment variable is predefined for apps hosted by Azure App Service.</span></span>

<span data-ttu-id="ae9a6-317">Chcete-li vytvořit pravidla filtru protokolování, přečtěte si část [Konfigurace](xref:fundamentals/logging/index#log-filtering) a [filtrování protokolů](xref:fundamentals/logging/index#log-filtering) v dokumentaci ASP.NET Core protokolování.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-317">To create logging filter rules, see the [Configuration](xref:fundamentals/logging/index#log-filtering) and [Log filtering](xref:fundamentals/logging/index#log-filtering) sections of the ASP.NET Core logging documentation.</span></span>

<span data-ttu-id="ae9a6-318">Další informace o formátech cest najdete v tématu [formáty souborů cest v systémech Windows](/dotnet/standard/io/file-path-formats).</span><span class="sxs-lookup"><span data-stu-id="ae9a6-318">For more information on path formats, see [File path formats on Windows systems](/dotnet/standard/io/file-path-formats).</span></span>

## <a name="enhanced-diagnostic-logs"></a><span data-ttu-id="ae9a6-319">Rozšířené diagnostické protokoly</span><span class="sxs-lookup"><span data-stu-id="ae9a6-319">Enhanced diagnostic logs</span></span>

<span data-ttu-id="ae9a6-320">Modul ASP.NET Core lze nakonfigurovat tak, aby poskytoval rozšířené diagnostické protokoly.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-320">The ASP.NET Core Module is configurable to provide enhanced diagnostics logs.</span></span> <span data-ttu-id="ae9a6-321">Přidejte `<handlerSettings>` element do `<aspNetCore>` elementu v *souboru Web. config*. Nastavení `debugLevel` pro `TRACE` zpřístupňuje vyšší věrnost diagnostických informací:</span><span class="sxs-lookup"><span data-stu-id="ae9a6-321">Add the `<handlerSettings>` element to the `<aspNetCore>` element in *web.config*. Setting the `debugLevel` to `TRACE` exposes a higher fidelity of diagnostic information:</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="inprocess">
  <handlerSettings>
    <handlerSetting name="debugFile" value=".\logs\aspnetcore-debug.log" />
    <handlerSetting name="debugLevel" value="FILE,TRACE" />
  </handlerSettings>
</aspNetCore>
```

<span data-ttu-id="ae9a6-322">Všechny složky v cestě (*protokoly* v předchozím příkladu) se vytvoří modulem, když se vytvoří soubor protokolu.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-322">Any folders in the path (*logs* in the preceding example) are created by the module when the log file is created.</span></span> <span data-ttu-id="ae9a6-323">Fond aplikací musí mít oprávnění k zápisu do umístění, kam se protokoly zapisují (použijte `IIS AppPool\<app_pool_name>` k poskytnutí oprávnění k zápisu).</span><span class="sxs-lookup"><span data-stu-id="ae9a6-323">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="ae9a6-324">Hodnoty úrovně ladění ( `debugLevel` ) mohou zahrnovat jak úroveň, tak i umístění.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-324">Debug level (`debugLevel`) values can include both the level and the location.</span></span>

<span data-ttu-id="ae9a6-325">Úrovně (v pořadí od nejméně po nejpodrobnější):</span><span class="sxs-lookup"><span data-stu-id="ae9a6-325">Levels (in order from least to most verbose):</span></span>

* <span data-ttu-id="ae9a6-326">ERROR</span><span class="sxs-lookup"><span data-stu-id="ae9a6-326">ERROR</span></span>
* <span data-ttu-id="ae9a6-327">WARNING</span><span class="sxs-lookup"><span data-stu-id="ae9a6-327">WARNING</span></span>
* <span data-ttu-id="ae9a6-328">PŘÍJEMCE</span><span class="sxs-lookup"><span data-stu-id="ae9a6-328">INFO</span></span>
* <span data-ttu-id="ae9a6-329">TRACE</span><span class="sxs-lookup"><span data-stu-id="ae9a6-329">TRACE</span></span>

<span data-ttu-id="ae9a6-330">Umístění (je povoleno více umístění):</span><span class="sxs-lookup"><span data-stu-id="ae9a6-330">Locations (multiple locations are permitted):</span></span>

* <span data-ttu-id="ae9a6-331">STROMU</span><span class="sxs-lookup"><span data-stu-id="ae9a6-331">CONSOLE</span></span>
* <span data-ttu-id="ae9a6-332">EVENTLOG</span><span class="sxs-lookup"><span data-stu-id="ae9a6-332">EVENTLOG</span></span>
* <span data-ttu-id="ae9a6-333">SOUBOR</span><span class="sxs-lookup"><span data-stu-id="ae9a6-333">FILE</span></span>

<span data-ttu-id="ae9a6-334">Nastavení obslužné rutiny lze také poskytnout pomocí proměnných prostředí:</span><span class="sxs-lookup"><span data-stu-id="ae9a6-334">The handler settings can also be provided via environment variables:</span></span>

* <span data-ttu-id="ae9a6-335">`ASPNETCORE_MODULE_DEBUG_FILE`: Cesta k souboru protokolu ladění.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-335">`ASPNETCORE_MODULE_DEBUG_FILE`: Path to the debug log file.</span></span> <span data-ttu-id="ae9a6-336">(Výchozí: *aspnetcore-Debug. log*)</span><span class="sxs-lookup"><span data-stu-id="ae9a6-336">(Default: *aspnetcore-debug.log*)</span></span>
* <span data-ttu-id="ae9a6-337">`ASPNETCORE_MODULE_DEBUG`: Nastavení úrovně ladění.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-337">`ASPNETCORE_MODULE_DEBUG`: Debug level setting.</span></span>

> [!WARNING]
> <span data-ttu-id="ae9a6-338">Nenechávejte **protokolování** ladění povolené v nasazení po delší dobu, než je potřeba k řešení problému.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-338">Do **not** leave debug logging enabled in the deployment for longer than required to troubleshoot an issue.</span></span> <span data-ttu-id="ae9a6-339">Velikost protokolu není omezená.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-339">The size of the log isn't limited.</span></span> <span data-ttu-id="ae9a6-340">Když necháte protokol ladění povolený, může dojít k vyčerpání dostupného místa na disku a selhání serveru nebo služby App Service.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-340">Leaving the debug log enabled can exhaust the available disk space and crash the server or app service.</span></span>

<span data-ttu-id="ae9a6-341">Příklad [Configuration with web.config](#configuration-with-webconfig) `aspNetCore` elementu v souboru *Web. config* naleznete v tématu Konfigurace pomocí souboru Web. config.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-341">See [Configuration with web.config](#configuration-with-webconfig) for an example of the `aspNetCore` element in the *web.config* file.</span></span>

## <a name="modify-the-stack-size"></a><span data-ttu-id="ae9a6-342">Úprava velikosti zásobníku</span><span class="sxs-lookup"><span data-stu-id="ae9a6-342">Modify the stack size</span></span>

<span data-ttu-id="ae9a6-343">*Platí pouze při použití modelu hostování v rámci procesu.*</span><span class="sxs-lookup"><span data-stu-id="ae9a6-343">*Only applies when using the in-process hosting model.*</span></span>

<span data-ttu-id="ae9a6-344">Nakonfigurujte velikost spravovaného zásobníku pomocí `stackSize` nastavení v bajtech v *souboru Web. config*. Výchozí velikost je `1048576` bajtů (1 MB).</span><span class="sxs-lookup"><span data-stu-id="ae9a6-344">Configure the managed stack size using the `stackSize` setting in bytes in *web.config*. The default size is `1048576` bytes (1 MB).</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="inprocess">
  <handlerSettings>
    <handlerSetting name="stackSize" value="2097152" />
  </handlerSettings>
</aspNetCore>
```

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="ae9a6-345">Konfigurace proxy serveru používá protokol HTTP a token párování.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-345">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="ae9a6-346">*Platí pouze pro hostování mimo proces.*</span><span class="sxs-lookup"><span data-stu-id="ae9a6-346">*Only applies to out-of-process hosting.*</span></span>

<span data-ttu-id="ae9a6-347">Proxy vytvořený mezi modulem ASP.NET Core a Kestrel používá protokol HTTP.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-347">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="ae9a6-348">Nehrozí riziko odposlouchávání provozu mezi modulem a Kestrel z umístění od serveru.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-348">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="ae9a6-349">Párovací token se používá k zajištění, že žádosti přijaté službou Kestrel byly proxy službou IIS a nepocházejí z jiného zdroje.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-349">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="ae9a6-350">Token párování se vytvoří a nastaví na proměnnou prostředí ( `ASPNETCORE_TOKEN` ) modulem.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-350">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="ae9a6-351">Párovací token je také nastaven na hlavičku ( `MS-ASPNETCORE-TOKEN` ) pro každý požadavek proxy.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-351">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="ae9a6-352">Middleware služby IIS kontroluje každý požadavek, který obdrží, a potvrdí, že hodnota hlavičky párového tokenu odpovídá hodnotě proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-352">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="ae9a6-353">Pokud se hodnoty tokenu neshodují, požadavek se zaprotokoluje a odmítne.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-353">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="ae9a6-354">Proměnná prostředí párového tokenu a přenos dat mezi modulem a Kestrel nejsou přístupné z umístění mimo server.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-354">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="ae9a6-355">Bez znalosti hodnoty párovacího tokenu nemůže útočník odeslat žádosti, které obcházejí kontrolu v Middlewari IIS.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-355">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="ae9a6-356">ASP.NET Core modul se sdílenou konfigurací služby IIS</span><span class="sxs-lookup"><span data-stu-id="ae9a6-356">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="ae9a6-357">Instalační služba modulu ASP.NET Core se spouští s oprávněními účtu **TrustedInstaller** .</span><span class="sxs-lookup"><span data-stu-id="ae9a6-357">The ASP.NET Core Module installer runs with the privileges of the **TrustedInstaller** account.</span></span> <span data-ttu-id="ae9a6-358">Vzhledem k tomu, že účet místního systému nemá oprávnění k úpravám pro cestu ke sdílené složce, kterou používá sdílená konfigurace služby IIS, vyvolá Instalační program chybu odepření přístupu při pokusu o konfiguraci nastavení modulu v souboru *ApplicationHost. config* ve sdílené složce.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-358">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the *applicationHost.config* file on the share.</span></span>

<span data-ttu-id="ae9a6-359">Pokud používáte konfiguraci IIS Shared na stejném počítači jako instalaci služby IIS, spusťte instalační program sady prostředků ASP.NET Core s `OPT_NO_SHARED_CONFIG_CHECK` parametrem nastaveným na `1` :</span><span class="sxs-lookup"><span data-stu-id="ae9a6-359">When using an IIS Shared Configuration on the same machine as the IIS installation, run the ASP.NET Core Hosting Bundle installer with the `OPT_NO_SHARED_CONFIG_CHECK` parameter set to `1`:</span></span>

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

<span data-ttu-id="ae9a6-360">Pokud cesta ke sdílené konfiguraci není ve stejném počítači jako instalace služby IIS, postupujte podle těchto kroků:</span><span class="sxs-lookup"><span data-stu-id="ae9a6-360">When the path to the shared configuration isn't on the same machine as the IIS installation, follow these steps:</span></span>

1. <span data-ttu-id="ae9a6-361">Zakažte sdílenou konfiguraci služby IIS.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-361">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="ae9a6-362">Spusťte instalační program.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-362">Run the installer.</span></span>
1. <span data-ttu-id="ae9a6-363">Exportujte aktualizovaný soubor *ApplicationHost. config* do sdílené složky.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-363">Export the updated *applicationHost.config* file to the share.</span></span>
1. <span data-ttu-id="ae9a6-364">Znovu povolte sdílenou konfiguraci služby IIS.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-364">Re-enable the IIS Shared Configuration.</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="ae9a6-365">Verze modulu a instalační protokoly hostitelské sady</span><span class="sxs-lookup"><span data-stu-id="ae9a6-365">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="ae9a6-366">Postup určení verze nainstalovaného modulu ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="ae9a6-366">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="ae9a6-367">V hostitelském systému přejděte na *%windir%\system32\inetsrv*.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-367">On the hosting system, navigate to *%windir%\System32\inetsrv*.</span></span>
1. <span data-ttu-id="ae9a6-368">Vyhledejte soubor *aspnetcore. dll* .</span><span class="sxs-lookup"><span data-stu-id="ae9a6-368">Locate the *aspnetcore.dll* file.</span></span>
1. <span data-ttu-id="ae9a6-369">Klikněte na soubor pravým tlačítkem a v místní nabídce vyberte **vlastnosti** .</span><span class="sxs-lookup"><span data-stu-id="ae9a6-369">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="ae9a6-370">Vyberte kartu **Podrobnosti** . **Verze souboru** a **verze produktu** reprezentují nainstalovanou verzi modulu.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-370">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="ae9a6-371">Protokoly instalačních balíčků pro modul se nacházejí v *C: \\ Uživatelé \\ % UserName% \\ data \\ Local \\ TEMP*. Soubor má název *dd_DotNetCoreWinSvrHosting__ \<timestamp> _000_AspNetCoreModule_x64. log*.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-371">The Hosting Bundle installer logs for the module are found at *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. The file is named *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="ae9a6-372">Umístění souboru modulu, schématu a konfiguračního souboru</span><span class="sxs-lookup"><span data-stu-id="ae9a6-372">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="ae9a6-373">Modul</span><span class="sxs-lookup"><span data-stu-id="ae9a6-373">Module</span></span>

<span data-ttu-id="ae9a6-374">**Služba IIS (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="ae9a6-374">**IIS (x86/amd64):**</span></span>

* <span data-ttu-id="ae9a6-375">%windir%\System32\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="ae9a6-375">%windir%\System32\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="ae9a6-376">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="ae9a6-376">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="ae9a6-377">%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="ae9a6-377">%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="ae9a6-378">% ProgramFiles (x86)% \ IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="ae9a6-378">%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

<span data-ttu-id="ae9a6-379">**IIS Express (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="ae9a6-379">**IIS Express (x86/amd64):**</span></span>

* <span data-ttu-id="ae9a6-380">%ProgramFiles%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="ae9a6-380">%ProgramFiles%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="ae9a6-381">% ProgramFiles (x86)% \ IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="ae9a6-381">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="ae9a6-382">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="ae9a6-382">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="ae9a6-383">% ProgramFiles (x86)% \ IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="ae9a6-383">%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

### <a name="schema"></a><span data-ttu-id="ae9a6-384">Schéma</span><span class="sxs-lookup"><span data-stu-id="ae9a6-384">Schema</span></span>

<span data-ttu-id="ae9a6-385">**IIS**</span><span class="sxs-lookup"><span data-stu-id="ae9a6-385">**IIS**</span></span>

* <span data-ttu-id="ae9a6-386">%windir%\System32\inetsrv\config\schema\ aspnetcore_schema. XML</span><span class="sxs-lookup"><span data-stu-id="ae9a6-386">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span></span>

* <span data-ttu-id="ae9a6-387">%windir%\System32\inetsrv\config\schema\ aspnetcore_schema_v2. XML</span><span class="sxs-lookup"><span data-stu-id="ae9a6-387">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span></span>

<span data-ttu-id="ae9a6-388">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="ae9a6-388">**IIS Express**</span></span>

* <span data-ttu-id="ae9a6-389">%ProgramFiles%\IIS Express\config\schema\ aspnetcore_schema. XML</span><span class="sxs-lookup"><span data-stu-id="ae9a6-389">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span></span>

* <span data-ttu-id="ae9a6-390">%ProgramFiles%\IIS Express\config\schema\ aspnetcore_schema_v2. XML</span><span class="sxs-lookup"><span data-stu-id="ae9a6-390">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span></span>

### <a name="configuration"></a><span data-ttu-id="ae9a6-391">Konfigurace</span><span class="sxs-lookup"><span data-stu-id="ae9a6-391">Configuration</span></span>

<span data-ttu-id="ae9a6-392">**IIS**</span><span class="sxs-lookup"><span data-stu-id="ae9a6-392">**IIS**</span></span>

* <span data-ttu-id="ae9a6-393">%windir%\System32\inetsrv\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="ae9a6-393">%windir%\System32\inetsrv\config\applicationHost.config</span></span>

<span data-ttu-id="ae9a6-394">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="ae9a6-394">**IIS Express**</span></span>

* <span data-ttu-id="ae9a6-395">Visual Studio: {ROOT aplikace} \\ . vs\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="ae9a6-395">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span></span>

* <span data-ttu-id="ae9a6-396">*IISExpress. exe* CLI:%USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span><span class="sxs-lookup"><span data-stu-id="ae9a6-396">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span></span>

<span data-ttu-id="ae9a6-397">Soubory lze najít hledáním *aspnetcore* v souboru *ApplicationHost. config* .</span><span class="sxs-lookup"><span data-stu-id="ae9a6-397">The files can be found by searching for *aspnetcore* in the *applicationHost.config* file.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="ae9a6-398">Modul ASP.NET Core je nativním modulem služby IIS, který se připojuje k kanálu IIS na jednu z těchto:</span><span class="sxs-lookup"><span data-stu-id="ae9a6-398">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to either:</span></span>

* <span data-ttu-id="ae9a6-399">Hostování aplikace ASP.NET Core uvnitř pracovního procesu služby IIS ( `w3wp.exe` ), který se nazývá [model hostování v rámci procesu](#in-process-hosting-model).</span><span class="sxs-lookup"><span data-stu-id="ae9a6-399">Host an ASP.NET Core app inside of the IIS worker process (`w3wp.exe`), called the [in-process hosting model](#in-process-hosting-model).</span></span>
* <span data-ttu-id="ae9a6-400">Dopředné webové požadavky do back-endu ASP.NET Core aplikace, na které běží [Server Kestrel](xref:fundamentals/servers/kestrel), se nazývá [model hostování mimo proces](#out-of-process-hosting-model).</span><span class="sxs-lookup"><span data-stu-id="ae9a6-400">Forward web requests to a backend ASP.NET Core app running the [Kestrel server](xref:fundamentals/servers/kestrel), called the [out-of-process hosting model](#out-of-process-hosting-model).</span></span>

<span data-ttu-id="ae9a6-401">Podporované verze systému Windows:</span><span class="sxs-lookup"><span data-stu-id="ae9a6-401">Supported Windows versions:</span></span>

* <span data-ttu-id="ae9a6-402">Windows 7 nebo novější</span><span class="sxs-lookup"><span data-stu-id="ae9a6-402">Windows 7 or later</span></span>
* <span data-ttu-id="ae9a6-403">Windows Server 2008 R2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="ae9a6-403">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="ae9a6-404">Při hostování v procesu používá modul implementaci vnitroprocesového serveru pro službu IIS nazvanou IIS HTTP Server ( `IISHttpServer` ).</span><span class="sxs-lookup"><span data-stu-id="ae9a6-404">When hosting in-process, the module uses an in-process server implementation for IIS, called IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="ae9a6-405">Při hostování mimo proces funguje modul pouze s Kestrel.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-405">When hosting out-of-process, the module only works with Kestrel.</span></span> <span data-ttu-id="ae9a6-406">Modul nefunguje s [ovladačem HTTP. sys](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="ae9a6-406">The module doesn't function with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

## <a name="hosting-models"></a><span data-ttu-id="ae9a6-407">Modely hostingu</span><span class="sxs-lookup"><span data-stu-id="ae9a6-407">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="ae9a6-408">Model hostování v procesu</span><span class="sxs-lookup"><span data-stu-id="ae9a6-408">In-process hosting model</span></span>

<span data-ttu-id="ae9a6-409">Chcete-li nakonfigurovat aplikaci pro hostování v rámci procesu, přidejte do `<AspNetCoreHostingModel>` souboru projektu aplikace vlastnost s hodnotou `InProcess` (hostování mimo proces je nastavená na `OutOfProcess` ):</span><span class="sxs-lookup"><span data-stu-id="ae9a6-409">To configure an app for in-process hosting, add the `<AspNetCoreHostingModel>` property to the app's project file with a value of `InProcess` (out-of-process hosting is set with `OutOfProcess`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>InProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="ae9a6-410">Model hostování v rámci procesu není podporován pro aplikace ASP.NET Core, které cílí na .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-410">The in-process hosting model isn't supported for ASP.NET Core apps that target the .NET Framework.</span></span>

<span data-ttu-id="ae9a6-411">Hodnota rozlišuje `<AspNetCoreHostingModel>` malá a velká písmena, takže `inprocess` a `outofprocess` jsou platné hodnoty.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-411">The value of `<AspNetCoreHostingModel>` is case insensitive, so `inprocess` and `outofprocess` are valid values.</span></span>

<span data-ttu-id="ae9a6-412">Pokud `<AspNetCoreHostingModel>` vlastnost není v souboru přítomna, výchozí hodnota je `OutOfProcess` .</span><span class="sxs-lookup"><span data-stu-id="ae9a6-412">If the `<AspNetCoreHostingModel>` property isn't present in the file, the default value is `OutOfProcess`.</span></span>

<span data-ttu-id="ae9a6-413">Následující vlastnosti platí při hostování v procesu:</span><span class="sxs-lookup"><span data-stu-id="ae9a6-413">The following characteristics apply when hosting in-process:</span></span>

* <span data-ttu-id="ae9a6-414">`IISHttpServer`Místo serveru [Kestrel](xref:fundamentals/servers/kestrel) se používá server HTTP služby IIS ().</span><span class="sxs-lookup"><span data-stu-id="ae9a6-414">IIS HTTP Server (`IISHttpServer`) is used instead of [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span> <span data-ttu-id="ae9a6-415">Pro vnitroprocesové [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) volání <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> :</span><span class="sxs-lookup"><span data-stu-id="ae9a6-415">For in-process, [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> to:</span></span>

  * <span data-ttu-id="ae9a6-416">Zaregistrujte `IISHttpServer` .</span><span class="sxs-lookup"><span data-stu-id="ae9a6-416">Register the `IISHttpServer`.</span></span>
  * <span data-ttu-id="ae9a6-417">Nakonfigurujte port a základní cestu, na kterých má Server naslouchat při spuštění za modulem ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-417">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
  * <span data-ttu-id="ae9a6-418">Nakonfigurujte hostitele tak, aby zachytával chyby při spuštění.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-418">Configure the host to capture startup errors.</span></span>

* <span data-ttu-id="ae9a6-419">[Atribut RequestTimeout](#attributes-of-the-aspnetcore-element) se nevztahuje na hostování v rámci procesu.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-419">The [requestTimeout attribute](#attributes-of-the-aspnetcore-element) doesn't apply to in-process hosting.</span></span>

* <span data-ttu-id="ae9a6-420">Sdílení fondu aplikací mezi aplikacemi není podporováno.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-420">Sharing an app pool among apps isn't supported.</span></span> <span data-ttu-id="ae9a6-421">Použijte jeden fond aplikací na aplikaci.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-421">Use one app pool per app.</span></span>

* <span data-ttu-id="ae9a6-422">Při použití [nasazení webu](/iis/publish/using-web-deploy/introduction-to-web-deploy) nebo ručního umístění [souboru App_offline. htm v nasazení](xref:host-and-deploy/iis/index#locked-deployment-files)se aplikace nemusí vypnout okamžitě, pokud dojde k otevřenému připojení.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-422">When using [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) or manually placing an [app_offline.htm file in the deployment](xref:host-and-deploy/iis/index#locked-deployment-files), the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="ae9a6-423">Připojení k protokolu WebSocket může například zpozdit ukončení aplikace.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-423">For example, a websocket connection may delay app shut down.</span></span>

* <span data-ttu-id="ae9a6-424">Architektura (bitová verze) aplikace a nainstalovaného modulu runtime (x64 nebo x86) se musí shodovat s architekturou fondu aplikací.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-424">The architecture (bitness) of the app and installed runtime (x64 or x86) must match the architecture of the app pool.</span></span>

* <span data-ttu-id="ae9a6-425">Zjistila se odpojení klienta.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-425">Client disconnects are detected.</span></span> <span data-ttu-id="ae9a6-426">Token zrušení [HttpContext. RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) je zrušený, když se klient odpojí.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-426">The [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) cancellation token is cancelled when the client disconnects.</span></span>

* <span data-ttu-id="ae9a6-427">V ASP.NET Core 2.2.1 nebo dřívější <xref:System.IO.Directory.GetCurrentDirectory*> vrátí pracovní adresář procesu spuštěného službou IIS místo adresáře aplikace (například *C:\Windows\System32\inetsrv* pro *W3wp. exe*).</span><span class="sxs-lookup"><span data-stu-id="ae9a6-427">In ASP.NET Core 2.2.1 or earlier, <xref:System.IO.Directory.GetCurrentDirectory*> returns the worker directory of the process started by IIS rather than the app's directory (for example, *C:\Windows\System32\inetsrv* for *w3wp.exe*).</span></span>

  <span data-ttu-id="ae9a6-428">Vzorový kód, který nastaví aktuální adresář aplikace, najdete v tématu [Třída CurrentDirectoryHelpers](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/2.x/CurrentDirectoryHelpers.cs).</span><span class="sxs-lookup"><span data-stu-id="ae9a6-428">For sample code that sets the app's current directory, see the [CurrentDirectoryHelpers class](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/2.x/CurrentDirectoryHelpers.cs).</span></span> <span data-ttu-id="ae9a6-429">Zavolejte `SetCurrentDirectory` metodu.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-429">Call the `SetCurrentDirectory` method.</span></span> <span data-ttu-id="ae9a6-430">Následná volání pro <xref:System.IO.Directory.GetCurrentDirectory*> poskytnutí adresáře aplikace.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-430">Subsequent calls to <xref:System.IO.Directory.GetCurrentDirectory*> provide the app's directory.</span></span>

* <span data-ttu-id="ae9a6-431">Při hostování v procesu <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> není volána interně pro inicializaci uživatele.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-431">When hosting in-process, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span> <span data-ttu-id="ae9a6-432">Proto <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementace používaná k transformaci deklarací po každém ověření není ve výchozím nastavení aktivována.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-432">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="ae9a6-433">Při transformaci deklarací s <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementací zavolejte <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> na přidat ověřovací služby:</span><span class="sxs-lookup"><span data-stu-id="ae9a6-433">When transforming claims with an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation, call <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> to add authentication services:</span></span>

  ```csharp
  public void ConfigureServices(IServiceCollection services)
  {
      services.AddTransient<IClaimsTransformation, ClaimsTransformer>();
      services.AddAuthentication(IISServerDefaults.AuthenticationScheme);
  }

  public void Configure(IApplicationBuilder app)
  {
      app.UseAuthentication();
  }
  ```

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="ae9a6-434">Model hostování mimo proces</span><span class="sxs-lookup"><span data-stu-id="ae9a6-434">Out-of-process hosting model</span></span>

<span data-ttu-id="ae9a6-435">Chcete-li nakonfigurovat aplikaci pro hostování mimo procesy, použijte některý z následujících přístupů v souboru projektu:</span><span class="sxs-lookup"><span data-stu-id="ae9a6-435">To configure an app for out-of-process hosting, use either of the following approaches in the project file:</span></span>

* <span data-ttu-id="ae9a6-436">Nezadávejte `<AspNetCoreHostingModel>` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-436">Don't specify the `<AspNetCoreHostingModel>` property.</span></span> <span data-ttu-id="ae9a6-437">Pokud `<AspNetCoreHostingModel>` vlastnost není v souboru přítomna, výchozí hodnota je `OutOfProcess` .</span><span class="sxs-lookup"><span data-stu-id="ae9a6-437">If the `<AspNetCoreHostingModel>` property isn't present in the file, the default value is `OutOfProcess`.</span></span>
* <span data-ttu-id="ae9a6-438">Nastavte hodnotu `<AspNetCoreHostingModel>` vlastnosti na `OutOfProcess` (vnitroprocesové hostování v procesu je nastaveno na `InProcess` ):</span><span class="sxs-lookup"><span data-stu-id="ae9a6-438">Set the value of the `<AspNetCoreHostingModel>` property to `OutOfProcess` (in-process hosting is set with `InProcess`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="ae9a6-439">Hodnota rozlišuje malá a velká písmena, takže `inprocess` a `outofprocess` jsou platné hodnoty.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-439">The value is case insensitive, so `inprocess` and `outofprocess` are valid values.</span></span>

<span data-ttu-id="ae9a6-440">[Kestrel](xref:fundamentals/servers/kestrel) Místo http serveru IIS () se používá server Kestrel `IISHttpServer` .</span><span class="sxs-lookup"><span data-stu-id="ae9a6-440">[Kestrel](xref:fundamentals/servers/kestrel) server is used instead of IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="ae9a6-441">Pro mimoprocesové [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) volání <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> do:</span><span class="sxs-lookup"><span data-stu-id="ae9a6-441">For out-of-process, [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> to:</span></span>

* <span data-ttu-id="ae9a6-442">Nakonfigurujte port a základní cestu, na kterých má Server naslouchat při spuštění za modulem ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-442">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
* <span data-ttu-id="ae9a6-443">Nakonfigurujte hostitele tak, aby zachytával chyby při spuštění.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-443">Configure the host to capture startup errors.</span></span>

### <a name="hosting-model-changes"></a><span data-ttu-id="ae9a6-444">Změny modelu hostování</span><span class="sxs-lookup"><span data-stu-id="ae9a6-444">Hosting model changes</span></span>

<span data-ttu-id="ae9a6-445">Pokud `hostingModel` je nastavení změněno v souboru *Web. config* (vysvětleno v části [Konfigurace pomocí Web. config](#configuration-with-webconfig) ), modul recykluje pracovní proces pro službu IIS.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-445">If the `hostingModel` setting is changed in the *web.config* file (explained in the [Configuration with web.config](#configuration-with-webconfig) section), the module recycles the worker process for IIS.</span></span>

<span data-ttu-id="ae9a6-446">V případě IIS Express modul neprovádí recyklaci pracovního procesu, ale místo toho aktivuje bezproblémové vypnutí aktuálního procesu IIS Express.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-446">For IIS Express, the module doesn't recycle the worker process but instead triggers a graceful shutdown of the current IIS Express process.</span></span> <span data-ttu-id="ae9a6-447">Další požadavek na aplikaci vytvoří nový proces IIS Express.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-447">The next request to the app spawns a new IIS Express process.</span></span>

### <a name="process-name"></a><span data-ttu-id="ae9a6-448">Název procesu</span><span class="sxs-lookup"><span data-stu-id="ae9a6-448">Process name</span></span>

<span data-ttu-id="ae9a6-449">`Process.GetCurrentProcess().ProcessName`sestavy `w3wp` / `iisexpress` (v procesu) nebo `dotnet` (mimo proces).</span><span class="sxs-lookup"><span data-stu-id="ae9a6-449">`Process.GetCurrentProcess().ProcessName` reports `w3wp`/`iisexpress` (in-process) or `dotnet` (out-of-process).</span></span>

<span data-ttu-id="ae9a6-450">Mnoho nativních modulů, jako je ověřování systému Windows, zůstane aktivní.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-450">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="ae9a6-451">Další informace o aktivních modulech služby IIS pomocí modulu ASP.NET Core najdete v tématu <xref:host-and-deploy/iis/modules> .</span><span class="sxs-lookup"><span data-stu-id="ae9a6-451">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="ae9a6-452">Modul ASP.NET Core může také:</span><span class="sxs-lookup"><span data-stu-id="ae9a6-452">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="ae9a6-453">Nastavte proměnné prostředí pro pracovní proces.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-453">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="ae9a6-454">Protokoluje výstup stdout do úložiště souborů pro řešení problémů při spuštění.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-454">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="ae9a6-455">Předejte tokeny ověřování systému Windows.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-455">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="ae9a6-456">Jak nainstalovat a používat modul ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ae9a6-456">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="ae9a6-457">Pokyny k instalaci modulu ASP.NET Core najdete v tématu [instalace hostující sady .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span><span class="sxs-lookup"><span data-stu-id="ae9a6-457">For instructions on how to install the ASP.NET Core Module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="ae9a6-458">Konfigurace pomocí souboru Web. config</span><span class="sxs-lookup"><span data-stu-id="ae9a6-458">Configuration with web.config</span></span>

<span data-ttu-id="ae9a6-459">Modul ASP.NET Core je nakonfigurován s `aspNetCore` částí `system.webServer` uzlu v souboru *Web. config* webu.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-459">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="ae9a6-460">Následující soubor *Web. config* je publikován pro [nasazení závislé na rozhraní](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) a nakonfiguruje modul ASP.NET Core pro zpracování požadavků lokality:</span><span class="sxs-lookup"><span data-stu-id="ae9a6-460">The following *web.config* file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <location path="." inheritInChildApplications="false">
    <system.webServer>
      <handlers>
        <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModuleV2" resourceType="Unspecified" />
      </handlers>
      <aspNetCore processPath="dotnet"
                  arguments=".\MyApp.dll"
                  stdoutLogEnabled="false"
                  stdoutLogFile=".\logs\stdout"
                  hostingModel="inprocess" />
    </system.webServer>
  </location>
</configuration>
```

<span data-ttu-id="ae9a6-461">Následující soubor *Web. config* je publikován pro [samostatně uzavřené nasazení](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span><span class="sxs-lookup"><span data-stu-id="ae9a6-461">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <location path="." inheritInChildApplications="false">
    <system.webServer>
      <handlers>
        <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModuleV2" resourceType="Unspecified" />
      </handlers>
      <aspNetCore processPath=".\MyApp.exe"
                  stdoutLogEnabled="false"
                  stdoutLogFile=".\logs\stdout"
                  hostingModel="inprocess" />
    </system.webServer>
  </location>
</configuration>
```

<span data-ttu-id="ae9a6-462"><xref:System.Configuration.SectionInformation.InheritInChildApplications*>Vlastnost je nastavena na hodnotu `false` , která označuje, že nastavení zadaná v rámci [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) elementu nejsou děděna aplikacemi, které jsou umístěny v podadresáři aplikace.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-462">The <xref:System.Configuration.SectionInformation.InheritInChildApplications*> property is set to `false` to indicate that the settings specified within the [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) element aren't inherited by apps that reside in a subdirectory of the app.</span></span>

<span data-ttu-id="ae9a6-463">Když je aplikace nasazená na [Azure App Service](https://azure.microsoft.com/services/app-service/), `stdoutLogFile` cesta je nastavená na `\\?\%home%\LogFiles\stdout` .</span><span class="sxs-lookup"><span data-stu-id="ae9a6-463">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="ae9a6-464">Cesta ukládá protokoly stdout do složky *soubory protokolů* , což je umístění, které služba automaticky vytvořila.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-464">The path saves stdout logs to the *LogFiles* folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="ae9a6-465">Informace o konfiguraci dílčí aplikace IIS najdete v tématu <xref:host-and-deploy/iis/index#sub-applications> .</span><span class="sxs-lookup"><span data-stu-id="ae9a6-465">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="ae9a6-466">Atributy elementu aspNetCore</span><span class="sxs-lookup"><span data-stu-id="ae9a6-466">Attributes of the aspNetCore element</span></span>

| <span data-ttu-id="ae9a6-467">Atribut</span><span class="sxs-lookup"><span data-stu-id="ae9a6-467">Attribute</span></span> | <span data-ttu-id="ae9a6-468">Description</span><span class="sxs-lookup"><span data-stu-id="ae9a6-468">Description</span></span> | <span data-ttu-id="ae9a6-469">Výchozí</span><span class="sxs-lookup"><span data-stu-id="ae9a6-469">Default</span></span> |
| ---
<span data-ttu-id="ae9a6-470">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ae9a6-470">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ae9a6-471">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ae9a6-471">'Blazor'</span></span>
- <span data-ttu-id="ae9a6-472">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ae9a6-472">'Identity'</span></span>
- <span data-ttu-id="ae9a6-473">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ae9a6-473">'Let's Encrypt'</span></span>
- <span data-ttu-id="ae9a6-474">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ae9a6-474">'Razor'</span></span>
- <span data-ttu-id="ae9a6-475">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="ae9a6-475">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ae9a6-476">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ae9a6-476">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ae9a6-477">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ae9a6-477">'Blazor'</span></span>
- <span data-ttu-id="ae9a6-478">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ae9a6-478">'Identity'</span></span>
- <span data-ttu-id="ae9a6-479">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ae9a6-479">'Let's Encrypt'</span></span>
- <span data-ttu-id="ae9a6-480">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ae9a6-480">'Razor'</span></span>
- <span data-ttu-id="ae9a6-481">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="ae9a6-481">'SignalR' uid:</span></span> 

<span data-ttu-id="ae9a6-482">----- | ---Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ae9a6-482">----- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ae9a6-483">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ae9a6-483">'Blazor'</span></span>
- <span data-ttu-id="ae9a6-484">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ae9a6-484">'Identity'</span></span>
- <span data-ttu-id="ae9a6-485">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ae9a6-485">'Let's Encrypt'</span></span>
- <span data-ttu-id="ae9a6-486">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ae9a6-486">'Razor'</span></span>
- <span data-ttu-id="ae9a6-487">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="ae9a6-487">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ae9a6-488">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ae9a6-488">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ae9a6-489">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ae9a6-489">'Blazor'</span></span>
- <span data-ttu-id="ae9a6-490">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ae9a6-490">'Identity'</span></span>
- <span data-ttu-id="ae9a6-491">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ae9a6-491">'Let's Encrypt'</span></span>
- <span data-ttu-id="ae9a6-492">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ae9a6-492">'Razor'</span></span>
- <span data-ttu-id="ae9a6-493">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="ae9a6-493">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ae9a6-494">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ae9a6-494">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ae9a6-495">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ae9a6-495">'Blazor'</span></span>
- <span data-ttu-id="ae9a6-496">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ae9a6-496">'Identity'</span></span>
- <span data-ttu-id="ae9a6-497">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ae9a6-497">'Let's Encrypt'</span></span>
- <span data-ttu-id="ae9a6-498">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ae9a6-498">'Razor'</span></span>
- <span data-ttu-id="ae9a6-499">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="ae9a6-499">'SignalR' uid:</span></span> 

<span data-ttu-id="ae9a6-500">------ | :-----: | | `arguments` | </span><span class="sxs-lookup"><span data-stu-id="ae9a6-500">------ | :-----: | | `arguments` | </span></span><p><span data-ttu-id="ae9a6-501">Volitelný řetězcový atribut.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-501">Optional string attribute.</span></span></p><p><span data-ttu-id="ae9a6-502">Argumenty ke spustitelnému souboru určenému v **processPath**.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-502">Arguments to the executable specified in **processPath**.</span></span></p> <span data-ttu-id="ae9a6-503">| | | `disableStartUpErrorPage` | </span><span class="sxs-lookup"><span data-stu-id="ae9a6-503">| | | `disableStartUpErrorPage` | </span></span><p><span data-ttu-id="ae9a6-504">Volitelný atribut typu Boolean.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-504">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="ae9a6-505">Pokud je nastaveno na true, stránka **502,5 – selhání procesu** se potlačí a stavová stránka 502 konfigurovaná v *souboru Web. config* má přednost.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-505">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> <span data-ttu-id="ae9a6-506">| `false` | | `forwardWindowsAuthToken` | </span><span class="sxs-lookup"><span data-stu-id="ae9a6-506">| `false` | | `forwardWindowsAuthToken` | </span></span><p><span data-ttu-id="ae9a6-507">Volitelný atribut typu Boolean.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-507">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="ae9a6-508">Pokud je nastaveno na true, token se přepošle podřízenému procesu, který naslouchá na% ASPNETCORE_PORT% jako záhlaví MS-ASPNETCORE-WINAUTHTOKEN na žádost.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-508">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="ae9a6-509">Je zodpovědností za tento proces volání CloseHandle na tento token na žádost.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-509">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> <span data-ttu-id="ae9a6-510">| `true` | | `hostingModel` | </span><span class="sxs-lookup"><span data-stu-id="ae9a6-510">| `true` | | `hostingModel` | </span></span><p><span data-ttu-id="ae9a6-511">Volitelný řetězcový atribut.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-511">Optional string attribute.</span></span></p><p><span data-ttu-id="ae9a6-512">Určuje model hostování jako vnitroprocesové ( `InProcess` / `inprocess` ) nebo mimo proces ( `OutOfProcess` / `outofprocess` ).</span><span class="sxs-lookup"><span data-stu-id="ae9a6-512">Specifies the hosting model as in-process (`InProcess`/`inprocess`) or out-of-process (`OutOfProcess`/`outofprocess`).</span></span></p> | `OutOfProcess`<br><span data-ttu-id="ae9a6-513">`outofprocess` | | `processesPerApplication` | </span><span class="sxs-lookup"><span data-stu-id="ae9a6-513">`outofprocess` | | `processesPerApplication` | </span></span><p><span data-ttu-id="ae9a6-514">Volitelný celočíselný atribut</span><span class="sxs-lookup"><span data-stu-id="ae9a6-514">Optional integer attribute.</span></span></p><p><span data-ttu-id="ae9a6-515">Určuje počet instancí procesu určeného v nastavení **processPath** , které lze v rámci aplikace vystavit.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-515">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="ae9a6-516">&dagger;Pro hostování v rámci procesu je hodnota omezená na `1` .</span><span class="sxs-lookup"><span data-stu-id="ae9a6-516">&dagger;For in-process hosting, the value is limited to `1`.</span></span></p><p><span data-ttu-id="ae9a6-517">Nastavení `processesPerApplication` se nedoporučuje.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-517">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="ae9a6-518">Tento atribut bude v budoucí verzi odebrán.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-518">This attribute will be removed in a future release.</span></span></p> <span data-ttu-id="ae9a6-519">| Výchozí`1`</span><span class="sxs-lookup"><span data-stu-id="ae9a6-519">| Default: `1`</span></span><br><span data-ttu-id="ae9a6-520">Dlouhé`1`</span><span class="sxs-lookup"><span data-stu-id="ae9a6-520">Min: `1`</span></span><br><span data-ttu-id="ae9a6-521">Max: `100` &dagger; | |`processPath` | </span><span class="sxs-lookup"><span data-stu-id="ae9a6-521">Max: `100`&dagger; | | `processPath` | </span></span><p><span data-ttu-id="ae9a6-522">Povinný atribut řetězce.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-522">Required string attribute.</span></span></p><p><span data-ttu-id="ae9a6-523">Cesta ke spustitelnému souboru, který spouští proces naslouchající na požadavky HTTP.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-523">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="ae9a6-524">Jsou podporovány relativní cesty.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-524">Relative paths are supported.</span></span> <span data-ttu-id="ae9a6-525">Pokud cesta začíná `.` , bude tato cesta považována za relativní vzhledem k kořenu webu.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-525">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> <span data-ttu-id="ae9a6-526">| | | `rapidFailsPerMinute` | </span><span class="sxs-lookup"><span data-stu-id="ae9a6-526">| | | `rapidFailsPerMinute` | </span></span><p><span data-ttu-id="ae9a6-527">Volitelný celočíselný atribut</span><span class="sxs-lookup"><span data-stu-id="ae9a6-527">Optional integer attribute.</span></span></p><p><span data-ttu-id="ae9a6-528">Určuje, kolikrát je povoleno selhání procesu určeného v **processPath** za minutu.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-528">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="ae9a6-529">Pokud je tento limit překročen, modul ukončí spuštění procesu po zbytek minuty.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-529">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p><p><span data-ttu-id="ae9a6-530">Nepodporováno v hostování v rámci procesu.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-530">Not supported with in-process hosting.</span></span></p> <span data-ttu-id="ae9a6-531">| Výchozí`10`</span><span class="sxs-lookup"><span data-stu-id="ae9a6-531">| Default: `10`</span></span><br><span data-ttu-id="ae9a6-532">Dlouhé`0`</span><span class="sxs-lookup"><span data-stu-id="ae9a6-532">Min: `0`</span></span><br><span data-ttu-id="ae9a6-533">Max: `100` | |`requestTimeout` | </span><span class="sxs-lookup"><span data-stu-id="ae9a6-533">Max: `100` | | `requestTimeout` | </span></span><p><span data-ttu-id="ae9a6-534">Volitelný atribut TimeSpan.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-534">Optional timespan attribute.</span></span></p><p><span data-ttu-id="ae9a6-535">Určuje dobu, po kterou modul ASP.NET Core čeká na odpověď od procesu, který naslouchá na% ASPNETCORE_PORT%.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-535">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="ae9a6-536">Ve verzích modulu ASP.NET Core, které byly dodávány s vydáním ASP.NET Core 2,1 nebo vyšší, `requestTimeout` je určena v hodinách, minutách a sekundách.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-536">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p><p><span data-ttu-id="ae9a6-537">Neplatí pro hostování v procesu.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-537">Doesn't apply to in-process hosting.</span></span> <span data-ttu-id="ae9a6-538">Pro hostování v rámci procesu modul čeká, až aplikace zpracuje požadavek.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-538">For in-process hosting, the module waits for the app to process the request.</span></span></p><p><span data-ttu-id="ae9a6-539">Platné hodnoty segmentů minut a sekund řetězce jsou v rozsahu 0-59.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-539">Valid values for minutes and seconds segments of the string are in the range 0-59.</span></span> <span data-ttu-id="ae9a6-540">Při použití hodnoty **60** v hodnotě minut nebo sekund dojde k chybě systému *500 – interní chyba serveru*.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-540">Use of **60** in the value for minutes or seconds results in a *500 - Internal Server Error*.</span></span></p> <span data-ttu-id="ae9a6-541">| Výchozí`00:02:00`</span><span class="sxs-lookup"><span data-stu-id="ae9a6-541">| Default: `00:02:00`</span></span><br><span data-ttu-id="ae9a6-542">Dlouhé`00:00:00`</span><span class="sxs-lookup"><span data-stu-id="ae9a6-542">Min: `00:00:00`</span></span><br><span data-ttu-id="ae9a6-543">Max: `360:00:00` | |`shutdownTimeLimit` | </span><span class="sxs-lookup"><span data-stu-id="ae9a6-543">Max: `360:00:00` | | `shutdownTimeLimit` | </span></span><p><span data-ttu-id="ae9a6-544">Volitelný celočíselný atribut</span><span class="sxs-lookup"><span data-stu-id="ae9a6-544">Optional integer attribute.</span></span></p><p><span data-ttu-id="ae9a6-545">Doba v sekundách, po kterou modul čeká na řádné vypnutí spustitelného souboru, když se zjistí soubor *App_offline. htm* .</span><span class="sxs-lookup"><span data-stu-id="ae9a6-545">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> <span data-ttu-id="ae9a6-546">| Výchozí`10`</span><span class="sxs-lookup"><span data-stu-id="ae9a6-546">| Default: `10`</span></span><br><span data-ttu-id="ae9a6-547">Dlouhé`0`</span><span class="sxs-lookup"><span data-stu-id="ae9a6-547">Min: `0`</span></span><br><span data-ttu-id="ae9a6-548">Max: `600` | |`startupTimeLimit` | </span><span class="sxs-lookup"><span data-stu-id="ae9a6-548">Max: `600` | | `startupTimeLimit` | </span></span><p><span data-ttu-id="ae9a6-549">Volitelný celočíselný atribut</span><span class="sxs-lookup"><span data-stu-id="ae9a6-549">Optional integer attribute.</span></span></p><p><span data-ttu-id="ae9a6-550">Doba v sekundách, kterou modul počká, než spustitelný soubor spustí proces, který na portu naslouchá.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-550">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="ae9a6-551">Pokud je tento časový limit překročen, modul proces ukončuje.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-551">If this time limit is exceeded, the module kills the process.</span></span> <span data-ttu-id="ae9a6-552">Modul se pokusí znovu spustit proces, když obdrží novou žádost, a pokračuje v pokusu o restartování procesu u dalších příchozích požadavků, pokud se nepodaří spustit **rapidFailsPerMinute** počet pokusů během poslední minuty.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-552">The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="ae9a6-553">Hodnota 0 (nula **) není považována za** nekonečný časový limit.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-553">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> <span data-ttu-id="ae9a6-554">| Výchozí`120`</span><span class="sxs-lookup"><span data-stu-id="ae9a6-554">| Default: `120`</span></span><br><span data-ttu-id="ae9a6-555">Dlouhé`0`</span><span class="sxs-lookup"><span data-stu-id="ae9a6-555">Min: `0`</span></span><br><span data-ttu-id="ae9a6-556">Max: `3600` | |`stdoutLogEnabled` | </span><span class="sxs-lookup"><span data-stu-id="ae9a6-556">Max: `3600` | | `stdoutLogEnabled` | </span></span><p><span data-ttu-id="ae9a6-557">Volitelný atribut typu Boolean.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-557">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="ae9a6-558">Je-li nastavena hodnota true, **stdout** a **stderr** pro proces zadaný v **processPath** budou přesměrovány do souboru zadaného v **stdoutLogFile**.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-558">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> <span data-ttu-id="ae9a6-559">| `false` | | `stdoutLogFile` | </span><span class="sxs-lookup"><span data-stu-id="ae9a6-559">| `false` | | `stdoutLogFile` | </span></span><p><span data-ttu-id="ae9a6-560">Volitelný řetězcový atribut.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-560">Optional string attribute.</span></span></p><p><span data-ttu-id="ae9a6-561">Určuje relativní nebo absolutní cestu k souboru, pro který je **stdout** a **stderr** z procesu určeného v **processPath** protokolováno.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-561">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="ae9a6-562">Relativní cesty jsou relativní vzhledem k kořenu webu.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-562">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="ae9a6-563">Každá cesta začínající řetězcem `.` je relativní vzhledem k kořenu webu a všechny ostatní cesty se považují za absolutní cesty.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-563">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="ae9a6-564">Při vytvoření souboru protokolu se v modulu vytvoří všechny složky, které jsou v cestě zadané.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-564">Any folders provided in the path are created by the module when the log file is created.</span></span> <span data-ttu-id="ae9a6-565">Pomocí oddělovačů podtržítka se do posledního segmentu cesty **stdoutLogFile** přidá časové razítko, ID procesu a Přípona souboru (*. log*).</span><span class="sxs-lookup"><span data-stu-id="ae9a6-565">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="ae9a6-566">Pokud `.\logs\stdout` je zadán jako hodnota, je ukázkový protokol stdout uložen jako *stdout_20180205194132_1934. log* ve složce *logs* při uložení na 2/5/2018 v 19:41:32 s ID procesu 1934.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-566">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

### <a name="setting-environment-variables"></a><span data-ttu-id="ae9a6-567">Nastavení proměnných prostředí</span><span class="sxs-lookup"><span data-stu-id="ae9a6-567">Setting environment variables</span></span>

<span data-ttu-id="ae9a6-568">Proměnné prostředí lze zadat pro proces v `processPath` atributu.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-568">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="ae9a6-569">Zadejte proměnnou prostředí s `<environmentVariable>` podřízeným elementem `<environmentVariables>` elementu kolekce.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-569">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span> <span data-ttu-id="ae9a6-570">Proměnné prostředí nastavené v této části mají přednost před proměnnými prostředí systému.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-570">Environment variables set in this section take precedence over system environment variables.</span></span>

<span data-ttu-id="ae9a6-571">Následující příklad nastaví dvě proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-571">The following example sets two environment variables.</span></span> <span data-ttu-id="ae9a6-572">`ASPNETCORE_ENVIRONMENT`nakonfiguruje prostředí aplikace na `Development` .</span><span class="sxs-lookup"><span data-stu-id="ae9a6-572">`ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="ae9a6-573">Vývojář může tuto hodnotu dočasně nastavit v souboru *Web. config* , aby bylo možné vynutit, aby se [Stránka s výjimkou vývojářů](xref:fundamentals/error-handling) načetla při ladění výjimky aplikace.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-573">A developer may temporarily set this value in the *web.config* file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="ae9a6-574">`CONFIG_DIR`je příkladem uživatelsky definované proměnné prostředí, kde vývojář napsal kód, který přečte hodnotu při spuštění, aby vytvořil cestu pro načtení konfiguračního souboru aplikace.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-574">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile=".\logs\stdout"
      hostingModel="inprocess">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
    <environmentVariable name="CONFIG_DIR" value="f:\application_config" />
  </environmentVariables>
</aspNetCore>
```

> [!NOTE]
> <span data-ttu-id="ae9a6-575">Alternativou k nastavení prostředí přímo v souboru *Web. config* je zahrnutí `<EnvironmentName>` vlastnosti do [profilu publikování (. pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) nebo souboru projektu.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-575">An alternative to setting the environment directly in *web.config* is to include the `<EnvironmentName>` property in the [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) or project file.</span></span> <span data-ttu-id="ae9a6-576">Tento přístup nastaví prostředí v *souboru Web. config* při publikování projektu:</span><span class="sxs-lookup"><span data-stu-id="ae9a6-576">This approach sets the environment in *web.config* when the project is published:</span></span>
>
> ```xml
> <PropertyGroup>
>   <EnvironmentName>Development</EnvironmentName>
> </PropertyGroup>
> ```

> [!WARNING]
> <span data-ttu-id="ae9a6-577">Nastavte pouze `ASPNETCORE_ENVIRONMENT` proměnnou prostředí na `Development` pracovní a testovací servery, které nejsou přístupné nedůvěryhodným sítím, jako je například Internet.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-577">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## <a name="app_offlinehtm"></a><span data-ttu-id="ae9a6-578">app_offline. htm</span><span class="sxs-lookup"><span data-stu-id="ae9a6-578">app_offline.htm</span></span>

<span data-ttu-id="ae9a6-579">Pokud se v kořenovém adresáři aplikace zjistí soubor s názvem *App_offline. htm* , modul ASP.NET Core se pokusí aplikaci řádně vypnout a zastavit zpracování příchozích požadavků.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-579">If a file with the name *app_offline.htm* is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="ae9a6-580">Pokud aplikace běží i po uplynutí počtu sekund definovaného v `shutdownTimeLimit` , modul ASP.NET Core ukončuje běžící proces.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-580">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="ae9a6-581">Když je přítomen soubor *App_offline. htm* , ASP.NET Core modul reaguje na požadavky odesláním zpět obsahu souboru *App_offline. htm* .</span><span class="sxs-lookup"><span data-stu-id="ae9a6-581">While the *app_offline.htm* file is present, the ASP.NET Core Module responds to requests by sending back the contents of the *app_offline.htm* file.</span></span> <span data-ttu-id="ae9a6-582">Po odebrání souboru *App_offline. htm* aplikace spustí další požadavek.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-582">When the *app_offline.htm* file is removed, the next request starts the app.</span></span>

<span data-ttu-id="ae9a6-583">Při použití nezpracovaného modelu hostování se aplikace nemusí okamžitě vypnout, pokud dojde k otevřenému připojení.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-583">When using the out-of-process hosting model, the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="ae9a6-584">Připojení k protokolu WebSocket může například zpozdit ukončení aplikace.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-584">For example, a websocket connection may delay app shut down.</span></span>

## <a name="start-up-error-page"></a><span data-ttu-id="ae9a6-585">Spouštěcí chybová stránka</span><span class="sxs-lookup"><span data-stu-id="ae9a6-585">Start-up error page</span></span>

<span data-ttu-id="ae9a6-586">Hostování v procesu i mimo procesy vytváří vlastní chybové stránky, když se nepodaří aplikaci spustit.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-586">Both in-process and out-of-process hosting produce custom error pages when they fail to start the app.</span></span>

<span data-ttu-id="ae9a6-587">Pokud modul ASP.NET Core nenalezne obslužnou rutinu žádosti v procesu nebo mimo proces 500,0, zobrazí se stránka s kódem stavu *selhání načtení obslužné rutiny v* procesu nebo mimo proces.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-587">If the ASP.NET Core Module fails to find either the in-process or out-of-process request handler, a *500.0 - In-Process/Out-Of-Process Handler Load Failure* status code page appears.</span></span>

<span data-ttu-id="ae9a6-588">Pro vnitroprocesové hostování v případě, že se modul ASP.NET Core nepodaří spustit aplikaci, zobrazí se stránka s kódem stavu *chyby při spuštění 500,30* .</span><span class="sxs-lookup"><span data-stu-id="ae9a6-588">For in-process hosting if the ASP.NET Core Module fails to start the app, a *500.30 - Start Failure* status code page appears.</span></span>

<span data-ttu-id="ae9a6-589">Pro hostování mimo procesy, pokud se ASP.NET Core modulu nespustí back-end proces, nebo dojde k selhání procesu back-endu, ale nenaslouchá na konfigurovaném portu, zobrazí se stránka s kódem stavu *selhání procesu 502,5* .</span><span class="sxs-lookup"><span data-stu-id="ae9a6-589">For out-of-process hosting if the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span>

<span data-ttu-id="ae9a6-590">Chcete-li tuto stránku potlačit a vrátit se na výchozí stavovou stránku služby IIS 5xx, použijte `disableStartUpErrorPage` atribut.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-590">To suppress this page and revert to the default IIS 5xx status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="ae9a6-591">Další informace o konfiguraci vlastních chybových zpráv najdete v tématu [chyby \<httpErrors> protokolu HTTP ](/iis/configuration/system.webServer/httpErrors/).</span><span class="sxs-lookup"><span data-stu-id="ae9a6-591">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

## <a name="log-creation-and-redirection"></a><span data-ttu-id="ae9a6-592">Vytvoření a přesměrování protokolu</span><span class="sxs-lookup"><span data-stu-id="ae9a6-592">Log creation and redirection</span></span>

<span data-ttu-id="ae9a6-593">Modul ASP.NET Core přesměruje výstup konzoly stdout a stderr na disk, pokud `stdoutLogEnabled` `stdoutLogFile` `aspNetCore` jsou nastaveny atributy a elementu.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-593">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="ae9a6-594">`stdoutLogFile`Při vytvoření souboru protokolu jsou všechny složky v této cestě vytvářeny modulem.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-594">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="ae9a6-595">Fond aplikací musí mít oprávnění k zápisu do umístění, kam se protokoly zapisují (použijte `IIS AppPool\<app_pool_name>` k poskytnutí oprávnění k zápisu).</span><span class="sxs-lookup"><span data-stu-id="ae9a6-595">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="ae9a6-596">Protokoly nejsou otočeny, pokud nedochází k recyklování/restartování procesu.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-596">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="ae9a6-597">Je zodpovědností hostitele omezit místo na disku, které používají protokoly.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-597">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="ae9a6-598">Použití protokolu stdout se doporučuje pouze při řešení potíží se spouštěním aplikací při hostování služby IIS nebo při použití [podpory v době vývoje služby IIS se sadou Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), nikoli při ladění místně a při spuštění aplikace s IIS Express.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-598">Using the stdout log is only recommended for troubleshooting app startup issues when hosting on IIS or when using [development-time support for IIS with Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), not while debugging locally and running the app with IIS Express.</span></span>

<span data-ttu-id="ae9a6-599">Nepoužívejte protokol stdout pro obecné účely protokolování aplikace.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-599">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="ae9a6-600">Pro rutinu protokolování v aplikaci ASP.NET Core použijte knihovnu protokolování, která omezuje velikost souboru protokolu a otočí protokoly.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-600">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="ae9a6-601">Další informace najdete v tématu [Zprostředkovatelé protokolování třetích stran](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="ae9a6-601">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="ae9a6-602">Časové razítko a Přípona souboru se automaticky přidají při vytvoření souboru protokolu.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-602">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="ae9a6-603">Název souboru protokolu se skládá připojením časového razítka, ID procesu a přípony souboru (*. log*) k poslednímu segmentu `stdoutLogFile` cesty (obvykle *stdout*), oddělený podtržítky.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-603">The log file name is composed by appending the timestamp, process ID, and file extension (*.log*) to the last segment of the `stdoutLogFile` path (typically *stdout*) delimited by underscores.</span></span> <span data-ttu-id="ae9a6-604">Pokud `stdoutLogFile` cesta končí na *stdout*, protokol pro aplikaci s číslem PID 1934 vytvořeným v 2/5/2018 v 19:42:32 má název souboru *stdout_20180205194132_1934. log*.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-604">If the `stdoutLogFile` path ends with *stdout*, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name *stdout_20180205194132_1934.log*.</span></span>

<span data-ttu-id="ae9a6-605">Pokud `stdoutLogEnabled` je hodnota false, chyby, ke kterým dochází při spuštění aplikace, se zaznamenávají a generují do protokolu událostí až do 30 kB.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-605">If `stdoutLogEnabled` is false, errors that occur on app startup are captured and emitted to the event log up to 30 KB.</span></span> <span data-ttu-id="ae9a6-606">Po spuštění budou všechny další protokoly zahozeny.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-606">After startup, all additional logs are discarded.</span></span>

<span data-ttu-id="ae9a6-607">Následující vzorový `aspNetCore` prvek nakonfiguruje protokolování stdout na relativní cestě `.\log\` .</span><span class="sxs-lookup"><span data-stu-id="ae9a6-607">The following sample `aspNetCore` element configures stdout logging at the relative path `.\log\`.</span></span> <span data-ttu-id="ae9a6-608">Potvrďte, že identita uživatele fondu aplikací má oprávnění k zápisu do zadané cesty.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-608">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout"
    hostingModel="inprocess">
</aspNetCore>
```

<span data-ttu-id="ae9a6-609">Když publikujete aplikaci pro nasazení Azure App Service, sada web SDK nastaví `stdoutLogFile` hodnotu na `\\?\%home%\LogFiles\stdout` .</span><span class="sxs-lookup"><span data-stu-id="ae9a6-609">When publishing an app for Azure App Service deployment, the Web SDK sets the `stdoutLogFile` value to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="ae9a6-610">`%home`Proměnná prostředí je předdefinovaná pro aplikace hostované v Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-610">The `%home` environment variable is predefined for apps hosted by Azure App Service.</span></span>

<span data-ttu-id="ae9a6-611">Další informace o formátech cest najdete v tématu [formáty souborů cest v systémech Windows](/dotnet/standard/io/file-path-formats).</span><span class="sxs-lookup"><span data-stu-id="ae9a6-611">For more information on path formats, see [File path formats on Windows systems](/dotnet/standard/io/file-path-formats).</span></span>

## <a name="enhanced-diagnostic-logs"></a><span data-ttu-id="ae9a6-612">Rozšířené diagnostické protokoly</span><span class="sxs-lookup"><span data-stu-id="ae9a6-612">Enhanced diagnostic logs</span></span>

<span data-ttu-id="ae9a6-613">Modul ASP.NET Core lze nakonfigurovat tak, aby poskytoval rozšířené diagnostické protokoly.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-613">The ASP.NET Core Module is configurable to provide enhanced diagnostics logs.</span></span> <span data-ttu-id="ae9a6-614">Přidejte `<handlerSettings>` element do `<aspNetCore>` elementu v *souboru Web. config*. Nastavení `debugLevel` pro `TRACE` zpřístupňuje vyšší věrnost diagnostických informací:</span><span class="sxs-lookup"><span data-stu-id="ae9a6-614">Add the `<handlerSettings>` element to the `<aspNetCore>` element in *web.config*. Setting the `debugLevel` to `TRACE` exposes a higher fidelity of diagnostic information:</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="inprocess">
  <handlerSettings>
    <handlerSetting name="debugFile" value=".\logs\aspnetcore-debug.log" />
    <handlerSetting name="debugLevel" value="FILE,TRACE" />
  </handlerSettings>
</aspNetCore>
```

<span data-ttu-id="ae9a6-615">Složky v cestě poskytnuté `<handlerSetting>` hodnotě (*protokoly* v předchozím příkladu) nejsou vytvářeny modulem automaticky a měly by být v nasazení předběžně existující.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-615">Folders in the path provided to the `<handlerSetting>` value (*logs* in the preceding example) aren't created by the module automatically and should pre-exist in the deployment.</span></span> <span data-ttu-id="ae9a6-616">Fond aplikací musí mít oprávnění k zápisu do umístění, kam se protokoly zapisují (použijte `IIS AppPool\<app_pool_name>` k poskytnutí oprávnění k zápisu).</span><span class="sxs-lookup"><span data-stu-id="ae9a6-616">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="ae9a6-617">Hodnoty úrovně ladění ( `debugLevel` ) mohou zahrnovat jak úroveň, tak i umístění.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-617">Debug level (`debugLevel`) values can include both the level and the location.</span></span>

<span data-ttu-id="ae9a6-618">Úrovně (v pořadí od nejméně po nejpodrobnější):</span><span class="sxs-lookup"><span data-stu-id="ae9a6-618">Levels (in order from least to most verbose):</span></span>

* <span data-ttu-id="ae9a6-619">ERROR</span><span class="sxs-lookup"><span data-stu-id="ae9a6-619">ERROR</span></span>
* <span data-ttu-id="ae9a6-620">WARNING</span><span class="sxs-lookup"><span data-stu-id="ae9a6-620">WARNING</span></span>
* <span data-ttu-id="ae9a6-621">PŘÍJEMCE</span><span class="sxs-lookup"><span data-stu-id="ae9a6-621">INFO</span></span>
* <span data-ttu-id="ae9a6-622">TRACE</span><span class="sxs-lookup"><span data-stu-id="ae9a6-622">TRACE</span></span>

<span data-ttu-id="ae9a6-623">Umístění (je povoleno více umístění):</span><span class="sxs-lookup"><span data-stu-id="ae9a6-623">Locations (multiple locations are permitted):</span></span>

* <span data-ttu-id="ae9a6-624">STROMU</span><span class="sxs-lookup"><span data-stu-id="ae9a6-624">CONSOLE</span></span>
* <span data-ttu-id="ae9a6-625">EVENTLOG</span><span class="sxs-lookup"><span data-stu-id="ae9a6-625">EVENTLOG</span></span>
* <span data-ttu-id="ae9a6-626">SOUBOR</span><span class="sxs-lookup"><span data-stu-id="ae9a6-626">FILE</span></span>

<span data-ttu-id="ae9a6-627">Nastavení obslužné rutiny lze také poskytnout pomocí proměnných prostředí:</span><span class="sxs-lookup"><span data-stu-id="ae9a6-627">The handler settings can also be provided via environment variables:</span></span>

* <span data-ttu-id="ae9a6-628">`ASPNETCORE_MODULE_DEBUG_FILE`: Cesta k souboru protokolu ladění.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-628">`ASPNETCORE_MODULE_DEBUG_FILE`: Path to the debug log file.</span></span> <span data-ttu-id="ae9a6-629">(Výchozí: *aspnetcore-Debug. log*)</span><span class="sxs-lookup"><span data-stu-id="ae9a6-629">(Default: *aspnetcore-debug.log*)</span></span>
* <span data-ttu-id="ae9a6-630">`ASPNETCORE_MODULE_DEBUG`: Nastavení úrovně ladění.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-630">`ASPNETCORE_MODULE_DEBUG`: Debug level setting.</span></span>

> [!WARNING]
> <span data-ttu-id="ae9a6-631">Nenechávejte **protokolování** ladění povolené v nasazení po delší dobu, než je potřeba k řešení problému.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-631">Do **not** leave debug logging enabled in the deployment for longer than required to troubleshoot an issue.</span></span> <span data-ttu-id="ae9a6-632">Velikost protokolu není omezená.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-632">The size of the log isn't limited.</span></span> <span data-ttu-id="ae9a6-633">Když necháte protokol ladění povolený, může dojít k vyčerpání dostupného místa na disku a selhání serveru nebo služby App Service.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-633">Leaving the debug log enabled can exhaust the available disk space and crash the server or app service.</span></span>

<span data-ttu-id="ae9a6-634">Příklad [Configuration with web.config](#configuration-with-webconfig) `aspNetCore` elementu v souboru *Web. config* naleznete v tématu Konfigurace pomocí souboru Web. config.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-634">See [Configuration with web.config](#configuration-with-webconfig) for an example of the `aspNetCore` element in the *web.config* file.</span></span>

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="ae9a6-635">Konfigurace proxy serveru používá protokol HTTP a token párování.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-635">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="ae9a6-636">*Platí pouze pro hostování mimo proces.*</span><span class="sxs-lookup"><span data-stu-id="ae9a6-636">*Only applies to out-of-process hosting.*</span></span>

<span data-ttu-id="ae9a6-637">Proxy vytvořený mezi modulem ASP.NET Core a Kestrel používá protokol HTTP.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-637">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="ae9a6-638">Nehrozí riziko odposlouchávání provozu mezi modulem a Kestrel z umístění od serveru.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-638">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="ae9a6-639">Párovací token se používá k zajištění, že žádosti přijaté službou Kestrel byly proxy službou IIS a nepocházejí z jiného zdroje.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-639">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="ae9a6-640">Token párování se vytvoří a nastaví na proměnnou prostředí ( `ASPNETCORE_TOKEN` ) modulem.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-640">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="ae9a6-641">Párovací token je také nastaven na hlavičku ( `MS-ASPNETCORE-TOKEN` ) pro každý požadavek proxy.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-641">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="ae9a6-642">Middleware služby IIS kontroluje každý požadavek, který obdrží, a potvrdí, že hodnota hlavičky párového tokenu odpovídá hodnotě proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-642">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="ae9a6-643">Pokud se hodnoty tokenu neshodují, požadavek se zaprotokoluje a odmítne.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-643">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="ae9a6-644">Proměnná prostředí párového tokenu a přenos dat mezi modulem a Kestrel nejsou přístupné z umístění mimo server.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-644">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="ae9a6-645">Bez znalosti hodnoty párovacího tokenu nemůže útočník odeslat žádosti, které obcházejí kontrolu v Middlewari IIS.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-645">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="ae9a6-646">ASP.NET Core modul se sdílenou konfigurací služby IIS</span><span class="sxs-lookup"><span data-stu-id="ae9a6-646">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="ae9a6-647">Instalační služba modulu ASP.NET Core se spouští s oprávněními účtu **TrustedInstaller** .</span><span class="sxs-lookup"><span data-stu-id="ae9a6-647">The ASP.NET Core Module installer runs with the privileges of the **TrustedInstaller** account.</span></span> <span data-ttu-id="ae9a6-648">Vzhledem k tomu, že účet místního systému nemá oprávnění k úpravám pro cestu ke sdílené složce, kterou používá sdílená konfigurace služby IIS, vyvolá Instalační program chybu odepření přístupu při pokusu o konfiguraci nastavení modulu v souboru *ApplicationHost. config* ve sdílené složce.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-648">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the *applicationHost.config* file on the share.</span></span>

<span data-ttu-id="ae9a6-649">Pokud používáte konfiguraci IIS Shared na stejném počítači jako instalaci služby IIS, spusťte instalační program sady prostředků ASP.NET Core s `OPT_NO_SHARED_CONFIG_CHECK` parametrem nastaveným na `1` :</span><span class="sxs-lookup"><span data-stu-id="ae9a6-649">When using an IIS Shared Configuration on the same machine as the IIS installation, run the ASP.NET Core Hosting Bundle installer with the `OPT_NO_SHARED_CONFIG_CHECK` parameter set to `1`:</span></span>

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

<span data-ttu-id="ae9a6-650">Pokud cesta ke sdílené konfiguraci není ve stejném počítači jako instalace služby IIS, postupujte podle těchto kroků:</span><span class="sxs-lookup"><span data-stu-id="ae9a6-650">When the path to the shared configuration isn't on the same machine as the IIS installation, follow these steps:</span></span>

1. <span data-ttu-id="ae9a6-651">Zakažte sdílenou konfiguraci služby IIS.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-651">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="ae9a6-652">Spusťte instalační program.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-652">Run the installer.</span></span>
1. <span data-ttu-id="ae9a6-653">Exportujte aktualizovaný soubor *ApplicationHost. config* do sdílené složky.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-653">Export the updated *applicationHost.config* file to the share.</span></span>
1. <span data-ttu-id="ae9a6-654">Znovu povolte sdílenou konfiguraci služby IIS.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-654">Re-enable the IIS Shared Configuration.</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="ae9a6-655">Verze modulu a instalační protokoly hostitelské sady</span><span class="sxs-lookup"><span data-stu-id="ae9a6-655">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="ae9a6-656">Postup určení verze nainstalovaného modulu ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="ae9a6-656">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="ae9a6-657">V hostitelském systému přejděte na *%windir%\system32\inetsrv*.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-657">On the hosting system, navigate to *%windir%\System32\inetsrv*.</span></span>
1. <span data-ttu-id="ae9a6-658">Vyhledejte soubor *aspnetcore. dll* .</span><span class="sxs-lookup"><span data-stu-id="ae9a6-658">Locate the *aspnetcore.dll* file.</span></span>
1. <span data-ttu-id="ae9a6-659">Klikněte na soubor pravým tlačítkem a v místní nabídce vyberte **vlastnosti** .</span><span class="sxs-lookup"><span data-stu-id="ae9a6-659">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="ae9a6-660">Vyberte kartu **Podrobnosti** . **Verze souboru** a **verze produktu** reprezentují nainstalovanou verzi modulu.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-660">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="ae9a6-661">Protokoly instalačních balíčků pro modul se nacházejí v *C: \\ Uživatelé \\ % UserName% \\ data \\ Local \\ TEMP*. Soubor má název *dd_DotNetCoreWinSvrHosting__ \<timestamp> _000_AspNetCoreModule_x64. log*.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-661">The Hosting Bundle installer logs for the module are found at *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. The file is named *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="ae9a6-662">Umístění souboru modulu, schématu a konfiguračního souboru</span><span class="sxs-lookup"><span data-stu-id="ae9a6-662">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="ae9a6-663">Modul</span><span class="sxs-lookup"><span data-stu-id="ae9a6-663">Module</span></span>

<span data-ttu-id="ae9a6-664">**Služba IIS (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="ae9a6-664">**IIS (x86/amd64):**</span></span>

* <span data-ttu-id="ae9a6-665">%windir%\System32\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="ae9a6-665">%windir%\System32\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="ae9a6-666">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="ae9a6-666">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="ae9a6-667">%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="ae9a6-667">%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="ae9a6-668">% ProgramFiles (x86)% \ IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="ae9a6-668">%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

<span data-ttu-id="ae9a6-669">**IIS Express (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="ae9a6-669">**IIS Express (x86/amd64):**</span></span>

* <span data-ttu-id="ae9a6-670">%ProgramFiles%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="ae9a6-670">%ProgramFiles%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="ae9a6-671">% ProgramFiles (x86)% \ IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="ae9a6-671">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="ae9a6-672">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="ae9a6-672">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="ae9a6-673">% ProgramFiles (x86)% \ IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="ae9a6-673">%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

### <a name="schema"></a><span data-ttu-id="ae9a6-674">Schéma</span><span class="sxs-lookup"><span data-stu-id="ae9a6-674">Schema</span></span>

<span data-ttu-id="ae9a6-675">**IIS**</span><span class="sxs-lookup"><span data-stu-id="ae9a6-675">**IIS**</span></span>

* <span data-ttu-id="ae9a6-676">%windir%\System32\inetsrv\config\schema\ aspnetcore_schema. XML</span><span class="sxs-lookup"><span data-stu-id="ae9a6-676">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span></span>

* <span data-ttu-id="ae9a6-677">%windir%\System32\inetsrv\config\schema\ aspnetcore_schema_v2. XML</span><span class="sxs-lookup"><span data-stu-id="ae9a6-677">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span></span>

<span data-ttu-id="ae9a6-678">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="ae9a6-678">**IIS Express**</span></span>

* <span data-ttu-id="ae9a6-679">%ProgramFiles%\IIS Express\config\schema\ aspnetcore_schema. XML</span><span class="sxs-lookup"><span data-stu-id="ae9a6-679">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span></span>

* <span data-ttu-id="ae9a6-680">%ProgramFiles%\IIS Express\config\schema\ aspnetcore_schema_v2. XML</span><span class="sxs-lookup"><span data-stu-id="ae9a6-680">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span></span>

### <a name="configuration"></a><span data-ttu-id="ae9a6-681">Konfigurace</span><span class="sxs-lookup"><span data-stu-id="ae9a6-681">Configuration</span></span>

<span data-ttu-id="ae9a6-682">**IIS**</span><span class="sxs-lookup"><span data-stu-id="ae9a6-682">**IIS**</span></span>

* <span data-ttu-id="ae9a6-683">%windir%\System32\inetsrv\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="ae9a6-683">%windir%\System32\inetsrv\config\applicationHost.config</span></span>

<span data-ttu-id="ae9a6-684">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="ae9a6-684">**IIS Express**</span></span>

* <span data-ttu-id="ae9a6-685">Visual Studio: {ROOT aplikace} \\ . vs\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="ae9a6-685">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span></span>

* <span data-ttu-id="ae9a6-686">*IISExpress. exe* CLI:%USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span><span class="sxs-lookup"><span data-stu-id="ae9a6-686">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span></span>

<span data-ttu-id="ae9a6-687">Soubory lze najít hledáním *aspnetcore* v souboru *ApplicationHost. config* .</span><span class="sxs-lookup"><span data-stu-id="ae9a6-687">The files can be found by searching for *aspnetcore* in the *applicationHost.config* file.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="ae9a6-688">Modul ASP.NET Core je nativním modulem služby IIS, který se připojuje k kanálu IIS a přesměrovává webové požadavky na back-endové aplikace ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-688">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to forward web requests to backend ASP.NET Core apps.</span></span>

<span data-ttu-id="ae9a6-689">Podporované verze systému Windows:</span><span class="sxs-lookup"><span data-stu-id="ae9a6-689">Supported Windows versions:</span></span>

* <span data-ttu-id="ae9a6-690">Windows 7 nebo novější</span><span class="sxs-lookup"><span data-stu-id="ae9a6-690">Windows 7 or later</span></span>
* <span data-ttu-id="ae9a6-691">Windows Server 2008 R2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="ae9a6-691">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="ae9a6-692">Modul funguje pouze s Kestrel.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-692">The module only works with Kestrel.</span></span> <span data-ttu-id="ae9a6-693">Modul není kompatibilní s [http. sys](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="ae9a6-693">The module is incompatible with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

<span data-ttu-id="ae9a6-694">Vzhledem k tomu, že ASP.NET Core aplikace běží v procesu odděleném od pracovního procesu služby IIS, modul také zpracovává správu procesů.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-694">Because ASP.NET Core apps run in a process separate from the IIS worker process, the module also handles process management.</span></span> <span data-ttu-id="ae9a6-695">Modul spustí proces pro aplikaci ASP.NET Core, když první požadavek dorazí a restartuje aplikaci, pokud dojde k chybě.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-695">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it crashes.</span></span> <span data-ttu-id="ae9a6-696">To je v podstatě stejné chování jako u aplikací ASP.NET 4. x spouštěných vnitroprocesově ve službě IIS, které spravuje [Aktivační služba procesů systému Windows (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span><span class="sxs-lookup"><span data-stu-id="ae9a6-696">This is essentially the same behavior as seen with ASP.NET 4.x apps that run in-process in IIS that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="ae9a6-697">Následující diagram znázorňuje vztah mezi službou IIS, modulem ASP.NET Core a aplikací:</span><span class="sxs-lookup"><span data-stu-id="ae9a6-697">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app:</span></span>

![Modul ASP.NET Core](aspnet-core-module/_static/ancm-outofprocess.png)

<span data-ttu-id="ae9a6-699">Požadavky přicházející z webu do ovladače HTTP. sys v režimu jádra.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-699">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="ae9a6-700">Ovladač směruje požadavky do služby IIS na konfigurovaném portu webu, obvykle 80 (HTTP) nebo 443 (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="ae9a6-700">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="ae9a6-701">Modul předá požadavky do Kestrel na náhodném portu pro aplikaci, což není port 80 nebo 443.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-701">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="ae9a6-702">Modul Určuje port prostřednictvím proměnné prostředí při spuštění a [Služba IIS Integration middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) nakonfiguruje server, na kterém má naslouchat `http://localhost:{port}` .</span><span class="sxs-lookup"><span data-stu-id="ae9a6-702">The module specifies the port via an environment variable at startup, and the [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) configures the server to listen on `http://localhost:{port}`.</span></span> <span data-ttu-id="ae9a6-703">Budou provedeny další kontroly a požadavky, které nepocházejí z modulu, jsou odmítnuty.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-703">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="ae9a6-704">Modul nepodporuje předávání HTTPS, takže požadavky se předávají přes protokol HTTP i v případě, že je služba IIS prostřednictvím protokolu HTTPS přijímá.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-704">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="ae9a6-705">Po Kestrel žádosti z modulu se požadavek odešle do kanálu middlewaru ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-705">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="ae9a6-706">Kanál middlewaru zpracovává požadavek a předá ho jako `HttpContext` instanci do logiky aplikace.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-706">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="ae9a6-707">Middleware přidaný integrací služby IIS: aktualizace schématu, vzdálené IP adresy a pathbase pro přesměrování požadavku do Kestrel.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-707">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="ae9a6-708">Odpověď aplikace se předává zpátky do služby IIS, která ji přenáší zpátky do klienta HTTP, který žádost inicioval.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-708">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="ae9a6-709">Mnoho nativních modulů, jako je ověřování systému Windows, zůstane aktivní.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-709">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="ae9a6-710">Další informace o aktivních modulech služby IIS pomocí modulu ASP.NET Core najdete v tématu <xref:host-and-deploy/iis/modules> .</span><span class="sxs-lookup"><span data-stu-id="ae9a6-710">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="ae9a6-711">Modul ASP.NET Core může také:</span><span class="sxs-lookup"><span data-stu-id="ae9a6-711">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="ae9a6-712">Nastavte proměnné prostředí pro pracovní proces.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-712">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="ae9a6-713">Protokoluje výstup stdout do úložiště souborů pro řešení problémů při spuštění.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-713">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="ae9a6-714">Předejte tokeny ověřování systému Windows.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-714">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="ae9a6-715">Jak nainstalovat a používat modul ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ae9a6-715">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="ae9a6-716">Pokyny k instalaci modulu ASP.NET Core najdete v tématu [instalace hostující sady .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span><span class="sxs-lookup"><span data-stu-id="ae9a6-716">For instructions on how to install the ASP.NET Core Module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="ae9a6-717">Konfigurace pomocí souboru Web. config</span><span class="sxs-lookup"><span data-stu-id="ae9a6-717">Configuration with web.config</span></span>

<span data-ttu-id="ae9a6-718">Modul ASP.NET Core je nakonfigurován s `aspNetCore` částí `system.webServer` uzlu v souboru *Web. config* webu.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-718">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="ae9a6-719">Následující soubor *Web. config* je publikován pro [nasazení závislé na rozhraní](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) a nakonfiguruje modul ASP.NET Core pro zpracování požadavků lokality:</span><span class="sxs-lookup"><span data-stu-id="ae9a6-719">The following *web.config* file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <handlers>
      <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModule" resourceType="Unspecified" />
    </handlers>
    <aspNetCore processPath="dotnet"
                arguments=".\MyApp.dll"
                stdoutLogEnabled="false"
                stdoutLogFile=".\logs\stdout" />
  </system.webServer>
</configuration>
```

<span data-ttu-id="ae9a6-720">Následující soubor *Web. config* je publikován pro [samostatně uzavřené nasazení](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span><span class="sxs-lookup"><span data-stu-id="ae9a6-720">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <handlers>
      <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModule" resourceType="Unspecified" />
    </handlers>
    <aspNetCore processPath=".\MyApp.exe"
                stdoutLogEnabled="false"
                stdoutLogFile=".\logs\stdout" />
  </system.webServer>
</configuration>
```

<span data-ttu-id="ae9a6-721">Když je aplikace nasazená na [Azure App Service](https://azure.microsoft.com/services/app-service/), `stdoutLogFile` cesta je nastavená na `\\?\%home%\LogFiles\stdout` .</span><span class="sxs-lookup"><span data-stu-id="ae9a6-721">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="ae9a6-722">Cesta ukládá protokoly stdout do složky *soubory protokolů* , což je umístění, které služba automaticky vytvořila.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-722">The path saves stdout logs to the *LogFiles* folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="ae9a6-723">Informace o konfiguraci dílčí aplikace IIS najdete v tématu <xref:host-and-deploy/iis/index#sub-applications> .</span><span class="sxs-lookup"><span data-stu-id="ae9a6-723">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="ae9a6-724">Atributy elementu aspNetCore</span><span class="sxs-lookup"><span data-stu-id="ae9a6-724">Attributes of the aspNetCore element</span></span>

| <span data-ttu-id="ae9a6-725">Atribut</span><span class="sxs-lookup"><span data-stu-id="ae9a6-725">Attribute</span></span> | <span data-ttu-id="ae9a6-726">Description</span><span class="sxs-lookup"><span data-stu-id="ae9a6-726">Description</span></span> | <span data-ttu-id="ae9a6-727">Výchozí</span><span class="sxs-lookup"><span data-stu-id="ae9a6-727">Default</span></span> |
| ---
<span data-ttu-id="ae9a6-728">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ae9a6-728">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ae9a6-729">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ae9a6-729">'Blazor'</span></span>
- <span data-ttu-id="ae9a6-730">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ae9a6-730">'Identity'</span></span>
- <span data-ttu-id="ae9a6-731">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ae9a6-731">'Let's Encrypt'</span></span>
- <span data-ttu-id="ae9a6-732">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ae9a6-732">'Razor'</span></span>
- <span data-ttu-id="ae9a6-733">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="ae9a6-733">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ae9a6-734">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ae9a6-734">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ae9a6-735">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ae9a6-735">'Blazor'</span></span>
- <span data-ttu-id="ae9a6-736">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ae9a6-736">'Identity'</span></span>
- <span data-ttu-id="ae9a6-737">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ae9a6-737">'Let's Encrypt'</span></span>
- <span data-ttu-id="ae9a6-738">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ae9a6-738">'Razor'</span></span>
- <span data-ttu-id="ae9a6-739">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="ae9a6-739">'SignalR' uid:</span></span> 

<span data-ttu-id="ae9a6-740">----- | ---Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ae9a6-740">----- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ae9a6-741">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ae9a6-741">'Blazor'</span></span>
- <span data-ttu-id="ae9a6-742">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ae9a6-742">'Identity'</span></span>
- <span data-ttu-id="ae9a6-743">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ae9a6-743">'Let's Encrypt'</span></span>
- <span data-ttu-id="ae9a6-744">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ae9a6-744">'Razor'</span></span>
- <span data-ttu-id="ae9a6-745">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="ae9a6-745">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ae9a6-746">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ae9a6-746">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ae9a6-747">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ae9a6-747">'Blazor'</span></span>
- <span data-ttu-id="ae9a6-748">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ae9a6-748">'Identity'</span></span>
- <span data-ttu-id="ae9a6-749">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ae9a6-749">'Let's Encrypt'</span></span>
- <span data-ttu-id="ae9a6-750">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ae9a6-750">'Razor'</span></span>
- <span data-ttu-id="ae9a6-751">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="ae9a6-751">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ae9a6-752">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ae9a6-752">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ae9a6-753">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ae9a6-753">'Blazor'</span></span>
- <span data-ttu-id="ae9a6-754">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ae9a6-754">'Identity'</span></span>
- <span data-ttu-id="ae9a6-755">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ae9a6-755">'Let's Encrypt'</span></span>
- <span data-ttu-id="ae9a6-756">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ae9a6-756">'Razor'</span></span>
- <span data-ttu-id="ae9a6-757">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="ae9a6-757">'SignalR' uid:</span></span> 

<span data-ttu-id="ae9a6-758">------ | :-----: | | `arguments` | </span><span class="sxs-lookup"><span data-stu-id="ae9a6-758">------ | :-----: | | `arguments` | </span></span><p><span data-ttu-id="ae9a6-759">Volitelný řetězcový atribut.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-759">Optional string attribute.</span></span></p><p><span data-ttu-id="ae9a6-760">Argumenty ke spustitelnému souboru určenému v **processPath**.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-760">Arguments to the executable specified in **processPath**.</span></span></p><span data-ttu-id="ae9a6-761">| | | `disableStartUpErrorPage` | </span><span class="sxs-lookup"><span data-stu-id="ae9a6-761">| | | `disableStartUpErrorPage` | </span></span><p><span data-ttu-id="ae9a6-762">Volitelný atribut typu Boolean.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-762">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="ae9a6-763">Pokud je nastaveno na true, stránka **502,5 – selhání procesu** se potlačí a stavová stránka 502 konfigurovaná v *souboru Web. config* má přednost.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-763">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> <span data-ttu-id="ae9a6-764">| `false` | | `forwardWindowsAuthToken` | </span><span class="sxs-lookup"><span data-stu-id="ae9a6-764">| `false` | | `forwardWindowsAuthToken` | </span></span><p><span data-ttu-id="ae9a6-765">Volitelný atribut typu Boolean.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-765">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="ae9a6-766">Pokud je nastaveno na true, token se přepošle podřízenému procesu, který naslouchá na% ASPNETCORE_PORT% jako záhlaví MS-ASPNETCORE-WINAUTHTOKEN na žádost.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-766">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="ae9a6-767">Je zodpovědností za tento proces volání CloseHandle na tento token na žádost.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-767">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> <span data-ttu-id="ae9a6-768">| `true` | | `processesPerApplication` | </span><span class="sxs-lookup"><span data-stu-id="ae9a6-768">| `true` | | `processesPerApplication` | </span></span><p><span data-ttu-id="ae9a6-769">Volitelný celočíselný atribut</span><span class="sxs-lookup"><span data-stu-id="ae9a6-769">Optional integer attribute.</span></span></p><p><span data-ttu-id="ae9a6-770">Určuje počet instancí procesu určeného v nastavení **processPath** , které lze v rámci aplikace vystavit.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-770">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="ae9a6-771">Nastavení `processesPerApplication` se nedoporučuje.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-771">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="ae9a6-772">Tento atribut bude v budoucí verzi odebrán.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-772">This attribute will be removed in a future release.</span></span></p> <span data-ttu-id="ae9a6-773">| Výchozí`1`</span><span class="sxs-lookup"><span data-stu-id="ae9a6-773">| Default: `1`</span></span><br><span data-ttu-id="ae9a6-774">Dlouhé`1`</span><span class="sxs-lookup"><span data-stu-id="ae9a6-774">Min: `1`</span></span><br><span data-ttu-id="ae9a6-775">Max: `100` | |`processPath` | </span><span class="sxs-lookup"><span data-stu-id="ae9a6-775">Max: `100` | | `processPath` | </span></span><p><span data-ttu-id="ae9a6-776">Povinný atribut řetězce.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-776">Required string attribute.</span></span></p><p><span data-ttu-id="ae9a6-777">Cesta ke spustitelnému souboru, který spouští proces naslouchající na požadavky HTTP.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-777">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="ae9a6-778">Jsou podporovány relativní cesty.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-778">Relative paths are supported.</span></span> <span data-ttu-id="ae9a6-779">Pokud cesta začíná `.` , bude tato cesta považována za relativní vzhledem k kořenu webu.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-779">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> <span data-ttu-id="ae9a6-780">| | | `rapidFailsPerMinute` | </span><span class="sxs-lookup"><span data-stu-id="ae9a6-780">| | | `rapidFailsPerMinute` | </span></span><p><span data-ttu-id="ae9a6-781">Volitelný celočíselný atribut</span><span class="sxs-lookup"><span data-stu-id="ae9a6-781">Optional integer attribute.</span></span></p><p><span data-ttu-id="ae9a6-782">Určuje, kolikrát je povoleno selhání procesu určeného v **processPath** za minutu.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-782">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="ae9a6-783">Pokud je tento limit překročen, modul ukončí spuštění procesu po zbytek minuty.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-783">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p> <span data-ttu-id="ae9a6-784">| Výchozí`10`</span><span class="sxs-lookup"><span data-stu-id="ae9a6-784">| Default: `10`</span></span><br><span data-ttu-id="ae9a6-785">Dlouhé`0`</span><span class="sxs-lookup"><span data-stu-id="ae9a6-785">Min: `0`</span></span><br><span data-ttu-id="ae9a6-786">Max: `100` | |`requestTimeout` | </span><span class="sxs-lookup"><span data-stu-id="ae9a6-786">Max: `100` | | `requestTimeout` | </span></span><p><span data-ttu-id="ae9a6-787">Volitelný atribut TimeSpan.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-787">Optional timespan attribute.</span></span></p><p><span data-ttu-id="ae9a6-788">Určuje dobu, po kterou modul ASP.NET Core čeká na odpověď od procesu, který naslouchá na% ASPNETCORE_PORT%.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-788">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="ae9a6-789">Ve verzích modulu ASP.NET Core, které byly dodávány s vydáním ASP.NET Core 2,1 nebo vyšší, `requestTimeout` je určena v hodinách, minutách a sekundách.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-789">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p> <span data-ttu-id="ae9a6-790">| Výchozí`00:02:00`</span><span class="sxs-lookup"><span data-stu-id="ae9a6-790">| Default: `00:02:00`</span></span><br><span data-ttu-id="ae9a6-791">Dlouhé`00:00:00`</span><span class="sxs-lookup"><span data-stu-id="ae9a6-791">Min: `00:00:00`</span></span><br><span data-ttu-id="ae9a6-792">Max: `360:00:00` | |`shutdownTimeLimit` | </span><span class="sxs-lookup"><span data-stu-id="ae9a6-792">Max: `360:00:00` | | `shutdownTimeLimit` | </span></span><p><span data-ttu-id="ae9a6-793">Volitelný celočíselný atribut</span><span class="sxs-lookup"><span data-stu-id="ae9a6-793">Optional integer attribute.</span></span></p><p><span data-ttu-id="ae9a6-794">Doba v sekundách, po kterou modul čeká na řádné vypnutí spustitelného souboru, když se zjistí soubor *App_offline. htm* .</span><span class="sxs-lookup"><span data-stu-id="ae9a6-794">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> <span data-ttu-id="ae9a6-795">| Výchozí`10`</span><span class="sxs-lookup"><span data-stu-id="ae9a6-795">| Default: `10`</span></span><br><span data-ttu-id="ae9a6-796">Dlouhé`0`</span><span class="sxs-lookup"><span data-stu-id="ae9a6-796">Min: `0`</span></span><br><span data-ttu-id="ae9a6-797">Max: `600` | |`startupTimeLimit` | </span><span class="sxs-lookup"><span data-stu-id="ae9a6-797">Max: `600` | | `startupTimeLimit` | </span></span><p><span data-ttu-id="ae9a6-798">Volitelný celočíselný atribut</span><span class="sxs-lookup"><span data-stu-id="ae9a6-798">Optional integer attribute.</span></span></p><p><span data-ttu-id="ae9a6-799">Doba v sekundách, kterou modul počká, než spustitelný soubor spustí proces, který na portu naslouchá.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-799">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="ae9a6-800">Pokud je tento časový limit překročen, modul proces ukončuje.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-800">If this time limit is exceeded, the module kills the process.</span></span> <span data-ttu-id="ae9a6-801">Modul se pokusí znovu spustit proces, když obdrží novou žádost, a pokračuje v pokusu o restartování procesu u dalších příchozích požadavků, pokud se nepodaří spustit **rapidFailsPerMinute** počet pokusů během poslední minuty.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-801">The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="ae9a6-802">Hodnota 0 (nula **) není považována za** nekonečný časový limit.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-802">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> <span data-ttu-id="ae9a6-803">| Výchozí`120`</span><span class="sxs-lookup"><span data-stu-id="ae9a6-803">| Default: `120`</span></span><br><span data-ttu-id="ae9a6-804">Dlouhé`0`</span><span class="sxs-lookup"><span data-stu-id="ae9a6-804">Min: `0`</span></span><br><span data-ttu-id="ae9a6-805">Max: `3600` | |`stdoutLogEnabled` | </span><span class="sxs-lookup"><span data-stu-id="ae9a6-805">Max: `3600` | | `stdoutLogEnabled` | </span></span><p><span data-ttu-id="ae9a6-806">Volitelný atribut typu Boolean.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-806">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="ae9a6-807">Je-li nastavena hodnota true, **stdout** a **stderr** pro proces zadaný v **processPath** budou přesměrovány do souboru zadaného v **stdoutLogFile**.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-807">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> <span data-ttu-id="ae9a6-808">| `false` | | `stdoutLogFile` | </span><span class="sxs-lookup"><span data-stu-id="ae9a6-808">| `false` | | `stdoutLogFile` | </span></span><p><span data-ttu-id="ae9a6-809">Volitelný řetězcový atribut.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-809">Optional string attribute.</span></span></p><p><span data-ttu-id="ae9a6-810">Určuje relativní nebo absolutní cestu k souboru, pro který je **stdout** a **stderr** z procesu určeného v **processPath** protokolováno.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-810">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="ae9a6-811">Relativní cesty jsou relativní vzhledem k kořenu webu.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-811">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="ae9a6-812">Každá cesta začínající řetězcem `.` je relativní vzhledem k kořenu webu a všechny ostatní cesty se považují za absolutní cesty.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-812">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="ae9a6-813">Aby modul vytvořil soubor protokolu, musí existovat všechny složky, které jsou v cestě k dispozici.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-813">Any folders provided in the path must exist in order for the module to create the log file.</span></span> <span data-ttu-id="ae9a6-814">Pomocí oddělovačů podtržítka se do posledního segmentu cesty **stdoutLogFile** přidá časové razítko, ID procesu a Přípona souboru (*. log*).</span><span class="sxs-lookup"><span data-stu-id="ae9a6-814">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="ae9a6-815">Pokud `.\logs\stdout` je zadán jako hodnota, je ukázkový protokol stdout uložen jako *stdout_20180205194132_1934. log* ve složce *logs* při uložení na 2/5/2018 v 19:41:32 s ID procesu 1934.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-815">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

### <a name="setting-environment-variables"></a><span data-ttu-id="ae9a6-816">Nastavení proměnných prostředí</span><span class="sxs-lookup"><span data-stu-id="ae9a6-816">Setting environment variables</span></span>

<span data-ttu-id="ae9a6-817">Proměnné prostředí lze zadat pro proces v `processPath` atributu.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-817">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="ae9a6-818">Zadejte proměnnou prostředí s `<environmentVariable>` podřízeným elementem `<environmentVariables>` elementu kolekce.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-818">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span>

> [!WARNING]
> <span data-ttu-id="ae9a6-819">Proměnné prostředí nastavené v této části jsou v konfliktu se sadou proměnných prostředí systému se stejným názvem.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-819">Environment variables set in this section conflict with system environment variables set with the same name.</span></span> <span data-ttu-id="ae9a6-820">Pokud je proměnná prostředí nastavena v souboru *Web. config* i na úrovni systému systému Windows, bude hodnota ze souboru *Web. config* připojena k hodnotě proměnné prostředí systému (například `ASPNETCORE_ENVIRONMENT: Development;Development` ), což zabrání spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-820">If an environment variable is set in both the *web.config* file and at the system level in Windows, the value from the *web.config* file becomes appended to the system environment variable value (for example, `ASPNETCORE_ENVIRONMENT: Development;Development`), which prevents the app from starting.</span></span>

<span data-ttu-id="ae9a6-821">Následující příklad nastaví dvě proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-821">The following example sets two environment variables.</span></span> <span data-ttu-id="ae9a6-822">`ASPNETCORE_ENVIRONMENT`nakonfiguruje prostředí aplikace na `Development` .</span><span class="sxs-lookup"><span data-stu-id="ae9a6-822">`ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="ae9a6-823">Vývojář může tuto hodnotu dočasně nastavit v souboru *Web. config* , aby bylo možné vynutit, aby se [Stránka s výjimkou vývojářů](xref:fundamentals/error-handling) načetla při ladění výjimky aplikace.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-823">A developer may temporarily set this value in the *web.config* file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="ae9a6-824">`CONFIG_DIR`je příkladem uživatelsky definované proměnné prostředí, kde vývojář napsal kód, který přečte hodnotu při spuštění, aby vytvořil cestu pro načtení konfiguračního souboru aplikace.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-824">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile="\\?\%home%\LogFiles\stdout">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
    <environmentVariable name="CONFIG_DIR" value="f:\application_config" />
  </environmentVariables>
</aspNetCore>
```

> [!WARNING]
> <span data-ttu-id="ae9a6-825">Nastavte pouze `ASPNETCORE_ENVIRONMENT` proměnnou prostředí na `Development` pracovní a testovací servery, které nejsou přístupné nedůvěryhodným sítím, jako je například Internet.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-825">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## <a name="app_offlinehtm"></a><span data-ttu-id="ae9a6-826">app_offline. htm</span><span class="sxs-lookup"><span data-stu-id="ae9a6-826">app_offline.htm</span></span>

<span data-ttu-id="ae9a6-827">Pokud se v kořenovém adresáři aplikace zjistí soubor s názvem *App_offline. htm* , modul ASP.NET Core se pokusí aplikaci řádně vypnout a zastavit zpracování příchozích požadavků.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-827">If a file with the name *app_offline.htm* is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="ae9a6-828">Pokud aplikace běží i po uplynutí počtu sekund definovaného v `shutdownTimeLimit` , modul ASP.NET Core ukončuje běžící proces.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-828">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="ae9a6-829">Když je přítomen soubor *App_offline. htm* , ASP.NET Core modul reaguje na požadavky odesláním zpět obsahu souboru *App_offline. htm* .</span><span class="sxs-lookup"><span data-stu-id="ae9a6-829">While the *app_offline.htm* file is present, the ASP.NET Core Module responds to requests by sending back the contents of the *app_offline.htm* file.</span></span> <span data-ttu-id="ae9a6-830">Po odebrání souboru *App_offline. htm* aplikace spustí další požadavek.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-830">When the *app_offline.htm* file is removed, the next request starts the app.</span></span>

## <a name="start-up-error-page"></a><span data-ttu-id="ae9a6-831">Spouštěcí chybová stránka</span><span class="sxs-lookup"><span data-stu-id="ae9a6-831">Start-up error page</span></span>

<span data-ttu-id="ae9a6-832">Pokud se v modulu ASP.NET Core nepodaří spustit back-end proces nebo se spustí back-end proces, ale nenaslouchá na konfigurovaném portu, zobrazí se stránka s kódem stavu *selhání procesu 502,5* .</span><span class="sxs-lookup"><span data-stu-id="ae9a6-832">If the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span> <span data-ttu-id="ae9a6-833">Chcete-li tuto stránku potlačit a vrátit se na výchozí stavovou stránku služby IIS 502, použijte `disableStartUpErrorPage` atribut.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-833">To suppress this page and revert to the default IIS 502 status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="ae9a6-834">Další informace o konfiguraci vlastních chybových zpráv najdete v tématu [chyby \<httpErrors> protokolu HTTP ](/iis/configuration/system.webServer/httpErrors/).</span><span class="sxs-lookup"><span data-stu-id="ae9a6-834">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

![Stavová stránka selhání procesu procesu 502,5](aspnet-core-module/_static/ANCM-502_5.png)

## <a name="log-creation-and-redirection"></a><span data-ttu-id="ae9a6-836">Vytvoření a přesměrování protokolu</span><span class="sxs-lookup"><span data-stu-id="ae9a6-836">Log creation and redirection</span></span>

<span data-ttu-id="ae9a6-837">Modul ASP.NET Core přesměruje výstup konzoly stdout a stderr na disk, pokud `stdoutLogEnabled` `stdoutLogFile` `aspNetCore` jsou nastaveny atributy a elementu.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-837">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="ae9a6-838">`stdoutLogFile`Při vytvoření souboru protokolu jsou všechny složky v této cestě vytvářeny modulem.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-838">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="ae9a6-839">Fond aplikací musí mít oprávnění k zápisu do umístění, kam se protokoly zapisují (použijte `IIS AppPool\<app_pool_name>` k poskytnutí oprávnění k zápisu).</span><span class="sxs-lookup"><span data-stu-id="ae9a6-839">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="ae9a6-840">Protokoly nejsou otočeny, pokud nedochází k recyklování/restartování procesu.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-840">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="ae9a6-841">Je zodpovědností hostitele omezit místo na disku, které používají protokoly.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-841">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="ae9a6-842">Použití protokolu stdout se doporučuje pouze při řešení potíží se spouštěním aplikací při hostování služby IIS nebo při použití [podpory v době vývoje služby IIS se sadou Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), nikoli při ladění místně a při spuštění aplikace s IIS Express.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-842">Using the stdout log is only recommended for troubleshooting app startup issues when hosting on IIS or when using [development-time support for IIS with Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), not while debugging locally and running the app with IIS Express.</span></span>

<span data-ttu-id="ae9a6-843">Nepoužívejte protokol stdout pro obecné účely protokolování aplikace.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-843">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="ae9a6-844">Pro rutinu protokolování v aplikaci ASP.NET Core použijte knihovnu protokolování, která omezuje velikost souboru protokolu a otočí protokoly.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-844">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="ae9a6-845">Další informace najdete v tématu [Zprostředkovatelé protokolování třetích stran](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="ae9a6-845">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="ae9a6-846">Časové razítko a Přípona souboru se automaticky přidají při vytvoření souboru protokolu.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-846">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="ae9a6-847">Název souboru protokolu se skládá připojením časového razítka, ID procesu a přípony souboru (*. log*) k poslednímu segmentu `stdoutLogFile` cesty (obvykle *stdout*), oddělený podtržítky.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-847">The log file name is composed by appending the timestamp, process ID, and file extension (*.log*) to the last segment of the `stdoutLogFile` path (typically *stdout*) delimited by underscores.</span></span> <span data-ttu-id="ae9a6-848">Pokud `stdoutLogFile` cesta končí na *stdout*, protokol pro aplikaci s číslem PID 1934 vytvořeným v 2/5/2018 v 19:42:32 má název souboru *stdout_20180205194132_1934. log*.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-848">If the `stdoutLogFile` path ends with *stdout*, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name *stdout_20180205194132_1934.log*.</span></span>

<span data-ttu-id="ae9a6-849">Následující vzorový `aspNetCore` prvek nakonfiguruje protokolování stdout na relativní cestě `.\log\` .</span><span class="sxs-lookup"><span data-stu-id="ae9a6-849">The following sample `aspNetCore` element configures stdout logging at the relative path `.\log\`.</span></span> <span data-ttu-id="ae9a6-850">Potvrďte, že identita uživatele fondu aplikací má oprávnění k zápisu do zadané cesty.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-850">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout">
</aspNetCore>
```

<span data-ttu-id="ae9a6-851">Když publikujete aplikaci pro nasazení Azure App Service, sada web SDK nastaví `stdoutLogFile` hodnotu na `\\?\%home%\LogFiles\stdout` .</span><span class="sxs-lookup"><span data-stu-id="ae9a6-851">When publishing an app for Azure App Service deployment, the Web SDK sets the `stdoutLogFile` value to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="ae9a6-852">`%home`Proměnná prostředí je předdefinovaná pro aplikace hostované v Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-852">The `%home` environment variable is predefined for apps hosted by Azure App Service.</span></span>

<span data-ttu-id="ae9a6-853">Chcete-li vytvořit pravidla filtru protokolování, přečtěte si část [Konfigurace](xref:fundamentals/logging/index#log-filtering) a [filtrování protokolů](xref:fundamentals/logging/index#log-filtering) v dokumentaci ASP.NET Core protokolování.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-853">To create logging filter rules, see the [Configuration](xref:fundamentals/logging/index#log-filtering) and [Log filtering](xref:fundamentals/logging/index#log-filtering) sections of the ASP.NET Core logging documentation.</span></span>

<span data-ttu-id="ae9a6-854">Další informace o formátech cest najdete v tématu [formáty souborů cest v systémech Windows](/dotnet/standard/io/file-path-formats).</span><span class="sxs-lookup"><span data-stu-id="ae9a6-854">For more information on path formats, see [File path formats on Windows systems](/dotnet/standard/io/file-path-formats).</span></span>

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="ae9a6-855">Konfigurace proxy serveru používá protokol HTTP a token párování.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-855">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="ae9a6-856">Proxy vytvořený mezi modulem ASP.NET Core a Kestrel používá protokol HTTP.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-856">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="ae9a6-857">Nehrozí riziko odposlouchávání provozu mezi modulem a Kestrel z umístění od serveru.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-857">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="ae9a6-858">Párovací token se používá k zajištění, že žádosti přijaté službou Kestrel byly proxy službou IIS a nepocházejí z jiného zdroje.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-858">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="ae9a6-859">Token párování se vytvoří a nastaví na proměnnou prostředí ( `ASPNETCORE_TOKEN` ) modulem.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-859">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="ae9a6-860">Párovací token je také nastaven na hlavičku ( `MS-ASPNETCORE-TOKEN` ) pro každý požadavek proxy.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-860">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="ae9a6-861">Middleware služby IIS kontroluje každý požadavek, který obdrží, a potvrdí, že hodnota hlavičky párového tokenu odpovídá hodnotě proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-861">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="ae9a6-862">Pokud se hodnoty tokenu neshodují, požadavek se zaprotokoluje a odmítne.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-862">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="ae9a6-863">Proměnná prostředí párového tokenu a přenos dat mezi modulem a Kestrel nejsou přístupné z umístění mimo server.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-863">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="ae9a6-864">Bez znalosti hodnoty párovacího tokenu nemůže útočník odeslat žádosti, které obcházejí kontrolu v Middlewari IIS.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-864">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="ae9a6-865">ASP.NET Core modul se sdílenou konfigurací služby IIS</span><span class="sxs-lookup"><span data-stu-id="ae9a6-865">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="ae9a6-866">Instalační služba modulu ASP.NET Core se spouští s oprávněními účtu **TrustedInstaller** .</span><span class="sxs-lookup"><span data-stu-id="ae9a6-866">The ASP.NET Core Module installer runs with the privileges of the **TrustedInstaller** account.</span></span> <span data-ttu-id="ae9a6-867">Vzhledem k tomu, že účet místního systému nemá oprávnění k úpravám pro cestu ke sdílené složce, kterou používá sdílená konfigurace služby IIS, vyvolá Instalační program chybu odepření přístupu při pokusu o konfiguraci nastavení modulu v souboru *ApplicationHost. config* ve sdílené složce.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-867">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the *applicationHost.config* file on the share.</span></span>

<span data-ttu-id="ae9a6-868">Pokud používáte sdílenou konfiguraci služby IIS, postupujte podle těchto kroků:</span><span class="sxs-lookup"><span data-stu-id="ae9a6-868">When using an IIS Shared Configuration, follow these steps:</span></span>

1. <span data-ttu-id="ae9a6-869">Zakažte sdílenou konfiguraci služby IIS.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-869">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="ae9a6-870">Spusťte instalační program.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-870">Run the installer.</span></span>
1. <span data-ttu-id="ae9a6-871">Exportujte aktualizovaný soubor *ApplicationHost. config* do sdílené složky.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-871">Export the updated *applicationHost.config* file to the share.</span></span>
1. <span data-ttu-id="ae9a6-872">Znovu povolte sdílenou konfiguraci služby IIS.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-872">Re-enable the IIS Shared Configuration.</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="ae9a6-873">Verze modulu a instalační protokoly hostitelské sady</span><span class="sxs-lookup"><span data-stu-id="ae9a6-873">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="ae9a6-874">Postup určení verze nainstalovaného modulu ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="ae9a6-874">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="ae9a6-875">V hostitelském systému přejděte na *%windir%\system32\inetsrv*.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-875">On the hosting system, navigate to *%windir%\System32\inetsrv*.</span></span>
1. <span data-ttu-id="ae9a6-876">Vyhledejte soubor *aspnetcore. dll* .</span><span class="sxs-lookup"><span data-stu-id="ae9a6-876">Locate the *aspnetcore.dll* file.</span></span>
1. <span data-ttu-id="ae9a6-877">Klikněte na soubor pravým tlačítkem a v místní nabídce vyberte **vlastnosti** .</span><span class="sxs-lookup"><span data-stu-id="ae9a6-877">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="ae9a6-878">Vyberte kartu **Podrobnosti** . **Verze souboru** a **verze produktu** reprezentují nainstalovanou verzi modulu.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-878">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="ae9a6-879">Protokoly instalačních balíčků pro modul se nacházejí v *C: \\ Uživatelé \\ % UserName% \\ data \\ Local \\ TEMP*. Soubor má název *dd_DotNetCoreWinSvrHosting__ \<timestamp> _000_AspNetCoreModule_x64. log*.</span><span class="sxs-lookup"><span data-stu-id="ae9a6-879">The Hosting Bundle installer logs for the module are found at *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. The file is named *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="ae9a6-880">Umístění souboru modulu, schématu a konfiguračního souboru</span><span class="sxs-lookup"><span data-stu-id="ae9a6-880">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="ae9a6-881">Modul</span><span class="sxs-lookup"><span data-stu-id="ae9a6-881">Module</span></span>

<span data-ttu-id="ae9a6-882">**Služba IIS (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="ae9a6-882">**IIS (x86/amd64):**</span></span>

* <span data-ttu-id="ae9a6-883">%windir%\System32\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="ae9a6-883">%windir%\System32\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="ae9a6-884">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="ae9a6-884">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span></span>

<span data-ttu-id="ae9a6-885">**IIS Express (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="ae9a6-885">**IIS Express (x86/amd64):**</span></span>

* <span data-ttu-id="ae9a6-886">%ProgramFiles%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="ae9a6-886">%ProgramFiles%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="ae9a6-887">% ProgramFiles (x86)% \ IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="ae9a6-887">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span></span>

### <a name="schema"></a><span data-ttu-id="ae9a6-888">Schéma</span><span class="sxs-lookup"><span data-stu-id="ae9a6-888">Schema</span></span>

<span data-ttu-id="ae9a6-889">**IIS**</span><span class="sxs-lookup"><span data-stu-id="ae9a6-889">**IIS**</span></span>

* <span data-ttu-id="ae9a6-890">%windir%\System32\inetsrv\config\schema\ aspnetcore_schema. XML</span><span class="sxs-lookup"><span data-stu-id="ae9a6-890">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span></span>

<span data-ttu-id="ae9a6-891">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="ae9a6-891">**IIS Express**</span></span>

* <span data-ttu-id="ae9a6-892">%ProgramFiles%\IIS Express\config\schema\ aspnetcore_schema. XML</span><span class="sxs-lookup"><span data-stu-id="ae9a6-892">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span></span>

### <a name="configuration"></a><span data-ttu-id="ae9a6-893">Konfigurace</span><span class="sxs-lookup"><span data-stu-id="ae9a6-893">Configuration</span></span>

<span data-ttu-id="ae9a6-894">**IIS**</span><span class="sxs-lookup"><span data-stu-id="ae9a6-894">**IIS**</span></span>

* <span data-ttu-id="ae9a6-895">%windir%\System32\inetsrv\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="ae9a6-895">%windir%\System32\inetsrv\config\applicationHost.config</span></span>

<span data-ttu-id="ae9a6-896">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="ae9a6-896">**IIS Express**</span></span>

* <span data-ttu-id="ae9a6-897">Visual Studio: {ROOT aplikace} \\ . vs\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="ae9a6-897">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span></span>

* <span data-ttu-id="ae9a6-898">*IISExpress. exe* CLI:%USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span><span class="sxs-lookup"><span data-stu-id="ae9a6-898">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span></span>

<span data-ttu-id="ae9a6-899">Soubory lze najít hledáním *aspnetcore* v souboru *ApplicationHost. config* .</span><span class="sxs-lookup"><span data-stu-id="ae9a6-899">The files can be found by searching for *aspnetcore* in the *applicationHost.config* file.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="ae9a6-900">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="ae9a6-900">Additional resources</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/azure-apps/index>
* <span data-ttu-id="ae9a6-901">[Zdroj odkazu na modul ASP.NET Core (hlavní větev)](https://github.com/dotnet/aspnetcore/tree/master/src/Servers/IIS/AspNetCoreModuleV2): k výběru konkrétní verze (například) použijte rozevírací seznam **větev** `release/3.1` .</span><span class="sxs-lookup"><span data-stu-id="ae9a6-901">[ASP.NET Core Module reference source (master branch)](https://github.com/dotnet/aspnetcore/tree/master/src/Servers/IIS/AspNetCoreModuleV2): Use the **Branch** drop down list to select a specific release (for example, `release/3.1`).</span></span>
* <xref:host-and-deploy/iis/modules>
