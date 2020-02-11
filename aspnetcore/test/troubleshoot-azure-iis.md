---
title: Řešení potíží s ASP.NET Core v Azure App Service a IIS
author: guardrex
description: Naučte se diagnostikovat problémy s Azure App Service a Internetová informační služba nasazení ASP.NET Core aplikací (IIS).
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
uid: test/troubleshoot-azure-iis
ms.openlocfilehash: a5cd17e46126828c6bc8436ccaaca28edb2573d0
ms.sourcegitcommit: 235623b6e5a5d1841139c82a11ac2b4b3f31a7a9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/10/2020
ms.locfileid: "77114845"
---
# <a name="troubleshoot-aspnet-core-on-azure-app-service-and-iis"></a><span data-ttu-id="43bf9-103">Řešení potíží s ASP.NET Core v Azure App Service a IIS</span><span class="sxs-lookup"><span data-stu-id="43bf9-103">Troubleshoot ASP.NET Core on Azure App Service and IIS</span></span>

<span data-ttu-id="43bf9-104">Od [Luke Latham](https://github.com/guardrex) a [Justin Kotalik](https://github.com/jkotalik)</span><span class="sxs-lookup"><span data-stu-id="43bf9-104">By [Luke Latham](https://github.com/guardrex) and [Justin Kotalik](https://github.com/jkotalik)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="43bf9-105">Tento článek poskytuje informace o běžných chybách při spuštění aplikací a pokyny k diagnostice chyb při nasazení aplikace do Azure App Service nebo IIS:</span><span class="sxs-lookup"><span data-stu-id="43bf9-105">This article provides information on common app startup errors and instructions on how to diagnose errors when an app is deployed to Azure App Service or IIS:</span></span>

[<span data-ttu-id="43bf9-106">Chyby při spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="43bf9-106">App startup errors</span></span>](#app-startup-errors)  
<span data-ttu-id="43bf9-107">Vysvětluje běžné spouštění scénářů stavového kódu HTTP.</span><span class="sxs-lookup"><span data-stu-id="43bf9-107">Explains common startup HTTP status code scenarios.</span></span>

[<span data-ttu-id="43bf9-108">Řešení potíží na Azure App Service</span><span class="sxs-lookup"><span data-stu-id="43bf9-108">Troubleshoot on Azure App Service</span></span>](#troubleshoot-on-azure-app-service)  
<span data-ttu-id="43bf9-109">Poskytuje rady pro řešení potíží pro aplikace nasazené do Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="43bf9-109">Provides troubleshooting advice for apps deployed to Azure App Service.</span></span>

[<span data-ttu-id="43bf9-110">Odstraňování potíží ve službě IIS</span><span class="sxs-lookup"><span data-stu-id="43bf9-110">Troubleshoot on IIS</span></span>](#troubleshoot-on-iis)  
<span data-ttu-id="43bf9-111">Poskytuje rady pro řešení potíží pro aplikace nasazené do služby IIS nebo místně spuštěné v IIS Express.</span><span class="sxs-lookup"><span data-stu-id="43bf9-111">Provides troubleshooting advice for apps deployed to IIS or running on IIS Express locally.</span></span> <span data-ttu-id="43bf9-112">Pokyny platí pro nasazení systémů Windows Server a Desktop v systému Windows.</span><span class="sxs-lookup"><span data-stu-id="43bf9-112">The guidance applies to both Windows Server and Windows desktop deployments.</span></span>

[<span data-ttu-id="43bf9-113">Vymazat mezipaměti balíčků</span><span class="sxs-lookup"><span data-stu-id="43bf9-113">Clear package caches</span></span>](#clear-package-caches)  
<span data-ttu-id="43bf9-114">Vysvětluje, co dělat v případě, že při provádění podstatných upgradů nebo změně verze balíčku dojde k přerušení aplikace v nesouvislých balíčcích.</span><span class="sxs-lookup"><span data-stu-id="43bf9-114">Explains what to do when incoherent packages break an app when performing major upgrades or changing package versions.</span></span>

[<span data-ttu-id="43bf9-115">Další materiály</span><span class="sxs-lookup"><span data-stu-id="43bf9-115">Additional resources</span></span>](#additional-resources)  
<span data-ttu-id="43bf9-116">Seznam dalších témat pro odstraňování potíží.</span><span class="sxs-lookup"><span data-stu-id="43bf9-116">Lists additional troubleshooting topics.</span></span>

## <a name="app-startup-errors"></a><span data-ttu-id="43bf9-117">Chyby při spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="43bf9-117">App startup errors</span></span>

<span data-ttu-id="43bf9-118">V aplikaci Visual Studio je výchozím nastavením projektu ASP.NET Core [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hostování během ladění.</span><span class="sxs-lookup"><span data-stu-id="43bf9-118">In Visual Studio, an ASP.NET Core project defaults to [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hosting during debugging.</span></span> <span data-ttu-id="43bf9-119">*502,5 – selhání procesu* nebo *500,30 – chyba* , ke které dojde při místním ladění, se dá diagnostikovat pomocí Rady v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="43bf9-119">A *502.5 - Process Failure* or a *500.30 - Start Failure* that occurs when debugging locally can be diagnosed using the advice in this topic.</span></span>

### <a name="40314-forbidden"></a><span data-ttu-id="43bf9-120">403,14 zakázané</span><span class="sxs-lookup"><span data-stu-id="43bf9-120">403.14 Forbidden</span></span>

<span data-ttu-id="43bf9-121">Aplikaci se nepodařilo spustit.</span><span class="sxs-lookup"><span data-stu-id="43bf9-121">The app fails to start.</span></span> <span data-ttu-id="43bf9-122">Protokoluje se následující chyba:</span><span class="sxs-lookup"><span data-stu-id="43bf9-122">The following error is logged:</span></span>

```
The Web server is configured to not list the contents of this directory.
```

<span data-ttu-id="43bf9-123">K této chybě obvykle dochází v důsledku poškozeného nasazení v hostitelském systému, který zahrnuje kterýkoli z následujících scénářů:</span><span class="sxs-lookup"><span data-stu-id="43bf9-123">The error is usually caused by a broken deployment on the hosting system, which includes any of the following scenarios:</span></span>

* <span data-ttu-id="43bf9-124">Aplikace je nasazená do chybné složky v hostitelském systému.</span><span class="sxs-lookup"><span data-stu-id="43bf9-124">The app is deployed to the wrong folder on the hosting system.</span></span>
* <span data-ttu-id="43bf9-125">Procesu nasazení se nepodařilo přesunout všechny soubory a složky aplikace do složky pro nasazení v hostitelském systému.</span><span class="sxs-lookup"><span data-stu-id="43bf9-125">The deployment process failed to move all of the app's files and folders to the deployment folder on the hosting system.</span></span>
* <span data-ttu-id="43bf9-126">V nasazení chybí soubor *Web. config* , nebo je obsah souboru *Web. config* poškozený.</span><span class="sxs-lookup"><span data-stu-id="43bf9-126">The *web.config* file is missing from the deployment, or the *web.config* file contents are malformed.</span></span>

<span data-ttu-id="43bf9-127">Proveďte následující kroky:</span><span class="sxs-lookup"><span data-stu-id="43bf9-127">Perform the following steps:</span></span>

1. <span data-ttu-id="43bf9-128">Odstraňte všechny soubory a složky ze složky pro nasazení v hostitelském systému.</span><span class="sxs-lookup"><span data-stu-id="43bf9-128">Delete all of the files and folders from the deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="43bf9-129">Znovu nasaďte obsah složky pro *publikování* aplikace do hostitelského systému pomocí běžné metody nasazení, jako je například Visual Studio, PowerShell nebo ruční nasazení:</span><span class="sxs-lookup"><span data-stu-id="43bf9-129">Redeploy the contents of the app's *publish* folder to the hosting system using your normal method of deployment, such as Visual Studio, PowerShell, or manual deployment:</span></span>
   * <span data-ttu-id="43bf9-130">Zkontrolujte, zda je v nasazení přítomen soubor *Web. config* a zda jeho obsah je správný.</span><span class="sxs-lookup"><span data-stu-id="43bf9-130">Confirm that the *web.config* file is present in the deployment and that its contents are correct.</span></span>
   * <span data-ttu-id="43bf9-131">Při hostování na Azure App Service potvrďte, že je aplikace nasazená do `D:\home\site\wwwroot` složky.</span><span class="sxs-lookup"><span data-stu-id="43bf9-131">When hosting on Azure App Service, confirm that the app is deployed to the `D:\home\site\wwwroot` folder.</span></span>
   * <span data-ttu-id="43bf9-132">Když je aplikace hostovaná službou IIS, zkontrolujte, že je aplikace nasazená na **fyzickou cestu** služby IIS, která se zobrazuje v **základním nastavení** **Správce služby IIS**.</span><span class="sxs-lookup"><span data-stu-id="43bf9-132">When the app is hosted by IIS, confirm that the app is deployed to the IIS **Physical path** shown in **IIS Manager**'s **Basic Settings**.</span></span>
1. <span data-ttu-id="43bf9-133">Ověřte, zda jsou všechny soubory a složky aplikace nasazeny porovnáním nasazení v hostitelském systému s obsahem složky *publikování* projektu.</span><span class="sxs-lookup"><span data-stu-id="43bf9-133">Confirm that all of the app's files and folders are deployed by comparing the deployment on the hosting system to the contents of the project's *publish* folder.</span></span>

<span data-ttu-id="43bf9-134">Další informace o rozložení publikované aplikace ASP.NET Core najdete v tématu <xref:host-and-deploy/directory-structure>.</span><span class="sxs-lookup"><span data-stu-id="43bf9-134">For more information on the layout of a published ASP.NET Core app, see <xref:host-and-deploy/directory-structure>.</span></span> <span data-ttu-id="43bf9-135">Další informace o souboru *Web. config* naleznete v tématu <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>.</span><span class="sxs-lookup"><span data-stu-id="43bf9-135">For more information on the *web.config* file, see <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>.</span></span>

### <a name="500-internal-server-error"></a><span data-ttu-id="43bf9-136">500 – Interní chyba serveru</span><span class="sxs-lookup"><span data-stu-id="43bf9-136">500 Internal Server Error</span></span>

<span data-ttu-id="43bf9-137">Spuštění aplikace, ale chybu brání splnění žádosti. na serveru.</span><span class="sxs-lookup"><span data-stu-id="43bf9-137">The app starts, but an error prevents the server from fulfilling the request.</span></span>

<span data-ttu-id="43bf9-138">Při spuštění nebo při vytváření odpovědi, k této chybě dochází v kódu aplikace.</span><span class="sxs-lookup"><span data-stu-id="43bf9-138">This error occurs within the app's code during startup or while creating a response.</span></span> <span data-ttu-id="43bf9-139">Odpověď možná neobsahuje žádný obsah, nebo se odpověď může zobrazit jako *500 interní chyba serveru* v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="43bf9-139">The response may contain no content, or the response may appear as a *500 Internal Server Error* in the browser.</span></span> <span data-ttu-id="43bf9-140">V protokolu událostí aplikace obvykle hlásí, že aplikace se normálně spustit.</span><span class="sxs-lookup"><span data-stu-id="43bf9-140">The Application Event Log usually states that the app started normally.</span></span> <span data-ttu-id="43bf9-141">Z pohledu serveru, který je správný.</span><span class="sxs-lookup"><span data-stu-id="43bf9-141">From the server's perspective, that's correct.</span></span> <span data-ttu-id="43bf9-142">Aplikace začal, ale nemůže generovat platnou odpověď.</span><span class="sxs-lookup"><span data-stu-id="43bf9-142">The app did start, but it can't generate a valid response.</span></span> <span data-ttu-id="43bf9-143">Spusťte aplikaci na příkazovém řádku na serveru nebo povolte řešení potíží pomocí protokolu stdout modulu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="43bf9-143">Run the app at a command prompt on the server or enable the ASP.NET Core Module stdout log to troubleshoot the problem.</span></span>

### <a name="5000-in-process-handler-load-failure"></a><span data-ttu-id="43bf9-144">500.0 v procesu selhání načtení obslužné rutiny</span><span class="sxs-lookup"><span data-stu-id="43bf9-144">500.0 In-Process Handler Load Failure</span></span>

<span data-ttu-id="43bf9-145">Pracovní proces se nezdaří.</span><span class="sxs-lookup"><span data-stu-id="43bf9-145">The worker process fails.</span></span> <span data-ttu-id="43bf9-146">Aplikace se nespustí.</span><span class="sxs-lookup"><span data-stu-id="43bf9-146">The app doesn't start.</span></span>

<span data-ttu-id="43bf9-147">Při načítání komponent [modulu ASP.NET Core](xref:host-and-deploy/aspnet-core-module) došlo k neznámé chybě.</span><span class="sxs-lookup"><span data-stu-id="43bf9-147">An unknown error occurred loading [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) components.</span></span> <span data-ttu-id="43bf9-148">Proveďte jednu z následujících akcí:</span><span class="sxs-lookup"><span data-stu-id="43bf9-148">Take one of the following actions:</span></span>

* <span data-ttu-id="43bf9-149">Kontaktujte [Podpora Microsoftu](https://support.microsoft.com/oas/default.aspx?prid=15832) (vyberte **vývojářské nástroje** potom **ASP.NET Core**).</span><span class="sxs-lookup"><span data-stu-id="43bf9-149">Contact [Microsoft Support](https://support.microsoft.com/oas/default.aspx?prid=15832) (select **Developer Tools** then **ASP.NET Core**).</span></span>
* <span data-ttu-id="43bf9-150">Položte otázku na Stack Overflow.</span><span class="sxs-lookup"><span data-stu-id="43bf9-150">Ask a question on Stack Overflow.</span></span>
* <span data-ttu-id="43bf9-151">Vydejte problém do našeho [úložiště GitHub](https://github.com/dotnet/AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="43bf9-151">File an issue on our [GitHub repository](https://github.com/dotnet/AspNetCore).</span></span>

### <a name="50030-in-process-startup-failure"></a><span data-ttu-id="43bf9-152">500.30 v procesu selhání spuštění</span><span class="sxs-lookup"><span data-stu-id="43bf9-152">500.30 In-Process Startup Failure</span></span>

<span data-ttu-id="43bf9-153">Pracovní proces se nezdaří.</span><span class="sxs-lookup"><span data-stu-id="43bf9-153">The worker process fails.</span></span> <span data-ttu-id="43bf9-154">Aplikace se nespustí.</span><span class="sxs-lookup"><span data-stu-id="43bf9-154">The app doesn't start.</span></span>

<span data-ttu-id="43bf9-155">[Modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module) se pokusí spustit modul CLR .NET Core v procesu, ale jeho spuštění se nezdařilo.</span><span class="sxs-lookup"><span data-stu-id="43bf9-155">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the .NET Core CLR in-process, but it fails to start.</span></span> <span data-ttu-id="43bf9-156">Příčinou selhání spuštění procesu může být obvykle určení z položek protokolu událostí aplikace a protokolu stdout modulu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="43bf9-156">The cause of a process startup failure can usually be determined from entries in the Application Event Log and the ASP.NET Core Module stdout log.</span></span>

<span data-ttu-id="43bf9-157">Běžné podmínky selhání:</span><span class="sxs-lookup"><span data-stu-id="43bf9-157">Common failure conditions:</span></span>

* <span data-ttu-id="43bf9-158">Aplikace je nesprávně nakonfigurovaná kvůli cílení na verzi ASP.NET Core sdílené rozhraní, které není k dispozici.</span><span class="sxs-lookup"><span data-stu-id="43bf9-158">The app is misconfigured due to targeting a version of the ASP.NET Core shared framework that isn't present.</span></span> <span data-ttu-id="43bf9-159">Zkontrolujte, jaké verze rozhraní framework ASP.NET Core sdílené jsou nainstalovány v cílovém počítači.</span><span class="sxs-lookup"><span data-stu-id="43bf9-159">Check which versions of the ASP.NET Core shared framework are installed on the target machine.</span></span>
* <span data-ttu-id="43bf9-160">Při použití Azure Key Vault chybí oprávnění Key Vault.</span><span class="sxs-lookup"><span data-stu-id="43bf9-160">Using Azure Key Vault, lack of permissions to the Key Vault.</span></span> <span data-ttu-id="43bf9-161">Zkontrolujte zásady přístupu v cílovém Key Vault, abyste měli jistotu, že jsou udělená správná oprávnění.</span><span class="sxs-lookup"><span data-stu-id="43bf9-161">Check the access policies in the targeted Key Vault to ensure that the correct permissions are granted.</span></span>

### <a name="50031-ancm-failed-to-find-native-dependencies"></a><span data-ttu-id="43bf9-162">500,31 ANCM nepovedlo se najít nativní závislosti.</span><span class="sxs-lookup"><span data-stu-id="43bf9-162">500.31 ANCM Failed to Find Native Dependencies</span></span>

<span data-ttu-id="43bf9-163">Pracovní proces se nezdaří.</span><span class="sxs-lookup"><span data-stu-id="43bf9-163">The worker process fails.</span></span> <span data-ttu-id="43bf9-164">Aplikace se nespustí.</span><span class="sxs-lookup"><span data-stu-id="43bf9-164">The app doesn't start.</span></span>

<span data-ttu-id="43bf9-165">[Modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module) se pokusí spustit modul runtime .NET Core v procesu, ale jeho spuštění se nezdařilo.</span><span class="sxs-lookup"><span data-stu-id="43bf9-165">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the .NET Core runtime in-process, but it fails to start.</span></span> <span data-ttu-id="43bf9-166">Nejběžnější příčinou tohoto selhání při spuštění je, že modul runtime `Microsoft.NETCore.App` nebo `Microsoft.AspNetCore.App` není nainstalován.</span><span class="sxs-lookup"><span data-stu-id="43bf9-166">The most common cause of this startup failure is when the `Microsoft.NETCore.App` or `Microsoft.AspNetCore.App` runtime isn't installed.</span></span> <span data-ttu-id="43bf9-167">Pokud je aplikace nasazená na cílovou ASP.NET Core 3,0 a tato verze v počítači neexistuje, dojde k této chybě.</span><span class="sxs-lookup"><span data-stu-id="43bf9-167">If the app is deployed to target ASP.NET Core 3.0 and that version doesn't exist on the machine, this error occurs.</span></span> <span data-ttu-id="43bf9-168">Následuje příklad chybové zprávy:</span><span class="sxs-lookup"><span data-stu-id="43bf9-168">An example error message follows:</span></span>

```
The specified framework 'Microsoft.NETCore.App', version '3.0.0' was not found.
  - The following frameworks were found:
      2.2.1 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview5-27626-15 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27713-13 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27714-15 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27723-08 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
```

<span data-ttu-id="43bf9-169">Chybová zpráva obsahuje seznam všech nainstalovaných verzí rozhraní .NET Core a verze, kterou aplikace požaduje.</span><span class="sxs-lookup"><span data-stu-id="43bf9-169">The error message lists all the installed .NET Core versions and the version requested by the app.</span></span> <span data-ttu-id="43bf9-170">Chcete-li tuto chybu vyřešit, postupujte takto:</span><span class="sxs-lookup"><span data-stu-id="43bf9-170">To fix this error, either:</span></span>

* <span data-ttu-id="43bf9-171">Na počítač nainstalujte odpovídající verzi .NET Core.</span><span class="sxs-lookup"><span data-stu-id="43bf9-171">Install the appropriate version of .NET Core on the machine.</span></span>
* <span data-ttu-id="43bf9-172">Změňte aplikaci na cílovou verzi .NET Core, která je na počítači přítomná.</span><span class="sxs-lookup"><span data-stu-id="43bf9-172">Change the app to target a version of .NET Core that's present on the machine.</span></span>
* <span data-ttu-id="43bf9-173">Publikujte aplikaci jako samostatné [nasazení](/dotnet/core/deploying/#self-contained-deployments-scd).</span><span class="sxs-lookup"><span data-stu-id="43bf9-173">Publish the app as a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>

<span data-ttu-id="43bf9-174">Při spuštění ve vývoji (`ASPNETCORE_ENVIRONMENT` proměnná prostředí je nastavená na `Development`) se konkrétní chyba zapíše do odpovědi HTTP.</span><span class="sxs-lookup"><span data-stu-id="43bf9-174">When running in development (the `ASPNETCORE_ENVIRONMENT` environment variable is set to `Development`), the specific error is written to the HTTP response.</span></span> <span data-ttu-id="43bf9-175">Příčinu selhání spuštění procesu se nachází také v protokolu událostí aplikace.</span><span class="sxs-lookup"><span data-stu-id="43bf9-175">The cause of a process startup failure is also found in the Application Event Log.</span></span>

### <a name="50032-ancm-failed-to-load-dll"></a><span data-ttu-id="43bf9-176">500,32 ANCM načtení knihovny DLL se nezdařilo</span><span class="sxs-lookup"><span data-stu-id="43bf9-176">500.32 ANCM Failed to Load dll</span></span>

<span data-ttu-id="43bf9-177">Pracovní proces se nezdaří.</span><span class="sxs-lookup"><span data-stu-id="43bf9-177">The worker process fails.</span></span> <span data-ttu-id="43bf9-178">Aplikace se nespustí.</span><span class="sxs-lookup"><span data-stu-id="43bf9-178">The app doesn't start.</span></span>

<span data-ttu-id="43bf9-179">Nejběžnější příčinou této chyby je, že aplikace je publikovaná pro nekompatibilní architekturu procesoru.</span><span class="sxs-lookup"><span data-stu-id="43bf9-179">The most common cause for this error is that the app is published for an incompatible processor architecture.</span></span> <span data-ttu-id="43bf9-180">Pokud je pracovní proces spuštěn jako 32 aplikace a aplikace byla publikována na cílovou 64-bit, dojde k této chybě.</span><span class="sxs-lookup"><span data-stu-id="43bf9-180">If the worker process is running as a 32-bit app and the app was published to target 64-bit, this error occurs.</span></span>

<span data-ttu-id="43bf9-181">Chcete-li tuto chybu vyřešit, postupujte takto:</span><span class="sxs-lookup"><span data-stu-id="43bf9-181">To fix this error, either:</span></span>

* <span data-ttu-id="43bf9-182">Znovu publikujte aplikaci pro stejnou architekturu procesoru jako pracovní proces.</span><span class="sxs-lookup"><span data-stu-id="43bf9-182">Republish the app for the same processor architecture as the worker process.</span></span>
* <span data-ttu-id="43bf9-183">Publikování aplikace jako [nasazení závislého na rozhraní](/dotnet/core/deploying/#framework-dependent-executables-fde).</span><span class="sxs-lookup"><span data-stu-id="43bf9-183">Publish the app as a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-executables-fde).</span></span>

### <a name="50033-ancm-request-handler-load-failure"></a><span data-ttu-id="43bf9-184">500,33 selhání načtení obslužné rutiny žádosti ANCM</span><span class="sxs-lookup"><span data-stu-id="43bf9-184">500.33 ANCM Request Handler Load Failure</span></span>

<span data-ttu-id="43bf9-185">Pracovní proces se nezdaří.</span><span class="sxs-lookup"><span data-stu-id="43bf9-185">The worker process fails.</span></span> <span data-ttu-id="43bf9-186">Aplikace se nespustí.</span><span class="sxs-lookup"><span data-stu-id="43bf9-186">The app doesn't start.</span></span>

<span data-ttu-id="43bf9-187">Aplikace neodkazovala na rozhraní `Microsoft.AspNetCore.App` Framework.</span><span class="sxs-lookup"><span data-stu-id="43bf9-187">The app didn't reference the `Microsoft.AspNetCore.App` framework.</span></span> <span data-ttu-id="43bf9-188">[Modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module)může hostovat jenom aplikace cílené na rozhraní `Microsoft.AspNetCore.App` Framework.</span><span class="sxs-lookup"><span data-stu-id="43bf9-188">Only apps targeting the `Microsoft.AspNetCore.App` framework can be hosted by the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span>

<span data-ttu-id="43bf9-189">Chcete-li tuto chybu opravit, zkontrolujte, zda je aplikace cílena na rozhraní `Microsoft.AspNetCore.App` Framework.</span><span class="sxs-lookup"><span data-stu-id="43bf9-189">To fix this error, confirm that the app is targeting the `Microsoft.AspNetCore.App` framework.</span></span> <span data-ttu-id="43bf9-190">Zkontrolujte `.runtimeconfig.json` a ověřte rozhraní, které cílí na aplikaci.</span><span class="sxs-lookup"><span data-stu-id="43bf9-190">Check the `.runtimeconfig.json` to verify the framework targeted by the app.</span></span>

### <a name="50034-ancm-mixed-hosting-models-not-supported"></a><span data-ttu-id="43bf9-191">500,34 ANCM smíšené modely hostování se nepodporují.</span><span class="sxs-lookup"><span data-stu-id="43bf9-191">500.34 ANCM Mixed Hosting Models Not Supported</span></span>

<span data-ttu-id="43bf9-192">Pracovní proces nemůže v jednom procesu spustit jak aplikaci v rámci procesu, tak i aplikaci mimo proces.</span><span class="sxs-lookup"><span data-stu-id="43bf9-192">The worker process can't run both an in-process app and an out-of-process app in the same process.</span></span>

<span data-ttu-id="43bf9-193">Chcete-li tuto chybu opravit, spusťte aplikace v samostatných fondech aplikací služby IIS.</span><span class="sxs-lookup"><span data-stu-id="43bf9-193">To fix this error, run apps in separate IIS application pools.</span></span>

### <a name="50035-ancm-multiple-in-process-applications-in-same-process"></a><span data-ttu-id="43bf9-194">500,35 ANCM více vnitroprocesové aplikací ve stejném procesu</span><span class="sxs-lookup"><span data-stu-id="43bf9-194">500.35 ANCM Multiple In-Process Applications in same Process</span></span>

<span data-ttu-id="43bf9-195">Pracovní proces nemůže v jednom procesu spustit více vnitroprocesové aplikací.</span><span class="sxs-lookup"><span data-stu-id="43bf9-195">The worker process can't run multiple in-process apps in the same process.</span></span>

<span data-ttu-id="43bf9-196">Chcete-li tuto chybu opravit, spusťte aplikace v samostatných fondech aplikací služby IIS.</span><span class="sxs-lookup"><span data-stu-id="43bf9-196">To fix this error, run apps in separate IIS application pools.</span></span>

### <a name="50036-ancm-out-of-process-handler-load-failure"></a><span data-ttu-id="43bf9-197">500,36 ANCM selhání načtení obslužné rutiny mimo proces</span><span class="sxs-lookup"><span data-stu-id="43bf9-197">500.36 ANCM Out-Of-Process Handler Load Failure</span></span>

<span data-ttu-id="43bf9-198">Obslužná rutina žádosti mimo proces, *aspnetcorev2_outofprocess. dll*, není vedle souboru *aspnetcorev2. dll* .</span><span class="sxs-lookup"><span data-stu-id="43bf9-198">The out-of-process request handler, *aspnetcorev2_outofprocess.dll*, isn't next to the *aspnetcorev2.dll* file.</span></span> <span data-ttu-id="43bf9-199">To označuje poškozenou instalaci [modulu ASP.NET Core](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="43bf9-199">This indicates a corrupted installation of the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span>

<span data-ttu-id="43bf9-200">Chcete-li tuto chybu opravit, opravte instalaci [hostující sady .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) (pro IIS) nebo Visual Studio (pro IIS Express).</span><span class="sxs-lookup"><span data-stu-id="43bf9-200">To fix this error, repair the installation of the [.NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) (for IIS) or Visual Studio (for IIS Express).</span></span>

### <a name="50037-ancm-failed-to-start-within-startup-time-limit"></a><span data-ttu-id="43bf9-201">500,37 ANCM se nepodařilo spustit v časovém limitu spuštění.</span><span class="sxs-lookup"><span data-stu-id="43bf9-201">500.37 ANCM Failed to Start Within Startup Time Limit</span></span>

<span data-ttu-id="43bf9-202">ANCM se nepovedlo spustit v rámci časového limitu spuštění nechte.</span><span class="sxs-lookup"><span data-stu-id="43bf9-202">ANCM failed to start within the provied startup time limit.</span></span> <span data-ttu-id="43bf9-203">Ve výchozím nastavení je časový limit 120 sekund.</span><span class="sxs-lookup"><span data-stu-id="43bf9-203">By default, the timeout is 120 seconds.</span></span>

<span data-ttu-id="43bf9-204">K této chybě může dojít, když ve stejném počítači spustíte velký počet aplikací.</span><span class="sxs-lookup"><span data-stu-id="43bf9-204">This error can occur when starting a large number of apps on the same machine.</span></span> <span data-ttu-id="43bf9-205">Kontrolovat špičky využití procesoru a paměti na serveru při spuštění.</span><span class="sxs-lookup"><span data-stu-id="43bf9-205">Check for CPU/Memory usage spikes on the server during startup.</span></span> <span data-ttu-id="43bf9-206">Možná budete muset rozložit proces spouštění více aplikací.</span><span class="sxs-lookup"><span data-stu-id="43bf9-206">You may need to stagger the startup process of multiple apps.</span></span>

### <a name="5025-process-failure"></a><span data-ttu-id="43bf9-207">502.5 zpracovat selhání</span><span class="sxs-lookup"><span data-stu-id="43bf9-207">502.5 Process Failure</span></span>

<span data-ttu-id="43bf9-208">Pracovní proces se nezdaří.</span><span class="sxs-lookup"><span data-stu-id="43bf9-208">The worker process fails.</span></span> <span data-ttu-id="43bf9-209">Aplikace se nespustí.</span><span class="sxs-lookup"><span data-stu-id="43bf9-209">The app doesn't start.</span></span>

<span data-ttu-id="43bf9-210">[Modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module) se pokusí spustit pracovní proces, ale jeho spuštění se nezdařilo.</span><span class="sxs-lookup"><span data-stu-id="43bf9-210">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the worker process but it fails to start.</span></span> <span data-ttu-id="43bf9-211">Příčinou selhání spuštění procesu může být obvykle určení z položek protokolu událostí aplikace a protokolu stdout modulu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="43bf9-211">The cause of a process startup failure can usually be determined from entries in the Application Event Log and the ASP.NET Core Module stdout log.</span></span>

<span data-ttu-id="43bf9-212">Běžné chyby je, že aplikace je špatně nakonfigurovaný. kvůli cílení na určitou verzi rozhraní framework sdílené ASP.NET Core, který není k dispozici.</span><span class="sxs-lookup"><span data-stu-id="43bf9-212">A common failure condition is the app is misconfigured due to targeting a version of the ASP.NET Core shared framework that isn't present.</span></span> <span data-ttu-id="43bf9-213">Zkontrolujte, jaké verze rozhraní framework ASP.NET Core sdílené jsou nainstalovány v cílovém počítači.</span><span class="sxs-lookup"><span data-stu-id="43bf9-213">Check which versions of the ASP.NET Core shared framework are installed on the target machine.</span></span> <span data-ttu-id="43bf9-214">*Sdílené rozhraní* je sada sestavení (soubory *. dll* ), které jsou nainstalovány na počítači a na které odkazuje Metapackage jako `Microsoft.AspNetCore.App`.</span><span class="sxs-lookup"><span data-stu-id="43bf9-214">The *shared framework* is the set of assemblies (*.dll* files) that are installed on the machine and referenced by a metapackage such as `Microsoft.AspNetCore.App`.</span></span> <span data-ttu-id="43bf9-215">Odkaz Metapackage může určovat minimální požadovanou verzi.</span><span class="sxs-lookup"><span data-stu-id="43bf9-215">The metapackage reference can specify a minimum required version.</span></span> <span data-ttu-id="43bf9-216">Další informace najdete v tématu [sdílené rozhraní](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span><span class="sxs-lookup"><span data-stu-id="43bf9-216">For more information, see [The shared framework](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span></span>

<span data-ttu-id="43bf9-217">Chybová stránka *selhání procesu 502,5* se vrátí v případě, že při selhání hostování nebo aplikace dojde k chybě pracovního procesu:</span><span class="sxs-lookup"><span data-stu-id="43bf9-217">The *502.5 Process Failure* error page is returned when a hosting or app misconfiguration causes the worker process to fail:</span></span>

### <a name="failed-to-start-application-errorcode-0x800700c1"></a><span data-ttu-id="43bf9-218">Nepovedlo se spustit aplikaci (kód chyby "0x800700c1")</span><span class="sxs-lookup"><span data-stu-id="43bf9-218">Failed to start application (ErrorCode '0x800700c1')</span></span>

```
EventID: 1010
Source: IIS AspNetCore Module V2
Failed to start application '/LM/W3SVC/6/ROOT/', ErrorCode '0x800700c1'.
```

<span data-ttu-id="43bf9-219">Aplikaci se nepovedlo spustit, protože se nepovedlo načíst sestavení aplikace ( *. dll*).</span><span class="sxs-lookup"><span data-stu-id="43bf9-219">The app failed to start because the app's assembly (*.dll*) couldn't be loaded.</span></span>

<span data-ttu-id="43bf9-220">Tato chyba nastane, pokud došlo k neshodě bitové verze mezi publikované aplikace a proces w3wp/iisexpress.</span><span class="sxs-lookup"><span data-stu-id="43bf9-220">This error occurs when there's a bitness mismatch between the published app and the w3wp/iisexpress process.</span></span>

<span data-ttu-id="43bf9-221">Ověřte správnost nastavení 32-bit fondu aplikací:</span><span class="sxs-lookup"><span data-stu-id="43bf9-221">Confirm that the app pool's 32-bit setting is correct:</span></span>

1. <span data-ttu-id="43bf9-222">Vyberte fond aplikací v **fondech aplikací**Správce služby IIS.</span><span class="sxs-lookup"><span data-stu-id="43bf9-222">Select the app pool in IIS Manager's **Application Pools**.</span></span>
1. <span data-ttu-id="43bf9-223">V části **Upravit fond aplikací** na panelu **Akce** vyberte **Upřesnit nastavení** .</span><span class="sxs-lookup"><span data-stu-id="43bf9-223">Select **Advanced Settings** under **Edit Application Pool** in the **Actions** panel.</span></span>
1. <span data-ttu-id="43bf9-224">Nastavte **možnost povolit 32 – bitové aplikace**:</span><span class="sxs-lookup"><span data-stu-id="43bf9-224">Set **Enable 32-Bit Applications**:</span></span>
   * <span data-ttu-id="43bf9-225">Pokud nasazujete 32 (x86) aplikaci, nastavte hodnotu na `True`.</span><span class="sxs-lookup"><span data-stu-id="43bf9-225">If deploying a 32-bit (x86) app, set the value to `True`.</span></span>
   * <span data-ttu-id="43bf9-226">Pokud nasazujete 64 aplikaci (x64), nastavte hodnotu na `False`.</span><span class="sxs-lookup"><span data-stu-id="43bf9-226">If deploying a 64-bit (x64) app, set the value to `False`.</span></span>

<span data-ttu-id="43bf9-227">Ověřte, že mezi vlastností `<Platform>` MSBuild v souboru projektu a publikovaným bitová verzeem aplikace nedochází ke konfliktu.</span><span class="sxs-lookup"><span data-stu-id="43bf9-227">Confirm that there isn't a conflict between a `<Platform>` MSBuild property in the project file and the published bitness of the app.</span></span>

### <a name="connection-reset"></a><span data-ttu-id="43bf9-228">Obnovení připojení</span><span class="sxs-lookup"><span data-stu-id="43bf9-228">Connection reset</span></span>

<span data-ttu-id="43bf9-229">Pokud dojde k chybě po odeslání hlaviček, je příliš pozdě pro server, který odešle **500 interní chybu serveru** , když dojde k chybě.</span><span class="sxs-lookup"><span data-stu-id="43bf9-229">If an error occurs after the headers are sent, it's too late for the server to send a **500 Internal Server Error** when an error occurs.</span></span> <span data-ttu-id="43bf9-230">Často se to stane, když dojde k chybě při serializaci složitých objektů pro odpověď.</span><span class="sxs-lookup"><span data-stu-id="43bf9-230">This often happens when an error occurs during the serialization of complex objects for a response.</span></span> <span data-ttu-id="43bf9-231">Tento typ chyby se zobrazí jako chyba *resetování připojení* na klientovi.</span><span class="sxs-lookup"><span data-stu-id="43bf9-231">This type of error appears as a *connection reset* error on the client.</span></span> <span data-ttu-id="43bf9-232">[Protokolování aplikace](xref:fundamentals/logging/index) může pomoct řešit tyto typy chyb.</span><span class="sxs-lookup"><span data-stu-id="43bf9-232">[Application logging](xref:fundamentals/logging/index) can help troubleshoot these types of errors.</span></span>

### <a name="default-startup-limits"></a><span data-ttu-id="43bf9-233">Výchozí omezení při spuštění</span><span class="sxs-lookup"><span data-stu-id="43bf9-233">Default startup limits</span></span>

<span data-ttu-id="43bf9-234">[ASP.NET Core modul](xref:host-and-deploy/aspnet-core-module) je nakonfigurovaný s výchozí *startupTimeLimitou* 120 sekund.</span><span class="sxs-lookup"><span data-stu-id="43bf9-234">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) is configured with a default *startupTimeLimit* of 120 seconds.</span></span> <span data-ttu-id="43bf9-235">Když necháte na výchozí hodnotu, aplikace může trvat až dvě minuty, spusťte před modulu protokoly selhání procesu.</span><span class="sxs-lookup"><span data-stu-id="43bf9-235">When left at the default value, an app may take up to two minutes to start before the module logs a process failure.</span></span> <span data-ttu-id="43bf9-236">Informace o konfiguraci modulu naleznete v tématu [atributy elementu aspNetCore](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span><span class="sxs-lookup"><span data-stu-id="43bf9-236">For information on configuring the module, see [Attributes of the aspNetCore element](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span></span>

## <a name="troubleshoot-on-azure-app-service"></a><span data-ttu-id="43bf9-237">Řešení potíží na Azure App Service</span><span class="sxs-lookup"><span data-stu-id="43bf9-237">Troubleshoot on Azure App Service</span></span>

[!INCLUDE [Azure App Service Preview Notice](~/includes/azure-apps-preview-notice.md)]

### <a name="application-event-log-azure-app-service"></a><span data-ttu-id="43bf9-238">Protokol událostí aplikace (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="43bf9-238">Application Event Log (Azure App Service)</span></span>

<span data-ttu-id="43bf9-239">Chcete-li získat přístup k protokolu událostí aplikace, použijte okno **Diagnostika a řešení problémů** v Azure Portal:</span><span class="sxs-lookup"><span data-stu-id="43bf9-239">To access the Application Event Log, use the **Diagnose and solve problems** blade in the Azure portal:</span></span>

1. <span data-ttu-id="43bf9-240">V Azure Portal otevřete aplikaci v **App Services**.</span><span class="sxs-lookup"><span data-stu-id="43bf9-240">In the Azure portal, open the app in **App Services**.</span></span>
1. <span data-ttu-id="43bf9-241">Vyberte možnost **diagnostikovat a vyřešit problémy**.</span><span class="sxs-lookup"><span data-stu-id="43bf9-241">Select **Diagnose and solve problems**.</span></span>
1. <span data-ttu-id="43bf9-242">Vyberte **diagnostické nástroje** záhlaví.</span><span class="sxs-lookup"><span data-stu-id="43bf9-242">Select the **Diagnostic Tools** heading.</span></span>
1. <span data-ttu-id="43bf9-243">V nabídce **nástroje podpory**vyberte tlačítko **události aplikace** .</span><span class="sxs-lookup"><span data-stu-id="43bf9-243">Under **Support Tools**, select the **Application Events** button.</span></span>
1. <span data-ttu-id="43bf9-244">Projděte si nejnovější chybu, kterou poskytla položka *IIS AspNetCoreModule* nebo *IIS AspNetCoreModule v2* ve **zdrojovém** sloupci.</span><span class="sxs-lookup"><span data-stu-id="43bf9-244">Examine the latest error provided by the *IIS AspNetCoreModule* or *IIS AspNetCoreModule V2* entry in the **Source** column.</span></span>

<span data-ttu-id="43bf9-245">Alternativou k použití okna **diagnostikovat a řešit problémy** je kontrola souboru protokolu událostí aplikace přímo pomocí [Kudu](https://github.com/projectkudu/kudu/wiki):</span><span class="sxs-lookup"><span data-stu-id="43bf9-245">An alternative to using the **Diagnose and solve problems** blade is to examine the Application Event Log file directly using [Kudu](https://github.com/projectkudu/kudu/wiki):</span></span>

1. <span data-ttu-id="43bf9-246">Otevřete **Rozšířené nástroje** v oblasti **vývojové nástroje** .</span><span class="sxs-lookup"><span data-stu-id="43bf9-246">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="43bf9-247">Vyberte tlačítko **přejít&rarr;** .</span><span class="sxs-lookup"><span data-stu-id="43bf9-247">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="43bf9-248">Konzola Kudu se otevře v novém okně nebo záložce prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="43bf9-248">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="43bf9-249">Pomocí navigačního panelu v horní části stránky otevřete **konzolu ladění** a vyberte **cmd**.</span><span class="sxs-lookup"><span data-stu-id="43bf9-249">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="43bf9-250">Otevřete složku **soubory protokolů** .</span><span class="sxs-lookup"><span data-stu-id="43bf9-250">Open the **LogFiles** folder.</span></span>
1. <span data-ttu-id="43bf9-251">Vyberte ikonu tužky vedle souboru *EventLog. XML* .</span><span class="sxs-lookup"><span data-stu-id="43bf9-251">Select the pencil icon next to the *eventlog.xml* file.</span></span>
1. <span data-ttu-id="43bf9-252">Projděte si protokol.</span><span class="sxs-lookup"><span data-stu-id="43bf9-252">Examine the log.</span></span> <span data-ttu-id="43bf9-253">Posuňte se do dolní části protokolu, abyste viděli nejaktuálnější události.</span><span class="sxs-lookup"><span data-stu-id="43bf9-253">Scroll to the bottom of the log to see the most recent events.</span></span>

### <a name="run-the-app-in-the-kudu-console"></a><span data-ttu-id="43bf9-254">Spuštění aplikace v konzole Kudu</span><span class="sxs-lookup"><span data-stu-id="43bf9-254">Run the app in the Kudu console</span></span>

<span data-ttu-id="43bf9-255">Mnoho chyb při spuštění nevytvářejí užitečné informace v protokolu událostí aplikace.</span><span class="sxs-lookup"><span data-stu-id="43bf9-255">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="43bf9-256">Tuto chybu můžete zjistit spuštěním aplikace v konzole vzdáleného spuštění [Kudu](https://github.com/projectkudu/kudu/wiki) :</span><span class="sxs-lookup"><span data-stu-id="43bf9-256">You can run the app in the [Kudu](https://github.com/projectkudu/kudu/wiki) Remote Execution Console to discover the error:</span></span>

1. <span data-ttu-id="43bf9-257">Otevřete **Rozšířené nástroje** v oblasti **vývojové nástroje** .</span><span class="sxs-lookup"><span data-stu-id="43bf9-257">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="43bf9-258">Vyberte tlačítko **přejít&rarr;** .</span><span class="sxs-lookup"><span data-stu-id="43bf9-258">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="43bf9-259">Konzola Kudu se otevře v novém okně nebo záložce prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="43bf9-259">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="43bf9-260">Pomocí navigačního panelu v horní části stránky otevřete **konzolu ladění** a vyberte **cmd**.</span><span class="sxs-lookup"><span data-stu-id="43bf9-260">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>

#### <a name="test-a-32-bit-x86-app"></a><span data-ttu-id="43bf9-261">Testování 32 (x86) aplikace</span><span class="sxs-lookup"><span data-stu-id="43bf9-261">Test a 32-bit (x86) app</span></span>

<span data-ttu-id="43bf9-262">**Aktuální verze**</span><span class="sxs-lookup"><span data-stu-id="43bf9-262">**Current release**</span></span>

1. `cd d:\home\site\wwwroot`
1. <span data-ttu-id="43bf9-263">Spusťte aplikaci:</span><span class="sxs-lookup"><span data-stu-id="43bf9-263">Run the app:</span></span>
   * <span data-ttu-id="43bf9-264">Pokud aplikace je [nasazení závislé na rozhraní](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span><span class="sxs-lookup"><span data-stu-id="43bf9-264">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

     ```dotnetcli
     dotnet .\{ASSEMBLY NAME}.dll
     ```

   * <span data-ttu-id="43bf9-265">Pokud je aplikace samostatná, [nasazení](/dotnet/core/deploying/#self-contained-deployments-scd):</span><span class="sxs-lookup"><span data-stu-id="43bf9-265">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

     ```console
     {ASSEMBLY NAME}.exe
     ```

<span data-ttu-id="43bf9-266">Výstup konzoly z aplikace, v němž se zobrazují všechny chyby, je kanálem do konzoly Kudu.</span><span class="sxs-lookup"><span data-stu-id="43bf9-266">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="43bf9-267">**Nasazení závislé na architektuře spuštěné ve verzi Preview**</span><span class="sxs-lookup"><span data-stu-id="43bf9-267">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="43bf9-268">*Vyžaduje instalaci rozšíření webu ASP.NET Core {VERSION} (x86) Runtime.*</span><span class="sxs-lookup"><span data-stu-id="43bf9-268">*Requires installing the ASP.NET Core {VERSION} (x86) Runtime site extension.*</span></span>

1. <span data-ttu-id="43bf9-269">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32` (`{X.Y}` je verze modulu runtime)</span><span class="sxs-lookup"><span data-stu-id="43bf9-269">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="43bf9-270">Spusťte aplikaci: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="43bf9-270">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="43bf9-271">Výstup konzoly z aplikace, v němž se zobrazují všechny chyby, je kanálem do konzoly Kudu.</span><span class="sxs-lookup"><span data-stu-id="43bf9-271">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

#### <a name="test-a-64-bit-x64-app"></a><span data-ttu-id="43bf9-272">Testování 64 aplikace (x64)</span><span class="sxs-lookup"><span data-stu-id="43bf9-272">Test a 64-bit (x64) app</span></span>

<span data-ttu-id="43bf9-273">**Aktuální verze**</span><span class="sxs-lookup"><span data-stu-id="43bf9-273">**Current release**</span></span>

* <span data-ttu-id="43bf9-274">Pokud je aplikace [nasazením závislého na rozhraní](/dotnet/core/deploying/#framework-dependent-deployments-fdd)64 (x64):</span><span class="sxs-lookup"><span data-stu-id="43bf9-274">If the app is a 64-bit (x64) [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>
  1. `cd D:\Program Files\dotnet`
  1. <span data-ttu-id="43bf9-275">Spusťte aplikaci: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="43bf9-275">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>
* <span data-ttu-id="43bf9-276">Pokud je aplikace samostatná, [nasazení](/dotnet/core/deploying/#self-contained-deployments-scd):</span><span class="sxs-lookup"><span data-stu-id="43bf9-276">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>
  1. `cd D:\home\site\wwwroot`
  1. <span data-ttu-id="43bf9-277">Spusťte aplikaci: `{ASSEMBLY NAME}.exe`</span><span class="sxs-lookup"><span data-stu-id="43bf9-277">Run the app: `{ASSEMBLY NAME}.exe`</span></span>

<span data-ttu-id="43bf9-278">Výstup konzoly z aplikace, v němž se zobrazují všechny chyby, je kanálem do konzoly Kudu.</span><span class="sxs-lookup"><span data-stu-id="43bf9-278">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="43bf9-279">**Nasazení závislé na architektuře spuštěné ve verzi Preview**</span><span class="sxs-lookup"><span data-stu-id="43bf9-279">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="43bf9-280">*Vyžaduje instalaci rozšíření webu ASP.NET Core {VERSION} (x64) Runtime.*</span><span class="sxs-lookup"><span data-stu-id="43bf9-280">*Requires installing the ASP.NET Core {VERSION} (x64) Runtime site extension.*</span></span>

1. <span data-ttu-id="43bf9-281">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64` (`{X.Y}` je verze modulu runtime)</span><span class="sxs-lookup"><span data-stu-id="43bf9-281">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="43bf9-282">Spusťte aplikaci: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="43bf9-282">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="43bf9-283">Výstup konzoly z aplikace, v němž se zobrazují všechny chyby, je kanálem do konzoly Kudu.</span><span class="sxs-lookup"><span data-stu-id="43bf9-283">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

### <a name="aspnet-core-module-stdout-log-azure-app-service"></a><span data-ttu-id="43bf9-284">Protokol stdout v modulu ASP.NET Core (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="43bf9-284">ASP.NET Core Module stdout log (Azure App Service)</span></span>

<span data-ttu-id="43bf9-285">Protokol stdout modulu ASP.NET Coree často zaznamenává užitečné chybové zprávy, které se nenašly v protokolu událostí aplikace.</span><span class="sxs-lookup"><span data-stu-id="43bf9-285">The ASP.NET Core Module stdout log often records useful error messages not found in the Application Event Log.</span></span> <span data-ttu-id="43bf9-286">Povolení a zobrazení protokolů stdout:</span><span class="sxs-lookup"><span data-stu-id="43bf9-286">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="43bf9-287">Přejděte do okna **diagnostikovat a řešit problémy** v Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="43bf9-287">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="43bf9-288">V části **Vybrat kategorii problému**vyberte tlačítko pro **webovou aplikaci** .</span><span class="sxs-lookup"><span data-stu-id="43bf9-288">Under **SELECT PROBLEM CATEGORY**, select the **Web App Down** button.</span></span>
1. <span data-ttu-id="43bf9-289">V části **navrhovaná řešení** > **Povolit přesměrování protokolu stdout**kliknutím na tlačítko **otevřete konzolu Kudu a upravte soubor Web. config**.</span><span class="sxs-lookup"><span data-stu-id="43bf9-289">Under **Suggested Solutions** > **Enable Stdout Log Redirection**, select the button to **Open Kudu Console to edit Web.Config**.</span></span>
1. <span data-ttu-id="43bf9-290">V **konzole diagnostiky**Kudu otevřete složky v **lokalitě** cesty > **wwwroot**.</span><span class="sxs-lookup"><span data-stu-id="43bf9-290">In the Kudu **Diagnostic Console**, open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="43bf9-291">Posuňte se dolů a odhalte soubor *Web. config* v dolní části seznamu.</span><span class="sxs-lookup"><span data-stu-id="43bf9-291">Scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="43bf9-292">Klikněte na ikonu tužky vedle souboru *Web. config* .</span><span class="sxs-lookup"><span data-stu-id="43bf9-292">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="43bf9-293">Nastavte **stdoutLogEnabled** na `true` a změňte cestu **stdoutLogFile** na: `\\?\%home%\LogFiles\stdout`.</span><span class="sxs-lookup"><span data-stu-id="43bf9-293">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="43bf9-294">Vyberte **Uložit** a uložte aktualizovaný soubor *Web. config* .</span><span class="sxs-lookup"><span data-stu-id="43bf9-294">Select **Save** to save the updated *web.config* file.</span></span>
1. <span data-ttu-id="43bf9-295">Vytvořte žádost do aplikace.</span><span class="sxs-lookup"><span data-stu-id="43bf9-295">Make a request to the app.</span></span>
1. <span data-ttu-id="43bf9-296">Vraťte se na Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="43bf9-296">Return to the Azure portal.</span></span> <span data-ttu-id="43bf9-297">V oblasti **vývojové nástroje** vyberte okno **Pokročilé nástroje** .</span><span class="sxs-lookup"><span data-stu-id="43bf9-297">Select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="43bf9-298">Vyberte tlačítko **přejít&rarr;** .</span><span class="sxs-lookup"><span data-stu-id="43bf9-298">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="43bf9-299">Konzola Kudu se otevře v novém okně nebo záložce prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="43bf9-299">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="43bf9-300">Pomocí navigačního panelu v horní části stránky otevřete **konzolu ladění** a vyberte **cmd**.</span><span class="sxs-lookup"><span data-stu-id="43bf9-300">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="43bf9-301">Vyberte složku **soubory protokolů** .</span><span class="sxs-lookup"><span data-stu-id="43bf9-301">Select the **LogFiles** folder.</span></span>
1. <span data-ttu-id="43bf9-302">Zkontrolujte **upravený** sloupec a vyberte ikonu tužky a upravte protokol stdout s nejnovějším datem úpravy.</span><span class="sxs-lookup"><span data-stu-id="43bf9-302">Inspect the **Modified** column and select the pencil icon to edit the stdout log with the latest modification date.</span></span>
1. <span data-ttu-id="43bf9-303">Po otevření souboru protokolu se zobrazí chyba.</span><span class="sxs-lookup"><span data-stu-id="43bf9-303">When the log file opens, the error is displayed.</span></span>

<span data-ttu-id="43bf9-304">Zakázat protokolování stdout při odstraňování potíží:</span><span class="sxs-lookup"><span data-stu-id="43bf9-304">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="43bf9-305">V **diagnostické konzole**Kudu se vraťte do **lokality** cesty > **wwwroot** , aby se soubor *Web. config* vykryl.</span><span class="sxs-lookup"><span data-stu-id="43bf9-305">In the Kudu **Diagnostic Console**, return to the path **site** > **wwwroot** to reveal the *web.config* file.</span></span> <span data-ttu-id="43bf9-306">Otevřete soubor **Web. config** znovu výběrem ikony tužky.</span><span class="sxs-lookup"><span data-stu-id="43bf9-306">Open the **web.config** file again by selecting the pencil icon.</span></span>
1. <span data-ttu-id="43bf9-307">Nastavte **stdoutLogEnabled** na `false`.</span><span class="sxs-lookup"><span data-stu-id="43bf9-307">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="43bf9-308">Vyberte **Uložit** a soubor uložte.</span><span class="sxs-lookup"><span data-stu-id="43bf9-308">Select **Save** to save the file.</span></span>

<span data-ttu-id="43bf9-309">Další informace naleznete v tématu <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span><span class="sxs-lookup"><span data-stu-id="43bf9-309">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

> [!WARNING]
> <span data-ttu-id="43bf9-310">Nepodařilo se zakázat protokol stdout může vést k selhání aplikace nebo serveru.</span><span class="sxs-lookup"><span data-stu-id="43bf9-310">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="43bf9-311">Neexistuje žádné omezení velikosti souboru protokolu nebo počet souborů protokolů, které jsou vytvořeny.</span><span class="sxs-lookup"><span data-stu-id="43bf9-311">There's no limit on log file size or the number of log files created.</span></span> <span data-ttu-id="43bf9-312">K řešení problémů se spouštěním aplikací použijte pouze protokolování STDOUT.</span><span class="sxs-lookup"><span data-stu-id="43bf9-312">Only use stdout logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="43bf9-313">Pro obecné protokolování v aplikaci ASP.NET Core po spuštění použijte knihovnu protokolování, která omezuje velikost souboru protokolu a otočí protokoly.</span><span class="sxs-lookup"><span data-stu-id="43bf9-313">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="43bf9-314">Další informace najdete v tématu [Zprostředkovatelé protokolování třetích stran](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="43bf9-314">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="aspnet-core-module-debug-log-azure-app-service"></a><span data-ttu-id="43bf9-315">Protokol ladění modulu ASP.NET Core (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="43bf9-315">ASP.NET Core Module debug log (Azure App Service)</span></span>

<span data-ttu-id="43bf9-316">Protokol ladění ASP.NET Core modulu poskytuje další, hlubší protokolování z modulu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="43bf9-316">The ASP.NET Core Module debug log provides additional, deeper logging from the ASP.NET Core Module.</span></span> <span data-ttu-id="43bf9-317">Povolení a zobrazení protokolů stdout:</span><span class="sxs-lookup"><span data-stu-id="43bf9-317">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="43bf9-318">Pro povolení rozšířeného diagnostického protokolu proveďte jednu z následujících akcí:</span><span class="sxs-lookup"><span data-stu-id="43bf9-318">To enable the enhanced diagnostic log, perform either of the following:</span></span>
   * <span data-ttu-id="43bf9-319">Postupujte podle pokynů v [rozšířených diagnostických protokolech](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) a nakonfigurujte aplikaci pro rozšířené diagnostické protokolování.</span><span class="sxs-lookup"><span data-stu-id="43bf9-319">Follow the instructions in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) to configure the app for an enhanced diagnostic logging.</span></span> <span data-ttu-id="43bf9-320">Znovu nasaďte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="43bf9-320">Redeploy the app.</span></span>
   * <span data-ttu-id="43bf9-321">Přidejte `<handlerSettings>` zobrazené v [rozšířených diagnostických protokolech](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) do souboru *Web. config* živé aplikace pomocí konzoly Kudu:</span><span class="sxs-lookup"><span data-stu-id="43bf9-321">Add the `<handlerSettings>` shown in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) to the live app's *web.config* file using the Kudu console:</span></span>
     1. <span data-ttu-id="43bf9-322">Otevřete **Rozšířené nástroje** v oblasti **vývojové nástroje** .</span><span class="sxs-lookup"><span data-stu-id="43bf9-322">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="43bf9-323">Vyberte tlačítko **přejít&rarr;** .</span><span class="sxs-lookup"><span data-stu-id="43bf9-323">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="43bf9-324">Konzola Kudu se otevře v novém okně nebo záložce prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="43bf9-324">The Kudu console opens in a new browser tab or window.</span></span>
     1. <span data-ttu-id="43bf9-325">Pomocí navigačního panelu v horní části stránky otevřete **konzolu ladění** a vyberte **cmd**.</span><span class="sxs-lookup"><span data-stu-id="43bf9-325">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
     1. <span data-ttu-id="43bf9-326">Otevřete složky v **lokalitě** pro cestu > **wwwroot**.</span><span class="sxs-lookup"><span data-stu-id="43bf9-326">Open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="43bf9-327">Upravte soubor *Web. config* tak, že vyberete tlačítko tužky.</span><span class="sxs-lookup"><span data-stu-id="43bf9-327">Edit the *web.config* file by selecting the pencil button.</span></span> <span data-ttu-id="43bf9-328">Přidejte část `<handlerSettings>`, jak je znázorněno v [rozšířených diagnostických protokolech](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs).</span><span class="sxs-lookup"><span data-stu-id="43bf9-328">Add the `<handlerSettings>` section as shown in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs).</span></span> <span data-ttu-id="43bf9-329">Vyberte tlačítko **Uložit**.</span><span class="sxs-lookup"><span data-stu-id="43bf9-329">Select the **Save** button.</span></span>
1. <span data-ttu-id="43bf9-330">Otevřete **Rozšířené nástroje** v oblasti **vývojové nástroje** .</span><span class="sxs-lookup"><span data-stu-id="43bf9-330">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="43bf9-331">Vyberte tlačítko **přejít&rarr;** .</span><span class="sxs-lookup"><span data-stu-id="43bf9-331">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="43bf9-332">Konzola Kudu se otevře v novém okně nebo záložce prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="43bf9-332">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="43bf9-333">Pomocí navigačního panelu v horní části stránky otevřete **konzolu ladění** a vyberte **cmd**.</span><span class="sxs-lookup"><span data-stu-id="43bf9-333">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="43bf9-334">Otevřete složky v **lokalitě** pro cestu > **wwwroot**.</span><span class="sxs-lookup"><span data-stu-id="43bf9-334">Open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="43bf9-335">Pokud jste nezadali cestu k souboru *aspnetcore-Debug. log* , soubor se zobrazí v seznamu.</span><span class="sxs-lookup"><span data-stu-id="43bf9-335">If you didn't supply a path for the *aspnetcore-debug.log* file, the file appears in the list.</span></span> <span data-ttu-id="43bf9-336">Pokud jste zadali cestu, přejděte do umístění souboru protokolu.</span><span class="sxs-lookup"><span data-stu-id="43bf9-336">If you supplied a path, navigate to the location of the log file.</span></span>
1. <span data-ttu-id="43bf9-337">Otevřete soubor protokolu s tlačítkem tužky vedle názvu souboru.</span><span class="sxs-lookup"><span data-stu-id="43bf9-337">Open the log file with the pencil button next to the file name.</span></span>

<span data-ttu-id="43bf9-338">Zakázat protokolování ladění, pokud je dokončeno odstraňování potíží:</span><span class="sxs-lookup"><span data-stu-id="43bf9-338">Disable debug logging when troubleshooting is complete:</span></span>

<span data-ttu-id="43bf9-339">Chcete-li zakázat rozšířený protokol ladění, proveďte jednu z následujících akcí:</span><span class="sxs-lookup"><span data-stu-id="43bf9-339">To disable the enhanced debug log, perform either of the following:</span></span>

* <span data-ttu-id="43bf9-340">Odeberte `<handlerSettings>` ze souboru *Web. config* místně a pak aplikaci znovu nasaďte.</span><span class="sxs-lookup"><span data-stu-id="43bf9-340">Remove the `<handlerSettings>` from the *web.config* file locally and redeploy the app.</span></span>
* <span data-ttu-id="43bf9-341">Pomocí konzoly Kudu upravte soubor *Web. config* a odeberte oddíl `<handlerSettings>`.</span><span class="sxs-lookup"><span data-stu-id="43bf9-341">Use the Kudu console to edit the *web.config* file and remove the `<handlerSettings>` section.</span></span> <span data-ttu-id="43bf9-342">Uložte soubor.</span><span class="sxs-lookup"><span data-stu-id="43bf9-342">Save the file.</span></span>

<span data-ttu-id="43bf9-343">Další informace naleznete v tématu <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span><span class="sxs-lookup"><span data-stu-id="43bf9-343">For more information, see <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span></span>

> [!WARNING]
> <span data-ttu-id="43bf9-344">Nepovedlo se zakázat protokol ladění, může způsobit selhání aplikace nebo serveru.</span><span class="sxs-lookup"><span data-stu-id="43bf9-344">Failure to disable the debug log can lead to app or server failure.</span></span> <span data-ttu-id="43bf9-345">Velikost souboru protokolu není nijak omezena.</span><span class="sxs-lookup"><span data-stu-id="43bf9-345">There's no limit on log file size.</span></span> <span data-ttu-id="43bf9-346">K řešení problémů se spouštěním aplikace používejte pouze protokolování ladění.</span><span class="sxs-lookup"><span data-stu-id="43bf9-346">Only use debug logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="43bf9-347">Pro obecné protokolování v aplikaci ASP.NET Core po spuštění použijte knihovnu protokolování, která omezuje velikost souboru protokolu a otočí protokoly.</span><span class="sxs-lookup"><span data-stu-id="43bf9-347">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="43bf9-348">Další informace najdete v tématu [Zprostředkovatelé protokolování třetích stran](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="43bf9-348">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="slow-or-hanging-app-azure-app-service"></a><span data-ttu-id="43bf9-349">Pomalá nebo zavěšená aplikace (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="43bf9-349">Slow or hanging app (Azure App Service)</span></span>

<span data-ttu-id="43bf9-350">Pokud aplikace reaguje pomalu nebo přestane reagovat na žádost, přečtěte si následující články:</span><span class="sxs-lookup"><span data-stu-id="43bf9-350">When an app responds slowly or hangs on a request, see the following articles:</span></span>

* [<span data-ttu-id="43bf9-351">Řešení potíží s výkonem pomalých webových aplikací ve službě Azure App Service</span><span class="sxs-lookup"><span data-stu-id="43bf9-351">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="43bf9-352">K zachycení výpisu problémů s přerušovanou výjimkou nebo problémy s výkonem ve webové aplikaci Azure použijte rozšíření pro diagnostiku zhroucení serveru.</span><span class="sxs-lookup"><span data-stu-id="43bf9-352">Use Crash Diagnoser Site Extension to Capture Dump for Intermittent Exception issues or performance issues on Azure Web App</span></span>](https://blogs.msdn.microsoft.com/asiatech/2015/12/28/use-crash-diagnoser-site-extension-to-capture-dump-for-intermittent-exception-issues-or-performance-issues-on-azure-web-app/)

### <a name="monitoring-blades"></a><span data-ttu-id="43bf9-353">Okna monitorování</span><span class="sxs-lookup"><span data-stu-id="43bf9-353">Monitoring blades</span></span>

<span data-ttu-id="43bf9-354">Okna monitorování poskytují alternativní možnosti řešení potíží pro metody popsané dříve v tématu.</span><span class="sxs-lookup"><span data-stu-id="43bf9-354">Monitoring blades provide an alternative troubleshooting experience to the methods described earlier in the topic.</span></span> <span data-ttu-id="43bf9-355">Pomocí těchto oken můžete diagnostikovat chyby 500-Series.</span><span class="sxs-lookup"><span data-stu-id="43bf9-355">These blades can be used to diagnose 500-series errors.</span></span>

<span data-ttu-id="43bf9-356">Potvrďte, že jsou nainstalovaná rozšíření ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="43bf9-356">Confirm that the ASP.NET Core Extensions are installed.</span></span> <span data-ttu-id="43bf9-357">Pokud nejsou rozšíření nainstalována, nainstalujte je ručně:</span><span class="sxs-lookup"><span data-stu-id="43bf9-357">If the extensions aren't installed, install them manually:</span></span>

1. <span data-ttu-id="43bf9-358">V části okno **vývojové nástroje** vyberte okno **rozšíření** .</span><span class="sxs-lookup"><span data-stu-id="43bf9-358">In the **DEVELOPMENT TOOLS** blade section, select the **Extensions** blade.</span></span>
1. <span data-ttu-id="43bf9-359">V seznamu by se měla zobrazit **rozšíření ASP.NET Core** .</span><span class="sxs-lookup"><span data-stu-id="43bf9-359">The **ASP.NET Core Extensions** should appear in the list.</span></span>
1. <span data-ttu-id="43bf9-360">Pokud nejsou rozšíření nainstalovaná, vyberte tlačítko **Přidat** .</span><span class="sxs-lookup"><span data-stu-id="43bf9-360">If the extensions aren't installed, select the **Add** button.</span></span>
1. <span data-ttu-id="43bf9-361">Ze seznamu vyberte **rozšíření ASP.NET Core** .</span><span class="sxs-lookup"><span data-stu-id="43bf9-361">Choose the **ASP.NET Core Extensions** from the list.</span></span>
1. <span data-ttu-id="43bf9-362">Kliknutím na **OK** přijměte právní podmínky.</span><span class="sxs-lookup"><span data-stu-id="43bf9-362">Select **OK** to accept the legal terms.</span></span>
1. <span data-ttu-id="43bf9-363">V okně **Přidat rozšíření** vyberte **OK** .</span><span class="sxs-lookup"><span data-stu-id="43bf9-363">Select **OK** on the **Add extension** blade.</span></span>
1. <span data-ttu-id="43bf9-364">Informační automaticky otevíraná zpráva indikuje, že se rozšíření úspěšně nainstalovala.</span><span class="sxs-lookup"><span data-stu-id="43bf9-364">An informational pop-up message indicates when the extensions are successfully installed.</span></span>

<span data-ttu-id="43bf9-365">Pokud není povoleno protokolování stdout, postupujte následovně:</span><span class="sxs-lookup"><span data-stu-id="43bf9-365">If stdout logging isn't enabled, follow these steps:</span></span>

1. <span data-ttu-id="43bf9-366">V Azure Portal v oblasti **vývojové nástroje** vyberte okno **Pokročilé nástroje** .</span><span class="sxs-lookup"><span data-stu-id="43bf9-366">In the Azure portal, select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="43bf9-367">Vyberte tlačítko **přejít&rarr;** .</span><span class="sxs-lookup"><span data-stu-id="43bf9-367">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="43bf9-368">Konzola Kudu se otevře v novém okně nebo záložce prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="43bf9-368">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="43bf9-369">Pomocí navigačního panelu v horní části stránky otevřete **konzolu ladění** a vyberte **cmd**.</span><span class="sxs-lookup"><span data-stu-id="43bf9-369">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="43bf9-370">Otevřete složky v **lokalitě** pro cestu > **wwwroot** a posuňte se dolů, aby se soubor *Web. config* vypnul v dolní části seznamu.</span><span class="sxs-lookup"><span data-stu-id="43bf9-370">Open the folders to the path **site** > **wwwroot** and scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="43bf9-371">Klikněte na ikonu tužky vedle souboru *Web. config* .</span><span class="sxs-lookup"><span data-stu-id="43bf9-371">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="43bf9-372">Nastavte **stdoutLogEnabled** na `true` a změňte cestu **stdoutLogFile** na: `\\?\%home%\LogFiles\stdout`.</span><span class="sxs-lookup"><span data-stu-id="43bf9-372">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="43bf9-373">Vyberte **Uložit** a uložte aktualizovaný soubor *Web. config* .</span><span class="sxs-lookup"><span data-stu-id="43bf9-373">Select **Save** to save the updated *web.config* file.</span></span>

<span data-ttu-id="43bf9-374">Pokračovat k aktivaci protokolování diagnostiky:</span><span class="sxs-lookup"><span data-stu-id="43bf9-374">Proceed to activate diagnostic logging:</span></span>

1. <span data-ttu-id="43bf9-375">V Azure Portal vyberte okno **diagnostické protokoly** .</span><span class="sxs-lookup"><span data-stu-id="43bf9-375">In the Azure portal, select the **Diagnostics logs** blade.</span></span>
1. <span data-ttu-id="43bf9-376">Vyberte přepínač **zapnuto** pro **protokolování aplikace (systém souborů)** a **podrobné chybové zprávy**.</span><span class="sxs-lookup"><span data-stu-id="43bf9-376">Select the **On** switch for **Application Logging (Filesystem)** and **Detailed error messages**.</span></span> <span data-ttu-id="43bf9-377">V horní části okna vyberte tlačítko **Uložit** .</span><span class="sxs-lookup"><span data-stu-id="43bf9-377">Select the **Save** button at the top of the blade.</span></span>
1. <span data-ttu-id="43bf9-378">Chcete-li zahrnout trasování chybných požadavků, označované také jako protokolování událostí neúspěšných požadavků na FREB, vyberte přepínač **zapnuto** pro **trasování chybných požadavků**.</span><span class="sxs-lookup"><span data-stu-id="43bf9-378">To include failed request tracing, also known as Failed Request Event Buffering (FREB) logging, select the **On** switch for **Failed request tracing**.</span></span>
1. <span data-ttu-id="43bf9-379">Vyberte okno **log Stream** , které je uvedeno hned pod oknem **diagnostické protokoly** na portálu.</span><span class="sxs-lookup"><span data-stu-id="43bf9-379">Select the **Log stream** blade, which is listed immediately under the **Diagnostics logs** blade in the portal.</span></span>
1. <span data-ttu-id="43bf9-380">Vytvořte žádost do aplikace.</span><span class="sxs-lookup"><span data-stu-id="43bf9-380">Make a request to the app.</span></span>
1. <span data-ttu-id="43bf9-381">V datech streamu protokolu se označuje příčina chyby.</span><span class="sxs-lookup"><span data-stu-id="43bf9-381">Within the log stream data, the cause of the error is indicated.</span></span>

<span data-ttu-id="43bf9-382">Nezapomeňte zakázat protokolování stdout po dokončení řešení potíží.</span><span class="sxs-lookup"><span data-stu-id="43bf9-382">Be sure to disable stdout logging when troubleshooting is complete.</span></span>

<span data-ttu-id="43bf9-383">Zobrazení protokolů pro trasování chybných požadavků (protokoly FREB):</span><span class="sxs-lookup"><span data-stu-id="43bf9-383">To view the failed request tracing logs (FREB logs):</span></span>

1. <span data-ttu-id="43bf9-384">Přejděte do okna **diagnostikovat a řešit problémy** v Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="43bf9-384">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="43bf9-385">V oblasti **nástroje podpory** v postranním panelu vyberte **protokoly pro trasování chybných požadavků** .</span><span class="sxs-lookup"><span data-stu-id="43bf9-385">Select **Failed Request Tracing Logs** from the **SUPPORT TOOLS** area of the sidebar.</span></span>

<span data-ttu-id="43bf9-386">V [části Povolení protokolování diagnostiky pro webové aplikace v Azure App Service tématu](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) a [Nejčastější dotazy týkající se výkonu aplikace pro Web Apps v Azure: návody zapnout trasování chybných požadavků?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing) Další informace.</span><span class="sxs-lookup"><span data-stu-id="43bf9-386">See [Failed request traces section of the Enable diagnostics logging for web apps in Azure App Service topic](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) and the [Application performance FAQs for Web Apps in Azure: How do I turn on failed request tracing?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing) for more information.</span></span>

<span data-ttu-id="43bf9-387">Další informace najdete v tématu [Povolení protokolování diagnostiky pro webové aplikace v Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log).</span><span class="sxs-lookup"><span data-stu-id="43bf9-387">For more information, see [Enable diagnostics logging for web apps in Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log).</span></span>

> [!WARNING]
> <span data-ttu-id="43bf9-388">Nepodařilo se zakázat protokol stdout může vést k selhání aplikace nebo serveru.</span><span class="sxs-lookup"><span data-stu-id="43bf9-388">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="43bf9-389">Neexistuje žádné omezení velikosti souboru protokolu nebo počet souborů protokolů, které jsou vytvořeny.</span><span class="sxs-lookup"><span data-stu-id="43bf9-389">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="43bf9-390">Pro rutiny protokolování v aplikaci ASP.NET Core, použijte protokolování knihovnu, která omezuje velikost souboru protokolu a otočí protokoly.</span><span class="sxs-lookup"><span data-stu-id="43bf9-390">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="43bf9-391">Další informace najdete v tématu [Zprostředkovatelé protokolování třetích stran](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="43bf9-391">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

## <a name="troubleshoot-on-iis"></a><span data-ttu-id="43bf9-392">Řešení potíží ve službě IIS</span><span class="sxs-lookup"><span data-stu-id="43bf9-392">Troubleshoot on IIS</span></span>

### <a name="application-event-log-iis"></a><span data-ttu-id="43bf9-393">Protokol událostí aplikace (IIS)</span><span class="sxs-lookup"><span data-stu-id="43bf9-393">Application Event Log (IIS)</span></span>

<span data-ttu-id="43bf9-394">Přístup k protokolu událostí aplikace:</span><span class="sxs-lookup"><span data-stu-id="43bf9-394">Access the Application Event Log:</span></span>

1. <span data-ttu-id="43bf9-395">Otevřete nabídku Start, vyhledejte *Prohlížeč událostí*a vyberte aplikaci **Prohlížeč událostí** .</span><span class="sxs-lookup"><span data-stu-id="43bf9-395">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="43bf9-396">V **Prohlížeč událostí**otevřete uzel **protokoly systému Windows** .</span><span class="sxs-lookup"><span data-stu-id="43bf9-396">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="43bf9-397">Výběrem **aplikace** otevřete protokol událostí aplikace.</span><span class="sxs-lookup"><span data-stu-id="43bf9-397">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="43bf9-398">Vyhledejte chyby související s selhání aplikace.</span><span class="sxs-lookup"><span data-stu-id="43bf9-398">Search for errors associated with the failing app.</span></span> <span data-ttu-id="43bf9-399">Chyby mají ve *zdrojovém* sloupci hodnotu *modulu IIS AspNetCore* nebo *modul IIS Express AspNetCore* .</span><span class="sxs-lookup"><span data-stu-id="43bf9-399">Errors have a value of *IIS AspNetCore Module* or *IIS Express AspNetCore Module* in the *Source* column.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="43bf9-400">Spuštění aplikace příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="43bf9-400">Run the app at a command prompt</span></span>

<span data-ttu-id="43bf9-401">Mnoho chyb při spuštění nevytvářejí užitečné informace v protokolu událostí aplikace.</span><span class="sxs-lookup"><span data-stu-id="43bf9-401">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="43bf9-402">Příčin některých chyb můžete najít spuštěním aplikace v příkazovém řádku v hostitelském systému.</span><span class="sxs-lookup"><span data-stu-id="43bf9-402">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span>

#### <a name="framework-dependent-deployment"></a><span data-ttu-id="43bf9-403">Nasazení závisí na architektuře</span><span class="sxs-lookup"><span data-stu-id="43bf9-403">Framework-dependent deployment</span></span>

<span data-ttu-id="43bf9-404">Pokud aplikace je [nasazení závislé na rozhraní](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span><span class="sxs-lookup"><span data-stu-id="43bf9-404">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

1. <span data-ttu-id="43bf9-405">Na příkazovém řádku přejděte do složky pro nasazení a spusťte aplikaci spuštěním sestavení aplikace pomocí příkazu *dotnet. exe*.</span><span class="sxs-lookup"><span data-stu-id="43bf9-405">At a command prompt, navigate to the deployment folder and run the app by executing the app's assembly with *dotnet.exe*.</span></span> <span data-ttu-id="43bf9-406">V následujícím příkazu nahraďte název sestavení aplikace pro \<assembly_name >: `dotnet .\<assembly_name>.dll`.</span><span class="sxs-lookup"><span data-stu-id="43bf9-406">In the following command, substitute the name of the app's assembly for \<assembly_name>: `dotnet .\<assembly_name>.dll`.</span></span>
1. <span data-ttu-id="43bf9-407">Výstup z aplikace zobrazuje všechny chyby konzoly je zapsán do okna konzoly.</span><span class="sxs-lookup"><span data-stu-id="43bf9-407">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="43bf9-408">Je-li této chybě dojde při požadavku na aplikaci, vytvořte žádost na hostitele a port, kde Kestrel naslouchá.</span><span class="sxs-lookup"><span data-stu-id="43bf9-408">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="43bf9-409">Pomocí výchozího hostitele a příspěvku vytvořte žádost o `http://localhost:5000/`.</span><span class="sxs-lookup"><span data-stu-id="43bf9-409">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="43bf9-410">Aplikace reaguje, obvykle na adrese Kestrel koncový bod, tím je pravděpodobnější týkající se konfigurace hostování a méně pravděpodobné, že v rámci aplikace.</span><span class="sxs-lookup"><span data-stu-id="43bf9-410">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

#### <a name="self-contained-deployment"></a><span data-ttu-id="43bf9-411">Samostatná nasazení</span><span class="sxs-lookup"><span data-stu-id="43bf9-411">Self-contained deployment</span></span>

<span data-ttu-id="43bf9-412">Pokud je aplikace samostatná, [nasazení](/dotnet/core/deploying/#self-contained-deployments-scd):</span><span class="sxs-lookup"><span data-stu-id="43bf9-412">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

1. <span data-ttu-id="43bf9-413">Na příkazovém řádku přejděte do složky pro nasazení a spuštění spustitelného souboru aplikace.</span><span class="sxs-lookup"><span data-stu-id="43bf9-413">At a command prompt, navigate to the deployment folder and run the app's executable.</span></span> <span data-ttu-id="43bf9-414">V následujícím příkazu nahraďte název sestavení aplikace pro \<assembly_name >: `<assembly_name>.exe`.</span><span class="sxs-lookup"><span data-stu-id="43bf9-414">In the following command, substitute the name of the app's assembly for \<assembly_name>: `<assembly_name>.exe`.</span></span>
1. <span data-ttu-id="43bf9-415">Výstup z aplikace zobrazuje všechny chyby konzoly je zapsán do okna konzoly.</span><span class="sxs-lookup"><span data-stu-id="43bf9-415">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="43bf9-416">Je-li této chybě dojde při požadavku na aplikaci, vytvořte žádost na hostitele a port, kde Kestrel naslouchá.</span><span class="sxs-lookup"><span data-stu-id="43bf9-416">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="43bf9-417">Pomocí výchozího hostitele a příspěvku vytvořte žádost o `http://localhost:5000/`.</span><span class="sxs-lookup"><span data-stu-id="43bf9-417">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="43bf9-418">Aplikace reaguje, obvykle na adrese Kestrel koncový bod, tím je pravděpodobnější týkající se konfigurace hostování a méně pravděpodobné, že v rámci aplikace.</span><span class="sxs-lookup"><span data-stu-id="43bf9-418">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

### <a name="aspnet-core-module-stdout-log-iis"></a><span data-ttu-id="43bf9-419">Protokol stdout v modulu ASP.NET Core (IIS)</span><span class="sxs-lookup"><span data-stu-id="43bf9-419">ASP.NET Core Module stdout log (IIS)</span></span>

<span data-ttu-id="43bf9-420">Povolení a zobrazení protokolů stdout:</span><span class="sxs-lookup"><span data-stu-id="43bf9-420">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="43bf9-421">Přejděte do složky pro nasazení webu v hostitelském systému.</span><span class="sxs-lookup"><span data-stu-id="43bf9-421">Navigate to the site's deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="43bf9-422">Pokud složka *logs* není k dispozici, vytvořte složku.</span><span class="sxs-lookup"><span data-stu-id="43bf9-422">If the *logs* folder isn't present, create the folder.</span></span> <span data-ttu-id="43bf9-423">Pokyny k tomu, jak povolit MSBuild pro vytvoření složky *logs* v nasazení automaticky, najdete v tématu [Struktura adresáře](xref:host-and-deploy/directory-structure) .</span><span class="sxs-lookup"><span data-stu-id="43bf9-423">For instructions on how to enable MSBuild to create the *logs* folder in the deployment automatically, see the [Directory structure](xref:host-and-deploy/directory-structure) topic.</span></span>
1. <span data-ttu-id="43bf9-424">Upravte soubor *Web. config* .</span><span class="sxs-lookup"><span data-stu-id="43bf9-424">Edit the *web.config* file.</span></span> <span data-ttu-id="43bf9-425">Nastavte **stdoutLogEnabled** na `true` a změňte cestu **stdoutLogFile** tak, aby odkazovala na složku *logs* (například `.\logs\stdout`).</span><span class="sxs-lookup"><span data-stu-id="43bf9-425">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to point to the *logs* folder (for example, `.\logs\stdout`).</span></span> <span data-ttu-id="43bf9-426">`stdout` v cestě je předpona názvu souboru protokolu.</span><span class="sxs-lookup"><span data-stu-id="43bf9-426">`stdout` in the path is the log file name prefix.</span></span> <span data-ttu-id="43bf9-427">Časové razítko, id procesu a příponu souboru jsou přidány automaticky při vytvoření protokolu.</span><span class="sxs-lookup"><span data-stu-id="43bf9-427">A timestamp, process id, and file extension are added automatically when the log is created.</span></span> <span data-ttu-id="43bf9-428">Při použití `stdout` jako předpony názvu souboru se typický soubor protokolu jmenuje *stdout_20180205184032_5412. log*.</span><span class="sxs-lookup"><span data-stu-id="43bf9-428">Using `stdout` as the file name prefix, a typical log file is named *stdout_20180205184032_5412.log*.</span></span>
1. <span data-ttu-id="43bf9-429">Zajistěte, aby identita fondu aplikací měla oprávnění k zápisu do složky *logs* .</span><span class="sxs-lookup"><span data-stu-id="43bf9-429">Ensure your application pool's identity has write permissions to the *logs* folder.</span></span>
1. <span data-ttu-id="43bf9-430">Uložte aktualizovaný soubor *Web. config* .</span><span class="sxs-lookup"><span data-stu-id="43bf9-430">Save the updated *web.config* file.</span></span>
1. <span data-ttu-id="43bf9-431">Vytvořte žádost do aplikace.</span><span class="sxs-lookup"><span data-stu-id="43bf9-431">Make a request to the app.</span></span>
1. <span data-ttu-id="43bf9-432">Přejděte do složky *logs* .</span><span class="sxs-lookup"><span data-stu-id="43bf9-432">Navigate to the *logs* folder.</span></span> <span data-ttu-id="43bf9-433">Vyhledání a otevření protokolu nejnovější stdout.</span><span class="sxs-lookup"><span data-stu-id="43bf9-433">Find and open the most recent stdout log.</span></span>
1. <span data-ttu-id="43bf9-434">Studie v protokolu chyb.</span><span class="sxs-lookup"><span data-stu-id="43bf9-434">Study the log for errors.</span></span>

<span data-ttu-id="43bf9-435">Zakázat protokolování stdout při odstraňování potíží:</span><span class="sxs-lookup"><span data-stu-id="43bf9-435">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="43bf9-436">Upravte soubor *Web. config* .</span><span class="sxs-lookup"><span data-stu-id="43bf9-436">Edit the *web.config* file.</span></span>
1. <span data-ttu-id="43bf9-437">Nastavte **stdoutLogEnabled** na `false`.</span><span class="sxs-lookup"><span data-stu-id="43bf9-437">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="43bf9-438">Uložte soubor.</span><span class="sxs-lookup"><span data-stu-id="43bf9-438">Save the file.</span></span>

<span data-ttu-id="43bf9-439">Další informace naleznete v tématu <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span><span class="sxs-lookup"><span data-stu-id="43bf9-439">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

> [!WARNING]
> <span data-ttu-id="43bf9-440">Nepodařilo se zakázat protokol stdout může vést k selhání aplikace nebo serveru.</span><span class="sxs-lookup"><span data-stu-id="43bf9-440">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="43bf9-441">Neexistuje žádné omezení velikosti souboru protokolu nebo počet souborů protokolů, které jsou vytvořeny.</span><span class="sxs-lookup"><span data-stu-id="43bf9-441">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="43bf9-442">Pro rutiny protokolování v aplikaci ASP.NET Core, použijte protokolování knihovnu, která omezuje velikost souboru protokolu a otočí protokoly.</span><span class="sxs-lookup"><span data-stu-id="43bf9-442">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="43bf9-443">Další informace najdete v tématu [Zprostředkovatelé protokolování třetích stran](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="43bf9-443">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="aspnet-core-module-debug-log-iis"></a><span data-ttu-id="43bf9-444">Protokol ladění modulu ASP.NET Core (IIS)</span><span class="sxs-lookup"><span data-stu-id="43bf9-444">ASP.NET Core Module debug log (IIS)</span></span>

<span data-ttu-id="43bf9-445">Přidejte následující nastavení obslužné rutiny do souboru *Web. config* aplikace, aby bylo možné povolit protokol ladění ASP.NET Core modulu:</span><span class="sxs-lookup"><span data-stu-id="43bf9-445">Add the following handler settings to the app's *web.config* file to enable ASP.NET Core Module debug log:</span></span>

```xml
<aspNetCore ...>
  <handlerSettings>
    <handlerSetting name="debugLevel" value="file" />
    <handlerSetting name="debugFile" value="c:\temp\ancm.log" />
  </handlerSettings>
</aspNetCore>
```

<span data-ttu-id="43bf9-446">Potvrďte, že cesta zadaná pro protokol existuje a že identita fondu aplikací má oprávnění k zápisu do umístění.</span><span class="sxs-lookup"><span data-stu-id="43bf9-446">Confirm that the path specified for the log exists and that the app pool's identity has write permissions to the location.</span></span>

<span data-ttu-id="43bf9-447">Další informace naleznete v tématu <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span><span class="sxs-lookup"><span data-stu-id="43bf9-447">For more information, see <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span></span>

### <a name="enable-the-developer-exception-page"></a><span data-ttu-id="43bf9-448">Povolit na stránce výjimek pro vývojáře</span><span class="sxs-lookup"><span data-stu-id="43bf9-448">Enable the Developer Exception Page</span></span>

<span data-ttu-id="43bf9-449">[Proměnnou prostředí `ASPNETCORE_ENVIRONMENT` lze přidat do souboru Web. config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) , aby bylo možné aplikaci spustit ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="43bf9-449">The `ASPNETCORE_ENVIRONMENT` [environment variable can be added to web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) to run the app in the Development environment.</span></span> <span data-ttu-id="43bf9-450">Pokud se prostředí nepřepisuje při spuštění aplikace `UseEnvironment` v Tvůrci hostitele, nastavení proměnné prostředí umožní, aby se při spuštění aplikace zobrazila [Stránka s výjimkou vývojářů](xref:fundamentals/error-handling) .</span><span class="sxs-lookup"><span data-stu-id="43bf9-450">As long as the environment isn't overridden in app startup by `UseEnvironment` on the host builder, setting the environment variable allows the [Developer Exception Page](xref:fundamentals/error-handling) to appear when the app is run.</span></span>

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

<span data-ttu-id="43bf9-451">Nastavení proměnné prostředí pro `ASPNETCORE_ENVIRONMENT` se doporučuje jenom pro použití na pracovních a testovacích serverech, které nejsou přístupné pro Internet.</span><span class="sxs-lookup"><span data-stu-id="43bf9-451">Setting the environment variable for `ASPNETCORE_ENVIRONMENT` is only recommended for use on staging and testing servers that aren't exposed to the Internet.</span></span> <span data-ttu-id="43bf9-452">Po vyřešení potíží odeberte proměnnou prostředí ze souboru *Web. config* .</span><span class="sxs-lookup"><span data-stu-id="43bf9-452">Remove the environment variable from the *web.config* file after troubleshooting.</span></span> <span data-ttu-id="43bf9-453">Informace o nastavení proměnných prostředí v *souboru Web. config*naleznete v tématu [environmentVariables Child element of aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).</span><span class="sxs-lookup"><span data-stu-id="43bf9-453">For information on setting environment variables in *web.config*, see [environmentVariables child element of aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).</span></span>

### <a name="obtain-data-from-an-app"></a><span data-ttu-id="43bf9-454">Získání dat z aplikace</span><span class="sxs-lookup"><span data-stu-id="43bf9-454">Obtain data from an app</span></span>

<span data-ttu-id="43bf9-455">Pokud aplikace je schopná reagovat na požadavky, získáte žádost o připojení a další data z aplikace pomocí terminálu vložené middlewaru.</span><span class="sxs-lookup"><span data-stu-id="43bf9-455">If an app is capable of responding to requests, obtain request, connection, and additional data from the app using terminal inline middleware.</span></span> <span data-ttu-id="43bf9-456">Další informace a ukázku kódu naleznete v tématu <xref:test/troubleshoot#obtain-data-from-an-app>.</span><span class="sxs-lookup"><span data-stu-id="43bf9-456">For more information and sample code, see <xref:test/troubleshoot#obtain-data-from-an-app>.</span></span>

### <a name="slow-or-hanging-app-iis"></a><span data-ttu-id="43bf9-457">Pomalá nebo zavěšená aplikace (IIS)</span><span class="sxs-lookup"><span data-stu-id="43bf9-457">Slow or hanging app (IIS)</span></span>

<span data-ttu-id="43bf9-458">*Výpis stavu* systému je snímek paměti systému a může vám pomůže určit příčinu selhání aplikace, selhání při spuštění nebo pomalé aplikace.</span><span class="sxs-lookup"><span data-stu-id="43bf9-458">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="43bf9-459">Aplikace selže nebo dojde k výjimce.</span><span class="sxs-lookup"><span data-stu-id="43bf9-459">App crashes or encounters an exception</span></span>

<span data-ttu-id="43bf9-460">Získat a analyzovat výpis z [zasílání zpráv o chybách systému Windows (WER)](/windows/desktop/wer/windows-error-reporting):</span><span class="sxs-lookup"><span data-stu-id="43bf9-460">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="43bf9-461">Vytvořte složku, do které se budou ukládat soubory s výpisem stavu systému na `c:\dumps`.</span><span class="sxs-lookup"><span data-stu-id="43bf9-461">Create a folder to hold crash dump files at `c:\dumps`.</span></span> <span data-ttu-id="43bf9-462">Fond aplikací musí mít ke složce přístup pro zápis.</span><span class="sxs-lookup"><span data-stu-id="43bf9-462">The app pool must have write access to the folder.</span></span>
1. <span data-ttu-id="43bf9-463">Spusťte [skript prostředí PowerShell pro EnableDumps](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1):</span><span class="sxs-lookup"><span data-stu-id="43bf9-463">Run the [EnableDumps PowerShell script](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1):</span></span>
   * <span data-ttu-id="43bf9-464">Pokud aplikace používá [model hostování v rámci procesu](xref:host-and-deploy/iis/index#in-process-hosting-model), spusťte skript pro *W3wp. exe*:</span><span class="sxs-lookup"><span data-stu-id="43bf9-464">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\EnableDumps w3wp.exe c:\dumps
     ```

   * <span data-ttu-id="43bf9-465">Pokud aplikace používá [model hostování mimo proces](xref:host-and-deploy/iis/index#out-of-process-hosting-model), spusťte skript pro příkaz *dotnet. exe*:</span><span class="sxs-lookup"><span data-stu-id="43bf9-465">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\EnableDumps dotnet.exe c:\dumps
     ```

1. <span data-ttu-id="43bf9-466">Spusťte aplikaci za podmínek, které způsobí, že dojde k chybě.</span><span class="sxs-lookup"><span data-stu-id="43bf9-466">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="43bf9-467">Po chybě spusťte [skript PowerShellu DisableDumps](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1):</span><span class="sxs-lookup"><span data-stu-id="43bf9-467">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1):</span></span>
   * <span data-ttu-id="43bf9-468">Pokud aplikace používá [model hostování v rámci procesu](xref:host-and-deploy/iis/index#in-process-hosting-model), spusťte skript pro *W3wp. exe*:</span><span class="sxs-lookup"><span data-stu-id="43bf9-468">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\DisableDumps w3wp.exe
     ```

   * <span data-ttu-id="43bf9-469">Pokud aplikace používá [model hostování mimo proces](xref:host-and-deploy/iis/index#out-of-process-hosting-model), spusťte skript pro příkaz *dotnet. exe*:</span><span class="sxs-lookup"><span data-stu-id="43bf9-469">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\DisableDumps dotnet.exe
     ```

<span data-ttu-id="43bf9-470">Po selhání aplikace a dokončení shromažďování výpisu je možné aplikaci ukončit normálně.</span><span class="sxs-lookup"><span data-stu-id="43bf9-470">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="43bf9-471">Skript PowerShellu nakonfiguruje WER a shromáždí až pět výpisů paměti pro každou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="43bf9-471">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="43bf9-472">Výpisy stavu systému můžou zabírat velké množství místa na disku (každý až několik gigabajtů).</span><span class="sxs-lookup"><span data-stu-id="43bf9-472">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="43bf9-473">Aplikace přestane reagovat, dojde k chybě při spuštění nebo se spustí normálně.</span><span class="sxs-lookup"><span data-stu-id="43bf9-473">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="43bf9-474">Když *aplikace přestane reagovat (zastaví* se, ale nejedná se o chybu), selže během spuštění nebo se spustí normálně, podívejte [se na soubory výpisu paměti v uživatelském režimu: zvolením nejlepšího](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) nástroje vyberte vhodný nástroj pro vytváření výpisu.</span><span class="sxs-lookup"><span data-stu-id="43bf9-474">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="43bf9-475">Analýza výpisu paměti</span><span class="sxs-lookup"><span data-stu-id="43bf9-475">Analyze the dump</span></span>

<span data-ttu-id="43bf9-476">Výpis paměti lze analyzovat pomocí několika přístupů.</span><span class="sxs-lookup"><span data-stu-id="43bf9-476">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="43bf9-477">Další informace najdete v tématu [Analýza souboru s výpisem stavu v uživatelském režimu](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span><span class="sxs-lookup"><span data-stu-id="43bf9-477">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="clear-package-caches"></a><span data-ttu-id="43bf9-478">Vymazat mezipaměti balíčků</span><span class="sxs-lookup"><span data-stu-id="43bf9-478">Clear package caches</span></span>

<span data-ttu-id="43bf9-479">Funkční aplikace může po upgradu .NET Core SDK ve vývojovém počítači nebo změně verzí balíčku v rámci aplikace selhat okamžitě.</span><span class="sxs-lookup"><span data-stu-id="43bf9-479">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="43bf9-480">V některých případech osamocené balíčky mohou narušit funkce aplikace při provádění hlavní upgrady.</span><span class="sxs-lookup"><span data-stu-id="43bf9-480">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="43bf9-481">Většina těchto problémů můžete opravit podle těchto pokynů:</span><span class="sxs-lookup"><span data-stu-id="43bf9-481">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="43bf9-482">Odstraňte složky *bin* a *obj* .</span><span class="sxs-lookup"><span data-stu-id="43bf9-482">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="43bf9-483">Pomocí příkazu [dotnet All--Clear](/dotnet/core/tools/dotnet-nuget-locals) z příkazového prostředí vymažte mezipaměť balíčku.</span><span class="sxs-lookup"><span data-stu-id="43bf9-483">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="43bf9-484">Mazání mezipamětí balíčků lze také provést pomocí nástroje [NuGet. exe](https://www.nuget.org/downloads) a spuštěním příkazu `nuget locals all -clear`.</span><span class="sxs-lookup"><span data-stu-id="43bf9-484">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="43bf9-485">*NuGet. exe* není sada instalovaná instalace s desktopovým operačním systémem Windows a musí se získat samostatně z [webu NuGet](https://www.nuget.org/downloads).</span><span class="sxs-lookup"><span data-stu-id="43bf9-485">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="43bf9-486">Obnovit a znovu sestavte projekt.</span><span class="sxs-lookup"><span data-stu-id="43bf9-486">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="43bf9-487">Před opětovným nasazením aplikace odstraňte všechny soubory ve složce pro nasazení na serveru.</span><span class="sxs-lookup"><span data-stu-id="43bf9-487">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="43bf9-488">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="43bf9-488">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:fundamentals/error-handling>
* <xref:host-and-deploy/aspnet-core-module>

### <a name="azure-documentation"></a><span data-ttu-id="43bf9-489">Dokumentace k Azure</span><span class="sxs-lookup"><span data-stu-id="43bf9-489">Azure documentation</span></span>

* [<span data-ttu-id="43bf9-490">Application Insights pro ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="43bf9-490">Application Insights for ASP.NET Core</span></span>](/azure/application-insights/app-insights-asp-net-core)
* [<span data-ttu-id="43bf9-491">Část vzdálené ladění webových aplikací řešení potíží s webovou aplikací v Azure App Service pomocí sady Visual Studio</span><span class="sxs-lookup"><span data-stu-id="43bf9-491">Remote debugging web apps section of Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug)
* [<span data-ttu-id="43bf9-492">Přehled diagnostiky Azure App Service</span><span class="sxs-lookup"><span data-stu-id="43bf9-492">Azure App Service diagnostics overview</span></span>](/azure/app-service/app-service-diagnostics)
* [<span data-ttu-id="43bf9-493">Postup: Monitorování aplikací ve službě Azure App Service</span><span class="sxs-lookup"><span data-stu-id="43bf9-493">How to: Monitor Apps in Azure App Service</span></span>](/azure/app-service/web-sites-monitor)
* [<span data-ttu-id="43bf9-494">Řešení potíží s webovou aplikací v Azure App Service pomocí sady Visual Studio</span><span class="sxs-lookup"><span data-stu-id="43bf9-494">Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [<span data-ttu-id="43bf9-495">Řešení chyb HTTP z "502 špatné brány" a "nedostupné služby 503" v Azure Web Apps</span><span class="sxs-lookup"><span data-stu-id="43bf9-495">Troubleshoot HTTP errors of "502 bad gateway" and "503 service unavailable" in your Azure web apps</span></span>](/azure/app-service/app-service-web-troubleshoot-http-502-http-503)
* [<span data-ttu-id="43bf9-496">Řešení potíží s výkonem pomalých webových aplikací ve službě Azure App Service</span><span class="sxs-lookup"><span data-stu-id="43bf9-496">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="43bf9-497">Nejčastější dotazy týkající se výkonu aplikací pro Web Apps v Azure</span><span class="sxs-lookup"><span data-stu-id="43bf9-497">Application performance FAQs for Web Apps in Azure</span></span>](/azure/app-service/app-service-web-availability-performance-application-issues-faq)
* [<span data-ttu-id="43bf9-498">Sandbox webové aplikace Azure (omezení spuštění App Service Runtime)</span><span class="sxs-lookup"><span data-stu-id="43bf9-498">Azure Web App sandbox (App Service runtime execution limitations)</span></span>](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)
* [<span data-ttu-id="43bf9-499">Azure pátek: Azure App Service diagnostické prostředí a řešení potíží (12 minut video)</span><span class="sxs-lookup"><span data-stu-id="43bf9-499">Azure Friday: Azure App Service Diagnostic and Troubleshooting Experience (12-minute video)</span></span>](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)

### <a name="visual-studio-documentation"></a><span data-ttu-id="43bf9-500">Dokumentace sady Visual Studio</span><span class="sxs-lookup"><span data-stu-id="43bf9-500">Visual Studio documentation</span></span>

* [<span data-ttu-id="43bf9-501">Vzdálené ladění ASP.NET Core ve službě IIS v Azure v aplikaci Visual Studio 2017</span><span class="sxs-lookup"><span data-stu-id="43bf9-501">Remote Debug ASP.NET Core on IIS in Azure in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-azure)
* [<span data-ttu-id="43bf9-502">Vzdálené ladění ASP.NET Core na vzdáleném počítači IIS v aplikaci Visual Studio 2017</span><span class="sxs-lookup"><span data-stu-id="43bf9-502">Remote Debug ASP.NET Core on a Remote IIS Computer in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-aspnet-on-a-remote-iis-computer)
* [<span data-ttu-id="43bf9-503">Další informace k ladění pomocí sady Visual Studio</span><span class="sxs-lookup"><span data-stu-id="43bf9-503">Learn to debug using Visual Studio</span></span>](/visualstudio/debugger/getting-started-with-the-debugger)

### <a name="visual-studio-code-documentation"></a><span data-ttu-id="43bf9-504">Dokumentace k Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="43bf9-504">Visual Studio Code documentation</span></span>

* [<span data-ttu-id="43bf9-505">Ladění pomocí Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="43bf9-505">Debugging with Visual Studio Code</span></span>](https://code.visualstudio.com/docs/editor/debugging)

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="43bf9-506">Tento článek poskytuje informace o běžných chybách při spuštění aplikací a pokyny k diagnostice chyb při nasazení aplikace do Azure App Service nebo IIS:</span><span class="sxs-lookup"><span data-stu-id="43bf9-506">This article provides information on common app startup errors and instructions on how to diagnose errors when an app is deployed to Azure App Service or IIS:</span></span>

[<span data-ttu-id="43bf9-507">Chyby při spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="43bf9-507">App startup errors</span></span>](#app-startup-errors)  
<span data-ttu-id="43bf9-508">Vysvětluje běžné spouštění scénářů stavového kódu HTTP.</span><span class="sxs-lookup"><span data-stu-id="43bf9-508">Explains common startup HTTP status code scenarios.</span></span>

[<span data-ttu-id="43bf9-509">Řešení potíží na Azure App Service</span><span class="sxs-lookup"><span data-stu-id="43bf9-509">Troubleshoot on Azure App Service</span></span>](#troubleshoot-on-azure-app-service)  
<span data-ttu-id="43bf9-510">Poskytuje rady pro řešení potíží pro aplikace nasazené do Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="43bf9-510">Provides troubleshooting advice for apps deployed to Azure App Service.</span></span>

[<span data-ttu-id="43bf9-511">Odstraňování potíží ve službě IIS</span><span class="sxs-lookup"><span data-stu-id="43bf9-511">Troubleshoot on IIS</span></span>](#troubleshoot-on-iis)  
<span data-ttu-id="43bf9-512">Poskytuje rady pro řešení potíží pro aplikace nasazené do služby IIS nebo místně spuštěné v IIS Express.</span><span class="sxs-lookup"><span data-stu-id="43bf9-512">Provides troubleshooting advice for apps deployed to IIS or running on IIS Express locally.</span></span> <span data-ttu-id="43bf9-513">Pokyny platí pro nasazení systémů Windows Server a Desktop v systému Windows.</span><span class="sxs-lookup"><span data-stu-id="43bf9-513">The guidance applies to both Windows Server and Windows desktop deployments.</span></span>

[<span data-ttu-id="43bf9-514">Vymazat mezipaměti balíčků</span><span class="sxs-lookup"><span data-stu-id="43bf9-514">Clear package caches</span></span>](#clear-package-caches)  
<span data-ttu-id="43bf9-515">Vysvětluje, co dělat v případě, že při provádění podstatných upgradů nebo změně verze balíčku dojde k přerušení aplikace v nesouvislých balíčcích.</span><span class="sxs-lookup"><span data-stu-id="43bf9-515">Explains what to do when incoherent packages break an app when performing major upgrades or changing package versions.</span></span>

[<span data-ttu-id="43bf9-516">Další materiály</span><span class="sxs-lookup"><span data-stu-id="43bf9-516">Additional resources</span></span>](#additional-resources)  
<span data-ttu-id="43bf9-517">Seznam dalších témat pro odstraňování potíží.</span><span class="sxs-lookup"><span data-stu-id="43bf9-517">Lists additional troubleshooting topics.</span></span>

## <a name="app-startup-errors"></a><span data-ttu-id="43bf9-518">Chyby při spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="43bf9-518">App startup errors</span></span>

<span data-ttu-id="43bf9-519">V aplikaci Visual Studio je výchozím nastavením projektu ASP.NET Core [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hostování během ladění.</span><span class="sxs-lookup"><span data-stu-id="43bf9-519">In Visual Studio, an ASP.NET Core project defaults to [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hosting during debugging.</span></span> <span data-ttu-id="43bf9-520">*502,5 – selhání procesu* nebo *500,30 – chyba* , ke které dojde při místním ladění, se dá diagnostikovat pomocí Rady v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="43bf9-520">A *502.5 - Process Failure* or a *500.30 - Start Failure* that occurs when debugging locally can be diagnosed using the advice in this topic.</span></span>

### <a name="40314-forbidden"></a><span data-ttu-id="43bf9-521">403,14 zakázané</span><span class="sxs-lookup"><span data-stu-id="43bf9-521">403.14 Forbidden</span></span>

<span data-ttu-id="43bf9-522">Aplikaci se nepodařilo spustit.</span><span class="sxs-lookup"><span data-stu-id="43bf9-522">The app fails to start.</span></span> <span data-ttu-id="43bf9-523">Protokoluje se následující chyba:</span><span class="sxs-lookup"><span data-stu-id="43bf9-523">The following error is logged:</span></span>

```
The Web server is configured to not list the contents of this directory.
```

<span data-ttu-id="43bf9-524">K této chybě obvykle dochází v důsledku poškozeného nasazení v hostitelském systému, který zahrnuje kterýkoli z následujících scénářů:</span><span class="sxs-lookup"><span data-stu-id="43bf9-524">The error is usually caused by a broken deployment on the hosting system, which includes any of the following scenarios:</span></span>

* <span data-ttu-id="43bf9-525">Aplikace je nasazená do chybné složky v hostitelském systému.</span><span class="sxs-lookup"><span data-stu-id="43bf9-525">The app is deployed to the wrong folder on the hosting system.</span></span>
* <span data-ttu-id="43bf9-526">Procesu nasazení se nepodařilo přesunout všechny soubory a složky aplikace do složky pro nasazení v hostitelském systému.</span><span class="sxs-lookup"><span data-stu-id="43bf9-526">The deployment process failed to move all of the app's files and folders to the deployment folder on the hosting system.</span></span>
* <span data-ttu-id="43bf9-527">V nasazení chybí soubor *Web. config* , nebo je obsah souboru *Web. config* poškozený.</span><span class="sxs-lookup"><span data-stu-id="43bf9-527">The *web.config* file is missing from the deployment, or the *web.config* file contents are malformed.</span></span>

<span data-ttu-id="43bf9-528">Proveďte následující kroky:</span><span class="sxs-lookup"><span data-stu-id="43bf9-528">Perform the following steps:</span></span>

1. <span data-ttu-id="43bf9-529">Odstraňte všechny soubory a složky ze složky pro nasazení v hostitelském systému.</span><span class="sxs-lookup"><span data-stu-id="43bf9-529">Delete all of the files and folders from the deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="43bf9-530">Znovu nasaďte obsah složky pro *publikování* aplikace do hostitelského systému pomocí běžné metody nasazení, jako je například Visual Studio, PowerShell nebo ruční nasazení:</span><span class="sxs-lookup"><span data-stu-id="43bf9-530">Redeploy the contents of the app's *publish* folder to the hosting system using your normal method of deployment, such as Visual Studio, PowerShell, or manual deployment:</span></span>
   * <span data-ttu-id="43bf9-531">Zkontrolujte, zda je v nasazení přítomen soubor *Web. config* a zda jeho obsah je správný.</span><span class="sxs-lookup"><span data-stu-id="43bf9-531">Confirm that the *web.config* file is present in the deployment and that its contents are correct.</span></span>
   * <span data-ttu-id="43bf9-532">Při hostování na Azure App Service potvrďte, že je aplikace nasazená do `D:\home\site\wwwroot` složky.</span><span class="sxs-lookup"><span data-stu-id="43bf9-532">When hosting on Azure App Service, confirm that the app is deployed to the `D:\home\site\wwwroot` folder.</span></span>
   * <span data-ttu-id="43bf9-533">Když je aplikace hostovaná službou IIS, zkontrolujte, že je aplikace nasazená na **fyzickou cestu** služby IIS, která se zobrazuje v **základním nastavení** **Správce služby IIS**.</span><span class="sxs-lookup"><span data-stu-id="43bf9-533">When the app is hosted by IIS, confirm that the app is deployed to the IIS **Physical path** shown in **IIS Manager**'s **Basic Settings**.</span></span>
1. <span data-ttu-id="43bf9-534">Ověřte, zda jsou všechny soubory a složky aplikace nasazeny porovnáním nasazení v hostitelském systému s obsahem složky *publikování* projektu.</span><span class="sxs-lookup"><span data-stu-id="43bf9-534">Confirm that all of the app's files and folders are deployed by comparing the deployment on the hosting system to the contents of the project's *publish* folder.</span></span>

<span data-ttu-id="43bf9-535">Další informace o rozložení publikované aplikace ASP.NET Core najdete v tématu <xref:host-and-deploy/directory-structure>.</span><span class="sxs-lookup"><span data-stu-id="43bf9-535">For more information on the layout of a published ASP.NET Core app, see <xref:host-and-deploy/directory-structure>.</span></span> <span data-ttu-id="43bf9-536">Další informace o souboru *Web. config* naleznete v tématu <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>.</span><span class="sxs-lookup"><span data-stu-id="43bf9-536">For more information on the *web.config* file, see <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>.</span></span>

### <a name="500-internal-server-error"></a><span data-ttu-id="43bf9-537">500 – Interní chyba serveru</span><span class="sxs-lookup"><span data-stu-id="43bf9-537">500 Internal Server Error</span></span>

<span data-ttu-id="43bf9-538">Spuštění aplikace, ale chybu brání splnění žádosti. na serveru.</span><span class="sxs-lookup"><span data-stu-id="43bf9-538">The app starts, but an error prevents the server from fulfilling the request.</span></span>

<span data-ttu-id="43bf9-539">Při spuštění nebo při vytváření odpovědi, k této chybě dochází v kódu aplikace.</span><span class="sxs-lookup"><span data-stu-id="43bf9-539">This error occurs within the app's code during startup or while creating a response.</span></span> <span data-ttu-id="43bf9-540">Odpověď možná neobsahuje žádný obsah, nebo se odpověď může zobrazit jako *500 interní chyba serveru* v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="43bf9-540">The response may contain no content, or the response may appear as a *500 Internal Server Error* in the browser.</span></span> <span data-ttu-id="43bf9-541">V protokolu událostí aplikace obvykle hlásí, že aplikace se normálně spustit.</span><span class="sxs-lookup"><span data-stu-id="43bf9-541">The Application Event Log usually states that the app started normally.</span></span> <span data-ttu-id="43bf9-542">Z pohledu serveru, který je správný.</span><span class="sxs-lookup"><span data-stu-id="43bf9-542">From the server's perspective, that's correct.</span></span> <span data-ttu-id="43bf9-543">Aplikace začal, ale nemůže generovat platnou odpověď.</span><span class="sxs-lookup"><span data-stu-id="43bf9-543">The app did start, but it can't generate a valid response.</span></span> <span data-ttu-id="43bf9-544">Spusťte aplikaci na příkazovém řádku na serveru nebo povolte řešení potíží pomocí protokolu stdout modulu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="43bf9-544">Run the app at a command prompt on the server or enable the ASP.NET Core Module stdout log to troubleshoot the problem.</span></span>

### <a name="5000-in-process-handler-load-failure"></a><span data-ttu-id="43bf9-545">500.0 v procesu selhání načtení obslužné rutiny</span><span class="sxs-lookup"><span data-stu-id="43bf9-545">500.0 In-Process Handler Load Failure</span></span>

<span data-ttu-id="43bf9-546">Pracovní proces se nezdaří.</span><span class="sxs-lookup"><span data-stu-id="43bf9-546">The worker process fails.</span></span> <span data-ttu-id="43bf9-547">Aplikace se nespustí.</span><span class="sxs-lookup"><span data-stu-id="43bf9-547">The app doesn't start.</span></span>

<span data-ttu-id="43bf9-548">[Modulu ASP.NET Core](xref:host-and-deploy/aspnet-core-module) se nepovedlo najít modul CLR .NET Core a najít obslužnou rutinu žádosti v procesu (*aspnetcorev2_inprocess. dll*).</span><span class="sxs-lookup"><span data-stu-id="43bf9-548">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) fails to find the .NET Core CLR and find the in-process request handler (*aspnetcorev2_inprocess.dll*).</span></span> <span data-ttu-id="43bf9-549">Zkontrolujte, jestli:</span><span class="sxs-lookup"><span data-stu-id="43bf9-549">Check that:</span></span>

* <span data-ttu-id="43bf9-550">Aplikace cílí na balíček NuGet [Microsoft. AspNetCore. Server. IIS](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IIS) nebo na [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="43bf9-550">The app targets either the [Microsoft.AspNetCore.Server.IIS](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IIS) NuGet package or the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>
* <span data-ttu-id="43bf9-551">Verze rozhraní framework ASP.NET Core sdílené cíle, které aplikace nainstalované v cílovém počítači.</span><span class="sxs-lookup"><span data-stu-id="43bf9-551">The version of the ASP.NET Core shared framework that the app targets is installed on the target machine.</span></span>

### <a name="5000-out-of-process-handler-load-failure"></a><span data-ttu-id="43bf9-552">500.0 Chyba načtení out-Of-Process obslužné rutiny</span><span class="sxs-lookup"><span data-stu-id="43bf9-552">500.0 Out-Of-Process Handler Load Failure</span></span>

<span data-ttu-id="43bf9-553">Pracovní proces se nezdaří.</span><span class="sxs-lookup"><span data-stu-id="43bf9-553">The worker process fails.</span></span> <span data-ttu-id="43bf9-554">Aplikace se nespustí.</span><span class="sxs-lookup"><span data-stu-id="43bf9-554">The app doesn't start.</span></span>

<span data-ttu-id="43bf9-555">[Modulu ASP.NET Core](xref:host-and-deploy/aspnet-core-module) se nepodařilo najít obslužnou rutinu žádosti mimo proces hostování.</span><span class="sxs-lookup"><span data-stu-id="43bf9-555">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) fails to find the out-of-process hosting request handler.</span></span> <span data-ttu-id="43bf9-556">Ujistěte se, že *aspnetcorev2_outofprocess. dll* se nachází v podsložce vedle *aspnetcorev2. dll*.</span><span class="sxs-lookup"><span data-stu-id="43bf9-556">Make sure the *aspnetcorev2_outofprocess.dll* is present in a subfolder next to *aspnetcorev2.dll*.</span></span>

### <a name="5025-process-failure"></a><span data-ttu-id="43bf9-557">502.5 zpracovat selhání</span><span class="sxs-lookup"><span data-stu-id="43bf9-557">502.5 Process Failure</span></span>

<span data-ttu-id="43bf9-558">Pracovní proces se nezdaří.</span><span class="sxs-lookup"><span data-stu-id="43bf9-558">The worker process fails.</span></span> <span data-ttu-id="43bf9-559">Aplikace se nespustí.</span><span class="sxs-lookup"><span data-stu-id="43bf9-559">The app doesn't start.</span></span>

<span data-ttu-id="43bf9-560">[Modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module) se pokusí spustit pracovní proces, ale jeho spuštění se nezdařilo.</span><span class="sxs-lookup"><span data-stu-id="43bf9-560">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the worker process but it fails to start.</span></span> <span data-ttu-id="43bf9-561">Příčinou selhání spuštění procesu může být obvykle určení z položek protokolu událostí aplikace a protokolu stdout modulu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="43bf9-561">The cause of a process startup failure can usually be determined from entries in the Application Event Log and the ASP.NET Core Module stdout log.</span></span>

<span data-ttu-id="43bf9-562">Běžné chyby je, že aplikace je špatně nakonfigurovaný. kvůli cílení na určitou verzi rozhraní framework sdílené ASP.NET Core, který není k dispozici.</span><span class="sxs-lookup"><span data-stu-id="43bf9-562">A common failure condition is the app is misconfigured due to targeting a version of the ASP.NET Core shared framework that isn't present.</span></span> <span data-ttu-id="43bf9-563">Zkontrolujte, jaké verze rozhraní framework ASP.NET Core sdílené jsou nainstalovány v cílovém počítači.</span><span class="sxs-lookup"><span data-stu-id="43bf9-563">Check which versions of the ASP.NET Core shared framework are installed on the target machine.</span></span> <span data-ttu-id="43bf9-564">*Sdílené rozhraní* je sada sestavení (soubory *. dll* ), které jsou nainstalovány na počítači a na které odkazuje Metapackage jako `Microsoft.AspNetCore.App`.</span><span class="sxs-lookup"><span data-stu-id="43bf9-564">The *shared framework* is the set of assemblies (*.dll* files) that are installed on the machine and referenced by a metapackage such as `Microsoft.AspNetCore.App`.</span></span> <span data-ttu-id="43bf9-565">Odkaz Metapackage může určovat minimální požadovanou verzi.</span><span class="sxs-lookup"><span data-stu-id="43bf9-565">The metapackage reference can specify a minimum required version.</span></span> <span data-ttu-id="43bf9-566">Další informace najdete v tématu [sdílené rozhraní](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span><span class="sxs-lookup"><span data-stu-id="43bf9-566">For more information, see [The shared framework](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span></span>

<span data-ttu-id="43bf9-567">Chybová stránka *selhání procesu 502,5* se vrátí v případě, že při selhání hostování nebo aplikace dojde k chybě pracovního procesu:</span><span class="sxs-lookup"><span data-stu-id="43bf9-567">The *502.5 Process Failure* error page is returned when a hosting or app misconfiguration causes the worker process to fail:</span></span>

### <a name="failed-to-start-application-errorcode-0x800700c1"></a><span data-ttu-id="43bf9-568">Nepovedlo se spustit aplikaci (kód chyby "0x800700c1")</span><span class="sxs-lookup"><span data-stu-id="43bf9-568">Failed to start application (ErrorCode '0x800700c1')</span></span>

```
EventID: 1010
Source: IIS AspNetCore Module V2
Failed to start application '/LM/W3SVC/6/ROOT/', ErrorCode '0x800700c1'.
```

<span data-ttu-id="43bf9-569">Aplikaci se nepovedlo spustit, protože se nepovedlo načíst sestavení aplikace ( *. dll*).</span><span class="sxs-lookup"><span data-stu-id="43bf9-569">The app failed to start because the app's assembly (*.dll*) couldn't be loaded.</span></span>

<span data-ttu-id="43bf9-570">Tato chyba nastane, pokud došlo k neshodě bitové verze mezi publikované aplikace a proces w3wp/iisexpress.</span><span class="sxs-lookup"><span data-stu-id="43bf9-570">This error occurs when there's a bitness mismatch between the published app and the w3wp/iisexpress process.</span></span>

<span data-ttu-id="43bf9-571">Ověřte správnost nastavení 32-bit fondu aplikací:</span><span class="sxs-lookup"><span data-stu-id="43bf9-571">Confirm that the app pool's 32-bit setting is correct:</span></span>

1. <span data-ttu-id="43bf9-572">Vyberte fond aplikací v **fondech aplikací**Správce služby IIS.</span><span class="sxs-lookup"><span data-stu-id="43bf9-572">Select the app pool in IIS Manager's **Application Pools**.</span></span>
1. <span data-ttu-id="43bf9-573">V části **Upravit fond aplikací** na panelu **Akce** vyberte **Upřesnit nastavení** .</span><span class="sxs-lookup"><span data-stu-id="43bf9-573">Select **Advanced Settings** under **Edit Application Pool** in the **Actions** panel.</span></span>
1. <span data-ttu-id="43bf9-574">Nastavte **možnost povolit 32 – bitové aplikace**:</span><span class="sxs-lookup"><span data-stu-id="43bf9-574">Set **Enable 32-Bit Applications**:</span></span>
   * <span data-ttu-id="43bf9-575">Pokud nasazujete 32 (x86) aplikaci, nastavte hodnotu na `True`.</span><span class="sxs-lookup"><span data-stu-id="43bf9-575">If deploying a 32-bit (x86) app, set the value to `True`.</span></span>
   * <span data-ttu-id="43bf9-576">Pokud nasazujete 64 aplikaci (x64), nastavte hodnotu na `False`.</span><span class="sxs-lookup"><span data-stu-id="43bf9-576">If deploying a 64-bit (x64) app, set the value to `False`.</span></span>

<span data-ttu-id="43bf9-577">Ověřte, že mezi vlastností `<Platform>` MSBuild v souboru projektu a publikovaným bitová verzeem aplikace nedochází ke konfliktu.</span><span class="sxs-lookup"><span data-stu-id="43bf9-577">Confirm that there isn't a conflict between a `<Platform>` MSBuild property in the project file and the published bitness of the app.</span></span>

### <a name="connection-reset"></a><span data-ttu-id="43bf9-578">Obnovení připojení</span><span class="sxs-lookup"><span data-stu-id="43bf9-578">Connection reset</span></span>

<span data-ttu-id="43bf9-579">Pokud dojde k chybě po odeslání hlaviček, je příliš pozdě pro server, který odešle **500 interní chybu serveru** , když dojde k chybě.</span><span class="sxs-lookup"><span data-stu-id="43bf9-579">If an error occurs after the headers are sent, it's too late for the server to send a **500 Internal Server Error** when an error occurs.</span></span> <span data-ttu-id="43bf9-580">Často se to stane, když dojde k chybě při serializaci složitých objektů pro odpověď.</span><span class="sxs-lookup"><span data-stu-id="43bf9-580">This often happens when an error occurs during the serialization of complex objects for a response.</span></span> <span data-ttu-id="43bf9-581">Tento typ chyby se zobrazí jako chyba *resetování připojení* na klientovi.</span><span class="sxs-lookup"><span data-stu-id="43bf9-581">This type of error appears as a *connection reset* error on the client.</span></span> <span data-ttu-id="43bf9-582">[Protokolování aplikace](xref:fundamentals/logging/index) může pomoct řešit tyto typy chyb.</span><span class="sxs-lookup"><span data-stu-id="43bf9-582">[Application logging](xref:fundamentals/logging/index) can help troubleshoot these types of errors.</span></span>

### <a name="default-startup-limits"></a><span data-ttu-id="43bf9-583">Výchozí omezení při spuštění</span><span class="sxs-lookup"><span data-stu-id="43bf9-583">Default startup limits</span></span>

<span data-ttu-id="43bf9-584">[ASP.NET Core modul](xref:host-and-deploy/aspnet-core-module) je nakonfigurovaný s výchozí *startupTimeLimitou* 120 sekund.</span><span class="sxs-lookup"><span data-stu-id="43bf9-584">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) is configured with a default *startupTimeLimit* of 120 seconds.</span></span> <span data-ttu-id="43bf9-585">Když necháte na výchozí hodnotu, aplikace může trvat až dvě minuty, spusťte před modulu protokoly selhání procesu.</span><span class="sxs-lookup"><span data-stu-id="43bf9-585">When left at the default value, an app may take up to two minutes to start before the module logs a process failure.</span></span> <span data-ttu-id="43bf9-586">Informace o konfiguraci modulu naleznete v tématu [atributy elementu aspNetCore](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span><span class="sxs-lookup"><span data-stu-id="43bf9-586">For information on configuring the module, see [Attributes of the aspNetCore element](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span></span>

## <a name="troubleshoot-on-azure-app-service"></a><span data-ttu-id="43bf9-587">Řešení potíží na Azure App Service</span><span class="sxs-lookup"><span data-stu-id="43bf9-587">Troubleshoot on Azure App Service</span></span>

[!INCLUDE [Azure App Service Preview Notice](~/includes/azure-apps-preview-notice.md)]

### <a name="application-event-log-azure-app-service"></a><span data-ttu-id="43bf9-588">Protokol událostí aplikace (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="43bf9-588">Application Event Log (Azure App Service)</span></span>

<span data-ttu-id="43bf9-589">Chcete-li získat přístup k protokolu událostí aplikace, použijte okno **Diagnostika a řešení problémů** v Azure Portal:</span><span class="sxs-lookup"><span data-stu-id="43bf9-589">To access the Application Event Log, use the **Diagnose and solve problems** blade in the Azure portal:</span></span>

1. <span data-ttu-id="43bf9-590">V Azure Portal otevřete aplikaci v **App Services**.</span><span class="sxs-lookup"><span data-stu-id="43bf9-590">In the Azure portal, open the app in **App Services**.</span></span>
1. <span data-ttu-id="43bf9-591">Vyberte možnost **diagnostikovat a vyřešit problémy**.</span><span class="sxs-lookup"><span data-stu-id="43bf9-591">Select **Diagnose and solve problems**.</span></span>
1. <span data-ttu-id="43bf9-592">Vyberte **diagnostické nástroje** záhlaví.</span><span class="sxs-lookup"><span data-stu-id="43bf9-592">Select the **Diagnostic Tools** heading.</span></span>
1. <span data-ttu-id="43bf9-593">V nabídce **nástroje podpory**vyberte tlačítko **události aplikace** .</span><span class="sxs-lookup"><span data-stu-id="43bf9-593">Under **Support Tools**, select the **Application Events** button.</span></span>
1. <span data-ttu-id="43bf9-594">Projděte si nejnovější chybu, kterou poskytla položka *IIS AspNetCoreModule* nebo *IIS AspNetCoreModule v2* ve **zdrojovém** sloupci.</span><span class="sxs-lookup"><span data-stu-id="43bf9-594">Examine the latest error provided by the *IIS AspNetCoreModule* or *IIS AspNetCoreModule V2* entry in the **Source** column.</span></span>

<span data-ttu-id="43bf9-595">Alternativou k použití okna **diagnostikovat a řešit problémy** je kontrola souboru protokolu událostí aplikace přímo pomocí [Kudu](https://github.com/projectkudu/kudu/wiki):</span><span class="sxs-lookup"><span data-stu-id="43bf9-595">An alternative to using the **Diagnose and solve problems** blade is to examine the Application Event Log file directly using [Kudu](https://github.com/projectkudu/kudu/wiki):</span></span>

1. <span data-ttu-id="43bf9-596">Otevřete **Rozšířené nástroje** v oblasti **vývojové nástroje** .</span><span class="sxs-lookup"><span data-stu-id="43bf9-596">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="43bf9-597">Vyberte tlačítko **přejít&rarr;** .</span><span class="sxs-lookup"><span data-stu-id="43bf9-597">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="43bf9-598">Konzola Kudu se otevře v novém okně nebo záložce prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="43bf9-598">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="43bf9-599">Pomocí navigačního panelu v horní části stránky otevřete **konzolu ladění** a vyberte **cmd**.</span><span class="sxs-lookup"><span data-stu-id="43bf9-599">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="43bf9-600">Otevřete složku **soubory protokolů** .</span><span class="sxs-lookup"><span data-stu-id="43bf9-600">Open the **LogFiles** folder.</span></span>
1. <span data-ttu-id="43bf9-601">Vyberte ikonu tužky vedle souboru *EventLog. XML* .</span><span class="sxs-lookup"><span data-stu-id="43bf9-601">Select the pencil icon next to the *eventlog.xml* file.</span></span>
1. <span data-ttu-id="43bf9-602">Projděte si protokol.</span><span class="sxs-lookup"><span data-stu-id="43bf9-602">Examine the log.</span></span> <span data-ttu-id="43bf9-603">Posuňte se do dolní části protokolu, abyste viděli nejaktuálnější události.</span><span class="sxs-lookup"><span data-stu-id="43bf9-603">Scroll to the bottom of the log to see the most recent events.</span></span>

### <a name="run-the-app-in-the-kudu-console"></a><span data-ttu-id="43bf9-604">Spuštění aplikace v konzole Kudu</span><span class="sxs-lookup"><span data-stu-id="43bf9-604">Run the app in the Kudu console</span></span>

<span data-ttu-id="43bf9-605">Mnoho chyb při spuštění nevytvářejí užitečné informace v protokolu událostí aplikace.</span><span class="sxs-lookup"><span data-stu-id="43bf9-605">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="43bf9-606">Tuto chybu můžete zjistit spuštěním aplikace v konzole vzdáleného spuštění [Kudu](https://github.com/projectkudu/kudu/wiki) :</span><span class="sxs-lookup"><span data-stu-id="43bf9-606">You can run the app in the [Kudu](https://github.com/projectkudu/kudu/wiki) Remote Execution Console to discover the error:</span></span>

1. <span data-ttu-id="43bf9-607">Otevřete **Rozšířené nástroje** v oblasti **vývojové nástroje** .</span><span class="sxs-lookup"><span data-stu-id="43bf9-607">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="43bf9-608">Vyberte tlačítko **přejít&rarr;** .</span><span class="sxs-lookup"><span data-stu-id="43bf9-608">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="43bf9-609">Konzola Kudu se otevře v novém okně nebo záložce prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="43bf9-609">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="43bf9-610">Pomocí navigačního panelu v horní části stránky otevřete **konzolu ladění** a vyberte **cmd**.</span><span class="sxs-lookup"><span data-stu-id="43bf9-610">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>

#### <a name="test-a-32-bit-x86-app"></a><span data-ttu-id="43bf9-611">Testování 32 (x86) aplikace</span><span class="sxs-lookup"><span data-stu-id="43bf9-611">Test a 32-bit (x86) app</span></span>

<span data-ttu-id="43bf9-612">**Aktuální verze**</span><span class="sxs-lookup"><span data-stu-id="43bf9-612">**Current release**</span></span>

1. `cd d:\home\site\wwwroot`
1. <span data-ttu-id="43bf9-613">Spusťte aplikaci:</span><span class="sxs-lookup"><span data-stu-id="43bf9-613">Run the app:</span></span>
   * <span data-ttu-id="43bf9-614">Pokud aplikace je [nasazení závislé na rozhraní](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span><span class="sxs-lookup"><span data-stu-id="43bf9-614">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

     ```dotnetcli
     dotnet .\{ASSEMBLY NAME}.dll
     ```

   * <span data-ttu-id="43bf9-615">Pokud je aplikace samostatná, [nasazení](/dotnet/core/deploying/#self-contained-deployments-scd):</span><span class="sxs-lookup"><span data-stu-id="43bf9-615">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

     ```console
     {ASSEMBLY NAME}.exe
     ```

<span data-ttu-id="43bf9-616">Výstup konzoly z aplikace, v němž se zobrazují všechny chyby, je kanálem do konzoly Kudu.</span><span class="sxs-lookup"><span data-stu-id="43bf9-616">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="43bf9-617">**Nasazení závislé na architektuře spuštěné ve verzi Preview**</span><span class="sxs-lookup"><span data-stu-id="43bf9-617">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="43bf9-618">*Vyžaduje instalaci rozšíření webu ASP.NET Core {VERSION} (x86) Runtime.*</span><span class="sxs-lookup"><span data-stu-id="43bf9-618">*Requires installing the ASP.NET Core {VERSION} (x86) Runtime site extension.*</span></span>

1. <span data-ttu-id="43bf9-619">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32` (`{X.Y}` je verze modulu runtime)</span><span class="sxs-lookup"><span data-stu-id="43bf9-619">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="43bf9-620">Spusťte aplikaci: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="43bf9-620">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="43bf9-621">Výstup konzoly z aplikace, v němž se zobrazují všechny chyby, je kanálem do konzoly Kudu.</span><span class="sxs-lookup"><span data-stu-id="43bf9-621">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

#### <a name="test-a-64-bit-x64-app"></a><span data-ttu-id="43bf9-622">Testování 64 aplikace (x64)</span><span class="sxs-lookup"><span data-stu-id="43bf9-622">Test a 64-bit (x64) app</span></span>

<span data-ttu-id="43bf9-623">**Aktuální verze**</span><span class="sxs-lookup"><span data-stu-id="43bf9-623">**Current release**</span></span>

* <span data-ttu-id="43bf9-624">Pokud je aplikace [nasazením závislého na rozhraní](/dotnet/core/deploying/#framework-dependent-deployments-fdd)64 (x64):</span><span class="sxs-lookup"><span data-stu-id="43bf9-624">If the app is a 64-bit (x64) [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>
  1. `cd D:\Program Files\dotnet`
  1. <span data-ttu-id="43bf9-625">Spusťte aplikaci: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="43bf9-625">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>
* <span data-ttu-id="43bf9-626">Pokud je aplikace samostatná, [nasazení](/dotnet/core/deploying/#self-contained-deployments-scd):</span><span class="sxs-lookup"><span data-stu-id="43bf9-626">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>
  1. `cd D:\home\site\wwwroot`
  1. <span data-ttu-id="43bf9-627">Spusťte aplikaci: `{ASSEMBLY NAME}.exe`</span><span class="sxs-lookup"><span data-stu-id="43bf9-627">Run the app: `{ASSEMBLY NAME}.exe`</span></span>

<span data-ttu-id="43bf9-628">Výstup konzoly z aplikace, v němž se zobrazují všechny chyby, je kanálem do konzoly Kudu.</span><span class="sxs-lookup"><span data-stu-id="43bf9-628">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="43bf9-629">**Nasazení závislé na architektuře spuštěné ve verzi Preview**</span><span class="sxs-lookup"><span data-stu-id="43bf9-629">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="43bf9-630">*Vyžaduje instalaci rozšíření webu ASP.NET Core {VERSION} (x64) Runtime.*</span><span class="sxs-lookup"><span data-stu-id="43bf9-630">*Requires installing the ASP.NET Core {VERSION} (x64) Runtime site extension.*</span></span>

1. <span data-ttu-id="43bf9-631">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64` (`{X.Y}` je verze modulu runtime)</span><span class="sxs-lookup"><span data-stu-id="43bf9-631">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="43bf9-632">Spusťte aplikaci: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="43bf9-632">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="43bf9-633">Výstup konzoly z aplikace, v němž se zobrazují všechny chyby, je kanálem do konzoly Kudu.</span><span class="sxs-lookup"><span data-stu-id="43bf9-633">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

### <a name="aspnet-core-module-stdout-log-azure-app-service"></a><span data-ttu-id="43bf9-634">Protokol stdout v modulu ASP.NET Core (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="43bf9-634">ASP.NET Core Module stdout log (Azure App Service)</span></span>

<span data-ttu-id="43bf9-635">Protokol stdout modulu ASP.NET Coree často zaznamenává užitečné chybové zprávy, které se nenašly v protokolu událostí aplikace.</span><span class="sxs-lookup"><span data-stu-id="43bf9-635">The ASP.NET Core Module stdout log often records useful error messages not found in the Application Event Log.</span></span> <span data-ttu-id="43bf9-636">Povolení a zobrazení protokolů stdout:</span><span class="sxs-lookup"><span data-stu-id="43bf9-636">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="43bf9-637">Přejděte do okna **diagnostikovat a řešit problémy** v Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="43bf9-637">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="43bf9-638">V části **Vybrat kategorii problému**vyberte tlačítko pro **webovou aplikaci** .</span><span class="sxs-lookup"><span data-stu-id="43bf9-638">Under **SELECT PROBLEM CATEGORY**, select the **Web App Down** button.</span></span>
1. <span data-ttu-id="43bf9-639">V části **navrhovaná řešení** > **Povolit přesměrování protokolu stdout**kliknutím na tlačítko **otevřete konzolu Kudu a upravte soubor Web. config**.</span><span class="sxs-lookup"><span data-stu-id="43bf9-639">Under **Suggested Solutions** > **Enable Stdout Log Redirection**, select the button to **Open Kudu Console to edit Web.Config**.</span></span>
1. <span data-ttu-id="43bf9-640">V **konzole diagnostiky**Kudu otevřete složky v **lokalitě** cesty > **wwwroot**.</span><span class="sxs-lookup"><span data-stu-id="43bf9-640">In the Kudu **Diagnostic Console**, open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="43bf9-641">Posuňte se dolů a odhalte soubor *Web. config* v dolní části seznamu.</span><span class="sxs-lookup"><span data-stu-id="43bf9-641">Scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="43bf9-642">Klikněte na ikonu tužky vedle souboru *Web. config* .</span><span class="sxs-lookup"><span data-stu-id="43bf9-642">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="43bf9-643">Nastavte **stdoutLogEnabled** na `true` a změňte cestu **stdoutLogFile** na: `\\?\%home%\LogFiles\stdout`.</span><span class="sxs-lookup"><span data-stu-id="43bf9-643">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="43bf9-644">Vyberte **Uložit** a uložte aktualizovaný soubor *Web. config* .</span><span class="sxs-lookup"><span data-stu-id="43bf9-644">Select **Save** to save the updated *web.config* file.</span></span>
1. <span data-ttu-id="43bf9-645">Vytvořte žádost do aplikace.</span><span class="sxs-lookup"><span data-stu-id="43bf9-645">Make a request to the app.</span></span>
1. <span data-ttu-id="43bf9-646">Vraťte se na Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="43bf9-646">Return to the Azure portal.</span></span> <span data-ttu-id="43bf9-647">V oblasti **vývojové nástroje** vyberte okno **Pokročilé nástroje** .</span><span class="sxs-lookup"><span data-stu-id="43bf9-647">Select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="43bf9-648">Vyberte tlačítko **přejít&rarr;** .</span><span class="sxs-lookup"><span data-stu-id="43bf9-648">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="43bf9-649">Konzola Kudu se otevře v novém okně nebo záložce prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="43bf9-649">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="43bf9-650">Pomocí navigačního panelu v horní části stránky otevřete **konzolu ladění** a vyberte **cmd**.</span><span class="sxs-lookup"><span data-stu-id="43bf9-650">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="43bf9-651">Vyberte složku **soubory protokolů** .</span><span class="sxs-lookup"><span data-stu-id="43bf9-651">Select the **LogFiles** folder.</span></span>
1. <span data-ttu-id="43bf9-652">Zkontrolujte **upravený** sloupec a vyberte ikonu tužky a upravte protokol stdout s nejnovějším datem úpravy.</span><span class="sxs-lookup"><span data-stu-id="43bf9-652">Inspect the **Modified** column and select the pencil icon to edit the stdout log with the latest modification date.</span></span>
1. <span data-ttu-id="43bf9-653">Po otevření souboru protokolu se zobrazí chyba.</span><span class="sxs-lookup"><span data-stu-id="43bf9-653">When the log file opens, the error is displayed.</span></span>

<span data-ttu-id="43bf9-654">Zakázat protokolování stdout při odstraňování potíží:</span><span class="sxs-lookup"><span data-stu-id="43bf9-654">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="43bf9-655">V **diagnostické konzole**Kudu se vraťte do **lokality** cesty > **wwwroot** , aby se soubor *Web. config* vykryl.</span><span class="sxs-lookup"><span data-stu-id="43bf9-655">In the Kudu **Diagnostic Console**, return to the path **site** > **wwwroot** to reveal the *web.config* file.</span></span> <span data-ttu-id="43bf9-656">Otevřete soubor **Web. config** znovu výběrem ikony tužky.</span><span class="sxs-lookup"><span data-stu-id="43bf9-656">Open the **web.config** file again by selecting the pencil icon.</span></span>
1. <span data-ttu-id="43bf9-657">Nastavte **stdoutLogEnabled** na `false`.</span><span class="sxs-lookup"><span data-stu-id="43bf9-657">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="43bf9-658">Vyberte **Uložit** a soubor uložte.</span><span class="sxs-lookup"><span data-stu-id="43bf9-658">Select **Save** to save the file.</span></span>

<span data-ttu-id="43bf9-659">Další informace naleznete v tématu <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span><span class="sxs-lookup"><span data-stu-id="43bf9-659">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

> [!WARNING]
> <span data-ttu-id="43bf9-660">Nepodařilo se zakázat protokol stdout může vést k selhání aplikace nebo serveru.</span><span class="sxs-lookup"><span data-stu-id="43bf9-660">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="43bf9-661">Neexistuje žádné omezení velikosti souboru protokolu nebo počet souborů protokolů, které jsou vytvořeny.</span><span class="sxs-lookup"><span data-stu-id="43bf9-661">There's no limit on log file size or the number of log files created.</span></span> <span data-ttu-id="43bf9-662">K řešení problémů se spouštěním aplikací použijte pouze protokolování STDOUT.</span><span class="sxs-lookup"><span data-stu-id="43bf9-662">Only use stdout logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="43bf9-663">Pro obecné protokolování v aplikaci ASP.NET Core po spuštění použijte knihovnu protokolování, která omezuje velikost souboru protokolu a otočí protokoly.</span><span class="sxs-lookup"><span data-stu-id="43bf9-663">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="43bf9-664">Další informace najdete v tématu [Zprostředkovatelé protokolování třetích stran](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="43bf9-664">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="aspnet-core-module-debug-log-azure-app-service"></a><span data-ttu-id="43bf9-665">Protokol ladění modulu ASP.NET Core (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="43bf9-665">ASP.NET Core Module debug log (Azure App Service)</span></span>

<span data-ttu-id="43bf9-666">Protokol ladění ASP.NET Core modulu poskytuje další, hlubší protokolování z modulu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="43bf9-666">The ASP.NET Core Module debug log provides additional, deeper logging from the ASP.NET Core Module.</span></span> <span data-ttu-id="43bf9-667">Povolení a zobrazení protokolů stdout:</span><span class="sxs-lookup"><span data-stu-id="43bf9-667">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="43bf9-668">Pro povolení rozšířeného diagnostického protokolu proveďte jednu z následujících akcí:</span><span class="sxs-lookup"><span data-stu-id="43bf9-668">To enable the enhanced diagnostic log, perform either of the following:</span></span>
   * <span data-ttu-id="43bf9-669">Postupujte podle pokynů v [rozšířených diagnostických protokolech](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) a nakonfigurujte aplikaci pro rozšířené diagnostické protokolování.</span><span class="sxs-lookup"><span data-stu-id="43bf9-669">Follow the instructions in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) to configure the app for an enhanced diagnostic logging.</span></span> <span data-ttu-id="43bf9-670">Znovu nasaďte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="43bf9-670">Redeploy the app.</span></span>
   * <span data-ttu-id="43bf9-671">Přidejte `<handlerSettings>` zobrazené v [rozšířených diagnostických protokolech](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) do souboru *Web. config* živé aplikace pomocí konzoly Kudu:</span><span class="sxs-lookup"><span data-stu-id="43bf9-671">Add the `<handlerSettings>` shown in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) to the live app's *web.config* file using the Kudu console:</span></span>
     1. <span data-ttu-id="43bf9-672">Otevřete **Rozšířené nástroje** v oblasti **vývojové nástroje** .</span><span class="sxs-lookup"><span data-stu-id="43bf9-672">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="43bf9-673">Vyberte tlačítko **přejít&rarr;** .</span><span class="sxs-lookup"><span data-stu-id="43bf9-673">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="43bf9-674">Konzola Kudu se otevře v novém okně nebo záložce prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="43bf9-674">The Kudu console opens in a new browser tab or window.</span></span>
     1. <span data-ttu-id="43bf9-675">Pomocí navigačního panelu v horní části stránky otevřete **konzolu ladění** a vyberte **cmd**.</span><span class="sxs-lookup"><span data-stu-id="43bf9-675">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
     1. <span data-ttu-id="43bf9-676">Otevřete složky v **lokalitě** pro cestu > **wwwroot**.</span><span class="sxs-lookup"><span data-stu-id="43bf9-676">Open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="43bf9-677">Upravte soubor *Web. config* tak, že vyberete tlačítko tužky.</span><span class="sxs-lookup"><span data-stu-id="43bf9-677">Edit the *web.config* file by selecting the pencil button.</span></span> <span data-ttu-id="43bf9-678">Přidejte část `<handlerSettings>`, jak je znázorněno v [rozšířených diagnostických protokolech](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs).</span><span class="sxs-lookup"><span data-stu-id="43bf9-678">Add the `<handlerSettings>` section as shown in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs).</span></span> <span data-ttu-id="43bf9-679">Vyberte tlačítko **Uložit**.</span><span class="sxs-lookup"><span data-stu-id="43bf9-679">Select the **Save** button.</span></span>
1. <span data-ttu-id="43bf9-680">Otevřete **Rozšířené nástroje** v oblasti **vývojové nástroje** .</span><span class="sxs-lookup"><span data-stu-id="43bf9-680">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="43bf9-681">Vyberte tlačítko **přejít&rarr;** .</span><span class="sxs-lookup"><span data-stu-id="43bf9-681">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="43bf9-682">Konzola Kudu se otevře v novém okně nebo záložce prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="43bf9-682">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="43bf9-683">Pomocí navigačního panelu v horní části stránky otevřete **konzolu ladění** a vyberte **cmd**.</span><span class="sxs-lookup"><span data-stu-id="43bf9-683">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="43bf9-684">Otevřete složky v **lokalitě** pro cestu > **wwwroot**.</span><span class="sxs-lookup"><span data-stu-id="43bf9-684">Open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="43bf9-685">Pokud jste nezadali cestu k souboru *aspnetcore-Debug. log* , soubor se zobrazí v seznamu.</span><span class="sxs-lookup"><span data-stu-id="43bf9-685">If you didn't supply a path for the *aspnetcore-debug.log* file, the file appears in the list.</span></span> <span data-ttu-id="43bf9-686">Pokud jste zadali cestu, přejděte do umístění souboru protokolu.</span><span class="sxs-lookup"><span data-stu-id="43bf9-686">If you supplied a path, navigate to the location of the log file.</span></span>
1. <span data-ttu-id="43bf9-687">Otevřete soubor protokolu s tlačítkem tužky vedle názvu souboru.</span><span class="sxs-lookup"><span data-stu-id="43bf9-687">Open the log file with the pencil button next to the file name.</span></span>

<span data-ttu-id="43bf9-688">Zakázat protokolování ladění, pokud je dokončeno odstraňování potíží:</span><span class="sxs-lookup"><span data-stu-id="43bf9-688">Disable debug logging when troubleshooting is complete:</span></span>

<span data-ttu-id="43bf9-689">Chcete-li zakázat rozšířený protokol ladění, proveďte jednu z následujících akcí:</span><span class="sxs-lookup"><span data-stu-id="43bf9-689">To disable the enhanced debug log, perform either of the following:</span></span>

* <span data-ttu-id="43bf9-690">Odeberte `<handlerSettings>` ze souboru *Web. config* místně a pak aplikaci znovu nasaďte.</span><span class="sxs-lookup"><span data-stu-id="43bf9-690">Remove the `<handlerSettings>` from the *web.config* file locally and redeploy the app.</span></span>
* <span data-ttu-id="43bf9-691">Pomocí konzoly Kudu upravte soubor *Web. config* a odeberte oddíl `<handlerSettings>`.</span><span class="sxs-lookup"><span data-stu-id="43bf9-691">Use the Kudu console to edit the *web.config* file and remove the `<handlerSettings>` section.</span></span> <span data-ttu-id="43bf9-692">Uložte soubor.</span><span class="sxs-lookup"><span data-stu-id="43bf9-692">Save the file.</span></span>

<span data-ttu-id="43bf9-693">Další informace naleznete v tématu <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span><span class="sxs-lookup"><span data-stu-id="43bf9-693">For more information, see <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span></span>

> [!WARNING]
> <span data-ttu-id="43bf9-694">Nepovedlo se zakázat protokol ladění, může způsobit selhání aplikace nebo serveru.</span><span class="sxs-lookup"><span data-stu-id="43bf9-694">Failure to disable the debug log can lead to app or server failure.</span></span> <span data-ttu-id="43bf9-695">Velikost souboru protokolu není nijak omezena.</span><span class="sxs-lookup"><span data-stu-id="43bf9-695">There's no limit on log file size.</span></span> <span data-ttu-id="43bf9-696">K řešení problémů se spouštěním aplikace používejte pouze protokolování ladění.</span><span class="sxs-lookup"><span data-stu-id="43bf9-696">Only use debug logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="43bf9-697">Pro obecné protokolování v aplikaci ASP.NET Core po spuštění použijte knihovnu protokolování, která omezuje velikost souboru protokolu a otočí protokoly.</span><span class="sxs-lookup"><span data-stu-id="43bf9-697">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="43bf9-698">Další informace najdete v tématu [Zprostředkovatelé protokolování třetích stran](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="43bf9-698">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="slow-or-hanging-app-azure-app-service"></a><span data-ttu-id="43bf9-699">Pomalá nebo zavěšená aplikace (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="43bf9-699">Slow or hanging app (Azure App Service)</span></span>

<span data-ttu-id="43bf9-700">Pokud aplikace reaguje pomalu nebo přestane reagovat na žádost, přečtěte si následující články:</span><span class="sxs-lookup"><span data-stu-id="43bf9-700">When an app responds slowly or hangs on a request, see the following articles:</span></span>

* [<span data-ttu-id="43bf9-701">Řešení potíží s výkonem pomalých webových aplikací ve službě Azure App Service</span><span class="sxs-lookup"><span data-stu-id="43bf9-701">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="43bf9-702">K zachycení výpisu problémů s přerušovanou výjimkou nebo problémy s výkonem ve webové aplikaci Azure použijte rozšíření pro diagnostiku zhroucení serveru.</span><span class="sxs-lookup"><span data-stu-id="43bf9-702">Use Crash Diagnoser Site Extension to Capture Dump for Intermittent Exception issues or performance issues on Azure Web App</span></span>](https://blogs.msdn.microsoft.com/asiatech/2015/12/28/use-crash-diagnoser-site-extension-to-capture-dump-for-intermittent-exception-issues-or-performance-issues-on-azure-web-app/)

### <a name="monitoring-blades"></a><span data-ttu-id="43bf9-703">Okna monitorování</span><span class="sxs-lookup"><span data-stu-id="43bf9-703">Monitoring blades</span></span>

<span data-ttu-id="43bf9-704">Okna monitorování poskytují alternativní možnosti řešení potíží pro metody popsané dříve v tématu.</span><span class="sxs-lookup"><span data-stu-id="43bf9-704">Monitoring blades provide an alternative troubleshooting experience to the methods described earlier in the topic.</span></span> <span data-ttu-id="43bf9-705">Pomocí těchto oken můžete diagnostikovat chyby 500-Series.</span><span class="sxs-lookup"><span data-stu-id="43bf9-705">These blades can be used to diagnose 500-series errors.</span></span>

<span data-ttu-id="43bf9-706">Potvrďte, že jsou nainstalovaná rozšíření ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="43bf9-706">Confirm that the ASP.NET Core Extensions are installed.</span></span> <span data-ttu-id="43bf9-707">Pokud nejsou rozšíření nainstalována, nainstalujte je ručně:</span><span class="sxs-lookup"><span data-stu-id="43bf9-707">If the extensions aren't installed, install them manually:</span></span>

1. <span data-ttu-id="43bf9-708">V části okno **vývojové nástroje** vyberte okno **rozšíření** .</span><span class="sxs-lookup"><span data-stu-id="43bf9-708">In the **DEVELOPMENT TOOLS** blade section, select the **Extensions** blade.</span></span>
1. <span data-ttu-id="43bf9-709">V seznamu by se měla zobrazit **rozšíření ASP.NET Core** .</span><span class="sxs-lookup"><span data-stu-id="43bf9-709">The **ASP.NET Core Extensions** should appear in the list.</span></span>
1. <span data-ttu-id="43bf9-710">Pokud nejsou rozšíření nainstalovaná, vyberte tlačítko **Přidat** .</span><span class="sxs-lookup"><span data-stu-id="43bf9-710">If the extensions aren't installed, select the **Add** button.</span></span>
1. <span data-ttu-id="43bf9-711">Ze seznamu vyberte **rozšíření ASP.NET Core** .</span><span class="sxs-lookup"><span data-stu-id="43bf9-711">Choose the **ASP.NET Core Extensions** from the list.</span></span>
1. <span data-ttu-id="43bf9-712">Kliknutím na **OK** přijměte právní podmínky.</span><span class="sxs-lookup"><span data-stu-id="43bf9-712">Select **OK** to accept the legal terms.</span></span>
1. <span data-ttu-id="43bf9-713">V okně **Přidat rozšíření** vyberte **OK** .</span><span class="sxs-lookup"><span data-stu-id="43bf9-713">Select **OK** on the **Add extension** blade.</span></span>
1. <span data-ttu-id="43bf9-714">Informační automaticky otevíraná zpráva indikuje, že se rozšíření úspěšně nainstalovala.</span><span class="sxs-lookup"><span data-stu-id="43bf9-714">An informational pop-up message indicates when the extensions are successfully installed.</span></span>

<span data-ttu-id="43bf9-715">Pokud není povoleno protokolování stdout, postupujte následovně:</span><span class="sxs-lookup"><span data-stu-id="43bf9-715">If stdout logging isn't enabled, follow these steps:</span></span>

1. <span data-ttu-id="43bf9-716">V Azure Portal v oblasti **vývojové nástroje** vyberte okno **Pokročilé nástroje** .</span><span class="sxs-lookup"><span data-stu-id="43bf9-716">In the Azure portal, select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="43bf9-717">Vyberte tlačítko **přejít&rarr;** .</span><span class="sxs-lookup"><span data-stu-id="43bf9-717">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="43bf9-718">Konzola Kudu se otevře v novém okně nebo záložce prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="43bf9-718">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="43bf9-719">Pomocí navigačního panelu v horní části stránky otevřete **konzolu ladění** a vyberte **cmd**.</span><span class="sxs-lookup"><span data-stu-id="43bf9-719">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="43bf9-720">Otevřete složky v **lokalitě** pro cestu > **wwwroot** a posuňte se dolů, aby se soubor *Web. config* vypnul v dolní části seznamu.</span><span class="sxs-lookup"><span data-stu-id="43bf9-720">Open the folders to the path **site** > **wwwroot** and scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="43bf9-721">Klikněte na ikonu tužky vedle souboru *Web. config* .</span><span class="sxs-lookup"><span data-stu-id="43bf9-721">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="43bf9-722">Nastavte **stdoutLogEnabled** na `true` a změňte cestu **stdoutLogFile** na: `\\?\%home%\LogFiles\stdout`.</span><span class="sxs-lookup"><span data-stu-id="43bf9-722">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="43bf9-723">Vyberte **Uložit** a uložte aktualizovaný soubor *Web. config* .</span><span class="sxs-lookup"><span data-stu-id="43bf9-723">Select **Save** to save the updated *web.config* file.</span></span>

<span data-ttu-id="43bf9-724">Pokračovat k aktivaci protokolování diagnostiky:</span><span class="sxs-lookup"><span data-stu-id="43bf9-724">Proceed to activate diagnostic logging:</span></span>

1. <span data-ttu-id="43bf9-725">V Azure Portal vyberte okno **diagnostické protokoly** .</span><span class="sxs-lookup"><span data-stu-id="43bf9-725">In the Azure portal, select the **Diagnostics logs** blade.</span></span>
1. <span data-ttu-id="43bf9-726">Vyberte přepínač **zapnuto** pro **protokolování aplikace (systém souborů)** a **podrobné chybové zprávy**.</span><span class="sxs-lookup"><span data-stu-id="43bf9-726">Select the **On** switch for **Application Logging (Filesystem)** and **Detailed error messages**.</span></span> <span data-ttu-id="43bf9-727">V horní části okna vyberte tlačítko **Uložit** .</span><span class="sxs-lookup"><span data-stu-id="43bf9-727">Select the **Save** button at the top of the blade.</span></span>
1. <span data-ttu-id="43bf9-728">Chcete-li zahrnout trasování chybných požadavků, označované také jako protokolování událostí neúspěšných požadavků na FREB, vyberte přepínač **zapnuto** pro **trasování chybných požadavků**.</span><span class="sxs-lookup"><span data-stu-id="43bf9-728">To include failed request tracing, also known as Failed Request Event Buffering (FREB) logging, select the **On** switch for **Failed request tracing**.</span></span>
1. <span data-ttu-id="43bf9-729">Vyberte okno **log Stream** , které je uvedeno hned pod oknem **diagnostické protokoly** na portálu.</span><span class="sxs-lookup"><span data-stu-id="43bf9-729">Select the **Log stream** blade, which is listed immediately under the **Diagnostics logs** blade in the portal.</span></span>
1. <span data-ttu-id="43bf9-730">Vytvořte žádost do aplikace.</span><span class="sxs-lookup"><span data-stu-id="43bf9-730">Make a request to the app.</span></span>
1. <span data-ttu-id="43bf9-731">V datech streamu protokolu se označuje příčina chyby.</span><span class="sxs-lookup"><span data-stu-id="43bf9-731">Within the log stream data, the cause of the error is indicated.</span></span>

<span data-ttu-id="43bf9-732">Nezapomeňte zakázat protokolování stdout po dokončení řešení potíží.</span><span class="sxs-lookup"><span data-stu-id="43bf9-732">Be sure to disable stdout logging when troubleshooting is complete.</span></span>

<span data-ttu-id="43bf9-733">Zobrazení protokolů pro trasování chybných požadavků (protokoly FREB):</span><span class="sxs-lookup"><span data-stu-id="43bf9-733">To view the failed request tracing logs (FREB logs):</span></span>

1. <span data-ttu-id="43bf9-734">Přejděte do okna **diagnostikovat a řešit problémy** v Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="43bf9-734">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="43bf9-735">V oblasti **nástroje podpory** v postranním panelu vyberte **protokoly pro trasování chybných požadavků** .</span><span class="sxs-lookup"><span data-stu-id="43bf9-735">Select **Failed Request Tracing Logs** from the **SUPPORT TOOLS** area of the sidebar.</span></span>

<span data-ttu-id="43bf9-736">V [části Povolení protokolování diagnostiky pro webové aplikace v Azure App Service tématu](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) a [Nejčastější dotazy týkající se výkonu aplikace pro Web Apps v Azure: návody zapnout trasování chybných požadavků?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing) Další informace.</span><span class="sxs-lookup"><span data-stu-id="43bf9-736">See [Failed request traces section of the Enable diagnostics logging for web apps in Azure App Service topic](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) and the [Application performance FAQs for Web Apps in Azure: How do I turn on failed request tracing?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing) for more information.</span></span>

<span data-ttu-id="43bf9-737">Další informace najdete v tématu [Povolení protokolování diagnostiky pro webové aplikace v Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log).</span><span class="sxs-lookup"><span data-stu-id="43bf9-737">For more information, see [Enable diagnostics logging for web apps in Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log).</span></span>

> [!WARNING]
> <span data-ttu-id="43bf9-738">Nepodařilo se zakázat protokol stdout může vést k selhání aplikace nebo serveru.</span><span class="sxs-lookup"><span data-stu-id="43bf9-738">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="43bf9-739">Neexistuje žádné omezení velikosti souboru protokolu nebo počet souborů protokolů, které jsou vytvořeny.</span><span class="sxs-lookup"><span data-stu-id="43bf9-739">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="43bf9-740">Pro rutiny protokolování v aplikaci ASP.NET Core, použijte protokolování knihovnu, která omezuje velikost souboru protokolu a otočí protokoly.</span><span class="sxs-lookup"><span data-stu-id="43bf9-740">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="43bf9-741">Další informace najdete v tématu [Zprostředkovatelé protokolování třetích stran](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="43bf9-741">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

## <a name="troubleshoot-on-iis"></a><span data-ttu-id="43bf9-742">Řešení potíží ve službě IIS</span><span class="sxs-lookup"><span data-stu-id="43bf9-742">Troubleshoot on IIS</span></span>

### <a name="application-event-log-iis"></a><span data-ttu-id="43bf9-743">Protokol událostí aplikace (IIS)</span><span class="sxs-lookup"><span data-stu-id="43bf9-743">Application Event Log (IIS)</span></span>

<span data-ttu-id="43bf9-744">Přístup k protokolu událostí aplikace:</span><span class="sxs-lookup"><span data-stu-id="43bf9-744">Access the Application Event Log:</span></span>

1. <span data-ttu-id="43bf9-745">Otevřete nabídku Start, vyhledejte *Prohlížeč událostí*a vyberte aplikaci **Prohlížeč událostí** .</span><span class="sxs-lookup"><span data-stu-id="43bf9-745">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="43bf9-746">V **Prohlížeč událostí**otevřete uzel **protokoly systému Windows** .</span><span class="sxs-lookup"><span data-stu-id="43bf9-746">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="43bf9-747">Výběrem **aplikace** otevřete protokol událostí aplikace.</span><span class="sxs-lookup"><span data-stu-id="43bf9-747">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="43bf9-748">Vyhledejte chyby související s selhání aplikace.</span><span class="sxs-lookup"><span data-stu-id="43bf9-748">Search for errors associated with the failing app.</span></span> <span data-ttu-id="43bf9-749">Chyby mají ve *zdrojovém* sloupci hodnotu *modulu IIS AspNetCore* nebo *modul IIS Express AspNetCore* .</span><span class="sxs-lookup"><span data-stu-id="43bf9-749">Errors have a value of *IIS AspNetCore Module* or *IIS Express AspNetCore Module* in the *Source* column.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="43bf9-750">Spuštění aplikace příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="43bf9-750">Run the app at a command prompt</span></span>

<span data-ttu-id="43bf9-751">Mnoho chyb při spuštění nevytvářejí užitečné informace v protokolu událostí aplikace.</span><span class="sxs-lookup"><span data-stu-id="43bf9-751">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="43bf9-752">Příčin některých chyb můžete najít spuštěním aplikace v příkazovém řádku v hostitelském systému.</span><span class="sxs-lookup"><span data-stu-id="43bf9-752">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span>

#### <a name="framework-dependent-deployment"></a><span data-ttu-id="43bf9-753">Nasazení závisí na architektuře</span><span class="sxs-lookup"><span data-stu-id="43bf9-753">Framework-dependent deployment</span></span>

<span data-ttu-id="43bf9-754">Pokud aplikace je [nasazení závislé na rozhraní](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span><span class="sxs-lookup"><span data-stu-id="43bf9-754">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

1. <span data-ttu-id="43bf9-755">Na příkazovém řádku přejděte do složky pro nasazení a spusťte aplikaci spuštěním sestavení aplikace pomocí příkazu *dotnet. exe*.</span><span class="sxs-lookup"><span data-stu-id="43bf9-755">At a command prompt, navigate to the deployment folder and run the app by executing the app's assembly with *dotnet.exe*.</span></span> <span data-ttu-id="43bf9-756">V následujícím příkazu nahraďte název sestavení aplikace pro \<assembly_name >: `dotnet .\<assembly_name>.dll`.</span><span class="sxs-lookup"><span data-stu-id="43bf9-756">In the following command, substitute the name of the app's assembly for \<assembly_name>: `dotnet .\<assembly_name>.dll`.</span></span>
1. <span data-ttu-id="43bf9-757">Výstup z aplikace zobrazuje všechny chyby konzoly je zapsán do okna konzoly.</span><span class="sxs-lookup"><span data-stu-id="43bf9-757">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="43bf9-758">Je-li této chybě dojde při požadavku na aplikaci, vytvořte žádost na hostitele a port, kde Kestrel naslouchá.</span><span class="sxs-lookup"><span data-stu-id="43bf9-758">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="43bf9-759">Pomocí výchozího hostitele a příspěvku vytvořte žádost o `http://localhost:5000/`.</span><span class="sxs-lookup"><span data-stu-id="43bf9-759">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="43bf9-760">Aplikace reaguje, obvykle na adrese Kestrel koncový bod, tím je pravděpodobnější týkající se konfigurace hostování a méně pravděpodobné, že v rámci aplikace.</span><span class="sxs-lookup"><span data-stu-id="43bf9-760">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

#### <a name="self-contained-deployment"></a><span data-ttu-id="43bf9-761">Samostatná nasazení</span><span class="sxs-lookup"><span data-stu-id="43bf9-761">Self-contained deployment</span></span>

<span data-ttu-id="43bf9-762">Pokud je aplikace samostatná, [nasazení](/dotnet/core/deploying/#self-contained-deployments-scd):</span><span class="sxs-lookup"><span data-stu-id="43bf9-762">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

1. <span data-ttu-id="43bf9-763">Na příkazovém řádku přejděte do složky pro nasazení a spuštění spustitelného souboru aplikace.</span><span class="sxs-lookup"><span data-stu-id="43bf9-763">At a command prompt, navigate to the deployment folder and run the app's executable.</span></span> <span data-ttu-id="43bf9-764">V následujícím příkazu nahraďte název sestavení aplikace pro \<assembly_name >: `<assembly_name>.exe`.</span><span class="sxs-lookup"><span data-stu-id="43bf9-764">In the following command, substitute the name of the app's assembly for \<assembly_name>: `<assembly_name>.exe`.</span></span>
1. <span data-ttu-id="43bf9-765">Výstup z aplikace zobrazuje všechny chyby konzoly je zapsán do okna konzoly.</span><span class="sxs-lookup"><span data-stu-id="43bf9-765">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="43bf9-766">Je-li této chybě dojde při požadavku na aplikaci, vytvořte žádost na hostitele a port, kde Kestrel naslouchá.</span><span class="sxs-lookup"><span data-stu-id="43bf9-766">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="43bf9-767">Pomocí výchozího hostitele a příspěvku vytvořte žádost o `http://localhost:5000/`.</span><span class="sxs-lookup"><span data-stu-id="43bf9-767">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="43bf9-768">Aplikace reaguje, obvykle na adrese Kestrel koncový bod, tím je pravděpodobnější týkající se konfigurace hostování a méně pravděpodobné, že v rámci aplikace.</span><span class="sxs-lookup"><span data-stu-id="43bf9-768">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

### <a name="aspnet-core-module-stdout-log-iis"></a><span data-ttu-id="43bf9-769">Protokol stdout v modulu ASP.NET Core (IIS)</span><span class="sxs-lookup"><span data-stu-id="43bf9-769">ASP.NET Core Module stdout log (IIS)</span></span>

<span data-ttu-id="43bf9-770">Povolení a zobrazení protokolů stdout:</span><span class="sxs-lookup"><span data-stu-id="43bf9-770">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="43bf9-771">Přejděte do složky pro nasazení webu v hostitelském systému.</span><span class="sxs-lookup"><span data-stu-id="43bf9-771">Navigate to the site's deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="43bf9-772">Pokud složka *logs* není k dispozici, vytvořte složku.</span><span class="sxs-lookup"><span data-stu-id="43bf9-772">If the *logs* folder isn't present, create the folder.</span></span> <span data-ttu-id="43bf9-773">Pokyny k tomu, jak povolit MSBuild pro vytvoření složky *logs* v nasazení automaticky, najdete v tématu [Struktura adresáře](xref:host-and-deploy/directory-structure) .</span><span class="sxs-lookup"><span data-stu-id="43bf9-773">For instructions on how to enable MSBuild to create the *logs* folder in the deployment automatically, see the [Directory structure](xref:host-and-deploy/directory-structure) topic.</span></span>
1. <span data-ttu-id="43bf9-774">Upravte soubor *Web. config* .</span><span class="sxs-lookup"><span data-stu-id="43bf9-774">Edit the *web.config* file.</span></span> <span data-ttu-id="43bf9-775">Nastavte **stdoutLogEnabled** na `true` a změňte cestu **stdoutLogFile** tak, aby odkazovala na složku *logs* (například `.\logs\stdout`).</span><span class="sxs-lookup"><span data-stu-id="43bf9-775">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to point to the *logs* folder (for example, `.\logs\stdout`).</span></span> <span data-ttu-id="43bf9-776">`stdout` v cestě je předpona názvu souboru protokolu.</span><span class="sxs-lookup"><span data-stu-id="43bf9-776">`stdout` in the path is the log file name prefix.</span></span> <span data-ttu-id="43bf9-777">Časové razítko, id procesu a příponu souboru jsou přidány automaticky při vytvoření protokolu.</span><span class="sxs-lookup"><span data-stu-id="43bf9-777">A timestamp, process id, and file extension are added automatically when the log is created.</span></span> <span data-ttu-id="43bf9-778">Při použití `stdout` jako předpony názvu souboru se typický soubor protokolu jmenuje *stdout_20180205184032_5412. log*.</span><span class="sxs-lookup"><span data-stu-id="43bf9-778">Using `stdout` as the file name prefix, a typical log file is named *stdout_20180205184032_5412.log*.</span></span>
1. <span data-ttu-id="43bf9-779">Zajistěte, aby identita fondu aplikací měla oprávnění k zápisu do složky *logs* .</span><span class="sxs-lookup"><span data-stu-id="43bf9-779">Ensure your application pool's identity has write permissions to the *logs* folder.</span></span>
1. <span data-ttu-id="43bf9-780">Uložte aktualizovaný soubor *Web. config* .</span><span class="sxs-lookup"><span data-stu-id="43bf9-780">Save the updated *web.config* file.</span></span>
1. <span data-ttu-id="43bf9-781">Vytvořte žádost do aplikace.</span><span class="sxs-lookup"><span data-stu-id="43bf9-781">Make a request to the app.</span></span>
1. <span data-ttu-id="43bf9-782">Přejděte do složky *logs* .</span><span class="sxs-lookup"><span data-stu-id="43bf9-782">Navigate to the *logs* folder.</span></span> <span data-ttu-id="43bf9-783">Vyhledání a otevření protokolu nejnovější stdout.</span><span class="sxs-lookup"><span data-stu-id="43bf9-783">Find and open the most recent stdout log.</span></span>
1. <span data-ttu-id="43bf9-784">Studie v protokolu chyb.</span><span class="sxs-lookup"><span data-stu-id="43bf9-784">Study the log for errors.</span></span>

<span data-ttu-id="43bf9-785">Zakázat protokolování stdout při odstraňování potíží:</span><span class="sxs-lookup"><span data-stu-id="43bf9-785">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="43bf9-786">Upravte soubor *Web. config* .</span><span class="sxs-lookup"><span data-stu-id="43bf9-786">Edit the *web.config* file.</span></span>
1. <span data-ttu-id="43bf9-787">Nastavte **stdoutLogEnabled** na `false`.</span><span class="sxs-lookup"><span data-stu-id="43bf9-787">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="43bf9-788">Uložte soubor.</span><span class="sxs-lookup"><span data-stu-id="43bf9-788">Save the file.</span></span>

<span data-ttu-id="43bf9-789">Další informace naleznete v tématu <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span><span class="sxs-lookup"><span data-stu-id="43bf9-789">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

> [!WARNING]
> <span data-ttu-id="43bf9-790">Nepodařilo se zakázat protokol stdout může vést k selhání aplikace nebo serveru.</span><span class="sxs-lookup"><span data-stu-id="43bf9-790">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="43bf9-791">Neexistuje žádné omezení velikosti souboru protokolu nebo počet souborů protokolů, které jsou vytvořeny.</span><span class="sxs-lookup"><span data-stu-id="43bf9-791">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="43bf9-792">Pro rutiny protokolování v aplikaci ASP.NET Core, použijte protokolování knihovnu, která omezuje velikost souboru protokolu a otočí protokoly.</span><span class="sxs-lookup"><span data-stu-id="43bf9-792">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="43bf9-793">Další informace najdete v tématu [Zprostředkovatelé protokolování třetích stran](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="43bf9-793">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="aspnet-core-module-debug-log-iis"></a><span data-ttu-id="43bf9-794">Protokol ladění modulu ASP.NET Core (IIS)</span><span class="sxs-lookup"><span data-stu-id="43bf9-794">ASP.NET Core Module debug log (IIS)</span></span>

<span data-ttu-id="43bf9-795">Přidejte následující nastavení obslužné rutiny do souboru *Web. config* aplikace, aby bylo možné povolit protokol ladění ASP.NET Core modulu:</span><span class="sxs-lookup"><span data-stu-id="43bf9-795">Add the following handler settings to the app's *web.config* file to enable ASP.NET Core Module debug log:</span></span>

```xml
<aspNetCore ...>
  <handlerSettings>
    <handlerSetting name="debugLevel" value="file" />
    <handlerSetting name="debugFile" value="c:\temp\ancm.log" />
  </handlerSettings>
</aspNetCore>
```

<span data-ttu-id="43bf9-796">Potvrďte, že cesta zadaná pro protokol existuje a že identita fondu aplikací má oprávnění k zápisu do umístění.</span><span class="sxs-lookup"><span data-stu-id="43bf9-796">Confirm that the path specified for the log exists and that the app pool's identity has write permissions to the location.</span></span>

<span data-ttu-id="43bf9-797">Další informace naleznete v tématu <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span><span class="sxs-lookup"><span data-stu-id="43bf9-797">For more information, see <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span></span>

### <a name="enable-the-developer-exception-page"></a><span data-ttu-id="43bf9-798">Povolit na stránce výjimek pro vývojáře</span><span class="sxs-lookup"><span data-stu-id="43bf9-798">Enable the Developer Exception Page</span></span>

<span data-ttu-id="43bf9-799">[Proměnnou prostředí `ASPNETCORE_ENVIRONMENT` lze přidat do souboru Web. config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) , aby bylo možné aplikaci spustit ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="43bf9-799">The `ASPNETCORE_ENVIRONMENT` [environment variable can be added to web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) to run the app in the Development environment.</span></span> <span data-ttu-id="43bf9-800">Pokud se prostředí nepřepisuje při spuštění aplikace `UseEnvironment` v Tvůrci hostitele, nastavení proměnné prostředí umožní, aby se při spuštění aplikace zobrazila [Stránka s výjimkou vývojářů](xref:fundamentals/error-handling) .</span><span class="sxs-lookup"><span data-stu-id="43bf9-800">As long as the environment isn't overridden in app startup by `UseEnvironment` on the host builder, setting the environment variable allows the [Developer Exception Page](xref:fundamentals/error-handling) to appear when the app is run.</span></span>

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

<span data-ttu-id="43bf9-801">Nastavení proměnné prostředí pro `ASPNETCORE_ENVIRONMENT` se doporučuje jenom pro použití na pracovních a testovacích serverech, které nejsou přístupné pro Internet.</span><span class="sxs-lookup"><span data-stu-id="43bf9-801">Setting the environment variable for `ASPNETCORE_ENVIRONMENT` is only recommended for use on staging and testing servers that aren't exposed to the Internet.</span></span> <span data-ttu-id="43bf9-802">Po vyřešení potíží odeberte proměnnou prostředí ze souboru *Web. config* .</span><span class="sxs-lookup"><span data-stu-id="43bf9-802">Remove the environment variable from the *web.config* file after troubleshooting.</span></span> <span data-ttu-id="43bf9-803">Informace o nastavení proměnných prostředí v *souboru Web. config*naleznete v tématu [environmentVariables Child element of aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).</span><span class="sxs-lookup"><span data-stu-id="43bf9-803">For information on setting environment variables in *web.config*, see [environmentVariables child element of aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).</span></span>

### <a name="obtain-data-from-an-app"></a><span data-ttu-id="43bf9-804">Získání dat z aplikace</span><span class="sxs-lookup"><span data-stu-id="43bf9-804">Obtain data from an app</span></span>

<span data-ttu-id="43bf9-805">Pokud aplikace je schopná reagovat na požadavky, získáte žádost o připojení a další data z aplikace pomocí terminálu vložené middlewaru.</span><span class="sxs-lookup"><span data-stu-id="43bf9-805">If an app is capable of responding to requests, obtain request, connection, and additional data from the app using terminal inline middleware.</span></span> <span data-ttu-id="43bf9-806">Další informace a ukázku kódu naleznete v tématu <xref:test/troubleshoot#obtain-data-from-an-app>.</span><span class="sxs-lookup"><span data-stu-id="43bf9-806">For more information and sample code, see <xref:test/troubleshoot#obtain-data-from-an-app>.</span></span>

### <a name="slow-or-hanging-app-iis"></a><span data-ttu-id="43bf9-807">Pomalá nebo zavěšená aplikace (IIS)</span><span class="sxs-lookup"><span data-stu-id="43bf9-807">Slow or hanging app (IIS)</span></span>

<span data-ttu-id="43bf9-808">*Výpis stavu* systému je snímek paměti systému a může vám pomůže určit příčinu selhání aplikace, selhání při spuštění nebo pomalé aplikace.</span><span class="sxs-lookup"><span data-stu-id="43bf9-808">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="43bf9-809">Aplikace selže nebo dojde k výjimce.</span><span class="sxs-lookup"><span data-stu-id="43bf9-809">App crashes or encounters an exception</span></span>

<span data-ttu-id="43bf9-810">Získat a analyzovat výpis z [zasílání zpráv o chybách systému Windows (WER)](/windows/desktop/wer/windows-error-reporting):</span><span class="sxs-lookup"><span data-stu-id="43bf9-810">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="43bf9-811">Vytvořte složku, do které se budou ukládat soubory s výpisem stavu systému na `c:\dumps`.</span><span class="sxs-lookup"><span data-stu-id="43bf9-811">Create a folder to hold crash dump files at `c:\dumps`.</span></span> <span data-ttu-id="43bf9-812">Fond aplikací musí mít ke složce přístup pro zápis.</span><span class="sxs-lookup"><span data-stu-id="43bf9-812">The app pool must have write access to the folder.</span></span>
1. <span data-ttu-id="43bf9-813">Spusťte [skript prostředí PowerShell pro EnableDumps](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1):</span><span class="sxs-lookup"><span data-stu-id="43bf9-813">Run the [EnableDumps PowerShell script](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1):</span></span>
   * <span data-ttu-id="43bf9-814">Pokud aplikace používá [model hostování v rámci procesu](xref:host-and-deploy/iis/index#in-process-hosting-model), spusťte skript pro *W3wp. exe*:</span><span class="sxs-lookup"><span data-stu-id="43bf9-814">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\EnableDumps w3wp.exe c:\dumps
     ```

   * <span data-ttu-id="43bf9-815">Pokud aplikace používá [model hostování mimo proces](xref:host-and-deploy/iis/index#out-of-process-hosting-model), spusťte skript pro příkaz *dotnet. exe*:</span><span class="sxs-lookup"><span data-stu-id="43bf9-815">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\EnableDumps dotnet.exe c:\dumps
     ```

1. <span data-ttu-id="43bf9-816">Spusťte aplikaci za podmínek, které způsobí, že dojde k chybě.</span><span class="sxs-lookup"><span data-stu-id="43bf9-816">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="43bf9-817">Po chybě spusťte [skript PowerShellu DisableDumps](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1):</span><span class="sxs-lookup"><span data-stu-id="43bf9-817">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1):</span></span>
   * <span data-ttu-id="43bf9-818">Pokud aplikace používá [model hostování v rámci procesu](xref:host-and-deploy/iis/index#in-process-hosting-model), spusťte skript pro *W3wp. exe*:</span><span class="sxs-lookup"><span data-stu-id="43bf9-818">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\DisableDumps w3wp.exe
     ```

   * <span data-ttu-id="43bf9-819">Pokud aplikace používá [model hostování mimo proces](xref:host-and-deploy/iis/index#out-of-process-hosting-model), spusťte skript pro příkaz *dotnet. exe*:</span><span class="sxs-lookup"><span data-stu-id="43bf9-819">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\DisableDumps dotnet.exe
     ```

<span data-ttu-id="43bf9-820">Po selhání aplikace a dokončení shromažďování výpisu je možné aplikaci ukončit normálně.</span><span class="sxs-lookup"><span data-stu-id="43bf9-820">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="43bf9-821">Skript PowerShellu nakonfiguruje WER a shromáždí až pět výpisů paměti pro každou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="43bf9-821">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="43bf9-822">Výpisy stavu systému můžou zabírat velké množství místa na disku (každý až několik gigabajtů).</span><span class="sxs-lookup"><span data-stu-id="43bf9-822">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="43bf9-823">Aplikace přestane reagovat, dojde k chybě při spuštění nebo se spustí normálně.</span><span class="sxs-lookup"><span data-stu-id="43bf9-823">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="43bf9-824">Když *aplikace přestane reagovat (zastaví* se, ale nejedná se o chybu), selže během spuštění nebo se spustí normálně, podívejte [se na soubory výpisu paměti v uživatelském režimu: zvolením nejlepšího](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) nástroje vyberte vhodný nástroj pro vytváření výpisu.</span><span class="sxs-lookup"><span data-stu-id="43bf9-824">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="43bf9-825">Analýza výpisu paměti</span><span class="sxs-lookup"><span data-stu-id="43bf9-825">Analyze the dump</span></span>

<span data-ttu-id="43bf9-826">Výpis paměti lze analyzovat pomocí několika přístupů.</span><span class="sxs-lookup"><span data-stu-id="43bf9-826">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="43bf9-827">Další informace najdete v tématu [Analýza souboru s výpisem stavu v uživatelském režimu](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span><span class="sxs-lookup"><span data-stu-id="43bf9-827">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="clear-package-caches"></a><span data-ttu-id="43bf9-828">Vymazat mezipaměti balíčků</span><span class="sxs-lookup"><span data-stu-id="43bf9-828">Clear package caches</span></span>

<span data-ttu-id="43bf9-829">Funkční aplikace může po upgradu .NET Core SDK ve vývojovém počítači nebo změně verzí balíčku v rámci aplikace selhat okamžitě.</span><span class="sxs-lookup"><span data-stu-id="43bf9-829">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="43bf9-830">V některých případech osamocené balíčky mohou narušit funkce aplikace při provádění hlavní upgrady.</span><span class="sxs-lookup"><span data-stu-id="43bf9-830">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="43bf9-831">Většina těchto problémů můžete opravit podle těchto pokynů:</span><span class="sxs-lookup"><span data-stu-id="43bf9-831">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="43bf9-832">Odstraňte složky *bin* a *obj* .</span><span class="sxs-lookup"><span data-stu-id="43bf9-832">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="43bf9-833">Pomocí příkazu [dotnet All--Clear](/dotnet/core/tools/dotnet-nuget-locals) z příkazového prostředí vymažte mezipaměť balíčku.</span><span class="sxs-lookup"><span data-stu-id="43bf9-833">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="43bf9-834">Mazání mezipamětí balíčků lze také provést pomocí nástroje [NuGet. exe](https://www.nuget.org/downloads) a spuštěním příkazu `nuget locals all -clear`.</span><span class="sxs-lookup"><span data-stu-id="43bf9-834">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="43bf9-835">*NuGet. exe* není sada instalovaná instalace s desktopovým operačním systémem Windows a musí se získat samostatně z [webu NuGet](https://www.nuget.org/downloads).</span><span class="sxs-lookup"><span data-stu-id="43bf9-835">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="43bf9-836">Obnovit a znovu sestavte projekt.</span><span class="sxs-lookup"><span data-stu-id="43bf9-836">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="43bf9-837">Před opětovným nasazením aplikace odstraňte všechny soubory ve složce pro nasazení na serveru.</span><span class="sxs-lookup"><span data-stu-id="43bf9-837">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="43bf9-838">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="43bf9-838">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:fundamentals/error-handling>
* <xref:host-and-deploy/aspnet-core-module>

### <a name="azure-documentation"></a><span data-ttu-id="43bf9-839">Dokumentace k Azure</span><span class="sxs-lookup"><span data-stu-id="43bf9-839">Azure documentation</span></span>

* [<span data-ttu-id="43bf9-840">Application Insights pro ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="43bf9-840">Application Insights for ASP.NET Core</span></span>](/azure/application-insights/app-insights-asp-net-core)
* [<span data-ttu-id="43bf9-841">Část vzdálené ladění webových aplikací řešení potíží s webovou aplikací v Azure App Service pomocí sady Visual Studio</span><span class="sxs-lookup"><span data-stu-id="43bf9-841">Remote debugging web apps section of Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug)
* [<span data-ttu-id="43bf9-842">Přehled diagnostiky Azure App Service</span><span class="sxs-lookup"><span data-stu-id="43bf9-842">Azure App Service diagnostics overview</span></span>](/azure/app-service/app-service-diagnostics)
* [<span data-ttu-id="43bf9-843">Postup: Monitorování aplikací ve službě Azure App Service</span><span class="sxs-lookup"><span data-stu-id="43bf9-843">How to: Monitor Apps in Azure App Service</span></span>](/azure/app-service/web-sites-monitor)
* [<span data-ttu-id="43bf9-844">Řešení potíží s webovou aplikací v Azure App Service pomocí sady Visual Studio</span><span class="sxs-lookup"><span data-stu-id="43bf9-844">Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [<span data-ttu-id="43bf9-845">Řešení chyb HTTP z "502 špatné brány" a "nedostupné služby 503" v Azure Web Apps</span><span class="sxs-lookup"><span data-stu-id="43bf9-845">Troubleshoot HTTP errors of "502 bad gateway" and "503 service unavailable" in your Azure web apps</span></span>](/azure/app-service/app-service-web-troubleshoot-http-502-http-503)
* [<span data-ttu-id="43bf9-846">Řešení potíží s výkonem pomalých webových aplikací ve službě Azure App Service</span><span class="sxs-lookup"><span data-stu-id="43bf9-846">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="43bf9-847">Nejčastější dotazy týkající se výkonu aplikací pro Web Apps v Azure</span><span class="sxs-lookup"><span data-stu-id="43bf9-847">Application performance FAQs for Web Apps in Azure</span></span>](/azure/app-service/app-service-web-availability-performance-application-issues-faq)
* [<span data-ttu-id="43bf9-848">Sandbox webové aplikace Azure (omezení spuštění App Service Runtime)</span><span class="sxs-lookup"><span data-stu-id="43bf9-848">Azure Web App sandbox (App Service runtime execution limitations)</span></span>](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)
* [<span data-ttu-id="43bf9-849">Azure pátek: Azure App Service diagnostické prostředí a řešení potíží (12 minut video)</span><span class="sxs-lookup"><span data-stu-id="43bf9-849">Azure Friday: Azure App Service Diagnostic and Troubleshooting Experience (12-minute video)</span></span>](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)

### <a name="visual-studio-documentation"></a><span data-ttu-id="43bf9-850">Dokumentace sady Visual Studio</span><span class="sxs-lookup"><span data-stu-id="43bf9-850">Visual Studio documentation</span></span>

* [<span data-ttu-id="43bf9-851">Vzdálené ladění ASP.NET Core ve službě IIS v Azure v aplikaci Visual Studio 2017</span><span class="sxs-lookup"><span data-stu-id="43bf9-851">Remote Debug ASP.NET Core on IIS in Azure in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-azure)
* [<span data-ttu-id="43bf9-852">Vzdálené ladění ASP.NET Core na vzdáleném počítači IIS v aplikaci Visual Studio 2017</span><span class="sxs-lookup"><span data-stu-id="43bf9-852">Remote Debug ASP.NET Core on a Remote IIS Computer in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-aspnet-on-a-remote-iis-computer)
* [<span data-ttu-id="43bf9-853">Další informace k ladění pomocí sady Visual Studio</span><span class="sxs-lookup"><span data-stu-id="43bf9-853">Learn to debug using Visual Studio</span></span>](/visualstudio/debugger/getting-started-with-the-debugger)

### <a name="visual-studio-code-documentation"></a><span data-ttu-id="43bf9-854">Dokumentace k Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="43bf9-854">Visual Studio Code documentation</span></span>

* [<span data-ttu-id="43bf9-855">Ladění pomocí Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="43bf9-855">Debugging with Visual Studio Code</span></span>](https://code.visualstudio.com/docs/editor/debugging)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="43bf9-856">Tento článek poskytuje informace o běžných chybách při spuštění aplikací a pokyny k diagnostice chyb při nasazení aplikace do Azure App Service nebo IIS:</span><span class="sxs-lookup"><span data-stu-id="43bf9-856">This article provides information on common app startup errors and instructions on how to diagnose errors when an app is deployed to Azure App Service or IIS:</span></span>

[<span data-ttu-id="43bf9-857">Chyby při spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="43bf9-857">App startup errors</span></span>](#app-startup-errors)  
<span data-ttu-id="43bf9-858">Vysvětluje běžné spouštění scénářů stavového kódu HTTP.</span><span class="sxs-lookup"><span data-stu-id="43bf9-858">Explains common startup HTTP status code scenarios.</span></span>

[<span data-ttu-id="43bf9-859">Řešení potíží na Azure App Service</span><span class="sxs-lookup"><span data-stu-id="43bf9-859">Troubleshoot on Azure App Service</span></span>](#troubleshoot-on-azure-app-service)  
<span data-ttu-id="43bf9-860">Poskytuje rady pro řešení potíží pro aplikace nasazené do Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="43bf9-860">Provides troubleshooting advice for apps deployed to Azure App Service.</span></span>

[<span data-ttu-id="43bf9-861">Odstraňování potíží ve službě IIS</span><span class="sxs-lookup"><span data-stu-id="43bf9-861">Troubleshoot on IIS</span></span>](#troubleshoot-on-iis)  
<span data-ttu-id="43bf9-862">Poskytuje rady pro řešení potíží pro aplikace nasazené do služby IIS nebo místně spuštěné v IIS Express.</span><span class="sxs-lookup"><span data-stu-id="43bf9-862">Provides troubleshooting advice for apps deployed to IIS or running on IIS Express locally.</span></span> <span data-ttu-id="43bf9-863">Pokyny platí pro nasazení systémů Windows Server a Desktop v systému Windows.</span><span class="sxs-lookup"><span data-stu-id="43bf9-863">The guidance applies to both Windows Server and Windows desktop deployments.</span></span>

[<span data-ttu-id="43bf9-864">Vymazat mezipaměti balíčků</span><span class="sxs-lookup"><span data-stu-id="43bf9-864">Clear package caches</span></span>](#clear-package-caches)  
<span data-ttu-id="43bf9-865">Vysvětluje, co dělat v případě, že při provádění podstatných upgradů nebo změně verze balíčku dojde k přerušení aplikace v nesouvislých balíčcích.</span><span class="sxs-lookup"><span data-stu-id="43bf9-865">Explains what to do when incoherent packages break an app when performing major upgrades or changing package versions.</span></span>

[<span data-ttu-id="43bf9-866">Další materiály</span><span class="sxs-lookup"><span data-stu-id="43bf9-866">Additional resources</span></span>](#additional-resources)  
<span data-ttu-id="43bf9-867">Seznam dalších témat pro odstraňování potíží.</span><span class="sxs-lookup"><span data-stu-id="43bf9-867">Lists additional troubleshooting topics.</span></span>

## <a name="app-startup-errors"></a><span data-ttu-id="43bf9-868">Chyby při spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="43bf9-868">App startup errors</span></span>

<span data-ttu-id="43bf9-869">V aplikaci Visual Studio je výchozím nastavením projektu ASP.NET Core [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hostování během ladění.</span><span class="sxs-lookup"><span data-stu-id="43bf9-869">In Visual Studio, an ASP.NET Core project defaults to [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hosting during debugging.</span></span> <span data-ttu-id="43bf9-870">*Selhání procesu 502,5* , ke kterému dochází, když se místně ladění dá diagnostikovat pomocí Rady v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="43bf9-870">A *502.5 Process Failure* that occurs when debugging locally can be diagnosed using the advice in this topic.</span></span>

### <a name="40314-forbidden"></a><span data-ttu-id="43bf9-871">403,14 zakázané</span><span class="sxs-lookup"><span data-stu-id="43bf9-871">403.14 Forbidden</span></span>

<span data-ttu-id="43bf9-872">Aplikaci se nepodařilo spustit.</span><span class="sxs-lookup"><span data-stu-id="43bf9-872">The app fails to start.</span></span> <span data-ttu-id="43bf9-873">Protokoluje se následující chyba:</span><span class="sxs-lookup"><span data-stu-id="43bf9-873">The following error is logged:</span></span>

```
The Web server is configured to not list the contents of this directory.
```

<span data-ttu-id="43bf9-874">K této chybě obvykle dochází v důsledku poškozeného nasazení v hostitelském systému, který zahrnuje kterýkoli z následujících scénářů:</span><span class="sxs-lookup"><span data-stu-id="43bf9-874">The error is usually caused by a broken deployment on the hosting system, which includes any of the following scenarios:</span></span>

* <span data-ttu-id="43bf9-875">Aplikace je nasazená do chybné složky v hostitelském systému.</span><span class="sxs-lookup"><span data-stu-id="43bf9-875">The app is deployed to the wrong folder on the hosting system.</span></span>
* <span data-ttu-id="43bf9-876">Procesu nasazení se nepodařilo přesunout všechny soubory a složky aplikace do složky pro nasazení v hostitelském systému.</span><span class="sxs-lookup"><span data-stu-id="43bf9-876">The deployment process failed to move all of the app's files and folders to the deployment folder on the hosting system.</span></span>
* <span data-ttu-id="43bf9-877">V nasazení chybí soubor *Web. config* , nebo je obsah souboru *Web. config* poškozený.</span><span class="sxs-lookup"><span data-stu-id="43bf9-877">The *web.config* file is missing from the deployment, or the *web.config* file contents are malformed.</span></span>

<span data-ttu-id="43bf9-878">Proveďte následující kroky:</span><span class="sxs-lookup"><span data-stu-id="43bf9-878">Perform the following steps:</span></span>

1. <span data-ttu-id="43bf9-879">Odstraňte všechny soubory a složky ze složky pro nasazení v hostitelském systému.</span><span class="sxs-lookup"><span data-stu-id="43bf9-879">Delete all of the files and folders from the deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="43bf9-880">Znovu nasaďte obsah složky pro *publikování* aplikace do hostitelského systému pomocí běžné metody nasazení, jako je například Visual Studio, PowerShell nebo ruční nasazení:</span><span class="sxs-lookup"><span data-stu-id="43bf9-880">Redeploy the contents of the app's *publish* folder to the hosting system using your normal method of deployment, such as Visual Studio, PowerShell, or manual deployment:</span></span>
   * <span data-ttu-id="43bf9-881">Zkontrolujte, zda je v nasazení přítomen soubor *Web. config* a zda jeho obsah je správný.</span><span class="sxs-lookup"><span data-stu-id="43bf9-881">Confirm that the *web.config* file is present in the deployment and that its contents are correct.</span></span>
   * <span data-ttu-id="43bf9-882">Při hostování na Azure App Service potvrďte, že je aplikace nasazená do `D:\home\site\wwwroot` složky.</span><span class="sxs-lookup"><span data-stu-id="43bf9-882">When hosting on Azure App Service, confirm that the app is deployed to the `D:\home\site\wwwroot` folder.</span></span>
   * <span data-ttu-id="43bf9-883">Když je aplikace hostovaná službou IIS, zkontrolujte, že je aplikace nasazená na **fyzickou cestu** služby IIS, která se zobrazuje v **základním nastavení** **Správce služby IIS**.</span><span class="sxs-lookup"><span data-stu-id="43bf9-883">When the app is hosted by IIS, confirm that the app is deployed to the IIS **Physical path** shown in **IIS Manager**'s **Basic Settings**.</span></span>
1. <span data-ttu-id="43bf9-884">Ověřte, zda jsou všechny soubory a složky aplikace nasazeny porovnáním nasazení v hostitelském systému s obsahem složky *publikování* projektu.</span><span class="sxs-lookup"><span data-stu-id="43bf9-884">Confirm that all of the app's files and folders are deployed by comparing the deployment on the hosting system to the contents of the project's *publish* folder.</span></span>

<span data-ttu-id="43bf9-885">Další informace o rozložení publikované aplikace ASP.NET Core najdete v tématu <xref:host-and-deploy/directory-structure>.</span><span class="sxs-lookup"><span data-stu-id="43bf9-885">For more information on the layout of a published ASP.NET Core app, see <xref:host-and-deploy/directory-structure>.</span></span> <span data-ttu-id="43bf9-886">Další informace o souboru *Web. config* naleznete v tématu <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>.</span><span class="sxs-lookup"><span data-stu-id="43bf9-886">For more information on the *web.config* file, see <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>.</span></span>

### <a name="500-internal-server-error"></a><span data-ttu-id="43bf9-887">500 – Interní chyba serveru</span><span class="sxs-lookup"><span data-stu-id="43bf9-887">500 Internal Server Error</span></span>

<span data-ttu-id="43bf9-888">Spuštění aplikace, ale chybu brání splnění žádosti. na serveru.</span><span class="sxs-lookup"><span data-stu-id="43bf9-888">The app starts, but an error prevents the server from fulfilling the request.</span></span>

<span data-ttu-id="43bf9-889">Při spuštění nebo při vytváření odpovědi, k této chybě dochází v kódu aplikace.</span><span class="sxs-lookup"><span data-stu-id="43bf9-889">This error occurs within the app's code during startup or while creating a response.</span></span> <span data-ttu-id="43bf9-890">Odpověď možná neobsahuje žádný obsah, nebo se odpověď může zobrazit jako *500 interní chyba serveru* v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="43bf9-890">The response may contain no content, or the response may appear as a *500 Internal Server Error* in the browser.</span></span> <span data-ttu-id="43bf9-891">V protokolu událostí aplikace obvykle hlásí, že aplikace se normálně spustit.</span><span class="sxs-lookup"><span data-stu-id="43bf9-891">The Application Event Log usually states that the app started normally.</span></span> <span data-ttu-id="43bf9-892">Z pohledu serveru, který je správný.</span><span class="sxs-lookup"><span data-stu-id="43bf9-892">From the server's perspective, that's correct.</span></span> <span data-ttu-id="43bf9-893">Aplikace začal, ale nemůže generovat platnou odpověď.</span><span class="sxs-lookup"><span data-stu-id="43bf9-893">The app did start, but it can't generate a valid response.</span></span> <span data-ttu-id="43bf9-894">Spusťte aplikaci na příkazovém řádku na serveru nebo povolte řešení potíží pomocí protokolu stdout modulu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="43bf9-894">Run the app at a command prompt on the server or enable the ASP.NET Core Module stdout log to troubleshoot the problem.</span></span>

### <a name="5025-process-failure"></a><span data-ttu-id="43bf9-895">502.5 zpracovat selhání</span><span class="sxs-lookup"><span data-stu-id="43bf9-895">502.5 Process Failure</span></span>

<span data-ttu-id="43bf9-896">Pracovní proces se nezdaří.</span><span class="sxs-lookup"><span data-stu-id="43bf9-896">The worker process fails.</span></span> <span data-ttu-id="43bf9-897">Aplikace se nespustí.</span><span class="sxs-lookup"><span data-stu-id="43bf9-897">The app doesn't start.</span></span>

<span data-ttu-id="43bf9-898">[Modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module) se pokusí spustit pracovní proces, ale jeho spuštění se nezdařilo.</span><span class="sxs-lookup"><span data-stu-id="43bf9-898">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the worker process but it fails to start.</span></span> <span data-ttu-id="43bf9-899">Příčinou selhání spuštění procesu může být obvykle určení z položek protokolu událostí aplikace a protokolu stdout modulu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="43bf9-899">The cause of a process startup failure can usually be determined from entries in the Application Event Log and the ASP.NET Core Module stdout log.</span></span>

<span data-ttu-id="43bf9-900">Běžné chyby je, že aplikace je špatně nakonfigurovaný. kvůli cílení na určitou verzi rozhraní framework sdílené ASP.NET Core, který není k dispozici.</span><span class="sxs-lookup"><span data-stu-id="43bf9-900">A common failure condition is the app is misconfigured due to targeting a version of the ASP.NET Core shared framework that isn't present.</span></span> <span data-ttu-id="43bf9-901">Zkontrolujte, jaké verze rozhraní framework ASP.NET Core sdílené jsou nainstalovány v cílovém počítači.</span><span class="sxs-lookup"><span data-stu-id="43bf9-901">Check which versions of the ASP.NET Core shared framework are installed on the target machine.</span></span> <span data-ttu-id="43bf9-902">*Sdílené rozhraní* je sada sestavení (soubory *. dll* ), které jsou nainstalovány na počítači a na které odkazuje Metapackage jako `Microsoft.AspNetCore.App`.</span><span class="sxs-lookup"><span data-stu-id="43bf9-902">The *shared framework* is the set of assemblies (*.dll* files) that are installed on the machine and referenced by a metapackage such as `Microsoft.AspNetCore.App`.</span></span> <span data-ttu-id="43bf9-903">Odkaz Metapackage může určovat minimální požadovanou verzi.</span><span class="sxs-lookup"><span data-stu-id="43bf9-903">The metapackage reference can specify a minimum required version.</span></span> <span data-ttu-id="43bf9-904">Další informace najdete v tématu [sdílené rozhraní](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span><span class="sxs-lookup"><span data-stu-id="43bf9-904">For more information, see [The shared framework](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span></span>

<span data-ttu-id="43bf9-905">Chybová stránka *selhání procesu 502,5* se vrátí v případě, že při selhání hostování nebo aplikace dojde k chybě pracovního procesu:</span><span class="sxs-lookup"><span data-stu-id="43bf9-905">The *502.5 Process Failure* error page is returned when a hosting or app misconfiguration causes the worker process to fail:</span></span>

### <a name="failed-to-start-application-errorcode-0x800700c1"></a><span data-ttu-id="43bf9-906">Nepovedlo se spustit aplikaci (kód chyby "0x800700c1")</span><span class="sxs-lookup"><span data-stu-id="43bf9-906">Failed to start application (ErrorCode '0x800700c1')</span></span>

```
EventID: 1010
Source: IIS AspNetCore Module V2
Failed to start application '/LM/W3SVC/6/ROOT/', ErrorCode '0x800700c1'.
```

<span data-ttu-id="43bf9-907">Aplikaci se nepovedlo spustit, protože se nepovedlo načíst sestavení aplikace ( *. dll*).</span><span class="sxs-lookup"><span data-stu-id="43bf9-907">The app failed to start because the app's assembly (*.dll*) couldn't be loaded.</span></span>

<span data-ttu-id="43bf9-908">Tato chyba nastane, pokud došlo k neshodě bitové verze mezi publikované aplikace a proces w3wp/iisexpress.</span><span class="sxs-lookup"><span data-stu-id="43bf9-908">This error occurs when there's a bitness mismatch between the published app and the w3wp/iisexpress process.</span></span>

<span data-ttu-id="43bf9-909">Ověřte správnost nastavení 32-bit fondu aplikací:</span><span class="sxs-lookup"><span data-stu-id="43bf9-909">Confirm that the app pool's 32-bit setting is correct:</span></span>

1. <span data-ttu-id="43bf9-910">Vyberte fond aplikací v **fondech aplikací**Správce služby IIS.</span><span class="sxs-lookup"><span data-stu-id="43bf9-910">Select the app pool in IIS Manager's **Application Pools**.</span></span>
1. <span data-ttu-id="43bf9-911">V části **Upravit fond aplikací** na panelu **Akce** vyberte **Upřesnit nastavení** .</span><span class="sxs-lookup"><span data-stu-id="43bf9-911">Select **Advanced Settings** under **Edit Application Pool** in the **Actions** panel.</span></span>
1. <span data-ttu-id="43bf9-912">Nastavte **možnost povolit 32 – bitové aplikace**:</span><span class="sxs-lookup"><span data-stu-id="43bf9-912">Set **Enable 32-Bit Applications**:</span></span>
   * <span data-ttu-id="43bf9-913">Pokud nasazujete 32 (x86) aplikaci, nastavte hodnotu na `True`.</span><span class="sxs-lookup"><span data-stu-id="43bf9-913">If deploying a 32-bit (x86) app, set the value to `True`.</span></span>
   * <span data-ttu-id="43bf9-914">Pokud nasazujete 64 aplikaci (x64), nastavte hodnotu na `False`.</span><span class="sxs-lookup"><span data-stu-id="43bf9-914">If deploying a 64-bit (x64) app, set the value to `False`.</span></span>

<span data-ttu-id="43bf9-915">Ověřte, že mezi vlastností `<Platform>` MSBuild v souboru projektu a publikovaným bitová verzeem aplikace nedochází ke konfliktu.</span><span class="sxs-lookup"><span data-stu-id="43bf9-915">Confirm that there isn't a conflict between a `<Platform>` MSBuild property in the project file and the published bitness of the app.</span></span>

### <a name="connection-reset"></a><span data-ttu-id="43bf9-916">Obnovení připojení</span><span class="sxs-lookup"><span data-stu-id="43bf9-916">Connection reset</span></span>

<span data-ttu-id="43bf9-917">Pokud dojde k chybě po odeslání hlaviček, je příliš pozdě pro server, který odešle **500 interní chybu serveru** , když dojde k chybě.</span><span class="sxs-lookup"><span data-stu-id="43bf9-917">If an error occurs after the headers are sent, it's too late for the server to send a **500 Internal Server Error** when an error occurs.</span></span> <span data-ttu-id="43bf9-918">Často se to stane, když dojde k chybě při serializaci složitých objektů pro odpověď.</span><span class="sxs-lookup"><span data-stu-id="43bf9-918">This often happens when an error occurs during the serialization of complex objects for a response.</span></span> <span data-ttu-id="43bf9-919">Tento typ chyby se zobrazí jako chyba *resetování připojení* na klientovi.</span><span class="sxs-lookup"><span data-stu-id="43bf9-919">This type of error appears as a *connection reset* error on the client.</span></span> <span data-ttu-id="43bf9-920">[Protokolování aplikace](xref:fundamentals/logging/index) může pomoct řešit tyto typy chyb.</span><span class="sxs-lookup"><span data-stu-id="43bf9-920">[Application logging](xref:fundamentals/logging/index) can help troubleshoot these types of errors.</span></span>

### <a name="default-startup-limits"></a><span data-ttu-id="43bf9-921">Výchozí omezení při spuštění</span><span class="sxs-lookup"><span data-stu-id="43bf9-921">Default startup limits</span></span>

<span data-ttu-id="43bf9-922">[ASP.NET Core modul](xref:host-and-deploy/aspnet-core-module) je nakonfigurovaný s výchozí *startupTimeLimitou* 120 sekund.</span><span class="sxs-lookup"><span data-stu-id="43bf9-922">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) is configured with a default *startupTimeLimit* of 120 seconds.</span></span> <span data-ttu-id="43bf9-923">Když necháte na výchozí hodnotu, aplikace může trvat až dvě minuty, spusťte před modulu protokoly selhání procesu.</span><span class="sxs-lookup"><span data-stu-id="43bf9-923">When left at the default value, an app may take up to two minutes to start before the module logs a process failure.</span></span> <span data-ttu-id="43bf9-924">Informace o konfiguraci modulu naleznete v tématu [atributy elementu aspNetCore](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span><span class="sxs-lookup"><span data-stu-id="43bf9-924">For information on configuring the module, see [Attributes of the aspNetCore element](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span></span>

## <a name="troubleshoot-on-azure-app-service"></a><span data-ttu-id="43bf9-925">Řešení potíží na Azure App Service</span><span class="sxs-lookup"><span data-stu-id="43bf9-925">Troubleshoot on Azure App Service</span></span>

[!INCLUDE [Azure App Service Preview Notice](~/includes/azure-apps-preview-notice.md)]

### <a name="application-event-log-azure-app-service"></a><span data-ttu-id="43bf9-926">Protokol událostí aplikace (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="43bf9-926">Application Event Log (Azure App Service)</span></span>

<span data-ttu-id="43bf9-927">Chcete-li získat přístup k protokolu událostí aplikace, použijte okno **Diagnostika a řešení problémů** v Azure Portal:</span><span class="sxs-lookup"><span data-stu-id="43bf9-927">To access the Application Event Log, use the **Diagnose and solve problems** blade in the Azure portal:</span></span>

1. <span data-ttu-id="43bf9-928">V Azure Portal otevřete aplikaci v **App Services**.</span><span class="sxs-lookup"><span data-stu-id="43bf9-928">In the Azure portal, open the app in **App Services**.</span></span>
1. <span data-ttu-id="43bf9-929">Vyberte možnost **diagnostikovat a vyřešit problémy**.</span><span class="sxs-lookup"><span data-stu-id="43bf9-929">Select **Diagnose and solve problems**.</span></span>
1. <span data-ttu-id="43bf9-930">Vyberte **diagnostické nástroje** záhlaví.</span><span class="sxs-lookup"><span data-stu-id="43bf9-930">Select the **Diagnostic Tools** heading.</span></span>
1. <span data-ttu-id="43bf9-931">V nabídce **nástroje podpory**vyberte tlačítko **události aplikace** .</span><span class="sxs-lookup"><span data-stu-id="43bf9-931">Under **Support Tools**, select the **Application Events** button.</span></span>
1. <span data-ttu-id="43bf9-932">Projděte si nejnovější chybu, kterou poskytla položka *IIS AspNetCoreModule* nebo *IIS AspNetCoreModule v2* ve **zdrojovém** sloupci.</span><span class="sxs-lookup"><span data-stu-id="43bf9-932">Examine the latest error provided by the *IIS AspNetCoreModule* or *IIS AspNetCoreModule V2* entry in the **Source** column.</span></span>

<span data-ttu-id="43bf9-933">Alternativou k použití okna **diagnostikovat a řešit problémy** je kontrola souboru protokolu událostí aplikace přímo pomocí [Kudu](https://github.com/projectkudu/kudu/wiki):</span><span class="sxs-lookup"><span data-stu-id="43bf9-933">An alternative to using the **Diagnose and solve problems** blade is to examine the Application Event Log file directly using [Kudu](https://github.com/projectkudu/kudu/wiki):</span></span>

1. <span data-ttu-id="43bf9-934">Otevřete **Rozšířené nástroje** v oblasti **vývojové nástroje** .</span><span class="sxs-lookup"><span data-stu-id="43bf9-934">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="43bf9-935">Vyberte tlačítko **přejít&rarr;** .</span><span class="sxs-lookup"><span data-stu-id="43bf9-935">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="43bf9-936">Konzola Kudu se otevře v novém okně nebo záložce prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="43bf9-936">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="43bf9-937">Pomocí navigačního panelu v horní části stránky otevřete **konzolu ladění** a vyberte **cmd**.</span><span class="sxs-lookup"><span data-stu-id="43bf9-937">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="43bf9-938">Otevřete složku **soubory protokolů** .</span><span class="sxs-lookup"><span data-stu-id="43bf9-938">Open the **LogFiles** folder.</span></span>
1. <span data-ttu-id="43bf9-939">Vyberte ikonu tužky vedle souboru *EventLog. XML* .</span><span class="sxs-lookup"><span data-stu-id="43bf9-939">Select the pencil icon next to the *eventlog.xml* file.</span></span>
1. <span data-ttu-id="43bf9-940">Projděte si protokol.</span><span class="sxs-lookup"><span data-stu-id="43bf9-940">Examine the log.</span></span> <span data-ttu-id="43bf9-941">Posuňte se do dolní části protokolu, abyste viděli nejaktuálnější události.</span><span class="sxs-lookup"><span data-stu-id="43bf9-941">Scroll to the bottom of the log to see the most recent events.</span></span>

### <a name="run-the-app-in-the-kudu-console"></a><span data-ttu-id="43bf9-942">Spuštění aplikace v konzole Kudu</span><span class="sxs-lookup"><span data-stu-id="43bf9-942">Run the app in the Kudu console</span></span>

<span data-ttu-id="43bf9-943">Mnoho chyb při spuštění nevytvářejí užitečné informace v protokolu událostí aplikace.</span><span class="sxs-lookup"><span data-stu-id="43bf9-943">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="43bf9-944">Tuto chybu můžete zjistit spuštěním aplikace v konzole vzdáleného spuštění [Kudu](https://github.com/projectkudu/kudu/wiki) :</span><span class="sxs-lookup"><span data-stu-id="43bf9-944">You can run the app in the [Kudu](https://github.com/projectkudu/kudu/wiki) Remote Execution Console to discover the error:</span></span>

1. <span data-ttu-id="43bf9-945">Otevřete **Rozšířené nástroje** v oblasti **vývojové nástroje** .</span><span class="sxs-lookup"><span data-stu-id="43bf9-945">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="43bf9-946">Vyberte tlačítko **přejít&rarr;** .</span><span class="sxs-lookup"><span data-stu-id="43bf9-946">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="43bf9-947">Konzola Kudu se otevře v novém okně nebo záložce prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="43bf9-947">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="43bf9-948">Pomocí navigačního panelu v horní části stránky otevřete **konzolu ladění** a vyberte **cmd**.</span><span class="sxs-lookup"><span data-stu-id="43bf9-948">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>

#### <a name="test-a-32-bit-x86-app"></a><span data-ttu-id="43bf9-949">Testování 32 (x86) aplikace</span><span class="sxs-lookup"><span data-stu-id="43bf9-949">Test a 32-bit (x86) app</span></span>

<span data-ttu-id="43bf9-950">**Aktuální verze**</span><span class="sxs-lookup"><span data-stu-id="43bf9-950">**Current release**</span></span>

1. `cd d:\home\site\wwwroot`
1. <span data-ttu-id="43bf9-951">Spusťte aplikaci:</span><span class="sxs-lookup"><span data-stu-id="43bf9-951">Run the app:</span></span>
   * <span data-ttu-id="43bf9-952">Pokud aplikace je [nasazení závislé na rozhraní](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span><span class="sxs-lookup"><span data-stu-id="43bf9-952">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

     ```dotnetcli
     dotnet .\{ASSEMBLY NAME}.dll
     ```

   * <span data-ttu-id="43bf9-953">Pokud je aplikace samostatná, [nasazení](/dotnet/core/deploying/#self-contained-deployments-scd):</span><span class="sxs-lookup"><span data-stu-id="43bf9-953">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

     ```console
     {ASSEMBLY NAME}.exe
     ```

<span data-ttu-id="43bf9-954">Výstup konzoly z aplikace, v němž se zobrazují všechny chyby, je kanálem do konzoly Kudu.</span><span class="sxs-lookup"><span data-stu-id="43bf9-954">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="43bf9-955">**Nasazení závislé na architektuře spuštěné ve verzi Preview**</span><span class="sxs-lookup"><span data-stu-id="43bf9-955">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="43bf9-956">*Vyžaduje instalaci rozšíření webu ASP.NET Core {VERSION} (x86) Runtime.*</span><span class="sxs-lookup"><span data-stu-id="43bf9-956">*Requires installing the ASP.NET Core {VERSION} (x86) Runtime site extension.*</span></span>

1. <span data-ttu-id="43bf9-957">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32` (`{X.Y}` je verze modulu runtime)</span><span class="sxs-lookup"><span data-stu-id="43bf9-957">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="43bf9-958">Spusťte aplikaci: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="43bf9-958">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="43bf9-959">Výstup konzoly z aplikace, v němž se zobrazují všechny chyby, je kanálem do konzoly Kudu.</span><span class="sxs-lookup"><span data-stu-id="43bf9-959">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

#### <a name="test-a-64-bit-x64-app"></a><span data-ttu-id="43bf9-960">Testování 64 aplikace (x64)</span><span class="sxs-lookup"><span data-stu-id="43bf9-960">Test a 64-bit (x64) app</span></span>

<span data-ttu-id="43bf9-961">**Aktuální verze**</span><span class="sxs-lookup"><span data-stu-id="43bf9-961">**Current release**</span></span>

* <span data-ttu-id="43bf9-962">Pokud je aplikace [nasazením závislého na rozhraní](/dotnet/core/deploying/#framework-dependent-deployments-fdd)64 (x64):</span><span class="sxs-lookup"><span data-stu-id="43bf9-962">If the app is a 64-bit (x64) [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>
  1. `cd D:\Program Files\dotnet`
  1. <span data-ttu-id="43bf9-963">Spusťte aplikaci: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="43bf9-963">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>
* <span data-ttu-id="43bf9-964">Pokud je aplikace samostatná, [nasazení](/dotnet/core/deploying/#self-contained-deployments-scd):</span><span class="sxs-lookup"><span data-stu-id="43bf9-964">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>
  1. `cd D:\home\site\wwwroot`
  1. <span data-ttu-id="43bf9-965">Spusťte aplikaci: `{ASSEMBLY NAME}.exe`</span><span class="sxs-lookup"><span data-stu-id="43bf9-965">Run the app: `{ASSEMBLY NAME}.exe`</span></span>

<span data-ttu-id="43bf9-966">Výstup konzoly z aplikace, v němž se zobrazují všechny chyby, je kanálem do konzoly Kudu.</span><span class="sxs-lookup"><span data-stu-id="43bf9-966">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="43bf9-967">**Nasazení závislé na architektuře spuštěné ve verzi Preview**</span><span class="sxs-lookup"><span data-stu-id="43bf9-967">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="43bf9-968">*Vyžaduje instalaci rozšíření webu ASP.NET Core {VERSION} (x64) Runtime.*</span><span class="sxs-lookup"><span data-stu-id="43bf9-968">*Requires installing the ASP.NET Core {VERSION} (x64) Runtime site extension.*</span></span>

1. <span data-ttu-id="43bf9-969">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64` (`{X.Y}` je verze modulu runtime)</span><span class="sxs-lookup"><span data-stu-id="43bf9-969">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="43bf9-970">Spusťte aplikaci: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="43bf9-970">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="43bf9-971">Výstup konzoly z aplikace, v němž se zobrazují všechny chyby, je kanálem do konzoly Kudu.</span><span class="sxs-lookup"><span data-stu-id="43bf9-971">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

### <a name="aspnet-core-module-stdout-log-azure-app-service"></a><span data-ttu-id="43bf9-972">Protokol stdout v modulu ASP.NET Core (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="43bf9-972">ASP.NET Core Module stdout log (Azure App Service)</span></span>

<span data-ttu-id="43bf9-973">Protokol stdout modulu ASP.NET Coree často zaznamenává užitečné chybové zprávy, které se nenašly v protokolu událostí aplikace.</span><span class="sxs-lookup"><span data-stu-id="43bf9-973">The ASP.NET Core Module stdout log often records useful error messages not found in the Application Event Log.</span></span> <span data-ttu-id="43bf9-974">Povolení a zobrazení protokolů stdout:</span><span class="sxs-lookup"><span data-stu-id="43bf9-974">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="43bf9-975">Přejděte do okna **diagnostikovat a řešit problémy** v Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="43bf9-975">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="43bf9-976">V části **Vybrat kategorii problému**vyberte tlačítko pro **webovou aplikaci** .</span><span class="sxs-lookup"><span data-stu-id="43bf9-976">Under **SELECT PROBLEM CATEGORY**, select the **Web App Down** button.</span></span>
1. <span data-ttu-id="43bf9-977">V části **navrhovaná řešení** > **Povolit přesměrování protokolu stdout**kliknutím na tlačítko **otevřete konzolu Kudu a upravte soubor Web. config**.</span><span class="sxs-lookup"><span data-stu-id="43bf9-977">Under **Suggested Solutions** > **Enable Stdout Log Redirection**, select the button to **Open Kudu Console to edit Web.Config**.</span></span>
1. <span data-ttu-id="43bf9-978">V **konzole diagnostiky**Kudu otevřete složky v **lokalitě** cesty > **wwwroot**.</span><span class="sxs-lookup"><span data-stu-id="43bf9-978">In the Kudu **Diagnostic Console**, open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="43bf9-979">Posuňte se dolů a odhalte soubor *Web. config* v dolní části seznamu.</span><span class="sxs-lookup"><span data-stu-id="43bf9-979">Scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="43bf9-980">Klikněte na ikonu tužky vedle souboru *Web. config* .</span><span class="sxs-lookup"><span data-stu-id="43bf9-980">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="43bf9-981">Nastavte **stdoutLogEnabled** na `true` a změňte cestu **stdoutLogFile** na: `\\?\%home%\LogFiles\stdout`.</span><span class="sxs-lookup"><span data-stu-id="43bf9-981">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="43bf9-982">Vyberte **Uložit** a uložte aktualizovaný soubor *Web. config* .</span><span class="sxs-lookup"><span data-stu-id="43bf9-982">Select **Save** to save the updated *web.config* file.</span></span>
1. <span data-ttu-id="43bf9-983">Vytvořte žádost do aplikace.</span><span class="sxs-lookup"><span data-stu-id="43bf9-983">Make a request to the app.</span></span>
1. <span data-ttu-id="43bf9-984">Vraťte se na Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="43bf9-984">Return to the Azure portal.</span></span> <span data-ttu-id="43bf9-985">V oblasti **vývojové nástroje** vyberte okno **Pokročilé nástroje** .</span><span class="sxs-lookup"><span data-stu-id="43bf9-985">Select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="43bf9-986">Vyberte tlačítko **přejít&rarr;** .</span><span class="sxs-lookup"><span data-stu-id="43bf9-986">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="43bf9-987">Konzola Kudu se otevře v novém okně nebo záložce prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="43bf9-987">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="43bf9-988">Pomocí navigačního panelu v horní části stránky otevřete **konzolu ladění** a vyberte **cmd**.</span><span class="sxs-lookup"><span data-stu-id="43bf9-988">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="43bf9-989">Vyberte složku **soubory protokolů** .</span><span class="sxs-lookup"><span data-stu-id="43bf9-989">Select the **LogFiles** folder.</span></span>
1. <span data-ttu-id="43bf9-990">Zkontrolujte **upravený** sloupec a vyberte ikonu tužky a upravte protokol stdout s nejnovějším datem úpravy.</span><span class="sxs-lookup"><span data-stu-id="43bf9-990">Inspect the **Modified** column and select the pencil icon to edit the stdout log with the latest modification date.</span></span>
1. <span data-ttu-id="43bf9-991">Po otevření souboru protokolu se zobrazí chyba.</span><span class="sxs-lookup"><span data-stu-id="43bf9-991">When the log file opens, the error is displayed.</span></span>

<span data-ttu-id="43bf9-992">Zakázat protokolování stdout při odstraňování potíží:</span><span class="sxs-lookup"><span data-stu-id="43bf9-992">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="43bf9-993">V **diagnostické konzole**Kudu se vraťte do **lokality** cesty > **wwwroot** , aby se soubor *Web. config* vykryl.</span><span class="sxs-lookup"><span data-stu-id="43bf9-993">In the Kudu **Diagnostic Console**, return to the path **site** > **wwwroot** to reveal the *web.config* file.</span></span> <span data-ttu-id="43bf9-994">Otevřete soubor **Web. config** znovu výběrem ikony tužky.</span><span class="sxs-lookup"><span data-stu-id="43bf9-994">Open the **web.config** file again by selecting the pencil icon.</span></span>
1. <span data-ttu-id="43bf9-995">Nastavte **stdoutLogEnabled** na `false`.</span><span class="sxs-lookup"><span data-stu-id="43bf9-995">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="43bf9-996">Vyberte **Uložit** a soubor uložte.</span><span class="sxs-lookup"><span data-stu-id="43bf9-996">Select **Save** to save the file.</span></span>

<span data-ttu-id="43bf9-997">Další informace naleznete v tématu <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span><span class="sxs-lookup"><span data-stu-id="43bf9-997">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

> [!WARNING]
> <span data-ttu-id="43bf9-998">Nepodařilo se zakázat protokol stdout může vést k selhání aplikace nebo serveru.</span><span class="sxs-lookup"><span data-stu-id="43bf9-998">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="43bf9-999">Neexistuje žádné omezení velikosti souboru protokolu nebo počet souborů protokolů, které jsou vytvořeny.</span><span class="sxs-lookup"><span data-stu-id="43bf9-999">There's no limit on log file size or the number of log files created.</span></span> <span data-ttu-id="43bf9-1000">K řešení problémů se spouštěním aplikací použijte pouze protokolování STDOUT.</span><span class="sxs-lookup"><span data-stu-id="43bf9-1000">Only use stdout logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="43bf9-1001">Pro obecné protokolování v aplikaci ASP.NET Core po spuštění použijte knihovnu protokolování, která omezuje velikost souboru protokolu a otočí protokoly.</span><span class="sxs-lookup"><span data-stu-id="43bf9-1001">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="43bf9-1002">Další informace najdete v tématu [Zprostředkovatelé protokolování třetích stran](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="43bf9-1002">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="slow-or-hanging-app-azure-app-service"></a><span data-ttu-id="43bf9-1003">Pomalá nebo zavěšená aplikace (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="43bf9-1003">Slow or hanging app (Azure App Service)</span></span>

<span data-ttu-id="43bf9-1004">Pokud aplikace reaguje pomalu nebo přestane reagovat na žádost, přečtěte si následující články:</span><span class="sxs-lookup"><span data-stu-id="43bf9-1004">When an app responds slowly or hangs on a request, see the following articles:</span></span>

* [<span data-ttu-id="43bf9-1005">Řešení potíží s výkonem pomalých webových aplikací ve službě Azure App Service</span><span class="sxs-lookup"><span data-stu-id="43bf9-1005">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="43bf9-1006">K zachycení výpisu problémů s přerušovanou výjimkou nebo problémy s výkonem ve webové aplikaci Azure použijte rozšíření pro diagnostiku zhroucení serveru.</span><span class="sxs-lookup"><span data-stu-id="43bf9-1006">Use Crash Diagnoser Site Extension to Capture Dump for Intermittent Exception issues or performance issues on Azure Web App</span></span>](https://blogs.msdn.microsoft.com/asiatech/2015/12/28/use-crash-diagnoser-site-extension-to-capture-dump-for-intermittent-exception-issues-or-performance-issues-on-azure-web-app/)

### <a name="monitoring-blades"></a><span data-ttu-id="43bf9-1007">Okna monitorování</span><span class="sxs-lookup"><span data-stu-id="43bf9-1007">Monitoring blades</span></span>

<span data-ttu-id="43bf9-1008">Okna monitorování poskytují alternativní možnosti řešení potíží pro metody popsané dříve v tématu.</span><span class="sxs-lookup"><span data-stu-id="43bf9-1008">Monitoring blades provide an alternative troubleshooting experience to the methods described earlier in the topic.</span></span> <span data-ttu-id="43bf9-1009">Pomocí těchto oken můžete diagnostikovat chyby 500-Series.</span><span class="sxs-lookup"><span data-stu-id="43bf9-1009">These blades can be used to diagnose 500-series errors.</span></span>

<span data-ttu-id="43bf9-1010">Potvrďte, že jsou nainstalovaná rozšíření ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="43bf9-1010">Confirm that the ASP.NET Core Extensions are installed.</span></span> <span data-ttu-id="43bf9-1011">Pokud nejsou rozšíření nainstalována, nainstalujte je ručně:</span><span class="sxs-lookup"><span data-stu-id="43bf9-1011">If the extensions aren't installed, install them manually:</span></span>

1. <span data-ttu-id="43bf9-1012">V části okno **vývojové nástroje** vyberte okno **rozšíření** .</span><span class="sxs-lookup"><span data-stu-id="43bf9-1012">In the **DEVELOPMENT TOOLS** blade section, select the **Extensions** blade.</span></span>
1. <span data-ttu-id="43bf9-1013">V seznamu by se měla zobrazit **rozšíření ASP.NET Core** .</span><span class="sxs-lookup"><span data-stu-id="43bf9-1013">The **ASP.NET Core Extensions** should appear in the list.</span></span>
1. <span data-ttu-id="43bf9-1014">Pokud nejsou rozšíření nainstalovaná, vyberte tlačítko **Přidat** .</span><span class="sxs-lookup"><span data-stu-id="43bf9-1014">If the extensions aren't installed, select the **Add** button.</span></span>
1. <span data-ttu-id="43bf9-1015">Ze seznamu vyberte **rozšíření ASP.NET Core** .</span><span class="sxs-lookup"><span data-stu-id="43bf9-1015">Choose the **ASP.NET Core Extensions** from the list.</span></span>
1. <span data-ttu-id="43bf9-1016">Kliknutím na **OK** přijměte právní podmínky.</span><span class="sxs-lookup"><span data-stu-id="43bf9-1016">Select **OK** to accept the legal terms.</span></span>
1. <span data-ttu-id="43bf9-1017">V okně **Přidat rozšíření** vyberte **OK** .</span><span class="sxs-lookup"><span data-stu-id="43bf9-1017">Select **OK** on the **Add extension** blade.</span></span>
1. <span data-ttu-id="43bf9-1018">Informační automaticky otevíraná zpráva indikuje, že se rozšíření úspěšně nainstalovala.</span><span class="sxs-lookup"><span data-stu-id="43bf9-1018">An informational pop-up message indicates when the extensions are successfully installed.</span></span>

<span data-ttu-id="43bf9-1019">Pokud není povoleno protokolování stdout, postupujte následovně:</span><span class="sxs-lookup"><span data-stu-id="43bf9-1019">If stdout logging isn't enabled, follow these steps:</span></span>

1. <span data-ttu-id="43bf9-1020">V Azure Portal v oblasti **vývojové nástroje** vyberte okno **Pokročilé nástroje** .</span><span class="sxs-lookup"><span data-stu-id="43bf9-1020">In the Azure portal, select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="43bf9-1021">Vyberte tlačítko **přejít&rarr;** .</span><span class="sxs-lookup"><span data-stu-id="43bf9-1021">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="43bf9-1022">Konzola Kudu se otevře v novém okně nebo záložce prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="43bf9-1022">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="43bf9-1023">Pomocí navigačního panelu v horní části stránky otevřete **konzolu ladění** a vyberte **cmd**.</span><span class="sxs-lookup"><span data-stu-id="43bf9-1023">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="43bf9-1024">Otevřete složky v **lokalitě** pro cestu > **wwwroot** a posuňte se dolů, aby se soubor *Web. config* vypnul v dolní části seznamu.</span><span class="sxs-lookup"><span data-stu-id="43bf9-1024">Open the folders to the path **site** > **wwwroot** and scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="43bf9-1025">Klikněte na ikonu tužky vedle souboru *Web. config* .</span><span class="sxs-lookup"><span data-stu-id="43bf9-1025">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="43bf9-1026">Nastavte **stdoutLogEnabled** na `true` a změňte cestu **stdoutLogFile** na: `\\?\%home%\LogFiles\stdout`.</span><span class="sxs-lookup"><span data-stu-id="43bf9-1026">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="43bf9-1027">Vyberte **Uložit** a uložte aktualizovaný soubor *Web. config* .</span><span class="sxs-lookup"><span data-stu-id="43bf9-1027">Select **Save** to save the updated *web.config* file.</span></span>

<span data-ttu-id="43bf9-1028">Pokračovat k aktivaci protokolování diagnostiky:</span><span class="sxs-lookup"><span data-stu-id="43bf9-1028">Proceed to activate diagnostic logging:</span></span>

1. <span data-ttu-id="43bf9-1029">V Azure Portal vyberte okno **diagnostické protokoly** .</span><span class="sxs-lookup"><span data-stu-id="43bf9-1029">In the Azure portal, select the **Diagnostics logs** blade.</span></span>
1. <span data-ttu-id="43bf9-1030">Vyberte přepínač **zapnuto** pro **protokolování aplikace (systém souborů)** a **podrobné chybové zprávy**.</span><span class="sxs-lookup"><span data-stu-id="43bf9-1030">Select the **On** switch for **Application Logging (Filesystem)** and **Detailed error messages**.</span></span> <span data-ttu-id="43bf9-1031">V horní části okna vyberte tlačítko **Uložit** .</span><span class="sxs-lookup"><span data-stu-id="43bf9-1031">Select the **Save** button at the top of the blade.</span></span>
1. <span data-ttu-id="43bf9-1032">Chcete-li zahrnout trasování chybných požadavků, označované také jako protokolování událostí neúspěšných požadavků na FREB, vyberte přepínač **zapnuto** pro **trasování chybných požadavků**.</span><span class="sxs-lookup"><span data-stu-id="43bf9-1032">To include failed request tracing, also known as Failed Request Event Buffering (FREB) logging, select the **On** switch for **Failed request tracing**.</span></span>
1. <span data-ttu-id="43bf9-1033">Vyberte okno **log Stream** , které je uvedeno hned pod oknem **diagnostické protokoly** na portálu.</span><span class="sxs-lookup"><span data-stu-id="43bf9-1033">Select the **Log stream** blade, which is listed immediately under the **Diagnostics logs** blade in the portal.</span></span>
1. <span data-ttu-id="43bf9-1034">Vytvořte žádost do aplikace.</span><span class="sxs-lookup"><span data-stu-id="43bf9-1034">Make a request to the app.</span></span>
1. <span data-ttu-id="43bf9-1035">V datech streamu protokolu se označuje příčina chyby.</span><span class="sxs-lookup"><span data-stu-id="43bf9-1035">Within the log stream data, the cause of the error is indicated.</span></span>

<span data-ttu-id="43bf9-1036">Nezapomeňte zakázat protokolování stdout po dokončení řešení potíží.</span><span class="sxs-lookup"><span data-stu-id="43bf9-1036">Be sure to disable stdout logging when troubleshooting is complete.</span></span>

<span data-ttu-id="43bf9-1037">Zobrazení protokolů pro trasování chybných požadavků (protokoly FREB):</span><span class="sxs-lookup"><span data-stu-id="43bf9-1037">To view the failed request tracing logs (FREB logs):</span></span>

1. <span data-ttu-id="43bf9-1038">Přejděte do okna **diagnostikovat a řešit problémy** v Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="43bf9-1038">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="43bf9-1039">V oblasti **nástroje podpory** v postranním panelu vyberte **protokoly pro trasování chybných požadavků** .</span><span class="sxs-lookup"><span data-stu-id="43bf9-1039">Select **Failed Request Tracing Logs** from the **SUPPORT TOOLS** area of the sidebar.</span></span>

<span data-ttu-id="43bf9-1040">V [části Povolení protokolování diagnostiky pro webové aplikace v Azure App Service tématu](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) a [Nejčastější dotazy týkající se výkonu aplikace pro Web Apps v Azure: návody zapnout trasování chybných požadavků?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing) Další informace.</span><span class="sxs-lookup"><span data-stu-id="43bf9-1040">See [Failed request traces section of the Enable diagnostics logging for web apps in Azure App Service topic](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) and the [Application performance FAQs for Web Apps in Azure: How do I turn on failed request tracing?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing) for more information.</span></span>

<span data-ttu-id="43bf9-1041">Další informace najdete v tématu [Povolení protokolování diagnostiky pro webové aplikace v Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log).</span><span class="sxs-lookup"><span data-stu-id="43bf9-1041">For more information, see [Enable diagnostics logging for web apps in Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log).</span></span>

> [!WARNING]
> <span data-ttu-id="43bf9-1042">Nepodařilo se zakázat protokol stdout může vést k selhání aplikace nebo serveru.</span><span class="sxs-lookup"><span data-stu-id="43bf9-1042">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="43bf9-1043">Neexistuje žádné omezení velikosti souboru protokolu nebo počet souborů protokolů, které jsou vytvořeny.</span><span class="sxs-lookup"><span data-stu-id="43bf9-1043">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="43bf9-1044">Pro rutiny protokolování v aplikaci ASP.NET Core, použijte protokolování knihovnu, která omezuje velikost souboru protokolu a otočí protokoly.</span><span class="sxs-lookup"><span data-stu-id="43bf9-1044">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="43bf9-1045">Další informace najdete v tématu [Zprostředkovatelé protokolování třetích stran](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="43bf9-1045">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

## <a name="troubleshoot-on-iis"></a><span data-ttu-id="43bf9-1046">Řešení potíží ve službě IIS</span><span class="sxs-lookup"><span data-stu-id="43bf9-1046">Troubleshoot on IIS</span></span>

### <a name="application-event-log-iis"></a><span data-ttu-id="43bf9-1047">Protokol událostí aplikace (IIS)</span><span class="sxs-lookup"><span data-stu-id="43bf9-1047">Application Event Log (IIS)</span></span>

<span data-ttu-id="43bf9-1048">Přístup k protokolu událostí aplikace:</span><span class="sxs-lookup"><span data-stu-id="43bf9-1048">Access the Application Event Log:</span></span>

1. <span data-ttu-id="43bf9-1049">Otevřete nabídku Start, vyhledejte *Prohlížeč událostí*a vyberte aplikaci **Prohlížeč událostí** .</span><span class="sxs-lookup"><span data-stu-id="43bf9-1049">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="43bf9-1050">V **Prohlížeč událostí**otevřete uzel **protokoly systému Windows** .</span><span class="sxs-lookup"><span data-stu-id="43bf9-1050">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="43bf9-1051">Výběrem **aplikace** otevřete protokol událostí aplikace.</span><span class="sxs-lookup"><span data-stu-id="43bf9-1051">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="43bf9-1052">Vyhledejte chyby související s selhání aplikace.</span><span class="sxs-lookup"><span data-stu-id="43bf9-1052">Search for errors associated with the failing app.</span></span> <span data-ttu-id="43bf9-1053">Chyby mají ve *zdrojovém* sloupci hodnotu *modulu IIS AspNetCore* nebo *modul IIS Express AspNetCore* .</span><span class="sxs-lookup"><span data-stu-id="43bf9-1053">Errors have a value of *IIS AspNetCore Module* or *IIS Express AspNetCore Module* in the *Source* column.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="43bf9-1054">Spuštění aplikace příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="43bf9-1054">Run the app at a command prompt</span></span>

<span data-ttu-id="43bf9-1055">Mnoho chyb při spuštění nevytvářejí užitečné informace v protokolu událostí aplikace.</span><span class="sxs-lookup"><span data-stu-id="43bf9-1055">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="43bf9-1056">Příčin některých chyb můžete najít spuštěním aplikace v příkazovém řádku v hostitelském systému.</span><span class="sxs-lookup"><span data-stu-id="43bf9-1056">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span>

#### <a name="framework-dependent-deployment"></a><span data-ttu-id="43bf9-1057">Nasazení závisí na architektuře</span><span class="sxs-lookup"><span data-stu-id="43bf9-1057">Framework-dependent deployment</span></span>

<span data-ttu-id="43bf9-1058">Pokud aplikace je [nasazení závislé na rozhraní](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span><span class="sxs-lookup"><span data-stu-id="43bf9-1058">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

1. <span data-ttu-id="43bf9-1059">Na příkazovém řádku přejděte do složky pro nasazení a spusťte aplikaci spuštěním sestavení aplikace pomocí příkazu *dotnet. exe*.</span><span class="sxs-lookup"><span data-stu-id="43bf9-1059">At a command prompt, navigate to the deployment folder and run the app by executing the app's assembly with *dotnet.exe*.</span></span> <span data-ttu-id="43bf9-1060">V následujícím příkazu nahraďte název sestavení aplikace pro \<assembly_name >: `dotnet .\<assembly_name>.dll`.</span><span class="sxs-lookup"><span data-stu-id="43bf9-1060">In the following command, substitute the name of the app's assembly for \<assembly_name>: `dotnet .\<assembly_name>.dll`.</span></span>
1. <span data-ttu-id="43bf9-1061">Výstup z aplikace zobrazuje všechny chyby konzoly je zapsán do okna konzoly.</span><span class="sxs-lookup"><span data-stu-id="43bf9-1061">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="43bf9-1062">Je-li této chybě dojde při požadavku na aplikaci, vytvořte žádost na hostitele a port, kde Kestrel naslouchá.</span><span class="sxs-lookup"><span data-stu-id="43bf9-1062">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="43bf9-1063">Pomocí výchozího hostitele a příspěvku vytvořte žádost o `http://localhost:5000/`.</span><span class="sxs-lookup"><span data-stu-id="43bf9-1063">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="43bf9-1064">Aplikace reaguje, obvykle na adrese Kestrel koncový bod, tím je pravděpodobnější týkající se konfigurace hostování a méně pravděpodobné, že v rámci aplikace.</span><span class="sxs-lookup"><span data-stu-id="43bf9-1064">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

#### <a name="self-contained-deployment"></a><span data-ttu-id="43bf9-1065">Samostatná nasazení</span><span class="sxs-lookup"><span data-stu-id="43bf9-1065">Self-contained deployment</span></span>

<span data-ttu-id="43bf9-1066">Pokud je aplikace samostatná, [nasazení](/dotnet/core/deploying/#self-contained-deployments-scd):</span><span class="sxs-lookup"><span data-stu-id="43bf9-1066">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

1. <span data-ttu-id="43bf9-1067">Na příkazovém řádku přejděte do složky pro nasazení a spuštění spustitelného souboru aplikace.</span><span class="sxs-lookup"><span data-stu-id="43bf9-1067">At a command prompt, navigate to the deployment folder and run the app's executable.</span></span> <span data-ttu-id="43bf9-1068">V následujícím příkazu nahraďte název sestavení aplikace pro \<assembly_name >: `<assembly_name>.exe`.</span><span class="sxs-lookup"><span data-stu-id="43bf9-1068">In the following command, substitute the name of the app's assembly for \<assembly_name>: `<assembly_name>.exe`.</span></span>
1. <span data-ttu-id="43bf9-1069">Výstup z aplikace zobrazuje všechny chyby konzoly je zapsán do okna konzoly.</span><span class="sxs-lookup"><span data-stu-id="43bf9-1069">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="43bf9-1070">Je-li této chybě dojde při požadavku na aplikaci, vytvořte žádost na hostitele a port, kde Kestrel naslouchá.</span><span class="sxs-lookup"><span data-stu-id="43bf9-1070">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="43bf9-1071">Pomocí výchozího hostitele a příspěvku vytvořte žádost o `http://localhost:5000/`.</span><span class="sxs-lookup"><span data-stu-id="43bf9-1071">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="43bf9-1072">Aplikace reaguje, obvykle na adrese Kestrel koncový bod, tím je pravděpodobnější týkající se konfigurace hostování a méně pravděpodobné, že v rámci aplikace.</span><span class="sxs-lookup"><span data-stu-id="43bf9-1072">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

### <a name="aspnet-core-module-stdout-log-iis"></a><span data-ttu-id="43bf9-1073">Protokol stdout v modulu ASP.NET Core (IIS)</span><span class="sxs-lookup"><span data-stu-id="43bf9-1073">ASP.NET Core Module stdout log (IIS)</span></span>

<span data-ttu-id="43bf9-1074">Povolení a zobrazení protokolů stdout:</span><span class="sxs-lookup"><span data-stu-id="43bf9-1074">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="43bf9-1075">Přejděte do složky pro nasazení webu v hostitelském systému.</span><span class="sxs-lookup"><span data-stu-id="43bf9-1075">Navigate to the site's deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="43bf9-1076">Pokud složka *logs* není k dispozici, vytvořte složku.</span><span class="sxs-lookup"><span data-stu-id="43bf9-1076">If the *logs* folder isn't present, create the folder.</span></span> <span data-ttu-id="43bf9-1077">Pokyny k tomu, jak povolit MSBuild pro vytvoření složky *logs* v nasazení automaticky, najdete v tématu [Struktura adresáře](xref:host-and-deploy/directory-structure) .</span><span class="sxs-lookup"><span data-stu-id="43bf9-1077">For instructions on how to enable MSBuild to create the *logs* folder in the deployment automatically, see the [Directory structure](xref:host-and-deploy/directory-structure) topic.</span></span>
1. <span data-ttu-id="43bf9-1078">Upravte soubor *Web. config* .</span><span class="sxs-lookup"><span data-stu-id="43bf9-1078">Edit the *web.config* file.</span></span> <span data-ttu-id="43bf9-1079">Nastavte **stdoutLogEnabled** na `true` a změňte cestu **stdoutLogFile** tak, aby odkazovala na složku *logs* (například `.\logs\stdout`).</span><span class="sxs-lookup"><span data-stu-id="43bf9-1079">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to point to the *logs* folder (for example, `.\logs\stdout`).</span></span> <span data-ttu-id="43bf9-1080">`stdout` v cestě je předpona názvu souboru protokolu.</span><span class="sxs-lookup"><span data-stu-id="43bf9-1080">`stdout` in the path is the log file name prefix.</span></span> <span data-ttu-id="43bf9-1081">Časové razítko, id procesu a příponu souboru jsou přidány automaticky při vytvoření protokolu.</span><span class="sxs-lookup"><span data-stu-id="43bf9-1081">A timestamp, process id, and file extension are added automatically when the log is created.</span></span> <span data-ttu-id="43bf9-1082">Při použití `stdout` jako předpony názvu souboru se typický soubor protokolu jmenuje *stdout_20180205184032_5412. log*.</span><span class="sxs-lookup"><span data-stu-id="43bf9-1082">Using `stdout` as the file name prefix, a typical log file is named *stdout_20180205184032_5412.log*.</span></span>
1. <span data-ttu-id="43bf9-1083">Zajistěte, aby identita fondu aplikací měla oprávnění k zápisu do složky *logs* .</span><span class="sxs-lookup"><span data-stu-id="43bf9-1083">Ensure your application pool's identity has write permissions to the *logs* folder.</span></span>
1. <span data-ttu-id="43bf9-1084">Uložte aktualizovaný soubor *Web. config* .</span><span class="sxs-lookup"><span data-stu-id="43bf9-1084">Save the updated *web.config* file.</span></span>
1. <span data-ttu-id="43bf9-1085">Vytvořte žádost do aplikace.</span><span class="sxs-lookup"><span data-stu-id="43bf9-1085">Make a request to the app.</span></span>
1. <span data-ttu-id="43bf9-1086">Přejděte do složky *logs* .</span><span class="sxs-lookup"><span data-stu-id="43bf9-1086">Navigate to the *logs* folder.</span></span> <span data-ttu-id="43bf9-1087">Vyhledání a otevření protokolu nejnovější stdout.</span><span class="sxs-lookup"><span data-stu-id="43bf9-1087">Find and open the most recent stdout log.</span></span>
1. <span data-ttu-id="43bf9-1088">Studie v protokolu chyb.</span><span class="sxs-lookup"><span data-stu-id="43bf9-1088">Study the log for errors.</span></span>

<span data-ttu-id="43bf9-1089">Zakázat protokolování stdout při odstraňování potíží:</span><span class="sxs-lookup"><span data-stu-id="43bf9-1089">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="43bf9-1090">Upravte soubor *Web. config* .</span><span class="sxs-lookup"><span data-stu-id="43bf9-1090">Edit the *web.config* file.</span></span>
1. <span data-ttu-id="43bf9-1091">Nastavte **stdoutLogEnabled** na `false`.</span><span class="sxs-lookup"><span data-stu-id="43bf9-1091">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="43bf9-1092">Uložte soubor.</span><span class="sxs-lookup"><span data-stu-id="43bf9-1092">Save the file.</span></span>

<span data-ttu-id="43bf9-1093">Další informace naleznete v tématu <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span><span class="sxs-lookup"><span data-stu-id="43bf9-1093">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

> [!WARNING]
> <span data-ttu-id="43bf9-1094">Nepodařilo se zakázat protokol stdout může vést k selhání aplikace nebo serveru.</span><span class="sxs-lookup"><span data-stu-id="43bf9-1094">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="43bf9-1095">Neexistuje žádné omezení velikosti souboru protokolu nebo počet souborů protokolů, které jsou vytvořeny.</span><span class="sxs-lookup"><span data-stu-id="43bf9-1095">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="43bf9-1096">Pro rutiny protokolování v aplikaci ASP.NET Core, použijte protokolování knihovnu, která omezuje velikost souboru protokolu a otočí protokoly.</span><span class="sxs-lookup"><span data-stu-id="43bf9-1096">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="43bf9-1097">Další informace najdete v tématu [Zprostředkovatelé protokolování třetích stran](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="43bf9-1097">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="enable-the-developer-exception-page"></a><span data-ttu-id="43bf9-1098">Povolit na stránce výjimek pro vývojáře</span><span class="sxs-lookup"><span data-stu-id="43bf9-1098">Enable the Developer Exception Page</span></span>

<span data-ttu-id="43bf9-1099">[Proměnnou prostředí `ASPNETCORE_ENVIRONMENT` lze přidat do souboru Web. config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) , aby bylo možné aplikaci spustit ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="43bf9-1099">The `ASPNETCORE_ENVIRONMENT` [environment variable can be added to web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) to run the app in the Development environment.</span></span> <span data-ttu-id="43bf9-1100">Pokud se prostředí nepřepisuje při spuštění aplikace `UseEnvironment` v Tvůrci hostitele, nastavení proměnné prostředí umožní, aby se při spuštění aplikace zobrazila [Stránka s výjimkou vývojářů](xref:fundamentals/error-handling) .</span><span class="sxs-lookup"><span data-stu-id="43bf9-1100">As long as the environment isn't overridden in app startup by `UseEnvironment` on the host builder, setting the environment variable allows the [Developer Exception Page](xref:fundamentals/error-handling) to appear when the app is run.</span></span>

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

<span data-ttu-id="43bf9-1101">Nastavení proměnné prostředí pro `ASPNETCORE_ENVIRONMENT` se doporučuje jenom pro použití na pracovních a testovacích serverech, které nejsou přístupné pro Internet.</span><span class="sxs-lookup"><span data-stu-id="43bf9-1101">Setting the environment variable for `ASPNETCORE_ENVIRONMENT` is only recommended for use on staging and testing servers that aren't exposed to the Internet.</span></span> <span data-ttu-id="43bf9-1102">Po vyřešení potíží odeberte proměnnou prostředí ze souboru *Web. config* .</span><span class="sxs-lookup"><span data-stu-id="43bf9-1102">Remove the environment variable from the *web.config* file after troubleshooting.</span></span> <span data-ttu-id="43bf9-1103">Informace o nastavení proměnných prostředí v *souboru Web. config*naleznete v tématu [environmentVariables Child element of aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).</span><span class="sxs-lookup"><span data-stu-id="43bf9-1103">For information on setting environment variables in *web.config*, see [environmentVariables child element of aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).</span></span>

### <a name="obtain-data-from-an-app"></a><span data-ttu-id="43bf9-1104">Získání dat z aplikace</span><span class="sxs-lookup"><span data-stu-id="43bf9-1104">Obtain data from an app</span></span>

<span data-ttu-id="43bf9-1105">Pokud aplikace je schopná reagovat na požadavky, získáte žádost o připojení a další data z aplikace pomocí terminálu vložené middlewaru.</span><span class="sxs-lookup"><span data-stu-id="43bf9-1105">If an app is capable of responding to requests, obtain request, connection, and additional data from the app using terminal inline middleware.</span></span> <span data-ttu-id="43bf9-1106">Další informace a ukázku kódu naleznete v tématu <xref:test/troubleshoot#obtain-data-from-an-app>.</span><span class="sxs-lookup"><span data-stu-id="43bf9-1106">For more information and sample code, see <xref:test/troubleshoot#obtain-data-from-an-app>.</span></span>

### <a name="slow-or-hanging-app-iis"></a><span data-ttu-id="43bf9-1107">Pomalá nebo zavěšená aplikace (IIS)</span><span class="sxs-lookup"><span data-stu-id="43bf9-1107">Slow or hanging app (IIS)</span></span>

<span data-ttu-id="43bf9-1108">*Výpis stavu* systému je snímek paměti systému a může vám pomůže určit příčinu selhání aplikace, selhání při spuštění nebo pomalé aplikace.</span><span class="sxs-lookup"><span data-stu-id="43bf9-1108">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="43bf9-1109">Aplikace selže nebo dojde k výjimce.</span><span class="sxs-lookup"><span data-stu-id="43bf9-1109">App crashes or encounters an exception</span></span>

<span data-ttu-id="43bf9-1110">Získat a analyzovat výpis z [zasílání zpráv o chybách systému Windows (WER)](/windows/desktop/wer/windows-error-reporting):</span><span class="sxs-lookup"><span data-stu-id="43bf9-1110">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="43bf9-1111">Vytvořte složku, do které se budou ukládat soubory s výpisem stavu systému na `c:\dumps`.</span><span class="sxs-lookup"><span data-stu-id="43bf9-1111">Create a folder to hold crash dump files at `c:\dumps`.</span></span> <span data-ttu-id="43bf9-1112">Fond aplikací musí mít ke složce přístup pro zápis.</span><span class="sxs-lookup"><span data-stu-id="43bf9-1112">The app pool must have write access to the folder.</span></span>
1. <span data-ttu-id="43bf9-1113">Spusťte [skript prostředí PowerShell pro EnableDumps](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1):</span><span class="sxs-lookup"><span data-stu-id="43bf9-1113">Run the [EnableDumps PowerShell script](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1):</span></span>
   * <span data-ttu-id="43bf9-1114">Pokud aplikace používá [model hostování v rámci procesu](xref:host-and-deploy/iis/index#in-process-hosting-model), spusťte skript pro *W3wp. exe*:</span><span class="sxs-lookup"><span data-stu-id="43bf9-1114">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\EnableDumps w3wp.exe c:\dumps
     ```

   * <span data-ttu-id="43bf9-1115">Pokud aplikace používá [model hostování mimo proces](xref:host-and-deploy/iis/index#out-of-process-hosting-model), spusťte skript pro příkaz *dotnet. exe*:</span><span class="sxs-lookup"><span data-stu-id="43bf9-1115">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\EnableDumps dotnet.exe c:\dumps
     ```

1. <span data-ttu-id="43bf9-1116">Spusťte aplikaci za podmínek, které způsobí, že dojde k chybě.</span><span class="sxs-lookup"><span data-stu-id="43bf9-1116">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="43bf9-1117">Po chybě spusťte [skript PowerShellu DisableDumps](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1):</span><span class="sxs-lookup"><span data-stu-id="43bf9-1117">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1):</span></span>
   * <span data-ttu-id="43bf9-1118">Pokud aplikace používá [model hostování v rámci procesu](xref:host-and-deploy/iis/index#in-process-hosting-model), spusťte skript pro *W3wp. exe*:</span><span class="sxs-lookup"><span data-stu-id="43bf9-1118">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\DisableDumps w3wp.exe
     ```

   * <span data-ttu-id="43bf9-1119">Pokud aplikace používá [model hostování mimo proces](xref:host-and-deploy/iis/index#out-of-process-hosting-model), spusťte skript pro příkaz *dotnet. exe*:</span><span class="sxs-lookup"><span data-stu-id="43bf9-1119">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\DisableDumps dotnet.exe
     ```

<span data-ttu-id="43bf9-1120">Po selhání aplikace a dokončení shromažďování výpisu je možné aplikaci ukončit normálně.</span><span class="sxs-lookup"><span data-stu-id="43bf9-1120">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="43bf9-1121">Skript PowerShellu nakonfiguruje WER a shromáždí až pět výpisů paměti pro každou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="43bf9-1121">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="43bf9-1122">Výpisy stavu systému můžou zabírat velké množství místa na disku (každý až několik gigabajtů).</span><span class="sxs-lookup"><span data-stu-id="43bf9-1122">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="43bf9-1123">Aplikace přestane reagovat, dojde k chybě při spuštění nebo se spustí normálně.</span><span class="sxs-lookup"><span data-stu-id="43bf9-1123">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="43bf9-1124">Když *aplikace přestane reagovat (zastaví* se, ale nejedná se o chybu), selže během spuštění nebo se spustí normálně, podívejte [se na soubory výpisu paměti v uživatelském režimu: zvolením nejlepšího](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) nástroje vyberte vhodný nástroj pro vytváření výpisu.</span><span class="sxs-lookup"><span data-stu-id="43bf9-1124">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="43bf9-1125">Analýza výpisu paměti</span><span class="sxs-lookup"><span data-stu-id="43bf9-1125">Analyze the dump</span></span>

<span data-ttu-id="43bf9-1126">Výpis paměti lze analyzovat pomocí několika přístupů.</span><span class="sxs-lookup"><span data-stu-id="43bf9-1126">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="43bf9-1127">Další informace najdete v tématu [Analýza souboru s výpisem stavu v uživatelském režimu](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span><span class="sxs-lookup"><span data-stu-id="43bf9-1127">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="clear-package-caches"></a><span data-ttu-id="43bf9-1128">Vymazat mezipaměti balíčků</span><span class="sxs-lookup"><span data-stu-id="43bf9-1128">Clear package caches</span></span>

<span data-ttu-id="43bf9-1129">Funkční aplikace může po upgradu .NET Core SDK ve vývojovém počítači nebo změně verzí balíčku v rámci aplikace selhat okamžitě.</span><span class="sxs-lookup"><span data-stu-id="43bf9-1129">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="43bf9-1130">V některých případech osamocené balíčky mohou narušit funkce aplikace při provádění hlavní upgrady.</span><span class="sxs-lookup"><span data-stu-id="43bf9-1130">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="43bf9-1131">Většina těchto problémů můžete opravit podle těchto pokynů:</span><span class="sxs-lookup"><span data-stu-id="43bf9-1131">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="43bf9-1132">Odstraňte složky *bin* a *obj* .</span><span class="sxs-lookup"><span data-stu-id="43bf9-1132">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="43bf9-1133">Pomocí příkazu [dotnet All--Clear](/dotnet/core/tools/dotnet-nuget-locals) z příkazového prostředí vymažte mezipaměť balíčku.</span><span class="sxs-lookup"><span data-stu-id="43bf9-1133">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="43bf9-1134">Mazání mezipamětí balíčků lze také provést pomocí nástroje [NuGet. exe](https://www.nuget.org/downloads) a spuštěním příkazu `nuget locals all -clear`.</span><span class="sxs-lookup"><span data-stu-id="43bf9-1134">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="43bf9-1135">*NuGet. exe* není sada instalovaná instalace s desktopovým operačním systémem Windows a musí se získat samostatně z [webu NuGet](https://www.nuget.org/downloads).</span><span class="sxs-lookup"><span data-stu-id="43bf9-1135">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="43bf9-1136">Obnovit a znovu sestavte projekt.</span><span class="sxs-lookup"><span data-stu-id="43bf9-1136">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="43bf9-1137">Před opětovným nasazením aplikace odstraňte všechny soubory ve složce pro nasazení na serveru.</span><span class="sxs-lookup"><span data-stu-id="43bf9-1137">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="43bf9-1138">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="43bf9-1138">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:fundamentals/error-handling>
* <xref:host-and-deploy/aspnet-core-module>

### <a name="azure-documentation"></a><span data-ttu-id="43bf9-1139">Dokumentace k Azure</span><span class="sxs-lookup"><span data-stu-id="43bf9-1139">Azure documentation</span></span>

* [<span data-ttu-id="43bf9-1140">Application Insights pro ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="43bf9-1140">Application Insights for ASP.NET Core</span></span>](/azure/application-insights/app-insights-asp-net-core)
* [<span data-ttu-id="43bf9-1141">Část vzdálené ladění webových aplikací řešení potíží s webovou aplikací v Azure App Service pomocí sady Visual Studio</span><span class="sxs-lookup"><span data-stu-id="43bf9-1141">Remote debugging web apps section of Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug)
* [<span data-ttu-id="43bf9-1142">Přehled diagnostiky Azure App Service</span><span class="sxs-lookup"><span data-stu-id="43bf9-1142">Azure App Service diagnostics overview</span></span>](/azure/app-service/app-service-diagnostics)
* [<span data-ttu-id="43bf9-1143">Postup: Monitorování aplikací ve službě Azure App Service</span><span class="sxs-lookup"><span data-stu-id="43bf9-1143">How to: Monitor Apps in Azure App Service</span></span>](/azure/app-service/web-sites-monitor)
* [<span data-ttu-id="43bf9-1144">Řešení potíží s webovou aplikací v Azure App Service pomocí sady Visual Studio</span><span class="sxs-lookup"><span data-stu-id="43bf9-1144">Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [<span data-ttu-id="43bf9-1145">Řešení chyb HTTP z "502 špatné brány" a "nedostupné služby 503" v Azure Web Apps</span><span class="sxs-lookup"><span data-stu-id="43bf9-1145">Troubleshoot HTTP errors of "502 bad gateway" and "503 service unavailable" in your Azure web apps</span></span>](/azure/app-service/app-service-web-troubleshoot-http-502-http-503)
* [<span data-ttu-id="43bf9-1146">Řešení potíží s výkonem pomalých webových aplikací ve službě Azure App Service</span><span class="sxs-lookup"><span data-stu-id="43bf9-1146">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="43bf9-1147">Nejčastější dotazy týkající se výkonu aplikací pro Web Apps v Azure</span><span class="sxs-lookup"><span data-stu-id="43bf9-1147">Application performance FAQs for Web Apps in Azure</span></span>](/azure/app-service/app-service-web-availability-performance-application-issues-faq)
* [<span data-ttu-id="43bf9-1148">Sandbox webové aplikace Azure (omezení spuštění App Service Runtime)</span><span class="sxs-lookup"><span data-stu-id="43bf9-1148">Azure Web App sandbox (App Service runtime execution limitations)</span></span>](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)
* [<span data-ttu-id="43bf9-1149">Azure pátek: Azure App Service diagnostické prostředí a řešení potíží (12 minut video)</span><span class="sxs-lookup"><span data-stu-id="43bf9-1149">Azure Friday: Azure App Service Diagnostic and Troubleshooting Experience (12-minute video)</span></span>](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)

### <a name="visual-studio-documentation"></a><span data-ttu-id="43bf9-1150">Dokumentace sady Visual Studio</span><span class="sxs-lookup"><span data-stu-id="43bf9-1150">Visual Studio documentation</span></span>

* [<span data-ttu-id="43bf9-1151">Vzdálené ladění ASP.NET Core ve službě IIS v Azure v aplikaci Visual Studio 2017</span><span class="sxs-lookup"><span data-stu-id="43bf9-1151">Remote Debug ASP.NET Core on IIS in Azure in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-azure)
* [<span data-ttu-id="43bf9-1152">Vzdálené ladění ASP.NET Core na vzdáleném počítači IIS v aplikaci Visual Studio 2017</span><span class="sxs-lookup"><span data-stu-id="43bf9-1152">Remote Debug ASP.NET Core on a Remote IIS Computer in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-aspnet-on-a-remote-iis-computer)
* [<span data-ttu-id="43bf9-1153">Další informace k ladění pomocí sady Visual Studio</span><span class="sxs-lookup"><span data-stu-id="43bf9-1153">Learn to debug using Visual Studio</span></span>](/visualstudio/debugger/getting-started-with-the-debugger)

### <a name="visual-studio-code-documentation"></a><span data-ttu-id="43bf9-1154">Dokumentace k Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="43bf9-1154">Visual Studio Code documentation</span></span>

* [<span data-ttu-id="43bf9-1155">Ladění pomocí Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="43bf9-1155">Debugging with Visual Studio Code</span></span>](https://code.visualstudio.com/docs/editor/debugging)

::: moniker-end
