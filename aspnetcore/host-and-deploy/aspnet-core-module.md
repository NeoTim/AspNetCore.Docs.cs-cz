---
title: Modul ASP.NET Core
author: guardrex
description: Naučte se konfigurovat modul ASP.NET Core pro hostování ASP.NET Corech aplikací.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/13/2019
uid: host-and-deploy/aspnet-core-module
ms.openlocfilehash: 917ee462a8f9592120685b53d059a661cb4a7452
ms.sourcegitcommit: 07d98ada57f2a5f6d809d44bdad7a15013109549
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2019
ms.locfileid: "72333896"
---
# <a name="aspnet-core-module"></a><span data-ttu-id="f3b3d-103">Modul ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f3b3d-103">ASP.NET Core Module</span></span>

<span data-ttu-id="f3b3d-104">Díky [Dykstra](https://github.com/tdykstra), [Rick Strahl](https://github.com/RickStrahl), [Novák Rossův](https://github.com/Tratcher), [Rick Anderson](https://twitter.com/RickAndMSFT), [sourabh Shirhatti](https://twitter.com/sshirhatti), [Justin Kotalik](https://github.com/jkotalik)a [Luke](https://github.com/guardrex) Latham</span><span class="sxs-lookup"><span data-stu-id="f3b3d-104">By [Tom Dykstra](https://github.com/tdykstra), [Rick Strahl](https://github.com/RickStrahl), [Chris Ross](https://github.com/Tratcher), [Rick Anderson](https://twitter.com/RickAndMSFT), [Sourabh Shirhatti](https://twitter.com/sshirhatti), [Justin Kotalik](https://github.com/jkotalik), and [Luke Latham](https://github.com/guardrex)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f3b3d-105">Modul ASP.NET Core je nativním modulem služby IIS, který se připojuje k kanálu IIS na jednu z těchto:</span><span class="sxs-lookup"><span data-stu-id="f3b3d-105">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to either:</span></span>

* <span data-ttu-id="f3b3d-106">Hostování aplikace ASP.NET Core uvnitř pracovního procesu služby IIS (`w3wp.exe`) se nazývá [model hostování v rámci procesu](#in-process-hosting-model).</span><span class="sxs-lookup"><span data-stu-id="f3b3d-106">Host an ASP.NET Core app inside of the IIS worker process (`w3wp.exe`), called the [in-process hosting model](#in-process-hosting-model).</span></span>
* <span data-ttu-id="f3b3d-107">Dopředné webové požadavky do back-endu ASP.NET Core aplikace, na které běží [Server Kestrel](xref:fundamentals/servers/kestrel), se nazývá [model hostování mimo proces](#out-of-process-hosting-model).</span><span class="sxs-lookup"><span data-stu-id="f3b3d-107">Forward web requests to a backend ASP.NET Core app running the [Kestrel server](xref:fundamentals/servers/kestrel), called the [out-of-process hosting model](#out-of-process-hosting-model).</span></span>

<span data-ttu-id="f3b3d-108">Podporované verze systému Windows:</span><span class="sxs-lookup"><span data-stu-id="f3b3d-108">Supported Windows versions:</span></span>

* <span data-ttu-id="f3b3d-109">Windows 7 nebo novější</span><span class="sxs-lookup"><span data-stu-id="f3b3d-109">Windows 7 or later</span></span>
* <span data-ttu-id="f3b3d-110">Windows Server 2008 R2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="f3b3d-110">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="f3b3d-111">Při hostování v procesu používá modul implementaci vnitroprocesového serveru pro službu IIS, která se nazývá IIS HTTP Server (`IISHttpServer`).</span><span class="sxs-lookup"><span data-stu-id="f3b3d-111">When hosting in-process, the module uses an in-process server implementation for IIS, called IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="f3b3d-112">Při hostování mimo proces funguje modul pouze s Kestrel.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-112">When hosting out-of-process, the module only works with Kestrel.</span></span> <span data-ttu-id="f3b3d-113">Modul nefunguje s [ovladačem HTTP. sys](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="f3b3d-113">The module doesn't function with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

## <a name="hosting-models"></a><span data-ttu-id="f3b3d-114">Modely hostingu</span><span class="sxs-lookup"><span data-stu-id="f3b3d-114">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="f3b3d-115">Model hostování v procesu</span><span class="sxs-lookup"><span data-stu-id="f3b3d-115">In-process hosting model</span></span>

<span data-ttu-id="f3b3d-116">ASP.NET Core aplikace jako výchozí pro model hostování v rámci procesu.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-116">ASP.NET Core apps default to the in-process hosting model.</span></span>

<span data-ttu-id="f3b3d-117">Následující vlastnosti platí při hostování v procesu:</span><span class="sxs-lookup"><span data-stu-id="f3b3d-117">The following characteristics apply when hosting in-process:</span></span>

* <span data-ttu-id="f3b3d-118">Místo [Kestrel](xref:fundamentals/servers/kestrel) serveru se používá server HTTP služby IIS (`IISHttpServer`).</span><span class="sxs-lookup"><span data-stu-id="f3b3d-118">IIS HTTP Server (`IISHttpServer`) is used instead of [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span> <span data-ttu-id="f3b3d-119">Pro vnitroprocesové [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) volání <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> do:</span><span class="sxs-lookup"><span data-stu-id="f3b3d-119">For in-process, [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> to:</span></span>

  * <span data-ttu-id="f3b3d-120">Zaregistrujte `IISHttpServer`.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-120">Register the `IISHttpServer`.</span></span>
  * <span data-ttu-id="f3b3d-121">Nakonfigurujte port a základní cestu, na kterých má Server naslouchat při spuštění za modulem ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-121">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
  * <span data-ttu-id="f3b3d-122">Nakonfigurujte hostitele tak, aby zachytával chyby při spuštění.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-122">Configure the host to capture startup errors.</span></span>

* <span data-ttu-id="f3b3d-123">[Atribut RequestTimeout](#attributes-of-the-aspnetcore-element) se nevztahuje na hostování v rámci procesu.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-123">The [requestTimeout attribute](#attributes-of-the-aspnetcore-element) doesn't apply to in-process hosting.</span></span>

* <span data-ttu-id="f3b3d-124">Sdílení fondu aplikací mezi aplikacemi není podporováno.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-124">Sharing an app pool among apps isn't supported.</span></span> <span data-ttu-id="f3b3d-125">Použijte jeden fond aplikací na aplikaci.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-125">Use one app pool per app.</span></span>

* <span data-ttu-id="f3b3d-126">Při použití [nasazení webu](/iis/publish/using-web-deploy/introduction-to-web-deploy) nebo ručnímu umístění [souboru App_offline. htm v nasazení](xref:host-and-deploy/iis/index#locked-deployment-files)se aplikace nemusí okamžitě vypnout, pokud dojde k otevřenému připojení.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-126">When using [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) or manually placing an [app_offline.htm file in the deployment](xref:host-and-deploy/iis/index#locked-deployment-files), the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="f3b3d-127">Připojení k protokolu WebSocket může například zpozdit ukončení aplikace.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-127">For example, a websocket connection may delay app shut down.</span></span>

* <span data-ttu-id="f3b3d-128">Architektura (bitová verze) aplikace a nainstalovaného modulu runtime (x64 nebo x86) se musí shodovat s architekturou fondu aplikací.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-128">The architecture (bitness) of the app and installed runtime (x64 or x86) must match the architecture of the app pool.</span></span>

* <span data-ttu-id="f3b3d-129">Zjistila se odpojení klienta.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-129">Client disconnects are detected.</span></span> <span data-ttu-id="f3b3d-130">Token zrušení [HttpContext. RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) je zrušený, když se klient odpojí.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-130">The [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) cancellation token is cancelled when the client disconnects.</span></span>

* <span data-ttu-id="f3b3d-131">V ASP.NET Core 2.2.1 nebo starším <xref:System.IO.Directory.GetCurrentDirectory*> vrátí pracovní adresář procesu spuštěného službou IIS místo adresáře aplikace (například *C:\Windows\System32\inetsrv* pro *W3wp. exe*).</span><span class="sxs-lookup"><span data-stu-id="f3b3d-131">In ASP.NET Core 2.2.1 or earlier, <xref:System.IO.Directory.GetCurrentDirectory*> returns the worker directory of the process started by IIS rather than the app's directory (for example, *C:\Windows\System32\inetsrv* for *w3wp.exe*).</span></span>

  <span data-ttu-id="f3b3d-132">Vzorový kód, který nastaví aktuální adresář aplikace, najdete v tématu [Třída CurrentDirectoryHelpers](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/3.x/CurrentDirectoryHelpers.cs).</span><span class="sxs-lookup"><span data-stu-id="f3b3d-132">For sample code that sets the app's current directory, see the [CurrentDirectoryHelpers class](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/3.x/CurrentDirectoryHelpers.cs).</span></span> <span data-ttu-id="f3b3d-133">Zavolejte metodu `SetCurrentDirectory`.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-133">Call the `SetCurrentDirectory` method.</span></span> <span data-ttu-id="f3b3d-134">Následná volání <xref:System.IO.Directory.GetCurrentDirectory*> poskytují adresář aplikace.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-134">Subsequent calls to <xref:System.IO.Directory.GetCurrentDirectory*> provide the app's directory.</span></span>

* <span data-ttu-id="f3b3d-135">Při hostování v procesu se nevolá <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> interně pro inicializaci uživatele.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-135">When hosting in-process, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span> <span data-ttu-id="f3b3d-136">Proto implementace <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> používaná k transformaci deklarací po každém ověření není ve výchozím nastavení aktivována.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-136">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="f3b3d-137">Při transformaci deklarací s implementací <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> zavolejte <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> pro přidání ověřovacích služeb:</span><span class="sxs-lookup"><span data-stu-id="f3b3d-137">When transforming claims with an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation, call <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> to add authentication services:</span></span>

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
  
  * <span data-ttu-id="f3b3d-138">[Nasazení webového balíčku (v jednom souboru)](/aspnet/web-forms/overview/deployment/web-deployment-in-the-enterprise/deploying-web-packages) nejsou podporována.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-138">[Web Package (single-file) deployments](/aspnet/web-forms/overview/deployment/web-deployment-in-the-enterprise/deploying-web-packages) aren't supported.</span></span>

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="f3b3d-139">Model hostování mimo proces</span><span class="sxs-lookup"><span data-stu-id="f3b3d-139">Out-of-process hosting model</span></span>

<span data-ttu-id="f3b3d-140">Chcete-li nakonfigurovat aplikaci pro hostování mimo procesy, nastavte hodnotu vlastnosti `<AspNetCoreHostingModel>` na `OutOfProcess` v souboru projektu ( *. csproj*):</span><span class="sxs-lookup"><span data-stu-id="f3b3d-140">To configure an app for out-of-process hosting, set the value of the `<AspNetCoreHostingModel>` property to `OutOfProcess` in the project file (*.csproj*):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="f3b3d-141">Hostování v rámci procesu je nastaveno pomocí `InProcess`, což je výchozí hodnota.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-141">In-process hosting is set with `InProcess`, which is the default value.</span></span>

<span data-ttu-id="f3b3d-142">Místo HTTP serveru IIS (`IISHttpServer`) se používá server [Kestrel](xref:fundamentals/servers/kestrel) .</span><span class="sxs-lookup"><span data-stu-id="f3b3d-142">[Kestrel](xref:fundamentals/servers/kestrel) server is used instead of IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="f3b3d-143">Pro mimoprocesové aplikace [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) volá <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> do:</span><span class="sxs-lookup"><span data-stu-id="f3b3d-143">For out-of-process, [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> to:</span></span>

* <span data-ttu-id="f3b3d-144">Nakonfigurujte port a základní cestu, na kterých má Server naslouchat při spuštění za modulem ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-144">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
* <span data-ttu-id="f3b3d-145">Nakonfigurujte hostitele tak, aby zachytával chyby při spuštění.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-145">Configure the host to capture startup errors.</span></span>

### <a name="hosting-model-changes"></a><span data-ttu-id="f3b3d-146">Změny modelu hostování</span><span class="sxs-lookup"><span data-stu-id="f3b3d-146">Hosting model changes</span></span>

<span data-ttu-id="f3b3d-147">Pokud je nastavení `hostingModel` změněno v souboru *Web. config* (vysvětleno v části [Konfigurace pomocí Web. config](#configuration-with-webconfig) ), modul recykluje pracovní proces pro službu IIS.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-147">If the `hostingModel` setting is changed in the *web.config* file (explained in the [Configuration with web.config](#configuration-with-webconfig) section), the module recycles the worker process for IIS.</span></span>

<span data-ttu-id="f3b3d-148">V případě IIS Express modul neprovádí recyklaci pracovního procesu, ale místo toho aktivuje bezproblémové vypnutí aktuálního procesu IIS Express.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-148">For IIS Express, the module doesn't recycle the worker process but instead triggers a graceful shutdown of the current IIS Express process.</span></span> <span data-ttu-id="f3b3d-149">Další požadavek na aplikaci vytvoří nový proces IIS Express.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-149">The next request to the app spawns a new IIS Express process.</span></span>

### <a name="process-name"></a><span data-ttu-id="f3b3d-150">Název procesu</span><span class="sxs-lookup"><span data-stu-id="f3b3d-150">Process name</span></span>

<span data-ttu-id="f3b3d-151">sestavy `Process.GetCurrentProcess().ProcessName` `w3wp` @ no__t-2 @ no__t-3 (v procesu) nebo `dotnet` (mimo proces).</span><span class="sxs-lookup"><span data-stu-id="f3b3d-151">`Process.GetCurrentProcess().ProcessName` reports `w3wp`/`iisexpress` (in-process) or `dotnet` (out-of-process).</span></span>

<span data-ttu-id="f3b3d-152">Mnoho nativních modulů, jako je ověřování systému Windows, zůstane aktivní.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-152">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="f3b3d-153">Další informace o aktivních modulech služby IIS pomocí modulu ASP.NET Core najdete v tématu <xref:host-and-deploy/iis/modules>.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-153">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="f3b3d-154">Modul ASP.NET Core může také:</span><span class="sxs-lookup"><span data-stu-id="f3b3d-154">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="f3b3d-155">Nastavte proměnné prostředí pro pracovní proces.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-155">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="f3b3d-156">Protokoluje výstup stdout do úložiště souborů pro řešení problémů při spuštění.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-156">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="f3b3d-157">Předejte tokeny ověřování systému Windows.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-157">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="f3b3d-158">Jak nainstalovat a používat modul ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f3b3d-158">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="f3b3d-159">Pokyny k instalaci modulu ASP.NET Core najdete v tématu [instalace hostující sady .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span><span class="sxs-lookup"><span data-stu-id="f3b3d-159">For instructions on how to install the ASP.NET Core Module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="f3b3d-160">Konfigurace pomocí souboru Web. config</span><span class="sxs-lookup"><span data-stu-id="f3b3d-160">Configuration with web.config</span></span>

<span data-ttu-id="f3b3d-161">ASP.NET Core modul je nakonfigurovaný pomocí oddílu `aspNetCore` v uzlu `system.webServer` v souboru *Web. config* tohoto webu.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-161">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="f3b3d-162">Následující soubor *Web. config* je publikován pro [nasazení závislé na rozhraní](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) a nakonfiguruje modul ASP.NET Core pro zpracování požadavků lokality:</span><span class="sxs-lookup"><span data-stu-id="f3b3d-162">The following *web.config* file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

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
                  hostingModel="InProcess" />
    </system.webServer>
  </location>
</configuration>
```

<span data-ttu-id="f3b3d-163">Následující soubor *Web. config* je publikován pro [samostatně uzavřené nasazení](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span><span class="sxs-lookup"><span data-stu-id="f3b3d-163">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

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
                  hostingModel="InProcess" />
    </system.webServer>
  </location>
</configuration>
```

<span data-ttu-id="f3b3d-164">Vlastnost <xref:System.Configuration.SectionInformation.InheritInChildApplications*> je nastavena na hodnotu `false` k označení toho, že nastavení zadaná v rámci [> prvku \<location](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) nejsou děděna aplikacemi, které se nacházejí v podadresáři aplikace.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-164">The <xref:System.Configuration.SectionInformation.InheritInChildApplications*> property is set to `false` to indicate that the settings specified within the [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) element aren't inherited by apps that reside in a subdirectory of the app.</span></span>

<span data-ttu-id="f3b3d-165">Když se aplikace nasadí do [Azure App Service](https://azure.microsoft.com/services/app-service/), cesta `stdoutLogFile` je nastavená na `\\?\%home%\LogFiles\stdout`.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-165">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="f3b3d-166">Cesta ukládá protokoly stdout do složky *soubory protokolů* , což je umístění, které služba automaticky vytvořila.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-166">The path saves stdout logs to the *LogFiles* folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="f3b3d-167">Informace o konfiguraci dílčí aplikace IIS najdete v tématu <xref:host-and-deploy/iis/index#sub-applications>.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-167">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="f3b3d-168">Atributy elementu aspNetCore</span><span class="sxs-lookup"><span data-stu-id="f3b3d-168">Attributes of the aspNetCore element</span></span>

| <span data-ttu-id="f3b3d-169">Atribut</span><span class="sxs-lookup"><span data-stu-id="f3b3d-169">Attribute</span></span> | <span data-ttu-id="f3b3d-170">Popis</span><span class="sxs-lookup"><span data-stu-id="f3b3d-170">Description</span></span> | <span data-ttu-id="f3b3d-171">Výchozí</span><span class="sxs-lookup"><span data-stu-id="f3b3d-171">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="f3b3d-172">Volitelný řetězcový atribut.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-172">Optional string attribute.</span></span></p><p><span data-ttu-id="f3b3d-173">Argumenty ke spustitelnému souboru určenému v **processPath**.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-173">Arguments to the executable specified in **processPath**.</span></span></p> | |
| `disableStartUpErrorPage` | <p><span data-ttu-id="f3b3d-174">Volitelný atribut typu Boolean.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-174">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="f3b3d-175">Pokud je nastaveno na true, stránka **502,5 – selhání procesu** se potlačí a stavová stránka 502 konfigurovaná v *souboru Web. config* má přednost.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-175">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="f3b3d-176">Volitelný atribut typu Boolean.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-176">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="f3b3d-177">Pokud je nastaveno na true, token se přepošle podřízenému procesu, který naslouchá na% ASPNETCORE_PORT% jako hlavička MS-ASPNETCORE-WINAUTHTOKEN na vyžádání.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-177">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="f3b3d-178">Je zodpovědností za tento proces volání CloseHandle na tento token na žádost.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-178">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `hostingModel` | <p><span data-ttu-id="f3b3d-179">Volitelný řetězcový atribut.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-179">Optional string attribute.</span></span></p><p><span data-ttu-id="f3b3d-180">Určuje model hostování jako vnitroprocesové (`InProcess`) nebo mimo proces (`OutOfProcess`).</span><span class="sxs-lookup"><span data-stu-id="f3b3d-180">Specifies the hosting model as in-process (`InProcess`) or out-of-process (`OutOfProcess`).</span></span></p> | `InProcess` |
| `processesPerApplication` | <p><span data-ttu-id="f3b3d-181">Volitelný celočíselný atribut</span><span class="sxs-lookup"><span data-stu-id="f3b3d-181">Optional integer attribute.</span></span></p><p><span data-ttu-id="f3b3d-182">Určuje počet instancí procesu určeného v nastavení **processPath** , které lze v rámci aplikace vystavit.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-182">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="f3b3d-183">@no__t – 0For hostování v procesu, hodnota je omezená na `1`.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-183">&dagger;For in-process hosting, the value is limited to `1`.</span></span></p><p><span data-ttu-id="f3b3d-184">Nastavení `processesPerApplication` se nedoporučuje.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-184">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="f3b3d-185">Tento atribut bude v budoucí verzi odebrán.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-185">This attribute will be removed in a future release.</span></span></p> | <span data-ttu-id="f3b3d-186">Výchozí: `1`</span><span class="sxs-lookup"><span data-stu-id="f3b3d-186">Default: `1`</span></span><br><span data-ttu-id="f3b3d-187">Minimum: `1`</span><span class="sxs-lookup"><span data-stu-id="f3b3d-187">Min: `1`</span></span><br><span data-ttu-id="f3b3d-188">Max: `100` @ no__t-1</span><span class="sxs-lookup"><span data-stu-id="f3b3d-188">Max: `100`&dagger;</span></span> |
| `processPath` | <p><span data-ttu-id="f3b3d-189">Povinný atribut řetězce.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-189">Required string attribute.</span></span></p><p><span data-ttu-id="f3b3d-190">Cesta ke spustitelnému souboru, který spouští proces naslouchající na požadavky HTTP.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-190">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="f3b3d-191">Jsou podporovány relativní cesty.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-191">Relative paths are supported.</span></span> <span data-ttu-id="f3b3d-192">Pokud cesta začíná na `.`, bude cesta považována za relativní vzhledem k kořenu webu.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-192">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="f3b3d-193">Volitelný celočíselný atribut</span><span class="sxs-lookup"><span data-stu-id="f3b3d-193">Optional integer attribute.</span></span></p><p><span data-ttu-id="f3b3d-194">Určuje, kolikrát je povoleno selhání procesu určeného v **processPath** za minutu.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-194">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="f3b3d-195">Pokud je tento limit překročen, modul ukončí spuštění procesu po zbytek minuty.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-195">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p><p><span data-ttu-id="f3b3d-196">Nepodporováno v hostování v rámci procesu.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-196">Not supported with in-process hosting.</span></span></p> | <span data-ttu-id="f3b3d-197">Výchozí: `10`</span><span class="sxs-lookup"><span data-stu-id="f3b3d-197">Default: `10`</span></span><br><span data-ttu-id="f3b3d-198">Minimum: `0`</span><span class="sxs-lookup"><span data-stu-id="f3b3d-198">Min: `0`</span></span><br><span data-ttu-id="f3b3d-199">Max: `100`</span><span class="sxs-lookup"><span data-stu-id="f3b3d-199">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="f3b3d-200">Volitelný atribut TimeSpan.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-200">Optional timespan attribute.</span></span></p><p><span data-ttu-id="f3b3d-201">Určuje dobu, po kterou modul ASP.NET Core čeká na odezvu od procesu, na kterém naslouchá% ASPNETCORE_PORT%.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-201">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="f3b3d-202">Ve verzích modulu ASP.NET Core, které byly dodávány s vydáním ASP.NET Core 2,1 nebo vyšší, je `requestTimeout` zadáno v hodinách, minutách a sekundách.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-202">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p><p><span data-ttu-id="f3b3d-203">Neplatí pro hostování v procesu.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-203">Doesn't apply to in-process hosting.</span></span> <span data-ttu-id="f3b3d-204">Pro hostování v rámci procesu modul čeká, až aplikace zpracuje požadavek.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-204">For in-process hosting, the module waits for the app to process the request.</span></span></p><p><span data-ttu-id="f3b3d-205">Platné hodnoty segmentů minut a sekund řetězce jsou v rozsahu 0-59.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-205">Valid values for minutes and seconds segments of the string are in the range 0-59.</span></span> <span data-ttu-id="f3b3d-206">Při použití hodnoty **60** v hodnotě minut nebo sekund dojde k chybě systému *500 – interní chyba serveru*.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-206">Use of **60** in the value for minutes or seconds results in a *500 - Internal Server Error*.</span></span></p> | <span data-ttu-id="f3b3d-207">Výchozí: `00:02:00`</span><span class="sxs-lookup"><span data-stu-id="f3b3d-207">Default: `00:02:00`</span></span><br><span data-ttu-id="f3b3d-208">Minimum: `00:00:00`</span><span class="sxs-lookup"><span data-stu-id="f3b3d-208">Min: `00:00:00`</span></span><br><span data-ttu-id="f3b3d-209">Max: `360:00:00`</span><span class="sxs-lookup"><span data-stu-id="f3b3d-209">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="f3b3d-210">Volitelný celočíselný atribut</span><span class="sxs-lookup"><span data-stu-id="f3b3d-210">Optional integer attribute.</span></span></p><p><span data-ttu-id="f3b3d-211">Doba v sekundách, po kterou modul čeká na řádné vypnutí spustitelného souboru, když se zjistí soubor *App_offline. htm* .</span><span class="sxs-lookup"><span data-stu-id="f3b3d-211">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="f3b3d-212">Výchozí: `10`</span><span class="sxs-lookup"><span data-stu-id="f3b3d-212">Default: `10`</span></span><br><span data-ttu-id="f3b3d-213">Minimum: `0`</span><span class="sxs-lookup"><span data-stu-id="f3b3d-213">Min: `0`</span></span><br><span data-ttu-id="f3b3d-214">Max: `600`</span><span class="sxs-lookup"><span data-stu-id="f3b3d-214">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="f3b3d-215">Volitelný celočíselný atribut</span><span class="sxs-lookup"><span data-stu-id="f3b3d-215">Optional integer attribute.</span></span></p><p><span data-ttu-id="f3b3d-216">Doba v sekundách, kterou modul počká, než spustitelný soubor spustí proces, který na portu naslouchá.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-216">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="f3b3d-217">Pokud je tento časový limit překročen, modul proces ukončuje.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-217">If this time limit is exceeded, the module kills the process.</span></span> <span data-ttu-id="f3b3d-218">Modul se pokusí znovu spustit proces, když obdrží novou žádost, a pokračuje v pokusu o restartování procesu u dalších příchozích požadavků, pokud se nepodaří spustit **rapidFailsPerMinute** počet pokusů během poslední minuty.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-218">The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="f3b3d-219">Hodnota 0 (nula **) není považována za** nekonečný časový limit.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-219">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="f3b3d-220">Výchozí: `120`</span><span class="sxs-lookup"><span data-stu-id="f3b3d-220">Default: `120`</span></span><br><span data-ttu-id="f3b3d-221">Minimum: `0`</span><span class="sxs-lookup"><span data-stu-id="f3b3d-221">Min: `0`</span></span><br><span data-ttu-id="f3b3d-222">Max: `3600`</span><span class="sxs-lookup"><span data-stu-id="f3b3d-222">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="f3b3d-223">Volitelný atribut typu Boolean.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-223">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="f3b3d-224">Je-li nastavena hodnota true, **stdout** a **stderr** pro proces zadaný v **processPath** budou přesměrovány do souboru zadaného v **stdoutLogFile**.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-224">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="f3b3d-225">Volitelný řetězcový atribut.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-225">Optional string attribute.</span></span></p><p><span data-ttu-id="f3b3d-226">Určuje relativní nebo absolutní cestu k souboru, pro který je **stdout** a **stderr** z procesu určeného v **processPath** protokolováno.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-226">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="f3b3d-227">Relativní cesty jsou relativní vzhledem k kořenu webu.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-227">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="f3b3d-228">Všechny cesty začínající na `.` jsou relativní vzhledem k kořenu webu a všechny ostatní cesty se považují za absolutní cesty.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-228">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="f3b3d-229">Při vytvoření souboru protokolu se v modulu vytvoří všechny složky, které jsou v cestě zadané.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-229">Any folders provided in the path are created by the module when the log file is created.</span></span> <span data-ttu-id="f3b3d-230">Pomocí oddělovačů podtržítka se do posledního segmentu cesty **stdoutLogFile** přidá časové razítko, ID procesu a Přípona souboru ( *. log*).</span><span class="sxs-lookup"><span data-stu-id="f3b3d-230">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="f3b3d-231">Pokud je jako hodnota zadána hodnota `.\logs\stdout`, je ukázkový protokol stdout uložen jako *stdout_20180205194132_1934. log* ve složce *logs* při uložení na 2/5/2018 v 19:41:32 s ID procesu 1934.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-231">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

### <a name="set-environment-variables"></a><span data-ttu-id="f3b3d-232">Nastavení proměnných prostředí</span><span class="sxs-lookup"><span data-stu-id="f3b3d-232">Set environment variables</span></span>

<span data-ttu-id="f3b3d-233">Proměnné prostředí lze zadat pro proces v atributu `processPath`.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-233">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="f3b3d-234">Zadejte proměnnou prostředí s podřízeným prvkem `<environmentVariable>` elementu kolekce `<environmentVariables>`.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-234">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span> <span data-ttu-id="f3b3d-235">Proměnné prostředí nastavené v této části mají přednost před proměnnými prostředí systému.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-235">Environment variables set in this section take precedence over system environment variables.</span></span>

<span data-ttu-id="f3b3d-236">Následující příklad nastaví dvě proměnné prostředí v *souboru Web. config*. `ASPNETCORE_ENVIRONMENT` nakonfiguruje prostředí aplikace na `Development`.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-236">The following example sets two environment variables in *web.config*. `ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="f3b3d-237">Vývojář může tuto hodnotu dočasně nastavit v souboru *Web. config* , aby bylo možné vynutit, aby se [Stránka s výjimkou vývojářů](xref:fundamentals/error-handling) načetla při ladění výjimky aplikace.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-237">A developer may temporarily set this value in the *web.config* file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="f3b3d-238">`CONFIG_DIR` je příkladem uživatelsky definované proměnné prostředí, kde vývojář napsal kód, který přečte hodnotu při spuštění, aby vytvořil cestu pro načtení konfiguračního souboru aplikace.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-238">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile="\\?\%home%\LogFiles\stdout"
      hostingModel="InProcess">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
    <environmentVariable name="CONFIG_DIR" value="f:\application_config" />
  </environmentVariables>
</aspNetCore>
```

> [!NOTE]
> <span data-ttu-id="f3b3d-239">Alternativou k nastavení prostředí přímo v souboru *Web. config* je zahrnutí vlastnosti `<EnvironmentName>` do profilu publikování ( *. pubxml*) nebo souboru projektu.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-239">An alternative to setting the environment directly in *web.config* is to include the `<EnvironmentName>` property in the publish profile (*.pubxml*) or project file.</span></span> <span data-ttu-id="f3b3d-240">Tento přístup nastaví prostředí v *souboru Web. config* při publikování projektu:</span><span class="sxs-lookup"><span data-stu-id="f3b3d-240">This approach sets the environment in *web.config* when the project is published:</span></span>
>
> ```xml
> <PropertyGroup>
>   <EnvironmentName>Development</EnvironmentName>
> </PropertyGroup>
> ```

> [!WARNING]
> <span data-ttu-id="f3b3d-241">Nastavte pouze proměnnou prostředí `ASPNETCORE_ENVIRONMENT` na `Development` na pracovních a testovacích serverech, které nejsou přístupné pro nedůvěryhodné sítě, jako je například Internet.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-241">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## <a name="app_offlinehtm"></a><span data-ttu-id="f3b3d-242">App_offline. htm</span><span class="sxs-lookup"><span data-stu-id="f3b3d-242">app_offline.htm</span></span>

<span data-ttu-id="f3b3d-243">Pokud se v kořenovém adresáři aplikace zjistí soubor s názvem *App_offline. htm* , modul ASP.NET Core se pokusí aplikaci řádně vypnout a zastaví zpracování příchozích požadavků.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-243">If a file with the name *app_offline.htm* is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="f3b3d-244">Pokud aplikace běží i po uplynutí počtu sekund definovaného v `shutdownTimeLimit`, modul ASP.NET Core ukončuje běžící proces.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-244">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="f3b3d-245">Když je přítomen soubor *App_offline. htm* , ASP.NET Core modul reaguje na požadavky odesláním zpět obsahu souboru *App_offline. htm* .</span><span class="sxs-lookup"><span data-stu-id="f3b3d-245">While the *app_offline.htm* file is present, the ASP.NET Core Module responds to requests by sending back the contents of the *app_offline.htm* file.</span></span> <span data-ttu-id="f3b3d-246">Po odebrání souboru *App_offline. htm* aplikace spustí další požadavek.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-246">When the *app_offline.htm* file is removed, the next request starts the app.</span></span>

<span data-ttu-id="f3b3d-247">Při použití nezpracovaného modelu hostování se aplikace nemusí okamžitě vypnout, pokud dojde k otevřenému připojení.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-247">When using the out-of-process hosting model, the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="f3b3d-248">Připojení k protokolu WebSocket může například zpozdit ukončení aplikace.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-248">For example, a websocket connection may delay app shut down.</span></span>

## <a name="start-up-error-page"></a><span data-ttu-id="f3b3d-249">Spouštěcí chybová stránka</span><span class="sxs-lookup"><span data-stu-id="f3b3d-249">Start-up error page</span></span>

<span data-ttu-id="f3b3d-250">Hostování v procesu i mimo procesy vytváří vlastní chybové stránky, když se nepodaří aplikaci spustit.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-250">Both in-process and out-of-process hosting produce custom error pages when they fail to start the app.</span></span>

<span data-ttu-id="f3b3d-251">Pokud modul ASP.NET Core nenalezne obslužnou rutinu žádosti v procesu nebo mimo proces 500,0, zobrazí se stránka s kódem stavu *selhání načtení obslužné rutiny v* procesu nebo mimo proces.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-251">If the ASP.NET Core Module fails to find either the in-process or out-of-process request handler, a *500.0 - In-Process/Out-Of-Process Handler Load Failure* status code page appears.</span></span>

<span data-ttu-id="f3b3d-252">Pro vnitroprocesové hostování v případě, že se modul ASP.NET Core nepodaří spustit aplikaci, zobrazí se stránka s kódem stavu *chyby při spuštění 500,30* .</span><span class="sxs-lookup"><span data-stu-id="f3b3d-252">For in-process hosting if the ASP.NET Core Module fails to start the app, a *500.30 - Start Failure* status code page appears.</span></span>

<span data-ttu-id="f3b3d-253">Pro hostování mimo procesy, pokud se ASP.NET Core modulu nespustí back-end proces, nebo dojde k selhání procesu back-endu, ale nenaslouchá na konfigurovaném portu, zobrazí se stránka s kódem stavu *selhání procesu 502,5* .</span><span class="sxs-lookup"><span data-stu-id="f3b3d-253">For out-of-process hosting if the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span>

<span data-ttu-id="f3b3d-254">Chcete-li tuto stránku potlačit a vrátit se na výchozí stavovou stránku služby IIS 5xx, použijte atribut `disableStartUpErrorPage`.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-254">To suppress this page and revert to the default IIS 5xx status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="f3b3d-255">Další informace o konfiguraci vlastních chybových zpráv najdete v tématu [chyby protokolu HTTP \<httpErrors >](/iis/configuration/system.webServer/httpErrors/).</span><span class="sxs-lookup"><span data-stu-id="f3b3d-255">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

## <a name="log-creation-and-redirection"></a><span data-ttu-id="f3b3d-256">Vytvoření a přesměrování protokolu</span><span class="sxs-lookup"><span data-stu-id="f3b3d-256">Log creation and redirection</span></span>

<span data-ttu-id="f3b3d-257">Modul ASP.NET Core přesměruje výstup konzoly stdout a stderr na disk, pokud jsou nastaveny atributy `stdoutLogEnabled` a `stdoutLogFile` prvku `aspNetCore`.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-257">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="f3b3d-258">Při vytvoření souboru protokolu jsou všechny složky v cestě `stdoutLogFile` vytvářeny modulem.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-258">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="f3b3d-259">Fond aplikací musí mít oprávnění k zápisu do umístění, kam se zapisují protokoly (k poskytnutí oprávnění k zápisu použijte `IIS AppPool\<app_pool_name>`).</span><span class="sxs-lookup"><span data-stu-id="f3b3d-259">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="f3b3d-260">Protokoly nejsou otočeny, pokud nedochází k recyklování/restartování procesu.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-260">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="f3b3d-261">Je zodpovědností hostitele omezit místo na disku, které používají protokoly.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-261">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="f3b3d-262">Použití protokolu stdout se doporučuje jenom při řešení problémů se spouštěním aplikací.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-262">Using the stdout log is only recommended for troubleshooting app startup issues.</span></span> <span data-ttu-id="f3b3d-263">Nepoužívejte protokol stdout pro obecné účely protokolování aplikace.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-263">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="f3b3d-264">Pro rutinu protokolování v aplikaci ASP.NET Core použijte knihovnu protokolování, která omezuje velikost souboru protokolu a otočí protokoly.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-264">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="f3b3d-265">Další informace najdete v tématu [Zprostředkovatelé protokolování třetích stran](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="f3b3d-265">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="f3b3d-266">Časové razítko a Přípona souboru se automaticky přidají při vytvoření souboru protokolu.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-266">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="f3b3d-267">Název souboru protokolu se skládá připojením časového razítka, ID procesu a přípony souboru ( *. log*) k poslednímu segmentu cesty `stdoutLogFile` (obvykle *stdout*), oddělený podtržítky.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-267">The log file name is composed by appending the timestamp, process ID, and file extension (*.log*) to the last segment of the `stdoutLogFile` path (typically *stdout*) delimited by underscores.</span></span> <span data-ttu-id="f3b3d-268">Pokud cesta `stdoutLogFile` končí na *stdout*, protokol pro aplikaci s číslem PID 1934 vytvořeným v 2/5/2018 v 19:42:32 má název souboru *stdout_20180205194132_1934. log*.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-268">If the `stdoutLogFile` path ends with *stdout*, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name *stdout_20180205194132_1934.log*.</span></span>

<span data-ttu-id="f3b3d-269">Pokud je hodnota `stdoutLogEnabled` false, budou chyby, ke kterým dojde při spuštění aplikace, zachyceny a generovány do protokolu událostí až do 30 KB.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-269">If `stdoutLogEnabled` is false, errors that occur on app startup are captured and emitted to the event log up to 30 KB.</span></span> <span data-ttu-id="f3b3d-270">Po spuštění budou všechny další protokoly zahozeny.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-270">After startup, all additional logs are discarded.</span></span>

<span data-ttu-id="f3b3d-271">Následující vzorový @no__t element-0 v souboru *Web. config* nakonfiguruje protokolování stdout pro aplikaci hostovanou v Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-271">The following sample `aspNetCore` element in a *web.config* file configures stdout logging for an app hosted in Azure App Service.</span></span> <span data-ttu-id="f3b3d-272">Místní cesta nebo cesta ke sdílené síťové složce je přijatelná pro místní protokolování.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-272">A local path or network share path is acceptable for local logging.</span></span> <span data-ttu-id="f3b3d-273">Potvrďte, že identita uživatele fondu aplikací má oprávnění k zápisu do zadané cesty.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-273">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="InProcess">
</aspNetCore>
```

## <a name="enhanced-diagnostic-logs"></a><span data-ttu-id="f3b3d-274">Rozšířené diagnostické protokoly</span><span class="sxs-lookup"><span data-stu-id="f3b3d-274">Enhanced diagnostic logs</span></span>

<span data-ttu-id="f3b3d-275">Modul ASP.NET Core lze nakonfigurovat tak, aby poskytoval rozšířené diagnostické protokoly.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-275">The ASP.NET Core Module is configurable to provide enhanced diagnostics logs.</span></span> <span data-ttu-id="f3b3d-276">Přidejte `<handlerSettings>` elementu do prvku `<aspNetCore>` v *souboru Web. config*. Nastavení `debugLevel` na `TRACE` zpřístupňuje vyšší věrnost diagnostických informací:</span><span class="sxs-lookup"><span data-stu-id="f3b3d-276">Add the `<handlerSettings>` element to the `<aspNetCore>` element in *web.config*. Setting the `debugLevel` to `TRACE` exposes a higher fidelity of diagnostic information:</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="InProcess">
  <handlerSettings>
    <handlerSetting name="debugFile" value=".\logs\aspnetcore-debug.log" />
    <handlerSetting name="debugLevel" value="FILE,TRACE" />
  </handlerSettings>
</aspNetCore>
```

<span data-ttu-id="f3b3d-277">Všechny složky v cestě (*protokoly* v předchozím příkladu) se vytvoří modulem, když se vytvoří soubor protokolu.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-277">Any folders in the path (*logs* in the preceding example) are created by the module when the log file is created.</span></span> <span data-ttu-id="f3b3d-278">Fond aplikací musí mít oprávnění k zápisu do umístění, kam se zapisují protokoly (k poskytnutí oprávnění k zápisu použijte `IIS AppPool\<app_pool_name>`).</span><span class="sxs-lookup"><span data-stu-id="f3b3d-278">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="f3b3d-279">Hodnoty úrovně ladění (`debugLevel`) mohou zahrnovat jak úroveň, tak i umístění.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-279">Debug level (`debugLevel`) values can include both the level and the location.</span></span>

<span data-ttu-id="f3b3d-280">Úrovně (v pořadí od nejméně po nejpodrobnější):</span><span class="sxs-lookup"><span data-stu-id="f3b3d-280">Levels (in order from least to most verbose):</span></span>

* <span data-ttu-id="f3b3d-281">Chyba</span><span class="sxs-lookup"><span data-stu-id="f3b3d-281">ERROR</span></span>
* <span data-ttu-id="f3b3d-282">Upozornění</span><span class="sxs-lookup"><span data-stu-id="f3b3d-282">WARNING</span></span>
* <span data-ttu-id="f3b3d-283">PŘÍJEMCE</span><span class="sxs-lookup"><span data-stu-id="f3b3d-283">INFO</span></span>
* <span data-ttu-id="f3b3d-284">TRACE</span><span class="sxs-lookup"><span data-stu-id="f3b3d-284">TRACE</span></span>

<span data-ttu-id="f3b3d-285">Umístění (je povoleno více umístění):</span><span class="sxs-lookup"><span data-stu-id="f3b3d-285">Locations (multiple locations are permitted):</span></span>

* <span data-ttu-id="f3b3d-286">STROMU</span><span class="sxs-lookup"><span data-stu-id="f3b3d-286">CONSOLE</span></span>
* <span data-ttu-id="f3b3d-287">EVENTLOG</span><span class="sxs-lookup"><span data-stu-id="f3b3d-287">EVENTLOG</span></span>
* <span data-ttu-id="f3b3d-288">SOUBORŮ</span><span class="sxs-lookup"><span data-stu-id="f3b3d-288">FILE</span></span>

<span data-ttu-id="f3b3d-289">Nastavení obslužné rutiny lze také poskytnout pomocí proměnných prostředí:</span><span class="sxs-lookup"><span data-stu-id="f3b3d-289">The handler settings can also be provided via environment variables:</span></span>

* <span data-ttu-id="f3b3d-290">`ASPNETCORE_MODULE_DEBUG_FILE` &ndash; cesta k souboru protokolu ladění.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-290">`ASPNETCORE_MODULE_DEBUG_FILE` &ndash; Path to the debug log file.</span></span> <span data-ttu-id="f3b3d-291">(Výchozí: *aspnetcore-Debug. log*)</span><span class="sxs-lookup"><span data-stu-id="f3b3d-291">(Default: *aspnetcore-debug.log*)</span></span>
* <span data-ttu-id="f3b3d-292">nastavení úrovně ladění `ASPNETCORE_MODULE_DEBUG` &ndash;.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-292">`ASPNETCORE_MODULE_DEBUG` &ndash; Debug level setting.</span></span>

> [!WARNING]
> <span data-ttu-id="f3b3d-293">Nenechávejte **protokolování** ladění povolené v nasazení po delší dobu, než je potřeba k řešení problému.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-293">Do **not** leave debug logging enabled in the deployment for longer than required to troubleshoot an issue.</span></span> <span data-ttu-id="f3b3d-294">Velikost protokolu není omezená.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-294">The size of the log isn't limited.</span></span> <span data-ttu-id="f3b3d-295">Když necháte protokol ladění povolený, může dojít k vyčerpání dostupného místa na disku a selhání serveru nebo služby App Service.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-295">Leaving the debug log enabled can exhaust the available disk space and crash the server or app service.</span></span>

<span data-ttu-id="f3b3d-296">Příklad prvku `aspNetCore` v souboru *Web. config* naleznete v tématu [Konfigurace pomocí souboru Web. config](#configuration-with-webconfig) .</span><span class="sxs-lookup"><span data-stu-id="f3b3d-296">See [Configuration with web.config](#configuration-with-webconfig) for an example of the `aspNetCore` element in the *web.config* file.</span></span>

## <a name="modify-the-stack-size"></a><span data-ttu-id="f3b3d-297">Úprava velikosti zásobníku</span><span class="sxs-lookup"><span data-stu-id="f3b3d-297">Modify the stack size</span></span>

<span data-ttu-id="f3b3d-298">*Platí pouze při použití modelu hostování v rámci procesu.*</span><span class="sxs-lookup"><span data-stu-id="f3b3d-298">*Only applies when using the in-process hosting model.*</span></span>

<span data-ttu-id="f3b3d-299">Nakonfigurujte velikost spravovaného zásobníku pomocí nastavení `stackSize` v bajtech v *souboru Web. config*. Výchozí velikost je `1048576` bajty (1 MB).</span><span class="sxs-lookup"><span data-stu-id="f3b3d-299">Configure the managed stack size using the `stackSize` setting in bytes in *web.config*. The default size is `1048576` bytes (1 MB).</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="InProcess">
  <handlerSettings>
    <handlerSetting name="stackSize" value="2097152" />
  </handlerSettings>
</aspNetCore>
```

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="f3b3d-300">Konfigurace proxy serveru používá protokol HTTP a token párování.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-300">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="f3b3d-301">*Platí pouze pro hostování mimo proces.*</span><span class="sxs-lookup"><span data-stu-id="f3b3d-301">*Only applies to out-of-process hosting.*</span></span>

<span data-ttu-id="f3b3d-302">Proxy vytvořený mezi modulem ASP.NET Core a Kestrel používá protokol HTTP.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-302">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="f3b3d-303">Nehrozí riziko odposlouchávání provozu mezi modulem a Kestrel z umístění od serveru.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-303">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="f3b3d-304">Párovací token se používá k zajištění, že žádosti přijaté službou Kestrel byly proxy službou IIS a nepocházejí z jiného zdroje.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-304">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="f3b3d-305">Token párování se vytvoří a nastaví na proměnnou prostředí (`ASPNETCORE_TOKEN`) modulem.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-305">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="f3b3d-306">Párování tokenu je také nastaveno na hlavičku (`MS-ASPNETCORE-TOKEN`) na každém proxy požadavku.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-306">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="f3b3d-307">Middleware služby IIS kontroluje každý požadavek, který obdrží, a potvrdí, že hodnota hlavičky párového tokenu odpovídá hodnotě proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-307">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="f3b3d-308">Pokud se hodnoty tokenu neshodují, požadavek se zaprotokoluje a odmítne.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-308">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="f3b3d-309">Proměnná prostředí párového tokenu a přenos dat mezi modulem a Kestrel nejsou přístupné z umístění mimo server.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-309">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="f3b3d-310">Bez znalosti hodnoty párovacího tokenu nemůže útočník odeslat žádosti, které obcházejí kontrolu v Middlewari IIS.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-310">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="f3b3d-311">ASP.NET Core modul se sdílenou konfigurací služby IIS</span><span class="sxs-lookup"><span data-stu-id="f3b3d-311">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="f3b3d-312">Instalační služba modulu ASP.NET Core se spouští s oprávněními účtu **TrustedInstaller** .</span><span class="sxs-lookup"><span data-stu-id="f3b3d-312">The ASP.NET Core Module installer runs with the privileges of the **TrustedInstaller** account.</span></span> <span data-ttu-id="f3b3d-313">Vzhledem k tomu, že účet místního systému nemá oprávnění k úpravám pro cestu ke sdílené složce, kterou používá sdílená konfigurace služby IIS, vyvolá Instalační program chybu odepření přístupu při pokusu o konfiguraci nastavení modulu v souboru *ApplicationHost. config* v sdílená složka.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-313">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the *applicationHost.config* file on the share.</span></span>

<span data-ttu-id="f3b3d-314">Pokud používáte sdílenou konfiguraci služby IIS na stejném počítači jako instalaci služby IIS, spusťte instalační program sady prostředků ASP.NET Core s parametrem `OPT_NO_SHARED_CONFIG_CHECK` nastaveným na `1`:</span><span class="sxs-lookup"><span data-stu-id="f3b3d-314">When using an IIS Shared Configuration on the same machine as the IIS installation, run the ASP.NET Core Hosting Bundle installer with the `OPT_NO_SHARED_CONFIG_CHECK` parameter set to `1`:</span></span>

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

<span data-ttu-id="f3b3d-315">Pokud cesta ke sdílené konfiguraci není ve stejném počítači jako instalace služby IIS, postupujte podle těchto kroků:</span><span class="sxs-lookup"><span data-stu-id="f3b3d-315">When the path to the shared configuration isn't on the same machine as the IIS installation, follow these steps:</span></span>

1. <span data-ttu-id="f3b3d-316">Zakažte sdílenou konfiguraci služby IIS.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-316">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="f3b3d-317">Spusťte instalační program.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-317">Run the installer.</span></span>
1. <span data-ttu-id="f3b3d-318">Exportujte aktualizovaný soubor *ApplicationHost. config* do sdílené složky.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-318">Export the updated *applicationHost.config* file to the share.</span></span>
1. <span data-ttu-id="f3b3d-319">Znovu povolte sdílenou konfiguraci služby IIS.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-319">Re-enable the IIS Shared Configuration.</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="f3b3d-320">Verze modulu a instalační protokoly hostitelské sady</span><span class="sxs-lookup"><span data-stu-id="f3b3d-320">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="f3b3d-321">Postup určení verze nainstalovaného modulu ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="f3b3d-321">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="f3b3d-322">V hostitelském systému přejděte na *%windir%\system32\inetsrv*.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-322">On the hosting system, navigate to *%windir%\System32\inetsrv*.</span></span>
1. <span data-ttu-id="f3b3d-323">Vyhledejte soubor *aspnetcore. dll* .</span><span class="sxs-lookup"><span data-stu-id="f3b3d-323">Locate the *aspnetcore.dll* file.</span></span>
1. <span data-ttu-id="f3b3d-324">Klikněte na soubor pravým tlačítkem a v místní nabídce vyberte **vlastnosti** .</span><span class="sxs-lookup"><span data-stu-id="f3b3d-324">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="f3b3d-325">Vyberte kartu **Podrobnosti** . **Verze souboru** a **verze produktu** reprezentují nainstalovanou verzi modulu.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-325">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="f3b3d-326">Protokoly instalačních balíčků pro modul se nacházejí v *C: \\Users @ no__t-2% username% \\AppData @ no__t-4Local @ no__t-5Temp*. Soubor má název *dd_DotNetCoreWinSvrHosting__ @ no__t-7timestamp > _000_AspNetCoreModule_x64. log*.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-326">The Hosting Bundle installer logs for the module are found at *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. The file is named *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="f3b3d-327">Umístění souboru modulu, schématu a konfiguračního souboru</span><span class="sxs-lookup"><span data-stu-id="f3b3d-327">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="f3b3d-328">Modul</span><span class="sxs-lookup"><span data-stu-id="f3b3d-328">Module</span></span>

<span data-ttu-id="f3b3d-329">**Služba IIS (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="f3b3d-329">**IIS (x86/amd64):**</span></span>

* <span data-ttu-id="f3b3d-330">%windir%\System32\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="f3b3d-330">%windir%\System32\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="f3b3d-331">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="f3b3d-331">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="f3b3d-332">%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="f3b3d-332">%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="f3b3d-333">% ProgramFiles (x86)% \ IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="f3b3d-333">%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

<span data-ttu-id="f3b3d-334">**IIS Express (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="f3b3d-334">**IIS Express (x86/amd64):**</span></span>

* <span data-ttu-id="f3b3d-335">%ProgramFiles%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="f3b3d-335">%ProgramFiles%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="f3b3d-336">% ProgramFiles (x86)% \ IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="f3b3d-336">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="f3b3d-337">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="f3b3d-337">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="f3b3d-338">% ProgramFiles (x86)% \ IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="f3b3d-338">%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

### <a name="schema"></a><span data-ttu-id="f3b3d-339">Schéma</span><span class="sxs-lookup"><span data-stu-id="f3b3d-339">Schema</span></span>

<span data-ttu-id="f3b3d-340">**SLUŽBU**</span><span class="sxs-lookup"><span data-stu-id="f3b3d-340">**IIS**</span></span>

* <span data-ttu-id="f3b3d-341">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="f3b3d-341">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span></span>

* <span data-ttu-id="f3b3d-342">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span><span class="sxs-lookup"><span data-stu-id="f3b3d-342">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span></span>

<span data-ttu-id="f3b3d-343">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="f3b3d-343">**IIS Express**</span></span>

* <span data-ttu-id="f3b3d-344">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="f3b3d-344">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span></span>

* <span data-ttu-id="f3b3d-345">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span><span class="sxs-lookup"><span data-stu-id="f3b3d-345">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span></span>

### <a name="configuration"></a><span data-ttu-id="f3b3d-346">Konfigurace</span><span class="sxs-lookup"><span data-stu-id="f3b3d-346">Configuration</span></span>

<span data-ttu-id="f3b3d-347">**SLUŽBU**</span><span class="sxs-lookup"><span data-stu-id="f3b3d-347">**IIS**</span></span>

* <span data-ttu-id="f3b3d-348">%windir%\System32\inetsrv\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="f3b3d-348">%windir%\System32\inetsrv\config\applicationHost.config</span></span>

<span data-ttu-id="f3b3d-349">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="f3b3d-349">**IIS Express**</span></span>

* <span data-ttu-id="f3b3d-350">Visual Studio: {ROOT aplikace} \\. vs\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="f3b3d-350">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span></span>

* <span data-ttu-id="f3b3d-351">*IISExpress. exe* CLI:%USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span><span class="sxs-lookup"><span data-stu-id="f3b3d-351">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span></span>

<span data-ttu-id="f3b3d-352">Soubory lze najít hledáním *aspnetcore* v souboru *ApplicationHost. config* .</span><span class="sxs-lookup"><span data-stu-id="f3b3d-352">The files can be found by searching for *aspnetcore* in the *applicationHost.config* file.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="f3b3d-353">Modul ASP.NET Core je nativním modulem služby IIS, který se připojuje k kanálu IIS na jednu z těchto:</span><span class="sxs-lookup"><span data-stu-id="f3b3d-353">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to either:</span></span>

* <span data-ttu-id="f3b3d-354">Hostování aplikace ASP.NET Core uvnitř pracovního procesu služby IIS (`w3wp.exe`) se nazývá [model hostování v rámci procesu](#in-process-hosting-model).</span><span class="sxs-lookup"><span data-stu-id="f3b3d-354">Host an ASP.NET Core app inside of the IIS worker process (`w3wp.exe`), called the [in-process hosting model](#in-process-hosting-model).</span></span>
* <span data-ttu-id="f3b3d-355">Dopředné webové požadavky do back-endu ASP.NET Core aplikace, na které běží [Server Kestrel](xref:fundamentals/servers/kestrel), se nazývá [model hostování mimo proces](#out-of-process-hosting-model).</span><span class="sxs-lookup"><span data-stu-id="f3b3d-355">Forward web requests to a backend ASP.NET Core app running the [Kestrel server](xref:fundamentals/servers/kestrel), called the [out-of-process hosting model](#out-of-process-hosting-model).</span></span>

<span data-ttu-id="f3b3d-356">Podporované verze systému Windows:</span><span class="sxs-lookup"><span data-stu-id="f3b3d-356">Supported Windows versions:</span></span>

* <span data-ttu-id="f3b3d-357">Windows 7 nebo novější</span><span class="sxs-lookup"><span data-stu-id="f3b3d-357">Windows 7 or later</span></span>
* <span data-ttu-id="f3b3d-358">Windows Server 2008 R2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="f3b3d-358">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="f3b3d-359">Při hostování v procesu používá modul implementaci vnitroprocesového serveru pro službu IIS, která se nazývá IIS HTTP Server (`IISHttpServer`).</span><span class="sxs-lookup"><span data-stu-id="f3b3d-359">When hosting in-process, the module uses an in-process server implementation for IIS, called IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="f3b3d-360">Při hostování mimo proces funguje modul pouze s Kestrel.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-360">When hosting out-of-process, the module only works with Kestrel.</span></span> <span data-ttu-id="f3b3d-361">Modul nefunguje s [ovladačem HTTP. sys](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="f3b3d-361">The module doesn't function with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

## <a name="hosting-models"></a><span data-ttu-id="f3b3d-362">Modely hostingu</span><span class="sxs-lookup"><span data-stu-id="f3b3d-362">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="f3b3d-363">Model hostování v procesu</span><span class="sxs-lookup"><span data-stu-id="f3b3d-363">In-process hosting model</span></span>

<span data-ttu-id="f3b3d-364">Chcete-li nakonfigurovat aplikaci pro hostování v rámci procesu, přidejte do souboru projektu aplikace vlastnost `<AspNetCoreHostingModel>` s hodnotou `InProcess` (hostování mimo procesy je nastavena s `OutOfProcess`):</span><span class="sxs-lookup"><span data-stu-id="f3b3d-364">To configure an app for in-process hosting, add the `<AspNetCoreHostingModel>` property to the app's project file with a value of `InProcess` (out-of-process hosting is set with `OutOfProcess`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>InProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="f3b3d-365">Model hostování v rámci procesu není podporován pro aplikace ASP.NET Core, které cílí na .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-365">The in-process hosting model isn't supported for ASP.NET Core apps that target the .NET Framework.</span></span>

<span data-ttu-id="f3b3d-366">Pokud se v souboru nenachází vlastnost `<AspNetCoreHostingModel>`, výchozí hodnota je `OutOfProcess`.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-366">If the `<AspNetCoreHostingModel>` property isn't present in the file, the default value is `OutOfProcess`.</span></span>

<span data-ttu-id="f3b3d-367">Následující vlastnosti platí při hostování v procesu:</span><span class="sxs-lookup"><span data-stu-id="f3b3d-367">The following characteristics apply when hosting in-process:</span></span>

* <span data-ttu-id="f3b3d-368">Místo [Kestrel](xref:fundamentals/servers/kestrel) serveru se používá server HTTP služby IIS (`IISHttpServer`).</span><span class="sxs-lookup"><span data-stu-id="f3b3d-368">IIS HTTP Server (`IISHttpServer`) is used instead of [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span> <span data-ttu-id="f3b3d-369">Pro vnitroprocesové [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) volání <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> do:</span><span class="sxs-lookup"><span data-stu-id="f3b3d-369">For in-process, [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> to:</span></span>

  * <span data-ttu-id="f3b3d-370">Zaregistrujte `IISHttpServer`.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-370">Register the `IISHttpServer`.</span></span>
  * <span data-ttu-id="f3b3d-371">Nakonfigurujte port a základní cestu, na kterých má Server naslouchat při spuštění za modulem ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-371">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
  * <span data-ttu-id="f3b3d-372">Nakonfigurujte hostitele tak, aby zachytával chyby při spuštění.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-372">Configure the host to capture startup errors.</span></span>

* <span data-ttu-id="f3b3d-373">[Atribut RequestTimeout](#attributes-of-the-aspnetcore-element) se nevztahuje na hostování v rámci procesu.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-373">The [requestTimeout attribute](#attributes-of-the-aspnetcore-element) doesn't apply to in-process hosting.</span></span>

* <span data-ttu-id="f3b3d-374">Sdílení fondu aplikací mezi aplikacemi není podporováno.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-374">Sharing an app pool among apps isn't supported.</span></span> <span data-ttu-id="f3b3d-375">Použijte jeden fond aplikací na aplikaci.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-375">Use one app pool per app.</span></span>

* <span data-ttu-id="f3b3d-376">Při použití [nasazení webu](/iis/publish/using-web-deploy/introduction-to-web-deploy) nebo ručnímu umístění [souboru App_offline. htm v nasazení](xref:host-and-deploy/iis/index#locked-deployment-files)se aplikace nemusí okamžitě vypnout, pokud dojde k otevřenému připojení.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-376">When using [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) or manually placing an [app_offline.htm file in the deployment](xref:host-and-deploy/iis/index#locked-deployment-files), the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="f3b3d-377">Připojení k protokolu WebSocket může například zpozdit ukončení aplikace.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-377">For example, a websocket connection may delay app shut down.</span></span>

* <span data-ttu-id="f3b3d-378">Architektura (bitová verze) aplikace a nainstalovaného modulu runtime (x64 nebo x86) se musí shodovat s architekturou fondu aplikací.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-378">The architecture (bitness) of the app and installed runtime (x64 or x86) must match the architecture of the app pool.</span></span>

* <span data-ttu-id="f3b3d-379">Zjistila se odpojení klienta.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-379">Client disconnects are detected.</span></span> <span data-ttu-id="f3b3d-380">Token zrušení [HttpContext. RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) je zrušený, když se klient odpojí.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-380">The [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) cancellation token is cancelled when the client disconnects.</span></span>

* <span data-ttu-id="f3b3d-381">V ASP.NET Core 2.2.1 nebo starším <xref:System.IO.Directory.GetCurrentDirectory*> vrátí pracovní adresář procesu spuštěného službou IIS místo adresáře aplikace (například *C:\Windows\System32\inetsrv* pro *W3wp. exe*).</span><span class="sxs-lookup"><span data-stu-id="f3b3d-381">In ASP.NET Core 2.2.1 or earlier, <xref:System.IO.Directory.GetCurrentDirectory*> returns the worker directory of the process started by IIS rather than the app's directory (for example, *C:\Windows\System32\inetsrv* for *w3wp.exe*).</span></span>

  <span data-ttu-id="f3b3d-382">Vzorový kód, který nastaví aktuální adresář aplikace, najdete v tématu [Třída CurrentDirectoryHelpers](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/2.x/CurrentDirectoryHelpers.cs).</span><span class="sxs-lookup"><span data-stu-id="f3b3d-382">For sample code that sets the app's current directory, see the [CurrentDirectoryHelpers class](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/2.x/CurrentDirectoryHelpers.cs).</span></span> <span data-ttu-id="f3b3d-383">Zavolejte metodu `SetCurrentDirectory`.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-383">Call the `SetCurrentDirectory` method.</span></span> <span data-ttu-id="f3b3d-384">Následná volání <xref:System.IO.Directory.GetCurrentDirectory*> poskytují adresář aplikace.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-384">Subsequent calls to <xref:System.IO.Directory.GetCurrentDirectory*> provide the app's directory.</span></span>

* <span data-ttu-id="f3b3d-385">Při hostování v procesu se nevolá <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> interně pro inicializaci uživatele.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-385">When hosting in-process, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span> <span data-ttu-id="f3b3d-386">Proto implementace <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> používaná k transformaci deklarací po každém ověření není ve výchozím nastavení aktivována.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-386">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="f3b3d-387">Při transformaci deklarací s implementací <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> zavolejte <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> pro přidání ověřovacích služeb:</span><span class="sxs-lookup"><span data-stu-id="f3b3d-387">When transforming claims with an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation, call <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> to add authentication services:</span></span>

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

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="f3b3d-388">Model hostování mimo proces</span><span class="sxs-lookup"><span data-stu-id="f3b3d-388">Out-of-process hosting model</span></span>

<span data-ttu-id="f3b3d-389">Chcete-li nakonfigurovat aplikaci pro hostování mimo procesy, použijte některý z následujících přístupů v souboru projektu:</span><span class="sxs-lookup"><span data-stu-id="f3b3d-389">To configure an app for out-of-process hosting, use either of the following approaches in the project file:</span></span>

* <span data-ttu-id="f3b3d-390">Nezadávejte vlastnost `<AspNetCoreHostingModel>`.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-390">Don't specify the `<AspNetCoreHostingModel>` property.</span></span> <span data-ttu-id="f3b3d-391">Pokud se v souboru nenachází vlastnost `<AspNetCoreHostingModel>`, výchozí hodnota je `OutOfProcess`.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-391">If the `<AspNetCoreHostingModel>` property isn't present in the file, the default value is `OutOfProcess`.</span></span>
* <span data-ttu-id="f3b3d-392">Nastavte hodnotu vlastnosti `<AspNetCoreHostingModel>` na `OutOfProcess` (hostování v procesu je nastaveno pomocí `InProcess`):</span><span class="sxs-lookup"><span data-stu-id="f3b3d-392">Set the value of the `<AspNetCoreHostingModel>` property to `OutOfProcess` (in-process hosting is set with `InProcess`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="f3b3d-393">Místo HTTP serveru IIS (`IISHttpServer`) se používá server [Kestrel](xref:fundamentals/servers/kestrel) .</span><span class="sxs-lookup"><span data-stu-id="f3b3d-393">[Kestrel](xref:fundamentals/servers/kestrel) server is used instead of IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="f3b3d-394">Pro mimoprocesové aplikace [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) volá <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> do:</span><span class="sxs-lookup"><span data-stu-id="f3b3d-394">For out-of-process, [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> to:</span></span>

* <span data-ttu-id="f3b3d-395">Nakonfigurujte port a základní cestu, na kterých má Server naslouchat při spuštění za modulem ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-395">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
* <span data-ttu-id="f3b3d-396">Nakonfigurujte hostitele tak, aby zachytával chyby při spuštění.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-396">Configure the host to capture startup errors.</span></span>

### <a name="hosting-model-changes"></a><span data-ttu-id="f3b3d-397">Změny modelu hostování</span><span class="sxs-lookup"><span data-stu-id="f3b3d-397">Hosting model changes</span></span>

<span data-ttu-id="f3b3d-398">Pokud je nastavení `hostingModel` změněno v souboru *Web. config* (vysvětleno v části [Konfigurace pomocí Web. config](#configuration-with-webconfig) ), modul recykluje pracovní proces pro službu IIS.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-398">If the `hostingModel` setting is changed in the *web.config* file (explained in the [Configuration with web.config](#configuration-with-webconfig) section), the module recycles the worker process for IIS.</span></span>

<span data-ttu-id="f3b3d-399">V případě IIS Express modul neprovádí recyklaci pracovního procesu, ale místo toho aktivuje bezproblémové vypnutí aktuálního procesu IIS Express.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-399">For IIS Express, the module doesn't recycle the worker process but instead triggers a graceful shutdown of the current IIS Express process.</span></span> <span data-ttu-id="f3b3d-400">Další požadavek na aplikaci vytvoří nový proces IIS Express.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-400">The next request to the app spawns a new IIS Express process.</span></span>

### <a name="process-name"></a><span data-ttu-id="f3b3d-401">Název procesu</span><span class="sxs-lookup"><span data-stu-id="f3b3d-401">Process name</span></span>

<span data-ttu-id="f3b3d-402">sestavy `Process.GetCurrentProcess().ProcessName` `w3wp` @ no__t-2 @ no__t-3 (v procesu) nebo `dotnet` (mimo proces).</span><span class="sxs-lookup"><span data-stu-id="f3b3d-402">`Process.GetCurrentProcess().ProcessName` reports `w3wp`/`iisexpress` (in-process) or `dotnet` (out-of-process).</span></span>

<span data-ttu-id="f3b3d-403">Mnoho nativních modulů, jako je ověřování systému Windows, zůstane aktivní.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-403">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="f3b3d-404">Další informace o aktivních modulech služby IIS pomocí modulu ASP.NET Core najdete v tématu <xref:host-and-deploy/iis/modules>.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-404">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="f3b3d-405">Modul ASP.NET Core může také:</span><span class="sxs-lookup"><span data-stu-id="f3b3d-405">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="f3b3d-406">Nastavte proměnné prostředí pro pracovní proces.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-406">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="f3b3d-407">Protokoluje výstup stdout do úložiště souborů pro řešení problémů při spuštění.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-407">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="f3b3d-408">Předejte tokeny ověřování systému Windows.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-408">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="f3b3d-409">Jak nainstalovat a používat modul ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f3b3d-409">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="f3b3d-410">Pokyny k instalaci modulu ASP.NET Core najdete v tématu [instalace hostující sady .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span><span class="sxs-lookup"><span data-stu-id="f3b3d-410">For instructions on how to install the ASP.NET Core Module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="f3b3d-411">Konfigurace pomocí souboru Web. config</span><span class="sxs-lookup"><span data-stu-id="f3b3d-411">Configuration with web.config</span></span>

<span data-ttu-id="f3b3d-412">ASP.NET Core modul je nakonfigurovaný pomocí oddílu `aspNetCore` v uzlu `system.webServer` v souboru *Web. config* tohoto webu.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-412">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="f3b3d-413">Následující soubor *Web. config* je publikován pro [nasazení závislé na rozhraní](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) a nakonfiguruje modul ASP.NET Core pro zpracování požadavků lokality:</span><span class="sxs-lookup"><span data-stu-id="f3b3d-413">The following *web.config* file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

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
                  hostingModel="InProcess" />
    </system.webServer>
  </location>
</configuration>
```

<span data-ttu-id="f3b3d-414">Následující soubor *Web. config* je publikován pro [samostatně uzavřené nasazení](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span><span class="sxs-lookup"><span data-stu-id="f3b3d-414">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

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
                  hostingModel="InProcess" />
    </system.webServer>
  </location>
</configuration>
```

<span data-ttu-id="f3b3d-415">Vlastnost <xref:System.Configuration.SectionInformation.InheritInChildApplications*> je nastavena na hodnotu `false` k označení toho, že nastavení zadaná v rámci [> prvku \<location](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) nejsou děděna aplikacemi, které se nacházejí v podadresáři aplikace.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-415">The <xref:System.Configuration.SectionInformation.InheritInChildApplications*> property is set to `false` to indicate that the settings specified within the [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) element aren't inherited by apps that reside in a subdirectory of the app.</span></span>

<span data-ttu-id="f3b3d-416">Když se aplikace nasadí do [Azure App Service](https://azure.microsoft.com/services/app-service/), cesta `stdoutLogFile` je nastavená na `\\?\%home%\LogFiles\stdout`.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-416">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="f3b3d-417">Cesta ukládá protokoly stdout do složky *soubory protokolů* , což je umístění, které služba automaticky vytvořila.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-417">The path saves stdout logs to the *LogFiles* folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="f3b3d-418">Informace o konfiguraci dílčí aplikace IIS najdete v tématu <xref:host-and-deploy/iis/index#sub-applications>.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-418">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="f3b3d-419">Atributy elementu aspNetCore</span><span class="sxs-lookup"><span data-stu-id="f3b3d-419">Attributes of the aspNetCore element</span></span>

| <span data-ttu-id="f3b3d-420">Atribut</span><span class="sxs-lookup"><span data-stu-id="f3b3d-420">Attribute</span></span> | <span data-ttu-id="f3b3d-421">Popis</span><span class="sxs-lookup"><span data-stu-id="f3b3d-421">Description</span></span> | <span data-ttu-id="f3b3d-422">Výchozí</span><span class="sxs-lookup"><span data-stu-id="f3b3d-422">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="f3b3d-423">Volitelný řetězcový atribut.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-423">Optional string attribute.</span></span></p><p><span data-ttu-id="f3b3d-424">Argumenty ke spustitelnému souboru určenému v **processPath**.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-424">Arguments to the executable specified in **processPath**.</span></span></p> | |
| `disableStartUpErrorPage` | <p><span data-ttu-id="f3b3d-425">Volitelný atribut typu Boolean.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-425">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="f3b3d-426">Pokud je nastaveno na true, stránka **502,5 – selhání procesu** se potlačí a stavová stránka 502 konfigurovaná v *souboru Web. config* má přednost.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-426">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="f3b3d-427">Volitelný atribut typu Boolean.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-427">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="f3b3d-428">Pokud je nastaveno na true, token se přepošle podřízenému procesu, který naslouchá na% ASPNETCORE_PORT% jako hlavička MS-ASPNETCORE-WINAUTHTOKEN na vyžádání.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-428">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="f3b3d-429">Je zodpovědností za tento proces volání CloseHandle na tento token na žádost.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-429">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `hostingModel` | <p><span data-ttu-id="f3b3d-430">Volitelný řetězcový atribut.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-430">Optional string attribute.</span></span></p><p><span data-ttu-id="f3b3d-431">Určuje model hostování jako vnitroprocesové (`InProcess`) nebo mimo proces (`OutOfProcess`).</span><span class="sxs-lookup"><span data-stu-id="f3b3d-431">Specifies the hosting model as in-process (`InProcess`) or out-of-process (`OutOfProcess`).</span></span></p> | `OutOfProcess` |
| `processesPerApplication` | <p><span data-ttu-id="f3b3d-432">Volitelný celočíselný atribut</span><span class="sxs-lookup"><span data-stu-id="f3b3d-432">Optional integer attribute.</span></span></p><p><span data-ttu-id="f3b3d-433">Určuje počet instancí procesu určeného v nastavení **processPath** , které lze v rámci aplikace vystavit.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-433">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="f3b3d-434">@no__t – 0For hostování v procesu, hodnota je omezená na `1`.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-434">&dagger;For in-process hosting, the value is limited to `1`.</span></span></p><p><span data-ttu-id="f3b3d-435">Nastavení `processesPerApplication` se nedoporučuje.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-435">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="f3b3d-436">Tento atribut bude v budoucí verzi odebrán.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-436">This attribute will be removed in a future release.</span></span></p> | <span data-ttu-id="f3b3d-437">Výchozí: `1`</span><span class="sxs-lookup"><span data-stu-id="f3b3d-437">Default: `1`</span></span><br><span data-ttu-id="f3b3d-438">Minimum: `1`</span><span class="sxs-lookup"><span data-stu-id="f3b3d-438">Min: `1`</span></span><br><span data-ttu-id="f3b3d-439">Max: `100` @ no__t-1</span><span class="sxs-lookup"><span data-stu-id="f3b3d-439">Max: `100`&dagger;</span></span> |
| `processPath` | <p><span data-ttu-id="f3b3d-440">Povinný atribut řetězce.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-440">Required string attribute.</span></span></p><p><span data-ttu-id="f3b3d-441">Cesta ke spustitelnému souboru, který spouští proces naslouchající na požadavky HTTP.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-441">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="f3b3d-442">Jsou podporovány relativní cesty.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-442">Relative paths are supported.</span></span> <span data-ttu-id="f3b3d-443">Pokud cesta začíná na `.`, bude cesta považována za relativní vzhledem k kořenu webu.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-443">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="f3b3d-444">Volitelný celočíselný atribut</span><span class="sxs-lookup"><span data-stu-id="f3b3d-444">Optional integer attribute.</span></span></p><p><span data-ttu-id="f3b3d-445">Určuje, kolikrát je povoleno selhání procesu určeného v **processPath** za minutu.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-445">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="f3b3d-446">Pokud je tento limit překročen, modul ukončí spuštění procesu po zbytek minuty.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-446">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p><p><span data-ttu-id="f3b3d-447">Nepodporováno v hostování v rámci procesu.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-447">Not supported with in-process hosting.</span></span></p> | <span data-ttu-id="f3b3d-448">Výchozí: `10`</span><span class="sxs-lookup"><span data-stu-id="f3b3d-448">Default: `10`</span></span><br><span data-ttu-id="f3b3d-449">Minimum: `0`</span><span class="sxs-lookup"><span data-stu-id="f3b3d-449">Min: `0`</span></span><br><span data-ttu-id="f3b3d-450">Max: `100`</span><span class="sxs-lookup"><span data-stu-id="f3b3d-450">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="f3b3d-451">Volitelný atribut TimeSpan.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-451">Optional timespan attribute.</span></span></p><p><span data-ttu-id="f3b3d-452">Určuje dobu, po kterou modul ASP.NET Core čeká na odezvu od procesu, na kterém naslouchá% ASPNETCORE_PORT%.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-452">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="f3b3d-453">Ve verzích modulu ASP.NET Core, které byly dodávány s vydáním ASP.NET Core 2,1 nebo vyšší, je `requestTimeout` zadáno v hodinách, minutách a sekundách.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-453">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p><p><span data-ttu-id="f3b3d-454">Neplatí pro hostování v procesu.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-454">Doesn't apply to in-process hosting.</span></span> <span data-ttu-id="f3b3d-455">Pro hostování v rámci procesu modul čeká, až aplikace zpracuje požadavek.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-455">For in-process hosting, the module waits for the app to process the request.</span></span></p><p><span data-ttu-id="f3b3d-456">Platné hodnoty segmentů minut a sekund řetězce jsou v rozsahu 0-59.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-456">Valid values for minutes and seconds segments of the string are in the range 0-59.</span></span> <span data-ttu-id="f3b3d-457">Při použití hodnoty **60** v hodnotě minut nebo sekund dojde k chybě systému *500 – interní chyba serveru*.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-457">Use of **60** in the value for minutes or seconds results in a *500 - Internal Server Error*.</span></span></p> | <span data-ttu-id="f3b3d-458">Výchozí: `00:02:00`</span><span class="sxs-lookup"><span data-stu-id="f3b3d-458">Default: `00:02:00`</span></span><br><span data-ttu-id="f3b3d-459">Minimum: `00:00:00`</span><span class="sxs-lookup"><span data-stu-id="f3b3d-459">Min: `00:00:00`</span></span><br><span data-ttu-id="f3b3d-460">Max: `360:00:00`</span><span class="sxs-lookup"><span data-stu-id="f3b3d-460">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="f3b3d-461">Volitelný celočíselný atribut</span><span class="sxs-lookup"><span data-stu-id="f3b3d-461">Optional integer attribute.</span></span></p><p><span data-ttu-id="f3b3d-462">Doba v sekundách, po kterou modul čeká na řádné vypnutí spustitelného souboru, když se zjistí soubor *App_offline. htm* .</span><span class="sxs-lookup"><span data-stu-id="f3b3d-462">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="f3b3d-463">Výchozí: `10`</span><span class="sxs-lookup"><span data-stu-id="f3b3d-463">Default: `10`</span></span><br><span data-ttu-id="f3b3d-464">Minimum: `0`</span><span class="sxs-lookup"><span data-stu-id="f3b3d-464">Min: `0`</span></span><br><span data-ttu-id="f3b3d-465">Max: `600`</span><span class="sxs-lookup"><span data-stu-id="f3b3d-465">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="f3b3d-466">Volitelný celočíselný atribut</span><span class="sxs-lookup"><span data-stu-id="f3b3d-466">Optional integer attribute.</span></span></p><p><span data-ttu-id="f3b3d-467">Doba v sekundách, kterou modul počká, než spustitelný soubor spustí proces, který na portu naslouchá.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-467">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="f3b3d-468">Pokud je tento časový limit překročen, modul proces ukončuje.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-468">If this time limit is exceeded, the module kills the process.</span></span> <span data-ttu-id="f3b3d-469">Modul se pokusí znovu spustit proces, když obdrží novou žádost, a pokračuje v pokusu o restartování procesu u dalších příchozích požadavků, pokud se nepodaří spustit **rapidFailsPerMinute** počet pokusů během poslední minuty.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-469">The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="f3b3d-470">Hodnota 0 (nula **) není považována za** nekonečný časový limit.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-470">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="f3b3d-471">Výchozí: `120`</span><span class="sxs-lookup"><span data-stu-id="f3b3d-471">Default: `120`</span></span><br><span data-ttu-id="f3b3d-472">Minimum: `0`</span><span class="sxs-lookup"><span data-stu-id="f3b3d-472">Min: `0`</span></span><br><span data-ttu-id="f3b3d-473">Max: `3600`</span><span class="sxs-lookup"><span data-stu-id="f3b3d-473">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="f3b3d-474">Volitelný atribut typu Boolean.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-474">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="f3b3d-475">Je-li nastavena hodnota true, **stdout** a **stderr** pro proces zadaný v **processPath** budou přesměrovány do souboru zadaného v **stdoutLogFile**.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-475">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="f3b3d-476">Volitelný řetězcový atribut.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-476">Optional string attribute.</span></span></p><p><span data-ttu-id="f3b3d-477">Určuje relativní nebo absolutní cestu k souboru, pro který je **stdout** a **stderr** z procesu určeného v **processPath** protokolováno.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-477">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="f3b3d-478">Relativní cesty jsou relativní vzhledem k kořenu webu.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-478">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="f3b3d-479">Všechny cesty začínající na `.` jsou relativní vzhledem k kořenu webu a všechny ostatní cesty se považují za absolutní cesty.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-479">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="f3b3d-480">Při vytvoření souboru protokolu se v modulu vytvoří všechny složky, které jsou v cestě zadané.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-480">Any folders provided in the path are created by the module when the log file is created.</span></span> <span data-ttu-id="f3b3d-481">Pomocí oddělovačů podtržítka se do posledního segmentu cesty **stdoutLogFile** přidá časové razítko, ID procesu a Přípona souboru ( *. log*).</span><span class="sxs-lookup"><span data-stu-id="f3b3d-481">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="f3b3d-482">Pokud je jako hodnota zadána hodnota `.\logs\stdout`, je ukázkový protokol stdout uložen jako *stdout_20180205194132_1934. log* ve složce *logs* při uložení na 2/5/2018 v 19:41:32 s ID procesu 1934.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-482">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

### <a name="setting-environment-variables"></a><span data-ttu-id="f3b3d-483">Nastavení proměnných prostředí</span><span class="sxs-lookup"><span data-stu-id="f3b3d-483">Setting environment variables</span></span>

<span data-ttu-id="f3b3d-484">Proměnné prostředí lze zadat pro proces v atributu `processPath`.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-484">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="f3b3d-485">Zadejte proměnnou prostředí s podřízeným prvkem `<environmentVariable>` elementu kolekce `<environmentVariables>`.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-485">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span> <span data-ttu-id="f3b3d-486">Proměnné prostředí nastavené v této části mají přednost před proměnnými prostředí systému.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-486">Environment variables set in this section take precedence over system environment variables.</span></span>

<span data-ttu-id="f3b3d-487">Následující příklad nastaví dvě proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-487">The following example sets two environment variables.</span></span> <span data-ttu-id="f3b3d-488">`ASPNETCORE_ENVIRONMENT` nakonfiguruje prostředí aplikace na `Development`.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-488">`ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="f3b3d-489">Vývojář může tuto hodnotu dočasně nastavit v souboru *Web. config* , aby bylo možné vynutit, aby se [Stránka s výjimkou vývojářů](xref:fundamentals/error-handling) načetla při ladění výjimky aplikace.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-489">A developer may temporarily set this value in the *web.config* file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="f3b3d-490">`CONFIG_DIR` je příkladem uživatelsky definované proměnné prostředí, kde vývojář napsal kód, který přečte hodnotu při spuštění, aby vytvořil cestu pro načtení konfiguračního souboru aplikace.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-490">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile="\\?\%home%\LogFiles\stdout"
      hostingModel="InProcess">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
    <environmentVariable name="CONFIG_DIR" value="f:\application_config" />
  </environmentVariables>
</aspNetCore>
```

> [!NOTE]
> <span data-ttu-id="f3b3d-491">Alternativou k nastavení prostředí přímo v souboru *Web. config* je zahrnutí vlastnosti `<EnvironmentName>` do profilu publikování ( *. pubxml*) nebo souboru projektu.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-491">An alternative to setting the environment directly in *web.config* is to include the `<EnvironmentName>` property in the publish profile (*.pubxml*) or project file.</span></span> <span data-ttu-id="f3b3d-492">Tento přístup nastaví prostředí v *souboru Web. config* při publikování projektu:</span><span class="sxs-lookup"><span data-stu-id="f3b3d-492">This approach sets the environment in *web.config* when the project is published:</span></span>
>
> ```xml
> <PropertyGroup>
>   <EnvironmentName>Development</EnvironmentName>
> </PropertyGroup>
> ```

> [!WARNING]
> <span data-ttu-id="f3b3d-493">Nastavte pouze proměnnou prostředí `ASPNETCORE_ENVIRONMENT` na `Development` na pracovních a testovacích serverech, které nejsou přístupné pro nedůvěryhodné sítě, jako je například Internet.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-493">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## <a name="app_offlinehtm"></a><span data-ttu-id="f3b3d-494">App_offline. htm</span><span class="sxs-lookup"><span data-stu-id="f3b3d-494">app_offline.htm</span></span>

<span data-ttu-id="f3b3d-495">Pokud se v kořenovém adresáři aplikace zjistí soubor s názvem *App_offline. htm* , modul ASP.NET Core se pokusí aplikaci řádně vypnout a zastaví zpracování příchozích požadavků.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-495">If a file with the name *app_offline.htm* is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="f3b3d-496">Pokud aplikace běží i po uplynutí počtu sekund definovaného v `shutdownTimeLimit`, modul ASP.NET Core ukončuje běžící proces.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-496">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="f3b3d-497">Když je přítomen soubor *App_offline. htm* , ASP.NET Core modul reaguje na požadavky odesláním zpět obsahu souboru *App_offline. htm* .</span><span class="sxs-lookup"><span data-stu-id="f3b3d-497">While the *app_offline.htm* file is present, the ASP.NET Core Module responds to requests by sending back the contents of the *app_offline.htm* file.</span></span> <span data-ttu-id="f3b3d-498">Po odebrání souboru *App_offline. htm* aplikace spustí další požadavek.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-498">When the *app_offline.htm* file is removed, the next request starts the app.</span></span>

<span data-ttu-id="f3b3d-499">Při použití nezpracovaného modelu hostování se aplikace nemusí okamžitě vypnout, pokud dojde k otevřenému připojení.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-499">When using the out-of-process hosting model, the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="f3b3d-500">Připojení k protokolu WebSocket může například zpozdit ukončení aplikace.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-500">For example, a websocket connection may delay app shut down.</span></span>

## <a name="start-up-error-page"></a><span data-ttu-id="f3b3d-501">Spouštěcí chybová stránka</span><span class="sxs-lookup"><span data-stu-id="f3b3d-501">Start-up error page</span></span>

<span data-ttu-id="f3b3d-502">Hostování v procesu i mimo procesy vytváří vlastní chybové stránky, když se nepodaří aplikaci spustit.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-502">Both in-process and out-of-process hosting produce custom error pages when they fail to start the app.</span></span>

<span data-ttu-id="f3b3d-503">Pokud modul ASP.NET Core nenalezne obslužnou rutinu žádosti v procesu nebo mimo proces 500,0, zobrazí se stránka s kódem stavu *selhání načtení obslužné rutiny v* procesu nebo mimo proces.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-503">If the ASP.NET Core Module fails to find either the in-process or out-of-process request handler, a *500.0 - In-Process/Out-Of-Process Handler Load Failure* status code page appears.</span></span>

<span data-ttu-id="f3b3d-504">Pro vnitroprocesové hostování v případě, že se modul ASP.NET Core nepodaří spustit aplikaci, zobrazí se stránka s kódem stavu *chyby při spuštění 500,30* .</span><span class="sxs-lookup"><span data-stu-id="f3b3d-504">For in-process hosting if the ASP.NET Core Module fails to start the app, a *500.30 - Start Failure* status code page appears.</span></span>

<span data-ttu-id="f3b3d-505">Pro hostování mimo procesy, pokud se ASP.NET Core modulu nespustí back-end proces, nebo dojde k selhání procesu back-endu, ale nenaslouchá na konfigurovaném portu, zobrazí se stránka s kódem stavu *selhání procesu 502,5* .</span><span class="sxs-lookup"><span data-stu-id="f3b3d-505">For out-of-process hosting if the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span>

<span data-ttu-id="f3b3d-506">Chcete-li tuto stránku potlačit a vrátit se na výchozí stavovou stránku služby IIS 5xx, použijte atribut `disableStartUpErrorPage`.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-506">To suppress this page and revert to the default IIS 5xx status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="f3b3d-507">Další informace o konfiguraci vlastních chybových zpráv najdete v tématu [chyby protokolu HTTP \<httpErrors >](/iis/configuration/system.webServer/httpErrors/).</span><span class="sxs-lookup"><span data-stu-id="f3b3d-507">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

## <a name="log-creation-and-redirection"></a><span data-ttu-id="f3b3d-508">Vytvoření a přesměrování protokolu</span><span class="sxs-lookup"><span data-stu-id="f3b3d-508">Log creation and redirection</span></span>

<span data-ttu-id="f3b3d-509">Modul ASP.NET Core přesměruje výstup konzoly stdout a stderr na disk, pokud jsou nastaveny atributy `stdoutLogEnabled` a `stdoutLogFile` prvku `aspNetCore`.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-509">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="f3b3d-510">Při vytvoření souboru protokolu jsou všechny složky v cestě `stdoutLogFile` vytvářeny modulem.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-510">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="f3b3d-511">Fond aplikací musí mít oprávnění k zápisu do umístění, kam se zapisují protokoly (k poskytnutí oprávnění k zápisu použijte `IIS AppPool\<app_pool_name>`).</span><span class="sxs-lookup"><span data-stu-id="f3b3d-511">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="f3b3d-512">Protokoly nejsou otočeny, pokud nedochází k recyklování/restartování procesu.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-512">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="f3b3d-513">Je zodpovědností hostitele omezit místo na disku, které používají protokoly.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-513">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="f3b3d-514">Použití protokolu stdout se doporučuje jenom při řešení problémů se spouštěním aplikací.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-514">Using the stdout log is only recommended for troubleshooting app startup issues.</span></span> <span data-ttu-id="f3b3d-515">Nepoužívejte protokol stdout pro obecné účely protokolování aplikace.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-515">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="f3b3d-516">Pro rutinu protokolování v aplikaci ASP.NET Core použijte knihovnu protokolování, která omezuje velikost souboru protokolu a otočí protokoly.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-516">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="f3b3d-517">Další informace najdete v tématu [Zprostředkovatelé protokolování třetích stran](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="f3b3d-517">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="f3b3d-518">Časové razítko a Přípona souboru se automaticky přidají při vytvoření souboru protokolu.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-518">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="f3b3d-519">Název souboru protokolu se skládá připojením časového razítka, ID procesu a přípony souboru ( *. log*) k poslednímu segmentu cesty `stdoutLogFile` (obvykle *stdout*), oddělený podtržítky.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-519">The log file name is composed by appending the timestamp, process ID, and file extension (*.log*) to the last segment of the `stdoutLogFile` path (typically *stdout*) delimited by underscores.</span></span> <span data-ttu-id="f3b3d-520">Pokud cesta `stdoutLogFile` končí na *stdout*, protokol pro aplikaci s číslem PID 1934 vytvořeným v 2/5/2018 v 19:42:32 má název souboru *stdout_20180205194132_1934. log*.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-520">If the `stdoutLogFile` path ends with *stdout*, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name *stdout_20180205194132_1934.log*.</span></span>

<span data-ttu-id="f3b3d-521">Pokud je hodnota `stdoutLogEnabled` false, budou chyby, ke kterým dojde při spuštění aplikace, zachyceny a generovány do protokolu událostí až do 30 KB.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-521">If `stdoutLogEnabled` is false, errors that occur on app startup are captured and emitted to the event log up to 30 KB.</span></span> <span data-ttu-id="f3b3d-522">Po spuštění budou všechny další protokoly zahozeny.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-522">After startup, all additional logs are discarded.</span></span>

<span data-ttu-id="f3b3d-523">Následující vzorový prvek `aspNetCore` nakonfiguruje protokolování stdout pro aplikaci hostovanou v Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-523">The following sample `aspNetCore` element configures stdout logging for an app hosted in Azure App Service.</span></span> <span data-ttu-id="f3b3d-524">Místní cesta nebo cesta ke sdílené síťové složce je přijatelná pro místní protokolování.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-524">A local path or network share path is acceptable for local logging.</span></span> <span data-ttu-id="f3b3d-525">Potvrďte, že identita uživatele fondu aplikací má oprávnění k zápisu do zadané cesty.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-525">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="InProcess">
</aspNetCore>
```

## <a name="enhanced-diagnostic-logs"></a><span data-ttu-id="f3b3d-526">Rozšířené diagnostické protokoly</span><span class="sxs-lookup"><span data-stu-id="f3b3d-526">Enhanced diagnostic logs</span></span>

<span data-ttu-id="f3b3d-527">Modul ASP.NET Core lze nakonfigurovat tak, aby poskytoval rozšířené diagnostické protokoly.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-527">The ASP.NET Core Module is configurable to provide enhanced diagnostics logs.</span></span> <span data-ttu-id="f3b3d-528">Přidejte `<handlerSettings>` elementu do prvku `<aspNetCore>` v *souboru Web. config*. Nastavení `debugLevel` na `TRACE` zpřístupňuje vyšší věrnost diagnostických informací:</span><span class="sxs-lookup"><span data-stu-id="f3b3d-528">Add the `<handlerSettings>` element to the `<aspNetCore>` element in *web.config*. Setting the `debugLevel` to `TRACE` exposes a higher fidelity of diagnostic information:</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="InProcess">
  <handlerSettings>
    <handlerSetting name="debugFile" value=".\logs\aspnetcore-debug.log" />
    <handlerSetting name="debugLevel" value="FILE,TRACE" />
  </handlerSettings>
</aspNetCore>
```

<span data-ttu-id="f3b3d-529">Složky v cestě zadané pro hodnotu `<handlerSetting>` (*protokoly* v předchozím příkladu) se nevytváří modulem automaticky a měly by být v nasazení předběžně existující.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-529">Folders in the path provided to the `<handlerSetting>` value (*logs* in the preceding example) aren't created by the module automatically and should pre-exist in the deployment.</span></span> <span data-ttu-id="f3b3d-530">Fond aplikací musí mít oprávnění k zápisu do umístění, kam se zapisují protokoly (k poskytnutí oprávnění k zápisu použijte `IIS AppPool\<app_pool_name>`).</span><span class="sxs-lookup"><span data-stu-id="f3b3d-530">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="f3b3d-531">Hodnoty úrovně ladění (`debugLevel`) mohou zahrnovat jak úroveň, tak i umístění.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-531">Debug level (`debugLevel`) values can include both the level and the location.</span></span>

<span data-ttu-id="f3b3d-532">Úrovně (v pořadí od nejméně po nejpodrobnější):</span><span class="sxs-lookup"><span data-stu-id="f3b3d-532">Levels (in order from least to most verbose):</span></span>

* <span data-ttu-id="f3b3d-533">Chyba</span><span class="sxs-lookup"><span data-stu-id="f3b3d-533">ERROR</span></span>
* <span data-ttu-id="f3b3d-534">Upozornění</span><span class="sxs-lookup"><span data-stu-id="f3b3d-534">WARNING</span></span>
* <span data-ttu-id="f3b3d-535">PŘÍJEMCE</span><span class="sxs-lookup"><span data-stu-id="f3b3d-535">INFO</span></span>
* <span data-ttu-id="f3b3d-536">TRACE</span><span class="sxs-lookup"><span data-stu-id="f3b3d-536">TRACE</span></span>

<span data-ttu-id="f3b3d-537">Umístění (je povoleno více umístění):</span><span class="sxs-lookup"><span data-stu-id="f3b3d-537">Locations (multiple locations are permitted):</span></span>

* <span data-ttu-id="f3b3d-538">STROMU</span><span class="sxs-lookup"><span data-stu-id="f3b3d-538">CONSOLE</span></span>
* <span data-ttu-id="f3b3d-539">EVENTLOG</span><span class="sxs-lookup"><span data-stu-id="f3b3d-539">EVENTLOG</span></span>
* <span data-ttu-id="f3b3d-540">SOUBORŮ</span><span class="sxs-lookup"><span data-stu-id="f3b3d-540">FILE</span></span>

<span data-ttu-id="f3b3d-541">Nastavení obslužné rutiny lze také poskytnout pomocí proměnných prostředí:</span><span class="sxs-lookup"><span data-stu-id="f3b3d-541">The handler settings can also be provided via environment variables:</span></span>

* <span data-ttu-id="f3b3d-542">`ASPNETCORE_MODULE_DEBUG_FILE` &ndash; cesta k souboru protokolu ladění.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-542">`ASPNETCORE_MODULE_DEBUG_FILE` &ndash; Path to the debug log file.</span></span> <span data-ttu-id="f3b3d-543">(Výchozí: *aspnetcore-Debug. log*)</span><span class="sxs-lookup"><span data-stu-id="f3b3d-543">(Default: *aspnetcore-debug.log*)</span></span>
* <span data-ttu-id="f3b3d-544">nastavení úrovně ladění `ASPNETCORE_MODULE_DEBUG` &ndash;.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-544">`ASPNETCORE_MODULE_DEBUG` &ndash; Debug level setting.</span></span>

> [!WARNING]
> <span data-ttu-id="f3b3d-545">Nenechávejte **protokolování** ladění povolené v nasazení po delší dobu, než je potřeba k řešení problému.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-545">Do **not** leave debug logging enabled in the deployment for longer than required to troubleshoot an issue.</span></span> <span data-ttu-id="f3b3d-546">Velikost protokolu není omezená.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-546">The size of the log isn't limited.</span></span> <span data-ttu-id="f3b3d-547">Když necháte protokol ladění povolený, může dojít k vyčerpání dostupného místa na disku a selhání serveru nebo služby App Service.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-547">Leaving the debug log enabled can exhaust the available disk space and crash the server or app service.</span></span>

<span data-ttu-id="f3b3d-548">Příklad prvku `aspNetCore` v souboru *Web. config* naleznete v tématu [Konfigurace pomocí souboru Web. config](#configuration-with-webconfig) .</span><span class="sxs-lookup"><span data-stu-id="f3b3d-548">See [Configuration with web.config](#configuration-with-webconfig) for an example of the `aspNetCore` element in the *web.config* file.</span></span>

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="f3b3d-549">Konfigurace proxy serveru používá protokol HTTP a token párování.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-549">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="f3b3d-550">*Platí pouze pro hostování mimo proces.*</span><span class="sxs-lookup"><span data-stu-id="f3b3d-550">*Only applies to out-of-process hosting.*</span></span>

<span data-ttu-id="f3b3d-551">Proxy vytvořený mezi modulem ASP.NET Core a Kestrel používá protokol HTTP.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-551">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="f3b3d-552">Nehrozí riziko odposlouchávání provozu mezi modulem a Kestrel z umístění od serveru.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-552">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="f3b3d-553">Párovací token se používá k zajištění, že žádosti přijaté službou Kestrel byly proxy službou IIS a nepocházejí z jiného zdroje.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-553">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="f3b3d-554">Token párování se vytvoří a nastaví na proměnnou prostředí (`ASPNETCORE_TOKEN`) modulem.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-554">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="f3b3d-555">Párování tokenu je také nastaveno na hlavičku (`MS-ASPNETCORE-TOKEN`) na každém proxy požadavku.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-555">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="f3b3d-556">Middleware služby IIS kontroluje každý požadavek, který obdrží, a potvrdí, že hodnota hlavičky párového tokenu odpovídá hodnotě proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-556">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="f3b3d-557">Pokud se hodnoty tokenu neshodují, požadavek se zaprotokoluje a odmítne.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-557">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="f3b3d-558">Proměnná prostředí párového tokenu a přenos dat mezi modulem a Kestrel nejsou přístupné z umístění mimo server.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-558">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="f3b3d-559">Bez znalosti hodnoty párovacího tokenu nemůže útočník odeslat žádosti, které obcházejí kontrolu v Middlewari IIS.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-559">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="f3b3d-560">ASP.NET Core modul se sdílenou konfigurací služby IIS</span><span class="sxs-lookup"><span data-stu-id="f3b3d-560">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="f3b3d-561">Instalační služba modulu ASP.NET Core se spouští s oprávněními účtu **TrustedInstaller** .</span><span class="sxs-lookup"><span data-stu-id="f3b3d-561">The ASP.NET Core Module installer runs with the privileges of the **TrustedInstaller** account.</span></span> <span data-ttu-id="f3b3d-562">Vzhledem k tomu, že účet místního systému nemá oprávnění k úpravám pro cestu ke sdílené složce, kterou používá sdílená konfigurace služby IIS, vyvolá Instalační program chybu odepření přístupu při pokusu o konfiguraci nastavení modulu v souboru *ApplicationHost. config* v sdílená složka.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-562">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the *applicationHost.config* file on the share.</span></span>

<span data-ttu-id="f3b3d-563">Pokud používáte sdílenou konfiguraci služby IIS na stejném počítači jako instalaci služby IIS, spusťte instalační program sady prostředků ASP.NET Core s parametrem `OPT_NO_SHARED_CONFIG_CHECK` nastaveným na `1`:</span><span class="sxs-lookup"><span data-stu-id="f3b3d-563">When using an IIS Shared Configuration on the same machine as the IIS installation, run the ASP.NET Core Hosting Bundle installer with the `OPT_NO_SHARED_CONFIG_CHECK` parameter set to `1`:</span></span>

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

<span data-ttu-id="f3b3d-564">Pokud cesta ke sdílené konfiguraci není ve stejném počítači jako instalace služby IIS, postupujte podle těchto kroků:</span><span class="sxs-lookup"><span data-stu-id="f3b3d-564">When the path to the shared configuration isn't on the same machine as the IIS installation, follow these steps:</span></span>

1. <span data-ttu-id="f3b3d-565">Zakažte sdílenou konfiguraci služby IIS.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-565">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="f3b3d-566">Spusťte instalační program.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-566">Run the installer.</span></span>
1. <span data-ttu-id="f3b3d-567">Exportujte aktualizovaný soubor *ApplicationHost. config* do sdílené složky.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-567">Export the updated *applicationHost.config* file to the share.</span></span>
1. <span data-ttu-id="f3b3d-568">Znovu povolte sdílenou konfiguraci služby IIS.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-568">Re-enable the IIS Shared Configuration.</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="f3b3d-569">Verze modulu a instalační protokoly hostitelské sady</span><span class="sxs-lookup"><span data-stu-id="f3b3d-569">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="f3b3d-570">Postup určení verze nainstalovaného modulu ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="f3b3d-570">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="f3b3d-571">V hostitelském systému přejděte na *%windir%\system32\inetsrv*.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-571">On the hosting system, navigate to *%windir%\System32\inetsrv*.</span></span>
1. <span data-ttu-id="f3b3d-572">Vyhledejte soubor *aspnetcore. dll* .</span><span class="sxs-lookup"><span data-stu-id="f3b3d-572">Locate the *aspnetcore.dll* file.</span></span>
1. <span data-ttu-id="f3b3d-573">Klikněte na soubor pravým tlačítkem a v místní nabídce vyberte **vlastnosti** .</span><span class="sxs-lookup"><span data-stu-id="f3b3d-573">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="f3b3d-574">Vyberte kartu **Podrobnosti** . **Verze souboru** a **verze produktu** reprezentují nainstalovanou verzi modulu.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-574">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="f3b3d-575">Protokoly instalačních balíčků pro modul se nacházejí v *C: \\Users @ no__t-2% username% \\AppData @ no__t-4Local @ no__t-5Temp*. Soubor má název *dd_DotNetCoreWinSvrHosting__ @ no__t-7timestamp > _000_AspNetCoreModule_x64. log*.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-575">The Hosting Bundle installer logs for the module are found at *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. The file is named *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="f3b3d-576">Umístění souboru modulu, schématu a konfiguračního souboru</span><span class="sxs-lookup"><span data-stu-id="f3b3d-576">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="f3b3d-577">Modul</span><span class="sxs-lookup"><span data-stu-id="f3b3d-577">Module</span></span>

<span data-ttu-id="f3b3d-578">**Služba IIS (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="f3b3d-578">**IIS (x86/amd64):**</span></span>

* <span data-ttu-id="f3b3d-579">%windir%\System32\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="f3b3d-579">%windir%\System32\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="f3b3d-580">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="f3b3d-580">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="f3b3d-581">%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="f3b3d-581">%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="f3b3d-582">% ProgramFiles (x86)% \ IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="f3b3d-582">%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

<span data-ttu-id="f3b3d-583">**IIS Express (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="f3b3d-583">**IIS Express (x86/amd64):**</span></span>

* <span data-ttu-id="f3b3d-584">%ProgramFiles%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="f3b3d-584">%ProgramFiles%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="f3b3d-585">% ProgramFiles (x86)% \ IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="f3b3d-585">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="f3b3d-586">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="f3b3d-586">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="f3b3d-587">% ProgramFiles (x86)% \ IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="f3b3d-587">%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

### <a name="schema"></a><span data-ttu-id="f3b3d-588">Schéma</span><span class="sxs-lookup"><span data-stu-id="f3b3d-588">Schema</span></span>

<span data-ttu-id="f3b3d-589">**SLUŽBU**</span><span class="sxs-lookup"><span data-stu-id="f3b3d-589">**IIS**</span></span>

* <span data-ttu-id="f3b3d-590">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="f3b3d-590">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span></span>

* <span data-ttu-id="f3b3d-591">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span><span class="sxs-lookup"><span data-stu-id="f3b3d-591">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span></span>

<span data-ttu-id="f3b3d-592">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="f3b3d-592">**IIS Express**</span></span>

* <span data-ttu-id="f3b3d-593">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="f3b3d-593">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span></span>

* <span data-ttu-id="f3b3d-594">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span><span class="sxs-lookup"><span data-stu-id="f3b3d-594">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span></span>

### <a name="configuration"></a><span data-ttu-id="f3b3d-595">Konfigurace</span><span class="sxs-lookup"><span data-stu-id="f3b3d-595">Configuration</span></span>

<span data-ttu-id="f3b3d-596">**SLUŽBU**</span><span class="sxs-lookup"><span data-stu-id="f3b3d-596">**IIS**</span></span>

* <span data-ttu-id="f3b3d-597">%windir%\System32\inetsrv\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="f3b3d-597">%windir%\System32\inetsrv\config\applicationHost.config</span></span>

<span data-ttu-id="f3b3d-598">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="f3b3d-598">**IIS Express**</span></span>

* <span data-ttu-id="f3b3d-599">Visual Studio: {ROOT aplikace} \\. vs\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="f3b3d-599">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span></span>

* <span data-ttu-id="f3b3d-600">*IISExpress. exe* CLI:%USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span><span class="sxs-lookup"><span data-stu-id="f3b3d-600">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span></span>

<span data-ttu-id="f3b3d-601">Soubory lze najít hledáním *aspnetcore* v souboru *ApplicationHost. config* .</span><span class="sxs-lookup"><span data-stu-id="f3b3d-601">The files can be found by searching for *aspnetcore* in the *applicationHost.config* file.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="f3b3d-602">Modul ASP.NET Core je nativním modulem služby IIS, který se připojuje k kanálu IIS a přesměrovává webové požadavky na back-endové aplikace ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-602">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to forward web requests to backend ASP.NET Core apps.</span></span>

<span data-ttu-id="f3b3d-603">Podporované verze systému Windows:</span><span class="sxs-lookup"><span data-stu-id="f3b3d-603">Supported Windows versions:</span></span>

* <span data-ttu-id="f3b3d-604">Windows 7 nebo novější</span><span class="sxs-lookup"><span data-stu-id="f3b3d-604">Windows 7 or later</span></span>
* <span data-ttu-id="f3b3d-605">Windows Server 2008 R2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="f3b3d-605">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="f3b3d-606">Modul funguje pouze s Kestrel.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-606">The module only works with Kestrel.</span></span> <span data-ttu-id="f3b3d-607">Modul není kompatibilní s [http. sys](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="f3b3d-607">The module is incompatible with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

<span data-ttu-id="f3b3d-608">Vzhledem k tomu, že ASP.NET Core aplikace běží v procesu odděleném od pracovního procesu služby IIS, modul také zpracovává správu procesů.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-608">Because ASP.NET Core apps run in a process separate from the IIS worker process, the module also handles process management.</span></span> <span data-ttu-id="f3b3d-609">Modul spustí proces pro aplikaci ASP.NET Core, když první požadavek dorazí a restartuje aplikaci, pokud dojde k chybě.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-609">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it crashes.</span></span> <span data-ttu-id="f3b3d-610">To je v podstatě stejné chování jako u aplikací ASP.NET 4. x spouštěných vnitroprocesově ve službě IIS, které spravuje [Aktivační služba procesů systému Windows (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span><span class="sxs-lookup"><span data-stu-id="f3b3d-610">This is essentially the same behavior as seen with ASP.NET 4.x apps that run in-process in IIS that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="f3b3d-611">Následující diagram znázorňuje vztah mezi službou IIS, modulem ASP.NET Core a aplikací:</span><span class="sxs-lookup"><span data-stu-id="f3b3d-611">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app:</span></span>

![Modul ASP.NET Core](aspnet-core-module/_static/ancm-outofprocess.png)

<span data-ttu-id="f3b3d-613">Požadavky přicházející z webu do ovladače HTTP. sys v režimu jádra.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-613">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="f3b3d-614">Ovladač směruje požadavky do služby IIS na konfigurovaném portu webu, obvykle 80 (HTTP) nebo 443 (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="f3b3d-614">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="f3b3d-615">Modul předá požadavky do Kestrel na náhodném portu pro aplikaci, což není port 80 nebo 443.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-615">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="f3b3d-616">Modul Určuje port prostřednictvím proměnné prostředí při spuštění a [Služba IIS Integration middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) nakonfiguruje server tak, aby naslouchal `http://localhost:{port}`.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-616">The module specifies the port via an environment variable at startup, and the [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) configures the server to listen on `http://localhost:{port}`.</span></span> <span data-ttu-id="f3b3d-617">Budou provedeny další kontroly a požadavky, které nepocházejí z modulu, jsou odmítnuty.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-617">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="f3b3d-618">Modul nepodporuje předávání HTTPS, takže požadavky se předávají přes protokol HTTP i v případě, že je služba IIS prostřednictvím protokolu HTTPS přijímá.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-618">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="f3b3d-619">Po Kestrel žádosti z modulu se požadavek odešle do kanálu middlewaru ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-619">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="f3b3d-620">Kanál middlewaru zpracovává požadavek a předá ho jako instanci `HttpContext` do logiky aplikace.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-620">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="f3b3d-621">Middleware přidaný integrací služby IIS: aktualizace schématu, vzdálené IP adresy a pathbase pro přesměrování požadavku do Kestrel.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-621">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="f3b3d-622">Odpověď aplikace se předává zpátky do služby IIS, která ji přenáší zpátky do klienta HTTP, který žádost inicioval.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-622">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="f3b3d-623">Mnoho nativních modulů, jako je ověřování systému Windows, zůstane aktivní.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-623">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="f3b3d-624">Další informace o aktivních modulech služby IIS pomocí modulu ASP.NET Core najdete v tématu <xref:host-and-deploy/iis/modules>.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-624">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="f3b3d-625">Modul ASP.NET Core může také:</span><span class="sxs-lookup"><span data-stu-id="f3b3d-625">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="f3b3d-626">Nastavte proměnné prostředí pro pracovní proces.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-626">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="f3b3d-627">Protokoluje výstup stdout do úložiště souborů pro řešení problémů při spuštění.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-627">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="f3b3d-628">Předejte tokeny ověřování systému Windows.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-628">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="f3b3d-629">Jak nainstalovat a používat modul ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f3b3d-629">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="f3b3d-630">Pokyny k instalaci modulu ASP.NET Core najdete v tématu [instalace hostující sady .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span><span class="sxs-lookup"><span data-stu-id="f3b3d-630">For instructions on how to install the ASP.NET Core Module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="f3b3d-631">Konfigurace pomocí souboru Web. config</span><span class="sxs-lookup"><span data-stu-id="f3b3d-631">Configuration with web.config</span></span>

<span data-ttu-id="f3b3d-632">ASP.NET Core modul je nakonfigurovaný pomocí oddílu `aspNetCore` v uzlu `system.webServer` v souboru *Web. config* tohoto webu.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-632">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="f3b3d-633">Následující soubor *Web. config* je publikován pro [nasazení závislé na rozhraní](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) a nakonfiguruje modul ASP.NET Core pro zpracování požadavků lokality:</span><span class="sxs-lookup"><span data-stu-id="f3b3d-633">The following *web.config* file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

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

<span data-ttu-id="f3b3d-634">Následující soubor *Web. config* je publikován pro [samostatně uzavřené nasazení](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span><span class="sxs-lookup"><span data-stu-id="f3b3d-634">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

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

<span data-ttu-id="f3b3d-635">Když se aplikace nasadí do [Azure App Service](https://azure.microsoft.com/services/app-service/), cesta `stdoutLogFile` je nastavená na `\\?\%home%\LogFiles\stdout`.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-635">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="f3b3d-636">Cesta ukládá protokoly stdout do složky *soubory protokolů* , což je umístění, které služba automaticky vytvořila.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-636">The path saves stdout logs to the *LogFiles* folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="f3b3d-637">Informace o konfiguraci dílčí aplikace IIS najdete v tématu <xref:host-and-deploy/iis/index#sub-applications>.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-637">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="f3b3d-638">Atributy elementu aspNetCore</span><span class="sxs-lookup"><span data-stu-id="f3b3d-638">Attributes of the aspNetCore element</span></span>

| <span data-ttu-id="f3b3d-639">Atribut</span><span class="sxs-lookup"><span data-stu-id="f3b3d-639">Attribute</span></span> | <span data-ttu-id="f3b3d-640">Popis</span><span class="sxs-lookup"><span data-stu-id="f3b3d-640">Description</span></span> | <span data-ttu-id="f3b3d-641">Výchozí</span><span class="sxs-lookup"><span data-stu-id="f3b3d-641">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="f3b3d-642">Volitelný řetězcový atribut.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-642">Optional string attribute.</span></span></p><p><span data-ttu-id="f3b3d-643">Argumenty ke spustitelnému souboru určenému v **processPath**.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-643">Arguments to the executable specified in **processPath**.</span></span></p>| |
| `disableStartUpErrorPage` | <p><span data-ttu-id="f3b3d-644">Volitelný atribut typu Boolean.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-644">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="f3b3d-645">Pokud je nastaveno na true, stránka **502,5 – selhání procesu** se potlačí a stavová stránka 502 konfigurovaná v *souboru Web. config* má přednost.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-645">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="f3b3d-646">Volitelný atribut typu Boolean.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-646">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="f3b3d-647">Pokud je nastaveno na true, token se přepošle podřízenému procesu, který naslouchá na% ASPNETCORE_PORT% jako hlavička MS-ASPNETCORE-WINAUTHTOKEN na vyžádání.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-647">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="f3b3d-648">Je zodpovědností za tento proces volání CloseHandle na tento token na žádost.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-648">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `processesPerApplication` | <p><span data-ttu-id="f3b3d-649">Volitelný celočíselný atribut</span><span class="sxs-lookup"><span data-stu-id="f3b3d-649">Optional integer attribute.</span></span></p><p><span data-ttu-id="f3b3d-650">Určuje počet instancí procesu určeného v nastavení **processPath** , které lze v rámci aplikace vystavit.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-650">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="f3b3d-651">Nastavení `processesPerApplication` se nedoporučuje.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-651">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="f3b3d-652">Tento atribut bude v budoucí verzi odebrán.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-652">This attribute will be removed in a future release.</span></span></p> | <span data-ttu-id="f3b3d-653">Výchozí: `1`</span><span class="sxs-lookup"><span data-stu-id="f3b3d-653">Default: `1`</span></span><br><span data-ttu-id="f3b3d-654">Minimum: `1`</span><span class="sxs-lookup"><span data-stu-id="f3b3d-654">Min: `1`</span></span><br><span data-ttu-id="f3b3d-655">Max: `100`</span><span class="sxs-lookup"><span data-stu-id="f3b3d-655">Max: `100`</span></span> |
| `processPath` | <p><span data-ttu-id="f3b3d-656">Povinný atribut řetězce.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-656">Required string attribute.</span></span></p><p><span data-ttu-id="f3b3d-657">Cesta ke spustitelnému souboru, který spouští proces naslouchající na požadavky HTTP.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-657">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="f3b3d-658">Jsou podporovány relativní cesty.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-658">Relative paths are supported.</span></span> <span data-ttu-id="f3b3d-659">Pokud cesta začíná na `.`, bude cesta považována za relativní vzhledem k kořenu webu.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-659">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="f3b3d-660">Volitelný celočíselný atribut</span><span class="sxs-lookup"><span data-stu-id="f3b3d-660">Optional integer attribute.</span></span></p><p><span data-ttu-id="f3b3d-661">Určuje, kolikrát je povoleno selhání procesu určeného v **processPath** za minutu.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-661">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="f3b3d-662">Pokud je tento limit překročen, modul ukončí spuštění procesu po zbytek minuty.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-662">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p> | <span data-ttu-id="f3b3d-663">Výchozí: `10`</span><span class="sxs-lookup"><span data-stu-id="f3b3d-663">Default: `10`</span></span><br><span data-ttu-id="f3b3d-664">Minimum: `0`</span><span class="sxs-lookup"><span data-stu-id="f3b3d-664">Min: `0`</span></span><br><span data-ttu-id="f3b3d-665">Max: `100`</span><span class="sxs-lookup"><span data-stu-id="f3b3d-665">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="f3b3d-666">Volitelný atribut TimeSpan.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-666">Optional timespan attribute.</span></span></p><p><span data-ttu-id="f3b3d-667">Určuje dobu, po kterou modul ASP.NET Core čeká na odezvu od procesu, na kterém naslouchá% ASPNETCORE_PORT%.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-667">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="f3b3d-668">Ve verzích modulu ASP.NET Core, které byly dodávány s vydáním ASP.NET Core 2,1 nebo vyšší, je `requestTimeout` zadáno v hodinách, minutách a sekundách.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-668">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p> | <span data-ttu-id="f3b3d-669">Výchozí: `00:02:00`</span><span class="sxs-lookup"><span data-stu-id="f3b3d-669">Default: `00:02:00`</span></span><br><span data-ttu-id="f3b3d-670">Minimum: `00:00:00`</span><span class="sxs-lookup"><span data-stu-id="f3b3d-670">Min: `00:00:00`</span></span><br><span data-ttu-id="f3b3d-671">Max: `360:00:00`</span><span class="sxs-lookup"><span data-stu-id="f3b3d-671">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="f3b3d-672">Volitelný celočíselný atribut</span><span class="sxs-lookup"><span data-stu-id="f3b3d-672">Optional integer attribute.</span></span></p><p><span data-ttu-id="f3b3d-673">Doba v sekundách, po kterou modul čeká na řádné vypnutí spustitelného souboru, když se zjistí soubor *App_offline. htm* .</span><span class="sxs-lookup"><span data-stu-id="f3b3d-673">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="f3b3d-674">Výchozí: `10`</span><span class="sxs-lookup"><span data-stu-id="f3b3d-674">Default: `10`</span></span><br><span data-ttu-id="f3b3d-675">Minimum: `0`</span><span class="sxs-lookup"><span data-stu-id="f3b3d-675">Min: `0`</span></span><br><span data-ttu-id="f3b3d-676">Max: `600`</span><span class="sxs-lookup"><span data-stu-id="f3b3d-676">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="f3b3d-677">Volitelný celočíselný atribut</span><span class="sxs-lookup"><span data-stu-id="f3b3d-677">Optional integer attribute.</span></span></p><p><span data-ttu-id="f3b3d-678">Doba v sekundách, kterou modul počká, než spustitelný soubor spustí proces, který na portu naslouchá.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-678">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="f3b3d-679">Pokud je tento časový limit překročen, modul proces ukončuje.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-679">If this time limit is exceeded, the module kills the process.</span></span> <span data-ttu-id="f3b3d-680">Modul se pokusí znovu spustit proces, když obdrží novou žádost, a pokračuje v pokusu o restartování procesu u dalších příchozích požadavků, pokud se nepodaří spustit **rapidFailsPerMinute** počet pokusů během poslední minuty.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-680">The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="f3b3d-681">Hodnota 0 (nula **) není považována za** nekonečný časový limit.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-681">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="f3b3d-682">Výchozí: `120`</span><span class="sxs-lookup"><span data-stu-id="f3b3d-682">Default: `120`</span></span><br><span data-ttu-id="f3b3d-683">Minimum: `0`</span><span class="sxs-lookup"><span data-stu-id="f3b3d-683">Min: `0`</span></span><br><span data-ttu-id="f3b3d-684">Max: `3600`</span><span class="sxs-lookup"><span data-stu-id="f3b3d-684">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="f3b3d-685">Volitelný atribut typu Boolean.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-685">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="f3b3d-686">Je-li nastavena hodnota true, **stdout** a **stderr** pro proces zadaný v **processPath** budou přesměrovány do souboru zadaného v **stdoutLogFile**.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-686">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="f3b3d-687">Volitelný řetězcový atribut.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-687">Optional string attribute.</span></span></p><p><span data-ttu-id="f3b3d-688">Určuje relativní nebo absolutní cestu k souboru, pro který je **stdout** a **stderr** z procesu určeného v **processPath** protokolováno.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-688">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="f3b3d-689">Relativní cesty jsou relativní vzhledem k kořenu webu.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-689">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="f3b3d-690">Všechny cesty začínající na `.` jsou relativní vzhledem k kořenu webu a všechny ostatní cesty se považují za absolutní cesty.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-690">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="f3b3d-691">Aby modul vytvořil soubor protokolu, musí existovat všechny složky, které jsou v cestě k dispozici.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-691">Any folders provided in the path must exist in order for the module to create the log file.</span></span> <span data-ttu-id="f3b3d-692">Pomocí oddělovačů podtržítka se do posledního segmentu cesty **stdoutLogFile** přidá časové razítko, ID procesu a Přípona souboru ( *. log*).</span><span class="sxs-lookup"><span data-stu-id="f3b3d-692">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="f3b3d-693">Pokud je jako hodnota zadána hodnota `.\logs\stdout`, je ukázkový protokol stdout uložen jako *stdout_20180205194132_1934. log* ve složce *logs* při uložení na 2/5/2018 v 19:41:32 s ID procesu 1934.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-693">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

### <a name="setting-environment-variables"></a><span data-ttu-id="f3b3d-694">Nastavení proměnných prostředí</span><span class="sxs-lookup"><span data-stu-id="f3b3d-694">Setting environment variables</span></span>

<span data-ttu-id="f3b3d-695">Proměnné prostředí lze zadat pro proces v atributu `processPath`.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-695">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="f3b3d-696">Zadejte proměnnou prostředí s podřízeným prvkem `<environmentVariable>` elementu kolekce `<environmentVariables>`.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-696">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span>

> [!WARNING]
> <span data-ttu-id="f3b3d-697">Proměnné prostředí nastavené v této části jsou v konfliktu se sadou proměnných prostředí systému se stejným názvem.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-697">Environment variables set in this section conflict with system environment variables set with the same name.</span></span> <span data-ttu-id="f3b3d-698">Pokud je proměnná prostředí nastavena v souboru *Web. config* i na úrovni systému systému Windows, bude hodnota ze souboru *Web. config* připojena k hodnotě proměnné prostředí systému (například `ASPNETCORE_ENVIRONMENT: Development;Development`), která brání aplikaci. začátek.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-698">If an environment variable is set in both the *web.config* file and at the system level in Windows, the value from the *web.config* file becomes appended to the system environment variable value (for example, `ASPNETCORE_ENVIRONMENT: Development;Development`), which prevents the app from starting.</span></span>

<span data-ttu-id="f3b3d-699">Následující příklad nastaví dvě proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-699">The following example sets two environment variables.</span></span> <span data-ttu-id="f3b3d-700">`ASPNETCORE_ENVIRONMENT` nakonfiguruje prostředí aplikace na `Development`.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-700">`ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="f3b3d-701">Vývojář může tuto hodnotu dočasně nastavit v souboru *Web. config* , aby bylo možné vynutit, aby se [Stránka s výjimkou vývojářů](xref:fundamentals/error-handling) načetla při ladění výjimky aplikace.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-701">A developer may temporarily set this value in the *web.config* file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="f3b3d-702">`CONFIG_DIR` je příkladem uživatelsky definované proměnné prostředí, kde vývojář napsal kód, který přečte hodnotu při spuštění, aby vytvořil cestu pro načtení konfiguračního souboru aplikace.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-702">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

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
> <span data-ttu-id="f3b3d-703">Nastavte pouze proměnnou prostředí `ASPNETCORE_ENVIRONMENT` na `Development` na pracovních a testovacích serverech, které nejsou přístupné pro nedůvěryhodné sítě, jako je například Internet.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-703">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## <a name="app_offlinehtm"></a><span data-ttu-id="f3b3d-704">App_offline. htm</span><span class="sxs-lookup"><span data-stu-id="f3b3d-704">app_offline.htm</span></span>

<span data-ttu-id="f3b3d-705">Pokud se v kořenovém adresáři aplikace zjistí soubor s názvem *App_offline. htm* , modul ASP.NET Core se pokusí aplikaci řádně vypnout a zastaví zpracování příchozích požadavků.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-705">If a file with the name *app_offline.htm* is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="f3b3d-706">Pokud aplikace běží i po uplynutí počtu sekund definovaného v `shutdownTimeLimit`, modul ASP.NET Core ukončuje běžící proces.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-706">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="f3b3d-707">Když je přítomen soubor *App_offline. htm* , ASP.NET Core modul reaguje na požadavky odesláním zpět obsahu souboru *App_offline. htm* .</span><span class="sxs-lookup"><span data-stu-id="f3b3d-707">While the *app_offline.htm* file is present, the ASP.NET Core Module responds to requests by sending back the contents of the *app_offline.htm* file.</span></span> <span data-ttu-id="f3b3d-708">Po odebrání souboru *App_offline. htm* aplikace spustí další požadavek.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-708">When the *app_offline.htm* file is removed, the next request starts the app.</span></span>

## <a name="start-up-error-page"></a><span data-ttu-id="f3b3d-709">Spouštěcí chybová stránka</span><span class="sxs-lookup"><span data-stu-id="f3b3d-709">Start-up error page</span></span>

<span data-ttu-id="f3b3d-710">Pokud se v modulu ASP.NET Core nepodaří spustit back-end proces nebo se spustí back-end proces, ale nenaslouchá na konfigurovaném portu, zobrazí se stránka s kódem stavu *selhání procesu 502,5* .</span><span class="sxs-lookup"><span data-stu-id="f3b3d-710">If the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span> <span data-ttu-id="f3b3d-711">Chcete-li tuto stránku potlačit a vrátit se na výchozí stavovou stránku služby IIS 502, použijte atribut `disableStartUpErrorPage`.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-711">To suppress this page and revert to the default IIS 502 status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="f3b3d-712">Další informace o konfiguraci vlastních chybových zpráv najdete v tématu [chyby protokolu HTTP \<httpErrors >](/iis/configuration/system.webServer/httpErrors/).</span><span class="sxs-lookup"><span data-stu-id="f3b3d-712">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

![Stavová stránka selhání procesu procesu 502,5](aspnet-core-module/_static/ANCM-502_5.png)

## <a name="log-creation-and-redirection"></a><span data-ttu-id="f3b3d-714">Vytvoření a přesměrování protokolu</span><span class="sxs-lookup"><span data-stu-id="f3b3d-714">Log creation and redirection</span></span>

<span data-ttu-id="f3b3d-715">Modul ASP.NET Core přesměruje výstup konzoly stdout a stderr na disk, pokud jsou nastaveny atributy `stdoutLogEnabled` a `stdoutLogFile` prvku `aspNetCore`.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-715">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="f3b3d-716">Při vytvoření souboru protokolu jsou všechny složky v cestě `stdoutLogFile` vytvářeny modulem.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-716">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="f3b3d-717">Fond aplikací musí mít oprávnění k zápisu do umístění, kam se zapisují protokoly (k poskytnutí oprávnění k zápisu použijte `IIS AppPool\<app_pool_name>`).</span><span class="sxs-lookup"><span data-stu-id="f3b3d-717">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="f3b3d-718">Protokoly nejsou otočeny, pokud nedochází k recyklování/restartování procesu.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-718">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="f3b3d-719">Je zodpovědností hostitele omezit místo na disku, které používají protokoly.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-719">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="f3b3d-720">Použití protokolu stdout se doporučuje jenom při řešení problémů se spouštěním aplikací.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-720">Using the stdout log is only recommended for troubleshooting app startup issues.</span></span> <span data-ttu-id="f3b3d-721">Nepoužívejte protokol stdout pro obecné účely protokolování aplikace.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-721">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="f3b3d-722">Pro rutinu protokolování v aplikaci ASP.NET Core použijte knihovnu protokolování, která omezuje velikost souboru protokolu a otočí protokoly.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-722">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="f3b3d-723">Další informace najdete v tématu [Zprostředkovatelé protokolování třetích stran](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="f3b3d-723">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="f3b3d-724">Časové razítko a Přípona souboru se automaticky přidají při vytvoření souboru protokolu.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-724">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="f3b3d-725">Název souboru protokolu se skládá připojením časového razítka, ID procesu a přípony souboru ( *. log*) k poslednímu segmentu cesty `stdoutLogFile` (obvykle *stdout*), oddělený podtržítky.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-725">The log file name is composed by appending the timestamp, process ID, and file extension (*.log*) to the last segment of the `stdoutLogFile` path (typically *stdout*) delimited by underscores.</span></span> <span data-ttu-id="f3b3d-726">Pokud cesta `stdoutLogFile` končí na *stdout*, protokol pro aplikaci s číslem PID 1934 vytvořeným v 2/5/2018 v 19:42:32 má název souboru *stdout_20180205194132_1934. log*.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-726">If the `stdoutLogFile` path ends with *stdout*, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name *stdout_20180205194132_1934.log*.</span></span>

<span data-ttu-id="f3b3d-727">Následující vzorový prvek `aspNetCore` nakonfiguruje protokolování stdout pro aplikaci hostovanou v Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-727">The following sample `aspNetCore` element configures stdout logging for an app hosted in Azure App Service.</span></span> <span data-ttu-id="f3b3d-728">Místní cesta nebo cesta ke sdílené síťové složce je přijatelná pro místní protokolování.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-728">A local path or network share path is acceptable for local logging.</span></span> <span data-ttu-id="f3b3d-729">Potvrďte, že identita uživatele fondu aplikací má oprávnění k zápisu do zadané cesty.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-729">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile="\\?\%home%\LogFiles\stdout">
</aspNetCore>
```

<span data-ttu-id="f3b3d-730">Složky v cestě zadané pro hodnotu `<handlerSetting>` (*protokoly* v předchozím příkladu) se nevytváří modulem automaticky a měly by být v nasazení předběžně existující.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-730">Folders in the path provided to the `<handlerSetting>` value (*logs* in the preceding example) aren't created by the module automatically and should pre-exist in the deployment.</span></span> <span data-ttu-id="f3b3d-731">Fond aplikací musí mít oprávnění k zápisu do umístění, kam se zapisují protokoly (k poskytnutí oprávnění k zápisu použijte `IIS AppPool\<app_pool_name>`).</span><span class="sxs-lookup"><span data-stu-id="f3b3d-731">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="f3b3d-732">Příklad prvku `aspNetCore` v souboru *Web. config* naleznete v tématu [Konfigurace pomocí souboru Web. config](#configuration-with-webconfig) .</span><span class="sxs-lookup"><span data-stu-id="f3b3d-732">See [Configuration with web.config](#configuration-with-webconfig) for an example of the `aspNetCore` element in the *web.config* file.</span></span>

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="f3b3d-733">Konfigurace proxy serveru používá protokol HTTP a token párování.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-733">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="f3b3d-734">Proxy vytvořený mezi modulem ASP.NET Core a Kestrel používá protokol HTTP.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-734">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="f3b3d-735">Nehrozí riziko odposlouchávání provozu mezi modulem a Kestrel z umístění od serveru.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-735">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="f3b3d-736">Párovací token se používá k zajištění, že žádosti přijaté službou Kestrel byly proxy službou IIS a nepocházejí z jiného zdroje.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-736">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="f3b3d-737">Token párování se vytvoří a nastaví na proměnnou prostředí (`ASPNETCORE_TOKEN`) modulem.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-737">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="f3b3d-738">Párování tokenu je také nastaveno na hlavičku (`MS-ASPNETCORE-TOKEN`) na každém proxy požadavku.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-738">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="f3b3d-739">Middleware služby IIS kontroluje každý požadavek, který obdrží, a potvrdí, že hodnota hlavičky párového tokenu odpovídá hodnotě proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-739">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="f3b3d-740">Pokud se hodnoty tokenu neshodují, požadavek se zaprotokoluje a odmítne.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-740">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="f3b3d-741">Proměnná prostředí párového tokenu a přenos dat mezi modulem a Kestrel nejsou přístupné z umístění mimo server.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-741">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="f3b3d-742">Bez znalosti hodnoty párovacího tokenu nemůže útočník odeslat žádosti, které obcházejí kontrolu v Middlewari IIS.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-742">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="f3b3d-743">ASP.NET Core modul se sdílenou konfigurací služby IIS</span><span class="sxs-lookup"><span data-stu-id="f3b3d-743">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="f3b3d-744">Instalační služba modulu ASP.NET Core se spouští s oprávněními účtu **TrustedInstaller** .</span><span class="sxs-lookup"><span data-stu-id="f3b3d-744">The ASP.NET Core Module installer runs with the privileges of the **TrustedInstaller** account.</span></span> <span data-ttu-id="f3b3d-745">Vzhledem k tomu, že účet místního systému nemá oprávnění k úpravám pro cestu ke sdílené složce, kterou používá sdílená konfigurace služby IIS, vyvolá Instalační program chybu odepření přístupu při pokusu o konfiguraci nastavení modulu v souboru *ApplicationHost. config* v sdílená složka.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-745">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the *applicationHost.config* file on the share.</span></span>

<span data-ttu-id="f3b3d-746">Pokud používáte sdílenou konfiguraci služby IIS, postupujte podle těchto kroků:</span><span class="sxs-lookup"><span data-stu-id="f3b3d-746">When using an IIS Shared Configuration, follow these steps:</span></span>

1. <span data-ttu-id="f3b3d-747">Zakažte sdílenou konfiguraci služby IIS.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-747">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="f3b3d-748">Spusťte instalační program.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-748">Run the installer.</span></span>
1. <span data-ttu-id="f3b3d-749">Exportujte aktualizovaný soubor *ApplicationHost. config* do sdílené složky.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-749">Export the updated *applicationHost.config* file to the share.</span></span>
1. <span data-ttu-id="f3b3d-750">Znovu povolte sdílenou konfiguraci služby IIS.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-750">Re-enable the IIS Shared Configuration.</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="f3b3d-751">Verze modulu a instalační protokoly hostitelské sady</span><span class="sxs-lookup"><span data-stu-id="f3b3d-751">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="f3b3d-752">Postup určení verze nainstalovaného modulu ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="f3b3d-752">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="f3b3d-753">V hostitelském systému přejděte na *%windir%\system32\inetsrv*.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-753">On the hosting system, navigate to *%windir%\System32\inetsrv*.</span></span>
1. <span data-ttu-id="f3b3d-754">Vyhledejte soubor *aspnetcore. dll* .</span><span class="sxs-lookup"><span data-stu-id="f3b3d-754">Locate the *aspnetcore.dll* file.</span></span>
1. <span data-ttu-id="f3b3d-755">Klikněte na soubor pravým tlačítkem a v místní nabídce vyberte **vlastnosti** .</span><span class="sxs-lookup"><span data-stu-id="f3b3d-755">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="f3b3d-756">Vyberte kartu **Podrobnosti** . **Verze souboru** a **verze produktu** reprezentují nainstalovanou verzi modulu.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-756">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="f3b3d-757">Protokoly instalačních balíčků pro modul se nacházejí v *C: \\Users @ no__t-2% username% \\AppData @ no__t-4Local @ no__t-5Temp*. Soubor má název *dd_DotNetCoreWinSvrHosting__ @ no__t-7timestamp > _000_AspNetCoreModule_x64. log*.</span><span class="sxs-lookup"><span data-stu-id="f3b3d-757">The Hosting Bundle installer logs for the module are found at *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. The file is named *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="f3b3d-758">Umístění souboru modulu, schématu a konfiguračního souboru</span><span class="sxs-lookup"><span data-stu-id="f3b3d-758">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="f3b3d-759">Modul</span><span class="sxs-lookup"><span data-stu-id="f3b3d-759">Module</span></span>

<span data-ttu-id="f3b3d-760">**Služba IIS (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="f3b3d-760">**IIS (x86/amd64):**</span></span>

* <span data-ttu-id="f3b3d-761">%windir%\System32\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="f3b3d-761">%windir%\System32\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="f3b3d-762">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="f3b3d-762">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span></span>

<span data-ttu-id="f3b3d-763">**IIS Express (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="f3b3d-763">**IIS Express (x86/amd64):**</span></span>

* <span data-ttu-id="f3b3d-764">%ProgramFiles%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="f3b3d-764">%ProgramFiles%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="f3b3d-765">% ProgramFiles (x86)% \ IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="f3b3d-765">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span></span>

### <a name="schema"></a><span data-ttu-id="f3b3d-766">Schéma</span><span class="sxs-lookup"><span data-stu-id="f3b3d-766">Schema</span></span>

<span data-ttu-id="f3b3d-767">**SLUŽBU**</span><span class="sxs-lookup"><span data-stu-id="f3b3d-767">**IIS**</span></span>

* <span data-ttu-id="f3b3d-768">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="f3b3d-768">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span></span>

<span data-ttu-id="f3b3d-769">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="f3b3d-769">**IIS Express**</span></span>

* <span data-ttu-id="f3b3d-770">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="f3b3d-770">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span></span>

### <a name="configuration"></a><span data-ttu-id="f3b3d-771">Konfigurace</span><span class="sxs-lookup"><span data-stu-id="f3b3d-771">Configuration</span></span>

<span data-ttu-id="f3b3d-772">**SLUŽBU**</span><span class="sxs-lookup"><span data-stu-id="f3b3d-772">**IIS**</span></span>

* <span data-ttu-id="f3b3d-773">%windir%\System32\inetsrv\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="f3b3d-773">%windir%\System32\inetsrv\config\applicationHost.config</span></span>

<span data-ttu-id="f3b3d-774">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="f3b3d-774">**IIS Express**</span></span>

* <span data-ttu-id="f3b3d-775">Visual Studio: {ROOT aplikace} \\. vs\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="f3b3d-775">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span></span>

* <span data-ttu-id="f3b3d-776">*IISExpress. exe* CLI:%USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span><span class="sxs-lookup"><span data-stu-id="f3b3d-776">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span></span>

<span data-ttu-id="f3b3d-777">Soubory lze najít hledáním *aspnetcore* v souboru *ApplicationHost. config* .</span><span class="sxs-lookup"><span data-stu-id="f3b3d-777">The files can be found by searching for *aspnetcore* in the *applicationHost.config* file.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="f3b3d-778">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="f3b3d-778">Additional resources</span></span>

* <xref:host-and-deploy/iis/index>
* [<span data-ttu-id="f3b3d-779">Úložiště GitHub modulu ASP.NET Core (odkazový zdroj)</span><span class="sxs-lookup"><span data-stu-id="f3b3d-779">ASP.NET Core Module GitHub repository (reference source)</span></span>](https://github.com/aspnet/AspNetCoreModule)
* <xref:host-and-deploy/iis/modules>
