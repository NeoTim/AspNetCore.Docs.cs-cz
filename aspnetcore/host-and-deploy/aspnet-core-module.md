---
title: Referenční informace o ASP.NET Core modulu Konfigurace
author: guardrex
description: Zjistěte, jak nakonfigurovat modul ASP.NET Core pro hostování aplikací ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 11/12/2018
uid: host-and-deploy/aspnet-core-module
ms.openlocfilehash: 5a3fd9c3453c07ee550c7de0333c9a49d5d5d1af
ms.sourcegitcommit: e9b99854b0a8021dafabee0db5e1338067f250a9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/28/2018
ms.locfileid: "52450655"
---
# <a name="aspnet-core-module-configuration-reference"></a><span data-ttu-id="0ab65-103">Referenční informace o ASP.NET Core modulu Konfigurace</span><span class="sxs-lookup"><span data-stu-id="0ab65-103">ASP.NET Core Module configuration reference</span></span>

<span data-ttu-id="0ab65-104">Podle [Luke Latham](https://github.com/guardrex), [Rick Anderson](https://twitter.com/RickAndMSFT), [Sourabh Shirhatti](https://twitter.com/sshirhatti), a [Justin Kotalik](https://github.com/jkotalik)</span><span class="sxs-lookup"><span data-stu-id="0ab65-104">By [Luke Latham](https://github.com/guardrex), [Rick Anderson](https://twitter.com/RickAndMSFT), [Sourabh Shirhatti](https://twitter.com/sshirhatti), and [Justin Kotalik](https://github.com/jkotalik)</span></span>

<span data-ttu-id="0ab65-105">Tento dokument obsahuje pokyny o tom, jak nakonfigurovat modul ASP.NET Core pro hostování aplikací ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="0ab65-105">This document provides instructions on how to configure the ASP.NET Core Module for hosting ASP.NET Core apps.</span></span> <span data-ttu-id="0ab65-106">Úvod do modul ASP.NET Core a pokyny k instalaci, najdete v článku [modul ASP.NET Core přehled](xref:fundamentals/servers/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="0ab65-106">For an introduction to the ASP.NET Core Module and installation instructions, see the [ASP.NET Core Module overview](xref:fundamentals/servers/aspnet-core-module).</span></span>

::: moniker range=">= aspnetcore-2.2"

## <a name="hosting-model"></a><span data-ttu-id="0ab65-107">Model hostingu</span><span class="sxs-lookup"><span data-stu-id="0ab65-107">Hosting model</span></span>

<span data-ttu-id="0ab65-108">Modul pro aplikace běžící na .NET Core 2.2 nebo vyšší, podporuje model hostování v procesu za účelem vylepšení výkonu v porovnání s hostitelem (out-of-process) reverzních proxy serverů.</span><span class="sxs-lookup"><span data-stu-id="0ab65-108">For apps running on .NET Core 2.2 or later, the module supports an in-process hosting model for improved performance compared to reverse-proxy (out-of-process) hosting.</span></span> <span data-ttu-id="0ab65-109">Další informace naleznete v tématu <xref:fundamentals/servers/aspnet-core-module#aspnet-core-module-description>.</span><span class="sxs-lookup"><span data-stu-id="0ab65-109">For more information, see <xref:fundamentals/servers/aspnet-core-module#aspnet-core-module-description>.</span></span>

<span data-ttu-id="0ab65-110">Hostování v procsess je vyjádřit výslovný souhlas pro existující aplikace, ale [dotnet nové](/dotnet/core/tools/dotnet-new) výchozí šablony na model hostingu v procesu pro všechny služby IIS a služby IIS Express.</span><span class="sxs-lookup"><span data-stu-id="0ab65-110">In-procsess hosting is opt-in for existing apps, but [dotnet new](/dotnet/core/tools/dotnet-new) templates default to the in-process hosting model for all IIS and IIS Express scenarios.</span></span>

<span data-ttu-id="0ab65-111">Jak nakonfigurovat aplikaci pro hostování v procesu, přidejte `<AspNetCoreHostingModel>` vlastnosti do souboru projektu vaší aplikace (například *MyApp.csproj*) s hodnotou `inprocess` (hostování mimo proces se nastaví pomocí `outofprocess`):</span><span class="sxs-lookup"><span data-stu-id="0ab65-111">To configure an app for in-process hosting, add the `<AspNetCoreHostingModel>` property to the app's project file (for example, *MyApp.csproj*) with a value of `inprocess` (out-of-process hosting is set with `outofprocess`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>inprocess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="0ab65-112">Při hostování v procesu platí následující vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="0ab65-112">The following characteristics apply when hosting in-process:</span></span>

* <span data-ttu-id="0ab65-113">[Kestrel server](xref:fundamentals/servers/kestrel) se nepoužívá.</span><span class="sxs-lookup"><span data-stu-id="0ab65-113">The [Kestrel server](xref:fundamentals/servers/kestrel) isn't used.</span></span> <span data-ttu-id="0ab65-114">Vlastní <xref:Microsoft.AspNetCore.Hosting.Server.IServer> implementaci `IISHttpServer` funguje jako server aplikace.</span><span class="sxs-lookup"><span data-stu-id="0ab65-114">A custom <xref:Microsoft.AspNetCore.Hosting.Server.IServer> implementation, `IISHttpServer` acts as the app's server.</span></span>

* <span data-ttu-id="0ab65-115">[RequestTimeout atribut](#attributes-of-the-aspnetcore-element) neplatí pro hostování v procesu.</span><span class="sxs-lookup"><span data-stu-id="0ab65-115">The [requestTimeout attribute](#attributes-of-the-aspnetcore-element) doesn't apply to in-process hosting.</span></span>

* <span data-ttu-id="0ab65-116">Sdílení fondem aplikací mezi aplikacemi se nepodporuje.</span><span class="sxs-lookup"><span data-stu-id="0ab65-116">Sharing an app pool among apps isn't supported.</span></span> <span data-ttu-id="0ab65-117">Použijte jeden fond aplikací na aplikaci.</span><span class="sxs-lookup"><span data-stu-id="0ab65-117">Use one app pool per app.</span></span>

* <span data-ttu-id="0ab65-118">Při použití [Webdeploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) nebo ručně uvedení [soubor app_offline.htm v nasazení](xref:host-and-deploy/iis/index#locked-deployment-files), aplikace nemusí vypnout okamžitě při otevření připojení.</span><span class="sxs-lookup"><span data-stu-id="0ab65-118">When using [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) or manually placing an [app_offline.htm file in the deployment](xref:host-and-deploy/iis/index#locked-deployment-files), the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="0ab65-119">Připojení soketu websocket bylo třeba může způsobit prodlevu při ukončení aplikace.</span><span class="sxs-lookup"><span data-stu-id="0ab65-119">For example, a websocket connection may delay app shut down.</span></span>

* <span data-ttu-id="0ab65-120">Architektura (bitové verze) (x64 nebo x86) nainstalovaný modul runtime a aplikace musí odpovídat architektuře fondu aplikací.</span><span class="sxs-lookup"><span data-stu-id="0ab65-120">The architecture (bitness) of the app and installed runtime (x64 or x86) must match the architecture of the app pool.</span></span>

* <span data-ttu-id="0ab65-121">Pokud nastavení aplikace hostitele ručně pomocí `WebHostBuilder` (bez použití [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host)) a spuštění aplikace se nikdy přímo na serveru Kestrel (v místním prostředí), volání `UseKestrel` před voláním `UseIISIntegration`.</span><span class="sxs-lookup"><span data-stu-id="0ab65-121">If setting up the app's host manually with `WebHostBuilder` (not using [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host)) and the app is ever run directly on the Kestrel server (self-hosted), call `UseKestrel` before calling `UseIISIntegration`.</span></span> <span data-ttu-id="0ab65-122">Pokud je obrácený pořadí, hostitel se nepodaří spustit.</span><span class="sxs-lookup"><span data-stu-id="0ab65-122">If the order is reversed, the host fails to start.</span></span>

* <span data-ttu-id="0ab65-123">Odpojení klienta jsou zjištěny.</span><span class="sxs-lookup"><span data-stu-id="0ab65-123">Client disconnects are detected.</span></span> <span data-ttu-id="0ab65-124">[HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) token zrušení se zrušila, když se klient odpojí.</span><span class="sxs-lookup"><span data-stu-id="0ab65-124">The [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) cancellation token is cancelled when the client disconnects.</span></span>

* <span data-ttu-id="0ab65-125">`Directory.GetCurrentDirectory()` Vrátí adresáře pracovní proces spuštěný pomocí služby IIS, nikoli adresář aplikace (například *C:\Windows\System32\inetsrv* pro *w3wp.exe*).</span><span class="sxs-lookup"><span data-stu-id="0ab65-125">`Directory.GetCurrentDirectory()` returns the worker directory of the process started by IIS rather than the application directory (for example, *C:\Windows\System32\inetsrv* for *w3wp.exe*).</span></span>

### <a name="hosting-model-changes"></a><span data-ttu-id="0ab65-126">Hostování změny modelu</span><span class="sxs-lookup"><span data-stu-id="0ab65-126">Hosting model changes</span></span>

<span data-ttu-id="0ab65-127">Pokud `hostingModel` nastavení se změnilo v *web.config* souboru (podrobně [konfigurace pomocí souboru web.config](#configuration-with-webconfig) části), modul pro službu IIS recykluje pracovní proces.</span><span class="sxs-lookup"><span data-stu-id="0ab65-127">If the `hostingModel` setting is changed in the *web.config* file (explained in the [Configuration with web.config](#configuration-with-webconfig) section), the module recycles the worker process for IIS.</span></span>

<span data-ttu-id="0ab65-128">Pro službu IIS Express modul nebude recyklovat pracovní proces, ale místo toho aktivuje řádné vypnutí aktuální proces IIS Express.</span><span class="sxs-lookup"><span data-stu-id="0ab65-128">For IIS Express, the module doesn't recycle the worker process but instead triggers a graceful shutdown of the current IIS Express process.</span></span> <span data-ttu-id="0ab65-129">Další požadavek na aplikaci vytvoří nový proces IIS Express.</span><span class="sxs-lookup"><span data-stu-id="0ab65-129">The next request to the app spawns a new IIS Express process.</span></span>

### <a name="process-name"></a><span data-ttu-id="0ab65-130">Název procesu</span><span class="sxs-lookup"><span data-stu-id="0ab65-130">Process name</span></span>

<span data-ttu-id="0ab65-131">`Process.GetCurrentProcess().ProcessName` sestavy `w3wp` / `iisexpress` (v procesu) nebo `dotnet` (out-of-process).</span><span class="sxs-lookup"><span data-stu-id="0ab65-131">`Process.GetCurrentProcess().ProcessName` reports `w3wp`/`iisexpress` (in-process) or `dotnet` (out-of-process).</span></span>

::: moniker-end

## <a name="configuration-with-webconfig"></a><span data-ttu-id="0ab65-132">Konfigurace pomocí souboru web.config</span><span class="sxs-lookup"><span data-stu-id="0ab65-132">Configuration with web.config</span></span>

<span data-ttu-id="0ab65-133">Modul ASP.NET Core, nastavena `aspNetCore` část `system.webServer` uzlu na webu *web.config* souboru.</span><span class="sxs-lookup"><span data-stu-id="0ab65-133">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="0ab65-134">Následující *web.config* soubor je publikován pro [nasazení závisí na architektuře](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) a konfiguruje modul ASP.NET Core pro zpracování požadavků lokality:</span><span class="sxs-lookup"><span data-stu-id="0ab65-134">The following *web.config* file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

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
                  hostingModel="inprocess" />
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

<span data-ttu-id="0ab65-135">Následující *web.config* je publikována pro [samostatná nasazení](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span><span class="sxs-lookup"><span data-stu-id="0ab65-135">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

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
                  hostingModel="inprocess" />
    </system.webServer>
  </location>
</configuration>
```

<span data-ttu-id="0ab65-136"><xref:System.Configuration.SectionInformation.InheritInChildApplications*> Je nastavena na `false` k označení, že nastavení zadané v rámci [ \<umístění >](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) element nedědí od neznámých aplikací, které jsou umístěny v podadresáři aplikace.</span><span class="sxs-lookup"><span data-stu-id="0ab65-136">The <xref:System.Configuration.SectionInformation.InheritInChildApplications*> property is set to `false` to indicate that the settings specified within the [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) element aren't inherited by apps that reside in a subdirectory of the app.</span></span>

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

<span data-ttu-id="0ab65-137">Když je aplikace nasazená na [služby Azure App Service](https://azure.microsoft.com/services/app-service/), `stdoutLogFile` je nastavena cesta `\\?\%home%\LogFiles\stdout`.</span><span class="sxs-lookup"><span data-stu-id="0ab65-137">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="0ab65-138">Protokoly stdout a uloží ji *LogFiles* složce, která je na místě automaticky vytvořené službou.</span><span class="sxs-lookup"><span data-stu-id="0ab65-138">The path saves stdout logs to the *LogFiles* folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="0ab65-139">Informace o konfiguraci dílčí aplikace služby IIS, naleznete v tématu <xref:host-and-deploy/iis/index#sub-applications>.</span><span class="sxs-lookup"><span data-stu-id="0ab65-139">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="0ab65-140">Atributy elementu aspNetCore</span><span class="sxs-lookup"><span data-stu-id="0ab65-140">Attributes of the aspNetCore element</span></span>

::: moniker range=">= aspnetcore-2.2"

| <span data-ttu-id="0ab65-141">Atribut</span><span class="sxs-lookup"><span data-stu-id="0ab65-141">Attribute</span></span> | <span data-ttu-id="0ab65-142">Popis</span><span class="sxs-lookup"><span data-stu-id="0ab65-142">Description</span></span> | <span data-ttu-id="0ab65-143">Výchozí</span><span class="sxs-lookup"><span data-stu-id="0ab65-143">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="0ab65-144">Volitelný atribut řetězce.</span><span class="sxs-lookup"><span data-stu-id="0ab65-144">Optional string attribute.</span></span></p><p><span data-ttu-id="0ab65-145">Argumenty pro spustitelný soubor určený v **processPath**.</span><span class="sxs-lookup"><span data-stu-id="0ab65-145">Arguments to the executable specified in **processPath**.</span></span></p> | |
| `disableStartUpErrorPage` | <p><span data-ttu-id="0ab65-146">Volitelný logický atribut.</span><span class="sxs-lookup"><span data-stu-id="0ab65-146">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="0ab65-147">Při hodnotě true **502.5 – selhání procesu** Potlačené stránky a znakovou stránku 502 stav nakonfigurované v *web.config* má přednost před.</span><span class="sxs-lookup"><span data-stu-id="0ab65-147">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="0ab65-148">Volitelný logický atribut.</span><span class="sxs-lookup"><span data-stu-id="0ab65-148">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="0ab65-149">Při hodnotě true se token předá podřízený proces naslouchání na ASPNETCORE_PORT % jako záhlaví "MS-ASPNETCORE-WINAUTHTOKEN" každý požadavek.</span><span class="sxs-lookup"><span data-stu-id="0ab65-149">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="0ab65-150">Je odpovědností tento proces pro volání CloseHandle na tento token každý požadavek.</span><span class="sxs-lookup"><span data-stu-id="0ab65-150">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `hostingModel` | <p><span data-ttu-id="0ab65-151">Volitelný atribut řetězce.</span><span class="sxs-lookup"><span data-stu-id="0ab65-151">Optional string attribute.</span></span></p><p><span data-ttu-id="0ab65-152">Určuje model hostování jako v procesu (`inprocess`) nebo out-of-process (`outofprocess`).</span><span class="sxs-lookup"><span data-stu-id="0ab65-152">Specifies the hosting model as in-process (`inprocess`) or out-of-process (`outofprocess`).</span></span></p> | `outofprocess` |
| `processesPerApplication` | <p><span data-ttu-id="0ab65-153">Volitelný celočíselný atribut.</span><span class="sxs-lookup"><span data-stu-id="0ab65-153">Optional integer attribute.</span></span></p><p><span data-ttu-id="0ab65-154">Určuje počet instancí procesu zadaný v **processPath** nastavení, které lze se nespředený nahoru na app.</span><span class="sxs-lookup"><span data-stu-id="0ab65-154">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="0ab65-155">&dagger;Hostování v procesu, je omezena na hodnotu `1`.</span><span class="sxs-lookup"><span data-stu-id="0ab65-155">&dagger;For in-process hosting, the value is limited to `1`.</span></span></p> | <span data-ttu-id="0ab65-156">Výchozí hodnota: `1`</span><span class="sxs-lookup"><span data-stu-id="0ab65-156">Default: `1`</span></span><br><span data-ttu-id="0ab65-157">Min: `1`</span><span class="sxs-lookup"><span data-stu-id="0ab65-157">Min: `1`</span></span><br><span data-ttu-id="0ab65-158">Max: `100`&dagger;</span><span class="sxs-lookup"><span data-stu-id="0ab65-158">Max: `100`&dagger;</span></span> |
| `processPath` | <p><span data-ttu-id="0ab65-159">Požadovaný atribut typu string.</span><span class="sxs-lookup"><span data-stu-id="0ab65-159">Required string attribute.</span></span></p><p><span data-ttu-id="0ab65-160">Cesta ke spustitelnému souboru, který spustí nějaký proces naslouchání požadavků protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="0ab65-160">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="0ab65-161">Jsou podporovány relativní cesty.</span><span class="sxs-lookup"><span data-stu-id="0ab65-161">Relative paths are supported.</span></span> <span data-ttu-id="0ab65-162">Pokud cestu začíná `.`, cesta se považuje za kořeni webu.</span><span class="sxs-lookup"><span data-stu-id="0ab65-162">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="0ab65-163">Volitelný celočíselný atribut.</span><span class="sxs-lookup"><span data-stu-id="0ab65-163">Optional integer attribute.</span></span></p><p><span data-ttu-id="0ab65-164">Určuje, kolikrát podle procesu **processPath** může při selhání za minutu.</span><span class="sxs-lookup"><span data-stu-id="0ab65-164">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="0ab65-165">Pokud je tento limit překročen, modulu zastaví spuštění procesu pro zbytek minuty.</span><span class="sxs-lookup"><span data-stu-id="0ab65-165">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p><p><span data-ttu-id="0ab65-166">Není podporováno s hostitelem v procesu.</span><span class="sxs-lookup"><span data-stu-id="0ab65-166">Not supported with in-process hosting.</span></span></p> | <span data-ttu-id="0ab65-167">Výchozí hodnota: `10`</span><span class="sxs-lookup"><span data-stu-id="0ab65-167">Default: `10`</span></span><br><span data-ttu-id="0ab65-168">Min: `0`</span><span class="sxs-lookup"><span data-stu-id="0ab65-168">Min: `0`</span></span><br><span data-ttu-id="0ab65-169">Max: `100`</span><span class="sxs-lookup"><span data-stu-id="0ab65-169">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="0ab65-170">Atribut volitelný časový interval.</span><span class="sxs-lookup"><span data-stu-id="0ab65-170">Optional timespan attribute.</span></span></p><p><span data-ttu-id="0ab65-171">Určuje dobu, pro kterou modul ASP.NET Core čeká na odpověď z procesu naslouchání na ASPNETCORE_PORT %.</span><span class="sxs-lookup"><span data-stu-id="0ab65-171">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="0ab65-172">Ve verzích modul ASP.NET Core dodávané s verzí technologie ASP.NET Core 2.1 nebo novější `requestTimeout` je zadán v hodiny, minuty a sekundy.</span><span class="sxs-lookup"><span data-stu-id="0ab65-172">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p><p><span data-ttu-id="0ab65-173">Neplatí pro hostování v procesu.</span><span class="sxs-lookup"><span data-stu-id="0ab65-173">Doesn't apply to in-process hosting.</span></span> <span data-ttu-id="0ab65-174">Hostování v procesu, čeká modul app ke zpracování požadavku.</span><span class="sxs-lookup"><span data-stu-id="0ab65-174">For in-process hosting, the module waits for the app to process the request.</span></span></p> | <span data-ttu-id="0ab65-175">Výchozí hodnota: `00:02:00`</span><span class="sxs-lookup"><span data-stu-id="0ab65-175">Default: `00:02:00`</span></span><br><span data-ttu-id="0ab65-176">Min: `00:00:00`</span><span class="sxs-lookup"><span data-stu-id="0ab65-176">Min: `00:00:00`</span></span><br><span data-ttu-id="0ab65-177">Max: `360:00:00`</span><span class="sxs-lookup"><span data-stu-id="0ab65-177">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="0ab65-178">Volitelný celočíselný atribut.</span><span class="sxs-lookup"><span data-stu-id="0ab65-178">Optional integer attribute.</span></span></p><p><span data-ttu-id="0ab65-179">Doba trvání v sekundách, které modul čeká na spustitelný soubor na řádné vypnutí při *app_offline.htm* je detekován soubor.</span><span class="sxs-lookup"><span data-stu-id="0ab65-179">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="0ab65-180">Výchozí hodnota: `10`</span><span class="sxs-lookup"><span data-stu-id="0ab65-180">Default: `10`</span></span><br><span data-ttu-id="0ab65-181">Min: `0`</span><span class="sxs-lookup"><span data-stu-id="0ab65-181">Min: `0`</span></span><br><span data-ttu-id="0ab65-182">Max: `600`</span><span class="sxs-lookup"><span data-stu-id="0ab65-182">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="0ab65-183">Volitelný celočíselný atribut.</span><span class="sxs-lookup"><span data-stu-id="0ab65-183">Optional integer attribute.</span></span></p><p><span data-ttu-id="0ab65-184">Doba trvání v sekundách, které modul čeká na spustitelný soubor ke spuštění procesu naslouchání na portu.</span><span class="sxs-lookup"><span data-stu-id="0ab65-184">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="0ab65-185">Pokud je překročen časový limit, modul ukončí proces.</span><span class="sxs-lookup"><span data-stu-id="0ab65-185">If this time limit is exceeded, the module kills the process.</span></span> <span data-ttu-id="0ab65-186">Modul se pokusí znovu spustit proces, když ji dostane novou žádost a pokusit se restartovat proces na dalších příchozích požadavků, pokud se aplikaci nepodaří spustit **rapidFailsPerMinute** málokdy za posledních minuta se zajištěním provozu.</span><span class="sxs-lookup"><span data-stu-id="0ab65-186">The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="0ab65-187">Hodnota 0 (nula) je **ne** za neomezený časový limit.</span><span class="sxs-lookup"><span data-stu-id="0ab65-187">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="0ab65-188">Výchozí hodnota: `120`</span><span class="sxs-lookup"><span data-stu-id="0ab65-188">Default: `120`</span></span><br><span data-ttu-id="0ab65-189">Min: `0`</span><span class="sxs-lookup"><span data-stu-id="0ab65-189">Min: `0`</span></span><br><span data-ttu-id="0ab65-190">Max: `3600`</span><span class="sxs-lookup"><span data-stu-id="0ab65-190">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="0ab65-191">Volitelný logický atribut.</span><span class="sxs-lookup"><span data-stu-id="0ab65-191">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="0ab65-192">Při hodnotě true se **stdout** a **stderr** pro proces určený v **processPath** se přesměrují do souboru zadaného v **stdoutLogFile**.</span><span class="sxs-lookup"><span data-stu-id="0ab65-192">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="0ab65-193">Volitelný atribut řetězce.</span><span class="sxs-lookup"><span data-stu-id="0ab65-193">Optional string attribute.</span></span></p><p><span data-ttu-id="0ab65-194">Určuje relativní nebo absolutní cestu, pro kterou **stdout** a **stderr** z procesu podle **processPath** přihlášeni.</span><span class="sxs-lookup"><span data-stu-id="0ab65-194">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="0ab65-195">Relativní cesty jsou relativní vzhledem k kořen webu.</span><span class="sxs-lookup"><span data-stu-id="0ab65-195">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="0ab65-196">Libovolnou cestu od `.` jsou relativní vzhledem k webu kořenové a všechny ostatní cesty jsou považovány za absolutní cesty.</span><span class="sxs-lookup"><span data-stu-id="0ab65-196">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="0ab65-197">Všechny složky v cestě k dispozici musí existovat v pořadí pro modul pro vytvoření souboru protokolu.</span><span class="sxs-lookup"><span data-stu-id="0ab65-197">Any folders provided in the path must exist in order for the module to create the log file.</span></span> <span data-ttu-id="0ab65-198">Pomocí oddělovače podtržítko, časové razítko, ID procesu a přípona souboru (*.log*) jsou přidány na poslední segment **stdoutLogFile** cestu.</span><span class="sxs-lookup"><span data-stu-id="0ab65-198">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="0ab65-199">Pokud `.\logs\stdout` je zadaný jako hodnota, je uložen protokolu příklad stdout jako *stdout_20180205194132_1934.log* v *protokoly* složky při uložení 5 2. 2018 v 19:41:32 s ID procesu sady 1934.</span><span class="sxs-lookup"><span data-stu-id="0ab65-199">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

::: moniker-end

::: moniker range="= aspnetcore-2.1"

| <span data-ttu-id="0ab65-200">Atribut</span><span class="sxs-lookup"><span data-stu-id="0ab65-200">Attribute</span></span> | <span data-ttu-id="0ab65-201">Popis</span><span class="sxs-lookup"><span data-stu-id="0ab65-201">Description</span></span> | <span data-ttu-id="0ab65-202">Výchozí</span><span class="sxs-lookup"><span data-stu-id="0ab65-202">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="0ab65-203">Volitelný atribut řetězce.</span><span class="sxs-lookup"><span data-stu-id="0ab65-203">Optional string attribute.</span></span></p><p><span data-ttu-id="0ab65-204">Argumenty pro spustitelný soubor určený v **processPath**.</span><span class="sxs-lookup"><span data-stu-id="0ab65-204">Arguments to the executable specified in **processPath**.</span></span></p>| |
| `disableStartUpErrorPage` | <p><span data-ttu-id="0ab65-205">Volitelný logický atribut.</span><span class="sxs-lookup"><span data-stu-id="0ab65-205">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="0ab65-206">Při hodnotě true **502.5 – selhání procesu** Potlačené stránky a znakovou stránku 502 stav nakonfigurované v *web.config* má přednost před.</span><span class="sxs-lookup"><span data-stu-id="0ab65-206">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="0ab65-207">Volitelný logický atribut.</span><span class="sxs-lookup"><span data-stu-id="0ab65-207">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="0ab65-208">Při hodnotě true se token předá podřízený proces naslouchání na ASPNETCORE_PORT % jako záhlaví "MS-ASPNETCORE-WINAUTHTOKEN" každý požadavek.</span><span class="sxs-lookup"><span data-stu-id="0ab65-208">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="0ab65-209">Je odpovědností tento proces pro volání CloseHandle na tento token každý požadavek.</span><span class="sxs-lookup"><span data-stu-id="0ab65-209">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `processesPerApplication` | <p><span data-ttu-id="0ab65-210">Volitelný celočíselný atribut.</span><span class="sxs-lookup"><span data-stu-id="0ab65-210">Optional integer attribute.</span></span></p><p><span data-ttu-id="0ab65-211">Určuje počet instancí procesu zadaný v **processPath** nastavení, které lze se nespředený nahoru na app.</span><span class="sxs-lookup"><span data-stu-id="0ab65-211">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p> | <span data-ttu-id="0ab65-212">Výchozí hodnota: `1`</span><span class="sxs-lookup"><span data-stu-id="0ab65-212">Default: `1`</span></span><br><span data-ttu-id="0ab65-213">Min: `1`</span><span class="sxs-lookup"><span data-stu-id="0ab65-213">Min: `1`</span></span><br><span data-ttu-id="0ab65-214">Max: `100`</span><span class="sxs-lookup"><span data-stu-id="0ab65-214">Max: `100`</span></span> |
| `processPath` | <p><span data-ttu-id="0ab65-215">Požadovaný atribut typu string.</span><span class="sxs-lookup"><span data-stu-id="0ab65-215">Required string attribute.</span></span></p><p><span data-ttu-id="0ab65-216">Cesta ke spustitelnému souboru, který spustí nějaký proces naslouchání požadavků protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="0ab65-216">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="0ab65-217">Jsou podporovány relativní cesty.</span><span class="sxs-lookup"><span data-stu-id="0ab65-217">Relative paths are supported.</span></span> <span data-ttu-id="0ab65-218">Pokud cestu začíná `.`, cesta se považuje za kořeni webu.</span><span class="sxs-lookup"><span data-stu-id="0ab65-218">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="0ab65-219">Volitelný celočíselný atribut.</span><span class="sxs-lookup"><span data-stu-id="0ab65-219">Optional integer attribute.</span></span></p><p><span data-ttu-id="0ab65-220">Určuje, kolikrát podle procesu **processPath** může při selhání za minutu.</span><span class="sxs-lookup"><span data-stu-id="0ab65-220">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="0ab65-221">Pokud je tento limit překročen, modulu zastaví spuštění procesu pro zbytek minuty.</span><span class="sxs-lookup"><span data-stu-id="0ab65-221">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p> | <span data-ttu-id="0ab65-222">Výchozí hodnota: `10`</span><span class="sxs-lookup"><span data-stu-id="0ab65-222">Default: `10`</span></span><br><span data-ttu-id="0ab65-223">Min: `0`</span><span class="sxs-lookup"><span data-stu-id="0ab65-223">Min: `0`</span></span><br><span data-ttu-id="0ab65-224">Max: `100`</span><span class="sxs-lookup"><span data-stu-id="0ab65-224">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="0ab65-225">Atribut volitelný časový interval.</span><span class="sxs-lookup"><span data-stu-id="0ab65-225">Optional timespan attribute.</span></span></p><p><span data-ttu-id="0ab65-226">Určuje dobu, pro kterou modul ASP.NET Core čeká na odpověď z procesu naslouchání na ASPNETCORE_PORT %.</span><span class="sxs-lookup"><span data-stu-id="0ab65-226">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="0ab65-227">Ve verzích modul ASP.NET Core dodávané s verzí technologie ASP.NET Core 2.1 nebo novější `requestTimeout` je zadán v hodiny, minuty a sekundy.</span><span class="sxs-lookup"><span data-stu-id="0ab65-227">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p> | <span data-ttu-id="0ab65-228">Výchozí hodnota: `00:02:00`</span><span class="sxs-lookup"><span data-stu-id="0ab65-228">Default: `00:02:00`</span></span><br><span data-ttu-id="0ab65-229">Min: `00:00:00`</span><span class="sxs-lookup"><span data-stu-id="0ab65-229">Min: `00:00:00`</span></span><br><span data-ttu-id="0ab65-230">Max: `360:00:00`</span><span class="sxs-lookup"><span data-stu-id="0ab65-230">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="0ab65-231">Volitelný celočíselný atribut.</span><span class="sxs-lookup"><span data-stu-id="0ab65-231">Optional integer attribute.</span></span></p><p><span data-ttu-id="0ab65-232">Doba trvání v sekundách, které modul čeká na spustitelný soubor na řádné vypnutí při *app_offline.htm* je detekován soubor.</span><span class="sxs-lookup"><span data-stu-id="0ab65-232">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="0ab65-233">Výchozí hodnota: `10`</span><span class="sxs-lookup"><span data-stu-id="0ab65-233">Default: `10`</span></span><br><span data-ttu-id="0ab65-234">Min: `0`</span><span class="sxs-lookup"><span data-stu-id="0ab65-234">Min: `0`</span></span><br><span data-ttu-id="0ab65-235">Max: `600`</span><span class="sxs-lookup"><span data-stu-id="0ab65-235">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="0ab65-236">Volitelný celočíselný atribut.</span><span class="sxs-lookup"><span data-stu-id="0ab65-236">Optional integer attribute.</span></span></p><p><span data-ttu-id="0ab65-237">Doba trvání v sekundách, které modul čeká na spustitelný soubor ke spuštění procesu naslouchání na portu.</span><span class="sxs-lookup"><span data-stu-id="0ab65-237">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="0ab65-238">Pokud je překročen časový limit, modul ukončí proces.</span><span class="sxs-lookup"><span data-stu-id="0ab65-238">If this time limit is exceeded, the module kills the process.</span></span> <span data-ttu-id="0ab65-239">Modul se pokusí znovu spustit proces, když ji dostane novou žádost a pokusit se restartovat proces na dalších příchozích požadavků, pokud se aplikaci nepodaří spustit **rapidFailsPerMinute** málokdy za posledních minuta se zajištěním provozu.</span><span class="sxs-lookup"><span data-stu-id="0ab65-239">The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="0ab65-240">Hodnota 0 (nula) je **ne** za neomezený časový limit.</span><span class="sxs-lookup"><span data-stu-id="0ab65-240">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="0ab65-241">Výchozí hodnota: `120`</span><span class="sxs-lookup"><span data-stu-id="0ab65-241">Default: `120`</span></span><br><span data-ttu-id="0ab65-242">Min: `0`</span><span class="sxs-lookup"><span data-stu-id="0ab65-242">Min: `0`</span></span><br><span data-ttu-id="0ab65-243">Max: `3600`</span><span class="sxs-lookup"><span data-stu-id="0ab65-243">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="0ab65-244">Volitelný logický atribut.</span><span class="sxs-lookup"><span data-stu-id="0ab65-244">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="0ab65-245">Při hodnotě true se **stdout** a **stderr** pro proces určený v **processPath** se přesměrují do souboru zadaného v **stdoutLogFile**.</span><span class="sxs-lookup"><span data-stu-id="0ab65-245">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="0ab65-246">Volitelný atribut řetězce.</span><span class="sxs-lookup"><span data-stu-id="0ab65-246">Optional string attribute.</span></span></p><p><span data-ttu-id="0ab65-247">Určuje relativní nebo absolutní cestu, pro kterou **stdout** a **stderr** z procesu podle **processPath** přihlášeni.</span><span class="sxs-lookup"><span data-stu-id="0ab65-247">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="0ab65-248">Relativní cesty jsou relativní vzhledem k kořen webu.</span><span class="sxs-lookup"><span data-stu-id="0ab65-248">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="0ab65-249">Libovolnou cestu od `.` jsou relativní vzhledem k webu kořenové a všechny ostatní cesty jsou považovány za absolutní cesty.</span><span class="sxs-lookup"><span data-stu-id="0ab65-249">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="0ab65-250">Všechny složky v cestě k dispozici musí existovat v pořadí pro modul pro vytvoření souboru protokolu.</span><span class="sxs-lookup"><span data-stu-id="0ab65-250">Any folders provided in the path must exist in order for the module to create the log file.</span></span> <span data-ttu-id="0ab65-251">Pomocí oddělovače podtržítko, časové razítko, ID procesu a přípona souboru (*.log*) jsou přidány na poslední segment **stdoutLogFile** cestu.</span><span class="sxs-lookup"><span data-stu-id="0ab65-251">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="0ab65-252">Pokud `.\logs\stdout` je zadaný jako hodnota, je uložen protokolu příklad stdout jako *stdout_20180205194132_1934.log* v *protokoly* složky při uložení 5 2. 2018 v 19:41:32 s ID procesu sady 1934.</span><span class="sxs-lookup"><span data-stu-id="0ab65-252">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

| <span data-ttu-id="0ab65-253">Atribut</span><span class="sxs-lookup"><span data-stu-id="0ab65-253">Attribute</span></span> | <span data-ttu-id="0ab65-254">Popis</span><span class="sxs-lookup"><span data-stu-id="0ab65-254">Description</span></span> | <span data-ttu-id="0ab65-255">Výchozí</span><span class="sxs-lookup"><span data-stu-id="0ab65-255">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="0ab65-256">Volitelný atribut řetězce.</span><span class="sxs-lookup"><span data-stu-id="0ab65-256">Optional string attribute.</span></span></p><p><span data-ttu-id="0ab65-257">Argumenty pro spustitelný soubor určený v **processPath**.</span><span class="sxs-lookup"><span data-stu-id="0ab65-257">Arguments to the executable specified in **processPath**.</span></span></p>| |
| `disableStartUpErrorPage` | <p><span data-ttu-id="0ab65-258">Volitelný logický atribut.</span><span class="sxs-lookup"><span data-stu-id="0ab65-258">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="0ab65-259">Při hodnotě true **502.5 – selhání procesu** Potlačené stránky a znakovou stránku 502 stav nakonfigurované v *web.config* má přednost před.</span><span class="sxs-lookup"><span data-stu-id="0ab65-259">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="0ab65-260">Volitelný logický atribut.</span><span class="sxs-lookup"><span data-stu-id="0ab65-260">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="0ab65-261">Při hodnotě true se token předá podřízený proces naslouchání na ASPNETCORE_PORT % jako záhlaví "MS-ASPNETCORE-WINAUTHTOKEN" každý požadavek.</span><span class="sxs-lookup"><span data-stu-id="0ab65-261">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="0ab65-262">Je odpovědností tento proces pro volání CloseHandle na tento token každý požadavek.</span><span class="sxs-lookup"><span data-stu-id="0ab65-262">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `processesPerApplication` | <p><span data-ttu-id="0ab65-263">Volitelný celočíselný atribut.</span><span class="sxs-lookup"><span data-stu-id="0ab65-263">Optional integer attribute.</span></span></p><p><span data-ttu-id="0ab65-264">Určuje počet instancí procesu zadaný v **processPath** nastavení, které lze se nespředený nahoru na app.</span><span class="sxs-lookup"><span data-stu-id="0ab65-264">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p> | <span data-ttu-id="0ab65-265">Výchozí hodnota: `1`</span><span class="sxs-lookup"><span data-stu-id="0ab65-265">Default: `1`</span></span><br><span data-ttu-id="0ab65-266">Min: `1`</span><span class="sxs-lookup"><span data-stu-id="0ab65-266">Min: `1`</span></span><br><span data-ttu-id="0ab65-267">Max: `100`</span><span class="sxs-lookup"><span data-stu-id="0ab65-267">Max: `100`</span></span> |
| `processPath` | <p><span data-ttu-id="0ab65-268">Požadovaný atribut typu string.</span><span class="sxs-lookup"><span data-stu-id="0ab65-268">Required string attribute.</span></span></p><p><span data-ttu-id="0ab65-269">Cesta ke spustitelnému souboru, který spustí nějaký proces naslouchání požadavků protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="0ab65-269">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="0ab65-270">Jsou podporovány relativní cesty.</span><span class="sxs-lookup"><span data-stu-id="0ab65-270">Relative paths are supported.</span></span> <span data-ttu-id="0ab65-271">Pokud cestu začíná `.`, cesta se považuje za kořeni webu.</span><span class="sxs-lookup"><span data-stu-id="0ab65-271">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="0ab65-272">Volitelný celočíselný atribut.</span><span class="sxs-lookup"><span data-stu-id="0ab65-272">Optional integer attribute.</span></span></p><p><span data-ttu-id="0ab65-273">Určuje, kolikrát podle procesu **processPath** může při selhání za minutu.</span><span class="sxs-lookup"><span data-stu-id="0ab65-273">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="0ab65-274">Pokud je tento limit překročen, modulu zastaví spuštění procesu pro zbytek minuty.</span><span class="sxs-lookup"><span data-stu-id="0ab65-274">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p> | <span data-ttu-id="0ab65-275">Výchozí hodnota: `10`</span><span class="sxs-lookup"><span data-stu-id="0ab65-275">Default: `10`</span></span><br><span data-ttu-id="0ab65-276">Min: `0`</span><span class="sxs-lookup"><span data-stu-id="0ab65-276">Min: `0`</span></span><br><span data-ttu-id="0ab65-277">Max: `100`</span><span class="sxs-lookup"><span data-stu-id="0ab65-277">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="0ab65-278">Atribut volitelný časový interval.</span><span class="sxs-lookup"><span data-stu-id="0ab65-278">Optional timespan attribute.</span></span></p><p><span data-ttu-id="0ab65-279">Určuje dobu, pro kterou modul ASP.NET Core čeká na odpověď z procesu naslouchání na ASPNETCORE_PORT %.</span><span class="sxs-lookup"><span data-stu-id="0ab65-279">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="0ab65-280">Ve verzích modul ASP.NET Core dodávané s verzí technologie ASP.NET Core 2.0 nebo starší `requestTimeout` musí být zadán v celých minutách, v opačném případě výchozí hodnota 2 minuty.</span><span class="sxs-lookup"><span data-stu-id="0ab65-280">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.0 or earlier, the `requestTimeout` must be specified in whole minutes only, otherwise it defaults to 2 minutes.</span></span></p> | <span data-ttu-id="0ab65-281">Výchozí hodnota: `00:02:00`</span><span class="sxs-lookup"><span data-stu-id="0ab65-281">Default: `00:02:00`</span></span><br><span data-ttu-id="0ab65-282">Min: `00:00:00`</span><span class="sxs-lookup"><span data-stu-id="0ab65-282">Min: `00:00:00`</span></span><br><span data-ttu-id="0ab65-283">Max: `360:00:00`</span><span class="sxs-lookup"><span data-stu-id="0ab65-283">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="0ab65-284">Volitelný celočíselný atribut.</span><span class="sxs-lookup"><span data-stu-id="0ab65-284">Optional integer attribute.</span></span></p><p><span data-ttu-id="0ab65-285">Doba trvání v sekundách, které modul čeká na spustitelný soubor na řádné vypnutí při *app_offline.htm* je detekován soubor.</span><span class="sxs-lookup"><span data-stu-id="0ab65-285">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="0ab65-286">Výchozí hodnota: `10`</span><span class="sxs-lookup"><span data-stu-id="0ab65-286">Default: `10`</span></span><br><span data-ttu-id="0ab65-287">Min: `0`</span><span class="sxs-lookup"><span data-stu-id="0ab65-287">Min: `0`</span></span><br><span data-ttu-id="0ab65-288">Max: `600`</span><span class="sxs-lookup"><span data-stu-id="0ab65-288">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="0ab65-289">Volitelný celočíselný atribut.</span><span class="sxs-lookup"><span data-stu-id="0ab65-289">Optional integer attribute.</span></span></p><p><span data-ttu-id="0ab65-290">Doba trvání v sekundách, které modul čeká na spustitelný soubor ke spuštění procesu naslouchání na portu.</span><span class="sxs-lookup"><span data-stu-id="0ab65-290">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="0ab65-291">Pokud je překročen časový limit, modul ukončí proces.</span><span class="sxs-lookup"><span data-stu-id="0ab65-291">If this time limit is exceeded, the module kills the process.</span></span> <span data-ttu-id="0ab65-292">Modul se pokusí znovu spustit proces, když ji dostane novou žádost a pokusit se restartovat proces na dalších příchozích požadavků, pokud se aplikaci nepodaří spustit **rapidFailsPerMinute** málokdy za posledních minuta se zajištěním provozu.</span><span class="sxs-lookup"><span data-stu-id="0ab65-292">The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p> | <span data-ttu-id="0ab65-293">Výchozí hodnota: `120`</span><span class="sxs-lookup"><span data-stu-id="0ab65-293">Default: `120`</span></span><br><span data-ttu-id="0ab65-294">Min: `0`</span><span class="sxs-lookup"><span data-stu-id="0ab65-294">Min: `0`</span></span><br><span data-ttu-id="0ab65-295">Max: `3600`</span><span class="sxs-lookup"><span data-stu-id="0ab65-295">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="0ab65-296">Volitelný logický atribut.</span><span class="sxs-lookup"><span data-stu-id="0ab65-296">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="0ab65-297">Při hodnotě true se **stdout** a **stderr** pro proces určený v **processPath** se přesměrují do souboru zadaného v **stdoutLogFile**.</span><span class="sxs-lookup"><span data-stu-id="0ab65-297">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="0ab65-298">Volitelný atribut řetězce.</span><span class="sxs-lookup"><span data-stu-id="0ab65-298">Optional string attribute.</span></span></p><p><span data-ttu-id="0ab65-299">Určuje relativní nebo absolutní cestu, pro kterou **stdout** a **stderr** z procesu podle **processPath** přihlášeni.</span><span class="sxs-lookup"><span data-stu-id="0ab65-299">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="0ab65-300">Relativní cesty jsou relativní vzhledem k kořen webu.</span><span class="sxs-lookup"><span data-stu-id="0ab65-300">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="0ab65-301">Libovolnou cestu od `.` jsou relativní vzhledem k webu kořenové a všechny ostatní cesty jsou považovány za absolutní cesty.</span><span class="sxs-lookup"><span data-stu-id="0ab65-301">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="0ab65-302">Všechny složky v cestě k dispozici musí existovat v pořadí pro modul pro vytvoření souboru protokolu.</span><span class="sxs-lookup"><span data-stu-id="0ab65-302">Any folders provided in the path must exist in order for the module to create the log file.</span></span> <span data-ttu-id="0ab65-303">Pomocí oddělovače podtržítko, časové razítko, ID procesu a přípona souboru (*.log*) jsou přidány na poslední segment **stdoutLogFile** cestu.</span><span class="sxs-lookup"><span data-stu-id="0ab65-303">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="0ab65-304">Pokud `.\logs\stdout` je zadaný jako hodnota, je uložen protokolu příklad stdout jako *stdout_20180205194132_1934.log* v *protokoly* složky při uložení 5 2. 2018 v 19:41:32 s ID procesu sady 1934.</span><span class="sxs-lookup"><span data-stu-id="0ab65-304">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

::: moniker-end

### <a name="setting-environment-variables"></a><span data-ttu-id="0ab65-305">Nastavení proměnných prostředí</span><span class="sxs-lookup"><span data-stu-id="0ab65-305">Setting environment variables</span></span>

<span data-ttu-id="0ab65-306">Proměnné prostředí se dá nastavit pro proces v `processPath` atribut.</span><span class="sxs-lookup"><span data-stu-id="0ab65-306">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="0ab65-307">Zadat proměnné prostředí s `environmentVariable` podřízený prvek `environmentVariables` prvek kolekce.</span><span class="sxs-lookup"><span data-stu-id="0ab65-307">Specify an environment variable with the `environmentVariable` child element of an `environmentVariables` collection element.</span></span> <span data-ttu-id="0ab65-308">Proměnné prostředí nastavené v této části přednost systémové proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="0ab65-308">Environment variables set in this section take precedence over system environment variables.</span></span>

<span data-ttu-id="0ab65-309">Následující příklad nastaví dvou proměnných prostředí.</span><span class="sxs-lookup"><span data-stu-id="0ab65-309">The following example sets two environment variables.</span></span> <span data-ttu-id="0ab65-310">`ASPNETCORE_ENVIRONMENT` nakonfiguruje prostředí aplikace tak, aby `Development`.</span><span class="sxs-lookup"><span data-stu-id="0ab65-310">`ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="0ab65-311">Vývojáři mohou dočasně nastaví tuto hodnotu *web.config* souboru, aby bylo možné vynutit [stránku výjimek pro vývojáře](xref:fundamentals/error-handling) načíst při ladění aplikace výjimky.</span><span class="sxs-lookup"><span data-stu-id="0ab65-311">A developer may temporarily set this value in the *web.config* file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="0ab65-312">`CONFIG_DIR` je příkladem proměnné prostředí, kam má vývojář zapisovat kód, který čte hodnoty při spuštění tvoří cestu pro načtení konfiguračního souboru aplikace.</span><span class="sxs-lookup"><span data-stu-id="0ab65-312">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

::: moniker range=">= aspnetcore-2.2"

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile="\\?\%home%\LogFiles\stdout"
      hostingModel="inprocess">
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
> <span data-ttu-id="0ab65-313">Nastavit pouze `ASPNETCORE_ENVIRONMENT` proměnnou prostředí, aby `Development` na přípravy a testování serverů, které nejsou dostupné k nedůvěryhodným sítím, jako je Internet.</span><span class="sxs-lookup"><span data-stu-id="0ab65-313">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## <a name="appofflinehtm"></a><span data-ttu-id="0ab65-314">app_offline.htm</span><span class="sxs-lookup"><span data-stu-id="0ab65-314">app_offline.htm</span></span>

<span data-ttu-id="0ab65-315">Pokud soubor s názvem *app_offline.htm* je zjištěna v kořenovém adresáři aplikace, že modul ASP.NET Core se pokusí řádné vypnutí aplikace a zastavit zpracování příchozích požadavků.</span><span class="sxs-lookup"><span data-stu-id="0ab65-315">If a file with the name *app_offline.htm* is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="0ab65-316">Pokud aplikace pořád běží po dobu v sekundách podle `shutdownTimeLimit`, že modul ASP.NET Core ukončuje spuštěnému procesu.</span><span class="sxs-lookup"><span data-stu-id="0ab65-316">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="0ab65-317">Zatímco *app_offline.htm* soubor je k dispozici, že modul ASP.NET Core reaguje na požadavky odesílá zpět obsah *app_offline.htm* souboru.</span><span class="sxs-lookup"><span data-stu-id="0ab65-317">While the *app_offline.htm* file is present, the ASP.NET Core Module responds to requests by sending back the contents of the *app_offline.htm* file.</span></span> <span data-ttu-id="0ab65-318">Když *app_offline.htm* soubor bude odstraněn, příští žádosti o spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="0ab65-318">When the *app_offline.htm* file is removed, the next request starts the app.</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="0ab65-319">Pokud používáte model hostingu mimo proces, aplikace nemusí vypnout okamžitě při otevření připojení.</span><span class="sxs-lookup"><span data-stu-id="0ab65-319">When using the out-of-process hosting model, the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="0ab65-320">Připojení soketu websocket bylo třeba může způsobit prodlevu při ukončení aplikace.</span><span class="sxs-lookup"><span data-stu-id="0ab65-320">For example, a websocket connection may delay app shut down.</span></span>

::: moniker-end

## <a name="start-up-error-page"></a><span data-ttu-id="0ab65-321">Spuštění chybovou stránku</span><span class="sxs-lookup"><span data-stu-id="0ab65-321">Start-up error page</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="0ab65-322">Hostování v procesu a mimo proces vytvoření vlastní chybové stránky, když selžou a spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="0ab65-322">Both in-process and out-of-process hosting produce custom error pages when they fail to start the app.</span></span>

<span data-ttu-id="0ab65-323">Pokud se nepodaří najít buď nebo na více instancí procesu žádosti o obslužnou rutinu, že modul ASP.NET Core *500.0 – Chyba načtení obslužné rutiny v procesu/Out – proces* se zobrazí stav znakovou stránku.</span><span class="sxs-lookup"><span data-stu-id="0ab65-323">If the ASP.NET Core Module fails to find either the in-process or out-of-process request handler, a *500.0 - In-Process/Out-Of-Process Handler Load Failure* status code page appears.</span></span>

<span data-ttu-id="0ab65-324">Pro hostování v procesu, pokud se nepodaří spustit aplikaci, že modul ASP.NET Core *500.30 - Start selhání* se zobrazí stav znakovou stránku.</span><span class="sxs-lookup"><span data-stu-id="0ab65-324">For in-process hosting if the ASP.NET Core Module fails to start the app, a *500.30 - Start Failure* status code page appears.</span></span>

<span data-ttu-id="0ab65-325">Pro hostování mimo proces, pokud se nespustí back-endový proces nebo back-endový proces spustí ale nebude moci poslouchat na konfigurovaném portu, že modul ASP.NET Core *502.5 – selhání procesu* se zobrazí stav znakovou stránku.</span><span class="sxs-lookup"><span data-stu-id="0ab65-325">For out-of-process hosting if the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span>

<span data-ttu-id="0ab65-326">Chcete-li potlačit tuto stránku a vrátit se na výchozí služby IIS 5xx stav znakovou stránku, použijte `disableStartUpErrorPage` atribut.</span><span class="sxs-lookup"><span data-stu-id="0ab65-326">To suppress this page and revert to the default IIS 5xx status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="0ab65-327">Další informace o konfiguraci vlastních chybových zpráv, najdete v části [chyby protokolu HTTP \<httpErrors >](/iis/configuration/system.webServer/httpErrors/).</span><span class="sxs-lookup"><span data-stu-id="0ab65-327">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="0ab65-328">Pokud se nespustí back-endový proces nebo back-endový proces spustí ale nebude moci poslouchat na konfigurovaném portu, že modul ASP.NET Core *502.5 – selhání procesu* se zobrazí stav znakovou stránku.</span><span class="sxs-lookup"><span data-stu-id="0ab65-328">If the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span> <span data-ttu-id="0ab65-329">Chcete-li potlačit tuto stránku a vrátit se na výchozí stav služby IIS 502 znakovou stránku, použijte `disableStartUpErrorPage` atribut.</span><span class="sxs-lookup"><span data-stu-id="0ab65-329">To suppress this page and revert to the default IIS 502 status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="0ab65-330">Další informace o konfiguraci vlastních chybových zpráv, najdete v části [chyby protokolu HTTP \<httpErrors >](/iis/configuration/system.webServer/httpErrors/).</span><span class="sxs-lookup"><span data-stu-id="0ab65-330">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

![502.5 proces selhání stav znaková stránka](aspnet-core-module/_static/ANCM-502_5.png)

::: moniker-end

## <a name="log-creation-and-redirection"></a><span data-ttu-id="0ab65-332">Vytvoření protokolu a přesměrování</span><span class="sxs-lookup"><span data-stu-id="0ab65-332">Log creation and redirection</span></span>

<span data-ttu-id="0ab65-333">Modul ASP.NET Core přesměruje výstup stdout a stderr console na disk, pokud `stdoutLogEnabled` a `stdoutLogFile` atributy `aspNetCore` element jsou nastavené.</span><span class="sxs-lookup"><span data-stu-id="0ab65-333">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="0ab65-334">Všechny složky v `stdoutLogFile` cesta musí existovat v pořadí pro modul pro vytvoření souboru protokolu.</span><span class="sxs-lookup"><span data-stu-id="0ab65-334">Any folders in the `stdoutLogFile` path must exist in order for the module to create the log file.</span></span> <span data-ttu-id="0ab65-335">Fond aplikací musí mít oprávnění k zápisu do umístění, ve kterém jsou zapsány protokoly (použijte `IIS AppPool\<app_pool_name>` poskytnout oprávnění k zápisu).</span><span class="sxs-lookup"><span data-stu-id="0ab65-335">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="0ab65-336">Protokoly nejsou střídán, pokud dojde k recyklování procesů/restartování.</span><span class="sxs-lookup"><span data-stu-id="0ab65-336">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="0ab65-337">Je odpovědností hostitel k omezení místa na disku, které využijete v protokolech.</span><span class="sxs-lookup"><span data-stu-id="0ab65-337">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="0ab65-338">Použití protokolu stdout se doporučuje jenom pro řešení potíží při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="0ab65-338">Using the stdout log is only recommended for troubleshooting app startup issues.</span></span> <span data-ttu-id="0ab65-339">Nepoužívejte stdout protokolu pro účely protokolování obecné aplikace.</span><span class="sxs-lookup"><span data-stu-id="0ab65-339">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="0ab65-340">Pro rutiny protokolování v aplikaci ASP.NET Core, použijte protokolování knihovnu, která omezuje velikost souboru protokolu a otočí protokoly.</span><span class="sxs-lookup"><span data-stu-id="0ab65-340">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="0ab65-341">Další informace najdete v tématu [zprostředkovatele přihlášení třetí strany](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="0ab65-341">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="0ab65-342">Časové razítko a soubor rozšíření jsou přidány automaticky při vytvoření souboru protokolu.</span><span class="sxs-lookup"><span data-stu-id="0ab65-342">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="0ab65-343">Název souboru protokolu se skládá připojením časové razítko, ID procesu a přípona souboru (*.log*) na poslední segment `stdoutLogFile` cestu (obvykle *stdout*) oddělené podtržítka.</span><span class="sxs-lookup"><span data-stu-id="0ab65-343">The log file name is composed by appending the timestamp, process ID, and file extension (*.log*) to the last segment of the `stdoutLogFile` path (typically *stdout*) delimited by underscores.</span></span> <span data-ttu-id="0ab65-344">Pokud `stdoutLogFile` cesta končí *stdout*, má název souboru protokolu pro aplikaci s PID 1934 vytvořili 5 2. 2018 v 19:42:32 *stdout_20180205194132_1934.log*.</span><span class="sxs-lookup"><span data-stu-id="0ab65-344">If the `stdoutLogFile` path ends with *stdout*, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name *stdout_20180205194132_1934.log*.</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="0ab65-345">Pokud `stdoutLogEnabled` má hodnotu false, chyby, ke kterým dochází při spuštění aplikace se zachytí a do protokolu událostí, protože ho až 30 KB.</span><span class="sxs-lookup"><span data-stu-id="0ab65-345">If `stdoutLogEnabled` is false, errors that occur on app startup are captured and emitted to the event log up to 30 KB.</span></span> <span data-ttu-id="0ab65-346">Po spuštění se zahodí všechny další protokoly.</span><span class="sxs-lookup"><span data-stu-id="0ab65-346">After startup, all additional logs are discarded.</span></span>

::: moniker-end

<span data-ttu-id="0ab65-347">Následující ukázka `aspNetCore` element konfiguruje stdout protokolování pro aplikace hostované ve službě Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="0ab65-347">The following sample `aspNetCore` element configures stdout logging for an app hosted in Azure App Service.</span></span> <span data-ttu-id="0ab65-348">Místní cesta nebo cesta ke sdílené položce je přijatelné pro místní přihlášení.</span><span class="sxs-lookup"><span data-stu-id="0ab65-348">A local path or network share path is acceptable for local logging.</span></span> <span data-ttu-id="0ab65-349">Ověřte, jestli uživatel identita fondu aplikací má oprávnění k zápisu do zadaná výstupní cesta.</span><span class="sxs-lookup"><span data-stu-id="0ab65-349">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

::: moniker range=">= aspnetcore-2.2"

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="inprocess">
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

## <a name="enhanced-diagnostic-logs"></a><span data-ttu-id="0ab65-350">Rozšířené diagnostické protokoly</span><span class="sxs-lookup"><span data-stu-id="0ab65-350">Enhanced diagnostic logs</span></span>

<span data-ttu-id="0ab65-351">Poskytuje modul ASP.NET Core je konfigurovat, a poskytují rozšířené diagnostické protokoly.</span><span class="sxs-lookup"><span data-stu-id="0ab65-351">The ASP.NET Core Module provides is configurable to provide enhanced diagnostics logs.</span></span> <span data-ttu-id="0ab65-352">Přidat `<handlerSettings>` elementu `<aspNetCore>` prvek *web.config*. Nastavení `debugLevel` k `TRACE` zpřístupňuje větší věrnost diagnostické informace:</span><span class="sxs-lookup"><span data-stu-id="0ab65-352">Add the `<handlerSettings>` element to the `<aspNetCore>` element in *web.config*. Setting the `debugLevel` to `TRACE` exposes a higher fidelity of diagnostic information:</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="inprocess">
  <handlerSettings>
    <handlerSetting name="debugFile" value="aspnetcore-debug.log" />
    <handlerSetting name="debugLevel" value="FILE,TRACE" />
  </handlerSettings>
</aspNetCore>
```

<span data-ttu-id="0ab65-353">Ladit úrovně (`debugLevel`) hodnoty může obsahovat úroveň a umístění.</span><span class="sxs-lookup"><span data-stu-id="0ab65-353">Debug level (`debugLevel`) values can include both the level and the location.</span></span>

<span data-ttu-id="0ab65-354">Úrovně (v pořadí od nejméně na nejpodrobnější):</span><span class="sxs-lookup"><span data-stu-id="0ab65-354">Levels (in order from least to most verbose):</span></span>

* <span data-ttu-id="0ab65-355">CHYBA</span><span class="sxs-lookup"><span data-stu-id="0ab65-355">ERROR</span></span>
* <span data-ttu-id="0ab65-356">UPOZORNĚNÍ</span><span class="sxs-lookup"><span data-stu-id="0ab65-356">WARNING</span></span>
* <span data-ttu-id="0ab65-357">INFORMACE O</span><span class="sxs-lookup"><span data-stu-id="0ab65-357">INFO</span></span>
* <span data-ttu-id="0ab65-358">TRACE</span><span class="sxs-lookup"><span data-stu-id="0ab65-358">TRACE</span></span>

<span data-ttu-id="0ab65-359">Umístění (umístění více jsou povoleny):</span><span class="sxs-lookup"><span data-stu-id="0ab65-359">Locations (multiple locations are permitted):</span></span>

* <span data-ttu-id="0ab65-360">KONZOLY</span><span class="sxs-lookup"><span data-stu-id="0ab65-360">CONSOLE</span></span>
* <span data-ttu-id="0ab65-361">PROTOKOL UDÁLOSTÍ</span><span class="sxs-lookup"><span data-stu-id="0ab65-361">EVENTLOG</span></span>
* <span data-ttu-id="0ab65-362">SOUBOR</span><span class="sxs-lookup"><span data-stu-id="0ab65-362">FILE</span></span>

<span data-ttu-id="0ab65-363">Obslužná rutina nastavení se dá zadat i prostřednictvím proměnné prostředí:</span><span class="sxs-lookup"><span data-stu-id="0ab65-363">The handler settings can also be provided via environment variables:</span></span>

* <span data-ttu-id="0ab65-364">`ASPNETCORE_MODULE_DEBUG_FILE` &ndash; Cesta k souboru protokolu ladění.</span><span class="sxs-lookup"><span data-stu-id="0ab65-364">`ASPNETCORE_MODULE_DEBUG_FILE` &ndash; Path to the debug log file.</span></span> <span data-ttu-id="0ab65-365">(Výchozí: *aspnetcore-debug.log*)</span><span class="sxs-lookup"><span data-stu-id="0ab65-365">(Default: *aspnetcore-debug.log*)</span></span>
* <span data-ttu-id="0ab65-366">`ASPNETCORE_MODULE_DEBUG` &ndash; Ladit nastavení úrovně.</span><span class="sxs-lookup"><span data-stu-id="0ab65-366">`ASPNETCORE_MODULE_DEBUG` &ndash; Debug level setting.</span></span>

> [!WARNING]
> <span data-ttu-id="0ab65-367">Proveďte **není** protokolování ladění povoleno v nasazení pro déle, než se požaduje chcete vyřešit nějaký problém.</span><span class="sxs-lookup"><span data-stu-id="0ab65-367">Do **not** leave debug logging enabled in the deployment for longer than required to troubleshoot an issue.</span></span> <span data-ttu-id="0ab65-368">Velikost souboru protokolu není omezený.</span><span class="sxs-lookup"><span data-stu-id="0ab65-368">The size of the log isn't limited.</span></span> <span data-ttu-id="0ab65-369">Opuštění protokol ladění povoleno může vyčerpat dostupné místo na disku a chybách u aplikací na serveru nebo služby app service.</span><span class="sxs-lookup"><span data-stu-id="0ab65-369">Leaving the debug log enabled can exhaust the available disk space and crash the server or app service.</span></span>

::: moniker-end

<span data-ttu-id="0ab65-370">Naleznete v tématu [konfigurace pomocí souboru web.config](#configuration-with-webconfig) příklad `aspNetCore` prvek *web.config* souboru.</span><span class="sxs-lookup"><span data-stu-id="0ab65-370">See [Configuration with web.config](#configuration-with-webconfig) for an example of the `aspNetCore` element in the *web.config* file.</span></span>

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="0ab65-371">Konfigurace proxy serveru používá protokol HTTP a token pro párování</span><span class="sxs-lookup"><span data-stu-id="0ab65-371">Proxy configuration uses HTTP protocol and a pairing token</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="0ab65-372">*Platí jenom pro hostování mimo proces.*</span><span class="sxs-lookup"><span data-stu-id="0ab65-372">*Only applies to out-of-process hosting.*</span></span>

::: moniker-end

<span data-ttu-id="0ab65-373">Proxy server mezi modul ASP.NET Core a Kestrel používá protokol HTTP.</span><span class="sxs-lookup"><span data-stu-id="0ab65-373">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="0ab65-374">Pomocí protokolu HTTP je optimalizace výkonu, kdy přenos dat mezi modulu a Kestrel probíhá na adresu zpětné smyčky ze síťového rozhraní.</span><span class="sxs-lookup"><span data-stu-id="0ab65-374">Using HTTP is a performance optimization, where the traffic between the module and Kestrel takes place on a loopback address off of the network interface.</span></span> <span data-ttu-id="0ab65-375">Neexistuje žádné riziko odposloucháváním provozu mezi modulu a Kestrel z umístění mimo server.</span><span class="sxs-lookup"><span data-stu-id="0ab65-375">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="0ab65-376">Párování token se používá k zajištění, že požadavků přijatých službou Kestrel byly směrovány přes proxy server službou IIS a nebyl dodán z nějakého jiného zdroje.</span><span class="sxs-lookup"><span data-stu-id="0ab65-376">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="0ab65-377">Vytvoření a nastavení do proměnné prostředí párování token (`ASPNETCORE_TOKEN`) modulu.</span><span class="sxs-lookup"><span data-stu-id="0ab65-377">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="0ab65-378">Párování token byl nastavený i do záhlaví (`MSAspNetCoreToken`) na všechny požadavky směrovány přes proxy server.</span><span class="sxs-lookup"><span data-stu-id="0ab65-378">The pairing token is also set into a header (`MSAspNetCoreToken`) on every proxied request.</span></span> <span data-ttu-id="0ab65-379">Služba IIS Middleware kontroly požadavku že přijme potvrďte, že odpovídá párování hodnota tokenu hlavičky hodnotu proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="0ab65-379">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="0ab65-380">Pokud token hodnoty se neshodují, žádost je zaznamenána a odmítnut.</span><span class="sxs-lookup"><span data-stu-id="0ab65-380">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="0ab65-381">Párování proměnná tokenu prostředí a přenos dat mezi modulu a Kestrel nejsou dostupné z umístění mimo server.</span><span class="sxs-lookup"><span data-stu-id="0ab65-381">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="0ab65-382">Nainstalovat bez mého párování hodnota tokenu nelze útočník odesílat požadavky, které obejít kontrolu v IIS middlewaru.</span><span class="sxs-lookup"><span data-stu-id="0ab65-382">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="0ab65-383">Modul ASP.NET Core s službu IIS sdílenou konfiguraci</span><span class="sxs-lookup"><span data-stu-id="0ab65-383">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="0ab65-384">Modul ASP.NET Core instalační program spustí s oprávněními **systému** účtu.</span><span class="sxs-lookup"><span data-stu-id="0ab65-384">The ASP.NET Core Module installer runs with the privileges of the **SYSTEM** account.</span></span> <span data-ttu-id="0ab65-385">Vzhledem k tomu, že místní systémový účet mít nezmění oprávnění pro sdílenou složku cesta používaná systémem sdílené konfiguraci IIS, instalační program narazí na chybu při pokusu o konfiguraci nastavení modulu v odepření přístupu *applicationHost.config* ve sdílené složce.</span><span class="sxs-lookup"><span data-stu-id="0ab65-385">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer hits an access denied error when attempting to configure the module settings in *applicationHost.config* on the share.</span></span> <span data-ttu-id="0ab65-386">Pokud používáte sdílenou konfiguraci IIS, postupujte podle těchto kroků:</span><span class="sxs-lookup"><span data-stu-id="0ab65-386">When using an IIS Shared Configuration, follow these steps:</span></span>

1. <span data-ttu-id="0ab65-387">Zakážete sdílenou konfiguraci IIS.</span><span class="sxs-lookup"><span data-stu-id="0ab65-387">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="0ab65-388">Spusťte instalační program.</span><span class="sxs-lookup"><span data-stu-id="0ab65-388">Run the installer.</span></span>
1. <span data-ttu-id="0ab65-389">Export aktualizovaný *applicationHost.config* souboru do sdílené složky.</span><span class="sxs-lookup"><span data-stu-id="0ab65-389">Export the updated *applicationHost.config* file to the share.</span></span>
1. <span data-ttu-id="0ab65-390">Znovu povolte sdílenou konfiguraci IIS.</span><span class="sxs-lookup"><span data-stu-id="0ab65-390">Re-enable the IIS Shared Configuration.</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="0ab65-391">Verze modulu a hostování sady Instalační protokoly</span><span class="sxs-lookup"><span data-stu-id="0ab65-391">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="0ab65-392">Chcete-li zjistit verzi nainstalovaný modul ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="0ab65-392">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="0ab65-393">V hostitelském systému, přejděte na *%windir%\System32\inetsrv*.</span><span class="sxs-lookup"><span data-stu-id="0ab65-393">On the hosting system, navigate to *%windir%\System32\inetsrv*.</span></span>
1. <span data-ttu-id="0ab65-394">Vyhledejte *aspnetcore.dll* souboru.</span><span class="sxs-lookup"><span data-stu-id="0ab65-394">Locate the *aspnetcore.dll* file.</span></span>
1. <span data-ttu-id="0ab65-395">Klikněte pravým tlačítkem na soubor a vyberte **vlastnosti** z kontextové nabídky.</span><span class="sxs-lookup"><span data-stu-id="0ab65-395">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="0ab65-396">Vyberte **podrobnosti** kartu. **Verze souboru** a **verze produktu** představují nainstalovaná verze modulu.</span><span class="sxs-lookup"><span data-stu-id="0ab65-396">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="0ab65-397">Instalační protokoly hostování sady prostředků modulu se nacházejí v *C:\\uživatelé\\% UserName %\\AppData\\místní\\Temp*. Soubor *dd_DotNetCoreWinSvrHosting__\<časové razítko > _000_AspNetCoreModule_x64.log*.</span><span class="sxs-lookup"><span data-stu-id="0ab65-397">The Hosting Bundle installer logs for the module are found at *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. The file is named *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="0ab65-398">Modul, schéma a konfiguraci umístění souborů</span><span class="sxs-lookup"><span data-stu-id="0ab65-398">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="0ab65-399">Modul</span><span class="sxs-lookup"><span data-stu-id="0ab65-399">Module</span></span>

<span data-ttu-id="0ab65-400">**IIS (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="0ab65-400">**IIS (x86/amd64):**</span></span>

   * <span data-ttu-id="0ab65-401">%windir%\System32\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="0ab65-401">%windir%\System32\inetsrv\aspnetcore.dll</span></span>

   * <span data-ttu-id="0ab65-402">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="0ab65-402">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span></span>

::: moniker range=">= aspnetcore-2.2"

   * <span data-ttu-id="0ab65-403">%ProgramFiles%\IIS\Asp.NET core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="0ab65-403">%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

   * <span data-ttu-id="0ab65-404">% ProgramFiles (x86) %\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="0ab65-404">%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

::: moniker-end

<span data-ttu-id="0ab65-405">**Služba IIS Express (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="0ab65-405">**IIS Express (x86/amd64):**</span></span>

   * <span data-ttu-id="0ab65-406">%ProgramFiles%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="0ab65-406">%ProgramFiles%\IIS Express\aspnetcore.dll</span></span>

   * <span data-ttu-id="0ab65-407">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="0ab65-407">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span></span>

::: moniker range=">= aspnetcore-2.2"

   * <span data-ttu-id="0ab65-408">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="0ab65-408">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

   * <span data-ttu-id="0ab65-409">% ProgramFiles (x86) %\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="0ab65-409">%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

::: moniker-end

### <a name="schema"></a><span data-ttu-id="0ab65-410">Schéma</span><span class="sxs-lookup"><span data-stu-id="0ab65-410">Schema</span></span>

<span data-ttu-id="0ab65-411">**SLUŽBA IIS**</span><span class="sxs-lookup"><span data-stu-id="0ab65-411">**IIS**</span></span>

   * <span data-ttu-id="0ab65-412">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="0ab65-412">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span></span>

::: moniker range=">= aspnetcore-2.2"

   * <span data-ttu-id="0ab65-413">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.XML</span><span class="sxs-lookup"><span data-stu-id="0ab65-413">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span></span>

::: moniker-end
<span data-ttu-id="0ab65-414">**Služba IIS Express**</span><span class="sxs-lookup"><span data-stu-id="0ab65-414">**IIS Express**</span></span>

   * <span data-ttu-id="0ab65-415">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="0ab65-415">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span></span>

::: moniker range=">= aspnetcore-2.2"

   * <span data-ttu-id="0ab65-416">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span><span class="sxs-lookup"><span data-stu-id="0ab65-416">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span></span>

::: moniker-end

### <a name="configuration"></a><span data-ttu-id="0ab65-417">Konfigurace</span><span class="sxs-lookup"><span data-stu-id="0ab65-417">Configuration</span></span>

<span data-ttu-id="0ab65-418">**SLUŽBA IIS**</span><span class="sxs-lookup"><span data-stu-id="0ab65-418">**IIS**</span></span>

   * <span data-ttu-id="0ab65-419">%windir%\System32\inetsrv\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="0ab65-419">%windir%\System32\inetsrv\config\applicationHost.config</span></span>

<span data-ttu-id="0ab65-420">**Služba IIS Express**</span><span class="sxs-lookup"><span data-stu-id="0ab65-420">**IIS Express**</span></span>

   * <span data-ttu-id="0ab65-421">%ProgramFiles%\IIS Express\config\templates\PersonalWebServer\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="0ab65-421">%ProgramFiles%\IIS Express\config\templates\PersonalWebServer\applicationHost.config</span></span>

<span data-ttu-id="0ab65-422">Soubory můžete najít tak, že *aspnetcore* v *applicationHost.config* souboru.</span><span class="sxs-lookup"><span data-stu-id="0ab65-422">The files can be found by searching for *aspnetcore* in the *applicationHost.config* file.</span></span>
