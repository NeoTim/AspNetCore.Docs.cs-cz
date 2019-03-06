---
title: Referenční informace o běžných chybách pro Azure App Service a IIS s ASP.NET Core
author: guardrex
description: Získáte pomoc při řešení potíží pro běžné chyby při hostování aplikací ASP.NET Core v Azure App Service a službu IIS.
ms.author: riande
ms.custom: mvc
ms.date: 02/28/2019
uid: host-and-deploy/azure-iis-errors-reference
ms.openlocfilehash: 1c8cb31b306b38ec17596af0a84f22ca0e3d911c
ms.sourcegitcommit: 036d4b03fd86ca5bb378198e29ecf2704257f7b2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2019
ms.locfileid: "57346223"
---
# <a name="common-errors-reference-for-azure-app-service-and-iis-with-aspnet-core"></a><span data-ttu-id="d6fc5-103">Referenční informace o běžných chybách pro Azure App Service a IIS s ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d6fc5-103">Common errors reference for Azure App Service and IIS with ASP.NET Core</span></span>

<span data-ttu-id="d6fc5-104">Podle [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="d6fc5-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="d6fc5-105">Toto téma nabízí pomoc při řešení potíží pro běžné chyby při hostování aplikací ASP.NET Core v Azure App Service a službu IIS.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-105">This topic offers troubleshooting advice for common errors when hosting ASP.NET Core apps on Azure Apps Service and IIS.</span></span>

<span data-ttu-id="d6fc5-106">Shromážděte následující informace:</span><span class="sxs-lookup"><span data-stu-id="d6fc5-106">Collect the following information:</span></span>

* <span data-ttu-id="d6fc5-107">Chování prohlížeče (stavový kód a chybové zprávy)</span><span class="sxs-lookup"><span data-stu-id="d6fc5-107">Browser behavior (status code and error message)</span></span>
* <span data-ttu-id="d6fc5-108">Položky protokolu událostí aplikace</span><span class="sxs-lookup"><span data-stu-id="d6fc5-108">Application Event Log entries</span></span>
  * <span data-ttu-id="d6fc5-109">Azure App Service &ndash; naleznete v tématu <xref:host-and-deploy/azure-apps/troubleshoot>.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-109">Azure App Service &ndash; See <xref:host-and-deploy/azure-apps/troubleshoot>.</span></span>
  * <span data-ttu-id="d6fc5-110">IIS</span><span class="sxs-lookup"><span data-stu-id="d6fc5-110">IIS</span></span>
    1. <span data-ttu-id="d6fc5-111">Vyberte **Start** na **Windows** nabídky, typ *Prohlížeč událostí*a stiskněte klávesu **Enter**.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-111">Select **Start** on the **Windows** menu, type *Event Viewer*, and press **Enter**.</span></span>
    1. <span data-ttu-id="d6fc5-112">Po **Prohlížeč událostí** se otevře, rozbalte **protokoly Windows** > **aplikace** na bočním panelu.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-112">After the **Event Viewer** opens, expand **Windows Logs** > **Application** in the sidebar.</span></span>
* <span data-ttu-id="d6fc5-113">Ladění a ASP.NET Core modulu stdout položky protokolu</span><span class="sxs-lookup"><span data-stu-id="d6fc5-113">ASP.NET Core Module stdout and debug log entries</span></span>
  * <span data-ttu-id="d6fc5-114">Azure App Service &ndash; naleznete v tématu <xref:host-and-deploy/azure-apps/troubleshoot>.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-114">Azure App Service &ndash; See <xref:host-and-deploy/azure-apps/troubleshoot>.</span></span>
  * <span data-ttu-id="d6fc5-115">Služba IIS &ndash; postupujte podle pokynů [vytváření a přesměrování protokolu](xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection) a [rozšířené diagnostické protokoly](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) částech tématu modul ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-115">IIS &ndash; Follow the instructions in the [Log creation and redirection](xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection) and [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) sections of the ASP.NET Core Module topic.</span></span>

<span data-ttu-id="d6fc5-116">Porovnejte následující běžné chyby informace o chybě.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-116">Compare error information to the following common errors.</span></span> <span data-ttu-id="d6fc5-117">Pokud se najde shoda, postupujte podle pomoc při řešení potíží.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-117">If a match is found, follow the troubleshooting advice.</span></span>

<span data-ttu-id="d6fc5-118">Seznam chyb v tomto tématu není vyčerpávající.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-118">The list of errors in this topic isn't exhaustive.</span></span> <span data-ttu-id="d6fc5-119">Pokud narazíte na chybu tu nejsou uvedené, otevřete nový problém pomocí **obsahu zpětnou vazbu** tlačítko v dolní části tohoto tématu, s podrobnými pokyny o tom, jak chybu reprodukovat.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-119">If you encounter an error not listed here, open a new issue using the **Content feedback** button at the bottom of this topic with detailed instructions on how to reproduce the error.</span></span>

[!INCLUDE[Azure App Service Preview Notice](../includes/azure-apps-preview-notice.md)]

## <a name="installer-unable-to-obtain-vc-redistributable"></a><span data-ttu-id="d6fc5-120">Instalační program nelze získat VC ++ Redistributable</span><span class="sxs-lookup"><span data-stu-id="d6fc5-120">Installer unable to obtain VC++ Redistributable</span></span>

* <span data-ttu-id="d6fc5-121">**Instalační program výjimka:** 0x80072EFD **--nebo--** 0x80072f76 - Nespecifikovaná chyba</span><span class="sxs-lookup"><span data-stu-id="d6fc5-121">**Installer Exception:** 0x80072efd **--OR--** 0x80072f76 - Unspecified error</span></span>

* <span data-ttu-id="d6fc5-122">**Výjimky protokolu instalačního programu&#8224;:** Chyba 0x80072efd **--nebo--** 0x80072f76: Nepovedlo se spustit soubor EXE balíčku</span><span class="sxs-lookup"><span data-stu-id="d6fc5-122">**Installer Log Exception&#8224;:** Error 0x80072efd **--OR--** 0x80072f76: Failed to execute EXE package</span></span>

  <span data-ttu-id="d6fc5-123">&#8224;V protokolu se nachází na *C:\Users\{USER}\AppData\Local\Temp\dd_DotNetCoreWinSvrHosting__{TIMESTAMP}.log*.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-123">&#8224;The log is located at *C:\Users\{USER}\AppData\Local\Temp\dd_DotNetCoreWinSvrHosting__{TIMESTAMP}.log*.</span></span>

<span data-ttu-id="d6fc5-124">Řešení potíží:</span><span class="sxs-lookup"><span data-stu-id="d6fc5-124">Troubleshooting:</span></span>

<span data-ttu-id="d6fc5-125">Pokud systém nemá přístup k Internetu při [instalace sady .NET Core hostování](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle), tato výjimka nastane, pokud instalační program nemůže získat *Microsoft Visual C++ 2015 Redistributable*.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-125">If the system doesn't have Internet access while [installing the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle), this exception occurs when the installer is prevented from obtaining the *Microsoft Visual C++ 2015 Redistributable*.</span></span> <span data-ttu-id="d6fc5-126">Získat z instalačního programu [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=53840).</span><span class="sxs-lookup"><span data-stu-id="d6fc5-126">Obtain an installer from the [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=53840).</span></span> <span data-ttu-id="d6fc5-127">Pokud instalační program selže, server nemůže přijímat modulu runtime .NET Core vyžaduje k hostování [nasazení závisí na architektuře (chyba)](/dotnet/core/deploying/#framework-dependent-deployments-fdd).</span><span class="sxs-lookup"><span data-stu-id="d6fc5-127">If the installer fails, the server may not receive the .NET Core runtime required to host a [framework-dependent deployment (FDD)](/dotnet/core/deploying/#framework-dependent-deployments-fdd).</span></span> <span data-ttu-id="d6fc5-128">Pokud hostování disketové jednotky, ověřte, že modul runtime je nainstalován ve **programy a funkce** nebo **aplikace a funkce**.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-128">If hosting an FDD, confirm that the runtime is installed in **Programs & Features** or **Apps & features**.</span></span> <span data-ttu-id="d6fc5-129">Pokud konkrétní modul runtime je vyžadována, stáhněte si modul runtime z [archivy stáhnout .NET](https://dotnet.microsoft.com/download/archives) a nainstalujte ho na systém.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-129">If a specific runtime is required, download the runtime from the [.NET Download Archives](https://dotnet.microsoft.com/download/archives) and install it on the system.</span></span> <span data-ttu-id="d6fc5-130">Po instalaci modulu runtime, restartování systému nebo restartování služby IIS pomocí provádí **net stop byla /y** následovaný **net start w3svc** z příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-130">After installing the runtime, restart the system or restart IIS by executing **net stop was /y** followed by **net start w3svc** from a command prompt.</span></span>

## <a name="os-upgrade-removed-the-32-bit-aspnet-core-module"></a><span data-ttu-id="d6fc5-131">Upgrade operačního systému odebrat modul ASP.NET Core 32-bit</span><span class="sxs-lookup"><span data-stu-id="d6fc5-131">OS upgrade removed the 32-bit ASP.NET Core Module</span></span>

<span data-ttu-id="d6fc5-132">**Protokol aplikace:** Knihovnu DLL modulu **C:\WINDOWS\system32\inetsrv\aspnetcore.dll** se nepodařilo načíst.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-132">**Application Log:** The Module DLL **C:\WINDOWS\system32\inetsrv\aspnetcore.dll** failed to load.</span></span> <span data-ttu-id="d6fc5-133">Data jsou chyby.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-133">The data is the error.</span></span>

<span data-ttu-id="d6fc5-134">Řešení potíží:</span><span class="sxs-lookup"><span data-stu-id="d6fc5-134">Troubleshooting:</span></span>

<span data-ttu-id="d6fc5-135">Bez operačního systému souborů v **C:\Windows\SysWOW64\inetsrv** directory nezachovají se během operační systém upgradovat.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-135">Non-OS files in the **C:\Windows\SysWOW64\inetsrv** directory aren't preserved during an OS upgrade.</span></span> <span data-ttu-id="d6fc5-136">Pokud modul ASP.NET Core je nainstalovaná starší než upgrade operačního systému a pak v každém fondu aplikací běží v 32bitovém režimu po upgradu operačního systému, tento problém nastává.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-136">If the ASP.NET Core Module is installed prior to an OS upgrade and then any app pool is run in 32-bit mode after an OS upgrade, this issue is encountered.</span></span> <span data-ttu-id="d6fc5-137">Po upgradu operačního systému opravit modul ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-137">After an OS upgrade, repair the ASP.NET Core Module.</span></span> <span data-ttu-id="d6fc5-138">Zobrazit [instalaci sady .NET Core hostování](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span><span class="sxs-lookup"><span data-stu-id="d6fc5-138">See [Install the .NET Core Hosting bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span> <span data-ttu-id="d6fc5-139">Vyberte **opravit** při spuštění Instalační služby.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-139">Select **Repair** when the installer is run.</span></span>

## <a name="missing-site-extension-32-bit-x86-and-64-bit-x64-site-extensions-installed-or-wrong-process-bitness-set"></a><span data-ttu-id="d6fc5-140">Chybějící rozšíření webu, (x86) 32bitové a 64bitové (x64) nainstalované rozšíření webu, nebo nastavení bitové verze nesprávné procesu</span><span class="sxs-lookup"><span data-stu-id="d6fc5-140">Missing site extension, 32-bit (x86) and 64-bit (x64) site extensions installed, or wrong process bitness set</span></span>

<span data-ttu-id="d6fc5-141">*Platí pro aplikace hostované v Azure App Service.*</span><span class="sxs-lookup"><span data-stu-id="d6fc5-141">*Applies to apps hosted by Azure App Services.*</span></span>

* <span data-ttu-id="d6fc5-142">**Prohlížeč:** Chyba protokolu HTTP 500.0 - ANCM v procesu selhání načtení obslužné rutiny</span><span class="sxs-lookup"><span data-stu-id="d6fc5-142">**Browser:** HTTP Error 500.0 - ANCM In-Process Handler Load Failure</span></span> 

* <span data-ttu-id="d6fc5-143">**Protokol aplikace:** Vyvolání hostfxr najít obslužné rutiny inprocess požadavku se nezdařilo bez hledání všechny nativní závislosti.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-143">**Application Log:** Invoking hostfxr to find the inprocess request handler failed without finding any native dependencies.</span></span> <span data-ttu-id="d6fc5-144">Nelze najít inprocess žádost o obslužnou rutinu.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-144">Could not find inprocess request handler.</span></span> <span data-ttu-id="d6fc5-145">Vyvolání hostfxr zachycené výstup: Nebylo možné najít žádné architektura kompatibilní verzi.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-145">Captured output from invoking hostfxr: It was not possible to find any compatible framework version.</span></span> <span data-ttu-id="d6fc5-146">Zadané rozhraní "Microsoft.AspNetCore.App", verze: {VERSION} - preview –\*' nebyl nalezen.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-146">The specified framework 'Microsoft.AspNetCore.App', version '{VERSION}-preview-\*' was not found.</span></span> <span data-ttu-id="d6fc5-147">Nepovedlo se spustit aplikaci "/ LM/W3SVC/1416782824/ROOT", '0x8000ffff' kód chyby.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-147">Failed to start application '/LM/W3SVC/1416782824/ROOT', ErrorCode '0x8000ffff'.</span></span>

* <span data-ttu-id="d6fc5-148">**ASP.NET Core modulu stdout protokolu:** Nebylo možné najít žádné architektura kompatibilní verzi.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-148">**ASP.NET Core Module stdout Log:** It was not possible to find any compatible framework version.</span></span> <span data-ttu-id="d6fc5-149">Zadané rozhraní "Microsoft.AspNetCore.App", verze: {VERSION} - preview –\*' nebyl nalezen.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-149">The specified framework 'Microsoft.AspNetCore.App', version '{VERSION}-preview-\*' was not found.</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="d6fc5-150">**Modul ASP.NET Core protokol ladění:** Vyvolání hostfxr najít obslužné rutiny inprocess požadavku se nezdařilo bez hledání všechny nativní závislosti.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-150">**ASP.NET Core Module Debug Log:** Invoking hostfxr to find the inprocess request handler failed without finding any native dependencies.</span></span> <span data-ttu-id="d6fc5-151">To pravděpodobně znamená, že aplikace není správně nakonfigurovaný, Zkontrolujte prosím verze balíčky Microsoft.NetCore.App a Microsoft.AspNetCore.App, které jsou cílem aplikace a jsou nainstalovány v počítači.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-151">This most likely means the app is misconfigured, please check the versions of Microsoft.NetCore.App and Microsoft.AspNetCore.App that are targeted by the application and are installed on the machine.</span></span> <span data-ttu-id="d6fc5-152">Vrátí selhání HRESULT: 0x8000ffff.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-152">Failed HRESULT returned: 0x8000ffff.</span></span> <span data-ttu-id="d6fc5-153">Nelze najít inprocess žádost o obslužnou rutinu.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-153">Could not find inprocess request handler.</span></span> <span data-ttu-id="d6fc5-154">Nebylo možné najít žádné architektura kompatibilní verzi.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-154">It was not possible to find any compatible framework version.</span></span> <span data-ttu-id="d6fc5-155">Zadané rozhraní "Microsoft.AspNetCore.App", verze: {VERSION} - preview –\*' nebyl nalezen.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-155">The specified framework 'Microsoft.AspNetCore.App', version '{VERSION}-preview-\*' was not found.</span></span>

::: moniker-end

<span data-ttu-id="d6fc5-156">Řešení potíží:</span><span class="sxs-lookup"><span data-stu-id="d6fc5-156">Troubleshooting:</span></span>

* <span data-ttu-id="d6fc5-157">Pokud je aplikace spuštěna v modulu runtime ve verzi preview, instalovat buď 32-bit (x86) **nebo** 64-bit (x64) lokality příponu, která odpovídá počtu bitů aplikace a verze modulu runtime aplikace.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-157">If running the app on a preview runtime, install either the 32-bit (x86) **or** 64-bit (x64) site extension that matches the bitness of the app and the app's runtime version.</span></span> <span data-ttu-id="d6fc5-158">**Neprovádějte instalaci rozšíření nebo více verzí modulu runtime rozšíření.**</span><span class="sxs-lookup"><span data-stu-id="d6fc5-158">**Don't install both extensions or multiple runtime versions of the extension.**</span></span>

  * <span data-ttu-id="d6fc5-159">ASP.NET Core {RUNTIME VERSION} (x86) Runtime</span><span class="sxs-lookup"><span data-stu-id="d6fc5-159">ASP.NET Core {RUNTIME VERSION} (x86) Runtime</span></span>
  * <span data-ttu-id="d6fc5-160">ASP.NET Core {RUNTIME VERSION} (x64) Runtime</span><span class="sxs-lookup"><span data-stu-id="d6fc5-160">ASP.NET Core {RUNTIME VERSION} (x64) Runtime</span></span>

  <span data-ttu-id="d6fc5-161">Restartujte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-161">Restart the app.</span></span> <span data-ttu-id="d6fc5-162">Počkejte několik sekund pro aplikaci restartovat.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-162">Wait several seconds for the app to restart.</span></span> 

* <span data-ttu-id="d6fc5-163">Pokud aplikace běží na modulu runtime ve verzi preview a (x86) 32bitové i 64bitové (x64) [rozšířením webu](xref:host-and-deploy/azure-apps/index#install-the-preview-site-extension) jsou nainstalovány, odinstalujte rozšíření webu, který neodpovídá počtu bitů aplikace.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-163">If running the app on a preview runtime and both the 32-bit (x86) and 64-bit (x64) [site extensions](xref:host-and-deploy/azure-apps/index#install-the-preview-site-extension) are installed, uninstall the site extension that doesn't match the bitness of the app.</span></span> <span data-ttu-id="d6fc5-164">Po odebrání rozšíření webu, restartujte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-164">After removing the site extension, restart the app.</span></span> <span data-ttu-id="d6fc5-165">Počkejte několik sekund pro aplikaci restartovat.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-165">Wait several seconds for the app to restart.</span></span>

* <span data-ttu-id="d6fc5-166">Pokud spuštění aplikace v modulu runtime ve verzi preview a rozšíření bitové verze shody, které aplikace, které potvrzení verze preview webu rozšíření *verze modulu runtime* odpovídá verzi modulu runtime aplikace.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-166">If running the app on a preview runtime and the site extension's bitness matches that of the app, confirm that the preview site extension's *runtime version* matches the app's runtime version.</span></span>

* <span data-ttu-id="d6fc5-167">Ujistěte se, že aplikace **platformy** v **nastavení aplikace** odpovídá počtu bitů aplikace.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-167">Confirm that the app's **Platform** in **Application Settings** matches the bitness of the app.</span></span>

<span data-ttu-id="d6fc5-168">Další informace naleznete v tématu <xref:host-and-deploy/azure-apps/index#install-the-preview-site-extension>.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-168">For more information, see <xref:host-and-deploy/azure-apps/index#install-the-preview-site-extension>.</span></span>

## <a name="an-x86-app-is-deployed-but-the-app-pool-isnt-enabled-for-32-bit-apps"></a><span data-ttu-id="d6fc5-169">X x86 byla nasazena aplikace, ale fond aplikací není povolená pro 32bitové aplikace</span><span class="sxs-lookup"><span data-stu-id="d6fc5-169">An x86 app is deployed but the app pool isn't enabled for 32-bit apps</span></span>

* <span data-ttu-id="d6fc5-170">**Prohlížeč:** Chyba protokolu HTTP 500.30 - ANCM selhání spuštění v procesu</span><span class="sxs-lookup"><span data-stu-id="d6fc5-170">**Browser:** HTTP Error 500.30 - ANCM In-Process Start Failure</span></span>

* <span data-ttu-id="d6fc5-171">**Protokol aplikace:** Aplikace/LM/W3SVC/5/ROOT se fyzické kořenové {PATH} došlo k neočekávané výjimce spravované, kód výjimky = "0xe0434352".</span><span class="sxs-lookup"><span data-stu-id="d6fc5-171">**Application Log:** Application '/LM/W3SVC/5/ROOT' with physical root '{PATH}' hit unexpected managed exception, exception code = '0xe0434352'.</span></span> <span data-ttu-id="d6fc5-172">Další informace naleznete v protokolech stderr.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-172">Please check the stderr logs for more information.</span></span> <span data-ttu-id="d6fc5-173">Aplikace/LM/W3SVC/5/ROOT se fyzické kořenem {PATH} se nezdařilo načtení modulu clr a spravované aplikace.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-173">Application '/LM/W3SVC/5/ROOT' with physical root '{PATH}' failed to load clr and managed application.</span></span> <span data-ttu-id="d6fc5-174">Pracovní vlákno CLR byl předčasně ukončen.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-174">CLR worker thread exited prematurely</span></span>

* <span data-ttu-id="d6fc5-175">**ASP.NET Core modulu stdout protokolu:** Soubor protokolu je vytvořený, ale prázdný.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-175">**ASP.NET Core Module stdout Log:** The log file is created but empty.</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="d6fc5-176">**Modul ASP.NET Core protokol ladění:** Vrátí selhání HRESULT: 0x8007023e</span><span class="sxs-lookup"><span data-stu-id="d6fc5-176">**ASP.NET Core Module Debug Log:** Failed HRESULT returned: 0x8007023e</span></span>

::: moniker-end

<span data-ttu-id="d6fc5-177">Tento scénář je zachycena prostřednictvím sady SDK při publikování samostatnou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-177">This scenario is trapped by the SDK when publishing a self-contained app.</span></span> <span data-ttu-id="d6fc5-178">Sada SDK způsobí chybu, pokud identifikátor RID neodpovídá cílové platformy (například `win10-x64` identifikátorů RID s `<PlatformTarget>x86</PlatformTarget>` v souboru projektu).</span><span class="sxs-lookup"><span data-stu-id="d6fc5-178">The SDK produces an error if the RID doesn't match the platform target (for example, `win10-x64` RID with `<PlatformTarget>x86</PlatformTarget>` in the project file).</span></span>

<span data-ttu-id="d6fc5-179">Řešení potíží:</span><span class="sxs-lookup"><span data-stu-id="d6fc5-179">Troubleshooting:</span></span>

<span data-ttu-id="d6fc5-180">Pro x x86 nasazení závisí na architektuře (`<PlatformTarget>x86</PlatformTarget>`), povolit fond aplikací služby IIS pro 32bitové aplikace.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-180">For an x86 framework-dependent deployment (`<PlatformTarget>x86</PlatformTarget>`), enable the IIS app pool for 32-bit apps.</span></span> <span data-ttu-id="d6fc5-181">Ve Správci služby IIS otevřete fond aplikací **Upřesnit nastavení** a nastavte **povolit 32bitové aplikace** k **True**.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-181">In IIS Manager, open the app pool's **Advanced Settings** and set **Enable 32-Bit Applications** to **True**.</span></span>

## <a name="platform-conflicts-with-rid"></a><span data-ttu-id="d6fc5-182">Platformu je v konfliktu s identifikátorů RID</span><span class="sxs-lookup"><span data-stu-id="d6fc5-182">Platform conflicts with RID</span></span>

* <span data-ttu-id="d6fc5-183">**Prohlížeč:** Chyba protokolu HTTP 502.5 – selhání procesu</span><span class="sxs-lookup"><span data-stu-id="d6fc5-183">**Browser:** HTTP Error 502.5 - Process Failure</span></span>

* <span data-ttu-id="d6fc5-184">**Protokol aplikace:** Aplikace "MACHINE/WEBROOT nebo APPHOST / {sestavení}' s kořenem fyzické ' C:\{CESTU}\' Nepodařilo se spustit proces pomocí příkazového řádku" "C:\{CESTU} {sestavení}. { soubor EXE | dll} "', kód chyby =" 0x80004005: ff.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-184">**Application Log:** Application 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' with physical root 'C:\{PATH}\' failed to start process with commandline '"C:\{PATH}{ASSEMBLY}.{exe|dll}" ', ErrorCode = '0x80004005 : ff.</span></span>

* <span data-ttu-id="d6fc5-185">**ASP.NET Core modulu stdout protokolu:** Neošetřená výjimka: System.BadImageFormatException: Nelze načíst soubor nebo sestavení "{sestavení} .dll".</span><span class="sxs-lookup"><span data-stu-id="d6fc5-185">**ASP.NET Core Module stdout Log:** Unhandled Exception: System.BadImageFormatException: Could not load file or assembly '{ASSEMBLY}.dll'.</span></span> <span data-ttu-id="d6fc5-186">Došlo pokusu o načtení programu v nesprávném formátu.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-186">An attempt was made to load a program with an incorrect format.</span></span>

<span data-ttu-id="d6fc5-187">Řešení potíží:</span><span class="sxs-lookup"><span data-stu-id="d6fc5-187">Troubleshooting:</span></span>

* <span data-ttu-id="d6fc5-188">Potvrďte, že aplikace běží místně na Kestrel.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-188">Confirm that the app runs locally on Kestrel.</span></span> <span data-ttu-id="d6fc5-189">Selhání procesu může být výsledkem problému v rámci aplikace.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-189">A process failure might be the result of a problem within the app.</span></span> <span data-ttu-id="d6fc5-190">Další informace najdete v tématu [řešení (IIS)](xref:host-and-deploy/iis/troubleshoot) nebo [řešení potíží (Azure App Service)](xref:host-and-deploy/azure-apps/troubleshoot).</span><span class="sxs-lookup"><span data-stu-id="d6fc5-190">For more information, see [Troubleshoot (IIS)](xref:host-and-deploy/iis/troubleshoot) or [Troubleshoot (Azure App Service)](xref:host-and-deploy/azure-apps/troubleshoot).</span></span>

* <span data-ttu-id="d6fc5-191">Pokud tuto výjimku dojde k nasazení aplikace Azure při upgradu aplikace a nasazení novější sestavení, ručně odstraňte všechny soubory z předchozího nasazení.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-191">If this exception occurs for an Azure Apps deployment when upgrading an app and deploying newer assemblies, manually delete all files from the prior deployment.</span></span> <span data-ttu-id="d6fc5-192">Přetrvávání odstraněných nekompatibilní sestavení může mít za následek `System.BadImageFormatException` při nasazení upgradovaných aplikace došlo k výjimce.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-192">Lingering incompatible assemblies can result in a `System.BadImageFormatException` exception when deploying an upgraded app.</span></span>

## <a name="uri-endpoint-wrong-or-stopped-website"></a><span data-ttu-id="d6fc5-193">Identifikátor URI koncového bodu nesprávné nebo zastavená webu</span><span class="sxs-lookup"><span data-stu-id="d6fc5-193">URI endpoint wrong or stopped website</span></span>

* <span data-ttu-id="d6fc5-194">**Prohlížeč:** ERR_CONNECTION_REFUSED **--nebo--** nelze provést připojení</span><span class="sxs-lookup"><span data-stu-id="d6fc5-194">**Browser:** ERR_CONNECTION_REFUSED **--OR--** Unable to connect</span></span>

* <span data-ttu-id="d6fc5-195">**Protokol aplikace:** Žádná položka</span><span class="sxs-lookup"><span data-stu-id="d6fc5-195">**Application Log:** No entry</span></span>

* <span data-ttu-id="d6fc5-196">**ASP.NET Core modulu stdout protokolu:** Soubor protokolu se vytvoří.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-196">**ASP.NET Core Module stdout Log:** The log file isn't created.</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="d6fc5-197">**Modul ASP.NET Core protokol ladění:** Soubor protokolu se vytvoří.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-197">**ASP.NET Core Module Debug Log:** The log file isn't created.</span></span>

::: moniker-end

<span data-ttu-id="d6fc5-198">Řešení potíží:</span><span class="sxs-lookup"><span data-stu-id="d6fc5-198">Troubleshooting:</span></span>

* <span data-ttu-id="d6fc5-199">Potvrďte, že se používá na správný koncový bod identifikátoru URI pro aplikaci.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-199">Confirm the correct URI endpoint for the app is in use.</span></span> <span data-ttu-id="d6fc5-200">Zkontrolujte vazby.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-200">Check the bindings.</span></span>

* <span data-ttu-id="d6fc5-201">Potvrďte, že web služby IIS není v *Zastaveno* stavu.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-201">Confirm that the IIS website isn't in the *Stopped* state.</span></span>

## <a name="corewebengine-or-w3svc-server-features-disabled"></a><span data-ttu-id="d6fc5-202">Funkce serveru CoreWebEngine nebo W3SVC zakázána</span><span class="sxs-lookup"><span data-stu-id="d6fc5-202">CoreWebEngine or W3SVC server features disabled</span></span>

<span data-ttu-id="d6fc5-203">**Výjimka operačního systému:** Pokud chcete použít modul ASP.NET Core je nutné nainstalovat funkce služby IIS 7.0 CoreWebEngine a W3SVC.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-203">**OS Exception:** The IIS 7.0 CoreWebEngine and W3SVC features must be installed to use the ASP.NET Core Module.</span></span>

<span data-ttu-id="d6fc5-204">Řešení potíží:</span><span class="sxs-lookup"><span data-stu-id="d6fc5-204">Troubleshooting:</span></span>

<span data-ttu-id="d6fc5-205">Potvrďte, že jsou povolené správné role a funkce.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-205">Confirm that the proper role and features are enabled.</span></span> <span data-ttu-id="d6fc5-206">Zobrazit [konfiguraci služby IIS](xref:host-and-deploy/iis/index#iis-configuration).</span><span class="sxs-lookup"><span data-stu-id="d6fc5-206">See [IIS Configuration](xref:host-and-deploy/iis/index#iis-configuration).</span></span>

## <a name="incorrect-website-physical-path-or-app-missing"></a><span data-ttu-id="d6fc5-207">Fyzická cesta k webu nesprávná nebo chybějící aplikace</span><span class="sxs-lookup"><span data-stu-id="d6fc5-207">Incorrect website physical path or app missing</span></span>

* <span data-ttu-id="d6fc5-208">**Prohlížeč:** 403 Zakázáno - přístup byl odepřen. **--nebo--** 403.14 – zakázáno. ve webovém serveru je nakonfigurovaný na obsah tohoto adresáře v seznamu.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-208">**Browser:** 403 Forbidden - Access is denied **--OR--** 403.14 Forbidden - The Web server is configured to not list the contents of this directory.</span></span>

* <span data-ttu-id="d6fc5-209">**Protokol aplikace:** Žádná položka</span><span class="sxs-lookup"><span data-stu-id="d6fc5-209">**Application Log:** No entry</span></span>

* <span data-ttu-id="d6fc5-210">**ASP.NET Core modulu stdout protokolu:** Soubor protokolu se vytvoří.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-210">**ASP.NET Core Module stdout Log:** The log file isn't created.</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="d6fc5-211">**Modul ASP.NET Core protokol ladění:** Soubor protokolu se vytvoří.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-211">**ASP.NET Core Module Debug Log:** The log file isn't created.</span></span>

::: moniker-end

<span data-ttu-id="d6fc5-212">Řešení potíží:</span><span class="sxs-lookup"><span data-stu-id="d6fc5-212">Troubleshooting:</span></span>

<span data-ttu-id="d6fc5-213">Zkontrolujte web služby IIS **základní nastavení** a složky fyzické aplikace.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-213">Check the IIS website **Basic Settings** and the physical app folder.</span></span> <span data-ttu-id="d6fc5-214">Potvrďte, že aplikace bude ve složce na webu služby IIS **fyzická cesta**.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-214">Confirm that the app is in the folder at the IIS website **Physical path**.</span></span>

## <a name="incorrect-role-aspnet-core-module-not-installed-or-incorrect-permissions"></a><span data-ttu-id="d6fc5-215">Nesprávný role, není nainstalovaný modul ASP.NET Core nebo nesprávná oprávnění</span><span class="sxs-lookup"><span data-stu-id="d6fc5-215">Incorrect role, ASP.NET Core Module not installed, or incorrect permissions</span></span>

* <span data-ttu-id="d6fc5-216">**Prohlížeč:** 500.19 interní chyba serveru – požadovanou stránku nelze získat přístup, protože data související konfigurační stránky jsou neplatná.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-216">**Browser:** 500.19 Internal Server Error - The requested page cannot be accessed because the related configuration data for the page is invalid.</span></span> <span data-ttu-id="d6fc5-217">**--NEBO--** tuto stránku nelze zobrazit.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-217">**--OR--** This page can't be displayed</span></span>

* <span data-ttu-id="d6fc5-218">**Protokol aplikace:** Žádná položka</span><span class="sxs-lookup"><span data-stu-id="d6fc5-218">**Application Log:** No entry</span></span>

* <span data-ttu-id="d6fc5-219">**ASP.NET Core modulu stdout protokolu:** Soubor protokolu se vytvoří.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-219">**ASP.NET Core Module stdout Log:** The log file isn't created.</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="d6fc5-220">**Modul ASP.NET Core protokol ladění:** Soubor protokolu se vytvoří.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-220">**ASP.NET Core Module Debug Log:** The log file isn't created.</span></span>

::: moniker-end

<span data-ttu-id="d6fc5-221">Řešení potíží:</span><span class="sxs-lookup"><span data-stu-id="d6fc5-221">Troubleshooting:</span></span>

* <span data-ttu-id="d6fc5-222">Potvrďte, že je povoleno správné roli.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-222">Confirm that the proper role is enabled.</span></span> <span data-ttu-id="d6fc5-223">Zobrazit [konfiguraci služby IIS](xref:host-and-deploy/iis/index#iis-configuration).</span><span class="sxs-lookup"><span data-stu-id="d6fc5-223">See [IIS Configuration](xref:host-and-deploy/iis/index#iis-configuration).</span></span>

* <span data-ttu-id="d6fc5-224">Otevřít **programy a funkce** nebo **aplikace a funkce** a ujistěte se, že **Windows serveru, který hostuje** je nainstalována.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-224">Open **Programs & Features** or **Apps & features** and confirm that **Windows Server Hosting** is installed.</span></span> <span data-ttu-id="d6fc5-225">Pokud **Windows serveru, který hostuje** není k dispozici v seznamu nainstalovaných programů, stahování a instalace sady .NET Core hostování.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-225">If **Windows Server Hosting** isn't present in the list of installed programs, download and install the .NET Core Hosting Bundle.</span></span>

  [<span data-ttu-id="d6fc5-226">Aktuální instalační program sady hostování rozhraní .NET Core (s přímým přístupem ke stažení)</span><span class="sxs-lookup"><span data-stu-id="d6fc5-226">Current .NET Core Hosting Bundle installer (direct download)</span></span>](https://www.microsoft.com/net/permalink/dotnetcore-current-windows-runtime-bundle-installer)

  <span data-ttu-id="d6fc5-227">Další informace najdete v tématu [instalaci sady .NET Core hostování](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span><span class="sxs-lookup"><span data-stu-id="d6fc5-227">For more information, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

* <span data-ttu-id="d6fc5-228">Ujistěte se, že **fond aplikací** > **Model procesu** > **Identity** je nastavena na **ApplicationPoolIdentity** nebo vlastní identita má správná oprávnění pro přístup ke složce pro nasazení aplikace.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-228">Make sure that the **Application Pool** > **Process Model** > **Identity** is set to **ApplicationPoolIdentity** or the custom identity has the correct permissions to access the app's deployment folder.</span></span>

* <span data-ttu-id="d6fc5-229">Pokud odinstalaci sady hostování technologie ASP.NET Core a nainstalovat dřívější verzi sady hostování *applicationHost.config* soubor neobsahuje oddíl pro modul ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-229">If you uninstalled the ASP.NET Core Hosting Bundle and installed an earlier version of the hosting bundle, the *applicationHost.config* file doesn't include a section for the ASP.NET Core Module.</span></span> <span data-ttu-id="d6fc5-230">Otevřít *applicationHost.config* na *%windir%/System32/inetsrv/config* a najít `<configuration><configSections><sectionGroup name="system.webServer">` skupiny oddílů.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-230">Open *applicationHost.config* at *%windir%/System32/inetsrv/config* and find the `<configuration><configSections><sectionGroup name="system.webServer">` section group.</span></span> <span data-ttu-id="d6fc5-231">Pokud ze skupiny oddílů chybí část pro modul ASP.NET Core, přidejte prvek části:</span><span class="sxs-lookup"><span data-stu-id="d6fc5-231">If the section for the ASP.NET Core Module is missing from the section group, add the section element:</span></span>

  ```xml
  <section name="aspNetCore" overrideModeDefault="Allow" />
  ```
  
  <span data-ttu-id="d6fc5-232">Můžete také nainstalujte nejnovější verzi sady hostování technologie ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-232">Alternatively, install the lastest version of the ASP.NET Core Hosting Bundle.</span></span> <span data-ttu-id="d6fc5-233">Nejnovější verze je zpětně kompatibilní s aplikací ASP.NET Core s podporou.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-233">The latest version is backwards-compatible with supported ASP.NET Core apps.</span></span>

## <a name="incorrect-processpath-missing-path-variable-hosting-bundle-not-installed-systemiis-not-restarted-vc-redistributable-not-installed-or-dotnetexe-access-violation"></a><span data-ttu-id="d6fc5-234">Nesprávné processPath, chybějící proměnné PATH, hostování sady není nainstalovaný, není restartování systému/IIS, VC ++ Redistributable není nainstalovaný nebo dotnet.exe narušení přístupu</span><span class="sxs-lookup"><span data-stu-id="d6fc5-234">Incorrect processPath, missing PATH variable, Hosting Bundle not installed, system/IIS not restarted, VC++ Redistributable not installed, or dotnet.exe access violation</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="d6fc5-235">**Prohlížeč:** Chyba protokolu HTTP 500.0 - ANCM v procesu selhání načtení obslužné rutiny</span><span class="sxs-lookup"><span data-stu-id="d6fc5-235">**Browser:** HTTP Error 500.0 - ANCM In-Process Handler Load Failure</span></span>

* <span data-ttu-id="d6fc5-236">**Protokol aplikace:** Aplikace "MACHINE/WEBROOT nebo APPHOST / {sestavení}' s kořenem fyzické ' C:\{CESTU}\' Nepodařilo se spustit proces pomocí příkazového řádku '"{...}"</span><span class="sxs-lookup"><span data-stu-id="d6fc5-236">**Application Log:** Application 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' with physical root 'C:\{PATH}\' failed to start process with commandline '"{...}"</span></span> <span data-ttu-id="d6fc5-237">", Kód chyby =" 0x80070002: 0.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-237">', ErrorCode = '0x80070002 : 0.</span></span> <span data-ttu-id="d6fc5-238">Aplikace {PATH} nebylo možné spustit.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-238">Application '{PATH}' wasn't able to start.</span></span> <span data-ttu-id="d6fc5-239">Spustitelný soubor se nenašel v {PATH}.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-239">Executable was not found at '{PATH}'.</span></span> <span data-ttu-id="d6fc5-240">Nepovedlo se spustit aplikaci "/ LM/W3SVC/2/ROOT", '0x8007023e' kód chyby.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-240">Failed to start application '/LM/W3SVC/2/ROOT', ErrorCode '0x8007023e'.</span></span>

* <span data-ttu-id="d6fc5-241">**ASP.NET Core modulu stdout protokolu:** Soubor protokolu se vytvoří.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-241">**ASP.NET Core Module stdout Log:** The log file isn't created.</span></span>

* <span data-ttu-id="d6fc5-242">**Modul ASP.NET Core protokol ladění:** Protokol událostí: "Aplikace {PATH} nebylo možné spustit.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-242">**ASP.NET Core Module Debug Log:** Event Log: 'Application '{PATH}' wasn't able to start.</span></span> <span data-ttu-id="d6fc5-243">Spustitelný soubor se nenašel v {PATH}.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-243">Executable was not found at '{PATH}'.</span></span> <span data-ttu-id="d6fc5-244">Vrátí selhání HRESULT: 0x8007023e</span><span class="sxs-lookup"><span data-stu-id="d6fc5-244">Failed HRESULT returned: 0x8007023e</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* <span data-ttu-id="d6fc5-245">**Prohlížeč:** Chyba protokolu HTTP 502.5 – selhání procesu</span><span class="sxs-lookup"><span data-stu-id="d6fc5-245">**Browser:** HTTP Error 502.5 - Process Failure</span></span>

* <span data-ttu-id="d6fc5-246">**Protokol aplikace:** Aplikace "MACHINE/WEBROOT nebo APPHOST / {sestavení}' s kořenem fyzické ' C:\{CESTU}\' Nepodařilo se spustit proces pomocí příkazového řádku '"{...}"</span><span class="sxs-lookup"><span data-stu-id="d6fc5-246">**Application Log:** Application 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' with physical root 'C:\{PATH}\' failed to start process with commandline '"{...}"</span></span> <span data-ttu-id="d6fc5-247">", Kód chyby =" 0x80070002: 0.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-247">', ErrorCode = '0x80070002 : 0.</span></span>

* <span data-ttu-id="d6fc5-248">**ASP.NET Core modulu stdout protokolu:** Soubor protokolu je vytvořený, ale prázdný.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-248">**ASP.NET Core Module stdout Log:** The log file is created but empty.</span></span>

::: moniker-end

<span data-ttu-id="d6fc5-249">Řešení potíží:</span><span class="sxs-lookup"><span data-stu-id="d6fc5-249">Troubleshooting:</span></span>

* <span data-ttu-id="d6fc5-250">Potvrďte, že aplikace běží místně na Kestrel.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-250">Confirm that the app runs locally on Kestrel.</span></span> <span data-ttu-id="d6fc5-251">Selhání procesu může být výsledkem problému v rámci aplikace.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-251">A process failure might be the result of a problem within the app.</span></span> <span data-ttu-id="d6fc5-252">Další informace najdete v tématu [řešení (IIS)](xref:host-and-deploy/iis/troubleshoot) nebo [řešení potíží (Azure App Service)](xref:host-and-deploy/azure-apps/troubleshoot).</span><span class="sxs-lookup"><span data-stu-id="d6fc5-252">For more information, see [Troubleshoot (IIS)](xref:host-and-deploy/iis/troubleshoot) or [Troubleshoot (Azure App Service)](xref:host-and-deploy/azure-apps/troubleshoot).</span></span>

* <span data-ttu-id="d6fc5-253">Zkontrolujte *processPath* atribut na `<aspNetCore>` element v *web.config* potvrďte, že je `dotnet` nasazení závisí na architektuře (chyba) nebo `.\{ASSEMBLY}.exe` pro [samostatná nasazení (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span><span class="sxs-lookup"><span data-stu-id="d6fc5-253">Check the *processPath* attribute on the `<aspNetCore>` element in *web.config* to confirm that it's `dotnet` for a framework-dependent deployment (FDD) or `.\{ASSEMBLY}.exe` for a [self-contained deployment (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>

* <span data-ttu-id="d6fc5-254">Pro disketové jednotky *dotnet.exe* nemusí být přístupné přes nastavení cesty.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-254">For an FDD, *dotnet.exe* might not be accessible via the PATH settings.</span></span> <span data-ttu-id="d6fc5-255">Ujistěte se, že *C:\Program Files\dotnet\\*  existuje v systémové CESTĚ nastavení.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-255">Confirm that *C:\Program Files\dotnet\\* exists in the System PATH settings.</span></span>

* <span data-ttu-id="d6fc5-256">Pro disketové jednotky *dotnet.exe* nemusí být dostupný pro identitu uživatele fondu aplikací.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-256">For an FDD, *dotnet.exe* might not be accessible for the user identity of the app pool.</span></span> <span data-ttu-id="d6fc5-257">Potvrďte, že identita uživatele fondu aplikací má přístup k *C:\Program Files\dotnet* adresáře.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-257">Confirm that the app pool user identity has access to the *C:\Program Files\dotnet* directory.</span></span> <span data-ttu-id="d6fc5-258">Ověřte, zda jsou nakonfigurovaná pro identitu uživatele fondu aplikací na žádná pravidla Odepřít *C:\Program Files\dotnet* a adresáře aplikace.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-258">Confirm that there are no deny rules configured for the app pool user identity on the *C:\Program Files\dotnet* and app directories.</span></span>

* <span data-ttu-id="d6fc5-259">Disketové jednotky, byla nasazena a .NET Core instalaci bez nutnosti restartování služby IIS.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-259">An FDD may have been deployed and .NET Core installed without restarting IIS.</span></span> <span data-ttu-id="d6fc5-260">Restartujte server nebo restartování služby IIS pomocí provádí **net stop byla /y** následovaný **net start w3svc** z příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-260">Either restart the server or restart IIS by executing **net stop was /y** followed by **net start w3svc** from a command prompt.</span></span>

* <span data-ttu-id="d6fc5-261">Disketové jednotky je mohou nasadit bez nutnosti instalace modulu runtime .NET Core v hostitelském systému.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-261">An FDD may have been deployed without installing the .NET Core runtime on the hosting system.</span></span> <span data-ttu-id="d6fc5-262">Pokud nebyl nainstalován modul runtime .NET Core, spusťte **instalační program sady hostování rozhraní .NET Core** v systému.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-262">If the .NET Core runtime hasn't been installed, run the **.NET Core Hosting Bundle installer** on the system.</span></span>

  [<span data-ttu-id="d6fc5-263">Aktuální instalační program sady hostování rozhraní .NET Core (s přímým přístupem ke stažení)</span><span class="sxs-lookup"><span data-stu-id="d6fc5-263">Current .NET Core Hosting Bundle installer (direct download)</span></span>](https://www.microsoft.com/net/permalink/dotnetcore-current-windows-runtime-bundle-installer)

  <span data-ttu-id="d6fc5-264">Další informace najdete v tématu [instalaci sady .NET Core hostování](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span><span class="sxs-lookup"><span data-stu-id="d6fc5-264">For more information, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

  <span data-ttu-id="d6fc5-265">Pokud konkrétní modul runtime je vyžadována, stáhněte si modul runtime z [archivy stáhnout .NET](https://dotnet.microsoft.com/download/archives) a nainstalujte ho na systém.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-265">If a specific runtime is required, download the runtime from the [.NET Download Archives](https://dotnet.microsoft.com/download/archives) and install it on the system.</span></span> <span data-ttu-id="d6fc5-266">Dokončete instalaci restartováním systému nebo restartování služby IIS pomocí provádí **net stop byla /y** následovaný **net start w3svc** z příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-266">Complete the installation by restarting the system or restarting IIS by executing **net stop was /y** followed by **net start w3svc** from a command prompt.</span></span>

* <span data-ttu-id="d6fc5-267">Disketové jednotky, byla nasazena a *Microsoft Visual C++ 2015 Redistributable (x64)* není nainstalovaná v systému.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-267">An FDD may have been deployed and the *Microsoft Visual C++ 2015 Redistributable (x64)* isn't installed on the system.</span></span> <span data-ttu-id="d6fc5-268">Získat z instalačního programu [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=53840).</span><span class="sxs-lookup"><span data-stu-id="d6fc5-268">Obtain an installer from the [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=53840).</span></span>

## <a name="incorrect-arguments-of-aspnetcore-element"></a><span data-ttu-id="d6fc5-269">Nesprávné argumenty \<aspNetCore > – element</span><span class="sxs-lookup"><span data-stu-id="d6fc5-269">Incorrect arguments of \<aspNetCore> element</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="d6fc5-270">**Prohlížeč:** Chyba protokolu HTTP 500.0 - ANCM v procesu selhání načtení obslužné rutiny</span><span class="sxs-lookup"><span data-stu-id="d6fc5-270">**Browser:** HTTP Error 500.0 - ANCM In-Process Handler Load Failure</span></span>

* <span data-ttu-id="d6fc5-271">**Protokol aplikace:** Vyvolání hostfxr najít obslužné rutiny inprocess požadavku se nezdařilo bez hledání všechny nativní závislosti.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-271">**Application Log:** Invoking hostfxr to find the inprocess request handler failed without finding any native dependencies.</span></span> <span data-ttu-id="d6fc5-272">To pravděpodobně znamená, že aplikace není správně nakonfigurovaný, Zkontrolujte prosím verze balíčky Microsoft.NetCore.App a Microsoft.AspNetCore.App, které jsou cílem aplikace a jsou nainstalovány v počítači.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-272">This most likely means the app is misconfigured, please check the versions of Microsoft.NetCore.App and Microsoft.AspNetCore.App that are targeted by the application and are installed on the machine.</span></span> <span data-ttu-id="d6fc5-273">Nelze najít inprocess žádost o obslužnou rutinu.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-273">Could not find inprocess request handler.</span></span> <span data-ttu-id="d6fc5-274">Vyvolání hostfxr zachycené výstup: Nechtěli jste dotnet spustit příkazy SDK?</span><span class="sxs-lookup"><span data-stu-id="d6fc5-274">Captured output from invoking hostfxr: Did you mean to run dotnet SDK commands?</span></span> <span data-ttu-id="d6fc5-275">Nainstalujte dotnet SDK ze: https://go.microsoft.com/fwlink/?LinkID=798306&clcid=0x409 Nepovedlo se spustit aplikaci "/ LM/W3SVC/3/ROOT", '0x8000ffff' kód chyby.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-275">Please install dotnet SDK from: https://go.microsoft.com/fwlink/?LinkID=798306&clcid=0x409 Failed to start application '/LM/W3SVC/3/ROOT', ErrorCode '0x8000ffff'.</span></span>

* <span data-ttu-id="d6fc5-276">**ASP.NET Core modulu stdout protokolu:** Nechtěli jste dotnet spustit příkazy SDK?</span><span class="sxs-lookup"><span data-stu-id="d6fc5-276">**ASP.NET Core Module stdout Log:** Did you mean to run dotnet SDK commands?</span></span> <span data-ttu-id="d6fc5-277">Nainstalujte dotnet SDK ze: https://go.microsoft.com/fwlink/?LinkID=798306&clcid=0x409</span><span class="sxs-lookup"><span data-stu-id="d6fc5-277">Please install dotnet SDK from: https://go.microsoft.com/fwlink/?LinkID=798306&clcid=0x409</span></span>

* <span data-ttu-id="d6fc5-278">**Modul ASP.NET Core protokol ladění:** Vyvolání hostfxr najít obslužné rutiny inprocess požadavku se nezdařilo bez hledání všechny nativní závislosti.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-278">**ASP.NET Core Module Debug Log:** Invoking hostfxr to find the inprocess request handler failed without finding any native dependencies.</span></span> <span data-ttu-id="d6fc5-279">To pravděpodobně znamená, že aplikace není správně nakonfigurovaný, Zkontrolujte prosím verze balíčky Microsoft.NetCore.App a Microsoft.AspNetCore.App, které jsou cílem aplikace a jsou nainstalovány v počítači.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-279">This most likely means the app is misconfigured, please check the versions of Microsoft.NetCore.App and Microsoft.AspNetCore.App that are targeted by the application and are installed on the machine.</span></span> <span data-ttu-id="d6fc5-280">Vrátí selhání HRESULT: 0x8000ffff nelze najít inprocess žádost o obslužnou rutinu.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-280">Failed HRESULT returned: 0x8000ffff Could not find inprocess request handler.</span></span> <span data-ttu-id="d6fc5-281">Vyvolání hostfxr zachycené výstup: Nechtěli jste dotnet spustit příkazy SDK?</span><span class="sxs-lookup"><span data-stu-id="d6fc5-281">Captured output from invoking hostfxr: Did you mean to run dotnet SDK commands?</span></span> <span data-ttu-id="d6fc5-282">Nainstalujte dotnet SDK ze: https://go.microsoft.com/fwlink/?LinkID=798306&clcid=0x409 Vrátí selhání HRESULT: 0x8000ffff</span><span class="sxs-lookup"><span data-stu-id="d6fc5-282">Please install dotnet SDK from: https://go.microsoft.com/fwlink/?LinkID=798306&clcid=0x409 Failed HRESULT returned: 0x8000ffff</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* <span data-ttu-id="d6fc5-283">**Prohlížeč:** Chyba protokolu HTTP 502.5 – selhání procesu</span><span class="sxs-lookup"><span data-stu-id="d6fc5-283">**Browser:** HTTP Error 502.5 - Process Failure</span></span>

* <span data-ttu-id="d6fc5-284">**Protokol aplikace:** Aplikace "MACHINE/WEBROOT nebo APPHOST / {sestavení}' s kořenem fyzické ' C:\{CESTU}\' Nepodařilo se spustit proces pomocí příkazového řádku" "dotnet".\{ .Dll sestavení}', kód chyby = "0x80004005: 80008081.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-284">**Application Log:** Application 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' with physical root 'C:\{PATH}\' failed to start process with commandline '"dotnet" .\{ASSEMBLY}.dll', ErrorCode = '0x80004005 : 80008081.</span></span>

* <span data-ttu-id="d6fc5-285">**ASP.NET Core modulu stdout protokolu:** Aplikace pro spuštění neexistuje: 'PATH\{ASSEMBLY}.dll'</span><span class="sxs-lookup"><span data-stu-id="d6fc5-285">**ASP.NET Core Module stdout Log:** The application to execute does not exist: 'PATH\{ASSEMBLY}.dll'</span></span>

::: moniker-end

<span data-ttu-id="d6fc5-286">Řešení potíží:</span><span class="sxs-lookup"><span data-stu-id="d6fc5-286">Troubleshooting:</span></span>

* <span data-ttu-id="d6fc5-287">Potvrďte, že aplikace běží místně na Kestrel.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-287">Confirm that the app runs locally on Kestrel.</span></span> <span data-ttu-id="d6fc5-288">Selhání procesu může být výsledkem problému v rámci aplikace.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-288">A process failure might be the result of a problem within the app.</span></span> <span data-ttu-id="d6fc5-289">Další informace najdete v tématu [řešení (IIS)](xref:host-and-deploy/iis/troubleshoot) nebo [řešení potíží (Azure App Service)](xref:host-and-deploy/azure-apps/troubleshoot).</span><span class="sxs-lookup"><span data-stu-id="d6fc5-289">For more information, see [Troubleshoot (IIS)](xref:host-and-deploy/iis/troubleshoot) or [Troubleshoot (Azure App Service)](xref:host-and-deploy/azure-apps/troubleshoot).</span></span>

* <span data-ttu-id="d6fc5-290">Zkontrolujte *argumenty* atribut na `<aspNetCore>` element v *web.config* potvrďte, že je buď (a) `.\{ASSEMBLY}.dll` nasazení závisí na architektuře (chyba); nebo (b) není k dispozici, prázdný řetězec (`arguments=""`), nebo seznam argumentů aplikace (`arguments="{ARGUMENT_1}, {ARGUMENT_2}, ... {ARGUMENT_X}"`) pro samostatné nasazení (SCD).</span><span class="sxs-lookup"><span data-stu-id="d6fc5-290">Examine the *arguments* attribute on the `<aspNetCore>` element in *web.config* to confirm that it's either (a) `.\{ASSEMBLY}.dll` for a framework-dependent deployment (FDD); or (b) not present, an empty string (`arguments=""`), or a list of the app's arguments (`arguments="{ARGUMENT_1}, {ARGUMENT_2}, ... {ARGUMENT_X}"`) for a self-contained deployment (SCD).</span></span>

::: moniker range=">= aspnetcore-2.2"

## <a name="missing-net-core-shared-framework"></a><span data-ttu-id="d6fc5-291">Chybí sdílené architektuře .NET Core</span><span class="sxs-lookup"><span data-stu-id="d6fc5-291">Missing .NET Core shared framework</span></span>

* <span data-ttu-id="d6fc5-292">**Prohlížeč:** Chyba protokolu HTTP 500.0 - ANCM v procesu selhání načtení obslužné rutiny</span><span class="sxs-lookup"><span data-stu-id="d6fc5-292">**Browser:** HTTP Error 500.0 - ANCM In-Process Handler Load Failure</span></span>

* <span data-ttu-id="d6fc5-293">**Protokol aplikace:** Vyvolání hostfxr najít obslužné rutiny inprocess požadavku se nezdařilo bez hledání všechny nativní závislosti.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-293">**Application Log:** Invoking hostfxr to find the inprocess request handler failed without finding any native dependencies.</span></span> <span data-ttu-id="d6fc5-294">To pravděpodobně znamená, že aplikace není správně nakonfigurovaný, Zkontrolujte prosím verze balíčky Microsoft.NetCore.App a Microsoft.AspNetCore.App, které jsou cílem aplikace a jsou nainstalovány v počítači.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-294">This most likely means the app is misconfigured, please check the versions of Microsoft.NetCore.App and Microsoft.AspNetCore.App that are targeted by the application and are installed on the machine.</span></span> <span data-ttu-id="d6fc5-295">Nelze najít inprocess žádost o obslužnou rutinu.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-295">Could not find inprocess request handler.</span></span> <span data-ttu-id="d6fc5-296">Vyvolání hostfxr zachycené výstup: Nebylo možné najít žádné architektura kompatibilní verzi.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-296">Captured output from invoking hostfxr: It was not possible to find any compatible framework version.</span></span> <span data-ttu-id="d6fc5-297">Zadané rozhraní "Microsoft.AspNetCore.App", verze {VERSION} nebyl nalezen.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-297">The specified framework 'Microsoft.AspNetCore.App', version '{VERSION}' was not found.</span></span>

<span data-ttu-id="d6fc5-298">Nepovedlo se spustit aplikaci "/ LM/W3SVC/5/ROOT", '0x8000ffff' kód chyby.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-298">Failed to start application '/LM/W3SVC/5/ROOT', ErrorCode '0x8000ffff'.</span></span>

* <span data-ttu-id="d6fc5-299">**ASP.NET Core modulu stdout protokolu:** Nebylo možné najít žádné architektura kompatibilní verzi.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-299">**ASP.NET Core Module stdout Log:** It was not possible to find any compatible framework version.</span></span> <span data-ttu-id="d6fc5-300">Zadané rozhraní "Microsoft.AspNetCore.App", verze {VERSION} nebyl nalezen.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-300">The specified framework 'Microsoft.AspNetCore.App', version '{VERSION}' was not found.</span></span>

* <span data-ttu-id="d6fc5-301">**Modul ASP.NET Core protokol ladění:** Vrátí selhání HRESULT: 0x8000ffff</span><span class="sxs-lookup"><span data-stu-id="d6fc5-301">**ASP.NET Core Module Debug Log:** Failed HRESULT returned: 0x8000ffff</span></span>

::: moniker-end

<span data-ttu-id="d6fc5-302">Řešení potíží:</span><span class="sxs-lookup"><span data-stu-id="d6fc5-302">Troubleshooting:</span></span>

<span data-ttu-id="d6fc5-303">Nasazení závisí na architektuře (chyba) potvrďte, že v systému nainstalované správné modulu runtime.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-303">For a framework-dependent deployment (FDD), confirm that the correct runtime installed on the system.</span></span>

## <a name="stopped-application-pool"></a><span data-ttu-id="d6fc5-304">Zastavený fond aplikací</span><span class="sxs-lookup"><span data-stu-id="d6fc5-304">Stopped Application Pool</span></span>

* <span data-ttu-id="d6fc5-305">**Prohlížeč:** 503 – nedostupná služba</span><span class="sxs-lookup"><span data-stu-id="d6fc5-305">**Browser:** 503 Service Unavailable</span></span>

* <span data-ttu-id="d6fc5-306">**Protokol aplikace:** Žádná položka</span><span class="sxs-lookup"><span data-stu-id="d6fc5-306">**Application Log:** No entry</span></span>

* <span data-ttu-id="d6fc5-307">**ASP.NET Core modulu stdout protokolu:** Soubor protokolu se vytvoří.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-307">**ASP.NET Core Module stdout Log:** The log file isn't created.</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="d6fc5-308">**Modul ASP.NET Core protokol ladění:** Soubor protokolu se vytvoří.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-308">**ASP.NET Core Module Debug Log:** The log file isn't created.</span></span>

::: moniker-end

<span data-ttu-id="d6fc5-309">Řešení potíží:</span><span class="sxs-lookup"><span data-stu-id="d6fc5-309">Troubleshooting:</span></span>

<span data-ttu-id="d6fc5-310">Potvrďte, že se fond aplikací není v *Zastaveno* stavu.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-310">Confirm that the Application Pool isn't in the *Stopped* state.</span></span>

## <a name="sub-application-includes-a-handlers-section"></a><span data-ttu-id="d6fc5-311">Zahrnuje dílčí aplikace \<obslužné rutiny > části</span><span class="sxs-lookup"><span data-stu-id="d6fc5-311">Sub-application includes a \<handlers> section</span></span>

* <span data-ttu-id="d6fc5-312">**Prohlížeč:** Chyba protokolu HTTP 500.19 – vnitřní chyba serveru</span><span class="sxs-lookup"><span data-stu-id="d6fc5-312">**Browser:** HTTP Error 500.19 - Internal Server Error</span></span>

* <span data-ttu-id="d6fc5-313">**Protokol aplikace:** Žádná položka</span><span class="sxs-lookup"><span data-stu-id="d6fc5-313">**Application Log:** No entry</span></span>

* <span data-ttu-id="d6fc5-314">**ASP.NET Core modulu stdout protokolu:** Aplikace kořenový soubor protokolu se vytvoří a zobrazí normálního provozu.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-314">**ASP.NET Core Module stdout Log:** The root app's log file is created and shows normal operation.</span></span> <span data-ttu-id="d6fc5-315">Soubor protokolu sub – aplikace se nevytvoří.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-315">The sub-app's log file isn't created.</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="d6fc5-316">**Modul ASP.NET Core protokol ladění:** Aplikace kořenový soubor protokolu se vytvoří a zobrazí normálního provozu.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-316">**ASP.NET Core Module Debug Log:** The root app's log file is created and shows normal operation.</span></span> <span data-ttu-id="d6fc5-317">Soubor protokolu sub – aplikace se nevytvoří.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-317">The sub-app's log file isn't created.</span></span>

::: moniker-end

<span data-ttu-id="d6fc5-318">Řešení potíží:</span><span class="sxs-lookup"><span data-stu-id="d6fc5-318">Troubleshooting:</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="d6fc5-319">Ujistěte se, že sub aplikace *web.config* soubor neobsahuje `<handlers>` části nebo že podřízeným aplikacím nedědí obslužné rutiny nadřazená aplikace.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-319">Confirm that the sub-app's *web.config* file doesn't include a `<handlers>` section or that the sub-app doesn't inherit the parent app's handlers.</span></span>

<span data-ttu-id="d6fc5-320">Nadřazená aplikace `<system.webServer>` část *web.config* je umístěn uvnitř `<location>` elementu.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-320">The parent app's `<system.webServer>` section of *web.config* is placed inside of a `<location>` element.</span></span> <span data-ttu-id="d6fc5-321"><xref:System.Configuration.SectionInformation.InheritInChildApplications*> Je nastavena na `false` k označení, že nastavení zadané v rámci [ \<umístění >](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) element nedědí od neznámých aplikací, které jsou umístěny v podadresáři nadřazená aplikace.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-321">The <xref:System.Configuration.SectionInformation.InheritInChildApplications*> property is set to `false` to indicate that the settings specified within the [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) element aren't inherited by apps that reside in a subdirectory of the parent app.</span></span> <span data-ttu-id="d6fc5-322">Další informace naleznete v tématu <xref:host-and-deploy/aspnet-core-module>.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-322">For more information, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="d6fc5-323">Ujistěte se, že sub aplikace *web.config* soubor neobsahuje `<handlers>` oddílu.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-323">Confirm that the sub-app's *web.config* file doesn't include a `<handlers>` section.</span></span>

::: moniker-end

## <a name="stdout-log-path-incorrect"></a><span data-ttu-id="d6fc5-324">Nesprávná cesta protokolu STDOUT</span><span class="sxs-lookup"><span data-stu-id="d6fc5-324">stdout log path incorrect</span></span>

* <span data-ttu-id="d6fc5-325">**Prohlížeč:** Obvykle reakce aplikace.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-325">**Browser:** The app responds normally.</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="d6fc5-326">**Protokol aplikace:** Přesměrování stdout do C:\Program Files\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll nelze spustit.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-326">**Application Log:** Could not start stdout redirection in C:\Program Files\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll.</span></span> <span data-ttu-id="d6fc5-327">Zpráva o výjimce: HRESULT 0x80070005 vrácené na {PATH}\aspnetcoremodulev2\commonlib\fileoutputmanager.cpp:84.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-327">Exception message: HRESULT 0x80070005 returned at {PATH}\aspnetcoremodulev2\commonlib\fileoutputmanager.cpp:84.</span></span> <span data-ttu-id="d6fc5-328">Přesměrování stdout do C:\Program Files\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll nelze zastavit.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-328">Could not stop stdout redirection in C:\Program Files\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll.</span></span> <span data-ttu-id="d6fc5-329">Zpráva o výjimce: Hodnota HRESULT 0x80070002 vrátil v umístění {cesta}.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-329">Exception message: HRESULT 0x80070002 returned at {PATH}.</span></span> <span data-ttu-id="d6fc5-330">Nebylo možné spustit stdout přesměrování {PATH}\aspnetcorev2_inprocess.dll.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-330">Could not start stdout redirection in {PATH}\aspnetcorev2_inprocess.dll.</span></span>

* <span data-ttu-id="d6fc5-331">**ASP.NET Core modulu stdout protokolu:** Soubor protokolu se vytvoří.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-331">**ASP.NET Core Module stdout Log:** The log file isn't created.</span></span>

* <span data-ttu-id="d6fc5-332">**Modul ASP.NET Core protokol ladění:** Přesměrování stdout do C:\Program Files\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll nelze spustit.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-332">**ASP.NET Core Module debug Log:** Could not start stdout redirection in C:\Program Files\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll.</span></span> <span data-ttu-id="d6fc5-333">Zpráva o výjimce: HRESULT 0x80070005 vrácené na {PATH}\aspnetcoremodulev2\commonlib\fileoutputmanager.cpp:84.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-333">Exception message: HRESULT 0x80070005 returned at {PATH}\aspnetcoremodulev2\commonlib\fileoutputmanager.cpp:84.</span></span> <span data-ttu-id="d6fc5-334">Přesměrování stdout do C:\Program Files\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll nelze zastavit.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-334">Could not stop stdout redirection in C:\Program Files\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll.</span></span> <span data-ttu-id="d6fc5-335">Zpráva o výjimce: Hodnota HRESULT 0x80070002 vrátil v umístění {cesta}.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-335">Exception message: HRESULT 0x80070002 returned at {PATH}.</span></span> <span data-ttu-id="d6fc5-336">Nebylo možné spustit stdout přesměrování {PATH}\aspnetcorev2_inprocess.dll.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-336">Could not start stdout redirection in {PATH}\aspnetcorev2_inprocess.dll.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* <span data-ttu-id="d6fc5-337">**Protokol aplikace:** Upozornění: Nepovedlo se vytvořit stdoutLogFile \\?\{ Cesta} \path_doesnt_exist\stdout_ {ID procesu} _ {časové razítko} .log, kód chyby =-2147024893.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-337">**Application Log:** Warning: Could not create stdoutLogFile \\?\{PATH}\path_doesnt_exist\stdout_{PROCESS ID}_{TIMESTAMP}.log, ErrorCode = -2147024893.</span></span>

* <span data-ttu-id="d6fc5-338">**ASP.NET Core modulu stdout protokolu:** Soubor protokolu se vytvoří.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-338">**ASP.NET Core Module stdout Log:** The log file isn't created.</span></span>

::: moniker-end

<span data-ttu-id="d6fc5-339">Řešení potíží:</span><span class="sxs-lookup"><span data-stu-id="d6fc5-339">Troubleshooting:</span></span>

* <span data-ttu-id="d6fc5-340">`stdoutLogFile` Cestě zadané v `<aspNetCore>` prvek *web.config* neexistuje.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-340">The `stdoutLogFile` path specified in the `<aspNetCore>` element of *web.config* doesn't exist.</span></span> <span data-ttu-id="d6fc5-341">Další informace najdete v tématu [modul ASP.NET Core: Vytvoření a přesměrování protokolu](xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection).</span><span class="sxs-lookup"><span data-stu-id="d6fc5-341">For more information, see [ASP.NET Core Module: Log creation and redirection](xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection).</span></span>

* <span data-ttu-id="d6fc5-342">Uživatele fondu aplikací nemá oprávnění k zápisu do stdout cesta k protokolu.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-342">The app pool user doesn't have write access to the stdout log path.</span></span>

## <a name="application-configuration-general-issue"></a><span data-ttu-id="d6fc5-343">Hlavní problém s konfigurací aplikace</span><span class="sxs-lookup"><span data-stu-id="d6fc5-343">Application configuration general issue</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="d6fc5-344">**Prohlížeč:** Chyba protokolu HTTP 500.0 - ANCM v procesu obslužná rutina chyby načítání **--nebo--** Chyba protokolu HTTP 500.30 - ANCM selhání spuštění v procesu</span><span class="sxs-lookup"><span data-stu-id="d6fc5-344">**Browser:** HTTP Error 500.0 - ANCM In-Process Handler Load Failure **--OR--** HTTP Error 500.30 - ANCM In-Process Start Failure</span></span>

* <span data-ttu-id="d6fc5-345">**Protokol aplikace:** Proměnná</span><span class="sxs-lookup"><span data-stu-id="d6fc5-345">**Application Log:** Variable</span></span>

* <span data-ttu-id="d6fc5-346">**ASP.NET Core modulu stdout protokolu:** Soubor protokolu je vytvořený, ale prázdný nebo vytvořena s normální položky až do bodu selhání aplikace.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-346">**ASP.NET Core Module stdout Log:** The log file is created but empty or created with normal entries until the point of the app failing.</span></span>

* <span data-ttu-id="d6fc5-347">**Modul ASP.NET Core protokol ladění:** Proměnná</span><span class="sxs-lookup"><span data-stu-id="d6fc5-347">**ASP.NET Core Module Debug Log:** Variable</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* <span data-ttu-id="d6fc5-348">**Prohlížeč:** Chyba protokolu HTTP 502.5 – selhání procesu</span><span class="sxs-lookup"><span data-stu-id="d6fc5-348">**Browser:** HTTP Error 502.5 - Process Failure</span></span>

* <span data-ttu-id="d6fc5-349">**Protokol aplikace:** Aplikace "MACHINE/WEBROOT nebo APPHOST / {sestavení}' s kořenem fyzické ' C:\{CESTU}\' procesu vytvořené pomocí příkazového řádku" "C:\{CESTU}\{sestavení}. { soubor EXE | knihovny dll} "" ale došlo k chybě nebo neodpověděl nebo není naslouchat na daný port: {PORT}', kód chyby = {Chyba CODE}</span><span class="sxs-lookup"><span data-stu-id="d6fc5-349">**Application Log:** Application 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' with physical root 'C:\{PATH}\' created process with commandline '"C:\{PATH}\{ASSEMBLY}.{exe|dll}" ' but either crashed or did not respond or did not listen on the given port '{PORT}', ErrorCode = '{ERROR CODE}'</span></span>

* <span data-ttu-id="d6fc5-350">**ASP.NET Core modulu stdout protokolu:** Soubor protokolu je vytvořený, ale prázdný.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-350">**ASP.NET Core Module stdout Log:** The log file is created but empty.</span></span>

::: moniker-end

<span data-ttu-id="d6fc5-351">Řešení potíží:</span><span class="sxs-lookup"><span data-stu-id="d6fc5-351">Troubleshooting:</span></span>

<span data-ttu-id="d6fc5-352">Proces se nepodařilo spustit, pravděpodobně kvůli konfiguraci aplikace nebo programovací problém.</span><span class="sxs-lookup"><span data-stu-id="d6fc5-352">The process failed to start, most likely due to an app configuration or programming issue.</span></span>

<span data-ttu-id="d6fc5-353">Další informace naleznete v následujících tématech:</span><span class="sxs-lookup"><span data-stu-id="d6fc5-353">For more information, see the following topics:</span></span>

* <xref:host-and-deploy/iis/troubleshoot>
* <xref:host-and-deploy/azure-apps/troubleshoot>
* <xref:test/troubleshoot>
