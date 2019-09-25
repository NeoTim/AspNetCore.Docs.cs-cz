---
title: Modul ASP.NET Core
author: guardrex
description: Zjistěte, jak nakonfigurovat modul ASP.NET Core pro hostování aplikací ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/24/2019
uid: host-and-deploy/aspnet-core-module
ms.openlocfilehash: 811aafce6686b446440b146efd7449b598ed1722
ms.sourcegitcommit: e54672f5c493258dc449fac5b98faf47eb123b28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2019
ms.locfileid: "71248340"
---
# <a name="aspnet-core-module"></a><span data-ttu-id="6ee01-103">Modul ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6ee01-103">ASP.NET Core Module</span></span>

<span data-ttu-id="6ee01-104">Díky [Dykstra](https://github.com/tdykstra), [Rick Strahl](https://github.com/RickStrahl), [Novák Rossův](https://github.com/Tratcher), [Rick Anderson](https://twitter.com/RickAndMSFT), [sourabh Shirhatti](https://twitter.com/sshirhatti), [Justin Kotalik](https://github.com/jkotalik)a [Luke](https://github.com/guardrex) Latham</span><span class="sxs-lookup"><span data-stu-id="6ee01-104">By [Tom Dykstra](https://github.com/tdykstra), [Rick Strahl](https://github.com/RickStrahl), [Chris Ross](https://github.com/Tratcher), [Rick Anderson](https://twitter.com/RickAndMSFT), [Sourabh Shirhatti](https://twitter.com/sshirhatti), [Justin Kotalik](https://github.com/jkotalik), and [Luke Latham](https://github.com/guardrex)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="6ee01-105">Modul ASP.NET Core je nativním modulem služby IIS, který se připojuje k kanálu IIS na jednu z těchto:</span><span class="sxs-lookup"><span data-stu-id="6ee01-105">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to either:</span></span>

* <span data-ttu-id="6ee01-106">Hostování aplikace ASP.NET Core uvnitř pracovního procesu služby IIS (`w3wp.exe`), který se nazývá [model hostování v rámci procesu](#in-process-hosting-model).</span><span class="sxs-lookup"><span data-stu-id="6ee01-106">Host an ASP.NET Core app inside of the IIS worker process (`w3wp.exe`), called the [in-process hosting model](#in-process-hosting-model).</span></span>
* <span data-ttu-id="6ee01-107">Dopředné webové požadavky do back-endu ASP.NET Core aplikace, na které běží [Server Kestrel](xref:fundamentals/servers/kestrel), se nazývá [model hostování mimo proces](#out-of-process-hosting-model).</span><span class="sxs-lookup"><span data-stu-id="6ee01-107">Forward web requests to a backend ASP.NET Core app running the [Kestrel server](xref:fundamentals/servers/kestrel), called the [out-of-process hosting model](#out-of-process-hosting-model).</span></span>

<span data-ttu-id="6ee01-108">Podporované verze systému Windows:</span><span class="sxs-lookup"><span data-stu-id="6ee01-108">Supported Windows versions:</span></span>

* <span data-ttu-id="6ee01-109">Windows 7 nebo novější</span><span class="sxs-lookup"><span data-stu-id="6ee01-109">Windows 7 or later</span></span>
* <span data-ttu-id="6ee01-110">Windows Server 2008 R2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="6ee01-110">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="6ee01-111">Při hostování v procesu používá modul implementaci vnitroprocesového serveru pro službu IIS nazvanou IIS HTTP Server (`IISHttpServer`).</span><span class="sxs-lookup"><span data-stu-id="6ee01-111">When hosting in-process, the module uses an in-process server implementation for IIS, called IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="6ee01-112">Při hostování mimo proces funguje modul pouze s Kestrel.</span><span class="sxs-lookup"><span data-stu-id="6ee01-112">When hosting out-of-process, the module only works with Kestrel.</span></span> <span data-ttu-id="6ee01-113">Modul nefunguje s [ovladačem HTTP. sys](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="6ee01-113">The module doesn't function with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

## <a name="hosting-models"></a><span data-ttu-id="6ee01-114">Modely hostingu</span><span class="sxs-lookup"><span data-stu-id="6ee01-114">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="6ee01-115">Model hostování v procesu</span><span class="sxs-lookup"><span data-stu-id="6ee01-115">In-process hosting model</span></span>

<span data-ttu-id="6ee01-116">ASP.NET Core aplikace jako výchozí pro model hostování v rámci procesu.</span><span class="sxs-lookup"><span data-stu-id="6ee01-116">ASP.NET Core apps default to the in-process hosting model.</span></span>

<span data-ttu-id="6ee01-117">Při hostování v procesu platí následující vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="6ee01-117">The following characteristics apply when hosting in-process:</span></span>

* <span data-ttu-id="6ee01-118">Místo serveru [Kestrel](xref:fundamentals/servers/kestrel) se`IISHttpServer`používá server HTTP služby IIS ().</span><span class="sxs-lookup"><span data-stu-id="6ee01-118">IIS HTTP Server (`IISHttpServer`) is used instead of [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span> <span data-ttu-id="6ee01-119">Pro vnitroprocesové [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) volání <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> :</span><span class="sxs-lookup"><span data-stu-id="6ee01-119">For in-process, [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> to:</span></span>

  * <span data-ttu-id="6ee01-120">Zaregistrujte `IISHttpServer`.</span><span class="sxs-lookup"><span data-stu-id="6ee01-120">Register the `IISHttpServer`.</span></span>
  * <span data-ttu-id="6ee01-121">Nakonfigurujte port a základní cestu, na kterých má Server naslouchat při spuštění za modulem ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="6ee01-121">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
  * <span data-ttu-id="6ee01-122">Nakonfigurujte hostitele tak, aby zachytával chyby při spuštění.</span><span class="sxs-lookup"><span data-stu-id="6ee01-122">Configure the host to capture startup errors.</span></span>

* <span data-ttu-id="6ee01-123">[RequestTimeout atribut](#attributes-of-the-aspnetcore-element) neplatí pro hostování v procesu.</span><span class="sxs-lookup"><span data-stu-id="6ee01-123">The [requestTimeout attribute](#attributes-of-the-aspnetcore-element) doesn't apply to in-process hosting.</span></span>

* <span data-ttu-id="6ee01-124">Sdílení fondem aplikací mezi aplikacemi se nepodporuje.</span><span class="sxs-lookup"><span data-stu-id="6ee01-124">Sharing an app pool among apps isn't supported.</span></span> <span data-ttu-id="6ee01-125">Použijte jeden fond aplikací na aplikaci.</span><span class="sxs-lookup"><span data-stu-id="6ee01-125">Use one app pool per app.</span></span>

* <span data-ttu-id="6ee01-126">Při použití [Webdeploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) nebo ručně uvedení [soubor app_offline.htm v nasazení](xref:host-and-deploy/iis/index#locked-deployment-files), aplikace nemusí vypnout okamžitě při otevření připojení.</span><span class="sxs-lookup"><span data-stu-id="6ee01-126">When using [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) or manually placing an [app_offline.htm file in the deployment](xref:host-and-deploy/iis/index#locked-deployment-files), the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="6ee01-127">Připojení soketu websocket bylo třeba může způsobit prodlevu při ukončení aplikace.</span><span class="sxs-lookup"><span data-stu-id="6ee01-127">For example, a websocket connection may delay app shut down.</span></span>

* <span data-ttu-id="6ee01-128">Architektura (bitové verze) (x64 nebo x86) nainstalovaný modul runtime a aplikace musí odpovídat architektuře fondu aplikací.</span><span class="sxs-lookup"><span data-stu-id="6ee01-128">The architecture (bitness) of the app and installed runtime (x64 or x86) must match the architecture of the app pool.</span></span>

* <span data-ttu-id="6ee01-129">Odpojení klienta jsou zjištěny.</span><span class="sxs-lookup"><span data-stu-id="6ee01-129">Client disconnects are detected.</span></span> <span data-ttu-id="6ee01-130">[HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) token zrušení se zrušila, když se klient odpojí.</span><span class="sxs-lookup"><span data-stu-id="6ee01-130">The [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) cancellation token is cancelled when the client disconnects.</span></span>

* <span data-ttu-id="6ee01-131">V ASP.NET Core 2.2.1 nebo dřívější <xref:System.IO.Directory.GetCurrentDirectory*> vrátí pracovní adresář procesu spuštěného službou IIS místo adresáře aplikace (například *C:\Windows\System32\inetsrv* pro *W3wp. exe*).</span><span class="sxs-lookup"><span data-stu-id="6ee01-131">In ASP.NET Core 2.2.1 or earlier, <xref:System.IO.Directory.GetCurrentDirectory*> returns the worker directory of the process started by IIS rather than the app's directory (for example, *C:\Windows\System32\inetsrv* for *w3wp.exe*).</span></span>

  <span data-ttu-id="6ee01-132">Ukázkový kód, který nastaví aktuální adresář aplikace, najdete v článku [CurrentDirectoryHelpers třídy](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/3.x/CurrentDirectoryHelpers.cs).</span><span class="sxs-lookup"><span data-stu-id="6ee01-132">For sample code that sets the app's current directory, see the [CurrentDirectoryHelpers class](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/3.x/CurrentDirectoryHelpers.cs).</span></span> <span data-ttu-id="6ee01-133">Volání `SetCurrentDirectory` metody.</span><span class="sxs-lookup"><span data-stu-id="6ee01-133">Call the `SetCurrentDirectory` method.</span></span> <span data-ttu-id="6ee01-134">Následující volání <xref:System.IO.Directory.GetCurrentDirectory*> poskytují adresáře aplikace.</span><span class="sxs-lookup"><span data-stu-id="6ee01-134">Subsequent calls to <xref:System.IO.Directory.GetCurrentDirectory*> provide the app's directory.</span></span>

* <span data-ttu-id="6ee01-135">Při hostování v procesu <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> není volána interně pro inicializaci uživatele.</span><span class="sxs-lookup"><span data-stu-id="6ee01-135">When hosting in-process, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span> <span data-ttu-id="6ee01-136"><xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> Proto implementace používaná k transformaci deklarací po každém ověření není ve výchozím nastavení aktivována.</span><span class="sxs-lookup"><span data-stu-id="6ee01-136">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="6ee01-137">Při transformaci deklarací s <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementací zavolejte <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> na přidat ověřovací služby:</span><span class="sxs-lookup"><span data-stu-id="6ee01-137">When transforming claims with an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation, call <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> to add authentication services:</span></span>

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

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="6ee01-138">Model hostování mimo proces</span><span class="sxs-lookup"><span data-stu-id="6ee01-138">Out-of-process hosting model</span></span>

<span data-ttu-id="6ee01-139">Chcete-li nakonfigurovat aplikaci pro hostování mimo procesy, nastavte hodnotu `<AspNetCoreHostingModel>` vlastnosti na `OutOfProcess` ( `InProcess`vnitroprocesové hostování v procesu je nastaveno na, což je výchozí hodnota):</span><span class="sxs-lookup"><span data-stu-id="6ee01-139">To configure an app for out-of-process hosting, set the value of the `<AspNetCoreHostingModel>` property to `OutOfProcess` (in-process hosting is set with `InProcess`, which is the default value):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="6ee01-140">[](xref:fundamentals/servers/kestrel) Místo http serveru IIS (`IISHttpServer`) se používá server Kestrel.</span><span class="sxs-lookup"><span data-stu-id="6ee01-140">[Kestrel](xref:fundamentals/servers/kestrel) server is used instead of IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="6ee01-141">Pro mimoprocesové [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) volání <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> do:</span><span class="sxs-lookup"><span data-stu-id="6ee01-141">For out-of-process, [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> to:</span></span>

* <span data-ttu-id="6ee01-142">Nakonfigurujte port a základní cestu, na kterých má Server naslouchat při spuštění za modulem ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="6ee01-142">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
* <span data-ttu-id="6ee01-143">Nakonfigurujte hostitele tak, aby zachytával chyby při spuštění.</span><span class="sxs-lookup"><span data-stu-id="6ee01-143">Configure the host to capture startup errors.</span></span>

### <a name="hosting-model-changes"></a><span data-ttu-id="6ee01-144">Hostování změny modelu</span><span class="sxs-lookup"><span data-stu-id="6ee01-144">Hosting model changes</span></span>

<span data-ttu-id="6ee01-145">Pokud `hostingModel` nastavení se změnilo v *web.config* souboru (podrobně [konfigurace pomocí souboru web.config](#configuration-with-webconfig) části), modul pro službu IIS recykluje pracovní proces.</span><span class="sxs-lookup"><span data-stu-id="6ee01-145">If the `hostingModel` setting is changed in the *web.config* file (explained in the [Configuration with web.config](#configuration-with-webconfig) section), the module recycles the worker process for IIS.</span></span>

<span data-ttu-id="6ee01-146">Pro službu IIS Express modul nebude recyklovat pracovní proces, ale místo toho aktivuje řádné vypnutí aktuální proces IIS Express.</span><span class="sxs-lookup"><span data-stu-id="6ee01-146">For IIS Express, the module doesn't recycle the worker process but instead triggers a graceful shutdown of the current IIS Express process.</span></span> <span data-ttu-id="6ee01-147">Další požadavek na aplikaci vytvoří nový proces IIS Express.</span><span class="sxs-lookup"><span data-stu-id="6ee01-147">The next request to the app spawns a new IIS Express process.</span></span>

### <a name="process-name"></a><span data-ttu-id="6ee01-148">Název procesu</span><span class="sxs-lookup"><span data-stu-id="6ee01-148">Process name</span></span>

<span data-ttu-id="6ee01-149">`Process.GetCurrentProcess().ProcessName` sestavy `w3wp` / `iisexpress` (v procesu) nebo `dotnet` (out-of-process).</span><span class="sxs-lookup"><span data-stu-id="6ee01-149">`Process.GetCurrentProcess().ProcessName` reports `w3wp`/`iisexpress` (in-process) or `dotnet` (out-of-process).</span></span>

<span data-ttu-id="6ee01-150">Mnoho nativních modulů, jako je ověřování systému Windows, zůstane aktivní.</span><span class="sxs-lookup"><span data-stu-id="6ee01-150">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="6ee01-151">Další informace o aktivních modulech služby IIS pomocí modulu ASP.NET Core najdete v <xref:host-and-deploy/iis/modules>tématu.</span><span class="sxs-lookup"><span data-stu-id="6ee01-151">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="6ee01-152">Modul ASP.NET Core může také:</span><span class="sxs-lookup"><span data-stu-id="6ee01-152">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="6ee01-153">Nastavte proměnné prostředí pro pracovní proces.</span><span class="sxs-lookup"><span data-stu-id="6ee01-153">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="6ee01-154">Protokoluje výstup stdout do úložiště souborů pro řešení problémů při spuštění.</span><span class="sxs-lookup"><span data-stu-id="6ee01-154">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="6ee01-155">Předejte tokeny ověřování systému Windows.</span><span class="sxs-lookup"><span data-stu-id="6ee01-155">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="6ee01-156">Jak nainstalovat a používat modul ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6ee01-156">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="6ee01-157">Pokyny k instalaci modulu ASP.NET Core najdete v tématu [instalace hostující sady .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span><span class="sxs-lookup"><span data-stu-id="6ee01-157">For instructions on how to install the ASP.NET Core Module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="6ee01-158">Konfigurace pomocí souboru web.config</span><span class="sxs-lookup"><span data-stu-id="6ee01-158">Configuration with web.config</span></span>

<span data-ttu-id="6ee01-159">Modul ASP.NET Core, nastavena `aspNetCore` část `system.webServer` uzlu na webu *web.config* souboru.</span><span class="sxs-lookup"><span data-stu-id="6ee01-159">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="6ee01-160">Následující *web.config* soubor je publikován pro [nasazení závisí na architektuře](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) a konfiguruje modul ASP.NET Core pro zpracování požadavků lokality:</span><span class="sxs-lookup"><span data-stu-id="6ee01-160">The following *web.config* file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

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

<span data-ttu-id="6ee01-161">Následující *web.config* je publikována pro [samostatná nasazení](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span><span class="sxs-lookup"><span data-stu-id="6ee01-161">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

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

<span data-ttu-id="6ee01-162"><xref:System.Configuration.SectionInformation.InheritInChildApplications*> Je nastavena na `false` k označení, že nastavení zadané v rámci [ \<umístění >](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) element nedědí od neznámých aplikací, které jsou umístěny v podadresáři aplikace.</span><span class="sxs-lookup"><span data-stu-id="6ee01-162">The <xref:System.Configuration.SectionInformation.InheritInChildApplications*> property is set to `false` to indicate that the settings specified within the [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) element aren't inherited by apps that reside in a subdirectory of the app.</span></span>

<span data-ttu-id="6ee01-163">Když je aplikace nasazená na [služby Azure App Service](https://azure.microsoft.com/services/app-service/), `stdoutLogFile` je nastavena cesta `\\?\%home%\LogFiles\stdout`.</span><span class="sxs-lookup"><span data-stu-id="6ee01-163">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="6ee01-164">Protokoly stdout a uloží ji *LogFiles* složce, která je na místě automaticky vytvořené službou.</span><span class="sxs-lookup"><span data-stu-id="6ee01-164">The path saves stdout logs to the *LogFiles* folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="6ee01-165">Informace o konfiguraci dílčí aplikace služby IIS, naleznete v tématu <xref:host-and-deploy/iis/index#sub-applications>.</span><span class="sxs-lookup"><span data-stu-id="6ee01-165">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="6ee01-166">Atributy elementu aspNetCore</span><span class="sxs-lookup"><span data-stu-id="6ee01-166">Attributes of the aspNetCore element</span></span>

| <span data-ttu-id="6ee01-167">Atribut</span><span class="sxs-lookup"><span data-stu-id="6ee01-167">Attribute</span></span> | <span data-ttu-id="6ee01-168">Popis</span><span class="sxs-lookup"><span data-stu-id="6ee01-168">Description</span></span> | <span data-ttu-id="6ee01-169">Výchozí</span><span class="sxs-lookup"><span data-stu-id="6ee01-169">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="6ee01-170">Volitelný atribut řetězce.</span><span class="sxs-lookup"><span data-stu-id="6ee01-170">Optional string attribute.</span></span></p><p><span data-ttu-id="6ee01-171">Argumenty pro spustitelný soubor určený v **processPath**.</span><span class="sxs-lookup"><span data-stu-id="6ee01-171">Arguments to the executable specified in **processPath**.</span></span></p> | |
| `disableStartUpErrorPage` | <p><span data-ttu-id="6ee01-172">Volitelný logický atribut.</span><span class="sxs-lookup"><span data-stu-id="6ee01-172">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="6ee01-173">Při hodnotě true **502.5 – selhání procesu** Potlačené stránky a znakovou stránku 502 stav nakonfigurované v *web.config* má přednost před.</span><span class="sxs-lookup"><span data-stu-id="6ee01-173">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="6ee01-174">Volitelný logický atribut.</span><span class="sxs-lookup"><span data-stu-id="6ee01-174">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="6ee01-175">Při hodnotě true se token předá podřízený proces naslouchání na ASPNETCORE_PORT % jako záhlaví "MS-ASPNETCORE-WINAUTHTOKEN" každý požadavek.</span><span class="sxs-lookup"><span data-stu-id="6ee01-175">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="6ee01-176">Je odpovědností tento proces pro volání CloseHandle na tento token každý požadavek.</span><span class="sxs-lookup"><span data-stu-id="6ee01-176">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `hostingModel` | <p><span data-ttu-id="6ee01-177">Volitelný atribut řetězce.</span><span class="sxs-lookup"><span data-stu-id="6ee01-177">Optional string attribute.</span></span></p><p><span data-ttu-id="6ee01-178">Určuje model hostování jako v procesu (`InProcess`) nebo out-of-process (`OutOfProcess`).</span><span class="sxs-lookup"><span data-stu-id="6ee01-178">Specifies the hosting model as in-process (`InProcess`) or out-of-process (`OutOfProcess`).</span></span></p> | `InProcess` |
| `processesPerApplication` | <p><span data-ttu-id="6ee01-179">Volitelný celočíselný atribut.</span><span class="sxs-lookup"><span data-stu-id="6ee01-179">Optional integer attribute.</span></span></p><p><span data-ttu-id="6ee01-180">Určuje počet instancí procesu zadaný v **processPath** nastavení, které lze se nespředený nahoru na app.</span><span class="sxs-lookup"><span data-stu-id="6ee01-180">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="6ee01-181">&dagger;Hostování v procesu, je omezena na hodnotu `1`.</span><span class="sxs-lookup"><span data-stu-id="6ee01-181">&dagger;For in-process hosting, the value is limited to `1`.</span></span></p><p><span data-ttu-id="6ee01-182">Nastavení `processesPerApplication` se nedoporučuje.</span><span class="sxs-lookup"><span data-stu-id="6ee01-182">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="6ee01-183">Tento atribut bude v budoucí verzi odebrán.</span><span class="sxs-lookup"><span data-stu-id="6ee01-183">This attribute will be removed in a future release.</span></span></p> | <span data-ttu-id="6ee01-184">Výchozí hodnota: `1`</span><span class="sxs-lookup"><span data-stu-id="6ee01-184">Default: `1`</span></span><br><span data-ttu-id="6ee01-185">Min: `1`</span><span class="sxs-lookup"><span data-stu-id="6ee01-185">Min: `1`</span></span><br><span data-ttu-id="6ee01-186">Max: `100`&dagger;</span><span class="sxs-lookup"><span data-stu-id="6ee01-186">Max: `100`&dagger;</span></span> |
| `processPath` | <p><span data-ttu-id="6ee01-187">Požadovaný atribut typu string.</span><span class="sxs-lookup"><span data-stu-id="6ee01-187">Required string attribute.</span></span></p><p><span data-ttu-id="6ee01-188">Cesta ke spustitelnému souboru, který spustí nějaký proces naslouchání požadavků protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="6ee01-188">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="6ee01-189">Jsou podporovány relativní cesty.</span><span class="sxs-lookup"><span data-stu-id="6ee01-189">Relative paths are supported.</span></span> <span data-ttu-id="6ee01-190">Pokud cestu začíná `.`, cesta se považuje za kořeni webu.</span><span class="sxs-lookup"><span data-stu-id="6ee01-190">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="6ee01-191">Volitelný celočíselný atribut.</span><span class="sxs-lookup"><span data-stu-id="6ee01-191">Optional integer attribute.</span></span></p><p><span data-ttu-id="6ee01-192">Určuje, kolikrát podle procesu **processPath** může při selhání za minutu.</span><span class="sxs-lookup"><span data-stu-id="6ee01-192">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="6ee01-193">Pokud je tento limit překročen, modulu zastaví spuštění procesu pro zbytek minuty.</span><span class="sxs-lookup"><span data-stu-id="6ee01-193">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p><p><span data-ttu-id="6ee01-194">Není podporováno s hostitelem v procesu.</span><span class="sxs-lookup"><span data-stu-id="6ee01-194">Not supported with in-process hosting.</span></span></p> | <span data-ttu-id="6ee01-195">Výchozí hodnota: `10`</span><span class="sxs-lookup"><span data-stu-id="6ee01-195">Default: `10`</span></span><br><span data-ttu-id="6ee01-196">Min: `0`</span><span class="sxs-lookup"><span data-stu-id="6ee01-196">Min: `0`</span></span><br><span data-ttu-id="6ee01-197">Max: `100`</span><span class="sxs-lookup"><span data-stu-id="6ee01-197">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="6ee01-198">Atribut volitelný časový interval.</span><span class="sxs-lookup"><span data-stu-id="6ee01-198">Optional timespan attribute.</span></span></p><p><span data-ttu-id="6ee01-199">Určuje dobu, pro kterou modul ASP.NET Core čeká na odpověď z procesu naslouchání na ASPNETCORE_PORT %.</span><span class="sxs-lookup"><span data-stu-id="6ee01-199">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="6ee01-200">Ve verzích modul ASP.NET Core dodávané s verzí technologie ASP.NET Core 2.1 nebo novější `requestTimeout` je zadán v hodiny, minuty a sekundy.</span><span class="sxs-lookup"><span data-stu-id="6ee01-200">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p><p><span data-ttu-id="6ee01-201">Neplatí pro hostování v procesu.</span><span class="sxs-lookup"><span data-stu-id="6ee01-201">Doesn't apply to in-process hosting.</span></span> <span data-ttu-id="6ee01-202">Hostování v procesu, čeká modul app ke zpracování požadavku.</span><span class="sxs-lookup"><span data-stu-id="6ee01-202">For in-process hosting, the module waits for the app to process the request.</span></span></p><p><span data-ttu-id="6ee01-203">Platné hodnoty segmentů minut a sekund řetězce jsou v rozsahu 0-59.</span><span class="sxs-lookup"><span data-stu-id="6ee01-203">Valid values for minutes and seconds segments of the string are in the range 0-59.</span></span> <span data-ttu-id="6ee01-204">Při použití hodnoty **60** v hodnotě minut nebo sekund dojde k chybě systému *500 – interní chyba serveru*.</span><span class="sxs-lookup"><span data-stu-id="6ee01-204">Use of **60** in the value for minutes or seconds results in a *500 - Internal Server Error*.</span></span></p> | <span data-ttu-id="6ee01-205">Výchozí hodnota: `00:02:00`</span><span class="sxs-lookup"><span data-stu-id="6ee01-205">Default: `00:02:00`</span></span><br><span data-ttu-id="6ee01-206">Min: `00:00:00`</span><span class="sxs-lookup"><span data-stu-id="6ee01-206">Min: `00:00:00`</span></span><br><span data-ttu-id="6ee01-207">Max: `360:00:00`</span><span class="sxs-lookup"><span data-stu-id="6ee01-207">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="6ee01-208">Volitelný celočíselný atribut.</span><span class="sxs-lookup"><span data-stu-id="6ee01-208">Optional integer attribute.</span></span></p><p><span data-ttu-id="6ee01-209">Doba trvání v sekundách, které modul čeká na spustitelný soubor na řádné vypnutí při *app_offline.htm* je detekován soubor.</span><span class="sxs-lookup"><span data-stu-id="6ee01-209">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="6ee01-210">Výchozí hodnota: `10`</span><span class="sxs-lookup"><span data-stu-id="6ee01-210">Default: `10`</span></span><br><span data-ttu-id="6ee01-211">Min: `0`</span><span class="sxs-lookup"><span data-stu-id="6ee01-211">Min: `0`</span></span><br><span data-ttu-id="6ee01-212">Max: `600`</span><span class="sxs-lookup"><span data-stu-id="6ee01-212">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="6ee01-213">Volitelný celočíselný atribut.</span><span class="sxs-lookup"><span data-stu-id="6ee01-213">Optional integer attribute.</span></span></p><p><span data-ttu-id="6ee01-214">Doba trvání v sekundách, které modul čeká na spustitelný soubor ke spuštění procesu naslouchání na portu.</span><span class="sxs-lookup"><span data-stu-id="6ee01-214">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="6ee01-215">Pokud je překročen časový limit, modul ukončí proces.</span><span class="sxs-lookup"><span data-stu-id="6ee01-215">If this time limit is exceeded, the module kills the process.</span></span> <span data-ttu-id="6ee01-216">Modul se pokusí znovu spustit proces, když ji dostane novou žádost a pokusit se restartovat proces na dalších příchozích požadavků, pokud se aplikaci nepodaří spustit **rapidFailsPerMinute** málokdy za posledních minuta se zajištěním provozu.</span><span class="sxs-lookup"><span data-stu-id="6ee01-216">The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="6ee01-217">Hodnota 0 (nula) je **ne** za neomezený časový limit.</span><span class="sxs-lookup"><span data-stu-id="6ee01-217">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="6ee01-218">Výchozí hodnota: `120`</span><span class="sxs-lookup"><span data-stu-id="6ee01-218">Default: `120`</span></span><br><span data-ttu-id="6ee01-219">Min: `0`</span><span class="sxs-lookup"><span data-stu-id="6ee01-219">Min: `0`</span></span><br><span data-ttu-id="6ee01-220">Max: `3600`</span><span class="sxs-lookup"><span data-stu-id="6ee01-220">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="6ee01-221">Volitelný logický atribut.</span><span class="sxs-lookup"><span data-stu-id="6ee01-221">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="6ee01-222">Při hodnotě true se **stdout** a **stderr** pro proces určený v **processPath** se přesměrují do souboru zadaného v **stdoutLogFile**.</span><span class="sxs-lookup"><span data-stu-id="6ee01-222">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="6ee01-223">Volitelný atribut řetězce.</span><span class="sxs-lookup"><span data-stu-id="6ee01-223">Optional string attribute.</span></span></p><p><span data-ttu-id="6ee01-224">Určuje relativní nebo absolutní cestu, pro kterou **stdout** a **stderr** z procesu podle **processPath** přihlášeni.</span><span class="sxs-lookup"><span data-stu-id="6ee01-224">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="6ee01-225">Relativní cesty jsou relativní vzhledem k kořen webu.</span><span class="sxs-lookup"><span data-stu-id="6ee01-225">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="6ee01-226">Libovolnou cestu od `.` jsou relativní vzhledem k webu kořenové a všechny ostatní cesty jsou považovány za absolutní cesty.</span><span class="sxs-lookup"><span data-stu-id="6ee01-226">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="6ee01-227">Při vytvoření souboru protokolu se v modulu vytvoří všechny složky, které jsou v cestě zadané.</span><span class="sxs-lookup"><span data-stu-id="6ee01-227">Any folders provided in the path are created by the module when the log file is created.</span></span> <span data-ttu-id="6ee01-228">Pomocí oddělovače podtržítko, časové razítko, ID procesu a přípona souboru ( *.log*) jsou přidány na poslední segment **stdoutLogFile** cestu.</span><span class="sxs-lookup"><span data-stu-id="6ee01-228">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="6ee01-229">Pokud `.\logs\stdout` je zadaný jako hodnota, je uložen protokolu příklad stdout jako *stdout_20180205194132_1934.log* v *protokoly* složky při uložení 5 2. 2018 v 19:41:32 s ID procesu sady 1934.</span><span class="sxs-lookup"><span data-stu-id="6ee01-229">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

### <a name="setting-environment-variables"></a><span data-ttu-id="6ee01-230">Nastavení proměnných prostředí</span><span class="sxs-lookup"><span data-stu-id="6ee01-230">Setting environment variables</span></span>

<span data-ttu-id="6ee01-231">Proměnné prostředí se dá nastavit pro proces v `processPath` atribut.</span><span class="sxs-lookup"><span data-stu-id="6ee01-231">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="6ee01-232">Zadat proměnné prostředí s `<environmentVariable>` podřízený prvek `<environmentVariables>` prvek kolekce.</span><span class="sxs-lookup"><span data-stu-id="6ee01-232">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span> <span data-ttu-id="6ee01-233">Proměnné prostředí nastavené v této části přednost systémové proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="6ee01-233">Environment variables set in this section take precedence over system environment variables.</span></span>

<span data-ttu-id="6ee01-234">Následující příklad nastaví dvou proměnných prostředí.</span><span class="sxs-lookup"><span data-stu-id="6ee01-234">The following example sets two environment variables.</span></span> <span data-ttu-id="6ee01-235">`ASPNETCORE_ENVIRONMENT` nakonfiguruje prostředí aplikace tak, aby `Development`.</span><span class="sxs-lookup"><span data-stu-id="6ee01-235">`ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="6ee01-236">Vývojáři mohou dočasně nastaví tuto hodnotu *web.config* souboru, aby bylo možné vynutit [stránku výjimek pro vývojáře](xref:fundamentals/error-handling) načíst při ladění aplikace výjimky.</span><span class="sxs-lookup"><span data-stu-id="6ee01-236">A developer may temporarily set this value in the *web.config* file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="6ee01-237">`CONFIG_DIR` je příkladem proměnné prostředí, kam má vývojář zapisovat kód, který čte hodnoty při spuštění tvoří cestu pro načtení konfiguračního souboru aplikace.</span><span class="sxs-lookup"><span data-stu-id="6ee01-237">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

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
> <span data-ttu-id="6ee01-238">Alternativou k nastavení prostředí přímo v souboru *Web. config* je zahrnutí `<EnvironmentName>` vlastnosti do profilu publikování ( *. pubxml*) nebo souboru projektu.</span><span class="sxs-lookup"><span data-stu-id="6ee01-238">An alternative to setting the environment directly in *web.config* is to include the `<EnvironmentName>` property in the publish profile (*.pubxml*) or project file.</span></span> <span data-ttu-id="6ee01-239">Tento přístup nastaví prostředí v *souboru Web. config* při publikování projektu:</span><span class="sxs-lookup"><span data-stu-id="6ee01-239">This approach sets the environment in *web.config* when the project is published:</span></span>
>
> ```xml
> <PropertyGroup>
>   <EnvironmentName>Development</EnvironmentName>
> </PropertyGroup>
> ```

> [!WARNING]
> <span data-ttu-id="6ee01-240">Nastavit pouze `ASPNETCORE_ENVIRONMENT` proměnnou prostředí, aby `Development` na přípravy a testování serverů, které nejsou dostupné k nedůvěryhodným sítím, jako je Internet.</span><span class="sxs-lookup"><span data-stu-id="6ee01-240">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## <a name="app_offlinehtm"></a><span data-ttu-id="6ee01-241">app_offline.htm</span><span class="sxs-lookup"><span data-stu-id="6ee01-241">app_offline.htm</span></span>

<span data-ttu-id="6ee01-242">Pokud soubor s názvem *app_offline.htm* je zjištěna v kořenovém adresáři aplikace, že modul ASP.NET Core se pokusí řádné vypnutí aplikace a zastavit zpracování příchozích požadavků.</span><span class="sxs-lookup"><span data-stu-id="6ee01-242">If a file with the name *app_offline.htm* is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="6ee01-243">Pokud aplikace pořád běží po dobu v sekundách podle `shutdownTimeLimit`, že modul ASP.NET Core ukončuje spuštěnému procesu.</span><span class="sxs-lookup"><span data-stu-id="6ee01-243">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="6ee01-244">Zatímco *app_offline.htm* soubor je k dispozici, že modul ASP.NET Core reaguje na požadavky odesílá zpět obsah *app_offline.htm* souboru.</span><span class="sxs-lookup"><span data-stu-id="6ee01-244">While the *app_offline.htm* file is present, the ASP.NET Core Module responds to requests by sending back the contents of the *app_offline.htm* file.</span></span> <span data-ttu-id="6ee01-245">Když *app_offline.htm* soubor bude odstraněn, příští žádosti o spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="6ee01-245">When the *app_offline.htm* file is removed, the next request starts the app.</span></span>

<span data-ttu-id="6ee01-246">Pokud používáte model hostingu mimo proces, aplikace nemusí vypnout okamžitě při otevření připojení.</span><span class="sxs-lookup"><span data-stu-id="6ee01-246">When using the out-of-process hosting model, the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="6ee01-247">Připojení soketu websocket bylo třeba může způsobit prodlevu při ukončení aplikace.</span><span class="sxs-lookup"><span data-stu-id="6ee01-247">For example, a websocket connection may delay app shut down.</span></span>

## <a name="start-up-error-page"></a><span data-ttu-id="6ee01-248">Spuštění chybovou stránku</span><span class="sxs-lookup"><span data-stu-id="6ee01-248">Start-up error page</span></span>

<span data-ttu-id="6ee01-249">Hostování v procesu a mimo proces vytvoření vlastní chybové stránky, když selžou a spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="6ee01-249">Both in-process and out-of-process hosting produce custom error pages when they fail to start the app.</span></span>

<span data-ttu-id="6ee01-250">Pokud se nepodaří najít buď nebo na více instancí procesu žádosti o obslužnou rutinu, že modul ASP.NET Core *500.0 – Chyba načtení obslužné rutiny v procesu/Out – proces* se zobrazí stav znakovou stránku.</span><span class="sxs-lookup"><span data-stu-id="6ee01-250">If the ASP.NET Core Module fails to find either the in-process or out-of-process request handler, a *500.0 - In-Process/Out-Of-Process Handler Load Failure* status code page appears.</span></span>

<span data-ttu-id="6ee01-251">Pro hostování v procesu, pokud se nepodaří spustit aplikaci, že modul ASP.NET Core *500.30 - Start selhání* se zobrazí stav znakovou stránku.</span><span class="sxs-lookup"><span data-stu-id="6ee01-251">For in-process hosting if the ASP.NET Core Module fails to start the app, a *500.30 - Start Failure* status code page appears.</span></span>

<span data-ttu-id="6ee01-252">Pro hostování mimo proces, pokud se nespustí back-endový proces nebo back-endový proces spustí ale nebude moci poslouchat na konfigurovaném portu, že modul ASP.NET Core *502.5 – selhání procesu* se zobrazí stav znakovou stránku.</span><span class="sxs-lookup"><span data-stu-id="6ee01-252">For out-of-process hosting if the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span>

<span data-ttu-id="6ee01-253">Chcete-li potlačit tuto stránku a vrátit se na výchozí služby IIS 5xx stav znakovou stránku, použijte `disableStartUpErrorPage` atribut.</span><span class="sxs-lookup"><span data-stu-id="6ee01-253">To suppress this page and revert to the default IIS 5xx status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="6ee01-254">Další informace o konfiguraci vlastních chybových zpráv, najdete v části [chyby protokolu HTTP \<httpErrors >](/iis/configuration/system.webServer/httpErrors/).</span><span class="sxs-lookup"><span data-stu-id="6ee01-254">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

## <a name="log-creation-and-redirection"></a><span data-ttu-id="6ee01-255">Vytvoření protokolu a přesměrování</span><span class="sxs-lookup"><span data-stu-id="6ee01-255">Log creation and redirection</span></span>

<span data-ttu-id="6ee01-256">Modul ASP.NET Core přesměruje výstup stdout a stderr console na disk, pokud `stdoutLogEnabled` a `stdoutLogFile` atributy `aspNetCore` element jsou nastavené.</span><span class="sxs-lookup"><span data-stu-id="6ee01-256">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="6ee01-257">Při vytvoření souboru protokolu `stdoutLogFile` jsou všechny složky v této cestě vytvářeny modulem.</span><span class="sxs-lookup"><span data-stu-id="6ee01-257">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="6ee01-258">Fond aplikací musí mít oprávnění k zápisu do umístění, ve kterém jsou zapsány protokoly (použijte `IIS AppPool\<app_pool_name>` poskytnout oprávnění k zápisu).</span><span class="sxs-lookup"><span data-stu-id="6ee01-258">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="6ee01-259">Protokoly nejsou střídán, pokud dojde k recyklování procesů/restartování.</span><span class="sxs-lookup"><span data-stu-id="6ee01-259">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="6ee01-260">Je odpovědností hostitel k omezení místa na disku, které využijete v protokolech.</span><span class="sxs-lookup"><span data-stu-id="6ee01-260">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="6ee01-261">Použití protokolu stdout se doporučuje jenom pro řešení potíží při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="6ee01-261">Using the stdout log is only recommended for troubleshooting app startup issues.</span></span> <span data-ttu-id="6ee01-262">Nepoužívejte stdout protokolu pro účely protokolování obecné aplikace.</span><span class="sxs-lookup"><span data-stu-id="6ee01-262">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="6ee01-263">Pro rutiny protokolování v aplikaci ASP.NET Core, použijte protokolování knihovnu, která omezuje velikost souboru protokolu a otočí protokoly.</span><span class="sxs-lookup"><span data-stu-id="6ee01-263">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="6ee01-264">Další informace najdete v tématu [zprostředkovatele přihlášení třetí strany](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="6ee01-264">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="6ee01-265">Časové razítko a soubor rozšíření jsou přidány automaticky při vytvoření souboru protokolu.</span><span class="sxs-lookup"><span data-stu-id="6ee01-265">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="6ee01-266">Název souboru protokolu se skládá připojením časové razítko, ID procesu a přípona souboru ( *.log*) na poslední segment `stdoutLogFile` cestu (obvykle *stdout*) oddělené podtržítka.</span><span class="sxs-lookup"><span data-stu-id="6ee01-266">The log file name is composed by appending the timestamp, process ID, and file extension (*.log*) to the last segment of the `stdoutLogFile` path (typically *stdout*) delimited by underscores.</span></span> <span data-ttu-id="6ee01-267">Pokud `stdoutLogFile` cesta končí *stdout*, má název souboru protokolu pro aplikaci s PID 1934 vytvořili 5 2. 2018 v 19:42:32 *stdout_20180205194132_1934.log*.</span><span class="sxs-lookup"><span data-stu-id="6ee01-267">If the `stdoutLogFile` path ends with *stdout*, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name *stdout_20180205194132_1934.log*.</span></span>

<span data-ttu-id="6ee01-268">Pokud `stdoutLogEnabled` má hodnotu false, chyby, ke kterým dochází při spuštění aplikace se zachytí a do protokolu událostí, protože ho až 30 KB.</span><span class="sxs-lookup"><span data-stu-id="6ee01-268">If `stdoutLogEnabled` is false, errors that occur on app startup are captured and emitted to the event log up to 30 KB.</span></span> <span data-ttu-id="6ee01-269">Po spuštění se zahodí všechny další protokoly.</span><span class="sxs-lookup"><span data-stu-id="6ee01-269">After startup, all additional logs are discarded.</span></span>

<span data-ttu-id="6ee01-270">Následující ukázka `aspNetCore` element konfiguruje stdout protokolování pro aplikace hostované ve službě Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="6ee01-270">The following sample `aspNetCore` element configures stdout logging for an app hosted in Azure App Service.</span></span> <span data-ttu-id="6ee01-271">Místní cesta nebo cesta ke sdílené položce je přijatelné pro místní přihlášení.</span><span class="sxs-lookup"><span data-stu-id="6ee01-271">A local path or network share path is acceptable for local logging.</span></span> <span data-ttu-id="6ee01-272">Ověřte, jestli uživatel identita fondu aplikací má oprávnění k zápisu do zadaná výstupní cesta.</span><span class="sxs-lookup"><span data-stu-id="6ee01-272">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="InProcess">
</aspNetCore>
```

## <a name="enhanced-diagnostic-logs"></a><span data-ttu-id="6ee01-273">Rozšířené diagnostické protokoly</span><span class="sxs-lookup"><span data-stu-id="6ee01-273">Enhanced diagnostic logs</span></span>

<span data-ttu-id="6ee01-274">Modul ASP.NET Core lze nakonfigurovat tak, aby poskytoval rozšířené diagnostické protokoly.</span><span class="sxs-lookup"><span data-stu-id="6ee01-274">The ASP.NET Core Module is configurable to provide enhanced diagnostics logs.</span></span> <span data-ttu-id="6ee01-275">Přidat `<handlerSettings>` elementu `<aspNetCore>` prvek *web.config*. Nastavení `debugLevel` k `TRACE` zpřístupňuje větší věrnost diagnostické informace:</span><span class="sxs-lookup"><span data-stu-id="6ee01-275">Add the `<handlerSettings>` element to the `<aspNetCore>` element in *web.config*. Setting the `debugLevel` to `TRACE` exposes a higher fidelity of diagnostic information:</span></span>

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

<span data-ttu-id="6ee01-276">Všechny složky v cestě (*protokoly* v předchozím příkladu) se vytvoří modulem, když se vytvoří soubor protokolu.</span><span class="sxs-lookup"><span data-stu-id="6ee01-276">Any folders in the path (*logs* in the preceding example) are created by the module when the log file is created.</span></span> <span data-ttu-id="6ee01-277">Fond aplikací musí mít oprávnění k zápisu do umístění, ve kterém jsou zapsány protokoly (použijte `IIS AppPool\<app_pool_name>` poskytnout oprávnění k zápisu).</span><span class="sxs-lookup"><span data-stu-id="6ee01-277">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="6ee01-278">Ladit úrovně (`debugLevel`) hodnoty může obsahovat úroveň a umístění.</span><span class="sxs-lookup"><span data-stu-id="6ee01-278">Debug level (`debugLevel`) values can include both the level and the location.</span></span>

<span data-ttu-id="6ee01-279">Úrovně (v pořadí od nejméně na nejpodrobnější):</span><span class="sxs-lookup"><span data-stu-id="6ee01-279">Levels (in order from least to most verbose):</span></span>

* <span data-ttu-id="6ee01-280">CHYBA</span><span class="sxs-lookup"><span data-stu-id="6ee01-280">ERROR</span></span>
* <span data-ttu-id="6ee01-281">UPOZORNĚNÍ</span><span class="sxs-lookup"><span data-stu-id="6ee01-281">WARNING</span></span>
* <span data-ttu-id="6ee01-282">INFORMACE O</span><span class="sxs-lookup"><span data-stu-id="6ee01-282">INFO</span></span>
* <span data-ttu-id="6ee01-283">TRACE</span><span class="sxs-lookup"><span data-stu-id="6ee01-283">TRACE</span></span>

<span data-ttu-id="6ee01-284">Umístění (umístění více jsou povoleny):</span><span class="sxs-lookup"><span data-stu-id="6ee01-284">Locations (multiple locations are permitted):</span></span>

* <span data-ttu-id="6ee01-285">KONZOLY</span><span class="sxs-lookup"><span data-stu-id="6ee01-285">CONSOLE</span></span>
* <span data-ttu-id="6ee01-286">PROTOKOL UDÁLOSTÍ</span><span class="sxs-lookup"><span data-stu-id="6ee01-286">EVENTLOG</span></span>
* <span data-ttu-id="6ee01-287">SOUBOR</span><span class="sxs-lookup"><span data-stu-id="6ee01-287">FILE</span></span>

<span data-ttu-id="6ee01-288">Obslužná rutina nastavení se dá zadat i prostřednictvím proměnné prostředí:</span><span class="sxs-lookup"><span data-stu-id="6ee01-288">The handler settings can also be provided via environment variables:</span></span>

* <span data-ttu-id="6ee01-289">`ASPNETCORE_MODULE_DEBUG_FILE` &ndash; Cesta k souboru protokolu ladění.</span><span class="sxs-lookup"><span data-stu-id="6ee01-289">`ASPNETCORE_MODULE_DEBUG_FILE` &ndash; Path to the debug log file.</span></span> <span data-ttu-id="6ee01-290">(Výchozí: *aspnetcore-debug.log*)</span><span class="sxs-lookup"><span data-stu-id="6ee01-290">(Default: *aspnetcore-debug.log*)</span></span>
* <span data-ttu-id="6ee01-291">`ASPNETCORE_MODULE_DEBUG` &ndash; Ladit nastavení úrovně.</span><span class="sxs-lookup"><span data-stu-id="6ee01-291">`ASPNETCORE_MODULE_DEBUG` &ndash; Debug level setting.</span></span>

> [!WARNING]
> <span data-ttu-id="6ee01-292">Proveďte **není** protokolování ladění povoleno v nasazení pro déle, než se požaduje chcete vyřešit nějaký problém.</span><span class="sxs-lookup"><span data-stu-id="6ee01-292">Do **not** leave debug logging enabled in the deployment for longer than required to troubleshoot an issue.</span></span> <span data-ttu-id="6ee01-293">Velikost souboru protokolu není omezený.</span><span class="sxs-lookup"><span data-stu-id="6ee01-293">The size of the log isn't limited.</span></span> <span data-ttu-id="6ee01-294">Opuštění protokol ladění povoleno může vyčerpat dostupné místo na disku a chybách u aplikací na serveru nebo služby app service.</span><span class="sxs-lookup"><span data-stu-id="6ee01-294">Leaving the debug log enabled can exhaust the available disk space and crash the server or app service.</span></span>

<span data-ttu-id="6ee01-295">Naleznete v tématu [konfigurace pomocí souboru web.config](#configuration-with-webconfig) příklad `aspNetCore` prvek *web.config* souboru.</span><span class="sxs-lookup"><span data-stu-id="6ee01-295">See [Configuration with web.config](#configuration-with-webconfig) for an example of the `aspNetCore` element in the *web.config* file.</span></span>

## <a name="modify-the-stack-size"></a><span data-ttu-id="6ee01-296">Úprava velikosti zásobníku</span><span class="sxs-lookup"><span data-stu-id="6ee01-296">Modify the stack size</span></span>

<span data-ttu-id="6ee01-297">*Platí pouze při použití modelu hostování v rámci procesu.*</span><span class="sxs-lookup"><span data-stu-id="6ee01-297">*Only applies when using the in-process hosting model.*</span></span>

<span data-ttu-id="6ee01-298">Nastavte velikost spravovaného zásobníku pomocí `stackSize` nastavení v bajtech.</span><span class="sxs-lookup"><span data-stu-id="6ee01-298">Configure the managed stack size using the `stackSize` setting in bytes.</span></span> <span data-ttu-id="6ee01-299">Výchozí velikost je `1048576` bajtů (1 MB).</span><span class="sxs-lookup"><span data-stu-id="6ee01-299">The default size is `1048576` bytes (1 MB).</span></span>

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

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="6ee01-300">Konfigurace proxy serveru používá protokol HTTP a token pro párování</span><span class="sxs-lookup"><span data-stu-id="6ee01-300">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="6ee01-301">*Platí jenom pro hostování mimo proces.*</span><span class="sxs-lookup"><span data-stu-id="6ee01-301">*Only applies to out-of-process hosting.*</span></span>

<span data-ttu-id="6ee01-302">Proxy server mezi modul ASP.NET Core a Kestrel používá protokol HTTP.</span><span class="sxs-lookup"><span data-stu-id="6ee01-302">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="6ee01-303">Neexistuje žádné riziko odposloucháváním provozu mezi modulu a Kestrel z umístění mimo server.</span><span class="sxs-lookup"><span data-stu-id="6ee01-303">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="6ee01-304">Párování token se používá k zajištění, že požadavků přijatých službou Kestrel byly směrovány přes proxy server službou IIS a nebyl dodán z nějakého jiného zdroje.</span><span class="sxs-lookup"><span data-stu-id="6ee01-304">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="6ee01-305">Vytvoření a nastavení do proměnné prostředí párování token (`ASPNETCORE_TOKEN`) modulu.</span><span class="sxs-lookup"><span data-stu-id="6ee01-305">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="6ee01-306">Párování token byl nastavený i do záhlaví (`MS-ASPNETCORE-TOKEN`) na všechny požadavky směrovány přes proxy server.</span><span class="sxs-lookup"><span data-stu-id="6ee01-306">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="6ee01-307">Služba IIS Middleware kontroly požadavku že přijme potvrďte, že odpovídá párování hodnota tokenu hlavičky hodnotu proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="6ee01-307">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="6ee01-308">Pokud token hodnoty se neshodují, žádost je zaznamenána a odmítnut.</span><span class="sxs-lookup"><span data-stu-id="6ee01-308">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="6ee01-309">Párování proměnná tokenu prostředí a přenos dat mezi modulu a Kestrel nejsou dostupné z umístění mimo server.</span><span class="sxs-lookup"><span data-stu-id="6ee01-309">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="6ee01-310">Nainstalovat bez mého párování hodnota tokenu nelze útočník odesílat požadavky, které obejít kontrolu v IIS middlewaru.</span><span class="sxs-lookup"><span data-stu-id="6ee01-310">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="6ee01-311">Modul ASP.NET Core s službu IIS sdílenou konfiguraci</span><span class="sxs-lookup"><span data-stu-id="6ee01-311">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="6ee01-312">Instalační služba modulu ASP.NET Core se spouští s oprávněními účtu **TrustedInstaller** .</span><span class="sxs-lookup"><span data-stu-id="6ee01-312">The ASP.NET Core Module installer runs with the privileges of the **TrustedInstaller** account.</span></span> <span data-ttu-id="6ee01-313">Vzhledem k tomu, že účet místního systému nemá oprávnění k úpravám pro cestu ke sdílené složce, kterou používá sdílená konfigurace služby IIS, vyvolá Instalační program chybu odepření přístupu při pokusu o konfiguraci nastavení modulu v souboru *ApplicationHost. config* v sdílená složka.</span><span class="sxs-lookup"><span data-stu-id="6ee01-313">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the *applicationHost.config* file on the share.</span></span>

<span data-ttu-id="6ee01-314">Pokud používáte konfiguraci IIS Shared na stejném počítači jako instalaci služby IIS, spusťte instalační program sady prostředků ASP.NET Core s `OPT_NO_SHARED_CONFIG_CHECK` parametrem nastaveným na: `1`</span><span class="sxs-lookup"><span data-stu-id="6ee01-314">When using an IIS Shared Configuration on the same machine as the IIS installation, run the ASP.NET Core Hosting Bundle installer with the `OPT_NO_SHARED_CONFIG_CHECK` parameter set to `1`:</span></span>

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

<span data-ttu-id="6ee01-315">Pokud cesta ke sdílené konfiguraci není ve stejném počítači jako instalace služby IIS, postupujte podle těchto kroků:</span><span class="sxs-lookup"><span data-stu-id="6ee01-315">When the path to the shared configuration isn't on the same machine as the IIS installation, follow these steps:</span></span>

1. <span data-ttu-id="6ee01-316">Zakážete sdílenou konfiguraci IIS.</span><span class="sxs-lookup"><span data-stu-id="6ee01-316">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="6ee01-317">Spusťte instalační program.</span><span class="sxs-lookup"><span data-stu-id="6ee01-317">Run the installer.</span></span>
1. <span data-ttu-id="6ee01-318">Export aktualizovaný *applicationHost.config* souboru do sdílené složky.</span><span class="sxs-lookup"><span data-stu-id="6ee01-318">Export the updated *applicationHost.config* file to the share.</span></span>
1. <span data-ttu-id="6ee01-319">Znovu povolte sdílenou konfiguraci IIS.</span><span class="sxs-lookup"><span data-stu-id="6ee01-319">Re-enable the IIS Shared Configuration.</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="6ee01-320">Verze modulu a hostování sady Instalační protokoly</span><span class="sxs-lookup"><span data-stu-id="6ee01-320">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="6ee01-321">Chcete-li zjistit verzi nainstalovaný modul ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="6ee01-321">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="6ee01-322">V hostitelském systému, přejděte na *%windir%\System32\inetsrv*.</span><span class="sxs-lookup"><span data-stu-id="6ee01-322">On the hosting system, navigate to *%windir%\System32\inetsrv*.</span></span>
1. <span data-ttu-id="6ee01-323">Vyhledejte *aspnetcore.dll* souboru.</span><span class="sxs-lookup"><span data-stu-id="6ee01-323">Locate the *aspnetcore.dll* file.</span></span>
1. <span data-ttu-id="6ee01-324">Klikněte pravým tlačítkem na soubor a vyberte **vlastnosti** z kontextové nabídky.</span><span class="sxs-lookup"><span data-stu-id="6ee01-324">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="6ee01-325">Vyberte **podrobnosti** kartu. **Verze souboru** a **verze produktu** představují nainstalovaná verze modulu.</span><span class="sxs-lookup"><span data-stu-id="6ee01-325">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="6ee01-326">Instalační protokoly hostování sady prostředků modulu se nacházejí v *C:\\uživatelé\\% UserName %\\AppData\\místní\\Temp*. Soubor *dd_DotNetCoreWinSvrHosting__\<časové razítko > _000_AspNetCoreModule_x64.log*.</span><span class="sxs-lookup"><span data-stu-id="6ee01-326">The Hosting Bundle installer logs for the module are found at *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. The file is named *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="6ee01-327">Modul, schéma a konfiguraci umístění souborů</span><span class="sxs-lookup"><span data-stu-id="6ee01-327">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="6ee01-328">Modul</span><span class="sxs-lookup"><span data-stu-id="6ee01-328">Module</span></span>

<span data-ttu-id="6ee01-329">**IIS (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="6ee01-329">**IIS (x86/amd64):**</span></span>

* <span data-ttu-id="6ee01-330">%windir%\System32\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="6ee01-330">%windir%\System32\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="6ee01-331">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="6ee01-331">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="6ee01-332">%ProgramFiles%\IIS\Asp.NET core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="6ee01-332">%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="6ee01-333">% ProgramFiles (x86) %\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="6ee01-333">%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

<span data-ttu-id="6ee01-334">**Služba IIS Express (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="6ee01-334">**IIS Express (x86/amd64):**</span></span>

* <span data-ttu-id="6ee01-335">%ProgramFiles%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="6ee01-335">%ProgramFiles%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="6ee01-336">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="6ee01-336">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="6ee01-337">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="6ee01-337">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="6ee01-338">% ProgramFiles (x86) %\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="6ee01-338">%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

### <a name="schema"></a><span data-ttu-id="6ee01-339">Schéma</span><span class="sxs-lookup"><span data-stu-id="6ee01-339">Schema</span></span>

<span data-ttu-id="6ee01-340">**SLUŽBA IIS**</span><span class="sxs-lookup"><span data-stu-id="6ee01-340">**IIS**</span></span>

* <span data-ttu-id="6ee01-341">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="6ee01-341">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span></span>

* <span data-ttu-id="6ee01-342">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.XML</span><span class="sxs-lookup"><span data-stu-id="6ee01-342">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span></span>

<span data-ttu-id="6ee01-343">**Služba IIS Express**</span><span class="sxs-lookup"><span data-stu-id="6ee01-343">**IIS Express**</span></span>

* <span data-ttu-id="6ee01-344">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="6ee01-344">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span></span>

* <span data-ttu-id="6ee01-345">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span><span class="sxs-lookup"><span data-stu-id="6ee01-345">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span></span>

### <a name="configuration"></a><span data-ttu-id="6ee01-346">Konfigurace</span><span class="sxs-lookup"><span data-stu-id="6ee01-346">Configuration</span></span>

<span data-ttu-id="6ee01-347">**SLUŽBA IIS**</span><span class="sxs-lookup"><span data-stu-id="6ee01-347">**IIS**</span></span>

* <span data-ttu-id="6ee01-348">%windir%\System32\inetsrv\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="6ee01-348">%windir%\System32\inetsrv\config\applicationHost.config</span></span>

<span data-ttu-id="6ee01-349">**Služba IIS Express**</span><span class="sxs-lookup"><span data-stu-id="6ee01-349">**IIS Express**</span></span>

* <span data-ttu-id="6ee01-350">Visual Studio: {root aplikace}\\. vs\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="6ee01-350">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span></span>

* <span data-ttu-id="6ee01-351">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span><span class="sxs-lookup"><span data-stu-id="6ee01-351">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span></span>

<span data-ttu-id="6ee01-352">Soubory můžete najít tak, že *aspnetcore* v *applicationHost.config* souboru.</span><span class="sxs-lookup"><span data-stu-id="6ee01-352">The files can be found by searching for *aspnetcore* in the *applicationHost.config* file.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="6ee01-353">Modul ASP.NET Core je nativním modulem služby IIS, který se připojuje k kanálu IIS na jednu z těchto:</span><span class="sxs-lookup"><span data-stu-id="6ee01-353">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to either:</span></span>

* <span data-ttu-id="6ee01-354">Hostování aplikace ASP.NET Core uvnitř pracovního procesu služby IIS (`w3wp.exe`), který se nazývá [model hostování v rámci procesu](#in-process-hosting-model).</span><span class="sxs-lookup"><span data-stu-id="6ee01-354">Host an ASP.NET Core app inside of the IIS worker process (`w3wp.exe`), called the [in-process hosting model](#in-process-hosting-model).</span></span>
* <span data-ttu-id="6ee01-355">Dopředné webové požadavky do back-endu ASP.NET Core aplikace, na které běží [Server Kestrel](xref:fundamentals/servers/kestrel), se nazývá [model hostování mimo proces](#out-of-process-hosting-model).</span><span class="sxs-lookup"><span data-stu-id="6ee01-355">Forward web requests to a backend ASP.NET Core app running the [Kestrel server](xref:fundamentals/servers/kestrel), called the [out-of-process hosting model](#out-of-process-hosting-model).</span></span>

<span data-ttu-id="6ee01-356">Podporované verze systému Windows:</span><span class="sxs-lookup"><span data-stu-id="6ee01-356">Supported Windows versions:</span></span>

* <span data-ttu-id="6ee01-357">Windows 7 nebo novější</span><span class="sxs-lookup"><span data-stu-id="6ee01-357">Windows 7 or later</span></span>
* <span data-ttu-id="6ee01-358">Windows Server 2008 R2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="6ee01-358">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="6ee01-359">Při hostování v procesu používá modul implementaci vnitroprocesového serveru pro službu IIS nazvanou IIS HTTP Server (`IISHttpServer`).</span><span class="sxs-lookup"><span data-stu-id="6ee01-359">When hosting in-process, the module uses an in-process server implementation for IIS, called IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="6ee01-360">Při hostování mimo proces funguje modul pouze s Kestrel.</span><span class="sxs-lookup"><span data-stu-id="6ee01-360">When hosting out-of-process, the module only works with Kestrel.</span></span> <span data-ttu-id="6ee01-361">Modul nefunguje s [ovladačem HTTP. sys](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="6ee01-361">The module doesn't function with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

## <a name="hosting-models"></a><span data-ttu-id="6ee01-362">Modely hostingu</span><span class="sxs-lookup"><span data-stu-id="6ee01-362">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="6ee01-363">Model hostování v procesu</span><span class="sxs-lookup"><span data-stu-id="6ee01-363">In-process hosting model</span></span>

<span data-ttu-id="6ee01-364">Chcete-li nakonfigurovat aplikaci pro hostování v rámci procesu, přidejte `<AspNetCoreHostingModel>` do souboru projektu aplikace vlastnost s `InProcess` hodnotou (hostování mimo `OutOfProcess`proces je nastavená na):</span><span class="sxs-lookup"><span data-stu-id="6ee01-364">To configure an app for in-process hosting, add the `<AspNetCoreHostingModel>` property to the app's project file with a value of `InProcess` (out-of-process hosting is set with `OutOfProcess`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>InProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="6ee01-365">Model hostování v rámci procesu není podporován pro aplikace ASP.NET Core, které cílí na .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="6ee01-365">The in-process hosting model isn't supported for ASP.NET Core apps that target the .NET Framework.</span></span>

<span data-ttu-id="6ee01-366">Pokud vlastnost není v souboru přítomna, výchozí hodnota je `OutOfProcess`. `<AspNetCoreHostingModel>`</span><span class="sxs-lookup"><span data-stu-id="6ee01-366">If the `<AspNetCoreHostingModel>` property isn't present in the file, the default value is `OutOfProcess`.</span></span>

<span data-ttu-id="6ee01-367">Při hostování v procesu platí následující vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="6ee01-367">The following characteristics apply when hosting in-process:</span></span>

* <span data-ttu-id="6ee01-368">Místo serveru [Kestrel](xref:fundamentals/servers/kestrel) se`IISHttpServer`používá server HTTP služby IIS ().</span><span class="sxs-lookup"><span data-stu-id="6ee01-368">IIS HTTP Server (`IISHttpServer`) is used instead of [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span> <span data-ttu-id="6ee01-369">Pro vnitroprocesové [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) volání <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> :</span><span class="sxs-lookup"><span data-stu-id="6ee01-369">For in-process, [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> to:</span></span>

  * <span data-ttu-id="6ee01-370">Zaregistrujte `IISHttpServer`.</span><span class="sxs-lookup"><span data-stu-id="6ee01-370">Register the `IISHttpServer`.</span></span>
  * <span data-ttu-id="6ee01-371">Nakonfigurujte port a základní cestu, na kterých má Server naslouchat při spuštění za modulem ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="6ee01-371">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
  * <span data-ttu-id="6ee01-372">Nakonfigurujte hostitele tak, aby zachytával chyby při spuštění.</span><span class="sxs-lookup"><span data-stu-id="6ee01-372">Configure the host to capture startup errors.</span></span>

* <span data-ttu-id="6ee01-373">[RequestTimeout atribut](#attributes-of-the-aspnetcore-element) neplatí pro hostování v procesu.</span><span class="sxs-lookup"><span data-stu-id="6ee01-373">The [requestTimeout attribute](#attributes-of-the-aspnetcore-element) doesn't apply to in-process hosting.</span></span>

* <span data-ttu-id="6ee01-374">Sdílení fondem aplikací mezi aplikacemi se nepodporuje.</span><span class="sxs-lookup"><span data-stu-id="6ee01-374">Sharing an app pool among apps isn't supported.</span></span> <span data-ttu-id="6ee01-375">Použijte jeden fond aplikací na aplikaci.</span><span class="sxs-lookup"><span data-stu-id="6ee01-375">Use one app pool per app.</span></span>

* <span data-ttu-id="6ee01-376">Při použití [Webdeploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) nebo ručně uvedení [soubor app_offline.htm v nasazení](xref:host-and-deploy/iis/index#locked-deployment-files), aplikace nemusí vypnout okamžitě při otevření připojení.</span><span class="sxs-lookup"><span data-stu-id="6ee01-376">When using [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) or manually placing an [app_offline.htm file in the deployment](xref:host-and-deploy/iis/index#locked-deployment-files), the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="6ee01-377">Připojení soketu websocket bylo třeba může způsobit prodlevu při ukončení aplikace.</span><span class="sxs-lookup"><span data-stu-id="6ee01-377">For example, a websocket connection may delay app shut down.</span></span>

* <span data-ttu-id="6ee01-378">Architektura (bitové verze) (x64 nebo x86) nainstalovaný modul runtime a aplikace musí odpovídat architektuře fondu aplikací.</span><span class="sxs-lookup"><span data-stu-id="6ee01-378">The architecture (bitness) of the app and installed runtime (x64 or x86) must match the architecture of the app pool.</span></span>

* <span data-ttu-id="6ee01-379">Odpojení klienta jsou zjištěny.</span><span class="sxs-lookup"><span data-stu-id="6ee01-379">Client disconnects are detected.</span></span> <span data-ttu-id="6ee01-380">[HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) token zrušení se zrušila, když se klient odpojí.</span><span class="sxs-lookup"><span data-stu-id="6ee01-380">The [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) cancellation token is cancelled when the client disconnects.</span></span>

* <span data-ttu-id="6ee01-381">V ASP.NET Core 2.2.1 nebo dřívější <xref:System.IO.Directory.GetCurrentDirectory*> vrátí pracovní adresář procesu spuštěného službou IIS místo adresáře aplikace (například *C:\Windows\System32\inetsrv* pro *W3wp. exe*).</span><span class="sxs-lookup"><span data-stu-id="6ee01-381">In ASP.NET Core 2.2.1 or earlier, <xref:System.IO.Directory.GetCurrentDirectory*> returns the worker directory of the process started by IIS rather than the app's directory (for example, *C:\Windows\System32\inetsrv* for *w3wp.exe*).</span></span>

  <span data-ttu-id="6ee01-382">Ukázkový kód, který nastaví aktuální adresář aplikace, najdete v článku [CurrentDirectoryHelpers třídy](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/2.x/CurrentDirectoryHelpers.cs).</span><span class="sxs-lookup"><span data-stu-id="6ee01-382">For sample code that sets the app's current directory, see the [CurrentDirectoryHelpers class](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/2.x/CurrentDirectoryHelpers.cs).</span></span> <span data-ttu-id="6ee01-383">Volání `SetCurrentDirectory` metody.</span><span class="sxs-lookup"><span data-stu-id="6ee01-383">Call the `SetCurrentDirectory` method.</span></span> <span data-ttu-id="6ee01-384">Následující volání <xref:System.IO.Directory.GetCurrentDirectory*> poskytují adresáře aplikace.</span><span class="sxs-lookup"><span data-stu-id="6ee01-384">Subsequent calls to <xref:System.IO.Directory.GetCurrentDirectory*> provide the app's directory.</span></span>

* <span data-ttu-id="6ee01-385">Při hostování v procesu <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> není volána interně pro inicializaci uživatele.</span><span class="sxs-lookup"><span data-stu-id="6ee01-385">When hosting in-process, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span> <span data-ttu-id="6ee01-386"><xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> Proto implementace používaná k transformaci deklarací po každém ověření není ve výchozím nastavení aktivována.</span><span class="sxs-lookup"><span data-stu-id="6ee01-386">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="6ee01-387">Při transformaci deklarací s <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementací zavolejte <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> na přidat ověřovací služby:</span><span class="sxs-lookup"><span data-stu-id="6ee01-387">When transforming claims with an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation, call <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> to add authentication services:</span></span>

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

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="6ee01-388">Model hostování mimo proces</span><span class="sxs-lookup"><span data-stu-id="6ee01-388">Out-of-process hosting model</span></span>

<span data-ttu-id="6ee01-389">Chcete-li nakonfigurovat aplikaci pro hostování mimo procesy, použijte některý z následujících přístupů v souboru projektu:</span><span class="sxs-lookup"><span data-stu-id="6ee01-389">To configure an app for out-of-process hosting, use either of the following approaches in the project file:</span></span>

* <span data-ttu-id="6ee01-390">Nezadávejte `<AspNetCoreHostingModel>` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="6ee01-390">Don't specify the `<AspNetCoreHostingModel>` property.</span></span> <span data-ttu-id="6ee01-391">Pokud vlastnost není v souboru přítomna, výchozí hodnota je `OutOfProcess`. `<AspNetCoreHostingModel>`</span><span class="sxs-lookup"><span data-stu-id="6ee01-391">If the `<AspNetCoreHostingModel>` property isn't present in the file, the default value is `OutOfProcess`.</span></span>
* <span data-ttu-id="6ee01-392">Nastavte hodnotu `<AspNetCoreHostingModel>` vlastnosti na `OutOfProcess` ( `InProcess`vnitroprocesové hostování v procesu je nastaveno na):</span><span class="sxs-lookup"><span data-stu-id="6ee01-392">Set the value of the `<AspNetCoreHostingModel>` property to `OutOfProcess` (in-process hosting is set with `InProcess`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="6ee01-393">[](xref:fundamentals/servers/kestrel) Místo http serveru IIS (`IISHttpServer`) se používá server Kestrel.</span><span class="sxs-lookup"><span data-stu-id="6ee01-393">[Kestrel](xref:fundamentals/servers/kestrel) server is used instead of IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="6ee01-394">Pro mimoprocesové [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) volání <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> do:</span><span class="sxs-lookup"><span data-stu-id="6ee01-394">For out-of-process, [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> to:</span></span>

* <span data-ttu-id="6ee01-395">Nakonfigurujte port a základní cestu, na kterých má Server naslouchat při spuštění za modulem ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="6ee01-395">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
* <span data-ttu-id="6ee01-396">Nakonfigurujte hostitele tak, aby zachytával chyby při spuštění.</span><span class="sxs-lookup"><span data-stu-id="6ee01-396">Configure the host to capture startup errors.</span></span>

### <a name="hosting-model-changes"></a><span data-ttu-id="6ee01-397">Hostování změny modelu</span><span class="sxs-lookup"><span data-stu-id="6ee01-397">Hosting model changes</span></span>

<span data-ttu-id="6ee01-398">Pokud `hostingModel` nastavení se změnilo v *web.config* souboru (podrobně [konfigurace pomocí souboru web.config](#configuration-with-webconfig) části), modul pro službu IIS recykluje pracovní proces.</span><span class="sxs-lookup"><span data-stu-id="6ee01-398">If the `hostingModel` setting is changed in the *web.config* file (explained in the [Configuration with web.config](#configuration-with-webconfig) section), the module recycles the worker process for IIS.</span></span>

<span data-ttu-id="6ee01-399">Pro službu IIS Express modul nebude recyklovat pracovní proces, ale místo toho aktivuje řádné vypnutí aktuální proces IIS Express.</span><span class="sxs-lookup"><span data-stu-id="6ee01-399">For IIS Express, the module doesn't recycle the worker process but instead triggers a graceful shutdown of the current IIS Express process.</span></span> <span data-ttu-id="6ee01-400">Další požadavek na aplikaci vytvoří nový proces IIS Express.</span><span class="sxs-lookup"><span data-stu-id="6ee01-400">The next request to the app spawns a new IIS Express process.</span></span>

### <a name="process-name"></a><span data-ttu-id="6ee01-401">Název procesu</span><span class="sxs-lookup"><span data-stu-id="6ee01-401">Process name</span></span>

<span data-ttu-id="6ee01-402">`Process.GetCurrentProcess().ProcessName` sestavy `w3wp` / `iisexpress` (v procesu) nebo `dotnet` (out-of-process).</span><span class="sxs-lookup"><span data-stu-id="6ee01-402">`Process.GetCurrentProcess().ProcessName` reports `w3wp`/`iisexpress` (in-process) or `dotnet` (out-of-process).</span></span>

<span data-ttu-id="6ee01-403">Mnoho nativních modulů, jako je ověřování systému Windows, zůstane aktivní.</span><span class="sxs-lookup"><span data-stu-id="6ee01-403">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="6ee01-404">Další informace o aktivních modulech služby IIS pomocí modulu ASP.NET Core najdete v <xref:host-and-deploy/iis/modules>tématu.</span><span class="sxs-lookup"><span data-stu-id="6ee01-404">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="6ee01-405">Modul ASP.NET Core může také:</span><span class="sxs-lookup"><span data-stu-id="6ee01-405">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="6ee01-406">Nastavte proměnné prostředí pro pracovní proces.</span><span class="sxs-lookup"><span data-stu-id="6ee01-406">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="6ee01-407">Protokoluje výstup stdout do úložiště souborů pro řešení problémů při spuštění.</span><span class="sxs-lookup"><span data-stu-id="6ee01-407">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="6ee01-408">Předejte tokeny ověřování systému Windows.</span><span class="sxs-lookup"><span data-stu-id="6ee01-408">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="6ee01-409">Jak nainstalovat a používat modul ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6ee01-409">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="6ee01-410">Pokyny k instalaci modulu ASP.NET Core najdete v tématu [instalace hostující sady .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span><span class="sxs-lookup"><span data-stu-id="6ee01-410">For instructions on how to install the ASP.NET Core Module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="6ee01-411">Konfigurace pomocí souboru web.config</span><span class="sxs-lookup"><span data-stu-id="6ee01-411">Configuration with web.config</span></span>

<span data-ttu-id="6ee01-412">Modul ASP.NET Core, nastavena `aspNetCore` část `system.webServer` uzlu na webu *web.config* souboru.</span><span class="sxs-lookup"><span data-stu-id="6ee01-412">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="6ee01-413">Následující *web.config* soubor je publikován pro [nasazení závisí na architektuře](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) a konfiguruje modul ASP.NET Core pro zpracování požadavků lokality:</span><span class="sxs-lookup"><span data-stu-id="6ee01-413">The following *web.config* file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

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

<span data-ttu-id="6ee01-414">Následující *web.config* je publikována pro [samostatná nasazení](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span><span class="sxs-lookup"><span data-stu-id="6ee01-414">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

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

<span data-ttu-id="6ee01-415"><xref:System.Configuration.SectionInformation.InheritInChildApplications*> Je nastavena na `false` k označení, že nastavení zadané v rámci [ \<umístění >](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) element nedědí od neznámých aplikací, které jsou umístěny v podadresáři aplikace.</span><span class="sxs-lookup"><span data-stu-id="6ee01-415">The <xref:System.Configuration.SectionInformation.InheritInChildApplications*> property is set to `false` to indicate that the settings specified within the [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) element aren't inherited by apps that reside in a subdirectory of the app.</span></span>

<span data-ttu-id="6ee01-416">Když je aplikace nasazená na [služby Azure App Service](https://azure.microsoft.com/services/app-service/), `stdoutLogFile` je nastavena cesta `\\?\%home%\LogFiles\stdout`.</span><span class="sxs-lookup"><span data-stu-id="6ee01-416">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="6ee01-417">Protokoly stdout a uloží ji *LogFiles* složce, která je na místě automaticky vytvořené službou.</span><span class="sxs-lookup"><span data-stu-id="6ee01-417">The path saves stdout logs to the *LogFiles* folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="6ee01-418">Informace o konfiguraci dílčí aplikace služby IIS, naleznete v tématu <xref:host-and-deploy/iis/index#sub-applications>.</span><span class="sxs-lookup"><span data-stu-id="6ee01-418">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="6ee01-419">Atributy elementu aspNetCore</span><span class="sxs-lookup"><span data-stu-id="6ee01-419">Attributes of the aspNetCore element</span></span>

| <span data-ttu-id="6ee01-420">Atribut</span><span class="sxs-lookup"><span data-stu-id="6ee01-420">Attribute</span></span> | <span data-ttu-id="6ee01-421">Popis</span><span class="sxs-lookup"><span data-stu-id="6ee01-421">Description</span></span> | <span data-ttu-id="6ee01-422">Výchozí</span><span class="sxs-lookup"><span data-stu-id="6ee01-422">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="6ee01-423">Volitelný atribut řetězce.</span><span class="sxs-lookup"><span data-stu-id="6ee01-423">Optional string attribute.</span></span></p><p><span data-ttu-id="6ee01-424">Argumenty pro spustitelný soubor určený v **processPath**.</span><span class="sxs-lookup"><span data-stu-id="6ee01-424">Arguments to the executable specified in **processPath**.</span></span></p> | |
| `disableStartUpErrorPage` | <p><span data-ttu-id="6ee01-425">Volitelný logický atribut.</span><span class="sxs-lookup"><span data-stu-id="6ee01-425">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="6ee01-426">Při hodnotě true **502.5 – selhání procesu** Potlačené stránky a znakovou stránku 502 stav nakonfigurované v *web.config* má přednost před.</span><span class="sxs-lookup"><span data-stu-id="6ee01-426">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="6ee01-427">Volitelný logický atribut.</span><span class="sxs-lookup"><span data-stu-id="6ee01-427">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="6ee01-428">Při hodnotě true se token předá podřízený proces naslouchání na ASPNETCORE_PORT % jako záhlaví "MS-ASPNETCORE-WINAUTHTOKEN" každý požadavek.</span><span class="sxs-lookup"><span data-stu-id="6ee01-428">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="6ee01-429">Je odpovědností tento proces pro volání CloseHandle na tento token každý požadavek.</span><span class="sxs-lookup"><span data-stu-id="6ee01-429">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `hostingModel` | <p><span data-ttu-id="6ee01-430">Volitelný atribut řetězce.</span><span class="sxs-lookup"><span data-stu-id="6ee01-430">Optional string attribute.</span></span></p><p><span data-ttu-id="6ee01-431">Určuje model hostování jako v procesu (`InProcess`) nebo out-of-process (`OutOfProcess`).</span><span class="sxs-lookup"><span data-stu-id="6ee01-431">Specifies the hosting model as in-process (`InProcess`) or out-of-process (`OutOfProcess`).</span></span></p> | `OutOfProcess` |
| `processesPerApplication` | <p><span data-ttu-id="6ee01-432">Volitelný celočíselný atribut.</span><span class="sxs-lookup"><span data-stu-id="6ee01-432">Optional integer attribute.</span></span></p><p><span data-ttu-id="6ee01-433">Určuje počet instancí procesu zadaný v **processPath** nastavení, které lze se nespředený nahoru na app.</span><span class="sxs-lookup"><span data-stu-id="6ee01-433">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="6ee01-434">&dagger;Hostování v procesu, je omezena na hodnotu `1`.</span><span class="sxs-lookup"><span data-stu-id="6ee01-434">&dagger;For in-process hosting, the value is limited to `1`.</span></span></p><p><span data-ttu-id="6ee01-435">Nastavení `processesPerApplication` se nedoporučuje.</span><span class="sxs-lookup"><span data-stu-id="6ee01-435">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="6ee01-436">Tento atribut bude v budoucí verzi odebrán.</span><span class="sxs-lookup"><span data-stu-id="6ee01-436">This attribute will be removed in a future release.</span></span></p> | <span data-ttu-id="6ee01-437">Výchozí hodnota: `1`</span><span class="sxs-lookup"><span data-stu-id="6ee01-437">Default: `1`</span></span><br><span data-ttu-id="6ee01-438">Min: `1`</span><span class="sxs-lookup"><span data-stu-id="6ee01-438">Min: `1`</span></span><br><span data-ttu-id="6ee01-439">Max: `100`&dagger;</span><span class="sxs-lookup"><span data-stu-id="6ee01-439">Max: `100`&dagger;</span></span> |
| `processPath` | <p><span data-ttu-id="6ee01-440">Požadovaný atribut typu string.</span><span class="sxs-lookup"><span data-stu-id="6ee01-440">Required string attribute.</span></span></p><p><span data-ttu-id="6ee01-441">Cesta ke spustitelnému souboru, který spustí nějaký proces naslouchání požadavků protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="6ee01-441">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="6ee01-442">Jsou podporovány relativní cesty.</span><span class="sxs-lookup"><span data-stu-id="6ee01-442">Relative paths are supported.</span></span> <span data-ttu-id="6ee01-443">Pokud cestu začíná `.`, cesta se považuje za kořeni webu.</span><span class="sxs-lookup"><span data-stu-id="6ee01-443">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="6ee01-444">Volitelný celočíselný atribut.</span><span class="sxs-lookup"><span data-stu-id="6ee01-444">Optional integer attribute.</span></span></p><p><span data-ttu-id="6ee01-445">Určuje, kolikrát podle procesu **processPath** může při selhání za minutu.</span><span class="sxs-lookup"><span data-stu-id="6ee01-445">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="6ee01-446">Pokud je tento limit překročen, modulu zastaví spuštění procesu pro zbytek minuty.</span><span class="sxs-lookup"><span data-stu-id="6ee01-446">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p><p><span data-ttu-id="6ee01-447">Není podporováno s hostitelem v procesu.</span><span class="sxs-lookup"><span data-stu-id="6ee01-447">Not supported with in-process hosting.</span></span></p> | <span data-ttu-id="6ee01-448">Výchozí hodnota: `10`</span><span class="sxs-lookup"><span data-stu-id="6ee01-448">Default: `10`</span></span><br><span data-ttu-id="6ee01-449">Min: `0`</span><span class="sxs-lookup"><span data-stu-id="6ee01-449">Min: `0`</span></span><br><span data-ttu-id="6ee01-450">Max: `100`</span><span class="sxs-lookup"><span data-stu-id="6ee01-450">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="6ee01-451">Atribut volitelný časový interval.</span><span class="sxs-lookup"><span data-stu-id="6ee01-451">Optional timespan attribute.</span></span></p><p><span data-ttu-id="6ee01-452">Určuje dobu, pro kterou modul ASP.NET Core čeká na odpověď z procesu naslouchání na ASPNETCORE_PORT %.</span><span class="sxs-lookup"><span data-stu-id="6ee01-452">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="6ee01-453">Ve verzích modul ASP.NET Core dodávané s verzí technologie ASP.NET Core 2.1 nebo novější `requestTimeout` je zadán v hodiny, minuty a sekundy.</span><span class="sxs-lookup"><span data-stu-id="6ee01-453">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p><p><span data-ttu-id="6ee01-454">Neplatí pro hostování v procesu.</span><span class="sxs-lookup"><span data-stu-id="6ee01-454">Doesn't apply to in-process hosting.</span></span> <span data-ttu-id="6ee01-455">Hostování v procesu, čeká modul app ke zpracování požadavku.</span><span class="sxs-lookup"><span data-stu-id="6ee01-455">For in-process hosting, the module waits for the app to process the request.</span></span></p><p><span data-ttu-id="6ee01-456">Platné hodnoty segmentů minut a sekund řetězce jsou v rozsahu 0-59.</span><span class="sxs-lookup"><span data-stu-id="6ee01-456">Valid values for minutes and seconds segments of the string are in the range 0-59.</span></span> <span data-ttu-id="6ee01-457">Při použití hodnoty **60** v hodnotě minut nebo sekund dojde k chybě systému *500 – interní chyba serveru*.</span><span class="sxs-lookup"><span data-stu-id="6ee01-457">Use of **60** in the value for minutes or seconds results in a *500 - Internal Server Error*.</span></span></p> | <span data-ttu-id="6ee01-458">Výchozí hodnota: `00:02:00`</span><span class="sxs-lookup"><span data-stu-id="6ee01-458">Default: `00:02:00`</span></span><br><span data-ttu-id="6ee01-459">Min: `00:00:00`</span><span class="sxs-lookup"><span data-stu-id="6ee01-459">Min: `00:00:00`</span></span><br><span data-ttu-id="6ee01-460">Max: `360:00:00`</span><span class="sxs-lookup"><span data-stu-id="6ee01-460">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="6ee01-461">Volitelný celočíselný atribut.</span><span class="sxs-lookup"><span data-stu-id="6ee01-461">Optional integer attribute.</span></span></p><p><span data-ttu-id="6ee01-462">Doba trvání v sekundách, které modul čeká na spustitelný soubor na řádné vypnutí při *app_offline.htm* je detekován soubor.</span><span class="sxs-lookup"><span data-stu-id="6ee01-462">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="6ee01-463">Výchozí hodnota: `10`</span><span class="sxs-lookup"><span data-stu-id="6ee01-463">Default: `10`</span></span><br><span data-ttu-id="6ee01-464">Min: `0`</span><span class="sxs-lookup"><span data-stu-id="6ee01-464">Min: `0`</span></span><br><span data-ttu-id="6ee01-465">Max: `600`</span><span class="sxs-lookup"><span data-stu-id="6ee01-465">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="6ee01-466">Volitelný celočíselný atribut.</span><span class="sxs-lookup"><span data-stu-id="6ee01-466">Optional integer attribute.</span></span></p><p><span data-ttu-id="6ee01-467">Doba trvání v sekundách, které modul čeká na spustitelný soubor ke spuštění procesu naslouchání na portu.</span><span class="sxs-lookup"><span data-stu-id="6ee01-467">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="6ee01-468">Pokud je překročen časový limit, modul ukončí proces.</span><span class="sxs-lookup"><span data-stu-id="6ee01-468">If this time limit is exceeded, the module kills the process.</span></span> <span data-ttu-id="6ee01-469">Modul se pokusí znovu spustit proces, když ji dostane novou žádost a pokusit se restartovat proces na dalších příchozích požadavků, pokud se aplikaci nepodaří spustit **rapidFailsPerMinute** málokdy za posledních minuta se zajištěním provozu.</span><span class="sxs-lookup"><span data-stu-id="6ee01-469">The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="6ee01-470">Hodnota 0 (nula) je **ne** za neomezený časový limit.</span><span class="sxs-lookup"><span data-stu-id="6ee01-470">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="6ee01-471">Výchozí hodnota: `120`</span><span class="sxs-lookup"><span data-stu-id="6ee01-471">Default: `120`</span></span><br><span data-ttu-id="6ee01-472">Min: `0`</span><span class="sxs-lookup"><span data-stu-id="6ee01-472">Min: `0`</span></span><br><span data-ttu-id="6ee01-473">Max: `3600`</span><span class="sxs-lookup"><span data-stu-id="6ee01-473">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="6ee01-474">Volitelný logický atribut.</span><span class="sxs-lookup"><span data-stu-id="6ee01-474">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="6ee01-475">Při hodnotě true se **stdout** a **stderr** pro proces určený v **processPath** se přesměrují do souboru zadaného v **stdoutLogFile**.</span><span class="sxs-lookup"><span data-stu-id="6ee01-475">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="6ee01-476">Volitelný atribut řetězce.</span><span class="sxs-lookup"><span data-stu-id="6ee01-476">Optional string attribute.</span></span></p><p><span data-ttu-id="6ee01-477">Určuje relativní nebo absolutní cestu, pro kterou **stdout** a **stderr** z procesu podle **processPath** přihlášeni.</span><span class="sxs-lookup"><span data-stu-id="6ee01-477">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="6ee01-478">Relativní cesty jsou relativní vzhledem k kořen webu.</span><span class="sxs-lookup"><span data-stu-id="6ee01-478">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="6ee01-479">Libovolnou cestu od `.` jsou relativní vzhledem k webu kořenové a všechny ostatní cesty jsou považovány za absolutní cesty.</span><span class="sxs-lookup"><span data-stu-id="6ee01-479">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="6ee01-480">Při vytvoření souboru protokolu se v modulu vytvoří všechny složky, které jsou v cestě zadané.</span><span class="sxs-lookup"><span data-stu-id="6ee01-480">Any folders provided in the path are created by the module when the log file is created.</span></span> <span data-ttu-id="6ee01-481">Pomocí oddělovače podtržítko, časové razítko, ID procesu a přípona souboru ( *.log*) jsou přidány na poslední segment **stdoutLogFile** cestu.</span><span class="sxs-lookup"><span data-stu-id="6ee01-481">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="6ee01-482">Pokud `.\logs\stdout` je zadaný jako hodnota, je uložen protokolu příklad stdout jako *stdout_20180205194132_1934.log* v *protokoly* složky při uložení 5 2. 2018 v 19:41:32 s ID procesu sady 1934.</span><span class="sxs-lookup"><span data-stu-id="6ee01-482">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

### <a name="setting-environment-variables"></a><span data-ttu-id="6ee01-483">Nastavení proměnných prostředí</span><span class="sxs-lookup"><span data-stu-id="6ee01-483">Setting environment variables</span></span>

<span data-ttu-id="6ee01-484">Proměnné prostředí se dá nastavit pro proces v `processPath` atribut.</span><span class="sxs-lookup"><span data-stu-id="6ee01-484">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="6ee01-485">Zadat proměnné prostředí s `<environmentVariable>` podřízený prvek `<environmentVariables>` prvek kolekce.</span><span class="sxs-lookup"><span data-stu-id="6ee01-485">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span> <span data-ttu-id="6ee01-486">Proměnné prostředí nastavené v této části přednost systémové proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="6ee01-486">Environment variables set in this section take precedence over system environment variables.</span></span>

<span data-ttu-id="6ee01-487">Následující příklad nastaví dvou proměnných prostředí.</span><span class="sxs-lookup"><span data-stu-id="6ee01-487">The following example sets two environment variables.</span></span> <span data-ttu-id="6ee01-488">`ASPNETCORE_ENVIRONMENT` nakonfiguruje prostředí aplikace tak, aby `Development`.</span><span class="sxs-lookup"><span data-stu-id="6ee01-488">`ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="6ee01-489">Vývojáři mohou dočasně nastaví tuto hodnotu *web.config* souboru, aby bylo možné vynutit [stránku výjimek pro vývojáře](xref:fundamentals/error-handling) načíst při ladění aplikace výjimky.</span><span class="sxs-lookup"><span data-stu-id="6ee01-489">A developer may temporarily set this value in the *web.config* file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="6ee01-490">`CONFIG_DIR` je příkladem proměnné prostředí, kam má vývojář zapisovat kód, který čte hodnoty při spuštění tvoří cestu pro načtení konfiguračního souboru aplikace.</span><span class="sxs-lookup"><span data-stu-id="6ee01-490">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

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
> <span data-ttu-id="6ee01-491">Alternativou k nastavení prostředí přímo v souboru *Web. config* je zahrnutí `<EnvironmentName>` vlastnosti do profilu publikování ( *. pubxml*) nebo souboru projektu.</span><span class="sxs-lookup"><span data-stu-id="6ee01-491">An alternative to setting the environment directly in *web.config* is to include the `<EnvironmentName>` property in the publish profile (*.pubxml*) or project file.</span></span> <span data-ttu-id="6ee01-492">Tento přístup nastaví prostředí v *souboru Web. config* při publikování projektu:</span><span class="sxs-lookup"><span data-stu-id="6ee01-492">This approach sets the environment in *web.config* when the project is published:</span></span>
>
> ```xml
> <PropertyGroup>
>   <EnvironmentName>Development</EnvironmentName>
> </PropertyGroup>
> ```

> [!WARNING]
> <span data-ttu-id="6ee01-493">Nastavit pouze `ASPNETCORE_ENVIRONMENT` proměnnou prostředí, aby `Development` na přípravy a testování serverů, které nejsou dostupné k nedůvěryhodným sítím, jako je Internet.</span><span class="sxs-lookup"><span data-stu-id="6ee01-493">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## <a name="app_offlinehtm"></a><span data-ttu-id="6ee01-494">app_offline.htm</span><span class="sxs-lookup"><span data-stu-id="6ee01-494">app_offline.htm</span></span>

<span data-ttu-id="6ee01-495">Pokud soubor s názvem *app_offline.htm* je zjištěna v kořenovém adresáři aplikace, že modul ASP.NET Core se pokusí řádné vypnutí aplikace a zastavit zpracování příchozích požadavků.</span><span class="sxs-lookup"><span data-stu-id="6ee01-495">If a file with the name *app_offline.htm* is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="6ee01-496">Pokud aplikace pořád běží po dobu v sekundách podle `shutdownTimeLimit`, že modul ASP.NET Core ukončuje spuštěnému procesu.</span><span class="sxs-lookup"><span data-stu-id="6ee01-496">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="6ee01-497">Zatímco *app_offline.htm* soubor je k dispozici, že modul ASP.NET Core reaguje na požadavky odesílá zpět obsah *app_offline.htm* souboru.</span><span class="sxs-lookup"><span data-stu-id="6ee01-497">While the *app_offline.htm* file is present, the ASP.NET Core Module responds to requests by sending back the contents of the *app_offline.htm* file.</span></span> <span data-ttu-id="6ee01-498">Když *app_offline.htm* soubor bude odstraněn, příští žádosti o spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="6ee01-498">When the *app_offline.htm* file is removed, the next request starts the app.</span></span>

<span data-ttu-id="6ee01-499">Pokud používáte model hostingu mimo proces, aplikace nemusí vypnout okamžitě při otevření připojení.</span><span class="sxs-lookup"><span data-stu-id="6ee01-499">When using the out-of-process hosting model, the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="6ee01-500">Připojení soketu websocket bylo třeba může způsobit prodlevu při ukončení aplikace.</span><span class="sxs-lookup"><span data-stu-id="6ee01-500">For example, a websocket connection may delay app shut down.</span></span>

## <a name="start-up-error-page"></a><span data-ttu-id="6ee01-501">Spuštění chybovou stránku</span><span class="sxs-lookup"><span data-stu-id="6ee01-501">Start-up error page</span></span>

<span data-ttu-id="6ee01-502">Hostování v procesu a mimo proces vytvoření vlastní chybové stránky, když selžou a spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="6ee01-502">Both in-process and out-of-process hosting produce custom error pages when they fail to start the app.</span></span>

<span data-ttu-id="6ee01-503">Pokud se nepodaří najít buď nebo na více instancí procesu žádosti o obslužnou rutinu, že modul ASP.NET Core *500.0 – Chyba načtení obslužné rutiny v procesu/Out – proces* se zobrazí stav znakovou stránku.</span><span class="sxs-lookup"><span data-stu-id="6ee01-503">If the ASP.NET Core Module fails to find either the in-process or out-of-process request handler, a *500.0 - In-Process/Out-Of-Process Handler Load Failure* status code page appears.</span></span>

<span data-ttu-id="6ee01-504">Pro hostování v procesu, pokud se nepodaří spustit aplikaci, že modul ASP.NET Core *500.30 - Start selhání* se zobrazí stav znakovou stránku.</span><span class="sxs-lookup"><span data-stu-id="6ee01-504">For in-process hosting if the ASP.NET Core Module fails to start the app, a *500.30 - Start Failure* status code page appears.</span></span>

<span data-ttu-id="6ee01-505">Pro hostování mimo proces, pokud se nespustí back-endový proces nebo back-endový proces spustí ale nebude moci poslouchat na konfigurovaném portu, že modul ASP.NET Core *502.5 – selhání procesu* se zobrazí stav znakovou stránku.</span><span class="sxs-lookup"><span data-stu-id="6ee01-505">For out-of-process hosting if the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span>

<span data-ttu-id="6ee01-506">Chcete-li potlačit tuto stránku a vrátit se na výchozí služby IIS 5xx stav znakovou stránku, použijte `disableStartUpErrorPage` atribut.</span><span class="sxs-lookup"><span data-stu-id="6ee01-506">To suppress this page and revert to the default IIS 5xx status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="6ee01-507">Další informace o konfiguraci vlastních chybových zpráv, najdete v části [chyby protokolu HTTP \<httpErrors >](/iis/configuration/system.webServer/httpErrors/).</span><span class="sxs-lookup"><span data-stu-id="6ee01-507">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

## <a name="log-creation-and-redirection"></a><span data-ttu-id="6ee01-508">Vytvoření protokolu a přesměrování</span><span class="sxs-lookup"><span data-stu-id="6ee01-508">Log creation and redirection</span></span>

<span data-ttu-id="6ee01-509">Modul ASP.NET Core přesměruje výstup stdout a stderr console na disk, pokud `stdoutLogEnabled` a `stdoutLogFile` atributy `aspNetCore` element jsou nastavené.</span><span class="sxs-lookup"><span data-stu-id="6ee01-509">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="6ee01-510">Při vytvoření souboru protokolu `stdoutLogFile` jsou všechny složky v této cestě vytvářeny modulem.</span><span class="sxs-lookup"><span data-stu-id="6ee01-510">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="6ee01-511">Fond aplikací musí mít oprávnění k zápisu do umístění, ve kterém jsou zapsány protokoly (použijte `IIS AppPool\<app_pool_name>` poskytnout oprávnění k zápisu).</span><span class="sxs-lookup"><span data-stu-id="6ee01-511">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="6ee01-512">Protokoly nejsou střídán, pokud dojde k recyklování procesů/restartování.</span><span class="sxs-lookup"><span data-stu-id="6ee01-512">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="6ee01-513">Je odpovědností hostitel k omezení místa na disku, které využijete v protokolech.</span><span class="sxs-lookup"><span data-stu-id="6ee01-513">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="6ee01-514">Použití protokolu stdout se doporučuje jenom pro řešení potíží při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="6ee01-514">Using the stdout log is only recommended for troubleshooting app startup issues.</span></span> <span data-ttu-id="6ee01-515">Nepoužívejte stdout protokolu pro účely protokolování obecné aplikace.</span><span class="sxs-lookup"><span data-stu-id="6ee01-515">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="6ee01-516">Pro rutiny protokolování v aplikaci ASP.NET Core, použijte protokolování knihovnu, která omezuje velikost souboru protokolu a otočí protokoly.</span><span class="sxs-lookup"><span data-stu-id="6ee01-516">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="6ee01-517">Další informace najdete v tématu [zprostředkovatele přihlášení třetí strany](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="6ee01-517">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="6ee01-518">Časové razítko a soubor rozšíření jsou přidány automaticky při vytvoření souboru protokolu.</span><span class="sxs-lookup"><span data-stu-id="6ee01-518">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="6ee01-519">Název souboru protokolu se skládá připojením časové razítko, ID procesu a přípona souboru ( *.log*) na poslední segment `stdoutLogFile` cestu (obvykle *stdout*) oddělené podtržítka.</span><span class="sxs-lookup"><span data-stu-id="6ee01-519">The log file name is composed by appending the timestamp, process ID, and file extension (*.log*) to the last segment of the `stdoutLogFile` path (typically *stdout*) delimited by underscores.</span></span> <span data-ttu-id="6ee01-520">Pokud `stdoutLogFile` cesta končí *stdout*, má název souboru protokolu pro aplikaci s PID 1934 vytvořili 5 2. 2018 v 19:42:32 *stdout_20180205194132_1934.log*.</span><span class="sxs-lookup"><span data-stu-id="6ee01-520">If the `stdoutLogFile` path ends with *stdout*, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name *stdout_20180205194132_1934.log*.</span></span>

<span data-ttu-id="6ee01-521">Pokud `stdoutLogEnabled` má hodnotu false, chyby, ke kterým dochází při spuštění aplikace se zachytí a do protokolu událostí, protože ho až 30 KB.</span><span class="sxs-lookup"><span data-stu-id="6ee01-521">If `stdoutLogEnabled` is false, errors that occur on app startup are captured and emitted to the event log up to 30 KB.</span></span> <span data-ttu-id="6ee01-522">Po spuštění se zahodí všechny další protokoly.</span><span class="sxs-lookup"><span data-stu-id="6ee01-522">After startup, all additional logs are discarded.</span></span>

<span data-ttu-id="6ee01-523">Následující ukázka `aspNetCore` element konfiguruje stdout protokolování pro aplikace hostované ve službě Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="6ee01-523">The following sample `aspNetCore` element configures stdout logging for an app hosted in Azure App Service.</span></span> <span data-ttu-id="6ee01-524">Místní cesta nebo cesta ke sdílené položce je přijatelné pro místní přihlášení.</span><span class="sxs-lookup"><span data-stu-id="6ee01-524">A local path or network share path is acceptable for local logging.</span></span> <span data-ttu-id="6ee01-525">Ověřte, jestli uživatel identita fondu aplikací má oprávnění k zápisu do zadaná výstupní cesta.</span><span class="sxs-lookup"><span data-stu-id="6ee01-525">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="InProcess">
</aspNetCore>
```

## <a name="enhanced-diagnostic-logs"></a><span data-ttu-id="6ee01-526">Rozšířené diagnostické protokoly</span><span class="sxs-lookup"><span data-stu-id="6ee01-526">Enhanced diagnostic logs</span></span>

<span data-ttu-id="6ee01-527">Modul ASP.NET Core lze nakonfigurovat tak, aby poskytoval rozšířené diagnostické protokoly.</span><span class="sxs-lookup"><span data-stu-id="6ee01-527">The ASP.NET Core Module is configurable to provide enhanced diagnostics logs.</span></span> <span data-ttu-id="6ee01-528">Přidat `<handlerSettings>` elementu `<aspNetCore>` prvek *web.config*. Nastavení `debugLevel` k `TRACE` zpřístupňuje větší věrnost diagnostické informace:</span><span class="sxs-lookup"><span data-stu-id="6ee01-528">Add the `<handlerSettings>` element to the `<aspNetCore>` element in *web.config*. Setting the `debugLevel` to `TRACE` exposes a higher fidelity of diagnostic information:</span></span>

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

<span data-ttu-id="6ee01-529">Složky v cestě poskytnuté `<handlerSetting>` hodnotě (*protokoly* v předchozím příkladu) nejsou vytvářeny modulem automaticky a měly by být v nasazení předběžně existující.</span><span class="sxs-lookup"><span data-stu-id="6ee01-529">Folders in the path provided to the `<handlerSetting>` value (*logs* in the preceding example) aren't created by the module automatically and should pre-exist in the deployment.</span></span> <span data-ttu-id="6ee01-530">Fond aplikací musí mít oprávnění k zápisu do umístění, ve kterém jsou zapsány protokoly (použijte `IIS AppPool\<app_pool_name>` poskytnout oprávnění k zápisu).</span><span class="sxs-lookup"><span data-stu-id="6ee01-530">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="6ee01-531">Ladit úrovně (`debugLevel`) hodnoty může obsahovat úroveň a umístění.</span><span class="sxs-lookup"><span data-stu-id="6ee01-531">Debug level (`debugLevel`) values can include both the level and the location.</span></span>

<span data-ttu-id="6ee01-532">Úrovně (v pořadí od nejméně na nejpodrobnější):</span><span class="sxs-lookup"><span data-stu-id="6ee01-532">Levels (in order from least to most verbose):</span></span>

* <span data-ttu-id="6ee01-533">CHYBA</span><span class="sxs-lookup"><span data-stu-id="6ee01-533">ERROR</span></span>
* <span data-ttu-id="6ee01-534">UPOZORNĚNÍ</span><span class="sxs-lookup"><span data-stu-id="6ee01-534">WARNING</span></span>
* <span data-ttu-id="6ee01-535">INFORMACE O</span><span class="sxs-lookup"><span data-stu-id="6ee01-535">INFO</span></span>
* <span data-ttu-id="6ee01-536">TRACE</span><span class="sxs-lookup"><span data-stu-id="6ee01-536">TRACE</span></span>

<span data-ttu-id="6ee01-537">Umístění (umístění více jsou povoleny):</span><span class="sxs-lookup"><span data-stu-id="6ee01-537">Locations (multiple locations are permitted):</span></span>

* <span data-ttu-id="6ee01-538">KONZOLY</span><span class="sxs-lookup"><span data-stu-id="6ee01-538">CONSOLE</span></span>
* <span data-ttu-id="6ee01-539">PROTOKOL UDÁLOSTÍ</span><span class="sxs-lookup"><span data-stu-id="6ee01-539">EVENTLOG</span></span>
* <span data-ttu-id="6ee01-540">SOUBOR</span><span class="sxs-lookup"><span data-stu-id="6ee01-540">FILE</span></span>

<span data-ttu-id="6ee01-541">Obslužná rutina nastavení se dá zadat i prostřednictvím proměnné prostředí:</span><span class="sxs-lookup"><span data-stu-id="6ee01-541">The handler settings can also be provided via environment variables:</span></span>

* <span data-ttu-id="6ee01-542">`ASPNETCORE_MODULE_DEBUG_FILE` &ndash; Cesta k souboru protokolu ladění.</span><span class="sxs-lookup"><span data-stu-id="6ee01-542">`ASPNETCORE_MODULE_DEBUG_FILE` &ndash; Path to the debug log file.</span></span> <span data-ttu-id="6ee01-543">(Výchozí: *aspnetcore-debug.log*)</span><span class="sxs-lookup"><span data-stu-id="6ee01-543">(Default: *aspnetcore-debug.log*)</span></span>
* <span data-ttu-id="6ee01-544">`ASPNETCORE_MODULE_DEBUG` &ndash; Ladit nastavení úrovně.</span><span class="sxs-lookup"><span data-stu-id="6ee01-544">`ASPNETCORE_MODULE_DEBUG` &ndash; Debug level setting.</span></span>

> [!WARNING]
> <span data-ttu-id="6ee01-545">Proveďte **není** protokolování ladění povoleno v nasazení pro déle, než se požaduje chcete vyřešit nějaký problém.</span><span class="sxs-lookup"><span data-stu-id="6ee01-545">Do **not** leave debug logging enabled in the deployment for longer than required to troubleshoot an issue.</span></span> <span data-ttu-id="6ee01-546">Velikost souboru protokolu není omezený.</span><span class="sxs-lookup"><span data-stu-id="6ee01-546">The size of the log isn't limited.</span></span> <span data-ttu-id="6ee01-547">Opuštění protokol ladění povoleno může vyčerpat dostupné místo na disku a chybách u aplikací na serveru nebo služby app service.</span><span class="sxs-lookup"><span data-stu-id="6ee01-547">Leaving the debug log enabled can exhaust the available disk space and crash the server or app service.</span></span>

<span data-ttu-id="6ee01-548">Naleznete v tématu [konfigurace pomocí souboru web.config](#configuration-with-webconfig) příklad `aspNetCore` prvek *web.config* souboru.</span><span class="sxs-lookup"><span data-stu-id="6ee01-548">See [Configuration with web.config](#configuration-with-webconfig) for an example of the `aspNetCore` element in the *web.config* file.</span></span>

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="6ee01-549">Konfigurace proxy serveru používá protokol HTTP a token pro párování</span><span class="sxs-lookup"><span data-stu-id="6ee01-549">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="6ee01-550">*Platí jenom pro hostování mimo proces.*</span><span class="sxs-lookup"><span data-stu-id="6ee01-550">*Only applies to out-of-process hosting.*</span></span>

<span data-ttu-id="6ee01-551">Proxy server mezi modul ASP.NET Core a Kestrel používá protokol HTTP.</span><span class="sxs-lookup"><span data-stu-id="6ee01-551">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="6ee01-552">Neexistuje žádné riziko odposloucháváním provozu mezi modulu a Kestrel z umístění mimo server.</span><span class="sxs-lookup"><span data-stu-id="6ee01-552">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="6ee01-553">Párování token se používá k zajištění, že požadavků přijatých službou Kestrel byly směrovány přes proxy server službou IIS a nebyl dodán z nějakého jiného zdroje.</span><span class="sxs-lookup"><span data-stu-id="6ee01-553">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="6ee01-554">Vytvoření a nastavení do proměnné prostředí párování token (`ASPNETCORE_TOKEN`) modulu.</span><span class="sxs-lookup"><span data-stu-id="6ee01-554">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="6ee01-555">Párování token byl nastavený i do záhlaví (`MS-ASPNETCORE-TOKEN`) na všechny požadavky směrovány přes proxy server.</span><span class="sxs-lookup"><span data-stu-id="6ee01-555">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="6ee01-556">Služba IIS Middleware kontroly požadavku že přijme potvrďte, že odpovídá párování hodnota tokenu hlavičky hodnotu proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="6ee01-556">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="6ee01-557">Pokud token hodnoty se neshodují, žádost je zaznamenána a odmítnut.</span><span class="sxs-lookup"><span data-stu-id="6ee01-557">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="6ee01-558">Párování proměnná tokenu prostředí a přenos dat mezi modulu a Kestrel nejsou dostupné z umístění mimo server.</span><span class="sxs-lookup"><span data-stu-id="6ee01-558">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="6ee01-559">Nainstalovat bez mého párování hodnota tokenu nelze útočník odesílat požadavky, které obejít kontrolu v IIS middlewaru.</span><span class="sxs-lookup"><span data-stu-id="6ee01-559">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="6ee01-560">Modul ASP.NET Core s službu IIS sdílenou konfiguraci</span><span class="sxs-lookup"><span data-stu-id="6ee01-560">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="6ee01-561">Instalační služba modulu ASP.NET Core se spouští s oprávněními účtu **TrustedInstaller** .</span><span class="sxs-lookup"><span data-stu-id="6ee01-561">The ASP.NET Core Module installer runs with the privileges of the **TrustedInstaller** account.</span></span> <span data-ttu-id="6ee01-562">Vzhledem k tomu, že účet místního systému nemá oprávnění k úpravám pro cestu ke sdílené složce, kterou používá sdílená konfigurace služby IIS, vyvolá Instalační program chybu odepření přístupu při pokusu o konfiguraci nastavení modulu v souboru *ApplicationHost. config* v sdílená složka.</span><span class="sxs-lookup"><span data-stu-id="6ee01-562">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the *applicationHost.config* file on the share.</span></span>

<span data-ttu-id="6ee01-563">Pokud používáte konfiguraci IIS Shared na stejném počítači jako instalaci služby IIS, spusťte instalační program sady prostředků ASP.NET Core s `OPT_NO_SHARED_CONFIG_CHECK` parametrem nastaveným na: `1`</span><span class="sxs-lookup"><span data-stu-id="6ee01-563">When using an IIS Shared Configuration on the same machine as the IIS installation, run the ASP.NET Core Hosting Bundle installer with the `OPT_NO_SHARED_CONFIG_CHECK` parameter set to `1`:</span></span>

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

<span data-ttu-id="6ee01-564">Pokud cesta ke sdílené konfiguraci není ve stejném počítači jako instalace služby IIS, postupujte podle těchto kroků:</span><span class="sxs-lookup"><span data-stu-id="6ee01-564">When the path to the shared configuration isn't on the same machine as the IIS installation, follow these steps:</span></span>

1. <span data-ttu-id="6ee01-565">Zakážete sdílenou konfiguraci IIS.</span><span class="sxs-lookup"><span data-stu-id="6ee01-565">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="6ee01-566">Spusťte instalační program.</span><span class="sxs-lookup"><span data-stu-id="6ee01-566">Run the installer.</span></span>
1. <span data-ttu-id="6ee01-567">Export aktualizovaný *applicationHost.config* souboru do sdílené složky.</span><span class="sxs-lookup"><span data-stu-id="6ee01-567">Export the updated *applicationHost.config* file to the share.</span></span>
1. <span data-ttu-id="6ee01-568">Znovu povolte sdílenou konfiguraci IIS.</span><span class="sxs-lookup"><span data-stu-id="6ee01-568">Re-enable the IIS Shared Configuration.</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="6ee01-569">Verze modulu a hostování sady Instalační protokoly</span><span class="sxs-lookup"><span data-stu-id="6ee01-569">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="6ee01-570">Chcete-li zjistit verzi nainstalovaný modul ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="6ee01-570">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="6ee01-571">V hostitelském systému, přejděte na *%windir%\System32\inetsrv*.</span><span class="sxs-lookup"><span data-stu-id="6ee01-571">On the hosting system, navigate to *%windir%\System32\inetsrv*.</span></span>
1. <span data-ttu-id="6ee01-572">Vyhledejte *aspnetcore.dll* souboru.</span><span class="sxs-lookup"><span data-stu-id="6ee01-572">Locate the *aspnetcore.dll* file.</span></span>
1. <span data-ttu-id="6ee01-573">Klikněte pravým tlačítkem na soubor a vyberte **vlastnosti** z kontextové nabídky.</span><span class="sxs-lookup"><span data-stu-id="6ee01-573">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="6ee01-574">Vyberte **podrobnosti** kartu. **Verze souboru** a **verze produktu** představují nainstalovaná verze modulu.</span><span class="sxs-lookup"><span data-stu-id="6ee01-574">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="6ee01-575">Instalační protokoly hostování sady prostředků modulu se nacházejí v *C:\\uživatelé\\% UserName %\\AppData\\místní\\Temp*. Soubor *dd_DotNetCoreWinSvrHosting__\<časové razítko > _000_AspNetCoreModule_x64.log*.</span><span class="sxs-lookup"><span data-stu-id="6ee01-575">The Hosting Bundle installer logs for the module are found at *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. The file is named *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="6ee01-576">Modul, schéma a konfiguraci umístění souborů</span><span class="sxs-lookup"><span data-stu-id="6ee01-576">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="6ee01-577">Modul</span><span class="sxs-lookup"><span data-stu-id="6ee01-577">Module</span></span>

<span data-ttu-id="6ee01-578">**IIS (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="6ee01-578">**IIS (x86/amd64):**</span></span>

* <span data-ttu-id="6ee01-579">%windir%\System32\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="6ee01-579">%windir%\System32\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="6ee01-580">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="6ee01-580">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="6ee01-581">%ProgramFiles%\IIS\Asp.NET core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="6ee01-581">%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="6ee01-582">% ProgramFiles (x86) %\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="6ee01-582">%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

<span data-ttu-id="6ee01-583">**Služba IIS Express (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="6ee01-583">**IIS Express (x86/amd64):**</span></span>

* <span data-ttu-id="6ee01-584">%ProgramFiles%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="6ee01-584">%ProgramFiles%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="6ee01-585">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="6ee01-585">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="6ee01-586">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="6ee01-586">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="6ee01-587">% ProgramFiles (x86) %\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="6ee01-587">%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

### <a name="schema"></a><span data-ttu-id="6ee01-588">Schéma</span><span class="sxs-lookup"><span data-stu-id="6ee01-588">Schema</span></span>

<span data-ttu-id="6ee01-589">**SLUŽBA IIS**</span><span class="sxs-lookup"><span data-stu-id="6ee01-589">**IIS**</span></span>

* <span data-ttu-id="6ee01-590">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="6ee01-590">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span></span>

* <span data-ttu-id="6ee01-591">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.XML</span><span class="sxs-lookup"><span data-stu-id="6ee01-591">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span></span>

<span data-ttu-id="6ee01-592">**Služba IIS Express**</span><span class="sxs-lookup"><span data-stu-id="6ee01-592">**IIS Express**</span></span>

* <span data-ttu-id="6ee01-593">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="6ee01-593">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span></span>

* <span data-ttu-id="6ee01-594">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span><span class="sxs-lookup"><span data-stu-id="6ee01-594">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span></span>

### <a name="configuration"></a><span data-ttu-id="6ee01-595">Konfigurace</span><span class="sxs-lookup"><span data-stu-id="6ee01-595">Configuration</span></span>

<span data-ttu-id="6ee01-596">**SLUŽBA IIS**</span><span class="sxs-lookup"><span data-stu-id="6ee01-596">**IIS**</span></span>

* <span data-ttu-id="6ee01-597">%windir%\System32\inetsrv\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="6ee01-597">%windir%\System32\inetsrv\config\applicationHost.config</span></span>

<span data-ttu-id="6ee01-598">**Služba IIS Express**</span><span class="sxs-lookup"><span data-stu-id="6ee01-598">**IIS Express**</span></span>

* <span data-ttu-id="6ee01-599">Visual Studio: {root aplikace}\\. vs\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="6ee01-599">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span></span>

* <span data-ttu-id="6ee01-600">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span><span class="sxs-lookup"><span data-stu-id="6ee01-600">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span></span>

<span data-ttu-id="6ee01-601">Soubory můžete najít tak, že *aspnetcore* v *applicationHost.config* souboru.</span><span class="sxs-lookup"><span data-stu-id="6ee01-601">The files can be found by searching for *aspnetcore* in the *applicationHost.config* file.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="6ee01-602">Modul ASP.NET Core je nativním modulem služby IIS, který se připojuje k kanálu IIS a přesměrovává webové požadavky na back-endové aplikace ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="6ee01-602">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to forward web requests to backend ASP.NET Core apps.</span></span>

<span data-ttu-id="6ee01-603">Podporované verze systému Windows:</span><span class="sxs-lookup"><span data-stu-id="6ee01-603">Supported Windows versions:</span></span>

* <span data-ttu-id="6ee01-604">Windows 7 nebo novější</span><span class="sxs-lookup"><span data-stu-id="6ee01-604">Windows 7 or later</span></span>
* <span data-ttu-id="6ee01-605">Windows Server 2008 R2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="6ee01-605">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="6ee01-606">Modul funguje pouze s Kestrel.</span><span class="sxs-lookup"><span data-stu-id="6ee01-606">The module only works with Kestrel.</span></span> <span data-ttu-id="6ee01-607">Modul není kompatibilní s [http. sys](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="6ee01-607">The module is incompatible with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

<span data-ttu-id="6ee01-608">Vzhledem k tomu, že ASP.NET Core aplikace běží v procesu odděleném od pracovního procesu služby IIS, modul také zpracovává správu procesů.</span><span class="sxs-lookup"><span data-stu-id="6ee01-608">Because ASP.NET Core apps run in a process separate from the IIS worker process, the module also handles process management.</span></span> <span data-ttu-id="6ee01-609">Modul spustí proces pro aplikaci ASP.NET Core, když první požadavek dorazí a restartuje aplikaci, pokud dojde k chybě.</span><span class="sxs-lookup"><span data-stu-id="6ee01-609">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it crashes.</span></span> <span data-ttu-id="6ee01-610">To je v podstatě stejné chování jako u aplikací ASP.NET 4. x spouštěných vnitroprocesově ve službě IIS, které spravuje [Aktivační služba procesů systému Windows (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span><span class="sxs-lookup"><span data-stu-id="6ee01-610">This is essentially the same behavior as seen with ASP.NET 4.x apps that run in-process in IIS that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="6ee01-611">Následující diagram znázorňuje vztah mezi službou IIS, modulem ASP.NET Core a aplikací:</span><span class="sxs-lookup"><span data-stu-id="6ee01-611">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app:</span></span>

![Modul ASP.NET Core](aspnet-core-module/_static/ancm-outofprocess.png)

<span data-ttu-id="6ee01-613">Požadavky přicházející z webu do ovladače HTTP. sys v režimu jádra.</span><span class="sxs-lookup"><span data-stu-id="6ee01-613">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="6ee01-614">Ovladač směruje požadavky do služby IIS na konfigurovaném portu webu, obvykle 80 (HTTP) nebo 443 (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="6ee01-614">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="6ee01-615">Modul předá požadavky do Kestrel na náhodném portu pro aplikaci, což není port 80 nebo 443.</span><span class="sxs-lookup"><span data-stu-id="6ee01-615">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="6ee01-616">Modul Určuje port prostřednictvím proměnné prostředí při spuštění a [Služba IIS Integration middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) nakonfiguruje server, na kterém má naslouchat `http://localhost:{port}`.</span><span class="sxs-lookup"><span data-stu-id="6ee01-616">The module specifies the port via an environment variable at startup, and the [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) configures the server to listen on `http://localhost:{port}`.</span></span> <span data-ttu-id="6ee01-617">Budou provedeny další kontroly a požadavky, které nepocházejí z modulu, jsou odmítnuty.</span><span class="sxs-lookup"><span data-stu-id="6ee01-617">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="6ee01-618">Modul nepodporuje předávání HTTPS, takže požadavky se předávají přes protokol HTTP i v případě, že je služba IIS prostřednictvím protokolu HTTPS přijímá.</span><span class="sxs-lookup"><span data-stu-id="6ee01-618">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="6ee01-619">Po Kestrel žádosti z modulu se požadavek odešle do kanálu middlewaru ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="6ee01-619">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="6ee01-620">Kanál middlewaru zpracovává požadavek a předá ho jako `HttpContext` instanci do logiky aplikace.</span><span class="sxs-lookup"><span data-stu-id="6ee01-620">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="6ee01-621">Middleware přidaný integrací služby IIS: aktualizace schématu, vzdálené IP adresy a pathbase pro přesměrování požadavku do Kestrel.</span><span class="sxs-lookup"><span data-stu-id="6ee01-621">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="6ee01-622">Odpověď aplikace se předává zpátky do služby IIS, která ji přenáší zpátky do klienta HTTP, který žádost inicioval.</span><span class="sxs-lookup"><span data-stu-id="6ee01-622">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="6ee01-623">Mnoho nativních modulů, jako je ověřování systému Windows, zůstane aktivní.</span><span class="sxs-lookup"><span data-stu-id="6ee01-623">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="6ee01-624">Další informace o aktivních modulech služby IIS pomocí modulu ASP.NET Core najdete v <xref:host-and-deploy/iis/modules>tématu.</span><span class="sxs-lookup"><span data-stu-id="6ee01-624">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="6ee01-625">Modul ASP.NET Core může také:</span><span class="sxs-lookup"><span data-stu-id="6ee01-625">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="6ee01-626">Nastavte proměnné prostředí pro pracovní proces.</span><span class="sxs-lookup"><span data-stu-id="6ee01-626">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="6ee01-627">Protokoluje výstup stdout do úložiště souborů pro řešení problémů při spuštění.</span><span class="sxs-lookup"><span data-stu-id="6ee01-627">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="6ee01-628">Předejte tokeny ověřování systému Windows.</span><span class="sxs-lookup"><span data-stu-id="6ee01-628">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="6ee01-629">Jak nainstalovat a používat modul ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6ee01-629">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="6ee01-630">Pokyny k instalaci modulu ASP.NET Core najdete v tématu [instalace hostující sady .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span><span class="sxs-lookup"><span data-stu-id="6ee01-630">For instructions on how to install the ASP.NET Core Module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="6ee01-631">Konfigurace pomocí souboru web.config</span><span class="sxs-lookup"><span data-stu-id="6ee01-631">Configuration with web.config</span></span>

<span data-ttu-id="6ee01-632">Modul ASP.NET Core, nastavena `aspNetCore` část `system.webServer` uzlu na webu *web.config* souboru.</span><span class="sxs-lookup"><span data-stu-id="6ee01-632">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="6ee01-633">Následující *web.config* soubor je publikován pro [nasazení závisí na architektuře](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) a konfiguruje modul ASP.NET Core pro zpracování požadavků lokality:</span><span class="sxs-lookup"><span data-stu-id="6ee01-633">The following *web.config* file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

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

<span data-ttu-id="6ee01-634">Následující *web.config* je publikována pro [samostatná nasazení](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span><span class="sxs-lookup"><span data-stu-id="6ee01-634">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

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

<span data-ttu-id="6ee01-635">Když je aplikace nasazená na [služby Azure App Service](https://azure.microsoft.com/services/app-service/), `stdoutLogFile` je nastavena cesta `\\?\%home%\LogFiles\stdout`.</span><span class="sxs-lookup"><span data-stu-id="6ee01-635">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="6ee01-636">Protokoly stdout a uloží ji *LogFiles* složce, která je na místě automaticky vytvořené službou.</span><span class="sxs-lookup"><span data-stu-id="6ee01-636">The path saves stdout logs to the *LogFiles* folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="6ee01-637">Informace o konfiguraci dílčí aplikace služby IIS, naleznete v tématu <xref:host-and-deploy/iis/index#sub-applications>.</span><span class="sxs-lookup"><span data-stu-id="6ee01-637">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="6ee01-638">Atributy elementu aspNetCore</span><span class="sxs-lookup"><span data-stu-id="6ee01-638">Attributes of the aspNetCore element</span></span>

| <span data-ttu-id="6ee01-639">Atribut</span><span class="sxs-lookup"><span data-stu-id="6ee01-639">Attribute</span></span> | <span data-ttu-id="6ee01-640">Popis</span><span class="sxs-lookup"><span data-stu-id="6ee01-640">Description</span></span> | <span data-ttu-id="6ee01-641">Výchozí</span><span class="sxs-lookup"><span data-stu-id="6ee01-641">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="6ee01-642">Volitelný atribut řetězce.</span><span class="sxs-lookup"><span data-stu-id="6ee01-642">Optional string attribute.</span></span></p><p><span data-ttu-id="6ee01-643">Argumenty pro spustitelný soubor určený v **processPath**.</span><span class="sxs-lookup"><span data-stu-id="6ee01-643">Arguments to the executable specified in **processPath**.</span></span></p>| |
| `disableStartUpErrorPage` | <p><span data-ttu-id="6ee01-644">Volitelný logický atribut.</span><span class="sxs-lookup"><span data-stu-id="6ee01-644">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="6ee01-645">Při hodnotě true **502.5 – selhání procesu** Potlačené stránky a znakovou stránku 502 stav nakonfigurované v *web.config* má přednost před.</span><span class="sxs-lookup"><span data-stu-id="6ee01-645">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="6ee01-646">Volitelný logický atribut.</span><span class="sxs-lookup"><span data-stu-id="6ee01-646">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="6ee01-647">Při hodnotě true se token předá podřízený proces naslouchání na ASPNETCORE_PORT % jako záhlaví "MS-ASPNETCORE-WINAUTHTOKEN" každý požadavek.</span><span class="sxs-lookup"><span data-stu-id="6ee01-647">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="6ee01-648">Je odpovědností tento proces pro volání CloseHandle na tento token každý požadavek.</span><span class="sxs-lookup"><span data-stu-id="6ee01-648">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `processesPerApplication` | <p><span data-ttu-id="6ee01-649">Volitelný celočíselný atribut.</span><span class="sxs-lookup"><span data-stu-id="6ee01-649">Optional integer attribute.</span></span></p><p><span data-ttu-id="6ee01-650">Určuje počet instancí procesu zadaný v **processPath** nastavení, které lze se nespředený nahoru na app.</span><span class="sxs-lookup"><span data-stu-id="6ee01-650">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="6ee01-651">Nastavení `processesPerApplication` se nedoporučuje.</span><span class="sxs-lookup"><span data-stu-id="6ee01-651">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="6ee01-652">Tento atribut bude v budoucí verzi odebrán.</span><span class="sxs-lookup"><span data-stu-id="6ee01-652">This attribute will be removed in a future release.</span></span></p> | <span data-ttu-id="6ee01-653">Výchozí hodnota: `1`</span><span class="sxs-lookup"><span data-stu-id="6ee01-653">Default: `1`</span></span><br><span data-ttu-id="6ee01-654">Min: `1`</span><span class="sxs-lookup"><span data-stu-id="6ee01-654">Min: `1`</span></span><br><span data-ttu-id="6ee01-655">Max: `100`</span><span class="sxs-lookup"><span data-stu-id="6ee01-655">Max: `100`</span></span> |
| `processPath` | <p><span data-ttu-id="6ee01-656">Požadovaný atribut typu string.</span><span class="sxs-lookup"><span data-stu-id="6ee01-656">Required string attribute.</span></span></p><p><span data-ttu-id="6ee01-657">Cesta ke spustitelnému souboru, který spustí nějaký proces naslouchání požadavků protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="6ee01-657">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="6ee01-658">Jsou podporovány relativní cesty.</span><span class="sxs-lookup"><span data-stu-id="6ee01-658">Relative paths are supported.</span></span> <span data-ttu-id="6ee01-659">Pokud cestu začíná `.`, cesta se považuje za kořeni webu.</span><span class="sxs-lookup"><span data-stu-id="6ee01-659">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="6ee01-660">Volitelný celočíselný atribut.</span><span class="sxs-lookup"><span data-stu-id="6ee01-660">Optional integer attribute.</span></span></p><p><span data-ttu-id="6ee01-661">Určuje, kolikrát podle procesu **processPath** může při selhání za minutu.</span><span class="sxs-lookup"><span data-stu-id="6ee01-661">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="6ee01-662">Pokud je tento limit překročen, modulu zastaví spuštění procesu pro zbytek minuty.</span><span class="sxs-lookup"><span data-stu-id="6ee01-662">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p> | <span data-ttu-id="6ee01-663">Výchozí hodnota: `10`</span><span class="sxs-lookup"><span data-stu-id="6ee01-663">Default: `10`</span></span><br><span data-ttu-id="6ee01-664">Min: `0`</span><span class="sxs-lookup"><span data-stu-id="6ee01-664">Min: `0`</span></span><br><span data-ttu-id="6ee01-665">Max: `100`</span><span class="sxs-lookup"><span data-stu-id="6ee01-665">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="6ee01-666">Atribut volitelný časový interval.</span><span class="sxs-lookup"><span data-stu-id="6ee01-666">Optional timespan attribute.</span></span></p><p><span data-ttu-id="6ee01-667">Určuje dobu, pro kterou modul ASP.NET Core čeká na odpověď z procesu naslouchání na ASPNETCORE_PORT %.</span><span class="sxs-lookup"><span data-stu-id="6ee01-667">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="6ee01-668">Ve verzích modul ASP.NET Core dodávané s verzí technologie ASP.NET Core 2.1 nebo novější `requestTimeout` je zadán v hodiny, minuty a sekundy.</span><span class="sxs-lookup"><span data-stu-id="6ee01-668">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p> | <span data-ttu-id="6ee01-669">Výchozí hodnota: `00:02:00`</span><span class="sxs-lookup"><span data-stu-id="6ee01-669">Default: `00:02:00`</span></span><br><span data-ttu-id="6ee01-670">Min: `00:00:00`</span><span class="sxs-lookup"><span data-stu-id="6ee01-670">Min: `00:00:00`</span></span><br><span data-ttu-id="6ee01-671">Max: `360:00:00`</span><span class="sxs-lookup"><span data-stu-id="6ee01-671">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="6ee01-672">Volitelný celočíselný atribut.</span><span class="sxs-lookup"><span data-stu-id="6ee01-672">Optional integer attribute.</span></span></p><p><span data-ttu-id="6ee01-673">Doba trvání v sekundách, které modul čeká na spustitelný soubor na řádné vypnutí při *app_offline.htm* je detekován soubor.</span><span class="sxs-lookup"><span data-stu-id="6ee01-673">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="6ee01-674">Výchozí hodnota: `10`</span><span class="sxs-lookup"><span data-stu-id="6ee01-674">Default: `10`</span></span><br><span data-ttu-id="6ee01-675">Min: `0`</span><span class="sxs-lookup"><span data-stu-id="6ee01-675">Min: `0`</span></span><br><span data-ttu-id="6ee01-676">Max: `600`</span><span class="sxs-lookup"><span data-stu-id="6ee01-676">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="6ee01-677">Volitelný celočíselný atribut.</span><span class="sxs-lookup"><span data-stu-id="6ee01-677">Optional integer attribute.</span></span></p><p><span data-ttu-id="6ee01-678">Doba trvání v sekundách, které modul čeká na spustitelný soubor ke spuštění procesu naslouchání na portu.</span><span class="sxs-lookup"><span data-stu-id="6ee01-678">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="6ee01-679">Pokud je překročen časový limit, modul ukončí proces.</span><span class="sxs-lookup"><span data-stu-id="6ee01-679">If this time limit is exceeded, the module kills the process.</span></span> <span data-ttu-id="6ee01-680">Modul se pokusí znovu spustit proces, když ji dostane novou žádost a pokusit se restartovat proces na dalších příchozích požadavků, pokud se aplikaci nepodaří spustit **rapidFailsPerMinute** málokdy za posledních minuta se zajištěním provozu.</span><span class="sxs-lookup"><span data-stu-id="6ee01-680">The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="6ee01-681">Hodnota 0 (nula) je **ne** za neomezený časový limit.</span><span class="sxs-lookup"><span data-stu-id="6ee01-681">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="6ee01-682">Výchozí hodnota: `120`</span><span class="sxs-lookup"><span data-stu-id="6ee01-682">Default: `120`</span></span><br><span data-ttu-id="6ee01-683">Min: `0`</span><span class="sxs-lookup"><span data-stu-id="6ee01-683">Min: `0`</span></span><br><span data-ttu-id="6ee01-684">Max: `3600`</span><span class="sxs-lookup"><span data-stu-id="6ee01-684">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="6ee01-685">Volitelný logický atribut.</span><span class="sxs-lookup"><span data-stu-id="6ee01-685">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="6ee01-686">Při hodnotě true se **stdout** a **stderr** pro proces určený v **processPath** se přesměrují do souboru zadaného v **stdoutLogFile**.</span><span class="sxs-lookup"><span data-stu-id="6ee01-686">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="6ee01-687">Volitelný atribut řetězce.</span><span class="sxs-lookup"><span data-stu-id="6ee01-687">Optional string attribute.</span></span></p><p><span data-ttu-id="6ee01-688">Určuje relativní nebo absolutní cestu, pro kterou **stdout** a **stderr** z procesu podle **processPath** přihlášeni.</span><span class="sxs-lookup"><span data-stu-id="6ee01-688">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="6ee01-689">Relativní cesty jsou relativní vzhledem k kořen webu.</span><span class="sxs-lookup"><span data-stu-id="6ee01-689">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="6ee01-690">Libovolnou cestu od `.` jsou relativní vzhledem k webu kořenové a všechny ostatní cesty jsou považovány za absolutní cesty.</span><span class="sxs-lookup"><span data-stu-id="6ee01-690">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="6ee01-691">Všechny složky v cestě k dispozici musí existovat v pořadí pro modul pro vytvoření souboru protokolu.</span><span class="sxs-lookup"><span data-stu-id="6ee01-691">Any folders provided in the path must exist in order for the module to create the log file.</span></span> <span data-ttu-id="6ee01-692">Pomocí oddělovače podtržítko, časové razítko, ID procesu a přípona souboru ( *.log*) jsou přidány na poslední segment **stdoutLogFile** cestu.</span><span class="sxs-lookup"><span data-stu-id="6ee01-692">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="6ee01-693">Pokud `.\logs\stdout` je zadaný jako hodnota, je uložen protokolu příklad stdout jako *stdout_20180205194132_1934.log* v *protokoly* složky při uložení 5 2. 2018 v 19:41:32 s ID procesu sady 1934.</span><span class="sxs-lookup"><span data-stu-id="6ee01-693">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

### <a name="setting-environment-variables"></a><span data-ttu-id="6ee01-694">Nastavení proměnných prostředí</span><span class="sxs-lookup"><span data-stu-id="6ee01-694">Setting environment variables</span></span>

<span data-ttu-id="6ee01-695">Proměnné prostředí se dá nastavit pro proces v `processPath` atribut.</span><span class="sxs-lookup"><span data-stu-id="6ee01-695">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="6ee01-696">Zadat proměnné prostředí s `<environmentVariable>` podřízený prvek `<environmentVariables>` prvek kolekce.</span><span class="sxs-lookup"><span data-stu-id="6ee01-696">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span>

> [!WARNING]
> <span data-ttu-id="6ee01-697">Proměnné prostředí nastavené v této části jsou v konfliktu se sadou proměnných prostředí systému se stejným názvem.</span><span class="sxs-lookup"><span data-stu-id="6ee01-697">Environment variables set in this section conflict with system environment variables set with the same name.</span></span> <span data-ttu-id="6ee01-698">Pokud je proměnná prostředí nastavena v souboru *Web. config* i na úrovni systému systému Windows, bude hodnota ze souboru *Web. config* připojena k hodnotě proměnné prostředí systému (například `ASPNETCORE_ENVIRONMENT: Development;Development`), což zabrání aplikaci. od začátku.</span><span class="sxs-lookup"><span data-stu-id="6ee01-698">If an environment variable is set in both the *web.config* file and at the system level in Windows, the value from the *web.config* file becomes appended to the system environment variable value (for example, `ASPNETCORE_ENVIRONMENT: Development;Development`), which prevents the app from starting.</span></span>

<span data-ttu-id="6ee01-699">Následující příklad nastaví dvou proměnných prostředí.</span><span class="sxs-lookup"><span data-stu-id="6ee01-699">The following example sets two environment variables.</span></span> <span data-ttu-id="6ee01-700">`ASPNETCORE_ENVIRONMENT` nakonfiguruje prostředí aplikace tak, aby `Development`.</span><span class="sxs-lookup"><span data-stu-id="6ee01-700">`ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="6ee01-701">Vývojáři mohou dočasně nastaví tuto hodnotu *web.config* souboru, aby bylo možné vynutit [stránku výjimek pro vývojáře](xref:fundamentals/error-handling) načíst při ladění aplikace výjimky.</span><span class="sxs-lookup"><span data-stu-id="6ee01-701">A developer may temporarily set this value in the *web.config* file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="6ee01-702">`CONFIG_DIR` je příkladem proměnné prostředí, kam má vývojář zapisovat kód, který čte hodnoty při spuštění tvoří cestu pro načtení konfiguračního souboru aplikace.</span><span class="sxs-lookup"><span data-stu-id="6ee01-702">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

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
> <span data-ttu-id="6ee01-703">Nastavit pouze `ASPNETCORE_ENVIRONMENT` proměnnou prostředí, aby `Development` na přípravy a testování serverů, které nejsou dostupné k nedůvěryhodným sítím, jako je Internet.</span><span class="sxs-lookup"><span data-stu-id="6ee01-703">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## <a name="app_offlinehtm"></a><span data-ttu-id="6ee01-704">app_offline.htm</span><span class="sxs-lookup"><span data-stu-id="6ee01-704">app_offline.htm</span></span>

<span data-ttu-id="6ee01-705">Pokud soubor s názvem *app_offline.htm* je zjištěna v kořenovém adresáři aplikace, že modul ASP.NET Core se pokusí řádné vypnutí aplikace a zastavit zpracování příchozích požadavků.</span><span class="sxs-lookup"><span data-stu-id="6ee01-705">If a file with the name *app_offline.htm* is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="6ee01-706">Pokud aplikace pořád běží po dobu v sekundách podle `shutdownTimeLimit`, že modul ASP.NET Core ukončuje spuštěnému procesu.</span><span class="sxs-lookup"><span data-stu-id="6ee01-706">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="6ee01-707">Zatímco *app_offline.htm* soubor je k dispozici, že modul ASP.NET Core reaguje na požadavky odesílá zpět obsah *app_offline.htm* souboru.</span><span class="sxs-lookup"><span data-stu-id="6ee01-707">While the *app_offline.htm* file is present, the ASP.NET Core Module responds to requests by sending back the contents of the *app_offline.htm* file.</span></span> <span data-ttu-id="6ee01-708">Když *app_offline.htm* soubor bude odstraněn, příští žádosti o spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="6ee01-708">When the *app_offline.htm* file is removed, the next request starts the app.</span></span>

## <a name="start-up-error-page"></a><span data-ttu-id="6ee01-709">Spuštění chybovou stránku</span><span class="sxs-lookup"><span data-stu-id="6ee01-709">Start-up error page</span></span>

<span data-ttu-id="6ee01-710">Pokud se nespustí back-endový proces nebo back-endový proces spustí ale nebude moci poslouchat na konfigurovaném portu, že modul ASP.NET Core *502.5 – selhání procesu* se zobrazí stav znakovou stránku.</span><span class="sxs-lookup"><span data-stu-id="6ee01-710">If the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span> <span data-ttu-id="6ee01-711">Chcete-li potlačit tuto stránku a vrátit se na výchozí stav služby IIS 502 znakovou stránku, použijte `disableStartUpErrorPage` atribut.</span><span class="sxs-lookup"><span data-stu-id="6ee01-711">To suppress this page and revert to the default IIS 502 status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="6ee01-712">Další informace o konfiguraci vlastních chybových zpráv, najdete v části [chyby protokolu HTTP \<httpErrors >](/iis/configuration/system.webServer/httpErrors/).</span><span class="sxs-lookup"><span data-stu-id="6ee01-712">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

![502.5 proces selhání stav znaková stránka](aspnet-core-module/_static/ANCM-502_5.png)

## <a name="log-creation-and-redirection"></a><span data-ttu-id="6ee01-714">Vytvoření protokolu a přesměrování</span><span class="sxs-lookup"><span data-stu-id="6ee01-714">Log creation and redirection</span></span>

<span data-ttu-id="6ee01-715">Modul ASP.NET Core přesměruje výstup stdout a stderr console na disk, pokud `stdoutLogEnabled` a `stdoutLogFile` atributy `aspNetCore` element jsou nastavené.</span><span class="sxs-lookup"><span data-stu-id="6ee01-715">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="6ee01-716">Při vytvoření souboru protokolu `stdoutLogFile` jsou všechny složky v této cestě vytvářeny modulem.</span><span class="sxs-lookup"><span data-stu-id="6ee01-716">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="6ee01-717">Fond aplikací musí mít oprávnění k zápisu do umístění, ve kterém jsou zapsány protokoly (použijte `IIS AppPool\<app_pool_name>` poskytnout oprávnění k zápisu).</span><span class="sxs-lookup"><span data-stu-id="6ee01-717">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="6ee01-718">Protokoly nejsou střídán, pokud dojde k recyklování procesů/restartování.</span><span class="sxs-lookup"><span data-stu-id="6ee01-718">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="6ee01-719">Je odpovědností hostitel k omezení místa na disku, které využijete v protokolech.</span><span class="sxs-lookup"><span data-stu-id="6ee01-719">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="6ee01-720">Použití protokolu stdout se doporučuje jenom pro řešení potíží při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="6ee01-720">Using the stdout log is only recommended for troubleshooting app startup issues.</span></span> <span data-ttu-id="6ee01-721">Nepoužívejte stdout protokolu pro účely protokolování obecné aplikace.</span><span class="sxs-lookup"><span data-stu-id="6ee01-721">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="6ee01-722">Pro rutiny protokolování v aplikaci ASP.NET Core, použijte protokolování knihovnu, která omezuje velikost souboru protokolu a otočí protokoly.</span><span class="sxs-lookup"><span data-stu-id="6ee01-722">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="6ee01-723">Další informace najdete v tématu [zprostředkovatele přihlášení třetí strany](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="6ee01-723">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="6ee01-724">Časové razítko a soubor rozšíření jsou přidány automaticky při vytvoření souboru protokolu.</span><span class="sxs-lookup"><span data-stu-id="6ee01-724">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="6ee01-725">Název souboru protokolu se skládá připojením časové razítko, ID procesu a přípona souboru ( *.log*) na poslední segment `stdoutLogFile` cestu (obvykle *stdout*) oddělené podtržítka.</span><span class="sxs-lookup"><span data-stu-id="6ee01-725">The log file name is composed by appending the timestamp, process ID, and file extension (*.log*) to the last segment of the `stdoutLogFile` path (typically *stdout*) delimited by underscores.</span></span> <span data-ttu-id="6ee01-726">Pokud `stdoutLogFile` cesta končí *stdout*, má název souboru protokolu pro aplikaci s PID 1934 vytvořili 5 2. 2018 v 19:42:32 *stdout_20180205194132_1934.log*.</span><span class="sxs-lookup"><span data-stu-id="6ee01-726">If the `stdoutLogFile` path ends with *stdout*, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name *stdout_20180205194132_1934.log*.</span></span>

<span data-ttu-id="6ee01-727">Následující ukázka `aspNetCore` element konfiguruje stdout protokolování pro aplikace hostované ve službě Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="6ee01-727">The following sample `aspNetCore` element configures stdout logging for an app hosted in Azure App Service.</span></span> <span data-ttu-id="6ee01-728">Místní cesta nebo cesta ke sdílené položce je přijatelné pro místní přihlášení.</span><span class="sxs-lookup"><span data-stu-id="6ee01-728">A local path or network share path is acceptable for local logging.</span></span> <span data-ttu-id="6ee01-729">Ověřte, jestli uživatel identita fondu aplikací má oprávnění k zápisu do zadaná výstupní cesta.</span><span class="sxs-lookup"><span data-stu-id="6ee01-729">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile="\\?\%home%\LogFiles\stdout">
</aspNetCore>
```

<span data-ttu-id="6ee01-730">Složky v cestě poskytnuté `<handlerSetting>` hodnotě (*protokoly* v předchozím příkladu) nejsou vytvářeny modulem automaticky a měly by být v nasazení předběžně existující.</span><span class="sxs-lookup"><span data-stu-id="6ee01-730">Folders in the path provided to the `<handlerSetting>` value (*logs* in the preceding example) aren't created by the module automatically and should pre-exist in the deployment.</span></span> <span data-ttu-id="6ee01-731">Fond aplikací musí mít oprávnění k zápisu do umístění, ve kterém jsou zapsány protokoly (použijte `IIS AppPool\<app_pool_name>` poskytnout oprávnění k zápisu).</span><span class="sxs-lookup"><span data-stu-id="6ee01-731">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="6ee01-732">Naleznete v tématu [konfigurace pomocí souboru web.config](#configuration-with-webconfig) příklad `aspNetCore` prvek *web.config* souboru.</span><span class="sxs-lookup"><span data-stu-id="6ee01-732">See [Configuration with web.config](#configuration-with-webconfig) for an example of the `aspNetCore` element in the *web.config* file.</span></span>

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="6ee01-733">Konfigurace proxy serveru používá protokol HTTP a token pro párování</span><span class="sxs-lookup"><span data-stu-id="6ee01-733">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="6ee01-734">Proxy server mezi modul ASP.NET Core a Kestrel používá protokol HTTP.</span><span class="sxs-lookup"><span data-stu-id="6ee01-734">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="6ee01-735">Neexistuje žádné riziko odposloucháváním provozu mezi modulu a Kestrel z umístění mimo server.</span><span class="sxs-lookup"><span data-stu-id="6ee01-735">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="6ee01-736">Párování token se používá k zajištění, že požadavků přijatých službou Kestrel byly směrovány přes proxy server službou IIS a nebyl dodán z nějakého jiného zdroje.</span><span class="sxs-lookup"><span data-stu-id="6ee01-736">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="6ee01-737">Vytvoření a nastavení do proměnné prostředí párování token (`ASPNETCORE_TOKEN`) modulu.</span><span class="sxs-lookup"><span data-stu-id="6ee01-737">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="6ee01-738">Párování token byl nastavený i do záhlaví (`MS-ASPNETCORE-TOKEN`) na všechny požadavky směrovány přes proxy server.</span><span class="sxs-lookup"><span data-stu-id="6ee01-738">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="6ee01-739">Služba IIS Middleware kontroly požadavku že přijme potvrďte, že odpovídá párování hodnota tokenu hlavičky hodnotu proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="6ee01-739">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="6ee01-740">Pokud token hodnoty se neshodují, žádost je zaznamenána a odmítnut.</span><span class="sxs-lookup"><span data-stu-id="6ee01-740">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="6ee01-741">Párování proměnná tokenu prostředí a přenos dat mezi modulu a Kestrel nejsou dostupné z umístění mimo server.</span><span class="sxs-lookup"><span data-stu-id="6ee01-741">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="6ee01-742">Nainstalovat bez mého párování hodnota tokenu nelze útočník odesílat požadavky, které obejít kontrolu v IIS middlewaru.</span><span class="sxs-lookup"><span data-stu-id="6ee01-742">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="6ee01-743">Modul ASP.NET Core s službu IIS sdílenou konfiguraci</span><span class="sxs-lookup"><span data-stu-id="6ee01-743">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="6ee01-744">Instalační služba modulu ASP.NET Core se spouští s oprávněními účtu **TrustedInstaller** .</span><span class="sxs-lookup"><span data-stu-id="6ee01-744">The ASP.NET Core Module installer runs with the privileges of the **TrustedInstaller** account.</span></span> <span data-ttu-id="6ee01-745">Vzhledem k tomu, že účet místního systému nemá oprávnění k úpravám pro cestu ke sdílené složce, kterou používá sdílená konfigurace služby IIS, vyvolá Instalační program chybu odepření přístupu při pokusu o konfiguraci nastavení modulu v souboru *ApplicationHost. config* v sdílená složka.</span><span class="sxs-lookup"><span data-stu-id="6ee01-745">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the *applicationHost.config* file on the share.</span></span>

<span data-ttu-id="6ee01-746">Pokud používáte sdílenou konfiguraci IIS, postupujte podle těchto kroků:</span><span class="sxs-lookup"><span data-stu-id="6ee01-746">When using an IIS Shared Configuration, follow these steps:</span></span>

1. <span data-ttu-id="6ee01-747">Zakážete sdílenou konfiguraci IIS.</span><span class="sxs-lookup"><span data-stu-id="6ee01-747">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="6ee01-748">Spusťte instalační program.</span><span class="sxs-lookup"><span data-stu-id="6ee01-748">Run the installer.</span></span>
1. <span data-ttu-id="6ee01-749">Export aktualizovaný *applicationHost.config* souboru do sdílené složky.</span><span class="sxs-lookup"><span data-stu-id="6ee01-749">Export the updated *applicationHost.config* file to the share.</span></span>
1. <span data-ttu-id="6ee01-750">Znovu povolte sdílenou konfiguraci IIS.</span><span class="sxs-lookup"><span data-stu-id="6ee01-750">Re-enable the IIS Shared Configuration.</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="6ee01-751">Verze modulu a hostování sady Instalační protokoly</span><span class="sxs-lookup"><span data-stu-id="6ee01-751">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="6ee01-752">Chcete-li zjistit verzi nainstalovaný modul ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="6ee01-752">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="6ee01-753">V hostitelském systému, přejděte na *%windir%\System32\inetsrv*.</span><span class="sxs-lookup"><span data-stu-id="6ee01-753">On the hosting system, navigate to *%windir%\System32\inetsrv*.</span></span>
1. <span data-ttu-id="6ee01-754">Vyhledejte *aspnetcore.dll* souboru.</span><span class="sxs-lookup"><span data-stu-id="6ee01-754">Locate the *aspnetcore.dll* file.</span></span>
1. <span data-ttu-id="6ee01-755">Klikněte pravým tlačítkem na soubor a vyberte **vlastnosti** z kontextové nabídky.</span><span class="sxs-lookup"><span data-stu-id="6ee01-755">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="6ee01-756">Vyberte **podrobnosti** kartu. **Verze souboru** a **verze produktu** představují nainstalovaná verze modulu.</span><span class="sxs-lookup"><span data-stu-id="6ee01-756">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="6ee01-757">Instalační protokoly hostování sady prostředků modulu se nacházejí v *C:\\uživatelé\\% UserName %\\AppData\\místní\\Temp*. Soubor *dd_DotNetCoreWinSvrHosting__\<časové razítko > _000_AspNetCoreModule_x64.log*.</span><span class="sxs-lookup"><span data-stu-id="6ee01-757">The Hosting Bundle installer logs for the module are found at *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. The file is named *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="6ee01-758">Modul, schéma a konfiguraci umístění souborů</span><span class="sxs-lookup"><span data-stu-id="6ee01-758">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="6ee01-759">Modul</span><span class="sxs-lookup"><span data-stu-id="6ee01-759">Module</span></span>

<span data-ttu-id="6ee01-760">**IIS (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="6ee01-760">**IIS (x86/amd64):**</span></span>

* <span data-ttu-id="6ee01-761">%windir%\System32\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="6ee01-761">%windir%\System32\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="6ee01-762">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="6ee01-762">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span></span>

<span data-ttu-id="6ee01-763">**Služba IIS Express (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="6ee01-763">**IIS Express (x86/amd64):**</span></span>

* <span data-ttu-id="6ee01-764">%ProgramFiles%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="6ee01-764">%ProgramFiles%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="6ee01-765">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="6ee01-765">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span></span>

### <a name="schema"></a><span data-ttu-id="6ee01-766">Schéma</span><span class="sxs-lookup"><span data-stu-id="6ee01-766">Schema</span></span>

<span data-ttu-id="6ee01-767">**SLUŽBA IIS**</span><span class="sxs-lookup"><span data-stu-id="6ee01-767">**IIS**</span></span>

* <span data-ttu-id="6ee01-768">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="6ee01-768">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span></span>

<span data-ttu-id="6ee01-769">**Služba IIS Express**</span><span class="sxs-lookup"><span data-stu-id="6ee01-769">**IIS Express**</span></span>

* <span data-ttu-id="6ee01-770">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="6ee01-770">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span></span>

### <a name="configuration"></a><span data-ttu-id="6ee01-771">Konfigurace</span><span class="sxs-lookup"><span data-stu-id="6ee01-771">Configuration</span></span>

<span data-ttu-id="6ee01-772">**SLUŽBA IIS**</span><span class="sxs-lookup"><span data-stu-id="6ee01-772">**IIS**</span></span>

* <span data-ttu-id="6ee01-773">%windir%\System32\inetsrv\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="6ee01-773">%windir%\System32\inetsrv\config\applicationHost.config</span></span>

<span data-ttu-id="6ee01-774">**Služba IIS Express**</span><span class="sxs-lookup"><span data-stu-id="6ee01-774">**IIS Express**</span></span>

* <span data-ttu-id="6ee01-775">Visual Studio: {root aplikace}\\. vs\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="6ee01-775">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span></span>

* <span data-ttu-id="6ee01-776">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span><span class="sxs-lookup"><span data-stu-id="6ee01-776">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span></span>

<span data-ttu-id="6ee01-777">Soubory můžete najít tak, že *aspnetcore* v *applicationHost.config* souboru.</span><span class="sxs-lookup"><span data-stu-id="6ee01-777">The files can be found by searching for *aspnetcore* in the *applicationHost.config* file.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="6ee01-778">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="6ee01-778">Additional resources</span></span>

* <xref:host-and-deploy/iis/index>
* [<span data-ttu-id="6ee01-779">Úložiště GitHub modulu ASP.NET Core (odkazový zdroj)</span><span class="sxs-lookup"><span data-stu-id="6ee01-779">ASP.NET Core Module GitHub repository (reference source)</span></span>](https://github.com/aspnet/AspNetCoreModule)
* <xref:host-and-deploy/iis/modules>
