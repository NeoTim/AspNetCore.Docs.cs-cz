---
title: Řešení potíží s ASP.NET Core v Azure App Service a IIS
author: guardrex
description: Naučte se diagnostikovat problémy s Azure App Service a Internetová informační služba nasazení ASP.NET Core aplikací (IIS).
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/20/2019
uid: test/troubleshoot-azure-iis
ms.openlocfilehash: 49a0f59fb6930235de10c726f3695f2a5352efb2
ms.sourcegitcommit: 8157e5a351f49aeef3769f7d38b787b4386aad5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2019
ms.locfileid: "74251968"
---
# <a name="troubleshoot-aspnet-core-on-azure-app-service-and-iis"></a><span data-ttu-id="e68a4-103">Řešení potíží s ASP.NET Core v Azure App Service a IIS</span><span class="sxs-lookup"><span data-stu-id="e68a4-103">Troubleshoot ASP.NET Core on Azure App Service and IIS</span></span>

<span data-ttu-id="e68a4-104">Od [Luke Latham](https://github.com/guardrex) a [Justin Kotalik](https://github.com/jkotalik)</span><span class="sxs-lookup"><span data-stu-id="e68a4-104">By [Luke Latham](https://github.com/guardrex) and [Justin Kotalik](https://github.com/jkotalik)</span></span>

<span data-ttu-id="e68a4-105">Tento článek poskytuje informace o běžných chybách při spuštění aplikací a pokyny k diagnostice chyb při nasazení aplikace do Azure App Service nebo IIS:</span><span class="sxs-lookup"><span data-stu-id="e68a4-105">This article provides information on common app startup errors and instructions on how to diagnose errors when an app is deployed to Azure App Service or IIS:</span></span>

[<span data-ttu-id="e68a4-106">Chyby při spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="e68a4-106">App startup errors</span></span>](#app-startup-errors)  
<span data-ttu-id="e68a4-107">Vysvětluje běžné spouštění scénářů stavového kódu HTTP.</span><span class="sxs-lookup"><span data-stu-id="e68a4-107">Explains common startup HTTP status code scenarios.</span></span>

[<span data-ttu-id="e68a4-108">Řešení potíží na Azure App Service</span><span class="sxs-lookup"><span data-stu-id="e68a4-108">Troubleshoot on Azure App Service</span></span>](#troubleshoot-on-azure-app-service)  
<span data-ttu-id="e68a4-109">Poskytuje rady pro řešení potíží pro aplikace nasazené do Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="e68a4-109">Provides troubleshooting advice for apps deployed to Azure App Service.</span></span>

[<span data-ttu-id="e68a4-110">Odstraňování potíží ve službě IIS</span><span class="sxs-lookup"><span data-stu-id="e68a4-110">Troubleshoot on IIS</span></span>](#troubleshoot-on-iis)  
<span data-ttu-id="e68a4-111">Poskytuje rady pro řešení potíží pro aplikace nasazené do služby IIS nebo místně spuštěné v IIS Express.</span><span class="sxs-lookup"><span data-stu-id="e68a4-111">Provides troubleshooting advice for apps deployed to IIS or running on IIS Express locally.</span></span> <span data-ttu-id="e68a4-112">Pokyny platí pro nasazení systémů Windows Server a Desktop v systému Windows.</span><span class="sxs-lookup"><span data-stu-id="e68a4-112">The guidance applies to both Windows Server and Windows desktop deployments.</span></span>

[<span data-ttu-id="e68a4-113">Vymazat mezipaměti balíčků</span><span class="sxs-lookup"><span data-stu-id="e68a4-113">Clear package caches</span></span>](#clear-package-caches)  
<span data-ttu-id="e68a4-114">Vysvětluje, co dělat v případě, že při provádění podstatných upgradů nebo změně verze balíčku dojde k přerušení aplikace v nesouvislých balíčcích.</span><span class="sxs-lookup"><span data-stu-id="e68a4-114">Explains what to do when incoherent packages break an app when performing major upgrades or changing package versions.</span></span>

[<span data-ttu-id="e68a4-115">Další zdroje informací</span><span class="sxs-lookup"><span data-stu-id="e68a4-115">Additional resources</span></span>](#additional-resources)  
<span data-ttu-id="e68a4-116">Seznam dalších témat pro odstraňování potíží.</span><span class="sxs-lookup"><span data-stu-id="e68a4-116">Lists additional troubleshooting topics.</span></span>

## <a name="app-startup-errors"></a><span data-ttu-id="e68a4-117">Chyby při spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="e68a4-117">App startup errors</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="e68a4-118">V aplikaci Visual Studio je výchozím nastavením projektu ASP.NET Core [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hostování během ladění.</span><span class="sxs-lookup"><span data-stu-id="e68a4-118">In Visual Studio, an ASP.NET Core project defaults to [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hosting during debugging.</span></span> <span data-ttu-id="e68a4-119">*502,5 – selhání procesu* nebo *500,30 – chyba* , ke které dojde při místním ladění, se dá diagnostikovat pomocí Rady v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="e68a4-119">A *502.5 - Process Failure* or a *500.30 - Start Failure* that occurs when debugging locally can be diagnosed using the advice in this topic.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="e68a4-120">V aplikaci Visual Studio je výchozím nastavením projektu ASP.NET Core [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hostování během ladění.</span><span class="sxs-lookup"><span data-stu-id="e68a4-120">In Visual Studio, an ASP.NET Core project defaults to [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hosting during debugging.</span></span> <span data-ttu-id="e68a4-121">*Selhání procesu 502,5* , ke kterému dochází, když se místně ladění dá diagnostikovat pomocí Rady v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="e68a4-121">A *502.5 Process Failure* that occurs when debugging locally can be diagnosed using the advice in this topic.</span></span>

::: moniker-end

### <a name="40314-forbidden"></a><span data-ttu-id="e68a4-122">403,14 zakázané</span><span class="sxs-lookup"><span data-stu-id="e68a4-122">403.14 Forbidden</span></span>

<span data-ttu-id="e68a4-123">Aplikaci se nepodařilo spustit.</span><span class="sxs-lookup"><span data-stu-id="e68a4-123">The app fails to start.</span></span> <span data-ttu-id="e68a4-124">Protokoluje se následující chyba:</span><span class="sxs-lookup"><span data-stu-id="e68a4-124">The following error is logged:</span></span>

```
The Web server is configured to not list the contents of this directory.
```

<span data-ttu-id="e68a4-125">K této chybě obvykle dochází v důsledku poškozeného nasazení v hostitelském systému, který zahrnuje kterýkoli z následujících scénářů:</span><span class="sxs-lookup"><span data-stu-id="e68a4-125">The error is usually caused by a broken deployment on the hosting system, which includes any of the following scenarios:</span></span>

* <span data-ttu-id="e68a4-126">Aplikace je nasazená do chybné složky v hostitelském systému.</span><span class="sxs-lookup"><span data-stu-id="e68a4-126">The app is deployed to the wrong folder on the hosting system.</span></span>
* <span data-ttu-id="e68a4-127">Procesu nasazení se nepodařilo přesunout všechny soubory a složky aplikace do složky pro nasazení v hostitelském systému.</span><span class="sxs-lookup"><span data-stu-id="e68a4-127">The deployment process failed to move all of the app's files and folders to the deployment folder on the hosting system.</span></span>
* <span data-ttu-id="e68a4-128">V nasazení chybí soubor *Web. config* , nebo je obsah souboru *Web. config* poškozený.</span><span class="sxs-lookup"><span data-stu-id="e68a4-128">The *web.config* file is missing from the deployment, or the *web.config* file contents are malformed.</span></span>

<span data-ttu-id="e68a4-129">Proveďte následující kroky:</span><span class="sxs-lookup"><span data-stu-id="e68a4-129">Perform the following steps:</span></span>

1. <span data-ttu-id="e68a4-130">Odstraňte všechny soubory a složky ze složky pro nasazení v hostitelském systému.</span><span class="sxs-lookup"><span data-stu-id="e68a4-130">Delete all of the files and folders from the deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="e68a4-131">Znovu nasaďte obsah složky pro *publikování* aplikace do hostitelského systému pomocí běžné metody nasazení, jako je například Visual Studio, PowerShell nebo ruční nasazení:</span><span class="sxs-lookup"><span data-stu-id="e68a4-131">Redeploy the contents of the app's *publish* folder to the hosting system using your normal method of deployment, such as Visual Studio, PowerShell, or manual deployment:</span></span>
   * <span data-ttu-id="e68a4-132">Zkontrolujte, zda je v nasazení přítomen soubor *Web. config* a zda jeho obsah je správný.</span><span class="sxs-lookup"><span data-stu-id="e68a4-132">Confirm that the *web.config* file is present in the deployment and that its contents are correct.</span></span>
   * <span data-ttu-id="e68a4-133">Při hostování na Azure App Service potvrďte, že je aplikace nasazená do `D:\home\site\wwwroot` složky.</span><span class="sxs-lookup"><span data-stu-id="e68a4-133">When hosting on Azure App Service, confirm that the app is deployed to the `D:\home\site\wwwroot` folder.</span></span>
   * <span data-ttu-id="e68a4-134">Když je aplikace hostovaná službou IIS, zkontrolujte, že je aplikace nasazená na **fyzickou cestu** služby IIS, která se zobrazuje v **základním nastavení** **Správce služby IIS**.</span><span class="sxs-lookup"><span data-stu-id="e68a4-134">When the app is hosted by IIS, confirm that the app is deployed to the IIS **Physical path** shown in **IIS Manager**'s **Basic Settings**.</span></span>
1. <span data-ttu-id="e68a4-135">Ověřte, zda jsou všechny soubory a složky aplikace nasazeny porovnáním nasazení v hostitelském systému s obsahem složky *publikování* projektu.</span><span class="sxs-lookup"><span data-stu-id="e68a4-135">Confirm that all of the app's files and folders are deployed by comparing the deployment on the hosting system to the contents of the project's *publish* folder.</span></span>

<span data-ttu-id="e68a4-136">Další informace o rozložení publikované aplikace ASP.NET Core najdete v tématu <xref:host-and-deploy/directory-structure>.</span><span class="sxs-lookup"><span data-stu-id="e68a4-136">For more information on the layout of a published ASP.NET Core app, see <xref:host-and-deploy/directory-structure>.</span></span> <span data-ttu-id="e68a4-137">Další informace o souboru *Web. config* naleznete v tématu <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>.</span><span class="sxs-lookup"><span data-stu-id="e68a4-137">For more information on the *web.config* file, see <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>.</span></span>

### <a name="500-internal-server-error"></a><span data-ttu-id="e68a4-138">Chyba 500 interní Server</span><span class="sxs-lookup"><span data-stu-id="e68a4-138">500 Internal Server Error</span></span>

<span data-ttu-id="e68a4-139">Spuštění aplikace, ale chybu brání splnění žádosti. na serveru.</span><span class="sxs-lookup"><span data-stu-id="e68a4-139">The app starts, but an error prevents the server from fulfilling the request.</span></span>

<span data-ttu-id="e68a4-140">Při spuštění nebo při vytváření odpovědi, k této chybě dochází v kódu aplikace.</span><span class="sxs-lookup"><span data-stu-id="e68a4-140">This error occurs within the app's code during startup or while creating a response.</span></span> <span data-ttu-id="e68a4-141">Odpověď možná neobsahuje žádný obsah, nebo se odpověď může zobrazit jako *500 interní chyba serveru* v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="e68a4-141">The response may contain no content, or the response may appear as a *500 Internal Server Error* in the browser.</span></span> <span data-ttu-id="e68a4-142">V protokolu událostí aplikace obvykle hlásí, že aplikace se normálně spustit.</span><span class="sxs-lookup"><span data-stu-id="e68a4-142">The Application Event Log usually states that the app started normally.</span></span> <span data-ttu-id="e68a4-143">Z pohledu serveru, který je správný.</span><span class="sxs-lookup"><span data-stu-id="e68a4-143">From the server's perspective, that's correct.</span></span> <span data-ttu-id="e68a4-144">Aplikace začal, ale nemůže generovat platnou odpověď.</span><span class="sxs-lookup"><span data-stu-id="e68a4-144">The app did start, but it can't generate a valid response.</span></span> <span data-ttu-id="e68a4-145">Spusťte aplikaci na příkazovém řádku na serveru nebo povolte řešení potíží pomocí protokolu stdout modulu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e68a4-145">Run the app at a command prompt on the server or enable the ASP.NET Core Module stdout log to troubleshoot the problem.</span></span>

::: moniker range="= aspnetcore-2.2"

### <a name="5000-in-process-handler-load-failure"></a><span data-ttu-id="e68a4-146">500.0 v procesu selhání načtení obslužné rutiny</span><span class="sxs-lookup"><span data-stu-id="e68a4-146">500.0 In-Process Handler Load Failure</span></span>

<span data-ttu-id="e68a4-147">Pracovní proces se nezdaří.</span><span class="sxs-lookup"><span data-stu-id="e68a4-147">The worker process fails.</span></span> <span data-ttu-id="e68a4-148">Aplikace se nespustí.</span><span class="sxs-lookup"><span data-stu-id="e68a4-148">The app doesn't start.</span></span>

<span data-ttu-id="e68a4-149">[Modulu ASP.NET Core](xref:host-and-deploy/aspnet-core-module) se nepovedlo najít modul CLR .NET Core a najít obslužnou rutinu žádosti v procesu (*aspnetcorev2_inprocess. dll*).</span><span class="sxs-lookup"><span data-stu-id="e68a4-149">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) fails to find the .NET Core CLR and find the in-process request handler (*aspnetcorev2_inprocess.dll*).</span></span> <span data-ttu-id="e68a4-150">Zkontrolujte, jestli:</span><span class="sxs-lookup"><span data-stu-id="e68a4-150">Check that:</span></span>

* <span data-ttu-id="e68a4-151">Aplikace cílí na balíček NuGet [Microsoft. AspNetCore. Server. IIS](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IIS) nebo na [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="e68a4-151">The app targets either the [Microsoft.AspNetCore.Server.IIS](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IIS) NuGet package or the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>
* <span data-ttu-id="e68a4-152">Verze rozhraní framework ASP.NET Core sdílené cíle, které aplikace nainstalované v cílovém počítači.</span><span class="sxs-lookup"><span data-stu-id="e68a4-152">The version of the ASP.NET Core shared framework that the app targets is installed on the target machine.</span></span>

### <a name="5000-out-of-process-handler-load-failure"></a><span data-ttu-id="e68a4-153">500.0 Chyba načtení out-Of-Process obslužné rutiny</span><span class="sxs-lookup"><span data-stu-id="e68a4-153">500.0 Out-Of-Process Handler Load Failure</span></span>

<span data-ttu-id="e68a4-154">Pracovní proces se nezdaří.</span><span class="sxs-lookup"><span data-stu-id="e68a4-154">The worker process fails.</span></span> <span data-ttu-id="e68a4-155">Aplikace se nespustí.</span><span class="sxs-lookup"><span data-stu-id="e68a4-155">The app doesn't start.</span></span>

<span data-ttu-id="e68a4-156">[Modulu ASP.NET Core](xref:host-and-deploy/aspnet-core-module) se nepodařilo najít obslužnou rutinu žádosti mimo proces hostování.</span><span class="sxs-lookup"><span data-stu-id="e68a4-156">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) fails to find the out-of-process hosting request handler.</span></span> <span data-ttu-id="e68a4-157">Ujistěte se, že *aspnetcorev2_outofprocess. dll* se nachází v podsložce vedle *aspnetcorev2. dll*.</span><span class="sxs-lookup"><span data-stu-id="e68a4-157">Make sure the *aspnetcorev2_outofprocess.dll* is present in a subfolder next to *aspnetcorev2.dll*.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="5000-in-process-handler-load-failure"></a><span data-ttu-id="e68a4-158">500.0 v procesu selhání načtení obslužné rutiny</span><span class="sxs-lookup"><span data-stu-id="e68a4-158">500.0 In-Process Handler Load Failure</span></span>

<span data-ttu-id="e68a4-159">Pracovní proces se nezdaří.</span><span class="sxs-lookup"><span data-stu-id="e68a4-159">The worker process fails.</span></span> <span data-ttu-id="e68a4-160">Aplikace se nespustí.</span><span class="sxs-lookup"><span data-stu-id="e68a4-160">The app doesn't start.</span></span>

<span data-ttu-id="e68a4-161">Při načítání komponent [modulu ASP.NET Core](xref:host-and-deploy/aspnet-core-module) došlo k neznámé chybě.</span><span class="sxs-lookup"><span data-stu-id="e68a4-161">An unknown error occurred loading [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) components.</span></span> <span data-ttu-id="e68a4-162">Proveďte jednu z následujících akcí:</span><span class="sxs-lookup"><span data-stu-id="e68a4-162">Take one of the following actions:</span></span>

* <span data-ttu-id="e68a4-163">Kontaktujte [Podpora Microsoftu](https://support.microsoft.com/oas/default.aspx?prid=15832) (vyberte **vývojářské nástroje** potom **ASP.NET Core**).</span><span class="sxs-lookup"><span data-stu-id="e68a4-163">Contact [Microsoft Support](https://support.microsoft.com/oas/default.aspx?prid=15832) (select **Developer Tools** then **ASP.NET Core**).</span></span>
* <span data-ttu-id="e68a4-164">Položte otázku na Stack Overflow.</span><span class="sxs-lookup"><span data-stu-id="e68a4-164">Ask a question on Stack Overflow.</span></span>
* <span data-ttu-id="e68a4-165">Vydejte problém do našeho [úložiště GitHub](https://github.com/aspnet/AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="e68a4-165">File an issue on our [GitHub repository](https://github.com/aspnet/AspNetCore).</span></span>

### <a name="50030-in-process-startup-failure"></a><span data-ttu-id="e68a4-166">500.30 v procesu selhání spuštění</span><span class="sxs-lookup"><span data-stu-id="e68a4-166">500.30 In-Process Startup Failure</span></span>

<span data-ttu-id="e68a4-167">Pracovní proces se nezdaří.</span><span class="sxs-lookup"><span data-stu-id="e68a4-167">The worker process fails.</span></span> <span data-ttu-id="e68a4-168">Aplikace se nespustí.</span><span class="sxs-lookup"><span data-stu-id="e68a4-168">The app doesn't start.</span></span>

<span data-ttu-id="e68a4-169">[Modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module) se pokusí spustit modul CLR .NET Core v procesu, ale jeho spuštění se nezdařilo.</span><span class="sxs-lookup"><span data-stu-id="e68a4-169">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the .NET Core CLR in-process, but it fails to start.</span></span> <span data-ttu-id="e68a4-170">Příčinou selhání spuštění procesu může být obvykle určení z položek protokolu událostí aplikace a protokolu stdout modulu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e68a4-170">The cause of a process startup failure can usually be determined from entries in the Application Event Log and the ASP.NET Core Module stdout log.</span></span>

<span data-ttu-id="e68a4-171">Běžné chyby je, že aplikace je špatně nakonfigurovaný. kvůli cílení na určitou verzi rozhraní framework sdílené ASP.NET Core, který není k dispozici.</span><span class="sxs-lookup"><span data-stu-id="e68a4-171">A common failure condition is the app is misconfigured due to targeting a version of the ASP.NET Core shared framework that isn't present.</span></span> <span data-ttu-id="e68a4-172">Zkontrolujte, jaké verze rozhraní framework ASP.NET Core sdílené jsou nainstalovány v cílovém počítači.</span><span class="sxs-lookup"><span data-stu-id="e68a4-172">Check which versions of the ASP.NET Core shared framework are installed on the target machine.</span></span>

### <a name="50031-ancm-failed-to-find-native-dependencies"></a><span data-ttu-id="e68a4-173">500,31 ANCM nepovedlo se najít nativní závislosti.</span><span class="sxs-lookup"><span data-stu-id="e68a4-173">500.31 ANCM Failed to Find Native Dependencies</span></span>

<span data-ttu-id="e68a4-174">Pracovní proces se nezdaří.</span><span class="sxs-lookup"><span data-stu-id="e68a4-174">The worker process fails.</span></span> <span data-ttu-id="e68a4-175">Aplikace se nespustí.</span><span class="sxs-lookup"><span data-stu-id="e68a4-175">The app doesn't start.</span></span>

<span data-ttu-id="e68a4-176">[Modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module) se pokusí spustit modul runtime .NET Core v procesu, ale jeho spuštění se nezdařilo.</span><span class="sxs-lookup"><span data-stu-id="e68a4-176">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the .NET Core runtime in-process, but it fails to start.</span></span> <span data-ttu-id="e68a4-177">Nejběžnější příčinou tohoto selhání při spuštění je, že modul runtime `Microsoft.NETCore.App` nebo `Microsoft.AspNetCore.App` není nainstalován.</span><span class="sxs-lookup"><span data-stu-id="e68a4-177">The most common cause of this startup failure is when the `Microsoft.NETCore.App` or `Microsoft.AspNetCore.App` runtime isn't installed.</span></span> <span data-ttu-id="e68a4-178">Pokud je aplikace nasazená na cílovou ASP.NET Core 3,0 a tato verze v počítači neexistuje, dojde k této chybě.</span><span class="sxs-lookup"><span data-stu-id="e68a4-178">If the app is deployed to target ASP.NET Core 3.0 and that version doesn't exist on the machine, this error occurs.</span></span> <span data-ttu-id="e68a4-179">Následuje příklad chybové zprávy:</span><span class="sxs-lookup"><span data-stu-id="e68a4-179">An example error message follows:</span></span>

```
The specified framework 'Microsoft.NETCore.App', version '3.0.0' was not found.
  - The following frameworks were found:
      2.2.1 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview5-27626-15 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27713-13 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27714-15 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27723-08 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
```

<span data-ttu-id="e68a4-180">Chybová zpráva obsahuje seznam všech nainstalovaných verzí rozhraní .NET Core a verze, kterou aplikace požaduje.</span><span class="sxs-lookup"><span data-stu-id="e68a4-180">The error message lists all the installed .NET Core versions and the version requested by the app.</span></span> <span data-ttu-id="e68a4-181">Chcete-li tuto chybu vyřešit, postupujte takto:</span><span class="sxs-lookup"><span data-stu-id="e68a4-181">To fix this error, either:</span></span>

* <span data-ttu-id="e68a4-182">Na počítač nainstalujte odpovídající verzi .NET Core.</span><span class="sxs-lookup"><span data-stu-id="e68a4-182">Install the appropriate version of .NET Core on the machine.</span></span>
* <span data-ttu-id="e68a4-183">Změňte aplikaci na cílovou verzi .NET Core, která je na počítači přítomná.</span><span class="sxs-lookup"><span data-stu-id="e68a4-183">Change the app to target a version of .NET Core that's present on the machine.</span></span>
* <span data-ttu-id="e68a4-184">Publikujte aplikaci jako samostatné [nasazení](/dotnet/core/deploying/#self-contained-deployments-scd).</span><span class="sxs-lookup"><span data-stu-id="e68a4-184">Publish the app as a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>

<span data-ttu-id="e68a4-185">Při spuštění ve vývoji (`ASPNETCORE_ENVIRONMENT` proměnná prostředí je nastavená na `Development`) se konkrétní chyba zapíše do odpovědi HTTP.</span><span class="sxs-lookup"><span data-stu-id="e68a4-185">When running in development (the `ASPNETCORE_ENVIRONMENT` environment variable is set to `Development`), the specific error is written to the HTTP response.</span></span> <span data-ttu-id="e68a4-186">Příčinu selhání spuštění procesu se nachází také v protokolu událostí aplikace.</span><span class="sxs-lookup"><span data-stu-id="e68a4-186">The cause of a process startup failure is also found in the Application Event Log.</span></span>

### <a name="50032-ancm-failed-to-load-dll"></a><span data-ttu-id="e68a4-187">500,32 ANCM načtení knihovny DLL se nezdařilo</span><span class="sxs-lookup"><span data-stu-id="e68a4-187">500.32 ANCM Failed to Load dll</span></span>

<span data-ttu-id="e68a4-188">Pracovní proces se nezdaří.</span><span class="sxs-lookup"><span data-stu-id="e68a4-188">The worker process fails.</span></span> <span data-ttu-id="e68a4-189">Aplikace se nespustí.</span><span class="sxs-lookup"><span data-stu-id="e68a4-189">The app doesn't start.</span></span>

<span data-ttu-id="e68a4-190">Nejběžnější příčinou této chyby je, že aplikace je publikovaná pro nekompatibilní architekturu procesoru.</span><span class="sxs-lookup"><span data-stu-id="e68a4-190">The most common cause for this error is that the app is published for an incompatible processor architecture.</span></span> <span data-ttu-id="e68a4-191">Pokud je pracovní proces spuštěn jako 32 aplikace a aplikace byla publikována na cílovou 64-bit, dojde k této chybě.</span><span class="sxs-lookup"><span data-stu-id="e68a4-191">If the worker process is running as a 32-bit app and the app was published to target 64-bit, this error occurs.</span></span>

<span data-ttu-id="e68a4-192">Chcete-li tuto chybu vyřešit, postupujte takto:</span><span class="sxs-lookup"><span data-stu-id="e68a4-192">To fix this error, either:</span></span>

* <span data-ttu-id="e68a4-193">Znovu publikujte aplikaci pro stejnou architekturu procesoru jako pracovní proces.</span><span class="sxs-lookup"><span data-stu-id="e68a4-193">Republish the app for the same processor architecture as the worker process.</span></span>
* <span data-ttu-id="e68a4-194">Publikování aplikace jako [nasazení závislého na rozhraní](/dotnet/core/deploying/#framework-dependent-executables-fde).</span><span class="sxs-lookup"><span data-stu-id="e68a4-194">Publish the app as a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-executables-fde).</span></span>

### <a name="50033-ancm-request-handler-load-failure"></a><span data-ttu-id="e68a4-195">500,33 selhání načtení obslužné rutiny žádosti ANCM</span><span class="sxs-lookup"><span data-stu-id="e68a4-195">500.33 ANCM Request Handler Load Failure</span></span>

<span data-ttu-id="e68a4-196">Pracovní proces se nezdaří.</span><span class="sxs-lookup"><span data-stu-id="e68a4-196">The worker process fails.</span></span> <span data-ttu-id="e68a4-197">Aplikace se nespustí.</span><span class="sxs-lookup"><span data-stu-id="e68a4-197">The app doesn't start.</span></span>

<span data-ttu-id="e68a4-198">Aplikace neodkazovala na rozhraní `Microsoft.AspNetCore.App` Framework.</span><span class="sxs-lookup"><span data-stu-id="e68a4-198">The app didn't reference the `Microsoft.AspNetCore.App` framework.</span></span> <span data-ttu-id="e68a4-199">[Modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module)může hostovat jenom aplikace cílené na rozhraní `Microsoft.AspNetCore.App` Framework.</span><span class="sxs-lookup"><span data-stu-id="e68a4-199">Only apps targeting the `Microsoft.AspNetCore.App` framework can be hosted by the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span>

<span data-ttu-id="e68a4-200">Chcete-li tuto chybu opravit, zkontrolujte, zda je aplikace cílena na rozhraní `Microsoft.AspNetCore.App` Framework.</span><span class="sxs-lookup"><span data-stu-id="e68a4-200">To fix this error, confirm that the app is targeting the `Microsoft.AspNetCore.App` framework.</span></span> <span data-ttu-id="e68a4-201">Zkontrolujte `.runtimeconfig.json` a ověřte rozhraní, které cílí na aplikaci.</span><span class="sxs-lookup"><span data-stu-id="e68a4-201">Check the `.runtimeconfig.json` to verify the framework targeted by the app.</span></span>

### <a name="50034-ancm-mixed-hosting-models-not-supported"></a><span data-ttu-id="e68a4-202">500,34 ANCM smíšené modely hostování se nepodporují.</span><span class="sxs-lookup"><span data-stu-id="e68a4-202">500.34 ANCM Mixed Hosting Models Not Supported</span></span>

<span data-ttu-id="e68a4-203">Pracovní proces nemůže v jednom procesu spustit jak aplikaci v rámci procesu, tak i aplikaci mimo proces.</span><span class="sxs-lookup"><span data-stu-id="e68a4-203">The worker process can't run both an in-process app and an out-of-process app in the same process.</span></span>

<span data-ttu-id="e68a4-204">Chcete-li tuto chybu opravit, spusťte aplikace v samostatných fondech aplikací služby IIS.</span><span class="sxs-lookup"><span data-stu-id="e68a4-204">To fix this error, run apps in separate IIS application pools.</span></span>

### <a name="50035-ancm-multiple-in-process-applications-in-same-process"></a><span data-ttu-id="e68a4-205">500,35 ANCM více vnitroprocesové aplikací ve stejném procesu</span><span class="sxs-lookup"><span data-stu-id="e68a4-205">500.35 ANCM Multiple In-Process Applications in same Process</span></span>

<span data-ttu-id="e68a4-206">Pracovní proces nemůže v jednom procesu spustit více vnitroprocesové aplikací.</span><span class="sxs-lookup"><span data-stu-id="e68a4-206">The worker process can't run multiple in-process apps in the same process.</span></span>

<span data-ttu-id="e68a4-207">Chcete-li tuto chybu opravit, spusťte aplikace v samostatných fondech aplikací služby IIS.</span><span class="sxs-lookup"><span data-stu-id="e68a4-207">To fix this error, run apps in separate IIS application pools.</span></span>

### <a name="50036-ancm-out-of-process-handler-load-failure"></a><span data-ttu-id="e68a4-208">500,36 ANCM selhání načtení obslužné rutiny mimo proces</span><span class="sxs-lookup"><span data-stu-id="e68a4-208">500.36 ANCM Out-Of-Process Handler Load Failure</span></span>

<span data-ttu-id="e68a4-209">Obslužná rutina žádosti mimo proces, *aspnetcorev2_outofprocess. dll*, není vedle souboru *aspnetcorev2. dll* .</span><span class="sxs-lookup"><span data-stu-id="e68a4-209">The out-of-process request handler, *aspnetcorev2_outofprocess.dll*, isn't next to the *aspnetcorev2.dll* file.</span></span> <span data-ttu-id="e68a4-210">To označuje poškozenou instalaci [modulu ASP.NET Core](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="e68a4-210">This indicates a corrupted installation of the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span>

<span data-ttu-id="e68a4-211">Chcete-li tuto chybu opravit, opravte instalaci [hostující sady .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) (pro IIS) nebo Visual Studio (pro IIS Express).</span><span class="sxs-lookup"><span data-stu-id="e68a4-211">To fix this error, repair the installation of the [.NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) (for IIS) or Visual Studio (for IIS Express).</span></span>

### <a name="50037-ancm-failed-to-start-within-startup-time-limit"></a><span data-ttu-id="e68a4-212">500,37 ANCM se nepodařilo spustit v časovém limitu spuštění.</span><span class="sxs-lookup"><span data-stu-id="e68a4-212">500.37 ANCM Failed to Start Within Startup Time Limit</span></span>

<span data-ttu-id="e68a4-213">ANCM se nepovedlo spustit v rámci časového limitu spuštění nechte.</span><span class="sxs-lookup"><span data-stu-id="e68a4-213">ANCM failed to start within the provied startup time limit.</span></span> <span data-ttu-id="e68a4-214">Ve výchozím nastavení je časový limit 120 sekund.</span><span class="sxs-lookup"><span data-stu-id="e68a4-214">By default, the timeout is 120 seconds.</span></span>

<span data-ttu-id="e68a4-215">K této chybě může dojít, když ve stejném počítači spustíte velký počet aplikací.</span><span class="sxs-lookup"><span data-stu-id="e68a4-215">This error can occur when starting a large number of apps on the same machine.</span></span> <span data-ttu-id="e68a4-216">Kontrolovat špičky využití procesoru a paměti na serveru při spuštění.</span><span class="sxs-lookup"><span data-stu-id="e68a4-216">Check for CPU/Memory usage spikes on the server during startup.</span></span> <span data-ttu-id="e68a4-217">Možná budete muset rozložit proces spouštění více aplikací.</span><span class="sxs-lookup"><span data-stu-id="e68a4-217">You may need to stagger the startup process of multiple apps.</span></span>

::: moniker-end

### <a name="5025-process-failure"></a><span data-ttu-id="e68a4-218">502.5 zpracovat selhání</span><span class="sxs-lookup"><span data-stu-id="e68a4-218">502.5 Process Failure</span></span>

<span data-ttu-id="e68a4-219">Pracovní proces se nezdaří.</span><span class="sxs-lookup"><span data-stu-id="e68a4-219">The worker process fails.</span></span> <span data-ttu-id="e68a4-220">Aplikace se nespustí.</span><span class="sxs-lookup"><span data-stu-id="e68a4-220">The app doesn't start.</span></span>

<span data-ttu-id="e68a4-221">[Modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module) se pokusí spustit pracovní proces, ale jeho spuštění se nezdařilo.</span><span class="sxs-lookup"><span data-stu-id="e68a4-221">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the worker process but it fails to start.</span></span> <span data-ttu-id="e68a4-222">Příčinou selhání spuštění procesu může být obvykle určení z položek protokolu událostí aplikace a protokolu stdout modulu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e68a4-222">The cause of a process startup failure can usually be determined from entries in the Application Event Log and the ASP.NET Core Module stdout log.</span></span>

<span data-ttu-id="e68a4-223">Běžné chyby je, že aplikace je špatně nakonfigurovaný. kvůli cílení na určitou verzi rozhraní framework sdílené ASP.NET Core, který není k dispozici.</span><span class="sxs-lookup"><span data-stu-id="e68a4-223">A common failure condition is the app is misconfigured due to targeting a version of the ASP.NET Core shared framework that isn't present.</span></span> <span data-ttu-id="e68a4-224">Zkontrolujte, jaké verze rozhraní framework ASP.NET Core sdílené jsou nainstalovány v cílovém počítači.</span><span class="sxs-lookup"><span data-stu-id="e68a4-224">Check which versions of the ASP.NET Core shared framework are installed on the target machine.</span></span> <span data-ttu-id="e68a4-225">*Sdílené rozhraní* je sada sestavení (soubory *. dll* ), které jsou nainstalovány na počítači a na které odkazuje Metapackage jako `Microsoft.AspNetCore.App`.</span><span class="sxs-lookup"><span data-stu-id="e68a4-225">The *shared framework* is the set of assemblies (*.dll* files) that are installed on the machine and referenced by a metapackage such as `Microsoft.AspNetCore.App`.</span></span> <span data-ttu-id="e68a4-226">Odkaz Metapackage může určovat minimální požadovanou verzi.</span><span class="sxs-lookup"><span data-stu-id="e68a4-226">The metapackage reference can specify a minimum required version.</span></span> <span data-ttu-id="e68a4-227">Další informace najdete v tématu [sdílené rozhraní](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span><span class="sxs-lookup"><span data-stu-id="e68a4-227">For more information, see [The shared framework](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span></span>

<span data-ttu-id="e68a4-228">Chybová stránka *selhání procesu 502,5* se vrátí v případě, že při selhání hostování nebo aplikace dojde k chybě pracovního procesu:</span><span class="sxs-lookup"><span data-stu-id="e68a4-228">The *502.5 Process Failure* error page is returned when a hosting or app misconfiguration causes the worker process to fail:</span></span>

### <a name="failed-to-start-application-errorcode-0x800700c1"></a><span data-ttu-id="e68a4-229">Nepovedlo se spustit aplikaci (kód chyby "0x800700c1")</span><span class="sxs-lookup"><span data-stu-id="e68a4-229">Failed to start application (ErrorCode '0x800700c1')</span></span>

```
EventID: 1010
Source: IIS AspNetCore Module V2
Failed to start application '/LM/W3SVC/6/ROOT/', ErrorCode '0x800700c1'.
```

<span data-ttu-id="e68a4-230">Aplikaci se nepovedlo spustit, protože se nepovedlo načíst sestavení aplikace ( *. dll*).</span><span class="sxs-lookup"><span data-stu-id="e68a4-230">The app failed to start because the app's assembly (*.dll*) couldn't be loaded.</span></span>

<span data-ttu-id="e68a4-231">Tato chyba nastane, pokud došlo k neshodě bitové verze mezi publikované aplikace a proces w3wp/iisexpress.</span><span class="sxs-lookup"><span data-stu-id="e68a4-231">This error occurs when there's a bitness mismatch between the published app and the w3wp/iisexpress process.</span></span>

<span data-ttu-id="e68a4-232">Ověřte správnost nastavení 32-bit fondu aplikací:</span><span class="sxs-lookup"><span data-stu-id="e68a4-232">Confirm that the app pool's 32-bit setting is correct:</span></span>

1. <span data-ttu-id="e68a4-233">Vyberte fond aplikací v **fondech aplikací**Správce služby IIS.</span><span class="sxs-lookup"><span data-stu-id="e68a4-233">Select the app pool in IIS Manager's **Application Pools**.</span></span>
1. <span data-ttu-id="e68a4-234">V části **Upravit fond aplikací** na panelu **Akce** vyberte **Upřesnit nastavení** .</span><span class="sxs-lookup"><span data-stu-id="e68a4-234">Select **Advanced Settings** under **Edit Application Pool** in the **Actions** panel.</span></span>
1. <span data-ttu-id="e68a4-235">Nastavte **možnost povolit 32 – bitové aplikace**:</span><span class="sxs-lookup"><span data-stu-id="e68a4-235">Set **Enable 32-Bit Applications**:</span></span>
   * <span data-ttu-id="e68a4-236">Pokud nasazujete 32 (x86) aplikaci, nastavte hodnotu na `True`.</span><span class="sxs-lookup"><span data-stu-id="e68a4-236">If deploying a 32-bit (x86) app, set the value to `True`.</span></span>
   * <span data-ttu-id="e68a4-237">Pokud nasazujete 64 aplikaci (x64), nastavte hodnotu na `False`.</span><span class="sxs-lookup"><span data-stu-id="e68a4-237">If deploying a 64-bit (x64) app, set the value to `False`.</span></span>

<span data-ttu-id="e68a4-238">Ověřte, že mezi vlastností `<Platform>` MSBuild v souboru projektu a publikovaným bitová verzeem aplikace nedochází ke konfliktu.</span><span class="sxs-lookup"><span data-stu-id="e68a4-238">Confirm that there isn't a conflict between a `<Platform>` MSBuild property in the project file and the published bitness of the app.</span></span>

### <a name="connection-reset"></a><span data-ttu-id="e68a4-239">Obnovení připojení</span><span class="sxs-lookup"><span data-stu-id="e68a4-239">Connection reset</span></span>

<span data-ttu-id="e68a4-240">Pokud dojde k chybě po odeslání hlaviček, je příliš pozdě pro server, který odešle **500 interní chybu serveru** , když dojde k chybě.</span><span class="sxs-lookup"><span data-stu-id="e68a4-240">If an error occurs after the headers are sent, it's too late for the server to send a **500 Internal Server Error** when an error occurs.</span></span> <span data-ttu-id="e68a4-241">Často se to stane, když dojde k chybě při serializaci složitých objektů pro odpověď.</span><span class="sxs-lookup"><span data-stu-id="e68a4-241">This often happens when an error occurs during the serialization of complex objects for a response.</span></span> <span data-ttu-id="e68a4-242">Tento typ chyby se zobrazí jako chyba *resetování připojení* na klientovi.</span><span class="sxs-lookup"><span data-stu-id="e68a4-242">This type of error appears as a *connection reset* error on the client.</span></span> <span data-ttu-id="e68a4-243">[Protokolování aplikace](xref:fundamentals/logging/index) může pomoct řešit tyto typy chyb.</span><span class="sxs-lookup"><span data-stu-id="e68a4-243">[Application logging](xref:fundamentals/logging/index) can help troubleshoot these types of errors.</span></span>

### <a name="default-startup-limits"></a><span data-ttu-id="e68a4-244">Výchozí omezení při spuštění</span><span class="sxs-lookup"><span data-stu-id="e68a4-244">Default startup limits</span></span>

<span data-ttu-id="e68a4-245">[ASP.NET Core modul](xref:host-and-deploy/aspnet-core-module) je nakonfigurovaný s výchozí *startupTimeLimitou* 120 sekund.</span><span class="sxs-lookup"><span data-stu-id="e68a4-245">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) is configured with a default *startupTimeLimit* of 120 seconds.</span></span> <span data-ttu-id="e68a4-246">Když necháte na výchozí hodnotu, aplikace může trvat až dvě minuty, spusťte před modulu protokoly selhání procesu.</span><span class="sxs-lookup"><span data-stu-id="e68a4-246">When left at the default value, an app may take up to two minutes to start before the module logs a process failure.</span></span> <span data-ttu-id="e68a4-247">Informace o konfiguraci modulu naleznete v tématu [atributy elementu aspNetCore](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span><span class="sxs-lookup"><span data-stu-id="e68a4-247">For information on configuring the module, see [Attributes of the aspNetCore element](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span></span>

## <a name="troubleshoot-on-azure-app-service"></a><span data-ttu-id="e68a4-248">Řešení potíží na Azure App Service</span><span class="sxs-lookup"><span data-stu-id="e68a4-248">Troubleshoot on Azure App Service</span></span>

[!INCLUDE [Azure App Service Preview Notice](~/includes/azure-apps-preview-notice.md)]

### <a name="application-event-log-azure-app-service"></a><span data-ttu-id="e68a4-249">Protokol událostí aplikace (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="e68a4-249">Application Event Log (Azure App Service)</span></span>

<span data-ttu-id="e68a4-250">Chcete-li získat přístup k protokolu událostí aplikace, použijte okno **Diagnostika a řešení problémů** v Azure Portal:</span><span class="sxs-lookup"><span data-stu-id="e68a4-250">To access the Application Event Log, use the **Diagnose and solve problems** blade in the Azure portal:</span></span>

1. <span data-ttu-id="e68a4-251">V Azure Portal otevřete aplikaci v **App Services**.</span><span class="sxs-lookup"><span data-stu-id="e68a4-251">In the Azure portal, open the app in **App Services**.</span></span>
1. <span data-ttu-id="e68a4-252">Vyberte možnost **diagnostikovat a vyřešit problémy**.</span><span class="sxs-lookup"><span data-stu-id="e68a4-252">Select **Diagnose and solve problems**.</span></span>
1. <span data-ttu-id="e68a4-253">Vyberte **diagnostické nástroje** záhlaví.</span><span class="sxs-lookup"><span data-stu-id="e68a4-253">Select the **Diagnostic Tools** heading.</span></span>
1. <span data-ttu-id="e68a4-254">V nabídce **nástroje podpory**vyberte tlačítko **události aplikace** .</span><span class="sxs-lookup"><span data-stu-id="e68a4-254">Under **Support Tools**, select the **Application Events** button.</span></span>
1. <span data-ttu-id="e68a4-255">Projděte si nejnovější chybu, kterou poskytla položka *IIS AspNetCoreModule* nebo *IIS AspNetCoreModule v2* ve **zdrojovém** sloupci.</span><span class="sxs-lookup"><span data-stu-id="e68a4-255">Examine the latest error provided by the *IIS AspNetCoreModule* or *IIS AspNetCoreModule V2* entry in the **Source** column.</span></span>

<span data-ttu-id="e68a4-256">Alternativou k použití okna **diagnostikovat a řešit problémy** je kontrola souboru protokolu událostí aplikace přímo pomocí [Kudu](https://github.com/projectkudu/kudu/wiki):</span><span class="sxs-lookup"><span data-stu-id="e68a4-256">An alternative to using the **Diagnose and solve problems** blade is to examine the Application Event Log file directly using [Kudu](https://github.com/projectkudu/kudu/wiki):</span></span>

1. <span data-ttu-id="e68a4-257">Otevřete **Rozšířené nástroje** v oblasti **vývojové nástroje** .</span><span class="sxs-lookup"><span data-stu-id="e68a4-257">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="e68a4-258">Vyberte tlačítko **přejít&rarr;** .</span><span class="sxs-lookup"><span data-stu-id="e68a4-258">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="e68a4-259">Konzola Kudu se otevře v novém okně nebo záložce prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="e68a4-259">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="e68a4-260">Pomocí navigačního panelu v horní části stránky otevřete **konzolu ladění** a vyberte **cmd**.</span><span class="sxs-lookup"><span data-stu-id="e68a4-260">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="e68a4-261">Otevřete složku **soubory protokolů** .</span><span class="sxs-lookup"><span data-stu-id="e68a4-261">Open the **LogFiles** folder.</span></span>
1. <span data-ttu-id="e68a4-262">Vyberte ikonu tužky vedle souboru *EventLog. XML* .</span><span class="sxs-lookup"><span data-stu-id="e68a4-262">Select the pencil icon next to the *eventlog.xml* file.</span></span>
1. <span data-ttu-id="e68a4-263">Projděte si protokol.</span><span class="sxs-lookup"><span data-stu-id="e68a4-263">Examine the log.</span></span> <span data-ttu-id="e68a4-264">Posuňte se do dolní části protokolu, abyste viděli nejaktuálnější události.</span><span class="sxs-lookup"><span data-stu-id="e68a4-264">Scroll to the bottom of the log to see the most recent events.</span></span>

### <a name="run-the-app-in-the-kudu-console"></a><span data-ttu-id="e68a4-265">Spuštění aplikace v konzole Kudu</span><span class="sxs-lookup"><span data-stu-id="e68a4-265">Run the app in the Kudu console</span></span>

<span data-ttu-id="e68a4-266">Mnoho chyb při spuštění nevytvářejí užitečné informace v protokolu událostí aplikace.</span><span class="sxs-lookup"><span data-stu-id="e68a4-266">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="e68a4-267">Tuto chybu můžete zjistit spuštěním aplikace v konzole vzdáleného spuštění [Kudu](https://github.com/projectkudu/kudu/wiki) :</span><span class="sxs-lookup"><span data-stu-id="e68a4-267">You can run the app in the [Kudu](https://github.com/projectkudu/kudu/wiki) Remote Execution Console to discover the error:</span></span>

1. <span data-ttu-id="e68a4-268">Otevřete **Rozšířené nástroje** v oblasti **vývojové nástroje** .</span><span class="sxs-lookup"><span data-stu-id="e68a4-268">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="e68a4-269">Vyberte tlačítko **přejít&rarr;** .</span><span class="sxs-lookup"><span data-stu-id="e68a4-269">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="e68a4-270">Konzola Kudu se otevře v novém okně nebo záložce prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="e68a4-270">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="e68a4-271">Pomocí navigačního panelu v horní části stránky otevřete **konzolu ladění** a vyberte **cmd**.</span><span class="sxs-lookup"><span data-stu-id="e68a4-271">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>

#### <a name="test-a-32-bit-x86-app"></a><span data-ttu-id="e68a4-272">Testování 32 (x86) aplikace</span><span class="sxs-lookup"><span data-stu-id="e68a4-272">Test a 32-bit (x86) app</span></span>

<span data-ttu-id="e68a4-273">**Aktuální verze**</span><span class="sxs-lookup"><span data-stu-id="e68a4-273">**Current release**</span></span>

1. `cd d:\home\site\wwwroot`
1. <span data-ttu-id="e68a4-274">Spusťte aplikaci:</span><span class="sxs-lookup"><span data-stu-id="e68a4-274">Run the app:</span></span>
   * <span data-ttu-id="e68a4-275">Pokud aplikace je [nasazení závislé na rozhraní](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span><span class="sxs-lookup"><span data-stu-id="e68a4-275">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

     ```dotnetcli
     dotnet .\{ASSEMBLY NAME}.dll
     ```

   * <span data-ttu-id="e68a4-276">Pokud je aplikace samostatná, [nasazení](/dotnet/core/deploying/#self-contained-deployments-scd):</span><span class="sxs-lookup"><span data-stu-id="e68a4-276">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

     ```console
     {ASSEMBLY NAME}.exe
     ```

<span data-ttu-id="e68a4-277">Výstup konzoly z aplikace, v němž se zobrazují všechny chyby, je kanálem do konzoly Kudu.</span><span class="sxs-lookup"><span data-stu-id="e68a4-277">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="e68a4-278">**Nasazení závislé na architektuře spuštěné ve verzi Preview**</span><span class="sxs-lookup"><span data-stu-id="e68a4-278">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="e68a4-279">*Vyžaduje instalaci rozšíření webu ASP.NET Core {VERSION} (x86) Runtime.*</span><span class="sxs-lookup"><span data-stu-id="e68a4-279">*Requires installing the ASP.NET Core {VERSION} (x86) Runtime site extension.*</span></span>

1. <span data-ttu-id="e68a4-280">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32` (`{X.Y}` je verze modulu runtime)</span><span class="sxs-lookup"><span data-stu-id="e68a4-280">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="e68a4-281">Spusťte aplikaci: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="e68a4-281">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="e68a4-282">Výstup konzoly z aplikace, v němž se zobrazují všechny chyby, je kanálem do konzoly Kudu.</span><span class="sxs-lookup"><span data-stu-id="e68a4-282">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

#### <a name="test-a-64-bit-x64-app"></a><span data-ttu-id="e68a4-283">Testování 64 aplikace (x64)</span><span class="sxs-lookup"><span data-stu-id="e68a4-283">Test a 64-bit (x64) app</span></span>

<span data-ttu-id="e68a4-284">**Aktuální verze**</span><span class="sxs-lookup"><span data-stu-id="e68a4-284">**Current release**</span></span>

* <span data-ttu-id="e68a4-285">Pokud je aplikace [nasazením závislého na rozhraní](/dotnet/core/deploying/#framework-dependent-deployments-fdd)64 (x64):</span><span class="sxs-lookup"><span data-stu-id="e68a4-285">If the app is a 64-bit (x64) [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>
  1. `cd D:\Program Files\dotnet`
  1. <span data-ttu-id="e68a4-286">Spusťte aplikaci: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="e68a4-286">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>
* <span data-ttu-id="e68a4-287">Pokud je aplikace samostatná, [nasazení](/dotnet/core/deploying/#self-contained-deployments-scd):</span><span class="sxs-lookup"><span data-stu-id="e68a4-287">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>
  1. `cd D:\home\site\wwwroot`
  1. <span data-ttu-id="e68a4-288">Spusťte aplikaci: `{ASSEMBLY NAME}.exe`</span><span class="sxs-lookup"><span data-stu-id="e68a4-288">Run the app: `{ASSEMBLY NAME}.exe`</span></span>

<span data-ttu-id="e68a4-289">Výstup konzoly z aplikace, v němž se zobrazují všechny chyby, je kanálem do konzoly Kudu.</span><span class="sxs-lookup"><span data-stu-id="e68a4-289">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="e68a4-290">**Nasazení závislé na architektuře spuštěné ve verzi Preview**</span><span class="sxs-lookup"><span data-stu-id="e68a4-290">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="e68a4-291">*Vyžaduje instalaci rozšíření webu ASP.NET Core {VERSION} (x64) Runtime.*</span><span class="sxs-lookup"><span data-stu-id="e68a4-291">*Requires installing the ASP.NET Core {VERSION} (x64) Runtime site extension.*</span></span>

1. <span data-ttu-id="e68a4-292">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64` (`{X.Y}` je verze modulu runtime)</span><span class="sxs-lookup"><span data-stu-id="e68a4-292">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="e68a4-293">Spusťte aplikaci: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="e68a4-293">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="e68a4-294">Výstup konzoly z aplikace, v němž se zobrazují všechny chyby, je kanálem do konzoly Kudu.</span><span class="sxs-lookup"><span data-stu-id="e68a4-294">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

### <a name="aspnet-core-module-stdout-log-azure-app-service"></a><span data-ttu-id="e68a4-295">Protokol stdout v modulu ASP.NET Core (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="e68a4-295">ASP.NET Core Module stdout log (Azure App Service)</span></span>

<span data-ttu-id="e68a4-296">Protokol stdout modulu ASP.NET Coree často zaznamenává užitečné chybové zprávy, které se nenašly v protokolu událostí aplikace.</span><span class="sxs-lookup"><span data-stu-id="e68a4-296">The ASP.NET Core Module stdout log often records useful error messages not found in the Application Event Log.</span></span> <span data-ttu-id="e68a4-297">Povolení a zobrazení protokolů stdout:</span><span class="sxs-lookup"><span data-stu-id="e68a4-297">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="e68a4-298">Přejděte do okna **diagnostikovat a řešit problémy** v Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="e68a4-298">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="e68a4-299">V části **Vybrat kategorii problému**vyberte tlačítko pro **webovou aplikaci** .</span><span class="sxs-lookup"><span data-stu-id="e68a4-299">Under **SELECT PROBLEM CATEGORY**, select the **Web App Down** button.</span></span>
1. <span data-ttu-id="e68a4-300">V části **navrhovaná řešení** > **Povolit přesměrování protokolu stdout**kliknutím na tlačítko **otevřete konzolu Kudu a upravte soubor Web. config**.</span><span class="sxs-lookup"><span data-stu-id="e68a4-300">Under **Suggested Solutions** > **Enable Stdout Log Redirection**, select the button to **Open Kudu Console to edit Web.Config**.</span></span>
1. <span data-ttu-id="e68a4-301">V **konzole diagnostiky**Kudu otevřete složky v **lokalitě** cesty > **wwwroot**.</span><span class="sxs-lookup"><span data-stu-id="e68a4-301">In the Kudu **Diagnostic Console**, open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="e68a4-302">Posuňte se dolů a odhalte soubor *Web. config* v dolní části seznamu.</span><span class="sxs-lookup"><span data-stu-id="e68a4-302">Scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="e68a4-303">Klikněte na ikonu tužky vedle souboru *Web. config* .</span><span class="sxs-lookup"><span data-stu-id="e68a4-303">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="e68a4-304">Nastavte **stdoutLogEnabled** na `true` a změňte cestu **stdoutLogFile** na: `\\?\%home%\LogFiles\stdout`.</span><span class="sxs-lookup"><span data-stu-id="e68a4-304">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="e68a4-305">Vyberte **Uložit** a uložte aktualizovaný soubor *Web. config* .</span><span class="sxs-lookup"><span data-stu-id="e68a4-305">Select **Save** to save the updated *web.config* file.</span></span>
1. <span data-ttu-id="e68a4-306">Vytvořte žádost do aplikace.</span><span class="sxs-lookup"><span data-stu-id="e68a4-306">Make a request to the app.</span></span>
1. <span data-ttu-id="e68a4-307">Vraťte se do Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="e68a4-307">Return to the Azure portal.</span></span> <span data-ttu-id="e68a4-308">V oblasti **vývojové nástroje** vyberte okno **Pokročilé nástroje** .</span><span class="sxs-lookup"><span data-stu-id="e68a4-308">Select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="e68a4-309">Vyberte tlačítko **přejít&rarr;** .</span><span class="sxs-lookup"><span data-stu-id="e68a4-309">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="e68a4-310">Konzola Kudu se otevře v novém okně nebo záložce prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="e68a4-310">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="e68a4-311">Pomocí navigačního panelu v horní části stránky otevřete **konzolu ladění** a vyberte **cmd**.</span><span class="sxs-lookup"><span data-stu-id="e68a4-311">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="e68a4-312">Vyberte složku **soubory protokolů** .</span><span class="sxs-lookup"><span data-stu-id="e68a4-312">Select the **LogFiles** folder.</span></span>
1. <span data-ttu-id="e68a4-313">Zkontrolujte **upravený** sloupec a vyberte ikonu tužky a upravte protokol stdout s nejnovějším datem úpravy.</span><span class="sxs-lookup"><span data-stu-id="e68a4-313">Inspect the **Modified** column and select the pencil icon to edit the stdout log with the latest modification date.</span></span>
1. <span data-ttu-id="e68a4-314">Po otevření souboru protokolu se zobrazí chyba.</span><span class="sxs-lookup"><span data-stu-id="e68a4-314">When the log file opens, the error is displayed.</span></span>

<span data-ttu-id="e68a4-315">Zakázat protokolování stdout při odstraňování potíží:</span><span class="sxs-lookup"><span data-stu-id="e68a4-315">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="e68a4-316">V **diagnostické konzole**Kudu se vraťte do **lokality** cesty > **wwwroot** , aby se soubor *Web. config* vykryl.</span><span class="sxs-lookup"><span data-stu-id="e68a4-316">In the Kudu **Diagnostic Console**, return to the path **site** > **wwwroot** to reveal the *web.config* file.</span></span> <span data-ttu-id="e68a4-317">Otevřete soubor **Web. config** znovu výběrem ikony tužky.</span><span class="sxs-lookup"><span data-stu-id="e68a4-317">Open the **web.config** file again by selecting the pencil icon.</span></span>
1. <span data-ttu-id="e68a4-318">Nastavte **stdoutLogEnabled** na `false`.</span><span class="sxs-lookup"><span data-stu-id="e68a4-318">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="e68a4-319">Vyberte **Uložit** a soubor uložte.</span><span class="sxs-lookup"><span data-stu-id="e68a4-319">Select **Save** to save the file.</span></span>

<span data-ttu-id="e68a4-320">Další informace najdete v tématu <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span><span class="sxs-lookup"><span data-stu-id="e68a4-320">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

> [!WARNING]
> <span data-ttu-id="e68a4-321">Nepodařilo se zakázat protokol stdout může vést k selhání aplikace nebo serveru.</span><span class="sxs-lookup"><span data-stu-id="e68a4-321">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="e68a4-322">Neexistuje žádné omezení velikosti souboru protokolu nebo počet souborů protokolů, které jsou vytvořeny.</span><span class="sxs-lookup"><span data-stu-id="e68a4-322">There's no limit on log file size or the number of log files created.</span></span> <span data-ttu-id="e68a4-323">K řešení problémů se spouštěním aplikací použijte pouze protokolování STDOUT.</span><span class="sxs-lookup"><span data-stu-id="e68a4-323">Only use stdout logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="e68a4-324">Pro obecné protokolování v aplikaci ASP.NET Core po spuštění použijte knihovnu protokolování, která omezuje velikost souboru protokolu a otočí protokoly.</span><span class="sxs-lookup"><span data-stu-id="e68a4-324">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="e68a4-325">Další informace najdete v tématu [Zprostředkovatelé protokolování třetích stran](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="e68a4-325">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

::: moniker range=">= aspnetcore-2.2"

### <a name="aspnet-core-module-debug-log-azure-app-service"></a><span data-ttu-id="e68a4-326">Protokol ladění modulu ASP.NET Core (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="e68a4-326">ASP.NET Core Module debug log (Azure App Service)</span></span>

<span data-ttu-id="e68a4-327">Protokol ladění ASP.NET Core modulu poskytuje další, hlubší protokolování z modulu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e68a4-327">The ASP.NET Core Module debug log provides additional, deeper logging from the ASP.NET Core Module.</span></span> <span data-ttu-id="e68a4-328">Povolení a zobrazení protokolů stdout:</span><span class="sxs-lookup"><span data-stu-id="e68a4-328">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="e68a4-329">Pro povolení rozšířeného diagnostického protokolu proveďte jednu z následujících akcí:</span><span class="sxs-lookup"><span data-stu-id="e68a4-329">To enable the enhanced diagnostic log, perform either of the following:</span></span>
   * <span data-ttu-id="e68a4-330">Postupujte podle pokynů v [rozšířených diagnostických protokolech](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) a nakonfigurujte aplikaci pro rozšířené diagnostické protokolování.</span><span class="sxs-lookup"><span data-stu-id="e68a4-330">Follow the instructions in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) to configure the app for an enhanced diagnostic logging.</span></span> <span data-ttu-id="e68a4-331">Znovu nasaďte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="e68a4-331">Redeploy the app.</span></span>
   * <span data-ttu-id="e68a4-332">Přidejte `<handlerSettings>` zobrazené v [rozšířených diagnostických protokolech](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) do souboru *Web. config* živé aplikace pomocí konzoly Kudu:</span><span class="sxs-lookup"><span data-stu-id="e68a4-332">Add the `<handlerSettings>` shown in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) to the live app's *web.config* file using the Kudu console:</span></span>
     1. <span data-ttu-id="e68a4-333">Otevřete **Rozšířené nástroje** v oblasti **vývojové nástroje** .</span><span class="sxs-lookup"><span data-stu-id="e68a4-333">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="e68a4-334">Vyberte tlačítko **přejít&rarr;** .</span><span class="sxs-lookup"><span data-stu-id="e68a4-334">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="e68a4-335">Konzola Kudu se otevře v novém okně nebo záložce prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="e68a4-335">The Kudu console opens in a new browser tab or window.</span></span>
     1. <span data-ttu-id="e68a4-336">Pomocí navigačního panelu v horní části stránky otevřete **konzolu ladění** a vyberte **cmd**.</span><span class="sxs-lookup"><span data-stu-id="e68a4-336">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
     1. <span data-ttu-id="e68a4-337">Otevřete složky v **lokalitě** pro cestu > **wwwroot**.</span><span class="sxs-lookup"><span data-stu-id="e68a4-337">Open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="e68a4-338">Upravte soubor *Web. config* tak, že vyberete tlačítko tužky.</span><span class="sxs-lookup"><span data-stu-id="e68a4-338">Edit the *web.config* file by selecting the pencil button.</span></span> <span data-ttu-id="e68a4-339">Přidejte část `<handlerSettings>`, jak je znázorněno v [rozšířených diagnostických protokolech](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs).</span><span class="sxs-lookup"><span data-stu-id="e68a4-339">Add the `<handlerSettings>` section as shown in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs).</span></span> <span data-ttu-id="e68a4-340">Vyberte tlačítko **Uložit** .</span><span class="sxs-lookup"><span data-stu-id="e68a4-340">Select the **Save** button.</span></span>
1. <span data-ttu-id="e68a4-341">Otevřete **Rozšířené nástroje** v oblasti **vývojové nástroje** .</span><span class="sxs-lookup"><span data-stu-id="e68a4-341">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="e68a4-342">Vyberte tlačítko **přejít&rarr;** .</span><span class="sxs-lookup"><span data-stu-id="e68a4-342">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="e68a4-343">Konzola Kudu se otevře v novém okně nebo záložce prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="e68a4-343">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="e68a4-344">Pomocí navigačního panelu v horní části stránky otevřete **konzolu ladění** a vyberte **cmd**.</span><span class="sxs-lookup"><span data-stu-id="e68a4-344">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="e68a4-345">Otevřete složky v **lokalitě** pro cestu > **wwwroot**.</span><span class="sxs-lookup"><span data-stu-id="e68a4-345">Open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="e68a4-346">Pokud jste nezadali cestu k souboru *aspnetcore-Debug. log* , soubor se zobrazí v seznamu.</span><span class="sxs-lookup"><span data-stu-id="e68a4-346">If you didn't supply a path for the *aspnetcore-debug.log* file, the file appears in the list.</span></span> <span data-ttu-id="e68a4-347">Pokud jste zadali cestu, přejděte do umístění souboru protokolu.</span><span class="sxs-lookup"><span data-stu-id="e68a4-347">If you supplied a path, navigate to the location of the log file.</span></span>
1. <span data-ttu-id="e68a4-348">Otevřete soubor protokolu s tlačítkem tužky vedle názvu souboru.</span><span class="sxs-lookup"><span data-stu-id="e68a4-348">Open the log file with the pencil button next to the file name.</span></span>

<span data-ttu-id="e68a4-349">Zakázat protokolování ladění, pokud je dokončeno odstraňování potíží:</span><span class="sxs-lookup"><span data-stu-id="e68a4-349">Disable debug logging when troubleshooting is complete:</span></span>

<span data-ttu-id="e68a4-350">Chcete-li zakázat rozšířený protokol ladění, proveďte jednu z následujících akcí:</span><span class="sxs-lookup"><span data-stu-id="e68a4-350">To disable the enhanced debug log, perform either of the following:</span></span>

* <span data-ttu-id="e68a4-351">Odeberte `<handlerSettings>` ze souboru *Web. config* místně a pak aplikaci znovu nasaďte.</span><span class="sxs-lookup"><span data-stu-id="e68a4-351">Remove the `<handlerSettings>` from the *web.config* file locally and redeploy the app.</span></span>
* <span data-ttu-id="e68a4-352">Pomocí konzoly Kudu upravte soubor *Web. config* a odeberte oddíl `<handlerSettings>`.</span><span class="sxs-lookup"><span data-stu-id="e68a4-352">Use the Kudu console to edit the *web.config* file and remove the `<handlerSettings>` section.</span></span> <span data-ttu-id="e68a4-353">Uložte soubor.</span><span class="sxs-lookup"><span data-stu-id="e68a4-353">Save the file.</span></span>

<span data-ttu-id="e68a4-354">Další informace najdete v tématu <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span><span class="sxs-lookup"><span data-stu-id="e68a4-354">For more information, see <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span></span>

> [!WARNING]
> <span data-ttu-id="e68a4-355">Nepovedlo se zakázat protokol ladění, může způsobit selhání aplikace nebo serveru.</span><span class="sxs-lookup"><span data-stu-id="e68a4-355">Failure to disable the debug log can lead to app or server failure.</span></span> <span data-ttu-id="e68a4-356">Velikost souboru protokolu není nijak omezena.</span><span class="sxs-lookup"><span data-stu-id="e68a4-356">There's no limit on log file size.</span></span> <span data-ttu-id="e68a4-357">K řešení problémů se spouštěním aplikace používejte pouze protokolování ladění.</span><span class="sxs-lookup"><span data-stu-id="e68a4-357">Only use debug logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="e68a4-358">Pro obecné protokolování v aplikaci ASP.NET Core po spuštění použijte knihovnu protokolování, která omezuje velikost souboru protokolu a otočí protokoly.</span><span class="sxs-lookup"><span data-stu-id="e68a4-358">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="e68a4-359">Další informace najdete v tématu [Zprostředkovatelé protokolování třetích stran](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="e68a4-359">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

::: moniker-end

### <a name="slow-or-hanging-app-azure-app-service"></a><span data-ttu-id="e68a4-360">Pomalá nebo zavěšená aplikace (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="e68a4-360">Slow or hanging app (Azure App Service)</span></span>

<span data-ttu-id="e68a4-361">Pokud aplikace reaguje pomalu nebo přestane reagovat na žádost, přečtěte si následující články:</span><span class="sxs-lookup"><span data-stu-id="e68a4-361">When an app responds slowly or hangs on a request, see the following articles:</span></span>

* [<span data-ttu-id="e68a4-362">Řešení potíží s výkonem pomalých webových aplikací v Azure App Service</span><span class="sxs-lookup"><span data-stu-id="e68a4-362">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="e68a4-363">K zachycení výpisu problémů s přerušovanou výjimkou nebo problémy s výkonem ve webové aplikaci Azure použijte rozšíření pro diagnostiku zhroucení serveru.</span><span class="sxs-lookup"><span data-stu-id="e68a4-363">Use Crash Diagnoser Site Extension to Capture Dump for Intermittent Exception issues or performance issues on Azure Web App</span></span>](https://blogs.msdn.microsoft.com/asiatech/2015/12/28/use-crash-diagnoser-site-extension-to-capture-dump-for-intermittent-exception-issues-or-performance-issues-on-azure-web-app/)

### <a name="monitoring-blades"></a><span data-ttu-id="e68a4-364">Okna monitorování</span><span class="sxs-lookup"><span data-stu-id="e68a4-364">Monitoring blades</span></span>

<span data-ttu-id="e68a4-365">Okna monitorování poskytují alternativní možnosti řešení potíží pro metody popsané dříve v tématu.</span><span class="sxs-lookup"><span data-stu-id="e68a4-365">Monitoring blades provide an alternative troubleshooting experience to the methods described earlier in the topic.</span></span> <span data-ttu-id="e68a4-366">Pomocí těchto oken můžete diagnostikovat chyby 500-Series.</span><span class="sxs-lookup"><span data-stu-id="e68a4-366">These blades can be used to diagnose 500-series errors.</span></span>

<span data-ttu-id="e68a4-367">Potvrďte, že jsou nainstalovaná rozšíření ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e68a4-367">Confirm that the ASP.NET Core Extensions are installed.</span></span> <span data-ttu-id="e68a4-368">Pokud nejsou rozšíření nainstalována, nainstalujte je ručně:</span><span class="sxs-lookup"><span data-stu-id="e68a4-368">If the extensions aren't installed, install them manually:</span></span>

1. <span data-ttu-id="e68a4-369">V části okno **vývojové nástroje** vyberte okno **rozšíření** .</span><span class="sxs-lookup"><span data-stu-id="e68a4-369">In the **DEVELOPMENT TOOLS** blade section, select the **Extensions** blade.</span></span>
1. <span data-ttu-id="e68a4-370">V seznamu by se měla zobrazit **rozšíření ASP.NET Core** .</span><span class="sxs-lookup"><span data-stu-id="e68a4-370">The **ASP.NET Core Extensions** should appear in the list.</span></span>
1. <span data-ttu-id="e68a4-371">Pokud nejsou rozšíření nainstalovaná, vyberte tlačítko **Přidat** .</span><span class="sxs-lookup"><span data-stu-id="e68a4-371">If the extensions aren't installed, select the **Add** button.</span></span>
1. <span data-ttu-id="e68a4-372">Ze seznamu vyberte **rozšíření ASP.NET Core** .</span><span class="sxs-lookup"><span data-stu-id="e68a4-372">Choose the **ASP.NET Core Extensions** from the list.</span></span>
1. <span data-ttu-id="e68a4-373">Kliknutím na **OK** přijměte právní podmínky.</span><span class="sxs-lookup"><span data-stu-id="e68a4-373">Select **OK** to accept the legal terms.</span></span>
1. <span data-ttu-id="e68a4-374">V okně **Přidat rozšíření** vyberte **OK** .</span><span class="sxs-lookup"><span data-stu-id="e68a4-374">Select **OK** on the **Add extension** blade.</span></span>
1. <span data-ttu-id="e68a4-375">Informační automaticky otevíraná zpráva indikuje, že se rozšíření úspěšně nainstalovala.</span><span class="sxs-lookup"><span data-stu-id="e68a4-375">An informational pop-up message indicates when the extensions are successfully installed.</span></span>

<span data-ttu-id="e68a4-376">Pokud není povoleno protokolování stdout, postupujte následovně:</span><span class="sxs-lookup"><span data-stu-id="e68a4-376">If stdout logging isn't enabled, follow these steps:</span></span>

1. <span data-ttu-id="e68a4-377">V Azure Portal v oblasti **vývojové nástroje** vyberte okno **Pokročilé nástroje** .</span><span class="sxs-lookup"><span data-stu-id="e68a4-377">In the Azure portal, select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="e68a4-378">Vyberte tlačítko **přejít&rarr;** .</span><span class="sxs-lookup"><span data-stu-id="e68a4-378">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="e68a4-379">Konzola Kudu se otevře v novém okně nebo záložce prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="e68a4-379">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="e68a4-380">Pomocí navigačního panelu v horní části stránky otevřete **konzolu ladění** a vyberte **cmd**.</span><span class="sxs-lookup"><span data-stu-id="e68a4-380">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="e68a4-381">Otevřete složky v **lokalitě** pro cestu > **wwwroot** a posuňte se dolů, aby se soubor *Web. config* vypnul v dolní části seznamu.</span><span class="sxs-lookup"><span data-stu-id="e68a4-381">Open the folders to the path **site** > **wwwroot** and scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="e68a4-382">Klikněte na ikonu tužky vedle souboru *Web. config* .</span><span class="sxs-lookup"><span data-stu-id="e68a4-382">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="e68a4-383">Nastavte **stdoutLogEnabled** na `true` a změňte cestu **stdoutLogFile** na: `\\?\%home%\LogFiles\stdout`.</span><span class="sxs-lookup"><span data-stu-id="e68a4-383">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="e68a4-384">Vyberte **Uložit** a uložte aktualizovaný soubor *Web. config* .</span><span class="sxs-lookup"><span data-stu-id="e68a4-384">Select **Save** to save the updated *web.config* file.</span></span>

<span data-ttu-id="e68a4-385">Pokračovat k aktivaci protokolování diagnostiky:</span><span class="sxs-lookup"><span data-stu-id="e68a4-385">Proceed to activate diagnostic logging:</span></span>

1. <span data-ttu-id="e68a4-386">V Azure Portal vyberte okno **diagnostické protokoly** .</span><span class="sxs-lookup"><span data-stu-id="e68a4-386">In the Azure portal, select the **Diagnostics logs** blade.</span></span>
1. <span data-ttu-id="e68a4-387">Vyberte přepínač **zapnuto** pro **protokolování aplikace (systém souborů)** a **podrobné chybové zprávy**.</span><span class="sxs-lookup"><span data-stu-id="e68a4-387">Select the **On** switch for **Application Logging (Filesystem)** and **Detailed error messages**.</span></span> <span data-ttu-id="e68a4-388">V horní části okna vyberte tlačítko **Uložit** .</span><span class="sxs-lookup"><span data-stu-id="e68a4-388">Select the **Save** button at the top of the blade.</span></span>
1. <span data-ttu-id="e68a4-389">Chcete-li zahrnout trasování chybných požadavků, označované také jako protokolování událostí neúspěšných požadavků na FREB, vyberte přepínač **zapnuto** pro **trasování chybných požadavků**.</span><span class="sxs-lookup"><span data-stu-id="e68a4-389">To include failed request tracing, also known as Failed Request Event Buffering (FREB) logging, select the **On** switch for **Failed request tracing**.</span></span>
1. <span data-ttu-id="e68a4-390">Vyberte okno **log Stream** , které je uvedeno hned pod oknem **diagnostické protokoly** na portálu.</span><span class="sxs-lookup"><span data-stu-id="e68a4-390">Select the **Log stream** blade, which is listed immediately under the **Diagnostics logs** blade in the portal.</span></span>
1. <span data-ttu-id="e68a4-391">Vytvořte žádost do aplikace.</span><span class="sxs-lookup"><span data-stu-id="e68a4-391">Make a request to the app.</span></span>
1. <span data-ttu-id="e68a4-392">V datech streamu protokolu se označuje příčina chyby.</span><span class="sxs-lookup"><span data-stu-id="e68a4-392">Within the log stream data, the cause of the error is indicated.</span></span>

<span data-ttu-id="e68a4-393">Nezapomeňte zakázat protokolování stdout po dokončení řešení potíží.</span><span class="sxs-lookup"><span data-stu-id="e68a4-393">Be sure to disable stdout logging when troubleshooting is complete.</span></span>

<span data-ttu-id="e68a4-394">Zobrazení protokolů pro trasování chybných požadavků (protokoly FREB):</span><span class="sxs-lookup"><span data-stu-id="e68a4-394">To view the failed request tracing logs (FREB logs):</span></span>

1. <span data-ttu-id="e68a4-395">Přejděte do okna **diagnostikovat a řešit problémy** v Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="e68a4-395">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="e68a4-396">V oblasti **nástroje podpory** v postranním panelu vyberte **protokoly pro trasování chybných požadavků** .</span><span class="sxs-lookup"><span data-stu-id="e68a4-396">Select **Failed Request Tracing Logs** from the **SUPPORT TOOLS** area of the sidebar.</span></span>

<span data-ttu-id="e68a4-397">V [části Povolení protokolování diagnostiky pro webové aplikace v Azure App Service tématu](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) a [Nejčastější dotazy týkající se výkonu aplikace pro Web Apps v Azure: návody zapnout trasování chybných požadavků?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing) Další informace.</span><span class="sxs-lookup"><span data-stu-id="e68a4-397">See [Failed request traces section of the Enable diagnostics logging for web apps in Azure App Service topic](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) and the [Application performance FAQs for Web Apps in Azure: How do I turn on failed request tracing?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing) for more information.</span></span>

<span data-ttu-id="e68a4-398">Další informace najdete v tématu [Povolení protokolování diagnostiky pro webové aplikace v Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log).</span><span class="sxs-lookup"><span data-stu-id="e68a4-398">For more information, see [Enable diagnostics logging for web apps in Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log).</span></span>

> [!WARNING]
> <span data-ttu-id="e68a4-399">Nepodařilo se zakázat protokol stdout může vést k selhání aplikace nebo serveru.</span><span class="sxs-lookup"><span data-stu-id="e68a4-399">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="e68a4-400">Neexistuje žádné omezení velikosti souboru protokolu nebo počet souborů protokolů, které jsou vytvořeny.</span><span class="sxs-lookup"><span data-stu-id="e68a4-400">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="e68a4-401">Pro rutiny protokolování v aplikaci ASP.NET Core, použijte protokolování knihovnu, která omezuje velikost souboru protokolu a otočí protokoly.</span><span class="sxs-lookup"><span data-stu-id="e68a4-401">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="e68a4-402">Další informace najdete v tématu [Zprostředkovatelé protokolování třetích stran](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="e68a4-402">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

## <a name="troubleshoot-on-iis"></a><span data-ttu-id="e68a4-403">Řešení potíží ve službě IIS</span><span class="sxs-lookup"><span data-stu-id="e68a4-403">Troubleshoot on IIS</span></span>

### <a name="application-event-log-iis"></a><span data-ttu-id="e68a4-404">Protokol událostí aplikace (IIS)</span><span class="sxs-lookup"><span data-stu-id="e68a4-404">Application Event Log (IIS)</span></span>

<span data-ttu-id="e68a4-405">Přístup k protokolu událostí aplikace:</span><span class="sxs-lookup"><span data-stu-id="e68a4-405">Access the Application Event Log:</span></span>

1. <span data-ttu-id="e68a4-406">Otevřete nabídku Start, vyhledejte **Prohlížeč událostí**a pak vyberte aplikaci **Prohlížeč událostí** .</span><span class="sxs-lookup"><span data-stu-id="e68a4-406">Open the Start menu, search for **Event Viewer**, and then select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="e68a4-407">V **Prohlížeč událostí**otevřete uzel **protokoly systému Windows** .</span><span class="sxs-lookup"><span data-stu-id="e68a4-407">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="e68a4-408">Výběrem **aplikace** otevřete protokol událostí aplikace.</span><span class="sxs-lookup"><span data-stu-id="e68a4-408">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="e68a4-409">Vyhledejte chyby související s selhání aplikace.</span><span class="sxs-lookup"><span data-stu-id="e68a4-409">Search for errors associated with the failing app.</span></span> <span data-ttu-id="e68a4-410">Chyby mají ve *zdrojovém* sloupci hodnotu *modulu IIS AspNetCore* nebo *modul IIS Express AspNetCore* .</span><span class="sxs-lookup"><span data-stu-id="e68a4-410">Errors have a value of *IIS AspNetCore Module* or *IIS Express AspNetCore Module* in the *Source* column.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="e68a4-411">Spuštění aplikace příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="e68a4-411">Run the app at a command prompt</span></span>

<span data-ttu-id="e68a4-412">Mnoho chyb při spuštění nevytvářejí užitečné informace v protokolu událostí aplikace.</span><span class="sxs-lookup"><span data-stu-id="e68a4-412">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="e68a4-413">Příčin některých chyb můžete najít spuštěním aplikace v příkazovém řádku v hostitelském systému.</span><span class="sxs-lookup"><span data-stu-id="e68a4-413">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span>

#### <a name="framework-dependent-deployment"></a><span data-ttu-id="e68a4-414">Nasazení závisí na architektuře</span><span class="sxs-lookup"><span data-stu-id="e68a4-414">Framework-dependent deployment</span></span>

<span data-ttu-id="e68a4-415">Pokud aplikace je [nasazení závislé na rozhraní](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span><span class="sxs-lookup"><span data-stu-id="e68a4-415">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

1. <span data-ttu-id="e68a4-416">Na příkazovém řádku přejděte do složky pro nasazení a spusťte aplikaci spuštěním sestavení aplikace pomocí příkazu *dotnet. exe*.</span><span class="sxs-lookup"><span data-stu-id="e68a4-416">At a command prompt, navigate to the deployment folder and run the app by executing the app's assembly with *dotnet.exe*.</span></span> <span data-ttu-id="e68a4-417">V následujícím příkazu nahraďte název sestavení aplikace pro \<assembly_name >: `dotnet .\<assembly_name>.dll`.</span><span class="sxs-lookup"><span data-stu-id="e68a4-417">In the following command, substitute the name of the app's assembly for \<assembly_name>: `dotnet .\<assembly_name>.dll`.</span></span>
1. <span data-ttu-id="e68a4-418">Výstup z aplikace zobrazuje všechny chyby konzoly je zapsán do okna konzoly.</span><span class="sxs-lookup"><span data-stu-id="e68a4-418">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="e68a4-419">Je-li této chybě dojde při požadavku na aplikaci, vytvořte žádost na hostitele a port, kde Kestrel naslouchá.</span><span class="sxs-lookup"><span data-stu-id="e68a4-419">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="e68a4-420">Pomocí výchozího hostitele a příspěvku vytvořte žádost o `http://localhost:5000/`.</span><span class="sxs-lookup"><span data-stu-id="e68a4-420">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="e68a4-421">Aplikace reaguje, obvykle na adrese Kestrel koncový bod, tím je pravděpodobnější týkající se konfigurace hostování a méně pravděpodobné, že v rámci aplikace.</span><span class="sxs-lookup"><span data-stu-id="e68a4-421">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

#### <a name="self-contained-deployment"></a><span data-ttu-id="e68a4-422">Samostatná nasazení</span><span class="sxs-lookup"><span data-stu-id="e68a4-422">Self-contained deployment</span></span>

<span data-ttu-id="e68a4-423">Pokud je aplikace samostatná, [nasazení](/dotnet/core/deploying/#self-contained-deployments-scd):</span><span class="sxs-lookup"><span data-stu-id="e68a4-423">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

1. <span data-ttu-id="e68a4-424">Na příkazovém řádku přejděte do složky pro nasazení a spuštění spustitelného souboru aplikace.</span><span class="sxs-lookup"><span data-stu-id="e68a4-424">At a command prompt, navigate to the deployment folder and run the app's executable.</span></span> <span data-ttu-id="e68a4-425">V následujícím příkazu nahraďte název sestavení aplikace pro \<assembly_name >: `<assembly_name>.exe`.</span><span class="sxs-lookup"><span data-stu-id="e68a4-425">In the following command, substitute the name of the app's assembly for \<assembly_name>: `<assembly_name>.exe`.</span></span>
1. <span data-ttu-id="e68a4-426">Výstup z aplikace zobrazuje všechny chyby konzoly je zapsán do okna konzoly.</span><span class="sxs-lookup"><span data-stu-id="e68a4-426">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="e68a4-427">Je-li této chybě dojde při požadavku na aplikaci, vytvořte žádost na hostitele a port, kde Kestrel naslouchá.</span><span class="sxs-lookup"><span data-stu-id="e68a4-427">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="e68a4-428">Pomocí výchozího hostitele a příspěvku vytvořte žádost o `http://localhost:5000/`.</span><span class="sxs-lookup"><span data-stu-id="e68a4-428">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="e68a4-429">Aplikace reaguje, obvykle na adrese Kestrel koncový bod, tím je pravděpodobnější týkající se konfigurace hostování a méně pravděpodobné, že v rámci aplikace.</span><span class="sxs-lookup"><span data-stu-id="e68a4-429">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

### <a name="aspnet-core-module-stdout-log-iis"></a><span data-ttu-id="e68a4-430">Protokol stdout v modulu ASP.NET Core (IIS)</span><span class="sxs-lookup"><span data-stu-id="e68a4-430">ASP.NET Core Module stdout log (IIS)</span></span>

<span data-ttu-id="e68a4-431">Povolení a zobrazení protokolů stdout:</span><span class="sxs-lookup"><span data-stu-id="e68a4-431">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="e68a4-432">Přejděte do složky pro nasazení webu v hostitelském systému.</span><span class="sxs-lookup"><span data-stu-id="e68a4-432">Navigate to the site's deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="e68a4-433">Pokud složka *logs* není k dispozici, vytvořte složku.</span><span class="sxs-lookup"><span data-stu-id="e68a4-433">If the *logs* folder isn't present, create the folder.</span></span> <span data-ttu-id="e68a4-434">Pokyny k tomu, jak povolit MSBuild pro vytvoření složky *logs* v nasazení automaticky, najdete v tématu [Struktura adresáře](xref:host-and-deploy/directory-structure) .</span><span class="sxs-lookup"><span data-stu-id="e68a4-434">For instructions on how to enable MSBuild to create the *logs* folder in the deployment automatically, see the [Directory structure](xref:host-and-deploy/directory-structure) topic.</span></span>
1. <span data-ttu-id="e68a4-435">Upravte soubor *Web. config* .</span><span class="sxs-lookup"><span data-stu-id="e68a4-435">Edit the *web.config* file.</span></span> <span data-ttu-id="e68a4-436">Nastavte **stdoutLogEnabled** na `true` a změňte cestu **stdoutLogFile** tak, aby odkazovala na složku *logs* (například `.\logs\stdout`).</span><span class="sxs-lookup"><span data-stu-id="e68a4-436">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to point to the *logs* folder (for example, `.\logs\stdout`).</span></span> <span data-ttu-id="e68a4-437">`stdout` v cestě je předpona názvu souboru protokolu.</span><span class="sxs-lookup"><span data-stu-id="e68a4-437">`stdout` in the path is the log file name prefix.</span></span> <span data-ttu-id="e68a4-438">Časové razítko, id procesu a příponu souboru jsou přidány automaticky při vytvoření protokolu.</span><span class="sxs-lookup"><span data-stu-id="e68a4-438">A timestamp, process id, and file extension are added automatically when the log is created.</span></span> <span data-ttu-id="e68a4-439">Při použití `stdout` jako předpony názvu souboru se typický soubor protokolu jmenuje *stdout_20180205184032_5412. log*.</span><span class="sxs-lookup"><span data-stu-id="e68a4-439">Using `stdout` as the file name prefix, a typical log file is named *stdout_20180205184032_5412.log*.</span></span>
1. <span data-ttu-id="e68a4-440">Zajistěte, aby identita fondu aplikací měla oprávnění k zápisu do složky *logs* .</span><span class="sxs-lookup"><span data-stu-id="e68a4-440">Ensure your application pool's identity has write permissions to the *logs* folder.</span></span>
1. <span data-ttu-id="e68a4-441">Uložte aktualizovaný soubor *Web. config* .</span><span class="sxs-lookup"><span data-stu-id="e68a4-441">Save the updated *web.config* file.</span></span>
1. <span data-ttu-id="e68a4-442">Vytvořte žádost do aplikace.</span><span class="sxs-lookup"><span data-stu-id="e68a4-442">Make a request to the app.</span></span>
1. <span data-ttu-id="e68a4-443">Přejděte do složky *logs* .</span><span class="sxs-lookup"><span data-stu-id="e68a4-443">Navigate to the *logs* folder.</span></span> <span data-ttu-id="e68a4-444">Vyhledání a otevření protokolu nejnovější stdout.</span><span class="sxs-lookup"><span data-stu-id="e68a4-444">Find and open the most recent stdout log.</span></span>
1. <span data-ttu-id="e68a4-445">Studie v protokolu chyb.</span><span class="sxs-lookup"><span data-stu-id="e68a4-445">Study the log for errors.</span></span>

<span data-ttu-id="e68a4-446">Zakázat protokolování stdout při odstraňování potíží:</span><span class="sxs-lookup"><span data-stu-id="e68a4-446">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="e68a4-447">Upravte soubor *Web. config* .</span><span class="sxs-lookup"><span data-stu-id="e68a4-447">Edit the *web.config* file.</span></span>
1. <span data-ttu-id="e68a4-448">Nastavte **stdoutLogEnabled** na `false`.</span><span class="sxs-lookup"><span data-stu-id="e68a4-448">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="e68a4-449">Uložte soubor.</span><span class="sxs-lookup"><span data-stu-id="e68a4-449">Save the file.</span></span>

<span data-ttu-id="e68a4-450">Další informace najdete v tématu <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span><span class="sxs-lookup"><span data-stu-id="e68a4-450">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

> [!WARNING]
> <span data-ttu-id="e68a4-451">Nepodařilo se zakázat protokol stdout může vést k selhání aplikace nebo serveru.</span><span class="sxs-lookup"><span data-stu-id="e68a4-451">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="e68a4-452">Neexistuje žádné omezení velikosti souboru protokolu nebo počet souborů protokolů, které jsou vytvořeny.</span><span class="sxs-lookup"><span data-stu-id="e68a4-452">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="e68a4-453">Pro rutiny protokolování v aplikaci ASP.NET Core, použijte protokolování knihovnu, která omezuje velikost souboru protokolu a otočí protokoly.</span><span class="sxs-lookup"><span data-stu-id="e68a4-453">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="e68a4-454">Další informace najdete v tématu [Zprostředkovatelé protokolování třetích stran](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="e68a4-454">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

::: moniker range=">= aspnetcore-2.2"

### <a name="aspnet-core-module-debug-log-iis"></a><span data-ttu-id="e68a4-455">Protokol ladění modulu ASP.NET Core (IIS)</span><span class="sxs-lookup"><span data-stu-id="e68a4-455">ASP.NET Core Module debug log (IIS)</span></span>

<span data-ttu-id="e68a4-456">Přidejte následující nastavení obslužné rutiny do souboru *Web. config* aplikace, aby bylo možné povolit protokol ladění ASP.NET Core modulu:</span><span class="sxs-lookup"><span data-stu-id="e68a4-456">Add the following handler settings to the app's *web.config* file to enable ASP.NET Core Module debug log:</span></span>

```xml
<aspNetCore ...>
  <handlerSettings>
    <handlerSetting name="debugLevel" value="file" />
    <handlerSetting name="debugFile" value="c:\temp\ancm.log" />
  </handlerSettings>
</aspNetCore>
```

<span data-ttu-id="e68a4-457">Potvrďte, že cesta zadaná pro protokol existuje a že identita fondu aplikací má oprávnění k zápisu do umístění.</span><span class="sxs-lookup"><span data-stu-id="e68a4-457">Confirm that the path specified for the log exists and that the app pool's identity has write permissions to the location.</span></span>

<span data-ttu-id="e68a4-458">Další informace najdete v tématu <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span><span class="sxs-lookup"><span data-stu-id="e68a4-458">For more information, see <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span></span>

::: moniker-end

### <a name="enable-the-developer-exception-page"></a><span data-ttu-id="e68a4-459">Povolit na stránce výjimek pro vývojáře</span><span class="sxs-lookup"><span data-stu-id="e68a4-459">Enable the Developer Exception Page</span></span>

<span data-ttu-id="e68a4-460">[Proměnnou prostředí `ASPNETCORE_ENVIRONMENT` lze přidat do souboru Web. config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) , aby bylo možné aplikaci spustit ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="e68a4-460">The `ASPNETCORE_ENVIRONMENT` [environment variable can be added to web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) to run the app in the Development environment.</span></span> <span data-ttu-id="e68a4-461">Pokud se prostředí nepřepisuje při spuštění aplikace `UseEnvironment` v Tvůrci hostitele, nastavení proměnné prostředí umožní, aby se při spuštění aplikace zobrazila [Stránka s výjimkou vývojářů](xref:fundamentals/error-handling) .</span><span class="sxs-lookup"><span data-stu-id="e68a4-461">As long as the environment isn't overridden in app startup by `UseEnvironment` on the host builder, setting the environment variable allows the [Developer Exception Page](xref:fundamentals/error-handling) to appear when the app is run.</span></span>

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

<span data-ttu-id="e68a4-462">Nastavení proměnné prostředí pro `ASPNETCORE_ENVIRONMENT` se doporučuje jenom pro použití na pracovních a testovacích serverech, které nejsou přístupné pro Internet.</span><span class="sxs-lookup"><span data-stu-id="e68a4-462">Setting the environment variable for `ASPNETCORE_ENVIRONMENT` is only recommended for use on staging and testing servers that aren't exposed to the Internet.</span></span> <span data-ttu-id="e68a4-463">Po vyřešení potíží odeberte proměnnou prostředí ze souboru *Web. config* .</span><span class="sxs-lookup"><span data-stu-id="e68a4-463">Remove the environment variable from the *web.config* file after troubleshooting.</span></span> <span data-ttu-id="e68a4-464">Informace o nastavení proměnných prostředí v *souboru Web. config*naleznete v tématu [environmentVariables Child element of aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).</span><span class="sxs-lookup"><span data-stu-id="e68a4-464">For information on setting environment variables in *web.config*, see [environmentVariables child element of aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).</span></span>

### <a name="obtain-data-from-an-app"></a><span data-ttu-id="e68a4-465">Získání dat z aplikace</span><span class="sxs-lookup"><span data-stu-id="e68a4-465">Obtain data from an app</span></span>

<span data-ttu-id="e68a4-466">Pokud aplikace je schopná reagovat na požadavky, získáte žádost o připojení a další data z aplikace pomocí terminálu vložené middlewaru.</span><span class="sxs-lookup"><span data-stu-id="e68a4-466">If an app is capable of responding to requests, obtain request, connection, and additional data from the app using terminal inline middleware.</span></span> <span data-ttu-id="e68a4-467">Další informace a ukázku kódu naleznete v tématu <xref:test/troubleshoot#obtain-data-from-an-app>.</span><span class="sxs-lookup"><span data-stu-id="e68a4-467">For more information and sample code, see <xref:test/troubleshoot#obtain-data-from-an-app>.</span></span>

### <a name="slow-or-hanging-app-iis"></a><span data-ttu-id="e68a4-468">Pomalá nebo zavěšená aplikace (IIS)</span><span class="sxs-lookup"><span data-stu-id="e68a4-468">Slow or hanging app (IIS)</span></span>

<span data-ttu-id="e68a4-469">*Výpis stavu* systému je snímek paměti systému a může vám pomůže určit příčinu selhání aplikace, selhání při spuštění nebo pomalé aplikace.</span><span class="sxs-lookup"><span data-stu-id="e68a4-469">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="e68a4-470">Aplikace selže nebo dojde k výjimce.</span><span class="sxs-lookup"><span data-stu-id="e68a4-470">App crashes or encounters an exception</span></span>

<span data-ttu-id="e68a4-471">Získat a analyzovat výpis z [zasílání zpráv o chybách systému Windows (WER)](/windows/desktop/wer/windows-error-reporting):</span><span class="sxs-lookup"><span data-stu-id="e68a4-471">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="e68a4-472">Vytvořte složku, do které se budou ukládat soubory s výpisem stavu systému na `c:\dumps`.</span><span class="sxs-lookup"><span data-stu-id="e68a4-472">Create a folder to hold crash dump files at `c:\dumps`.</span></span> <span data-ttu-id="e68a4-473">Fond aplikací musí mít ke složce přístup pro zápis.</span><span class="sxs-lookup"><span data-stu-id="e68a4-473">The app pool must have write access to the folder.</span></span>
1. <span data-ttu-id="e68a4-474">Spusťte [skript prostředí PowerShell pro EnableDumps](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1):</span><span class="sxs-lookup"><span data-stu-id="e68a4-474">Run the [EnableDumps PowerShell script](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1):</span></span>
   * <span data-ttu-id="e68a4-475">Pokud aplikace používá [model hostování v rámci procesu](xref:host-and-deploy/iis/index#in-process-hosting-model), spusťte skript pro *W3wp. exe*:</span><span class="sxs-lookup"><span data-stu-id="e68a4-475">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\EnableDumps w3wp.exe c:\dumps
     ```

   * <span data-ttu-id="e68a4-476">Pokud aplikace používá [model hostování mimo proces](xref:host-and-deploy/iis/index#out-of-process-hosting-model), spusťte skript pro příkaz *dotnet. exe*:</span><span class="sxs-lookup"><span data-stu-id="e68a4-476">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\EnableDumps dotnet.exe c:\dumps
     ```

1. <span data-ttu-id="e68a4-477">Spusťte aplikaci za podmínek, které způsobí, že dojde k chybě.</span><span class="sxs-lookup"><span data-stu-id="e68a4-477">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="e68a4-478">Po chybě spusťte [skript PowerShellu DisableDumps](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1):</span><span class="sxs-lookup"><span data-stu-id="e68a4-478">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1):</span></span>
   * <span data-ttu-id="e68a4-479">Pokud aplikace používá [model hostování v rámci procesu](xref:host-and-deploy/iis/index#in-process-hosting-model), spusťte skript pro *W3wp. exe*:</span><span class="sxs-lookup"><span data-stu-id="e68a4-479">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\DisableDumps w3wp.exe
     ```

   * <span data-ttu-id="e68a4-480">Pokud aplikace používá [model hostování mimo proces](xref:host-and-deploy/iis/index#out-of-process-hosting-model), spusťte skript pro příkaz *dotnet. exe*:</span><span class="sxs-lookup"><span data-stu-id="e68a4-480">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\DisableDumps dotnet.exe
     ```

<span data-ttu-id="e68a4-481">Po selhání aplikace a dokončení shromažďování výpisu je možné aplikaci ukončit normálně.</span><span class="sxs-lookup"><span data-stu-id="e68a4-481">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="e68a4-482">Skript PowerShellu nakonfiguruje WER a shromáždí až pět výpisů paměti pro každou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="e68a4-482">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="e68a4-483">Výpisy stavu systému můžou zabírat velké množství místa na disku (každý až několik gigabajtů).</span><span class="sxs-lookup"><span data-stu-id="e68a4-483">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="e68a4-484">Aplikace přestane reagovat, dojde k chybě při spuštění nebo se spustí normálně.</span><span class="sxs-lookup"><span data-stu-id="e68a4-484">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="e68a4-485">Když *aplikace přestane reagovat (zastaví* se, ale nejedná se o chybu), selže během spuštění nebo se spustí normálně, podívejte [se na soubory výpisu paměti v uživatelském režimu: zvolením nejlepšího](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) nástroje vyberte vhodný nástroj pro vytváření výpisu.</span><span class="sxs-lookup"><span data-stu-id="e68a4-485">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="e68a4-486">Analýza výpisu paměti</span><span class="sxs-lookup"><span data-stu-id="e68a4-486">Analyze the dump</span></span>

<span data-ttu-id="e68a4-487">Výpis paměti lze analyzovat pomocí několika přístupů.</span><span class="sxs-lookup"><span data-stu-id="e68a4-487">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="e68a4-488">Další informace najdete v tématu [Analýza souboru s výpisem stavu v uživatelském režimu](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span><span class="sxs-lookup"><span data-stu-id="e68a4-488">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="clear-package-caches"></a><span data-ttu-id="e68a4-489">Vymazat mezipaměti balíčků</span><span class="sxs-lookup"><span data-stu-id="e68a4-489">Clear package caches</span></span>

<span data-ttu-id="e68a4-490">Někdy dojde k chybě funkční aplikace hned po upgradu .NET Core SDK ve vývojovém počítači nebo při změně verzí balíčku v rámci aplikace.</span><span class="sxs-lookup"><span data-stu-id="e68a4-490">Sometimes a functioning app fails immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="e68a4-491">V některých případech osamocené balíčky mohou narušit funkce aplikace při provádění hlavní upgrady.</span><span class="sxs-lookup"><span data-stu-id="e68a4-491">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="e68a4-492">Většina těchto problémů můžete opravit podle těchto pokynů:</span><span class="sxs-lookup"><span data-stu-id="e68a4-492">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="e68a4-493">Odstraňte složky *bin* a *obj* .</span><span class="sxs-lookup"><span data-stu-id="e68a4-493">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="e68a4-494">Vymažte mezipaměti balíčků spuštěním `dotnet nuget locals all --clear` z příkazového prostředí.</span><span class="sxs-lookup"><span data-stu-id="e68a4-494">Clear the package caches by executing `dotnet nuget locals all --clear` from a command shell.</span></span>

   <span data-ttu-id="e68a4-495">Mazání mezipamětí balíčků lze také provést pomocí nástroje [NuGet. exe](https://www.nuget.org/downloads) a spuštěním příkazu `nuget locals all -clear`.</span><span class="sxs-lookup"><span data-stu-id="e68a4-495">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="e68a4-496">*NuGet. exe* není sada instalovaná instalace s desktopovým operačním systémem Windows a musí se získat samostatně z [webu NuGet](https://www.nuget.org/downloads).</span><span class="sxs-lookup"><span data-stu-id="e68a4-496">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="e68a4-497">Obnovit a znovu sestavte projekt.</span><span class="sxs-lookup"><span data-stu-id="e68a4-497">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="e68a4-498">Před opětovným nasazením aplikace odstraňte všechny soubory ve složce pro nasazení na serveru.</span><span class="sxs-lookup"><span data-stu-id="e68a4-498">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e68a4-499">Další zdroje informací:</span><span class="sxs-lookup"><span data-stu-id="e68a4-499">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:fundamentals/error-handling>
* <xref:host-and-deploy/aspnet-core-module>

### <a name="azure-documentation"></a><span data-ttu-id="e68a4-500">Dokumentace k Azure</span><span class="sxs-lookup"><span data-stu-id="e68a4-500">Azure documentation</span></span>

* [<span data-ttu-id="e68a4-501">Application Insights pro ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e68a4-501">Application Insights for ASP.NET Core</span></span>](/azure/application-insights/app-insights-asp-net-core)
* [<span data-ttu-id="e68a4-502">Část vzdálené ladění webových aplikací řešení potíží s webovou aplikací v Azure App Service pomocí sady Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e68a4-502">Remote debugging web apps section of Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug)
* [<span data-ttu-id="e68a4-503">Přehled diagnostiky Azure App Service</span><span class="sxs-lookup"><span data-stu-id="e68a4-503">Azure App Service diagnostics overview</span></span>](/azure/app-service/app-service-diagnostics)
* [<span data-ttu-id="e68a4-504">Postupy: monitorování aplikací v Azure App Service</span><span class="sxs-lookup"><span data-stu-id="e68a4-504">How to: Monitor Apps in Azure App Service</span></span>](/azure/app-service/web-sites-monitor)
* [<span data-ttu-id="e68a4-505">Řešení potíží s webovou aplikací v Azure App Service pomocí sady Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e68a4-505">Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [<span data-ttu-id="e68a4-506">Řešení chyb HTTP z "502 špatné brány" a "nedostupné služby 503" v Azure Web Apps</span><span class="sxs-lookup"><span data-stu-id="e68a4-506">Troubleshoot HTTP errors of "502 bad gateway" and "503 service unavailable" in your Azure web apps</span></span>](/azure/app-service/app-service-web-troubleshoot-http-502-http-503)
* [<span data-ttu-id="e68a4-507">Řešení potíží s výkonem pomalých webových aplikací v Azure App Service</span><span class="sxs-lookup"><span data-stu-id="e68a4-507">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="e68a4-508">Nejčastější dotazy týkající se výkonu aplikací pro Web Apps v Azure</span><span class="sxs-lookup"><span data-stu-id="e68a4-508">Application performance FAQs for Web Apps in Azure</span></span>](/azure/app-service/app-service-web-availability-performance-application-issues-faq)
* [<span data-ttu-id="e68a4-509">Sandbox webové aplikace Azure (omezení spuštění App Service Runtime)</span><span class="sxs-lookup"><span data-stu-id="e68a4-509">Azure Web App sandbox (App Service runtime execution limitations)</span></span>](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)
* [<span data-ttu-id="e68a4-510">Azure pátek: Azure App Service diagnostické prostředí a řešení potíží (12 minut video)</span><span class="sxs-lookup"><span data-stu-id="e68a4-510">Azure Friday: Azure App Service Diagnostic and Troubleshooting Experience (12-minute video)</span></span>](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)

### <a name="visual-studio-documentation"></a><span data-ttu-id="e68a4-511">Dokumentace sady Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e68a4-511">Visual Studio documentation</span></span>

* [<span data-ttu-id="e68a4-512">Vzdálené ladění ASP.NET Core ve službě IIS v Azure v aplikaci Visual Studio 2017</span><span class="sxs-lookup"><span data-stu-id="e68a4-512">Remote Debug ASP.NET Core on IIS in Azure in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-azure)
* [<span data-ttu-id="e68a4-513">Vzdálené ladění ASP.NET Core na vzdáleném počítači IIS v aplikaci Visual Studio 2017</span><span class="sxs-lookup"><span data-stu-id="e68a4-513">Remote Debug ASP.NET Core on a Remote IIS Computer in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-aspnet-on-a-remote-iis-computer)
* [<span data-ttu-id="e68a4-514">Další informace k ladění pomocí sady Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e68a4-514">Learn to debug using Visual Studio</span></span>](/visualstudio/debugger/getting-started-with-the-debugger)

### <a name="visual-studio-code-documentation"></a><span data-ttu-id="e68a4-515">Dokumentace k Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e68a4-515">Visual Studio Code documentation</span></span>

* [<span data-ttu-id="e68a4-516">Ladění pomocí Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e68a4-516">Debugging with Visual Studio Code</span></span>](https://code.visualstudio.com/docs/editor/debugging)
