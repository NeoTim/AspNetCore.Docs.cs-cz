---
title: Modul ASP.NET Core
author: rick-anderson
description: Zjistěte, jak nakonfigurovat modul ASP.NET Core pro hostování aplikací ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2020
uid: host-and-deploy/aspnet-core-module
ms.openlocfilehash: 298d424557600735668217e1ef07ace606dac60b
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78667298"
---
# <a name="aspnet-core-module"></a><span data-ttu-id="cd367-103">Modul ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="cd367-103">ASP.NET Core Module</span></span>

<span data-ttu-id="cd367-104">Díky [Dykstra](https://github.com/tdykstra), [Rick Strahl](https://github.com/RickStrahl), [Novák Rossův](https://github.com/Tratcher), [Rick Anderson](https://twitter.com/RickAndMSFT), [sourabh Shirhatti](https://twitter.com/sshirhatti)a [Justin](https://github.com/jkotalik) Kotalik</span><span class="sxs-lookup"><span data-stu-id="cd367-104">By [Tom Dykstra](https://github.com/tdykstra), [Rick Strahl](https://github.com/RickStrahl), [Chris Ross](https://github.com/Tratcher), [Rick Anderson](https://twitter.com/RickAndMSFT), [Sourabh Shirhatti](https://twitter.com/sshirhatti), and [Justin Kotalik](https://github.com/jkotalik)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="cd367-105">Modul ASP.NET Core je nativním modulem služby IIS, který se připojuje k kanálu IIS na jednu z těchto:</span><span class="sxs-lookup"><span data-stu-id="cd367-105">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to either:</span></span>

* <span data-ttu-id="cd367-106">Hostování aplikace ASP.NET Core uvnitř pracovního procesu služby IIS (`w3wp.exe`), který se nazývá [model hostování v rámci procesu](#in-process-hosting-model).</span><span class="sxs-lookup"><span data-stu-id="cd367-106">Host an ASP.NET Core app inside of the IIS worker process (`w3wp.exe`), called the [in-process hosting model](#in-process-hosting-model).</span></span>
* <span data-ttu-id="cd367-107">Dopředné webové požadavky do back-endu ASP.NET Core aplikace, na které běží [Server Kestrel](xref:fundamentals/servers/kestrel), se nazývá [model hostování mimo proces](#out-of-process-hosting-model).</span><span class="sxs-lookup"><span data-stu-id="cd367-107">Forward web requests to a backend ASP.NET Core app running the [Kestrel server](xref:fundamentals/servers/kestrel), called the [out-of-process hosting model](#out-of-process-hosting-model).</span></span>

<span data-ttu-id="cd367-108">Podporované verze systému Windows:</span><span class="sxs-lookup"><span data-stu-id="cd367-108">Supported Windows versions:</span></span>

* <span data-ttu-id="cd367-109">Windows 7 nebo novější</span><span class="sxs-lookup"><span data-stu-id="cd367-109">Windows 7 or later</span></span>
* <span data-ttu-id="cd367-110">Windows Server 2008 R2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="cd367-110">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="cd367-111">Při hostování v procesu používá modul implementaci v rámci procesového serveru pro službu IIS, která se nazývá IIS HTTP Server (`IISHttpServer`).</span><span class="sxs-lookup"><span data-stu-id="cd367-111">When hosting in-process, the module uses an in-process server implementation for IIS, called IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="cd367-112">Při hostování mimo proces funguje modul pouze s Kestrel.</span><span class="sxs-lookup"><span data-stu-id="cd367-112">When hosting out-of-process, the module only works with Kestrel.</span></span> <span data-ttu-id="cd367-113">Modul nefunguje s [ovladačem HTTP. sys](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="cd367-113">The module doesn't function with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

## <a name="hosting-models"></a><span data-ttu-id="cd367-114">Modely hostingu</span><span class="sxs-lookup"><span data-stu-id="cd367-114">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="cd367-115">Model hostování v procesu</span><span class="sxs-lookup"><span data-stu-id="cd367-115">In-process hosting model</span></span>

<span data-ttu-id="cd367-116">ASP.NET Core aplikace jako výchozí pro model hostování v rámci procesu.</span><span class="sxs-lookup"><span data-stu-id="cd367-116">ASP.NET Core apps default to the in-process hosting model.</span></span>

<span data-ttu-id="cd367-117">Při hostování v procesu platí následující vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="cd367-117">The following characteristics apply when hosting in-process:</span></span>

* <span data-ttu-id="cd367-118">Místo [Kestrel](xref:fundamentals/servers/kestrel) serveru se používá server HTTP služby IIS (`IISHttpServer`).</span><span class="sxs-lookup"><span data-stu-id="cd367-118">IIS HTTP Server (`IISHttpServer`) is used instead of [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span> <span data-ttu-id="cd367-119">Pro vnitroprocesové [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) volání <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*>:</span><span class="sxs-lookup"><span data-stu-id="cd367-119">For in-process, [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> to:</span></span>

  * <span data-ttu-id="cd367-120">Zaregistrujte `IISHttpServer`.</span><span class="sxs-lookup"><span data-stu-id="cd367-120">Register the `IISHttpServer`.</span></span>
  * <span data-ttu-id="cd367-121">Nakonfigurujte port a základní cestu, na kterých má Server naslouchat při spuštění za modulem ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="cd367-121">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
  * <span data-ttu-id="cd367-122">Nakonfigurujte hostitele tak, aby zachytával chyby při spuštění.</span><span class="sxs-lookup"><span data-stu-id="cd367-122">Configure the host to capture startup errors.</span></span>

* <span data-ttu-id="cd367-123">[Atribut RequestTimeout](#attributes-of-the-aspnetcore-element) se nevztahuje na hostování v rámci procesu.</span><span class="sxs-lookup"><span data-stu-id="cd367-123">The [requestTimeout attribute](#attributes-of-the-aspnetcore-element) doesn't apply to in-process hosting.</span></span>

* <span data-ttu-id="cd367-124">Sdílení fondem aplikací mezi aplikacemi se nepodporuje.</span><span class="sxs-lookup"><span data-stu-id="cd367-124">Sharing an app pool among apps isn't supported.</span></span> <span data-ttu-id="cd367-125">Použijte jeden fond aplikací na aplikaci.</span><span class="sxs-lookup"><span data-stu-id="cd367-125">Use one app pool per app.</span></span>

* <span data-ttu-id="cd367-126">Při použití [nasazení webu](/iis/publish/using-web-deploy/introduction-to-web-deploy) nebo ručního umístění [souboru App_offline. htm v nasazení](xref:host-and-deploy/iis/index#locked-deployment-files)se aplikace nemusí vypnout okamžitě, pokud dojde k otevřenému připojení.</span><span class="sxs-lookup"><span data-stu-id="cd367-126">When using [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) or manually placing an [app_offline.htm file in the deployment](xref:host-and-deploy/iis/index#locked-deployment-files), the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="cd367-127">Připojení soketu websocket bylo třeba může způsobit prodlevu při ukončení aplikace.</span><span class="sxs-lookup"><span data-stu-id="cd367-127">For example, a websocket connection may delay app shut down.</span></span>

* <span data-ttu-id="cd367-128">Architektura (bitové verze) (x64 nebo x86) nainstalovaný modul runtime a aplikace musí odpovídat architektuře fondu aplikací.</span><span class="sxs-lookup"><span data-stu-id="cd367-128">The architecture (bitness) of the app and installed runtime (x64 or x86) must match the architecture of the app pool.</span></span>

* <span data-ttu-id="cd367-129">Odpojení klienta jsou zjištěny.</span><span class="sxs-lookup"><span data-stu-id="cd367-129">Client disconnects are detected.</span></span> <span data-ttu-id="cd367-130">Token zrušení [HttpContext. RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) je zrušený, když se klient odpojí.</span><span class="sxs-lookup"><span data-stu-id="cd367-130">The [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) cancellation token is cancelled when the client disconnects.</span></span>

* <span data-ttu-id="cd367-131">V ASP.NET Core 2.2.1 nebo starším <xref:System.IO.Directory.GetCurrentDirectory*> vrátí pracovní adresář procesu spuštěného službou IIS místo adresáře aplikace (například *C:\Windows\System32\inetsrv* pro *W3wp. exe*).</span><span class="sxs-lookup"><span data-stu-id="cd367-131">In ASP.NET Core 2.2.1 or earlier, <xref:System.IO.Directory.GetCurrentDirectory*> returns the worker directory of the process started by IIS rather than the app's directory (for example, *C:\Windows\System32\inetsrv* for *w3wp.exe*).</span></span>

  <span data-ttu-id="cd367-132">Vzorový kód, který nastaví aktuální adresář aplikace, najdete v tématu [Třída CurrentDirectoryHelpers](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/3.x/CurrentDirectoryHelpers.cs).</span><span class="sxs-lookup"><span data-stu-id="cd367-132">For sample code that sets the app's current directory, see the [CurrentDirectoryHelpers class](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/3.x/CurrentDirectoryHelpers.cs).</span></span> <span data-ttu-id="cd367-133">Zavolejte metodu `SetCurrentDirectory`.</span><span class="sxs-lookup"><span data-stu-id="cd367-133">Call the `SetCurrentDirectory` method.</span></span> <span data-ttu-id="cd367-134">Další volání <xref:System.IO.Directory.GetCurrentDirectory*> poskytují adresář aplikace.</span><span class="sxs-lookup"><span data-stu-id="cd367-134">Subsequent calls to <xref:System.IO.Directory.GetCurrentDirectory*> provide the app's directory.</span></span>

* <span data-ttu-id="cd367-135">Při hostování v procesu není <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> interně volána k inicializaci uživatele.</span><span class="sxs-lookup"><span data-stu-id="cd367-135">When hosting in-process, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span> <span data-ttu-id="cd367-136">Proto implementace <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> používaná k transformaci deklarací po každém ověření není ve výchozím nastavení aktivována.</span><span class="sxs-lookup"><span data-stu-id="cd367-136">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="cd367-137">Při transformaci deklarací s implementací <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> volejte <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> a přidejte ověřovací služby:</span><span class="sxs-lookup"><span data-stu-id="cd367-137">When transforming claims with an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation, call <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> to add authentication services:</span></span>

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
  
  * <span data-ttu-id="cd367-138">[Nasazení webového balíčku (v jednom souboru)](/aspnet/web-forms/overview/deployment/web-deployment-in-the-enterprise/deploying-web-packages) nejsou podporována.</span><span class="sxs-lookup"><span data-stu-id="cd367-138">[Web Package (single-file) deployments](/aspnet/web-forms/overview/deployment/web-deployment-in-the-enterprise/deploying-web-packages) aren't supported.</span></span>

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="cd367-139">Model hostování mimo proces</span><span class="sxs-lookup"><span data-stu-id="cd367-139">Out-of-process hosting model</span></span>

<span data-ttu-id="cd367-140">Chcete-li nakonfigurovat aplikaci pro hostování mimo procesy, nastavte hodnotu vlastnosti `<AspNetCoreHostingModel>` na `OutOfProcess` v souboru projektu ( *. csproj*):</span><span class="sxs-lookup"><span data-stu-id="cd367-140">To configure an app for out-of-process hosting, set the value of the `<AspNetCoreHostingModel>` property to `OutOfProcess` in the project file (*.csproj*):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="cd367-141">Hostování v rámci procesu je nastaveno pomocí `InProcess`, což je výchozí hodnota.</span><span class="sxs-lookup"><span data-stu-id="cd367-141">In-process hosting is set with `InProcess`, which is the default value.</span></span>

<span data-ttu-id="cd367-142">Hodnota `<AspNetCoreHostingModel>` rozlišuje malá a velká písmena, takže `inprocess` a `outofprocess` jsou platné hodnoty.</span><span class="sxs-lookup"><span data-stu-id="cd367-142">The value of `<AspNetCoreHostingModel>` is case insensitive, so `inprocess` and `outofprocess` are valid values.</span></span>

<span data-ttu-id="cd367-143">Namísto serveru HTTP IIS (`IISHttpServer`) se používá server [Kestrel](xref:fundamentals/servers/kestrel) .</span><span class="sxs-lookup"><span data-stu-id="cd367-143">[Kestrel](xref:fundamentals/servers/kestrel) server is used instead of IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="cd367-144">Pro nedokončené volání [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) volá <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> k:</span><span class="sxs-lookup"><span data-stu-id="cd367-144">For out-of-process, [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> to:</span></span>

* <span data-ttu-id="cd367-145">Nakonfigurujte port a základní cestu, na kterých má Server naslouchat při spuštění za modulem ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="cd367-145">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
* <span data-ttu-id="cd367-146">Nakonfigurujte hostitele tak, aby zachytával chyby při spuštění.</span><span class="sxs-lookup"><span data-stu-id="cd367-146">Configure the host to capture startup errors.</span></span>

### <a name="hosting-model-changes"></a><span data-ttu-id="cd367-147">Hostování změny modelu</span><span class="sxs-lookup"><span data-stu-id="cd367-147">Hosting model changes</span></span>

<span data-ttu-id="cd367-148">Pokud je nastavení `hostingModel` změněno v souboru *Web. config* (vysvětleno v části [Konfigurace pomocí Web. config](#configuration-with-webconfig) ), modul recykluje pracovní proces pro službu IIS.</span><span class="sxs-lookup"><span data-stu-id="cd367-148">If the `hostingModel` setting is changed in the *web.config* file (explained in the [Configuration with web.config](#configuration-with-webconfig) section), the module recycles the worker process for IIS.</span></span>

<span data-ttu-id="cd367-149">Pro službu IIS Express modul nebude recyklovat pracovní proces, ale místo toho aktivuje řádné vypnutí aktuální proces IIS Express.</span><span class="sxs-lookup"><span data-stu-id="cd367-149">For IIS Express, the module doesn't recycle the worker process but instead triggers a graceful shutdown of the current IIS Express process.</span></span> <span data-ttu-id="cd367-150">Další požadavek na aplikaci vytvoří nový proces IIS Express.</span><span class="sxs-lookup"><span data-stu-id="cd367-150">The next request to the app spawns a new IIS Express process.</span></span>

### <a name="process-name"></a><span data-ttu-id="cd367-151">Název procesu</span><span class="sxs-lookup"><span data-stu-id="cd367-151">Process name</span></span>

<span data-ttu-id="cd367-152">sestavy `Process.GetCurrentProcess().ProcessName` `w3wp`/`iisexpress` (v procesu) nebo `dotnet` (mimo proces).</span><span class="sxs-lookup"><span data-stu-id="cd367-152">`Process.GetCurrentProcess().ProcessName` reports `w3wp`/`iisexpress` (in-process) or `dotnet` (out-of-process).</span></span>

<span data-ttu-id="cd367-153">Mnoho nativních modulů, jako je ověřování systému Windows, zůstane aktivní.</span><span class="sxs-lookup"><span data-stu-id="cd367-153">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="cd367-154">Další informace o aktivních modulech služby IIS pomocí modulu ASP.NET Core najdete v tématu <xref:host-and-deploy/iis/modules>.</span><span class="sxs-lookup"><span data-stu-id="cd367-154">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="cd367-155">Modul ASP.NET Core může také:</span><span class="sxs-lookup"><span data-stu-id="cd367-155">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="cd367-156">Nastavte proměnné prostředí pro pracovní proces.</span><span class="sxs-lookup"><span data-stu-id="cd367-156">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="cd367-157">Protokoluje výstup stdout do úložiště souborů pro řešení problémů při spuštění.</span><span class="sxs-lookup"><span data-stu-id="cd367-157">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="cd367-158">Předejte tokeny ověřování systému Windows.</span><span class="sxs-lookup"><span data-stu-id="cd367-158">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="cd367-159">Jak nainstalovat a používat modul ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="cd367-159">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="cd367-160">Pokyny k instalaci modulu ASP.NET Core najdete v tématu [instalace hostující sady .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span><span class="sxs-lookup"><span data-stu-id="cd367-160">For instructions on how to install the ASP.NET Core Module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="cd367-161">Konfigurace pomocí souboru web.config</span><span class="sxs-lookup"><span data-stu-id="cd367-161">Configuration with web.config</span></span>

<span data-ttu-id="cd367-162">ASP.NET Core modul je nakonfigurovaný pomocí oddílu `aspNetCore` uzlu `system.webServer` v souboru *Web. config* webu.</span><span class="sxs-lookup"><span data-stu-id="cd367-162">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="cd367-163">Následující soubor *Web. config* je publikován pro [nasazení závislé na rozhraní](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) a nakonfiguruje modul ASP.NET Core pro zpracování požadavků lokality:</span><span class="sxs-lookup"><span data-stu-id="cd367-163">The following *web.config* file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

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

<span data-ttu-id="cd367-164">Následující soubor *Web. config* je publikován pro [samostatně uzavřené nasazení](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span><span class="sxs-lookup"><span data-stu-id="cd367-164">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

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

<span data-ttu-id="cd367-165">Vlastnost <xref:System.Configuration.SectionInformation.InheritInChildApplications*> je nastavena na hodnotu `false`, která označuje, že nastavení zadaná v [\<umístění >](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) nejsou děděna aplikacemi, které se nacházejí v podadresáři aplikace.</span><span class="sxs-lookup"><span data-stu-id="cd367-165">The <xref:System.Configuration.SectionInformation.InheritInChildApplications*> property is set to `false` to indicate that the settings specified within the [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) element aren't inherited by apps that reside in a subdirectory of the app.</span></span>

<span data-ttu-id="cd367-166">Když se aplikace nasadí do [Azure App Service](https://azure.microsoft.com/services/app-service/), `stdoutLogFile`á cesta je nastavená na `\\?\%home%\LogFiles\stdout`.</span><span class="sxs-lookup"><span data-stu-id="cd367-166">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="cd367-167">Cesta ukládá protokoly stdout do složky *soubory protokolů* , což je umístění, které služba automaticky vytvořila.</span><span class="sxs-lookup"><span data-stu-id="cd367-167">The path saves stdout logs to the *LogFiles* folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="cd367-168">Informace o konfiguraci dílčí aplikace IIS najdete v tématu <xref:host-and-deploy/iis/index#sub-applications>.</span><span class="sxs-lookup"><span data-stu-id="cd367-168">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="cd367-169">Atributy elementu aspNetCore</span><span class="sxs-lookup"><span data-stu-id="cd367-169">Attributes of the aspNetCore element</span></span>

| <span data-ttu-id="cd367-170">Atribut</span><span class="sxs-lookup"><span data-stu-id="cd367-170">Attribute</span></span> | <span data-ttu-id="cd367-171">Popis</span><span class="sxs-lookup"><span data-stu-id="cd367-171">Description</span></span> | <span data-ttu-id="cd367-172">Výchozí</span><span class="sxs-lookup"><span data-stu-id="cd367-172">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="cd367-173">Volitelný atribut řetězce.</span><span class="sxs-lookup"><span data-stu-id="cd367-173">Optional string attribute.</span></span></p><p><span data-ttu-id="cd367-174">Argumenty ke spustitelnému souboru určenému v **processPath**.</span><span class="sxs-lookup"><span data-stu-id="cd367-174">Arguments to the executable specified in **processPath**.</span></span></p> | |
| `disableStartUpErrorPage` | <p><span data-ttu-id="cd367-175">Volitelný logický atribut.</span><span class="sxs-lookup"><span data-stu-id="cd367-175">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="cd367-176">Pokud je nastaveno na true, stránka **502,5 – selhání procesu** se potlačí a stavová stránka 502 konfigurovaná v *souboru Web. config* má přednost.</span><span class="sxs-lookup"><span data-stu-id="cd367-176">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="cd367-177">Volitelný logický atribut.</span><span class="sxs-lookup"><span data-stu-id="cd367-177">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="cd367-178">Při hodnotě true se token předá podřízený proces naslouchání na ASPNETCORE_PORT % jako záhlaví "MS-ASPNETCORE-WINAUTHTOKEN" každý požadavek.</span><span class="sxs-lookup"><span data-stu-id="cd367-178">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="cd367-179">Je odpovědností tento proces pro volání CloseHandle na tento token každý požadavek.</span><span class="sxs-lookup"><span data-stu-id="cd367-179">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `hostingModel` | <p><span data-ttu-id="cd367-180">Volitelný atribut řetězce.</span><span class="sxs-lookup"><span data-stu-id="cd367-180">Optional string attribute.</span></span></p><p><span data-ttu-id="cd367-181">Určuje model hostování jako vnitroprocesové (`InProcess`/`inprocess`) nebo mimo proces (`OutOfProcess`/`outofprocess`).</span><span class="sxs-lookup"><span data-stu-id="cd367-181">Specifies the hosting model as in-process (`InProcess`/`inprocess`) or out-of-process (`OutOfProcess`/`outofprocess`).</span></span></p> | `InProcess`<br>`inprocess` |
| `processesPerApplication` | <p><span data-ttu-id="cd367-182">Volitelný celočíselný atribut.</span><span class="sxs-lookup"><span data-stu-id="cd367-182">Optional integer attribute.</span></span></p><p><span data-ttu-id="cd367-183">Určuje počet instancí procesu určeného v nastavení **processPath** , které lze v rámci aplikace vystavit.</span><span class="sxs-lookup"><span data-stu-id="cd367-183">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="cd367-184">&dagger;pro hostování v rámci procesu je hodnota omezená na `1`.</span><span class="sxs-lookup"><span data-stu-id="cd367-184">&dagger;For in-process hosting, the value is limited to `1`.</span></span></p><p><span data-ttu-id="cd367-185">Nastavení `processesPerApplication` se nedoporučuje.</span><span class="sxs-lookup"><span data-stu-id="cd367-185">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="cd367-186">Tento atribut bude v budoucí verzi odebrán.</span><span class="sxs-lookup"><span data-stu-id="cd367-186">This attribute will be removed in a future release.</span></span></p> | <span data-ttu-id="cd367-187">Výchozí: `1`</span><span class="sxs-lookup"><span data-stu-id="cd367-187">Default: `1`</span></span><br><span data-ttu-id="cd367-188">Minimum: `1`</span><span class="sxs-lookup"><span data-stu-id="cd367-188">Min: `1`</span></span><br><span data-ttu-id="cd367-189">Max: `100`&dagger;</span><span class="sxs-lookup"><span data-stu-id="cd367-189">Max: `100`&dagger;</span></span> |
| `processPath` | <p><span data-ttu-id="cd367-190">Požadovaný atribut typu string.</span><span class="sxs-lookup"><span data-stu-id="cd367-190">Required string attribute.</span></span></p><p><span data-ttu-id="cd367-191">Cesta ke spustitelnému souboru, který spustí nějaký proces naslouchání požadavků protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="cd367-191">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="cd367-192">Jsou podporovány relativní cesty.</span><span class="sxs-lookup"><span data-stu-id="cd367-192">Relative paths are supported.</span></span> <span data-ttu-id="cd367-193">Pokud cesta začíná na `.`, je cesta považována za relativní vzhledem k kořenu webu.</span><span class="sxs-lookup"><span data-stu-id="cd367-193">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="cd367-194">Volitelný celočíselný atribut.</span><span class="sxs-lookup"><span data-stu-id="cd367-194">Optional integer attribute.</span></span></p><p><span data-ttu-id="cd367-195">Určuje, kolikrát je povoleno selhání procesu určeného v **processPath** za minutu.</span><span class="sxs-lookup"><span data-stu-id="cd367-195">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="cd367-196">Pokud je tento limit překročen, modulu zastaví spuštění procesu pro zbytek minuty.</span><span class="sxs-lookup"><span data-stu-id="cd367-196">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p><p><span data-ttu-id="cd367-197">Není podporováno s hostitelem v procesu.</span><span class="sxs-lookup"><span data-stu-id="cd367-197">Not supported with in-process hosting.</span></span></p> | <span data-ttu-id="cd367-198">Výchozí: `10`</span><span class="sxs-lookup"><span data-stu-id="cd367-198">Default: `10`</span></span><br><span data-ttu-id="cd367-199">Minimum: `0`</span><span class="sxs-lookup"><span data-stu-id="cd367-199">Min: `0`</span></span><br><span data-ttu-id="cd367-200">Maximum: `100`</span><span class="sxs-lookup"><span data-stu-id="cd367-200">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="cd367-201">Atribut volitelný časový interval.</span><span class="sxs-lookup"><span data-stu-id="cd367-201">Optional timespan attribute.</span></span></p><p><span data-ttu-id="cd367-202">Určuje dobu, pro kterou modul ASP.NET Core čeká na odpověď z procesu naslouchání na ASPNETCORE_PORT %.</span><span class="sxs-lookup"><span data-stu-id="cd367-202">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="cd367-203">Ve verzích modulu ASP.NET Core, které byly dodávány s vydáním ASP.NET Core 2,1 nebo novějším, je `requestTimeout` zadána v hodinách, minutách a sekundách.</span><span class="sxs-lookup"><span data-stu-id="cd367-203">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p><p><span data-ttu-id="cd367-204">Neplatí pro hostování v procesu.</span><span class="sxs-lookup"><span data-stu-id="cd367-204">Doesn't apply to in-process hosting.</span></span> <span data-ttu-id="cd367-205">Hostování v procesu, čeká modul app ke zpracování požadavku.</span><span class="sxs-lookup"><span data-stu-id="cd367-205">For in-process hosting, the module waits for the app to process the request.</span></span></p><p><span data-ttu-id="cd367-206">Platné hodnoty segmentů minut a sekund řetězce jsou v rozsahu 0-59.</span><span class="sxs-lookup"><span data-stu-id="cd367-206">Valid values for minutes and seconds segments of the string are in the range 0-59.</span></span> <span data-ttu-id="cd367-207">Při použití hodnoty **60** v hodnotě minut nebo sekund dojde k chybě systému *500 – interní chyba serveru*.</span><span class="sxs-lookup"><span data-stu-id="cd367-207">Use of **60** in the value for minutes or seconds results in a *500 - Internal Server Error*.</span></span></p> | <span data-ttu-id="cd367-208">Výchozí: `00:02:00`</span><span class="sxs-lookup"><span data-stu-id="cd367-208">Default: `00:02:00`</span></span><br><span data-ttu-id="cd367-209">Minimum: `00:00:00`</span><span class="sxs-lookup"><span data-stu-id="cd367-209">Min: `00:00:00`</span></span><br><span data-ttu-id="cd367-210">Maximum: `360:00:00`</span><span class="sxs-lookup"><span data-stu-id="cd367-210">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="cd367-211">Volitelný celočíselný atribut.</span><span class="sxs-lookup"><span data-stu-id="cd367-211">Optional integer attribute.</span></span></p><p><span data-ttu-id="cd367-212">Doba v sekundách, po kterou modul čeká na řádné vypnutí spustitelného souboru, když se zjistí soubor *App_offline. htm* .</span><span class="sxs-lookup"><span data-stu-id="cd367-212">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="cd367-213">Výchozí: `10`</span><span class="sxs-lookup"><span data-stu-id="cd367-213">Default: `10`</span></span><br><span data-ttu-id="cd367-214">Minimum: `0`</span><span class="sxs-lookup"><span data-stu-id="cd367-214">Min: `0`</span></span><br><span data-ttu-id="cd367-215">Maximum: `600`</span><span class="sxs-lookup"><span data-stu-id="cd367-215">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="cd367-216">Volitelný celočíselný atribut.</span><span class="sxs-lookup"><span data-stu-id="cd367-216">Optional integer attribute.</span></span></p><p><span data-ttu-id="cd367-217">Doba trvání v sekundách, které modul čeká na spustitelný soubor ke spuštění procesu naslouchání na portu.</span><span class="sxs-lookup"><span data-stu-id="cd367-217">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="cd367-218">Pokud je překročen časový limit, modul ukončí proces.</span><span class="sxs-lookup"><span data-stu-id="cd367-218">If this time limit is exceeded, the module kills the process.</span></span> <span data-ttu-id="cd367-219">Modul se pokusí znovu spustit proces, když obdrží novou žádost, a pokračuje v pokusu o restartování procesu u dalších příchozích požadavků, pokud se nepodaří spustit **rapidFailsPerMinute** počet pokusů během poslední minuty.</span><span class="sxs-lookup"><span data-stu-id="cd367-219">The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="cd367-220">Hodnota 0 (nula **) není považována za** nekonečný časový limit.</span><span class="sxs-lookup"><span data-stu-id="cd367-220">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="cd367-221">Výchozí: `120`</span><span class="sxs-lookup"><span data-stu-id="cd367-221">Default: `120`</span></span><br><span data-ttu-id="cd367-222">Minimum: `0`</span><span class="sxs-lookup"><span data-stu-id="cd367-222">Min: `0`</span></span><br><span data-ttu-id="cd367-223">Maximum: `3600`</span><span class="sxs-lookup"><span data-stu-id="cd367-223">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="cd367-224">Volitelný logický atribut.</span><span class="sxs-lookup"><span data-stu-id="cd367-224">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="cd367-225">Je-li nastavena hodnota true, **stdout** a **stderr** pro proces zadaný v **processPath** budou přesměrovány do souboru zadaného v **stdoutLogFile**.</span><span class="sxs-lookup"><span data-stu-id="cd367-225">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="cd367-226">Volitelný atribut řetězce.</span><span class="sxs-lookup"><span data-stu-id="cd367-226">Optional string attribute.</span></span></p><p><span data-ttu-id="cd367-227">Určuje relativní nebo absolutní cestu k souboru, pro který je **stdout** a **stderr** z procesu určeného v **processPath** protokolováno.</span><span class="sxs-lookup"><span data-stu-id="cd367-227">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="cd367-228">Relativní cesty jsou relativní vzhledem k kořen webu.</span><span class="sxs-lookup"><span data-stu-id="cd367-228">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="cd367-229">Všechny cesty začínající na `.` jsou relativní vzhledem k kořenu webu a všechny ostatní cesty se považují za absolutní cesty.</span><span class="sxs-lookup"><span data-stu-id="cd367-229">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="cd367-230">Při vytvoření souboru protokolu se v modulu vytvoří všechny složky, které jsou v cestě zadané.</span><span class="sxs-lookup"><span data-stu-id="cd367-230">Any folders provided in the path are created by the module when the log file is created.</span></span> <span data-ttu-id="cd367-231">Pomocí oddělovačů podtržítka se do posledního segmentu cesty **stdoutLogFile** přidá časové razítko, ID procesu a Přípona souboru ( *. log*).</span><span class="sxs-lookup"><span data-stu-id="cd367-231">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="cd367-232">Pokud je jako hodnota zadána `.\logs\stdout`, je ukázkový protokol stdout uložen jako *stdout_20180205194132_1934. log* ve složce *logs* při uložení na 2/5/2018 v 19:41:32 s ID procesu 1934.</span><span class="sxs-lookup"><span data-stu-id="cd367-232">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

### <a name="set-environment-variables"></a><span data-ttu-id="cd367-233">Nastavení proměnných prostředí</span><span class="sxs-lookup"><span data-stu-id="cd367-233">Set environment variables</span></span>

<span data-ttu-id="cd367-234">Proměnné prostředí lze zadat pro proces v atributu `processPath`.</span><span class="sxs-lookup"><span data-stu-id="cd367-234">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="cd367-235">Zadejte proměnnou prostředí s `<environmentVariable>` podřízeným elementem elementu `<environmentVariables>` kolekce.</span><span class="sxs-lookup"><span data-stu-id="cd367-235">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span> <span data-ttu-id="cd367-236">Proměnné prostředí nastavené v této části přednost systémové proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="cd367-236">Environment variables set in this section take precedence over system environment variables.</span></span>

<span data-ttu-id="cd367-237">Následující příklad nastaví dvě proměnné prostředí v *souboru Web. config*. `ASPNETCORE_ENVIRONMENT` nakonfiguruje prostředí aplikace tak, aby bylo `Development`.</span><span class="sxs-lookup"><span data-stu-id="cd367-237">The following example sets two environment variables in *web.config*. `ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="cd367-238">Vývojář může tuto hodnotu dočasně nastavit v souboru *Web. config* , aby bylo možné vynutit, aby se [Stránka s výjimkou vývojářů](xref:fundamentals/error-handling) načetla při ladění výjimky aplikace.</span><span class="sxs-lookup"><span data-stu-id="cd367-238">A developer may temporarily set this value in the *web.config* file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="cd367-239">`CONFIG_DIR` je příklad uživatelsky definované proměnné prostředí, kde vývojář napsal kód, který přečte hodnotu při spuštění, aby vytvořil cestu pro načtení konfiguračního souboru aplikace.</span><span class="sxs-lookup"><span data-stu-id="cd367-239">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

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
> <span data-ttu-id="cd367-240">Alternativou k nastavení prostředí přímo v souboru *Web. config* je zahrnutí vlastnosti `<EnvironmentName>` do [profilu publikování (. pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) nebo souboru projektu.</span><span class="sxs-lookup"><span data-stu-id="cd367-240">An alternative to setting the environment directly in *web.config* is to include the `<EnvironmentName>` property in the [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) or project file.</span></span> <span data-ttu-id="cd367-241">Tento přístup nastaví prostředí v *souboru Web. config* při publikování projektu:</span><span class="sxs-lookup"><span data-stu-id="cd367-241">This approach sets the environment in *web.config* when the project is published:</span></span>
>
> ```xml
> <PropertyGroup>
>   <EnvironmentName>Development</EnvironmentName>
> </PropertyGroup>
> ```

> [!WARNING]
> <span data-ttu-id="cd367-242">Nastavte pouze proměnnou prostředí `ASPNETCORE_ENVIRONMENT`, aby `Development` na pracovních a testovacích serverech, které nejsou přístupné nedůvěryhodným sítím, jako je například Internet.</span><span class="sxs-lookup"><span data-stu-id="cd367-242">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## <a name="app_offlinehtm"></a><span data-ttu-id="cd367-243">app_offline.htm</span><span class="sxs-lookup"><span data-stu-id="cd367-243">app_offline.htm</span></span>

<span data-ttu-id="cd367-244">Pokud se v kořenovém adresáři aplikace zjistí soubor s názvem *App_offline. htm* , modul ASP.NET Core se pokusí aplikaci řádně vypnout a zastavit zpracování příchozích požadavků.</span><span class="sxs-lookup"><span data-stu-id="cd367-244">If a file with the name *app_offline.htm* is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="cd367-245">Pokud aplikace běží i po uplynutí počtu sekund definovaného v `shutdownTimeLimit`, modul ASP.NET Core ukončuje běžící proces.</span><span class="sxs-lookup"><span data-stu-id="cd367-245">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="cd367-246">Když je přítomen soubor *App_offline. htm* , ASP.NET Core modul reaguje na požadavky odesláním zpět obsahu souboru *App_offline. htm* .</span><span class="sxs-lookup"><span data-stu-id="cd367-246">While the *app_offline.htm* file is present, the ASP.NET Core Module responds to requests by sending back the contents of the *app_offline.htm* file.</span></span> <span data-ttu-id="cd367-247">Po odebrání souboru *App_offline. htm* aplikace spustí další požadavek.</span><span class="sxs-lookup"><span data-stu-id="cd367-247">When the *app_offline.htm* file is removed, the next request starts the app.</span></span>

<span data-ttu-id="cd367-248">Pokud používáte model hostingu mimo proces, aplikace nemusí vypnout okamžitě při otevření připojení.</span><span class="sxs-lookup"><span data-stu-id="cd367-248">When using the out-of-process hosting model, the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="cd367-249">Připojení soketu websocket bylo třeba může způsobit prodlevu při ukončení aplikace.</span><span class="sxs-lookup"><span data-stu-id="cd367-249">For example, a websocket connection may delay app shut down.</span></span>

## <a name="start-up-error-page"></a><span data-ttu-id="cd367-250">Spuštění chybovou stránku</span><span class="sxs-lookup"><span data-stu-id="cd367-250">Start-up error page</span></span>

<span data-ttu-id="cd367-251">Hostování v procesu a mimo proces vytvoření vlastní chybové stránky, když selžou a spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="cd367-251">Both in-process and out-of-process hosting produce custom error pages when they fail to start the app.</span></span>

<span data-ttu-id="cd367-252">Pokud modul ASP.NET Core nenalezne obslužnou rutinu žádosti v procesu nebo mimo proces 500,0, zobrazí se stránka s kódem stavu *selhání načtení obslužné rutiny v* procesu nebo mimo proces.</span><span class="sxs-lookup"><span data-stu-id="cd367-252">If the ASP.NET Core Module fails to find either the in-process or out-of-process request handler, a *500.0 - In-Process/Out-Of-Process Handler Load Failure* status code page appears.</span></span>

<span data-ttu-id="cd367-253">Pro vnitroprocesové hostování v případě, že se modul ASP.NET Core nepodaří spustit aplikaci, zobrazí se stránka s kódem stavu *chyby při spuštění 500,30* .</span><span class="sxs-lookup"><span data-stu-id="cd367-253">For in-process hosting if the ASP.NET Core Module fails to start the app, a *500.30 - Start Failure* status code page appears.</span></span>

<span data-ttu-id="cd367-254">Pro hostování mimo procesy, pokud se ASP.NET Core modulu nespustí back-end proces, nebo dojde k selhání procesu back-endu, ale nenaslouchá na konfigurovaném portu, zobrazí se stránka s kódem stavu *selhání procesu 502,5* .</span><span class="sxs-lookup"><span data-stu-id="cd367-254">For out-of-process hosting if the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span>

<span data-ttu-id="cd367-255">Chcete-li tuto stránku potlačit a vrátit se na výchozí stavovou stránku služby IIS 5xx, použijte atribut `disableStartUpErrorPage`.</span><span class="sxs-lookup"><span data-stu-id="cd367-255">To suppress this page and revert to the default IIS 5xx status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="cd367-256">Další informace o konfiguraci vlastních chybových zpráv najdete v tématu [chyby protokolu HTTP \<httpErrors >](/iis/configuration/system.webServer/httpErrors/).</span><span class="sxs-lookup"><span data-stu-id="cd367-256">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

## <a name="log-creation-and-redirection"></a><span data-ttu-id="cd367-257">Vytvoření protokolu a přesměrování</span><span class="sxs-lookup"><span data-stu-id="cd367-257">Log creation and redirection</span></span>

<span data-ttu-id="cd367-258">Modul ASP.NET Core přesměruje výstup konzoly stdout a stderr na disk, pokud jsou nastaveny atributy `stdoutLogEnabled` a `stdoutLogFile` elementu `aspNetCore`.</span><span class="sxs-lookup"><span data-stu-id="cd367-258">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="cd367-259">Všechny složky v `stdoutLogFile` cestě jsou vytvořeny modulem při vytvoření souboru protokolu.</span><span class="sxs-lookup"><span data-stu-id="cd367-259">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="cd367-260">Fond aplikací musí mít oprávnění k zápisu do umístění, kam se zapisují protokoly (k poskytnutí oprávnění k zápisu použijte `IIS AppPool\<app_pool_name>`).</span><span class="sxs-lookup"><span data-stu-id="cd367-260">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="cd367-261">Protokoly nejsou střídán, pokud dojde k recyklování procesů/restartování.</span><span class="sxs-lookup"><span data-stu-id="cd367-261">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="cd367-262">Je odpovědností hostitel k omezení místa na disku, které využijete v protokolech.</span><span class="sxs-lookup"><span data-stu-id="cd367-262">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="cd367-263">Použití protokolu stdout se doporučuje pouze při řešení potíží se spouštěním aplikací při hostování služby IIS nebo při použití [podpory v době vývoje služby IIS se sadou Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), nikoli při ladění místně a při spuštění aplikace s IIS Express.</span><span class="sxs-lookup"><span data-stu-id="cd367-263">Using the stdout log is only recommended for troubleshooting app startup issues when hosting on IIS or when using [development-time support for IIS with Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), not while debugging locally and running the app with IIS Express.</span></span>

<span data-ttu-id="cd367-264">Nepoužívejte stdout protokolu pro účely protokolování obecné aplikace.</span><span class="sxs-lookup"><span data-stu-id="cd367-264">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="cd367-265">Pro rutiny protokolování v aplikaci ASP.NET Core, použijte protokolování knihovnu, která omezuje velikost souboru protokolu a otočí protokoly.</span><span class="sxs-lookup"><span data-stu-id="cd367-265">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="cd367-266">Další informace najdete v tématu [Zprostředkovatelé protokolování třetích stran](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="cd367-266">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="cd367-267">Časové razítko a soubor rozšíření jsou přidány automaticky při vytvoření souboru protokolu.</span><span class="sxs-lookup"><span data-stu-id="cd367-267">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="cd367-268">Název souboru protokolu se skládá tak, že se připojí časové razítko, ID procesu a Přípona souboru ( *. log*) k poslednímu segmentu `stdoutLogFile` cesty (obvykle *stdout*) oddělené podtržítky.</span><span class="sxs-lookup"><span data-stu-id="cd367-268">The log file name is composed by appending the timestamp, process ID, and file extension (*.log*) to the last segment of the `stdoutLogFile` path (typically *stdout*) delimited by underscores.</span></span> <span data-ttu-id="cd367-269">Pokud `stdoutLogFile` cesta končí na *stdout*, protokol pro aplikaci s číslem PID 1934 vytvořeným v 2/5/2018 v 19:42:32 má název souboru *stdout_20180205194132_1934. log*.</span><span class="sxs-lookup"><span data-stu-id="cd367-269">If the `stdoutLogFile` path ends with *stdout*, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name *stdout_20180205194132_1934.log*.</span></span>

<span data-ttu-id="cd367-270">Pokud je `stdoutLogEnabled` false, chyby, ke kterým dochází při spuštění aplikace, se zaznamenávají a generují do protokolu událostí až do 30 KB.</span><span class="sxs-lookup"><span data-stu-id="cd367-270">If `stdoutLogEnabled` is false, errors that occur on app startup are captured and emitted to the event log up to 30 KB.</span></span> <span data-ttu-id="cd367-271">Po spuštění se zahodí všechny další protokoly.</span><span class="sxs-lookup"><span data-stu-id="cd367-271">After startup, all additional logs are discarded.</span></span>

<span data-ttu-id="cd367-272">Následující vzorový `aspNetCore` prvek nakonfiguruje protokolování stdout na relativní cestě `.\log\`.</span><span class="sxs-lookup"><span data-stu-id="cd367-272">The following sample `aspNetCore` element configures stdout logging at the relative path `.\log\`.</span></span> <span data-ttu-id="cd367-273">Ověřte, jestli uživatel identita fondu aplikací má oprávnění k zápisu do zadaná výstupní cesta.</span><span class="sxs-lookup"><span data-stu-id="cd367-273">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout"
    hostingModel="inprocess">
</aspNetCore>
```

<span data-ttu-id="cd367-274">Když publikujete aplikaci pro nasazení Azure App Service, Webová sada SDK nastaví hodnotu `stdoutLogFile` na `\\?\%home%\LogFiles\stdout`.</span><span class="sxs-lookup"><span data-stu-id="cd367-274">When publishing an app for Azure App Service deployment, the Web SDK sets the `stdoutLogFile` value to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="cd367-275">Pro aplikace hostované v Azure App Service je předdefinovaná proměnná prostředí `%home`.</span><span class="sxs-lookup"><span data-stu-id="cd367-275">The `%home` environment variable is predefined for apps hosted by Azure App Service.</span></span>

<span data-ttu-id="cd367-276">Chcete-li vytvořit pravidla filtru protokolování, přečtěte si část [Konfigurace](xref:fundamentals/logging/index#log-filtering) a [filtrování protokolů](xref:fundamentals/logging/index#log-filtering) v dokumentaci ASP.NET Core protokolování.</span><span class="sxs-lookup"><span data-stu-id="cd367-276">To create logging filter rules, see the [Configuration](xref:fundamentals/logging/index#log-filtering) and [Log filtering](xref:fundamentals/logging/index#log-filtering) sections of the ASP.NET Core logging documentation.</span></span>

<span data-ttu-id="cd367-277">Další informace o formátech cest najdete v tématu [formáty souborů cest v systémech Windows](/dotnet/standard/io/file-path-formats).</span><span class="sxs-lookup"><span data-stu-id="cd367-277">For more information on path formats, see [File path formats on Windows systems](/dotnet/standard/io/file-path-formats).</span></span>

## <a name="enhanced-diagnostic-logs"></a><span data-ttu-id="cd367-278">Rozšířené diagnostické protokoly</span><span class="sxs-lookup"><span data-stu-id="cd367-278">Enhanced diagnostic logs</span></span>

<span data-ttu-id="cd367-279">Modul ASP.NET Core lze nakonfigurovat tak, aby poskytoval rozšířené diagnostické protokoly.</span><span class="sxs-lookup"><span data-stu-id="cd367-279">The ASP.NET Core Module is configurable to provide enhanced diagnostics logs.</span></span> <span data-ttu-id="cd367-280">Přidejte `<handlerSettings>` element do elementu `<aspNetCore>` v *souboru Web. config*. Nastavení `debugLevel` na `TRACE` zpřístupňuje vyšší věrnost diagnostických informací:</span><span class="sxs-lookup"><span data-stu-id="cd367-280">Add the `<handlerSettings>` element to the `<aspNetCore>` element in *web.config*. Setting the `debugLevel` to `TRACE` exposes a higher fidelity of diagnostic information:</span></span>

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

<span data-ttu-id="cd367-281">Všechny složky v cestě (*protokoly* v předchozím příkladu) se vytvoří modulem, když se vytvoří soubor protokolu.</span><span class="sxs-lookup"><span data-stu-id="cd367-281">Any folders in the path (*logs* in the preceding example) are created by the module when the log file is created.</span></span> <span data-ttu-id="cd367-282">Fond aplikací musí mít oprávnění k zápisu do umístění, kam se zapisují protokoly (k poskytnutí oprávnění k zápisu použijte `IIS AppPool\<app_pool_name>`).</span><span class="sxs-lookup"><span data-stu-id="cd367-282">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="cd367-283">Hodnoty úrovně ladění (`debugLevel`) mohou zahrnovat jak úroveň, tak i umístění.</span><span class="sxs-lookup"><span data-stu-id="cd367-283">Debug level (`debugLevel`) values can include both the level and the location.</span></span>

<span data-ttu-id="cd367-284">Úrovně (v pořadí od nejméně na nejpodrobnější):</span><span class="sxs-lookup"><span data-stu-id="cd367-284">Levels (in order from least to most verbose):</span></span>

* <span data-ttu-id="cd367-285">CHYBA</span><span class="sxs-lookup"><span data-stu-id="cd367-285">ERROR</span></span>
* <span data-ttu-id="cd367-286">UPOZORNĚNÍ</span><span class="sxs-lookup"><span data-stu-id="cd367-286">WARNING</span></span>
* <span data-ttu-id="cd367-287">INFORMACE O</span><span class="sxs-lookup"><span data-stu-id="cd367-287">INFO</span></span>
* <span data-ttu-id="cd367-288">TRASOVÁNÍ</span><span class="sxs-lookup"><span data-stu-id="cd367-288">TRACE</span></span>

<span data-ttu-id="cd367-289">Umístění (umístění více jsou povoleny):</span><span class="sxs-lookup"><span data-stu-id="cd367-289">Locations (multiple locations are permitted):</span></span>

* <span data-ttu-id="cd367-290">KONZOLY</span><span class="sxs-lookup"><span data-stu-id="cd367-290">CONSOLE</span></span>
* <span data-ttu-id="cd367-291">PROTOKOL UDÁLOSTÍ</span><span class="sxs-lookup"><span data-stu-id="cd367-291">EVENTLOG</span></span>
* <span data-ttu-id="cd367-292">SOUBOR</span><span class="sxs-lookup"><span data-stu-id="cd367-292">FILE</span></span>

<span data-ttu-id="cd367-293">Obslužná rutina nastavení se dá zadat i prostřednictvím proměnné prostředí:</span><span class="sxs-lookup"><span data-stu-id="cd367-293">The handler settings can also be provided via environment variables:</span></span>

* <span data-ttu-id="cd367-294">`ASPNETCORE_MODULE_DEBUG_FILE` &ndash; cestu k souboru protokolu ladění.</span><span class="sxs-lookup"><span data-stu-id="cd367-294">`ASPNETCORE_MODULE_DEBUG_FILE` &ndash; Path to the debug log file.</span></span> <span data-ttu-id="cd367-295">(Výchozí: *aspnetcore-Debug. log*)</span><span class="sxs-lookup"><span data-stu-id="cd367-295">(Default: *aspnetcore-debug.log*)</span></span>
* <span data-ttu-id="cd367-296">`ASPNETCORE_MODULE_DEBUG` &ndash; nastavení úrovně ladění.</span><span class="sxs-lookup"><span data-stu-id="cd367-296">`ASPNETCORE_MODULE_DEBUG` &ndash; Debug level setting.</span></span>

> [!WARNING]
> <span data-ttu-id="cd367-297">Nenechávejte **protokolování** ladění povolené v nasazení po delší dobu, než je potřeba k řešení problému.</span><span class="sxs-lookup"><span data-stu-id="cd367-297">Do **not** leave debug logging enabled in the deployment for longer than required to troubleshoot an issue.</span></span> <span data-ttu-id="cd367-298">Velikost souboru protokolu není omezený.</span><span class="sxs-lookup"><span data-stu-id="cd367-298">The size of the log isn't limited.</span></span> <span data-ttu-id="cd367-299">Opuštění protokol ladění povoleno může vyčerpat dostupné místo na disku a chybách u aplikací na serveru nebo služby app service.</span><span class="sxs-lookup"><span data-stu-id="cd367-299">Leaving the debug log enabled can exhaust the available disk space and crash the server or app service.</span></span>

<span data-ttu-id="cd367-300">Příklad `aspNetCore` elementu v souboru *Web. config* naleznete v tématu [Konfigurace pomocí souboru Web. config](#configuration-with-webconfig) .</span><span class="sxs-lookup"><span data-stu-id="cd367-300">See [Configuration with web.config](#configuration-with-webconfig) for an example of the `aspNetCore` element in the *web.config* file.</span></span>

## <a name="modify-the-stack-size"></a><span data-ttu-id="cd367-301">Úprava velikosti zásobníku</span><span class="sxs-lookup"><span data-stu-id="cd367-301">Modify the stack size</span></span>

<span data-ttu-id="cd367-302">*Platí pouze při použití modelu hostování v rámci procesu.*</span><span class="sxs-lookup"><span data-stu-id="cd367-302">*Only applies when using the in-process hosting model.*</span></span>

<span data-ttu-id="cd367-303">Nakonfigurujte velikost spravovaného zásobníku pomocí nastavení `stackSize` v bajtech v *souboru Web. config*. Výchozí velikost je `1048576` bajtů (1 MB).</span><span class="sxs-lookup"><span data-stu-id="cd367-303">Configure the managed stack size using the `stackSize` setting in bytes in *web.config*. The default size is `1048576` bytes (1 MB).</span></span>

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

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="cd367-304">Konfigurace proxy serveru používá protokol HTTP a token pro párování</span><span class="sxs-lookup"><span data-stu-id="cd367-304">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="cd367-305">*Platí pouze pro hostování mimo proces.*</span><span class="sxs-lookup"><span data-stu-id="cd367-305">*Only applies to out-of-process hosting.*</span></span>

<span data-ttu-id="cd367-306">Proxy server mezi modul ASP.NET Core a Kestrel používá protokol HTTP.</span><span class="sxs-lookup"><span data-stu-id="cd367-306">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="cd367-307">Neexistuje žádné riziko odposloucháváním provozu mezi modulu a Kestrel z umístění mimo server.</span><span class="sxs-lookup"><span data-stu-id="cd367-307">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="cd367-308">Párování token se používá k zajištění, že požadavků přijatých službou Kestrel byly směrovány přes proxy server službou IIS a nebyl dodán z nějakého jiného zdroje.</span><span class="sxs-lookup"><span data-stu-id="cd367-308">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="cd367-309">Token párování se vytvoří a nastaví na proměnnou prostředí (`ASPNETCORE_TOKEN`) modulem.</span><span class="sxs-lookup"><span data-stu-id="cd367-309">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="cd367-310">Párovací token je také nastaven na hlavičku (`MS-ASPNETCORE-TOKEN`) u každého požadavku proxy.</span><span class="sxs-lookup"><span data-stu-id="cd367-310">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="cd367-311">Služba IIS Middleware kontroly požadavku že přijme potvrďte, že odpovídá párování hodnota tokenu hlavičky hodnotu proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="cd367-311">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="cd367-312">Pokud token hodnoty se neshodují, žádost je zaznamenána a odmítnut.</span><span class="sxs-lookup"><span data-stu-id="cd367-312">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="cd367-313">Párování proměnná tokenu prostředí a přenos dat mezi modulu a Kestrel nejsou dostupné z umístění mimo server.</span><span class="sxs-lookup"><span data-stu-id="cd367-313">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="cd367-314">Nainstalovat bez mého párování hodnota tokenu nelze útočník odesílat požadavky, které obejít kontrolu v IIS middlewaru.</span><span class="sxs-lookup"><span data-stu-id="cd367-314">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="cd367-315">Modul ASP.NET Core s službu IIS sdílenou konfiguraci</span><span class="sxs-lookup"><span data-stu-id="cd367-315">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="cd367-316">Instalační služba modulu ASP.NET Core se spouští s oprávněními účtu **TrustedInstaller** .</span><span class="sxs-lookup"><span data-stu-id="cd367-316">The ASP.NET Core Module installer runs with the privileges of the **TrustedInstaller** account.</span></span> <span data-ttu-id="cd367-317">Vzhledem k tomu, že účet místního systému nemá oprávnění k úpravám pro cestu ke sdílené složce, kterou používá sdílená konfigurace služby IIS, vyvolá Instalační program chybu odepření přístupu při pokusu o konfiguraci nastavení modulu v souboru *ApplicationHost. config* ve sdílené složce.</span><span class="sxs-lookup"><span data-stu-id="cd367-317">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the *applicationHost.config* file on the share.</span></span>

<span data-ttu-id="cd367-318">Pokud používáte sdílenou konfiguraci IIS na stejném počítači jako instalaci služby IIS, spusťte instalační program sady prostředků ASP.NET Core s parametrem `OPT_NO_SHARED_CONFIG_CHECK` nastaveným na `1`:</span><span class="sxs-lookup"><span data-stu-id="cd367-318">When using an IIS Shared Configuration on the same machine as the IIS installation, run the ASP.NET Core Hosting Bundle installer with the `OPT_NO_SHARED_CONFIG_CHECK` parameter set to `1`:</span></span>

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

<span data-ttu-id="cd367-319">Pokud cesta ke sdílené konfiguraci není ve stejném počítači jako instalace služby IIS, postupujte podle těchto kroků:</span><span class="sxs-lookup"><span data-stu-id="cd367-319">When the path to the shared configuration isn't on the same machine as the IIS installation, follow these steps:</span></span>

1. <span data-ttu-id="cd367-320">Zakážete sdílenou konfiguraci IIS.</span><span class="sxs-lookup"><span data-stu-id="cd367-320">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="cd367-321">Spusťte instalační program.</span><span class="sxs-lookup"><span data-stu-id="cd367-321">Run the installer.</span></span>
1. <span data-ttu-id="cd367-322">Exportujte aktualizovaný soubor *ApplicationHost. config* do sdílené složky.</span><span class="sxs-lookup"><span data-stu-id="cd367-322">Export the updated *applicationHost.config* file to the share.</span></span>
1. <span data-ttu-id="cd367-323">Znovu povolte sdílenou konfiguraci IIS.</span><span class="sxs-lookup"><span data-stu-id="cd367-323">Re-enable the IIS Shared Configuration.</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="cd367-324">Verze modulu a hostování sady Instalační protokoly</span><span class="sxs-lookup"><span data-stu-id="cd367-324">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="cd367-325">Chcete-li zjistit verzi nainstalovaný modul ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="cd367-325">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="cd367-326">V hostitelském systému přejděte na *%windir%\system32\inetsrv*.</span><span class="sxs-lookup"><span data-stu-id="cd367-326">On the hosting system, navigate to *%windir%\System32\inetsrv*.</span></span>
1. <span data-ttu-id="cd367-327">Vyhledejte soubor *aspnetcore. dll* .</span><span class="sxs-lookup"><span data-stu-id="cd367-327">Locate the *aspnetcore.dll* file.</span></span>
1. <span data-ttu-id="cd367-328">Klikněte na soubor pravým tlačítkem a v místní nabídce vyberte **vlastnosti** .</span><span class="sxs-lookup"><span data-stu-id="cd367-328">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="cd367-329">Vyberte kartu **Podrobnosti** . **Verze souboru** a **verze produktu** reprezentují nainstalovanou verzi modulu.</span><span class="sxs-lookup"><span data-stu-id="cd367-329">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="cd367-330">Protokoly instalačních balíčků pro modul jsou k dispozici v *C:\\uživatelé\\% username%\\data\\místní\\Temp*. Soubor má název *dd_DotNetCoreWinSvrHosting__\<časové razítko > _000_AspNetCoreModule_x64. log*.</span><span class="sxs-lookup"><span data-stu-id="cd367-330">The Hosting Bundle installer logs for the module are found at *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. The file is named *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="cd367-331">Modul, schéma a konfiguraci umístění souborů</span><span class="sxs-lookup"><span data-stu-id="cd367-331">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="cd367-332">Modul</span><span class="sxs-lookup"><span data-stu-id="cd367-332">Module</span></span>

<span data-ttu-id="cd367-333">**Služba IIS (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="cd367-333">**IIS (x86/amd64):**</span></span>

* <span data-ttu-id="cd367-334">%windir%\System32\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="cd367-334">%windir%\System32\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="cd367-335">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="cd367-335">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="cd367-336">%ProgramFiles%\IIS\Asp.NET core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="cd367-336">%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="cd367-337">% ProgramFiles (x86) %\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="cd367-337">%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

<span data-ttu-id="cd367-338">**IIS Express (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="cd367-338">**IIS Express (x86/amd64):**</span></span>

* <span data-ttu-id="cd367-339">%ProgramFiles%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="cd367-339">%ProgramFiles%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="cd367-340">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="cd367-340">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="cd367-341">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="cd367-341">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="cd367-342">% ProgramFiles (x86) %\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="cd367-342">%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

### <a name="schema"></a><span data-ttu-id="cd367-343">Schéma</span><span class="sxs-lookup"><span data-stu-id="cd367-343">Schema</span></span>

<span data-ttu-id="cd367-344">**SLUŽBU**</span><span class="sxs-lookup"><span data-stu-id="cd367-344">**IIS**</span></span>

* <span data-ttu-id="cd367-345">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="cd367-345">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span></span>

* <span data-ttu-id="cd367-346">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.XML</span><span class="sxs-lookup"><span data-stu-id="cd367-346">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span></span>

<span data-ttu-id="cd367-347">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="cd367-347">**IIS Express**</span></span>

* <span data-ttu-id="cd367-348">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="cd367-348">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span></span>

* <span data-ttu-id="cd367-349">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span><span class="sxs-lookup"><span data-stu-id="cd367-349">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span></span>

### <a name="configuration"></a><span data-ttu-id="cd367-350">Konfigurace</span><span class="sxs-lookup"><span data-stu-id="cd367-350">Configuration</span></span>

<span data-ttu-id="cd367-351">**SLUŽBU**</span><span class="sxs-lookup"><span data-stu-id="cd367-351">**IIS**</span></span>

* <span data-ttu-id="cd367-352">%windir%\System32\inetsrv\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="cd367-352">%windir%\System32\inetsrv\config\applicationHost.config</span></span>

<span data-ttu-id="cd367-353">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="cd367-353">**IIS Express**</span></span>

* <span data-ttu-id="cd367-354">Visual Studio: {ROOT aplikace}\\. vs\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="cd367-354">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span></span>

* <span data-ttu-id="cd367-355">*IISExpress. exe* CLI:%USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span><span class="sxs-lookup"><span data-stu-id="cd367-355">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span></span>

<span data-ttu-id="cd367-356">Soubory lze najít hledáním *aspnetcore* v souboru *ApplicationHost. config* .</span><span class="sxs-lookup"><span data-stu-id="cd367-356">The files can be found by searching for *aspnetcore* in the *applicationHost.config* file.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="cd367-357">Modul ASP.NET Core je nativním modulem služby IIS, který se připojuje k kanálu IIS na jednu z těchto:</span><span class="sxs-lookup"><span data-stu-id="cd367-357">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to either:</span></span>

* <span data-ttu-id="cd367-358">Hostování aplikace ASP.NET Core uvnitř pracovního procesu služby IIS (`w3wp.exe`), který se nazývá [model hostování v rámci procesu](#in-process-hosting-model).</span><span class="sxs-lookup"><span data-stu-id="cd367-358">Host an ASP.NET Core app inside of the IIS worker process (`w3wp.exe`), called the [in-process hosting model](#in-process-hosting-model).</span></span>
* <span data-ttu-id="cd367-359">Dopředné webové požadavky do back-endu ASP.NET Core aplikace, na které běží [Server Kestrel](xref:fundamentals/servers/kestrel), se nazývá [model hostování mimo proces](#out-of-process-hosting-model).</span><span class="sxs-lookup"><span data-stu-id="cd367-359">Forward web requests to a backend ASP.NET Core app running the [Kestrel server](xref:fundamentals/servers/kestrel), called the [out-of-process hosting model](#out-of-process-hosting-model).</span></span>

<span data-ttu-id="cd367-360">Podporované verze systému Windows:</span><span class="sxs-lookup"><span data-stu-id="cd367-360">Supported Windows versions:</span></span>

* <span data-ttu-id="cd367-361">Windows 7 nebo novější</span><span class="sxs-lookup"><span data-stu-id="cd367-361">Windows 7 or later</span></span>
* <span data-ttu-id="cd367-362">Windows Server 2008 R2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="cd367-362">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="cd367-363">Při hostování v procesu používá modul implementaci v rámci procesového serveru pro službu IIS, která se nazývá IIS HTTP Server (`IISHttpServer`).</span><span class="sxs-lookup"><span data-stu-id="cd367-363">When hosting in-process, the module uses an in-process server implementation for IIS, called IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="cd367-364">Při hostování mimo proces funguje modul pouze s Kestrel.</span><span class="sxs-lookup"><span data-stu-id="cd367-364">When hosting out-of-process, the module only works with Kestrel.</span></span> <span data-ttu-id="cd367-365">Modul nefunguje s [ovladačem HTTP. sys](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="cd367-365">The module doesn't function with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

## <a name="hosting-models"></a><span data-ttu-id="cd367-366">Modely hostingu</span><span class="sxs-lookup"><span data-stu-id="cd367-366">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="cd367-367">Model hostování v procesu</span><span class="sxs-lookup"><span data-stu-id="cd367-367">In-process hosting model</span></span>

<span data-ttu-id="cd367-368">Chcete-li nakonfigurovat aplikaci pro hostování v rámci procesu, přidejte do souboru projektu aplikace vlastnost `<AspNetCoreHostingModel>` s hodnotou `InProcess` (hostování mimo procesy je nastaveno s `OutOfProcess`):</span><span class="sxs-lookup"><span data-stu-id="cd367-368">To configure an app for in-process hosting, add the `<AspNetCoreHostingModel>` property to the app's project file with a value of `InProcess` (out-of-process hosting is set with `OutOfProcess`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>InProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="cd367-369">Model hostování v rámci procesu není podporován pro aplikace ASP.NET Core, které cílí na .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="cd367-369">The in-process hosting model isn't supported for ASP.NET Core apps that target the .NET Framework.</span></span>

<span data-ttu-id="cd367-370">Hodnota `<AspNetCoreHostingModel>` rozlišuje malá a velká písmena, takže `inprocess` a `outofprocess` jsou platné hodnoty.</span><span class="sxs-lookup"><span data-stu-id="cd367-370">The value of `<AspNetCoreHostingModel>` is case insensitive, so `inprocess` and `outofprocess` are valid values.</span></span>

<span data-ttu-id="cd367-371">Pokud se v souboru nenachází vlastnost `<AspNetCoreHostingModel>`, je výchozí hodnota `OutOfProcess`.</span><span class="sxs-lookup"><span data-stu-id="cd367-371">If the `<AspNetCoreHostingModel>` property isn't present in the file, the default value is `OutOfProcess`.</span></span>

<span data-ttu-id="cd367-372">Při hostování v procesu platí následující vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="cd367-372">The following characteristics apply when hosting in-process:</span></span>

* <span data-ttu-id="cd367-373">Místo [Kestrel](xref:fundamentals/servers/kestrel) serveru se používá server HTTP služby IIS (`IISHttpServer`).</span><span class="sxs-lookup"><span data-stu-id="cd367-373">IIS HTTP Server (`IISHttpServer`) is used instead of [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span> <span data-ttu-id="cd367-374">Pro vnitroprocesové [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) volání <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*>:</span><span class="sxs-lookup"><span data-stu-id="cd367-374">For in-process, [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> to:</span></span>

  * <span data-ttu-id="cd367-375">Zaregistrujte `IISHttpServer`.</span><span class="sxs-lookup"><span data-stu-id="cd367-375">Register the `IISHttpServer`.</span></span>
  * <span data-ttu-id="cd367-376">Nakonfigurujte port a základní cestu, na kterých má Server naslouchat při spuštění za modulem ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="cd367-376">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
  * <span data-ttu-id="cd367-377">Nakonfigurujte hostitele tak, aby zachytával chyby při spuštění.</span><span class="sxs-lookup"><span data-stu-id="cd367-377">Configure the host to capture startup errors.</span></span>

* <span data-ttu-id="cd367-378">[Atribut RequestTimeout](#attributes-of-the-aspnetcore-element) se nevztahuje na hostování v rámci procesu.</span><span class="sxs-lookup"><span data-stu-id="cd367-378">The [requestTimeout attribute](#attributes-of-the-aspnetcore-element) doesn't apply to in-process hosting.</span></span>

* <span data-ttu-id="cd367-379">Sdílení fondem aplikací mezi aplikacemi se nepodporuje.</span><span class="sxs-lookup"><span data-stu-id="cd367-379">Sharing an app pool among apps isn't supported.</span></span> <span data-ttu-id="cd367-380">Použijte jeden fond aplikací na aplikaci.</span><span class="sxs-lookup"><span data-stu-id="cd367-380">Use one app pool per app.</span></span>

* <span data-ttu-id="cd367-381">Při použití [nasazení webu](/iis/publish/using-web-deploy/introduction-to-web-deploy) nebo ručního umístění [souboru App_offline. htm v nasazení](xref:host-and-deploy/iis/index#locked-deployment-files)se aplikace nemusí vypnout okamžitě, pokud dojde k otevřenému připojení.</span><span class="sxs-lookup"><span data-stu-id="cd367-381">When using [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) or manually placing an [app_offline.htm file in the deployment](xref:host-and-deploy/iis/index#locked-deployment-files), the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="cd367-382">Připojení soketu websocket bylo třeba může způsobit prodlevu při ukončení aplikace.</span><span class="sxs-lookup"><span data-stu-id="cd367-382">For example, a websocket connection may delay app shut down.</span></span>

* <span data-ttu-id="cd367-383">Architektura (bitové verze) (x64 nebo x86) nainstalovaný modul runtime a aplikace musí odpovídat architektuře fondu aplikací.</span><span class="sxs-lookup"><span data-stu-id="cd367-383">The architecture (bitness) of the app and installed runtime (x64 or x86) must match the architecture of the app pool.</span></span>

* <span data-ttu-id="cd367-384">Odpojení klienta jsou zjištěny.</span><span class="sxs-lookup"><span data-stu-id="cd367-384">Client disconnects are detected.</span></span> <span data-ttu-id="cd367-385">Token zrušení [HttpContext. RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) je zrušený, když se klient odpojí.</span><span class="sxs-lookup"><span data-stu-id="cd367-385">The [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) cancellation token is cancelled when the client disconnects.</span></span>

* <span data-ttu-id="cd367-386">V ASP.NET Core 2.2.1 nebo starším <xref:System.IO.Directory.GetCurrentDirectory*> vrátí pracovní adresář procesu spuštěného službou IIS místo adresáře aplikace (například *C:\Windows\System32\inetsrv* pro *W3wp. exe*).</span><span class="sxs-lookup"><span data-stu-id="cd367-386">In ASP.NET Core 2.2.1 or earlier, <xref:System.IO.Directory.GetCurrentDirectory*> returns the worker directory of the process started by IIS rather than the app's directory (for example, *C:\Windows\System32\inetsrv* for *w3wp.exe*).</span></span>

  <span data-ttu-id="cd367-387">Vzorový kód, který nastaví aktuální adresář aplikace, najdete v tématu [Třída CurrentDirectoryHelpers](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/2.x/CurrentDirectoryHelpers.cs).</span><span class="sxs-lookup"><span data-stu-id="cd367-387">For sample code that sets the app's current directory, see the [CurrentDirectoryHelpers class](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/2.x/CurrentDirectoryHelpers.cs).</span></span> <span data-ttu-id="cd367-388">Zavolejte metodu `SetCurrentDirectory`.</span><span class="sxs-lookup"><span data-stu-id="cd367-388">Call the `SetCurrentDirectory` method.</span></span> <span data-ttu-id="cd367-389">Další volání <xref:System.IO.Directory.GetCurrentDirectory*> poskytují adresář aplikace.</span><span class="sxs-lookup"><span data-stu-id="cd367-389">Subsequent calls to <xref:System.IO.Directory.GetCurrentDirectory*> provide the app's directory.</span></span>

* <span data-ttu-id="cd367-390">Při hostování v procesu není <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> interně volána k inicializaci uživatele.</span><span class="sxs-lookup"><span data-stu-id="cd367-390">When hosting in-process, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span> <span data-ttu-id="cd367-391">Proto implementace <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> používaná k transformaci deklarací po každém ověření není ve výchozím nastavení aktivována.</span><span class="sxs-lookup"><span data-stu-id="cd367-391">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="cd367-392">Při transformaci deklarací s implementací <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> volejte <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> a přidejte ověřovací služby:</span><span class="sxs-lookup"><span data-stu-id="cd367-392">When transforming claims with an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation, call <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> to add authentication services:</span></span>

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

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="cd367-393">Model hostování mimo proces</span><span class="sxs-lookup"><span data-stu-id="cd367-393">Out-of-process hosting model</span></span>

<span data-ttu-id="cd367-394">Chcete-li nakonfigurovat aplikaci pro hostování mimo procesy, použijte některý z následujících přístupů v souboru projektu:</span><span class="sxs-lookup"><span data-stu-id="cd367-394">To configure an app for out-of-process hosting, use either of the following approaches in the project file:</span></span>

* <span data-ttu-id="cd367-395">Nezadávejte vlastnost `<AspNetCoreHostingModel>`.</span><span class="sxs-lookup"><span data-stu-id="cd367-395">Don't specify the `<AspNetCoreHostingModel>` property.</span></span> <span data-ttu-id="cd367-396">Pokud se v souboru nenachází vlastnost `<AspNetCoreHostingModel>`, je výchozí hodnota `OutOfProcess`.</span><span class="sxs-lookup"><span data-stu-id="cd367-396">If the `<AspNetCoreHostingModel>` property isn't present in the file, the default value is `OutOfProcess`.</span></span>
* <span data-ttu-id="cd367-397">Nastavte hodnotu vlastnosti `<AspNetCoreHostingModel>` na `OutOfProcess` (hostování v procesu je nastaveno pomocí `InProcess`):</span><span class="sxs-lookup"><span data-stu-id="cd367-397">Set the value of the `<AspNetCoreHostingModel>` property to `OutOfProcess` (in-process hosting is set with `InProcess`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="cd367-398">Hodnota rozlišuje malá a velká písmena, takže `inprocess` a `outofprocess` platné hodnoty.</span><span class="sxs-lookup"><span data-stu-id="cd367-398">The value is case insensitive, so `inprocess` and `outofprocess` are valid values.</span></span>

<span data-ttu-id="cd367-399">Namísto serveru HTTP IIS (`IISHttpServer`) se používá server [Kestrel](xref:fundamentals/servers/kestrel) .</span><span class="sxs-lookup"><span data-stu-id="cd367-399">[Kestrel](xref:fundamentals/servers/kestrel) server is used instead of IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="cd367-400">Pro nedokončené volání [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) volá <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> k:</span><span class="sxs-lookup"><span data-stu-id="cd367-400">For out-of-process, [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> to:</span></span>

* <span data-ttu-id="cd367-401">Nakonfigurujte port a základní cestu, na kterých má Server naslouchat při spuštění za modulem ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="cd367-401">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
* <span data-ttu-id="cd367-402">Nakonfigurujte hostitele tak, aby zachytával chyby při spuštění.</span><span class="sxs-lookup"><span data-stu-id="cd367-402">Configure the host to capture startup errors.</span></span>

### <a name="hosting-model-changes"></a><span data-ttu-id="cd367-403">Hostování změny modelu</span><span class="sxs-lookup"><span data-stu-id="cd367-403">Hosting model changes</span></span>

<span data-ttu-id="cd367-404">Pokud je nastavení `hostingModel` změněno v souboru *Web. config* (vysvětleno v části [Konfigurace pomocí Web. config](#configuration-with-webconfig) ), modul recykluje pracovní proces pro službu IIS.</span><span class="sxs-lookup"><span data-stu-id="cd367-404">If the `hostingModel` setting is changed in the *web.config* file (explained in the [Configuration with web.config](#configuration-with-webconfig) section), the module recycles the worker process for IIS.</span></span>

<span data-ttu-id="cd367-405">Pro službu IIS Express modul nebude recyklovat pracovní proces, ale místo toho aktivuje řádné vypnutí aktuální proces IIS Express.</span><span class="sxs-lookup"><span data-stu-id="cd367-405">For IIS Express, the module doesn't recycle the worker process but instead triggers a graceful shutdown of the current IIS Express process.</span></span> <span data-ttu-id="cd367-406">Další požadavek na aplikaci vytvoří nový proces IIS Express.</span><span class="sxs-lookup"><span data-stu-id="cd367-406">The next request to the app spawns a new IIS Express process.</span></span>

### <a name="process-name"></a><span data-ttu-id="cd367-407">Název procesu</span><span class="sxs-lookup"><span data-stu-id="cd367-407">Process name</span></span>

<span data-ttu-id="cd367-408">sestavy `Process.GetCurrentProcess().ProcessName` `w3wp`/`iisexpress` (v procesu) nebo `dotnet` (mimo proces).</span><span class="sxs-lookup"><span data-stu-id="cd367-408">`Process.GetCurrentProcess().ProcessName` reports `w3wp`/`iisexpress` (in-process) or `dotnet` (out-of-process).</span></span>

<span data-ttu-id="cd367-409">Mnoho nativních modulů, jako je ověřování systému Windows, zůstane aktivní.</span><span class="sxs-lookup"><span data-stu-id="cd367-409">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="cd367-410">Další informace o aktivních modulech služby IIS pomocí modulu ASP.NET Core najdete v tématu <xref:host-and-deploy/iis/modules>.</span><span class="sxs-lookup"><span data-stu-id="cd367-410">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="cd367-411">Modul ASP.NET Core může také:</span><span class="sxs-lookup"><span data-stu-id="cd367-411">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="cd367-412">Nastavte proměnné prostředí pro pracovní proces.</span><span class="sxs-lookup"><span data-stu-id="cd367-412">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="cd367-413">Protokoluje výstup stdout do úložiště souborů pro řešení problémů při spuštění.</span><span class="sxs-lookup"><span data-stu-id="cd367-413">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="cd367-414">Předejte tokeny ověřování systému Windows.</span><span class="sxs-lookup"><span data-stu-id="cd367-414">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="cd367-415">Jak nainstalovat a používat modul ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="cd367-415">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="cd367-416">Pokyny k instalaci modulu ASP.NET Core najdete v tématu [instalace hostující sady .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span><span class="sxs-lookup"><span data-stu-id="cd367-416">For instructions on how to install the ASP.NET Core Module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="cd367-417">Konfigurace pomocí souboru web.config</span><span class="sxs-lookup"><span data-stu-id="cd367-417">Configuration with web.config</span></span>

<span data-ttu-id="cd367-418">ASP.NET Core modul je nakonfigurovaný pomocí oddílu `aspNetCore` uzlu `system.webServer` v souboru *Web. config* webu.</span><span class="sxs-lookup"><span data-stu-id="cd367-418">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="cd367-419">Následující soubor *Web. config* je publikován pro [nasazení závislé na rozhraní](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) a nakonfiguruje modul ASP.NET Core pro zpracování požadavků lokality:</span><span class="sxs-lookup"><span data-stu-id="cd367-419">The following *web.config* file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

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

<span data-ttu-id="cd367-420">Následující soubor *Web. config* je publikován pro [samostatně uzavřené nasazení](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span><span class="sxs-lookup"><span data-stu-id="cd367-420">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

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

<span data-ttu-id="cd367-421">Vlastnost <xref:System.Configuration.SectionInformation.InheritInChildApplications*> je nastavena na hodnotu `false`, která označuje, že nastavení zadaná v [\<umístění >](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) nejsou děděna aplikacemi, které se nacházejí v podadresáři aplikace.</span><span class="sxs-lookup"><span data-stu-id="cd367-421">The <xref:System.Configuration.SectionInformation.InheritInChildApplications*> property is set to `false` to indicate that the settings specified within the [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) element aren't inherited by apps that reside in a subdirectory of the app.</span></span>

<span data-ttu-id="cd367-422">Když se aplikace nasadí do [Azure App Service](https://azure.microsoft.com/services/app-service/), `stdoutLogFile`á cesta je nastavená na `\\?\%home%\LogFiles\stdout`.</span><span class="sxs-lookup"><span data-stu-id="cd367-422">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="cd367-423">Cesta ukládá protokoly stdout do složky *soubory protokolů* , což je umístění, které služba automaticky vytvořila.</span><span class="sxs-lookup"><span data-stu-id="cd367-423">The path saves stdout logs to the *LogFiles* folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="cd367-424">Informace o konfiguraci dílčí aplikace IIS najdete v tématu <xref:host-and-deploy/iis/index#sub-applications>.</span><span class="sxs-lookup"><span data-stu-id="cd367-424">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="cd367-425">Atributy elementu aspNetCore</span><span class="sxs-lookup"><span data-stu-id="cd367-425">Attributes of the aspNetCore element</span></span>

| <span data-ttu-id="cd367-426">Atribut</span><span class="sxs-lookup"><span data-stu-id="cd367-426">Attribute</span></span> | <span data-ttu-id="cd367-427">Popis</span><span class="sxs-lookup"><span data-stu-id="cd367-427">Description</span></span> | <span data-ttu-id="cd367-428">Výchozí</span><span class="sxs-lookup"><span data-stu-id="cd367-428">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="cd367-429">Volitelný atribut řetězce.</span><span class="sxs-lookup"><span data-stu-id="cd367-429">Optional string attribute.</span></span></p><p><span data-ttu-id="cd367-430">Argumenty ke spustitelnému souboru určenému v **processPath**.</span><span class="sxs-lookup"><span data-stu-id="cd367-430">Arguments to the executable specified in **processPath**.</span></span></p> | |
| `disableStartUpErrorPage` | <p><span data-ttu-id="cd367-431">Volitelný logický atribut.</span><span class="sxs-lookup"><span data-stu-id="cd367-431">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="cd367-432">Pokud je nastaveno na true, stránka **502,5 – selhání procesu** se potlačí a stavová stránka 502 konfigurovaná v *souboru Web. config* má přednost.</span><span class="sxs-lookup"><span data-stu-id="cd367-432">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="cd367-433">Volitelný logický atribut.</span><span class="sxs-lookup"><span data-stu-id="cd367-433">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="cd367-434">Při hodnotě true se token předá podřízený proces naslouchání na ASPNETCORE_PORT % jako záhlaví "MS-ASPNETCORE-WINAUTHTOKEN" každý požadavek.</span><span class="sxs-lookup"><span data-stu-id="cd367-434">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="cd367-435">Je odpovědností tento proces pro volání CloseHandle na tento token každý požadavek.</span><span class="sxs-lookup"><span data-stu-id="cd367-435">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `hostingModel` | <p><span data-ttu-id="cd367-436">Volitelný atribut řetězce.</span><span class="sxs-lookup"><span data-stu-id="cd367-436">Optional string attribute.</span></span></p><p><span data-ttu-id="cd367-437">Určuje model hostování jako vnitroprocesové (`InProcess`/`inprocess`) nebo mimo proces (`OutOfProcess`/`outofprocess`).</span><span class="sxs-lookup"><span data-stu-id="cd367-437">Specifies the hosting model as in-process (`InProcess`/`inprocess`) or out-of-process (`OutOfProcess`/`outofprocess`).</span></span></p> | `OutOfProcess`<br>`outofprocess` |
| `processesPerApplication` | <p><span data-ttu-id="cd367-438">Volitelný celočíselný atribut.</span><span class="sxs-lookup"><span data-stu-id="cd367-438">Optional integer attribute.</span></span></p><p><span data-ttu-id="cd367-439">Určuje počet instancí procesu určeného v nastavení **processPath** , které lze v rámci aplikace vystavit.</span><span class="sxs-lookup"><span data-stu-id="cd367-439">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="cd367-440">&dagger;pro hostování v rámci procesu je hodnota omezená na `1`.</span><span class="sxs-lookup"><span data-stu-id="cd367-440">&dagger;For in-process hosting, the value is limited to `1`.</span></span></p><p><span data-ttu-id="cd367-441">Nastavení `processesPerApplication` se nedoporučuje.</span><span class="sxs-lookup"><span data-stu-id="cd367-441">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="cd367-442">Tento atribut bude v budoucí verzi odebrán.</span><span class="sxs-lookup"><span data-stu-id="cd367-442">This attribute will be removed in a future release.</span></span></p> | <span data-ttu-id="cd367-443">Výchozí: `1`</span><span class="sxs-lookup"><span data-stu-id="cd367-443">Default: `1`</span></span><br><span data-ttu-id="cd367-444">Minimum: `1`</span><span class="sxs-lookup"><span data-stu-id="cd367-444">Min: `1`</span></span><br><span data-ttu-id="cd367-445">Max: `100`&dagger;</span><span class="sxs-lookup"><span data-stu-id="cd367-445">Max: `100`&dagger;</span></span> |
| `processPath` | <p><span data-ttu-id="cd367-446">Požadovaný atribut typu string.</span><span class="sxs-lookup"><span data-stu-id="cd367-446">Required string attribute.</span></span></p><p><span data-ttu-id="cd367-447">Cesta ke spustitelnému souboru, který spustí nějaký proces naslouchání požadavků protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="cd367-447">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="cd367-448">Jsou podporovány relativní cesty.</span><span class="sxs-lookup"><span data-stu-id="cd367-448">Relative paths are supported.</span></span> <span data-ttu-id="cd367-449">Pokud cesta začíná na `.`, je cesta považována za relativní vzhledem k kořenu webu.</span><span class="sxs-lookup"><span data-stu-id="cd367-449">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="cd367-450">Volitelný celočíselný atribut.</span><span class="sxs-lookup"><span data-stu-id="cd367-450">Optional integer attribute.</span></span></p><p><span data-ttu-id="cd367-451">Určuje, kolikrát je povoleno selhání procesu určeného v **processPath** za minutu.</span><span class="sxs-lookup"><span data-stu-id="cd367-451">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="cd367-452">Pokud je tento limit překročen, modulu zastaví spuštění procesu pro zbytek minuty.</span><span class="sxs-lookup"><span data-stu-id="cd367-452">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p><p><span data-ttu-id="cd367-453">Není podporováno s hostitelem v procesu.</span><span class="sxs-lookup"><span data-stu-id="cd367-453">Not supported with in-process hosting.</span></span></p> | <span data-ttu-id="cd367-454">Výchozí: `10`</span><span class="sxs-lookup"><span data-stu-id="cd367-454">Default: `10`</span></span><br><span data-ttu-id="cd367-455">Minimum: `0`</span><span class="sxs-lookup"><span data-stu-id="cd367-455">Min: `0`</span></span><br><span data-ttu-id="cd367-456">Maximum: `100`</span><span class="sxs-lookup"><span data-stu-id="cd367-456">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="cd367-457">Atribut volitelný časový interval.</span><span class="sxs-lookup"><span data-stu-id="cd367-457">Optional timespan attribute.</span></span></p><p><span data-ttu-id="cd367-458">Určuje dobu, pro kterou modul ASP.NET Core čeká na odpověď z procesu naslouchání na ASPNETCORE_PORT %.</span><span class="sxs-lookup"><span data-stu-id="cd367-458">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="cd367-459">Ve verzích modulu ASP.NET Core, které byly dodávány s vydáním ASP.NET Core 2,1 nebo novějším, je `requestTimeout` zadána v hodinách, minutách a sekundách.</span><span class="sxs-lookup"><span data-stu-id="cd367-459">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p><p><span data-ttu-id="cd367-460">Neplatí pro hostování v procesu.</span><span class="sxs-lookup"><span data-stu-id="cd367-460">Doesn't apply to in-process hosting.</span></span> <span data-ttu-id="cd367-461">Hostování v procesu, čeká modul app ke zpracování požadavku.</span><span class="sxs-lookup"><span data-stu-id="cd367-461">For in-process hosting, the module waits for the app to process the request.</span></span></p><p><span data-ttu-id="cd367-462">Platné hodnoty segmentů minut a sekund řetězce jsou v rozsahu 0-59.</span><span class="sxs-lookup"><span data-stu-id="cd367-462">Valid values for minutes and seconds segments of the string are in the range 0-59.</span></span> <span data-ttu-id="cd367-463">Při použití hodnoty **60** v hodnotě minut nebo sekund dojde k chybě systému *500 – interní chyba serveru*.</span><span class="sxs-lookup"><span data-stu-id="cd367-463">Use of **60** in the value for minutes or seconds results in a *500 - Internal Server Error*.</span></span></p> | <span data-ttu-id="cd367-464">Výchozí: `00:02:00`</span><span class="sxs-lookup"><span data-stu-id="cd367-464">Default: `00:02:00`</span></span><br><span data-ttu-id="cd367-465">Minimum: `00:00:00`</span><span class="sxs-lookup"><span data-stu-id="cd367-465">Min: `00:00:00`</span></span><br><span data-ttu-id="cd367-466">Maximum: `360:00:00`</span><span class="sxs-lookup"><span data-stu-id="cd367-466">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="cd367-467">Volitelný celočíselný atribut.</span><span class="sxs-lookup"><span data-stu-id="cd367-467">Optional integer attribute.</span></span></p><p><span data-ttu-id="cd367-468">Doba v sekundách, po kterou modul čeká na řádné vypnutí spustitelného souboru, když se zjistí soubor *App_offline. htm* .</span><span class="sxs-lookup"><span data-stu-id="cd367-468">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="cd367-469">Výchozí: `10`</span><span class="sxs-lookup"><span data-stu-id="cd367-469">Default: `10`</span></span><br><span data-ttu-id="cd367-470">Minimum: `0`</span><span class="sxs-lookup"><span data-stu-id="cd367-470">Min: `0`</span></span><br><span data-ttu-id="cd367-471">Maximum: `600`</span><span class="sxs-lookup"><span data-stu-id="cd367-471">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="cd367-472">Volitelný celočíselný atribut.</span><span class="sxs-lookup"><span data-stu-id="cd367-472">Optional integer attribute.</span></span></p><p><span data-ttu-id="cd367-473">Doba trvání v sekundách, které modul čeká na spustitelný soubor ke spuštění procesu naslouchání na portu.</span><span class="sxs-lookup"><span data-stu-id="cd367-473">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="cd367-474">Pokud je překročen časový limit, modul ukončí proces.</span><span class="sxs-lookup"><span data-stu-id="cd367-474">If this time limit is exceeded, the module kills the process.</span></span> <span data-ttu-id="cd367-475">Modul se pokusí znovu spustit proces, když obdrží novou žádost, a pokračuje v pokusu o restartování procesu u dalších příchozích požadavků, pokud se nepodaří spustit **rapidFailsPerMinute** počet pokusů během poslední minuty.</span><span class="sxs-lookup"><span data-stu-id="cd367-475">The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="cd367-476">Hodnota 0 (nula **) není považována za** nekonečný časový limit.</span><span class="sxs-lookup"><span data-stu-id="cd367-476">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="cd367-477">Výchozí: `120`</span><span class="sxs-lookup"><span data-stu-id="cd367-477">Default: `120`</span></span><br><span data-ttu-id="cd367-478">Minimum: `0`</span><span class="sxs-lookup"><span data-stu-id="cd367-478">Min: `0`</span></span><br><span data-ttu-id="cd367-479">Maximum: `3600`</span><span class="sxs-lookup"><span data-stu-id="cd367-479">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="cd367-480">Volitelný logický atribut.</span><span class="sxs-lookup"><span data-stu-id="cd367-480">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="cd367-481">Je-li nastavena hodnota true, **stdout** a **stderr** pro proces zadaný v **processPath** budou přesměrovány do souboru zadaného v **stdoutLogFile**.</span><span class="sxs-lookup"><span data-stu-id="cd367-481">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="cd367-482">Volitelný atribut řetězce.</span><span class="sxs-lookup"><span data-stu-id="cd367-482">Optional string attribute.</span></span></p><p><span data-ttu-id="cd367-483">Určuje relativní nebo absolutní cestu k souboru, pro který je **stdout** a **stderr** z procesu určeného v **processPath** protokolováno.</span><span class="sxs-lookup"><span data-stu-id="cd367-483">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="cd367-484">Relativní cesty jsou relativní vzhledem k kořen webu.</span><span class="sxs-lookup"><span data-stu-id="cd367-484">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="cd367-485">Všechny cesty začínající na `.` jsou relativní vzhledem k kořenu webu a všechny ostatní cesty se považují za absolutní cesty.</span><span class="sxs-lookup"><span data-stu-id="cd367-485">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="cd367-486">Při vytvoření souboru protokolu se v modulu vytvoří všechny složky, které jsou v cestě zadané.</span><span class="sxs-lookup"><span data-stu-id="cd367-486">Any folders provided in the path are created by the module when the log file is created.</span></span> <span data-ttu-id="cd367-487">Pomocí oddělovačů podtržítka se do posledního segmentu cesty **stdoutLogFile** přidá časové razítko, ID procesu a Přípona souboru ( *. log*).</span><span class="sxs-lookup"><span data-stu-id="cd367-487">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="cd367-488">Pokud je jako hodnota zadána `.\logs\stdout`, je ukázkový protokol stdout uložen jako *stdout_20180205194132_1934. log* ve složce *logs* při uložení na 2/5/2018 v 19:41:32 s ID procesu 1934.</span><span class="sxs-lookup"><span data-stu-id="cd367-488">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

### <a name="setting-environment-variables"></a><span data-ttu-id="cd367-489">Nastavení proměnných prostředí</span><span class="sxs-lookup"><span data-stu-id="cd367-489">Setting environment variables</span></span>

<span data-ttu-id="cd367-490">Proměnné prostředí lze zadat pro proces v atributu `processPath`.</span><span class="sxs-lookup"><span data-stu-id="cd367-490">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="cd367-491">Zadejte proměnnou prostředí s `<environmentVariable>` podřízeným elementem elementu `<environmentVariables>` kolekce.</span><span class="sxs-lookup"><span data-stu-id="cd367-491">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span> <span data-ttu-id="cd367-492">Proměnné prostředí nastavené v této části přednost systémové proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="cd367-492">Environment variables set in this section take precedence over system environment variables.</span></span>

<span data-ttu-id="cd367-493">Následující příklad nastaví dvou proměnných prostředí.</span><span class="sxs-lookup"><span data-stu-id="cd367-493">The following example sets two environment variables.</span></span> <span data-ttu-id="cd367-494">`ASPNETCORE_ENVIRONMENT` nakonfiguruje prostředí aplikace tak, aby bylo `Development`.</span><span class="sxs-lookup"><span data-stu-id="cd367-494">`ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="cd367-495">Vývojář může tuto hodnotu dočasně nastavit v souboru *Web. config* , aby bylo možné vynutit, aby se [Stránka s výjimkou vývojářů](xref:fundamentals/error-handling) načetla při ladění výjimky aplikace.</span><span class="sxs-lookup"><span data-stu-id="cd367-495">A developer may temporarily set this value in the *web.config* file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="cd367-496">`CONFIG_DIR` je příklad uživatelsky definované proměnné prostředí, kde vývojář napsal kód, který přečte hodnotu při spuštění, aby vytvořil cestu pro načtení konfiguračního souboru aplikace.</span><span class="sxs-lookup"><span data-stu-id="cd367-496">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

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
> <span data-ttu-id="cd367-497">Alternativou k nastavení prostředí přímo v souboru *Web. config* je zahrnutí vlastnosti `<EnvironmentName>` do [profilu publikování (. pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) nebo souboru projektu.</span><span class="sxs-lookup"><span data-stu-id="cd367-497">An alternative to setting the environment directly in *web.config* is to include the `<EnvironmentName>` property in the [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) or project file.</span></span> <span data-ttu-id="cd367-498">Tento přístup nastaví prostředí v *souboru Web. config* při publikování projektu:</span><span class="sxs-lookup"><span data-stu-id="cd367-498">This approach sets the environment in *web.config* when the project is published:</span></span>
>
> ```xml
> <PropertyGroup>
>   <EnvironmentName>Development</EnvironmentName>
> </PropertyGroup>
> ```

> [!WARNING]
> <span data-ttu-id="cd367-499">Nastavte pouze proměnnou prostředí `ASPNETCORE_ENVIRONMENT`, aby `Development` na pracovních a testovacích serverech, které nejsou přístupné nedůvěryhodným sítím, jako je například Internet.</span><span class="sxs-lookup"><span data-stu-id="cd367-499">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## <a name="app_offlinehtm"></a><span data-ttu-id="cd367-500">app_offline.htm</span><span class="sxs-lookup"><span data-stu-id="cd367-500">app_offline.htm</span></span>

<span data-ttu-id="cd367-501">Pokud se v kořenovém adresáři aplikace zjistí soubor s názvem *App_offline. htm* , modul ASP.NET Core se pokusí aplikaci řádně vypnout a zastavit zpracování příchozích požadavků.</span><span class="sxs-lookup"><span data-stu-id="cd367-501">If a file with the name *app_offline.htm* is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="cd367-502">Pokud aplikace běží i po uplynutí počtu sekund definovaného v `shutdownTimeLimit`, modul ASP.NET Core ukončuje běžící proces.</span><span class="sxs-lookup"><span data-stu-id="cd367-502">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="cd367-503">Když je přítomen soubor *App_offline. htm* , ASP.NET Core modul reaguje na požadavky odesláním zpět obsahu souboru *App_offline. htm* .</span><span class="sxs-lookup"><span data-stu-id="cd367-503">While the *app_offline.htm* file is present, the ASP.NET Core Module responds to requests by sending back the contents of the *app_offline.htm* file.</span></span> <span data-ttu-id="cd367-504">Po odebrání souboru *App_offline. htm* aplikace spustí další požadavek.</span><span class="sxs-lookup"><span data-stu-id="cd367-504">When the *app_offline.htm* file is removed, the next request starts the app.</span></span>

<span data-ttu-id="cd367-505">Pokud používáte model hostingu mimo proces, aplikace nemusí vypnout okamžitě při otevření připojení.</span><span class="sxs-lookup"><span data-stu-id="cd367-505">When using the out-of-process hosting model, the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="cd367-506">Připojení soketu websocket bylo třeba může způsobit prodlevu při ukončení aplikace.</span><span class="sxs-lookup"><span data-stu-id="cd367-506">For example, a websocket connection may delay app shut down.</span></span>

## <a name="start-up-error-page"></a><span data-ttu-id="cd367-507">Spuštění chybovou stránku</span><span class="sxs-lookup"><span data-stu-id="cd367-507">Start-up error page</span></span>

<span data-ttu-id="cd367-508">Hostování v procesu a mimo proces vytvoření vlastní chybové stránky, když selžou a spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="cd367-508">Both in-process and out-of-process hosting produce custom error pages when they fail to start the app.</span></span>

<span data-ttu-id="cd367-509">Pokud modul ASP.NET Core nenalezne obslužnou rutinu žádosti v procesu nebo mimo proces 500,0, zobrazí se stránka s kódem stavu *selhání načtení obslužné rutiny v* procesu nebo mimo proces.</span><span class="sxs-lookup"><span data-stu-id="cd367-509">If the ASP.NET Core Module fails to find either the in-process or out-of-process request handler, a *500.0 - In-Process/Out-Of-Process Handler Load Failure* status code page appears.</span></span>

<span data-ttu-id="cd367-510">Pro vnitroprocesové hostování v případě, že se modul ASP.NET Core nepodaří spustit aplikaci, zobrazí se stránka s kódem stavu *chyby při spuštění 500,30* .</span><span class="sxs-lookup"><span data-stu-id="cd367-510">For in-process hosting if the ASP.NET Core Module fails to start the app, a *500.30 - Start Failure* status code page appears.</span></span>

<span data-ttu-id="cd367-511">Pro hostování mimo procesy, pokud se ASP.NET Core modulu nespustí back-end proces, nebo dojde k selhání procesu back-endu, ale nenaslouchá na konfigurovaném portu, zobrazí se stránka s kódem stavu *selhání procesu 502,5* .</span><span class="sxs-lookup"><span data-stu-id="cd367-511">For out-of-process hosting if the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span>

<span data-ttu-id="cd367-512">Chcete-li tuto stránku potlačit a vrátit se na výchozí stavovou stránku služby IIS 5xx, použijte atribut `disableStartUpErrorPage`.</span><span class="sxs-lookup"><span data-stu-id="cd367-512">To suppress this page and revert to the default IIS 5xx status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="cd367-513">Další informace o konfiguraci vlastních chybových zpráv najdete v tématu [chyby protokolu HTTP \<httpErrors >](/iis/configuration/system.webServer/httpErrors/).</span><span class="sxs-lookup"><span data-stu-id="cd367-513">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

## <a name="log-creation-and-redirection"></a><span data-ttu-id="cd367-514">Vytvoření protokolu a přesměrování</span><span class="sxs-lookup"><span data-stu-id="cd367-514">Log creation and redirection</span></span>

<span data-ttu-id="cd367-515">Modul ASP.NET Core přesměruje výstup konzoly stdout a stderr na disk, pokud jsou nastaveny atributy `stdoutLogEnabled` a `stdoutLogFile` elementu `aspNetCore`.</span><span class="sxs-lookup"><span data-stu-id="cd367-515">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="cd367-516">Všechny složky v `stdoutLogFile` cestě jsou vytvořeny modulem při vytvoření souboru protokolu.</span><span class="sxs-lookup"><span data-stu-id="cd367-516">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="cd367-517">Fond aplikací musí mít oprávnění k zápisu do umístění, kam se zapisují protokoly (k poskytnutí oprávnění k zápisu použijte `IIS AppPool\<app_pool_name>`).</span><span class="sxs-lookup"><span data-stu-id="cd367-517">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="cd367-518">Protokoly nejsou střídán, pokud dojde k recyklování procesů/restartování.</span><span class="sxs-lookup"><span data-stu-id="cd367-518">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="cd367-519">Je odpovědností hostitel k omezení místa na disku, které využijete v protokolech.</span><span class="sxs-lookup"><span data-stu-id="cd367-519">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="cd367-520">Použití protokolu stdout se doporučuje pouze při řešení potíží se spouštěním aplikací při hostování služby IIS nebo při použití [podpory v době vývoje služby IIS se sadou Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), nikoli při ladění místně a při spuštění aplikace s IIS Express.</span><span class="sxs-lookup"><span data-stu-id="cd367-520">Using the stdout log is only recommended for troubleshooting app startup issues when hosting on IIS or when using [development-time support for IIS with Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), not while debugging locally and running the app with IIS Express.</span></span>

<span data-ttu-id="cd367-521">Nepoužívejte stdout protokolu pro účely protokolování obecné aplikace.</span><span class="sxs-lookup"><span data-stu-id="cd367-521">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="cd367-522">Pro rutiny protokolování v aplikaci ASP.NET Core, použijte protokolování knihovnu, která omezuje velikost souboru protokolu a otočí protokoly.</span><span class="sxs-lookup"><span data-stu-id="cd367-522">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="cd367-523">Další informace najdete v tématu [Zprostředkovatelé protokolování třetích stran](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="cd367-523">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="cd367-524">Časové razítko a soubor rozšíření jsou přidány automaticky při vytvoření souboru protokolu.</span><span class="sxs-lookup"><span data-stu-id="cd367-524">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="cd367-525">Název souboru protokolu se skládá tak, že se připojí časové razítko, ID procesu a Přípona souboru ( *. log*) k poslednímu segmentu `stdoutLogFile` cesty (obvykle *stdout*) oddělené podtržítky.</span><span class="sxs-lookup"><span data-stu-id="cd367-525">The log file name is composed by appending the timestamp, process ID, and file extension (*.log*) to the last segment of the `stdoutLogFile` path (typically *stdout*) delimited by underscores.</span></span> <span data-ttu-id="cd367-526">Pokud `stdoutLogFile` cesta končí na *stdout*, protokol pro aplikaci s číslem PID 1934 vytvořeným v 2/5/2018 v 19:42:32 má název souboru *stdout_20180205194132_1934. log*.</span><span class="sxs-lookup"><span data-stu-id="cd367-526">If the `stdoutLogFile` path ends with *stdout*, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name *stdout_20180205194132_1934.log*.</span></span>

<span data-ttu-id="cd367-527">Pokud je `stdoutLogEnabled` false, chyby, ke kterým dochází při spuštění aplikace, se zaznamenávají a generují do protokolu událostí až do 30 KB.</span><span class="sxs-lookup"><span data-stu-id="cd367-527">If `stdoutLogEnabled` is false, errors that occur on app startup are captured and emitted to the event log up to 30 KB.</span></span> <span data-ttu-id="cd367-528">Po spuštění se zahodí všechny další protokoly.</span><span class="sxs-lookup"><span data-stu-id="cd367-528">After startup, all additional logs are discarded.</span></span>

<span data-ttu-id="cd367-529">Následující vzorový `aspNetCore` prvek nakonfiguruje protokolování stdout na relativní cestě `.\log\`.</span><span class="sxs-lookup"><span data-stu-id="cd367-529">The following sample `aspNetCore` element configures stdout logging at the relative path `.\log\`.</span></span> <span data-ttu-id="cd367-530">Ověřte, jestli uživatel identita fondu aplikací má oprávnění k zápisu do zadaná výstupní cesta.</span><span class="sxs-lookup"><span data-stu-id="cd367-530">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout"
    hostingModel="inprocess">
</aspNetCore>
```

<span data-ttu-id="cd367-531">Když publikujete aplikaci pro nasazení Azure App Service, Webová sada SDK nastaví hodnotu `stdoutLogFile` na `\\?\%home%\LogFiles\stdout`.</span><span class="sxs-lookup"><span data-stu-id="cd367-531">When publishing an app for Azure App Service deployment, the Web SDK sets the `stdoutLogFile` value to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="cd367-532">Pro aplikace hostované v Azure App Service je předdefinovaná proměnná prostředí `%home`.</span><span class="sxs-lookup"><span data-stu-id="cd367-532">The `%home` environment variable is predefined for apps hosted by Azure App Service.</span></span>

<span data-ttu-id="cd367-533">Další informace o formátech cest najdete v tématu [formáty souborů cest v systémech Windows](/dotnet/standard/io/file-path-formats).</span><span class="sxs-lookup"><span data-stu-id="cd367-533">For more information on path formats, see [File path formats on Windows systems](/dotnet/standard/io/file-path-formats).</span></span>

## <a name="enhanced-diagnostic-logs"></a><span data-ttu-id="cd367-534">Rozšířené diagnostické protokoly</span><span class="sxs-lookup"><span data-stu-id="cd367-534">Enhanced diagnostic logs</span></span>

<span data-ttu-id="cd367-535">Modul ASP.NET Core lze nakonfigurovat tak, aby poskytoval rozšířené diagnostické protokoly.</span><span class="sxs-lookup"><span data-stu-id="cd367-535">The ASP.NET Core Module is configurable to provide enhanced diagnostics logs.</span></span> <span data-ttu-id="cd367-536">Přidejte `<handlerSettings>` element do elementu `<aspNetCore>` v *souboru Web. config*. Nastavení `debugLevel` na `TRACE` zpřístupňuje vyšší věrnost diagnostických informací:</span><span class="sxs-lookup"><span data-stu-id="cd367-536">Add the `<handlerSettings>` element to the `<aspNetCore>` element in *web.config*. Setting the `debugLevel` to `TRACE` exposes a higher fidelity of diagnostic information:</span></span>

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

<span data-ttu-id="cd367-537">Složky v cestě zadané `<handlerSetting>` hodnotě (*protokoly* v předchozím příkladu) se nevytváří modulem automaticky a měly by být v nasazení předběžně existující.</span><span class="sxs-lookup"><span data-stu-id="cd367-537">Folders in the path provided to the `<handlerSetting>` value (*logs* in the preceding example) aren't created by the module automatically and should pre-exist in the deployment.</span></span> <span data-ttu-id="cd367-538">Fond aplikací musí mít oprávnění k zápisu do umístění, kam se zapisují protokoly (k poskytnutí oprávnění k zápisu použijte `IIS AppPool\<app_pool_name>`).</span><span class="sxs-lookup"><span data-stu-id="cd367-538">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="cd367-539">Hodnoty úrovně ladění (`debugLevel`) mohou zahrnovat jak úroveň, tak i umístění.</span><span class="sxs-lookup"><span data-stu-id="cd367-539">Debug level (`debugLevel`) values can include both the level and the location.</span></span>

<span data-ttu-id="cd367-540">Úrovně (v pořadí od nejméně na nejpodrobnější):</span><span class="sxs-lookup"><span data-stu-id="cd367-540">Levels (in order from least to most verbose):</span></span>

* <span data-ttu-id="cd367-541">CHYBA</span><span class="sxs-lookup"><span data-stu-id="cd367-541">ERROR</span></span>
* <span data-ttu-id="cd367-542">UPOZORNĚNÍ</span><span class="sxs-lookup"><span data-stu-id="cd367-542">WARNING</span></span>
* <span data-ttu-id="cd367-543">INFORMACE O</span><span class="sxs-lookup"><span data-stu-id="cd367-543">INFO</span></span>
* <span data-ttu-id="cd367-544">TRASOVÁNÍ</span><span class="sxs-lookup"><span data-stu-id="cd367-544">TRACE</span></span>

<span data-ttu-id="cd367-545">Umístění (umístění více jsou povoleny):</span><span class="sxs-lookup"><span data-stu-id="cd367-545">Locations (multiple locations are permitted):</span></span>

* <span data-ttu-id="cd367-546">KONZOLY</span><span class="sxs-lookup"><span data-stu-id="cd367-546">CONSOLE</span></span>
* <span data-ttu-id="cd367-547">PROTOKOL UDÁLOSTÍ</span><span class="sxs-lookup"><span data-stu-id="cd367-547">EVENTLOG</span></span>
* <span data-ttu-id="cd367-548">SOUBOR</span><span class="sxs-lookup"><span data-stu-id="cd367-548">FILE</span></span>

<span data-ttu-id="cd367-549">Obslužná rutina nastavení se dá zadat i prostřednictvím proměnné prostředí:</span><span class="sxs-lookup"><span data-stu-id="cd367-549">The handler settings can also be provided via environment variables:</span></span>

* <span data-ttu-id="cd367-550">`ASPNETCORE_MODULE_DEBUG_FILE` &ndash; cestu k souboru protokolu ladění.</span><span class="sxs-lookup"><span data-stu-id="cd367-550">`ASPNETCORE_MODULE_DEBUG_FILE` &ndash; Path to the debug log file.</span></span> <span data-ttu-id="cd367-551">(Výchozí: *aspnetcore-Debug. log*)</span><span class="sxs-lookup"><span data-stu-id="cd367-551">(Default: *aspnetcore-debug.log*)</span></span>
* <span data-ttu-id="cd367-552">`ASPNETCORE_MODULE_DEBUG` &ndash; nastavení úrovně ladění.</span><span class="sxs-lookup"><span data-stu-id="cd367-552">`ASPNETCORE_MODULE_DEBUG` &ndash; Debug level setting.</span></span>

> [!WARNING]
> <span data-ttu-id="cd367-553">Nenechávejte **protokolování** ladění povolené v nasazení po delší dobu, než je potřeba k řešení problému.</span><span class="sxs-lookup"><span data-stu-id="cd367-553">Do **not** leave debug logging enabled in the deployment for longer than required to troubleshoot an issue.</span></span> <span data-ttu-id="cd367-554">Velikost souboru protokolu není omezený.</span><span class="sxs-lookup"><span data-stu-id="cd367-554">The size of the log isn't limited.</span></span> <span data-ttu-id="cd367-555">Opuštění protokol ladění povoleno může vyčerpat dostupné místo na disku a chybách u aplikací na serveru nebo služby app service.</span><span class="sxs-lookup"><span data-stu-id="cd367-555">Leaving the debug log enabled can exhaust the available disk space and crash the server or app service.</span></span>

<span data-ttu-id="cd367-556">Příklad `aspNetCore` elementu v souboru *Web. config* naleznete v tématu [Konfigurace pomocí souboru Web. config](#configuration-with-webconfig) .</span><span class="sxs-lookup"><span data-stu-id="cd367-556">See [Configuration with web.config](#configuration-with-webconfig) for an example of the `aspNetCore` element in the *web.config* file.</span></span>

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="cd367-557">Konfigurace proxy serveru používá protokol HTTP a token pro párování</span><span class="sxs-lookup"><span data-stu-id="cd367-557">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="cd367-558">*Platí pouze pro hostování mimo proces.*</span><span class="sxs-lookup"><span data-stu-id="cd367-558">*Only applies to out-of-process hosting.*</span></span>

<span data-ttu-id="cd367-559">Proxy server mezi modul ASP.NET Core a Kestrel používá protokol HTTP.</span><span class="sxs-lookup"><span data-stu-id="cd367-559">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="cd367-560">Neexistuje žádné riziko odposloucháváním provozu mezi modulu a Kestrel z umístění mimo server.</span><span class="sxs-lookup"><span data-stu-id="cd367-560">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="cd367-561">Párování token se používá k zajištění, že požadavků přijatých službou Kestrel byly směrovány přes proxy server službou IIS a nebyl dodán z nějakého jiného zdroje.</span><span class="sxs-lookup"><span data-stu-id="cd367-561">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="cd367-562">Token párování se vytvoří a nastaví na proměnnou prostředí (`ASPNETCORE_TOKEN`) modulem.</span><span class="sxs-lookup"><span data-stu-id="cd367-562">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="cd367-563">Párovací token je také nastaven na hlavičku (`MS-ASPNETCORE-TOKEN`) u každého požadavku proxy.</span><span class="sxs-lookup"><span data-stu-id="cd367-563">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="cd367-564">Služba IIS Middleware kontroly požadavku že přijme potvrďte, že odpovídá párování hodnota tokenu hlavičky hodnotu proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="cd367-564">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="cd367-565">Pokud token hodnoty se neshodují, žádost je zaznamenána a odmítnut.</span><span class="sxs-lookup"><span data-stu-id="cd367-565">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="cd367-566">Párování proměnná tokenu prostředí a přenos dat mezi modulu a Kestrel nejsou dostupné z umístění mimo server.</span><span class="sxs-lookup"><span data-stu-id="cd367-566">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="cd367-567">Nainstalovat bez mého párování hodnota tokenu nelze útočník odesílat požadavky, které obejít kontrolu v IIS middlewaru.</span><span class="sxs-lookup"><span data-stu-id="cd367-567">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="cd367-568">Modul ASP.NET Core s službu IIS sdílenou konfiguraci</span><span class="sxs-lookup"><span data-stu-id="cd367-568">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="cd367-569">Instalační služba modulu ASP.NET Core se spouští s oprávněními účtu **TrustedInstaller** .</span><span class="sxs-lookup"><span data-stu-id="cd367-569">The ASP.NET Core Module installer runs with the privileges of the **TrustedInstaller** account.</span></span> <span data-ttu-id="cd367-570">Vzhledem k tomu, že účet místního systému nemá oprávnění k úpravám pro cestu ke sdílené složce, kterou používá sdílená konfigurace služby IIS, vyvolá Instalační program chybu odepření přístupu při pokusu o konfiguraci nastavení modulu v souboru *ApplicationHost. config* ve sdílené složce.</span><span class="sxs-lookup"><span data-stu-id="cd367-570">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the *applicationHost.config* file on the share.</span></span>

<span data-ttu-id="cd367-571">Pokud používáte sdílenou konfiguraci IIS na stejném počítači jako instalaci služby IIS, spusťte instalační program sady prostředků ASP.NET Core s parametrem `OPT_NO_SHARED_CONFIG_CHECK` nastaveným na `1`:</span><span class="sxs-lookup"><span data-stu-id="cd367-571">When using an IIS Shared Configuration on the same machine as the IIS installation, run the ASP.NET Core Hosting Bundle installer with the `OPT_NO_SHARED_CONFIG_CHECK` parameter set to `1`:</span></span>

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

<span data-ttu-id="cd367-572">Pokud cesta ke sdílené konfiguraci není ve stejném počítači jako instalace služby IIS, postupujte podle těchto kroků:</span><span class="sxs-lookup"><span data-stu-id="cd367-572">When the path to the shared configuration isn't on the same machine as the IIS installation, follow these steps:</span></span>

1. <span data-ttu-id="cd367-573">Zakážete sdílenou konfiguraci IIS.</span><span class="sxs-lookup"><span data-stu-id="cd367-573">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="cd367-574">Spusťte instalační program.</span><span class="sxs-lookup"><span data-stu-id="cd367-574">Run the installer.</span></span>
1. <span data-ttu-id="cd367-575">Exportujte aktualizovaný soubor *ApplicationHost. config* do sdílené složky.</span><span class="sxs-lookup"><span data-stu-id="cd367-575">Export the updated *applicationHost.config* file to the share.</span></span>
1. <span data-ttu-id="cd367-576">Znovu povolte sdílenou konfiguraci IIS.</span><span class="sxs-lookup"><span data-stu-id="cd367-576">Re-enable the IIS Shared Configuration.</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="cd367-577">Verze modulu a hostování sady Instalační protokoly</span><span class="sxs-lookup"><span data-stu-id="cd367-577">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="cd367-578">Chcete-li zjistit verzi nainstalovaný modul ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="cd367-578">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="cd367-579">V hostitelském systému přejděte na *%windir%\system32\inetsrv*.</span><span class="sxs-lookup"><span data-stu-id="cd367-579">On the hosting system, navigate to *%windir%\System32\inetsrv*.</span></span>
1. <span data-ttu-id="cd367-580">Vyhledejte soubor *aspnetcore. dll* .</span><span class="sxs-lookup"><span data-stu-id="cd367-580">Locate the *aspnetcore.dll* file.</span></span>
1. <span data-ttu-id="cd367-581">Klikněte na soubor pravým tlačítkem a v místní nabídce vyberte **vlastnosti** .</span><span class="sxs-lookup"><span data-stu-id="cd367-581">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="cd367-582">Vyberte kartu **Podrobnosti** . **Verze souboru** a **verze produktu** reprezentují nainstalovanou verzi modulu.</span><span class="sxs-lookup"><span data-stu-id="cd367-582">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="cd367-583">Protokoly instalačních balíčků pro modul jsou k dispozici v *C:\\uživatelé\\% username%\\data\\místní\\Temp*. Soubor má název *dd_DotNetCoreWinSvrHosting__\<časové razítko > _000_AspNetCoreModule_x64. log*.</span><span class="sxs-lookup"><span data-stu-id="cd367-583">The Hosting Bundle installer logs for the module are found at *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. The file is named *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="cd367-584">Modul, schéma a konfiguraci umístění souborů</span><span class="sxs-lookup"><span data-stu-id="cd367-584">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="cd367-585">Modul</span><span class="sxs-lookup"><span data-stu-id="cd367-585">Module</span></span>

<span data-ttu-id="cd367-586">**Služba IIS (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="cd367-586">**IIS (x86/amd64):**</span></span>

* <span data-ttu-id="cd367-587">%windir%\System32\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="cd367-587">%windir%\System32\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="cd367-588">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="cd367-588">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="cd367-589">%ProgramFiles%\IIS\Asp.NET core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="cd367-589">%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="cd367-590">% ProgramFiles (x86) %\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="cd367-590">%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

<span data-ttu-id="cd367-591">**IIS Express (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="cd367-591">**IIS Express (x86/amd64):**</span></span>

* <span data-ttu-id="cd367-592">%ProgramFiles%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="cd367-592">%ProgramFiles%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="cd367-593">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="cd367-593">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="cd367-594">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="cd367-594">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="cd367-595">% ProgramFiles (x86) %\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="cd367-595">%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

### <a name="schema"></a><span data-ttu-id="cd367-596">Schéma</span><span class="sxs-lookup"><span data-stu-id="cd367-596">Schema</span></span>

<span data-ttu-id="cd367-597">**SLUŽBU**</span><span class="sxs-lookup"><span data-stu-id="cd367-597">**IIS**</span></span>

* <span data-ttu-id="cd367-598">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="cd367-598">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span></span>

* <span data-ttu-id="cd367-599">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.XML</span><span class="sxs-lookup"><span data-stu-id="cd367-599">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span></span>

<span data-ttu-id="cd367-600">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="cd367-600">**IIS Express**</span></span>

* <span data-ttu-id="cd367-601">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="cd367-601">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span></span>

* <span data-ttu-id="cd367-602">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span><span class="sxs-lookup"><span data-stu-id="cd367-602">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span></span>

### <a name="configuration"></a><span data-ttu-id="cd367-603">Konfigurace</span><span class="sxs-lookup"><span data-stu-id="cd367-603">Configuration</span></span>

<span data-ttu-id="cd367-604">**SLUŽBU**</span><span class="sxs-lookup"><span data-stu-id="cd367-604">**IIS**</span></span>

* <span data-ttu-id="cd367-605">%windir%\System32\inetsrv\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="cd367-605">%windir%\System32\inetsrv\config\applicationHost.config</span></span>

<span data-ttu-id="cd367-606">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="cd367-606">**IIS Express**</span></span>

* <span data-ttu-id="cd367-607">Visual Studio: {ROOT aplikace}\\. vs\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="cd367-607">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span></span>

* <span data-ttu-id="cd367-608">*IISExpress. exe* CLI:%USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span><span class="sxs-lookup"><span data-stu-id="cd367-608">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span></span>

<span data-ttu-id="cd367-609">Soubory lze najít hledáním *aspnetcore* v souboru *ApplicationHost. config* .</span><span class="sxs-lookup"><span data-stu-id="cd367-609">The files can be found by searching for *aspnetcore* in the *applicationHost.config* file.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="cd367-610">Modul ASP.NET Core je nativním modulem služby IIS, který se připojuje k kanálu IIS a přesměrovává webové požadavky na back-endové aplikace ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="cd367-610">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to forward web requests to backend ASP.NET Core apps.</span></span>

<span data-ttu-id="cd367-611">Podporované verze systému Windows:</span><span class="sxs-lookup"><span data-stu-id="cd367-611">Supported Windows versions:</span></span>

* <span data-ttu-id="cd367-612">Windows 7 nebo novější</span><span class="sxs-lookup"><span data-stu-id="cd367-612">Windows 7 or later</span></span>
* <span data-ttu-id="cd367-613">Windows Server 2008 R2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="cd367-613">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="cd367-614">Modul funguje pouze s Kestrel.</span><span class="sxs-lookup"><span data-stu-id="cd367-614">The module only works with Kestrel.</span></span> <span data-ttu-id="cd367-615">Modul není kompatibilní s [http. sys](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="cd367-615">The module is incompatible with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

<span data-ttu-id="cd367-616">Vzhledem k tomu, že ASP.NET Core aplikace běží v procesu odděleném od pracovního procesu služby IIS, modul také zpracovává správu procesů.</span><span class="sxs-lookup"><span data-stu-id="cd367-616">Because ASP.NET Core apps run in a process separate from the IIS worker process, the module also handles process management.</span></span> <span data-ttu-id="cd367-617">Modul spustí proces pro aplikaci ASP.NET Core, když první požadavek dorazí a restartuje aplikaci, pokud dojde k chybě.</span><span class="sxs-lookup"><span data-stu-id="cd367-617">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it crashes.</span></span> <span data-ttu-id="cd367-618">To je v podstatě stejné chování jako u aplikací ASP.NET 4. x spouštěných vnitroprocesově ve službě IIS, které spravuje [Aktivační služba procesů systému Windows (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span><span class="sxs-lookup"><span data-stu-id="cd367-618">This is essentially the same behavior as seen with ASP.NET 4.x apps that run in-process in IIS that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="cd367-619">Následující diagram znázorňuje vztah mezi službou IIS, modulem ASP.NET Core a aplikací:</span><span class="sxs-lookup"><span data-stu-id="cd367-619">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app:</span></span>

![Modul ASP.NET Core](aspnet-core-module/_static/ancm-outofprocess.png)

<span data-ttu-id="cd367-621">Požadavky přicházející z webu do ovladače HTTP. sys v režimu jádra.</span><span class="sxs-lookup"><span data-stu-id="cd367-621">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="cd367-622">Ovladač směruje požadavky do služby IIS na konfigurovaném portu webu, obvykle 80 (HTTP) nebo 443 (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="cd367-622">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="cd367-623">Modul předá požadavky do Kestrel na náhodném portu pro aplikaci, což není port 80 nebo 443.</span><span class="sxs-lookup"><span data-stu-id="cd367-623">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="cd367-624">Modul Určuje port prostřednictvím proměnné prostředí při spuštění a [Služba IIS Integration middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) nakonfiguruje server tak, aby naslouchal `http://localhost:{port}`.</span><span class="sxs-lookup"><span data-stu-id="cd367-624">The module specifies the port via an environment variable at startup, and the [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) configures the server to listen on `http://localhost:{port}`.</span></span> <span data-ttu-id="cd367-625">Budou provedeny další kontroly a požadavky, které nepocházejí z modulu, jsou odmítnuty.</span><span class="sxs-lookup"><span data-stu-id="cd367-625">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="cd367-626">Modul nepodporuje předávání HTTPS, takže požadavky se předávají přes protokol HTTP i v případě, že je služba IIS prostřednictvím protokolu HTTPS přijímá.</span><span class="sxs-lookup"><span data-stu-id="cd367-626">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="cd367-627">Po Kestrel žádosti z modulu se požadavek odešle do kanálu middlewaru ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="cd367-627">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="cd367-628">Kanál middlewaru zpracovává požadavek a předá ho jako instanci `HttpContext` do logiky aplikace.</span><span class="sxs-lookup"><span data-stu-id="cd367-628">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="cd367-629">Middleware přidaný integrací služby IIS: aktualizace schématu, vzdálené IP adresy a pathbase pro přesměrování požadavku do Kestrel.</span><span class="sxs-lookup"><span data-stu-id="cd367-629">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="cd367-630">Odpověď aplikace se předává zpátky do služby IIS, která ji přenáší zpátky do klienta HTTP, který žádost inicioval.</span><span class="sxs-lookup"><span data-stu-id="cd367-630">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="cd367-631">Mnoho nativních modulů, jako je ověřování systému Windows, zůstane aktivní.</span><span class="sxs-lookup"><span data-stu-id="cd367-631">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="cd367-632">Další informace o aktivních modulech služby IIS pomocí modulu ASP.NET Core najdete v tématu <xref:host-and-deploy/iis/modules>.</span><span class="sxs-lookup"><span data-stu-id="cd367-632">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="cd367-633">Modul ASP.NET Core může také:</span><span class="sxs-lookup"><span data-stu-id="cd367-633">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="cd367-634">Nastavte proměnné prostředí pro pracovní proces.</span><span class="sxs-lookup"><span data-stu-id="cd367-634">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="cd367-635">Protokoluje výstup stdout do úložiště souborů pro řešení problémů při spuštění.</span><span class="sxs-lookup"><span data-stu-id="cd367-635">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="cd367-636">Předejte tokeny ověřování systému Windows.</span><span class="sxs-lookup"><span data-stu-id="cd367-636">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="cd367-637">Jak nainstalovat a používat modul ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="cd367-637">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="cd367-638">Pokyny k instalaci modulu ASP.NET Core najdete v tématu [instalace hostující sady .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span><span class="sxs-lookup"><span data-stu-id="cd367-638">For instructions on how to install the ASP.NET Core Module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="cd367-639">Konfigurace pomocí souboru web.config</span><span class="sxs-lookup"><span data-stu-id="cd367-639">Configuration with web.config</span></span>

<span data-ttu-id="cd367-640">ASP.NET Core modul je nakonfigurovaný pomocí oddílu `aspNetCore` uzlu `system.webServer` v souboru *Web. config* webu.</span><span class="sxs-lookup"><span data-stu-id="cd367-640">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="cd367-641">Následující soubor *Web. config* je publikován pro [nasazení závislé na rozhraní](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) a nakonfiguruje modul ASP.NET Core pro zpracování požadavků lokality:</span><span class="sxs-lookup"><span data-stu-id="cd367-641">The following *web.config* file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

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

<span data-ttu-id="cd367-642">Následující soubor *Web. config* je publikován pro [samostatně uzavřené nasazení](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span><span class="sxs-lookup"><span data-stu-id="cd367-642">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

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

<span data-ttu-id="cd367-643">Když se aplikace nasadí do [Azure App Service](https://azure.microsoft.com/services/app-service/), `stdoutLogFile`á cesta je nastavená na `\\?\%home%\LogFiles\stdout`.</span><span class="sxs-lookup"><span data-stu-id="cd367-643">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="cd367-644">Cesta ukládá protokoly stdout do složky *soubory protokolů* , což je umístění, které služba automaticky vytvořila.</span><span class="sxs-lookup"><span data-stu-id="cd367-644">The path saves stdout logs to the *LogFiles* folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="cd367-645">Informace o konfiguraci dílčí aplikace IIS najdete v tématu <xref:host-and-deploy/iis/index#sub-applications>.</span><span class="sxs-lookup"><span data-stu-id="cd367-645">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="cd367-646">Atributy elementu aspNetCore</span><span class="sxs-lookup"><span data-stu-id="cd367-646">Attributes of the aspNetCore element</span></span>

| <span data-ttu-id="cd367-647">Atribut</span><span class="sxs-lookup"><span data-stu-id="cd367-647">Attribute</span></span> | <span data-ttu-id="cd367-648">Popis</span><span class="sxs-lookup"><span data-stu-id="cd367-648">Description</span></span> | <span data-ttu-id="cd367-649">Výchozí</span><span class="sxs-lookup"><span data-stu-id="cd367-649">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="cd367-650">Volitelný atribut řetězce.</span><span class="sxs-lookup"><span data-stu-id="cd367-650">Optional string attribute.</span></span></p><p><span data-ttu-id="cd367-651">Argumenty ke spustitelnému souboru určenému v **processPath**.</span><span class="sxs-lookup"><span data-stu-id="cd367-651">Arguments to the executable specified in **processPath**.</span></span></p>| |
| `disableStartUpErrorPage` | <p><span data-ttu-id="cd367-652">Volitelný logický atribut.</span><span class="sxs-lookup"><span data-stu-id="cd367-652">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="cd367-653">Pokud je nastaveno na true, stránka **502,5 – selhání procesu** se potlačí a stavová stránka 502 konfigurovaná v *souboru Web. config* má přednost.</span><span class="sxs-lookup"><span data-stu-id="cd367-653">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="cd367-654">Volitelný logický atribut.</span><span class="sxs-lookup"><span data-stu-id="cd367-654">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="cd367-655">Při hodnotě true se token předá podřízený proces naslouchání na ASPNETCORE_PORT % jako záhlaví "MS-ASPNETCORE-WINAUTHTOKEN" každý požadavek.</span><span class="sxs-lookup"><span data-stu-id="cd367-655">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="cd367-656">Je odpovědností tento proces pro volání CloseHandle na tento token každý požadavek.</span><span class="sxs-lookup"><span data-stu-id="cd367-656">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `processesPerApplication` | <p><span data-ttu-id="cd367-657">Volitelný celočíselný atribut.</span><span class="sxs-lookup"><span data-stu-id="cd367-657">Optional integer attribute.</span></span></p><p><span data-ttu-id="cd367-658">Určuje počet instancí procesu určeného v nastavení **processPath** , které lze v rámci aplikace vystavit.</span><span class="sxs-lookup"><span data-stu-id="cd367-658">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="cd367-659">Nastavení `processesPerApplication` se nedoporučuje.</span><span class="sxs-lookup"><span data-stu-id="cd367-659">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="cd367-660">Tento atribut bude v budoucí verzi odebrán.</span><span class="sxs-lookup"><span data-stu-id="cd367-660">This attribute will be removed in a future release.</span></span></p> | <span data-ttu-id="cd367-661">Výchozí: `1`</span><span class="sxs-lookup"><span data-stu-id="cd367-661">Default: `1`</span></span><br><span data-ttu-id="cd367-662">Minimum: `1`</span><span class="sxs-lookup"><span data-stu-id="cd367-662">Min: `1`</span></span><br><span data-ttu-id="cd367-663">Maximum: `100`</span><span class="sxs-lookup"><span data-stu-id="cd367-663">Max: `100`</span></span> |
| `processPath` | <p><span data-ttu-id="cd367-664">Požadovaný atribut typu string.</span><span class="sxs-lookup"><span data-stu-id="cd367-664">Required string attribute.</span></span></p><p><span data-ttu-id="cd367-665">Cesta ke spustitelnému souboru, který spustí nějaký proces naslouchání požadavků protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="cd367-665">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="cd367-666">Jsou podporovány relativní cesty.</span><span class="sxs-lookup"><span data-stu-id="cd367-666">Relative paths are supported.</span></span> <span data-ttu-id="cd367-667">Pokud cesta začíná na `.`, je cesta považována za relativní vzhledem k kořenu webu.</span><span class="sxs-lookup"><span data-stu-id="cd367-667">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="cd367-668">Volitelný celočíselný atribut.</span><span class="sxs-lookup"><span data-stu-id="cd367-668">Optional integer attribute.</span></span></p><p><span data-ttu-id="cd367-669">Určuje, kolikrát je povoleno selhání procesu určeného v **processPath** za minutu.</span><span class="sxs-lookup"><span data-stu-id="cd367-669">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="cd367-670">Pokud je tento limit překročen, modulu zastaví spuštění procesu pro zbytek minuty.</span><span class="sxs-lookup"><span data-stu-id="cd367-670">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p> | <span data-ttu-id="cd367-671">Výchozí: `10`</span><span class="sxs-lookup"><span data-stu-id="cd367-671">Default: `10`</span></span><br><span data-ttu-id="cd367-672">Minimum: `0`</span><span class="sxs-lookup"><span data-stu-id="cd367-672">Min: `0`</span></span><br><span data-ttu-id="cd367-673">Maximum: `100`</span><span class="sxs-lookup"><span data-stu-id="cd367-673">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="cd367-674">Atribut volitelný časový interval.</span><span class="sxs-lookup"><span data-stu-id="cd367-674">Optional timespan attribute.</span></span></p><p><span data-ttu-id="cd367-675">Určuje dobu, pro kterou modul ASP.NET Core čeká na odpověď z procesu naslouchání na ASPNETCORE_PORT %.</span><span class="sxs-lookup"><span data-stu-id="cd367-675">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="cd367-676">Ve verzích modulu ASP.NET Core, které byly dodávány s vydáním ASP.NET Core 2,1 nebo novějším, je `requestTimeout` zadána v hodinách, minutách a sekundách.</span><span class="sxs-lookup"><span data-stu-id="cd367-676">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p> | <span data-ttu-id="cd367-677">Výchozí: `00:02:00`</span><span class="sxs-lookup"><span data-stu-id="cd367-677">Default: `00:02:00`</span></span><br><span data-ttu-id="cd367-678">Minimum: `00:00:00`</span><span class="sxs-lookup"><span data-stu-id="cd367-678">Min: `00:00:00`</span></span><br><span data-ttu-id="cd367-679">Maximum: `360:00:00`</span><span class="sxs-lookup"><span data-stu-id="cd367-679">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="cd367-680">Volitelný celočíselný atribut.</span><span class="sxs-lookup"><span data-stu-id="cd367-680">Optional integer attribute.</span></span></p><p><span data-ttu-id="cd367-681">Doba v sekundách, po kterou modul čeká na řádné vypnutí spustitelného souboru, když se zjistí soubor *App_offline. htm* .</span><span class="sxs-lookup"><span data-stu-id="cd367-681">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="cd367-682">Výchozí: `10`</span><span class="sxs-lookup"><span data-stu-id="cd367-682">Default: `10`</span></span><br><span data-ttu-id="cd367-683">Minimum: `0`</span><span class="sxs-lookup"><span data-stu-id="cd367-683">Min: `0`</span></span><br><span data-ttu-id="cd367-684">Maximum: `600`</span><span class="sxs-lookup"><span data-stu-id="cd367-684">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="cd367-685">Volitelný celočíselný atribut.</span><span class="sxs-lookup"><span data-stu-id="cd367-685">Optional integer attribute.</span></span></p><p><span data-ttu-id="cd367-686">Doba trvání v sekundách, které modul čeká na spustitelný soubor ke spuštění procesu naslouchání na portu.</span><span class="sxs-lookup"><span data-stu-id="cd367-686">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="cd367-687">Pokud je překročen časový limit, modul ukončí proces.</span><span class="sxs-lookup"><span data-stu-id="cd367-687">If this time limit is exceeded, the module kills the process.</span></span> <span data-ttu-id="cd367-688">Modul se pokusí znovu spustit proces, když obdrží novou žádost, a pokračuje v pokusu o restartování procesu u dalších příchozích požadavků, pokud se nepodaří spustit **rapidFailsPerMinute** počet pokusů během poslední minuty.</span><span class="sxs-lookup"><span data-stu-id="cd367-688">The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="cd367-689">Hodnota 0 (nula **) není považována za** nekonečný časový limit.</span><span class="sxs-lookup"><span data-stu-id="cd367-689">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="cd367-690">Výchozí: `120`</span><span class="sxs-lookup"><span data-stu-id="cd367-690">Default: `120`</span></span><br><span data-ttu-id="cd367-691">Minimum: `0`</span><span class="sxs-lookup"><span data-stu-id="cd367-691">Min: `0`</span></span><br><span data-ttu-id="cd367-692">Maximum: `3600`</span><span class="sxs-lookup"><span data-stu-id="cd367-692">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="cd367-693">Volitelný logický atribut.</span><span class="sxs-lookup"><span data-stu-id="cd367-693">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="cd367-694">Je-li nastavena hodnota true, **stdout** a **stderr** pro proces zadaný v **processPath** budou přesměrovány do souboru zadaného v **stdoutLogFile**.</span><span class="sxs-lookup"><span data-stu-id="cd367-694">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="cd367-695">Volitelný atribut řetězce.</span><span class="sxs-lookup"><span data-stu-id="cd367-695">Optional string attribute.</span></span></p><p><span data-ttu-id="cd367-696">Určuje relativní nebo absolutní cestu k souboru, pro který je **stdout** a **stderr** z procesu určeného v **processPath** protokolováno.</span><span class="sxs-lookup"><span data-stu-id="cd367-696">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="cd367-697">Relativní cesty jsou relativní vzhledem k kořen webu.</span><span class="sxs-lookup"><span data-stu-id="cd367-697">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="cd367-698">Všechny cesty začínající na `.` jsou relativní vzhledem k kořenu webu a všechny ostatní cesty se považují za absolutní cesty.</span><span class="sxs-lookup"><span data-stu-id="cd367-698">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="cd367-699">Všechny složky v cestě k dispozici musí existovat v pořadí pro modul pro vytvoření souboru protokolu.</span><span class="sxs-lookup"><span data-stu-id="cd367-699">Any folders provided in the path must exist in order for the module to create the log file.</span></span> <span data-ttu-id="cd367-700">Pomocí oddělovačů podtržítka se do posledního segmentu cesty **stdoutLogFile** přidá časové razítko, ID procesu a Přípona souboru ( *. log*).</span><span class="sxs-lookup"><span data-stu-id="cd367-700">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="cd367-701">Pokud je jako hodnota zadána `.\logs\stdout`, je ukázkový protokol stdout uložen jako *stdout_20180205194132_1934. log* ve složce *logs* při uložení na 2/5/2018 v 19:41:32 s ID procesu 1934.</span><span class="sxs-lookup"><span data-stu-id="cd367-701">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

### <a name="setting-environment-variables"></a><span data-ttu-id="cd367-702">Nastavení proměnných prostředí</span><span class="sxs-lookup"><span data-stu-id="cd367-702">Setting environment variables</span></span>

<span data-ttu-id="cd367-703">Proměnné prostředí lze zadat pro proces v atributu `processPath`.</span><span class="sxs-lookup"><span data-stu-id="cd367-703">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="cd367-704">Zadejte proměnnou prostředí s `<environmentVariable>` podřízeným elementem elementu `<environmentVariables>` kolekce.</span><span class="sxs-lookup"><span data-stu-id="cd367-704">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span>

> [!WARNING]
> <span data-ttu-id="cd367-705">Proměnné prostředí nastavené v této části jsou v konfliktu se sadou proměnných prostředí systému se stejným názvem.</span><span class="sxs-lookup"><span data-stu-id="cd367-705">Environment variables set in this section conflict with system environment variables set with the same name.</span></span> <span data-ttu-id="cd367-706">Pokud je proměnná prostředí nastavena v souboru *Web. config* i na úrovni systému systému Windows, bude hodnota ze souboru *Web. config* připojena k hodnotě proměnné prostředí systému (například `ASPNETCORE_ENVIRONMENT: Development;Development`), což zabrání spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="cd367-706">If an environment variable is set in both the *web.config* file and at the system level in Windows, the value from the *web.config* file becomes appended to the system environment variable value (for example, `ASPNETCORE_ENVIRONMENT: Development;Development`), which prevents the app from starting.</span></span>

<span data-ttu-id="cd367-707">Následující příklad nastaví dvou proměnných prostředí.</span><span class="sxs-lookup"><span data-stu-id="cd367-707">The following example sets two environment variables.</span></span> <span data-ttu-id="cd367-708">`ASPNETCORE_ENVIRONMENT` nakonfiguruje prostředí aplikace tak, aby bylo `Development`.</span><span class="sxs-lookup"><span data-stu-id="cd367-708">`ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="cd367-709">Vývojář může tuto hodnotu dočasně nastavit v souboru *Web. config* , aby bylo možné vynutit, aby se [Stránka s výjimkou vývojářů](xref:fundamentals/error-handling) načetla při ladění výjimky aplikace.</span><span class="sxs-lookup"><span data-stu-id="cd367-709">A developer may temporarily set this value in the *web.config* file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="cd367-710">`CONFIG_DIR` je příklad uživatelsky definované proměnné prostředí, kde vývojář napsal kód, který přečte hodnotu při spuštění, aby vytvořil cestu pro načtení konfiguračního souboru aplikace.</span><span class="sxs-lookup"><span data-stu-id="cd367-710">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

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
> <span data-ttu-id="cd367-711">Nastavte pouze proměnnou prostředí `ASPNETCORE_ENVIRONMENT`, aby `Development` na pracovních a testovacích serverech, které nejsou přístupné nedůvěryhodným sítím, jako je například Internet.</span><span class="sxs-lookup"><span data-stu-id="cd367-711">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## <a name="app_offlinehtm"></a><span data-ttu-id="cd367-712">app_offline.htm</span><span class="sxs-lookup"><span data-stu-id="cd367-712">app_offline.htm</span></span>

<span data-ttu-id="cd367-713">Pokud se v kořenovém adresáři aplikace zjistí soubor s názvem *App_offline. htm* , modul ASP.NET Core se pokusí aplikaci řádně vypnout a zastavit zpracování příchozích požadavků.</span><span class="sxs-lookup"><span data-stu-id="cd367-713">If a file with the name *app_offline.htm* is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="cd367-714">Pokud aplikace běží i po uplynutí počtu sekund definovaného v `shutdownTimeLimit`, modul ASP.NET Core ukončuje běžící proces.</span><span class="sxs-lookup"><span data-stu-id="cd367-714">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="cd367-715">Když je přítomen soubor *App_offline. htm* , ASP.NET Core modul reaguje na požadavky odesláním zpět obsahu souboru *App_offline. htm* .</span><span class="sxs-lookup"><span data-stu-id="cd367-715">While the *app_offline.htm* file is present, the ASP.NET Core Module responds to requests by sending back the contents of the *app_offline.htm* file.</span></span> <span data-ttu-id="cd367-716">Po odebrání souboru *App_offline. htm* aplikace spustí další požadavek.</span><span class="sxs-lookup"><span data-stu-id="cd367-716">When the *app_offline.htm* file is removed, the next request starts the app.</span></span>

## <a name="start-up-error-page"></a><span data-ttu-id="cd367-717">Spuštění chybovou stránku</span><span class="sxs-lookup"><span data-stu-id="cd367-717">Start-up error page</span></span>

<span data-ttu-id="cd367-718">Pokud se v modulu ASP.NET Core nepodaří spustit back-end proces nebo se spustí back-end proces, ale nenaslouchá na konfigurovaném portu, zobrazí se stránka s kódem stavu *selhání procesu 502,5* .</span><span class="sxs-lookup"><span data-stu-id="cd367-718">If the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span> <span data-ttu-id="cd367-719">Chcete-li tuto stránku potlačit a vrátit se na výchozí stavovou stránku služby IIS 502, použijte atribut `disableStartUpErrorPage`.</span><span class="sxs-lookup"><span data-stu-id="cd367-719">To suppress this page and revert to the default IIS 502 status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="cd367-720">Další informace o konfiguraci vlastních chybových zpráv najdete v tématu [chyby protokolu HTTP \<httpErrors >](/iis/configuration/system.webServer/httpErrors/).</span><span class="sxs-lookup"><span data-stu-id="cd367-720">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

![502.5 proces selhání stav znaková stránka](aspnet-core-module/_static/ANCM-502_5.png)

## <a name="log-creation-and-redirection"></a><span data-ttu-id="cd367-722">Vytvoření protokolu a přesměrování</span><span class="sxs-lookup"><span data-stu-id="cd367-722">Log creation and redirection</span></span>

<span data-ttu-id="cd367-723">Modul ASP.NET Core přesměruje výstup konzoly stdout a stderr na disk, pokud jsou nastaveny atributy `stdoutLogEnabled` a `stdoutLogFile` elementu `aspNetCore`.</span><span class="sxs-lookup"><span data-stu-id="cd367-723">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="cd367-724">Všechny složky v `stdoutLogFile` cestě jsou vytvořeny modulem při vytvoření souboru protokolu.</span><span class="sxs-lookup"><span data-stu-id="cd367-724">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="cd367-725">Fond aplikací musí mít oprávnění k zápisu do umístění, kam se zapisují protokoly (k poskytnutí oprávnění k zápisu použijte `IIS AppPool\<app_pool_name>`).</span><span class="sxs-lookup"><span data-stu-id="cd367-725">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="cd367-726">Protokoly nejsou střídán, pokud dojde k recyklování procesů/restartování.</span><span class="sxs-lookup"><span data-stu-id="cd367-726">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="cd367-727">Je odpovědností hostitel k omezení místa na disku, které využijete v protokolech.</span><span class="sxs-lookup"><span data-stu-id="cd367-727">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="cd367-728">Použití protokolu stdout se doporučuje pouze při řešení potíží se spouštěním aplikací při hostování služby IIS nebo při použití [podpory v době vývoje služby IIS se sadou Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), nikoli při ladění místně a při spuštění aplikace s IIS Express.</span><span class="sxs-lookup"><span data-stu-id="cd367-728">Using the stdout log is only recommended for troubleshooting app startup issues when hosting on IIS or when using [development-time support for IIS with Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), not while debugging locally and running the app with IIS Express.</span></span>

<span data-ttu-id="cd367-729">Nepoužívejte stdout protokolu pro účely protokolování obecné aplikace.</span><span class="sxs-lookup"><span data-stu-id="cd367-729">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="cd367-730">Pro rutiny protokolování v aplikaci ASP.NET Core, použijte protokolování knihovnu, která omezuje velikost souboru protokolu a otočí protokoly.</span><span class="sxs-lookup"><span data-stu-id="cd367-730">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="cd367-731">Další informace najdete v tématu [Zprostředkovatelé protokolování třetích stran](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="cd367-731">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="cd367-732">Časové razítko a soubor rozšíření jsou přidány automaticky při vytvoření souboru protokolu.</span><span class="sxs-lookup"><span data-stu-id="cd367-732">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="cd367-733">Název souboru protokolu se skládá tak, že se připojí časové razítko, ID procesu a Přípona souboru ( *. log*) k poslednímu segmentu `stdoutLogFile` cesty (obvykle *stdout*) oddělené podtržítky.</span><span class="sxs-lookup"><span data-stu-id="cd367-733">The log file name is composed by appending the timestamp, process ID, and file extension (*.log*) to the last segment of the `stdoutLogFile` path (typically *stdout*) delimited by underscores.</span></span> <span data-ttu-id="cd367-734">Pokud `stdoutLogFile` cesta končí na *stdout*, protokol pro aplikaci s číslem PID 1934 vytvořeným v 2/5/2018 v 19:42:32 má název souboru *stdout_20180205194132_1934. log*.</span><span class="sxs-lookup"><span data-stu-id="cd367-734">If the `stdoutLogFile` path ends with *stdout*, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name *stdout_20180205194132_1934.log*.</span></span>

<span data-ttu-id="cd367-735">Následující vzorový `aspNetCore` prvek nakonfiguruje protokolování stdout na relativní cestě `.\log\`.</span><span class="sxs-lookup"><span data-stu-id="cd367-735">The following sample `aspNetCore` element configures stdout logging at the relative path `.\log\`.</span></span> <span data-ttu-id="cd367-736">Ověřte, jestli uživatel identita fondu aplikací má oprávnění k zápisu do zadaná výstupní cesta.</span><span class="sxs-lookup"><span data-stu-id="cd367-736">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout">
</aspNetCore>
```

<span data-ttu-id="cd367-737">Když publikujete aplikaci pro nasazení Azure App Service, Webová sada SDK nastaví hodnotu `stdoutLogFile` na `\\?\%home%\LogFiles\stdout`.</span><span class="sxs-lookup"><span data-stu-id="cd367-737">When publishing an app for Azure App Service deployment, the Web SDK sets the `stdoutLogFile` value to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="cd367-738">Pro aplikace hostované v Azure App Service je předdefinovaná proměnná prostředí `%home`.</span><span class="sxs-lookup"><span data-stu-id="cd367-738">The `%home` environment variable is predefined for apps hosted by Azure App Service.</span></span>

<span data-ttu-id="cd367-739">Chcete-li vytvořit pravidla filtru protokolování, přečtěte si část [Konfigurace](xref:fundamentals/logging/index#log-filtering) a [filtrování protokolů](xref:fundamentals/logging/index#log-filtering) v dokumentaci ASP.NET Core protokolování.</span><span class="sxs-lookup"><span data-stu-id="cd367-739">To create logging filter rules, see the [Configuration](xref:fundamentals/logging/index#log-filtering) and [Log filtering](xref:fundamentals/logging/index#log-filtering) sections of the ASP.NET Core logging documentation.</span></span>

<span data-ttu-id="cd367-740">Další informace o formátech cest najdete v tématu [formáty souborů cest v systémech Windows](/dotnet/standard/io/file-path-formats).</span><span class="sxs-lookup"><span data-stu-id="cd367-740">For more information on path formats, see [File path formats on Windows systems](/dotnet/standard/io/file-path-formats).</span></span>

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="cd367-741">Konfigurace proxy serveru používá protokol HTTP a token pro párování</span><span class="sxs-lookup"><span data-stu-id="cd367-741">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="cd367-742">Proxy server mezi modul ASP.NET Core a Kestrel používá protokol HTTP.</span><span class="sxs-lookup"><span data-stu-id="cd367-742">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="cd367-743">Neexistuje žádné riziko odposloucháváním provozu mezi modulu a Kestrel z umístění mimo server.</span><span class="sxs-lookup"><span data-stu-id="cd367-743">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="cd367-744">Párování token se používá k zajištění, že požadavků přijatých službou Kestrel byly směrovány přes proxy server službou IIS a nebyl dodán z nějakého jiného zdroje.</span><span class="sxs-lookup"><span data-stu-id="cd367-744">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="cd367-745">Token párování se vytvoří a nastaví na proměnnou prostředí (`ASPNETCORE_TOKEN`) modulem.</span><span class="sxs-lookup"><span data-stu-id="cd367-745">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="cd367-746">Párovací token je také nastaven na hlavičku (`MS-ASPNETCORE-TOKEN`) u každého požadavku proxy.</span><span class="sxs-lookup"><span data-stu-id="cd367-746">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="cd367-747">Služba IIS Middleware kontroly požadavku že přijme potvrďte, že odpovídá párování hodnota tokenu hlavičky hodnotu proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="cd367-747">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="cd367-748">Pokud token hodnoty se neshodují, žádost je zaznamenána a odmítnut.</span><span class="sxs-lookup"><span data-stu-id="cd367-748">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="cd367-749">Párování proměnná tokenu prostředí a přenos dat mezi modulu a Kestrel nejsou dostupné z umístění mimo server.</span><span class="sxs-lookup"><span data-stu-id="cd367-749">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="cd367-750">Nainstalovat bez mého párování hodnota tokenu nelze útočník odesílat požadavky, které obejít kontrolu v IIS middlewaru.</span><span class="sxs-lookup"><span data-stu-id="cd367-750">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="cd367-751">Modul ASP.NET Core s službu IIS sdílenou konfiguraci</span><span class="sxs-lookup"><span data-stu-id="cd367-751">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="cd367-752">Instalační služba modulu ASP.NET Core se spouští s oprávněními účtu **TrustedInstaller** .</span><span class="sxs-lookup"><span data-stu-id="cd367-752">The ASP.NET Core Module installer runs with the privileges of the **TrustedInstaller** account.</span></span> <span data-ttu-id="cd367-753">Vzhledem k tomu, že účet místního systému nemá oprávnění k úpravám pro cestu ke sdílené složce, kterou používá sdílená konfigurace služby IIS, vyvolá Instalační program chybu odepření přístupu při pokusu o konfiguraci nastavení modulu v souboru *ApplicationHost. config* ve sdílené složce.</span><span class="sxs-lookup"><span data-stu-id="cd367-753">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the *applicationHost.config* file on the share.</span></span>

<span data-ttu-id="cd367-754">Pokud používáte sdílenou konfiguraci IIS, postupujte podle těchto kroků:</span><span class="sxs-lookup"><span data-stu-id="cd367-754">When using an IIS Shared Configuration, follow these steps:</span></span>

1. <span data-ttu-id="cd367-755">Zakážete sdílenou konfiguraci IIS.</span><span class="sxs-lookup"><span data-stu-id="cd367-755">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="cd367-756">Spusťte instalační program.</span><span class="sxs-lookup"><span data-stu-id="cd367-756">Run the installer.</span></span>
1. <span data-ttu-id="cd367-757">Exportujte aktualizovaný soubor *ApplicationHost. config* do sdílené složky.</span><span class="sxs-lookup"><span data-stu-id="cd367-757">Export the updated *applicationHost.config* file to the share.</span></span>
1. <span data-ttu-id="cd367-758">Znovu povolte sdílenou konfiguraci IIS.</span><span class="sxs-lookup"><span data-stu-id="cd367-758">Re-enable the IIS Shared Configuration.</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="cd367-759">Verze modulu a hostování sady Instalační protokoly</span><span class="sxs-lookup"><span data-stu-id="cd367-759">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="cd367-760">Chcete-li zjistit verzi nainstalovaný modul ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="cd367-760">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="cd367-761">V hostitelském systému přejděte na *%windir%\system32\inetsrv*.</span><span class="sxs-lookup"><span data-stu-id="cd367-761">On the hosting system, navigate to *%windir%\System32\inetsrv*.</span></span>
1. <span data-ttu-id="cd367-762">Vyhledejte soubor *aspnetcore. dll* .</span><span class="sxs-lookup"><span data-stu-id="cd367-762">Locate the *aspnetcore.dll* file.</span></span>
1. <span data-ttu-id="cd367-763">Klikněte na soubor pravým tlačítkem a v místní nabídce vyberte **vlastnosti** .</span><span class="sxs-lookup"><span data-stu-id="cd367-763">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="cd367-764">Vyberte kartu **Podrobnosti** . **Verze souboru** a **verze produktu** reprezentují nainstalovanou verzi modulu.</span><span class="sxs-lookup"><span data-stu-id="cd367-764">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="cd367-765">Protokoly instalačních balíčků pro modul jsou k dispozici v *C:\\uživatelé\\% username%\\data\\místní\\Temp*. Soubor má název *dd_DotNetCoreWinSvrHosting__\<časové razítko > _000_AspNetCoreModule_x64. log*.</span><span class="sxs-lookup"><span data-stu-id="cd367-765">The Hosting Bundle installer logs for the module are found at *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. The file is named *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="cd367-766">Modul, schéma a konfiguraci umístění souborů</span><span class="sxs-lookup"><span data-stu-id="cd367-766">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="cd367-767">Modul</span><span class="sxs-lookup"><span data-stu-id="cd367-767">Module</span></span>

<span data-ttu-id="cd367-768">**Služba IIS (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="cd367-768">**IIS (x86/amd64):**</span></span>

* <span data-ttu-id="cd367-769">%windir%\System32\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="cd367-769">%windir%\System32\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="cd367-770">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="cd367-770">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span></span>

<span data-ttu-id="cd367-771">**IIS Express (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="cd367-771">**IIS Express (x86/amd64):**</span></span>

* <span data-ttu-id="cd367-772">%ProgramFiles%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="cd367-772">%ProgramFiles%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="cd367-773">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="cd367-773">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span></span>

### <a name="schema"></a><span data-ttu-id="cd367-774">Schéma</span><span class="sxs-lookup"><span data-stu-id="cd367-774">Schema</span></span>

<span data-ttu-id="cd367-775">**SLUŽBU**</span><span class="sxs-lookup"><span data-stu-id="cd367-775">**IIS**</span></span>

* <span data-ttu-id="cd367-776">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="cd367-776">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span></span>

<span data-ttu-id="cd367-777">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="cd367-777">**IIS Express**</span></span>

* <span data-ttu-id="cd367-778">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="cd367-778">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span></span>

### <a name="configuration"></a><span data-ttu-id="cd367-779">Konfigurace</span><span class="sxs-lookup"><span data-stu-id="cd367-779">Configuration</span></span>

<span data-ttu-id="cd367-780">**SLUŽBU**</span><span class="sxs-lookup"><span data-stu-id="cd367-780">**IIS**</span></span>

* <span data-ttu-id="cd367-781">%windir%\System32\inetsrv\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="cd367-781">%windir%\System32\inetsrv\config\applicationHost.config</span></span>

<span data-ttu-id="cd367-782">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="cd367-782">**IIS Express**</span></span>

* <span data-ttu-id="cd367-783">Visual Studio: {ROOT aplikace}\\. vs\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="cd367-783">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span></span>

* <span data-ttu-id="cd367-784">*IISExpress. exe* CLI:%USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span><span class="sxs-lookup"><span data-stu-id="cd367-784">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span></span>

<span data-ttu-id="cd367-785">Soubory lze najít hledáním *aspnetcore* v souboru *ApplicationHost. config* .</span><span class="sxs-lookup"><span data-stu-id="cd367-785">The files can be found by searching for *aspnetcore* in the *applicationHost.config* file.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="cd367-786">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="cd367-786">Additional resources</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/azure-apps/index>
* <span data-ttu-id="cd367-787">[Zdrojový odkaz na modul ASP.NET Core (hlavní větev)](https://github.com/dotnet/aspnetcore/tree/master/src/Servers/IIS/AspNetCoreModuleV2) &ndash; použít rozevírací seznam **větve** k výběru konkrétní verze (například `release/3.1`).</span><span class="sxs-lookup"><span data-stu-id="cd367-787">[ASP.NET Core Module reference source (master branch)](https://github.com/dotnet/aspnetcore/tree/master/src/Servers/IIS/AspNetCoreModuleV2) &ndash; Use the **Branch** drop down list to select a specific release (for example, `release/3.1`).</span></span>
* <xref:host-and-deploy/iis/modules>
