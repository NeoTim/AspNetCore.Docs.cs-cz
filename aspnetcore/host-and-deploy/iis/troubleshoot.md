---
title: Řešení potíží s ASP.NET Core ve službě IIS
author: guardrex
description: Zjistěte, jak diagnostikovat problémy s nasazením aplikací ASP.NET Core Internetové informační služby (IIS).
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/19/2019
uid: host-and-deploy/iis/troubleshoot
ms.openlocfilehash: 4df370dd9b1a5a651bcf767b8b9ace4220bdc345
ms.sourcegitcommit: 9f11685382eb1f4dd0fb694dea797adacedf9e20
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/21/2019
ms.locfileid: "67313659"
---
# <a name="troubleshoot-aspnet-core-on-iis"></a><span data-ttu-id="4b796-103">Řešení potíží s ASP.NET Core ve službě IIS</span><span class="sxs-lookup"><span data-stu-id="4b796-103">Troubleshoot ASP.NET Core on IIS</span></span>

<span data-ttu-id="4b796-104">Podle [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="4b796-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="4b796-105">Tento článek obsahuje pokyny o tom, jak Diagnostika ASP.NET Core problém při spuštění aplikace při hostování za nástrojem s [Internetové informační služby (IIS)](/iis).</span><span class="sxs-lookup"><span data-stu-id="4b796-105">This article provides instructions on how to diagnose an ASP.NET Core app startup issue when hosting with [Internet Information Services (IIS)](/iis).</span></span> <span data-ttu-id="4b796-106">Informace v tomto článku se vztahují k hostování ve službě IIS na serveru systému Windows a Windows Desktop.</span><span class="sxs-lookup"><span data-stu-id="4b796-106">The information in this article applies to hosting in IIS on Windows Server and Windows Desktop.</span></span>

<span data-ttu-id="4b796-107">Další témata pro řešení potíží:</span><span class="sxs-lookup"><span data-stu-id="4b796-107">Additional troubleshooting topics:</span></span>

* <span data-ttu-id="4b796-108">Azure App Service používá také [modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module) a služby IIS pro hostování aplikací.</span><span class="sxs-lookup"><span data-stu-id="4b796-108">Azure App Service also uses the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) and IIS to host apps.</span></span> <span data-ttu-id="4b796-109">Poradce při potížích, která se týkají konkrétně Azure App Service, najdete v části <xref:host-and-deploy/azure-apps/troubleshoot>.</span><span class="sxs-lookup"><span data-stu-id="4b796-109">For troubleshooting advice that pertains specifically to Azure App Service, see <xref:host-and-deploy/azure-apps/troubleshoot>.</span></span>
* <span data-ttu-id="4b796-110"><xref:fundamentals/error-handling> Popisuje způsob zpracování chyb v ASP.NET Core aplikace během vývoje v místním systému.</span><span class="sxs-lookup"><span data-stu-id="4b796-110"><xref:fundamentals/error-handling> covers how to handle errors in ASP.NET Core apps during development on a local system.</span></span>
* <span data-ttu-id="4b796-111">[Zjistěte, jak ladit pomocí sady Visual Studio](/visualstudio/debugger/getting-started-with-the-debugger) představuje funkce ladicího programu sady Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="4b796-111">[Learn to debug using Visual Studio](/visualstudio/debugger/getting-started-with-the-debugger) introduces the features of the Visual Studio debugger.</span></span>
* <span data-ttu-id="4b796-112">[Ladění pomocí Visual Studio Code](https://code.visualstudio.com/docs/editor/debugging) popisuje podporu ladění, které jsou součástí Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="4b796-112">[Debugging with Visual Studio Code](https://code.visualstudio.com/docs/editor/debugging) describes the debugging support built into Visual Studio Code.</span></span>

[!INCLUDE[](~/includes/azure-iis-startup-errors.md)]

## <a name="troubleshoot-app-startup-errors"></a><span data-ttu-id="4b796-113">Řešení chyb při spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="4b796-113">Troubleshoot app startup errors</span></span>

### <a name="enable-the-aspnet-core-module-debug-log"></a><span data-ttu-id="4b796-114">Povolit protokol ladění modul ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4b796-114">Enable the ASP.NET Core Module debug log</span></span>

<span data-ttu-id="4b796-115">Přidáním následujícího nastavení obslužné rutiny na aplikaci *web.config* souboru povolení protokolů ladění modul ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="4b796-115">Add the following handler settings to the app's *web.config* file to enable ASP.NET Core Module debug logs:</span></span>

```xml
<aspNetCore ...>
  <handlerSettings>
    <handlerSetting name="debugLevel" value="file" />
    <handlerSetting name="debugFile" value="c:\temp\ancm.log" />
  </handlerSettings>
</aspNetCore>
```

<span data-ttu-id="4b796-116">Potvrďte, že cesta zadaná pro protokol existuje a že identita fondu aplikací má oprávnění k zápisu do umístění.</span><span class="sxs-lookup"><span data-stu-id="4b796-116">Confirm that the path specified for the log exists and that the app pool's identity has write permissions to the location.</span></span>

### <a name="application-event-log"></a><span data-ttu-id="4b796-117">Protokol událostí aplikace</span><span class="sxs-lookup"><span data-stu-id="4b796-117">Application Event Log</span></span>

<span data-ttu-id="4b796-118">Přístup k protokolu událostí aplikace:</span><span class="sxs-lookup"><span data-stu-id="4b796-118">Access the Application Event Log:</span></span>

1. <span data-ttu-id="4b796-119">Otevření nabídky Start, vyhledejte **Prohlížeč událostí**a pak vyberte **Prohlížeč událostí** aplikace.</span><span class="sxs-lookup"><span data-stu-id="4b796-119">Open the Start menu, search for **Event Viewer**, and then select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="4b796-120">V **Prohlížeč událostí**, otevřete **protokoly Windows** uzlu.</span><span class="sxs-lookup"><span data-stu-id="4b796-120">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="4b796-121">Vyberte **aplikace** otevřít protokol událostí aplikace.</span><span class="sxs-lookup"><span data-stu-id="4b796-121">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="4b796-122">Vyhledejte chyby související s selhání aplikace.</span><span class="sxs-lookup"><span data-stu-id="4b796-122">Search for errors associated with the failing app.</span></span> <span data-ttu-id="4b796-123">Chyby mají hodnotu *modulu IIS AspNetCore* nebo *služby IIS Express AspNetCore modulu* v *zdroj* sloupce.</span><span class="sxs-lookup"><span data-stu-id="4b796-123">Errors have a value of *IIS AspNetCore Module* or *IIS Express AspNetCore Module* in the *Source* column.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="4b796-124">Spuštění aplikace příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="4b796-124">Run the app at a command prompt</span></span>

<span data-ttu-id="4b796-125">Mnoho chyb při spuštění nevytvářejí užitečné informace v protokolu událostí aplikace.</span><span class="sxs-lookup"><span data-stu-id="4b796-125">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="4b796-126">Příčin některých chyb můžete najít spuštěním aplikace v příkazovém řádku v hostitelském systému.</span><span class="sxs-lookup"><span data-stu-id="4b796-126">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span>

#### <a name="framework-dependent-deployment"></a><span data-ttu-id="4b796-127">Nasazení závisí na architektuře</span><span class="sxs-lookup"><span data-stu-id="4b796-127">Framework-dependent deployment</span></span>

<span data-ttu-id="4b796-128">Pokud je aplikace [nasazení závisí na architektuře](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span><span class="sxs-lookup"><span data-stu-id="4b796-128">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

1. <span data-ttu-id="4b796-129">Na příkazovém řádku přejděte do složky pro nasazení a spuštění aplikace spuštěním sestavení aplikace s *dotnet.exe*.</span><span class="sxs-lookup"><span data-stu-id="4b796-129">At a command prompt, navigate to the deployment folder and run the app by executing the app's assembly with *dotnet.exe*.</span></span> <span data-ttu-id="4b796-130">V následujícím příkazu nahraďte název sestavení aplikace pro \<název_sestavení >: `dotnet .\<assembly_name>.dll`.</span><span class="sxs-lookup"><span data-stu-id="4b796-130">In the following command, substitute the name of the app's assembly for \<assembly_name>: `dotnet .\<assembly_name>.dll`.</span></span>
1. <span data-ttu-id="4b796-131">Výstup z aplikace zobrazuje všechny chyby konzoly je zapsán do okna konzoly.</span><span class="sxs-lookup"><span data-stu-id="4b796-131">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="4b796-132">Je-li této chybě dojde při požadavku na aplikaci, vytvořte žádost na hostitele a port, kde Kestrel naslouchá.</span><span class="sxs-lookup"><span data-stu-id="4b796-132">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="4b796-133">Pomocí výchozího hostitele a post, vytvořit žádost o `http://localhost:5000/`.</span><span class="sxs-lookup"><span data-stu-id="4b796-133">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="4b796-134">Aplikace reaguje, obvykle na adrese Kestrel koncový bod, tím je pravděpodobnější týkající se konfigurace hostování a méně pravděpodobné, že v rámci aplikace.</span><span class="sxs-lookup"><span data-stu-id="4b796-134">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

#### <a name="self-contained-deployment"></a><span data-ttu-id="4b796-135">Samostatná nasazení</span><span class="sxs-lookup"><span data-stu-id="4b796-135">Self-contained deployment</span></span>

<span data-ttu-id="4b796-136">Pokud je aplikace [samostatná nasazení](/dotnet/core/deploying/#self-contained-deployments-scd):</span><span class="sxs-lookup"><span data-stu-id="4b796-136">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

1. <span data-ttu-id="4b796-137">Na příkazovém řádku přejděte do složky pro nasazení a spuštění spustitelného souboru aplikace.</span><span class="sxs-lookup"><span data-stu-id="4b796-137">At a command prompt, navigate to the deployment folder and run the app's executable.</span></span> <span data-ttu-id="4b796-138">V následujícím příkazu nahraďte název sestavení aplikace pro \<název_sestavení >: `<assembly_name>.exe`.</span><span class="sxs-lookup"><span data-stu-id="4b796-138">In the following command, substitute the name of the app's assembly for \<assembly_name>: `<assembly_name>.exe`.</span></span>
1. <span data-ttu-id="4b796-139">Výstup z aplikace zobrazuje všechny chyby konzoly je zapsán do okna konzoly.</span><span class="sxs-lookup"><span data-stu-id="4b796-139">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="4b796-140">Je-li této chybě dojde při požadavku na aplikaci, vytvořte žádost na hostitele a port, kde Kestrel naslouchá.</span><span class="sxs-lookup"><span data-stu-id="4b796-140">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="4b796-141">Pomocí výchozího hostitele a post, vytvořit žádost o `http://localhost:5000/`.</span><span class="sxs-lookup"><span data-stu-id="4b796-141">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="4b796-142">Aplikace reaguje, obvykle na adrese Kestrel koncový bod, tím je pravděpodobnější týkající se konfigurace hostování a méně pravděpodobné, že v rámci aplikace.</span><span class="sxs-lookup"><span data-stu-id="4b796-142">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

### <a name="aspnet-core-module-stdout-log"></a><span data-ttu-id="4b796-143">ASP.NET Core modulu stdout protokolu</span><span class="sxs-lookup"><span data-stu-id="4b796-143">ASP.NET Core Module stdout log</span></span>

<span data-ttu-id="4b796-144">Povolení a zobrazení protokolů stdout:</span><span class="sxs-lookup"><span data-stu-id="4b796-144">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="4b796-145">Přejděte do složky pro nasazení webu v hostitelském systému.</span><span class="sxs-lookup"><span data-stu-id="4b796-145">Navigate to the site's deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="4b796-146">Pokud *protokoly* složka není k dispozici, vytvořte složku.</span><span class="sxs-lookup"><span data-stu-id="4b796-146">If the *logs* folder isn't present, create the folder.</span></span> <span data-ttu-id="4b796-147">Pokyny o tom, jak povolit MSBuild k vytvoření *protokoly* složky v nasazení automaticky, zobrazí [adresářovou strukturu](xref:host-and-deploy/directory-structure) tématu.</span><span class="sxs-lookup"><span data-stu-id="4b796-147">For instructions on how to enable MSBuild to create the *logs* folder in the deployment automatically, see the [Directory structure](xref:host-and-deploy/directory-structure) topic.</span></span>
1. <span data-ttu-id="4b796-148">Upravit *web.config* souboru.</span><span class="sxs-lookup"><span data-stu-id="4b796-148">Edit the *web.config* file.</span></span> <span data-ttu-id="4b796-149">Nastavte **stdoutLogEnabled** k `true` a změnit **stdoutLogFile** tak, aby odkazoval na cestu *protokoly* složky (například `.\logs\stdout`).</span><span class="sxs-lookup"><span data-stu-id="4b796-149">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to point to the *logs* folder (for example, `.\logs\stdout`).</span></span> <span data-ttu-id="4b796-150">`stdout` v cestě je předpona názvu souboru protokolu.</span><span class="sxs-lookup"><span data-stu-id="4b796-150">`stdout` in the path is the log file name prefix.</span></span> <span data-ttu-id="4b796-151">Časové razítko, id procesu a příponu souboru jsou přidány automaticky při vytvoření protokolu.</span><span class="sxs-lookup"><span data-stu-id="4b796-151">A timestamp, process id, and file extension are added automatically when the log is created.</span></span> <span data-ttu-id="4b796-152">Pomocí `stdout` jako předpona názvu souboru, má název souboru typické protokolu *stdout_20180205184032_5412.log*.</span><span class="sxs-lookup"><span data-stu-id="4b796-152">Using `stdout` as the file name prefix, a typical log file is named *stdout_20180205184032_5412.log*.</span></span>
1. <span data-ttu-id="4b796-153">Ověřte identitu fondu aplikací má oprávnění k zápisu *protokoly* složky.</span><span class="sxs-lookup"><span data-stu-id="4b796-153">Ensure your application pool's identity has write permissions to the *logs* folder.</span></span>
1. <span data-ttu-id="4b796-154">Uložte aktualizovaný *web.config* souboru.</span><span class="sxs-lookup"><span data-stu-id="4b796-154">Save the updated *web.config* file.</span></span>
1. <span data-ttu-id="4b796-155">Vytvořte žádost do aplikace.</span><span class="sxs-lookup"><span data-stu-id="4b796-155">Make a request to the app.</span></span>
1. <span data-ttu-id="4b796-156">Přejděte *protokoly* složky.</span><span class="sxs-lookup"><span data-stu-id="4b796-156">Navigate to the *logs* folder.</span></span> <span data-ttu-id="4b796-157">Vyhledání a otevření protokolu nejnovější stdout.</span><span class="sxs-lookup"><span data-stu-id="4b796-157">Find and open the most recent stdout log.</span></span>
1. <span data-ttu-id="4b796-158">Studie v protokolu chyb.</span><span class="sxs-lookup"><span data-stu-id="4b796-158">Study the log for errors.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="4b796-159">Zakážete protokolování stdout po dokončení odstraňování potíží.</span><span class="sxs-lookup"><span data-stu-id="4b796-159">Disable stdout logging when troubleshooting is complete.</span></span>

1. <span data-ttu-id="4b796-160">Upravit *web.config* souboru.</span><span class="sxs-lookup"><span data-stu-id="4b796-160">Edit the *web.config* file.</span></span>
1. <span data-ttu-id="4b796-161">Nastavte **stdoutLogEnabled** k `false`.</span><span class="sxs-lookup"><span data-stu-id="4b796-161">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="4b796-162">Uložte soubor.</span><span class="sxs-lookup"><span data-stu-id="4b796-162">Save the file.</span></span>

> [!WARNING]
> <span data-ttu-id="4b796-163">Nepodařilo se zakázat protokol stdout může vést k selhání aplikace nebo serveru.</span><span class="sxs-lookup"><span data-stu-id="4b796-163">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="4b796-164">Neexistuje žádné omezení velikosti souboru protokolu nebo počet souborů protokolů, které jsou vytvořeny.</span><span class="sxs-lookup"><span data-stu-id="4b796-164">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="4b796-165">Pro rutiny protokolování v aplikaci ASP.NET Core, použijte protokolování knihovnu, která omezuje velikost souboru protokolu a otočí protokoly.</span><span class="sxs-lookup"><span data-stu-id="4b796-165">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="4b796-166">Další informace najdete v tématu [zprostředkovatele přihlášení třetí strany](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="4b796-166">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

## <a name="enable-the-developer-exception-page"></a><span data-ttu-id="4b796-167">Povolit na stránce výjimek pro vývojáře</span><span class="sxs-lookup"><span data-stu-id="4b796-167">Enable the Developer Exception Page</span></span>

<span data-ttu-id="4b796-168">`ASPNETCORE_ENVIRONMENT` [Proměnnou prostředí lze přidat do souboru web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) ke spuštění aplikace ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="4b796-168">The `ASPNETCORE_ENVIRONMENT` [environment variable can be added to web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) to run the app in the Development environment.</span></span> <span data-ttu-id="4b796-169">Tak dlouho, dokud není prostředí přepsána ve spuštění aplikace podle `UseEnvironment` na tvůrce hostitele nastavení proměnné prostředí umožňuje [stránku výjimek pro vývojáře](xref:fundamentals/error-handling) se zobrazí při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="4b796-169">As long as the environment isn't overridden in app startup by `UseEnvironment` on the host builder, setting the environment variable allows the [Developer Exception Page](xref:fundamentals/error-handling) to appear when the app is run.</span></span>

::: moniker range=">= aspnetcore-2.2"

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile=".\logs\stdout"
      hostingModel="InProcess">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
  </environmentVariables>
</aspNetCore>
```

::: moniker-end

::: moniker range="< aspnetcore-2.2"

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile=".\logs\stdout">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
  </environmentVariables>
</aspNetCore>
```

::: moniker-end

<span data-ttu-id="4b796-170">Nastavení proměnné prostředí pro `ASPNETCORE_ENVIRONMENT` se doporučuje jenom pro použití v přípravy a testování serverů, které nejsou vystaveny v Internetu.</span><span class="sxs-lookup"><span data-stu-id="4b796-170">Setting the environment variable for `ASPNETCORE_ENVIRONMENT` is only recommended for use on staging and testing servers that aren't exposed to the Internet.</span></span> <span data-ttu-id="4b796-171">Odebrat z proměnné prostředí *web.config* soubor po vyřešení potíží.</span><span class="sxs-lookup"><span data-stu-id="4b796-171">Remove the environment variable from the *web.config* file after troubleshooting.</span></span> <span data-ttu-id="4b796-172">Informace o nastavení proměnných prostředí *web.config*, naleznete v tématu [environmentVariables podřízený prvek aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).</span><span class="sxs-lookup"><span data-stu-id="4b796-172">For information on setting environment variables in *web.config*, see [environmentVariables child element of aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).</span></span>

## <a name="common-startup-errors"></a><span data-ttu-id="4b796-173">Běžné chyby spuštění</span><span class="sxs-lookup"><span data-stu-id="4b796-173">Common startup errors</span></span>

<span data-ttu-id="4b796-174">Viz <xref:host-and-deploy/azure-iis-errors-reference>.</span><span class="sxs-lookup"><span data-stu-id="4b796-174">See <xref:host-and-deploy/azure-iis-errors-reference>.</span></span> <span data-ttu-id="4b796-175">Většina běžných problémů, které brání spuštění aplikace jsou zahrnuté v referenčním tématu.</span><span class="sxs-lookup"><span data-stu-id="4b796-175">Most of the common problems that prevent app startup are covered in the reference topic.</span></span>

## <a name="obtain-data-from-an-app"></a><span data-ttu-id="4b796-176">Získání dat z aplikace</span><span class="sxs-lookup"><span data-stu-id="4b796-176">Obtain data from an app</span></span>

<span data-ttu-id="4b796-177">Pokud aplikace je schopná reagovat na požadavky, získáte žádost o připojení a další data z aplikace pomocí terminálu vložené middlewaru.</span><span class="sxs-lookup"><span data-stu-id="4b796-177">If an app is capable of responding to requests, obtain request, connection, and additional data from the app using terminal inline middleware.</span></span> <span data-ttu-id="4b796-178">Další informace a ukázky kódu najdete v tématu <xref:test/troubleshoot#obtain-data-from-an-app>.</span><span class="sxs-lookup"><span data-stu-id="4b796-178">For more information and sample code, see <xref:test/troubleshoot#obtain-data-from-an-app>.</span></span>

## <a name="create-a-dump"></a><span data-ttu-id="4b796-179">Vytvoření výpisu</span><span class="sxs-lookup"><span data-stu-id="4b796-179">Create a dump</span></span>

<span data-ttu-id="4b796-180">A *s výpisem paměti* snímek paměti v systému a vám může pomoct zjistit příčinu selhání aplikace, spuštění selhání nebo pomalé aplikace.</span><span class="sxs-lookup"><span data-stu-id="4b796-180">A *dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="4b796-181">Aplikace dojde k chybě nebo dojde k výjimce</span><span class="sxs-lookup"><span data-stu-id="4b796-181">App crashes or encounters an exception</span></span>

<span data-ttu-id="4b796-182">Získání a analýza výpisu paměti z [hlášení chyb Windows (zasílání)](/windows/desktop/wer/windows-error-reporting):</span><span class="sxs-lookup"><span data-stu-id="4b796-182">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="4b796-183">Vytvořte složku pro uložení souborů se stavem systému na `c:\dumps`.</span><span class="sxs-lookup"><span data-stu-id="4b796-183">Create a folder to hold crash dump files at `c:\dumps`.</span></span> <span data-ttu-id="4b796-184">Fond aplikací musí mít oprávnění k zápisu do složky.</span><span class="sxs-lookup"><span data-stu-id="4b796-184">The app pool must have write access to the folder.</span></span>
1. <span data-ttu-id="4b796-185">Spustit [EnableDumps Powershellový skript](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/iis/troubleshoot/scripts/EnableDumps.ps1):</span><span class="sxs-lookup"><span data-stu-id="4b796-185">Run the [EnableDumps PowerShell script](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/iis/troubleshoot/scripts/EnableDumps.ps1):</span></span>
   * <span data-ttu-id="4b796-186">Pokud aplikace využívá [model hostingu v procesu](xref:host-and-deploy/iis/index#in-process-hosting-model), spusťte skript pro *w3wp.exe*:</span><span class="sxs-lookup"><span data-stu-id="4b796-186">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\EnableDumps w3wp.exe c:\dumps
     ```

   * <span data-ttu-id="4b796-187">Pokud aplikace využívá [model hostingu mimo proces](xref:host-and-deploy/iis/index#out-of-process-hosting-model), spusťte skript pro *dotnet.exe*:</span><span class="sxs-lookup"><span data-stu-id="4b796-187">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\EnableDumps dotnet.exe c:\dumps
     ```

1. <span data-ttu-id="4b796-188">Spusťte aplikaci v rámci podmínky, které způsobily selhání dojde k.</span><span class="sxs-lookup"><span data-stu-id="4b796-188">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="4b796-189">Po došlo k selhání, spusťte [DisableDumps Powershellový skript](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/iis/troubleshoot/scripts/DisableDumps.ps1):</span><span class="sxs-lookup"><span data-stu-id="4b796-189">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/iis/troubleshoot/scripts/DisableDumps.ps1):</span></span>
   * <span data-ttu-id="4b796-190">Pokud aplikace využívá [model hostingu v procesu](xref:host-and-deploy/iis/index#in-process-hosting-model), spusťte skript pro *w3wp.exe*:</span><span class="sxs-lookup"><span data-stu-id="4b796-190">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\DisableDumps w3wp.exe
     ```

   * <span data-ttu-id="4b796-191">Pokud aplikace využívá [model hostingu mimo proces](xref:host-and-deploy/iis/index#out-of-process-hosting-model), spusťte skript pro *dotnet.exe*:</span><span class="sxs-lookup"><span data-stu-id="4b796-191">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\DisableDumps dotnet.exe
     ```

<span data-ttu-id="4b796-192">Po dokončení shromažďování výpisu stavu systému dojde k chybě aplikace a aplikace je povoleno jej měla ukončit normálně.</span><span class="sxs-lookup"><span data-stu-id="4b796-192">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="4b796-193">Skript Powershellu nakonfiguruje zasílání shromažďovat až o pěti výpisů paměti na aplikaci.</span><span class="sxs-lookup"><span data-stu-id="4b796-193">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="4b796-194">Výpisy stavu systému může trvat až velké množství místa na disku (až několik gigabajtů každý).</span><span class="sxs-lookup"><span data-stu-id="4b796-194">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="4b796-195">Aplikace přestane reagovat, při spuštění selže nebo běží normálně</span><span class="sxs-lookup"><span data-stu-id="4b796-195">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="4b796-196">Když aplikaci *přestane reagovat* (přestane reagovat, ale nedojde k chybě), selže při spuštění nebo spuštění za normálních okolností viz [soubory výpisu paměti uživatelského režimu: Výběr vždycky ten nejlepší nástroj](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) vybrat vhodného nástroje k vytvoření výpisu paměti.</span><span class="sxs-lookup"><span data-stu-id="4b796-196">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

### <a name="analyze-the-dump"></a><span data-ttu-id="4b796-197">Analýza výpisu paměti</span><span class="sxs-lookup"><span data-stu-id="4b796-197">Analyze the dump</span></span>

<span data-ttu-id="4b796-198">Výpis paměti mohou být analyzovány pomocí několik přístupů.</span><span class="sxs-lookup"><span data-stu-id="4b796-198">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="4b796-199">Další informace najdete v tématu [analýzy souboru výpisu paměti uživatelského režimu](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span><span class="sxs-lookup"><span data-stu-id="4b796-199">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="remote-debugging"></a><span data-ttu-id="4b796-200">Vzdálené ladění</span><span class="sxs-lookup"><span data-stu-id="4b796-200">Remote debugging</span></span>

<span data-ttu-id="4b796-201">Zobrazit [vzdálené ladění ASP.NET Core ve vzdáleném počítači služby IIS v sadě Visual Studio 2017](/visualstudio/debugger/remote-debugging-aspnet-on-a-remote-iis-computer) v dokumentaci k sadě Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="4b796-201">See [Remote Debug ASP.NET Core on a Remote IIS Computer in Visual Studio 2017](/visualstudio/debugger/remote-debugging-aspnet-on-a-remote-iis-computer) in the Visual Studio documentation.</span></span>

## <a name="application-insights"></a><span data-ttu-id="4b796-202">Application Insights</span><span class="sxs-lookup"><span data-stu-id="4b796-202">Application Insights</span></span>

<span data-ttu-id="4b796-203">[Application Insights](/azure/application-insights/) poskytuje telemetrická data z aplikací hostovaných službou IIS, včetně protokolování a generování sestav funkce chyb.</span><span class="sxs-lookup"><span data-stu-id="4b796-203">[Application Insights](/azure/application-insights/) provides telemetry from apps hosted by IIS, including error logging and reporting features.</span></span> <span data-ttu-id="4b796-204">Application Insights může jenom nahlásit to o chybách, ke kterým dochází po spuštění aplikace, když funkce protokolování aplikace budou k dispozici.</span><span class="sxs-lookup"><span data-stu-id="4b796-204">Application Insights can only report on errors that occur after the app starts when the app's logging features become available.</span></span> <span data-ttu-id="4b796-205">Další informace najdete v tématu [Application Insights pro ASP.NET Core](/azure/application-insights/app-insights-asp-net-core).</span><span class="sxs-lookup"><span data-stu-id="4b796-205">For more information, see [Application Insights for ASP.NET Core](/azure/application-insights/app-insights-asp-net-core).</span></span>

## <a name="additional-advice"></a><span data-ttu-id="4b796-206">Další Rady</span><span class="sxs-lookup"><span data-stu-id="4b796-206">Additional advice</span></span>

<span data-ttu-id="4b796-207">Někdy funkční aplikace selže okamžitě po provedení upgradu buď .NET Core SDK na vývojové počítače nebo balíček verze v rámci aplikace.</span><span class="sxs-lookup"><span data-stu-id="4b796-207">Sometimes a functioning app fails immediately after upgrading either the .NET Core SDK on the development machine or package versions within the app.</span></span> <span data-ttu-id="4b796-208">V některých případech osamocené balíčky mohou narušit funkce aplikace při provádění hlavní upgrady.</span><span class="sxs-lookup"><span data-stu-id="4b796-208">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="4b796-209">Většina těchto problémů můžete opravit podle těchto pokynů:</span><span class="sxs-lookup"><span data-stu-id="4b796-209">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="4b796-210">Odstranit *bin* a *obj* složek.</span><span class="sxs-lookup"><span data-stu-id="4b796-210">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="4b796-211">Vymazat balíček ukládá do mezipaměti na *% UserProfile %\\.nuget\\balíčky* a *% LocalAppData %\\Nuget\\v3-cache*.</span><span class="sxs-lookup"><span data-stu-id="4b796-211">Clear the package caches at *%UserProfile%\\.nuget\\packages* and *%LocalAppData%\\Nuget\\v3-cache*.</span></span>
1. <span data-ttu-id="4b796-212">Obnovit a znovu sestavte projekt.</span><span class="sxs-lookup"><span data-stu-id="4b796-212">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="4b796-213">Potvrďte, že předchozího nasazení na serveru byl zcela odstraněn před opětovným nasazením aplikace.</span><span class="sxs-lookup"><span data-stu-id="4b796-213">Confirm that the prior deployment on the server has been completely deleted prior to redeploying the app.</span></span>

> [!TIP]
> <span data-ttu-id="4b796-214">Pohodlný způsob, jak Vymazat mezipaměti balíčku je ke spuštění `dotnet nuget locals all --clear` z příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="4b796-214">A convenient way to clear package caches is to execute `dotnet nuget locals all --clear` from a command prompt.</span></span>
>
> <span data-ttu-id="4b796-215">Vymazání mezipaměti balíčku provést taky pomocí [nuget.exe](https://www.nuget.org/downloads) nástroj a provádění příkazu `nuget locals all -clear`.</span><span class="sxs-lookup"><span data-stu-id="4b796-215">Clearing package caches can also be accomplished by using the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="4b796-216">*nuget.exe* není připojené instalace s operačním systémem klasické pracovní plochy Windows a je potřeba pořídit samostatně z [webu NuGet](https://www.nuget.org/downloads).</span><span class="sxs-lookup"><span data-stu-id="4b796-216">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4b796-217">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="4b796-217">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:fundamentals/error-handling>
* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/azure-apps/troubleshoot>
