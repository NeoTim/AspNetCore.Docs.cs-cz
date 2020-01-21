---
title: Řešení potíží s ASP.NET Core v Azure App Service a IIS
author: guardrex
description: Naučte se diagnostikovat problémy s Azure App Service a Internetová informační služba nasazení ASP.NET Core aplikací (IIS).
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/18/2020
uid: test/troubleshoot-azure-iis
ms.openlocfilehash: 071dba9e936351e201b7582b3d0667cd6fac54bb
ms.sourcegitcommit: f259889044d1fc0f0c7e3882df0008157ced4915
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/21/2020
ms.locfileid: "76294616"
---
# <a name="troubleshoot-aspnet-core-on-azure-app-service-and-iis"></a><span data-ttu-id="e0f54-103">Řešení potíží s ASP.NET Core v Azure App Service a IIS</span><span class="sxs-lookup"><span data-stu-id="e0f54-103">Troubleshoot ASP.NET Core on Azure App Service and IIS</span></span>

<span data-ttu-id="e0f54-104">Od [Luke Latham](https://github.com/guardrex) a [Justin Kotalik](https://github.com/jkotalik)</span><span class="sxs-lookup"><span data-stu-id="e0f54-104">By [Luke Latham](https://github.com/guardrex) and [Justin Kotalik](https://github.com/jkotalik)</span></span>

<span data-ttu-id="e0f54-105">Tento článek poskytuje informace o běžných chybách při spuštění aplikací a pokyny k diagnostice chyb při nasazení aplikace do Azure App Service nebo IIS:</span><span class="sxs-lookup"><span data-stu-id="e0f54-105">This article provides information on common app startup errors and instructions on how to diagnose errors when an app is deployed to Azure App Service or IIS:</span></span>

[<span data-ttu-id="e0f54-106">Chyby při spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="e0f54-106">App startup errors</span></span>](#app-startup-errors)  
<span data-ttu-id="e0f54-107">Vysvětluje běžné spouštění scénářů stavového kódu HTTP.</span><span class="sxs-lookup"><span data-stu-id="e0f54-107">Explains common startup HTTP status code scenarios.</span></span>

[<span data-ttu-id="e0f54-108">Řešení potíží na Azure App Service</span><span class="sxs-lookup"><span data-stu-id="e0f54-108">Troubleshoot on Azure App Service</span></span>](#troubleshoot-on-azure-app-service)  
<span data-ttu-id="e0f54-109">Poskytuje rady pro řešení potíží pro aplikace nasazené do Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="e0f54-109">Provides troubleshooting advice for apps deployed to Azure App Service.</span></span>

[<span data-ttu-id="e0f54-110">Odstraňování potíží ve službě IIS</span><span class="sxs-lookup"><span data-stu-id="e0f54-110">Troubleshoot on IIS</span></span>](#troubleshoot-on-iis)  
<span data-ttu-id="e0f54-111">Poskytuje rady pro řešení potíží pro aplikace nasazené do služby IIS nebo místně spuštěné v IIS Express.</span><span class="sxs-lookup"><span data-stu-id="e0f54-111">Provides troubleshooting advice for apps deployed to IIS or running on IIS Express locally.</span></span> <span data-ttu-id="e0f54-112">Pokyny platí pro nasazení systémů Windows Server a Desktop v systému Windows.</span><span class="sxs-lookup"><span data-stu-id="e0f54-112">The guidance applies to both Windows Server and Windows desktop deployments.</span></span>

[<span data-ttu-id="e0f54-113">Vymazat mezipaměti balíčků</span><span class="sxs-lookup"><span data-stu-id="e0f54-113">Clear package caches</span></span>](#clear-package-caches)  
<span data-ttu-id="e0f54-114">Vysvětluje, co dělat v případě, že při provádění podstatných upgradů nebo změně verze balíčku dojde k přerušení aplikace v nesouvislých balíčcích.</span><span class="sxs-lookup"><span data-stu-id="e0f54-114">Explains what to do when incoherent packages break an app when performing major upgrades or changing package versions.</span></span>

[<span data-ttu-id="e0f54-115">Další zdroje informací</span><span class="sxs-lookup"><span data-stu-id="e0f54-115">Additional resources</span></span>](#additional-resources)  
<span data-ttu-id="e0f54-116">Seznam dalších témat pro odstraňování potíží.</span><span class="sxs-lookup"><span data-stu-id="e0f54-116">Lists additional troubleshooting topics.</span></span>

## <a name="app-startup-errors"></a><span data-ttu-id="e0f54-117">Chyby při spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="e0f54-117">App startup errors</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="e0f54-118">V sadě Visual Studio projekt ASP.NET Core výchozí hodnota je [služby IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hostování během ladění.</span><span class="sxs-lookup"><span data-stu-id="e0f54-118">In Visual Studio, an ASP.NET Core project defaults to [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hosting during debugging.</span></span> <span data-ttu-id="e0f54-119">*502,5 – selhání procesu* nebo *500,30 – chyba* , ke které dojde při místním ladění, se dá diagnostikovat pomocí Rady v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="e0f54-119">A *502.5 - Process Failure* or a *500.30 - Start Failure* that occurs when debugging locally can be diagnosed using the advice in this topic.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="e0f54-120">V sadě Visual Studio projekt ASP.NET Core výchozí hodnota je [služby IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hostování během ladění.</span><span class="sxs-lookup"><span data-stu-id="e0f54-120">In Visual Studio, an ASP.NET Core project defaults to [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hosting during debugging.</span></span> <span data-ttu-id="e0f54-121">*Selhání procesu 502,5* , ke kterému dochází, když se místně ladění dá diagnostikovat pomocí Rady v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="e0f54-121">A *502.5 Process Failure* that occurs when debugging locally can be diagnosed using the advice in this topic.</span></span>

::: moniker-end

### <a name="40314-forbidden"></a><span data-ttu-id="e0f54-122">403,14 zakázané</span><span class="sxs-lookup"><span data-stu-id="e0f54-122">403.14 Forbidden</span></span>

<span data-ttu-id="e0f54-123">Aplikaci se nepodařilo spustit.</span><span class="sxs-lookup"><span data-stu-id="e0f54-123">The app fails to start.</span></span> <span data-ttu-id="e0f54-124">Protokoluje se následující chyba:</span><span class="sxs-lookup"><span data-stu-id="e0f54-124">The following error is logged:</span></span>

```
The Web server is configured to not list the contents of this directory.
```

<span data-ttu-id="e0f54-125">K této chybě obvykle dochází v důsledku poškozeného nasazení v hostitelském systému, který zahrnuje kterýkoli z následujících scénářů:</span><span class="sxs-lookup"><span data-stu-id="e0f54-125">The error is usually caused by a broken deployment on the hosting system, which includes any of the following scenarios:</span></span>

* <span data-ttu-id="e0f54-126">Aplikace je nasazená do chybné složky v hostitelském systému.</span><span class="sxs-lookup"><span data-stu-id="e0f54-126">The app is deployed to the wrong folder on the hosting system.</span></span>
* <span data-ttu-id="e0f54-127">Procesu nasazení se nepodařilo přesunout všechny soubory a složky aplikace do složky pro nasazení v hostitelském systému.</span><span class="sxs-lookup"><span data-stu-id="e0f54-127">The deployment process failed to move all of the app's files and folders to the deployment folder on the hosting system.</span></span>
* <span data-ttu-id="e0f54-128">V nasazení chybí soubor *Web. config* , nebo je obsah souboru *Web. config* poškozený.</span><span class="sxs-lookup"><span data-stu-id="e0f54-128">The *web.config* file is missing from the deployment, or the *web.config* file contents are malformed.</span></span>

<span data-ttu-id="e0f54-129">Proveďte následující kroky:</span><span class="sxs-lookup"><span data-stu-id="e0f54-129">Perform the following steps:</span></span>

1. <span data-ttu-id="e0f54-130">Odstraňte všechny soubory a složky ze složky pro nasazení v hostitelském systému.</span><span class="sxs-lookup"><span data-stu-id="e0f54-130">Delete all of the files and folders from the deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="e0f54-131">Znovu nasaďte obsah složky pro *publikování* aplikace do hostitelského systému pomocí běžné metody nasazení, jako je například Visual Studio, PowerShell nebo ruční nasazení:</span><span class="sxs-lookup"><span data-stu-id="e0f54-131">Redeploy the contents of the app's *publish* folder to the hosting system using your normal method of deployment, such as Visual Studio, PowerShell, or manual deployment:</span></span>
   * <span data-ttu-id="e0f54-132">Zkontrolujte, zda je v nasazení přítomen soubor *Web. config* a zda jeho obsah je správný.</span><span class="sxs-lookup"><span data-stu-id="e0f54-132">Confirm that the *web.config* file is present in the deployment and that its contents are correct.</span></span>
   * <span data-ttu-id="e0f54-133">Při hostování na Azure App Service potvrďte, že je aplikace nasazená do `D:\home\site\wwwroot` složky.</span><span class="sxs-lookup"><span data-stu-id="e0f54-133">When hosting on Azure App Service, confirm that the app is deployed to the `D:\home\site\wwwroot` folder.</span></span>
   * <span data-ttu-id="e0f54-134">Když je aplikace hostovaná službou IIS, zkontrolujte, že je aplikace nasazená na **fyzickou cestu** služby IIS, která se zobrazuje v **základním nastavení** **Správce služby IIS**.</span><span class="sxs-lookup"><span data-stu-id="e0f54-134">When the app is hosted by IIS, confirm that the app is deployed to the IIS **Physical path** shown in **IIS Manager**'s **Basic Settings**.</span></span>
1. <span data-ttu-id="e0f54-135">Ověřte, zda jsou všechny soubory a složky aplikace nasazeny porovnáním nasazení v hostitelském systému s obsahem složky *publikování* projektu.</span><span class="sxs-lookup"><span data-stu-id="e0f54-135">Confirm that all of the app's files and folders are deployed by comparing the deployment on the hosting system to the contents of the project's *publish* folder.</span></span>

<span data-ttu-id="e0f54-136">Další informace o rozložení publikované aplikace ASP.NET Core najdete v tématu <xref:host-and-deploy/directory-structure>.</span><span class="sxs-lookup"><span data-stu-id="e0f54-136">For more information on the layout of a published ASP.NET Core app, see <xref:host-and-deploy/directory-structure>.</span></span> <span data-ttu-id="e0f54-137">Další informace o souboru *Web. config* naleznete v tématu <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>.</span><span class="sxs-lookup"><span data-stu-id="e0f54-137">For more information on the *web.config* file, see <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>.</span></span>

### <a name="500-internal-server-error"></a><span data-ttu-id="e0f54-138">Chyba 500 interní Server</span><span class="sxs-lookup"><span data-stu-id="e0f54-138">500 Internal Server Error</span></span>

<span data-ttu-id="e0f54-139">Spuštění aplikace, ale chybu brání splnění žádosti. na serveru.</span><span class="sxs-lookup"><span data-stu-id="e0f54-139">The app starts, but an error prevents the server from fulfilling the request.</span></span>

<span data-ttu-id="e0f54-140">Při spuštění nebo při vytváření odpovědi, k této chybě dochází v kódu aplikace.</span><span class="sxs-lookup"><span data-stu-id="e0f54-140">This error occurs within the app's code during startup or while creating a response.</span></span> <span data-ttu-id="e0f54-141">Odpověď může obsahovat žádný obsah nebo se může zobrazit odpovědi *500 – Interní chyba serveru* v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="e0f54-141">The response may contain no content, or the response may appear as a *500 Internal Server Error* in the browser.</span></span> <span data-ttu-id="e0f54-142">V protokolu událostí aplikace obvykle hlásí, že aplikace se normálně spustit.</span><span class="sxs-lookup"><span data-stu-id="e0f54-142">The Application Event Log usually states that the app started normally.</span></span> <span data-ttu-id="e0f54-143">Z pohledu serveru, který je správný.</span><span class="sxs-lookup"><span data-stu-id="e0f54-143">From the server's perspective, that's correct.</span></span> <span data-ttu-id="e0f54-144">Aplikace začal, ale nemůže generovat platnou odpověď.</span><span class="sxs-lookup"><span data-stu-id="e0f54-144">The app did start, but it can't generate a valid response.</span></span> <span data-ttu-id="e0f54-145">Spusťte aplikaci na příkazovém řádku na serveru nebo povolte řešení potíží pomocí protokolu stdout modulu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e0f54-145">Run the app at a command prompt on the server or enable the ASP.NET Core Module stdout log to troubleshoot the problem.</span></span>

::: moniker range="= aspnetcore-2.2"

### <a name="5000-in-process-handler-load-failure"></a><span data-ttu-id="e0f54-146">500.0 v procesu selhání načtení obslužné rutiny</span><span class="sxs-lookup"><span data-stu-id="e0f54-146">500.0 In-Process Handler Load Failure</span></span>

<span data-ttu-id="e0f54-147">Pracovní proces se nezdaří.</span><span class="sxs-lookup"><span data-stu-id="e0f54-147">The worker process fails.</span></span> <span data-ttu-id="e0f54-148">Aplikace se nespustí.</span><span class="sxs-lookup"><span data-stu-id="e0f54-148">The app doesn't start.</span></span>

<span data-ttu-id="e0f54-149">[Modulu ASP.NET Core](xref:host-and-deploy/aspnet-core-module) se nepovedlo najít modul CLR .NET Core a najít obslužnou rutinu žádosti v procesu (*aspnetcorev2_inprocess. dll*).</span><span class="sxs-lookup"><span data-stu-id="e0f54-149">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) fails to find the .NET Core CLR and find the in-process request handler (*aspnetcorev2_inprocess.dll*).</span></span> <span data-ttu-id="e0f54-150">Zkontrolujte, jestli:</span><span class="sxs-lookup"><span data-stu-id="e0f54-150">Check that:</span></span>

* <span data-ttu-id="e0f54-151">Aplikace cílí na buď [Microsoft.AspNetCore.Server.IIS](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IIS) balíček NuGet nebo [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="e0f54-151">The app targets either the [Microsoft.AspNetCore.Server.IIS](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IIS) NuGet package or the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>
* <span data-ttu-id="e0f54-152">Verze rozhraní framework ASP.NET Core sdílené cíle, které aplikace nainstalované v cílovém počítači.</span><span class="sxs-lookup"><span data-stu-id="e0f54-152">The version of the ASP.NET Core shared framework that the app targets is installed on the target machine.</span></span>

### <a name="5000-out-of-process-handler-load-failure"></a><span data-ttu-id="e0f54-153">500.0 Chyba načtení out-Of-Process obslužné rutiny</span><span class="sxs-lookup"><span data-stu-id="e0f54-153">500.0 Out-Of-Process Handler Load Failure</span></span>

<span data-ttu-id="e0f54-154">Pracovní proces se nezdaří.</span><span class="sxs-lookup"><span data-stu-id="e0f54-154">The worker process fails.</span></span> <span data-ttu-id="e0f54-155">Aplikace se nespustí.</span><span class="sxs-lookup"><span data-stu-id="e0f54-155">The app doesn't start.</span></span>

<span data-ttu-id="e0f54-156">[Modulu ASP.NET Core](xref:host-and-deploy/aspnet-core-module) se nepodařilo najít obslužnou rutinu žádosti mimo proces hostování.</span><span class="sxs-lookup"><span data-stu-id="e0f54-156">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) fails to find the out-of-process hosting request handler.</span></span> <span data-ttu-id="e0f54-157">Ujistěte se, *aspnetcorev2_outofprocess.dll* je k dispozici v podsložce vedle *aspnetcorev2.dll*.</span><span class="sxs-lookup"><span data-stu-id="e0f54-157">Make sure the *aspnetcorev2_outofprocess.dll* is present in a subfolder next to *aspnetcorev2.dll*.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="5000-in-process-handler-load-failure"></a><span data-ttu-id="e0f54-158">500.0 v procesu selhání načtení obslužné rutiny</span><span class="sxs-lookup"><span data-stu-id="e0f54-158">500.0 In-Process Handler Load Failure</span></span>

<span data-ttu-id="e0f54-159">Pracovní proces se nezdaří.</span><span class="sxs-lookup"><span data-stu-id="e0f54-159">The worker process fails.</span></span> <span data-ttu-id="e0f54-160">Aplikace se nespustí.</span><span class="sxs-lookup"><span data-stu-id="e0f54-160">The app doesn't start.</span></span>

<span data-ttu-id="e0f54-161">Při načítání komponent [modulu ASP.NET Core](xref:host-and-deploy/aspnet-core-module) došlo k neznámé chybě.</span><span class="sxs-lookup"><span data-stu-id="e0f54-161">An unknown error occurred loading [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) components.</span></span> <span data-ttu-id="e0f54-162">Proveďte jednu z následujících akcí:</span><span class="sxs-lookup"><span data-stu-id="e0f54-162">Take one of the following actions:</span></span>

* <span data-ttu-id="e0f54-163">Kontaktujte [Podpora Microsoftu](https://support.microsoft.com/oas/default.aspx?prid=15832) (vyberte **vývojářské nástroje** potom **ASP.NET Core**).</span><span class="sxs-lookup"><span data-stu-id="e0f54-163">Contact [Microsoft Support](https://support.microsoft.com/oas/default.aspx?prid=15832) (select **Developer Tools** then **ASP.NET Core**).</span></span>
* <span data-ttu-id="e0f54-164">Položte otázku na Stack Overflow.</span><span class="sxs-lookup"><span data-stu-id="e0f54-164">Ask a question on Stack Overflow.</span></span>
* <span data-ttu-id="e0f54-165">Vydejte problém do našeho [úložiště GitHub](https://github.com/dotnet/AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="e0f54-165">File an issue on our [GitHub repository](https://github.com/dotnet/AspNetCore).</span></span>

### <a name="50030-in-process-startup-failure"></a><span data-ttu-id="e0f54-166">500.30 v procesu selhání spuštění</span><span class="sxs-lookup"><span data-stu-id="e0f54-166">500.30 In-Process Startup Failure</span></span>

<span data-ttu-id="e0f54-167">Pracovní proces se nezdaří.</span><span class="sxs-lookup"><span data-stu-id="e0f54-167">The worker process fails.</span></span> <span data-ttu-id="e0f54-168">Aplikace se nespustí.</span><span class="sxs-lookup"><span data-stu-id="e0f54-168">The app doesn't start.</span></span>

<span data-ttu-id="e0f54-169">[Modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module) se pokusí spustit modul CLR .NET Core v procesu, ale jeho spuštění se nezdařilo.</span><span class="sxs-lookup"><span data-stu-id="e0f54-169">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the .NET Core CLR in-process, but it fails to start.</span></span> <span data-ttu-id="e0f54-170">Příčinou selhání spuštění procesu může být obvykle určení z položek protokolu událostí aplikace a protokolu stdout modulu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e0f54-170">The cause of a process startup failure can usually be determined from entries in the Application Event Log and the ASP.NET Core Module stdout log.</span></span>

<span data-ttu-id="e0f54-171">Běžné podmínky selhání:</span><span class="sxs-lookup"><span data-stu-id="e0f54-171">Common failure conditions:</span></span>

* <span data-ttu-id="e0f54-172">Aplikace je nesprávně nakonfigurovaná kvůli cílení na verzi ASP.NET Core sdílené rozhraní, které není k dispozici.</span><span class="sxs-lookup"><span data-stu-id="e0f54-172">The app is misconfigured due to targeting a version of the ASP.NET Core shared framework that isn't present.</span></span> <span data-ttu-id="e0f54-173">Zkontrolujte, jaké verze rozhraní framework ASP.NET Core sdílené jsou nainstalovány v cílovém počítači.</span><span class="sxs-lookup"><span data-stu-id="e0f54-173">Check which versions of the ASP.NET Core shared framework are installed on the target machine.</span></span>
* <span data-ttu-id="e0f54-174">Při použití Azure Key Vault chybí oprávnění Key Vault.</span><span class="sxs-lookup"><span data-stu-id="e0f54-174">Using Azure Key Vault, lack of permissions to the Key Vault.</span></span> <span data-ttu-id="e0f54-175">Zkontrolujte zásady přístupu v cílovém Key Vault, abyste měli jistotu, že jsou udělená správná oprávnění.</span><span class="sxs-lookup"><span data-stu-id="e0f54-175">Check the access policies in the targeted Key Vault to ensure that the correct permissions are granted.</span></span>

### <a name="50031-ancm-failed-to-find-native-dependencies"></a><span data-ttu-id="e0f54-176">500,31 ANCM nepovedlo se najít nativní závislosti.</span><span class="sxs-lookup"><span data-stu-id="e0f54-176">500.31 ANCM Failed to Find Native Dependencies</span></span>

<span data-ttu-id="e0f54-177">Pracovní proces se nezdaří.</span><span class="sxs-lookup"><span data-stu-id="e0f54-177">The worker process fails.</span></span> <span data-ttu-id="e0f54-178">Aplikace se nespustí.</span><span class="sxs-lookup"><span data-stu-id="e0f54-178">The app doesn't start.</span></span>

<span data-ttu-id="e0f54-179">[Modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module) se pokusí spustit modul runtime .NET Core v procesu, ale jeho spuštění se nezdařilo.</span><span class="sxs-lookup"><span data-stu-id="e0f54-179">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the .NET Core runtime in-process, but it fails to start.</span></span> <span data-ttu-id="e0f54-180">Nejběžnější příčinou tohoto selhání při spuštění je, že modul runtime `Microsoft.NETCore.App` nebo `Microsoft.AspNetCore.App` není nainstalován.</span><span class="sxs-lookup"><span data-stu-id="e0f54-180">The most common cause of this startup failure is when the `Microsoft.NETCore.App` or `Microsoft.AspNetCore.App` runtime isn't installed.</span></span> <span data-ttu-id="e0f54-181">Pokud je aplikace nasazená na cílovou ASP.NET Core 3,0 a tato verze v počítači neexistuje, dojde k této chybě.</span><span class="sxs-lookup"><span data-stu-id="e0f54-181">If the app is deployed to target ASP.NET Core 3.0 and that version doesn't exist on the machine, this error occurs.</span></span> <span data-ttu-id="e0f54-182">Následuje příklad chybové zprávy:</span><span class="sxs-lookup"><span data-stu-id="e0f54-182">An example error message follows:</span></span>

```
The specified framework 'Microsoft.NETCore.App', version '3.0.0' was not found.
  - The following frameworks were found:
      2.2.1 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview5-27626-15 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27713-13 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27714-15 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27723-08 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
```

<span data-ttu-id="e0f54-183">Chybová zpráva obsahuje seznam všech nainstalovaných verzí rozhraní .NET Core a verze, kterou aplikace požaduje.</span><span class="sxs-lookup"><span data-stu-id="e0f54-183">The error message lists all the installed .NET Core versions and the version requested by the app.</span></span> <span data-ttu-id="e0f54-184">Chcete-li tuto chybu vyřešit, postupujte takto:</span><span class="sxs-lookup"><span data-stu-id="e0f54-184">To fix this error, either:</span></span>

* <span data-ttu-id="e0f54-185">Na počítač nainstalujte odpovídající verzi .NET Core.</span><span class="sxs-lookup"><span data-stu-id="e0f54-185">Install the appropriate version of .NET Core on the machine.</span></span>
* <span data-ttu-id="e0f54-186">Změňte aplikaci na cílovou verzi .NET Core, která je na počítači přítomná.</span><span class="sxs-lookup"><span data-stu-id="e0f54-186">Change the app to target a version of .NET Core that's present on the machine.</span></span>
* <span data-ttu-id="e0f54-187">Publikujte aplikaci jako samostatné [nasazení](/dotnet/core/deploying/#self-contained-deployments-scd).</span><span class="sxs-lookup"><span data-stu-id="e0f54-187">Publish the app as a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>

<span data-ttu-id="e0f54-188">Při spuštění ve vývoji (`ASPNETCORE_ENVIRONMENT` proměnná prostředí je nastavená na `Development`) se konkrétní chyba zapíše do odpovědi HTTP.</span><span class="sxs-lookup"><span data-stu-id="e0f54-188">When running in development (the `ASPNETCORE_ENVIRONMENT` environment variable is set to `Development`), the specific error is written to the HTTP response.</span></span> <span data-ttu-id="e0f54-189">Příčinu selhání spuštění procesu se nachází také v protokolu událostí aplikace.</span><span class="sxs-lookup"><span data-stu-id="e0f54-189">The cause of a process startup failure is also found in the Application Event Log.</span></span>

### <a name="50032-ancm-failed-to-load-dll"></a><span data-ttu-id="e0f54-190">500,32 ANCM načtení knihovny DLL se nezdařilo</span><span class="sxs-lookup"><span data-stu-id="e0f54-190">500.32 ANCM Failed to Load dll</span></span>

<span data-ttu-id="e0f54-191">Pracovní proces se nezdaří.</span><span class="sxs-lookup"><span data-stu-id="e0f54-191">The worker process fails.</span></span> <span data-ttu-id="e0f54-192">Aplikace se nespustí.</span><span class="sxs-lookup"><span data-stu-id="e0f54-192">The app doesn't start.</span></span>

<span data-ttu-id="e0f54-193">Nejběžnější příčinou této chyby je, že aplikace je publikovaná pro nekompatibilní architekturu procesoru.</span><span class="sxs-lookup"><span data-stu-id="e0f54-193">The most common cause for this error is that the app is published for an incompatible processor architecture.</span></span> <span data-ttu-id="e0f54-194">Pokud je pracovní proces spuštěn jako 32 aplikace a aplikace byla publikována na cílovou 64-bit, dojde k této chybě.</span><span class="sxs-lookup"><span data-stu-id="e0f54-194">If the worker process is running as a 32-bit app and the app was published to target 64-bit, this error occurs.</span></span>

<span data-ttu-id="e0f54-195">Chcete-li tuto chybu vyřešit, postupujte takto:</span><span class="sxs-lookup"><span data-stu-id="e0f54-195">To fix this error, either:</span></span>

* <span data-ttu-id="e0f54-196">Znovu publikujte aplikaci pro stejnou architekturu procesoru jako pracovní proces.</span><span class="sxs-lookup"><span data-stu-id="e0f54-196">Republish the app for the same processor architecture as the worker process.</span></span>
* <span data-ttu-id="e0f54-197">Publikování aplikace jako [nasazení závislého na rozhraní](/dotnet/core/deploying/#framework-dependent-executables-fde).</span><span class="sxs-lookup"><span data-stu-id="e0f54-197">Publish the app as a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-executables-fde).</span></span>

### <a name="50033-ancm-request-handler-load-failure"></a><span data-ttu-id="e0f54-198">500,33 selhání načtení obslužné rutiny žádosti ANCM</span><span class="sxs-lookup"><span data-stu-id="e0f54-198">500.33 ANCM Request Handler Load Failure</span></span>

<span data-ttu-id="e0f54-199">Pracovní proces se nezdaří.</span><span class="sxs-lookup"><span data-stu-id="e0f54-199">The worker process fails.</span></span> <span data-ttu-id="e0f54-200">Aplikace se nespustí.</span><span class="sxs-lookup"><span data-stu-id="e0f54-200">The app doesn't start.</span></span>

<span data-ttu-id="e0f54-201">Aplikace neodkazovala na rozhraní `Microsoft.AspNetCore.App` Framework.</span><span class="sxs-lookup"><span data-stu-id="e0f54-201">The app didn't reference the `Microsoft.AspNetCore.App` framework.</span></span> <span data-ttu-id="e0f54-202">[Modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module)může hostovat jenom aplikace cílené na rozhraní `Microsoft.AspNetCore.App` Framework.</span><span class="sxs-lookup"><span data-stu-id="e0f54-202">Only apps targeting the `Microsoft.AspNetCore.App` framework can be hosted by the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span>

<span data-ttu-id="e0f54-203">Chcete-li tuto chybu opravit, zkontrolujte, zda je aplikace cílena na rozhraní `Microsoft.AspNetCore.App` Framework.</span><span class="sxs-lookup"><span data-stu-id="e0f54-203">To fix this error, confirm that the app is targeting the `Microsoft.AspNetCore.App` framework.</span></span> <span data-ttu-id="e0f54-204">Zkontrolujte `.runtimeconfig.json` a ověřte rozhraní, které cílí na aplikaci.</span><span class="sxs-lookup"><span data-stu-id="e0f54-204">Check the `.runtimeconfig.json` to verify the framework targeted by the app.</span></span>

### <a name="50034-ancm-mixed-hosting-models-not-supported"></a><span data-ttu-id="e0f54-205">500,34 ANCM smíšené modely hostování se nepodporují.</span><span class="sxs-lookup"><span data-stu-id="e0f54-205">500.34 ANCM Mixed Hosting Models Not Supported</span></span>

<span data-ttu-id="e0f54-206">Pracovní proces nemůže v jednom procesu spustit jak aplikaci v rámci procesu, tak i aplikaci mimo proces.</span><span class="sxs-lookup"><span data-stu-id="e0f54-206">The worker process can't run both an in-process app and an out-of-process app in the same process.</span></span>

<span data-ttu-id="e0f54-207">Chcete-li tuto chybu opravit, spusťte aplikace v samostatných fondech aplikací služby IIS.</span><span class="sxs-lookup"><span data-stu-id="e0f54-207">To fix this error, run apps in separate IIS application pools.</span></span>

### <a name="50035-ancm-multiple-in-process-applications-in-same-process"></a><span data-ttu-id="e0f54-208">500,35 ANCM více vnitroprocesové aplikací ve stejném procesu</span><span class="sxs-lookup"><span data-stu-id="e0f54-208">500.35 ANCM Multiple In-Process Applications in same Process</span></span>

<span data-ttu-id="e0f54-209">Pracovní proces nemůže v jednom procesu spustit více vnitroprocesové aplikací.</span><span class="sxs-lookup"><span data-stu-id="e0f54-209">The worker process can't run multiple in-process apps in the same process.</span></span>

<span data-ttu-id="e0f54-210">Chcete-li tuto chybu opravit, spusťte aplikace v samostatných fondech aplikací služby IIS.</span><span class="sxs-lookup"><span data-stu-id="e0f54-210">To fix this error, run apps in separate IIS application pools.</span></span>

### <a name="50036-ancm-out-of-process-handler-load-failure"></a><span data-ttu-id="e0f54-211">500,36 ANCM selhání načtení obslužné rutiny mimo proces</span><span class="sxs-lookup"><span data-stu-id="e0f54-211">500.36 ANCM Out-Of-Process Handler Load Failure</span></span>

<span data-ttu-id="e0f54-212">Obslužná rutina žádosti mimo proces, *aspnetcorev2_outofprocess. dll*, není vedle souboru *aspnetcorev2. dll* .</span><span class="sxs-lookup"><span data-stu-id="e0f54-212">The out-of-process request handler, *aspnetcorev2_outofprocess.dll*, isn't next to the *aspnetcorev2.dll* file.</span></span> <span data-ttu-id="e0f54-213">To označuje poškozenou instalaci [modulu ASP.NET Core](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="e0f54-213">This indicates a corrupted installation of the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span>

<span data-ttu-id="e0f54-214">Chcete-li tuto chybu opravit, opravte instalaci [hostující sady .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) (pro IIS) nebo Visual Studio (pro IIS Express).</span><span class="sxs-lookup"><span data-stu-id="e0f54-214">To fix this error, repair the installation of the [.NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) (for IIS) or Visual Studio (for IIS Express).</span></span>

### <a name="50037-ancm-failed-to-start-within-startup-time-limit"></a><span data-ttu-id="e0f54-215">500,37 ANCM se nepodařilo spustit v časovém limitu spuštění.</span><span class="sxs-lookup"><span data-stu-id="e0f54-215">500.37 ANCM Failed to Start Within Startup Time Limit</span></span>

<span data-ttu-id="e0f54-216">ANCM se nepovedlo spustit v rámci časového limitu spuštění nechte.</span><span class="sxs-lookup"><span data-stu-id="e0f54-216">ANCM failed to start within the provied startup time limit.</span></span> <span data-ttu-id="e0f54-217">Ve výchozím nastavení je časový limit 120 sekund.</span><span class="sxs-lookup"><span data-stu-id="e0f54-217">By default, the timeout is 120 seconds.</span></span>

<span data-ttu-id="e0f54-218">K této chybě může dojít, když ve stejném počítači spustíte velký počet aplikací.</span><span class="sxs-lookup"><span data-stu-id="e0f54-218">This error can occur when starting a large number of apps on the same machine.</span></span> <span data-ttu-id="e0f54-219">Kontrolovat špičky využití procesoru a paměti na serveru při spuštění.</span><span class="sxs-lookup"><span data-stu-id="e0f54-219">Check for CPU/Memory usage spikes on the server during startup.</span></span> <span data-ttu-id="e0f54-220">Možná budete muset rozložit proces spouštění více aplikací.</span><span class="sxs-lookup"><span data-stu-id="e0f54-220">You may need to stagger the startup process of multiple apps.</span></span>

::: moniker-end

### <a name="5025-process-failure"></a><span data-ttu-id="e0f54-221">502.5 zpracovat selhání</span><span class="sxs-lookup"><span data-stu-id="e0f54-221">502.5 Process Failure</span></span>

<span data-ttu-id="e0f54-222">Pracovní proces se nezdaří.</span><span class="sxs-lookup"><span data-stu-id="e0f54-222">The worker process fails.</span></span> <span data-ttu-id="e0f54-223">Aplikace se nespustí.</span><span class="sxs-lookup"><span data-stu-id="e0f54-223">The app doesn't start.</span></span>

<span data-ttu-id="e0f54-224">[Modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module) se pokusí spustit pracovní proces, ale jeho spuštění se nezdařilo.</span><span class="sxs-lookup"><span data-stu-id="e0f54-224">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the worker process but it fails to start.</span></span> <span data-ttu-id="e0f54-225">Příčinou selhání spuštění procesu může být obvykle určení z položek protokolu událostí aplikace a protokolu stdout modulu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e0f54-225">The cause of a process startup failure can usually be determined from entries in the Application Event Log and the ASP.NET Core Module stdout log.</span></span>

<span data-ttu-id="e0f54-226">Běžné chyby je, že aplikace je špatně nakonfigurovaný. kvůli cílení na určitou verzi rozhraní framework sdílené ASP.NET Core, který není k dispozici.</span><span class="sxs-lookup"><span data-stu-id="e0f54-226">A common failure condition is the app is misconfigured due to targeting a version of the ASP.NET Core shared framework that isn't present.</span></span> <span data-ttu-id="e0f54-227">Zkontrolujte, jaké verze rozhraní framework ASP.NET Core sdílené jsou nainstalovány v cílovém počítači.</span><span class="sxs-lookup"><span data-stu-id="e0f54-227">Check which versions of the ASP.NET Core shared framework are installed on the target machine.</span></span> <span data-ttu-id="e0f54-228">*Sdílené rozhraní* je sada sestavení (soubory *. dll* ), které jsou nainstalovány na počítači a na které odkazuje Metapackage jako `Microsoft.AspNetCore.App`.</span><span class="sxs-lookup"><span data-stu-id="e0f54-228">The *shared framework* is the set of assemblies (*.dll* files) that are installed on the machine and referenced by a metapackage such as `Microsoft.AspNetCore.App`.</span></span> <span data-ttu-id="e0f54-229">Odkaz Metapackage může určovat minimální požadovanou verzi.</span><span class="sxs-lookup"><span data-stu-id="e0f54-229">The metapackage reference can specify a minimum required version.</span></span> <span data-ttu-id="e0f54-230">Další informace najdete v tématu [sdílené rozhraní](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span><span class="sxs-lookup"><span data-stu-id="e0f54-230">For more information, see [The shared framework](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span></span>

<span data-ttu-id="e0f54-231">*502.5 selhání procesu* při hostování nebo aplikace chybná konfigurace způsobí, že se pracovní proces selže, vrátí se chybová stránka:</span><span class="sxs-lookup"><span data-stu-id="e0f54-231">The *502.5 Process Failure* error page is returned when a hosting or app misconfiguration causes the worker process to fail:</span></span>

### <a name="failed-to-start-application-errorcode-0x800700c1"></a><span data-ttu-id="e0f54-232">Nepovedlo se spustit aplikaci (kód chyby "0x800700c1")</span><span class="sxs-lookup"><span data-stu-id="e0f54-232">Failed to start application (ErrorCode '0x800700c1')</span></span>

```
EventID: 1010
Source: IIS AspNetCore Module V2
Failed to start application '/LM/W3SVC/6/ROOT/', ErrorCode '0x800700c1'.
```

<span data-ttu-id="e0f54-233">Aplikaci se nepodařilo spustit, protože sestavení aplikace ( *.dll*) nelze načíst.</span><span class="sxs-lookup"><span data-stu-id="e0f54-233">The app failed to start because the app's assembly (*.dll*) couldn't be loaded.</span></span>

<span data-ttu-id="e0f54-234">Tato chyba nastane, pokud došlo k neshodě bitové verze mezi publikované aplikace a proces w3wp/iisexpress.</span><span class="sxs-lookup"><span data-stu-id="e0f54-234">This error occurs when there's a bitness mismatch between the published app and the w3wp/iisexpress process.</span></span>

<span data-ttu-id="e0f54-235">Ověřte správnost nastavení 32-bit fondu aplikací:</span><span class="sxs-lookup"><span data-stu-id="e0f54-235">Confirm that the app pool's 32-bit setting is correct:</span></span>

1. <span data-ttu-id="e0f54-236">Vyberte fond aplikací ve Správci služby IIS na **fondy aplikací**.</span><span class="sxs-lookup"><span data-stu-id="e0f54-236">Select the app pool in IIS Manager's **Application Pools**.</span></span>
1. <span data-ttu-id="e0f54-237">Vyberte **Upřesnit nastavení** pod **upravit fond aplikací** v **akce** panelu.</span><span class="sxs-lookup"><span data-stu-id="e0f54-237">Select **Advanced Settings** under **Edit Application Pool** in the **Actions** panel.</span></span>
1. <span data-ttu-id="e0f54-238">Nastavte **povolit 32bitové aplikace**:</span><span class="sxs-lookup"><span data-stu-id="e0f54-238">Set **Enable 32-Bit Applications**:</span></span>
   * <span data-ttu-id="e0f54-239">Pokud nasazení (x86) 32bitové aplikace, nastavte hodnotu na `True`.</span><span class="sxs-lookup"><span data-stu-id="e0f54-239">If deploying a 32-bit (x86) app, set the value to `True`.</span></span>
   * <span data-ttu-id="e0f54-240">Pokud nasazení (x64) 64bitové aplikace, nastavte hodnotu na `False`.</span><span class="sxs-lookup"><span data-stu-id="e0f54-240">If deploying a 64-bit (x64) app, set the value to `False`.</span></span>

<span data-ttu-id="e0f54-241">Ověřte, že mezi vlastností `<Platform>` MSBuild v souboru projektu a publikovaným bitová verzeem aplikace nedochází ke konfliktu.</span><span class="sxs-lookup"><span data-stu-id="e0f54-241">Confirm that there isn't a conflict between a `<Platform>` MSBuild property in the project file and the published bitness of the app.</span></span>

### <a name="connection-reset"></a><span data-ttu-id="e0f54-242">Obnovení připojení</span><span class="sxs-lookup"><span data-stu-id="e0f54-242">Connection reset</span></span>

<span data-ttu-id="e0f54-243">Pokud dojde k chybě po odeslání hlavičky, bude příliš pozdě pro server k odeslání **500 – Interní chyba serveru** , když dojde k chybě.</span><span class="sxs-lookup"><span data-stu-id="e0f54-243">If an error occurs after the headers are sent, it's too late for the server to send a **500 Internal Server Error** when an error occurs.</span></span> <span data-ttu-id="e0f54-244">Často se to stane, když dojde k chybě při serializaci složitých objektů pro odpověď.</span><span class="sxs-lookup"><span data-stu-id="e0f54-244">This often happens when an error occurs during the serialization of complex objects for a response.</span></span> <span data-ttu-id="e0f54-245">Tento typ chyby se zobrazí jako *obnovení připojení* chyba na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="e0f54-245">This type of error appears as a *connection reset* error on the client.</span></span> <span data-ttu-id="e0f54-246">[Protokolování aplikací](xref:fundamentals/logging/index) mohou pomoci při řešení těchto typů chyb.</span><span class="sxs-lookup"><span data-stu-id="e0f54-246">[Application logging](xref:fundamentals/logging/index) can help troubleshoot these types of errors.</span></span>

### <a name="default-startup-limits"></a><span data-ttu-id="e0f54-247">Výchozí omezení při spuštění</span><span class="sxs-lookup"><span data-stu-id="e0f54-247">Default startup limits</span></span>

<span data-ttu-id="e0f54-248">[ASP.NET Core modul](xref:host-and-deploy/aspnet-core-module) je nakonfigurovaný s výchozí *startupTimeLimitou* 120 sekund.</span><span class="sxs-lookup"><span data-stu-id="e0f54-248">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) is configured with a default *startupTimeLimit* of 120 seconds.</span></span> <span data-ttu-id="e0f54-249">Když necháte na výchozí hodnotu, aplikace může trvat až dvě minuty, spusťte před modulu protokoly selhání procesu.</span><span class="sxs-lookup"><span data-stu-id="e0f54-249">When left at the default value, an app may take up to two minutes to start before the module logs a process failure.</span></span> <span data-ttu-id="e0f54-250">Informace o konfiguraci modulu najdete v tématu [atributy elementu aspNetCore](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span><span class="sxs-lookup"><span data-stu-id="e0f54-250">For information on configuring the module, see [Attributes of the aspNetCore element](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span></span>

## <a name="troubleshoot-on-azure-app-service"></a><span data-ttu-id="e0f54-251">Řešení potíží na Azure App Service</span><span class="sxs-lookup"><span data-stu-id="e0f54-251">Troubleshoot on Azure App Service</span></span>

[!INCLUDE [Azure App Service Preview Notice](~/includes/azure-apps-preview-notice.md)]

### <a name="application-event-log-azure-app-service"></a><span data-ttu-id="e0f54-252">Protokol událostí aplikace (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="e0f54-252">Application Event Log (Azure App Service)</span></span>

<span data-ttu-id="e0f54-253">Chcete-li získat přístup k protokolu událostí aplikace, použijte okno **Diagnostika a řešení problémů** v Azure Portal:</span><span class="sxs-lookup"><span data-stu-id="e0f54-253">To access the Application Event Log, use the **Diagnose and solve problems** blade in the Azure portal:</span></span>

1. <span data-ttu-id="e0f54-254">V Azure Portal otevřete aplikaci v **App Services**.</span><span class="sxs-lookup"><span data-stu-id="e0f54-254">In the Azure portal, open the app in **App Services**.</span></span>
1. <span data-ttu-id="e0f54-255">Vyberte **Diagnostikovat a řešit problémy**.</span><span class="sxs-lookup"><span data-stu-id="e0f54-255">Select **Diagnose and solve problems**.</span></span>
1. <span data-ttu-id="e0f54-256">Vyberte **diagnostické nástroje** záhlaví.</span><span class="sxs-lookup"><span data-stu-id="e0f54-256">Select the **Diagnostic Tools** heading.</span></span>
1. <span data-ttu-id="e0f54-257">V nabídce **nástroje podpory**vyberte tlačítko **události aplikace** .</span><span class="sxs-lookup"><span data-stu-id="e0f54-257">Under **Support Tools**, select the **Application Events** button.</span></span>
1. <span data-ttu-id="e0f54-258">Projděte si nejnovější chybu, kterou poskytla položka *IIS AspNetCoreModule* nebo *IIS AspNetCoreModule v2* ve **zdrojovém** sloupci.</span><span class="sxs-lookup"><span data-stu-id="e0f54-258">Examine the latest error provided by the *IIS AspNetCoreModule* or *IIS AspNetCoreModule V2* entry in the **Source** column.</span></span>

<span data-ttu-id="e0f54-259">Alternativou k použití okna **diagnostikovat a řešit problémy** je kontrola souboru protokolu událostí aplikace přímo pomocí [Kudu](https://github.com/projectkudu/kudu/wiki):</span><span class="sxs-lookup"><span data-stu-id="e0f54-259">An alternative to using the **Diagnose and solve problems** blade is to examine the Application Event Log file directly using [Kudu](https://github.com/projectkudu/kudu/wiki):</span></span>

1. <span data-ttu-id="e0f54-260">Otevřete **Rozšířené nástroje** v oblasti **vývojové nástroje** .</span><span class="sxs-lookup"><span data-stu-id="e0f54-260">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="e0f54-261">Vyberte tlačítko **přejít&rarr;** .</span><span class="sxs-lookup"><span data-stu-id="e0f54-261">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="e0f54-262">Konzola Kudu se otevře v novém okně nebo záložce prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="e0f54-262">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="e0f54-263">Pomocí navigačního panelu v horní části stránky otevřete **konzolu ladění** a vyberte **cmd**.</span><span class="sxs-lookup"><span data-stu-id="e0f54-263">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="e0f54-264">Otevřete složku **soubory protokolů** .</span><span class="sxs-lookup"><span data-stu-id="e0f54-264">Open the **LogFiles** folder.</span></span>
1. <span data-ttu-id="e0f54-265">Vyberte ikonu tužky vedle souboru *EventLog. XML* .</span><span class="sxs-lookup"><span data-stu-id="e0f54-265">Select the pencil icon next to the *eventlog.xml* file.</span></span>
1. <span data-ttu-id="e0f54-266">Projděte si protokol.</span><span class="sxs-lookup"><span data-stu-id="e0f54-266">Examine the log.</span></span> <span data-ttu-id="e0f54-267">Posuňte se do dolní části protokolu, abyste viděli nejaktuálnější události.</span><span class="sxs-lookup"><span data-stu-id="e0f54-267">Scroll to the bottom of the log to see the most recent events.</span></span>

### <a name="run-the-app-in-the-kudu-console"></a><span data-ttu-id="e0f54-268">Spuštění aplikace v konzole Kudu</span><span class="sxs-lookup"><span data-stu-id="e0f54-268">Run the app in the Kudu console</span></span>

<span data-ttu-id="e0f54-269">Mnoho chyb při spuštění nevytvářejí užitečné informace v protokolu událostí aplikace.</span><span class="sxs-lookup"><span data-stu-id="e0f54-269">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="e0f54-270">Tuto chybu můžete zjistit spuštěním aplikace v konzole vzdáleného spuštění [Kudu](https://github.com/projectkudu/kudu/wiki) :</span><span class="sxs-lookup"><span data-stu-id="e0f54-270">You can run the app in the [Kudu](https://github.com/projectkudu/kudu/wiki) Remote Execution Console to discover the error:</span></span>

1. <span data-ttu-id="e0f54-271">Otevřete **Rozšířené nástroje** v oblasti **vývojové nástroje** .</span><span class="sxs-lookup"><span data-stu-id="e0f54-271">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="e0f54-272">Vyberte tlačítko **přejít&rarr;** .</span><span class="sxs-lookup"><span data-stu-id="e0f54-272">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="e0f54-273">Konzola Kudu se otevře v novém okně nebo záložce prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="e0f54-273">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="e0f54-274">Pomocí navigačního panelu v horní části stránky otevřete **konzolu ladění** a vyberte **cmd**.</span><span class="sxs-lookup"><span data-stu-id="e0f54-274">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>

#### <a name="test-a-32-bit-x86-app"></a><span data-ttu-id="e0f54-275">Testování 32 (x86) aplikace</span><span class="sxs-lookup"><span data-stu-id="e0f54-275">Test a 32-bit (x86) app</span></span>

<span data-ttu-id="e0f54-276">**Aktuální verze**</span><span class="sxs-lookup"><span data-stu-id="e0f54-276">**Current release**</span></span>

1. `cd d:\home\site\wwwroot`
1. <span data-ttu-id="e0f54-277">Spusťte aplikaci:</span><span class="sxs-lookup"><span data-stu-id="e0f54-277">Run the app:</span></span>
   * <span data-ttu-id="e0f54-278">Pokud je aplikace [nasazení závisí na architektuře](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span><span class="sxs-lookup"><span data-stu-id="e0f54-278">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

     ```dotnetcli
     dotnet .\{ASSEMBLY NAME}.dll
     ```

   * <span data-ttu-id="e0f54-279">Pokud je aplikace [samostatná nasazení](/dotnet/core/deploying/#self-contained-deployments-scd):</span><span class="sxs-lookup"><span data-stu-id="e0f54-279">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

     ```console
     {ASSEMBLY NAME}.exe
     ```

<span data-ttu-id="e0f54-280">Výstup konzoly z aplikace, v němž se zobrazují všechny chyby, je předán do konzoly Kudu.</span><span class="sxs-lookup"><span data-stu-id="e0f54-280">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="e0f54-281">**Nasazení závislé na architektuře spuštěné ve verzi Preview**</span><span class="sxs-lookup"><span data-stu-id="e0f54-281">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="e0f54-282">*Vyžaduje instalaci rozšíření webu ASP.NET Core {VERSION} (x86) Runtime.*</span><span class="sxs-lookup"><span data-stu-id="e0f54-282">*Requires installing the ASP.NET Core {VERSION} (x86) Runtime site extension.*</span></span>

1. <span data-ttu-id="e0f54-283">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32` (`{X.Y}` je verze modulu runtime)</span><span class="sxs-lookup"><span data-stu-id="e0f54-283">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="e0f54-284">Spusťte aplikaci: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="e0f54-284">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="e0f54-285">Výstup konzoly z aplikace, v němž se zobrazují všechny chyby, je předán do konzoly Kudu.</span><span class="sxs-lookup"><span data-stu-id="e0f54-285">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

#### <a name="test-a-64-bit-x64-app"></a><span data-ttu-id="e0f54-286">Testování 64 aplikace (x64)</span><span class="sxs-lookup"><span data-stu-id="e0f54-286">Test a 64-bit (x64) app</span></span>

<span data-ttu-id="e0f54-287">**Aktuální verze**</span><span class="sxs-lookup"><span data-stu-id="e0f54-287">**Current release**</span></span>

* <span data-ttu-id="e0f54-288">Pokud je aplikace [nasazením závislého na rozhraní](/dotnet/core/deploying/#framework-dependent-deployments-fdd)64 (x64):</span><span class="sxs-lookup"><span data-stu-id="e0f54-288">If the app is a 64-bit (x64) [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>
  1. `cd D:\Program Files\dotnet`
  1. <span data-ttu-id="e0f54-289">Spusťte aplikaci: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="e0f54-289">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>
* <span data-ttu-id="e0f54-290">Pokud je aplikace [samostatná nasazení](/dotnet/core/deploying/#self-contained-deployments-scd):</span><span class="sxs-lookup"><span data-stu-id="e0f54-290">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>
  1. `cd D:\home\site\wwwroot`
  1. <span data-ttu-id="e0f54-291">Spusťte aplikaci: `{ASSEMBLY NAME}.exe`</span><span class="sxs-lookup"><span data-stu-id="e0f54-291">Run the app: `{ASSEMBLY NAME}.exe`</span></span>

<span data-ttu-id="e0f54-292">Výstup konzoly z aplikace, v němž se zobrazují všechny chyby, je předán do konzoly Kudu.</span><span class="sxs-lookup"><span data-stu-id="e0f54-292">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="e0f54-293">**Nasazení závislé na architektuře spuštěné ve verzi Preview**</span><span class="sxs-lookup"><span data-stu-id="e0f54-293">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="e0f54-294">*Vyžaduje instalaci rozšíření webu ASP.NET Core {VERSION} (x64) Runtime.*</span><span class="sxs-lookup"><span data-stu-id="e0f54-294">*Requires installing the ASP.NET Core {VERSION} (x64) Runtime site extension.*</span></span>

1. <span data-ttu-id="e0f54-295">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64` (`{X.Y}` je verze modulu runtime)</span><span class="sxs-lookup"><span data-stu-id="e0f54-295">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="e0f54-296">Spusťte aplikaci: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="e0f54-296">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="e0f54-297">Výstup konzoly z aplikace, v němž se zobrazují všechny chyby, je předán do konzoly Kudu.</span><span class="sxs-lookup"><span data-stu-id="e0f54-297">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

### <a name="aspnet-core-module-stdout-log-azure-app-service"></a><span data-ttu-id="e0f54-298">Protokol stdout v modulu ASP.NET Core (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="e0f54-298">ASP.NET Core Module stdout log (Azure App Service)</span></span>

<span data-ttu-id="e0f54-299">Protokol stdout modulu ASP.NET Coree často zaznamenává užitečné chybové zprávy, které se nenašly v protokolu událostí aplikace.</span><span class="sxs-lookup"><span data-stu-id="e0f54-299">The ASP.NET Core Module stdout log often records useful error messages not found in the Application Event Log.</span></span> <span data-ttu-id="e0f54-300">Povolení a zobrazení protokolů stdout:</span><span class="sxs-lookup"><span data-stu-id="e0f54-300">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="e0f54-301">Přejděte do okna **diagnostikovat a řešit problémy** v Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="e0f54-301">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="e0f54-302">V části **Vybrat kategorii problému**vyberte tlačítko pro **webovou aplikaci** .</span><span class="sxs-lookup"><span data-stu-id="e0f54-302">Under **SELECT PROBLEM CATEGORY**, select the **Web App Down** button.</span></span>
1. <span data-ttu-id="e0f54-303">V části **navrhovaná řešení** > **Povolit přesměrování protokolu stdout**kliknutím na tlačítko **otevřete konzolu Kudu a upravte soubor Web. config**.</span><span class="sxs-lookup"><span data-stu-id="e0f54-303">Under **Suggested Solutions** > **Enable Stdout Log Redirection**, select the button to **Open Kudu Console to edit Web.Config**.</span></span>
1. <span data-ttu-id="e0f54-304">V **konzole diagnostiky**Kudu otevřete složky v **lokalitě** cesty > **wwwroot**.</span><span class="sxs-lookup"><span data-stu-id="e0f54-304">In the Kudu **Diagnostic Console**, open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="e0f54-305">Posuňte se dolů a odhalte soubor *Web. config* v dolní části seznamu.</span><span class="sxs-lookup"><span data-stu-id="e0f54-305">Scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="e0f54-306">Klikněte na ikonu tužky vedle souboru *Web. config* .</span><span class="sxs-lookup"><span data-stu-id="e0f54-306">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="e0f54-307">Nastavte **stdoutLogEnabled** na `true` a změňte cestu **stdoutLogFile** na: `\\?\%home%\LogFiles\stdout`.</span><span class="sxs-lookup"><span data-stu-id="e0f54-307">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="e0f54-308">Vyberte **Uložit** a uložte aktualizovaný soubor *Web. config* .</span><span class="sxs-lookup"><span data-stu-id="e0f54-308">Select **Save** to save the updated *web.config* file.</span></span>
1. <span data-ttu-id="e0f54-309">Vytvořte žádost do aplikace.</span><span class="sxs-lookup"><span data-stu-id="e0f54-309">Make a request to the app.</span></span>
1. <span data-ttu-id="e0f54-310">Vraťte se na Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="e0f54-310">Return to the Azure portal.</span></span> <span data-ttu-id="e0f54-311">V oblasti **vývojové nástroje** vyberte okno **Pokročilé nástroje** .</span><span class="sxs-lookup"><span data-stu-id="e0f54-311">Select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="e0f54-312">Vyberte tlačítko **přejít&rarr;** .</span><span class="sxs-lookup"><span data-stu-id="e0f54-312">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="e0f54-313">Konzola Kudu se otevře v novém okně nebo záložce prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="e0f54-313">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="e0f54-314">Pomocí navigačního panelu v horní části stránky otevřete **konzolu ladění** a vyberte **cmd**.</span><span class="sxs-lookup"><span data-stu-id="e0f54-314">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="e0f54-315">Vyberte složku **soubory protokolů** .</span><span class="sxs-lookup"><span data-stu-id="e0f54-315">Select the **LogFiles** folder.</span></span>
1. <span data-ttu-id="e0f54-316">Zkontrolujte **upravený** sloupec a vyberte ikonu tužky a upravte protokol stdout s nejnovějším datem úpravy.</span><span class="sxs-lookup"><span data-stu-id="e0f54-316">Inspect the **Modified** column and select the pencil icon to edit the stdout log with the latest modification date.</span></span>
1. <span data-ttu-id="e0f54-317">Po otevření souboru protokolu se zobrazí chyba.</span><span class="sxs-lookup"><span data-stu-id="e0f54-317">When the log file opens, the error is displayed.</span></span>

<span data-ttu-id="e0f54-318">Zakázat protokolování stdout při odstraňování potíží:</span><span class="sxs-lookup"><span data-stu-id="e0f54-318">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="e0f54-319">V **diagnostické konzole**Kudu se vraťte do **lokality** cesty > **wwwroot** , aby se soubor *Web. config* vykryl.</span><span class="sxs-lookup"><span data-stu-id="e0f54-319">In the Kudu **Diagnostic Console**, return to the path **site** > **wwwroot** to reveal the *web.config* file.</span></span> <span data-ttu-id="e0f54-320">Otevřete soubor **Web. config** znovu výběrem ikony tužky.</span><span class="sxs-lookup"><span data-stu-id="e0f54-320">Open the **web.config** file again by selecting the pencil icon.</span></span>
1. <span data-ttu-id="e0f54-321">Nastavte **stdoutLogEnabled** k `false`.</span><span class="sxs-lookup"><span data-stu-id="e0f54-321">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="e0f54-322">Vyberte **Uložit** a soubor uložte.</span><span class="sxs-lookup"><span data-stu-id="e0f54-322">Select **Save** to save the file.</span></span>

<span data-ttu-id="e0f54-323">Další informace najdete v tématu <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span><span class="sxs-lookup"><span data-stu-id="e0f54-323">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

> [!WARNING]
> <span data-ttu-id="e0f54-324">Nepodařilo se zakázat protokol stdout může vést k selhání aplikace nebo serveru.</span><span class="sxs-lookup"><span data-stu-id="e0f54-324">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="e0f54-325">Neexistuje žádné omezení velikosti souboru protokolu nebo počet souborů protokolů, které jsou vytvořeny.</span><span class="sxs-lookup"><span data-stu-id="e0f54-325">There's no limit on log file size or the number of log files created.</span></span> <span data-ttu-id="e0f54-326">K řešení problémů se spouštěním aplikací použijte pouze protokolování STDOUT.</span><span class="sxs-lookup"><span data-stu-id="e0f54-326">Only use stdout logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="e0f54-327">Pro obecné protokolování v aplikaci ASP.NET Core po spuštění použijte knihovnu protokolování, která omezuje velikost souboru protokolu a otočí protokoly.</span><span class="sxs-lookup"><span data-stu-id="e0f54-327">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="e0f54-328">Další informace najdete v tématu [zprostředkovatele přihlášení třetí strany](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="e0f54-328">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

::: moniker range=">= aspnetcore-2.2"

### <a name="aspnet-core-module-debug-log-azure-app-service"></a><span data-ttu-id="e0f54-329">Protokol ladění modulu ASP.NET Core (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="e0f54-329">ASP.NET Core Module debug log (Azure App Service)</span></span>

<span data-ttu-id="e0f54-330">Protokol ladění ASP.NET Core modulu poskytuje další, hlubší protokolování z modulu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e0f54-330">The ASP.NET Core Module debug log provides additional, deeper logging from the ASP.NET Core Module.</span></span> <span data-ttu-id="e0f54-331">Povolení a zobrazení protokolů stdout:</span><span class="sxs-lookup"><span data-stu-id="e0f54-331">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="e0f54-332">Pro povolení rozšířeného diagnostického protokolu proveďte jednu z následujících akcí:</span><span class="sxs-lookup"><span data-stu-id="e0f54-332">To enable the enhanced diagnostic log, perform either of the following:</span></span>
   * <span data-ttu-id="e0f54-333">Postupujte podle pokynů v [rozšířených diagnostických protokolech](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) a nakonfigurujte aplikaci pro rozšířené diagnostické protokolování.</span><span class="sxs-lookup"><span data-stu-id="e0f54-333">Follow the instructions in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) to configure the app for an enhanced diagnostic logging.</span></span> <span data-ttu-id="e0f54-334">Znovu nasaďte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="e0f54-334">Redeploy the app.</span></span>
   * <span data-ttu-id="e0f54-335">Přidejte `<handlerSettings>` zobrazené v [rozšířených diagnostických protokolech](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) do souboru *Web. config* živé aplikace pomocí konzoly Kudu:</span><span class="sxs-lookup"><span data-stu-id="e0f54-335">Add the `<handlerSettings>` shown in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) to the live app's *web.config* file using the Kudu console:</span></span>
     1. <span data-ttu-id="e0f54-336">Otevřete **Rozšířené nástroje** v oblasti **vývojové nástroje** .</span><span class="sxs-lookup"><span data-stu-id="e0f54-336">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="e0f54-337">Vyberte tlačítko **přejít&rarr;** .</span><span class="sxs-lookup"><span data-stu-id="e0f54-337">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="e0f54-338">Konzola Kudu se otevře v novém okně nebo záložce prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="e0f54-338">The Kudu console opens in a new browser tab or window.</span></span>
     1. <span data-ttu-id="e0f54-339">Pomocí navigačního panelu v horní části stránky otevřete **konzolu ladění** a vyberte **cmd**.</span><span class="sxs-lookup"><span data-stu-id="e0f54-339">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
     1. <span data-ttu-id="e0f54-340">Otevřete složky v **lokalitě** pro cestu > **wwwroot**.</span><span class="sxs-lookup"><span data-stu-id="e0f54-340">Open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="e0f54-341">Upravte soubor *Web. config* tak, že vyberete tlačítko tužky.</span><span class="sxs-lookup"><span data-stu-id="e0f54-341">Edit the *web.config* file by selecting the pencil button.</span></span> <span data-ttu-id="e0f54-342">Přidejte část `<handlerSettings>`, jak je znázorněno v [rozšířených diagnostických protokolech](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs).</span><span class="sxs-lookup"><span data-stu-id="e0f54-342">Add the `<handlerSettings>` section as shown in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs).</span></span> <span data-ttu-id="e0f54-343">Vyberte tlačítko **Uložit**.</span><span class="sxs-lookup"><span data-stu-id="e0f54-343">Select the **Save** button.</span></span>
1. <span data-ttu-id="e0f54-344">Otevřete **Rozšířené nástroje** v oblasti **vývojové nástroje** .</span><span class="sxs-lookup"><span data-stu-id="e0f54-344">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="e0f54-345">Vyberte tlačítko **přejít&rarr;** .</span><span class="sxs-lookup"><span data-stu-id="e0f54-345">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="e0f54-346">Konzola Kudu se otevře v novém okně nebo záložce prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="e0f54-346">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="e0f54-347">Pomocí navigačního panelu v horní části stránky otevřete **konzolu ladění** a vyberte **cmd**.</span><span class="sxs-lookup"><span data-stu-id="e0f54-347">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="e0f54-348">Otevřete složky v **lokalitě** pro cestu > **wwwroot**.</span><span class="sxs-lookup"><span data-stu-id="e0f54-348">Open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="e0f54-349">Pokud jste nezadali cestu k souboru *aspnetcore-Debug. log* , soubor se zobrazí v seznamu.</span><span class="sxs-lookup"><span data-stu-id="e0f54-349">If you didn't supply a path for the *aspnetcore-debug.log* file, the file appears in the list.</span></span> <span data-ttu-id="e0f54-350">Pokud jste zadali cestu, přejděte do umístění souboru protokolu.</span><span class="sxs-lookup"><span data-stu-id="e0f54-350">If you supplied a path, navigate to the location of the log file.</span></span>
1. <span data-ttu-id="e0f54-351">Otevřete soubor protokolu s tlačítkem tužky vedle názvu souboru.</span><span class="sxs-lookup"><span data-stu-id="e0f54-351">Open the log file with the pencil button next to the file name.</span></span>

<span data-ttu-id="e0f54-352">Zakázat protokolování ladění, pokud je dokončeno odstraňování potíží:</span><span class="sxs-lookup"><span data-stu-id="e0f54-352">Disable debug logging when troubleshooting is complete:</span></span>

<span data-ttu-id="e0f54-353">Chcete-li zakázat rozšířený protokol ladění, proveďte jednu z následujících akcí:</span><span class="sxs-lookup"><span data-stu-id="e0f54-353">To disable the enhanced debug log, perform either of the following:</span></span>

* <span data-ttu-id="e0f54-354">Odeberte `<handlerSettings>` ze souboru *Web. config* místně a pak aplikaci znovu nasaďte.</span><span class="sxs-lookup"><span data-stu-id="e0f54-354">Remove the `<handlerSettings>` from the *web.config* file locally and redeploy the app.</span></span>
* <span data-ttu-id="e0f54-355">Pomocí konzoly Kudu upravte soubor *Web. config* a odeberte oddíl `<handlerSettings>`.</span><span class="sxs-lookup"><span data-stu-id="e0f54-355">Use the Kudu console to edit the *web.config* file and remove the `<handlerSettings>` section.</span></span> <span data-ttu-id="e0f54-356">Uložte soubor.</span><span class="sxs-lookup"><span data-stu-id="e0f54-356">Save the file.</span></span>

<span data-ttu-id="e0f54-357">Další informace najdete v tématu <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span><span class="sxs-lookup"><span data-stu-id="e0f54-357">For more information, see <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span></span>

> [!WARNING]
> <span data-ttu-id="e0f54-358">Nepovedlo se zakázat protokol ladění, může způsobit selhání aplikace nebo serveru.</span><span class="sxs-lookup"><span data-stu-id="e0f54-358">Failure to disable the debug log can lead to app or server failure.</span></span> <span data-ttu-id="e0f54-359">Velikost souboru protokolu není nijak omezena.</span><span class="sxs-lookup"><span data-stu-id="e0f54-359">There's no limit on log file size.</span></span> <span data-ttu-id="e0f54-360">K řešení problémů se spouštěním aplikace používejte pouze protokolování ladění.</span><span class="sxs-lookup"><span data-stu-id="e0f54-360">Only use debug logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="e0f54-361">Pro obecné protokolování v aplikaci ASP.NET Core po spuštění použijte knihovnu protokolování, která omezuje velikost souboru protokolu a otočí protokoly.</span><span class="sxs-lookup"><span data-stu-id="e0f54-361">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="e0f54-362">Další informace najdete v tématu [zprostředkovatele přihlášení třetí strany](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="e0f54-362">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

::: moniker-end

### <a name="slow-or-hanging-app-azure-app-service"></a><span data-ttu-id="e0f54-363">Pomalá nebo zavěšená aplikace (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="e0f54-363">Slow or hanging app (Azure App Service)</span></span>

<span data-ttu-id="e0f54-364">Pokud aplikace reaguje pomalu nebo přestane reagovat na žádost, přečtěte si následující články:</span><span class="sxs-lookup"><span data-stu-id="e0f54-364">When an app responds slowly or hangs on a request, see the following articles:</span></span>

* [<span data-ttu-id="e0f54-365">Řešení potíží s výkonem pomalých webových aplikací ve službě Azure App Service</span><span class="sxs-lookup"><span data-stu-id="e0f54-365">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="e0f54-366">K zachycení výpisu problémů s přerušovanou výjimkou nebo problémy s výkonem ve webové aplikaci Azure použijte rozšíření pro diagnostiku zhroucení serveru.</span><span class="sxs-lookup"><span data-stu-id="e0f54-366">Use Crash Diagnoser Site Extension to Capture Dump for Intermittent Exception issues or performance issues on Azure Web App</span></span>](https://blogs.msdn.microsoft.com/asiatech/2015/12/28/use-crash-diagnoser-site-extension-to-capture-dump-for-intermittent-exception-issues-or-performance-issues-on-azure-web-app/)

### <a name="monitoring-blades"></a><span data-ttu-id="e0f54-367">Okna monitorování</span><span class="sxs-lookup"><span data-stu-id="e0f54-367">Monitoring blades</span></span>

<span data-ttu-id="e0f54-368">Okna monitorování poskytují alternativní možnosti řešení potíží pro metody popsané dříve v tématu.</span><span class="sxs-lookup"><span data-stu-id="e0f54-368">Monitoring blades provide an alternative troubleshooting experience to the methods described earlier in the topic.</span></span> <span data-ttu-id="e0f54-369">Pomocí těchto oken můžete diagnostikovat chyby 500-Series.</span><span class="sxs-lookup"><span data-stu-id="e0f54-369">These blades can be used to diagnose 500-series errors.</span></span>

<span data-ttu-id="e0f54-370">Potvrďte, že jsou nainstalovaná rozšíření ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e0f54-370">Confirm that the ASP.NET Core Extensions are installed.</span></span> <span data-ttu-id="e0f54-371">Pokud nejsou rozšíření nainstalována, nainstalujte je ručně:</span><span class="sxs-lookup"><span data-stu-id="e0f54-371">If the extensions aren't installed, install them manually:</span></span>

1. <span data-ttu-id="e0f54-372">V části okno **vývojové nástroje** vyberte okno **rozšíření** .</span><span class="sxs-lookup"><span data-stu-id="e0f54-372">In the **DEVELOPMENT TOOLS** blade section, select the **Extensions** blade.</span></span>
1. <span data-ttu-id="e0f54-373">V seznamu by se měla zobrazit **rozšíření ASP.NET Core** .</span><span class="sxs-lookup"><span data-stu-id="e0f54-373">The **ASP.NET Core Extensions** should appear in the list.</span></span>
1. <span data-ttu-id="e0f54-374">Pokud nejsou rozšíření nainstalovaná, vyberte tlačítko **Přidat** .</span><span class="sxs-lookup"><span data-stu-id="e0f54-374">If the extensions aren't installed, select the **Add** button.</span></span>
1. <span data-ttu-id="e0f54-375">Ze seznamu vyberte **rozšíření ASP.NET Core** .</span><span class="sxs-lookup"><span data-stu-id="e0f54-375">Choose the **ASP.NET Core Extensions** from the list.</span></span>
1. <span data-ttu-id="e0f54-376">Kliknutím na **OK** přijměte právní podmínky.</span><span class="sxs-lookup"><span data-stu-id="e0f54-376">Select **OK** to accept the legal terms.</span></span>
1. <span data-ttu-id="e0f54-377">V okně **Přidat rozšíření** vyberte **OK** .</span><span class="sxs-lookup"><span data-stu-id="e0f54-377">Select **OK** on the **Add extension** blade.</span></span>
1. <span data-ttu-id="e0f54-378">Informační automaticky otevíraná zpráva indikuje, že se rozšíření úspěšně nainstalovala.</span><span class="sxs-lookup"><span data-stu-id="e0f54-378">An informational pop-up message indicates when the extensions are successfully installed.</span></span>

<span data-ttu-id="e0f54-379">Pokud není povoleno protokolování stdout, postupujte následovně:</span><span class="sxs-lookup"><span data-stu-id="e0f54-379">If stdout logging isn't enabled, follow these steps:</span></span>

1. <span data-ttu-id="e0f54-380">V Azure Portal v oblasti **vývojové nástroje** vyberte okno **Pokročilé nástroje** .</span><span class="sxs-lookup"><span data-stu-id="e0f54-380">In the Azure portal, select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="e0f54-381">Vyberte tlačítko **přejít&rarr;** .</span><span class="sxs-lookup"><span data-stu-id="e0f54-381">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="e0f54-382">Konzola Kudu se otevře v novém okně nebo záložce prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="e0f54-382">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="e0f54-383">Pomocí navigačního panelu v horní části stránky otevřete **konzolu ladění** a vyberte **cmd**.</span><span class="sxs-lookup"><span data-stu-id="e0f54-383">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="e0f54-384">Otevřete složky v **lokalitě** pro cestu > **wwwroot** a posuňte se dolů, aby se soubor *Web. config* vypnul v dolní části seznamu.</span><span class="sxs-lookup"><span data-stu-id="e0f54-384">Open the folders to the path **site** > **wwwroot** and scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="e0f54-385">Klikněte na ikonu tužky vedle souboru *Web. config* .</span><span class="sxs-lookup"><span data-stu-id="e0f54-385">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="e0f54-386">Nastavte **stdoutLogEnabled** na `true` a změňte cestu **stdoutLogFile** na: `\\?\%home%\LogFiles\stdout`.</span><span class="sxs-lookup"><span data-stu-id="e0f54-386">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="e0f54-387">Vyberte **Uložit** a uložte aktualizovaný soubor *Web. config* .</span><span class="sxs-lookup"><span data-stu-id="e0f54-387">Select **Save** to save the updated *web.config* file.</span></span>

<span data-ttu-id="e0f54-388">Pokračovat k aktivaci protokolování diagnostiky:</span><span class="sxs-lookup"><span data-stu-id="e0f54-388">Proceed to activate diagnostic logging:</span></span>

1. <span data-ttu-id="e0f54-389">V Azure Portal vyberte okno **diagnostické protokoly** .</span><span class="sxs-lookup"><span data-stu-id="e0f54-389">In the Azure portal, select the **Diagnostics logs** blade.</span></span>
1. <span data-ttu-id="e0f54-390">Vyberte přepínač **zapnuto** pro **protokolování aplikace (systém souborů)** a **podrobné chybové zprávy**.</span><span class="sxs-lookup"><span data-stu-id="e0f54-390">Select the **On** switch for **Application Logging (Filesystem)** and **Detailed error messages**.</span></span> <span data-ttu-id="e0f54-391">V horní části okna vyberte tlačítko **Uložit** .</span><span class="sxs-lookup"><span data-stu-id="e0f54-391">Select the **Save** button at the top of the blade.</span></span>
1. <span data-ttu-id="e0f54-392">Chcete-li zahrnout trasování chybných požadavků, označované také jako protokolování událostí neúspěšných požadavků na FREB, vyberte přepínač **zapnuto** pro **trasování chybných požadavků**.</span><span class="sxs-lookup"><span data-stu-id="e0f54-392">To include failed request tracing, also known as Failed Request Event Buffering (FREB) logging, select the **On** switch for **Failed request tracing**.</span></span>
1. <span data-ttu-id="e0f54-393">Vyberte okno **log Stream** , které je uvedeno hned pod oknem **diagnostické protokoly** na portálu.</span><span class="sxs-lookup"><span data-stu-id="e0f54-393">Select the **Log stream** blade, which is listed immediately under the **Diagnostics logs** blade in the portal.</span></span>
1. <span data-ttu-id="e0f54-394">Vytvořte žádost do aplikace.</span><span class="sxs-lookup"><span data-stu-id="e0f54-394">Make a request to the app.</span></span>
1. <span data-ttu-id="e0f54-395">V datech streamu protokolu se označuje příčina chyby.</span><span class="sxs-lookup"><span data-stu-id="e0f54-395">Within the log stream data, the cause of the error is indicated.</span></span>

<span data-ttu-id="e0f54-396">Nezapomeňte zakázat protokolování stdout po dokončení řešení potíží.</span><span class="sxs-lookup"><span data-stu-id="e0f54-396">Be sure to disable stdout logging when troubleshooting is complete.</span></span>

<span data-ttu-id="e0f54-397">Zobrazení protokolů pro trasování chybných požadavků (protokoly FREB):</span><span class="sxs-lookup"><span data-stu-id="e0f54-397">To view the failed request tracing logs (FREB logs):</span></span>

1. <span data-ttu-id="e0f54-398">Přejděte do okna **diagnostikovat a řešit problémy** v Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="e0f54-398">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="e0f54-399">V oblasti **nástroje podpory** v postranním panelu vyberte **protokoly pro trasování chybných požadavků** .</span><span class="sxs-lookup"><span data-stu-id="e0f54-399">Select **Failed Request Tracing Logs** from the **SUPPORT TOOLS** area of the sidebar.</span></span>

<span data-ttu-id="e0f54-400">V [části Povolení protokolování diagnostiky pro webové aplikace v Azure App Service tématu](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) a [Nejčastější dotazy týkající se výkonu aplikace pro Web Apps v Azure: návody zapnout trasování chybných požadavků?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing) Další informace.</span><span class="sxs-lookup"><span data-stu-id="e0f54-400">See [Failed request traces section of the Enable diagnostics logging for web apps in Azure App Service topic](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) and the [Application performance FAQs for Web Apps in Azure: How do I turn on failed request tracing?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing) for more information.</span></span>

<span data-ttu-id="e0f54-401">Další informace najdete v tématu [Povolení protokolování diagnostiky pro webové aplikace v Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log).</span><span class="sxs-lookup"><span data-stu-id="e0f54-401">For more information, see [Enable diagnostics logging for web apps in Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log).</span></span>

> [!WARNING]
> <span data-ttu-id="e0f54-402">Nepodařilo se zakázat protokol stdout může vést k selhání aplikace nebo serveru.</span><span class="sxs-lookup"><span data-stu-id="e0f54-402">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="e0f54-403">Neexistuje žádné omezení velikosti souboru protokolu nebo počet souborů protokolů, které jsou vytvořeny.</span><span class="sxs-lookup"><span data-stu-id="e0f54-403">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="e0f54-404">Pro rutiny protokolování v aplikaci ASP.NET Core, použijte protokolování knihovnu, která omezuje velikost souboru protokolu a otočí protokoly.</span><span class="sxs-lookup"><span data-stu-id="e0f54-404">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="e0f54-405">Další informace najdete v tématu [zprostředkovatele přihlášení třetí strany](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="e0f54-405">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

## <a name="troubleshoot-on-iis"></a><span data-ttu-id="e0f54-406">Řešení potíží ve službě IIS</span><span class="sxs-lookup"><span data-stu-id="e0f54-406">Troubleshoot on IIS</span></span>

### <a name="application-event-log-iis"></a><span data-ttu-id="e0f54-407">Protokol událostí aplikace (IIS)</span><span class="sxs-lookup"><span data-stu-id="e0f54-407">Application Event Log (IIS)</span></span>

<span data-ttu-id="e0f54-408">Přístup k protokolu událostí aplikace:</span><span class="sxs-lookup"><span data-stu-id="e0f54-408">Access the Application Event Log:</span></span>

1. <span data-ttu-id="e0f54-409">Otevřete nabídku Start, vyhledejte *Prohlížeč událostí*a vyberte aplikaci **Prohlížeč událostí** .</span><span class="sxs-lookup"><span data-stu-id="e0f54-409">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="e0f54-410">V **Prohlížeč událostí**, otevřete **protokoly Windows** uzlu.</span><span class="sxs-lookup"><span data-stu-id="e0f54-410">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="e0f54-411">Vyberte **aplikace** otevřít protokol událostí aplikace.</span><span class="sxs-lookup"><span data-stu-id="e0f54-411">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="e0f54-412">Vyhledejte chyby související s selhání aplikace.</span><span class="sxs-lookup"><span data-stu-id="e0f54-412">Search for errors associated with the failing app.</span></span> <span data-ttu-id="e0f54-413">Chyby mají hodnotu *modulu IIS AspNetCore* nebo *služby IIS Express AspNetCore modulu* v *zdroj* sloupce.</span><span class="sxs-lookup"><span data-stu-id="e0f54-413">Errors have a value of *IIS AspNetCore Module* or *IIS Express AspNetCore Module* in the *Source* column.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="e0f54-414">Spuštění aplikace příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="e0f54-414">Run the app at a command prompt</span></span>

<span data-ttu-id="e0f54-415">Mnoho chyb při spuštění nevytvářejí užitečné informace v protokolu událostí aplikace.</span><span class="sxs-lookup"><span data-stu-id="e0f54-415">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="e0f54-416">Příčin některých chyb můžete najít spuštěním aplikace v příkazovém řádku v hostitelském systému.</span><span class="sxs-lookup"><span data-stu-id="e0f54-416">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span>

#### <a name="framework-dependent-deployment"></a><span data-ttu-id="e0f54-417">Nasazení závisí na architektuře</span><span class="sxs-lookup"><span data-stu-id="e0f54-417">Framework-dependent deployment</span></span>

<span data-ttu-id="e0f54-418">Pokud je aplikace [nasazení závisí na architektuře](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span><span class="sxs-lookup"><span data-stu-id="e0f54-418">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

1. <span data-ttu-id="e0f54-419">Na příkazovém řádku přejděte do složky pro nasazení a spuštění aplikace spuštěním sestavení aplikace s *dotnet.exe*.</span><span class="sxs-lookup"><span data-stu-id="e0f54-419">At a command prompt, navigate to the deployment folder and run the app by executing the app's assembly with *dotnet.exe*.</span></span> <span data-ttu-id="e0f54-420">V následujícím příkazu nahraďte název sestavení aplikace pro \<název_sestavení >: `dotnet .\<assembly_name>.dll`.</span><span class="sxs-lookup"><span data-stu-id="e0f54-420">In the following command, substitute the name of the app's assembly for \<assembly_name>: `dotnet .\<assembly_name>.dll`.</span></span>
1. <span data-ttu-id="e0f54-421">Výstup z aplikace zobrazuje všechny chyby konzoly je zapsán do okna konzoly.</span><span class="sxs-lookup"><span data-stu-id="e0f54-421">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="e0f54-422">Je-li této chybě dojde při požadavku na aplikaci, vytvořte žádost na hostitele a port, kde Kestrel naslouchá.</span><span class="sxs-lookup"><span data-stu-id="e0f54-422">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="e0f54-423">Pomocí výchozího hostitele a post, vytvořit žádost o `http://localhost:5000/`.</span><span class="sxs-lookup"><span data-stu-id="e0f54-423">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="e0f54-424">Aplikace reaguje, obvykle na adrese Kestrel koncový bod, tím je pravděpodobnější týkající se konfigurace hostování a méně pravděpodobné, že v rámci aplikace.</span><span class="sxs-lookup"><span data-stu-id="e0f54-424">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

#### <a name="self-contained-deployment"></a><span data-ttu-id="e0f54-425">Samostatná nasazení</span><span class="sxs-lookup"><span data-stu-id="e0f54-425">Self-contained deployment</span></span>

<span data-ttu-id="e0f54-426">Pokud je aplikace [samostatná nasazení](/dotnet/core/deploying/#self-contained-deployments-scd):</span><span class="sxs-lookup"><span data-stu-id="e0f54-426">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

1. <span data-ttu-id="e0f54-427">Na příkazovém řádku přejděte do složky pro nasazení a spuštění spustitelného souboru aplikace.</span><span class="sxs-lookup"><span data-stu-id="e0f54-427">At a command prompt, navigate to the deployment folder and run the app's executable.</span></span> <span data-ttu-id="e0f54-428">V následujícím příkazu nahraďte název sestavení aplikace pro \<název_sestavení >: `<assembly_name>.exe`.</span><span class="sxs-lookup"><span data-stu-id="e0f54-428">In the following command, substitute the name of the app's assembly for \<assembly_name>: `<assembly_name>.exe`.</span></span>
1. <span data-ttu-id="e0f54-429">Výstup z aplikace zobrazuje všechny chyby konzoly je zapsán do okna konzoly.</span><span class="sxs-lookup"><span data-stu-id="e0f54-429">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="e0f54-430">Je-li této chybě dojde při požadavku na aplikaci, vytvořte žádost na hostitele a port, kde Kestrel naslouchá.</span><span class="sxs-lookup"><span data-stu-id="e0f54-430">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="e0f54-431">Pomocí výchozího hostitele a post, vytvořit žádost o `http://localhost:5000/`.</span><span class="sxs-lookup"><span data-stu-id="e0f54-431">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="e0f54-432">Aplikace reaguje, obvykle na adrese Kestrel koncový bod, tím je pravděpodobnější týkající se konfigurace hostování a méně pravděpodobné, že v rámci aplikace.</span><span class="sxs-lookup"><span data-stu-id="e0f54-432">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

### <a name="aspnet-core-module-stdout-log-iis"></a><span data-ttu-id="e0f54-433">Protokol stdout v modulu ASP.NET Core (IIS)</span><span class="sxs-lookup"><span data-stu-id="e0f54-433">ASP.NET Core Module stdout log (IIS)</span></span>

<span data-ttu-id="e0f54-434">Povolení a zobrazení protokolů stdout:</span><span class="sxs-lookup"><span data-stu-id="e0f54-434">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="e0f54-435">Přejděte do složky pro nasazení webu v hostitelském systému.</span><span class="sxs-lookup"><span data-stu-id="e0f54-435">Navigate to the site's deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="e0f54-436">Pokud *protokoly* složka není k dispozici, vytvořte složku.</span><span class="sxs-lookup"><span data-stu-id="e0f54-436">If the *logs* folder isn't present, create the folder.</span></span> <span data-ttu-id="e0f54-437">Pokyny o tom, jak povolit MSBuild k vytvoření *protokoly* složky v nasazení automaticky, zobrazí [adresářovou strukturu](xref:host-and-deploy/directory-structure) tématu.</span><span class="sxs-lookup"><span data-stu-id="e0f54-437">For instructions on how to enable MSBuild to create the *logs* folder in the deployment automatically, see the [Directory structure](xref:host-and-deploy/directory-structure) topic.</span></span>
1. <span data-ttu-id="e0f54-438">Upravit *web.config* souboru.</span><span class="sxs-lookup"><span data-stu-id="e0f54-438">Edit the *web.config* file.</span></span> <span data-ttu-id="e0f54-439">Nastavte **stdoutLogEnabled** k `true` a změnit **stdoutLogFile** tak, aby odkazoval na cestu *protokoly* složky (například `.\logs\stdout`).</span><span class="sxs-lookup"><span data-stu-id="e0f54-439">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to point to the *logs* folder (for example, `.\logs\stdout`).</span></span> <span data-ttu-id="e0f54-440">`stdout` v cestě je předpona názvu souboru protokolu.</span><span class="sxs-lookup"><span data-stu-id="e0f54-440">`stdout` in the path is the log file name prefix.</span></span> <span data-ttu-id="e0f54-441">Časové razítko, id procesu a příponu souboru jsou přidány automaticky při vytvoření protokolu.</span><span class="sxs-lookup"><span data-stu-id="e0f54-441">A timestamp, process id, and file extension are added automatically when the log is created.</span></span> <span data-ttu-id="e0f54-442">Pomocí `stdout` jako předpona názvu souboru, má název souboru typické protokolu *stdout_20180205184032_5412.log*.</span><span class="sxs-lookup"><span data-stu-id="e0f54-442">Using `stdout` as the file name prefix, a typical log file is named *stdout_20180205184032_5412.log*.</span></span>
1. <span data-ttu-id="e0f54-443">Ověřte identitu fondu aplikací má oprávnění k zápisu *protokoly* složky.</span><span class="sxs-lookup"><span data-stu-id="e0f54-443">Ensure your application pool's identity has write permissions to the *logs* folder.</span></span>
1. <span data-ttu-id="e0f54-444">Uložte aktualizovaný *web.config* souboru.</span><span class="sxs-lookup"><span data-stu-id="e0f54-444">Save the updated *web.config* file.</span></span>
1. <span data-ttu-id="e0f54-445">Vytvořte žádost do aplikace.</span><span class="sxs-lookup"><span data-stu-id="e0f54-445">Make a request to the app.</span></span>
1. <span data-ttu-id="e0f54-446">Přejděte *protokoly* složky.</span><span class="sxs-lookup"><span data-stu-id="e0f54-446">Navigate to the *logs* folder.</span></span> <span data-ttu-id="e0f54-447">Vyhledání a otevření protokolu nejnovější stdout.</span><span class="sxs-lookup"><span data-stu-id="e0f54-447">Find and open the most recent stdout log.</span></span>
1. <span data-ttu-id="e0f54-448">Studie v protokolu chyb.</span><span class="sxs-lookup"><span data-stu-id="e0f54-448">Study the log for errors.</span></span>

<span data-ttu-id="e0f54-449">Zakázat protokolování stdout při odstraňování potíží:</span><span class="sxs-lookup"><span data-stu-id="e0f54-449">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="e0f54-450">Upravit *web.config* souboru.</span><span class="sxs-lookup"><span data-stu-id="e0f54-450">Edit the *web.config* file.</span></span>
1. <span data-ttu-id="e0f54-451">Nastavte **stdoutLogEnabled** k `false`.</span><span class="sxs-lookup"><span data-stu-id="e0f54-451">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="e0f54-452">Uložte soubor.</span><span class="sxs-lookup"><span data-stu-id="e0f54-452">Save the file.</span></span>

<span data-ttu-id="e0f54-453">Další informace najdete v tématu <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span><span class="sxs-lookup"><span data-stu-id="e0f54-453">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

> [!WARNING]
> <span data-ttu-id="e0f54-454">Nepodařilo se zakázat protokol stdout může vést k selhání aplikace nebo serveru.</span><span class="sxs-lookup"><span data-stu-id="e0f54-454">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="e0f54-455">Neexistuje žádné omezení velikosti souboru protokolu nebo počet souborů protokolů, které jsou vytvořeny.</span><span class="sxs-lookup"><span data-stu-id="e0f54-455">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="e0f54-456">Pro rutiny protokolování v aplikaci ASP.NET Core, použijte protokolování knihovnu, která omezuje velikost souboru protokolu a otočí protokoly.</span><span class="sxs-lookup"><span data-stu-id="e0f54-456">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="e0f54-457">Další informace najdete v tématu [zprostředkovatele přihlášení třetí strany](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="e0f54-457">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

::: moniker range=">= aspnetcore-2.2"

### <a name="aspnet-core-module-debug-log-iis"></a><span data-ttu-id="e0f54-458">Protokol ladění modulu ASP.NET Core (IIS)</span><span class="sxs-lookup"><span data-stu-id="e0f54-458">ASP.NET Core Module debug log (IIS)</span></span>

<span data-ttu-id="e0f54-459">Přidejte následující nastavení obslužné rutiny do souboru *Web. config* aplikace, aby bylo možné povolit protokol ladění ASP.NET Core modulu:</span><span class="sxs-lookup"><span data-stu-id="e0f54-459">Add the following handler settings to the app's *web.config* file to enable ASP.NET Core Module debug log:</span></span>

```xml
<aspNetCore ...>
  <handlerSettings>
    <handlerSetting name="debugLevel" value="file" />
    <handlerSetting name="debugFile" value="c:\temp\ancm.log" />
  </handlerSettings>
</aspNetCore>
```

<span data-ttu-id="e0f54-460">Potvrďte, že cesta zadaná pro protokol existuje a že identita fondu aplikací má oprávnění k zápisu do umístění.</span><span class="sxs-lookup"><span data-stu-id="e0f54-460">Confirm that the path specified for the log exists and that the app pool's identity has write permissions to the location.</span></span>

<span data-ttu-id="e0f54-461">Další informace najdete v tématu <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span><span class="sxs-lookup"><span data-stu-id="e0f54-461">For more information, see <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span></span>

::: moniker-end

### <a name="enable-the-developer-exception-page"></a><span data-ttu-id="e0f54-462">Povolit na stránce výjimek pro vývojáře</span><span class="sxs-lookup"><span data-stu-id="e0f54-462">Enable the Developer Exception Page</span></span>

<span data-ttu-id="e0f54-463">`ASPNETCORE_ENVIRONMENT` [Proměnnou prostředí lze přidat do souboru web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) ke spuštění aplikace ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="e0f54-463">The `ASPNETCORE_ENVIRONMENT` [environment variable can be added to web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) to run the app in the Development environment.</span></span> <span data-ttu-id="e0f54-464">Tak dlouho, dokud není prostředí přepsána ve spuštění aplikace podle `UseEnvironment` na tvůrce hostitele nastavení proměnné prostředí umožňuje [stránku výjimek pro vývojáře](xref:fundamentals/error-handling) se zobrazí při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="e0f54-464">As long as the environment isn't overridden in app startup by `UseEnvironment` on the host builder, setting the environment variable allows the [Developer Exception Page](xref:fundamentals/error-handling) to appear when the app is run.</span></span>

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

<span data-ttu-id="e0f54-465">Nastavení proměnné prostředí pro `ASPNETCORE_ENVIRONMENT` se doporučuje jenom pro použití v přípravy a testování serverů, které nejsou vystaveny v Internetu.</span><span class="sxs-lookup"><span data-stu-id="e0f54-465">Setting the environment variable for `ASPNETCORE_ENVIRONMENT` is only recommended for use on staging and testing servers that aren't exposed to the Internet.</span></span> <span data-ttu-id="e0f54-466">Odebrat z proměnné prostředí *web.config* soubor po vyřešení potíží.</span><span class="sxs-lookup"><span data-stu-id="e0f54-466">Remove the environment variable from the *web.config* file after troubleshooting.</span></span> <span data-ttu-id="e0f54-467">Informace o nastavení proměnných prostředí *web.config*, naleznete v tématu [environmentVariables podřízený prvek aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).</span><span class="sxs-lookup"><span data-stu-id="e0f54-467">For information on setting environment variables in *web.config*, see [environmentVariables child element of aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).</span></span>

### <a name="obtain-data-from-an-app"></a><span data-ttu-id="e0f54-468">Získání dat z aplikace</span><span class="sxs-lookup"><span data-stu-id="e0f54-468">Obtain data from an app</span></span>

<span data-ttu-id="e0f54-469">Pokud aplikace je schopná reagovat na požadavky, získáte žádost o připojení a další data z aplikace pomocí terminálu vložené middlewaru.</span><span class="sxs-lookup"><span data-stu-id="e0f54-469">If an app is capable of responding to requests, obtain request, connection, and additional data from the app using terminal inline middleware.</span></span> <span data-ttu-id="e0f54-470">Další informace a ukázky kódu najdete v tématu <xref:test/troubleshoot#obtain-data-from-an-app>.</span><span class="sxs-lookup"><span data-stu-id="e0f54-470">For more information and sample code, see <xref:test/troubleshoot#obtain-data-from-an-app>.</span></span>

### <a name="slow-or-hanging-app-iis"></a><span data-ttu-id="e0f54-471">Pomalá nebo zavěšená aplikace (IIS)</span><span class="sxs-lookup"><span data-stu-id="e0f54-471">Slow or hanging app (IIS)</span></span>

<span data-ttu-id="e0f54-472">*Výpis stavu* systému je snímek paměti systému a může vám pomůže určit příčinu selhání aplikace, selhání při spuštění nebo pomalé aplikace.</span><span class="sxs-lookup"><span data-stu-id="e0f54-472">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="e0f54-473">Aplikace selže nebo dojde k výjimce.</span><span class="sxs-lookup"><span data-stu-id="e0f54-473">App crashes or encounters an exception</span></span>

<span data-ttu-id="e0f54-474">Získat a analyzovat výpis z [zasílání zpráv o chybách systému Windows (WER)](/windows/desktop/wer/windows-error-reporting):</span><span class="sxs-lookup"><span data-stu-id="e0f54-474">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="e0f54-475">Vytvořte složku, do které se budou ukládat soubory s výpisem stavu systému na `c:\dumps`.</span><span class="sxs-lookup"><span data-stu-id="e0f54-475">Create a folder to hold crash dump files at `c:\dumps`.</span></span> <span data-ttu-id="e0f54-476">Fond aplikací musí mít ke složce přístup pro zápis.</span><span class="sxs-lookup"><span data-stu-id="e0f54-476">The app pool must have write access to the folder.</span></span>
1. <span data-ttu-id="e0f54-477">Spusťte [skript prostředí PowerShell pro EnableDumps](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1):</span><span class="sxs-lookup"><span data-stu-id="e0f54-477">Run the [EnableDumps PowerShell script](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1):</span></span>
   * <span data-ttu-id="e0f54-478">Pokud aplikace používá [model hostování v rámci procesu](xref:host-and-deploy/iis/index#in-process-hosting-model), spusťte skript pro *W3wp. exe*:</span><span class="sxs-lookup"><span data-stu-id="e0f54-478">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\EnableDumps w3wp.exe c:\dumps
     ```

   * <span data-ttu-id="e0f54-479">Pokud aplikace používá [model hostování mimo proces](xref:host-and-deploy/iis/index#out-of-process-hosting-model), spusťte skript pro příkaz *dotnet. exe*:</span><span class="sxs-lookup"><span data-stu-id="e0f54-479">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\EnableDumps dotnet.exe c:\dumps
     ```

1. <span data-ttu-id="e0f54-480">Spusťte aplikaci za podmínek, které způsobí, že dojde k chybě.</span><span class="sxs-lookup"><span data-stu-id="e0f54-480">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="e0f54-481">Po chybě spusťte [skript PowerShellu DisableDumps](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1):</span><span class="sxs-lookup"><span data-stu-id="e0f54-481">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1):</span></span>
   * <span data-ttu-id="e0f54-482">Pokud aplikace používá [model hostování v rámci procesu](xref:host-and-deploy/iis/index#in-process-hosting-model), spusťte skript pro *W3wp. exe*:</span><span class="sxs-lookup"><span data-stu-id="e0f54-482">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\DisableDumps w3wp.exe
     ```

   * <span data-ttu-id="e0f54-483">Pokud aplikace používá [model hostování mimo proces](xref:host-and-deploy/iis/index#out-of-process-hosting-model), spusťte skript pro příkaz *dotnet. exe*:</span><span class="sxs-lookup"><span data-stu-id="e0f54-483">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\DisableDumps dotnet.exe
     ```

<span data-ttu-id="e0f54-484">Po selhání aplikace a dokončení shromažďování výpisu je možné aplikaci ukončit normálně.</span><span class="sxs-lookup"><span data-stu-id="e0f54-484">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="e0f54-485">Skript PowerShellu nakonfiguruje WER a shromáždí až pět výpisů paměti pro každou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="e0f54-485">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="e0f54-486">Výpisy stavu systému můžou zabírat velké množství místa na disku (každý až několik gigabajtů).</span><span class="sxs-lookup"><span data-stu-id="e0f54-486">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="e0f54-487">Aplikace přestane reagovat, dojde k chybě při spuštění nebo se spustí normálně.</span><span class="sxs-lookup"><span data-stu-id="e0f54-487">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="e0f54-488">Když *aplikace přestane reagovat (zastaví* se, ale nejedná se o chybu), selže během spuštění nebo se spustí normálně, podívejte [se na soubory výpisu paměti v uživatelském režimu: zvolením nejlepšího](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) nástroje vyberte vhodný nástroj pro vytváření výpisu.</span><span class="sxs-lookup"><span data-stu-id="e0f54-488">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="e0f54-489">Analýza výpisu paměti</span><span class="sxs-lookup"><span data-stu-id="e0f54-489">Analyze the dump</span></span>

<span data-ttu-id="e0f54-490">Výpis paměti lze analyzovat pomocí několika přístupů.</span><span class="sxs-lookup"><span data-stu-id="e0f54-490">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="e0f54-491">Další informace najdete v tématu [Analýza souboru s výpisem stavu v uživatelském režimu](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span><span class="sxs-lookup"><span data-stu-id="e0f54-491">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="clear-package-caches"></a><span data-ttu-id="e0f54-492">Vymazat mezipaměti balíčků</span><span class="sxs-lookup"><span data-stu-id="e0f54-492">Clear package caches</span></span>

<span data-ttu-id="e0f54-493">Funkční aplikace může po upgradu .NET Core SDK ve vývojovém počítači nebo změně verzí balíčku v rámci aplikace selhat okamžitě.</span><span class="sxs-lookup"><span data-stu-id="e0f54-493">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="e0f54-494">V některých případech osamocené balíčky mohou narušit funkce aplikace při provádění hlavní upgrady.</span><span class="sxs-lookup"><span data-stu-id="e0f54-494">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="e0f54-495">Většina těchto problémů můžete opravit podle těchto pokynů:</span><span class="sxs-lookup"><span data-stu-id="e0f54-495">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="e0f54-496">Odstranit *bin* a *obj* složek.</span><span class="sxs-lookup"><span data-stu-id="e0f54-496">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="e0f54-497">Pomocí příkazu [dotnet All--Clear](/dotnet/core/tools/dotnet-nuget-locals) z příkazového prostředí vymažte mezipaměť balíčku.</span><span class="sxs-lookup"><span data-stu-id="e0f54-497">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="e0f54-498">Mazání mezipamětí balíčků lze také provést pomocí nástroje [NuGet. exe](https://www.nuget.org/downloads) a spuštěním příkazu `nuget locals all -clear`.</span><span class="sxs-lookup"><span data-stu-id="e0f54-498">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="e0f54-499">*nuget.exe* není připojené instalace s operačním systémem klasické pracovní plochy Windows a je potřeba pořídit samostatně z [webu NuGet](https://www.nuget.org/downloads).</span><span class="sxs-lookup"><span data-stu-id="e0f54-499">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="e0f54-500">Obnovit a znovu sestavte projekt.</span><span class="sxs-lookup"><span data-stu-id="e0f54-500">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="e0f54-501">Před opětovným nasazením aplikace odstraňte všechny soubory ve složce pro nasazení na serveru.</span><span class="sxs-lookup"><span data-stu-id="e0f54-501">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e0f54-502">Další materiály a zdroje informací</span><span class="sxs-lookup"><span data-stu-id="e0f54-502">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:fundamentals/error-handling>
* <xref:host-and-deploy/aspnet-core-module>

### <a name="azure-documentation"></a><span data-ttu-id="e0f54-503">Dokumentace k Azure</span><span class="sxs-lookup"><span data-stu-id="e0f54-503">Azure documentation</span></span>

* [<span data-ttu-id="e0f54-504">Application Insights pro ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e0f54-504">Application Insights for ASP.NET Core</span></span>](/azure/application-insights/app-insights-asp-net-core)
* [<span data-ttu-id="e0f54-505">Část vzdálené ladění webových aplikací řešení potíží s webovou aplikací v Azure App Service pomocí sady Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e0f54-505">Remote debugging web apps section of Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug)
* [<span data-ttu-id="e0f54-506">Přehled diagnostiky Azure App Service</span><span class="sxs-lookup"><span data-stu-id="e0f54-506">Azure App Service diagnostics overview</span></span>](/azure/app-service/app-service-diagnostics)
* [<span data-ttu-id="e0f54-507">Postup: Monitorování aplikací ve službě Azure App Service</span><span class="sxs-lookup"><span data-stu-id="e0f54-507">How to: Monitor Apps in Azure App Service</span></span>](/azure/app-service/web-sites-monitor)
* [<span data-ttu-id="e0f54-508">Řešení potíží s webovou aplikací ve službě Azure App Service pomocí sady Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e0f54-508">Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [<span data-ttu-id="e0f54-509">Řešení chyb HTTP z "502 špatné brány" a "nedostupné služby 503" v Azure Web Apps</span><span class="sxs-lookup"><span data-stu-id="e0f54-509">Troubleshoot HTTP errors of "502 bad gateway" and "503 service unavailable" in your Azure web apps</span></span>](/azure/app-service/app-service-web-troubleshoot-http-502-http-503)
* [<span data-ttu-id="e0f54-510">Řešení potíží s výkonem pomalých webových aplikací ve službě Azure App Service</span><span class="sxs-lookup"><span data-stu-id="e0f54-510">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="e0f54-511">Nejčastější dotazy týkající se výkonu aplikací pro Web Apps v Azure</span><span class="sxs-lookup"><span data-stu-id="e0f54-511">Application performance FAQs for Web Apps in Azure</span></span>](/azure/app-service/app-service-web-availability-performance-application-issues-faq)
* [<span data-ttu-id="e0f54-512">Sandbox webové aplikace Azure (omezení spuštění App Service Runtime)</span><span class="sxs-lookup"><span data-stu-id="e0f54-512">Azure Web App sandbox (App Service runtime execution limitations)</span></span>](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)
* [<span data-ttu-id="e0f54-513">Azure pátek: Azure App Service diagnostické prostředí a řešení potíží (12 minut video)</span><span class="sxs-lookup"><span data-stu-id="e0f54-513">Azure Friday: Azure App Service Diagnostic and Troubleshooting Experience (12-minute video)</span></span>](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)

### <a name="visual-studio-documentation"></a><span data-ttu-id="e0f54-514">Dokumentace sady Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e0f54-514">Visual Studio documentation</span></span>

* [<span data-ttu-id="e0f54-515">Vzdálené ladění ASP.NET Core ve službě IIS v Azure v aplikaci Visual Studio 2017</span><span class="sxs-lookup"><span data-stu-id="e0f54-515">Remote Debug ASP.NET Core on IIS in Azure in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-azure)
* [<span data-ttu-id="e0f54-516">Vzdálené ladění ASP.NET Core na vzdáleném počítači IIS v aplikaci Visual Studio 2017</span><span class="sxs-lookup"><span data-stu-id="e0f54-516">Remote Debug ASP.NET Core on a Remote IIS Computer in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-aspnet-on-a-remote-iis-computer)
* [<span data-ttu-id="e0f54-517">Další informace k ladění pomocí sady Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e0f54-517">Learn to debug using Visual Studio</span></span>](/visualstudio/debugger/getting-started-with-the-debugger)

### <a name="visual-studio-code-documentation"></a><span data-ttu-id="e0f54-518">Dokumentace k editoru Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e0f54-518">Visual Studio Code documentation</span></span>

* [<span data-ttu-id="e0f54-519">Ladění ve Visual Studiu Code</span><span class="sxs-lookup"><span data-stu-id="e0f54-519">Debugging with Visual Studio Code</span></span>](https://code.visualstudio.com/docs/editor/debugging)
