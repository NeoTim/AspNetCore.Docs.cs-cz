---
title: Referenční informace o ASP.NET Core modulu Konfigurace
author: guardrex
description: Zjistěte, jak nakonfigurovat modul ASP.NET Core pro hostování aplikací ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 12/06/2018
uid: host-and-deploy/aspnet-core-module
ms.openlocfilehash: 0ad73d89ffa3a8a3625c6e248efaad821e1b4d0a
ms.sourcegitcommit: 49faca2644590fc081d86db46ea5e29edfc28b7b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/09/2018
ms.locfileid: "53121554"
---
# <a name="aspnet-core-module-configuration-reference"></a><span data-ttu-id="09ec2-103">Referenční informace o ASP.NET Core modulu Konfigurace</span><span class="sxs-lookup"><span data-stu-id="09ec2-103">ASP.NET Core Module configuration reference</span></span>

<span data-ttu-id="09ec2-104">Podle [Luke Latham](https://github.com/guardrex), [Rick Anderson](https://twitter.com/RickAndMSFT), [Sourabh Shirhatti](https://twitter.com/sshirhatti), a [Justin Kotalik](https://github.com/jkotalik)</span><span class="sxs-lookup"><span data-stu-id="09ec2-104">By [Luke Latham](https://github.com/guardrex), [Rick Anderson](https://twitter.com/RickAndMSFT), [Sourabh Shirhatti](https://twitter.com/sshirhatti), and [Justin Kotalik](https://github.com/jkotalik)</span></span>

<span data-ttu-id="09ec2-105">Tento dokument obsahuje pokyny o tom, jak nakonfigurovat modul ASP.NET Core pro hostování aplikací ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="09ec2-105">This document provides instructions on how to configure the ASP.NET Core Module for hosting ASP.NET Core apps.</span></span> <span data-ttu-id="09ec2-106">Úvod do modul ASP.NET Core a pokyny k instalaci, najdete v článku [modul ASP.NET Core přehled](xref:fundamentals/servers/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="09ec2-106">For an introduction to the ASP.NET Core Module and installation instructions, see the [ASP.NET Core Module overview](xref:fundamentals/servers/aspnet-core-module).</span></span>

::: moniker range=">= aspnetcore-2.2"

## <a name="hosting-model"></a><span data-ttu-id="09ec2-107">Model hostingu</span><span class="sxs-lookup"><span data-stu-id="09ec2-107">Hosting model</span></span>

<span data-ttu-id="09ec2-108">Modul pro aplikace běžící na .NET Core 2.2 nebo vyšší, podporuje model hostování v procesu za účelem vylepšení výkonu v porovnání s hostitelem (out-of-process) reverzních proxy serverů.</span><span class="sxs-lookup"><span data-stu-id="09ec2-108">For apps running on .NET Core 2.2 or later, the module supports an in-process hosting model for improved performance compared to reverse-proxy (out-of-process) hosting.</span></span> <span data-ttu-id="09ec2-109">Další informace naleznete v tématu <xref:fundamentals/servers/aspnet-core-module#aspnet-core-module-description>.</span><span class="sxs-lookup"><span data-stu-id="09ec2-109">For more information, see <xref:fundamentals/servers/aspnet-core-module#aspnet-core-module-description>.</span></span>

<span data-ttu-id="09ec2-110">Hostování v procsess je vyjádřit výslovný souhlas pro existující aplikace, ale [dotnet nové](/dotnet/core/tools/dotnet-new) výchozí šablony na model hostingu v procesu pro všechny služby IIS a služby IIS Express.</span><span class="sxs-lookup"><span data-stu-id="09ec2-110">In-procsess hosting is opt-in for existing apps, but [dotnet new](/dotnet/core/tools/dotnet-new) templates default to the in-process hosting model for all IIS and IIS Express scenarios.</span></span>

<span data-ttu-id="09ec2-111">Jak nakonfigurovat aplikaci pro hostování v procesu, přidejte `<AspNetCoreHostingModel>` vlastnosti do souboru projektu vaší aplikace (například *MyApp.csproj*) s hodnotou `InProcess` (hostování mimo proces se nastaví pomocí `outofprocess`):</span><span class="sxs-lookup"><span data-stu-id="09ec2-111">To configure an app for in-process hosting, add the `<AspNetCoreHostingModel>` property to the app's project file (for example, *MyApp.csproj*) with a value of `InProcess` (out-of-process hosting is set with `outofprocess`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>InProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="09ec2-112">Při hostování v procesu platí následující vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="09ec2-112">The following characteristics apply when hosting in-process:</span></span>

* <span data-ttu-id="09ec2-113">Server služby IIS protokolu HTTP (`IISHttpServer`) se použije namísto [Kestrel](xref:fundamentals/servers/kestrel) serveru.</span><span class="sxs-lookup"><span data-stu-id="09ec2-113">IIS HTTP Server (`IISHttpServer`) is used instead of the [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span> <span data-ttu-id="09ec2-114">Server služby IIS protokolu HTTP (`IISHttpServer`) je další <xref:Microsoft.AspNetCore.Hosting.Server.IServer> implementace, který převádí nativní požadavků služby IIS technologie ASP.NET Core spravuje aplikace pro zpracování požadavků.</span><span class="sxs-lookup"><span data-stu-id="09ec2-114">IIS HTTP Server (`IISHttpServer`) is another <xref:Microsoft.AspNetCore.Hosting.Server.IServer> implementation that converts IIS native requests into ASP.NET Core managed requests for processing by the app.</span></span>

* <span data-ttu-id="09ec2-115">[RequestTimeout atribut](#attributes-of-the-aspnetcore-element) neplatí pro hostování v procesu.</span><span class="sxs-lookup"><span data-stu-id="09ec2-115">The [requestTimeout attribute](#attributes-of-the-aspnetcore-element) doesn't apply to in-process hosting.</span></span>

* <span data-ttu-id="09ec2-116">Sdílení fondem aplikací mezi aplikacemi se nepodporuje.</span><span class="sxs-lookup"><span data-stu-id="09ec2-116">Sharing an app pool among apps isn't supported.</span></span> <span data-ttu-id="09ec2-117">Použijte jeden fond aplikací na aplikaci.</span><span class="sxs-lookup"><span data-stu-id="09ec2-117">Use one app pool per app.</span></span>

* <span data-ttu-id="09ec2-118">Při použití [Webdeploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) nebo ručně uvedení [soubor app_offline.htm v nasazení](xref:host-and-deploy/iis/index#locked-deployment-files), aplikace nemusí vypnout okamžitě při otevření připojení.</span><span class="sxs-lookup"><span data-stu-id="09ec2-118">When using [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) or manually placing an [app_offline.htm file in the deployment](xref:host-and-deploy/iis/index#locked-deployment-files), the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="09ec2-119">Připojení soketu websocket bylo třeba může způsobit prodlevu při ukončení aplikace.</span><span class="sxs-lookup"><span data-stu-id="09ec2-119">For example, a websocket connection may delay app shut down.</span></span>

* <span data-ttu-id="09ec2-120">Architektura (bitové verze) (x64 nebo x86) nainstalovaný modul runtime a aplikace musí odpovídat architektuře fondu aplikací.</span><span class="sxs-lookup"><span data-stu-id="09ec2-120">The architecture (bitness) of the app and installed runtime (x64 or x86) must match the architecture of the app pool.</span></span>

* <span data-ttu-id="09ec2-121">Pokud nastavení aplikace hostitele ručně pomocí `WebHostBuilder` (bez použití [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host)) a spuštění aplikace se nikdy přímo na serveru Kestrel (v místním prostředí), volání `UseKestrel` před voláním `UseIISIntegration`.</span><span class="sxs-lookup"><span data-stu-id="09ec2-121">If setting up the app's host manually with `WebHostBuilder` (not using [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host)) and the app is ever run directly on the Kestrel server (self-hosted), call `UseKestrel` before calling `UseIISIntegration`.</span></span> <span data-ttu-id="09ec2-122">Pokud je obrácený pořadí, hostitel se nepodaří spustit.</span><span class="sxs-lookup"><span data-stu-id="09ec2-122">If the order is reversed, the host fails to start.</span></span>

* <span data-ttu-id="09ec2-123">Odpojení klienta jsou zjištěny.</span><span class="sxs-lookup"><span data-stu-id="09ec2-123">Client disconnects are detected.</span></span> <span data-ttu-id="09ec2-124">[HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) token zrušení se zrušila, když se klient odpojí.</span><span class="sxs-lookup"><span data-stu-id="09ec2-124">The [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) cancellation token is cancelled when the client disconnects.</span></span>

* <span data-ttu-id="09ec2-125"><xref:System.IO.Directory.GetCurrentDirectory*> Vrátí adresáře pracovního procesu tím, že služba IIS spíše než adresáře aplikace (například *C:\Windows\System32\inetsrv* pro *w3wp.exe*).</span><span class="sxs-lookup"><span data-stu-id="09ec2-125"><xref:System.IO.Directory.GetCurrentDirectory*> returns the worker directory of the process started by IIS rather than the app's directory (for example, *C:\Windows\System32\inetsrv* for *w3wp.exe*).</span></span>

  <span data-ttu-id="09ec2-126">Ukázkový kód, který nastaví aktuální adresář aplikace, najdete v článku [CurrentDirectoryHelpers třídy](https://github.com/aspnet/Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/2.x/CurrentDirectoryHelpers.cs).</span><span class="sxs-lookup"><span data-stu-id="09ec2-126">For sample code that sets the app's current directory, see the [CurrentDirectoryHelpers class](https://github.com/aspnet/Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/2.x/CurrentDirectoryHelpers.cs).</span></span> <span data-ttu-id="09ec2-127">Volání `SetCurrentDirectory` metody.</span><span class="sxs-lookup"><span data-stu-id="09ec2-127">Call the `SetCurrentDirectory` method.</span></span> <span data-ttu-id="09ec2-128">Následující volání <xref:System.IO.Directory.GetCurrentDirectory*> poskytují adresáře aplikace.</span><span class="sxs-lookup"><span data-stu-id="09ec2-128">Subsequent calls to <xref:System.IO.Directory.GetCurrentDirectory*> provide the app's directory.</span></span>

### <a name="hosting-model-changes"></a><span data-ttu-id="09ec2-129">Hostování změny modelu</span><span class="sxs-lookup"><span data-stu-id="09ec2-129">Hosting model changes</span></span>

<span data-ttu-id="09ec2-130">Pokud `hostingModel` nastavení se změnilo v *web.config* souboru (podrobně [konfigurace pomocí souboru web.config](#configuration-with-webconfig) části), modul pro službu IIS recykluje pracovní proces.</span><span class="sxs-lookup"><span data-stu-id="09ec2-130">If the `hostingModel` setting is changed in the *web.config* file (explained in the [Configuration with web.config](#configuration-with-webconfig) section), the module recycles the worker process for IIS.</span></span>

<span data-ttu-id="09ec2-131">Pro službu IIS Express modul nebude recyklovat pracovní proces, ale místo toho aktivuje řádné vypnutí aktuální proces IIS Express.</span><span class="sxs-lookup"><span data-stu-id="09ec2-131">For IIS Express, the module doesn't recycle the worker process but instead triggers a graceful shutdown of the current IIS Express process.</span></span> <span data-ttu-id="09ec2-132">Další požadavek na aplikaci vytvoří nový proces IIS Express.</span><span class="sxs-lookup"><span data-stu-id="09ec2-132">The next request to the app spawns a new IIS Express process.</span></span>

### <a name="process-name"></a><span data-ttu-id="09ec2-133">Název procesu</span><span class="sxs-lookup"><span data-stu-id="09ec2-133">Process name</span></span>

<span data-ttu-id="09ec2-134">`Process.GetCurrentProcess().ProcessName` sestavy `w3wp` / `iisexpress` (v procesu) nebo `dotnet` (out-of-process).</span><span class="sxs-lookup"><span data-stu-id="09ec2-134">`Process.GetCurrentProcess().ProcessName` reports `w3wp`/`iisexpress` (in-process) or `dotnet` (out-of-process).</span></span>

::: moniker-end

## <a name="configuration-with-webconfig"></a><span data-ttu-id="09ec2-135">Konfigurace pomocí souboru web.config</span><span class="sxs-lookup"><span data-stu-id="09ec2-135">Configuration with web.config</span></span>

<span data-ttu-id="09ec2-136">Modul ASP.NET Core, nastavena `aspNetCore` část `system.webServer` uzlu na webu *web.config* souboru.</span><span class="sxs-lookup"><span data-stu-id="09ec2-136">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="09ec2-137">Následující *web.config* soubor je publikován pro [nasazení závisí na architektuře](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) a konfiguruje modul ASP.NET Core pro zpracování požadavků lokality:</span><span class="sxs-lookup"><span data-stu-id="09ec2-137">The following *web.config* file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

::: moniker range=">= aspnetcore-2.2"

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

::: moniker-end

::: moniker range="< aspnetcore-2.2"

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

::: moniker-end

<span data-ttu-id="09ec2-138">Následující *web.config* je publikována pro [samostatná nasazení](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span><span class="sxs-lookup"><span data-stu-id="09ec2-138">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

::: moniker range=">= aspnetcore-2.2"

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

<span data-ttu-id="09ec2-139"><xref:System.Configuration.SectionInformation.InheritInChildApplications*> Je nastavena na `false` k označení, že nastavení zadané v rámci [ \<umístění >](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) element nedědí od neznámých aplikací, které jsou umístěny v podadresáři aplikace.</span><span class="sxs-lookup"><span data-stu-id="09ec2-139">The <xref:System.Configuration.SectionInformation.InheritInChildApplications*> property is set to `false` to indicate that the settings specified within the [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) element aren't inherited by apps that reside in a subdirectory of the app.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

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

::: moniker-end

<span data-ttu-id="09ec2-140">Když je aplikace nasazená na [služby Azure App Service](https://azure.microsoft.com/services/app-service/), `stdoutLogFile` je nastavena cesta `\\?\%home%\LogFiles\stdout`.</span><span class="sxs-lookup"><span data-stu-id="09ec2-140">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="09ec2-141">Protokoly stdout a uloží ji *LogFiles* složce, která je na místě automaticky vytvořené službou.</span><span class="sxs-lookup"><span data-stu-id="09ec2-141">The path saves stdout logs to the *LogFiles* folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="09ec2-142">Informace o konfiguraci dílčí aplikace služby IIS, naleznete v tématu <xref:host-and-deploy/iis/index#sub-applications>.</span><span class="sxs-lookup"><span data-stu-id="09ec2-142">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="09ec2-143">Atributy elementu aspNetCore</span><span class="sxs-lookup"><span data-stu-id="09ec2-143">Attributes of the aspNetCore element</span></span>

::: moniker range=">= aspnetcore-2.2"

| <span data-ttu-id="09ec2-144">Atribut</span><span class="sxs-lookup"><span data-stu-id="09ec2-144">Attribute</span></span> | <span data-ttu-id="09ec2-145">Popis</span><span class="sxs-lookup"><span data-stu-id="09ec2-145">Description</span></span> | <span data-ttu-id="09ec2-146">Výchozí</span><span class="sxs-lookup"><span data-stu-id="09ec2-146">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="09ec2-147">Volitelný atribut řetězce.</span><span class="sxs-lookup"><span data-stu-id="09ec2-147">Optional string attribute.</span></span></p><p><span data-ttu-id="09ec2-148">Argumenty pro spustitelný soubor určený v **processPath**.</span><span class="sxs-lookup"><span data-stu-id="09ec2-148">Arguments to the executable specified in **processPath**.</span></span></p> | |
| `disableStartUpErrorPage` | <p><span data-ttu-id="09ec2-149">Volitelný logický atribut.</span><span class="sxs-lookup"><span data-stu-id="09ec2-149">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="09ec2-150">Při hodnotě true **502.5 – selhání procesu** Potlačené stránky a znakovou stránku 502 stav nakonfigurované v *web.config* má přednost před.</span><span class="sxs-lookup"><span data-stu-id="09ec2-150">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="09ec2-151">Volitelný logický atribut.</span><span class="sxs-lookup"><span data-stu-id="09ec2-151">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="09ec2-152">Při hodnotě true se token předá podřízený proces naslouchání na ASPNETCORE_PORT % jako záhlaví "MS-ASPNETCORE-WINAUTHTOKEN" každý požadavek.</span><span class="sxs-lookup"><span data-stu-id="09ec2-152">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="09ec2-153">Je odpovědností tento proces pro volání CloseHandle na tento token každý požadavek.</span><span class="sxs-lookup"><span data-stu-id="09ec2-153">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `hostingModel` | <p><span data-ttu-id="09ec2-154">Volitelný atribut řetězce.</span><span class="sxs-lookup"><span data-stu-id="09ec2-154">Optional string attribute.</span></span></p><p><span data-ttu-id="09ec2-155">Určuje model hostování jako v procesu (`InProcess`) nebo out-of-process (`OutOfProcess`).</span><span class="sxs-lookup"><span data-stu-id="09ec2-155">Specifies the hosting model as in-process (`InProcess`) or out-of-process (`OutOfProcess`).</span></span></p> | `OutOfProcess` |
| `processesPerApplication` | <p><span data-ttu-id="09ec2-156">Volitelný celočíselný atribut.</span><span class="sxs-lookup"><span data-stu-id="09ec2-156">Optional integer attribute.</span></span></p><p><span data-ttu-id="09ec2-157">Určuje počet instancí procesu zadaný v **processPath** nastavení, které lze se nespředený nahoru na app.</span><span class="sxs-lookup"><span data-stu-id="09ec2-157">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="09ec2-158">&dagger;Hostování v procesu, je omezena na hodnotu `1`.</span><span class="sxs-lookup"><span data-stu-id="09ec2-158">&dagger;For in-process hosting, the value is limited to `1`.</span></span></p> | <span data-ttu-id="09ec2-159">Výchozí hodnota: `1`</span><span class="sxs-lookup"><span data-stu-id="09ec2-159">Default: `1`</span></span><br><span data-ttu-id="09ec2-160">Min: `1`</span><span class="sxs-lookup"><span data-stu-id="09ec2-160">Min: `1`</span></span><br><span data-ttu-id="09ec2-161">Max: `100`&dagger;</span><span class="sxs-lookup"><span data-stu-id="09ec2-161">Max: `100`&dagger;</span></span> |
| `processPath` | <p><span data-ttu-id="09ec2-162">Požadovaný atribut typu string.</span><span class="sxs-lookup"><span data-stu-id="09ec2-162">Required string attribute.</span></span></p><p><span data-ttu-id="09ec2-163">Cesta ke spustitelnému souboru, který spustí nějaký proces naslouchání požadavků protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="09ec2-163">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="09ec2-164">Jsou podporovány relativní cesty.</span><span class="sxs-lookup"><span data-stu-id="09ec2-164">Relative paths are supported.</span></span> <span data-ttu-id="09ec2-165">Pokud cestu začíná `.`, cesta se považuje za kořeni webu.</span><span class="sxs-lookup"><span data-stu-id="09ec2-165">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="09ec2-166">Volitelný celočíselný atribut.</span><span class="sxs-lookup"><span data-stu-id="09ec2-166">Optional integer attribute.</span></span></p><p><span data-ttu-id="09ec2-167">Určuje, kolikrát podle procesu **processPath** může při selhání za minutu.</span><span class="sxs-lookup"><span data-stu-id="09ec2-167">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="09ec2-168">Pokud je tento limit překročen, modulu zastaví spuštění procesu pro zbytek minuty.</span><span class="sxs-lookup"><span data-stu-id="09ec2-168">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p><p><span data-ttu-id="09ec2-169">Není podporováno s hostitelem v procesu.</span><span class="sxs-lookup"><span data-stu-id="09ec2-169">Not supported with in-process hosting.</span></span></p> | <span data-ttu-id="09ec2-170">Výchozí hodnota: `10`</span><span class="sxs-lookup"><span data-stu-id="09ec2-170">Default: `10`</span></span><br><span data-ttu-id="09ec2-171">Min: `0`</span><span class="sxs-lookup"><span data-stu-id="09ec2-171">Min: `0`</span></span><br><span data-ttu-id="09ec2-172">Max: `100`</span><span class="sxs-lookup"><span data-stu-id="09ec2-172">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="09ec2-173">Atribut volitelný časový interval.</span><span class="sxs-lookup"><span data-stu-id="09ec2-173">Optional timespan attribute.</span></span></p><p><span data-ttu-id="09ec2-174">Určuje dobu, pro kterou modul ASP.NET Core čeká na odpověď z procesu naslouchání na ASPNETCORE_PORT %.</span><span class="sxs-lookup"><span data-stu-id="09ec2-174">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="09ec2-175">Ve verzích modul ASP.NET Core dodávané s verzí technologie ASP.NET Core 2.1 nebo novější `requestTimeout` je zadán v hodiny, minuty a sekundy.</span><span class="sxs-lookup"><span data-stu-id="09ec2-175">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p><p><span data-ttu-id="09ec2-176">Neplatí pro hostování v procesu.</span><span class="sxs-lookup"><span data-stu-id="09ec2-176">Doesn't apply to in-process hosting.</span></span> <span data-ttu-id="09ec2-177">Hostování v procesu, čeká modul app ke zpracování požadavku.</span><span class="sxs-lookup"><span data-stu-id="09ec2-177">For in-process hosting, the module waits for the app to process the request.</span></span></p> | <span data-ttu-id="09ec2-178">Výchozí hodnota: `00:02:00`</span><span class="sxs-lookup"><span data-stu-id="09ec2-178">Default: `00:02:00`</span></span><br><span data-ttu-id="09ec2-179">Min: `00:00:00`</span><span class="sxs-lookup"><span data-stu-id="09ec2-179">Min: `00:00:00`</span></span><br><span data-ttu-id="09ec2-180">Max: `360:00:00`</span><span class="sxs-lookup"><span data-stu-id="09ec2-180">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="09ec2-181">Volitelný celočíselný atribut.</span><span class="sxs-lookup"><span data-stu-id="09ec2-181">Optional integer attribute.</span></span></p><p><span data-ttu-id="09ec2-182">Doba trvání v sekundách, které modul čeká na spustitelný soubor na řádné vypnutí při *app_offline.htm* je detekován soubor.</span><span class="sxs-lookup"><span data-stu-id="09ec2-182">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="09ec2-183">Výchozí hodnota: `10`</span><span class="sxs-lookup"><span data-stu-id="09ec2-183">Default: `10`</span></span><br><span data-ttu-id="09ec2-184">Min: `0`</span><span class="sxs-lookup"><span data-stu-id="09ec2-184">Min: `0`</span></span><br><span data-ttu-id="09ec2-185">Max: `600`</span><span class="sxs-lookup"><span data-stu-id="09ec2-185">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="09ec2-186">Volitelný celočíselný atribut.</span><span class="sxs-lookup"><span data-stu-id="09ec2-186">Optional integer attribute.</span></span></p><p><span data-ttu-id="09ec2-187">Doba trvání v sekundách, které modul čeká na spustitelný soubor ke spuštění procesu naslouchání na portu.</span><span class="sxs-lookup"><span data-stu-id="09ec2-187">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="09ec2-188">Pokud je překročen časový limit, modul ukončí proces.</span><span class="sxs-lookup"><span data-stu-id="09ec2-188">If this time limit is exceeded, the module kills the process.</span></span> <span data-ttu-id="09ec2-189">Modul se pokusí znovu spustit proces, když ji dostane novou žádost a pokusit se restartovat proces na dalších příchozích požadavků, pokud se aplikaci nepodaří spustit **rapidFailsPerMinute** málokdy za posledních minuta se zajištěním provozu.</span><span class="sxs-lookup"><span data-stu-id="09ec2-189">The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="09ec2-190">Hodnota 0 (nula) je **ne** za neomezený časový limit.</span><span class="sxs-lookup"><span data-stu-id="09ec2-190">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="09ec2-191">Výchozí hodnota: `120`</span><span class="sxs-lookup"><span data-stu-id="09ec2-191">Default: `120`</span></span><br><span data-ttu-id="09ec2-192">Min: `0`</span><span class="sxs-lookup"><span data-stu-id="09ec2-192">Min: `0`</span></span><br><span data-ttu-id="09ec2-193">Max: `3600`</span><span class="sxs-lookup"><span data-stu-id="09ec2-193">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="09ec2-194">Volitelný logický atribut.</span><span class="sxs-lookup"><span data-stu-id="09ec2-194">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="09ec2-195">Při hodnotě true se **stdout** a **stderr** pro proces určený v **processPath** se přesměrují do souboru zadaného v **stdoutLogFile**.</span><span class="sxs-lookup"><span data-stu-id="09ec2-195">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="09ec2-196">Volitelný atribut řetězce.</span><span class="sxs-lookup"><span data-stu-id="09ec2-196">Optional string attribute.</span></span></p><p><span data-ttu-id="09ec2-197">Určuje relativní nebo absolutní cestu, pro kterou **stdout** a **stderr** z procesu podle **processPath** přihlášeni.</span><span class="sxs-lookup"><span data-stu-id="09ec2-197">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="09ec2-198">Relativní cesty jsou relativní vzhledem k kořen webu.</span><span class="sxs-lookup"><span data-stu-id="09ec2-198">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="09ec2-199">Libovolnou cestu od `.` jsou relativní vzhledem k webu kořenové a všechny ostatní cesty jsou považovány za absolutní cesty.</span><span class="sxs-lookup"><span data-stu-id="09ec2-199">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="09ec2-200">Všechny složky v cestě k dispozici musí existovat v pořadí pro modul pro vytvoření souboru protokolu.</span><span class="sxs-lookup"><span data-stu-id="09ec2-200">Any folders provided in the path must exist in order for the module to create the log file.</span></span> <span data-ttu-id="09ec2-201">Pomocí oddělovače podtržítko, časové razítko, ID procesu a přípona souboru (*.log*) jsou přidány na poslední segment **stdoutLogFile** cestu.</span><span class="sxs-lookup"><span data-stu-id="09ec2-201">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="09ec2-202">Pokud `.\logs\stdout` je zadaný jako hodnota, je uložen protokolu příklad stdout jako *stdout_20180205194132_1934.log* v *protokoly* složky při uložení 5 2. 2018 v 19:41:32 s ID procesu sady 1934.</span><span class="sxs-lookup"><span data-stu-id="09ec2-202">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

::: moniker-end

::: moniker range="= aspnetcore-2.1"

| <span data-ttu-id="09ec2-203">Atribut</span><span class="sxs-lookup"><span data-stu-id="09ec2-203">Attribute</span></span> | <span data-ttu-id="09ec2-204">Popis</span><span class="sxs-lookup"><span data-stu-id="09ec2-204">Description</span></span> | <span data-ttu-id="09ec2-205">Výchozí</span><span class="sxs-lookup"><span data-stu-id="09ec2-205">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="09ec2-206">Volitelný atribut řetězce.</span><span class="sxs-lookup"><span data-stu-id="09ec2-206">Optional string attribute.</span></span></p><p><span data-ttu-id="09ec2-207">Argumenty pro spustitelný soubor určený v **processPath**.</span><span class="sxs-lookup"><span data-stu-id="09ec2-207">Arguments to the executable specified in **processPath**.</span></span></p>| |
| `disableStartUpErrorPage` | <p><span data-ttu-id="09ec2-208">Volitelný logický atribut.</span><span class="sxs-lookup"><span data-stu-id="09ec2-208">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="09ec2-209">Při hodnotě true **502.5 – selhání procesu** Potlačené stránky a znakovou stránku 502 stav nakonfigurované v *web.config* má přednost před.</span><span class="sxs-lookup"><span data-stu-id="09ec2-209">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="09ec2-210">Volitelný logický atribut.</span><span class="sxs-lookup"><span data-stu-id="09ec2-210">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="09ec2-211">Při hodnotě true se token předá podřízený proces naslouchání na ASPNETCORE_PORT % jako záhlaví "MS-ASPNETCORE-WINAUTHTOKEN" každý požadavek.</span><span class="sxs-lookup"><span data-stu-id="09ec2-211">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="09ec2-212">Je odpovědností tento proces pro volání CloseHandle na tento token každý požadavek.</span><span class="sxs-lookup"><span data-stu-id="09ec2-212">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `processesPerApplication` | <p><span data-ttu-id="09ec2-213">Volitelný celočíselný atribut.</span><span class="sxs-lookup"><span data-stu-id="09ec2-213">Optional integer attribute.</span></span></p><p><span data-ttu-id="09ec2-214">Určuje počet instancí procesu zadaný v **processPath** nastavení, které lze se nespředený nahoru na app.</span><span class="sxs-lookup"><span data-stu-id="09ec2-214">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p> | <span data-ttu-id="09ec2-215">Výchozí hodnota: `1`</span><span class="sxs-lookup"><span data-stu-id="09ec2-215">Default: `1`</span></span><br><span data-ttu-id="09ec2-216">Min: `1`</span><span class="sxs-lookup"><span data-stu-id="09ec2-216">Min: `1`</span></span><br><span data-ttu-id="09ec2-217">Max: `100`</span><span class="sxs-lookup"><span data-stu-id="09ec2-217">Max: `100`</span></span> |
| `processPath` | <p><span data-ttu-id="09ec2-218">Požadovaný atribut typu string.</span><span class="sxs-lookup"><span data-stu-id="09ec2-218">Required string attribute.</span></span></p><p><span data-ttu-id="09ec2-219">Cesta ke spustitelnému souboru, který spustí nějaký proces naslouchání požadavků protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="09ec2-219">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="09ec2-220">Jsou podporovány relativní cesty.</span><span class="sxs-lookup"><span data-stu-id="09ec2-220">Relative paths are supported.</span></span> <span data-ttu-id="09ec2-221">Pokud cestu začíná `.`, cesta se považuje za kořeni webu.</span><span class="sxs-lookup"><span data-stu-id="09ec2-221">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="09ec2-222">Volitelný celočíselný atribut.</span><span class="sxs-lookup"><span data-stu-id="09ec2-222">Optional integer attribute.</span></span></p><p><span data-ttu-id="09ec2-223">Určuje, kolikrát podle procesu **processPath** může při selhání za minutu.</span><span class="sxs-lookup"><span data-stu-id="09ec2-223">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="09ec2-224">Pokud je tento limit překročen, modulu zastaví spuštění procesu pro zbytek minuty.</span><span class="sxs-lookup"><span data-stu-id="09ec2-224">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p> | <span data-ttu-id="09ec2-225">Výchozí hodnota: `10`</span><span class="sxs-lookup"><span data-stu-id="09ec2-225">Default: `10`</span></span><br><span data-ttu-id="09ec2-226">Min: `0`</span><span class="sxs-lookup"><span data-stu-id="09ec2-226">Min: `0`</span></span><br><span data-ttu-id="09ec2-227">Max: `100`</span><span class="sxs-lookup"><span data-stu-id="09ec2-227">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="09ec2-228">Atribut volitelný časový interval.</span><span class="sxs-lookup"><span data-stu-id="09ec2-228">Optional timespan attribute.</span></span></p><p><span data-ttu-id="09ec2-229">Určuje dobu, pro kterou modul ASP.NET Core čeká na odpověď z procesu naslouchání na ASPNETCORE_PORT %.</span><span class="sxs-lookup"><span data-stu-id="09ec2-229">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="09ec2-230">Ve verzích modul ASP.NET Core dodávané s verzí technologie ASP.NET Core 2.1 nebo novější `requestTimeout` je zadán v hodiny, minuty a sekundy.</span><span class="sxs-lookup"><span data-stu-id="09ec2-230">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p> | <span data-ttu-id="09ec2-231">Výchozí hodnota: `00:02:00`</span><span class="sxs-lookup"><span data-stu-id="09ec2-231">Default: `00:02:00`</span></span><br><span data-ttu-id="09ec2-232">Min: `00:00:00`</span><span class="sxs-lookup"><span data-stu-id="09ec2-232">Min: `00:00:00`</span></span><br><span data-ttu-id="09ec2-233">Max: `360:00:00`</span><span class="sxs-lookup"><span data-stu-id="09ec2-233">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="09ec2-234">Volitelný celočíselný atribut.</span><span class="sxs-lookup"><span data-stu-id="09ec2-234">Optional integer attribute.</span></span></p><p><span data-ttu-id="09ec2-235">Doba trvání v sekundách, které modul čeká na spustitelný soubor na řádné vypnutí při *app_offline.htm* je detekován soubor.</span><span class="sxs-lookup"><span data-stu-id="09ec2-235">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="09ec2-236">Výchozí hodnota: `10`</span><span class="sxs-lookup"><span data-stu-id="09ec2-236">Default: `10`</span></span><br><span data-ttu-id="09ec2-237">Min: `0`</span><span class="sxs-lookup"><span data-stu-id="09ec2-237">Min: `0`</span></span><br><span data-ttu-id="09ec2-238">Max: `600`</span><span class="sxs-lookup"><span data-stu-id="09ec2-238">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="09ec2-239">Volitelný celočíselný atribut.</span><span class="sxs-lookup"><span data-stu-id="09ec2-239">Optional integer attribute.</span></span></p><p><span data-ttu-id="09ec2-240">Doba trvání v sekundách, které modul čeká na spustitelný soubor ke spuštění procesu naslouchání na portu.</span><span class="sxs-lookup"><span data-stu-id="09ec2-240">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="09ec2-241">Pokud je překročen časový limit, modul ukončí proces.</span><span class="sxs-lookup"><span data-stu-id="09ec2-241">If this time limit is exceeded, the module kills the process.</span></span> <span data-ttu-id="09ec2-242">Modul se pokusí znovu spustit proces, když ji dostane novou žádost a pokusit se restartovat proces na dalších příchozích požadavků, pokud se aplikaci nepodaří spustit **rapidFailsPerMinute** málokdy za posledních minuta se zajištěním provozu.</span><span class="sxs-lookup"><span data-stu-id="09ec2-242">The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="09ec2-243">Hodnota 0 (nula) je **ne** za neomezený časový limit.</span><span class="sxs-lookup"><span data-stu-id="09ec2-243">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="09ec2-244">Výchozí hodnota: `120`</span><span class="sxs-lookup"><span data-stu-id="09ec2-244">Default: `120`</span></span><br><span data-ttu-id="09ec2-245">Min: `0`</span><span class="sxs-lookup"><span data-stu-id="09ec2-245">Min: `0`</span></span><br><span data-ttu-id="09ec2-246">Max: `3600`</span><span class="sxs-lookup"><span data-stu-id="09ec2-246">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="09ec2-247">Volitelný logický atribut.</span><span class="sxs-lookup"><span data-stu-id="09ec2-247">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="09ec2-248">Při hodnotě true se **stdout** a **stderr** pro proces určený v **processPath** se přesměrují do souboru zadaného v **stdoutLogFile**.</span><span class="sxs-lookup"><span data-stu-id="09ec2-248">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="09ec2-249">Volitelný atribut řetězce.</span><span class="sxs-lookup"><span data-stu-id="09ec2-249">Optional string attribute.</span></span></p><p><span data-ttu-id="09ec2-250">Určuje relativní nebo absolutní cestu, pro kterou **stdout** a **stderr** z procesu podle **processPath** přihlášeni.</span><span class="sxs-lookup"><span data-stu-id="09ec2-250">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="09ec2-251">Relativní cesty jsou relativní vzhledem k kořen webu.</span><span class="sxs-lookup"><span data-stu-id="09ec2-251">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="09ec2-252">Libovolnou cestu od `.` jsou relativní vzhledem k webu kořenové a všechny ostatní cesty jsou považovány za absolutní cesty.</span><span class="sxs-lookup"><span data-stu-id="09ec2-252">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="09ec2-253">Všechny složky v cestě k dispozici musí existovat v pořadí pro modul pro vytvoření souboru protokolu.</span><span class="sxs-lookup"><span data-stu-id="09ec2-253">Any folders provided in the path must exist in order for the module to create the log file.</span></span> <span data-ttu-id="09ec2-254">Pomocí oddělovače podtržítko, časové razítko, ID procesu a přípona souboru (*.log*) jsou přidány na poslední segment **stdoutLogFile** cestu.</span><span class="sxs-lookup"><span data-stu-id="09ec2-254">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="09ec2-255">Pokud `.\logs\stdout` je zadaný jako hodnota, je uložen protokolu příklad stdout jako *stdout_20180205194132_1934.log* v *protokoly* složky při uložení 5 2. 2018 v 19:41:32 s ID procesu sady 1934.</span><span class="sxs-lookup"><span data-stu-id="09ec2-255">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

| <span data-ttu-id="09ec2-256">Atribut</span><span class="sxs-lookup"><span data-stu-id="09ec2-256">Attribute</span></span> | <span data-ttu-id="09ec2-257">Popis</span><span class="sxs-lookup"><span data-stu-id="09ec2-257">Description</span></span> | <span data-ttu-id="09ec2-258">Výchozí</span><span class="sxs-lookup"><span data-stu-id="09ec2-258">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="09ec2-259">Volitelný atribut řetězce.</span><span class="sxs-lookup"><span data-stu-id="09ec2-259">Optional string attribute.</span></span></p><p><span data-ttu-id="09ec2-260">Argumenty pro spustitelný soubor určený v **processPath**.</span><span class="sxs-lookup"><span data-stu-id="09ec2-260">Arguments to the executable specified in **processPath**.</span></span></p>| |
| `disableStartUpErrorPage` | <p><span data-ttu-id="09ec2-261">Volitelný logický atribut.</span><span class="sxs-lookup"><span data-stu-id="09ec2-261">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="09ec2-262">Při hodnotě true **502.5 – selhání procesu** Potlačené stránky a znakovou stránku 502 stav nakonfigurované v *web.config* má přednost před.</span><span class="sxs-lookup"><span data-stu-id="09ec2-262">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="09ec2-263">Volitelný logický atribut.</span><span class="sxs-lookup"><span data-stu-id="09ec2-263">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="09ec2-264">Při hodnotě true se token předá podřízený proces naslouchání na ASPNETCORE_PORT % jako záhlaví "MS-ASPNETCORE-WINAUTHTOKEN" každý požadavek.</span><span class="sxs-lookup"><span data-stu-id="09ec2-264">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="09ec2-265">Je odpovědností tento proces pro volání CloseHandle na tento token každý požadavek.</span><span class="sxs-lookup"><span data-stu-id="09ec2-265">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `processesPerApplication` | <p><span data-ttu-id="09ec2-266">Volitelný celočíselný atribut.</span><span class="sxs-lookup"><span data-stu-id="09ec2-266">Optional integer attribute.</span></span></p><p><span data-ttu-id="09ec2-267">Určuje počet instancí procesu zadaný v **processPath** nastavení, které lze se nespředený nahoru na app.</span><span class="sxs-lookup"><span data-stu-id="09ec2-267">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p> | <span data-ttu-id="09ec2-268">Výchozí hodnota: `1`</span><span class="sxs-lookup"><span data-stu-id="09ec2-268">Default: `1`</span></span><br><span data-ttu-id="09ec2-269">Min: `1`</span><span class="sxs-lookup"><span data-stu-id="09ec2-269">Min: `1`</span></span><br><span data-ttu-id="09ec2-270">Max: `100`</span><span class="sxs-lookup"><span data-stu-id="09ec2-270">Max: `100`</span></span> |
| `processPath` | <p><span data-ttu-id="09ec2-271">Požadovaný atribut typu string.</span><span class="sxs-lookup"><span data-stu-id="09ec2-271">Required string attribute.</span></span></p><p><span data-ttu-id="09ec2-272">Cesta ke spustitelnému souboru, který spustí nějaký proces naslouchání požadavků protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="09ec2-272">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="09ec2-273">Jsou podporovány relativní cesty.</span><span class="sxs-lookup"><span data-stu-id="09ec2-273">Relative paths are supported.</span></span> <span data-ttu-id="09ec2-274">Pokud cestu začíná `.`, cesta se považuje za kořeni webu.</span><span class="sxs-lookup"><span data-stu-id="09ec2-274">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="09ec2-275">Volitelný celočíselný atribut.</span><span class="sxs-lookup"><span data-stu-id="09ec2-275">Optional integer attribute.</span></span></p><p><span data-ttu-id="09ec2-276">Určuje, kolikrát podle procesu **processPath** může při selhání za minutu.</span><span class="sxs-lookup"><span data-stu-id="09ec2-276">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="09ec2-277">Pokud je tento limit překročen, modulu zastaví spuštění procesu pro zbytek minuty.</span><span class="sxs-lookup"><span data-stu-id="09ec2-277">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p> | <span data-ttu-id="09ec2-278">Výchozí hodnota: `10`</span><span class="sxs-lookup"><span data-stu-id="09ec2-278">Default: `10`</span></span><br><span data-ttu-id="09ec2-279">Min: `0`</span><span class="sxs-lookup"><span data-stu-id="09ec2-279">Min: `0`</span></span><br><span data-ttu-id="09ec2-280">Max: `100`</span><span class="sxs-lookup"><span data-stu-id="09ec2-280">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="09ec2-281">Atribut volitelný časový interval.</span><span class="sxs-lookup"><span data-stu-id="09ec2-281">Optional timespan attribute.</span></span></p><p><span data-ttu-id="09ec2-282">Určuje dobu, pro kterou modul ASP.NET Core čeká na odpověď z procesu naslouchání na ASPNETCORE_PORT %.</span><span class="sxs-lookup"><span data-stu-id="09ec2-282">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="09ec2-283">Ve verzích modul ASP.NET Core dodávané s verzí technologie ASP.NET Core 2.0 nebo starší `requestTimeout` musí být zadán v celých minutách, v opačném případě výchozí hodnota 2 minuty.</span><span class="sxs-lookup"><span data-stu-id="09ec2-283">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.0 or earlier, the `requestTimeout` must be specified in whole minutes only, otherwise it defaults to 2 minutes.</span></span></p> | <span data-ttu-id="09ec2-284">Výchozí hodnota: `00:02:00`</span><span class="sxs-lookup"><span data-stu-id="09ec2-284">Default: `00:02:00`</span></span><br><span data-ttu-id="09ec2-285">Min: `00:00:00`</span><span class="sxs-lookup"><span data-stu-id="09ec2-285">Min: `00:00:00`</span></span><br><span data-ttu-id="09ec2-286">Max: `360:00:00`</span><span class="sxs-lookup"><span data-stu-id="09ec2-286">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="09ec2-287">Volitelný celočíselný atribut.</span><span class="sxs-lookup"><span data-stu-id="09ec2-287">Optional integer attribute.</span></span></p><p><span data-ttu-id="09ec2-288">Doba trvání v sekundách, které modul čeká na spustitelný soubor na řádné vypnutí při *app_offline.htm* je detekován soubor.</span><span class="sxs-lookup"><span data-stu-id="09ec2-288">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="09ec2-289">Výchozí hodnota: `10`</span><span class="sxs-lookup"><span data-stu-id="09ec2-289">Default: `10`</span></span><br><span data-ttu-id="09ec2-290">Min: `0`</span><span class="sxs-lookup"><span data-stu-id="09ec2-290">Min: `0`</span></span><br><span data-ttu-id="09ec2-291">Max: `600`</span><span class="sxs-lookup"><span data-stu-id="09ec2-291">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="09ec2-292">Volitelný celočíselný atribut.</span><span class="sxs-lookup"><span data-stu-id="09ec2-292">Optional integer attribute.</span></span></p><p><span data-ttu-id="09ec2-293">Doba trvání v sekundách, které modul čeká na spustitelný soubor ke spuštění procesu naslouchání na portu.</span><span class="sxs-lookup"><span data-stu-id="09ec2-293">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="09ec2-294">Pokud je překročen časový limit, modul ukončí proces.</span><span class="sxs-lookup"><span data-stu-id="09ec2-294">If this time limit is exceeded, the module kills the process.</span></span> <span data-ttu-id="09ec2-295">Modul se pokusí znovu spustit proces, když ji dostane novou žádost a pokusit se restartovat proces na dalších příchozích požadavků, pokud se aplikaci nepodaří spustit **rapidFailsPerMinute** málokdy za posledních minuta se zajištěním provozu.</span><span class="sxs-lookup"><span data-stu-id="09ec2-295">The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p> | <span data-ttu-id="09ec2-296">Výchozí hodnota: `120`</span><span class="sxs-lookup"><span data-stu-id="09ec2-296">Default: `120`</span></span><br><span data-ttu-id="09ec2-297">Min: `0`</span><span class="sxs-lookup"><span data-stu-id="09ec2-297">Min: `0`</span></span><br><span data-ttu-id="09ec2-298">Max: `3600`</span><span class="sxs-lookup"><span data-stu-id="09ec2-298">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="09ec2-299">Volitelný logický atribut.</span><span class="sxs-lookup"><span data-stu-id="09ec2-299">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="09ec2-300">Při hodnotě true se **stdout** a **stderr** pro proces určený v **processPath** se přesměrují do souboru zadaného v **stdoutLogFile**.</span><span class="sxs-lookup"><span data-stu-id="09ec2-300">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="09ec2-301">Volitelný atribut řetězce.</span><span class="sxs-lookup"><span data-stu-id="09ec2-301">Optional string attribute.</span></span></p><p><span data-ttu-id="09ec2-302">Určuje relativní nebo absolutní cestu, pro kterou **stdout** a **stderr** z procesu podle **processPath** přihlášeni.</span><span class="sxs-lookup"><span data-stu-id="09ec2-302">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="09ec2-303">Relativní cesty jsou relativní vzhledem k kořen webu.</span><span class="sxs-lookup"><span data-stu-id="09ec2-303">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="09ec2-304">Libovolnou cestu od `.` jsou relativní vzhledem k webu kořenové a všechny ostatní cesty jsou považovány za absolutní cesty.</span><span class="sxs-lookup"><span data-stu-id="09ec2-304">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="09ec2-305">Všechny složky v cestě k dispozici musí existovat v pořadí pro modul pro vytvoření souboru protokolu.</span><span class="sxs-lookup"><span data-stu-id="09ec2-305">Any folders provided in the path must exist in order for the module to create the log file.</span></span> <span data-ttu-id="09ec2-306">Pomocí oddělovače podtržítko, časové razítko, ID procesu a přípona souboru (*.log*) jsou přidány na poslední segment **stdoutLogFile** cestu.</span><span class="sxs-lookup"><span data-stu-id="09ec2-306">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="09ec2-307">Pokud `.\logs\stdout` je zadaný jako hodnota, je uložen protokolu příklad stdout jako *stdout_20180205194132_1934.log* v *protokoly* složky při uložení 5 2. 2018 v 19:41:32 s ID procesu sady 1934.</span><span class="sxs-lookup"><span data-stu-id="09ec2-307">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

::: moniker-end

### <a name="setting-environment-variables"></a><span data-ttu-id="09ec2-308">Nastavení proměnných prostředí</span><span class="sxs-lookup"><span data-stu-id="09ec2-308">Setting environment variables</span></span>

<span data-ttu-id="09ec2-309">Proměnné prostředí se dá nastavit pro proces v `processPath` atribut.</span><span class="sxs-lookup"><span data-stu-id="09ec2-309">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="09ec2-310">Zadat proměnné prostředí s `environmentVariable` podřízený prvek `environmentVariables` prvek kolekce.</span><span class="sxs-lookup"><span data-stu-id="09ec2-310">Specify an environment variable with the `environmentVariable` child element of an `environmentVariables` collection element.</span></span> <span data-ttu-id="09ec2-311">Proměnné prostředí nastavené v této části přednost systémové proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="09ec2-311">Environment variables set in this section take precedence over system environment variables.</span></span>

<span data-ttu-id="09ec2-312">Následující příklad nastaví dvou proměnných prostředí.</span><span class="sxs-lookup"><span data-stu-id="09ec2-312">The following example sets two environment variables.</span></span> <span data-ttu-id="09ec2-313">`ASPNETCORE_ENVIRONMENT` nakonfiguruje prostředí aplikace tak, aby `Development`.</span><span class="sxs-lookup"><span data-stu-id="09ec2-313">`ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="09ec2-314">Vývojáři mohou dočasně nastaví tuto hodnotu *web.config* souboru, aby bylo možné vynutit [stránku výjimek pro vývojáře](xref:fundamentals/error-handling) načíst při ladění aplikace výjimky.</span><span class="sxs-lookup"><span data-stu-id="09ec2-314">A developer may temporarily set this value in the *web.config* file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="09ec2-315">`CONFIG_DIR` je příkladem proměnné prostředí, kam má vývojář zapisovat kód, který čte hodnoty při spuštění tvoří cestu pro načtení konfiguračního souboru aplikace.</span><span class="sxs-lookup"><span data-stu-id="09ec2-315">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

::: moniker range=">= aspnetcore-2.2"

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

::: moniker-end

::: moniker range="< aspnetcore-2.2"

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

::: moniker-end

> [!WARNING]
> <span data-ttu-id="09ec2-316">Nastavit pouze `ASPNETCORE_ENVIRONMENT` proměnnou prostředí, aby `Development` na přípravy a testování serverů, které nejsou dostupné k nedůvěryhodným sítím, jako je Internet.</span><span class="sxs-lookup"><span data-stu-id="09ec2-316">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## <a name="appofflinehtm"></a><span data-ttu-id="09ec2-317">app_offline.htm</span><span class="sxs-lookup"><span data-stu-id="09ec2-317">app_offline.htm</span></span>

<span data-ttu-id="09ec2-318">Pokud soubor s názvem *app_offline.htm* je zjištěna v kořenovém adresáři aplikace, že modul ASP.NET Core se pokusí řádné vypnutí aplikace a zastavit zpracování příchozích požadavků.</span><span class="sxs-lookup"><span data-stu-id="09ec2-318">If a file with the name *app_offline.htm* is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="09ec2-319">Pokud aplikace pořád běží po dobu v sekundách podle `shutdownTimeLimit`, že modul ASP.NET Core ukončuje spuštěnému procesu.</span><span class="sxs-lookup"><span data-stu-id="09ec2-319">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="09ec2-320">Zatímco *app_offline.htm* soubor je k dispozici, že modul ASP.NET Core reaguje na požadavky odesílá zpět obsah *app_offline.htm* souboru.</span><span class="sxs-lookup"><span data-stu-id="09ec2-320">While the *app_offline.htm* file is present, the ASP.NET Core Module responds to requests by sending back the contents of the *app_offline.htm* file.</span></span> <span data-ttu-id="09ec2-321">Když *app_offline.htm* soubor bude odstraněn, příští žádosti o spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="09ec2-321">When the *app_offline.htm* file is removed, the next request starts the app.</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="09ec2-322">Pokud používáte model hostingu mimo proces, aplikace nemusí vypnout okamžitě při otevření připojení.</span><span class="sxs-lookup"><span data-stu-id="09ec2-322">When using the out-of-process hosting model, the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="09ec2-323">Připojení soketu websocket bylo třeba může způsobit prodlevu při ukončení aplikace.</span><span class="sxs-lookup"><span data-stu-id="09ec2-323">For example, a websocket connection may delay app shut down.</span></span>

::: moniker-end

## <a name="start-up-error-page"></a><span data-ttu-id="09ec2-324">Spuštění chybovou stránku</span><span class="sxs-lookup"><span data-stu-id="09ec2-324">Start-up error page</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="09ec2-325">Hostování v procesu a mimo proces vytvoření vlastní chybové stránky, když selžou a spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="09ec2-325">Both in-process and out-of-process hosting produce custom error pages when they fail to start the app.</span></span>

<span data-ttu-id="09ec2-326">Pokud se nepodaří najít buď nebo na více instancí procesu žádosti o obslužnou rutinu, že modul ASP.NET Core *500.0 – Chyba načtení obslužné rutiny v procesu/Out – proces* se zobrazí stav znakovou stránku.</span><span class="sxs-lookup"><span data-stu-id="09ec2-326">If the ASP.NET Core Module fails to find either the in-process or out-of-process request handler, a *500.0 - In-Process/Out-Of-Process Handler Load Failure* status code page appears.</span></span>

<span data-ttu-id="09ec2-327">Pro hostování v procesu, pokud se nepodaří spustit aplikaci, že modul ASP.NET Core *500.30 - Start selhání* se zobrazí stav znakovou stránku.</span><span class="sxs-lookup"><span data-stu-id="09ec2-327">For in-process hosting if the ASP.NET Core Module fails to start the app, a *500.30 - Start Failure* status code page appears.</span></span>

<span data-ttu-id="09ec2-328">Pro hostování mimo proces, pokud se nespustí back-endový proces nebo back-endový proces spustí ale nebude moci poslouchat na konfigurovaném portu, že modul ASP.NET Core *502.5 – selhání procesu* se zobrazí stav znakovou stránku.</span><span class="sxs-lookup"><span data-stu-id="09ec2-328">For out-of-process hosting if the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span>

<span data-ttu-id="09ec2-329">Chcete-li potlačit tuto stránku a vrátit se na výchozí služby IIS 5xx stav znakovou stránku, použijte `disableStartUpErrorPage` atribut.</span><span class="sxs-lookup"><span data-stu-id="09ec2-329">To suppress this page and revert to the default IIS 5xx status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="09ec2-330">Další informace o konfiguraci vlastních chybových zpráv, najdete v části [chyby protokolu HTTP \<httpErrors >](/iis/configuration/system.webServer/httpErrors/).</span><span class="sxs-lookup"><span data-stu-id="09ec2-330">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="09ec2-331">Pokud se nespustí back-endový proces nebo back-endový proces spustí ale nebude moci poslouchat na konfigurovaném portu, že modul ASP.NET Core *502.5 – selhání procesu* se zobrazí stav znakovou stránku.</span><span class="sxs-lookup"><span data-stu-id="09ec2-331">If the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span> <span data-ttu-id="09ec2-332">Chcete-li potlačit tuto stránku a vrátit se na výchozí stav služby IIS 502 znakovou stránku, použijte `disableStartUpErrorPage` atribut.</span><span class="sxs-lookup"><span data-stu-id="09ec2-332">To suppress this page and revert to the default IIS 502 status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="09ec2-333">Další informace o konfiguraci vlastních chybových zpráv, najdete v části [chyby protokolu HTTP \<httpErrors >](/iis/configuration/system.webServer/httpErrors/).</span><span class="sxs-lookup"><span data-stu-id="09ec2-333">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

![502.5 proces selhání stav znaková stránka](aspnet-core-module/_static/ANCM-502_5.png)

::: moniker-end

## <a name="log-creation-and-redirection"></a><span data-ttu-id="09ec2-335">Vytvoření protokolu a přesměrování</span><span class="sxs-lookup"><span data-stu-id="09ec2-335">Log creation and redirection</span></span>

<span data-ttu-id="09ec2-336">Modul ASP.NET Core přesměruje výstup stdout a stderr console na disk, pokud `stdoutLogEnabled` a `stdoutLogFile` atributy `aspNetCore` element jsou nastavené.</span><span class="sxs-lookup"><span data-stu-id="09ec2-336">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="09ec2-337">Všechny složky v `stdoutLogFile` cesta musí existovat v pořadí pro modul pro vytvoření souboru protokolu.</span><span class="sxs-lookup"><span data-stu-id="09ec2-337">Any folders in the `stdoutLogFile` path must exist in order for the module to create the log file.</span></span> <span data-ttu-id="09ec2-338">Fond aplikací musí mít oprávnění k zápisu do umístění, ve kterém jsou zapsány protokoly (použijte `IIS AppPool\<app_pool_name>` poskytnout oprávnění k zápisu).</span><span class="sxs-lookup"><span data-stu-id="09ec2-338">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="09ec2-339">Protokoly nejsou střídán, pokud dojde k recyklování procesů/restartování.</span><span class="sxs-lookup"><span data-stu-id="09ec2-339">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="09ec2-340">Je odpovědností hostitel k omezení místa na disku, které využijete v protokolech.</span><span class="sxs-lookup"><span data-stu-id="09ec2-340">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="09ec2-341">Použití protokolu stdout se doporučuje jenom pro řešení potíží při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="09ec2-341">Using the stdout log is only recommended for troubleshooting app startup issues.</span></span> <span data-ttu-id="09ec2-342">Nepoužívejte stdout protokolu pro účely protokolování obecné aplikace.</span><span class="sxs-lookup"><span data-stu-id="09ec2-342">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="09ec2-343">Pro rutiny protokolování v aplikaci ASP.NET Core, použijte protokolování knihovnu, která omezuje velikost souboru protokolu a otočí protokoly.</span><span class="sxs-lookup"><span data-stu-id="09ec2-343">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="09ec2-344">Další informace najdete v tématu [zprostředkovatele přihlášení třetí strany](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="09ec2-344">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="09ec2-345">Časové razítko a soubor rozšíření jsou přidány automaticky při vytvoření souboru protokolu.</span><span class="sxs-lookup"><span data-stu-id="09ec2-345">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="09ec2-346">Název souboru protokolu se skládá připojením časové razítko, ID procesu a přípona souboru (*.log*) na poslední segment `stdoutLogFile` cestu (obvykle *stdout*) oddělené podtržítka.</span><span class="sxs-lookup"><span data-stu-id="09ec2-346">The log file name is composed by appending the timestamp, process ID, and file extension (*.log*) to the last segment of the `stdoutLogFile` path (typically *stdout*) delimited by underscores.</span></span> <span data-ttu-id="09ec2-347">Pokud `stdoutLogFile` cesta končí *stdout*, má název souboru protokolu pro aplikaci s PID 1934 vytvořili 5 2. 2018 v 19:42:32 *stdout_20180205194132_1934.log*.</span><span class="sxs-lookup"><span data-stu-id="09ec2-347">If the `stdoutLogFile` path ends with *stdout*, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name *stdout_20180205194132_1934.log*.</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="09ec2-348">Pokud `stdoutLogEnabled` má hodnotu false, chyby, ke kterým dochází při spuštění aplikace se zachytí a do protokolu událostí, protože ho až 30 KB.</span><span class="sxs-lookup"><span data-stu-id="09ec2-348">If `stdoutLogEnabled` is false, errors that occur on app startup are captured and emitted to the event log up to 30 KB.</span></span> <span data-ttu-id="09ec2-349">Po spuštění se zahodí všechny další protokoly.</span><span class="sxs-lookup"><span data-stu-id="09ec2-349">After startup, all additional logs are discarded.</span></span>

::: moniker-end

<span data-ttu-id="09ec2-350">Následující ukázka `aspNetCore` element konfiguruje stdout protokolování pro aplikace hostované ve službě Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="09ec2-350">The following sample `aspNetCore` element configures stdout logging for an app hosted in Azure App Service.</span></span> <span data-ttu-id="09ec2-351">Místní cesta nebo cesta ke sdílené položce je přijatelné pro místní přihlášení.</span><span class="sxs-lookup"><span data-stu-id="09ec2-351">A local path or network share path is acceptable for local logging.</span></span> <span data-ttu-id="09ec2-352">Ověřte, jestli uživatel identita fondu aplikací má oprávnění k zápisu do zadaná výstupní cesta.</span><span class="sxs-lookup"><span data-stu-id="09ec2-352">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

::: moniker range=">= aspnetcore-2.2"

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="InProcess">
</aspNetCore>
```

::: moniker-end

::: moniker range="< aspnetcore-2.2"

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile="\\?\%home%\LogFiles\stdout">
</aspNetCore>
```

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

## <a name="enhanced-diagnostic-logs"></a><span data-ttu-id="09ec2-353">Rozšířené diagnostické protokoly</span><span class="sxs-lookup"><span data-stu-id="09ec2-353">Enhanced diagnostic logs</span></span>

<span data-ttu-id="09ec2-354">Poskytuje modul ASP.NET Core je konfigurovat, a poskytují rozšířené diagnostické protokoly.</span><span class="sxs-lookup"><span data-stu-id="09ec2-354">The ASP.NET Core Module provides is configurable to provide enhanced diagnostics logs.</span></span> <span data-ttu-id="09ec2-355">Přidat `<handlerSettings>` elementu `<aspNetCore>` prvek *web.config*. Nastavení `debugLevel` k `TRACE` zpřístupňuje větší věrnost diagnostické informace:</span><span class="sxs-lookup"><span data-stu-id="09ec2-355">Add the `<handlerSettings>` element to the `<aspNetCore>` element in *web.config*. Setting the `debugLevel` to `TRACE` exposes a higher fidelity of diagnostic information:</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="InProcess">
  <handlerSettings>
    <handlerSetting name="debugFile" value="aspnetcore-debug.log" />
    <handlerSetting name="debugLevel" value="FILE,TRACE" />
  </handlerSettings>
</aspNetCore>
```

<span data-ttu-id="09ec2-356">Ladit úrovně (`debugLevel`) hodnoty může obsahovat úroveň a umístění.</span><span class="sxs-lookup"><span data-stu-id="09ec2-356">Debug level (`debugLevel`) values can include both the level and the location.</span></span>

<span data-ttu-id="09ec2-357">Úrovně (v pořadí od nejméně na nejpodrobnější):</span><span class="sxs-lookup"><span data-stu-id="09ec2-357">Levels (in order from least to most verbose):</span></span>

* <span data-ttu-id="09ec2-358">CHYBA</span><span class="sxs-lookup"><span data-stu-id="09ec2-358">ERROR</span></span>
* <span data-ttu-id="09ec2-359">UPOZORNĚNÍ</span><span class="sxs-lookup"><span data-stu-id="09ec2-359">WARNING</span></span>
* <span data-ttu-id="09ec2-360">INFORMACE O</span><span class="sxs-lookup"><span data-stu-id="09ec2-360">INFO</span></span>
* <span data-ttu-id="09ec2-361">TRACE</span><span class="sxs-lookup"><span data-stu-id="09ec2-361">TRACE</span></span>

<span data-ttu-id="09ec2-362">Umístění (umístění více jsou povoleny):</span><span class="sxs-lookup"><span data-stu-id="09ec2-362">Locations (multiple locations are permitted):</span></span>

* <span data-ttu-id="09ec2-363">KONZOLY</span><span class="sxs-lookup"><span data-stu-id="09ec2-363">CONSOLE</span></span>
* <span data-ttu-id="09ec2-364">PROTOKOL UDÁLOSTÍ</span><span class="sxs-lookup"><span data-stu-id="09ec2-364">EVENTLOG</span></span>
* <span data-ttu-id="09ec2-365">SOUBOR</span><span class="sxs-lookup"><span data-stu-id="09ec2-365">FILE</span></span>

<span data-ttu-id="09ec2-366">Obslužná rutina nastavení se dá zadat i prostřednictvím proměnné prostředí:</span><span class="sxs-lookup"><span data-stu-id="09ec2-366">The handler settings can also be provided via environment variables:</span></span>

* <span data-ttu-id="09ec2-367">`ASPNETCORE_MODULE_DEBUG_FILE` &ndash; Cesta k souboru protokolu ladění.</span><span class="sxs-lookup"><span data-stu-id="09ec2-367">`ASPNETCORE_MODULE_DEBUG_FILE` &ndash; Path to the debug log file.</span></span> <span data-ttu-id="09ec2-368">(Výchozí: *aspnetcore-debug.log*)</span><span class="sxs-lookup"><span data-stu-id="09ec2-368">(Default: *aspnetcore-debug.log*)</span></span>
* <span data-ttu-id="09ec2-369">`ASPNETCORE_MODULE_DEBUG` &ndash; Ladit nastavení úrovně.</span><span class="sxs-lookup"><span data-stu-id="09ec2-369">`ASPNETCORE_MODULE_DEBUG` &ndash; Debug level setting.</span></span>

> [!WARNING]
> <span data-ttu-id="09ec2-370">Proveďte **není** protokolování ladění povoleno v nasazení pro déle, než se požaduje chcete vyřešit nějaký problém.</span><span class="sxs-lookup"><span data-stu-id="09ec2-370">Do **not** leave debug logging enabled in the deployment for longer than required to troubleshoot an issue.</span></span> <span data-ttu-id="09ec2-371">Velikost souboru protokolu není omezený.</span><span class="sxs-lookup"><span data-stu-id="09ec2-371">The size of the log isn't limited.</span></span> <span data-ttu-id="09ec2-372">Opuštění protokol ladění povoleno může vyčerpat dostupné místo na disku a chybách u aplikací na serveru nebo služby app service.</span><span class="sxs-lookup"><span data-stu-id="09ec2-372">Leaving the debug log enabled can exhaust the available disk space and crash the server or app service.</span></span>

::: moniker-end

<span data-ttu-id="09ec2-373">Naleznete v tématu [konfigurace pomocí souboru web.config](#configuration-with-webconfig) příklad `aspNetCore` prvek *web.config* souboru.</span><span class="sxs-lookup"><span data-stu-id="09ec2-373">See [Configuration with web.config](#configuration-with-webconfig) for an example of the `aspNetCore` element in the *web.config* file.</span></span>

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="09ec2-374">Konfigurace proxy serveru používá protokol HTTP a token pro párování</span><span class="sxs-lookup"><span data-stu-id="09ec2-374">Proxy configuration uses HTTP protocol and a pairing token</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="09ec2-375">*Platí jenom pro hostování mimo proces.*</span><span class="sxs-lookup"><span data-stu-id="09ec2-375">*Only applies to out-of-process hosting.*</span></span>

::: moniker-end

<span data-ttu-id="09ec2-376">Proxy server mezi modul ASP.NET Core a Kestrel používá protokol HTTP.</span><span class="sxs-lookup"><span data-stu-id="09ec2-376">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="09ec2-377">Pomocí protokolu HTTP je optimalizace výkonu, kdy přenos dat mezi modulu a Kestrel probíhá na adresu zpětné smyčky ze síťového rozhraní.</span><span class="sxs-lookup"><span data-stu-id="09ec2-377">Using HTTP is a performance optimization, where the traffic between the module and Kestrel takes place on a loopback address off of the network interface.</span></span> <span data-ttu-id="09ec2-378">Neexistuje žádné riziko odposloucháváním provozu mezi modulu a Kestrel z umístění mimo server.</span><span class="sxs-lookup"><span data-stu-id="09ec2-378">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="09ec2-379">Párování token se používá k zajištění, že požadavků přijatých službou Kestrel byly směrovány přes proxy server službou IIS a nebyl dodán z nějakého jiného zdroje.</span><span class="sxs-lookup"><span data-stu-id="09ec2-379">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="09ec2-380">Vytvoření a nastavení do proměnné prostředí párování token (`ASPNETCORE_TOKEN`) modulu.</span><span class="sxs-lookup"><span data-stu-id="09ec2-380">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="09ec2-381">Párování token byl nastavený i do záhlaví (`MSAspNetCoreToken`) na všechny požadavky směrovány přes proxy server.</span><span class="sxs-lookup"><span data-stu-id="09ec2-381">The pairing token is also set into a header (`MSAspNetCoreToken`) on every proxied request.</span></span> <span data-ttu-id="09ec2-382">Služba IIS Middleware kontroly požadavku že přijme potvrďte, že odpovídá párování hodnota tokenu hlavičky hodnotu proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="09ec2-382">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="09ec2-383">Pokud token hodnoty se neshodují, žádost je zaznamenána a odmítnut.</span><span class="sxs-lookup"><span data-stu-id="09ec2-383">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="09ec2-384">Párování proměnná tokenu prostředí a přenos dat mezi modulu a Kestrel nejsou dostupné z umístění mimo server.</span><span class="sxs-lookup"><span data-stu-id="09ec2-384">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="09ec2-385">Nainstalovat bez mého párování hodnota tokenu nelze útočník odesílat požadavky, které obejít kontrolu v IIS middlewaru.</span><span class="sxs-lookup"><span data-stu-id="09ec2-385">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="09ec2-386">Modul ASP.NET Core s službu IIS sdílenou konfiguraci</span><span class="sxs-lookup"><span data-stu-id="09ec2-386">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="09ec2-387">Modul ASP.NET Core instalační program spustí s oprávněními **systému** účtu.</span><span class="sxs-lookup"><span data-stu-id="09ec2-387">The ASP.NET Core Module installer runs with the privileges of the **SYSTEM** account.</span></span> <span data-ttu-id="09ec2-388">Vzhledem k tomu, že místní systémový účet mít nezmění oprávnění pro sdílenou složku cesta používaná systémem sdílené konfiguraci IIS, instalační program narazí na chybu při pokusu o konfiguraci nastavení modulu v odepření přístupu *applicationHost.config* ve sdílené složce.</span><span class="sxs-lookup"><span data-stu-id="09ec2-388">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer hits an access denied error when attempting to configure the module settings in *applicationHost.config* on the share.</span></span> <span data-ttu-id="09ec2-389">Pokud používáte sdílenou konfiguraci IIS, postupujte podle těchto kroků:</span><span class="sxs-lookup"><span data-stu-id="09ec2-389">When using an IIS Shared Configuration, follow these steps:</span></span>

1. <span data-ttu-id="09ec2-390">Zakážete sdílenou konfiguraci IIS.</span><span class="sxs-lookup"><span data-stu-id="09ec2-390">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="09ec2-391">Spusťte instalační program.</span><span class="sxs-lookup"><span data-stu-id="09ec2-391">Run the installer.</span></span>
1. <span data-ttu-id="09ec2-392">Export aktualizovaný *applicationHost.config* souboru do sdílené složky.</span><span class="sxs-lookup"><span data-stu-id="09ec2-392">Export the updated *applicationHost.config* file to the share.</span></span>
1. <span data-ttu-id="09ec2-393">Znovu povolte sdílenou konfiguraci IIS.</span><span class="sxs-lookup"><span data-stu-id="09ec2-393">Re-enable the IIS Shared Configuration.</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="09ec2-394">Verze modulu a hostování sady Instalační protokoly</span><span class="sxs-lookup"><span data-stu-id="09ec2-394">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="09ec2-395">Chcete-li zjistit verzi nainstalovaný modul ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="09ec2-395">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="09ec2-396">V hostitelském systému, přejděte na *%windir%\System32\inetsrv*.</span><span class="sxs-lookup"><span data-stu-id="09ec2-396">On the hosting system, navigate to *%windir%\System32\inetsrv*.</span></span>
1. <span data-ttu-id="09ec2-397">Vyhledejte *aspnetcore.dll* souboru.</span><span class="sxs-lookup"><span data-stu-id="09ec2-397">Locate the *aspnetcore.dll* file.</span></span>
1. <span data-ttu-id="09ec2-398">Klikněte pravým tlačítkem na soubor a vyberte **vlastnosti** z kontextové nabídky.</span><span class="sxs-lookup"><span data-stu-id="09ec2-398">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="09ec2-399">Vyberte **podrobnosti** kartu. **Verze souboru** a **verze produktu** představují nainstalovaná verze modulu.</span><span class="sxs-lookup"><span data-stu-id="09ec2-399">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="09ec2-400">Instalační protokoly hostování sady prostředků modulu se nacházejí v *C:\\uživatelé\\% UserName %\\AppData\\místní\\Temp*. Soubor *dd_DotNetCoreWinSvrHosting__\<časové razítko > _000_AspNetCoreModule_x64.log*.</span><span class="sxs-lookup"><span data-stu-id="09ec2-400">The Hosting Bundle installer logs for the module are found at *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. The file is named *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="09ec2-401">Modul, schéma a konfiguraci umístění souborů</span><span class="sxs-lookup"><span data-stu-id="09ec2-401">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="09ec2-402">Modul</span><span class="sxs-lookup"><span data-stu-id="09ec2-402">Module</span></span>

<span data-ttu-id="09ec2-403">**IIS (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="09ec2-403">**IIS (x86/amd64):**</span></span>

   * <span data-ttu-id="09ec2-404">%windir%\System32\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="09ec2-404">%windir%\System32\inetsrv\aspnetcore.dll</span></span>

   * <span data-ttu-id="09ec2-405">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="09ec2-405">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span></span>

::: moniker range=">= aspnetcore-2.2"

   * <span data-ttu-id="09ec2-406">%ProgramFiles%\IIS\Asp.NET core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="09ec2-406">%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

   * <span data-ttu-id="09ec2-407">% ProgramFiles (x86) %\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="09ec2-407">%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

::: moniker-end

<span data-ttu-id="09ec2-408">**Služba IIS Express (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="09ec2-408">**IIS Express (x86/amd64):**</span></span>

   * <span data-ttu-id="09ec2-409">%ProgramFiles%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="09ec2-409">%ProgramFiles%\IIS Express\aspnetcore.dll</span></span>

   * <span data-ttu-id="09ec2-410">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="09ec2-410">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span></span>

::: moniker range=">= aspnetcore-2.2"

   * <span data-ttu-id="09ec2-411">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="09ec2-411">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

   * <span data-ttu-id="09ec2-412">% ProgramFiles (x86) %\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="09ec2-412">%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

::: moniker-end

### <a name="schema"></a><span data-ttu-id="09ec2-413">Schéma</span><span class="sxs-lookup"><span data-stu-id="09ec2-413">Schema</span></span>

<span data-ttu-id="09ec2-414">**SLUŽBA IIS**</span><span class="sxs-lookup"><span data-stu-id="09ec2-414">**IIS**</span></span>

   * <span data-ttu-id="09ec2-415">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="09ec2-415">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span></span>

::: moniker range=">= aspnetcore-2.2"

   * <span data-ttu-id="09ec2-416">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.XML</span><span class="sxs-lookup"><span data-stu-id="09ec2-416">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span></span>

::: moniker-end
<span data-ttu-id="09ec2-417">**Služba IIS Express**</span><span class="sxs-lookup"><span data-stu-id="09ec2-417">**IIS Express**</span></span>

   * <span data-ttu-id="09ec2-418">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="09ec2-418">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span></span>

::: moniker range=">= aspnetcore-2.2"

   * <span data-ttu-id="09ec2-419">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span><span class="sxs-lookup"><span data-stu-id="09ec2-419">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span></span>

::: moniker-end

### <a name="configuration"></a><span data-ttu-id="09ec2-420">Konfigurace</span><span class="sxs-lookup"><span data-stu-id="09ec2-420">Configuration</span></span>

<span data-ttu-id="09ec2-421">**SLUŽBA IIS**</span><span class="sxs-lookup"><span data-stu-id="09ec2-421">**IIS**</span></span>

   * <span data-ttu-id="09ec2-422">%windir%\System32\inetsrv\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="09ec2-422">%windir%\System32\inetsrv\config\applicationHost.config</span></span>

<span data-ttu-id="09ec2-423">**Služba IIS Express**</span><span class="sxs-lookup"><span data-stu-id="09ec2-423">**IIS Express**</span></span>

   * <span data-ttu-id="09ec2-424">%ProgramFiles%\IIS Express\config\templates\PersonalWebServer\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="09ec2-424">%ProgramFiles%\IIS Express\config\templates\PersonalWebServer\applicationHost.config</span></span>

<span data-ttu-id="09ec2-425">Soubory můžete najít tak, že *aspnetcore* v *applicationHost.config* souboru.</span><span class="sxs-lookup"><span data-stu-id="09ec2-425">The files can be found by searching for *aspnetcore* in the *applicationHost.config* file.</span></span>
