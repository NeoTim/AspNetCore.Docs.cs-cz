---
title: Referenční informace o běžných chybách pro Azure App Service a IIS s ASP.NET Core
author: guardrex
description: Získejte Rady pro řešení běžných chyb při hostování ASP.NET Corech aplikací ve službě Azure Apps a službě IIS.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/11/2019
uid: host-and-deploy/azure-iis-errors-reference
ms.openlocfilehash: 047ef23bd2f4d349d2d342d17764c7edd3e0de4a
ms.sourcegitcommit: 4649814d1ae32248419da4e8f8242850fd8679a5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2019
ms.locfileid: "71975679"
---
# <a name="common-errors-reference-for-azure-app-service-and-iis-with-aspnet-core"></a><span data-ttu-id="ca36d-103">Referenční informace o běžných chybách pro Azure App Service a IIS s ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ca36d-103">Common errors reference for Azure App Service and IIS with ASP.NET Core</span></span>

<span data-ttu-id="ca36d-104">Podle [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="ca36d-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="ca36d-105">Toto téma popisuje běžné chyby a poskytuje rady pro řešení problémů při hostování ASP.NET Core aplikací ve službě Azure Apps a službě IIS.</span><span class="sxs-lookup"><span data-stu-id="ca36d-105">This topic describes common errors and provides troubleshooting advice for specific errors when hosting ASP.NET Core apps on Azure Apps Service and IIS.</span></span>

<span data-ttu-id="ca36d-106">Obecné pokyny k odstraňování potíží najdete v tématu @no__t – 0.</span><span class="sxs-lookup"><span data-stu-id="ca36d-106">For general troubleshooting guidance, see <xref:test/troubleshoot-azure-iis>.</span></span>

<span data-ttu-id="ca36d-107">Shromážděte následující informace:</span><span class="sxs-lookup"><span data-stu-id="ca36d-107">Collect the following information:</span></span>

* <span data-ttu-id="ca36d-108">Chování prohlížeče (Stavový kód a chybová zpráva)</span><span class="sxs-lookup"><span data-stu-id="ca36d-108">Browser behavior (status code and error message)</span></span>
* <span data-ttu-id="ca36d-109">Položky protokolu událostí aplikace</span><span class="sxs-lookup"><span data-stu-id="ca36d-109">Application Event Log entries</span></span>
  * <span data-ttu-id="ca36d-110">Azure App Service &ndash; viz <xref:test/troubleshoot-azure-iis>.</span><span class="sxs-lookup"><span data-stu-id="ca36d-110">Azure App Service &ndash; See <xref:test/troubleshoot-azure-iis>.</span></span>
  * <span data-ttu-id="ca36d-111">IIS</span><span class="sxs-lookup"><span data-stu-id="ca36d-111">IIS</span></span>
    1. <span data-ttu-id="ca36d-112">V nabídce **systému Windows** vyberte **Start** , zadejte příkaz *Prohlížeč událostí*a stiskněte klávesu **ENTER**.</span><span class="sxs-lookup"><span data-stu-id="ca36d-112">Select **Start** on the **Windows** menu, type *Event Viewer*, and press **Enter**.</span></span>
    1. <span data-ttu-id="ca36d-113">Po otevření **Prohlížeč událostí** rozbalte na postranním panelu položku **protokoly Windows** **aplikace**  > .</span><span class="sxs-lookup"><span data-stu-id="ca36d-113">After the **Event Viewer** opens, expand **Windows Logs** > **Application** in the sidebar.</span></span>
* <span data-ttu-id="ca36d-114">ASP.NET Core modul stdout a položky protokolu ladění</span><span class="sxs-lookup"><span data-stu-id="ca36d-114">ASP.NET Core Module stdout and debug log entries</span></span>
  * <span data-ttu-id="ca36d-115">Azure App Service &ndash; viz <xref:test/troubleshoot-azure-iis>.</span><span class="sxs-lookup"><span data-stu-id="ca36d-115">Azure App Service &ndash; See <xref:test/troubleshoot-azure-iis>.</span></span>
  * <span data-ttu-id="ca36d-116">Služba IIS &ndash; postupujte podle pokynů v částech [Vytvoření protokolu a přesměrování](xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection) a [Rozšířené diagnostické protokoly](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) v tématu ASP.NET Core modulu.</span><span class="sxs-lookup"><span data-stu-id="ca36d-116">IIS &ndash; Follow the instructions in the [Log creation and redirection](xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection) and [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) sections of the ASP.NET Core Module topic.</span></span>

<span data-ttu-id="ca36d-117">Porovnejte informace o chybě s následujícími běžnými chybami.</span><span class="sxs-lookup"><span data-stu-id="ca36d-117">Compare error information to the following common errors.</span></span> <span data-ttu-id="ca36d-118">Pokud se najde shoda, postupujte podle pokynů pro řešení potíží.</span><span class="sxs-lookup"><span data-stu-id="ca36d-118">If a match is found, follow the troubleshooting advice.</span></span>

<span data-ttu-id="ca36d-119">Seznam chyb v tomto tématu není vyčerpávající.</span><span class="sxs-lookup"><span data-stu-id="ca36d-119">The list of errors in this topic isn't exhaustive.</span></span> <span data-ttu-id="ca36d-120">Pokud narazíte na chybu, která zde není uvedená, otevřete nový problém pomocí tlačítka pro **odeslání obsahu** v dolní části tohoto tématu s podrobnými pokyny pro reprodukování chyby.</span><span class="sxs-lookup"><span data-stu-id="ca36d-120">If you encounter an error not listed here, open a new issue using the **Content feedback** button at the bottom of this topic with detailed instructions on how to reproduce the error.</span></span>

[!INCLUDE[Azure App Service Preview Notice](../includes/azure-apps-preview-notice.md)]

## <a name="os-upgrade-removed-the-32-bit-aspnet-core-module"></a><span data-ttu-id="ca36d-121">Upgrade operačního systému odebral modul 32 ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ca36d-121">OS upgrade removed the 32-bit ASP.NET Core Module</span></span>

<span data-ttu-id="ca36d-122">**Protokol aplikace:** Načtení knihovny DLL modulu **C:\WINDOWS\system32\inetsrv\aspnetcore.dll** se nezdařilo.</span><span class="sxs-lookup"><span data-stu-id="ca36d-122">**Application Log:** The Module DLL **C:\WINDOWS\system32\inetsrv\aspnetcore.dll** failed to load.</span></span> <span data-ttu-id="ca36d-123">Tato data jsou chybná.</span><span class="sxs-lookup"><span data-stu-id="ca36d-123">The data is the error.</span></span>

<span data-ttu-id="ca36d-124">Řešení potíží:</span><span class="sxs-lookup"><span data-stu-id="ca36d-124">Troubleshooting:</span></span>

<span data-ttu-id="ca36d-125">Soubory jiného typu než operační systém v adresáři **C:\Windows\SysWOW64\inetsrv** nejsou zachovány během upgradu operačního systému.</span><span class="sxs-lookup"><span data-stu-id="ca36d-125">Non-OS files in the **C:\Windows\SysWOW64\inetsrv** directory aren't preserved during an OS upgrade.</span></span> <span data-ttu-id="ca36d-126">Pokud je modul ASP.NET Core nainstalován před upgradem operačního systému a potom po upgradu operačního systému běží libovolný fond aplikací v 32ovém režimu, dojde k tomuto problému.</span><span class="sxs-lookup"><span data-stu-id="ca36d-126">If the ASP.NET Core Module is installed prior to an OS upgrade and then any app pool is run in 32-bit mode after an OS upgrade, this issue is encountered.</span></span> <span data-ttu-id="ca36d-127">Po upgradu operačního systému opravte modul ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ca36d-127">After an OS upgrade, repair the ASP.NET Core Module.</span></span> <span data-ttu-id="ca36d-128">Viz [instalace hostující sady .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span><span class="sxs-lookup"><span data-stu-id="ca36d-128">See [Install the .NET Core Hosting bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span> <span data-ttu-id="ca36d-129">Po spuštění instalačního programu vyberte možnost **opravit** .</span><span class="sxs-lookup"><span data-stu-id="ca36d-129">Select **Repair** when the installer is run.</span></span>

## <a name="missing-site-extension-32-bit-x86-and-64-bit-x64-site-extensions-installed-or-wrong-process-bitness-set"></a><span data-ttu-id="ca36d-130">Chybí rozšíření webového serveru, 32 (x86) a 64-bit (x64) rozšíření webu nebo nesprávná bitová verze sada procesů.</span><span class="sxs-lookup"><span data-stu-id="ca36d-130">Missing site extension, 32-bit (x86) and 64-bit (x64) site extensions installed, or wrong process bitness set</span></span>

<span data-ttu-id="ca36d-131">*Platí pro aplikace hostované v Azure App Services.*</span><span class="sxs-lookup"><span data-stu-id="ca36d-131">*Applies to apps hosted by Azure App Services.*</span></span>

* <span data-ttu-id="ca36d-132">**Prohlížeee** Chyba protokolu HTTP 500,0 – selhání načtení obslužné rutiny ANCM v procesu</span><span class="sxs-lookup"><span data-stu-id="ca36d-132">**Browser:** HTTP Error 500.0 - ANCM In-Process Handler Load Failure</span></span>

* <span data-ttu-id="ca36d-133">**Protokol aplikace:** Vyvolání hostfxr k nalezení obslužné rutiny žádosti o zpracování se nezdařilo bez nalezení nativních závislostí.</span><span class="sxs-lookup"><span data-stu-id="ca36d-133">**Application Log:** Invoking hostfxr to find the inprocess request handler failed without finding any native dependencies.</span></span> <span data-ttu-id="ca36d-134">Nepovedlo se najít obslužnou rutinu žádosti o zpracování.</span><span class="sxs-lookup"><span data-stu-id="ca36d-134">Could not find inprocess request handler.</span></span> <span data-ttu-id="ca36d-135">Zachycený výstup z vyvolání hostfxr: Nebylo možné najít žádnou kompatibilní verzi rozhraní.</span><span class="sxs-lookup"><span data-stu-id="ca36d-135">Captured output from invoking hostfxr: It was not possible to find any compatible framework version.</span></span> <span data-ttu-id="ca36d-136">Zadané rozhraní Microsoft. AspNetCore. app, verze {VERSION}-Preview-\* nebylo nalezeno.</span><span class="sxs-lookup"><span data-stu-id="ca36d-136">The specified framework 'Microsoft.AspNetCore.App', version '{VERSION}-preview-\*' was not found.</span></span> <span data-ttu-id="ca36d-137">Nepodařilo se spustit aplikaci '/LM/W3SVC/1416782824/ROOT ', ErrorCode ' 0x8000FFFF '.</span><span class="sxs-lookup"><span data-stu-id="ca36d-137">Failed to start application '/LM/W3SVC/1416782824/ROOT', ErrorCode '0x8000ffff'.</span></span>

* <span data-ttu-id="ca36d-138">**Protokol stdout modulu ASP.NET Core:** Nebylo možné najít žádnou kompatibilní verzi rozhraní.</span><span class="sxs-lookup"><span data-stu-id="ca36d-138">**ASP.NET Core Module stdout Log:** It was not possible to find any compatible framework version.</span></span> <span data-ttu-id="ca36d-139">Zadané rozhraní Microsoft. AspNetCore. app, verze {VERSION}-Preview-\* nebylo nalezeno.</span><span class="sxs-lookup"><span data-stu-id="ca36d-139">The specified framework 'Microsoft.AspNetCore.App', version '{VERSION}-preview-\*' was not found.</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="ca36d-140">**Protokol ladění ASP.NET Coreho modulu:** Vyvolání hostfxr k nalezení obslužné rutiny žádosti o zpracování se nezdařilo bez nalezení nativních závislostí.</span><span class="sxs-lookup"><span data-stu-id="ca36d-140">**ASP.NET Core Module Debug Log:** Invoking hostfxr to find the inprocess request handler failed without finding any native dependencies.</span></span> <span data-ttu-id="ca36d-141">To pravděpodobně znamená, že aplikace je nesprávně nakonfigurovaná. Zkontrolujte prosím verze Microsoft. NetCore. app a Microsoft. AspNetCore. app, na které cílí aplikace a které jsou nainstalované v počítači.</span><span class="sxs-lookup"><span data-stu-id="ca36d-141">This most likely means the app is misconfigured, please check the versions of Microsoft.NetCore.App and Microsoft.AspNetCore.App that are targeted by the application and are installed on the machine.</span></span> <span data-ttu-id="ca36d-142">Vrátila se neúspěšná hodnota HRESULT: 0x8000ffff.</span><span class="sxs-lookup"><span data-stu-id="ca36d-142">Failed HRESULT returned: 0x8000ffff.</span></span> <span data-ttu-id="ca36d-143">Nepovedlo se najít obslužnou rutinu žádosti o zpracování.</span><span class="sxs-lookup"><span data-stu-id="ca36d-143">Could not find inprocess request handler.</span></span> <span data-ttu-id="ca36d-144">Nebylo možné najít žádnou kompatibilní verzi rozhraní.</span><span class="sxs-lookup"><span data-stu-id="ca36d-144">It was not possible to find any compatible framework version.</span></span> <span data-ttu-id="ca36d-145">Zadané rozhraní Microsoft. AspNetCore. app, verze {VERSION}-Preview-\* nebylo nalezeno.</span><span class="sxs-lookup"><span data-stu-id="ca36d-145">The specified framework 'Microsoft.AspNetCore.App', version '{VERSION}-preview-\*' was not found.</span></span>

::: moniker-end

<span data-ttu-id="ca36d-146">Řešení potíží:</span><span class="sxs-lookup"><span data-stu-id="ca36d-146">Troubleshooting:</span></span>

* <span data-ttu-id="ca36d-147">Pokud spouštíte aplikaci ve verzi Preview, nainstalujte buď rozšíření 32 (x86) **nebo** 64-bit (x64), které odpovídá bitová verze aplikace a verzi modulu runtime aplikace.</span><span class="sxs-lookup"><span data-stu-id="ca36d-147">If running the app on a preview runtime, install either the 32-bit (x86) **or** 64-bit (x64) site extension that matches the bitness of the app and the app's runtime version.</span></span> <span data-ttu-id="ca36d-148">**Neinstalujte obě rozšíření nebo více verzí modulu runtime rozšíření.**</span><span class="sxs-lookup"><span data-stu-id="ca36d-148">**Don't install both extensions or multiple runtime versions of the extension.**</span></span>

  * <span data-ttu-id="ca36d-149">Modul runtime verze ASP.NET Core {RUNTIME} (x86)</span><span class="sxs-lookup"><span data-stu-id="ca36d-149">ASP.NET Core {RUNTIME VERSION} (x86) Runtime</span></span>
  * <span data-ttu-id="ca36d-150">Modul runtime verze ASP.NET Core {RUNTIME} (x64)</span><span class="sxs-lookup"><span data-stu-id="ca36d-150">ASP.NET Core {RUNTIME VERSION} (x64) Runtime</span></span>

  <span data-ttu-id="ca36d-151">Restartujte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="ca36d-151">Restart the app.</span></span> <span data-ttu-id="ca36d-152">Počkejte několik sekund, než se aplikace restartuje.</span><span class="sxs-lookup"><span data-stu-id="ca36d-152">Wait several seconds for the app to restart.</span></span>

* <span data-ttu-id="ca36d-153">Pokud je aplikace spuštěná ve verzi Preview a nainstalují se [rozšíření lokality](xref:host-and-deploy/azure-apps/index#install-the-preview-site-extension) 32 (x86) i 64 (x64), odinstalujte rozšíření webu, které se neshoduje s bitová verze aplikace.</span><span class="sxs-lookup"><span data-stu-id="ca36d-153">If running the app on a preview runtime and both the 32-bit (x86) and 64-bit (x64) [site extensions](xref:host-and-deploy/azure-apps/index#install-the-preview-site-extension) are installed, uninstall the site extension that doesn't match the bitness of the app.</span></span> <span data-ttu-id="ca36d-154">Po odebrání rozšíření lokality aplikaci restartujte.</span><span class="sxs-lookup"><span data-stu-id="ca36d-154">After removing the site extension, restart the app.</span></span> <span data-ttu-id="ca36d-155">Počkejte několik sekund, než se aplikace restartuje.</span><span class="sxs-lookup"><span data-stu-id="ca36d-155">Wait several seconds for the app to restart.</span></span>

* <span data-ttu-id="ca36d-156">Pokud je aplikace spuštěná ve verzi Preview a bitová verze rozšíření lokality odpovídá této aplikaci, zkontrolujte, že *verze modulu runtime* rozšíření lokality Preview odpovídá verzi modulu runtime aplikace.</span><span class="sxs-lookup"><span data-stu-id="ca36d-156">If running the app on a preview runtime and the site extension's bitness matches that of the app, confirm that the preview site extension's *runtime version* matches the app's runtime version.</span></span>

* <span data-ttu-id="ca36d-157">Ověřte, že **platforma** aplikace v **nastavení aplikace** odpovídá bitová verze aplikace.</span><span class="sxs-lookup"><span data-stu-id="ca36d-157">Confirm that the app's **Platform** in **Application Settings** matches the bitness of the app.</span></span>

<span data-ttu-id="ca36d-158">Další informace naleznete v tématu <xref:host-and-deploy/azure-apps/index#install-the-preview-site-extension>.</span><span class="sxs-lookup"><span data-stu-id="ca36d-158">For more information, see <xref:host-and-deploy/azure-apps/index#install-the-preview-site-extension>.</span></span>

## <a name="an-x86-app-is-deployed-but-the-app-pool-isnt-enabled-for-32-bit-apps"></a><span data-ttu-id="ca36d-159">Je nasazená aplikace x86, ale fond aplikací není povolený pro 32 aplikace.</span><span class="sxs-lookup"><span data-stu-id="ca36d-159">An x86 app is deployed but the app pool isn't enabled for 32-bit apps</span></span>

* <span data-ttu-id="ca36d-160">**Prohlížeee** Chyba protokolu HTTP 500,30 – Chyba spuštění ANCM v procesu</span><span class="sxs-lookup"><span data-stu-id="ca36d-160">**Browser:** HTTP Error 500.30 - ANCM In-Process Start Failure</span></span>

* <span data-ttu-id="ca36d-161">**Protokol aplikace:** U aplikace "/LM/W3SVC/5/ROOT" s fyzickým kořenovým adresářem {PATH} se dosáhlo neočekávané spravované výjimky, kód výjimky = "0xe0434352".</span><span class="sxs-lookup"><span data-stu-id="ca36d-161">**Application Log:** Application '/LM/W3SVC/5/ROOT' with physical root '{PATH}' hit unexpected managed exception, exception code = '0xe0434352'.</span></span> <span data-ttu-id="ca36d-162">Další informace najdete v protokolech stderr.</span><span class="sxs-lookup"><span data-stu-id="ca36d-162">Please check the stderr logs for more information.</span></span> <span data-ttu-id="ca36d-163">Aplikaci '/LM/W3SVC/5/ROOT ' s fyzickým kořenem ' {PATH} ' se nepodařilo načíst modul CLR a spravovanou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="ca36d-163">Application '/LM/W3SVC/5/ROOT' with physical root '{PATH}' failed to load clr and managed application.</span></span> <span data-ttu-id="ca36d-164">Pracovní vlákno CLR byl předčasně ukončeno.</span><span class="sxs-lookup"><span data-stu-id="ca36d-164">CLR worker thread exited prematurely</span></span>

* <span data-ttu-id="ca36d-165">**Protokol stdout modulu ASP.NET Core:** Soubor protokolu je vytvořen, ale je prázdný.</span><span class="sxs-lookup"><span data-stu-id="ca36d-165">**ASP.NET Core Module stdout Log:** The log file is created but empty.</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="ca36d-166">**Protokol ladění ASP.NET Coreho modulu:** Vrátila se neúspěšná hodnota HRESULT: 0x8007023e</span><span class="sxs-lookup"><span data-stu-id="ca36d-166">**ASP.NET Core Module Debug Log:** Failed HRESULT returned: 0x8007023e</span></span>

::: moniker-end

<span data-ttu-id="ca36d-167">Tento scénář je při publikování samostatně obsažené aplikace zachycen sadou SDK.</span><span class="sxs-lookup"><span data-stu-id="ca36d-167">This scenario is trapped by the SDK when publishing a self-contained app.</span></span> <span data-ttu-id="ca36d-168">Sada SDK vytvoří chybu, pokud identifikátor RID neodpovídá cíli platformy (například `win10-x64` RID s `<PlatformTarget>x86</PlatformTarget>` v souboru projektu).</span><span class="sxs-lookup"><span data-stu-id="ca36d-168">The SDK produces an error if the RID doesn't match the platform target (for example, `win10-x64` RID with `<PlatformTarget>x86</PlatformTarget>` in the project file).</span></span>

<span data-ttu-id="ca36d-169">Řešení potíží:</span><span class="sxs-lookup"><span data-stu-id="ca36d-169">Troubleshooting:</span></span>

<span data-ttu-id="ca36d-170">Pro nasazení závislé na architektuře x86 (`<PlatformTarget>x86</PlatformTarget>`) Povolte fond aplikací služby IIS pro 32 aplikace.</span><span class="sxs-lookup"><span data-stu-id="ca36d-170">For an x86 framework-dependent deployment (`<PlatformTarget>x86</PlatformTarget>`), enable the IIS app pool for 32-bit apps.</span></span> <span data-ttu-id="ca36d-171">Ve Správci služby IIS otevřete **Rozšířené nastavení** fondu aplikací a nastavte **možnost Povolit 32-bitové aplikace** na **hodnotu true**.</span><span class="sxs-lookup"><span data-stu-id="ca36d-171">In IIS Manager, open the app pool's **Advanced Settings** and set **Enable 32-Bit Applications** to **True**.</span></span>

## <a name="platform-conflicts-with-rid"></a><span data-ttu-id="ca36d-172">Konflikty platformy s identifikátorem RID</span><span class="sxs-lookup"><span data-stu-id="ca36d-172">Platform conflicts with RID</span></span>

* <span data-ttu-id="ca36d-173">**Prohlížeee** Chyba protokolu HTTP 502,5 – chyba procesu</span><span class="sxs-lookup"><span data-stu-id="ca36d-173">**Browser:** HTTP Error 502.5 - Process Failure</span></span>

* <span data-ttu-id="ca36d-174">**Protokol aplikace:** Aplikaci ' MACHINE/WEBROOT/APPHOST/{ASSEMBLY} ' s fyzickým kořenem ' C: \{PATH} \' se nepodařilo spustit proces pomocí příkazu CommandLine ' "C: \{PATH} {ASSEMBLY}. {exe | dll} "', ErrorCode = ' 0x80004005: FF.</span><span class="sxs-lookup"><span data-stu-id="ca36d-174">**Application Log:** Application 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' with physical root 'C:\{PATH}\' failed to start process with commandline '"C:\{PATH}{ASSEMBLY}.{exe|dll}" ', ErrorCode = '0x80004005 : ff.</span></span>

* <span data-ttu-id="ca36d-175">**Protokol stdout modulu ASP.NET Core:** Neošetřená výjimka: System.BadImageFormatException: Nepovedlo se načíst soubor nebo sestavení {ASSEMBLY}. dll.</span><span class="sxs-lookup"><span data-stu-id="ca36d-175">**ASP.NET Core Module stdout Log:** Unhandled Exception: System.BadImageFormatException: Could not load file or assembly '{ASSEMBLY}.dll'.</span></span> <span data-ttu-id="ca36d-176">Byl proveden pokus o načtení programu v nesprávném formátu.</span><span class="sxs-lookup"><span data-stu-id="ca36d-176">An attempt was made to load a program with an incorrect format.</span></span>

<span data-ttu-id="ca36d-177">Řešení potíží:</span><span class="sxs-lookup"><span data-stu-id="ca36d-177">Troubleshooting:</span></span>

* <span data-ttu-id="ca36d-178">Ověřte, že se aplikace spouští místně na Kestrel.</span><span class="sxs-lookup"><span data-stu-id="ca36d-178">Confirm that the app runs locally on Kestrel.</span></span> <span data-ttu-id="ca36d-179">Selhání procesu může být výsledkem problému v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="ca36d-179">A process failure might be the result of a problem within the app.</span></span> <span data-ttu-id="ca36d-180">Další informace naleznete v tématu <xref:test/troubleshoot-azure-iis>.</span><span class="sxs-lookup"><span data-stu-id="ca36d-180">For more information, see <xref:test/troubleshoot-azure-iis>.</span></span>

* <span data-ttu-id="ca36d-181">Pokud k této výjimce dojde pro nasazení aplikací Azure při upgradu aplikace a nasazení novějších sestavení, odstraňte ručně všechny soubory z předchozího nasazení.</span><span class="sxs-lookup"><span data-stu-id="ca36d-181">If this exception occurs for an Azure Apps deployment when upgrading an app and deploying newer assemblies, manually delete all files from the prior deployment.</span></span> <span data-ttu-id="ca36d-182">Při zaznamenání nekompatibilních sestavení může při nasazování upgradované aplikace dojít k výjimce `System.BadImageFormatException`.</span><span class="sxs-lookup"><span data-stu-id="ca36d-182">Lingering incompatible assemblies can result in a `System.BadImageFormatException` exception when deploying an upgraded app.</span></span>

## <a name="uri-endpoint-wrong-or-stopped-website"></a><span data-ttu-id="ca36d-183">Koncový bod identifikátoru URI je chybný nebo zastavený Web.</span><span class="sxs-lookup"><span data-stu-id="ca36d-183">URI endpoint wrong or stopped website</span></span>

* <span data-ttu-id="ca36d-184">**Prohlížeee** ERR_CONNECTION_REFUSED **--nebo--** nelze se připojit</span><span class="sxs-lookup"><span data-stu-id="ca36d-184">**Browser:** ERR_CONNECTION_REFUSED **--OR--** Unable to connect</span></span>

* <span data-ttu-id="ca36d-185">**Protokol aplikace:** Žádná položka</span><span class="sxs-lookup"><span data-stu-id="ca36d-185">**Application Log:** No entry</span></span>

* <span data-ttu-id="ca36d-186">**Protokol stdout modulu ASP.NET Core:** Soubor protokolu není vytvořen.</span><span class="sxs-lookup"><span data-stu-id="ca36d-186">**ASP.NET Core Module stdout Log:** The log file isn't created.</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="ca36d-187">**Protokol ladění ASP.NET Coreho modulu:** Soubor protokolu není vytvořen.</span><span class="sxs-lookup"><span data-stu-id="ca36d-187">**ASP.NET Core Module Debug Log:** The log file isn't created.</span></span>

::: moniker-end

<span data-ttu-id="ca36d-188">Řešení potíží:</span><span class="sxs-lookup"><span data-stu-id="ca36d-188">Troubleshooting:</span></span>

* <span data-ttu-id="ca36d-189">Potvrďte, že se pro aplikaci používá správný koncový bod identifikátoru URI.</span><span class="sxs-lookup"><span data-stu-id="ca36d-189">Confirm the correct URI endpoint for the app is in use.</span></span> <span data-ttu-id="ca36d-190">Ověřte vazby.</span><span class="sxs-lookup"><span data-stu-id="ca36d-190">Check the bindings.</span></span>

* <span data-ttu-id="ca36d-191">Ověřte, že web IIS není ve stavu *Zastaveno* .</span><span class="sxs-lookup"><span data-stu-id="ca36d-191">Confirm that the IIS website isn't in the *Stopped* state.</span></span>

## <a name="corewebengine-or-w3svc-server-features-disabled"></a><span data-ttu-id="ca36d-192">Funkce serveru CoreWebEngine nebo W3SVC jsou zakázané.</span><span class="sxs-lookup"><span data-stu-id="ca36d-192">CoreWebEngine or W3SVC server features disabled</span></span>

<span data-ttu-id="ca36d-193">**Výjimka OS:** Aby bylo možné používat modul ASP.NET Core, musí být nainstalovány funkce IIS 7,0 CoreWebEngine a W3SVC.</span><span class="sxs-lookup"><span data-stu-id="ca36d-193">**OS Exception:** The IIS 7.0 CoreWebEngine and W3SVC features must be installed to use the ASP.NET Core Module.</span></span>

<span data-ttu-id="ca36d-194">Řešení potíží:</span><span class="sxs-lookup"><span data-stu-id="ca36d-194">Troubleshooting:</span></span>

<span data-ttu-id="ca36d-195">Zkontrolujte, jestli jsou povolené správné role a funkce.</span><span class="sxs-lookup"><span data-stu-id="ca36d-195">Confirm that the proper role and features are enabled.</span></span> <span data-ttu-id="ca36d-196">Viz [Konfigurace služby IIS](xref:host-and-deploy/iis/index#iis-configuration).</span><span class="sxs-lookup"><span data-stu-id="ca36d-196">See [IIS Configuration](xref:host-and-deploy/iis/index#iis-configuration).</span></span>

## <a name="incorrect-website-physical-path-or-app-missing"></a><span data-ttu-id="ca36d-197">Chybí nesprávná fyzická cesta nebo aplikace na webu.</span><span class="sxs-lookup"><span data-stu-id="ca36d-197">Incorrect website physical path or app missing</span></span>

* <span data-ttu-id="ca36d-198">**Prohlížeee** 403 zakázán-přístup byl odepřen **--nebo--** 403,14 zakázán – webový server je nakonfigurován tak, aby neobsahoval seznam obsahu tohoto adresáře.</span><span class="sxs-lookup"><span data-stu-id="ca36d-198">**Browser:** 403 Forbidden - Access is denied **--OR--** 403.14 Forbidden - The Web server is configured to not list the contents of this directory.</span></span>

* <span data-ttu-id="ca36d-199">**Protokol aplikace:** Žádná položka</span><span class="sxs-lookup"><span data-stu-id="ca36d-199">**Application Log:** No entry</span></span>

* <span data-ttu-id="ca36d-200">**Protokol stdout modulu ASP.NET Core:** Soubor protokolu není vytvořen.</span><span class="sxs-lookup"><span data-stu-id="ca36d-200">**ASP.NET Core Module stdout Log:** The log file isn't created.</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="ca36d-201">**Protokol ladění ASP.NET Coreho modulu:** Soubor protokolu není vytvořen.</span><span class="sxs-lookup"><span data-stu-id="ca36d-201">**ASP.NET Core Module Debug Log:** The log file isn't created.</span></span>

::: moniker-end

<span data-ttu-id="ca36d-202">Řešení potíží:</span><span class="sxs-lookup"><span data-stu-id="ca36d-202">Troubleshooting:</span></span>

<span data-ttu-id="ca36d-203">Ověřte **základní nastavení** webu IIS a složku fyzické aplikace.</span><span class="sxs-lookup"><span data-stu-id="ca36d-203">Check the IIS website **Basic Settings** and the physical app folder.</span></span> <span data-ttu-id="ca36d-204">Ověřte, že je aplikace ve složce na **fyzické cestě**webu IIS.</span><span class="sxs-lookup"><span data-stu-id="ca36d-204">Confirm that the app is in the folder at the IIS website **Physical path**.</span></span>

## <a name="incorrect-role-aspnet-core-module-not-installed-or-incorrect-permissions"></a><span data-ttu-id="ca36d-205">Nesprávná role, ASP.NET Core modul není nainstalovaný nebo má nesprávná oprávnění.</span><span class="sxs-lookup"><span data-stu-id="ca36d-205">Incorrect role, ASP.NET Core Module not installed, or incorrect permissions</span></span>

* <span data-ttu-id="ca36d-206">**Prohlížeee** 500,19 interní chyba serveru – k požadované stránce nelze přistupovat, protože související konfigurační data stránky jsou neplatná.</span><span class="sxs-lookup"><span data-stu-id="ca36d-206">**Browser:** 500.19 Internal Server Error - The requested page cannot be accessed because the related configuration data for the page is invalid.</span></span> <span data-ttu-id="ca36d-207">**--Nebo--** Tuto stránku nejde zobrazit.</span><span class="sxs-lookup"><span data-stu-id="ca36d-207">**--OR--** This page can't be displayed</span></span>

* <span data-ttu-id="ca36d-208">**Protokol aplikace:** Žádná položka</span><span class="sxs-lookup"><span data-stu-id="ca36d-208">**Application Log:** No entry</span></span>

* <span data-ttu-id="ca36d-209">**Protokol stdout modulu ASP.NET Core:** Soubor protokolu není vytvořen.</span><span class="sxs-lookup"><span data-stu-id="ca36d-209">**ASP.NET Core Module stdout Log:** The log file isn't created.</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="ca36d-210">**Protokol ladění ASP.NET Coreho modulu:** Soubor protokolu není vytvořen.</span><span class="sxs-lookup"><span data-stu-id="ca36d-210">**ASP.NET Core Module Debug Log:** The log file isn't created.</span></span>

::: moniker-end

<span data-ttu-id="ca36d-211">Řešení potíží:</span><span class="sxs-lookup"><span data-stu-id="ca36d-211">Troubleshooting:</span></span>

* <span data-ttu-id="ca36d-212">Potvrďte, že je povolená správná role.</span><span class="sxs-lookup"><span data-stu-id="ca36d-212">Confirm that the proper role is enabled.</span></span> <span data-ttu-id="ca36d-213">Viz [Konfigurace služby IIS](xref:host-and-deploy/iis/index#iis-configuration).</span><span class="sxs-lookup"><span data-stu-id="ca36d-213">See [IIS Configuration](xref:host-and-deploy/iis/index#iis-configuration).</span></span>

* <span data-ttu-id="ca36d-214">Otevřete **programy & funkce** nebo **aplikace & funkce** a potvrďte, že je nainstalované **hostování Windows serveru** .</span><span class="sxs-lookup"><span data-stu-id="ca36d-214">Open **Programs & Features** or **Apps & features** and confirm that **Windows Server Hosting** is installed.</span></span> <span data-ttu-id="ca36d-215">Pokud se v seznamu nainstalovaných programů nenachází **hostování Windows serveru** , Stáhněte a nainstalujte hostující sadu .NET Core.</span><span class="sxs-lookup"><span data-stu-id="ca36d-215">If **Windows Server Hosting** isn't present in the list of installed programs, download and install the .NET Core Hosting Bundle.</span></span>

  [<span data-ttu-id="ca36d-216">Aktuální instalační program sady hostování rozhraní .NET Core (s přímým přístupem ke stažení)</span><span class="sxs-lookup"><span data-stu-id="ca36d-216">Current .NET Core Hosting Bundle installer (direct download)</span></span>](https://www.microsoft.com/net/permalink/dotnetcore-current-windows-runtime-bundle-installer)

  <span data-ttu-id="ca36d-217">Další informace najdete v tématu [instalace hostující sady .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span><span class="sxs-lookup"><span data-stu-id="ca36d-217">For more information, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

* <span data-ttu-id="ca36d-218">Ujistěte se, že **fond aplikací** > **model procesu** > **Identita** je nastavená na **ApplicationPoolIdentity** nebo vlastní identita má správná oprávnění pro přístup ke složce pro nasazení aplikace.</span><span class="sxs-lookup"><span data-stu-id="ca36d-218">Make sure that the **Application Pool** > **Process Model** > **Identity** is set to **ApplicationPoolIdentity** or the custom identity has the correct permissions to access the app's deployment folder.</span></span>

* <span data-ttu-id="ca36d-219">Pokud jste odinstalovali sadu hostující sadu ASP.NET Core a nainstalovali jste starší verzi hostujícího balíčku, soubor *ApplicationHost. config* neobsahuje oddíl pro modul ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ca36d-219">If you uninstalled the ASP.NET Core Hosting Bundle and installed an earlier version of the hosting bundle, the *applicationHost.config* file doesn't include a section for the ASP.NET Core Module.</span></span> <span data-ttu-id="ca36d-220">Otevřete soubor *ApplicationHost. config* v umístění *% windir%/system32/Inetsrv/config* a vyhledejte skupinu oddílů `<configuration><configSections><sectionGroup name="system.webServer">`.</span><span class="sxs-lookup"><span data-stu-id="ca36d-220">Open *applicationHost.config* at *%windir%/System32/inetsrv/config* and find the `<configuration><configSections><sectionGroup name="system.webServer">` section group.</span></span> <span data-ttu-id="ca36d-221">Pokud ve skupině oddílů chybí oddíl ASP.NET Core modulu, přidejte element section:</span><span class="sxs-lookup"><span data-stu-id="ca36d-221">If the section for the ASP.NET Core Module is missing from the section group, add the section element:</span></span>

  ```xml
  <section name="aspNetCore" overrideModeDefault="Allow" />
  ```

  <span data-ttu-id="ca36d-222">Případně nainstalujte nejnovější verzi sady ASP.NET Core hostující sada.</span><span class="sxs-lookup"><span data-stu-id="ca36d-222">Alternatively, install the latest version of the ASP.NET Core Hosting Bundle.</span></span> <span data-ttu-id="ca36d-223">Nejnovější verze je zpětně kompatibilní s podporovanými ASP.NET Core aplikacemi.</span><span class="sxs-lookup"><span data-stu-id="ca36d-223">The latest version is backwards-compatible with supported ASP.NET Core apps.</span></span>

## <a name="incorrect-processpath-missing-path-variable-hosting-bundle-not-installed-systemiis-not-restarted-vc-redistributable-not-installed-or-dotnetexe-access-violation"></a><span data-ttu-id="ca36d-224">Nesprávná processPath, chybějící Proměnná cesty, hostující sada není nainstalovaná, systém/IIS se nerestartuje, VC + + Redistributable není nainstalovaný nebo došlo k narušení přístupu dotnet. exe.</span><span class="sxs-lookup"><span data-stu-id="ca36d-224">Incorrect processPath, missing PATH variable, Hosting Bundle not installed, system/IIS not restarted, VC++ Redistributable not installed, or dotnet.exe access violation</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="ca36d-225">**Prohlížeee** Chyba protokolu HTTP 500,0 – selhání načtení obslužné rutiny ANCM v procesu</span><span class="sxs-lookup"><span data-stu-id="ca36d-225">**Browser:** HTTP Error 500.0 - ANCM In-Process Handler Load Failure</span></span>

* <span data-ttu-id="ca36d-226">**Protokol aplikace:** Aplikaci ' MACHINE/WEBROOT/APPHOST/{ASSEMBLY} ' s fyzickým kořenem ' C: \{PATH} \' se nepodařilo spustit proces pomocí příkazu CommandLine ' "{...}"</span><span class="sxs-lookup"><span data-stu-id="ca36d-226">**Application Log:** Application 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' with physical root 'C:\{PATH}\' failed to start process with commandline '"{...}"</span></span> <span data-ttu-id="ca36d-227">', ErrorCode = ' 0x80070002: 0.</span><span class="sxs-lookup"><span data-stu-id="ca36d-227">', ErrorCode = '0x80070002 : 0.</span></span> <span data-ttu-id="ca36d-228">Aplikaci {PATH} se nepodařilo spustit.</span><span class="sxs-lookup"><span data-stu-id="ca36d-228">Application '{PATH}' wasn't able to start.</span></span> <span data-ttu-id="ca36d-229">Spustitelný soubor se nenašel v cestě {PATH}.</span><span class="sxs-lookup"><span data-stu-id="ca36d-229">Executable was not found at '{PATH}'.</span></span> <span data-ttu-id="ca36d-230">Nepodařilo se spustit aplikaci '/LM/W3SVC/2/ROOT ', ErrorCode ' 0x8007023e '.</span><span class="sxs-lookup"><span data-stu-id="ca36d-230">Failed to start application '/LM/W3SVC/2/ROOT', ErrorCode '0x8007023e'.</span></span>

* <span data-ttu-id="ca36d-231">**Protokol stdout modulu ASP.NET Core:** Soubor protokolu není vytvořen.</span><span class="sxs-lookup"><span data-stu-id="ca36d-231">**ASP.NET Core Module stdout Log:** The log file isn't created.</span></span>

* <span data-ttu-id="ca36d-232">**Protokol ladění ASP.NET Coreho modulu:** Protokol událostí: Aplikaci {PATH} se nepodařilo spustit.</span><span class="sxs-lookup"><span data-stu-id="ca36d-232">**ASP.NET Core Module Debug Log:** Event Log: 'Application '{PATH}' wasn't able to start.</span></span> <span data-ttu-id="ca36d-233">Spustitelný soubor se nenašel v cestě {PATH}.</span><span class="sxs-lookup"><span data-stu-id="ca36d-233">Executable was not found at '{PATH}'.</span></span> <span data-ttu-id="ca36d-234">Vrátila se neúspěšná hodnota HRESULT: 0x8007023e</span><span class="sxs-lookup"><span data-stu-id="ca36d-234">Failed HRESULT returned: 0x8007023e</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* <span data-ttu-id="ca36d-235">**Prohlížeee** Chyba protokolu HTTP 502,5 – chyba procesu</span><span class="sxs-lookup"><span data-stu-id="ca36d-235">**Browser:** HTTP Error 502.5 - Process Failure</span></span>

* <span data-ttu-id="ca36d-236">**Protokol aplikace:** Aplikaci ' MACHINE/WEBROOT/APPHOST/{ASSEMBLY} ' s fyzickým kořenem ' C: \{PATH} \' se nepodařilo spustit proces pomocí příkazu CommandLine ' "{...}"</span><span class="sxs-lookup"><span data-stu-id="ca36d-236">**Application Log:** Application 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' with physical root 'C:\{PATH}\' failed to start process with commandline '"{...}"</span></span> <span data-ttu-id="ca36d-237">', ErrorCode = ' 0x80070002: 0.</span><span class="sxs-lookup"><span data-stu-id="ca36d-237">', ErrorCode = '0x80070002 : 0.</span></span>

* <span data-ttu-id="ca36d-238">**Protokol stdout modulu ASP.NET Core:** Soubor protokolu je vytvořen, ale je prázdný.</span><span class="sxs-lookup"><span data-stu-id="ca36d-238">**ASP.NET Core Module stdout Log:** The log file is created but empty.</span></span>

::: moniker-end

<span data-ttu-id="ca36d-239">Řešení potíží:</span><span class="sxs-lookup"><span data-stu-id="ca36d-239">Troubleshooting:</span></span>

* <span data-ttu-id="ca36d-240">Ověřte, že se aplikace spouští místně na Kestrel.</span><span class="sxs-lookup"><span data-stu-id="ca36d-240">Confirm that the app runs locally on Kestrel.</span></span> <span data-ttu-id="ca36d-241">Selhání procesu může být výsledkem problému v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="ca36d-241">A process failure might be the result of a problem within the app.</span></span> <span data-ttu-id="ca36d-242">Další informace naleznete v tématu <xref:test/troubleshoot-azure-iis>.</span><span class="sxs-lookup"><span data-stu-id="ca36d-242">For more information, see <xref:test/troubleshoot-azure-iis>.</span></span>

* <span data-ttu-id="ca36d-243">Zkontrolujte atribut *processPath* v elementu `<aspNetCore>` v *souboru Web. config* a potvrďte tak, že se jedná o `dotnet` pro nasazení závislé na rozhraní (FDD) nebo `.\{ASSEMBLY}.exe` pro [samostatně uzavřené nasazení (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span><span class="sxs-lookup"><span data-stu-id="ca36d-243">Check the *processPath* attribute on the `<aspNetCore>` element in *web.config* to confirm that it's `dotnet` for a framework-dependent deployment (FDD) or `.\{ASSEMBLY}.exe` for a [self-contained deployment (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>

* <span data-ttu-id="ca36d-244">V případě FDD nemusí být příkaz *dotnet. exe* přístupný prostřednictvím nastavení cesty.</span><span class="sxs-lookup"><span data-stu-id="ca36d-244">For an FDD, *dotnet.exe* might not be accessible via the PATH settings.</span></span> <span data-ttu-id="ca36d-245">Ověřte, že v nastavení systémové cesty existuje *C:\Program Files\dotnet @ no__t-1* .</span><span class="sxs-lookup"><span data-stu-id="ca36d-245">Confirm that *C:\Program Files\dotnet\\* exists in the System PATH settings.</span></span>

* <span data-ttu-id="ca36d-246">Pro FDD nemusí být příkaz *dotnet. exe* dostupný pro identitu uživatele fondu aplikací.</span><span class="sxs-lookup"><span data-stu-id="ca36d-246">For an FDD, *dotnet.exe* might not be accessible for the user identity of the app pool.</span></span> <span data-ttu-id="ca36d-247">Potvrďte, že identita uživatele fondu aplikací má přístup k adresáři *C:\Program Files\dotnet* .</span><span class="sxs-lookup"><span data-stu-id="ca36d-247">Confirm that the app pool user identity has access to the *C:\Program Files\dotnet* directory.</span></span> <span data-ttu-id="ca36d-248">Ověřte, že v adresáři *C:\Program Files\dotnet* a App Directory nejsou nakonfigurovaná žádná pravidla odepření pro identitu uživatele fondu aplikací.</span><span class="sxs-lookup"><span data-stu-id="ca36d-248">Confirm that there are no deny rules configured for the app pool user identity on the *C:\Program Files\dotnet* and app directories.</span></span>

* <span data-ttu-id="ca36d-249">FDD mohla být nasazena a .NET Core nainstalována bez restartování služby IIS.</span><span class="sxs-lookup"><span data-stu-id="ca36d-249">An FDD may have been deployed and .NET Core installed without restarting IIS.</span></span> <span data-ttu-id="ca36d-250">Buď restartujte server, nebo restartujte službu IIS spuštěním příkazu **net stop** , který následuje po příkazu **net start w3svc** z příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="ca36d-250">Either restart the server or restart IIS by executing **net stop was /y** followed by **net start w3svc** from a command prompt.</span></span>

* <span data-ttu-id="ca36d-251">FDD mohla být nasazena bez instalace modulu runtime .NET Core v hostitelském systému.</span><span class="sxs-lookup"><span data-stu-id="ca36d-251">An FDD may have been deployed without installing the .NET Core runtime on the hosting system.</span></span> <span data-ttu-id="ca36d-252">Pokud modul runtime .NET Core není nainstalovaný, spusťte v systému **instalační program sady hostitelských svazků .NET Core** .</span><span class="sxs-lookup"><span data-stu-id="ca36d-252">If the .NET Core runtime hasn't been installed, run the **.NET Core Hosting Bundle installer** on the system.</span></span>

  [<span data-ttu-id="ca36d-253">Aktuální instalační program sady hostování rozhraní .NET Core (s přímým přístupem ke stažení)</span><span class="sxs-lookup"><span data-stu-id="ca36d-253">Current .NET Core Hosting Bundle installer (direct download)</span></span>](https://www.microsoft.com/net/permalink/dotnetcore-current-windows-runtime-bundle-installer)

  <span data-ttu-id="ca36d-254">Další informace najdete v tématu [instalace hostující sady .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span><span class="sxs-lookup"><span data-stu-id="ca36d-254">For more information, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

  <span data-ttu-id="ca36d-255">Pokud je nutný konkrétní modul runtime, Stáhněte si modul runtime z [archivu stahování v rozhraní .NET](https://dotnet.microsoft.com/download/archives) a nainstalujte ho do systému.</span><span class="sxs-lookup"><span data-stu-id="ca36d-255">If a specific runtime is required, download the runtime from the [.NET Download Archives](https://dotnet.microsoft.com/download/archives) and install it on the system.</span></span> <span data-ttu-id="ca36d-256">Dokončete instalaci restartováním systému nebo restartováním služby IIS spuštěním příkazu **net stop** , který následuje po příkazu **net start w3svc** z příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="ca36d-256">Complete the installation by restarting the system or restarting IIS by executing **net stop was /y** followed by **net start w3svc** from a command prompt.</span></span>

## <a name="incorrect-arguments-of-aspnetcore-element"></a><span data-ttu-id="ca36d-257">Nesprávné argumenty elementu \<aspNetCore ></span><span class="sxs-lookup"><span data-stu-id="ca36d-257">Incorrect arguments of \<aspNetCore> element</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="ca36d-258">**Prohlížeee** Chyba protokolu HTTP 500,0 – selhání načtení obslužné rutiny ANCM v procesu</span><span class="sxs-lookup"><span data-stu-id="ca36d-258">**Browser:** HTTP Error 500.0 - ANCM In-Process Handler Load Failure</span></span>

* <span data-ttu-id="ca36d-259">**Protokol aplikace:** Vyvolání hostfxr k nalezení obslužné rutiny žádosti o zpracování se nezdařilo bez nalezení nativních závislostí.</span><span class="sxs-lookup"><span data-stu-id="ca36d-259">**Application Log:** Invoking hostfxr to find the inprocess request handler failed without finding any native dependencies.</span></span> <span data-ttu-id="ca36d-260">To pravděpodobně znamená, že aplikace je nesprávně nakonfigurovaná. Zkontrolujte prosím verze Microsoft. NetCore. app a Microsoft. AspNetCore. app, na které cílí aplikace a které jsou nainstalované v počítači.</span><span class="sxs-lookup"><span data-stu-id="ca36d-260">This most likely means the app is misconfigured, please check the versions of Microsoft.NetCore.App and Microsoft.AspNetCore.App that are targeted by the application and are installed on the machine.</span></span> <span data-ttu-id="ca36d-261">Nepovedlo se najít obslužnou rutinu žádosti o zpracování.</span><span class="sxs-lookup"><span data-stu-id="ca36d-261">Could not find inprocess request handler.</span></span> <span data-ttu-id="ca36d-262">Zachycený výstup z vyvolání hostfxr: Měli jste v úmyslu spustit příkazy sady dotnet SDK?</span><span class="sxs-lookup"><span data-stu-id="ca36d-262">Captured output from invoking hostfxr: Did you mean to run dotnet SDK commands?</span></span> <span data-ttu-id="ca36d-263">Nainstalujte prosím sadu dotnet SDK z: https://go.microsoft.com/fwlink/?LinkID=798306&clcid=0x409 spuštění aplikace "/LM/W3SVC/3/ROOT", ErrorCode "0x8000FFFF" se nezdařilo.</span><span class="sxs-lookup"><span data-stu-id="ca36d-263">Please install dotnet SDK from: https://go.microsoft.com/fwlink/?LinkID=798306&clcid=0x409 Failed to start application '/LM/W3SVC/3/ROOT', ErrorCode '0x8000ffff'.</span></span>

* <span data-ttu-id="ca36d-264">**Protokol stdout modulu ASP.NET Core:** Měli jste v úmyslu spustit příkazy sady dotnet SDK?</span><span class="sxs-lookup"><span data-stu-id="ca36d-264">**ASP.NET Core Module stdout Log:** Did you mean to run dotnet SDK commands?</span></span> <span data-ttu-id="ca36d-265">Nainstalujte prosím sadu dotnet SDK z: https://go.microsoft.com/fwlink/?LinkID=798306&clcid=0x409</span><span class="sxs-lookup"><span data-stu-id="ca36d-265">Please install dotnet SDK from: https://go.microsoft.com/fwlink/?LinkID=798306&clcid=0x409</span></span>

* <span data-ttu-id="ca36d-266">**Protokol ladění ASP.NET Coreho modulu:** Vyvolání hostfxr k nalezení obslužné rutiny žádosti o zpracování se nezdařilo bez nalezení nativních závislostí.</span><span class="sxs-lookup"><span data-stu-id="ca36d-266">**ASP.NET Core Module Debug Log:** Invoking hostfxr to find the inprocess request handler failed without finding any native dependencies.</span></span> <span data-ttu-id="ca36d-267">To pravděpodobně znamená, že aplikace je nesprávně nakonfigurovaná. Zkontrolujte prosím verze Microsoft. NetCore. app a Microsoft. AspNetCore. app, na které cílí aplikace a které jsou nainstalované v počítači.</span><span class="sxs-lookup"><span data-stu-id="ca36d-267">This most likely means the app is misconfigured, please check the versions of Microsoft.NetCore.App and Microsoft.AspNetCore.App that are targeted by the application and are installed on the machine.</span></span> <span data-ttu-id="ca36d-268">Vrátila se neúspěšná hodnota HRESULT: 0x8000FFFF nemůže najít obslužnou rutinu žádosti o zpracování.</span><span class="sxs-lookup"><span data-stu-id="ca36d-268">Failed HRESULT returned: 0x8000ffff Could not find inprocess request handler.</span></span> <span data-ttu-id="ca36d-269">Zachycený výstup z vyvolání hostfxr: Měli jste v úmyslu spustit příkazy sady dotnet SDK?</span><span class="sxs-lookup"><span data-stu-id="ca36d-269">Captured output from invoking hostfxr: Did you mean to run dotnet SDK commands?</span></span> <span data-ttu-id="ca36d-270">Nainstalujte prosím sadu dotnet SDK z: vrácení neúspěšného https://go.microsoft.com/fwlink/?LinkID=798306&clcid=0x409 vrácených HRESULT: 0x8000ffff</span><span class="sxs-lookup"><span data-stu-id="ca36d-270">Please install dotnet SDK from: https://go.microsoft.com/fwlink/?LinkID=798306&clcid=0x409 Failed HRESULT returned: 0x8000ffff</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* <span data-ttu-id="ca36d-271">**Prohlížeee** Chyba protokolu HTTP 502,5 – chyba procesu</span><span class="sxs-lookup"><span data-stu-id="ca36d-271">**Browser:** HTTP Error 502.5 - Process Failure</span></span>

* <span data-ttu-id="ca36d-272">**Protokol aplikace:** Aplikaci ' MACHINE/WEBROOT/APPHOST/{ASSEMBLY} ' s fyzickým kořenem ' C: \{PATH} \' se nepodařilo spustit proces pomocí příkazu CommandLine ' "dotnet". \{ASSEMBLY}. dll ", ErrorCode = ' 0x80004005: 80008081.</span><span class="sxs-lookup"><span data-stu-id="ca36d-272">**Application Log:** Application 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' with physical root 'C:\{PATH}\' failed to start process with commandline '"dotnet" .\{ASSEMBLY}.dll', ErrorCode = '0x80004005 : 80008081.</span></span>

* <span data-ttu-id="ca36d-273">**Protokol stdout modulu ASP.NET Core:** Aplikace, která má být spuštěna, neexistuje: ' PATH @ no__t-0ASSEMBLY}. dll '</span><span class="sxs-lookup"><span data-stu-id="ca36d-273">**ASP.NET Core Module stdout Log:** The application to execute does not exist: 'PATH\{ASSEMBLY}.dll'</span></span>

::: moniker-end

<span data-ttu-id="ca36d-274">Řešení potíží:</span><span class="sxs-lookup"><span data-stu-id="ca36d-274">Troubleshooting:</span></span>

* <span data-ttu-id="ca36d-275">Ověřte, že se aplikace spouští místně na Kestrel.</span><span class="sxs-lookup"><span data-stu-id="ca36d-275">Confirm that the app runs locally on Kestrel.</span></span> <span data-ttu-id="ca36d-276">Selhání procesu může být výsledkem problému v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="ca36d-276">A process failure might be the result of a problem within the app.</span></span> <span data-ttu-id="ca36d-277">Další informace naleznete v tématu <xref:test/troubleshoot-azure-iis>.</span><span class="sxs-lookup"><span data-stu-id="ca36d-277">For more information, see <xref:test/troubleshoot-azure-iis>.</span></span>

* <span data-ttu-id="ca36d-278">Zkontrolujte atribut *arguments* elementu `<aspNetCore>` v *souboru Web. config* a potvrďte tak, že je to buď (a) `.\{ASSEMBLY}.dll` pro nasazení závislé na rozhraní (FDD); nebo (b) není k dispozici, prázdný řetězec (`arguments=""`) nebo seznam argumentů aplikace (`arguments="{ARGUMENT_1}, {ARGUMENT_2}, ... {ARGUMENT_X}"`) pro samostatně uzavřené nasazení (SCD).</span><span class="sxs-lookup"><span data-stu-id="ca36d-278">Examine the *arguments* attribute on the `<aspNetCore>` element in *web.config* to confirm that it's either (a) `.\{ASSEMBLY}.dll` for a framework-dependent deployment (FDD); or (b) not present, an empty string (`arguments=""`), or a list of the app's arguments (`arguments="{ARGUMENT_1}, {ARGUMENT_2}, ... {ARGUMENT_X}"`) for a self-contained deployment (SCD).</span></span>

::: moniker range=">= aspnetcore-2.2"

## <a name="missing-net-core-shared-framework"></a><span data-ttu-id="ca36d-279">Chybějící sdílené rozhraní .NET Core</span><span class="sxs-lookup"><span data-stu-id="ca36d-279">Missing .NET Core shared framework</span></span>

* <span data-ttu-id="ca36d-280">**Prohlížeee** Chyba protokolu HTTP 500,0 – selhání načtení obslužné rutiny ANCM v procesu</span><span class="sxs-lookup"><span data-stu-id="ca36d-280">**Browser:** HTTP Error 500.0 - ANCM In-Process Handler Load Failure</span></span>

* <span data-ttu-id="ca36d-281">**Protokol aplikace:** Vyvolání hostfxr k nalezení obslužné rutiny žádosti o zpracování se nezdařilo bez nalezení nativních závislostí.</span><span class="sxs-lookup"><span data-stu-id="ca36d-281">**Application Log:** Invoking hostfxr to find the inprocess request handler failed without finding any native dependencies.</span></span> <span data-ttu-id="ca36d-282">To pravděpodobně znamená, že aplikace je nesprávně nakonfigurovaná. Zkontrolujte prosím verze Microsoft. NetCore. app a Microsoft. AspNetCore. app, na které cílí aplikace a které jsou nainstalované v počítači.</span><span class="sxs-lookup"><span data-stu-id="ca36d-282">This most likely means the app is misconfigured, please check the versions of Microsoft.NetCore.App and Microsoft.AspNetCore.App that are targeted by the application and are installed on the machine.</span></span> <span data-ttu-id="ca36d-283">Nepovedlo se najít obslužnou rutinu žádosti o zpracování.</span><span class="sxs-lookup"><span data-stu-id="ca36d-283">Could not find inprocess request handler.</span></span> <span data-ttu-id="ca36d-284">Zachycený výstup z vyvolání hostfxr: Nebylo možné najít žádnou kompatibilní verzi rozhraní.</span><span class="sxs-lookup"><span data-stu-id="ca36d-284">Captured output from invoking hostfxr: It was not possible to find any compatible framework version.</span></span> <span data-ttu-id="ca36d-285">Zadané rozhraní Microsoft. AspNetCore. app, verze {VERSION} se nenašlo.</span><span class="sxs-lookup"><span data-stu-id="ca36d-285">The specified framework 'Microsoft.AspNetCore.App', version '{VERSION}' was not found.</span></span>

<span data-ttu-id="ca36d-286">Nepodařilo se spustit aplikaci '/LM/W3SVC/5/ROOT ', ErrorCode ' 0x8000FFFF '.</span><span class="sxs-lookup"><span data-stu-id="ca36d-286">Failed to start application '/LM/W3SVC/5/ROOT', ErrorCode '0x8000ffff'.</span></span>

* <span data-ttu-id="ca36d-287">**Protokol stdout modulu ASP.NET Core:** Nebylo možné najít žádnou kompatibilní verzi rozhraní.</span><span class="sxs-lookup"><span data-stu-id="ca36d-287">**ASP.NET Core Module stdout Log:** It was not possible to find any compatible framework version.</span></span> <span data-ttu-id="ca36d-288">Zadané rozhraní Microsoft. AspNetCore. app, verze {VERSION} se nenašlo.</span><span class="sxs-lookup"><span data-stu-id="ca36d-288">The specified framework 'Microsoft.AspNetCore.App', version '{VERSION}' was not found.</span></span>

* <span data-ttu-id="ca36d-289">**Protokol ladění ASP.NET Coreho modulu:** Vrátila se neúspěšná hodnota HRESULT: 0x8000ffff</span><span class="sxs-lookup"><span data-stu-id="ca36d-289">**ASP.NET Core Module Debug Log:** Failed HRESULT returned: 0x8000ffff</span></span>

::: moniker-end

<span data-ttu-id="ca36d-290">Řešení potíží:</span><span class="sxs-lookup"><span data-stu-id="ca36d-290">Troubleshooting:</span></span>

<span data-ttu-id="ca36d-291">Pro nasazení závislé na rozhraní (FDD) Zkontrolujte, že je v systému nainstalovaný správný modul runtime.</span><span class="sxs-lookup"><span data-stu-id="ca36d-291">For a framework-dependent deployment (FDD), confirm that the correct runtime installed on the system.</span></span>

## <a name="stopped-application-pool"></a><span data-ttu-id="ca36d-292">Fond aplikací se zastavil.</span><span class="sxs-lookup"><span data-stu-id="ca36d-292">Stopped Application Pool</span></span>

* <span data-ttu-id="ca36d-293">**Prohlížeee** Služba 503 není dostupná.</span><span class="sxs-lookup"><span data-stu-id="ca36d-293">**Browser:** 503 Service Unavailable</span></span>

* <span data-ttu-id="ca36d-294">**Protokol aplikace:** Žádná položka</span><span class="sxs-lookup"><span data-stu-id="ca36d-294">**Application Log:** No entry</span></span>

* <span data-ttu-id="ca36d-295">**Protokol stdout modulu ASP.NET Core:** Soubor protokolu není vytvořen.</span><span class="sxs-lookup"><span data-stu-id="ca36d-295">**ASP.NET Core Module stdout Log:** The log file isn't created.</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="ca36d-296">**Protokol ladění ASP.NET Coreho modulu:** Soubor protokolu není vytvořen.</span><span class="sxs-lookup"><span data-stu-id="ca36d-296">**ASP.NET Core Module Debug Log:** The log file isn't created.</span></span>

::: moniker-end

<span data-ttu-id="ca36d-297">Řešení potíží:</span><span class="sxs-lookup"><span data-stu-id="ca36d-297">Troubleshooting:</span></span>

<span data-ttu-id="ca36d-298">Ověřte, že fond aplikací není v *zastaveném* stavu.</span><span class="sxs-lookup"><span data-stu-id="ca36d-298">Confirm that the Application Pool isn't in the *Stopped* state.</span></span>

## <a name="sub-application-includes-a-handlers-section"></a><span data-ttu-id="ca36d-299">Dílčí aplikace obsahuje oddíl \<handlers >.</span><span class="sxs-lookup"><span data-stu-id="ca36d-299">Sub-application includes a \<handlers> section</span></span>

* <span data-ttu-id="ca36d-300">**Prohlížeee** Chyba protokolu HTTP 500,19 – interní chyba serveru</span><span class="sxs-lookup"><span data-stu-id="ca36d-300">**Browser:** HTTP Error 500.19 - Internal Server Error</span></span>

* <span data-ttu-id="ca36d-301">**Protokol aplikace:** Žádná položka</span><span class="sxs-lookup"><span data-stu-id="ca36d-301">**Application Log:** No entry</span></span>

* <span data-ttu-id="ca36d-302">**Protokol stdout modulu ASP.NET Core:** Vytvoří se soubor protokolu kořenové aplikace a zobrazí se normální operace.</span><span class="sxs-lookup"><span data-stu-id="ca36d-302">**ASP.NET Core Module stdout Log:** The root app's log file is created and shows normal operation.</span></span> <span data-ttu-id="ca36d-303">Soubor protokolu dílčí aplikace se nevytvoří.</span><span class="sxs-lookup"><span data-stu-id="ca36d-303">The sub-app's log file isn't created.</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="ca36d-304">**Protokol ladění ASP.NET Coreho modulu:** Vytvoří se soubor protokolu kořenové aplikace a zobrazí se normální operace.</span><span class="sxs-lookup"><span data-stu-id="ca36d-304">**ASP.NET Core Module Debug Log:** The root app's log file is created and shows normal operation.</span></span> <span data-ttu-id="ca36d-305">Soubor protokolu dílčí aplikace se nevytvoří.</span><span class="sxs-lookup"><span data-stu-id="ca36d-305">The sub-app's log file isn't created.</span></span>

::: moniker-end

<span data-ttu-id="ca36d-306">Řešení potíží:</span><span class="sxs-lookup"><span data-stu-id="ca36d-306">Troubleshooting:</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="ca36d-307">Potvrďte, že soubor *Web. config* dílčí aplikace neobsahuje oddíl `<handlers>` nebo že dílčí aplikace nedědí obslužné rutiny nadřazené aplikace.</span><span class="sxs-lookup"><span data-stu-id="ca36d-307">Confirm that the sub-app's *web.config* file doesn't include a `<handlers>` section or that the sub-app doesn't inherit the parent app's handlers.</span></span>

<span data-ttu-id="ca36d-308">Oddíl *Web. config* nadřazené aplikace `<system.webServer>` je umístěn uvnitř elementu `<location>`.</span><span class="sxs-lookup"><span data-stu-id="ca36d-308">The parent app's `<system.webServer>` section of *web.config* is placed inside of a `<location>` element.</span></span> <span data-ttu-id="ca36d-309">Vlastnost <xref:System.Configuration.SectionInformation.InheritInChildApplications*> je nastavena na hodnotu `false` k označení toho, že nastavení zadaná v rámci [> prvku \<location](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) nejsou děděna aplikacemi, které se nacházejí v podadresáři nadřazené aplikace.</span><span class="sxs-lookup"><span data-stu-id="ca36d-309">The <xref:System.Configuration.SectionInformation.InheritInChildApplications*> property is set to `false` to indicate that the settings specified within the [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) element aren't inherited by apps that reside in a subdirectory of the parent app.</span></span> <span data-ttu-id="ca36d-310">Další informace naleznete v tématu <xref:host-and-deploy/aspnet-core-module>.</span><span class="sxs-lookup"><span data-stu-id="ca36d-310">For more information, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="ca36d-311">Potvrďte, že soubor *Web. config* dílčí aplikace neobsahuje oddíl `<handlers>`.</span><span class="sxs-lookup"><span data-stu-id="ca36d-311">Confirm that the sub-app's *web.config* file doesn't include a `<handlers>` section.</span></span>

::: moniker-end

## <a name="stdout-log-path-incorrect"></a><span data-ttu-id="ca36d-312">nesprávná cesta protokolu STDOUT</span><span class="sxs-lookup"><span data-stu-id="ca36d-312">stdout log path incorrect</span></span>

* <span data-ttu-id="ca36d-313">**Prohlížeee** Aplikace reaguje normálně.</span><span class="sxs-lookup"><span data-stu-id="ca36d-313">**Browser:** The app responds normally.</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="ca36d-314">**Protokol aplikace:** Nelze spustit přesměrování stdout v adresáři C:\Program Files\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll.</span><span class="sxs-lookup"><span data-stu-id="ca36d-314">**Application Log:** Could not start stdout redirection in C:\Program Files\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll.</span></span> <span data-ttu-id="ca36d-315">Zpráva o výjimce: HRESULT 0x80070005 vrácený v {PATH} \aspnetcoremodulev2\commonlib\fileoutputmanager.cpp: 84.</span><span class="sxs-lookup"><span data-stu-id="ca36d-315">Exception message: HRESULT 0x80070005 returned at {PATH}\aspnetcoremodulev2\commonlib\fileoutputmanager.cpp:84.</span></span> <span data-ttu-id="ca36d-316">Nejde zastavit přesměrování stdout v adresáři C:\Program Files\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll.</span><span class="sxs-lookup"><span data-stu-id="ca36d-316">Could not stop stdout redirection in C:\Program Files\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll.</span></span> <span data-ttu-id="ca36d-317">Zpráva o výjimce: HRESULT 0x80070002 vráceno v {PATH}.</span><span class="sxs-lookup"><span data-stu-id="ca36d-317">Exception message: HRESULT 0x80070002 returned at {PATH}.</span></span> <span data-ttu-id="ca36d-318">Nejde spustit přesměrování stdout v cestě {PATH} \aspnetcorev2_inprocess.dll.</span><span class="sxs-lookup"><span data-stu-id="ca36d-318">Could not start stdout redirection in {PATH}\aspnetcorev2_inprocess.dll.</span></span>

* <span data-ttu-id="ca36d-319">**Protokol stdout modulu ASP.NET Core:** Soubor protokolu není vytvořen.</span><span class="sxs-lookup"><span data-stu-id="ca36d-319">**ASP.NET Core Module stdout Log:** The log file isn't created.</span></span>

* <span data-ttu-id="ca36d-320">**Protokol ladění ASP.NET Coreho modulu:** Nelze spustit přesměrování stdout v adresáři C:\Program Files\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll.</span><span class="sxs-lookup"><span data-stu-id="ca36d-320">**ASP.NET Core Module debug Log:** Could not start stdout redirection in C:\Program Files\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll.</span></span> <span data-ttu-id="ca36d-321">Zpráva o výjimce: HRESULT 0x80070005 vrácený v {PATH} \aspnetcoremodulev2\commonlib\fileoutputmanager.cpp: 84.</span><span class="sxs-lookup"><span data-stu-id="ca36d-321">Exception message: HRESULT 0x80070005 returned at {PATH}\aspnetcoremodulev2\commonlib\fileoutputmanager.cpp:84.</span></span> <span data-ttu-id="ca36d-322">Nejde zastavit přesměrování stdout v adresáři C:\Program Files\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll.</span><span class="sxs-lookup"><span data-stu-id="ca36d-322">Could not stop stdout redirection in C:\Program Files\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll.</span></span> <span data-ttu-id="ca36d-323">Zpráva o výjimce: HRESULT 0x80070002 vráceno v {PATH}.</span><span class="sxs-lookup"><span data-stu-id="ca36d-323">Exception message: HRESULT 0x80070002 returned at {PATH}.</span></span> <span data-ttu-id="ca36d-324">Nejde spustit přesměrování stdout v cestě {PATH} \aspnetcorev2_inprocess.dll.</span><span class="sxs-lookup"><span data-stu-id="ca36d-324">Could not start stdout redirection in {PATH}\aspnetcorev2_inprocess.dll.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* <span data-ttu-id="ca36d-325">**Protokol aplikace:** Upozornění: Nepovedlo se vytvořit stdoutLogFile \\? \{PATH} \path_doesnt_exist\stdout_{PROCESS ID} _ {TIMESTAMP}. log, ErrorCode =-2147024893.</span><span class="sxs-lookup"><span data-stu-id="ca36d-325">**Application Log:** Warning: Could not create stdoutLogFile \\?\{PATH}\path_doesnt_exist\stdout_{PROCESS ID}_{TIMESTAMP}.log, ErrorCode = -2147024893.</span></span>

* <span data-ttu-id="ca36d-326">**Protokol stdout modulu ASP.NET Core:** Soubor protokolu není vytvořen.</span><span class="sxs-lookup"><span data-stu-id="ca36d-326">**ASP.NET Core Module stdout Log:** The log file isn't created.</span></span>

::: moniker-end

<span data-ttu-id="ca36d-327">Řešení potíží:</span><span class="sxs-lookup"><span data-stu-id="ca36d-327">Troubleshooting:</span></span>

* <span data-ttu-id="ca36d-328">Cesta `stdoutLogFile` zadaná v prvku `<aspNetCore>` souboru *Web. config* neexistuje.</span><span class="sxs-lookup"><span data-stu-id="ca36d-328">The `stdoutLogFile` path specified in the `<aspNetCore>` element of *web.config* doesn't exist.</span></span> <span data-ttu-id="ca36d-329">Další informace najdete v tématu @no__t 0ASP.NET Core Module: Vytváření a přesměrování protokolu @ no__t-0.</span><span class="sxs-lookup"><span data-stu-id="ca36d-329">For more information, see [ASP.NET Core Module: Log creation and redirection](xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection).</span></span>

* <span data-ttu-id="ca36d-330">Uživatel fondu aplikací nemá přístup pro zápis do cesty protokolu STDOUT.</span><span class="sxs-lookup"><span data-stu-id="ca36d-330">The app pool user doesn't have write access to the stdout log path.</span></span>

## <a name="application-configuration-general-issue"></a><span data-ttu-id="ca36d-331">Obecný problém konfigurace aplikace</span><span class="sxs-lookup"><span data-stu-id="ca36d-331">Application configuration general issue</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="ca36d-332">**Prohlížeee** Chyba protokolu HTTP 500,0 – Chyba při načítání obslužné rutiny ANCM v procesu **--nebo--** Chyba protokolu HTTP 500,30-ANCM v procesu spuštění při selhání</span><span class="sxs-lookup"><span data-stu-id="ca36d-332">**Browser:** HTTP Error 500.0 - ANCM In-Process Handler Load Failure **--OR--** HTTP Error 500.30 - ANCM In-Process Start Failure</span></span>

* <span data-ttu-id="ca36d-333">**Protokol aplikace:** Proměnná</span><span class="sxs-lookup"><span data-stu-id="ca36d-333">**Application Log:** Variable</span></span>

* <span data-ttu-id="ca36d-334">**Protokol stdout modulu ASP.NET Core:** Soubor protokolu se vytvoří, ale vyprázdní nebo vytvoří s normálními položkami, dokud neselže bod aplikace.</span><span class="sxs-lookup"><span data-stu-id="ca36d-334">**ASP.NET Core Module stdout Log:** The log file is created but empty or created with normal entries until the point of the app failing.</span></span>

* <span data-ttu-id="ca36d-335">**Protokol ladění ASP.NET Coreho modulu:** Proměnná</span><span class="sxs-lookup"><span data-stu-id="ca36d-335">**ASP.NET Core Module Debug Log:** Variable</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* <span data-ttu-id="ca36d-336">**Prohlížeee** Chyba protokolu HTTP 502,5 – chyba procesu</span><span class="sxs-lookup"><span data-stu-id="ca36d-336">**Browser:** HTTP Error 502.5 - Process Failure</span></span>

* <span data-ttu-id="ca36d-337">**Protokol aplikace:** Aplikace ' MACHINE/WEBROOT/APPHOST/{ASSEMBLY} ' s fyzickým kořenovým ' C: \{PATH} vytvořeným procesem \' s použitím příkazu CommandLine ' "C: \{PATH} \{ASSEMBLY}. {exe | dll} "", ale buď došlo k chybě, nebo nereagovala nebo nenaslouchala na daném portu "{PORT}", ErrorCode = "{kód chyby}"</span><span class="sxs-lookup"><span data-stu-id="ca36d-337">**Application Log:** Application 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' with physical root 'C:\{PATH}\' created process with commandline '"C:\{PATH}\{ASSEMBLY}.{exe|dll}" ' but either crashed or did not respond or did not listen on the given port '{PORT}', ErrorCode = '{ERROR CODE}'</span></span>

* <span data-ttu-id="ca36d-338">**Protokol stdout modulu ASP.NET Core:** Soubor protokolu je vytvořen, ale je prázdný.</span><span class="sxs-lookup"><span data-stu-id="ca36d-338">**ASP.NET Core Module stdout Log:** The log file is created but empty.</span></span>

::: moniker-end

<span data-ttu-id="ca36d-339">Řešení potíží:</span><span class="sxs-lookup"><span data-stu-id="ca36d-339">Troubleshooting:</span></span>

<span data-ttu-id="ca36d-340">Proces se nepovedlo spustit, pravděpodobně v důsledku konfigurace aplikace nebo problému s programováním.</span><span class="sxs-lookup"><span data-stu-id="ca36d-340">The process failed to start, most likely due to an app configuration or programming issue.</span></span>

<span data-ttu-id="ca36d-341">Další informace naleznete v následujících tématech:</span><span class="sxs-lookup"><span data-stu-id="ca36d-341">For more information, see the following topics:</span></span>

* <xref:test/troubleshoot-azure-iis>
* <xref:test/troubleshoot>
