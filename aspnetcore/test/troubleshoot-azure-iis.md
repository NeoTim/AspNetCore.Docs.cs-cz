---
title: Řešení potíží s ASP.NET Core v Azure App Service a IIS
author: rick-anderson
description: Naučte se diagnostikovat problémy s Azure App Service a Internetová informační služba nasazení ASP.NET Core aplikací (IIS).
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: test/troubleshoot-azure-iis
ms.openlocfilehash: 09b004abd423abc9cc8e83d3bb3fea1dddf09e14
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776627"
---
# <a name="troubleshoot-aspnet-core-on-azure-app-service-and-iis"></a><span data-ttu-id="7634a-103">Řešení potíží s ASP.NET Core v Azure App Service a IIS</span><span class="sxs-lookup"><span data-stu-id="7634a-103">Troubleshoot ASP.NET Core on Azure App Service and IIS</span></span>

<span data-ttu-id="7634a-104">Od [Justin Kotalik](https://github.com/jkotalik)</span><span class="sxs-lookup"><span data-stu-id="7634a-104">By [Justin Kotalik](https://github.com/jkotalik)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="7634a-105">Tento článek poskytuje informace o běžných chybách při spuštění aplikací a pokyny k diagnostice chyb při nasazení aplikace do Azure App Service nebo IIS:</span><span class="sxs-lookup"><span data-stu-id="7634a-105">This article provides information on common app startup errors and instructions on how to diagnose errors when an app is deployed to Azure App Service or IIS:</span></span>

[<span data-ttu-id="7634a-106">Chyby při spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="7634a-106">App startup errors</span></span>](#app-startup-errors)  
<span data-ttu-id="7634a-107">Vysvětluje běžné spouštění scénářů stavového kódu HTTP.</span><span class="sxs-lookup"><span data-stu-id="7634a-107">Explains common startup HTTP status code scenarios.</span></span>

[<span data-ttu-id="7634a-108">Řešení potíží na Azure App Service</span><span class="sxs-lookup"><span data-stu-id="7634a-108">Troubleshoot on Azure App Service</span></span>](#troubleshoot-on-azure-app-service)  
<span data-ttu-id="7634a-109">Poskytuje rady pro řešení potíží pro aplikace nasazené do Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="7634a-109">Provides troubleshooting advice for apps deployed to Azure App Service.</span></span>

[<span data-ttu-id="7634a-110">Odstraňování potíží ve službě IIS</span><span class="sxs-lookup"><span data-stu-id="7634a-110">Troubleshoot on IIS</span></span>](#troubleshoot-on-iis)  
<span data-ttu-id="7634a-111">Poskytuje rady pro řešení potíží pro aplikace nasazené do služby IIS nebo místně spuštěné v IIS Express.</span><span class="sxs-lookup"><span data-stu-id="7634a-111">Provides troubleshooting advice for apps deployed to IIS or running on IIS Express locally.</span></span> <span data-ttu-id="7634a-112">Pokyny platí pro nasazení systémů Windows Server a Desktop v systému Windows.</span><span class="sxs-lookup"><span data-stu-id="7634a-112">The guidance applies to both Windows Server and Windows desktop deployments.</span></span>

[<span data-ttu-id="7634a-113">Vymazat mezipaměti balíčků</span><span class="sxs-lookup"><span data-stu-id="7634a-113">Clear package caches</span></span>](#clear-package-caches)  
<span data-ttu-id="7634a-114">Vysvětluje, co dělat v případě, že při provádění podstatných upgradů nebo změně verze balíčku dojde k přerušení aplikace v nesouvislých balíčcích.</span><span class="sxs-lookup"><span data-stu-id="7634a-114">Explains what to do when incoherent packages break an app when performing major upgrades or changing package versions.</span></span>

[<span data-ttu-id="7634a-115">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="7634a-115">Additional resources</span></span>](#additional-resources)  
<span data-ttu-id="7634a-116">Seznam dalších témat pro odstraňování potíží.</span><span class="sxs-lookup"><span data-stu-id="7634a-116">Lists additional troubleshooting topics.</span></span>

## <a name="app-startup-errors"></a><span data-ttu-id="7634a-117">Chyby při spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="7634a-117">App startup errors</span></span>

<span data-ttu-id="7634a-118">V aplikaci Visual Studio je výchozím nastavením projektu ASP.NET Core [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hostování během ladění.</span><span class="sxs-lookup"><span data-stu-id="7634a-118">In Visual Studio, an ASP.NET Core project defaults to [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hosting during debugging.</span></span> <span data-ttu-id="7634a-119">*502,5 – selhání procesu* nebo *500,30 – chyba* , ke které dojde při místním ladění, se dá diagnostikovat pomocí Rady v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="7634a-119">A *502.5 - Process Failure* or a *500.30 - Start Failure* that occurs when debugging locally can be diagnosed using the advice in this topic.</span></span>

### <a name="40314-forbidden"></a><span data-ttu-id="7634a-120">403,14 zakázané</span><span class="sxs-lookup"><span data-stu-id="7634a-120">403.14 Forbidden</span></span>

<span data-ttu-id="7634a-121">Aplikaci se nepodařilo spustit.</span><span class="sxs-lookup"><span data-stu-id="7634a-121">The app fails to start.</span></span> <span data-ttu-id="7634a-122">Protokoluje se následující chyba:</span><span class="sxs-lookup"><span data-stu-id="7634a-122">The following error is logged:</span></span>

```
The Web server is configured to not list the contents of this directory.
```

<span data-ttu-id="7634a-123">K této chybě obvykle dochází v důsledku poškozeného nasazení v hostitelském systému, který zahrnuje kterýkoli z následujících scénářů:</span><span class="sxs-lookup"><span data-stu-id="7634a-123">The error is usually caused by a broken deployment on the hosting system, which includes any of the following scenarios:</span></span>

* <span data-ttu-id="7634a-124">Aplikace je nasazená do chybné složky v hostitelském systému.</span><span class="sxs-lookup"><span data-stu-id="7634a-124">The app is deployed to the wrong folder on the hosting system.</span></span>
* <span data-ttu-id="7634a-125">Procesu nasazení se nepodařilo přesunout všechny soubory a složky aplikace do složky pro nasazení v hostitelském systému.</span><span class="sxs-lookup"><span data-stu-id="7634a-125">The deployment process failed to move all of the app's files and folders to the deployment folder on the hosting system.</span></span>
* <span data-ttu-id="7634a-126">V nasazení chybí soubor *Web. config* , nebo je obsah souboru *Web. config* poškozený.</span><span class="sxs-lookup"><span data-stu-id="7634a-126">The *web.config* file is missing from the deployment, or the *web.config* file contents are malformed.</span></span>

<span data-ttu-id="7634a-127">Proveďte následující kroky:</span><span class="sxs-lookup"><span data-stu-id="7634a-127">Perform the following steps:</span></span>

1. <span data-ttu-id="7634a-128">Odstraňte všechny soubory a složky ze složky pro nasazení v hostitelském systému.</span><span class="sxs-lookup"><span data-stu-id="7634a-128">Delete all of the files and folders from the deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="7634a-129">Znovu nasaďte obsah složky pro *publikování* aplikace do hostitelského systému pomocí běžné metody nasazení, jako je například Visual Studio, PowerShell nebo ruční nasazení:</span><span class="sxs-lookup"><span data-stu-id="7634a-129">Redeploy the contents of the app's *publish* folder to the hosting system using your normal method of deployment, such as Visual Studio, PowerShell, or manual deployment:</span></span>
   * <span data-ttu-id="7634a-130">Zkontrolujte, zda je v nasazení přítomen soubor *Web. config* a zda jeho obsah je správný.</span><span class="sxs-lookup"><span data-stu-id="7634a-130">Confirm that the *web.config* file is present in the deployment and that its contents are correct.</span></span>
   * <span data-ttu-id="7634a-131">Při hostování na Azure App Service potvrďte, že je aplikace nasazená do `D:\home\site\wwwroot` složky.</span><span class="sxs-lookup"><span data-stu-id="7634a-131">When hosting on Azure App Service, confirm that the app is deployed to the `D:\home\site\wwwroot` folder.</span></span>
   * <span data-ttu-id="7634a-132">Když je aplikace hostovaná službou IIS, zkontrolujte, že je aplikace nasazená na **fyzickou cestu** služby IIS, která se zobrazuje v **základním nastavení** **Správce služby IIS**.</span><span class="sxs-lookup"><span data-stu-id="7634a-132">When the app is hosted by IIS, confirm that the app is deployed to the IIS **Physical path** shown in **IIS Manager**'s **Basic Settings**.</span></span>
1. <span data-ttu-id="7634a-133">Ověřte, zda jsou všechny soubory a složky aplikace nasazeny porovnáním nasazení v hostitelském systému s obsahem složky *publikování* projektu.</span><span class="sxs-lookup"><span data-stu-id="7634a-133">Confirm that all of the app's files and folders are deployed by comparing the deployment on the hosting system to the contents of the project's *publish* folder.</span></span>

<span data-ttu-id="7634a-134">Další informace o rozložení publikované aplikace ASP.NET Core najdete v tématu <xref:host-and-deploy/directory-structure>.</span><span class="sxs-lookup"><span data-stu-id="7634a-134">For more information on the layout of a published ASP.NET Core app, see <xref:host-and-deploy/directory-structure>.</span></span> <span data-ttu-id="7634a-135">Další informace o souboru *Web. config* naleznete v tématu <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>.</span><span class="sxs-lookup"><span data-stu-id="7634a-135">For more information on the *web.config* file, see <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>.</span></span>

### <a name="500-internal-server-error"></a><span data-ttu-id="7634a-136">500 – Interní chyba serveru</span><span class="sxs-lookup"><span data-stu-id="7634a-136">500 Internal Server Error</span></span>

<span data-ttu-id="7634a-137">Aplikace se spustí, ale chyba brání serveru v splnění žádosti.</span><span class="sxs-lookup"><span data-stu-id="7634a-137">The app starts, but an error prevents the server from fulfilling the request.</span></span>

<span data-ttu-id="7634a-138">K této chybě dochází v kódu aplikace během spuštění nebo při vytváření odpovědi.</span><span class="sxs-lookup"><span data-stu-id="7634a-138">This error occurs within the app's code during startup or while creating a response.</span></span> <span data-ttu-id="7634a-139">Odpověď možná neobsahuje žádný obsah, nebo se odpověď může zobrazit jako *500 interní chyba serveru* v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="7634a-139">The response may contain no content, or the response may appear as a *500 Internal Server Error* in the browser.</span></span> <span data-ttu-id="7634a-140">Protokol událostí aplikace obvykle uvádí, že aplikace začala normálně.</span><span class="sxs-lookup"><span data-stu-id="7634a-140">The Application Event Log usually states that the app started normally.</span></span> <span data-ttu-id="7634a-141">Z hlediska serveru je to správné.</span><span class="sxs-lookup"><span data-stu-id="7634a-141">From the server's perspective, that's correct.</span></span> <span data-ttu-id="7634a-142">Aplikace se spustila, ale nemůže vygenerovat platnou odpověď.</span><span class="sxs-lookup"><span data-stu-id="7634a-142">The app did start, but it can't generate a valid response.</span></span> <span data-ttu-id="7634a-143">Spusťte aplikaci na příkazovém řádku na serveru nebo povolte řešení potíží pomocí protokolu stdout modulu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7634a-143">Run the app at a command prompt on the server or enable the ASP.NET Core Module stdout log to troubleshoot the problem.</span></span>

### <a name="5000-in-process-handler-load-failure"></a><span data-ttu-id="7634a-144">500,0 selhání načtení obslužné rutiny v procesu</span><span class="sxs-lookup"><span data-stu-id="7634a-144">500.0 In-Process Handler Load Failure</span></span>

<span data-ttu-id="7634a-145">Pracovní proces se nezdařil.</span><span class="sxs-lookup"><span data-stu-id="7634a-145">The worker process fails.</span></span> <span data-ttu-id="7634a-146">Aplikace se nespustí.</span><span class="sxs-lookup"><span data-stu-id="7634a-146">The app doesn't start.</span></span>

<span data-ttu-id="7634a-147">Při načítání komponent [modulu ASP.NET Core](xref:host-and-deploy/aspnet-core-module) došlo k neznámé chybě.</span><span class="sxs-lookup"><span data-stu-id="7634a-147">An unknown error occurred loading [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) components.</span></span> <span data-ttu-id="7634a-148">Proveďte jednu z následujících akcí:</span><span class="sxs-lookup"><span data-stu-id="7634a-148">Take one of the following actions:</span></span>

* <span data-ttu-id="7634a-149">Kontaktujte [Podpora Microsoftu](https://support.microsoft.com/oas/default.aspx?prid=15832) (vyberte **vývojářské nástroje** potom **ASP.NET Core**).</span><span class="sxs-lookup"><span data-stu-id="7634a-149">Contact [Microsoft Support](https://support.microsoft.com/oas/default.aspx?prid=15832) (select **Developer Tools** then **ASP.NET Core**).</span></span>
* <span data-ttu-id="7634a-150">Položte otázku na Stack Overflow.</span><span class="sxs-lookup"><span data-stu-id="7634a-150">Ask a question on Stack Overflow.</span></span>
* <span data-ttu-id="7634a-151">Vydejte problém do našeho [úložiště GitHub](https://github.com/dotnet/AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="7634a-151">File an issue on our [GitHub repository](https://github.com/dotnet/AspNetCore).</span></span>

### <a name="50030-in-process-startup-failure"></a><span data-ttu-id="7634a-152">500,30 selhání při spuštění v procesu</span><span class="sxs-lookup"><span data-stu-id="7634a-152">500.30 In-Process Startup Failure</span></span>

<span data-ttu-id="7634a-153">Pracovní proces se nezdařil.</span><span class="sxs-lookup"><span data-stu-id="7634a-153">The worker process fails.</span></span> <span data-ttu-id="7634a-154">Aplikace se nespustí.</span><span class="sxs-lookup"><span data-stu-id="7634a-154">The app doesn't start.</span></span>

<span data-ttu-id="7634a-155">[Modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module) se pokusí spustit modul CLR .NET Core v procesu, ale jeho spuštění se nezdařilo.</span><span class="sxs-lookup"><span data-stu-id="7634a-155">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the .NET Core CLR in-process, but it fails to start.</span></span> <span data-ttu-id="7634a-156">Příčinou selhání spuštění procesu může být obvykle určení z položek protokolu událostí aplikace a protokolu stdout modulu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7634a-156">The cause of a process startup failure can usually be determined from entries in the Application Event Log and the ASP.NET Core Module stdout log.</span></span>

<span data-ttu-id="7634a-157">Běžné podmínky selhání:</span><span class="sxs-lookup"><span data-stu-id="7634a-157">Common failure conditions:</span></span>

* <span data-ttu-id="7634a-158">Aplikace je nesprávně nakonfigurovaná kvůli cílení na verzi ASP.NET Core sdílené rozhraní, které není k dispozici.</span><span class="sxs-lookup"><span data-stu-id="7634a-158">The app is misconfigured due to targeting a version of the ASP.NET Core shared framework that isn't present.</span></span> <span data-ttu-id="7634a-159">Ověřte, které verze ASP.NET Core sdílené rozhraní jsou nainstalovány v cílovém počítači.</span><span class="sxs-lookup"><span data-stu-id="7634a-159">Check which versions of the ASP.NET Core shared framework are installed on the target machine.</span></span>
* <span data-ttu-id="7634a-160">Při použití Azure Key Vault chybí oprávnění Key Vault.</span><span class="sxs-lookup"><span data-stu-id="7634a-160">Using Azure Key Vault, lack of permissions to the Key Vault.</span></span> <span data-ttu-id="7634a-161">Zkontrolujte zásady přístupu v cílovém Key Vault, abyste měli jistotu, že jsou udělená správná oprávnění.</span><span class="sxs-lookup"><span data-stu-id="7634a-161">Check the access policies in the targeted Key Vault to ensure that the correct permissions are granted.</span></span>

### <a name="50031-ancm-failed-to-find-native-dependencies"></a><span data-ttu-id="7634a-162">500,31 ANCM nepovedlo se najít nativní závislosti.</span><span class="sxs-lookup"><span data-stu-id="7634a-162">500.31 ANCM Failed to Find Native Dependencies</span></span>

<span data-ttu-id="7634a-163">Pracovní proces se nezdařil.</span><span class="sxs-lookup"><span data-stu-id="7634a-163">The worker process fails.</span></span> <span data-ttu-id="7634a-164">Aplikace se nespustí.</span><span class="sxs-lookup"><span data-stu-id="7634a-164">The app doesn't start.</span></span>

<span data-ttu-id="7634a-165">[Modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module) se pokusí spustit modul runtime .NET Core v procesu, ale jeho spuštění se nezdařilo.</span><span class="sxs-lookup"><span data-stu-id="7634a-165">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the .NET Core runtime in-process, but it fails to start.</span></span> <span data-ttu-id="7634a-166">Nejběžnější příčinou tohoto selhání při spuštění je, že `Microsoft.NETCore.App` modul runtime nebo `Microsoft.AspNetCore.App` není nainstalován.</span><span class="sxs-lookup"><span data-stu-id="7634a-166">The most common cause of this startup failure is when the `Microsoft.NETCore.App` or `Microsoft.AspNetCore.App` runtime isn't installed.</span></span> <span data-ttu-id="7634a-167">Pokud je aplikace nasazená na cílovou ASP.NET Core 3,0 a tato verze v počítači neexistuje, dojde k této chybě.</span><span class="sxs-lookup"><span data-stu-id="7634a-167">If the app is deployed to target ASP.NET Core 3.0 and that version doesn't exist on the machine, this error occurs.</span></span> <span data-ttu-id="7634a-168">Následuje příklad chybové zprávy:</span><span class="sxs-lookup"><span data-stu-id="7634a-168">An example error message follows:</span></span>

```
The specified framework 'Microsoft.NETCore.App', version '3.0.0' was not found.
  - The following frameworks were found:
      2.2.1 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview5-27626-15 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27713-13 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27714-15 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27723-08 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
```

<span data-ttu-id="7634a-169">Chybová zpráva obsahuje seznam všech nainstalovaných verzí rozhraní .NET Core a verze, kterou aplikace požaduje.</span><span class="sxs-lookup"><span data-stu-id="7634a-169">The error message lists all the installed .NET Core versions and the version requested by the app.</span></span> <span data-ttu-id="7634a-170">Chcete-li tuto chybu vyřešit, postupujte takto:</span><span class="sxs-lookup"><span data-stu-id="7634a-170">To fix this error, either:</span></span>

* <span data-ttu-id="7634a-171">Na počítač nainstalujte odpovídající verzi .NET Core.</span><span class="sxs-lookup"><span data-stu-id="7634a-171">Install the appropriate version of .NET Core on the machine.</span></span>
* <span data-ttu-id="7634a-172">Změňte aplikaci na cílovou verzi .NET Core, která je na počítači přítomná.</span><span class="sxs-lookup"><span data-stu-id="7634a-172">Change the app to target a version of .NET Core that's present on the machine.</span></span>
* <span data-ttu-id="7634a-173">Publikujte aplikaci jako samostatné [nasazení](/dotnet/core/deploying/#self-contained-deployments-scd).</span><span class="sxs-lookup"><span data-stu-id="7634a-173">Publish the app as a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>

<span data-ttu-id="7634a-174">Při spuštění ve vývoji (proměnná `ASPNETCORE_ENVIRONMENT` prostředí je nastavená na `Development`hodnotu) se konkrétní chyba zapíše do odpovědi HTTP.</span><span class="sxs-lookup"><span data-stu-id="7634a-174">When running in development (the `ASPNETCORE_ENVIRONMENT` environment variable is set to `Development`), the specific error is written to the HTTP response.</span></span> <span data-ttu-id="7634a-175">Příčinu selhání spuštění procesu se nachází také v protokolu událostí aplikace.</span><span class="sxs-lookup"><span data-stu-id="7634a-175">The cause of a process startup failure is also found in the Application Event Log.</span></span>

### <a name="50032-ancm-failed-to-load-dll"></a><span data-ttu-id="7634a-176">500,32 ANCM načtení knihovny DLL se nezdařilo</span><span class="sxs-lookup"><span data-stu-id="7634a-176">500.32 ANCM Failed to Load dll</span></span>

<span data-ttu-id="7634a-177">Pracovní proces se nezdařil.</span><span class="sxs-lookup"><span data-stu-id="7634a-177">The worker process fails.</span></span> <span data-ttu-id="7634a-178">Aplikace se nespustí.</span><span class="sxs-lookup"><span data-stu-id="7634a-178">The app doesn't start.</span></span>

<span data-ttu-id="7634a-179">Nejběžnější příčinou této chyby je, že aplikace je publikovaná pro nekompatibilní architekturu procesoru.</span><span class="sxs-lookup"><span data-stu-id="7634a-179">The most common cause for this error is that the app is published for an incompatible processor architecture.</span></span> <span data-ttu-id="7634a-180">Pokud je pracovní proces spuštěn jako 32 aplikace a aplikace byla publikována na cílovou 64-bit, dojde k této chybě.</span><span class="sxs-lookup"><span data-stu-id="7634a-180">If the worker process is running as a 32-bit app and the app was published to target 64-bit, this error occurs.</span></span>

<span data-ttu-id="7634a-181">Chcete-li tuto chybu vyřešit, postupujte takto:</span><span class="sxs-lookup"><span data-stu-id="7634a-181">To fix this error, either:</span></span>

* <span data-ttu-id="7634a-182">Znovu publikujte aplikaci pro stejnou architekturu procesoru jako pracovní proces.</span><span class="sxs-lookup"><span data-stu-id="7634a-182">Republish the app for the same processor architecture as the worker process.</span></span>
* <span data-ttu-id="7634a-183">Publikování aplikace jako [nasazení závislého na rozhraní](/dotnet/core/deploying/#framework-dependent-executables-fde).</span><span class="sxs-lookup"><span data-stu-id="7634a-183">Publish the app as a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-executables-fde).</span></span>

### <a name="50033-ancm-request-handler-load-failure"></a><span data-ttu-id="7634a-184">500,33 selhání načtení obslužné rutiny žádosti ANCM</span><span class="sxs-lookup"><span data-stu-id="7634a-184">500.33 ANCM Request Handler Load Failure</span></span>

<span data-ttu-id="7634a-185">Pracovní proces se nezdařil.</span><span class="sxs-lookup"><span data-stu-id="7634a-185">The worker process fails.</span></span> <span data-ttu-id="7634a-186">Aplikace se nespustí.</span><span class="sxs-lookup"><span data-stu-id="7634a-186">The app doesn't start.</span></span>

<span data-ttu-id="7634a-187">Aplikace neodkazovala na `Microsoft.AspNetCore.App` rozhraní.</span><span class="sxs-lookup"><span data-stu-id="7634a-187">The app didn't reference the `Microsoft.AspNetCore.App` framework.</span></span> <span data-ttu-id="7634a-188">[Modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module)může hostovat `Microsoft.AspNetCore.App` jenom aplikace cílené na rozhraní.</span><span class="sxs-lookup"><span data-stu-id="7634a-188">Only apps targeting the `Microsoft.AspNetCore.App` framework can be hosted by the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span>

<span data-ttu-id="7634a-189">Chcete-li tuto chybu opravit, zkontrolujte, zda je aplikace `Microsoft.AspNetCore.App` cílena na rozhraní.</span><span class="sxs-lookup"><span data-stu-id="7634a-189">To fix this error, confirm that the app is targeting the `Microsoft.AspNetCore.App` framework.</span></span> <span data-ttu-id="7634a-190">Zkontrolujte, `.runtimeconfig.json` jestli chcete ověřit architekturu, na kterou aplikace cílí.</span><span class="sxs-lookup"><span data-stu-id="7634a-190">Check the `.runtimeconfig.json` to verify the framework targeted by the app.</span></span>

### <a name="50034-ancm-mixed-hosting-models-not-supported"></a><span data-ttu-id="7634a-191">500,34 ANCM smíšené modely hostování se nepodporují.</span><span class="sxs-lookup"><span data-stu-id="7634a-191">500.34 ANCM Mixed Hosting Models Not Supported</span></span>

<span data-ttu-id="7634a-192">Pracovní proces nemůže v jednom procesu spustit jak aplikaci v rámci procesu, tak i aplikaci mimo proces.</span><span class="sxs-lookup"><span data-stu-id="7634a-192">The worker process can't run both an in-process app and an out-of-process app in the same process.</span></span>

<span data-ttu-id="7634a-193">Chcete-li tuto chybu opravit, spusťte aplikace v samostatných fondech aplikací služby IIS.</span><span class="sxs-lookup"><span data-stu-id="7634a-193">To fix this error, run apps in separate IIS application pools.</span></span>

### <a name="50035-ancm-multiple-in-process-applications-in-same-process"></a><span data-ttu-id="7634a-194">500,35 ANCM více vnitroprocesové aplikací ve stejném procesu</span><span class="sxs-lookup"><span data-stu-id="7634a-194">500.35 ANCM Multiple In-Process Applications in same Process</span></span>

<span data-ttu-id="7634a-195">Pracovní proces nemůže v jednom procesu spustit více vnitroprocesové aplikací.</span><span class="sxs-lookup"><span data-stu-id="7634a-195">The worker process can't run multiple in-process apps in the same process.</span></span>

<span data-ttu-id="7634a-196">Chcete-li tuto chybu opravit, spusťte aplikace v samostatných fondech aplikací služby IIS.</span><span class="sxs-lookup"><span data-stu-id="7634a-196">To fix this error, run apps in separate IIS application pools.</span></span>

### <a name="50036-ancm-out-of-process-handler-load-failure"></a><span data-ttu-id="7634a-197">500,36 ANCM selhání načtení obslužné rutiny mimo proces</span><span class="sxs-lookup"><span data-stu-id="7634a-197">500.36 ANCM Out-Of-Process Handler Load Failure</span></span>

<span data-ttu-id="7634a-198">Obslužná rutina žádosti mimo proces, *aspnetcorev2_outofprocess. dll*, není vedle souboru *aspnetcorev2. dll* .</span><span class="sxs-lookup"><span data-stu-id="7634a-198">The out-of-process request handler, *aspnetcorev2_outofprocess.dll*, isn't next to the *aspnetcorev2.dll* file.</span></span> <span data-ttu-id="7634a-199">To označuje poškozenou instalaci [modulu ASP.NET Core](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="7634a-199">This indicates a corrupted installation of the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span>

<span data-ttu-id="7634a-200">Chcete-li tuto chybu opravit, opravte instalaci [hostující sady .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) (pro IIS) nebo Visual Studio (pro IIS Express).</span><span class="sxs-lookup"><span data-stu-id="7634a-200">To fix this error, repair the installation of the [.NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) (for IIS) or Visual Studio (for IIS Express).</span></span>

### <a name="50037-ancm-failed-to-start-within-startup-time-limit"></a><span data-ttu-id="7634a-201">500,37 ANCM se nepodařilo spustit v časovém limitu spuštění.</span><span class="sxs-lookup"><span data-stu-id="7634a-201">500.37 ANCM Failed to Start Within Startup Time Limit</span></span>

<span data-ttu-id="7634a-202">ANCM se nepovedlo spustit v rámci časového limitu spuštění nechte.</span><span class="sxs-lookup"><span data-stu-id="7634a-202">ANCM failed to start within the provied startup time limit.</span></span> <span data-ttu-id="7634a-203">Ve výchozím nastavení je časový limit 120 sekund.</span><span class="sxs-lookup"><span data-stu-id="7634a-203">By default, the timeout is 120 seconds.</span></span>

<span data-ttu-id="7634a-204">K této chybě může dojít, když ve stejném počítači spustíte velký počet aplikací.</span><span class="sxs-lookup"><span data-stu-id="7634a-204">This error can occur when starting a large number of apps on the same machine.</span></span> <span data-ttu-id="7634a-205">Kontrolovat špičky využití procesoru a paměti na serveru při spuštění.</span><span class="sxs-lookup"><span data-stu-id="7634a-205">Check for CPU/Memory usage spikes on the server during startup.</span></span> <span data-ttu-id="7634a-206">Možná budete muset rozložit proces spouštění více aplikací.</span><span class="sxs-lookup"><span data-stu-id="7634a-206">You may need to stagger the startup process of multiple apps.</span></span>

### <a name="50038-ancm-application-dll-not-found"></a><span data-ttu-id="7634a-207">500,38 knihovna DLL aplikace ANCM nebyla nalezena.</span><span class="sxs-lookup"><span data-stu-id="7634a-207">500.38 ANCM Application DLL Not Found</span></span>

<span data-ttu-id="7634a-208">ANCM se nepodařilo najít knihovnu DLL aplikace, která by měla být vedle spustitelného souboru.</span><span class="sxs-lookup"><span data-stu-id="7634a-208">ANCM failed to locate the application DLL, which should be next to the executable.</span></span>

<span data-ttu-id="7634a-209">K této chybě dochází, když hostuje aplikaci zabalenou jako spustitelný soubor s [jedním souborem](/dotnet/core/whats-new/dotnet-core-3-0#single-file-executables) pomocí modelu hostování v rámci procesu.</span><span class="sxs-lookup"><span data-stu-id="7634a-209">This error occurs when hosting an app packaged as a [single-file executable](/dotnet/core/whats-new/dotnet-core-3-0#single-file-executables) using the in-process hosting model.</span></span> <span data-ttu-id="7634a-210">Model in-Process vyžaduje, aby ANCM do existujícího procesu IIS načetl aplikaci .NET Core.</span><span class="sxs-lookup"><span data-stu-id="7634a-210">The in-process model requires that the ANCM load the .NET Core app into the existing IIS process.</span></span> <span data-ttu-id="7634a-211">Tento scénář není podporován modelem nasazení s jedním souborem.</span><span class="sxs-lookup"><span data-stu-id="7634a-211">This scenario isn't supported by the single-file deployment model.</span></span> <span data-ttu-id="7634a-212">Tuto chybu můžete vyřešit pomocí **jednoho** z následujících přístupů v souboru projektu aplikace:</span><span class="sxs-lookup"><span data-stu-id="7634a-212">Use **one** of the following approaches in the app's project file to fix this error:</span></span>

1. <span data-ttu-id="7634a-213">Zakažte publikování jedním souborem nastavením vlastnosti `PublishSingleFile` MSBuild na. `false`</span><span class="sxs-lookup"><span data-stu-id="7634a-213">Disable single-file publishing by setting the `PublishSingleFile` MSBuild property to `false`.</span></span>
1. <span data-ttu-id="7634a-214">Přepněte do nezpracovaného modelu hostování nastavením vlastnosti `AspNetCoreHostingModel` MSBuild na. `OutOfProcess`</span><span class="sxs-lookup"><span data-stu-id="7634a-214">Switch to the out-of-process hosting model by setting the `AspNetCoreHostingModel` MSBuild property to `OutOfProcess`.</span></span>

### <a name="5025-process-failure"></a><span data-ttu-id="7634a-215">502.5 – Selhání procesu</span><span class="sxs-lookup"><span data-stu-id="7634a-215">502.5 Process Failure</span></span>

<span data-ttu-id="7634a-216">Pracovní proces se nezdařil.</span><span class="sxs-lookup"><span data-stu-id="7634a-216">The worker process fails.</span></span> <span data-ttu-id="7634a-217">Aplikace se nespustí.</span><span class="sxs-lookup"><span data-stu-id="7634a-217">The app doesn't start.</span></span>

<span data-ttu-id="7634a-218">[Modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module) se pokusí spustit pracovní proces, ale jeho spuštění se nezdařilo.</span><span class="sxs-lookup"><span data-stu-id="7634a-218">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the worker process but it fails to start.</span></span> <span data-ttu-id="7634a-219">Příčinou selhání spuštění procesu může být obvykle určení z položek protokolu událostí aplikace a protokolu stdout modulu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7634a-219">The cause of a process startup failure can usually be determined from entries in the Application Event Log and the ASP.NET Core Module stdout log.</span></span>

<span data-ttu-id="7634a-220">Obvyklou chybovou podmínkou je, že aplikace je nesprávně nakonfigurovaná kvůli cílení na verzi ASP.NET Core sdílené rozhraní, které není k dispozici.</span><span class="sxs-lookup"><span data-stu-id="7634a-220">A common failure condition is the app is misconfigured due to targeting a version of the ASP.NET Core shared framework that isn't present.</span></span> <span data-ttu-id="7634a-221">Ověřte, které verze ASP.NET Core sdílené rozhraní jsou nainstalovány v cílovém počítači.</span><span class="sxs-lookup"><span data-stu-id="7634a-221">Check which versions of the ASP.NET Core shared framework are installed on the target machine.</span></span> <span data-ttu-id="7634a-222">*Sdílené rozhraní* je sada sestavení (soubory *. dll* ), které jsou nainstalovány na počítači a na které odkazuje Metapackage jako `Microsoft.AspNetCore.App`.</span><span class="sxs-lookup"><span data-stu-id="7634a-222">The *shared framework* is the set of assemblies (*.dll* files) that are installed on the machine and referenced by a metapackage such as `Microsoft.AspNetCore.App`.</span></span> <span data-ttu-id="7634a-223">Odkaz Metapackage může určovat minimální požadovanou verzi.</span><span class="sxs-lookup"><span data-stu-id="7634a-223">The metapackage reference can specify a minimum required version.</span></span> <span data-ttu-id="7634a-224">Další informace najdete v tématu [sdílené rozhraní](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span><span class="sxs-lookup"><span data-stu-id="7634a-224">For more information, see [The shared framework](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span></span>

<span data-ttu-id="7634a-225">Chybová stránka *selhání procesu 502,5* se vrátí v případě, že při selhání hostování nebo aplikace dojde k chybě pracovního procesu:</span><span class="sxs-lookup"><span data-stu-id="7634a-225">The *502.5 Process Failure* error page is returned when a hosting or app misconfiguration causes the worker process to fail:</span></span>

### <a name="failed-to-start-application-errorcode-0x800700c1"></a><span data-ttu-id="7634a-226">Nepovedlo se spustit aplikaci (ErrorCode ' 0x800700C1 ')</span><span class="sxs-lookup"><span data-stu-id="7634a-226">Failed to start application (ErrorCode '0x800700c1')</span></span>

```
EventID: 1010
Source: IIS AspNetCore Module V2
Failed to start application '/LM/W3SVC/6/ROOT/', ErrorCode '0x800700c1'.
```

<span data-ttu-id="7634a-227">Aplikaci se nepovedlo spustit, protože se nepovedlo načíst sestavení aplikace (*. dll*).</span><span class="sxs-lookup"><span data-stu-id="7634a-227">The app failed to start because the app's assembly (*.dll*) couldn't be loaded.</span></span>

<span data-ttu-id="7634a-228">K této chybě dochází, pokud dojde k neshodě bitová verze mezi publikovanou aplikací a procesem W3wp/IISExpress.</span><span class="sxs-lookup"><span data-stu-id="7634a-228">This error occurs when there's a bitness mismatch between the published app and the w3wp/iisexpress process.</span></span>

<span data-ttu-id="7634a-229">Zkontrolujte, jestli je správně nastavené nastavení 32 fondu aplikací:</span><span class="sxs-lookup"><span data-stu-id="7634a-229">Confirm that the app pool's 32-bit setting is correct:</span></span>

1. <span data-ttu-id="7634a-230">Vyberte fond aplikací v **fondech aplikací**Správce služby IIS.</span><span class="sxs-lookup"><span data-stu-id="7634a-230">Select the app pool in IIS Manager's **Application Pools**.</span></span>
1. <span data-ttu-id="7634a-231">V části **Upravit fond aplikací** na panelu **Akce** vyberte **Upřesnit nastavení** .</span><span class="sxs-lookup"><span data-stu-id="7634a-231">Select **Advanced Settings** under **Edit Application Pool** in the **Actions** panel.</span></span>
1. <span data-ttu-id="7634a-232">Nastavte **možnost povolit 32 – bitové aplikace**:</span><span class="sxs-lookup"><span data-stu-id="7634a-232">Set **Enable 32-Bit Applications**:</span></span>
   * <span data-ttu-id="7634a-233">Pokud nasazujete 32 (x86) aplikaci, nastavte hodnotu na `True`.</span><span class="sxs-lookup"><span data-stu-id="7634a-233">If deploying a 32-bit (x86) app, set the value to `True`.</span></span>
   * <span data-ttu-id="7634a-234">Pokud nasazujete 64 aplikaci (x64), nastavte hodnotu na `False`.</span><span class="sxs-lookup"><span data-stu-id="7634a-234">If deploying a 64-bit (x64) app, set the value to `False`.</span></span>

<span data-ttu-id="7634a-235">Ověřte, že mezi vlastností `<Platform>` MSBuild v souboru projektu a publikovaným bitová verzeem aplikace nedochází ke konfliktu.</span><span class="sxs-lookup"><span data-stu-id="7634a-235">Confirm that there isn't a conflict between a `<Platform>` MSBuild property in the project file and the published bitness of the app.</span></span>

### <a name="connection-reset"></a><span data-ttu-id="7634a-236">Resetování připojení</span><span class="sxs-lookup"><span data-stu-id="7634a-236">Connection reset</span></span>

<span data-ttu-id="7634a-237">Pokud dojde k chybě po odeslání hlaviček, je příliš pozdě pro server, který odešle **500 interní chybu serveru** , když dojde k chybě.</span><span class="sxs-lookup"><span data-stu-id="7634a-237">If an error occurs after the headers are sent, it's too late for the server to send a **500 Internal Server Error** when an error occurs.</span></span> <span data-ttu-id="7634a-238">K tomu často dochází, když dojde k chybě během serializace složitých objektů pro odpověď.</span><span class="sxs-lookup"><span data-stu-id="7634a-238">This often happens when an error occurs during the serialization of complex objects for a response.</span></span> <span data-ttu-id="7634a-239">Tento typ chyby se zobrazí jako chyba *resetování připojení* na klientovi.</span><span class="sxs-lookup"><span data-stu-id="7634a-239">This type of error appears as a *connection reset* error on the client.</span></span> <span data-ttu-id="7634a-240">[Protokolování aplikace](xref:fundamentals/logging/index) může pomoct řešit tyto typy chyb.</span><span class="sxs-lookup"><span data-stu-id="7634a-240">[Application logging](xref:fundamentals/logging/index) can help troubleshoot these types of errors.</span></span>

### <a name="default-startup-limits"></a><span data-ttu-id="7634a-241">Výchozí omezení spouštění</span><span class="sxs-lookup"><span data-stu-id="7634a-241">Default startup limits</span></span>

<span data-ttu-id="7634a-242">[ASP.NET Core modul](xref:host-and-deploy/aspnet-core-module) je nakonfigurovaný s výchozí *startupTimeLimitou* 120 sekund.</span><span class="sxs-lookup"><span data-stu-id="7634a-242">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) is configured with a default *startupTimeLimit* of 120 seconds.</span></span> <span data-ttu-id="7634a-243">Pokud necháte výchozí hodnotu, aplikace může trvat až dvě minuty, než se modul zaznamená selhání procesu.</span><span class="sxs-lookup"><span data-stu-id="7634a-243">When left at the default value, an app may take up to two minutes to start before the module logs a process failure.</span></span> <span data-ttu-id="7634a-244">Informace o konfiguraci modulu naleznete v tématu [atributy elementu aspNetCore](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span><span class="sxs-lookup"><span data-stu-id="7634a-244">For information on configuring the module, see [Attributes of the aspNetCore element](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span></span>

## <a name="troubleshoot-on-azure-app-service"></a><span data-ttu-id="7634a-245">Řešení potíží na Azure App Service</span><span class="sxs-lookup"><span data-stu-id="7634a-245">Troubleshoot on Azure App Service</span></span>

[!INCLUDE [Azure App Service Preview Notice](~/includes/azure-apps-preview-notice.md)]

### <a name="application-event-log-azure-app-service"></a><span data-ttu-id="7634a-246">Protokol událostí aplikace (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="7634a-246">Application Event Log (Azure App Service)</span></span>

<span data-ttu-id="7634a-247">Chcete-li získat přístup k protokolu událostí aplikace, použijte okno **Diagnostika a řešení problémů** v Azure Portal:</span><span class="sxs-lookup"><span data-stu-id="7634a-247">To access the Application Event Log, use the **Diagnose and solve problems** blade in the Azure portal:</span></span>

1. <span data-ttu-id="7634a-248">V Azure Portal otevřete aplikaci v **App Services**.</span><span class="sxs-lookup"><span data-stu-id="7634a-248">In the Azure portal, open the app in **App Services**.</span></span>
1. <span data-ttu-id="7634a-249">Vyberte **Diagnostikovat a řešit problémy**.</span><span class="sxs-lookup"><span data-stu-id="7634a-249">Select **Diagnose and solve problems**.</span></span>
1. <span data-ttu-id="7634a-250">Vyberte **diagnostické nástroje** záhlaví.</span><span class="sxs-lookup"><span data-stu-id="7634a-250">Select the **Diagnostic Tools** heading.</span></span>
1. <span data-ttu-id="7634a-251">V nabídce **nástroje podpory**vyberte tlačítko **události aplikace** .</span><span class="sxs-lookup"><span data-stu-id="7634a-251">Under **Support Tools**, select the **Application Events** button.</span></span>
1. <span data-ttu-id="7634a-252">Projděte si nejnovější chybu, kterou poskytla položka *IIS AspNetCoreModule* nebo *IIS AspNetCoreModule v2* ve **zdrojovém** sloupci.</span><span class="sxs-lookup"><span data-stu-id="7634a-252">Examine the latest error provided by the *IIS AspNetCoreModule* or *IIS AspNetCoreModule V2* entry in the **Source** column.</span></span>

<span data-ttu-id="7634a-253">Alternativou k použití okna **diagnostikovat a řešit problémy** je kontrola souboru protokolu událostí aplikace přímo pomocí [Kudu](https://github.com/projectkudu/kudu/wiki):</span><span class="sxs-lookup"><span data-stu-id="7634a-253">An alternative to using the **Diagnose and solve problems** blade is to examine the Application Event Log file directly using [Kudu](https://github.com/projectkudu/kudu/wiki):</span></span>

1. <span data-ttu-id="7634a-254">Otevřete **Rozšířené nástroje** v oblasti **vývojové nástroje** .</span><span class="sxs-lookup"><span data-stu-id="7634a-254">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="7634a-255">Vyberte tlačítko \*\*Přejít&rarr; \*\* .</span><span class="sxs-lookup"><span data-stu-id="7634a-255">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="7634a-256">Konzola Kudu se otevře v novém okně nebo záložce prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="7634a-256">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="7634a-257">Pomocí navigačního panelu v horní části stránky otevřete **konzolu ladění** a vyberte **cmd**.</span><span class="sxs-lookup"><span data-stu-id="7634a-257">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="7634a-258">Otevřete složku **soubory protokolů** .</span><span class="sxs-lookup"><span data-stu-id="7634a-258">Open the **LogFiles** folder.</span></span>
1. <span data-ttu-id="7634a-259">Vyberte ikonu tužky vedle souboru *EventLog. XML* .</span><span class="sxs-lookup"><span data-stu-id="7634a-259">Select the pencil icon next to the *eventlog.xml* file.</span></span>
1. <span data-ttu-id="7634a-260">Projděte si protokol.</span><span class="sxs-lookup"><span data-stu-id="7634a-260">Examine the log.</span></span> <span data-ttu-id="7634a-261">Posuňte se do dolní části protokolu, abyste viděli nejaktuálnější události.</span><span class="sxs-lookup"><span data-stu-id="7634a-261">Scroll to the bottom of the log to see the most recent events.</span></span>

### <a name="run-the-app-in-the-kudu-console"></a><span data-ttu-id="7634a-262">Spuštění aplikace v konzole Kudu</span><span class="sxs-lookup"><span data-stu-id="7634a-262">Run the app in the Kudu console</span></span>

<span data-ttu-id="7634a-263">Mnoho chyb při spuštění nevytváří užitečné informace v protokolu událostí aplikace.</span><span class="sxs-lookup"><span data-stu-id="7634a-263">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="7634a-264">Tuto chybu můžete zjistit spuštěním aplikace v konzole vzdáleného spuštění [Kudu](https://github.com/projectkudu/kudu/wiki) :</span><span class="sxs-lookup"><span data-stu-id="7634a-264">You can run the app in the [Kudu](https://github.com/projectkudu/kudu/wiki) Remote Execution Console to discover the error:</span></span>

1. <span data-ttu-id="7634a-265">Otevřete **Rozšířené nástroje** v oblasti **vývojové nástroje** .</span><span class="sxs-lookup"><span data-stu-id="7634a-265">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="7634a-266">Vyberte tlačítko \*\*Přejít&rarr; \*\* .</span><span class="sxs-lookup"><span data-stu-id="7634a-266">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="7634a-267">Konzola Kudu se otevře v novém okně nebo záložce prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="7634a-267">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="7634a-268">Pomocí navigačního panelu v horní části stránky otevřete **konzolu ladění** a vyberte **cmd**.</span><span class="sxs-lookup"><span data-stu-id="7634a-268">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>

#### <a name="test-a-32-bit-x86-app"></a><span data-ttu-id="7634a-269">Testování 32 (x86) aplikace</span><span class="sxs-lookup"><span data-stu-id="7634a-269">Test a 32-bit (x86) app</span></span>

<span data-ttu-id="7634a-270">**Aktuální verze**</span><span class="sxs-lookup"><span data-stu-id="7634a-270">**Current release**</span></span>

1. `cd d:\home\site\wwwroot`
1. <span data-ttu-id="7634a-271">Spusťte aplikaci:</span><span class="sxs-lookup"><span data-stu-id="7634a-271">Run the app:</span></span>
   * <span data-ttu-id="7634a-272">Pokud aplikace je [nasazení závislé na rozhraní](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span><span class="sxs-lookup"><span data-stu-id="7634a-272">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

     ```dotnetcli
     dotnet .\{ASSEMBLY NAME}.dll
     ```

   * <span data-ttu-id="7634a-273">Pokud je aplikace samostatná, [nasazení](/dotnet/core/deploying/#self-contained-deployments-scd):</span><span class="sxs-lookup"><span data-stu-id="7634a-273">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

     ```console
     {ASSEMBLY NAME}.exe
     ```

<span data-ttu-id="7634a-274">Výstup konzoly z aplikace, v němž se zobrazují všechny chyby, je předán do konzoly Kudu.</span><span class="sxs-lookup"><span data-stu-id="7634a-274">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="7634a-275">**Nasazení závislé na architektuře spuštěné ve verzi Preview**</span><span class="sxs-lookup"><span data-stu-id="7634a-275">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="7634a-276">*Vyžaduje instalaci rozšíření webu ASP.NET Core {VERSION} (x86) Runtime.*</span><span class="sxs-lookup"><span data-stu-id="7634a-276">*Requires installing the ASP.NET Core {VERSION} (x86) Runtime site extension.*</span></span>

1. <span data-ttu-id="7634a-277">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32`(`{X.Y}` je verze modulu runtime)</span><span class="sxs-lookup"><span data-stu-id="7634a-277">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="7634a-278">Spusťte aplikaci: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="7634a-278">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="7634a-279">Výstup konzoly z aplikace, v němž se zobrazují všechny chyby, je předán do konzoly Kudu.</span><span class="sxs-lookup"><span data-stu-id="7634a-279">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

#### <a name="test-a-64-bit-x64-app"></a><span data-ttu-id="7634a-280">Testování 64 aplikace (x64)</span><span class="sxs-lookup"><span data-stu-id="7634a-280">Test a 64-bit (x64) app</span></span>

<span data-ttu-id="7634a-281">**Aktuální verze**</span><span class="sxs-lookup"><span data-stu-id="7634a-281">**Current release**</span></span>

* <span data-ttu-id="7634a-282">Pokud je aplikace [nasazením závislého na rozhraní](/dotnet/core/deploying/#framework-dependent-deployments-fdd)64 (x64):</span><span class="sxs-lookup"><span data-stu-id="7634a-282">If the app is a 64-bit (x64) [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>
  1. `cd D:\Program Files\dotnet`
  1. <span data-ttu-id="7634a-283">Spusťte aplikaci: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="7634a-283">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>
* <span data-ttu-id="7634a-284">Pokud je aplikace samostatná, [nasazení](/dotnet/core/deploying/#self-contained-deployments-scd):</span><span class="sxs-lookup"><span data-stu-id="7634a-284">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>
  1. `cd D:\home\site\wwwroot`
  1. <span data-ttu-id="7634a-285">Spusťte aplikaci: `{ASSEMBLY NAME}.exe`</span><span class="sxs-lookup"><span data-stu-id="7634a-285">Run the app: `{ASSEMBLY NAME}.exe`</span></span>

<span data-ttu-id="7634a-286">Výstup konzoly z aplikace, v němž se zobrazují všechny chyby, je předán do konzoly Kudu.</span><span class="sxs-lookup"><span data-stu-id="7634a-286">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="7634a-287">**Nasazení závislé na architektuře spuštěné ve verzi Preview**</span><span class="sxs-lookup"><span data-stu-id="7634a-287">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="7634a-288">*Vyžaduje instalaci rozšíření webu ASP.NET Core {VERSION} (x64) Runtime.*</span><span class="sxs-lookup"><span data-stu-id="7634a-288">*Requires installing the ASP.NET Core {VERSION} (x64) Runtime site extension.*</span></span>

1. <span data-ttu-id="7634a-289">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64`(`{X.Y}` je verze modulu runtime)</span><span class="sxs-lookup"><span data-stu-id="7634a-289">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="7634a-290">Spusťte aplikaci: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="7634a-290">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="7634a-291">Výstup konzoly z aplikace, v němž se zobrazují všechny chyby, je předán do konzoly Kudu.</span><span class="sxs-lookup"><span data-stu-id="7634a-291">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

### <a name="aspnet-core-module-stdout-log-azure-app-service"></a><span data-ttu-id="7634a-292">Protokol stdout v modulu ASP.NET Core (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="7634a-292">ASP.NET Core Module stdout log (Azure App Service)</span></span>

<span data-ttu-id="7634a-293">Protokol stdout modulu ASP.NET Coree často zaznamenává užitečné chybové zprávy, které se nenašly v protokolu událostí aplikace.</span><span class="sxs-lookup"><span data-stu-id="7634a-293">The ASP.NET Core Module stdout log often records useful error messages not found in the Application Event Log.</span></span> <span data-ttu-id="7634a-294">Postup povolení a zobrazení protokolů stdout:</span><span class="sxs-lookup"><span data-stu-id="7634a-294">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="7634a-295">Přejděte do okna **diagnostikovat a řešit problémy** v Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="7634a-295">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="7634a-296">V části **Vybrat kategorii problému**vyberte tlačítko pro **webovou aplikaci** .</span><span class="sxs-lookup"><span data-stu-id="7634a-296">Under **SELECT PROBLEM CATEGORY**, select the **Web App Down** button.</span></span>
1. <span data-ttu-id="7634a-297">V části **navrhovaná řešení** > **povolte přesměrování protokolu stdout**a výběrem tlačítka **otevřete konzolu Kudu a upravte soubor Web. config**.</span><span class="sxs-lookup"><span data-stu-id="7634a-297">Under **Suggested Solutions** > **Enable Stdout Log Redirection**, select the button to **Open Kudu Console to edit Web.Config**.</span></span>
1. <span data-ttu-id="7634a-298">V konzole pro **diagnostiku**Kudu otevřete složky v **lokalitě** > s cestou**wwwroot**.</span><span class="sxs-lookup"><span data-stu-id="7634a-298">In the Kudu **Diagnostic Console**, open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="7634a-299">Posuňte se dolů a odhalte soubor *Web. config* v dolní části seznamu.</span><span class="sxs-lookup"><span data-stu-id="7634a-299">Scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="7634a-300">Klikněte na ikonu tužky vedle souboru *Web. config* .</span><span class="sxs-lookup"><span data-stu-id="7634a-300">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="7634a-301">Nastavte **stdoutLogEnabled** na `true` a změňte cestu **stdoutLogFile** na: `\\?\%home%\LogFiles\stdout`.</span><span class="sxs-lookup"><span data-stu-id="7634a-301">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="7634a-302">Vyberte **Uložit** a uložte aktualizovaný soubor *Web. config* .</span><span class="sxs-lookup"><span data-stu-id="7634a-302">Select **Save** to save the updated *web.config* file.</span></span>
1. <span data-ttu-id="7634a-303">Vytvořte žádost do aplikace.</span><span class="sxs-lookup"><span data-stu-id="7634a-303">Make a request to the app.</span></span>
1. <span data-ttu-id="7634a-304">Vraťte se na Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="7634a-304">Return to the Azure portal.</span></span> <span data-ttu-id="7634a-305">V oblasti **vývojové nástroje** vyberte okno **Pokročilé nástroje** .</span><span class="sxs-lookup"><span data-stu-id="7634a-305">Select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="7634a-306">Vyberte tlačítko \*\*Přejít&rarr; \*\* .</span><span class="sxs-lookup"><span data-stu-id="7634a-306">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="7634a-307">Konzola Kudu se otevře v novém okně nebo záložce prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="7634a-307">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="7634a-308">Pomocí navigačního panelu v horní části stránky otevřete **konzolu ladění** a vyberte **cmd**.</span><span class="sxs-lookup"><span data-stu-id="7634a-308">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="7634a-309">Vyberte složku **soubory protokolů** .</span><span class="sxs-lookup"><span data-stu-id="7634a-309">Select the **LogFiles** folder.</span></span>
1. <span data-ttu-id="7634a-310">Zkontrolujte **upravený** sloupec a vyberte ikonu tužky a upravte protokol stdout s nejnovějším datem úpravy.</span><span class="sxs-lookup"><span data-stu-id="7634a-310">Inspect the **Modified** column and select the pencil icon to edit the stdout log with the latest modification date.</span></span>
1. <span data-ttu-id="7634a-311">Po otevření souboru protokolu se zobrazí chyba.</span><span class="sxs-lookup"><span data-stu-id="7634a-311">When the log file opens, the error is displayed.</span></span>

<span data-ttu-id="7634a-312">Zakázat protokolování stdout při odstraňování potíží:</span><span class="sxs-lookup"><span data-stu-id="7634a-312">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="7634a-313">V **diagnostické konzole**Kudu se vraťte do cesty**wwwroot** **lokality** > , která odhalí soubor *Web. config* .</span><span class="sxs-lookup"><span data-stu-id="7634a-313">In the Kudu **Diagnostic Console**, return to the path **site** > **wwwroot** to reveal the *web.config* file.</span></span> <span data-ttu-id="7634a-314">Otevřete soubor **Web. config** znovu výběrem ikony tužky.</span><span class="sxs-lookup"><span data-stu-id="7634a-314">Open the **web.config** file again by selecting the pencil icon.</span></span>
1. <span data-ttu-id="7634a-315">Nastavte **stdoutLogEnabled** na `false`.</span><span class="sxs-lookup"><span data-stu-id="7634a-315">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="7634a-316">Vyberte **Uložit** a soubor uložte.</span><span class="sxs-lookup"><span data-stu-id="7634a-316">Select **Save** to save the file.</span></span>

<span data-ttu-id="7634a-317">Další informace naleznete v tématu <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span><span class="sxs-lookup"><span data-stu-id="7634a-317">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

> [!WARNING]
> <span data-ttu-id="7634a-318">Nepovedlo se zakázat protokol stdout, což může způsobit selhání aplikace nebo serveru.</span><span class="sxs-lookup"><span data-stu-id="7634a-318">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="7634a-319">Velikost souboru protokolu ani počet vytvořených souborů protokolu nijak neomezuje.</span><span class="sxs-lookup"><span data-stu-id="7634a-319">There's no limit on log file size or the number of log files created.</span></span> <span data-ttu-id="7634a-320">K řešení problémů se spouštěním aplikací použijte pouze protokolování STDOUT.</span><span class="sxs-lookup"><span data-stu-id="7634a-320">Only use stdout logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="7634a-321">Pro obecné protokolování v aplikaci ASP.NET Core po spuštění použijte knihovnu protokolování, která omezuje velikost souboru protokolu a otočí protokoly.</span><span class="sxs-lookup"><span data-stu-id="7634a-321">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="7634a-322">Další informace najdete v tématu [Zprostředkovatelé protokolování třetích stran](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="7634a-322">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="aspnet-core-module-debug-log-azure-app-service"></a><span data-ttu-id="7634a-323">Protokol ladění modulu ASP.NET Core (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="7634a-323">ASP.NET Core Module debug log (Azure App Service)</span></span>

<span data-ttu-id="7634a-324">Protokol ladění ASP.NET Core modulu poskytuje další, hlubší protokolování z modulu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7634a-324">The ASP.NET Core Module debug log provides additional, deeper logging from the ASP.NET Core Module.</span></span> <span data-ttu-id="7634a-325">Postup povolení a zobrazení protokolů stdout:</span><span class="sxs-lookup"><span data-stu-id="7634a-325">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="7634a-326">Pro povolení rozšířeného diagnostického protokolu proveďte jednu z následujících akcí:</span><span class="sxs-lookup"><span data-stu-id="7634a-326">To enable the enhanced diagnostic log, perform either of the following:</span></span>
   * <span data-ttu-id="7634a-327">Postupujte podle pokynů v [rozšířených diagnostických protokolech](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) a nakonfigurujte aplikaci pro rozšířené diagnostické protokolování.</span><span class="sxs-lookup"><span data-stu-id="7634a-327">Follow the instructions in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) to configure the app for an enhanced diagnostic logging.</span></span> <span data-ttu-id="7634a-328">Znovu nasaďte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="7634a-328">Redeploy the app.</span></span>
   * <span data-ttu-id="7634a-329">Pomocí konzoly `<handlerSettings>` Kudu přidejte do souboru *Web. config* živé aplikace zobrazené [Rozšířené diagnostické protokoly](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) :</span><span class="sxs-lookup"><span data-stu-id="7634a-329">Add the `<handlerSettings>` shown in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) to the live app's *web.config* file using the Kudu console:</span></span>
     1. <span data-ttu-id="7634a-330">Otevřete **Rozšířené nástroje** v oblasti **vývojové nástroje** .</span><span class="sxs-lookup"><span data-stu-id="7634a-330">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="7634a-331">Vyberte tlačítko \*\*Přejít&rarr; \*\* .</span><span class="sxs-lookup"><span data-stu-id="7634a-331">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="7634a-332">Konzola Kudu se otevře v novém okně nebo záložce prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="7634a-332">The Kudu console opens in a new browser tab or window.</span></span>
     1. <span data-ttu-id="7634a-333">Pomocí navigačního panelu v horní části stránky otevřete **konzolu ladění** a vyberte **cmd**.</span><span class="sxs-lookup"><span data-stu-id="7634a-333">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
     1. <span data-ttu-id="7634a-334">Otevřete složky na cestě **lokality** > **wwwroot**.</span><span class="sxs-lookup"><span data-stu-id="7634a-334">Open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="7634a-335">Upravte soubor *Web. config* tak, že vyberete tlačítko tužky.</span><span class="sxs-lookup"><span data-stu-id="7634a-335">Edit the *web.config* file by selecting the pencil button.</span></span> <span data-ttu-id="7634a-336">Přidejte oddíl `<handlerSettings>` , jak je znázorněno v [rozšířených diagnostických protokolech](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs).</span><span class="sxs-lookup"><span data-stu-id="7634a-336">Add the `<handlerSettings>` section as shown in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs).</span></span> <span data-ttu-id="7634a-337">Vyberte tlačítko **Uložit**.</span><span class="sxs-lookup"><span data-stu-id="7634a-337">Select the **Save** button.</span></span>
1. <span data-ttu-id="7634a-338">Otevřete **Rozšířené nástroje** v oblasti **vývojové nástroje** .</span><span class="sxs-lookup"><span data-stu-id="7634a-338">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="7634a-339">Vyberte tlačítko \*\*Přejít&rarr; \*\* .</span><span class="sxs-lookup"><span data-stu-id="7634a-339">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="7634a-340">Konzola Kudu se otevře v novém okně nebo záložce prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="7634a-340">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="7634a-341">Pomocí navigačního panelu v horní části stránky otevřete **konzolu ladění** a vyberte **cmd**.</span><span class="sxs-lookup"><span data-stu-id="7634a-341">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="7634a-342">Otevřete složky na cestě **lokality** > **wwwroot**.</span><span class="sxs-lookup"><span data-stu-id="7634a-342">Open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="7634a-343">Pokud jste nezadali cestu k souboru *aspnetcore-Debug. log* , soubor se zobrazí v seznamu.</span><span class="sxs-lookup"><span data-stu-id="7634a-343">If you didn't supply a path for the *aspnetcore-debug.log* file, the file appears in the list.</span></span> <span data-ttu-id="7634a-344">Pokud jste zadali cestu, přejděte do umístění souboru protokolu.</span><span class="sxs-lookup"><span data-stu-id="7634a-344">If you supplied a path, navigate to the location of the log file.</span></span>
1. <span data-ttu-id="7634a-345">Otevřete soubor protokolu s tlačítkem tužky vedle názvu souboru.</span><span class="sxs-lookup"><span data-stu-id="7634a-345">Open the log file with the pencil button next to the file name.</span></span>

<span data-ttu-id="7634a-346">Zakázat protokolování ladění, pokud je dokončeno odstraňování potíží:</span><span class="sxs-lookup"><span data-stu-id="7634a-346">Disable debug logging when troubleshooting is complete:</span></span>

<span data-ttu-id="7634a-347">Chcete-li zakázat rozšířený protokol ladění, proveďte jednu z následujících akcí:</span><span class="sxs-lookup"><span data-stu-id="7634a-347">To disable the enhanced debug log, perform either of the following:</span></span>

* <span data-ttu-id="7634a-348">`<handlerSettings>` Odeberte soubor *Web. config* místně a pak aplikaci znovu nasaďte.</span><span class="sxs-lookup"><span data-stu-id="7634a-348">Remove the `<handlerSettings>` from the *web.config* file locally and redeploy the app.</span></span>
* <span data-ttu-id="7634a-349">Pomocí konzoly Kudu upravte soubor *Web. config* a odeberte `<handlerSettings>` oddíl.</span><span class="sxs-lookup"><span data-stu-id="7634a-349">Use the Kudu console to edit the *web.config* file and remove the `<handlerSettings>` section.</span></span> <span data-ttu-id="7634a-350">Uložte soubor.</span><span class="sxs-lookup"><span data-stu-id="7634a-350">Save the file.</span></span>

<span data-ttu-id="7634a-351">Další informace naleznete v tématu <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span><span class="sxs-lookup"><span data-stu-id="7634a-351">For more information, see <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span></span>

> [!WARNING]
> <span data-ttu-id="7634a-352">Nepovedlo se zakázat protokol ladění, může způsobit selhání aplikace nebo serveru.</span><span class="sxs-lookup"><span data-stu-id="7634a-352">Failure to disable the debug log can lead to app or server failure.</span></span> <span data-ttu-id="7634a-353">Velikost souboru protokolu není nijak omezena.</span><span class="sxs-lookup"><span data-stu-id="7634a-353">There's no limit on log file size.</span></span> <span data-ttu-id="7634a-354">K řešení problémů se spouštěním aplikace používejte pouze protokolování ladění.</span><span class="sxs-lookup"><span data-stu-id="7634a-354">Only use debug logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="7634a-355">Pro obecné protokolování v aplikaci ASP.NET Core po spuštění použijte knihovnu protokolování, která omezuje velikost souboru protokolu a otočí protokoly.</span><span class="sxs-lookup"><span data-stu-id="7634a-355">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="7634a-356">Další informace najdete v tématu [Zprostředkovatelé protokolování třetích stran](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="7634a-356">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="slow-or-hanging-app-azure-app-service"></a><span data-ttu-id="7634a-357">Pomalá nebo zavěšená aplikace (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="7634a-357">Slow or hanging app (Azure App Service)</span></span>

<span data-ttu-id="7634a-358">Pokud aplikace reaguje pomalu nebo přestane reagovat na žádost, přečtěte si následující články:</span><span class="sxs-lookup"><span data-stu-id="7634a-358">When an app responds slowly or hangs on a request, see the following articles:</span></span>

* [<span data-ttu-id="7634a-359">Řešení potíží s výkonem pomalých webových aplikací ve službě Azure App Service</span><span class="sxs-lookup"><span data-stu-id="7634a-359">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="7634a-360">K zachycení výpisu problémů s přerušovanou výjimkou nebo problémy s výkonem ve webové aplikaci Azure použijte rozšíření pro diagnostiku zhroucení serveru.</span><span class="sxs-lookup"><span data-stu-id="7634a-360">Use Crash Diagnoser Site Extension to Capture Dump for Intermittent Exception issues or performance issues on Azure Web App</span></span>](https://blogs.msdn.microsoft.com/asiatech/2015/12/28/use-crash-diagnoser-site-extension-to-capture-dump-for-intermittent-exception-issues-or-performance-issues-on-azure-web-app/)

### <a name="monitoring-blades"></a><span data-ttu-id="7634a-361">Okna monitorování</span><span class="sxs-lookup"><span data-stu-id="7634a-361">Monitoring blades</span></span>

<span data-ttu-id="7634a-362">Okna monitorování poskytují alternativní možnosti řešení potíží pro metody popsané dříve v tématu.</span><span class="sxs-lookup"><span data-stu-id="7634a-362">Monitoring blades provide an alternative troubleshooting experience to the methods described earlier in the topic.</span></span> <span data-ttu-id="7634a-363">Pomocí těchto oken můžete diagnostikovat chyby 500-Series.</span><span class="sxs-lookup"><span data-stu-id="7634a-363">These blades can be used to diagnose 500-series errors.</span></span>

<span data-ttu-id="7634a-364">Potvrďte, že jsou nainstalovaná rozšíření ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7634a-364">Confirm that the ASP.NET Core Extensions are installed.</span></span> <span data-ttu-id="7634a-365">Pokud nejsou rozšíření nainstalována, nainstalujte je ručně:</span><span class="sxs-lookup"><span data-stu-id="7634a-365">If the extensions aren't installed, install them manually:</span></span>

1. <span data-ttu-id="7634a-366">V části okno **vývojové nástroje** vyberte okno **rozšíření** .</span><span class="sxs-lookup"><span data-stu-id="7634a-366">In the **DEVELOPMENT TOOLS** blade section, select the **Extensions** blade.</span></span>
1. <span data-ttu-id="7634a-367">V seznamu by se měla zobrazit **rozšíření ASP.NET Core** .</span><span class="sxs-lookup"><span data-stu-id="7634a-367">The **ASP.NET Core Extensions** should appear in the list.</span></span>
1. <span data-ttu-id="7634a-368">Pokud nejsou rozšíření nainstalovaná, vyberte tlačítko **Přidat** .</span><span class="sxs-lookup"><span data-stu-id="7634a-368">If the extensions aren't installed, select the **Add** button.</span></span>
1. <span data-ttu-id="7634a-369">Ze seznamu vyberte **rozšíření ASP.NET Core** .</span><span class="sxs-lookup"><span data-stu-id="7634a-369">Choose the **ASP.NET Core Extensions** from the list.</span></span>
1. <span data-ttu-id="7634a-370">Kliknutím na **OK** přijměte právní podmínky.</span><span class="sxs-lookup"><span data-stu-id="7634a-370">Select **OK** to accept the legal terms.</span></span>
1. <span data-ttu-id="7634a-371">V okně **Přidat rozšíření** vyberte **OK** .</span><span class="sxs-lookup"><span data-stu-id="7634a-371">Select **OK** on the **Add extension** blade.</span></span>
1. <span data-ttu-id="7634a-372">Informační automaticky otevíraná zpráva indikuje, že se rozšíření úspěšně nainstalovala.</span><span class="sxs-lookup"><span data-stu-id="7634a-372">An informational pop-up message indicates when the extensions are successfully installed.</span></span>

<span data-ttu-id="7634a-373">Pokud není povoleno protokolování stdout, postupujte následovně:</span><span class="sxs-lookup"><span data-stu-id="7634a-373">If stdout logging isn't enabled, follow these steps:</span></span>

1. <span data-ttu-id="7634a-374">V Azure Portal v oblasti **vývojové nástroje** vyberte okno **Pokročilé nástroje** .</span><span class="sxs-lookup"><span data-stu-id="7634a-374">In the Azure portal, select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="7634a-375">Vyberte tlačítko \*\*Přejít&rarr; \*\* .</span><span class="sxs-lookup"><span data-stu-id="7634a-375">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="7634a-376">Konzola Kudu se otevře v novém okně nebo záložce prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="7634a-376">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="7634a-377">Pomocí navigačního panelu v horní části stránky otevřete **konzolu ladění** a vyberte **cmd**.</span><span class="sxs-lookup"><span data-stu-id="7634a-377">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="7634a-378">Otevřete složky **pro cestu** > **wwwroot** a posuňte se dolů, aby se v dolní části seznamu zobrazil soubor *Web. config* .</span><span class="sxs-lookup"><span data-stu-id="7634a-378">Open the folders to the path **site** > **wwwroot** and scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="7634a-379">Klikněte na ikonu tužky vedle souboru *Web. config* .</span><span class="sxs-lookup"><span data-stu-id="7634a-379">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="7634a-380">Nastavte **stdoutLogEnabled** na `true` a změňte cestu **stdoutLogFile** na: `\\?\%home%\LogFiles\stdout`.</span><span class="sxs-lookup"><span data-stu-id="7634a-380">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="7634a-381">Vyberte **Uložit** a uložte aktualizovaný soubor *Web. config* .</span><span class="sxs-lookup"><span data-stu-id="7634a-381">Select **Save** to save the updated *web.config* file.</span></span>

<span data-ttu-id="7634a-382">Pokračovat k aktivaci protokolování diagnostiky:</span><span class="sxs-lookup"><span data-stu-id="7634a-382">Proceed to activate diagnostic logging:</span></span>

1. <span data-ttu-id="7634a-383">V Azure Portal vyberte okno **diagnostické protokoly** .</span><span class="sxs-lookup"><span data-stu-id="7634a-383">In the Azure portal, select the **Diagnostics logs** blade.</span></span>
1. <span data-ttu-id="7634a-384">Vyberte přepínač **zapnuto** pro **protokolování aplikace (systém souborů)** a **podrobné chybové zprávy**.</span><span class="sxs-lookup"><span data-stu-id="7634a-384">Select the **On** switch for **Application Logging (Filesystem)** and **Detailed error messages**.</span></span> <span data-ttu-id="7634a-385">V horní části okna vyberte tlačítko **Uložit** .</span><span class="sxs-lookup"><span data-stu-id="7634a-385">Select the **Save** button at the top of the blade.</span></span>
1. <span data-ttu-id="7634a-386">Chcete-li zahrnout trasování chybných požadavků, označované také jako protokolování událostí neúspěšných požadavků na FREB, vyberte přepínač **zapnuto** pro **trasování chybných požadavků**.</span><span class="sxs-lookup"><span data-stu-id="7634a-386">To include failed request tracing, also known as Failed Request Event Buffering (FREB) logging, select the **On** switch for **Failed request tracing**.</span></span>
1. <span data-ttu-id="7634a-387">Vyberte okno **log Stream** , které je uvedeno hned pod oknem **diagnostické protokoly** na portálu.</span><span class="sxs-lookup"><span data-stu-id="7634a-387">Select the **Log stream** blade, which is listed immediately under the **Diagnostics logs** blade in the portal.</span></span>
1. <span data-ttu-id="7634a-388">Vytvořte žádost do aplikace.</span><span class="sxs-lookup"><span data-stu-id="7634a-388">Make a request to the app.</span></span>
1. <span data-ttu-id="7634a-389">V datech streamu protokolu se označuje příčina chyby.</span><span class="sxs-lookup"><span data-stu-id="7634a-389">Within the log stream data, the cause of the error is indicated.</span></span>

<span data-ttu-id="7634a-390">Nezapomeňte zakázat protokolování stdout po dokončení řešení potíží.</span><span class="sxs-lookup"><span data-stu-id="7634a-390">Be sure to disable stdout logging when troubleshooting is complete.</span></span>

<span data-ttu-id="7634a-391">Zobrazení protokolů pro trasování chybných požadavků (protokoly FREB):</span><span class="sxs-lookup"><span data-stu-id="7634a-391">To view the failed request tracing logs (FREB logs):</span></span>

1. <span data-ttu-id="7634a-392">Přejděte do okna **diagnostikovat a řešit problémy** v Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="7634a-392">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="7634a-393">V oblasti **nástroje podpory** v postranním panelu vyberte **protokoly pro trasování chybných požadavků** .</span><span class="sxs-lookup"><span data-stu-id="7634a-393">Select **Failed Request Tracing Logs** from the **SUPPORT TOOLS** area of the sidebar.</span></span>

<span data-ttu-id="7634a-394">V [části Povolení protokolování diagnostiky pro webové aplikace v Azure App Service tématu](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) a [Nejčastější dotazy týkající se výkonu aplikace pro Web Apps v Azure: návody zapnout trasování chybných požadavků?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing) Další informace.</span><span class="sxs-lookup"><span data-stu-id="7634a-394">See [Failed request traces section of the Enable diagnostics logging for web apps in Azure App Service topic](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) and the [Application performance FAQs for Web Apps in Azure: How do I turn on failed request tracing?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing) for more information.</span></span>

<span data-ttu-id="7634a-395">Další informace najdete v tématu [Povolení protokolování diagnostiky pro webové aplikace v Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log).</span><span class="sxs-lookup"><span data-stu-id="7634a-395">For more information, see [Enable diagnostics logging for web apps in Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log).</span></span>

> [!WARNING]
> <span data-ttu-id="7634a-396">Nepovedlo se zakázat protokol stdout, což může způsobit selhání aplikace nebo serveru.</span><span class="sxs-lookup"><span data-stu-id="7634a-396">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="7634a-397">Velikost souboru protokolu ani počet vytvořených souborů protokolu nijak neomezuje.</span><span class="sxs-lookup"><span data-stu-id="7634a-397">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="7634a-398">Pro rutinu protokolování v aplikaci ASP.NET Core použijte knihovnu protokolování, která omezuje velikost souboru protokolu a otočí protokoly.</span><span class="sxs-lookup"><span data-stu-id="7634a-398">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="7634a-399">Další informace najdete v tématu [Zprostředkovatelé protokolování třetích stran](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="7634a-399">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

## <a name="troubleshoot-on-iis"></a><span data-ttu-id="7634a-400">Odstraňování potíží ve službě IIS</span><span class="sxs-lookup"><span data-stu-id="7634a-400">Troubleshoot on IIS</span></span>

### <a name="application-event-log-iis"></a><span data-ttu-id="7634a-401">Protokol událostí aplikace (IIS)</span><span class="sxs-lookup"><span data-stu-id="7634a-401">Application Event Log (IIS)</span></span>

<span data-ttu-id="7634a-402">Přístup k protokolu událostí aplikace:</span><span class="sxs-lookup"><span data-stu-id="7634a-402">Access the Application Event Log:</span></span>

1. <span data-ttu-id="7634a-403">Otevřete nabídku Start, vyhledejte *Prohlížeč událostí*a vyberte aplikaci **Prohlížeč událostí** .</span><span class="sxs-lookup"><span data-stu-id="7634a-403">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="7634a-404">V **Prohlížeč událostí**otevřete uzel **protokoly systému Windows** .</span><span class="sxs-lookup"><span data-stu-id="7634a-404">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="7634a-405">Výběrem **aplikace** otevřete protokol událostí aplikace.</span><span class="sxs-lookup"><span data-stu-id="7634a-405">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="7634a-406">Vyhledá chyby spojené s chybnou aplikací.</span><span class="sxs-lookup"><span data-stu-id="7634a-406">Search for errors associated with the failing app.</span></span> <span data-ttu-id="7634a-407">Chyby mají ve *zdrojovém* sloupci hodnotu *modulu IIS AspNetCore* nebo *modul IIS Express AspNetCore* .</span><span class="sxs-lookup"><span data-stu-id="7634a-407">Errors have a value of *IIS AspNetCore Module* or *IIS Express AspNetCore Module* in the *Source* column.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="7634a-408">Spuštění aplikace na příkazovém řádku</span><span class="sxs-lookup"><span data-stu-id="7634a-408">Run the app at a command prompt</span></span>

<span data-ttu-id="7634a-409">Mnoho chyb při spuštění nevytváří užitečné informace v protokolu událostí aplikace.</span><span class="sxs-lookup"><span data-stu-id="7634a-409">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="7634a-410">Příčinu některých chyb můžete najít spuštěním aplikace na příkazovém řádku v hostitelském systému.</span><span class="sxs-lookup"><span data-stu-id="7634a-410">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span>

#### <a name="framework-dependent-deployment"></a><span data-ttu-id="7634a-411">Nasazení závislé na rozhraní</span><span class="sxs-lookup"><span data-stu-id="7634a-411">Framework-dependent deployment</span></span>

<span data-ttu-id="7634a-412">Pokud aplikace je [nasazení závislé na rozhraní](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span><span class="sxs-lookup"><span data-stu-id="7634a-412">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

1. <span data-ttu-id="7634a-413">Na příkazovém řádku přejděte do složky pro nasazení a spusťte aplikaci spuštěním sestavení aplikace pomocí příkazu *dotnet. exe*.</span><span class="sxs-lookup"><span data-stu-id="7634a-413">At a command prompt, navigate to the deployment folder and run the app by executing the app's assembly with *dotnet.exe*.</span></span> <span data-ttu-id="7634a-414">V následujícím příkazu nahraďte název sestavení aplikace pro \<assembly_name>:. `dotnet .\<assembly_name>.dll`</span><span class="sxs-lookup"><span data-stu-id="7634a-414">In the following command, substitute the name of the app's assembly for \<assembly_name>: `dotnet .\<assembly_name>.dll`.</span></span>
1. <span data-ttu-id="7634a-415">Výstup konzoly z aplikace, který zobrazuje všechny chyby, je zapsán do okna konzoly.</span><span class="sxs-lookup"><span data-stu-id="7634a-415">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="7634a-416">Pokud dojde k chybám při podání žádosti do aplikace, vytvořte žádost na hostitele a port, kde Kestrel naslouchá.</span><span class="sxs-lookup"><span data-stu-id="7634a-416">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="7634a-417">Pomocí výchozího hostitele a příspěvku vytvořte žádost `http://localhost:5000/`.</span><span class="sxs-lookup"><span data-stu-id="7634a-417">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="7634a-418">Pokud aplikace reaguje normálně na adrese koncového bodu Kestrel, problém je pravděpodobnější v souvislosti s konfigurací hostování a méně pravděpodobným v rámci aplikace.</span><span class="sxs-lookup"><span data-stu-id="7634a-418">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

#### <a name="self-contained-deployment"></a><span data-ttu-id="7634a-419">Samostatně zahrnuté nasazení</span><span class="sxs-lookup"><span data-stu-id="7634a-419">Self-contained deployment</span></span>

<span data-ttu-id="7634a-420">Pokud je aplikace samostatná, [nasazení](/dotnet/core/deploying/#self-contained-deployments-scd):</span><span class="sxs-lookup"><span data-stu-id="7634a-420">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

1. <span data-ttu-id="7634a-421">Na příkazovém řádku přejděte do složky pro nasazení a spusťte spustitelný soubor aplikace.</span><span class="sxs-lookup"><span data-stu-id="7634a-421">At a command prompt, navigate to the deployment folder and run the app's executable.</span></span> <span data-ttu-id="7634a-422">V následujícím příkazu nahraďte název sestavení aplikace pro \<assembly_name>:. `<assembly_name>.exe`</span><span class="sxs-lookup"><span data-stu-id="7634a-422">In the following command, substitute the name of the app's assembly for \<assembly_name>: `<assembly_name>.exe`.</span></span>
1. <span data-ttu-id="7634a-423">Výstup konzoly z aplikace, který zobrazuje všechny chyby, je zapsán do okna konzoly.</span><span class="sxs-lookup"><span data-stu-id="7634a-423">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="7634a-424">Pokud dojde k chybám při podání žádosti do aplikace, vytvořte žádost na hostitele a port, kde Kestrel naslouchá.</span><span class="sxs-lookup"><span data-stu-id="7634a-424">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="7634a-425">Pomocí výchozího hostitele a příspěvku vytvořte žádost `http://localhost:5000/`.</span><span class="sxs-lookup"><span data-stu-id="7634a-425">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="7634a-426">Pokud aplikace reaguje normálně na adrese koncového bodu Kestrel, problém je pravděpodobnější v souvislosti s konfigurací hostování a méně pravděpodobným v rámci aplikace.</span><span class="sxs-lookup"><span data-stu-id="7634a-426">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

### <a name="aspnet-core-module-stdout-log-iis"></a><span data-ttu-id="7634a-427">Protokol stdout v modulu ASP.NET Core (IIS)</span><span class="sxs-lookup"><span data-stu-id="7634a-427">ASP.NET Core Module stdout log (IIS)</span></span>

<span data-ttu-id="7634a-428">Postup povolení a zobrazení protokolů stdout:</span><span class="sxs-lookup"><span data-stu-id="7634a-428">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="7634a-429">Přejděte do složky pro nasazení webu v hostitelském systému.</span><span class="sxs-lookup"><span data-stu-id="7634a-429">Navigate to the site's deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="7634a-430">Pokud složka *logs* není k dispozici, vytvořte složku.</span><span class="sxs-lookup"><span data-stu-id="7634a-430">If the *logs* folder isn't present, create the folder.</span></span> <span data-ttu-id="7634a-431">Pokyny k tomu, jak povolit MSBuild pro vytvoření složky *logs* v nasazení automaticky, najdete v tématu [Struktura adresáře](xref:host-and-deploy/directory-structure) .</span><span class="sxs-lookup"><span data-stu-id="7634a-431">For instructions on how to enable MSBuild to create the *logs* folder in the deployment automatically, see the [Directory structure](xref:host-and-deploy/directory-structure) topic.</span></span>
1. <span data-ttu-id="7634a-432">Upravte soubor *Web. config* .</span><span class="sxs-lookup"><span data-stu-id="7634a-432">Edit the *web.config* file.</span></span> <span data-ttu-id="7634a-433">Nastavte **stdoutLogEnabled** na `true` a změňte cestu **stdoutLogFile** tak, aby odkazovala na složku *logs* (například `.\logs\stdout`).</span><span class="sxs-lookup"><span data-stu-id="7634a-433">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to point to the *logs* folder (for example, `.\logs\stdout`).</span></span> <span data-ttu-id="7634a-434">`stdout`v cestě je předpona názvu souboru protokolu.</span><span class="sxs-lookup"><span data-stu-id="7634a-434">`stdout` in the path is the log file name prefix.</span></span> <span data-ttu-id="7634a-435">Časové razítko, ID procesu a Přípona souboru se automaticky přidají při vytvoření protokolu.</span><span class="sxs-lookup"><span data-stu-id="7634a-435">A timestamp, process id, and file extension are added automatically when the log is created.</span></span> <span data-ttu-id="7634a-436">Jako `stdout` Předpona názvu souboru se používá typický soubor protokolu s názvem *stdout_20180205184032_5412. log*.</span><span class="sxs-lookup"><span data-stu-id="7634a-436">Using `stdout` as the file name prefix, a typical log file is named *stdout_20180205184032_5412.log*.</span></span>
1. <span data-ttu-id="7634a-437">Zajistěte, aby identita fondu aplikací měla oprávnění k zápisu do složky *logs* .</span><span class="sxs-lookup"><span data-stu-id="7634a-437">Ensure your application pool's identity has write permissions to the *logs* folder.</span></span>
1. <span data-ttu-id="7634a-438">Uložte aktualizovaný soubor *Web. config* .</span><span class="sxs-lookup"><span data-stu-id="7634a-438">Save the updated *web.config* file.</span></span>
1. <span data-ttu-id="7634a-439">Vytvořte žádost do aplikace.</span><span class="sxs-lookup"><span data-stu-id="7634a-439">Make a request to the app.</span></span>
1. <span data-ttu-id="7634a-440">Přejděte do složky *logs* .</span><span class="sxs-lookup"><span data-stu-id="7634a-440">Navigate to the *logs* folder.</span></span> <span data-ttu-id="7634a-441">Vyhledejte a otevřete nejnovější protokol STDOUT.</span><span class="sxs-lookup"><span data-stu-id="7634a-441">Find and open the most recent stdout log.</span></span>
1. <span data-ttu-id="7634a-442">Prostudujte si protokol o chybách.</span><span class="sxs-lookup"><span data-stu-id="7634a-442">Study the log for errors.</span></span>

<span data-ttu-id="7634a-443">Zakázat protokolování stdout při odstraňování potíží:</span><span class="sxs-lookup"><span data-stu-id="7634a-443">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="7634a-444">Upravte soubor *Web. config* .</span><span class="sxs-lookup"><span data-stu-id="7634a-444">Edit the *web.config* file.</span></span>
1. <span data-ttu-id="7634a-445">Nastavte **stdoutLogEnabled** na `false`.</span><span class="sxs-lookup"><span data-stu-id="7634a-445">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="7634a-446">Uložte soubor.</span><span class="sxs-lookup"><span data-stu-id="7634a-446">Save the file.</span></span>

<span data-ttu-id="7634a-447">Další informace naleznete v tématu <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span><span class="sxs-lookup"><span data-stu-id="7634a-447">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

> [!WARNING]
> <span data-ttu-id="7634a-448">Nepovedlo se zakázat protokol stdout, což může způsobit selhání aplikace nebo serveru.</span><span class="sxs-lookup"><span data-stu-id="7634a-448">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="7634a-449">Velikost souboru protokolu ani počet vytvořených souborů protokolu nijak neomezuje.</span><span class="sxs-lookup"><span data-stu-id="7634a-449">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="7634a-450">Pro rutinu protokolování v aplikaci ASP.NET Core použijte knihovnu protokolování, která omezuje velikost souboru protokolu a otočí protokoly.</span><span class="sxs-lookup"><span data-stu-id="7634a-450">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="7634a-451">Další informace najdete v tématu [Zprostředkovatelé protokolování třetích stran](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="7634a-451">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="aspnet-core-module-debug-log-iis"></a><span data-ttu-id="7634a-452">Protokol ladění modulu ASP.NET Core (IIS)</span><span class="sxs-lookup"><span data-stu-id="7634a-452">ASP.NET Core Module debug log (IIS)</span></span>

<span data-ttu-id="7634a-453">Přidejte následující nastavení obslužné rutiny do souboru *Web. config* aplikace, aby bylo možné povolit protokol ladění ASP.NET Core modulu:</span><span class="sxs-lookup"><span data-stu-id="7634a-453">Add the following handler settings to the app's *web.config* file to enable ASP.NET Core Module debug log:</span></span>

```xml
<aspNetCore ...>
  <handlerSettings>
    <handlerSetting name="debugLevel" value="file" />
    <handlerSetting name="debugFile" value="c:\temp\ancm.log" />
  </handlerSettings>
</aspNetCore>
```

<span data-ttu-id="7634a-454">Potvrďte, že zadaná cesta k protokolu existuje a že má identita fondu aplikací oprávnění k zápisu do tohoto umístění.</span><span class="sxs-lookup"><span data-stu-id="7634a-454">Confirm that the path specified for the log exists and that the app pool's identity has write permissions to the location.</span></span>

<span data-ttu-id="7634a-455">Další informace naleznete v tématu <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span><span class="sxs-lookup"><span data-stu-id="7634a-455">For more information, see <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span></span>

### <a name="enable-the-developer-exception-page"></a><span data-ttu-id="7634a-456">Povolit stránku výjimky pro vývojáře</span><span class="sxs-lookup"><span data-stu-id="7634a-456">Enable the Developer Exception Page</span></span>

<span data-ttu-id="7634a-457">`ASPNETCORE_ENVIRONMENT` [Proměnnou prostředí lze přidat do souboru Web. config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) , aby bylo možné aplikaci spustit ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="7634a-457">The `ASPNETCORE_ENVIRONMENT` [environment variable can be added to web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) to run the app in the Development environment.</span></span> <span data-ttu-id="7634a-458">Pokud se prostředí nepřepisuje při spuštění `UseEnvironment` aplikace v Tvůrci hostitele, nastavení proměnné prostředí umožní, aby se při spuštění aplikace zobrazila stránka s [výjimkou vývojářů](xref:fundamentals/error-handling) .</span><span class="sxs-lookup"><span data-stu-id="7634a-458">As long as the environment isn't overridden in app startup by `UseEnvironment` on the host builder, setting the environment variable allows the [Developer Exception Page](xref:fundamentals/error-handling) to appear when the app is run.</span></span>

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

<span data-ttu-id="7634a-459">Nastavení proměnné prostředí pro `ASPNETCORE_ENVIRONMENT` se doporučuje jenom pro použití na pracovních a testovacích serverech, které nejsou přístupné pro Internet.</span><span class="sxs-lookup"><span data-stu-id="7634a-459">Setting the environment variable for `ASPNETCORE_ENVIRONMENT` is only recommended for use on staging and testing servers that aren't exposed to the Internet.</span></span> <span data-ttu-id="7634a-460">Po vyřešení potíží odeberte proměnnou prostředí ze souboru *Web. config* .</span><span class="sxs-lookup"><span data-stu-id="7634a-460">Remove the environment variable from the *web.config* file after troubleshooting.</span></span> <span data-ttu-id="7634a-461">Informace o nastavení proměnných prostředí v *souboru Web. config*naleznete v tématu [environmentVariables Child element of aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).</span><span class="sxs-lookup"><span data-stu-id="7634a-461">For information on setting environment variables in *web.config*, see [environmentVariables child element of aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).</span></span>

### <a name="obtain-data-from-an-app"></a><span data-ttu-id="7634a-462">Získání dat z aplikace</span><span class="sxs-lookup"><span data-stu-id="7634a-462">Obtain data from an app</span></span>

<span data-ttu-id="7634a-463">Pokud aplikace dokáže reagovat na žádosti, získat požadavek, připojení a další data z aplikace pomocí vloženého middlewaru terminálu.</span><span class="sxs-lookup"><span data-stu-id="7634a-463">If an app is capable of responding to requests, obtain request, connection, and additional data from the app using terminal inline middleware.</span></span> <span data-ttu-id="7634a-464">Další informace a ukázka kódu naleznete v tématu <xref:test/troubleshoot#obtain-data-from-an-app>.</span><span class="sxs-lookup"><span data-stu-id="7634a-464">For more information and sample code, see <xref:test/troubleshoot#obtain-data-from-an-app>.</span></span>

### <a name="slow-or-hanging-app-iis"></a><span data-ttu-id="7634a-465">Pomalá nebo zavěšená aplikace (IIS)</span><span class="sxs-lookup"><span data-stu-id="7634a-465">Slow or hanging app (IIS)</span></span>

<span data-ttu-id="7634a-466">*Výpis stavu* systému je snímek paměti systému a může vám pomůže určit příčinu selhání aplikace, selhání při spuštění nebo pomalé aplikace.</span><span class="sxs-lookup"><span data-stu-id="7634a-466">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="7634a-467">Aplikace selže nebo dojde k výjimce.</span><span class="sxs-lookup"><span data-stu-id="7634a-467">App crashes or encounters an exception</span></span>

<span data-ttu-id="7634a-468">Získat a analyzovat výpis z [zasílání zpráv o chybách systému Windows (WER)](/windows/desktop/wer/windows-error-reporting):</span><span class="sxs-lookup"><span data-stu-id="7634a-468">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="7634a-469">Vytvořte složku uchovávající soubory s výpisem stavu `c:\dumps`systému.</span><span class="sxs-lookup"><span data-stu-id="7634a-469">Create a folder to hold crash dump files at `c:\dumps`.</span></span> <span data-ttu-id="7634a-470">Fond aplikací musí mít ke složce přístup pro zápis.</span><span class="sxs-lookup"><span data-stu-id="7634a-470">The app pool must have write access to the folder.</span></span>
1. <span data-ttu-id="7634a-471">Spusťte [skript prostředí PowerShell pro EnableDumps](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1):</span><span class="sxs-lookup"><span data-stu-id="7634a-471">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1):</span></span>
   * <span data-ttu-id="7634a-472">Pokud aplikace používá [model hostování v rámci procesu](xref:host-and-deploy/iis/index#in-process-hosting-model), spusťte skript pro *W3wp. exe*:</span><span class="sxs-lookup"><span data-stu-id="7634a-472">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\EnableDumps w3wp.exe c:\dumps
     ```

   * <span data-ttu-id="7634a-473">Pokud aplikace používá [model hostování mimo proces](xref:host-and-deploy/iis/index#out-of-process-hosting-model), spusťte skript pro příkaz *dotnet. exe*:</span><span class="sxs-lookup"><span data-stu-id="7634a-473">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\EnableDumps dotnet.exe c:\dumps
     ```

1. <span data-ttu-id="7634a-474">Spusťte aplikaci za podmínek, které způsobí, že dojde k chybě.</span><span class="sxs-lookup"><span data-stu-id="7634a-474">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="7634a-475">Po chybě spusťte [skript PowerShellu DisableDumps](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1):</span><span class="sxs-lookup"><span data-stu-id="7634a-475">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1):</span></span>
   * <span data-ttu-id="7634a-476">Pokud aplikace používá [model hostování v rámci procesu](xref:host-and-deploy/iis/index#in-process-hosting-model), spusťte skript pro *W3wp. exe*:</span><span class="sxs-lookup"><span data-stu-id="7634a-476">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\DisableDumps w3wp.exe
     ```

   * <span data-ttu-id="7634a-477">Pokud aplikace používá [model hostování mimo proces](xref:host-and-deploy/iis/index#out-of-process-hosting-model), spusťte skript pro příkaz *dotnet. exe*:</span><span class="sxs-lookup"><span data-stu-id="7634a-477">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\DisableDumps dotnet.exe
     ```

<span data-ttu-id="7634a-478">Po selhání aplikace a dokončení shromažďování výpisu je možné aplikaci ukončit normálně.</span><span class="sxs-lookup"><span data-stu-id="7634a-478">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="7634a-479">Skript PowerShellu nakonfiguruje WER a shromáždí až pět výpisů paměti pro každou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="7634a-479">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="7634a-480">Výpisy stavu systému můžou zabírat velké množství místa na disku (každý až několik gigabajtů).</span><span class="sxs-lookup"><span data-stu-id="7634a-480">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="7634a-481">Aplikace přestane reagovat, dojde k chybě při spuštění nebo se spustí normálně.</span><span class="sxs-lookup"><span data-stu-id="7634a-481">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="7634a-482">Když *aplikace přestane reagovat (zastaví* se, ale nejedná se o chybu), selže během spuštění nebo se spustí normálně, podívejte [se na soubory výpisu paměti v uživatelském režimu: zvolením nejlepšího](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) nástroje vyberte vhodný nástroj pro vytváření výpisu.</span><span class="sxs-lookup"><span data-stu-id="7634a-482">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="7634a-483">Analýza výpisu paměti</span><span class="sxs-lookup"><span data-stu-id="7634a-483">Analyze the dump</span></span>

<span data-ttu-id="7634a-484">Výpis paměti lze analyzovat pomocí několika přístupů.</span><span class="sxs-lookup"><span data-stu-id="7634a-484">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="7634a-485">Další informace najdete v tématu [Analýza souboru s výpisem stavu v uživatelském režimu](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span><span class="sxs-lookup"><span data-stu-id="7634a-485">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="clear-package-caches"></a><span data-ttu-id="7634a-486">Vymazat mezipaměti balíčků</span><span class="sxs-lookup"><span data-stu-id="7634a-486">Clear package caches</span></span>

<span data-ttu-id="7634a-487">Funkční aplikace může po upgradu .NET Core SDK ve vývojovém počítači nebo změně verzí balíčku v rámci aplikace selhat okamžitě.</span><span class="sxs-lookup"><span data-stu-id="7634a-487">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="7634a-488">V některých případech můžou nesouvislé balíčky při provádění hlavních upgradů poškodit aplikaci.</span><span class="sxs-lookup"><span data-stu-id="7634a-488">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="7634a-489">Většinu těchto problémů můžete vyřešit pomocí následujících pokynů:</span><span class="sxs-lookup"><span data-stu-id="7634a-489">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="7634a-490">Odstraňte složky *bin* a *obj* .</span><span class="sxs-lookup"><span data-stu-id="7634a-490">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="7634a-491">Pomocí příkazu [dotnet All--Clear](/dotnet/core/tools/dotnet-nuget-locals) z příkazového prostředí vymažte mezipaměť balíčku.</span><span class="sxs-lookup"><span data-stu-id="7634a-491">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="7634a-492">Mazání mezipamětí balíčků lze také provést pomocí nástroje [NuGet. exe](https://www.nuget.org/downloads) a provedením příkazu `nuget locals all -clear`.</span><span class="sxs-lookup"><span data-stu-id="7634a-492">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="7634a-493">*NuGet. exe* není sada instalovaná instalace s desktopovým operačním systémem Windows a musí se získat samostatně z [webu NuGet](https://www.nuget.org/downloads).</span><span class="sxs-lookup"><span data-stu-id="7634a-493">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="7634a-494">Obnovte a znovu sestavte projekt.</span><span class="sxs-lookup"><span data-stu-id="7634a-494">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="7634a-495">Před opětovným nasazením aplikace odstraňte všechny soubory ve složce pro nasazení na serveru.</span><span class="sxs-lookup"><span data-stu-id="7634a-495">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7634a-496">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="7634a-496">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:fundamentals/error-handling>
* <xref:host-and-deploy/aspnet-core-module>

### <a name="azure-documentation"></a><span data-ttu-id="7634a-497">Dokumentace k Azure</span><span class="sxs-lookup"><span data-stu-id="7634a-497">Azure documentation</span></span>

* [<span data-ttu-id="7634a-498">Application Insights pro ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7634a-498">Application Insights for ASP.NET Core</span></span>](/azure/application-insights/app-insights-asp-net-core)
* [<span data-ttu-id="7634a-499">Část vzdálené ladění webových aplikací řešení potíží s webovou aplikací v Azure App Service pomocí sady Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7634a-499">Remote debugging web apps section of Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug)
* [<span data-ttu-id="7634a-500">Přehled diagnostiky Azure App Service</span><span class="sxs-lookup"><span data-stu-id="7634a-500">Azure App Service diagnostics overview</span></span>](/azure/app-service/app-service-diagnostics)
* [<span data-ttu-id="7634a-501">Postup: Monitorování aplikací ve službě Azure App Service</span><span class="sxs-lookup"><span data-stu-id="7634a-501">How to: Monitor Apps in Azure App Service</span></span>](/azure/app-service/web-sites-monitor)
* [<span data-ttu-id="7634a-502">Řešení potíží s webovou aplikací v Azure App Service pomocí sady Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7634a-502">Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [<span data-ttu-id="7634a-503">Řešení chyb HTTP z "502 špatné brány" a "nedostupné služby 503" v Azure Web Apps</span><span class="sxs-lookup"><span data-stu-id="7634a-503">Troubleshoot HTTP errors of "502 bad gateway" and "503 service unavailable" in your Azure web apps</span></span>](/azure/app-service/app-service-web-troubleshoot-http-502-http-503)
* [<span data-ttu-id="7634a-504">Řešení potíží s výkonem pomalých webových aplikací ve službě Azure App Service</span><span class="sxs-lookup"><span data-stu-id="7634a-504">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="7634a-505">Nejčastější dotazy týkající se výkonu aplikací pro Web Apps v Azure</span><span class="sxs-lookup"><span data-stu-id="7634a-505">Application performance FAQs for Web Apps in Azure</span></span>](/azure/app-service/app-service-web-availability-performance-application-issues-faq)
* [<span data-ttu-id="7634a-506">Sandbox webové aplikace Azure (omezení spuštění App Service Runtime)</span><span class="sxs-lookup"><span data-stu-id="7634a-506">Azure Web App sandbox (App Service runtime execution limitations)</span></span>](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)
* [<span data-ttu-id="7634a-507">Azure pátek: Azure App Service diagnostické prostředí a řešení potíží (12 minut video)</span><span class="sxs-lookup"><span data-stu-id="7634a-507">Azure Friday: Azure App Service Diagnostic and Troubleshooting Experience (12-minute video)</span></span>](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)

### <a name="visual-studio-documentation"></a><span data-ttu-id="7634a-508">Dokumentace sady Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7634a-508">Visual Studio documentation</span></span>

* [<span data-ttu-id="7634a-509">Vzdálené ladění ASP.NET Core ve službě IIS v Azure v aplikaci Visual Studio 2017</span><span class="sxs-lookup"><span data-stu-id="7634a-509">Remote Debug ASP.NET Core on IIS in Azure in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-azure)
* [<span data-ttu-id="7634a-510">Vzdálené ladění ASP.NET Core na vzdáleném počítači IIS v aplikaci Visual Studio 2017</span><span class="sxs-lookup"><span data-stu-id="7634a-510">Remote Debug ASP.NET Core on a Remote IIS Computer in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-aspnet-on-a-remote-iis-computer)
* [<span data-ttu-id="7634a-511">Informace o ladění pomocí sady Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7634a-511">Learn to debug using Visual Studio</span></span>](/visualstudio/debugger/getting-started-with-the-debugger)

### <a name="visual-studio-code-documentation"></a><span data-ttu-id="7634a-512">Dokumentace k Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7634a-512">Visual Studio Code documentation</span></span>

* [<span data-ttu-id="7634a-513">Ladění pomocí Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7634a-513">Debugging with Visual Studio Code</span></span>](https://code.visualstudio.com/docs/editor/debugging)

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="7634a-514">Tento článek poskytuje informace o běžných chybách při spuštění aplikací a pokyny k diagnostice chyb při nasazení aplikace do Azure App Service nebo IIS:</span><span class="sxs-lookup"><span data-stu-id="7634a-514">This article provides information on common app startup errors and instructions on how to diagnose errors when an app is deployed to Azure App Service or IIS:</span></span>

[<span data-ttu-id="7634a-515">Chyby při spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="7634a-515">App startup errors</span></span>](#app-startup-errors)  
<span data-ttu-id="7634a-516">Vysvětluje běžné spouštění scénářů stavového kódu HTTP.</span><span class="sxs-lookup"><span data-stu-id="7634a-516">Explains common startup HTTP status code scenarios.</span></span>

[<span data-ttu-id="7634a-517">Řešení potíží na Azure App Service</span><span class="sxs-lookup"><span data-stu-id="7634a-517">Troubleshoot on Azure App Service</span></span>](#troubleshoot-on-azure-app-service)  
<span data-ttu-id="7634a-518">Poskytuje rady pro řešení potíží pro aplikace nasazené do Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="7634a-518">Provides troubleshooting advice for apps deployed to Azure App Service.</span></span>

[<span data-ttu-id="7634a-519">Odstraňování potíží ve službě IIS</span><span class="sxs-lookup"><span data-stu-id="7634a-519">Troubleshoot on IIS</span></span>](#troubleshoot-on-iis)  
<span data-ttu-id="7634a-520">Poskytuje rady pro řešení potíží pro aplikace nasazené do služby IIS nebo místně spuštěné v IIS Express.</span><span class="sxs-lookup"><span data-stu-id="7634a-520">Provides troubleshooting advice for apps deployed to IIS or running on IIS Express locally.</span></span> <span data-ttu-id="7634a-521">Pokyny platí pro nasazení systémů Windows Server a Desktop v systému Windows.</span><span class="sxs-lookup"><span data-stu-id="7634a-521">The guidance applies to both Windows Server and Windows desktop deployments.</span></span>

[<span data-ttu-id="7634a-522">Vymazat mezipaměti balíčků</span><span class="sxs-lookup"><span data-stu-id="7634a-522">Clear package caches</span></span>](#clear-package-caches)  
<span data-ttu-id="7634a-523">Vysvětluje, co dělat v případě, že při provádění podstatných upgradů nebo změně verze balíčku dojde k přerušení aplikace v nesouvislých balíčcích.</span><span class="sxs-lookup"><span data-stu-id="7634a-523">Explains what to do when incoherent packages break an app when performing major upgrades or changing package versions.</span></span>

[<span data-ttu-id="7634a-524">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="7634a-524">Additional resources</span></span>](#additional-resources)  
<span data-ttu-id="7634a-525">Seznam dalších témat pro odstraňování potíží.</span><span class="sxs-lookup"><span data-stu-id="7634a-525">Lists additional troubleshooting topics.</span></span>

## <a name="app-startup-errors"></a><span data-ttu-id="7634a-526">Chyby při spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="7634a-526">App startup errors</span></span>

<span data-ttu-id="7634a-527">V aplikaci Visual Studio je výchozím nastavením projektu ASP.NET Core [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hostování během ladění.</span><span class="sxs-lookup"><span data-stu-id="7634a-527">In Visual Studio, an ASP.NET Core project defaults to [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hosting during debugging.</span></span> <span data-ttu-id="7634a-528">*502,5 – selhání procesu* nebo *500,30 – chyba* , ke které dojde při místním ladění, se dá diagnostikovat pomocí Rady v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="7634a-528">A *502.5 - Process Failure* or a *500.30 - Start Failure* that occurs when debugging locally can be diagnosed using the advice in this topic.</span></span>

### <a name="40314-forbidden"></a><span data-ttu-id="7634a-529">403,14 zakázané</span><span class="sxs-lookup"><span data-stu-id="7634a-529">403.14 Forbidden</span></span>

<span data-ttu-id="7634a-530">Aplikaci se nepodařilo spustit.</span><span class="sxs-lookup"><span data-stu-id="7634a-530">The app fails to start.</span></span> <span data-ttu-id="7634a-531">Protokoluje se následující chyba:</span><span class="sxs-lookup"><span data-stu-id="7634a-531">The following error is logged:</span></span>

```
The Web server is configured to not list the contents of this directory.
```

<span data-ttu-id="7634a-532">K této chybě obvykle dochází v důsledku poškozeného nasazení v hostitelském systému, který zahrnuje kterýkoli z následujících scénářů:</span><span class="sxs-lookup"><span data-stu-id="7634a-532">The error is usually caused by a broken deployment on the hosting system, which includes any of the following scenarios:</span></span>

* <span data-ttu-id="7634a-533">Aplikace je nasazená do chybné složky v hostitelském systému.</span><span class="sxs-lookup"><span data-stu-id="7634a-533">The app is deployed to the wrong folder on the hosting system.</span></span>
* <span data-ttu-id="7634a-534">Procesu nasazení se nepodařilo přesunout všechny soubory a složky aplikace do složky pro nasazení v hostitelském systému.</span><span class="sxs-lookup"><span data-stu-id="7634a-534">The deployment process failed to move all of the app's files and folders to the deployment folder on the hosting system.</span></span>
* <span data-ttu-id="7634a-535">V nasazení chybí soubor *Web. config* , nebo je obsah souboru *Web. config* poškozený.</span><span class="sxs-lookup"><span data-stu-id="7634a-535">The *web.config* file is missing from the deployment, or the *web.config* file contents are malformed.</span></span>

<span data-ttu-id="7634a-536">Proveďte následující kroky:</span><span class="sxs-lookup"><span data-stu-id="7634a-536">Perform the following steps:</span></span>

1. <span data-ttu-id="7634a-537">Odstraňte všechny soubory a složky ze složky pro nasazení v hostitelském systému.</span><span class="sxs-lookup"><span data-stu-id="7634a-537">Delete all of the files and folders from the deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="7634a-538">Znovu nasaďte obsah složky pro *publikování* aplikace do hostitelského systému pomocí běžné metody nasazení, jako je například Visual Studio, PowerShell nebo ruční nasazení:</span><span class="sxs-lookup"><span data-stu-id="7634a-538">Redeploy the contents of the app's *publish* folder to the hosting system using your normal method of deployment, such as Visual Studio, PowerShell, or manual deployment:</span></span>
   * <span data-ttu-id="7634a-539">Zkontrolujte, zda je v nasazení přítomen soubor *Web. config* a zda jeho obsah je správný.</span><span class="sxs-lookup"><span data-stu-id="7634a-539">Confirm that the *web.config* file is present in the deployment and that its contents are correct.</span></span>
   * <span data-ttu-id="7634a-540">Při hostování na Azure App Service potvrďte, že je aplikace nasazená do `D:\home\site\wwwroot` složky.</span><span class="sxs-lookup"><span data-stu-id="7634a-540">When hosting on Azure App Service, confirm that the app is deployed to the `D:\home\site\wwwroot` folder.</span></span>
   * <span data-ttu-id="7634a-541">Když je aplikace hostovaná službou IIS, zkontrolujte, že je aplikace nasazená na **fyzickou cestu** služby IIS, která se zobrazuje v **základním nastavení** **Správce služby IIS**.</span><span class="sxs-lookup"><span data-stu-id="7634a-541">When the app is hosted by IIS, confirm that the app is deployed to the IIS **Physical path** shown in **IIS Manager**'s **Basic Settings**.</span></span>
1. <span data-ttu-id="7634a-542">Ověřte, zda jsou všechny soubory a složky aplikace nasazeny porovnáním nasazení v hostitelském systému s obsahem složky *publikování* projektu.</span><span class="sxs-lookup"><span data-stu-id="7634a-542">Confirm that all of the app's files and folders are deployed by comparing the deployment on the hosting system to the contents of the project's *publish* folder.</span></span>

<span data-ttu-id="7634a-543">Další informace o rozložení publikované aplikace ASP.NET Core najdete v tématu <xref:host-and-deploy/directory-structure>.</span><span class="sxs-lookup"><span data-stu-id="7634a-543">For more information on the layout of a published ASP.NET Core app, see <xref:host-and-deploy/directory-structure>.</span></span> <span data-ttu-id="7634a-544">Další informace o souboru *Web. config* naleznete v tématu <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>.</span><span class="sxs-lookup"><span data-stu-id="7634a-544">For more information on the *web.config* file, see <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>.</span></span>

### <a name="500-internal-server-error"></a><span data-ttu-id="7634a-545">500 – Interní chyba serveru</span><span class="sxs-lookup"><span data-stu-id="7634a-545">500 Internal Server Error</span></span>

<span data-ttu-id="7634a-546">Aplikace se spustí, ale chyba brání serveru v splnění žádosti.</span><span class="sxs-lookup"><span data-stu-id="7634a-546">The app starts, but an error prevents the server from fulfilling the request.</span></span>

<span data-ttu-id="7634a-547">K této chybě dochází v kódu aplikace během spuštění nebo při vytváření odpovědi.</span><span class="sxs-lookup"><span data-stu-id="7634a-547">This error occurs within the app's code during startup or while creating a response.</span></span> <span data-ttu-id="7634a-548">Odpověď možná neobsahuje žádný obsah, nebo se odpověď může zobrazit jako *500 interní chyba serveru* v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="7634a-548">The response may contain no content, or the response may appear as a *500 Internal Server Error* in the browser.</span></span> <span data-ttu-id="7634a-549">Protokol událostí aplikace obvykle uvádí, že aplikace začala normálně.</span><span class="sxs-lookup"><span data-stu-id="7634a-549">The Application Event Log usually states that the app started normally.</span></span> <span data-ttu-id="7634a-550">Z hlediska serveru je to správné.</span><span class="sxs-lookup"><span data-stu-id="7634a-550">From the server's perspective, that's correct.</span></span> <span data-ttu-id="7634a-551">Aplikace se spustila, ale nemůže vygenerovat platnou odpověď.</span><span class="sxs-lookup"><span data-stu-id="7634a-551">The app did start, but it can't generate a valid response.</span></span> <span data-ttu-id="7634a-552">Spusťte aplikaci na příkazovém řádku na serveru nebo povolte řešení potíží pomocí protokolu stdout modulu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7634a-552">Run the app at a command prompt on the server or enable the ASP.NET Core Module stdout log to troubleshoot the problem.</span></span>

### <a name="5000-in-process-handler-load-failure"></a><span data-ttu-id="7634a-553">500,0 selhání načtení obslužné rutiny v procesu</span><span class="sxs-lookup"><span data-stu-id="7634a-553">500.0 In-Process Handler Load Failure</span></span>

<span data-ttu-id="7634a-554">Pracovní proces se nezdařil.</span><span class="sxs-lookup"><span data-stu-id="7634a-554">The worker process fails.</span></span> <span data-ttu-id="7634a-555">Aplikace se nespustí.</span><span class="sxs-lookup"><span data-stu-id="7634a-555">The app doesn't start.</span></span>

<span data-ttu-id="7634a-556">[Modulu ASP.NET Core](xref:host-and-deploy/aspnet-core-module) se nepovedlo najít modul CLR .NET Core a najít obslužnou rutinu žádosti v procesu (*aspnetcorev2_inprocess. dll*).</span><span class="sxs-lookup"><span data-stu-id="7634a-556">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) fails to find the .NET Core CLR and find the in-process request handler (*aspnetcorev2_inprocess.dll*).</span></span> <span data-ttu-id="7634a-557">Ověřte, že:</span><span class="sxs-lookup"><span data-stu-id="7634a-557">Check that:</span></span>

* <span data-ttu-id="7634a-558">Aplikace cílí na balíček NuGet [Microsoft. AspNetCore. Server. IIS](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IIS) nebo na [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="7634a-558">The app targets either the [Microsoft.AspNetCore.Server.IIS](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IIS) NuGet package or the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>
* <span data-ttu-id="7634a-559">Verze ASP.NET Core sdílené architektury, na které je aplikace cílena, je nainstalována na cílovém počítači.</span><span class="sxs-lookup"><span data-stu-id="7634a-559">The version of the ASP.NET Core shared framework that the app targets is installed on the target machine.</span></span>

### <a name="5000-out-of-process-handler-load-failure"></a><span data-ttu-id="7634a-560">500,0 selhání načtení obslužné rutiny mimo proces</span><span class="sxs-lookup"><span data-stu-id="7634a-560">500.0 Out-Of-Process Handler Load Failure</span></span>

<span data-ttu-id="7634a-561">Pracovní proces se nezdařil.</span><span class="sxs-lookup"><span data-stu-id="7634a-561">The worker process fails.</span></span> <span data-ttu-id="7634a-562">Aplikace se nespustí.</span><span class="sxs-lookup"><span data-stu-id="7634a-562">The app doesn't start.</span></span>

<span data-ttu-id="7634a-563">[Modulu ASP.NET Core](xref:host-and-deploy/aspnet-core-module) se nepodařilo najít obslužnou rutinu žádosti mimo proces hostování.</span><span class="sxs-lookup"><span data-stu-id="7634a-563">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) fails to find the out-of-process hosting request handler.</span></span> <span data-ttu-id="7634a-564">Ujistěte se, že *aspnetcorev2_outofprocess. dll* se nachází v podsložce vedle *aspnetcorev2. dll*.</span><span class="sxs-lookup"><span data-stu-id="7634a-564">Make sure the *aspnetcorev2_outofprocess.dll* is present in a subfolder next to *aspnetcorev2.dll*.</span></span>

### <a name="5025-process-failure"></a><span data-ttu-id="7634a-565">502.5 – Selhání procesu</span><span class="sxs-lookup"><span data-stu-id="7634a-565">502.5 Process Failure</span></span>

<span data-ttu-id="7634a-566">Pracovní proces se nezdařil.</span><span class="sxs-lookup"><span data-stu-id="7634a-566">The worker process fails.</span></span> <span data-ttu-id="7634a-567">Aplikace se nespustí.</span><span class="sxs-lookup"><span data-stu-id="7634a-567">The app doesn't start.</span></span>

<span data-ttu-id="7634a-568">[Modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module) se pokusí spustit pracovní proces, ale jeho spuštění se nezdařilo.</span><span class="sxs-lookup"><span data-stu-id="7634a-568">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the worker process but it fails to start.</span></span> <span data-ttu-id="7634a-569">Příčinou selhání spuštění procesu může být obvykle určení z položek protokolu událostí aplikace a protokolu stdout modulu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7634a-569">The cause of a process startup failure can usually be determined from entries in the Application Event Log and the ASP.NET Core Module stdout log.</span></span>

<span data-ttu-id="7634a-570">Obvyklou chybovou podmínkou je, že aplikace je nesprávně nakonfigurovaná kvůli cílení na verzi ASP.NET Core sdílené rozhraní, které není k dispozici.</span><span class="sxs-lookup"><span data-stu-id="7634a-570">A common failure condition is the app is misconfigured due to targeting a version of the ASP.NET Core shared framework that isn't present.</span></span> <span data-ttu-id="7634a-571">Ověřte, které verze ASP.NET Core sdílené rozhraní jsou nainstalovány v cílovém počítači.</span><span class="sxs-lookup"><span data-stu-id="7634a-571">Check which versions of the ASP.NET Core shared framework are installed on the target machine.</span></span> <span data-ttu-id="7634a-572">*Sdílené rozhraní* je sada sestavení (soubory *. dll* ), které jsou nainstalovány na počítači a na které odkazuje Metapackage jako `Microsoft.AspNetCore.App`.</span><span class="sxs-lookup"><span data-stu-id="7634a-572">The *shared framework* is the set of assemblies (*.dll* files) that are installed on the machine and referenced by a metapackage such as `Microsoft.AspNetCore.App`.</span></span> <span data-ttu-id="7634a-573">Odkaz Metapackage může určovat minimální požadovanou verzi.</span><span class="sxs-lookup"><span data-stu-id="7634a-573">The metapackage reference can specify a minimum required version.</span></span> <span data-ttu-id="7634a-574">Další informace najdete v tématu [sdílené rozhraní](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span><span class="sxs-lookup"><span data-stu-id="7634a-574">For more information, see [The shared framework](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span></span>

<span data-ttu-id="7634a-575">Chybová stránka *selhání procesu 502,5* se vrátí v případě, že při selhání hostování nebo aplikace dojde k chybě pracovního procesu:</span><span class="sxs-lookup"><span data-stu-id="7634a-575">The *502.5 Process Failure* error page is returned when a hosting or app misconfiguration causes the worker process to fail:</span></span>

### <a name="failed-to-start-application-errorcode-0x800700c1"></a><span data-ttu-id="7634a-576">Nepovedlo se spustit aplikaci (ErrorCode ' 0x800700C1 ')</span><span class="sxs-lookup"><span data-stu-id="7634a-576">Failed to start application (ErrorCode '0x800700c1')</span></span>

```
EventID: 1010
Source: IIS AspNetCore Module V2
Failed to start application '/LM/W3SVC/6/ROOT/', ErrorCode '0x800700c1'.
```

<span data-ttu-id="7634a-577">Aplikaci se nepovedlo spustit, protože se nepovedlo načíst sestavení aplikace (*. dll*).</span><span class="sxs-lookup"><span data-stu-id="7634a-577">The app failed to start because the app's assembly (*.dll*) couldn't be loaded.</span></span>

<span data-ttu-id="7634a-578">K této chybě dochází, pokud dojde k neshodě bitová verze mezi publikovanou aplikací a procesem W3wp/IISExpress.</span><span class="sxs-lookup"><span data-stu-id="7634a-578">This error occurs when there's a bitness mismatch between the published app and the w3wp/iisexpress process.</span></span>

<span data-ttu-id="7634a-579">Zkontrolujte, jestli je správně nastavené nastavení 32 fondu aplikací:</span><span class="sxs-lookup"><span data-stu-id="7634a-579">Confirm that the app pool's 32-bit setting is correct:</span></span>

1. <span data-ttu-id="7634a-580">Vyberte fond aplikací v **fondech aplikací**Správce služby IIS.</span><span class="sxs-lookup"><span data-stu-id="7634a-580">Select the app pool in IIS Manager's **Application Pools**.</span></span>
1. <span data-ttu-id="7634a-581">V části **Upravit fond aplikací** na panelu **Akce** vyberte **Upřesnit nastavení** .</span><span class="sxs-lookup"><span data-stu-id="7634a-581">Select **Advanced Settings** under **Edit Application Pool** in the **Actions** panel.</span></span>
1. <span data-ttu-id="7634a-582">Nastavte **možnost povolit 32 – bitové aplikace**:</span><span class="sxs-lookup"><span data-stu-id="7634a-582">Set **Enable 32-Bit Applications**:</span></span>
   * <span data-ttu-id="7634a-583">Pokud nasazujete 32 (x86) aplikaci, nastavte hodnotu na `True`.</span><span class="sxs-lookup"><span data-stu-id="7634a-583">If deploying a 32-bit (x86) app, set the value to `True`.</span></span>
   * <span data-ttu-id="7634a-584">Pokud nasazujete 64 aplikaci (x64), nastavte hodnotu na `False`.</span><span class="sxs-lookup"><span data-stu-id="7634a-584">If deploying a 64-bit (x64) app, set the value to `False`.</span></span>

<span data-ttu-id="7634a-585">Ověřte, že mezi vlastností `<Platform>` MSBuild v souboru projektu a publikovaným bitová verzeem aplikace nedochází ke konfliktu.</span><span class="sxs-lookup"><span data-stu-id="7634a-585">Confirm that there isn't a conflict between a `<Platform>` MSBuild property in the project file and the published bitness of the app.</span></span>

### <a name="connection-reset"></a><span data-ttu-id="7634a-586">Resetování připojení</span><span class="sxs-lookup"><span data-stu-id="7634a-586">Connection reset</span></span>

<span data-ttu-id="7634a-587">Pokud dojde k chybě po odeslání hlaviček, je příliš pozdě pro server, který odešle **500 interní chybu serveru** , když dojde k chybě.</span><span class="sxs-lookup"><span data-stu-id="7634a-587">If an error occurs after the headers are sent, it's too late for the server to send a **500 Internal Server Error** when an error occurs.</span></span> <span data-ttu-id="7634a-588">K tomu často dochází, když dojde k chybě během serializace složitých objektů pro odpověď.</span><span class="sxs-lookup"><span data-stu-id="7634a-588">This often happens when an error occurs during the serialization of complex objects for a response.</span></span> <span data-ttu-id="7634a-589">Tento typ chyby se zobrazí jako chyba *resetování připojení* na klientovi.</span><span class="sxs-lookup"><span data-stu-id="7634a-589">This type of error appears as a *connection reset* error on the client.</span></span> <span data-ttu-id="7634a-590">[Protokolování aplikace](xref:fundamentals/logging/index) může pomoct řešit tyto typy chyb.</span><span class="sxs-lookup"><span data-stu-id="7634a-590">[Application logging](xref:fundamentals/logging/index) can help troubleshoot these types of errors.</span></span>

### <a name="default-startup-limits"></a><span data-ttu-id="7634a-591">Výchozí omezení spouštění</span><span class="sxs-lookup"><span data-stu-id="7634a-591">Default startup limits</span></span>

<span data-ttu-id="7634a-592">[ASP.NET Core modul](xref:host-and-deploy/aspnet-core-module) je nakonfigurovaný s výchozí *startupTimeLimitou* 120 sekund.</span><span class="sxs-lookup"><span data-stu-id="7634a-592">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) is configured with a default *startupTimeLimit* of 120 seconds.</span></span> <span data-ttu-id="7634a-593">Pokud necháte výchozí hodnotu, aplikace může trvat až dvě minuty, než se modul zaznamená selhání procesu.</span><span class="sxs-lookup"><span data-stu-id="7634a-593">When left at the default value, an app may take up to two minutes to start before the module logs a process failure.</span></span> <span data-ttu-id="7634a-594">Informace o konfiguraci modulu naleznete v tématu [atributy elementu aspNetCore](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span><span class="sxs-lookup"><span data-stu-id="7634a-594">For information on configuring the module, see [Attributes of the aspNetCore element](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span></span>

## <a name="troubleshoot-on-azure-app-service"></a><span data-ttu-id="7634a-595">Řešení potíží na Azure App Service</span><span class="sxs-lookup"><span data-stu-id="7634a-595">Troubleshoot on Azure App Service</span></span>

[!INCLUDE [Azure App Service Preview Notice](~/includes/azure-apps-preview-notice.md)]

### <a name="application-event-log-azure-app-service"></a><span data-ttu-id="7634a-596">Protokol událostí aplikace (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="7634a-596">Application Event Log (Azure App Service)</span></span>

<span data-ttu-id="7634a-597">Chcete-li získat přístup k protokolu událostí aplikace, použijte okno **Diagnostika a řešení problémů** v Azure Portal:</span><span class="sxs-lookup"><span data-stu-id="7634a-597">To access the Application Event Log, use the **Diagnose and solve problems** blade in the Azure portal:</span></span>

1. <span data-ttu-id="7634a-598">V Azure Portal otevřete aplikaci v **App Services**.</span><span class="sxs-lookup"><span data-stu-id="7634a-598">In the Azure portal, open the app in **App Services**.</span></span>
1. <span data-ttu-id="7634a-599">Vyberte **Diagnostikovat a řešit problémy**.</span><span class="sxs-lookup"><span data-stu-id="7634a-599">Select **Diagnose and solve problems**.</span></span>
1. <span data-ttu-id="7634a-600">Vyberte **diagnostické nástroje** záhlaví.</span><span class="sxs-lookup"><span data-stu-id="7634a-600">Select the **Diagnostic Tools** heading.</span></span>
1. <span data-ttu-id="7634a-601">V nabídce **nástroje podpory**vyberte tlačítko **události aplikace** .</span><span class="sxs-lookup"><span data-stu-id="7634a-601">Under **Support Tools**, select the **Application Events** button.</span></span>
1. <span data-ttu-id="7634a-602">Projděte si nejnovější chybu, kterou poskytla položka *IIS AspNetCoreModule* nebo *IIS AspNetCoreModule v2* ve **zdrojovém** sloupci.</span><span class="sxs-lookup"><span data-stu-id="7634a-602">Examine the latest error provided by the *IIS AspNetCoreModule* or *IIS AspNetCoreModule V2* entry in the **Source** column.</span></span>

<span data-ttu-id="7634a-603">Alternativou k použití okna **diagnostikovat a řešit problémy** je kontrola souboru protokolu událostí aplikace přímo pomocí [Kudu](https://github.com/projectkudu/kudu/wiki):</span><span class="sxs-lookup"><span data-stu-id="7634a-603">An alternative to using the **Diagnose and solve problems** blade is to examine the Application Event Log file directly using [Kudu](https://github.com/projectkudu/kudu/wiki):</span></span>

1. <span data-ttu-id="7634a-604">Otevřete **Rozšířené nástroje** v oblasti **vývojové nástroje** .</span><span class="sxs-lookup"><span data-stu-id="7634a-604">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="7634a-605">Vyberte tlačítko \*\*Přejít&rarr; \*\* .</span><span class="sxs-lookup"><span data-stu-id="7634a-605">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="7634a-606">Konzola Kudu se otevře v novém okně nebo záložce prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="7634a-606">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="7634a-607">Pomocí navigačního panelu v horní části stránky otevřete **konzolu ladění** a vyberte **cmd**.</span><span class="sxs-lookup"><span data-stu-id="7634a-607">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="7634a-608">Otevřete složku **soubory protokolů** .</span><span class="sxs-lookup"><span data-stu-id="7634a-608">Open the **LogFiles** folder.</span></span>
1. <span data-ttu-id="7634a-609">Vyberte ikonu tužky vedle souboru *EventLog. XML* .</span><span class="sxs-lookup"><span data-stu-id="7634a-609">Select the pencil icon next to the *eventlog.xml* file.</span></span>
1. <span data-ttu-id="7634a-610">Projděte si protokol.</span><span class="sxs-lookup"><span data-stu-id="7634a-610">Examine the log.</span></span> <span data-ttu-id="7634a-611">Posuňte se do dolní části protokolu, abyste viděli nejaktuálnější události.</span><span class="sxs-lookup"><span data-stu-id="7634a-611">Scroll to the bottom of the log to see the most recent events.</span></span>

### <a name="run-the-app-in-the-kudu-console"></a><span data-ttu-id="7634a-612">Spuštění aplikace v konzole Kudu</span><span class="sxs-lookup"><span data-stu-id="7634a-612">Run the app in the Kudu console</span></span>

<span data-ttu-id="7634a-613">Mnoho chyb při spuštění nevytváří užitečné informace v protokolu událostí aplikace.</span><span class="sxs-lookup"><span data-stu-id="7634a-613">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="7634a-614">Tuto chybu můžete zjistit spuštěním aplikace v konzole vzdáleného spuštění [Kudu](https://github.com/projectkudu/kudu/wiki) :</span><span class="sxs-lookup"><span data-stu-id="7634a-614">You can run the app in the [Kudu](https://github.com/projectkudu/kudu/wiki) Remote Execution Console to discover the error:</span></span>

1. <span data-ttu-id="7634a-615">Otevřete **Rozšířené nástroje** v oblasti **vývojové nástroje** .</span><span class="sxs-lookup"><span data-stu-id="7634a-615">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="7634a-616">Vyberte tlačítko \*\*Přejít&rarr; \*\* .</span><span class="sxs-lookup"><span data-stu-id="7634a-616">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="7634a-617">Konzola Kudu se otevře v novém okně nebo záložce prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="7634a-617">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="7634a-618">Pomocí navigačního panelu v horní části stránky otevřete **konzolu ladění** a vyberte **cmd**.</span><span class="sxs-lookup"><span data-stu-id="7634a-618">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>

#### <a name="test-a-32-bit-x86-app"></a><span data-ttu-id="7634a-619">Testování 32 (x86) aplikace</span><span class="sxs-lookup"><span data-stu-id="7634a-619">Test a 32-bit (x86) app</span></span>

<span data-ttu-id="7634a-620">**Aktuální verze**</span><span class="sxs-lookup"><span data-stu-id="7634a-620">**Current release**</span></span>

1. `cd d:\home\site\wwwroot`
1. <span data-ttu-id="7634a-621">Spusťte aplikaci:</span><span class="sxs-lookup"><span data-stu-id="7634a-621">Run the app:</span></span>
   * <span data-ttu-id="7634a-622">Pokud aplikace je [nasazení závislé na rozhraní](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span><span class="sxs-lookup"><span data-stu-id="7634a-622">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

     ```dotnetcli
     dotnet .\{ASSEMBLY NAME}.dll
     ```

   * <span data-ttu-id="7634a-623">Pokud je aplikace samostatná, [nasazení](/dotnet/core/deploying/#self-contained-deployments-scd):</span><span class="sxs-lookup"><span data-stu-id="7634a-623">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

     ```console
     {ASSEMBLY NAME}.exe
     ```

<span data-ttu-id="7634a-624">Výstup konzoly z aplikace, v němž se zobrazují všechny chyby, je předán do konzoly Kudu.</span><span class="sxs-lookup"><span data-stu-id="7634a-624">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="7634a-625">**Nasazení závislé na architektuře spuštěné ve verzi Preview**</span><span class="sxs-lookup"><span data-stu-id="7634a-625">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="7634a-626">*Vyžaduje instalaci rozšíření webu ASP.NET Core {VERSION} (x86) Runtime.*</span><span class="sxs-lookup"><span data-stu-id="7634a-626">*Requires installing the ASP.NET Core {VERSION} (x86) Runtime site extension.*</span></span>

1. <span data-ttu-id="7634a-627">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32`(`{X.Y}` je verze modulu runtime)</span><span class="sxs-lookup"><span data-stu-id="7634a-627">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="7634a-628">Spusťte aplikaci: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="7634a-628">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="7634a-629">Výstup konzoly z aplikace, v němž se zobrazují všechny chyby, je předán do konzoly Kudu.</span><span class="sxs-lookup"><span data-stu-id="7634a-629">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

#### <a name="test-a-64-bit-x64-app"></a><span data-ttu-id="7634a-630">Testování 64 aplikace (x64)</span><span class="sxs-lookup"><span data-stu-id="7634a-630">Test a 64-bit (x64) app</span></span>

<span data-ttu-id="7634a-631">**Aktuální verze**</span><span class="sxs-lookup"><span data-stu-id="7634a-631">**Current release**</span></span>

* <span data-ttu-id="7634a-632">Pokud je aplikace [nasazením závislého na rozhraní](/dotnet/core/deploying/#framework-dependent-deployments-fdd)64 (x64):</span><span class="sxs-lookup"><span data-stu-id="7634a-632">If the app is a 64-bit (x64) [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>
  1. `cd D:\Program Files\dotnet`
  1. <span data-ttu-id="7634a-633">Spusťte aplikaci: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="7634a-633">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>
* <span data-ttu-id="7634a-634">Pokud je aplikace samostatná, [nasazení](/dotnet/core/deploying/#self-contained-deployments-scd):</span><span class="sxs-lookup"><span data-stu-id="7634a-634">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>
  1. `cd D:\home\site\wwwroot`
  1. <span data-ttu-id="7634a-635">Spusťte aplikaci: `{ASSEMBLY NAME}.exe`</span><span class="sxs-lookup"><span data-stu-id="7634a-635">Run the app: `{ASSEMBLY NAME}.exe`</span></span>

<span data-ttu-id="7634a-636">Výstup konzoly z aplikace, v němž se zobrazují všechny chyby, je předán do konzoly Kudu.</span><span class="sxs-lookup"><span data-stu-id="7634a-636">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="7634a-637">**Nasazení závislé na architektuře spuštěné ve verzi Preview**</span><span class="sxs-lookup"><span data-stu-id="7634a-637">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="7634a-638">*Vyžaduje instalaci rozšíření webu ASP.NET Core {VERSION} (x64) Runtime.*</span><span class="sxs-lookup"><span data-stu-id="7634a-638">*Requires installing the ASP.NET Core {VERSION} (x64) Runtime site extension.*</span></span>

1. <span data-ttu-id="7634a-639">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64`(`{X.Y}` je verze modulu runtime)</span><span class="sxs-lookup"><span data-stu-id="7634a-639">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="7634a-640">Spusťte aplikaci: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="7634a-640">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="7634a-641">Výstup konzoly z aplikace, v němž se zobrazují všechny chyby, je předán do konzoly Kudu.</span><span class="sxs-lookup"><span data-stu-id="7634a-641">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

### <a name="aspnet-core-module-stdout-log-azure-app-service"></a><span data-ttu-id="7634a-642">Protokol stdout v modulu ASP.NET Core (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="7634a-642">ASP.NET Core Module stdout log (Azure App Service)</span></span>

<span data-ttu-id="7634a-643">Protokol stdout modulu ASP.NET Coree často zaznamenává užitečné chybové zprávy, které se nenašly v protokolu událostí aplikace.</span><span class="sxs-lookup"><span data-stu-id="7634a-643">The ASP.NET Core Module stdout log often records useful error messages not found in the Application Event Log.</span></span> <span data-ttu-id="7634a-644">Postup povolení a zobrazení protokolů stdout:</span><span class="sxs-lookup"><span data-stu-id="7634a-644">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="7634a-645">Přejděte do okna **diagnostikovat a řešit problémy** v Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="7634a-645">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="7634a-646">V části **Vybrat kategorii problému**vyberte tlačítko pro **webovou aplikaci** .</span><span class="sxs-lookup"><span data-stu-id="7634a-646">Under **SELECT PROBLEM CATEGORY**, select the **Web App Down** button.</span></span>
1. <span data-ttu-id="7634a-647">V části **navrhovaná řešení** > **povolte přesměrování protokolu stdout**a výběrem tlačítka **otevřete konzolu Kudu a upravte soubor Web. config**.</span><span class="sxs-lookup"><span data-stu-id="7634a-647">Under **Suggested Solutions** > **Enable Stdout Log Redirection**, select the button to **Open Kudu Console to edit Web.Config**.</span></span>
1. <span data-ttu-id="7634a-648">V konzole pro **diagnostiku**Kudu otevřete složky v **lokalitě** > s cestou**wwwroot**.</span><span class="sxs-lookup"><span data-stu-id="7634a-648">In the Kudu **Diagnostic Console**, open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="7634a-649">Posuňte se dolů a odhalte soubor *Web. config* v dolní části seznamu.</span><span class="sxs-lookup"><span data-stu-id="7634a-649">Scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="7634a-650">Klikněte na ikonu tužky vedle souboru *Web. config* .</span><span class="sxs-lookup"><span data-stu-id="7634a-650">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="7634a-651">Nastavte **stdoutLogEnabled** na `true` a změňte cestu **stdoutLogFile** na: `\\?\%home%\LogFiles\stdout`.</span><span class="sxs-lookup"><span data-stu-id="7634a-651">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="7634a-652">Vyberte **Uložit** a uložte aktualizovaný soubor *Web. config* .</span><span class="sxs-lookup"><span data-stu-id="7634a-652">Select **Save** to save the updated *web.config* file.</span></span>
1. <span data-ttu-id="7634a-653">Vytvořte žádost do aplikace.</span><span class="sxs-lookup"><span data-stu-id="7634a-653">Make a request to the app.</span></span>
1. <span data-ttu-id="7634a-654">Vraťte se na Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="7634a-654">Return to the Azure portal.</span></span> <span data-ttu-id="7634a-655">V oblasti **vývojové nástroje** vyberte okno **Pokročilé nástroje** .</span><span class="sxs-lookup"><span data-stu-id="7634a-655">Select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="7634a-656">Vyberte tlačítko \*\*Přejít&rarr; \*\* .</span><span class="sxs-lookup"><span data-stu-id="7634a-656">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="7634a-657">Konzola Kudu se otevře v novém okně nebo záložce prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="7634a-657">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="7634a-658">Pomocí navigačního panelu v horní části stránky otevřete **konzolu ladění** a vyberte **cmd**.</span><span class="sxs-lookup"><span data-stu-id="7634a-658">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="7634a-659">Vyberte složku **soubory protokolů** .</span><span class="sxs-lookup"><span data-stu-id="7634a-659">Select the **LogFiles** folder.</span></span>
1. <span data-ttu-id="7634a-660">Zkontrolujte **upravený** sloupec a vyberte ikonu tužky a upravte protokol stdout s nejnovějším datem úpravy.</span><span class="sxs-lookup"><span data-stu-id="7634a-660">Inspect the **Modified** column and select the pencil icon to edit the stdout log with the latest modification date.</span></span>
1. <span data-ttu-id="7634a-661">Po otevření souboru protokolu se zobrazí chyba.</span><span class="sxs-lookup"><span data-stu-id="7634a-661">When the log file opens, the error is displayed.</span></span>

<span data-ttu-id="7634a-662">Zakázat protokolování stdout při odstraňování potíží:</span><span class="sxs-lookup"><span data-stu-id="7634a-662">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="7634a-663">V **diagnostické konzole**Kudu se vraťte do cesty**wwwroot** **lokality** > , která odhalí soubor *Web. config* .</span><span class="sxs-lookup"><span data-stu-id="7634a-663">In the Kudu **Diagnostic Console**, return to the path **site** > **wwwroot** to reveal the *web.config* file.</span></span> <span data-ttu-id="7634a-664">Otevřete soubor **Web. config** znovu výběrem ikony tužky.</span><span class="sxs-lookup"><span data-stu-id="7634a-664">Open the **web.config** file again by selecting the pencil icon.</span></span>
1. <span data-ttu-id="7634a-665">Nastavte **stdoutLogEnabled** na `false`.</span><span class="sxs-lookup"><span data-stu-id="7634a-665">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="7634a-666">Vyberte **Uložit** a soubor uložte.</span><span class="sxs-lookup"><span data-stu-id="7634a-666">Select **Save** to save the file.</span></span>

<span data-ttu-id="7634a-667">Další informace naleznete v tématu <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span><span class="sxs-lookup"><span data-stu-id="7634a-667">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

> [!WARNING]
> <span data-ttu-id="7634a-668">Nepovedlo se zakázat protokol stdout, což může způsobit selhání aplikace nebo serveru.</span><span class="sxs-lookup"><span data-stu-id="7634a-668">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="7634a-669">Velikost souboru protokolu ani počet vytvořených souborů protokolu nijak neomezuje.</span><span class="sxs-lookup"><span data-stu-id="7634a-669">There's no limit on log file size or the number of log files created.</span></span> <span data-ttu-id="7634a-670">K řešení problémů se spouštěním aplikací použijte pouze protokolování STDOUT.</span><span class="sxs-lookup"><span data-stu-id="7634a-670">Only use stdout logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="7634a-671">Pro obecné protokolování v aplikaci ASP.NET Core po spuštění použijte knihovnu protokolování, která omezuje velikost souboru protokolu a otočí protokoly.</span><span class="sxs-lookup"><span data-stu-id="7634a-671">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="7634a-672">Další informace najdete v tématu [Zprostředkovatelé protokolování třetích stran](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="7634a-672">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="aspnet-core-module-debug-log-azure-app-service"></a><span data-ttu-id="7634a-673">Protokol ladění modulu ASP.NET Core (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="7634a-673">ASP.NET Core Module debug log (Azure App Service)</span></span>

<span data-ttu-id="7634a-674">Protokol ladění ASP.NET Core modulu poskytuje další, hlubší protokolování z modulu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7634a-674">The ASP.NET Core Module debug log provides additional, deeper logging from the ASP.NET Core Module.</span></span> <span data-ttu-id="7634a-675">Postup povolení a zobrazení protokolů stdout:</span><span class="sxs-lookup"><span data-stu-id="7634a-675">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="7634a-676">Pro povolení rozšířeného diagnostického protokolu proveďte jednu z následujících akcí:</span><span class="sxs-lookup"><span data-stu-id="7634a-676">To enable the enhanced diagnostic log, perform either of the following:</span></span>
   * <span data-ttu-id="7634a-677">Postupujte podle pokynů v [rozšířených diagnostických protokolech](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) a nakonfigurujte aplikaci pro rozšířené diagnostické protokolování.</span><span class="sxs-lookup"><span data-stu-id="7634a-677">Follow the instructions in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) to configure the app for an enhanced diagnostic logging.</span></span> <span data-ttu-id="7634a-678">Znovu nasaďte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="7634a-678">Redeploy the app.</span></span>
   * <span data-ttu-id="7634a-679">Pomocí konzoly `<handlerSettings>` Kudu přidejte do souboru *Web. config* živé aplikace zobrazené [Rozšířené diagnostické protokoly](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) :</span><span class="sxs-lookup"><span data-stu-id="7634a-679">Add the `<handlerSettings>` shown in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) to the live app's *web.config* file using the Kudu console:</span></span>
     1. <span data-ttu-id="7634a-680">Otevřete **Rozšířené nástroje** v oblasti **vývojové nástroje** .</span><span class="sxs-lookup"><span data-stu-id="7634a-680">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="7634a-681">Vyberte tlačítko \*\*Přejít&rarr; \*\* .</span><span class="sxs-lookup"><span data-stu-id="7634a-681">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="7634a-682">Konzola Kudu se otevře v novém okně nebo záložce prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="7634a-682">The Kudu console opens in a new browser tab or window.</span></span>
     1. <span data-ttu-id="7634a-683">Pomocí navigačního panelu v horní části stránky otevřete **konzolu ladění** a vyberte **cmd**.</span><span class="sxs-lookup"><span data-stu-id="7634a-683">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
     1. <span data-ttu-id="7634a-684">Otevřete složky na cestě **lokality** > **wwwroot**.</span><span class="sxs-lookup"><span data-stu-id="7634a-684">Open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="7634a-685">Upravte soubor *Web. config* tak, že vyberete tlačítko tužky.</span><span class="sxs-lookup"><span data-stu-id="7634a-685">Edit the *web.config* file by selecting the pencil button.</span></span> <span data-ttu-id="7634a-686">Přidejte oddíl `<handlerSettings>` , jak je znázorněno v [rozšířených diagnostických protokolech](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs).</span><span class="sxs-lookup"><span data-stu-id="7634a-686">Add the `<handlerSettings>` section as shown in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs).</span></span> <span data-ttu-id="7634a-687">Vyberte tlačítko **Uložit**.</span><span class="sxs-lookup"><span data-stu-id="7634a-687">Select the **Save** button.</span></span>
1. <span data-ttu-id="7634a-688">Otevřete **Rozšířené nástroje** v oblasti **vývojové nástroje** .</span><span class="sxs-lookup"><span data-stu-id="7634a-688">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="7634a-689">Vyberte tlačítko \*\*Přejít&rarr; \*\* .</span><span class="sxs-lookup"><span data-stu-id="7634a-689">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="7634a-690">Konzola Kudu se otevře v novém okně nebo záložce prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="7634a-690">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="7634a-691">Pomocí navigačního panelu v horní části stránky otevřete **konzolu ladění** a vyberte **cmd**.</span><span class="sxs-lookup"><span data-stu-id="7634a-691">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="7634a-692">Otevřete složky na cestě **lokality** > **wwwroot**.</span><span class="sxs-lookup"><span data-stu-id="7634a-692">Open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="7634a-693">Pokud jste nezadali cestu k souboru *aspnetcore-Debug. log* , soubor se zobrazí v seznamu.</span><span class="sxs-lookup"><span data-stu-id="7634a-693">If you didn't supply a path for the *aspnetcore-debug.log* file, the file appears in the list.</span></span> <span data-ttu-id="7634a-694">Pokud jste zadali cestu, přejděte do umístění souboru protokolu.</span><span class="sxs-lookup"><span data-stu-id="7634a-694">If you supplied a path, navigate to the location of the log file.</span></span>
1. <span data-ttu-id="7634a-695">Otevřete soubor protokolu s tlačítkem tužky vedle názvu souboru.</span><span class="sxs-lookup"><span data-stu-id="7634a-695">Open the log file with the pencil button next to the file name.</span></span>

<span data-ttu-id="7634a-696">Zakázat protokolování ladění, pokud je dokončeno odstraňování potíží:</span><span class="sxs-lookup"><span data-stu-id="7634a-696">Disable debug logging when troubleshooting is complete:</span></span>

<span data-ttu-id="7634a-697">Chcete-li zakázat rozšířený protokol ladění, proveďte jednu z následujících akcí:</span><span class="sxs-lookup"><span data-stu-id="7634a-697">To disable the enhanced debug log, perform either of the following:</span></span>

* <span data-ttu-id="7634a-698">`<handlerSettings>` Odeberte soubor *Web. config* místně a pak aplikaci znovu nasaďte.</span><span class="sxs-lookup"><span data-stu-id="7634a-698">Remove the `<handlerSettings>` from the *web.config* file locally and redeploy the app.</span></span>
* <span data-ttu-id="7634a-699">Pomocí konzoly Kudu upravte soubor *Web. config* a odeberte `<handlerSettings>` oddíl.</span><span class="sxs-lookup"><span data-stu-id="7634a-699">Use the Kudu console to edit the *web.config* file and remove the `<handlerSettings>` section.</span></span> <span data-ttu-id="7634a-700">Uložte soubor.</span><span class="sxs-lookup"><span data-stu-id="7634a-700">Save the file.</span></span>

<span data-ttu-id="7634a-701">Další informace naleznete v tématu <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span><span class="sxs-lookup"><span data-stu-id="7634a-701">For more information, see <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span></span>

> [!WARNING]
> <span data-ttu-id="7634a-702">Nepovedlo se zakázat protokol ladění, může způsobit selhání aplikace nebo serveru.</span><span class="sxs-lookup"><span data-stu-id="7634a-702">Failure to disable the debug log can lead to app or server failure.</span></span> <span data-ttu-id="7634a-703">Velikost souboru protokolu není nijak omezena.</span><span class="sxs-lookup"><span data-stu-id="7634a-703">There's no limit on log file size.</span></span> <span data-ttu-id="7634a-704">K řešení problémů se spouštěním aplikace používejte pouze protokolování ladění.</span><span class="sxs-lookup"><span data-stu-id="7634a-704">Only use debug logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="7634a-705">Pro obecné protokolování v aplikaci ASP.NET Core po spuštění použijte knihovnu protokolování, která omezuje velikost souboru protokolu a otočí protokoly.</span><span class="sxs-lookup"><span data-stu-id="7634a-705">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="7634a-706">Další informace najdete v tématu [Zprostředkovatelé protokolování třetích stran](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="7634a-706">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="slow-or-hanging-app-azure-app-service"></a><span data-ttu-id="7634a-707">Pomalá nebo zavěšená aplikace (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="7634a-707">Slow or hanging app (Azure App Service)</span></span>

<span data-ttu-id="7634a-708">Pokud aplikace reaguje pomalu nebo přestane reagovat na žádost, přečtěte si následující články:</span><span class="sxs-lookup"><span data-stu-id="7634a-708">When an app responds slowly or hangs on a request, see the following articles:</span></span>

* [<span data-ttu-id="7634a-709">Řešení potíží s výkonem pomalých webových aplikací ve službě Azure App Service</span><span class="sxs-lookup"><span data-stu-id="7634a-709">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="7634a-710">K zachycení výpisu problémů s přerušovanou výjimkou nebo problémy s výkonem ve webové aplikaci Azure použijte rozšíření pro diagnostiku zhroucení serveru.</span><span class="sxs-lookup"><span data-stu-id="7634a-710">Use Crash Diagnoser Site Extension to Capture Dump for Intermittent Exception issues or performance issues on Azure Web App</span></span>](https://blogs.msdn.microsoft.com/asiatech/2015/12/28/use-crash-diagnoser-site-extension-to-capture-dump-for-intermittent-exception-issues-or-performance-issues-on-azure-web-app/)

### <a name="monitoring-blades"></a><span data-ttu-id="7634a-711">Okna monitorování</span><span class="sxs-lookup"><span data-stu-id="7634a-711">Monitoring blades</span></span>

<span data-ttu-id="7634a-712">Okna monitorování poskytují alternativní možnosti řešení potíží pro metody popsané dříve v tématu.</span><span class="sxs-lookup"><span data-stu-id="7634a-712">Monitoring blades provide an alternative troubleshooting experience to the methods described earlier in the topic.</span></span> <span data-ttu-id="7634a-713">Pomocí těchto oken můžete diagnostikovat chyby 500-Series.</span><span class="sxs-lookup"><span data-stu-id="7634a-713">These blades can be used to diagnose 500-series errors.</span></span>

<span data-ttu-id="7634a-714">Potvrďte, že jsou nainstalovaná rozšíření ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7634a-714">Confirm that the ASP.NET Core Extensions are installed.</span></span> <span data-ttu-id="7634a-715">Pokud nejsou rozšíření nainstalována, nainstalujte je ručně:</span><span class="sxs-lookup"><span data-stu-id="7634a-715">If the extensions aren't installed, install them manually:</span></span>

1. <span data-ttu-id="7634a-716">V části okno **vývojové nástroje** vyberte okno **rozšíření** .</span><span class="sxs-lookup"><span data-stu-id="7634a-716">In the **DEVELOPMENT TOOLS** blade section, select the **Extensions** blade.</span></span>
1. <span data-ttu-id="7634a-717">V seznamu by se měla zobrazit **rozšíření ASP.NET Core** .</span><span class="sxs-lookup"><span data-stu-id="7634a-717">The **ASP.NET Core Extensions** should appear in the list.</span></span>
1. <span data-ttu-id="7634a-718">Pokud nejsou rozšíření nainstalovaná, vyberte tlačítko **Přidat** .</span><span class="sxs-lookup"><span data-stu-id="7634a-718">If the extensions aren't installed, select the **Add** button.</span></span>
1. <span data-ttu-id="7634a-719">Ze seznamu vyberte **rozšíření ASP.NET Core** .</span><span class="sxs-lookup"><span data-stu-id="7634a-719">Choose the **ASP.NET Core Extensions** from the list.</span></span>
1. <span data-ttu-id="7634a-720">Kliknutím na **OK** přijměte právní podmínky.</span><span class="sxs-lookup"><span data-stu-id="7634a-720">Select **OK** to accept the legal terms.</span></span>
1. <span data-ttu-id="7634a-721">V okně **Přidat rozšíření** vyberte **OK** .</span><span class="sxs-lookup"><span data-stu-id="7634a-721">Select **OK** on the **Add extension** blade.</span></span>
1. <span data-ttu-id="7634a-722">Informační automaticky otevíraná zpráva indikuje, že se rozšíření úspěšně nainstalovala.</span><span class="sxs-lookup"><span data-stu-id="7634a-722">An informational pop-up message indicates when the extensions are successfully installed.</span></span>

<span data-ttu-id="7634a-723">Pokud není povoleno protokolování stdout, postupujte následovně:</span><span class="sxs-lookup"><span data-stu-id="7634a-723">If stdout logging isn't enabled, follow these steps:</span></span>

1. <span data-ttu-id="7634a-724">V Azure Portal v oblasti **vývojové nástroje** vyberte okno **Pokročilé nástroje** .</span><span class="sxs-lookup"><span data-stu-id="7634a-724">In the Azure portal, select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="7634a-725">Vyberte tlačítko \*\*Přejít&rarr; \*\* .</span><span class="sxs-lookup"><span data-stu-id="7634a-725">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="7634a-726">Konzola Kudu se otevře v novém okně nebo záložce prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="7634a-726">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="7634a-727">Pomocí navigačního panelu v horní části stránky otevřete **konzolu ladění** a vyberte **cmd**.</span><span class="sxs-lookup"><span data-stu-id="7634a-727">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="7634a-728">Otevřete složky **pro cestu** > **wwwroot** a posuňte se dolů, aby se v dolní části seznamu zobrazil soubor *Web. config* .</span><span class="sxs-lookup"><span data-stu-id="7634a-728">Open the folders to the path **site** > **wwwroot** and scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="7634a-729">Klikněte na ikonu tužky vedle souboru *Web. config* .</span><span class="sxs-lookup"><span data-stu-id="7634a-729">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="7634a-730">Nastavte **stdoutLogEnabled** na `true` a změňte cestu **stdoutLogFile** na: `\\?\%home%\LogFiles\stdout`.</span><span class="sxs-lookup"><span data-stu-id="7634a-730">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="7634a-731">Vyberte **Uložit** a uložte aktualizovaný soubor *Web. config* .</span><span class="sxs-lookup"><span data-stu-id="7634a-731">Select **Save** to save the updated *web.config* file.</span></span>

<span data-ttu-id="7634a-732">Pokračovat k aktivaci protokolování diagnostiky:</span><span class="sxs-lookup"><span data-stu-id="7634a-732">Proceed to activate diagnostic logging:</span></span>

1. <span data-ttu-id="7634a-733">V Azure Portal vyberte okno **diagnostické protokoly** .</span><span class="sxs-lookup"><span data-stu-id="7634a-733">In the Azure portal, select the **Diagnostics logs** blade.</span></span>
1. <span data-ttu-id="7634a-734">Vyberte přepínač **zapnuto** pro **protokolování aplikace (systém souborů)** a **podrobné chybové zprávy**.</span><span class="sxs-lookup"><span data-stu-id="7634a-734">Select the **On** switch for **Application Logging (Filesystem)** and **Detailed error messages**.</span></span> <span data-ttu-id="7634a-735">V horní části okna vyberte tlačítko **Uložit** .</span><span class="sxs-lookup"><span data-stu-id="7634a-735">Select the **Save** button at the top of the blade.</span></span>
1. <span data-ttu-id="7634a-736">Chcete-li zahrnout trasování chybných požadavků, označované také jako protokolování událostí neúspěšných požadavků na FREB, vyberte přepínač **zapnuto** pro **trasování chybných požadavků**.</span><span class="sxs-lookup"><span data-stu-id="7634a-736">To include failed request tracing, also known as Failed Request Event Buffering (FREB) logging, select the **On** switch for **Failed request tracing**.</span></span>
1. <span data-ttu-id="7634a-737">Vyberte okno **log Stream** , které je uvedeno hned pod oknem **diagnostické protokoly** na portálu.</span><span class="sxs-lookup"><span data-stu-id="7634a-737">Select the **Log stream** blade, which is listed immediately under the **Diagnostics logs** blade in the portal.</span></span>
1. <span data-ttu-id="7634a-738">Vytvořte žádost do aplikace.</span><span class="sxs-lookup"><span data-stu-id="7634a-738">Make a request to the app.</span></span>
1. <span data-ttu-id="7634a-739">V datech streamu protokolu se označuje příčina chyby.</span><span class="sxs-lookup"><span data-stu-id="7634a-739">Within the log stream data, the cause of the error is indicated.</span></span>

<span data-ttu-id="7634a-740">Nezapomeňte zakázat protokolování stdout po dokončení řešení potíží.</span><span class="sxs-lookup"><span data-stu-id="7634a-740">Be sure to disable stdout logging when troubleshooting is complete.</span></span>

<span data-ttu-id="7634a-741">Zobrazení protokolů pro trasování chybných požadavků (protokoly FREB):</span><span class="sxs-lookup"><span data-stu-id="7634a-741">To view the failed request tracing logs (FREB logs):</span></span>

1. <span data-ttu-id="7634a-742">Přejděte do okna **diagnostikovat a řešit problémy** v Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="7634a-742">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="7634a-743">V oblasti **nástroje podpory** v postranním panelu vyberte **protokoly pro trasování chybných požadavků** .</span><span class="sxs-lookup"><span data-stu-id="7634a-743">Select **Failed Request Tracing Logs** from the **SUPPORT TOOLS** area of the sidebar.</span></span>

<span data-ttu-id="7634a-744">V [části Povolení protokolování diagnostiky pro webové aplikace v Azure App Service tématu](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) a [Nejčastější dotazy týkající se výkonu aplikace pro Web Apps v Azure: návody zapnout trasování chybných požadavků?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing) Další informace.</span><span class="sxs-lookup"><span data-stu-id="7634a-744">See [Failed request traces section of the Enable diagnostics logging for web apps in Azure App Service topic](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) and the [Application performance FAQs for Web Apps in Azure: How do I turn on failed request tracing?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing) for more information.</span></span>

<span data-ttu-id="7634a-745">Další informace najdete v tématu [Povolení protokolování diagnostiky pro webové aplikace v Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log).</span><span class="sxs-lookup"><span data-stu-id="7634a-745">For more information, see [Enable diagnostics logging for web apps in Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log).</span></span>

> [!WARNING]
> <span data-ttu-id="7634a-746">Nepovedlo se zakázat protokol stdout, což může způsobit selhání aplikace nebo serveru.</span><span class="sxs-lookup"><span data-stu-id="7634a-746">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="7634a-747">Velikost souboru protokolu ani počet vytvořených souborů protokolu nijak neomezuje.</span><span class="sxs-lookup"><span data-stu-id="7634a-747">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="7634a-748">Pro rutinu protokolování v aplikaci ASP.NET Core použijte knihovnu protokolování, která omezuje velikost souboru protokolu a otočí protokoly.</span><span class="sxs-lookup"><span data-stu-id="7634a-748">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="7634a-749">Další informace najdete v tématu [Zprostředkovatelé protokolování třetích stran](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="7634a-749">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

## <a name="troubleshoot-on-iis"></a><span data-ttu-id="7634a-750">Odstraňování potíží ve službě IIS</span><span class="sxs-lookup"><span data-stu-id="7634a-750">Troubleshoot on IIS</span></span>

### <a name="application-event-log-iis"></a><span data-ttu-id="7634a-751">Protokol událostí aplikace (IIS)</span><span class="sxs-lookup"><span data-stu-id="7634a-751">Application Event Log (IIS)</span></span>

<span data-ttu-id="7634a-752">Přístup k protokolu událostí aplikace:</span><span class="sxs-lookup"><span data-stu-id="7634a-752">Access the Application Event Log:</span></span>

1. <span data-ttu-id="7634a-753">Otevřete nabídku Start, vyhledejte *Prohlížeč událostí*a vyberte aplikaci **Prohlížeč událostí** .</span><span class="sxs-lookup"><span data-stu-id="7634a-753">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="7634a-754">V **Prohlížeč událostí**otevřete uzel **protokoly systému Windows** .</span><span class="sxs-lookup"><span data-stu-id="7634a-754">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="7634a-755">Výběrem **aplikace** otevřete protokol událostí aplikace.</span><span class="sxs-lookup"><span data-stu-id="7634a-755">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="7634a-756">Vyhledá chyby spojené s chybnou aplikací.</span><span class="sxs-lookup"><span data-stu-id="7634a-756">Search for errors associated with the failing app.</span></span> <span data-ttu-id="7634a-757">Chyby mají ve *zdrojovém* sloupci hodnotu *modulu IIS AspNetCore* nebo *modul IIS Express AspNetCore* .</span><span class="sxs-lookup"><span data-stu-id="7634a-757">Errors have a value of *IIS AspNetCore Module* or *IIS Express AspNetCore Module* in the *Source* column.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="7634a-758">Spuštění aplikace na příkazovém řádku</span><span class="sxs-lookup"><span data-stu-id="7634a-758">Run the app at a command prompt</span></span>

<span data-ttu-id="7634a-759">Mnoho chyb při spuštění nevytváří užitečné informace v protokolu událostí aplikace.</span><span class="sxs-lookup"><span data-stu-id="7634a-759">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="7634a-760">Příčinu některých chyb můžete najít spuštěním aplikace na příkazovém řádku v hostitelském systému.</span><span class="sxs-lookup"><span data-stu-id="7634a-760">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span>

#### <a name="framework-dependent-deployment"></a><span data-ttu-id="7634a-761">Nasazení závislé na rozhraní</span><span class="sxs-lookup"><span data-stu-id="7634a-761">Framework-dependent deployment</span></span>

<span data-ttu-id="7634a-762">Pokud aplikace je [nasazení závislé na rozhraní](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span><span class="sxs-lookup"><span data-stu-id="7634a-762">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

1. <span data-ttu-id="7634a-763">Na příkazovém řádku přejděte do složky pro nasazení a spusťte aplikaci spuštěním sestavení aplikace pomocí příkazu *dotnet. exe*.</span><span class="sxs-lookup"><span data-stu-id="7634a-763">At a command prompt, navigate to the deployment folder and run the app by executing the app's assembly with *dotnet.exe*.</span></span> <span data-ttu-id="7634a-764">V následujícím příkazu nahraďte název sestavení aplikace pro \<assembly_name>:. `dotnet .\<assembly_name>.dll`</span><span class="sxs-lookup"><span data-stu-id="7634a-764">In the following command, substitute the name of the app's assembly for \<assembly_name>: `dotnet .\<assembly_name>.dll`.</span></span>
1. <span data-ttu-id="7634a-765">Výstup konzoly z aplikace, který zobrazuje všechny chyby, je zapsán do okna konzoly.</span><span class="sxs-lookup"><span data-stu-id="7634a-765">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="7634a-766">Pokud dojde k chybám při podání žádosti do aplikace, vytvořte žádost na hostitele a port, kde Kestrel naslouchá.</span><span class="sxs-lookup"><span data-stu-id="7634a-766">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="7634a-767">Pomocí výchozího hostitele a příspěvku vytvořte žádost `http://localhost:5000/`.</span><span class="sxs-lookup"><span data-stu-id="7634a-767">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="7634a-768">Pokud aplikace reaguje normálně na adrese koncového bodu Kestrel, problém je pravděpodobnější v souvislosti s konfigurací hostování a méně pravděpodobným v rámci aplikace.</span><span class="sxs-lookup"><span data-stu-id="7634a-768">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

#### <a name="self-contained-deployment"></a><span data-ttu-id="7634a-769">Samostatně zahrnuté nasazení</span><span class="sxs-lookup"><span data-stu-id="7634a-769">Self-contained deployment</span></span>

<span data-ttu-id="7634a-770">Pokud je aplikace samostatná, [nasazení](/dotnet/core/deploying/#self-contained-deployments-scd):</span><span class="sxs-lookup"><span data-stu-id="7634a-770">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

1. <span data-ttu-id="7634a-771">Na příkazovém řádku přejděte do složky pro nasazení a spusťte spustitelný soubor aplikace.</span><span class="sxs-lookup"><span data-stu-id="7634a-771">At a command prompt, navigate to the deployment folder and run the app's executable.</span></span> <span data-ttu-id="7634a-772">V následujícím příkazu nahraďte název sestavení aplikace pro \<assembly_name>:. `<assembly_name>.exe`</span><span class="sxs-lookup"><span data-stu-id="7634a-772">In the following command, substitute the name of the app's assembly for \<assembly_name>: `<assembly_name>.exe`.</span></span>
1. <span data-ttu-id="7634a-773">Výstup konzoly z aplikace, který zobrazuje všechny chyby, je zapsán do okna konzoly.</span><span class="sxs-lookup"><span data-stu-id="7634a-773">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="7634a-774">Pokud dojde k chybám při podání žádosti do aplikace, vytvořte žádost na hostitele a port, kde Kestrel naslouchá.</span><span class="sxs-lookup"><span data-stu-id="7634a-774">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="7634a-775">Pomocí výchozího hostitele a příspěvku vytvořte žádost `http://localhost:5000/`.</span><span class="sxs-lookup"><span data-stu-id="7634a-775">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="7634a-776">Pokud aplikace reaguje normálně na adrese koncového bodu Kestrel, problém je pravděpodobnější v souvislosti s konfigurací hostování a méně pravděpodobným v rámci aplikace.</span><span class="sxs-lookup"><span data-stu-id="7634a-776">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

### <a name="aspnet-core-module-stdout-log-iis"></a><span data-ttu-id="7634a-777">Protokol stdout v modulu ASP.NET Core (IIS)</span><span class="sxs-lookup"><span data-stu-id="7634a-777">ASP.NET Core Module stdout log (IIS)</span></span>

<span data-ttu-id="7634a-778">Postup povolení a zobrazení protokolů stdout:</span><span class="sxs-lookup"><span data-stu-id="7634a-778">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="7634a-779">Přejděte do složky pro nasazení webu v hostitelském systému.</span><span class="sxs-lookup"><span data-stu-id="7634a-779">Navigate to the site's deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="7634a-780">Pokud složka *logs* není k dispozici, vytvořte složku.</span><span class="sxs-lookup"><span data-stu-id="7634a-780">If the *logs* folder isn't present, create the folder.</span></span> <span data-ttu-id="7634a-781">Pokyny k tomu, jak povolit MSBuild pro vytvoření složky *logs* v nasazení automaticky, najdete v tématu [Struktura adresáře](xref:host-and-deploy/directory-structure) .</span><span class="sxs-lookup"><span data-stu-id="7634a-781">For instructions on how to enable MSBuild to create the *logs* folder in the deployment automatically, see the [Directory structure](xref:host-and-deploy/directory-structure) topic.</span></span>
1. <span data-ttu-id="7634a-782">Upravte soubor *Web. config* .</span><span class="sxs-lookup"><span data-stu-id="7634a-782">Edit the *web.config* file.</span></span> <span data-ttu-id="7634a-783">Nastavte **stdoutLogEnabled** na `true` a změňte cestu **stdoutLogFile** tak, aby odkazovala na složku *logs* (například `.\logs\stdout`).</span><span class="sxs-lookup"><span data-stu-id="7634a-783">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to point to the *logs* folder (for example, `.\logs\stdout`).</span></span> <span data-ttu-id="7634a-784">`stdout`v cestě je předpona názvu souboru protokolu.</span><span class="sxs-lookup"><span data-stu-id="7634a-784">`stdout` in the path is the log file name prefix.</span></span> <span data-ttu-id="7634a-785">Časové razítko, ID procesu a Přípona souboru se automaticky přidají při vytvoření protokolu.</span><span class="sxs-lookup"><span data-stu-id="7634a-785">A timestamp, process id, and file extension are added automatically when the log is created.</span></span> <span data-ttu-id="7634a-786">Jako `stdout` Předpona názvu souboru se používá typický soubor protokolu s názvem *stdout_20180205184032_5412. log*.</span><span class="sxs-lookup"><span data-stu-id="7634a-786">Using `stdout` as the file name prefix, a typical log file is named *stdout_20180205184032_5412.log*.</span></span>
1. <span data-ttu-id="7634a-787">Zajistěte, aby identita fondu aplikací měla oprávnění k zápisu do složky *logs* .</span><span class="sxs-lookup"><span data-stu-id="7634a-787">Ensure your application pool's identity has write permissions to the *logs* folder.</span></span>
1. <span data-ttu-id="7634a-788">Uložte aktualizovaný soubor *Web. config* .</span><span class="sxs-lookup"><span data-stu-id="7634a-788">Save the updated *web.config* file.</span></span>
1. <span data-ttu-id="7634a-789">Vytvořte žádost do aplikace.</span><span class="sxs-lookup"><span data-stu-id="7634a-789">Make a request to the app.</span></span>
1. <span data-ttu-id="7634a-790">Přejděte do složky *logs* .</span><span class="sxs-lookup"><span data-stu-id="7634a-790">Navigate to the *logs* folder.</span></span> <span data-ttu-id="7634a-791">Vyhledejte a otevřete nejnovější protokol STDOUT.</span><span class="sxs-lookup"><span data-stu-id="7634a-791">Find and open the most recent stdout log.</span></span>
1. <span data-ttu-id="7634a-792">Prostudujte si protokol o chybách.</span><span class="sxs-lookup"><span data-stu-id="7634a-792">Study the log for errors.</span></span>

<span data-ttu-id="7634a-793">Zakázat protokolování stdout při odstraňování potíží:</span><span class="sxs-lookup"><span data-stu-id="7634a-793">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="7634a-794">Upravte soubor *Web. config* .</span><span class="sxs-lookup"><span data-stu-id="7634a-794">Edit the *web.config* file.</span></span>
1. <span data-ttu-id="7634a-795">Nastavte **stdoutLogEnabled** na `false`.</span><span class="sxs-lookup"><span data-stu-id="7634a-795">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="7634a-796">Uložte soubor.</span><span class="sxs-lookup"><span data-stu-id="7634a-796">Save the file.</span></span>

<span data-ttu-id="7634a-797">Další informace naleznete v tématu <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span><span class="sxs-lookup"><span data-stu-id="7634a-797">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

> [!WARNING]
> <span data-ttu-id="7634a-798">Nepovedlo se zakázat protokol stdout, což může způsobit selhání aplikace nebo serveru.</span><span class="sxs-lookup"><span data-stu-id="7634a-798">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="7634a-799">Velikost souboru protokolu ani počet vytvořených souborů protokolu nijak neomezuje.</span><span class="sxs-lookup"><span data-stu-id="7634a-799">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="7634a-800">Pro rutinu protokolování v aplikaci ASP.NET Core použijte knihovnu protokolování, která omezuje velikost souboru protokolu a otočí protokoly.</span><span class="sxs-lookup"><span data-stu-id="7634a-800">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="7634a-801">Další informace najdete v tématu [Zprostředkovatelé protokolování třetích stran](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="7634a-801">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="aspnet-core-module-debug-log-iis"></a><span data-ttu-id="7634a-802">Protokol ladění modulu ASP.NET Core (IIS)</span><span class="sxs-lookup"><span data-stu-id="7634a-802">ASP.NET Core Module debug log (IIS)</span></span>

<span data-ttu-id="7634a-803">Přidejte následující nastavení obslužné rutiny do souboru *Web. config* aplikace, aby bylo možné povolit protokol ladění ASP.NET Core modulu:</span><span class="sxs-lookup"><span data-stu-id="7634a-803">Add the following handler settings to the app's *web.config* file to enable ASP.NET Core Module debug log:</span></span>

```xml
<aspNetCore ...>
  <handlerSettings>
    <handlerSetting name="debugLevel" value="file" />
    <handlerSetting name="debugFile" value="c:\temp\ancm.log" />
  </handlerSettings>
</aspNetCore>
```

<span data-ttu-id="7634a-804">Potvrďte, že zadaná cesta k protokolu existuje a že má identita fondu aplikací oprávnění k zápisu do tohoto umístění.</span><span class="sxs-lookup"><span data-stu-id="7634a-804">Confirm that the path specified for the log exists and that the app pool's identity has write permissions to the location.</span></span>

<span data-ttu-id="7634a-805">Další informace naleznete v tématu <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span><span class="sxs-lookup"><span data-stu-id="7634a-805">For more information, see <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span></span>

### <a name="enable-the-developer-exception-page"></a><span data-ttu-id="7634a-806">Povolit stránku výjimky pro vývojáře</span><span class="sxs-lookup"><span data-stu-id="7634a-806">Enable the Developer Exception Page</span></span>

<span data-ttu-id="7634a-807">`ASPNETCORE_ENVIRONMENT` [Proměnnou prostředí lze přidat do souboru Web. config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) , aby bylo možné aplikaci spustit ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="7634a-807">The `ASPNETCORE_ENVIRONMENT` [environment variable can be added to web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) to run the app in the Development environment.</span></span> <span data-ttu-id="7634a-808">Pokud se prostředí nepřepisuje při spuštění `UseEnvironment` aplikace v Tvůrci hostitele, nastavení proměnné prostředí umožní, aby se při spuštění aplikace zobrazila stránka s [výjimkou vývojářů](xref:fundamentals/error-handling) .</span><span class="sxs-lookup"><span data-stu-id="7634a-808">As long as the environment isn't overridden in app startup by `UseEnvironment` on the host builder, setting the environment variable allows the [Developer Exception Page](xref:fundamentals/error-handling) to appear when the app is run.</span></span>

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

<span data-ttu-id="7634a-809">Nastavení proměnné prostředí pro `ASPNETCORE_ENVIRONMENT` se doporučuje jenom pro použití na pracovních a testovacích serverech, které nejsou přístupné pro Internet.</span><span class="sxs-lookup"><span data-stu-id="7634a-809">Setting the environment variable for `ASPNETCORE_ENVIRONMENT` is only recommended for use on staging and testing servers that aren't exposed to the Internet.</span></span> <span data-ttu-id="7634a-810">Po vyřešení potíží odeberte proměnnou prostředí ze souboru *Web. config* .</span><span class="sxs-lookup"><span data-stu-id="7634a-810">Remove the environment variable from the *web.config* file after troubleshooting.</span></span> <span data-ttu-id="7634a-811">Informace o nastavení proměnných prostředí v *souboru Web. config*naleznete v tématu [environmentVariables Child element of aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).</span><span class="sxs-lookup"><span data-stu-id="7634a-811">For information on setting environment variables in *web.config*, see [environmentVariables child element of aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).</span></span>

### <a name="obtain-data-from-an-app"></a><span data-ttu-id="7634a-812">Získání dat z aplikace</span><span class="sxs-lookup"><span data-stu-id="7634a-812">Obtain data from an app</span></span>

<span data-ttu-id="7634a-813">Pokud aplikace dokáže reagovat na žádosti, získat požadavek, připojení a další data z aplikace pomocí vloženého middlewaru terminálu.</span><span class="sxs-lookup"><span data-stu-id="7634a-813">If an app is capable of responding to requests, obtain request, connection, and additional data from the app using terminal inline middleware.</span></span> <span data-ttu-id="7634a-814">Další informace a ukázka kódu naleznete v tématu <xref:test/troubleshoot#obtain-data-from-an-app>.</span><span class="sxs-lookup"><span data-stu-id="7634a-814">For more information and sample code, see <xref:test/troubleshoot#obtain-data-from-an-app>.</span></span>

### <a name="slow-or-hanging-app-iis"></a><span data-ttu-id="7634a-815">Pomalá nebo zavěšená aplikace (IIS)</span><span class="sxs-lookup"><span data-stu-id="7634a-815">Slow or hanging app (IIS)</span></span>

<span data-ttu-id="7634a-816">*Výpis stavu* systému je snímek paměti systému a může vám pomůže určit příčinu selhání aplikace, selhání při spuštění nebo pomalé aplikace.</span><span class="sxs-lookup"><span data-stu-id="7634a-816">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="7634a-817">Aplikace selže nebo dojde k výjimce.</span><span class="sxs-lookup"><span data-stu-id="7634a-817">App crashes or encounters an exception</span></span>

<span data-ttu-id="7634a-818">Získat a analyzovat výpis z [zasílání zpráv o chybách systému Windows (WER)](/windows/desktop/wer/windows-error-reporting):</span><span class="sxs-lookup"><span data-stu-id="7634a-818">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="7634a-819">Vytvořte složku uchovávající soubory s výpisem stavu `c:\dumps`systému.</span><span class="sxs-lookup"><span data-stu-id="7634a-819">Create a folder to hold crash dump files at `c:\dumps`.</span></span> <span data-ttu-id="7634a-820">Fond aplikací musí mít ke složce přístup pro zápis.</span><span class="sxs-lookup"><span data-stu-id="7634a-820">The app pool must have write access to the folder.</span></span>
1. <span data-ttu-id="7634a-821">Spusťte [skript prostředí PowerShell pro EnableDumps](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1):</span><span class="sxs-lookup"><span data-stu-id="7634a-821">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1):</span></span>
   * <span data-ttu-id="7634a-822">Pokud aplikace používá [model hostování v rámci procesu](xref:host-and-deploy/iis/index#in-process-hosting-model), spusťte skript pro *W3wp. exe*:</span><span class="sxs-lookup"><span data-stu-id="7634a-822">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\EnableDumps w3wp.exe c:\dumps
     ```

   * <span data-ttu-id="7634a-823">Pokud aplikace používá [model hostování mimo proces](xref:host-and-deploy/iis/index#out-of-process-hosting-model), spusťte skript pro příkaz *dotnet. exe*:</span><span class="sxs-lookup"><span data-stu-id="7634a-823">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\EnableDumps dotnet.exe c:\dumps
     ```

1. <span data-ttu-id="7634a-824">Spusťte aplikaci za podmínek, které způsobí, že dojde k chybě.</span><span class="sxs-lookup"><span data-stu-id="7634a-824">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="7634a-825">Po chybě spusťte [skript PowerShellu DisableDumps](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1):</span><span class="sxs-lookup"><span data-stu-id="7634a-825">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1):</span></span>
   * <span data-ttu-id="7634a-826">Pokud aplikace používá [model hostování v rámci procesu](xref:host-and-deploy/iis/index#in-process-hosting-model), spusťte skript pro *W3wp. exe*:</span><span class="sxs-lookup"><span data-stu-id="7634a-826">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\DisableDumps w3wp.exe
     ```

   * <span data-ttu-id="7634a-827">Pokud aplikace používá [model hostování mimo proces](xref:host-and-deploy/iis/index#out-of-process-hosting-model), spusťte skript pro příkaz *dotnet. exe*:</span><span class="sxs-lookup"><span data-stu-id="7634a-827">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\DisableDumps dotnet.exe
     ```

<span data-ttu-id="7634a-828">Po selhání aplikace a dokončení shromažďování výpisu je možné aplikaci ukončit normálně.</span><span class="sxs-lookup"><span data-stu-id="7634a-828">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="7634a-829">Skript PowerShellu nakonfiguruje WER a shromáždí až pět výpisů paměti pro každou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="7634a-829">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="7634a-830">Výpisy stavu systému můžou zabírat velké množství místa na disku (každý až několik gigabajtů).</span><span class="sxs-lookup"><span data-stu-id="7634a-830">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="7634a-831">Aplikace přestane reagovat, dojde k chybě při spuštění nebo se spustí normálně.</span><span class="sxs-lookup"><span data-stu-id="7634a-831">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="7634a-832">Když *aplikace přestane reagovat (zastaví* se, ale nejedná se o chybu), selže během spuštění nebo se spustí normálně, podívejte [se na soubory výpisu paměti v uživatelském režimu: zvolením nejlepšího](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) nástroje vyberte vhodný nástroj pro vytváření výpisu.</span><span class="sxs-lookup"><span data-stu-id="7634a-832">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="7634a-833">Analýza výpisu paměti</span><span class="sxs-lookup"><span data-stu-id="7634a-833">Analyze the dump</span></span>

<span data-ttu-id="7634a-834">Výpis paměti lze analyzovat pomocí několika přístupů.</span><span class="sxs-lookup"><span data-stu-id="7634a-834">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="7634a-835">Další informace najdete v tématu [Analýza souboru s výpisem stavu v uživatelském režimu](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span><span class="sxs-lookup"><span data-stu-id="7634a-835">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="clear-package-caches"></a><span data-ttu-id="7634a-836">Vymazat mezipaměti balíčků</span><span class="sxs-lookup"><span data-stu-id="7634a-836">Clear package caches</span></span>

<span data-ttu-id="7634a-837">Funkční aplikace může po upgradu .NET Core SDK ve vývojovém počítači nebo změně verzí balíčku v rámci aplikace selhat okamžitě.</span><span class="sxs-lookup"><span data-stu-id="7634a-837">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="7634a-838">V některých případech můžou nesouvislé balíčky při provádění hlavních upgradů poškodit aplikaci.</span><span class="sxs-lookup"><span data-stu-id="7634a-838">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="7634a-839">Většinu těchto problémů můžete vyřešit pomocí následujících pokynů:</span><span class="sxs-lookup"><span data-stu-id="7634a-839">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="7634a-840">Odstraňte složky *bin* a *obj* .</span><span class="sxs-lookup"><span data-stu-id="7634a-840">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="7634a-841">Pomocí příkazu [dotnet All--Clear](/dotnet/core/tools/dotnet-nuget-locals) z příkazového prostředí vymažte mezipaměť balíčku.</span><span class="sxs-lookup"><span data-stu-id="7634a-841">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="7634a-842">Mazání mezipamětí balíčků lze také provést pomocí nástroje [NuGet. exe](https://www.nuget.org/downloads) a provedením příkazu `nuget locals all -clear`.</span><span class="sxs-lookup"><span data-stu-id="7634a-842">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="7634a-843">*NuGet. exe* není sada instalovaná instalace s desktopovým operačním systémem Windows a musí se získat samostatně z [webu NuGet](https://www.nuget.org/downloads).</span><span class="sxs-lookup"><span data-stu-id="7634a-843">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="7634a-844">Obnovte a znovu sestavte projekt.</span><span class="sxs-lookup"><span data-stu-id="7634a-844">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="7634a-845">Před opětovným nasazením aplikace odstraňte všechny soubory ve složce pro nasazení na serveru.</span><span class="sxs-lookup"><span data-stu-id="7634a-845">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7634a-846">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="7634a-846">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:fundamentals/error-handling>
* <xref:host-and-deploy/aspnet-core-module>

### <a name="azure-documentation"></a><span data-ttu-id="7634a-847">Dokumentace k Azure</span><span class="sxs-lookup"><span data-stu-id="7634a-847">Azure documentation</span></span>

* [<span data-ttu-id="7634a-848">Application Insights pro ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7634a-848">Application Insights for ASP.NET Core</span></span>](/azure/application-insights/app-insights-asp-net-core)
* [<span data-ttu-id="7634a-849">Část vzdálené ladění webových aplikací řešení potíží s webovou aplikací v Azure App Service pomocí sady Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7634a-849">Remote debugging web apps section of Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug)
* [<span data-ttu-id="7634a-850">Přehled diagnostiky Azure App Service</span><span class="sxs-lookup"><span data-stu-id="7634a-850">Azure App Service diagnostics overview</span></span>](/azure/app-service/app-service-diagnostics)
* [<span data-ttu-id="7634a-851">Postup: Monitorování aplikací ve službě Azure App Service</span><span class="sxs-lookup"><span data-stu-id="7634a-851">How to: Monitor Apps in Azure App Service</span></span>](/azure/app-service/web-sites-monitor)
* [<span data-ttu-id="7634a-852">Řešení potíží s webovou aplikací v Azure App Service pomocí sady Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7634a-852">Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [<span data-ttu-id="7634a-853">Řešení chyb HTTP z "502 špatné brány" a "nedostupné služby 503" v Azure Web Apps</span><span class="sxs-lookup"><span data-stu-id="7634a-853">Troubleshoot HTTP errors of "502 bad gateway" and "503 service unavailable" in your Azure web apps</span></span>](/azure/app-service/app-service-web-troubleshoot-http-502-http-503)
* [<span data-ttu-id="7634a-854">Řešení potíží s výkonem pomalých webových aplikací ve službě Azure App Service</span><span class="sxs-lookup"><span data-stu-id="7634a-854">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="7634a-855">Nejčastější dotazy týkající se výkonu aplikací pro Web Apps v Azure</span><span class="sxs-lookup"><span data-stu-id="7634a-855">Application performance FAQs for Web Apps in Azure</span></span>](/azure/app-service/app-service-web-availability-performance-application-issues-faq)
* [<span data-ttu-id="7634a-856">Sandbox webové aplikace Azure (omezení spuštění App Service Runtime)</span><span class="sxs-lookup"><span data-stu-id="7634a-856">Azure Web App sandbox (App Service runtime execution limitations)</span></span>](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)
* [<span data-ttu-id="7634a-857">Azure pátek: Azure App Service diagnostické prostředí a řešení potíží (12 minut video)</span><span class="sxs-lookup"><span data-stu-id="7634a-857">Azure Friday: Azure App Service Diagnostic and Troubleshooting Experience (12-minute video)</span></span>](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)

### <a name="visual-studio-documentation"></a><span data-ttu-id="7634a-858">Dokumentace sady Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7634a-858">Visual Studio documentation</span></span>

* [<span data-ttu-id="7634a-859">Vzdálené ladění ASP.NET Core ve službě IIS v Azure v aplikaci Visual Studio 2017</span><span class="sxs-lookup"><span data-stu-id="7634a-859">Remote Debug ASP.NET Core on IIS in Azure in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-azure)
* [<span data-ttu-id="7634a-860">Vzdálené ladění ASP.NET Core na vzdáleném počítači IIS v aplikaci Visual Studio 2017</span><span class="sxs-lookup"><span data-stu-id="7634a-860">Remote Debug ASP.NET Core on a Remote IIS Computer in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-aspnet-on-a-remote-iis-computer)
* [<span data-ttu-id="7634a-861">Informace o ladění pomocí sady Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7634a-861">Learn to debug using Visual Studio</span></span>](/visualstudio/debugger/getting-started-with-the-debugger)

### <a name="visual-studio-code-documentation"></a><span data-ttu-id="7634a-862">Dokumentace k Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7634a-862">Visual Studio Code documentation</span></span>

* [<span data-ttu-id="7634a-863">Ladění pomocí Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7634a-863">Debugging with Visual Studio Code</span></span>](https://code.visualstudio.com/docs/editor/debugging)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="7634a-864">Tento článek poskytuje informace o běžných chybách při spuštění aplikací a pokyny k diagnostice chyb při nasazení aplikace do Azure App Service nebo IIS:</span><span class="sxs-lookup"><span data-stu-id="7634a-864">This article provides information on common app startup errors and instructions on how to diagnose errors when an app is deployed to Azure App Service or IIS:</span></span>

[<span data-ttu-id="7634a-865">Chyby při spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="7634a-865">App startup errors</span></span>](#app-startup-errors)  
<span data-ttu-id="7634a-866">Vysvětluje běžné spouštění scénářů stavového kódu HTTP.</span><span class="sxs-lookup"><span data-stu-id="7634a-866">Explains common startup HTTP status code scenarios.</span></span>

[<span data-ttu-id="7634a-867">Řešení potíží na Azure App Service</span><span class="sxs-lookup"><span data-stu-id="7634a-867">Troubleshoot on Azure App Service</span></span>](#troubleshoot-on-azure-app-service)  
<span data-ttu-id="7634a-868">Poskytuje rady pro řešení potíží pro aplikace nasazené do Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="7634a-868">Provides troubleshooting advice for apps deployed to Azure App Service.</span></span>

[<span data-ttu-id="7634a-869">Odstraňování potíží ve službě IIS</span><span class="sxs-lookup"><span data-stu-id="7634a-869">Troubleshoot on IIS</span></span>](#troubleshoot-on-iis)  
<span data-ttu-id="7634a-870">Poskytuje rady pro řešení potíží pro aplikace nasazené do služby IIS nebo místně spuštěné v IIS Express.</span><span class="sxs-lookup"><span data-stu-id="7634a-870">Provides troubleshooting advice for apps deployed to IIS or running on IIS Express locally.</span></span> <span data-ttu-id="7634a-871">Pokyny platí pro nasazení systémů Windows Server a Desktop v systému Windows.</span><span class="sxs-lookup"><span data-stu-id="7634a-871">The guidance applies to both Windows Server and Windows desktop deployments.</span></span>

[<span data-ttu-id="7634a-872">Vymazat mezipaměti balíčků</span><span class="sxs-lookup"><span data-stu-id="7634a-872">Clear package caches</span></span>](#clear-package-caches)  
<span data-ttu-id="7634a-873">Vysvětluje, co dělat v případě, že při provádění podstatných upgradů nebo změně verze balíčku dojde k přerušení aplikace v nesouvislých balíčcích.</span><span class="sxs-lookup"><span data-stu-id="7634a-873">Explains what to do when incoherent packages break an app when performing major upgrades or changing package versions.</span></span>

[<span data-ttu-id="7634a-874">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="7634a-874">Additional resources</span></span>](#additional-resources)  
<span data-ttu-id="7634a-875">Seznam dalších témat pro odstraňování potíží.</span><span class="sxs-lookup"><span data-stu-id="7634a-875">Lists additional troubleshooting topics.</span></span>

## <a name="app-startup-errors"></a><span data-ttu-id="7634a-876">Chyby při spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="7634a-876">App startup errors</span></span>

<span data-ttu-id="7634a-877">V aplikaci Visual Studio je výchozím nastavením projektu ASP.NET Core [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hostování během ladění.</span><span class="sxs-lookup"><span data-stu-id="7634a-877">In Visual Studio, an ASP.NET Core project defaults to [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hosting during debugging.</span></span> <span data-ttu-id="7634a-878">*Selhání procesu 502,5* , ke kterému dochází, když se místně ladění dá diagnostikovat pomocí Rady v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="7634a-878">A *502.5 Process Failure* that occurs when debugging locally can be diagnosed using the advice in this topic.</span></span>

### <a name="40314-forbidden"></a><span data-ttu-id="7634a-879">403,14 zakázané</span><span class="sxs-lookup"><span data-stu-id="7634a-879">403.14 Forbidden</span></span>

<span data-ttu-id="7634a-880">Aplikaci se nepodařilo spustit.</span><span class="sxs-lookup"><span data-stu-id="7634a-880">The app fails to start.</span></span> <span data-ttu-id="7634a-881">Protokoluje se následující chyba:</span><span class="sxs-lookup"><span data-stu-id="7634a-881">The following error is logged:</span></span>

```
The Web server is configured to not list the contents of this directory.
```

<span data-ttu-id="7634a-882">K této chybě obvykle dochází v důsledku poškozeného nasazení v hostitelském systému, který zahrnuje kterýkoli z následujících scénářů:</span><span class="sxs-lookup"><span data-stu-id="7634a-882">The error is usually caused by a broken deployment on the hosting system, which includes any of the following scenarios:</span></span>

* <span data-ttu-id="7634a-883">Aplikace je nasazená do chybné složky v hostitelském systému.</span><span class="sxs-lookup"><span data-stu-id="7634a-883">The app is deployed to the wrong folder on the hosting system.</span></span>
* <span data-ttu-id="7634a-884">Procesu nasazení se nepodařilo přesunout všechny soubory a složky aplikace do složky pro nasazení v hostitelském systému.</span><span class="sxs-lookup"><span data-stu-id="7634a-884">The deployment process failed to move all of the app's files and folders to the deployment folder on the hosting system.</span></span>
* <span data-ttu-id="7634a-885">V nasazení chybí soubor *Web. config* , nebo je obsah souboru *Web. config* poškozený.</span><span class="sxs-lookup"><span data-stu-id="7634a-885">The *web.config* file is missing from the deployment, or the *web.config* file contents are malformed.</span></span>

<span data-ttu-id="7634a-886">Proveďte následující kroky:</span><span class="sxs-lookup"><span data-stu-id="7634a-886">Perform the following steps:</span></span>

1. <span data-ttu-id="7634a-887">Odstraňte všechny soubory a složky ze složky pro nasazení v hostitelském systému.</span><span class="sxs-lookup"><span data-stu-id="7634a-887">Delete all of the files and folders from the deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="7634a-888">Znovu nasaďte obsah složky pro *publikování* aplikace do hostitelského systému pomocí běžné metody nasazení, jako je například Visual Studio, PowerShell nebo ruční nasazení:</span><span class="sxs-lookup"><span data-stu-id="7634a-888">Redeploy the contents of the app's *publish* folder to the hosting system using your normal method of deployment, such as Visual Studio, PowerShell, or manual deployment:</span></span>
   * <span data-ttu-id="7634a-889">Zkontrolujte, zda je v nasazení přítomen soubor *Web. config* a zda jeho obsah je správný.</span><span class="sxs-lookup"><span data-stu-id="7634a-889">Confirm that the *web.config* file is present in the deployment and that its contents are correct.</span></span>
   * <span data-ttu-id="7634a-890">Při hostování na Azure App Service potvrďte, že je aplikace nasazená do `D:\home\site\wwwroot` složky.</span><span class="sxs-lookup"><span data-stu-id="7634a-890">When hosting on Azure App Service, confirm that the app is deployed to the `D:\home\site\wwwroot` folder.</span></span>
   * <span data-ttu-id="7634a-891">Když je aplikace hostovaná službou IIS, zkontrolujte, že je aplikace nasazená na **fyzickou cestu** služby IIS, která se zobrazuje v **základním nastavení** **Správce služby IIS**.</span><span class="sxs-lookup"><span data-stu-id="7634a-891">When the app is hosted by IIS, confirm that the app is deployed to the IIS **Physical path** shown in **IIS Manager**'s **Basic Settings**.</span></span>
1. <span data-ttu-id="7634a-892">Ověřte, zda jsou všechny soubory a složky aplikace nasazeny porovnáním nasazení v hostitelském systému s obsahem složky *publikování* projektu.</span><span class="sxs-lookup"><span data-stu-id="7634a-892">Confirm that all of the app's files and folders are deployed by comparing the deployment on the hosting system to the contents of the project's *publish* folder.</span></span>

<span data-ttu-id="7634a-893">Další informace o rozložení publikované aplikace ASP.NET Core najdete v tématu <xref:host-and-deploy/directory-structure>.</span><span class="sxs-lookup"><span data-stu-id="7634a-893">For more information on the layout of a published ASP.NET Core app, see <xref:host-and-deploy/directory-structure>.</span></span> <span data-ttu-id="7634a-894">Další informace o souboru *Web. config* naleznete v tématu <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>.</span><span class="sxs-lookup"><span data-stu-id="7634a-894">For more information on the *web.config* file, see <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>.</span></span>

### <a name="500-internal-server-error"></a><span data-ttu-id="7634a-895">500 – Interní chyba serveru</span><span class="sxs-lookup"><span data-stu-id="7634a-895">500 Internal Server Error</span></span>

<span data-ttu-id="7634a-896">Aplikace se spustí, ale chyba brání serveru v splnění žádosti.</span><span class="sxs-lookup"><span data-stu-id="7634a-896">The app starts, but an error prevents the server from fulfilling the request.</span></span>

<span data-ttu-id="7634a-897">K této chybě dochází v kódu aplikace během spuštění nebo při vytváření odpovědi.</span><span class="sxs-lookup"><span data-stu-id="7634a-897">This error occurs within the app's code during startup or while creating a response.</span></span> <span data-ttu-id="7634a-898">Odpověď možná neobsahuje žádný obsah, nebo se odpověď může zobrazit jako *500 interní chyba serveru* v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="7634a-898">The response may contain no content, or the response may appear as a *500 Internal Server Error* in the browser.</span></span> <span data-ttu-id="7634a-899">Protokol událostí aplikace obvykle uvádí, že aplikace začala normálně.</span><span class="sxs-lookup"><span data-stu-id="7634a-899">The Application Event Log usually states that the app started normally.</span></span> <span data-ttu-id="7634a-900">Z hlediska serveru je to správné.</span><span class="sxs-lookup"><span data-stu-id="7634a-900">From the server's perspective, that's correct.</span></span> <span data-ttu-id="7634a-901">Aplikace se spustila, ale nemůže vygenerovat platnou odpověď.</span><span class="sxs-lookup"><span data-stu-id="7634a-901">The app did start, but it can't generate a valid response.</span></span> <span data-ttu-id="7634a-902">Spusťte aplikaci na příkazovém řádku na serveru nebo povolte řešení potíží pomocí protokolu stdout modulu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7634a-902">Run the app at a command prompt on the server or enable the ASP.NET Core Module stdout log to troubleshoot the problem.</span></span>

### <a name="5025-process-failure"></a><span data-ttu-id="7634a-903">502.5 – Selhání procesu</span><span class="sxs-lookup"><span data-stu-id="7634a-903">502.5 Process Failure</span></span>

<span data-ttu-id="7634a-904">Pracovní proces se nezdařil.</span><span class="sxs-lookup"><span data-stu-id="7634a-904">The worker process fails.</span></span> <span data-ttu-id="7634a-905">Aplikace se nespustí.</span><span class="sxs-lookup"><span data-stu-id="7634a-905">The app doesn't start.</span></span>

<span data-ttu-id="7634a-906">[Modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module) se pokusí spustit pracovní proces, ale jeho spuštění se nezdařilo.</span><span class="sxs-lookup"><span data-stu-id="7634a-906">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the worker process but it fails to start.</span></span> <span data-ttu-id="7634a-907">Příčinou selhání spuštění procesu může být obvykle určení z položek protokolu událostí aplikace a protokolu stdout modulu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7634a-907">The cause of a process startup failure can usually be determined from entries in the Application Event Log and the ASP.NET Core Module stdout log.</span></span>

<span data-ttu-id="7634a-908">Obvyklou chybovou podmínkou je, že aplikace je nesprávně nakonfigurovaná kvůli cílení na verzi ASP.NET Core sdílené rozhraní, které není k dispozici.</span><span class="sxs-lookup"><span data-stu-id="7634a-908">A common failure condition is the app is misconfigured due to targeting a version of the ASP.NET Core shared framework that isn't present.</span></span> <span data-ttu-id="7634a-909">Ověřte, které verze ASP.NET Core sdílené rozhraní jsou nainstalovány v cílovém počítači.</span><span class="sxs-lookup"><span data-stu-id="7634a-909">Check which versions of the ASP.NET Core shared framework are installed on the target machine.</span></span> <span data-ttu-id="7634a-910">*Sdílené rozhraní* je sada sestavení (soubory *. dll* ), které jsou nainstalovány na počítači a na které odkazuje Metapackage jako `Microsoft.AspNetCore.App`.</span><span class="sxs-lookup"><span data-stu-id="7634a-910">The *shared framework* is the set of assemblies (*.dll* files) that are installed on the machine and referenced by a metapackage such as `Microsoft.AspNetCore.App`.</span></span> <span data-ttu-id="7634a-911">Odkaz Metapackage může určovat minimální požadovanou verzi.</span><span class="sxs-lookup"><span data-stu-id="7634a-911">The metapackage reference can specify a minimum required version.</span></span> <span data-ttu-id="7634a-912">Další informace najdete v tématu [sdílené rozhraní](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span><span class="sxs-lookup"><span data-stu-id="7634a-912">For more information, see [The shared framework](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span></span>

<span data-ttu-id="7634a-913">Chybová stránka *selhání procesu 502,5* se vrátí v případě, že při selhání hostování nebo aplikace dojde k chybě pracovního procesu:</span><span class="sxs-lookup"><span data-stu-id="7634a-913">The *502.5 Process Failure* error page is returned when a hosting or app misconfiguration causes the worker process to fail:</span></span>

### <a name="failed-to-start-application-errorcode-0x800700c1"></a><span data-ttu-id="7634a-914">Nepovedlo se spustit aplikaci (ErrorCode ' 0x800700C1 ')</span><span class="sxs-lookup"><span data-stu-id="7634a-914">Failed to start application (ErrorCode '0x800700c1')</span></span>

```
EventID: 1010
Source: IIS AspNetCore Module V2
Failed to start application '/LM/W3SVC/6/ROOT/', ErrorCode '0x800700c1'.
```

<span data-ttu-id="7634a-915">Aplikaci se nepovedlo spustit, protože se nepovedlo načíst sestavení aplikace (*. dll*).</span><span class="sxs-lookup"><span data-stu-id="7634a-915">The app failed to start because the app's assembly (*.dll*) couldn't be loaded.</span></span>

<span data-ttu-id="7634a-916">K této chybě dochází, pokud dojde k neshodě bitová verze mezi publikovanou aplikací a procesem W3wp/IISExpress.</span><span class="sxs-lookup"><span data-stu-id="7634a-916">This error occurs when there's a bitness mismatch between the published app and the w3wp/iisexpress process.</span></span>

<span data-ttu-id="7634a-917">Zkontrolujte, jestli je správně nastavené nastavení 32 fondu aplikací:</span><span class="sxs-lookup"><span data-stu-id="7634a-917">Confirm that the app pool's 32-bit setting is correct:</span></span>

1. <span data-ttu-id="7634a-918">Vyberte fond aplikací v **fondech aplikací**Správce služby IIS.</span><span class="sxs-lookup"><span data-stu-id="7634a-918">Select the app pool in IIS Manager's **Application Pools**.</span></span>
1. <span data-ttu-id="7634a-919">V části **Upravit fond aplikací** na panelu **Akce** vyberte **Upřesnit nastavení** .</span><span class="sxs-lookup"><span data-stu-id="7634a-919">Select **Advanced Settings** under **Edit Application Pool** in the **Actions** panel.</span></span>
1. <span data-ttu-id="7634a-920">Nastavte **možnost povolit 32 – bitové aplikace**:</span><span class="sxs-lookup"><span data-stu-id="7634a-920">Set **Enable 32-Bit Applications**:</span></span>
   * <span data-ttu-id="7634a-921">Pokud nasazujete 32 (x86) aplikaci, nastavte hodnotu na `True`.</span><span class="sxs-lookup"><span data-stu-id="7634a-921">If deploying a 32-bit (x86) app, set the value to `True`.</span></span>
   * <span data-ttu-id="7634a-922">Pokud nasazujete 64 aplikaci (x64), nastavte hodnotu na `False`.</span><span class="sxs-lookup"><span data-stu-id="7634a-922">If deploying a 64-bit (x64) app, set the value to `False`.</span></span>

<span data-ttu-id="7634a-923">Ověřte, že mezi vlastností `<Platform>` MSBuild v souboru projektu a publikovaným bitová verzeem aplikace nedochází ke konfliktu.</span><span class="sxs-lookup"><span data-stu-id="7634a-923">Confirm that there isn't a conflict between a `<Platform>` MSBuild property in the project file and the published bitness of the app.</span></span>

### <a name="connection-reset"></a><span data-ttu-id="7634a-924">Resetování připojení</span><span class="sxs-lookup"><span data-stu-id="7634a-924">Connection reset</span></span>

<span data-ttu-id="7634a-925">Pokud dojde k chybě po odeslání hlaviček, je příliš pozdě pro server, který odešle **500 interní chybu serveru** , když dojde k chybě.</span><span class="sxs-lookup"><span data-stu-id="7634a-925">If an error occurs after the headers are sent, it's too late for the server to send a **500 Internal Server Error** when an error occurs.</span></span> <span data-ttu-id="7634a-926">K tomu často dochází, když dojde k chybě během serializace složitých objektů pro odpověď.</span><span class="sxs-lookup"><span data-stu-id="7634a-926">This often happens when an error occurs during the serialization of complex objects for a response.</span></span> <span data-ttu-id="7634a-927">Tento typ chyby se zobrazí jako chyba *resetování připojení* na klientovi.</span><span class="sxs-lookup"><span data-stu-id="7634a-927">This type of error appears as a *connection reset* error on the client.</span></span> <span data-ttu-id="7634a-928">[Protokolování aplikace](xref:fundamentals/logging/index) může pomoct řešit tyto typy chyb.</span><span class="sxs-lookup"><span data-stu-id="7634a-928">[Application logging](xref:fundamentals/logging/index) can help troubleshoot these types of errors.</span></span>

### <a name="default-startup-limits"></a><span data-ttu-id="7634a-929">Výchozí omezení spouštění</span><span class="sxs-lookup"><span data-stu-id="7634a-929">Default startup limits</span></span>

<span data-ttu-id="7634a-930">[ASP.NET Core modul](xref:host-and-deploy/aspnet-core-module) je nakonfigurovaný s výchozí *startupTimeLimitou* 120 sekund.</span><span class="sxs-lookup"><span data-stu-id="7634a-930">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) is configured with a default *startupTimeLimit* of 120 seconds.</span></span> <span data-ttu-id="7634a-931">Pokud necháte výchozí hodnotu, aplikace může trvat až dvě minuty, než se modul zaznamená selhání procesu.</span><span class="sxs-lookup"><span data-stu-id="7634a-931">When left at the default value, an app may take up to two minutes to start before the module logs a process failure.</span></span> <span data-ttu-id="7634a-932">Informace o konfiguraci modulu naleznete v tématu [atributy elementu aspNetCore](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span><span class="sxs-lookup"><span data-stu-id="7634a-932">For information on configuring the module, see [Attributes of the aspNetCore element](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span></span>

## <a name="troubleshoot-on-azure-app-service"></a><span data-ttu-id="7634a-933">Řešení potíží na Azure App Service</span><span class="sxs-lookup"><span data-stu-id="7634a-933">Troubleshoot on Azure App Service</span></span>

[!INCLUDE [Azure App Service Preview Notice](~/includes/azure-apps-preview-notice.md)]

### <a name="application-event-log-azure-app-service"></a><span data-ttu-id="7634a-934">Protokol událostí aplikace (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="7634a-934">Application Event Log (Azure App Service)</span></span>

<span data-ttu-id="7634a-935">Chcete-li získat přístup k protokolu událostí aplikace, použijte okno **Diagnostika a řešení problémů** v Azure Portal:</span><span class="sxs-lookup"><span data-stu-id="7634a-935">To access the Application Event Log, use the **Diagnose and solve problems** blade in the Azure portal:</span></span>

1. <span data-ttu-id="7634a-936">V Azure Portal otevřete aplikaci v **App Services**.</span><span class="sxs-lookup"><span data-stu-id="7634a-936">In the Azure portal, open the app in **App Services**.</span></span>
1. <span data-ttu-id="7634a-937">Vyberte **Diagnostikovat a řešit problémy**.</span><span class="sxs-lookup"><span data-stu-id="7634a-937">Select **Diagnose and solve problems**.</span></span>
1. <span data-ttu-id="7634a-938">Vyberte **diagnostické nástroje** záhlaví.</span><span class="sxs-lookup"><span data-stu-id="7634a-938">Select the **Diagnostic Tools** heading.</span></span>
1. <span data-ttu-id="7634a-939">V nabídce **nástroje podpory**vyberte tlačítko **události aplikace** .</span><span class="sxs-lookup"><span data-stu-id="7634a-939">Under **Support Tools**, select the **Application Events** button.</span></span>
1. <span data-ttu-id="7634a-940">Projděte si nejnovější chybu, kterou poskytla položka *IIS AspNetCoreModule* nebo *IIS AspNetCoreModule v2* ve **zdrojovém** sloupci.</span><span class="sxs-lookup"><span data-stu-id="7634a-940">Examine the latest error provided by the *IIS AspNetCoreModule* or *IIS AspNetCoreModule V2* entry in the **Source** column.</span></span>

<span data-ttu-id="7634a-941">Alternativou k použití okna **diagnostikovat a řešit problémy** je kontrola souboru protokolu událostí aplikace přímo pomocí [Kudu](https://github.com/projectkudu/kudu/wiki):</span><span class="sxs-lookup"><span data-stu-id="7634a-941">An alternative to using the **Diagnose and solve problems** blade is to examine the Application Event Log file directly using [Kudu](https://github.com/projectkudu/kudu/wiki):</span></span>

1. <span data-ttu-id="7634a-942">Otevřete **Rozšířené nástroje** v oblasti **vývojové nástroje** .</span><span class="sxs-lookup"><span data-stu-id="7634a-942">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="7634a-943">Vyberte tlačítko \*\*Přejít&rarr; \*\* .</span><span class="sxs-lookup"><span data-stu-id="7634a-943">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="7634a-944">Konzola Kudu se otevře v novém okně nebo záložce prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="7634a-944">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="7634a-945">Pomocí navigačního panelu v horní části stránky otevřete **konzolu ladění** a vyberte **cmd**.</span><span class="sxs-lookup"><span data-stu-id="7634a-945">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="7634a-946">Otevřete složku **soubory protokolů** .</span><span class="sxs-lookup"><span data-stu-id="7634a-946">Open the **LogFiles** folder.</span></span>
1. <span data-ttu-id="7634a-947">Vyberte ikonu tužky vedle souboru *EventLog. XML* .</span><span class="sxs-lookup"><span data-stu-id="7634a-947">Select the pencil icon next to the *eventlog.xml* file.</span></span>
1. <span data-ttu-id="7634a-948">Projděte si protokol.</span><span class="sxs-lookup"><span data-stu-id="7634a-948">Examine the log.</span></span> <span data-ttu-id="7634a-949">Posuňte se do dolní části protokolu, abyste viděli nejaktuálnější události.</span><span class="sxs-lookup"><span data-stu-id="7634a-949">Scroll to the bottom of the log to see the most recent events.</span></span>

### <a name="run-the-app-in-the-kudu-console"></a><span data-ttu-id="7634a-950">Spuštění aplikace v konzole Kudu</span><span class="sxs-lookup"><span data-stu-id="7634a-950">Run the app in the Kudu console</span></span>

<span data-ttu-id="7634a-951">Mnoho chyb při spuštění nevytváří užitečné informace v protokolu událostí aplikace.</span><span class="sxs-lookup"><span data-stu-id="7634a-951">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="7634a-952">Tuto chybu můžete zjistit spuštěním aplikace v konzole vzdáleného spuštění [Kudu](https://github.com/projectkudu/kudu/wiki) :</span><span class="sxs-lookup"><span data-stu-id="7634a-952">You can run the app in the [Kudu](https://github.com/projectkudu/kudu/wiki) Remote Execution Console to discover the error:</span></span>

1. <span data-ttu-id="7634a-953">Otevřete **Rozšířené nástroje** v oblasti **vývojové nástroje** .</span><span class="sxs-lookup"><span data-stu-id="7634a-953">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="7634a-954">Vyberte tlačítko \*\*Přejít&rarr; \*\* .</span><span class="sxs-lookup"><span data-stu-id="7634a-954">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="7634a-955">Konzola Kudu se otevře v novém okně nebo záložce prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="7634a-955">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="7634a-956">Pomocí navigačního panelu v horní části stránky otevřete **konzolu ladění** a vyberte **cmd**.</span><span class="sxs-lookup"><span data-stu-id="7634a-956">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>

#### <a name="test-a-32-bit-x86-app"></a><span data-ttu-id="7634a-957">Testování 32 (x86) aplikace</span><span class="sxs-lookup"><span data-stu-id="7634a-957">Test a 32-bit (x86) app</span></span>

<span data-ttu-id="7634a-958">**Aktuální verze**</span><span class="sxs-lookup"><span data-stu-id="7634a-958">**Current release**</span></span>

1. `cd d:\home\site\wwwroot`
1. <span data-ttu-id="7634a-959">Spusťte aplikaci:</span><span class="sxs-lookup"><span data-stu-id="7634a-959">Run the app:</span></span>
   * <span data-ttu-id="7634a-960">Pokud aplikace je [nasazení závislé na rozhraní](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span><span class="sxs-lookup"><span data-stu-id="7634a-960">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

     ```dotnetcli
     dotnet .\{ASSEMBLY NAME}.dll
     ```

   * <span data-ttu-id="7634a-961">Pokud je aplikace samostatná, [nasazení](/dotnet/core/deploying/#self-contained-deployments-scd):</span><span class="sxs-lookup"><span data-stu-id="7634a-961">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

     ```console
     {ASSEMBLY NAME}.exe
     ```

<span data-ttu-id="7634a-962">Výstup konzoly z aplikace, v němž se zobrazují všechny chyby, je předán do konzoly Kudu.</span><span class="sxs-lookup"><span data-stu-id="7634a-962">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="7634a-963">**Nasazení závislé na architektuře spuštěné ve verzi Preview**</span><span class="sxs-lookup"><span data-stu-id="7634a-963">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="7634a-964">*Vyžaduje instalaci rozšíření webu ASP.NET Core {VERSION} (x86) Runtime.*</span><span class="sxs-lookup"><span data-stu-id="7634a-964">*Requires installing the ASP.NET Core {VERSION} (x86) Runtime site extension.*</span></span>

1. <span data-ttu-id="7634a-965">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32`(`{X.Y}` je verze modulu runtime)</span><span class="sxs-lookup"><span data-stu-id="7634a-965">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="7634a-966">Spusťte aplikaci: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="7634a-966">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="7634a-967">Výstup konzoly z aplikace, v němž se zobrazují všechny chyby, je předán do konzoly Kudu.</span><span class="sxs-lookup"><span data-stu-id="7634a-967">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

#### <a name="test-a-64-bit-x64-app"></a><span data-ttu-id="7634a-968">Testování 64 aplikace (x64)</span><span class="sxs-lookup"><span data-stu-id="7634a-968">Test a 64-bit (x64) app</span></span>

<span data-ttu-id="7634a-969">**Aktuální verze**</span><span class="sxs-lookup"><span data-stu-id="7634a-969">**Current release**</span></span>

* <span data-ttu-id="7634a-970">Pokud je aplikace [nasazením závislého na rozhraní](/dotnet/core/deploying/#framework-dependent-deployments-fdd)64 (x64):</span><span class="sxs-lookup"><span data-stu-id="7634a-970">If the app is a 64-bit (x64) [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>
  1. `cd D:\Program Files\dotnet`
  1. <span data-ttu-id="7634a-971">Spusťte aplikaci: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="7634a-971">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>
* <span data-ttu-id="7634a-972">Pokud je aplikace samostatná, [nasazení](/dotnet/core/deploying/#self-contained-deployments-scd):</span><span class="sxs-lookup"><span data-stu-id="7634a-972">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>
  1. `cd D:\home\site\wwwroot`
  1. <span data-ttu-id="7634a-973">Spusťte aplikaci: `{ASSEMBLY NAME}.exe`</span><span class="sxs-lookup"><span data-stu-id="7634a-973">Run the app: `{ASSEMBLY NAME}.exe`</span></span>

<span data-ttu-id="7634a-974">Výstup konzoly z aplikace, v němž se zobrazují všechny chyby, je předán do konzoly Kudu.</span><span class="sxs-lookup"><span data-stu-id="7634a-974">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="7634a-975">**Nasazení závislé na architektuře spuštěné ve verzi Preview**</span><span class="sxs-lookup"><span data-stu-id="7634a-975">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="7634a-976">*Vyžaduje instalaci rozšíření webu ASP.NET Core {VERSION} (x64) Runtime.*</span><span class="sxs-lookup"><span data-stu-id="7634a-976">*Requires installing the ASP.NET Core {VERSION} (x64) Runtime site extension.*</span></span>

1. <span data-ttu-id="7634a-977">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64`(`{X.Y}` je verze modulu runtime)</span><span class="sxs-lookup"><span data-stu-id="7634a-977">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="7634a-978">Spusťte aplikaci: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="7634a-978">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="7634a-979">Výstup konzoly z aplikace, v němž se zobrazují všechny chyby, je předán do konzoly Kudu.</span><span class="sxs-lookup"><span data-stu-id="7634a-979">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

### <a name="aspnet-core-module-stdout-log-azure-app-service"></a><span data-ttu-id="7634a-980">Protokol stdout v modulu ASP.NET Core (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="7634a-980">ASP.NET Core Module stdout log (Azure App Service)</span></span>

<span data-ttu-id="7634a-981">Protokol stdout modulu ASP.NET Coree často zaznamenává užitečné chybové zprávy, které se nenašly v protokolu událostí aplikace.</span><span class="sxs-lookup"><span data-stu-id="7634a-981">The ASP.NET Core Module stdout log often records useful error messages not found in the Application Event Log.</span></span> <span data-ttu-id="7634a-982">Postup povolení a zobrazení protokolů stdout:</span><span class="sxs-lookup"><span data-stu-id="7634a-982">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="7634a-983">Přejděte do okna **diagnostikovat a řešit problémy** v Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="7634a-983">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="7634a-984">V části **Vybrat kategorii problému**vyberte tlačítko pro **webovou aplikaci** .</span><span class="sxs-lookup"><span data-stu-id="7634a-984">Under **SELECT PROBLEM CATEGORY**, select the **Web App Down** button.</span></span>
1. <span data-ttu-id="7634a-985">V části **navrhovaná řešení** > **povolte přesměrování protokolu stdout**a výběrem tlačítka **otevřete konzolu Kudu a upravte soubor Web. config**.</span><span class="sxs-lookup"><span data-stu-id="7634a-985">Under **Suggested Solutions** > **Enable Stdout Log Redirection**, select the button to **Open Kudu Console to edit Web.Config**.</span></span>
1. <span data-ttu-id="7634a-986">V konzole pro **diagnostiku**Kudu otevřete složky v **lokalitě** > s cestou**wwwroot**.</span><span class="sxs-lookup"><span data-stu-id="7634a-986">In the Kudu **Diagnostic Console**, open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="7634a-987">Posuňte se dolů a odhalte soubor *Web. config* v dolní části seznamu.</span><span class="sxs-lookup"><span data-stu-id="7634a-987">Scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="7634a-988">Klikněte na ikonu tužky vedle souboru *Web. config* .</span><span class="sxs-lookup"><span data-stu-id="7634a-988">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="7634a-989">Nastavte **stdoutLogEnabled** na `true` a změňte cestu **stdoutLogFile** na: `\\?\%home%\LogFiles\stdout`.</span><span class="sxs-lookup"><span data-stu-id="7634a-989">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="7634a-990">Vyberte **Uložit** a uložte aktualizovaný soubor *Web. config* .</span><span class="sxs-lookup"><span data-stu-id="7634a-990">Select **Save** to save the updated *web.config* file.</span></span>
1. <span data-ttu-id="7634a-991">Vytvořte žádost do aplikace.</span><span class="sxs-lookup"><span data-stu-id="7634a-991">Make a request to the app.</span></span>
1. <span data-ttu-id="7634a-992">Vraťte se na Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="7634a-992">Return to the Azure portal.</span></span> <span data-ttu-id="7634a-993">V oblasti **vývojové nástroje** vyberte okno **Pokročilé nástroje** .</span><span class="sxs-lookup"><span data-stu-id="7634a-993">Select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="7634a-994">Vyberte tlačítko \*\*Přejít&rarr; \*\* .</span><span class="sxs-lookup"><span data-stu-id="7634a-994">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="7634a-995">Konzola Kudu se otevře v novém okně nebo záložce prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="7634a-995">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="7634a-996">Pomocí navigačního panelu v horní části stránky otevřete **konzolu ladění** a vyberte **cmd**.</span><span class="sxs-lookup"><span data-stu-id="7634a-996">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="7634a-997">Vyberte složku **soubory protokolů** .</span><span class="sxs-lookup"><span data-stu-id="7634a-997">Select the **LogFiles** folder.</span></span>
1. <span data-ttu-id="7634a-998">Zkontrolujte **upravený** sloupec a vyberte ikonu tužky a upravte protokol stdout s nejnovějším datem úpravy.</span><span class="sxs-lookup"><span data-stu-id="7634a-998">Inspect the **Modified** column and select the pencil icon to edit the stdout log with the latest modification date.</span></span>
1. <span data-ttu-id="7634a-999">Po otevření souboru protokolu se zobrazí chyba.</span><span class="sxs-lookup"><span data-stu-id="7634a-999">When the log file opens, the error is displayed.</span></span>

<span data-ttu-id="7634a-1000">Zakázat protokolování stdout při odstraňování potíží:</span><span class="sxs-lookup"><span data-stu-id="7634a-1000">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="7634a-1001">V **diagnostické konzole**Kudu se vraťte do cesty**wwwroot** **lokality** > , která odhalí soubor *Web. config* .</span><span class="sxs-lookup"><span data-stu-id="7634a-1001">In the Kudu **Diagnostic Console**, return to the path **site** > **wwwroot** to reveal the *web.config* file.</span></span> <span data-ttu-id="7634a-1002">Otevřete soubor **Web. config** znovu výběrem ikony tužky.</span><span class="sxs-lookup"><span data-stu-id="7634a-1002">Open the **web.config** file again by selecting the pencil icon.</span></span>
1. <span data-ttu-id="7634a-1003">Nastavte **stdoutLogEnabled** na `false`.</span><span class="sxs-lookup"><span data-stu-id="7634a-1003">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="7634a-1004">Vyberte **Uložit** a soubor uložte.</span><span class="sxs-lookup"><span data-stu-id="7634a-1004">Select **Save** to save the file.</span></span>

<span data-ttu-id="7634a-1005">Další informace naleznete v tématu <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span><span class="sxs-lookup"><span data-stu-id="7634a-1005">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

> [!WARNING]
> <span data-ttu-id="7634a-1006">Nepovedlo se zakázat protokol stdout, což může způsobit selhání aplikace nebo serveru.</span><span class="sxs-lookup"><span data-stu-id="7634a-1006">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="7634a-1007">Velikost souboru protokolu ani počet vytvořených souborů protokolu nijak neomezuje.</span><span class="sxs-lookup"><span data-stu-id="7634a-1007">There's no limit on log file size or the number of log files created.</span></span> <span data-ttu-id="7634a-1008">K řešení problémů se spouštěním aplikací použijte pouze protokolování STDOUT.</span><span class="sxs-lookup"><span data-stu-id="7634a-1008">Only use stdout logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="7634a-1009">Pro obecné protokolování v aplikaci ASP.NET Core po spuštění použijte knihovnu protokolování, která omezuje velikost souboru protokolu a otočí protokoly.</span><span class="sxs-lookup"><span data-stu-id="7634a-1009">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="7634a-1010">Další informace najdete v tématu [Zprostředkovatelé protokolování třetích stran](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="7634a-1010">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="slow-or-hanging-app-azure-app-service"></a><span data-ttu-id="7634a-1011">Pomalá nebo zavěšená aplikace (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="7634a-1011">Slow or hanging app (Azure App Service)</span></span>

<span data-ttu-id="7634a-1012">Pokud aplikace reaguje pomalu nebo přestane reagovat na žádost, přečtěte si následující články:</span><span class="sxs-lookup"><span data-stu-id="7634a-1012">When an app responds slowly or hangs on a request, see the following articles:</span></span>

* [<span data-ttu-id="7634a-1013">Řešení potíží s výkonem pomalých webových aplikací ve službě Azure App Service</span><span class="sxs-lookup"><span data-stu-id="7634a-1013">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="7634a-1014">K zachycení výpisu problémů s přerušovanou výjimkou nebo problémy s výkonem ve webové aplikaci Azure použijte rozšíření pro diagnostiku zhroucení serveru.</span><span class="sxs-lookup"><span data-stu-id="7634a-1014">Use Crash Diagnoser Site Extension to Capture Dump for Intermittent Exception issues or performance issues on Azure Web App</span></span>](https://blogs.msdn.microsoft.com/asiatech/2015/12/28/use-crash-diagnoser-site-extension-to-capture-dump-for-intermittent-exception-issues-or-performance-issues-on-azure-web-app/)

### <a name="monitoring-blades"></a><span data-ttu-id="7634a-1015">Okna monitorování</span><span class="sxs-lookup"><span data-stu-id="7634a-1015">Monitoring blades</span></span>

<span data-ttu-id="7634a-1016">Okna monitorování poskytují alternativní možnosti řešení potíží pro metody popsané dříve v tématu.</span><span class="sxs-lookup"><span data-stu-id="7634a-1016">Monitoring blades provide an alternative troubleshooting experience to the methods described earlier in the topic.</span></span> <span data-ttu-id="7634a-1017">Pomocí těchto oken můžete diagnostikovat chyby 500-Series.</span><span class="sxs-lookup"><span data-stu-id="7634a-1017">These blades can be used to diagnose 500-series errors.</span></span>

<span data-ttu-id="7634a-1018">Potvrďte, že jsou nainstalovaná rozšíření ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7634a-1018">Confirm that the ASP.NET Core Extensions are installed.</span></span> <span data-ttu-id="7634a-1019">Pokud nejsou rozšíření nainstalována, nainstalujte je ručně:</span><span class="sxs-lookup"><span data-stu-id="7634a-1019">If the extensions aren't installed, install them manually:</span></span>

1. <span data-ttu-id="7634a-1020">V části okno **vývojové nástroje** vyberte okno **rozšíření** .</span><span class="sxs-lookup"><span data-stu-id="7634a-1020">In the **DEVELOPMENT TOOLS** blade section, select the **Extensions** blade.</span></span>
1. <span data-ttu-id="7634a-1021">V seznamu by se měla zobrazit **rozšíření ASP.NET Core** .</span><span class="sxs-lookup"><span data-stu-id="7634a-1021">The **ASP.NET Core Extensions** should appear in the list.</span></span>
1. <span data-ttu-id="7634a-1022">Pokud nejsou rozšíření nainstalovaná, vyberte tlačítko **Přidat** .</span><span class="sxs-lookup"><span data-stu-id="7634a-1022">If the extensions aren't installed, select the **Add** button.</span></span>
1. <span data-ttu-id="7634a-1023">Ze seznamu vyberte **rozšíření ASP.NET Core** .</span><span class="sxs-lookup"><span data-stu-id="7634a-1023">Choose the **ASP.NET Core Extensions** from the list.</span></span>
1. <span data-ttu-id="7634a-1024">Kliknutím na **OK** přijměte právní podmínky.</span><span class="sxs-lookup"><span data-stu-id="7634a-1024">Select **OK** to accept the legal terms.</span></span>
1. <span data-ttu-id="7634a-1025">V okně **Přidat rozšíření** vyberte **OK** .</span><span class="sxs-lookup"><span data-stu-id="7634a-1025">Select **OK** on the **Add extension** blade.</span></span>
1. <span data-ttu-id="7634a-1026">Informační automaticky otevíraná zpráva indikuje, že se rozšíření úspěšně nainstalovala.</span><span class="sxs-lookup"><span data-stu-id="7634a-1026">An informational pop-up message indicates when the extensions are successfully installed.</span></span>

<span data-ttu-id="7634a-1027">Pokud není povoleno protokolování stdout, postupujte následovně:</span><span class="sxs-lookup"><span data-stu-id="7634a-1027">If stdout logging isn't enabled, follow these steps:</span></span>

1. <span data-ttu-id="7634a-1028">V Azure Portal v oblasti **vývojové nástroje** vyberte okno **Pokročilé nástroje** .</span><span class="sxs-lookup"><span data-stu-id="7634a-1028">In the Azure portal, select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="7634a-1029">Vyberte tlačítko \*\*Přejít&rarr; \*\* .</span><span class="sxs-lookup"><span data-stu-id="7634a-1029">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="7634a-1030">Konzola Kudu se otevře v novém okně nebo záložce prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="7634a-1030">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="7634a-1031">Pomocí navigačního panelu v horní části stránky otevřete **konzolu ladění** a vyberte **cmd**.</span><span class="sxs-lookup"><span data-stu-id="7634a-1031">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="7634a-1032">Otevřete složky **pro cestu** > **wwwroot** a posuňte se dolů, aby se v dolní části seznamu zobrazil soubor *Web. config* .</span><span class="sxs-lookup"><span data-stu-id="7634a-1032">Open the folders to the path **site** > **wwwroot** and scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="7634a-1033">Klikněte na ikonu tužky vedle souboru *Web. config* .</span><span class="sxs-lookup"><span data-stu-id="7634a-1033">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="7634a-1034">Nastavte **stdoutLogEnabled** na `true` a změňte cestu **stdoutLogFile** na: `\\?\%home%\LogFiles\stdout`.</span><span class="sxs-lookup"><span data-stu-id="7634a-1034">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="7634a-1035">Vyberte **Uložit** a uložte aktualizovaný soubor *Web. config* .</span><span class="sxs-lookup"><span data-stu-id="7634a-1035">Select **Save** to save the updated *web.config* file.</span></span>

<span data-ttu-id="7634a-1036">Pokračovat k aktivaci protokolování diagnostiky:</span><span class="sxs-lookup"><span data-stu-id="7634a-1036">Proceed to activate diagnostic logging:</span></span>

1. <span data-ttu-id="7634a-1037">V Azure Portal vyberte okno **diagnostické protokoly** .</span><span class="sxs-lookup"><span data-stu-id="7634a-1037">In the Azure portal, select the **Diagnostics logs** blade.</span></span>
1. <span data-ttu-id="7634a-1038">Vyberte přepínač **zapnuto** pro **protokolování aplikace (systém souborů)** a **podrobné chybové zprávy**.</span><span class="sxs-lookup"><span data-stu-id="7634a-1038">Select the **On** switch for **Application Logging (Filesystem)** and **Detailed error messages**.</span></span> <span data-ttu-id="7634a-1039">V horní části okna vyberte tlačítko **Uložit** .</span><span class="sxs-lookup"><span data-stu-id="7634a-1039">Select the **Save** button at the top of the blade.</span></span>
1. <span data-ttu-id="7634a-1040">Chcete-li zahrnout trasování chybných požadavků, označované také jako protokolování událostí neúspěšných požadavků na FREB, vyberte přepínač **zapnuto** pro **trasování chybných požadavků**.</span><span class="sxs-lookup"><span data-stu-id="7634a-1040">To include failed request tracing, also known as Failed Request Event Buffering (FREB) logging, select the **On** switch for **Failed request tracing**.</span></span>
1. <span data-ttu-id="7634a-1041">Vyberte okno **log Stream** , které je uvedeno hned pod oknem **diagnostické protokoly** na portálu.</span><span class="sxs-lookup"><span data-stu-id="7634a-1041">Select the **Log stream** blade, which is listed immediately under the **Diagnostics logs** blade in the portal.</span></span>
1. <span data-ttu-id="7634a-1042">Vytvořte žádost do aplikace.</span><span class="sxs-lookup"><span data-stu-id="7634a-1042">Make a request to the app.</span></span>
1. <span data-ttu-id="7634a-1043">V datech streamu protokolu se označuje příčina chyby.</span><span class="sxs-lookup"><span data-stu-id="7634a-1043">Within the log stream data, the cause of the error is indicated.</span></span>

<span data-ttu-id="7634a-1044">Nezapomeňte zakázat protokolování stdout po dokončení řešení potíží.</span><span class="sxs-lookup"><span data-stu-id="7634a-1044">Be sure to disable stdout logging when troubleshooting is complete.</span></span>

<span data-ttu-id="7634a-1045">Zobrazení protokolů pro trasování chybných požadavků (protokoly FREB):</span><span class="sxs-lookup"><span data-stu-id="7634a-1045">To view the failed request tracing logs (FREB logs):</span></span>

1. <span data-ttu-id="7634a-1046">Přejděte do okna **diagnostikovat a řešit problémy** v Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="7634a-1046">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="7634a-1047">V oblasti **nástroje podpory** v postranním panelu vyberte **protokoly pro trasování chybných požadavků** .</span><span class="sxs-lookup"><span data-stu-id="7634a-1047">Select **Failed Request Tracing Logs** from the **SUPPORT TOOLS** area of the sidebar.</span></span>

<span data-ttu-id="7634a-1048">V [části Povolení protokolování diagnostiky pro webové aplikace v Azure App Service tématu](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) a [Nejčastější dotazy týkající se výkonu aplikace pro Web Apps v Azure: návody zapnout trasování chybných požadavků?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing) Další informace.</span><span class="sxs-lookup"><span data-stu-id="7634a-1048">See [Failed request traces section of the Enable diagnostics logging for web apps in Azure App Service topic](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) and the [Application performance FAQs for Web Apps in Azure: How do I turn on failed request tracing?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing) for more information.</span></span>

<span data-ttu-id="7634a-1049">Další informace najdete v tématu [Povolení protokolování diagnostiky pro webové aplikace v Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log).</span><span class="sxs-lookup"><span data-stu-id="7634a-1049">For more information, see [Enable diagnostics logging for web apps in Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log).</span></span>

> [!WARNING]
> <span data-ttu-id="7634a-1050">Nepovedlo se zakázat protokol stdout, což může způsobit selhání aplikace nebo serveru.</span><span class="sxs-lookup"><span data-stu-id="7634a-1050">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="7634a-1051">Velikost souboru protokolu ani počet vytvořených souborů protokolu nijak neomezuje.</span><span class="sxs-lookup"><span data-stu-id="7634a-1051">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="7634a-1052">Pro rutinu protokolování v aplikaci ASP.NET Core použijte knihovnu protokolování, která omezuje velikost souboru protokolu a otočí protokoly.</span><span class="sxs-lookup"><span data-stu-id="7634a-1052">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="7634a-1053">Další informace najdete v tématu [Zprostředkovatelé protokolování třetích stran](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="7634a-1053">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

## <a name="troubleshoot-on-iis"></a><span data-ttu-id="7634a-1054">Odstraňování potíží ve službě IIS</span><span class="sxs-lookup"><span data-stu-id="7634a-1054">Troubleshoot on IIS</span></span>

### <a name="application-event-log-iis"></a><span data-ttu-id="7634a-1055">Protokol událostí aplikace (IIS)</span><span class="sxs-lookup"><span data-stu-id="7634a-1055">Application Event Log (IIS)</span></span>

<span data-ttu-id="7634a-1056">Přístup k protokolu událostí aplikace:</span><span class="sxs-lookup"><span data-stu-id="7634a-1056">Access the Application Event Log:</span></span>

1. <span data-ttu-id="7634a-1057">Otevřete nabídku Start, vyhledejte *Prohlížeč událostí*a vyberte aplikaci **Prohlížeč událostí** .</span><span class="sxs-lookup"><span data-stu-id="7634a-1057">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="7634a-1058">V **Prohlížeč událostí**otevřete uzel **protokoly systému Windows** .</span><span class="sxs-lookup"><span data-stu-id="7634a-1058">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="7634a-1059">Výběrem **aplikace** otevřete protokol událostí aplikace.</span><span class="sxs-lookup"><span data-stu-id="7634a-1059">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="7634a-1060">Vyhledá chyby spojené s chybnou aplikací.</span><span class="sxs-lookup"><span data-stu-id="7634a-1060">Search for errors associated with the failing app.</span></span> <span data-ttu-id="7634a-1061">Chyby mají ve *zdrojovém* sloupci hodnotu *modulu IIS AspNetCore* nebo *modul IIS Express AspNetCore* .</span><span class="sxs-lookup"><span data-stu-id="7634a-1061">Errors have a value of *IIS AspNetCore Module* or *IIS Express AspNetCore Module* in the *Source* column.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="7634a-1062">Spuštění aplikace na příkazovém řádku</span><span class="sxs-lookup"><span data-stu-id="7634a-1062">Run the app at a command prompt</span></span>

<span data-ttu-id="7634a-1063">Mnoho chyb při spuštění nevytváří užitečné informace v protokolu událostí aplikace.</span><span class="sxs-lookup"><span data-stu-id="7634a-1063">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="7634a-1064">Příčinu některých chyb můžete najít spuštěním aplikace na příkazovém řádku v hostitelském systému.</span><span class="sxs-lookup"><span data-stu-id="7634a-1064">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span>

#### <a name="framework-dependent-deployment"></a><span data-ttu-id="7634a-1065">Nasazení závislé na rozhraní</span><span class="sxs-lookup"><span data-stu-id="7634a-1065">Framework-dependent deployment</span></span>

<span data-ttu-id="7634a-1066">Pokud aplikace je [nasazení závislé na rozhraní](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span><span class="sxs-lookup"><span data-stu-id="7634a-1066">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

1. <span data-ttu-id="7634a-1067">Na příkazovém řádku přejděte do složky pro nasazení a spusťte aplikaci spuštěním sestavení aplikace pomocí příkazu *dotnet. exe*.</span><span class="sxs-lookup"><span data-stu-id="7634a-1067">At a command prompt, navigate to the deployment folder and run the app by executing the app's assembly with *dotnet.exe*.</span></span> <span data-ttu-id="7634a-1068">V následujícím příkazu nahraďte název sestavení aplikace pro \<assembly_name>:. `dotnet .\<assembly_name>.dll`</span><span class="sxs-lookup"><span data-stu-id="7634a-1068">In the following command, substitute the name of the app's assembly for \<assembly_name>: `dotnet .\<assembly_name>.dll`.</span></span>
1. <span data-ttu-id="7634a-1069">Výstup konzoly z aplikace, který zobrazuje všechny chyby, je zapsán do okna konzoly.</span><span class="sxs-lookup"><span data-stu-id="7634a-1069">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="7634a-1070">Pokud dojde k chybám při podání žádosti do aplikace, vytvořte žádost na hostitele a port, kde Kestrel naslouchá.</span><span class="sxs-lookup"><span data-stu-id="7634a-1070">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="7634a-1071">Pomocí výchozího hostitele a příspěvku vytvořte žádost `http://localhost:5000/`.</span><span class="sxs-lookup"><span data-stu-id="7634a-1071">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="7634a-1072">Pokud aplikace reaguje normálně na adrese koncového bodu Kestrel, problém je pravděpodobnější v souvislosti s konfigurací hostování a méně pravděpodobným v rámci aplikace.</span><span class="sxs-lookup"><span data-stu-id="7634a-1072">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

#### <a name="self-contained-deployment"></a><span data-ttu-id="7634a-1073">Samostatně zahrnuté nasazení</span><span class="sxs-lookup"><span data-stu-id="7634a-1073">Self-contained deployment</span></span>

<span data-ttu-id="7634a-1074">Pokud je aplikace samostatná, [nasazení](/dotnet/core/deploying/#self-contained-deployments-scd):</span><span class="sxs-lookup"><span data-stu-id="7634a-1074">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

1. <span data-ttu-id="7634a-1075">Na příkazovém řádku přejděte do složky pro nasazení a spusťte spustitelný soubor aplikace.</span><span class="sxs-lookup"><span data-stu-id="7634a-1075">At a command prompt, navigate to the deployment folder and run the app's executable.</span></span> <span data-ttu-id="7634a-1076">V následujícím příkazu nahraďte název sestavení aplikace pro \<assembly_name>:. `<assembly_name>.exe`</span><span class="sxs-lookup"><span data-stu-id="7634a-1076">In the following command, substitute the name of the app's assembly for \<assembly_name>: `<assembly_name>.exe`.</span></span>
1. <span data-ttu-id="7634a-1077">Výstup konzoly z aplikace, který zobrazuje všechny chyby, je zapsán do okna konzoly.</span><span class="sxs-lookup"><span data-stu-id="7634a-1077">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="7634a-1078">Pokud dojde k chybám při podání žádosti do aplikace, vytvořte žádost na hostitele a port, kde Kestrel naslouchá.</span><span class="sxs-lookup"><span data-stu-id="7634a-1078">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="7634a-1079">Pomocí výchozího hostitele a příspěvku vytvořte žádost `http://localhost:5000/`.</span><span class="sxs-lookup"><span data-stu-id="7634a-1079">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="7634a-1080">Pokud aplikace reaguje normálně na adrese koncového bodu Kestrel, problém je pravděpodobnější v souvislosti s konfigurací hostování a méně pravděpodobným v rámci aplikace.</span><span class="sxs-lookup"><span data-stu-id="7634a-1080">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

### <a name="aspnet-core-module-stdout-log-iis"></a><span data-ttu-id="7634a-1081">Protokol stdout v modulu ASP.NET Core (IIS)</span><span class="sxs-lookup"><span data-stu-id="7634a-1081">ASP.NET Core Module stdout log (IIS)</span></span>

<span data-ttu-id="7634a-1082">Postup povolení a zobrazení protokolů stdout:</span><span class="sxs-lookup"><span data-stu-id="7634a-1082">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="7634a-1083">Přejděte do složky pro nasazení webu v hostitelském systému.</span><span class="sxs-lookup"><span data-stu-id="7634a-1083">Navigate to the site's deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="7634a-1084">Pokud složka *logs* není k dispozici, vytvořte složku.</span><span class="sxs-lookup"><span data-stu-id="7634a-1084">If the *logs* folder isn't present, create the folder.</span></span> <span data-ttu-id="7634a-1085">Pokyny k tomu, jak povolit MSBuild pro vytvoření složky *logs* v nasazení automaticky, najdete v tématu [Struktura adresáře](xref:host-and-deploy/directory-structure) .</span><span class="sxs-lookup"><span data-stu-id="7634a-1085">For instructions on how to enable MSBuild to create the *logs* folder in the deployment automatically, see the [Directory structure](xref:host-and-deploy/directory-structure) topic.</span></span>
1. <span data-ttu-id="7634a-1086">Upravte soubor *Web. config* .</span><span class="sxs-lookup"><span data-stu-id="7634a-1086">Edit the *web.config* file.</span></span> <span data-ttu-id="7634a-1087">Nastavte **stdoutLogEnabled** na `true` a změňte cestu **stdoutLogFile** tak, aby odkazovala na složku *logs* (například `.\logs\stdout`).</span><span class="sxs-lookup"><span data-stu-id="7634a-1087">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to point to the *logs* folder (for example, `.\logs\stdout`).</span></span> <span data-ttu-id="7634a-1088">`stdout`v cestě je předpona názvu souboru protokolu.</span><span class="sxs-lookup"><span data-stu-id="7634a-1088">`stdout` in the path is the log file name prefix.</span></span> <span data-ttu-id="7634a-1089">Časové razítko, ID procesu a Přípona souboru se automaticky přidají při vytvoření protokolu.</span><span class="sxs-lookup"><span data-stu-id="7634a-1089">A timestamp, process id, and file extension are added automatically when the log is created.</span></span> <span data-ttu-id="7634a-1090">Jako `stdout` Předpona názvu souboru se používá typický soubor protokolu s názvem *stdout_20180205184032_5412. log*.</span><span class="sxs-lookup"><span data-stu-id="7634a-1090">Using `stdout` as the file name prefix, a typical log file is named *stdout_20180205184032_5412.log*.</span></span>
1. <span data-ttu-id="7634a-1091">Zajistěte, aby identita fondu aplikací měla oprávnění k zápisu do složky *logs* .</span><span class="sxs-lookup"><span data-stu-id="7634a-1091">Ensure your application pool's identity has write permissions to the *logs* folder.</span></span>
1. <span data-ttu-id="7634a-1092">Uložte aktualizovaný soubor *Web. config* .</span><span class="sxs-lookup"><span data-stu-id="7634a-1092">Save the updated *web.config* file.</span></span>
1. <span data-ttu-id="7634a-1093">Vytvořte žádost do aplikace.</span><span class="sxs-lookup"><span data-stu-id="7634a-1093">Make a request to the app.</span></span>
1. <span data-ttu-id="7634a-1094">Přejděte do složky *logs* .</span><span class="sxs-lookup"><span data-stu-id="7634a-1094">Navigate to the *logs* folder.</span></span> <span data-ttu-id="7634a-1095">Vyhledejte a otevřete nejnovější protokol STDOUT.</span><span class="sxs-lookup"><span data-stu-id="7634a-1095">Find and open the most recent stdout log.</span></span>
1. <span data-ttu-id="7634a-1096">Prostudujte si protokol o chybách.</span><span class="sxs-lookup"><span data-stu-id="7634a-1096">Study the log for errors.</span></span>

<span data-ttu-id="7634a-1097">Zakázat protokolování stdout při odstraňování potíží:</span><span class="sxs-lookup"><span data-stu-id="7634a-1097">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="7634a-1098">Upravte soubor *Web. config* .</span><span class="sxs-lookup"><span data-stu-id="7634a-1098">Edit the *web.config* file.</span></span>
1. <span data-ttu-id="7634a-1099">Nastavte **stdoutLogEnabled** na `false`.</span><span class="sxs-lookup"><span data-stu-id="7634a-1099">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="7634a-1100">Uložte soubor.</span><span class="sxs-lookup"><span data-stu-id="7634a-1100">Save the file.</span></span>

<span data-ttu-id="7634a-1101">Další informace naleznete v tématu <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span><span class="sxs-lookup"><span data-stu-id="7634a-1101">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

> [!WARNING]
> <span data-ttu-id="7634a-1102">Nepovedlo se zakázat protokol stdout, což může způsobit selhání aplikace nebo serveru.</span><span class="sxs-lookup"><span data-stu-id="7634a-1102">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="7634a-1103">Velikost souboru protokolu ani počet vytvořených souborů protokolu nijak neomezuje.</span><span class="sxs-lookup"><span data-stu-id="7634a-1103">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="7634a-1104">Pro rutinu protokolování v aplikaci ASP.NET Core použijte knihovnu protokolování, která omezuje velikost souboru protokolu a otočí protokoly.</span><span class="sxs-lookup"><span data-stu-id="7634a-1104">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="7634a-1105">Další informace najdete v tématu [Zprostředkovatelé protokolování třetích stran](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="7634a-1105">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="enable-the-developer-exception-page"></a><span data-ttu-id="7634a-1106">Povolit stránku výjimky pro vývojáře</span><span class="sxs-lookup"><span data-stu-id="7634a-1106">Enable the Developer Exception Page</span></span>

<span data-ttu-id="7634a-1107">`ASPNETCORE_ENVIRONMENT` [Proměnnou prostředí lze přidat do souboru Web. config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) , aby bylo možné aplikaci spustit ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="7634a-1107">The `ASPNETCORE_ENVIRONMENT` [environment variable can be added to web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) to run the app in the Development environment.</span></span> <span data-ttu-id="7634a-1108">Pokud se prostředí nepřepisuje při spuštění `UseEnvironment` aplikace v Tvůrci hostitele, nastavení proměnné prostředí umožní, aby se při spuštění aplikace zobrazila stránka s [výjimkou vývojářů](xref:fundamentals/error-handling) .</span><span class="sxs-lookup"><span data-stu-id="7634a-1108">As long as the environment isn't overridden in app startup by `UseEnvironment` on the host builder, setting the environment variable allows the [Developer Exception Page](xref:fundamentals/error-handling) to appear when the app is run.</span></span>

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

<span data-ttu-id="7634a-1109">Nastavení proměnné prostředí pro `ASPNETCORE_ENVIRONMENT` se doporučuje jenom pro použití na pracovních a testovacích serverech, které nejsou přístupné pro Internet.</span><span class="sxs-lookup"><span data-stu-id="7634a-1109">Setting the environment variable for `ASPNETCORE_ENVIRONMENT` is only recommended for use on staging and testing servers that aren't exposed to the Internet.</span></span> <span data-ttu-id="7634a-1110">Po vyřešení potíží odeberte proměnnou prostředí ze souboru *Web. config* .</span><span class="sxs-lookup"><span data-stu-id="7634a-1110">Remove the environment variable from the *web.config* file after troubleshooting.</span></span> <span data-ttu-id="7634a-1111">Informace o nastavení proměnných prostředí v *souboru Web. config*naleznete v tématu [environmentVariables Child element of aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).</span><span class="sxs-lookup"><span data-stu-id="7634a-1111">For information on setting environment variables in *web.config*, see [environmentVariables child element of aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).</span></span>

### <a name="obtain-data-from-an-app"></a><span data-ttu-id="7634a-1112">Získání dat z aplikace</span><span class="sxs-lookup"><span data-stu-id="7634a-1112">Obtain data from an app</span></span>

<span data-ttu-id="7634a-1113">Pokud aplikace dokáže reagovat na žádosti, získat požadavek, připojení a další data z aplikace pomocí vloženého middlewaru terminálu.</span><span class="sxs-lookup"><span data-stu-id="7634a-1113">If an app is capable of responding to requests, obtain request, connection, and additional data from the app using terminal inline middleware.</span></span> <span data-ttu-id="7634a-1114">Další informace a ukázka kódu naleznete v tématu <xref:test/troubleshoot#obtain-data-from-an-app>.</span><span class="sxs-lookup"><span data-stu-id="7634a-1114">For more information and sample code, see <xref:test/troubleshoot#obtain-data-from-an-app>.</span></span>

### <a name="slow-or-hanging-app-iis"></a><span data-ttu-id="7634a-1115">Pomalá nebo zavěšená aplikace (IIS)</span><span class="sxs-lookup"><span data-stu-id="7634a-1115">Slow or hanging app (IIS)</span></span>

<span data-ttu-id="7634a-1116">*Výpis stavu* systému je snímek paměti systému a může vám pomůže určit příčinu selhání aplikace, selhání při spuštění nebo pomalé aplikace.</span><span class="sxs-lookup"><span data-stu-id="7634a-1116">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="7634a-1117">Aplikace selže nebo dojde k výjimce.</span><span class="sxs-lookup"><span data-stu-id="7634a-1117">App crashes or encounters an exception</span></span>

<span data-ttu-id="7634a-1118">Získat a analyzovat výpis z [zasílání zpráv o chybách systému Windows (WER)](/windows/desktop/wer/windows-error-reporting):</span><span class="sxs-lookup"><span data-stu-id="7634a-1118">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="7634a-1119">Vytvořte složku uchovávající soubory s výpisem stavu `c:\dumps`systému.</span><span class="sxs-lookup"><span data-stu-id="7634a-1119">Create a folder to hold crash dump files at `c:\dumps`.</span></span> <span data-ttu-id="7634a-1120">Fond aplikací musí mít ke složce přístup pro zápis.</span><span class="sxs-lookup"><span data-stu-id="7634a-1120">The app pool must have write access to the folder.</span></span>
1. <span data-ttu-id="7634a-1121">Spusťte [skript prostředí PowerShell pro EnableDumps](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1):</span><span class="sxs-lookup"><span data-stu-id="7634a-1121">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1):</span></span>
   * <span data-ttu-id="7634a-1122">Pokud aplikace používá [model hostování v rámci procesu](xref:host-and-deploy/iis/index#in-process-hosting-model), spusťte skript pro *W3wp. exe*:</span><span class="sxs-lookup"><span data-stu-id="7634a-1122">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\EnableDumps w3wp.exe c:\dumps
     ```

   * <span data-ttu-id="7634a-1123">Pokud aplikace používá [model hostování mimo proces](xref:host-and-deploy/iis/index#out-of-process-hosting-model), spusťte skript pro příkaz *dotnet. exe*:</span><span class="sxs-lookup"><span data-stu-id="7634a-1123">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\EnableDumps dotnet.exe c:\dumps
     ```

1. <span data-ttu-id="7634a-1124">Spusťte aplikaci za podmínek, které způsobí, že dojde k chybě.</span><span class="sxs-lookup"><span data-stu-id="7634a-1124">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="7634a-1125">Po chybě spusťte [skript PowerShellu DisableDumps](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1):</span><span class="sxs-lookup"><span data-stu-id="7634a-1125">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1):</span></span>
   * <span data-ttu-id="7634a-1126">Pokud aplikace používá [model hostování v rámci procesu](xref:host-and-deploy/iis/index#in-process-hosting-model), spusťte skript pro *W3wp. exe*:</span><span class="sxs-lookup"><span data-stu-id="7634a-1126">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\DisableDumps w3wp.exe
     ```

   * <span data-ttu-id="7634a-1127">Pokud aplikace používá [model hostování mimo proces](xref:host-and-deploy/iis/index#out-of-process-hosting-model), spusťte skript pro příkaz *dotnet. exe*:</span><span class="sxs-lookup"><span data-stu-id="7634a-1127">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\DisableDumps dotnet.exe
     ```

<span data-ttu-id="7634a-1128">Po selhání aplikace a dokončení shromažďování výpisu je možné aplikaci ukončit normálně.</span><span class="sxs-lookup"><span data-stu-id="7634a-1128">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="7634a-1129">Skript PowerShellu nakonfiguruje WER a shromáždí až pět výpisů paměti pro každou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="7634a-1129">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="7634a-1130">Výpisy stavu systému můžou zabírat velké množství místa na disku (každý až několik gigabajtů).</span><span class="sxs-lookup"><span data-stu-id="7634a-1130">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="7634a-1131">Aplikace přestane reagovat, dojde k chybě při spuštění nebo se spustí normálně.</span><span class="sxs-lookup"><span data-stu-id="7634a-1131">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="7634a-1132">Když *aplikace přestane reagovat (zastaví* se, ale nejedná se o chybu), selže během spuštění nebo se spustí normálně, podívejte [se na soubory výpisu paměti v uživatelském režimu: zvolením nejlepšího](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) nástroje vyberte vhodný nástroj pro vytváření výpisu.</span><span class="sxs-lookup"><span data-stu-id="7634a-1132">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="7634a-1133">Analýza výpisu paměti</span><span class="sxs-lookup"><span data-stu-id="7634a-1133">Analyze the dump</span></span>

<span data-ttu-id="7634a-1134">Výpis paměti lze analyzovat pomocí několika přístupů.</span><span class="sxs-lookup"><span data-stu-id="7634a-1134">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="7634a-1135">Další informace najdete v tématu [Analýza souboru s výpisem stavu v uživatelském režimu](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span><span class="sxs-lookup"><span data-stu-id="7634a-1135">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="clear-package-caches"></a><span data-ttu-id="7634a-1136">Vymazat mezipaměti balíčků</span><span class="sxs-lookup"><span data-stu-id="7634a-1136">Clear package caches</span></span>

<span data-ttu-id="7634a-1137">Funkční aplikace může po upgradu .NET Core SDK ve vývojovém počítači nebo změně verzí balíčku v rámci aplikace selhat okamžitě.</span><span class="sxs-lookup"><span data-stu-id="7634a-1137">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="7634a-1138">V některých případech můžou nesouvislé balíčky při provádění hlavních upgradů poškodit aplikaci.</span><span class="sxs-lookup"><span data-stu-id="7634a-1138">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="7634a-1139">Většinu těchto problémů můžete vyřešit pomocí následujících pokynů:</span><span class="sxs-lookup"><span data-stu-id="7634a-1139">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="7634a-1140">Odstraňte složky *bin* a *obj* .</span><span class="sxs-lookup"><span data-stu-id="7634a-1140">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="7634a-1141">Pomocí příkazu [dotnet All--Clear](/dotnet/core/tools/dotnet-nuget-locals) z příkazového prostředí vymažte mezipaměť balíčku.</span><span class="sxs-lookup"><span data-stu-id="7634a-1141">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="7634a-1142">Mazání mezipamětí balíčků lze také provést pomocí nástroje [NuGet. exe](https://www.nuget.org/downloads) a provedením příkazu `nuget locals all -clear`.</span><span class="sxs-lookup"><span data-stu-id="7634a-1142">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="7634a-1143">*NuGet. exe* není sada instalovaná instalace s desktopovým operačním systémem Windows a musí se získat samostatně z [webu NuGet](https://www.nuget.org/downloads).</span><span class="sxs-lookup"><span data-stu-id="7634a-1143">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="7634a-1144">Obnovte a znovu sestavte projekt.</span><span class="sxs-lookup"><span data-stu-id="7634a-1144">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="7634a-1145">Před opětovným nasazením aplikace odstraňte všechny soubory ve složce pro nasazení na serveru.</span><span class="sxs-lookup"><span data-stu-id="7634a-1145">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7634a-1146">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="7634a-1146">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:fundamentals/error-handling>
* <xref:host-and-deploy/aspnet-core-module>

### <a name="azure-documentation"></a><span data-ttu-id="7634a-1147">Dokumentace k Azure</span><span class="sxs-lookup"><span data-stu-id="7634a-1147">Azure documentation</span></span>

* [<span data-ttu-id="7634a-1148">Application Insights pro ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7634a-1148">Application Insights for ASP.NET Core</span></span>](/azure/application-insights/app-insights-asp-net-core)
* [<span data-ttu-id="7634a-1149">Část vzdálené ladění webových aplikací řešení potíží s webovou aplikací v Azure App Service pomocí sady Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7634a-1149">Remote debugging web apps section of Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug)
* [<span data-ttu-id="7634a-1150">Přehled diagnostiky Azure App Service</span><span class="sxs-lookup"><span data-stu-id="7634a-1150">Azure App Service diagnostics overview</span></span>](/azure/app-service/app-service-diagnostics)
* [<span data-ttu-id="7634a-1151">Postup: Monitorování aplikací ve službě Azure App Service</span><span class="sxs-lookup"><span data-stu-id="7634a-1151">How to: Monitor Apps in Azure App Service</span></span>](/azure/app-service/web-sites-monitor)
* [<span data-ttu-id="7634a-1152">Řešení potíží s webovou aplikací v Azure App Service pomocí sady Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7634a-1152">Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [<span data-ttu-id="7634a-1153">Řešení chyb HTTP z "502 špatné brány" a "nedostupné služby 503" v Azure Web Apps</span><span class="sxs-lookup"><span data-stu-id="7634a-1153">Troubleshoot HTTP errors of "502 bad gateway" and "503 service unavailable" in your Azure web apps</span></span>](/azure/app-service/app-service-web-troubleshoot-http-502-http-503)
* [<span data-ttu-id="7634a-1154">Řešení potíží s výkonem pomalých webových aplikací ve službě Azure App Service</span><span class="sxs-lookup"><span data-stu-id="7634a-1154">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="7634a-1155">Nejčastější dotazy týkající se výkonu aplikací pro Web Apps v Azure</span><span class="sxs-lookup"><span data-stu-id="7634a-1155">Application performance FAQs for Web Apps in Azure</span></span>](/azure/app-service/app-service-web-availability-performance-application-issues-faq)
* [<span data-ttu-id="7634a-1156">Sandbox webové aplikace Azure (omezení spuštění App Service Runtime)</span><span class="sxs-lookup"><span data-stu-id="7634a-1156">Azure Web App sandbox (App Service runtime execution limitations)</span></span>](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)
* [<span data-ttu-id="7634a-1157">Azure pátek: Azure App Service diagnostické prostředí a řešení potíží (12 minut video)</span><span class="sxs-lookup"><span data-stu-id="7634a-1157">Azure Friday: Azure App Service Diagnostic and Troubleshooting Experience (12-minute video)</span></span>](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)

### <a name="visual-studio-documentation"></a><span data-ttu-id="7634a-1158">Dokumentace sady Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7634a-1158">Visual Studio documentation</span></span>

* [<span data-ttu-id="7634a-1159">Vzdálené ladění ASP.NET Core ve službě IIS v Azure v aplikaci Visual Studio 2017</span><span class="sxs-lookup"><span data-stu-id="7634a-1159">Remote Debug ASP.NET Core on IIS in Azure in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-azure)
* [<span data-ttu-id="7634a-1160">Vzdálené ladění ASP.NET Core na vzdáleném počítači IIS v aplikaci Visual Studio 2017</span><span class="sxs-lookup"><span data-stu-id="7634a-1160">Remote Debug ASP.NET Core on a Remote IIS Computer in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-aspnet-on-a-remote-iis-computer)
* [<span data-ttu-id="7634a-1161">Informace o ladění pomocí sady Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7634a-1161">Learn to debug using Visual Studio</span></span>](/visualstudio/debugger/getting-started-with-the-debugger)

### <a name="visual-studio-code-documentation"></a><span data-ttu-id="7634a-1162">Dokumentace k Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7634a-1162">Visual Studio Code documentation</span></span>

* [<span data-ttu-id="7634a-1163">Ladění pomocí Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7634a-1163">Debugging with Visual Studio Code</span></span>](https://code.visualstudio.com/docs/editor/debugging)

::: moniker-end
