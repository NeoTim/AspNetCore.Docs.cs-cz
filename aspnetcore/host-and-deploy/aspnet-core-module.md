---
title: Modul ASP.NET Core
author: guardrex
description: Zjistěte, jak nakonfigurovat modul ASP.NET Core pro hostování aplikací ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/08/2019
uid: host-and-deploy/aspnet-core-module
ms.openlocfilehash: c1c34f368cb3f7767bf0f229ff70c5ab53c6005f
ms.sourcegitcommit: fcdf9aaa6c45c1a926bd870ed8f893bdb4935152
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2019
ms.locfileid: "72165316"
---
# <a name="aspnet-core-module"></a><span data-ttu-id="1e2f9-103">Modul ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1e2f9-103">ASP.NET Core Module</span></span>

<span data-ttu-id="1e2f9-104">Díky [Dykstra](https://github.com/tdykstra), [Rick Strahl](https://github.com/RickStrahl), [Novák Rossův](https://github.com/Tratcher), [Rick Anderson](https://twitter.com/RickAndMSFT), [sourabh Shirhatti](https://twitter.com/sshirhatti), [Justin Kotalik](https://github.com/jkotalik)a [Luke](https://github.com/guardrex) Latham</span><span class="sxs-lookup"><span data-stu-id="1e2f9-104">By [Tom Dykstra](https://github.com/tdykstra), [Rick Strahl](https://github.com/RickStrahl), [Chris Ross](https://github.com/Tratcher), [Rick Anderson](https://twitter.com/RickAndMSFT), [Sourabh Shirhatti](https://twitter.com/sshirhatti), [Justin Kotalik](https://github.com/jkotalik), and [Luke Latham](https://github.com/guardrex)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="1e2f9-105">Modul ASP.NET Core je nativním modulem služby IIS, který se připojuje k kanálu IIS na jednu z těchto:</span><span class="sxs-lookup"><span data-stu-id="1e2f9-105">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to either:</span></span>

* <span data-ttu-id="1e2f9-106">Hostování aplikace ASP.NET Core uvnitř pracovního procesu služby IIS (`w3wp.exe`) se nazývá [model hostování v rámci procesu](#in-process-hosting-model).</span><span class="sxs-lookup"><span data-stu-id="1e2f9-106">Host an ASP.NET Core app inside of the IIS worker process (`w3wp.exe`), called the [in-process hosting model](#in-process-hosting-model).</span></span>
* <span data-ttu-id="1e2f9-107">Dopředné webové požadavky do back-endu ASP.NET Core aplikace, na které běží [Server Kestrel](xref:fundamentals/servers/kestrel), se nazývá [model hostování mimo proces](#out-of-process-hosting-model).</span><span class="sxs-lookup"><span data-stu-id="1e2f9-107">Forward web requests to a backend ASP.NET Core app running the [Kestrel server](xref:fundamentals/servers/kestrel), called the [out-of-process hosting model](#out-of-process-hosting-model).</span></span>

<span data-ttu-id="1e2f9-108">Podporované verze systému Windows:</span><span class="sxs-lookup"><span data-stu-id="1e2f9-108">Supported Windows versions:</span></span>

* <span data-ttu-id="1e2f9-109">Windows 7 nebo novější</span><span class="sxs-lookup"><span data-stu-id="1e2f9-109">Windows 7 or later</span></span>
* <span data-ttu-id="1e2f9-110">Windows Server 2008 R2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="1e2f9-110">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="1e2f9-111">Při hostování v procesu používá modul implementaci vnitroprocesového serveru pro službu IIS, která se nazývá IIS HTTP Server (`IISHttpServer`).</span><span class="sxs-lookup"><span data-stu-id="1e2f9-111">When hosting in-process, the module uses an in-process server implementation for IIS, called IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="1e2f9-112">Při hostování mimo proces funguje modul pouze s Kestrel.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-112">When hosting out-of-process, the module only works with Kestrel.</span></span> <span data-ttu-id="1e2f9-113">Modul nefunguje s [ovladačem HTTP. sys](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="1e2f9-113">The module doesn't function with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

## <a name="hosting-models"></a><span data-ttu-id="1e2f9-114">Modely hostingu</span><span class="sxs-lookup"><span data-stu-id="1e2f9-114">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="1e2f9-115">Model hostování v procesu</span><span class="sxs-lookup"><span data-stu-id="1e2f9-115">In-process hosting model</span></span>

<span data-ttu-id="1e2f9-116">ASP.NET Core aplikace jako výchozí pro model hostování v rámci procesu.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-116">ASP.NET Core apps default to the in-process hosting model.</span></span>

<span data-ttu-id="1e2f9-117">Při hostování v procesu platí následující vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="1e2f9-117">The following characteristics apply when hosting in-process:</span></span>

* <span data-ttu-id="1e2f9-118">Místo [Kestrel](xref:fundamentals/servers/kestrel) serveru se používá server HTTP služby IIS (`IISHttpServer`).</span><span class="sxs-lookup"><span data-stu-id="1e2f9-118">IIS HTTP Server (`IISHttpServer`) is used instead of [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span> <span data-ttu-id="1e2f9-119">Pro vnitroprocesové [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) volání <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> do:</span><span class="sxs-lookup"><span data-stu-id="1e2f9-119">For in-process, [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> to:</span></span>

  * <span data-ttu-id="1e2f9-120">Zaregistrujte `IISHttpServer`.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-120">Register the `IISHttpServer`.</span></span>
  * <span data-ttu-id="1e2f9-121">Nakonfigurujte port a základní cestu, na kterých má Server naslouchat při spuštění za modulem ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-121">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
  * <span data-ttu-id="1e2f9-122">Nakonfigurujte hostitele tak, aby zachytával chyby při spuštění.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-122">Configure the host to capture startup errors.</span></span>

* <span data-ttu-id="1e2f9-123">[RequestTimeout atribut](#attributes-of-the-aspnetcore-element) neplatí pro hostování v procesu.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-123">The [requestTimeout attribute](#attributes-of-the-aspnetcore-element) doesn't apply to in-process hosting.</span></span>

* <span data-ttu-id="1e2f9-124">Sdílení fondem aplikací mezi aplikacemi se nepodporuje.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-124">Sharing an app pool among apps isn't supported.</span></span> <span data-ttu-id="1e2f9-125">Použijte jeden fond aplikací na aplikaci.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-125">Use one app pool per app.</span></span>

* <span data-ttu-id="1e2f9-126">Při použití [Webdeploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) nebo ručně uvedení [soubor app_offline.htm v nasazení](xref:host-and-deploy/iis/index#locked-deployment-files), aplikace nemusí vypnout okamžitě při otevření připojení.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-126">When using [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) or manually placing an [app_offline.htm file in the deployment](xref:host-and-deploy/iis/index#locked-deployment-files), the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="1e2f9-127">Připojení soketu websocket bylo třeba může způsobit prodlevu při ukončení aplikace.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-127">For example, a websocket connection may delay app shut down.</span></span>

* <span data-ttu-id="1e2f9-128">Architektura (bitové verze) (x64 nebo x86) nainstalovaný modul runtime a aplikace musí odpovídat architektuře fondu aplikací.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-128">The architecture (bitness) of the app and installed runtime (x64 or x86) must match the architecture of the app pool.</span></span>

* <span data-ttu-id="1e2f9-129">Odpojení klienta jsou zjištěny.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-129">Client disconnects are detected.</span></span> <span data-ttu-id="1e2f9-130">[HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) token zrušení se zrušila, když se klient odpojí.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-130">The [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) cancellation token is cancelled when the client disconnects.</span></span>

* <span data-ttu-id="1e2f9-131">V ASP.NET Core 2.2.1 nebo starším <xref:System.IO.Directory.GetCurrentDirectory*> vrátí pracovní adresář procesu spuštěného službou IIS místo adresáře aplikace (například *C:\Windows\System32\inetsrv* pro *W3wp. exe*).</span><span class="sxs-lookup"><span data-stu-id="1e2f9-131">In ASP.NET Core 2.2.1 or earlier, <xref:System.IO.Directory.GetCurrentDirectory*> returns the worker directory of the process started by IIS rather than the app's directory (for example, *C:\Windows\System32\inetsrv* for *w3wp.exe*).</span></span>

  <span data-ttu-id="1e2f9-132">Ukázkový kód, který nastaví aktuální adresář aplikace, najdete v článku [CurrentDirectoryHelpers třídy](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/3.x/CurrentDirectoryHelpers.cs).</span><span class="sxs-lookup"><span data-stu-id="1e2f9-132">For sample code that sets the app's current directory, see the [CurrentDirectoryHelpers class](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/3.x/CurrentDirectoryHelpers.cs).</span></span> <span data-ttu-id="1e2f9-133">Volání `SetCurrentDirectory` metody.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-133">Call the `SetCurrentDirectory` method.</span></span> <span data-ttu-id="1e2f9-134">Následující volání <xref:System.IO.Directory.GetCurrentDirectory*> poskytují adresáře aplikace.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-134">Subsequent calls to <xref:System.IO.Directory.GetCurrentDirectory*> provide the app's directory.</span></span>

* <span data-ttu-id="1e2f9-135">Při hostování v procesu se nevolá <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> interně pro inicializaci uživatele.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-135">When hosting in-process, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span> <span data-ttu-id="1e2f9-136">Proto implementace <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> používaná k transformaci deklarací po každém ověření není ve výchozím nastavení aktivována.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-136">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="1e2f9-137">Při transformaci deklarací s implementací <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> zavolejte <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> pro přidání ověřovacích služeb:</span><span class="sxs-lookup"><span data-stu-id="1e2f9-137">When transforming claims with an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation, call <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> to add authentication services:</span></span>

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

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="1e2f9-138">Model hostování mimo proces</span><span class="sxs-lookup"><span data-stu-id="1e2f9-138">Out-of-process hosting model</span></span>

<span data-ttu-id="1e2f9-139">Chcete-li nakonfigurovat aplikaci pro hostování mimo procesy, nastavte hodnotu vlastnosti `<AspNetCoreHostingModel>` na `OutOfProcess` v souboru projektu ( *. csproj*):</span><span class="sxs-lookup"><span data-stu-id="1e2f9-139">To configure an app for out-of-process hosting, set the value of the `<AspNetCoreHostingModel>` property to `OutOfProcess` in the project file (*.csproj*):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="1e2f9-140">Hostování v rámci procesu je nastaveno pomocí `InProcess`, což je výchozí hodnota.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-140">In-process hosting is set with `InProcess`, which is the default value.</span></span>

<span data-ttu-id="1e2f9-141">Místo HTTP serveru IIS (`IISHttpServer`) se používá server [Kestrel](xref:fundamentals/servers/kestrel) .</span><span class="sxs-lookup"><span data-stu-id="1e2f9-141">[Kestrel](xref:fundamentals/servers/kestrel) server is used instead of IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="1e2f9-142">Pro mimoprocesové aplikace [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) volá <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> do:</span><span class="sxs-lookup"><span data-stu-id="1e2f9-142">For out-of-process, [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> to:</span></span>

* <span data-ttu-id="1e2f9-143">Nakonfigurujte port a základní cestu, na kterých má Server naslouchat při spuštění za modulem ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-143">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
* <span data-ttu-id="1e2f9-144">Nakonfigurujte hostitele tak, aby zachytával chyby při spuštění.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-144">Configure the host to capture startup errors.</span></span>

### <a name="hosting-model-changes"></a><span data-ttu-id="1e2f9-145">Hostování změny modelu</span><span class="sxs-lookup"><span data-stu-id="1e2f9-145">Hosting model changes</span></span>

<span data-ttu-id="1e2f9-146">Pokud `hostingModel` nastavení se změnilo v *web.config* souboru (podrobně [konfigurace pomocí souboru web.config](#configuration-with-webconfig) části), modul pro službu IIS recykluje pracovní proces.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-146">If the `hostingModel` setting is changed in the *web.config* file (explained in the [Configuration with web.config](#configuration-with-webconfig) section), the module recycles the worker process for IIS.</span></span>

<span data-ttu-id="1e2f9-147">Pro službu IIS Express modul nebude recyklovat pracovní proces, ale místo toho aktivuje řádné vypnutí aktuální proces IIS Express.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-147">For IIS Express, the module doesn't recycle the worker process but instead triggers a graceful shutdown of the current IIS Express process.</span></span> <span data-ttu-id="1e2f9-148">Další požadavek na aplikaci vytvoří nový proces IIS Express.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-148">The next request to the app spawns a new IIS Express process.</span></span>

### <a name="process-name"></a><span data-ttu-id="1e2f9-149">Název procesu</span><span class="sxs-lookup"><span data-stu-id="1e2f9-149">Process name</span></span>

<span data-ttu-id="1e2f9-150">`Process.GetCurrentProcess().ProcessName` sestavy `w3wp` / `iisexpress` (v procesu) nebo `dotnet` (out-of-process).</span><span class="sxs-lookup"><span data-stu-id="1e2f9-150">`Process.GetCurrentProcess().ProcessName` reports `w3wp`/`iisexpress` (in-process) or `dotnet` (out-of-process).</span></span>

<span data-ttu-id="1e2f9-151">Mnoho nativních modulů, jako je ověřování systému Windows, zůstane aktivní.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-151">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="1e2f9-152">Další informace o aktivních modulech služby IIS pomocí modulu ASP.NET Core najdete v tématu <xref:host-and-deploy/iis/modules>.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-152">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="1e2f9-153">Modul ASP.NET Core může také:</span><span class="sxs-lookup"><span data-stu-id="1e2f9-153">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="1e2f9-154">Nastavte proměnné prostředí pro pracovní proces.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-154">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="1e2f9-155">Protokoluje výstup stdout do úložiště souborů pro řešení problémů při spuštění.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-155">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="1e2f9-156">Předejte tokeny ověřování systému Windows.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-156">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="1e2f9-157">Jak nainstalovat a používat modul ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1e2f9-157">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="1e2f9-158">Pokyny k instalaci modulu ASP.NET Core najdete v tématu [instalace hostující sady .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span><span class="sxs-lookup"><span data-stu-id="1e2f9-158">For instructions on how to install the ASP.NET Core Module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="1e2f9-159">Konfigurace pomocí souboru web.config</span><span class="sxs-lookup"><span data-stu-id="1e2f9-159">Configuration with web.config</span></span>

<span data-ttu-id="1e2f9-160">Modul ASP.NET Core, nastavena `aspNetCore` část `system.webServer` uzlu na webu *web.config* souboru.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-160">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="1e2f9-161">Následující *web.config* soubor je publikován pro [nasazení závisí na architektuře](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) a konfiguruje modul ASP.NET Core pro zpracování požadavků lokality:</span><span class="sxs-lookup"><span data-stu-id="1e2f9-161">The following *web.config* file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

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

<span data-ttu-id="1e2f9-162">Následující *web.config* je publikována pro [samostatná nasazení](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span><span class="sxs-lookup"><span data-stu-id="1e2f9-162">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

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

<span data-ttu-id="1e2f9-163"><xref:System.Configuration.SectionInformation.InheritInChildApplications*> Je nastavena na `false` k označení, že nastavení zadané v rámci [ \<umístění >](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) element nedědí od neznámých aplikací, které jsou umístěny v podadresáři aplikace.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-163">The <xref:System.Configuration.SectionInformation.InheritInChildApplications*> property is set to `false` to indicate that the settings specified within the [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) element aren't inherited by apps that reside in a subdirectory of the app.</span></span>

<span data-ttu-id="1e2f9-164">Když je aplikace nasazená na [služby Azure App Service](https://azure.microsoft.com/services/app-service/), `stdoutLogFile` je nastavena cesta `\\?\%home%\LogFiles\stdout`.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-164">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="1e2f9-165">Protokoly stdout a uloží ji *LogFiles* složce, která je na místě automaticky vytvořené službou.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-165">The path saves stdout logs to the *LogFiles* folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="1e2f9-166">Informace o konfiguraci dílčí aplikace služby IIS, naleznete v tématu <xref:host-and-deploy/iis/index#sub-applications>.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-166">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="1e2f9-167">Atributy elementu aspNetCore</span><span class="sxs-lookup"><span data-stu-id="1e2f9-167">Attributes of the aspNetCore element</span></span>

| <span data-ttu-id="1e2f9-168">Atribut</span><span class="sxs-lookup"><span data-stu-id="1e2f9-168">Attribute</span></span> | <span data-ttu-id="1e2f9-169">Popis</span><span class="sxs-lookup"><span data-stu-id="1e2f9-169">Description</span></span> | <span data-ttu-id="1e2f9-170">Výchozí</span><span class="sxs-lookup"><span data-stu-id="1e2f9-170">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="1e2f9-171">Volitelný atribut řetězce.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-171">Optional string attribute.</span></span></p><p><span data-ttu-id="1e2f9-172">Argumenty pro spustitelný soubor určený v **processPath**.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-172">Arguments to the executable specified in **processPath**.</span></span></p> | |
| `disableStartUpErrorPage` | <p><span data-ttu-id="1e2f9-173">Volitelný logický atribut.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-173">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="1e2f9-174">Při hodnotě true **502.5 – selhání procesu** Potlačené stránky a znakovou stránku 502 stav nakonfigurované v *web.config* má přednost před.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-174">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="1e2f9-175">Volitelný logický atribut.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-175">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="1e2f9-176">Při hodnotě true se token předá podřízený proces naslouchání na ASPNETCORE_PORT % jako záhlaví "MS-ASPNETCORE-WINAUTHTOKEN" každý požadavek.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-176">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="1e2f9-177">Je odpovědností tento proces pro volání CloseHandle na tento token každý požadavek.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-177">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `hostingModel` | <p><span data-ttu-id="1e2f9-178">Volitelný atribut řetězce.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-178">Optional string attribute.</span></span></p><p><span data-ttu-id="1e2f9-179">Určuje model hostování jako v procesu (`InProcess`) nebo out-of-process (`OutOfProcess`).</span><span class="sxs-lookup"><span data-stu-id="1e2f9-179">Specifies the hosting model as in-process (`InProcess`) or out-of-process (`OutOfProcess`).</span></span></p> | `InProcess` |
| `processesPerApplication` | <p><span data-ttu-id="1e2f9-180">Volitelný celočíselný atribut.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-180">Optional integer attribute.</span></span></p><p><span data-ttu-id="1e2f9-181">Určuje počet instancí procesu zadaný v **processPath** nastavení, které lze se nespředený nahoru na app.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-181">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="1e2f9-182">&dagger;Hostování v procesu, je omezena na hodnotu `1`.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-182">&dagger;For in-process hosting, the value is limited to `1`.</span></span></p><p><span data-ttu-id="1e2f9-183">Nastavení `processesPerApplication` se nedoporučuje.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-183">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="1e2f9-184">Tento atribut bude v budoucí verzi odebrán.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-184">This attribute will be removed in a future release.</span></span></p> | <span data-ttu-id="1e2f9-185">Výchozí hodnota: `1`</span><span class="sxs-lookup"><span data-stu-id="1e2f9-185">Default: `1`</span></span><br><span data-ttu-id="1e2f9-186">Min: `1`</span><span class="sxs-lookup"><span data-stu-id="1e2f9-186">Min: `1`</span></span><br><span data-ttu-id="1e2f9-187">Max: `100`&dagger;</span><span class="sxs-lookup"><span data-stu-id="1e2f9-187">Max: `100`&dagger;</span></span> |
| `processPath` | <p><span data-ttu-id="1e2f9-188">Požadovaný atribut typu string.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-188">Required string attribute.</span></span></p><p><span data-ttu-id="1e2f9-189">Cesta ke spustitelnému souboru, který spustí nějaký proces naslouchání požadavků protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-189">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="1e2f9-190">Jsou podporovány relativní cesty.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-190">Relative paths are supported.</span></span> <span data-ttu-id="1e2f9-191">Pokud cestu začíná `.`, cesta se považuje za kořeni webu.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-191">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="1e2f9-192">Volitelný celočíselný atribut.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-192">Optional integer attribute.</span></span></p><p><span data-ttu-id="1e2f9-193">Určuje, kolikrát podle procesu **processPath** může při selhání za minutu.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-193">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="1e2f9-194">Pokud je tento limit překročen, modulu zastaví spuštění procesu pro zbytek minuty.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-194">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p><p><span data-ttu-id="1e2f9-195">Není podporováno s hostitelem v procesu.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-195">Not supported with in-process hosting.</span></span></p> | <span data-ttu-id="1e2f9-196">Výchozí hodnota: `10`</span><span class="sxs-lookup"><span data-stu-id="1e2f9-196">Default: `10`</span></span><br><span data-ttu-id="1e2f9-197">Min: `0`</span><span class="sxs-lookup"><span data-stu-id="1e2f9-197">Min: `0`</span></span><br><span data-ttu-id="1e2f9-198">Max: `100`</span><span class="sxs-lookup"><span data-stu-id="1e2f9-198">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="1e2f9-199">Atribut volitelný časový interval.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-199">Optional timespan attribute.</span></span></p><p><span data-ttu-id="1e2f9-200">Určuje dobu, pro kterou modul ASP.NET Core čeká na odpověď z procesu naslouchání na ASPNETCORE_PORT %.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-200">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="1e2f9-201">Ve verzích modul ASP.NET Core dodávané s verzí technologie ASP.NET Core 2.1 nebo novější `requestTimeout` je zadán v hodiny, minuty a sekundy.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-201">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p><p><span data-ttu-id="1e2f9-202">Neplatí pro hostování v procesu.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-202">Doesn't apply to in-process hosting.</span></span> <span data-ttu-id="1e2f9-203">Hostování v procesu, čeká modul app ke zpracování požadavku.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-203">For in-process hosting, the module waits for the app to process the request.</span></span></p><p><span data-ttu-id="1e2f9-204">Platné hodnoty segmentů minut a sekund řetězce jsou v rozsahu 0-59.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-204">Valid values for minutes and seconds segments of the string are in the range 0-59.</span></span> <span data-ttu-id="1e2f9-205">Při použití hodnoty **60** v hodnotě minut nebo sekund dojde k chybě systému *500 – interní chyba serveru*.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-205">Use of **60** in the value for minutes or seconds results in a *500 - Internal Server Error*.</span></span></p> | <span data-ttu-id="1e2f9-206">Výchozí hodnota: `00:02:00`</span><span class="sxs-lookup"><span data-stu-id="1e2f9-206">Default: `00:02:00`</span></span><br><span data-ttu-id="1e2f9-207">Min: `00:00:00`</span><span class="sxs-lookup"><span data-stu-id="1e2f9-207">Min: `00:00:00`</span></span><br><span data-ttu-id="1e2f9-208">Max: `360:00:00`</span><span class="sxs-lookup"><span data-stu-id="1e2f9-208">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="1e2f9-209">Volitelný celočíselný atribut.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-209">Optional integer attribute.</span></span></p><p><span data-ttu-id="1e2f9-210">Doba trvání v sekundách, které modul čeká na spustitelný soubor na řádné vypnutí při *app_offline.htm* je detekován soubor.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-210">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="1e2f9-211">Výchozí hodnota: `10`</span><span class="sxs-lookup"><span data-stu-id="1e2f9-211">Default: `10`</span></span><br><span data-ttu-id="1e2f9-212">Min: `0`</span><span class="sxs-lookup"><span data-stu-id="1e2f9-212">Min: `0`</span></span><br><span data-ttu-id="1e2f9-213">Max: `600`</span><span class="sxs-lookup"><span data-stu-id="1e2f9-213">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="1e2f9-214">Volitelný celočíselný atribut.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-214">Optional integer attribute.</span></span></p><p><span data-ttu-id="1e2f9-215">Doba trvání v sekundách, které modul čeká na spustitelný soubor ke spuštění procesu naslouchání na portu.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-215">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="1e2f9-216">Pokud je překročen časový limit, modul ukončí proces.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-216">If this time limit is exceeded, the module kills the process.</span></span> <span data-ttu-id="1e2f9-217">Modul se pokusí znovu spustit proces, když ji dostane novou žádost a pokusit se restartovat proces na dalších příchozích požadavků, pokud se aplikaci nepodaří spustit **rapidFailsPerMinute** málokdy za posledních minuta se zajištěním provozu.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-217">The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="1e2f9-218">Hodnota 0 (nula) je **ne** za neomezený časový limit.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-218">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="1e2f9-219">Výchozí hodnota: `120`</span><span class="sxs-lookup"><span data-stu-id="1e2f9-219">Default: `120`</span></span><br><span data-ttu-id="1e2f9-220">Min: `0`</span><span class="sxs-lookup"><span data-stu-id="1e2f9-220">Min: `0`</span></span><br><span data-ttu-id="1e2f9-221">Max: `3600`</span><span class="sxs-lookup"><span data-stu-id="1e2f9-221">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="1e2f9-222">Volitelný logický atribut.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-222">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="1e2f9-223">Při hodnotě true se **stdout** a **stderr** pro proces určený v **processPath** se přesměrují do souboru zadaného v **stdoutLogFile**.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-223">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="1e2f9-224">Volitelný atribut řetězce.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-224">Optional string attribute.</span></span></p><p><span data-ttu-id="1e2f9-225">Určuje relativní nebo absolutní cestu, pro kterou **stdout** a **stderr** z procesu podle **processPath** přihlášeni.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-225">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="1e2f9-226">Relativní cesty jsou relativní vzhledem k kořen webu.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-226">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="1e2f9-227">Libovolnou cestu od `.` jsou relativní vzhledem k webu kořenové a všechny ostatní cesty jsou považovány za absolutní cesty.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-227">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="1e2f9-228">Při vytvoření souboru protokolu se v modulu vytvoří všechny složky, které jsou v cestě zadané.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-228">Any folders provided in the path are created by the module when the log file is created.</span></span> <span data-ttu-id="1e2f9-229">Pomocí oddělovače podtržítko, časové razítko, ID procesu a přípona souboru ( *.log*) jsou přidány na poslední segment **stdoutLogFile** cestu.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-229">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="1e2f9-230">Pokud `.\logs\stdout` je zadaný jako hodnota, je uložen protokolu příklad stdout jako *stdout_20180205194132_1934.log* v *protokoly* složky při uložení 5 2. 2018 v 19:41:32 s ID procesu sady 1934.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-230">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

### <a name="set-environment-variables"></a><span data-ttu-id="1e2f9-231">Nastavení proměnných prostředí</span><span class="sxs-lookup"><span data-stu-id="1e2f9-231">Set environment variables</span></span>

<span data-ttu-id="1e2f9-232">Proměnné prostředí se dá nastavit pro proces v `processPath` atribut.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-232">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="1e2f9-233">Zadat proměnné prostředí s `<environmentVariable>` podřízený prvek `<environmentVariables>` prvek kolekce.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-233">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span> <span data-ttu-id="1e2f9-234">Proměnné prostředí nastavené v této části přednost systémové proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-234">Environment variables set in this section take precedence over system environment variables.</span></span>

<span data-ttu-id="1e2f9-235">Následující příklad nastaví dvě proměnné prostředí v *souboru Web. config*. `ASPNETCORE_ENVIRONMENT` nakonfiguruje prostředí aplikace na `Development`.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-235">The following example sets two environment variables in *web.config*. `ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="1e2f9-236">Vývojáři mohou dočasně nastaví tuto hodnotu *web.config* souboru, aby bylo možné vynutit [stránku výjimek pro vývojáře](xref:fundamentals/error-handling) načíst při ladění aplikace výjimky.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-236">A developer may temporarily set this value in the *web.config* file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="1e2f9-237">`CONFIG_DIR` je příkladem proměnné prostředí, kam má vývojář zapisovat kód, který čte hodnoty při spuštění tvoří cestu pro načtení konfiguračního souboru aplikace.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-237">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

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
> <span data-ttu-id="1e2f9-238">Alternativou k nastavení prostředí přímo v souboru *Web. config* je zahrnutí vlastnosti `<EnvironmentName>` do profilu publikování ( *. pubxml*) nebo souboru projektu.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-238">An alternative to setting the environment directly in *web.config* is to include the `<EnvironmentName>` property in the publish profile (*.pubxml*) or project file.</span></span> <span data-ttu-id="1e2f9-239">Tento přístup nastaví prostředí v *souboru Web. config* při publikování projektu:</span><span class="sxs-lookup"><span data-stu-id="1e2f9-239">This approach sets the environment in *web.config* when the project is published:</span></span>
>
> ```xml
> <PropertyGroup>
>   <EnvironmentName>Development</EnvironmentName>
> </PropertyGroup>
> ```

> [!WARNING]
> <span data-ttu-id="1e2f9-240">Nastavit pouze `ASPNETCORE_ENVIRONMENT` proměnnou prostředí, aby `Development` na přípravy a testování serverů, které nejsou dostupné k nedůvěryhodným sítím, jako je Internet.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-240">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## <a name="app_offlinehtm"></a><span data-ttu-id="1e2f9-241">app_offline.htm</span><span class="sxs-lookup"><span data-stu-id="1e2f9-241">app_offline.htm</span></span>

<span data-ttu-id="1e2f9-242">Pokud soubor s názvem *app_offline.htm* je zjištěna v kořenovém adresáři aplikace, že modul ASP.NET Core se pokusí řádné vypnutí aplikace a zastavit zpracování příchozích požadavků.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-242">If a file with the name *app_offline.htm* is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="1e2f9-243">Pokud aplikace pořád běží po dobu v sekundách podle `shutdownTimeLimit`, že modul ASP.NET Core ukončuje spuštěnému procesu.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-243">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="1e2f9-244">Zatímco *app_offline.htm* soubor je k dispozici, že modul ASP.NET Core reaguje na požadavky odesílá zpět obsah *app_offline.htm* souboru.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-244">While the *app_offline.htm* file is present, the ASP.NET Core Module responds to requests by sending back the contents of the *app_offline.htm* file.</span></span> <span data-ttu-id="1e2f9-245">Když *app_offline.htm* soubor bude odstraněn, příští žádosti o spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-245">When the *app_offline.htm* file is removed, the next request starts the app.</span></span>

<span data-ttu-id="1e2f9-246">Pokud používáte model hostingu mimo proces, aplikace nemusí vypnout okamžitě při otevření připojení.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-246">When using the out-of-process hosting model, the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="1e2f9-247">Připojení soketu websocket bylo třeba může způsobit prodlevu při ukončení aplikace.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-247">For example, a websocket connection may delay app shut down.</span></span>

## <a name="start-up-error-page"></a><span data-ttu-id="1e2f9-248">Spuštění chybovou stránku</span><span class="sxs-lookup"><span data-stu-id="1e2f9-248">Start-up error page</span></span>

<span data-ttu-id="1e2f9-249">Hostování v procesu a mimo proces vytvoření vlastní chybové stránky, když selžou a spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-249">Both in-process and out-of-process hosting produce custom error pages when they fail to start the app.</span></span>

<span data-ttu-id="1e2f9-250">Pokud se nepodaří najít buď nebo na více instancí procesu žádosti o obslužnou rutinu, že modul ASP.NET Core *500.0 – Chyba načtení obslužné rutiny v procesu/Out – proces* se zobrazí stav znakovou stránku.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-250">If the ASP.NET Core Module fails to find either the in-process or out-of-process request handler, a *500.0 - In-Process/Out-Of-Process Handler Load Failure* status code page appears.</span></span>

<span data-ttu-id="1e2f9-251">Pro hostování v procesu, pokud se nepodaří spustit aplikaci, že modul ASP.NET Core *500.30 - Start selhání* se zobrazí stav znakovou stránku.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-251">For in-process hosting if the ASP.NET Core Module fails to start the app, a *500.30 - Start Failure* status code page appears.</span></span>

<span data-ttu-id="1e2f9-252">Pro hostování mimo proces, pokud se nespustí back-endový proces nebo back-endový proces spustí ale nebude moci poslouchat na konfigurovaném portu, že modul ASP.NET Core *502.5 – selhání procesu* se zobrazí stav znakovou stránku.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-252">For out-of-process hosting if the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span>

<span data-ttu-id="1e2f9-253">Chcete-li potlačit tuto stránku a vrátit se na výchozí služby IIS 5xx stav znakovou stránku, použijte `disableStartUpErrorPage` atribut.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-253">To suppress this page and revert to the default IIS 5xx status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="1e2f9-254">Další informace o konfiguraci vlastních chybových zpráv, najdete v části [chyby protokolu HTTP \<httpErrors >](/iis/configuration/system.webServer/httpErrors/).</span><span class="sxs-lookup"><span data-stu-id="1e2f9-254">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

## <a name="log-creation-and-redirection"></a><span data-ttu-id="1e2f9-255">Vytvoření protokolu a přesměrování</span><span class="sxs-lookup"><span data-stu-id="1e2f9-255">Log creation and redirection</span></span>

<span data-ttu-id="1e2f9-256">Modul ASP.NET Core přesměruje výstup stdout a stderr console na disk, pokud `stdoutLogEnabled` a `stdoutLogFile` atributy `aspNetCore` element jsou nastavené.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-256">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="1e2f9-257">Při vytvoření souboru protokolu jsou všechny složky v cestě `stdoutLogFile` vytvářeny modulem.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-257">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="1e2f9-258">Fond aplikací musí mít oprávnění k zápisu do umístění, ve kterém jsou zapsány protokoly (použijte `IIS AppPool\<app_pool_name>` poskytnout oprávnění k zápisu).</span><span class="sxs-lookup"><span data-stu-id="1e2f9-258">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="1e2f9-259">Protokoly nejsou střídán, pokud dojde k recyklování procesů/restartování.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-259">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="1e2f9-260">Je odpovědností hostitel k omezení místa na disku, které využijete v protokolech.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-260">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="1e2f9-261">Použití protokolu stdout se doporučuje jenom pro řešení potíží při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-261">Using the stdout log is only recommended for troubleshooting app startup issues.</span></span> <span data-ttu-id="1e2f9-262">Nepoužívejte stdout protokolu pro účely protokolování obecné aplikace.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-262">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="1e2f9-263">Pro rutiny protokolování v aplikaci ASP.NET Core, použijte protokolování knihovnu, která omezuje velikost souboru protokolu a otočí protokoly.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-263">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="1e2f9-264">Další informace najdete v tématu [zprostředkovatele přihlášení třetí strany](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="1e2f9-264">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="1e2f9-265">Časové razítko a soubor rozšíření jsou přidány automaticky při vytvoření souboru protokolu.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-265">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="1e2f9-266">Název souboru protokolu se skládá připojením časové razítko, ID procesu a přípona souboru ( *.log*) na poslední segment `stdoutLogFile` cestu (obvykle *stdout*) oddělené podtržítka.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-266">The log file name is composed by appending the timestamp, process ID, and file extension (*.log*) to the last segment of the `stdoutLogFile` path (typically *stdout*) delimited by underscores.</span></span> <span data-ttu-id="1e2f9-267">Pokud `stdoutLogFile` cesta končí *stdout*, má název souboru protokolu pro aplikaci s PID 1934 vytvořili 5 2. 2018 v 19:42:32 *stdout_20180205194132_1934.log*.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-267">If the `stdoutLogFile` path ends with *stdout*, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name *stdout_20180205194132_1934.log*.</span></span>

<span data-ttu-id="1e2f9-268">Pokud `stdoutLogEnabled` má hodnotu false, chyby, ke kterým dochází při spuštění aplikace se zachytí a do protokolu událostí, protože ho až 30 KB.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-268">If `stdoutLogEnabled` is false, errors that occur on app startup are captured and emitted to the event log up to 30 KB.</span></span> <span data-ttu-id="1e2f9-269">Po spuštění se zahodí všechny další protokoly.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-269">After startup, all additional logs are discarded.</span></span>

<span data-ttu-id="1e2f9-270">Následující vzorový @no__t element-0 v souboru *Web. config* nakonfiguruje protokolování stdout pro aplikaci hostovanou v Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-270">The following sample `aspNetCore` element in a *web.config* file configures stdout logging for an app hosted in Azure App Service.</span></span> <span data-ttu-id="1e2f9-271">Místní cesta nebo cesta ke sdílené položce je přijatelné pro místní přihlášení.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-271">A local path or network share path is acceptable for local logging.</span></span> <span data-ttu-id="1e2f9-272">Ověřte, jestli uživatel identita fondu aplikací má oprávnění k zápisu do zadaná výstupní cesta.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-272">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="InProcess">
</aspNetCore>
```

## <a name="enhanced-diagnostic-logs"></a><span data-ttu-id="1e2f9-273">Rozšířené diagnostické protokoly</span><span class="sxs-lookup"><span data-stu-id="1e2f9-273">Enhanced diagnostic logs</span></span>

<span data-ttu-id="1e2f9-274">Modul ASP.NET Core lze nakonfigurovat tak, aby poskytoval rozšířené diagnostické protokoly.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-274">The ASP.NET Core Module is configurable to provide enhanced diagnostics logs.</span></span> <span data-ttu-id="1e2f9-275">Přidat `<handlerSettings>` elementu `<aspNetCore>` prvek *web.config*. Nastavení `debugLevel` k `TRACE` zpřístupňuje větší věrnost diagnostické informace:</span><span class="sxs-lookup"><span data-stu-id="1e2f9-275">Add the `<handlerSettings>` element to the `<aspNetCore>` element in *web.config*. Setting the `debugLevel` to `TRACE` exposes a higher fidelity of diagnostic information:</span></span>

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

<span data-ttu-id="1e2f9-276">Všechny složky v cestě (*protokoly* v předchozím příkladu) se vytvoří modulem, když se vytvoří soubor protokolu.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-276">Any folders in the path (*logs* in the preceding example) are created by the module when the log file is created.</span></span> <span data-ttu-id="1e2f9-277">Fond aplikací musí mít oprávnění k zápisu do umístění, ve kterém jsou zapsány protokoly (použijte `IIS AppPool\<app_pool_name>` poskytnout oprávnění k zápisu).</span><span class="sxs-lookup"><span data-stu-id="1e2f9-277">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="1e2f9-278">Ladit úrovně (`debugLevel`) hodnoty může obsahovat úroveň a umístění.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-278">Debug level (`debugLevel`) values can include both the level and the location.</span></span>

<span data-ttu-id="1e2f9-279">Úrovně (v pořadí od nejméně na nejpodrobnější):</span><span class="sxs-lookup"><span data-stu-id="1e2f9-279">Levels (in order from least to most verbose):</span></span>

* <span data-ttu-id="1e2f9-280">CHYBA</span><span class="sxs-lookup"><span data-stu-id="1e2f9-280">ERROR</span></span>
* <span data-ttu-id="1e2f9-281">UPOZORNĚNÍ</span><span class="sxs-lookup"><span data-stu-id="1e2f9-281">WARNING</span></span>
* <span data-ttu-id="1e2f9-282">INFORMACE O</span><span class="sxs-lookup"><span data-stu-id="1e2f9-282">INFO</span></span>
* <span data-ttu-id="1e2f9-283">TRACE</span><span class="sxs-lookup"><span data-stu-id="1e2f9-283">TRACE</span></span>

<span data-ttu-id="1e2f9-284">Umístění (umístění více jsou povoleny):</span><span class="sxs-lookup"><span data-stu-id="1e2f9-284">Locations (multiple locations are permitted):</span></span>

* <span data-ttu-id="1e2f9-285">KONZOLY</span><span class="sxs-lookup"><span data-stu-id="1e2f9-285">CONSOLE</span></span>
* <span data-ttu-id="1e2f9-286">PROTOKOL UDÁLOSTÍ</span><span class="sxs-lookup"><span data-stu-id="1e2f9-286">EVENTLOG</span></span>
* <span data-ttu-id="1e2f9-287">SOUBOR</span><span class="sxs-lookup"><span data-stu-id="1e2f9-287">FILE</span></span>

<span data-ttu-id="1e2f9-288">Obslužná rutina nastavení se dá zadat i prostřednictvím proměnné prostředí:</span><span class="sxs-lookup"><span data-stu-id="1e2f9-288">The handler settings can also be provided via environment variables:</span></span>

* <span data-ttu-id="1e2f9-289">`ASPNETCORE_MODULE_DEBUG_FILE` &ndash; Cesta k souboru protokolu ladění.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-289">`ASPNETCORE_MODULE_DEBUG_FILE` &ndash; Path to the debug log file.</span></span> <span data-ttu-id="1e2f9-290">(Výchozí: *aspnetcore-debug.log*)</span><span class="sxs-lookup"><span data-stu-id="1e2f9-290">(Default: *aspnetcore-debug.log*)</span></span>
* <span data-ttu-id="1e2f9-291">`ASPNETCORE_MODULE_DEBUG` &ndash; Ladit nastavení úrovně.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-291">`ASPNETCORE_MODULE_DEBUG` &ndash; Debug level setting.</span></span>

> [!WARNING]
> <span data-ttu-id="1e2f9-292">Proveďte **není** protokolování ladění povoleno v nasazení pro déle, než se požaduje chcete vyřešit nějaký problém.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-292">Do **not** leave debug logging enabled in the deployment for longer than required to troubleshoot an issue.</span></span> <span data-ttu-id="1e2f9-293">Velikost souboru protokolu není omezený.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-293">The size of the log isn't limited.</span></span> <span data-ttu-id="1e2f9-294">Opuštění protokol ladění povoleno může vyčerpat dostupné místo na disku a chybách u aplikací na serveru nebo služby app service.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-294">Leaving the debug log enabled can exhaust the available disk space and crash the server or app service.</span></span>

<span data-ttu-id="1e2f9-295">Naleznete v tématu [konfigurace pomocí souboru web.config](#configuration-with-webconfig) příklad `aspNetCore` prvek *web.config* souboru.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-295">See [Configuration with web.config](#configuration-with-webconfig) for an example of the `aspNetCore` element in the *web.config* file.</span></span>

## <a name="modify-the-stack-size"></a><span data-ttu-id="1e2f9-296">Úprava velikosti zásobníku</span><span class="sxs-lookup"><span data-stu-id="1e2f9-296">Modify the stack size</span></span>

<span data-ttu-id="1e2f9-297">*Platí pouze při použití modelu hostování v rámci procesu.*</span><span class="sxs-lookup"><span data-stu-id="1e2f9-297">*Only applies when using the in-process hosting model.*</span></span>

<span data-ttu-id="1e2f9-298">Nakonfigurujte velikost spravovaného zásobníku pomocí nastavení `stackSize` v bajtech v *souboru Web. config*. Výchozí velikost je `1048576` bajtů (1 MB).</span><span class="sxs-lookup"><span data-stu-id="1e2f9-298">Configure the managed stack size using the `stackSize` setting in bytes in *web.config*. The default size is `1048576` bytes (1 MB).</span></span>

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

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="1e2f9-299">Konfigurace proxy serveru používá protokol HTTP a token pro párování</span><span class="sxs-lookup"><span data-stu-id="1e2f9-299">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="1e2f9-300">*Platí jenom pro hostování mimo proces.*</span><span class="sxs-lookup"><span data-stu-id="1e2f9-300">*Only applies to out-of-process hosting.*</span></span>

<span data-ttu-id="1e2f9-301">Proxy server mezi modul ASP.NET Core a Kestrel používá protokol HTTP.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-301">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="1e2f9-302">Neexistuje žádné riziko odposloucháváním provozu mezi modulu a Kestrel z umístění mimo server.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-302">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="1e2f9-303">Párování token se používá k zajištění, že požadavků přijatých službou Kestrel byly směrovány přes proxy server službou IIS a nebyl dodán z nějakého jiného zdroje.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-303">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="1e2f9-304">Vytvoření a nastavení do proměnné prostředí párování token (`ASPNETCORE_TOKEN`) modulu.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-304">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="1e2f9-305">Párování token byl nastavený i do záhlaví (`MS-ASPNETCORE-TOKEN`) na všechny požadavky směrovány přes proxy server.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-305">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="1e2f9-306">Služba IIS Middleware kontroly požadavku že přijme potvrďte, že odpovídá párování hodnota tokenu hlavičky hodnotu proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-306">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="1e2f9-307">Pokud token hodnoty se neshodují, žádost je zaznamenána a odmítnut.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-307">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="1e2f9-308">Párování proměnná tokenu prostředí a přenos dat mezi modulu a Kestrel nejsou dostupné z umístění mimo server.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-308">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="1e2f9-309">Nainstalovat bez mého párování hodnota tokenu nelze útočník odesílat požadavky, které obejít kontrolu v IIS middlewaru.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-309">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="1e2f9-310">Modul ASP.NET Core s službu IIS sdílenou konfiguraci</span><span class="sxs-lookup"><span data-stu-id="1e2f9-310">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="1e2f9-311">Instalační služba modulu ASP.NET Core se spouští s oprávněními účtu **TrustedInstaller** .</span><span class="sxs-lookup"><span data-stu-id="1e2f9-311">The ASP.NET Core Module installer runs with the privileges of the **TrustedInstaller** account.</span></span> <span data-ttu-id="1e2f9-312">Vzhledem k tomu, že účet místního systému nemá oprávnění k úpravám pro cestu ke sdílené složce, kterou používá sdílená konfigurace služby IIS, vyvolá Instalační program chybu odepření přístupu při pokusu o konfiguraci nastavení modulu v souboru *ApplicationHost. config* v sdílená složka.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-312">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the *applicationHost.config* file on the share.</span></span>

<span data-ttu-id="1e2f9-313">Pokud používáte sdílenou konfiguraci služby IIS na stejném počítači jako instalaci služby IIS, spusťte instalační program sady prostředků ASP.NET Core s parametrem `OPT_NO_SHARED_CONFIG_CHECK` nastaveným na `1`:</span><span class="sxs-lookup"><span data-stu-id="1e2f9-313">When using an IIS Shared Configuration on the same machine as the IIS installation, run the ASP.NET Core Hosting Bundle installer with the `OPT_NO_SHARED_CONFIG_CHECK` parameter set to `1`:</span></span>

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

<span data-ttu-id="1e2f9-314">Pokud cesta ke sdílené konfiguraci není ve stejném počítači jako instalace služby IIS, postupujte podle těchto kroků:</span><span class="sxs-lookup"><span data-stu-id="1e2f9-314">When the path to the shared configuration isn't on the same machine as the IIS installation, follow these steps:</span></span>

1. <span data-ttu-id="1e2f9-315">Zakážete sdílenou konfiguraci IIS.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-315">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="1e2f9-316">Spusťte instalační program.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-316">Run the installer.</span></span>
1. <span data-ttu-id="1e2f9-317">Export aktualizovaný *applicationHost.config* souboru do sdílené složky.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-317">Export the updated *applicationHost.config* file to the share.</span></span>
1. <span data-ttu-id="1e2f9-318">Znovu povolte sdílenou konfiguraci IIS.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-318">Re-enable the IIS Shared Configuration.</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="1e2f9-319">Verze modulu a hostování sady Instalační protokoly</span><span class="sxs-lookup"><span data-stu-id="1e2f9-319">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="1e2f9-320">Chcete-li zjistit verzi nainstalovaný modul ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="1e2f9-320">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="1e2f9-321">V hostitelském systému, přejděte na *%windir%\System32\inetsrv*.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-321">On the hosting system, navigate to *%windir%\System32\inetsrv*.</span></span>
1. <span data-ttu-id="1e2f9-322">Vyhledejte *aspnetcore.dll* souboru.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-322">Locate the *aspnetcore.dll* file.</span></span>
1. <span data-ttu-id="1e2f9-323">Klikněte pravým tlačítkem na soubor a vyberte **vlastnosti** z kontextové nabídky.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-323">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="1e2f9-324">Vyberte **podrobnosti** kartu. **Verze souboru** a **verze produktu** představují nainstalovaná verze modulu.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-324">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="1e2f9-325">Instalační protokoly hostování sady prostředků modulu se nacházejí v *C:\\uživatelé\\% UserName %\\AppData\\místní\\Temp*. Soubor *dd_DotNetCoreWinSvrHosting__\<časové razítko > _000_AspNetCoreModule_x64.log*.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-325">The Hosting Bundle installer logs for the module are found at *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. The file is named *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="1e2f9-326">Modul, schéma a konfiguraci umístění souborů</span><span class="sxs-lookup"><span data-stu-id="1e2f9-326">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="1e2f9-327">Modul</span><span class="sxs-lookup"><span data-stu-id="1e2f9-327">Module</span></span>

<span data-ttu-id="1e2f9-328">**IIS (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="1e2f9-328">**IIS (x86/amd64):**</span></span>

* <span data-ttu-id="1e2f9-329">%windir%\System32\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="1e2f9-329">%windir%\System32\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="1e2f9-330">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="1e2f9-330">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="1e2f9-331">%ProgramFiles%\IIS\Asp.NET core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="1e2f9-331">%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="1e2f9-332">% ProgramFiles (x86) %\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="1e2f9-332">%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

<span data-ttu-id="1e2f9-333">**Služba IIS Express (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="1e2f9-333">**IIS Express (x86/amd64):**</span></span>

* <span data-ttu-id="1e2f9-334">%ProgramFiles%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="1e2f9-334">%ProgramFiles%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="1e2f9-335">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="1e2f9-335">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="1e2f9-336">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="1e2f9-336">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="1e2f9-337">% ProgramFiles (x86) %\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="1e2f9-337">%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

### <a name="schema"></a><span data-ttu-id="1e2f9-338">Schéma</span><span class="sxs-lookup"><span data-stu-id="1e2f9-338">Schema</span></span>

<span data-ttu-id="1e2f9-339">**SLUŽBA IIS**</span><span class="sxs-lookup"><span data-stu-id="1e2f9-339">**IIS**</span></span>

* <span data-ttu-id="1e2f9-340">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="1e2f9-340">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span></span>

* <span data-ttu-id="1e2f9-341">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.XML</span><span class="sxs-lookup"><span data-stu-id="1e2f9-341">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span></span>

<span data-ttu-id="1e2f9-342">**Služba IIS Express**</span><span class="sxs-lookup"><span data-stu-id="1e2f9-342">**IIS Express**</span></span>

* <span data-ttu-id="1e2f9-343">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="1e2f9-343">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span></span>

* <span data-ttu-id="1e2f9-344">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span><span class="sxs-lookup"><span data-stu-id="1e2f9-344">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span></span>

### <a name="configuration"></a><span data-ttu-id="1e2f9-345">Konfigurace</span><span class="sxs-lookup"><span data-stu-id="1e2f9-345">Configuration</span></span>

<span data-ttu-id="1e2f9-346">**SLUŽBA IIS**</span><span class="sxs-lookup"><span data-stu-id="1e2f9-346">**IIS**</span></span>

* <span data-ttu-id="1e2f9-347">%windir%\System32\inetsrv\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="1e2f9-347">%windir%\System32\inetsrv\config\applicationHost.config</span></span>

<span data-ttu-id="1e2f9-348">**Služba IIS Express**</span><span class="sxs-lookup"><span data-stu-id="1e2f9-348">**IIS Express**</span></span>

* <span data-ttu-id="1e2f9-349">Visual Studio: {ROOT aplikace} \\. vs\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="1e2f9-349">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span></span>

* <span data-ttu-id="1e2f9-350">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span><span class="sxs-lookup"><span data-stu-id="1e2f9-350">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span></span>

<span data-ttu-id="1e2f9-351">Soubory můžete najít tak, že *aspnetcore* v *applicationHost.config* souboru.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-351">The files can be found by searching for *aspnetcore* in the *applicationHost.config* file.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="1e2f9-352">Modul ASP.NET Core je nativním modulem služby IIS, který se připojuje k kanálu IIS na jednu z těchto:</span><span class="sxs-lookup"><span data-stu-id="1e2f9-352">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to either:</span></span>

* <span data-ttu-id="1e2f9-353">Hostování aplikace ASP.NET Core uvnitř pracovního procesu služby IIS (`w3wp.exe`) se nazývá [model hostování v rámci procesu](#in-process-hosting-model).</span><span class="sxs-lookup"><span data-stu-id="1e2f9-353">Host an ASP.NET Core app inside of the IIS worker process (`w3wp.exe`), called the [in-process hosting model](#in-process-hosting-model).</span></span>
* <span data-ttu-id="1e2f9-354">Dopředné webové požadavky do back-endu ASP.NET Core aplikace, na které běží [Server Kestrel](xref:fundamentals/servers/kestrel), se nazývá [model hostování mimo proces](#out-of-process-hosting-model).</span><span class="sxs-lookup"><span data-stu-id="1e2f9-354">Forward web requests to a backend ASP.NET Core app running the [Kestrel server](xref:fundamentals/servers/kestrel), called the [out-of-process hosting model](#out-of-process-hosting-model).</span></span>

<span data-ttu-id="1e2f9-355">Podporované verze systému Windows:</span><span class="sxs-lookup"><span data-stu-id="1e2f9-355">Supported Windows versions:</span></span>

* <span data-ttu-id="1e2f9-356">Windows 7 nebo novější</span><span class="sxs-lookup"><span data-stu-id="1e2f9-356">Windows 7 or later</span></span>
* <span data-ttu-id="1e2f9-357">Windows Server 2008 R2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="1e2f9-357">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="1e2f9-358">Při hostování v procesu používá modul implementaci vnitroprocesového serveru pro službu IIS, která se nazývá IIS HTTP Server (`IISHttpServer`).</span><span class="sxs-lookup"><span data-stu-id="1e2f9-358">When hosting in-process, the module uses an in-process server implementation for IIS, called IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="1e2f9-359">Při hostování mimo proces funguje modul pouze s Kestrel.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-359">When hosting out-of-process, the module only works with Kestrel.</span></span> <span data-ttu-id="1e2f9-360">Modul nefunguje s [ovladačem HTTP. sys](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="1e2f9-360">The module doesn't function with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

## <a name="hosting-models"></a><span data-ttu-id="1e2f9-361">Modely hostingu</span><span class="sxs-lookup"><span data-stu-id="1e2f9-361">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="1e2f9-362">Model hostování v procesu</span><span class="sxs-lookup"><span data-stu-id="1e2f9-362">In-process hosting model</span></span>

<span data-ttu-id="1e2f9-363">Chcete-li nakonfigurovat aplikaci pro hostování v rámci procesu, přidejte do souboru projektu aplikace vlastnost `<AspNetCoreHostingModel>` s hodnotou `InProcess` (hostování mimo procesy je nastavena s `OutOfProcess`):</span><span class="sxs-lookup"><span data-stu-id="1e2f9-363">To configure an app for in-process hosting, add the `<AspNetCoreHostingModel>` property to the app's project file with a value of `InProcess` (out-of-process hosting is set with `OutOfProcess`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>InProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="1e2f9-364">Model hostování v rámci procesu není podporován pro aplikace ASP.NET Core, které cílí na .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-364">The in-process hosting model isn't supported for ASP.NET Core apps that target the .NET Framework.</span></span>

<span data-ttu-id="1e2f9-365">Pokud se v souboru nenachází vlastnost `<AspNetCoreHostingModel>`, výchozí hodnota je `OutOfProcess`.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-365">If the `<AspNetCoreHostingModel>` property isn't present in the file, the default value is `OutOfProcess`.</span></span>

<span data-ttu-id="1e2f9-366">Při hostování v procesu platí následující vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="1e2f9-366">The following characteristics apply when hosting in-process:</span></span>

* <span data-ttu-id="1e2f9-367">Místo [Kestrel](xref:fundamentals/servers/kestrel) serveru se používá server HTTP služby IIS (`IISHttpServer`).</span><span class="sxs-lookup"><span data-stu-id="1e2f9-367">IIS HTTP Server (`IISHttpServer`) is used instead of [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span> <span data-ttu-id="1e2f9-368">Pro vnitroprocesové [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) volání <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> do:</span><span class="sxs-lookup"><span data-stu-id="1e2f9-368">For in-process, [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> to:</span></span>

  * <span data-ttu-id="1e2f9-369">Zaregistrujte `IISHttpServer`.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-369">Register the `IISHttpServer`.</span></span>
  * <span data-ttu-id="1e2f9-370">Nakonfigurujte port a základní cestu, na kterých má Server naslouchat při spuštění za modulem ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-370">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
  * <span data-ttu-id="1e2f9-371">Nakonfigurujte hostitele tak, aby zachytával chyby při spuštění.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-371">Configure the host to capture startup errors.</span></span>

* <span data-ttu-id="1e2f9-372">[RequestTimeout atribut](#attributes-of-the-aspnetcore-element) neplatí pro hostování v procesu.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-372">The [requestTimeout attribute](#attributes-of-the-aspnetcore-element) doesn't apply to in-process hosting.</span></span>

* <span data-ttu-id="1e2f9-373">Sdílení fondem aplikací mezi aplikacemi se nepodporuje.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-373">Sharing an app pool among apps isn't supported.</span></span> <span data-ttu-id="1e2f9-374">Použijte jeden fond aplikací na aplikaci.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-374">Use one app pool per app.</span></span>

* <span data-ttu-id="1e2f9-375">Při použití [Webdeploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) nebo ručně uvedení [soubor app_offline.htm v nasazení](xref:host-and-deploy/iis/index#locked-deployment-files), aplikace nemusí vypnout okamžitě při otevření připojení.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-375">When using [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) or manually placing an [app_offline.htm file in the deployment](xref:host-and-deploy/iis/index#locked-deployment-files), the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="1e2f9-376">Připojení soketu websocket bylo třeba může způsobit prodlevu při ukončení aplikace.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-376">For example, a websocket connection may delay app shut down.</span></span>

* <span data-ttu-id="1e2f9-377">Architektura (bitové verze) (x64 nebo x86) nainstalovaný modul runtime a aplikace musí odpovídat architektuře fondu aplikací.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-377">The architecture (bitness) of the app and installed runtime (x64 or x86) must match the architecture of the app pool.</span></span>

* <span data-ttu-id="1e2f9-378">Odpojení klienta jsou zjištěny.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-378">Client disconnects are detected.</span></span> <span data-ttu-id="1e2f9-379">[HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) token zrušení se zrušila, když se klient odpojí.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-379">The [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) cancellation token is cancelled when the client disconnects.</span></span>

* <span data-ttu-id="1e2f9-380">V ASP.NET Core 2.2.1 nebo starším <xref:System.IO.Directory.GetCurrentDirectory*> vrátí pracovní adresář procesu spuštěného službou IIS místo adresáře aplikace (například *C:\Windows\System32\inetsrv* pro *W3wp. exe*).</span><span class="sxs-lookup"><span data-stu-id="1e2f9-380">In ASP.NET Core 2.2.1 or earlier, <xref:System.IO.Directory.GetCurrentDirectory*> returns the worker directory of the process started by IIS rather than the app's directory (for example, *C:\Windows\System32\inetsrv* for *w3wp.exe*).</span></span>

  <span data-ttu-id="1e2f9-381">Ukázkový kód, který nastaví aktuální adresář aplikace, najdete v článku [CurrentDirectoryHelpers třídy](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/2.x/CurrentDirectoryHelpers.cs).</span><span class="sxs-lookup"><span data-stu-id="1e2f9-381">For sample code that sets the app's current directory, see the [CurrentDirectoryHelpers class](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/2.x/CurrentDirectoryHelpers.cs).</span></span> <span data-ttu-id="1e2f9-382">Volání `SetCurrentDirectory` metody.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-382">Call the `SetCurrentDirectory` method.</span></span> <span data-ttu-id="1e2f9-383">Následující volání <xref:System.IO.Directory.GetCurrentDirectory*> poskytují adresáře aplikace.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-383">Subsequent calls to <xref:System.IO.Directory.GetCurrentDirectory*> provide the app's directory.</span></span>

* <span data-ttu-id="1e2f9-384">Při hostování v procesu se nevolá <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> interně pro inicializaci uživatele.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-384">When hosting in-process, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span> <span data-ttu-id="1e2f9-385">Proto implementace <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> používaná k transformaci deklarací po každém ověření není ve výchozím nastavení aktivována.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-385">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="1e2f9-386">Při transformaci deklarací s implementací <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> zavolejte <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> pro přidání ověřovacích služeb:</span><span class="sxs-lookup"><span data-stu-id="1e2f9-386">When transforming claims with an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation, call <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> to add authentication services:</span></span>

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

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="1e2f9-387">Model hostování mimo proces</span><span class="sxs-lookup"><span data-stu-id="1e2f9-387">Out-of-process hosting model</span></span>

<span data-ttu-id="1e2f9-388">Chcete-li nakonfigurovat aplikaci pro hostování mimo procesy, použijte některý z následujících přístupů v souboru projektu:</span><span class="sxs-lookup"><span data-stu-id="1e2f9-388">To configure an app for out-of-process hosting, use either of the following approaches in the project file:</span></span>

* <span data-ttu-id="1e2f9-389">Nezadávejte vlastnost `<AspNetCoreHostingModel>`.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-389">Don't specify the `<AspNetCoreHostingModel>` property.</span></span> <span data-ttu-id="1e2f9-390">Pokud se v souboru nenachází vlastnost `<AspNetCoreHostingModel>`, výchozí hodnota je `OutOfProcess`.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-390">If the `<AspNetCoreHostingModel>` property isn't present in the file, the default value is `OutOfProcess`.</span></span>
* <span data-ttu-id="1e2f9-391">Nastavte hodnotu vlastnosti `<AspNetCoreHostingModel>` na `OutOfProcess` (hostování v procesu je nastaveno pomocí `InProcess`):</span><span class="sxs-lookup"><span data-stu-id="1e2f9-391">Set the value of the `<AspNetCoreHostingModel>` property to `OutOfProcess` (in-process hosting is set with `InProcess`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="1e2f9-392">Místo HTTP serveru IIS (`IISHttpServer`) se používá server [Kestrel](xref:fundamentals/servers/kestrel) .</span><span class="sxs-lookup"><span data-stu-id="1e2f9-392">[Kestrel](xref:fundamentals/servers/kestrel) server is used instead of IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="1e2f9-393">Pro mimoprocesové aplikace [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) volá <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> do:</span><span class="sxs-lookup"><span data-stu-id="1e2f9-393">For out-of-process, [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> to:</span></span>

* <span data-ttu-id="1e2f9-394">Nakonfigurujte port a základní cestu, na kterých má Server naslouchat při spuštění za modulem ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-394">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
* <span data-ttu-id="1e2f9-395">Nakonfigurujte hostitele tak, aby zachytával chyby při spuštění.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-395">Configure the host to capture startup errors.</span></span>

### <a name="hosting-model-changes"></a><span data-ttu-id="1e2f9-396">Hostování změny modelu</span><span class="sxs-lookup"><span data-stu-id="1e2f9-396">Hosting model changes</span></span>

<span data-ttu-id="1e2f9-397">Pokud `hostingModel` nastavení se změnilo v *web.config* souboru (podrobně [konfigurace pomocí souboru web.config](#configuration-with-webconfig) části), modul pro službu IIS recykluje pracovní proces.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-397">If the `hostingModel` setting is changed in the *web.config* file (explained in the [Configuration with web.config](#configuration-with-webconfig) section), the module recycles the worker process for IIS.</span></span>

<span data-ttu-id="1e2f9-398">Pro službu IIS Express modul nebude recyklovat pracovní proces, ale místo toho aktivuje řádné vypnutí aktuální proces IIS Express.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-398">For IIS Express, the module doesn't recycle the worker process but instead triggers a graceful shutdown of the current IIS Express process.</span></span> <span data-ttu-id="1e2f9-399">Další požadavek na aplikaci vytvoří nový proces IIS Express.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-399">The next request to the app spawns a new IIS Express process.</span></span>

### <a name="process-name"></a><span data-ttu-id="1e2f9-400">Název procesu</span><span class="sxs-lookup"><span data-stu-id="1e2f9-400">Process name</span></span>

<span data-ttu-id="1e2f9-401">`Process.GetCurrentProcess().ProcessName` sestavy `w3wp` / `iisexpress` (v procesu) nebo `dotnet` (out-of-process).</span><span class="sxs-lookup"><span data-stu-id="1e2f9-401">`Process.GetCurrentProcess().ProcessName` reports `w3wp`/`iisexpress` (in-process) or `dotnet` (out-of-process).</span></span>

<span data-ttu-id="1e2f9-402">Mnoho nativních modulů, jako je ověřování systému Windows, zůstane aktivní.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-402">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="1e2f9-403">Další informace o aktivních modulech služby IIS pomocí modulu ASP.NET Core najdete v tématu <xref:host-and-deploy/iis/modules>.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-403">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="1e2f9-404">Modul ASP.NET Core může také:</span><span class="sxs-lookup"><span data-stu-id="1e2f9-404">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="1e2f9-405">Nastavte proměnné prostředí pro pracovní proces.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-405">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="1e2f9-406">Protokoluje výstup stdout do úložiště souborů pro řešení problémů při spuštění.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-406">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="1e2f9-407">Předejte tokeny ověřování systému Windows.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-407">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="1e2f9-408">Jak nainstalovat a používat modul ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1e2f9-408">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="1e2f9-409">Pokyny k instalaci modulu ASP.NET Core najdete v tématu [instalace hostující sady .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span><span class="sxs-lookup"><span data-stu-id="1e2f9-409">For instructions on how to install the ASP.NET Core Module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="1e2f9-410">Konfigurace pomocí souboru web.config</span><span class="sxs-lookup"><span data-stu-id="1e2f9-410">Configuration with web.config</span></span>

<span data-ttu-id="1e2f9-411">Modul ASP.NET Core, nastavena `aspNetCore` část `system.webServer` uzlu na webu *web.config* souboru.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-411">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="1e2f9-412">Následující *web.config* soubor je publikován pro [nasazení závisí na architektuře](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) a konfiguruje modul ASP.NET Core pro zpracování požadavků lokality:</span><span class="sxs-lookup"><span data-stu-id="1e2f9-412">The following *web.config* file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

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

<span data-ttu-id="1e2f9-413">Následující *web.config* je publikována pro [samostatná nasazení](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span><span class="sxs-lookup"><span data-stu-id="1e2f9-413">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

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

<span data-ttu-id="1e2f9-414"><xref:System.Configuration.SectionInformation.InheritInChildApplications*> Je nastavena na `false` k označení, že nastavení zadané v rámci [ \<umístění >](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) element nedědí od neznámých aplikací, které jsou umístěny v podadresáři aplikace.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-414">The <xref:System.Configuration.SectionInformation.InheritInChildApplications*> property is set to `false` to indicate that the settings specified within the [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) element aren't inherited by apps that reside in a subdirectory of the app.</span></span>

<span data-ttu-id="1e2f9-415">Když je aplikace nasazená na [služby Azure App Service](https://azure.microsoft.com/services/app-service/), `stdoutLogFile` je nastavena cesta `\\?\%home%\LogFiles\stdout`.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-415">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="1e2f9-416">Protokoly stdout a uloží ji *LogFiles* složce, která je na místě automaticky vytvořené službou.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-416">The path saves stdout logs to the *LogFiles* folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="1e2f9-417">Informace o konfiguraci dílčí aplikace služby IIS, naleznete v tématu <xref:host-and-deploy/iis/index#sub-applications>.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-417">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="1e2f9-418">Atributy elementu aspNetCore</span><span class="sxs-lookup"><span data-stu-id="1e2f9-418">Attributes of the aspNetCore element</span></span>

| <span data-ttu-id="1e2f9-419">Atribut</span><span class="sxs-lookup"><span data-stu-id="1e2f9-419">Attribute</span></span> | <span data-ttu-id="1e2f9-420">Popis</span><span class="sxs-lookup"><span data-stu-id="1e2f9-420">Description</span></span> | <span data-ttu-id="1e2f9-421">Výchozí</span><span class="sxs-lookup"><span data-stu-id="1e2f9-421">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="1e2f9-422">Volitelný atribut řetězce.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-422">Optional string attribute.</span></span></p><p><span data-ttu-id="1e2f9-423">Argumenty pro spustitelný soubor určený v **processPath**.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-423">Arguments to the executable specified in **processPath**.</span></span></p> | |
| `disableStartUpErrorPage` | <p><span data-ttu-id="1e2f9-424">Volitelný logický atribut.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-424">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="1e2f9-425">Při hodnotě true **502.5 – selhání procesu** Potlačené stránky a znakovou stránku 502 stav nakonfigurované v *web.config* má přednost před.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-425">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="1e2f9-426">Volitelný logický atribut.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-426">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="1e2f9-427">Při hodnotě true se token předá podřízený proces naslouchání na ASPNETCORE_PORT % jako záhlaví "MS-ASPNETCORE-WINAUTHTOKEN" každý požadavek.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-427">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="1e2f9-428">Je odpovědností tento proces pro volání CloseHandle na tento token každý požadavek.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-428">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `hostingModel` | <p><span data-ttu-id="1e2f9-429">Volitelný atribut řetězce.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-429">Optional string attribute.</span></span></p><p><span data-ttu-id="1e2f9-430">Určuje model hostování jako v procesu (`InProcess`) nebo out-of-process (`OutOfProcess`).</span><span class="sxs-lookup"><span data-stu-id="1e2f9-430">Specifies the hosting model as in-process (`InProcess`) or out-of-process (`OutOfProcess`).</span></span></p> | `OutOfProcess` |
| `processesPerApplication` | <p><span data-ttu-id="1e2f9-431">Volitelný celočíselný atribut.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-431">Optional integer attribute.</span></span></p><p><span data-ttu-id="1e2f9-432">Určuje počet instancí procesu zadaný v **processPath** nastavení, které lze se nespředený nahoru na app.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-432">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="1e2f9-433">&dagger;Hostování v procesu, je omezena na hodnotu `1`.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-433">&dagger;For in-process hosting, the value is limited to `1`.</span></span></p><p><span data-ttu-id="1e2f9-434">Nastavení `processesPerApplication` se nedoporučuje.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-434">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="1e2f9-435">Tento atribut bude v budoucí verzi odebrán.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-435">This attribute will be removed in a future release.</span></span></p> | <span data-ttu-id="1e2f9-436">Výchozí hodnota: `1`</span><span class="sxs-lookup"><span data-stu-id="1e2f9-436">Default: `1`</span></span><br><span data-ttu-id="1e2f9-437">Min: `1`</span><span class="sxs-lookup"><span data-stu-id="1e2f9-437">Min: `1`</span></span><br><span data-ttu-id="1e2f9-438">Max: `100`&dagger;</span><span class="sxs-lookup"><span data-stu-id="1e2f9-438">Max: `100`&dagger;</span></span> |
| `processPath` | <p><span data-ttu-id="1e2f9-439">Požadovaný atribut typu string.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-439">Required string attribute.</span></span></p><p><span data-ttu-id="1e2f9-440">Cesta ke spustitelnému souboru, který spustí nějaký proces naslouchání požadavků protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-440">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="1e2f9-441">Jsou podporovány relativní cesty.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-441">Relative paths are supported.</span></span> <span data-ttu-id="1e2f9-442">Pokud cestu začíná `.`, cesta se považuje za kořeni webu.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-442">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="1e2f9-443">Volitelný celočíselný atribut.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-443">Optional integer attribute.</span></span></p><p><span data-ttu-id="1e2f9-444">Určuje, kolikrát podle procesu **processPath** může při selhání za minutu.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-444">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="1e2f9-445">Pokud je tento limit překročen, modulu zastaví spuštění procesu pro zbytek minuty.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-445">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p><p><span data-ttu-id="1e2f9-446">Není podporováno s hostitelem v procesu.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-446">Not supported with in-process hosting.</span></span></p> | <span data-ttu-id="1e2f9-447">Výchozí hodnota: `10`</span><span class="sxs-lookup"><span data-stu-id="1e2f9-447">Default: `10`</span></span><br><span data-ttu-id="1e2f9-448">Min: `0`</span><span class="sxs-lookup"><span data-stu-id="1e2f9-448">Min: `0`</span></span><br><span data-ttu-id="1e2f9-449">Max: `100`</span><span class="sxs-lookup"><span data-stu-id="1e2f9-449">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="1e2f9-450">Atribut volitelný časový interval.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-450">Optional timespan attribute.</span></span></p><p><span data-ttu-id="1e2f9-451">Určuje dobu, pro kterou modul ASP.NET Core čeká na odpověď z procesu naslouchání na ASPNETCORE_PORT %.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-451">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="1e2f9-452">Ve verzích modul ASP.NET Core dodávané s verzí technologie ASP.NET Core 2.1 nebo novější `requestTimeout` je zadán v hodiny, minuty a sekundy.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-452">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p><p><span data-ttu-id="1e2f9-453">Neplatí pro hostování v procesu.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-453">Doesn't apply to in-process hosting.</span></span> <span data-ttu-id="1e2f9-454">Hostování v procesu, čeká modul app ke zpracování požadavku.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-454">For in-process hosting, the module waits for the app to process the request.</span></span></p><p><span data-ttu-id="1e2f9-455">Platné hodnoty segmentů minut a sekund řetězce jsou v rozsahu 0-59.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-455">Valid values for minutes and seconds segments of the string are in the range 0-59.</span></span> <span data-ttu-id="1e2f9-456">Při použití hodnoty **60** v hodnotě minut nebo sekund dojde k chybě systému *500 – interní chyba serveru*.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-456">Use of **60** in the value for minutes or seconds results in a *500 - Internal Server Error*.</span></span></p> | <span data-ttu-id="1e2f9-457">Výchozí hodnota: `00:02:00`</span><span class="sxs-lookup"><span data-stu-id="1e2f9-457">Default: `00:02:00`</span></span><br><span data-ttu-id="1e2f9-458">Min: `00:00:00`</span><span class="sxs-lookup"><span data-stu-id="1e2f9-458">Min: `00:00:00`</span></span><br><span data-ttu-id="1e2f9-459">Max: `360:00:00`</span><span class="sxs-lookup"><span data-stu-id="1e2f9-459">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="1e2f9-460">Volitelný celočíselný atribut.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-460">Optional integer attribute.</span></span></p><p><span data-ttu-id="1e2f9-461">Doba trvání v sekundách, které modul čeká na spustitelný soubor na řádné vypnutí při *app_offline.htm* je detekován soubor.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-461">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="1e2f9-462">Výchozí hodnota: `10`</span><span class="sxs-lookup"><span data-stu-id="1e2f9-462">Default: `10`</span></span><br><span data-ttu-id="1e2f9-463">Min: `0`</span><span class="sxs-lookup"><span data-stu-id="1e2f9-463">Min: `0`</span></span><br><span data-ttu-id="1e2f9-464">Max: `600`</span><span class="sxs-lookup"><span data-stu-id="1e2f9-464">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="1e2f9-465">Volitelný celočíselný atribut.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-465">Optional integer attribute.</span></span></p><p><span data-ttu-id="1e2f9-466">Doba trvání v sekundách, které modul čeká na spustitelný soubor ke spuštění procesu naslouchání na portu.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-466">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="1e2f9-467">Pokud je překročen časový limit, modul ukončí proces.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-467">If this time limit is exceeded, the module kills the process.</span></span> <span data-ttu-id="1e2f9-468">Modul se pokusí znovu spustit proces, když ji dostane novou žádost a pokusit se restartovat proces na dalších příchozích požadavků, pokud se aplikaci nepodaří spustit **rapidFailsPerMinute** málokdy za posledních minuta se zajištěním provozu.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-468">The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="1e2f9-469">Hodnota 0 (nula) je **ne** za neomezený časový limit.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-469">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="1e2f9-470">Výchozí hodnota: `120`</span><span class="sxs-lookup"><span data-stu-id="1e2f9-470">Default: `120`</span></span><br><span data-ttu-id="1e2f9-471">Min: `0`</span><span class="sxs-lookup"><span data-stu-id="1e2f9-471">Min: `0`</span></span><br><span data-ttu-id="1e2f9-472">Max: `3600`</span><span class="sxs-lookup"><span data-stu-id="1e2f9-472">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="1e2f9-473">Volitelný logický atribut.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-473">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="1e2f9-474">Při hodnotě true se **stdout** a **stderr** pro proces určený v **processPath** se přesměrují do souboru zadaného v **stdoutLogFile**.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-474">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="1e2f9-475">Volitelný atribut řetězce.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-475">Optional string attribute.</span></span></p><p><span data-ttu-id="1e2f9-476">Určuje relativní nebo absolutní cestu, pro kterou **stdout** a **stderr** z procesu podle **processPath** přihlášeni.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-476">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="1e2f9-477">Relativní cesty jsou relativní vzhledem k kořen webu.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-477">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="1e2f9-478">Libovolnou cestu od `.` jsou relativní vzhledem k webu kořenové a všechny ostatní cesty jsou považovány za absolutní cesty.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-478">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="1e2f9-479">Při vytvoření souboru protokolu se v modulu vytvoří všechny složky, které jsou v cestě zadané.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-479">Any folders provided in the path are created by the module when the log file is created.</span></span> <span data-ttu-id="1e2f9-480">Pomocí oddělovače podtržítko, časové razítko, ID procesu a přípona souboru ( *.log*) jsou přidány na poslední segment **stdoutLogFile** cestu.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-480">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="1e2f9-481">Pokud `.\logs\stdout` je zadaný jako hodnota, je uložen protokolu příklad stdout jako *stdout_20180205194132_1934.log* v *protokoly* složky při uložení 5 2. 2018 v 19:41:32 s ID procesu sady 1934.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-481">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

### <a name="setting-environment-variables"></a><span data-ttu-id="1e2f9-482">Nastavení proměnných prostředí</span><span class="sxs-lookup"><span data-stu-id="1e2f9-482">Setting environment variables</span></span>

<span data-ttu-id="1e2f9-483">Proměnné prostředí se dá nastavit pro proces v `processPath` atribut.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-483">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="1e2f9-484">Zadat proměnné prostředí s `<environmentVariable>` podřízený prvek `<environmentVariables>` prvek kolekce.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-484">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span> <span data-ttu-id="1e2f9-485">Proměnné prostředí nastavené v této části přednost systémové proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-485">Environment variables set in this section take precedence over system environment variables.</span></span>

<span data-ttu-id="1e2f9-486">Následující příklad nastaví dvou proměnných prostředí.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-486">The following example sets two environment variables.</span></span> <span data-ttu-id="1e2f9-487">`ASPNETCORE_ENVIRONMENT` nakonfiguruje prostředí aplikace tak, aby `Development`.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-487">`ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="1e2f9-488">Vývojáři mohou dočasně nastaví tuto hodnotu *web.config* souboru, aby bylo možné vynutit [stránku výjimek pro vývojáře](xref:fundamentals/error-handling) načíst při ladění aplikace výjimky.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-488">A developer may temporarily set this value in the *web.config* file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="1e2f9-489">`CONFIG_DIR` je příkladem proměnné prostředí, kam má vývojář zapisovat kód, který čte hodnoty při spuštění tvoří cestu pro načtení konfiguračního souboru aplikace.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-489">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

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
> <span data-ttu-id="1e2f9-490">Alternativou k nastavení prostředí přímo v souboru *Web. config* je zahrnutí vlastnosti `<EnvironmentName>` do profilu publikování ( *. pubxml*) nebo souboru projektu.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-490">An alternative to setting the environment directly in *web.config* is to include the `<EnvironmentName>` property in the publish profile (*.pubxml*) or project file.</span></span> <span data-ttu-id="1e2f9-491">Tento přístup nastaví prostředí v *souboru Web. config* při publikování projektu:</span><span class="sxs-lookup"><span data-stu-id="1e2f9-491">This approach sets the environment in *web.config* when the project is published:</span></span>
>
> ```xml
> <PropertyGroup>
>   <EnvironmentName>Development</EnvironmentName>
> </PropertyGroup>
> ```

> [!WARNING]
> <span data-ttu-id="1e2f9-492">Nastavit pouze `ASPNETCORE_ENVIRONMENT` proměnnou prostředí, aby `Development` na přípravy a testování serverů, které nejsou dostupné k nedůvěryhodným sítím, jako je Internet.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-492">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## <a name="app_offlinehtm"></a><span data-ttu-id="1e2f9-493">app_offline.htm</span><span class="sxs-lookup"><span data-stu-id="1e2f9-493">app_offline.htm</span></span>

<span data-ttu-id="1e2f9-494">Pokud soubor s názvem *app_offline.htm* je zjištěna v kořenovém adresáři aplikace, že modul ASP.NET Core se pokusí řádné vypnutí aplikace a zastavit zpracování příchozích požadavků.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-494">If a file with the name *app_offline.htm* is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="1e2f9-495">Pokud aplikace pořád běží po dobu v sekundách podle `shutdownTimeLimit`, že modul ASP.NET Core ukončuje spuštěnému procesu.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-495">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="1e2f9-496">Zatímco *app_offline.htm* soubor je k dispozici, že modul ASP.NET Core reaguje na požadavky odesílá zpět obsah *app_offline.htm* souboru.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-496">While the *app_offline.htm* file is present, the ASP.NET Core Module responds to requests by sending back the contents of the *app_offline.htm* file.</span></span> <span data-ttu-id="1e2f9-497">Když *app_offline.htm* soubor bude odstraněn, příští žádosti o spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-497">When the *app_offline.htm* file is removed, the next request starts the app.</span></span>

<span data-ttu-id="1e2f9-498">Pokud používáte model hostingu mimo proces, aplikace nemusí vypnout okamžitě při otevření připojení.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-498">When using the out-of-process hosting model, the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="1e2f9-499">Připojení soketu websocket bylo třeba může způsobit prodlevu při ukončení aplikace.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-499">For example, a websocket connection may delay app shut down.</span></span>

## <a name="start-up-error-page"></a><span data-ttu-id="1e2f9-500">Spuštění chybovou stránku</span><span class="sxs-lookup"><span data-stu-id="1e2f9-500">Start-up error page</span></span>

<span data-ttu-id="1e2f9-501">Hostování v procesu a mimo proces vytvoření vlastní chybové stránky, když selžou a spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-501">Both in-process and out-of-process hosting produce custom error pages when they fail to start the app.</span></span>

<span data-ttu-id="1e2f9-502">Pokud se nepodaří najít buď nebo na více instancí procesu žádosti o obslužnou rutinu, že modul ASP.NET Core *500.0 – Chyba načtení obslužné rutiny v procesu/Out – proces* se zobrazí stav znakovou stránku.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-502">If the ASP.NET Core Module fails to find either the in-process or out-of-process request handler, a *500.0 - In-Process/Out-Of-Process Handler Load Failure* status code page appears.</span></span>

<span data-ttu-id="1e2f9-503">Pro hostování v procesu, pokud se nepodaří spustit aplikaci, že modul ASP.NET Core *500.30 - Start selhání* se zobrazí stav znakovou stránku.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-503">For in-process hosting if the ASP.NET Core Module fails to start the app, a *500.30 - Start Failure* status code page appears.</span></span>

<span data-ttu-id="1e2f9-504">Pro hostování mimo proces, pokud se nespustí back-endový proces nebo back-endový proces spustí ale nebude moci poslouchat na konfigurovaném portu, že modul ASP.NET Core *502.5 – selhání procesu* se zobrazí stav znakovou stránku.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-504">For out-of-process hosting if the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span>

<span data-ttu-id="1e2f9-505">Chcete-li potlačit tuto stránku a vrátit se na výchozí služby IIS 5xx stav znakovou stránku, použijte `disableStartUpErrorPage` atribut.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-505">To suppress this page and revert to the default IIS 5xx status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="1e2f9-506">Další informace o konfiguraci vlastních chybových zpráv, najdete v části [chyby protokolu HTTP \<httpErrors >](/iis/configuration/system.webServer/httpErrors/).</span><span class="sxs-lookup"><span data-stu-id="1e2f9-506">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

## <a name="log-creation-and-redirection"></a><span data-ttu-id="1e2f9-507">Vytvoření protokolu a přesměrování</span><span class="sxs-lookup"><span data-stu-id="1e2f9-507">Log creation and redirection</span></span>

<span data-ttu-id="1e2f9-508">Modul ASP.NET Core přesměruje výstup stdout a stderr console na disk, pokud `stdoutLogEnabled` a `stdoutLogFile` atributy `aspNetCore` element jsou nastavené.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-508">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="1e2f9-509">Při vytvoření souboru protokolu jsou všechny složky v cestě `stdoutLogFile` vytvářeny modulem.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-509">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="1e2f9-510">Fond aplikací musí mít oprávnění k zápisu do umístění, ve kterém jsou zapsány protokoly (použijte `IIS AppPool\<app_pool_name>` poskytnout oprávnění k zápisu).</span><span class="sxs-lookup"><span data-stu-id="1e2f9-510">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="1e2f9-511">Protokoly nejsou střídán, pokud dojde k recyklování procesů/restartování.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-511">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="1e2f9-512">Je odpovědností hostitel k omezení místa na disku, které využijete v protokolech.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-512">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="1e2f9-513">Použití protokolu stdout se doporučuje jenom pro řešení potíží při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-513">Using the stdout log is only recommended for troubleshooting app startup issues.</span></span> <span data-ttu-id="1e2f9-514">Nepoužívejte stdout protokolu pro účely protokolování obecné aplikace.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-514">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="1e2f9-515">Pro rutiny protokolování v aplikaci ASP.NET Core, použijte protokolování knihovnu, která omezuje velikost souboru protokolu a otočí protokoly.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-515">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="1e2f9-516">Další informace najdete v tématu [zprostředkovatele přihlášení třetí strany](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="1e2f9-516">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="1e2f9-517">Časové razítko a soubor rozšíření jsou přidány automaticky při vytvoření souboru protokolu.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-517">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="1e2f9-518">Název souboru protokolu se skládá připojením časové razítko, ID procesu a přípona souboru ( *.log*) na poslední segment `stdoutLogFile` cestu (obvykle *stdout*) oddělené podtržítka.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-518">The log file name is composed by appending the timestamp, process ID, and file extension (*.log*) to the last segment of the `stdoutLogFile` path (typically *stdout*) delimited by underscores.</span></span> <span data-ttu-id="1e2f9-519">Pokud `stdoutLogFile` cesta končí *stdout*, má název souboru protokolu pro aplikaci s PID 1934 vytvořili 5 2. 2018 v 19:42:32 *stdout_20180205194132_1934.log*.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-519">If the `stdoutLogFile` path ends with *stdout*, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name *stdout_20180205194132_1934.log*.</span></span>

<span data-ttu-id="1e2f9-520">Pokud `stdoutLogEnabled` má hodnotu false, chyby, ke kterým dochází při spuštění aplikace se zachytí a do protokolu událostí, protože ho až 30 KB.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-520">If `stdoutLogEnabled` is false, errors that occur on app startup are captured and emitted to the event log up to 30 KB.</span></span> <span data-ttu-id="1e2f9-521">Po spuštění se zahodí všechny další protokoly.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-521">After startup, all additional logs are discarded.</span></span>

<span data-ttu-id="1e2f9-522">Následující ukázka `aspNetCore` element konfiguruje stdout protokolování pro aplikace hostované ve službě Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-522">The following sample `aspNetCore` element configures stdout logging for an app hosted in Azure App Service.</span></span> <span data-ttu-id="1e2f9-523">Místní cesta nebo cesta ke sdílené položce je přijatelné pro místní přihlášení.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-523">A local path or network share path is acceptable for local logging.</span></span> <span data-ttu-id="1e2f9-524">Ověřte, jestli uživatel identita fondu aplikací má oprávnění k zápisu do zadaná výstupní cesta.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-524">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="InProcess">
</aspNetCore>
```

## <a name="enhanced-diagnostic-logs"></a><span data-ttu-id="1e2f9-525">Rozšířené diagnostické protokoly</span><span class="sxs-lookup"><span data-stu-id="1e2f9-525">Enhanced diagnostic logs</span></span>

<span data-ttu-id="1e2f9-526">Modul ASP.NET Core lze nakonfigurovat tak, aby poskytoval rozšířené diagnostické protokoly.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-526">The ASP.NET Core Module is configurable to provide enhanced diagnostics logs.</span></span> <span data-ttu-id="1e2f9-527">Přidat `<handlerSettings>` elementu `<aspNetCore>` prvek *web.config*. Nastavení `debugLevel` k `TRACE` zpřístupňuje větší věrnost diagnostické informace:</span><span class="sxs-lookup"><span data-stu-id="1e2f9-527">Add the `<handlerSettings>` element to the `<aspNetCore>` element in *web.config*. Setting the `debugLevel` to `TRACE` exposes a higher fidelity of diagnostic information:</span></span>

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

<span data-ttu-id="1e2f9-528">Složky v cestě zadané pro hodnotu `<handlerSetting>` (*protokoly* v předchozím příkladu) se nevytváří modulem automaticky a měly by být v nasazení předběžně existující.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-528">Folders in the path provided to the `<handlerSetting>` value (*logs* in the preceding example) aren't created by the module automatically and should pre-exist in the deployment.</span></span> <span data-ttu-id="1e2f9-529">Fond aplikací musí mít oprávnění k zápisu do umístění, ve kterém jsou zapsány protokoly (použijte `IIS AppPool\<app_pool_name>` poskytnout oprávnění k zápisu).</span><span class="sxs-lookup"><span data-stu-id="1e2f9-529">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="1e2f9-530">Ladit úrovně (`debugLevel`) hodnoty může obsahovat úroveň a umístění.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-530">Debug level (`debugLevel`) values can include both the level and the location.</span></span>

<span data-ttu-id="1e2f9-531">Úrovně (v pořadí od nejméně na nejpodrobnější):</span><span class="sxs-lookup"><span data-stu-id="1e2f9-531">Levels (in order from least to most verbose):</span></span>

* <span data-ttu-id="1e2f9-532">CHYBA</span><span class="sxs-lookup"><span data-stu-id="1e2f9-532">ERROR</span></span>
* <span data-ttu-id="1e2f9-533">UPOZORNĚNÍ</span><span class="sxs-lookup"><span data-stu-id="1e2f9-533">WARNING</span></span>
* <span data-ttu-id="1e2f9-534">INFORMACE O</span><span class="sxs-lookup"><span data-stu-id="1e2f9-534">INFO</span></span>
* <span data-ttu-id="1e2f9-535">TRACE</span><span class="sxs-lookup"><span data-stu-id="1e2f9-535">TRACE</span></span>

<span data-ttu-id="1e2f9-536">Umístění (umístění více jsou povoleny):</span><span class="sxs-lookup"><span data-stu-id="1e2f9-536">Locations (multiple locations are permitted):</span></span>

* <span data-ttu-id="1e2f9-537">KONZOLY</span><span class="sxs-lookup"><span data-stu-id="1e2f9-537">CONSOLE</span></span>
* <span data-ttu-id="1e2f9-538">PROTOKOL UDÁLOSTÍ</span><span class="sxs-lookup"><span data-stu-id="1e2f9-538">EVENTLOG</span></span>
* <span data-ttu-id="1e2f9-539">SOUBOR</span><span class="sxs-lookup"><span data-stu-id="1e2f9-539">FILE</span></span>

<span data-ttu-id="1e2f9-540">Obslužná rutina nastavení se dá zadat i prostřednictvím proměnné prostředí:</span><span class="sxs-lookup"><span data-stu-id="1e2f9-540">The handler settings can also be provided via environment variables:</span></span>

* <span data-ttu-id="1e2f9-541">`ASPNETCORE_MODULE_DEBUG_FILE` &ndash; Cesta k souboru protokolu ladění.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-541">`ASPNETCORE_MODULE_DEBUG_FILE` &ndash; Path to the debug log file.</span></span> <span data-ttu-id="1e2f9-542">(Výchozí: *aspnetcore-debug.log*)</span><span class="sxs-lookup"><span data-stu-id="1e2f9-542">(Default: *aspnetcore-debug.log*)</span></span>
* <span data-ttu-id="1e2f9-543">`ASPNETCORE_MODULE_DEBUG` &ndash; Ladit nastavení úrovně.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-543">`ASPNETCORE_MODULE_DEBUG` &ndash; Debug level setting.</span></span>

> [!WARNING]
> <span data-ttu-id="1e2f9-544">Proveďte **není** protokolování ladění povoleno v nasazení pro déle, než se požaduje chcete vyřešit nějaký problém.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-544">Do **not** leave debug logging enabled in the deployment for longer than required to troubleshoot an issue.</span></span> <span data-ttu-id="1e2f9-545">Velikost souboru protokolu není omezený.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-545">The size of the log isn't limited.</span></span> <span data-ttu-id="1e2f9-546">Opuštění protokol ladění povoleno může vyčerpat dostupné místo na disku a chybách u aplikací na serveru nebo služby app service.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-546">Leaving the debug log enabled can exhaust the available disk space and crash the server or app service.</span></span>

<span data-ttu-id="1e2f9-547">Naleznete v tématu [konfigurace pomocí souboru web.config](#configuration-with-webconfig) příklad `aspNetCore` prvek *web.config* souboru.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-547">See [Configuration with web.config](#configuration-with-webconfig) for an example of the `aspNetCore` element in the *web.config* file.</span></span>

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="1e2f9-548">Konfigurace proxy serveru používá protokol HTTP a token pro párování</span><span class="sxs-lookup"><span data-stu-id="1e2f9-548">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="1e2f9-549">*Platí jenom pro hostování mimo proces.*</span><span class="sxs-lookup"><span data-stu-id="1e2f9-549">*Only applies to out-of-process hosting.*</span></span>

<span data-ttu-id="1e2f9-550">Proxy server mezi modul ASP.NET Core a Kestrel používá protokol HTTP.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-550">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="1e2f9-551">Neexistuje žádné riziko odposloucháváním provozu mezi modulu a Kestrel z umístění mimo server.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-551">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="1e2f9-552">Párování token se používá k zajištění, že požadavků přijatých službou Kestrel byly směrovány přes proxy server službou IIS a nebyl dodán z nějakého jiného zdroje.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-552">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="1e2f9-553">Vytvoření a nastavení do proměnné prostředí párování token (`ASPNETCORE_TOKEN`) modulu.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-553">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="1e2f9-554">Párování token byl nastavený i do záhlaví (`MS-ASPNETCORE-TOKEN`) na všechny požadavky směrovány přes proxy server.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-554">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="1e2f9-555">Služba IIS Middleware kontroly požadavku že přijme potvrďte, že odpovídá párování hodnota tokenu hlavičky hodnotu proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-555">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="1e2f9-556">Pokud token hodnoty se neshodují, žádost je zaznamenána a odmítnut.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-556">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="1e2f9-557">Párování proměnná tokenu prostředí a přenos dat mezi modulu a Kestrel nejsou dostupné z umístění mimo server.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-557">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="1e2f9-558">Nainstalovat bez mého párování hodnota tokenu nelze útočník odesílat požadavky, které obejít kontrolu v IIS middlewaru.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-558">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="1e2f9-559">Modul ASP.NET Core s službu IIS sdílenou konfiguraci</span><span class="sxs-lookup"><span data-stu-id="1e2f9-559">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="1e2f9-560">Instalační služba modulu ASP.NET Core se spouští s oprávněními účtu **TrustedInstaller** .</span><span class="sxs-lookup"><span data-stu-id="1e2f9-560">The ASP.NET Core Module installer runs with the privileges of the **TrustedInstaller** account.</span></span> <span data-ttu-id="1e2f9-561">Vzhledem k tomu, že účet místního systému nemá oprávnění k úpravám pro cestu ke sdílené složce, kterou používá sdílená konfigurace služby IIS, vyvolá Instalační program chybu odepření přístupu při pokusu o konfiguraci nastavení modulu v souboru *ApplicationHost. config* v sdílená složka.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-561">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the *applicationHost.config* file on the share.</span></span>

<span data-ttu-id="1e2f9-562">Pokud používáte sdílenou konfiguraci služby IIS na stejném počítači jako instalaci služby IIS, spusťte instalační program sady prostředků ASP.NET Core s parametrem `OPT_NO_SHARED_CONFIG_CHECK` nastaveným na `1`:</span><span class="sxs-lookup"><span data-stu-id="1e2f9-562">When using an IIS Shared Configuration on the same machine as the IIS installation, run the ASP.NET Core Hosting Bundle installer with the `OPT_NO_SHARED_CONFIG_CHECK` parameter set to `1`:</span></span>

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

<span data-ttu-id="1e2f9-563">Pokud cesta ke sdílené konfiguraci není ve stejném počítači jako instalace služby IIS, postupujte podle těchto kroků:</span><span class="sxs-lookup"><span data-stu-id="1e2f9-563">When the path to the shared configuration isn't on the same machine as the IIS installation, follow these steps:</span></span>

1. <span data-ttu-id="1e2f9-564">Zakážete sdílenou konfiguraci IIS.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-564">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="1e2f9-565">Spusťte instalační program.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-565">Run the installer.</span></span>
1. <span data-ttu-id="1e2f9-566">Export aktualizovaný *applicationHost.config* souboru do sdílené složky.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-566">Export the updated *applicationHost.config* file to the share.</span></span>
1. <span data-ttu-id="1e2f9-567">Znovu povolte sdílenou konfiguraci IIS.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-567">Re-enable the IIS Shared Configuration.</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="1e2f9-568">Verze modulu a hostování sady Instalační protokoly</span><span class="sxs-lookup"><span data-stu-id="1e2f9-568">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="1e2f9-569">Chcete-li zjistit verzi nainstalovaný modul ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="1e2f9-569">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="1e2f9-570">V hostitelském systému, přejděte na *%windir%\System32\inetsrv*.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-570">On the hosting system, navigate to *%windir%\System32\inetsrv*.</span></span>
1. <span data-ttu-id="1e2f9-571">Vyhledejte *aspnetcore.dll* souboru.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-571">Locate the *aspnetcore.dll* file.</span></span>
1. <span data-ttu-id="1e2f9-572">Klikněte pravým tlačítkem na soubor a vyberte **vlastnosti** z kontextové nabídky.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-572">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="1e2f9-573">Vyberte **podrobnosti** kartu. **Verze souboru** a **verze produktu** představují nainstalovaná verze modulu.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-573">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="1e2f9-574">Instalační protokoly hostování sady prostředků modulu se nacházejí v *C:\\uživatelé\\% UserName %\\AppData\\místní\\Temp*. Soubor *dd_DotNetCoreWinSvrHosting__\<časové razítko > _000_AspNetCoreModule_x64.log*.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-574">The Hosting Bundle installer logs for the module are found at *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. The file is named *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="1e2f9-575">Modul, schéma a konfiguraci umístění souborů</span><span class="sxs-lookup"><span data-stu-id="1e2f9-575">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="1e2f9-576">Modul</span><span class="sxs-lookup"><span data-stu-id="1e2f9-576">Module</span></span>

<span data-ttu-id="1e2f9-577">**IIS (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="1e2f9-577">**IIS (x86/amd64):**</span></span>

* <span data-ttu-id="1e2f9-578">%windir%\System32\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="1e2f9-578">%windir%\System32\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="1e2f9-579">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="1e2f9-579">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="1e2f9-580">%ProgramFiles%\IIS\Asp.NET core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="1e2f9-580">%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="1e2f9-581">% ProgramFiles (x86) %\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="1e2f9-581">%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

<span data-ttu-id="1e2f9-582">**Služba IIS Express (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="1e2f9-582">**IIS Express (x86/amd64):**</span></span>

* <span data-ttu-id="1e2f9-583">%ProgramFiles%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="1e2f9-583">%ProgramFiles%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="1e2f9-584">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="1e2f9-584">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="1e2f9-585">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="1e2f9-585">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="1e2f9-586">% ProgramFiles (x86) %\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="1e2f9-586">%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

### <a name="schema"></a><span data-ttu-id="1e2f9-587">Schéma</span><span class="sxs-lookup"><span data-stu-id="1e2f9-587">Schema</span></span>

<span data-ttu-id="1e2f9-588">**SLUŽBA IIS**</span><span class="sxs-lookup"><span data-stu-id="1e2f9-588">**IIS**</span></span>

* <span data-ttu-id="1e2f9-589">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="1e2f9-589">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span></span>

* <span data-ttu-id="1e2f9-590">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.XML</span><span class="sxs-lookup"><span data-stu-id="1e2f9-590">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span></span>

<span data-ttu-id="1e2f9-591">**Služba IIS Express**</span><span class="sxs-lookup"><span data-stu-id="1e2f9-591">**IIS Express**</span></span>

* <span data-ttu-id="1e2f9-592">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="1e2f9-592">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span></span>

* <span data-ttu-id="1e2f9-593">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span><span class="sxs-lookup"><span data-stu-id="1e2f9-593">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span></span>

### <a name="configuration"></a><span data-ttu-id="1e2f9-594">Konfigurace</span><span class="sxs-lookup"><span data-stu-id="1e2f9-594">Configuration</span></span>

<span data-ttu-id="1e2f9-595">**SLUŽBA IIS**</span><span class="sxs-lookup"><span data-stu-id="1e2f9-595">**IIS**</span></span>

* <span data-ttu-id="1e2f9-596">%windir%\System32\inetsrv\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="1e2f9-596">%windir%\System32\inetsrv\config\applicationHost.config</span></span>

<span data-ttu-id="1e2f9-597">**Služba IIS Express**</span><span class="sxs-lookup"><span data-stu-id="1e2f9-597">**IIS Express**</span></span>

* <span data-ttu-id="1e2f9-598">Visual Studio: {ROOT aplikace} \\. vs\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="1e2f9-598">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span></span>

* <span data-ttu-id="1e2f9-599">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span><span class="sxs-lookup"><span data-stu-id="1e2f9-599">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span></span>

<span data-ttu-id="1e2f9-600">Soubory můžete najít tak, že *aspnetcore* v *applicationHost.config* souboru.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-600">The files can be found by searching for *aspnetcore* in the *applicationHost.config* file.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="1e2f9-601">Modul ASP.NET Core je nativním modulem služby IIS, který se připojuje k kanálu IIS a přesměrovává webové požadavky na back-endové aplikace ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-601">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to forward web requests to backend ASP.NET Core apps.</span></span>

<span data-ttu-id="1e2f9-602">Podporované verze systému Windows:</span><span class="sxs-lookup"><span data-stu-id="1e2f9-602">Supported Windows versions:</span></span>

* <span data-ttu-id="1e2f9-603">Windows 7 nebo novější</span><span class="sxs-lookup"><span data-stu-id="1e2f9-603">Windows 7 or later</span></span>
* <span data-ttu-id="1e2f9-604">Windows Server 2008 R2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="1e2f9-604">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="1e2f9-605">Modul funguje pouze s Kestrel.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-605">The module only works with Kestrel.</span></span> <span data-ttu-id="1e2f9-606">Modul není kompatibilní s [http. sys](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="1e2f9-606">The module is incompatible with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

<span data-ttu-id="1e2f9-607">Vzhledem k tomu, že ASP.NET Core aplikace běží v procesu odděleném od pracovního procesu služby IIS, modul také zpracovává správu procesů.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-607">Because ASP.NET Core apps run in a process separate from the IIS worker process, the module also handles process management.</span></span> <span data-ttu-id="1e2f9-608">Modul spustí proces pro aplikaci ASP.NET Core, když první požadavek dorazí a restartuje aplikaci, pokud dojde k chybě.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-608">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it crashes.</span></span> <span data-ttu-id="1e2f9-609">To je v podstatě stejné chování jako u aplikací ASP.NET 4. x spouštěných vnitroprocesově ve službě IIS, které spravuje [Aktivační služba procesů systému Windows (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span><span class="sxs-lookup"><span data-stu-id="1e2f9-609">This is essentially the same behavior as seen with ASP.NET 4.x apps that run in-process in IIS that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="1e2f9-610">Následující diagram znázorňuje vztah mezi službou IIS, modulem ASP.NET Core a aplikací:</span><span class="sxs-lookup"><span data-stu-id="1e2f9-610">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app:</span></span>

![Modul ASP.NET Core](aspnet-core-module/_static/ancm-outofprocess.png)

<span data-ttu-id="1e2f9-612">Požadavky přicházející z webu do ovladače HTTP. sys v režimu jádra.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-612">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="1e2f9-613">Ovladač směruje požadavky do služby IIS na konfigurovaném portu webu, obvykle 80 (HTTP) nebo 443 (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="1e2f9-613">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="1e2f9-614">Modul předá požadavky do Kestrel na náhodném portu pro aplikaci, což není port 80 nebo 443.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-614">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="1e2f9-615">Modul Určuje port prostřednictvím proměnné prostředí při spuštění a [Služba IIS Integration middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) nakonfiguruje server tak, aby naslouchal `http://localhost:{port}`.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-615">The module specifies the port via an environment variable at startup, and the [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) configures the server to listen on `http://localhost:{port}`.</span></span> <span data-ttu-id="1e2f9-616">Budou provedeny další kontroly a požadavky, které nepocházejí z modulu, jsou odmítnuty.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-616">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="1e2f9-617">Modul nepodporuje předávání HTTPS, takže požadavky se předávají přes protokol HTTP i v případě, že je služba IIS prostřednictvím protokolu HTTPS přijímá.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-617">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="1e2f9-618">Po Kestrel žádosti z modulu se požadavek odešle do kanálu middlewaru ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-618">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="1e2f9-619">Kanál middlewaru zpracovává požadavek a předá ho jako instanci `HttpContext` do logiky aplikace.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-619">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="1e2f9-620">Middleware přidaný integrací služby IIS: aktualizace schématu, vzdálené IP adresy a pathbase pro přesměrování požadavku do Kestrel.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-620">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="1e2f9-621">Odpověď aplikace se předává zpátky do služby IIS, která ji přenáší zpátky do klienta HTTP, který žádost inicioval.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-621">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="1e2f9-622">Mnoho nativních modulů, jako je ověřování systému Windows, zůstane aktivní.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-622">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="1e2f9-623">Další informace o aktivních modulech služby IIS pomocí modulu ASP.NET Core najdete v tématu <xref:host-and-deploy/iis/modules>.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-623">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="1e2f9-624">Modul ASP.NET Core může také:</span><span class="sxs-lookup"><span data-stu-id="1e2f9-624">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="1e2f9-625">Nastavte proměnné prostředí pro pracovní proces.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-625">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="1e2f9-626">Protokoluje výstup stdout do úložiště souborů pro řešení problémů při spuštění.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-626">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="1e2f9-627">Předejte tokeny ověřování systému Windows.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-627">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="1e2f9-628">Jak nainstalovat a používat modul ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1e2f9-628">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="1e2f9-629">Pokyny k instalaci modulu ASP.NET Core najdete v tématu [instalace hostující sady .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span><span class="sxs-lookup"><span data-stu-id="1e2f9-629">For instructions on how to install the ASP.NET Core Module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="1e2f9-630">Konfigurace pomocí souboru web.config</span><span class="sxs-lookup"><span data-stu-id="1e2f9-630">Configuration with web.config</span></span>

<span data-ttu-id="1e2f9-631">Modul ASP.NET Core, nastavena `aspNetCore` část `system.webServer` uzlu na webu *web.config* souboru.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-631">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="1e2f9-632">Následující *web.config* soubor je publikován pro [nasazení závisí na architektuře](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) a konfiguruje modul ASP.NET Core pro zpracování požadavků lokality:</span><span class="sxs-lookup"><span data-stu-id="1e2f9-632">The following *web.config* file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

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

<span data-ttu-id="1e2f9-633">Následující *web.config* je publikována pro [samostatná nasazení](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span><span class="sxs-lookup"><span data-stu-id="1e2f9-633">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

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

<span data-ttu-id="1e2f9-634">Když je aplikace nasazená na [služby Azure App Service](https://azure.microsoft.com/services/app-service/), `stdoutLogFile` je nastavena cesta `\\?\%home%\LogFiles\stdout`.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-634">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="1e2f9-635">Protokoly stdout a uloží ji *LogFiles* složce, která je na místě automaticky vytvořené službou.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-635">The path saves stdout logs to the *LogFiles* folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="1e2f9-636">Informace o konfiguraci dílčí aplikace služby IIS, naleznete v tématu <xref:host-and-deploy/iis/index#sub-applications>.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-636">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="1e2f9-637">Atributy elementu aspNetCore</span><span class="sxs-lookup"><span data-stu-id="1e2f9-637">Attributes of the aspNetCore element</span></span>

| <span data-ttu-id="1e2f9-638">Atribut</span><span class="sxs-lookup"><span data-stu-id="1e2f9-638">Attribute</span></span> | <span data-ttu-id="1e2f9-639">Popis</span><span class="sxs-lookup"><span data-stu-id="1e2f9-639">Description</span></span> | <span data-ttu-id="1e2f9-640">Výchozí</span><span class="sxs-lookup"><span data-stu-id="1e2f9-640">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="1e2f9-641">Volitelný atribut řetězce.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-641">Optional string attribute.</span></span></p><p><span data-ttu-id="1e2f9-642">Argumenty pro spustitelný soubor určený v **processPath**.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-642">Arguments to the executable specified in **processPath**.</span></span></p>| |
| `disableStartUpErrorPage` | <p><span data-ttu-id="1e2f9-643">Volitelný logický atribut.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-643">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="1e2f9-644">Při hodnotě true **502.5 – selhání procesu** Potlačené stránky a znakovou stránku 502 stav nakonfigurované v *web.config* má přednost před.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-644">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="1e2f9-645">Volitelný logický atribut.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-645">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="1e2f9-646">Při hodnotě true se token předá podřízený proces naslouchání na ASPNETCORE_PORT % jako záhlaví "MS-ASPNETCORE-WINAUTHTOKEN" každý požadavek.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-646">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="1e2f9-647">Je odpovědností tento proces pro volání CloseHandle na tento token každý požadavek.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-647">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `processesPerApplication` | <p><span data-ttu-id="1e2f9-648">Volitelný celočíselný atribut.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-648">Optional integer attribute.</span></span></p><p><span data-ttu-id="1e2f9-649">Určuje počet instancí procesu zadaný v **processPath** nastavení, které lze se nespředený nahoru na app.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-649">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="1e2f9-650">Nastavení `processesPerApplication` se nedoporučuje.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-650">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="1e2f9-651">Tento atribut bude v budoucí verzi odebrán.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-651">This attribute will be removed in a future release.</span></span></p> | <span data-ttu-id="1e2f9-652">Výchozí hodnota: `1`</span><span class="sxs-lookup"><span data-stu-id="1e2f9-652">Default: `1`</span></span><br><span data-ttu-id="1e2f9-653">Min: `1`</span><span class="sxs-lookup"><span data-stu-id="1e2f9-653">Min: `1`</span></span><br><span data-ttu-id="1e2f9-654">Max: `100`</span><span class="sxs-lookup"><span data-stu-id="1e2f9-654">Max: `100`</span></span> |
| `processPath` | <p><span data-ttu-id="1e2f9-655">Požadovaný atribut typu string.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-655">Required string attribute.</span></span></p><p><span data-ttu-id="1e2f9-656">Cesta ke spustitelnému souboru, který spustí nějaký proces naslouchání požadavků protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-656">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="1e2f9-657">Jsou podporovány relativní cesty.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-657">Relative paths are supported.</span></span> <span data-ttu-id="1e2f9-658">Pokud cestu začíná `.`, cesta se považuje za kořeni webu.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-658">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="1e2f9-659">Volitelný celočíselný atribut.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-659">Optional integer attribute.</span></span></p><p><span data-ttu-id="1e2f9-660">Určuje, kolikrát podle procesu **processPath** může při selhání za minutu.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-660">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="1e2f9-661">Pokud je tento limit překročen, modulu zastaví spuštění procesu pro zbytek minuty.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-661">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p> | <span data-ttu-id="1e2f9-662">Výchozí hodnota: `10`</span><span class="sxs-lookup"><span data-stu-id="1e2f9-662">Default: `10`</span></span><br><span data-ttu-id="1e2f9-663">Min: `0`</span><span class="sxs-lookup"><span data-stu-id="1e2f9-663">Min: `0`</span></span><br><span data-ttu-id="1e2f9-664">Max: `100`</span><span class="sxs-lookup"><span data-stu-id="1e2f9-664">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="1e2f9-665">Atribut volitelný časový interval.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-665">Optional timespan attribute.</span></span></p><p><span data-ttu-id="1e2f9-666">Určuje dobu, pro kterou modul ASP.NET Core čeká na odpověď z procesu naslouchání na ASPNETCORE_PORT %.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-666">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="1e2f9-667">Ve verzích modul ASP.NET Core dodávané s verzí technologie ASP.NET Core 2.1 nebo novější `requestTimeout` je zadán v hodiny, minuty a sekundy.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-667">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p> | <span data-ttu-id="1e2f9-668">Výchozí hodnota: `00:02:00`</span><span class="sxs-lookup"><span data-stu-id="1e2f9-668">Default: `00:02:00`</span></span><br><span data-ttu-id="1e2f9-669">Min: `00:00:00`</span><span class="sxs-lookup"><span data-stu-id="1e2f9-669">Min: `00:00:00`</span></span><br><span data-ttu-id="1e2f9-670">Max: `360:00:00`</span><span class="sxs-lookup"><span data-stu-id="1e2f9-670">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="1e2f9-671">Volitelný celočíselný atribut.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-671">Optional integer attribute.</span></span></p><p><span data-ttu-id="1e2f9-672">Doba trvání v sekundách, které modul čeká na spustitelný soubor na řádné vypnutí při *app_offline.htm* je detekován soubor.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-672">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="1e2f9-673">Výchozí hodnota: `10`</span><span class="sxs-lookup"><span data-stu-id="1e2f9-673">Default: `10`</span></span><br><span data-ttu-id="1e2f9-674">Min: `0`</span><span class="sxs-lookup"><span data-stu-id="1e2f9-674">Min: `0`</span></span><br><span data-ttu-id="1e2f9-675">Max: `600`</span><span class="sxs-lookup"><span data-stu-id="1e2f9-675">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="1e2f9-676">Volitelný celočíselný atribut.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-676">Optional integer attribute.</span></span></p><p><span data-ttu-id="1e2f9-677">Doba trvání v sekundách, které modul čeká na spustitelný soubor ke spuštění procesu naslouchání na portu.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-677">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="1e2f9-678">Pokud je překročen časový limit, modul ukončí proces.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-678">If this time limit is exceeded, the module kills the process.</span></span> <span data-ttu-id="1e2f9-679">Modul se pokusí znovu spustit proces, když ji dostane novou žádost a pokusit se restartovat proces na dalších příchozích požadavků, pokud se aplikaci nepodaří spustit **rapidFailsPerMinute** málokdy za posledních minuta se zajištěním provozu.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-679">The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="1e2f9-680">Hodnota 0 (nula) je **ne** za neomezený časový limit.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-680">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="1e2f9-681">Výchozí hodnota: `120`</span><span class="sxs-lookup"><span data-stu-id="1e2f9-681">Default: `120`</span></span><br><span data-ttu-id="1e2f9-682">Min: `0`</span><span class="sxs-lookup"><span data-stu-id="1e2f9-682">Min: `0`</span></span><br><span data-ttu-id="1e2f9-683">Max: `3600`</span><span class="sxs-lookup"><span data-stu-id="1e2f9-683">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="1e2f9-684">Volitelný logický atribut.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-684">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="1e2f9-685">Při hodnotě true se **stdout** a **stderr** pro proces určený v **processPath** se přesměrují do souboru zadaného v **stdoutLogFile**.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-685">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="1e2f9-686">Volitelný atribut řetězce.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-686">Optional string attribute.</span></span></p><p><span data-ttu-id="1e2f9-687">Určuje relativní nebo absolutní cestu, pro kterou **stdout** a **stderr** z procesu podle **processPath** přihlášeni.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-687">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="1e2f9-688">Relativní cesty jsou relativní vzhledem k kořen webu.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-688">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="1e2f9-689">Libovolnou cestu od `.` jsou relativní vzhledem k webu kořenové a všechny ostatní cesty jsou považovány za absolutní cesty.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-689">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="1e2f9-690">Všechny složky v cestě k dispozici musí existovat v pořadí pro modul pro vytvoření souboru protokolu.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-690">Any folders provided in the path must exist in order for the module to create the log file.</span></span> <span data-ttu-id="1e2f9-691">Pomocí oddělovače podtržítko, časové razítko, ID procesu a přípona souboru ( *.log*) jsou přidány na poslední segment **stdoutLogFile** cestu.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-691">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="1e2f9-692">Pokud `.\logs\stdout` je zadaný jako hodnota, je uložen protokolu příklad stdout jako *stdout_20180205194132_1934.log* v *protokoly* složky při uložení 5 2. 2018 v 19:41:32 s ID procesu sady 1934.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-692">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

### <a name="setting-environment-variables"></a><span data-ttu-id="1e2f9-693">Nastavení proměnných prostředí</span><span class="sxs-lookup"><span data-stu-id="1e2f9-693">Setting environment variables</span></span>

<span data-ttu-id="1e2f9-694">Proměnné prostředí se dá nastavit pro proces v `processPath` atribut.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-694">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="1e2f9-695">Zadat proměnné prostředí s `<environmentVariable>` podřízený prvek `<environmentVariables>` prvek kolekce.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-695">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span>

> [!WARNING]
> <span data-ttu-id="1e2f9-696">Proměnné prostředí nastavené v této části jsou v konfliktu se sadou proměnných prostředí systému se stejným názvem.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-696">Environment variables set in this section conflict with system environment variables set with the same name.</span></span> <span data-ttu-id="1e2f9-697">Pokud je proměnná prostředí nastavena v souboru *Web. config* i na úrovni systému systému Windows, bude hodnota ze souboru *Web. config* připojena k hodnotě proměnné prostředí systému (například `ASPNETCORE_ENVIRONMENT: Development;Development`), která brání aplikaci. začátek.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-697">If an environment variable is set in both the *web.config* file and at the system level in Windows, the value from the *web.config* file becomes appended to the system environment variable value (for example, `ASPNETCORE_ENVIRONMENT: Development;Development`), which prevents the app from starting.</span></span>

<span data-ttu-id="1e2f9-698">Následující příklad nastaví dvou proměnných prostředí.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-698">The following example sets two environment variables.</span></span> <span data-ttu-id="1e2f9-699">`ASPNETCORE_ENVIRONMENT` nakonfiguruje prostředí aplikace tak, aby `Development`.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-699">`ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="1e2f9-700">Vývojáři mohou dočasně nastaví tuto hodnotu *web.config* souboru, aby bylo možné vynutit [stránku výjimek pro vývojáře](xref:fundamentals/error-handling) načíst při ladění aplikace výjimky.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-700">A developer may temporarily set this value in the *web.config* file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="1e2f9-701">`CONFIG_DIR` je příkladem proměnné prostředí, kam má vývojář zapisovat kód, který čte hodnoty při spuštění tvoří cestu pro načtení konfiguračního souboru aplikace.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-701">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

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
> <span data-ttu-id="1e2f9-702">Nastavit pouze `ASPNETCORE_ENVIRONMENT` proměnnou prostředí, aby `Development` na přípravy a testování serverů, které nejsou dostupné k nedůvěryhodným sítím, jako je Internet.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-702">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## <a name="app_offlinehtm"></a><span data-ttu-id="1e2f9-703">app_offline.htm</span><span class="sxs-lookup"><span data-stu-id="1e2f9-703">app_offline.htm</span></span>

<span data-ttu-id="1e2f9-704">Pokud soubor s názvem *app_offline.htm* je zjištěna v kořenovém adresáři aplikace, že modul ASP.NET Core se pokusí řádné vypnutí aplikace a zastavit zpracování příchozích požadavků.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-704">If a file with the name *app_offline.htm* is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="1e2f9-705">Pokud aplikace pořád běží po dobu v sekundách podle `shutdownTimeLimit`, že modul ASP.NET Core ukončuje spuštěnému procesu.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-705">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="1e2f9-706">Zatímco *app_offline.htm* soubor je k dispozici, že modul ASP.NET Core reaguje na požadavky odesílá zpět obsah *app_offline.htm* souboru.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-706">While the *app_offline.htm* file is present, the ASP.NET Core Module responds to requests by sending back the contents of the *app_offline.htm* file.</span></span> <span data-ttu-id="1e2f9-707">Když *app_offline.htm* soubor bude odstraněn, příští žádosti o spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-707">When the *app_offline.htm* file is removed, the next request starts the app.</span></span>

## <a name="start-up-error-page"></a><span data-ttu-id="1e2f9-708">Spuštění chybovou stránku</span><span class="sxs-lookup"><span data-stu-id="1e2f9-708">Start-up error page</span></span>

<span data-ttu-id="1e2f9-709">Pokud se nespustí back-endový proces nebo back-endový proces spustí ale nebude moci poslouchat na konfigurovaném portu, že modul ASP.NET Core *502.5 – selhání procesu* se zobrazí stav znakovou stránku.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-709">If the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span> <span data-ttu-id="1e2f9-710">Chcete-li potlačit tuto stránku a vrátit se na výchozí stav služby IIS 502 znakovou stránku, použijte `disableStartUpErrorPage` atribut.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-710">To suppress this page and revert to the default IIS 502 status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="1e2f9-711">Další informace o konfiguraci vlastních chybových zpráv, najdete v části [chyby protokolu HTTP \<httpErrors >](/iis/configuration/system.webServer/httpErrors/).</span><span class="sxs-lookup"><span data-stu-id="1e2f9-711">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

![502.5 proces selhání stav znaková stránka](aspnet-core-module/_static/ANCM-502_5.png)

## <a name="log-creation-and-redirection"></a><span data-ttu-id="1e2f9-713">Vytvoření protokolu a přesměrování</span><span class="sxs-lookup"><span data-stu-id="1e2f9-713">Log creation and redirection</span></span>

<span data-ttu-id="1e2f9-714">Modul ASP.NET Core přesměruje výstup stdout a stderr console na disk, pokud `stdoutLogEnabled` a `stdoutLogFile` atributy `aspNetCore` element jsou nastavené.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-714">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="1e2f9-715">Při vytvoření souboru protokolu jsou všechny složky v cestě `stdoutLogFile` vytvářeny modulem.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-715">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="1e2f9-716">Fond aplikací musí mít oprávnění k zápisu do umístění, ve kterém jsou zapsány protokoly (použijte `IIS AppPool\<app_pool_name>` poskytnout oprávnění k zápisu).</span><span class="sxs-lookup"><span data-stu-id="1e2f9-716">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="1e2f9-717">Protokoly nejsou střídán, pokud dojde k recyklování procesů/restartování.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-717">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="1e2f9-718">Je odpovědností hostitel k omezení místa na disku, které využijete v protokolech.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-718">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="1e2f9-719">Použití protokolu stdout se doporučuje jenom pro řešení potíží při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-719">Using the stdout log is only recommended for troubleshooting app startup issues.</span></span> <span data-ttu-id="1e2f9-720">Nepoužívejte stdout protokolu pro účely protokolování obecné aplikace.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-720">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="1e2f9-721">Pro rutiny protokolování v aplikaci ASP.NET Core, použijte protokolování knihovnu, která omezuje velikost souboru protokolu a otočí protokoly.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-721">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="1e2f9-722">Další informace najdete v tématu [zprostředkovatele přihlášení třetí strany](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="1e2f9-722">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="1e2f9-723">Časové razítko a soubor rozšíření jsou přidány automaticky při vytvoření souboru protokolu.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-723">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="1e2f9-724">Název souboru protokolu se skládá připojením časové razítko, ID procesu a přípona souboru ( *.log*) na poslední segment `stdoutLogFile` cestu (obvykle *stdout*) oddělené podtržítka.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-724">The log file name is composed by appending the timestamp, process ID, and file extension (*.log*) to the last segment of the `stdoutLogFile` path (typically *stdout*) delimited by underscores.</span></span> <span data-ttu-id="1e2f9-725">Pokud `stdoutLogFile` cesta končí *stdout*, má název souboru protokolu pro aplikaci s PID 1934 vytvořili 5 2. 2018 v 19:42:32 *stdout_20180205194132_1934.log*.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-725">If the `stdoutLogFile` path ends with *stdout*, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name *stdout_20180205194132_1934.log*.</span></span>

<span data-ttu-id="1e2f9-726">Následující ukázka `aspNetCore` element konfiguruje stdout protokolování pro aplikace hostované ve službě Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-726">The following sample `aspNetCore` element configures stdout logging for an app hosted in Azure App Service.</span></span> <span data-ttu-id="1e2f9-727">Místní cesta nebo cesta ke sdílené položce je přijatelné pro místní přihlášení.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-727">A local path or network share path is acceptable for local logging.</span></span> <span data-ttu-id="1e2f9-728">Ověřte, jestli uživatel identita fondu aplikací má oprávnění k zápisu do zadaná výstupní cesta.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-728">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile="\\?\%home%\LogFiles\stdout">
</aspNetCore>
```

<span data-ttu-id="1e2f9-729">Složky v cestě zadané pro hodnotu `<handlerSetting>` (*protokoly* v předchozím příkladu) se nevytváří modulem automaticky a měly by být v nasazení předběžně existující.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-729">Folders in the path provided to the `<handlerSetting>` value (*logs* in the preceding example) aren't created by the module automatically and should pre-exist in the deployment.</span></span> <span data-ttu-id="1e2f9-730">Fond aplikací musí mít oprávnění k zápisu do umístění, ve kterém jsou zapsány protokoly (použijte `IIS AppPool\<app_pool_name>` poskytnout oprávnění k zápisu).</span><span class="sxs-lookup"><span data-stu-id="1e2f9-730">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="1e2f9-731">Naleznete v tématu [konfigurace pomocí souboru web.config](#configuration-with-webconfig) příklad `aspNetCore` prvek *web.config* souboru.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-731">See [Configuration with web.config](#configuration-with-webconfig) for an example of the `aspNetCore` element in the *web.config* file.</span></span>

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="1e2f9-732">Konfigurace proxy serveru používá protokol HTTP a token pro párování</span><span class="sxs-lookup"><span data-stu-id="1e2f9-732">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="1e2f9-733">Proxy server mezi modul ASP.NET Core a Kestrel používá protokol HTTP.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-733">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="1e2f9-734">Neexistuje žádné riziko odposloucháváním provozu mezi modulu a Kestrel z umístění mimo server.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-734">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="1e2f9-735">Párování token se používá k zajištění, že požadavků přijatých službou Kestrel byly směrovány přes proxy server službou IIS a nebyl dodán z nějakého jiného zdroje.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-735">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="1e2f9-736">Vytvoření a nastavení do proměnné prostředí párování token (`ASPNETCORE_TOKEN`) modulu.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-736">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="1e2f9-737">Párování token byl nastavený i do záhlaví (`MS-ASPNETCORE-TOKEN`) na všechny požadavky směrovány přes proxy server.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-737">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="1e2f9-738">Služba IIS Middleware kontroly požadavku že přijme potvrďte, že odpovídá párování hodnota tokenu hlavičky hodnotu proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-738">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="1e2f9-739">Pokud token hodnoty se neshodují, žádost je zaznamenána a odmítnut.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-739">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="1e2f9-740">Párování proměnná tokenu prostředí a přenos dat mezi modulu a Kestrel nejsou dostupné z umístění mimo server.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-740">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="1e2f9-741">Nainstalovat bez mého párování hodnota tokenu nelze útočník odesílat požadavky, které obejít kontrolu v IIS middlewaru.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-741">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="1e2f9-742">Modul ASP.NET Core s službu IIS sdílenou konfiguraci</span><span class="sxs-lookup"><span data-stu-id="1e2f9-742">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="1e2f9-743">Instalační služba modulu ASP.NET Core se spouští s oprávněními účtu **TrustedInstaller** .</span><span class="sxs-lookup"><span data-stu-id="1e2f9-743">The ASP.NET Core Module installer runs with the privileges of the **TrustedInstaller** account.</span></span> <span data-ttu-id="1e2f9-744">Vzhledem k tomu, že účet místního systému nemá oprávnění k úpravám pro cestu ke sdílené složce, kterou používá sdílená konfigurace služby IIS, vyvolá Instalační program chybu odepření přístupu při pokusu o konfiguraci nastavení modulu v souboru *ApplicationHost. config* v sdílená složka.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-744">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the *applicationHost.config* file on the share.</span></span>

<span data-ttu-id="1e2f9-745">Pokud používáte sdílenou konfiguraci IIS, postupujte podle těchto kroků:</span><span class="sxs-lookup"><span data-stu-id="1e2f9-745">When using an IIS Shared Configuration, follow these steps:</span></span>

1. <span data-ttu-id="1e2f9-746">Zakážete sdílenou konfiguraci IIS.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-746">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="1e2f9-747">Spusťte instalační program.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-747">Run the installer.</span></span>
1. <span data-ttu-id="1e2f9-748">Export aktualizovaný *applicationHost.config* souboru do sdílené složky.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-748">Export the updated *applicationHost.config* file to the share.</span></span>
1. <span data-ttu-id="1e2f9-749">Znovu povolte sdílenou konfiguraci IIS.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-749">Re-enable the IIS Shared Configuration.</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="1e2f9-750">Verze modulu a hostování sady Instalační protokoly</span><span class="sxs-lookup"><span data-stu-id="1e2f9-750">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="1e2f9-751">Chcete-li zjistit verzi nainstalovaný modul ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="1e2f9-751">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="1e2f9-752">V hostitelském systému, přejděte na *%windir%\System32\inetsrv*.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-752">On the hosting system, navigate to *%windir%\System32\inetsrv*.</span></span>
1. <span data-ttu-id="1e2f9-753">Vyhledejte *aspnetcore.dll* souboru.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-753">Locate the *aspnetcore.dll* file.</span></span>
1. <span data-ttu-id="1e2f9-754">Klikněte pravým tlačítkem na soubor a vyberte **vlastnosti** z kontextové nabídky.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-754">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="1e2f9-755">Vyberte **podrobnosti** kartu. **Verze souboru** a **verze produktu** představují nainstalovaná verze modulu.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-755">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="1e2f9-756">Instalační protokoly hostování sady prostředků modulu se nacházejí v *C:\\uživatelé\\% UserName %\\AppData\\místní\\Temp*. Soubor *dd_DotNetCoreWinSvrHosting__\<časové razítko > _000_AspNetCoreModule_x64.log*.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-756">The Hosting Bundle installer logs for the module are found at *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. The file is named *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="1e2f9-757">Modul, schéma a konfiguraci umístění souborů</span><span class="sxs-lookup"><span data-stu-id="1e2f9-757">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="1e2f9-758">Modul</span><span class="sxs-lookup"><span data-stu-id="1e2f9-758">Module</span></span>

<span data-ttu-id="1e2f9-759">**IIS (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="1e2f9-759">**IIS (x86/amd64):**</span></span>

* <span data-ttu-id="1e2f9-760">%windir%\System32\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="1e2f9-760">%windir%\System32\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="1e2f9-761">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="1e2f9-761">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span></span>

<span data-ttu-id="1e2f9-762">**Služba IIS Express (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="1e2f9-762">**IIS Express (x86/amd64):**</span></span>

* <span data-ttu-id="1e2f9-763">%ProgramFiles%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="1e2f9-763">%ProgramFiles%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="1e2f9-764">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="1e2f9-764">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span></span>

### <a name="schema"></a><span data-ttu-id="1e2f9-765">Schéma</span><span class="sxs-lookup"><span data-stu-id="1e2f9-765">Schema</span></span>

<span data-ttu-id="1e2f9-766">**SLUŽBA IIS**</span><span class="sxs-lookup"><span data-stu-id="1e2f9-766">**IIS**</span></span>

* <span data-ttu-id="1e2f9-767">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="1e2f9-767">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span></span>

<span data-ttu-id="1e2f9-768">**Služba IIS Express**</span><span class="sxs-lookup"><span data-stu-id="1e2f9-768">**IIS Express**</span></span>

* <span data-ttu-id="1e2f9-769">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="1e2f9-769">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span></span>

### <a name="configuration"></a><span data-ttu-id="1e2f9-770">Konfiguraci</span><span class="sxs-lookup"><span data-stu-id="1e2f9-770">Configuration</span></span>

<span data-ttu-id="1e2f9-771">**SLUŽBA IIS**</span><span class="sxs-lookup"><span data-stu-id="1e2f9-771">**IIS**</span></span>

* <span data-ttu-id="1e2f9-772">%windir%\System32\inetsrv\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="1e2f9-772">%windir%\System32\inetsrv\config\applicationHost.config</span></span>

<span data-ttu-id="1e2f9-773">**Služba IIS Express**</span><span class="sxs-lookup"><span data-stu-id="1e2f9-773">**IIS Express**</span></span>

* <span data-ttu-id="1e2f9-774">Visual Studio: {ROOT aplikace} \\. vs\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="1e2f9-774">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span></span>

* <span data-ttu-id="1e2f9-775">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span><span class="sxs-lookup"><span data-stu-id="1e2f9-775">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span></span>

<span data-ttu-id="1e2f9-776">Soubory můžete najít tak, že *aspnetcore* v *applicationHost.config* souboru.</span><span class="sxs-lookup"><span data-stu-id="1e2f9-776">The files can be found by searching for *aspnetcore* in the *applicationHost.config* file.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="1e2f9-777">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="1e2f9-777">Additional resources</span></span>

* <xref:host-and-deploy/iis/index>
* [<span data-ttu-id="1e2f9-778">Úložiště GitHub modulu ASP.NET Core (odkazový zdroj)</span><span class="sxs-lookup"><span data-stu-id="1e2f9-778">ASP.NET Core Module GitHub repository (reference source)</span></span>](https://github.com/aspnet/AspNetCoreModule)
* <xref:host-and-deploy/iis/modules>
