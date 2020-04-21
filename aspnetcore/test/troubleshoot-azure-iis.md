---
title: Řešení potíží ASP.NET jádro ve službě Azure App Service a IIS
author: rick-anderson
description: Zjistěte, jak diagnostikovat problémy s nasazením služby Azure App Service a Internet Information Services (IIS) aplikací ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
uid: test/troubleshoot-azure-iis
ms.openlocfilehash: f994cd1274bda9082a7cd8b637968b2769db1671
ms.sourcegitcommit: 5547d920f322e5a823575c031529e4755ab119de
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81661713"
---
# <a name="troubleshoot-aspnet-core-on-azure-app-service-and-iis"></a><span data-ttu-id="c9a8b-103">Řešení potíží ASP.NET jádro ve službě Azure App Service a IIS</span><span class="sxs-lookup"><span data-stu-id="c9a8b-103">Troubleshoot ASP.NET Core on Azure App Service and IIS</span></span>

<span data-ttu-id="c9a8b-104">Podle [Justin Kotalik](https://github.com/jkotalik)</span><span class="sxs-lookup"><span data-stu-id="c9a8b-104">By [Justin Kotalik](https://github.com/jkotalik)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c9a8b-105">Tento článek obsahuje informace o běžných chybách při spuštění aplikace a pokyny, jak diagnostikovat chyby při nasazení aplikace do služby Azure App Service nebo IIS:</span><span class="sxs-lookup"><span data-stu-id="c9a8b-105">This article provides information on common app startup errors and instructions on how to diagnose errors when an app is deployed to Azure App Service or IIS:</span></span>

[<span data-ttu-id="c9a8b-106">Chyby při spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="c9a8b-106">App startup errors</span></span>](#app-startup-errors)  
<span data-ttu-id="c9a8b-107">Vysvětluje běžné scénáře stavového kódu HTTP při spuštění.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-107">Explains common startup HTTP status code scenarios.</span></span>

[<span data-ttu-id="c9a8b-108">Poradce při potížích se službou Azure App Service</span><span class="sxs-lookup"><span data-stu-id="c9a8b-108">Troubleshoot on Azure App Service</span></span>](#troubleshoot-on-azure-app-service)  
<span data-ttu-id="c9a8b-109">Poskytuje rady pro řešení potíží pro aplikace nasazené do služby Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-109">Provides troubleshooting advice for apps deployed to Azure App Service.</span></span>

[<span data-ttu-id="c9a8b-110">Odstraňování potíží ve službě IIS</span><span class="sxs-lookup"><span data-stu-id="c9a8b-110">Troubleshoot on IIS</span></span>](#troubleshoot-on-iis)  
<span data-ttu-id="c9a8b-111">Poskytuje rady pro řešení potíží s aplikacemi nasazenými do služby IIS nebo spuštěných místně ve službě IIS Express.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-111">Provides troubleshooting advice for apps deployed to IIS or running on IIS Express locally.</span></span> <span data-ttu-id="c9a8b-112">Pokyny platí pro nasazení plochy systému Windows Server i Windows.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-112">The guidance applies to both Windows Server and Windows desktop deployments.</span></span>

[<span data-ttu-id="c9a8b-113">Vymazat mezipaměti balíčků</span><span class="sxs-lookup"><span data-stu-id="c9a8b-113">Clear package caches</span></span>](#clear-package-caches)  
<span data-ttu-id="c9a8b-114">Vysvětluje, co dělat, když nesouvislé balíčky přerušit aplikaci při provádění hlavních upgradů nebo změny verze balíčku.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-114">Explains what to do when incoherent packages break an app when performing major upgrades or changing package versions.</span></span>

[<span data-ttu-id="c9a8b-115">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="c9a8b-115">Additional resources</span></span>](#additional-resources)  
<span data-ttu-id="c9a8b-116">Obsahuje další témata pro řešení potíží.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-116">Lists additional troubleshooting topics.</span></span>

## <a name="app-startup-errors"></a><span data-ttu-id="c9a8b-117">Chyby při spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="c9a8b-117">App startup errors</span></span>

<span data-ttu-id="c9a8b-118">V sadě Visual Studio, ASP.NET projektu Core výchozí [iis express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hostování během ladění.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-118">In Visual Studio, an ASP.NET Core project defaults to [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hosting during debugging.</span></span> <span data-ttu-id="c9a8b-119">A *502.5 - Selhání procesu* nebo *500.30 - Start Selhání,* ke kterému dochází při ladění místně lze diagnostikovat pomocí rady v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-119">A *502.5 - Process Failure* or a *500.30 - Start Failure* that occurs when debugging locally can be diagnosed using the advice in this topic.</span></span>

### <a name="40314-forbidden"></a><span data-ttu-id="c9a8b-120">403.14 Zakázáno</span><span class="sxs-lookup"><span data-stu-id="c9a8b-120">403.14 Forbidden</span></span>

<span data-ttu-id="c9a8b-121">Aplikace se nespustí.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-121">The app fails to start.</span></span> <span data-ttu-id="c9a8b-122">Je zaznamenána následující chyba:</span><span class="sxs-lookup"><span data-stu-id="c9a8b-122">The following error is logged:</span></span>

```
The Web server is configured to not list the contents of this directory.
```

<span data-ttu-id="c9a8b-123">Chyba je obvykle způsobena přerušeným nasazením v hostitelském systému, který zahrnuje některý z následujících scénářů:</span><span class="sxs-lookup"><span data-stu-id="c9a8b-123">The error is usually caused by a broken deployment on the hosting system, which includes any of the following scenarios:</span></span>

* <span data-ttu-id="c9a8b-124">Aplikace je nasazena do nesprávné složky v hostitelském systému.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-124">The app is deployed to the wrong folder on the hosting system.</span></span>
* <span data-ttu-id="c9a8b-125">Procesu nasazení se nepodařilo přesunout všechny soubory a složky aplikace do složky nasazení v hostitelském systému.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-125">The deployment process failed to move all of the app's files and folders to the deployment folder on the hosting system.</span></span>
* <span data-ttu-id="c9a8b-126">Soubor *web.config* chybí v nasazení nebo je obsah souboru *web.config* poškozen.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-126">The *web.config* file is missing from the deployment, or the *web.config* file contents are malformed.</span></span>

<span data-ttu-id="c9a8b-127">Proveďte následující kroky:</span><span class="sxs-lookup"><span data-stu-id="c9a8b-127">Perform the following steps:</span></span>

1. <span data-ttu-id="c9a8b-128">Odstraňte všechny soubory a složky ze složky nasazení v hostitelském systému.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-128">Delete all of the files and folders from the deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="c9a8b-129">Znovu nasadit obsah složky *publikování* aplikace do hostitelského systému pomocí normální metody nasazení, jako je Visual Studio, PowerShell nebo ruční nasazení:</span><span class="sxs-lookup"><span data-stu-id="c9a8b-129">Redeploy the contents of the app's *publish* folder to the hosting system using your normal method of deployment, such as Visual Studio, PowerShell, or manual deployment:</span></span>
   * <span data-ttu-id="c9a8b-130">Zkontrolujte, zda je soubor *web.config* v nasazení přítomen a zda je jeho obsah správný.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-130">Confirm that the *web.config* file is present in the deployment and that its contents are correct.</span></span>
   * <span data-ttu-id="c9a8b-131">Při hostování ve službě Azure App Service zkontrolujte, že se aplikace nasadí do `D:\home\site\wwwroot` složky.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-131">When hosting on Azure App Service, confirm that the app is deployed to the `D:\home\site\wwwroot` folder.</span></span>
   * <span data-ttu-id="c9a8b-132">Pokud je aplikace hostována službou IIS, zkontrolujte, zda je aplikace nasazena do **fyzické cesty** služby IIS zobrazené v **základním nastavení** **správce služby IIS**.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-132">When the app is hosted by IIS, confirm that the app is deployed to the IIS **Physical path** shown in **IIS Manager**'s **Basic Settings**.</span></span>
1. <span data-ttu-id="c9a8b-133">Zkontrolujte, zda jsou všechny soubory a složky aplikace nasazeny porovnáním nasazení v hostitelském systému s obsahem složky *publikování* projektu.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-133">Confirm that all of the app's files and folders are deployed by comparing the deployment on the hosting system to the contents of the project's *publish* folder.</span></span>

<span data-ttu-id="c9a8b-134">Další informace o rozložení publikované aplikace ASP.NET <xref:host-and-deploy/directory-structure>Core najdete v tématu .</span><span class="sxs-lookup"><span data-stu-id="c9a8b-134">For more information on the layout of a published ASP.NET Core app, see <xref:host-and-deploy/directory-structure>.</span></span> <span data-ttu-id="c9a8b-135">Další informace o souboru *web.config* naleznete v tématu <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-135">For more information on the *web.config* file, see <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>.</span></span>

### <a name="500-internal-server-error"></a><span data-ttu-id="c9a8b-136">500 – Interní chyba serveru</span><span class="sxs-lookup"><span data-stu-id="c9a8b-136">500 Internal Server Error</span></span>

<span data-ttu-id="c9a8b-137">Aplikace se spustí, ale chyba zabrání serveru v plnění požadavku.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-137">The app starts, but an error prevents the server from fulfilling the request.</span></span>

<span data-ttu-id="c9a8b-138">K této chybě dochází v kódu aplikace při spuštění nebo při vytváření odpovědi.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-138">This error occurs within the app's code during startup or while creating a response.</span></span> <span data-ttu-id="c9a8b-139">Odpověď může obsahovat žádný obsah nebo odpověď může zobrazit jako *500 Vnitřní chyba serveru* v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-139">The response may contain no content, or the response may appear as a *500 Internal Server Error* in the browser.</span></span> <span data-ttu-id="c9a8b-140">Protokol událostí aplikace obvykle uvádí, že aplikace byla spuštěna normálně.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-140">The Application Event Log usually states that the app started normally.</span></span> <span data-ttu-id="c9a8b-141">Z pohledu serveru je to správné.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-141">From the server's perspective, that's correct.</span></span> <span data-ttu-id="c9a8b-142">Aplikace se spustila, ale nemůže generovat platnou odpověď.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-142">The app did start, but it can't generate a valid response.</span></span> <span data-ttu-id="c9a8b-143">Spusťte aplikaci na příkazovém řádku na serveru nebo povolte protokol stdout ASP.NET Core Module k řešení problému.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-143">Run the app at a command prompt on the server or enable the ASP.NET Core Module stdout log to troubleshoot the problem.</span></span>

### <a name="5000-in-process-handler-load-failure"></a><span data-ttu-id="c9a8b-144">Selhání zatížení obslužné rutiny procesu 500.0</span><span class="sxs-lookup"><span data-stu-id="c9a8b-144">500.0 In-Process Handler Load Failure</span></span>

<span data-ttu-id="c9a8b-145">Pracovní proces se nezdaří.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-145">The worker process fails.</span></span> <span data-ttu-id="c9a8b-146">Aplikace se nespustí.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-146">The app doesn't start.</span></span>

<span data-ttu-id="c9a8b-147">Došlo k neznámé chybě načítání ASP.NET součástmi [základního modulu.](xref:host-and-deploy/aspnet-core-module)</span><span class="sxs-lookup"><span data-stu-id="c9a8b-147">An unknown error occurred loading [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) components.</span></span> <span data-ttu-id="c9a8b-148">Proveďte jednu z následujících akcí:</span><span class="sxs-lookup"><span data-stu-id="c9a8b-148">Take one of the following actions:</span></span>

* <span data-ttu-id="c9a8b-149">Obraťte se [na podporu společnosti Microsoft](https://support.microsoft.com/oas/default.aspx?prid=15832) (vyberte nástroje pro **vývojáře** **a potom ASP.NET jádrem).**</span><span class="sxs-lookup"><span data-stu-id="c9a8b-149">Contact [Microsoft Support](https://support.microsoft.com/oas/default.aspx?prid=15832) (select **Developer Tools** then **ASP.NET Core**).</span></span>
* <span data-ttu-id="c9a8b-150">Zeptejte se na Stack Přetečení.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-150">Ask a question on Stack Overflow.</span></span>
* <span data-ttu-id="c9a8b-151">Posuzte problém na našem [úložišti GitHub](https://github.com/dotnet/AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="c9a8b-151">File an issue on our [GitHub repository](https://github.com/dotnet/AspNetCore).</span></span>

### <a name="50030-in-process-startup-failure"></a><span data-ttu-id="c9a8b-152">500.30 Selhání spuštění v průběhu procesu</span><span class="sxs-lookup"><span data-stu-id="c9a8b-152">500.30 In-Process Startup Failure</span></span>

<span data-ttu-id="c9a8b-153">Pracovní proces se nezdaří.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-153">The worker process fails.</span></span> <span data-ttu-id="c9a8b-154">Aplikace se nespustí.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-154">The app doesn't start.</span></span>

<span data-ttu-id="c9a8b-155">[ASP.NET základní modul](xref:host-and-deploy/aspnet-core-module) se pokusí spustit .NET Core CLR v procesu, ale nepodaří spustit.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-155">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the .NET Core CLR in-process, but it fails to start.</span></span> <span data-ttu-id="c9a8b-156">Příčinu selhání spuštění procesu lze obvykle určit z položek v protokolu událostí aplikace a ASP.NET protokolu stdout základního modulu.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-156">The cause of a process startup failure can usually be determined from entries in the Application Event Log and the ASP.NET Core Module stdout log.</span></span>

<span data-ttu-id="c9a8b-157">Běžné podmínky selhání:</span><span class="sxs-lookup"><span data-stu-id="c9a8b-157">Common failure conditions:</span></span>

* <span data-ttu-id="c9a8b-158">Aplikace je nesprávně nakonfigurována kvůli cílení na verzi sdíleného rozhraní ASP.NET Core, která není k dispozici.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-158">The app is misconfigured due to targeting a version of the ASP.NET Core shared framework that isn't present.</span></span> <span data-ttu-id="c9a8b-159">Zkontrolujte, které verze ASP.NET sdíleného rozhraní Core jsou nainstalovány v cílovém počítači.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-159">Check which versions of the ASP.NET Core shared framework are installed on the target machine.</span></span>
* <span data-ttu-id="c9a8b-160">Použití azure key vault, nedostatek oprávnění k trezoru klíčů.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-160">Using Azure Key Vault, lack of permissions to the Key Vault.</span></span> <span data-ttu-id="c9a8b-161">Zkontrolujte zásady přístupu v cílovém trezoru klíčů a ujistěte se, že jsou udělena správná oprávnění.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-161">Check the access policies in the targeted Key Vault to ensure that the correct permissions are granted.</span></span>

### <a name="50031-ancm-failed-to-find-native-dependencies"></a><span data-ttu-id="c9a8b-162">500.31 ANCM se nepodařilo najít nativní závislosti</span><span class="sxs-lookup"><span data-stu-id="c9a8b-162">500.31 ANCM Failed to Find Native Dependencies</span></span>

<span data-ttu-id="c9a8b-163">Pracovní proces se nezdaří.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-163">The worker process fails.</span></span> <span data-ttu-id="c9a8b-164">Aplikace se nespustí.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-164">The app doesn't start.</span></span>

<span data-ttu-id="c9a8b-165">Základní [modul ASP.NET](xref:host-and-deploy/aspnet-core-module) pokusí spustit modul .NET Core v procesu, ale nepodaří se jej spustit.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-165">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the .NET Core runtime in-process, but it fails to start.</span></span> <span data-ttu-id="c9a8b-166">Nejčastější příčinou tohoto selhání při spuštění `Microsoft.NETCore.App` `Microsoft.AspNetCore.App` je, když není nainstalován nebo runtime.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-166">The most common cause of this startup failure is when the `Microsoft.NETCore.App` or `Microsoft.AspNetCore.App` runtime isn't installed.</span></span> <span data-ttu-id="c9a8b-167">Pokud se aplikace nasadí na cílovou ASP.NET Core 3.0 a tato verze v počítači neexistuje, dojde k této chybě.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-167">If the app is deployed to target ASP.NET Core 3.0 and that version doesn't exist on the machine, this error occurs.</span></span> <span data-ttu-id="c9a8b-168">Následuje ukázková chybová zpráva:</span><span class="sxs-lookup"><span data-stu-id="c9a8b-168">An example error message follows:</span></span>

```
The specified framework 'Microsoft.NETCore.App', version '3.0.0' was not found.
  - The following frameworks were found:
      2.2.1 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview5-27626-15 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27713-13 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27714-15 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27723-08 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
```

<span data-ttu-id="c9a8b-169">Chybová zpráva obsahuje seznam všech nainstalovaných verzí rozhraní .NET Core a verze požadované aplikací.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-169">The error message lists all the installed .NET Core versions and the version requested by the app.</span></span> <span data-ttu-id="c9a8b-170">Chcete-li tuto chybu opravit, buď:</span><span class="sxs-lookup"><span data-stu-id="c9a8b-170">To fix this error, either:</span></span>

* <span data-ttu-id="c9a8b-171">Nainstalujte do počítače příslušnou verzi rozhraní .NET Core.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-171">Install the appropriate version of .NET Core on the machine.</span></span>
* <span data-ttu-id="c9a8b-172">Změňte aplikaci tak, aby cílila na verzi .NET Core, která je v počítači k dispozici.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-172">Change the app to target a version of .NET Core that's present on the machine.</span></span>
* <span data-ttu-id="c9a8b-173">Publikujte aplikaci jako [samostatné nasazení](/dotnet/core/deploying/#self-contained-deployments-scd).</span><span class="sxs-lookup"><span data-stu-id="c9a8b-173">Publish the app as a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>

<span data-ttu-id="c9a8b-174">Při spuštění ve `ASPNETCORE_ENVIRONMENT` vývoji (proměnná prostředí je nastavena na `Development`) konkrétní chyba je zapsána do odpovědi HTTP.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-174">When running in development (the `ASPNETCORE_ENVIRONMENT` environment variable is set to `Development`), the specific error is written to the HTTP response.</span></span> <span data-ttu-id="c9a8b-175">Příčina selhání spuštění procesu je také nalezena v protokolu událostí aplikace.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-175">The cause of a process startup failure is also found in the Application Event Log.</span></span>

### <a name="50032-ancm-failed-to-load-dll"></a><span data-ttu-id="c9a8b-176">500.32 ANCM se nepodařilo načíst dll</span><span class="sxs-lookup"><span data-stu-id="c9a8b-176">500.32 ANCM Failed to Load dll</span></span>

<span data-ttu-id="c9a8b-177">Pracovní proces se nezdaří.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-177">The worker process fails.</span></span> <span data-ttu-id="c9a8b-178">Aplikace se nespustí.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-178">The app doesn't start.</span></span>

<span data-ttu-id="c9a8b-179">Nejčastější příčinou této chyby je, že aplikace je publikována pro nekompatibilní architekturu procesoru.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-179">The most common cause for this error is that the app is published for an incompatible processor architecture.</span></span> <span data-ttu-id="c9a8b-180">Pokud pracovní proces běží jako 32bitová aplikace a aplikace byla publikována na cíl 64bit, dojde k této chybě.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-180">If the worker process is running as a 32-bit app and the app was published to target 64-bit, this error occurs.</span></span>

<span data-ttu-id="c9a8b-181">Chcete-li tuto chybu opravit, buď:</span><span class="sxs-lookup"><span data-stu-id="c9a8b-181">To fix this error, either:</span></span>

* <span data-ttu-id="c9a8b-182">Znovu publikujte aplikaci pro stejnou architekturu procesoru jako pracovní proces.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-182">Republish the app for the same processor architecture as the worker process.</span></span>
* <span data-ttu-id="c9a8b-183">Publikujte aplikaci jako [nasazení závislé na rámci](/dotnet/core/deploying/#framework-dependent-executables-fde).</span><span class="sxs-lookup"><span data-stu-id="c9a8b-183">Publish the app as a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-executables-fde).</span></span>

### <a name="50033-ancm-request-handler-load-failure"></a><span data-ttu-id="c9a8b-184">500.33 Ancm Request Handler Selhání zatížení</span><span class="sxs-lookup"><span data-stu-id="c9a8b-184">500.33 ANCM Request Handler Load Failure</span></span>

<span data-ttu-id="c9a8b-185">Pracovní proces se nezdaří.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-185">The worker process fails.</span></span> <span data-ttu-id="c9a8b-186">Aplikace se nespustí.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-186">The app doesn't start.</span></span>

<span data-ttu-id="c9a8b-187">Aplikace neodkazovala na `Microsoft.AspNetCore.App` rozhraní.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-187">The app didn't reference the `Microsoft.AspNetCore.App` framework.</span></span> <span data-ttu-id="c9a8b-188">ASP.NET core `Microsoft.AspNetCore.App` modulu může hostovat pouze aplikace, které cílí na [architekturu](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="c9a8b-188">Only apps targeting the `Microsoft.AspNetCore.App` framework can be hosted by the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span>

<span data-ttu-id="c9a8b-189">Chcete-li tuto chybu opravit, zkontrolujte, zda aplikace cílí na architekturu. `Microsoft.AspNetCore.App`</span><span class="sxs-lookup"><span data-stu-id="c9a8b-189">To fix this error, confirm that the app is targeting the `Microsoft.AspNetCore.App` framework.</span></span> <span data-ttu-id="c9a8b-190">Zkontrolujte, `.runtimeconfig.json` zda chcete ověřit rámec, na který se aplikace zaměřuje.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-190">Check the `.runtimeconfig.json` to verify the framework targeted by the app.</span></span>

### <a name="50034-ancm-mixed-hosting-models-not-supported"></a><span data-ttu-id="c9a8b-191">500.34 CM smíšené hostingové modely nejsou podporovány</span><span class="sxs-lookup"><span data-stu-id="c9a8b-191">500.34 ANCM Mixed Hosting Models Not Supported</span></span>

<span data-ttu-id="c9a8b-192">Pracovní proces nemůže spustit v procesu aplikace a mimo proces aplikace ve stejném procesu.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-192">The worker process can't run both an in-process app and an out-of-process app in the same process.</span></span>

<span data-ttu-id="c9a8b-193">Chcete-li tuto chybu opravit, spouštějte aplikace v samostatných fondech aplikací služby IIS.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-193">To fix this error, run apps in separate IIS application pools.</span></span>

### <a name="50035-ancm-multiple-in-process-applications-in-same-process"></a><span data-ttu-id="c9a8b-194">500.35 ANCM více in-process aplikací ve stejném procesu</span><span class="sxs-lookup"><span data-stu-id="c9a8b-194">500.35 ANCM Multiple In-Process Applications in same Process</span></span>

<span data-ttu-id="c9a8b-195">Pracovní proces nemůže ve stejném procesu spouštět více aplikací v procesu.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-195">The worker process can't run multiple in-process apps in the same process.</span></span>

<span data-ttu-id="c9a8b-196">Chcete-li tuto chybu opravit, spouštějte aplikace v samostatných fondech aplikací služby IIS.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-196">To fix this error, run apps in separate IIS application pools.</span></span>

### <a name="50036-ancm-out-of-process-handler-load-failure"></a><span data-ttu-id="c9a8b-197">500.36 ANCM selhání zatížení obslužné rutiny</span><span class="sxs-lookup"><span data-stu-id="c9a8b-197">500.36 ANCM Out-Of-Process Handler Load Failure</span></span>

<span data-ttu-id="c9a8b-198">Obslužná rutina požadavku mimo proces *aspnetcorev2_outofprocess.dll*není vedle souboru *aspnetcorev2.dll.*</span><span class="sxs-lookup"><span data-stu-id="c9a8b-198">The out-of-process request handler, *aspnetcorev2_outofprocess.dll*, isn't next to the *aspnetcorev2.dll* file.</span></span> <span data-ttu-id="c9a8b-199">To znamená poškozenou instalaci [ASP.NET základního modulu](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="c9a8b-199">This indicates a corrupted installation of the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span>

<span data-ttu-id="c9a8b-200">Chcete-li tuto chybu opravit, opravte instalaci [sady .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) (pro službu IIS) nebo sady Visual Studio (pro službu IIS Express).</span><span class="sxs-lookup"><span data-stu-id="c9a8b-200">To fix this error, repair the installation of the [.NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) (for IIS) or Visual Studio (for IIS Express).</span></span>

### <a name="50037-ancm-failed-to-start-within-startup-time-limit"></a><span data-ttu-id="c9a8b-201">500.37 ANCM se nepodařilo spustit v rámci časového limitu spuštění</span><span class="sxs-lookup"><span data-stu-id="c9a8b-201">500.37 ANCM Failed to Start Within Startup Time Limit</span></span>

<span data-ttu-id="c9a8b-202">Ancm se nepodařilo spustit v rámci provied čas spuštění časového limitu.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-202">ANCM failed to start within the provied startup time limit.</span></span> <span data-ttu-id="c9a8b-203">Ve výchozím nastavení je časový limit 120 sekund.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-203">By default, the timeout is 120 seconds.</span></span>

<span data-ttu-id="c9a8b-204">K této chybě může dojít při spuštění velkého počtu aplikací ve stejném počítači.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-204">This error can occur when starting a large number of apps on the same machine.</span></span> <span data-ttu-id="c9a8b-205">Při spuštění zkontrolujte špičky využití procesoru a paměti na serveru.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-205">Check for CPU/Memory usage spikes on the server during startup.</span></span> <span data-ttu-id="c9a8b-206">Možná budete muset rozložit proces spuštění více aplikací.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-206">You may need to stagger the startup process of multiple apps.</span></span>

### <a name="50038-ancm-application-dll-not-found"></a><span data-ttu-id="c9a8b-207">500.38 DLL aplikace ANCM nebyla nalezena</span><span class="sxs-lookup"><span data-stu-id="c9a8b-207">500.38 ANCM Application DLL Not Found</span></span>

<span data-ttu-id="c9a8b-208">ANCM se nepodařilo najít dll aplikace, která by měla být vedle spustitelného souboru.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-208">ANCM failed to locate the application DLL, which should be next to the executable.</span></span>

<span data-ttu-id="c9a8b-209">K této chybě dochází při hostování aplikace zabalené jako [spustitelný soubor s jedním souborem](/dotnet/core/whats-new/dotnet-core-3-0#single-file-executables) pomocí modelu hostování v procesu.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-209">This error occurs when hosting an app packaged as a [single-file executable](/dotnet/core/whats-new/dotnet-core-3-0#single-file-executables) using the in-process hosting model.</span></span> <span data-ttu-id="c9a8b-210">V procesu model vyžaduje, aby ANCM načíst .NET Core aplikace do existujícího procesu IIS.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-210">The in-process model requires that the ANCM load the .NET Core app into the existing IIS process.</span></span> <span data-ttu-id="c9a8b-211">Tento scénář není podporován modelem nasazení s jedním souborem.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-211">This scenario isn't supported by the single-file deployment model.</span></span> <span data-ttu-id="c9a8b-212">K opravě této chyby použijte **jeden** z následujících přístupů v souboru projektu aplikace:</span><span class="sxs-lookup"><span data-stu-id="c9a8b-212">Use **one** of the following approaches in the app's project file to fix this error:</span></span>

1. <span data-ttu-id="c9a8b-213">Zakázání publikování jednoho souboru nastavením `PublishSingleFile` `false`vlastnosti MSBuild na .</span><span class="sxs-lookup"><span data-stu-id="c9a8b-213">Disable single-file publishing by setting the `PublishSingleFile` MSBuild property to `false`.</span></span>
1. <span data-ttu-id="c9a8b-214">Přepněte na mimoprocesový model hostování `AspNetCoreHostingModel` nastavením vlastnosti `OutOfProcess`MSBuild na .</span><span class="sxs-lookup"><span data-stu-id="c9a8b-214">Switch to the out-of-process hosting model by setting the `AspNetCoreHostingModel` MSBuild property to `OutOfProcess`.</span></span>

### <a name="5025-process-failure"></a><span data-ttu-id="c9a8b-215">502.5 – Selhání procesu</span><span class="sxs-lookup"><span data-stu-id="c9a8b-215">502.5 Process Failure</span></span>

<span data-ttu-id="c9a8b-216">Pracovní proces se nezdaří.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-216">The worker process fails.</span></span> <span data-ttu-id="c9a8b-217">Aplikace se nespustí.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-217">The app doesn't start.</span></span>

<span data-ttu-id="c9a8b-218">Základní [modul ASP.NET](xref:host-and-deploy/aspnet-core-module) se pokusí spustit pracovní proces, ale nepodaří se jej spustit.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-218">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the worker process but it fails to start.</span></span> <span data-ttu-id="c9a8b-219">Příčinu selhání spuštění procesu lze obvykle určit z položek v protokolu událostí aplikace a ASP.NET protokolu stdout základního modulu.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-219">The cause of a process startup failure can usually be determined from entries in the Application Event Log and the ASP.NET Core Module stdout log.</span></span>

<span data-ttu-id="c9a8b-220">Obvyklá podmínka selhání je, že aplikace je nesprávně nakonfigurovaná kvůli cílení na verzi sdíleného rozhraní ASP.NET Core, která není k dispozici.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-220">A common failure condition is the app is misconfigured due to targeting a version of the ASP.NET Core shared framework that isn't present.</span></span> <span data-ttu-id="c9a8b-221">Zkontrolujte, které verze ASP.NET sdíleného rozhraní Core jsou nainstalovány v cílovém počítači.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-221">Check which versions of the ASP.NET Core shared framework are installed on the target machine.</span></span> <span data-ttu-id="c9a8b-222">Sdílená *architektura* je sada sestavení (*soubory DLL),* které jsou nainstalovány v počítači a odkazovány metabalíčkem, například `Microsoft.AspNetCore.App`.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-222">The *shared framework* is the set of assemblies (*.dll* files) that are installed on the machine and referenced by a metapackage such as `Microsoft.AspNetCore.App`.</span></span> <span data-ttu-id="c9a8b-223">Odkaz na metabalíček může určit minimální požadovanou verzi.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-223">The metapackage reference can specify a minimum required version.</span></span> <span data-ttu-id="c9a8b-224">Další informace naleznete [v tématu sdílený rámec](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span><span class="sxs-lookup"><span data-stu-id="c9a8b-224">For more information, see [The shared framework](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span></span>

<span data-ttu-id="c9a8b-225">Chybová stránka *502.5 Process Failure* je vrácena, když selhání konfigurace hostitele nebo aplikace způsobí selhání pracovního procesu:</span><span class="sxs-lookup"><span data-stu-id="c9a8b-225">The *502.5 Process Failure* error page is returned when a hosting or app misconfiguration causes the worker process to fail:</span></span>

### <a name="failed-to-start-application-errorcode-0x800700c1"></a><span data-ttu-id="c9a8b-226">Spuštění aplikace se nezdařilo (ErrorCode '0x800700c1')</span><span class="sxs-lookup"><span data-stu-id="c9a8b-226">Failed to start application (ErrorCode '0x800700c1')</span></span>

```
EventID: 1010
Source: IIS AspNetCore Module V2
Failed to start application '/LM/W3SVC/6/ROOT/', ErrorCode '0x800700c1'.
```

<span data-ttu-id="c9a8b-227">Spuštění aplikace se nezdařilo, protože nebylo možné načíst sestavení aplikace (*DLL*).</span><span class="sxs-lookup"><span data-stu-id="c9a8b-227">The app failed to start because the app's assembly (*.dll*) couldn't be loaded.</span></span>

<span data-ttu-id="c9a8b-228">K této chybě dochází, pokud je neodpovídající bitness mezi publikovanou aplikací a w3wp/iisexpress procesu.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-228">This error occurs when there's a bitness mismatch between the published app and the w3wp/iisexpress process.</span></span>

<span data-ttu-id="c9a8b-229">Zkontrolujte, zda je 32bitové nastavení fondu aplikací správné:</span><span class="sxs-lookup"><span data-stu-id="c9a8b-229">Confirm that the app pool's 32-bit setting is correct:</span></span>

1. <span data-ttu-id="c9a8b-230">Vyberte fond aplikací ve **fondech aplikací**Správce služby IIS .</span><span class="sxs-lookup"><span data-stu-id="c9a8b-230">Select the app pool in IIS Manager's **Application Pools**.</span></span>
1. <span data-ttu-id="c9a8b-231">V části Upravit fond aplikací v panelu **Akce** vyberte **Upřesnit nastavení** v části Upravit **fond aplikací.**</span><span class="sxs-lookup"><span data-stu-id="c9a8b-231">Select **Advanced Settings** under **Edit Application Pool** in the **Actions** panel.</span></span>
1. <span data-ttu-id="c9a8b-232">Nastavit **povolit 32bitové aplikace**:</span><span class="sxs-lookup"><span data-stu-id="c9a8b-232">Set **Enable 32-Bit Applications**:</span></span>
   * <span data-ttu-id="c9a8b-233">Pokud nasazujete 32bitovou (x86) `True`aplikaci, nastavte hodnotu na .</span><span class="sxs-lookup"><span data-stu-id="c9a8b-233">If deploying a 32-bit (x86) app, set the value to `True`.</span></span>
   * <span data-ttu-id="c9a8b-234">Pokud nasazujete 64bitovou (x64) `False`aplikaci, nastavte hodnotu na .</span><span class="sxs-lookup"><span data-stu-id="c9a8b-234">If deploying a 64-bit (x64) app, set the value to `False`.</span></span>

<span data-ttu-id="c9a8b-235">Zkontrolujte, zda v souboru `<Platform>` projektu není konflikt mezi vlastností MSBuild a publikovanou bitness aplikace.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-235">Confirm that there isn't a conflict between a `<Platform>` MSBuild property in the project file and the published bitness of the app.</span></span>

### <a name="connection-reset"></a><span data-ttu-id="c9a8b-236">Resetování připojení</span><span class="sxs-lookup"><span data-stu-id="c9a8b-236">Connection reset</span></span>

<span data-ttu-id="c9a8b-237">Pokud dojde k chybě po odeslání záhlaví, je příliš pozdě na to, aby server odeslal **chybu interního serveru 500,** když dojde k chybě.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-237">If an error occurs after the headers are sent, it's too late for the server to send a **500 Internal Server Error** when an error occurs.</span></span> <span data-ttu-id="c9a8b-238">K tomu často dochází, když dojde k chybě během serializace složitých objektů pro odpověď.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-238">This often happens when an error occurs during the serialization of complex objects for a response.</span></span> <span data-ttu-id="c9a8b-239">Tento typ chyby se zobrazí jako chyba *obnovení připojení* v klientovi.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-239">This type of error appears as a *connection reset* error on the client.</span></span> <span data-ttu-id="c9a8b-240">[Protokolování aplikací](xref:fundamentals/logging/index) může pomoci při řešení těchto typů chyb.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-240">[Application logging](xref:fundamentals/logging/index) can help troubleshoot these types of errors.</span></span>

### <a name="default-startup-limits"></a><span data-ttu-id="c9a8b-241">Výchozí limity pro spuštění</span><span class="sxs-lookup"><span data-stu-id="c9a8b-241">Default startup limits</span></span>

<span data-ttu-id="c9a8b-242">[Základní modul ASP.NET](xref:host-and-deploy/aspnet-core-module) je nakonfigurován s výchozím *spouštěcím limitem* 120 sekund.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-242">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) is configured with a default *startupTimeLimit* of 120 seconds.</span></span> <span data-ttu-id="c9a8b-243">Pokud zůstane na výchozí hodnotu, aplikace může trvat až dvě minuty ke spuštění, než modul zaznamená selhání procesu.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-243">When left at the default value, an app may take up to two minutes to start before the module logs a process failure.</span></span> <span data-ttu-id="c9a8b-244">Informace o konfiguraci modulu naleznete v [tématu Atributy prvku aspNetCore](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span><span class="sxs-lookup"><span data-stu-id="c9a8b-244">For information on configuring the module, see [Attributes of the aspNetCore element](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span></span>

## <a name="troubleshoot-on-azure-app-service"></a><span data-ttu-id="c9a8b-245">Poradce při potížích se službou Azure App Service</span><span class="sxs-lookup"><span data-stu-id="c9a8b-245">Troubleshoot on Azure App Service</span></span>

[!INCLUDE [Azure App Service Preview Notice](~/includes/azure-apps-preview-notice.md)]

### <a name="application-event-log-azure-app-service"></a><span data-ttu-id="c9a8b-246">Protokol událostí aplikace (služba Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="c9a8b-246">Application Event Log (Azure App Service)</span></span>

<span data-ttu-id="c9a8b-247">Chcete-li získat přístup k protokolu událostí aplikace, použijte okno **Diagnostikovat a vyřešit problémy** na webu Azure Portal:</span><span class="sxs-lookup"><span data-stu-id="c9a8b-247">To access the Application Event Log, use the **Diagnose and solve problems** blade in the Azure portal:</span></span>

1. <span data-ttu-id="c9a8b-248">Na webu Azure Portal otevřete aplikaci ve **Službách aplikací**.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-248">In the Azure portal, open the app in **App Services**.</span></span>
1. <span data-ttu-id="c9a8b-249">Vyberte **Diagnostikovat a řešit problémy**.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-249">Select **Diagnose and solve problems**.</span></span>
1. <span data-ttu-id="c9a8b-250">Vyberte nadpis **Diagnostické nástroje.**</span><span class="sxs-lookup"><span data-stu-id="c9a8b-250">Select the **Diagnostic Tools** heading.</span></span>
1. <span data-ttu-id="c9a8b-251">V části **Nástroje podpory**vyberte tlačítko **Události aplikace.**</span><span class="sxs-lookup"><span data-stu-id="c9a8b-251">Under **Support Tools**, select the **Application Events** button.</span></span>
1. <span data-ttu-id="c9a8b-252">Prohlédněte si nejnovější chybu poskytovanou položkou *AspNetCoreModule* nebo *IIS AspNetCoreModule V2* ve sloupci **Zdroj.**</span><span class="sxs-lookup"><span data-stu-id="c9a8b-252">Examine the latest error provided by the *IIS AspNetCoreModule* or *IIS AspNetCoreModule V2* entry in the **Source** column.</span></span>

<span data-ttu-id="c9a8b-253">Alternativou k použití **funkce Diagnostika a řešení problémů** je prozkoumat soubor protokolu událostí aplikace přímo pomocí [kudu](https://github.com/projectkudu/kudu/wiki):</span><span class="sxs-lookup"><span data-stu-id="c9a8b-253">An alternative to using the **Diagnose and solve problems** blade is to examine the Application Event Log file directly using [Kudu](https://github.com/projectkudu/kudu/wiki):</span></span>

1. <span data-ttu-id="c9a8b-254">Otevřete **rozšířené nástroje** v oblasti **vývojových nástrojů.**</span><span class="sxs-lookup"><span data-stu-id="c9a8b-254">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="c9a8b-255">Vyberte tlačítko \*\*Přejít.&rarr; \*\*</span><span class="sxs-lookup"><span data-stu-id="c9a8b-255">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="c9a8b-256">Konzole Kudu se otevře v novém kartě prohlížeče nebo v okně.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-256">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="c9a8b-257">Pomocí navigačního panelu v horní části stránky otevřete **konzolu ladění** a vyberte **možnost CMD**.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-257">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="c9a8b-258">Otevřete složku **LogFiles.**</span><span class="sxs-lookup"><span data-stu-id="c9a8b-258">Open the **LogFiles** folder.</span></span>
1. <span data-ttu-id="c9a8b-259">Vyberte ikonu tužky vedle souboru *eventlog.xml.*</span><span class="sxs-lookup"><span data-stu-id="c9a8b-259">Select the pencil icon next to the *eventlog.xml* file.</span></span>
1. <span data-ttu-id="c9a8b-260">Prohlédněte si protokol.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-260">Examine the log.</span></span> <span data-ttu-id="c9a8b-261">Přejděte do dolní části protokolu zobrazíte nejnovější události.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-261">Scroll to the bottom of the log to see the most recent events.</span></span>

### <a name="run-the-app-in-the-kudu-console"></a><span data-ttu-id="c9a8b-262">Spuštění aplikace v konzoli Kudu</span><span class="sxs-lookup"><span data-stu-id="c9a8b-262">Run the app in the Kudu console</span></span>

<span data-ttu-id="c9a8b-263">Mnoho chyb při spuštění nevytváří užitečné informace v protokolu událostí aplikace.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-263">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="c9a8b-264">Aplikaci můžete spustit v konzole vzdáleného spuštění [Kudu](https://github.com/projectkudu/kudu/wiki) a zjistit chybu:</span><span class="sxs-lookup"><span data-stu-id="c9a8b-264">You can run the app in the [Kudu](https://github.com/projectkudu/kudu/wiki) Remote Execution Console to discover the error:</span></span>

1. <span data-ttu-id="c9a8b-265">Otevřete **rozšířené nástroje** v oblasti **vývojových nástrojů.**</span><span class="sxs-lookup"><span data-stu-id="c9a8b-265">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="c9a8b-266">Vyberte tlačítko \*\*Přejít.&rarr; \*\*</span><span class="sxs-lookup"><span data-stu-id="c9a8b-266">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="c9a8b-267">Konzole Kudu se otevře v novém kartě prohlížeče nebo v okně.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-267">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="c9a8b-268">Pomocí navigačního panelu v horní části stránky otevřete **konzolu ladění** a vyberte **možnost CMD**.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-268">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>

#### <a name="test-a-32-bit-x86-app"></a><span data-ttu-id="c9a8b-269">Testování 32bitové aplikace (x86)</span><span class="sxs-lookup"><span data-stu-id="c9a8b-269">Test a 32-bit (x86) app</span></span>

<span data-ttu-id="c9a8b-270">**Aktuální verze**</span><span class="sxs-lookup"><span data-stu-id="c9a8b-270">**Current release**</span></span>

1. `cd d:\home\site\wwwroot`
1. <span data-ttu-id="c9a8b-271">Spusťte aplikaci:</span><span class="sxs-lookup"><span data-stu-id="c9a8b-271">Run the app:</span></span>
   * <span data-ttu-id="c9a8b-272">Pokud je aplikace [nasazení závislé na rámci](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span><span class="sxs-lookup"><span data-stu-id="c9a8b-272">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

     ```dotnetcli
     dotnet .\{ASSEMBLY NAME}.dll
     ```

   * <span data-ttu-id="c9a8b-273">Pokud se jedná o [samostatné nasazení](/dotnet/core/deploying/#self-contained-deployments-scd)aplikace :</span><span class="sxs-lookup"><span data-stu-id="c9a8b-273">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

     ```console
     {ASSEMBLY NAME}.exe
     ```

<span data-ttu-id="c9a8b-274">Výstup konzoly z aplikace, v němž se zobrazují všechny chyby, je předán do konzoly Kudu.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-274">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="c9a8b-275">**Nasazení závislé na rozhraní spuštěné ve verzi preview**</span><span class="sxs-lookup"><span data-stu-id="c9a8b-275">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="c9a8b-276">*Vyžaduje instalaci rozšíření webu ASP.NET Core {VERSION} (x86).*</span><span class="sxs-lookup"><span data-stu-id="c9a8b-276">*Requires installing the ASP.NET Core {VERSION} (x86) Runtime site extension.*</span></span>

1. <span data-ttu-id="c9a8b-277">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32`(`{X.Y}` je runtime verze)</span><span class="sxs-lookup"><span data-stu-id="c9a8b-277">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="c9a8b-278">Spusťte aplikaci: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="c9a8b-278">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="c9a8b-279">Výstup konzoly z aplikace, v němž se zobrazují všechny chyby, je předán do konzoly Kudu.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-279">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

#### <a name="test-a-64-bit-x64-app"></a><span data-ttu-id="c9a8b-280">Testování 64bitové aplikace (x64)</span><span class="sxs-lookup"><span data-stu-id="c9a8b-280">Test a 64-bit (x64) app</span></span>

<span data-ttu-id="c9a8b-281">**Aktuální verze**</span><span class="sxs-lookup"><span data-stu-id="c9a8b-281">**Current release**</span></span>

* <span data-ttu-id="c9a8b-282">Pokud je aplikace 64bitové (x64) [nasazení závislé na rozhraní](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span><span class="sxs-lookup"><span data-stu-id="c9a8b-282">If the app is a 64-bit (x64) [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>
  1. `cd D:\Program Files\dotnet`
  1. <span data-ttu-id="c9a8b-283">Spusťte aplikaci: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="c9a8b-283">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>
* <span data-ttu-id="c9a8b-284">Pokud se jedná o [samostatné nasazení](/dotnet/core/deploying/#self-contained-deployments-scd)aplikace :</span><span class="sxs-lookup"><span data-stu-id="c9a8b-284">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>
  1. `cd D:\home\site\wwwroot`
  1. <span data-ttu-id="c9a8b-285">Spusťte aplikaci: `{ASSEMBLY NAME}.exe`</span><span class="sxs-lookup"><span data-stu-id="c9a8b-285">Run the app: `{ASSEMBLY NAME}.exe`</span></span>

<span data-ttu-id="c9a8b-286">Výstup konzoly z aplikace, v němž se zobrazují všechny chyby, je předán do konzoly Kudu.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-286">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="c9a8b-287">**Nasazení závislé na rozhraní spuštěné ve verzi preview**</span><span class="sxs-lookup"><span data-stu-id="c9a8b-287">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="c9a8b-288">*Vyžaduje instalaci rozšíření webu ASP.NET Core {VERSION} (x64).*</span><span class="sxs-lookup"><span data-stu-id="c9a8b-288">*Requires installing the ASP.NET Core {VERSION} (x64) Runtime site extension.*</span></span>

1. <span data-ttu-id="c9a8b-289">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64`(`{X.Y}` je runtime verze)</span><span class="sxs-lookup"><span data-stu-id="c9a8b-289">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="c9a8b-290">Spusťte aplikaci: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="c9a8b-290">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="c9a8b-291">Výstup konzoly z aplikace, v němž se zobrazují všechny chyby, je předán do konzoly Kudu.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-291">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

### <a name="aspnet-core-module-stdout-log-azure-app-service"></a><span data-ttu-id="c9a8b-292">ASP.NET protokol stdout modulu (služba Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="c9a8b-292">ASP.NET Core Module stdout log (Azure App Service)</span></span>

<span data-ttu-id="c9a8b-293">Protokol stdout ASP.NET Core Module často zaznamenává užitečné chybové zprávy, které nebyly nalezeny v protokolu událostí aplikace.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-293">The ASP.NET Core Module stdout log often records useful error messages not found in the Application Event Log.</span></span> <span data-ttu-id="c9a8b-294">Povolení a zobrazení protokolů stdout:</span><span class="sxs-lookup"><span data-stu-id="c9a8b-294">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="c9a8b-295">Přejděte na okno **Diagnostikovat a vyřešit problémy** na webu Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-295">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="c9a8b-296">V **části SELECT PROBLEM CATEGORY**vyberte tlačítko Web App **Down** .</span><span class="sxs-lookup"><span data-stu-id="c9a8b-296">Under **SELECT PROBLEM CATEGORY**, select the **Web App Down** button.</span></span>
1. <span data-ttu-id="c9a8b-297">V části **Navrhovaná řešení** > **Povolit přesměrování protokolu Stdout**vyberte tlačítko **pro otevření konzoly Kudu a upravte web.Config**.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-297">Under **Suggested Solutions** > **Enable Stdout Log Redirection**, select the button to **Open Kudu Console to edit Web.Config**.</span></span>
1. <span data-ttu-id="c9a8b-298">V **diagnostické konzole**Kudu otevřete složky webu **site** > **wwwroot**.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-298">In the Kudu **Diagnostic Console**, open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="c9a8b-299">Posunutím dolů zobrazíte soubor *web.config* v dolní části seznamu.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-299">Scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="c9a8b-300">Klikněte na ikonu tužky vedle souboru *web.config.*</span><span class="sxs-lookup"><span data-stu-id="c9a8b-300">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="c9a8b-301">Nastavte **stdoutLogEnabled** na `true` cestu **stdoutLogFile** `\\?\%home%\LogFiles\stdout`a změňte ji na: .</span><span class="sxs-lookup"><span data-stu-id="c9a8b-301">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="c9a8b-302">Vyberte **Uložit,** chcete-li uložit aktualizovaný soubor *web.config.*</span><span class="sxs-lookup"><span data-stu-id="c9a8b-302">Select **Save** to save the updated *web.config* file.</span></span>
1. <span data-ttu-id="c9a8b-303">Požádejte o to do aplikace.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-303">Make a request to the app.</span></span>
1. <span data-ttu-id="c9a8b-304">Vraťte se na Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-304">Return to the Azure portal.</span></span> <span data-ttu-id="c9a8b-305">Vyberte okno **Pokročilé nástroje** v oblasti **NÁSTROJE VÝVOJE.**</span><span class="sxs-lookup"><span data-stu-id="c9a8b-305">Select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="c9a8b-306">Vyberte tlačítko \*\*Přejít.&rarr; \*\*</span><span class="sxs-lookup"><span data-stu-id="c9a8b-306">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="c9a8b-307">Konzole Kudu se otevře v novém kartě prohlížeče nebo v okně.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-307">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="c9a8b-308">Pomocí navigačního panelu v horní části stránky otevřete **konzolu ladění** a vyberte **možnost CMD**.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-308">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="c9a8b-309">Vyberte složku **LogFiles.**</span><span class="sxs-lookup"><span data-stu-id="c9a8b-309">Select the **LogFiles** folder.</span></span>
1. <span data-ttu-id="c9a8b-310">Zkontrolujte sloupec **Změněno** a vyberte ikonu tužky a upravte protokol stdout s nejnovějším datem změny.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-310">Inspect the **Modified** column and select the pencil icon to edit the stdout log with the latest modification date.</span></span>
1. <span data-ttu-id="c9a8b-311">Po otevření souboru protokolu se zobrazí chyba.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-311">When the log file opens, the error is displayed.</span></span>

<span data-ttu-id="c9a8b-312">Zakažte protokolování stdout po dokončení řešení potíží:</span><span class="sxs-lookup"><span data-stu-id="c9a8b-312">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="c9a8b-313">V diagnostické **konzole**Kudu se vraťte na **web** > **wwwroot** cesty a odhalte soubor *web.config.*</span><span class="sxs-lookup"><span data-stu-id="c9a8b-313">In the Kudu **Diagnostic Console**, return to the path **site** > **wwwroot** to reveal the *web.config* file.</span></span> <span data-ttu-id="c9a8b-314">Znovu otevřete soubor **web.config** výběrem ikony tužky.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-314">Open the **web.config** file again by selecting the pencil icon.</span></span>
1. <span data-ttu-id="c9a8b-315">Nastavte **stdoutLogEnabled** na `false`.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-315">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="c9a8b-316">Chcete-li soubor uložit, vyberte **Uložit.**</span><span class="sxs-lookup"><span data-stu-id="c9a8b-316">Select **Save** to save the file.</span></span>

<span data-ttu-id="c9a8b-317">Další informace naleznete v tématu <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-317">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

> [!WARNING]
> <span data-ttu-id="c9a8b-318">Selhání zakázání protokolu stdout může vést k selhání aplikace nebo serveru.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-318">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="c9a8b-319">Neexistuje žádné omezení na velikost souboru protokolu nebo počet vytvořených souborů protokolu.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-319">There's no limit on log file size or the number of log files created.</span></span> <span data-ttu-id="c9a8b-320">K řešení problémů se spuštěním aplikace používejte protokolování stdout pouze.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-320">Only use stdout logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="c9a8b-321">Pro obecné protokolování v aplikaci ASP.NET Core po spuštění použijte knihovnu protokolování, která omezuje velikost souboru protokolu a otáčí protokoly.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-321">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="c9a8b-322">Další informace naleznete [v tématu zprostředkovatelé protokolování třetích stran](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="c9a8b-322">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="aspnet-core-module-debug-log-azure-app-service"></a><span data-ttu-id="c9a8b-323">protokol ladění ASP.NET základního modulu (služba Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="c9a8b-323">ASP.NET Core Module debug log (Azure App Service)</span></span>

<span data-ttu-id="c9a8b-324">Protokol ladění ASP.NET core modul poskytuje další, hlubší protokolování z ASP.NET core modulu.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-324">The ASP.NET Core Module debug log provides additional, deeper logging from the ASP.NET Core Module.</span></span> <span data-ttu-id="c9a8b-325">Povolení a zobrazení protokolů stdout:</span><span class="sxs-lookup"><span data-stu-id="c9a8b-325">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="c9a8b-326">Chcete-li povolit rozšířený diagnostický protokol, proveďte jednu z následujících akcí:</span><span class="sxs-lookup"><span data-stu-id="c9a8b-326">To enable the enhanced diagnostic log, perform either of the following:</span></span>
   * <span data-ttu-id="c9a8b-327">Podle pokynů v [rozšířené diagnostické protokoly](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) nakonfigurovat aplikaci pro rozšířené diagnostické protokolování.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-327">Follow the instructions in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) to configure the app for an enhanced diagnostic logging.</span></span> <span data-ttu-id="c9a8b-328">Znovu nasadit aplikaci.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-328">Redeploy the app.</span></span>
   * <span data-ttu-id="c9a8b-329">Přidejte `<handlerSettings>` zobrazené v [rozšířených diagnostických protokolech](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) do souboru *web.config* živé aplikace pomocí konzoly Kudu:</span><span class="sxs-lookup"><span data-stu-id="c9a8b-329">Add the `<handlerSettings>` shown in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) to the live app's *web.config* file using the Kudu console:</span></span>
     1. <span data-ttu-id="c9a8b-330">Otevřete **rozšířené nástroje** v oblasti **vývojových nástrojů.**</span><span class="sxs-lookup"><span data-stu-id="c9a8b-330">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="c9a8b-331">Vyberte tlačítko \*\*Přejít.&rarr; \*\*</span><span class="sxs-lookup"><span data-stu-id="c9a8b-331">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="c9a8b-332">Konzole Kudu se otevře v novém kartě prohlížeče nebo v okně.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-332">The Kudu console opens in a new browser tab or window.</span></span>
     1. <span data-ttu-id="c9a8b-333">Pomocí navigačního panelu v horní části stránky otevřete **konzolu ladění** a vyberte **možnost CMD**.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-333">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
     1. <span data-ttu-id="c9a8b-334">Otevřete složky na **webu** > cesty**wwwroot**.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-334">Open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="c9a8b-335">Upravte soubor *web.config* výběrem tlačítka tužky.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-335">Edit the *web.config* file by selecting the pencil button.</span></span> <span data-ttu-id="c9a8b-336">Přidejte `<handlerSettings>` oddíl, jak je znázorněno v [rozšířené diagnostické protokoly](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs).</span><span class="sxs-lookup"><span data-stu-id="c9a8b-336">Add the `<handlerSettings>` section as shown in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs).</span></span> <span data-ttu-id="c9a8b-337">Vyberte tlačítko **Uložit**.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-337">Select the **Save** button.</span></span>
1. <span data-ttu-id="c9a8b-338">Otevřete **rozšířené nástroje** v oblasti **vývojových nástrojů.**</span><span class="sxs-lookup"><span data-stu-id="c9a8b-338">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="c9a8b-339">Vyberte tlačítko \*\*Přejít.&rarr; \*\*</span><span class="sxs-lookup"><span data-stu-id="c9a8b-339">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="c9a8b-340">Konzole Kudu se otevře v novém kartě prohlížeče nebo v okně.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-340">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="c9a8b-341">Pomocí navigačního panelu v horní části stránky otevřete **konzolu ladění** a vyberte **možnost CMD**.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-341">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="c9a8b-342">Otevřete složky na **webu** > cesty**wwwroot**.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-342">Open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="c9a8b-343">Pokud jste nezadali cestu pro soubor *aspnetcore-debug.log,* soubor se zobrazí v seznamu.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-343">If you didn't supply a path for the *aspnetcore-debug.log* file, the file appears in the list.</span></span> <span data-ttu-id="c9a8b-344">Pokud jste zadali cestu, přejděte do umístění souboru protokolu.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-344">If you supplied a path, navigate to the location of the log file.</span></span>
1. <span data-ttu-id="c9a8b-345">Otevřete soubor protokolu pomocí tlačítka tužky vedle názvu souboru.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-345">Open the log file with the pencil button next to the file name.</span></span>

<span data-ttu-id="c9a8b-346">Zakažte protokolování ladění po dokončení řešení potíží:</span><span class="sxs-lookup"><span data-stu-id="c9a8b-346">Disable debug logging when troubleshooting is complete:</span></span>

<span data-ttu-id="c9a8b-347">Chcete-li protokol rozšířeného ladění zakázat, proveďte některou z následujících akcí:</span><span class="sxs-lookup"><span data-stu-id="c9a8b-347">To disable the enhanced debug log, perform either of the following:</span></span>

* <span data-ttu-id="c9a8b-348">Odeberte `<handlerSettings>` soubor *web.config* místně a znovu nasadit aplikaci.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-348">Remove the `<handlerSettings>` from the *web.config* file locally and redeploy the app.</span></span>
* <span data-ttu-id="c9a8b-349">Pomocí konzoly Kudu upravte soubor *web.config* a odeberte `<handlerSettings>` oddíl.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-349">Use the Kudu console to edit the *web.config* file and remove the `<handlerSettings>` section.</span></span> <span data-ttu-id="c9a8b-350">Uložte soubor.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-350">Save the file.</span></span>

<span data-ttu-id="c9a8b-351">Další informace naleznete v tématu <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-351">For more information, see <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span></span>

> [!WARNING]
> <span data-ttu-id="c9a8b-352">Selhání zakázání protokolu ladění může vést k selhání aplikace nebo serveru.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-352">Failure to disable the debug log can lead to app or server failure.</span></span> <span data-ttu-id="c9a8b-353">Neexistuje žádné omezení na velikost souboru protokolu.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-353">There's no limit on log file size.</span></span> <span data-ttu-id="c9a8b-354">Protokolování ladění používejte pouze k řešení potíží se spuštěním aplikace.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-354">Only use debug logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="c9a8b-355">Pro obecné protokolování v aplikaci ASP.NET Core po spuštění použijte knihovnu protokolování, která omezuje velikost souboru protokolu a otáčí protokoly.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-355">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="c9a8b-356">Další informace naleznete [v tématu zprostředkovatelé protokolování třetích stran](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="c9a8b-356">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="slow-or-hanging-app-azure-app-service"></a><span data-ttu-id="c9a8b-357">Pomalá nebo zavěšená aplikace (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="c9a8b-357">Slow or hanging app (Azure App Service)</span></span>

<span data-ttu-id="c9a8b-358">Když aplikace reaguje pomalu nebo se při žádosti zablokuje, přečtěte si následující články:</span><span class="sxs-lookup"><span data-stu-id="c9a8b-358">When an app responds slowly or hangs on a request, see the following articles:</span></span>

* [<span data-ttu-id="c9a8b-359">Řešení potíží s výkonem pomalých webových aplikací ve službě Azure App Service</span><span class="sxs-lookup"><span data-stu-id="c9a8b-359">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="c9a8b-360">Použití rozšíření webu Diagnostikátor selhání k zachycení výpisu pro občasné výjimky problémy nebo problémy s výkonem na Azure Web App</span><span class="sxs-lookup"><span data-stu-id="c9a8b-360">Use Crash Diagnoser Site Extension to Capture Dump for Intermittent Exception issues or performance issues on Azure Web App</span></span>](https://blogs.msdn.microsoft.com/asiatech/2015/12/28/use-crash-diagnoser-site-extension-to-capture-dump-for-intermittent-exception-issues-or-performance-issues-on-azure-web-app/)

### <a name="monitoring-blades"></a><span data-ttu-id="c9a8b-361">Monitorovací nože</span><span class="sxs-lookup"><span data-stu-id="c9a8b-361">Monitoring blades</span></span>

<span data-ttu-id="c9a8b-362">Monitorování blades poskytují alternativní řešení potíží k metodám popsaným výše v tématu.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-362">Monitoring blades provide an alternative troubleshooting experience to the methods described earlier in the topic.</span></span> <span data-ttu-id="c9a8b-363">Tyto čepele lze použít k diagnostice chyb řady 500.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-363">These blades can be used to diagnose 500-series errors.</span></span>

<span data-ttu-id="c9a8b-364">Zkontrolujte, zda jsou nainstalovány ASP.NET rozšíření core.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-364">Confirm that the ASP.NET Core Extensions are installed.</span></span> <span data-ttu-id="c9a8b-365">Pokud rozšíření nejsou nainstalovaná, nainstalujte je ručně:</span><span class="sxs-lookup"><span data-stu-id="c9a8b-365">If the extensions aren't installed, install them manually:</span></span>

1. <span data-ttu-id="c9a8b-366">V části **Čepele NÁSTROJE PRO VÝVOJ** vyberte okno **Rozšíření.**</span><span class="sxs-lookup"><span data-stu-id="c9a8b-366">In the **DEVELOPMENT TOOLS** blade section, select the **Extensions** blade.</span></span>
1. <span data-ttu-id="c9a8b-367">V seznamu by se měl **zobrazit ASP.NET rozšíření jádra.**</span><span class="sxs-lookup"><span data-stu-id="c9a8b-367">The **ASP.NET Core Extensions** should appear in the list.</span></span>
1. <span data-ttu-id="c9a8b-368">Pokud rozšíření nejsou nainstalovaná, vyberte tlačítko **Přidat.**</span><span class="sxs-lookup"><span data-stu-id="c9a8b-368">If the extensions aren't installed, select the **Add** button.</span></span>
1. <span data-ttu-id="c9a8b-369">Ze seznamu zvolte **ASP.NET jádrová rozšíření.**</span><span class="sxs-lookup"><span data-stu-id="c9a8b-369">Choose the **ASP.NET Core Extensions** from the list.</span></span>
1. <span data-ttu-id="c9a8b-370">Chcete-li přijmout zákonné podmínky, vyberte **možnost OK.**</span><span class="sxs-lookup"><span data-stu-id="c9a8b-370">Select **OK** to accept the legal terms.</span></span>
1. <span data-ttu-id="c9a8b-371">V okně **Přidat prodlužovací** okno vyberte **OK.**</span><span class="sxs-lookup"><span data-stu-id="c9a8b-371">Select **OK** on the **Add extension** blade.</span></span>
1. <span data-ttu-id="c9a8b-372">Informační zpráva o automaticky otevíraná okna označuje, kdy jsou rozšíření úspěšně nainstalována.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-372">An informational pop-up message indicates when the extensions are successfully installed.</span></span>

<span data-ttu-id="c9a8b-373">Pokud protokolování stdout není povoleno, postupujte takto:</span><span class="sxs-lookup"><span data-stu-id="c9a8b-373">If stdout logging isn't enabled, follow these steps:</span></span>

1. <span data-ttu-id="c9a8b-374">Na webu Azure Portal vyberte okno **Rozšířené nástroje** v oblasti NÁSTROJE **PRO VÝVOJ.**</span><span class="sxs-lookup"><span data-stu-id="c9a8b-374">In the Azure portal, select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="c9a8b-375">Vyberte tlačítko \*\*Přejít.&rarr; \*\*</span><span class="sxs-lookup"><span data-stu-id="c9a8b-375">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="c9a8b-376">Konzole Kudu se otevře v novém kartě prohlížeče nebo v okně.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-376">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="c9a8b-377">Pomocí navigačního panelu v horní části stránky otevřete **konzolu ladění** a vyberte **možnost CMD**.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-377">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="c9a8b-378">Otevřete složky na **webu** > cesty **wwwroot** a posuňte se dolů a odhalte soubor *web.config* v dolní části seznamu.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-378">Open the folders to the path **site** > **wwwroot** and scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="c9a8b-379">Klikněte na ikonu tužky vedle souboru *web.config.*</span><span class="sxs-lookup"><span data-stu-id="c9a8b-379">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="c9a8b-380">Nastavte **stdoutLogEnabled** na `true` cestu **stdoutLogFile** `\\?\%home%\LogFiles\stdout`a změňte ji na: .</span><span class="sxs-lookup"><span data-stu-id="c9a8b-380">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="c9a8b-381">Vyberte **Uložit,** chcete-li uložit aktualizovaný soubor *web.config.*</span><span class="sxs-lookup"><span data-stu-id="c9a8b-381">Select **Save** to save the updated *web.config* file.</span></span>

<span data-ttu-id="c9a8b-382">Postupujte k aktivaci diagnostického protokolování:</span><span class="sxs-lookup"><span data-stu-id="c9a8b-382">Proceed to activate diagnostic logging:</span></span>

1. <span data-ttu-id="c9a8b-383">Na webu Azure Portal vyberte okno **Protokoly diagnostiky.**</span><span class="sxs-lookup"><span data-stu-id="c9a8b-383">In the Azure portal, select the **Diagnostics logs** blade.</span></span>
1. <span data-ttu-id="c9a8b-384">Vyberte přepínač **Zapnuto** pro **protokolování aplikací (souborový systém)** a **Podrobné chybové zprávy**.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-384">Select the **On** switch for **Application Logging (Filesystem)** and **Detailed error messages**.</span></span> <span data-ttu-id="c9a8b-385">V horní části nože vyberte tlačítko **Uložit.**</span><span class="sxs-lookup"><span data-stu-id="c9a8b-385">Select the **Save** button at the top of the blade.</span></span>
1. <span data-ttu-id="c9a8b-386">Chcete-li zahrnout trasování neúspěšných požadavků, známé také jako protokolfreb (Failed Request Event Buffering), vyberte přepínač **Zapnuto** pro **trasování neúspěšných požadavků**.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-386">To include failed request tracing, also known as Failed Request Event Buffering (FREB) logging, select the **On** switch for **Failed request tracing**.</span></span>
1. <span data-ttu-id="c9a8b-387">Vyberte okno **protokolu datového proudu,** který je uveden okamžitě pod **protokoly protokoly diagnostiky** na portálu.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-387">Select the **Log stream** blade, which is listed immediately under the **Diagnostics logs** blade in the portal.</span></span>
1. <span data-ttu-id="c9a8b-388">Požádejte o to do aplikace.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-388">Make a request to the app.</span></span>
1. <span data-ttu-id="c9a8b-389">V rámci dat datového proudu protokolu je uvedena příčina chyby.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-389">Within the log stream data, the cause of the error is indicated.</span></span>

<span data-ttu-id="c9a8b-390">Nezapomeňte zakázat stdout protokolování po dokončení řešení potíží.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-390">Be sure to disable stdout logging when troubleshooting is complete.</span></span>

<span data-ttu-id="c9a8b-391">Zobrazení protokolů trasování neúspěšných požadavků (protokoly FREB):</span><span class="sxs-lookup"><span data-stu-id="c9a8b-391">To view the failed request tracing logs (FREB logs):</span></span>

1. <span data-ttu-id="c9a8b-392">Přejděte na okno **Diagnostikovat a vyřešit problémy** na webu Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-392">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="c9a8b-393">V yberte **Protokoly trasování neúspěšných požadavků** v oblasti **NÁSTROJE PODPORY** na postranním panelu.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-393">Select **Failed Request Tracing Logs** from the **SUPPORT TOOLS** area of the sidebar.</span></span>

<span data-ttu-id="c9a8b-394">Další informace [najdete v tématu Sledování neúspěšných požadavků v tématu Povolit diagnostiku pro webové aplikace v tématu Služby aplikací](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) a [v nejčastějších dotazech k výkonu aplikací pro webové aplikace v Azure: Jak zapnu trasování neúspěšných požadavků?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing)</span><span class="sxs-lookup"><span data-stu-id="c9a8b-394">See [Failed request traces section of the Enable diagnostics logging for web apps in Azure App Service topic](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) and the [Application performance FAQs for Web Apps in Azure: How do I turn on failed request tracing?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing) for more information.</span></span>

<span data-ttu-id="c9a8b-395">Další informace najdete [v tématu Povolení protokolování diagnostiky pro webové aplikace ve službě Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log).</span><span class="sxs-lookup"><span data-stu-id="c9a8b-395">For more information, see [Enable diagnostics logging for web apps in Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log).</span></span>

> [!WARNING]
> <span data-ttu-id="c9a8b-396">Selhání zakázání protokolu stdout může vést k selhání aplikace nebo serveru.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-396">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="c9a8b-397">Neexistuje žádné omezení na velikost souboru protokolu nebo počet vytvořených souborů protokolu.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-397">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="c9a8b-398">Pro rutinní protokolování v aplikaci ASP.NET Core použijte knihovnu protokolování, která omezuje velikost souboru protokolu a otáčí protokoly.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-398">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="c9a8b-399">Další informace naleznete [v tématu zprostředkovatelé protokolování třetích stran](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="c9a8b-399">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

## <a name="troubleshoot-on-iis"></a><span data-ttu-id="c9a8b-400">Odstraňování potíží ve službě IIS</span><span class="sxs-lookup"><span data-stu-id="c9a8b-400">Troubleshoot on IIS</span></span>

### <a name="application-event-log-iis"></a><span data-ttu-id="c9a8b-401">Protokol událostí aplikace (IIS)</span><span class="sxs-lookup"><span data-stu-id="c9a8b-401">Application Event Log (IIS)</span></span>

<span data-ttu-id="c9a8b-402">Přístup k protokolu událostí aplikace:</span><span class="sxs-lookup"><span data-stu-id="c9a8b-402">Access the Application Event Log:</span></span>

1. <span data-ttu-id="c9a8b-403">Otevřete nabídku Start, vyhledejte *Prohlížeč událostí*a vyberte aplikaci **Prohlížeč událostí.**</span><span class="sxs-lookup"><span data-stu-id="c9a8b-403">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="c9a8b-404">V **Prohlížeči událostí**otevřete uzel **Protokoly systému Windows.**</span><span class="sxs-lookup"><span data-stu-id="c9a8b-404">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="c9a8b-405">Výběrem **možnosti Aplikace** otevřete protokol událostí aplikace.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-405">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="c9a8b-406">Vyhledejte chyby spojené s aplikací se selháním.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-406">Search for errors associated with the failing app.</span></span> <span data-ttu-id="c9a8b-407">Chyby mají hodnotu *modulu AspNetCore služby IIS* nebo *modulu AspNetCore služby IIS Express* ve sloupci *Zdroj.*</span><span class="sxs-lookup"><span data-stu-id="c9a8b-407">Errors have a value of *IIS AspNetCore Module* or *IIS Express AspNetCore Module* in the *Source* column.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="c9a8b-408">Spuštění aplikace na příkazovém řádku</span><span class="sxs-lookup"><span data-stu-id="c9a8b-408">Run the app at a command prompt</span></span>

<span data-ttu-id="c9a8b-409">Mnoho chyb při spuštění nevytváří užitečné informace v protokolu událostí aplikace.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-409">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="c9a8b-410">Příčinu některých chyb můžete najít spuštěním aplikace na příkazovém řádku v hostitelském systému.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-410">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span>

#### <a name="framework-dependent-deployment"></a><span data-ttu-id="c9a8b-411">Nasazení závislé na rámci</span><span class="sxs-lookup"><span data-stu-id="c9a8b-411">Framework-dependent deployment</span></span>

<span data-ttu-id="c9a8b-412">Pokud je aplikace [nasazení závislé na rámci](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span><span class="sxs-lookup"><span data-stu-id="c9a8b-412">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

1. <span data-ttu-id="c9a8b-413">Na příkazovém řádku přejděte do složky nasazení a spusťte aplikaci spuštěním sestavení aplikace pomocí *dotnet.exe*.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-413">At a command prompt, navigate to the deployment folder and run the app by executing the app's assembly with *dotnet.exe*.</span></span> <span data-ttu-id="c9a8b-414">V následujícím příkazu nahraďte název sestavení \<aplikace assembly_name `dotnet .\<assembly_name>.dll`>: .</span><span class="sxs-lookup"><span data-stu-id="c9a8b-414">In the following command, substitute the name of the app's assembly for \<assembly_name>: `dotnet .\<assembly_name>.dll`.</span></span>
1. <span data-ttu-id="c9a8b-415">Výstup konzoly z aplikace, zobrazující případné chyby, je zapsán do okna konzoly.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-415">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="c9a8b-416">Pokud dojde k chybám při podání požadavku na aplikaci, podat žádost na hostitele a port, kde Kestrel naslouchá.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-416">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="c9a8b-417">Pomocí výchozího hostitele a příspěvku `http://localhost:5000/`požádejte o místo .</span><span class="sxs-lookup"><span data-stu-id="c9a8b-417">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="c9a8b-418">Pokud aplikace reaguje normálně na adrese koncového bodu Kestrel, problém je pravděpodobnější související s konfigurací hostování a méně pravděpodobné v rámci aplikace.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-418">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

#### <a name="self-contained-deployment"></a><span data-ttu-id="c9a8b-419">Samostatné nasazení</span><span class="sxs-lookup"><span data-stu-id="c9a8b-419">Self-contained deployment</span></span>

<span data-ttu-id="c9a8b-420">Pokud se jedná o [samostatné nasazení](/dotnet/core/deploying/#self-contained-deployments-scd)aplikace :</span><span class="sxs-lookup"><span data-stu-id="c9a8b-420">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

1. <span data-ttu-id="c9a8b-421">Na příkazovém řádku přejděte do složky nasazení a spusťte spustitelný soubor aplikace.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-421">At a command prompt, navigate to the deployment folder and run the app's executable.</span></span> <span data-ttu-id="c9a8b-422">V následujícím příkazu nahraďte název sestavení \<aplikace assembly_name `<assembly_name>.exe`>: .</span><span class="sxs-lookup"><span data-stu-id="c9a8b-422">In the following command, substitute the name of the app's assembly for \<assembly_name>: `<assembly_name>.exe`.</span></span>
1. <span data-ttu-id="c9a8b-423">Výstup konzoly z aplikace, zobrazující případné chyby, je zapsán do okna konzoly.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-423">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="c9a8b-424">Pokud dojde k chybám při podání požadavku na aplikaci, podat žádost na hostitele a port, kde Kestrel naslouchá.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-424">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="c9a8b-425">Pomocí výchozího hostitele a příspěvku `http://localhost:5000/`požádejte o místo .</span><span class="sxs-lookup"><span data-stu-id="c9a8b-425">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="c9a8b-426">Pokud aplikace reaguje normálně na adrese koncového bodu Kestrel, problém je pravděpodobnější související s konfigurací hostování a méně pravděpodobné v rámci aplikace.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-426">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

### <a name="aspnet-core-module-stdout-log-iis"></a><span data-ttu-id="c9a8b-427">ASP.NET protokol stdout modulu (IIS)</span><span class="sxs-lookup"><span data-stu-id="c9a8b-427">ASP.NET Core Module stdout log (IIS)</span></span>

<span data-ttu-id="c9a8b-428">Povolení a zobrazení protokolů stdout:</span><span class="sxs-lookup"><span data-stu-id="c9a8b-428">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="c9a8b-429">Přejděte do složky nasazení webu v hostitelském systému.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-429">Navigate to the site's deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="c9a8b-430">Pokud složka *protokolů* není k dispozici, vytvořte složku.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-430">If the *logs* folder isn't present, create the folder.</span></span> <span data-ttu-id="c9a8b-431">Pokyny k povolení msbuild vytvořit složku *protokolů* v nasazení automaticky, naleznete v tématu [directory struktura.](xref:host-and-deploy/directory-structure)</span><span class="sxs-lookup"><span data-stu-id="c9a8b-431">For instructions on how to enable MSBuild to create the *logs* folder in the deployment automatically, see the [Directory structure](xref:host-and-deploy/directory-structure) topic.</span></span>
1. <span data-ttu-id="c9a8b-432">Upravte soubor *web.config.*</span><span class="sxs-lookup"><span data-stu-id="c9a8b-432">Edit the *web.config* file.</span></span> <span data-ttu-id="c9a8b-433">Nastavte **stdoutLogEnabled** a `true` změňte cestu **stdoutLogFile** tak, `.\logs\stdout`aby přecózovala na složku *protokolů* (například ).</span><span class="sxs-lookup"><span data-stu-id="c9a8b-433">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to point to the *logs* folder (for example, `.\logs\stdout`).</span></span> <span data-ttu-id="c9a8b-434">`stdout`v cestě je předpona názvu souboru protokolu.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-434">`stdout` in the path is the log file name prefix.</span></span> <span data-ttu-id="c9a8b-435">Časové razítko, ID procesu a přípona souboru jsou přidány automaticky při vytvoření protokolu.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-435">A timestamp, process id, and file extension are added automatically when the log is created.</span></span> <span data-ttu-id="c9a8b-436">Jako `stdout` předpona názvu souboru je typický soubor protokolu pojmenován *stdout_20180205184032_5412.log*.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-436">Using `stdout` as the file name prefix, a typical log file is named *stdout_20180205184032_5412.log*.</span></span>
1. <span data-ttu-id="c9a8b-437">Ujistěte se, že identita fondu aplikací má oprávnění k zápisu do složky *protokolů.*</span><span class="sxs-lookup"><span data-stu-id="c9a8b-437">Ensure your application pool's identity has write permissions to the *logs* folder.</span></span>
1. <span data-ttu-id="c9a8b-438">Uložte aktualizovaný soubor *web.config.*</span><span class="sxs-lookup"><span data-stu-id="c9a8b-438">Save the updated *web.config* file.</span></span>
1. <span data-ttu-id="c9a8b-439">Požádejte o to do aplikace.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-439">Make a request to the app.</span></span>
1. <span data-ttu-id="c9a8b-440">Přejděte do složky *protokolů.*</span><span class="sxs-lookup"><span data-stu-id="c9a8b-440">Navigate to the *logs* folder.</span></span> <span data-ttu-id="c9a8b-441">Vyhledejte a otevřete nejnovější protokol stdout.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-441">Find and open the most recent stdout log.</span></span>
1. <span data-ttu-id="c9a8b-442">Prostudujte si protokol pro chyby.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-442">Study the log for errors.</span></span>

<span data-ttu-id="c9a8b-443">Zakažte protokolování stdout po dokončení řešení potíží:</span><span class="sxs-lookup"><span data-stu-id="c9a8b-443">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="c9a8b-444">Upravte soubor *web.config.*</span><span class="sxs-lookup"><span data-stu-id="c9a8b-444">Edit the *web.config* file.</span></span>
1. <span data-ttu-id="c9a8b-445">Nastavte **stdoutLogEnabled** na `false`.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-445">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="c9a8b-446">Uložte soubor.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-446">Save the file.</span></span>

<span data-ttu-id="c9a8b-447">Další informace naleznete v tématu <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-447">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

> [!WARNING]
> <span data-ttu-id="c9a8b-448">Selhání zakázání protokolu stdout může vést k selhání aplikace nebo serveru.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-448">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="c9a8b-449">Neexistuje žádné omezení na velikost souboru protokolu nebo počet vytvořených souborů protokolu.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-449">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="c9a8b-450">Pro rutinní protokolování v aplikaci ASP.NET Core použijte knihovnu protokolování, která omezuje velikost souboru protokolu a otáčí protokoly.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-450">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="c9a8b-451">Další informace naleznete [v tématu zprostředkovatelé protokolování třetích stran](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="c9a8b-451">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="aspnet-core-module-debug-log-iis"></a><span data-ttu-id="c9a8b-452">protokol ladění základního modulu ASP.NET (IIS)</span><span class="sxs-lookup"><span data-stu-id="c9a8b-452">ASP.NET Core Module debug log (IIS)</span></span>

<span data-ttu-id="c9a8b-453">Přidejte do souboru *web.config* aplikace následující nastavení obslužné rutiny, která umožní ASP.NET protokol ladění základního modulu:</span><span class="sxs-lookup"><span data-stu-id="c9a8b-453">Add the following handler settings to the app's *web.config* file to enable ASP.NET Core Module debug log:</span></span>

```xml
<aspNetCore ...>
  <handlerSettings>
    <handlerSetting name="debugLevel" value="file" />
    <handlerSetting name="debugFile" value="c:\temp\ancm.log" />
  </handlerSettings>
</aspNetCore>
```

<span data-ttu-id="c9a8b-454">Zkontrolujte, zda existuje cesta zadaná pro protokol a zda má identita fondu aplikací oprávnění k zápisu do umístění.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-454">Confirm that the path specified for the log exists and that the app pool's identity has write permissions to the location.</span></span>

<span data-ttu-id="c9a8b-455">Další informace naleznete v tématu <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-455">For more information, see <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span></span>

### <a name="enable-the-developer-exception-page"></a><span data-ttu-id="c9a8b-456">Povolení stránky výjimky pro vývojáře</span><span class="sxs-lookup"><span data-stu-id="c9a8b-456">Enable the Developer Exception Page</span></span>

<span data-ttu-id="c9a8b-457">`ASPNETCORE_ENVIRONMENT` [Proměnnou prostředí lze přidat do web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) pro spuštění aplikace ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-457">The `ASPNETCORE_ENVIRONMENT` [environment variable can be added to web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) to run the app in the Development environment.</span></span> <span data-ttu-id="c9a8b-458">Pokud prostředí není přepsáno při spuštění `UseEnvironment` aplikace tvůrcem hostitele, nastavení proměnné prostředí umožňuje, aby se [stránka výjimky pro vývojáře](xref:fundamentals/error-handling) zobrazila při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-458">As long as the environment isn't overridden in app startup by `UseEnvironment` on the host builder, setting the environment variable allows the [Developer Exception Page](xref:fundamentals/error-handling) to appear when the app is run.</span></span>

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

<span data-ttu-id="c9a8b-459">Nastavení proměnné prostředí `ASPNETCORE_ENVIRONMENT` pro se doporučuje pouze pro použití na pracovních a testovacích serverech, které nejsou vystaveny Internetu.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-459">Setting the environment variable for `ASPNETCORE_ENVIRONMENT` is only recommended for use on staging and testing servers that aren't exposed to the Internet.</span></span> <span data-ttu-id="c9a8b-460">Po odstranění potíží odeberte proměnnou prostředí ze souboru *web.config.*</span><span class="sxs-lookup"><span data-stu-id="c9a8b-460">Remove the environment variable from the *web.config* file after troubleshooting.</span></span> <span data-ttu-id="c9a8b-461">Informace o nastavení proměnných prostředí v *souboru web.config*naleznete v [tématu podřízený prvek environmentVariables aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).</span><span class="sxs-lookup"><span data-stu-id="c9a8b-461">For information on setting environment variables in *web.config*, see [environmentVariables child element of aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).</span></span>

### <a name="obtain-data-from-an-app"></a><span data-ttu-id="c9a8b-462">Získání dat z aplikace</span><span class="sxs-lookup"><span data-stu-id="c9a8b-462">Obtain data from an app</span></span>

<span data-ttu-id="c9a8b-463">Pokud je aplikace schopná reagovat na požadavky, získejte požadavek, připojení a další data z aplikace pomocí terminálového inline middlewaru.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-463">If an app is capable of responding to requests, obtain request, connection, and additional data from the app using terminal inline middleware.</span></span> <span data-ttu-id="c9a8b-464">Další informace a ukázkový <xref:test/troubleshoot#obtain-data-from-an-app>kód naleznete v tématu .</span><span class="sxs-lookup"><span data-stu-id="c9a8b-464">For more information and sample code, see <xref:test/troubleshoot#obtain-data-from-an-app>.</span></span>

### <a name="slow-or-hanging-app-iis"></a><span data-ttu-id="c9a8b-465">Pomalá nebo závěsná aplikace (IIS)</span><span class="sxs-lookup"><span data-stu-id="c9a8b-465">Slow or hanging app (IIS)</span></span>

<span data-ttu-id="c9a8b-466">*Výpis stavu systému* je snímek paměti systému a může pomoci určit příčinu selhání aplikace, selhání při spuštění nebo pomalé aplikace.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-466">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="c9a8b-467">Aplikace dojde k chybě nebo narazí na výjimku</span><span class="sxs-lookup"><span data-stu-id="c9a8b-467">App crashes or encounters an exception</span></span>

<span data-ttu-id="c9a8b-468">Získání a analýza výpisu z [programu Zasílání zpráv o chybách systému Windows (WER)](/windows/desktop/wer/windows-error-reporting):</span><span class="sxs-lookup"><span data-stu-id="c9a8b-468">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="c9a8b-469">Vytvořte složku, ve které `c:\dumps`budou uloženy soubory s výpisem stavu systému .</span><span class="sxs-lookup"><span data-stu-id="c9a8b-469">Create a folder to hold crash dump files at `c:\dumps`.</span></span> <span data-ttu-id="c9a8b-470">Fond aplikací musí mít přístup pro zápis do složky.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-470">The app pool must have write access to the folder.</span></span>
1. <span data-ttu-id="c9a8b-471">Spusťte [skript EnableDumps PowerShell](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1):</span><span class="sxs-lookup"><span data-stu-id="c9a8b-471">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1):</span></span>
   * <span data-ttu-id="c9a8b-472">Pokud aplikace používá [model hostování v procesu](xref:host-and-deploy/iis/index#in-process-hosting-model), spusťte skript pro *w3wp.exe*:</span><span class="sxs-lookup"><span data-stu-id="c9a8b-472">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\EnableDumps w3wp.exe c:\dumps
     ```

   * <span data-ttu-id="c9a8b-473">Pokud aplikace používá [mimoprocesový model hostování](xref:host-and-deploy/iis/index#out-of-process-hosting-model), spusťte skript pro *dotnet.exe*:</span><span class="sxs-lookup"><span data-stu-id="c9a8b-473">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\EnableDumps dotnet.exe c:\dumps
     ```

1. <span data-ttu-id="c9a8b-474">Spusťte aplikaci za podmínek, které způsobují selhání dojít.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-474">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="c9a8b-475">Po selhání spusťte [skript DisableDumps PowerShell](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1):</span><span class="sxs-lookup"><span data-stu-id="c9a8b-475">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1):</span></span>
   * <span data-ttu-id="c9a8b-476">Pokud aplikace používá [model hostování v procesu](xref:host-and-deploy/iis/index#in-process-hosting-model), spusťte skript pro *w3wp.exe*:</span><span class="sxs-lookup"><span data-stu-id="c9a8b-476">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\DisableDumps w3wp.exe
     ```

   * <span data-ttu-id="c9a8b-477">Pokud aplikace používá [mimoprocesový model hostování](xref:host-and-deploy/iis/index#out-of-process-hosting-model), spusťte skript pro *dotnet.exe*:</span><span class="sxs-lookup"><span data-stu-id="c9a8b-477">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\DisableDumps dotnet.exe
     ```

<span data-ttu-id="c9a8b-478">Po zhroucení aplikace a výpis kolekce je kompletní, aplikace je povoleno ukončit normálně.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-478">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="c9a8b-479">Skript PowerShell udává WER tak, aby shromažďoval až pět výpisů v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-479">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="c9a8b-480">Výpisy stavu systému může zabírají velké množství místa na disku (až několik gigabajtů každý).</span><span class="sxs-lookup"><span data-stu-id="c9a8b-480">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="c9a8b-481">Aplikace přestane reagovat, selže při spuštění nebo běží normálně</span><span class="sxs-lookup"><span data-stu-id="c9a8b-481">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="c9a8b-482">Když aplikace *přestane reagovat* (přestane reagovat, ale nezhroutí se), selže při spuštění nebo běží normálně, najdete v [tématu Soubory výpisu uživatelského režimu: Výběr nejvhodnějšího nástroje](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) pro výběr vhodného nástroje pro vytvoření výpisu.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-482">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="c9a8b-483">Analyzovat výpis</span><span class="sxs-lookup"><span data-stu-id="c9a8b-483">Analyze the dump</span></span>

<span data-ttu-id="c9a8b-484">Výpis lze analyzovat pomocí několika přístupů.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-484">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="c9a8b-485">Další informace naleznete [v tématu Analýza souboru s výpisem stavu uživatelského režimu](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span><span class="sxs-lookup"><span data-stu-id="c9a8b-485">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="clear-package-caches"></a><span data-ttu-id="c9a8b-486">Vymazat mezipaměti balíčků</span><span class="sxs-lookup"><span data-stu-id="c9a8b-486">Clear package caches</span></span>

<span data-ttu-id="c9a8b-487">Funkční aplikace může selhat ihned po upgradu sady .NET Core SDK ve vývojovém počítači nebo změně verze balíčku v rámci aplikace.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-487">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="c9a8b-488">V některých případech nesouvislé balíčky může přerušit aplikaci při provádění hlavních upgradů.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-488">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="c9a8b-489">Většinu těchto problémů lze opravit podle následujících pokynů:</span><span class="sxs-lookup"><span data-stu-id="c9a8b-489">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="c9a8b-490">Odstraňte složky *bin* a *obj.*</span><span class="sxs-lookup"><span data-stu-id="c9a8b-490">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="c9a8b-491">Vymazat mezipaměti balíčku spuštěním [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) z příkazového prostředí.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-491">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="c9a8b-492">Vymazání mezipaměti balíčků lze také provést pomocí nástroje [nuget.exe](https://www.nuget.org/downloads) a spuštěnípříkazu `nuget locals all -clear`.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-492">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="c9a8b-493">*nuget.exe* není přibalená instalace s operačním systémem Windows desktop a musí být získány odděleně od [webu NuGet](https://www.nuget.org/downloads).</span><span class="sxs-lookup"><span data-stu-id="c9a8b-493">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="c9a8b-494">Obnovte a znovu vytvořte projekt.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-494">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="c9a8b-495">Před opětovnou nasazením aplikace odstraňte všechny soubory ve složce nasazení na serveru.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-495">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c9a8b-496">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="c9a8b-496">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:fundamentals/error-handling>
* <xref:host-and-deploy/aspnet-core-module>

### <a name="azure-documentation"></a><span data-ttu-id="c9a8b-497">Dokumentace k Azure</span><span class="sxs-lookup"><span data-stu-id="c9a8b-497">Azure documentation</span></span>

* [<span data-ttu-id="c9a8b-498">Application Insights pro ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c9a8b-498">Application Insights for ASP.NET Core</span></span>](/azure/application-insights/app-insights-asp-net-core)
* [<span data-ttu-id="c9a8b-499">Část Řešení potíží s webovou aplikací ve službě Azure App Service ve vzdáleném ladění webových aplikací pomocí Visual Studia</span><span class="sxs-lookup"><span data-stu-id="c9a8b-499">Remote debugging web apps section of Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug)
* [<span data-ttu-id="c9a8b-500">Přehled diagnostiky služby Azure App Service</span><span class="sxs-lookup"><span data-stu-id="c9a8b-500">Azure App Service diagnostics overview</span></span>](/azure/app-service/app-service-diagnostics)
* [<span data-ttu-id="c9a8b-501">Postup: Monitorování aplikací ve službě Azure App Service</span><span class="sxs-lookup"><span data-stu-id="c9a8b-501">How to: Monitor Apps in Azure App Service</span></span>](/azure/app-service/web-sites-monitor)
* [<span data-ttu-id="c9a8b-502">Poradce při potížích s webovou aplikací ve službě Azure App Service pomocí Visual Studia</span><span class="sxs-lookup"><span data-stu-id="c9a8b-502">Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [<span data-ttu-id="c9a8b-503">Poradce při potížích s chybami HTTP "502 chybná brána" a "503 služba není k dispozici" ve svých webových aplikacích Azure</span><span class="sxs-lookup"><span data-stu-id="c9a8b-503">Troubleshoot HTTP errors of "502 bad gateway" and "503 service unavailable" in your Azure web apps</span></span>](/azure/app-service/app-service-web-troubleshoot-http-502-http-503)
* [<span data-ttu-id="c9a8b-504">Řešení potíží s výkonem pomalých webových aplikací ve službě Azure App Service</span><span class="sxs-lookup"><span data-stu-id="c9a8b-504">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="c9a8b-505">Časté otázky k výkonu aplikací pro webové aplikace v Azure</span><span class="sxs-lookup"><span data-stu-id="c9a8b-505">Application performance FAQs for Web Apps in Azure</span></span>](/azure/app-service/app-service-web-availability-performance-application-issues-faq)
* [<span data-ttu-id="c9a8b-506">Azure Web App izolovaného prostoru (omezení spuštění služby App Service)</span><span class="sxs-lookup"><span data-stu-id="c9a8b-506">Azure Web App sandbox (App Service runtime execution limitations)</span></span>](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)
* [<span data-ttu-id="c9a8b-507">Azure Friday: Azure App Service Diagnostic and Troubleshooting Experience (12minutové video)</span><span class="sxs-lookup"><span data-stu-id="c9a8b-507">Azure Friday: Azure App Service Diagnostic and Troubleshooting Experience (12-minute video)</span></span>](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)

### <a name="visual-studio-documentation"></a><span data-ttu-id="c9a8b-508">Dokumentace sady Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c9a8b-508">Visual Studio documentation</span></span>

* [<span data-ttu-id="c9a8b-509">Vzdálené ladění ASP.NET jádro ve službě IIS v Azure ve Visual Studiu 2017</span><span class="sxs-lookup"><span data-stu-id="c9a8b-509">Remote Debug ASP.NET Core on IIS in Azure in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-azure)
* [<span data-ttu-id="c9a8b-510">Vzdálené ladění ASP.NET jádra ve vzdáleném počítači služby IIS v sadě Visual Studio 2017</span><span class="sxs-lookup"><span data-stu-id="c9a8b-510">Remote Debug ASP.NET Core on a Remote IIS Computer in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-aspnet-on-a-remote-iis-computer)
* [<span data-ttu-id="c9a8b-511">Informace o ladění pomocí sady Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c9a8b-511">Learn to debug using Visual Studio</span></span>](/visualstudio/debugger/getting-started-with-the-debugger)

### <a name="visual-studio-code-documentation"></a><span data-ttu-id="c9a8b-512">Dokumentace kódu visual studia</span><span class="sxs-lookup"><span data-stu-id="c9a8b-512">Visual Studio Code documentation</span></span>

* [<span data-ttu-id="c9a8b-513">Ladění pomocí kódu sady Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c9a8b-513">Debugging with Visual Studio Code</span></span>](https://code.visualstudio.com/docs/editor/debugging)

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="c9a8b-514">Tento článek obsahuje informace o běžných chybách při spuštění aplikace a pokyny, jak diagnostikovat chyby při nasazení aplikace do služby Azure App Service nebo IIS:</span><span class="sxs-lookup"><span data-stu-id="c9a8b-514">This article provides information on common app startup errors and instructions on how to diagnose errors when an app is deployed to Azure App Service or IIS:</span></span>

[<span data-ttu-id="c9a8b-515">Chyby při spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="c9a8b-515">App startup errors</span></span>](#app-startup-errors)  
<span data-ttu-id="c9a8b-516">Vysvětluje běžné scénáře stavového kódu HTTP při spuštění.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-516">Explains common startup HTTP status code scenarios.</span></span>

[<span data-ttu-id="c9a8b-517">Poradce při potížích se službou Azure App Service</span><span class="sxs-lookup"><span data-stu-id="c9a8b-517">Troubleshoot on Azure App Service</span></span>](#troubleshoot-on-azure-app-service)  
<span data-ttu-id="c9a8b-518">Poskytuje rady pro řešení potíží pro aplikace nasazené do služby Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-518">Provides troubleshooting advice for apps deployed to Azure App Service.</span></span>

[<span data-ttu-id="c9a8b-519">Odstraňování potíží ve službě IIS</span><span class="sxs-lookup"><span data-stu-id="c9a8b-519">Troubleshoot on IIS</span></span>](#troubleshoot-on-iis)  
<span data-ttu-id="c9a8b-520">Poskytuje rady pro řešení potíží s aplikacemi nasazenými do služby IIS nebo spuštěných místně ve službě IIS Express.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-520">Provides troubleshooting advice for apps deployed to IIS or running on IIS Express locally.</span></span> <span data-ttu-id="c9a8b-521">Pokyny platí pro nasazení plochy systému Windows Server i Windows.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-521">The guidance applies to both Windows Server and Windows desktop deployments.</span></span>

[<span data-ttu-id="c9a8b-522">Vymazat mezipaměti balíčků</span><span class="sxs-lookup"><span data-stu-id="c9a8b-522">Clear package caches</span></span>](#clear-package-caches)  
<span data-ttu-id="c9a8b-523">Vysvětluje, co dělat, když nesouvislé balíčky přerušit aplikaci při provádění hlavních upgradů nebo změny verze balíčku.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-523">Explains what to do when incoherent packages break an app when performing major upgrades or changing package versions.</span></span>

[<span data-ttu-id="c9a8b-524">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="c9a8b-524">Additional resources</span></span>](#additional-resources)  
<span data-ttu-id="c9a8b-525">Obsahuje další témata pro řešení potíží.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-525">Lists additional troubleshooting topics.</span></span>

## <a name="app-startup-errors"></a><span data-ttu-id="c9a8b-526">Chyby při spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="c9a8b-526">App startup errors</span></span>

<span data-ttu-id="c9a8b-527">V sadě Visual Studio, ASP.NET projektu Core výchozí [iis express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hostování během ladění.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-527">In Visual Studio, an ASP.NET Core project defaults to [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hosting during debugging.</span></span> <span data-ttu-id="c9a8b-528">A *502.5 - Selhání procesu* nebo *500.30 - Start Selhání,* ke kterému dochází při ladění místně lze diagnostikovat pomocí rady v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-528">A *502.5 - Process Failure* or a *500.30 - Start Failure* that occurs when debugging locally can be diagnosed using the advice in this topic.</span></span>

### <a name="40314-forbidden"></a><span data-ttu-id="c9a8b-529">403.14 Zakázáno</span><span class="sxs-lookup"><span data-stu-id="c9a8b-529">403.14 Forbidden</span></span>

<span data-ttu-id="c9a8b-530">Aplikace se nespustí.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-530">The app fails to start.</span></span> <span data-ttu-id="c9a8b-531">Je zaznamenána následující chyba:</span><span class="sxs-lookup"><span data-stu-id="c9a8b-531">The following error is logged:</span></span>

```
The Web server is configured to not list the contents of this directory.
```

<span data-ttu-id="c9a8b-532">Chyba je obvykle způsobena přerušeným nasazením v hostitelském systému, který zahrnuje některý z následujících scénářů:</span><span class="sxs-lookup"><span data-stu-id="c9a8b-532">The error is usually caused by a broken deployment on the hosting system, which includes any of the following scenarios:</span></span>

* <span data-ttu-id="c9a8b-533">Aplikace je nasazena do nesprávné složky v hostitelském systému.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-533">The app is deployed to the wrong folder on the hosting system.</span></span>
* <span data-ttu-id="c9a8b-534">Procesu nasazení se nepodařilo přesunout všechny soubory a složky aplikace do složky nasazení v hostitelském systému.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-534">The deployment process failed to move all of the app's files and folders to the deployment folder on the hosting system.</span></span>
* <span data-ttu-id="c9a8b-535">Soubor *web.config* chybí v nasazení nebo je obsah souboru *web.config* poškozen.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-535">The *web.config* file is missing from the deployment, or the *web.config* file contents are malformed.</span></span>

<span data-ttu-id="c9a8b-536">Proveďte následující kroky:</span><span class="sxs-lookup"><span data-stu-id="c9a8b-536">Perform the following steps:</span></span>

1. <span data-ttu-id="c9a8b-537">Odstraňte všechny soubory a složky ze složky nasazení v hostitelském systému.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-537">Delete all of the files and folders from the deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="c9a8b-538">Znovu nasadit obsah složky *publikování* aplikace do hostitelského systému pomocí normální metody nasazení, jako je Visual Studio, PowerShell nebo ruční nasazení:</span><span class="sxs-lookup"><span data-stu-id="c9a8b-538">Redeploy the contents of the app's *publish* folder to the hosting system using your normal method of deployment, such as Visual Studio, PowerShell, or manual deployment:</span></span>
   * <span data-ttu-id="c9a8b-539">Zkontrolujte, zda je soubor *web.config* v nasazení přítomen a zda je jeho obsah správný.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-539">Confirm that the *web.config* file is present in the deployment and that its contents are correct.</span></span>
   * <span data-ttu-id="c9a8b-540">Při hostování ve službě Azure App Service zkontrolujte, že se aplikace nasadí do `D:\home\site\wwwroot` složky.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-540">When hosting on Azure App Service, confirm that the app is deployed to the `D:\home\site\wwwroot` folder.</span></span>
   * <span data-ttu-id="c9a8b-541">Pokud je aplikace hostována službou IIS, zkontrolujte, zda je aplikace nasazena do **fyzické cesty** služby IIS zobrazené v **základním nastavení** **správce služby IIS**.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-541">When the app is hosted by IIS, confirm that the app is deployed to the IIS **Physical path** shown in **IIS Manager**'s **Basic Settings**.</span></span>
1. <span data-ttu-id="c9a8b-542">Zkontrolujte, zda jsou všechny soubory a složky aplikace nasazeny porovnáním nasazení v hostitelském systému s obsahem složky *publikování* projektu.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-542">Confirm that all of the app's files and folders are deployed by comparing the deployment on the hosting system to the contents of the project's *publish* folder.</span></span>

<span data-ttu-id="c9a8b-543">Další informace o rozložení publikované aplikace ASP.NET <xref:host-and-deploy/directory-structure>Core najdete v tématu .</span><span class="sxs-lookup"><span data-stu-id="c9a8b-543">For more information on the layout of a published ASP.NET Core app, see <xref:host-and-deploy/directory-structure>.</span></span> <span data-ttu-id="c9a8b-544">Další informace o souboru *web.config* naleznete v tématu <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-544">For more information on the *web.config* file, see <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>.</span></span>

### <a name="500-internal-server-error"></a><span data-ttu-id="c9a8b-545">500 – Interní chyba serveru</span><span class="sxs-lookup"><span data-stu-id="c9a8b-545">500 Internal Server Error</span></span>

<span data-ttu-id="c9a8b-546">Aplikace se spustí, ale chyba zabrání serveru v plnění požadavku.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-546">The app starts, but an error prevents the server from fulfilling the request.</span></span>

<span data-ttu-id="c9a8b-547">K této chybě dochází v kódu aplikace při spuštění nebo při vytváření odpovědi.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-547">This error occurs within the app's code during startup or while creating a response.</span></span> <span data-ttu-id="c9a8b-548">Odpověď může obsahovat žádný obsah nebo odpověď může zobrazit jako *500 Vnitřní chyba serveru* v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-548">The response may contain no content, or the response may appear as a *500 Internal Server Error* in the browser.</span></span> <span data-ttu-id="c9a8b-549">Protokol událostí aplikace obvykle uvádí, že aplikace byla spuštěna normálně.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-549">The Application Event Log usually states that the app started normally.</span></span> <span data-ttu-id="c9a8b-550">Z pohledu serveru je to správné.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-550">From the server's perspective, that's correct.</span></span> <span data-ttu-id="c9a8b-551">Aplikace se spustila, ale nemůže generovat platnou odpověď.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-551">The app did start, but it can't generate a valid response.</span></span> <span data-ttu-id="c9a8b-552">Spusťte aplikaci na příkazovém řádku na serveru nebo povolte protokol stdout ASP.NET Core Module k řešení problému.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-552">Run the app at a command prompt on the server or enable the ASP.NET Core Module stdout log to troubleshoot the problem.</span></span>

### <a name="5000-in-process-handler-load-failure"></a><span data-ttu-id="c9a8b-553">Selhání zatížení obslužné rutiny procesu 500.0</span><span class="sxs-lookup"><span data-stu-id="c9a8b-553">500.0 In-Process Handler Load Failure</span></span>

<span data-ttu-id="c9a8b-554">Pracovní proces se nezdaří.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-554">The worker process fails.</span></span> <span data-ttu-id="c9a8b-555">Aplikace se nespustí.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-555">The app doesn't start.</span></span>

<span data-ttu-id="c9a8b-556">Základní [modul ASP.NET](xref:host-and-deploy/aspnet-core-module) nenalezl clr jádra .NET a našel obslužnou rutinu požadavku v procesu (*aspnetcorev2_inprocess.dll*).</span><span class="sxs-lookup"><span data-stu-id="c9a8b-556">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) fails to find the .NET Core CLR and find the in-process request handler (*aspnetcorev2_inprocess.dll*).</span></span> <span data-ttu-id="c9a8b-557">Zkontrolujte, zda:</span><span class="sxs-lookup"><span data-stu-id="c9a8b-557">Check that:</span></span>

* <span data-ttu-id="c9a8b-558">Aplikace cílí buď na balíček [Microsoft.AspNetCore.Server.IIS](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IIS) NuGet, nebo na [metabalíček Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="c9a8b-558">The app targets either the [Microsoft.AspNetCore.Server.IIS](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IIS) NuGet package or the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>
* <span data-ttu-id="c9a8b-559">Verze sdíleného rozhraní ASP.NET Core, na kterou je aplikace nainstalovaná v cílovém počítači.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-559">The version of the ASP.NET Core shared framework that the app targets is installed on the target machine.</span></span>

### <a name="5000-out-of-process-handler-load-failure"></a><span data-ttu-id="c9a8b-560">500.0 Selhání zatížení obslužné rutiny mimo proces</span><span class="sxs-lookup"><span data-stu-id="c9a8b-560">500.0 Out-Of-Process Handler Load Failure</span></span>

<span data-ttu-id="c9a8b-561">Pracovní proces se nezdaří.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-561">The worker process fails.</span></span> <span data-ttu-id="c9a8b-562">Aplikace se nespustí.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-562">The app doesn't start.</span></span>

<span data-ttu-id="c9a8b-563">[ASP.NET core modul](xref:host-and-deploy/aspnet-core-module) unese nalezení obslužné rutiny požadavků mimo proces hostování.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-563">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) fails to find the out-of-process hosting request handler.</span></span> <span data-ttu-id="c9a8b-564">Ujistěte se, *že aspnetcorev2_outofprocess.dll* je k dispozici v podsložce vedle *aspnetcorev2.dll*.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-564">Make sure the *aspnetcorev2_outofprocess.dll* is present in a subfolder next to *aspnetcorev2.dll*.</span></span>

### <a name="5025-process-failure"></a><span data-ttu-id="c9a8b-565">502.5 – Selhání procesu</span><span class="sxs-lookup"><span data-stu-id="c9a8b-565">502.5 Process Failure</span></span>

<span data-ttu-id="c9a8b-566">Pracovní proces se nezdaří.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-566">The worker process fails.</span></span> <span data-ttu-id="c9a8b-567">Aplikace se nespustí.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-567">The app doesn't start.</span></span>

<span data-ttu-id="c9a8b-568">Základní [modul ASP.NET](xref:host-and-deploy/aspnet-core-module) se pokusí spustit pracovní proces, ale nepodaří se jej spustit.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-568">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the worker process but it fails to start.</span></span> <span data-ttu-id="c9a8b-569">Příčinu selhání spuštění procesu lze obvykle určit z položek v protokolu událostí aplikace a ASP.NET protokolu stdout základního modulu.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-569">The cause of a process startup failure can usually be determined from entries in the Application Event Log and the ASP.NET Core Module stdout log.</span></span>

<span data-ttu-id="c9a8b-570">Obvyklá podmínka selhání je, že aplikace je nesprávně nakonfigurovaná kvůli cílení na verzi sdíleného rozhraní ASP.NET Core, která není k dispozici.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-570">A common failure condition is the app is misconfigured due to targeting a version of the ASP.NET Core shared framework that isn't present.</span></span> <span data-ttu-id="c9a8b-571">Zkontrolujte, které verze ASP.NET sdíleného rozhraní Core jsou nainstalovány v cílovém počítači.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-571">Check which versions of the ASP.NET Core shared framework are installed on the target machine.</span></span> <span data-ttu-id="c9a8b-572">Sdílená *architektura* je sada sestavení (*soubory DLL),* které jsou nainstalovány v počítači a odkazovány metabalíčkem, například `Microsoft.AspNetCore.App`.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-572">The *shared framework* is the set of assemblies (*.dll* files) that are installed on the machine and referenced by a metapackage such as `Microsoft.AspNetCore.App`.</span></span> <span data-ttu-id="c9a8b-573">Odkaz na metabalíček může určit minimální požadovanou verzi.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-573">The metapackage reference can specify a minimum required version.</span></span> <span data-ttu-id="c9a8b-574">Další informace naleznete [v tématu sdílený rámec](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span><span class="sxs-lookup"><span data-stu-id="c9a8b-574">For more information, see [The shared framework](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span></span>

<span data-ttu-id="c9a8b-575">Chybová stránka *502.5 Process Failure* je vrácena, když selhání konfigurace hostitele nebo aplikace způsobí selhání pracovního procesu:</span><span class="sxs-lookup"><span data-stu-id="c9a8b-575">The *502.5 Process Failure* error page is returned when a hosting or app misconfiguration causes the worker process to fail:</span></span>

### <a name="failed-to-start-application-errorcode-0x800700c1"></a><span data-ttu-id="c9a8b-576">Spuštění aplikace se nezdařilo (ErrorCode '0x800700c1')</span><span class="sxs-lookup"><span data-stu-id="c9a8b-576">Failed to start application (ErrorCode '0x800700c1')</span></span>

```
EventID: 1010
Source: IIS AspNetCore Module V2
Failed to start application '/LM/W3SVC/6/ROOT/', ErrorCode '0x800700c1'.
```

<span data-ttu-id="c9a8b-577">Spuštění aplikace se nezdařilo, protože nebylo možné načíst sestavení aplikace (*DLL*).</span><span class="sxs-lookup"><span data-stu-id="c9a8b-577">The app failed to start because the app's assembly (*.dll*) couldn't be loaded.</span></span>

<span data-ttu-id="c9a8b-578">K této chybě dochází, pokud je neodpovídající bitness mezi publikovanou aplikací a w3wp/iisexpress procesu.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-578">This error occurs when there's a bitness mismatch between the published app and the w3wp/iisexpress process.</span></span>

<span data-ttu-id="c9a8b-579">Zkontrolujte, zda je 32bitové nastavení fondu aplikací správné:</span><span class="sxs-lookup"><span data-stu-id="c9a8b-579">Confirm that the app pool's 32-bit setting is correct:</span></span>

1. <span data-ttu-id="c9a8b-580">Vyberte fond aplikací ve **fondech aplikací**Správce služby IIS .</span><span class="sxs-lookup"><span data-stu-id="c9a8b-580">Select the app pool in IIS Manager's **Application Pools**.</span></span>
1. <span data-ttu-id="c9a8b-581">V části Upravit fond aplikací v panelu **Akce** vyberte **Upřesnit nastavení** v části Upravit **fond aplikací.**</span><span class="sxs-lookup"><span data-stu-id="c9a8b-581">Select **Advanced Settings** under **Edit Application Pool** in the **Actions** panel.</span></span>
1. <span data-ttu-id="c9a8b-582">Nastavit **povolit 32bitové aplikace**:</span><span class="sxs-lookup"><span data-stu-id="c9a8b-582">Set **Enable 32-Bit Applications**:</span></span>
   * <span data-ttu-id="c9a8b-583">Pokud nasazujete 32bitovou (x86) `True`aplikaci, nastavte hodnotu na .</span><span class="sxs-lookup"><span data-stu-id="c9a8b-583">If deploying a 32-bit (x86) app, set the value to `True`.</span></span>
   * <span data-ttu-id="c9a8b-584">Pokud nasazujete 64bitovou (x64) `False`aplikaci, nastavte hodnotu na .</span><span class="sxs-lookup"><span data-stu-id="c9a8b-584">If deploying a 64-bit (x64) app, set the value to `False`.</span></span>

<span data-ttu-id="c9a8b-585">Zkontrolujte, zda v souboru `<Platform>` projektu není konflikt mezi vlastností MSBuild a publikovanou bitness aplikace.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-585">Confirm that there isn't a conflict between a `<Platform>` MSBuild property in the project file and the published bitness of the app.</span></span>

### <a name="connection-reset"></a><span data-ttu-id="c9a8b-586">Resetování připojení</span><span class="sxs-lookup"><span data-stu-id="c9a8b-586">Connection reset</span></span>

<span data-ttu-id="c9a8b-587">Pokud dojde k chybě po odeslání záhlaví, je příliš pozdě na to, aby server odeslal **chybu interního serveru 500,** když dojde k chybě.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-587">If an error occurs after the headers are sent, it's too late for the server to send a **500 Internal Server Error** when an error occurs.</span></span> <span data-ttu-id="c9a8b-588">K tomu často dochází, když dojde k chybě během serializace složitých objektů pro odpověď.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-588">This often happens when an error occurs during the serialization of complex objects for a response.</span></span> <span data-ttu-id="c9a8b-589">Tento typ chyby se zobrazí jako chyba *obnovení připojení* v klientovi.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-589">This type of error appears as a *connection reset* error on the client.</span></span> <span data-ttu-id="c9a8b-590">[Protokolování aplikací](xref:fundamentals/logging/index) může pomoci při řešení těchto typů chyb.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-590">[Application logging](xref:fundamentals/logging/index) can help troubleshoot these types of errors.</span></span>

### <a name="default-startup-limits"></a><span data-ttu-id="c9a8b-591">Výchozí limity pro spuštění</span><span class="sxs-lookup"><span data-stu-id="c9a8b-591">Default startup limits</span></span>

<span data-ttu-id="c9a8b-592">[Základní modul ASP.NET](xref:host-and-deploy/aspnet-core-module) je nakonfigurován s výchozím *spouštěcím limitem* 120 sekund.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-592">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) is configured with a default *startupTimeLimit* of 120 seconds.</span></span> <span data-ttu-id="c9a8b-593">Pokud zůstane na výchozí hodnotu, aplikace může trvat až dvě minuty ke spuštění, než modul zaznamená selhání procesu.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-593">When left at the default value, an app may take up to two minutes to start before the module logs a process failure.</span></span> <span data-ttu-id="c9a8b-594">Informace o konfiguraci modulu naleznete v [tématu Atributy prvku aspNetCore](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span><span class="sxs-lookup"><span data-stu-id="c9a8b-594">For information on configuring the module, see [Attributes of the aspNetCore element](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span></span>

## <a name="troubleshoot-on-azure-app-service"></a><span data-ttu-id="c9a8b-595">Poradce při potížích se službou Azure App Service</span><span class="sxs-lookup"><span data-stu-id="c9a8b-595">Troubleshoot on Azure App Service</span></span>

[!INCLUDE [Azure App Service Preview Notice](~/includes/azure-apps-preview-notice.md)]

### <a name="application-event-log-azure-app-service"></a><span data-ttu-id="c9a8b-596">Protokol událostí aplikace (služba Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="c9a8b-596">Application Event Log (Azure App Service)</span></span>

<span data-ttu-id="c9a8b-597">Chcete-li získat přístup k protokolu událostí aplikace, použijte okno **Diagnostikovat a vyřešit problémy** na webu Azure Portal:</span><span class="sxs-lookup"><span data-stu-id="c9a8b-597">To access the Application Event Log, use the **Diagnose and solve problems** blade in the Azure portal:</span></span>

1. <span data-ttu-id="c9a8b-598">Na webu Azure Portal otevřete aplikaci ve **Službách aplikací**.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-598">In the Azure portal, open the app in **App Services**.</span></span>
1. <span data-ttu-id="c9a8b-599">Vyberte **Diagnostikovat a řešit problémy**.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-599">Select **Diagnose and solve problems**.</span></span>
1. <span data-ttu-id="c9a8b-600">Vyberte nadpis **Diagnostické nástroje.**</span><span class="sxs-lookup"><span data-stu-id="c9a8b-600">Select the **Diagnostic Tools** heading.</span></span>
1. <span data-ttu-id="c9a8b-601">V části **Nástroje podpory**vyberte tlačítko **Události aplikace.**</span><span class="sxs-lookup"><span data-stu-id="c9a8b-601">Under **Support Tools**, select the **Application Events** button.</span></span>
1. <span data-ttu-id="c9a8b-602">Prohlédněte si nejnovější chybu poskytovanou položkou *AspNetCoreModule* nebo *IIS AspNetCoreModule V2* ve sloupci **Zdroj.**</span><span class="sxs-lookup"><span data-stu-id="c9a8b-602">Examine the latest error provided by the *IIS AspNetCoreModule* or *IIS AspNetCoreModule V2* entry in the **Source** column.</span></span>

<span data-ttu-id="c9a8b-603">Alternativou k použití **funkce Diagnostika a řešení problémů** je prozkoumat soubor protokolu událostí aplikace přímo pomocí [kudu](https://github.com/projectkudu/kudu/wiki):</span><span class="sxs-lookup"><span data-stu-id="c9a8b-603">An alternative to using the **Diagnose and solve problems** blade is to examine the Application Event Log file directly using [Kudu](https://github.com/projectkudu/kudu/wiki):</span></span>

1. <span data-ttu-id="c9a8b-604">Otevřete **rozšířené nástroje** v oblasti **vývojových nástrojů.**</span><span class="sxs-lookup"><span data-stu-id="c9a8b-604">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="c9a8b-605">Vyberte tlačítko \*\*Přejít.&rarr; \*\*</span><span class="sxs-lookup"><span data-stu-id="c9a8b-605">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="c9a8b-606">Konzole Kudu se otevře v novém kartě prohlížeče nebo v okně.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-606">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="c9a8b-607">Pomocí navigačního panelu v horní části stránky otevřete **konzolu ladění** a vyberte **možnost CMD**.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-607">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="c9a8b-608">Otevřete složku **LogFiles.**</span><span class="sxs-lookup"><span data-stu-id="c9a8b-608">Open the **LogFiles** folder.</span></span>
1. <span data-ttu-id="c9a8b-609">Vyberte ikonu tužky vedle souboru *eventlog.xml.*</span><span class="sxs-lookup"><span data-stu-id="c9a8b-609">Select the pencil icon next to the *eventlog.xml* file.</span></span>
1. <span data-ttu-id="c9a8b-610">Prohlédněte si protokol.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-610">Examine the log.</span></span> <span data-ttu-id="c9a8b-611">Přejděte do dolní části protokolu zobrazíte nejnovější události.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-611">Scroll to the bottom of the log to see the most recent events.</span></span>

### <a name="run-the-app-in-the-kudu-console"></a><span data-ttu-id="c9a8b-612">Spuštění aplikace v konzoli Kudu</span><span class="sxs-lookup"><span data-stu-id="c9a8b-612">Run the app in the Kudu console</span></span>

<span data-ttu-id="c9a8b-613">Mnoho chyb při spuštění nevytváří užitečné informace v protokolu událostí aplikace.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-613">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="c9a8b-614">Aplikaci můžete spustit v konzole vzdáleného spuštění [Kudu](https://github.com/projectkudu/kudu/wiki) a zjistit chybu:</span><span class="sxs-lookup"><span data-stu-id="c9a8b-614">You can run the app in the [Kudu](https://github.com/projectkudu/kudu/wiki) Remote Execution Console to discover the error:</span></span>

1. <span data-ttu-id="c9a8b-615">Otevřete **rozšířené nástroje** v oblasti **vývojových nástrojů.**</span><span class="sxs-lookup"><span data-stu-id="c9a8b-615">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="c9a8b-616">Vyberte tlačítko \*\*Přejít.&rarr; \*\*</span><span class="sxs-lookup"><span data-stu-id="c9a8b-616">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="c9a8b-617">Konzole Kudu se otevře v novém kartě prohlížeče nebo v okně.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-617">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="c9a8b-618">Pomocí navigačního panelu v horní části stránky otevřete **konzolu ladění** a vyberte **možnost CMD**.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-618">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>

#### <a name="test-a-32-bit-x86-app"></a><span data-ttu-id="c9a8b-619">Testování 32bitové aplikace (x86)</span><span class="sxs-lookup"><span data-stu-id="c9a8b-619">Test a 32-bit (x86) app</span></span>

<span data-ttu-id="c9a8b-620">**Aktuální verze**</span><span class="sxs-lookup"><span data-stu-id="c9a8b-620">**Current release**</span></span>

1. `cd d:\home\site\wwwroot`
1. <span data-ttu-id="c9a8b-621">Spusťte aplikaci:</span><span class="sxs-lookup"><span data-stu-id="c9a8b-621">Run the app:</span></span>
   * <span data-ttu-id="c9a8b-622">Pokud je aplikace [nasazení závislé na rámci](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span><span class="sxs-lookup"><span data-stu-id="c9a8b-622">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

     ```dotnetcli
     dotnet .\{ASSEMBLY NAME}.dll
     ```

   * <span data-ttu-id="c9a8b-623">Pokud se jedná o [samostatné nasazení](/dotnet/core/deploying/#self-contained-deployments-scd)aplikace :</span><span class="sxs-lookup"><span data-stu-id="c9a8b-623">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

     ```console
     {ASSEMBLY NAME}.exe
     ```

<span data-ttu-id="c9a8b-624">Výstup konzoly z aplikace, v němž se zobrazují všechny chyby, je předán do konzoly Kudu.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-624">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="c9a8b-625">**Nasazení závislé na rozhraní spuštěné ve verzi preview**</span><span class="sxs-lookup"><span data-stu-id="c9a8b-625">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="c9a8b-626">*Vyžaduje instalaci rozšíření webu ASP.NET Core {VERSION} (x86).*</span><span class="sxs-lookup"><span data-stu-id="c9a8b-626">*Requires installing the ASP.NET Core {VERSION} (x86) Runtime site extension.*</span></span>

1. <span data-ttu-id="c9a8b-627">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32`(`{X.Y}` je runtime verze)</span><span class="sxs-lookup"><span data-stu-id="c9a8b-627">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="c9a8b-628">Spusťte aplikaci: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="c9a8b-628">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="c9a8b-629">Výstup konzoly z aplikace, v němž se zobrazují všechny chyby, je předán do konzoly Kudu.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-629">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

#### <a name="test-a-64-bit-x64-app"></a><span data-ttu-id="c9a8b-630">Testování 64bitové aplikace (x64)</span><span class="sxs-lookup"><span data-stu-id="c9a8b-630">Test a 64-bit (x64) app</span></span>

<span data-ttu-id="c9a8b-631">**Aktuální verze**</span><span class="sxs-lookup"><span data-stu-id="c9a8b-631">**Current release**</span></span>

* <span data-ttu-id="c9a8b-632">Pokud je aplikace 64bitové (x64) [nasazení závislé na rozhraní](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span><span class="sxs-lookup"><span data-stu-id="c9a8b-632">If the app is a 64-bit (x64) [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>
  1. `cd D:\Program Files\dotnet`
  1. <span data-ttu-id="c9a8b-633">Spusťte aplikaci: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="c9a8b-633">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>
* <span data-ttu-id="c9a8b-634">Pokud se jedná o [samostatné nasazení](/dotnet/core/deploying/#self-contained-deployments-scd)aplikace :</span><span class="sxs-lookup"><span data-stu-id="c9a8b-634">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>
  1. `cd D:\home\site\wwwroot`
  1. <span data-ttu-id="c9a8b-635">Spusťte aplikaci: `{ASSEMBLY NAME}.exe`</span><span class="sxs-lookup"><span data-stu-id="c9a8b-635">Run the app: `{ASSEMBLY NAME}.exe`</span></span>

<span data-ttu-id="c9a8b-636">Výstup konzoly z aplikace, v němž se zobrazují všechny chyby, je předán do konzoly Kudu.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-636">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="c9a8b-637">**Nasazení závislé na rozhraní spuštěné ve verzi preview**</span><span class="sxs-lookup"><span data-stu-id="c9a8b-637">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="c9a8b-638">*Vyžaduje instalaci rozšíření webu ASP.NET Core {VERSION} (x64).*</span><span class="sxs-lookup"><span data-stu-id="c9a8b-638">*Requires installing the ASP.NET Core {VERSION} (x64) Runtime site extension.*</span></span>

1. <span data-ttu-id="c9a8b-639">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64`(`{X.Y}` je runtime verze)</span><span class="sxs-lookup"><span data-stu-id="c9a8b-639">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="c9a8b-640">Spusťte aplikaci: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="c9a8b-640">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="c9a8b-641">Výstup konzoly z aplikace, v němž se zobrazují všechny chyby, je předán do konzoly Kudu.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-641">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

### <a name="aspnet-core-module-stdout-log-azure-app-service"></a><span data-ttu-id="c9a8b-642">ASP.NET protokol stdout modulu (služba Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="c9a8b-642">ASP.NET Core Module stdout log (Azure App Service)</span></span>

<span data-ttu-id="c9a8b-643">Protokol stdout ASP.NET Core Module často zaznamenává užitečné chybové zprávy, které nebyly nalezeny v protokolu událostí aplikace.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-643">The ASP.NET Core Module stdout log often records useful error messages not found in the Application Event Log.</span></span> <span data-ttu-id="c9a8b-644">Povolení a zobrazení protokolů stdout:</span><span class="sxs-lookup"><span data-stu-id="c9a8b-644">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="c9a8b-645">Přejděte na okno **Diagnostikovat a vyřešit problémy** na webu Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-645">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="c9a8b-646">V **části SELECT PROBLEM CATEGORY**vyberte tlačítko Web App **Down** .</span><span class="sxs-lookup"><span data-stu-id="c9a8b-646">Under **SELECT PROBLEM CATEGORY**, select the **Web App Down** button.</span></span>
1. <span data-ttu-id="c9a8b-647">V části **Navrhovaná řešení** > **Povolit přesměrování protokolu Stdout**vyberte tlačítko **pro otevření konzoly Kudu a upravte web.Config**.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-647">Under **Suggested Solutions** > **Enable Stdout Log Redirection**, select the button to **Open Kudu Console to edit Web.Config**.</span></span>
1. <span data-ttu-id="c9a8b-648">V **diagnostické konzole**Kudu otevřete složky webu **site** > **wwwroot**.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-648">In the Kudu **Diagnostic Console**, open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="c9a8b-649">Posunutím dolů zobrazíte soubor *web.config* v dolní části seznamu.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-649">Scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="c9a8b-650">Klikněte na ikonu tužky vedle souboru *web.config.*</span><span class="sxs-lookup"><span data-stu-id="c9a8b-650">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="c9a8b-651">Nastavte **stdoutLogEnabled** na `true` cestu **stdoutLogFile** `\\?\%home%\LogFiles\stdout`a změňte ji na: .</span><span class="sxs-lookup"><span data-stu-id="c9a8b-651">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="c9a8b-652">Vyberte **Uložit,** chcete-li uložit aktualizovaný soubor *web.config.*</span><span class="sxs-lookup"><span data-stu-id="c9a8b-652">Select **Save** to save the updated *web.config* file.</span></span>
1. <span data-ttu-id="c9a8b-653">Požádejte o to do aplikace.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-653">Make a request to the app.</span></span>
1. <span data-ttu-id="c9a8b-654">Vraťte se na Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-654">Return to the Azure portal.</span></span> <span data-ttu-id="c9a8b-655">Vyberte okno **Pokročilé nástroje** v oblasti **NÁSTROJE VÝVOJE.**</span><span class="sxs-lookup"><span data-stu-id="c9a8b-655">Select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="c9a8b-656">Vyberte tlačítko \*\*Přejít.&rarr; \*\*</span><span class="sxs-lookup"><span data-stu-id="c9a8b-656">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="c9a8b-657">Konzole Kudu se otevře v novém kartě prohlížeče nebo v okně.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-657">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="c9a8b-658">Pomocí navigačního panelu v horní části stránky otevřete **konzolu ladění** a vyberte **možnost CMD**.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-658">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="c9a8b-659">Vyberte složku **LogFiles.**</span><span class="sxs-lookup"><span data-stu-id="c9a8b-659">Select the **LogFiles** folder.</span></span>
1. <span data-ttu-id="c9a8b-660">Zkontrolujte sloupec **Změněno** a vyberte ikonu tužky a upravte protokol stdout s nejnovějším datem změny.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-660">Inspect the **Modified** column and select the pencil icon to edit the stdout log with the latest modification date.</span></span>
1. <span data-ttu-id="c9a8b-661">Po otevření souboru protokolu se zobrazí chyba.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-661">When the log file opens, the error is displayed.</span></span>

<span data-ttu-id="c9a8b-662">Zakažte protokolování stdout po dokončení řešení potíží:</span><span class="sxs-lookup"><span data-stu-id="c9a8b-662">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="c9a8b-663">V diagnostické **konzole**Kudu se vraťte na **web** > **wwwroot** cesty a odhalte soubor *web.config.*</span><span class="sxs-lookup"><span data-stu-id="c9a8b-663">In the Kudu **Diagnostic Console**, return to the path **site** > **wwwroot** to reveal the *web.config* file.</span></span> <span data-ttu-id="c9a8b-664">Znovu otevřete soubor **web.config** výběrem ikony tužky.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-664">Open the **web.config** file again by selecting the pencil icon.</span></span>
1. <span data-ttu-id="c9a8b-665">Nastavte **stdoutLogEnabled** na `false`.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-665">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="c9a8b-666">Chcete-li soubor uložit, vyberte **Uložit.**</span><span class="sxs-lookup"><span data-stu-id="c9a8b-666">Select **Save** to save the file.</span></span>

<span data-ttu-id="c9a8b-667">Další informace naleznete v tématu <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-667">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

> [!WARNING]
> <span data-ttu-id="c9a8b-668">Selhání zakázání protokolu stdout může vést k selhání aplikace nebo serveru.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-668">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="c9a8b-669">Neexistuje žádné omezení na velikost souboru protokolu nebo počet vytvořených souborů protokolu.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-669">There's no limit on log file size or the number of log files created.</span></span> <span data-ttu-id="c9a8b-670">K řešení problémů se spuštěním aplikace používejte protokolování stdout pouze.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-670">Only use stdout logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="c9a8b-671">Pro obecné protokolování v aplikaci ASP.NET Core po spuštění použijte knihovnu protokolování, která omezuje velikost souboru protokolu a otáčí protokoly.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-671">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="c9a8b-672">Další informace naleznete [v tématu zprostředkovatelé protokolování třetích stran](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="c9a8b-672">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="aspnet-core-module-debug-log-azure-app-service"></a><span data-ttu-id="c9a8b-673">protokol ladění ASP.NET základního modulu (služba Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="c9a8b-673">ASP.NET Core Module debug log (Azure App Service)</span></span>

<span data-ttu-id="c9a8b-674">Protokol ladění ASP.NET core modul poskytuje další, hlubší protokolování z ASP.NET core modulu.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-674">The ASP.NET Core Module debug log provides additional, deeper logging from the ASP.NET Core Module.</span></span> <span data-ttu-id="c9a8b-675">Povolení a zobrazení protokolů stdout:</span><span class="sxs-lookup"><span data-stu-id="c9a8b-675">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="c9a8b-676">Chcete-li povolit rozšířený diagnostický protokol, proveďte jednu z následujících akcí:</span><span class="sxs-lookup"><span data-stu-id="c9a8b-676">To enable the enhanced diagnostic log, perform either of the following:</span></span>
   * <span data-ttu-id="c9a8b-677">Podle pokynů v [rozšířené diagnostické protokoly](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) nakonfigurovat aplikaci pro rozšířené diagnostické protokolování.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-677">Follow the instructions in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) to configure the app for an enhanced diagnostic logging.</span></span> <span data-ttu-id="c9a8b-678">Znovu nasadit aplikaci.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-678">Redeploy the app.</span></span>
   * <span data-ttu-id="c9a8b-679">Přidejte `<handlerSettings>` zobrazené v [rozšířených diagnostických protokolech](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) do souboru *web.config* živé aplikace pomocí konzoly Kudu:</span><span class="sxs-lookup"><span data-stu-id="c9a8b-679">Add the `<handlerSettings>` shown in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) to the live app's *web.config* file using the Kudu console:</span></span>
     1. <span data-ttu-id="c9a8b-680">Otevřete **rozšířené nástroje** v oblasti **vývojových nástrojů.**</span><span class="sxs-lookup"><span data-stu-id="c9a8b-680">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="c9a8b-681">Vyberte tlačítko \*\*Přejít.&rarr; \*\*</span><span class="sxs-lookup"><span data-stu-id="c9a8b-681">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="c9a8b-682">Konzole Kudu se otevře v novém kartě prohlížeče nebo v okně.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-682">The Kudu console opens in a new browser tab or window.</span></span>
     1. <span data-ttu-id="c9a8b-683">Pomocí navigačního panelu v horní části stránky otevřete **konzolu ladění** a vyberte **možnost CMD**.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-683">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
     1. <span data-ttu-id="c9a8b-684">Otevřete složky na **webu** > cesty**wwwroot**.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-684">Open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="c9a8b-685">Upravte soubor *web.config* výběrem tlačítka tužky.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-685">Edit the *web.config* file by selecting the pencil button.</span></span> <span data-ttu-id="c9a8b-686">Přidejte `<handlerSettings>` oddíl, jak je znázorněno v [rozšířené diagnostické protokoly](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs).</span><span class="sxs-lookup"><span data-stu-id="c9a8b-686">Add the `<handlerSettings>` section as shown in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs).</span></span> <span data-ttu-id="c9a8b-687">Vyberte tlačítko **Uložit**.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-687">Select the **Save** button.</span></span>
1. <span data-ttu-id="c9a8b-688">Otevřete **rozšířené nástroje** v oblasti **vývojových nástrojů.**</span><span class="sxs-lookup"><span data-stu-id="c9a8b-688">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="c9a8b-689">Vyberte tlačítko \*\*Přejít.&rarr; \*\*</span><span class="sxs-lookup"><span data-stu-id="c9a8b-689">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="c9a8b-690">Konzole Kudu se otevře v novém kartě prohlížeče nebo v okně.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-690">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="c9a8b-691">Pomocí navigačního panelu v horní části stránky otevřete **konzolu ladění** a vyberte **možnost CMD**.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-691">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="c9a8b-692">Otevřete složky na **webu** > cesty**wwwroot**.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-692">Open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="c9a8b-693">Pokud jste nezadali cestu pro soubor *aspnetcore-debug.log,* soubor se zobrazí v seznamu.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-693">If you didn't supply a path for the *aspnetcore-debug.log* file, the file appears in the list.</span></span> <span data-ttu-id="c9a8b-694">Pokud jste zadali cestu, přejděte do umístění souboru protokolu.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-694">If you supplied a path, navigate to the location of the log file.</span></span>
1. <span data-ttu-id="c9a8b-695">Otevřete soubor protokolu pomocí tlačítka tužky vedle názvu souboru.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-695">Open the log file with the pencil button next to the file name.</span></span>

<span data-ttu-id="c9a8b-696">Zakažte protokolování ladění po dokončení řešení potíží:</span><span class="sxs-lookup"><span data-stu-id="c9a8b-696">Disable debug logging when troubleshooting is complete:</span></span>

<span data-ttu-id="c9a8b-697">Chcete-li protokol rozšířeného ladění zakázat, proveďte některou z následujících akcí:</span><span class="sxs-lookup"><span data-stu-id="c9a8b-697">To disable the enhanced debug log, perform either of the following:</span></span>

* <span data-ttu-id="c9a8b-698">Odeberte `<handlerSettings>` soubor *web.config* místně a znovu nasadit aplikaci.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-698">Remove the `<handlerSettings>` from the *web.config* file locally and redeploy the app.</span></span>
* <span data-ttu-id="c9a8b-699">Pomocí konzoly Kudu upravte soubor *web.config* a odeberte `<handlerSettings>` oddíl.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-699">Use the Kudu console to edit the *web.config* file and remove the `<handlerSettings>` section.</span></span> <span data-ttu-id="c9a8b-700">Uložte soubor.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-700">Save the file.</span></span>

<span data-ttu-id="c9a8b-701">Další informace naleznete v tématu <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-701">For more information, see <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span></span>

> [!WARNING]
> <span data-ttu-id="c9a8b-702">Selhání zakázání protokolu ladění může vést k selhání aplikace nebo serveru.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-702">Failure to disable the debug log can lead to app or server failure.</span></span> <span data-ttu-id="c9a8b-703">Neexistuje žádné omezení na velikost souboru protokolu.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-703">There's no limit on log file size.</span></span> <span data-ttu-id="c9a8b-704">Protokolování ladění používejte pouze k řešení potíží se spuštěním aplikace.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-704">Only use debug logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="c9a8b-705">Pro obecné protokolování v aplikaci ASP.NET Core po spuštění použijte knihovnu protokolování, která omezuje velikost souboru protokolu a otáčí protokoly.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-705">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="c9a8b-706">Další informace naleznete [v tématu zprostředkovatelé protokolování třetích stran](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="c9a8b-706">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="slow-or-hanging-app-azure-app-service"></a><span data-ttu-id="c9a8b-707">Pomalá nebo zavěšená aplikace (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="c9a8b-707">Slow or hanging app (Azure App Service)</span></span>

<span data-ttu-id="c9a8b-708">Když aplikace reaguje pomalu nebo se při žádosti zablokuje, přečtěte si následující články:</span><span class="sxs-lookup"><span data-stu-id="c9a8b-708">When an app responds slowly or hangs on a request, see the following articles:</span></span>

* [<span data-ttu-id="c9a8b-709">Řešení potíží s výkonem pomalých webových aplikací ve službě Azure App Service</span><span class="sxs-lookup"><span data-stu-id="c9a8b-709">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="c9a8b-710">Použití rozšíření webu Diagnostikátor selhání k zachycení výpisu pro občasné výjimky problémy nebo problémy s výkonem na Azure Web App</span><span class="sxs-lookup"><span data-stu-id="c9a8b-710">Use Crash Diagnoser Site Extension to Capture Dump for Intermittent Exception issues or performance issues on Azure Web App</span></span>](https://blogs.msdn.microsoft.com/asiatech/2015/12/28/use-crash-diagnoser-site-extension-to-capture-dump-for-intermittent-exception-issues-or-performance-issues-on-azure-web-app/)

### <a name="monitoring-blades"></a><span data-ttu-id="c9a8b-711">Monitorovací nože</span><span class="sxs-lookup"><span data-stu-id="c9a8b-711">Monitoring blades</span></span>

<span data-ttu-id="c9a8b-712">Monitorování blades poskytují alternativní řešení potíží k metodám popsaným výše v tématu.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-712">Monitoring blades provide an alternative troubleshooting experience to the methods described earlier in the topic.</span></span> <span data-ttu-id="c9a8b-713">Tyto čepele lze použít k diagnostice chyb řady 500.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-713">These blades can be used to diagnose 500-series errors.</span></span>

<span data-ttu-id="c9a8b-714">Zkontrolujte, zda jsou nainstalovány ASP.NET rozšíření core.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-714">Confirm that the ASP.NET Core Extensions are installed.</span></span> <span data-ttu-id="c9a8b-715">Pokud rozšíření nejsou nainstalovaná, nainstalujte je ručně:</span><span class="sxs-lookup"><span data-stu-id="c9a8b-715">If the extensions aren't installed, install them manually:</span></span>

1. <span data-ttu-id="c9a8b-716">V části **Čepele NÁSTROJE PRO VÝVOJ** vyberte okno **Rozšíření.**</span><span class="sxs-lookup"><span data-stu-id="c9a8b-716">In the **DEVELOPMENT TOOLS** blade section, select the **Extensions** blade.</span></span>
1. <span data-ttu-id="c9a8b-717">V seznamu by se měl **zobrazit ASP.NET rozšíření jádra.**</span><span class="sxs-lookup"><span data-stu-id="c9a8b-717">The **ASP.NET Core Extensions** should appear in the list.</span></span>
1. <span data-ttu-id="c9a8b-718">Pokud rozšíření nejsou nainstalovaná, vyberte tlačítko **Přidat.**</span><span class="sxs-lookup"><span data-stu-id="c9a8b-718">If the extensions aren't installed, select the **Add** button.</span></span>
1. <span data-ttu-id="c9a8b-719">Ze seznamu zvolte **ASP.NET jádrová rozšíření.**</span><span class="sxs-lookup"><span data-stu-id="c9a8b-719">Choose the **ASP.NET Core Extensions** from the list.</span></span>
1. <span data-ttu-id="c9a8b-720">Chcete-li přijmout zákonné podmínky, vyberte **možnost OK.**</span><span class="sxs-lookup"><span data-stu-id="c9a8b-720">Select **OK** to accept the legal terms.</span></span>
1. <span data-ttu-id="c9a8b-721">V okně **Přidat prodlužovací** okno vyberte **OK.**</span><span class="sxs-lookup"><span data-stu-id="c9a8b-721">Select **OK** on the **Add extension** blade.</span></span>
1. <span data-ttu-id="c9a8b-722">Informační zpráva o automaticky otevíraná okna označuje, kdy jsou rozšíření úspěšně nainstalována.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-722">An informational pop-up message indicates when the extensions are successfully installed.</span></span>

<span data-ttu-id="c9a8b-723">Pokud protokolování stdout není povoleno, postupujte takto:</span><span class="sxs-lookup"><span data-stu-id="c9a8b-723">If stdout logging isn't enabled, follow these steps:</span></span>

1. <span data-ttu-id="c9a8b-724">Na webu Azure Portal vyberte okno **Rozšířené nástroje** v oblasti NÁSTROJE **PRO VÝVOJ.**</span><span class="sxs-lookup"><span data-stu-id="c9a8b-724">In the Azure portal, select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="c9a8b-725">Vyberte tlačítko \*\*Přejít.&rarr; \*\*</span><span class="sxs-lookup"><span data-stu-id="c9a8b-725">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="c9a8b-726">Konzole Kudu se otevře v novém kartě prohlížeče nebo v okně.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-726">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="c9a8b-727">Pomocí navigačního panelu v horní části stránky otevřete **konzolu ladění** a vyberte **možnost CMD**.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-727">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="c9a8b-728">Otevřete složky na **webu** > cesty **wwwroot** a posuňte se dolů a odhalte soubor *web.config* v dolní části seznamu.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-728">Open the folders to the path **site** > **wwwroot** and scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="c9a8b-729">Klikněte na ikonu tužky vedle souboru *web.config.*</span><span class="sxs-lookup"><span data-stu-id="c9a8b-729">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="c9a8b-730">Nastavte **stdoutLogEnabled** na `true` cestu **stdoutLogFile** `\\?\%home%\LogFiles\stdout`a změňte ji na: .</span><span class="sxs-lookup"><span data-stu-id="c9a8b-730">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="c9a8b-731">Vyberte **Uložit,** chcete-li uložit aktualizovaný soubor *web.config.*</span><span class="sxs-lookup"><span data-stu-id="c9a8b-731">Select **Save** to save the updated *web.config* file.</span></span>

<span data-ttu-id="c9a8b-732">Postupujte k aktivaci diagnostického protokolování:</span><span class="sxs-lookup"><span data-stu-id="c9a8b-732">Proceed to activate diagnostic logging:</span></span>

1. <span data-ttu-id="c9a8b-733">Na webu Azure Portal vyberte okno **Protokoly diagnostiky.**</span><span class="sxs-lookup"><span data-stu-id="c9a8b-733">In the Azure portal, select the **Diagnostics logs** blade.</span></span>
1. <span data-ttu-id="c9a8b-734">Vyberte přepínač **Zapnuto** pro **protokolování aplikací (souborový systém)** a **Podrobné chybové zprávy**.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-734">Select the **On** switch for **Application Logging (Filesystem)** and **Detailed error messages**.</span></span> <span data-ttu-id="c9a8b-735">V horní části nože vyberte tlačítko **Uložit.**</span><span class="sxs-lookup"><span data-stu-id="c9a8b-735">Select the **Save** button at the top of the blade.</span></span>
1. <span data-ttu-id="c9a8b-736">Chcete-li zahrnout trasování neúspěšných požadavků, známé také jako protokolfreb (Failed Request Event Buffering), vyberte přepínač **Zapnuto** pro **trasování neúspěšných požadavků**.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-736">To include failed request tracing, also known as Failed Request Event Buffering (FREB) logging, select the **On** switch for **Failed request tracing**.</span></span>
1. <span data-ttu-id="c9a8b-737">Vyberte okno **protokolu datového proudu,** který je uveden okamžitě pod **protokoly protokoly diagnostiky** na portálu.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-737">Select the **Log stream** blade, which is listed immediately under the **Diagnostics logs** blade in the portal.</span></span>
1. <span data-ttu-id="c9a8b-738">Požádejte o to do aplikace.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-738">Make a request to the app.</span></span>
1. <span data-ttu-id="c9a8b-739">V rámci dat datového proudu protokolu je uvedena příčina chyby.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-739">Within the log stream data, the cause of the error is indicated.</span></span>

<span data-ttu-id="c9a8b-740">Nezapomeňte zakázat stdout protokolování po dokončení řešení potíží.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-740">Be sure to disable stdout logging when troubleshooting is complete.</span></span>

<span data-ttu-id="c9a8b-741">Zobrazení protokolů trasování neúspěšných požadavků (protokoly FREB):</span><span class="sxs-lookup"><span data-stu-id="c9a8b-741">To view the failed request tracing logs (FREB logs):</span></span>

1. <span data-ttu-id="c9a8b-742">Přejděte na okno **Diagnostikovat a vyřešit problémy** na webu Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-742">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="c9a8b-743">V yberte **Protokoly trasování neúspěšných požadavků** v oblasti **NÁSTROJE PODPORY** na postranním panelu.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-743">Select **Failed Request Tracing Logs** from the **SUPPORT TOOLS** area of the sidebar.</span></span>

<span data-ttu-id="c9a8b-744">Další informace [najdete v tématu Sledování neúspěšných požadavků v tématu Povolit diagnostiku pro webové aplikace v tématu Služby aplikací](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) a [v nejčastějších dotazech k výkonu aplikací pro webové aplikace v Azure: Jak zapnu trasování neúspěšných požadavků?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing)</span><span class="sxs-lookup"><span data-stu-id="c9a8b-744">See [Failed request traces section of the Enable diagnostics logging for web apps in Azure App Service topic](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) and the [Application performance FAQs for Web Apps in Azure: How do I turn on failed request tracing?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing) for more information.</span></span>

<span data-ttu-id="c9a8b-745">Další informace najdete [v tématu Povolení protokolování diagnostiky pro webové aplikace ve službě Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log).</span><span class="sxs-lookup"><span data-stu-id="c9a8b-745">For more information, see [Enable diagnostics logging for web apps in Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log).</span></span>

> [!WARNING]
> <span data-ttu-id="c9a8b-746">Selhání zakázání protokolu stdout může vést k selhání aplikace nebo serveru.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-746">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="c9a8b-747">Neexistuje žádné omezení na velikost souboru protokolu nebo počet vytvořených souborů protokolu.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-747">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="c9a8b-748">Pro rutinní protokolování v aplikaci ASP.NET Core použijte knihovnu protokolování, která omezuje velikost souboru protokolu a otáčí protokoly.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-748">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="c9a8b-749">Další informace naleznete [v tématu zprostředkovatelé protokolování třetích stran](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="c9a8b-749">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

## <a name="troubleshoot-on-iis"></a><span data-ttu-id="c9a8b-750">Odstraňování potíží ve službě IIS</span><span class="sxs-lookup"><span data-stu-id="c9a8b-750">Troubleshoot on IIS</span></span>

### <a name="application-event-log-iis"></a><span data-ttu-id="c9a8b-751">Protokol událostí aplikace (IIS)</span><span class="sxs-lookup"><span data-stu-id="c9a8b-751">Application Event Log (IIS)</span></span>

<span data-ttu-id="c9a8b-752">Přístup k protokolu událostí aplikace:</span><span class="sxs-lookup"><span data-stu-id="c9a8b-752">Access the Application Event Log:</span></span>

1. <span data-ttu-id="c9a8b-753">Otevřete nabídku Start, vyhledejte *Prohlížeč událostí*a vyberte aplikaci **Prohlížeč událostí.**</span><span class="sxs-lookup"><span data-stu-id="c9a8b-753">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="c9a8b-754">V **Prohlížeči událostí**otevřete uzel **Protokoly systému Windows.**</span><span class="sxs-lookup"><span data-stu-id="c9a8b-754">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="c9a8b-755">Výběrem **možnosti Aplikace** otevřete protokol událostí aplikace.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-755">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="c9a8b-756">Vyhledejte chyby spojené s aplikací se selháním.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-756">Search for errors associated with the failing app.</span></span> <span data-ttu-id="c9a8b-757">Chyby mají hodnotu *modulu AspNetCore služby IIS* nebo *modulu AspNetCore služby IIS Express* ve sloupci *Zdroj.*</span><span class="sxs-lookup"><span data-stu-id="c9a8b-757">Errors have a value of *IIS AspNetCore Module* or *IIS Express AspNetCore Module* in the *Source* column.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="c9a8b-758">Spuštění aplikace na příkazovém řádku</span><span class="sxs-lookup"><span data-stu-id="c9a8b-758">Run the app at a command prompt</span></span>

<span data-ttu-id="c9a8b-759">Mnoho chyb při spuštění nevytváří užitečné informace v protokolu událostí aplikace.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-759">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="c9a8b-760">Příčinu některých chyb můžete najít spuštěním aplikace na příkazovém řádku v hostitelském systému.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-760">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span>

#### <a name="framework-dependent-deployment"></a><span data-ttu-id="c9a8b-761">Nasazení závislé na rámci</span><span class="sxs-lookup"><span data-stu-id="c9a8b-761">Framework-dependent deployment</span></span>

<span data-ttu-id="c9a8b-762">Pokud je aplikace [nasazení závislé na rámci](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span><span class="sxs-lookup"><span data-stu-id="c9a8b-762">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

1. <span data-ttu-id="c9a8b-763">Na příkazovém řádku přejděte do složky nasazení a spusťte aplikaci spuštěním sestavení aplikace pomocí *dotnet.exe*.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-763">At a command prompt, navigate to the deployment folder and run the app by executing the app's assembly with *dotnet.exe*.</span></span> <span data-ttu-id="c9a8b-764">V následujícím příkazu nahraďte název sestavení \<aplikace assembly_name `dotnet .\<assembly_name>.dll`>: .</span><span class="sxs-lookup"><span data-stu-id="c9a8b-764">In the following command, substitute the name of the app's assembly for \<assembly_name>: `dotnet .\<assembly_name>.dll`.</span></span>
1. <span data-ttu-id="c9a8b-765">Výstup konzoly z aplikace, zobrazující případné chyby, je zapsán do okna konzoly.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-765">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="c9a8b-766">Pokud dojde k chybám při podání požadavku na aplikaci, podat žádost na hostitele a port, kde Kestrel naslouchá.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-766">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="c9a8b-767">Pomocí výchozího hostitele a příspěvku `http://localhost:5000/`požádejte o místo .</span><span class="sxs-lookup"><span data-stu-id="c9a8b-767">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="c9a8b-768">Pokud aplikace reaguje normálně na adrese koncového bodu Kestrel, problém je pravděpodobnější související s konfigurací hostování a méně pravděpodobné v rámci aplikace.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-768">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

#### <a name="self-contained-deployment"></a><span data-ttu-id="c9a8b-769">Samostatné nasazení</span><span class="sxs-lookup"><span data-stu-id="c9a8b-769">Self-contained deployment</span></span>

<span data-ttu-id="c9a8b-770">Pokud se jedná o [samostatné nasazení](/dotnet/core/deploying/#self-contained-deployments-scd)aplikace :</span><span class="sxs-lookup"><span data-stu-id="c9a8b-770">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

1. <span data-ttu-id="c9a8b-771">Na příkazovém řádku přejděte do složky nasazení a spusťte spustitelný soubor aplikace.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-771">At a command prompt, navigate to the deployment folder and run the app's executable.</span></span> <span data-ttu-id="c9a8b-772">V následujícím příkazu nahraďte název sestavení \<aplikace assembly_name `<assembly_name>.exe`>: .</span><span class="sxs-lookup"><span data-stu-id="c9a8b-772">In the following command, substitute the name of the app's assembly for \<assembly_name>: `<assembly_name>.exe`.</span></span>
1. <span data-ttu-id="c9a8b-773">Výstup konzoly z aplikace, zobrazující případné chyby, je zapsán do okna konzoly.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-773">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="c9a8b-774">Pokud dojde k chybám při podání požadavku na aplikaci, podat žádost na hostitele a port, kde Kestrel naslouchá.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-774">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="c9a8b-775">Pomocí výchozího hostitele a příspěvku `http://localhost:5000/`požádejte o místo .</span><span class="sxs-lookup"><span data-stu-id="c9a8b-775">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="c9a8b-776">Pokud aplikace reaguje normálně na adrese koncového bodu Kestrel, problém je pravděpodobnější související s konfigurací hostování a méně pravděpodobné v rámci aplikace.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-776">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

### <a name="aspnet-core-module-stdout-log-iis"></a><span data-ttu-id="c9a8b-777">ASP.NET protokol stdout modulu (IIS)</span><span class="sxs-lookup"><span data-stu-id="c9a8b-777">ASP.NET Core Module stdout log (IIS)</span></span>

<span data-ttu-id="c9a8b-778">Povolení a zobrazení protokolů stdout:</span><span class="sxs-lookup"><span data-stu-id="c9a8b-778">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="c9a8b-779">Přejděte do složky nasazení webu v hostitelském systému.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-779">Navigate to the site's deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="c9a8b-780">Pokud složka *protokolů* není k dispozici, vytvořte složku.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-780">If the *logs* folder isn't present, create the folder.</span></span> <span data-ttu-id="c9a8b-781">Pokyny k povolení msbuild vytvořit složku *protokolů* v nasazení automaticky, naleznete v tématu [directory struktura.](xref:host-and-deploy/directory-structure)</span><span class="sxs-lookup"><span data-stu-id="c9a8b-781">For instructions on how to enable MSBuild to create the *logs* folder in the deployment automatically, see the [Directory structure](xref:host-and-deploy/directory-structure) topic.</span></span>
1. <span data-ttu-id="c9a8b-782">Upravte soubor *web.config.*</span><span class="sxs-lookup"><span data-stu-id="c9a8b-782">Edit the *web.config* file.</span></span> <span data-ttu-id="c9a8b-783">Nastavte **stdoutLogEnabled** a `true` změňte cestu **stdoutLogFile** tak, `.\logs\stdout`aby přecózovala na složku *protokolů* (například ).</span><span class="sxs-lookup"><span data-stu-id="c9a8b-783">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to point to the *logs* folder (for example, `.\logs\stdout`).</span></span> <span data-ttu-id="c9a8b-784">`stdout`v cestě je předpona názvu souboru protokolu.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-784">`stdout` in the path is the log file name prefix.</span></span> <span data-ttu-id="c9a8b-785">Časové razítko, ID procesu a přípona souboru jsou přidány automaticky při vytvoření protokolu.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-785">A timestamp, process id, and file extension are added automatically when the log is created.</span></span> <span data-ttu-id="c9a8b-786">Jako `stdout` předpona názvu souboru je typický soubor protokolu pojmenován *stdout_20180205184032_5412.log*.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-786">Using `stdout` as the file name prefix, a typical log file is named *stdout_20180205184032_5412.log*.</span></span>
1. <span data-ttu-id="c9a8b-787">Ujistěte se, že identita fondu aplikací má oprávnění k zápisu do složky *protokolů.*</span><span class="sxs-lookup"><span data-stu-id="c9a8b-787">Ensure your application pool's identity has write permissions to the *logs* folder.</span></span>
1. <span data-ttu-id="c9a8b-788">Uložte aktualizovaný soubor *web.config.*</span><span class="sxs-lookup"><span data-stu-id="c9a8b-788">Save the updated *web.config* file.</span></span>
1. <span data-ttu-id="c9a8b-789">Požádejte o to do aplikace.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-789">Make a request to the app.</span></span>
1. <span data-ttu-id="c9a8b-790">Přejděte do složky *protokolů.*</span><span class="sxs-lookup"><span data-stu-id="c9a8b-790">Navigate to the *logs* folder.</span></span> <span data-ttu-id="c9a8b-791">Vyhledejte a otevřete nejnovější protokol stdout.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-791">Find and open the most recent stdout log.</span></span>
1. <span data-ttu-id="c9a8b-792">Prostudujte si protokol pro chyby.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-792">Study the log for errors.</span></span>

<span data-ttu-id="c9a8b-793">Zakažte protokolování stdout po dokončení řešení potíží:</span><span class="sxs-lookup"><span data-stu-id="c9a8b-793">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="c9a8b-794">Upravte soubor *web.config.*</span><span class="sxs-lookup"><span data-stu-id="c9a8b-794">Edit the *web.config* file.</span></span>
1. <span data-ttu-id="c9a8b-795">Nastavte **stdoutLogEnabled** na `false`.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-795">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="c9a8b-796">Uložte soubor.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-796">Save the file.</span></span>

<span data-ttu-id="c9a8b-797">Další informace naleznete v tématu <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-797">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

> [!WARNING]
> <span data-ttu-id="c9a8b-798">Selhání zakázání protokolu stdout může vést k selhání aplikace nebo serveru.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-798">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="c9a8b-799">Neexistuje žádné omezení na velikost souboru protokolu nebo počet vytvořených souborů protokolu.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-799">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="c9a8b-800">Pro rutinní protokolování v aplikaci ASP.NET Core použijte knihovnu protokolování, která omezuje velikost souboru protokolu a otáčí protokoly.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-800">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="c9a8b-801">Další informace naleznete [v tématu zprostředkovatelé protokolování třetích stran](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="c9a8b-801">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="aspnet-core-module-debug-log-iis"></a><span data-ttu-id="c9a8b-802">protokol ladění základního modulu ASP.NET (IIS)</span><span class="sxs-lookup"><span data-stu-id="c9a8b-802">ASP.NET Core Module debug log (IIS)</span></span>

<span data-ttu-id="c9a8b-803">Přidejte do souboru *web.config* aplikace následující nastavení obslužné rutiny, která umožní ASP.NET protokol ladění základního modulu:</span><span class="sxs-lookup"><span data-stu-id="c9a8b-803">Add the following handler settings to the app's *web.config* file to enable ASP.NET Core Module debug log:</span></span>

```xml
<aspNetCore ...>
  <handlerSettings>
    <handlerSetting name="debugLevel" value="file" />
    <handlerSetting name="debugFile" value="c:\temp\ancm.log" />
  </handlerSettings>
</aspNetCore>
```

<span data-ttu-id="c9a8b-804">Zkontrolujte, zda existuje cesta zadaná pro protokol a zda má identita fondu aplikací oprávnění k zápisu do umístění.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-804">Confirm that the path specified for the log exists and that the app pool's identity has write permissions to the location.</span></span>

<span data-ttu-id="c9a8b-805">Další informace naleznete v tématu <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-805">For more information, see <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span></span>

### <a name="enable-the-developer-exception-page"></a><span data-ttu-id="c9a8b-806">Povolení stránky výjimky pro vývojáře</span><span class="sxs-lookup"><span data-stu-id="c9a8b-806">Enable the Developer Exception Page</span></span>

<span data-ttu-id="c9a8b-807">`ASPNETCORE_ENVIRONMENT` [Proměnnou prostředí lze přidat do web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) pro spuštění aplikace ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-807">The `ASPNETCORE_ENVIRONMENT` [environment variable can be added to web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) to run the app in the Development environment.</span></span> <span data-ttu-id="c9a8b-808">Pokud prostředí není přepsáno při spuštění `UseEnvironment` aplikace tvůrcem hostitele, nastavení proměnné prostředí umožňuje, aby se [stránka výjimky pro vývojáře](xref:fundamentals/error-handling) zobrazila při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-808">As long as the environment isn't overridden in app startup by `UseEnvironment` on the host builder, setting the environment variable allows the [Developer Exception Page](xref:fundamentals/error-handling) to appear when the app is run.</span></span>

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

<span data-ttu-id="c9a8b-809">Nastavení proměnné prostředí `ASPNETCORE_ENVIRONMENT` pro se doporučuje pouze pro použití na pracovních a testovacích serverech, které nejsou vystaveny Internetu.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-809">Setting the environment variable for `ASPNETCORE_ENVIRONMENT` is only recommended for use on staging and testing servers that aren't exposed to the Internet.</span></span> <span data-ttu-id="c9a8b-810">Po odstranění potíží odeberte proměnnou prostředí ze souboru *web.config.*</span><span class="sxs-lookup"><span data-stu-id="c9a8b-810">Remove the environment variable from the *web.config* file after troubleshooting.</span></span> <span data-ttu-id="c9a8b-811">Informace o nastavení proměnných prostředí v *souboru web.config*naleznete v [tématu podřízený prvek environmentVariables aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).</span><span class="sxs-lookup"><span data-stu-id="c9a8b-811">For information on setting environment variables in *web.config*, see [environmentVariables child element of aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).</span></span>

### <a name="obtain-data-from-an-app"></a><span data-ttu-id="c9a8b-812">Získání dat z aplikace</span><span class="sxs-lookup"><span data-stu-id="c9a8b-812">Obtain data from an app</span></span>

<span data-ttu-id="c9a8b-813">Pokud je aplikace schopná reagovat na požadavky, získejte požadavek, připojení a další data z aplikace pomocí terminálového inline middlewaru.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-813">If an app is capable of responding to requests, obtain request, connection, and additional data from the app using terminal inline middleware.</span></span> <span data-ttu-id="c9a8b-814">Další informace a ukázkový <xref:test/troubleshoot#obtain-data-from-an-app>kód naleznete v tématu .</span><span class="sxs-lookup"><span data-stu-id="c9a8b-814">For more information and sample code, see <xref:test/troubleshoot#obtain-data-from-an-app>.</span></span>

### <a name="slow-or-hanging-app-iis"></a><span data-ttu-id="c9a8b-815">Pomalá nebo závěsná aplikace (IIS)</span><span class="sxs-lookup"><span data-stu-id="c9a8b-815">Slow or hanging app (IIS)</span></span>

<span data-ttu-id="c9a8b-816">*Výpis stavu systému* je snímek paměti systému a může pomoci určit příčinu selhání aplikace, selhání při spuštění nebo pomalé aplikace.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-816">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="c9a8b-817">Aplikace dojde k chybě nebo narazí na výjimku</span><span class="sxs-lookup"><span data-stu-id="c9a8b-817">App crashes or encounters an exception</span></span>

<span data-ttu-id="c9a8b-818">Získání a analýza výpisu z [programu Zasílání zpráv o chybách systému Windows (WER)](/windows/desktop/wer/windows-error-reporting):</span><span class="sxs-lookup"><span data-stu-id="c9a8b-818">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="c9a8b-819">Vytvořte složku, ve které `c:\dumps`budou uloženy soubory s výpisem stavu systému .</span><span class="sxs-lookup"><span data-stu-id="c9a8b-819">Create a folder to hold crash dump files at `c:\dumps`.</span></span> <span data-ttu-id="c9a8b-820">Fond aplikací musí mít přístup pro zápis do složky.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-820">The app pool must have write access to the folder.</span></span>
1. <span data-ttu-id="c9a8b-821">Spusťte [skript EnableDumps PowerShell](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1):</span><span class="sxs-lookup"><span data-stu-id="c9a8b-821">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1):</span></span>
   * <span data-ttu-id="c9a8b-822">Pokud aplikace používá [model hostování v procesu](xref:host-and-deploy/iis/index#in-process-hosting-model), spusťte skript pro *w3wp.exe*:</span><span class="sxs-lookup"><span data-stu-id="c9a8b-822">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\EnableDumps w3wp.exe c:\dumps
     ```

   * <span data-ttu-id="c9a8b-823">Pokud aplikace používá [mimoprocesový model hostování](xref:host-and-deploy/iis/index#out-of-process-hosting-model), spusťte skript pro *dotnet.exe*:</span><span class="sxs-lookup"><span data-stu-id="c9a8b-823">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\EnableDumps dotnet.exe c:\dumps
     ```

1. <span data-ttu-id="c9a8b-824">Spusťte aplikaci za podmínek, které způsobují selhání dojít.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-824">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="c9a8b-825">Po selhání spusťte [skript DisableDumps PowerShell](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1):</span><span class="sxs-lookup"><span data-stu-id="c9a8b-825">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1):</span></span>
   * <span data-ttu-id="c9a8b-826">Pokud aplikace používá [model hostování v procesu](xref:host-and-deploy/iis/index#in-process-hosting-model), spusťte skript pro *w3wp.exe*:</span><span class="sxs-lookup"><span data-stu-id="c9a8b-826">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\DisableDumps w3wp.exe
     ```

   * <span data-ttu-id="c9a8b-827">Pokud aplikace používá [mimoprocesový model hostování](xref:host-and-deploy/iis/index#out-of-process-hosting-model), spusťte skript pro *dotnet.exe*:</span><span class="sxs-lookup"><span data-stu-id="c9a8b-827">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\DisableDumps dotnet.exe
     ```

<span data-ttu-id="c9a8b-828">Po zhroucení aplikace a výpis kolekce je kompletní, aplikace je povoleno ukončit normálně.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-828">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="c9a8b-829">Skript PowerShell udává WER tak, aby shromažďoval až pět výpisů v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-829">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="c9a8b-830">Výpisy stavu systému může zabírají velké množství místa na disku (až několik gigabajtů každý).</span><span class="sxs-lookup"><span data-stu-id="c9a8b-830">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="c9a8b-831">Aplikace přestane reagovat, selže při spuštění nebo běží normálně</span><span class="sxs-lookup"><span data-stu-id="c9a8b-831">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="c9a8b-832">Když aplikace *přestane reagovat* (přestane reagovat, ale nezhroutí se), selže při spuštění nebo běží normálně, najdete v [tématu Soubory výpisu uživatelského režimu: Výběr nejvhodnějšího nástroje](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) pro výběr vhodného nástroje pro vytvoření výpisu.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-832">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="c9a8b-833">Analyzovat výpis</span><span class="sxs-lookup"><span data-stu-id="c9a8b-833">Analyze the dump</span></span>

<span data-ttu-id="c9a8b-834">Výpis lze analyzovat pomocí několika přístupů.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-834">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="c9a8b-835">Další informace naleznete [v tématu Analýza souboru s výpisem stavu uživatelského režimu](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span><span class="sxs-lookup"><span data-stu-id="c9a8b-835">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="clear-package-caches"></a><span data-ttu-id="c9a8b-836">Vymazat mezipaměti balíčků</span><span class="sxs-lookup"><span data-stu-id="c9a8b-836">Clear package caches</span></span>

<span data-ttu-id="c9a8b-837">Funkční aplikace může selhat ihned po upgradu sady .NET Core SDK ve vývojovém počítači nebo změně verze balíčku v rámci aplikace.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-837">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="c9a8b-838">V některých případech nesouvislé balíčky může přerušit aplikaci při provádění hlavních upgradů.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-838">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="c9a8b-839">Většinu těchto problémů lze opravit podle následujících pokynů:</span><span class="sxs-lookup"><span data-stu-id="c9a8b-839">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="c9a8b-840">Odstraňte složky *bin* a *obj.*</span><span class="sxs-lookup"><span data-stu-id="c9a8b-840">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="c9a8b-841">Vymazat mezipaměti balíčku spuštěním [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) z příkazového prostředí.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-841">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="c9a8b-842">Vymazání mezipaměti balíčků lze také provést pomocí nástroje [nuget.exe](https://www.nuget.org/downloads) a spuštěnípříkazu `nuget locals all -clear`.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-842">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="c9a8b-843">*nuget.exe* není přibalená instalace s operačním systémem Windows desktop a musí být získány odděleně od [webu NuGet](https://www.nuget.org/downloads).</span><span class="sxs-lookup"><span data-stu-id="c9a8b-843">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="c9a8b-844">Obnovte a znovu vytvořte projekt.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-844">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="c9a8b-845">Před opětovnou nasazením aplikace odstraňte všechny soubory ve složce nasazení na serveru.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-845">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c9a8b-846">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="c9a8b-846">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:fundamentals/error-handling>
* <xref:host-and-deploy/aspnet-core-module>

### <a name="azure-documentation"></a><span data-ttu-id="c9a8b-847">Dokumentace k Azure</span><span class="sxs-lookup"><span data-stu-id="c9a8b-847">Azure documentation</span></span>

* [<span data-ttu-id="c9a8b-848">Application Insights pro ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c9a8b-848">Application Insights for ASP.NET Core</span></span>](/azure/application-insights/app-insights-asp-net-core)
* [<span data-ttu-id="c9a8b-849">Část Řešení potíží s webovou aplikací ve službě Azure App Service ve vzdáleném ladění webových aplikací pomocí Visual Studia</span><span class="sxs-lookup"><span data-stu-id="c9a8b-849">Remote debugging web apps section of Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug)
* [<span data-ttu-id="c9a8b-850">Přehled diagnostiky služby Azure App Service</span><span class="sxs-lookup"><span data-stu-id="c9a8b-850">Azure App Service diagnostics overview</span></span>](/azure/app-service/app-service-diagnostics)
* [<span data-ttu-id="c9a8b-851">Postup: Monitorování aplikací ve službě Azure App Service</span><span class="sxs-lookup"><span data-stu-id="c9a8b-851">How to: Monitor Apps in Azure App Service</span></span>](/azure/app-service/web-sites-monitor)
* [<span data-ttu-id="c9a8b-852">Poradce při potížích s webovou aplikací ve službě Azure App Service pomocí Visual Studia</span><span class="sxs-lookup"><span data-stu-id="c9a8b-852">Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [<span data-ttu-id="c9a8b-853">Poradce při potížích s chybami HTTP "502 chybná brána" a "503 služba není k dispozici" ve svých webových aplikacích Azure</span><span class="sxs-lookup"><span data-stu-id="c9a8b-853">Troubleshoot HTTP errors of "502 bad gateway" and "503 service unavailable" in your Azure web apps</span></span>](/azure/app-service/app-service-web-troubleshoot-http-502-http-503)
* [<span data-ttu-id="c9a8b-854">Řešení potíží s výkonem pomalých webových aplikací ve službě Azure App Service</span><span class="sxs-lookup"><span data-stu-id="c9a8b-854">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="c9a8b-855">Časté otázky k výkonu aplikací pro webové aplikace v Azure</span><span class="sxs-lookup"><span data-stu-id="c9a8b-855">Application performance FAQs for Web Apps in Azure</span></span>](/azure/app-service/app-service-web-availability-performance-application-issues-faq)
* [<span data-ttu-id="c9a8b-856">Azure Web App izolovaného prostoru (omezení spuštění služby App Service)</span><span class="sxs-lookup"><span data-stu-id="c9a8b-856">Azure Web App sandbox (App Service runtime execution limitations)</span></span>](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)
* [<span data-ttu-id="c9a8b-857">Azure Friday: Azure App Service Diagnostic and Troubleshooting Experience (12minutové video)</span><span class="sxs-lookup"><span data-stu-id="c9a8b-857">Azure Friday: Azure App Service Diagnostic and Troubleshooting Experience (12-minute video)</span></span>](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)

### <a name="visual-studio-documentation"></a><span data-ttu-id="c9a8b-858">Dokumentace sady Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c9a8b-858">Visual Studio documentation</span></span>

* [<span data-ttu-id="c9a8b-859">Vzdálené ladění ASP.NET jádro ve službě IIS v Azure ve Visual Studiu 2017</span><span class="sxs-lookup"><span data-stu-id="c9a8b-859">Remote Debug ASP.NET Core on IIS in Azure in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-azure)
* [<span data-ttu-id="c9a8b-860">Vzdálené ladění ASP.NET jádra ve vzdáleném počítači služby IIS v sadě Visual Studio 2017</span><span class="sxs-lookup"><span data-stu-id="c9a8b-860">Remote Debug ASP.NET Core on a Remote IIS Computer in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-aspnet-on-a-remote-iis-computer)
* [<span data-ttu-id="c9a8b-861">Informace o ladění pomocí sady Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c9a8b-861">Learn to debug using Visual Studio</span></span>](/visualstudio/debugger/getting-started-with-the-debugger)

### <a name="visual-studio-code-documentation"></a><span data-ttu-id="c9a8b-862">Dokumentace kódu visual studia</span><span class="sxs-lookup"><span data-stu-id="c9a8b-862">Visual Studio Code documentation</span></span>

* [<span data-ttu-id="c9a8b-863">Ladění pomocí kódu sady Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c9a8b-863">Debugging with Visual Studio Code</span></span>](https://code.visualstudio.com/docs/editor/debugging)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="c9a8b-864">Tento článek obsahuje informace o běžných chybách při spuštění aplikace a pokyny, jak diagnostikovat chyby při nasazení aplikace do služby Azure App Service nebo IIS:</span><span class="sxs-lookup"><span data-stu-id="c9a8b-864">This article provides information on common app startup errors and instructions on how to diagnose errors when an app is deployed to Azure App Service or IIS:</span></span>

[<span data-ttu-id="c9a8b-865">Chyby při spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="c9a8b-865">App startup errors</span></span>](#app-startup-errors)  
<span data-ttu-id="c9a8b-866">Vysvětluje běžné scénáře stavového kódu HTTP při spuštění.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-866">Explains common startup HTTP status code scenarios.</span></span>

[<span data-ttu-id="c9a8b-867">Poradce při potížích se službou Azure App Service</span><span class="sxs-lookup"><span data-stu-id="c9a8b-867">Troubleshoot on Azure App Service</span></span>](#troubleshoot-on-azure-app-service)  
<span data-ttu-id="c9a8b-868">Poskytuje rady pro řešení potíží pro aplikace nasazené do služby Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-868">Provides troubleshooting advice for apps deployed to Azure App Service.</span></span>

[<span data-ttu-id="c9a8b-869">Odstraňování potíží ve službě IIS</span><span class="sxs-lookup"><span data-stu-id="c9a8b-869">Troubleshoot on IIS</span></span>](#troubleshoot-on-iis)  
<span data-ttu-id="c9a8b-870">Poskytuje rady pro řešení potíží s aplikacemi nasazenými do služby IIS nebo spuštěných místně ve službě IIS Express.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-870">Provides troubleshooting advice for apps deployed to IIS or running on IIS Express locally.</span></span> <span data-ttu-id="c9a8b-871">Pokyny platí pro nasazení plochy systému Windows Server i Windows.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-871">The guidance applies to both Windows Server and Windows desktop deployments.</span></span>

[<span data-ttu-id="c9a8b-872">Vymazat mezipaměti balíčků</span><span class="sxs-lookup"><span data-stu-id="c9a8b-872">Clear package caches</span></span>](#clear-package-caches)  
<span data-ttu-id="c9a8b-873">Vysvětluje, co dělat, když nesouvislé balíčky přerušit aplikaci při provádění hlavních upgradů nebo změny verze balíčku.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-873">Explains what to do when incoherent packages break an app when performing major upgrades or changing package versions.</span></span>

[<span data-ttu-id="c9a8b-874">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="c9a8b-874">Additional resources</span></span>](#additional-resources)  
<span data-ttu-id="c9a8b-875">Obsahuje další témata pro řešení potíží.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-875">Lists additional troubleshooting topics.</span></span>

## <a name="app-startup-errors"></a><span data-ttu-id="c9a8b-876">Chyby při spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="c9a8b-876">App startup errors</span></span>

<span data-ttu-id="c9a8b-877">V sadě Visual Studio, ASP.NET projektu Core výchozí [iis express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hostování během ladění.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-877">In Visual Studio, an ASP.NET Core project defaults to [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hosting during debugging.</span></span> <span data-ttu-id="c9a8b-878">*502.5 Selhání procesu,* ke kterému dochází při místním ladění lze diagnostikovat pomocí rady v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-878">A *502.5 Process Failure* that occurs when debugging locally can be diagnosed using the advice in this topic.</span></span>

### <a name="40314-forbidden"></a><span data-ttu-id="c9a8b-879">403.14 Zakázáno</span><span class="sxs-lookup"><span data-stu-id="c9a8b-879">403.14 Forbidden</span></span>

<span data-ttu-id="c9a8b-880">Aplikace se nespustí.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-880">The app fails to start.</span></span> <span data-ttu-id="c9a8b-881">Je zaznamenána následující chyba:</span><span class="sxs-lookup"><span data-stu-id="c9a8b-881">The following error is logged:</span></span>

```
The Web server is configured to not list the contents of this directory.
```

<span data-ttu-id="c9a8b-882">Chyba je obvykle způsobena přerušeným nasazením v hostitelském systému, který zahrnuje některý z následujících scénářů:</span><span class="sxs-lookup"><span data-stu-id="c9a8b-882">The error is usually caused by a broken deployment on the hosting system, which includes any of the following scenarios:</span></span>

* <span data-ttu-id="c9a8b-883">Aplikace je nasazena do nesprávné složky v hostitelském systému.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-883">The app is deployed to the wrong folder on the hosting system.</span></span>
* <span data-ttu-id="c9a8b-884">Procesu nasazení se nepodařilo přesunout všechny soubory a složky aplikace do složky nasazení v hostitelském systému.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-884">The deployment process failed to move all of the app's files and folders to the deployment folder on the hosting system.</span></span>
* <span data-ttu-id="c9a8b-885">Soubor *web.config* chybí v nasazení nebo je obsah souboru *web.config* poškozen.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-885">The *web.config* file is missing from the deployment, or the *web.config* file contents are malformed.</span></span>

<span data-ttu-id="c9a8b-886">Proveďte následující kroky:</span><span class="sxs-lookup"><span data-stu-id="c9a8b-886">Perform the following steps:</span></span>

1. <span data-ttu-id="c9a8b-887">Odstraňte všechny soubory a složky ze složky nasazení v hostitelském systému.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-887">Delete all of the files and folders from the deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="c9a8b-888">Znovu nasadit obsah složky *publikování* aplikace do hostitelského systému pomocí normální metody nasazení, jako je Visual Studio, PowerShell nebo ruční nasazení:</span><span class="sxs-lookup"><span data-stu-id="c9a8b-888">Redeploy the contents of the app's *publish* folder to the hosting system using your normal method of deployment, such as Visual Studio, PowerShell, or manual deployment:</span></span>
   * <span data-ttu-id="c9a8b-889">Zkontrolujte, zda je soubor *web.config* v nasazení přítomen a zda je jeho obsah správný.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-889">Confirm that the *web.config* file is present in the deployment and that its contents are correct.</span></span>
   * <span data-ttu-id="c9a8b-890">Při hostování ve službě Azure App Service zkontrolujte, že se aplikace nasadí do `D:\home\site\wwwroot` složky.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-890">When hosting on Azure App Service, confirm that the app is deployed to the `D:\home\site\wwwroot` folder.</span></span>
   * <span data-ttu-id="c9a8b-891">Pokud je aplikace hostována službou IIS, zkontrolujte, zda je aplikace nasazena do **fyzické cesty** služby IIS zobrazené v **základním nastavení** **správce služby IIS**.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-891">When the app is hosted by IIS, confirm that the app is deployed to the IIS **Physical path** shown in **IIS Manager**'s **Basic Settings**.</span></span>
1. <span data-ttu-id="c9a8b-892">Zkontrolujte, zda jsou všechny soubory a složky aplikace nasazeny porovnáním nasazení v hostitelském systému s obsahem složky *publikování* projektu.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-892">Confirm that all of the app's files and folders are deployed by comparing the deployment on the hosting system to the contents of the project's *publish* folder.</span></span>

<span data-ttu-id="c9a8b-893">Další informace o rozložení publikované aplikace ASP.NET <xref:host-and-deploy/directory-structure>Core najdete v tématu .</span><span class="sxs-lookup"><span data-stu-id="c9a8b-893">For more information on the layout of a published ASP.NET Core app, see <xref:host-and-deploy/directory-structure>.</span></span> <span data-ttu-id="c9a8b-894">Další informace o souboru *web.config* naleznete v tématu <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-894">For more information on the *web.config* file, see <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>.</span></span>

### <a name="500-internal-server-error"></a><span data-ttu-id="c9a8b-895">500 – Interní chyba serveru</span><span class="sxs-lookup"><span data-stu-id="c9a8b-895">500 Internal Server Error</span></span>

<span data-ttu-id="c9a8b-896">Aplikace se spustí, ale chyba zabrání serveru v plnění požadavku.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-896">The app starts, but an error prevents the server from fulfilling the request.</span></span>

<span data-ttu-id="c9a8b-897">K této chybě dochází v kódu aplikace při spuštění nebo při vytváření odpovědi.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-897">This error occurs within the app's code during startup or while creating a response.</span></span> <span data-ttu-id="c9a8b-898">Odpověď může obsahovat žádný obsah nebo odpověď může zobrazit jako *500 Vnitřní chyba serveru* v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-898">The response may contain no content, or the response may appear as a *500 Internal Server Error* in the browser.</span></span> <span data-ttu-id="c9a8b-899">Protokol událostí aplikace obvykle uvádí, že aplikace byla spuštěna normálně.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-899">The Application Event Log usually states that the app started normally.</span></span> <span data-ttu-id="c9a8b-900">Z pohledu serveru je to správné.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-900">From the server's perspective, that's correct.</span></span> <span data-ttu-id="c9a8b-901">Aplikace se spustila, ale nemůže generovat platnou odpověď.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-901">The app did start, but it can't generate a valid response.</span></span> <span data-ttu-id="c9a8b-902">Spusťte aplikaci na příkazovém řádku na serveru nebo povolte protokol stdout ASP.NET Core Module k řešení problému.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-902">Run the app at a command prompt on the server or enable the ASP.NET Core Module stdout log to troubleshoot the problem.</span></span>

### <a name="5025-process-failure"></a><span data-ttu-id="c9a8b-903">502.5 – Selhání procesu</span><span class="sxs-lookup"><span data-stu-id="c9a8b-903">502.5 Process Failure</span></span>

<span data-ttu-id="c9a8b-904">Pracovní proces se nezdaří.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-904">The worker process fails.</span></span> <span data-ttu-id="c9a8b-905">Aplikace se nespustí.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-905">The app doesn't start.</span></span>

<span data-ttu-id="c9a8b-906">Základní [modul ASP.NET](xref:host-and-deploy/aspnet-core-module) se pokusí spustit pracovní proces, ale nepodaří se jej spustit.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-906">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the worker process but it fails to start.</span></span> <span data-ttu-id="c9a8b-907">Příčinu selhání spuštění procesu lze obvykle určit z položek v protokolu událostí aplikace a ASP.NET protokolu stdout základního modulu.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-907">The cause of a process startup failure can usually be determined from entries in the Application Event Log and the ASP.NET Core Module stdout log.</span></span>

<span data-ttu-id="c9a8b-908">Obvyklá podmínka selhání je, že aplikace je nesprávně nakonfigurovaná kvůli cílení na verzi sdíleného rozhraní ASP.NET Core, která není k dispozici.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-908">A common failure condition is the app is misconfigured due to targeting a version of the ASP.NET Core shared framework that isn't present.</span></span> <span data-ttu-id="c9a8b-909">Zkontrolujte, které verze ASP.NET sdíleného rozhraní Core jsou nainstalovány v cílovém počítači.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-909">Check which versions of the ASP.NET Core shared framework are installed on the target machine.</span></span> <span data-ttu-id="c9a8b-910">Sdílená *architektura* je sada sestavení (*soubory DLL),* které jsou nainstalovány v počítači a odkazovány metabalíčkem, například `Microsoft.AspNetCore.App`.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-910">The *shared framework* is the set of assemblies (*.dll* files) that are installed on the machine and referenced by a metapackage such as `Microsoft.AspNetCore.App`.</span></span> <span data-ttu-id="c9a8b-911">Odkaz na metabalíček může určit minimální požadovanou verzi.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-911">The metapackage reference can specify a minimum required version.</span></span> <span data-ttu-id="c9a8b-912">Další informace naleznete [v tématu sdílený rámec](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span><span class="sxs-lookup"><span data-stu-id="c9a8b-912">For more information, see [The shared framework](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span></span>

<span data-ttu-id="c9a8b-913">Chybová stránka *502.5 Process Failure* je vrácena, když selhání konfigurace hostitele nebo aplikace způsobí selhání pracovního procesu:</span><span class="sxs-lookup"><span data-stu-id="c9a8b-913">The *502.5 Process Failure* error page is returned when a hosting or app misconfiguration causes the worker process to fail:</span></span>

### <a name="failed-to-start-application-errorcode-0x800700c1"></a><span data-ttu-id="c9a8b-914">Spuštění aplikace se nezdařilo (ErrorCode '0x800700c1')</span><span class="sxs-lookup"><span data-stu-id="c9a8b-914">Failed to start application (ErrorCode '0x800700c1')</span></span>

```
EventID: 1010
Source: IIS AspNetCore Module V2
Failed to start application '/LM/W3SVC/6/ROOT/', ErrorCode '0x800700c1'.
```

<span data-ttu-id="c9a8b-915">Spuštění aplikace se nezdařilo, protože nebylo možné načíst sestavení aplikace (*DLL*).</span><span class="sxs-lookup"><span data-stu-id="c9a8b-915">The app failed to start because the app's assembly (*.dll*) couldn't be loaded.</span></span>

<span data-ttu-id="c9a8b-916">K této chybě dochází, pokud je neodpovídající bitness mezi publikovanou aplikací a w3wp/iisexpress procesu.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-916">This error occurs when there's a bitness mismatch between the published app and the w3wp/iisexpress process.</span></span>

<span data-ttu-id="c9a8b-917">Zkontrolujte, zda je 32bitové nastavení fondu aplikací správné:</span><span class="sxs-lookup"><span data-stu-id="c9a8b-917">Confirm that the app pool's 32-bit setting is correct:</span></span>

1. <span data-ttu-id="c9a8b-918">Vyberte fond aplikací ve **fondech aplikací**Správce služby IIS .</span><span class="sxs-lookup"><span data-stu-id="c9a8b-918">Select the app pool in IIS Manager's **Application Pools**.</span></span>
1. <span data-ttu-id="c9a8b-919">V části Upravit fond aplikací v panelu **Akce** vyberte **Upřesnit nastavení** v části Upravit **fond aplikací.**</span><span class="sxs-lookup"><span data-stu-id="c9a8b-919">Select **Advanced Settings** under **Edit Application Pool** in the **Actions** panel.</span></span>
1. <span data-ttu-id="c9a8b-920">Nastavit **povolit 32bitové aplikace**:</span><span class="sxs-lookup"><span data-stu-id="c9a8b-920">Set **Enable 32-Bit Applications**:</span></span>
   * <span data-ttu-id="c9a8b-921">Pokud nasazujete 32bitovou (x86) `True`aplikaci, nastavte hodnotu na .</span><span class="sxs-lookup"><span data-stu-id="c9a8b-921">If deploying a 32-bit (x86) app, set the value to `True`.</span></span>
   * <span data-ttu-id="c9a8b-922">Pokud nasazujete 64bitovou (x64) `False`aplikaci, nastavte hodnotu na .</span><span class="sxs-lookup"><span data-stu-id="c9a8b-922">If deploying a 64-bit (x64) app, set the value to `False`.</span></span>

<span data-ttu-id="c9a8b-923">Zkontrolujte, zda v souboru `<Platform>` projektu není konflikt mezi vlastností MSBuild a publikovanou bitness aplikace.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-923">Confirm that there isn't a conflict between a `<Platform>` MSBuild property in the project file and the published bitness of the app.</span></span>

### <a name="connection-reset"></a><span data-ttu-id="c9a8b-924">Resetování připojení</span><span class="sxs-lookup"><span data-stu-id="c9a8b-924">Connection reset</span></span>

<span data-ttu-id="c9a8b-925">Pokud dojde k chybě po odeslání záhlaví, je příliš pozdě na to, aby server odeslal **chybu interního serveru 500,** když dojde k chybě.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-925">If an error occurs after the headers are sent, it's too late for the server to send a **500 Internal Server Error** when an error occurs.</span></span> <span data-ttu-id="c9a8b-926">K tomu často dochází, když dojde k chybě během serializace složitých objektů pro odpověď.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-926">This often happens when an error occurs during the serialization of complex objects for a response.</span></span> <span data-ttu-id="c9a8b-927">Tento typ chyby se zobrazí jako chyba *obnovení připojení* v klientovi.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-927">This type of error appears as a *connection reset* error on the client.</span></span> <span data-ttu-id="c9a8b-928">[Protokolování aplikací](xref:fundamentals/logging/index) může pomoci při řešení těchto typů chyb.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-928">[Application logging](xref:fundamentals/logging/index) can help troubleshoot these types of errors.</span></span>

### <a name="default-startup-limits"></a><span data-ttu-id="c9a8b-929">Výchozí limity pro spuštění</span><span class="sxs-lookup"><span data-stu-id="c9a8b-929">Default startup limits</span></span>

<span data-ttu-id="c9a8b-930">[Základní modul ASP.NET](xref:host-and-deploy/aspnet-core-module) je nakonfigurován s výchozím *spouštěcím limitem* 120 sekund.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-930">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) is configured with a default *startupTimeLimit* of 120 seconds.</span></span> <span data-ttu-id="c9a8b-931">Pokud zůstane na výchozí hodnotu, aplikace může trvat až dvě minuty ke spuštění, než modul zaznamená selhání procesu.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-931">When left at the default value, an app may take up to two minutes to start before the module logs a process failure.</span></span> <span data-ttu-id="c9a8b-932">Informace o konfiguraci modulu naleznete v [tématu Atributy prvku aspNetCore](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span><span class="sxs-lookup"><span data-stu-id="c9a8b-932">For information on configuring the module, see [Attributes of the aspNetCore element](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span></span>

## <a name="troubleshoot-on-azure-app-service"></a><span data-ttu-id="c9a8b-933">Poradce při potížích se službou Azure App Service</span><span class="sxs-lookup"><span data-stu-id="c9a8b-933">Troubleshoot on Azure App Service</span></span>

[!INCLUDE [Azure App Service Preview Notice](~/includes/azure-apps-preview-notice.md)]

### <a name="application-event-log-azure-app-service"></a><span data-ttu-id="c9a8b-934">Protokol událostí aplikace (služba Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="c9a8b-934">Application Event Log (Azure App Service)</span></span>

<span data-ttu-id="c9a8b-935">Chcete-li získat přístup k protokolu událostí aplikace, použijte okno **Diagnostikovat a vyřešit problémy** na webu Azure Portal:</span><span class="sxs-lookup"><span data-stu-id="c9a8b-935">To access the Application Event Log, use the **Diagnose and solve problems** blade in the Azure portal:</span></span>

1. <span data-ttu-id="c9a8b-936">Na webu Azure Portal otevřete aplikaci ve **Službách aplikací**.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-936">In the Azure portal, open the app in **App Services**.</span></span>
1. <span data-ttu-id="c9a8b-937">Vyberte **Diagnostikovat a řešit problémy**.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-937">Select **Diagnose and solve problems**.</span></span>
1. <span data-ttu-id="c9a8b-938">Vyberte nadpis **Diagnostické nástroje.**</span><span class="sxs-lookup"><span data-stu-id="c9a8b-938">Select the **Diagnostic Tools** heading.</span></span>
1. <span data-ttu-id="c9a8b-939">V části **Nástroje podpory**vyberte tlačítko **Události aplikace.**</span><span class="sxs-lookup"><span data-stu-id="c9a8b-939">Under **Support Tools**, select the **Application Events** button.</span></span>
1. <span data-ttu-id="c9a8b-940">Prohlédněte si nejnovější chybu poskytovanou položkou *AspNetCoreModule* nebo *IIS AspNetCoreModule V2* ve sloupci **Zdroj.**</span><span class="sxs-lookup"><span data-stu-id="c9a8b-940">Examine the latest error provided by the *IIS AspNetCoreModule* or *IIS AspNetCoreModule V2* entry in the **Source** column.</span></span>

<span data-ttu-id="c9a8b-941">Alternativou k použití **funkce Diagnostika a řešení problémů** je prozkoumat soubor protokolu událostí aplikace přímo pomocí [kudu](https://github.com/projectkudu/kudu/wiki):</span><span class="sxs-lookup"><span data-stu-id="c9a8b-941">An alternative to using the **Diagnose and solve problems** blade is to examine the Application Event Log file directly using [Kudu](https://github.com/projectkudu/kudu/wiki):</span></span>

1. <span data-ttu-id="c9a8b-942">Otevřete **rozšířené nástroje** v oblasti **vývojových nástrojů.**</span><span class="sxs-lookup"><span data-stu-id="c9a8b-942">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="c9a8b-943">Vyberte tlačítko \*\*Přejít.&rarr; \*\*</span><span class="sxs-lookup"><span data-stu-id="c9a8b-943">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="c9a8b-944">Konzole Kudu se otevře v novém kartě prohlížeče nebo v okně.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-944">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="c9a8b-945">Pomocí navigačního panelu v horní části stránky otevřete **konzolu ladění** a vyberte **možnost CMD**.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-945">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="c9a8b-946">Otevřete složku **LogFiles.**</span><span class="sxs-lookup"><span data-stu-id="c9a8b-946">Open the **LogFiles** folder.</span></span>
1. <span data-ttu-id="c9a8b-947">Vyberte ikonu tužky vedle souboru *eventlog.xml.*</span><span class="sxs-lookup"><span data-stu-id="c9a8b-947">Select the pencil icon next to the *eventlog.xml* file.</span></span>
1. <span data-ttu-id="c9a8b-948">Prohlédněte si protokol.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-948">Examine the log.</span></span> <span data-ttu-id="c9a8b-949">Přejděte do dolní části protokolu zobrazíte nejnovější události.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-949">Scroll to the bottom of the log to see the most recent events.</span></span>

### <a name="run-the-app-in-the-kudu-console"></a><span data-ttu-id="c9a8b-950">Spuštění aplikace v konzoli Kudu</span><span class="sxs-lookup"><span data-stu-id="c9a8b-950">Run the app in the Kudu console</span></span>

<span data-ttu-id="c9a8b-951">Mnoho chyb při spuštění nevytváří užitečné informace v protokolu událostí aplikace.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-951">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="c9a8b-952">Aplikaci můžete spustit v konzole vzdáleného spuštění [Kudu](https://github.com/projectkudu/kudu/wiki) a zjistit chybu:</span><span class="sxs-lookup"><span data-stu-id="c9a8b-952">You can run the app in the [Kudu](https://github.com/projectkudu/kudu/wiki) Remote Execution Console to discover the error:</span></span>

1. <span data-ttu-id="c9a8b-953">Otevřete **rozšířené nástroje** v oblasti **vývojových nástrojů.**</span><span class="sxs-lookup"><span data-stu-id="c9a8b-953">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="c9a8b-954">Vyberte tlačítko \*\*Přejít.&rarr; \*\*</span><span class="sxs-lookup"><span data-stu-id="c9a8b-954">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="c9a8b-955">Konzole Kudu se otevře v novém kartě prohlížeče nebo v okně.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-955">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="c9a8b-956">Pomocí navigačního panelu v horní části stránky otevřete **konzolu ladění** a vyberte **možnost CMD**.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-956">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>

#### <a name="test-a-32-bit-x86-app"></a><span data-ttu-id="c9a8b-957">Testování 32bitové aplikace (x86)</span><span class="sxs-lookup"><span data-stu-id="c9a8b-957">Test a 32-bit (x86) app</span></span>

<span data-ttu-id="c9a8b-958">**Aktuální verze**</span><span class="sxs-lookup"><span data-stu-id="c9a8b-958">**Current release**</span></span>

1. `cd d:\home\site\wwwroot`
1. <span data-ttu-id="c9a8b-959">Spusťte aplikaci:</span><span class="sxs-lookup"><span data-stu-id="c9a8b-959">Run the app:</span></span>
   * <span data-ttu-id="c9a8b-960">Pokud je aplikace [nasazení závislé na rámci](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span><span class="sxs-lookup"><span data-stu-id="c9a8b-960">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

     ```dotnetcli
     dotnet .\{ASSEMBLY NAME}.dll
     ```

   * <span data-ttu-id="c9a8b-961">Pokud se jedná o [samostatné nasazení](/dotnet/core/deploying/#self-contained-deployments-scd)aplikace :</span><span class="sxs-lookup"><span data-stu-id="c9a8b-961">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

     ```console
     {ASSEMBLY NAME}.exe
     ```

<span data-ttu-id="c9a8b-962">Výstup konzoly z aplikace, v němž se zobrazují všechny chyby, je předán do konzoly Kudu.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-962">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="c9a8b-963">**Nasazení závislé na rozhraní spuštěné ve verzi preview**</span><span class="sxs-lookup"><span data-stu-id="c9a8b-963">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="c9a8b-964">*Vyžaduje instalaci rozšíření webu ASP.NET Core {VERSION} (x86).*</span><span class="sxs-lookup"><span data-stu-id="c9a8b-964">*Requires installing the ASP.NET Core {VERSION} (x86) Runtime site extension.*</span></span>

1. <span data-ttu-id="c9a8b-965">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32`(`{X.Y}` je runtime verze)</span><span class="sxs-lookup"><span data-stu-id="c9a8b-965">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="c9a8b-966">Spusťte aplikaci: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="c9a8b-966">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="c9a8b-967">Výstup konzoly z aplikace, v němž se zobrazují všechny chyby, je předán do konzoly Kudu.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-967">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

#### <a name="test-a-64-bit-x64-app"></a><span data-ttu-id="c9a8b-968">Testování 64bitové aplikace (x64)</span><span class="sxs-lookup"><span data-stu-id="c9a8b-968">Test a 64-bit (x64) app</span></span>

<span data-ttu-id="c9a8b-969">**Aktuální verze**</span><span class="sxs-lookup"><span data-stu-id="c9a8b-969">**Current release**</span></span>

* <span data-ttu-id="c9a8b-970">Pokud je aplikace 64bitové (x64) [nasazení závislé na rozhraní](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span><span class="sxs-lookup"><span data-stu-id="c9a8b-970">If the app is a 64-bit (x64) [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>
  1. `cd D:\Program Files\dotnet`
  1. <span data-ttu-id="c9a8b-971">Spusťte aplikaci: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="c9a8b-971">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>
* <span data-ttu-id="c9a8b-972">Pokud se jedná o [samostatné nasazení](/dotnet/core/deploying/#self-contained-deployments-scd)aplikace :</span><span class="sxs-lookup"><span data-stu-id="c9a8b-972">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>
  1. `cd D:\home\site\wwwroot`
  1. <span data-ttu-id="c9a8b-973">Spusťte aplikaci: `{ASSEMBLY NAME}.exe`</span><span class="sxs-lookup"><span data-stu-id="c9a8b-973">Run the app: `{ASSEMBLY NAME}.exe`</span></span>

<span data-ttu-id="c9a8b-974">Výstup konzoly z aplikace, v němž se zobrazují všechny chyby, je předán do konzoly Kudu.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-974">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="c9a8b-975">**Nasazení závislé na rozhraní spuštěné ve verzi preview**</span><span class="sxs-lookup"><span data-stu-id="c9a8b-975">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="c9a8b-976">*Vyžaduje instalaci rozšíření webu ASP.NET Core {VERSION} (x64).*</span><span class="sxs-lookup"><span data-stu-id="c9a8b-976">*Requires installing the ASP.NET Core {VERSION} (x64) Runtime site extension.*</span></span>

1. <span data-ttu-id="c9a8b-977">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64`(`{X.Y}` je runtime verze)</span><span class="sxs-lookup"><span data-stu-id="c9a8b-977">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="c9a8b-978">Spusťte aplikaci: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="c9a8b-978">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="c9a8b-979">Výstup konzoly z aplikace, v němž se zobrazují všechny chyby, je předán do konzoly Kudu.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-979">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

### <a name="aspnet-core-module-stdout-log-azure-app-service"></a><span data-ttu-id="c9a8b-980">ASP.NET protokol stdout modulu (služba Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="c9a8b-980">ASP.NET Core Module stdout log (Azure App Service)</span></span>

<span data-ttu-id="c9a8b-981">Protokol stdout ASP.NET Core Module často zaznamenává užitečné chybové zprávy, které nebyly nalezeny v protokolu událostí aplikace.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-981">The ASP.NET Core Module stdout log often records useful error messages not found in the Application Event Log.</span></span> <span data-ttu-id="c9a8b-982">Povolení a zobrazení protokolů stdout:</span><span class="sxs-lookup"><span data-stu-id="c9a8b-982">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="c9a8b-983">Přejděte na okno **Diagnostikovat a vyřešit problémy** na webu Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-983">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="c9a8b-984">V **části SELECT PROBLEM CATEGORY**vyberte tlačítko Web App **Down** .</span><span class="sxs-lookup"><span data-stu-id="c9a8b-984">Under **SELECT PROBLEM CATEGORY**, select the **Web App Down** button.</span></span>
1. <span data-ttu-id="c9a8b-985">V části **Navrhovaná řešení** > **Povolit přesměrování protokolu Stdout**vyberte tlačítko **pro otevření konzoly Kudu a upravte web.Config**.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-985">Under **Suggested Solutions** > **Enable Stdout Log Redirection**, select the button to **Open Kudu Console to edit Web.Config**.</span></span>
1. <span data-ttu-id="c9a8b-986">V **diagnostické konzole**Kudu otevřete složky webu **site** > **wwwroot**.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-986">In the Kudu **Diagnostic Console**, open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="c9a8b-987">Posunutím dolů zobrazíte soubor *web.config* v dolní části seznamu.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-987">Scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="c9a8b-988">Klikněte na ikonu tužky vedle souboru *web.config.*</span><span class="sxs-lookup"><span data-stu-id="c9a8b-988">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="c9a8b-989">Nastavte **stdoutLogEnabled** na `true` cestu **stdoutLogFile** `\\?\%home%\LogFiles\stdout`a změňte ji na: .</span><span class="sxs-lookup"><span data-stu-id="c9a8b-989">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="c9a8b-990">Vyberte **Uložit,** chcete-li uložit aktualizovaný soubor *web.config.*</span><span class="sxs-lookup"><span data-stu-id="c9a8b-990">Select **Save** to save the updated *web.config* file.</span></span>
1. <span data-ttu-id="c9a8b-991">Požádejte o to do aplikace.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-991">Make a request to the app.</span></span>
1. <span data-ttu-id="c9a8b-992">Vraťte se na Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-992">Return to the Azure portal.</span></span> <span data-ttu-id="c9a8b-993">Vyberte okno **Pokročilé nástroje** v oblasti **NÁSTROJE VÝVOJE.**</span><span class="sxs-lookup"><span data-stu-id="c9a8b-993">Select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="c9a8b-994">Vyberte tlačítko \*\*Přejít.&rarr; \*\*</span><span class="sxs-lookup"><span data-stu-id="c9a8b-994">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="c9a8b-995">Konzole Kudu se otevře v novém kartě prohlížeče nebo v okně.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-995">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="c9a8b-996">Pomocí navigačního panelu v horní části stránky otevřete **konzolu ladění** a vyberte **možnost CMD**.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-996">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="c9a8b-997">Vyberte složku **LogFiles.**</span><span class="sxs-lookup"><span data-stu-id="c9a8b-997">Select the **LogFiles** folder.</span></span>
1. <span data-ttu-id="c9a8b-998">Zkontrolujte sloupec **Změněno** a vyberte ikonu tužky a upravte protokol stdout s nejnovějším datem změny.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-998">Inspect the **Modified** column and select the pencil icon to edit the stdout log with the latest modification date.</span></span>
1. <span data-ttu-id="c9a8b-999">Po otevření souboru protokolu se zobrazí chyba.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-999">When the log file opens, the error is displayed.</span></span>

<span data-ttu-id="c9a8b-1000">Zakažte protokolování stdout po dokončení řešení potíží:</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1000">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="c9a8b-1001">V diagnostické **konzole**Kudu se vraťte na **web** > **wwwroot** cesty a odhalte soubor *web.config.*</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1001">In the Kudu **Diagnostic Console**, return to the path **site** > **wwwroot** to reveal the *web.config* file.</span></span> <span data-ttu-id="c9a8b-1002">Znovu otevřete soubor **web.config** výběrem ikony tužky.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1002">Open the **web.config** file again by selecting the pencil icon.</span></span>
1. <span data-ttu-id="c9a8b-1003">Nastavte **stdoutLogEnabled** na `false`.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1003">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="c9a8b-1004">Chcete-li soubor uložit, vyberte **Uložit.**</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1004">Select **Save** to save the file.</span></span>

<span data-ttu-id="c9a8b-1005">Další informace naleznete v tématu <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1005">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

> [!WARNING]
> <span data-ttu-id="c9a8b-1006">Selhání zakázání protokolu stdout může vést k selhání aplikace nebo serveru.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1006">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="c9a8b-1007">Neexistuje žádné omezení na velikost souboru protokolu nebo počet vytvořených souborů protokolu.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1007">There's no limit on log file size or the number of log files created.</span></span> <span data-ttu-id="c9a8b-1008">K řešení problémů se spuštěním aplikace používejte protokolování stdout pouze.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1008">Only use stdout logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="c9a8b-1009">Pro obecné protokolování v aplikaci ASP.NET Core po spuštění použijte knihovnu protokolování, která omezuje velikost souboru protokolu a otáčí protokoly.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1009">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="c9a8b-1010">Další informace naleznete [v tématu zprostředkovatelé protokolování třetích stran](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1010">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="slow-or-hanging-app-azure-app-service"></a><span data-ttu-id="c9a8b-1011">Pomalá nebo zavěšená aplikace (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1011">Slow or hanging app (Azure App Service)</span></span>

<span data-ttu-id="c9a8b-1012">Když aplikace reaguje pomalu nebo se při žádosti zablokuje, přečtěte si následující články:</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1012">When an app responds slowly or hangs on a request, see the following articles:</span></span>

* [<span data-ttu-id="c9a8b-1013">Řešení potíží s výkonem pomalých webových aplikací ve službě Azure App Service</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1013">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="c9a8b-1014">Použití rozšíření webu Diagnostikátor selhání k zachycení výpisu pro občasné výjimky problémy nebo problémy s výkonem na Azure Web App</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1014">Use Crash Diagnoser Site Extension to Capture Dump for Intermittent Exception issues or performance issues on Azure Web App</span></span>](https://blogs.msdn.microsoft.com/asiatech/2015/12/28/use-crash-diagnoser-site-extension-to-capture-dump-for-intermittent-exception-issues-or-performance-issues-on-azure-web-app/)

### <a name="monitoring-blades"></a><span data-ttu-id="c9a8b-1015">Monitorovací nože</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1015">Monitoring blades</span></span>

<span data-ttu-id="c9a8b-1016">Monitorování blades poskytují alternativní řešení potíží k metodám popsaným výše v tématu.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1016">Monitoring blades provide an alternative troubleshooting experience to the methods described earlier in the topic.</span></span> <span data-ttu-id="c9a8b-1017">Tyto čepele lze použít k diagnostice chyb řady 500.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1017">These blades can be used to diagnose 500-series errors.</span></span>

<span data-ttu-id="c9a8b-1018">Zkontrolujte, zda jsou nainstalovány ASP.NET rozšíření core.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1018">Confirm that the ASP.NET Core Extensions are installed.</span></span> <span data-ttu-id="c9a8b-1019">Pokud rozšíření nejsou nainstalovaná, nainstalujte je ručně:</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1019">If the extensions aren't installed, install them manually:</span></span>

1. <span data-ttu-id="c9a8b-1020">V části **Čepele NÁSTROJE PRO VÝVOJ** vyberte okno **Rozšíření.**</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1020">In the **DEVELOPMENT TOOLS** blade section, select the **Extensions** blade.</span></span>
1. <span data-ttu-id="c9a8b-1021">V seznamu by se měl **zobrazit ASP.NET rozšíření jádra.**</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1021">The **ASP.NET Core Extensions** should appear in the list.</span></span>
1. <span data-ttu-id="c9a8b-1022">Pokud rozšíření nejsou nainstalovaná, vyberte tlačítko **Přidat.**</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1022">If the extensions aren't installed, select the **Add** button.</span></span>
1. <span data-ttu-id="c9a8b-1023">Ze seznamu zvolte **ASP.NET jádrová rozšíření.**</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1023">Choose the **ASP.NET Core Extensions** from the list.</span></span>
1. <span data-ttu-id="c9a8b-1024">Chcete-li přijmout zákonné podmínky, vyberte **možnost OK.**</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1024">Select **OK** to accept the legal terms.</span></span>
1. <span data-ttu-id="c9a8b-1025">V okně **Přidat prodlužovací** okno vyberte **OK.**</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1025">Select **OK** on the **Add extension** blade.</span></span>
1. <span data-ttu-id="c9a8b-1026">Informační zpráva o automaticky otevíraná okna označuje, kdy jsou rozšíření úspěšně nainstalována.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1026">An informational pop-up message indicates when the extensions are successfully installed.</span></span>

<span data-ttu-id="c9a8b-1027">Pokud protokolování stdout není povoleno, postupujte takto:</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1027">If stdout logging isn't enabled, follow these steps:</span></span>

1. <span data-ttu-id="c9a8b-1028">Na webu Azure Portal vyberte okno **Rozšířené nástroje** v oblasti NÁSTROJE **PRO VÝVOJ.**</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1028">In the Azure portal, select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="c9a8b-1029">Vyberte tlačítko \*\*Přejít.&rarr; \*\*</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1029">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="c9a8b-1030">Konzole Kudu se otevře v novém kartě prohlížeče nebo v okně.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1030">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="c9a8b-1031">Pomocí navigačního panelu v horní části stránky otevřete **konzolu ladění** a vyberte **možnost CMD**.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1031">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="c9a8b-1032">Otevřete složky na **webu** > cesty **wwwroot** a posuňte se dolů a odhalte soubor *web.config* v dolní části seznamu.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1032">Open the folders to the path **site** > **wwwroot** and scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="c9a8b-1033">Klikněte na ikonu tužky vedle souboru *web.config.*</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1033">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="c9a8b-1034">Nastavte **stdoutLogEnabled** na `true` cestu **stdoutLogFile** `\\?\%home%\LogFiles\stdout`a změňte ji na: .</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1034">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="c9a8b-1035">Vyberte **Uložit,** chcete-li uložit aktualizovaný soubor *web.config.*</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1035">Select **Save** to save the updated *web.config* file.</span></span>

<span data-ttu-id="c9a8b-1036">Postupujte k aktivaci diagnostického protokolování:</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1036">Proceed to activate diagnostic logging:</span></span>

1. <span data-ttu-id="c9a8b-1037">Na webu Azure Portal vyberte okno **Protokoly diagnostiky.**</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1037">In the Azure portal, select the **Diagnostics logs** blade.</span></span>
1. <span data-ttu-id="c9a8b-1038">Vyberte přepínač **Zapnuto** pro **protokolování aplikací (souborový systém)** a **Podrobné chybové zprávy**.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1038">Select the **On** switch for **Application Logging (Filesystem)** and **Detailed error messages**.</span></span> <span data-ttu-id="c9a8b-1039">V horní části nože vyberte tlačítko **Uložit.**</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1039">Select the **Save** button at the top of the blade.</span></span>
1. <span data-ttu-id="c9a8b-1040">Chcete-li zahrnout trasování neúspěšných požadavků, známé také jako protokolfreb (Failed Request Event Buffering), vyberte přepínač **Zapnuto** pro **trasování neúspěšných požadavků**.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1040">To include failed request tracing, also known as Failed Request Event Buffering (FREB) logging, select the **On** switch for **Failed request tracing**.</span></span>
1. <span data-ttu-id="c9a8b-1041">Vyberte okno **protokolu datového proudu,** který je uveden okamžitě pod **protokoly protokoly diagnostiky** na portálu.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1041">Select the **Log stream** blade, which is listed immediately under the **Diagnostics logs** blade in the portal.</span></span>
1. <span data-ttu-id="c9a8b-1042">Požádejte o to do aplikace.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1042">Make a request to the app.</span></span>
1. <span data-ttu-id="c9a8b-1043">V rámci dat datového proudu protokolu je uvedena příčina chyby.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1043">Within the log stream data, the cause of the error is indicated.</span></span>

<span data-ttu-id="c9a8b-1044">Nezapomeňte zakázat stdout protokolování po dokončení řešení potíží.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1044">Be sure to disable stdout logging when troubleshooting is complete.</span></span>

<span data-ttu-id="c9a8b-1045">Zobrazení protokolů trasování neúspěšných požadavků (protokoly FREB):</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1045">To view the failed request tracing logs (FREB logs):</span></span>

1. <span data-ttu-id="c9a8b-1046">Přejděte na okno **Diagnostikovat a vyřešit problémy** na webu Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1046">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="c9a8b-1047">V yberte **Protokoly trasování neúspěšných požadavků** v oblasti **NÁSTROJE PODPORY** na postranním panelu.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1047">Select **Failed Request Tracing Logs** from the **SUPPORT TOOLS** area of the sidebar.</span></span>

<span data-ttu-id="c9a8b-1048">Další informace [najdete v tématu Sledování neúspěšných požadavků v tématu Povolit diagnostiku pro webové aplikace v tématu Služby aplikací](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) a [v nejčastějších dotazech k výkonu aplikací pro webové aplikace v Azure: Jak zapnu trasování neúspěšných požadavků?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing)</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1048">See [Failed request traces section of the Enable diagnostics logging for web apps in Azure App Service topic](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) and the [Application performance FAQs for Web Apps in Azure: How do I turn on failed request tracing?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing) for more information.</span></span>

<span data-ttu-id="c9a8b-1049">Další informace najdete [v tématu Povolení protokolování diagnostiky pro webové aplikace ve službě Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log).</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1049">For more information, see [Enable diagnostics logging for web apps in Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log).</span></span>

> [!WARNING]
> <span data-ttu-id="c9a8b-1050">Selhání zakázání protokolu stdout může vést k selhání aplikace nebo serveru.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1050">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="c9a8b-1051">Neexistuje žádné omezení na velikost souboru protokolu nebo počet vytvořených souborů protokolu.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1051">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="c9a8b-1052">Pro rutinní protokolování v aplikaci ASP.NET Core použijte knihovnu protokolování, která omezuje velikost souboru protokolu a otáčí protokoly.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1052">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="c9a8b-1053">Další informace naleznete [v tématu zprostředkovatelé protokolování třetích stran](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1053">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

## <a name="troubleshoot-on-iis"></a><span data-ttu-id="c9a8b-1054">Odstraňování potíží ve službě IIS</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1054">Troubleshoot on IIS</span></span>

### <a name="application-event-log-iis"></a><span data-ttu-id="c9a8b-1055">Protokol událostí aplikace (IIS)</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1055">Application Event Log (IIS)</span></span>

<span data-ttu-id="c9a8b-1056">Přístup k protokolu událostí aplikace:</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1056">Access the Application Event Log:</span></span>

1. <span data-ttu-id="c9a8b-1057">Otevřete nabídku Start, vyhledejte *Prohlížeč událostí*a vyberte aplikaci **Prohlížeč událostí.**</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1057">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="c9a8b-1058">V **Prohlížeči událostí**otevřete uzel **Protokoly systému Windows.**</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1058">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="c9a8b-1059">Výběrem **možnosti Aplikace** otevřete protokol událostí aplikace.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1059">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="c9a8b-1060">Vyhledejte chyby spojené s aplikací se selháním.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1060">Search for errors associated with the failing app.</span></span> <span data-ttu-id="c9a8b-1061">Chyby mají hodnotu *modulu AspNetCore služby IIS* nebo *modulu AspNetCore služby IIS Express* ve sloupci *Zdroj.*</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1061">Errors have a value of *IIS AspNetCore Module* or *IIS Express AspNetCore Module* in the *Source* column.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="c9a8b-1062">Spuštění aplikace na příkazovém řádku</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1062">Run the app at a command prompt</span></span>

<span data-ttu-id="c9a8b-1063">Mnoho chyb při spuštění nevytváří užitečné informace v protokolu událostí aplikace.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1063">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="c9a8b-1064">Příčinu některých chyb můžete najít spuštěním aplikace na příkazovém řádku v hostitelském systému.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1064">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span>

#### <a name="framework-dependent-deployment"></a><span data-ttu-id="c9a8b-1065">Nasazení závislé na rámci</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1065">Framework-dependent deployment</span></span>

<span data-ttu-id="c9a8b-1066">Pokud je aplikace [nasazení závislé na rámci](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1066">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

1. <span data-ttu-id="c9a8b-1067">Na příkazovém řádku přejděte do složky nasazení a spusťte aplikaci spuštěním sestavení aplikace pomocí *dotnet.exe*.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1067">At a command prompt, navigate to the deployment folder and run the app by executing the app's assembly with *dotnet.exe*.</span></span> <span data-ttu-id="c9a8b-1068">V následujícím příkazu nahraďte název sestavení \<aplikace assembly_name `dotnet .\<assembly_name>.dll`>: .</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1068">In the following command, substitute the name of the app's assembly for \<assembly_name>: `dotnet .\<assembly_name>.dll`.</span></span>
1. <span data-ttu-id="c9a8b-1069">Výstup konzoly z aplikace, zobrazující případné chyby, je zapsán do okna konzoly.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1069">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="c9a8b-1070">Pokud dojde k chybám při podání požadavku na aplikaci, podat žádost na hostitele a port, kde Kestrel naslouchá.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1070">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="c9a8b-1071">Pomocí výchozího hostitele a příspěvku `http://localhost:5000/`požádejte o místo .</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1071">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="c9a8b-1072">Pokud aplikace reaguje normálně na adrese koncového bodu Kestrel, problém je pravděpodobnější související s konfigurací hostování a méně pravděpodobné v rámci aplikace.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1072">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

#### <a name="self-contained-deployment"></a><span data-ttu-id="c9a8b-1073">Samostatné nasazení</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1073">Self-contained deployment</span></span>

<span data-ttu-id="c9a8b-1074">Pokud se jedná o [samostatné nasazení](/dotnet/core/deploying/#self-contained-deployments-scd)aplikace :</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1074">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

1. <span data-ttu-id="c9a8b-1075">Na příkazovém řádku přejděte do složky nasazení a spusťte spustitelný soubor aplikace.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1075">At a command prompt, navigate to the deployment folder and run the app's executable.</span></span> <span data-ttu-id="c9a8b-1076">V následujícím příkazu nahraďte název sestavení \<aplikace assembly_name `<assembly_name>.exe`>: .</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1076">In the following command, substitute the name of the app's assembly for \<assembly_name>: `<assembly_name>.exe`.</span></span>
1. <span data-ttu-id="c9a8b-1077">Výstup konzoly z aplikace, zobrazující případné chyby, je zapsán do okna konzoly.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1077">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="c9a8b-1078">Pokud dojde k chybám při podání požadavku na aplikaci, podat žádost na hostitele a port, kde Kestrel naslouchá.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1078">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="c9a8b-1079">Pomocí výchozího hostitele a příspěvku `http://localhost:5000/`požádejte o místo .</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1079">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="c9a8b-1080">Pokud aplikace reaguje normálně na adrese koncového bodu Kestrel, problém je pravděpodobnější související s konfigurací hostování a méně pravděpodobné v rámci aplikace.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1080">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

### <a name="aspnet-core-module-stdout-log-iis"></a><span data-ttu-id="c9a8b-1081">ASP.NET protokol stdout modulu (IIS)</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1081">ASP.NET Core Module stdout log (IIS)</span></span>

<span data-ttu-id="c9a8b-1082">Povolení a zobrazení protokolů stdout:</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1082">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="c9a8b-1083">Přejděte do složky nasazení webu v hostitelském systému.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1083">Navigate to the site's deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="c9a8b-1084">Pokud složka *protokolů* není k dispozici, vytvořte složku.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1084">If the *logs* folder isn't present, create the folder.</span></span> <span data-ttu-id="c9a8b-1085">Pokyny k povolení msbuild vytvořit složku *protokolů* v nasazení automaticky, naleznete v tématu [directory struktura.](xref:host-and-deploy/directory-structure)</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1085">For instructions on how to enable MSBuild to create the *logs* folder in the deployment automatically, see the [Directory structure](xref:host-and-deploy/directory-structure) topic.</span></span>
1. <span data-ttu-id="c9a8b-1086">Upravte soubor *web.config.*</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1086">Edit the *web.config* file.</span></span> <span data-ttu-id="c9a8b-1087">Nastavte **stdoutLogEnabled** a `true` změňte cestu **stdoutLogFile** tak, `.\logs\stdout`aby přecózovala na složku *protokolů* (například ).</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1087">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to point to the *logs* folder (for example, `.\logs\stdout`).</span></span> <span data-ttu-id="c9a8b-1088">`stdout`v cestě je předpona názvu souboru protokolu.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1088">`stdout` in the path is the log file name prefix.</span></span> <span data-ttu-id="c9a8b-1089">Časové razítko, ID procesu a přípona souboru jsou přidány automaticky při vytvoření protokolu.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1089">A timestamp, process id, and file extension are added automatically when the log is created.</span></span> <span data-ttu-id="c9a8b-1090">Jako `stdout` předpona názvu souboru je typický soubor protokolu pojmenován *stdout_20180205184032_5412.log*.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1090">Using `stdout` as the file name prefix, a typical log file is named *stdout_20180205184032_5412.log*.</span></span>
1. <span data-ttu-id="c9a8b-1091">Ujistěte se, že identita fondu aplikací má oprávnění k zápisu do složky *protokolů.*</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1091">Ensure your application pool's identity has write permissions to the *logs* folder.</span></span>
1. <span data-ttu-id="c9a8b-1092">Uložte aktualizovaný soubor *web.config.*</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1092">Save the updated *web.config* file.</span></span>
1. <span data-ttu-id="c9a8b-1093">Požádejte o to do aplikace.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1093">Make a request to the app.</span></span>
1. <span data-ttu-id="c9a8b-1094">Přejděte do složky *protokolů.*</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1094">Navigate to the *logs* folder.</span></span> <span data-ttu-id="c9a8b-1095">Vyhledejte a otevřete nejnovější protokol stdout.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1095">Find and open the most recent stdout log.</span></span>
1. <span data-ttu-id="c9a8b-1096">Prostudujte si protokol pro chyby.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1096">Study the log for errors.</span></span>

<span data-ttu-id="c9a8b-1097">Zakažte protokolování stdout po dokončení řešení potíží:</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1097">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="c9a8b-1098">Upravte soubor *web.config.*</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1098">Edit the *web.config* file.</span></span>
1. <span data-ttu-id="c9a8b-1099">Nastavte **stdoutLogEnabled** na `false`.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1099">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="c9a8b-1100">Uložte soubor.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1100">Save the file.</span></span>

<span data-ttu-id="c9a8b-1101">Další informace naleznete v tématu <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1101">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

> [!WARNING]
> <span data-ttu-id="c9a8b-1102">Selhání zakázání protokolu stdout může vést k selhání aplikace nebo serveru.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1102">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="c9a8b-1103">Neexistuje žádné omezení na velikost souboru protokolu nebo počet vytvořených souborů protokolu.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1103">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="c9a8b-1104">Pro rutinní protokolování v aplikaci ASP.NET Core použijte knihovnu protokolování, která omezuje velikost souboru protokolu a otáčí protokoly.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1104">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="c9a8b-1105">Další informace naleznete [v tématu zprostředkovatelé protokolování třetích stran](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1105">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="enable-the-developer-exception-page"></a><span data-ttu-id="c9a8b-1106">Povolení stránky výjimky pro vývojáře</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1106">Enable the Developer Exception Page</span></span>

<span data-ttu-id="c9a8b-1107">`ASPNETCORE_ENVIRONMENT` [Proměnnou prostředí lze přidat do web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) pro spuštění aplikace ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1107">The `ASPNETCORE_ENVIRONMENT` [environment variable can be added to web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) to run the app in the Development environment.</span></span> <span data-ttu-id="c9a8b-1108">Pokud prostředí není přepsáno při spuštění `UseEnvironment` aplikace tvůrcem hostitele, nastavení proměnné prostředí umožňuje, aby se [stránka výjimky pro vývojáře](xref:fundamentals/error-handling) zobrazila při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1108">As long as the environment isn't overridden in app startup by `UseEnvironment` on the host builder, setting the environment variable allows the [Developer Exception Page](xref:fundamentals/error-handling) to appear when the app is run.</span></span>

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

<span data-ttu-id="c9a8b-1109">Nastavení proměnné prostředí `ASPNETCORE_ENVIRONMENT` pro se doporučuje pouze pro použití na pracovních a testovacích serverech, které nejsou vystaveny Internetu.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1109">Setting the environment variable for `ASPNETCORE_ENVIRONMENT` is only recommended for use on staging and testing servers that aren't exposed to the Internet.</span></span> <span data-ttu-id="c9a8b-1110">Po odstranění potíží odeberte proměnnou prostředí ze souboru *web.config.*</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1110">Remove the environment variable from the *web.config* file after troubleshooting.</span></span> <span data-ttu-id="c9a8b-1111">Informace o nastavení proměnných prostředí v *souboru web.config*naleznete v [tématu podřízený prvek environmentVariables aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1111">For information on setting environment variables in *web.config*, see [environmentVariables child element of aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).</span></span>

### <a name="obtain-data-from-an-app"></a><span data-ttu-id="c9a8b-1112">Získání dat z aplikace</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1112">Obtain data from an app</span></span>

<span data-ttu-id="c9a8b-1113">Pokud je aplikace schopná reagovat na požadavky, získejte požadavek, připojení a další data z aplikace pomocí terminálového inline middlewaru.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1113">If an app is capable of responding to requests, obtain request, connection, and additional data from the app using terminal inline middleware.</span></span> <span data-ttu-id="c9a8b-1114">Další informace a ukázkový <xref:test/troubleshoot#obtain-data-from-an-app>kód naleznete v tématu .</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1114">For more information and sample code, see <xref:test/troubleshoot#obtain-data-from-an-app>.</span></span>

### <a name="slow-or-hanging-app-iis"></a><span data-ttu-id="c9a8b-1115">Pomalá nebo závěsná aplikace (IIS)</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1115">Slow or hanging app (IIS)</span></span>

<span data-ttu-id="c9a8b-1116">*Výpis stavu systému* je snímek paměti systému a může pomoci určit příčinu selhání aplikace, selhání při spuštění nebo pomalé aplikace.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1116">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="c9a8b-1117">Aplikace dojde k chybě nebo narazí na výjimku</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1117">App crashes or encounters an exception</span></span>

<span data-ttu-id="c9a8b-1118">Získání a analýza výpisu z [programu Zasílání zpráv o chybách systému Windows (WER)](/windows/desktop/wer/windows-error-reporting):</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1118">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="c9a8b-1119">Vytvořte složku, ve které `c:\dumps`budou uloženy soubory s výpisem stavu systému .</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1119">Create a folder to hold crash dump files at `c:\dumps`.</span></span> <span data-ttu-id="c9a8b-1120">Fond aplikací musí mít přístup pro zápis do složky.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1120">The app pool must have write access to the folder.</span></span>
1. <span data-ttu-id="c9a8b-1121">Spusťte [skript EnableDumps PowerShell](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1):</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1121">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1):</span></span>
   * <span data-ttu-id="c9a8b-1122">Pokud aplikace používá [model hostování v procesu](xref:host-and-deploy/iis/index#in-process-hosting-model), spusťte skript pro *w3wp.exe*:</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1122">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\EnableDumps w3wp.exe c:\dumps
     ```

   * <span data-ttu-id="c9a8b-1123">Pokud aplikace používá [mimoprocesový model hostování](xref:host-and-deploy/iis/index#out-of-process-hosting-model), spusťte skript pro *dotnet.exe*:</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1123">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\EnableDumps dotnet.exe c:\dumps
     ```

1. <span data-ttu-id="c9a8b-1124">Spusťte aplikaci za podmínek, které způsobují selhání dojít.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1124">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="c9a8b-1125">Po selhání spusťte [skript DisableDumps PowerShell](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1):</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1125">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1):</span></span>
   * <span data-ttu-id="c9a8b-1126">Pokud aplikace používá [model hostování v procesu](xref:host-and-deploy/iis/index#in-process-hosting-model), spusťte skript pro *w3wp.exe*:</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1126">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\DisableDumps w3wp.exe
     ```

   * <span data-ttu-id="c9a8b-1127">Pokud aplikace používá [mimoprocesový model hostování](xref:host-and-deploy/iis/index#out-of-process-hosting-model), spusťte skript pro *dotnet.exe*:</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1127">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\DisableDumps dotnet.exe
     ```

<span data-ttu-id="c9a8b-1128">Po zhroucení aplikace a výpis kolekce je kompletní, aplikace je povoleno ukončit normálně.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1128">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="c9a8b-1129">Skript PowerShell udává WER tak, aby shromažďoval až pět výpisů v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1129">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="c9a8b-1130">Výpisy stavu systému může zabírají velké množství místa na disku (až několik gigabajtů každý).</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1130">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="c9a8b-1131">Aplikace přestane reagovat, selže při spuštění nebo běží normálně</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1131">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="c9a8b-1132">Když aplikace *přestane reagovat* (přestane reagovat, ale nezhroutí se), selže při spuštění nebo běží normálně, najdete v [tématu Soubory výpisu uživatelského režimu: Výběr nejvhodnějšího nástroje](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) pro výběr vhodného nástroje pro vytvoření výpisu.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1132">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="c9a8b-1133">Analyzovat výpis</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1133">Analyze the dump</span></span>

<span data-ttu-id="c9a8b-1134">Výpis lze analyzovat pomocí několika přístupů.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1134">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="c9a8b-1135">Další informace naleznete [v tématu Analýza souboru s výpisem stavu uživatelského režimu](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1135">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="clear-package-caches"></a><span data-ttu-id="c9a8b-1136">Vymazat mezipaměti balíčků</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1136">Clear package caches</span></span>

<span data-ttu-id="c9a8b-1137">Funkční aplikace může selhat ihned po upgradu sady .NET Core SDK ve vývojovém počítači nebo změně verze balíčku v rámci aplikace.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1137">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="c9a8b-1138">V některých případech nesouvislé balíčky může přerušit aplikaci při provádění hlavních upgradů.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1138">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="c9a8b-1139">Většinu těchto problémů lze opravit podle následujících pokynů:</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1139">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="c9a8b-1140">Odstraňte složky *bin* a *obj.*</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1140">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="c9a8b-1141">Vymazat mezipaměti balíčku spuštěním [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) z příkazového prostředí.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1141">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="c9a8b-1142">Vymazání mezipaměti balíčků lze také provést pomocí nástroje [nuget.exe](https://www.nuget.org/downloads) a spuštěnípříkazu `nuget locals all -clear`.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1142">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="c9a8b-1143">*nuget.exe* není přibalená instalace s operačním systémem Windows desktop a musí být získány odděleně od [webu NuGet](https://www.nuget.org/downloads).</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1143">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="c9a8b-1144">Obnovte a znovu vytvořte projekt.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1144">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="c9a8b-1145">Před opětovnou nasazením aplikace odstraňte všechny soubory ve složce nasazení na serveru.</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1145">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c9a8b-1146">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1146">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:fundamentals/error-handling>
* <xref:host-and-deploy/aspnet-core-module>

### <a name="azure-documentation"></a><span data-ttu-id="c9a8b-1147">Dokumentace k Azure</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1147">Azure documentation</span></span>

* [<span data-ttu-id="c9a8b-1148">Application Insights pro ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1148">Application Insights for ASP.NET Core</span></span>](/azure/application-insights/app-insights-asp-net-core)
* [<span data-ttu-id="c9a8b-1149">Část Řešení potíží s webovou aplikací ve službě Azure App Service ve vzdáleném ladění webových aplikací pomocí Visual Studia</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1149">Remote debugging web apps section of Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug)
* [<span data-ttu-id="c9a8b-1150">Přehled diagnostiky služby Azure App Service</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1150">Azure App Service diagnostics overview</span></span>](/azure/app-service/app-service-diagnostics)
* [<span data-ttu-id="c9a8b-1151">Postup: Monitorování aplikací ve službě Azure App Service</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1151">How to: Monitor Apps in Azure App Service</span></span>](/azure/app-service/web-sites-monitor)
* [<span data-ttu-id="c9a8b-1152">Poradce při potížích s webovou aplikací ve službě Azure App Service pomocí Visual Studia</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1152">Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [<span data-ttu-id="c9a8b-1153">Poradce při potížích s chybami HTTP "502 chybná brána" a "503 služba není k dispozici" ve svých webových aplikacích Azure</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1153">Troubleshoot HTTP errors of "502 bad gateway" and "503 service unavailable" in your Azure web apps</span></span>](/azure/app-service/app-service-web-troubleshoot-http-502-http-503)
* [<span data-ttu-id="c9a8b-1154">Řešení potíží s výkonem pomalých webových aplikací ve službě Azure App Service</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1154">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="c9a8b-1155">Časté otázky k výkonu aplikací pro webové aplikace v Azure</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1155">Application performance FAQs for Web Apps in Azure</span></span>](/azure/app-service/app-service-web-availability-performance-application-issues-faq)
* [<span data-ttu-id="c9a8b-1156">Azure Web App izolovaného prostoru (omezení spuštění služby App Service)</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1156">Azure Web App sandbox (App Service runtime execution limitations)</span></span>](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)
* [<span data-ttu-id="c9a8b-1157">Azure Friday: Azure App Service Diagnostic and Troubleshooting Experience (12minutové video)</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1157">Azure Friday: Azure App Service Diagnostic and Troubleshooting Experience (12-minute video)</span></span>](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)

### <a name="visual-studio-documentation"></a><span data-ttu-id="c9a8b-1158">Dokumentace sady Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1158">Visual Studio documentation</span></span>

* [<span data-ttu-id="c9a8b-1159">Vzdálené ladění ASP.NET jádro ve službě IIS v Azure ve Visual Studiu 2017</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1159">Remote Debug ASP.NET Core on IIS in Azure in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-azure)
* [<span data-ttu-id="c9a8b-1160">Vzdálené ladění ASP.NET jádra ve vzdáleném počítači služby IIS v sadě Visual Studio 2017</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1160">Remote Debug ASP.NET Core on a Remote IIS Computer in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-aspnet-on-a-remote-iis-computer)
* [<span data-ttu-id="c9a8b-1161">Informace o ladění pomocí sady Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1161">Learn to debug using Visual Studio</span></span>](/visualstudio/debugger/getting-started-with-the-debugger)

### <a name="visual-studio-code-documentation"></a><span data-ttu-id="c9a8b-1162">Dokumentace kódu visual studia</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1162">Visual Studio Code documentation</span></span>

* [<span data-ttu-id="c9a8b-1163">Ladění pomocí kódu sady Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c9a8b-1163">Debugging with Visual Studio Code</span></span>](https://code.visualstudio.com/docs/editor/debugging)

::: moniker-end
