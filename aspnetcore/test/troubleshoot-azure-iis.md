---
title: Řešení potíží s ASP.NET Core v Azure App Service a IIS
author: rick-anderson
description: Naučte se diagnostikovat problémy s Azure App Service a Internetová informační služba nasazení ASP.NET Core aplikací (IIS).
monikerRange: '>= aspnetcore-2.1'
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
uid: test/troubleshoot-azure-iis
ms.openlocfilehash: bc968489a35405c1be0a4ac2e52e6dff4c3af050
ms.sourcegitcommit: 8fcb08312a59c37e3542e7a67dad25faf5bb8e76
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2020
ms.locfileid: "90009736"
---
# <a name="troubleshoot-aspnet-core-on-azure-app-service-and-iis"></a><span data-ttu-id="a560a-103">Řešení potíží s ASP.NET Core v Azure App Service a IIS</span><span class="sxs-lookup"><span data-stu-id="a560a-103">Troubleshoot ASP.NET Core on Azure App Service and IIS</span></span>

<span data-ttu-id="a560a-104">Od [Justin Kotalik](https://github.com/jkotalik)</span><span class="sxs-lookup"><span data-stu-id="a560a-104">By [Justin Kotalik](https://github.com/jkotalik)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="a560a-105">Tento článek poskytuje informace o běžných chybách při spuštění aplikací a pokyny k diagnostice chyb při nasazení aplikace do Azure App Service nebo IIS:</span><span class="sxs-lookup"><span data-stu-id="a560a-105">This article provides information on common app startup errors and instructions on how to diagnose errors when an app is deployed to Azure App Service or IIS:</span></span>

[<span data-ttu-id="a560a-106">Chyby při spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="a560a-106">App startup errors</span></span>](#app-startup-errors)  
<span data-ttu-id="a560a-107">Vysvětluje běžné spouštění scénářů stavového kódu HTTP.</span><span class="sxs-lookup"><span data-stu-id="a560a-107">Explains common startup HTTP status code scenarios.</span></span>

[<span data-ttu-id="a560a-108">Řešení potíží na Azure App Service</span><span class="sxs-lookup"><span data-stu-id="a560a-108">Troubleshoot on Azure App Service</span></span>](#troubleshoot-on-azure-app-service)  
<span data-ttu-id="a560a-109">Poskytuje rady pro řešení potíží pro aplikace nasazené do Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="a560a-109">Provides troubleshooting advice for apps deployed to Azure App Service.</span></span>

[<span data-ttu-id="a560a-110">Odstraňování potíží ve službě IIS</span><span class="sxs-lookup"><span data-stu-id="a560a-110">Troubleshoot on IIS</span></span>](#troubleshoot-on-iis)  
<span data-ttu-id="a560a-111">Poskytuje rady pro řešení potíží pro aplikace nasazené do služby IIS nebo místně spuštěné v IIS Express.</span><span class="sxs-lookup"><span data-stu-id="a560a-111">Provides troubleshooting advice for apps deployed to IIS or running on IIS Express locally.</span></span> <span data-ttu-id="a560a-112">Pokyny platí pro nasazení systémů Windows Server a Desktop v systému Windows.</span><span class="sxs-lookup"><span data-stu-id="a560a-112">The guidance applies to both Windows Server and Windows desktop deployments.</span></span>

[<span data-ttu-id="a560a-113">Vymazat mezipaměti balíčků</span><span class="sxs-lookup"><span data-stu-id="a560a-113">Clear package caches</span></span>](#clear-package-caches)  
<span data-ttu-id="a560a-114">Vysvětluje, co dělat v případě, že při provádění podstatných upgradů nebo změně verze balíčku dojde k přerušení aplikace v nesouvislých balíčcích.</span><span class="sxs-lookup"><span data-stu-id="a560a-114">Explains what to do when incoherent packages break an app when performing major upgrades or changing package versions.</span></span>

[<span data-ttu-id="a560a-115">Další materiály</span><span class="sxs-lookup"><span data-stu-id="a560a-115">Additional resources</span></span>](#additional-resources)  
<span data-ttu-id="a560a-116">Seznam dalších témat pro odstraňování potíží.</span><span class="sxs-lookup"><span data-stu-id="a560a-116">Lists additional troubleshooting topics.</span></span>

## <a name="app-startup-errors"></a><span data-ttu-id="a560a-117">Chyby při spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="a560a-117">App startup errors</span></span>

<span data-ttu-id="a560a-118">V aplikaci Visual Studio je výchozím nastavením projektu ASP.NET Core [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hostování během ladění.</span><span class="sxs-lookup"><span data-stu-id="a560a-118">In Visual Studio, an ASP.NET Core project defaults to [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hosting during debugging.</span></span> <span data-ttu-id="a560a-119">*502,5 – selhání procesu* nebo *500,30 – chyba* , ke které dojde při místním ladění, se dá diagnostikovat pomocí Rady v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="a560a-119">A *502.5 - Process Failure* or a *500.30 - Start Failure* that occurs when debugging locally can be diagnosed using the advice in this topic.</span></span>

### <a name="40314-forbidden"></a><span data-ttu-id="a560a-120">403,14 zakázané</span><span class="sxs-lookup"><span data-stu-id="a560a-120">403.14 Forbidden</span></span>

<span data-ttu-id="a560a-121">Aplikaci se nepodařilo spustit.</span><span class="sxs-lookup"><span data-stu-id="a560a-121">The app fails to start.</span></span> <span data-ttu-id="a560a-122">Protokoluje se následující chyba:</span><span class="sxs-lookup"><span data-stu-id="a560a-122">The following error is logged:</span></span>

```
The Web server is configured to not list the contents of this directory.
```

<span data-ttu-id="a560a-123">K této chybě obvykle dochází v důsledku poškozeného nasazení v hostitelském systému, který zahrnuje kterýkoli z následujících scénářů:</span><span class="sxs-lookup"><span data-stu-id="a560a-123">The error is usually caused by a broken deployment on the hosting system, which includes any of the following scenarios:</span></span>

* <span data-ttu-id="a560a-124">Aplikace je nasazená do chybné složky v hostitelském systému.</span><span class="sxs-lookup"><span data-stu-id="a560a-124">The app is deployed to the wrong folder on the hosting system.</span></span>
* <span data-ttu-id="a560a-125">Procesu nasazení se nepodařilo přesunout všechny soubory a složky aplikace do složky pro nasazení v hostitelském systému.</span><span class="sxs-lookup"><span data-stu-id="a560a-125">The deployment process failed to move all of the app's files and folders to the deployment folder on the hosting system.</span></span>
* <span data-ttu-id="a560a-126">V nasazení chybí soubor *web.config* , nebo je obsah souboru *web.config* poškozený.</span><span class="sxs-lookup"><span data-stu-id="a560a-126">The *web.config* file is missing from the deployment, or the *web.config* file contents are malformed.</span></span>

<span data-ttu-id="a560a-127">Proveďte tyto kroky:</span><span class="sxs-lookup"><span data-stu-id="a560a-127">Perform the following steps:</span></span>

1. <span data-ttu-id="a560a-128">Odstraňte všechny soubory a složky ze složky pro nasazení v hostitelském systému.</span><span class="sxs-lookup"><span data-stu-id="a560a-128">Delete all of the files and folders from the deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="a560a-129">Znovu nasaďte obsah složky pro *publikování* aplikace do hostitelského systému pomocí běžné metody nasazení, jako je například Visual Studio, PowerShell nebo ruční nasazení:</span><span class="sxs-lookup"><span data-stu-id="a560a-129">Redeploy the contents of the app's *publish* folder to the hosting system using your normal method of deployment, such as Visual Studio, PowerShell, or manual deployment:</span></span>
   * <span data-ttu-id="a560a-130">Potvrďte, že se soubor *web.config* v nasazení nachází a že jeho obsah je správný.</span><span class="sxs-lookup"><span data-stu-id="a560a-130">Confirm that the *web.config* file is present in the deployment and that its contents are correct.</span></span>
   * <span data-ttu-id="a560a-131">Při hostování na Azure App Service potvrďte, že je aplikace nasazená do `D:\home\site\wwwroot` složky.</span><span class="sxs-lookup"><span data-stu-id="a560a-131">When hosting on Azure App Service, confirm that the app is deployed to the `D:\home\site\wwwroot` folder.</span></span>
   * <span data-ttu-id="a560a-132">Když je aplikace hostovaná službou IIS, zkontrolujte, že je aplikace nasazená na **fyzickou cestu** služby IIS, která se zobrazuje v **základním nastavení** **Správce služby IIS**.</span><span class="sxs-lookup"><span data-stu-id="a560a-132">When the app is hosted by IIS, confirm that the app is deployed to the IIS **Physical path** shown in **IIS Manager**'s **Basic Settings**.</span></span>
1. <span data-ttu-id="a560a-133">Ověřte, zda jsou všechny soubory a složky aplikace nasazeny porovnáním nasazení v hostitelském systému s obsahem složky *publikování* projektu.</span><span class="sxs-lookup"><span data-stu-id="a560a-133">Confirm that all of the app's files and folders are deployed by comparing the deployment on the hosting system to the contents of the project's *publish* folder.</span></span>

<span data-ttu-id="a560a-134">Další informace o rozložení publikované aplikace ASP.NET Core najdete v tématu <xref:host-and-deploy/directory-structure> .</span><span class="sxs-lookup"><span data-stu-id="a560a-134">For more information on the layout of a published ASP.NET Core app, see <xref:host-and-deploy/directory-structure>.</span></span> <span data-ttu-id="a560a-135">Další informace o souboru *web.config* naleznete v tématu <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig> .</span><span class="sxs-lookup"><span data-stu-id="a560a-135">For more information on the *web.config* file, see <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>.</span></span>

### <a name="500-internal-server-error"></a><span data-ttu-id="a560a-136">500 – Vnitřní chyba serveru</span><span class="sxs-lookup"><span data-stu-id="a560a-136">500 Internal Server Error</span></span>

<span data-ttu-id="a560a-137">Aplikace se spustí, ale chyba brání serveru v splnění žádosti.</span><span class="sxs-lookup"><span data-stu-id="a560a-137">The app starts, but an error prevents the server from fulfilling the request.</span></span>

<span data-ttu-id="a560a-138">K této chybě dochází v kódu aplikace během spuštění nebo při vytváření odpovědi.</span><span class="sxs-lookup"><span data-stu-id="a560a-138">This error occurs within the app's code during startup or while creating a response.</span></span> <span data-ttu-id="a560a-139">Odpověď možná neobsahuje žádný obsah, nebo se odpověď může zobrazit jako *500 interní chyba serveru* v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="a560a-139">The response may contain no content, or the response may appear as a *500 Internal Server Error* in the browser.</span></span> <span data-ttu-id="a560a-140">Protokol událostí aplikace obvykle uvádí, že aplikace začala normálně.</span><span class="sxs-lookup"><span data-stu-id="a560a-140">The Application Event Log usually states that the app started normally.</span></span> <span data-ttu-id="a560a-141">Z hlediska serveru je to správné.</span><span class="sxs-lookup"><span data-stu-id="a560a-141">From the server's perspective, that's correct.</span></span> <span data-ttu-id="a560a-142">Aplikace se spustila, ale nemůže vygenerovat platnou odpověď.</span><span class="sxs-lookup"><span data-stu-id="a560a-142">The app did start, but it can't generate a valid response.</span></span> <span data-ttu-id="a560a-143">Spusťte aplikaci na příkazovém řádku na serveru nebo povolte řešení potíží pomocí protokolu stdout modulu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a560a-143">Run the app at a command prompt on the server or enable the ASP.NET Core Module stdout log to troubleshoot the problem.</span></span>

### <a name="5000-in-process-handler-load-failure"></a><span data-ttu-id="a560a-144">500,0 selhání načtení obslužné rutiny v procesu</span><span class="sxs-lookup"><span data-stu-id="a560a-144">500.0 In-Process Handler Load Failure</span></span>

<span data-ttu-id="a560a-145">Pracovní proces se nezdařil.</span><span class="sxs-lookup"><span data-stu-id="a560a-145">The worker process fails.</span></span> <span data-ttu-id="a560a-146">Aplikace se nespustí.</span><span class="sxs-lookup"><span data-stu-id="a560a-146">The app doesn't start.</span></span>

<span data-ttu-id="a560a-147">Při načítání komponent [modulu ASP.NET Core](xref:host-and-deploy/aspnet-core-module) došlo k neznámé chybě.</span><span class="sxs-lookup"><span data-stu-id="a560a-147">An unknown error occurred loading [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) components.</span></span> <span data-ttu-id="a560a-148">Proveďte jednu z následujících akcí:</span><span class="sxs-lookup"><span data-stu-id="a560a-148">Take one of the following actions:</span></span>

* <span data-ttu-id="a560a-149">Kontaktujte [Podpora Microsoftu](https://support.microsoft.com/oas/default.aspx?prid=15832) (vyberte **vývojářské nástroje** potom **ASP.NET Core**).</span><span class="sxs-lookup"><span data-stu-id="a560a-149">Contact [Microsoft Support](https://support.microsoft.com/oas/default.aspx?prid=15832) (select **Developer Tools** then **ASP.NET Core**).</span></span>
* <span data-ttu-id="a560a-150">Položte otázku na Stack Overflow.</span><span class="sxs-lookup"><span data-stu-id="a560a-150">Ask a question on Stack Overflow.</span></span>
* <span data-ttu-id="a560a-151">Vydejte problém do našeho [úložiště GitHub](https://github.com/dotnet/AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="a560a-151">File an issue on our [GitHub repository](https://github.com/dotnet/AspNetCore).</span></span>

### <a name="50030-in-process-startup-failure"></a><span data-ttu-id="a560a-152">500,30 selhání při spuštění v procesu</span><span class="sxs-lookup"><span data-stu-id="a560a-152">500.30 In-Process Startup Failure</span></span>

<span data-ttu-id="a560a-153">Pracovní proces se nezdařil.</span><span class="sxs-lookup"><span data-stu-id="a560a-153">The worker process fails.</span></span> <span data-ttu-id="a560a-154">Aplikace se nespustí.</span><span class="sxs-lookup"><span data-stu-id="a560a-154">The app doesn't start.</span></span>

<span data-ttu-id="a560a-155">[Modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module) se pokusí spustit modul CLR .NET Core v procesu, ale jeho spuštění se nezdařilo.</span><span class="sxs-lookup"><span data-stu-id="a560a-155">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the .NET Core CLR in-process, but it fails to start.</span></span> <span data-ttu-id="a560a-156">Příčinou selhání spuštění procesu může být obvykle určení z položek protokolu událostí aplikace a protokolu stdout modulu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a560a-156">The cause of a process startup failure can usually be determined from entries in the Application Event Log and the ASP.NET Core Module stdout log.</span></span>

<span data-ttu-id="a560a-157">Běžné podmínky selhání:</span><span class="sxs-lookup"><span data-stu-id="a560a-157">Common failure conditions:</span></span>

* <span data-ttu-id="a560a-158">Aplikace je nesprávně nakonfigurovaná kvůli cílení na verzi ASP.NET Core sdílené rozhraní, které není k dispozici.</span><span class="sxs-lookup"><span data-stu-id="a560a-158">The app is misconfigured due to targeting a version of the ASP.NET Core shared framework that isn't present.</span></span> <span data-ttu-id="a560a-159">Ověřte, které verze ASP.NET Core sdílené rozhraní jsou nainstalovány v cílovém počítači.</span><span class="sxs-lookup"><span data-stu-id="a560a-159">Check which versions of the ASP.NET Core shared framework are installed on the target machine.</span></span>
* <span data-ttu-id="a560a-160">Při použití Azure Key Vault chybí oprávnění Key Vault.</span><span class="sxs-lookup"><span data-stu-id="a560a-160">Using Azure Key Vault, lack of permissions to the Key Vault.</span></span> <span data-ttu-id="a560a-161">Zkontrolujte zásady přístupu v cílovém Key Vault, abyste měli jistotu, že jsou udělená správná oprávnění.</span><span class="sxs-lookup"><span data-stu-id="a560a-161">Check the access policies in the targeted Key Vault to ensure that the correct permissions are granted.</span></span>

### <a name="50031-ancm-failed-to-find-native-dependencies"></a><span data-ttu-id="a560a-162">500,31 ANCM nepovedlo se najít nativní závislosti.</span><span class="sxs-lookup"><span data-stu-id="a560a-162">500.31 ANCM Failed to Find Native Dependencies</span></span>

<span data-ttu-id="a560a-163">Pracovní proces se nezdařil.</span><span class="sxs-lookup"><span data-stu-id="a560a-163">The worker process fails.</span></span> <span data-ttu-id="a560a-164">Aplikace se nespustí.</span><span class="sxs-lookup"><span data-stu-id="a560a-164">The app doesn't start.</span></span>

<span data-ttu-id="a560a-165">[Modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module) se pokusí spustit modul runtime .NET Core v procesu, ale jeho spuštění se nezdařilo.</span><span class="sxs-lookup"><span data-stu-id="a560a-165">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the .NET Core runtime in-process, but it fails to start.</span></span> <span data-ttu-id="a560a-166">Nejběžnější příčinou tohoto selhání při spuštění je, že `Microsoft.NETCore.App` `Microsoft.AspNetCore.App` modul runtime nebo není nainstalován.</span><span class="sxs-lookup"><span data-stu-id="a560a-166">The most common cause of this startup failure is when the `Microsoft.NETCore.App` or `Microsoft.AspNetCore.App` runtime isn't installed.</span></span> <span data-ttu-id="a560a-167">Pokud je aplikace nasazená na cílovou ASP.NET Core 3,0 a tato verze v počítači neexistuje, dojde k této chybě.</span><span class="sxs-lookup"><span data-stu-id="a560a-167">If the app is deployed to target ASP.NET Core 3.0 and that version doesn't exist on the machine, this error occurs.</span></span> <span data-ttu-id="a560a-168">Následuje příklad chybové zprávy:</span><span class="sxs-lookup"><span data-stu-id="a560a-168">An example error message follows:</span></span>

```
The specified framework 'Microsoft.NETCore.App', version '3.0.0' was not found.
  - The following frameworks were found:
      2.2.1 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview5-27626-15 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27713-13 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27714-15 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27723-08 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
```

<span data-ttu-id="a560a-169">Chybová zpráva obsahuje seznam všech nainstalovaných verzí rozhraní .NET Core a verze, kterou aplikace požaduje.</span><span class="sxs-lookup"><span data-stu-id="a560a-169">The error message lists all the installed .NET Core versions and the version requested by the app.</span></span> <span data-ttu-id="a560a-170">Chcete-li tuto chybu vyřešit, postupujte takto:</span><span class="sxs-lookup"><span data-stu-id="a560a-170">To fix this error, either:</span></span>

* <span data-ttu-id="a560a-171">Na počítač nainstalujte odpovídající verzi .NET Core.</span><span class="sxs-lookup"><span data-stu-id="a560a-171">Install the appropriate version of .NET Core on the machine.</span></span>
* <span data-ttu-id="a560a-172">Změňte aplikaci na cílovou verzi .NET Core, která je na počítači přítomná.</span><span class="sxs-lookup"><span data-stu-id="a560a-172">Change the app to target a version of .NET Core that's present on the machine.</span></span>
* <span data-ttu-id="a560a-173">Publikujte aplikaci jako samostatné [nasazení](/dotnet/core/deploying/#self-contained-deployments-scd).</span><span class="sxs-lookup"><span data-stu-id="a560a-173">Publish the app as a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>

<span data-ttu-id="a560a-174">Při spuštění ve vývoji ( `ASPNETCORE_ENVIRONMENT` Proměnná prostředí je nastavená na hodnotu `Development` ) se konkrétní chyba zapíše do odpovědi HTTP.</span><span class="sxs-lookup"><span data-stu-id="a560a-174">When running in development (the `ASPNETCORE_ENVIRONMENT` environment variable is set to `Development`), the specific error is written to the HTTP response.</span></span> <span data-ttu-id="a560a-175">Příčinu selhání spuštění procesu se nachází také v protokolu událostí aplikace.</span><span class="sxs-lookup"><span data-stu-id="a560a-175">The cause of a process startup failure is also found in the Application Event Log.</span></span>

### <a name="50032-ancm-failed-to-load-dll"></a><span data-ttu-id="a560a-176">500,32 ANCM načtení knihovny DLL se nezdařilo</span><span class="sxs-lookup"><span data-stu-id="a560a-176">500.32 ANCM Failed to Load dll</span></span>

<span data-ttu-id="a560a-177">Pracovní proces se nezdařil.</span><span class="sxs-lookup"><span data-stu-id="a560a-177">The worker process fails.</span></span> <span data-ttu-id="a560a-178">Aplikace se nespustí.</span><span class="sxs-lookup"><span data-stu-id="a560a-178">The app doesn't start.</span></span>

<span data-ttu-id="a560a-179">Nejběžnější příčinou této chyby je, že aplikace je publikovaná pro nekompatibilní architekturu procesoru.</span><span class="sxs-lookup"><span data-stu-id="a560a-179">The most common cause for this error is that the app is published for an incompatible processor architecture.</span></span> <span data-ttu-id="a560a-180">Pokud je pracovní proces spuštěn jako 32 aplikace a aplikace byla publikována na cílovou 64-bit, dojde k této chybě.</span><span class="sxs-lookup"><span data-stu-id="a560a-180">If the worker process is running as a 32-bit app and the app was published to target 64-bit, this error occurs.</span></span>

<span data-ttu-id="a560a-181">Chcete-li tuto chybu vyřešit, postupujte takto:</span><span class="sxs-lookup"><span data-stu-id="a560a-181">To fix this error, either:</span></span>

* <span data-ttu-id="a560a-182">Znovu publikujte aplikaci pro stejnou architekturu procesoru jako pracovní proces.</span><span class="sxs-lookup"><span data-stu-id="a560a-182">Republish the app for the same processor architecture as the worker process.</span></span>
* <span data-ttu-id="a560a-183">Publikování aplikace jako [nasazení závislého na rozhraní](/dotnet/core/deploying/#framework-dependent-executables-fde).</span><span class="sxs-lookup"><span data-stu-id="a560a-183">Publish the app as a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-executables-fde).</span></span>

### <a name="50033-ancm-request-handler-load-failure"></a><span data-ttu-id="a560a-184">500,33 selhání načtení obslužné rutiny žádosti ANCM</span><span class="sxs-lookup"><span data-stu-id="a560a-184">500.33 ANCM Request Handler Load Failure</span></span>

<span data-ttu-id="a560a-185">Pracovní proces se nezdařil.</span><span class="sxs-lookup"><span data-stu-id="a560a-185">The worker process fails.</span></span> <span data-ttu-id="a560a-186">Aplikace se nespustí.</span><span class="sxs-lookup"><span data-stu-id="a560a-186">The app doesn't start.</span></span>

<span data-ttu-id="a560a-187">Aplikace neodkazovala na `Microsoft.AspNetCore.App` rozhraní.</span><span class="sxs-lookup"><span data-stu-id="a560a-187">The app didn't reference the `Microsoft.AspNetCore.App` framework.</span></span> <span data-ttu-id="a560a-188">`Microsoft.AspNetCore.App` [Modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module)může hostovat jenom aplikace cílené na rozhraní.</span><span class="sxs-lookup"><span data-stu-id="a560a-188">Only apps targeting the `Microsoft.AspNetCore.App` framework can be hosted by the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span>

<span data-ttu-id="a560a-189">Chcete-li tuto chybu opravit, zkontrolujte, zda je aplikace cílena na `Microsoft.AspNetCore.App` rozhraní.</span><span class="sxs-lookup"><span data-stu-id="a560a-189">To fix this error, confirm that the app is targeting the `Microsoft.AspNetCore.App` framework.</span></span> <span data-ttu-id="a560a-190">Zkontrolujte, `.runtimeconfig.json` jestli chcete ověřit architekturu, na kterou aplikace cílí.</span><span class="sxs-lookup"><span data-stu-id="a560a-190">Check the `.runtimeconfig.json` to verify the framework targeted by the app.</span></span>

### <a name="50034-ancm-mixed-hosting-models-not-supported"></a><span data-ttu-id="a560a-191">500,34 ANCM smíšené modely hostování se nepodporují.</span><span class="sxs-lookup"><span data-stu-id="a560a-191">500.34 ANCM Mixed Hosting Models Not Supported</span></span>

<span data-ttu-id="a560a-192">Pracovní proces nemůže v jednom procesu spustit jak aplikaci v rámci procesu, tak i aplikaci mimo proces.</span><span class="sxs-lookup"><span data-stu-id="a560a-192">The worker process can't run both an in-process app and an out-of-process app in the same process.</span></span>

<span data-ttu-id="a560a-193">Chcete-li tuto chybu opravit, spusťte aplikace v samostatných fondech aplikací služby IIS.</span><span class="sxs-lookup"><span data-stu-id="a560a-193">To fix this error, run apps in separate IIS application pools.</span></span>

### <a name="50035-ancm-multiple-in-process-applications-in-same-process"></a><span data-ttu-id="a560a-194">500,35 ANCM více vnitroprocesové aplikací ve stejném procesu</span><span class="sxs-lookup"><span data-stu-id="a560a-194">500.35 ANCM Multiple In-Process Applications in same Process</span></span>

<span data-ttu-id="a560a-195">Pracovní proces nemůže v jednom procesu spustit více vnitroprocesové aplikací.</span><span class="sxs-lookup"><span data-stu-id="a560a-195">The worker process can't run multiple in-process apps in the same process.</span></span>

<span data-ttu-id="a560a-196">Chcete-li tuto chybu opravit, spusťte aplikace v samostatných fondech aplikací služby IIS.</span><span class="sxs-lookup"><span data-stu-id="a560a-196">To fix this error, run apps in separate IIS application pools.</span></span>

### <a name="50036-ancm-out-of-process-handler-load-failure"></a><span data-ttu-id="a560a-197">500,36 ANCM selhání načtení obslužné rutiny mimo proces</span><span class="sxs-lookup"><span data-stu-id="a560a-197">500.36 ANCM Out-Of-Process Handler Load Failure</span></span>

<span data-ttu-id="a560a-198">Obslužná rutina žádosti mimo proces, *aspnetcorev2_outofprocess.dll*, není vedle \*aspnetcorev2.dllho \* souboru.</span><span class="sxs-lookup"><span data-stu-id="a560a-198">The out-of-process request handler, *aspnetcorev2_outofprocess.dll*, isn't next to the *aspnetcorev2.dll* file.</span></span> <span data-ttu-id="a560a-199">To označuje poškozenou instalaci [modulu ASP.NET Core](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="a560a-199">This indicates a corrupted installation of the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span>

<span data-ttu-id="a560a-200">Chcete-li tuto chybu opravit, opravte instalaci [hostující sady .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) (pro IIS) nebo Visual Studio (pro IIS Express).</span><span class="sxs-lookup"><span data-stu-id="a560a-200">To fix this error, repair the installation of the [.NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) (for IIS) or Visual Studio (for IIS Express).</span></span>

### <a name="50037-ancm-failed-to-start-within-startup-time-limit"></a><span data-ttu-id="a560a-201">500,37 ANCM se nepodařilo spustit v časovém limitu spuštění.</span><span class="sxs-lookup"><span data-stu-id="a560a-201">500.37 ANCM Failed to Start Within Startup Time Limit</span></span>

<span data-ttu-id="a560a-202">ANCM se nepovedlo spustit v zadaném časovém limitu spuštění.</span><span class="sxs-lookup"><span data-stu-id="a560a-202">ANCM failed to start within the provided startup time limit.</span></span> <span data-ttu-id="a560a-203">Ve výchozím nastavení je časový limit 120 sekund.</span><span class="sxs-lookup"><span data-stu-id="a560a-203">By default, the timeout is 120 seconds.</span></span>

<span data-ttu-id="a560a-204">K této chybě může dojít, když ve stejném počítači spustíte velký počet aplikací.</span><span class="sxs-lookup"><span data-stu-id="a560a-204">This error can occur when starting a large number of apps on the same machine.</span></span> <span data-ttu-id="a560a-205">Kontrolovat špičky využití procesoru a paměti na serveru při spuštění.</span><span class="sxs-lookup"><span data-stu-id="a560a-205">Check for CPU/Memory usage spikes on the server during startup.</span></span> <span data-ttu-id="a560a-206">Možná budete muset rozložit proces spouštění více aplikací.</span><span class="sxs-lookup"><span data-stu-id="a560a-206">You may need to stagger the startup process of multiple apps.</span></span>

### <a name="50038-ancm-application-dll-not-found"></a><span data-ttu-id="a560a-207">500,38 knihovna DLL aplikace ANCM nebyla nalezena.</span><span class="sxs-lookup"><span data-stu-id="a560a-207">500.38 ANCM Application DLL Not Found</span></span>

<span data-ttu-id="a560a-208">ANCM se nepodařilo najít knihovnu DLL aplikace, která by měla být vedle spustitelného souboru.</span><span class="sxs-lookup"><span data-stu-id="a560a-208">ANCM failed to locate the application DLL, which should be next to the executable.</span></span>

<span data-ttu-id="a560a-209">K této chybě dochází, když hostuje aplikaci zabalenou jako spustitelný soubor s [jedním souborem](/dotnet/core/whats-new/dotnet-core-3-0#single-file-executables) pomocí modelu hostování v rámci procesu.</span><span class="sxs-lookup"><span data-stu-id="a560a-209">This error occurs when hosting an app packaged as a [single-file executable](/dotnet/core/whats-new/dotnet-core-3-0#single-file-executables) using the in-process hosting model.</span></span> <span data-ttu-id="a560a-210">Model in-Process vyžaduje, aby ANCM do existujícího procesu IIS načetl aplikaci .NET Core.</span><span class="sxs-lookup"><span data-stu-id="a560a-210">The in-process model requires that the ANCM load the .NET Core app into the existing IIS process.</span></span> <span data-ttu-id="a560a-211">Tento scénář není podporován modelem nasazení s jedním souborem.</span><span class="sxs-lookup"><span data-stu-id="a560a-211">This scenario isn't supported by the single-file deployment model.</span></span> <span data-ttu-id="a560a-212">Tuto chybu můžete vyřešit pomocí **jednoho** z následujících přístupů v souboru projektu aplikace:</span><span class="sxs-lookup"><span data-stu-id="a560a-212">Use **one** of the following approaches in the app's project file to fix this error:</span></span>

1. <span data-ttu-id="a560a-213">Zakažte publikování jedním souborem nastavením `PublishSingleFile` vlastnosti MSBuild na `false` .</span><span class="sxs-lookup"><span data-stu-id="a560a-213">Disable single-file publishing by setting the `PublishSingleFile` MSBuild property to `false`.</span></span>
1. <span data-ttu-id="a560a-214">Přepněte do nezpracovaného modelu hostování nastavením `AspNetCoreHostingModel` vlastnosti MSBuild na `OutOfProcess` .</span><span class="sxs-lookup"><span data-stu-id="a560a-214">Switch to the out-of-process hosting model by setting the `AspNetCoreHostingModel` MSBuild property to `OutOfProcess`.</span></span>

### <a name="5025-process-failure"></a><span data-ttu-id="a560a-215">502.5 – Selhání procesu</span><span class="sxs-lookup"><span data-stu-id="a560a-215">502.5 Process Failure</span></span>

<span data-ttu-id="a560a-216">Pracovní proces se nezdařil.</span><span class="sxs-lookup"><span data-stu-id="a560a-216">The worker process fails.</span></span> <span data-ttu-id="a560a-217">Aplikace se nespustí.</span><span class="sxs-lookup"><span data-stu-id="a560a-217">The app doesn't start.</span></span>

<span data-ttu-id="a560a-218">[Modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module) se pokusí spustit pracovní proces, ale jeho spuštění se nezdařilo.</span><span class="sxs-lookup"><span data-stu-id="a560a-218">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the worker process but it fails to start.</span></span> <span data-ttu-id="a560a-219">Příčinou selhání spuštění procesu může být obvykle určení z položek protokolu událostí aplikace a protokolu stdout modulu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a560a-219">The cause of a process startup failure can usually be determined from entries in the Application Event Log and the ASP.NET Core Module stdout log.</span></span>

<span data-ttu-id="a560a-220">Obvyklou chybovou podmínkou je, že aplikace je nesprávně nakonfigurovaná kvůli cílení na verzi ASP.NET Core sdílené rozhraní, které není k dispozici.</span><span class="sxs-lookup"><span data-stu-id="a560a-220">A common failure condition is the app is misconfigured due to targeting a version of the ASP.NET Core shared framework that isn't present.</span></span> <span data-ttu-id="a560a-221">Ověřte, které verze ASP.NET Core sdílené rozhraní jsou nainstalovány v cílovém počítači.</span><span class="sxs-lookup"><span data-stu-id="a560a-221">Check which versions of the ASP.NET Core shared framework are installed on the target machine.</span></span> <span data-ttu-id="a560a-222">*Sdílené rozhraní* je sada sestavení (soubory *. dll* ), které jsou nainstalovány na počítači a na které odkazuje Metapackage jako `Microsoft.AspNetCore.App` .</span><span class="sxs-lookup"><span data-stu-id="a560a-222">The *shared framework* is the set of assemblies (*.dll* files) that are installed on the machine and referenced by a metapackage such as `Microsoft.AspNetCore.App`.</span></span> <span data-ttu-id="a560a-223">Odkaz Metapackage může určovat minimální požadovanou verzi.</span><span class="sxs-lookup"><span data-stu-id="a560a-223">The metapackage reference can specify a minimum required version.</span></span> <span data-ttu-id="a560a-224">Další informace najdete v tématu [sdílené rozhraní](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span><span class="sxs-lookup"><span data-stu-id="a560a-224">For more information, see [The shared framework](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span></span>

<span data-ttu-id="a560a-225">Chybová stránka *selhání procesu 502,5* se vrátí v případě, že při selhání hostování nebo aplikace dojde k chybě pracovního procesu:</span><span class="sxs-lookup"><span data-stu-id="a560a-225">The *502.5 Process Failure* error page is returned when a hosting or app misconfiguration causes the worker process to fail:</span></span>

### <a name="failed-to-start-application-errorcode-0x800700c1"></a><span data-ttu-id="a560a-226">Nepovedlo se spustit aplikaci (ErrorCode ' 0x800700C1 ')</span><span class="sxs-lookup"><span data-stu-id="a560a-226">Failed to start application (ErrorCode '0x800700c1')</span></span>

```
EventID: 1010
Source: IIS AspNetCore Module V2
Failed to start application '/LM/W3SVC/6/ROOT/', ErrorCode '0x800700c1'.
```

<span data-ttu-id="a560a-227">Aplikaci se nepovedlo spustit, protože se nepovedlo načíst sestavení aplikace (*. dll*).</span><span class="sxs-lookup"><span data-stu-id="a560a-227">The app failed to start because the app's assembly (*.dll*) couldn't be loaded.</span></span>

<span data-ttu-id="a560a-228">K této chybě dochází, pokud dojde k neshodě bitová verze mezi publikovanou aplikací a procesem W3wp/IISExpress.</span><span class="sxs-lookup"><span data-stu-id="a560a-228">This error occurs when there's a bitness mismatch between the published app and the w3wp/iisexpress process.</span></span>

<span data-ttu-id="a560a-229">Zkontrolujte, jestli je správně nastavené nastavení 32 fondu aplikací:</span><span class="sxs-lookup"><span data-stu-id="a560a-229">Confirm that the app pool's 32-bit setting is correct:</span></span>

1. <span data-ttu-id="a560a-230">Vyberte fond aplikací v **fondech aplikací**Správce služby IIS.</span><span class="sxs-lookup"><span data-stu-id="a560a-230">Select the app pool in IIS Manager's **Application Pools**.</span></span>
1. <span data-ttu-id="a560a-231">V části **Upravit fond aplikací** na panelu **Akce** vyberte **Upřesnit nastavení** .</span><span class="sxs-lookup"><span data-stu-id="a560a-231">Select **Advanced Settings** under **Edit Application Pool** in the **Actions** panel.</span></span>
1. <span data-ttu-id="a560a-232">Nastavte **možnost povolit 32 – bitové aplikace**:</span><span class="sxs-lookup"><span data-stu-id="a560a-232">Set **Enable 32-Bit Applications**:</span></span>
   * <span data-ttu-id="a560a-233">Pokud nasazujete 32 (x86) aplikaci, nastavte hodnotu na `True` .</span><span class="sxs-lookup"><span data-stu-id="a560a-233">If deploying a 32-bit (x86) app, set the value to `True`.</span></span>
   * <span data-ttu-id="a560a-234">Pokud nasazujete 64 aplikaci (x64), nastavte hodnotu na `False` .</span><span class="sxs-lookup"><span data-stu-id="a560a-234">If deploying a 64-bit (x64) app, set the value to `False`.</span></span>

<span data-ttu-id="a560a-235">Ověřte, že mezi `<Platform>` vlastností MSBuild v souboru projektu a publikovaným bitová verzeem aplikace nedochází ke konfliktu.</span><span class="sxs-lookup"><span data-stu-id="a560a-235">Confirm that there isn't a conflict between a `<Platform>` MSBuild property in the project file and the published bitness of the app.</span></span>

### <a name="connection-reset"></a><span data-ttu-id="a560a-236">Resetování připojení</span><span class="sxs-lookup"><span data-stu-id="a560a-236">Connection reset</span></span>

<span data-ttu-id="a560a-237">Pokud dojde k chybě po odeslání hlaviček, je příliš pozdě pro server, který odešle **500 interní chybu serveru** , když dojde k chybě.</span><span class="sxs-lookup"><span data-stu-id="a560a-237">If an error occurs after the headers are sent, it's too late for the server to send a **500 Internal Server Error** when an error occurs.</span></span> <span data-ttu-id="a560a-238">K tomu často dochází, když dojde k chybě během serializace složitých objektů pro odpověď.</span><span class="sxs-lookup"><span data-stu-id="a560a-238">This often happens when an error occurs during the serialization of complex objects for a response.</span></span> <span data-ttu-id="a560a-239">Tento typ chyby se zobrazí jako chyba *resetování připojení* na klientovi.</span><span class="sxs-lookup"><span data-stu-id="a560a-239">This type of error appears as a *connection reset* error on the client.</span></span> <span data-ttu-id="a560a-240">[Protokolování aplikace](xref:fundamentals/logging/index) může pomoct řešit tyto typy chyb.</span><span class="sxs-lookup"><span data-stu-id="a560a-240">[Application logging](xref:fundamentals/logging/index) can help troubleshoot these types of errors.</span></span>

### <a name="default-startup-limits"></a><span data-ttu-id="a560a-241">Výchozí omezení spouštění</span><span class="sxs-lookup"><span data-stu-id="a560a-241">Default startup limits</span></span>

<span data-ttu-id="a560a-242">[ASP.NET Core modul](xref:host-and-deploy/aspnet-core-module) je nakonfigurovaný s výchozí *startupTimeLimitou* 120 sekund.</span><span class="sxs-lookup"><span data-stu-id="a560a-242">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) is configured with a default *startupTimeLimit* of 120 seconds.</span></span> <span data-ttu-id="a560a-243">Pokud necháte výchozí hodnotu, aplikace může trvat až dvě minuty, než se modul zaznamená selhání procesu.</span><span class="sxs-lookup"><span data-stu-id="a560a-243">When left at the default value, an app may take up to two minutes to start before the module logs a process failure.</span></span> <span data-ttu-id="a560a-244">Informace o konfiguraci modulu naleznete v tématu [atributy elementu aspNetCore](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span><span class="sxs-lookup"><span data-stu-id="a560a-244">For information on configuring the module, see [Attributes of the aspNetCore element](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span></span>

## <a name="troubleshoot-on-azure-app-service"></a><span data-ttu-id="a560a-245">Řešení potíží na Azure App Service</span><span class="sxs-lookup"><span data-stu-id="a560a-245">Troubleshoot on Azure App Service</span></span>

[!INCLUDE [Azure App Service Preview Notice](~/includes/azure-apps-preview-notice.md)]

### <a name="application-event-log-azure-app-service"></a><span data-ttu-id="a560a-246">Protokol událostí aplikace (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="a560a-246">Application Event Log (Azure App Service)</span></span>

<span data-ttu-id="a560a-247">Chcete-li získat přístup k protokolu událostí aplikace, použijte okno **Diagnostika a řešení problémů** v Azure Portal:</span><span class="sxs-lookup"><span data-stu-id="a560a-247">To access the Application Event Log, use the **Diagnose and solve problems** blade in the Azure portal:</span></span>

1. <span data-ttu-id="a560a-248">V Azure Portal otevřete aplikaci v **App Services**.</span><span class="sxs-lookup"><span data-stu-id="a560a-248">In the Azure portal, open the app in **App Services**.</span></span>
1. <span data-ttu-id="a560a-249">Vyberte **Diagnostikovat a řešit problémy**.</span><span class="sxs-lookup"><span data-stu-id="a560a-249">Select **Diagnose and solve problems**.</span></span>
1. <span data-ttu-id="a560a-250">Vyberte **diagnostické nástroje** záhlaví.</span><span class="sxs-lookup"><span data-stu-id="a560a-250">Select the **Diagnostic Tools** heading.</span></span>
1. <span data-ttu-id="a560a-251">V nabídce **nástroje podpory**vyberte tlačítko **události aplikace** .</span><span class="sxs-lookup"><span data-stu-id="a560a-251">Under **Support Tools**, select the **Application Events** button.</span></span>
1. <span data-ttu-id="a560a-252">Projděte si nejnovější chybu, kterou poskytla položka *IIS AspNetCoreModule* nebo *IIS AspNetCoreModule v2* ve **zdrojovém** sloupci.</span><span class="sxs-lookup"><span data-stu-id="a560a-252">Examine the latest error provided by the *IIS AspNetCoreModule* or *IIS AspNetCoreModule V2* entry in the **Source** column.</span></span>

<span data-ttu-id="a560a-253">Alternativou k použití okna **diagnostikovat a řešit problémy** je kontrola souboru protokolu událostí aplikace přímo pomocí [Kudu](https://github.com/projectkudu/kudu/wiki):</span><span class="sxs-lookup"><span data-stu-id="a560a-253">An alternative to using the **Diagnose and solve problems** blade is to examine the Application Event Log file directly using [Kudu](https://github.com/projectkudu/kudu/wiki):</span></span>

1. <span data-ttu-id="a560a-254">Otevřete **Rozšířené nástroje** v oblasti **vývojové nástroje** .</span><span class="sxs-lookup"><span data-stu-id="a560a-254">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="a560a-255">Vyberte tlačítko \*\*Přejít &rarr; \*\* .</span><span class="sxs-lookup"><span data-stu-id="a560a-255">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="a560a-256">Konzola Kudu se otevře v novém okně nebo záložce prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="a560a-256">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="a560a-257">Pomocí navigačního panelu v horní části stránky otevřete **konzolu ladění** a vyberte **cmd**.</span><span class="sxs-lookup"><span data-stu-id="a560a-257">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="a560a-258">Otevřete složku **soubory protokolů** .</span><span class="sxs-lookup"><span data-stu-id="a560a-258">Open the **LogFiles** folder.</span></span>
1. <span data-ttu-id="a560a-259">Vyberte ikonu tužky vedle *eventlog.xml* souboru.</span><span class="sxs-lookup"><span data-stu-id="a560a-259">Select the pencil icon next to the *eventlog.xml* file.</span></span>
1. <span data-ttu-id="a560a-260">Projděte si protokol.</span><span class="sxs-lookup"><span data-stu-id="a560a-260">Examine the log.</span></span> <span data-ttu-id="a560a-261">Posuňte se do dolní části protokolu, abyste viděli nejaktuálnější události.</span><span class="sxs-lookup"><span data-stu-id="a560a-261">Scroll to the bottom of the log to see the most recent events.</span></span>

### <a name="run-the-app-in-the-kudu-console"></a><span data-ttu-id="a560a-262">Spuštění aplikace v konzole Kudu</span><span class="sxs-lookup"><span data-stu-id="a560a-262">Run the app in the Kudu console</span></span>

<span data-ttu-id="a560a-263">Mnoho chyb při spuštění nevytváří užitečné informace v protokolu událostí aplikace.</span><span class="sxs-lookup"><span data-stu-id="a560a-263">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="a560a-264">Tuto chybu můžete zjistit spuštěním aplikace v konzole vzdáleného spuštění [Kudu](https://github.com/projectkudu/kudu/wiki) :</span><span class="sxs-lookup"><span data-stu-id="a560a-264">You can run the app in the [Kudu](https://github.com/projectkudu/kudu/wiki) Remote Execution Console to discover the error:</span></span>

1. <span data-ttu-id="a560a-265">Otevřete **Rozšířené nástroje** v oblasti **vývojové nástroje** .</span><span class="sxs-lookup"><span data-stu-id="a560a-265">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="a560a-266">Vyberte tlačítko \*\*Přejít &rarr; \*\* .</span><span class="sxs-lookup"><span data-stu-id="a560a-266">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="a560a-267">Konzola Kudu se otevře v novém okně nebo záložce prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="a560a-267">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="a560a-268">Pomocí navigačního panelu v horní části stránky otevřete **konzolu ladění** a vyberte **cmd**.</span><span class="sxs-lookup"><span data-stu-id="a560a-268">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>

#### <a name="test-a-32-bit-x86-app"></a><span data-ttu-id="a560a-269">Testování 32 (x86) aplikace</span><span class="sxs-lookup"><span data-stu-id="a560a-269">Test a 32-bit (x86) app</span></span>

<span data-ttu-id="a560a-270">**Aktuální verze**</span><span class="sxs-lookup"><span data-stu-id="a560a-270">**Current release**</span></span>

1. `cd d:\home\site\wwwroot`
1. <span data-ttu-id="a560a-271">Spusťte aplikaci: </span><span class="sxs-lookup"><span data-stu-id="a560a-271">Run the app:</span></span>
   * <span data-ttu-id="a560a-272">Pokud aplikace je [nasazení závislé na rozhraní](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span><span class="sxs-lookup"><span data-stu-id="a560a-272">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

     ```dotnetcli
     dotnet .\{ASSEMBLY NAME}.dll
     ```

   * <span data-ttu-id="a560a-273">Pokud je aplikace samostatná, [nasazení](/dotnet/core/deploying/#self-contained-deployments-scd):</span><span class="sxs-lookup"><span data-stu-id="a560a-273">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

     ```console
     {ASSEMBLY NAME}.exe
     ```

<span data-ttu-id="a560a-274">Výstup konzoly z aplikace, v němž se zobrazují všechny chyby, je předán do konzoly Kudu.</span><span class="sxs-lookup"><span data-stu-id="a560a-274">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="a560a-275">**Nasazení závislé na architektuře spuštěné ve verzi Preview**</span><span class="sxs-lookup"><span data-stu-id="a560a-275">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="a560a-276">*Vyžaduje instalaci rozšíření webu ASP.NET Core {VERSION} (x86) Runtime.*</span><span class="sxs-lookup"><span data-stu-id="a560a-276">*Requires installing the ASP.NET Core {VERSION} (x86) Runtime site extension.*</span></span>

1. <span data-ttu-id="a560a-277">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32` ( `{X.Y}` je verze modulu runtime)</span><span class="sxs-lookup"><span data-stu-id="a560a-277">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="a560a-278">Spusťte aplikaci: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="a560a-278">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="a560a-279">Výstup konzoly z aplikace, v němž se zobrazují všechny chyby, je předán do konzoly Kudu.</span><span class="sxs-lookup"><span data-stu-id="a560a-279">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

#### <a name="test-a-64-bit-x64-app"></a><span data-ttu-id="a560a-280">Testování 64 aplikace (x64)</span><span class="sxs-lookup"><span data-stu-id="a560a-280">Test a 64-bit (x64) app</span></span>

<span data-ttu-id="a560a-281">**Aktuální verze**</span><span class="sxs-lookup"><span data-stu-id="a560a-281">**Current release**</span></span>

* <span data-ttu-id="a560a-282">Pokud je aplikace [nasazením závislého na rozhraní](/dotnet/core/deploying/#framework-dependent-deployments-fdd)64 (x64):</span><span class="sxs-lookup"><span data-stu-id="a560a-282">If the app is a 64-bit (x64) [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>
  1. `cd D:\Program Files\dotnet`
  1. <span data-ttu-id="a560a-283">Spusťte aplikaci: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="a560a-283">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>
* <span data-ttu-id="a560a-284">Pokud je aplikace samostatná, [nasazení](/dotnet/core/deploying/#self-contained-deployments-scd):</span><span class="sxs-lookup"><span data-stu-id="a560a-284">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>
  1. `cd D:\home\site\wwwroot`
  1. <span data-ttu-id="a560a-285">Spusťte aplikaci: `{ASSEMBLY NAME}.exe`</span><span class="sxs-lookup"><span data-stu-id="a560a-285">Run the app: `{ASSEMBLY NAME}.exe`</span></span>

<span data-ttu-id="a560a-286">Výstup konzoly z aplikace, v němž se zobrazují všechny chyby, je předán do konzoly Kudu.</span><span class="sxs-lookup"><span data-stu-id="a560a-286">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="a560a-287">**Nasazení závislé na architektuře spuštěné ve verzi Preview**</span><span class="sxs-lookup"><span data-stu-id="a560a-287">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="a560a-288">*Vyžaduje instalaci rozšíření webu ASP.NET Core {VERSION} (x64) Runtime.*</span><span class="sxs-lookup"><span data-stu-id="a560a-288">*Requires installing the ASP.NET Core {VERSION} (x64) Runtime site extension.*</span></span>

1. <span data-ttu-id="a560a-289">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64` ( `{X.Y}` je verze modulu runtime)</span><span class="sxs-lookup"><span data-stu-id="a560a-289">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="a560a-290">Spusťte aplikaci: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="a560a-290">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="a560a-291">Výstup konzoly z aplikace, v němž se zobrazují všechny chyby, je předán do konzoly Kudu.</span><span class="sxs-lookup"><span data-stu-id="a560a-291">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

### <a name="aspnet-core-module-stdout-log-azure-app-service"></a><span data-ttu-id="a560a-292">Protokol stdout v modulu ASP.NET Core (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="a560a-292">ASP.NET Core Module stdout log (Azure App Service)</span></span>

> [!WARNING]
> <span data-ttu-id="a560a-293">Nepovedlo se zakázat protokol stdout, což může způsobit selhání aplikace nebo serveru.</span><span class="sxs-lookup"><span data-stu-id="a560a-293">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="a560a-294">Velikost souboru protokolu ani počet vytvořených souborů protokolu nijak neomezuje.</span><span class="sxs-lookup"><span data-stu-id="a560a-294">There's no limit on log file size or the number of log files created.</span></span> <span data-ttu-id="a560a-295">K řešení problémů se spouštěním aplikací použijte pouze protokolování STDOUT.</span><span class="sxs-lookup"><span data-stu-id="a560a-295">Only use stdout logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="a560a-296">Pro obecné protokolování v aplikaci ASP.NET Core po spuštění použijte knihovnu protokolování, která omezuje velikost souboru protokolu a otočí protokoly.</span><span class="sxs-lookup"><span data-stu-id="a560a-296">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="a560a-297">Další informace najdete v tématu [Zprostředkovatelé protokolování třetích stran](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="a560a-297">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="a560a-298">Protokol stdout modulu ASP.NET Coree často zaznamenává užitečné chybové zprávy, které se nenašly v protokolu událostí aplikace.</span><span class="sxs-lookup"><span data-stu-id="a560a-298">The ASP.NET Core Module stdout log often records useful error messages not found in the Application Event Log.</span></span> <span data-ttu-id="a560a-299">Postup povolení a zobrazení protokolů stdout:</span><span class="sxs-lookup"><span data-stu-id="a560a-299">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="a560a-300">Na webu Azure Portal přejděte do webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="a560a-300">In the Azure Portal, navigate to the web app.</span></span>
1. <span data-ttu-id="a560a-301">V okně **App Service** do vyhledávacího pole zadejte **Kudu** .</span><span class="sxs-lookup"><span data-stu-id="a560a-301">In the **App Service** blade, enter **kudu** in the search box.</span></span>
1. <span data-ttu-id="a560a-302">Vyberte **Rozšířené nástroje** > **Přejít**.</span><span class="sxs-lookup"><span data-stu-id="a560a-302">Select **Advanced Tools** > **Go**.</span></span>
1. <span data-ttu-id="a560a-303">Vyberte  **ladit konzolu > cmd**.</span><span class="sxs-lookup"><span data-stu-id="a560a-303">Select  **Debug console > CMD**.</span></span>
1. <span data-ttu-id="a560a-304">Přejít na *Web/wwwroot*</span><span class="sxs-lookup"><span data-stu-id="a560a-304">Navigate to *site/wwwroot*</span></span>
1. <span data-ttu-id="a560a-305">Vyberte ikonu tužky a upravte soubor *web.config* .</span><span class="sxs-lookup"><span data-stu-id="a560a-305">Select the pencil icon to edit the *web.config* file.</span></span>
1. <span data-ttu-id="a560a-306">V `<aspNetCore />` elementu nastavte `stdoutLogEnabled="true"` a vyberte **Uložit**.</span><span class="sxs-lookup"><span data-stu-id="a560a-306">In the `<aspNetCore />` element, set `stdoutLogEnabled="true"` and select **Save**.</span></span>

<span data-ttu-id="a560a-307">Zakažte protokolování stdout, když se dokončí odstraňování potíží nastavením `stdoutLogEnabled="false"` .</span><span class="sxs-lookup"><span data-stu-id="a560a-307">Disable stdout logging when troubleshooting is complete by setting `stdoutLogEnabled="false"`.</span></span>

<span data-ttu-id="a560a-308">Další informace naleznete v tématu <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span><span class="sxs-lookup"><span data-stu-id="a560a-308">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

### <a name="aspnet-core-module-debug-log-azure-app-service"></a><span data-ttu-id="a560a-309">Protokol ladění modulu ASP.NET Core (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="a560a-309">ASP.NET Core Module debug log (Azure App Service)</span></span>

<span data-ttu-id="a560a-310">Protokol ladění ASP.NET Core modulu poskytuje další, hlubší protokolování z modulu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a560a-310">The ASP.NET Core Module debug log provides additional, deeper logging from the ASP.NET Core Module.</span></span> <span data-ttu-id="a560a-311">Postup povolení a zobrazení protokolů stdout:</span><span class="sxs-lookup"><span data-stu-id="a560a-311">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="a560a-312">Pro povolení rozšířeného diagnostického protokolu proveďte jednu z následujících akcí:</span><span class="sxs-lookup"><span data-stu-id="a560a-312">To enable the enhanced diagnostic log, perform either of the following:</span></span>
   * <span data-ttu-id="a560a-313">Postupujte podle pokynů v [rozšířených diagnostických protokolech](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) a nakonfigurujte aplikaci pro rozšířené diagnostické protokolování.</span><span class="sxs-lookup"><span data-stu-id="a560a-313">Follow the instructions in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) to configure the app for an enhanced diagnostic logging.</span></span> <span data-ttu-id="a560a-314">Znovu nasaďte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="a560a-314">Redeploy the app.</span></span>
   * <span data-ttu-id="a560a-315">Přidejte `<handlerSettings>` zobrazené v [rozšířených diagnostických protokolech](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) do souboru *web.config* živé aplikace pomocí konzoly Kudu:</span><span class="sxs-lookup"><span data-stu-id="a560a-315">Add the `<handlerSettings>` shown in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) to the live app's *web.config* file using the Kudu console:</span></span>
     1. <span data-ttu-id="a560a-316">Otevřete **Rozšířené nástroje** v oblasti **vývojové nástroje** .</span><span class="sxs-lookup"><span data-stu-id="a560a-316">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="a560a-317">Vyberte tlačítko \*\*Přejít &rarr; \*\* .</span><span class="sxs-lookup"><span data-stu-id="a560a-317">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="a560a-318">Konzola Kudu se otevře v novém okně nebo záložce prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="a560a-318">The Kudu console opens in a new browser tab or window.</span></span>
     1. <span data-ttu-id="a560a-319">Pomocí navigačního panelu v horní části stránky otevřete **konzolu ladění** a vyberte **cmd**.</span><span class="sxs-lookup"><span data-stu-id="a560a-319">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
     1. <span data-ttu-id="a560a-320">Otevřete složky na cestě **lokality**  >  **wwwroot**.</span><span class="sxs-lookup"><span data-stu-id="a560a-320">Open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="a560a-321">Upravte soubor *web.config* tak, že vyberete tlačítko tužky.</span><span class="sxs-lookup"><span data-stu-id="a560a-321">Edit the *web.config* file by selecting the pencil button.</span></span> <span data-ttu-id="a560a-322">Přidejte `<handlerSettings>` oddíl, jak je znázorněno v [rozšířených diagnostických protokolech](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs).</span><span class="sxs-lookup"><span data-stu-id="a560a-322">Add the `<handlerSettings>` section as shown in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs).</span></span> <span data-ttu-id="a560a-323">Vyberte tlačítko **Uložit**.</span><span class="sxs-lookup"><span data-stu-id="a560a-323">Select the **Save** button.</span></span>
1. <span data-ttu-id="a560a-324">Otevřete **Rozšířené nástroje** v oblasti **vývojové nástroje** .</span><span class="sxs-lookup"><span data-stu-id="a560a-324">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="a560a-325">Vyberte tlačítko \*\*Přejít &rarr; \*\* .</span><span class="sxs-lookup"><span data-stu-id="a560a-325">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="a560a-326">Konzola Kudu se otevře v novém okně nebo záložce prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="a560a-326">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="a560a-327">Pomocí navigačního panelu v horní části stránky otevřete **konzolu ladění** a vyberte **cmd**.</span><span class="sxs-lookup"><span data-stu-id="a560a-327">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="a560a-328">Otevřete složky na cestě **lokality**  >  **wwwroot**.</span><span class="sxs-lookup"><span data-stu-id="a560a-328">Open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="a560a-329">Pokud jste nezadali cestu k souboru *aspnetcore-Debug. log* , soubor se zobrazí v seznamu.</span><span class="sxs-lookup"><span data-stu-id="a560a-329">If you didn't supply a path for the *aspnetcore-debug.log* file, the file appears in the list.</span></span> <span data-ttu-id="a560a-330">Pokud jste zadali cestu, přejděte do umístění souboru protokolu.</span><span class="sxs-lookup"><span data-stu-id="a560a-330">If you supplied a path, navigate to the location of the log file.</span></span>
1. <span data-ttu-id="a560a-331">Otevřete soubor protokolu s tlačítkem tužky vedle názvu souboru.</span><span class="sxs-lookup"><span data-stu-id="a560a-331">Open the log file with the pencil button next to the file name.</span></span>

<span data-ttu-id="a560a-332">Zakázat protokolování ladění, pokud je dokončeno odstraňování potíží:</span><span class="sxs-lookup"><span data-stu-id="a560a-332">Disable debug logging when troubleshooting is complete:</span></span>

<span data-ttu-id="a560a-333">Chcete-li zakázat rozšířený protokol ladění, proveďte jednu z následujících akcí:</span><span class="sxs-lookup"><span data-stu-id="a560a-333">To disable the enhanced debug log, perform either of the following:</span></span>

* <span data-ttu-id="a560a-334">Odeberte `<handlerSettings>` soubor z *web.config* místně a aplikaci znovu nasaďte.</span><span class="sxs-lookup"><span data-stu-id="a560a-334">Remove the `<handlerSettings>` from the *web.config* file locally and redeploy the app.</span></span>
* <span data-ttu-id="a560a-335">Pomocí konzoly Kudu upravte soubor *web.config* a odeberte `<handlerSettings>` oddíl.</span><span class="sxs-lookup"><span data-stu-id="a560a-335">Use the Kudu console to edit the *web.config* file and remove the `<handlerSettings>` section.</span></span> <span data-ttu-id="a560a-336">Soubor uložte.</span><span class="sxs-lookup"><span data-stu-id="a560a-336">Save the file.</span></span>

<span data-ttu-id="a560a-337">Další informace naleznete v tématu <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span><span class="sxs-lookup"><span data-stu-id="a560a-337">For more information, see <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span></span>

> [!WARNING]
> <span data-ttu-id="a560a-338">Nepovedlo se zakázat protokol ladění, může způsobit selhání aplikace nebo serveru.</span><span class="sxs-lookup"><span data-stu-id="a560a-338">Failure to disable the debug log can lead to app or server failure.</span></span> <span data-ttu-id="a560a-339">Velikost souboru protokolu není nijak omezena.</span><span class="sxs-lookup"><span data-stu-id="a560a-339">There's no limit on log file size.</span></span> <span data-ttu-id="a560a-340">K řešení problémů se spouštěním aplikace používejte pouze protokolování ladění.</span><span class="sxs-lookup"><span data-stu-id="a560a-340">Only use debug logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="a560a-341">Pro obecné protokolování v aplikaci ASP.NET Core po spuštění použijte knihovnu protokolování, která omezuje velikost souboru protokolu a otočí protokoly.</span><span class="sxs-lookup"><span data-stu-id="a560a-341">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="a560a-342">Další informace najdete v tématu [Zprostředkovatelé protokolování třetích stran](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="a560a-342">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="slow-or-hanging-app-azure-app-service"></a><span data-ttu-id="a560a-343">Pomalá nebo zavěšená aplikace (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="a560a-343">Slow or hanging app (Azure App Service)</span></span>

<span data-ttu-id="a560a-344">Pokud aplikace reaguje pomalu nebo přestane reagovat na žádost, přečtěte si následující články:</span><span class="sxs-lookup"><span data-stu-id="a560a-344">When an app responds slowly or hangs on a request, see the following articles:</span></span>

* [<span data-ttu-id="a560a-345">Řešení potíží s výkonem pomalých webových aplikací ve službě Azure App Service</span><span class="sxs-lookup"><span data-stu-id="a560a-345">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="a560a-346">K zachycení výpisu problémů s přerušovanou výjimkou nebo problémy s výkonem ve webové aplikaci Azure použijte rozšíření pro diagnostiku zhroucení serveru.</span><span class="sxs-lookup"><span data-stu-id="a560a-346">Use Crash Diagnoser Site Extension to Capture Dump for Intermittent Exception issues or performance issues on Azure Web App</span></span>](https://blogs.msdn.microsoft.com/asiatech/2015/12/28/use-crash-diagnoser-site-extension-to-capture-dump-for-intermittent-exception-issues-or-performance-issues-on-azure-web-app/)

### <a name="monitoring-blades"></a><span data-ttu-id="a560a-347">Okna monitorování</span><span class="sxs-lookup"><span data-stu-id="a560a-347">Monitoring blades</span></span>

<span data-ttu-id="a560a-348">Okna monitorování poskytují alternativní možnosti řešení potíží pro metody popsané dříve v tématu.</span><span class="sxs-lookup"><span data-stu-id="a560a-348">Monitoring blades provide an alternative troubleshooting experience to the methods described earlier in the topic.</span></span> <span data-ttu-id="a560a-349">Pomocí těchto oken můžete diagnostikovat chyby 500-Series.</span><span class="sxs-lookup"><span data-stu-id="a560a-349">These blades can be used to diagnose 500-series errors.</span></span>

<span data-ttu-id="a560a-350">Potvrďte, že jsou nainstalovaná rozšíření ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a560a-350">Confirm that the ASP.NET Core Extensions are installed.</span></span> <span data-ttu-id="a560a-351">Pokud nejsou rozšíření nainstalována, nainstalujte je ručně:</span><span class="sxs-lookup"><span data-stu-id="a560a-351">If the extensions aren't installed, install them manually:</span></span>

1. <span data-ttu-id="a560a-352">V části okno **vývojové nástroje** vyberte okno **rozšíření** .</span><span class="sxs-lookup"><span data-stu-id="a560a-352">In the **DEVELOPMENT TOOLS** blade section, select the **Extensions** blade.</span></span>
1. <span data-ttu-id="a560a-353">V seznamu by se měla zobrazit **rozšíření ASP.NET Core** .</span><span class="sxs-lookup"><span data-stu-id="a560a-353">The **ASP.NET Core Extensions** should appear in the list.</span></span>
1. <span data-ttu-id="a560a-354">Pokud nejsou rozšíření nainstalovaná, vyberte tlačítko **Přidat** .</span><span class="sxs-lookup"><span data-stu-id="a560a-354">If the extensions aren't installed, select the **Add** button.</span></span>
1. <span data-ttu-id="a560a-355">Ze seznamu vyberte **rozšíření ASP.NET Core** .</span><span class="sxs-lookup"><span data-stu-id="a560a-355">Choose the **ASP.NET Core Extensions** from the list.</span></span>
1. <span data-ttu-id="a560a-356">Kliknutím na **OK** přijměte právní podmínky.</span><span class="sxs-lookup"><span data-stu-id="a560a-356">Select **OK** to accept the legal terms.</span></span>
1. <span data-ttu-id="a560a-357">V okně **Přidat rozšíření** vyberte **OK** .</span><span class="sxs-lookup"><span data-stu-id="a560a-357">Select **OK** on the **Add extension** blade.</span></span>
1. <span data-ttu-id="a560a-358">Informační automaticky otevíraná zpráva indikuje, že se rozšíření úspěšně nainstalovala.</span><span class="sxs-lookup"><span data-stu-id="a560a-358">An informational pop-up message indicates when the extensions are successfully installed.</span></span>

<span data-ttu-id="a560a-359">Pokud není povoleno protokolování stdout, postupujte následovně:</span><span class="sxs-lookup"><span data-stu-id="a560a-359">If stdout logging isn't enabled, follow these steps:</span></span>

1. <span data-ttu-id="a560a-360">V Azure Portal v oblasti **vývojové nástroje** vyberte okno **Pokročilé nástroje** .</span><span class="sxs-lookup"><span data-stu-id="a560a-360">In the Azure portal, select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="a560a-361">Vyberte tlačítko \*\*Přejít &rarr; \*\* .</span><span class="sxs-lookup"><span data-stu-id="a560a-361">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="a560a-362">Konzola Kudu se otevře v novém okně nebo záložce prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="a560a-362">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="a560a-363">Pomocí navigačního panelu v horní části stránky otevřete **konzolu ladění** a vyberte **cmd**.</span><span class="sxs-lookup"><span data-stu-id="a560a-363">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="a560a-364">Otevřete složky **pro cestu** > **wwwroot** a posuňte se dolů, aby se soubor *web.config* v dolní části seznamu.</span><span class="sxs-lookup"><span data-stu-id="a560a-364">Open the folders to the path **site** > **wwwroot** and scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="a560a-365">Klikněte na ikonu tužky vedle *web.config* souboru.</span><span class="sxs-lookup"><span data-stu-id="a560a-365">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="a560a-366">Nastavte **stdoutLogEnabled** na `true` a změňte cestu **stdoutLogFile** na: `\\?\%home%\LogFiles\stdout` .</span><span class="sxs-lookup"><span data-stu-id="a560a-366">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="a560a-367">Výběrem **Uložit** Uložte aktualizovaný soubor *web.config* .</span><span class="sxs-lookup"><span data-stu-id="a560a-367">Select **Save** to save the updated *web.config* file.</span></span>

<span data-ttu-id="a560a-368">Pokračovat k aktivaci protokolování diagnostiky:</span><span class="sxs-lookup"><span data-stu-id="a560a-368">Proceed to activate diagnostic logging:</span></span>

1. <span data-ttu-id="a560a-369">V Azure Portal vyberte okno **diagnostické protokoly** .</span><span class="sxs-lookup"><span data-stu-id="a560a-369">In the Azure portal, select the **Diagnostics logs** blade.</span></span>
1. <span data-ttu-id="a560a-370">Vyberte přepínač **zapnuto** pro **protokolování aplikace (systém souborů)** a **podrobné chybové zprávy**.</span><span class="sxs-lookup"><span data-stu-id="a560a-370">Select the **On** switch for **Application Logging (Filesystem)** and **Detailed error messages**.</span></span> <span data-ttu-id="a560a-371">V horní části okna vyberte tlačítko **Uložit** .</span><span class="sxs-lookup"><span data-stu-id="a560a-371">Select the **Save** button at the top of the blade.</span></span>
1. <span data-ttu-id="a560a-372">Chcete-li zahrnout trasování chybných požadavků, označované také jako protokolování událostí neúspěšných požadavků na FREB, vyberte přepínač **zapnuto** pro **trasování chybných požadavků**.</span><span class="sxs-lookup"><span data-stu-id="a560a-372">To include failed request tracing, also known as Failed Request Event Buffering (FREB) logging, select the **On** switch for **Failed request tracing**.</span></span>
1. <span data-ttu-id="a560a-373">Vyberte okno **log Stream** , které je uvedeno hned pod oknem **diagnostické protokoly** na portálu.</span><span class="sxs-lookup"><span data-stu-id="a560a-373">Select the **Log stream** blade, which is listed immediately under the **Diagnostics logs** blade in the portal.</span></span>
1. <span data-ttu-id="a560a-374">Vytvořte žádost do aplikace.</span><span class="sxs-lookup"><span data-stu-id="a560a-374">Make a request to the app.</span></span>
1. <span data-ttu-id="a560a-375">V datech streamu protokolu se označuje příčina chyby.</span><span class="sxs-lookup"><span data-stu-id="a560a-375">Within the log stream data, the cause of the error is indicated.</span></span>

<span data-ttu-id="a560a-376">Nezapomeňte zakázat protokolování stdout po dokončení řešení potíží.</span><span class="sxs-lookup"><span data-stu-id="a560a-376">Be sure to disable stdout logging when troubleshooting is complete.</span></span>

<span data-ttu-id="a560a-377">Zobrazení protokolů pro trasování chybných požadavků (protokoly FREB):</span><span class="sxs-lookup"><span data-stu-id="a560a-377">To view the failed request tracing logs (FREB logs):</span></span>

1. <span data-ttu-id="a560a-378">Přejděte do okna **diagnostikovat a řešit problémy** v Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="a560a-378">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="a560a-379">V oblasti **nástroje podpory** v postranním panelu vyberte **protokoly pro trasování chybných požadavků** .</span><span class="sxs-lookup"><span data-stu-id="a560a-379">Select **Failed Request Tracing Logs** from the **SUPPORT TOOLS** area of the sidebar.</span></span>

<span data-ttu-id="a560a-380">V [části Povolení protokolování diagnostiky pro webové aplikace v Azure App Service tématu](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) a [Nejčastější dotazy týkající se výkonu aplikace pro Web Apps v Azure: návody zapnout trasování chybných požadavků?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing) Další informace.</span><span class="sxs-lookup"><span data-stu-id="a560a-380">See [Failed request traces section of the Enable diagnostics logging for web apps in Azure App Service topic](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) and the [Application performance FAQs for Web Apps in Azure: How do I turn on failed request tracing?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing) for more information.</span></span>

<span data-ttu-id="a560a-381">Další informace najdete v tématu [Povolení protokolování diagnostiky pro webové aplikace v Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log).</span><span class="sxs-lookup"><span data-stu-id="a560a-381">For more information, see [Enable diagnostics logging for web apps in Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log).</span></span>

> [!WARNING]
> <span data-ttu-id="a560a-382">Nepovedlo se zakázat protokol stdout, což může způsobit selhání aplikace nebo serveru.</span><span class="sxs-lookup"><span data-stu-id="a560a-382">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="a560a-383">Velikost souboru protokolu ani počet vytvořených souborů protokolu nijak neomezuje.</span><span class="sxs-lookup"><span data-stu-id="a560a-383">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="a560a-384">Pro rutinu protokolování v aplikaci ASP.NET Core použijte knihovnu protokolování, která omezuje velikost souboru protokolu a otočí protokoly.</span><span class="sxs-lookup"><span data-stu-id="a560a-384">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="a560a-385">Další informace najdete v tématu [Zprostředkovatelé protokolování třetích stran](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="a560a-385">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

## <a name="troubleshoot-on-iis"></a><span data-ttu-id="a560a-386">Odstraňování potíží ve službě IIS</span><span class="sxs-lookup"><span data-stu-id="a560a-386">Troubleshoot on IIS</span></span>

### <a name="application-event-log-iis"></a><span data-ttu-id="a560a-387">Protokol událostí aplikace (IIS)</span><span class="sxs-lookup"><span data-stu-id="a560a-387">Application Event Log (IIS)</span></span>

<span data-ttu-id="a560a-388">Přístup k protokolu událostí aplikace:</span><span class="sxs-lookup"><span data-stu-id="a560a-388">Access the Application Event Log:</span></span>

1. <span data-ttu-id="a560a-389">Otevřete nabídku Start, vyhledejte *Prohlížeč událostí*a vyberte aplikaci **Prohlížeč událostí** .</span><span class="sxs-lookup"><span data-stu-id="a560a-389">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="a560a-390">V **Prohlížeč událostí**otevřete uzel **protokoly systému Windows** .</span><span class="sxs-lookup"><span data-stu-id="a560a-390">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="a560a-391">Výběrem **aplikace** otevřete protokol událostí aplikace.</span><span class="sxs-lookup"><span data-stu-id="a560a-391">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="a560a-392">Vyhledá chyby spojené s chybnou aplikací.</span><span class="sxs-lookup"><span data-stu-id="a560a-392">Search for errors associated with the failing app.</span></span> <span data-ttu-id="a560a-393">Chyby mají ve *zdrojovém* sloupci hodnotu *modulu IIS AspNetCore* nebo *modul IIS Express AspNetCore* .</span><span class="sxs-lookup"><span data-stu-id="a560a-393">Errors have a value of *IIS AspNetCore Module* or *IIS Express AspNetCore Module* in the *Source* column.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="a560a-394">Spuštění aplikace na příkazovém řádku</span><span class="sxs-lookup"><span data-stu-id="a560a-394">Run the app at a command prompt</span></span>

<span data-ttu-id="a560a-395">Mnoho chyb při spuštění nevytváří užitečné informace v protokolu událostí aplikace.</span><span class="sxs-lookup"><span data-stu-id="a560a-395">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="a560a-396">Příčinu některých chyb můžete najít spuštěním aplikace na příkazovém řádku v hostitelském systému.</span><span class="sxs-lookup"><span data-stu-id="a560a-396">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span>

#### <a name="framework-dependent-deployment"></a><span data-ttu-id="a560a-397">Nasazení závislé na rozhraní</span><span class="sxs-lookup"><span data-stu-id="a560a-397">Framework-dependent deployment</span></span>

<span data-ttu-id="a560a-398">Pokud aplikace je [nasazení závislé na rozhraní](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span><span class="sxs-lookup"><span data-stu-id="a560a-398">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

1. <span data-ttu-id="a560a-399">Na příkazovém řádku přejděte do složky pro nasazení a spusťte aplikaci spuštěním sestavení aplikace pomocí *dotnet.exe*.</span><span class="sxs-lookup"><span data-stu-id="a560a-399">At a command prompt, navigate to the deployment folder and run the app by executing the app's assembly with *dotnet.exe*.</span></span> <span data-ttu-id="a560a-400">V následujícím příkazu nahraďte název sestavení aplikace pro \<assembly_name> : `dotnet .\<assembly_name>.dll` .</span><span class="sxs-lookup"><span data-stu-id="a560a-400">In the following command, substitute the name of the app's assembly for \<assembly_name>: `dotnet .\<assembly_name>.dll`.</span></span>
1. <span data-ttu-id="a560a-401">Výstup konzoly z aplikace, který zobrazuje všechny chyby, je zapsán do okna konzoly.</span><span class="sxs-lookup"><span data-stu-id="a560a-401">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="a560a-402">Pokud dojde k chybám při podání žádosti do aplikace, vytvořte žádost na hostitele a port, kde Kestrel naslouchá.</span><span class="sxs-lookup"><span data-stu-id="a560a-402">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="a560a-403">Pomocí výchozího hostitele a příspěvku vytvořte žádost `http://localhost:5000/` .</span><span class="sxs-lookup"><span data-stu-id="a560a-403">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="a560a-404">Pokud aplikace reaguje normálně na adrese koncového bodu Kestrel, problém je pravděpodobnější v souvislosti s konfigurací hostování a méně pravděpodobným v rámci aplikace.</span><span class="sxs-lookup"><span data-stu-id="a560a-404">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

#### <a name="self-contained-deployment"></a><span data-ttu-id="a560a-405">Samostatně zahrnuté nasazení</span><span class="sxs-lookup"><span data-stu-id="a560a-405">Self-contained deployment</span></span>

<span data-ttu-id="a560a-406">Pokud je aplikace samostatná, [nasazení](/dotnet/core/deploying/#self-contained-deployments-scd):</span><span class="sxs-lookup"><span data-stu-id="a560a-406">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

1. <span data-ttu-id="a560a-407">Na příkazovém řádku přejděte do složky pro nasazení a spusťte spustitelný soubor aplikace.</span><span class="sxs-lookup"><span data-stu-id="a560a-407">At a command prompt, navigate to the deployment folder and run the app's executable.</span></span> <span data-ttu-id="a560a-408">V následujícím příkazu nahraďte název sestavení aplikace pro \<assembly_name> : `<assembly_name>.exe` .</span><span class="sxs-lookup"><span data-stu-id="a560a-408">In the following command, substitute the name of the app's assembly for \<assembly_name>: `<assembly_name>.exe`.</span></span>
1. <span data-ttu-id="a560a-409">Výstup konzoly z aplikace, který zobrazuje všechny chyby, je zapsán do okna konzoly.</span><span class="sxs-lookup"><span data-stu-id="a560a-409">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="a560a-410">Pokud dojde k chybám při podání žádosti do aplikace, vytvořte žádost na hostitele a port, kde Kestrel naslouchá.</span><span class="sxs-lookup"><span data-stu-id="a560a-410">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="a560a-411">Pomocí výchozího hostitele a příspěvku vytvořte žádost `http://localhost:5000/` .</span><span class="sxs-lookup"><span data-stu-id="a560a-411">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="a560a-412">Pokud aplikace reaguje normálně na adrese koncového bodu Kestrel, problém je pravděpodobnější v souvislosti s konfigurací hostování a méně pravděpodobným v rámci aplikace.</span><span class="sxs-lookup"><span data-stu-id="a560a-412">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

### <a name="aspnet-core-module-stdout-log-iis"></a><span data-ttu-id="a560a-413">Protokol stdout v modulu ASP.NET Core (IIS)</span><span class="sxs-lookup"><span data-stu-id="a560a-413">ASP.NET Core Module stdout log (IIS)</span></span>

<span data-ttu-id="a560a-414">Postup povolení a zobrazení protokolů stdout:</span><span class="sxs-lookup"><span data-stu-id="a560a-414">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="a560a-415">Přejděte do složky pro nasazení webu v hostitelském systému.</span><span class="sxs-lookup"><span data-stu-id="a560a-415">Navigate to the site's deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="a560a-416">Pokud složka *logs* není k dispozici, vytvořte složku.</span><span class="sxs-lookup"><span data-stu-id="a560a-416">If the *logs* folder isn't present, create the folder.</span></span> <span data-ttu-id="a560a-417">Pokyny k tomu, jak povolit MSBuild pro vytvoření složky *logs* v nasazení automaticky, najdete v tématu [Struktura adresáře](xref:host-and-deploy/directory-structure) .</span><span class="sxs-lookup"><span data-stu-id="a560a-417">For instructions on how to enable MSBuild to create the *logs* folder in the deployment automatically, see the [Directory structure](xref:host-and-deploy/directory-structure) topic.</span></span>
1. <span data-ttu-id="a560a-418">Upravte soubor *web.config* .</span><span class="sxs-lookup"><span data-stu-id="a560a-418">Edit the *web.config* file.</span></span> <span data-ttu-id="a560a-419">Nastavte **stdoutLogEnabled** na `true` a změňte cestu **stdoutLogFile** tak, aby odkazovala na složku *logs* (například `.\logs\stdout` ).</span><span class="sxs-lookup"><span data-stu-id="a560a-419">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to point to the *logs* folder (for example, `.\logs\stdout`).</span></span> <span data-ttu-id="a560a-420">`stdout` v cestě je předpona názvu souboru protokolu.</span><span class="sxs-lookup"><span data-stu-id="a560a-420">`stdout` in the path is the log file name prefix.</span></span> <span data-ttu-id="a560a-421">Časové razítko, ID procesu a Přípona souboru se automaticky přidají při vytvoření protokolu.</span><span class="sxs-lookup"><span data-stu-id="a560a-421">A timestamp, process id, and file extension are added automatically when the log is created.</span></span> <span data-ttu-id="a560a-422">`stdout`Jako předpona názvu souboru se používá typický soubor protokolu s názvem *stdout_20180205184032_5412. log*.</span><span class="sxs-lookup"><span data-stu-id="a560a-422">Using `stdout` as the file name prefix, a typical log file is named *stdout_20180205184032_5412.log*.</span></span>
1. <span data-ttu-id="a560a-423">Zajistěte, aby identita fondu aplikací měla oprávnění k zápisu do složky *logs* .</span><span class="sxs-lookup"><span data-stu-id="a560a-423">Ensure your application pool's identity has write permissions to the *logs* folder.</span></span>
1. <span data-ttu-id="a560a-424">Uložte aktualizovaný soubor *web.config* .</span><span class="sxs-lookup"><span data-stu-id="a560a-424">Save the updated *web.config* file.</span></span>
1. <span data-ttu-id="a560a-425">Vytvořte žádost do aplikace.</span><span class="sxs-lookup"><span data-stu-id="a560a-425">Make a request to the app.</span></span>
1. <span data-ttu-id="a560a-426">Přejděte do složky *logs* .</span><span class="sxs-lookup"><span data-stu-id="a560a-426">Navigate to the *logs* folder.</span></span> <span data-ttu-id="a560a-427">Vyhledejte a otevřete nejnovější protokol STDOUT.</span><span class="sxs-lookup"><span data-stu-id="a560a-427">Find and open the most recent stdout log.</span></span>
1. <span data-ttu-id="a560a-428">Prostudujte si protokol o chybách.</span><span class="sxs-lookup"><span data-stu-id="a560a-428">Study the log for errors.</span></span>

<span data-ttu-id="a560a-429">Zakázat protokolování stdout při odstraňování potíží:</span><span class="sxs-lookup"><span data-stu-id="a560a-429">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="a560a-430">Upravte soubor *web.config* .</span><span class="sxs-lookup"><span data-stu-id="a560a-430">Edit the *web.config* file.</span></span>
1. <span data-ttu-id="a560a-431">Nastavte **stdoutLogEnabled** na `false` .</span><span class="sxs-lookup"><span data-stu-id="a560a-431">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="a560a-432">Soubor uložte.</span><span class="sxs-lookup"><span data-stu-id="a560a-432">Save the file.</span></span>

<span data-ttu-id="a560a-433">Další informace naleznete v tématu <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span><span class="sxs-lookup"><span data-stu-id="a560a-433">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

> [!WARNING]
> <span data-ttu-id="a560a-434">Nepovedlo se zakázat protokol stdout, což může způsobit selhání aplikace nebo serveru.</span><span class="sxs-lookup"><span data-stu-id="a560a-434">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="a560a-435">Velikost souboru protokolu ani počet vytvořených souborů protokolu nijak neomezuje.</span><span class="sxs-lookup"><span data-stu-id="a560a-435">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="a560a-436">Pro rutinu protokolování v aplikaci ASP.NET Core použijte knihovnu protokolování, která omezuje velikost souboru protokolu a otočí protokoly.</span><span class="sxs-lookup"><span data-stu-id="a560a-436">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="a560a-437">Další informace najdete v tématu [Zprostředkovatelé protokolování třetích stran](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="a560a-437">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="aspnet-core-module-debug-log-iis"></a><span data-ttu-id="a560a-438">Protokol ladění modulu ASP.NET Core (IIS)</span><span class="sxs-lookup"><span data-stu-id="a560a-438">ASP.NET Core Module debug log (IIS)</span></span>

<span data-ttu-id="a560a-439">Do souboru *web.config* aplikace přidejte následující nastavení obslužných rutin, aby se povolil protokol ladění ASP.NET Core modulu:</span><span class="sxs-lookup"><span data-stu-id="a560a-439">Add the following handler settings to the app's *web.config* file to enable ASP.NET Core Module debug log:</span></span>

```xml
<aspNetCore ...>
  <handlerSettings>
    <handlerSetting name="debugLevel" value="file" />
    <handlerSetting name="debugFile" value="c:\temp\ancm.log" />
  </handlerSettings>
</aspNetCore>
```

<span data-ttu-id="a560a-440">Potvrďte, že zadaná cesta k protokolu existuje a že má identita fondu aplikací oprávnění k zápisu do tohoto umístění.</span><span class="sxs-lookup"><span data-stu-id="a560a-440">Confirm that the path specified for the log exists and that the app pool's identity has write permissions to the location.</span></span>

<span data-ttu-id="a560a-441">Další informace naleznete v tématu <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span><span class="sxs-lookup"><span data-stu-id="a560a-441">For more information, see <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span></span>

### <a name="enable-the-developer-exception-page"></a><span data-ttu-id="a560a-442">Povolit stránku výjimky pro vývojáře</span><span class="sxs-lookup"><span data-stu-id="a560a-442">Enable the Developer Exception Page</span></span>

<span data-ttu-id="a560a-443">`ASPNETCORE_ENVIRONMENT` [Proměnnou prostředí lze přidat do web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) ke spuštění aplikace ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="a560a-443">The `ASPNETCORE_ENVIRONMENT` [environment variable can be added to web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) to run the app in the Development environment.</span></span> <span data-ttu-id="a560a-444">Pokud se prostředí nepřepisuje při spuštění aplikace v `UseEnvironment` tvůrci hostitele, nastavení proměnné prostředí umožní, aby se při spuštění aplikace zobrazila [Stránka s výjimkou vývojářů](xref:fundamentals/error-handling) .</span><span class="sxs-lookup"><span data-stu-id="a560a-444">As long as the environment isn't overridden in app startup by `UseEnvironment` on the host builder, setting the environment variable allows the [Developer Exception Page](xref:fundamentals/error-handling) to appear when the app is run.</span></span>

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

<span data-ttu-id="a560a-445">Nastavení proměnné prostředí pro `ASPNETCORE_ENVIRONMENT` se doporučuje jenom pro použití na pracovních a testovacích serverech, které nejsou přístupné pro Internet.</span><span class="sxs-lookup"><span data-stu-id="a560a-445">Setting the environment variable for `ASPNETCORE_ENVIRONMENT` is only recommended for use on staging and testing servers that aren't exposed to the Internet.</span></span> <span data-ttu-id="a560a-446">Po vyřešení potíží odeberte proměnnou prostředí ze souboru *web.config* .</span><span class="sxs-lookup"><span data-stu-id="a560a-446">Remove the environment variable from the *web.config* file after troubleshooting.</span></span> <span data-ttu-id="a560a-447">Informace o nastavení proměnných prostředí v *web.config*naleznete v tématu [environmentVariables Child element of aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).</span><span class="sxs-lookup"><span data-stu-id="a560a-447">For information on setting environment variables in *web.config*, see [environmentVariables child element of aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).</span></span>

### <a name="obtain-data-from-an-app"></a><span data-ttu-id="a560a-448">Získání dat z aplikace</span><span class="sxs-lookup"><span data-stu-id="a560a-448">Obtain data from an app</span></span>

<span data-ttu-id="a560a-449">Pokud aplikace dokáže reagovat na žádosti, získat požadavek, připojení a další data z aplikace pomocí vloženého middlewaru terminálu.</span><span class="sxs-lookup"><span data-stu-id="a560a-449">If an app is capable of responding to requests, obtain request, connection, and additional data from the app using terminal inline middleware.</span></span> <span data-ttu-id="a560a-450">Další informace a ukázka kódu naleznete v tématu <xref:test/troubleshoot#obtain-data-from-an-app> .</span><span class="sxs-lookup"><span data-stu-id="a560a-450">For more information and sample code, see <xref:test/troubleshoot#obtain-data-from-an-app>.</span></span>

### <a name="slow-or-hanging-app-iis"></a><span data-ttu-id="a560a-451">Pomalá nebo zavěšená aplikace (IIS)</span><span class="sxs-lookup"><span data-stu-id="a560a-451">Slow or hanging app (IIS)</span></span>

<span data-ttu-id="a560a-452">*Výpis stavu* systému je snímek paměti systému a může vám pomůže určit příčinu selhání aplikace, selhání při spuštění nebo pomalé aplikace.</span><span class="sxs-lookup"><span data-stu-id="a560a-452">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="a560a-453">Aplikace selže nebo dojde k výjimce.</span><span class="sxs-lookup"><span data-stu-id="a560a-453">App crashes or encounters an exception</span></span>

<span data-ttu-id="a560a-454">Získat a analyzovat výpis z [zasílání zpráv o chybách systému Windows (WER)](/windows/desktop/wer/windows-error-reporting):</span><span class="sxs-lookup"><span data-stu-id="a560a-454">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="a560a-455">Vytvořte složku uchovávající soubory s výpisem stavu systému `c:\dumps` .</span><span class="sxs-lookup"><span data-stu-id="a560a-455">Create a folder to hold crash dump files at `c:\dumps`.</span></span> <span data-ttu-id="a560a-456">Fond aplikací musí mít ke složce přístup pro zápis.</span><span class="sxs-lookup"><span data-stu-id="a560a-456">The app pool must have write access to the folder.</span></span>
1. <span data-ttu-id="a560a-457">Spusťte [skript prostředí PowerShell pro EnableDumps](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1):</span><span class="sxs-lookup"><span data-stu-id="a560a-457">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1):</span></span>
   * <span data-ttu-id="a560a-458">Pokud aplikace používá [model hostování v rámci procesu](xref:host-and-deploy/iis/index#in-process-hosting-model), spusťte skript pro *w3wp.exe*:</span><span class="sxs-lookup"><span data-stu-id="a560a-458">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\EnableDumps w3wp.exe c:\dumps
     ```

   * <span data-ttu-id="a560a-459">Pokud aplikace používá [model hostování mimo proces](xref:host-and-deploy/iis/index#out-of-process-hosting-model), spusťte skript pro *dotnet.exe*:</span><span class="sxs-lookup"><span data-stu-id="a560a-459">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\EnableDumps dotnet.exe c:\dumps
     ```

1. <span data-ttu-id="a560a-460">Spusťte aplikaci za podmínek, které způsobí, že dojde k chybě.</span><span class="sxs-lookup"><span data-stu-id="a560a-460">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="a560a-461">Po chybě spusťte [skript PowerShellu DisableDumps](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1):</span><span class="sxs-lookup"><span data-stu-id="a560a-461">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1):</span></span>
   * <span data-ttu-id="a560a-462">Pokud aplikace používá [model hostování v rámci procesu](xref:host-and-deploy/iis/index#in-process-hosting-model), spusťte skript pro *w3wp.exe*:</span><span class="sxs-lookup"><span data-stu-id="a560a-462">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\DisableDumps w3wp.exe
     ```

   * <span data-ttu-id="a560a-463">Pokud aplikace používá [model hostování mimo proces](xref:host-and-deploy/iis/index#out-of-process-hosting-model), spusťte skript pro *dotnet.exe*:</span><span class="sxs-lookup"><span data-stu-id="a560a-463">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\DisableDumps dotnet.exe
     ```

<span data-ttu-id="a560a-464">Po selhání aplikace a dokončení shromažďování výpisu je možné aplikaci ukončit normálně.</span><span class="sxs-lookup"><span data-stu-id="a560a-464">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="a560a-465">Skript PowerShellu nakonfiguruje WER a shromáždí až pět výpisů paměti pro každou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="a560a-465">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="a560a-466">Výpisy stavu systému můžou zabírat velké množství místa na disku (každý až několik gigabajtů).</span><span class="sxs-lookup"><span data-stu-id="a560a-466">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="a560a-467">Aplikace přestane reagovat, dojde k chybě při spuštění nebo se spustí normálně.</span><span class="sxs-lookup"><span data-stu-id="a560a-467">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="a560a-468">Když *aplikace přestane reagovat (zastaví* se, ale nejedná se o chybu), selže během spuštění nebo se spustí normálně, podívejte [se na soubory výpisu paměti v uživatelském režimu: zvolením nejlepšího](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) nástroje vyberte vhodný nástroj pro vytváření výpisu.</span><span class="sxs-lookup"><span data-stu-id="a560a-468">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="a560a-469">Analýza výpisu paměti</span><span class="sxs-lookup"><span data-stu-id="a560a-469">Analyze the dump</span></span>

<span data-ttu-id="a560a-470">Výpis paměti lze analyzovat pomocí několika přístupů.</span><span class="sxs-lookup"><span data-stu-id="a560a-470">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="a560a-471">Další informace najdete v tématu [Analýza souboru s výpisem stavu v uživatelském režimu](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span><span class="sxs-lookup"><span data-stu-id="a560a-471">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="clear-package-caches"></a><span data-ttu-id="a560a-472">Vymazat mezipaměti balíčků</span><span class="sxs-lookup"><span data-stu-id="a560a-472">Clear package caches</span></span>

<span data-ttu-id="a560a-473">Funkční aplikace může po upgradu .NET Core SDK ve vývojovém počítači nebo změně verzí balíčku v rámci aplikace selhat okamžitě.</span><span class="sxs-lookup"><span data-stu-id="a560a-473">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="a560a-474">V některých případech můžou nesouvislé balíčky při provádění hlavních upgradů poškodit aplikaci.</span><span class="sxs-lookup"><span data-stu-id="a560a-474">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="a560a-475">Většinu těchto problémů můžete vyřešit pomocí následujících pokynů:</span><span class="sxs-lookup"><span data-stu-id="a560a-475">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="a560a-476">Odstraňte složky *bin* a *obj* .</span><span class="sxs-lookup"><span data-stu-id="a560a-476">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="a560a-477">Pomocí příkazu [dotnet All--Clear](/dotnet/core/tools/dotnet-nuget-locals) z příkazového prostředí vymažte mezipaměť balíčku.</span><span class="sxs-lookup"><span data-stu-id="a560a-477">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="a560a-478">Mazání mezipamětí balíčků lze také provést pomocí nástroje [nuget.exe](https://www.nuget.org/downloads) a provedení příkazu `nuget locals all -clear` .</span><span class="sxs-lookup"><span data-stu-id="a560a-478">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="a560a-479">*nuget.exe* není sada odinstalována s desktopovým operačním systémem Windows a je nutné ji získat samostatně z [webu NuGet](https://www.nuget.org/downloads).</span><span class="sxs-lookup"><span data-stu-id="a560a-479">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="a560a-480">Obnovte a znovu sestavte projekt.</span><span class="sxs-lookup"><span data-stu-id="a560a-480">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="a560a-481">Před opětovným nasazením aplikace odstraňte všechny soubory ve složce pro nasazení na serveru.</span><span class="sxs-lookup"><span data-stu-id="a560a-481">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a560a-482">Další zdroje informací</span><span class="sxs-lookup"><span data-stu-id="a560a-482">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:fundamentals/error-handling>
* <xref:host-and-deploy/aspnet-core-module>

### <a name="azure-documentation"></a><span data-ttu-id="a560a-483">Dokumentace k Azure</span><span class="sxs-lookup"><span data-stu-id="a560a-483">Azure documentation</span></span>

* [<span data-ttu-id="a560a-484">Application Insights pro ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a560a-484">Application Insights for ASP.NET Core</span></span>](/azure/application-insights/app-insights-asp-net-core)
* [<span data-ttu-id="a560a-485">Část vzdálené ladění webových aplikací řešení potíží s webovou aplikací v Azure App Service pomocí sady Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a560a-485">Remote debugging web apps section of Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug)
* [<span data-ttu-id="a560a-486">Přehled diagnostiky Azure App Service</span><span class="sxs-lookup"><span data-stu-id="a560a-486">Azure App Service diagnostics overview</span></span>](/azure/app-service/app-service-diagnostics)
* [<span data-ttu-id="a560a-487">Postup: Monitorování aplikací ve službě Azure App Service</span><span class="sxs-lookup"><span data-stu-id="a560a-487">How to: Monitor Apps in Azure App Service</span></span>](/azure/app-service/web-sites-monitor)
* [<span data-ttu-id="a560a-488">Řešení potíží s webovou aplikací v Azure App Service pomocí sady Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a560a-488">Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [<span data-ttu-id="a560a-489">Řešení chyb HTTP z "502 špatné brány" a "nedostupné služby 503" v Azure Web Apps</span><span class="sxs-lookup"><span data-stu-id="a560a-489">Troubleshoot HTTP errors of "502 bad gateway" and "503 service unavailable" in your Azure web apps</span></span>](/azure/app-service/app-service-web-troubleshoot-http-502-http-503)
* [<span data-ttu-id="a560a-490">Řešení potíží s výkonem pomalých webových aplikací ve službě Azure App Service</span><span class="sxs-lookup"><span data-stu-id="a560a-490">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="a560a-491">Nejčastější dotazy týkající se výkonu aplikací pro Web Apps v Azure</span><span class="sxs-lookup"><span data-stu-id="a560a-491">Application performance FAQs for Web Apps in Azure</span></span>](/azure/app-service/app-service-web-availability-performance-application-issues-faq)
* [<span data-ttu-id="a560a-492">Sandbox webové aplikace Azure (omezení spuštění App Service Runtime)</span><span class="sxs-lookup"><span data-stu-id="a560a-492">Azure Web App sandbox (App Service runtime execution limitations)</span></span>](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)
* [<span data-ttu-id="a560a-493">Azure pátek: Azure App Service diagnostické prostředí a řešení potíží (12 minut video)</span><span class="sxs-lookup"><span data-stu-id="a560a-493">Azure Friday: Azure App Service Diagnostic and Troubleshooting Experience (12-minute video)</span></span>](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)

### <a name="visual-studio-documentation"></a><span data-ttu-id="a560a-494">Dokumentace sady Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a560a-494">Visual Studio documentation</span></span>

* [<span data-ttu-id="a560a-495">Vzdálené ladění ASP.NET Core ve službě IIS v Azure v aplikaci Visual Studio 2017</span><span class="sxs-lookup"><span data-stu-id="a560a-495">Remote Debug ASP.NET Core on IIS in Azure in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-azure)
* [<span data-ttu-id="a560a-496">Vzdálené ladění ASP.NET Core na vzdáleném počítači IIS v aplikaci Visual Studio 2017</span><span class="sxs-lookup"><span data-stu-id="a560a-496">Remote Debug ASP.NET Core on a Remote IIS Computer in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-aspnet-on-a-remote-iis-computer)
* [<span data-ttu-id="a560a-497">Informace o ladění pomocí sady Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a560a-497">Learn to debug using Visual Studio</span></span>](/visualstudio/debugger/getting-started-with-the-debugger)

### <a name="visual-studio-code-documentation"></a><span data-ttu-id="a560a-498">Dokumentace k Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a560a-498">Visual Studio Code documentation</span></span>

* [<span data-ttu-id="a560a-499">Ladění pomocí Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a560a-499">Debugging with Visual Studio Code</span></span>](https://code.visualstudio.com/docs/editor/debugging)

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="a560a-500">Tento článek poskytuje informace o běžných chybách při spuštění aplikací a pokyny k diagnostice chyb při nasazení aplikace do Azure App Service nebo IIS:</span><span class="sxs-lookup"><span data-stu-id="a560a-500">This article provides information on common app startup errors and instructions on how to diagnose errors when an app is deployed to Azure App Service or IIS:</span></span>

[<span data-ttu-id="a560a-501">Chyby při spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="a560a-501">App startup errors</span></span>](#app-startup-errors)  
<span data-ttu-id="a560a-502">Vysvětluje běžné spouštění scénářů stavového kódu HTTP.</span><span class="sxs-lookup"><span data-stu-id="a560a-502">Explains common startup HTTP status code scenarios.</span></span>

[<span data-ttu-id="a560a-503">Řešení potíží na Azure App Service</span><span class="sxs-lookup"><span data-stu-id="a560a-503">Troubleshoot on Azure App Service</span></span>](#troubleshoot-on-azure-app-service)  
<span data-ttu-id="a560a-504">Poskytuje rady pro řešení potíží pro aplikace nasazené do Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="a560a-504">Provides troubleshooting advice for apps deployed to Azure App Service.</span></span>

[<span data-ttu-id="a560a-505">Odstraňování potíží ve službě IIS</span><span class="sxs-lookup"><span data-stu-id="a560a-505">Troubleshoot on IIS</span></span>](#troubleshoot-on-iis)  
<span data-ttu-id="a560a-506">Poskytuje rady pro řešení potíží pro aplikace nasazené do služby IIS nebo místně spuštěné v IIS Express.</span><span class="sxs-lookup"><span data-stu-id="a560a-506">Provides troubleshooting advice for apps deployed to IIS or running on IIS Express locally.</span></span> <span data-ttu-id="a560a-507">Pokyny platí pro nasazení systémů Windows Server a Desktop v systému Windows.</span><span class="sxs-lookup"><span data-stu-id="a560a-507">The guidance applies to both Windows Server and Windows desktop deployments.</span></span>

[<span data-ttu-id="a560a-508">Vymazat mezipaměti balíčků</span><span class="sxs-lookup"><span data-stu-id="a560a-508">Clear package caches</span></span>](#clear-package-caches)  
<span data-ttu-id="a560a-509">Vysvětluje, co dělat v případě, že při provádění podstatných upgradů nebo změně verze balíčku dojde k přerušení aplikace v nesouvislých balíčcích.</span><span class="sxs-lookup"><span data-stu-id="a560a-509">Explains what to do when incoherent packages break an app when performing major upgrades or changing package versions.</span></span>

[<span data-ttu-id="a560a-510">Další materiály</span><span class="sxs-lookup"><span data-stu-id="a560a-510">Additional resources</span></span>](#additional-resources)  
<span data-ttu-id="a560a-511">Seznam dalších témat pro odstraňování potíží.</span><span class="sxs-lookup"><span data-stu-id="a560a-511">Lists additional troubleshooting topics.</span></span>

## <a name="app-startup-errors"></a><span data-ttu-id="a560a-512">Chyby při spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="a560a-512">App startup errors</span></span>

<span data-ttu-id="a560a-513">V aplikaci Visual Studio je výchozím nastavením projektu ASP.NET Core [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hostování během ladění.</span><span class="sxs-lookup"><span data-stu-id="a560a-513">In Visual Studio, an ASP.NET Core project defaults to [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hosting during debugging.</span></span> <span data-ttu-id="a560a-514">*502,5 – selhání procesu* nebo *500,30 – chyba* , ke které dojde při místním ladění, se dá diagnostikovat pomocí Rady v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="a560a-514">A *502.5 - Process Failure* or a *500.30 - Start Failure* that occurs when debugging locally can be diagnosed using the advice in this topic.</span></span>

### <a name="40314-forbidden"></a><span data-ttu-id="a560a-515">403,14 zakázané</span><span class="sxs-lookup"><span data-stu-id="a560a-515">403.14 Forbidden</span></span>

<span data-ttu-id="a560a-516">Aplikaci se nepodařilo spustit.</span><span class="sxs-lookup"><span data-stu-id="a560a-516">The app fails to start.</span></span> <span data-ttu-id="a560a-517">Protokoluje se následující chyba:</span><span class="sxs-lookup"><span data-stu-id="a560a-517">The following error is logged:</span></span>

```
The Web server is configured to not list the contents of this directory.
```

<span data-ttu-id="a560a-518">K této chybě obvykle dochází v důsledku poškozeného nasazení v hostitelském systému, který zahrnuje kterýkoli z následujících scénářů:</span><span class="sxs-lookup"><span data-stu-id="a560a-518">The error is usually caused by a broken deployment on the hosting system, which includes any of the following scenarios:</span></span>

* <span data-ttu-id="a560a-519">Aplikace je nasazená do chybné složky v hostitelském systému.</span><span class="sxs-lookup"><span data-stu-id="a560a-519">The app is deployed to the wrong folder on the hosting system.</span></span>
* <span data-ttu-id="a560a-520">Procesu nasazení se nepodařilo přesunout všechny soubory a složky aplikace do složky pro nasazení v hostitelském systému.</span><span class="sxs-lookup"><span data-stu-id="a560a-520">The deployment process failed to move all of the app's files and folders to the deployment folder on the hosting system.</span></span>
* <span data-ttu-id="a560a-521">V nasazení chybí soubor *web.config* , nebo je obsah souboru *web.config* poškozený.</span><span class="sxs-lookup"><span data-stu-id="a560a-521">The *web.config* file is missing from the deployment, or the *web.config* file contents are malformed.</span></span>

<span data-ttu-id="a560a-522">Proveďte tyto kroky:</span><span class="sxs-lookup"><span data-stu-id="a560a-522">Perform the following steps:</span></span>

1. <span data-ttu-id="a560a-523">Odstraňte všechny soubory a složky ze složky pro nasazení v hostitelském systému.</span><span class="sxs-lookup"><span data-stu-id="a560a-523">Delete all of the files and folders from the deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="a560a-524">Znovu nasaďte obsah složky pro *publikování* aplikace do hostitelského systému pomocí běžné metody nasazení, jako je například Visual Studio, PowerShell nebo ruční nasazení:</span><span class="sxs-lookup"><span data-stu-id="a560a-524">Redeploy the contents of the app's *publish* folder to the hosting system using your normal method of deployment, such as Visual Studio, PowerShell, or manual deployment:</span></span>
   * <span data-ttu-id="a560a-525">Potvrďte, že se soubor *web.config* v nasazení nachází a že jeho obsah je správný.</span><span class="sxs-lookup"><span data-stu-id="a560a-525">Confirm that the *web.config* file is present in the deployment and that its contents are correct.</span></span>
   * <span data-ttu-id="a560a-526">Při hostování na Azure App Service potvrďte, že je aplikace nasazená do `D:\home\site\wwwroot` složky.</span><span class="sxs-lookup"><span data-stu-id="a560a-526">When hosting on Azure App Service, confirm that the app is deployed to the `D:\home\site\wwwroot` folder.</span></span>
   * <span data-ttu-id="a560a-527">Když je aplikace hostovaná službou IIS, zkontrolujte, že je aplikace nasazená na **fyzickou cestu** služby IIS, která se zobrazuje v **základním nastavení** **Správce služby IIS**.</span><span class="sxs-lookup"><span data-stu-id="a560a-527">When the app is hosted by IIS, confirm that the app is deployed to the IIS **Physical path** shown in **IIS Manager**'s **Basic Settings**.</span></span>
1. <span data-ttu-id="a560a-528">Ověřte, zda jsou všechny soubory a složky aplikace nasazeny porovnáním nasazení v hostitelském systému s obsahem složky *publikování* projektu.</span><span class="sxs-lookup"><span data-stu-id="a560a-528">Confirm that all of the app's files and folders are deployed by comparing the deployment on the hosting system to the contents of the project's *publish* folder.</span></span>

<span data-ttu-id="a560a-529">Další informace o rozložení publikované aplikace ASP.NET Core najdete v tématu <xref:host-and-deploy/directory-structure> .</span><span class="sxs-lookup"><span data-stu-id="a560a-529">For more information on the layout of a published ASP.NET Core app, see <xref:host-and-deploy/directory-structure>.</span></span> <span data-ttu-id="a560a-530">Další informace o souboru *web.config* naleznete v tématu <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig> .</span><span class="sxs-lookup"><span data-stu-id="a560a-530">For more information on the *web.config* file, see <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>.</span></span>

### <a name="500-internal-server-error"></a><span data-ttu-id="a560a-531">500 – Vnitřní chyba serveru</span><span class="sxs-lookup"><span data-stu-id="a560a-531">500 Internal Server Error</span></span>

<span data-ttu-id="a560a-532">Aplikace se spustí, ale chyba brání serveru v splnění žádosti.</span><span class="sxs-lookup"><span data-stu-id="a560a-532">The app starts, but an error prevents the server from fulfilling the request.</span></span>

<span data-ttu-id="a560a-533">K této chybě dochází v kódu aplikace během spuštění nebo při vytváření odpovědi.</span><span class="sxs-lookup"><span data-stu-id="a560a-533">This error occurs within the app's code during startup or while creating a response.</span></span> <span data-ttu-id="a560a-534">Odpověď možná neobsahuje žádný obsah, nebo se odpověď může zobrazit jako *500 interní chyba serveru* v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="a560a-534">The response may contain no content, or the response may appear as a *500 Internal Server Error* in the browser.</span></span> <span data-ttu-id="a560a-535">Protokol událostí aplikace obvykle uvádí, že aplikace začala normálně.</span><span class="sxs-lookup"><span data-stu-id="a560a-535">The Application Event Log usually states that the app started normally.</span></span> <span data-ttu-id="a560a-536">Z hlediska serveru je to správné.</span><span class="sxs-lookup"><span data-stu-id="a560a-536">From the server's perspective, that's correct.</span></span> <span data-ttu-id="a560a-537">Aplikace se spustila, ale nemůže vygenerovat platnou odpověď.</span><span class="sxs-lookup"><span data-stu-id="a560a-537">The app did start, but it can't generate a valid response.</span></span> <span data-ttu-id="a560a-538">Spusťte aplikaci na příkazovém řádku na serveru nebo povolte řešení potíží pomocí protokolu stdout modulu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a560a-538">Run the app at a command prompt on the server or enable the ASP.NET Core Module stdout log to troubleshoot the problem.</span></span>

### <a name="5000-in-process-handler-load-failure"></a><span data-ttu-id="a560a-539">500,0 selhání načtení obslužné rutiny v procesu</span><span class="sxs-lookup"><span data-stu-id="a560a-539">500.0 In-Process Handler Load Failure</span></span>

<span data-ttu-id="a560a-540">Pracovní proces se nezdařil.</span><span class="sxs-lookup"><span data-stu-id="a560a-540">The worker process fails.</span></span> <span data-ttu-id="a560a-541">Aplikace se nespustí.</span><span class="sxs-lookup"><span data-stu-id="a560a-541">The app doesn't start.</span></span>

<span data-ttu-id="a560a-542">[Modulu ASP.NET Core](xref:host-and-deploy/aspnet-core-module) se nepovedlo najít modul CLR .NET Core a najít obslužnou rutinu žádosti v procesu (*aspnetcorev2_inprocess.dll*).</span><span class="sxs-lookup"><span data-stu-id="a560a-542">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) fails to find the .NET Core CLR and find the in-process request handler (*aspnetcorev2_inprocess.dll*).</span></span> <span data-ttu-id="a560a-543">Ověřte, že:</span><span class="sxs-lookup"><span data-stu-id="a560a-543">Check that:</span></span>

* <span data-ttu-id="a560a-544">Aplikace cílí na balíček NuGet [Microsoft. AspNetCore. Server. IIS](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IIS) nebo na [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="a560a-544">The app targets either the [Microsoft.AspNetCore.Server.IIS](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IIS) NuGet package or the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>
* <span data-ttu-id="a560a-545">Verze ASP.NET Core sdílené architektury, na které je aplikace cílena, je nainstalována na cílovém počítači.</span><span class="sxs-lookup"><span data-stu-id="a560a-545">The version of the ASP.NET Core shared framework that the app targets is installed on the target machine.</span></span>

### <a name="5000-out-of-process-handler-load-failure"></a><span data-ttu-id="a560a-546">500,0 selhání načtení obslužné rutiny mimo proces</span><span class="sxs-lookup"><span data-stu-id="a560a-546">500.0 Out-Of-Process Handler Load Failure</span></span>

<span data-ttu-id="a560a-547">Pracovní proces se nezdařil.</span><span class="sxs-lookup"><span data-stu-id="a560a-547">The worker process fails.</span></span> <span data-ttu-id="a560a-548">Aplikace se nespustí.</span><span class="sxs-lookup"><span data-stu-id="a560a-548">The app doesn't start.</span></span>

<span data-ttu-id="a560a-549">[Modulu ASP.NET Core](xref:host-and-deploy/aspnet-core-module) se nepodařilo najít obslužnou rutinu žádosti mimo proces hostování.</span><span class="sxs-lookup"><span data-stu-id="a560a-549">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) fails to find the out-of-process hosting request handler.</span></span> <span data-ttu-id="a560a-550">Ujistěte se, že *aspnetcorev2_outofprocess.dll* k dispozici v podsložce vedle *aspnetcorev2.dll*.</span><span class="sxs-lookup"><span data-stu-id="a560a-550">Make sure the *aspnetcorev2_outofprocess.dll* is present in a subfolder next to *aspnetcorev2.dll*.</span></span>

### <a name="5025-process-failure"></a><span data-ttu-id="a560a-551">502.5 – Selhání procesu</span><span class="sxs-lookup"><span data-stu-id="a560a-551">502.5 Process Failure</span></span>

<span data-ttu-id="a560a-552">Pracovní proces se nezdařil.</span><span class="sxs-lookup"><span data-stu-id="a560a-552">The worker process fails.</span></span> <span data-ttu-id="a560a-553">Aplikace se nespustí.</span><span class="sxs-lookup"><span data-stu-id="a560a-553">The app doesn't start.</span></span>

<span data-ttu-id="a560a-554">[Modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module) se pokusí spustit pracovní proces, ale jeho spuštění se nezdařilo.</span><span class="sxs-lookup"><span data-stu-id="a560a-554">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the worker process but it fails to start.</span></span> <span data-ttu-id="a560a-555">Příčinou selhání spuštění procesu může být obvykle určení z položek protokolu událostí aplikace a protokolu stdout modulu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a560a-555">The cause of a process startup failure can usually be determined from entries in the Application Event Log and the ASP.NET Core Module stdout log.</span></span>

<span data-ttu-id="a560a-556">Obvyklou chybovou podmínkou je, že aplikace je nesprávně nakonfigurovaná kvůli cílení na verzi ASP.NET Core sdílené rozhraní, které není k dispozici.</span><span class="sxs-lookup"><span data-stu-id="a560a-556">A common failure condition is the app is misconfigured due to targeting a version of the ASP.NET Core shared framework that isn't present.</span></span> <span data-ttu-id="a560a-557">Ověřte, které verze ASP.NET Core sdílené rozhraní jsou nainstalovány v cílovém počítači.</span><span class="sxs-lookup"><span data-stu-id="a560a-557">Check which versions of the ASP.NET Core shared framework are installed on the target machine.</span></span> <span data-ttu-id="a560a-558">*Sdílené rozhraní* je sada sestavení (soubory *. dll* ), které jsou nainstalovány na počítači a na které odkazuje Metapackage jako `Microsoft.AspNetCore.App` .</span><span class="sxs-lookup"><span data-stu-id="a560a-558">The *shared framework* is the set of assemblies (*.dll* files) that are installed on the machine and referenced by a metapackage such as `Microsoft.AspNetCore.App`.</span></span> <span data-ttu-id="a560a-559">Odkaz Metapackage může určovat minimální požadovanou verzi.</span><span class="sxs-lookup"><span data-stu-id="a560a-559">The metapackage reference can specify a minimum required version.</span></span> <span data-ttu-id="a560a-560">Další informace najdete v tématu [sdílené rozhraní](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span><span class="sxs-lookup"><span data-stu-id="a560a-560">For more information, see [The shared framework](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span></span>

<span data-ttu-id="a560a-561">Chybová stránka *selhání procesu 502,5* se vrátí v případě, že při selhání hostování nebo aplikace dojde k chybě pracovního procesu:</span><span class="sxs-lookup"><span data-stu-id="a560a-561">The *502.5 Process Failure* error page is returned when a hosting or app misconfiguration causes the worker process to fail:</span></span>

### <a name="failed-to-start-application-errorcode-0x800700c1"></a><span data-ttu-id="a560a-562">Nepovedlo se spustit aplikaci (ErrorCode ' 0x800700C1 ')</span><span class="sxs-lookup"><span data-stu-id="a560a-562">Failed to start application (ErrorCode '0x800700c1')</span></span>

```
EventID: 1010
Source: IIS AspNetCore Module V2
Failed to start application '/LM/W3SVC/6/ROOT/', ErrorCode '0x800700c1'.
```

<span data-ttu-id="a560a-563">Aplikaci se nepovedlo spustit, protože se nepovedlo načíst sestavení aplikace (*. dll*).</span><span class="sxs-lookup"><span data-stu-id="a560a-563">The app failed to start because the app's assembly (*.dll*) couldn't be loaded.</span></span>

<span data-ttu-id="a560a-564">K této chybě dochází, pokud dojde k neshodě bitová verze mezi publikovanou aplikací a procesem W3wp/IISExpress.</span><span class="sxs-lookup"><span data-stu-id="a560a-564">This error occurs when there's a bitness mismatch between the published app and the w3wp/iisexpress process.</span></span>

<span data-ttu-id="a560a-565">Zkontrolujte, jestli je správně nastavené nastavení 32 fondu aplikací:</span><span class="sxs-lookup"><span data-stu-id="a560a-565">Confirm that the app pool's 32-bit setting is correct:</span></span>

1. <span data-ttu-id="a560a-566">Vyberte fond aplikací v **fondech aplikací**Správce služby IIS.</span><span class="sxs-lookup"><span data-stu-id="a560a-566">Select the app pool in IIS Manager's **Application Pools**.</span></span>
1. <span data-ttu-id="a560a-567">V části **Upravit fond aplikací** na panelu **Akce** vyberte **Upřesnit nastavení** .</span><span class="sxs-lookup"><span data-stu-id="a560a-567">Select **Advanced Settings** under **Edit Application Pool** in the **Actions** panel.</span></span>
1. <span data-ttu-id="a560a-568">Nastavte **možnost povolit 32 – bitové aplikace**:</span><span class="sxs-lookup"><span data-stu-id="a560a-568">Set **Enable 32-Bit Applications**:</span></span>
   * <span data-ttu-id="a560a-569">Pokud nasazujete 32 (x86) aplikaci, nastavte hodnotu na `True` .</span><span class="sxs-lookup"><span data-stu-id="a560a-569">If deploying a 32-bit (x86) app, set the value to `True`.</span></span>
   * <span data-ttu-id="a560a-570">Pokud nasazujete 64 aplikaci (x64), nastavte hodnotu na `False` .</span><span class="sxs-lookup"><span data-stu-id="a560a-570">If deploying a 64-bit (x64) app, set the value to `False`.</span></span>

<span data-ttu-id="a560a-571">Ověřte, že mezi `<Platform>` vlastností MSBuild v souboru projektu a publikovaným bitová verzeem aplikace nedochází ke konfliktu.</span><span class="sxs-lookup"><span data-stu-id="a560a-571">Confirm that there isn't a conflict between a `<Platform>` MSBuild property in the project file and the published bitness of the app.</span></span>

### <a name="connection-reset"></a><span data-ttu-id="a560a-572">Resetování připojení</span><span class="sxs-lookup"><span data-stu-id="a560a-572">Connection reset</span></span>

<span data-ttu-id="a560a-573">Pokud dojde k chybě po odeslání hlaviček, je příliš pozdě pro server, který odešle **500 interní chybu serveru** , když dojde k chybě.</span><span class="sxs-lookup"><span data-stu-id="a560a-573">If an error occurs after the headers are sent, it's too late for the server to send a **500 Internal Server Error** when an error occurs.</span></span> <span data-ttu-id="a560a-574">K tomu často dochází, když dojde k chybě během serializace složitých objektů pro odpověď.</span><span class="sxs-lookup"><span data-stu-id="a560a-574">This often happens when an error occurs during the serialization of complex objects for a response.</span></span> <span data-ttu-id="a560a-575">Tento typ chyby se zobrazí jako chyba *resetování připojení* na klientovi.</span><span class="sxs-lookup"><span data-stu-id="a560a-575">This type of error appears as a *connection reset* error on the client.</span></span> <span data-ttu-id="a560a-576">[Protokolování aplikace](xref:fundamentals/logging/index) může pomoct řešit tyto typy chyb.</span><span class="sxs-lookup"><span data-stu-id="a560a-576">[Application logging](xref:fundamentals/logging/index) can help troubleshoot these types of errors.</span></span>

### <a name="default-startup-limits"></a><span data-ttu-id="a560a-577">Výchozí omezení spouštění</span><span class="sxs-lookup"><span data-stu-id="a560a-577">Default startup limits</span></span>

<span data-ttu-id="a560a-578">[ASP.NET Core modul](xref:host-and-deploy/aspnet-core-module) je nakonfigurovaný s výchozí *startupTimeLimitou* 120 sekund.</span><span class="sxs-lookup"><span data-stu-id="a560a-578">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) is configured with a default *startupTimeLimit* of 120 seconds.</span></span> <span data-ttu-id="a560a-579">Pokud necháte výchozí hodnotu, aplikace může trvat až dvě minuty, než se modul zaznamená selhání procesu.</span><span class="sxs-lookup"><span data-stu-id="a560a-579">When left at the default value, an app may take up to two minutes to start before the module logs a process failure.</span></span> <span data-ttu-id="a560a-580">Informace o konfiguraci modulu naleznete v tématu [atributy elementu aspNetCore](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span><span class="sxs-lookup"><span data-stu-id="a560a-580">For information on configuring the module, see [Attributes of the aspNetCore element](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span></span>

## <a name="troubleshoot-on-azure-app-service"></a><span data-ttu-id="a560a-581">Řešení potíží na Azure App Service</span><span class="sxs-lookup"><span data-stu-id="a560a-581">Troubleshoot on Azure App Service</span></span>

[!INCLUDE [Azure App Service Preview Notice](~/includes/azure-apps-preview-notice.md)]

### <a name="application-event-log-azure-app-service"></a><span data-ttu-id="a560a-582">Protokol událostí aplikace (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="a560a-582">Application Event Log (Azure App Service)</span></span>

<span data-ttu-id="a560a-583">Chcete-li získat přístup k protokolu událostí aplikace, použijte okno **Diagnostika a řešení problémů** v Azure Portal:</span><span class="sxs-lookup"><span data-stu-id="a560a-583">To access the Application Event Log, use the **Diagnose and solve problems** blade in the Azure portal:</span></span>

1. <span data-ttu-id="a560a-584">V Azure Portal otevřete aplikaci v **App Services**.</span><span class="sxs-lookup"><span data-stu-id="a560a-584">In the Azure portal, open the app in **App Services**.</span></span>
1. <span data-ttu-id="a560a-585">Vyberte **Diagnostikovat a řešit problémy**.</span><span class="sxs-lookup"><span data-stu-id="a560a-585">Select **Diagnose and solve problems**.</span></span>
1. <span data-ttu-id="a560a-586">Vyberte **diagnostické nástroje** záhlaví.</span><span class="sxs-lookup"><span data-stu-id="a560a-586">Select the **Diagnostic Tools** heading.</span></span>
1. <span data-ttu-id="a560a-587">V nabídce **nástroje podpory**vyberte tlačítko **události aplikace** .</span><span class="sxs-lookup"><span data-stu-id="a560a-587">Under **Support Tools**, select the **Application Events** button.</span></span>
1. <span data-ttu-id="a560a-588">Projděte si nejnovější chybu, kterou poskytla položka *IIS AspNetCoreModule* nebo *IIS AspNetCoreModule v2* ve **zdrojovém** sloupci.</span><span class="sxs-lookup"><span data-stu-id="a560a-588">Examine the latest error provided by the *IIS AspNetCoreModule* or *IIS AspNetCoreModule V2* entry in the **Source** column.</span></span>

<span data-ttu-id="a560a-589">Alternativou k použití okna **diagnostikovat a řešit problémy** je kontrola souboru protokolu událostí aplikace přímo pomocí [Kudu](https://github.com/projectkudu/kudu/wiki):</span><span class="sxs-lookup"><span data-stu-id="a560a-589">An alternative to using the **Diagnose and solve problems** blade is to examine the Application Event Log file directly using [Kudu](https://github.com/projectkudu/kudu/wiki):</span></span>

1. <span data-ttu-id="a560a-590">Otevřete **Rozšířené nástroje** v oblasti **vývojové nástroje** .</span><span class="sxs-lookup"><span data-stu-id="a560a-590">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="a560a-591">Vyberte tlačítko \*\*Přejít &rarr; \*\* .</span><span class="sxs-lookup"><span data-stu-id="a560a-591">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="a560a-592">Konzola Kudu se otevře v novém okně nebo záložce prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="a560a-592">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="a560a-593">Pomocí navigačního panelu v horní části stránky otevřete **konzolu ladění** a vyberte **cmd**.</span><span class="sxs-lookup"><span data-stu-id="a560a-593">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="a560a-594">Otevřete složku **soubory protokolů** .</span><span class="sxs-lookup"><span data-stu-id="a560a-594">Open the **LogFiles** folder.</span></span>
1. <span data-ttu-id="a560a-595">Vyberte ikonu tužky vedle *eventlog.xml* souboru.</span><span class="sxs-lookup"><span data-stu-id="a560a-595">Select the pencil icon next to the *eventlog.xml* file.</span></span>
1. <span data-ttu-id="a560a-596">Projděte si protokol.</span><span class="sxs-lookup"><span data-stu-id="a560a-596">Examine the log.</span></span> <span data-ttu-id="a560a-597">Posuňte se do dolní části protokolu, abyste viděli nejaktuálnější události.</span><span class="sxs-lookup"><span data-stu-id="a560a-597">Scroll to the bottom of the log to see the most recent events.</span></span>

### <a name="run-the-app-in-the-kudu-console"></a><span data-ttu-id="a560a-598">Spuštění aplikace v konzole Kudu</span><span class="sxs-lookup"><span data-stu-id="a560a-598">Run the app in the Kudu console</span></span>

<span data-ttu-id="a560a-599">Mnoho chyb při spuštění nevytváří užitečné informace v protokolu událostí aplikace.</span><span class="sxs-lookup"><span data-stu-id="a560a-599">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="a560a-600">Tuto chybu můžete zjistit spuštěním aplikace v konzole vzdáleného spuštění [Kudu](https://github.com/projectkudu/kudu/wiki) :</span><span class="sxs-lookup"><span data-stu-id="a560a-600">You can run the app in the [Kudu](https://github.com/projectkudu/kudu/wiki) Remote Execution Console to discover the error:</span></span>

1. <span data-ttu-id="a560a-601">Otevřete **Rozšířené nástroje** v oblasti **vývojové nástroje** .</span><span class="sxs-lookup"><span data-stu-id="a560a-601">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="a560a-602">Vyberte tlačítko \*\*Přejít &rarr; \*\* .</span><span class="sxs-lookup"><span data-stu-id="a560a-602">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="a560a-603">Konzola Kudu se otevře v novém okně nebo záložce prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="a560a-603">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="a560a-604">Pomocí navigačního panelu v horní části stránky otevřete **konzolu ladění** a vyberte **cmd**.</span><span class="sxs-lookup"><span data-stu-id="a560a-604">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>

#### <a name="test-a-32-bit-x86-app"></a><span data-ttu-id="a560a-605">Testování 32 (x86) aplikace</span><span class="sxs-lookup"><span data-stu-id="a560a-605">Test a 32-bit (x86) app</span></span>

<span data-ttu-id="a560a-606">**Aktuální verze**</span><span class="sxs-lookup"><span data-stu-id="a560a-606">**Current release**</span></span>

1. `cd d:\home\site\wwwroot`
1. <span data-ttu-id="a560a-607">Spusťte aplikaci: </span><span class="sxs-lookup"><span data-stu-id="a560a-607">Run the app:</span></span>
   * <span data-ttu-id="a560a-608">Pokud aplikace je [nasazení závislé na rozhraní](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span><span class="sxs-lookup"><span data-stu-id="a560a-608">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

     ```dotnetcli
     dotnet .\{ASSEMBLY NAME}.dll
     ```

   * <span data-ttu-id="a560a-609">Pokud je aplikace samostatná, [nasazení](/dotnet/core/deploying/#self-contained-deployments-scd):</span><span class="sxs-lookup"><span data-stu-id="a560a-609">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

     ```console
     {ASSEMBLY NAME}.exe
     ```

<span data-ttu-id="a560a-610">Výstup konzoly z aplikace, v němž se zobrazují všechny chyby, je předán do konzoly Kudu.</span><span class="sxs-lookup"><span data-stu-id="a560a-610">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="a560a-611">**Nasazení závislé na architektuře spuštěné ve verzi Preview**</span><span class="sxs-lookup"><span data-stu-id="a560a-611">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="a560a-612">*Vyžaduje instalaci rozšíření webu ASP.NET Core {VERSION} (x86) Runtime.*</span><span class="sxs-lookup"><span data-stu-id="a560a-612">*Requires installing the ASP.NET Core {VERSION} (x86) Runtime site extension.*</span></span>

1. <span data-ttu-id="a560a-613">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32` ( `{X.Y}` je verze modulu runtime)</span><span class="sxs-lookup"><span data-stu-id="a560a-613">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="a560a-614">Spusťte aplikaci: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="a560a-614">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="a560a-615">Výstup konzoly z aplikace, v němž se zobrazují všechny chyby, je předán do konzoly Kudu.</span><span class="sxs-lookup"><span data-stu-id="a560a-615">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

#### <a name="test-a-64-bit-x64-app"></a><span data-ttu-id="a560a-616">Testování 64 aplikace (x64)</span><span class="sxs-lookup"><span data-stu-id="a560a-616">Test a 64-bit (x64) app</span></span>

<span data-ttu-id="a560a-617">**Aktuální verze**</span><span class="sxs-lookup"><span data-stu-id="a560a-617">**Current release**</span></span>

* <span data-ttu-id="a560a-618">Pokud je aplikace [nasazením závislého na rozhraní](/dotnet/core/deploying/#framework-dependent-deployments-fdd)64 (x64):</span><span class="sxs-lookup"><span data-stu-id="a560a-618">If the app is a 64-bit (x64) [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>
  1. `cd D:\Program Files\dotnet`
  1. <span data-ttu-id="a560a-619">Spusťte aplikaci: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="a560a-619">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>
* <span data-ttu-id="a560a-620">Pokud je aplikace samostatná, [nasazení](/dotnet/core/deploying/#self-contained-deployments-scd):</span><span class="sxs-lookup"><span data-stu-id="a560a-620">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>
  1. `cd D:\home\site\wwwroot`
  1. <span data-ttu-id="a560a-621">Spusťte aplikaci: `{ASSEMBLY NAME}.exe`</span><span class="sxs-lookup"><span data-stu-id="a560a-621">Run the app: `{ASSEMBLY NAME}.exe`</span></span>

<span data-ttu-id="a560a-622">Výstup konzoly z aplikace, v němž se zobrazují všechny chyby, je předán do konzoly Kudu.</span><span class="sxs-lookup"><span data-stu-id="a560a-622">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="a560a-623">**Nasazení závislé na architektuře spuštěné ve verzi Preview**</span><span class="sxs-lookup"><span data-stu-id="a560a-623">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="a560a-624">*Vyžaduje instalaci rozšíření webu ASP.NET Core {VERSION} (x64) Runtime.*</span><span class="sxs-lookup"><span data-stu-id="a560a-624">*Requires installing the ASP.NET Core {VERSION} (x64) Runtime site extension.*</span></span>

1. <span data-ttu-id="a560a-625">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64` ( `{X.Y}` je verze modulu runtime)</span><span class="sxs-lookup"><span data-stu-id="a560a-625">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="a560a-626">Spusťte aplikaci: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="a560a-626">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="a560a-627">Výstup konzoly z aplikace, v němž se zobrazují všechny chyby, je předán do konzoly Kudu.</span><span class="sxs-lookup"><span data-stu-id="a560a-627">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

### <a name="aspnet-core-module-stdout-log-azure-app-service"></a><span data-ttu-id="a560a-628">Protokol stdout v modulu ASP.NET Core (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="a560a-628">ASP.NET Core Module stdout log (Azure App Service)</span></span>

<span data-ttu-id="a560a-629">Protokol stdout modulu ASP.NET Coree často zaznamenává užitečné chybové zprávy, které se nenašly v protokolu událostí aplikace.</span><span class="sxs-lookup"><span data-stu-id="a560a-629">The ASP.NET Core Module stdout log often records useful error messages not found in the Application Event Log.</span></span> <span data-ttu-id="a560a-630">Postup povolení a zobrazení protokolů stdout:</span><span class="sxs-lookup"><span data-stu-id="a560a-630">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="a560a-631">Přejděte do okna **diagnostikovat a řešit problémy** v Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="a560a-631">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="a560a-632">V části **Vybrat kategorii problému**vyberte tlačítko pro **webovou aplikaci** .</span><span class="sxs-lookup"><span data-stu-id="a560a-632">Under **SELECT PROBLEM CATEGORY**, select the **Web App Down** button.</span></span>
1. <span data-ttu-id="a560a-633">V části **navrhovaná řešení** > **povolte přesměrování protokolu stdout**a výběrem tlačítka **otevřete konzolu Kudu a upravte Web.Config**.</span><span class="sxs-lookup"><span data-stu-id="a560a-633">Under **Suggested Solutions** > **Enable Stdout Log Redirection**, select the button to **Open Kudu Console to edit Web.Config**.</span></span>
1. <span data-ttu-id="a560a-634">V konzole pro **diagnostiku**Kudu otevřete složky v lokalitě s cestou **site**  >  **wwwroot**.</span><span class="sxs-lookup"><span data-stu-id="a560a-634">In the Kudu **Diagnostic Console**, open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="a560a-635">Posuňte se dolů, aby se soubor *web.config* v dolní části seznamu.</span><span class="sxs-lookup"><span data-stu-id="a560a-635">Scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="a560a-636">Klikněte na ikonu tužky vedle *web.config* souboru.</span><span class="sxs-lookup"><span data-stu-id="a560a-636">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="a560a-637">Nastavte **stdoutLogEnabled** na `true` a změňte cestu **stdoutLogFile** na: `\\?\%home%\LogFiles\stdout` .</span><span class="sxs-lookup"><span data-stu-id="a560a-637">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="a560a-638">Výběrem **Uložit** Uložte aktualizovaný soubor *web.config* .</span><span class="sxs-lookup"><span data-stu-id="a560a-638">Select **Save** to save the updated *web.config* file.</span></span>
1. <span data-ttu-id="a560a-639">Vytvořte žádost do aplikace.</span><span class="sxs-lookup"><span data-stu-id="a560a-639">Make a request to the app.</span></span>
1. <span data-ttu-id="a560a-640">Vraťte se na Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="a560a-640">Return to the Azure portal.</span></span> <span data-ttu-id="a560a-641">V oblasti **vývojové nástroje** vyberte okno **Pokročilé nástroje** .</span><span class="sxs-lookup"><span data-stu-id="a560a-641">Select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="a560a-642">Vyberte tlačítko \*\*Přejít &rarr; \*\* .</span><span class="sxs-lookup"><span data-stu-id="a560a-642">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="a560a-643">Konzola Kudu se otevře v novém okně nebo záložce prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="a560a-643">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="a560a-644">Pomocí navigačního panelu v horní části stránky otevřete **konzolu ladění** a vyberte **cmd**.</span><span class="sxs-lookup"><span data-stu-id="a560a-644">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="a560a-645">Vyberte složku **soubory protokolů** .</span><span class="sxs-lookup"><span data-stu-id="a560a-645">Select the **LogFiles** folder.</span></span>
1. <span data-ttu-id="a560a-646">Zkontrolujte **upravený** sloupec a vyberte ikonu tužky a upravte protokol stdout s nejnovějším datem úpravy.</span><span class="sxs-lookup"><span data-stu-id="a560a-646">Inspect the **Modified** column and select the pencil icon to edit the stdout log with the latest modification date.</span></span>
1. <span data-ttu-id="a560a-647">Po otevření souboru protokolu se zobrazí chyba.</span><span class="sxs-lookup"><span data-stu-id="a560a-647">When the log file opens, the error is displayed.</span></span>

<span data-ttu-id="a560a-648">Zakázat protokolování stdout při odstraňování potíží:</span><span class="sxs-lookup"><span data-stu-id="a560a-648">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="a560a-649">V **diagnostické konzole**Kudu se vraťte do umístění **site**  >  **wwwroot** lokality a odhalte tak soubor *web.config* .</span><span class="sxs-lookup"><span data-stu-id="a560a-649">In the Kudu **Diagnostic Console**, return to the path **site** > **wwwroot** to reveal the *web.config* file.</span></span> <span data-ttu-id="a560a-650">Znovu otevřete soubor **web.config** tím, že vyberete ikonu tužky.</span><span class="sxs-lookup"><span data-stu-id="a560a-650">Open the **web.config** file again by selecting the pencil icon.</span></span>
1. <span data-ttu-id="a560a-651">Nastavte **stdoutLogEnabled** na `false` .</span><span class="sxs-lookup"><span data-stu-id="a560a-651">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="a560a-652">Vyberte **Uložit** a soubor uložte.</span><span class="sxs-lookup"><span data-stu-id="a560a-652">Select **Save** to save the file.</span></span>

<span data-ttu-id="a560a-653">Další informace naleznete v tématu <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span><span class="sxs-lookup"><span data-stu-id="a560a-653">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

> [!WARNING]
> <span data-ttu-id="a560a-654">Nepovedlo se zakázat protokol stdout, což může způsobit selhání aplikace nebo serveru.</span><span class="sxs-lookup"><span data-stu-id="a560a-654">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="a560a-655">Velikost souboru protokolu ani počet vytvořených souborů protokolu nijak neomezuje.</span><span class="sxs-lookup"><span data-stu-id="a560a-655">There's no limit on log file size or the number of log files created.</span></span> <span data-ttu-id="a560a-656">K řešení problémů se spouštěním aplikací použijte pouze protokolování STDOUT.</span><span class="sxs-lookup"><span data-stu-id="a560a-656">Only use stdout logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="a560a-657">Pro obecné protokolování v aplikaci ASP.NET Core po spuštění použijte knihovnu protokolování, která omezuje velikost souboru protokolu a otočí protokoly.</span><span class="sxs-lookup"><span data-stu-id="a560a-657">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="a560a-658">Další informace najdete v tématu [Zprostředkovatelé protokolování třetích stran](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="a560a-658">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="aspnet-core-module-debug-log-azure-app-service"></a><span data-ttu-id="a560a-659">Protokol ladění modulu ASP.NET Core (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="a560a-659">ASP.NET Core Module debug log (Azure App Service)</span></span>

<span data-ttu-id="a560a-660">Protokol ladění ASP.NET Core modulu poskytuje další, hlubší protokolování z modulu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a560a-660">The ASP.NET Core Module debug log provides additional, deeper logging from the ASP.NET Core Module.</span></span> <span data-ttu-id="a560a-661">Postup povolení a zobrazení protokolů stdout:</span><span class="sxs-lookup"><span data-stu-id="a560a-661">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="a560a-662">Pro povolení rozšířeného diagnostického protokolu proveďte jednu z následujících akcí:</span><span class="sxs-lookup"><span data-stu-id="a560a-662">To enable the enhanced diagnostic log, perform either of the following:</span></span>
   * <span data-ttu-id="a560a-663">Postupujte podle pokynů v [rozšířených diagnostických protokolech](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) a nakonfigurujte aplikaci pro rozšířené diagnostické protokolování.</span><span class="sxs-lookup"><span data-stu-id="a560a-663">Follow the instructions in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) to configure the app for an enhanced diagnostic logging.</span></span> <span data-ttu-id="a560a-664">Znovu nasaďte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="a560a-664">Redeploy the app.</span></span>
   * <span data-ttu-id="a560a-665">Přidejte `<handlerSettings>` zobrazené v [rozšířených diagnostických protokolech](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) do souboru *web.config* živé aplikace pomocí konzoly Kudu:</span><span class="sxs-lookup"><span data-stu-id="a560a-665">Add the `<handlerSettings>` shown in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) to the live app's *web.config* file using the Kudu console:</span></span>
     1. <span data-ttu-id="a560a-666">Otevřete **Rozšířené nástroje** v oblasti **vývojové nástroje** .</span><span class="sxs-lookup"><span data-stu-id="a560a-666">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="a560a-667">Vyberte tlačítko \*\*Přejít &rarr; \*\* .</span><span class="sxs-lookup"><span data-stu-id="a560a-667">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="a560a-668">Konzola Kudu se otevře v novém okně nebo záložce prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="a560a-668">The Kudu console opens in a new browser tab or window.</span></span>
     1. <span data-ttu-id="a560a-669">Pomocí navigačního panelu v horní části stránky otevřete **konzolu ladění** a vyberte **cmd**.</span><span class="sxs-lookup"><span data-stu-id="a560a-669">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
     1. <span data-ttu-id="a560a-670">Otevřete složky na cestě **lokality**  >  **wwwroot**.</span><span class="sxs-lookup"><span data-stu-id="a560a-670">Open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="a560a-671">Upravte soubor *web.config* tak, že vyberete tlačítko tužky.</span><span class="sxs-lookup"><span data-stu-id="a560a-671">Edit the *web.config* file by selecting the pencil button.</span></span> <span data-ttu-id="a560a-672">Přidejte `<handlerSettings>` oddíl, jak je znázorněno v [rozšířených diagnostických protokolech](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs).</span><span class="sxs-lookup"><span data-stu-id="a560a-672">Add the `<handlerSettings>` section as shown in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs).</span></span> <span data-ttu-id="a560a-673">Vyberte tlačítko **Uložit**.</span><span class="sxs-lookup"><span data-stu-id="a560a-673">Select the **Save** button.</span></span>
1. <span data-ttu-id="a560a-674">Otevřete **Rozšířené nástroje** v oblasti **vývojové nástroje** .</span><span class="sxs-lookup"><span data-stu-id="a560a-674">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="a560a-675">Vyberte tlačítko \*\*Přejít &rarr; \*\* .</span><span class="sxs-lookup"><span data-stu-id="a560a-675">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="a560a-676">Konzola Kudu se otevře v novém okně nebo záložce prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="a560a-676">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="a560a-677">Pomocí navigačního panelu v horní části stránky otevřete **konzolu ladění** a vyberte **cmd**.</span><span class="sxs-lookup"><span data-stu-id="a560a-677">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="a560a-678">Otevřete složky na cestě **lokality**  >  **wwwroot**.</span><span class="sxs-lookup"><span data-stu-id="a560a-678">Open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="a560a-679">Pokud jste nezadali cestu k souboru *aspnetcore-Debug. log* , soubor se zobrazí v seznamu.</span><span class="sxs-lookup"><span data-stu-id="a560a-679">If you didn't supply a path for the *aspnetcore-debug.log* file, the file appears in the list.</span></span> <span data-ttu-id="a560a-680">Pokud jste zadali cestu, přejděte do umístění souboru protokolu.</span><span class="sxs-lookup"><span data-stu-id="a560a-680">If you supplied a path, navigate to the location of the log file.</span></span>
1. <span data-ttu-id="a560a-681">Otevřete soubor protokolu s tlačítkem tužky vedle názvu souboru.</span><span class="sxs-lookup"><span data-stu-id="a560a-681">Open the log file with the pencil button next to the file name.</span></span>

<span data-ttu-id="a560a-682">Zakázat protokolování ladění, pokud je dokončeno odstraňování potíží:</span><span class="sxs-lookup"><span data-stu-id="a560a-682">Disable debug logging when troubleshooting is complete:</span></span>

<span data-ttu-id="a560a-683">Chcete-li zakázat rozšířený protokol ladění, proveďte jednu z následujících akcí:</span><span class="sxs-lookup"><span data-stu-id="a560a-683">To disable the enhanced debug log, perform either of the following:</span></span>

* <span data-ttu-id="a560a-684">Odeberte `<handlerSettings>` soubor z *web.config* místně a aplikaci znovu nasaďte.</span><span class="sxs-lookup"><span data-stu-id="a560a-684">Remove the `<handlerSettings>` from the *web.config* file locally and redeploy the app.</span></span>
* <span data-ttu-id="a560a-685">Pomocí konzoly Kudu upravte soubor *web.config* a odeberte `<handlerSettings>` oddíl.</span><span class="sxs-lookup"><span data-stu-id="a560a-685">Use the Kudu console to edit the *web.config* file and remove the `<handlerSettings>` section.</span></span> <span data-ttu-id="a560a-686">Soubor uložte.</span><span class="sxs-lookup"><span data-stu-id="a560a-686">Save the file.</span></span>

<span data-ttu-id="a560a-687">Další informace naleznete v tématu <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span><span class="sxs-lookup"><span data-stu-id="a560a-687">For more information, see <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span></span>

> [!WARNING]
> <span data-ttu-id="a560a-688">Nepovedlo se zakázat protokol ladění, může způsobit selhání aplikace nebo serveru.</span><span class="sxs-lookup"><span data-stu-id="a560a-688">Failure to disable the debug log can lead to app or server failure.</span></span> <span data-ttu-id="a560a-689">Velikost souboru protokolu není nijak omezena.</span><span class="sxs-lookup"><span data-stu-id="a560a-689">There's no limit on log file size.</span></span> <span data-ttu-id="a560a-690">K řešení problémů se spouštěním aplikace používejte pouze protokolování ladění.</span><span class="sxs-lookup"><span data-stu-id="a560a-690">Only use debug logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="a560a-691">Pro obecné protokolování v aplikaci ASP.NET Core po spuštění použijte knihovnu protokolování, která omezuje velikost souboru protokolu a otočí protokoly.</span><span class="sxs-lookup"><span data-stu-id="a560a-691">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="a560a-692">Další informace najdete v tématu [Zprostředkovatelé protokolování třetích stran](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="a560a-692">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="slow-or-hanging-app-azure-app-service"></a><span data-ttu-id="a560a-693">Pomalá nebo zavěšená aplikace (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="a560a-693">Slow or hanging app (Azure App Service)</span></span>

<span data-ttu-id="a560a-694">Pokud aplikace reaguje pomalu nebo přestane reagovat na žádost, přečtěte si následující články:</span><span class="sxs-lookup"><span data-stu-id="a560a-694">When an app responds slowly or hangs on a request, see the following articles:</span></span>

* [<span data-ttu-id="a560a-695">Řešení potíží s výkonem pomalých webových aplikací ve službě Azure App Service</span><span class="sxs-lookup"><span data-stu-id="a560a-695">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="a560a-696">K zachycení výpisu problémů s přerušovanou výjimkou nebo problémy s výkonem ve webové aplikaci Azure použijte rozšíření pro diagnostiku zhroucení serveru.</span><span class="sxs-lookup"><span data-stu-id="a560a-696">Use Crash Diagnoser Site Extension to Capture Dump for Intermittent Exception issues or performance issues on Azure Web App</span></span>](https://blogs.msdn.microsoft.com/asiatech/2015/12/28/use-crash-diagnoser-site-extension-to-capture-dump-for-intermittent-exception-issues-or-performance-issues-on-azure-web-app/)

### <a name="monitoring-blades"></a><span data-ttu-id="a560a-697">Okna monitorování</span><span class="sxs-lookup"><span data-stu-id="a560a-697">Monitoring blades</span></span>

<span data-ttu-id="a560a-698">Okna monitorování poskytují alternativní možnosti řešení potíží pro metody popsané dříve v tématu.</span><span class="sxs-lookup"><span data-stu-id="a560a-698">Monitoring blades provide an alternative troubleshooting experience to the methods described earlier in the topic.</span></span> <span data-ttu-id="a560a-699">Pomocí těchto oken můžete diagnostikovat chyby 500-Series.</span><span class="sxs-lookup"><span data-stu-id="a560a-699">These blades can be used to diagnose 500-series errors.</span></span>

<span data-ttu-id="a560a-700">Potvrďte, že jsou nainstalovaná rozšíření ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a560a-700">Confirm that the ASP.NET Core Extensions are installed.</span></span> <span data-ttu-id="a560a-701">Pokud nejsou rozšíření nainstalována, nainstalujte je ručně:</span><span class="sxs-lookup"><span data-stu-id="a560a-701">If the extensions aren't installed, install them manually:</span></span>

1. <span data-ttu-id="a560a-702">V části okno **vývojové nástroje** vyberte okno **rozšíření** .</span><span class="sxs-lookup"><span data-stu-id="a560a-702">In the **DEVELOPMENT TOOLS** blade section, select the **Extensions** blade.</span></span>
1. <span data-ttu-id="a560a-703">V seznamu by se měla zobrazit **rozšíření ASP.NET Core** .</span><span class="sxs-lookup"><span data-stu-id="a560a-703">The **ASP.NET Core Extensions** should appear in the list.</span></span>
1. <span data-ttu-id="a560a-704">Pokud nejsou rozšíření nainstalovaná, vyberte tlačítko **Přidat** .</span><span class="sxs-lookup"><span data-stu-id="a560a-704">If the extensions aren't installed, select the **Add** button.</span></span>
1. <span data-ttu-id="a560a-705">Ze seznamu vyberte **rozšíření ASP.NET Core** .</span><span class="sxs-lookup"><span data-stu-id="a560a-705">Choose the **ASP.NET Core Extensions** from the list.</span></span>
1. <span data-ttu-id="a560a-706">Kliknutím na **OK** přijměte právní podmínky.</span><span class="sxs-lookup"><span data-stu-id="a560a-706">Select **OK** to accept the legal terms.</span></span>
1. <span data-ttu-id="a560a-707">V okně **Přidat rozšíření** vyberte **OK** .</span><span class="sxs-lookup"><span data-stu-id="a560a-707">Select **OK** on the **Add extension** blade.</span></span>
1. <span data-ttu-id="a560a-708">Informační automaticky otevíraná zpráva indikuje, že se rozšíření úspěšně nainstalovala.</span><span class="sxs-lookup"><span data-stu-id="a560a-708">An informational pop-up message indicates when the extensions are successfully installed.</span></span>

<span data-ttu-id="a560a-709">Pokud není povoleno protokolování stdout, postupujte následovně:</span><span class="sxs-lookup"><span data-stu-id="a560a-709">If stdout logging isn't enabled, follow these steps:</span></span>

1. <span data-ttu-id="a560a-710">V Azure Portal v oblasti **vývojové nástroje** vyberte okno **Pokročilé nástroje** .</span><span class="sxs-lookup"><span data-stu-id="a560a-710">In the Azure portal, select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="a560a-711">Vyberte tlačítko \*\*Přejít &rarr; \*\* .</span><span class="sxs-lookup"><span data-stu-id="a560a-711">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="a560a-712">Konzola Kudu se otevře v novém okně nebo záložce prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="a560a-712">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="a560a-713">Pomocí navigačního panelu v horní části stránky otevřete **konzolu ladění** a vyberte **cmd**.</span><span class="sxs-lookup"><span data-stu-id="a560a-713">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="a560a-714">Otevřete složky **pro cestu** > **wwwroot** a posuňte se dolů, aby se soubor *web.config* v dolní části seznamu.</span><span class="sxs-lookup"><span data-stu-id="a560a-714">Open the folders to the path **site** > **wwwroot** and scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="a560a-715">Klikněte na ikonu tužky vedle *web.config* souboru.</span><span class="sxs-lookup"><span data-stu-id="a560a-715">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="a560a-716">Nastavte **stdoutLogEnabled** na `true` a změňte cestu **stdoutLogFile** na: `\\?\%home%\LogFiles\stdout` .</span><span class="sxs-lookup"><span data-stu-id="a560a-716">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="a560a-717">Výběrem **Uložit** Uložte aktualizovaný soubor *web.config* .</span><span class="sxs-lookup"><span data-stu-id="a560a-717">Select **Save** to save the updated *web.config* file.</span></span>

<span data-ttu-id="a560a-718">Pokračovat k aktivaci protokolování diagnostiky:</span><span class="sxs-lookup"><span data-stu-id="a560a-718">Proceed to activate diagnostic logging:</span></span>

1. <span data-ttu-id="a560a-719">V Azure Portal vyberte okno **diagnostické protokoly** .</span><span class="sxs-lookup"><span data-stu-id="a560a-719">In the Azure portal, select the **Diagnostics logs** blade.</span></span>
1. <span data-ttu-id="a560a-720">Vyberte přepínač **zapnuto** pro **protokolování aplikace (systém souborů)** a **podrobné chybové zprávy**.</span><span class="sxs-lookup"><span data-stu-id="a560a-720">Select the **On** switch for **Application Logging (Filesystem)** and **Detailed error messages**.</span></span> <span data-ttu-id="a560a-721">V horní části okna vyberte tlačítko **Uložit** .</span><span class="sxs-lookup"><span data-stu-id="a560a-721">Select the **Save** button at the top of the blade.</span></span>
1. <span data-ttu-id="a560a-722">Chcete-li zahrnout trasování chybných požadavků, označované také jako protokolování událostí neúspěšných požadavků na FREB, vyberte přepínač **zapnuto** pro **trasování chybných požadavků**.</span><span class="sxs-lookup"><span data-stu-id="a560a-722">To include failed request tracing, also known as Failed Request Event Buffering (FREB) logging, select the **On** switch for **Failed request tracing**.</span></span>
1. <span data-ttu-id="a560a-723">Vyberte okno **log Stream** , které je uvedeno hned pod oknem **diagnostické protokoly** na portálu.</span><span class="sxs-lookup"><span data-stu-id="a560a-723">Select the **Log stream** blade, which is listed immediately under the **Diagnostics logs** blade in the portal.</span></span>
1. <span data-ttu-id="a560a-724">Vytvořte žádost do aplikace.</span><span class="sxs-lookup"><span data-stu-id="a560a-724">Make a request to the app.</span></span>
1. <span data-ttu-id="a560a-725">V datech streamu protokolu se označuje příčina chyby.</span><span class="sxs-lookup"><span data-stu-id="a560a-725">Within the log stream data, the cause of the error is indicated.</span></span>

<span data-ttu-id="a560a-726">Nezapomeňte zakázat protokolování stdout po dokončení řešení potíží.</span><span class="sxs-lookup"><span data-stu-id="a560a-726">Be sure to disable stdout logging when troubleshooting is complete.</span></span>

<span data-ttu-id="a560a-727">Zobrazení protokolů pro trasování chybných požadavků (protokoly FREB):</span><span class="sxs-lookup"><span data-stu-id="a560a-727">To view the failed request tracing logs (FREB logs):</span></span>

1. <span data-ttu-id="a560a-728">Přejděte do okna **diagnostikovat a řešit problémy** v Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="a560a-728">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="a560a-729">V oblasti **nástroje podpory** v postranním panelu vyberte **protokoly pro trasování chybných požadavků** .</span><span class="sxs-lookup"><span data-stu-id="a560a-729">Select **Failed Request Tracing Logs** from the **SUPPORT TOOLS** area of the sidebar.</span></span>

<span data-ttu-id="a560a-730">V [části Povolení protokolování diagnostiky pro webové aplikace v Azure App Service tématu](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) a [Nejčastější dotazy týkající se výkonu aplikace pro Web Apps v Azure: návody zapnout trasování chybných požadavků?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing) Další informace.</span><span class="sxs-lookup"><span data-stu-id="a560a-730">See [Failed request traces section of the Enable diagnostics logging for web apps in Azure App Service topic](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) and the [Application performance FAQs for Web Apps in Azure: How do I turn on failed request tracing?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing) for more information.</span></span>

<span data-ttu-id="a560a-731">Další informace najdete v tématu [Povolení protokolování diagnostiky pro webové aplikace v Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log).</span><span class="sxs-lookup"><span data-stu-id="a560a-731">For more information, see [Enable diagnostics logging for web apps in Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log).</span></span>

> [!WARNING]
> <span data-ttu-id="a560a-732">Nepovedlo se zakázat protokol stdout, což může způsobit selhání aplikace nebo serveru.</span><span class="sxs-lookup"><span data-stu-id="a560a-732">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="a560a-733">Velikost souboru protokolu ani počet vytvořených souborů protokolu nijak neomezuje.</span><span class="sxs-lookup"><span data-stu-id="a560a-733">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="a560a-734">Pro rutinu protokolování v aplikaci ASP.NET Core použijte knihovnu protokolování, která omezuje velikost souboru protokolu a otočí protokoly.</span><span class="sxs-lookup"><span data-stu-id="a560a-734">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="a560a-735">Další informace najdete v tématu [Zprostředkovatelé protokolování třetích stran](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="a560a-735">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

## <a name="troubleshoot-on-iis"></a><span data-ttu-id="a560a-736">Odstraňování potíží ve službě IIS</span><span class="sxs-lookup"><span data-stu-id="a560a-736">Troubleshoot on IIS</span></span>

### <a name="application-event-log-iis"></a><span data-ttu-id="a560a-737">Protokol událostí aplikace (IIS)</span><span class="sxs-lookup"><span data-stu-id="a560a-737">Application Event Log (IIS)</span></span>

<span data-ttu-id="a560a-738">Přístup k protokolu událostí aplikace:</span><span class="sxs-lookup"><span data-stu-id="a560a-738">Access the Application Event Log:</span></span>

1. <span data-ttu-id="a560a-739">Otevřete nabídku Start, vyhledejte *Prohlížeč událostí*a vyberte aplikaci **Prohlížeč událostí** .</span><span class="sxs-lookup"><span data-stu-id="a560a-739">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="a560a-740">V **Prohlížeč událostí**otevřete uzel **protokoly systému Windows** .</span><span class="sxs-lookup"><span data-stu-id="a560a-740">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="a560a-741">Výběrem **aplikace** otevřete protokol událostí aplikace.</span><span class="sxs-lookup"><span data-stu-id="a560a-741">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="a560a-742">Vyhledá chyby spojené s chybnou aplikací.</span><span class="sxs-lookup"><span data-stu-id="a560a-742">Search for errors associated with the failing app.</span></span> <span data-ttu-id="a560a-743">Chyby mají ve *zdrojovém* sloupci hodnotu *modulu IIS AspNetCore* nebo *modul IIS Express AspNetCore* .</span><span class="sxs-lookup"><span data-stu-id="a560a-743">Errors have a value of *IIS AspNetCore Module* or *IIS Express AspNetCore Module* in the *Source* column.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="a560a-744">Spuštění aplikace na příkazovém řádku</span><span class="sxs-lookup"><span data-stu-id="a560a-744">Run the app at a command prompt</span></span>

<span data-ttu-id="a560a-745">Mnoho chyb při spuštění nevytváří užitečné informace v protokolu událostí aplikace.</span><span class="sxs-lookup"><span data-stu-id="a560a-745">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="a560a-746">Příčinu některých chyb můžete najít spuštěním aplikace na příkazovém řádku v hostitelském systému.</span><span class="sxs-lookup"><span data-stu-id="a560a-746">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span>

#### <a name="framework-dependent-deployment"></a><span data-ttu-id="a560a-747">Nasazení závislé na rozhraní</span><span class="sxs-lookup"><span data-stu-id="a560a-747">Framework-dependent deployment</span></span>

<span data-ttu-id="a560a-748">Pokud aplikace je [nasazení závislé na rozhraní](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span><span class="sxs-lookup"><span data-stu-id="a560a-748">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

1. <span data-ttu-id="a560a-749">Na příkazovém řádku přejděte do složky pro nasazení a spusťte aplikaci spuštěním sestavení aplikace pomocí *dotnet.exe*.</span><span class="sxs-lookup"><span data-stu-id="a560a-749">At a command prompt, navigate to the deployment folder and run the app by executing the app's assembly with *dotnet.exe*.</span></span> <span data-ttu-id="a560a-750">V následujícím příkazu nahraďte název sestavení aplikace pro \<assembly_name> : `dotnet .\<assembly_name>.dll` .</span><span class="sxs-lookup"><span data-stu-id="a560a-750">In the following command, substitute the name of the app's assembly for \<assembly_name>: `dotnet .\<assembly_name>.dll`.</span></span>
1. <span data-ttu-id="a560a-751">Výstup konzoly z aplikace, který zobrazuje všechny chyby, je zapsán do okna konzoly.</span><span class="sxs-lookup"><span data-stu-id="a560a-751">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="a560a-752">Pokud dojde k chybám při podání žádosti do aplikace, vytvořte žádost na hostitele a port, kde Kestrel naslouchá.</span><span class="sxs-lookup"><span data-stu-id="a560a-752">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="a560a-753">Pomocí výchozího hostitele a příspěvku vytvořte žádost `http://localhost:5000/` .</span><span class="sxs-lookup"><span data-stu-id="a560a-753">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="a560a-754">Pokud aplikace reaguje normálně na adrese koncového bodu Kestrel, problém je pravděpodobnější v souvislosti s konfigurací hostování a méně pravděpodobným v rámci aplikace.</span><span class="sxs-lookup"><span data-stu-id="a560a-754">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

#### <a name="self-contained-deployment"></a><span data-ttu-id="a560a-755">Samostatně zahrnuté nasazení</span><span class="sxs-lookup"><span data-stu-id="a560a-755">Self-contained deployment</span></span>

<span data-ttu-id="a560a-756">Pokud je aplikace samostatná, [nasazení](/dotnet/core/deploying/#self-contained-deployments-scd):</span><span class="sxs-lookup"><span data-stu-id="a560a-756">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

1. <span data-ttu-id="a560a-757">Na příkazovém řádku přejděte do složky pro nasazení a spusťte spustitelný soubor aplikace.</span><span class="sxs-lookup"><span data-stu-id="a560a-757">At a command prompt, navigate to the deployment folder and run the app's executable.</span></span> <span data-ttu-id="a560a-758">V následujícím příkazu nahraďte název sestavení aplikace pro \<assembly_name> : `<assembly_name>.exe` .</span><span class="sxs-lookup"><span data-stu-id="a560a-758">In the following command, substitute the name of the app's assembly for \<assembly_name>: `<assembly_name>.exe`.</span></span>
1. <span data-ttu-id="a560a-759">Výstup konzoly z aplikace, který zobrazuje všechny chyby, je zapsán do okna konzoly.</span><span class="sxs-lookup"><span data-stu-id="a560a-759">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="a560a-760">Pokud dojde k chybám při podání žádosti do aplikace, vytvořte žádost na hostitele a port, kde Kestrel naslouchá.</span><span class="sxs-lookup"><span data-stu-id="a560a-760">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="a560a-761">Pomocí výchozího hostitele a příspěvku vytvořte žádost `http://localhost:5000/` .</span><span class="sxs-lookup"><span data-stu-id="a560a-761">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="a560a-762">Pokud aplikace reaguje normálně na adrese koncového bodu Kestrel, problém je pravděpodobnější v souvislosti s konfigurací hostování a méně pravděpodobným v rámci aplikace.</span><span class="sxs-lookup"><span data-stu-id="a560a-762">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

### <a name="aspnet-core-module-stdout-log-iis"></a><span data-ttu-id="a560a-763">Protokol stdout v modulu ASP.NET Core (IIS)</span><span class="sxs-lookup"><span data-stu-id="a560a-763">ASP.NET Core Module stdout log (IIS)</span></span>

<span data-ttu-id="a560a-764">Postup povolení a zobrazení protokolů stdout:</span><span class="sxs-lookup"><span data-stu-id="a560a-764">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="a560a-765">Přejděte do složky pro nasazení webu v hostitelském systému.</span><span class="sxs-lookup"><span data-stu-id="a560a-765">Navigate to the site's deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="a560a-766">Pokud složka *logs* není k dispozici, vytvořte složku.</span><span class="sxs-lookup"><span data-stu-id="a560a-766">If the *logs* folder isn't present, create the folder.</span></span> <span data-ttu-id="a560a-767">Pokyny k tomu, jak povolit MSBuild pro vytvoření složky *logs* v nasazení automaticky, najdete v tématu [Struktura adresáře](xref:host-and-deploy/directory-structure) .</span><span class="sxs-lookup"><span data-stu-id="a560a-767">For instructions on how to enable MSBuild to create the *logs* folder in the deployment automatically, see the [Directory structure](xref:host-and-deploy/directory-structure) topic.</span></span>
1. <span data-ttu-id="a560a-768">Upravte soubor *web.config* .</span><span class="sxs-lookup"><span data-stu-id="a560a-768">Edit the *web.config* file.</span></span> <span data-ttu-id="a560a-769">Nastavte **stdoutLogEnabled** na `true` a změňte cestu **stdoutLogFile** tak, aby odkazovala na složku *logs* (například `.\logs\stdout` ).</span><span class="sxs-lookup"><span data-stu-id="a560a-769">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to point to the *logs* folder (for example, `.\logs\stdout`).</span></span> <span data-ttu-id="a560a-770">`stdout` v cestě je předpona názvu souboru protokolu.</span><span class="sxs-lookup"><span data-stu-id="a560a-770">`stdout` in the path is the log file name prefix.</span></span> <span data-ttu-id="a560a-771">Časové razítko, ID procesu a Přípona souboru se automaticky přidají při vytvoření protokolu.</span><span class="sxs-lookup"><span data-stu-id="a560a-771">A timestamp, process id, and file extension are added automatically when the log is created.</span></span> <span data-ttu-id="a560a-772">`stdout`Jako předpona názvu souboru se používá typický soubor protokolu s názvem *stdout_20180205184032_5412. log*.</span><span class="sxs-lookup"><span data-stu-id="a560a-772">Using `stdout` as the file name prefix, a typical log file is named *stdout_20180205184032_5412.log*.</span></span>
1. <span data-ttu-id="a560a-773">Zajistěte, aby identita fondu aplikací měla oprávnění k zápisu do složky *logs* .</span><span class="sxs-lookup"><span data-stu-id="a560a-773">Ensure your application pool's identity has write permissions to the *logs* folder.</span></span>
1. <span data-ttu-id="a560a-774">Uložte aktualizovaný soubor *web.config* .</span><span class="sxs-lookup"><span data-stu-id="a560a-774">Save the updated *web.config* file.</span></span>
1. <span data-ttu-id="a560a-775">Vytvořte žádost do aplikace.</span><span class="sxs-lookup"><span data-stu-id="a560a-775">Make a request to the app.</span></span>
1. <span data-ttu-id="a560a-776">Přejděte do složky *logs* .</span><span class="sxs-lookup"><span data-stu-id="a560a-776">Navigate to the *logs* folder.</span></span> <span data-ttu-id="a560a-777">Vyhledejte a otevřete nejnovější protokol STDOUT.</span><span class="sxs-lookup"><span data-stu-id="a560a-777">Find and open the most recent stdout log.</span></span>
1. <span data-ttu-id="a560a-778">Prostudujte si protokol o chybách.</span><span class="sxs-lookup"><span data-stu-id="a560a-778">Study the log for errors.</span></span>

<span data-ttu-id="a560a-779">Zakázat protokolování stdout při odstraňování potíží:</span><span class="sxs-lookup"><span data-stu-id="a560a-779">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="a560a-780">Upravte soubor *web.config* .</span><span class="sxs-lookup"><span data-stu-id="a560a-780">Edit the *web.config* file.</span></span>
1. <span data-ttu-id="a560a-781">Nastavte **stdoutLogEnabled** na `false` .</span><span class="sxs-lookup"><span data-stu-id="a560a-781">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="a560a-782">Soubor uložte.</span><span class="sxs-lookup"><span data-stu-id="a560a-782">Save the file.</span></span>

<span data-ttu-id="a560a-783">Další informace naleznete v tématu <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span><span class="sxs-lookup"><span data-stu-id="a560a-783">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

> [!WARNING]
> <span data-ttu-id="a560a-784">Nepovedlo se zakázat protokol stdout, což může způsobit selhání aplikace nebo serveru.</span><span class="sxs-lookup"><span data-stu-id="a560a-784">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="a560a-785">Velikost souboru protokolu ani počet vytvořených souborů protokolu nijak neomezuje.</span><span class="sxs-lookup"><span data-stu-id="a560a-785">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="a560a-786">Pro rutinu protokolování v aplikaci ASP.NET Core použijte knihovnu protokolování, která omezuje velikost souboru protokolu a otočí protokoly.</span><span class="sxs-lookup"><span data-stu-id="a560a-786">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="a560a-787">Další informace najdete v tématu [Zprostředkovatelé protokolování třetích stran](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="a560a-787">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="aspnet-core-module-debug-log-iis"></a><span data-ttu-id="a560a-788">Protokol ladění modulu ASP.NET Core (IIS)</span><span class="sxs-lookup"><span data-stu-id="a560a-788">ASP.NET Core Module debug log (IIS)</span></span>

<span data-ttu-id="a560a-789">Do souboru *web.config* aplikace přidejte následující nastavení obslužných rutin, aby se povolil protokol ladění ASP.NET Core modulu:</span><span class="sxs-lookup"><span data-stu-id="a560a-789">Add the following handler settings to the app's *web.config* file to enable ASP.NET Core Module debug log:</span></span>

```xml
<aspNetCore ...>
  <handlerSettings>
    <handlerSetting name="debugLevel" value="file" />
    <handlerSetting name="debugFile" value="c:\temp\ancm.log" />
  </handlerSettings>
</aspNetCore>
```

<span data-ttu-id="a560a-790">Potvrďte, že zadaná cesta k protokolu existuje a že má identita fondu aplikací oprávnění k zápisu do tohoto umístění.</span><span class="sxs-lookup"><span data-stu-id="a560a-790">Confirm that the path specified for the log exists and that the app pool's identity has write permissions to the location.</span></span>

<span data-ttu-id="a560a-791">Další informace naleznete v tématu <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span><span class="sxs-lookup"><span data-stu-id="a560a-791">For more information, see <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span></span>

### <a name="enable-the-developer-exception-page"></a><span data-ttu-id="a560a-792">Povolit stránku výjimky pro vývojáře</span><span class="sxs-lookup"><span data-stu-id="a560a-792">Enable the Developer Exception Page</span></span>

<span data-ttu-id="a560a-793">`ASPNETCORE_ENVIRONMENT` [Proměnnou prostředí lze přidat do web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) ke spuštění aplikace ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="a560a-793">The `ASPNETCORE_ENVIRONMENT` [environment variable can be added to web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) to run the app in the Development environment.</span></span> <span data-ttu-id="a560a-794">Pokud se prostředí nepřepisuje při spuštění aplikace v `UseEnvironment` tvůrci hostitele, nastavení proměnné prostředí umožní, aby se při spuštění aplikace zobrazila [Stránka s výjimkou vývojářů](xref:fundamentals/error-handling) .</span><span class="sxs-lookup"><span data-stu-id="a560a-794">As long as the environment isn't overridden in app startup by `UseEnvironment` on the host builder, setting the environment variable allows the [Developer Exception Page](xref:fundamentals/error-handling) to appear when the app is run.</span></span>

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

<span data-ttu-id="a560a-795">Nastavení proměnné prostředí pro `ASPNETCORE_ENVIRONMENT` se doporučuje jenom pro použití na pracovních a testovacích serverech, které nejsou přístupné pro Internet.</span><span class="sxs-lookup"><span data-stu-id="a560a-795">Setting the environment variable for `ASPNETCORE_ENVIRONMENT` is only recommended for use on staging and testing servers that aren't exposed to the Internet.</span></span> <span data-ttu-id="a560a-796">Po vyřešení potíží odeberte proměnnou prostředí ze souboru *web.config* .</span><span class="sxs-lookup"><span data-stu-id="a560a-796">Remove the environment variable from the *web.config* file after troubleshooting.</span></span> <span data-ttu-id="a560a-797">Informace o nastavení proměnných prostředí v *web.config*naleznete v tématu [environmentVariables Child element of aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).</span><span class="sxs-lookup"><span data-stu-id="a560a-797">For information on setting environment variables in *web.config*, see [environmentVariables child element of aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).</span></span>

### <a name="obtain-data-from-an-app"></a><span data-ttu-id="a560a-798">Získání dat z aplikace</span><span class="sxs-lookup"><span data-stu-id="a560a-798">Obtain data from an app</span></span>

<span data-ttu-id="a560a-799">Pokud aplikace dokáže reagovat na žádosti, získat požadavek, připojení a další data z aplikace pomocí vloženého middlewaru terminálu.</span><span class="sxs-lookup"><span data-stu-id="a560a-799">If an app is capable of responding to requests, obtain request, connection, and additional data from the app using terminal inline middleware.</span></span> <span data-ttu-id="a560a-800">Další informace a ukázka kódu naleznete v tématu <xref:test/troubleshoot#obtain-data-from-an-app> .</span><span class="sxs-lookup"><span data-stu-id="a560a-800">For more information and sample code, see <xref:test/troubleshoot#obtain-data-from-an-app>.</span></span>

### <a name="slow-or-hanging-app-iis"></a><span data-ttu-id="a560a-801">Pomalá nebo zavěšená aplikace (IIS)</span><span class="sxs-lookup"><span data-stu-id="a560a-801">Slow or hanging app (IIS)</span></span>

<span data-ttu-id="a560a-802">*Výpis stavu* systému je snímek paměti systému a může vám pomůže určit příčinu selhání aplikace, selhání při spuštění nebo pomalé aplikace.</span><span class="sxs-lookup"><span data-stu-id="a560a-802">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="a560a-803">Aplikace selže nebo dojde k výjimce.</span><span class="sxs-lookup"><span data-stu-id="a560a-803">App crashes or encounters an exception</span></span>

<span data-ttu-id="a560a-804">Získat a analyzovat výpis z [zasílání zpráv o chybách systému Windows (WER)](/windows/desktop/wer/windows-error-reporting):</span><span class="sxs-lookup"><span data-stu-id="a560a-804">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="a560a-805">Vytvořte složku uchovávající soubory s výpisem stavu systému `c:\dumps` .</span><span class="sxs-lookup"><span data-stu-id="a560a-805">Create a folder to hold crash dump files at `c:\dumps`.</span></span> <span data-ttu-id="a560a-806">Fond aplikací musí mít ke složce přístup pro zápis.</span><span class="sxs-lookup"><span data-stu-id="a560a-806">The app pool must have write access to the folder.</span></span>
1. <span data-ttu-id="a560a-807">Spusťte [skript prostředí PowerShell pro EnableDumps](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1):</span><span class="sxs-lookup"><span data-stu-id="a560a-807">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1):</span></span>
   * <span data-ttu-id="a560a-808">Pokud aplikace používá [model hostování v rámci procesu](xref:host-and-deploy/iis/index#in-process-hosting-model), spusťte skript pro *w3wp.exe*:</span><span class="sxs-lookup"><span data-stu-id="a560a-808">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\EnableDumps w3wp.exe c:\dumps
     ```

   * <span data-ttu-id="a560a-809">Pokud aplikace používá [model hostování mimo proces](xref:host-and-deploy/iis/index#out-of-process-hosting-model), spusťte skript pro *dotnet.exe*:</span><span class="sxs-lookup"><span data-stu-id="a560a-809">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\EnableDumps dotnet.exe c:\dumps
     ```

1. <span data-ttu-id="a560a-810">Spusťte aplikaci za podmínek, které způsobí, že dojde k chybě.</span><span class="sxs-lookup"><span data-stu-id="a560a-810">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="a560a-811">Po chybě spusťte [skript PowerShellu DisableDumps](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1):</span><span class="sxs-lookup"><span data-stu-id="a560a-811">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1):</span></span>
   * <span data-ttu-id="a560a-812">Pokud aplikace používá [model hostování v rámci procesu](xref:host-and-deploy/iis/index#in-process-hosting-model), spusťte skript pro *w3wp.exe*:</span><span class="sxs-lookup"><span data-stu-id="a560a-812">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\DisableDumps w3wp.exe
     ```

   * <span data-ttu-id="a560a-813">Pokud aplikace používá [model hostování mimo proces](xref:host-and-deploy/iis/index#out-of-process-hosting-model), spusťte skript pro *dotnet.exe*:</span><span class="sxs-lookup"><span data-stu-id="a560a-813">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\DisableDumps dotnet.exe
     ```

<span data-ttu-id="a560a-814">Po selhání aplikace a dokončení shromažďování výpisu je možné aplikaci ukončit normálně.</span><span class="sxs-lookup"><span data-stu-id="a560a-814">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="a560a-815">Skript PowerShellu nakonfiguruje WER a shromáždí až pět výpisů paměti pro každou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="a560a-815">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="a560a-816">Výpisy stavu systému můžou zabírat velké množství místa na disku (každý až několik gigabajtů).</span><span class="sxs-lookup"><span data-stu-id="a560a-816">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="a560a-817">Aplikace přestane reagovat, dojde k chybě při spuštění nebo se spustí normálně.</span><span class="sxs-lookup"><span data-stu-id="a560a-817">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="a560a-818">Když *aplikace přestane reagovat (zastaví* se, ale nejedná se o chybu), selže během spuštění nebo se spustí normálně, podívejte [se na soubory výpisu paměti v uživatelském režimu: zvolením nejlepšího](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) nástroje vyberte vhodný nástroj pro vytváření výpisu.</span><span class="sxs-lookup"><span data-stu-id="a560a-818">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="a560a-819">Analýza výpisu paměti</span><span class="sxs-lookup"><span data-stu-id="a560a-819">Analyze the dump</span></span>

<span data-ttu-id="a560a-820">Výpis paměti lze analyzovat pomocí několika přístupů.</span><span class="sxs-lookup"><span data-stu-id="a560a-820">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="a560a-821">Další informace najdete v tématu [Analýza souboru s výpisem stavu v uživatelském režimu](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span><span class="sxs-lookup"><span data-stu-id="a560a-821">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="clear-package-caches"></a><span data-ttu-id="a560a-822">Vymazat mezipaměti balíčků</span><span class="sxs-lookup"><span data-stu-id="a560a-822">Clear package caches</span></span>

<span data-ttu-id="a560a-823">Funkční aplikace může po upgradu .NET Core SDK ve vývojovém počítači nebo změně verzí balíčku v rámci aplikace selhat okamžitě.</span><span class="sxs-lookup"><span data-stu-id="a560a-823">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="a560a-824">V některých případech můžou nesouvislé balíčky při provádění hlavních upgradů poškodit aplikaci.</span><span class="sxs-lookup"><span data-stu-id="a560a-824">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="a560a-825">Většinu těchto problémů můžete vyřešit pomocí následujících pokynů:</span><span class="sxs-lookup"><span data-stu-id="a560a-825">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="a560a-826">Odstraňte složky *bin* a *obj* .</span><span class="sxs-lookup"><span data-stu-id="a560a-826">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="a560a-827">Pomocí příkazu [dotnet All--Clear](/dotnet/core/tools/dotnet-nuget-locals) z příkazového prostředí vymažte mezipaměť balíčku.</span><span class="sxs-lookup"><span data-stu-id="a560a-827">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="a560a-828">Mazání mezipamětí balíčků lze také provést pomocí nástroje [nuget.exe](https://www.nuget.org/downloads) a provedení příkazu `nuget locals all -clear` .</span><span class="sxs-lookup"><span data-stu-id="a560a-828">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="a560a-829">*nuget.exe* není sada odinstalována s desktopovým operačním systémem Windows a je nutné ji získat samostatně z [webu NuGet](https://www.nuget.org/downloads).</span><span class="sxs-lookup"><span data-stu-id="a560a-829">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="a560a-830">Obnovte a znovu sestavte projekt.</span><span class="sxs-lookup"><span data-stu-id="a560a-830">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="a560a-831">Před opětovným nasazením aplikace odstraňte všechny soubory ve složce pro nasazení na serveru.</span><span class="sxs-lookup"><span data-stu-id="a560a-831">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a560a-832">Další zdroje informací</span><span class="sxs-lookup"><span data-stu-id="a560a-832">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:fundamentals/error-handling>
* <xref:host-and-deploy/aspnet-core-module>

### <a name="azure-documentation"></a><span data-ttu-id="a560a-833">Dokumentace k Azure</span><span class="sxs-lookup"><span data-stu-id="a560a-833">Azure documentation</span></span>

* [<span data-ttu-id="a560a-834">Application Insights pro ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a560a-834">Application Insights for ASP.NET Core</span></span>](/azure/application-insights/app-insights-asp-net-core)
* [<span data-ttu-id="a560a-835">Část vzdálené ladění webových aplikací řešení potíží s webovou aplikací v Azure App Service pomocí sady Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a560a-835">Remote debugging web apps section of Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug)
* [<span data-ttu-id="a560a-836">Přehled diagnostiky Azure App Service</span><span class="sxs-lookup"><span data-stu-id="a560a-836">Azure App Service diagnostics overview</span></span>](/azure/app-service/app-service-diagnostics)
* [<span data-ttu-id="a560a-837">Postup: Monitorování aplikací ve službě Azure App Service</span><span class="sxs-lookup"><span data-stu-id="a560a-837">How to: Monitor Apps in Azure App Service</span></span>](/azure/app-service/web-sites-monitor)
* [<span data-ttu-id="a560a-838">Řešení potíží s webovou aplikací v Azure App Service pomocí sady Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a560a-838">Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [<span data-ttu-id="a560a-839">Řešení chyb HTTP z "502 špatné brány" a "nedostupné služby 503" v Azure Web Apps</span><span class="sxs-lookup"><span data-stu-id="a560a-839">Troubleshoot HTTP errors of "502 bad gateway" and "503 service unavailable" in your Azure web apps</span></span>](/azure/app-service/app-service-web-troubleshoot-http-502-http-503)
* [<span data-ttu-id="a560a-840">Řešení potíží s výkonem pomalých webových aplikací ve službě Azure App Service</span><span class="sxs-lookup"><span data-stu-id="a560a-840">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="a560a-841">Nejčastější dotazy týkající se výkonu aplikací pro Web Apps v Azure</span><span class="sxs-lookup"><span data-stu-id="a560a-841">Application performance FAQs for Web Apps in Azure</span></span>](/azure/app-service/app-service-web-availability-performance-application-issues-faq)
* [<span data-ttu-id="a560a-842">Sandbox webové aplikace Azure (omezení spuštění App Service Runtime)</span><span class="sxs-lookup"><span data-stu-id="a560a-842">Azure Web App sandbox (App Service runtime execution limitations)</span></span>](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)
* [<span data-ttu-id="a560a-843">Azure pátek: Azure App Service diagnostické prostředí a řešení potíží (12 minut video)</span><span class="sxs-lookup"><span data-stu-id="a560a-843">Azure Friday: Azure App Service Diagnostic and Troubleshooting Experience (12-minute video)</span></span>](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)

### <a name="visual-studio-documentation"></a><span data-ttu-id="a560a-844">Dokumentace sady Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a560a-844">Visual Studio documentation</span></span>

* [<span data-ttu-id="a560a-845">Vzdálené ladění ASP.NET Core ve službě IIS v Azure v aplikaci Visual Studio 2017</span><span class="sxs-lookup"><span data-stu-id="a560a-845">Remote Debug ASP.NET Core on IIS in Azure in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-azure)
* [<span data-ttu-id="a560a-846">Vzdálené ladění ASP.NET Core na vzdáleném počítači IIS v aplikaci Visual Studio 2017</span><span class="sxs-lookup"><span data-stu-id="a560a-846">Remote Debug ASP.NET Core on a Remote IIS Computer in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-aspnet-on-a-remote-iis-computer)
* [<span data-ttu-id="a560a-847">Informace o ladění pomocí sady Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a560a-847">Learn to debug using Visual Studio</span></span>](/visualstudio/debugger/getting-started-with-the-debugger)

### <a name="visual-studio-code-documentation"></a><span data-ttu-id="a560a-848">Dokumentace k Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a560a-848">Visual Studio Code documentation</span></span>

* [<span data-ttu-id="a560a-849">Ladění pomocí Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a560a-849">Debugging with Visual Studio Code</span></span>](https://code.visualstudio.com/docs/editor/debugging)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="a560a-850">Tento článek poskytuje informace o běžných chybách při spuštění aplikací a pokyny k diagnostice chyb při nasazení aplikace do Azure App Service nebo IIS:</span><span class="sxs-lookup"><span data-stu-id="a560a-850">This article provides information on common app startup errors and instructions on how to diagnose errors when an app is deployed to Azure App Service or IIS:</span></span>

[<span data-ttu-id="a560a-851">Chyby při spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="a560a-851">App startup errors</span></span>](#app-startup-errors)  
<span data-ttu-id="a560a-852">Vysvětluje běžné spouštění scénářů stavového kódu HTTP.</span><span class="sxs-lookup"><span data-stu-id="a560a-852">Explains common startup HTTP status code scenarios.</span></span>

[<span data-ttu-id="a560a-853">Řešení potíží na Azure App Service</span><span class="sxs-lookup"><span data-stu-id="a560a-853">Troubleshoot on Azure App Service</span></span>](#troubleshoot-on-azure-app-service)  
<span data-ttu-id="a560a-854">Poskytuje rady pro řešení potíží pro aplikace nasazené do Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="a560a-854">Provides troubleshooting advice for apps deployed to Azure App Service.</span></span>

[<span data-ttu-id="a560a-855">Odstraňování potíží ve službě IIS</span><span class="sxs-lookup"><span data-stu-id="a560a-855">Troubleshoot on IIS</span></span>](#troubleshoot-on-iis)  
<span data-ttu-id="a560a-856">Poskytuje rady pro řešení potíží pro aplikace nasazené do služby IIS nebo místně spuštěné v IIS Express.</span><span class="sxs-lookup"><span data-stu-id="a560a-856">Provides troubleshooting advice for apps deployed to IIS or running on IIS Express locally.</span></span> <span data-ttu-id="a560a-857">Pokyny platí pro nasazení systémů Windows Server a Desktop v systému Windows.</span><span class="sxs-lookup"><span data-stu-id="a560a-857">The guidance applies to both Windows Server and Windows desktop deployments.</span></span>

[<span data-ttu-id="a560a-858">Vymazat mezipaměti balíčků</span><span class="sxs-lookup"><span data-stu-id="a560a-858">Clear package caches</span></span>](#clear-package-caches)  
<span data-ttu-id="a560a-859">Vysvětluje, co dělat v případě, že při provádění podstatných upgradů nebo změně verze balíčku dojde k přerušení aplikace v nesouvislých balíčcích.</span><span class="sxs-lookup"><span data-stu-id="a560a-859">Explains what to do when incoherent packages break an app when performing major upgrades or changing package versions.</span></span>

[<span data-ttu-id="a560a-860">Další materiály</span><span class="sxs-lookup"><span data-stu-id="a560a-860">Additional resources</span></span>](#additional-resources)  
<span data-ttu-id="a560a-861">Seznam dalších témat pro odstraňování potíží.</span><span class="sxs-lookup"><span data-stu-id="a560a-861">Lists additional troubleshooting topics.</span></span>

## <a name="app-startup-errors"></a><span data-ttu-id="a560a-862">Chyby při spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="a560a-862">App startup errors</span></span>

<span data-ttu-id="a560a-863">V aplikaci Visual Studio je výchozím nastavením projektu ASP.NET Core [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hostování během ladění.</span><span class="sxs-lookup"><span data-stu-id="a560a-863">In Visual Studio, an ASP.NET Core project defaults to [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hosting during debugging.</span></span> <span data-ttu-id="a560a-864">*Selhání procesu 502,5* , ke kterému dochází, když se místně ladění dá diagnostikovat pomocí Rady v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="a560a-864">A *502.5 Process Failure* that occurs when debugging locally can be diagnosed using the advice in this topic.</span></span>

### <a name="40314-forbidden"></a><span data-ttu-id="a560a-865">403,14 zakázané</span><span class="sxs-lookup"><span data-stu-id="a560a-865">403.14 Forbidden</span></span>

<span data-ttu-id="a560a-866">Aplikaci se nepodařilo spustit.</span><span class="sxs-lookup"><span data-stu-id="a560a-866">The app fails to start.</span></span> <span data-ttu-id="a560a-867">Protokoluje se následující chyba:</span><span class="sxs-lookup"><span data-stu-id="a560a-867">The following error is logged:</span></span>

```
The Web server is configured to not list the contents of this directory.
```

<span data-ttu-id="a560a-868">K této chybě obvykle dochází v důsledku poškozeného nasazení v hostitelském systému, který zahrnuje kterýkoli z následujících scénářů:</span><span class="sxs-lookup"><span data-stu-id="a560a-868">The error is usually caused by a broken deployment on the hosting system, which includes any of the following scenarios:</span></span>

* <span data-ttu-id="a560a-869">Aplikace je nasazená do chybné složky v hostitelském systému.</span><span class="sxs-lookup"><span data-stu-id="a560a-869">The app is deployed to the wrong folder on the hosting system.</span></span>
* <span data-ttu-id="a560a-870">Procesu nasazení se nepodařilo přesunout všechny soubory a složky aplikace do složky pro nasazení v hostitelském systému.</span><span class="sxs-lookup"><span data-stu-id="a560a-870">The deployment process failed to move all of the app's files and folders to the deployment folder on the hosting system.</span></span>
* <span data-ttu-id="a560a-871">V nasazení chybí soubor *web.config* , nebo je obsah souboru *web.config* poškozený.</span><span class="sxs-lookup"><span data-stu-id="a560a-871">The *web.config* file is missing from the deployment, or the *web.config* file contents are malformed.</span></span>

<span data-ttu-id="a560a-872">Proveďte tyto kroky:</span><span class="sxs-lookup"><span data-stu-id="a560a-872">Perform the following steps:</span></span>

1. <span data-ttu-id="a560a-873">Odstraňte všechny soubory a složky ze složky pro nasazení v hostitelském systému.</span><span class="sxs-lookup"><span data-stu-id="a560a-873">Delete all of the files and folders from the deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="a560a-874">Znovu nasaďte obsah složky pro *publikování* aplikace do hostitelského systému pomocí běžné metody nasazení, jako je například Visual Studio, PowerShell nebo ruční nasazení:</span><span class="sxs-lookup"><span data-stu-id="a560a-874">Redeploy the contents of the app's *publish* folder to the hosting system using your normal method of deployment, such as Visual Studio, PowerShell, or manual deployment:</span></span>
   * <span data-ttu-id="a560a-875">Potvrďte, že se soubor *web.config* v nasazení nachází a že jeho obsah je správný.</span><span class="sxs-lookup"><span data-stu-id="a560a-875">Confirm that the *web.config* file is present in the deployment and that its contents are correct.</span></span>
   * <span data-ttu-id="a560a-876">Při hostování na Azure App Service potvrďte, že je aplikace nasazená do `D:\home\site\wwwroot` složky.</span><span class="sxs-lookup"><span data-stu-id="a560a-876">When hosting on Azure App Service, confirm that the app is deployed to the `D:\home\site\wwwroot` folder.</span></span>
   * <span data-ttu-id="a560a-877">Když je aplikace hostovaná službou IIS, zkontrolujte, že je aplikace nasazená na **fyzickou cestu** služby IIS, která se zobrazuje v **základním nastavení** **Správce služby IIS**.</span><span class="sxs-lookup"><span data-stu-id="a560a-877">When the app is hosted by IIS, confirm that the app is deployed to the IIS **Physical path** shown in **IIS Manager**'s **Basic Settings**.</span></span>
1. <span data-ttu-id="a560a-878">Ověřte, zda jsou všechny soubory a složky aplikace nasazeny porovnáním nasazení v hostitelském systému s obsahem složky *publikování* projektu.</span><span class="sxs-lookup"><span data-stu-id="a560a-878">Confirm that all of the app's files and folders are deployed by comparing the deployment on the hosting system to the contents of the project's *publish* folder.</span></span>

<span data-ttu-id="a560a-879">Další informace o rozložení publikované aplikace ASP.NET Core najdete v tématu <xref:host-and-deploy/directory-structure> .</span><span class="sxs-lookup"><span data-stu-id="a560a-879">For more information on the layout of a published ASP.NET Core app, see <xref:host-and-deploy/directory-structure>.</span></span> <span data-ttu-id="a560a-880">Další informace o souboru *web.config* naleznete v tématu <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig> .</span><span class="sxs-lookup"><span data-stu-id="a560a-880">For more information on the *web.config* file, see <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>.</span></span>

### <a name="500-internal-server-error"></a><span data-ttu-id="a560a-881">500 – Vnitřní chyba serveru</span><span class="sxs-lookup"><span data-stu-id="a560a-881">500 Internal Server Error</span></span>

<span data-ttu-id="a560a-882">Aplikace se spustí, ale chyba brání serveru v splnění žádosti.</span><span class="sxs-lookup"><span data-stu-id="a560a-882">The app starts, but an error prevents the server from fulfilling the request.</span></span>

<span data-ttu-id="a560a-883">K této chybě dochází v kódu aplikace během spuštění nebo při vytváření odpovědi.</span><span class="sxs-lookup"><span data-stu-id="a560a-883">This error occurs within the app's code during startup or while creating a response.</span></span> <span data-ttu-id="a560a-884">Odpověď možná neobsahuje žádný obsah, nebo se odpověď může zobrazit jako *500 interní chyba serveru* v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="a560a-884">The response may contain no content, or the response may appear as a *500 Internal Server Error* in the browser.</span></span> <span data-ttu-id="a560a-885">Protokol událostí aplikace obvykle uvádí, že aplikace začala normálně.</span><span class="sxs-lookup"><span data-stu-id="a560a-885">The Application Event Log usually states that the app started normally.</span></span> <span data-ttu-id="a560a-886">Z hlediska serveru je to správné.</span><span class="sxs-lookup"><span data-stu-id="a560a-886">From the server's perspective, that's correct.</span></span> <span data-ttu-id="a560a-887">Aplikace se spustila, ale nemůže vygenerovat platnou odpověď.</span><span class="sxs-lookup"><span data-stu-id="a560a-887">The app did start, but it can't generate a valid response.</span></span> <span data-ttu-id="a560a-888">Spusťte aplikaci na příkazovém řádku na serveru nebo povolte řešení potíží pomocí protokolu stdout modulu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a560a-888">Run the app at a command prompt on the server or enable the ASP.NET Core Module stdout log to troubleshoot the problem.</span></span>

### <a name="5025-process-failure"></a><span data-ttu-id="a560a-889">502.5 – Selhání procesu</span><span class="sxs-lookup"><span data-stu-id="a560a-889">502.5 Process Failure</span></span>

<span data-ttu-id="a560a-890">Pracovní proces se nezdařil.</span><span class="sxs-lookup"><span data-stu-id="a560a-890">The worker process fails.</span></span> <span data-ttu-id="a560a-891">Aplikace se nespustí.</span><span class="sxs-lookup"><span data-stu-id="a560a-891">The app doesn't start.</span></span>

<span data-ttu-id="a560a-892">[Modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module) se pokusí spustit pracovní proces, ale jeho spuštění se nezdařilo.</span><span class="sxs-lookup"><span data-stu-id="a560a-892">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the worker process but it fails to start.</span></span> <span data-ttu-id="a560a-893">Příčinou selhání spuštění procesu může být obvykle určení z položek protokolu událostí aplikace a protokolu stdout modulu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a560a-893">The cause of a process startup failure can usually be determined from entries in the Application Event Log and the ASP.NET Core Module stdout log.</span></span>

<span data-ttu-id="a560a-894">Obvyklou chybovou podmínkou je, že aplikace je nesprávně nakonfigurovaná kvůli cílení na verzi ASP.NET Core sdílené rozhraní, které není k dispozici.</span><span class="sxs-lookup"><span data-stu-id="a560a-894">A common failure condition is the app is misconfigured due to targeting a version of the ASP.NET Core shared framework that isn't present.</span></span> <span data-ttu-id="a560a-895">Ověřte, které verze ASP.NET Core sdílené rozhraní jsou nainstalovány v cílovém počítači.</span><span class="sxs-lookup"><span data-stu-id="a560a-895">Check which versions of the ASP.NET Core shared framework are installed on the target machine.</span></span> <span data-ttu-id="a560a-896">*Sdílené rozhraní* je sada sestavení (soubory *. dll* ), které jsou nainstalovány na počítači a na které odkazuje Metapackage jako `Microsoft.AspNetCore.App` .</span><span class="sxs-lookup"><span data-stu-id="a560a-896">The *shared framework* is the set of assemblies (*.dll* files) that are installed on the machine and referenced by a metapackage such as `Microsoft.AspNetCore.App`.</span></span> <span data-ttu-id="a560a-897">Odkaz Metapackage může určovat minimální požadovanou verzi.</span><span class="sxs-lookup"><span data-stu-id="a560a-897">The metapackage reference can specify a minimum required version.</span></span> <span data-ttu-id="a560a-898">Další informace najdete v tématu [sdílené rozhraní](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span><span class="sxs-lookup"><span data-stu-id="a560a-898">For more information, see [The shared framework](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span></span>

<span data-ttu-id="a560a-899">Chybová stránka *selhání procesu 502,5* se vrátí v případě, že při selhání hostování nebo aplikace dojde k chybě pracovního procesu:</span><span class="sxs-lookup"><span data-stu-id="a560a-899">The *502.5 Process Failure* error page is returned when a hosting or app misconfiguration causes the worker process to fail:</span></span>

### <a name="failed-to-start-application-errorcode-0x800700c1"></a><span data-ttu-id="a560a-900">Nepovedlo se spustit aplikaci (ErrorCode ' 0x800700C1 ')</span><span class="sxs-lookup"><span data-stu-id="a560a-900">Failed to start application (ErrorCode '0x800700c1')</span></span>

```
EventID: 1010
Source: IIS AspNetCore Module V2
Failed to start application '/LM/W3SVC/6/ROOT/', ErrorCode '0x800700c1'.
```

<span data-ttu-id="a560a-901">Aplikaci se nepovedlo spustit, protože se nepovedlo načíst sestavení aplikace (*. dll*).</span><span class="sxs-lookup"><span data-stu-id="a560a-901">The app failed to start because the app's assembly (*.dll*) couldn't be loaded.</span></span>

<span data-ttu-id="a560a-902">K této chybě dochází, pokud dojde k neshodě bitová verze mezi publikovanou aplikací a procesem W3wp/IISExpress.</span><span class="sxs-lookup"><span data-stu-id="a560a-902">This error occurs when there's a bitness mismatch between the published app and the w3wp/iisexpress process.</span></span>

<span data-ttu-id="a560a-903">Zkontrolujte, jestli je správně nastavené nastavení 32 fondu aplikací:</span><span class="sxs-lookup"><span data-stu-id="a560a-903">Confirm that the app pool's 32-bit setting is correct:</span></span>

1. <span data-ttu-id="a560a-904">Vyberte fond aplikací v **fondech aplikací**Správce služby IIS.</span><span class="sxs-lookup"><span data-stu-id="a560a-904">Select the app pool in IIS Manager's **Application Pools**.</span></span>
1. <span data-ttu-id="a560a-905">V části **Upravit fond aplikací** na panelu **Akce** vyberte **Upřesnit nastavení** .</span><span class="sxs-lookup"><span data-stu-id="a560a-905">Select **Advanced Settings** under **Edit Application Pool** in the **Actions** panel.</span></span>
1. <span data-ttu-id="a560a-906">Nastavte **možnost povolit 32 – bitové aplikace**:</span><span class="sxs-lookup"><span data-stu-id="a560a-906">Set **Enable 32-Bit Applications**:</span></span>
   * <span data-ttu-id="a560a-907">Pokud nasazujete 32 (x86) aplikaci, nastavte hodnotu na `True` .</span><span class="sxs-lookup"><span data-stu-id="a560a-907">If deploying a 32-bit (x86) app, set the value to `True`.</span></span>
   * <span data-ttu-id="a560a-908">Pokud nasazujete 64 aplikaci (x64), nastavte hodnotu na `False` .</span><span class="sxs-lookup"><span data-stu-id="a560a-908">If deploying a 64-bit (x64) app, set the value to `False`.</span></span>

<span data-ttu-id="a560a-909">Ověřte, že mezi `<Platform>` vlastností MSBuild v souboru projektu a publikovaným bitová verzeem aplikace nedochází ke konfliktu.</span><span class="sxs-lookup"><span data-stu-id="a560a-909">Confirm that there isn't a conflict between a `<Platform>` MSBuild property in the project file and the published bitness of the app.</span></span>

### <a name="connection-reset"></a><span data-ttu-id="a560a-910">Resetování připojení</span><span class="sxs-lookup"><span data-stu-id="a560a-910">Connection reset</span></span>

<span data-ttu-id="a560a-911">Pokud dojde k chybě po odeslání hlaviček, je příliš pozdě pro server, který odešle **500 interní chybu serveru** , když dojde k chybě.</span><span class="sxs-lookup"><span data-stu-id="a560a-911">If an error occurs after the headers are sent, it's too late for the server to send a **500 Internal Server Error** when an error occurs.</span></span> <span data-ttu-id="a560a-912">K tomu často dochází, když dojde k chybě během serializace složitých objektů pro odpověď.</span><span class="sxs-lookup"><span data-stu-id="a560a-912">This often happens when an error occurs during the serialization of complex objects for a response.</span></span> <span data-ttu-id="a560a-913">Tento typ chyby se zobrazí jako chyba *resetování připojení* na klientovi.</span><span class="sxs-lookup"><span data-stu-id="a560a-913">This type of error appears as a *connection reset* error on the client.</span></span> <span data-ttu-id="a560a-914">[Protokolování aplikace](xref:fundamentals/logging/index) může pomoct řešit tyto typy chyb.</span><span class="sxs-lookup"><span data-stu-id="a560a-914">[Application logging](xref:fundamentals/logging/index) can help troubleshoot these types of errors.</span></span>

### <a name="default-startup-limits"></a><span data-ttu-id="a560a-915">Výchozí omezení spouštění</span><span class="sxs-lookup"><span data-stu-id="a560a-915">Default startup limits</span></span>

<span data-ttu-id="a560a-916">[ASP.NET Core modul](xref:host-and-deploy/aspnet-core-module) je nakonfigurovaný s výchozí *startupTimeLimitou* 120 sekund.</span><span class="sxs-lookup"><span data-stu-id="a560a-916">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) is configured with a default *startupTimeLimit* of 120 seconds.</span></span> <span data-ttu-id="a560a-917">Pokud necháte výchozí hodnotu, aplikace může trvat až dvě minuty, než se modul zaznamená selhání procesu.</span><span class="sxs-lookup"><span data-stu-id="a560a-917">When left at the default value, an app may take up to two minutes to start before the module logs a process failure.</span></span> <span data-ttu-id="a560a-918">Informace o konfiguraci modulu naleznete v tématu [atributy elementu aspNetCore](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span><span class="sxs-lookup"><span data-stu-id="a560a-918">For information on configuring the module, see [Attributes of the aspNetCore element](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span></span>

## <a name="troubleshoot-on-azure-app-service"></a><span data-ttu-id="a560a-919">Řešení potíží na Azure App Service</span><span class="sxs-lookup"><span data-stu-id="a560a-919">Troubleshoot on Azure App Service</span></span>

[!INCLUDE [Azure App Service Preview Notice](~/includes/azure-apps-preview-notice.md)]

### <a name="application-event-log-azure-app-service"></a><span data-ttu-id="a560a-920">Protokol událostí aplikace (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="a560a-920">Application Event Log (Azure App Service)</span></span>

<span data-ttu-id="a560a-921">Chcete-li získat přístup k protokolu událostí aplikace, použijte okno **Diagnostika a řešení problémů** v Azure Portal:</span><span class="sxs-lookup"><span data-stu-id="a560a-921">To access the Application Event Log, use the **Diagnose and solve problems** blade in the Azure portal:</span></span>

1. <span data-ttu-id="a560a-922">V Azure Portal otevřete aplikaci v **App Services**.</span><span class="sxs-lookup"><span data-stu-id="a560a-922">In the Azure portal, open the app in **App Services**.</span></span>
1. <span data-ttu-id="a560a-923">Vyberte **Diagnostikovat a řešit problémy**.</span><span class="sxs-lookup"><span data-stu-id="a560a-923">Select **Diagnose and solve problems**.</span></span>
1. <span data-ttu-id="a560a-924">Vyberte **diagnostické nástroje** záhlaví.</span><span class="sxs-lookup"><span data-stu-id="a560a-924">Select the **Diagnostic Tools** heading.</span></span>
1. <span data-ttu-id="a560a-925">V nabídce **nástroje podpory**vyberte tlačítko **události aplikace** .</span><span class="sxs-lookup"><span data-stu-id="a560a-925">Under **Support Tools**, select the **Application Events** button.</span></span>
1. <span data-ttu-id="a560a-926">Projděte si nejnovější chybu, kterou poskytla položka *IIS AspNetCoreModule* nebo *IIS AspNetCoreModule v2* ve **zdrojovém** sloupci.</span><span class="sxs-lookup"><span data-stu-id="a560a-926">Examine the latest error provided by the *IIS AspNetCoreModule* or *IIS AspNetCoreModule V2* entry in the **Source** column.</span></span>

<span data-ttu-id="a560a-927">Alternativou k použití okna **diagnostikovat a řešit problémy** je kontrola souboru protokolu událostí aplikace přímo pomocí [Kudu](https://github.com/projectkudu/kudu/wiki):</span><span class="sxs-lookup"><span data-stu-id="a560a-927">An alternative to using the **Diagnose and solve problems** blade is to examine the Application Event Log file directly using [Kudu](https://github.com/projectkudu/kudu/wiki):</span></span>

1. <span data-ttu-id="a560a-928">Otevřete **Rozšířené nástroje** v oblasti **vývojové nástroje** .</span><span class="sxs-lookup"><span data-stu-id="a560a-928">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="a560a-929">Vyberte tlačítko \*\*Přejít &rarr; \*\* .</span><span class="sxs-lookup"><span data-stu-id="a560a-929">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="a560a-930">Konzola Kudu se otevře v novém okně nebo záložce prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="a560a-930">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="a560a-931">Pomocí navigačního panelu v horní části stránky otevřete **konzolu ladění** a vyberte **cmd**.</span><span class="sxs-lookup"><span data-stu-id="a560a-931">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="a560a-932">Otevřete složku **soubory protokolů** .</span><span class="sxs-lookup"><span data-stu-id="a560a-932">Open the **LogFiles** folder.</span></span>
1. <span data-ttu-id="a560a-933">Vyberte ikonu tužky vedle *eventlog.xml* souboru.</span><span class="sxs-lookup"><span data-stu-id="a560a-933">Select the pencil icon next to the *eventlog.xml* file.</span></span>
1. <span data-ttu-id="a560a-934">Projděte si protokol.</span><span class="sxs-lookup"><span data-stu-id="a560a-934">Examine the log.</span></span> <span data-ttu-id="a560a-935">Posuňte se do dolní části protokolu, abyste viděli nejaktuálnější události.</span><span class="sxs-lookup"><span data-stu-id="a560a-935">Scroll to the bottom of the log to see the most recent events.</span></span>

### <a name="run-the-app-in-the-kudu-console"></a><span data-ttu-id="a560a-936">Spuštění aplikace v konzole Kudu</span><span class="sxs-lookup"><span data-stu-id="a560a-936">Run the app in the Kudu console</span></span>

<span data-ttu-id="a560a-937">Mnoho chyb při spuštění nevytváří užitečné informace v protokolu událostí aplikace.</span><span class="sxs-lookup"><span data-stu-id="a560a-937">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="a560a-938">Tuto chybu můžete zjistit spuštěním aplikace v konzole vzdáleného spuštění [Kudu](https://github.com/projectkudu/kudu/wiki) :</span><span class="sxs-lookup"><span data-stu-id="a560a-938">You can run the app in the [Kudu](https://github.com/projectkudu/kudu/wiki) Remote Execution Console to discover the error:</span></span>

1. <span data-ttu-id="a560a-939">Otevřete **Rozšířené nástroje** v oblasti **vývojové nástroje** .</span><span class="sxs-lookup"><span data-stu-id="a560a-939">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="a560a-940">Vyberte tlačítko \*\*Přejít &rarr; \*\* .</span><span class="sxs-lookup"><span data-stu-id="a560a-940">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="a560a-941">Konzola Kudu se otevře v novém okně nebo záložce prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="a560a-941">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="a560a-942">Pomocí navigačního panelu v horní části stránky otevřete **konzolu ladění** a vyberte **cmd**.</span><span class="sxs-lookup"><span data-stu-id="a560a-942">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>

#### <a name="test-a-32-bit-x86-app"></a><span data-ttu-id="a560a-943">Testování 32 (x86) aplikace</span><span class="sxs-lookup"><span data-stu-id="a560a-943">Test a 32-bit (x86) app</span></span>

<span data-ttu-id="a560a-944">**Aktuální verze**</span><span class="sxs-lookup"><span data-stu-id="a560a-944">**Current release**</span></span>

1. `cd d:\home\site\wwwroot`
1. <span data-ttu-id="a560a-945">Spusťte aplikaci: </span><span class="sxs-lookup"><span data-stu-id="a560a-945">Run the app:</span></span>
   * <span data-ttu-id="a560a-946">Pokud aplikace je [nasazení závislé na rozhraní](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span><span class="sxs-lookup"><span data-stu-id="a560a-946">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

     ```dotnetcli
     dotnet .\{ASSEMBLY NAME}.dll
     ```

   * <span data-ttu-id="a560a-947">Pokud je aplikace samostatná, [nasazení](/dotnet/core/deploying/#self-contained-deployments-scd):</span><span class="sxs-lookup"><span data-stu-id="a560a-947">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

     ```console
     {ASSEMBLY NAME}.exe
     ```

<span data-ttu-id="a560a-948">Výstup konzoly z aplikace, v němž se zobrazují všechny chyby, je předán do konzoly Kudu.</span><span class="sxs-lookup"><span data-stu-id="a560a-948">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="a560a-949">**Nasazení závislé na architektuře spuštěné ve verzi Preview**</span><span class="sxs-lookup"><span data-stu-id="a560a-949">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="a560a-950">*Vyžaduje instalaci rozšíření webu ASP.NET Core {VERSION} (x86) Runtime.*</span><span class="sxs-lookup"><span data-stu-id="a560a-950">*Requires installing the ASP.NET Core {VERSION} (x86) Runtime site extension.*</span></span>

1. <span data-ttu-id="a560a-951">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32` ( `{X.Y}` je verze modulu runtime)</span><span class="sxs-lookup"><span data-stu-id="a560a-951">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="a560a-952">Spusťte aplikaci: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="a560a-952">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="a560a-953">Výstup konzoly z aplikace, v němž se zobrazují všechny chyby, je předán do konzoly Kudu.</span><span class="sxs-lookup"><span data-stu-id="a560a-953">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

#### <a name="test-a-64-bit-x64-app"></a><span data-ttu-id="a560a-954">Testování 64 aplikace (x64)</span><span class="sxs-lookup"><span data-stu-id="a560a-954">Test a 64-bit (x64) app</span></span>

<span data-ttu-id="a560a-955">**Aktuální verze**</span><span class="sxs-lookup"><span data-stu-id="a560a-955">**Current release**</span></span>

* <span data-ttu-id="a560a-956">Pokud je aplikace [nasazením závislého na rozhraní](/dotnet/core/deploying/#framework-dependent-deployments-fdd)64 (x64):</span><span class="sxs-lookup"><span data-stu-id="a560a-956">If the app is a 64-bit (x64) [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>
  1. `cd D:\Program Files\dotnet`
  1. <span data-ttu-id="a560a-957">Spusťte aplikaci: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="a560a-957">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>
* <span data-ttu-id="a560a-958">Pokud je aplikace samostatná, [nasazení](/dotnet/core/deploying/#self-contained-deployments-scd):</span><span class="sxs-lookup"><span data-stu-id="a560a-958">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>
  1. `cd D:\home\site\wwwroot`
  1. <span data-ttu-id="a560a-959">Spusťte aplikaci: `{ASSEMBLY NAME}.exe`</span><span class="sxs-lookup"><span data-stu-id="a560a-959">Run the app: `{ASSEMBLY NAME}.exe`</span></span>

<span data-ttu-id="a560a-960">Výstup konzoly z aplikace, v němž se zobrazují všechny chyby, je předán do konzoly Kudu.</span><span class="sxs-lookup"><span data-stu-id="a560a-960">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="a560a-961">**Nasazení závislé na architektuře spuštěné ve verzi Preview**</span><span class="sxs-lookup"><span data-stu-id="a560a-961">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="a560a-962">*Vyžaduje instalaci rozšíření webu ASP.NET Core {VERSION} (x64) Runtime.*</span><span class="sxs-lookup"><span data-stu-id="a560a-962">*Requires installing the ASP.NET Core {VERSION} (x64) Runtime site extension.*</span></span>

1. <span data-ttu-id="a560a-963">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64` ( `{X.Y}` je verze modulu runtime)</span><span class="sxs-lookup"><span data-stu-id="a560a-963">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="a560a-964">Spusťte aplikaci: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="a560a-964">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="a560a-965">Výstup konzoly z aplikace, v němž se zobrazují všechny chyby, je předán do konzoly Kudu.</span><span class="sxs-lookup"><span data-stu-id="a560a-965">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

### <a name="aspnet-core-module-stdout-log-azure-app-service"></a><span data-ttu-id="a560a-966">Protokol stdout v modulu ASP.NET Core (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="a560a-966">ASP.NET Core Module stdout log (Azure App Service)</span></span>

<span data-ttu-id="a560a-967">Protokol stdout modulu ASP.NET Coree často zaznamenává užitečné chybové zprávy, které se nenašly v protokolu událostí aplikace.</span><span class="sxs-lookup"><span data-stu-id="a560a-967">The ASP.NET Core Module stdout log often records useful error messages not found in the Application Event Log.</span></span> <span data-ttu-id="a560a-968">Postup povolení a zobrazení protokolů stdout:</span><span class="sxs-lookup"><span data-stu-id="a560a-968">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="a560a-969">Přejděte do okna **diagnostikovat a řešit problémy** v Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="a560a-969">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="a560a-970">V části **Vybrat kategorii problému**vyberte tlačítko pro **webovou aplikaci** .</span><span class="sxs-lookup"><span data-stu-id="a560a-970">Under **SELECT PROBLEM CATEGORY**, select the **Web App Down** button.</span></span>
1. <span data-ttu-id="a560a-971">V části **navrhovaná řešení** > **povolte přesměrování protokolu stdout**a výběrem tlačítka **otevřete konzolu Kudu a upravte Web.Config**.</span><span class="sxs-lookup"><span data-stu-id="a560a-971">Under **Suggested Solutions** > **Enable Stdout Log Redirection**, select the button to **Open Kudu Console to edit Web.Config**.</span></span>
1. <span data-ttu-id="a560a-972">V konzole pro **diagnostiku**Kudu otevřete složky v lokalitě s cestou **site**  >  **wwwroot**.</span><span class="sxs-lookup"><span data-stu-id="a560a-972">In the Kudu **Diagnostic Console**, open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="a560a-973">Posuňte se dolů, aby se soubor *web.config* v dolní části seznamu.</span><span class="sxs-lookup"><span data-stu-id="a560a-973">Scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="a560a-974">Klikněte na ikonu tužky vedle *web.config* souboru.</span><span class="sxs-lookup"><span data-stu-id="a560a-974">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="a560a-975">Nastavte **stdoutLogEnabled** na `true` a změňte cestu **stdoutLogFile** na: `\\?\%home%\LogFiles\stdout` .</span><span class="sxs-lookup"><span data-stu-id="a560a-975">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="a560a-976">Výběrem **Uložit** Uložte aktualizovaný soubor *web.config* .</span><span class="sxs-lookup"><span data-stu-id="a560a-976">Select **Save** to save the updated *web.config* file.</span></span>
1. <span data-ttu-id="a560a-977">Vytvořte žádost do aplikace.</span><span class="sxs-lookup"><span data-stu-id="a560a-977">Make a request to the app.</span></span>
1. <span data-ttu-id="a560a-978">Vraťte se na Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="a560a-978">Return to the Azure portal.</span></span> <span data-ttu-id="a560a-979">V oblasti **vývojové nástroje** vyberte okno **Pokročilé nástroje** .</span><span class="sxs-lookup"><span data-stu-id="a560a-979">Select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="a560a-980">Vyberte tlačítko \*\*Přejít &rarr; \*\* .</span><span class="sxs-lookup"><span data-stu-id="a560a-980">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="a560a-981">Konzola Kudu se otevře v novém okně nebo záložce prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="a560a-981">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="a560a-982">Pomocí navigačního panelu v horní části stránky otevřete **konzolu ladění** a vyberte **cmd**.</span><span class="sxs-lookup"><span data-stu-id="a560a-982">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="a560a-983">Vyberte složku **soubory protokolů** .</span><span class="sxs-lookup"><span data-stu-id="a560a-983">Select the **LogFiles** folder.</span></span>
1. <span data-ttu-id="a560a-984">Zkontrolujte **upravený** sloupec a vyberte ikonu tužky a upravte protokol stdout s nejnovějším datem úpravy.</span><span class="sxs-lookup"><span data-stu-id="a560a-984">Inspect the **Modified** column and select the pencil icon to edit the stdout log with the latest modification date.</span></span>
1. <span data-ttu-id="a560a-985">Po otevření souboru protokolu se zobrazí chyba.</span><span class="sxs-lookup"><span data-stu-id="a560a-985">When the log file opens, the error is displayed.</span></span>

<span data-ttu-id="a560a-986">Zakázat protokolování stdout při odstraňování potíží:</span><span class="sxs-lookup"><span data-stu-id="a560a-986">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="a560a-987">V **diagnostické konzole**Kudu se vraťte do umístění **site**  >  **wwwroot** lokality a odhalte tak soubor *web.config* .</span><span class="sxs-lookup"><span data-stu-id="a560a-987">In the Kudu **Diagnostic Console**, return to the path **site** > **wwwroot** to reveal the *web.config* file.</span></span> <span data-ttu-id="a560a-988">Znovu otevřete soubor **web.config** tím, že vyberete ikonu tužky.</span><span class="sxs-lookup"><span data-stu-id="a560a-988">Open the **web.config** file again by selecting the pencil icon.</span></span>
1. <span data-ttu-id="a560a-989">Nastavte **stdoutLogEnabled** na `false` .</span><span class="sxs-lookup"><span data-stu-id="a560a-989">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="a560a-990">Vyberte **Uložit** a soubor uložte.</span><span class="sxs-lookup"><span data-stu-id="a560a-990">Select **Save** to save the file.</span></span>

<span data-ttu-id="a560a-991">Další informace naleznete v tématu <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span><span class="sxs-lookup"><span data-stu-id="a560a-991">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

> [!WARNING]
> <span data-ttu-id="a560a-992">Nepovedlo se zakázat protokol stdout, což může způsobit selhání aplikace nebo serveru.</span><span class="sxs-lookup"><span data-stu-id="a560a-992">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="a560a-993">Velikost souboru protokolu ani počet vytvořených souborů protokolu nijak neomezuje.</span><span class="sxs-lookup"><span data-stu-id="a560a-993">There's no limit on log file size or the number of log files created.</span></span> <span data-ttu-id="a560a-994">K řešení problémů se spouštěním aplikací použijte pouze protokolování STDOUT.</span><span class="sxs-lookup"><span data-stu-id="a560a-994">Only use stdout logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="a560a-995">Pro obecné protokolování v aplikaci ASP.NET Core po spuštění použijte knihovnu protokolování, která omezuje velikost souboru protokolu a otočí protokoly.</span><span class="sxs-lookup"><span data-stu-id="a560a-995">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="a560a-996">Další informace najdete v tématu [Zprostředkovatelé protokolování třetích stran](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="a560a-996">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="slow-or-hanging-app-azure-app-service"></a><span data-ttu-id="a560a-997">Pomalá nebo zavěšená aplikace (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="a560a-997">Slow or hanging app (Azure App Service)</span></span>

<span data-ttu-id="a560a-998">Pokud aplikace reaguje pomalu nebo přestane reagovat na žádost, přečtěte si následující články:</span><span class="sxs-lookup"><span data-stu-id="a560a-998">When an app responds slowly or hangs on a request, see the following articles:</span></span>

* [<span data-ttu-id="a560a-999">Řešení potíží s výkonem pomalých webových aplikací ve službě Azure App Service</span><span class="sxs-lookup"><span data-stu-id="a560a-999">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="a560a-1000">K zachycení výpisu problémů s přerušovanou výjimkou nebo problémy s výkonem ve webové aplikaci Azure použijte rozšíření pro diagnostiku zhroucení serveru.</span><span class="sxs-lookup"><span data-stu-id="a560a-1000">Use Crash Diagnoser Site Extension to Capture Dump for Intermittent Exception issues or performance issues on Azure Web App</span></span>](https://blogs.msdn.microsoft.com/asiatech/2015/12/28/use-crash-diagnoser-site-extension-to-capture-dump-for-intermittent-exception-issues-or-performance-issues-on-azure-web-app/)

### <a name="monitoring-blades"></a><span data-ttu-id="a560a-1001">Okna monitorování</span><span class="sxs-lookup"><span data-stu-id="a560a-1001">Monitoring blades</span></span>

<span data-ttu-id="a560a-1002">Okna monitorování poskytují alternativní možnosti řešení potíží pro metody popsané dříve v tématu.</span><span class="sxs-lookup"><span data-stu-id="a560a-1002">Monitoring blades provide an alternative troubleshooting experience to the methods described earlier in the topic.</span></span> <span data-ttu-id="a560a-1003">Pomocí těchto oken můžete diagnostikovat chyby 500-Series.</span><span class="sxs-lookup"><span data-stu-id="a560a-1003">These blades can be used to diagnose 500-series errors.</span></span>

<span data-ttu-id="a560a-1004">Potvrďte, že jsou nainstalovaná rozšíření ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a560a-1004">Confirm that the ASP.NET Core Extensions are installed.</span></span> <span data-ttu-id="a560a-1005">Pokud nejsou rozšíření nainstalována, nainstalujte je ručně:</span><span class="sxs-lookup"><span data-stu-id="a560a-1005">If the extensions aren't installed, install them manually:</span></span>

1. <span data-ttu-id="a560a-1006">V části okno **vývojové nástroje** vyberte okno **rozšíření** .</span><span class="sxs-lookup"><span data-stu-id="a560a-1006">In the **DEVELOPMENT TOOLS** blade section, select the **Extensions** blade.</span></span>
1. <span data-ttu-id="a560a-1007">V seznamu by se měla zobrazit **rozšíření ASP.NET Core** .</span><span class="sxs-lookup"><span data-stu-id="a560a-1007">The **ASP.NET Core Extensions** should appear in the list.</span></span>
1. <span data-ttu-id="a560a-1008">Pokud nejsou rozšíření nainstalovaná, vyberte tlačítko **Přidat** .</span><span class="sxs-lookup"><span data-stu-id="a560a-1008">If the extensions aren't installed, select the **Add** button.</span></span>
1. <span data-ttu-id="a560a-1009">Ze seznamu vyberte **rozšíření ASP.NET Core** .</span><span class="sxs-lookup"><span data-stu-id="a560a-1009">Choose the **ASP.NET Core Extensions** from the list.</span></span>
1. <span data-ttu-id="a560a-1010">Kliknutím na **OK** přijměte právní podmínky.</span><span class="sxs-lookup"><span data-stu-id="a560a-1010">Select **OK** to accept the legal terms.</span></span>
1. <span data-ttu-id="a560a-1011">V okně **Přidat rozšíření** vyberte **OK** .</span><span class="sxs-lookup"><span data-stu-id="a560a-1011">Select **OK** on the **Add extension** blade.</span></span>
1. <span data-ttu-id="a560a-1012">Informační automaticky otevíraná zpráva indikuje, že se rozšíření úspěšně nainstalovala.</span><span class="sxs-lookup"><span data-stu-id="a560a-1012">An informational pop-up message indicates when the extensions are successfully installed.</span></span>

<span data-ttu-id="a560a-1013">Pokud není povoleno protokolování stdout, postupujte následovně:</span><span class="sxs-lookup"><span data-stu-id="a560a-1013">If stdout logging isn't enabled, follow these steps:</span></span>

1. <span data-ttu-id="a560a-1014">V Azure Portal v oblasti **vývojové nástroje** vyberte okno **Pokročilé nástroje** .</span><span class="sxs-lookup"><span data-stu-id="a560a-1014">In the Azure portal, select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="a560a-1015">Vyberte tlačítko \*\*Přejít &rarr; \*\* .</span><span class="sxs-lookup"><span data-stu-id="a560a-1015">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="a560a-1016">Konzola Kudu se otevře v novém okně nebo záložce prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="a560a-1016">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="a560a-1017">Pomocí navigačního panelu v horní části stránky otevřete **konzolu ladění** a vyberte **cmd**.</span><span class="sxs-lookup"><span data-stu-id="a560a-1017">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="a560a-1018">Otevřete složky **pro cestu** > **wwwroot** a posuňte se dolů, aby se soubor *web.config* v dolní části seznamu.</span><span class="sxs-lookup"><span data-stu-id="a560a-1018">Open the folders to the path **site** > **wwwroot** and scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="a560a-1019">Klikněte na ikonu tužky vedle *web.config* souboru.</span><span class="sxs-lookup"><span data-stu-id="a560a-1019">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="a560a-1020">Nastavte **stdoutLogEnabled** na `true` a změňte cestu **stdoutLogFile** na: `\\?\%home%\LogFiles\stdout` .</span><span class="sxs-lookup"><span data-stu-id="a560a-1020">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="a560a-1021">Výběrem **Uložit** Uložte aktualizovaný soubor *web.config* .</span><span class="sxs-lookup"><span data-stu-id="a560a-1021">Select **Save** to save the updated *web.config* file.</span></span>

<span data-ttu-id="a560a-1022">Pokračovat k aktivaci protokolování diagnostiky:</span><span class="sxs-lookup"><span data-stu-id="a560a-1022">Proceed to activate diagnostic logging:</span></span>

1. <span data-ttu-id="a560a-1023">V Azure Portal vyberte okno **diagnostické protokoly** .</span><span class="sxs-lookup"><span data-stu-id="a560a-1023">In the Azure portal, select the **Diagnostics logs** blade.</span></span>
1. <span data-ttu-id="a560a-1024">Vyberte přepínač **zapnuto** pro **protokolování aplikace (systém souborů)** a **podrobné chybové zprávy**.</span><span class="sxs-lookup"><span data-stu-id="a560a-1024">Select the **On** switch for **Application Logging (Filesystem)** and **Detailed error messages**.</span></span> <span data-ttu-id="a560a-1025">V horní části okna vyberte tlačítko **Uložit** .</span><span class="sxs-lookup"><span data-stu-id="a560a-1025">Select the **Save** button at the top of the blade.</span></span>
1. <span data-ttu-id="a560a-1026">Chcete-li zahrnout trasování chybných požadavků, označované také jako protokolování událostí neúspěšných požadavků na FREB, vyberte přepínač **zapnuto** pro **trasování chybných požadavků**.</span><span class="sxs-lookup"><span data-stu-id="a560a-1026">To include failed request tracing, also known as Failed Request Event Buffering (FREB) logging, select the **On** switch for **Failed request tracing**.</span></span>
1. <span data-ttu-id="a560a-1027">Vyberte okno **log Stream** , které je uvedeno hned pod oknem **diagnostické protokoly** na portálu.</span><span class="sxs-lookup"><span data-stu-id="a560a-1027">Select the **Log stream** blade, which is listed immediately under the **Diagnostics logs** blade in the portal.</span></span>
1. <span data-ttu-id="a560a-1028">Vytvořte žádost do aplikace.</span><span class="sxs-lookup"><span data-stu-id="a560a-1028">Make a request to the app.</span></span>
1. <span data-ttu-id="a560a-1029">V datech streamu protokolu se označuje příčina chyby.</span><span class="sxs-lookup"><span data-stu-id="a560a-1029">Within the log stream data, the cause of the error is indicated.</span></span>

<span data-ttu-id="a560a-1030">Nezapomeňte zakázat protokolování stdout po dokončení řešení potíží.</span><span class="sxs-lookup"><span data-stu-id="a560a-1030">Be sure to disable stdout logging when troubleshooting is complete.</span></span>

<span data-ttu-id="a560a-1031">Zobrazení protokolů pro trasování chybných požadavků (protokoly FREB):</span><span class="sxs-lookup"><span data-stu-id="a560a-1031">To view the failed request tracing logs (FREB logs):</span></span>

1. <span data-ttu-id="a560a-1032">Přejděte do okna **diagnostikovat a řešit problémy** v Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="a560a-1032">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="a560a-1033">V oblasti **nástroje podpory** v postranním panelu vyberte **protokoly pro trasování chybných požadavků** .</span><span class="sxs-lookup"><span data-stu-id="a560a-1033">Select **Failed Request Tracing Logs** from the **SUPPORT TOOLS** area of the sidebar.</span></span>

<span data-ttu-id="a560a-1034">V [části Povolení protokolování diagnostiky pro webové aplikace v Azure App Service tématu](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) a [Nejčastější dotazy týkající se výkonu aplikace pro Web Apps v Azure: návody zapnout trasování chybných požadavků?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing) Další informace.</span><span class="sxs-lookup"><span data-stu-id="a560a-1034">See [Failed request traces section of the Enable diagnostics logging for web apps in Azure App Service topic](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) and the [Application performance FAQs for Web Apps in Azure: How do I turn on failed request tracing?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing) for more information.</span></span>

<span data-ttu-id="a560a-1035">Další informace najdete v tématu [Povolení protokolování diagnostiky pro webové aplikace v Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log).</span><span class="sxs-lookup"><span data-stu-id="a560a-1035">For more information, see [Enable diagnostics logging for web apps in Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log).</span></span>

> [!WARNING]
> <span data-ttu-id="a560a-1036">Nepovedlo se zakázat protokol stdout, což může způsobit selhání aplikace nebo serveru.</span><span class="sxs-lookup"><span data-stu-id="a560a-1036">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="a560a-1037">Velikost souboru protokolu ani počet vytvořených souborů protokolu nijak neomezuje.</span><span class="sxs-lookup"><span data-stu-id="a560a-1037">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="a560a-1038">Pro rutinu protokolování v aplikaci ASP.NET Core použijte knihovnu protokolování, která omezuje velikost souboru protokolu a otočí protokoly.</span><span class="sxs-lookup"><span data-stu-id="a560a-1038">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="a560a-1039">Další informace najdete v tématu [Zprostředkovatelé protokolování třetích stran](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="a560a-1039">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

## <a name="troubleshoot-on-iis"></a><span data-ttu-id="a560a-1040">Odstraňování potíží ve službě IIS</span><span class="sxs-lookup"><span data-stu-id="a560a-1040">Troubleshoot on IIS</span></span>

### <a name="application-event-log-iis"></a><span data-ttu-id="a560a-1041">Protokol událostí aplikace (IIS)</span><span class="sxs-lookup"><span data-stu-id="a560a-1041">Application Event Log (IIS)</span></span>

<span data-ttu-id="a560a-1042">Přístup k protokolu událostí aplikace:</span><span class="sxs-lookup"><span data-stu-id="a560a-1042">Access the Application Event Log:</span></span>

1. <span data-ttu-id="a560a-1043">Otevřete nabídku Start, vyhledejte *Prohlížeč událostí*a vyberte aplikaci **Prohlížeč událostí** .</span><span class="sxs-lookup"><span data-stu-id="a560a-1043">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="a560a-1044">V **Prohlížeč událostí**otevřete uzel **protokoly systému Windows** .</span><span class="sxs-lookup"><span data-stu-id="a560a-1044">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="a560a-1045">Výběrem **aplikace** otevřete protokol událostí aplikace.</span><span class="sxs-lookup"><span data-stu-id="a560a-1045">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="a560a-1046">Vyhledá chyby spojené s chybnou aplikací.</span><span class="sxs-lookup"><span data-stu-id="a560a-1046">Search for errors associated with the failing app.</span></span> <span data-ttu-id="a560a-1047">Chyby mají ve *zdrojovém* sloupci hodnotu *modulu IIS AspNetCore* nebo *modul IIS Express AspNetCore* .</span><span class="sxs-lookup"><span data-stu-id="a560a-1047">Errors have a value of *IIS AspNetCore Module* or *IIS Express AspNetCore Module* in the *Source* column.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="a560a-1048">Spuštění aplikace na příkazovém řádku</span><span class="sxs-lookup"><span data-stu-id="a560a-1048">Run the app at a command prompt</span></span>

<span data-ttu-id="a560a-1049">Mnoho chyb při spuštění nevytváří užitečné informace v protokolu událostí aplikace.</span><span class="sxs-lookup"><span data-stu-id="a560a-1049">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="a560a-1050">Příčinu některých chyb můžete najít spuštěním aplikace na příkazovém řádku v hostitelském systému.</span><span class="sxs-lookup"><span data-stu-id="a560a-1050">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span>

#### <a name="framework-dependent-deployment"></a><span data-ttu-id="a560a-1051">Nasazení závislé na rozhraní</span><span class="sxs-lookup"><span data-stu-id="a560a-1051">Framework-dependent deployment</span></span>

<span data-ttu-id="a560a-1052">Pokud aplikace je [nasazení závislé na rozhraní](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span><span class="sxs-lookup"><span data-stu-id="a560a-1052">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

1. <span data-ttu-id="a560a-1053">Na příkazovém řádku přejděte do složky pro nasazení a spusťte aplikaci spuštěním sestavení aplikace pomocí *dotnet.exe*.</span><span class="sxs-lookup"><span data-stu-id="a560a-1053">At a command prompt, navigate to the deployment folder and run the app by executing the app's assembly with *dotnet.exe*.</span></span> <span data-ttu-id="a560a-1054">V následujícím příkazu nahraďte název sestavení aplikace pro \<assembly_name> : `dotnet .\<assembly_name>.dll` .</span><span class="sxs-lookup"><span data-stu-id="a560a-1054">In the following command, substitute the name of the app's assembly for \<assembly_name>: `dotnet .\<assembly_name>.dll`.</span></span>
1. <span data-ttu-id="a560a-1055">Výstup konzoly z aplikace, který zobrazuje všechny chyby, je zapsán do okna konzoly.</span><span class="sxs-lookup"><span data-stu-id="a560a-1055">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="a560a-1056">Pokud dojde k chybám při podání žádosti do aplikace, vytvořte žádost na hostitele a port, kde Kestrel naslouchá.</span><span class="sxs-lookup"><span data-stu-id="a560a-1056">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="a560a-1057">Pomocí výchozího hostitele a příspěvku vytvořte žádost `http://localhost:5000/` .</span><span class="sxs-lookup"><span data-stu-id="a560a-1057">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="a560a-1058">Pokud aplikace reaguje normálně na adrese koncového bodu Kestrel, problém je pravděpodobnější v souvislosti s konfigurací hostování a méně pravděpodobným v rámci aplikace.</span><span class="sxs-lookup"><span data-stu-id="a560a-1058">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

#### <a name="self-contained-deployment"></a><span data-ttu-id="a560a-1059">Samostatně zahrnuté nasazení</span><span class="sxs-lookup"><span data-stu-id="a560a-1059">Self-contained deployment</span></span>

<span data-ttu-id="a560a-1060">Pokud je aplikace samostatná, [nasazení](/dotnet/core/deploying/#self-contained-deployments-scd):</span><span class="sxs-lookup"><span data-stu-id="a560a-1060">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

1. <span data-ttu-id="a560a-1061">Na příkazovém řádku přejděte do složky pro nasazení a spusťte spustitelný soubor aplikace.</span><span class="sxs-lookup"><span data-stu-id="a560a-1061">At a command prompt, navigate to the deployment folder and run the app's executable.</span></span> <span data-ttu-id="a560a-1062">V následujícím příkazu nahraďte název sestavení aplikace pro \<assembly_name> : `<assembly_name>.exe` .</span><span class="sxs-lookup"><span data-stu-id="a560a-1062">In the following command, substitute the name of the app's assembly for \<assembly_name>: `<assembly_name>.exe`.</span></span>
1. <span data-ttu-id="a560a-1063">Výstup konzoly z aplikace, který zobrazuje všechny chyby, je zapsán do okna konzoly.</span><span class="sxs-lookup"><span data-stu-id="a560a-1063">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="a560a-1064">Pokud dojde k chybám při podání žádosti do aplikace, vytvořte žádost na hostitele a port, kde Kestrel naslouchá.</span><span class="sxs-lookup"><span data-stu-id="a560a-1064">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="a560a-1065">Pomocí výchozího hostitele a příspěvku vytvořte žádost `http://localhost:5000/` .</span><span class="sxs-lookup"><span data-stu-id="a560a-1065">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="a560a-1066">Pokud aplikace reaguje normálně na adrese koncového bodu Kestrel, problém je pravděpodobnější v souvislosti s konfigurací hostování a méně pravděpodobným v rámci aplikace.</span><span class="sxs-lookup"><span data-stu-id="a560a-1066">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

### <a name="aspnet-core-module-stdout-log-iis"></a><span data-ttu-id="a560a-1067">Protokol stdout v modulu ASP.NET Core (IIS)</span><span class="sxs-lookup"><span data-stu-id="a560a-1067">ASP.NET Core Module stdout log (IIS)</span></span>

<span data-ttu-id="a560a-1068">Postup povolení a zobrazení protokolů stdout:</span><span class="sxs-lookup"><span data-stu-id="a560a-1068">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="a560a-1069">Přejděte do složky pro nasazení webu v hostitelském systému.</span><span class="sxs-lookup"><span data-stu-id="a560a-1069">Navigate to the site's deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="a560a-1070">Pokud složka *logs* není k dispozici, vytvořte složku.</span><span class="sxs-lookup"><span data-stu-id="a560a-1070">If the *logs* folder isn't present, create the folder.</span></span> <span data-ttu-id="a560a-1071">Pokyny k tomu, jak povolit MSBuild pro vytvoření složky *logs* v nasazení automaticky, najdete v tématu [Struktura adresáře](xref:host-and-deploy/directory-structure) .</span><span class="sxs-lookup"><span data-stu-id="a560a-1071">For instructions on how to enable MSBuild to create the *logs* folder in the deployment automatically, see the [Directory structure](xref:host-and-deploy/directory-structure) topic.</span></span>
1. <span data-ttu-id="a560a-1072">Upravte soubor *web.config* .</span><span class="sxs-lookup"><span data-stu-id="a560a-1072">Edit the *web.config* file.</span></span> <span data-ttu-id="a560a-1073">Nastavte **stdoutLogEnabled** na `true` a změňte cestu **stdoutLogFile** tak, aby odkazovala na složku *logs* (například `.\logs\stdout` ).</span><span class="sxs-lookup"><span data-stu-id="a560a-1073">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to point to the *logs* folder (for example, `.\logs\stdout`).</span></span> <span data-ttu-id="a560a-1074">`stdout` v cestě je předpona názvu souboru protokolu.</span><span class="sxs-lookup"><span data-stu-id="a560a-1074">`stdout` in the path is the log file name prefix.</span></span> <span data-ttu-id="a560a-1075">Časové razítko, ID procesu a Přípona souboru se automaticky přidají při vytvoření protokolu.</span><span class="sxs-lookup"><span data-stu-id="a560a-1075">A timestamp, process id, and file extension are added automatically when the log is created.</span></span> <span data-ttu-id="a560a-1076">`stdout`Jako předpona názvu souboru se používá typický soubor protokolu s názvem *stdout_20180205184032_5412. log*.</span><span class="sxs-lookup"><span data-stu-id="a560a-1076">Using `stdout` as the file name prefix, a typical log file is named *stdout_20180205184032_5412.log*.</span></span>
1. <span data-ttu-id="a560a-1077">Zajistěte, aby identita fondu aplikací měla oprávnění k zápisu do složky *logs* .</span><span class="sxs-lookup"><span data-stu-id="a560a-1077">Ensure your application pool's identity has write permissions to the *logs* folder.</span></span>
1. <span data-ttu-id="a560a-1078">Uložte aktualizovaný soubor *web.config* .</span><span class="sxs-lookup"><span data-stu-id="a560a-1078">Save the updated *web.config* file.</span></span>
1. <span data-ttu-id="a560a-1079">Vytvořte žádost do aplikace.</span><span class="sxs-lookup"><span data-stu-id="a560a-1079">Make a request to the app.</span></span>
1. <span data-ttu-id="a560a-1080">Přejděte do složky *logs* .</span><span class="sxs-lookup"><span data-stu-id="a560a-1080">Navigate to the *logs* folder.</span></span> <span data-ttu-id="a560a-1081">Vyhledejte a otevřete nejnovější protokol STDOUT.</span><span class="sxs-lookup"><span data-stu-id="a560a-1081">Find and open the most recent stdout log.</span></span>
1. <span data-ttu-id="a560a-1082">Prostudujte si protokol o chybách.</span><span class="sxs-lookup"><span data-stu-id="a560a-1082">Study the log for errors.</span></span>

<span data-ttu-id="a560a-1083">Zakázat protokolování stdout při odstraňování potíží:</span><span class="sxs-lookup"><span data-stu-id="a560a-1083">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="a560a-1084">Upravte soubor *web.config* .</span><span class="sxs-lookup"><span data-stu-id="a560a-1084">Edit the *web.config* file.</span></span>
1. <span data-ttu-id="a560a-1085">Nastavte **stdoutLogEnabled** na `false` .</span><span class="sxs-lookup"><span data-stu-id="a560a-1085">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="a560a-1086">Soubor uložte.</span><span class="sxs-lookup"><span data-stu-id="a560a-1086">Save the file.</span></span>

<span data-ttu-id="a560a-1087">Další informace naleznete v tématu <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span><span class="sxs-lookup"><span data-stu-id="a560a-1087">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

> [!WARNING]
> <span data-ttu-id="a560a-1088">Nepovedlo se zakázat protokol stdout, což může způsobit selhání aplikace nebo serveru.</span><span class="sxs-lookup"><span data-stu-id="a560a-1088">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="a560a-1089">Velikost souboru protokolu ani počet vytvořených souborů protokolu nijak neomezuje.</span><span class="sxs-lookup"><span data-stu-id="a560a-1089">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="a560a-1090">Pro rutinu protokolování v aplikaci ASP.NET Core použijte knihovnu protokolování, která omezuje velikost souboru protokolu a otočí protokoly.</span><span class="sxs-lookup"><span data-stu-id="a560a-1090">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="a560a-1091">Další informace najdete v tématu [Zprostředkovatelé protokolování třetích stran](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="a560a-1091">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="enable-the-developer-exception-page"></a><span data-ttu-id="a560a-1092">Povolit stránku výjimky pro vývojáře</span><span class="sxs-lookup"><span data-stu-id="a560a-1092">Enable the Developer Exception Page</span></span>

<span data-ttu-id="a560a-1093">`ASPNETCORE_ENVIRONMENT` [Proměnnou prostředí lze přidat do web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) ke spuštění aplikace ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="a560a-1093">The `ASPNETCORE_ENVIRONMENT` [environment variable can be added to web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) to run the app in the Development environment.</span></span> <span data-ttu-id="a560a-1094">Pokud se prostředí nepřepisuje při spuštění aplikace v `UseEnvironment` tvůrci hostitele, nastavení proměnné prostředí umožní, aby se při spuštění aplikace zobrazila [Stránka s výjimkou vývojářů](xref:fundamentals/error-handling) .</span><span class="sxs-lookup"><span data-stu-id="a560a-1094">As long as the environment isn't overridden in app startup by `UseEnvironment` on the host builder, setting the environment variable allows the [Developer Exception Page](xref:fundamentals/error-handling) to appear when the app is run.</span></span>

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

<span data-ttu-id="a560a-1095">Nastavení proměnné prostředí pro `ASPNETCORE_ENVIRONMENT` se doporučuje jenom pro použití na pracovních a testovacích serverech, které nejsou přístupné pro Internet.</span><span class="sxs-lookup"><span data-stu-id="a560a-1095">Setting the environment variable for `ASPNETCORE_ENVIRONMENT` is only recommended for use on staging and testing servers that aren't exposed to the Internet.</span></span> <span data-ttu-id="a560a-1096">Po vyřešení potíží odeberte proměnnou prostředí ze souboru *web.config* .</span><span class="sxs-lookup"><span data-stu-id="a560a-1096">Remove the environment variable from the *web.config* file after troubleshooting.</span></span> <span data-ttu-id="a560a-1097">Informace o nastavení proměnných prostředí v *web.config*naleznete v tématu [environmentVariables Child element of aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).</span><span class="sxs-lookup"><span data-stu-id="a560a-1097">For information on setting environment variables in *web.config*, see [environmentVariables child element of aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).</span></span>

### <a name="obtain-data-from-an-app"></a><span data-ttu-id="a560a-1098">Získání dat z aplikace</span><span class="sxs-lookup"><span data-stu-id="a560a-1098">Obtain data from an app</span></span>

<span data-ttu-id="a560a-1099">Pokud aplikace dokáže reagovat na žádosti, získat požadavek, připojení a další data z aplikace pomocí vloženého middlewaru terminálu.</span><span class="sxs-lookup"><span data-stu-id="a560a-1099">If an app is capable of responding to requests, obtain request, connection, and additional data from the app using terminal inline middleware.</span></span> <span data-ttu-id="a560a-1100">Další informace a ukázka kódu naleznete v tématu <xref:test/troubleshoot#obtain-data-from-an-app> .</span><span class="sxs-lookup"><span data-stu-id="a560a-1100">For more information and sample code, see <xref:test/troubleshoot#obtain-data-from-an-app>.</span></span>

### <a name="slow-or-hanging-app-iis"></a><span data-ttu-id="a560a-1101">Pomalá nebo zavěšená aplikace (IIS)</span><span class="sxs-lookup"><span data-stu-id="a560a-1101">Slow or hanging app (IIS)</span></span>

<span data-ttu-id="a560a-1102">*Výpis stavu* systému je snímek paměti systému a může vám pomůže určit příčinu selhání aplikace, selhání při spuštění nebo pomalé aplikace.</span><span class="sxs-lookup"><span data-stu-id="a560a-1102">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="a560a-1103">Aplikace selže nebo dojde k výjimce.</span><span class="sxs-lookup"><span data-stu-id="a560a-1103">App crashes or encounters an exception</span></span>

<span data-ttu-id="a560a-1104">Získat a analyzovat výpis z [zasílání zpráv o chybách systému Windows (WER)](/windows/desktop/wer/windows-error-reporting):</span><span class="sxs-lookup"><span data-stu-id="a560a-1104">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="a560a-1105">Vytvořte složku uchovávající soubory s výpisem stavu systému `c:\dumps` .</span><span class="sxs-lookup"><span data-stu-id="a560a-1105">Create a folder to hold crash dump files at `c:\dumps`.</span></span> <span data-ttu-id="a560a-1106">Fond aplikací musí mít ke složce přístup pro zápis.</span><span class="sxs-lookup"><span data-stu-id="a560a-1106">The app pool must have write access to the folder.</span></span>
1. <span data-ttu-id="a560a-1107">Spusťte [skript prostředí PowerShell pro EnableDumps](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1):</span><span class="sxs-lookup"><span data-stu-id="a560a-1107">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1):</span></span>
   * <span data-ttu-id="a560a-1108">Pokud aplikace používá [model hostování v rámci procesu](xref:host-and-deploy/iis/index#in-process-hosting-model), spusťte skript pro *w3wp.exe*:</span><span class="sxs-lookup"><span data-stu-id="a560a-1108">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\EnableDumps w3wp.exe c:\dumps
     ```

   * <span data-ttu-id="a560a-1109">Pokud aplikace používá [model hostování mimo proces](xref:host-and-deploy/iis/index#out-of-process-hosting-model), spusťte skript pro *dotnet.exe*:</span><span class="sxs-lookup"><span data-stu-id="a560a-1109">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\EnableDumps dotnet.exe c:\dumps
     ```

1. <span data-ttu-id="a560a-1110">Spusťte aplikaci za podmínek, které způsobí, že dojde k chybě.</span><span class="sxs-lookup"><span data-stu-id="a560a-1110">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="a560a-1111">Po chybě spusťte [skript PowerShellu DisableDumps](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1):</span><span class="sxs-lookup"><span data-stu-id="a560a-1111">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1):</span></span>
   * <span data-ttu-id="a560a-1112">Pokud aplikace používá [model hostování v rámci procesu](xref:host-and-deploy/iis/index#in-process-hosting-model), spusťte skript pro *w3wp.exe*:</span><span class="sxs-lookup"><span data-stu-id="a560a-1112">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\DisableDumps w3wp.exe
     ```

   * <span data-ttu-id="a560a-1113">Pokud aplikace používá [model hostování mimo proces](xref:host-and-deploy/iis/index#out-of-process-hosting-model), spusťte skript pro *dotnet.exe*:</span><span class="sxs-lookup"><span data-stu-id="a560a-1113">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\DisableDumps dotnet.exe
     ```

<span data-ttu-id="a560a-1114">Po selhání aplikace a dokončení shromažďování výpisu je možné aplikaci ukončit normálně.</span><span class="sxs-lookup"><span data-stu-id="a560a-1114">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="a560a-1115">Skript PowerShellu nakonfiguruje WER a shromáždí až pět výpisů paměti pro každou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="a560a-1115">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="a560a-1116">Výpisy stavu systému můžou zabírat velké množství místa na disku (každý až několik gigabajtů).</span><span class="sxs-lookup"><span data-stu-id="a560a-1116">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="a560a-1117">Aplikace přestane reagovat, dojde k chybě při spuštění nebo se spustí normálně.</span><span class="sxs-lookup"><span data-stu-id="a560a-1117">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="a560a-1118">Když *aplikace přestane reagovat (zastaví* se, ale nejedná se o chybu), selže během spuštění nebo se spustí normálně, podívejte [se na soubory výpisu paměti v uživatelském režimu: zvolením nejlepšího](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) nástroje vyberte vhodný nástroj pro vytváření výpisu.</span><span class="sxs-lookup"><span data-stu-id="a560a-1118">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="a560a-1119">Analýza výpisu paměti</span><span class="sxs-lookup"><span data-stu-id="a560a-1119">Analyze the dump</span></span>

<span data-ttu-id="a560a-1120">Výpis paměti lze analyzovat pomocí několika přístupů.</span><span class="sxs-lookup"><span data-stu-id="a560a-1120">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="a560a-1121">Další informace najdete v tématu [Analýza souboru s výpisem stavu v uživatelském režimu](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span><span class="sxs-lookup"><span data-stu-id="a560a-1121">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="clear-package-caches"></a><span data-ttu-id="a560a-1122">Vymazat mezipaměti balíčků</span><span class="sxs-lookup"><span data-stu-id="a560a-1122">Clear package caches</span></span>

<span data-ttu-id="a560a-1123">Funkční aplikace může po upgradu .NET Core SDK ve vývojovém počítači nebo změně verzí balíčku v rámci aplikace selhat okamžitě.</span><span class="sxs-lookup"><span data-stu-id="a560a-1123">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="a560a-1124">V některých případech můžou nesouvislé balíčky při provádění hlavních upgradů poškodit aplikaci.</span><span class="sxs-lookup"><span data-stu-id="a560a-1124">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="a560a-1125">Většinu těchto problémů můžete vyřešit pomocí následujících pokynů:</span><span class="sxs-lookup"><span data-stu-id="a560a-1125">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="a560a-1126">Odstraňte složky *bin* a *obj* .</span><span class="sxs-lookup"><span data-stu-id="a560a-1126">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="a560a-1127">Pomocí příkazu [dotnet All--Clear](/dotnet/core/tools/dotnet-nuget-locals) z příkazového prostředí vymažte mezipaměť balíčku.</span><span class="sxs-lookup"><span data-stu-id="a560a-1127">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="a560a-1128">Mazání mezipamětí balíčků lze také provést pomocí nástroje [nuget.exe](https://www.nuget.org/downloads) a provedení příkazu `nuget locals all -clear` .</span><span class="sxs-lookup"><span data-stu-id="a560a-1128">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="a560a-1129">*nuget.exe* není sada odinstalována s desktopovým operačním systémem Windows a je nutné ji získat samostatně z [webu NuGet](https://www.nuget.org/downloads).</span><span class="sxs-lookup"><span data-stu-id="a560a-1129">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="a560a-1130">Obnovte a znovu sestavte projekt.</span><span class="sxs-lookup"><span data-stu-id="a560a-1130">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="a560a-1131">Před opětovným nasazením aplikace odstraňte všechny soubory ve složce pro nasazení na serveru.</span><span class="sxs-lookup"><span data-stu-id="a560a-1131">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a560a-1132">Další zdroje informací</span><span class="sxs-lookup"><span data-stu-id="a560a-1132">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:fundamentals/error-handling>
* <xref:host-and-deploy/aspnet-core-module>

### <a name="azure-documentation"></a><span data-ttu-id="a560a-1133">Dokumentace k Azure</span><span class="sxs-lookup"><span data-stu-id="a560a-1133">Azure documentation</span></span>

* [<span data-ttu-id="a560a-1134">Application Insights pro ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a560a-1134">Application Insights for ASP.NET Core</span></span>](/azure/application-insights/app-insights-asp-net-core)
* [<span data-ttu-id="a560a-1135">Část vzdálené ladění webových aplikací řešení potíží s webovou aplikací v Azure App Service pomocí sady Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a560a-1135">Remote debugging web apps section of Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug)
* [<span data-ttu-id="a560a-1136">Přehled diagnostiky Azure App Service</span><span class="sxs-lookup"><span data-stu-id="a560a-1136">Azure App Service diagnostics overview</span></span>](/azure/app-service/app-service-diagnostics)
* [<span data-ttu-id="a560a-1137">Postup: Monitorování aplikací ve službě Azure App Service</span><span class="sxs-lookup"><span data-stu-id="a560a-1137">How to: Monitor Apps in Azure App Service</span></span>](/azure/app-service/web-sites-monitor)
* [<span data-ttu-id="a560a-1138">Řešení potíží s webovou aplikací v Azure App Service pomocí sady Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a560a-1138">Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [<span data-ttu-id="a560a-1139">Řešení chyb HTTP z "502 špatné brány" a "nedostupné služby 503" v Azure Web Apps</span><span class="sxs-lookup"><span data-stu-id="a560a-1139">Troubleshoot HTTP errors of "502 bad gateway" and "503 service unavailable" in your Azure web apps</span></span>](/azure/app-service/app-service-web-troubleshoot-http-502-http-503)
* [<span data-ttu-id="a560a-1140">Řešení potíží s výkonem pomalých webových aplikací ve službě Azure App Service</span><span class="sxs-lookup"><span data-stu-id="a560a-1140">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="a560a-1141">Nejčastější dotazy týkající se výkonu aplikací pro Web Apps v Azure</span><span class="sxs-lookup"><span data-stu-id="a560a-1141">Application performance FAQs for Web Apps in Azure</span></span>](/azure/app-service/app-service-web-availability-performance-application-issues-faq)
* [<span data-ttu-id="a560a-1142">Sandbox webové aplikace Azure (omezení spuštění App Service Runtime)</span><span class="sxs-lookup"><span data-stu-id="a560a-1142">Azure Web App sandbox (App Service runtime execution limitations)</span></span>](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)
* [<span data-ttu-id="a560a-1143">Azure pátek: Azure App Service diagnostické prostředí a řešení potíží (12 minut video)</span><span class="sxs-lookup"><span data-stu-id="a560a-1143">Azure Friday: Azure App Service Diagnostic and Troubleshooting Experience (12-minute video)</span></span>](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)

### <a name="visual-studio-documentation"></a><span data-ttu-id="a560a-1144">Dokumentace sady Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a560a-1144">Visual Studio documentation</span></span>

* [<span data-ttu-id="a560a-1145">Vzdálené ladění ASP.NET Core ve službě IIS v Azure v aplikaci Visual Studio 2017</span><span class="sxs-lookup"><span data-stu-id="a560a-1145">Remote Debug ASP.NET Core on IIS in Azure in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-azure)
* [<span data-ttu-id="a560a-1146">Vzdálené ladění ASP.NET Core na vzdáleném počítači IIS v aplikaci Visual Studio 2017</span><span class="sxs-lookup"><span data-stu-id="a560a-1146">Remote Debug ASP.NET Core on a Remote IIS Computer in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-aspnet-on-a-remote-iis-computer)
* [<span data-ttu-id="a560a-1147">Informace o ladění pomocí sady Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a560a-1147">Learn to debug using Visual Studio</span></span>](/visualstudio/debugger/getting-started-with-the-debugger)

### <a name="visual-studio-code-documentation"></a><span data-ttu-id="a560a-1148">Dokumentace k Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a560a-1148">Visual Studio Code documentation</span></span>

* [<span data-ttu-id="a560a-1149">Ladění pomocí Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a560a-1149">Debugging with Visual Studio Code</span></span>](https://code.visualstudio.com/docs/editor/debugging)

::: moniker-end
