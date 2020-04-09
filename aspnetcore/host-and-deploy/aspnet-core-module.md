---
title: Modul ASP.NET Core
author: rick-anderson
description: Přečtěte si, jak nakonfigurovat základní modul ASP.NET pro hostování aplikací ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2020
uid: host-and-deploy/aspnet-core-module
ms.openlocfilehash: 298d424557600735668217e1ef07ace606dac60b
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78667298"
---
# <a name="aspnet-core-module"></a><span data-ttu-id="78c3b-103">Modul ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="78c3b-103">ASP.NET Core Module</span></span>

<span data-ttu-id="78c3b-104">[Tom Dykstra](https://github.com/tdykstra), [Rick Strahl](https://github.com/RickStrahl), [Chris Ross](https://github.com/Tratcher), [Rick Anderson](https://twitter.com/RickAndMSFT), [Sourabh Shirhatti](https://twitter.com/sshirhatti), a [Justin Kotalik](https://github.com/jkotalik)</span><span class="sxs-lookup"><span data-stu-id="78c3b-104">By [Tom Dykstra](https://github.com/tdykstra), [Rick Strahl](https://github.com/RickStrahl), [Chris Ross](https://github.com/Tratcher), [Rick Anderson](https://twitter.com/RickAndMSFT), [Sourabh Shirhatti](https://twitter.com/sshirhatti), and [Justin Kotalik](https://github.com/jkotalik)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="78c3b-105">Základní modul ASP.NET je nativní modul iis, který se zapojuje do kanálu iis a provádí následující:</span><span class="sxs-lookup"><span data-stu-id="78c3b-105">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to either:</span></span>

* <span data-ttu-id="78c3b-106">Hostujte aplikaci ASP.NET Core uvnitř pracovního procesu služby IIS (`w3wp.exe`), nazývanou model hostování v [procesu](#in-process-hosting-model).</span><span class="sxs-lookup"><span data-stu-id="78c3b-106">Host an ASP.NET Core app inside of the IIS worker process (`w3wp.exe`), called the [in-process hosting model](#in-process-hosting-model).</span></span>
* <span data-ttu-id="78c3b-107">Přepošle webové požadavky do back-endu ASP.NET aplikaci Core se systémem [Kestrel server](xref:fundamentals/servers/kestrel), nazvaný [mimoprocesový model hostování](#out-of-process-hosting-model).</span><span class="sxs-lookup"><span data-stu-id="78c3b-107">Forward web requests to a backend ASP.NET Core app running the [Kestrel server](xref:fundamentals/servers/kestrel), called the [out-of-process hosting model](#out-of-process-hosting-model).</span></span>

<span data-ttu-id="78c3b-108">Podporované verze systému Windows:</span><span class="sxs-lookup"><span data-stu-id="78c3b-108">Supported Windows versions:</span></span>

* <span data-ttu-id="78c3b-109">Windows 7 nebo novější</span><span class="sxs-lookup"><span data-stu-id="78c3b-109">Windows 7 or later</span></span>
* <span data-ttu-id="78c3b-110">Windows Server 2008 R2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="78c3b-110">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="78c3b-111">Při hostování v procesu modul používá pro službu IIS implementaci serveru`IISHttpServer`v procesu nazvanou IIS HTTP Server ( ).</span><span class="sxs-lookup"><span data-stu-id="78c3b-111">When hosting in-process, the module uses an in-process server implementation for IIS, called IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="78c3b-112">Při hostování mimo proces modul pracuje pouze s Kestrel.</span><span class="sxs-lookup"><span data-stu-id="78c3b-112">When hosting out-of-process, the module only works with Kestrel.</span></span> <span data-ttu-id="78c3b-113">Modul nefunguje s [HTTP.sys](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="78c3b-113">The module doesn't function with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

## <a name="hosting-models"></a><span data-ttu-id="78c3b-114">Modely hostingu</span><span class="sxs-lookup"><span data-stu-id="78c3b-114">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="78c3b-115">Model hostování v procesu</span><span class="sxs-lookup"><span data-stu-id="78c3b-115">In-process hosting model</span></span>

<span data-ttu-id="78c3b-116">ASP.NET základní aplikace výchozí pro model hostování v procesu.</span><span class="sxs-lookup"><span data-stu-id="78c3b-116">ASP.NET Core apps default to the in-process hosting model.</span></span>

<span data-ttu-id="78c3b-117">Následující charakteristiky platí při hostování v průběhu procesu:</span><span class="sxs-lookup"><span data-stu-id="78c3b-117">The following characteristics apply when hosting in-process:</span></span>

* <span data-ttu-id="78c3b-118">Místo serveru`IISHttpServer` [Kestrel](xref:fundamentals/servers/kestrel) se používá server HTTP služby IIS ( ).</span><span class="sxs-lookup"><span data-stu-id="78c3b-118">IIS HTTP Server (`IISHttpServer`) is used instead of [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span> <span data-ttu-id="78c3b-119">Pro v průběhu procesu <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) volá na:</span><span class="sxs-lookup"><span data-stu-id="78c3b-119">For in-process, [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> to:</span></span>

  * <span data-ttu-id="78c3b-120">Zaregistrujte `IISHttpServer`soubor .</span><span class="sxs-lookup"><span data-stu-id="78c3b-120">Register the `IISHttpServer`.</span></span>
  * <span data-ttu-id="78c3b-121">Nakonfigurujte port a základní cestu, kterou by měl server poslouchat při spuštění za ASP.NET základního modulu.</span><span class="sxs-lookup"><span data-stu-id="78c3b-121">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
  * <span data-ttu-id="78c3b-122">Nakonfigurujte hostitele tak, aby zaznamenával chyby při spuštění.</span><span class="sxs-lookup"><span data-stu-id="78c3b-122">Configure the host to capture startup errors.</span></span>

* <span data-ttu-id="78c3b-123">Atribut [requestTimeout](#attributes-of-the-aspnetcore-element) se nevztahuje na hostování v procesu.</span><span class="sxs-lookup"><span data-stu-id="78c3b-123">The [requestTimeout attribute](#attributes-of-the-aspnetcore-element) doesn't apply to in-process hosting.</span></span>

* <span data-ttu-id="78c3b-124">Sdílení fondu aplikací mezi aplikacemi není podporované.</span><span class="sxs-lookup"><span data-stu-id="78c3b-124">Sharing an app pool among apps isn't supported.</span></span> <span data-ttu-id="78c3b-125">Použijte jeden fond aplikací pro jednu aplikaci.</span><span class="sxs-lookup"><span data-stu-id="78c3b-125">Use one app pool per app.</span></span>

* <span data-ttu-id="78c3b-126">Při použití [nasazení na webu](/iis/publish/using-web-deploy/introduction-to-web-deploy) nebo ručním [umístěním souboru app_offline.htm do nasazení](xref:host-and-deploy/iis/index#locked-deployment-files)nemusí být aplikace okamžitě ukončena, pokud existuje otevřené připojení.</span><span class="sxs-lookup"><span data-stu-id="78c3b-126">When using [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) or manually placing an [app_offline.htm file in the deployment](xref:host-and-deploy/iis/index#locked-deployment-files), the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="78c3b-127">Například připojení websocket může zpozdit vypnutí aplikace.</span><span class="sxs-lookup"><span data-stu-id="78c3b-127">For example, a websocket connection may delay app shut down.</span></span>

* <span data-ttu-id="78c3b-128">Architektura (bitness) aplikace a nainstalovaný runtime (x64 nebo x86) musí odpovídat architektuře fondu aplikací.</span><span class="sxs-lookup"><span data-stu-id="78c3b-128">The architecture (bitness) of the app and installed runtime (x64 or x86) must match the architecture of the app pool.</span></span>

* <span data-ttu-id="78c3b-129">Jsou zjištěna odpojení klienta.</span><span class="sxs-lookup"><span data-stu-id="78c3b-129">Client disconnects are detected.</span></span> <span data-ttu-id="78c3b-130">Token zrušení [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) je zrušen, když se klient odpojí.</span><span class="sxs-lookup"><span data-stu-id="78c3b-130">The [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) cancellation token is cancelled when the client disconnects.</span></span>

* <span data-ttu-id="78c3b-131">V ASP.NET jádrem 2.2.1 nebo starším <xref:System.IO.Directory.GetCurrentDirectory*> vrátí pracovní adresář procesu spuštěného službou IIS, nikoli adresář aplikace (například *C:\Windows\System32\inetsrv* pro *w3wp.exe*).</span><span class="sxs-lookup"><span data-stu-id="78c3b-131">In ASP.NET Core 2.2.1 or earlier, <xref:System.IO.Directory.GetCurrentDirectory*> returns the worker directory of the process started by IIS rather than the app's directory (for example, *C:\Windows\System32\inetsrv* for *w3wp.exe*).</span></span>

  <span data-ttu-id="78c3b-132">Ukázkový kód, který nastavuje aktuální adresář aplikace, naleznete ve [třídě CurrentDirectoryHelpers](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/3.x/CurrentDirectoryHelpers.cs).</span><span class="sxs-lookup"><span data-stu-id="78c3b-132">For sample code that sets the app's current directory, see the [CurrentDirectoryHelpers class](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/3.x/CurrentDirectoryHelpers.cs).</span></span> <span data-ttu-id="78c3b-133">Volání `SetCurrentDirectory` metody.</span><span class="sxs-lookup"><span data-stu-id="78c3b-133">Call the `SetCurrentDirectory` method.</span></span> <span data-ttu-id="78c3b-134">Následná <xref:System.IO.Directory.GetCurrentDirectory*> volání k poskytnutí adresáře aplikace.</span><span class="sxs-lookup"><span data-stu-id="78c3b-134">Subsequent calls to <xref:System.IO.Directory.GetCurrentDirectory*> provide the app's directory.</span></span>

* <span data-ttu-id="78c3b-135">Při hostování v <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> procesu, není volána interně inicializovat uživatele.</span><span class="sxs-lookup"><span data-stu-id="78c3b-135">When hosting in-process, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span> <span data-ttu-id="78c3b-136">Proto <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementace slouží k transformaci deklarací identity po každém ověřování není aktivován ve výchozím nastavení.</span><span class="sxs-lookup"><span data-stu-id="78c3b-136">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="78c3b-137">Při transformaci deklarací <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> identity <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> s implementací volejte a přidejte ověřovací služby:</span><span class="sxs-lookup"><span data-stu-id="78c3b-137">When transforming claims with an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation, call <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> to add authentication services:</span></span>

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
  
  * <span data-ttu-id="78c3b-138">[Nasazení webového balíčku (jednoho souboru) nejsou podporována.](/aspnet/web-forms/overview/deployment/web-deployment-in-the-enterprise/deploying-web-packages)</span><span class="sxs-lookup"><span data-stu-id="78c3b-138">[Web Package (single-file) deployments](/aspnet/web-forms/overview/deployment/web-deployment-in-the-enterprise/deploying-web-packages) aren't supported.</span></span>

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="78c3b-139">Model hostování mimo proces</span><span class="sxs-lookup"><span data-stu-id="78c3b-139">Out-of-process hosting model</span></span>

<span data-ttu-id="78c3b-140">Chcete-li nakonfigurovat aplikaci pro mimoprocesový hosting, nastavte hodnotu `<AspNetCoreHostingModel>` vlastnosti v `OutOfProcess` souboru projektu (*.csproj*):</span><span class="sxs-lookup"><span data-stu-id="78c3b-140">To configure an app for out-of-process hosting, set the value of the `<AspNetCoreHostingModel>` property to `OutOfProcess` in the project file (*.csproj*):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="78c3b-141">Hostování v procesu `InProcess`je nastaveno pomocí , což je výchozí hodnota.</span><span class="sxs-lookup"><span data-stu-id="78c3b-141">In-process hosting is set with `InProcess`, which is the default value.</span></span>

<span data-ttu-id="78c3b-142">Hodnota `<AspNetCoreHostingModel>` je malá a velká `inprocess` `outofprocess` písmena, tak a jsou platné hodnoty.</span><span class="sxs-lookup"><span data-stu-id="78c3b-142">The value of `<AspNetCoreHostingModel>` is case insensitive, so `inprocess` and `outofprocess` are valid values.</span></span>

<span data-ttu-id="78c3b-143">Místo http serveru služby IIS (`IISHttpServer`) se používá [poštovné](xref:fundamentals/servers/kestrel) server .</span><span class="sxs-lookup"><span data-stu-id="78c3b-143">[Kestrel](xref:fundamentals/servers/kestrel) server is used instead of IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="78c3b-144">Pro mimoproces, [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) volá <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> na:</span><span class="sxs-lookup"><span data-stu-id="78c3b-144">For out-of-process, [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> to:</span></span>

* <span data-ttu-id="78c3b-145">Nakonfigurujte port a základní cestu, kterou by měl server poslouchat při spuštění za ASP.NET základního modulu.</span><span class="sxs-lookup"><span data-stu-id="78c3b-145">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
* <span data-ttu-id="78c3b-146">Nakonfigurujte hostitele tak, aby zaznamenával chyby při spuštění.</span><span class="sxs-lookup"><span data-stu-id="78c3b-146">Configure the host to capture startup errors.</span></span>

### <a name="hosting-model-changes"></a><span data-ttu-id="78c3b-147">Hostování změn modelu</span><span class="sxs-lookup"><span data-stu-id="78c3b-147">Hosting model changes</span></span>

<span data-ttu-id="78c3b-148">Pokud `hostingModel` se nastavení změní v souboru *web.config* (vysvětleno v části [Konfigurace s web.config),](#configuration-with-webconfig) modul recykluje pracovní proces služby IIS.</span><span class="sxs-lookup"><span data-stu-id="78c3b-148">If the `hostingModel` setting is changed in the *web.config* file (explained in the [Configuration with web.config](#configuration-with-webconfig) section), the module recycles the worker process for IIS.</span></span>

<span data-ttu-id="78c3b-149">Pro iis Express modul nerecykluje pracovní proces, ale místo toho aktivuje řádné vypnutí aktuálního procesu iis express.</span><span class="sxs-lookup"><span data-stu-id="78c3b-149">For IIS Express, the module doesn't recycle the worker process but instead triggers a graceful shutdown of the current IIS Express process.</span></span> <span data-ttu-id="78c3b-150">Další požadavek na aplikaci spouští nový proces služby IIS Express.</span><span class="sxs-lookup"><span data-stu-id="78c3b-150">The next request to the app spawns a new IIS Express process.</span></span>

### <a name="process-name"></a><span data-ttu-id="78c3b-151">Název procesu</span><span class="sxs-lookup"><span data-stu-id="78c3b-151">Process name</span></span>

<span data-ttu-id="78c3b-152">`Process.GetCurrentProcess().ProcessName``w3wp` / `iisexpress` (v průběhu) nebo `dotnet` (mimoproces).</span><span class="sxs-lookup"><span data-stu-id="78c3b-152">`Process.GetCurrentProcess().ProcessName` reports `w3wp`/`iisexpress` (in-process) or `dotnet` (out-of-process).</span></span>

<span data-ttu-id="78c3b-153">Mnoho nativních modulů, například ověřování systému Windows, zůstává aktivní.</span><span class="sxs-lookup"><span data-stu-id="78c3b-153">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="78c3b-154">Další informace o modulech služby IIS aktivních <xref:host-and-deploy/iis/modules>v modulu ASP.NET Core naleznete v tématu .</span><span class="sxs-lookup"><span data-stu-id="78c3b-154">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="78c3b-155">Základní modul ASP.NET může také:</span><span class="sxs-lookup"><span data-stu-id="78c3b-155">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="78c3b-156">Nastavte proměnné prostředí pro pracovní proces.</span><span class="sxs-lookup"><span data-stu-id="78c3b-156">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="78c3b-157">Protokolovat výstup stdout do úložiště souborů pro řešení problémů se spuštěním.</span><span class="sxs-lookup"><span data-stu-id="78c3b-157">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="78c3b-158">Přepošlete ověřovací tokeny systému Windows.</span><span class="sxs-lookup"><span data-stu-id="78c3b-158">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="78c3b-159">Jak nainstalovat a používat ASP.NET core modul</span><span class="sxs-lookup"><span data-stu-id="78c3b-159">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="78c3b-160">Pokyny k instalaci ASP.NET základního modulu naleznete [v tématu Instalace sady .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span><span class="sxs-lookup"><span data-stu-id="78c3b-160">For instructions on how to install the ASP.NET Core Module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="78c3b-161">Konfigurace pomocí souboru web.config</span><span class="sxs-lookup"><span data-stu-id="78c3b-161">Configuration with web.config</span></span>

<span data-ttu-id="78c3b-162">Základní modul ASP.NET je `aspNetCore` konfigurován s `system.webServer` částí uzlu v souboru *web.config* webu.</span><span class="sxs-lookup"><span data-stu-id="78c3b-162">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="78c3b-163">Následující soubor *web.config* je publikován pro [nasazení závislé na rámci](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) a konfiguruje ASP.NET core modul pro zpracování požadavků webu:</span><span class="sxs-lookup"><span data-stu-id="78c3b-163">The following *web.config* file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

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

<span data-ttu-id="78c3b-164">Následující *web.config* je publikován pro [samostatné nasazení](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span><span class="sxs-lookup"><span data-stu-id="78c3b-164">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

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

<span data-ttu-id="78c3b-165">Vlastnost <xref:System.Configuration.SectionInformation.InheritInChildApplications*> je nastavena tak, aby `false` označovala, že nastavení zadaná v [ \<rámci umístění>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) prvek nejsou zděděna aplikacemi, které jsou umístěny v podadresáři aplikace.</span><span class="sxs-lookup"><span data-stu-id="78c3b-165">The <xref:System.Configuration.SectionInformation.InheritInChildApplications*> property is set to `false` to indicate that the settings specified within the [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) element aren't inherited by apps that reside in a subdirectory of the app.</span></span>

<span data-ttu-id="78c3b-166">Když se aplikace nasadí do `stdoutLogFile` [služby Azure App Service](https://azure.microsoft.com/services/app-service/), je cesta nastavená na `\\?\%home%\LogFiles\stdout`.</span><span class="sxs-lookup"><span data-stu-id="78c3b-166">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="78c3b-167">Cesta uloží protokoly stdout do složky *LogFiles,* což je umístění automaticky vytvořené službou.</span><span class="sxs-lookup"><span data-stu-id="78c3b-167">The path saves stdout logs to the *LogFiles* folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="78c3b-168">Informace o konfiguraci podaplikací služby IIS naleznete v tématu <xref:host-and-deploy/iis/index#sub-applications>.</span><span class="sxs-lookup"><span data-stu-id="78c3b-168">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="78c3b-169">Atributy prvku aspNetCore</span><span class="sxs-lookup"><span data-stu-id="78c3b-169">Attributes of the aspNetCore element</span></span>

| <span data-ttu-id="78c3b-170">Atribut</span><span class="sxs-lookup"><span data-stu-id="78c3b-170">Attribute</span></span> | <span data-ttu-id="78c3b-171">Popis</span><span class="sxs-lookup"><span data-stu-id="78c3b-171">Description</span></span> | <span data-ttu-id="78c3b-172">Výchozí</span><span class="sxs-lookup"><span data-stu-id="78c3b-172">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="78c3b-173">Volitelný atribut řetězce.</span><span class="sxs-lookup"><span data-stu-id="78c3b-173">Optional string attribute.</span></span></p><p><span data-ttu-id="78c3b-174">Argumenty ke spustitelnému souboru zadanému v **processPath**.</span><span class="sxs-lookup"><span data-stu-id="78c3b-174">Arguments to the executable specified in **processPath**.</span></span></p> | |
| `disableStartUpErrorPage` | <p><span data-ttu-id="78c3b-175">Volitelný logický atribut.</span><span class="sxs-lookup"><span data-stu-id="78c3b-175">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="78c3b-176">Pokud true, **502.5 - Selhání procesu** stránka je potlačena a 502 stavový kód stránky nakonfigurované v *web.config* má přednost.</span><span class="sxs-lookup"><span data-stu-id="78c3b-176">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="78c3b-177">Volitelný logický atribut.</span><span class="sxs-lookup"><span data-stu-id="78c3b-177">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="78c3b-178">Pokud true, token je předán podřízený proces naslouchání na %ASPNETCORE_PORT% jako záhlaví 'MS-ASPNETCORE-WINAUTHTOKEN' na požadavek.</span><span class="sxs-lookup"><span data-stu-id="78c3b-178">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="78c3b-179">Je odpovědností tohoto procesu volání CloseHandle na tento token na požadavek.</span><span class="sxs-lookup"><span data-stu-id="78c3b-179">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `hostingModel` | <p><span data-ttu-id="78c3b-180">Volitelný atribut řetězce.</span><span class="sxs-lookup"><span data-stu-id="78c3b-180">Optional string attribute.</span></span></p><p><span data-ttu-id="78c3b-181">Určuje hostitelský model jako v`InProcess`/`inprocess`procesu ( )`OutOfProcess`/`outofprocess`nebo mimo proces ( ).</span><span class="sxs-lookup"><span data-stu-id="78c3b-181">Specifies the hosting model as in-process (`InProcess`/`inprocess`) or out-of-process (`OutOfProcess`/`outofprocess`).</span></span></p> | `InProcess`<br>`inprocess` |
| `processesPerApplication` | <p><span data-ttu-id="78c3b-182">Volitelný atribut celé číslo.</span><span class="sxs-lookup"><span data-stu-id="78c3b-182">Optional integer attribute.</span></span></p><p><span data-ttu-id="78c3b-183">Určuje počet instancí procesu určeného v nastavení **processPath,** které lze přetavit podle aplikace.</span><span class="sxs-lookup"><span data-stu-id="78c3b-183">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="78c3b-184">&dagger;Pro hostování v procesu je `1`hodnota omezena na .</span><span class="sxs-lookup"><span data-stu-id="78c3b-184">&dagger;For in-process hosting, the value is limited to `1`.</span></span></p><p><span data-ttu-id="78c3b-185">Nastavení `processesPerApplication` se nedoporučuje.</span><span class="sxs-lookup"><span data-stu-id="78c3b-185">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="78c3b-186">Tento atribut bude odebrán v budoucí verzi.</span><span class="sxs-lookup"><span data-stu-id="78c3b-186">This attribute will be removed in a future release.</span></span></p> | <span data-ttu-id="78c3b-187">Výchozí:`1`</span><span class="sxs-lookup"><span data-stu-id="78c3b-187">Default: `1`</span></span><br><span data-ttu-id="78c3b-188">Min:`1`</span><span class="sxs-lookup"><span data-stu-id="78c3b-188">Min: `1`</span></span><br><span data-ttu-id="78c3b-189">Max:`100`&dagger;</span><span class="sxs-lookup"><span data-stu-id="78c3b-189">Max: `100`&dagger;</span></span> |
| `processPath` | <p><span data-ttu-id="78c3b-190">Povinný atribut řetězce.</span><span class="sxs-lookup"><span data-stu-id="78c3b-190">Required string attribute.</span></span></p><p><span data-ttu-id="78c3b-191">Cesta ke spustitelnému souboru, který spustí proces naslouchání pro požadavky HTTP.</span><span class="sxs-lookup"><span data-stu-id="78c3b-191">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="78c3b-192">Relativní cesty jsou podporovány.</span><span class="sxs-lookup"><span data-stu-id="78c3b-192">Relative paths are supported.</span></span> <span data-ttu-id="78c3b-193">Pokud cesta začíná `.`, cesta je považována za relativní ke kořenovému adresáři webu.</span><span class="sxs-lookup"><span data-stu-id="78c3b-193">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="78c3b-194">Volitelný atribut celé číslo.</span><span class="sxs-lookup"><span data-stu-id="78c3b-194">Optional integer attribute.</span></span></p><p><span data-ttu-id="78c3b-195">Určuje počet, kolikrát je povoleno selhání procesu zadaného v **processPath** za minutu.</span><span class="sxs-lookup"><span data-stu-id="78c3b-195">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="78c3b-196">Pokud je tento limit překročen, modul přestane spouštět proces po zbytek minuty.</span><span class="sxs-lookup"><span data-stu-id="78c3b-196">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p><p><span data-ttu-id="78c3b-197">Není podporováno s hostováním v procesu.</span><span class="sxs-lookup"><span data-stu-id="78c3b-197">Not supported with in-process hosting.</span></span></p> | <span data-ttu-id="78c3b-198">Výchozí:`10`</span><span class="sxs-lookup"><span data-stu-id="78c3b-198">Default: `10`</span></span><br><span data-ttu-id="78c3b-199">Min:`0`</span><span class="sxs-lookup"><span data-stu-id="78c3b-199">Min: `0`</span></span><br><span data-ttu-id="78c3b-200">Max:`100`</span><span class="sxs-lookup"><span data-stu-id="78c3b-200">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="78c3b-201">Volitelný atribut timespan.</span><span class="sxs-lookup"><span data-stu-id="78c3b-201">Optional timespan attribute.</span></span></p><p><span data-ttu-id="78c3b-202">Určuje dobu, po kterou základní modul ASP.NET čeká na odpověď z procesu naslouchání na %ASPNETCORE_PORT%.</span><span class="sxs-lookup"><span data-stu-id="78c3b-202">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="78c3b-203">Ve verzích ASP.NET core modul, který byl dodán s vydáním ASP.NET `requestTimeout` Core 2.1 nebo novější, je zadán v hodinách, minutách a sekundách.</span><span class="sxs-lookup"><span data-stu-id="78c3b-203">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p><p><span data-ttu-id="78c3b-204">Nevztahuje se na hostování v procesu.</span><span class="sxs-lookup"><span data-stu-id="78c3b-204">Doesn't apply to in-process hosting.</span></span> <span data-ttu-id="78c3b-205">Pro hostování v procesu modul čeká na aplikaci ke zpracování požadavku.</span><span class="sxs-lookup"><span data-stu-id="78c3b-205">For in-process hosting, the module waits for the app to process the request.</span></span></p><p><span data-ttu-id="78c3b-206">Platné hodnoty pro minuty a sekundy segmenty řetězce jsou v rozsahu 0-59.</span><span class="sxs-lookup"><span data-stu-id="78c3b-206">Valid values for minutes and seconds segments of the string are in the range 0-59.</span></span> <span data-ttu-id="78c3b-207">Použití **60** v hodnotě minut nebo sekund má za následek *500 - Vnitřní chyba serveru*.</span><span class="sxs-lookup"><span data-stu-id="78c3b-207">Use of **60** in the value for minutes or seconds results in a *500 - Internal Server Error*.</span></span></p> | <span data-ttu-id="78c3b-208">Výchozí:`00:02:00`</span><span class="sxs-lookup"><span data-stu-id="78c3b-208">Default: `00:02:00`</span></span><br><span data-ttu-id="78c3b-209">Min:`00:00:00`</span><span class="sxs-lookup"><span data-stu-id="78c3b-209">Min: `00:00:00`</span></span><br><span data-ttu-id="78c3b-210">Max:`360:00:00`</span><span class="sxs-lookup"><span data-stu-id="78c3b-210">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="78c3b-211">Volitelný atribut celé číslo.</span><span class="sxs-lookup"><span data-stu-id="78c3b-211">Optional integer attribute.</span></span></p><p><span data-ttu-id="78c3b-212">Doba trvání v sekundách, po kterou modul čeká na řádné vypnutí spustitelného souboru, když je zjištěn soubor *app_offline.htm.*</span><span class="sxs-lookup"><span data-stu-id="78c3b-212">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="78c3b-213">Výchozí:`10`</span><span class="sxs-lookup"><span data-stu-id="78c3b-213">Default: `10`</span></span><br><span data-ttu-id="78c3b-214">Min:`0`</span><span class="sxs-lookup"><span data-stu-id="78c3b-214">Min: `0`</span></span><br><span data-ttu-id="78c3b-215">Max:`600`</span><span class="sxs-lookup"><span data-stu-id="78c3b-215">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="78c3b-216">Volitelný atribut celé číslo.</span><span class="sxs-lookup"><span data-stu-id="78c3b-216">Optional integer attribute.</span></span></p><p><span data-ttu-id="78c3b-217">Doba trvání v sekundách, po kterou modul čeká na spuštění spustitelného souboru, který spustí proces naslouchání na portu.</span><span class="sxs-lookup"><span data-stu-id="78c3b-217">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="78c3b-218">Pokud je tento časový limit překročen, modul proces unejde.</span><span class="sxs-lookup"><span data-stu-id="78c3b-218">If this time limit is exceeded, the module kills the process.</span></span> <span data-ttu-id="78c3b-219">Modul se pokusí znovu spustit proces, když obdrží nový požadavek a nadále se pokouší restartovat proces na následné příchozí požadavky, pokud aplikace se nezdaří spustit **rapidFailsPerMinute** počet opakování v poslední rolling minute.</span><span class="sxs-lookup"><span data-stu-id="78c3b-219">The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="78c3b-220">Hodnota 0 (nula) **se nepovažuje** za nekonečný časový rozsah.</span><span class="sxs-lookup"><span data-stu-id="78c3b-220">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="78c3b-221">Výchozí:`120`</span><span class="sxs-lookup"><span data-stu-id="78c3b-221">Default: `120`</span></span><br><span data-ttu-id="78c3b-222">Min:`0`</span><span class="sxs-lookup"><span data-stu-id="78c3b-222">Min: `0`</span></span><br><span data-ttu-id="78c3b-223">Max:`3600`</span><span class="sxs-lookup"><span data-stu-id="78c3b-223">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="78c3b-224">Volitelný logický atribut.</span><span class="sxs-lookup"><span data-stu-id="78c3b-224">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="78c3b-225">Pokud true, **stdout** a **stderr** pro proces zadaný v **processPath** jsou přesměrovány na soubor zadaný v **stdoutLogFile**.</span><span class="sxs-lookup"><span data-stu-id="78c3b-225">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="78c3b-226">Volitelný atribut řetězce.</span><span class="sxs-lookup"><span data-stu-id="78c3b-226">Optional string attribute.</span></span></p><p><span data-ttu-id="78c3b-227">Určuje relativní nebo absolutní cestu k souboru, pro kterou jsou protokolovány **stdout** a **stderr** z procesu určeného v **processPath.**</span><span class="sxs-lookup"><span data-stu-id="78c3b-227">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="78c3b-228">Relativní cesty jsou relativní ke kořenu webu.</span><span class="sxs-lookup"><span data-stu-id="78c3b-228">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="78c3b-229">Každá cesta `.` začínající jsou relativní ke kořenu webu a všechny ostatní cesty jsou považovány za absolutní cesty.</span><span class="sxs-lookup"><span data-stu-id="78c3b-229">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="78c3b-230">Všechny složky uvedené v cestě jsou vytvořeny modulem při vytvoření souboru protokolu.</span><span class="sxs-lookup"><span data-stu-id="78c3b-230">Any folders provided in the path are created by the module when the log file is created.</span></span> <span data-ttu-id="78c3b-231">Pomocí oddělovačů podtržítek se do posledního segmentu cesty **stdoutLogFile** přidají časové razítko, ID procesu a přípona souboru (*.log).*</span><span class="sxs-lookup"><span data-stu-id="78c3b-231">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="78c3b-232">Pokud `.\logs\stdout` je zadán jako hodnota, příklad stdout log je uložen jako *stdout_20180205194132_1934.log* ve složce *protokoly* při uložení na 2/5/2018 v 19:41:32 s ID procesu 1934.</span><span class="sxs-lookup"><span data-stu-id="78c3b-232">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

### <a name="set-environment-variables"></a><span data-ttu-id="78c3b-233">Nastavení proměnných prostředí</span><span class="sxs-lookup"><span data-stu-id="78c3b-233">Set environment variables</span></span>

<span data-ttu-id="78c3b-234">Proměnné prostředí lze zadat pro proces `processPath` v atributu.</span><span class="sxs-lookup"><span data-stu-id="78c3b-234">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="78c3b-235">Zadejte proměnnou `<environmentVariable>` prostředí s `<environmentVariables>` podřízeným prvkem prvku kolekce.</span><span class="sxs-lookup"><span data-stu-id="78c3b-235">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span> <span data-ttu-id="78c3b-236">Proměnné prostředí nastavené v této části mají přednost před proměnnými systémového prostředí.</span><span class="sxs-lookup"><span data-stu-id="78c3b-236">Environment variables set in this section take precedence over system environment variables.</span></span>

<span data-ttu-id="78c3b-237">Následující příklad nastaví dvě proměnné prostředí v *web.config*. `ASPNETCORE_ENVIRONMENT` konfiguruje prostředí `Development`aplikace tak, aby .</span><span class="sxs-lookup"><span data-stu-id="78c3b-237">The following example sets two environment variables in *web.config*. `ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="78c3b-238">Vývojář může dočasně nastavit tuto hodnotu v souboru *web.config,* aby se [stránka výjimky vývojáře načetla](xref:fundamentals/error-handling) při ladění výjimky aplikace.</span><span class="sxs-lookup"><span data-stu-id="78c3b-238">A developer may temporarily set this value in the *web.config* file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="78c3b-239">`CONFIG_DIR`je příkladem uživatelem definované proměnné prostředí, kde vývojář napsal kód, který při spuštění přečte hodnotu a vytvoří cestu pro načtení konfiguračního souboru aplikace.</span><span class="sxs-lookup"><span data-stu-id="78c3b-239">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

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
> <span data-ttu-id="78c3b-240">Alternativou k nastavení prostředí přímo v *web.config* je zahrnout `<EnvironmentName>` vlastnost v profilu publikování [(.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) nebo souboru projektu.</span><span class="sxs-lookup"><span data-stu-id="78c3b-240">An alternative to setting the environment directly in *web.config* is to include the `<EnvironmentName>` property in the [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) or project file.</span></span> <span data-ttu-id="78c3b-241">Tento přístup nastaví prostředí v *web.config* při publikování projektu:</span><span class="sxs-lookup"><span data-stu-id="78c3b-241">This approach sets the environment in *web.config* when the project is published:</span></span>
>
> ```xml
> <PropertyGroup>
>   <EnvironmentName>Development</EnvironmentName>
> </PropertyGroup>
> ```

> [!WARNING]
> <span data-ttu-id="78c3b-242">Proměnnou `ASPNETCORE_ENVIRONMENT` prostředí nastavte `Development` pouze na pracovní a testovací servery, které nejsou přístupné nedůvěryhodným sítím, jako je například Internet.</span><span class="sxs-lookup"><span data-stu-id="78c3b-242">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## <a name="app_offlinehtm"></a><span data-ttu-id="78c3b-243">App_offline.htm</span><span class="sxs-lookup"><span data-stu-id="78c3b-243">app_offline.htm</span></span>

<span data-ttu-id="78c3b-244">Pokud je v kořenovém adresáři aplikace zjištěn soubor s názvem *app_offline.htm,* pokusí se ASP.NET základní modul řádně vypnout aplikaci a zastavit zpracování příchozích požadavků.</span><span class="sxs-lookup"><span data-stu-id="78c3b-244">If a file with the name *app_offline.htm* is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="78c3b-245">Pokud je aplikace stále spuštěna po `shutdownTimeLimit`počtu sekund definovaném v aplikaci , ASP.NET základní modul unejde spuštěný proces.</span><span class="sxs-lookup"><span data-stu-id="78c3b-245">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="78c3b-246">Zatímco soubor *app_offline.htm* je k dispozici, ASP.NET Core Module reaguje na požadavky odesláním zpět obsah souboru *app_offline.htm.*</span><span class="sxs-lookup"><span data-stu-id="78c3b-246">While the *app_offline.htm* file is present, the ASP.NET Core Module responds to requests by sending back the contents of the *app_offline.htm* file.</span></span> <span data-ttu-id="78c3b-247">Po odebrání souboru *app_offline.htm* spustí další požadavek aplikaci.</span><span class="sxs-lookup"><span data-stu-id="78c3b-247">When the *app_offline.htm* file is removed, the next request starts the app.</span></span>

<span data-ttu-id="78c3b-248">Při použití mimoproceshostingového modelu se aplikace nemusí okamžitě vypnout, pokud existuje otevřené připojení.</span><span class="sxs-lookup"><span data-stu-id="78c3b-248">When using the out-of-process hosting model, the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="78c3b-249">Například připojení websocket může zpozdit vypnutí aplikace.</span><span class="sxs-lookup"><span data-stu-id="78c3b-249">For example, a websocket connection may delay app shut down.</span></span>

## <a name="start-up-error-page"></a><span data-ttu-id="78c3b-250">Chybová stránka při spuštění</span><span class="sxs-lookup"><span data-stu-id="78c3b-250">Start-up error page</span></span>

<span data-ttu-id="78c3b-251">Hostování v průběhu procesu i mimo proces vytváří vlastní chybové stránky, když se jim nepodaří spustit aplikaci.</span><span class="sxs-lookup"><span data-stu-id="78c3b-251">Both in-process and out-of-process hosting produce custom error pages when they fail to start the app.</span></span>

<span data-ttu-id="78c3b-252">Pokud ASP.NET základnímodul nenalezne obslužnou rutinu požadavku v procesu nebo mimo proces, zobrazí se stránka s kódem chyby selhání *zatížení 500.0 - In-Process/Out-Of-Process Handler.*</span><span class="sxs-lookup"><span data-stu-id="78c3b-252">If the ASP.NET Core Module fails to find either the in-process or out-of-process request handler, a *500.0 - In-Process/Out-Of-Process Handler Load Failure* status code page appears.</span></span>

<span data-ttu-id="78c3b-253">Pro hostování v procesu, pokud ASP.NET základní modul nepodaří spustit aplikaci, zobrazí se stránka stavového kódu *500.30 - Start Failure.*</span><span class="sxs-lookup"><span data-stu-id="78c3b-253">For in-process hosting if the ASP.NET Core Module fails to start the app, a *500.30 - Start Failure* status code page appears.</span></span>

<span data-ttu-id="78c3b-254">Pro mimoproceshosting, pokud ASP.NET core modul se nezdaří spustit proces back-endu nebo proces back-endu spustí, ale nedokáže poslouchat na nakonfigurovaném portu, zobrazí se stránka s kódem stavového kódu *selhání procesu 502.5 - Process Failure.*</span><span class="sxs-lookup"><span data-stu-id="78c3b-254">For out-of-process hosting if the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span>

<span data-ttu-id="78c3b-255">Chcete-li tuto stránku potlačit a vrátit se k výchozí `disableStartUpErrorPage` stránce se stavovým kódem iIS 5xx, použijte atribut.</span><span class="sxs-lookup"><span data-stu-id="78c3b-255">To suppress this page and revert to the default IIS 5xx status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="78c3b-256">Další informace o konfiguraci vlastních chybových zpráv naleznete v [tématu Http Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span><span class="sxs-lookup"><span data-stu-id="78c3b-256">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

## <a name="log-creation-and-redirection"></a><span data-ttu-id="78c3b-257">Vytvoření a přesměrování protokolu</span><span class="sxs-lookup"><span data-stu-id="78c3b-257">Log creation and redirection</span></span>

<span data-ttu-id="78c3b-258">ASP.NET Core Module přesměruje výstup konzoly stdout a `stdoutLogEnabled` `stdoutLogFile` stderr `aspNetCore` na disk, pokud jsou nastaveny atributy a prvek.</span><span class="sxs-lookup"><span data-stu-id="78c3b-258">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="78c3b-259">Všechny složky `stdoutLogFile` v cestě jsou vytvořeny modulem při vytvoření souboru protokolu.</span><span class="sxs-lookup"><span data-stu-id="78c3b-259">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="78c3b-260">Fond aplikací musí mít přístup pro zápis do umístění, `IIS AppPool\<app_pool_name>` kde jsou protokoly zapsány (slouží k poskytnutí oprávnění k zápisu).</span><span class="sxs-lookup"><span data-stu-id="78c3b-260">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="78c3b-261">Protokoly nejsou otočeny, pokud nedojde k recyklaci nebo restartování procesu.</span><span class="sxs-lookup"><span data-stu-id="78c3b-261">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="78c3b-262">Je odpovědností hoster omezit místo na disku protokoly spotřebovávají.</span><span class="sxs-lookup"><span data-stu-id="78c3b-262">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="78c3b-263">Použití protokolu stdout se doporučuje pouze pro řešení problémů se spuštěním aplikace při hostování ve službě IIS nebo při použití [podpory služby IIS v době vývoje se službou Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), nikoli při místním ladění a spuštění aplikace se službou IIS Express.</span><span class="sxs-lookup"><span data-stu-id="78c3b-263">Using the stdout log is only recommended for troubleshooting app startup issues when hosting on IIS or when using [development-time support for IIS with Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), not while debugging locally and running the app with IIS Express.</span></span>

<span data-ttu-id="78c3b-264">Nepoužívejte protokol stdout pro obecné účely protokolování aplikací.</span><span class="sxs-lookup"><span data-stu-id="78c3b-264">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="78c3b-265">Pro rutinní protokolování v aplikaci ASP.NET Core použijte knihovnu protokolování, která omezuje velikost souboru protokolu a otáčí protokoly.</span><span class="sxs-lookup"><span data-stu-id="78c3b-265">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="78c3b-266">Další informace naleznete [v tématu zprostředkovatelé protokolování třetích stran](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="78c3b-266">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="78c3b-267">Časové razítko a přípona souboru jsou přidány automaticky při vytvoření souboru protokolu.</span><span class="sxs-lookup"><span data-stu-id="78c3b-267">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="78c3b-268">Název souboru protokolu se skládá připojením časového razítka, ID procesu a přípony `stdoutLogFile` souboru (*.log*) k poslednímu segmentu cesty (obvykle *stdout)* vymezeném podtržítky.</span><span class="sxs-lookup"><span data-stu-id="78c3b-268">The log file name is composed by appending the timestamp, process ID, and file extension (*.log*) to the last segment of the `stdoutLogFile` path (typically *stdout*) delimited by underscores.</span></span> <span data-ttu-id="78c3b-269">Pokud `stdoutLogFile` cesta končí *stdout*, protokol pro aplikaci s PID 1934 vytvořený na 2/5/2018 v 19:42:32 má název souboru *stdout_20180205194132_1934.log*.</span><span class="sxs-lookup"><span data-stu-id="78c3b-269">If the `stdoutLogFile` path ends with *stdout*, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name *stdout_20180205194132_1934.log*.</span></span>

<span data-ttu-id="78c3b-270">Pokud `stdoutLogEnabled` je false, chyby, ke kterým dojde při spuštění aplikace jsou zachyceny a vyzařovány do protokolu událostí až 30 KB.</span><span class="sxs-lookup"><span data-stu-id="78c3b-270">If `stdoutLogEnabled` is false, errors that occur on app startup are captured and emitted to the event log up to 30 KB.</span></span> <span data-ttu-id="78c3b-271">Po spuštění jsou všechny další protokoly zahozeny.</span><span class="sxs-lookup"><span data-stu-id="78c3b-271">After startup, all additional logs are discarded.</span></span>

<span data-ttu-id="78c3b-272">Následující ukázkový `aspNetCore` prvek konfiguruje protokolování stdout na relativní cestě `.\log\`.</span><span class="sxs-lookup"><span data-stu-id="78c3b-272">The following sample `aspNetCore` element configures stdout logging at the relative path `.\log\`.</span></span> <span data-ttu-id="78c3b-273">Zkontrolujte, zda má identita uživatele AppPoolu oprávnění k zápisu do poskytnuté cesty.</span><span class="sxs-lookup"><span data-stu-id="78c3b-273">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout"
    hostingModel="inprocess">
</aspNetCore>
```

<span data-ttu-id="78c3b-274">Při publikování aplikace pro nasazení služby Azure App `stdoutLogFile` Service `\\?\%home%\LogFiles\stdout`nastaví sada Web SDK hodnotu na .</span><span class="sxs-lookup"><span data-stu-id="78c3b-274">When publishing an app for Azure App Service deployment, the Web SDK sets the `stdoutLogFile` value to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="78c3b-275">Proměnná `%home` prostředí je předdefinovaná pro aplikace hostované službou Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="78c3b-275">The `%home` environment variable is predefined for apps hosted by Azure App Service.</span></span>

<span data-ttu-id="78c3b-276">Chcete-li vytvořit pravidla filtru protokolování, přečtěte si části filtrování [konfigurace](xref:fundamentals/logging/index#log-filtering) a [protokolů](xref:fundamentals/logging/index#log-filtering) v dokumentaci k protokolování jádra ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="78c3b-276">To create logging filter rules, see the [Configuration](xref:fundamentals/logging/index#log-filtering) and [Log filtering](xref:fundamentals/logging/index#log-filtering) sections of the ASP.NET Core logging documentation.</span></span>

<span data-ttu-id="78c3b-277">Další informace o formátech cest naleznete v [tématu Formátcesty k souborům v systémech Windows](/dotnet/standard/io/file-path-formats).</span><span class="sxs-lookup"><span data-stu-id="78c3b-277">For more information on path formats, see [File path formats on Windows systems](/dotnet/standard/io/file-path-formats).</span></span>

## <a name="enhanced-diagnostic-logs"></a><span data-ttu-id="78c3b-278">Rozšířené diagnostické protokoly</span><span class="sxs-lookup"><span data-stu-id="78c3b-278">Enhanced diagnostic logs</span></span>

<span data-ttu-id="78c3b-279">Základní modul ASP.NET je konfigurovatelný tak, aby poskytoval protokoly rozšířené diagnostiky.</span><span class="sxs-lookup"><span data-stu-id="78c3b-279">The ASP.NET Core Module is configurable to provide enhanced diagnostics logs.</span></span> <span data-ttu-id="78c3b-280">Přidejte `<handlerSettings>` prvek `<aspNetCore>` do prvku v *souboru web.config*. Nastavení `debugLevel` na `TRACE` vystavit vyšší věrnost diagnostických informací:</span><span class="sxs-lookup"><span data-stu-id="78c3b-280">Add the `<handlerSettings>` element to the `<aspNetCore>` element in *web.config*. Setting the `debugLevel` to `TRACE` exposes a higher fidelity of diagnostic information:</span></span>

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

<span data-ttu-id="78c3b-281">Všechny složky v cestě *(protokoly* v předchozím příkladu) jsou vytvořeny modulem při vytvoření souboru protokolu.</span><span class="sxs-lookup"><span data-stu-id="78c3b-281">Any folders in the path (*logs* in the preceding example) are created by the module when the log file is created.</span></span> <span data-ttu-id="78c3b-282">Fond aplikací musí mít přístup pro zápis do umístění, `IIS AppPool\<app_pool_name>` kde jsou protokoly zapsány (slouží k poskytnutí oprávnění k zápisu).</span><span class="sxs-lookup"><span data-stu-id="78c3b-282">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="78c3b-283">Hodnoty úrovně`debugLevel`ladění ( ) mohou zahrnovat úroveň i umístění.</span><span class="sxs-lookup"><span data-stu-id="78c3b-283">Debug level (`debugLevel`) values can include both the level and the location.</span></span>

<span data-ttu-id="78c3b-284">Úrovně (v pořadí od nejméně k většině verbose):</span><span class="sxs-lookup"><span data-stu-id="78c3b-284">Levels (in order from least to most verbose):</span></span>

* <span data-ttu-id="78c3b-285">ERROR</span><span class="sxs-lookup"><span data-stu-id="78c3b-285">ERROR</span></span>
* <span data-ttu-id="78c3b-286">WARNING</span><span class="sxs-lookup"><span data-stu-id="78c3b-286">WARNING</span></span>
* <span data-ttu-id="78c3b-287">Info</span><span class="sxs-lookup"><span data-stu-id="78c3b-287">INFO</span></span>
* <span data-ttu-id="78c3b-288">TRACE</span><span class="sxs-lookup"><span data-stu-id="78c3b-288">TRACE</span></span>

<span data-ttu-id="78c3b-289">Umístění (více míst je povoleno):</span><span class="sxs-lookup"><span data-stu-id="78c3b-289">Locations (multiple locations are permitted):</span></span>

* <span data-ttu-id="78c3b-290">Konzoly</span><span class="sxs-lookup"><span data-stu-id="78c3b-290">CONSOLE</span></span>
* <span data-ttu-id="78c3b-291">Eventlog</span><span class="sxs-lookup"><span data-stu-id="78c3b-291">EVENTLOG</span></span>
* <span data-ttu-id="78c3b-292">SOUBOR</span><span class="sxs-lookup"><span data-stu-id="78c3b-292">FILE</span></span>

<span data-ttu-id="78c3b-293">Nastavení obslužné rutiny lze také poskytnout prostřednictvím proměnných prostředí:</span><span class="sxs-lookup"><span data-stu-id="78c3b-293">The handler settings can also be provided via environment variables:</span></span>

* <span data-ttu-id="78c3b-294">`ASPNETCORE_MODULE_DEBUG_FILE`&ndash; Cesta k souboru protokolu ladění.</span><span class="sxs-lookup"><span data-stu-id="78c3b-294">`ASPNETCORE_MODULE_DEBUG_FILE` &ndash; Path to the debug log file.</span></span> <span data-ttu-id="78c3b-295">(Výchozí: *aspnetcore-debug.log*)</span><span class="sxs-lookup"><span data-stu-id="78c3b-295">(Default: *aspnetcore-debug.log*)</span></span>
* <span data-ttu-id="78c3b-296">`ASPNETCORE_MODULE_DEBUG`&ndash; Nastavení úrovně ladění.</span><span class="sxs-lookup"><span data-stu-id="78c3b-296">`ASPNETCORE_MODULE_DEBUG` &ndash; Debug level setting.</span></span>

> [!WARNING]
> <span data-ttu-id="78c3b-297">**Nenechávejte** protokolování ladění povolené v nasazení déle, než je nutné k řešení problému.</span><span class="sxs-lookup"><span data-stu-id="78c3b-297">Do **not** leave debug logging enabled in the deployment for longer than required to troubleshoot an issue.</span></span> <span data-ttu-id="78c3b-298">Velikost protokolu není omezena.</span><span class="sxs-lookup"><span data-stu-id="78c3b-298">The size of the log isn't limited.</span></span> <span data-ttu-id="78c3b-299">Ponechání protokolu ladění povoleno může vyčerpat dostupné místo na disku a selhání serveru nebo služby aplikace.</span><span class="sxs-lookup"><span data-stu-id="78c3b-299">Leaving the debug log enabled can exhaust the available disk space and crash the server or app service.</span></span>

<span data-ttu-id="78c3b-300">Příklad `aspNetCore` prvku v souboru *web.config* naleznete v části Konfigurace [pomocí souboru web.config.](#configuration-with-webconfig)</span><span class="sxs-lookup"><span data-stu-id="78c3b-300">See [Configuration with web.config](#configuration-with-webconfig) for an example of the `aspNetCore` element in the *web.config* file.</span></span>

## <a name="modify-the-stack-size"></a><span data-ttu-id="78c3b-301">Změna velikosti zásobníku</span><span class="sxs-lookup"><span data-stu-id="78c3b-301">Modify the stack size</span></span>

<span data-ttu-id="78c3b-302">*Platí pouze při použití modelu hostování v procesu.*</span><span class="sxs-lookup"><span data-stu-id="78c3b-302">*Only applies when using the in-process hosting model.*</span></span>

<span data-ttu-id="78c3b-303">Nakonfigurujte velikost `stackSize` spravovaného zásobníku pomocí nastavení v bajtů v *souboru web.config*. Výchozí velikost `1048576` je bajty (1 MB).</span><span class="sxs-lookup"><span data-stu-id="78c3b-303">Configure the managed stack size using the `stackSize` setting in bytes in *web.config*. The default size is `1048576` bytes (1 MB).</span></span>

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

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="78c3b-304">Konfigurace serveru proxy používá protokol HTTP a token párování</span><span class="sxs-lookup"><span data-stu-id="78c3b-304">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="78c3b-305">*Platí pouze pro mimoprocesový hosting.*</span><span class="sxs-lookup"><span data-stu-id="78c3b-305">*Only applies to out-of-process hosting.*</span></span>

<span data-ttu-id="78c3b-306">Proxy server vytvořený mezi ASP.NET Core Module a Kestrel používá protokol HTTP.</span><span class="sxs-lookup"><span data-stu-id="78c3b-306">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="78c3b-307">Neexistuje žádné riziko odposlouchávání provozu mezi modulem a Kestrel z umístění mimo server.</span><span class="sxs-lookup"><span data-stu-id="78c3b-307">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="78c3b-308">Token párování se používá k zajištění, že požadavky přijaté Kestrel byly proxied službou IIS a nepochází z jiného zdroje.</span><span class="sxs-lookup"><span data-stu-id="78c3b-308">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="78c3b-309">Token párování je vytvořen a nastaven do`ASPNETCORE_TOKEN`proměnné prostředí ( ) modulem.</span><span class="sxs-lookup"><span data-stu-id="78c3b-309">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="78c3b-310">Token párování je také nastaven do`MS-ASPNETCORE-TOKEN`záhlaví ( ) na každém proxied požadavku.</span><span class="sxs-lookup"><span data-stu-id="78c3b-310">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="78c3b-311">Služby IIS Middleware zkontroluje každý požadavek, který obdrží, a potvrdí, že hodnota záhlaví párování tokenu odpovídá hodnotě proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="78c3b-311">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="78c3b-312">Pokud jsou hodnoty tokenu neodpovídající, požadavek je zaznamenána a odmítnuta.</span><span class="sxs-lookup"><span data-stu-id="78c3b-312">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="78c3b-313">Proměnná prostředí tokenu párování a přenosy mezi modulem a Kestrelem nejsou přístupné z umístění mimo server.</span><span class="sxs-lookup"><span data-stu-id="78c3b-313">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="78c3b-314">Bez znalosti hodnoty tokenu párování nemůže útočník odeslat požadavky, které by obcéme kontroly v middlewaru služby IIS.</span><span class="sxs-lookup"><span data-stu-id="78c3b-314">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="78c3b-315">ASP.NET základní modul se sdílenou konfigurací služby IIS</span><span class="sxs-lookup"><span data-stu-id="78c3b-315">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="78c3b-316">Instalační program ASP.NET Core Module běží s oprávněními účtu **TrustedInstaller.**</span><span class="sxs-lookup"><span data-stu-id="78c3b-316">The ASP.NET Core Module installer runs with the privileges of the **TrustedInstaller** account.</span></span> <span data-ttu-id="78c3b-317">Vzhledem k tomu, že místní systémový účet nemá oprávnění ke změně cesty sdílení používané sdílenou konfigurací služby IIS, instalační program vyvolá chybu odepření přístupu při pokusu o konfiguraci nastavení modulu v souboru *applicationHost.config* ve sdílené složce.</span><span class="sxs-lookup"><span data-stu-id="78c3b-317">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the *applicationHost.config* file on the share.</span></span>

<span data-ttu-id="78c3b-318">Při použití sdílené konfigurace služby IIS ve stejném počítači jako instalace služby `OPT_NO_SHARED_CONFIG_CHECK` IIS `1`spusťte instalační program sady ASP.NET Core Hosting Bundle s parametrem nastaveným na :</span><span class="sxs-lookup"><span data-stu-id="78c3b-318">When using an IIS Shared Configuration on the same machine as the IIS installation, run the ASP.NET Core Hosting Bundle installer with the `OPT_NO_SHARED_CONFIG_CHECK` parameter set to `1`:</span></span>

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

<span data-ttu-id="78c3b-319">Pokud cesta ke sdílené konfiguraci není ve stejném počítači jako instalace služby IIS, postupujte takto:</span><span class="sxs-lookup"><span data-stu-id="78c3b-319">When the path to the shared configuration isn't on the same machine as the IIS installation, follow these steps:</span></span>

1. <span data-ttu-id="78c3b-320">Zakažte sdílenou konfiguraci služby IIS.</span><span class="sxs-lookup"><span data-stu-id="78c3b-320">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="78c3b-321">Spusťte instalační program.</span><span class="sxs-lookup"><span data-stu-id="78c3b-321">Run the installer.</span></span>
1. <span data-ttu-id="78c3b-322">Exportujte aktualizovaný soubor *applicationHost.config* do sdílené složky.</span><span class="sxs-lookup"><span data-stu-id="78c3b-322">Export the updated *applicationHost.config* file to the share.</span></span>
1. <span data-ttu-id="78c3b-323">Znovu povolte sdílenou konfiguraci služby IIS.</span><span class="sxs-lookup"><span data-stu-id="78c3b-323">Re-enable the IIS Shared Configuration.</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="78c3b-324">Protokoly instalačního programu verze modulu a instalačního programu hostingového balíčku</span><span class="sxs-lookup"><span data-stu-id="78c3b-324">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="78c3b-325">Chcete-li zjistit verzi nainstalovaného ASP.NET základního modulu:</span><span class="sxs-lookup"><span data-stu-id="78c3b-325">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="78c3b-326">V hostitelském systému přejděte na *%windir%\System32\inetsrv*.</span><span class="sxs-lookup"><span data-stu-id="78c3b-326">On the hosting system, navigate to *%windir%\System32\inetsrv*.</span></span>
1. <span data-ttu-id="78c3b-327">Vyhledejte soubor *aspnetcore.dll.*</span><span class="sxs-lookup"><span data-stu-id="78c3b-327">Locate the *aspnetcore.dll* file.</span></span>
1. <span data-ttu-id="78c3b-328">Klepněte pravým tlačítkem myši na soubor a v kontextové nabídce vyberte **Vlastnosti.**</span><span class="sxs-lookup"><span data-stu-id="78c3b-328">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="78c3b-329">Vyberte kartu **Podrobnosti.** **Verze souboru** a **verze produktu** představují nainstalovanou verzi modulu.</span><span class="sxs-lookup"><span data-stu-id="78c3b-329">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="78c3b-330">Protokoly instalačního programu instalačního programu instalačního programu sady Hosting bundle pro modul naleznete na adrese *\\C:\\Users %UserName%\\AppData\\Local\\Temp*. Soubor je pojmenován *dd_DotNetCoreWinSvrHosting__\<časové razítko>_000_AspNetCoreModule_x64.log*.</span><span class="sxs-lookup"><span data-stu-id="78c3b-330">The Hosting Bundle installer logs for the module are found at *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. The file is named *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="78c3b-331">Umístění modulů, schémat a konfiguračních souborů</span><span class="sxs-lookup"><span data-stu-id="78c3b-331">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="78c3b-332">Modul</span><span class="sxs-lookup"><span data-stu-id="78c3b-332">Module</span></span>

<span data-ttu-id="78c3b-333">**IIS (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="78c3b-333">**IIS (x86/amd64):**</span></span>

* <span data-ttu-id="78c3b-334">%windir%\System32\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="78c3b-334">%windir%\System32\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="78c3b-335">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="78c3b-335">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="78c3b-336">%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="78c3b-336">%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="78c3b-337">%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="78c3b-337">%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

<span data-ttu-id="78c3b-338">**IIS Express (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="78c3b-338">**IIS Express (x86/amd64):**</span></span>

* <span data-ttu-id="78c3b-339">%ProgramFiles%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="78c3b-339">%ProgramFiles%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="78c3b-340">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="78c3b-340">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="78c3b-341">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="78c3b-341">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="78c3b-342">%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="78c3b-342">%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

### <a name="schema"></a><span data-ttu-id="78c3b-343">Schéma</span><span class="sxs-lookup"><span data-stu-id="78c3b-343">Schema</span></span>

<span data-ttu-id="78c3b-344">**IIS**</span><span class="sxs-lookup"><span data-stu-id="78c3b-344">**IIS**</span></span>

* <span data-ttu-id="78c3b-345">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="78c3b-345">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span></span>

* <span data-ttu-id="78c3b-346">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span><span class="sxs-lookup"><span data-stu-id="78c3b-346">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span></span>

<span data-ttu-id="78c3b-347">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="78c3b-347">**IIS Express**</span></span>

* <span data-ttu-id="78c3b-348">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="78c3b-348">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span></span>

* <span data-ttu-id="78c3b-349">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span><span class="sxs-lookup"><span data-stu-id="78c3b-349">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span></span>

### <a name="configuration"></a><span data-ttu-id="78c3b-350">Konfigurace</span><span class="sxs-lookup"><span data-stu-id="78c3b-350">Configuration</span></span>

<span data-ttu-id="78c3b-351">**IIS**</span><span class="sxs-lookup"><span data-stu-id="78c3b-351">**IIS**</span></span>

* <span data-ttu-id="78c3b-352">%windir%\System32\inetsrv\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="78c3b-352">%windir%\System32\inetsrv\config\applicationHost.config</span></span>

<span data-ttu-id="78c3b-353">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="78c3b-353">**IIS Express**</span></span>

* <span data-ttu-id="78c3b-354">Visual Studio: {APPLICATION\\ROOT} .vs\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="78c3b-354">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span></span>

* <span data-ttu-id="78c3b-355">*Iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span><span class="sxs-lookup"><span data-stu-id="78c3b-355">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span></span>

<span data-ttu-id="78c3b-356">Soubory lze nalézt hledáním *aspnetcore* v souboru *applicationHost.config.*</span><span class="sxs-lookup"><span data-stu-id="78c3b-356">The files can be found by searching for *aspnetcore* in the *applicationHost.config* file.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="78c3b-357">Základní modul ASP.NET je nativní modul iis, který se zapojuje do kanálu iis a provádí následující:</span><span class="sxs-lookup"><span data-stu-id="78c3b-357">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to either:</span></span>

* <span data-ttu-id="78c3b-358">Hostujte aplikaci ASP.NET Core uvnitř pracovního procesu služby IIS (`w3wp.exe`), nazývanou model hostování v [procesu](#in-process-hosting-model).</span><span class="sxs-lookup"><span data-stu-id="78c3b-358">Host an ASP.NET Core app inside of the IIS worker process (`w3wp.exe`), called the [in-process hosting model](#in-process-hosting-model).</span></span>
* <span data-ttu-id="78c3b-359">Přepošle webové požadavky do back-endu ASP.NET aplikaci Core se systémem [Kestrel server](xref:fundamentals/servers/kestrel), nazvaný [mimoprocesový model hostování](#out-of-process-hosting-model).</span><span class="sxs-lookup"><span data-stu-id="78c3b-359">Forward web requests to a backend ASP.NET Core app running the [Kestrel server](xref:fundamentals/servers/kestrel), called the [out-of-process hosting model](#out-of-process-hosting-model).</span></span>

<span data-ttu-id="78c3b-360">Podporované verze systému Windows:</span><span class="sxs-lookup"><span data-stu-id="78c3b-360">Supported Windows versions:</span></span>

* <span data-ttu-id="78c3b-361">Windows 7 nebo novější</span><span class="sxs-lookup"><span data-stu-id="78c3b-361">Windows 7 or later</span></span>
* <span data-ttu-id="78c3b-362">Windows Server 2008 R2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="78c3b-362">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="78c3b-363">Při hostování v procesu modul používá pro službu IIS implementaci serveru`IISHttpServer`v procesu nazvanou IIS HTTP Server ( ).</span><span class="sxs-lookup"><span data-stu-id="78c3b-363">When hosting in-process, the module uses an in-process server implementation for IIS, called IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="78c3b-364">Při hostování mimo proces modul pracuje pouze s Kestrel.</span><span class="sxs-lookup"><span data-stu-id="78c3b-364">When hosting out-of-process, the module only works with Kestrel.</span></span> <span data-ttu-id="78c3b-365">Modul nefunguje s [HTTP.sys](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="78c3b-365">The module doesn't function with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

## <a name="hosting-models"></a><span data-ttu-id="78c3b-366">Modely hostingu</span><span class="sxs-lookup"><span data-stu-id="78c3b-366">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="78c3b-367">Model hostování v procesu</span><span class="sxs-lookup"><span data-stu-id="78c3b-367">In-process hosting model</span></span>

<span data-ttu-id="78c3b-368">Chcete-li nakonfigurovat aplikaci pro `<AspNetCoreHostingModel>` hostování v rámci procesu, přidejte `InProcess` vlastnost do souboru projektu `OutOfProcess`aplikace s hodnotou (mimoprocesový hosting je nastaven s ):</span><span class="sxs-lookup"><span data-stu-id="78c3b-368">To configure an app for in-process hosting, add the `<AspNetCoreHostingModel>` property to the app's project file with a value of `InProcess` (out-of-process hosting is set with `OutOfProcess`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>InProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="78c3b-369">Model hostování v procesu není podporován pro aplikace ASP.NET Core, které cílí na rozhraní .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="78c3b-369">The in-process hosting model isn't supported for ASP.NET Core apps that target the .NET Framework.</span></span>

<span data-ttu-id="78c3b-370">Hodnota `<AspNetCoreHostingModel>` je malá a velká `inprocess` `outofprocess` písmena, tak a jsou platné hodnoty.</span><span class="sxs-lookup"><span data-stu-id="78c3b-370">The value of `<AspNetCoreHostingModel>` is case insensitive, so `inprocess` and `outofprocess` are valid values.</span></span>

<span data-ttu-id="78c3b-371">Pokud `<AspNetCoreHostingModel>` vlastnost není v souboru k dispozici, `OutOfProcess`je výchozí hodnota .</span><span class="sxs-lookup"><span data-stu-id="78c3b-371">If the `<AspNetCoreHostingModel>` property isn't present in the file, the default value is `OutOfProcess`.</span></span>

<span data-ttu-id="78c3b-372">Následující charakteristiky platí při hostování v průběhu procesu:</span><span class="sxs-lookup"><span data-stu-id="78c3b-372">The following characteristics apply when hosting in-process:</span></span>

* <span data-ttu-id="78c3b-373">Místo serveru`IISHttpServer` [Kestrel](xref:fundamentals/servers/kestrel) se používá server HTTP služby IIS ( ).</span><span class="sxs-lookup"><span data-stu-id="78c3b-373">IIS HTTP Server (`IISHttpServer`) is used instead of [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span> <span data-ttu-id="78c3b-374">Pro v průběhu procesu <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) volá na:</span><span class="sxs-lookup"><span data-stu-id="78c3b-374">For in-process, [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> to:</span></span>

  * <span data-ttu-id="78c3b-375">Zaregistrujte `IISHttpServer`soubor .</span><span class="sxs-lookup"><span data-stu-id="78c3b-375">Register the `IISHttpServer`.</span></span>
  * <span data-ttu-id="78c3b-376">Nakonfigurujte port a základní cestu, kterou by měl server poslouchat při spuštění za ASP.NET základního modulu.</span><span class="sxs-lookup"><span data-stu-id="78c3b-376">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
  * <span data-ttu-id="78c3b-377">Nakonfigurujte hostitele tak, aby zaznamenával chyby při spuštění.</span><span class="sxs-lookup"><span data-stu-id="78c3b-377">Configure the host to capture startup errors.</span></span>

* <span data-ttu-id="78c3b-378">Atribut [requestTimeout](#attributes-of-the-aspnetcore-element) se nevztahuje na hostování v procesu.</span><span class="sxs-lookup"><span data-stu-id="78c3b-378">The [requestTimeout attribute](#attributes-of-the-aspnetcore-element) doesn't apply to in-process hosting.</span></span>

* <span data-ttu-id="78c3b-379">Sdílení fondu aplikací mezi aplikacemi není podporované.</span><span class="sxs-lookup"><span data-stu-id="78c3b-379">Sharing an app pool among apps isn't supported.</span></span> <span data-ttu-id="78c3b-380">Použijte jeden fond aplikací pro jednu aplikaci.</span><span class="sxs-lookup"><span data-stu-id="78c3b-380">Use one app pool per app.</span></span>

* <span data-ttu-id="78c3b-381">Při použití [nasazení na webu](/iis/publish/using-web-deploy/introduction-to-web-deploy) nebo ručním [umístěním souboru app_offline.htm do nasazení](xref:host-and-deploy/iis/index#locked-deployment-files)nemusí být aplikace okamžitě ukončena, pokud existuje otevřené připojení.</span><span class="sxs-lookup"><span data-stu-id="78c3b-381">When using [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) or manually placing an [app_offline.htm file in the deployment](xref:host-and-deploy/iis/index#locked-deployment-files), the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="78c3b-382">Například připojení websocket může zpozdit vypnutí aplikace.</span><span class="sxs-lookup"><span data-stu-id="78c3b-382">For example, a websocket connection may delay app shut down.</span></span>

* <span data-ttu-id="78c3b-383">Architektura (bitness) aplikace a nainstalovaný runtime (x64 nebo x86) musí odpovídat architektuře fondu aplikací.</span><span class="sxs-lookup"><span data-stu-id="78c3b-383">The architecture (bitness) of the app and installed runtime (x64 or x86) must match the architecture of the app pool.</span></span>

* <span data-ttu-id="78c3b-384">Jsou zjištěna odpojení klienta.</span><span class="sxs-lookup"><span data-stu-id="78c3b-384">Client disconnects are detected.</span></span> <span data-ttu-id="78c3b-385">Token zrušení [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) je zrušen, když se klient odpojí.</span><span class="sxs-lookup"><span data-stu-id="78c3b-385">The [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) cancellation token is cancelled when the client disconnects.</span></span>

* <span data-ttu-id="78c3b-386">V ASP.NET jádrem 2.2.1 nebo starším <xref:System.IO.Directory.GetCurrentDirectory*> vrátí pracovní adresář procesu spuštěného službou IIS, nikoli adresář aplikace (například *C:\Windows\System32\inetsrv* pro *w3wp.exe*).</span><span class="sxs-lookup"><span data-stu-id="78c3b-386">In ASP.NET Core 2.2.1 or earlier, <xref:System.IO.Directory.GetCurrentDirectory*> returns the worker directory of the process started by IIS rather than the app's directory (for example, *C:\Windows\System32\inetsrv* for *w3wp.exe*).</span></span>

  <span data-ttu-id="78c3b-387">Ukázkový kód, který nastavuje aktuální adresář aplikace, naleznete ve [třídě CurrentDirectoryHelpers](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/2.x/CurrentDirectoryHelpers.cs).</span><span class="sxs-lookup"><span data-stu-id="78c3b-387">For sample code that sets the app's current directory, see the [CurrentDirectoryHelpers class](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/2.x/CurrentDirectoryHelpers.cs).</span></span> <span data-ttu-id="78c3b-388">Volání `SetCurrentDirectory` metody.</span><span class="sxs-lookup"><span data-stu-id="78c3b-388">Call the `SetCurrentDirectory` method.</span></span> <span data-ttu-id="78c3b-389">Následná <xref:System.IO.Directory.GetCurrentDirectory*> volání k poskytnutí adresáře aplikace.</span><span class="sxs-lookup"><span data-stu-id="78c3b-389">Subsequent calls to <xref:System.IO.Directory.GetCurrentDirectory*> provide the app's directory.</span></span>

* <span data-ttu-id="78c3b-390">Při hostování v <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> procesu, není volána interně inicializovat uživatele.</span><span class="sxs-lookup"><span data-stu-id="78c3b-390">When hosting in-process, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span> <span data-ttu-id="78c3b-391">Proto <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementace slouží k transformaci deklarací identity po každém ověřování není aktivován ve výchozím nastavení.</span><span class="sxs-lookup"><span data-stu-id="78c3b-391">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="78c3b-392">Při transformaci deklarací <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> identity <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> s implementací volejte a přidejte ověřovací služby:</span><span class="sxs-lookup"><span data-stu-id="78c3b-392">When transforming claims with an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation, call <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> to add authentication services:</span></span>

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

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="78c3b-393">Model hostování mimo proces</span><span class="sxs-lookup"><span data-stu-id="78c3b-393">Out-of-process hosting model</span></span>

<span data-ttu-id="78c3b-394">Chcete-li nakonfigurovat aplikaci pro mimoprocesový hosting, použijte v souboru projektu některý z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="78c3b-394">To configure an app for out-of-process hosting, use either of the following approaches in the project file:</span></span>

* <span data-ttu-id="78c3b-395">Nezadávejte `<AspNetCoreHostingModel>` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="78c3b-395">Don't specify the `<AspNetCoreHostingModel>` property.</span></span> <span data-ttu-id="78c3b-396">Pokud `<AspNetCoreHostingModel>` vlastnost není v souboru k dispozici, `OutOfProcess`je výchozí hodnota .</span><span class="sxs-lookup"><span data-stu-id="78c3b-396">If the `<AspNetCoreHostingModel>` property isn't present in the file, the default value is `OutOfProcess`.</span></span>
* <span data-ttu-id="78c3b-397">Nastavte hodnotu `<AspNetCoreHostingModel>` vlastnosti `OutOfProcess` na (hostování v `InProcess`procesu je nastaveno):</span><span class="sxs-lookup"><span data-stu-id="78c3b-397">Set the value of the `<AspNetCoreHostingModel>` property to `OutOfProcess` (in-process hosting is set with `InProcess`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="78c3b-398">Hodnota je malá a `inprocess` velká `outofprocess` písmena, takže a jsou platné hodnoty.</span><span class="sxs-lookup"><span data-stu-id="78c3b-398">The value is case insensitive, so `inprocess` and `outofprocess` are valid values.</span></span>

<span data-ttu-id="78c3b-399">Místo http serveru služby IIS (`IISHttpServer`) se používá [poštovné](xref:fundamentals/servers/kestrel) server .</span><span class="sxs-lookup"><span data-stu-id="78c3b-399">[Kestrel](xref:fundamentals/servers/kestrel) server is used instead of IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="78c3b-400">Pro mimoproces, [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) volá <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> na:</span><span class="sxs-lookup"><span data-stu-id="78c3b-400">For out-of-process, [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> to:</span></span>

* <span data-ttu-id="78c3b-401">Nakonfigurujte port a základní cestu, kterou by měl server poslouchat při spuštění za ASP.NET základního modulu.</span><span class="sxs-lookup"><span data-stu-id="78c3b-401">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
* <span data-ttu-id="78c3b-402">Nakonfigurujte hostitele tak, aby zaznamenával chyby při spuštění.</span><span class="sxs-lookup"><span data-stu-id="78c3b-402">Configure the host to capture startup errors.</span></span>

### <a name="hosting-model-changes"></a><span data-ttu-id="78c3b-403">Hostování změn modelu</span><span class="sxs-lookup"><span data-stu-id="78c3b-403">Hosting model changes</span></span>

<span data-ttu-id="78c3b-404">Pokud `hostingModel` se nastavení změní v souboru *web.config* (vysvětleno v části [Konfigurace s web.config),](#configuration-with-webconfig) modul recykluje pracovní proces služby IIS.</span><span class="sxs-lookup"><span data-stu-id="78c3b-404">If the `hostingModel` setting is changed in the *web.config* file (explained in the [Configuration with web.config](#configuration-with-webconfig) section), the module recycles the worker process for IIS.</span></span>

<span data-ttu-id="78c3b-405">Pro iis Express modul nerecykluje pracovní proces, ale místo toho aktivuje řádné vypnutí aktuálního procesu iis express.</span><span class="sxs-lookup"><span data-stu-id="78c3b-405">For IIS Express, the module doesn't recycle the worker process but instead triggers a graceful shutdown of the current IIS Express process.</span></span> <span data-ttu-id="78c3b-406">Další požadavek na aplikaci spouští nový proces služby IIS Express.</span><span class="sxs-lookup"><span data-stu-id="78c3b-406">The next request to the app spawns a new IIS Express process.</span></span>

### <a name="process-name"></a><span data-ttu-id="78c3b-407">Název procesu</span><span class="sxs-lookup"><span data-stu-id="78c3b-407">Process name</span></span>

<span data-ttu-id="78c3b-408">`Process.GetCurrentProcess().ProcessName``w3wp` / `iisexpress` (v průběhu) nebo `dotnet` (mimoproces).</span><span class="sxs-lookup"><span data-stu-id="78c3b-408">`Process.GetCurrentProcess().ProcessName` reports `w3wp`/`iisexpress` (in-process) or `dotnet` (out-of-process).</span></span>

<span data-ttu-id="78c3b-409">Mnoho nativních modulů, například ověřování systému Windows, zůstává aktivní.</span><span class="sxs-lookup"><span data-stu-id="78c3b-409">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="78c3b-410">Další informace o modulech služby IIS aktivních <xref:host-and-deploy/iis/modules>v modulu ASP.NET Core naleznete v tématu .</span><span class="sxs-lookup"><span data-stu-id="78c3b-410">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="78c3b-411">Základní modul ASP.NET může také:</span><span class="sxs-lookup"><span data-stu-id="78c3b-411">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="78c3b-412">Nastavte proměnné prostředí pro pracovní proces.</span><span class="sxs-lookup"><span data-stu-id="78c3b-412">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="78c3b-413">Protokolovat výstup stdout do úložiště souborů pro řešení problémů se spuštěním.</span><span class="sxs-lookup"><span data-stu-id="78c3b-413">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="78c3b-414">Přepošlete ověřovací tokeny systému Windows.</span><span class="sxs-lookup"><span data-stu-id="78c3b-414">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="78c3b-415">Jak nainstalovat a používat ASP.NET core modul</span><span class="sxs-lookup"><span data-stu-id="78c3b-415">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="78c3b-416">Pokyny k instalaci ASP.NET základního modulu naleznete [v tématu Instalace sady .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span><span class="sxs-lookup"><span data-stu-id="78c3b-416">For instructions on how to install the ASP.NET Core Module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="78c3b-417">Konfigurace pomocí souboru web.config</span><span class="sxs-lookup"><span data-stu-id="78c3b-417">Configuration with web.config</span></span>

<span data-ttu-id="78c3b-418">Základní modul ASP.NET je `aspNetCore` konfigurován s `system.webServer` částí uzlu v souboru *web.config* webu.</span><span class="sxs-lookup"><span data-stu-id="78c3b-418">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="78c3b-419">Následující soubor *web.config* je publikován pro [nasazení závislé na rámci](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) a konfiguruje ASP.NET core modul pro zpracování požadavků webu:</span><span class="sxs-lookup"><span data-stu-id="78c3b-419">The following *web.config* file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

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

<span data-ttu-id="78c3b-420">Následující *web.config* je publikován pro [samostatné nasazení](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span><span class="sxs-lookup"><span data-stu-id="78c3b-420">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

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

<span data-ttu-id="78c3b-421">Vlastnost <xref:System.Configuration.SectionInformation.InheritInChildApplications*> je nastavena tak, aby `false` označovala, že nastavení zadaná v [ \<rámci umístění>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) prvek nejsou zděděna aplikacemi, které jsou umístěny v podadresáři aplikace.</span><span class="sxs-lookup"><span data-stu-id="78c3b-421">The <xref:System.Configuration.SectionInformation.InheritInChildApplications*> property is set to `false` to indicate that the settings specified within the [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) element aren't inherited by apps that reside in a subdirectory of the app.</span></span>

<span data-ttu-id="78c3b-422">Když se aplikace nasadí do `stdoutLogFile` [služby Azure App Service](https://azure.microsoft.com/services/app-service/), je cesta nastavená na `\\?\%home%\LogFiles\stdout`.</span><span class="sxs-lookup"><span data-stu-id="78c3b-422">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="78c3b-423">Cesta uloží protokoly stdout do složky *LogFiles,* což je umístění automaticky vytvořené službou.</span><span class="sxs-lookup"><span data-stu-id="78c3b-423">The path saves stdout logs to the *LogFiles* folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="78c3b-424">Informace o konfiguraci podaplikací služby IIS naleznete v tématu <xref:host-and-deploy/iis/index#sub-applications>.</span><span class="sxs-lookup"><span data-stu-id="78c3b-424">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="78c3b-425">Atributy prvku aspNetCore</span><span class="sxs-lookup"><span data-stu-id="78c3b-425">Attributes of the aspNetCore element</span></span>

| <span data-ttu-id="78c3b-426">Atribut</span><span class="sxs-lookup"><span data-stu-id="78c3b-426">Attribute</span></span> | <span data-ttu-id="78c3b-427">Popis</span><span class="sxs-lookup"><span data-stu-id="78c3b-427">Description</span></span> | <span data-ttu-id="78c3b-428">Výchozí</span><span class="sxs-lookup"><span data-stu-id="78c3b-428">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="78c3b-429">Volitelný atribut řetězce.</span><span class="sxs-lookup"><span data-stu-id="78c3b-429">Optional string attribute.</span></span></p><p><span data-ttu-id="78c3b-430">Argumenty ke spustitelnému souboru zadanému v **processPath**.</span><span class="sxs-lookup"><span data-stu-id="78c3b-430">Arguments to the executable specified in **processPath**.</span></span></p> | |
| `disableStartUpErrorPage` | <p><span data-ttu-id="78c3b-431">Volitelný logický atribut.</span><span class="sxs-lookup"><span data-stu-id="78c3b-431">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="78c3b-432">Pokud true, **502.5 - Selhání procesu** stránka je potlačena a 502 stavový kód stránky nakonfigurované v *web.config* má přednost.</span><span class="sxs-lookup"><span data-stu-id="78c3b-432">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="78c3b-433">Volitelný logický atribut.</span><span class="sxs-lookup"><span data-stu-id="78c3b-433">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="78c3b-434">Pokud true, token je předán podřízený proces naslouchání na %ASPNETCORE_PORT% jako záhlaví 'MS-ASPNETCORE-WINAUTHTOKEN' na požadavek.</span><span class="sxs-lookup"><span data-stu-id="78c3b-434">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="78c3b-435">Je odpovědností tohoto procesu volání CloseHandle na tento token na požadavek.</span><span class="sxs-lookup"><span data-stu-id="78c3b-435">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `hostingModel` | <p><span data-ttu-id="78c3b-436">Volitelný atribut řetězce.</span><span class="sxs-lookup"><span data-stu-id="78c3b-436">Optional string attribute.</span></span></p><p><span data-ttu-id="78c3b-437">Určuje hostitelský model jako v`InProcess`/`inprocess`procesu ( )`OutOfProcess`/`outofprocess`nebo mimo proces ( ).</span><span class="sxs-lookup"><span data-stu-id="78c3b-437">Specifies the hosting model as in-process (`InProcess`/`inprocess`) or out-of-process (`OutOfProcess`/`outofprocess`).</span></span></p> | `OutOfProcess`<br>`outofprocess` |
| `processesPerApplication` | <p><span data-ttu-id="78c3b-438">Volitelný atribut celé číslo.</span><span class="sxs-lookup"><span data-stu-id="78c3b-438">Optional integer attribute.</span></span></p><p><span data-ttu-id="78c3b-439">Určuje počet instancí procesu určeného v nastavení **processPath,** které lze přetavit podle aplikace.</span><span class="sxs-lookup"><span data-stu-id="78c3b-439">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="78c3b-440">&dagger;Pro hostování v procesu je `1`hodnota omezena na .</span><span class="sxs-lookup"><span data-stu-id="78c3b-440">&dagger;For in-process hosting, the value is limited to `1`.</span></span></p><p><span data-ttu-id="78c3b-441">Nastavení `processesPerApplication` se nedoporučuje.</span><span class="sxs-lookup"><span data-stu-id="78c3b-441">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="78c3b-442">Tento atribut bude odebrán v budoucí verzi.</span><span class="sxs-lookup"><span data-stu-id="78c3b-442">This attribute will be removed in a future release.</span></span></p> | <span data-ttu-id="78c3b-443">Výchozí:`1`</span><span class="sxs-lookup"><span data-stu-id="78c3b-443">Default: `1`</span></span><br><span data-ttu-id="78c3b-444">Min:`1`</span><span class="sxs-lookup"><span data-stu-id="78c3b-444">Min: `1`</span></span><br><span data-ttu-id="78c3b-445">Max:`100`&dagger;</span><span class="sxs-lookup"><span data-stu-id="78c3b-445">Max: `100`&dagger;</span></span> |
| `processPath` | <p><span data-ttu-id="78c3b-446">Povinný atribut řetězce.</span><span class="sxs-lookup"><span data-stu-id="78c3b-446">Required string attribute.</span></span></p><p><span data-ttu-id="78c3b-447">Cesta ke spustitelnému souboru, který spustí proces naslouchání pro požadavky HTTP.</span><span class="sxs-lookup"><span data-stu-id="78c3b-447">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="78c3b-448">Relativní cesty jsou podporovány.</span><span class="sxs-lookup"><span data-stu-id="78c3b-448">Relative paths are supported.</span></span> <span data-ttu-id="78c3b-449">Pokud cesta začíná `.`, cesta je považována za relativní ke kořenovému adresáři webu.</span><span class="sxs-lookup"><span data-stu-id="78c3b-449">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="78c3b-450">Volitelný atribut celé číslo.</span><span class="sxs-lookup"><span data-stu-id="78c3b-450">Optional integer attribute.</span></span></p><p><span data-ttu-id="78c3b-451">Určuje počet, kolikrát je povoleno selhání procesu zadaného v **processPath** za minutu.</span><span class="sxs-lookup"><span data-stu-id="78c3b-451">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="78c3b-452">Pokud je tento limit překročen, modul přestane spouštět proces po zbytek minuty.</span><span class="sxs-lookup"><span data-stu-id="78c3b-452">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p><p><span data-ttu-id="78c3b-453">Není podporováno s hostováním v procesu.</span><span class="sxs-lookup"><span data-stu-id="78c3b-453">Not supported with in-process hosting.</span></span></p> | <span data-ttu-id="78c3b-454">Výchozí:`10`</span><span class="sxs-lookup"><span data-stu-id="78c3b-454">Default: `10`</span></span><br><span data-ttu-id="78c3b-455">Min:`0`</span><span class="sxs-lookup"><span data-stu-id="78c3b-455">Min: `0`</span></span><br><span data-ttu-id="78c3b-456">Max:`100`</span><span class="sxs-lookup"><span data-stu-id="78c3b-456">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="78c3b-457">Volitelný atribut timespan.</span><span class="sxs-lookup"><span data-stu-id="78c3b-457">Optional timespan attribute.</span></span></p><p><span data-ttu-id="78c3b-458">Určuje dobu, po kterou základní modul ASP.NET čeká na odpověď z procesu naslouchání na %ASPNETCORE_PORT%.</span><span class="sxs-lookup"><span data-stu-id="78c3b-458">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="78c3b-459">Ve verzích ASP.NET core modul, který byl dodán s vydáním ASP.NET `requestTimeout` Core 2.1 nebo novější, je zadán v hodinách, minutách a sekundách.</span><span class="sxs-lookup"><span data-stu-id="78c3b-459">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p><p><span data-ttu-id="78c3b-460">Nevztahuje se na hostování v procesu.</span><span class="sxs-lookup"><span data-stu-id="78c3b-460">Doesn't apply to in-process hosting.</span></span> <span data-ttu-id="78c3b-461">Pro hostování v procesu modul čeká na aplikaci ke zpracování požadavku.</span><span class="sxs-lookup"><span data-stu-id="78c3b-461">For in-process hosting, the module waits for the app to process the request.</span></span></p><p><span data-ttu-id="78c3b-462">Platné hodnoty pro minuty a sekundy segmenty řetězce jsou v rozsahu 0-59.</span><span class="sxs-lookup"><span data-stu-id="78c3b-462">Valid values for minutes and seconds segments of the string are in the range 0-59.</span></span> <span data-ttu-id="78c3b-463">Použití **60** v hodnotě minut nebo sekund má za následek *500 - Vnitřní chyba serveru*.</span><span class="sxs-lookup"><span data-stu-id="78c3b-463">Use of **60** in the value for minutes or seconds results in a *500 - Internal Server Error*.</span></span></p> | <span data-ttu-id="78c3b-464">Výchozí:`00:02:00`</span><span class="sxs-lookup"><span data-stu-id="78c3b-464">Default: `00:02:00`</span></span><br><span data-ttu-id="78c3b-465">Min:`00:00:00`</span><span class="sxs-lookup"><span data-stu-id="78c3b-465">Min: `00:00:00`</span></span><br><span data-ttu-id="78c3b-466">Max:`360:00:00`</span><span class="sxs-lookup"><span data-stu-id="78c3b-466">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="78c3b-467">Volitelný atribut celé číslo.</span><span class="sxs-lookup"><span data-stu-id="78c3b-467">Optional integer attribute.</span></span></p><p><span data-ttu-id="78c3b-468">Doba trvání v sekundách, po kterou modul čeká na řádné vypnutí spustitelného souboru, když je zjištěn soubor *app_offline.htm.*</span><span class="sxs-lookup"><span data-stu-id="78c3b-468">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="78c3b-469">Výchozí:`10`</span><span class="sxs-lookup"><span data-stu-id="78c3b-469">Default: `10`</span></span><br><span data-ttu-id="78c3b-470">Min:`0`</span><span class="sxs-lookup"><span data-stu-id="78c3b-470">Min: `0`</span></span><br><span data-ttu-id="78c3b-471">Max:`600`</span><span class="sxs-lookup"><span data-stu-id="78c3b-471">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="78c3b-472">Volitelný atribut celé číslo.</span><span class="sxs-lookup"><span data-stu-id="78c3b-472">Optional integer attribute.</span></span></p><p><span data-ttu-id="78c3b-473">Doba trvání v sekundách, po kterou modul čeká na spuštění spustitelného souboru, který spustí proces naslouchání na portu.</span><span class="sxs-lookup"><span data-stu-id="78c3b-473">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="78c3b-474">Pokud je tento časový limit překročen, modul proces unejde.</span><span class="sxs-lookup"><span data-stu-id="78c3b-474">If this time limit is exceeded, the module kills the process.</span></span> <span data-ttu-id="78c3b-475">Modul se pokusí znovu spustit proces, když obdrží nový požadavek a nadále se pokouší restartovat proces na následné příchozí požadavky, pokud aplikace se nezdaří spustit **rapidFailsPerMinute** počet opakování v poslední rolling minute.</span><span class="sxs-lookup"><span data-stu-id="78c3b-475">The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="78c3b-476">Hodnota 0 (nula) **se nepovažuje** za nekonečný časový rozsah.</span><span class="sxs-lookup"><span data-stu-id="78c3b-476">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="78c3b-477">Výchozí:`120`</span><span class="sxs-lookup"><span data-stu-id="78c3b-477">Default: `120`</span></span><br><span data-ttu-id="78c3b-478">Min:`0`</span><span class="sxs-lookup"><span data-stu-id="78c3b-478">Min: `0`</span></span><br><span data-ttu-id="78c3b-479">Max:`3600`</span><span class="sxs-lookup"><span data-stu-id="78c3b-479">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="78c3b-480">Volitelný logický atribut.</span><span class="sxs-lookup"><span data-stu-id="78c3b-480">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="78c3b-481">Pokud true, **stdout** a **stderr** pro proces zadaný v **processPath** jsou přesměrovány na soubor zadaný v **stdoutLogFile**.</span><span class="sxs-lookup"><span data-stu-id="78c3b-481">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="78c3b-482">Volitelný atribut řetězce.</span><span class="sxs-lookup"><span data-stu-id="78c3b-482">Optional string attribute.</span></span></p><p><span data-ttu-id="78c3b-483">Určuje relativní nebo absolutní cestu k souboru, pro kterou jsou protokolovány **stdout** a **stderr** z procesu určeného v **processPath.**</span><span class="sxs-lookup"><span data-stu-id="78c3b-483">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="78c3b-484">Relativní cesty jsou relativní ke kořenu webu.</span><span class="sxs-lookup"><span data-stu-id="78c3b-484">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="78c3b-485">Každá cesta `.` začínající jsou relativní ke kořenu webu a všechny ostatní cesty jsou považovány za absolutní cesty.</span><span class="sxs-lookup"><span data-stu-id="78c3b-485">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="78c3b-486">Všechny složky uvedené v cestě jsou vytvořeny modulem při vytvoření souboru protokolu.</span><span class="sxs-lookup"><span data-stu-id="78c3b-486">Any folders provided in the path are created by the module when the log file is created.</span></span> <span data-ttu-id="78c3b-487">Pomocí oddělovačů podtržítek se do posledního segmentu cesty **stdoutLogFile** přidají časové razítko, ID procesu a přípona souboru (*.log).*</span><span class="sxs-lookup"><span data-stu-id="78c3b-487">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="78c3b-488">Pokud `.\logs\stdout` je zadán jako hodnota, příklad stdout log je uložen jako *stdout_20180205194132_1934.log* ve složce *protokoly* při uložení na 2/5/2018 v 19:41:32 s ID procesu 1934.</span><span class="sxs-lookup"><span data-stu-id="78c3b-488">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

### <a name="setting-environment-variables"></a><span data-ttu-id="78c3b-489">Nastavení proměnných prostředí</span><span class="sxs-lookup"><span data-stu-id="78c3b-489">Setting environment variables</span></span>

<span data-ttu-id="78c3b-490">Proměnné prostředí lze zadat pro proces `processPath` v atributu.</span><span class="sxs-lookup"><span data-stu-id="78c3b-490">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="78c3b-491">Zadejte proměnnou `<environmentVariable>` prostředí s `<environmentVariables>` podřízeným prvkem prvku kolekce.</span><span class="sxs-lookup"><span data-stu-id="78c3b-491">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span> <span data-ttu-id="78c3b-492">Proměnné prostředí nastavené v této části mají přednost před proměnnými systémového prostředí.</span><span class="sxs-lookup"><span data-stu-id="78c3b-492">Environment variables set in this section take precedence over system environment variables.</span></span>

<span data-ttu-id="78c3b-493">Následující příklad nastaví dvě proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="78c3b-493">The following example sets two environment variables.</span></span> <span data-ttu-id="78c3b-494">`ASPNETCORE_ENVIRONMENT`konfiguruje prostředí `Development`aplikace tak, aby .</span><span class="sxs-lookup"><span data-stu-id="78c3b-494">`ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="78c3b-495">Vývojář může dočasně nastavit tuto hodnotu v souboru *web.config,* aby se [stránka výjimky vývojáře načetla](xref:fundamentals/error-handling) při ladění výjimky aplikace.</span><span class="sxs-lookup"><span data-stu-id="78c3b-495">A developer may temporarily set this value in the *web.config* file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="78c3b-496">`CONFIG_DIR`je příkladem uživatelem definované proměnné prostředí, kde vývojář napsal kód, který při spuštění přečte hodnotu a vytvoří cestu pro načtení konfiguračního souboru aplikace.</span><span class="sxs-lookup"><span data-stu-id="78c3b-496">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

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
> <span data-ttu-id="78c3b-497">Alternativou k nastavení prostředí přímo v *web.config* je zahrnout `<EnvironmentName>` vlastnost v profilu publikování [(.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) nebo souboru projektu.</span><span class="sxs-lookup"><span data-stu-id="78c3b-497">An alternative to setting the environment directly in *web.config* is to include the `<EnvironmentName>` property in the [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) or project file.</span></span> <span data-ttu-id="78c3b-498">Tento přístup nastaví prostředí v *web.config* při publikování projektu:</span><span class="sxs-lookup"><span data-stu-id="78c3b-498">This approach sets the environment in *web.config* when the project is published:</span></span>
>
> ```xml
> <PropertyGroup>
>   <EnvironmentName>Development</EnvironmentName>
> </PropertyGroup>
> ```

> [!WARNING]
> <span data-ttu-id="78c3b-499">Proměnnou `ASPNETCORE_ENVIRONMENT` prostředí nastavte `Development` pouze na pracovní a testovací servery, které nejsou přístupné nedůvěryhodným sítím, jako je například Internet.</span><span class="sxs-lookup"><span data-stu-id="78c3b-499">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## <a name="app_offlinehtm"></a><span data-ttu-id="78c3b-500">App_offline.htm</span><span class="sxs-lookup"><span data-stu-id="78c3b-500">app_offline.htm</span></span>

<span data-ttu-id="78c3b-501">Pokud je v kořenovém adresáři aplikace zjištěn soubor s názvem *app_offline.htm,* pokusí se ASP.NET základní modul řádně vypnout aplikaci a zastavit zpracování příchozích požadavků.</span><span class="sxs-lookup"><span data-stu-id="78c3b-501">If a file with the name *app_offline.htm* is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="78c3b-502">Pokud je aplikace stále spuštěna po `shutdownTimeLimit`počtu sekund definovaném v aplikaci , ASP.NET základní modul unejde spuštěný proces.</span><span class="sxs-lookup"><span data-stu-id="78c3b-502">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="78c3b-503">Zatímco soubor *app_offline.htm* je k dispozici, ASP.NET Core Module reaguje na požadavky odesláním zpět obsah souboru *app_offline.htm.*</span><span class="sxs-lookup"><span data-stu-id="78c3b-503">While the *app_offline.htm* file is present, the ASP.NET Core Module responds to requests by sending back the contents of the *app_offline.htm* file.</span></span> <span data-ttu-id="78c3b-504">Po odebrání souboru *app_offline.htm* spustí další požadavek aplikaci.</span><span class="sxs-lookup"><span data-stu-id="78c3b-504">When the *app_offline.htm* file is removed, the next request starts the app.</span></span>

<span data-ttu-id="78c3b-505">Při použití mimoproceshostingového modelu se aplikace nemusí okamžitě vypnout, pokud existuje otevřené připojení.</span><span class="sxs-lookup"><span data-stu-id="78c3b-505">When using the out-of-process hosting model, the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="78c3b-506">Například připojení websocket může zpozdit vypnutí aplikace.</span><span class="sxs-lookup"><span data-stu-id="78c3b-506">For example, a websocket connection may delay app shut down.</span></span>

## <a name="start-up-error-page"></a><span data-ttu-id="78c3b-507">Chybová stránka při spuštění</span><span class="sxs-lookup"><span data-stu-id="78c3b-507">Start-up error page</span></span>

<span data-ttu-id="78c3b-508">Hostování v průběhu procesu i mimo proces vytváří vlastní chybové stránky, když se jim nepodaří spustit aplikaci.</span><span class="sxs-lookup"><span data-stu-id="78c3b-508">Both in-process and out-of-process hosting produce custom error pages when they fail to start the app.</span></span>

<span data-ttu-id="78c3b-509">Pokud ASP.NET základnímodul nenalezne obslužnou rutinu požadavku v procesu nebo mimo proces, zobrazí se stránka s kódem chyby selhání *zatížení 500.0 - In-Process/Out-Of-Process Handler.*</span><span class="sxs-lookup"><span data-stu-id="78c3b-509">If the ASP.NET Core Module fails to find either the in-process or out-of-process request handler, a *500.0 - In-Process/Out-Of-Process Handler Load Failure* status code page appears.</span></span>

<span data-ttu-id="78c3b-510">Pro hostování v procesu, pokud ASP.NET základní modul nepodaří spustit aplikaci, zobrazí se stránka stavového kódu *500.30 - Start Failure.*</span><span class="sxs-lookup"><span data-stu-id="78c3b-510">For in-process hosting if the ASP.NET Core Module fails to start the app, a *500.30 - Start Failure* status code page appears.</span></span>

<span data-ttu-id="78c3b-511">Pro mimoproceshosting, pokud ASP.NET core modul se nezdaří spustit proces back-endu nebo proces back-endu spustí, ale nedokáže poslouchat na nakonfigurovaném portu, zobrazí se stránka s kódem stavového kódu *selhání procesu 502.5 - Process Failure.*</span><span class="sxs-lookup"><span data-stu-id="78c3b-511">For out-of-process hosting if the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span>

<span data-ttu-id="78c3b-512">Chcete-li tuto stránku potlačit a vrátit se k výchozí `disableStartUpErrorPage` stránce se stavovým kódem iIS 5xx, použijte atribut.</span><span class="sxs-lookup"><span data-stu-id="78c3b-512">To suppress this page and revert to the default IIS 5xx status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="78c3b-513">Další informace o konfiguraci vlastních chybových zpráv naleznete v [tématu Http Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span><span class="sxs-lookup"><span data-stu-id="78c3b-513">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

## <a name="log-creation-and-redirection"></a><span data-ttu-id="78c3b-514">Vytvoření a přesměrování protokolu</span><span class="sxs-lookup"><span data-stu-id="78c3b-514">Log creation and redirection</span></span>

<span data-ttu-id="78c3b-515">ASP.NET Core Module přesměruje výstup konzoly stdout a `stdoutLogEnabled` `stdoutLogFile` stderr `aspNetCore` na disk, pokud jsou nastaveny atributy a prvek.</span><span class="sxs-lookup"><span data-stu-id="78c3b-515">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="78c3b-516">Všechny složky `stdoutLogFile` v cestě jsou vytvořeny modulem při vytvoření souboru protokolu.</span><span class="sxs-lookup"><span data-stu-id="78c3b-516">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="78c3b-517">Fond aplikací musí mít přístup pro zápis do umístění, `IIS AppPool\<app_pool_name>` kde jsou protokoly zapsány (slouží k poskytnutí oprávnění k zápisu).</span><span class="sxs-lookup"><span data-stu-id="78c3b-517">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="78c3b-518">Protokoly nejsou otočeny, pokud nedojde k recyklaci nebo restartování procesu.</span><span class="sxs-lookup"><span data-stu-id="78c3b-518">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="78c3b-519">Je odpovědností hoster omezit místo na disku protokoly spotřebovávají.</span><span class="sxs-lookup"><span data-stu-id="78c3b-519">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="78c3b-520">Použití protokolu stdout se doporučuje pouze pro řešení problémů se spuštěním aplikace při hostování ve službě IIS nebo při použití [podpory služby IIS v době vývoje se službou Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), nikoli při místním ladění a spuštění aplikace se službou IIS Express.</span><span class="sxs-lookup"><span data-stu-id="78c3b-520">Using the stdout log is only recommended for troubleshooting app startup issues when hosting on IIS or when using [development-time support for IIS with Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), not while debugging locally and running the app with IIS Express.</span></span>

<span data-ttu-id="78c3b-521">Nepoužívejte protokol stdout pro obecné účely protokolování aplikací.</span><span class="sxs-lookup"><span data-stu-id="78c3b-521">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="78c3b-522">Pro rutinní protokolování v aplikaci ASP.NET Core použijte knihovnu protokolování, která omezuje velikost souboru protokolu a otáčí protokoly.</span><span class="sxs-lookup"><span data-stu-id="78c3b-522">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="78c3b-523">Další informace naleznete [v tématu zprostředkovatelé protokolování třetích stran](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="78c3b-523">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="78c3b-524">Časové razítko a přípona souboru jsou přidány automaticky při vytvoření souboru protokolu.</span><span class="sxs-lookup"><span data-stu-id="78c3b-524">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="78c3b-525">Název souboru protokolu se skládá připojením časového razítka, ID procesu a přípony `stdoutLogFile` souboru (*.log*) k poslednímu segmentu cesty (obvykle *stdout)* vymezeném podtržítky.</span><span class="sxs-lookup"><span data-stu-id="78c3b-525">The log file name is composed by appending the timestamp, process ID, and file extension (*.log*) to the last segment of the `stdoutLogFile` path (typically *stdout*) delimited by underscores.</span></span> <span data-ttu-id="78c3b-526">Pokud `stdoutLogFile` cesta končí *stdout*, protokol pro aplikaci s PID 1934 vytvořený na 2/5/2018 v 19:42:32 má název souboru *stdout_20180205194132_1934.log*.</span><span class="sxs-lookup"><span data-stu-id="78c3b-526">If the `stdoutLogFile` path ends with *stdout*, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name *stdout_20180205194132_1934.log*.</span></span>

<span data-ttu-id="78c3b-527">Pokud `stdoutLogEnabled` je false, chyby, ke kterým dojde při spuštění aplikace jsou zachyceny a vyzařovány do protokolu událostí až 30 KB.</span><span class="sxs-lookup"><span data-stu-id="78c3b-527">If `stdoutLogEnabled` is false, errors that occur on app startup are captured and emitted to the event log up to 30 KB.</span></span> <span data-ttu-id="78c3b-528">Po spuštění jsou všechny další protokoly zahozeny.</span><span class="sxs-lookup"><span data-stu-id="78c3b-528">After startup, all additional logs are discarded.</span></span>

<span data-ttu-id="78c3b-529">Následující ukázkový `aspNetCore` prvek konfiguruje protokolování stdout na relativní cestě `.\log\`.</span><span class="sxs-lookup"><span data-stu-id="78c3b-529">The following sample `aspNetCore` element configures stdout logging at the relative path `.\log\`.</span></span> <span data-ttu-id="78c3b-530">Zkontrolujte, zda má identita uživatele AppPoolu oprávnění k zápisu do poskytnuté cesty.</span><span class="sxs-lookup"><span data-stu-id="78c3b-530">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout"
    hostingModel="inprocess">
</aspNetCore>
```

<span data-ttu-id="78c3b-531">Při publikování aplikace pro nasazení služby Azure App `stdoutLogFile` Service `\\?\%home%\LogFiles\stdout`nastaví sada Web SDK hodnotu na .</span><span class="sxs-lookup"><span data-stu-id="78c3b-531">When publishing an app for Azure App Service deployment, the Web SDK sets the `stdoutLogFile` value to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="78c3b-532">Proměnná `%home` prostředí je předdefinovaná pro aplikace hostované službou Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="78c3b-532">The `%home` environment variable is predefined for apps hosted by Azure App Service.</span></span>

<span data-ttu-id="78c3b-533">Další informace o formátech cest naleznete v [tématu Formátcesty k souborům v systémech Windows](/dotnet/standard/io/file-path-formats).</span><span class="sxs-lookup"><span data-stu-id="78c3b-533">For more information on path formats, see [File path formats on Windows systems](/dotnet/standard/io/file-path-formats).</span></span>

## <a name="enhanced-diagnostic-logs"></a><span data-ttu-id="78c3b-534">Rozšířené diagnostické protokoly</span><span class="sxs-lookup"><span data-stu-id="78c3b-534">Enhanced diagnostic logs</span></span>

<span data-ttu-id="78c3b-535">Základní modul ASP.NET je konfigurovatelný tak, aby poskytoval protokoly rozšířené diagnostiky.</span><span class="sxs-lookup"><span data-stu-id="78c3b-535">The ASP.NET Core Module is configurable to provide enhanced diagnostics logs.</span></span> <span data-ttu-id="78c3b-536">Přidejte `<handlerSettings>` prvek `<aspNetCore>` do prvku v *souboru web.config*. Nastavení `debugLevel` na `TRACE` vystavit vyšší věrnost diagnostických informací:</span><span class="sxs-lookup"><span data-stu-id="78c3b-536">Add the `<handlerSettings>` element to the `<aspNetCore>` element in *web.config*. Setting the `debugLevel` to `TRACE` exposes a higher fidelity of diagnostic information:</span></span>

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

<span data-ttu-id="78c3b-537">Složky v cestě k `<handlerSetting>` hodnotě *(protokoly* v předchozím příkladu) nejsou vytvořeny modulem automaticky a měly by existovat předem v nasazení.</span><span class="sxs-lookup"><span data-stu-id="78c3b-537">Folders in the path provided to the `<handlerSetting>` value (*logs* in the preceding example) aren't created by the module automatically and should pre-exist in the deployment.</span></span> <span data-ttu-id="78c3b-538">Fond aplikací musí mít přístup pro zápis do umístění, `IIS AppPool\<app_pool_name>` kde jsou protokoly zapsány (slouží k poskytnutí oprávnění k zápisu).</span><span class="sxs-lookup"><span data-stu-id="78c3b-538">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="78c3b-539">Hodnoty úrovně`debugLevel`ladění ( ) mohou zahrnovat úroveň i umístění.</span><span class="sxs-lookup"><span data-stu-id="78c3b-539">Debug level (`debugLevel`) values can include both the level and the location.</span></span>

<span data-ttu-id="78c3b-540">Úrovně (v pořadí od nejméně k většině verbose):</span><span class="sxs-lookup"><span data-stu-id="78c3b-540">Levels (in order from least to most verbose):</span></span>

* <span data-ttu-id="78c3b-541">ERROR</span><span class="sxs-lookup"><span data-stu-id="78c3b-541">ERROR</span></span>
* <span data-ttu-id="78c3b-542">WARNING</span><span class="sxs-lookup"><span data-stu-id="78c3b-542">WARNING</span></span>
* <span data-ttu-id="78c3b-543">Info</span><span class="sxs-lookup"><span data-stu-id="78c3b-543">INFO</span></span>
* <span data-ttu-id="78c3b-544">TRACE</span><span class="sxs-lookup"><span data-stu-id="78c3b-544">TRACE</span></span>

<span data-ttu-id="78c3b-545">Umístění (více míst je povoleno):</span><span class="sxs-lookup"><span data-stu-id="78c3b-545">Locations (multiple locations are permitted):</span></span>

* <span data-ttu-id="78c3b-546">Konzoly</span><span class="sxs-lookup"><span data-stu-id="78c3b-546">CONSOLE</span></span>
* <span data-ttu-id="78c3b-547">Eventlog</span><span class="sxs-lookup"><span data-stu-id="78c3b-547">EVENTLOG</span></span>
* <span data-ttu-id="78c3b-548">SOUBOR</span><span class="sxs-lookup"><span data-stu-id="78c3b-548">FILE</span></span>

<span data-ttu-id="78c3b-549">Nastavení obslužné rutiny lze také poskytnout prostřednictvím proměnných prostředí:</span><span class="sxs-lookup"><span data-stu-id="78c3b-549">The handler settings can also be provided via environment variables:</span></span>

* <span data-ttu-id="78c3b-550">`ASPNETCORE_MODULE_DEBUG_FILE`&ndash; Cesta k souboru protokolu ladění.</span><span class="sxs-lookup"><span data-stu-id="78c3b-550">`ASPNETCORE_MODULE_DEBUG_FILE` &ndash; Path to the debug log file.</span></span> <span data-ttu-id="78c3b-551">(Výchozí: *aspnetcore-debug.log*)</span><span class="sxs-lookup"><span data-stu-id="78c3b-551">(Default: *aspnetcore-debug.log*)</span></span>
* <span data-ttu-id="78c3b-552">`ASPNETCORE_MODULE_DEBUG`&ndash; Nastavení úrovně ladění.</span><span class="sxs-lookup"><span data-stu-id="78c3b-552">`ASPNETCORE_MODULE_DEBUG` &ndash; Debug level setting.</span></span>

> [!WARNING]
> <span data-ttu-id="78c3b-553">**Nenechávejte** protokolování ladění povolené v nasazení déle, než je nutné k řešení problému.</span><span class="sxs-lookup"><span data-stu-id="78c3b-553">Do **not** leave debug logging enabled in the deployment for longer than required to troubleshoot an issue.</span></span> <span data-ttu-id="78c3b-554">Velikost protokolu není omezena.</span><span class="sxs-lookup"><span data-stu-id="78c3b-554">The size of the log isn't limited.</span></span> <span data-ttu-id="78c3b-555">Ponechání protokolu ladění povoleno může vyčerpat dostupné místo na disku a selhání serveru nebo služby aplikace.</span><span class="sxs-lookup"><span data-stu-id="78c3b-555">Leaving the debug log enabled can exhaust the available disk space and crash the server or app service.</span></span>

<span data-ttu-id="78c3b-556">Příklad `aspNetCore` prvku v souboru *web.config* naleznete v části Konfigurace [pomocí souboru web.config.](#configuration-with-webconfig)</span><span class="sxs-lookup"><span data-stu-id="78c3b-556">See [Configuration with web.config](#configuration-with-webconfig) for an example of the `aspNetCore` element in the *web.config* file.</span></span>

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="78c3b-557">Konfigurace serveru proxy používá protokol HTTP a token párování</span><span class="sxs-lookup"><span data-stu-id="78c3b-557">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="78c3b-558">*Platí pouze pro mimoprocesový hosting.*</span><span class="sxs-lookup"><span data-stu-id="78c3b-558">*Only applies to out-of-process hosting.*</span></span>

<span data-ttu-id="78c3b-559">Proxy server vytvořený mezi ASP.NET Core Module a Kestrel používá protokol HTTP.</span><span class="sxs-lookup"><span data-stu-id="78c3b-559">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="78c3b-560">Neexistuje žádné riziko odposlouchávání provozu mezi modulem a Kestrel z umístění mimo server.</span><span class="sxs-lookup"><span data-stu-id="78c3b-560">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="78c3b-561">Token párování se používá k zajištění, že požadavky přijaté Kestrel byly proxied službou IIS a nepochází z jiného zdroje.</span><span class="sxs-lookup"><span data-stu-id="78c3b-561">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="78c3b-562">Token párování je vytvořen a nastaven do`ASPNETCORE_TOKEN`proměnné prostředí ( ) modulem.</span><span class="sxs-lookup"><span data-stu-id="78c3b-562">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="78c3b-563">Token párování je také nastaven do`MS-ASPNETCORE-TOKEN`záhlaví ( ) na každém proxied požadavku.</span><span class="sxs-lookup"><span data-stu-id="78c3b-563">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="78c3b-564">Služby IIS Middleware zkontroluje každý požadavek, který obdrží, a potvrdí, že hodnota záhlaví párování tokenu odpovídá hodnotě proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="78c3b-564">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="78c3b-565">Pokud jsou hodnoty tokenu neodpovídající, požadavek je zaznamenána a odmítnuta.</span><span class="sxs-lookup"><span data-stu-id="78c3b-565">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="78c3b-566">Proměnná prostředí tokenu párování a přenosy mezi modulem a Kestrelem nejsou přístupné z umístění mimo server.</span><span class="sxs-lookup"><span data-stu-id="78c3b-566">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="78c3b-567">Bez znalosti hodnoty tokenu párování nemůže útočník odeslat požadavky, které by obcéme kontroly v middlewaru služby IIS.</span><span class="sxs-lookup"><span data-stu-id="78c3b-567">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="78c3b-568">ASP.NET základní modul se sdílenou konfigurací služby IIS</span><span class="sxs-lookup"><span data-stu-id="78c3b-568">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="78c3b-569">Instalační program ASP.NET Core Module běží s oprávněními účtu **TrustedInstaller.**</span><span class="sxs-lookup"><span data-stu-id="78c3b-569">The ASP.NET Core Module installer runs with the privileges of the **TrustedInstaller** account.</span></span> <span data-ttu-id="78c3b-570">Vzhledem k tomu, že místní systémový účet nemá oprávnění ke změně cesty sdílení používané sdílenou konfigurací služby IIS, instalační program vyvolá chybu odepření přístupu při pokusu o konfiguraci nastavení modulu v souboru *applicationHost.config* ve sdílené složce.</span><span class="sxs-lookup"><span data-stu-id="78c3b-570">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the *applicationHost.config* file on the share.</span></span>

<span data-ttu-id="78c3b-571">Při použití sdílené konfigurace služby IIS ve stejném počítači jako instalace služby `OPT_NO_SHARED_CONFIG_CHECK` IIS `1`spusťte instalační program sady ASP.NET Core Hosting Bundle s parametrem nastaveným na :</span><span class="sxs-lookup"><span data-stu-id="78c3b-571">When using an IIS Shared Configuration on the same machine as the IIS installation, run the ASP.NET Core Hosting Bundle installer with the `OPT_NO_SHARED_CONFIG_CHECK` parameter set to `1`:</span></span>

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

<span data-ttu-id="78c3b-572">Pokud cesta ke sdílené konfiguraci není ve stejném počítači jako instalace služby IIS, postupujte takto:</span><span class="sxs-lookup"><span data-stu-id="78c3b-572">When the path to the shared configuration isn't on the same machine as the IIS installation, follow these steps:</span></span>

1. <span data-ttu-id="78c3b-573">Zakažte sdílenou konfiguraci služby IIS.</span><span class="sxs-lookup"><span data-stu-id="78c3b-573">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="78c3b-574">Spusťte instalační program.</span><span class="sxs-lookup"><span data-stu-id="78c3b-574">Run the installer.</span></span>
1. <span data-ttu-id="78c3b-575">Exportujte aktualizovaný soubor *applicationHost.config* do sdílené složky.</span><span class="sxs-lookup"><span data-stu-id="78c3b-575">Export the updated *applicationHost.config* file to the share.</span></span>
1. <span data-ttu-id="78c3b-576">Znovu povolte sdílenou konfiguraci služby IIS.</span><span class="sxs-lookup"><span data-stu-id="78c3b-576">Re-enable the IIS Shared Configuration.</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="78c3b-577">Protokoly instalačního programu verze modulu a instalačního programu hostingového balíčku</span><span class="sxs-lookup"><span data-stu-id="78c3b-577">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="78c3b-578">Chcete-li zjistit verzi nainstalovaného ASP.NET základního modulu:</span><span class="sxs-lookup"><span data-stu-id="78c3b-578">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="78c3b-579">V hostitelském systému přejděte na *%windir%\System32\inetsrv*.</span><span class="sxs-lookup"><span data-stu-id="78c3b-579">On the hosting system, navigate to *%windir%\System32\inetsrv*.</span></span>
1. <span data-ttu-id="78c3b-580">Vyhledejte soubor *aspnetcore.dll.*</span><span class="sxs-lookup"><span data-stu-id="78c3b-580">Locate the *aspnetcore.dll* file.</span></span>
1. <span data-ttu-id="78c3b-581">Klepněte pravým tlačítkem myši na soubor a v kontextové nabídce vyberte **Vlastnosti.**</span><span class="sxs-lookup"><span data-stu-id="78c3b-581">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="78c3b-582">Vyberte kartu **Podrobnosti.** **Verze souboru** a **verze produktu** představují nainstalovanou verzi modulu.</span><span class="sxs-lookup"><span data-stu-id="78c3b-582">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="78c3b-583">Protokoly instalačního programu instalačního programu instalačního programu sady Hosting bundle pro modul naleznete na adrese *\\C:\\Users %UserName%\\AppData\\Local\\Temp*. Soubor je pojmenován *dd_DotNetCoreWinSvrHosting__\<časové razítko>_000_AspNetCoreModule_x64.log*.</span><span class="sxs-lookup"><span data-stu-id="78c3b-583">The Hosting Bundle installer logs for the module are found at *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. The file is named *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="78c3b-584">Umístění modulů, schémat a konfiguračních souborů</span><span class="sxs-lookup"><span data-stu-id="78c3b-584">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="78c3b-585">Modul</span><span class="sxs-lookup"><span data-stu-id="78c3b-585">Module</span></span>

<span data-ttu-id="78c3b-586">**IIS (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="78c3b-586">**IIS (x86/amd64):**</span></span>

* <span data-ttu-id="78c3b-587">%windir%\System32\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="78c3b-587">%windir%\System32\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="78c3b-588">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="78c3b-588">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="78c3b-589">%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="78c3b-589">%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="78c3b-590">%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="78c3b-590">%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

<span data-ttu-id="78c3b-591">**IIS Express (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="78c3b-591">**IIS Express (x86/amd64):**</span></span>

* <span data-ttu-id="78c3b-592">%ProgramFiles%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="78c3b-592">%ProgramFiles%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="78c3b-593">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="78c3b-593">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="78c3b-594">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="78c3b-594">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="78c3b-595">%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="78c3b-595">%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

### <a name="schema"></a><span data-ttu-id="78c3b-596">Schéma</span><span class="sxs-lookup"><span data-stu-id="78c3b-596">Schema</span></span>

<span data-ttu-id="78c3b-597">**IIS**</span><span class="sxs-lookup"><span data-stu-id="78c3b-597">**IIS**</span></span>

* <span data-ttu-id="78c3b-598">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="78c3b-598">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span></span>

* <span data-ttu-id="78c3b-599">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span><span class="sxs-lookup"><span data-stu-id="78c3b-599">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span></span>

<span data-ttu-id="78c3b-600">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="78c3b-600">**IIS Express**</span></span>

* <span data-ttu-id="78c3b-601">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="78c3b-601">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span></span>

* <span data-ttu-id="78c3b-602">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span><span class="sxs-lookup"><span data-stu-id="78c3b-602">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span></span>

### <a name="configuration"></a><span data-ttu-id="78c3b-603">Konfigurace</span><span class="sxs-lookup"><span data-stu-id="78c3b-603">Configuration</span></span>

<span data-ttu-id="78c3b-604">**IIS**</span><span class="sxs-lookup"><span data-stu-id="78c3b-604">**IIS**</span></span>

* <span data-ttu-id="78c3b-605">%windir%\System32\inetsrv\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="78c3b-605">%windir%\System32\inetsrv\config\applicationHost.config</span></span>

<span data-ttu-id="78c3b-606">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="78c3b-606">**IIS Express**</span></span>

* <span data-ttu-id="78c3b-607">Visual Studio: {APPLICATION\\ROOT} .vs\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="78c3b-607">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span></span>

* <span data-ttu-id="78c3b-608">*Iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span><span class="sxs-lookup"><span data-stu-id="78c3b-608">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span></span>

<span data-ttu-id="78c3b-609">Soubory lze nalézt hledáním *aspnetcore* v souboru *applicationHost.config.*</span><span class="sxs-lookup"><span data-stu-id="78c3b-609">The files can be found by searching for *aspnetcore* in the *applicationHost.config* file.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="78c3b-610">Základní modul ASP.NET je nativní modul služby IIS, který se zapojuje do kanálu služby IIS a předává webové požadavky do backendu ASP.NET aplikací core.</span><span class="sxs-lookup"><span data-stu-id="78c3b-610">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to forward web requests to backend ASP.NET Core apps.</span></span>

<span data-ttu-id="78c3b-611">Podporované verze systému Windows:</span><span class="sxs-lookup"><span data-stu-id="78c3b-611">Supported Windows versions:</span></span>

* <span data-ttu-id="78c3b-612">Windows 7 nebo novější</span><span class="sxs-lookup"><span data-stu-id="78c3b-612">Windows 7 or later</span></span>
* <span data-ttu-id="78c3b-613">Windows Server 2008 R2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="78c3b-613">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="78c3b-614">Modul pracuje pouze s Kestrelem.</span><span class="sxs-lookup"><span data-stu-id="78c3b-614">The module only works with Kestrel.</span></span> <span data-ttu-id="78c3b-615">Modul není kompatibilní se [souborem HTTP.sys](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="78c3b-615">The module is incompatible with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

<span data-ttu-id="78c3b-616">Vzhledem k tomu, že aplikace ASP.NET Core běží v procesu odděleném od pracovního procesu služby IIS, modul také zpracovává správu procesů.</span><span class="sxs-lookup"><span data-stu-id="78c3b-616">Because ASP.NET Core apps run in a process separate from the IIS worker process, the module also handles process management.</span></span> <span data-ttu-id="78c3b-617">Modul spustí proces pro aplikaci ASP.NET Core při příchodu prvního požadavku a restartuje aplikaci, pokud dojde k chybě.</span><span class="sxs-lookup"><span data-stu-id="78c3b-617">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it crashes.</span></span> <span data-ttu-id="78c3b-618">Jedná se v podstatě o stejné chování jako u aplikací ASP.NET 4.x, které běží v procesu ve službě IIS, které jsou spravovány [službou aktivace procesů systému Windows (WAS).](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was)</span><span class="sxs-lookup"><span data-stu-id="78c3b-618">This is essentially the same behavior as seen with ASP.NET 4.x apps that run in-process in IIS that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="78c3b-619">Následující diagram znázorňuje vztah mezi iis, ASP.NET základní modul a aplikací:</span><span class="sxs-lookup"><span data-stu-id="78c3b-619">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app:</span></span>

![Modul ASP.NET Core](aspnet-core-module/_static/ancm-outofprocess.png)

<span data-ttu-id="78c3b-621">Požadavky dorazí z webu do ovladače HTTP.sys v režimu jádra.</span><span class="sxs-lookup"><span data-stu-id="78c3b-621">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="78c3b-622">Řidič směruje požadavky do služby IIS na nakonfigurovaném portu webu, obvykle 80 (HTTP) nebo 443 (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="78c3b-622">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="78c3b-623">Modul předá požadavky Kestrel na náhodný port pro aplikaci, která není port 80 nebo 443.</span><span class="sxs-lookup"><span data-stu-id="78c3b-623">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="78c3b-624">Modul určuje port prostřednictvím proměnné prostředí při spuštění a [middleware integrace služby IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) nakonfiguruje server tak, aby naslouchal . `http://localhost:{port}`</span><span class="sxs-lookup"><span data-stu-id="78c3b-624">The module specifies the port via an environment variable at startup, and the [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) configures the server to listen on `http://localhost:{port}`.</span></span> <span data-ttu-id="78c3b-625">Jsou prováděny další kontroly a požadavky, které nepocházejí z modulu, jsou odmítnuty.</span><span class="sxs-lookup"><span data-stu-id="78c3b-625">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="78c3b-626">Modul nepodporuje předávání https, takže požadavky jsou předávány přes HTTP i v případě, že přijaté službou IIS přes protokol HTTPS.</span><span class="sxs-lookup"><span data-stu-id="78c3b-626">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="78c3b-627">Poté, co Kestrel vyzvedne požadavek z modulu, je požadavek posunut do kanálu ASP.NET middleware.</span><span class="sxs-lookup"><span data-stu-id="78c3b-627">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="78c3b-628">Middleware kanálu zpracovává požadavek a předá `HttpContext` jej jako instanci logiky aplikace.</span><span class="sxs-lookup"><span data-stu-id="78c3b-628">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="78c3b-629">Middleware přidané integrací služby IIS aktualizuje schéma, vzdálenou IP adresu a pathbase tak, aby odpovídaly předání požadavku ke společnosti Kestrel.</span><span class="sxs-lookup"><span data-stu-id="78c3b-629">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="78c3b-630">Odpověď aplikace je předána zpět do služby IIS, která ji odešle zpět do klienta HTTP, který inicioval požadavek.</span><span class="sxs-lookup"><span data-stu-id="78c3b-630">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="78c3b-631">Mnoho nativních modulů, například ověřování systému Windows, zůstává aktivní.</span><span class="sxs-lookup"><span data-stu-id="78c3b-631">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="78c3b-632">Další informace o modulech služby IIS aktivních <xref:host-and-deploy/iis/modules>v modulu ASP.NET Core naleznete v tématu .</span><span class="sxs-lookup"><span data-stu-id="78c3b-632">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="78c3b-633">Základní modul ASP.NET může také:</span><span class="sxs-lookup"><span data-stu-id="78c3b-633">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="78c3b-634">Nastavte proměnné prostředí pro pracovní proces.</span><span class="sxs-lookup"><span data-stu-id="78c3b-634">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="78c3b-635">Protokolovat výstup stdout do úložiště souborů pro řešení problémů se spuštěním.</span><span class="sxs-lookup"><span data-stu-id="78c3b-635">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="78c3b-636">Přepošlete ověřovací tokeny systému Windows.</span><span class="sxs-lookup"><span data-stu-id="78c3b-636">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="78c3b-637">Jak nainstalovat a používat ASP.NET core modul</span><span class="sxs-lookup"><span data-stu-id="78c3b-637">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="78c3b-638">Pokyny k instalaci ASP.NET základního modulu naleznete [v tématu Instalace sady .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span><span class="sxs-lookup"><span data-stu-id="78c3b-638">For instructions on how to install the ASP.NET Core Module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="78c3b-639">Konfigurace pomocí souboru web.config</span><span class="sxs-lookup"><span data-stu-id="78c3b-639">Configuration with web.config</span></span>

<span data-ttu-id="78c3b-640">Základní modul ASP.NET je `aspNetCore` konfigurován s `system.webServer` částí uzlu v souboru *web.config* webu.</span><span class="sxs-lookup"><span data-stu-id="78c3b-640">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="78c3b-641">Následující soubor *web.config* je publikován pro [nasazení závislé na rámci](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) a konfiguruje ASP.NET core modul pro zpracování požadavků webu:</span><span class="sxs-lookup"><span data-stu-id="78c3b-641">The following *web.config* file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

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

<span data-ttu-id="78c3b-642">Následující *web.config* je publikován pro [samostatné nasazení](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span><span class="sxs-lookup"><span data-stu-id="78c3b-642">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

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

<span data-ttu-id="78c3b-643">Když se aplikace nasadí do `stdoutLogFile` [služby Azure App Service](https://azure.microsoft.com/services/app-service/), je cesta nastavená na `\\?\%home%\LogFiles\stdout`.</span><span class="sxs-lookup"><span data-stu-id="78c3b-643">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="78c3b-644">Cesta uloží protokoly stdout do složky *LogFiles,* což je umístění automaticky vytvořené službou.</span><span class="sxs-lookup"><span data-stu-id="78c3b-644">The path saves stdout logs to the *LogFiles* folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="78c3b-645">Informace o konfiguraci podaplikací služby IIS naleznete v tématu <xref:host-and-deploy/iis/index#sub-applications>.</span><span class="sxs-lookup"><span data-stu-id="78c3b-645">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="78c3b-646">Atributy prvku aspNetCore</span><span class="sxs-lookup"><span data-stu-id="78c3b-646">Attributes of the aspNetCore element</span></span>

| <span data-ttu-id="78c3b-647">Atribut</span><span class="sxs-lookup"><span data-stu-id="78c3b-647">Attribute</span></span> | <span data-ttu-id="78c3b-648">Popis</span><span class="sxs-lookup"><span data-stu-id="78c3b-648">Description</span></span> | <span data-ttu-id="78c3b-649">Výchozí</span><span class="sxs-lookup"><span data-stu-id="78c3b-649">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="78c3b-650">Volitelný atribut řetězce.</span><span class="sxs-lookup"><span data-stu-id="78c3b-650">Optional string attribute.</span></span></p><p><span data-ttu-id="78c3b-651">Argumenty ke spustitelnému souboru zadanému v **processPath**.</span><span class="sxs-lookup"><span data-stu-id="78c3b-651">Arguments to the executable specified in **processPath**.</span></span></p>| |
| `disableStartUpErrorPage` | <p><span data-ttu-id="78c3b-652">Volitelný logický atribut.</span><span class="sxs-lookup"><span data-stu-id="78c3b-652">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="78c3b-653">Pokud true, **502.5 - Selhání procesu** stránka je potlačena a 502 stavový kód stránky nakonfigurované v *web.config* má přednost.</span><span class="sxs-lookup"><span data-stu-id="78c3b-653">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="78c3b-654">Volitelný logický atribut.</span><span class="sxs-lookup"><span data-stu-id="78c3b-654">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="78c3b-655">Pokud true, token je předán podřízený proces naslouchání na %ASPNETCORE_PORT% jako záhlaví 'MS-ASPNETCORE-WINAUTHTOKEN' na požadavek.</span><span class="sxs-lookup"><span data-stu-id="78c3b-655">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="78c3b-656">Je odpovědností tohoto procesu volání CloseHandle na tento token na požadavek.</span><span class="sxs-lookup"><span data-stu-id="78c3b-656">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `processesPerApplication` | <p><span data-ttu-id="78c3b-657">Volitelný atribut celé číslo.</span><span class="sxs-lookup"><span data-stu-id="78c3b-657">Optional integer attribute.</span></span></p><p><span data-ttu-id="78c3b-658">Určuje počet instancí procesu určeného v nastavení **processPath,** které lze přetavit podle aplikace.</span><span class="sxs-lookup"><span data-stu-id="78c3b-658">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="78c3b-659">Nastavení `processesPerApplication` se nedoporučuje.</span><span class="sxs-lookup"><span data-stu-id="78c3b-659">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="78c3b-660">Tento atribut bude odebrán v budoucí verzi.</span><span class="sxs-lookup"><span data-stu-id="78c3b-660">This attribute will be removed in a future release.</span></span></p> | <span data-ttu-id="78c3b-661">Výchozí:`1`</span><span class="sxs-lookup"><span data-stu-id="78c3b-661">Default: `1`</span></span><br><span data-ttu-id="78c3b-662">Min:`1`</span><span class="sxs-lookup"><span data-stu-id="78c3b-662">Min: `1`</span></span><br><span data-ttu-id="78c3b-663">Max:`100`</span><span class="sxs-lookup"><span data-stu-id="78c3b-663">Max: `100`</span></span> |
| `processPath` | <p><span data-ttu-id="78c3b-664">Povinný atribut řetězce.</span><span class="sxs-lookup"><span data-stu-id="78c3b-664">Required string attribute.</span></span></p><p><span data-ttu-id="78c3b-665">Cesta ke spustitelnému souboru, který spustí proces naslouchání pro požadavky HTTP.</span><span class="sxs-lookup"><span data-stu-id="78c3b-665">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="78c3b-666">Relativní cesty jsou podporovány.</span><span class="sxs-lookup"><span data-stu-id="78c3b-666">Relative paths are supported.</span></span> <span data-ttu-id="78c3b-667">Pokud cesta začíná `.`, cesta je považována za relativní ke kořenovému adresáři webu.</span><span class="sxs-lookup"><span data-stu-id="78c3b-667">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="78c3b-668">Volitelný atribut celé číslo.</span><span class="sxs-lookup"><span data-stu-id="78c3b-668">Optional integer attribute.</span></span></p><p><span data-ttu-id="78c3b-669">Určuje počet, kolikrát je povoleno selhání procesu zadaného v **processPath** za minutu.</span><span class="sxs-lookup"><span data-stu-id="78c3b-669">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="78c3b-670">Pokud je tento limit překročen, modul přestane spouštět proces po zbytek minuty.</span><span class="sxs-lookup"><span data-stu-id="78c3b-670">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p> | <span data-ttu-id="78c3b-671">Výchozí:`10`</span><span class="sxs-lookup"><span data-stu-id="78c3b-671">Default: `10`</span></span><br><span data-ttu-id="78c3b-672">Min:`0`</span><span class="sxs-lookup"><span data-stu-id="78c3b-672">Min: `0`</span></span><br><span data-ttu-id="78c3b-673">Max:`100`</span><span class="sxs-lookup"><span data-stu-id="78c3b-673">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="78c3b-674">Volitelný atribut timespan.</span><span class="sxs-lookup"><span data-stu-id="78c3b-674">Optional timespan attribute.</span></span></p><p><span data-ttu-id="78c3b-675">Určuje dobu, po kterou základní modul ASP.NET čeká na odpověď z procesu naslouchání na %ASPNETCORE_PORT%.</span><span class="sxs-lookup"><span data-stu-id="78c3b-675">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="78c3b-676">Ve verzích ASP.NET core modul, který byl dodán s vydáním ASP.NET `requestTimeout` Core 2.1 nebo novější, je zadán v hodinách, minutách a sekundách.</span><span class="sxs-lookup"><span data-stu-id="78c3b-676">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p> | <span data-ttu-id="78c3b-677">Výchozí:`00:02:00`</span><span class="sxs-lookup"><span data-stu-id="78c3b-677">Default: `00:02:00`</span></span><br><span data-ttu-id="78c3b-678">Min:`00:00:00`</span><span class="sxs-lookup"><span data-stu-id="78c3b-678">Min: `00:00:00`</span></span><br><span data-ttu-id="78c3b-679">Max:`360:00:00`</span><span class="sxs-lookup"><span data-stu-id="78c3b-679">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="78c3b-680">Volitelný atribut celé číslo.</span><span class="sxs-lookup"><span data-stu-id="78c3b-680">Optional integer attribute.</span></span></p><p><span data-ttu-id="78c3b-681">Doba trvání v sekundách, po kterou modul čeká na řádné vypnutí spustitelného souboru, když je zjištěn soubor *app_offline.htm.*</span><span class="sxs-lookup"><span data-stu-id="78c3b-681">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="78c3b-682">Výchozí:`10`</span><span class="sxs-lookup"><span data-stu-id="78c3b-682">Default: `10`</span></span><br><span data-ttu-id="78c3b-683">Min:`0`</span><span class="sxs-lookup"><span data-stu-id="78c3b-683">Min: `0`</span></span><br><span data-ttu-id="78c3b-684">Max:`600`</span><span class="sxs-lookup"><span data-stu-id="78c3b-684">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="78c3b-685">Volitelný atribut celé číslo.</span><span class="sxs-lookup"><span data-stu-id="78c3b-685">Optional integer attribute.</span></span></p><p><span data-ttu-id="78c3b-686">Doba trvání v sekundách, po kterou modul čeká na spuštění spustitelného souboru, který spustí proces naslouchání na portu.</span><span class="sxs-lookup"><span data-stu-id="78c3b-686">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="78c3b-687">Pokud je tento časový limit překročen, modul proces unejde.</span><span class="sxs-lookup"><span data-stu-id="78c3b-687">If this time limit is exceeded, the module kills the process.</span></span> <span data-ttu-id="78c3b-688">Modul se pokusí znovu spustit proces, když obdrží nový požadavek a nadále se pokouší restartovat proces na následné příchozí požadavky, pokud aplikace se nezdaří spustit **rapidFailsPerMinute** počet opakování v poslední rolling minute.</span><span class="sxs-lookup"><span data-stu-id="78c3b-688">The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="78c3b-689">Hodnota 0 (nula) **se nepovažuje** za nekonečný časový rozsah.</span><span class="sxs-lookup"><span data-stu-id="78c3b-689">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="78c3b-690">Výchozí:`120`</span><span class="sxs-lookup"><span data-stu-id="78c3b-690">Default: `120`</span></span><br><span data-ttu-id="78c3b-691">Min:`0`</span><span class="sxs-lookup"><span data-stu-id="78c3b-691">Min: `0`</span></span><br><span data-ttu-id="78c3b-692">Max:`3600`</span><span class="sxs-lookup"><span data-stu-id="78c3b-692">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="78c3b-693">Volitelný logický atribut.</span><span class="sxs-lookup"><span data-stu-id="78c3b-693">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="78c3b-694">Pokud true, **stdout** a **stderr** pro proces zadaný v **processPath** jsou přesměrovány na soubor zadaný v **stdoutLogFile**.</span><span class="sxs-lookup"><span data-stu-id="78c3b-694">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="78c3b-695">Volitelný atribut řetězce.</span><span class="sxs-lookup"><span data-stu-id="78c3b-695">Optional string attribute.</span></span></p><p><span data-ttu-id="78c3b-696">Určuje relativní nebo absolutní cestu k souboru, pro kterou jsou protokolovány **stdout** a **stderr** z procesu určeného v **processPath.**</span><span class="sxs-lookup"><span data-stu-id="78c3b-696">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="78c3b-697">Relativní cesty jsou relativní ke kořenu webu.</span><span class="sxs-lookup"><span data-stu-id="78c3b-697">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="78c3b-698">Každá cesta `.` začínající jsou relativní ke kořenu webu a všechny ostatní cesty jsou považovány za absolutní cesty.</span><span class="sxs-lookup"><span data-stu-id="78c3b-698">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="78c3b-699">Všechny složky uvedené v cestě musí existovat, aby modul mohl vytvořit soubor protokolu.</span><span class="sxs-lookup"><span data-stu-id="78c3b-699">Any folders provided in the path must exist in order for the module to create the log file.</span></span> <span data-ttu-id="78c3b-700">Pomocí oddělovačů podtržítek se do posledního segmentu cesty **stdoutLogFile** přidají časové razítko, ID procesu a přípona souboru (*.log).*</span><span class="sxs-lookup"><span data-stu-id="78c3b-700">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="78c3b-701">Pokud `.\logs\stdout` je zadán jako hodnota, příklad stdout log je uložen jako *stdout_20180205194132_1934.log* ve složce *protokoly* při uložení na 2/5/2018 v 19:41:32 s ID procesu 1934.</span><span class="sxs-lookup"><span data-stu-id="78c3b-701">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

### <a name="setting-environment-variables"></a><span data-ttu-id="78c3b-702">Nastavení proměnných prostředí</span><span class="sxs-lookup"><span data-stu-id="78c3b-702">Setting environment variables</span></span>

<span data-ttu-id="78c3b-703">Proměnné prostředí lze zadat pro proces `processPath` v atributu.</span><span class="sxs-lookup"><span data-stu-id="78c3b-703">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="78c3b-704">Zadejte proměnnou `<environmentVariable>` prostředí s `<environmentVariables>` podřízeným prvkem prvku kolekce.</span><span class="sxs-lookup"><span data-stu-id="78c3b-704">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span>

> [!WARNING]
> <span data-ttu-id="78c3b-705">Proměnné prostředí nastavené v této části jsou v konfliktu s proměnnými systémového prostředí nastavenými se stejným názvem.</span><span class="sxs-lookup"><span data-stu-id="78c3b-705">Environment variables set in this section conflict with system environment variables set with the same name.</span></span> <span data-ttu-id="78c3b-706">Pokud je proměnná prostředí nastavena jak v souboru *web.config,* `ASPNETCORE_ENVIRONMENT: Development;Development`tak na systémové úrovni v systému Windows, hodnota ze souboru *web.config* se připojí k hodnotě proměnné systémového prostředí (například ), která brání spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="78c3b-706">If an environment variable is set in both the *web.config* file and at the system level in Windows, the value from the *web.config* file becomes appended to the system environment variable value (for example, `ASPNETCORE_ENVIRONMENT: Development;Development`), which prevents the app from starting.</span></span>

<span data-ttu-id="78c3b-707">Následující příklad nastaví dvě proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="78c3b-707">The following example sets two environment variables.</span></span> <span data-ttu-id="78c3b-708">`ASPNETCORE_ENVIRONMENT`konfiguruje prostředí `Development`aplikace tak, aby .</span><span class="sxs-lookup"><span data-stu-id="78c3b-708">`ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="78c3b-709">Vývojář může dočasně nastavit tuto hodnotu v souboru *web.config,* aby se [stránka výjimky vývojáře načetla](xref:fundamentals/error-handling) při ladění výjimky aplikace.</span><span class="sxs-lookup"><span data-stu-id="78c3b-709">A developer may temporarily set this value in the *web.config* file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="78c3b-710">`CONFIG_DIR`je příkladem uživatelem definované proměnné prostředí, kde vývojář napsal kód, který při spuštění přečte hodnotu a vytvoří cestu pro načtení konfiguračního souboru aplikace.</span><span class="sxs-lookup"><span data-stu-id="78c3b-710">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

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
> <span data-ttu-id="78c3b-711">Proměnnou `ASPNETCORE_ENVIRONMENT` prostředí nastavte `Development` pouze na pracovní a testovací servery, které nejsou přístupné nedůvěryhodným sítím, jako je například Internet.</span><span class="sxs-lookup"><span data-stu-id="78c3b-711">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## <a name="app_offlinehtm"></a><span data-ttu-id="78c3b-712">App_offline.htm</span><span class="sxs-lookup"><span data-stu-id="78c3b-712">app_offline.htm</span></span>

<span data-ttu-id="78c3b-713">Pokud je v kořenovém adresáři aplikace zjištěn soubor s názvem *app_offline.htm,* pokusí se ASP.NET základní modul řádně vypnout aplikaci a zastavit zpracování příchozích požadavků.</span><span class="sxs-lookup"><span data-stu-id="78c3b-713">If a file with the name *app_offline.htm* is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="78c3b-714">Pokud je aplikace stále spuštěna po `shutdownTimeLimit`počtu sekund definovaném v aplikaci , ASP.NET základní modul unejde spuštěný proces.</span><span class="sxs-lookup"><span data-stu-id="78c3b-714">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="78c3b-715">Zatímco soubor *app_offline.htm* je k dispozici, ASP.NET Core Module reaguje na požadavky odesláním zpět obsah souboru *app_offline.htm.*</span><span class="sxs-lookup"><span data-stu-id="78c3b-715">While the *app_offline.htm* file is present, the ASP.NET Core Module responds to requests by sending back the contents of the *app_offline.htm* file.</span></span> <span data-ttu-id="78c3b-716">Po odebrání souboru *app_offline.htm* spustí další požadavek aplikaci.</span><span class="sxs-lookup"><span data-stu-id="78c3b-716">When the *app_offline.htm* file is removed, the next request starts the app.</span></span>

## <a name="start-up-error-page"></a><span data-ttu-id="78c3b-717">Chybová stránka při spuštění</span><span class="sxs-lookup"><span data-stu-id="78c3b-717">Start-up error page</span></span>

<span data-ttu-id="78c3b-718">Pokud se ASP.NET základnímodul nespustí proces back-endu nebo se spustí proces back-endu, ale na konfigurovaném portu nenaslouchá, zobrazí se stránka se stavovým kódem *selhání procesu 502.5 - Process Failure.*</span><span class="sxs-lookup"><span data-stu-id="78c3b-718">If the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span> <span data-ttu-id="78c3b-719">Chcete-li tuto stránku potlačit a vrátit se k výchozí `disableStartUpErrorPage` stránce stavového kódu iIS 502, použijte atribut.</span><span class="sxs-lookup"><span data-stu-id="78c3b-719">To suppress this page and revert to the default IIS 502 status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="78c3b-720">Další informace o konfiguraci vlastních chybových zpráv naleznete v [tématu Http Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span><span class="sxs-lookup"><span data-stu-id="78c3b-720">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

![502.5 Stránka kódu stavu selhání procesu](aspnet-core-module/_static/ANCM-502_5.png)

## <a name="log-creation-and-redirection"></a><span data-ttu-id="78c3b-722">Vytvoření a přesměrování protokolu</span><span class="sxs-lookup"><span data-stu-id="78c3b-722">Log creation and redirection</span></span>

<span data-ttu-id="78c3b-723">ASP.NET Core Module přesměruje výstup konzoly stdout a `stdoutLogEnabled` `stdoutLogFile` stderr `aspNetCore` na disk, pokud jsou nastaveny atributy a prvek.</span><span class="sxs-lookup"><span data-stu-id="78c3b-723">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="78c3b-724">Všechny složky `stdoutLogFile` v cestě jsou vytvořeny modulem při vytvoření souboru protokolu.</span><span class="sxs-lookup"><span data-stu-id="78c3b-724">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="78c3b-725">Fond aplikací musí mít přístup pro zápis do umístění, `IIS AppPool\<app_pool_name>` kde jsou protokoly zapsány (slouží k poskytnutí oprávnění k zápisu).</span><span class="sxs-lookup"><span data-stu-id="78c3b-725">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="78c3b-726">Protokoly nejsou otočeny, pokud nedojde k recyklaci nebo restartování procesu.</span><span class="sxs-lookup"><span data-stu-id="78c3b-726">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="78c3b-727">Je odpovědností hoster omezit místo na disku protokoly spotřebovávají.</span><span class="sxs-lookup"><span data-stu-id="78c3b-727">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="78c3b-728">Použití protokolu stdout se doporučuje pouze pro řešení problémů se spuštěním aplikace při hostování ve službě IIS nebo při použití [podpory služby IIS v době vývoje se službou Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), nikoli při místním ladění a spuštění aplikace se službou IIS Express.</span><span class="sxs-lookup"><span data-stu-id="78c3b-728">Using the stdout log is only recommended for troubleshooting app startup issues when hosting on IIS or when using [development-time support for IIS with Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), not while debugging locally and running the app with IIS Express.</span></span>

<span data-ttu-id="78c3b-729">Nepoužívejte protokol stdout pro obecné účely protokolování aplikací.</span><span class="sxs-lookup"><span data-stu-id="78c3b-729">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="78c3b-730">Pro rutinní protokolování v aplikaci ASP.NET Core použijte knihovnu protokolování, která omezuje velikost souboru protokolu a otáčí protokoly.</span><span class="sxs-lookup"><span data-stu-id="78c3b-730">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="78c3b-731">Další informace naleznete [v tématu zprostředkovatelé protokolování třetích stran](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="78c3b-731">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="78c3b-732">Časové razítko a přípona souboru jsou přidány automaticky při vytvoření souboru protokolu.</span><span class="sxs-lookup"><span data-stu-id="78c3b-732">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="78c3b-733">Název souboru protokolu se skládá připojením časového razítka, ID procesu a přípony `stdoutLogFile` souboru (*.log*) k poslednímu segmentu cesty (obvykle *stdout)* vymezeném podtržítky.</span><span class="sxs-lookup"><span data-stu-id="78c3b-733">The log file name is composed by appending the timestamp, process ID, and file extension (*.log*) to the last segment of the `stdoutLogFile` path (typically *stdout*) delimited by underscores.</span></span> <span data-ttu-id="78c3b-734">Pokud `stdoutLogFile` cesta končí *stdout*, protokol pro aplikaci s PID 1934 vytvořený na 2/5/2018 v 19:42:32 má název souboru *stdout_20180205194132_1934.log*.</span><span class="sxs-lookup"><span data-stu-id="78c3b-734">If the `stdoutLogFile` path ends with *stdout*, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name *stdout_20180205194132_1934.log*.</span></span>

<span data-ttu-id="78c3b-735">Následující ukázkový `aspNetCore` prvek konfiguruje protokolování stdout na relativní cestě `.\log\`.</span><span class="sxs-lookup"><span data-stu-id="78c3b-735">The following sample `aspNetCore` element configures stdout logging at the relative path `.\log\`.</span></span> <span data-ttu-id="78c3b-736">Zkontrolujte, zda má identita uživatele AppPoolu oprávnění k zápisu do poskytnuté cesty.</span><span class="sxs-lookup"><span data-stu-id="78c3b-736">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout">
</aspNetCore>
```

<span data-ttu-id="78c3b-737">Při publikování aplikace pro nasazení služby Azure App `stdoutLogFile` Service `\\?\%home%\LogFiles\stdout`nastaví sada Web SDK hodnotu na .</span><span class="sxs-lookup"><span data-stu-id="78c3b-737">When publishing an app for Azure App Service deployment, the Web SDK sets the `stdoutLogFile` value to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="78c3b-738">Proměnná `%home` prostředí je předdefinovaná pro aplikace hostované službou Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="78c3b-738">The `%home` environment variable is predefined for apps hosted by Azure App Service.</span></span>

<span data-ttu-id="78c3b-739">Chcete-li vytvořit pravidla filtru protokolování, přečtěte si části filtrování [konfigurace](xref:fundamentals/logging/index#log-filtering) a [protokolů](xref:fundamentals/logging/index#log-filtering) v dokumentaci k protokolování jádra ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="78c3b-739">To create logging filter rules, see the [Configuration](xref:fundamentals/logging/index#log-filtering) and [Log filtering](xref:fundamentals/logging/index#log-filtering) sections of the ASP.NET Core logging documentation.</span></span>

<span data-ttu-id="78c3b-740">Další informace o formátech cest naleznete v [tématu Formátcesty k souborům v systémech Windows](/dotnet/standard/io/file-path-formats).</span><span class="sxs-lookup"><span data-stu-id="78c3b-740">For more information on path formats, see [File path formats on Windows systems](/dotnet/standard/io/file-path-formats).</span></span>

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="78c3b-741">Konfigurace serveru proxy používá protokol HTTP a token párování</span><span class="sxs-lookup"><span data-stu-id="78c3b-741">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="78c3b-742">Proxy server vytvořený mezi ASP.NET Core Module a Kestrel používá protokol HTTP.</span><span class="sxs-lookup"><span data-stu-id="78c3b-742">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="78c3b-743">Neexistuje žádné riziko odposlouchávání provozu mezi modulem a Kestrel z umístění mimo server.</span><span class="sxs-lookup"><span data-stu-id="78c3b-743">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="78c3b-744">Token párování se používá k zajištění, že požadavky přijaté Kestrel byly proxied službou IIS a nepochází z jiného zdroje.</span><span class="sxs-lookup"><span data-stu-id="78c3b-744">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="78c3b-745">Token párování je vytvořen a nastaven do`ASPNETCORE_TOKEN`proměnné prostředí ( ) modulem.</span><span class="sxs-lookup"><span data-stu-id="78c3b-745">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="78c3b-746">Token párování je také nastaven do`MS-ASPNETCORE-TOKEN`záhlaví ( ) na každém proxied požadavku.</span><span class="sxs-lookup"><span data-stu-id="78c3b-746">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="78c3b-747">Služby IIS Middleware zkontroluje každý požadavek, který obdrží, a potvrdí, že hodnota záhlaví párování tokenu odpovídá hodnotě proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="78c3b-747">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="78c3b-748">Pokud jsou hodnoty tokenu neodpovídající, požadavek je zaznamenána a odmítnuta.</span><span class="sxs-lookup"><span data-stu-id="78c3b-748">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="78c3b-749">Proměnná prostředí tokenu párování a přenosy mezi modulem a Kestrelem nejsou přístupné z umístění mimo server.</span><span class="sxs-lookup"><span data-stu-id="78c3b-749">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="78c3b-750">Bez znalosti hodnoty tokenu párování nemůže útočník odeslat požadavky, které by obcéme kontroly v middlewaru služby IIS.</span><span class="sxs-lookup"><span data-stu-id="78c3b-750">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="78c3b-751">ASP.NET základní modul se sdílenou konfigurací služby IIS</span><span class="sxs-lookup"><span data-stu-id="78c3b-751">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="78c3b-752">Instalační program ASP.NET Core Module běží s oprávněními účtu **TrustedInstaller.**</span><span class="sxs-lookup"><span data-stu-id="78c3b-752">The ASP.NET Core Module installer runs with the privileges of the **TrustedInstaller** account.</span></span> <span data-ttu-id="78c3b-753">Vzhledem k tomu, že místní systémový účet nemá oprávnění ke změně cesty sdílení používané sdílenou konfigurací služby IIS, instalační program vyvolá chybu odepření přístupu při pokusu o konfiguraci nastavení modulu v souboru *applicationHost.config* ve sdílené složce.</span><span class="sxs-lookup"><span data-stu-id="78c3b-753">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the *applicationHost.config* file on the share.</span></span>

<span data-ttu-id="78c3b-754">Při použití sdílené konfigurace služby IIS postupujte takto:</span><span class="sxs-lookup"><span data-stu-id="78c3b-754">When using an IIS Shared Configuration, follow these steps:</span></span>

1. <span data-ttu-id="78c3b-755">Zakažte sdílenou konfiguraci služby IIS.</span><span class="sxs-lookup"><span data-stu-id="78c3b-755">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="78c3b-756">Spusťte instalační program.</span><span class="sxs-lookup"><span data-stu-id="78c3b-756">Run the installer.</span></span>
1. <span data-ttu-id="78c3b-757">Exportujte aktualizovaný soubor *applicationHost.config* do sdílené složky.</span><span class="sxs-lookup"><span data-stu-id="78c3b-757">Export the updated *applicationHost.config* file to the share.</span></span>
1. <span data-ttu-id="78c3b-758">Znovu povolte sdílenou konfiguraci služby IIS.</span><span class="sxs-lookup"><span data-stu-id="78c3b-758">Re-enable the IIS Shared Configuration.</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="78c3b-759">Protokoly instalačního programu verze modulu a instalačního programu hostingového balíčku</span><span class="sxs-lookup"><span data-stu-id="78c3b-759">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="78c3b-760">Chcete-li zjistit verzi nainstalovaného ASP.NET základního modulu:</span><span class="sxs-lookup"><span data-stu-id="78c3b-760">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="78c3b-761">V hostitelském systému přejděte na *%windir%\System32\inetsrv*.</span><span class="sxs-lookup"><span data-stu-id="78c3b-761">On the hosting system, navigate to *%windir%\System32\inetsrv*.</span></span>
1. <span data-ttu-id="78c3b-762">Vyhledejte soubor *aspnetcore.dll.*</span><span class="sxs-lookup"><span data-stu-id="78c3b-762">Locate the *aspnetcore.dll* file.</span></span>
1. <span data-ttu-id="78c3b-763">Klepněte pravým tlačítkem myši na soubor a v kontextové nabídce vyberte **Vlastnosti.**</span><span class="sxs-lookup"><span data-stu-id="78c3b-763">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="78c3b-764">Vyberte kartu **Podrobnosti.** **Verze souboru** a **verze produktu** představují nainstalovanou verzi modulu.</span><span class="sxs-lookup"><span data-stu-id="78c3b-764">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="78c3b-765">Protokoly instalačního programu instalačního programu instalačního programu sady Hosting bundle pro modul naleznete na adrese *\\C:\\Users %UserName%\\AppData\\Local\\Temp*. Soubor je pojmenován *dd_DotNetCoreWinSvrHosting__\<časové razítko>_000_AspNetCoreModule_x64.log*.</span><span class="sxs-lookup"><span data-stu-id="78c3b-765">The Hosting Bundle installer logs for the module are found at *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. The file is named *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="78c3b-766">Umístění modulů, schémat a konfiguračních souborů</span><span class="sxs-lookup"><span data-stu-id="78c3b-766">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="78c3b-767">Modul</span><span class="sxs-lookup"><span data-stu-id="78c3b-767">Module</span></span>

<span data-ttu-id="78c3b-768">**IIS (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="78c3b-768">**IIS (x86/amd64):**</span></span>

* <span data-ttu-id="78c3b-769">%windir%\System32\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="78c3b-769">%windir%\System32\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="78c3b-770">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="78c3b-770">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span></span>

<span data-ttu-id="78c3b-771">**IIS Express (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="78c3b-771">**IIS Express (x86/amd64):**</span></span>

* <span data-ttu-id="78c3b-772">%ProgramFiles%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="78c3b-772">%ProgramFiles%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="78c3b-773">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="78c3b-773">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span></span>

### <a name="schema"></a><span data-ttu-id="78c3b-774">Schéma</span><span class="sxs-lookup"><span data-stu-id="78c3b-774">Schema</span></span>

<span data-ttu-id="78c3b-775">**IIS**</span><span class="sxs-lookup"><span data-stu-id="78c3b-775">**IIS**</span></span>

* <span data-ttu-id="78c3b-776">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="78c3b-776">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span></span>

<span data-ttu-id="78c3b-777">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="78c3b-777">**IIS Express**</span></span>

* <span data-ttu-id="78c3b-778">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="78c3b-778">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span></span>

### <a name="configuration"></a><span data-ttu-id="78c3b-779">Konfigurace</span><span class="sxs-lookup"><span data-stu-id="78c3b-779">Configuration</span></span>

<span data-ttu-id="78c3b-780">**IIS**</span><span class="sxs-lookup"><span data-stu-id="78c3b-780">**IIS**</span></span>

* <span data-ttu-id="78c3b-781">%windir%\System32\inetsrv\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="78c3b-781">%windir%\System32\inetsrv\config\applicationHost.config</span></span>

<span data-ttu-id="78c3b-782">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="78c3b-782">**IIS Express**</span></span>

* <span data-ttu-id="78c3b-783">Visual Studio: {APPLICATION\\ROOT} .vs\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="78c3b-783">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span></span>

* <span data-ttu-id="78c3b-784">*Iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span><span class="sxs-lookup"><span data-stu-id="78c3b-784">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span></span>

<span data-ttu-id="78c3b-785">Soubory lze nalézt hledáním *aspnetcore* v souboru *applicationHost.config.*</span><span class="sxs-lookup"><span data-stu-id="78c3b-785">The files can be found by searching for *aspnetcore* in the *applicationHost.config* file.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="78c3b-786">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="78c3b-786">Additional resources</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/azure-apps/index>
* <span data-ttu-id="78c3b-787">[ASP.NET zdroj odkazu základního modulu (hlavní větev)](https://github.com/dotnet/aspnetcore/tree/master/src/Servers/IIS/AspNetCoreModuleV2) &ndash; Pomocí rozevíracího seznamu **Větev** vyberte konkrétní verzi `release/3.1`(například).</span><span class="sxs-lookup"><span data-stu-id="78c3b-787">[ASP.NET Core Module reference source (master branch)](https://github.com/dotnet/aspnetcore/tree/master/src/Servers/IIS/AspNetCoreModuleV2) &ndash; Use the **Branch** drop down list to select a specific release (for example, `release/3.1`).</span></span>
* <xref:host-and-deploy/iis/modules>
