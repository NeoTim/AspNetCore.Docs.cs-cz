---
title: Modul ASP.NET Core
author: rick-anderson
description: Naučte se konfigurovat modul ASP.NET Core pro hostování ASP.NET Corech aplikací.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/aspnet-core-module
ms.openlocfilehash: 9197f8509141b30dffcc2ccc11979f8853b37d39
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2020
ms.locfileid: "88633133"
---
# <a name="aspnet-core-module"></a><span data-ttu-id="837c0-103">Modul ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="837c0-103">ASP.NET Core Module</span></span>

<span data-ttu-id="837c0-104">Díky [Dykstra](https://github.com/tdykstra), [Rick Strahl](https://github.com/RickStrahl), [Novák Rossův](https://github.com/Tratcher), [Rick Anderson](https://twitter.com/RickAndMSFT), [sourabh Shirhatti](https://twitter.com/sshirhatti)a [Justin](https://github.com/jkotalik) Kotalik</span><span class="sxs-lookup"><span data-stu-id="837c0-104">By [Tom Dykstra](https://github.com/tdykstra), [Rick Strahl](https://github.com/RickStrahl), [Chris Ross](https://github.com/Tratcher), [Rick Anderson](https://twitter.com/RickAndMSFT), [Sourabh Shirhatti](https://twitter.com/sshirhatti), and [Justin Kotalik](https://github.com/jkotalik)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="837c0-105">Modul ASP.NET Core je nativním modulem služby IIS, který se připojuje k kanálu IIS na jednu z těchto:</span><span class="sxs-lookup"><span data-stu-id="837c0-105">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to either:</span></span>

* <span data-ttu-id="837c0-106">Hostování aplikace ASP.NET Core uvnitř pracovního procesu služby IIS ( `w3wp.exe` ), který se nazývá [model hostování v rámci procesu](#in-process-hosting-model).</span><span class="sxs-lookup"><span data-stu-id="837c0-106">Host an ASP.NET Core app inside of the IIS worker process (`w3wp.exe`), called the [in-process hosting model](#in-process-hosting-model).</span></span>
* <span data-ttu-id="837c0-107">Dopředné webové požadavky do back-endu ASP.NET Core aplikace, na které běží [Server Kestrel](xref:fundamentals/servers/kestrel), se nazývá [model hostování mimo proces](#out-of-process-hosting-model).</span><span class="sxs-lookup"><span data-stu-id="837c0-107">Forward web requests to a backend ASP.NET Core app running the [Kestrel server](xref:fundamentals/servers/kestrel), called the [out-of-process hosting model](#out-of-process-hosting-model).</span></span>

<span data-ttu-id="837c0-108">Podporované verze systému Windows:</span><span class="sxs-lookup"><span data-stu-id="837c0-108">Supported Windows versions:</span></span>

* <span data-ttu-id="837c0-109">Windows 7 nebo novější</span><span class="sxs-lookup"><span data-stu-id="837c0-109">Windows 7 or later</span></span>
* <span data-ttu-id="837c0-110">Windows Server 2012 R2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="837c0-110">Windows Server 2012 R2 or later</span></span>

<span data-ttu-id="837c0-111">Při hostování v procesu používá modul implementaci vnitroprocesového serveru pro službu IIS nazvanou IIS HTTP Server ( `IISHttpServer` ).</span><span class="sxs-lookup"><span data-stu-id="837c0-111">When hosting in-process, the module uses an in-process server implementation for IIS, called IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="837c0-112">Při hostování mimo proces funguje modul pouze s Kestrel.</span><span class="sxs-lookup"><span data-stu-id="837c0-112">When hosting out-of-process, the module only works with Kestrel.</span></span> <span data-ttu-id="837c0-113">Modul nefunguje s [HTTP.sys](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="837c0-113">The module doesn't function with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

## <a name="hosting-models"></a><span data-ttu-id="837c0-114">Modely hostingu</span><span class="sxs-lookup"><span data-stu-id="837c0-114">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="837c0-115">Model hostování v procesu</span><span class="sxs-lookup"><span data-stu-id="837c0-115">In-process hosting model</span></span>

<span data-ttu-id="837c0-116">ASP.NET Core aplikace jako výchozí pro model hostování v rámci procesu.</span><span class="sxs-lookup"><span data-stu-id="837c0-116">ASP.NET Core apps default to the in-process hosting model.</span></span>

<span data-ttu-id="837c0-117">Následující vlastnosti platí při hostování v procesu:</span><span class="sxs-lookup"><span data-stu-id="837c0-117">The following characteristics apply when hosting in-process:</span></span>

* <span data-ttu-id="837c0-118">`IISHttpServer`Místo serveru [Kestrel](xref:fundamentals/servers/kestrel) se používá server HTTP služby IIS ().</span><span class="sxs-lookup"><span data-stu-id="837c0-118">IIS HTTP Server (`IISHttpServer`) is used instead of [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span> <span data-ttu-id="837c0-119">Pro vnitroprocesové [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) volání <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> :</span><span class="sxs-lookup"><span data-stu-id="837c0-119">For in-process, [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> to:</span></span>

  * <span data-ttu-id="837c0-120">Zaregistrujte `IISHttpServer` .</span><span class="sxs-lookup"><span data-stu-id="837c0-120">Register the `IISHttpServer`.</span></span>
  * <span data-ttu-id="837c0-121">Nakonfigurujte port a základní cestu, na kterých má Server naslouchat při spuštění za modulem ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="837c0-121">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
  * <span data-ttu-id="837c0-122">Nakonfigurujte hostitele tak, aby zachytával chyby při spuštění.</span><span class="sxs-lookup"><span data-stu-id="837c0-122">Configure the host to capture startup errors.</span></span>

* <span data-ttu-id="837c0-123">[Atribut RequestTimeout](#attributes-of-the-aspnetcore-element) se nevztahuje na hostování v rámci procesu.</span><span class="sxs-lookup"><span data-stu-id="837c0-123">The [requestTimeout attribute](#attributes-of-the-aspnetcore-element) doesn't apply to in-process hosting.</span></span>

* <span data-ttu-id="837c0-124">Sdílení fondu aplikací mezi aplikacemi není podporováno.</span><span class="sxs-lookup"><span data-stu-id="837c0-124">Sharing an app pool among apps isn't supported.</span></span> <span data-ttu-id="837c0-125">Použijte jeden fond aplikací na aplikaci.</span><span class="sxs-lookup"><span data-stu-id="837c0-125">Use one app pool per app.</span></span>

* <span data-ttu-id="837c0-126">Při použití [nasazení webu](/iis/publish/using-web-deploy/introduction-to-web-deploy) nebo ručního umístění [ souboruapp_offline.htm v nasazení](xref:host-and-deploy/iis/index#locked-deployment-files)se aplikace nemusí vypnout okamžitě, pokud dojde k otevřenému připojení.</span><span class="sxs-lookup"><span data-stu-id="837c0-126">When using [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) or manually placing an [app_offline.htm file in the deployment](xref:host-and-deploy/iis/index#locked-deployment-files), the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="837c0-127">Připojení k protokolu WebSocket může například zpozdit ukončení aplikace.</span><span class="sxs-lookup"><span data-stu-id="837c0-127">For example, a websocket connection may delay app shut down.</span></span>

* <span data-ttu-id="837c0-128">Architektura (bitová verze) aplikace a nainstalovaného modulu runtime (x64 nebo x86) se musí shodovat s architekturou fondu aplikací.</span><span class="sxs-lookup"><span data-stu-id="837c0-128">The architecture (bitness) of the app and installed runtime (x64 or x86) must match the architecture of the app pool.</span></span>

* <span data-ttu-id="837c0-129">Zjistila se odpojení klienta.</span><span class="sxs-lookup"><span data-stu-id="837c0-129">Client disconnects are detected.</span></span> <span data-ttu-id="837c0-130">Token zrušení [HttpContext. RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) je zrušený, když se klient odpojí.</span><span class="sxs-lookup"><span data-stu-id="837c0-130">The [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) cancellation token is cancelled when the client disconnects.</span></span>

* <span data-ttu-id="837c0-131">V ASP.NET Core 2.2.1 nebo dřívější <xref:System.IO.Directory.GetCurrentDirectory*> vrátí pracovní adresář procesu spuštěného službou IIS místo adresáře aplikace (například *C:\Windows\System32\inetsrv* pro *w3wp.exe*).</span><span class="sxs-lookup"><span data-stu-id="837c0-131">In ASP.NET Core 2.2.1 or earlier, <xref:System.IO.Directory.GetCurrentDirectory*> returns the worker directory of the process started by IIS rather than the app's directory (for example, *C:\Windows\System32\inetsrv* for *w3wp.exe*).</span></span>

  <span data-ttu-id="837c0-132">Vzorový kód, který nastaví aktuální adresář aplikace, najdete v tématu [Třída CurrentDirectoryHelpers](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/3.x/CurrentDirectoryHelpers.cs).</span><span class="sxs-lookup"><span data-stu-id="837c0-132">For sample code that sets the app's current directory, see the [CurrentDirectoryHelpers class](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/3.x/CurrentDirectoryHelpers.cs).</span></span> <span data-ttu-id="837c0-133">Zavolejte `SetCurrentDirectory` metodu.</span><span class="sxs-lookup"><span data-stu-id="837c0-133">Call the `SetCurrentDirectory` method.</span></span> <span data-ttu-id="837c0-134">Následná volání pro <xref:System.IO.Directory.GetCurrentDirectory*> poskytnutí adresáře aplikace.</span><span class="sxs-lookup"><span data-stu-id="837c0-134">Subsequent calls to <xref:System.IO.Directory.GetCurrentDirectory*> provide the app's directory.</span></span>

* <span data-ttu-id="837c0-135">Při hostování v procesu <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> není volána interně pro inicializaci uživatele.</span><span class="sxs-lookup"><span data-stu-id="837c0-135">When hosting in-process, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span> <span data-ttu-id="837c0-136">Proto <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementace používaná k transformaci deklarací po každém ověření není ve výchozím nastavení aktivována.</span><span class="sxs-lookup"><span data-stu-id="837c0-136">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="837c0-137">Při transformaci deklarací s <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementací zavolejte <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> na přidat ověřovací služby:</span><span class="sxs-lookup"><span data-stu-id="837c0-137">When transforming claims with an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation, call <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> to add authentication services:</span></span>

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
  
  * <span data-ttu-id="837c0-138">[Nasazení webového balíčku (v jednom souboru)](/aspnet/web-forms/overview/deployment/web-deployment-in-the-enterprise/deploying-web-packages) nejsou podporována.</span><span class="sxs-lookup"><span data-stu-id="837c0-138">[Web Package (single-file) deployments](/aspnet/web-forms/overview/deployment/web-deployment-in-the-enterprise/deploying-web-packages) aren't supported.</span></span>

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="837c0-139">Model hostování mimo proces</span><span class="sxs-lookup"><span data-stu-id="837c0-139">Out-of-process hosting model</span></span>

<span data-ttu-id="837c0-140">Chcete-li nakonfigurovat aplikaci pro hostování mimo procesy, nastavte hodnotu `<AspNetCoreHostingModel>` vlastnosti na `OutOfProcess` soubor projektu (*. csproj*):</span><span class="sxs-lookup"><span data-stu-id="837c0-140">To configure an app for out-of-process hosting, set the value of the `<AspNetCoreHostingModel>` property to `OutOfProcess` in the project file (*.csproj*):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="837c0-141">Hostování v rámci procesu je nastaveno na `InProcess` , což je výchozí hodnota.</span><span class="sxs-lookup"><span data-stu-id="837c0-141">In-process hosting is set with `InProcess`, which is the default value.</span></span>

<span data-ttu-id="837c0-142">Hodnota rozlišuje `<AspNetCoreHostingModel>` malá a velká písmena, takže `inprocess` a `outofprocess` jsou platné hodnoty.</span><span class="sxs-lookup"><span data-stu-id="837c0-142">The value of `<AspNetCoreHostingModel>` is case insensitive, so `inprocess` and `outofprocess` are valid values.</span></span>

<span data-ttu-id="837c0-143">[Kestrel](xref:fundamentals/servers/kestrel) Místo http serveru IIS () se používá server Kestrel `IISHttpServer` .</span><span class="sxs-lookup"><span data-stu-id="837c0-143">[Kestrel](xref:fundamentals/servers/kestrel) server is used instead of IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="837c0-144">Pro mimoprocesové [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) volání <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> do:</span><span class="sxs-lookup"><span data-stu-id="837c0-144">For out-of-process, [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> to:</span></span>

* <span data-ttu-id="837c0-145">Nakonfigurujte port a základní cestu, na kterých má Server naslouchat při spuštění za modulem ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="837c0-145">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
* <span data-ttu-id="837c0-146">Nakonfigurujte hostitele tak, aby zachytával chyby při spuštění.</span><span class="sxs-lookup"><span data-stu-id="837c0-146">Configure the host to capture startup errors.</span></span>

### <a name="hosting-model-changes"></a><span data-ttu-id="837c0-147">Změny modelu hostování</span><span class="sxs-lookup"><span data-stu-id="837c0-147">Hosting model changes</span></span>

<span data-ttu-id="837c0-148">Pokud se `hostingModel` změní nastavení v souboru *web.config* (vysvětleno v části [konfigurace s web.config](#configuration-with-webconfig) ), modul recykluje pracovní proces pro službu IIS.</span><span class="sxs-lookup"><span data-stu-id="837c0-148">If the `hostingModel` setting is changed in the *web.config* file (explained in the [Configuration with web.config](#configuration-with-webconfig) section), the module recycles the worker process for IIS.</span></span>

<span data-ttu-id="837c0-149">V případě IIS Express modul neprovádí recyklaci pracovního procesu, ale místo toho aktivuje bezproblémové vypnutí aktuálního procesu IIS Express.</span><span class="sxs-lookup"><span data-stu-id="837c0-149">For IIS Express, the module doesn't recycle the worker process but instead triggers a graceful shutdown of the current IIS Express process.</span></span> <span data-ttu-id="837c0-150">Další požadavek na aplikaci vytvoří nový proces IIS Express.</span><span class="sxs-lookup"><span data-stu-id="837c0-150">The next request to the app spawns a new IIS Express process.</span></span>

### <a name="process-name"></a><span data-ttu-id="837c0-151">Název procesu</span><span class="sxs-lookup"><span data-stu-id="837c0-151">Process name</span></span>

<span data-ttu-id="837c0-152">`Process.GetCurrentProcess().ProcessName`sestavy `w3wp` / `iisexpress` (v procesu) nebo `dotnet` (mimo proces).</span><span class="sxs-lookup"><span data-stu-id="837c0-152">`Process.GetCurrentProcess().ProcessName` reports `w3wp`/`iisexpress` (in-process) or `dotnet` (out-of-process).</span></span>

<span data-ttu-id="837c0-153">Mnoho nativních modulů, jako je ověřování systému Windows, zůstane aktivní.</span><span class="sxs-lookup"><span data-stu-id="837c0-153">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="837c0-154">Další informace o aktivních modulech služby IIS pomocí modulu ASP.NET Core najdete v tématu <xref:host-and-deploy/iis/modules> .</span><span class="sxs-lookup"><span data-stu-id="837c0-154">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="837c0-155">Modul ASP.NET Core může také:</span><span class="sxs-lookup"><span data-stu-id="837c0-155">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="837c0-156">Nastavte proměnné prostředí pro pracovní proces.</span><span class="sxs-lookup"><span data-stu-id="837c0-156">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="837c0-157">Protokoluje výstup stdout do úložiště souborů pro řešení problémů při spuštění.</span><span class="sxs-lookup"><span data-stu-id="837c0-157">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="837c0-158">Předejte tokeny ověřování systému Windows.</span><span class="sxs-lookup"><span data-stu-id="837c0-158">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="837c0-159">Jak nainstalovat a používat modul ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="837c0-159">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="837c0-160">Pokyny k instalaci modulu ASP.NET Core najdete v tématu [instalace hostující sady .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span><span class="sxs-lookup"><span data-stu-id="837c0-160">For instructions on how to install the ASP.NET Core Module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="837c0-161">Konfigurace s web.config</span><span class="sxs-lookup"><span data-stu-id="837c0-161">Configuration with web.config</span></span>

<span data-ttu-id="837c0-162">Modul ASP.NET Core je nakonfigurován s `aspNetCore` částí `system.webServer` uzlu v souboru *web.config* webu.</span><span class="sxs-lookup"><span data-stu-id="837c0-162">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="837c0-163">Následující *web.config* soubor je publikován pro [nasazení závislé na rozhraní](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) a konfiguruje modul ASP.NET Core, aby zpracovával požadavky webu:</span><span class="sxs-lookup"><span data-stu-id="837c0-163">The following *web.config* file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

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

<span data-ttu-id="837c0-164">Následující *web.config* jsou publikovány pro [samostatně uzavřené nasazení](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span><span class="sxs-lookup"><span data-stu-id="837c0-164">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

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

<span data-ttu-id="837c0-165"><xref:System.Configuration.SectionInformation.InheritInChildApplications*>Vlastnost je nastavena na hodnotu `false` , která označuje, že nastavení zadaná v rámci [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) elementu nejsou děděna aplikacemi, které jsou umístěny v podadresáři aplikace.</span><span class="sxs-lookup"><span data-stu-id="837c0-165">The <xref:System.Configuration.SectionInformation.InheritInChildApplications*> property is set to `false` to indicate that the settings specified within the [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) element aren't inherited by apps that reside in a subdirectory of the app.</span></span>

<span data-ttu-id="837c0-166">Když je aplikace nasazená na [Azure App Service](https://azure.microsoft.com/services/app-service/), `stdoutLogFile` cesta je nastavená na `\\?\%home%\LogFiles\stdout` .</span><span class="sxs-lookup"><span data-stu-id="837c0-166">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="837c0-167">Cesta ukládá protokoly stdout do složky *soubory protokolů* , což je umístění, které služba automaticky vytvořila.</span><span class="sxs-lookup"><span data-stu-id="837c0-167">The path saves stdout logs to the *LogFiles* folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="837c0-168">Informace o konfiguraci dílčí aplikace IIS najdete v tématu <xref:host-and-deploy/iis/index#sub-applications> .</span><span class="sxs-lookup"><span data-stu-id="837c0-168">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="837c0-169">Atributy elementu aspNetCore</span><span class="sxs-lookup"><span data-stu-id="837c0-169">Attributes of the aspNetCore element</span></span>

| <span data-ttu-id="837c0-170">Atribut</span><span class="sxs-lookup"><span data-stu-id="837c0-170">Attribute</span></span> | <span data-ttu-id="837c0-171">Popis</span><span class="sxs-lookup"><span data-stu-id="837c0-171">Description</span></span> | <span data-ttu-id="837c0-172">Výchozí</span><span class="sxs-lookup"><span data-stu-id="837c0-172">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="837c0-173">Volitelný řetězcový atribut.</span><span class="sxs-lookup"><span data-stu-id="837c0-173">Optional string attribute.</span></span></p><p><span data-ttu-id="837c0-174">Argumenty ke spustitelnému souboru určenému v **processPath**.</span><span class="sxs-lookup"><span data-stu-id="837c0-174">Arguments to the executable specified in **processPath**.</span></span></p> | |
| `disableStartUpErrorPage` | <p><span data-ttu-id="837c0-175">Volitelný atribut typu Boolean.</span><span class="sxs-lookup"><span data-stu-id="837c0-175">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="837c0-176">Pokud je nastaveno na true, stránka **502,5-procesového selhání** se potlačí a stavová stránka 502, která je nakonfigurovaná ve *web.config* , má přednost.</span><span class="sxs-lookup"><span data-stu-id="837c0-176">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="837c0-177">Volitelný atribut typu Boolean.</span><span class="sxs-lookup"><span data-stu-id="837c0-177">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="837c0-178">Pokud je nastaveno na true, token se přepošle podřízenému procesu, který naslouchá na% ASPNETCORE_PORT% jako záhlaví MS-ASPNETCORE-WINAUTHTOKEN na žádost.</span><span class="sxs-lookup"><span data-stu-id="837c0-178">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="837c0-179">Je zodpovědností za tento proces volání CloseHandle na tento token na žádost.</span><span class="sxs-lookup"><span data-stu-id="837c0-179">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `hostingModel` | <p><span data-ttu-id="837c0-180">Volitelný řetězcový atribut.</span><span class="sxs-lookup"><span data-stu-id="837c0-180">Optional string attribute.</span></span></p><p><span data-ttu-id="837c0-181">Určuje model hostování jako vnitroprocesové ( `InProcess` / `inprocess` ) nebo mimo proces ( `OutOfProcess` / `outofprocess` ).</span><span class="sxs-lookup"><span data-stu-id="837c0-181">Specifies the hosting model as in-process (`InProcess`/`inprocess`) or out-of-process (`OutOfProcess`/`outofprocess`).</span></span></p> | `InProcess`<br>`inprocess` |
| `processesPerApplication` | <p><span data-ttu-id="837c0-182">Volitelný celočíselný atribut</span><span class="sxs-lookup"><span data-stu-id="837c0-182">Optional integer attribute.</span></span></p><p><span data-ttu-id="837c0-183">Určuje počet instancí procesu určeného v nastavení **processPath** , které lze v rámci aplikace vystavit.</span><span class="sxs-lookup"><span data-stu-id="837c0-183">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="837c0-184">&dagger;Pro hostování v rámci procesu je hodnota omezená na `1` .</span><span class="sxs-lookup"><span data-stu-id="837c0-184">&dagger;For in-process hosting, the value is limited to `1`.</span></span></p><p><span data-ttu-id="837c0-185">Nastavení `processesPerApplication` se nedoporučuje.</span><span class="sxs-lookup"><span data-stu-id="837c0-185">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="837c0-186">Tento atribut bude v budoucí verzi odebrán.</span><span class="sxs-lookup"><span data-stu-id="837c0-186">This attribute will be removed in a future release.</span></span></p> | <span data-ttu-id="837c0-187">Výchozí `1`</span><span class="sxs-lookup"><span data-stu-id="837c0-187">Default: `1`</span></span><br><span data-ttu-id="837c0-188">Dlouhé `1`</span><span class="sxs-lookup"><span data-stu-id="837c0-188">Min: `1`</span></span><br><span data-ttu-id="837c0-189">Počet `100`&dagger;</span><span class="sxs-lookup"><span data-stu-id="837c0-189">Max: `100`&dagger;</span></span> |
| `processPath` | <p><span data-ttu-id="837c0-190">Povinný atribut řetězce.</span><span class="sxs-lookup"><span data-stu-id="837c0-190">Required string attribute.</span></span></p><p><span data-ttu-id="837c0-191">Cesta ke spustitelnému souboru, který spouští proces naslouchající na požadavky HTTP.</span><span class="sxs-lookup"><span data-stu-id="837c0-191">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="837c0-192">Jsou podporovány relativní cesty.</span><span class="sxs-lookup"><span data-stu-id="837c0-192">Relative paths are supported.</span></span> <span data-ttu-id="837c0-193">Pokud cesta začíná `.` , bude tato cesta považována za relativní vzhledem k kořenu webu.</span><span class="sxs-lookup"><span data-stu-id="837c0-193">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="837c0-194">Volitelný celočíselný atribut</span><span class="sxs-lookup"><span data-stu-id="837c0-194">Optional integer attribute.</span></span></p><p><span data-ttu-id="837c0-195">Určuje, kolikrát je povoleno selhání procesu určeného v **processPath** za minutu.</span><span class="sxs-lookup"><span data-stu-id="837c0-195">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="837c0-196">Pokud je tento limit překročen, modul ukončí spuštění procesu po zbytek minuty.</span><span class="sxs-lookup"><span data-stu-id="837c0-196">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p><p><span data-ttu-id="837c0-197">Nepodporováno v hostování v rámci procesu.</span><span class="sxs-lookup"><span data-stu-id="837c0-197">Not supported with in-process hosting.</span></span></p> | <span data-ttu-id="837c0-198">Výchozí `10`</span><span class="sxs-lookup"><span data-stu-id="837c0-198">Default: `10`</span></span><br><span data-ttu-id="837c0-199">Dlouhé `0`</span><span class="sxs-lookup"><span data-stu-id="837c0-199">Min: `0`</span></span><br><span data-ttu-id="837c0-200">Počet `100`</span><span class="sxs-lookup"><span data-stu-id="837c0-200">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="837c0-201">Volitelný atribut TimeSpan.</span><span class="sxs-lookup"><span data-stu-id="837c0-201">Optional timespan attribute.</span></span></p><p><span data-ttu-id="837c0-202">Určuje dobu, po kterou modul ASP.NET Core čeká na odpověď od procesu, který naslouchá na% ASPNETCORE_PORT%.</span><span class="sxs-lookup"><span data-stu-id="837c0-202">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="837c0-203">Ve verzích modulu ASP.NET Core, které byly dodávány s vydáním ASP.NET Core 2,1 nebo vyšší, `requestTimeout` je určena v hodinách, minutách a sekundách.</span><span class="sxs-lookup"><span data-stu-id="837c0-203">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p><p><span data-ttu-id="837c0-204">Neplatí pro hostování v procesu.</span><span class="sxs-lookup"><span data-stu-id="837c0-204">Doesn't apply to in-process hosting.</span></span> <span data-ttu-id="837c0-205">Pro hostování v rámci procesu modul čeká, až aplikace zpracuje požadavek.</span><span class="sxs-lookup"><span data-stu-id="837c0-205">For in-process hosting, the module waits for the app to process the request.</span></span></p><p><span data-ttu-id="837c0-206">Platné hodnoty segmentů minut a sekund řetězce jsou v rozsahu 0-59.</span><span class="sxs-lookup"><span data-stu-id="837c0-206">Valid values for minutes and seconds segments of the string are in the range 0-59.</span></span> <span data-ttu-id="837c0-207">Při použití hodnoty **60** v hodnotě minut nebo sekund dojde k chybě systému *500 – interní chyba serveru*.</span><span class="sxs-lookup"><span data-stu-id="837c0-207">Use of **60** in the value for minutes or seconds results in a *500 - Internal Server Error*.</span></span></p> | <span data-ttu-id="837c0-208">Výchozí `00:02:00`</span><span class="sxs-lookup"><span data-stu-id="837c0-208">Default: `00:02:00`</span></span><br><span data-ttu-id="837c0-209">Dlouhé `00:00:00`</span><span class="sxs-lookup"><span data-stu-id="837c0-209">Min: `00:00:00`</span></span><br><span data-ttu-id="837c0-210">Počet `360:00:00`</span><span class="sxs-lookup"><span data-stu-id="837c0-210">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="837c0-211">Volitelný celočíselný atribut</span><span class="sxs-lookup"><span data-stu-id="837c0-211">Optional integer attribute.</span></span></p><p><span data-ttu-id="837c0-212">Doba v sekundách, po kterou modul čeká na řádné vypnutí spustitelného souboru, když se zjistí soubor *app_offline.htm* .</span><span class="sxs-lookup"><span data-stu-id="837c0-212">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="837c0-213">Výchozí `10`</span><span class="sxs-lookup"><span data-stu-id="837c0-213">Default: `10`</span></span><br><span data-ttu-id="837c0-214">Dlouhé `0`</span><span class="sxs-lookup"><span data-stu-id="837c0-214">Min: `0`</span></span><br><span data-ttu-id="837c0-215">Počet `600`</span><span class="sxs-lookup"><span data-stu-id="837c0-215">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="837c0-216">Volitelný celočíselný atribut</span><span class="sxs-lookup"><span data-stu-id="837c0-216">Optional integer attribute.</span></span></p><p><span data-ttu-id="837c0-217">Doba v sekundách, kterou modul počká, než spustitelný soubor spustí proces, který na portu naslouchá.</span><span class="sxs-lookup"><span data-stu-id="837c0-217">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="837c0-218">Pokud je tento časový limit překročen, modul proces ukončuje.</span><span class="sxs-lookup"><span data-stu-id="837c0-218">If this time limit is exceeded, the module kills the process.</span></span></p><p><span data-ttu-id="837c0-219">Při hostování *v procesu*: proces **není restartován a** **nepoužívá nastavení** **rapidFailsPerMinute** .</span><span class="sxs-lookup"><span data-stu-id="837c0-219">When hosting *in-process*: The process is **not** restarted and does **not** use the **rapidFailsPerMinute** setting.</span></span></p><p><span data-ttu-id="837c0-220">Při hostování *mimo proces*: modul se pokusí znovu spustit proces, když obdrží novou žádost, a pokračuje v pokusu o restartování procesu u dalších příchozích požadavků, pokud se aplikaci nepodaří spustit **rapidFailsPerMinute** počet pokusů během poslední minuty.</span><span class="sxs-lookup"><span data-stu-id="837c0-220">When hosting *out-of-process*: The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="837c0-221">Hodnota 0 (nula **) není považována za** nekonečný časový limit.</span><span class="sxs-lookup"><span data-stu-id="837c0-221">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="837c0-222">Výchozí `120`</span><span class="sxs-lookup"><span data-stu-id="837c0-222">Default: `120`</span></span><br><span data-ttu-id="837c0-223">Dlouhé `0`</span><span class="sxs-lookup"><span data-stu-id="837c0-223">Min: `0`</span></span><br><span data-ttu-id="837c0-224">Počet `3600`</span><span class="sxs-lookup"><span data-stu-id="837c0-224">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="837c0-225">Volitelný atribut typu Boolean.</span><span class="sxs-lookup"><span data-stu-id="837c0-225">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="837c0-226">Je-li nastavena hodnota true, **stdout** a **stderr** pro proces zadaný v **processPath** budou přesměrovány do souboru zadaného v **stdoutLogFile**.</span><span class="sxs-lookup"><span data-stu-id="837c0-226">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="837c0-227">Volitelný řetězcový atribut.</span><span class="sxs-lookup"><span data-stu-id="837c0-227">Optional string attribute.</span></span></p><p><span data-ttu-id="837c0-228">Určuje relativní nebo absolutní cestu k souboru, pro který je **stdout** a **stderr** z procesu určeného v **processPath** protokolováno.</span><span class="sxs-lookup"><span data-stu-id="837c0-228">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="837c0-229">Relativní cesty jsou relativní vzhledem k kořenu webu.</span><span class="sxs-lookup"><span data-stu-id="837c0-229">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="837c0-230">Každá cesta začínající řetězcem `.` je relativní vzhledem k kořenu webu a všechny ostatní cesty se považují za absolutní cesty.</span><span class="sxs-lookup"><span data-stu-id="837c0-230">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="837c0-231">Při vytvoření souboru protokolu se v modulu vytvoří všechny složky, které jsou v cestě zadané.</span><span class="sxs-lookup"><span data-stu-id="837c0-231">Any folders provided in the path are created by the module when the log file is created.</span></span> <span data-ttu-id="837c0-232">Pomocí oddělovačů podtržítka se do posledního segmentu cesty **stdoutLogFile** přidá časové razítko, ID procesu a Přípona souboru (*. log*).</span><span class="sxs-lookup"><span data-stu-id="837c0-232">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="837c0-233">Pokud `.\logs\stdout` je zadán jako hodnota, je ukázkový protokol stdout uložen jako *stdout_20180205194132_1934. log* ve složce *logs* při uložení na 2/5/2018 v 19:41:32 s ID procesu 1934.</span><span class="sxs-lookup"><span data-stu-id="837c0-233">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

### <a name="set-environment-variables"></a><span data-ttu-id="837c0-234">Nastavení proměnných prostředí</span><span class="sxs-lookup"><span data-stu-id="837c0-234">Set environment variables</span></span>

<span data-ttu-id="837c0-235">Proměnné prostředí lze zadat pro proces v `processPath` atributu.</span><span class="sxs-lookup"><span data-stu-id="837c0-235">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="837c0-236">Zadejte proměnnou prostředí s `<environmentVariable>` podřízeným elementem `<environmentVariables>` elementu kolekce.</span><span class="sxs-lookup"><span data-stu-id="837c0-236">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span> <span data-ttu-id="837c0-237">Proměnné prostředí nastavené v této části mají přednost před proměnnými prostředí systému.</span><span class="sxs-lookup"><span data-stu-id="837c0-237">Environment variables set in this section take precedence over system environment variables.</span></span>

<span data-ttu-id="837c0-238">Následující příklad nastaví dvě proměnné prostředí v *web.config*. `ASPNETCORE_ENVIRONMENT` nakonfiguruje prostředí aplikace na `Development` .</span><span class="sxs-lookup"><span data-stu-id="837c0-238">The following example sets two environment variables in *web.config*. `ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="837c0-239">Vývojář může tuto hodnotu dočasně nastavit v souboru *web.config* , aby bylo možné vynutit, aby se [Stránka s výjimkou vývojářů](xref:fundamentals/error-handling) načetla při ladění výjimky aplikace.</span><span class="sxs-lookup"><span data-stu-id="837c0-239">A developer may temporarily set this value in the *web.config* file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="837c0-240">`CONFIG_DIR` je příkladem uživatelsky definované proměnné prostředí, kde vývojář napsal kód, který přečte hodnotu při spuštění, aby vytvořil cestu pro načtení konfiguračního souboru aplikace.</span><span class="sxs-lookup"><span data-stu-id="837c0-240">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

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
> <span data-ttu-id="837c0-241">Alternativou k nastavení prostředí přímo v *web.config* je zahrnutí `<EnvironmentName>` vlastnosti do [profilu publikování (. pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) nebo souboru projektu.</span><span class="sxs-lookup"><span data-stu-id="837c0-241">An alternative to setting the environment directly in *web.config* is to include the `<EnvironmentName>` property in the [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) or project file.</span></span> <span data-ttu-id="837c0-242">Tento přístup nastaví prostředí v *web.config* při publikování projektu:</span><span class="sxs-lookup"><span data-stu-id="837c0-242">This approach sets the environment in *web.config* when the project is published:</span></span>
>
> ```xml
> <PropertyGroup>
>   <EnvironmentName>Development</EnvironmentName>
> </PropertyGroup>
> ```

> [!WARNING]
> <span data-ttu-id="837c0-243">Nastavte pouze `ASPNETCORE_ENVIRONMENT` proměnnou prostředí na `Development` pracovní a testovací servery, které nejsou přístupné nedůvěryhodným sítím, jako je například Internet.</span><span class="sxs-lookup"><span data-stu-id="837c0-243">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## <a name="app_offlinehtm"></a><span data-ttu-id="837c0-244">app_offline.htm</span><span class="sxs-lookup"><span data-stu-id="837c0-244">app_offline.htm</span></span>

<span data-ttu-id="837c0-245">Pokud se v kořenovém adresáři aplikace zjistí soubor s názvem *app_offline.htm* , modul ASP.NET Core se pokusí aplikaci řádně vypnout a zastavit zpracování příchozích požadavků.</span><span class="sxs-lookup"><span data-stu-id="837c0-245">If a file with the name *app_offline.htm* is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="837c0-246">Pokud aplikace běží i po uplynutí počtu sekund definovaného v `shutdownTimeLimit` , modul ASP.NET Core ukončuje běžící proces.</span><span class="sxs-lookup"><span data-stu-id="837c0-246">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="837c0-247">Když je přítomen soubor *app_offline.htm* , ASP.NET Core modul reaguje na požadavky odesláním zpět obsahu *app_offline.htm* souboru.</span><span class="sxs-lookup"><span data-stu-id="837c0-247">While the *app_offline.htm* file is present, the ASP.NET Core Module responds to requests by sending back the contents of the *app_offline.htm* file.</span></span> <span data-ttu-id="837c0-248">Po odebrání souboru *app_offline.htm* spustí aplikace další požadavek.</span><span class="sxs-lookup"><span data-stu-id="837c0-248">When the *app_offline.htm* file is removed, the next request starts the app.</span></span>

<span data-ttu-id="837c0-249">Při použití nezpracovaného modelu hostování se aplikace nemusí okamžitě vypnout, pokud dojde k otevřenému připojení.</span><span class="sxs-lookup"><span data-stu-id="837c0-249">When using the out-of-process hosting model, the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="837c0-250">Připojení k protokolu WebSocket může například zpozdit ukončení aplikace.</span><span class="sxs-lookup"><span data-stu-id="837c0-250">For example, a websocket connection may delay app shut down.</span></span>

## <a name="start-up-error-page"></a><span data-ttu-id="837c0-251">Spouštěcí chybová stránka</span><span class="sxs-lookup"><span data-stu-id="837c0-251">Start-up error page</span></span>

<span data-ttu-id="837c0-252">Hostování v procesu i mimo procesy vytváří vlastní chybové stránky, když se nepodaří aplikaci spustit.</span><span class="sxs-lookup"><span data-stu-id="837c0-252">Both in-process and out-of-process hosting produce custom error pages when they fail to start the app.</span></span>

<span data-ttu-id="837c0-253">Pokud modul ASP.NET Core nenalezne obslužnou rutinu žádosti v procesu nebo mimo proces 500,0, zobrazí se stránka s kódem stavu *selhání načtení obslužné rutiny v* procesu nebo mimo proces.</span><span class="sxs-lookup"><span data-stu-id="837c0-253">If the ASP.NET Core Module fails to find either the in-process or out-of-process request handler, a *500.0 - In-Process/Out-Of-Process Handler Load Failure* status code page appears.</span></span>

<span data-ttu-id="837c0-254">Pro vnitroprocesové hostování v případě, že se modul ASP.NET Core nepodaří spustit aplikaci, zobrazí se stránka s kódem stavu *chyby při spuštění 500,30* .</span><span class="sxs-lookup"><span data-stu-id="837c0-254">For in-process hosting if the ASP.NET Core Module fails to start the app, a *500.30 - Start Failure* status code page appears.</span></span>

<span data-ttu-id="837c0-255">Pro hostování mimo procesy, pokud se ASP.NET Core modulu nespustí back-end proces, nebo dojde k selhání procesu back-endu, ale nenaslouchá na konfigurovaném portu, zobrazí se stránka s kódem stavu *selhání procesu 502,5* .</span><span class="sxs-lookup"><span data-stu-id="837c0-255">For out-of-process hosting if the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span>

<span data-ttu-id="837c0-256">Chcete-li tuto stránku potlačit a vrátit se na výchozí stavovou stránku služby IIS 5xx, použijte `disableStartUpErrorPage` atribut.</span><span class="sxs-lookup"><span data-stu-id="837c0-256">To suppress this page and revert to the default IIS 5xx status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="837c0-257">Další informace o konfiguraci vlastních chybových zpráv najdete v tématu [chyby \<httpErrors> protokolu HTTP ](/iis/configuration/system.webServer/httpErrors/).</span><span class="sxs-lookup"><span data-stu-id="837c0-257">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

## <a name="log-creation-and-redirection"></a><span data-ttu-id="837c0-258">Vytvoření a přesměrování protokolu</span><span class="sxs-lookup"><span data-stu-id="837c0-258">Log creation and redirection</span></span>

<span data-ttu-id="837c0-259">Modul ASP.NET Core přesměruje výstup konzoly stdout a stderr na disk, pokud `stdoutLogEnabled` `stdoutLogFile` `aspNetCore` jsou nastaveny atributy a elementu.</span><span class="sxs-lookup"><span data-stu-id="837c0-259">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="837c0-260">`stdoutLogFile`Při vytvoření souboru protokolu jsou všechny složky v této cestě vytvářeny modulem.</span><span class="sxs-lookup"><span data-stu-id="837c0-260">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="837c0-261">Fond aplikací musí mít oprávnění k zápisu do umístění, kam se protokoly zapisují (použijte `IIS AppPool\<app_pool_name>` k poskytnutí oprávnění k zápisu).</span><span class="sxs-lookup"><span data-stu-id="837c0-261">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="837c0-262">Protokoly nejsou otočeny, pokud nedochází k recyklování/restartování procesu.</span><span class="sxs-lookup"><span data-stu-id="837c0-262">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="837c0-263">Je zodpovědností hostitele omezit místo na disku, které používají protokoly.</span><span class="sxs-lookup"><span data-stu-id="837c0-263">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="837c0-264">Použití protokolu stdout se doporučuje pouze při řešení potíží se spouštěním aplikací při hostování služby IIS nebo při použití [podpory v době vývoje služby IIS se sadou Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), nikoli při ladění místně a při spuštění aplikace s IIS Express.</span><span class="sxs-lookup"><span data-stu-id="837c0-264">Using the stdout log is only recommended for troubleshooting app startup issues when hosting on IIS or when using [development-time support for IIS with Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), not while debugging locally and running the app with IIS Express.</span></span>

<span data-ttu-id="837c0-265">Nepoužívejte protokol stdout pro obecné účely protokolování aplikace.</span><span class="sxs-lookup"><span data-stu-id="837c0-265">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="837c0-266">Pro rutinu protokolování v aplikaci ASP.NET Core použijte knihovnu protokolování, která omezuje velikost souboru protokolu a otočí protokoly.</span><span class="sxs-lookup"><span data-stu-id="837c0-266">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="837c0-267">Další informace najdete v tématu [Zprostředkovatelé protokolování třetích stran](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="837c0-267">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="837c0-268">Časové razítko a Přípona souboru se automaticky přidají při vytvoření souboru protokolu.</span><span class="sxs-lookup"><span data-stu-id="837c0-268">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="837c0-269">Název souboru protokolu se skládá připojením časového razítka, ID procesu a přípony souboru (*. log*) k poslednímu segmentu `stdoutLogFile` cesty (obvykle *stdout*), oddělený podtržítky.</span><span class="sxs-lookup"><span data-stu-id="837c0-269">The log file name is composed by appending the timestamp, process ID, and file extension (*.log*) to the last segment of the `stdoutLogFile` path (typically *stdout*) delimited by underscores.</span></span> <span data-ttu-id="837c0-270">Pokud `stdoutLogFile` cesta končí na *stdout*, protokol pro aplikaci s číslem PID 1934 vytvořeným v 2/5/2018 v 19:42:32 má název souboru *stdout_20180205194132_1934. log*.</span><span class="sxs-lookup"><span data-stu-id="837c0-270">If the `stdoutLogFile` path ends with *stdout*, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name *stdout_20180205194132_1934.log*.</span></span>

<span data-ttu-id="837c0-271">Pokud `stdoutLogEnabled` je hodnota false, chyby, ke kterým dochází při spuštění aplikace, se zaznamenávají a generují do protokolu událostí až do 30 kB.</span><span class="sxs-lookup"><span data-stu-id="837c0-271">If `stdoutLogEnabled` is false, errors that occur on app startup are captured and emitted to the event log up to 30 KB.</span></span> <span data-ttu-id="837c0-272">Po spuštění budou všechny další protokoly zahozeny.</span><span class="sxs-lookup"><span data-stu-id="837c0-272">After startup, all additional logs are discarded.</span></span>

<span data-ttu-id="837c0-273">Následující vzorový `aspNetCore` prvek nakonfiguruje protokolování stdout na relativní cestě `.\log\` .</span><span class="sxs-lookup"><span data-stu-id="837c0-273">The following sample `aspNetCore` element configures stdout logging at the relative path `.\log\`.</span></span> <span data-ttu-id="837c0-274">Potvrďte, že identita uživatele fondu aplikací má oprávnění k zápisu do zadané cesty.</span><span class="sxs-lookup"><span data-stu-id="837c0-274">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout"
    hostingModel="inprocess">
</aspNetCore>
```

<span data-ttu-id="837c0-275">Když publikujete aplikaci pro nasazení Azure App Service, sada web SDK nastaví `stdoutLogFile` hodnotu na `\\?\%home%\LogFiles\stdout` .</span><span class="sxs-lookup"><span data-stu-id="837c0-275">When publishing an app for Azure App Service deployment, the Web SDK sets the `stdoutLogFile` value to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="837c0-276">`%home`Proměnná prostředí je předdefinovaná pro aplikace hostované v Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="837c0-276">The `%home` environment variable is predefined for apps hosted by Azure App Service.</span></span>

<span data-ttu-id="837c0-277">Chcete-li vytvořit pravidla filtru protokolování, přečtěte si část [Konfigurace](xref:fundamentals/logging/index#log-filtering) a [filtrování protokolů](xref:fundamentals/logging/index#log-filtering) v dokumentaci ASP.NET Core protokolování.</span><span class="sxs-lookup"><span data-stu-id="837c0-277">To create logging filter rules, see the [Configuration](xref:fundamentals/logging/index#log-filtering) and [Log filtering](xref:fundamentals/logging/index#log-filtering) sections of the ASP.NET Core logging documentation.</span></span>

<span data-ttu-id="837c0-278">Další informace o formátech cest najdete v tématu [formáty souborů cest v systémech Windows](/dotnet/standard/io/file-path-formats).</span><span class="sxs-lookup"><span data-stu-id="837c0-278">For more information on path formats, see [File path formats on Windows systems](/dotnet/standard/io/file-path-formats).</span></span>

## <a name="enhanced-diagnostic-logs"></a><span data-ttu-id="837c0-279">Rozšířené diagnostické protokoly</span><span class="sxs-lookup"><span data-stu-id="837c0-279">Enhanced diagnostic logs</span></span>

<span data-ttu-id="837c0-280">Modul ASP.NET Core lze nakonfigurovat tak, aby poskytoval rozšířené diagnostické protokoly.</span><span class="sxs-lookup"><span data-stu-id="837c0-280">The ASP.NET Core Module is configurable to provide enhanced diagnostics logs.</span></span> <span data-ttu-id="837c0-281">Přidejte `<handlerSettings>` element do `<aspNetCore>` prvku v *web.config*. Nastavení `debugLevel` pro `TRACE` zpřístupňuje vyšší věrnost diagnostických informací:</span><span class="sxs-lookup"><span data-stu-id="837c0-281">Add the `<handlerSettings>` element to the `<aspNetCore>` element in *web.config*. Setting the `debugLevel` to `TRACE` exposes a higher fidelity of diagnostic information:</span></span>

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

<span data-ttu-id="837c0-282">Všechny složky v cestě (*protokoly* v předchozím příkladu) se vytvoří modulem, když se vytvoří soubor protokolu.</span><span class="sxs-lookup"><span data-stu-id="837c0-282">Any folders in the path (*logs* in the preceding example) are created by the module when the log file is created.</span></span> <span data-ttu-id="837c0-283">Fond aplikací musí mít oprávnění k zápisu do umístění, kam se protokoly zapisují (použijte `IIS AppPool\<app_pool_name>` k poskytnutí oprávnění k zápisu).</span><span class="sxs-lookup"><span data-stu-id="837c0-283">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="837c0-284">Hodnoty úrovně ladění ( `debugLevel` ) mohou zahrnovat jak úroveň, tak i umístění.</span><span class="sxs-lookup"><span data-stu-id="837c0-284">Debug level (`debugLevel`) values can include both the level and the location.</span></span>

<span data-ttu-id="837c0-285">Úrovně (v pořadí od nejméně po nejpodrobnější):</span><span class="sxs-lookup"><span data-stu-id="837c0-285">Levels (in order from least to most verbose):</span></span>

* <span data-ttu-id="837c0-286">CHYBA</span><span class="sxs-lookup"><span data-stu-id="837c0-286">ERROR</span></span>
* <span data-ttu-id="837c0-287">WARNING</span><span class="sxs-lookup"><span data-stu-id="837c0-287">WARNING</span></span>
* <span data-ttu-id="837c0-288">PŘÍJEMCE</span><span class="sxs-lookup"><span data-stu-id="837c0-288">INFO</span></span>
* <span data-ttu-id="837c0-289">TRACE</span><span class="sxs-lookup"><span data-stu-id="837c0-289">TRACE</span></span>

<span data-ttu-id="837c0-290">Umístění (je povoleno více umístění):</span><span class="sxs-lookup"><span data-stu-id="837c0-290">Locations (multiple locations are permitted):</span></span>

* <span data-ttu-id="837c0-291">STROMU</span><span class="sxs-lookup"><span data-stu-id="837c0-291">CONSOLE</span></span>
* <span data-ttu-id="837c0-292">EVENTLOG</span><span class="sxs-lookup"><span data-stu-id="837c0-292">EVENTLOG</span></span>
* <span data-ttu-id="837c0-293">SOUBOR</span><span class="sxs-lookup"><span data-stu-id="837c0-293">FILE</span></span>

<span data-ttu-id="837c0-294">Nastavení obslužné rutiny lze také poskytnout pomocí proměnných prostředí:</span><span class="sxs-lookup"><span data-stu-id="837c0-294">The handler settings can also be provided via environment variables:</span></span>

* <span data-ttu-id="837c0-295">`ASPNETCORE_MODULE_DEBUG_FILE`: Cesta k souboru protokolu ladění.</span><span class="sxs-lookup"><span data-stu-id="837c0-295">`ASPNETCORE_MODULE_DEBUG_FILE`: Path to the debug log file.</span></span> <span data-ttu-id="837c0-296">(Výchozí: *aspnetcore-Debug. log*)</span><span class="sxs-lookup"><span data-stu-id="837c0-296">(Default: *aspnetcore-debug.log*)</span></span>
* <span data-ttu-id="837c0-297">`ASPNETCORE_MODULE_DEBUG`: Nastavení úrovně ladění.</span><span class="sxs-lookup"><span data-stu-id="837c0-297">`ASPNETCORE_MODULE_DEBUG`: Debug level setting.</span></span>

> [!WARNING]
> <span data-ttu-id="837c0-298">Nenechávejte **protokolování** ladění povolené v nasazení po delší dobu, než je potřeba k řešení problému.</span><span class="sxs-lookup"><span data-stu-id="837c0-298">Do **not** leave debug logging enabled in the deployment for longer than required to troubleshoot an issue.</span></span> <span data-ttu-id="837c0-299">Velikost protokolu není omezená.</span><span class="sxs-lookup"><span data-stu-id="837c0-299">The size of the log isn't limited.</span></span> <span data-ttu-id="837c0-300">Když necháte protokol ladění povolený, může dojít k vyčerpání dostupného místa na disku a selhání serveru nebo služby App Service.</span><span class="sxs-lookup"><span data-stu-id="837c0-300">Leaving the debug log enabled can exhaust the available disk space and crash the server or app service.</span></span>

<span data-ttu-id="837c0-301">Příklad prvku v souboruweb.confignaleznete v tématu [konfigurace s web.config](#configuration-with-webconfig) `aspNetCore` . *web.config*</span><span class="sxs-lookup"><span data-stu-id="837c0-301">See [Configuration with web.config](#configuration-with-webconfig) for an example of the `aspNetCore` element in the *web.config* file.</span></span>

## <a name="modify-the-stack-size"></a><span data-ttu-id="837c0-302">Úprava velikosti zásobníku</span><span class="sxs-lookup"><span data-stu-id="837c0-302">Modify the stack size</span></span>

<span data-ttu-id="837c0-303">*Platí pouze při použití modelu hostování v rámci procesu.*</span><span class="sxs-lookup"><span data-stu-id="837c0-303">*Only applies when using the in-process hosting model.*</span></span>

<span data-ttu-id="837c0-304">Nakonfigurujte velikost spravovaného zásobníku pomocí `stackSize` nastavení v bajtech v *web.config*. Výchozí velikost je `1048576` bajtů (1 MB).</span><span class="sxs-lookup"><span data-stu-id="837c0-304">Configure the managed stack size using the `stackSize` setting in bytes in *web.config*. The default size is `1048576` bytes (1 MB).</span></span>

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

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="837c0-305">Konfigurace proxy serveru používá protokol HTTP a token párování.</span><span class="sxs-lookup"><span data-stu-id="837c0-305">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="837c0-306">*Platí pouze pro hostování mimo proces.*</span><span class="sxs-lookup"><span data-stu-id="837c0-306">*Only applies to out-of-process hosting.*</span></span>

<span data-ttu-id="837c0-307">Proxy vytvořený mezi modulem ASP.NET Core a Kestrel používá protokol HTTP.</span><span class="sxs-lookup"><span data-stu-id="837c0-307">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="837c0-308">Nehrozí riziko odposlouchávání provozu mezi modulem a Kestrel z umístění od serveru.</span><span class="sxs-lookup"><span data-stu-id="837c0-308">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="837c0-309">Párovací token se používá k zajištění, že žádosti přijaté službou Kestrel byly proxy službou IIS a nepocházejí z jiného zdroje.</span><span class="sxs-lookup"><span data-stu-id="837c0-309">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="837c0-310">Token párování se vytvoří a nastaví na proměnnou prostředí ( `ASPNETCORE_TOKEN` ) modulem.</span><span class="sxs-lookup"><span data-stu-id="837c0-310">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="837c0-311">Párovací token je také nastaven na hlavičku ( `MS-ASPNETCORE-TOKEN` ) pro každý požadavek proxy.</span><span class="sxs-lookup"><span data-stu-id="837c0-311">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="837c0-312">Middleware služby IIS kontroluje každý požadavek, který obdrží, a potvrdí, že hodnota hlavičky párového tokenu odpovídá hodnotě proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="837c0-312">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="837c0-313">Pokud se hodnoty tokenu neshodují, požadavek se zaprotokoluje a odmítne.</span><span class="sxs-lookup"><span data-stu-id="837c0-313">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="837c0-314">Proměnná prostředí párového tokenu a přenos dat mezi modulem a Kestrel nejsou přístupné z umístění mimo server.</span><span class="sxs-lookup"><span data-stu-id="837c0-314">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="837c0-315">Bez znalosti hodnoty párovacího tokenu nemůže útočník odeslat žádosti, které obcházejí kontrolu v Middlewari IIS.</span><span class="sxs-lookup"><span data-stu-id="837c0-315">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="837c0-316">ASP.NET Core modul se sdílenou konfigurací služby IIS</span><span class="sxs-lookup"><span data-stu-id="837c0-316">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="837c0-317">Instalační služba modulu ASP.NET Core se spouští s oprávněními účtu **TrustedInstaller** .</span><span class="sxs-lookup"><span data-stu-id="837c0-317">The ASP.NET Core Module installer runs with the privileges of the **TrustedInstaller** account.</span></span> <span data-ttu-id="837c0-318">Vzhledem k tomu, že účet místního systému nemá oprávnění k úpravám pro cestu ke sdílené složce, kterou používá sdílená konfigurace služby IIS, vyvolá Instalační program chybu odepření přístupu při pokusu o konfiguraci nastavení modulu v souboru *applicationHost.config* ve sdílené složce.</span><span class="sxs-lookup"><span data-stu-id="837c0-318">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the *applicationHost.config* file on the share.</span></span>

<span data-ttu-id="837c0-319">Pokud používáte konfiguraci IIS Shared na stejném počítači jako instalaci služby IIS, spusťte instalační program sady prostředků ASP.NET Core s `OPT_NO_SHARED_CONFIG_CHECK` parametrem nastaveným na `1` :</span><span class="sxs-lookup"><span data-stu-id="837c0-319">When using an IIS Shared Configuration on the same machine as the IIS installation, run the ASP.NET Core Hosting Bundle installer with the `OPT_NO_SHARED_CONFIG_CHECK` parameter set to `1`:</span></span>

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

<span data-ttu-id="837c0-320">Pokud cesta ke sdílené konfiguraci není ve stejném počítači jako instalace služby IIS, postupujte podle těchto kroků:</span><span class="sxs-lookup"><span data-stu-id="837c0-320">When the path to the shared configuration isn't on the same machine as the IIS installation, follow these steps:</span></span>

1. <span data-ttu-id="837c0-321">Zakažte sdílenou konfiguraci služby IIS.</span><span class="sxs-lookup"><span data-stu-id="837c0-321">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="837c0-322">Spusťte instalační program.</span><span class="sxs-lookup"><span data-stu-id="837c0-322">Run the installer.</span></span>
1. <span data-ttu-id="837c0-323">Exportujte aktualizovaný soubor *applicationHost.config* do sdílené složky.</span><span class="sxs-lookup"><span data-stu-id="837c0-323">Export the updated *applicationHost.config* file to the share.</span></span>
1. <span data-ttu-id="837c0-324">Znovu povolte sdílenou konfiguraci služby IIS.</span><span class="sxs-lookup"><span data-stu-id="837c0-324">Re-enable the IIS Shared Configuration.</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="837c0-325">Verze modulu a instalační protokoly hostitelské sady</span><span class="sxs-lookup"><span data-stu-id="837c0-325">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="837c0-326">Postup určení verze nainstalovaného modulu ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="837c0-326">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="837c0-327">V hostitelském systému přejděte na *%windir%\system32\inetsrv*.</span><span class="sxs-lookup"><span data-stu-id="837c0-327">On the hosting system, navigate to *%windir%\System32\inetsrv*.</span></span>
1. <span data-ttu-id="837c0-328">Vyhledejte soubor *aspnetcore.dll* .</span><span class="sxs-lookup"><span data-stu-id="837c0-328">Locate the *aspnetcore.dll* file.</span></span>
1. <span data-ttu-id="837c0-329">Klikněte na soubor pravým tlačítkem a v místní nabídce vyberte **vlastnosti** .</span><span class="sxs-lookup"><span data-stu-id="837c0-329">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="837c0-330">Vyberte kartu **Podrobnosti** . **Verze souboru** a **verze produktu** reprezentují nainstalovanou verzi modulu.</span><span class="sxs-lookup"><span data-stu-id="837c0-330">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="837c0-331">Protokoly instalačních balíčků pro modul se nacházejí v *C: \\ Uživatelé \\ % UserName% \\ data \\ Local \\ TEMP*. Soubor má název *dd_DotNetCoreWinSvrHosting__ \<timestamp> _000_AspNetCoreModule_x64. log*.</span><span class="sxs-lookup"><span data-stu-id="837c0-331">The Hosting Bundle installer logs for the module are found at *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. The file is named *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="837c0-332">Umístění souboru modulu, schématu a konfiguračního souboru</span><span class="sxs-lookup"><span data-stu-id="837c0-332">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="837c0-333">Modul</span><span class="sxs-lookup"><span data-stu-id="837c0-333">Module</span></span>

<span data-ttu-id="837c0-334">**Služba IIS (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="837c0-334">**IIS (x86/amd64):**</span></span>

* <span data-ttu-id="837c0-335">% windir% \System32\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="837c0-335">%windir%\System32\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="837c0-336">% windir% \SysWOW64\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="837c0-336">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="837c0-337">Základní Module\V2\aspnetcorev2.dll%ProgramFiles%\IIS\Asp.Net</span><span class="sxs-lookup"><span data-stu-id="837c0-337">%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="837c0-338">% ProgramFiles (x86)% \ IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="837c0-338">%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

<span data-ttu-id="837c0-339">**IIS Express (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="837c0-339">**IIS Express (x86/amd64):**</span></span>

* <span data-ttu-id="837c0-340">%ProgramFiles%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="837c0-340">%ProgramFiles%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="837c0-341">% ProgramFiles (x86)% \ IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="837c0-341">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="837c0-342">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="837c0-342">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="837c0-343">% ProgramFiles (x86)% \ IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="837c0-343">%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

### <a name="schema"></a><span data-ttu-id="837c0-344">Schéma</span><span class="sxs-lookup"><span data-stu-id="837c0-344">Schema</span></span>

<span data-ttu-id="837c0-345">**IIS**</span><span class="sxs-lookup"><span data-stu-id="837c0-345">**IIS**</span></span>

* <span data-ttu-id="837c0-346">% windir% \System32\inetsrv\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="837c0-346">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span></span>

* <span data-ttu-id="837c0-347">% windir% \System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span><span class="sxs-lookup"><span data-stu-id="837c0-347">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span></span>

<span data-ttu-id="837c0-348">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="837c0-348">**IIS Express**</span></span>

* <span data-ttu-id="837c0-349">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="837c0-349">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span></span>

* <span data-ttu-id="837c0-350">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span><span class="sxs-lookup"><span data-stu-id="837c0-350">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span></span>

### <a name="configuration"></a><span data-ttu-id="837c0-351">Konfigurace</span><span class="sxs-lookup"><span data-stu-id="837c0-351">Configuration</span></span>

<span data-ttu-id="837c0-352">**IIS**</span><span class="sxs-lookup"><span data-stu-id="837c0-352">**IIS**</span></span>

* <span data-ttu-id="837c0-353">% windir% \System32\inetsrv\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="837c0-353">%windir%\System32\inetsrv\config\applicationHost.config</span></span>

<span data-ttu-id="837c0-354">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="837c0-354">**IIS Express**</span></span>

* <span data-ttu-id="837c0-355">Visual Studio: {ROOT aplikace} \\.vs\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="837c0-355">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span></span>

* <span data-ttu-id="837c0-356">*iisexpress.exe* CLI:% USERPROFILE% \Documents\IISExpress\config\applicationhost.config</span><span class="sxs-lookup"><span data-stu-id="837c0-356">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span></span>

<span data-ttu-id="837c0-357">Soubory můžete najít hledáním *aspnetcore* v souboru *applicationHost.config* .</span><span class="sxs-lookup"><span data-stu-id="837c0-357">The files can be found by searching for *aspnetcore* in the *applicationHost.config* file.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="837c0-358">Modul ASP.NET Core je nativním modulem služby IIS, který se připojuje k kanálu IIS na jednu z těchto:</span><span class="sxs-lookup"><span data-stu-id="837c0-358">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to either:</span></span>

* <span data-ttu-id="837c0-359">Hostování aplikace ASP.NET Core uvnitř pracovního procesu služby IIS ( `w3wp.exe` ), který se nazývá [model hostování v rámci procesu](#in-process-hosting-model).</span><span class="sxs-lookup"><span data-stu-id="837c0-359">Host an ASP.NET Core app inside of the IIS worker process (`w3wp.exe`), called the [in-process hosting model](#in-process-hosting-model).</span></span>
* <span data-ttu-id="837c0-360">Dopředné webové požadavky do back-endu ASP.NET Core aplikace, na které běží [Server Kestrel](xref:fundamentals/servers/kestrel), se nazývá [model hostování mimo proces](#out-of-process-hosting-model).</span><span class="sxs-lookup"><span data-stu-id="837c0-360">Forward web requests to a backend ASP.NET Core app running the [Kestrel server](xref:fundamentals/servers/kestrel), called the [out-of-process hosting model](#out-of-process-hosting-model).</span></span>

<span data-ttu-id="837c0-361">Podporované verze systému Windows:</span><span class="sxs-lookup"><span data-stu-id="837c0-361">Supported Windows versions:</span></span>

* <span data-ttu-id="837c0-362">Windows 7 nebo novější</span><span class="sxs-lookup"><span data-stu-id="837c0-362">Windows 7 or later</span></span>
* <span data-ttu-id="837c0-363">Windows Server 2008 R2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="837c0-363">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="837c0-364">Při hostování v procesu používá modul implementaci vnitroprocesového serveru pro službu IIS nazvanou IIS HTTP Server ( `IISHttpServer` ).</span><span class="sxs-lookup"><span data-stu-id="837c0-364">When hosting in-process, the module uses an in-process server implementation for IIS, called IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="837c0-365">Při hostování mimo proces funguje modul pouze s Kestrel.</span><span class="sxs-lookup"><span data-stu-id="837c0-365">When hosting out-of-process, the module only works with Kestrel.</span></span> <span data-ttu-id="837c0-366">Modul nefunguje s [HTTP.sys](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="837c0-366">The module doesn't function with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

## <a name="hosting-models"></a><span data-ttu-id="837c0-367">Modely hostingu</span><span class="sxs-lookup"><span data-stu-id="837c0-367">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="837c0-368">Model hostování v procesu</span><span class="sxs-lookup"><span data-stu-id="837c0-368">In-process hosting model</span></span>

<span data-ttu-id="837c0-369">Chcete-li nakonfigurovat aplikaci pro hostování v rámci procesu, přidejte do `<AspNetCoreHostingModel>` souboru projektu aplikace vlastnost s hodnotou `InProcess` (hostování mimo proces je nastavená na `OutOfProcess` ):</span><span class="sxs-lookup"><span data-stu-id="837c0-369">To configure an app for in-process hosting, add the `<AspNetCoreHostingModel>` property to the app's project file with a value of `InProcess` (out-of-process hosting is set with `OutOfProcess`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>InProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="837c0-370">Model hostování v rámci procesu není podporován pro aplikace ASP.NET Core, které cílí na .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="837c0-370">The in-process hosting model isn't supported for ASP.NET Core apps that target the .NET Framework.</span></span>

<span data-ttu-id="837c0-371">Hodnota rozlišuje `<AspNetCoreHostingModel>` malá a velká písmena, takže `inprocess` a `outofprocess` jsou platné hodnoty.</span><span class="sxs-lookup"><span data-stu-id="837c0-371">The value of `<AspNetCoreHostingModel>` is case insensitive, so `inprocess` and `outofprocess` are valid values.</span></span>

<span data-ttu-id="837c0-372">Pokud `<AspNetCoreHostingModel>` vlastnost není v souboru přítomna, výchozí hodnota je `OutOfProcess` .</span><span class="sxs-lookup"><span data-stu-id="837c0-372">If the `<AspNetCoreHostingModel>` property isn't present in the file, the default value is `OutOfProcess`.</span></span>

<span data-ttu-id="837c0-373">Následující vlastnosti platí při hostování v procesu:</span><span class="sxs-lookup"><span data-stu-id="837c0-373">The following characteristics apply when hosting in-process:</span></span>

* <span data-ttu-id="837c0-374">`IISHttpServer`Místo serveru [Kestrel](xref:fundamentals/servers/kestrel) se používá server HTTP služby IIS ().</span><span class="sxs-lookup"><span data-stu-id="837c0-374">IIS HTTP Server (`IISHttpServer`) is used instead of [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span> <span data-ttu-id="837c0-375">Pro vnitroprocesové [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) volání <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> :</span><span class="sxs-lookup"><span data-stu-id="837c0-375">For in-process, [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> to:</span></span>

  * <span data-ttu-id="837c0-376">Zaregistrujte `IISHttpServer` .</span><span class="sxs-lookup"><span data-stu-id="837c0-376">Register the `IISHttpServer`.</span></span>
  * <span data-ttu-id="837c0-377">Nakonfigurujte port a základní cestu, na kterých má Server naslouchat při spuštění za modulem ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="837c0-377">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
  * <span data-ttu-id="837c0-378">Nakonfigurujte hostitele tak, aby zachytával chyby při spuštění.</span><span class="sxs-lookup"><span data-stu-id="837c0-378">Configure the host to capture startup errors.</span></span>

* <span data-ttu-id="837c0-379">[Atribut RequestTimeout](#attributes-of-the-aspnetcore-element) se nevztahuje na hostování v rámci procesu.</span><span class="sxs-lookup"><span data-stu-id="837c0-379">The [requestTimeout attribute](#attributes-of-the-aspnetcore-element) doesn't apply to in-process hosting.</span></span>

* <span data-ttu-id="837c0-380">Sdílení fondu aplikací mezi aplikacemi není podporováno.</span><span class="sxs-lookup"><span data-stu-id="837c0-380">Sharing an app pool among apps isn't supported.</span></span> <span data-ttu-id="837c0-381">Použijte jeden fond aplikací na aplikaci.</span><span class="sxs-lookup"><span data-stu-id="837c0-381">Use one app pool per app.</span></span>

* <span data-ttu-id="837c0-382">Při použití [nasazení webu](/iis/publish/using-web-deploy/introduction-to-web-deploy) nebo ručního umístění [ souboruapp_offline.htm v nasazení](xref:host-and-deploy/iis/index#locked-deployment-files)se aplikace nemusí vypnout okamžitě, pokud dojde k otevřenému připojení.</span><span class="sxs-lookup"><span data-stu-id="837c0-382">When using [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) or manually placing an [app_offline.htm file in the deployment](xref:host-and-deploy/iis/index#locked-deployment-files), the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="837c0-383">Připojení k protokolu WebSocket může například zpozdit ukončení aplikace.</span><span class="sxs-lookup"><span data-stu-id="837c0-383">For example, a websocket connection may delay app shut down.</span></span>

* <span data-ttu-id="837c0-384">Architektura (bitová verze) aplikace a nainstalovaného modulu runtime (x64 nebo x86) se musí shodovat s architekturou fondu aplikací.</span><span class="sxs-lookup"><span data-stu-id="837c0-384">The architecture (bitness) of the app and installed runtime (x64 or x86) must match the architecture of the app pool.</span></span>

* <span data-ttu-id="837c0-385">Zjistila se odpojení klienta.</span><span class="sxs-lookup"><span data-stu-id="837c0-385">Client disconnects are detected.</span></span> <span data-ttu-id="837c0-386">Token zrušení [HttpContext. RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) je zrušený, když se klient odpojí.</span><span class="sxs-lookup"><span data-stu-id="837c0-386">The [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) cancellation token is cancelled when the client disconnects.</span></span>

* <span data-ttu-id="837c0-387">V ASP.NET Core 2.2.1 nebo dřívější <xref:System.IO.Directory.GetCurrentDirectory*> vrátí pracovní adresář procesu spuštěného službou IIS místo adresáře aplikace (například *C:\Windows\System32\inetsrv* pro *w3wp.exe*).</span><span class="sxs-lookup"><span data-stu-id="837c0-387">In ASP.NET Core 2.2.1 or earlier, <xref:System.IO.Directory.GetCurrentDirectory*> returns the worker directory of the process started by IIS rather than the app's directory (for example, *C:\Windows\System32\inetsrv* for *w3wp.exe*).</span></span>

  <span data-ttu-id="837c0-388">Vzorový kód, který nastaví aktuální adresář aplikace, najdete v tématu [Třída CurrentDirectoryHelpers](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/2.x/CurrentDirectoryHelpers.cs).</span><span class="sxs-lookup"><span data-stu-id="837c0-388">For sample code that sets the app's current directory, see the [CurrentDirectoryHelpers class](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/2.x/CurrentDirectoryHelpers.cs).</span></span> <span data-ttu-id="837c0-389">Zavolejte `SetCurrentDirectory` metodu.</span><span class="sxs-lookup"><span data-stu-id="837c0-389">Call the `SetCurrentDirectory` method.</span></span> <span data-ttu-id="837c0-390">Následná volání pro <xref:System.IO.Directory.GetCurrentDirectory*> poskytnutí adresáře aplikace.</span><span class="sxs-lookup"><span data-stu-id="837c0-390">Subsequent calls to <xref:System.IO.Directory.GetCurrentDirectory*> provide the app's directory.</span></span>

* <span data-ttu-id="837c0-391">Při hostování v procesu <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> není volána interně pro inicializaci uživatele.</span><span class="sxs-lookup"><span data-stu-id="837c0-391">When hosting in-process, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span> <span data-ttu-id="837c0-392">Proto <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementace používaná k transformaci deklarací po každém ověření není ve výchozím nastavení aktivována.</span><span class="sxs-lookup"><span data-stu-id="837c0-392">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="837c0-393">Při transformaci deklarací s <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementací zavolejte <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> na přidat ověřovací služby:</span><span class="sxs-lookup"><span data-stu-id="837c0-393">When transforming claims with an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation, call <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> to add authentication services:</span></span>

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

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="837c0-394">Model hostování mimo proces</span><span class="sxs-lookup"><span data-stu-id="837c0-394">Out-of-process hosting model</span></span>

<span data-ttu-id="837c0-395">Chcete-li nakonfigurovat aplikaci pro hostování mimo procesy, použijte některý z následujících přístupů v souboru projektu:</span><span class="sxs-lookup"><span data-stu-id="837c0-395">To configure an app for out-of-process hosting, use either of the following approaches in the project file:</span></span>

* <span data-ttu-id="837c0-396">Nezadávejte `<AspNetCoreHostingModel>` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="837c0-396">Don't specify the `<AspNetCoreHostingModel>` property.</span></span> <span data-ttu-id="837c0-397">Pokud `<AspNetCoreHostingModel>` vlastnost není v souboru přítomna, výchozí hodnota je `OutOfProcess` .</span><span class="sxs-lookup"><span data-stu-id="837c0-397">If the `<AspNetCoreHostingModel>` property isn't present in the file, the default value is `OutOfProcess`.</span></span>
* <span data-ttu-id="837c0-398">Nastavte hodnotu `<AspNetCoreHostingModel>` vlastnosti na `OutOfProcess` (vnitroprocesové hostování v procesu je nastaveno na `InProcess` ):</span><span class="sxs-lookup"><span data-stu-id="837c0-398">Set the value of the `<AspNetCoreHostingModel>` property to `OutOfProcess` (in-process hosting is set with `InProcess`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="837c0-399">Hodnota rozlišuje malá a velká písmena, takže `inprocess` a `outofprocess` jsou platné hodnoty.</span><span class="sxs-lookup"><span data-stu-id="837c0-399">The value is case insensitive, so `inprocess` and `outofprocess` are valid values.</span></span>

<span data-ttu-id="837c0-400">[Kestrel](xref:fundamentals/servers/kestrel) Místo http serveru IIS () se používá server Kestrel `IISHttpServer` .</span><span class="sxs-lookup"><span data-stu-id="837c0-400">[Kestrel](xref:fundamentals/servers/kestrel) server is used instead of IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="837c0-401">Pro mimoprocesové [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) volání <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> do:</span><span class="sxs-lookup"><span data-stu-id="837c0-401">For out-of-process, [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> to:</span></span>

* <span data-ttu-id="837c0-402">Nakonfigurujte port a základní cestu, na kterých má Server naslouchat při spuštění za modulem ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="837c0-402">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
* <span data-ttu-id="837c0-403">Nakonfigurujte hostitele tak, aby zachytával chyby při spuštění.</span><span class="sxs-lookup"><span data-stu-id="837c0-403">Configure the host to capture startup errors.</span></span>

### <a name="hosting-model-changes"></a><span data-ttu-id="837c0-404">Změny modelu hostování</span><span class="sxs-lookup"><span data-stu-id="837c0-404">Hosting model changes</span></span>

<span data-ttu-id="837c0-405">Pokud se `hostingModel` změní nastavení v souboru *web.config* (vysvětleno v části [konfigurace s web.config](#configuration-with-webconfig) ), modul recykluje pracovní proces pro službu IIS.</span><span class="sxs-lookup"><span data-stu-id="837c0-405">If the `hostingModel` setting is changed in the *web.config* file (explained in the [Configuration with web.config](#configuration-with-webconfig) section), the module recycles the worker process for IIS.</span></span>

<span data-ttu-id="837c0-406">V případě IIS Express modul neprovádí recyklaci pracovního procesu, ale místo toho aktivuje bezproblémové vypnutí aktuálního procesu IIS Express.</span><span class="sxs-lookup"><span data-stu-id="837c0-406">For IIS Express, the module doesn't recycle the worker process but instead triggers a graceful shutdown of the current IIS Express process.</span></span> <span data-ttu-id="837c0-407">Další požadavek na aplikaci vytvoří nový proces IIS Express.</span><span class="sxs-lookup"><span data-stu-id="837c0-407">The next request to the app spawns a new IIS Express process.</span></span>

### <a name="process-name"></a><span data-ttu-id="837c0-408">Název procesu</span><span class="sxs-lookup"><span data-stu-id="837c0-408">Process name</span></span>

<span data-ttu-id="837c0-409">`Process.GetCurrentProcess().ProcessName`sestavy `w3wp` / `iisexpress` (v procesu) nebo `dotnet` (mimo proces).</span><span class="sxs-lookup"><span data-stu-id="837c0-409">`Process.GetCurrentProcess().ProcessName` reports `w3wp`/`iisexpress` (in-process) or `dotnet` (out-of-process).</span></span>

<span data-ttu-id="837c0-410">Mnoho nativních modulů, jako je ověřování systému Windows, zůstane aktivní.</span><span class="sxs-lookup"><span data-stu-id="837c0-410">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="837c0-411">Další informace o aktivních modulech služby IIS pomocí modulu ASP.NET Core najdete v tématu <xref:host-and-deploy/iis/modules> .</span><span class="sxs-lookup"><span data-stu-id="837c0-411">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="837c0-412">Modul ASP.NET Core může také:</span><span class="sxs-lookup"><span data-stu-id="837c0-412">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="837c0-413">Nastavte proměnné prostředí pro pracovní proces.</span><span class="sxs-lookup"><span data-stu-id="837c0-413">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="837c0-414">Protokoluje výstup stdout do úložiště souborů pro řešení problémů při spuštění.</span><span class="sxs-lookup"><span data-stu-id="837c0-414">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="837c0-415">Předejte tokeny ověřování systému Windows.</span><span class="sxs-lookup"><span data-stu-id="837c0-415">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="837c0-416">Jak nainstalovat a používat modul ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="837c0-416">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="837c0-417">Pokyny k instalaci modulu ASP.NET Core najdete v tématu [instalace hostující sady .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span><span class="sxs-lookup"><span data-stu-id="837c0-417">For instructions on how to install the ASP.NET Core Module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="837c0-418">Konfigurace s web.config</span><span class="sxs-lookup"><span data-stu-id="837c0-418">Configuration with web.config</span></span>

<span data-ttu-id="837c0-419">Modul ASP.NET Core je nakonfigurován s `aspNetCore` částí `system.webServer` uzlu v souboru *web.config* webu.</span><span class="sxs-lookup"><span data-stu-id="837c0-419">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="837c0-420">Následující *web.config* soubor je publikován pro [nasazení závislé na rozhraní](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) a konfiguruje modul ASP.NET Core, aby zpracovával požadavky webu:</span><span class="sxs-lookup"><span data-stu-id="837c0-420">The following *web.config* file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

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

<span data-ttu-id="837c0-421">Následující *web.config* jsou publikovány pro [samostatně uzavřené nasazení](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span><span class="sxs-lookup"><span data-stu-id="837c0-421">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

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

<span data-ttu-id="837c0-422"><xref:System.Configuration.SectionInformation.InheritInChildApplications*>Vlastnost je nastavena na hodnotu `false` , která označuje, že nastavení zadaná v rámci [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) elementu nejsou děděna aplikacemi, které jsou umístěny v podadresáři aplikace.</span><span class="sxs-lookup"><span data-stu-id="837c0-422">The <xref:System.Configuration.SectionInformation.InheritInChildApplications*> property is set to `false` to indicate that the settings specified within the [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) element aren't inherited by apps that reside in a subdirectory of the app.</span></span>

<span data-ttu-id="837c0-423">Když je aplikace nasazená na [Azure App Service](https://azure.microsoft.com/services/app-service/), `stdoutLogFile` cesta je nastavená na `\\?\%home%\LogFiles\stdout` .</span><span class="sxs-lookup"><span data-stu-id="837c0-423">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="837c0-424">Cesta ukládá protokoly stdout do složky *soubory protokolů* , což je umístění, které služba automaticky vytvořila.</span><span class="sxs-lookup"><span data-stu-id="837c0-424">The path saves stdout logs to the *LogFiles* folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="837c0-425">Informace o konfiguraci dílčí aplikace IIS najdete v tématu <xref:host-and-deploy/iis/index#sub-applications> .</span><span class="sxs-lookup"><span data-stu-id="837c0-425">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="837c0-426">Atributy elementu aspNetCore</span><span class="sxs-lookup"><span data-stu-id="837c0-426">Attributes of the aspNetCore element</span></span>

| <span data-ttu-id="837c0-427">Atribut</span><span class="sxs-lookup"><span data-stu-id="837c0-427">Attribute</span></span> | <span data-ttu-id="837c0-428">Popis</span><span class="sxs-lookup"><span data-stu-id="837c0-428">Description</span></span> | <span data-ttu-id="837c0-429">Výchozí</span><span class="sxs-lookup"><span data-stu-id="837c0-429">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="837c0-430">Volitelný řetězcový atribut.</span><span class="sxs-lookup"><span data-stu-id="837c0-430">Optional string attribute.</span></span></p><p><span data-ttu-id="837c0-431">Argumenty ke spustitelnému souboru určenému v **processPath**.</span><span class="sxs-lookup"><span data-stu-id="837c0-431">Arguments to the executable specified in **processPath**.</span></span></p> | |
| `disableStartUpErrorPage` | <p><span data-ttu-id="837c0-432">Volitelný atribut typu Boolean.</span><span class="sxs-lookup"><span data-stu-id="837c0-432">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="837c0-433">Pokud je nastaveno na true, stránka **502,5-procesového selhání** se potlačí a stavová stránka 502, která je nakonfigurovaná ve *web.config* , má přednost.</span><span class="sxs-lookup"><span data-stu-id="837c0-433">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="837c0-434">Volitelný atribut typu Boolean.</span><span class="sxs-lookup"><span data-stu-id="837c0-434">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="837c0-435">Pokud je nastaveno na true, token se přepošle podřízenému procesu, který naslouchá na% ASPNETCORE_PORT% jako záhlaví MS-ASPNETCORE-WINAUTHTOKEN na žádost.</span><span class="sxs-lookup"><span data-stu-id="837c0-435">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="837c0-436">Je zodpovědností za tento proces volání CloseHandle na tento token na žádost.</span><span class="sxs-lookup"><span data-stu-id="837c0-436">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `hostingModel` | <p><span data-ttu-id="837c0-437">Volitelný řetězcový atribut.</span><span class="sxs-lookup"><span data-stu-id="837c0-437">Optional string attribute.</span></span></p><p><span data-ttu-id="837c0-438">Určuje model hostování jako vnitroprocesové ( `InProcess` / `inprocess` ) nebo mimo proces ( `OutOfProcess` / `outofprocess` ).</span><span class="sxs-lookup"><span data-stu-id="837c0-438">Specifies the hosting model as in-process (`InProcess`/`inprocess`) or out-of-process (`OutOfProcess`/`outofprocess`).</span></span></p> | `OutOfProcess`<br>`outofprocess` |
| `processesPerApplication` | <p><span data-ttu-id="837c0-439">Volitelný celočíselný atribut</span><span class="sxs-lookup"><span data-stu-id="837c0-439">Optional integer attribute.</span></span></p><p><span data-ttu-id="837c0-440">Určuje počet instancí procesu určeného v nastavení **processPath** , které lze v rámci aplikace vystavit.</span><span class="sxs-lookup"><span data-stu-id="837c0-440">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="837c0-441">&dagger;Pro hostování v rámci procesu je hodnota omezená na `1` .</span><span class="sxs-lookup"><span data-stu-id="837c0-441">&dagger;For in-process hosting, the value is limited to `1`.</span></span></p><p><span data-ttu-id="837c0-442">Nastavení `processesPerApplication` se nedoporučuje.</span><span class="sxs-lookup"><span data-stu-id="837c0-442">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="837c0-443">Tento atribut bude v budoucí verzi odebrán.</span><span class="sxs-lookup"><span data-stu-id="837c0-443">This attribute will be removed in a future release.</span></span></p> | <span data-ttu-id="837c0-444">Výchozí `1`</span><span class="sxs-lookup"><span data-stu-id="837c0-444">Default: `1`</span></span><br><span data-ttu-id="837c0-445">Dlouhé `1`</span><span class="sxs-lookup"><span data-stu-id="837c0-445">Min: `1`</span></span><br><span data-ttu-id="837c0-446">Počet `100`&dagger;</span><span class="sxs-lookup"><span data-stu-id="837c0-446">Max: `100`&dagger;</span></span> |
| `processPath` | <p><span data-ttu-id="837c0-447">Povinný atribut řetězce.</span><span class="sxs-lookup"><span data-stu-id="837c0-447">Required string attribute.</span></span></p><p><span data-ttu-id="837c0-448">Cesta ke spustitelnému souboru, který spouští proces naslouchající na požadavky HTTP.</span><span class="sxs-lookup"><span data-stu-id="837c0-448">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="837c0-449">Jsou podporovány relativní cesty.</span><span class="sxs-lookup"><span data-stu-id="837c0-449">Relative paths are supported.</span></span> <span data-ttu-id="837c0-450">Pokud cesta začíná `.` , bude tato cesta považována za relativní vzhledem k kořenu webu.</span><span class="sxs-lookup"><span data-stu-id="837c0-450">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="837c0-451">Volitelný celočíselný atribut</span><span class="sxs-lookup"><span data-stu-id="837c0-451">Optional integer attribute.</span></span></p><p><span data-ttu-id="837c0-452">Určuje, kolikrát je povoleno selhání procesu určeného v **processPath** za minutu.</span><span class="sxs-lookup"><span data-stu-id="837c0-452">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="837c0-453">Pokud je tento limit překročen, modul ukončí spuštění procesu po zbytek minuty.</span><span class="sxs-lookup"><span data-stu-id="837c0-453">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p><p><span data-ttu-id="837c0-454">Nepodporováno v hostování v rámci procesu.</span><span class="sxs-lookup"><span data-stu-id="837c0-454">Not supported with in-process hosting.</span></span></p> | <span data-ttu-id="837c0-455">Výchozí `10`</span><span class="sxs-lookup"><span data-stu-id="837c0-455">Default: `10`</span></span><br><span data-ttu-id="837c0-456">Dlouhé `0`</span><span class="sxs-lookup"><span data-stu-id="837c0-456">Min: `0`</span></span><br><span data-ttu-id="837c0-457">Počet `100`</span><span class="sxs-lookup"><span data-stu-id="837c0-457">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="837c0-458">Volitelný atribut TimeSpan.</span><span class="sxs-lookup"><span data-stu-id="837c0-458">Optional timespan attribute.</span></span></p><p><span data-ttu-id="837c0-459">Určuje dobu, po kterou modul ASP.NET Core čeká na odpověď od procesu, který naslouchá na% ASPNETCORE_PORT%.</span><span class="sxs-lookup"><span data-stu-id="837c0-459">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="837c0-460">Ve verzích modulu ASP.NET Core, které byly dodávány s vydáním ASP.NET Core 2,1 nebo vyšší, `requestTimeout` je určena v hodinách, minutách a sekundách.</span><span class="sxs-lookup"><span data-stu-id="837c0-460">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p><p><span data-ttu-id="837c0-461">Neplatí pro hostování v procesu.</span><span class="sxs-lookup"><span data-stu-id="837c0-461">Doesn't apply to in-process hosting.</span></span> <span data-ttu-id="837c0-462">Pro hostování v rámci procesu modul čeká, až aplikace zpracuje požadavek.</span><span class="sxs-lookup"><span data-stu-id="837c0-462">For in-process hosting, the module waits for the app to process the request.</span></span></p><p><span data-ttu-id="837c0-463">Platné hodnoty segmentů minut a sekund řetězce jsou v rozsahu 0-59.</span><span class="sxs-lookup"><span data-stu-id="837c0-463">Valid values for minutes and seconds segments of the string are in the range 0-59.</span></span> <span data-ttu-id="837c0-464">Při použití hodnoty **60** v hodnotě minut nebo sekund dojde k chybě systému *500 – interní chyba serveru*.</span><span class="sxs-lookup"><span data-stu-id="837c0-464">Use of **60** in the value for minutes or seconds results in a *500 - Internal Server Error*.</span></span></p> | <span data-ttu-id="837c0-465">Výchozí `00:02:00`</span><span class="sxs-lookup"><span data-stu-id="837c0-465">Default: `00:02:00`</span></span><br><span data-ttu-id="837c0-466">Dlouhé `00:00:00`</span><span class="sxs-lookup"><span data-stu-id="837c0-466">Min: `00:00:00`</span></span><br><span data-ttu-id="837c0-467">Počet `360:00:00`</span><span class="sxs-lookup"><span data-stu-id="837c0-467">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="837c0-468">Volitelný celočíselný atribut</span><span class="sxs-lookup"><span data-stu-id="837c0-468">Optional integer attribute.</span></span></p><p><span data-ttu-id="837c0-469">Doba v sekundách, po kterou modul čeká na řádné vypnutí spustitelného souboru, když se zjistí soubor *app_offline.htm* .</span><span class="sxs-lookup"><span data-stu-id="837c0-469">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="837c0-470">Výchozí `10`</span><span class="sxs-lookup"><span data-stu-id="837c0-470">Default: `10`</span></span><br><span data-ttu-id="837c0-471">Dlouhé `0`</span><span class="sxs-lookup"><span data-stu-id="837c0-471">Min: `0`</span></span><br><span data-ttu-id="837c0-472">Počet `600`</span><span class="sxs-lookup"><span data-stu-id="837c0-472">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="837c0-473">Volitelný celočíselný atribut</span><span class="sxs-lookup"><span data-stu-id="837c0-473">Optional integer attribute.</span></span></p><p><span data-ttu-id="837c0-474">Doba v sekundách, kterou modul počká, než spustitelný soubor spustí proces, který na portu naslouchá.</span><span class="sxs-lookup"><span data-stu-id="837c0-474">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="837c0-475">Pokud je tento časový limit překročen, modul proces ukončuje.</span><span class="sxs-lookup"><span data-stu-id="837c0-475">If this time limit is exceeded, the module kills the process.</span></span></p><p><span data-ttu-id="837c0-476">Při hostování *v procesu*: proces **není restartován a** **nepoužívá nastavení** **rapidFailsPerMinute** .</span><span class="sxs-lookup"><span data-stu-id="837c0-476">When hosting *in-process*: The process is **not** restarted and does **not** use the **rapidFailsPerMinute** setting.</span></span></p><p><span data-ttu-id="837c0-477">Při hostování *mimo proces*: modul se pokusí znovu spustit proces, když obdrží novou žádost, a pokračuje v pokusu o restartování procesu u dalších příchozích požadavků, pokud se aplikaci nepodaří spustit **rapidFailsPerMinute** počet pokusů během poslední minuty.</span><span class="sxs-lookup"><span data-stu-id="837c0-477">When hosting *out-of-process*: The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="837c0-478">Hodnota 0 (nula **) není považována za** nekonečný časový limit.</span><span class="sxs-lookup"><span data-stu-id="837c0-478">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="837c0-479">Výchozí `120`</span><span class="sxs-lookup"><span data-stu-id="837c0-479">Default: `120`</span></span><br><span data-ttu-id="837c0-480">Dlouhé `0`</span><span class="sxs-lookup"><span data-stu-id="837c0-480">Min: `0`</span></span><br><span data-ttu-id="837c0-481">Počet `3600`</span><span class="sxs-lookup"><span data-stu-id="837c0-481">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="837c0-482">Volitelný atribut typu Boolean.</span><span class="sxs-lookup"><span data-stu-id="837c0-482">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="837c0-483">Je-li nastavena hodnota true, **stdout** a **stderr** pro proces zadaný v **processPath** budou přesměrovány do souboru zadaného v **stdoutLogFile**.</span><span class="sxs-lookup"><span data-stu-id="837c0-483">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="837c0-484">Volitelný řetězcový atribut.</span><span class="sxs-lookup"><span data-stu-id="837c0-484">Optional string attribute.</span></span></p><p><span data-ttu-id="837c0-485">Určuje relativní nebo absolutní cestu k souboru, pro který je **stdout** a **stderr** z procesu určeného v **processPath** protokolováno.</span><span class="sxs-lookup"><span data-stu-id="837c0-485">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="837c0-486">Relativní cesty jsou relativní vzhledem k kořenu webu.</span><span class="sxs-lookup"><span data-stu-id="837c0-486">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="837c0-487">Každá cesta začínající řetězcem `.` je relativní vzhledem k kořenu webu a všechny ostatní cesty se považují za absolutní cesty.</span><span class="sxs-lookup"><span data-stu-id="837c0-487">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="837c0-488">Při vytvoření souboru protokolu se v modulu vytvoří všechny složky, které jsou v cestě zadané.</span><span class="sxs-lookup"><span data-stu-id="837c0-488">Any folders provided in the path are created by the module when the log file is created.</span></span> <span data-ttu-id="837c0-489">Pomocí oddělovačů podtržítka se do posledního segmentu cesty **stdoutLogFile** přidá časové razítko, ID procesu a Přípona souboru (*. log*).</span><span class="sxs-lookup"><span data-stu-id="837c0-489">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="837c0-490">Pokud `.\logs\stdout` je zadán jako hodnota, je ukázkový protokol stdout uložen jako *stdout_20180205194132_1934. log* ve složce *logs* při uložení na 2/5/2018 v 19:41:32 s ID procesu 1934.</span><span class="sxs-lookup"><span data-stu-id="837c0-490">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

### <a name="setting-environment-variables"></a><span data-ttu-id="837c0-491">Nastavení proměnných prostředí</span><span class="sxs-lookup"><span data-stu-id="837c0-491">Setting environment variables</span></span>

<span data-ttu-id="837c0-492">Proměnné prostředí lze zadat pro proces v `processPath` atributu.</span><span class="sxs-lookup"><span data-stu-id="837c0-492">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="837c0-493">Zadejte proměnnou prostředí s `<environmentVariable>` podřízeným elementem `<environmentVariables>` elementu kolekce.</span><span class="sxs-lookup"><span data-stu-id="837c0-493">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span> <span data-ttu-id="837c0-494">Proměnné prostředí nastavené v této části mají přednost před proměnnými prostředí systému.</span><span class="sxs-lookup"><span data-stu-id="837c0-494">Environment variables set in this section take precedence over system environment variables.</span></span>

<span data-ttu-id="837c0-495">Následující příklad nastaví dvě proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="837c0-495">The following example sets two environment variables.</span></span> <span data-ttu-id="837c0-496">`ASPNETCORE_ENVIRONMENT` nakonfiguruje prostředí aplikace na `Development` .</span><span class="sxs-lookup"><span data-stu-id="837c0-496">`ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="837c0-497">Vývojář může tuto hodnotu dočasně nastavit v souboru *web.config* , aby bylo možné vynutit, aby se [Stránka s výjimkou vývojářů](xref:fundamentals/error-handling) načetla při ladění výjimky aplikace.</span><span class="sxs-lookup"><span data-stu-id="837c0-497">A developer may temporarily set this value in the *web.config* file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="837c0-498">`CONFIG_DIR` je příkladem uživatelsky definované proměnné prostředí, kde vývojář napsal kód, který přečte hodnotu při spuštění, aby vytvořil cestu pro načtení konfiguračního souboru aplikace.</span><span class="sxs-lookup"><span data-stu-id="837c0-498">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

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
> <span data-ttu-id="837c0-499">Alternativou k nastavení prostředí přímo v *web.config* je zahrnutí `<EnvironmentName>` vlastnosti do [profilu publikování (. pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) nebo souboru projektu.</span><span class="sxs-lookup"><span data-stu-id="837c0-499">An alternative to setting the environment directly in *web.config* is to include the `<EnvironmentName>` property in the [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) or project file.</span></span> <span data-ttu-id="837c0-500">Tento přístup nastaví prostředí v *web.config* při publikování projektu:</span><span class="sxs-lookup"><span data-stu-id="837c0-500">This approach sets the environment in *web.config* when the project is published:</span></span>
>
> ```xml
> <PropertyGroup>
>   <EnvironmentName>Development</EnvironmentName>
> </PropertyGroup>
> ```

> [!WARNING]
> <span data-ttu-id="837c0-501">Nastavte pouze `ASPNETCORE_ENVIRONMENT` proměnnou prostředí na `Development` pracovní a testovací servery, které nejsou přístupné nedůvěryhodným sítím, jako je například Internet.</span><span class="sxs-lookup"><span data-stu-id="837c0-501">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## <a name="app_offlinehtm"></a><span data-ttu-id="837c0-502">app_offline.htm</span><span class="sxs-lookup"><span data-stu-id="837c0-502">app_offline.htm</span></span>

<span data-ttu-id="837c0-503">Pokud se v kořenovém adresáři aplikace zjistí soubor s názvem *app_offline.htm* , modul ASP.NET Core se pokusí aplikaci řádně vypnout a zastavit zpracování příchozích požadavků.</span><span class="sxs-lookup"><span data-stu-id="837c0-503">If a file with the name *app_offline.htm* is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="837c0-504">Pokud aplikace běží i po uplynutí počtu sekund definovaného v `shutdownTimeLimit` , modul ASP.NET Core ukončuje běžící proces.</span><span class="sxs-lookup"><span data-stu-id="837c0-504">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="837c0-505">Když je přítomen soubor *app_offline.htm* , ASP.NET Core modul reaguje na požadavky odesláním zpět obsahu *app_offline.htm* souboru.</span><span class="sxs-lookup"><span data-stu-id="837c0-505">While the *app_offline.htm* file is present, the ASP.NET Core Module responds to requests by sending back the contents of the *app_offline.htm* file.</span></span> <span data-ttu-id="837c0-506">Po odebrání souboru *app_offline.htm* spustí aplikace další požadavek.</span><span class="sxs-lookup"><span data-stu-id="837c0-506">When the *app_offline.htm* file is removed, the next request starts the app.</span></span>

<span data-ttu-id="837c0-507">Při použití nezpracovaného modelu hostování se aplikace nemusí okamžitě vypnout, pokud dojde k otevřenému připojení.</span><span class="sxs-lookup"><span data-stu-id="837c0-507">When using the out-of-process hosting model, the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="837c0-508">Připojení k protokolu WebSocket může například zpozdit ukončení aplikace.</span><span class="sxs-lookup"><span data-stu-id="837c0-508">For example, a websocket connection may delay app shut down.</span></span>

## <a name="start-up-error-page"></a><span data-ttu-id="837c0-509">Spouštěcí chybová stránka</span><span class="sxs-lookup"><span data-stu-id="837c0-509">Start-up error page</span></span>

<span data-ttu-id="837c0-510">Hostování v procesu i mimo procesy vytváří vlastní chybové stránky, když se nepodaří aplikaci spustit.</span><span class="sxs-lookup"><span data-stu-id="837c0-510">Both in-process and out-of-process hosting produce custom error pages when they fail to start the app.</span></span>

<span data-ttu-id="837c0-511">Pokud modul ASP.NET Core nenalezne obslužnou rutinu žádosti v procesu nebo mimo proces 500,0, zobrazí se stránka s kódem stavu *selhání načtení obslužné rutiny v* procesu nebo mimo proces.</span><span class="sxs-lookup"><span data-stu-id="837c0-511">If the ASP.NET Core Module fails to find either the in-process or out-of-process request handler, a *500.0 - In-Process/Out-Of-Process Handler Load Failure* status code page appears.</span></span>

<span data-ttu-id="837c0-512">Pro vnitroprocesové hostování v případě, že se modul ASP.NET Core nepodaří spustit aplikaci, zobrazí se stránka s kódem stavu *chyby při spuštění 500,30* .</span><span class="sxs-lookup"><span data-stu-id="837c0-512">For in-process hosting if the ASP.NET Core Module fails to start the app, a *500.30 - Start Failure* status code page appears.</span></span>

<span data-ttu-id="837c0-513">Pro hostování mimo procesy, pokud se ASP.NET Core modulu nespustí back-end proces, nebo dojde k selhání procesu back-endu, ale nenaslouchá na konfigurovaném portu, zobrazí se stránka s kódem stavu *selhání procesu 502,5* .</span><span class="sxs-lookup"><span data-stu-id="837c0-513">For out-of-process hosting if the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span>

<span data-ttu-id="837c0-514">Chcete-li tuto stránku potlačit a vrátit se na výchozí stavovou stránku služby IIS 5xx, použijte `disableStartUpErrorPage` atribut.</span><span class="sxs-lookup"><span data-stu-id="837c0-514">To suppress this page and revert to the default IIS 5xx status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="837c0-515">Další informace o konfiguraci vlastních chybových zpráv najdete v tématu [chyby \<httpErrors> protokolu HTTP ](/iis/configuration/system.webServer/httpErrors/).</span><span class="sxs-lookup"><span data-stu-id="837c0-515">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

## <a name="log-creation-and-redirection"></a><span data-ttu-id="837c0-516">Vytvoření a přesměrování protokolu</span><span class="sxs-lookup"><span data-stu-id="837c0-516">Log creation and redirection</span></span>

<span data-ttu-id="837c0-517">Modul ASP.NET Core přesměruje výstup konzoly stdout a stderr na disk, pokud `stdoutLogEnabled` `stdoutLogFile` `aspNetCore` jsou nastaveny atributy a elementu.</span><span class="sxs-lookup"><span data-stu-id="837c0-517">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="837c0-518">`stdoutLogFile`Při vytvoření souboru protokolu jsou všechny složky v této cestě vytvářeny modulem.</span><span class="sxs-lookup"><span data-stu-id="837c0-518">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="837c0-519">Fond aplikací musí mít oprávnění k zápisu do umístění, kam se protokoly zapisují (použijte `IIS AppPool\<app_pool_name>` k poskytnutí oprávnění k zápisu).</span><span class="sxs-lookup"><span data-stu-id="837c0-519">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="837c0-520">Protokoly nejsou otočeny, pokud nedochází k recyklování/restartování procesu.</span><span class="sxs-lookup"><span data-stu-id="837c0-520">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="837c0-521">Je zodpovědností hostitele omezit místo na disku, které používají protokoly.</span><span class="sxs-lookup"><span data-stu-id="837c0-521">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="837c0-522">Použití protokolu stdout se doporučuje pouze při řešení potíží se spouštěním aplikací při hostování služby IIS nebo při použití [podpory v době vývoje služby IIS se sadou Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), nikoli při ladění místně a při spuštění aplikace s IIS Express.</span><span class="sxs-lookup"><span data-stu-id="837c0-522">Using the stdout log is only recommended for troubleshooting app startup issues when hosting on IIS or when using [development-time support for IIS with Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), not while debugging locally and running the app with IIS Express.</span></span>

<span data-ttu-id="837c0-523">Nepoužívejte protokol stdout pro obecné účely protokolování aplikace.</span><span class="sxs-lookup"><span data-stu-id="837c0-523">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="837c0-524">Pro rutinu protokolování v aplikaci ASP.NET Core použijte knihovnu protokolování, která omezuje velikost souboru protokolu a otočí protokoly.</span><span class="sxs-lookup"><span data-stu-id="837c0-524">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="837c0-525">Další informace najdete v tématu [Zprostředkovatelé protokolování třetích stran](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="837c0-525">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="837c0-526">Časové razítko a Přípona souboru se automaticky přidají při vytvoření souboru protokolu.</span><span class="sxs-lookup"><span data-stu-id="837c0-526">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="837c0-527">Název souboru protokolu se skládá připojením časového razítka, ID procesu a přípony souboru (*. log*) k poslednímu segmentu `stdoutLogFile` cesty (obvykle *stdout*), oddělený podtržítky.</span><span class="sxs-lookup"><span data-stu-id="837c0-527">The log file name is composed by appending the timestamp, process ID, and file extension (*.log*) to the last segment of the `stdoutLogFile` path (typically *stdout*) delimited by underscores.</span></span> <span data-ttu-id="837c0-528">Pokud `stdoutLogFile` cesta končí na *stdout*, protokol pro aplikaci s číslem PID 1934 vytvořeným v 2/5/2018 v 19:42:32 má název souboru *stdout_20180205194132_1934. log*.</span><span class="sxs-lookup"><span data-stu-id="837c0-528">If the `stdoutLogFile` path ends with *stdout*, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name *stdout_20180205194132_1934.log*.</span></span>

<span data-ttu-id="837c0-529">Pokud `stdoutLogEnabled` je hodnota false, chyby, ke kterým dochází při spuštění aplikace, se zaznamenávají a generují do protokolu událostí až do 30 kB.</span><span class="sxs-lookup"><span data-stu-id="837c0-529">If `stdoutLogEnabled` is false, errors that occur on app startup are captured and emitted to the event log up to 30 KB.</span></span> <span data-ttu-id="837c0-530">Po spuštění budou všechny další protokoly zahozeny.</span><span class="sxs-lookup"><span data-stu-id="837c0-530">After startup, all additional logs are discarded.</span></span>

<span data-ttu-id="837c0-531">Následující vzorový `aspNetCore` prvek nakonfiguruje protokolování stdout na relativní cestě `.\log\` .</span><span class="sxs-lookup"><span data-stu-id="837c0-531">The following sample `aspNetCore` element configures stdout logging at the relative path `.\log\`.</span></span> <span data-ttu-id="837c0-532">Potvrďte, že identita uživatele fondu aplikací má oprávnění k zápisu do zadané cesty.</span><span class="sxs-lookup"><span data-stu-id="837c0-532">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout"
    hostingModel="inprocess">
</aspNetCore>
```

<span data-ttu-id="837c0-533">Když publikujete aplikaci pro nasazení Azure App Service, sada web SDK nastaví `stdoutLogFile` hodnotu na `\\?\%home%\LogFiles\stdout` .</span><span class="sxs-lookup"><span data-stu-id="837c0-533">When publishing an app for Azure App Service deployment, the Web SDK sets the `stdoutLogFile` value to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="837c0-534">`%home`Proměnná prostředí je předdefinovaná pro aplikace hostované v Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="837c0-534">The `%home` environment variable is predefined for apps hosted by Azure App Service.</span></span>

<span data-ttu-id="837c0-535">Další informace o formátech cest najdete v tématu [formáty souborů cest v systémech Windows](/dotnet/standard/io/file-path-formats).</span><span class="sxs-lookup"><span data-stu-id="837c0-535">For more information on path formats, see [File path formats on Windows systems](/dotnet/standard/io/file-path-formats).</span></span>

## <a name="enhanced-diagnostic-logs"></a><span data-ttu-id="837c0-536">Rozšířené diagnostické protokoly</span><span class="sxs-lookup"><span data-stu-id="837c0-536">Enhanced diagnostic logs</span></span>

<span data-ttu-id="837c0-537">Modul ASP.NET Core lze nakonfigurovat tak, aby poskytoval rozšířené diagnostické protokoly.</span><span class="sxs-lookup"><span data-stu-id="837c0-537">The ASP.NET Core Module is configurable to provide enhanced diagnostics logs.</span></span> <span data-ttu-id="837c0-538">Přidejte `<handlerSettings>` element do `<aspNetCore>` prvku v *web.config*. Nastavení `debugLevel` pro `TRACE` zpřístupňuje vyšší věrnost diagnostických informací:</span><span class="sxs-lookup"><span data-stu-id="837c0-538">Add the `<handlerSettings>` element to the `<aspNetCore>` element in *web.config*. Setting the `debugLevel` to `TRACE` exposes a higher fidelity of diagnostic information:</span></span>

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

<span data-ttu-id="837c0-539">Složky v cestě poskytnuté `<handlerSetting>` hodnotě (*protokoly* v předchozím příkladu) nejsou vytvářeny modulem automaticky a měly by být v nasazení předběžně existující.</span><span class="sxs-lookup"><span data-stu-id="837c0-539">Folders in the path provided to the `<handlerSetting>` value (*logs* in the preceding example) aren't created by the module automatically and should pre-exist in the deployment.</span></span> <span data-ttu-id="837c0-540">Fond aplikací musí mít oprávnění k zápisu do umístění, kam se protokoly zapisují (použijte `IIS AppPool\<app_pool_name>` k poskytnutí oprávnění k zápisu).</span><span class="sxs-lookup"><span data-stu-id="837c0-540">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="837c0-541">Hodnoty úrovně ladění ( `debugLevel` ) mohou zahrnovat jak úroveň, tak i umístění.</span><span class="sxs-lookup"><span data-stu-id="837c0-541">Debug level (`debugLevel`) values can include both the level and the location.</span></span>

<span data-ttu-id="837c0-542">Úrovně (v pořadí od nejméně po nejpodrobnější):</span><span class="sxs-lookup"><span data-stu-id="837c0-542">Levels (in order from least to most verbose):</span></span>

* <span data-ttu-id="837c0-543">CHYBA</span><span class="sxs-lookup"><span data-stu-id="837c0-543">ERROR</span></span>
* <span data-ttu-id="837c0-544">WARNING</span><span class="sxs-lookup"><span data-stu-id="837c0-544">WARNING</span></span>
* <span data-ttu-id="837c0-545">PŘÍJEMCE</span><span class="sxs-lookup"><span data-stu-id="837c0-545">INFO</span></span>
* <span data-ttu-id="837c0-546">TRACE</span><span class="sxs-lookup"><span data-stu-id="837c0-546">TRACE</span></span>

<span data-ttu-id="837c0-547">Umístění (je povoleno více umístění):</span><span class="sxs-lookup"><span data-stu-id="837c0-547">Locations (multiple locations are permitted):</span></span>

* <span data-ttu-id="837c0-548">STROMU</span><span class="sxs-lookup"><span data-stu-id="837c0-548">CONSOLE</span></span>
* <span data-ttu-id="837c0-549">EVENTLOG</span><span class="sxs-lookup"><span data-stu-id="837c0-549">EVENTLOG</span></span>
* <span data-ttu-id="837c0-550">SOUBOR</span><span class="sxs-lookup"><span data-stu-id="837c0-550">FILE</span></span>

<span data-ttu-id="837c0-551">Nastavení obslužné rutiny lze také poskytnout pomocí proměnných prostředí:</span><span class="sxs-lookup"><span data-stu-id="837c0-551">The handler settings can also be provided via environment variables:</span></span>

* <span data-ttu-id="837c0-552">`ASPNETCORE_MODULE_DEBUG_FILE`: Cesta k souboru protokolu ladění.</span><span class="sxs-lookup"><span data-stu-id="837c0-552">`ASPNETCORE_MODULE_DEBUG_FILE`: Path to the debug log file.</span></span> <span data-ttu-id="837c0-553">(Výchozí: *aspnetcore-Debug. log*)</span><span class="sxs-lookup"><span data-stu-id="837c0-553">(Default: *aspnetcore-debug.log*)</span></span>
* <span data-ttu-id="837c0-554">`ASPNETCORE_MODULE_DEBUG`: Nastavení úrovně ladění.</span><span class="sxs-lookup"><span data-stu-id="837c0-554">`ASPNETCORE_MODULE_DEBUG`: Debug level setting.</span></span>

> [!WARNING]
> <span data-ttu-id="837c0-555">Nenechávejte **protokolování** ladění povolené v nasazení po delší dobu, než je potřeba k řešení problému.</span><span class="sxs-lookup"><span data-stu-id="837c0-555">Do **not** leave debug logging enabled in the deployment for longer than required to troubleshoot an issue.</span></span> <span data-ttu-id="837c0-556">Velikost protokolu není omezená.</span><span class="sxs-lookup"><span data-stu-id="837c0-556">The size of the log isn't limited.</span></span> <span data-ttu-id="837c0-557">Když necháte protokol ladění povolený, může dojít k vyčerpání dostupného místa na disku a selhání serveru nebo služby App Service.</span><span class="sxs-lookup"><span data-stu-id="837c0-557">Leaving the debug log enabled can exhaust the available disk space and crash the server or app service.</span></span>

<span data-ttu-id="837c0-558">Příklad prvku v souboruweb.confignaleznete v tématu [konfigurace s web.config](#configuration-with-webconfig) `aspNetCore` . *web.config*</span><span class="sxs-lookup"><span data-stu-id="837c0-558">See [Configuration with web.config](#configuration-with-webconfig) for an example of the `aspNetCore` element in the *web.config* file.</span></span>

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="837c0-559">Konfigurace proxy serveru používá protokol HTTP a token párování.</span><span class="sxs-lookup"><span data-stu-id="837c0-559">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="837c0-560">*Platí pouze pro hostování mimo proces.*</span><span class="sxs-lookup"><span data-stu-id="837c0-560">*Only applies to out-of-process hosting.*</span></span>

<span data-ttu-id="837c0-561">Proxy vytvořený mezi modulem ASP.NET Core a Kestrel používá protokol HTTP.</span><span class="sxs-lookup"><span data-stu-id="837c0-561">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="837c0-562">Nehrozí riziko odposlouchávání provozu mezi modulem a Kestrel z umístění od serveru.</span><span class="sxs-lookup"><span data-stu-id="837c0-562">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="837c0-563">Párovací token se používá k zajištění, že žádosti přijaté službou Kestrel byly proxy službou IIS a nepocházejí z jiného zdroje.</span><span class="sxs-lookup"><span data-stu-id="837c0-563">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="837c0-564">Token párování se vytvoří a nastaví na proměnnou prostředí ( `ASPNETCORE_TOKEN` ) modulem.</span><span class="sxs-lookup"><span data-stu-id="837c0-564">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="837c0-565">Párovací token je také nastaven na hlavičku ( `MS-ASPNETCORE-TOKEN` ) pro každý požadavek proxy.</span><span class="sxs-lookup"><span data-stu-id="837c0-565">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="837c0-566">Middleware služby IIS kontroluje každý požadavek, který obdrží, a potvrdí, že hodnota hlavičky párového tokenu odpovídá hodnotě proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="837c0-566">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="837c0-567">Pokud se hodnoty tokenu neshodují, požadavek se zaprotokoluje a odmítne.</span><span class="sxs-lookup"><span data-stu-id="837c0-567">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="837c0-568">Proměnná prostředí párového tokenu a přenos dat mezi modulem a Kestrel nejsou přístupné z umístění mimo server.</span><span class="sxs-lookup"><span data-stu-id="837c0-568">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="837c0-569">Bez znalosti hodnoty párovacího tokenu nemůže útočník odeslat žádosti, které obcházejí kontrolu v Middlewari IIS.</span><span class="sxs-lookup"><span data-stu-id="837c0-569">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="837c0-570">ASP.NET Core modul se sdílenou konfigurací služby IIS</span><span class="sxs-lookup"><span data-stu-id="837c0-570">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="837c0-571">Instalační služba modulu ASP.NET Core se spouští s oprávněními účtu **TrustedInstaller** .</span><span class="sxs-lookup"><span data-stu-id="837c0-571">The ASP.NET Core Module installer runs with the privileges of the **TrustedInstaller** account.</span></span> <span data-ttu-id="837c0-572">Vzhledem k tomu, že účet místního systému nemá oprávnění k úpravám pro cestu ke sdílené složce, kterou používá sdílená konfigurace služby IIS, vyvolá Instalační program chybu odepření přístupu při pokusu o konfiguraci nastavení modulu v souboru *applicationHost.config* ve sdílené složce.</span><span class="sxs-lookup"><span data-stu-id="837c0-572">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the *applicationHost.config* file on the share.</span></span>

<span data-ttu-id="837c0-573">Pokud používáte konfiguraci IIS Shared na stejném počítači jako instalaci služby IIS, spusťte instalační program sady prostředků ASP.NET Core s `OPT_NO_SHARED_CONFIG_CHECK` parametrem nastaveným na `1` :</span><span class="sxs-lookup"><span data-stu-id="837c0-573">When using an IIS Shared Configuration on the same machine as the IIS installation, run the ASP.NET Core Hosting Bundle installer with the `OPT_NO_SHARED_CONFIG_CHECK` parameter set to `1`:</span></span>

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

<span data-ttu-id="837c0-574">Pokud cesta ke sdílené konfiguraci není ve stejném počítači jako instalace služby IIS, postupujte podle těchto kroků:</span><span class="sxs-lookup"><span data-stu-id="837c0-574">When the path to the shared configuration isn't on the same machine as the IIS installation, follow these steps:</span></span>

1. <span data-ttu-id="837c0-575">Zakažte sdílenou konfiguraci služby IIS.</span><span class="sxs-lookup"><span data-stu-id="837c0-575">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="837c0-576">Spusťte instalační program.</span><span class="sxs-lookup"><span data-stu-id="837c0-576">Run the installer.</span></span>
1. <span data-ttu-id="837c0-577">Exportujte aktualizovaný soubor *applicationHost.config* do sdílené složky.</span><span class="sxs-lookup"><span data-stu-id="837c0-577">Export the updated *applicationHost.config* file to the share.</span></span>
1. <span data-ttu-id="837c0-578">Znovu povolte sdílenou konfiguraci služby IIS.</span><span class="sxs-lookup"><span data-stu-id="837c0-578">Re-enable the IIS Shared Configuration.</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="837c0-579">Verze modulu a instalační protokoly hostitelské sady</span><span class="sxs-lookup"><span data-stu-id="837c0-579">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="837c0-580">Postup určení verze nainstalovaného modulu ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="837c0-580">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="837c0-581">V hostitelském systému přejděte na *%windir%\system32\inetsrv*.</span><span class="sxs-lookup"><span data-stu-id="837c0-581">On the hosting system, navigate to *%windir%\System32\inetsrv*.</span></span>
1. <span data-ttu-id="837c0-582">Vyhledejte soubor *aspnetcore.dll* .</span><span class="sxs-lookup"><span data-stu-id="837c0-582">Locate the *aspnetcore.dll* file.</span></span>
1. <span data-ttu-id="837c0-583">Klikněte na soubor pravým tlačítkem a v místní nabídce vyberte **vlastnosti** .</span><span class="sxs-lookup"><span data-stu-id="837c0-583">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="837c0-584">Vyberte kartu **Podrobnosti** . **Verze souboru** a **verze produktu** reprezentují nainstalovanou verzi modulu.</span><span class="sxs-lookup"><span data-stu-id="837c0-584">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="837c0-585">Protokoly instalačních balíčků pro modul se nacházejí v *C: \\ Uživatelé \\ % UserName% \\ data \\ Local \\ TEMP*. Soubor má název *dd_DotNetCoreWinSvrHosting__ \<timestamp> _000_AspNetCoreModule_x64. log*.</span><span class="sxs-lookup"><span data-stu-id="837c0-585">The Hosting Bundle installer logs for the module are found at *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. The file is named *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="837c0-586">Umístění souboru modulu, schématu a konfiguračního souboru</span><span class="sxs-lookup"><span data-stu-id="837c0-586">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="837c0-587">Modul</span><span class="sxs-lookup"><span data-stu-id="837c0-587">Module</span></span>

<span data-ttu-id="837c0-588">**Služba IIS (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="837c0-588">**IIS (x86/amd64):**</span></span>

* <span data-ttu-id="837c0-589">% windir% \System32\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="837c0-589">%windir%\System32\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="837c0-590">% windir% \SysWOW64\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="837c0-590">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="837c0-591">Základní Module\V2\aspnetcorev2.dll%ProgramFiles%\IIS\Asp.Net</span><span class="sxs-lookup"><span data-stu-id="837c0-591">%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="837c0-592">% ProgramFiles (x86)% \ IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="837c0-592">%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

<span data-ttu-id="837c0-593">**IIS Express (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="837c0-593">**IIS Express (x86/amd64):**</span></span>

* <span data-ttu-id="837c0-594">%ProgramFiles%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="837c0-594">%ProgramFiles%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="837c0-595">% ProgramFiles (x86)% \ IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="837c0-595">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="837c0-596">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="837c0-596">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="837c0-597">% ProgramFiles (x86)% \ IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="837c0-597">%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

### <a name="schema"></a><span data-ttu-id="837c0-598">Schéma</span><span class="sxs-lookup"><span data-stu-id="837c0-598">Schema</span></span>

<span data-ttu-id="837c0-599">**IIS**</span><span class="sxs-lookup"><span data-stu-id="837c0-599">**IIS**</span></span>

* <span data-ttu-id="837c0-600">% windir% \System32\inetsrv\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="837c0-600">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span></span>

* <span data-ttu-id="837c0-601">% windir% \System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span><span class="sxs-lookup"><span data-stu-id="837c0-601">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span></span>

<span data-ttu-id="837c0-602">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="837c0-602">**IIS Express**</span></span>

* <span data-ttu-id="837c0-603">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="837c0-603">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span></span>

* <span data-ttu-id="837c0-604">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span><span class="sxs-lookup"><span data-stu-id="837c0-604">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span></span>

### <a name="configuration"></a><span data-ttu-id="837c0-605">Konfigurace</span><span class="sxs-lookup"><span data-stu-id="837c0-605">Configuration</span></span>

<span data-ttu-id="837c0-606">**IIS**</span><span class="sxs-lookup"><span data-stu-id="837c0-606">**IIS**</span></span>

* <span data-ttu-id="837c0-607">% windir% \System32\inetsrv\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="837c0-607">%windir%\System32\inetsrv\config\applicationHost.config</span></span>

<span data-ttu-id="837c0-608">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="837c0-608">**IIS Express**</span></span>

* <span data-ttu-id="837c0-609">Visual Studio: {ROOT aplikace} \\.vs\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="837c0-609">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span></span>

* <span data-ttu-id="837c0-610">*iisexpress.exe* CLI:% USERPROFILE% \Documents\IISExpress\config\applicationhost.config</span><span class="sxs-lookup"><span data-stu-id="837c0-610">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span></span>

<span data-ttu-id="837c0-611">Soubory můžete najít hledáním *aspnetcore* v souboru *applicationHost.config* .</span><span class="sxs-lookup"><span data-stu-id="837c0-611">The files can be found by searching for *aspnetcore* in the *applicationHost.config* file.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="837c0-612">Modul ASP.NET Core je nativním modulem služby IIS, který se připojuje k kanálu IIS a přesměrovává webové požadavky na back-endové aplikace ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="837c0-612">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to forward web requests to backend ASP.NET Core apps.</span></span>

<span data-ttu-id="837c0-613">Podporované verze systému Windows:</span><span class="sxs-lookup"><span data-stu-id="837c0-613">Supported Windows versions:</span></span>

* <span data-ttu-id="837c0-614">Windows 7 nebo novější</span><span class="sxs-lookup"><span data-stu-id="837c0-614">Windows 7 or later</span></span>
* <span data-ttu-id="837c0-615">Windows Server 2008 R2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="837c0-615">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="837c0-616">Modul funguje pouze s Kestrel.</span><span class="sxs-lookup"><span data-stu-id="837c0-616">The module only works with Kestrel.</span></span> <span data-ttu-id="837c0-617">Modul není kompatibilní s [HTTP.sys](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="837c0-617">The module is incompatible with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

<span data-ttu-id="837c0-618">Vzhledem k tomu, že ASP.NET Core aplikace běží v procesu odděleném od pracovního procesu služby IIS, modul také zpracovává správu procesů.</span><span class="sxs-lookup"><span data-stu-id="837c0-618">Because ASP.NET Core apps run in a process separate from the IIS worker process, the module also handles process management.</span></span> <span data-ttu-id="837c0-619">Modul spustí proces pro aplikaci ASP.NET Core, když první požadavek dorazí a restartuje aplikaci, pokud dojde k chybě.</span><span class="sxs-lookup"><span data-stu-id="837c0-619">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it crashes.</span></span> <span data-ttu-id="837c0-620">To je v podstatě stejné chování jako u aplikací ASP.NET 4. x spouštěných vnitroprocesově ve službě IIS, které spravuje [Aktivační služba procesů systému Windows (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span><span class="sxs-lookup"><span data-stu-id="837c0-620">This is essentially the same behavior as seen with ASP.NET 4.x apps that run in-process in IIS that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="837c0-621">Následující diagram znázorňuje vztah mezi službou IIS, modulem ASP.NET Core a aplikací:</span><span class="sxs-lookup"><span data-stu-id="837c0-621">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app:</span></span>

![Modul ASP.NET Core](aspnet-core-module/_static/ancm-outofprocess.png)

<span data-ttu-id="837c0-623">Požadavky přicházející z webu do ovladače HTTP.sys režimu jádra.</span><span class="sxs-lookup"><span data-stu-id="837c0-623">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="837c0-624">Ovladač směruje požadavky do služby IIS na konfigurovaném portu webu, obvykle 80 (HTTP) nebo 443 (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="837c0-624">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="837c0-625">Modul předá požadavky do Kestrel na náhodném portu pro aplikaci, což není port 80 nebo 443.</span><span class="sxs-lookup"><span data-stu-id="837c0-625">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="837c0-626">Modul Určuje port prostřednictvím proměnné prostředí při spuštění a [Služba IIS Integration middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) nakonfiguruje server, na kterém má naslouchat `http://localhost:{port}` .</span><span class="sxs-lookup"><span data-stu-id="837c0-626">The module specifies the port via an environment variable at startup, and the [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) configures the server to listen on `http://localhost:{port}`.</span></span> <span data-ttu-id="837c0-627">Budou provedeny další kontroly a požadavky, které nepocházejí z modulu, jsou odmítnuty.</span><span class="sxs-lookup"><span data-stu-id="837c0-627">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="837c0-628">Modul nepodporuje předávání HTTPS, takže požadavky se předávají přes protokol HTTP i v případě, že je služba IIS prostřednictvím protokolu HTTPS přijímá.</span><span class="sxs-lookup"><span data-stu-id="837c0-628">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="837c0-629">Po Kestrel žádosti z modulu se požadavek odešle do kanálu middlewaru ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="837c0-629">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="837c0-630">Kanál middlewaru zpracovává požadavek a předá ho jako `HttpContext` instanci do logiky aplikace.</span><span class="sxs-lookup"><span data-stu-id="837c0-630">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="837c0-631">Middleware přidaný integrací služby IIS: aktualizace schématu, vzdálené IP adresy a pathbase pro přesměrování požadavku do Kestrel.</span><span class="sxs-lookup"><span data-stu-id="837c0-631">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="837c0-632">Odpověď aplikace se předává zpátky do služby IIS, která ji přenáší zpátky do klienta HTTP, který žádost inicioval.</span><span class="sxs-lookup"><span data-stu-id="837c0-632">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="837c0-633">Mnoho nativních modulů, jako je ověřování systému Windows, zůstane aktivní.</span><span class="sxs-lookup"><span data-stu-id="837c0-633">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="837c0-634">Další informace o aktivních modulech služby IIS pomocí modulu ASP.NET Core najdete v tématu <xref:host-and-deploy/iis/modules> .</span><span class="sxs-lookup"><span data-stu-id="837c0-634">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="837c0-635">Modul ASP.NET Core může také:</span><span class="sxs-lookup"><span data-stu-id="837c0-635">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="837c0-636">Nastavte proměnné prostředí pro pracovní proces.</span><span class="sxs-lookup"><span data-stu-id="837c0-636">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="837c0-637">Protokoluje výstup stdout do úložiště souborů pro řešení problémů při spuštění.</span><span class="sxs-lookup"><span data-stu-id="837c0-637">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="837c0-638">Předejte tokeny ověřování systému Windows.</span><span class="sxs-lookup"><span data-stu-id="837c0-638">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="837c0-639">Jak nainstalovat a používat modul ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="837c0-639">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="837c0-640">Pokyny k instalaci modulu ASP.NET Core najdete v tématu [instalace hostující sady .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span><span class="sxs-lookup"><span data-stu-id="837c0-640">For instructions on how to install the ASP.NET Core Module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="837c0-641">Konfigurace s web.config</span><span class="sxs-lookup"><span data-stu-id="837c0-641">Configuration with web.config</span></span>

<span data-ttu-id="837c0-642">Modul ASP.NET Core je nakonfigurován s `aspNetCore` částí `system.webServer` uzlu v souboru *web.config* webu.</span><span class="sxs-lookup"><span data-stu-id="837c0-642">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="837c0-643">Následující *web.config* soubor je publikován pro [nasazení závislé na rozhraní](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) a konfiguruje modul ASP.NET Core, aby zpracovával požadavky webu:</span><span class="sxs-lookup"><span data-stu-id="837c0-643">The following *web.config* file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

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

<span data-ttu-id="837c0-644">Následující *web.config* jsou publikovány pro [samostatně uzavřené nasazení](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span><span class="sxs-lookup"><span data-stu-id="837c0-644">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

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

<span data-ttu-id="837c0-645">Když je aplikace nasazená na [Azure App Service](https://azure.microsoft.com/services/app-service/), `stdoutLogFile` cesta je nastavená na `\\?\%home%\LogFiles\stdout` .</span><span class="sxs-lookup"><span data-stu-id="837c0-645">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="837c0-646">Cesta ukládá protokoly stdout do složky *soubory protokolů* , což je umístění, které služba automaticky vytvořila.</span><span class="sxs-lookup"><span data-stu-id="837c0-646">The path saves stdout logs to the *LogFiles* folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="837c0-647">Informace o konfiguraci dílčí aplikace IIS najdete v tématu <xref:host-and-deploy/iis/index#sub-applications> .</span><span class="sxs-lookup"><span data-stu-id="837c0-647">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="837c0-648">Atributy elementu aspNetCore</span><span class="sxs-lookup"><span data-stu-id="837c0-648">Attributes of the aspNetCore element</span></span>

| <span data-ttu-id="837c0-649">Atribut</span><span class="sxs-lookup"><span data-stu-id="837c0-649">Attribute</span></span> | <span data-ttu-id="837c0-650">Popis</span><span class="sxs-lookup"><span data-stu-id="837c0-650">Description</span></span> | <span data-ttu-id="837c0-651">Výchozí</span><span class="sxs-lookup"><span data-stu-id="837c0-651">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="837c0-652">Volitelný řetězcový atribut.</span><span class="sxs-lookup"><span data-stu-id="837c0-652">Optional string attribute.</span></span></p><p><span data-ttu-id="837c0-653">Argumenty ke spustitelnému souboru určenému v **processPath**.</span><span class="sxs-lookup"><span data-stu-id="837c0-653">Arguments to the executable specified in **processPath**.</span></span></p>| |
| `disableStartUpErrorPage` | <p><span data-ttu-id="837c0-654">Volitelný atribut typu Boolean.</span><span class="sxs-lookup"><span data-stu-id="837c0-654">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="837c0-655">Pokud je nastaveno na true, stránka **502,5-procesového selhání** se potlačí a stavová stránka 502, která je nakonfigurovaná ve *web.config* , má přednost.</span><span class="sxs-lookup"><span data-stu-id="837c0-655">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="837c0-656">Volitelný atribut typu Boolean.</span><span class="sxs-lookup"><span data-stu-id="837c0-656">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="837c0-657">Pokud je nastaveno na true, token se přepošle podřízenému procesu, který naslouchá na% ASPNETCORE_PORT% jako záhlaví MS-ASPNETCORE-WINAUTHTOKEN na žádost.</span><span class="sxs-lookup"><span data-stu-id="837c0-657">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="837c0-658">Je zodpovědností za tento proces volání CloseHandle na tento token na žádost.</span><span class="sxs-lookup"><span data-stu-id="837c0-658">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `processesPerApplication` | <p><span data-ttu-id="837c0-659">Volitelný celočíselný atribut</span><span class="sxs-lookup"><span data-stu-id="837c0-659">Optional integer attribute.</span></span></p><p><span data-ttu-id="837c0-660">Určuje počet instancí procesu určeného v nastavení **processPath** , které lze v rámci aplikace vystavit.</span><span class="sxs-lookup"><span data-stu-id="837c0-660">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="837c0-661">Nastavení `processesPerApplication` se nedoporučuje.</span><span class="sxs-lookup"><span data-stu-id="837c0-661">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="837c0-662">Tento atribut bude v budoucí verzi odebrán.</span><span class="sxs-lookup"><span data-stu-id="837c0-662">This attribute will be removed in a future release.</span></span></p> | <span data-ttu-id="837c0-663">Výchozí `1`</span><span class="sxs-lookup"><span data-stu-id="837c0-663">Default: `1`</span></span><br><span data-ttu-id="837c0-664">Dlouhé `1`</span><span class="sxs-lookup"><span data-stu-id="837c0-664">Min: `1`</span></span><br><span data-ttu-id="837c0-665">Počet `100`</span><span class="sxs-lookup"><span data-stu-id="837c0-665">Max: `100`</span></span> |
| `processPath` | <p><span data-ttu-id="837c0-666">Povinný atribut řetězce.</span><span class="sxs-lookup"><span data-stu-id="837c0-666">Required string attribute.</span></span></p><p><span data-ttu-id="837c0-667">Cesta ke spustitelnému souboru, který spouští proces naslouchající na požadavky HTTP.</span><span class="sxs-lookup"><span data-stu-id="837c0-667">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="837c0-668">Jsou podporovány relativní cesty.</span><span class="sxs-lookup"><span data-stu-id="837c0-668">Relative paths are supported.</span></span> <span data-ttu-id="837c0-669">Pokud cesta začíná `.` , bude tato cesta považována za relativní vzhledem k kořenu webu.</span><span class="sxs-lookup"><span data-stu-id="837c0-669">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="837c0-670">Volitelný celočíselný atribut</span><span class="sxs-lookup"><span data-stu-id="837c0-670">Optional integer attribute.</span></span></p><p><span data-ttu-id="837c0-671">Určuje, kolikrát je povoleno selhání procesu určeného v **processPath** za minutu.</span><span class="sxs-lookup"><span data-stu-id="837c0-671">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="837c0-672">Pokud je tento limit překročen, modul ukončí spuštění procesu po zbytek minuty.</span><span class="sxs-lookup"><span data-stu-id="837c0-672">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p> | <span data-ttu-id="837c0-673">Výchozí `10`</span><span class="sxs-lookup"><span data-stu-id="837c0-673">Default: `10`</span></span><br><span data-ttu-id="837c0-674">Dlouhé `0`</span><span class="sxs-lookup"><span data-stu-id="837c0-674">Min: `0`</span></span><br><span data-ttu-id="837c0-675">Počet `100`</span><span class="sxs-lookup"><span data-stu-id="837c0-675">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="837c0-676">Volitelný atribut TimeSpan.</span><span class="sxs-lookup"><span data-stu-id="837c0-676">Optional timespan attribute.</span></span></p><p><span data-ttu-id="837c0-677">Určuje dobu, po kterou modul ASP.NET Core čeká na odpověď od procesu, který naslouchá na% ASPNETCORE_PORT%.</span><span class="sxs-lookup"><span data-stu-id="837c0-677">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="837c0-678">Ve verzích modulu ASP.NET Core, které byly dodávány s vydáním ASP.NET Core 2,1 nebo vyšší, `requestTimeout` je určena v hodinách, minutách a sekundách.</span><span class="sxs-lookup"><span data-stu-id="837c0-678">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p> | <span data-ttu-id="837c0-679">Výchozí `00:02:00`</span><span class="sxs-lookup"><span data-stu-id="837c0-679">Default: `00:02:00`</span></span><br><span data-ttu-id="837c0-680">Dlouhé `00:00:00`</span><span class="sxs-lookup"><span data-stu-id="837c0-680">Min: `00:00:00`</span></span><br><span data-ttu-id="837c0-681">Počet `360:00:00`</span><span class="sxs-lookup"><span data-stu-id="837c0-681">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="837c0-682">Volitelný celočíselný atribut</span><span class="sxs-lookup"><span data-stu-id="837c0-682">Optional integer attribute.</span></span></p><p><span data-ttu-id="837c0-683">Doba v sekundách, po kterou modul čeká na řádné vypnutí spustitelného souboru, když se zjistí soubor *app_offline.htm* .</span><span class="sxs-lookup"><span data-stu-id="837c0-683">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="837c0-684">Výchozí `10`</span><span class="sxs-lookup"><span data-stu-id="837c0-684">Default: `10`</span></span><br><span data-ttu-id="837c0-685">Dlouhé `0`</span><span class="sxs-lookup"><span data-stu-id="837c0-685">Min: `0`</span></span><br><span data-ttu-id="837c0-686">Počet `600`</span><span class="sxs-lookup"><span data-stu-id="837c0-686">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="837c0-687">Volitelný celočíselný atribut</span><span class="sxs-lookup"><span data-stu-id="837c0-687">Optional integer attribute.</span></span></p><p><span data-ttu-id="837c0-688">Doba v sekundách, kterou modul počká, než spustitelný soubor spustí proces, který na portu naslouchá.</span><span class="sxs-lookup"><span data-stu-id="837c0-688">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="837c0-689">Pokud je tento časový limit překročen, modul proces ukončuje.</span><span class="sxs-lookup"><span data-stu-id="837c0-689">If this time limit is exceeded, the module kills the process.</span></span> <span data-ttu-id="837c0-690">Modul se pokusí znovu spustit proces, když obdrží novou žádost, a pokračuje v pokusu o restartování procesu u dalších příchozích požadavků, pokud se nepodaří spustit **rapidFailsPerMinute** počet pokusů během poslední minuty.</span><span class="sxs-lookup"><span data-stu-id="837c0-690">The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="837c0-691">Hodnota 0 (nula **) není považována za** nekonečný časový limit.</span><span class="sxs-lookup"><span data-stu-id="837c0-691">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="837c0-692">Výchozí `120`</span><span class="sxs-lookup"><span data-stu-id="837c0-692">Default: `120`</span></span><br><span data-ttu-id="837c0-693">Dlouhé `0`</span><span class="sxs-lookup"><span data-stu-id="837c0-693">Min: `0`</span></span><br><span data-ttu-id="837c0-694">Počet `3600`</span><span class="sxs-lookup"><span data-stu-id="837c0-694">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="837c0-695">Volitelný atribut typu Boolean.</span><span class="sxs-lookup"><span data-stu-id="837c0-695">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="837c0-696">Je-li nastavena hodnota true, **stdout** a **stderr** pro proces zadaný v **processPath** budou přesměrovány do souboru zadaného v **stdoutLogFile**.</span><span class="sxs-lookup"><span data-stu-id="837c0-696">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="837c0-697">Volitelný řetězcový atribut.</span><span class="sxs-lookup"><span data-stu-id="837c0-697">Optional string attribute.</span></span></p><p><span data-ttu-id="837c0-698">Určuje relativní nebo absolutní cestu k souboru, pro který je **stdout** a **stderr** z procesu určeného v **processPath** protokolováno.</span><span class="sxs-lookup"><span data-stu-id="837c0-698">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="837c0-699">Relativní cesty jsou relativní vzhledem k kořenu webu.</span><span class="sxs-lookup"><span data-stu-id="837c0-699">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="837c0-700">Každá cesta začínající řetězcem `.` je relativní vzhledem k kořenu webu a všechny ostatní cesty se považují za absolutní cesty.</span><span class="sxs-lookup"><span data-stu-id="837c0-700">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="837c0-701">Aby modul vytvořil soubor protokolu, musí existovat všechny složky, které jsou v cestě k dispozici.</span><span class="sxs-lookup"><span data-stu-id="837c0-701">Any folders provided in the path must exist in order for the module to create the log file.</span></span> <span data-ttu-id="837c0-702">Pomocí oddělovačů podtržítka se do posledního segmentu cesty **stdoutLogFile** přidá časové razítko, ID procesu a Přípona souboru (*. log*).</span><span class="sxs-lookup"><span data-stu-id="837c0-702">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="837c0-703">Pokud `.\logs\stdout` je zadán jako hodnota, je ukázkový protokol stdout uložen jako *stdout_20180205194132_1934. log* ve složce *logs* při uložení na 2/5/2018 v 19:41:32 s ID procesu 1934.</span><span class="sxs-lookup"><span data-stu-id="837c0-703">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

### <a name="setting-environment-variables"></a><span data-ttu-id="837c0-704">Nastavení proměnných prostředí</span><span class="sxs-lookup"><span data-stu-id="837c0-704">Setting environment variables</span></span>

<span data-ttu-id="837c0-705">Proměnné prostředí lze zadat pro proces v `processPath` atributu.</span><span class="sxs-lookup"><span data-stu-id="837c0-705">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="837c0-706">Zadejte proměnnou prostředí s `<environmentVariable>` podřízeným elementem `<environmentVariables>` elementu kolekce.</span><span class="sxs-lookup"><span data-stu-id="837c0-706">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span>

> [!WARNING]
> <span data-ttu-id="837c0-707">Proměnné prostředí nastavené v této části jsou v konfliktu se sadou proměnných prostředí systému se stejným názvem.</span><span class="sxs-lookup"><span data-stu-id="837c0-707">Environment variables set in this section conflict with system environment variables set with the same name.</span></span> <span data-ttu-id="837c0-708">Pokud je proměnná prostředí nastavena v souboru *web.config* i na úrovni systému systému Windows, bude hodnota ze souboru *web.config* připojena k hodnotě proměnné prostředí systému (například `ASPNETCORE_ENVIRONMENT: Development;Development` ), což zabrání spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="837c0-708">If an environment variable is set in both the *web.config* file and at the system level in Windows, the value from the *web.config* file becomes appended to the system environment variable value (for example, `ASPNETCORE_ENVIRONMENT: Development;Development`), which prevents the app from starting.</span></span>

<span data-ttu-id="837c0-709">Následující příklad nastaví dvě proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="837c0-709">The following example sets two environment variables.</span></span> <span data-ttu-id="837c0-710">`ASPNETCORE_ENVIRONMENT` nakonfiguruje prostředí aplikace na `Development` .</span><span class="sxs-lookup"><span data-stu-id="837c0-710">`ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="837c0-711">Vývojář může tuto hodnotu dočasně nastavit v souboru *web.config* , aby bylo možné vynutit, aby se [Stránka s výjimkou vývojářů](xref:fundamentals/error-handling) načetla při ladění výjimky aplikace.</span><span class="sxs-lookup"><span data-stu-id="837c0-711">A developer may temporarily set this value in the *web.config* file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="837c0-712">`CONFIG_DIR` je příkladem uživatelsky definované proměnné prostředí, kde vývojář napsal kód, který přečte hodnotu při spuštění, aby vytvořil cestu pro načtení konfiguračního souboru aplikace.</span><span class="sxs-lookup"><span data-stu-id="837c0-712">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

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
> <span data-ttu-id="837c0-713">Nastavte pouze `ASPNETCORE_ENVIRONMENT` proměnnou prostředí na `Development` pracovní a testovací servery, které nejsou přístupné nedůvěryhodným sítím, jako je například Internet.</span><span class="sxs-lookup"><span data-stu-id="837c0-713">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## <a name="app_offlinehtm"></a><span data-ttu-id="837c0-714">app_offline.htm</span><span class="sxs-lookup"><span data-stu-id="837c0-714">app_offline.htm</span></span>

<span data-ttu-id="837c0-715">Pokud se v kořenovém adresáři aplikace zjistí soubor s názvem *app_offline.htm* , modul ASP.NET Core se pokusí aplikaci řádně vypnout a zastavit zpracování příchozích požadavků.</span><span class="sxs-lookup"><span data-stu-id="837c0-715">If a file with the name *app_offline.htm* is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="837c0-716">Pokud aplikace běží i po uplynutí počtu sekund definovaného v `shutdownTimeLimit` , modul ASP.NET Core ukončuje běžící proces.</span><span class="sxs-lookup"><span data-stu-id="837c0-716">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="837c0-717">Když je přítomen soubor *app_offline.htm* , ASP.NET Core modul reaguje na požadavky odesláním zpět obsahu *app_offline.htm* souboru.</span><span class="sxs-lookup"><span data-stu-id="837c0-717">While the *app_offline.htm* file is present, the ASP.NET Core Module responds to requests by sending back the contents of the *app_offline.htm* file.</span></span> <span data-ttu-id="837c0-718">Po odebrání souboru *app_offline.htm* spustí aplikace další požadavek.</span><span class="sxs-lookup"><span data-stu-id="837c0-718">When the *app_offline.htm* file is removed, the next request starts the app.</span></span>

## <a name="start-up-error-page"></a><span data-ttu-id="837c0-719">Spouštěcí chybová stránka</span><span class="sxs-lookup"><span data-stu-id="837c0-719">Start-up error page</span></span>

<span data-ttu-id="837c0-720">Pokud se v modulu ASP.NET Core nepodaří spustit back-end proces nebo se spustí back-end proces, ale nenaslouchá na konfigurovaném portu, zobrazí se stránka s kódem stavu *selhání procesu 502,5* .</span><span class="sxs-lookup"><span data-stu-id="837c0-720">If the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span> <span data-ttu-id="837c0-721">Chcete-li tuto stránku potlačit a vrátit se na výchozí stavovou stránku služby IIS 502, použijte `disableStartUpErrorPage` atribut.</span><span class="sxs-lookup"><span data-stu-id="837c0-721">To suppress this page and revert to the default IIS 502 status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="837c0-722">Další informace o konfiguraci vlastních chybových zpráv najdete v tématu [chyby \<httpErrors> protokolu HTTP ](/iis/configuration/system.webServer/httpErrors/).</span><span class="sxs-lookup"><span data-stu-id="837c0-722">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

![Stavová stránka selhání procesu procesu 502,5](aspnet-core-module/_static/ANCM-502_5.png)

## <a name="log-creation-and-redirection"></a><span data-ttu-id="837c0-724">Vytvoření a přesměrování protokolu</span><span class="sxs-lookup"><span data-stu-id="837c0-724">Log creation and redirection</span></span>

<span data-ttu-id="837c0-725">Modul ASP.NET Core přesměruje výstup konzoly stdout a stderr na disk, pokud `stdoutLogEnabled` `stdoutLogFile` `aspNetCore` jsou nastaveny atributy a elementu.</span><span class="sxs-lookup"><span data-stu-id="837c0-725">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="837c0-726">`stdoutLogFile`Při vytvoření souboru protokolu jsou všechny složky v této cestě vytvářeny modulem.</span><span class="sxs-lookup"><span data-stu-id="837c0-726">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="837c0-727">Fond aplikací musí mít oprávnění k zápisu do umístění, kam se protokoly zapisují (použijte `IIS AppPool\<app_pool_name>` k poskytnutí oprávnění k zápisu).</span><span class="sxs-lookup"><span data-stu-id="837c0-727">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="837c0-728">Protokoly nejsou otočeny, pokud nedochází k recyklování/restartování procesu.</span><span class="sxs-lookup"><span data-stu-id="837c0-728">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="837c0-729">Je zodpovědností hostitele omezit místo na disku, které používají protokoly.</span><span class="sxs-lookup"><span data-stu-id="837c0-729">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="837c0-730">Použití protokolu stdout se doporučuje pouze při řešení potíží se spouštěním aplikací při hostování služby IIS nebo při použití [podpory v době vývoje služby IIS se sadou Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), nikoli při ladění místně a při spuštění aplikace s IIS Express.</span><span class="sxs-lookup"><span data-stu-id="837c0-730">Using the stdout log is only recommended for troubleshooting app startup issues when hosting on IIS or when using [development-time support for IIS with Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), not while debugging locally and running the app with IIS Express.</span></span>

<span data-ttu-id="837c0-731">Nepoužívejte protokol stdout pro obecné účely protokolování aplikace.</span><span class="sxs-lookup"><span data-stu-id="837c0-731">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="837c0-732">Pro rutinu protokolování v aplikaci ASP.NET Core použijte knihovnu protokolování, která omezuje velikost souboru protokolu a otočí protokoly.</span><span class="sxs-lookup"><span data-stu-id="837c0-732">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="837c0-733">Další informace najdete v tématu [Zprostředkovatelé protokolování třetích stran](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="837c0-733">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="837c0-734">Časové razítko a Přípona souboru se automaticky přidají při vytvoření souboru protokolu.</span><span class="sxs-lookup"><span data-stu-id="837c0-734">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="837c0-735">Název souboru protokolu se skládá připojením časového razítka, ID procesu a přípony souboru (*. log*) k poslednímu segmentu `stdoutLogFile` cesty (obvykle *stdout*), oddělený podtržítky.</span><span class="sxs-lookup"><span data-stu-id="837c0-735">The log file name is composed by appending the timestamp, process ID, and file extension (*.log*) to the last segment of the `stdoutLogFile` path (typically *stdout*) delimited by underscores.</span></span> <span data-ttu-id="837c0-736">Pokud `stdoutLogFile` cesta končí na *stdout*, protokol pro aplikaci s číslem PID 1934 vytvořeným v 2/5/2018 v 19:42:32 má název souboru *stdout_20180205194132_1934. log*.</span><span class="sxs-lookup"><span data-stu-id="837c0-736">If the `stdoutLogFile` path ends with *stdout*, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name *stdout_20180205194132_1934.log*.</span></span>

<span data-ttu-id="837c0-737">Následující vzorový `aspNetCore` prvek nakonfiguruje protokolování stdout na relativní cestě `.\log\` .</span><span class="sxs-lookup"><span data-stu-id="837c0-737">The following sample `aspNetCore` element configures stdout logging at the relative path `.\log\`.</span></span> <span data-ttu-id="837c0-738">Potvrďte, že identita uživatele fondu aplikací má oprávnění k zápisu do zadané cesty.</span><span class="sxs-lookup"><span data-stu-id="837c0-738">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout">
</aspNetCore>
```

<span data-ttu-id="837c0-739">Když publikujete aplikaci pro nasazení Azure App Service, sada web SDK nastaví `stdoutLogFile` hodnotu na `\\?\%home%\LogFiles\stdout` .</span><span class="sxs-lookup"><span data-stu-id="837c0-739">When publishing an app for Azure App Service deployment, the Web SDK sets the `stdoutLogFile` value to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="837c0-740">`%home`Proměnná prostředí je předdefinovaná pro aplikace hostované v Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="837c0-740">The `%home` environment variable is predefined for apps hosted by Azure App Service.</span></span>

<span data-ttu-id="837c0-741">Chcete-li vytvořit pravidla filtru protokolování, přečtěte si část [Konfigurace](xref:fundamentals/logging/index#log-filtering) a [filtrování protokolů](xref:fundamentals/logging/index#log-filtering) v dokumentaci ASP.NET Core protokolování.</span><span class="sxs-lookup"><span data-stu-id="837c0-741">To create logging filter rules, see the [Configuration](xref:fundamentals/logging/index#log-filtering) and [Log filtering](xref:fundamentals/logging/index#log-filtering) sections of the ASP.NET Core logging documentation.</span></span>

<span data-ttu-id="837c0-742">Další informace o formátech cest najdete v tématu [formáty souborů cest v systémech Windows](/dotnet/standard/io/file-path-formats).</span><span class="sxs-lookup"><span data-stu-id="837c0-742">For more information on path formats, see [File path formats on Windows systems](/dotnet/standard/io/file-path-formats).</span></span>

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="837c0-743">Konfigurace proxy serveru používá protokol HTTP a token párování.</span><span class="sxs-lookup"><span data-stu-id="837c0-743">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="837c0-744">Proxy vytvořený mezi modulem ASP.NET Core a Kestrel používá protokol HTTP.</span><span class="sxs-lookup"><span data-stu-id="837c0-744">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="837c0-745">Nehrozí riziko odposlouchávání provozu mezi modulem a Kestrel z umístění od serveru.</span><span class="sxs-lookup"><span data-stu-id="837c0-745">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="837c0-746">Párovací token se používá k zajištění, že žádosti přijaté službou Kestrel byly proxy službou IIS a nepocházejí z jiného zdroje.</span><span class="sxs-lookup"><span data-stu-id="837c0-746">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="837c0-747">Token párování se vytvoří a nastaví na proměnnou prostředí ( `ASPNETCORE_TOKEN` ) modulem.</span><span class="sxs-lookup"><span data-stu-id="837c0-747">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="837c0-748">Párovací token je také nastaven na hlavičku ( `MS-ASPNETCORE-TOKEN` ) pro každý požadavek proxy.</span><span class="sxs-lookup"><span data-stu-id="837c0-748">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="837c0-749">Middleware služby IIS kontroluje každý požadavek, který obdrží, a potvrdí, že hodnota hlavičky párového tokenu odpovídá hodnotě proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="837c0-749">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="837c0-750">Pokud se hodnoty tokenu neshodují, požadavek se zaprotokoluje a odmítne.</span><span class="sxs-lookup"><span data-stu-id="837c0-750">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="837c0-751">Proměnná prostředí párového tokenu a přenos dat mezi modulem a Kestrel nejsou přístupné z umístění mimo server.</span><span class="sxs-lookup"><span data-stu-id="837c0-751">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="837c0-752">Bez znalosti hodnoty párovacího tokenu nemůže útočník odeslat žádosti, které obcházejí kontrolu v Middlewari IIS.</span><span class="sxs-lookup"><span data-stu-id="837c0-752">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="837c0-753">ASP.NET Core modul se sdílenou konfigurací služby IIS</span><span class="sxs-lookup"><span data-stu-id="837c0-753">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="837c0-754">Instalační služba modulu ASP.NET Core se spouští s oprávněními účtu **TrustedInstaller** .</span><span class="sxs-lookup"><span data-stu-id="837c0-754">The ASP.NET Core Module installer runs with the privileges of the **TrustedInstaller** account.</span></span> <span data-ttu-id="837c0-755">Vzhledem k tomu, že účet místního systému nemá oprávnění k úpravám pro cestu ke sdílené složce, kterou používá sdílená konfigurace služby IIS, vyvolá Instalační program chybu odepření přístupu při pokusu o konfiguraci nastavení modulu v souboru *applicationHost.config* ve sdílené složce.</span><span class="sxs-lookup"><span data-stu-id="837c0-755">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the *applicationHost.config* file on the share.</span></span>

<span data-ttu-id="837c0-756">Pokud používáte sdílenou konfiguraci služby IIS, postupujte podle těchto kroků:</span><span class="sxs-lookup"><span data-stu-id="837c0-756">When using an IIS Shared Configuration, follow these steps:</span></span>

1. <span data-ttu-id="837c0-757">Zakažte sdílenou konfiguraci služby IIS.</span><span class="sxs-lookup"><span data-stu-id="837c0-757">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="837c0-758">Spusťte instalační program.</span><span class="sxs-lookup"><span data-stu-id="837c0-758">Run the installer.</span></span>
1. <span data-ttu-id="837c0-759">Exportujte aktualizovaný soubor *applicationHost.config* do sdílené složky.</span><span class="sxs-lookup"><span data-stu-id="837c0-759">Export the updated *applicationHost.config* file to the share.</span></span>
1. <span data-ttu-id="837c0-760">Znovu povolte sdílenou konfiguraci služby IIS.</span><span class="sxs-lookup"><span data-stu-id="837c0-760">Re-enable the IIS Shared Configuration.</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="837c0-761">Verze modulu a instalační protokoly hostitelské sady</span><span class="sxs-lookup"><span data-stu-id="837c0-761">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="837c0-762">Postup určení verze nainstalovaného modulu ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="837c0-762">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="837c0-763">V hostitelském systému přejděte na *%windir%\system32\inetsrv*.</span><span class="sxs-lookup"><span data-stu-id="837c0-763">On the hosting system, navigate to *%windir%\System32\inetsrv*.</span></span>
1. <span data-ttu-id="837c0-764">Vyhledejte soubor *aspnetcore.dll* .</span><span class="sxs-lookup"><span data-stu-id="837c0-764">Locate the *aspnetcore.dll* file.</span></span>
1. <span data-ttu-id="837c0-765">Klikněte na soubor pravým tlačítkem a v místní nabídce vyberte **vlastnosti** .</span><span class="sxs-lookup"><span data-stu-id="837c0-765">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="837c0-766">Vyberte kartu **Podrobnosti** . **Verze souboru** a **verze produktu** reprezentují nainstalovanou verzi modulu.</span><span class="sxs-lookup"><span data-stu-id="837c0-766">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="837c0-767">Protokoly instalačních balíčků pro modul se nacházejí v *C: \\ Uživatelé \\ % UserName% \\ data \\ Local \\ TEMP*. Soubor má název *dd_DotNetCoreWinSvrHosting__ \<timestamp> _000_AspNetCoreModule_x64. log*.</span><span class="sxs-lookup"><span data-stu-id="837c0-767">The Hosting Bundle installer logs for the module are found at *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. The file is named *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="837c0-768">Umístění souboru modulu, schématu a konfiguračního souboru</span><span class="sxs-lookup"><span data-stu-id="837c0-768">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="837c0-769">Modul</span><span class="sxs-lookup"><span data-stu-id="837c0-769">Module</span></span>

<span data-ttu-id="837c0-770">**Služba IIS (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="837c0-770">**IIS (x86/amd64):**</span></span>

* <span data-ttu-id="837c0-771">% windir% \System32\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="837c0-771">%windir%\System32\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="837c0-772">% windir% \SysWOW64\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="837c0-772">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span></span>

<span data-ttu-id="837c0-773">**IIS Express (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="837c0-773">**IIS Express (x86/amd64):**</span></span>

* <span data-ttu-id="837c0-774">%ProgramFiles%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="837c0-774">%ProgramFiles%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="837c0-775">% ProgramFiles (x86)% \ IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="837c0-775">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span></span>

### <a name="schema"></a><span data-ttu-id="837c0-776">Schéma</span><span class="sxs-lookup"><span data-stu-id="837c0-776">Schema</span></span>

<span data-ttu-id="837c0-777">**IIS**</span><span class="sxs-lookup"><span data-stu-id="837c0-777">**IIS**</span></span>

* <span data-ttu-id="837c0-778">% windir% \System32\inetsrv\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="837c0-778">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span></span>

<span data-ttu-id="837c0-779">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="837c0-779">**IIS Express**</span></span>

* <span data-ttu-id="837c0-780">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="837c0-780">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span></span>

### <a name="configuration"></a><span data-ttu-id="837c0-781">Konfigurace</span><span class="sxs-lookup"><span data-stu-id="837c0-781">Configuration</span></span>

<span data-ttu-id="837c0-782">**IIS**</span><span class="sxs-lookup"><span data-stu-id="837c0-782">**IIS**</span></span>

* <span data-ttu-id="837c0-783">% windir% \System32\inetsrv\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="837c0-783">%windir%\System32\inetsrv\config\applicationHost.config</span></span>

<span data-ttu-id="837c0-784">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="837c0-784">**IIS Express**</span></span>

* <span data-ttu-id="837c0-785">Visual Studio: {ROOT aplikace} \\.vs\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="837c0-785">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span></span>

* <span data-ttu-id="837c0-786">*iisexpress.exe* CLI:% USERPROFILE% \Documents\IISExpress\config\applicationhost.config</span><span class="sxs-lookup"><span data-stu-id="837c0-786">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span></span>

<span data-ttu-id="837c0-787">Soubory můžete najít hledáním *aspnetcore* v souboru *applicationHost.config* .</span><span class="sxs-lookup"><span data-stu-id="837c0-787">The files can be found by searching for *aspnetcore* in the *applicationHost.config* file.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="837c0-788">Další zdroje informací</span><span class="sxs-lookup"><span data-stu-id="837c0-788">Additional resources</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/azure-apps/index>
* <span data-ttu-id="837c0-789">[Zdroj odkazu na modul ASP.NET Core (hlavní větev)](https://github.com/dotnet/aspnetcore/tree/master/src/Servers/IIS/AspNetCoreModuleV2): k výběru konkrétní verze (například) použijte rozevírací seznam **větev** `release/3.1` .</span><span class="sxs-lookup"><span data-stu-id="837c0-789">[ASP.NET Core Module reference source (master branch)](https://github.com/dotnet/aspnetcore/tree/master/src/Servers/IIS/AspNetCoreModuleV2): Use the **Branch** drop down list to select a specific release (for example, `release/3.1`).</span></span>
* <xref:host-and-deploy/iis/modules>
