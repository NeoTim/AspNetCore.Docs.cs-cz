---
title: Modul ASP.NET Core
author: guardrex
description: Naučte se konfigurovat modul ASP.NET Core pro hostování ASP.NET Corech aplikací.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/07/2019
uid: host-and-deploy/aspnet-core-module
ms.openlocfilehash: c9bbd36b8a55b837f6d78abf99215c5496895a39
ms.sourcegitcommit: 67116718dc33a7a01696d41af38590fdbb58e014
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2019
ms.locfileid: "73799417"
---
# <a name="aspnet-core-module"></a><span data-ttu-id="5032e-103">Modul ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5032e-103">ASP.NET Core Module</span></span>

<span data-ttu-id="5032e-104">Díky [Dykstra](https://github.com/tdykstra), [Rick Strahl](https://github.com/RickStrahl), [Novák Rossův](https://github.com/Tratcher), [Rick Anderson](https://twitter.com/RickAndMSFT), [sourabh Shirhatti](https://twitter.com/sshirhatti), [Justin Kotalik](https://github.com/jkotalik)a [Luke](https://github.com/guardrex) Latham</span><span class="sxs-lookup"><span data-stu-id="5032e-104">By [Tom Dykstra](https://github.com/tdykstra), [Rick Strahl](https://github.com/RickStrahl), [Chris Ross](https://github.com/Tratcher), [Rick Anderson](https://twitter.com/RickAndMSFT), [Sourabh Shirhatti](https://twitter.com/sshirhatti), [Justin Kotalik](https://github.com/jkotalik), and [Luke Latham](https://github.com/guardrex)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="5032e-105">Modul ASP.NET Core je nativním modulem služby IIS, který se připojuje k kanálu IIS na jednu z těchto:</span><span class="sxs-lookup"><span data-stu-id="5032e-105">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to either:</span></span>

* <span data-ttu-id="5032e-106">Hostování aplikace ASP.NET Core uvnitř pracovního procesu služby IIS (`w3wp.exe`) se nazývá [model hostování v rámci procesu](#in-process-hosting-model).</span><span class="sxs-lookup"><span data-stu-id="5032e-106">Host an ASP.NET Core app inside of the IIS worker process (`w3wp.exe`), called the [in-process hosting model](#in-process-hosting-model).</span></span>
* <span data-ttu-id="5032e-107">Dopředné webové požadavky do back-endu ASP.NET Core aplikace, na které běží [Server Kestrel](xref:fundamentals/servers/kestrel), se nazývá [model hostování mimo proces](#out-of-process-hosting-model).</span><span class="sxs-lookup"><span data-stu-id="5032e-107">Forward web requests to a backend ASP.NET Core app running the [Kestrel server](xref:fundamentals/servers/kestrel), called the [out-of-process hosting model](#out-of-process-hosting-model).</span></span>

<span data-ttu-id="5032e-108">Podporované verze systému Windows:</span><span class="sxs-lookup"><span data-stu-id="5032e-108">Supported Windows versions:</span></span>

* <span data-ttu-id="5032e-109">Windows 7 nebo novější</span><span class="sxs-lookup"><span data-stu-id="5032e-109">Windows 7 or later</span></span>
* <span data-ttu-id="5032e-110">Windows Server 2008 R2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="5032e-110">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="5032e-111">Při hostování v procesu používá modul implementaci vnitroprocesového serveru pro službu IIS, která se nazývá IIS HTTP Server (`IISHttpServer`).</span><span class="sxs-lookup"><span data-stu-id="5032e-111">When hosting in-process, the module uses an in-process server implementation for IIS, called IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="5032e-112">Při hostování mimo proces funguje modul pouze s Kestrel.</span><span class="sxs-lookup"><span data-stu-id="5032e-112">When hosting out-of-process, the module only works with Kestrel.</span></span> <span data-ttu-id="5032e-113">Modul nefunguje s [ovladačem HTTP. sys](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="5032e-113">The module doesn't function with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

## <a name="hosting-models"></a><span data-ttu-id="5032e-114">Modely hostingu</span><span class="sxs-lookup"><span data-stu-id="5032e-114">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="5032e-115">Model hostování v procesu</span><span class="sxs-lookup"><span data-stu-id="5032e-115">In-process hosting model</span></span>

<span data-ttu-id="5032e-116">ASP.NET Core aplikace jako výchozí pro model hostování v rámci procesu.</span><span class="sxs-lookup"><span data-stu-id="5032e-116">ASP.NET Core apps default to the in-process hosting model.</span></span>

<span data-ttu-id="5032e-117">Následující vlastnosti platí při hostování v procesu:</span><span class="sxs-lookup"><span data-stu-id="5032e-117">The following characteristics apply when hosting in-process:</span></span>

* <span data-ttu-id="5032e-118">Místo [Kestrel](xref:fundamentals/servers/kestrel) serveru se používá server HTTP služby IIS (`IISHttpServer`).</span><span class="sxs-lookup"><span data-stu-id="5032e-118">IIS HTTP Server (`IISHttpServer`) is used instead of [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span> <span data-ttu-id="5032e-119">Pro vnitroprocesové [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) volání <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> do:</span><span class="sxs-lookup"><span data-stu-id="5032e-119">For in-process, [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> to:</span></span>

  * <span data-ttu-id="5032e-120">Zaregistrujte `IISHttpServer`.</span><span class="sxs-lookup"><span data-stu-id="5032e-120">Register the `IISHttpServer`.</span></span>
  * <span data-ttu-id="5032e-121">Nakonfigurujte port a základní cestu, na kterých má Server naslouchat při spuštění za modulem ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5032e-121">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
  * <span data-ttu-id="5032e-122">Nakonfigurujte hostitele tak, aby zachytával chyby při spuštění.</span><span class="sxs-lookup"><span data-stu-id="5032e-122">Configure the host to capture startup errors.</span></span>

* <span data-ttu-id="5032e-123">[Atribut RequestTimeout](#attributes-of-the-aspnetcore-element) se nevztahuje na hostování v rámci procesu.</span><span class="sxs-lookup"><span data-stu-id="5032e-123">The [requestTimeout attribute](#attributes-of-the-aspnetcore-element) doesn't apply to in-process hosting.</span></span>

* <span data-ttu-id="5032e-124">Sdílení fondu aplikací mezi aplikacemi není podporováno.</span><span class="sxs-lookup"><span data-stu-id="5032e-124">Sharing an app pool among apps isn't supported.</span></span> <span data-ttu-id="5032e-125">Použijte jeden fond aplikací na aplikaci.</span><span class="sxs-lookup"><span data-stu-id="5032e-125">Use one app pool per app.</span></span>

* <span data-ttu-id="5032e-126">Při použití [nasazení webu](/iis/publish/using-web-deploy/introduction-to-web-deploy) nebo ručnímu umístění [souboru App_offline. htm v nasazení](xref:host-and-deploy/iis/index#locked-deployment-files)se aplikace nemusí okamžitě vypnout, pokud dojde k otevřenému připojení.</span><span class="sxs-lookup"><span data-stu-id="5032e-126">When using [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) or manually placing an [app_offline.htm file in the deployment](xref:host-and-deploy/iis/index#locked-deployment-files), the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="5032e-127">Připojení k protokolu WebSocket může například zpozdit ukončení aplikace.</span><span class="sxs-lookup"><span data-stu-id="5032e-127">For example, a websocket connection may delay app shut down.</span></span>

* <span data-ttu-id="5032e-128">Architektura (bitová verze) aplikace a nainstalovaného modulu runtime (x64 nebo x86) se musí shodovat s architekturou fondu aplikací.</span><span class="sxs-lookup"><span data-stu-id="5032e-128">The architecture (bitness) of the app and installed runtime (x64 or x86) must match the architecture of the app pool.</span></span>

* <span data-ttu-id="5032e-129">Zjistila se odpojení klienta.</span><span class="sxs-lookup"><span data-stu-id="5032e-129">Client disconnects are detected.</span></span> <span data-ttu-id="5032e-130">Token zrušení [HttpContext. RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) je zrušený, když se klient odpojí.</span><span class="sxs-lookup"><span data-stu-id="5032e-130">The [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) cancellation token is cancelled when the client disconnects.</span></span>

* <span data-ttu-id="5032e-131">V ASP.NET Core 2.2.1 nebo starším <xref:System.IO.Directory.GetCurrentDirectory*> vrátí pracovní adresář procesu spuštěného službou IIS místo adresáře aplikace (například *C:\Windows\System32\inetsrv* pro *W3wp. exe*).</span><span class="sxs-lookup"><span data-stu-id="5032e-131">In ASP.NET Core 2.2.1 or earlier, <xref:System.IO.Directory.GetCurrentDirectory*> returns the worker directory of the process started by IIS rather than the app's directory (for example, *C:\Windows\System32\inetsrv* for *w3wp.exe*).</span></span>

  <span data-ttu-id="5032e-132">Vzorový kód, který nastaví aktuální adresář aplikace, najdete v tématu [Třída CurrentDirectoryHelpers](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/3.x/CurrentDirectoryHelpers.cs).</span><span class="sxs-lookup"><span data-stu-id="5032e-132">For sample code that sets the app's current directory, see the [CurrentDirectoryHelpers class](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/3.x/CurrentDirectoryHelpers.cs).</span></span> <span data-ttu-id="5032e-133">Zavolejte metodu `SetCurrentDirectory`.</span><span class="sxs-lookup"><span data-stu-id="5032e-133">Call the `SetCurrentDirectory` method.</span></span> <span data-ttu-id="5032e-134">Následná volání <xref:System.IO.Directory.GetCurrentDirectory*> poskytují adresář aplikace.</span><span class="sxs-lookup"><span data-stu-id="5032e-134">Subsequent calls to <xref:System.IO.Directory.GetCurrentDirectory*> provide the app's directory.</span></span>

* <span data-ttu-id="5032e-135">Při hostování v procesu se nevolá <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> interně pro inicializaci uživatele.</span><span class="sxs-lookup"><span data-stu-id="5032e-135">When hosting in-process, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span> <span data-ttu-id="5032e-136">Proto implementace <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> používaná k transformaci deklarací po každém ověření není ve výchozím nastavení aktivována.</span><span class="sxs-lookup"><span data-stu-id="5032e-136">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="5032e-137">Při transformaci deklarací s implementací <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> zavolejte <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> pro přidání ověřovacích služeb:</span><span class="sxs-lookup"><span data-stu-id="5032e-137">When transforming claims with an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation, call <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> to add authentication services:</span></span>

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
  
  * <span data-ttu-id="5032e-138">[Nasazení webového balíčku (v jednom souboru)](/aspnet/web-forms/overview/deployment/web-deployment-in-the-enterprise/deploying-web-packages) nejsou podporována.</span><span class="sxs-lookup"><span data-stu-id="5032e-138">[Web Package (single-file) deployments](/aspnet/web-forms/overview/deployment/web-deployment-in-the-enterprise/deploying-web-packages) aren't supported.</span></span>

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="5032e-139">Model hostování mimo proces</span><span class="sxs-lookup"><span data-stu-id="5032e-139">Out-of-process hosting model</span></span>

<span data-ttu-id="5032e-140">Chcete-li nakonfigurovat aplikaci pro hostování mimo procesy, nastavte hodnotu vlastnosti `<AspNetCoreHostingModel>` na `OutOfProcess` v souboru projektu ( *. csproj*):</span><span class="sxs-lookup"><span data-stu-id="5032e-140">To configure an app for out-of-process hosting, set the value of the `<AspNetCoreHostingModel>` property to `OutOfProcess` in the project file (*.csproj*):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="5032e-141">Hostování v rámci procesu je nastaveno pomocí `InProcess`, což je výchozí hodnota.</span><span class="sxs-lookup"><span data-stu-id="5032e-141">In-process hosting is set with `InProcess`, which is the default value.</span></span>

<span data-ttu-id="5032e-142">Hodnota `<AspNetCoreHostingModel>` rozlišuje malá a velká písmena, takže `inprocess` a `outofprocess` jsou platné hodnoty.</span><span class="sxs-lookup"><span data-stu-id="5032e-142">The value of `<AspNetCoreHostingModel>` is case insensitive, so `inprocess` and `outofprocess` are valid values.</span></span>

<span data-ttu-id="5032e-143">Místo HTTP serveru IIS (`IISHttpServer`) se používá server [Kestrel](xref:fundamentals/servers/kestrel) .</span><span class="sxs-lookup"><span data-stu-id="5032e-143">[Kestrel](xref:fundamentals/servers/kestrel) server is used instead of IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="5032e-144">Pro mimoprocesové aplikace [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) volá <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> do:</span><span class="sxs-lookup"><span data-stu-id="5032e-144">For out-of-process, [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> to:</span></span>

* <span data-ttu-id="5032e-145">Nakonfigurujte port a základní cestu, na kterých má Server naslouchat při spuštění za modulem ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5032e-145">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
* <span data-ttu-id="5032e-146">Nakonfigurujte hostitele tak, aby zachytával chyby při spuštění.</span><span class="sxs-lookup"><span data-stu-id="5032e-146">Configure the host to capture startup errors.</span></span>

### <a name="hosting-model-changes"></a><span data-ttu-id="5032e-147">Změny modelu hostování</span><span class="sxs-lookup"><span data-stu-id="5032e-147">Hosting model changes</span></span>

<span data-ttu-id="5032e-148">Pokud je nastavení `hostingModel` změněno v souboru *Web. config* (vysvětleno v části [Konfigurace pomocí Web. config](#configuration-with-webconfig) ), modul recykluje pracovní proces pro službu IIS.</span><span class="sxs-lookup"><span data-stu-id="5032e-148">If the `hostingModel` setting is changed in the *web.config* file (explained in the [Configuration with web.config](#configuration-with-webconfig) section), the module recycles the worker process for IIS.</span></span>

<span data-ttu-id="5032e-149">V případě IIS Express modul neprovádí recyklaci pracovního procesu, ale místo toho aktivuje bezproblémové vypnutí aktuálního procesu IIS Express.</span><span class="sxs-lookup"><span data-stu-id="5032e-149">For IIS Express, the module doesn't recycle the worker process but instead triggers a graceful shutdown of the current IIS Express process.</span></span> <span data-ttu-id="5032e-150">Další požadavek na aplikaci vytvoří nový proces IIS Express.</span><span class="sxs-lookup"><span data-stu-id="5032e-150">The next request to the app spawns a new IIS Express process.</span></span>

### <a name="process-name"></a><span data-ttu-id="5032e-151">Název procesu</span><span class="sxs-lookup"><span data-stu-id="5032e-151">Process name</span></span>

<span data-ttu-id="5032e-152">sestavy `Process.GetCurrentProcess().ProcessName` `w3wp`/`iisexpress` (v procesu) nebo `dotnet` (mimo proces).</span><span class="sxs-lookup"><span data-stu-id="5032e-152">`Process.GetCurrentProcess().ProcessName` reports `w3wp`/`iisexpress` (in-process) or `dotnet` (out-of-process).</span></span>

<span data-ttu-id="5032e-153">Mnoho nativních modulů, jako je ověřování systému Windows, zůstane aktivní.</span><span class="sxs-lookup"><span data-stu-id="5032e-153">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="5032e-154">Další informace o aktivních modulech služby IIS pomocí modulu ASP.NET Core najdete v tématu <xref:host-and-deploy/iis/modules>.</span><span class="sxs-lookup"><span data-stu-id="5032e-154">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="5032e-155">Modul ASP.NET Core může také:</span><span class="sxs-lookup"><span data-stu-id="5032e-155">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="5032e-156">Nastavte proměnné prostředí pro pracovní proces.</span><span class="sxs-lookup"><span data-stu-id="5032e-156">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="5032e-157">Protokoluje výstup stdout do úložiště souborů pro řešení problémů při spuštění.</span><span class="sxs-lookup"><span data-stu-id="5032e-157">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="5032e-158">Předejte tokeny ověřování systému Windows.</span><span class="sxs-lookup"><span data-stu-id="5032e-158">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="5032e-159">Jak nainstalovat a používat modul ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5032e-159">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="5032e-160">Pokyny k instalaci modulu ASP.NET Core najdete v tématu [instalace hostující sady .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span><span class="sxs-lookup"><span data-stu-id="5032e-160">For instructions on how to install the ASP.NET Core Module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="5032e-161">Konfigurace pomocí souboru Web. config</span><span class="sxs-lookup"><span data-stu-id="5032e-161">Configuration with web.config</span></span>

<span data-ttu-id="5032e-162">ASP.NET Core modul je nakonfigurovaný pomocí oddílu `aspNetCore` v uzlu `system.webServer` v souboru *Web. config* tohoto webu.</span><span class="sxs-lookup"><span data-stu-id="5032e-162">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="5032e-163">Následující soubor *Web. config* je publikován pro [nasazení závislé na rozhraní](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) a nakonfiguruje modul ASP.NET Core pro zpracování požadavků lokality:</span><span class="sxs-lookup"><span data-stu-id="5032e-163">The following *web.config* file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

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

<span data-ttu-id="5032e-164">Následující soubor *Web. config* je publikován pro [samostatně uzavřené nasazení](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span><span class="sxs-lookup"><span data-stu-id="5032e-164">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

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

<span data-ttu-id="5032e-165">Vlastnost <xref:System.Configuration.SectionInformation.InheritInChildApplications*> je nastavena na hodnotu `false` k označení toho, že nastavení zadaná v rámci [> prvku \<location](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) nejsou děděna aplikacemi, které se nacházejí v podadresáři aplikace.</span><span class="sxs-lookup"><span data-stu-id="5032e-165">The <xref:System.Configuration.SectionInformation.InheritInChildApplications*> property is set to `false` to indicate that the settings specified within the [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) element aren't inherited by apps that reside in a subdirectory of the app.</span></span>

<span data-ttu-id="5032e-166">Když se aplikace nasadí do [Azure App Service](https://azure.microsoft.com/services/app-service/), `stdoutLogFile`á cesta je nastavená na `\\?\%home%\LogFiles\stdout`.</span><span class="sxs-lookup"><span data-stu-id="5032e-166">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="5032e-167">Cesta ukládá protokoly stdout do složky *soubory protokolů* , což je umístění, které služba automaticky vytvořila.</span><span class="sxs-lookup"><span data-stu-id="5032e-167">The path saves stdout logs to the *LogFiles* folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="5032e-168">Informace o konfiguraci dílčí aplikace IIS najdete v tématu <xref:host-and-deploy/iis/index#sub-applications>.</span><span class="sxs-lookup"><span data-stu-id="5032e-168">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="5032e-169">Atributy elementu aspNetCore</span><span class="sxs-lookup"><span data-stu-id="5032e-169">Attributes of the aspNetCore element</span></span>

| <span data-ttu-id="5032e-170">Atribut</span><span class="sxs-lookup"><span data-stu-id="5032e-170">Attribute</span></span> | <span data-ttu-id="5032e-171">Popis</span><span class="sxs-lookup"><span data-stu-id="5032e-171">Description</span></span> | <span data-ttu-id="5032e-172">Výchozí</span><span class="sxs-lookup"><span data-stu-id="5032e-172">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="5032e-173">Volitelný řetězcový atribut.</span><span class="sxs-lookup"><span data-stu-id="5032e-173">Optional string attribute.</span></span></p><p><span data-ttu-id="5032e-174">Argumenty ke spustitelnému souboru určenému v **processPath**.</span><span class="sxs-lookup"><span data-stu-id="5032e-174">Arguments to the executable specified in **processPath**.</span></span></p> | |
| `disableStartUpErrorPage` | <p><span data-ttu-id="5032e-175">Volitelný atribut typu Boolean.</span><span class="sxs-lookup"><span data-stu-id="5032e-175">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="5032e-176">Pokud je nastaveno na true, stránka **502,5 – selhání procesu** se potlačí a stavová stránka 502 konfigurovaná v *souboru Web. config* má přednost.</span><span class="sxs-lookup"><span data-stu-id="5032e-176">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="5032e-177">Volitelný atribut typu Boolean.</span><span class="sxs-lookup"><span data-stu-id="5032e-177">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="5032e-178">Pokud je nastaveno na true, token se přepošle podřízenému procesu, který naslouchá na% ASPNETCORE_PORT% jako hlavička MS-ASPNETCORE-WINAUTHTOKEN na vyžádání.</span><span class="sxs-lookup"><span data-stu-id="5032e-178">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="5032e-179">Je zodpovědností za tento proces volání CloseHandle na tento token na žádost.</span><span class="sxs-lookup"><span data-stu-id="5032e-179">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `hostingModel` | <p><span data-ttu-id="5032e-180">Volitelný řetězcový atribut.</span><span class="sxs-lookup"><span data-stu-id="5032e-180">Optional string attribute.</span></span></p><p><span data-ttu-id="5032e-181">Určuje model hostování jako vnitroprocesové (`InProcess`/`inprocess`) nebo mimo proces (`OutOfProcess`/`outofprocess`).</span><span class="sxs-lookup"><span data-stu-id="5032e-181">Specifies the hosting model as in-process (`InProcess`/`inprocess`) or out-of-process (`OutOfProcess`/`outofprocess`).</span></span></p> | `InProcess`<br>`inprocess` |
| `processesPerApplication` | <p><span data-ttu-id="5032e-182">Volitelný celočíselný atribut</span><span class="sxs-lookup"><span data-stu-id="5032e-182">Optional integer attribute.</span></span></p><p><span data-ttu-id="5032e-183">Určuje počet instancí procesu určeného v nastavení **processPath** , které lze v rámci aplikace vystavit.</span><span class="sxs-lookup"><span data-stu-id="5032e-183">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="5032e-184">&dagger;pro hostování v rámci procesu je hodnota omezená na `1`.</span><span class="sxs-lookup"><span data-stu-id="5032e-184">&dagger;For in-process hosting, the value is limited to `1`.</span></span></p><p><span data-ttu-id="5032e-185">Nastavení `processesPerApplication` se nedoporučuje.</span><span class="sxs-lookup"><span data-stu-id="5032e-185">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="5032e-186">Tento atribut bude v budoucí verzi odebrán.</span><span class="sxs-lookup"><span data-stu-id="5032e-186">This attribute will be removed in a future release.</span></span></p> | <span data-ttu-id="5032e-187">Výchozí: `1`</span><span class="sxs-lookup"><span data-stu-id="5032e-187">Default: `1`</span></span><br><span data-ttu-id="5032e-188">Minimum: `1`</span><span class="sxs-lookup"><span data-stu-id="5032e-188">Min: `1`</span></span><br><span data-ttu-id="5032e-189">Max: `100`&dagger;</span><span class="sxs-lookup"><span data-stu-id="5032e-189">Max: `100`&dagger;</span></span> |
| `processPath` | <p><span data-ttu-id="5032e-190">Povinný atribut řetězce.</span><span class="sxs-lookup"><span data-stu-id="5032e-190">Required string attribute.</span></span></p><p><span data-ttu-id="5032e-191">Cesta ke spustitelnému souboru, který spouští proces naslouchající na požadavky HTTP.</span><span class="sxs-lookup"><span data-stu-id="5032e-191">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="5032e-192">Jsou podporovány relativní cesty.</span><span class="sxs-lookup"><span data-stu-id="5032e-192">Relative paths are supported.</span></span> <span data-ttu-id="5032e-193">Pokud cesta začíná na `.`, bude cesta považována za relativní vzhledem k kořenu webu.</span><span class="sxs-lookup"><span data-stu-id="5032e-193">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="5032e-194">Volitelný celočíselný atribut</span><span class="sxs-lookup"><span data-stu-id="5032e-194">Optional integer attribute.</span></span></p><p><span data-ttu-id="5032e-195">Určuje, kolikrát je povoleno selhání procesu určeného v **processPath** za minutu.</span><span class="sxs-lookup"><span data-stu-id="5032e-195">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="5032e-196">Pokud je tento limit překročen, modul ukončí spuštění procesu po zbytek minuty.</span><span class="sxs-lookup"><span data-stu-id="5032e-196">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p><p><span data-ttu-id="5032e-197">Nepodporováno v hostování v rámci procesu.</span><span class="sxs-lookup"><span data-stu-id="5032e-197">Not supported with in-process hosting.</span></span></p> | <span data-ttu-id="5032e-198">Výchozí: `10`</span><span class="sxs-lookup"><span data-stu-id="5032e-198">Default: `10`</span></span><br><span data-ttu-id="5032e-199">Minimum: `0`</span><span class="sxs-lookup"><span data-stu-id="5032e-199">Min: `0`</span></span><br><span data-ttu-id="5032e-200">Max: `100`</span><span class="sxs-lookup"><span data-stu-id="5032e-200">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="5032e-201">Volitelný atribut TimeSpan.</span><span class="sxs-lookup"><span data-stu-id="5032e-201">Optional timespan attribute.</span></span></p><p><span data-ttu-id="5032e-202">Určuje dobu, po kterou modul ASP.NET Core čeká na odezvu od procesu, na kterém naslouchá% ASPNETCORE_PORT%.</span><span class="sxs-lookup"><span data-stu-id="5032e-202">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="5032e-203">Ve verzích modulu ASP.NET Core, které byly dodávány s vydáním ASP.NET Core 2,1 nebo vyšší, je `requestTimeout` zadáno v hodinách, minutách a sekundách.</span><span class="sxs-lookup"><span data-stu-id="5032e-203">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p><p><span data-ttu-id="5032e-204">Neplatí pro hostování v procesu.</span><span class="sxs-lookup"><span data-stu-id="5032e-204">Doesn't apply to in-process hosting.</span></span> <span data-ttu-id="5032e-205">Pro hostování v rámci procesu modul čeká, až aplikace zpracuje požadavek.</span><span class="sxs-lookup"><span data-stu-id="5032e-205">For in-process hosting, the module waits for the app to process the request.</span></span></p><p><span data-ttu-id="5032e-206">Platné hodnoty segmentů minut a sekund řetězce jsou v rozsahu 0-59.</span><span class="sxs-lookup"><span data-stu-id="5032e-206">Valid values for minutes and seconds segments of the string are in the range 0-59.</span></span> <span data-ttu-id="5032e-207">Při použití hodnoty **60** v hodnotě minut nebo sekund dojde k chybě systému *500 – interní chyba serveru*.</span><span class="sxs-lookup"><span data-stu-id="5032e-207">Use of **60** in the value for minutes or seconds results in a *500 - Internal Server Error*.</span></span></p> | <span data-ttu-id="5032e-208">Výchozí: `00:02:00`</span><span class="sxs-lookup"><span data-stu-id="5032e-208">Default: `00:02:00`</span></span><br><span data-ttu-id="5032e-209">Minimum: `00:00:00`</span><span class="sxs-lookup"><span data-stu-id="5032e-209">Min: `00:00:00`</span></span><br><span data-ttu-id="5032e-210">Max: `360:00:00`</span><span class="sxs-lookup"><span data-stu-id="5032e-210">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="5032e-211">Volitelný celočíselný atribut</span><span class="sxs-lookup"><span data-stu-id="5032e-211">Optional integer attribute.</span></span></p><p><span data-ttu-id="5032e-212">Doba v sekundách, po kterou modul čeká na řádné vypnutí spustitelného souboru, když se zjistí soubor *App_offline. htm* .</span><span class="sxs-lookup"><span data-stu-id="5032e-212">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="5032e-213">Výchozí: `10`</span><span class="sxs-lookup"><span data-stu-id="5032e-213">Default: `10`</span></span><br><span data-ttu-id="5032e-214">Minimum: `0`</span><span class="sxs-lookup"><span data-stu-id="5032e-214">Min: `0`</span></span><br><span data-ttu-id="5032e-215">Max: `600`</span><span class="sxs-lookup"><span data-stu-id="5032e-215">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="5032e-216">Volitelný celočíselný atribut</span><span class="sxs-lookup"><span data-stu-id="5032e-216">Optional integer attribute.</span></span></p><p><span data-ttu-id="5032e-217">Doba v sekundách, kterou modul počká, než spustitelný soubor spustí proces, který na portu naslouchá.</span><span class="sxs-lookup"><span data-stu-id="5032e-217">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="5032e-218">Pokud je tento časový limit překročen, modul proces ukončuje.</span><span class="sxs-lookup"><span data-stu-id="5032e-218">If this time limit is exceeded, the module kills the process.</span></span> <span data-ttu-id="5032e-219">Modul se pokusí znovu spustit proces, když obdrží novou žádost, a pokračuje v pokusu o restartování procesu u dalších příchozích požadavků, pokud se nepodaří spustit **rapidFailsPerMinute** počet pokusů během poslední minuty.</span><span class="sxs-lookup"><span data-stu-id="5032e-219">The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="5032e-220">Hodnota 0 (nula **) není považována za** nekonečný časový limit.</span><span class="sxs-lookup"><span data-stu-id="5032e-220">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="5032e-221">Výchozí: `120`</span><span class="sxs-lookup"><span data-stu-id="5032e-221">Default: `120`</span></span><br><span data-ttu-id="5032e-222">Minimum: `0`</span><span class="sxs-lookup"><span data-stu-id="5032e-222">Min: `0`</span></span><br><span data-ttu-id="5032e-223">Max: `3600`</span><span class="sxs-lookup"><span data-stu-id="5032e-223">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="5032e-224">Volitelný atribut typu Boolean.</span><span class="sxs-lookup"><span data-stu-id="5032e-224">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="5032e-225">Je-li nastavena hodnota true, **stdout** a **stderr** pro proces zadaný v **processPath** budou přesměrovány do souboru zadaného v **stdoutLogFile**.</span><span class="sxs-lookup"><span data-stu-id="5032e-225">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="5032e-226">Volitelný řetězcový atribut.</span><span class="sxs-lookup"><span data-stu-id="5032e-226">Optional string attribute.</span></span></p><p><span data-ttu-id="5032e-227">Určuje relativní nebo absolutní cestu k souboru, pro který je **stdout** a **stderr** z procesu určeného v **processPath** protokolováno.</span><span class="sxs-lookup"><span data-stu-id="5032e-227">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="5032e-228">Relativní cesty jsou relativní vzhledem k kořenu webu.</span><span class="sxs-lookup"><span data-stu-id="5032e-228">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="5032e-229">Všechny cesty začínající na `.` jsou relativní vzhledem k kořenu webu a všechny ostatní cesty se považují za absolutní cesty.</span><span class="sxs-lookup"><span data-stu-id="5032e-229">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="5032e-230">Při vytvoření souboru protokolu se v modulu vytvoří všechny složky, které jsou v cestě zadané.</span><span class="sxs-lookup"><span data-stu-id="5032e-230">Any folders provided in the path are created by the module when the log file is created.</span></span> <span data-ttu-id="5032e-231">Pomocí oddělovačů podtržítka se do posledního segmentu cesty **stdoutLogFile** přidá časové razítko, ID procesu a Přípona souboru ( *. log*).</span><span class="sxs-lookup"><span data-stu-id="5032e-231">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="5032e-232">Pokud je jako hodnota zadána `.\logs\stdout`, je ukázkový protokol stdout uložen jako *stdout_20180205194132_1934. log* ve složce *logs* při uložení na 2/5/2018 v 19:41:32 s ID procesu 1934.</span><span class="sxs-lookup"><span data-stu-id="5032e-232">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

### <a name="set-environment-variables"></a><span data-ttu-id="5032e-233">Nastavení proměnných prostředí</span><span class="sxs-lookup"><span data-stu-id="5032e-233">Set environment variables</span></span>

<span data-ttu-id="5032e-234">Proměnné prostředí lze zadat pro proces v atributu `processPath`.</span><span class="sxs-lookup"><span data-stu-id="5032e-234">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="5032e-235">Zadejte proměnnou prostředí s podřízeným prvkem `<environmentVariable>` elementu kolekce `<environmentVariables>`.</span><span class="sxs-lookup"><span data-stu-id="5032e-235">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span> <span data-ttu-id="5032e-236">Proměnné prostředí nastavené v této části mají přednost před proměnnými prostředí systému.</span><span class="sxs-lookup"><span data-stu-id="5032e-236">Environment variables set in this section take precedence over system environment variables.</span></span>

<span data-ttu-id="5032e-237">Následující příklad nastaví dvě proměnné prostředí v *souboru Web. config*. `ASPNETCORE_ENVIRONMENT` nakonfiguruje prostředí aplikace tak, aby bylo `Development`.</span><span class="sxs-lookup"><span data-stu-id="5032e-237">The following example sets two environment variables in *web.config*. `ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="5032e-238">Vývojář může tuto hodnotu dočasně nastavit v souboru *Web. config* , aby bylo možné vynutit, aby se [Stránka s výjimkou vývojářů](xref:fundamentals/error-handling) načetla při ladění výjimky aplikace.</span><span class="sxs-lookup"><span data-stu-id="5032e-238">A developer may temporarily set this value in the *web.config* file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="5032e-239">`CONFIG_DIR` je příklad uživatelsky definované proměnné prostředí, kde vývojář napsal kód, který přečte hodnotu při spuštění, aby vytvořil cestu pro načtení konfiguračního souboru aplikace.</span><span class="sxs-lookup"><span data-stu-id="5032e-239">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

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
> <span data-ttu-id="5032e-240">Alternativou k nastavení prostředí přímo v souboru *Web. config* je zahrnutí vlastnosti `<EnvironmentName>` do [profilu publikování (. pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) nebo souboru projektu.</span><span class="sxs-lookup"><span data-stu-id="5032e-240">An alternative to setting the environment directly in *web.config* is to include the `<EnvironmentName>` property in the [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) or project file.</span></span> <span data-ttu-id="5032e-241">Tento přístup nastaví prostředí v *souboru Web. config* při publikování projektu:</span><span class="sxs-lookup"><span data-stu-id="5032e-241">This approach sets the environment in *web.config* when the project is published:</span></span>
>
> ```xml
> <PropertyGroup>
>   <EnvironmentName>Development</EnvironmentName>
> </PropertyGroup>
> ```

> [!WARNING]
> <span data-ttu-id="5032e-242">Nastavte pouze proměnnou prostředí `ASPNETCORE_ENVIRONMENT`, aby `Development` na pracovních a testovacích serverech, které nejsou přístupné nedůvěryhodným sítím, jako je například Internet.</span><span class="sxs-lookup"><span data-stu-id="5032e-242">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## <a name="app_offlinehtm"></a><span data-ttu-id="5032e-243">App_offline. htm</span><span class="sxs-lookup"><span data-stu-id="5032e-243">app_offline.htm</span></span>

<span data-ttu-id="5032e-244">Pokud se v kořenovém adresáři aplikace zjistí soubor s názvem *App_offline. htm* , modul ASP.NET Core se pokusí aplikaci řádně vypnout a zastaví zpracování příchozích požadavků.</span><span class="sxs-lookup"><span data-stu-id="5032e-244">If a file with the name *app_offline.htm* is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="5032e-245">Pokud aplikace běží i po uplynutí počtu sekund definovaného v `shutdownTimeLimit`, modul ASP.NET Core ukončuje běžící proces.</span><span class="sxs-lookup"><span data-stu-id="5032e-245">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="5032e-246">Když je přítomen soubor *App_offline. htm* , ASP.NET Core modul reaguje na požadavky odesláním zpět obsahu souboru *App_offline. htm* .</span><span class="sxs-lookup"><span data-stu-id="5032e-246">While the *app_offline.htm* file is present, the ASP.NET Core Module responds to requests by sending back the contents of the *app_offline.htm* file.</span></span> <span data-ttu-id="5032e-247">Po odebrání souboru *App_offline. htm* aplikace spustí další požadavek.</span><span class="sxs-lookup"><span data-stu-id="5032e-247">When the *app_offline.htm* file is removed, the next request starts the app.</span></span>

<span data-ttu-id="5032e-248">Při použití nezpracovaného modelu hostování se aplikace nemusí okamžitě vypnout, pokud dojde k otevřenému připojení.</span><span class="sxs-lookup"><span data-stu-id="5032e-248">When using the out-of-process hosting model, the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="5032e-249">Připojení k protokolu WebSocket může například zpozdit ukončení aplikace.</span><span class="sxs-lookup"><span data-stu-id="5032e-249">For example, a websocket connection may delay app shut down.</span></span>

## <a name="start-up-error-page"></a><span data-ttu-id="5032e-250">Spouštěcí chybová stránka</span><span class="sxs-lookup"><span data-stu-id="5032e-250">Start-up error page</span></span>

<span data-ttu-id="5032e-251">Hostování v procesu i mimo procesy vytváří vlastní chybové stránky, když se nepodaří aplikaci spustit.</span><span class="sxs-lookup"><span data-stu-id="5032e-251">Both in-process and out-of-process hosting produce custom error pages when they fail to start the app.</span></span>

<span data-ttu-id="5032e-252">Pokud modul ASP.NET Core nenalezne obslužnou rutinu žádosti v procesu nebo mimo proces 500,0, zobrazí se stránka s kódem stavu *selhání načtení obslužné rutiny v* procesu nebo mimo proces.</span><span class="sxs-lookup"><span data-stu-id="5032e-252">If the ASP.NET Core Module fails to find either the in-process or out-of-process request handler, a *500.0 - In-Process/Out-Of-Process Handler Load Failure* status code page appears.</span></span>

<span data-ttu-id="5032e-253">Pro vnitroprocesové hostování v případě, že se modul ASP.NET Core nepodaří spustit aplikaci, zobrazí se stránka s kódem stavu *chyby při spuštění 500,30* .</span><span class="sxs-lookup"><span data-stu-id="5032e-253">For in-process hosting if the ASP.NET Core Module fails to start the app, a *500.30 - Start Failure* status code page appears.</span></span>

<span data-ttu-id="5032e-254">Pro hostování mimo procesy, pokud se ASP.NET Core modulu nespustí back-end proces, nebo dojde k selhání procesu back-endu, ale nenaslouchá na konfigurovaném portu, zobrazí se stránka s kódem stavu *selhání procesu 502,5* .</span><span class="sxs-lookup"><span data-stu-id="5032e-254">For out-of-process hosting if the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span>

<span data-ttu-id="5032e-255">Chcete-li tuto stránku potlačit a vrátit se na výchozí stavovou stránku služby IIS 5xx, použijte atribut `disableStartUpErrorPage`.</span><span class="sxs-lookup"><span data-stu-id="5032e-255">To suppress this page and revert to the default IIS 5xx status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="5032e-256">Další informace o konfiguraci vlastních chybových zpráv najdete v tématu [chyby protokolu HTTP \<httpErrors >](/iis/configuration/system.webServer/httpErrors/).</span><span class="sxs-lookup"><span data-stu-id="5032e-256">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

## <a name="log-creation-and-redirection"></a><span data-ttu-id="5032e-257">Vytvoření a přesměrování protokolu</span><span class="sxs-lookup"><span data-stu-id="5032e-257">Log creation and redirection</span></span>

<span data-ttu-id="5032e-258">Modul ASP.NET Core přesměruje výstup konzoly stdout a stderr na disk, pokud jsou nastaveny atributy `stdoutLogEnabled` a `stdoutLogFile` prvku `aspNetCore`.</span><span class="sxs-lookup"><span data-stu-id="5032e-258">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="5032e-259">Při vytvoření souboru protokolu jsou všechny složky v cestě `stdoutLogFile` vytvářeny modulem.</span><span class="sxs-lookup"><span data-stu-id="5032e-259">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="5032e-260">Fond aplikací musí mít oprávnění k zápisu do umístění, kam se zapisují protokoly (k poskytnutí oprávnění k zápisu použijte `IIS AppPool\<app_pool_name>`).</span><span class="sxs-lookup"><span data-stu-id="5032e-260">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="5032e-261">Protokoly nejsou otočeny, pokud nedochází k recyklování/restartování procesu.</span><span class="sxs-lookup"><span data-stu-id="5032e-261">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="5032e-262">Je zodpovědností hostitele omezit místo na disku, které používají protokoly.</span><span class="sxs-lookup"><span data-stu-id="5032e-262">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="5032e-263">Použití protokolu stdout se doporučuje pouze při řešení potíží se spouštěním aplikací při hostování služby IIS nebo při použití [podpory v době vývoje služby IIS se sadou Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), nikoli při ladění místně a při spuštění aplikace s IIS Express.</span><span class="sxs-lookup"><span data-stu-id="5032e-263">Using the stdout log is only recommended for troubleshooting app startup issues when hosting on IIS or when using [development-time support for IIS with Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), not while debugging locally and running the app with IIS Express.</span></span>

<span data-ttu-id="5032e-264">Nepoužívejte protokol stdout pro obecné účely protokolování aplikace.</span><span class="sxs-lookup"><span data-stu-id="5032e-264">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="5032e-265">Pro rutinu protokolování v aplikaci ASP.NET Core použijte knihovnu protokolování, která omezuje velikost souboru protokolu a otočí protokoly.</span><span class="sxs-lookup"><span data-stu-id="5032e-265">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="5032e-266">Další informace najdete v tématu [Zprostředkovatelé protokolování třetích stran](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="5032e-266">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="5032e-267">Časové razítko a Přípona souboru se automaticky přidají při vytvoření souboru protokolu.</span><span class="sxs-lookup"><span data-stu-id="5032e-267">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="5032e-268">Název souboru protokolu se skládá připojením časového razítka, ID procesu a přípony souboru ( *. log*) k poslednímu segmentu cesty `stdoutLogFile` (obvykle *stdout*), oddělený podtržítky.</span><span class="sxs-lookup"><span data-stu-id="5032e-268">The log file name is composed by appending the timestamp, process ID, and file extension (*.log*) to the last segment of the `stdoutLogFile` path (typically *stdout*) delimited by underscores.</span></span> <span data-ttu-id="5032e-269">Pokud `stdoutLogFile` cesta končí na *stdout*, protokol pro aplikaci s číslem PID 1934 vytvořeným v 2/5/2018 v 19:42:32 má název souboru *stdout_20180205194132_1934. log*.</span><span class="sxs-lookup"><span data-stu-id="5032e-269">If the `stdoutLogFile` path ends with *stdout*, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name *stdout_20180205194132_1934.log*.</span></span>

<span data-ttu-id="5032e-270">Pokud je hodnota `stdoutLogEnabled` false, budou chyby, ke kterým dojde při spuštění aplikace, zachyceny a generovány do protokolu událostí až do 30 KB.</span><span class="sxs-lookup"><span data-stu-id="5032e-270">If `stdoutLogEnabled` is false, errors that occur on app startup are captured and emitted to the event log up to 30 KB.</span></span> <span data-ttu-id="5032e-271">Po spuštění budou všechny další protokoly zahozeny.</span><span class="sxs-lookup"><span data-stu-id="5032e-271">After startup, all additional logs are discarded.</span></span>

<span data-ttu-id="5032e-272">Následující vzorový `aspNetCore` prvek nakonfiguruje protokolování stdout na relativní cestě `.\log\`.</span><span class="sxs-lookup"><span data-stu-id="5032e-272">The following sample `aspNetCore` element configures stdout logging at the relative path `.\log\`.</span></span> <span data-ttu-id="5032e-273">Potvrďte, že identita uživatele fondu aplikací má oprávnění k zápisu do zadané cesty.</span><span class="sxs-lookup"><span data-stu-id="5032e-273">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout"
    hostingModel="inprocess">
</aspNetCore>
```

<span data-ttu-id="5032e-274">Když publikujete aplikaci pro nasazení Azure App Service, Webová sada SDK nastaví hodnotu `stdoutLogFile` na `\\?\%home%\LogFiles\stdout`.</span><span class="sxs-lookup"><span data-stu-id="5032e-274">When publishing an app for Azure App Service deployment, the Web SDK sets the `stdoutLogFile` value to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="5032e-275">Pro aplikace hostované v Azure App Service je předdefinovaná proměnná prostředí `%home`.</span><span class="sxs-lookup"><span data-stu-id="5032e-275">The `%home` environment variable is predefined for apps hosted by Azure App Service.</span></span>

<span data-ttu-id="5032e-276">Chcete-li vytvořit pravidla filtru protokolování, přečtěte si část [Konfigurace](xref:fundamentals/logging/index#log-filtering) a [filtrování protokolů](xref:fundamentals/logging/index#log-filtering) v dokumentaci ASP.NET Core protokolování.</span><span class="sxs-lookup"><span data-stu-id="5032e-276">To create logging filter rules, see the [Configuration](xref:fundamentals/logging/index#log-filtering) and [Log filtering](xref:fundamentals/logging/index#log-filtering) sections of the ASP.NET Core logging documentation.</span></span>

<span data-ttu-id="5032e-277">Další informace o formátech cest najdete v tématu [formáty souborů cest v systémech Windows](/dotnet/standard/io/file-path-formats).</span><span class="sxs-lookup"><span data-stu-id="5032e-277">For more information on path formats, see [File path formats on Windows systems](/dotnet/standard/io/file-path-formats).</span></span>

## <a name="enhanced-diagnostic-logs"></a><span data-ttu-id="5032e-278">Rozšířené diagnostické protokoly</span><span class="sxs-lookup"><span data-stu-id="5032e-278">Enhanced diagnostic logs</span></span>

<span data-ttu-id="5032e-279">Modul ASP.NET Core lze nakonfigurovat tak, aby poskytoval rozšířené diagnostické protokoly.</span><span class="sxs-lookup"><span data-stu-id="5032e-279">The ASP.NET Core Module is configurable to provide enhanced diagnostics logs.</span></span> <span data-ttu-id="5032e-280">Přidejte `<handlerSettings>` elementu do prvku `<aspNetCore>` v *souboru Web. config*. Nastavení `debugLevel` na `TRACE` zpřístupňuje vyšší věrnost diagnostických informací:</span><span class="sxs-lookup"><span data-stu-id="5032e-280">Add the `<handlerSettings>` element to the `<aspNetCore>` element in *web.config*. Setting the `debugLevel` to `TRACE` exposes a higher fidelity of diagnostic information:</span></span>

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

<span data-ttu-id="5032e-281">Všechny složky v cestě (*protokoly* v předchozím příkladu) se vytvoří modulem, když se vytvoří soubor protokolu.</span><span class="sxs-lookup"><span data-stu-id="5032e-281">Any folders in the path (*logs* in the preceding example) are created by the module when the log file is created.</span></span> <span data-ttu-id="5032e-282">Fond aplikací musí mít oprávnění k zápisu do umístění, kam se zapisují protokoly (k poskytnutí oprávnění k zápisu použijte `IIS AppPool\<app_pool_name>`).</span><span class="sxs-lookup"><span data-stu-id="5032e-282">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="5032e-283">Hodnoty úrovně ladění (`debugLevel`) mohou zahrnovat jak úroveň, tak i umístění.</span><span class="sxs-lookup"><span data-stu-id="5032e-283">Debug level (`debugLevel`) values can include both the level and the location.</span></span>

<span data-ttu-id="5032e-284">Úrovně (v pořadí od nejméně po nejpodrobnější):</span><span class="sxs-lookup"><span data-stu-id="5032e-284">Levels (in order from least to most verbose):</span></span>

* <span data-ttu-id="5032e-285">Chyba</span><span class="sxs-lookup"><span data-stu-id="5032e-285">ERROR</span></span>
* <span data-ttu-id="5032e-286">Upozornění</span><span class="sxs-lookup"><span data-stu-id="5032e-286">WARNING</span></span>
* <span data-ttu-id="5032e-287">PŘÍJEMCE</span><span class="sxs-lookup"><span data-stu-id="5032e-287">INFO</span></span>
* <span data-ttu-id="5032e-288">TRACE</span><span class="sxs-lookup"><span data-stu-id="5032e-288">TRACE</span></span>

<span data-ttu-id="5032e-289">Umístění (je povoleno více umístění):</span><span class="sxs-lookup"><span data-stu-id="5032e-289">Locations (multiple locations are permitted):</span></span>

* <span data-ttu-id="5032e-290">STROMU</span><span class="sxs-lookup"><span data-stu-id="5032e-290">CONSOLE</span></span>
* <span data-ttu-id="5032e-291">EVENTLOG</span><span class="sxs-lookup"><span data-stu-id="5032e-291">EVENTLOG</span></span>
* <span data-ttu-id="5032e-292">SOUBORŮ</span><span class="sxs-lookup"><span data-stu-id="5032e-292">FILE</span></span>

<span data-ttu-id="5032e-293">Nastavení obslužné rutiny lze také poskytnout pomocí proměnných prostředí:</span><span class="sxs-lookup"><span data-stu-id="5032e-293">The handler settings can also be provided via environment variables:</span></span>

* <span data-ttu-id="5032e-294">`ASPNETCORE_MODULE_DEBUG_FILE` &ndash; cestu k souboru protokolu ladění.</span><span class="sxs-lookup"><span data-stu-id="5032e-294">`ASPNETCORE_MODULE_DEBUG_FILE` &ndash; Path to the debug log file.</span></span> <span data-ttu-id="5032e-295">(Výchozí: *aspnetcore-Debug. log*)</span><span class="sxs-lookup"><span data-stu-id="5032e-295">(Default: *aspnetcore-debug.log*)</span></span>
* <span data-ttu-id="5032e-296">`ASPNETCORE_MODULE_DEBUG` &ndash; nastavení úrovně ladění.</span><span class="sxs-lookup"><span data-stu-id="5032e-296">`ASPNETCORE_MODULE_DEBUG` &ndash; Debug level setting.</span></span>

> [!WARNING]
> <span data-ttu-id="5032e-297">Nenechávejte **protokolování** ladění povolené v nasazení po delší dobu, než je potřeba k řešení problému.</span><span class="sxs-lookup"><span data-stu-id="5032e-297">Do **not** leave debug logging enabled in the deployment for longer than required to troubleshoot an issue.</span></span> <span data-ttu-id="5032e-298">Velikost protokolu není omezená.</span><span class="sxs-lookup"><span data-stu-id="5032e-298">The size of the log isn't limited.</span></span> <span data-ttu-id="5032e-299">Když necháte protokol ladění povolený, může dojít k vyčerpání dostupného místa na disku a selhání serveru nebo služby App Service.</span><span class="sxs-lookup"><span data-stu-id="5032e-299">Leaving the debug log enabled can exhaust the available disk space and crash the server or app service.</span></span>

<span data-ttu-id="5032e-300">Příklad prvku `aspNetCore` v souboru *Web. config* naleznete v tématu [Konfigurace pomocí souboru Web. config](#configuration-with-webconfig) .</span><span class="sxs-lookup"><span data-stu-id="5032e-300">See [Configuration with web.config](#configuration-with-webconfig) for an example of the `aspNetCore` element in the *web.config* file.</span></span>

## <a name="modify-the-stack-size"></a><span data-ttu-id="5032e-301">Úprava velikosti zásobníku</span><span class="sxs-lookup"><span data-stu-id="5032e-301">Modify the stack size</span></span>

<span data-ttu-id="5032e-302">*Platí pouze při použití modelu hostování v rámci procesu.*</span><span class="sxs-lookup"><span data-stu-id="5032e-302">*Only applies when using the in-process hosting model.*</span></span>

<span data-ttu-id="5032e-303">Nakonfigurujte velikost spravovaného zásobníku pomocí nastavení `stackSize` v bajtech v *souboru Web. config*. Výchozí velikost je `1048576` bajty (1 MB).</span><span class="sxs-lookup"><span data-stu-id="5032e-303">Configure the managed stack size using the `stackSize` setting in bytes in *web.config*. The default size is `1048576` bytes (1 MB).</span></span>

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

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="5032e-304">Konfigurace proxy serveru používá protokol HTTP a token párování.</span><span class="sxs-lookup"><span data-stu-id="5032e-304">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="5032e-305">*Platí pouze pro hostování mimo proces.*</span><span class="sxs-lookup"><span data-stu-id="5032e-305">*Only applies to out-of-process hosting.*</span></span>

<span data-ttu-id="5032e-306">Proxy vytvořený mezi modulem ASP.NET Core a Kestrel používá protokol HTTP.</span><span class="sxs-lookup"><span data-stu-id="5032e-306">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="5032e-307">Nehrozí riziko odposlouchávání provozu mezi modulem a Kestrel z umístění od serveru.</span><span class="sxs-lookup"><span data-stu-id="5032e-307">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="5032e-308">Párovací token se používá k zajištění, že žádosti přijaté službou Kestrel byly proxy službou IIS a nepocházejí z jiného zdroje.</span><span class="sxs-lookup"><span data-stu-id="5032e-308">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="5032e-309">Token párování se vytvoří a nastaví na proměnnou prostředí (`ASPNETCORE_TOKEN`) modulem.</span><span class="sxs-lookup"><span data-stu-id="5032e-309">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="5032e-310">Párování tokenu je také nastaveno na hlavičku (`MS-ASPNETCORE-TOKEN`) na každém proxy požadavku.</span><span class="sxs-lookup"><span data-stu-id="5032e-310">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="5032e-311">Middleware služby IIS kontroluje každý požadavek, který obdrží, a potvrdí, že hodnota hlavičky párového tokenu odpovídá hodnotě proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="5032e-311">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="5032e-312">Pokud se hodnoty tokenu neshodují, požadavek se zaprotokoluje a odmítne.</span><span class="sxs-lookup"><span data-stu-id="5032e-312">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="5032e-313">Proměnná prostředí párového tokenu a přenos dat mezi modulem a Kestrel nejsou přístupné z umístění mimo server.</span><span class="sxs-lookup"><span data-stu-id="5032e-313">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="5032e-314">Bez znalosti hodnoty párovacího tokenu nemůže útočník odeslat žádosti, které obcházejí kontrolu v Middlewari IIS.</span><span class="sxs-lookup"><span data-stu-id="5032e-314">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="5032e-315">ASP.NET Core modul se sdílenou konfigurací služby IIS</span><span class="sxs-lookup"><span data-stu-id="5032e-315">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="5032e-316">Instalační služba modulu ASP.NET Core se spouští s oprávněními účtu **TrustedInstaller** .</span><span class="sxs-lookup"><span data-stu-id="5032e-316">The ASP.NET Core Module installer runs with the privileges of the **TrustedInstaller** account.</span></span> <span data-ttu-id="5032e-317">Vzhledem k tomu, že účet místního systému nemá oprávnění k úpravám pro cestu ke sdílené složce, kterou používá sdílená konfigurace služby IIS, vyvolá Instalační program chybu odepření přístupu při pokusu o konfiguraci nastavení modulu v souboru *ApplicationHost. config* v sdílená složka.</span><span class="sxs-lookup"><span data-stu-id="5032e-317">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the *applicationHost.config* file on the share.</span></span>

<span data-ttu-id="5032e-318">Pokud používáte sdílenou konfiguraci IIS na stejném počítači jako instalaci služby IIS, spusťte instalační program sady prostředků ASP.NET Core s parametrem `OPT_NO_SHARED_CONFIG_CHECK` nastaveným na `1`:</span><span class="sxs-lookup"><span data-stu-id="5032e-318">When using an IIS Shared Configuration on the same machine as the IIS installation, run the ASP.NET Core Hosting Bundle installer with the `OPT_NO_SHARED_CONFIG_CHECK` parameter set to `1`:</span></span>

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

<span data-ttu-id="5032e-319">Pokud cesta ke sdílené konfiguraci není ve stejném počítači jako instalace služby IIS, postupujte podle těchto kroků:</span><span class="sxs-lookup"><span data-stu-id="5032e-319">When the path to the shared configuration isn't on the same machine as the IIS installation, follow these steps:</span></span>

1. <span data-ttu-id="5032e-320">Zakažte sdílenou konfiguraci služby IIS.</span><span class="sxs-lookup"><span data-stu-id="5032e-320">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="5032e-321">Spusťte instalační program.</span><span class="sxs-lookup"><span data-stu-id="5032e-321">Run the installer.</span></span>
1. <span data-ttu-id="5032e-322">Exportujte aktualizovaný soubor *ApplicationHost. config* do sdílené složky.</span><span class="sxs-lookup"><span data-stu-id="5032e-322">Export the updated *applicationHost.config* file to the share.</span></span>
1. <span data-ttu-id="5032e-323">Znovu povolte sdílenou konfiguraci služby IIS.</span><span class="sxs-lookup"><span data-stu-id="5032e-323">Re-enable the IIS Shared Configuration.</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="5032e-324">Verze modulu a instalační protokoly hostitelské sady</span><span class="sxs-lookup"><span data-stu-id="5032e-324">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="5032e-325">Postup určení verze nainstalovaného modulu ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="5032e-325">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="5032e-326">V hostitelském systému přejděte na *%windir%\system32\inetsrv*.</span><span class="sxs-lookup"><span data-stu-id="5032e-326">On the hosting system, navigate to *%windir%\System32\inetsrv*.</span></span>
1. <span data-ttu-id="5032e-327">Vyhledejte soubor *aspnetcore. dll* .</span><span class="sxs-lookup"><span data-stu-id="5032e-327">Locate the *aspnetcore.dll* file.</span></span>
1. <span data-ttu-id="5032e-328">Klikněte na soubor pravým tlačítkem a v místní nabídce vyberte **vlastnosti** .</span><span class="sxs-lookup"><span data-stu-id="5032e-328">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="5032e-329">Vyberte kartu **Podrobnosti** . **Verze souboru** a **verze produktu** reprezentují nainstalovanou verzi modulu.</span><span class="sxs-lookup"><span data-stu-id="5032e-329">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="5032e-330">Protokoly instalačních balíčků pro modul jsou k dispozici v *C:\\uživatelé\\% username%\\data\\místní\\Temp*. Soubor má název *dd_DotNetCoreWinSvrHosting__\<časové razítko > _000_AspNetCoreModule_x64. log*.</span><span class="sxs-lookup"><span data-stu-id="5032e-330">The Hosting Bundle installer logs for the module are found at *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. The file is named *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="5032e-331">Umístění souboru modulu, schématu a konfiguračního souboru</span><span class="sxs-lookup"><span data-stu-id="5032e-331">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="5032e-332">Modul</span><span class="sxs-lookup"><span data-stu-id="5032e-332">Module</span></span>

<span data-ttu-id="5032e-333">**Služba IIS (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="5032e-333">**IIS (x86/amd64):**</span></span>

* <span data-ttu-id="5032e-334">%windir%\System32\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="5032e-334">%windir%\System32\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="5032e-335">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="5032e-335">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="5032e-336">%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="5032e-336">%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="5032e-337">% ProgramFiles (x86)% \ IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="5032e-337">%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

<span data-ttu-id="5032e-338">**IIS Express (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="5032e-338">**IIS Express (x86/amd64):**</span></span>

* <span data-ttu-id="5032e-339">%ProgramFiles%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="5032e-339">%ProgramFiles%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="5032e-340">% ProgramFiles (x86)% \ IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="5032e-340">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="5032e-341">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="5032e-341">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="5032e-342">% ProgramFiles (x86)% \ IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="5032e-342">%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

### <a name="schema"></a><span data-ttu-id="5032e-343">Schéma</span><span class="sxs-lookup"><span data-stu-id="5032e-343">Schema</span></span>

<span data-ttu-id="5032e-344">**SLUŽBU**</span><span class="sxs-lookup"><span data-stu-id="5032e-344">**IIS**</span></span>

* <span data-ttu-id="5032e-345">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="5032e-345">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span></span>

* <span data-ttu-id="5032e-346">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span><span class="sxs-lookup"><span data-stu-id="5032e-346">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span></span>

<span data-ttu-id="5032e-347">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="5032e-347">**IIS Express**</span></span>

* <span data-ttu-id="5032e-348">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="5032e-348">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span></span>

* <span data-ttu-id="5032e-349">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span><span class="sxs-lookup"><span data-stu-id="5032e-349">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span></span>

### <a name="configuration"></a><span data-ttu-id="5032e-350">Konfigurace</span><span class="sxs-lookup"><span data-stu-id="5032e-350">Configuration</span></span>

<span data-ttu-id="5032e-351">**SLUŽBU**</span><span class="sxs-lookup"><span data-stu-id="5032e-351">**IIS**</span></span>

* <span data-ttu-id="5032e-352">%windir%\System32\inetsrv\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="5032e-352">%windir%\System32\inetsrv\config\applicationHost.config</span></span>

<span data-ttu-id="5032e-353">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="5032e-353">**IIS Express**</span></span>

* <span data-ttu-id="5032e-354">Visual Studio: {ROOT aplikace} \\. vs\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="5032e-354">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span></span>

* <span data-ttu-id="5032e-355">*IISExpress. exe* CLI:%USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span><span class="sxs-lookup"><span data-stu-id="5032e-355">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span></span>

<span data-ttu-id="5032e-356">Soubory lze najít hledáním *aspnetcore* v souboru *ApplicationHost. config* .</span><span class="sxs-lookup"><span data-stu-id="5032e-356">The files can be found by searching for *aspnetcore* in the *applicationHost.config* file.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="5032e-357">Modul ASP.NET Core je nativním modulem služby IIS, který se připojuje k kanálu IIS na jednu z těchto:</span><span class="sxs-lookup"><span data-stu-id="5032e-357">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to either:</span></span>

* <span data-ttu-id="5032e-358">Hostování aplikace ASP.NET Core uvnitř pracovního procesu služby IIS (`w3wp.exe`) se nazývá [model hostování v rámci procesu](#in-process-hosting-model).</span><span class="sxs-lookup"><span data-stu-id="5032e-358">Host an ASP.NET Core app inside of the IIS worker process (`w3wp.exe`), called the [in-process hosting model](#in-process-hosting-model).</span></span>
* <span data-ttu-id="5032e-359">Dopředné webové požadavky do back-endu ASP.NET Core aplikace, na které běží [Server Kestrel](xref:fundamentals/servers/kestrel), se nazývá [model hostování mimo proces](#out-of-process-hosting-model).</span><span class="sxs-lookup"><span data-stu-id="5032e-359">Forward web requests to a backend ASP.NET Core app running the [Kestrel server](xref:fundamentals/servers/kestrel), called the [out-of-process hosting model](#out-of-process-hosting-model).</span></span>

<span data-ttu-id="5032e-360">Podporované verze systému Windows:</span><span class="sxs-lookup"><span data-stu-id="5032e-360">Supported Windows versions:</span></span>

* <span data-ttu-id="5032e-361">Windows 7 nebo novější</span><span class="sxs-lookup"><span data-stu-id="5032e-361">Windows 7 or later</span></span>
* <span data-ttu-id="5032e-362">Windows Server 2008 R2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="5032e-362">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="5032e-363">Při hostování v procesu používá modul implementaci vnitroprocesového serveru pro službu IIS, která se nazývá IIS HTTP Server (`IISHttpServer`).</span><span class="sxs-lookup"><span data-stu-id="5032e-363">When hosting in-process, the module uses an in-process server implementation for IIS, called IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="5032e-364">Při hostování mimo proces funguje modul pouze s Kestrel.</span><span class="sxs-lookup"><span data-stu-id="5032e-364">When hosting out-of-process, the module only works with Kestrel.</span></span> <span data-ttu-id="5032e-365">Modul nefunguje s [ovladačem HTTP. sys](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="5032e-365">The module doesn't function with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

## <a name="hosting-models"></a><span data-ttu-id="5032e-366">Modely hostingu</span><span class="sxs-lookup"><span data-stu-id="5032e-366">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="5032e-367">Model hostování v procesu</span><span class="sxs-lookup"><span data-stu-id="5032e-367">In-process hosting model</span></span>

<span data-ttu-id="5032e-368">Chcete-li nakonfigurovat aplikaci pro hostování v rámci procesu, přidejte do souboru projektu aplikace vlastnost `<AspNetCoreHostingModel>` s hodnotou `InProcess` (hostování mimo procesy je nastavena s `OutOfProcess`):</span><span class="sxs-lookup"><span data-stu-id="5032e-368">To configure an app for in-process hosting, add the `<AspNetCoreHostingModel>` property to the app's project file with a value of `InProcess` (out-of-process hosting is set with `OutOfProcess`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>InProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="5032e-369">Model hostování v rámci procesu není podporován pro aplikace ASP.NET Core, které cílí na .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="5032e-369">The in-process hosting model isn't supported for ASP.NET Core apps that target the .NET Framework.</span></span>

<span data-ttu-id="5032e-370">Hodnota `<AspNetCoreHostingModel>` rozlišuje malá a velká písmena, takže `inprocess` a `outofprocess` jsou platné hodnoty.</span><span class="sxs-lookup"><span data-stu-id="5032e-370">The value of `<AspNetCoreHostingModel>` is case insensitive, so `inprocess` and `outofprocess` are valid values.</span></span>

<span data-ttu-id="5032e-371">Pokud se v souboru nenachází vlastnost `<AspNetCoreHostingModel>`, výchozí hodnota je `OutOfProcess`.</span><span class="sxs-lookup"><span data-stu-id="5032e-371">If the `<AspNetCoreHostingModel>` property isn't present in the file, the default value is `OutOfProcess`.</span></span>

<span data-ttu-id="5032e-372">Následující vlastnosti platí při hostování v procesu:</span><span class="sxs-lookup"><span data-stu-id="5032e-372">The following characteristics apply when hosting in-process:</span></span>

* <span data-ttu-id="5032e-373">Místo [Kestrel](xref:fundamentals/servers/kestrel) serveru se používá server HTTP služby IIS (`IISHttpServer`).</span><span class="sxs-lookup"><span data-stu-id="5032e-373">IIS HTTP Server (`IISHttpServer`) is used instead of [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span> <span data-ttu-id="5032e-374">Pro vnitroprocesové [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) volání <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> do:</span><span class="sxs-lookup"><span data-stu-id="5032e-374">For in-process, [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> to:</span></span>

  * <span data-ttu-id="5032e-375">Zaregistrujte `IISHttpServer`.</span><span class="sxs-lookup"><span data-stu-id="5032e-375">Register the `IISHttpServer`.</span></span>
  * <span data-ttu-id="5032e-376">Nakonfigurujte port a základní cestu, na kterých má Server naslouchat při spuštění za modulem ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5032e-376">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
  * <span data-ttu-id="5032e-377">Nakonfigurujte hostitele tak, aby zachytával chyby při spuštění.</span><span class="sxs-lookup"><span data-stu-id="5032e-377">Configure the host to capture startup errors.</span></span>

* <span data-ttu-id="5032e-378">[Atribut RequestTimeout](#attributes-of-the-aspnetcore-element) se nevztahuje na hostování v rámci procesu.</span><span class="sxs-lookup"><span data-stu-id="5032e-378">The [requestTimeout attribute](#attributes-of-the-aspnetcore-element) doesn't apply to in-process hosting.</span></span>

* <span data-ttu-id="5032e-379">Sdílení fondu aplikací mezi aplikacemi není podporováno.</span><span class="sxs-lookup"><span data-stu-id="5032e-379">Sharing an app pool among apps isn't supported.</span></span> <span data-ttu-id="5032e-380">Použijte jeden fond aplikací na aplikaci.</span><span class="sxs-lookup"><span data-stu-id="5032e-380">Use one app pool per app.</span></span>

* <span data-ttu-id="5032e-381">Při použití [nasazení webu](/iis/publish/using-web-deploy/introduction-to-web-deploy) nebo ručnímu umístění [souboru App_offline. htm v nasazení](xref:host-and-deploy/iis/index#locked-deployment-files)se aplikace nemusí okamžitě vypnout, pokud dojde k otevřenému připojení.</span><span class="sxs-lookup"><span data-stu-id="5032e-381">When using [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) or manually placing an [app_offline.htm file in the deployment](xref:host-and-deploy/iis/index#locked-deployment-files), the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="5032e-382">Připojení k protokolu WebSocket může například zpozdit ukončení aplikace.</span><span class="sxs-lookup"><span data-stu-id="5032e-382">For example, a websocket connection may delay app shut down.</span></span>

* <span data-ttu-id="5032e-383">Architektura (bitová verze) aplikace a nainstalovaného modulu runtime (x64 nebo x86) se musí shodovat s architekturou fondu aplikací.</span><span class="sxs-lookup"><span data-stu-id="5032e-383">The architecture (bitness) of the app and installed runtime (x64 or x86) must match the architecture of the app pool.</span></span>

* <span data-ttu-id="5032e-384">Zjistila se odpojení klienta.</span><span class="sxs-lookup"><span data-stu-id="5032e-384">Client disconnects are detected.</span></span> <span data-ttu-id="5032e-385">Token zrušení [HttpContext. RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) je zrušený, když se klient odpojí.</span><span class="sxs-lookup"><span data-stu-id="5032e-385">The [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) cancellation token is cancelled when the client disconnects.</span></span>

* <span data-ttu-id="5032e-386">V ASP.NET Core 2.2.1 nebo starším <xref:System.IO.Directory.GetCurrentDirectory*> vrátí pracovní adresář procesu spuštěného službou IIS místo adresáře aplikace (například *C:\Windows\System32\inetsrv* pro *W3wp. exe*).</span><span class="sxs-lookup"><span data-stu-id="5032e-386">In ASP.NET Core 2.2.1 or earlier, <xref:System.IO.Directory.GetCurrentDirectory*> returns the worker directory of the process started by IIS rather than the app's directory (for example, *C:\Windows\System32\inetsrv* for *w3wp.exe*).</span></span>

  <span data-ttu-id="5032e-387">Vzorový kód, který nastaví aktuální adresář aplikace, najdete v tématu [Třída CurrentDirectoryHelpers](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/2.x/CurrentDirectoryHelpers.cs).</span><span class="sxs-lookup"><span data-stu-id="5032e-387">For sample code that sets the app's current directory, see the [CurrentDirectoryHelpers class](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/2.x/CurrentDirectoryHelpers.cs).</span></span> <span data-ttu-id="5032e-388">Zavolejte metodu `SetCurrentDirectory`.</span><span class="sxs-lookup"><span data-stu-id="5032e-388">Call the `SetCurrentDirectory` method.</span></span> <span data-ttu-id="5032e-389">Následná volání <xref:System.IO.Directory.GetCurrentDirectory*> poskytují adresář aplikace.</span><span class="sxs-lookup"><span data-stu-id="5032e-389">Subsequent calls to <xref:System.IO.Directory.GetCurrentDirectory*> provide the app's directory.</span></span>

* <span data-ttu-id="5032e-390">Při hostování v procesu se nevolá <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> interně pro inicializaci uživatele.</span><span class="sxs-lookup"><span data-stu-id="5032e-390">When hosting in-process, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span> <span data-ttu-id="5032e-391">Proto implementace <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> používaná k transformaci deklarací po každém ověření není ve výchozím nastavení aktivována.</span><span class="sxs-lookup"><span data-stu-id="5032e-391">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="5032e-392">Při transformaci deklarací s implementací <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> zavolejte <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> pro přidání ověřovacích služeb:</span><span class="sxs-lookup"><span data-stu-id="5032e-392">When transforming claims with an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation, call <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> to add authentication services:</span></span>

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

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="5032e-393">Model hostování mimo proces</span><span class="sxs-lookup"><span data-stu-id="5032e-393">Out-of-process hosting model</span></span>

<span data-ttu-id="5032e-394">Chcete-li nakonfigurovat aplikaci pro hostování mimo procesy, použijte některý z následujících přístupů v souboru projektu:</span><span class="sxs-lookup"><span data-stu-id="5032e-394">To configure an app for out-of-process hosting, use either of the following approaches in the project file:</span></span>

* <span data-ttu-id="5032e-395">Nezadávejte vlastnost `<AspNetCoreHostingModel>`.</span><span class="sxs-lookup"><span data-stu-id="5032e-395">Don't specify the `<AspNetCoreHostingModel>` property.</span></span> <span data-ttu-id="5032e-396">Pokud se v souboru nenachází vlastnost `<AspNetCoreHostingModel>`, výchozí hodnota je `OutOfProcess`.</span><span class="sxs-lookup"><span data-stu-id="5032e-396">If the `<AspNetCoreHostingModel>` property isn't present in the file, the default value is `OutOfProcess`.</span></span>
* <span data-ttu-id="5032e-397">Nastavte hodnotu vlastnosti `<AspNetCoreHostingModel>` na `OutOfProcess` (hostování v procesu je nastaveno pomocí `InProcess`):</span><span class="sxs-lookup"><span data-stu-id="5032e-397">Set the value of the `<AspNetCoreHostingModel>` property to `OutOfProcess` (in-process hosting is set with `InProcess`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="5032e-398">Hodnota rozlišuje malá a velká písmena, takže `inprocess` a `outofprocess` platné hodnoty.</span><span class="sxs-lookup"><span data-stu-id="5032e-398">The value is case insensitive, so `inprocess` and `outofprocess` are valid values.</span></span>

<span data-ttu-id="5032e-399">Místo HTTP serveru IIS (`IISHttpServer`) se používá server [Kestrel](xref:fundamentals/servers/kestrel) .</span><span class="sxs-lookup"><span data-stu-id="5032e-399">[Kestrel](xref:fundamentals/servers/kestrel) server is used instead of IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="5032e-400">Pro mimoprocesové aplikace [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) volá <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> do:</span><span class="sxs-lookup"><span data-stu-id="5032e-400">For out-of-process, [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> to:</span></span>

* <span data-ttu-id="5032e-401">Nakonfigurujte port a základní cestu, na kterých má Server naslouchat při spuštění za modulem ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5032e-401">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
* <span data-ttu-id="5032e-402">Nakonfigurujte hostitele tak, aby zachytával chyby při spuštění.</span><span class="sxs-lookup"><span data-stu-id="5032e-402">Configure the host to capture startup errors.</span></span>

### <a name="hosting-model-changes"></a><span data-ttu-id="5032e-403">Změny modelu hostování</span><span class="sxs-lookup"><span data-stu-id="5032e-403">Hosting model changes</span></span>

<span data-ttu-id="5032e-404">Pokud je nastavení `hostingModel` změněno v souboru *Web. config* (vysvětleno v části [Konfigurace pomocí Web. config](#configuration-with-webconfig) ), modul recykluje pracovní proces pro službu IIS.</span><span class="sxs-lookup"><span data-stu-id="5032e-404">If the `hostingModel` setting is changed in the *web.config* file (explained in the [Configuration with web.config](#configuration-with-webconfig) section), the module recycles the worker process for IIS.</span></span>

<span data-ttu-id="5032e-405">V případě IIS Express modul neprovádí recyklaci pracovního procesu, ale místo toho aktivuje bezproblémové vypnutí aktuálního procesu IIS Express.</span><span class="sxs-lookup"><span data-stu-id="5032e-405">For IIS Express, the module doesn't recycle the worker process but instead triggers a graceful shutdown of the current IIS Express process.</span></span> <span data-ttu-id="5032e-406">Další požadavek na aplikaci vytvoří nový proces IIS Express.</span><span class="sxs-lookup"><span data-stu-id="5032e-406">The next request to the app spawns a new IIS Express process.</span></span>

### <a name="process-name"></a><span data-ttu-id="5032e-407">Název procesu</span><span class="sxs-lookup"><span data-stu-id="5032e-407">Process name</span></span>

<span data-ttu-id="5032e-408">sestavy `Process.GetCurrentProcess().ProcessName` `w3wp`/`iisexpress` (v procesu) nebo `dotnet` (mimo proces).</span><span class="sxs-lookup"><span data-stu-id="5032e-408">`Process.GetCurrentProcess().ProcessName` reports `w3wp`/`iisexpress` (in-process) or `dotnet` (out-of-process).</span></span>

<span data-ttu-id="5032e-409">Mnoho nativních modulů, jako je ověřování systému Windows, zůstane aktivní.</span><span class="sxs-lookup"><span data-stu-id="5032e-409">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="5032e-410">Další informace o aktivních modulech služby IIS pomocí modulu ASP.NET Core najdete v tématu <xref:host-and-deploy/iis/modules>.</span><span class="sxs-lookup"><span data-stu-id="5032e-410">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="5032e-411">Modul ASP.NET Core může také:</span><span class="sxs-lookup"><span data-stu-id="5032e-411">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="5032e-412">Nastavte proměnné prostředí pro pracovní proces.</span><span class="sxs-lookup"><span data-stu-id="5032e-412">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="5032e-413">Protokoluje výstup stdout do úložiště souborů pro řešení problémů při spuštění.</span><span class="sxs-lookup"><span data-stu-id="5032e-413">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="5032e-414">Předejte tokeny ověřování systému Windows.</span><span class="sxs-lookup"><span data-stu-id="5032e-414">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="5032e-415">Jak nainstalovat a používat modul ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5032e-415">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="5032e-416">Pokyny k instalaci modulu ASP.NET Core najdete v tématu [instalace hostující sady .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span><span class="sxs-lookup"><span data-stu-id="5032e-416">For instructions on how to install the ASP.NET Core Module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="5032e-417">Konfigurace pomocí souboru Web. config</span><span class="sxs-lookup"><span data-stu-id="5032e-417">Configuration with web.config</span></span>

<span data-ttu-id="5032e-418">ASP.NET Core modul je nakonfigurovaný pomocí oddílu `aspNetCore` v uzlu `system.webServer` v souboru *Web. config* tohoto webu.</span><span class="sxs-lookup"><span data-stu-id="5032e-418">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="5032e-419">Následující soubor *Web. config* je publikován pro [nasazení závislé na rozhraní](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) a nakonfiguruje modul ASP.NET Core pro zpracování požadavků lokality:</span><span class="sxs-lookup"><span data-stu-id="5032e-419">The following *web.config* file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

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

<span data-ttu-id="5032e-420">Následující soubor *Web. config* je publikován pro [samostatně uzavřené nasazení](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span><span class="sxs-lookup"><span data-stu-id="5032e-420">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

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

<span data-ttu-id="5032e-421">Vlastnost <xref:System.Configuration.SectionInformation.InheritInChildApplications*> je nastavena na hodnotu `false` k označení toho, že nastavení zadaná v rámci [> prvku \<location](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) nejsou děděna aplikacemi, které se nacházejí v podadresáři aplikace.</span><span class="sxs-lookup"><span data-stu-id="5032e-421">The <xref:System.Configuration.SectionInformation.InheritInChildApplications*> property is set to `false` to indicate that the settings specified within the [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) element aren't inherited by apps that reside in a subdirectory of the app.</span></span>

<span data-ttu-id="5032e-422">Když se aplikace nasadí do [Azure App Service](https://azure.microsoft.com/services/app-service/), `stdoutLogFile`á cesta je nastavená na `\\?\%home%\LogFiles\stdout`.</span><span class="sxs-lookup"><span data-stu-id="5032e-422">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="5032e-423">Cesta ukládá protokoly stdout do složky *soubory protokolů* , což je umístění, které služba automaticky vytvořila.</span><span class="sxs-lookup"><span data-stu-id="5032e-423">The path saves stdout logs to the *LogFiles* folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="5032e-424">Informace o konfiguraci dílčí aplikace IIS najdete v tématu <xref:host-and-deploy/iis/index#sub-applications>.</span><span class="sxs-lookup"><span data-stu-id="5032e-424">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="5032e-425">Atributy elementu aspNetCore</span><span class="sxs-lookup"><span data-stu-id="5032e-425">Attributes of the aspNetCore element</span></span>

| <span data-ttu-id="5032e-426">Atribut</span><span class="sxs-lookup"><span data-stu-id="5032e-426">Attribute</span></span> | <span data-ttu-id="5032e-427">Popis</span><span class="sxs-lookup"><span data-stu-id="5032e-427">Description</span></span> | <span data-ttu-id="5032e-428">Výchozí</span><span class="sxs-lookup"><span data-stu-id="5032e-428">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="5032e-429">Volitelný řetězcový atribut.</span><span class="sxs-lookup"><span data-stu-id="5032e-429">Optional string attribute.</span></span></p><p><span data-ttu-id="5032e-430">Argumenty ke spustitelnému souboru určenému v **processPath**.</span><span class="sxs-lookup"><span data-stu-id="5032e-430">Arguments to the executable specified in **processPath**.</span></span></p> | |
| `disableStartUpErrorPage` | <p><span data-ttu-id="5032e-431">Volitelný atribut typu Boolean.</span><span class="sxs-lookup"><span data-stu-id="5032e-431">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="5032e-432">Pokud je nastaveno na true, stránka **502,5 – selhání procesu** se potlačí a stavová stránka 502 konfigurovaná v *souboru Web. config* má přednost.</span><span class="sxs-lookup"><span data-stu-id="5032e-432">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="5032e-433">Volitelný atribut typu Boolean.</span><span class="sxs-lookup"><span data-stu-id="5032e-433">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="5032e-434">Pokud je nastaveno na true, token se přepošle podřízenému procesu, který naslouchá na% ASPNETCORE_PORT% jako hlavička MS-ASPNETCORE-WINAUTHTOKEN na vyžádání.</span><span class="sxs-lookup"><span data-stu-id="5032e-434">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="5032e-435">Je zodpovědností za tento proces volání CloseHandle na tento token na žádost.</span><span class="sxs-lookup"><span data-stu-id="5032e-435">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `hostingModel` | <p><span data-ttu-id="5032e-436">Volitelný řetězcový atribut.</span><span class="sxs-lookup"><span data-stu-id="5032e-436">Optional string attribute.</span></span></p><p><span data-ttu-id="5032e-437">Určuje model hostování jako vnitroprocesové (`InProcess`/`inprocess`) nebo mimo proces (`OutOfProcess`/`outofprocess`).</span><span class="sxs-lookup"><span data-stu-id="5032e-437">Specifies the hosting model as in-process (`InProcess`/`inprocess`) or out-of-process (`OutOfProcess`/`outofprocess`).</span></span></p> | `OutOfProcess`<br>`outofprocess` |
| `processesPerApplication` | <p><span data-ttu-id="5032e-438">Volitelný celočíselný atribut</span><span class="sxs-lookup"><span data-stu-id="5032e-438">Optional integer attribute.</span></span></p><p><span data-ttu-id="5032e-439">Určuje počet instancí procesu určeného v nastavení **processPath** , které lze v rámci aplikace vystavit.</span><span class="sxs-lookup"><span data-stu-id="5032e-439">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="5032e-440">&dagger;pro hostování v rámci procesu je hodnota omezená na `1`.</span><span class="sxs-lookup"><span data-stu-id="5032e-440">&dagger;For in-process hosting, the value is limited to `1`.</span></span></p><p><span data-ttu-id="5032e-441">Nastavení `processesPerApplication` se nedoporučuje.</span><span class="sxs-lookup"><span data-stu-id="5032e-441">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="5032e-442">Tento atribut bude v budoucí verzi odebrán.</span><span class="sxs-lookup"><span data-stu-id="5032e-442">This attribute will be removed in a future release.</span></span></p> | <span data-ttu-id="5032e-443">Výchozí: `1`</span><span class="sxs-lookup"><span data-stu-id="5032e-443">Default: `1`</span></span><br><span data-ttu-id="5032e-444">Minimum: `1`</span><span class="sxs-lookup"><span data-stu-id="5032e-444">Min: `1`</span></span><br><span data-ttu-id="5032e-445">Max: `100`&dagger;</span><span class="sxs-lookup"><span data-stu-id="5032e-445">Max: `100`&dagger;</span></span> |
| `processPath` | <p><span data-ttu-id="5032e-446">Povinný atribut řetězce.</span><span class="sxs-lookup"><span data-stu-id="5032e-446">Required string attribute.</span></span></p><p><span data-ttu-id="5032e-447">Cesta ke spustitelnému souboru, který spouští proces naslouchající na požadavky HTTP.</span><span class="sxs-lookup"><span data-stu-id="5032e-447">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="5032e-448">Jsou podporovány relativní cesty.</span><span class="sxs-lookup"><span data-stu-id="5032e-448">Relative paths are supported.</span></span> <span data-ttu-id="5032e-449">Pokud cesta začíná na `.`, bude cesta považována za relativní vzhledem k kořenu webu.</span><span class="sxs-lookup"><span data-stu-id="5032e-449">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="5032e-450">Volitelný celočíselný atribut</span><span class="sxs-lookup"><span data-stu-id="5032e-450">Optional integer attribute.</span></span></p><p><span data-ttu-id="5032e-451">Určuje, kolikrát je povoleno selhání procesu určeného v **processPath** za minutu.</span><span class="sxs-lookup"><span data-stu-id="5032e-451">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="5032e-452">Pokud je tento limit překročen, modul ukončí spuštění procesu po zbytek minuty.</span><span class="sxs-lookup"><span data-stu-id="5032e-452">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p><p><span data-ttu-id="5032e-453">Nepodporováno v hostování v rámci procesu.</span><span class="sxs-lookup"><span data-stu-id="5032e-453">Not supported with in-process hosting.</span></span></p> | <span data-ttu-id="5032e-454">Výchozí: `10`</span><span class="sxs-lookup"><span data-stu-id="5032e-454">Default: `10`</span></span><br><span data-ttu-id="5032e-455">Minimum: `0`</span><span class="sxs-lookup"><span data-stu-id="5032e-455">Min: `0`</span></span><br><span data-ttu-id="5032e-456">Max: `100`</span><span class="sxs-lookup"><span data-stu-id="5032e-456">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="5032e-457">Volitelný atribut TimeSpan.</span><span class="sxs-lookup"><span data-stu-id="5032e-457">Optional timespan attribute.</span></span></p><p><span data-ttu-id="5032e-458">Určuje dobu, po kterou modul ASP.NET Core čeká na odezvu od procesu, na kterém naslouchá% ASPNETCORE_PORT%.</span><span class="sxs-lookup"><span data-stu-id="5032e-458">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="5032e-459">Ve verzích modulu ASP.NET Core, které byly dodávány s vydáním ASP.NET Core 2,1 nebo vyšší, je `requestTimeout` zadáno v hodinách, minutách a sekundách.</span><span class="sxs-lookup"><span data-stu-id="5032e-459">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p><p><span data-ttu-id="5032e-460">Neplatí pro hostování v procesu.</span><span class="sxs-lookup"><span data-stu-id="5032e-460">Doesn't apply to in-process hosting.</span></span> <span data-ttu-id="5032e-461">Pro hostování v rámci procesu modul čeká, až aplikace zpracuje požadavek.</span><span class="sxs-lookup"><span data-stu-id="5032e-461">For in-process hosting, the module waits for the app to process the request.</span></span></p><p><span data-ttu-id="5032e-462">Platné hodnoty segmentů minut a sekund řetězce jsou v rozsahu 0-59.</span><span class="sxs-lookup"><span data-stu-id="5032e-462">Valid values for minutes and seconds segments of the string are in the range 0-59.</span></span> <span data-ttu-id="5032e-463">Při použití hodnoty **60** v hodnotě minut nebo sekund dojde k chybě systému *500 – interní chyba serveru*.</span><span class="sxs-lookup"><span data-stu-id="5032e-463">Use of **60** in the value for minutes or seconds results in a *500 - Internal Server Error*.</span></span></p> | <span data-ttu-id="5032e-464">Výchozí: `00:02:00`</span><span class="sxs-lookup"><span data-stu-id="5032e-464">Default: `00:02:00`</span></span><br><span data-ttu-id="5032e-465">Minimum: `00:00:00`</span><span class="sxs-lookup"><span data-stu-id="5032e-465">Min: `00:00:00`</span></span><br><span data-ttu-id="5032e-466">Max: `360:00:00`</span><span class="sxs-lookup"><span data-stu-id="5032e-466">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="5032e-467">Volitelný celočíselný atribut</span><span class="sxs-lookup"><span data-stu-id="5032e-467">Optional integer attribute.</span></span></p><p><span data-ttu-id="5032e-468">Doba v sekundách, po kterou modul čeká na řádné vypnutí spustitelného souboru, když se zjistí soubor *App_offline. htm* .</span><span class="sxs-lookup"><span data-stu-id="5032e-468">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="5032e-469">Výchozí: `10`</span><span class="sxs-lookup"><span data-stu-id="5032e-469">Default: `10`</span></span><br><span data-ttu-id="5032e-470">Minimum: `0`</span><span class="sxs-lookup"><span data-stu-id="5032e-470">Min: `0`</span></span><br><span data-ttu-id="5032e-471">Max: `600`</span><span class="sxs-lookup"><span data-stu-id="5032e-471">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="5032e-472">Volitelný celočíselný atribut</span><span class="sxs-lookup"><span data-stu-id="5032e-472">Optional integer attribute.</span></span></p><p><span data-ttu-id="5032e-473">Doba v sekundách, kterou modul počká, než spustitelný soubor spustí proces, který na portu naslouchá.</span><span class="sxs-lookup"><span data-stu-id="5032e-473">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="5032e-474">Pokud je tento časový limit překročen, modul proces ukončuje.</span><span class="sxs-lookup"><span data-stu-id="5032e-474">If this time limit is exceeded, the module kills the process.</span></span> <span data-ttu-id="5032e-475">Modul se pokusí znovu spustit proces, když obdrží novou žádost, a pokračuje v pokusu o restartování procesu u dalších příchozích požadavků, pokud se nepodaří spustit **rapidFailsPerMinute** počet pokusů během poslední minuty.</span><span class="sxs-lookup"><span data-stu-id="5032e-475">The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="5032e-476">Hodnota 0 (nula **) není považována za** nekonečný časový limit.</span><span class="sxs-lookup"><span data-stu-id="5032e-476">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="5032e-477">Výchozí: `120`</span><span class="sxs-lookup"><span data-stu-id="5032e-477">Default: `120`</span></span><br><span data-ttu-id="5032e-478">Minimum: `0`</span><span class="sxs-lookup"><span data-stu-id="5032e-478">Min: `0`</span></span><br><span data-ttu-id="5032e-479">Max: `3600`</span><span class="sxs-lookup"><span data-stu-id="5032e-479">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="5032e-480">Volitelný atribut typu Boolean.</span><span class="sxs-lookup"><span data-stu-id="5032e-480">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="5032e-481">Je-li nastavena hodnota true, **stdout** a **stderr** pro proces zadaný v **processPath** budou přesměrovány do souboru zadaného v **stdoutLogFile**.</span><span class="sxs-lookup"><span data-stu-id="5032e-481">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="5032e-482">Volitelný řetězcový atribut.</span><span class="sxs-lookup"><span data-stu-id="5032e-482">Optional string attribute.</span></span></p><p><span data-ttu-id="5032e-483">Určuje relativní nebo absolutní cestu k souboru, pro který je **stdout** a **stderr** z procesu určeného v **processPath** protokolováno.</span><span class="sxs-lookup"><span data-stu-id="5032e-483">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="5032e-484">Relativní cesty jsou relativní vzhledem k kořenu webu.</span><span class="sxs-lookup"><span data-stu-id="5032e-484">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="5032e-485">Všechny cesty začínající na `.` jsou relativní vzhledem k kořenu webu a všechny ostatní cesty se považují za absolutní cesty.</span><span class="sxs-lookup"><span data-stu-id="5032e-485">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="5032e-486">Při vytvoření souboru protokolu se v modulu vytvoří všechny složky, které jsou v cestě zadané.</span><span class="sxs-lookup"><span data-stu-id="5032e-486">Any folders provided in the path are created by the module when the log file is created.</span></span> <span data-ttu-id="5032e-487">Pomocí oddělovačů podtržítka se do posledního segmentu cesty **stdoutLogFile** přidá časové razítko, ID procesu a Přípona souboru ( *. log*).</span><span class="sxs-lookup"><span data-stu-id="5032e-487">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="5032e-488">Pokud je jako hodnota zadána `.\logs\stdout`, je ukázkový protokol stdout uložen jako *stdout_20180205194132_1934. log* ve složce *logs* při uložení na 2/5/2018 v 19:41:32 s ID procesu 1934.</span><span class="sxs-lookup"><span data-stu-id="5032e-488">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

### <a name="setting-environment-variables"></a><span data-ttu-id="5032e-489">Nastavení proměnných prostředí</span><span class="sxs-lookup"><span data-stu-id="5032e-489">Setting environment variables</span></span>

<span data-ttu-id="5032e-490">Proměnné prostředí lze zadat pro proces v atributu `processPath`.</span><span class="sxs-lookup"><span data-stu-id="5032e-490">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="5032e-491">Zadejte proměnnou prostředí s podřízeným prvkem `<environmentVariable>` elementu kolekce `<environmentVariables>`.</span><span class="sxs-lookup"><span data-stu-id="5032e-491">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span> <span data-ttu-id="5032e-492">Proměnné prostředí nastavené v této části mají přednost před proměnnými prostředí systému.</span><span class="sxs-lookup"><span data-stu-id="5032e-492">Environment variables set in this section take precedence over system environment variables.</span></span>

<span data-ttu-id="5032e-493">Následující příklad nastaví dvě proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="5032e-493">The following example sets two environment variables.</span></span> <span data-ttu-id="5032e-494">`ASPNETCORE_ENVIRONMENT` nakonfiguruje prostředí aplikace tak, aby bylo `Development`.</span><span class="sxs-lookup"><span data-stu-id="5032e-494">`ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="5032e-495">Vývojář může tuto hodnotu dočasně nastavit v souboru *Web. config* , aby bylo možné vynutit, aby se [Stránka s výjimkou vývojářů](xref:fundamentals/error-handling) načetla při ladění výjimky aplikace.</span><span class="sxs-lookup"><span data-stu-id="5032e-495">A developer may temporarily set this value in the *web.config* file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="5032e-496">`CONFIG_DIR` je příklad uživatelsky definované proměnné prostředí, kde vývojář napsal kód, který přečte hodnotu při spuštění, aby vytvořil cestu pro načtení konfiguračního souboru aplikace.</span><span class="sxs-lookup"><span data-stu-id="5032e-496">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

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
> <span data-ttu-id="5032e-497">Alternativou k nastavení prostředí přímo v souboru *Web. config* je zahrnutí vlastnosti `<EnvironmentName>` do [profilu publikování (. pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) nebo souboru projektu.</span><span class="sxs-lookup"><span data-stu-id="5032e-497">An alternative to setting the environment directly in *web.config* is to include the `<EnvironmentName>` property in the [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) or project file.</span></span> <span data-ttu-id="5032e-498">Tento přístup nastaví prostředí v *souboru Web. config* při publikování projektu:</span><span class="sxs-lookup"><span data-stu-id="5032e-498">This approach sets the environment in *web.config* when the project is published:</span></span>
>
> ```xml
> <PropertyGroup>
>   <EnvironmentName>Development</EnvironmentName>
> </PropertyGroup>
> ```

> [!WARNING]
> <span data-ttu-id="5032e-499">Nastavte pouze proměnnou prostředí `ASPNETCORE_ENVIRONMENT`, aby `Development` na pracovních a testovacích serverech, které nejsou přístupné nedůvěryhodným sítím, jako je například Internet.</span><span class="sxs-lookup"><span data-stu-id="5032e-499">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## <a name="app_offlinehtm"></a><span data-ttu-id="5032e-500">App_offline. htm</span><span class="sxs-lookup"><span data-stu-id="5032e-500">app_offline.htm</span></span>

<span data-ttu-id="5032e-501">Pokud se v kořenovém adresáři aplikace zjistí soubor s názvem *App_offline. htm* , modul ASP.NET Core se pokusí aplikaci řádně vypnout a zastaví zpracování příchozích požadavků.</span><span class="sxs-lookup"><span data-stu-id="5032e-501">If a file with the name *app_offline.htm* is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="5032e-502">Pokud aplikace běží i po uplynutí počtu sekund definovaného v `shutdownTimeLimit`, modul ASP.NET Core ukončuje běžící proces.</span><span class="sxs-lookup"><span data-stu-id="5032e-502">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="5032e-503">Když je přítomen soubor *App_offline. htm* , ASP.NET Core modul reaguje na požadavky odesláním zpět obsahu souboru *App_offline. htm* .</span><span class="sxs-lookup"><span data-stu-id="5032e-503">While the *app_offline.htm* file is present, the ASP.NET Core Module responds to requests by sending back the contents of the *app_offline.htm* file.</span></span> <span data-ttu-id="5032e-504">Po odebrání souboru *App_offline. htm* aplikace spustí další požadavek.</span><span class="sxs-lookup"><span data-stu-id="5032e-504">When the *app_offline.htm* file is removed, the next request starts the app.</span></span>

<span data-ttu-id="5032e-505">Při použití nezpracovaného modelu hostování se aplikace nemusí okamžitě vypnout, pokud dojde k otevřenému připojení.</span><span class="sxs-lookup"><span data-stu-id="5032e-505">When using the out-of-process hosting model, the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="5032e-506">Připojení k protokolu WebSocket může například zpozdit ukončení aplikace.</span><span class="sxs-lookup"><span data-stu-id="5032e-506">For example, a websocket connection may delay app shut down.</span></span>

## <a name="start-up-error-page"></a><span data-ttu-id="5032e-507">Spouštěcí chybová stránka</span><span class="sxs-lookup"><span data-stu-id="5032e-507">Start-up error page</span></span>

<span data-ttu-id="5032e-508">Hostování v procesu i mimo procesy vytváří vlastní chybové stránky, když se nepodaří aplikaci spustit.</span><span class="sxs-lookup"><span data-stu-id="5032e-508">Both in-process and out-of-process hosting produce custom error pages when they fail to start the app.</span></span>

<span data-ttu-id="5032e-509">Pokud modul ASP.NET Core nenalezne obslužnou rutinu žádosti v procesu nebo mimo proces 500,0, zobrazí se stránka s kódem stavu *selhání načtení obslužné rutiny v* procesu nebo mimo proces.</span><span class="sxs-lookup"><span data-stu-id="5032e-509">If the ASP.NET Core Module fails to find either the in-process or out-of-process request handler, a *500.0 - In-Process/Out-Of-Process Handler Load Failure* status code page appears.</span></span>

<span data-ttu-id="5032e-510">Pro vnitroprocesové hostování v případě, že se modul ASP.NET Core nepodaří spustit aplikaci, zobrazí se stránka s kódem stavu *chyby při spuštění 500,30* .</span><span class="sxs-lookup"><span data-stu-id="5032e-510">For in-process hosting if the ASP.NET Core Module fails to start the app, a *500.30 - Start Failure* status code page appears.</span></span>

<span data-ttu-id="5032e-511">Pro hostování mimo procesy, pokud se ASP.NET Core modulu nespustí back-end proces, nebo dojde k selhání procesu back-endu, ale nenaslouchá na konfigurovaném portu, zobrazí se stránka s kódem stavu *selhání procesu 502,5* .</span><span class="sxs-lookup"><span data-stu-id="5032e-511">For out-of-process hosting if the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span>

<span data-ttu-id="5032e-512">Chcete-li tuto stránku potlačit a vrátit se na výchozí stavovou stránku služby IIS 5xx, použijte atribut `disableStartUpErrorPage`.</span><span class="sxs-lookup"><span data-stu-id="5032e-512">To suppress this page and revert to the default IIS 5xx status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="5032e-513">Další informace o konfiguraci vlastních chybových zpráv najdete v tématu [chyby protokolu HTTP \<httpErrors >](/iis/configuration/system.webServer/httpErrors/).</span><span class="sxs-lookup"><span data-stu-id="5032e-513">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

## <a name="log-creation-and-redirection"></a><span data-ttu-id="5032e-514">Vytvoření a přesměrování protokolu</span><span class="sxs-lookup"><span data-stu-id="5032e-514">Log creation and redirection</span></span>

<span data-ttu-id="5032e-515">Modul ASP.NET Core přesměruje výstup konzoly stdout a stderr na disk, pokud jsou nastaveny atributy `stdoutLogEnabled` a `stdoutLogFile` prvku `aspNetCore`.</span><span class="sxs-lookup"><span data-stu-id="5032e-515">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="5032e-516">Při vytvoření souboru protokolu jsou všechny složky v cestě `stdoutLogFile` vytvářeny modulem.</span><span class="sxs-lookup"><span data-stu-id="5032e-516">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="5032e-517">Fond aplikací musí mít oprávnění k zápisu do umístění, kam se zapisují protokoly (k poskytnutí oprávnění k zápisu použijte `IIS AppPool\<app_pool_name>`).</span><span class="sxs-lookup"><span data-stu-id="5032e-517">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="5032e-518">Protokoly nejsou otočeny, pokud nedochází k recyklování/restartování procesu.</span><span class="sxs-lookup"><span data-stu-id="5032e-518">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="5032e-519">Je zodpovědností hostitele omezit místo na disku, které používají protokoly.</span><span class="sxs-lookup"><span data-stu-id="5032e-519">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="5032e-520">Použití protokolu stdout se doporučuje pouze při řešení potíží se spouštěním aplikací při hostování služby IIS nebo při použití [podpory v době vývoje služby IIS se sadou Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), nikoli při ladění místně a při spuštění aplikace s IIS Express.</span><span class="sxs-lookup"><span data-stu-id="5032e-520">Using the stdout log is only recommended for troubleshooting app startup issues when hosting on IIS or when using [development-time support for IIS with Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), not while debugging locally and running the app with IIS Express.</span></span>

<span data-ttu-id="5032e-521">Nepoužívejte protokol stdout pro obecné účely protokolování aplikace.</span><span class="sxs-lookup"><span data-stu-id="5032e-521">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="5032e-522">Pro rutinu protokolování v aplikaci ASP.NET Core použijte knihovnu protokolování, která omezuje velikost souboru protokolu a otočí protokoly.</span><span class="sxs-lookup"><span data-stu-id="5032e-522">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="5032e-523">Další informace najdete v tématu [Zprostředkovatelé protokolování třetích stran](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="5032e-523">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="5032e-524">Časové razítko a Přípona souboru se automaticky přidají při vytvoření souboru protokolu.</span><span class="sxs-lookup"><span data-stu-id="5032e-524">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="5032e-525">Název souboru protokolu se skládá připojením časového razítka, ID procesu a přípony souboru ( *. log*) k poslednímu segmentu cesty `stdoutLogFile` (obvykle *stdout*), oddělený podtržítky.</span><span class="sxs-lookup"><span data-stu-id="5032e-525">The log file name is composed by appending the timestamp, process ID, and file extension (*.log*) to the last segment of the `stdoutLogFile` path (typically *stdout*) delimited by underscores.</span></span> <span data-ttu-id="5032e-526">Pokud `stdoutLogFile` cesta končí na *stdout*, protokol pro aplikaci s číslem PID 1934 vytvořeným v 2/5/2018 v 19:42:32 má název souboru *stdout_20180205194132_1934. log*.</span><span class="sxs-lookup"><span data-stu-id="5032e-526">If the `stdoutLogFile` path ends with *stdout*, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name *stdout_20180205194132_1934.log*.</span></span>

<span data-ttu-id="5032e-527">Pokud je hodnota `stdoutLogEnabled` false, budou chyby, ke kterým dojde při spuštění aplikace, zachyceny a generovány do protokolu událostí až do 30 KB.</span><span class="sxs-lookup"><span data-stu-id="5032e-527">If `stdoutLogEnabled` is false, errors that occur on app startup are captured and emitted to the event log up to 30 KB.</span></span> <span data-ttu-id="5032e-528">Po spuštění budou všechny další protokoly zahozeny.</span><span class="sxs-lookup"><span data-stu-id="5032e-528">After startup, all additional logs are discarded.</span></span>

<span data-ttu-id="5032e-529">Následující vzorový `aspNetCore` prvek nakonfiguruje protokolování stdout na relativní cestě `.\log\`.</span><span class="sxs-lookup"><span data-stu-id="5032e-529">The following sample `aspNetCore` element configures stdout logging at the relative path `.\log\`.</span></span> <span data-ttu-id="5032e-530">Potvrďte, že identita uživatele fondu aplikací má oprávnění k zápisu do zadané cesty.</span><span class="sxs-lookup"><span data-stu-id="5032e-530">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout"
    hostingModel="inprocess">
</aspNetCore>
```

<span data-ttu-id="5032e-531">Když publikujete aplikaci pro nasazení Azure App Service, Webová sada SDK nastaví hodnotu `stdoutLogFile` na `\\?\%home%\LogFiles\stdout`.</span><span class="sxs-lookup"><span data-stu-id="5032e-531">When publishing an app for Azure App Service deployment, the Web SDK sets the `stdoutLogFile` value to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="5032e-532">Pro aplikace hostované v Azure App Service je předdefinovaná proměnná prostředí `%home`.</span><span class="sxs-lookup"><span data-stu-id="5032e-532">The `%home` environment variable is predefined for apps hosted by Azure App Service.</span></span>

<span data-ttu-id="5032e-533">Další informace o formátech cest najdete v tématu [formáty souborů cest v systémech Windows](/dotnet/standard/io/file-path-formats).</span><span class="sxs-lookup"><span data-stu-id="5032e-533">For more information on path formats, see [File path formats on Windows systems](/dotnet/standard/io/file-path-formats).</span></span>

## <a name="enhanced-diagnostic-logs"></a><span data-ttu-id="5032e-534">Rozšířené diagnostické protokoly</span><span class="sxs-lookup"><span data-stu-id="5032e-534">Enhanced diagnostic logs</span></span>

<span data-ttu-id="5032e-535">Modul ASP.NET Core lze nakonfigurovat tak, aby poskytoval rozšířené diagnostické protokoly.</span><span class="sxs-lookup"><span data-stu-id="5032e-535">The ASP.NET Core Module is configurable to provide enhanced diagnostics logs.</span></span> <span data-ttu-id="5032e-536">Přidejte `<handlerSettings>` elementu do prvku `<aspNetCore>` v *souboru Web. config*. Nastavení `debugLevel` na `TRACE` zpřístupňuje vyšší věrnost diagnostických informací:</span><span class="sxs-lookup"><span data-stu-id="5032e-536">Add the `<handlerSettings>` element to the `<aspNetCore>` element in *web.config*. Setting the `debugLevel` to `TRACE` exposes a higher fidelity of diagnostic information:</span></span>

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

<span data-ttu-id="5032e-537">Složky v cestě zadané pro hodnotu `<handlerSetting>` (*protokoly* v předchozím příkladu) se nevytváří modulem automaticky a měly by být v nasazení předběžně existující.</span><span class="sxs-lookup"><span data-stu-id="5032e-537">Folders in the path provided to the `<handlerSetting>` value (*logs* in the preceding example) aren't created by the module automatically and should pre-exist in the deployment.</span></span> <span data-ttu-id="5032e-538">Fond aplikací musí mít oprávnění k zápisu do umístění, kam se zapisují protokoly (k poskytnutí oprávnění k zápisu použijte `IIS AppPool\<app_pool_name>`).</span><span class="sxs-lookup"><span data-stu-id="5032e-538">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="5032e-539">Hodnoty úrovně ladění (`debugLevel`) mohou zahrnovat jak úroveň, tak i umístění.</span><span class="sxs-lookup"><span data-stu-id="5032e-539">Debug level (`debugLevel`) values can include both the level and the location.</span></span>

<span data-ttu-id="5032e-540">Úrovně (v pořadí od nejméně po nejpodrobnější):</span><span class="sxs-lookup"><span data-stu-id="5032e-540">Levels (in order from least to most verbose):</span></span>

* <span data-ttu-id="5032e-541">Chyba</span><span class="sxs-lookup"><span data-stu-id="5032e-541">ERROR</span></span>
* <span data-ttu-id="5032e-542">Upozornění</span><span class="sxs-lookup"><span data-stu-id="5032e-542">WARNING</span></span>
* <span data-ttu-id="5032e-543">PŘÍJEMCE</span><span class="sxs-lookup"><span data-stu-id="5032e-543">INFO</span></span>
* <span data-ttu-id="5032e-544">TRACE</span><span class="sxs-lookup"><span data-stu-id="5032e-544">TRACE</span></span>

<span data-ttu-id="5032e-545">Umístění (je povoleno více umístění):</span><span class="sxs-lookup"><span data-stu-id="5032e-545">Locations (multiple locations are permitted):</span></span>

* <span data-ttu-id="5032e-546">STROMU</span><span class="sxs-lookup"><span data-stu-id="5032e-546">CONSOLE</span></span>
* <span data-ttu-id="5032e-547">EVENTLOG</span><span class="sxs-lookup"><span data-stu-id="5032e-547">EVENTLOG</span></span>
* <span data-ttu-id="5032e-548">SOUBORŮ</span><span class="sxs-lookup"><span data-stu-id="5032e-548">FILE</span></span>

<span data-ttu-id="5032e-549">Nastavení obslužné rutiny lze také poskytnout pomocí proměnných prostředí:</span><span class="sxs-lookup"><span data-stu-id="5032e-549">The handler settings can also be provided via environment variables:</span></span>

* <span data-ttu-id="5032e-550">`ASPNETCORE_MODULE_DEBUG_FILE` &ndash; cestu k souboru protokolu ladění.</span><span class="sxs-lookup"><span data-stu-id="5032e-550">`ASPNETCORE_MODULE_DEBUG_FILE` &ndash; Path to the debug log file.</span></span> <span data-ttu-id="5032e-551">(Výchozí: *aspnetcore-Debug. log*)</span><span class="sxs-lookup"><span data-stu-id="5032e-551">(Default: *aspnetcore-debug.log*)</span></span>
* <span data-ttu-id="5032e-552">`ASPNETCORE_MODULE_DEBUG` &ndash; nastavení úrovně ladění.</span><span class="sxs-lookup"><span data-stu-id="5032e-552">`ASPNETCORE_MODULE_DEBUG` &ndash; Debug level setting.</span></span>

> [!WARNING]
> <span data-ttu-id="5032e-553">Nenechávejte **protokolování** ladění povolené v nasazení po delší dobu, než je potřeba k řešení problému.</span><span class="sxs-lookup"><span data-stu-id="5032e-553">Do **not** leave debug logging enabled in the deployment for longer than required to troubleshoot an issue.</span></span> <span data-ttu-id="5032e-554">Velikost protokolu není omezená.</span><span class="sxs-lookup"><span data-stu-id="5032e-554">The size of the log isn't limited.</span></span> <span data-ttu-id="5032e-555">Když necháte protokol ladění povolený, může dojít k vyčerpání dostupného místa na disku a selhání serveru nebo služby App Service.</span><span class="sxs-lookup"><span data-stu-id="5032e-555">Leaving the debug log enabled can exhaust the available disk space and crash the server or app service.</span></span>

<span data-ttu-id="5032e-556">Příklad prvku `aspNetCore` v souboru *Web. config* naleznete v tématu [Konfigurace pomocí souboru Web. config](#configuration-with-webconfig) .</span><span class="sxs-lookup"><span data-stu-id="5032e-556">See [Configuration with web.config](#configuration-with-webconfig) for an example of the `aspNetCore` element in the *web.config* file.</span></span>

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="5032e-557">Konfigurace proxy serveru používá protokol HTTP a token párování.</span><span class="sxs-lookup"><span data-stu-id="5032e-557">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="5032e-558">*Platí pouze pro hostování mimo proces.*</span><span class="sxs-lookup"><span data-stu-id="5032e-558">*Only applies to out-of-process hosting.*</span></span>

<span data-ttu-id="5032e-559">Proxy vytvořený mezi modulem ASP.NET Core a Kestrel používá protokol HTTP.</span><span class="sxs-lookup"><span data-stu-id="5032e-559">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="5032e-560">Nehrozí riziko odposlouchávání provozu mezi modulem a Kestrel z umístění od serveru.</span><span class="sxs-lookup"><span data-stu-id="5032e-560">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="5032e-561">Párovací token se používá k zajištění, že žádosti přijaté službou Kestrel byly proxy službou IIS a nepocházejí z jiného zdroje.</span><span class="sxs-lookup"><span data-stu-id="5032e-561">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="5032e-562">Token párování se vytvoří a nastaví na proměnnou prostředí (`ASPNETCORE_TOKEN`) modulem.</span><span class="sxs-lookup"><span data-stu-id="5032e-562">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="5032e-563">Párování tokenu je také nastaveno na hlavičku (`MS-ASPNETCORE-TOKEN`) na každém proxy požadavku.</span><span class="sxs-lookup"><span data-stu-id="5032e-563">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="5032e-564">Middleware služby IIS kontroluje každý požadavek, který obdrží, a potvrdí, že hodnota hlavičky párového tokenu odpovídá hodnotě proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="5032e-564">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="5032e-565">Pokud se hodnoty tokenu neshodují, požadavek se zaprotokoluje a odmítne.</span><span class="sxs-lookup"><span data-stu-id="5032e-565">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="5032e-566">Proměnná prostředí párového tokenu a přenos dat mezi modulem a Kestrel nejsou přístupné z umístění mimo server.</span><span class="sxs-lookup"><span data-stu-id="5032e-566">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="5032e-567">Bez znalosti hodnoty párovacího tokenu nemůže útočník odeslat žádosti, které obcházejí kontrolu v Middlewari IIS.</span><span class="sxs-lookup"><span data-stu-id="5032e-567">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="5032e-568">ASP.NET Core modul se sdílenou konfigurací služby IIS</span><span class="sxs-lookup"><span data-stu-id="5032e-568">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="5032e-569">Instalační služba modulu ASP.NET Core se spouští s oprávněními účtu **TrustedInstaller** .</span><span class="sxs-lookup"><span data-stu-id="5032e-569">The ASP.NET Core Module installer runs with the privileges of the **TrustedInstaller** account.</span></span> <span data-ttu-id="5032e-570">Vzhledem k tomu, že účet místního systému nemá oprávnění k úpravám pro cestu ke sdílené složce, kterou používá sdílená konfigurace služby IIS, vyvolá Instalační program chybu odepření přístupu při pokusu o konfiguraci nastavení modulu v souboru *ApplicationHost. config* v sdílená složka.</span><span class="sxs-lookup"><span data-stu-id="5032e-570">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the *applicationHost.config* file on the share.</span></span>

<span data-ttu-id="5032e-571">Pokud používáte sdílenou konfiguraci IIS na stejném počítači jako instalaci služby IIS, spusťte instalační program sady prostředků ASP.NET Core s parametrem `OPT_NO_SHARED_CONFIG_CHECK` nastaveným na `1`:</span><span class="sxs-lookup"><span data-stu-id="5032e-571">When using an IIS Shared Configuration on the same machine as the IIS installation, run the ASP.NET Core Hosting Bundle installer with the `OPT_NO_SHARED_CONFIG_CHECK` parameter set to `1`:</span></span>

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

<span data-ttu-id="5032e-572">Pokud cesta ke sdílené konfiguraci není ve stejném počítači jako instalace služby IIS, postupujte podle těchto kroků:</span><span class="sxs-lookup"><span data-stu-id="5032e-572">When the path to the shared configuration isn't on the same machine as the IIS installation, follow these steps:</span></span>

1. <span data-ttu-id="5032e-573">Zakažte sdílenou konfiguraci služby IIS.</span><span class="sxs-lookup"><span data-stu-id="5032e-573">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="5032e-574">Spusťte instalační program.</span><span class="sxs-lookup"><span data-stu-id="5032e-574">Run the installer.</span></span>
1. <span data-ttu-id="5032e-575">Exportujte aktualizovaný soubor *ApplicationHost. config* do sdílené složky.</span><span class="sxs-lookup"><span data-stu-id="5032e-575">Export the updated *applicationHost.config* file to the share.</span></span>
1. <span data-ttu-id="5032e-576">Znovu povolte sdílenou konfiguraci služby IIS.</span><span class="sxs-lookup"><span data-stu-id="5032e-576">Re-enable the IIS Shared Configuration.</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="5032e-577">Verze modulu a instalační protokoly hostitelské sady</span><span class="sxs-lookup"><span data-stu-id="5032e-577">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="5032e-578">Postup určení verze nainstalovaného modulu ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="5032e-578">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="5032e-579">V hostitelském systému přejděte na *%windir%\system32\inetsrv*.</span><span class="sxs-lookup"><span data-stu-id="5032e-579">On the hosting system, navigate to *%windir%\System32\inetsrv*.</span></span>
1. <span data-ttu-id="5032e-580">Vyhledejte soubor *aspnetcore. dll* .</span><span class="sxs-lookup"><span data-stu-id="5032e-580">Locate the *aspnetcore.dll* file.</span></span>
1. <span data-ttu-id="5032e-581">Klikněte na soubor pravým tlačítkem a v místní nabídce vyberte **vlastnosti** .</span><span class="sxs-lookup"><span data-stu-id="5032e-581">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="5032e-582">Vyberte kartu **Podrobnosti** . **Verze souboru** a **verze produktu** reprezentují nainstalovanou verzi modulu.</span><span class="sxs-lookup"><span data-stu-id="5032e-582">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="5032e-583">Protokoly instalačních balíčků pro modul jsou k dispozici v *C:\\uživatelé\\% username%\\data\\místní\\Temp*. Soubor má název *dd_DotNetCoreWinSvrHosting__\<časové razítko > _000_AspNetCoreModule_x64. log*.</span><span class="sxs-lookup"><span data-stu-id="5032e-583">The Hosting Bundle installer logs for the module are found at *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. The file is named *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="5032e-584">Umístění souboru modulu, schématu a konfiguračního souboru</span><span class="sxs-lookup"><span data-stu-id="5032e-584">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="5032e-585">Modul</span><span class="sxs-lookup"><span data-stu-id="5032e-585">Module</span></span>

<span data-ttu-id="5032e-586">**Služba IIS (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="5032e-586">**IIS (x86/amd64):**</span></span>

* <span data-ttu-id="5032e-587">%windir%\System32\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="5032e-587">%windir%\System32\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="5032e-588">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="5032e-588">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="5032e-589">%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="5032e-589">%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="5032e-590">% ProgramFiles (x86)% \ IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="5032e-590">%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

<span data-ttu-id="5032e-591">**IIS Express (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="5032e-591">**IIS Express (x86/amd64):**</span></span>

* <span data-ttu-id="5032e-592">%ProgramFiles%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="5032e-592">%ProgramFiles%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="5032e-593">% ProgramFiles (x86)% \ IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="5032e-593">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="5032e-594">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="5032e-594">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="5032e-595">% ProgramFiles (x86)% \ IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="5032e-595">%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

### <a name="schema"></a><span data-ttu-id="5032e-596">Schéma</span><span class="sxs-lookup"><span data-stu-id="5032e-596">Schema</span></span>

<span data-ttu-id="5032e-597">**SLUŽBU**</span><span class="sxs-lookup"><span data-stu-id="5032e-597">**IIS**</span></span>

* <span data-ttu-id="5032e-598">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="5032e-598">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span></span>

* <span data-ttu-id="5032e-599">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span><span class="sxs-lookup"><span data-stu-id="5032e-599">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span></span>

<span data-ttu-id="5032e-600">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="5032e-600">**IIS Express**</span></span>

* <span data-ttu-id="5032e-601">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="5032e-601">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span></span>

* <span data-ttu-id="5032e-602">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span><span class="sxs-lookup"><span data-stu-id="5032e-602">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span></span>

### <a name="configuration"></a><span data-ttu-id="5032e-603">Konfigurace</span><span class="sxs-lookup"><span data-stu-id="5032e-603">Configuration</span></span>

<span data-ttu-id="5032e-604">**SLUŽBU**</span><span class="sxs-lookup"><span data-stu-id="5032e-604">**IIS**</span></span>

* <span data-ttu-id="5032e-605">%windir%\System32\inetsrv\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="5032e-605">%windir%\System32\inetsrv\config\applicationHost.config</span></span>

<span data-ttu-id="5032e-606">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="5032e-606">**IIS Express**</span></span>

* <span data-ttu-id="5032e-607">Visual Studio: {ROOT aplikace} \\. vs\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="5032e-607">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span></span>

* <span data-ttu-id="5032e-608">*IISExpress. exe* CLI:%USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span><span class="sxs-lookup"><span data-stu-id="5032e-608">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span></span>

<span data-ttu-id="5032e-609">Soubory lze najít hledáním *aspnetcore* v souboru *ApplicationHost. config* .</span><span class="sxs-lookup"><span data-stu-id="5032e-609">The files can be found by searching for *aspnetcore* in the *applicationHost.config* file.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="5032e-610">Modul ASP.NET Core je nativním modulem služby IIS, který se připojuje k kanálu IIS a přesměrovává webové požadavky na back-endové aplikace ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5032e-610">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to forward web requests to backend ASP.NET Core apps.</span></span>

<span data-ttu-id="5032e-611">Podporované verze systému Windows:</span><span class="sxs-lookup"><span data-stu-id="5032e-611">Supported Windows versions:</span></span>

* <span data-ttu-id="5032e-612">Windows 7 nebo novější</span><span class="sxs-lookup"><span data-stu-id="5032e-612">Windows 7 or later</span></span>
* <span data-ttu-id="5032e-613">Windows Server 2008 R2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="5032e-613">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="5032e-614">Modul funguje pouze s Kestrel.</span><span class="sxs-lookup"><span data-stu-id="5032e-614">The module only works with Kestrel.</span></span> <span data-ttu-id="5032e-615">Modul není kompatibilní s [http. sys](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="5032e-615">The module is incompatible with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

<span data-ttu-id="5032e-616">Vzhledem k tomu, že ASP.NET Core aplikace běží v procesu odděleném od pracovního procesu služby IIS, modul také zpracovává správu procesů.</span><span class="sxs-lookup"><span data-stu-id="5032e-616">Because ASP.NET Core apps run in a process separate from the IIS worker process, the module also handles process management.</span></span> <span data-ttu-id="5032e-617">Modul spustí proces pro aplikaci ASP.NET Core, když první požadavek dorazí a restartuje aplikaci, pokud dojde k chybě.</span><span class="sxs-lookup"><span data-stu-id="5032e-617">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it crashes.</span></span> <span data-ttu-id="5032e-618">To je v podstatě stejné chování jako u aplikací ASP.NET 4. x spouštěných vnitroprocesově ve službě IIS, které spravuje [Aktivační služba procesů systému Windows (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span><span class="sxs-lookup"><span data-stu-id="5032e-618">This is essentially the same behavior as seen with ASP.NET 4.x apps that run in-process in IIS that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="5032e-619">Následující diagram znázorňuje vztah mezi službou IIS, modulem ASP.NET Core a aplikací:</span><span class="sxs-lookup"><span data-stu-id="5032e-619">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app:</span></span>

![Modul ASP.NET Core](aspnet-core-module/_static/ancm-outofprocess.png)

<span data-ttu-id="5032e-621">Požadavky přicházející z webu do ovladače HTTP. sys v režimu jádra.</span><span class="sxs-lookup"><span data-stu-id="5032e-621">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="5032e-622">Ovladač směruje požadavky do služby IIS na konfigurovaném portu webu, obvykle 80 (HTTP) nebo 443 (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="5032e-622">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="5032e-623">Modul předá požadavky do Kestrel na náhodném portu pro aplikaci, což není port 80 nebo 443.</span><span class="sxs-lookup"><span data-stu-id="5032e-623">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="5032e-624">Modul Určuje port prostřednictvím proměnné prostředí při spuštění a [Služba IIS Integration middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) nakonfiguruje server tak, aby naslouchal `http://localhost:{port}`.</span><span class="sxs-lookup"><span data-stu-id="5032e-624">The module specifies the port via an environment variable at startup, and the [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) configures the server to listen on `http://localhost:{port}`.</span></span> <span data-ttu-id="5032e-625">Budou provedeny další kontroly a požadavky, které nepocházejí z modulu, jsou odmítnuty.</span><span class="sxs-lookup"><span data-stu-id="5032e-625">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="5032e-626">Modul nepodporuje předávání HTTPS, takže požadavky se předávají přes protokol HTTP i v případě, že je služba IIS prostřednictvím protokolu HTTPS přijímá.</span><span class="sxs-lookup"><span data-stu-id="5032e-626">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="5032e-627">Po Kestrel žádosti z modulu se požadavek odešle do kanálu middlewaru ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5032e-627">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="5032e-628">Kanál middlewaru zpracovává požadavek a předá ho jako instanci `HttpContext` do logiky aplikace.</span><span class="sxs-lookup"><span data-stu-id="5032e-628">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="5032e-629">Middleware přidaný integrací služby IIS: aktualizace schématu, vzdálené IP adresy a pathbase pro přesměrování požadavku do Kestrel.</span><span class="sxs-lookup"><span data-stu-id="5032e-629">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="5032e-630">Odpověď aplikace se předává zpátky do služby IIS, která ji přenáší zpátky do klienta HTTP, který žádost inicioval.</span><span class="sxs-lookup"><span data-stu-id="5032e-630">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="5032e-631">Mnoho nativních modulů, jako je ověřování systému Windows, zůstane aktivní.</span><span class="sxs-lookup"><span data-stu-id="5032e-631">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="5032e-632">Další informace o aktivních modulech služby IIS pomocí modulu ASP.NET Core najdete v tématu <xref:host-and-deploy/iis/modules>.</span><span class="sxs-lookup"><span data-stu-id="5032e-632">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="5032e-633">Modul ASP.NET Core může také:</span><span class="sxs-lookup"><span data-stu-id="5032e-633">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="5032e-634">Nastavte proměnné prostředí pro pracovní proces.</span><span class="sxs-lookup"><span data-stu-id="5032e-634">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="5032e-635">Protokoluje výstup stdout do úložiště souborů pro řešení problémů při spuštění.</span><span class="sxs-lookup"><span data-stu-id="5032e-635">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="5032e-636">Předejte tokeny ověřování systému Windows.</span><span class="sxs-lookup"><span data-stu-id="5032e-636">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="5032e-637">Jak nainstalovat a používat modul ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5032e-637">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="5032e-638">Pokyny k instalaci modulu ASP.NET Core najdete v tématu [instalace hostující sady .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span><span class="sxs-lookup"><span data-stu-id="5032e-638">For instructions on how to install the ASP.NET Core Module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="5032e-639">Konfigurace pomocí souboru Web. config</span><span class="sxs-lookup"><span data-stu-id="5032e-639">Configuration with web.config</span></span>

<span data-ttu-id="5032e-640">ASP.NET Core modul je nakonfigurovaný pomocí oddílu `aspNetCore` v uzlu `system.webServer` v souboru *Web. config* tohoto webu.</span><span class="sxs-lookup"><span data-stu-id="5032e-640">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="5032e-641">Následující soubor *Web. config* je publikován pro [nasazení závislé na rozhraní](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) a nakonfiguruje modul ASP.NET Core pro zpracování požadavků lokality:</span><span class="sxs-lookup"><span data-stu-id="5032e-641">The following *web.config* file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

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

<span data-ttu-id="5032e-642">Následující soubor *Web. config* je publikován pro [samostatně uzavřené nasazení](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span><span class="sxs-lookup"><span data-stu-id="5032e-642">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

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

<span data-ttu-id="5032e-643">Když se aplikace nasadí do [Azure App Service](https://azure.microsoft.com/services/app-service/), `stdoutLogFile`á cesta je nastavená na `\\?\%home%\LogFiles\stdout`.</span><span class="sxs-lookup"><span data-stu-id="5032e-643">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="5032e-644">Cesta ukládá protokoly stdout do složky *soubory protokolů* , což je umístění, které služba automaticky vytvořila.</span><span class="sxs-lookup"><span data-stu-id="5032e-644">The path saves stdout logs to the *LogFiles* folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="5032e-645">Informace o konfiguraci dílčí aplikace IIS najdete v tématu <xref:host-and-deploy/iis/index#sub-applications>.</span><span class="sxs-lookup"><span data-stu-id="5032e-645">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="5032e-646">Atributy elementu aspNetCore</span><span class="sxs-lookup"><span data-stu-id="5032e-646">Attributes of the aspNetCore element</span></span>

| <span data-ttu-id="5032e-647">Atribut</span><span class="sxs-lookup"><span data-stu-id="5032e-647">Attribute</span></span> | <span data-ttu-id="5032e-648">Popis</span><span class="sxs-lookup"><span data-stu-id="5032e-648">Description</span></span> | <span data-ttu-id="5032e-649">Výchozí</span><span class="sxs-lookup"><span data-stu-id="5032e-649">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="5032e-650">Volitelný řetězcový atribut.</span><span class="sxs-lookup"><span data-stu-id="5032e-650">Optional string attribute.</span></span></p><p><span data-ttu-id="5032e-651">Argumenty ke spustitelnému souboru určenému v **processPath**.</span><span class="sxs-lookup"><span data-stu-id="5032e-651">Arguments to the executable specified in **processPath**.</span></span></p>| |
| `disableStartUpErrorPage` | <p><span data-ttu-id="5032e-652">Volitelný atribut typu Boolean.</span><span class="sxs-lookup"><span data-stu-id="5032e-652">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="5032e-653">Pokud je nastaveno na true, stránka **502,5 – selhání procesu** se potlačí a stavová stránka 502 konfigurovaná v *souboru Web. config* má přednost.</span><span class="sxs-lookup"><span data-stu-id="5032e-653">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="5032e-654">Volitelný atribut typu Boolean.</span><span class="sxs-lookup"><span data-stu-id="5032e-654">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="5032e-655">Pokud je nastaveno na true, token se přepošle podřízenému procesu, který naslouchá na% ASPNETCORE_PORT% jako hlavička MS-ASPNETCORE-WINAUTHTOKEN na vyžádání.</span><span class="sxs-lookup"><span data-stu-id="5032e-655">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="5032e-656">Je zodpovědností za tento proces volání CloseHandle na tento token na žádost.</span><span class="sxs-lookup"><span data-stu-id="5032e-656">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `processesPerApplication` | <p><span data-ttu-id="5032e-657">Volitelný celočíselný atribut</span><span class="sxs-lookup"><span data-stu-id="5032e-657">Optional integer attribute.</span></span></p><p><span data-ttu-id="5032e-658">Určuje počet instancí procesu určeného v nastavení **processPath** , které lze v rámci aplikace vystavit.</span><span class="sxs-lookup"><span data-stu-id="5032e-658">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="5032e-659">Nastavení `processesPerApplication` se nedoporučuje.</span><span class="sxs-lookup"><span data-stu-id="5032e-659">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="5032e-660">Tento atribut bude v budoucí verzi odebrán.</span><span class="sxs-lookup"><span data-stu-id="5032e-660">This attribute will be removed in a future release.</span></span></p> | <span data-ttu-id="5032e-661">Výchozí: `1`</span><span class="sxs-lookup"><span data-stu-id="5032e-661">Default: `1`</span></span><br><span data-ttu-id="5032e-662">Minimum: `1`</span><span class="sxs-lookup"><span data-stu-id="5032e-662">Min: `1`</span></span><br><span data-ttu-id="5032e-663">Max: `100`</span><span class="sxs-lookup"><span data-stu-id="5032e-663">Max: `100`</span></span> |
| `processPath` | <p><span data-ttu-id="5032e-664">Povinný atribut řetězce.</span><span class="sxs-lookup"><span data-stu-id="5032e-664">Required string attribute.</span></span></p><p><span data-ttu-id="5032e-665">Cesta ke spustitelnému souboru, který spouští proces naslouchající na požadavky HTTP.</span><span class="sxs-lookup"><span data-stu-id="5032e-665">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="5032e-666">Jsou podporovány relativní cesty.</span><span class="sxs-lookup"><span data-stu-id="5032e-666">Relative paths are supported.</span></span> <span data-ttu-id="5032e-667">Pokud cesta začíná na `.`, bude cesta považována za relativní vzhledem k kořenu webu.</span><span class="sxs-lookup"><span data-stu-id="5032e-667">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="5032e-668">Volitelný celočíselný atribut</span><span class="sxs-lookup"><span data-stu-id="5032e-668">Optional integer attribute.</span></span></p><p><span data-ttu-id="5032e-669">Určuje, kolikrát je povoleno selhání procesu určeného v **processPath** za minutu.</span><span class="sxs-lookup"><span data-stu-id="5032e-669">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="5032e-670">Pokud je tento limit překročen, modul ukončí spuštění procesu po zbytek minuty.</span><span class="sxs-lookup"><span data-stu-id="5032e-670">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p> | <span data-ttu-id="5032e-671">Výchozí: `10`</span><span class="sxs-lookup"><span data-stu-id="5032e-671">Default: `10`</span></span><br><span data-ttu-id="5032e-672">Minimum: `0`</span><span class="sxs-lookup"><span data-stu-id="5032e-672">Min: `0`</span></span><br><span data-ttu-id="5032e-673">Max: `100`</span><span class="sxs-lookup"><span data-stu-id="5032e-673">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="5032e-674">Volitelný atribut TimeSpan.</span><span class="sxs-lookup"><span data-stu-id="5032e-674">Optional timespan attribute.</span></span></p><p><span data-ttu-id="5032e-675">Určuje dobu, po kterou modul ASP.NET Core čeká na odezvu od procesu, na kterém naslouchá% ASPNETCORE_PORT%.</span><span class="sxs-lookup"><span data-stu-id="5032e-675">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="5032e-676">Ve verzích modulu ASP.NET Core, které byly dodávány s vydáním ASP.NET Core 2,1 nebo vyšší, je `requestTimeout` zadáno v hodinách, minutách a sekundách.</span><span class="sxs-lookup"><span data-stu-id="5032e-676">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p> | <span data-ttu-id="5032e-677">Výchozí: `00:02:00`</span><span class="sxs-lookup"><span data-stu-id="5032e-677">Default: `00:02:00`</span></span><br><span data-ttu-id="5032e-678">Minimum: `00:00:00`</span><span class="sxs-lookup"><span data-stu-id="5032e-678">Min: `00:00:00`</span></span><br><span data-ttu-id="5032e-679">Max: `360:00:00`</span><span class="sxs-lookup"><span data-stu-id="5032e-679">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="5032e-680">Volitelný celočíselný atribut</span><span class="sxs-lookup"><span data-stu-id="5032e-680">Optional integer attribute.</span></span></p><p><span data-ttu-id="5032e-681">Doba v sekundách, po kterou modul čeká na řádné vypnutí spustitelného souboru, když se zjistí soubor *App_offline. htm* .</span><span class="sxs-lookup"><span data-stu-id="5032e-681">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="5032e-682">Výchozí: `10`</span><span class="sxs-lookup"><span data-stu-id="5032e-682">Default: `10`</span></span><br><span data-ttu-id="5032e-683">Minimum: `0`</span><span class="sxs-lookup"><span data-stu-id="5032e-683">Min: `0`</span></span><br><span data-ttu-id="5032e-684">Max: `600`</span><span class="sxs-lookup"><span data-stu-id="5032e-684">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="5032e-685">Volitelný celočíselný atribut</span><span class="sxs-lookup"><span data-stu-id="5032e-685">Optional integer attribute.</span></span></p><p><span data-ttu-id="5032e-686">Doba v sekundách, kterou modul počká, než spustitelný soubor spustí proces, který na portu naslouchá.</span><span class="sxs-lookup"><span data-stu-id="5032e-686">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="5032e-687">Pokud je tento časový limit překročen, modul proces ukončuje.</span><span class="sxs-lookup"><span data-stu-id="5032e-687">If this time limit is exceeded, the module kills the process.</span></span> <span data-ttu-id="5032e-688">Modul se pokusí znovu spustit proces, když obdrží novou žádost, a pokračuje v pokusu o restartování procesu u dalších příchozích požadavků, pokud se nepodaří spustit **rapidFailsPerMinute** počet pokusů během poslední minuty.</span><span class="sxs-lookup"><span data-stu-id="5032e-688">The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="5032e-689">Hodnota 0 (nula **) není považována za** nekonečný časový limit.</span><span class="sxs-lookup"><span data-stu-id="5032e-689">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="5032e-690">Výchozí: `120`</span><span class="sxs-lookup"><span data-stu-id="5032e-690">Default: `120`</span></span><br><span data-ttu-id="5032e-691">Minimum: `0`</span><span class="sxs-lookup"><span data-stu-id="5032e-691">Min: `0`</span></span><br><span data-ttu-id="5032e-692">Max: `3600`</span><span class="sxs-lookup"><span data-stu-id="5032e-692">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="5032e-693">Volitelný atribut typu Boolean.</span><span class="sxs-lookup"><span data-stu-id="5032e-693">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="5032e-694">Je-li nastavena hodnota true, **stdout** a **stderr** pro proces zadaný v **processPath** budou přesměrovány do souboru zadaného v **stdoutLogFile**.</span><span class="sxs-lookup"><span data-stu-id="5032e-694">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="5032e-695">Volitelný řetězcový atribut.</span><span class="sxs-lookup"><span data-stu-id="5032e-695">Optional string attribute.</span></span></p><p><span data-ttu-id="5032e-696">Určuje relativní nebo absolutní cestu k souboru, pro který je **stdout** a **stderr** z procesu určeného v **processPath** protokolováno.</span><span class="sxs-lookup"><span data-stu-id="5032e-696">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="5032e-697">Relativní cesty jsou relativní vzhledem k kořenu webu.</span><span class="sxs-lookup"><span data-stu-id="5032e-697">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="5032e-698">Všechny cesty začínající na `.` jsou relativní vzhledem k kořenu webu a všechny ostatní cesty se považují za absolutní cesty.</span><span class="sxs-lookup"><span data-stu-id="5032e-698">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="5032e-699">Aby modul vytvořil soubor protokolu, musí existovat všechny složky, které jsou v cestě k dispozici.</span><span class="sxs-lookup"><span data-stu-id="5032e-699">Any folders provided in the path must exist in order for the module to create the log file.</span></span> <span data-ttu-id="5032e-700">Pomocí oddělovačů podtržítka se do posledního segmentu cesty **stdoutLogFile** přidá časové razítko, ID procesu a Přípona souboru ( *. log*).</span><span class="sxs-lookup"><span data-stu-id="5032e-700">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="5032e-701">Pokud je jako hodnota zadána `.\logs\stdout`, je ukázkový protokol stdout uložen jako *stdout_20180205194132_1934. log* ve složce *logs* při uložení na 2/5/2018 v 19:41:32 s ID procesu 1934.</span><span class="sxs-lookup"><span data-stu-id="5032e-701">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

### <a name="setting-environment-variables"></a><span data-ttu-id="5032e-702">Nastavení proměnných prostředí</span><span class="sxs-lookup"><span data-stu-id="5032e-702">Setting environment variables</span></span>

<span data-ttu-id="5032e-703">Proměnné prostředí lze zadat pro proces v atributu `processPath`.</span><span class="sxs-lookup"><span data-stu-id="5032e-703">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="5032e-704">Zadejte proměnnou prostředí s podřízeným prvkem `<environmentVariable>` elementu kolekce `<environmentVariables>`.</span><span class="sxs-lookup"><span data-stu-id="5032e-704">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span>

> [!WARNING]
> <span data-ttu-id="5032e-705">Proměnné prostředí nastavené v této části jsou v konfliktu se sadou proměnných prostředí systému se stejným názvem.</span><span class="sxs-lookup"><span data-stu-id="5032e-705">Environment variables set in this section conflict with system environment variables set with the same name.</span></span> <span data-ttu-id="5032e-706">Pokud je proměnná prostředí nastavena v souboru *Web. config* i na úrovni systému systému Windows, bude hodnota ze souboru *Web. config* připojena k hodnotě proměnné prostředí systému (například `ASPNETCORE_ENVIRONMENT: Development;Development`), která brání aplikaci z začátek.</span><span class="sxs-lookup"><span data-stu-id="5032e-706">If an environment variable is set in both the *web.config* file and at the system level in Windows, the value from the *web.config* file becomes appended to the system environment variable value (for example, `ASPNETCORE_ENVIRONMENT: Development;Development`), which prevents the app from starting.</span></span>

<span data-ttu-id="5032e-707">Následující příklad nastaví dvě proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="5032e-707">The following example sets two environment variables.</span></span> <span data-ttu-id="5032e-708">`ASPNETCORE_ENVIRONMENT` nakonfiguruje prostředí aplikace tak, aby bylo `Development`.</span><span class="sxs-lookup"><span data-stu-id="5032e-708">`ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="5032e-709">Vývojář může tuto hodnotu dočasně nastavit v souboru *Web. config* , aby bylo možné vynutit, aby se [Stránka s výjimkou vývojářů](xref:fundamentals/error-handling) načetla při ladění výjimky aplikace.</span><span class="sxs-lookup"><span data-stu-id="5032e-709">A developer may temporarily set this value in the *web.config* file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="5032e-710">`CONFIG_DIR` je příklad uživatelsky definované proměnné prostředí, kde vývojář napsal kód, který přečte hodnotu při spuštění, aby vytvořil cestu pro načtení konfiguračního souboru aplikace.</span><span class="sxs-lookup"><span data-stu-id="5032e-710">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

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
> <span data-ttu-id="5032e-711">Nastavte pouze proměnnou prostředí `ASPNETCORE_ENVIRONMENT`, aby `Development` na pracovních a testovacích serverech, které nejsou přístupné nedůvěryhodným sítím, jako je například Internet.</span><span class="sxs-lookup"><span data-stu-id="5032e-711">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## <a name="app_offlinehtm"></a><span data-ttu-id="5032e-712">App_offline. htm</span><span class="sxs-lookup"><span data-stu-id="5032e-712">app_offline.htm</span></span>

<span data-ttu-id="5032e-713">Pokud se v kořenovém adresáři aplikace zjistí soubor s názvem *App_offline. htm* , modul ASP.NET Core se pokusí aplikaci řádně vypnout a zastaví zpracování příchozích požadavků.</span><span class="sxs-lookup"><span data-stu-id="5032e-713">If a file with the name *app_offline.htm* is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="5032e-714">Pokud aplikace běží i po uplynutí počtu sekund definovaného v `shutdownTimeLimit`, modul ASP.NET Core ukončuje běžící proces.</span><span class="sxs-lookup"><span data-stu-id="5032e-714">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="5032e-715">Když je přítomen soubor *App_offline. htm* , ASP.NET Core modul reaguje na požadavky odesláním zpět obsahu souboru *App_offline. htm* .</span><span class="sxs-lookup"><span data-stu-id="5032e-715">While the *app_offline.htm* file is present, the ASP.NET Core Module responds to requests by sending back the contents of the *app_offline.htm* file.</span></span> <span data-ttu-id="5032e-716">Po odebrání souboru *App_offline. htm* aplikace spustí další požadavek.</span><span class="sxs-lookup"><span data-stu-id="5032e-716">When the *app_offline.htm* file is removed, the next request starts the app.</span></span>

## <a name="start-up-error-page"></a><span data-ttu-id="5032e-717">Spouštěcí chybová stránka</span><span class="sxs-lookup"><span data-stu-id="5032e-717">Start-up error page</span></span>

<span data-ttu-id="5032e-718">Pokud se v modulu ASP.NET Core nepodaří spustit back-end proces nebo se spustí back-end proces, ale nenaslouchá na konfigurovaném portu, zobrazí se stránka s kódem stavu *selhání procesu 502,5* .</span><span class="sxs-lookup"><span data-stu-id="5032e-718">If the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span> <span data-ttu-id="5032e-719">Chcete-li tuto stránku potlačit a vrátit se na výchozí stavovou stránku služby IIS 502, použijte atribut `disableStartUpErrorPage`.</span><span class="sxs-lookup"><span data-stu-id="5032e-719">To suppress this page and revert to the default IIS 502 status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="5032e-720">Další informace o konfiguraci vlastních chybových zpráv najdete v tématu [chyby protokolu HTTP \<httpErrors >](/iis/configuration/system.webServer/httpErrors/).</span><span class="sxs-lookup"><span data-stu-id="5032e-720">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

![Stavová stránka selhání procesu procesu 502,5](aspnet-core-module/_static/ANCM-502_5.png)

## <a name="log-creation-and-redirection"></a><span data-ttu-id="5032e-722">Vytvoření a přesměrování protokolu</span><span class="sxs-lookup"><span data-stu-id="5032e-722">Log creation and redirection</span></span>

<span data-ttu-id="5032e-723">Modul ASP.NET Core přesměruje výstup konzoly stdout a stderr na disk, pokud jsou nastaveny atributy `stdoutLogEnabled` a `stdoutLogFile` prvku `aspNetCore`.</span><span class="sxs-lookup"><span data-stu-id="5032e-723">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="5032e-724">Při vytvoření souboru protokolu jsou všechny složky v cestě `stdoutLogFile` vytvářeny modulem.</span><span class="sxs-lookup"><span data-stu-id="5032e-724">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="5032e-725">Fond aplikací musí mít oprávnění k zápisu do umístění, kam se zapisují protokoly (k poskytnutí oprávnění k zápisu použijte `IIS AppPool\<app_pool_name>`).</span><span class="sxs-lookup"><span data-stu-id="5032e-725">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="5032e-726">Protokoly nejsou otočeny, pokud nedochází k recyklování/restartování procesu.</span><span class="sxs-lookup"><span data-stu-id="5032e-726">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="5032e-727">Je zodpovědností hostitele omezit místo na disku, které používají protokoly.</span><span class="sxs-lookup"><span data-stu-id="5032e-727">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="5032e-728">Použití protokolu stdout se doporučuje pouze při řešení potíží se spouštěním aplikací při hostování služby IIS nebo při použití [podpory v době vývoje služby IIS se sadou Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), nikoli při ladění místně a při spuštění aplikace s IIS Express.</span><span class="sxs-lookup"><span data-stu-id="5032e-728">Using the stdout log is only recommended for troubleshooting app startup issues when hosting on IIS or when using [development-time support for IIS with Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), not while debugging locally and running the app with IIS Express.</span></span>

<span data-ttu-id="5032e-729">Nepoužívejte protokol stdout pro obecné účely protokolování aplikace.</span><span class="sxs-lookup"><span data-stu-id="5032e-729">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="5032e-730">Pro rutinu protokolování v aplikaci ASP.NET Core použijte knihovnu protokolování, která omezuje velikost souboru protokolu a otočí protokoly.</span><span class="sxs-lookup"><span data-stu-id="5032e-730">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="5032e-731">Další informace najdete v tématu [Zprostředkovatelé protokolování třetích stran](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="5032e-731">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="5032e-732">Časové razítko a Přípona souboru se automaticky přidají při vytvoření souboru protokolu.</span><span class="sxs-lookup"><span data-stu-id="5032e-732">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="5032e-733">Název souboru protokolu se skládá připojením časového razítka, ID procesu a přípony souboru ( *. log*) k poslednímu segmentu cesty `stdoutLogFile` (obvykle *stdout*), oddělený podtržítky.</span><span class="sxs-lookup"><span data-stu-id="5032e-733">The log file name is composed by appending the timestamp, process ID, and file extension (*.log*) to the last segment of the `stdoutLogFile` path (typically *stdout*) delimited by underscores.</span></span> <span data-ttu-id="5032e-734">Pokud `stdoutLogFile` cesta končí na *stdout*, protokol pro aplikaci s číslem PID 1934 vytvořeným v 2/5/2018 v 19:42:32 má název souboru *stdout_20180205194132_1934. log*.</span><span class="sxs-lookup"><span data-stu-id="5032e-734">If the `stdoutLogFile` path ends with *stdout*, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name *stdout_20180205194132_1934.log*.</span></span>

<span data-ttu-id="5032e-735">Následující vzorový `aspNetCore` prvek nakonfiguruje protokolování stdout na relativní cestě `.\log\`.</span><span class="sxs-lookup"><span data-stu-id="5032e-735">The following sample `aspNetCore` element configures stdout logging at the relative path `.\log\`.</span></span> <span data-ttu-id="5032e-736">Potvrďte, že identita uživatele fondu aplikací má oprávnění k zápisu do zadané cesty.</span><span class="sxs-lookup"><span data-stu-id="5032e-736">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout">
</aspNetCore>
```

<span data-ttu-id="5032e-737">Když publikujete aplikaci pro nasazení Azure App Service, Webová sada SDK nastaví hodnotu `stdoutLogFile` na `\\?\%home%\LogFiles\stdout`.</span><span class="sxs-lookup"><span data-stu-id="5032e-737">When publishing an app for Azure App Service deployment, the Web SDK sets the `stdoutLogFile` value to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="5032e-738">Pro aplikace hostované v Azure App Service je předdefinovaná proměnná prostředí `%home`.</span><span class="sxs-lookup"><span data-stu-id="5032e-738">The `%home` environment variable is predefined for apps hosted by Azure App Service.</span></span>

<span data-ttu-id="5032e-739">Chcete-li vytvořit pravidla filtru protokolování, přečtěte si část [Konfigurace](xref:fundamentals/logging/index#log-filtering) a [filtrování protokolů](xref:fundamentals/logging/index#log-filtering) v dokumentaci ASP.NET Core protokolování.</span><span class="sxs-lookup"><span data-stu-id="5032e-739">To create logging filter rules, see the [Configuration](xref:fundamentals/logging/index#log-filtering) and [Log filtering](xref:fundamentals/logging/index#log-filtering) sections of the ASP.NET Core logging documentation.</span></span>

<span data-ttu-id="5032e-740">Další informace o formátech cest najdete v tématu [formáty souborů cest v systémech Windows](/dotnet/standard/io/file-path-formats).</span><span class="sxs-lookup"><span data-stu-id="5032e-740">For more information on path formats, see [File path formats on Windows systems](/dotnet/standard/io/file-path-formats).</span></span>

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="5032e-741">Konfigurace proxy serveru používá protokol HTTP a token párování.</span><span class="sxs-lookup"><span data-stu-id="5032e-741">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="5032e-742">Proxy vytvořený mezi modulem ASP.NET Core a Kestrel používá protokol HTTP.</span><span class="sxs-lookup"><span data-stu-id="5032e-742">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="5032e-743">Nehrozí riziko odposlouchávání provozu mezi modulem a Kestrel z umístění od serveru.</span><span class="sxs-lookup"><span data-stu-id="5032e-743">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="5032e-744">Párovací token se používá k zajištění, že žádosti přijaté službou Kestrel byly proxy službou IIS a nepocházejí z jiného zdroje.</span><span class="sxs-lookup"><span data-stu-id="5032e-744">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="5032e-745">Token párování se vytvoří a nastaví na proměnnou prostředí (`ASPNETCORE_TOKEN`) modulem.</span><span class="sxs-lookup"><span data-stu-id="5032e-745">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="5032e-746">Párování tokenu je také nastaveno na hlavičku (`MS-ASPNETCORE-TOKEN`) na každém proxy požadavku.</span><span class="sxs-lookup"><span data-stu-id="5032e-746">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="5032e-747">Middleware služby IIS kontroluje každý požadavek, který obdrží, a potvrdí, že hodnota hlavičky párového tokenu odpovídá hodnotě proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="5032e-747">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="5032e-748">Pokud se hodnoty tokenu neshodují, požadavek se zaprotokoluje a odmítne.</span><span class="sxs-lookup"><span data-stu-id="5032e-748">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="5032e-749">Proměnná prostředí párového tokenu a přenos dat mezi modulem a Kestrel nejsou přístupné z umístění mimo server.</span><span class="sxs-lookup"><span data-stu-id="5032e-749">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="5032e-750">Bez znalosti hodnoty párovacího tokenu nemůže útočník odeslat žádosti, které obcházejí kontrolu v Middlewari IIS.</span><span class="sxs-lookup"><span data-stu-id="5032e-750">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="5032e-751">ASP.NET Core modul se sdílenou konfigurací služby IIS</span><span class="sxs-lookup"><span data-stu-id="5032e-751">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="5032e-752">Instalační služba modulu ASP.NET Core se spouští s oprávněními účtu **TrustedInstaller** .</span><span class="sxs-lookup"><span data-stu-id="5032e-752">The ASP.NET Core Module installer runs with the privileges of the **TrustedInstaller** account.</span></span> <span data-ttu-id="5032e-753">Vzhledem k tomu, že účet místního systému nemá oprávnění k úpravám pro cestu ke sdílené složce, kterou používá sdílená konfigurace služby IIS, vyvolá Instalační program chybu odepření přístupu při pokusu o konfiguraci nastavení modulu v souboru *ApplicationHost. config* v sdílená složka.</span><span class="sxs-lookup"><span data-stu-id="5032e-753">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the *applicationHost.config* file on the share.</span></span>

<span data-ttu-id="5032e-754">Pokud používáte sdílenou konfiguraci služby IIS, postupujte podle těchto kroků:</span><span class="sxs-lookup"><span data-stu-id="5032e-754">When using an IIS Shared Configuration, follow these steps:</span></span>

1. <span data-ttu-id="5032e-755">Zakažte sdílenou konfiguraci služby IIS.</span><span class="sxs-lookup"><span data-stu-id="5032e-755">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="5032e-756">Spusťte instalační program.</span><span class="sxs-lookup"><span data-stu-id="5032e-756">Run the installer.</span></span>
1. <span data-ttu-id="5032e-757">Exportujte aktualizovaný soubor *ApplicationHost. config* do sdílené složky.</span><span class="sxs-lookup"><span data-stu-id="5032e-757">Export the updated *applicationHost.config* file to the share.</span></span>
1. <span data-ttu-id="5032e-758">Znovu povolte sdílenou konfiguraci služby IIS.</span><span class="sxs-lookup"><span data-stu-id="5032e-758">Re-enable the IIS Shared Configuration.</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="5032e-759">Verze modulu a instalační protokoly hostitelské sady</span><span class="sxs-lookup"><span data-stu-id="5032e-759">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="5032e-760">Postup určení verze nainstalovaného modulu ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="5032e-760">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="5032e-761">V hostitelském systému přejděte na *%windir%\system32\inetsrv*.</span><span class="sxs-lookup"><span data-stu-id="5032e-761">On the hosting system, navigate to *%windir%\System32\inetsrv*.</span></span>
1. <span data-ttu-id="5032e-762">Vyhledejte soubor *aspnetcore. dll* .</span><span class="sxs-lookup"><span data-stu-id="5032e-762">Locate the *aspnetcore.dll* file.</span></span>
1. <span data-ttu-id="5032e-763">Klikněte na soubor pravým tlačítkem a v místní nabídce vyberte **vlastnosti** .</span><span class="sxs-lookup"><span data-stu-id="5032e-763">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="5032e-764">Vyberte kartu **Podrobnosti** . **Verze souboru** a **verze produktu** reprezentují nainstalovanou verzi modulu.</span><span class="sxs-lookup"><span data-stu-id="5032e-764">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="5032e-765">Protokoly instalačních balíčků pro modul jsou k dispozici v *C:\\uživatelé\\% username%\\data\\místní\\Temp*. Soubor má název *dd_DotNetCoreWinSvrHosting__\<časové razítko > _000_AspNetCoreModule_x64. log*.</span><span class="sxs-lookup"><span data-stu-id="5032e-765">The Hosting Bundle installer logs for the module are found at *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. The file is named *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="5032e-766">Umístění souboru modulu, schématu a konfiguračního souboru</span><span class="sxs-lookup"><span data-stu-id="5032e-766">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="5032e-767">Modul</span><span class="sxs-lookup"><span data-stu-id="5032e-767">Module</span></span>

<span data-ttu-id="5032e-768">**Služba IIS (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="5032e-768">**IIS (x86/amd64):**</span></span>

* <span data-ttu-id="5032e-769">%windir%\System32\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="5032e-769">%windir%\System32\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="5032e-770">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="5032e-770">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span></span>

<span data-ttu-id="5032e-771">**IIS Express (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="5032e-771">**IIS Express (x86/amd64):**</span></span>

* <span data-ttu-id="5032e-772">%ProgramFiles%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="5032e-772">%ProgramFiles%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="5032e-773">% ProgramFiles (x86)% \ IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="5032e-773">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span></span>

### <a name="schema"></a><span data-ttu-id="5032e-774">Schéma</span><span class="sxs-lookup"><span data-stu-id="5032e-774">Schema</span></span>

<span data-ttu-id="5032e-775">**SLUŽBU**</span><span class="sxs-lookup"><span data-stu-id="5032e-775">**IIS**</span></span>

* <span data-ttu-id="5032e-776">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="5032e-776">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span></span>

<span data-ttu-id="5032e-777">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="5032e-777">**IIS Express**</span></span>

* <span data-ttu-id="5032e-778">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="5032e-778">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span></span>

### <a name="configuration"></a><span data-ttu-id="5032e-779">Konfigurace</span><span class="sxs-lookup"><span data-stu-id="5032e-779">Configuration</span></span>

<span data-ttu-id="5032e-780">**SLUŽBU**</span><span class="sxs-lookup"><span data-stu-id="5032e-780">**IIS**</span></span>

* <span data-ttu-id="5032e-781">%windir%\System32\inetsrv\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="5032e-781">%windir%\System32\inetsrv\config\applicationHost.config</span></span>

<span data-ttu-id="5032e-782">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="5032e-782">**IIS Express**</span></span>

* <span data-ttu-id="5032e-783">Visual Studio: {ROOT aplikace} \\. vs\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="5032e-783">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span></span>

* <span data-ttu-id="5032e-784">*IISExpress. exe* CLI:%USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span><span class="sxs-lookup"><span data-stu-id="5032e-784">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span></span>

<span data-ttu-id="5032e-785">Soubory lze najít hledáním *aspnetcore* v souboru *ApplicationHost. config* .</span><span class="sxs-lookup"><span data-stu-id="5032e-785">The files can be found by searching for *aspnetcore* in the *applicationHost.config* file.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="5032e-786">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="5032e-786">Additional resources</span></span>

* <xref:host-and-deploy/iis/index>
* [<span data-ttu-id="5032e-787">Úložiště GitHub modulu ASP.NET Core (odkazový zdroj)</span><span class="sxs-lookup"><span data-stu-id="5032e-787">ASP.NET Core Module GitHub repository (reference source)</span></span>](https://github.com/aspnet/AspNetCoreModule)
* <xref:host-and-deploy/iis/modules>
