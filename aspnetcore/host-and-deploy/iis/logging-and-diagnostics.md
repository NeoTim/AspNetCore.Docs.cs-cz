---
title: Vytvoření a přesměrování protokolu pomocí modulu ASP.NET Core
author: rick-anderson
description: Nakonfigurujte službu IIS a modul ASP.NET Core pro zaznamenávání protokolů a diagnostických informací.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
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
uid: host-and-deploy/iis/logging-and-diagnostics
ms.openlocfilehash: 523eec53d7d21723dcf136c4e5ce299533a78cc6
ms.sourcegitcommit: daa9ccf580df531254da9dce8593441ac963c674
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91901022"
---
# <a name="log-creation-and-redirection"></a><span data-ttu-id="a1f6c-103">Vytvoření a přesměrování protokolu</span><span class="sxs-lookup"><span data-stu-id="a1f6c-103">Log creation and redirection</span></span>

<span data-ttu-id="a1f6c-104">Modul ASP.NET Core přesměruje výstup konzoly stdout a stderr na disk, pokud `stdoutLogEnabled` `stdoutLogFile` `aspNetCore` jsou nastaveny atributy a elementu.</span><span class="sxs-lookup"><span data-stu-id="a1f6c-104">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="a1f6c-105">`stdoutLogFile`Při vytvoření souboru protokolu jsou všechny složky v této cestě vytvářeny modulem.</span><span class="sxs-lookup"><span data-stu-id="a1f6c-105">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="a1f6c-106">Fond aplikací musí mít oprávnění k zápisu do umístění, kam se protokoly zapisují (použijte `IIS AppPool\{APP POOL NAME}` k poskytnutí oprávnění k zápisu, kde zástupný symbol `{APP POOL NAME}` je název fondu aplikací).</span><span class="sxs-lookup"><span data-stu-id="a1f6c-106">The app pool must have write access to the location where the logs are written (use `IIS AppPool\{APP POOL NAME}` to provide write permission, where the placeholder `{APP POOL NAME}` is the app pool name).</span></span>

<span data-ttu-id="a1f6c-107">Protokoly nejsou otočeny, pokud nedochází k recyklování/restartování procesu.</span><span class="sxs-lookup"><span data-stu-id="a1f6c-107">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="a1f6c-108">Je zodpovědností hostitele omezit místo na disku, které používají protokoly.</span><span class="sxs-lookup"><span data-stu-id="a1f6c-108">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="a1f6c-109">Použití protokolu stdout se doporučuje pouze při řešení potíží se spouštěním aplikací při hostování služby IIS nebo při použití [podpory v době vývoje služby IIS se sadou Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), nikoli při ladění místně a při spuštění aplikace s IIS Express.</span><span class="sxs-lookup"><span data-stu-id="a1f6c-109">Using the stdout log is only recommended for troubleshooting app startup issues when hosting on IIS or when using [development-time support for IIS with Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), not while debugging locally and running the app with IIS Express.</span></span>

<span data-ttu-id="a1f6c-110">Nepoužívejte protokol stdout pro obecné účely protokolování aplikace.</span><span class="sxs-lookup"><span data-stu-id="a1f6c-110">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="a1f6c-111">Pro rutinu protokolování v aplikaci ASP.NET Core použijte knihovnu protokolování, která omezuje velikost souboru protokolu a otočí protokoly.</span><span class="sxs-lookup"><span data-stu-id="a1f6c-111">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="a1f6c-112">Další informace najdete v tématu [Zprostředkovatelé protokolování třetích stran](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="a1f6c-112">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="a1f6c-113">Časové razítko a Přípona souboru se automaticky přidají při vytvoření souboru protokolu.</span><span class="sxs-lookup"><span data-stu-id="a1f6c-113">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="a1f6c-114">Název souboru protokolu se skládá tak, že se připojí časové razítko, ID procesu a Přípona souboru ( `.log` ) k poslednímu segmentu `stdoutLogFile` cesty (obvykle `stdout` ) oddělené podtržítky.</span><span class="sxs-lookup"><span data-stu-id="a1f6c-114">The log file name is composed by appending the timestamp, process ID, and file extension (`.log`) to the last segment of the `stdoutLogFile` path (typically `stdout`) delimited by underscores.</span></span> <span data-ttu-id="a1f6c-115">Pokud `stdoutLogFile` cesta končí `stdout` , protokol pro aplikaci s číslem PID 1934 vytvořeným v 2/5/2018 v 19:42:32 má název souboru `stdout_20180205194132_1934.log` .</span><span class="sxs-lookup"><span data-stu-id="a1f6c-115">If the `stdoutLogFile` path ends with `stdout`, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name `stdout_20180205194132_1934.log`.</span></span>

<span data-ttu-id="a1f6c-116">Pokud `stdoutLogEnabled` je hodnota false, chyby, ke kterým dochází při spuštění aplikace, se zaznamenávají a generují do protokolu událostí až do 30 kB.</span><span class="sxs-lookup"><span data-stu-id="a1f6c-116">If `stdoutLogEnabled` is false, errors that occur on app startup are captured and emitted to the event log up to 30 KB.</span></span> <span data-ttu-id="a1f6c-117">Po spuštění budou všechny další protokoly zahozeny.</span><span class="sxs-lookup"><span data-stu-id="a1f6c-117">After startup, all additional logs are discarded.</span></span>

<span data-ttu-id="a1f6c-118">Následující vzorový `aspNetCore` prvek nakonfiguruje protokolování stdout na relativní cestě `.\log\` .</span><span class="sxs-lookup"><span data-stu-id="a1f6c-118">The following sample `aspNetCore` element configures stdout logging at the relative path `.\log\`.</span></span> <span data-ttu-id="a1f6c-119">Potvrďte, že identita uživatele fondu aplikací má oprávnění k zápisu do zadané cesty.</span><span class="sxs-lookup"><span data-stu-id="a1f6c-119">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout"
    hostingModel="inprocess">
</aspNetCore>
```

<span data-ttu-id="a1f6c-120">Když publikujete aplikaci pro nasazení Azure App Service, sada web SDK nastaví `stdoutLogFile` hodnotu na `\\?\%home%\LogFiles\stdout` .</span><span class="sxs-lookup"><span data-stu-id="a1f6c-120">When publishing an app for Azure App Service deployment, the Web SDK sets the `stdoutLogFile` value to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="a1f6c-121">`%home`Proměnná prostředí je předdefinovaná pro aplikace hostované v Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="a1f6c-121">The `%home` environment variable is predefined for apps hosted by Azure App Service.</span></span>

<span data-ttu-id="a1f6c-122">Chcete-li vytvořit pravidla filtru protokolování, přečtěte si část [Konfigurace](xref:fundamentals/logging/index#log-filtering) a [filtrování protokolů](xref:fundamentals/logging/index#log-filtering) v dokumentaci ASP.NET Core protokolování.</span><span class="sxs-lookup"><span data-stu-id="a1f6c-122">To create logging filter rules, see the [Configuration](xref:fundamentals/logging/index#log-filtering) and [Log filtering](xref:fundamentals/logging/index#log-filtering) sections of the ASP.NET Core logging documentation.</span></span>

<span data-ttu-id="a1f6c-123">Další informace o formátech cest najdete v tématu [formáty souborů cest v systémech Windows](/dotnet/standard/io/file-path-formats).</span><span class="sxs-lookup"><span data-stu-id="a1f6c-123">For more information on path formats, see [File path formats on Windows systems](/dotnet/standard/io/file-path-formats).</span></span>

## <a name="enhanced-diagnostic-logs"></a><span data-ttu-id="a1f6c-124">Rozšířené diagnostické protokoly</span><span class="sxs-lookup"><span data-stu-id="a1f6c-124">Enhanced diagnostic logs</span></span>

<span data-ttu-id="a1f6c-125">Modul ASP.NET Core lze nakonfigurovat tak, aby poskytoval rozšířené diagnostické protokoly.</span><span class="sxs-lookup"><span data-stu-id="a1f6c-125">The ASP.NET Core Module is configurable to provide enhanced diagnostics logs.</span></span> <span data-ttu-id="a1f6c-126">Přidejte `<handlerSettings>` element do `<aspNetCore>` prvku v `web.config` .</span><span class="sxs-lookup"><span data-stu-id="a1f6c-126">Add the `<handlerSettings>` element to the `<aspNetCore>` element in `web.config`.</span></span> <span data-ttu-id="a1f6c-127">Nastavení `debugLevel` pro `TRACE` zpřístupňuje vyšší věrnost diagnostických informací:</span><span class="sxs-lookup"><span data-stu-id="a1f6c-127">Setting the `debugLevel` to `TRACE` exposes a higher fidelity of diagnostic information:</span></span>

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

<span data-ttu-id="a1f6c-128">Všechny složky v cestě ( `logs` v předchozím příkladu) se vytvoří modulem, když se vytvoří soubor protokolu.</span><span class="sxs-lookup"><span data-stu-id="a1f6c-128">Any folders in the path (`logs` in the preceding example) are created by the module when the log file is created.</span></span> <span data-ttu-id="a1f6c-129">Fond aplikací musí mít oprávnění k zápisu do umístění, kam se protokoly zapisují (použijte `IIS AppPool\{APP POOL NAME}` k poskytnutí oprávnění k zápisu, kde zástupný symbol `{APP POOL NAME}` je název fondu aplikací).</span><span class="sxs-lookup"><span data-stu-id="a1f6c-129">The app pool must have write access to the location where the logs are written (use `IIS AppPool\{APP POOL NAME}` to provide write permission, where the placeholder `{APP POOL NAME}` is the app pool name).</span></span>

<span data-ttu-id="a1f6c-130">Hodnoty úrovně ladění ( `debugLevel` ) mohou zahrnovat jak úroveň, tak i umístění.</span><span class="sxs-lookup"><span data-stu-id="a1f6c-130">Debug level (`debugLevel`) values can include both the level and the location.</span></span>

<span data-ttu-id="a1f6c-131">Úrovně (v pořadí od nejméně po nejpodrobnější):</span><span class="sxs-lookup"><span data-stu-id="a1f6c-131">Levels (in order from least to most verbose):</span></span>

* <span data-ttu-id="a1f6c-132">CHYBA</span><span class="sxs-lookup"><span data-stu-id="a1f6c-132">ERROR</span></span>
* <span data-ttu-id="a1f6c-133">WARNING</span><span class="sxs-lookup"><span data-stu-id="a1f6c-133">WARNING</span></span>
* <span data-ttu-id="a1f6c-134">PŘÍJEMCE</span><span class="sxs-lookup"><span data-stu-id="a1f6c-134">INFO</span></span>
* <span data-ttu-id="a1f6c-135">TRACE</span><span class="sxs-lookup"><span data-stu-id="a1f6c-135">TRACE</span></span>

<span data-ttu-id="a1f6c-136">Umístění (je povoleno více umístění):</span><span class="sxs-lookup"><span data-stu-id="a1f6c-136">Locations (multiple locations are permitted):</span></span>

* <span data-ttu-id="a1f6c-137">STROMU</span><span class="sxs-lookup"><span data-stu-id="a1f6c-137">CONSOLE</span></span>
* <span data-ttu-id="a1f6c-138">EVENTLOG</span><span class="sxs-lookup"><span data-stu-id="a1f6c-138">EVENTLOG</span></span>
* <span data-ttu-id="a1f6c-139">SOUBOR</span><span class="sxs-lookup"><span data-stu-id="a1f6c-139">FILE</span></span>

<span data-ttu-id="a1f6c-140">Nastavení obslužné rutiny lze také poskytnout pomocí proměnných prostředí:</span><span class="sxs-lookup"><span data-stu-id="a1f6c-140">The handler settings can also be provided via environment variables:</span></span>

* <span data-ttu-id="a1f6c-141">`ASPNETCORE_MODULE_DEBUG_FILE`: Cesta k souboru protokolu ladění.</span><span class="sxs-lookup"><span data-stu-id="a1f6c-141">`ASPNETCORE_MODULE_DEBUG_FILE`: Path to the debug log file.</span></span> <span data-ttu-id="a1f6c-142">(Výchozí: `aspnetcore-debug.log` )</span><span class="sxs-lookup"><span data-stu-id="a1f6c-142">(Default: `aspnetcore-debug.log`)</span></span>
* <span data-ttu-id="a1f6c-143">`ASPNETCORE_MODULE_DEBUG`: Nastavení úrovně ladění.</span><span class="sxs-lookup"><span data-stu-id="a1f6c-143">`ASPNETCORE_MODULE_DEBUG`: Debug level setting.</span></span>

> [!WARNING]
> <span data-ttu-id="a1f6c-144">Nenechávejte **protokolování** ladění povolené v nasazení po delší dobu, než je potřeba k řešení problému.</span><span class="sxs-lookup"><span data-stu-id="a1f6c-144">Do **not** leave debug logging enabled in the deployment for longer than required to troubleshoot an issue.</span></span> <span data-ttu-id="a1f6c-145">Velikost protokolu není omezená.</span><span class="sxs-lookup"><span data-stu-id="a1f6c-145">The size of the log isn't limited.</span></span> <span data-ttu-id="a1f6c-146">Když necháte protokol ladění povolený, může dojít k vyčerpání dostupného místa na disku a selhání serveru nebo služby App Service.</span><span class="sxs-lookup"><span data-stu-id="a1f6c-146">Leaving the debug log enabled can exhaust the available disk space and crash the server or app service.</span></span>

<span data-ttu-id="a1f6c-147">Příklad prvku v souboru naleznete v tématu [Konfigurace modulu `web.config` ASP.NET Core](xref:host-and-deploy/iis/web-config#configuration-of-aspnet-core-module-with-webconfig) `aspNetCore` `web.config` .</span><span class="sxs-lookup"><span data-stu-id="a1f6c-147">See [Configuration of ASP.NET Core Module with `web.config`](xref:host-and-deploy/iis/web-config#configuration-of-aspnet-core-module-with-webconfig) for an example of the `aspNetCore` element in the `web.config` file.</span></span>
