---
title: Řešení potíží ASP.NET jádro ve službě Azure App Service a IIS
author: rick-anderson
description: Zjistěte, jak diagnostikovat problémy s nasazením služby Azure App Service a Internet Information Services (IIS) aplikací ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
uid: test/troubleshoot-azure-iis
ms.openlocfilehash: 671f68da2ea261cb8ae32a9d5ef875217859054d
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78655328"
---
# <a name="troubleshoot-aspnet-core-on-azure-app-service-and-iis"></a><span data-ttu-id="e6b6b-103">Řešení potíží ASP.NET jádro ve službě Azure App Service a IIS</span><span class="sxs-lookup"><span data-stu-id="e6b6b-103">Troubleshoot ASP.NET Core on Azure App Service and IIS</span></span>

<span data-ttu-id="e6b6b-104">Podle [Justin Kotalik](https://github.com/jkotalik)</span><span class="sxs-lookup"><span data-stu-id="e6b6b-104">By [Justin Kotalik](https://github.com/jkotalik)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="e6b6b-105">Tento článek obsahuje informace o běžných chybách při spuštění aplikace a pokyny, jak diagnostikovat chyby při nasazení aplikace do služby Azure App Service nebo IIS:</span><span class="sxs-lookup"><span data-stu-id="e6b6b-105">This article provides information on common app startup errors and instructions on how to diagnose errors when an app is deployed to Azure App Service or IIS:</span></span>

[<span data-ttu-id="e6b6b-106">Chyby při spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="e6b6b-106">App startup errors</span></span>](#app-startup-errors)  
<span data-ttu-id="e6b6b-107">Vysvětluje běžné scénáře stavového kódu HTTP při spuštění.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-107">Explains common startup HTTP status code scenarios.</span></span>

[<span data-ttu-id="e6b6b-108">Poradce při potížích se službou Azure App Service</span><span class="sxs-lookup"><span data-stu-id="e6b6b-108">Troubleshoot on Azure App Service</span></span>](#troubleshoot-on-azure-app-service)  
<span data-ttu-id="e6b6b-109">Poskytuje rady pro řešení potíží pro aplikace nasazené do služby Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-109">Provides troubleshooting advice for apps deployed to Azure App Service.</span></span>

[<span data-ttu-id="e6b6b-110">Odstraňování potíží ve službě IIS</span><span class="sxs-lookup"><span data-stu-id="e6b6b-110">Troubleshoot on IIS</span></span>](#troubleshoot-on-iis)  
<span data-ttu-id="e6b6b-111">Poskytuje rady pro řešení potíží s aplikacemi nasazenými do služby IIS nebo spuštěných místně ve službě IIS Express.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-111">Provides troubleshooting advice for apps deployed to IIS or running on IIS Express locally.</span></span> <span data-ttu-id="e6b6b-112">Pokyny platí pro nasazení plochy systému Windows Server i Windows.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-112">The guidance applies to both Windows Server and Windows desktop deployments.</span></span>

[<span data-ttu-id="e6b6b-113">Vymazat mezipaměti balíčků</span><span class="sxs-lookup"><span data-stu-id="e6b6b-113">Clear package caches</span></span>](#clear-package-caches)  
<span data-ttu-id="e6b6b-114">Vysvětluje, co dělat, když nesouvislé balíčky přerušit aplikaci při provádění hlavních upgradů nebo změny verze balíčku.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-114">Explains what to do when incoherent packages break an app when performing major upgrades or changing package versions.</span></span>

[<span data-ttu-id="e6b6b-115">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="e6b6b-115">Additional resources</span></span>](#additional-resources)  
<span data-ttu-id="e6b6b-116">Obsahuje další témata pro řešení potíží.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-116">Lists additional troubleshooting topics.</span></span>

## <a name="app-startup-errors"></a><span data-ttu-id="e6b6b-117">Chyby při spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="e6b6b-117">App startup errors</span></span>

<span data-ttu-id="e6b6b-118">V sadě Visual Studio, ASP.NET projektu Core výchozí [iis express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hostování během ladění.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-118">In Visual Studio, an ASP.NET Core project defaults to [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hosting during debugging.</span></span> <span data-ttu-id="e6b6b-119">A *502.5 - Selhání procesu* nebo *500.30 - Start Selhání,* ke kterému dochází při ladění místně lze diagnostikovat pomocí rady v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-119">A *502.5 - Process Failure* or a *500.30 - Start Failure* that occurs when debugging locally can be diagnosed using the advice in this topic.</span></span>

### <a name="40314-forbidden"></a><span data-ttu-id="e6b6b-120">403.14 Zakázáno</span><span class="sxs-lookup"><span data-stu-id="e6b6b-120">403.14 Forbidden</span></span>

<span data-ttu-id="e6b6b-121">Aplikace se nespustí.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-121">The app fails to start.</span></span> <span data-ttu-id="e6b6b-122">Je zaznamenána následující chyba:</span><span class="sxs-lookup"><span data-stu-id="e6b6b-122">The following error is logged:</span></span>

```
The Web server is configured to not list the contents of this directory.
```

<span data-ttu-id="e6b6b-123">Chyba je obvykle způsobena přerušeným nasazením v hostitelském systému, který zahrnuje některý z následujících scénářů:</span><span class="sxs-lookup"><span data-stu-id="e6b6b-123">The error is usually caused by a broken deployment on the hosting system, which includes any of the following scenarios:</span></span>

* <span data-ttu-id="e6b6b-124">Aplikace je nasazena do nesprávné složky v hostitelském systému.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-124">The app is deployed to the wrong folder on the hosting system.</span></span>
* <span data-ttu-id="e6b6b-125">Procesu nasazení se nepodařilo přesunout všechny soubory a složky aplikace do složky nasazení v hostitelském systému.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-125">The deployment process failed to move all of the app's files and folders to the deployment folder on the hosting system.</span></span>
* <span data-ttu-id="e6b6b-126">Soubor *web.config* chybí v nasazení nebo je obsah souboru *web.config* poškozen.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-126">The *web.config* file is missing from the deployment, or the *web.config* file contents are malformed.</span></span>

<span data-ttu-id="e6b6b-127">Proveďte následující kroky:</span><span class="sxs-lookup"><span data-stu-id="e6b6b-127">Perform the following steps:</span></span>

1. <span data-ttu-id="e6b6b-128">Odstraňte všechny soubory a složky ze složky nasazení v hostitelském systému.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-128">Delete all of the files and folders from the deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="e6b6b-129">Znovu nasadit obsah složky *publikování* aplikace do hostitelského systému pomocí normální metody nasazení, jako je Visual Studio, PowerShell nebo ruční nasazení:</span><span class="sxs-lookup"><span data-stu-id="e6b6b-129">Redeploy the contents of the app's *publish* folder to the hosting system using your normal method of deployment, such as Visual Studio, PowerShell, or manual deployment:</span></span>
   * <span data-ttu-id="e6b6b-130">Zkontrolujte, zda je soubor *web.config* v nasazení přítomen a zda je jeho obsah správný.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-130">Confirm that the *web.config* file is present in the deployment and that its contents are correct.</span></span>
   * <span data-ttu-id="e6b6b-131">Při hostování ve službě Azure App Service zkontrolujte, že se aplikace nasadí do `D:\home\site\wwwroot` složky.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-131">When hosting on Azure App Service, confirm that the app is deployed to the `D:\home\site\wwwroot` folder.</span></span>
   * <span data-ttu-id="e6b6b-132">Pokud je aplikace hostována službou IIS, zkontrolujte, zda je aplikace nasazena do **fyzické cesty** služby IIS zobrazené v **základním nastavení** **správce služby IIS**.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-132">When the app is hosted by IIS, confirm that the app is deployed to the IIS **Physical path** shown in **IIS Manager**'s **Basic Settings**.</span></span>
1. <span data-ttu-id="e6b6b-133">Zkontrolujte, zda jsou všechny soubory a složky aplikace nasazeny porovnáním nasazení v hostitelském systému s obsahem složky *publikování* projektu.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-133">Confirm that all of the app's files and folders are deployed by comparing the deployment on the hosting system to the contents of the project's *publish* folder.</span></span>

<span data-ttu-id="e6b6b-134">Další informace o rozložení publikované aplikace ASP.NET <xref:host-and-deploy/directory-structure>Core najdete v tématu .</span><span class="sxs-lookup"><span data-stu-id="e6b6b-134">For more information on the layout of a published ASP.NET Core app, see <xref:host-and-deploy/directory-structure>.</span></span> <span data-ttu-id="e6b6b-135">Další informace o souboru *web.config* naleznete v tématu <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-135">For more information on the *web.config* file, see <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>.</span></span>

### <a name="500-internal-server-error"></a><span data-ttu-id="e6b6b-136">500 – Interní chyba serveru</span><span class="sxs-lookup"><span data-stu-id="e6b6b-136">500 Internal Server Error</span></span>

<span data-ttu-id="e6b6b-137">Aplikace se spustí, ale chyba zabrání serveru v plnění požadavku.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-137">The app starts, but an error prevents the server from fulfilling the request.</span></span>

<span data-ttu-id="e6b6b-138">K této chybě dochází v kódu aplikace při spuštění nebo při vytváření odpovědi.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-138">This error occurs within the app's code during startup or while creating a response.</span></span> <span data-ttu-id="e6b6b-139">Odpověď může obsahovat žádný obsah nebo odpověď může zobrazit jako *500 Vnitřní chyba serveru* v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-139">The response may contain no content, or the response may appear as a *500 Internal Server Error* in the browser.</span></span> <span data-ttu-id="e6b6b-140">Protokol událostí aplikace obvykle uvádí, že aplikace byla spuštěna normálně.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-140">The Application Event Log usually states that the app started normally.</span></span> <span data-ttu-id="e6b6b-141">Z pohledu serveru je to správné.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-141">From the server's perspective, that's correct.</span></span> <span data-ttu-id="e6b6b-142">Aplikace se spustila, ale nemůže generovat platnou odpověď.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-142">The app did start, but it can't generate a valid response.</span></span> <span data-ttu-id="e6b6b-143">Spusťte aplikaci na příkazovém řádku na serveru nebo povolte protokol stdout ASP.NET Core Module k řešení problému.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-143">Run the app at a command prompt on the server or enable the ASP.NET Core Module stdout log to troubleshoot the problem.</span></span>

### <a name="5000-in-process-handler-load-failure"></a><span data-ttu-id="e6b6b-144">Selhání zatížení obslužné rutiny procesu 500.0</span><span class="sxs-lookup"><span data-stu-id="e6b6b-144">500.0 In-Process Handler Load Failure</span></span>

<span data-ttu-id="e6b6b-145">Pracovní proces se nezdaří.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-145">The worker process fails.</span></span> <span data-ttu-id="e6b6b-146">Aplikace se nespustí.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-146">The app doesn't start.</span></span>

<span data-ttu-id="e6b6b-147">Došlo k neznámé chybě načítání ASP.NET součástmi [základního modulu.](xref:host-and-deploy/aspnet-core-module)</span><span class="sxs-lookup"><span data-stu-id="e6b6b-147">An unknown error occurred loading [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) components.</span></span> <span data-ttu-id="e6b6b-148">Proveďte jednu z následujících akcí:</span><span class="sxs-lookup"><span data-stu-id="e6b6b-148">Take one of the following actions:</span></span>

* <span data-ttu-id="e6b6b-149">Obraťte se [na podporu společnosti Microsoft](https://support.microsoft.com/oas/default.aspx?prid=15832) (vyberte nástroje pro **vývojáře** **a potom ASP.NET jádrem).**</span><span class="sxs-lookup"><span data-stu-id="e6b6b-149">Contact [Microsoft Support](https://support.microsoft.com/oas/default.aspx?prid=15832) (select **Developer Tools** then **ASP.NET Core**).</span></span>
* <span data-ttu-id="e6b6b-150">Zeptejte se na Stack Přetečení.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-150">Ask a question on Stack Overflow.</span></span>
* <span data-ttu-id="e6b6b-151">Posuzte problém na našem [úložišti GitHub](https://github.com/dotnet/AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="e6b6b-151">File an issue on our [GitHub repository](https://github.com/dotnet/AspNetCore).</span></span>

### <a name="50030-in-process-startup-failure"></a><span data-ttu-id="e6b6b-152">500.30 Selhání spuštění v průběhu procesu</span><span class="sxs-lookup"><span data-stu-id="e6b6b-152">500.30 In-Process Startup Failure</span></span>

<span data-ttu-id="e6b6b-153">Pracovní proces se nezdaří.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-153">The worker process fails.</span></span> <span data-ttu-id="e6b6b-154">Aplikace se nespustí.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-154">The app doesn't start.</span></span>

<span data-ttu-id="e6b6b-155">[ASP.NET základní modul](xref:host-and-deploy/aspnet-core-module) se pokusí spustit .NET Core CLR v procesu, ale nepodaří spustit.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-155">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the .NET Core CLR in-process, but it fails to start.</span></span> <span data-ttu-id="e6b6b-156">Příčinu selhání spuštění procesu lze obvykle určit z položek v protokolu událostí aplikace a ASP.NET protokolu stdout základního modulu.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-156">The cause of a process startup failure can usually be determined from entries in the Application Event Log and the ASP.NET Core Module stdout log.</span></span>

<span data-ttu-id="e6b6b-157">Běžné podmínky selhání:</span><span class="sxs-lookup"><span data-stu-id="e6b6b-157">Common failure conditions:</span></span>

* <span data-ttu-id="e6b6b-158">Aplikace je nesprávně nakonfigurována kvůli cílení na verzi sdíleného rozhraní ASP.NET Core, která není k dispozici.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-158">The app is misconfigured due to targeting a version of the ASP.NET Core shared framework that isn't present.</span></span> <span data-ttu-id="e6b6b-159">Zkontrolujte, které verze ASP.NET sdíleného rozhraní Core jsou nainstalovány v cílovém počítači.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-159">Check which versions of the ASP.NET Core shared framework are installed on the target machine.</span></span>
* <span data-ttu-id="e6b6b-160">Použití azure key vault, nedostatek oprávnění k trezoru klíčů.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-160">Using Azure Key Vault, lack of permissions to the Key Vault.</span></span> <span data-ttu-id="e6b6b-161">Zkontrolujte zásady přístupu v cílovém trezoru klíčů a ujistěte se, že jsou udělena správná oprávnění.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-161">Check the access policies in the targeted Key Vault to ensure that the correct permissions are granted.</span></span>

### <a name="50031-ancm-failed-to-find-native-dependencies"></a><span data-ttu-id="e6b6b-162">500.31 ANCM se nepodařilo najít nativní závislosti</span><span class="sxs-lookup"><span data-stu-id="e6b6b-162">500.31 ANCM Failed to Find Native Dependencies</span></span>

<span data-ttu-id="e6b6b-163">Pracovní proces se nezdaří.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-163">The worker process fails.</span></span> <span data-ttu-id="e6b6b-164">Aplikace se nespustí.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-164">The app doesn't start.</span></span>

<span data-ttu-id="e6b6b-165">Základní [modul ASP.NET](xref:host-and-deploy/aspnet-core-module) pokusí spustit modul .NET Core v procesu, ale nepodaří se jej spustit.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-165">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the .NET Core runtime in-process, but it fails to start.</span></span> <span data-ttu-id="e6b6b-166">Nejčastější příčinou tohoto selhání při spuštění `Microsoft.NETCore.App` `Microsoft.AspNetCore.App` je, když není nainstalován nebo runtime.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-166">The most common cause of this startup failure is when the `Microsoft.NETCore.App` or `Microsoft.AspNetCore.App` runtime isn't installed.</span></span> <span data-ttu-id="e6b6b-167">Pokud se aplikace nasadí na cílovou ASP.NET Core 3.0 a tato verze v počítači neexistuje, dojde k této chybě.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-167">If the app is deployed to target ASP.NET Core 3.0 and that version doesn't exist on the machine, this error occurs.</span></span> <span data-ttu-id="e6b6b-168">Následuje ukázková chybová zpráva:</span><span class="sxs-lookup"><span data-stu-id="e6b6b-168">An example error message follows:</span></span>

```
The specified framework 'Microsoft.NETCore.App', version '3.0.0' was not found.
  - The following frameworks were found:
      2.2.1 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview5-27626-15 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27713-13 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27714-15 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27723-08 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
```

<span data-ttu-id="e6b6b-169">Chybová zpráva obsahuje seznam všech nainstalovaných verzí rozhraní .NET Core a verze požadované aplikací.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-169">The error message lists all the installed .NET Core versions and the version requested by the app.</span></span> <span data-ttu-id="e6b6b-170">Chcete-li tuto chybu opravit, buď:</span><span class="sxs-lookup"><span data-stu-id="e6b6b-170">To fix this error, either:</span></span>

* <span data-ttu-id="e6b6b-171">Nainstalujte do počítače příslušnou verzi rozhraní .NET Core.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-171">Install the appropriate version of .NET Core on the machine.</span></span>
* <span data-ttu-id="e6b6b-172">Změňte aplikaci tak, aby cílila na verzi .NET Core, která je v počítači k dispozici.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-172">Change the app to target a version of .NET Core that's present on the machine.</span></span>
* <span data-ttu-id="e6b6b-173">Publikujte aplikaci jako [samostatné nasazení](/dotnet/core/deploying/#self-contained-deployments-scd).</span><span class="sxs-lookup"><span data-stu-id="e6b6b-173">Publish the app as a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>

<span data-ttu-id="e6b6b-174">Při spuštění ve `ASPNETCORE_ENVIRONMENT` vývoji (proměnná prostředí je nastavena na `Development`) konkrétní chyba je zapsána do odpovědi HTTP.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-174">When running in development (the `ASPNETCORE_ENVIRONMENT` environment variable is set to `Development`), the specific error is written to the HTTP response.</span></span> <span data-ttu-id="e6b6b-175">Příčina selhání spuštění procesu je také nalezena v protokolu událostí aplikace.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-175">The cause of a process startup failure is also found in the Application Event Log.</span></span>

### <a name="50032-ancm-failed-to-load-dll"></a><span data-ttu-id="e6b6b-176">500.32 ANCM se nepodařilo načíst dll</span><span class="sxs-lookup"><span data-stu-id="e6b6b-176">500.32 ANCM Failed to Load dll</span></span>

<span data-ttu-id="e6b6b-177">Pracovní proces se nezdaří.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-177">The worker process fails.</span></span> <span data-ttu-id="e6b6b-178">Aplikace se nespustí.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-178">The app doesn't start.</span></span>

<span data-ttu-id="e6b6b-179">Nejčastější příčinou této chyby je, že aplikace je publikována pro nekompatibilní architekturu procesoru.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-179">The most common cause for this error is that the app is published for an incompatible processor architecture.</span></span> <span data-ttu-id="e6b6b-180">Pokud pracovní proces běží jako 32bitová aplikace a aplikace byla publikována na cíl 64bit, dojde k této chybě.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-180">If the worker process is running as a 32-bit app and the app was published to target 64-bit, this error occurs.</span></span>

<span data-ttu-id="e6b6b-181">Chcete-li tuto chybu opravit, buď:</span><span class="sxs-lookup"><span data-stu-id="e6b6b-181">To fix this error, either:</span></span>

* <span data-ttu-id="e6b6b-182">Znovu publikujte aplikaci pro stejnou architekturu procesoru jako pracovní proces.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-182">Republish the app for the same processor architecture as the worker process.</span></span>
* <span data-ttu-id="e6b6b-183">Publikujte aplikaci jako [nasazení závislé na rámci](/dotnet/core/deploying/#framework-dependent-executables-fde).</span><span class="sxs-lookup"><span data-stu-id="e6b6b-183">Publish the app as a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-executables-fde).</span></span>

### <a name="50033-ancm-request-handler-load-failure"></a><span data-ttu-id="e6b6b-184">500.33 Ancm Request Handler Selhání zatížení</span><span class="sxs-lookup"><span data-stu-id="e6b6b-184">500.33 ANCM Request Handler Load Failure</span></span>

<span data-ttu-id="e6b6b-185">Pracovní proces se nezdaří.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-185">The worker process fails.</span></span> <span data-ttu-id="e6b6b-186">Aplikace se nespustí.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-186">The app doesn't start.</span></span>

<span data-ttu-id="e6b6b-187">Aplikace neodkazovala na `Microsoft.AspNetCore.App` rozhraní.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-187">The app didn't reference the `Microsoft.AspNetCore.App` framework.</span></span> <span data-ttu-id="e6b6b-188">ASP.NET core `Microsoft.AspNetCore.App` modulu může hostovat pouze aplikace, které cílí na [architekturu](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="e6b6b-188">Only apps targeting the `Microsoft.AspNetCore.App` framework can be hosted by the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span>

<span data-ttu-id="e6b6b-189">Chcete-li tuto chybu opravit, zkontrolujte, zda aplikace cílí na architekturu. `Microsoft.AspNetCore.App`</span><span class="sxs-lookup"><span data-stu-id="e6b6b-189">To fix this error, confirm that the app is targeting the `Microsoft.AspNetCore.App` framework.</span></span> <span data-ttu-id="e6b6b-190">Zkontrolujte, `.runtimeconfig.json` zda chcete ověřit rámec, na který se aplikace zaměřuje.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-190">Check the `.runtimeconfig.json` to verify the framework targeted by the app.</span></span>

### <a name="50034-ancm-mixed-hosting-models-not-supported"></a><span data-ttu-id="e6b6b-191">500.34 CM smíšené hostingové modely nejsou podporovány</span><span class="sxs-lookup"><span data-stu-id="e6b6b-191">500.34 ANCM Mixed Hosting Models Not Supported</span></span>

<span data-ttu-id="e6b6b-192">Pracovní proces nemůže spustit v procesu aplikace a mimo proces aplikace ve stejném procesu.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-192">The worker process can't run both an in-process app and an out-of-process app in the same process.</span></span>

<span data-ttu-id="e6b6b-193">Chcete-li tuto chybu opravit, spouštějte aplikace v samostatných fondech aplikací služby IIS.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-193">To fix this error, run apps in separate IIS application pools.</span></span>

### <a name="50035-ancm-multiple-in-process-applications-in-same-process"></a><span data-ttu-id="e6b6b-194">500.35 ANCM více in-process aplikací ve stejném procesu</span><span class="sxs-lookup"><span data-stu-id="e6b6b-194">500.35 ANCM Multiple In-Process Applications in same Process</span></span>

<span data-ttu-id="e6b6b-195">Pracovní proces nemůže ve stejném procesu spouštět více aplikací v procesu.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-195">The worker process can't run multiple in-process apps in the same process.</span></span>

<span data-ttu-id="e6b6b-196">Chcete-li tuto chybu opravit, spouštějte aplikace v samostatných fondech aplikací služby IIS.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-196">To fix this error, run apps in separate IIS application pools.</span></span>

### <a name="50036-ancm-out-of-process-handler-load-failure"></a><span data-ttu-id="e6b6b-197">500.36 ANCM selhání zatížení obslužné rutiny</span><span class="sxs-lookup"><span data-stu-id="e6b6b-197">500.36 ANCM Out-Of-Process Handler Load Failure</span></span>

<span data-ttu-id="e6b6b-198">Obslužná rutina požadavku mimo proces *aspnetcorev2_outofprocess.dll*není vedle souboru *aspnetcorev2.dll.*</span><span class="sxs-lookup"><span data-stu-id="e6b6b-198">The out-of-process request handler, *aspnetcorev2_outofprocess.dll*, isn't next to the *aspnetcorev2.dll* file.</span></span> <span data-ttu-id="e6b6b-199">To znamená poškozenou instalaci [ASP.NET základního modulu](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="e6b6b-199">This indicates a corrupted installation of the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span>

<span data-ttu-id="e6b6b-200">Chcete-li tuto chybu opravit, opravte instalaci [sady .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) (pro službu IIS) nebo sady Visual Studio (pro službu IIS Express).</span><span class="sxs-lookup"><span data-stu-id="e6b6b-200">To fix this error, repair the installation of the [.NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) (for IIS) or Visual Studio (for IIS Express).</span></span>

### <a name="50037-ancm-failed-to-start-within-startup-time-limit"></a><span data-ttu-id="e6b6b-201">500.37 ANCM se nepodařilo spustit v rámci časového limitu spuštění</span><span class="sxs-lookup"><span data-stu-id="e6b6b-201">500.37 ANCM Failed to Start Within Startup Time Limit</span></span>

<span data-ttu-id="e6b6b-202">Ancm se nepodařilo spustit v rámci provied čas spuštění časového limitu.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-202">ANCM failed to start within the provied startup time limit.</span></span> <span data-ttu-id="e6b6b-203">Ve výchozím nastavení je časový limit 120 sekund.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-203">By default, the timeout is 120 seconds.</span></span>

<span data-ttu-id="e6b6b-204">K této chybě může dojít při spuštění velkého počtu aplikací ve stejném počítači.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-204">This error can occur when starting a large number of apps on the same machine.</span></span> <span data-ttu-id="e6b6b-205">Při spuštění zkontrolujte špičky využití procesoru a paměti na serveru.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-205">Check for CPU/Memory usage spikes on the server during startup.</span></span> <span data-ttu-id="e6b6b-206">Možná budete muset rozložit proces spuštění více aplikací.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-206">You may need to stagger the startup process of multiple apps.</span></span>

### <a name="5025-process-failure"></a><span data-ttu-id="e6b6b-207">502.5 – Selhání procesu</span><span class="sxs-lookup"><span data-stu-id="e6b6b-207">502.5 Process Failure</span></span>

<span data-ttu-id="e6b6b-208">Pracovní proces se nezdaří.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-208">The worker process fails.</span></span> <span data-ttu-id="e6b6b-209">Aplikace se nespustí.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-209">The app doesn't start.</span></span>

<span data-ttu-id="e6b6b-210">Základní [modul ASP.NET](xref:host-and-deploy/aspnet-core-module) se pokusí spustit pracovní proces, ale nepodaří se jej spustit.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-210">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the worker process but it fails to start.</span></span> <span data-ttu-id="e6b6b-211">Příčinu selhání spuštění procesu lze obvykle určit z položek v protokolu událostí aplikace a ASP.NET protokolu stdout základního modulu.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-211">The cause of a process startup failure can usually be determined from entries in the Application Event Log and the ASP.NET Core Module stdout log.</span></span>

<span data-ttu-id="e6b6b-212">Obvyklá podmínka selhání je, že aplikace je nesprávně nakonfigurovaná kvůli cílení na verzi sdíleného rozhraní ASP.NET Core, která není k dispozici.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-212">A common failure condition is the app is misconfigured due to targeting a version of the ASP.NET Core shared framework that isn't present.</span></span> <span data-ttu-id="e6b6b-213">Zkontrolujte, které verze ASP.NET sdíleného rozhraní Core jsou nainstalovány v cílovém počítači.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-213">Check which versions of the ASP.NET Core shared framework are installed on the target machine.</span></span> <span data-ttu-id="e6b6b-214">Sdílená *architektura* je sada sestavení (*soubory DLL),* které jsou nainstalovány v počítači a odkazovány metabalíčkem, například `Microsoft.AspNetCore.App`.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-214">The *shared framework* is the set of assemblies (*.dll* files) that are installed on the machine and referenced by a metapackage such as `Microsoft.AspNetCore.App`.</span></span> <span data-ttu-id="e6b6b-215">Odkaz na metabalíček může určit minimální požadovanou verzi.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-215">The metapackage reference can specify a minimum required version.</span></span> <span data-ttu-id="e6b6b-216">Další informace naleznete [v tématu sdílený rámec](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span><span class="sxs-lookup"><span data-stu-id="e6b6b-216">For more information, see [The shared framework](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span></span>

<span data-ttu-id="e6b6b-217">Chybová stránka *502.5 Process Failure* je vrácena, když selhání konfigurace hostitele nebo aplikace způsobí selhání pracovního procesu:</span><span class="sxs-lookup"><span data-stu-id="e6b6b-217">The *502.5 Process Failure* error page is returned when a hosting or app misconfiguration causes the worker process to fail:</span></span>

### <a name="failed-to-start-application-errorcode-0x800700c1"></a><span data-ttu-id="e6b6b-218">Spuštění aplikace se nezdařilo (ErrorCode '0x800700c1')</span><span class="sxs-lookup"><span data-stu-id="e6b6b-218">Failed to start application (ErrorCode '0x800700c1')</span></span>

```
EventID: 1010
Source: IIS AspNetCore Module V2
Failed to start application '/LM/W3SVC/6/ROOT/', ErrorCode '0x800700c1'.
```

<span data-ttu-id="e6b6b-219">Spuštění aplikace se nezdařilo, protože nebylo možné načíst sestavení aplikace (*DLL*).</span><span class="sxs-lookup"><span data-stu-id="e6b6b-219">The app failed to start because the app's assembly (*.dll*) couldn't be loaded.</span></span>

<span data-ttu-id="e6b6b-220">K této chybě dochází, pokud je neodpovídající bitness mezi publikovanou aplikací a w3wp/iisexpress procesu.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-220">This error occurs when there's a bitness mismatch between the published app and the w3wp/iisexpress process.</span></span>

<span data-ttu-id="e6b6b-221">Zkontrolujte, zda je 32bitové nastavení fondu aplikací správné:</span><span class="sxs-lookup"><span data-stu-id="e6b6b-221">Confirm that the app pool's 32-bit setting is correct:</span></span>

1. <span data-ttu-id="e6b6b-222">Vyberte fond aplikací ve **fondech aplikací**Správce služby IIS .</span><span class="sxs-lookup"><span data-stu-id="e6b6b-222">Select the app pool in IIS Manager's **Application Pools**.</span></span>
1. <span data-ttu-id="e6b6b-223">V části Upravit fond aplikací v panelu **Akce** vyberte **Upřesnit nastavení** v části Upravit **fond aplikací.**</span><span class="sxs-lookup"><span data-stu-id="e6b6b-223">Select **Advanced Settings** under **Edit Application Pool** in the **Actions** panel.</span></span>
1. <span data-ttu-id="e6b6b-224">Nastavit **povolit 32bitové aplikace**:</span><span class="sxs-lookup"><span data-stu-id="e6b6b-224">Set **Enable 32-Bit Applications**:</span></span>
   * <span data-ttu-id="e6b6b-225">Pokud nasazujete 32bitovou (x86) `True`aplikaci, nastavte hodnotu na .</span><span class="sxs-lookup"><span data-stu-id="e6b6b-225">If deploying a 32-bit (x86) app, set the value to `True`.</span></span>
   * <span data-ttu-id="e6b6b-226">Pokud nasazujete 64bitovou (x64) `False`aplikaci, nastavte hodnotu na .</span><span class="sxs-lookup"><span data-stu-id="e6b6b-226">If deploying a 64-bit (x64) app, set the value to `False`.</span></span>

<span data-ttu-id="e6b6b-227">Zkontrolujte, zda v souboru `<Platform>` projektu není konflikt mezi vlastností MSBuild a publikovanou bitness aplikace.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-227">Confirm that there isn't a conflict between a `<Platform>` MSBuild property in the project file and the published bitness of the app.</span></span>

### <a name="connection-reset"></a><span data-ttu-id="e6b6b-228">Resetování připojení</span><span class="sxs-lookup"><span data-stu-id="e6b6b-228">Connection reset</span></span>

<span data-ttu-id="e6b6b-229">Pokud dojde k chybě po odeslání záhlaví, je příliš pozdě na to, aby server odeslal **chybu interního serveru 500,** když dojde k chybě.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-229">If an error occurs after the headers are sent, it's too late for the server to send a **500 Internal Server Error** when an error occurs.</span></span> <span data-ttu-id="e6b6b-230">K tomu často dochází, když dojde k chybě během serializace složitých objektů pro odpověď.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-230">This often happens when an error occurs during the serialization of complex objects for a response.</span></span> <span data-ttu-id="e6b6b-231">Tento typ chyby se zobrazí jako chyba *obnovení připojení* v klientovi.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-231">This type of error appears as a *connection reset* error on the client.</span></span> <span data-ttu-id="e6b6b-232">[Protokolování aplikací](xref:fundamentals/logging/index) může pomoci při řešení těchto typů chyb.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-232">[Application logging](xref:fundamentals/logging/index) can help troubleshoot these types of errors.</span></span>

### <a name="default-startup-limits"></a><span data-ttu-id="e6b6b-233">Výchozí limity pro spuštění</span><span class="sxs-lookup"><span data-stu-id="e6b6b-233">Default startup limits</span></span>

<span data-ttu-id="e6b6b-234">[Základní modul ASP.NET](xref:host-and-deploy/aspnet-core-module) je nakonfigurován s výchozím *spouštěcím limitem* 120 sekund.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-234">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) is configured with a default *startupTimeLimit* of 120 seconds.</span></span> <span data-ttu-id="e6b6b-235">Pokud zůstane na výchozí hodnotu, aplikace může trvat až dvě minuty ke spuštění, než modul zaznamená selhání procesu.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-235">When left at the default value, an app may take up to two minutes to start before the module logs a process failure.</span></span> <span data-ttu-id="e6b6b-236">Informace o konfiguraci modulu naleznete v [tématu Atributy prvku aspNetCore](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span><span class="sxs-lookup"><span data-stu-id="e6b6b-236">For information on configuring the module, see [Attributes of the aspNetCore element](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span></span>

## <a name="troubleshoot-on-azure-app-service"></a><span data-ttu-id="e6b6b-237">Poradce při potížích se službou Azure App Service</span><span class="sxs-lookup"><span data-stu-id="e6b6b-237">Troubleshoot on Azure App Service</span></span>

[!INCLUDE [Azure App Service Preview Notice](~/includes/azure-apps-preview-notice.md)]

### <a name="application-event-log-azure-app-service"></a><span data-ttu-id="e6b6b-238">Protokol událostí aplikace (služba Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="e6b6b-238">Application Event Log (Azure App Service)</span></span>

<span data-ttu-id="e6b6b-239">Chcete-li získat přístup k protokolu událostí aplikace, použijte okno **Diagnostikovat a vyřešit problémy** na webu Azure Portal:</span><span class="sxs-lookup"><span data-stu-id="e6b6b-239">To access the Application Event Log, use the **Diagnose and solve problems** blade in the Azure portal:</span></span>

1. <span data-ttu-id="e6b6b-240">Na webu Azure Portal otevřete aplikaci ve **Službách aplikací**.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-240">In the Azure portal, open the app in **App Services**.</span></span>
1. <span data-ttu-id="e6b6b-241">Vyberte **Diagnostikovat a řešit problémy**.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-241">Select **Diagnose and solve problems**.</span></span>
1. <span data-ttu-id="e6b6b-242">Vyberte nadpis **Diagnostické nástroje.**</span><span class="sxs-lookup"><span data-stu-id="e6b6b-242">Select the **Diagnostic Tools** heading.</span></span>
1. <span data-ttu-id="e6b6b-243">V části **Nástroje podpory**vyberte tlačítko **Události aplikace.**</span><span class="sxs-lookup"><span data-stu-id="e6b6b-243">Under **Support Tools**, select the **Application Events** button.</span></span>
1. <span data-ttu-id="e6b6b-244">Prohlédněte si nejnovější chybu poskytovanou položkou *AspNetCoreModule* nebo *IIS AspNetCoreModule V2* ve sloupci **Zdroj.**</span><span class="sxs-lookup"><span data-stu-id="e6b6b-244">Examine the latest error provided by the *IIS AspNetCoreModule* or *IIS AspNetCoreModule V2* entry in the **Source** column.</span></span>

<span data-ttu-id="e6b6b-245">Alternativou k použití **funkce Diagnostika a řešení problémů** je prozkoumat soubor protokolu událostí aplikace přímo pomocí [kudu](https://github.com/projectkudu/kudu/wiki):</span><span class="sxs-lookup"><span data-stu-id="e6b6b-245">An alternative to using the **Diagnose and solve problems** blade is to examine the Application Event Log file directly using [Kudu](https://github.com/projectkudu/kudu/wiki):</span></span>

1. <span data-ttu-id="e6b6b-246">Otevřete **rozšířené nástroje** v oblasti **vývojových nástrojů.**</span><span class="sxs-lookup"><span data-stu-id="e6b6b-246">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="e6b6b-247">Vyberte tlačítko \*\*Přejít.&rarr; \*\*</span><span class="sxs-lookup"><span data-stu-id="e6b6b-247">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="e6b6b-248">Konzole Kudu se otevře v novém kartě prohlížeče nebo v okně.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-248">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="e6b6b-249">Pomocí navigačního panelu v horní části stránky otevřete **konzolu ladění** a vyberte **možnost CMD**.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-249">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="e6b6b-250">Otevřete složku **LogFiles.**</span><span class="sxs-lookup"><span data-stu-id="e6b6b-250">Open the **LogFiles** folder.</span></span>
1. <span data-ttu-id="e6b6b-251">Vyberte ikonu tužky vedle souboru *eventlog.xml.*</span><span class="sxs-lookup"><span data-stu-id="e6b6b-251">Select the pencil icon next to the *eventlog.xml* file.</span></span>
1. <span data-ttu-id="e6b6b-252">Prohlédněte si protokol.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-252">Examine the log.</span></span> <span data-ttu-id="e6b6b-253">Přejděte do dolní části protokolu zobrazíte nejnovější události.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-253">Scroll to the bottom of the log to see the most recent events.</span></span>

### <a name="run-the-app-in-the-kudu-console"></a><span data-ttu-id="e6b6b-254">Spuštění aplikace v konzoli Kudu</span><span class="sxs-lookup"><span data-stu-id="e6b6b-254">Run the app in the Kudu console</span></span>

<span data-ttu-id="e6b6b-255">Mnoho chyb při spuštění nevytváří užitečné informace v protokolu událostí aplikace.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-255">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="e6b6b-256">Aplikaci můžete spustit v konzole vzdáleného spuštění [Kudu](https://github.com/projectkudu/kudu/wiki) a zjistit chybu:</span><span class="sxs-lookup"><span data-stu-id="e6b6b-256">You can run the app in the [Kudu](https://github.com/projectkudu/kudu/wiki) Remote Execution Console to discover the error:</span></span>

1. <span data-ttu-id="e6b6b-257">Otevřete **rozšířené nástroje** v oblasti **vývojových nástrojů.**</span><span class="sxs-lookup"><span data-stu-id="e6b6b-257">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="e6b6b-258">Vyberte tlačítko \*\*Přejít.&rarr; \*\*</span><span class="sxs-lookup"><span data-stu-id="e6b6b-258">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="e6b6b-259">Konzole Kudu se otevře v novém kartě prohlížeče nebo v okně.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-259">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="e6b6b-260">Pomocí navigačního panelu v horní části stránky otevřete **konzolu ladění** a vyberte **možnost CMD**.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-260">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>

#### <a name="test-a-32-bit-x86-app"></a><span data-ttu-id="e6b6b-261">Testování 32bitové aplikace (x86)</span><span class="sxs-lookup"><span data-stu-id="e6b6b-261">Test a 32-bit (x86) app</span></span>

<span data-ttu-id="e6b6b-262">**Aktuální verze**</span><span class="sxs-lookup"><span data-stu-id="e6b6b-262">**Current release**</span></span>

1. `cd d:\home\site\wwwroot`
1. <span data-ttu-id="e6b6b-263">Spusťte aplikaci:</span><span class="sxs-lookup"><span data-stu-id="e6b6b-263">Run the app:</span></span>
   * <span data-ttu-id="e6b6b-264">Pokud je aplikace [nasazení závislé na rámci](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span><span class="sxs-lookup"><span data-stu-id="e6b6b-264">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

     ```dotnetcli
     dotnet .\{ASSEMBLY NAME}.dll
     ```

   * <span data-ttu-id="e6b6b-265">Pokud se jedná o [samostatné nasazení](/dotnet/core/deploying/#self-contained-deployments-scd)aplikace :</span><span class="sxs-lookup"><span data-stu-id="e6b6b-265">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

     ```console
     {ASSEMBLY NAME}.exe
     ```

<span data-ttu-id="e6b6b-266">Výstup konzoly z aplikace, v němž se zobrazují všechny chyby, je předán do konzoly Kudu.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-266">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="e6b6b-267">**Nasazení závislé na rozhraní spuštěné ve verzi preview**</span><span class="sxs-lookup"><span data-stu-id="e6b6b-267">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="e6b6b-268">*Vyžaduje instalaci rozšíření webu ASP.NET Core {VERSION} (x86).*</span><span class="sxs-lookup"><span data-stu-id="e6b6b-268">*Requires installing the ASP.NET Core {VERSION} (x86) Runtime site extension.*</span></span>

1. <span data-ttu-id="e6b6b-269">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32`(`{X.Y}` je runtime verze)</span><span class="sxs-lookup"><span data-stu-id="e6b6b-269">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="e6b6b-270">Spusťte aplikaci: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="e6b6b-270">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="e6b6b-271">Výstup konzoly z aplikace, v němž se zobrazují všechny chyby, je předán do konzoly Kudu.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-271">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

#### <a name="test-a-64-bit-x64-app"></a><span data-ttu-id="e6b6b-272">Testování 64bitové aplikace (x64)</span><span class="sxs-lookup"><span data-stu-id="e6b6b-272">Test a 64-bit (x64) app</span></span>

<span data-ttu-id="e6b6b-273">**Aktuální verze**</span><span class="sxs-lookup"><span data-stu-id="e6b6b-273">**Current release**</span></span>

* <span data-ttu-id="e6b6b-274">Pokud je aplikace 64bitové (x64) [nasazení závislé na rozhraní](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span><span class="sxs-lookup"><span data-stu-id="e6b6b-274">If the app is a 64-bit (x64) [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>
  1. `cd D:\Program Files\dotnet`
  1. <span data-ttu-id="e6b6b-275">Spusťte aplikaci: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="e6b6b-275">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>
* <span data-ttu-id="e6b6b-276">Pokud se jedná o [samostatné nasazení](/dotnet/core/deploying/#self-contained-deployments-scd)aplikace :</span><span class="sxs-lookup"><span data-stu-id="e6b6b-276">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>
  1. `cd D:\home\site\wwwroot`
  1. <span data-ttu-id="e6b6b-277">Spusťte aplikaci: `{ASSEMBLY NAME}.exe`</span><span class="sxs-lookup"><span data-stu-id="e6b6b-277">Run the app: `{ASSEMBLY NAME}.exe`</span></span>

<span data-ttu-id="e6b6b-278">Výstup konzoly z aplikace, v němž se zobrazují všechny chyby, je předán do konzoly Kudu.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-278">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="e6b6b-279">**Nasazení závislé na rozhraní spuštěné ve verzi preview**</span><span class="sxs-lookup"><span data-stu-id="e6b6b-279">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="e6b6b-280">*Vyžaduje instalaci rozšíření webu ASP.NET Core {VERSION} (x64).*</span><span class="sxs-lookup"><span data-stu-id="e6b6b-280">*Requires installing the ASP.NET Core {VERSION} (x64) Runtime site extension.*</span></span>

1. <span data-ttu-id="e6b6b-281">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64`(`{X.Y}` je runtime verze)</span><span class="sxs-lookup"><span data-stu-id="e6b6b-281">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="e6b6b-282">Spusťte aplikaci: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="e6b6b-282">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="e6b6b-283">Výstup konzoly z aplikace, v němž se zobrazují všechny chyby, je předán do konzoly Kudu.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-283">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

### <a name="aspnet-core-module-stdout-log-azure-app-service"></a><span data-ttu-id="e6b6b-284">ASP.NET protokol stdout modulu (služba Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="e6b6b-284">ASP.NET Core Module stdout log (Azure App Service)</span></span>

<span data-ttu-id="e6b6b-285">Protokol stdout ASP.NET Core Module často zaznamenává užitečné chybové zprávy, které nebyly nalezeny v protokolu událostí aplikace.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-285">The ASP.NET Core Module stdout log often records useful error messages not found in the Application Event Log.</span></span> <span data-ttu-id="e6b6b-286">Povolení a zobrazení protokolů stdout:</span><span class="sxs-lookup"><span data-stu-id="e6b6b-286">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="e6b6b-287">Přejděte na okno **Diagnostikovat a vyřešit problémy** na webu Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-287">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="e6b6b-288">V **části SELECT PROBLEM CATEGORY**vyberte tlačítko Web App **Down** .</span><span class="sxs-lookup"><span data-stu-id="e6b6b-288">Under **SELECT PROBLEM CATEGORY**, select the **Web App Down** button.</span></span>
1. <span data-ttu-id="e6b6b-289">V části **Navrhovaná řešení** > **Povolit přesměrování protokolu Stdout**vyberte tlačítko **pro otevření konzoly Kudu a upravte web.Config**.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-289">Under **Suggested Solutions** > **Enable Stdout Log Redirection**, select the button to **Open Kudu Console to edit Web.Config**.</span></span>
1. <span data-ttu-id="e6b6b-290">V **diagnostické konzole**Kudu otevřete složky webu **site** > **wwwroot**.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-290">In the Kudu **Diagnostic Console**, open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="e6b6b-291">Posunutím dolů zobrazíte soubor *web.config* v dolní části seznamu.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-291">Scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="e6b6b-292">Klikněte na ikonu tužky vedle souboru *web.config.*</span><span class="sxs-lookup"><span data-stu-id="e6b6b-292">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="e6b6b-293">Nastavte **stdoutLogEnabled** na `true` cestu **stdoutLogFile** `\\?\%home%\LogFiles\stdout`a změňte ji na: .</span><span class="sxs-lookup"><span data-stu-id="e6b6b-293">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="e6b6b-294">Vyberte **Uložit,** chcete-li uložit aktualizovaný soubor *web.config.*</span><span class="sxs-lookup"><span data-stu-id="e6b6b-294">Select **Save** to save the updated *web.config* file.</span></span>
1. <span data-ttu-id="e6b6b-295">Požádejte o to do aplikace.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-295">Make a request to the app.</span></span>
1. <span data-ttu-id="e6b6b-296">Vraťte se na Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-296">Return to the Azure portal.</span></span> <span data-ttu-id="e6b6b-297">Vyberte okno **Pokročilé nástroje** v oblasti **NÁSTROJE VÝVOJE.**</span><span class="sxs-lookup"><span data-stu-id="e6b6b-297">Select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="e6b6b-298">Vyberte tlačítko \*\*Přejít.&rarr; \*\*</span><span class="sxs-lookup"><span data-stu-id="e6b6b-298">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="e6b6b-299">Konzole Kudu se otevře v novém kartě prohlížeče nebo v okně.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-299">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="e6b6b-300">Pomocí navigačního panelu v horní části stránky otevřete **konzolu ladění** a vyberte **možnost CMD**.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-300">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="e6b6b-301">Vyberte složku **LogFiles.**</span><span class="sxs-lookup"><span data-stu-id="e6b6b-301">Select the **LogFiles** folder.</span></span>
1. <span data-ttu-id="e6b6b-302">Zkontrolujte sloupec **Změněno** a vyberte ikonu tužky a upravte protokol stdout s nejnovějším datem změny.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-302">Inspect the **Modified** column and select the pencil icon to edit the stdout log with the latest modification date.</span></span>
1. <span data-ttu-id="e6b6b-303">Po otevření souboru protokolu se zobrazí chyba.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-303">When the log file opens, the error is displayed.</span></span>

<span data-ttu-id="e6b6b-304">Zakažte protokolování stdout po dokončení řešení potíží:</span><span class="sxs-lookup"><span data-stu-id="e6b6b-304">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="e6b6b-305">V diagnostické **konzole**Kudu se vraťte na **web** > **wwwroot** cesty a odhalte soubor *web.config.*</span><span class="sxs-lookup"><span data-stu-id="e6b6b-305">In the Kudu **Diagnostic Console**, return to the path **site** > **wwwroot** to reveal the *web.config* file.</span></span> <span data-ttu-id="e6b6b-306">Znovu otevřete soubor **web.config** výběrem ikony tužky.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-306">Open the **web.config** file again by selecting the pencil icon.</span></span>
1. <span data-ttu-id="e6b6b-307">Nastavte **stdoutLogEnabled** na `false`.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-307">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="e6b6b-308">Chcete-li soubor uložit, vyberte **Uložit.**</span><span class="sxs-lookup"><span data-stu-id="e6b6b-308">Select **Save** to save the file.</span></span>

<span data-ttu-id="e6b6b-309">Další informace naleznete v tématu <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-309">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

> [!WARNING]
> <span data-ttu-id="e6b6b-310">Selhání zakázání protokolu stdout může vést k selhání aplikace nebo serveru.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-310">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="e6b6b-311">Neexistuje žádné omezení na velikost souboru protokolu nebo počet vytvořených souborů protokolu.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-311">There's no limit on log file size or the number of log files created.</span></span> <span data-ttu-id="e6b6b-312">K řešení problémů se spuštěním aplikace používejte protokolování stdout pouze.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-312">Only use stdout logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="e6b6b-313">Pro obecné protokolování v aplikaci ASP.NET Core po spuštění použijte knihovnu protokolování, která omezuje velikost souboru protokolu a otáčí protokoly.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-313">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="e6b6b-314">Další informace naleznete [v tématu zprostředkovatelé protokolování třetích stran](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="e6b6b-314">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="aspnet-core-module-debug-log-azure-app-service"></a><span data-ttu-id="e6b6b-315">protokol ladění ASP.NET základního modulu (služba Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="e6b6b-315">ASP.NET Core Module debug log (Azure App Service)</span></span>

<span data-ttu-id="e6b6b-316">Protokol ladění ASP.NET core modul poskytuje další, hlubší protokolování z ASP.NET core modulu.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-316">The ASP.NET Core Module debug log provides additional, deeper logging from the ASP.NET Core Module.</span></span> <span data-ttu-id="e6b6b-317">Povolení a zobrazení protokolů stdout:</span><span class="sxs-lookup"><span data-stu-id="e6b6b-317">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="e6b6b-318">Chcete-li povolit rozšířený diagnostický protokol, proveďte jednu z následujících akcí:</span><span class="sxs-lookup"><span data-stu-id="e6b6b-318">To enable the enhanced diagnostic log, perform either of the following:</span></span>
   * <span data-ttu-id="e6b6b-319">Podle pokynů v [rozšířené diagnostické protokoly](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) nakonfigurovat aplikaci pro rozšířené diagnostické protokolování.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-319">Follow the instructions in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) to configure the app for an enhanced diagnostic logging.</span></span> <span data-ttu-id="e6b6b-320">Znovu nasadit aplikaci.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-320">Redeploy the app.</span></span>
   * <span data-ttu-id="e6b6b-321">Přidejte `<handlerSettings>` zobrazené v [rozšířených diagnostických protokolech](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) do souboru *web.config* živé aplikace pomocí konzoly Kudu:</span><span class="sxs-lookup"><span data-stu-id="e6b6b-321">Add the `<handlerSettings>` shown in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) to the live app's *web.config* file using the Kudu console:</span></span>
     1. <span data-ttu-id="e6b6b-322">Otevřete **rozšířené nástroje** v oblasti **vývojových nástrojů.**</span><span class="sxs-lookup"><span data-stu-id="e6b6b-322">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="e6b6b-323">Vyberte tlačítko \*\*Přejít.&rarr; \*\*</span><span class="sxs-lookup"><span data-stu-id="e6b6b-323">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="e6b6b-324">Konzole Kudu se otevře v novém kartě prohlížeče nebo v okně.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-324">The Kudu console opens in a new browser tab or window.</span></span>
     1. <span data-ttu-id="e6b6b-325">Pomocí navigačního panelu v horní části stránky otevřete **konzolu ladění** a vyberte **možnost CMD**.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-325">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
     1. <span data-ttu-id="e6b6b-326">Otevřete složky na **webu** > cesty**wwwroot**.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-326">Open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="e6b6b-327">Upravte soubor *web.config* výběrem tlačítka tužky.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-327">Edit the *web.config* file by selecting the pencil button.</span></span> <span data-ttu-id="e6b6b-328">Přidejte `<handlerSettings>` oddíl, jak je znázorněno v [rozšířené diagnostické protokoly](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs).</span><span class="sxs-lookup"><span data-stu-id="e6b6b-328">Add the `<handlerSettings>` section as shown in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs).</span></span> <span data-ttu-id="e6b6b-329">Vyberte tlačítko **Uložit**.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-329">Select the **Save** button.</span></span>
1. <span data-ttu-id="e6b6b-330">Otevřete **rozšířené nástroje** v oblasti **vývojových nástrojů.**</span><span class="sxs-lookup"><span data-stu-id="e6b6b-330">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="e6b6b-331">Vyberte tlačítko \*\*Přejít.&rarr; \*\*</span><span class="sxs-lookup"><span data-stu-id="e6b6b-331">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="e6b6b-332">Konzole Kudu se otevře v novém kartě prohlížeče nebo v okně.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-332">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="e6b6b-333">Pomocí navigačního panelu v horní části stránky otevřete **konzolu ladění** a vyberte **možnost CMD**.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-333">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="e6b6b-334">Otevřete složky na **webu** > cesty**wwwroot**.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-334">Open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="e6b6b-335">Pokud jste nezadali cestu pro soubor *aspnetcore-debug.log,* soubor se zobrazí v seznamu.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-335">If you didn't supply a path for the *aspnetcore-debug.log* file, the file appears in the list.</span></span> <span data-ttu-id="e6b6b-336">Pokud jste zadali cestu, přejděte do umístění souboru protokolu.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-336">If you supplied a path, navigate to the location of the log file.</span></span>
1. <span data-ttu-id="e6b6b-337">Otevřete soubor protokolu pomocí tlačítka tužky vedle názvu souboru.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-337">Open the log file with the pencil button next to the file name.</span></span>

<span data-ttu-id="e6b6b-338">Zakažte protokolování ladění po dokončení řešení potíží:</span><span class="sxs-lookup"><span data-stu-id="e6b6b-338">Disable debug logging when troubleshooting is complete:</span></span>

<span data-ttu-id="e6b6b-339">Chcete-li protokol rozšířeného ladění zakázat, proveďte některou z následujících akcí:</span><span class="sxs-lookup"><span data-stu-id="e6b6b-339">To disable the enhanced debug log, perform either of the following:</span></span>

* <span data-ttu-id="e6b6b-340">Odeberte `<handlerSettings>` soubor *web.config* místně a znovu nasadit aplikaci.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-340">Remove the `<handlerSettings>` from the *web.config* file locally and redeploy the app.</span></span>
* <span data-ttu-id="e6b6b-341">Pomocí konzoly Kudu upravte soubor *web.config* a odeberte `<handlerSettings>` oddíl.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-341">Use the Kudu console to edit the *web.config* file and remove the `<handlerSettings>` section.</span></span> <span data-ttu-id="e6b6b-342">Uložte soubor.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-342">Save the file.</span></span>

<span data-ttu-id="e6b6b-343">Další informace naleznete v tématu <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-343">For more information, see <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span></span>

> [!WARNING]
> <span data-ttu-id="e6b6b-344">Selhání zakázání protokolu ladění může vést k selhání aplikace nebo serveru.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-344">Failure to disable the debug log can lead to app or server failure.</span></span> <span data-ttu-id="e6b6b-345">Neexistuje žádné omezení na velikost souboru protokolu.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-345">There's no limit on log file size.</span></span> <span data-ttu-id="e6b6b-346">Protokolování ladění používejte pouze k řešení potíží se spuštěním aplikace.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-346">Only use debug logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="e6b6b-347">Pro obecné protokolování v aplikaci ASP.NET Core po spuštění použijte knihovnu protokolování, která omezuje velikost souboru protokolu a otáčí protokoly.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-347">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="e6b6b-348">Další informace naleznete [v tématu zprostředkovatelé protokolování třetích stran](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="e6b6b-348">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="slow-or-hanging-app-azure-app-service"></a><span data-ttu-id="e6b6b-349">Pomalá nebo zavěšená aplikace (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="e6b6b-349">Slow or hanging app (Azure App Service)</span></span>

<span data-ttu-id="e6b6b-350">Když aplikace reaguje pomalu nebo se při žádosti zablokuje, přečtěte si následující články:</span><span class="sxs-lookup"><span data-stu-id="e6b6b-350">When an app responds slowly or hangs on a request, see the following articles:</span></span>

* [<span data-ttu-id="e6b6b-351">Řešení potíží s výkonem pomalých webových aplikací ve službě Azure App Service</span><span class="sxs-lookup"><span data-stu-id="e6b6b-351">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="e6b6b-352">Použití rozšíření webu Diagnostikátor selhání k zachycení výpisu pro občasné výjimky problémy nebo problémy s výkonem na Azure Web App</span><span class="sxs-lookup"><span data-stu-id="e6b6b-352">Use Crash Diagnoser Site Extension to Capture Dump for Intermittent Exception issues or performance issues on Azure Web App</span></span>](https://blogs.msdn.microsoft.com/asiatech/2015/12/28/use-crash-diagnoser-site-extension-to-capture-dump-for-intermittent-exception-issues-or-performance-issues-on-azure-web-app/)

### <a name="monitoring-blades"></a><span data-ttu-id="e6b6b-353">Monitorovací nože</span><span class="sxs-lookup"><span data-stu-id="e6b6b-353">Monitoring blades</span></span>

<span data-ttu-id="e6b6b-354">Monitorování blades poskytují alternativní řešení potíží k metodám popsaným výše v tématu.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-354">Monitoring blades provide an alternative troubleshooting experience to the methods described earlier in the topic.</span></span> <span data-ttu-id="e6b6b-355">Tyto čepele lze použít k diagnostice chyb řady 500.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-355">These blades can be used to diagnose 500-series errors.</span></span>

<span data-ttu-id="e6b6b-356">Zkontrolujte, zda jsou nainstalovány ASP.NET rozšíření core.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-356">Confirm that the ASP.NET Core Extensions are installed.</span></span> <span data-ttu-id="e6b6b-357">Pokud rozšíření nejsou nainstalovaná, nainstalujte je ručně:</span><span class="sxs-lookup"><span data-stu-id="e6b6b-357">If the extensions aren't installed, install them manually:</span></span>

1. <span data-ttu-id="e6b6b-358">V části **Čepele NÁSTROJE PRO VÝVOJ** vyberte okno **Rozšíření.**</span><span class="sxs-lookup"><span data-stu-id="e6b6b-358">In the **DEVELOPMENT TOOLS** blade section, select the **Extensions** blade.</span></span>
1. <span data-ttu-id="e6b6b-359">V seznamu by se měl **zobrazit ASP.NET rozšíření jádra.**</span><span class="sxs-lookup"><span data-stu-id="e6b6b-359">The **ASP.NET Core Extensions** should appear in the list.</span></span>
1. <span data-ttu-id="e6b6b-360">Pokud rozšíření nejsou nainstalovaná, vyberte tlačítko **Přidat.**</span><span class="sxs-lookup"><span data-stu-id="e6b6b-360">If the extensions aren't installed, select the **Add** button.</span></span>
1. <span data-ttu-id="e6b6b-361">Ze seznamu zvolte **ASP.NET jádrová rozšíření.**</span><span class="sxs-lookup"><span data-stu-id="e6b6b-361">Choose the **ASP.NET Core Extensions** from the list.</span></span>
1. <span data-ttu-id="e6b6b-362">Chcete-li přijmout zákonné podmínky, vyberte **možnost OK.**</span><span class="sxs-lookup"><span data-stu-id="e6b6b-362">Select **OK** to accept the legal terms.</span></span>
1. <span data-ttu-id="e6b6b-363">V okně **Přidat prodlužovací** okno vyberte **OK.**</span><span class="sxs-lookup"><span data-stu-id="e6b6b-363">Select **OK** on the **Add extension** blade.</span></span>
1. <span data-ttu-id="e6b6b-364">Informační zpráva o automaticky otevíraná okna označuje, kdy jsou rozšíření úspěšně nainstalována.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-364">An informational pop-up message indicates when the extensions are successfully installed.</span></span>

<span data-ttu-id="e6b6b-365">Pokud protokolování stdout není povoleno, postupujte takto:</span><span class="sxs-lookup"><span data-stu-id="e6b6b-365">If stdout logging isn't enabled, follow these steps:</span></span>

1. <span data-ttu-id="e6b6b-366">Na webu Azure Portal vyberte okno **Rozšířené nástroje** v oblasti NÁSTROJE **PRO VÝVOJ.**</span><span class="sxs-lookup"><span data-stu-id="e6b6b-366">In the Azure portal, select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="e6b6b-367">Vyberte tlačítko \*\*Přejít.&rarr; \*\*</span><span class="sxs-lookup"><span data-stu-id="e6b6b-367">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="e6b6b-368">Konzole Kudu se otevře v novém kartě prohlížeče nebo v okně.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-368">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="e6b6b-369">Pomocí navigačního panelu v horní části stránky otevřete **konzolu ladění** a vyberte **možnost CMD**.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-369">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="e6b6b-370">Otevřete složky na **webu** > cesty **wwwroot** a posuňte se dolů a odhalte soubor *web.config* v dolní části seznamu.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-370">Open the folders to the path **site** > **wwwroot** and scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="e6b6b-371">Klikněte na ikonu tužky vedle souboru *web.config.*</span><span class="sxs-lookup"><span data-stu-id="e6b6b-371">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="e6b6b-372">Nastavte **stdoutLogEnabled** na `true` cestu **stdoutLogFile** `\\?\%home%\LogFiles\stdout`a změňte ji na: .</span><span class="sxs-lookup"><span data-stu-id="e6b6b-372">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="e6b6b-373">Vyberte **Uložit,** chcete-li uložit aktualizovaný soubor *web.config.*</span><span class="sxs-lookup"><span data-stu-id="e6b6b-373">Select **Save** to save the updated *web.config* file.</span></span>

<span data-ttu-id="e6b6b-374">Postupujte k aktivaci diagnostického protokolování:</span><span class="sxs-lookup"><span data-stu-id="e6b6b-374">Proceed to activate diagnostic logging:</span></span>

1. <span data-ttu-id="e6b6b-375">Na webu Azure Portal vyberte okno **Protokoly diagnostiky.**</span><span class="sxs-lookup"><span data-stu-id="e6b6b-375">In the Azure portal, select the **Diagnostics logs** blade.</span></span>
1. <span data-ttu-id="e6b6b-376">Vyberte přepínač **Zapnuto** pro **protokolování aplikací (souborový systém)** a **Podrobné chybové zprávy**.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-376">Select the **On** switch for **Application Logging (Filesystem)** and **Detailed error messages**.</span></span> <span data-ttu-id="e6b6b-377">V horní části nože vyberte tlačítko **Uložit.**</span><span class="sxs-lookup"><span data-stu-id="e6b6b-377">Select the **Save** button at the top of the blade.</span></span>
1. <span data-ttu-id="e6b6b-378">Chcete-li zahrnout trasování neúspěšných požadavků, známé také jako protokolfreb (Failed Request Event Buffering), vyberte přepínač **Zapnuto** pro **trasování neúspěšných požadavků**.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-378">To include failed request tracing, also known as Failed Request Event Buffering (FREB) logging, select the **On** switch for **Failed request tracing**.</span></span>
1. <span data-ttu-id="e6b6b-379">Vyberte okno **protokolu datového proudu,** který je uveden okamžitě pod **protokoly protokoly diagnostiky** na portálu.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-379">Select the **Log stream** blade, which is listed immediately under the **Diagnostics logs** blade in the portal.</span></span>
1. <span data-ttu-id="e6b6b-380">Požádejte o to do aplikace.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-380">Make a request to the app.</span></span>
1. <span data-ttu-id="e6b6b-381">V rámci dat datového proudu protokolu je uvedena příčina chyby.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-381">Within the log stream data, the cause of the error is indicated.</span></span>

<span data-ttu-id="e6b6b-382">Nezapomeňte zakázat stdout protokolování po dokončení řešení potíží.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-382">Be sure to disable stdout logging when troubleshooting is complete.</span></span>

<span data-ttu-id="e6b6b-383">Zobrazení protokolů trasování neúspěšných požadavků (protokoly FREB):</span><span class="sxs-lookup"><span data-stu-id="e6b6b-383">To view the failed request tracing logs (FREB logs):</span></span>

1. <span data-ttu-id="e6b6b-384">Přejděte na okno **Diagnostikovat a vyřešit problémy** na webu Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-384">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="e6b6b-385">V yberte **Protokoly trasování neúspěšných požadavků** v oblasti **NÁSTROJE PODPORY** na postranním panelu.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-385">Select **Failed Request Tracing Logs** from the **SUPPORT TOOLS** area of the sidebar.</span></span>

<span data-ttu-id="e6b6b-386">Další informace [najdete v tématu Sledování neúspěšných požadavků v tématu Povolit diagnostiku pro webové aplikace v tématu Služby aplikací](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) a [v nejčastějších dotazech k výkonu aplikací pro webové aplikace v Azure: Jak zapnu trasování neúspěšných požadavků?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing)</span><span class="sxs-lookup"><span data-stu-id="e6b6b-386">See [Failed request traces section of the Enable diagnostics logging for web apps in Azure App Service topic](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) and the [Application performance FAQs for Web Apps in Azure: How do I turn on failed request tracing?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing) for more information.</span></span>

<span data-ttu-id="e6b6b-387">Další informace najdete [v tématu Povolení protokolování diagnostiky pro webové aplikace ve službě Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log).</span><span class="sxs-lookup"><span data-stu-id="e6b6b-387">For more information, see [Enable diagnostics logging for web apps in Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log).</span></span>

> [!WARNING]
> <span data-ttu-id="e6b6b-388">Selhání zakázání protokolu stdout může vést k selhání aplikace nebo serveru.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-388">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="e6b6b-389">Neexistuje žádné omezení na velikost souboru protokolu nebo počet vytvořených souborů protokolu.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-389">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="e6b6b-390">Pro rutinní protokolování v aplikaci ASP.NET Core použijte knihovnu protokolování, která omezuje velikost souboru protokolu a otáčí protokoly.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-390">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="e6b6b-391">Další informace naleznete [v tématu zprostředkovatelé protokolování třetích stran](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="e6b6b-391">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

## <a name="troubleshoot-on-iis"></a><span data-ttu-id="e6b6b-392">Odstraňování potíží ve službě IIS</span><span class="sxs-lookup"><span data-stu-id="e6b6b-392">Troubleshoot on IIS</span></span>

### <a name="application-event-log-iis"></a><span data-ttu-id="e6b6b-393">Protokol událostí aplikace (IIS)</span><span class="sxs-lookup"><span data-stu-id="e6b6b-393">Application Event Log (IIS)</span></span>

<span data-ttu-id="e6b6b-394">Přístup k protokolu událostí aplikace:</span><span class="sxs-lookup"><span data-stu-id="e6b6b-394">Access the Application Event Log:</span></span>

1. <span data-ttu-id="e6b6b-395">Otevřete nabídku Start, vyhledejte *Prohlížeč událostí*a vyberte aplikaci **Prohlížeč událostí.**</span><span class="sxs-lookup"><span data-stu-id="e6b6b-395">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="e6b6b-396">V **Prohlížeči událostí**otevřete uzel **Protokoly systému Windows.**</span><span class="sxs-lookup"><span data-stu-id="e6b6b-396">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="e6b6b-397">Výběrem **možnosti Aplikace** otevřete protokol událostí aplikace.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-397">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="e6b6b-398">Vyhledejte chyby spojené s aplikací se selháním.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-398">Search for errors associated with the failing app.</span></span> <span data-ttu-id="e6b6b-399">Chyby mají hodnotu *modulu AspNetCore služby IIS* nebo *modulu AspNetCore služby IIS Express* ve sloupci *Zdroj.*</span><span class="sxs-lookup"><span data-stu-id="e6b6b-399">Errors have a value of *IIS AspNetCore Module* or *IIS Express AspNetCore Module* in the *Source* column.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="e6b6b-400">Spuštění aplikace na příkazovém řádku</span><span class="sxs-lookup"><span data-stu-id="e6b6b-400">Run the app at a command prompt</span></span>

<span data-ttu-id="e6b6b-401">Mnoho chyb při spuštění nevytváří užitečné informace v protokolu událostí aplikace.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-401">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="e6b6b-402">Příčinu některých chyb můžete najít spuštěním aplikace na příkazovém řádku v hostitelském systému.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-402">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span>

#### <a name="framework-dependent-deployment"></a><span data-ttu-id="e6b6b-403">Nasazení závislé na rámci</span><span class="sxs-lookup"><span data-stu-id="e6b6b-403">Framework-dependent deployment</span></span>

<span data-ttu-id="e6b6b-404">Pokud je aplikace [nasazení závislé na rámci](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span><span class="sxs-lookup"><span data-stu-id="e6b6b-404">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

1. <span data-ttu-id="e6b6b-405">Na příkazovém řádku přejděte do složky nasazení a spusťte aplikaci spuštěním sestavení aplikace pomocí *dotnet.exe*.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-405">At a command prompt, navigate to the deployment folder and run the app by executing the app's assembly with *dotnet.exe*.</span></span> <span data-ttu-id="e6b6b-406">V následujícím příkazu nahraďte název sestavení \<aplikace assembly_name `dotnet .\<assembly_name>.dll`>: .</span><span class="sxs-lookup"><span data-stu-id="e6b6b-406">In the following command, substitute the name of the app's assembly for \<assembly_name>: `dotnet .\<assembly_name>.dll`.</span></span>
1. <span data-ttu-id="e6b6b-407">Výstup konzoly z aplikace, zobrazující případné chyby, je zapsán do okna konzoly.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-407">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="e6b6b-408">Pokud dojde k chybám při podání požadavku na aplikaci, podat žádost na hostitele a port, kde Kestrel naslouchá.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-408">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="e6b6b-409">Pomocí výchozího hostitele a příspěvku `http://localhost:5000/`požádejte o místo .</span><span class="sxs-lookup"><span data-stu-id="e6b6b-409">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="e6b6b-410">Pokud aplikace reaguje normálně na adrese koncového bodu Kestrel, problém je pravděpodobnější související s konfigurací hostování a méně pravděpodobné v rámci aplikace.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-410">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

#### <a name="self-contained-deployment"></a><span data-ttu-id="e6b6b-411">Samostatné nasazení</span><span class="sxs-lookup"><span data-stu-id="e6b6b-411">Self-contained deployment</span></span>

<span data-ttu-id="e6b6b-412">Pokud se jedná o [samostatné nasazení](/dotnet/core/deploying/#self-contained-deployments-scd)aplikace :</span><span class="sxs-lookup"><span data-stu-id="e6b6b-412">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

1. <span data-ttu-id="e6b6b-413">Na příkazovém řádku přejděte do složky nasazení a spusťte spustitelný soubor aplikace.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-413">At a command prompt, navigate to the deployment folder and run the app's executable.</span></span> <span data-ttu-id="e6b6b-414">V následujícím příkazu nahraďte název sestavení \<aplikace assembly_name `<assembly_name>.exe`>: .</span><span class="sxs-lookup"><span data-stu-id="e6b6b-414">In the following command, substitute the name of the app's assembly for \<assembly_name>: `<assembly_name>.exe`.</span></span>
1. <span data-ttu-id="e6b6b-415">Výstup konzoly z aplikace, zobrazující případné chyby, je zapsán do okna konzoly.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-415">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="e6b6b-416">Pokud dojde k chybám při podání požadavku na aplikaci, podat žádost na hostitele a port, kde Kestrel naslouchá.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-416">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="e6b6b-417">Pomocí výchozího hostitele a příspěvku `http://localhost:5000/`požádejte o místo .</span><span class="sxs-lookup"><span data-stu-id="e6b6b-417">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="e6b6b-418">Pokud aplikace reaguje normálně na adrese koncového bodu Kestrel, problém je pravděpodobnější související s konfigurací hostování a méně pravděpodobné v rámci aplikace.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-418">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

### <a name="aspnet-core-module-stdout-log-iis"></a><span data-ttu-id="e6b6b-419">ASP.NET protokol stdout modulu (IIS)</span><span class="sxs-lookup"><span data-stu-id="e6b6b-419">ASP.NET Core Module stdout log (IIS)</span></span>

<span data-ttu-id="e6b6b-420">Povolení a zobrazení protokolů stdout:</span><span class="sxs-lookup"><span data-stu-id="e6b6b-420">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="e6b6b-421">Přejděte do složky nasazení webu v hostitelském systému.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-421">Navigate to the site's deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="e6b6b-422">Pokud složka *protokolů* není k dispozici, vytvořte složku.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-422">If the *logs* folder isn't present, create the folder.</span></span> <span data-ttu-id="e6b6b-423">Pokyny k povolení msbuild vytvořit složku *protokolů* v nasazení automaticky, naleznete v tématu [directory struktura.](xref:host-and-deploy/directory-structure)</span><span class="sxs-lookup"><span data-stu-id="e6b6b-423">For instructions on how to enable MSBuild to create the *logs* folder in the deployment automatically, see the [Directory structure](xref:host-and-deploy/directory-structure) topic.</span></span>
1. <span data-ttu-id="e6b6b-424">Upravte soubor *web.config.*</span><span class="sxs-lookup"><span data-stu-id="e6b6b-424">Edit the *web.config* file.</span></span> <span data-ttu-id="e6b6b-425">Nastavte **stdoutLogEnabled** a `true` změňte cestu **stdoutLogFile** tak, `.\logs\stdout`aby přecózovala na složku *protokolů* (například ).</span><span class="sxs-lookup"><span data-stu-id="e6b6b-425">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to point to the *logs* folder (for example, `.\logs\stdout`).</span></span> <span data-ttu-id="e6b6b-426">`stdout`v cestě je předpona názvu souboru protokolu.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-426">`stdout` in the path is the log file name prefix.</span></span> <span data-ttu-id="e6b6b-427">Časové razítko, ID procesu a přípona souboru jsou přidány automaticky při vytvoření protokolu.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-427">A timestamp, process id, and file extension are added automatically when the log is created.</span></span> <span data-ttu-id="e6b6b-428">Jako `stdout` předpona názvu souboru je typický soubor protokolu pojmenován *stdout_20180205184032_5412.log*.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-428">Using `stdout` as the file name prefix, a typical log file is named *stdout_20180205184032_5412.log*.</span></span>
1. <span data-ttu-id="e6b6b-429">Ujistěte se, že identita fondu aplikací má oprávnění k zápisu do složky *protokolů.*</span><span class="sxs-lookup"><span data-stu-id="e6b6b-429">Ensure your application pool's identity has write permissions to the *logs* folder.</span></span>
1. <span data-ttu-id="e6b6b-430">Uložte aktualizovaný soubor *web.config.*</span><span class="sxs-lookup"><span data-stu-id="e6b6b-430">Save the updated *web.config* file.</span></span>
1. <span data-ttu-id="e6b6b-431">Požádejte o to do aplikace.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-431">Make a request to the app.</span></span>
1. <span data-ttu-id="e6b6b-432">Přejděte do složky *protokolů.*</span><span class="sxs-lookup"><span data-stu-id="e6b6b-432">Navigate to the *logs* folder.</span></span> <span data-ttu-id="e6b6b-433">Vyhledejte a otevřete nejnovější protokol stdout.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-433">Find and open the most recent stdout log.</span></span>
1. <span data-ttu-id="e6b6b-434">Prostudujte si protokol pro chyby.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-434">Study the log for errors.</span></span>

<span data-ttu-id="e6b6b-435">Zakažte protokolování stdout po dokončení řešení potíží:</span><span class="sxs-lookup"><span data-stu-id="e6b6b-435">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="e6b6b-436">Upravte soubor *web.config.*</span><span class="sxs-lookup"><span data-stu-id="e6b6b-436">Edit the *web.config* file.</span></span>
1. <span data-ttu-id="e6b6b-437">Nastavte **stdoutLogEnabled** na `false`.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-437">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="e6b6b-438">Uložte soubor.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-438">Save the file.</span></span>

<span data-ttu-id="e6b6b-439">Další informace naleznete v tématu <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-439">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

> [!WARNING]
> <span data-ttu-id="e6b6b-440">Selhání zakázání protokolu stdout může vést k selhání aplikace nebo serveru.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-440">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="e6b6b-441">Neexistuje žádné omezení na velikost souboru protokolu nebo počet vytvořených souborů protokolu.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-441">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="e6b6b-442">Pro rutinní protokolování v aplikaci ASP.NET Core použijte knihovnu protokolování, která omezuje velikost souboru protokolu a otáčí protokoly.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-442">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="e6b6b-443">Další informace naleznete [v tématu zprostředkovatelé protokolování třetích stran](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="e6b6b-443">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="aspnet-core-module-debug-log-iis"></a><span data-ttu-id="e6b6b-444">protokol ladění základního modulu ASP.NET (IIS)</span><span class="sxs-lookup"><span data-stu-id="e6b6b-444">ASP.NET Core Module debug log (IIS)</span></span>

<span data-ttu-id="e6b6b-445">Přidejte do souboru *web.config* aplikace následující nastavení obslužné rutiny, která umožní ASP.NET protokol ladění základního modulu:</span><span class="sxs-lookup"><span data-stu-id="e6b6b-445">Add the following handler settings to the app's *web.config* file to enable ASP.NET Core Module debug log:</span></span>

```xml
<aspNetCore ...>
  <handlerSettings>
    <handlerSetting name="debugLevel" value="file" />
    <handlerSetting name="debugFile" value="c:\temp\ancm.log" />
  </handlerSettings>
</aspNetCore>
```

<span data-ttu-id="e6b6b-446">Zkontrolujte, zda existuje cesta zadaná pro protokol a zda má identita fondu aplikací oprávnění k zápisu do umístění.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-446">Confirm that the path specified for the log exists and that the app pool's identity has write permissions to the location.</span></span>

<span data-ttu-id="e6b6b-447">Další informace naleznete v tématu <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-447">For more information, see <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span></span>

### <a name="enable-the-developer-exception-page"></a><span data-ttu-id="e6b6b-448">Povolení stránky výjimky pro vývojáře</span><span class="sxs-lookup"><span data-stu-id="e6b6b-448">Enable the Developer Exception Page</span></span>

<span data-ttu-id="e6b6b-449">`ASPNETCORE_ENVIRONMENT` [Proměnnou prostředí lze přidat do web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) pro spuštění aplikace ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-449">The `ASPNETCORE_ENVIRONMENT` [environment variable can be added to web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) to run the app in the Development environment.</span></span> <span data-ttu-id="e6b6b-450">Pokud prostředí není přepsáno při spuštění `UseEnvironment` aplikace tvůrcem hostitele, nastavení proměnné prostředí umožňuje, aby se [stránka výjimky pro vývojáře](xref:fundamentals/error-handling) zobrazila při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-450">As long as the environment isn't overridden in app startup by `UseEnvironment` on the host builder, setting the environment variable allows the [Developer Exception Page](xref:fundamentals/error-handling) to appear when the app is run.</span></span>

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

<span data-ttu-id="e6b6b-451">Nastavení proměnné prostředí `ASPNETCORE_ENVIRONMENT` pro se doporučuje pouze pro použití na pracovních a testovacích serverech, které nejsou vystaveny Internetu.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-451">Setting the environment variable for `ASPNETCORE_ENVIRONMENT` is only recommended for use on staging and testing servers that aren't exposed to the Internet.</span></span> <span data-ttu-id="e6b6b-452">Po odstranění potíží odeberte proměnnou prostředí ze souboru *web.config.*</span><span class="sxs-lookup"><span data-stu-id="e6b6b-452">Remove the environment variable from the *web.config* file after troubleshooting.</span></span> <span data-ttu-id="e6b6b-453">Informace o nastavení proměnných prostředí v *souboru web.config*naleznete v [tématu podřízený prvek environmentVariables aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).</span><span class="sxs-lookup"><span data-stu-id="e6b6b-453">For information on setting environment variables in *web.config*, see [environmentVariables child element of aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).</span></span>

### <a name="obtain-data-from-an-app"></a><span data-ttu-id="e6b6b-454">Získání dat z aplikace</span><span class="sxs-lookup"><span data-stu-id="e6b6b-454">Obtain data from an app</span></span>

<span data-ttu-id="e6b6b-455">Pokud je aplikace schopná reagovat na požadavky, získejte požadavek, připojení a další data z aplikace pomocí terminálového inline middlewaru.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-455">If an app is capable of responding to requests, obtain request, connection, and additional data from the app using terminal inline middleware.</span></span> <span data-ttu-id="e6b6b-456">Další informace a ukázkový <xref:test/troubleshoot#obtain-data-from-an-app>kód naleznete v tématu .</span><span class="sxs-lookup"><span data-stu-id="e6b6b-456">For more information and sample code, see <xref:test/troubleshoot#obtain-data-from-an-app>.</span></span>

### <a name="slow-or-hanging-app-iis"></a><span data-ttu-id="e6b6b-457">Pomalá nebo závěsná aplikace (IIS)</span><span class="sxs-lookup"><span data-stu-id="e6b6b-457">Slow or hanging app (IIS)</span></span>

<span data-ttu-id="e6b6b-458">*Výpis stavu systému* je snímek paměti systému a může pomoci určit příčinu selhání aplikace, selhání při spuštění nebo pomalé aplikace.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-458">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="e6b6b-459">Aplikace dojde k chybě nebo narazí na výjimku</span><span class="sxs-lookup"><span data-stu-id="e6b6b-459">App crashes or encounters an exception</span></span>

<span data-ttu-id="e6b6b-460">Získání a analýza výpisu z [programu Zasílání zpráv o chybách systému Windows (WER)](/windows/desktop/wer/windows-error-reporting):</span><span class="sxs-lookup"><span data-stu-id="e6b6b-460">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="e6b6b-461">Vytvořte složku, ve které `c:\dumps`budou uloženy soubory s výpisem stavu systému .</span><span class="sxs-lookup"><span data-stu-id="e6b6b-461">Create a folder to hold crash dump files at `c:\dumps`.</span></span> <span data-ttu-id="e6b6b-462">Fond aplikací musí mít přístup pro zápis do složky.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-462">The app pool must have write access to the folder.</span></span>
1. <span data-ttu-id="e6b6b-463">Spusťte [skript EnableDumps PowerShell](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1):</span><span class="sxs-lookup"><span data-stu-id="e6b6b-463">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1):</span></span>
   * <span data-ttu-id="e6b6b-464">Pokud aplikace používá [model hostování v procesu](xref:host-and-deploy/iis/index#in-process-hosting-model), spusťte skript pro *w3wp.exe*:</span><span class="sxs-lookup"><span data-stu-id="e6b6b-464">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\EnableDumps w3wp.exe c:\dumps
     ```

   * <span data-ttu-id="e6b6b-465">Pokud aplikace používá [mimoprocesový model hostování](xref:host-and-deploy/iis/index#out-of-process-hosting-model), spusťte skript pro *dotnet.exe*:</span><span class="sxs-lookup"><span data-stu-id="e6b6b-465">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\EnableDumps dotnet.exe c:\dumps
     ```

1. <span data-ttu-id="e6b6b-466">Spusťte aplikaci za podmínek, které způsobují selhání dojít.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-466">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="e6b6b-467">Po selhání spusťte [skript DisableDumps PowerShell](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1):</span><span class="sxs-lookup"><span data-stu-id="e6b6b-467">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1):</span></span>
   * <span data-ttu-id="e6b6b-468">Pokud aplikace používá [model hostování v procesu](xref:host-and-deploy/iis/index#in-process-hosting-model), spusťte skript pro *w3wp.exe*:</span><span class="sxs-lookup"><span data-stu-id="e6b6b-468">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\DisableDumps w3wp.exe
     ```

   * <span data-ttu-id="e6b6b-469">Pokud aplikace používá [mimoprocesový model hostování](xref:host-and-deploy/iis/index#out-of-process-hosting-model), spusťte skript pro *dotnet.exe*:</span><span class="sxs-lookup"><span data-stu-id="e6b6b-469">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\DisableDumps dotnet.exe
     ```

<span data-ttu-id="e6b6b-470">Po zhroucení aplikace a výpis kolekce je kompletní, aplikace je povoleno ukončit normálně.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-470">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="e6b6b-471">Skript PowerShell udává WER tak, aby shromažďoval až pět výpisů v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-471">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="e6b6b-472">Výpisy stavu systému může zabírají velké množství místa na disku (až několik gigabajtů každý).</span><span class="sxs-lookup"><span data-stu-id="e6b6b-472">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="e6b6b-473">Aplikace přestane reagovat, selže při spuštění nebo běží normálně</span><span class="sxs-lookup"><span data-stu-id="e6b6b-473">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="e6b6b-474">Když aplikace *přestane reagovat* (přestane reagovat, ale nezhroutí se), selže při spuštění nebo běží normálně, najdete v [tématu Soubory výpisu uživatelského režimu: Výběr nejvhodnějšího nástroje](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) pro výběr vhodného nástroje pro vytvoření výpisu.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-474">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="e6b6b-475">Analyzovat výpis</span><span class="sxs-lookup"><span data-stu-id="e6b6b-475">Analyze the dump</span></span>

<span data-ttu-id="e6b6b-476">Výpis lze analyzovat pomocí několika přístupů.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-476">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="e6b6b-477">Další informace naleznete [v tématu Analýza souboru s výpisem stavu uživatelského režimu](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span><span class="sxs-lookup"><span data-stu-id="e6b6b-477">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="clear-package-caches"></a><span data-ttu-id="e6b6b-478">Vymazat mezipaměti balíčků</span><span class="sxs-lookup"><span data-stu-id="e6b6b-478">Clear package caches</span></span>

<span data-ttu-id="e6b6b-479">Funkční aplikace může selhat ihned po upgradu sady .NET Core SDK ve vývojovém počítači nebo změně verze balíčku v rámci aplikace.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-479">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="e6b6b-480">V některých případech nesouvislé balíčky může přerušit aplikaci při provádění hlavních upgradů.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-480">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="e6b6b-481">Většinu těchto problémů lze opravit podle následujících pokynů:</span><span class="sxs-lookup"><span data-stu-id="e6b6b-481">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="e6b6b-482">Odstraňte složky *bin* a *obj.*</span><span class="sxs-lookup"><span data-stu-id="e6b6b-482">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="e6b6b-483">Vymazat mezipaměti balíčku spuštěním [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) z příkazového prostředí.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-483">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="e6b6b-484">Vymazání mezipaměti balíčků lze také provést pomocí nástroje [nuget.exe](https://www.nuget.org/downloads) a spuštěnípříkazu `nuget locals all -clear`.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-484">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="e6b6b-485">*nuget.exe* není přibalená instalace s operačním systémem Windows desktop a musí být získány odděleně od [webu NuGet](https://www.nuget.org/downloads).</span><span class="sxs-lookup"><span data-stu-id="e6b6b-485">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="e6b6b-486">Obnovte a znovu vytvořte projekt.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-486">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="e6b6b-487">Před opětovnou nasazením aplikace odstraňte všechny soubory ve složce nasazení na serveru.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-487">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e6b6b-488">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="e6b6b-488">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:fundamentals/error-handling>
* <xref:host-and-deploy/aspnet-core-module>

### <a name="azure-documentation"></a><span data-ttu-id="e6b6b-489">Dokumentace k Azure</span><span class="sxs-lookup"><span data-stu-id="e6b6b-489">Azure documentation</span></span>

* [<span data-ttu-id="e6b6b-490">Application Insights pro ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e6b6b-490">Application Insights for ASP.NET Core</span></span>](/azure/application-insights/app-insights-asp-net-core)
* [<span data-ttu-id="e6b6b-491">Část Řešení potíží s webovou aplikací ve službě Azure App Service ve vzdáleném ladění webových aplikací pomocí Visual Studia</span><span class="sxs-lookup"><span data-stu-id="e6b6b-491">Remote debugging web apps section of Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug)
* [<span data-ttu-id="e6b6b-492">Přehled diagnostiky služby Azure App Service</span><span class="sxs-lookup"><span data-stu-id="e6b6b-492">Azure App Service diagnostics overview</span></span>](/azure/app-service/app-service-diagnostics)
* [<span data-ttu-id="e6b6b-493">Postup: Monitorování aplikací ve službě Azure App Service</span><span class="sxs-lookup"><span data-stu-id="e6b6b-493">How to: Monitor Apps in Azure App Service</span></span>](/azure/app-service/web-sites-monitor)
* [<span data-ttu-id="e6b6b-494">Poradce při potížích s webovou aplikací ve službě Azure App Service pomocí Visual Studia</span><span class="sxs-lookup"><span data-stu-id="e6b6b-494">Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [<span data-ttu-id="e6b6b-495">Poradce při potížích s chybami HTTP "502 chybná brána" a "503 služba není k dispozici" ve svých webových aplikacích Azure</span><span class="sxs-lookup"><span data-stu-id="e6b6b-495">Troubleshoot HTTP errors of "502 bad gateway" and "503 service unavailable" in your Azure web apps</span></span>](/azure/app-service/app-service-web-troubleshoot-http-502-http-503)
* [<span data-ttu-id="e6b6b-496">Řešení potíží s výkonem pomalých webových aplikací ve službě Azure App Service</span><span class="sxs-lookup"><span data-stu-id="e6b6b-496">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="e6b6b-497">Časté otázky k výkonu aplikací pro webové aplikace v Azure</span><span class="sxs-lookup"><span data-stu-id="e6b6b-497">Application performance FAQs for Web Apps in Azure</span></span>](/azure/app-service/app-service-web-availability-performance-application-issues-faq)
* [<span data-ttu-id="e6b6b-498">Azure Web App izolovaného prostoru (omezení spuštění služby App Service)</span><span class="sxs-lookup"><span data-stu-id="e6b6b-498">Azure Web App sandbox (App Service runtime execution limitations)</span></span>](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)
* [<span data-ttu-id="e6b6b-499">Azure Friday: Azure App Service Diagnostic and Troubleshooting Experience (12minutové video)</span><span class="sxs-lookup"><span data-stu-id="e6b6b-499">Azure Friday: Azure App Service Diagnostic and Troubleshooting Experience (12-minute video)</span></span>](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)

### <a name="visual-studio-documentation"></a><span data-ttu-id="e6b6b-500">Dokumentace sady Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e6b6b-500">Visual Studio documentation</span></span>

* [<span data-ttu-id="e6b6b-501">Vzdálené ladění ASP.NET jádro ve službě IIS v Azure ve Visual Studiu 2017</span><span class="sxs-lookup"><span data-stu-id="e6b6b-501">Remote Debug ASP.NET Core on IIS in Azure in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-azure)
* [<span data-ttu-id="e6b6b-502">Vzdálené ladění ASP.NET jádra ve vzdáleném počítači služby IIS v sadě Visual Studio 2017</span><span class="sxs-lookup"><span data-stu-id="e6b6b-502">Remote Debug ASP.NET Core on a Remote IIS Computer in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-aspnet-on-a-remote-iis-computer)
* [<span data-ttu-id="e6b6b-503">Informace o ladění pomocí sady Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e6b6b-503">Learn to debug using Visual Studio</span></span>](/visualstudio/debugger/getting-started-with-the-debugger)

### <a name="visual-studio-code-documentation"></a><span data-ttu-id="e6b6b-504">Dokumentace kódu visual studia</span><span class="sxs-lookup"><span data-stu-id="e6b6b-504">Visual Studio Code documentation</span></span>

* [<span data-ttu-id="e6b6b-505">Ladění pomocí kódu sady Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e6b6b-505">Debugging with Visual Studio Code</span></span>](https://code.visualstudio.com/docs/editor/debugging)

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="e6b6b-506">Tento článek obsahuje informace o běžných chybách při spuštění aplikace a pokyny, jak diagnostikovat chyby při nasazení aplikace do služby Azure App Service nebo IIS:</span><span class="sxs-lookup"><span data-stu-id="e6b6b-506">This article provides information on common app startup errors and instructions on how to diagnose errors when an app is deployed to Azure App Service or IIS:</span></span>

[<span data-ttu-id="e6b6b-507">Chyby při spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="e6b6b-507">App startup errors</span></span>](#app-startup-errors)  
<span data-ttu-id="e6b6b-508">Vysvětluje běžné scénáře stavového kódu HTTP při spuštění.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-508">Explains common startup HTTP status code scenarios.</span></span>

[<span data-ttu-id="e6b6b-509">Poradce při potížích se službou Azure App Service</span><span class="sxs-lookup"><span data-stu-id="e6b6b-509">Troubleshoot on Azure App Service</span></span>](#troubleshoot-on-azure-app-service)  
<span data-ttu-id="e6b6b-510">Poskytuje rady pro řešení potíží pro aplikace nasazené do služby Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-510">Provides troubleshooting advice for apps deployed to Azure App Service.</span></span>

[<span data-ttu-id="e6b6b-511">Odstraňování potíží ve službě IIS</span><span class="sxs-lookup"><span data-stu-id="e6b6b-511">Troubleshoot on IIS</span></span>](#troubleshoot-on-iis)  
<span data-ttu-id="e6b6b-512">Poskytuje rady pro řešení potíží s aplikacemi nasazenými do služby IIS nebo spuštěných místně ve službě IIS Express.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-512">Provides troubleshooting advice for apps deployed to IIS or running on IIS Express locally.</span></span> <span data-ttu-id="e6b6b-513">Pokyny platí pro nasazení plochy systému Windows Server i Windows.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-513">The guidance applies to both Windows Server and Windows desktop deployments.</span></span>

[<span data-ttu-id="e6b6b-514">Vymazat mezipaměti balíčků</span><span class="sxs-lookup"><span data-stu-id="e6b6b-514">Clear package caches</span></span>](#clear-package-caches)  
<span data-ttu-id="e6b6b-515">Vysvětluje, co dělat, když nesouvislé balíčky přerušit aplikaci při provádění hlavních upgradů nebo změny verze balíčku.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-515">Explains what to do when incoherent packages break an app when performing major upgrades or changing package versions.</span></span>

[<span data-ttu-id="e6b6b-516">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="e6b6b-516">Additional resources</span></span>](#additional-resources)  
<span data-ttu-id="e6b6b-517">Obsahuje další témata pro řešení potíží.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-517">Lists additional troubleshooting topics.</span></span>

## <a name="app-startup-errors"></a><span data-ttu-id="e6b6b-518">Chyby při spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="e6b6b-518">App startup errors</span></span>

<span data-ttu-id="e6b6b-519">V sadě Visual Studio, ASP.NET projektu Core výchozí [iis express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hostování během ladění.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-519">In Visual Studio, an ASP.NET Core project defaults to [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hosting during debugging.</span></span> <span data-ttu-id="e6b6b-520">A *502.5 - Selhání procesu* nebo *500.30 - Start Selhání,* ke kterému dochází při ladění místně lze diagnostikovat pomocí rady v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-520">A *502.5 - Process Failure* or a *500.30 - Start Failure* that occurs when debugging locally can be diagnosed using the advice in this topic.</span></span>

### <a name="40314-forbidden"></a><span data-ttu-id="e6b6b-521">403.14 Zakázáno</span><span class="sxs-lookup"><span data-stu-id="e6b6b-521">403.14 Forbidden</span></span>

<span data-ttu-id="e6b6b-522">Aplikace se nespustí.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-522">The app fails to start.</span></span> <span data-ttu-id="e6b6b-523">Je zaznamenána následující chyba:</span><span class="sxs-lookup"><span data-stu-id="e6b6b-523">The following error is logged:</span></span>

```
The Web server is configured to not list the contents of this directory.
```

<span data-ttu-id="e6b6b-524">Chyba je obvykle způsobena přerušeným nasazením v hostitelském systému, který zahrnuje některý z následujících scénářů:</span><span class="sxs-lookup"><span data-stu-id="e6b6b-524">The error is usually caused by a broken deployment on the hosting system, which includes any of the following scenarios:</span></span>

* <span data-ttu-id="e6b6b-525">Aplikace je nasazena do nesprávné složky v hostitelském systému.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-525">The app is deployed to the wrong folder on the hosting system.</span></span>
* <span data-ttu-id="e6b6b-526">Procesu nasazení se nepodařilo přesunout všechny soubory a složky aplikace do složky nasazení v hostitelském systému.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-526">The deployment process failed to move all of the app's files and folders to the deployment folder on the hosting system.</span></span>
* <span data-ttu-id="e6b6b-527">Soubor *web.config* chybí v nasazení nebo je obsah souboru *web.config* poškozen.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-527">The *web.config* file is missing from the deployment, or the *web.config* file contents are malformed.</span></span>

<span data-ttu-id="e6b6b-528">Proveďte následující kroky:</span><span class="sxs-lookup"><span data-stu-id="e6b6b-528">Perform the following steps:</span></span>

1. <span data-ttu-id="e6b6b-529">Odstraňte všechny soubory a složky ze složky nasazení v hostitelském systému.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-529">Delete all of the files and folders from the deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="e6b6b-530">Znovu nasadit obsah složky *publikování* aplikace do hostitelského systému pomocí normální metody nasazení, jako je Visual Studio, PowerShell nebo ruční nasazení:</span><span class="sxs-lookup"><span data-stu-id="e6b6b-530">Redeploy the contents of the app's *publish* folder to the hosting system using your normal method of deployment, such as Visual Studio, PowerShell, or manual deployment:</span></span>
   * <span data-ttu-id="e6b6b-531">Zkontrolujte, zda je soubor *web.config* v nasazení přítomen a zda je jeho obsah správný.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-531">Confirm that the *web.config* file is present in the deployment and that its contents are correct.</span></span>
   * <span data-ttu-id="e6b6b-532">Při hostování ve službě Azure App Service zkontrolujte, že se aplikace nasadí do `D:\home\site\wwwroot` složky.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-532">When hosting on Azure App Service, confirm that the app is deployed to the `D:\home\site\wwwroot` folder.</span></span>
   * <span data-ttu-id="e6b6b-533">Pokud je aplikace hostována službou IIS, zkontrolujte, zda je aplikace nasazena do **fyzické cesty** služby IIS zobrazené v **základním nastavení** **správce služby IIS**.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-533">When the app is hosted by IIS, confirm that the app is deployed to the IIS **Physical path** shown in **IIS Manager**'s **Basic Settings**.</span></span>
1. <span data-ttu-id="e6b6b-534">Zkontrolujte, zda jsou všechny soubory a složky aplikace nasazeny porovnáním nasazení v hostitelském systému s obsahem složky *publikování* projektu.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-534">Confirm that all of the app's files and folders are deployed by comparing the deployment on the hosting system to the contents of the project's *publish* folder.</span></span>

<span data-ttu-id="e6b6b-535">Další informace o rozložení publikované aplikace ASP.NET <xref:host-and-deploy/directory-structure>Core najdete v tématu .</span><span class="sxs-lookup"><span data-stu-id="e6b6b-535">For more information on the layout of a published ASP.NET Core app, see <xref:host-and-deploy/directory-structure>.</span></span> <span data-ttu-id="e6b6b-536">Další informace o souboru *web.config* naleznete v tématu <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-536">For more information on the *web.config* file, see <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>.</span></span>

### <a name="500-internal-server-error"></a><span data-ttu-id="e6b6b-537">500 – Interní chyba serveru</span><span class="sxs-lookup"><span data-stu-id="e6b6b-537">500 Internal Server Error</span></span>

<span data-ttu-id="e6b6b-538">Aplikace se spustí, ale chyba zabrání serveru v plnění požadavku.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-538">The app starts, but an error prevents the server from fulfilling the request.</span></span>

<span data-ttu-id="e6b6b-539">K této chybě dochází v kódu aplikace při spuštění nebo při vytváření odpovědi.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-539">This error occurs within the app's code during startup or while creating a response.</span></span> <span data-ttu-id="e6b6b-540">Odpověď může obsahovat žádný obsah nebo odpověď může zobrazit jako *500 Vnitřní chyba serveru* v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-540">The response may contain no content, or the response may appear as a *500 Internal Server Error* in the browser.</span></span> <span data-ttu-id="e6b6b-541">Protokol událostí aplikace obvykle uvádí, že aplikace byla spuštěna normálně.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-541">The Application Event Log usually states that the app started normally.</span></span> <span data-ttu-id="e6b6b-542">Z pohledu serveru je to správné.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-542">From the server's perspective, that's correct.</span></span> <span data-ttu-id="e6b6b-543">Aplikace se spustila, ale nemůže generovat platnou odpověď.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-543">The app did start, but it can't generate a valid response.</span></span> <span data-ttu-id="e6b6b-544">Spusťte aplikaci na příkazovém řádku na serveru nebo povolte protokol stdout ASP.NET Core Module k řešení problému.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-544">Run the app at a command prompt on the server or enable the ASP.NET Core Module stdout log to troubleshoot the problem.</span></span>

### <a name="5000-in-process-handler-load-failure"></a><span data-ttu-id="e6b6b-545">Selhání zatížení obslužné rutiny procesu 500.0</span><span class="sxs-lookup"><span data-stu-id="e6b6b-545">500.0 In-Process Handler Load Failure</span></span>

<span data-ttu-id="e6b6b-546">Pracovní proces se nezdaří.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-546">The worker process fails.</span></span> <span data-ttu-id="e6b6b-547">Aplikace se nespustí.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-547">The app doesn't start.</span></span>

<span data-ttu-id="e6b6b-548">Základní [modul ASP.NET](xref:host-and-deploy/aspnet-core-module) nenalezl clr jádra .NET a našel obslužnou rutinu požadavku v procesu (*aspnetcorev2_inprocess.dll*).</span><span class="sxs-lookup"><span data-stu-id="e6b6b-548">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) fails to find the .NET Core CLR and find the in-process request handler (*aspnetcorev2_inprocess.dll*).</span></span> <span data-ttu-id="e6b6b-549">Zkontrolujte, zda:</span><span class="sxs-lookup"><span data-stu-id="e6b6b-549">Check that:</span></span>

* <span data-ttu-id="e6b6b-550">Aplikace cílí buď na balíček [Microsoft.AspNetCore.Server.IIS](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IIS) NuGet, nebo na [metabalíček Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="e6b6b-550">The app targets either the [Microsoft.AspNetCore.Server.IIS](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IIS) NuGet package or the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>
* <span data-ttu-id="e6b6b-551">Verze sdíleného rozhraní ASP.NET Core, na kterou je aplikace nainstalovaná v cílovém počítači.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-551">The version of the ASP.NET Core shared framework that the app targets is installed on the target machine.</span></span>

### <a name="5000-out-of-process-handler-load-failure"></a><span data-ttu-id="e6b6b-552">500.0 Selhání zatížení obslužné rutiny mimo proces</span><span class="sxs-lookup"><span data-stu-id="e6b6b-552">500.0 Out-Of-Process Handler Load Failure</span></span>

<span data-ttu-id="e6b6b-553">Pracovní proces se nezdaří.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-553">The worker process fails.</span></span> <span data-ttu-id="e6b6b-554">Aplikace se nespustí.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-554">The app doesn't start.</span></span>

<span data-ttu-id="e6b6b-555">[ASP.NET core modul](xref:host-and-deploy/aspnet-core-module) unese nalezení obslužné rutiny požadavků mimo proces hostování.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-555">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) fails to find the out-of-process hosting request handler.</span></span> <span data-ttu-id="e6b6b-556">Ujistěte se, *že aspnetcorev2_outofprocess.dll* je k dispozici v podsložce vedle *aspnetcorev2.dll*.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-556">Make sure the *aspnetcorev2_outofprocess.dll* is present in a subfolder next to *aspnetcorev2.dll*.</span></span>

### <a name="5025-process-failure"></a><span data-ttu-id="e6b6b-557">502.5 – Selhání procesu</span><span class="sxs-lookup"><span data-stu-id="e6b6b-557">502.5 Process Failure</span></span>

<span data-ttu-id="e6b6b-558">Pracovní proces se nezdaří.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-558">The worker process fails.</span></span> <span data-ttu-id="e6b6b-559">Aplikace se nespustí.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-559">The app doesn't start.</span></span>

<span data-ttu-id="e6b6b-560">Základní [modul ASP.NET](xref:host-and-deploy/aspnet-core-module) se pokusí spustit pracovní proces, ale nepodaří se jej spustit.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-560">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the worker process but it fails to start.</span></span> <span data-ttu-id="e6b6b-561">Příčinu selhání spuštění procesu lze obvykle určit z položek v protokolu událostí aplikace a ASP.NET protokolu stdout základního modulu.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-561">The cause of a process startup failure can usually be determined from entries in the Application Event Log and the ASP.NET Core Module stdout log.</span></span>

<span data-ttu-id="e6b6b-562">Obvyklá podmínka selhání je, že aplikace je nesprávně nakonfigurovaná kvůli cílení na verzi sdíleného rozhraní ASP.NET Core, která není k dispozici.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-562">A common failure condition is the app is misconfigured due to targeting a version of the ASP.NET Core shared framework that isn't present.</span></span> <span data-ttu-id="e6b6b-563">Zkontrolujte, které verze ASP.NET sdíleného rozhraní Core jsou nainstalovány v cílovém počítači.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-563">Check which versions of the ASP.NET Core shared framework are installed on the target machine.</span></span> <span data-ttu-id="e6b6b-564">Sdílená *architektura* je sada sestavení (*soubory DLL),* které jsou nainstalovány v počítači a odkazovány metabalíčkem, například `Microsoft.AspNetCore.App`.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-564">The *shared framework* is the set of assemblies (*.dll* files) that are installed on the machine and referenced by a metapackage such as `Microsoft.AspNetCore.App`.</span></span> <span data-ttu-id="e6b6b-565">Odkaz na metabalíček může určit minimální požadovanou verzi.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-565">The metapackage reference can specify a minimum required version.</span></span> <span data-ttu-id="e6b6b-566">Další informace naleznete [v tématu sdílený rámec](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span><span class="sxs-lookup"><span data-stu-id="e6b6b-566">For more information, see [The shared framework](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span></span>

<span data-ttu-id="e6b6b-567">Chybová stránka *502.5 Process Failure* je vrácena, když selhání konfigurace hostitele nebo aplikace způsobí selhání pracovního procesu:</span><span class="sxs-lookup"><span data-stu-id="e6b6b-567">The *502.5 Process Failure* error page is returned when a hosting or app misconfiguration causes the worker process to fail:</span></span>

### <a name="failed-to-start-application-errorcode-0x800700c1"></a><span data-ttu-id="e6b6b-568">Spuštění aplikace se nezdařilo (ErrorCode '0x800700c1')</span><span class="sxs-lookup"><span data-stu-id="e6b6b-568">Failed to start application (ErrorCode '0x800700c1')</span></span>

```
EventID: 1010
Source: IIS AspNetCore Module V2
Failed to start application '/LM/W3SVC/6/ROOT/', ErrorCode '0x800700c1'.
```

<span data-ttu-id="e6b6b-569">Spuštění aplikace se nezdařilo, protože nebylo možné načíst sestavení aplikace (*DLL*).</span><span class="sxs-lookup"><span data-stu-id="e6b6b-569">The app failed to start because the app's assembly (*.dll*) couldn't be loaded.</span></span>

<span data-ttu-id="e6b6b-570">K této chybě dochází, pokud je neodpovídající bitness mezi publikovanou aplikací a w3wp/iisexpress procesu.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-570">This error occurs when there's a bitness mismatch between the published app and the w3wp/iisexpress process.</span></span>

<span data-ttu-id="e6b6b-571">Zkontrolujte, zda je 32bitové nastavení fondu aplikací správné:</span><span class="sxs-lookup"><span data-stu-id="e6b6b-571">Confirm that the app pool's 32-bit setting is correct:</span></span>

1. <span data-ttu-id="e6b6b-572">Vyberte fond aplikací ve **fondech aplikací**Správce služby IIS .</span><span class="sxs-lookup"><span data-stu-id="e6b6b-572">Select the app pool in IIS Manager's **Application Pools**.</span></span>
1. <span data-ttu-id="e6b6b-573">V části Upravit fond aplikací v panelu **Akce** vyberte **Upřesnit nastavení** v části Upravit **fond aplikací.**</span><span class="sxs-lookup"><span data-stu-id="e6b6b-573">Select **Advanced Settings** under **Edit Application Pool** in the **Actions** panel.</span></span>
1. <span data-ttu-id="e6b6b-574">Nastavit **povolit 32bitové aplikace**:</span><span class="sxs-lookup"><span data-stu-id="e6b6b-574">Set **Enable 32-Bit Applications**:</span></span>
   * <span data-ttu-id="e6b6b-575">Pokud nasazujete 32bitovou (x86) `True`aplikaci, nastavte hodnotu na .</span><span class="sxs-lookup"><span data-stu-id="e6b6b-575">If deploying a 32-bit (x86) app, set the value to `True`.</span></span>
   * <span data-ttu-id="e6b6b-576">Pokud nasazujete 64bitovou (x64) `False`aplikaci, nastavte hodnotu na .</span><span class="sxs-lookup"><span data-stu-id="e6b6b-576">If deploying a 64-bit (x64) app, set the value to `False`.</span></span>

<span data-ttu-id="e6b6b-577">Zkontrolujte, zda v souboru `<Platform>` projektu není konflikt mezi vlastností MSBuild a publikovanou bitness aplikace.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-577">Confirm that there isn't a conflict between a `<Platform>` MSBuild property in the project file and the published bitness of the app.</span></span>

### <a name="connection-reset"></a><span data-ttu-id="e6b6b-578">Resetování připojení</span><span class="sxs-lookup"><span data-stu-id="e6b6b-578">Connection reset</span></span>

<span data-ttu-id="e6b6b-579">Pokud dojde k chybě po odeslání záhlaví, je příliš pozdě na to, aby server odeslal **chybu interního serveru 500,** když dojde k chybě.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-579">If an error occurs after the headers are sent, it's too late for the server to send a **500 Internal Server Error** when an error occurs.</span></span> <span data-ttu-id="e6b6b-580">K tomu často dochází, když dojde k chybě během serializace složitých objektů pro odpověď.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-580">This often happens when an error occurs during the serialization of complex objects for a response.</span></span> <span data-ttu-id="e6b6b-581">Tento typ chyby se zobrazí jako chyba *obnovení připojení* v klientovi.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-581">This type of error appears as a *connection reset* error on the client.</span></span> <span data-ttu-id="e6b6b-582">[Protokolování aplikací](xref:fundamentals/logging/index) může pomoci při řešení těchto typů chyb.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-582">[Application logging](xref:fundamentals/logging/index) can help troubleshoot these types of errors.</span></span>

### <a name="default-startup-limits"></a><span data-ttu-id="e6b6b-583">Výchozí limity pro spuštění</span><span class="sxs-lookup"><span data-stu-id="e6b6b-583">Default startup limits</span></span>

<span data-ttu-id="e6b6b-584">[Základní modul ASP.NET](xref:host-and-deploy/aspnet-core-module) je nakonfigurován s výchozím *spouštěcím limitem* 120 sekund.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-584">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) is configured with a default *startupTimeLimit* of 120 seconds.</span></span> <span data-ttu-id="e6b6b-585">Pokud zůstane na výchozí hodnotu, aplikace může trvat až dvě minuty ke spuštění, než modul zaznamená selhání procesu.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-585">When left at the default value, an app may take up to two minutes to start before the module logs a process failure.</span></span> <span data-ttu-id="e6b6b-586">Informace o konfiguraci modulu naleznete v [tématu Atributy prvku aspNetCore](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span><span class="sxs-lookup"><span data-stu-id="e6b6b-586">For information on configuring the module, see [Attributes of the aspNetCore element](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span></span>

## <a name="troubleshoot-on-azure-app-service"></a><span data-ttu-id="e6b6b-587">Poradce při potížích se službou Azure App Service</span><span class="sxs-lookup"><span data-stu-id="e6b6b-587">Troubleshoot on Azure App Service</span></span>

[!INCLUDE [Azure App Service Preview Notice](~/includes/azure-apps-preview-notice.md)]

### <a name="application-event-log-azure-app-service"></a><span data-ttu-id="e6b6b-588">Protokol událostí aplikace (služba Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="e6b6b-588">Application Event Log (Azure App Service)</span></span>

<span data-ttu-id="e6b6b-589">Chcete-li získat přístup k protokolu událostí aplikace, použijte okno **Diagnostikovat a vyřešit problémy** na webu Azure Portal:</span><span class="sxs-lookup"><span data-stu-id="e6b6b-589">To access the Application Event Log, use the **Diagnose and solve problems** blade in the Azure portal:</span></span>

1. <span data-ttu-id="e6b6b-590">Na webu Azure Portal otevřete aplikaci ve **Službách aplikací**.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-590">In the Azure portal, open the app in **App Services**.</span></span>
1. <span data-ttu-id="e6b6b-591">Vyberte **Diagnostikovat a řešit problémy**.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-591">Select **Diagnose and solve problems**.</span></span>
1. <span data-ttu-id="e6b6b-592">Vyberte nadpis **Diagnostické nástroje.**</span><span class="sxs-lookup"><span data-stu-id="e6b6b-592">Select the **Diagnostic Tools** heading.</span></span>
1. <span data-ttu-id="e6b6b-593">V části **Nástroje podpory**vyberte tlačítko **Události aplikace.**</span><span class="sxs-lookup"><span data-stu-id="e6b6b-593">Under **Support Tools**, select the **Application Events** button.</span></span>
1. <span data-ttu-id="e6b6b-594">Prohlédněte si nejnovější chybu poskytovanou položkou *AspNetCoreModule* nebo *IIS AspNetCoreModule V2* ve sloupci **Zdroj.**</span><span class="sxs-lookup"><span data-stu-id="e6b6b-594">Examine the latest error provided by the *IIS AspNetCoreModule* or *IIS AspNetCoreModule V2* entry in the **Source** column.</span></span>

<span data-ttu-id="e6b6b-595">Alternativou k použití **funkce Diagnostika a řešení problémů** je prozkoumat soubor protokolu událostí aplikace přímo pomocí [kudu](https://github.com/projectkudu/kudu/wiki):</span><span class="sxs-lookup"><span data-stu-id="e6b6b-595">An alternative to using the **Diagnose and solve problems** blade is to examine the Application Event Log file directly using [Kudu](https://github.com/projectkudu/kudu/wiki):</span></span>

1. <span data-ttu-id="e6b6b-596">Otevřete **rozšířené nástroje** v oblasti **vývojových nástrojů.**</span><span class="sxs-lookup"><span data-stu-id="e6b6b-596">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="e6b6b-597">Vyberte tlačítko \*\*Přejít.&rarr; \*\*</span><span class="sxs-lookup"><span data-stu-id="e6b6b-597">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="e6b6b-598">Konzole Kudu se otevře v novém kartě prohlížeče nebo v okně.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-598">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="e6b6b-599">Pomocí navigačního panelu v horní části stránky otevřete **konzolu ladění** a vyberte **možnost CMD**.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-599">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="e6b6b-600">Otevřete složku **LogFiles.**</span><span class="sxs-lookup"><span data-stu-id="e6b6b-600">Open the **LogFiles** folder.</span></span>
1. <span data-ttu-id="e6b6b-601">Vyberte ikonu tužky vedle souboru *eventlog.xml.*</span><span class="sxs-lookup"><span data-stu-id="e6b6b-601">Select the pencil icon next to the *eventlog.xml* file.</span></span>
1. <span data-ttu-id="e6b6b-602">Prohlédněte si protokol.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-602">Examine the log.</span></span> <span data-ttu-id="e6b6b-603">Přejděte do dolní části protokolu zobrazíte nejnovější události.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-603">Scroll to the bottom of the log to see the most recent events.</span></span>

### <a name="run-the-app-in-the-kudu-console"></a><span data-ttu-id="e6b6b-604">Spuštění aplikace v konzoli Kudu</span><span class="sxs-lookup"><span data-stu-id="e6b6b-604">Run the app in the Kudu console</span></span>

<span data-ttu-id="e6b6b-605">Mnoho chyb při spuštění nevytváří užitečné informace v protokolu událostí aplikace.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-605">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="e6b6b-606">Aplikaci můžete spustit v konzole vzdáleného spuštění [Kudu](https://github.com/projectkudu/kudu/wiki) a zjistit chybu:</span><span class="sxs-lookup"><span data-stu-id="e6b6b-606">You can run the app in the [Kudu](https://github.com/projectkudu/kudu/wiki) Remote Execution Console to discover the error:</span></span>

1. <span data-ttu-id="e6b6b-607">Otevřete **rozšířené nástroje** v oblasti **vývojových nástrojů.**</span><span class="sxs-lookup"><span data-stu-id="e6b6b-607">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="e6b6b-608">Vyberte tlačítko \*\*Přejít.&rarr; \*\*</span><span class="sxs-lookup"><span data-stu-id="e6b6b-608">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="e6b6b-609">Konzole Kudu se otevře v novém kartě prohlížeče nebo v okně.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-609">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="e6b6b-610">Pomocí navigačního panelu v horní části stránky otevřete **konzolu ladění** a vyberte **možnost CMD**.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-610">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>

#### <a name="test-a-32-bit-x86-app"></a><span data-ttu-id="e6b6b-611">Testování 32bitové aplikace (x86)</span><span class="sxs-lookup"><span data-stu-id="e6b6b-611">Test a 32-bit (x86) app</span></span>

<span data-ttu-id="e6b6b-612">**Aktuální verze**</span><span class="sxs-lookup"><span data-stu-id="e6b6b-612">**Current release**</span></span>

1. `cd d:\home\site\wwwroot`
1. <span data-ttu-id="e6b6b-613">Spusťte aplikaci:</span><span class="sxs-lookup"><span data-stu-id="e6b6b-613">Run the app:</span></span>
   * <span data-ttu-id="e6b6b-614">Pokud je aplikace [nasazení závislé na rámci](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span><span class="sxs-lookup"><span data-stu-id="e6b6b-614">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

     ```dotnetcli
     dotnet .\{ASSEMBLY NAME}.dll
     ```

   * <span data-ttu-id="e6b6b-615">Pokud se jedná o [samostatné nasazení](/dotnet/core/deploying/#self-contained-deployments-scd)aplikace :</span><span class="sxs-lookup"><span data-stu-id="e6b6b-615">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

     ```console
     {ASSEMBLY NAME}.exe
     ```

<span data-ttu-id="e6b6b-616">Výstup konzoly z aplikace, v němž se zobrazují všechny chyby, je předán do konzoly Kudu.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-616">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="e6b6b-617">**Nasazení závislé na rozhraní spuštěné ve verzi preview**</span><span class="sxs-lookup"><span data-stu-id="e6b6b-617">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="e6b6b-618">*Vyžaduje instalaci rozšíření webu ASP.NET Core {VERSION} (x86).*</span><span class="sxs-lookup"><span data-stu-id="e6b6b-618">*Requires installing the ASP.NET Core {VERSION} (x86) Runtime site extension.*</span></span>

1. <span data-ttu-id="e6b6b-619">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32`(`{X.Y}` je runtime verze)</span><span class="sxs-lookup"><span data-stu-id="e6b6b-619">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="e6b6b-620">Spusťte aplikaci: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="e6b6b-620">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="e6b6b-621">Výstup konzoly z aplikace, v němž se zobrazují všechny chyby, je předán do konzoly Kudu.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-621">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

#### <a name="test-a-64-bit-x64-app"></a><span data-ttu-id="e6b6b-622">Testování 64bitové aplikace (x64)</span><span class="sxs-lookup"><span data-stu-id="e6b6b-622">Test a 64-bit (x64) app</span></span>

<span data-ttu-id="e6b6b-623">**Aktuální verze**</span><span class="sxs-lookup"><span data-stu-id="e6b6b-623">**Current release**</span></span>

* <span data-ttu-id="e6b6b-624">Pokud je aplikace 64bitové (x64) [nasazení závislé na rozhraní](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span><span class="sxs-lookup"><span data-stu-id="e6b6b-624">If the app is a 64-bit (x64) [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>
  1. `cd D:\Program Files\dotnet`
  1. <span data-ttu-id="e6b6b-625">Spusťte aplikaci: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="e6b6b-625">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>
* <span data-ttu-id="e6b6b-626">Pokud se jedná o [samostatné nasazení](/dotnet/core/deploying/#self-contained-deployments-scd)aplikace :</span><span class="sxs-lookup"><span data-stu-id="e6b6b-626">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>
  1. `cd D:\home\site\wwwroot`
  1. <span data-ttu-id="e6b6b-627">Spusťte aplikaci: `{ASSEMBLY NAME}.exe`</span><span class="sxs-lookup"><span data-stu-id="e6b6b-627">Run the app: `{ASSEMBLY NAME}.exe`</span></span>

<span data-ttu-id="e6b6b-628">Výstup konzoly z aplikace, v němž se zobrazují všechny chyby, je předán do konzoly Kudu.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-628">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="e6b6b-629">**Nasazení závislé na rozhraní spuštěné ve verzi preview**</span><span class="sxs-lookup"><span data-stu-id="e6b6b-629">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="e6b6b-630">*Vyžaduje instalaci rozšíření webu ASP.NET Core {VERSION} (x64).*</span><span class="sxs-lookup"><span data-stu-id="e6b6b-630">*Requires installing the ASP.NET Core {VERSION} (x64) Runtime site extension.*</span></span>

1. <span data-ttu-id="e6b6b-631">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64`(`{X.Y}` je runtime verze)</span><span class="sxs-lookup"><span data-stu-id="e6b6b-631">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="e6b6b-632">Spusťte aplikaci: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="e6b6b-632">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="e6b6b-633">Výstup konzoly z aplikace, v němž se zobrazují všechny chyby, je předán do konzoly Kudu.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-633">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

### <a name="aspnet-core-module-stdout-log-azure-app-service"></a><span data-ttu-id="e6b6b-634">ASP.NET protokol stdout modulu (služba Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="e6b6b-634">ASP.NET Core Module stdout log (Azure App Service)</span></span>

<span data-ttu-id="e6b6b-635">Protokol stdout ASP.NET Core Module často zaznamenává užitečné chybové zprávy, které nebyly nalezeny v protokolu událostí aplikace.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-635">The ASP.NET Core Module stdout log often records useful error messages not found in the Application Event Log.</span></span> <span data-ttu-id="e6b6b-636">Povolení a zobrazení protokolů stdout:</span><span class="sxs-lookup"><span data-stu-id="e6b6b-636">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="e6b6b-637">Přejděte na okno **Diagnostikovat a vyřešit problémy** na webu Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-637">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="e6b6b-638">V **části SELECT PROBLEM CATEGORY**vyberte tlačítko Web App **Down** .</span><span class="sxs-lookup"><span data-stu-id="e6b6b-638">Under **SELECT PROBLEM CATEGORY**, select the **Web App Down** button.</span></span>
1. <span data-ttu-id="e6b6b-639">V části **Navrhovaná řešení** > **Povolit přesměrování protokolu Stdout**vyberte tlačítko **pro otevření konzoly Kudu a upravte web.Config**.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-639">Under **Suggested Solutions** > **Enable Stdout Log Redirection**, select the button to **Open Kudu Console to edit Web.Config**.</span></span>
1. <span data-ttu-id="e6b6b-640">V **diagnostické konzole**Kudu otevřete složky webu **site** > **wwwroot**.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-640">In the Kudu **Diagnostic Console**, open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="e6b6b-641">Posunutím dolů zobrazíte soubor *web.config* v dolní části seznamu.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-641">Scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="e6b6b-642">Klikněte na ikonu tužky vedle souboru *web.config.*</span><span class="sxs-lookup"><span data-stu-id="e6b6b-642">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="e6b6b-643">Nastavte **stdoutLogEnabled** na `true` cestu **stdoutLogFile** `\\?\%home%\LogFiles\stdout`a změňte ji na: .</span><span class="sxs-lookup"><span data-stu-id="e6b6b-643">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="e6b6b-644">Vyberte **Uložit,** chcete-li uložit aktualizovaný soubor *web.config.*</span><span class="sxs-lookup"><span data-stu-id="e6b6b-644">Select **Save** to save the updated *web.config* file.</span></span>
1. <span data-ttu-id="e6b6b-645">Požádejte o to do aplikace.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-645">Make a request to the app.</span></span>
1. <span data-ttu-id="e6b6b-646">Vraťte se na Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-646">Return to the Azure portal.</span></span> <span data-ttu-id="e6b6b-647">Vyberte okno **Pokročilé nástroje** v oblasti **NÁSTROJE VÝVOJE.**</span><span class="sxs-lookup"><span data-stu-id="e6b6b-647">Select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="e6b6b-648">Vyberte tlačítko \*\*Přejít.&rarr; \*\*</span><span class="sxs-lookup"><span data-stu-id="e6b6b-648">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="e6b6b-649">Konzole Kudu se otevře v novém kartě prohlížeče nebo v okně.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-649">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="e6b6b-650">Pomocí navigačního panelu v horní části stránky otevřete **konzolu ladění** a vyberte **možnost CMD**.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-650">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="e6b6b-651">Vyberte složku **LogFiles.**</span><span class="sxs-lookup"><span data-stu-id="e6b6b-651">Select the **LogFiles** folder.</span></span>
1. <span data-ttu-id="e6b6b-652">Zkontrolujte sloupec **Změněno** a vyberte ikonu tužky a upravte protokol stdout s nejnovějším datem změny.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-652">Inspect the **Modified** column and select the pencil icon to edit the stdout log with the latest modification date.</span></span>
1. <span data-ttu-id="e6b6b-653">Po otevření souboru protokolu se zobrazí chyba.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-653">When the log file opens, the error is displayed.</span></span>

<span data-ttu-id="e6b6b-654">Zakažte protokolování stdout po dokončení řešení potíží:</span><span class="sxs-lookup"><span data-stu-id="e6b6b-654">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="e6b6b-655">V diagnostické **konzole**Kudu se vraťte na **web** > **wwwroot** cesty a odhalte soubor *web.config.*</span><span class="sxs-lookup"><span data-stu-id="e6b6b-655">In the Kudu **Diagnostic Console**, return to the path **site** > **wwwroot** to reveal the *web.config* file.</span></span> <span data-ttu-id="e6b6b-656">Znovu otevřete soubor **web.config** výběrem ikony tužky.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-656">Open the **web.config** file again by selecting the pencil icon.</span></span>
1. <span data-ttu-id="e6b6b-657">Nastavte **stdoutLogEnabled** na `false`.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-657">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="e6b6b-658">Chcete-li soubor uložit, vyberte **Uložit.**</span><span class="sxs-lookup"><span data-stu-id="e6b6b-658">Select **Save** to save the file.</span></span>

<span data-ttu-id="e6b6b-659">Další informace naleznete v tématu <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-659">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

> [!WARNING]
> <span data-ttu-id="e6b6b-660">Selhání zakázání protokolu stdout může vést k selhání aplikace nebo serveru.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-660">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="e6b6b-661">Neexistuje žádné omezení na velikost souboru protokolu nebo počet vytvořených souborů protokolu.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-661">There's no limit on log file size or the number of log files created.</span></span> <span data-ttu-id="e6b6b-662">K řešení problémů se spuštěním aplikace používejte protokolování stdout pouze.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-662">Only use stdout logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="e6b6b-663">Pro obecné protokolování v aplikaci ASP.NET Core po spuštění použijte knihovnu protokolování, která omezuje velikost souboru protokolu a otáčí protokoly.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-663">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="e6b6b-664">Další informace naleznete [v tématu zprostředkovatelé protokolování třetích stran](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="e6b6b-664">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="aspnet-core-module-debug-log-azure-app-service"></a><span data-ttu-id="e6b6b-665">protokol ladění ASP.NET základního modulu (služba Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="e6b6b-665">ASP.NET Core Module debug log (Azure App Service)</span></span>

<span data-ttu-id="e6b6b-666">Protokol ladění ASP.NET core modul poskytuje další, hlubší protokolování z ASP.NET core modulu.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-666">The ASP.NET Core Module debug log provides additional, deeper logging from the ASP.NET Core Module.</span></span> <span data-ttu-id="e6b6b-667">Povolení a zobrazení protokolů stdout:</span><span class="sxs-lookup"><span data-stu-id="e6b6b-667">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="e6b6b-668">Chcete-li povolit rozšířený diagnostický protokol, proveďte jednu z následujících akcí:</span><span class="sxs-lookup"><span data-stu-id="e6b6b-668">To enable the enhanced diagnostic log, perform either of the following:</span></span>
   * <span data-ttu-id="e6b6b-669">Podle pokynů v [rozšířené diagnostické protokoly](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) nakonfigurovat aplikaci pro rozšířené diagnostické protokolování.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-669">Follow the instructions in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) to configure the app for an enhanced diagnostic logging.</span></span> <span data-ttu-id="e6b6b-670">Znovu nasadit aplikaci.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-670">Redeploy the app.</span></span>
   * <span data-ttu-id="e6b6b-671">Přidejte `<handlerSettings>` zobrazené v [rozšířených diagnostických protokolech](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) do souboru *web.config* živé aplikace pomocí konzoly Kudu:</span><span class="sxs-lookup"><span data-stu-id="e6b6b-671">Add the `<handlerSettings>` shown in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) to the live app's *web.config* file using the Kudu console:</span></span>
     1. <span data-ttu-id="e6b6b-672">Otevřete **rozšířené nástroje** v oblasti **vývojových nástrojů.**</span><span class="sxs-lookup"><span data-stu-id="e6b6b-672">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="e6b6b-673">Vyberte tlačítko \*\*Přejít.&rarr; \*\*</span><span class="sxs-lookup"><span data-stu-id="e6b6b-673">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="e6b6b-674">Konzole Kudu se otevře v novém kartě prohlížeče nebo v okně.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-674">The Kudu console opens in a new browser tab or window.</span></span>
     1. <span data-ttu-id="e6b6b-675">Pomocí navigačního panelu v horní části stránky otevřete **konzolu ladění** a vyberte **možnost CMD**.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-675">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
     1. <span data-ttu-id="e6b6b-676">Otevřete složky na **webu** > cesty**wwwroot**.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-676">Open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="e6b6b-677">Upravte soubor *web.config* výběrem tlačítka tužky.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-677">Edit the *web.config* file by selecting the pencil button.</span></span> <span data-ttu-id="e6b6b-678">Přidejte `<handlerSettings>` oddíl, jak je znázorněno v [rozšířené diagnostické protokoly](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs).</span><span class="sxs-lookup"><span data-stu-id="e6b6b-678">Add the `<handlerSettings>` section as shown in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs).</span></span> <span data-ttu-id="e6b6b-679">Vyberte tlačítko **Uložit**.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-679">Select the **Save** button.</span></span>
1. <span data-ttu-id="e6b6b-680">Otevřete **rozšířené nástroje** v oblasti **vývojových nástrojů.**</span><span class="sxs-lookup"><span data-stu-id="e6b6b-680">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="e6b6b-681">Vyberte tlačítko \*\*Přejít.&rarr; \*\*</span><span class="sxs-lookup"><span data-stu-id="e6b6b-681">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="e6b6b-682">Konzole Kudu se otevře v novém kartě prohlížeče nebo v okně.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-682">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="e6b6b-683">Pomocí navigačního panelu v horní části stránky otevřete **konzolu ladění** a vyberte **možnost CMD**.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-683">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="e6b6b-684">Otevřete složky na **webu** > cesty**wwwroot**.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-684">Open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="e6b6b-685">Pokud jste nezadali cestu pro soubor *aspnetcore-debug.log,* soubor se zobrazí v seznamu.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-685">If you didn't supply a path for the *aspnetcore-debug.log* file, the file appears in the list.</span></span> <span data-ttu-id="e6b6b-686">Pokud jste zadali cestu, přejděte do umístění souboru protokolu.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-686">If you supplied a path, navigate to the location of the log file.</span></span>
1. <span data-ttu-id="e6b6b-687">Otevřete soubor protokolu pomocí tlačítka tužky vedle názvu souboru.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-687">Open the log file with the pencil button next to the file name.</span></span>

<span data-ttu-id="e6b6b-688">Zakažte protokolování ladění po dokončení řešení potíží:</span><span class="sxs-lookup"><span data-stu-id="e6b6b-688">Disable debug logging when troubleshooting is complete:</span></span>

<span data-ttu-id="e6b6b-689">Chcete-li protokol rozšířeného ladění zakázat, proveďte některou z následujících akcí:</span><span class="sxs-lookup"><span data-stu-id="e6b6b-689">To disable the enhanced debug log, perform either of the following:</span></span>

* <span data-ttu-id="e6b6b-690">Odeberte `<handlerSettings>` soubor *web.config* místně a znovu nasadit aplikaci.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-690">Remove the `<handlerSettings>` from the *web.config* file locally and redeploy the app.</span></span>
* <span data-ttu-id="e6b6b-691">Pomocí konzoly Kudu upravte soubor *web.config* a odeberte `<handlerSettings>` oddíl.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-691">Use the Kudu console to edit the *web.config* file and remove the `<handlerSettings>` section.</span></span> <span data-ttu-id="e6b6b-692">Uložte soubor.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-692">Save the file.</span></span>

<span data-ttu-id="e6b6b-693">Další informace naleznete v tématu <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-693">For more information, see <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span></span>

> [!WARNING]
> <span data-ttu-id="e6b6b-694">Selhání zakázání protokolu ladění může vést k selhání aplikace nebo serveru.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-694">Failure to disable the debug log can lead to app or server failure.</span></span> <span data-ttu-id="e6b6b-695">Neexistuje žádné omezení na velikost souboru protokolu.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-695">There's no limit on log file size.</span></span> <span data-ttu-id="e6b6b-696">Protokolování ladění používejte pouze k řešení potíží se spuštěním aplikace.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-696">Only use debug logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="e6b6b-697">Pro obecné protokolování v aplikaci ASP.NET Core po spuštění použijte knihovnu protokolování, která omezuje velikost souboru protokolu a otáčí protokoly.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-697">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="e6b6b-698">Další informace naleznete [v tématu zprostředkovatelé protokolování třetích stran](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="e6b6b-698">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="slow-or-hanging-app-azure-app-service"></a><span data-ttu-id="e6b6b-699">Pomalá nebo zavěšená aplikace (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="e6b6b-699">Slow or hanging app (Azure App Service)</span></span>

<span data-ttu-id="e6b6b-700">Když aplikace reaguje pomalu nebo se při žádosti zablokuje, přečtěte si následující články:</span><span class="sxs-lookup"><span data-stu-id="e6b6b-700">When an app responds slowly or hangs on a request, see the following articles:</span></span>

* [<span data-ttu-id="e6b6b-701">Řešení potíží s výkonem pomalých webových aplikací ve službě Azure App Service</span><span class="sxs-lookup"><span data-stu-id="e6b6b-701">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="e6b6b-702">Použití rozšíření webu Diagnostikátor selhání k zachycení výpisu pro občasné výjimky problémy nebo problémy s výkonem na Azure Web App</span><span class="sxs-lookup"><span data-stu-id="e6b6b-702">Use Crash Diagnoser Site Extension to Capture Dump for Intermittent Exception issues or performance issues on Azure Web App</span></span>](https://blogs.msdn.microsoft.com/asiatech/2015/12/28/use-crash-diagnoser-site-extension-to-capture-dump-for-intermittent-exception-issues-or-performance-issues-on-azure-web-app/)

### <a name="monitoring-blades"></a><span data-ttu-id="e6b6b-703">Monitorovací nože</span><span class="sxs-lookup"><span data-stu-id="e6b6b-703">Monitoring blades</span></span>

<span data-ttu-id="e6b6b-704">Monitorování blades poskytují alternativní řešení potíží k metodám popsaným výše v tématu.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-704">Monitoring blades provide an alternative troubleshooting experience to the methods described earlier in the topic.</span></span> <span data-ttu-id="e6b6b-705">Tyto čepele lze použít k diagnostice chyb řady 500.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-705">These blades can be used to diagnose 500-series errors.</span></span>

<span data-ttu-id="e6b6b-706">Zkontrolujte, zda jsou nainstalovány ASP.NET rozšíření core.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-706">Confirm that the ASP.NET Core Extensions are installed.</span></span> <span data-ttu-id="e6b6b-707">Pokud rozšíření nejsou nainstalovaná, nainstalujte je ručně:</span><span class="sxs-lookup"><span data-stu-id="e6b6b-707">If the extensions aren't installed, install them manually:</span></span>

1. <span data-ttu-id="e6b6b-708">V části **Čepele NÁSTROJE PRO VÝVOJ** vyberte okno **Rozšíření.**</span><span class="sxs-lookup"><span data-stu-id="e6b6b-708">In the **DEVELOPMENT TOOLS** blade section, select the **Extensions** blade.</span></span>
1. <span data-ttu-id="e6b6b-709">V seznamu by se měl **zobrazit ASP.NET rozšíření jádra.**</span><span class="sxs-lookup"><span data-stu-id="e6b6b-709">The **ASP.NET Core Extensions** should appear in the list.</span></span>
1. <span data-ttu-id="e6b6b-710">Pokud rozšíření nejsou nainstalovaná, vyberte tlačítko **Přidat.**</span><span class="sxs-lookup"><span data-stu-id="e6b6b-710">If the extensions aren't installed, select the **Add** button.</span></span>
1. <span data-ttu-id="e6b6b-711">Ze seznamu zvolte **ASP.NET jádrová rozšíření.**</span><span class="sxs-lookup"><span data-stu-id="e6b6b-711">Choose the **ASP.NET Core Extensions** from the list.</span></span>
1. <span data-ttu-id="e6b6b-712">Chcete-li přijmout zákonné podmínky, vyberte **možnost OK.**</span><span class="sxs-lookup"><span data-stu-id="e6b6b-712">Select **OK** to accept the legal terms.</span></span>
1. <span data-ttu-id="e6b6b-713">V okně **Přidat prodlužovací** okno vyberte **OK.**</span><span class="sxs-lookup"><span data-stu-id="e6b6b-713">Select **OK** on the **Add extension** blade.</span></span>
1. <span data-ttu-id="e6b6b-714">Informační zpráva o automaticky otevíraná okna označuje, kdy jsou rozšíření úspěšně nainstalována.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-714">An informational pop-up message indicates when the extensions are successfully installed.</span></span>

<span data-ttu-id="e6b6b-715">Pokud protokolování stdout není povoleno, postupujte takto:</span><span class="sxs-lookup"><span data-stu-id="e6b6b-715">If stdout logging isn't enabled, follow these steps:</span></span>

1. <span data-ttu-id="e6b6b-716">Na webu Azure Portal vyberte okno **Rozšířené nástroje** v oblasti NÁSTROJE **PRO VÝVOJ.**</span><span class="sxs-lookup"><span data-stu-id="e6b6b-716">In the Azure portal, select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="e6b6b-717">Vyberte tlačítko \*\*Přejít.&rarr; \*\*</span><span class="sxs-lookup"><span data-stu-id="e6b6b-717">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="e6b6b-718">Konzole Kudu se otevře v novém kartě prohlížeče nebo v okně.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-718">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="e6b6b-719">Pomocí navigačního panelu v horní části stránky otevřete **konzolu ladění** a vyberte **možnost CMD**.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-719">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="e6b6b-720">Otevřete složky na **webu** > cesty **wwwroot** a posuňte se dolů a odhalte soubor *web.config* v dolní části seznamu.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-720">Open the folders to the path **site** > **wwwroot** and scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="e6b6b-721">Klikněte na ikonu tužky vedle souboru *web.config.*</span><span class="sxs-lookup"><span data-stu-id="e6b6b-721">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="e6b6b-722">Nastavte **stdoutLogEnabled** na `true` cestu **stdoutLogFile** `\\?\%home%\LogFiles\stdout`a změňte ji na: .</span><span class="sxs-lookup"><span data-stu-id="e6b6b-722">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="e6b6b-723">Vyberte **Uložit,** chcete-li uložit aktualizovaný soubor *web.config.*</span><span class="sxs-lookup"><span data-stu-id="e6b6b-723">Select **Save** to save the updated *web.config* file.</span></span>

<span data-ttu-id="e6b6b-724">Postupujte k aktivaci diagnostického protokolování:</span><span class="sxs-lookup"><span data-stu-id="e6b6b-724">Proceed to activate diagnostic logging:</span></span>

1. <span data-ttu-id="e6b6b-725">Na webu Azure Portal vyberte okno **Protokoly diagnostiky.**</span><span class="sxs-lookup"><span data-stu-id="e6b6b-725">In the Azure portal, select the **Diagnostics logs** blade.</span></span>
1. <span data-ttu-id="e6b6b-726">Vyberte přepínač **Zapnuto** pro **protokolování aplikací (souborový systém)** a **Podrobné chybové zprávy**.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-726">Select the **On** switch for **Application Logging (Filesystem)** and **Detailed error messages**.</span></span> <span data-ttu-id="e6b6b-727">V horní části nože vyberte tlačítko **Uložit.**</span><span class="sxs-lookup"><span data-stu-id="e6b6b-727">Select the **Save** button at the top of the blade.</span></span>
1. <span data-ttu-id="e6b6b-728">Chcete-li zahrnout trasování neúspěšných požadavků, známé také jako protokolfreb (Failed Request Event Buffering), vyberte přepínač **Zapnuto** pro **trasování neúspěšných požadavků**.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-728">To include failed request tracing, also known as Failed Request Event Buffering (FREB) logging, select the **On** switch for **Failed request tracing**.</span></span>
1. <span data-ttu-id="e6b6b-729">Vyberte okno **protokolu datového proudu,** který je uveden okamžitě pod **protokoly protokoly diagnostiky** na portálu.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-729">Select the **Log stream** blade, which is listed immediately under the **Diagnostics logs** blade in the portal.</span></span>
1. <span data-ttu-id="e6b6b-730">Požádejte o to do aplikace.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-730">Make a request to the app.</span></span>
1. <span data-ttu-id="e6b6b-731">V rámci dat datového proudu protokolu je uvedena příčina chyby.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-731">Within the log stream data, the cause of the error is indicated.</span></span>

<span data-ttu-id="e6b6b-732">Nezapomeňte zakázat stdout protokolování po dokončení řešení potíží.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-732">Be sure to disable stdout logging when troubleshooting is complete.</span></span>

<span data-ttu-id="e6b6b-733">Zobrazení protokolů trasování neúspěšných požadavků (protokoly FREB):</span><span class="sxs-lookup"><span data-stu-id="e6b6b-733">To view the failed request tracing logs (FREB logs):</span></span>

1. <span data-ttu-id="e6b6b-734">Přejděte na okno **Diagnostikovat a vyřešit problémy** na webu Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-734">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="e6b6b-735">V yberte **Protokoly trasování neúspěšných požadavků** v oblasti **NÁSTROJE PODPORY** na postranním panelu.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-735">Select **Failed Request Tracing Logs** from the **SUPPORT TOOLS** area of the sidebar.</span></span>

<span data-ttu-id="e6b6b-736">Další informace [najdete v tématu Sledování neúspěšných požadavků v tématu Povolit diagnostiku pro webové aplikace v tématu Služby aplikací](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) a [v nejčastějších dotazech k výkonu aplikací pro webové aplikace v Azure: Jak zapnu trasování neúspěšných požadavků?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing)</span><span class="sxs-lookup"><span data-stu-id="e6b6b-736">See [Failed request traces section of the Enable diagnostics logging for web apps in Azure App Service topic](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) and the [Application performance FAQs for Web Apps in Azure: How do I turn on failed request tracing?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing) for more information.</span></span>

<span data-ttu-id="e6b6b-737">Další informace najdete [v tématu Povolení protokolování diagnostiky pro webové aplikace ve službě Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log).</span><span class="sxs-lookup"><span data-stu-id="e6b6b-737">For more information, see [Enable diagnostics logging for web apps in Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log).</span></span>

> [!WARNING]
> <span data-ttu-id="e6b6b-738">Selhání zakázání protokolu stdout může vést k selhání aplikace nebo serveru.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-738">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="e6b6b-739">Neexistuje žádné omezení na velikost souboru protokolu nebo počet vytvořených souborů protokolu.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-739">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="e6b6b-740">Pro rutinní protokolování v aplikaci ASP.NET Core použijte knihovnu protokolování, která omezuje velikost souboru protokolu a otáčí protokoly.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-740">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="e6b6b-741">Další informace naleznete [v tématu zprostředkovatelé protokolování třetích stran](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="e6b6b-741">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

## <a name="troubleshoot-on-iis"></a><span data-ttu-id="e6b6b-742">Odstraňování potíží ve službě IIS</span><span class="sxs-lookup"><span data-stu-id="e6b6b-742">Troubleshoot on IIS</span></span>

### <a name="application-event-log-iis"></a><span data-ttu-id="e6b6b-743">Protokol událostí aplikace (IIS)</span><span class="sxs-lookup"><span data-stu-id="e6b6b-743">Application Event Log (IIS)</span></span>

<span data-ttu-id="e6b6b-744">Přístup k protokolu událostí aplikace:</span><span class="sxs-lookup"><span data-stu-id="e6b6b-744">Access the Application Event Log:</span></span>

1. <span data-ttu-id="e6b6b-745">Otevřete nabídku Start, vyhledejte *Prohlížeč událostí*a vyberte aplikaci **Prohlížeč událostí.**</span><span class="sxs-lookup"><span data-stu-id="e6b6b-745">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="e6b6b-746">V **Prohlížeči událostí**otevřete uzel **Protokoly systému Windows.**</span><span class="sxs-lookup"><span data-stu-id="e6b6b-746">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="e6b6b-747">Výběrem **možnosti Aplikace** otevřete protokol událostí aplikace.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-747">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="e6b6b-748">Vyhledejte chyby spojené s aplikací se selháním.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-748">Search for errors associated with the failing app.</span></span> <span data-ttu-id="e6b6b-749">Chyby mají hodnotu *modulu AspNetCore služby IIS* nebo *modulu AspNetCore služby IIS Express* ve sloupci *Zdroj.*</span><span class="sxs-lookup"><span data-stu-id="e6b6b-749">Errors have a value of *IIS AspNetCore Module* or *IIS Express AspNetCore Module* in the *Source* column.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="e6b6b-750">Spuštění aplikace na příkazovém řádku</span><span class="sxs-lookup"><span data-stu-id="e6b6b-750">Run the app at a command prompt</span></span>

<span data-ttu-id="e6b6b-751">Mnoho chyb při spuštění nevytváří užitečné informace v protokolu událostí aplikace.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-751">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="e6b6b-752">Příčinu některých chyb můžete najít spuštěním aplikace na příkazovém řádku v hostitelském systému.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-752">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span>

#### <a name="framework-dependent-deployment"></a><span data-ttu-id="e6b6b-753">Nasazení závislé na rámci</span><span class="sxs-lookup"><span data-stu-id="e6b6b-753">Framework-dependent deployment</span></span>

<span data-ttu-id="e6b6b-754">Pokud je aplikace [nasazení závislé na rámci](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span><span class="sxs-lookup"><span data-stu-id="e6b6b-754">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

1. <span data-ttu-id="e6b6b-755">Na příkazovém řádku přejděte do složky nasazení a spusťte aplikaci spuštěním sestavení aplikace pomocí *dotnet.exe*.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-755">At a command prompt, navigate to the deployment folder and run the app by executing the app's assembly with *dotnet.exe*.</span></span> <span data-ttu-id="e6b6b-756">V následujícím příkazu nahraďte název sestavení \<aplikace assembly_name `dotnet .\<assembly_name>.dll`>: .</span><span class="sxs-lookup"><span data-stu-id="e6b6b-756">In the following command, substitute the name of the app's assembly for \<assembly_name>: `dotnet .\<assembly_name>.dll`.</span></span>
1. <span data-ttu-id="e6b6b-757">Výstup konzoly z aplikace, zobrazující případné chyby, je zapsán do okna konzoly.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-757">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="e6b6b-758">Pokud dojde k chybám při podání požadavku na aplikaci, podat žádost na hostitele a port, kde Kestrel naslouchá.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-758">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="e6b6b-759">Pomocí výchozího hostitele a příspěvku `http://localhost:5000/`požádejte o místo .</span><span class="sxs-lookup"><span data-stu-id="e6b6b-759">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="e6b6b-760">Pokud aplikace reaguje normálně na adrese koncového bodu Kestrel, problém je pravděpodobnější související s konfigurací hostování a méně pravděpodobné v rámci aplikace.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-760">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

#### <a name="self-contained-deployment"></a><span data-ttu-id="e6b6b-761">Samostatné nasazení</span><span class="sxs-lookup"><span data-stu-id="e6b6b-761">Self-contained deployment</span></span>

<span data-ttu-id="e6b6b-762">Pokud se jedná o [samostatné nasazení](/dotnet/core/deploying/#self-contained-deployments-scd)aplikace :</span><span class="sxs-lookup"><span data-stu-id="e6b6b-762">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

1. <span data-ttu-id="e6b6b-763">Na příkazovém řádku přejděte do složky nasazení a spusťte spustitelný soubor aplikace.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-763">At a command prompt, navigate to the deployment folder and run the app's executable.</span></span> <span data-ttu-id="e6b6b-764">V následujícím příkazu nahraďte název sestavení \<aplikace assembly_name `<assembly_name>.exe`>: .</span><span class="sxs-lookup"><span data-stu-id="e6b6b-764">In the following command, substitute the name of the app's assembly for \<assembly_name>: `<assembly_name>.exe`.</span></span>
1. <span data-ttu-id="e6b6b-765">Výstup konzoly z aplikace, zobrazující případné chyby, je zapsán do okna konzoly.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-765">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="e6b6b-766">Pokud dojde k chybám při podání požadavku na aplikaci, podat žádost na hostitele a port, kde Kestrel naslouchá.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-766">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="e6b6b-767">Pomocí výchozího hostitele a příspěvku `http://localhost:5000/`požádejte o místo .</span><span class="sxs-lookup"><span data-stu-id="e6b6b-767">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="e6b6b-768">Pokud aplikace reaguje normálně na adrese koncového bodu Kestrel, problém je pravděpodobnější související s konfigurací hostování a méně pravděpodobné v rámci aplikace.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-768">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

### <a name="aspnet-core-module-stdout-log-iis"></a><span data-ttu-id="e6b6b-769">ASP.NET protokol stdout modulu (IIS)</span><span class="sxs-lookup"><span data-stu-id="e6b6b-769">ASP.NET Core Module stdout log (IIS)</span></span>

<span data-ttu-id="e6b6b-770">Povolení a zobrazení protokolů stdout:</span><span class="sxs-lookup"><span data-stu-id="e6b6b-770">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="e6b6b-771">Přejděte do složky nasazení webu v hostitelském systému.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-771">Navigate to the site's deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="e6b6b-772">Pokud složka *protokolů* není k dispozici, vytvořte složku.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-772">If the *logs* folder isn't present, create the folder.</span></span> <span data-ttu-id="e6b6b-773">Pokyny k povolení msbuild vytvořit složku *protokolů* v nasazení automaticky, naleznete v tématu [directory struktura.](xref:host-and-deploy/directory-structure)</span><span class="sxs-lookup"><span data-stu-id="e6b6b-773">For instructions on how to enable MSBuild to create the *logs* folder in the deployment automatically, see the [Directory structure](xref:host-and-deploy/directory-structure) topic.</span></span>
1. <span data-ttu-id="e6b6b-774">Upravte soubor *web.config.*</span><span class="sxs-lookup"><span data-stu-id="e6b6b-774">Edit the *web.config* file.</span></span> <span data-ttu-id="e6b6b-775">Nastavte **stdoutLogEnabled** a `true` změňte cestu **stdoutLogFile** tak, `.\logs\stdout`aby přecózovala na složku *protokolů* (například ).</span><span class="sxs-lookup"><span data-stu-id="e6b6b-775">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to point to the *logs* folder (for example, `.\logs\stdout`).</span></span> <span data-ttu-id="e6b6b-776">`stdout`v cestě je předpona názvu souboru protokolu.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-776">`stdout` in the path is the log file name prefix.</span></span> <span data-ttu-id="e6b6b-777">Časové razítko, ID procesu a přípona souboru jsou přidány automaticky při vytvoření protokolu.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-777">A timestamp, process id, and file extension are added automatically when the log is created.</span></span> <span data-ttu-id="e6b6b-778">Jako `stdout` předpona názvu souboru je typický soubor protokolu pojmenován *stdout_20180205184032_5412.log*.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-778">Using `stdout` as the file name prefix, a typical log file is named *stdout_20180205184032_5412.log*.</span></span>
1. <span data-ttu-id="e6b6b-779">Ujistěte se, že identita fondu aplikací má oprávnění k zápisu do složky *protokolů.*</span><span class="sxs-lookup"><span data-stu-id="e6b6b-779">Ensure your application pool's identity has write permissions to the *logs* folder.</span></span>
1. <span data-ttu-id="e6b6b-780">Uložte aktualizovaný soubor *web.config.*</span><span class="sxs-lookup"><span data-stu-id="e6b6b-780">Save the updated *web.config* file.</span></span>
1. <span data-ttu-id="e6b6b-781">Požádejte o to do aplikace.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-781">Make a request to the app.</span></span>
1. <span data-ttu-id="e6b6b-782">Přejděte do složky *protokolů.*</span><span class="sxs-lookup"><span data-stu-id="e6b6b-782">Navigate to the *logs* folder.</span></span> <span data-ttu-id="e6b6b-783">Vyhledejte a otevřete nejnovější protokol stdout.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-783">Find and open the most recent stdout log.</span></span>
1. <span data-ttu-id="e6b6b-784">Prostudujte si protokol pro chyby.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-784">Study the log for errors.</span></span>

<span data-ttu-id="e6b6b-785">Zakažte protokolování stdout po dokončení řešení potíží:</span><span class="sxs-lookup"><span data-stu-id="e6b6b-785">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="e6b6b-786">Upravte soubor *web.config.*</span><span class="sxs-lookup"><span data-stu-id="e6b6b-786">Edit the *web.config* file.</span></span>
1. <span data-ttu-id="e6b6b-787">Nastavte **stdoutLogEnabled** na `false`.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-787">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="e6b6b-788">Uložte soubor.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-788">Save the file.</span></span>

<span data-ttu-id="e6b6b-789">Další informace naleznete v tématu <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-789">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

> [!WARNING]
> <span data-ttu-id="e6b6b-790">Selhání zakázání protokolu stdout může vést k selhání aplikace nebo serveru.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-790">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="e6b6b-791">Neexistuje žádné omezení na velikost souboru protokolu nebo počet vytvořených souborů protokolu.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-791">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="e6b6b-792">Pro rutinní protokolování v aplikaci ASP.NET Core použijte knihovnu protokolování, která omezuje velikost souboru protokolu a otáčí protokoly.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-792">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="e6b6b-793">Další informace naleznete [v tématu zprostředkovatelé protokolování třetích stran](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="e6b6b-793">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="aspnet-core-module-debug-log-iis"></a><span data-ttu-id="e6b6b-794">protokol ladění základního modulu ASP.NET (IIS)</span><span class="sxs-lookup"><span data-stu-id="e6b6b-794">ASP.NET Core Module debug log (IIS)</span></span>

<span data-ttu-id="e6b6b-795">Přidejte do souboru *web.config* aplikace následující nastavení obslužné rutiny, která umožní ASP.NET protokol ladění základního modulu:</span><span class="sxs-lookup"><span data-stu-id="e6b6b-795">Add the following handler settings to the app's *web.config* file to enable ASP.NET Core Module debug log:</span></span>

```xml
<aspNetCore ...>
  <handlerSettings>
    <handlerSetting name="debugLevel" value="file" />
    <handlerSetting name="debugFile" value="c:\temp\ancm.log" />
  </handlerSettings>
</aspNetCore>
```

<span data-ttu-id="e6b6b-796">Zkontrolujte, zda existuje cesta zadaná pro protokol a zda má identita fondu aplikací oprávnění k zápisu do umístění.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-796">Confirm that the path specified for the log exists and that the app pool's identity has write permissions to the location.</span></span>

<span data-ttu-id="e6b6b-797">Další informace naleznete v tématu <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-797">For more information, see <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span></span>

### <a name="enable-the-developer-exception-page"></a><span data-ttu-id="e6b6b-798">Povolení stránky výjimky pro vývojáře</span><span class="sxs-lookup"><span data-stu-id="e6b6b-798">Enable the Developer Exception Page</span></span>

<span data-ttu-id="e6b6b-799">`ASPNETCORE_ENVIRONMENT` [Proměnnou prostředí lze přidat do web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) pro spuštění aplikace ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-799">The `ASPNETCORE_ENVIRONMENT` [environment variable can be added to web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) to run the app in the Development environment.</span></span> <span data-ttu-id="e6b6b-800">Pokud prostředí není přepsáno při spuštění `UseEnvironment` aplikace tvůrcem hostitele, nastavení proměnné prostředí umožňuje, aby se [stránka výjimky pro vývojáře](xref:fundamentals/error-handling) zobrazila při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-800">As long as the environment isn't overridden in app startup by `UseEnvironment` on the host builder, setting the environment variable allows the [Developer Exception Page](xref:fundamentals/error-handling) to appear when the app is run.</span></span>

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

<span data-ttu-id="e6b6b-801">Nastavení proměnné prostředí `ASPNETCORE_ENVIRONMENT` pro se doporučuje pouze pro použití na pracovních a testovacích serverech, které nejsou vystaveny Internetu.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-801">Setting the environment variable for `ASPNETCORE_ENVIRONMENT` is only recommended for use on staging and testing servers that aren't exposed to the Internet.</span></span> <span data-ttu-id="e6b6b-802">Po odstranění potíží odeberte proměnnou prostředí ze souboru *web.config.*</span><span class="sxs-lookup"><span data-stu-id="e6b6b-802">Remove the environment variable from the *web.config* file after troubleshooting.</span></span> <span data-ttu-id="e6b6b-803">Informace o nastavení proměnných prostředí v *souboru web.config*naleznete v [tématu podřízený prvek environmentVariables aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).</span><span class="sxs-lookup"><span data-stu-id="e6b6b-803">For information on setting environment variables in *web.config*, see [environmentVariables child element of aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).</span></span>

### <a name="obtain-data-from-an-app"></a><span data-ttu-id="e6b6b-804">Získání dat z aplikace</span><span class="sxs-lookup"><span data-stu-id="e6b6b-804">Obtain data from an app</span></span>

<span data-ttu-id="e6b6b-805">Pokud je aplikace schopná reagovat na požadavky, získejte požadavek, připojení a další data z aplikace pomocí terminálového inline middlewaru.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-805">If an app is capable of responding to requests, obtain request, connection, and additional data from the app using terminal inline middleware.</span></span> <span data-ttu-id="e6b6b-806">Další informace a ukázkový <xref:test/troubleshoot#obtain-data-from-an-app>kód naleznete v tématu .</span><span class="sxs-lookup"><span data-stu-id="e6b6b-806">For more information and sample code, see <xref:test/troubleshoot#obtain-data-from-an-app>.</span></span>

### <a name="slow-or-hanging-app-iis"></a><span data-ttu-id="e6b6b-807">Pomalá nebo závěsná aplikace (IIS)</span><span class="sxs-lookup"><span data-stu-id="e6b6b-807">Slow or hanging app (IIS)</span></span>

<span data-ttu-id="e6b6b-808">*Výpis stavu systému* je snímek paměti systému a může pomoci určit příčinu selhání aplikace, selhání při spuštění nebo pomalé aplikace.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-808">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="e6b6b-809">Aplikace dojde k chybě nebo narazí na výjimku</span><span class="sxs-lookup"><span data-stu-id="e6b6b-809">App crashes or encounters an exception</span></span>

<span data-ttu-id="e6b6b-810">Získání a analýza výpisu z [programu Zasílání zpráv o chybách systému Windows (WER)](/windows/desktop/wer/windows-error-reporting):</span><span class="sxs-lookup"><span data-stu-id="e6b6b-810">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="e6b6b-811">Vytvořte složku, ve které `c:\dumps`budou uloženy soubory s výpisem stavu systému .</span><span class="sxs-lookup"><span data-stu-id="e6b6b-811">Create a folder to hold crash dump files at `c:\dumps`.</span></span> <span data-ttu-id="e6b6b-812">Fond aplikací musí mít přístup pro zápis do složky.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-812">The app pool must have write access to the folder.</span></span>
1. <span data-ttu-id="e6b6b-813">Spusťte [skript EnableDumps PowerShell](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1):</span><span class="sxs-lookup"><span data-stu-id="e6b6b-813">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1):</span></span>
   * <span data-ttu-id="e6b6b-814">Pokud aplikace používá [model hostování v procesu](xref:host-and-deploy/iis/index#in-process-hosting-model), spusťte skript pro *w3wp.exe*:</span><span class="sxs-lookup"><span data-stu-id="e6b6b-814">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\EnableDumps w3wp.exe c:\dumps
     ```

   * <span data-ttu-id="e6b6b-815">Pokud aplikace používá [mimoprocesový model hostování](xref:host-and-deploy/iis/index#out-of-process-hosting-model), spusťte skript pro *dotnet.exe*:</span><span class="sxs-lookup"><span data-stu-id="e6b6b-815">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\EnableDumps dotnet.exe c:\dumps
     ```

1. <span data-ttu-id="e6b6b-816">Spusťte aplikaci za podmínek, které způsobují selhání dojít.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-816">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="e6b6b-817">Po selhání spusťte [skript DisableDumps PowerShell](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1):</span><span class="sxs-lookup"><span data-stu-id="e6b6b-817">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1):</span></span>
   * <span data-ttu-id="e6b6b-818">Pokud aplikace používá [model hostování v procesu](xref:host-and-deploy/iis/index#in-process-hosting-model), spusťte skript pro *w3wp.exe*:</span><span class="sxs-lookup"><span data-stu-id="e6b6b-818">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\DisableDumps w3wp.exe
     ```

   * <span data-ttu-id="e6b6b-819">Pokud aplikace používá [mimoprocesový model hostování](xref:host-and-deploy/iis/index#out-of-process-hosting-model), spusťte skript pro *dotnet.exe*:</span><span class="sxs-lookup"><span data-stu-id="e6b6b-819">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\DisableDumps dotnet.exe
     ```

<span data-ttu-id="e6b6b-820">Po zhroucení aplikace a výpis kolekce je kompletní, aplikace je povoleno ukončit normálně.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-820">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="e6b6b-821">Skript PowerShell udává WER tak, aby shromažďoval až pět výpisů v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-821">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="e6b6b-822">Výpisy stavu systému může zabírají velké množství místa na disku (až několik gigabajtů každý).</span><span class="sxs-lookup"><span data-stu-id="e6b6b-822">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="e6b6b-823">Aplikace přestane reagovat, selže při spuštění nebo běží normálně</span><span class="sxs-lookup"><span data-stu-id="e6b6b-823">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="e6b6b-824">Když aplikace *přestane reagovat* (přestane reagovat, ale nezhroutí se), selže při spuštění nebo běží normálně, najdete v [tématu Soubory výpisu uživatelského režimu: Výběr nejvhodnějšího nástroje](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) pro výběr vhodného nástroje pro vytvoření výpisu.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-824">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="e6b6b-825">Analyzovat výpis</span><span class="sxs-lookup"><span data-stu-id="e6b6b-825">Analyze the dump</span></span>

<span data-ttu-id="e6b6b-826">Výpis lze analyzovat pomocí několika přístupů.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-826">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="e6b6b-827">Další informace naleznete [v tématu Analýza souboru s výpisem stavu uživatelského režimu](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span><span class="sxs-lookup"><span data-stu-id="e6b6b-827">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="clear-package-caches"></a><span data-ttu-id="e6b6b-828">Vymazat mezipaměti balíčků</span><span class="sxs-lookup"><span data-stu-id="e6b6b-828">Clear package caches</span></span>

<span data-ttu-id="e6b6b-829">Funkční aplikace může selhat ihned po upgradu sady .NET Core SDK ve vývojovém počítači nebo změně verze balíčku v rámci aplikace.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-829">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="e6b6b-830">V některých případech nesouvislé balíčky může přerušit aplikaci při provádění hlavních upgradů.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-830">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="e6b6b-831">Většinu těchto problémů lze opravit podle následujících pokynů:</span><span class="sxs-lookup"><span data-stu-id="e6b6b-831">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="e6b6b-832">Odstraňte složky *bin* a *obj.*</span><span class="sxs-lookup"><span data-stu-id="e6b6b-832">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="e6b6b-833">Vymazat mezipaměti balíčku spuštěním [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) z příkazového prostředí.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-833">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="e6b6b-834">Vymazání mezipaměti balíčků lze také provést pomocí nástroje [nuget.exe](https://www.nuget.org/downloads) a spuštěnípříkazu `nuget locals all -clear`.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-834">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="e6b6b-835">*nuget.exe* není přibalená instalace s operačním systémem Windows desktop a musí být získány odděleně od [webu NuGet](https://www.nuget.org/downloads).</span><span class="sxs-lookup"><span data-stu-id="e6b6b-835">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="e6b6b-836">Obnovte a znovu vytvořte projekt.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-836">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="e6b6b-837">Před opětovnou nasazením aplikace odstraňte všechny soubory ve složce nasazení na serveru.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-837">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e6b6b-838">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="e6b6b-838">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:fundamentals/error-handling>
* <xref:host-and-deploy/aspnet-core-module>

### <a name="azure-documentation"></a><span data-ttu-id="e6b6b-839">Dokumentace k Azure</span><span class="sxs-lookup"><span data-stu-id="e6b6b-839">Azure documentation</span></span>

* [<span data-ttu-id="e6b6b-840">Application Insights pro ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e6b6b-840">Application Insights for ASP.NET Core</span></span>](/azure/application-insights/app-insights-asp-net-core)
* [<span data-ttu-id="e6b6b-841">Část Řešení potíží s webovou aplikací ve službě Azure App Service ve vzdáleném ladění webových aplikací pomocí Visual Studia</span><span class="sxs-lookup"><span data-stu-id="e6b6b-841">Remote debugging web apps section of Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug)
* [<span data-ttu-id="e6b6b-842">Přehled diagnostiky služby Azure App Service</span><span class="sxs-lookup"><span data-stu-id="e6b6b-842">Azure App Service diagnostics overview</span></span>](/azure/app-service/app-service-diagnostics)
* [<span data-ttu-id="e6b6b-843">Postup: Monitorování aplikací ve službě Azure App Service</span><span class="sxs-lookup"><span data-stu-id="e6b6b-843">How to: Monitor Apps in Azure App Service</span></span>](/azure/app-service/web-sites-monitor)
* [<span data-ttu-id="e6b6b-844">Poradce při potížích s webovou aplikací ve službě Azure App Service pomocí Visual Studia</span><span class="sxs-lookup"><span data-stu-id="e6b6b-844">Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [<span data-ttu-id="e6b6b-845">Poradce při potížích s chybami HTTP "502 chybná brána" a "503 služba není k dispozici" ve svých webových aplikacích Azure</span><span class="sxs-lookup"><span data-stu-id="e6b6b-845">Troubleshoot HTTP errors of "502 bad gateway" and "503 service unavailable" in your Azure web apps</span></span>](/azure/app-service/app-service-web-troubleshoot-http-502-http-503)
* [<span data-ttu-id="e6b6b-846">Řešení potíží s výkonem pomalých webových aplikací ve službě Azure App Service</span><span class="sxs-lookup"><span data-stu-id="e6b6b-846">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="e6b6b-847">Časté otázky k výkonu aplikací pro webové aplikace v Azure</span><span class="sxs-lookup"><span data-stu-id="e6b6b-847">Application performance FAQs for Web Apps in Azure</span></span>](/azure/app-service/app-service-web-availability-performance-application-issues-faq)
* [<span data-ttu-id="e6b6b-848">Azure Web App izolovaného prostoru (omezení spuštění služby App Service)</span><span class="sxs-lookup"><span data-stu-id="e6b6b-848">Azure Web App sandbox (App Service runtime execution limitations)</span></span>](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)
* [<span data-ttu-id="e6b6b-849">Azure Friday: Azure App Service Diagnostic and Troubleshooting Experience (12minutové video)</span><span class="sxs-lookup"><span data-stu-id="e6b6b-849">Azure Friday: Azure App Service Diagnostic and Troubleshooting Experience (12-minute video)</span></span>](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)

### <a name="visual-studio-documentation"></a><span data-ttu-id="e6b6b-850">Dokumentace sady Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e6b6b-850">Visual Studio documentation</span></span>

* [<span data-ttu-id="e6b6b-851">Vzdálené ladění ASP.NET jádro ve službě IIS v Azure ve Visual Studiu 2017</span><span class="sxs-lookup"><span data-stu-id="e6b6b-851">Remote Debug ASP.NET Core on IIS in Azure in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-azure)
* [<span data-ttu-id="e6b6b-852">Vzdálené ladění ASP.NET jádra ve vzdáleném počítači služby IIS v sadě Visual Studio 2017</span><span class="sxs-lookup"><span data-stu-id="e6b6b-852">Remote Debug ASP.NET Core on a Remote IIS Computer in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-aspnet-on-a-remote-iis-computer)
* [<span data-ttu-id="e6b6b-853">Informace o ladění pomocí sady Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e6b6b-853">Learn to debug using Visual Studio</span></span>](/visualstudio/debugger/getting-started-with-the-debugger)

### <a name="visual-studio-code-documentation"></a><span data-ttu-id="e6b6b-854">Dokumentace kódu visual studia</span><span class="sxs-lookup"><span data-stu-id="e6b6b-854">Visual Studio Code documentation</span></span>

* [<span data-ttu-id="e6b6b-855">Ladění pomocí kódu sady Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e6b6b-855">Debugging with Visual Studio Code</span></span>](https://code.visualstudio.com/docs/editor/debugging)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="e6b6b-856">Tento článek obsahuje informace o běžných chybách při spuštění aplikace a pokyny, jak diagnostikovat chyby při nasazení aplikace do služby Azure App Service nebo IIS:</span><span class="sxs-lookup"><span data-stu-id="e6b6b-856">This article provides information on common app startup errors and instructions on how to diagnose errors when an app is deployed to Azure App Service or IIS:</span></span>

[<span data-ttu-id="e6b6b-857">Chyby při spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="e6b6b-857">App startup errors</span></span>](#app-startup-errors)  
<span data-ttu-id="e6b6b-858">Vysvětluje běžné scénáře stavového kódu HTTP při spuštění.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-858">Explains common startup HTTP status code scenarios.</span></span>

[<span data-ttu-id="e6b6b-859">Poradce při potížích se službou Azure App Service</span><span class="sxs-lookup"><span data-stu-id="e6b6b-859">Troubleshoot on Azure App Service</span></span>](#troubleshoot-on-azure-app-service)  
<span data-ttu-id="e6b6b-860">Poskytuje rady pro řešení potíží pro aplikace nasazené do služby Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-860">Provides troubleshooting advice for apps deployed to Azure App Service.</span></span>

[<span data-ttu-id="e6b6b-861">Odstraňování potíží ve službě IIS</span><span class="sxs-lookup"><span data-stu-id="e6b6b-861">Troubleshoot on IIS</span></span>](#troubleshoot-on-iis)  
<span data-ttu-id="e6b6b-862">Poskytuje rady pro řešení potíží s aplikacemi nasazenými do služby IIS nebo spuštěných místně ve službě IIS Express.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-862">Provides troubleshooting advice for apps deployed to IIS or running on IIS Express locally.</span></span> <span data-ttu-id="e6b6b-863">Pokyny platí pro nasazení plochy systému Windows Server i Windows.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-863">The guidance applies to both Windows Server and Windows desktop deployments.</span></span>

[<span data-ttu-id="e6b6b-864">Vymazat mezipaměti balíčků</span><span class="sxs-lookup"><span data-stu-id="e6b6b-864">Clear package caches</span></span>](#clear-package-caches)  
<span data-ttu-id="e6b6b-865">Vysvětluje, co dělat, když nesouvislé balíčky přerušit aplikaci při provádění hlavních upgradů nebo změny verze balíčku.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-865">Explains what to do when incoherent packages break an app when performing major upgrades or changing package versions.</span></span>

[<span data-ttu-id="e6b6b-866">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="e6b6b-866">Additional resources</span></span>](#additional-resources)  
<span data-ttu-id="e6b6b-867">Obsahuje další témata pro řešení potíží.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-867">Lists additional troubleshooting topics.</span></span>

## <a name="app-startup-errors"></a><span data-ttu-id="e6b6b-868">Chyby při spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="e6b6b-868">App startup errors</span></span>

<span data-ttu-id="e6b6b-869">V sadě Visual Studio, ASP.NET projektu Core výchozí [iis express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hostování během ladění.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-869">In Visual Studio, an ASP.NET Core project defaults to [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hosting during debugging.</span></span> <span data-ttu-id="e6b6b-870">*502.5 Selhání procesu,* ke kterému dochází při místním ladění lze diagnostikovat pomocí rady v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-870">A *502.5 Process Failure* that occurs when debugging locally can be diagnosed using the advice in this topic.</span></span>

### <a name="40314-forbidden"></a><span data-ttu-id="e6b6b-871">403.14 Zakázáno</span><span class="sxs-lookup"><span data-stu-id="e6b6b-871">403.14 Forbidden</span></span>

<span data-ttu-id="e6b6b-872">Aplikace se nespustí.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-872">The app fails to start.</span></span> <span data-ttu-id="e6b6b-873">Je zaznamenána následující chyba:</span><span class="sxs-lookup"><span data-stu-id="e6b6b-873">The following error is logged:</span></span>

```
The Web server is configured to not list the contents of this directory.
```

<span data-ttu-id="e6b6b-874">Chyba je obvykle způsobena přerušeným nasazením v hostitelském systému, který zahrnuje některý z následujících scénářů:</span><span class="sxs-lookup"><span data-stu-id="e6b6b-874">The error is usually caused by a broken deployment on the hosting system, which includes any of the following scenarios:</span></span>

* <span data-ttu-id="e6b6b-875">Aplikace je nasazena do nesprávné složky v hostitelském systému.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-875">The app is deployed to the wrong folder on the hosting system.</span></span>
* <span data-ttu-id="e6b6b-876">Procesu nasazení se nepodařilo přesunout všechny soubory a složky aplikace do složky nasazení v hostitelském systému.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-876">The deployment process failed to move all of the app's files and folders to the deployment folder on the hosting system.</span></span>
* <span data-ttu-id="e6b6b-877">Soubor *web.config* chybí v nasazení nebo je obsah souboru *web.config* poškozen.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-877">The *web.config* file is missing from the deployment, or the *web.config* file contents are malformed.</span></span>

<span data-ttu-id="e6b6b-878">Proveďte následující kroky:</span><span class="sxs-lookup"><span data-stu-id="e6b6b-878">Perform the following steps:</span></span>

1. <span data-ttu-id="e6b6b-879">Odstraňte všechny soubory a složky ze složky nasazení v hostitelském systému.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-879">Delete all of the files and folders from the deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="e6b6b-880">Znovu nasadit obsah složky *publikování* aplikace do hostitelského systému pomocí normální metody nasazení, jako je Visual Studio, PowerShell nebo ruční nasazení:</span><span class="sxs-lookup"><span data-stu-id="e6b6b-880">Redeploy the contents of the app's *publish* folder to the hosting system using your normal method of deployment, such as Visual Studio, PowerShell, or manual deployment:</span></span>
   * <span data-ttu-id="e6b6b-881">Zkontrolujte, zda je soubor *web.config* v nasazení přítomen a zda je jeho obsah správný.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-881">Confirm that the *web.config* file is present in the deployment and that its contents are correct.</span></span>
   * <span data-ttu-id="e6b6b-882">Při hostování ve službě Azure App Service zkontrolujte, že se aplikace nasadí do `D:\home\site\wwwroot` složky.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-882">When hosting on Azure App Service, confirm that the app is deployed to the `D:\home\site\wwwroot` folder.</span></span>
   * <span data-ttu-id="e6b6b-883">Pokud je aplikace hostována službou IIS, zkontrolujte, zda je aplikace nasazena do **fyzické cesty** služby IIS zobrazené v **základním nastavení** **správce služby IIS**.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-883">When the app is hosted by IIS, confirm that the app is deployed to the IIS **Physical path** shown in **IIS Manager**'s **Basic Settings**.</span></span>
1. <span data-ttu-id="e6b6b-884">Zkontrolujte, zda jsou všechny soubory a složky aplikace nasazeny porovnáním nasazení v hostitelském systému s obsahem složky *publikování* projektu.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-884">Confirm that all of the app's files and folders are deployed by comparing the deployment on the hosting system to the contents of the project's *publish* folder.</span></span>

<span data-ttu-id="e6b6b-885">Další informace o rozložení publikované aplikace ASP.NET <xref:host-and-deploy/directory-structure>Core najdete v tématu .</span><span class="sxs-lookup"><span data-stu-id="e6b6b-885">For more information on the layout of a published ASP.NET Core app, see <xref:host-and-deploy/directory-structure>.</span></span> <span data-ttu-id="e6b6b-886">Další informace o souboru *web.config* naleznete v tématu <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-886">For more information on the *web.config* file, see <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>.</span></span>

### <a name="500-internal-server-error"></a><span data-ttu-id="e6b6b-887">500 – Interní chyba serveru</span><span class="sxs-lookup"><span data-stu-id="e6b6b-887">500 Internal Server Error</span></span>

<span data-ttu-id="e6b6b-888">Aplikace se spustí, ale chyba zabrání serveru v plnění požadavku.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-888">The app starts, but an error prevents the server from fulfilling the request.</span></span>

<span data-ttu-id="e6b6b-889">K této chybě dochází v kódu aplikace při spuštění nebo při vytváření odpovědi.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-889">This error occurs within the app's code during startup or while creating a response.</span></span> <span data-ttu-id="e6b6b-890">Odpověď může obsahovat žádný obsah nebo odpověď může zobrazit jako *500 Vnitřní chyba serveru* v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-890">The response may contain no content, or the response may appear as a *500 Internal Server Error* in the browser.</span></span> <span data-ttu-id="e6b6b-891">Protokol událostí aplikace obvykle uvádí, že aplikace byla spuštěna normálně.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-891">The Application Event Log usually states that the app started normally.</span></span> <span data-ttu-id="e6b6b-892">Z pohledu serveru je to správné.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-892">From the server's perspective, that's correct.</span></span> <span data-ttu-id="e6b6b-893">Aplikace se spustila, ale nemůže generovat platnou odpověď.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-893">The app did start, but it can't generate a valid response.</span></span> <span data-ttu-id="e6b6b-894">Spusťte aplikaci na příkazovém řádku na serveru nebo povolte protokol stdout ASP.NET Core Module k řešení problému.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-894">Run the app at a command prompt on the server or enable the ASP.NET Core Module stdout log to troubleshoot the problem.</span></span>

### <a name="5025-process-failure"></a><span data-ttu-id="e6b6b-895">502.5 – Selhání procesu</span><span class="sxs-lookup"><span data-stu-id="e6b6b-895">502.5 Process Failure</span></span>

<span data-ttu-id="e6b6b-896">Pracovní proces se nezdaří.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-896">The worker process fails.</span></span> <span data-ttu-id="e6b6b-897">Aplikace se nespustí.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-897">The app doesn't start.</span></span>

<span data-ttu-id="e6b6b-898">Základní [modul ASP.NET](xref:host-and-deploy/aspnet-core-module) se pokusí spustit pracovní proces, ale nepodaří se jej spustit.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-898">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the worker process but it fails to start.</span></span> <span data-ttu-id="e6b6b-899">Příčinu selhání spuštění procesu lze obvykle určit z položek v protokolu událostí aplikace a ASP.NET protokolu stdout základního modulu.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-899">The cause of a process startup failure can usually be determined from entries in the Application Event Log and the ASP.NET Core Module stdout log.</span></span>

<span data-ttu-id="e6b6b-900">Obvyklá podmínka selhání je, že aplikace je nesprávně nakonfigurovaná kvůli cílení na verzi sdíleného rozhraní ASP.NET Core, která není k dispozici.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-900">A common failure condition is the app is misconfigured due to targeting a version of the ASP.NET Core shared framework that isn't present.</span></span> <span data-ttu-id="e6b6b-901">Zkontrolujte, které verze ASP.NET sdíleného rozhraní Core jsou nainstalovány v cílovém počítači.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-901">Check which versions of the ASP.NET Core shared framework are installed on the target machine.</span></span> <span data-ttu-id="e6b6b-902">Sdílená *architektura* je sada sestavení (*soubory DLL),* které jsou nainstalovány v počítači a odkazovány metabalíčkem, například `Microsoft.AspNetCore.App`.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-902">The *shared framework* is the set of assemblies (*.dll* files) that are installed on the machine and referenced by a metapackage such as `Microsoft.AspNetCore.App`.</span></span> <span data-ttu-id="e6b6b-903">Odkaz na metabalíček může určit minimální požadovanou verzi.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-903">The metapackage reference can specify a minimum required version.</span></span> <span data-ttu-id="e6b6b-904">Další informace naleznete [v tématu sdílený rámec](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span><span class="sxs-lookup"><span data-stu-id="e6b6b-904">For more information, see [The shared framework](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span></span>

<span data-ttu-id="e6b6b-905">Chybová stránka *502.5 Process Failure* je vrácena, když selhání konfigurace hostitele nebo aplikace způsobí selhání pracovního procesu:</span><span class="sxs-lookup"><span data-stu-id="e6b6b-905">The *502.5 Process Failure* error page is returned when a hosting or app misconfiguration causes the worker process to fail:</span></span>

### <a name="failed-to-start-application-errorcode-0x800700c1"></a><span data-ttu-id="e6b6b-906">Spuštění aplikace se nezdařilo (ErrorCode '0x800700c1')</span><span class="sxs-lookup"><span data-stu-id="e6b6b-906">Failed to start application (ErrorCode '0x800700c1')</span></span>

```
EventID: 1010
Source: IIS AspNetCore Module V2
Failed to start application '/LM/W3SVC/6/ROOT/', ErrorCode '0x800700c1'.
```

<span data-ttu-id="e6b6b-907">Spuštění aplikace se nezdařilo, protože nebylo možné načíst sestavení aplikace (*DLL*).</span><span class="sxs-lookup"><span data-stu-id="e6b6b-907">The app failed to start because the app's assembly (*.dll*) couldn't be loaded.</span></span>

<span data-ttu-id="e6b6b-908">K této chybě dochází, pokud je neodpovídající bitness mezi publikovanou aplikací a w3wp/iisexpress procesu.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-908">This error occurs when there's a bitness mismatch between the published app and the w3wp/iisexpress process.</span></span>

<span data-ttu-id="e6b6b-909">Zkontrolujte, zda je 32bitové nastavení fondu aplikací správné:</span><span class="sxs-lookup"><span data-stu-id="e6b6b-909">Confirm that the app pool's 32-bit setting is correct:</span></span>

1. <span data-ttu-id="e6b6b-910">Vyberte fond aplikací ve **fondech aplikací**Správce služby IIS .</span><span class="sxs-lookup"><span data-stu-id="e6b6b-910">Select the app pool in IIS Manager's **Application Pools**.</span></span>
1. <span data-ttu-id="e6b6b-911">V části Upravit fond aplikací v panelu **Akce** vyberte **Upřesnit nastavení** v části Upravit **fond aplikací.**</span><span class="sxs-lookup"><span data-stu-id="e6b6b-911">Select **Advanced Settings** under **Edit Application Pool** in the **Actions** panel.</span></span>
1. <span data-ttu-id="e6b6b-912">Nastavit **povolit 32bitové aplikace**:</span><span class="sxs-lookup"><span data-stu-id="e6b6b-912">Set **Enable 32-Bit Applications**:</span></span>
   * <span data-ttu-id="e6b6b-913">Pokud nasazujete 32bitovou (x86) `True`aplikaci, nastavte hodnotu na .</span><span class="sxs-lookup"><span data-stu-id="e6b6b-913">If deploying a 32-bit (x86) app, set the value to `True`.</span></span>
   * <span data-ttu-id="e6b6b-914">Pokud nasazujete 64bitovou (x64) `False`aplikaci, nastavte hodnotu na .</span><span class="sxs-lookup"><span data-stu-id="e6b6b-914">If deploying a 64-bit (x64) app, set the value to `False`.</span></span>

<span data-ttu-id="e6b6b-915">Zkontrolujte, zda v souboru `<Platform>` projektu není konflikt mezi vlastností MSBuild a publikovanou bitness aplikace.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-915">Confirm that there isn't a conflict between a `<Platform>` MSBuild property in the project file and the published bitness of the app.</span></span>

### <a name="connection-reset"></a><span data-ttu-id="e6b6b-916">Resetování připojení</span><span class="sxs-lookup"><span data-stu-id="e6b6b-916">Connection reset</span></span>

<span data-ttu-id="e6b6b-917">Pokud dojde k chybě po odeslání záhlaví, je příliš pozdě na to, aby server odeslal **chybu interního serveru 500,** když dojde k chybě.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-917">If an error occurs after the headers are sent, it's too late for the server to send a **500 Internal Server Error** when an error occurs.</span></span> <span data-ttu-id="e6b6b-918">K tomu často dochází, když dojde k chybě během serializace složitých objektů pro odpověď.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-918">This often happens when an error occurs during the serialization of complex objects for a response.</span></span> <span data-ttu-id="e6b6b-919">Tento typ chyby se zobrazí jako chyba *obnovení připojení* v klientovi.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-919">This type of error appears as a *connection reset* error on the client.</span></span> <span data-ttu-id="e6b6b-920">[Protokolování aplikací](xref:fundamentals/logging/index) může pomoci při řešení těchto typů chyb.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-920">[Application logging](xref:fundamentals/logging/index) can help troubleshoot these types of errors.</span></span>

### <a name="default-startup-limits"></a><span data-ttu-id="e6b6b-921">Výchozí limity pro spuštění</span><span class="sxs-lookup"><span data-stu-id="e6b6b-921">Default startup limits</span></span>

<span data-ttu-id="e6b6b-922">[Základní modul ASP.NET](xref:host-and-deploy/aspnet-core-module) je nakonfigurován s výchozím *spouštěcím limitem* 120 sekund.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-922">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) is configured with a default *startupTimeLimit* of 120 seconds.</span></span> <span data-ttu-id="e6b6b-923">Pokud zůstane na výchozí hodnotu, aplikace může trvat až dvě minuty ke spuštění, než modul zaznamená selhání procesu.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-923">When left at the default value, an app may take up to two minutes to start before the module logs a process failure.</span></span> <span data-ttu-id="e6b6b-924">Informace o konfiguraci modulu naleznete v [tématu Atributy prvku aspNetCore](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span><span class="sxs-lookup"><span data-stu-id="e6b6b-924">For information on configuring the module, see [Attributes of the aspNetCore element](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span></span>

## <a name="troubleshoot-on-azure-app-service"></a><span data-ttu-id="e6b6b-925">Poradce při potížích se službou Azure App Service</span><span class="sxs-lookup"><span data-stu-id="e6b6b-925">Troubleshoot on Azure App Service</span></span>

[!INCLUDE [Azure App Service Preview Notice](~/includes/azure-apps-preview-notice.md)]

### <a name="application-event-log-azure-app-service"></a><span data-ttu-id="e6b6b-926">Protokol událostí aplikace (služba Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="e6b6b-926">Application Event Log (Azure App Service)</span></span>

<span data-ttu-id="e6b6b-927">Chcete-li získat přístup k protokolu událostí aplikace, použijte okno **Diagnostikovat a vyřešit problémy** na webu Azure Portal:</span><span class="sxs-lookup"><span data-stu-id="e6b6b-927">To access the Application Event Log, use the **Diagnose and solve problems** blade in the Azure portal:</span></span>

1. <span data-ttu-id="e6b6b-928">Na webu Azure Portal otevřete aplikaci ve **Službách aplikací**.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-928">In the Azure portal, open the app in **App Services**.</span></span>
1. <span data-ttu-id="e6b6b-929">Vyberte **Diagnostikovat a řešit problémy**.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-929">Select **Diagnose and solve problems**.</span></span>
1. <span data-ttu-id="e6b6b-930">Vyberte nadpis **Diagnostické nástroje.**</span><span class="sxs-lookup"><span data-stu-id="e6b6b-930">Select the **Diagnostic Tools** heading.</span></span>
1. <span data-ttu-id="e6b6b-931">V části **Nástroje podpory**vyberte tlačítko **Události aplikace.**</span><span class="sxs-lookup"><span data-stu-id="e6b6b-931">Under **Support Tools**, select the **Application Events** button.</span></span>
1. <span data-ttu-id="e6b6b-932">Prohlédněte si nejnovější chybu poskytovanou položkou *AspNetCoreModule* nebo *IIS AspNetCoreModule V2* ve sloupci **Zdroj.**</span><span class="sxs-lookup"><span data-stu-id="e6b6b-932">Examine the latest error provided by the *IIS AspNetCoreModule* or *IIS AspNetCoreModule V2* entry in the **Source** column.</span></span>

<span data-ttu-id="e6b6b-933">Alternativou k použití **funkce Diagnostika a řešení problémů** je prozkoumat soubor protokolu událostí aplikace přímo pomocí [kudu](https://github.com/projectkudu/kudu/wiki):</span><span class="sxs-lookup"><span data-stu-id="e6b6b-933">An alternative to using the **Diagnose and solve problems** blade is to examine the Application Event Log file directly using [Kudu](https://github.com/projectkudu/kudu/wiki):</span></span>

1. <span data-ttu-id="e6b6b-934">Otevřete **rozšířené nástroje** v oblasti **vývojových nástrojů.**</span><span class="sxs-lookup"><span data-stu-id="e6b6b-934">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="e6b6b-935">Vyberte tlačítko \*\*Přejít.&rarr; \*\*</span><span class="sxs-lookup"><span data-stu-id="e6b6b-935">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="e6b6b-936">Konzole Kudu se otevře v novém kartě prohlížeče nebo v okně.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-936">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="e6b6b-937">Pomocí navigačního panelu v horní části stránky otevřete **konzolu ladění** a vyberte **možnost CMD**.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-937">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="e6b6b-938">Otevřete složku **LogFiles.**</span><span class="sxs-lookup"><span data-stu-id="e6b6b-938">Open the **LogFiles** folder.</span></span>
1. <span data-ttu-id="e6b6b-939">Vyberte ikonu tužky vedle souboru *eventlog.xml.*</span><span class="sxs-lookup"><span data-stu-id="e6b6b-939">Select the pencil icon next to the *eventlog.xml* file.</span></span>
1. <span data-ttu-id="e6b6b-940">Prohlédněte si protokol.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-940">Examine the log.</span></span> <span data-ttu-id="e6b6b-941">Přejděte do dolní části protokolu zobrazíte nejnovější události.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-941">Scroll to the bottom of the log to see the most recent events.</span></span>

### <a name="run-the-app-in-the-kudu-console"></a><span data-ttu-id="e6b6b-942">Spuštění aplikace v konzoli Kudu</span><span class="sxs-lookup"><span data-stu-id="e6b6b-942">Run the app in the Kudu console</span></span>

<span data-ttu-id="e6b6b-943">Mnoho chyb při spuštění nevytváří užitečné informace v protokolu událostí aplikace.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-943">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="e6b6b-944">Aplikaci můžete spustit v konzole vzdáleného spuštění [Kudu](https://github.com/projectkudu/kudu/wiki) a zjistit chybu:</span><span class="sxs-lookup"><span data-stu-id="e6b6b-944">You can run the app in the [Kudu](https://github.com/projectkudu/kudu/wiki) Remote Execution Console to discover the error:</span></span>

1. <span data-ttu-id="e6b6b-945">Otevřete **rozšířené nástroje** v oblasti **vývojových nástrojů.**</span><span class="sxs-lookup"><span data-stu-id="e6b6b-945">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="e6b6b-946">Vyberte tlačítko \*\*Přejít.&rarr; \*\*</span><span class="sxs-lookup"><span data-stu-id="e6b6b-946">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="e6b6b-947">Konzole Kudu se otevře v novém kartě prohlížeče nebo v okně.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-947">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="e6b6b-948">Pomocí navigačního panelu v horní části stránky otevřete **konzolu ladění** a vyberte **možnost CMD**.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-948">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>

#### <a name="test-a-32-bit-x86-app"></a><span data-ttu-id="e6b6b-949">Testování 32bitové aplikace (x86)</span><span class="sxs-lookup"><span data-stu-id="e6b6b-949">Test a 32-bit (x86) app</span></span>

<span data-ttu-id="e6b6b-950">**Aktuální verze**</span><span class="sxs-lookup"><span data-stu-id="e6b6b-950">**Current release**</span></span>

1. `cd d:\home\site\wwwroot`
1. <span data-ttu-id="e6b6b-951">Spusťte aplikaci:</span><span class="sxs-lookup"><span data-stu-id="e6b6b-951">Run the app:</span></span>
   * <span data-ttu-id="e6b6b-952">Pokud je aplikace [nasazení závislé na rámci](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span><span class="sxs-lookup"><span data-stu-id="e6b6b-952">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

     ```dotnetcli
     dotnet .\{ASSEMBLY NAME}.dll
     ```

   * <span data-ttu-id="e6b6b-953">Pokud se jedná o [samostatné nasazení](/dotnet/core/deploying/#self-contained-deployments-scd)aplikace :</span><span class="sxs-lookup"><span data-stu-id="e6b6b-953">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

     ```console
     {ASSEMBLY NAME}.exe
     ```

<span data-ttu-id="e6b6b-954">Výstup konzoly z aplikace, v němž se zobrazují všechny chyby, je předán do konzoly Kudu.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-954">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="e6b6b-955">**Nasazení závislé na rozhraní spuštěné ve verzi preview**</span><span class="sxs-lookup"><span data-stu-id="e6b6b-955">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="e6b6b-956">*Vyžaduje instalaci rozšíření webu ASP.NET Core {VERSION} (x86).*</span><span class="sxs-lookup"><span data-stu-id="e6b6b-956">*Requires installing the ASP.NET Core {VERSION} (x86) Runtime site extension.*</span></span>

1. <span data-ttu-id="e6b6b-957">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32`(`{X.Y}` je runtime verze)</span><span class="sxs-lookup"><span data-stu-id="e6b6b-957">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="e6b6b-958">Spusťte aplikaci: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="e6b6b-958">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="e6b6b-959">Výstup konzoly z aplikace, v němž se zobrazují všechny chyby, je předán do konzoly Kudu.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-959">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

#### <a name="test-a-64-bit-x64-app"></a><span data-ttu-id="e6b6b-960">Testování 64bitové aplikace (x64)</span><span class="sxs-lookup"><span data-stu-id="e6b6b-960">Test a 64-bit (x64) app</span></span>

<span data-ttu-id="e6b6b-961">**Aktuální verze**</span><span class="sxs-lookup"><span data-stu-id="e6b6b-961">**Current release**</span></span>

* <span data-ttu-id="e6b6b-962">Pokud je aplikace 64bitové (x64) [nasazení závislé na rozhraní](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span><span class="sxs-lookup"><span data-stu-id="e6b6b-962">If the app is a 64-bit (x64) [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>
  1. `cd D:\Program Files\dotnet`
  1. <span data-ttu-id="e6b6b-963">Spusťte aplikaci: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="e6b6b-963">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>
* <span data-ttu-id="e6b6b-964">Pokud se jedná o [samostatné nasazení](/dotnet/core/deploying/#self-contained-deployments-scd)aplikace :</span><span class="sxs-lookup"><span data-stu-id="e6b6b-964">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>
  1. `cd D:\home\site\wwwroot`
  1. <span data-ttu-id="e6b6b-965">Spusťte aplikaci: `{ASSEMBLY NAME}.exe`</span><span class="sxs-lookup"><span data-stu-id="e6b6b-965">Run the app: `{ASSEMBLY NAME}.exe`</span></span>

<span data-ttu-id="e6b6b-966">Výstup konzoly z aplikace, v němž se zobrazují všechny chyby, je předán do konzoly Kudu.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-966">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="e6b6b-967">**Nasazení závislé na rozhraní spuštěné ve verzi preview**</span><span class="sxs-lookup"><span data-stu-id="e6b6b-967">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="e6b6b-968">*Vyžaduje instalaci rozšíření webu ASP.NET Core {VERSION} (x64).*</span><span class="sxs-lookup"><span data-stu-id="e6b6b-968">*Requires installing the ASP.NET Core {VERSION} (x64) Runtime site extension.*</span></span>

1. <span data-ttu-id="e6b6b-969">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64`(`{X.Y}` je runtime verze)</span><span class="sxs-lookup"><span data-stu-id="e6b6b-969">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="e6b6b-970">Spusťte aplikaci: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="e6b6b-970">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="e6b6b-971">Výstup konzoly z aplikace, v němž se zobrazují všechny chyby, je předán do konzoly Kudu.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-971">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

### <a name="aspnet-core-module-stdout-log-azure-app-service"></a><span data-ttu-id="e6b6b-972">ASP.NET protokol stdout modulu (služba Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="e6b6b-972">ASP.NET Core Module stdout log (Azure App Service)</span></span>

<span data-ttu-id="e6b6b-973">Protokol stdout ASP.NET Core Module často zaznamenává užitečné chybové zprávy, které nebyly nalezeny v protokolu událostí aplikace.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-973">The ASP.NET Core Module stdout log often records useful error messages not found in the Application Event Log.</span></span> <span data-ttu-id="e6b6b-974">Povolení a zobrazení protokolů stdout:</span><span class="sxs-lookup"><span data-stu-id="e6b6b-974">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="e6b6b-975">Přejděte na okno **Diagnostikovat a vyřešit problémy** na webu Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-975">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="e6b6b-976">V **části SELECT PROBLEM CATEGORY**vyberte tlačítko Web App **Down** .</span><span class="sxs-lookup"><span data-stu-id="e6b6b-976">Under **SELECT PROBLEM CATEGORY**, select the **Web App Down** button.</span></span>
1. <span data-ttu-id="e6b6b-977">V části **Navrhovaná řešení** > **Povolit přesměrování protokolu Stdout**vyberte tlačítko **pro otevření konzoly Kudu a upravte web.Config**.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-977">Under **Suggested Solutions** > **Enable Stdout Log Redirection**, select the button to **Open Kudu Console to edit Web.Config**.</span></span>
1. <span data-ttu-id="e6b6b-978">V **diagnostické konzole**Kudu otevřete složky webu **site** > **wwwroot**.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-978">In the Kudu **Diagnostic Console**, open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="e6b6b-979">Posunutím dolů zobrazíte soubor *web.config* v dolní části seznamu.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-979">Scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="e6b6b-980">Klikněte na ikonu tužky vedle souboru *web.config.*</span><span class="sxs-lookup"><span data-stu-id="e6b6b-980">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="e6b6b-981">Nastavte **stdoutLogEnabled** na `true` cestu **stdoutLogFile** `\\?\%home%\LogFiles\stdout`a změňte ji na: .</span><span class="sxs-lookup"><span data-stu-id="e6b6b-981">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="e6b6b-982">Vyberte **Uložit,** chcete-li uložit aktualizovaný soubor *web.config.*</span><span class="sxs-lookup"><span data-stu-id="e6b6b-982">Select **Save** to save the updated *web.config* file.</span></span>
1. <span data-ttu-id="e6b6b-983">Požádejte o to do aplikace.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-983">Make a request to the app.</span></span>
1. <span data-ttu-id="e6b6b-984">Vraťte se na Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-984">Return to the Azure portal.</span></span> <span data-ttu-id="e6b6b-985">Vyberte okno **Pokročilé nástroje** v oblasti **NÁSTROJE VÝVOJE.**</span><span class="sxs-lookup"><span data-stu-id="e6b6b-985">Select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="e6b6b-986">Vyberte tlačítko \*\*Přejít.&rarr; \*\*</span><span class="sxs-lookup"><span data-stu-id="e6b6b-986">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="e6b6b-987">Konzole Kudu se otevře v novém kartě prohlížeče nebo v okně.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-987">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="e6b6b-988">Pomocí navigačního panelu v horní části stránky otevřete **konzolu ladění** a vyberte **možnost CMD**.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-988">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="e6b6b-989">Vyberte složku **LogFiles.**</span><span class="sxs-lookup"><span data-stu-id="e6b6b-989">Select the **LogFiles** folder.</span></span>
1. <span data-ttu-id="e6b6b-990">Zkontrolujte sloupec **Změněno** a vyberte ikonu tužky a upravte protokol stdout s nejnovějším datem změny.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-990">Inspect the **Modified** column and select the pencil icon to edit the stdout log with the latest modification date.</span></span>
1. <span data-ttu-id="e6b6b-991">Po otevření souboru protokolu se zobrazí chyba.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-991">When the log file opens, the error is displayed.</span></span>

<span data-ttu-id="e6b6b-992">Zakažte protokolování stdout po dokončení řešení potíží:</span><span class="sxs-lookup"><span data-stu-id="e6b6b-992">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="e6b6b-993">V diagnostické **konzole**Kudu se vraťte na **web** > **wwwroot** cesty a odhalte soubor *web.config.*</span><span class="sxs-lookup"><span data-stu-id="e6b6b-993">In the Kudu **Diagnostic Console**, return to the path **site** > **wwwroot** to reveal the *web.config* file.</span></span> <span data-ttu-id="e6b6b-994">Znovu otevřete soubor **web.config** výběrem ikony tužky.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-994">Open the **web.config** file again by selecting the pencil icon.</span></span>
1. <span data-ttu-id="e6b6b-995">Nastavte **stdoutLogEnabled** na `false`.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-995">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="e6b6b-996">Chcete-li soubor uložit, vyberte **Uložit.**</span><span class="sxs-lookup"><span data-stu-id="e6b6b-996">Select **Save** to save the file.</span></span>

<span data-ttu-id="e6b6b-997">Další informace naleznete v tématu <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-997">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

> [!WARNING]
> <span data-ttu-id="e6b6b-998">Selhání zakázání protokolu stdout může vést k selhání aplikace nebo serveru.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-998">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="e6b6b-999">Neexistuje žádné omezení na velikost souboru protokolu nebo počet vytvořených souborů protokolu.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-999">There's no limit on log file size or the number of log files created.</span></span> <span data-ttu-id="e6b6b-1000">K řešení problémů se spuštěním aplikace používejte protokolování stdout pouze.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1000">Only use stdout logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="e6b6b-1001">Pro obecné protokolování v aplikaci ASP.NET Core po spuštění použijte knihovnu protokolování, která omezuje velikost souboru protokolu a otáčí protokoly.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1001">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="e6b6b-1002">Další informace naleznete [v tématu zprostředkovatelé protokolování třetích stran](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1002">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="slow-or-hanging-app-azure-app-service"></a><span data-ttu-id="e6b6b-1003">Pomalá nebo zavěšená aplikace (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1003">Slow or hanging app (Azure App Service)</span></span>

<span data-ttu-id="e6b6b-1004">Když aplikace reaguje pomalu nebo se při žádosti zablokuje, přečtěte si následující články:</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1004">When an app responds slowly or hangs on a request, see the following articles:</span></span>

* [<span data-ttu-id="e6b6b-1005">Řešení potíží s výkonem pomalých webových aplikací ve službě Azure App Service</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1005">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="e6b6b-1006">Použití rozšíření webu Diagnostikátor selhání k zachycení výpisu pro občasné výjimky problémy nebo problémy s výkonem na Azure Web App</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1006">Use Crash Diagnoser Site Extension to Capture Dump for Intermittent Exception issues or performance issues on Azure Web App</span></span>](https://blogs.msdn.microsoft.com/asiatech/2015/12/28/use-crash-diagnoser-site-extension-to-capture-dump-for-intermittent-exception-issues-or-performance-issues-on-azure-web-app/)

### <a name="monitoring-blades"></a><span data-ttu-id="e6b6b-1007">Monitorovací nože</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1007">Monitoring blades</span></span>

<span data-ttu-id="e6b6b-1008">Monitorování blades poskytují alternativní řešení potíží k metodám popsaným výše v tématu.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1008">Monitoring blades provide an alternative troubleshooting experience to the methods described earlier in the topic.</span></span> <span data-ttu-id="e6b6b-1009">Tyto čepele lze použít k diagnostice chyb řady 500.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1009">These blades can be used to diagnose 500-series errors.</span></span>

<span data-ttu-id="e6b6b-1010">Zkontrolujte, zda jsou nainstalovány ASP.NET rozšíření core.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1010">Confirm that the ASP.NET Core Extensions are installed.</span></span> <span data-ttu-id="e6b6b-1011">Pokud rozšíření nejsou nainstalovaná, nainstalujte je ručně:</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1011">If the extensions aren't installed, install them manually:</span></span>

1. <span data-ttu-id="e6b6b-1012">V části **Čepele NÁSTROJE PRO VÝVOJ** vyberte okno **Rozšíření.**</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1012">In the **DEVELOPMENT TOOLS** blade section, select the **Extensions** blade.</span></span>
1. <span data-ttu-id="e6b6b-1013">V seznamu by se měl **zobrazit ASP.NET rozšíření jádra.**</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1013">The **ASP.NET Core Extensions** should appear in the list.</span></span>
1. <span data-ttu-id="e6b6b-1014">Pokud rozšíření nejsou nainstalovaná, vyberte tlačítko **Přidat.**</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1014">If the extensions aren't installed, select the **Add** button.</span></span>
1. <span data-ttu-id="e6b6b-1015">Ze seznamu zvolte **ASP.NET jádrová rozšíření.**</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1015">Choose the **ASP.NET Core Extensions** from the list.</span></span>
1. <span data-ttu-id="e6b6b-1016">Chcete-li přijmout zákonné podmínky, vyberte **možnost OK.**</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1016">Select **OK** to accept the legal terms.</span></span>
1. <span data-ttu-id="e6b6b-1017">V okně **Přidat prodlužovací** okno vyberte **OK.**</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1017">Select **OK** on the **Add extension** blade.</span></span>
1. <span data-ttu-id="e6b6b-1018">Informační zpráva o automaticky otevíraná okna označuje, kdy jsou rozšíření úspěšně nainstalována.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1018">An informational pop-up message indicates when the extensions are successfully installed.</span></span>

<span data-ttu-id="e6b6b-1019">Pokud protokolování stdout není povoleno, postupujte takto:</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1019">If stdout logging isn't enabled, follow these steps:</span></span>

1. <span data-ttu-id="e6b6b-1020">Na webu Azure Portal vyberte okno **Rozšířené nástroje** v oblasti NÁSTROJE **PRO VÝVOJ.**</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1020">In the Azure portal, select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="e6b6b-1021">Vyberte tlačítko \*\*Přejít.&rarr; \*\*</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1021">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="e6b6b-1022">Konzole Kudu se otevře v novém kartě prohlížeče nebo v okně.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1022">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="e6b6b-1023">Pomocí navigačního panelu v horní části stránky otevřete **konzolu ladění** a vyberte **možnost CMD**.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1023">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="e6b6b-1024">Otevřete složky na **webu** > cesty **wwwroot** a posuňte se dolů a odhalte soubor *web.config* v dolní části seznamu.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1024">Open the folders to the path **site** > **wwwroot** and scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="e6b6b-1025">Klikněte na ikonu tužky vedle souboru *web.config.*</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1025">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="e6b6b-1026">Nastavte **stdoutLogEnabled** na `true` cestu **stdoutLogFile** `\\?\%home%\LogFiles\stdout`a změňte ji na: .</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1026">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="e6b6b-1027">Vyberte **Uložit,** chcete-li uložit aktualizovaný soubor *web.config.*</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1027">Select **Save** to save the updated *web.config* file.</span></span>

<span data-ttu-id="e6b6b-1028">Postupujte k aktivaci diagnostického protokolování:</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1028">Proceed to activate diagnostic logging:</span></span>

1. <span data-ttu-id="e6b6b-1029">Na webu Azure Portal vyberte okno **Protokoly diagnostiky.**</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1029">In the Azure portal, select the **Diagnostics logs** blade.</span></span>
1. <span data-ttu-id="e6b6b-1030">Vyberte přepínač **Zapnuto** pro **protokolování aplikací (souborový systém)** a **Podrobné chybové zprávy**.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1030">Select the **On** switch for **Application Logging (Filesystem)** and **Detailed error messages**.</span></span> <span data-ttu-id="e6b6b-1031">V horní části nože vyberte tlačítko **Uložit.**</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1031">Select the **Save** button at the top of the blade.</span></span>
1. <span data-ttu-id="e6b6b-1032">Chcete-li zahrnout trasování neúspěšných požadavků, známé také jako protokolfreb (Failed Request Event Buffering), vyberte přepínač **Zapnuto** pro **trasování neúspěšných požadavků**.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1032">To include failed request tracing, also known as Failed Request Event Buffering (FREB) logging, select the **On** switch for **Failed request tracing**.</span></span>
1. <span data-ttu-id="e6b6b-1033">Vyberte okno **protokolu datového proudu,** který je uveden okamžitě pod **protokoly protokoly diagnostiky** na portálu.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1033">Select the **Log stream** blade, which is listed immediately under the **Diagnostics logs** blade in the portal.</span></span>
1. <span data-ttu-id="e6b6b-1034">Požádejte o to do aplikace.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1034">Make a request to the app.</span></span>
1. <span data-ttu-id="e6b6b-1035">V rámci dat datového proudu protokolu je uvedena příčina chyby.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1035">Within the log stream data, the cause of the error is indicated.</span></span>

<span data-ttu-id="e6b6b-1036">Nezapomeňte zakázat stdout protokolování po dokončení řešení potíží.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1036">Be sure to disable stdout logging when troubleshooting is complete.</span></span>

<span data-ttu-id="e6b6b-1037">Zobrazení protokolů trasování neúspěšných požadavků (protokoly FREB):</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1037">To view the failed request tracing logs (FREB logs):</span></span>

1. <span data-ttu-id="e6b6b-1038">Přejděte na okno **Diagnostikovat a vyřešit problémy** na webu Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1038">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="e6b6b-1039">V yberte **Protokoly trasování neúspěšných požadavků** v oblasti **NÁSTROJE PODPORY** na postranním panelu.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1039">Select **Failed Request Tracing Logs** from the **SUPPORT TOOLS** area of the sidebar.</span></span>

<span data-ttu-id="e6b6b-1040">Další informace [najdete v tématu Sledování neúspěšných požadavků v tématu Povolit diagnostiku pro webové aplikace v tématu Služby aplikací](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) a [v nejčastějších dotazech k výkonu aplikací pro webové aplikace v Azure: Jak zapnu trasování neúspěšných požadavků?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing)</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1040">See [Failed request traces section of the Enable diagnostics logging for web apps in Azure App Service topic](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) and the [Application performance FAQs for Web Apps in Azure: How do I turn on failed request tracing?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing) for more information.</span></span>

<span data-ttu-id="e6b6b-1041">Další informace najdete [v tématu Povolení protokolování diagnostiky pro webové aplikace ve službě Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log).</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1041">For more information, see [Enable diagnostics logging for web apps in Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log).</span></span>

> [!WARNING]
> <span data-ttu-id="e6b6b-1042">Selhání zakázání protokolu stdout může vést k selhání aplikace nebo serveru.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1042">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="e6b6b-1043">Neexistuje žádné omezení na velikost souboru protokolu nebo počet vytvořených souborů protokolu.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1043">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="e6b6b-1044">Pro rutinní protokolování v aplikaci ASP.NET Core použijte knihovnu protokolování, která omezuje velikost souboru protokolu a otáčí protokoly.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1044">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="e6b6b-1045">Další informace naleznete [v tématu zprostředkovatelé protokolování třetích stran](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1045">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

## <a name="troubleshoot-on-iis"></a><span data-ttu-id="e6b6b-1046">Odstraňování potíží ve službě IIS</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1046">Troubleshoot on IIS</span></span>

### <a name="application-event-log-iis"></a><span data-ttu-id="e6b6b-1047">Protokol událostí aplikace (IIS)</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1047">Application Event Log (IIS)</span></span>

<span data-ttu-id="e6b6b-1048">Přístup k protokolu událostí aplikace:</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1048">Access the Application Event Log:</span></span>

1. <span data-ttu-id="e6b6b-1049">Otevřete nabídku Start, vyhledejte *Prohlížeč událostí*a vyberte aplikaci **Prohlížeč událostí.**</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1049">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="e6b6b-1050">V **Prohlížeči událostí**otevřete uzel **Protokoly systému Windows.**</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1050">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="e6b6b-1051">Výběrem **možnosti Aplikace** otevřete protokol událostí aplikace.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1051">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="e6b6b-1052">Vyhledejte chyby spojené s aplikací se selháním.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1052">Search for errors associated with the failing app.</span></span> <span data-ttu-id="e6b6b-1053">Chyby mají hodnotu *modulu AspNetCore služby IIS* nebo *modulu AspNetCore služby IIS Express* ve sloupci *Zdroj.*</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1053">Errors have a value of *IIS AspNetCore Module* or *IIS Express AspNetCore Module* in the *Source* column.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="e6b6b-1054">Spuštění aplikace na příkazovém řádku</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1054">Run the app at a command prompt</span></span>

<span data-ttu-id="e6b6b-1055">Mnoho chyb při spuštění nevytváří užitečné informace v protokolu událostí aplikace.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1055">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="e6b6b-1056">Příčinu některých chyb můžete najít spuštěním aplikace na příkazovém řádku v hostitelském systému.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1056">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span>

#### <a name="framework-dependent-deployment"></a><span data-ttu-id="e6b6b-1057">Nasazení závislé na rámci</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1057">Framework-dependent deployment</span></span>

<span data-ttu-id="e6b6b-1058">Pokud je aplikace [nasazení závislé na rámci](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1058">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

1. <span data-ttu-id="e6b6b-1059">Na příkazovém řádku přejděte do složky nasazení a spusťte aplikaci spuštěním sestavení aplikace pomocí *dotnet.exe*.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1059">At a command prompt, navigate to the deployment folder and run the app by executing the app's assembly with *dotnet.exe*.</span></span> <span data-ttu-id="e6b6b-1060">V následujícím příkazu nahraďte název sestavení \<aplikace assembly_name `dotnet .\<assembly_name>.dll`>: .</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1060">In the following command, substitute the name of the app's assembly for \<assembly_name>: `dotnet .\<assembly_name>.dll`.</span></span>
1. <span data-ttu-id="e6b6b-1061">Výstup konzoly z aplikace, zobrazující případné chyby, je zapsán do okna konzoly.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1061">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="e6b6b-1062">Pokud dojde k chybám při podání požadavku na aplikaci, podat žádost na hostitele a port, kde Kestrel naslouchá.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1062">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="e6b6b-1063">Pomocí výchozího hostitele a příspěvku `http://localhost:5000/`požádejte o místo .</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1063">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="e6b6b-1064">Pokud aplikace reaguje normálně na adrese koncového bodu Kestrel, problém je pravděpodobnější související s konfigurací hostování a méně pravděpodobné v rámci aplikace.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1064">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

#### <a name="self-contained-deployment"></a><span data-ttu-id="e6b6b-1065">Samostatné nasazení</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1065">Self-contained deployment</span></span>

<span data-ttu-id="e6b6b-1066">Pokud se jedná o [samostatné nasazení](/dotnet/core/deploying/#self-contained-deployments-scd)aplikace :</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1066">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

1. <span data-ttu-id="e6b6b-1067">Na příkazovém řádku přejděte do složky nasazení a spusťte spustitelný soubor aplikace.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1067">At a command prompt, navigate to the deployment folder and run the app's executable.</span></span> <span data-ttu-id="e6b6b-1068">V následujícím příkazu nahraďte název sestavení \<aplikace assembly_name `<assembly_name>.exe`>: .</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1068">In the following command, substitute the name of the app's assembly for \<assembly_name>: `<assembly_name>.exe`.</span></span>
1. <span data-ttu-id="e6b6b-1069">Výstup konzoly z aplikace, zobrazující případné chyby, je zapsán do okna konzoly.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1069">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="e6b6b-1070">Pokud dojde k chybám při podání požadavku na aplikaci, podat žádost na hostitele a port, kde Kestrel naslouchá.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1070">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="e6b6b-1071">Pomocí výchozího hostitele a příspěvku `http://localhost:5000/`požádejte o místo .</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1071">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="e6b6b-1072">Pokud aplikace reaguje normálně na adrese koncového bodu Kestrel, problém je pravděpodobnější související s konfigurací hostování a méně pravděpodobné v rámci aplikace.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1072">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

### <a name="aspnet-core-module-stdout-log-iis"></a><span data-ttu-id="e6b6b-1073">ASP.NET protokol stdout modulu (IIS)</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1073">ASP.NET Core Module stdout log (IIS)</span></span>

<span data-ttu-id="e6b6b-1074">Povolení a zobrazení protokolů stdout:</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1074">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="e6b6b-1075">Přejděte do složky nasazení webu v hostitelském systému.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1075">Navigate to the site's deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="e6b6b-1076">Pokud složka *protokolů* není k dispozici, vytvořte složku.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1076">If the *logs* folder isn't present, create the folder.</span></span> <span data-ttu-id="e6b6b-1077">Pokyny k povolení msbuild vytvořit složku *protokolů* v nasazení automaticky, naleznete v tématu [directory struktura.](xref:host-and-deploy/directory-structure)</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1077">For instructions on how to enable MSBuild to create the *logs* folder in the deployment automatically, see the [Directory structure](xref:host-and-deploy/directory-structure) topic.</span></span>
1. <span data-ttu-id="e6b6b-1078">Upravte soubor *web.config.*</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1078">Edit the *web.config* file.</span></span> <span data-ttu-id="e6b6b-1079">Nastavte **stdoutLogEnabled** a `true` změňte cestu **stdoutLogFile** tak, `.\logs\stdout`aby přecózovala na složku *protokolů* (například ).</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1079">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to point to the *logs* folder (for example, `.\logs\stdout`).</span></span> <span data-ttu-id="e6b6b-1080">`stdout`v cestě je předpona názvu souboru protokolu.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1080">`stdout` in the path is the log file name prefix.</span></span> <span data-ttu-id="e6b6b-1081">Časové razítko, ID procesu a přípona souboru jsou přidány automaticky při vytvoření protokolu.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1081">A timestamp, process id, and file extension are added automatically when the log is created.</span></span> <span data-ttu-id="e6b6b-1082">Jako `stdout` předpona názvu souboru je typický soubor protokolu pojmenován *stdout_20180205184032_5412.log*.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1082">Using `stdout` as the file name prefix, a typical log file is named *stdout_20180205184032_5412.log*.</span></span>
1. <span data-ttu-id="e6b6b-1083">Ujistěte se, že identita fondu aplikací má oprávnění k zápisu do složky *protokolů.*</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1083">Ensure your application pool's identity has write permissions to the *logs* folder.</span></span>
1. <span data-ttu-id="e6b6b-1084">Uložte aktualizovaný soubor *web.config.*</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1084">Save the updated *web.config* file.</span></span>
1. <span data-ttu-id="e6b6b-1085">Požádejte o to do aplikace.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1085">Make a request to the app.</span></span>
1. <span data-ttu-id="e6b6b-1086">Přejděte do složky *protokolů.*</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1086">Navigate to the *logs* folder.</span></span> <span data-ttu-id="e6b6b-1087">Vyhledejte a otevřete nejnovější protokol stdout.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1087">Find and open the most recent stdout log.</span></span>
1. <span data-ttu-id="e6b6b-1088">Prostudujte si protokol pro chyby.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1088">Study the log for errors.</span></span>

<span data-ttu-id="e6b6b-1089">Zakažte protokolování stdout po dokončení řešení potíží:</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1089">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="e6b6b-1090">Upravte soubor *web.config.*</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1090">Edit the *web.config* file.</span></span>
1. <span data-ttu-id="e6b6b-1091">Nastavte **stdoutLogEnabled** na `false`.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1091">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="e6b6b-1092">Uložte soubor.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1092">Save the file.</span></span>

<span data-ttu-id="e6b6b-1093">Další informace naleznete v tématu <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1093">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

> [!WARNING]
> <span data-ttu-id="e6b6b-1094">Selhání zakázání protokolu stdout může vést k selhání aplikace nebo serveru.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1094">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="e6b6b-1095">Neexistuje žádné omezení na velikost souboru protokolu nebo počet vytvořených souborů protokolu.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1095">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="e6b6b-1096">Pro rutinní protokolování v aplikaci ASP.NET Core použijte knihovnu protokolování, která omezuje velikost souboru protokolu a otáčí protokoly.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1096">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="e6b6b-1097">Další informace naleznete [v tématu zprostředkovatelé protokolování třetích stran](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1097">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="enable-the-developer-exception-page"></a><span data-ttu-id="e6b6b-1098">Povolení stránky výjimky pro vývojáře</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1098">Enable the Developer Exception Page</span></span>

<span data-ttu-id="e6b6b-1099">`ASPNETCORE_ENVIRONMENT` [Proměnnou prostředí lze přidat do web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) pro spuštění aplikace ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1099">The `ASPNETCORE_ENVIRONMENT` [environment variable can be added to web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) to run the app in the Development environment.</span></span> <span data-ttu-id="e6b6b-1100">Pokud prostředí není přepsáno při spuštění `UseEnvironment` aplikace tvůrcem hostitele, nastavení proměnné prostředí umožňuje, aby se [stránka výjimky pro vývojáře](xref:fundamentals/error-handling) zobrazila při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1100">As long as the environment isn't overridden in app startup by `UseEnvironment` on the host builder, setting the environment variable allows the [Developer Exception Page](xref:fundamentals/error-handling) to appear when the app is run.</span></span>

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

<span data-ttu-id="e6b6b-1101">Nastavení proměnné prostředí `ASPNETCORE_ENVIRONMENT` pro se doporučuje pouze pro použití na pracovních a testovacích serverech, které nejsou vystaveny Internetu.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1101">Setting the environment variable for `ASPNETCORE_ENVIRONMENT` is only recommended for use on staging and testing servers that aren't exposed to the Internet.</span></span> <span data-ttu-id="e6b6b-1102">Po odstranění potíží odeberte proměnnou prostředí ze souboru *web.config.*</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1102">Remove the environment variable from the *web.config* file after troubleshooting.</span></span> <span data-ttu-id="e6b6b-1103">Informace o nastavení proměnných prostředí v *souboru web.config*naleznete v [tématu podřízený prvek environmentVariables aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1103">For information on setting environment variables in *web.config*, see [environmentVariables child element of aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).</span></span>

### <a name="obtain-data-from-an-app"></a><span data-ttu-id="e6b6b-1104">Získání dat z aplikace</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1104">Obtain data from an app</span></span>

<span data-ttu-id="e6b6b-1105">Pokud je aplikace schopná reagovat na požadavky, získejte požadavek, připojení a další data z aplikace pomocí terminálového inline middlewaru.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1105">If an app is capable of responding to requests, obtain request, connection, and additional data from the app using terminal inline middleware.</span></span> <span data-ttu-id="e6b6b-1106">Další informace a ukázkový <xref:test/troubleshoot#obtain-data-from-an-app>kód naleznete v tématu .</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1106">For more information and sample code, see <xref:test/troubleshoot#obtain-data-from-an-app>.</span></span>

### <a name="slow-or-hanging-app-iis"></a><span data-ttu-id="e6b6b-1107">Pomalá nebo závěsná aplikace (IIS)</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1107">Slow or hanging app (IIS)</span></span>

<span data-ttu-id="e6b6b-1108">*Výpis stavu systému* je snímek paměti systému a může pomoci určit příčinu selhání aplikace, selhání při spuštění nebo pomalé aplikace.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1108">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="e6b6b-1109">Aplikace dojde k chybě nebo narazí na výjimku</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1109">App crashes or encounters an exception</span></span>

<span data-ttu-id="e6b6b-1110">Získání a analýza výpisu z [programu Zasílání zpráv o chybách systému Windows (WER)](/windows/desktop/wer/windows-error-reporting):</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1110">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="e6b6b-1111">Vytvořte složku, ve které `c:\dumps`budou uloženy soubory s výpisem stavu systému .</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1111">Create a folder to hold crash dump files at `c:\dumps`.</span></span> <span data-ttu-id="e6b6b-1112">Fond aplikací musí mít přístup pro zápis do složky.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1112">The app pool must have write access to the folder.</span></span>
1. <span data-ttu-id="e6b6b-1113">Spusťte [skript EnableDumps PowerShell](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1):</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1113">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1):</span></span>
   * <span data-ttu-id="e6b6b-1114">Pokud aplikace používá [model hostování v procesu](xref:host-and-deploy/iis/index#in-process-hosting-model), spusťte skript pro *w3wp.exe*:</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1114">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\EnableDumps w3wp.exe c:\dumps
     ```

   * <span data-ttu-id="e6b6b-1115">Pokud aplikace používá [mimoprocesový model hostování](xref:host-and-deploy/iis/index#out-of-process-hosting-model), spusťte skript pro *dotnet.exe*:</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1115">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\EnableDumps dotnet.exe c:\dumps
     ```

1. <span data-ttu-id="e6b6b-1116">Spusťte aplikaci za podmínek, které způsobují selhání dojít.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1116">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="e6b6b-1117">Po selhání spusťte [skript DisableDumps PowerShell](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1):</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1117">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1):</span></span>
   * <span data-ttu-id="e6b6b-1118">Pokud aplikace používá [model hostování v procesu](xref:host-and-deploy/iis/index#in-process-hosting-model), spusťte skript pro *w3wp.exe*:</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1118">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\DisableDumps w3wp.exe
     ```

   * <span data-ttu-id="e6b6b-1119">Pokud aplikace používá [mimoprocesový model hostování](xref:host-and-deploy/iis/index#out-of-process-hosting-model), spusťte skript pro *dotnet.exe*:</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1119">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\DisableDumps dotnet.exe
     ```

<span data-ttu-id="e6b6b-1120">Po zhroucení aplikace a výpis kolekce je kompletní, aplikace je povoleno ukončit normálně.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1120">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="e6b6b-1121">Skript PowerShell udává WER tak, aby shromažďoval až pět výpisů v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1121">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="e6b6b-1122">Výpisy stavu systému může zabírají velké množství místa na disku (až několik gigabajtů každý).</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1122">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="e6b6b-1123">Aplikace přestane reagovat, selže při spuštění nebo běží normálně</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1123">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="e6b6b-1124">Když aplikace *přestane reagovat* (přestane reagovat, ale nezhroutí se), selže při spuštění nebo běží normálně, najdete v [tématu Soubory výpisu uživatelského režimu: Výběr nejvhodnějšího nástroje](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) pro výběr vhodného nástroje pro vytvoření výpisu.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1124">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="e6b6b-1125">Analyzovat výpis</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1125">Analyze the dump</span></span>

<span data-ttu-id="e6b6b-1126">Výpis lze analyzovat pomocí několika přístupů.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1126">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="e6b6b-1127">Další informace naleznete [v tématu Analýza souboru s výpisem stavu uživatelského režimu](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1127">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="clear-package-caches"></a><span data-ttu-id="e6b6b-1128">Vymazat mezipaměti balíčků</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1128">Clear package caches</span></span>

<span data-ttu-id="e6b6b-1129">Funkční aplikace může selhat ihned po upgradu sady .NET Core SDK ve vývojovém počítači nebo změně verze balíčku v rámci aplikace.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1129">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="e6b6b-1130">V některých případech nesouvislé balíčky může přerušit aplikaci při provádění hlavních upgradů.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1130">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="e6b6b-1131">Většinu těchto problémů lze opravit podle následujících pokynů:</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1131">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="e6b6b-1132">Odstraňte složky *bin* a *obj.*</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1132">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="e6b6b-1133">Vymazat mezipaměti balíčku spuštěním [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) z příkazového prostředí.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1133">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="e6b6b-1134">Vymazání mezipaměti balíčků lze také provést pomocí nástroje [nuget.exe](https://www.nuget.org/downloads) a spuštěnípříkazu `nuget locals all -clear`.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1134">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="e6b6b-1135">*nuget.exe* není přibalená instalace s operačním systémem Windows desktop a musí být získány odděleně od [webu NuGet](https://www.nuget.org/downloads).</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1135">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="e6b6b-1136">Obnovte a znovu vytvořte projekt.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1136">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="e6b6b-1137">Před opětovnou nasazením aplikace odstraňte všechny soubory ve složce nasazení na serveru.</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1137">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e6b6b-1138">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1138">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:fundamentals/error-handling>
* <xref:host-and-deploy/aspnet-core-module>

### <a name="azure-documentation"></a><span data-ttu-id="e6b6b-1139">Dokumentace k Azure</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1139">Azure documentation</span></span>

* [<span data-ttu-id="e6b6b-1140">Application Insights pro ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1140">Application Insights for ASP.NET Core</span></span>](/azure/application-insights/app-insights-asp-net-core)
* [<span data-ttu-id="e6b6b-1141">Část Řešení potíží s webovou aplikací ve službě Azure App Service ve vzdáleném ladění webových aplikací pomocí Visual Studia</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1141">Remote debugging web apps section of Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug)
* [<span data-ttu-id="e6b6b-1142">Přehled diagnostiky služby Azure App Service</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1142">Azure App Service diagnostics overview</span></span>](/azure/app-service/app-service-diagnostics)
* [<span data-ttu-id="e6b6b-1143">Postup: Monitorování aplikací ve službě Azure App Service</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1143">How to: Monitor Apps in Azure App Service</span></span>](/azure/app-service/web-sites-monitor)
* [<span data-ttu-id="e6b6b-1144">Poradce při potížích s webovou aplikací ve službě Azure App Service pomocí Visual Studia</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1144">Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [<span data-ttu-id="e6b6b-1145">Poradce při potížích s chybami HTTP "502 chybná brána" a "503 služba není k dispozici" ve svých webových aplikacích Azure</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1145">Troubleshoot HTTP errors of "502 bad gateway" and "503 service unavailable" in your Azure web apps</span></span>](/azure/app-service/app-service-web-troubleshoot-http-502-http-503)
* [<span data-ttu-id="e6b6b-1146">Řešení potíží s výkonem pomalých webových aplikací ve službě Azure App Service</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1146">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="e6b6b-1147">Časté otázky k výkonu aplikací pro webové aplikace v Azure</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1147">Application performance FAQs for Web Apps in Azure</span></span>](/azure/app-service/app-service-web-availability-performance-application-issues-faq)
* [<span data-ttu-id="e6b6b-1148">Azure Web App izolovaného prostoru (omezení spuštění služby App Service)</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1148">Azure Web App sandbox (App Service runtime execution limitations)</span></span>](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)
* [<span data-ttu-id="e6b6b-1149">Azure Friday: Azure App Service Diagnostic and Troubleshooting Experience (12minutové video)</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1149">Azure Friday: Azure App Service Diagnostic and Troubleshooting Experience (12-minute video)</span></span>](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)

### <a name="visual-studio-documentation"></a><span data-ttu-id="e6b6b-1150">Dokumentace sady Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1150">Visual Studio documentation</span></span>

* [<span data-ttu-id="e6b6b-1151">Vzdálené ladění ASP.NET jádro ve službě IIS v Azure ve Visual Studiu 2017</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1151">Remote Debug ASP.NET Core on IIS in Azure in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-azure)
* [<span data-ttu-id="e6b6b-1152">Vzdálené ladění ASP.NET jádra ve vzdáleném počítači služby IIS v sadě Visual Studio 2017</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1152">Remote Debug ASP.NET Core on a Remote IIS Computer in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-aspnet-on-a-remote-iis-computer)
* [<span data-ttu-id="e6b6b-1153">Informace o ladění pomocí sady Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1153">Learn to debug using Visual Studio</span></span>](/visualstudio/debugger/getting-started-with-the-debugger)

### <a name="visual-studio-code-documentation"></a><span data-ttu-id="e6b6b-1154">Dokumentace kódu visual studia</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1154">Visual Studio Code documentation</span></span>

* [<span data-ttu-id="e6b6b-1155">Ladění pomocí kódu sady Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e6b6b-1155">Debugging with Visual Studio Code</span></span>](https://code.visualstudio.com/docs/editor/debugging)

::: moniker-end
