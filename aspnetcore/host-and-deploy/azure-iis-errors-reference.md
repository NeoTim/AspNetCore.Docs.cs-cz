---
title: Referenční informace o běžných chybách pro Azure App Service a IIS s ASP.NET Core
author: guardrex
description: Získejte Rady pro řešení běžných chyb při hostování ASP.NET Corech aplikací ve službě Azure Apps a službě IIS.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/10/2019
uid: host-and-deploy/azure-iis-errors-reference
ms.openlocfilehash: 3030bc57be113d9034123c96403742442b9240bb
ms.sourcegitcommit: b40613c603d6f0cc71f3232c16df61550907f550
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/18/2019
ms.locfileid: "68308104"
---
# <a name="common-errors-reference-for-azure-app-service-and-iis-with-aspnet-core"></a><span data-ttu-id="5c661-103">Referenční informace o běžných chybách pro Azure App Service a IIS s ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5c661-103">Common errors reference for Azure App Service and IIS with ASP.NET Core</span></span>

<span data-ttu-id="5c661-104">Podle [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="5c661-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="5c661-105">Toto téma nabízí Rady pro řešení běžných chyb při hostování ASP.NET Corech aplikací ve službě Azure Apps a službě IIS.</span><span class="sxs-lookup"><span data-stu-id="5c661-105">This topic offers troubleshooting advice for common errors when hosting ASP.NET Core apps on Azure Apps Service and IIS.</span></span>

<span data-ttu-id="5c661-106">Shromážděte následující informace:</span><span class="sxs-lookup"><span data-stu-id="5c661-106">Collect the following information:</span></span>

* <span data-ttu-id="5c661-107">Chování prohlížeče (Stavový kód a chybová zpráva)</span><span class="sxs-lookup"><span data-stu-id="5c661-107">Browser behavior (status code and error message)</span></span>
* <span data-ttu-id="5c661-108">Položky protokolu událostí aplikace</span><span class="sxs-lookup"><span data-stu-id="5c661-108">Application Event Log entries</span></span>
  * <span data-ttu-id="5c661-109">Azure App Service &ndash; najdete <xref:test/troubleshoot-azure-iis>v tématu.</span><span class="sxs-lookup"><span data-stu-id="5c661-109">Azure App Service &ndash; See <xref:test/troubleshoot-azure-iis>.</span></span>
  * <span data-ttu-id="5c661-110">IIS</span><span class="sxs-lookup"><span data-stu-id="5c661-110">IIS</span></span>
    1. <span data-ttu-id="5c661-111">V nabídce **systému Windows** vyberte **Start** , zadejte příkaz *Prohlížeč událostí*a stiskněte klávesu **ENTER**.</span><span class="sxs-lookup"><span data-stu-id="5c661-111">Select **Start** on the **Windows** menu, type *Event Viewer*, and press **Enter**.</span></span>
    1. <span data-ttu-id="5c661-112">Po otevření **Prohlížeč událostí** rozbalte na postranním panelu položku **protokoly** > systému Windows.</span><span class="sxs-lookup"><span data-stu-id="5c661-112">After the **Event Viewer** opens, expand **Windows Logs** > **Application** in the sidebar.</span></span>
* <span data-ttu-id="5c661-113">ASP.NET Core modul stdout a položky protokolu ladění</span><span class="sxs-lookup"><span data-stu-id="5c661-113">ASP.NET Core Module stdout and debug log entries</span></span>
  * <span data-ttu-id="5c661-114">Azure App Service &ndash; najdete <xref:test/troubleshoot-azure-iis>v tématu.</span><span class="sxs-lookup"><span data-stu-id="5c661-114">Azure App Service &ndash; See <xref:test/troubleshoot-azure-iis>.</span></span>
  * <span data-ttu-id="5c661-115">Služba &ndash; IIS podle pokynů v částech [Vytvoření protokolu a přesměrování](xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection) a [Rozšířené diagnostické protokoly](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) v tématu ASP.NET Core modulu.</span><span class="sxs-lookup"><span data-stu-id="5c661-115">IIS &ndash; Follow the instructions in the [Log creation and redirection](xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection) and [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) sections of the ASP.NET Core Module topic.</span></span>

<span data-ttu-id="5c661-116">Porovnejte informace o chybě s následujícími běžnými chybami.</span><span class="sxs-lookup"><span data-stu-id="5c661-116">Compare error information to the following common errors.</span></span> <span data-ttu-id="5c661-117">Pokud se najde shoda, postupujte podle pokynů pro řešení potíží.</span><span class="sxs-lookup"><span data-stu-id="5c661-117">If a match is found, follow the troubleshooting advice.</span></span>

<span data-ttu-id="5c661-118">Seznam chyb v tomto tématu není vyčerpávající.</span><span class="sxs-lookup"><span data-stu-id="5c661-118">The list of errors in this topic isn't exhaustive.</span></span> <span data-ttu-id="5c661-119">Pokud narazíte na chybu, která zde není uvedená, otevřete nový problém pomocí tlačítka pro **odeslání obsahu** v dolní části tohoto tématu s podrobnými pokyny pro reprodukování chyby.</span><span class="sxs-lookup"><span data-stu-id="5c661-119">If you encounter an error not listed here, open a new issue using the **Content feedback** button at the bottom of this topic with detailed instructions on how to reproduce the error.</span></span>

[!INCLUDE[Azure App Service Preview Notice](../includes/azure-apps-preview-notice.md)]

## <a name="installer-unable-to-obtain-vc-redistributable"></a><span data-ttu-id="5c661-120">Instalační program nemůže získat balíček VC + + Redistributable.</span><span class="sxs-lookup"><span data-stu-id="5c661-120">Installer unable to obtain VC++ Redistributable</span></span>

* <span data-ttu-id="5c661-121">**Výjimka instalačního programu:** 0x80072EFD **--nebo--** 0x80072f76 – neurčená chyba</span><span class="sxs-lookup"><span data-stu-id="5c661-121">**Installer Exception:** 0x80072efd **--OR--** 0x80072f76 - Unspecified error</span></span>

* <span data-ttu-id="5c661-122">**Výjimka&#8224;protokolu instalačního programu:** Chyba 0x80072EFD **--nebo--** 0x80072f76: Nepovedlo se spustit balíček EXE.</span><span class="sxs-lookup"><span data-stu-id="5c661-122">**Installer Log Exception&#8224;:** Error 0x80072efd **--OR--** 0x80072f76: Failed to execute EXE package</span></span>

  <span data-ttu-id="5c661-123">&#8224;Protokol se nachází na adrese *C:\Users\{uživatel} \AppData\Local\Temp\dd_DotNetCoreWinSvrHosting__{timestamp}.log*.</span><span class="sxs-lookup"><span data-stu-id="5c661-123">&#8224;The log is located at *C:\Users\{USER}\AppData\Local\Temp\dd_DotNetCoreWinSvrHosting__{TIMESTAMP}.log*.</span></span>

<span data-ttu-id="5c661-124">Řešení potíží:</span><span class="sxs-lookup"><span data-stu-id="5c661-124">Troubleshooting:</span></span>

<span data-ttu-id="5c661-125">Pokud systém nemá přístup k Internetu při [instalaci hostující sady .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle), k této výjimce dojde, když instalačnímu programu zabráníte v získání sady *Microsoft C++ Visual 2015 Redistributable*.</span><span class="sxs-lookup"><span data-stu-id="5c661-125">If the system doesn't have Internet access while [installing the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle), this exception occurs when the installer is prevented from obtaining the *Microsoft Visual C++ 2015 Redistributable*.</span></span> <span data-ttu-id="5c661-126">Získejte instalační program z [webu Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=53840).</span><span class="sxs-lookup"><span data-stu-id="5c661-126">Obtain an installer from the [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=53840).</span></span> <span data-ttu-id="5c661-127">Pokud se instalační program nepovede, server nemusí získat modul runtime .NET Core potřebný k hostování [nasazení závislého na rozhraní (FDD)](/dotnet/core/deploying/#framework-dependent-deployments-fdd).</span><span class="sxs-lookup"><span data-stu-id="5c661-127">If the installer fails, the server may not receive the .NET Core runtime required to host a [framework-dependent deployment (FDD)](/dotnet/core/deploying/#framework-dependent-deployments-fdd).</span></span> <span data-ttu-id="5c661-128">Pokud hostuje FDD, zkontrolujte, že je modul runtime nainstalovaný v části **programy & funkce** nebo **aplikace & funkce**.</span><span class="sxs-lookup"><span data-stu-id="5c661-128">If hosting an FDD, confirm that the runtime is installed in **Programs & Features** or **Apps & features**.</span></span> <span data-ttu-id="5c661-129">Pokud je nutný konkrétní modul runtime, Stáhněte si modul runtime z [archivu stahování v rozhraní .NET](https://dotnet.microsoft.com/download/archives) a nainstalujte ho do systému.</span><span class="sxs-lookup"><span data-stu-id="5c661-129">If a specific runtime is required, download the runtime from the [.NET Download Archives](https://dotnet.microsoft.com/download/archives) and install it on the system.</span></span> <span data-ttu-id="5c661-130">Po instalaci modulu runtime restartujte systém nebo restartujte službu IIS spuštěním příkazu **net stop** , který následuje po příkazu **net start w3svc** z příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="5c661-130">After installing the runtime, restart the system or restart IIS by executing **net stop was /y** followed by **net start w3svc** from a command prompt.</span></span>

## <a name="os-upgrade-removed-the-32-bit-aspnet-core-module"></a><span data-ttu-id="5c661-131">Upgrade operačního systému odebral modul 32 ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5c661-131">OS upgrade removed the 32-bit ASP.NET Core Module</span></span>

<span data-ttu-id="5c661-132">**Protokol aplikace:** Načtení knihovny DLL modulu **C:\WINDOWS\system32\inetsrv\aspnetcore.dll** se nezdařilo.</span><span class="sxs-lookup"><span data-stu-id="5c661-132">**Application Log:** The Module DLL **C:\WINDOWS\system32\inetsrv\aspnetcore.dll** failed to load.</span></span> <span data-ttu-id="5c661-133">Tato data jsou chybná.</span><span class="sxs-lookup"><span data-stu-id="5c661-133">The data is the error.</span></span>

<span data-ttu-id="5c661-134">Řešení potíží:</span><span class="sxs-lookup"><span data-stu-id="5c661-134">Troubleshooting:</span></span>

<span data-ttu-id="5c661-135">Soubory jiného typu než operační systém v adresáři **C:\Windows\SysWOW64\inetsrv** nejsou zachovány během upgradu operačního systému.</span><span class="sxs-lookup"><span data-stu-id="5c661-135">Non-OS files in the **C:\Windows\SysWOW64\inetsrv** directory aren't preserved during an OS upgrade.</span></span> <span data-ttu-id="5c661-136">Pokud je modul ASP.NET Core nainstalován před upgradem operačního systému a potom po upgradu operačního systému běží libovolný fond aplikací v 32ovém režimu, dojde k tomuto problému.</span><span class="sxs-lookup"><span data-stu-id="5c661-136">If the ASP.NET Core Module is installed prior to an OS upgrade and then any app pool is run in 32-bit mode after an OS upgrade, this issue is encountered.</span></span> <span data-ttu-id="5c661-137">Po upgradu operačního systému opravte modul ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5c661-137">After an OS upgrade, repair the ASP.NET Core Module.</span></span> <span data-ttu-id="5c661-138">Viz [instalace hostující sady .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span><span class="sxs-lookup"><span data-stu-id="5c661-138">See [Install the .NET Core Hosting bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span> <span data-ttu-id="5c661-139">Po spuštění instalačního programu vyberte možnost **opravit** .</span><span class="sxs-lookup"><span data-stu-id="5c661-139">Select **Repair** when the installer is run.</span></span>

## <a name="missing-site-extension-32-bit-x86-and-64-bit-x64-site-extensions-installed-or-wrong-process-bitness-set"></a><span data-ttu-id="5c661-140">Chybí rozšíření webového serveru, 32 (x86) a 64-bit (x64) rozšíření webu nebo nesprávná bitová verze sada procesů.</span><span class="sxs-lookup"><span data-stu-id="5c661-140">Missing site extension, 32-bit (x86) and 64-bit (x64) site extensions installed, or wrong process bitness set</span></span>

<span data-ttu-id="5c661-141">*Platí pro aplikace hostované v Azure App Services.*</span><span class="sxs-lookup"><span data-stu-id="5c661-141">*Applies to apps hosted by Azure App Services.*</span></span>

* <span data-ttu-id="5c661-142">**Prohlížeee** Chyba protokolu HTTP 500,0 – selhání načtení obslužné rutiny ANCM v procesu</span><span class="sxs-lookup"><span data-stu-id="5c661-142">**Browser:** HTTP Error 500.0 - ANCM In-Process Handler Load Failure</span></span>

* <span data-ttu-id="5c661-143">**Protokol aplikace:** Vyvolání hostfxr k nalezení obslužné rutiny žádosti o zpracování se nezdařilo bez nalezení nativních závislostí.</span><span class="sxs-lookup"><span data-stu-id="5c661-143">**Application Log:** Invoking hostfxr to find the inprocess request handler failed without finding any native dependencies.</span></span> <span data-ttu-id="5c661-144">Nepovedlo se najít obslužnou rutinu žádosti o zpracování.</span><span class="sxs-lookup"><span data-stu-id="5c661-144">Could not find inprocess request handler.</span></span> <span data-ttu-id="5c661-145">Zachycený výstup z vyvolání hostfxr: Nebylo možné najít žádnou kompatibilní verzi rozhraní.</span><span class="sxs-lookup"><span data-stu-id="5c661-145">Captured output from invoking hostfxr: It was not possible to find any compatible framework version.</span></span> <span data-ttu-id="5c661-146">Zadané rozhraní Microsoft. AspNetCore. app ', verze {Version}-Preview-\*' nebylo nalezeno.</span><span class="sxs-lookup"><span data-stu-id="5c661-146">The specified framework 'Microsoft.AspNetCore.App', version '{VERSION}-preview-\*' was not found.</span></span> <span data-ttu-id="5c661-147">Nepodařilo se spustit aplikaci '/LM/W3SVC/1416782824/ROOT ', ErrorCode ' 0x8000FFFF '.</span><span class="sxs-lookup"><span data-stu-id="5c661-147">Failed to start application '/LM/W3SVC/1416782824/ROOT', ErrorCode '0x8000ffff'.</span></span>

* <span data-ttu-id="5c661-148">**Protokol stdout modulu ASP.NET Core:** Nebylo možné najít žádnou kompatibilní verzi rozhraní.</span><span class="sxs-lookup"><span data-stu-id="5c661-148">**ASP.NET Core Module stdout Log:** It was not possible to find any compatible framework version.</span></span> <span data-ttu-id="5c661-149">Zadané rozhraní Microsoft. AspNetCore. app ', verze {Version}-Preview-\*' nebylo nalezeno.</span><span class="sxs-lookup"><span data-stu-id="5c661-149">The specified framework 'Microsoft.AspNetCore.App', version '{VERSION}-preview-\*' was not found.</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="5c661-150">**Protokol ladění ASP.NET Coreho modulu:** Vyvolání hostfxr k nalezení obslužné rutiny žádosti o zpracování se nezdařilo bez nalezení nativních závislostí.</span><span class="sxs-lookup"><span data-stu-id="5c661-150">**ASP.NET Core Module Debug Log:** Invoking hostfxr to find the inprocess request handler failed without finding any native dependencies.</span></span> <span data-ttu-id="5c661-151">To pravděpodobně znamená, že aplikace je nesprávně nakonfigurovaná. Zkontrolujte prosím verze Microsoft. NetCore. app a Microsoft. AspNetCore. app, na které cílí aplikace a které jsou nainstalované v počítači.</span><span class="sxs-lookup"><span data-stu-id="5c661-151">This most likely means the app is misconfigured, please check the versions of Microsoft.NetCore.App and Microsoft.AspNetCore.App that are targeted by the application and are installed on the machine.</span></span> <span data-ttu-id="5c661-152">Vrátila se neúspěšná hodnota HRESULT: 0x8000ffff.</span><span class="sxs-lookup"><span data-stu-id="5c661-152">Failed HRESULT returned: 0x8000ffff.</span></span> <span data-ttu-id="5c661-153">Nepovedlo se najít obslužnou rutinu žádosti o zpracování.</span><span class="sxs-lookup"><span data-stu-id="5c661-153">Could not find inprocess request handler.</span></span> <span data-ttu-id="5c661-154">Nebylo možné najít žádnou kompatibilní verzi rozhraní.</span><span class="sxs-lookup"><span data-stu-id="5c661-154">It was not possible to find any compatible framework version.</span></span> <span data-ttu-id="5c661-155">Zadané rozhraní Microsoft. AspNetCore. app ', verze {Version}-Preview-\*' nebylo nalezeno.</span><span class="sxs-lookup"><span data-stu-id="5c661-155">The specified framework 'Microsoft.AspNetCore.App', version '{VERSION}-preview-\*' was not found.</span></span>

::: moniker-end

<span data-ttu-id="5c661-156">Řešení potíží:</span><span class="sxs-lookup"><span data-stu-id="5c661-156">Troubleshooting:</span></span>

* <span data-ttu-id="5c661-157">Pokud spouštíte aplikaci ve verzi Preview, nainstalujte buď rozšíření 32 (x86) **nebo** 64-bit (x64), které odpovídá bitová verze aplikace a verzi modulu runtime aplikace.</span><span class="sxs-lookup"><span data-stu-id="5c661-157">If running the app on a preview runtime, install either the 32-bit (x86) **or** 64-bit (x64) site extension that matches the bitness of the app and the app's runtime version.</span></span> <span data-ttu-id="5c661-158">**Neinstalujte obě rozšíření nebo více verzí modulu runtime rozšíření.**</span><span class="sxs-lookup"><span data-stu-id="5c661-158">**Don't install both extensions or multiple runtime versions of the extension.**</span></span>

  * <span data-ttu-id="5c661-159">Modul runtime verze ASP.NET Core {RUNTIME} (x86)</span><span class="sxs-lookup"><span data-stu-id="5c661-159">ASP.NET Core {RUNTIME VERSION} (x86) Runtime</span></span>
  * <span data-ttu-id="5c661-160">Modul runtime verze ASP.NET Core {RUNTIME} (x64)</span><span class="sxs-lookup"><span data-stu-id="5c661-160">ASP.NET Core {RUNTIME VERSION} (x64) Runtime</span></span>

  <span data-ttu-id="5c661-161">Restartujte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="5c661-161">Restart the app.</span></span> <span data-ttu-id="5c661-162">Počkejte několik sekund, než se aplikace restartuje.</span><span class="sxs-lookup"><span data-stu-id="5c661-162">Wait several seconds for the app to restart.</span></span>

* <span data-ttu-id="5c661-163">Pokud je aplikace spuštěná ve verzi Preview a nainstalují se [rozšíření lokality](xref:host-and-deploy/azure-apps/index#install-the-preview-site-extension) 32 (x86) i 64 (x64), odinstalujte rozšíření webu, které se neshoduje s bitová verze aplikace.</span><span class="sxs-lookup"><span data-stu-id="5c661-163">If running the app on a preview runtime and both the 32-bit (x86) and 64-bit (x64) [site extensions](xref:host-and-deploy/azure-apps/index#install-the-preview-site-extension) are installed, uninstall the site extension that doesn't match the bitness of the app.</span></span> <span data-ttu-id="5c661-164">Po odebrání rozšíření lokality aplikaci restartujte.</span><span class="sxs-lookup"><span data-stu-id="5c661-164">After removing the site extension, restart the app.</span></span> <span data-ttu-id="5c661-165">Počkejte několik sekund, než se aplikace restartuje.</span><span class="sxs-lookup"><span data-stu-id="5c661-165">Wait several seconds for the app to restart.</span></span>

* <span data-ttu-id="5c661-166">Pokud je aplikace spuštěná ve verzi Preview a bitová verze rozšíření lokality odpovídá této aplikaci, zkontrolujte, že *verze modulu runtime* rozšíření lokality Preview odpovídá verzi modulu runtime aplikace.</span><span class="sxs-lookup"><span data-stu-id="5c661-166">If running the app on a preview runtime and the site extension's bitness matches that of the app, confirm that the preview site extension's *runtime version* matches the app's runtime version.</span></span>

* <span data-ttu-id="5c661-167">Ověřte, že **platforma** aplikace v **nastavení aplikace** odpovídá bitová verze aplikace.</span><span class="sxs-lookup"><span data-stu-id="5c661-167">Confirm that the app's **Platform** in **Application Settings** matches the bitness of the app.</span></span>

<span data-ttu-id="5c661-168">Další informace naleznete v tématu <xref:host-and-deploy/azure-apps/index#install-the-preview-site-extension>.</span><span class="sxs-lookup"><span data-stu-id="5c661-168">For more information, see <xref:host-and-deploy/azure-apps/index#install-the-preview-site-extension>.</span></span>

## <a name="an-x86-app-is-deployed-but-the-app-pool-isnt-enabled-for-32-bit-apps"></a><span data-ttu-id="5c661-169">Je nasazená aplikace x86, ale fond aplikací není povolený pro 32 aplikace.</span><span class="sxs-lookup"><span data-stu-id="5c661-169">An x86 app is deployed but the app pool isn't enabled for 32-bit apps</span></span>

* <span data-ttu-id="5c661-170">**Prohlížeee** Chyba protokolu HTTP 500,30 – Chyba spuštění ANCM v procesu</span><span class="sxs-lookup"><span data-stu-id="5c661-170">**Browser:** HTTP Error 500.30 - ANCM In-Process Start Failure</span></span>

* <span data-ttu-id="5c661-171">**Protokol aplikace:** U aplikace "/LM/W3SVC/5/ROOT" s fyzickým kořenovým adresářem {PATH} se dosáhlo neočekávané spravované výjimky, kód výjimky = "0xe0434352".</span><span class="sxs-lookup"><span data-stu-id="5c661-171">**Application Log:** Application '/LM/W3SVC/5/ROOT' with physical root '{PATH}' hit unexpected managed exception, exception code = '0xe0434352'.</span></span> <span data-ttu-id="5c661-172">Další informace najdete v protokolech stderr.</span><span class="sxs-lookup"><span data-stu-id="5c661-172">Please check the stderr logs for more information.</span></span> <span data-ttu-id="5c661-173">Aplikaci '/LM/W3SVC/5/ROOT ' s fyzickým kořenem ' {PATH} ' se nepodařilo načíst modul CLR a spravovanou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="5c661-173">Application '/LM/W3SVC/5/ROOT' with physical root '{PATH}' failed to load clr and managed application.</span></span> <span data-ttu-id="5c661-174">Pracovní vlákno CLR byl předčasně ukončeno.</span><span class="sxs-lookup"><span data-stu-id="5c661-174">CLR worker thread exited prematurely</span></span>

* <span data-ttu-id="5c661-175">**Protokol stdout modulu ASP.NET Core:** Soubor protokolu je vytvořen, ale je prázdný.</span><span class="sxs-lookup"><span data-stu-id="5c661-175">**ASP.NET Core Module stdout Log:** The log file is created but empty.</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="5c661-176">**Protokol ladění ASP.NET Coreho modulu:** Vrátila se neúspěšná hodnota HRESULT: 0x8007023e</span><span class="sxs-lookup"><span data-stu-id="5c661-176">**ASP.NET Core Module Debug Log:** Failed HRESULT returned: 0x8007023e</span></span>

::: moniker-end

<span data-ttu-id="5c661-177">Tento scénář je při publikování samostatně obsažené aplikace zachycen sadou SDK.</span><span class="sxs-lookup"><span data-stu-id="5c661-177">This scenario is trapped by the SDK when publishing a self-contained app.</span></span> <span data-ttu-id="5c661-178">Sada SDK vytvoří chybu, pokud identifikátor RID neodpovídá cílové platformě (například `win10-x64` `<PlatformTarget>x86</PlatformTarget>` identifikátor RID v souboru projektu).</span><span class="sxs-lookup"><span data-stu-id="5c661-178">The SDK produces an error if the RID doesn't match the platform target (for example, `win10-x64` RID with `<PlatformTarget>x86</PlatformTarget>` in the project file).</span></span>

<span data-ttu-id="5c661-179">Řešení potíží:</span><span class="sxs-lookup"><span data-stu-id="5c661-179">Troubleshooting:</span></span>

<span data-ttu-id="5c661-180">Pro nasazení závislé na architektuře x86 (`<PlatformTarget>x86</PlatformTarget>`) Povolte fond aplikací IIS pro 32 aplikace.</span><span class="sxs-lookup"><span data-stu-id="5c661-180">For an x86 framework-dependent deployment (`<PlatformTarget>x86</PlatformTarget>`), enable the IIS app pool for 32-bit apps.</span></span> <span data-ttu-id="5c661-181">Ve Správci služby IIS otevřete **Rozšířené nastavení** fondu aplikací a nastavte **možnost Povolit 32-bitové aplikace** na **hodnotu true**.</span><span class="sxs-lookup"><span data-stu-id="5c661-181">In IIS Manager, open the app pool's **Advanced Settings** and set **Enable 32-Bit Applications** to **True**.</span></span>

## <a name="platform-conflicts-with-rid"></a><span data-ttu-id="5c661-182">Konflikty platformy s identifikátorem RID</span><span class="sxs-lookup"><span data-stu-id="5c661-182">Platform conflicts with RID</span></span>

* <span data-ttu-id="5c661-183">**Prohlížeee** Chyba protokolu HTTP 502,5 – chyba procesu</span><span class="sxs-lookup"><span data-stu-id="5c661-183">**Browser:** HTTP Error 502.5 - Process Failure</span></span>

* <span data-ttu-id="5c661-184">**Protokol aplikace:** Nepodařilo se spustit proces pro aplikaci Machine/Webroot/apphost/{Assembly} s fyzickým\{kořenovým\' adresářem c: cesta} pomocí příkazu CommandLine '\{"C: cesta} {Assembly}. { exe | dll} "', ErrorCode = ' 0x80004005: FF.</span><span class="sxs-lookup"><span data-stu-id="5c661-184">**Application Log:** Application 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' with physical root 'C:\{PATH}\' failed to start process with commandline '"C:\{PATH}{ASSEMBLY}.{exe|dll}" ', ErrorCode = '0x80004005 : ff.</span></span>

* <span data-ttu-id="5c661-185">**Protokol stdout modulu ASP.NET Core:** Neošetřená výjimka: System.BadImageFormatException: Nepovedlo se načíst soubor nebo sestavení {ASSEMBLY}. dll.</span><span class="sxs-lookup"><span data-stu-id="5c661-185">**ASP.NET Core Module stdout Log:** Unhandled Exception: System.BadImageFormatException: Could not load file or assembly '{ASSEMBLY}.dll'.</span></span> <span data-ttu-id="5c661-186">Byl proveden pokus o načtení programu v nesprávném formátu.</span><span class="sxs-lookup"><span data-stu-id="5c661-186">An attempt was made to load a program with an incorrect format.</span></span>

<span data-ttu-id="5c661-187">Řešení potíží:</span><span class="sxs-lookup"><span data-stu-id="5c661-187">Troubleshooting:</span></span>

* <span data-ttu-id="5c661-188">Ověřte, že se aplikace spouští místně na Kestrel.</span><span class="sxs-lookup"><span data-stu-id="5c661-188">Confirm that the app runs locally on Kestrel.</span></span> <span data-ttu-id="5c661-189">Selhání procesu může být výsledkem problému v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="5c661-189">A process failure might be the result of a problem within the app.</span></span> <span data-ttu-id="5c661-190">Další informace naleznete v tématu <xref:test/troubleshoot-azure-iis>.</span><span class="sxs-lookup"><span data-stu-id="5c661-190">For more information, see <xref:test/troubleshoot-azure-iis>.</span></span>

* <span data-ttu-id="5c661-191">Pokud k této výjimce dojde pro nasazení aplikací Azure při upgradu aplikace a nasazení novějších sestavení, odstraňte ručně všechny soubory z předchozího nasazení.</span><span class="sxs-lookup"><span data-stu-id="5c661-191">If this exception occurs for an Azure Apps deployment when upgrading an app and deploying newer assemblies, manually delete all files from the prior deployment.</span></span> <span data-ttu-id="5c661-192">Při zaznamenání nekompatibilních sestavení `System.BadImageFormatException` může dojít k výjimce při nasazení inovované aplikace.</span><span class="sxs-lookup"><span data-stu-id="5c661-192">Lingering incompatible assemblies can result in a `System.BadImageFormatException` exception when deploying an upgraded app.</span></span>

## <a name="uri-endpoint-wrong-or-stopped-website"></a><span data-ttu-id="5c661-193">Koncový bod identifikátoru URI je chybný nebo zastavený Web.</span><span class="sxs-lookup"><span data-stu-id="5c661-193">URI endpoint wrong or stopped website</span></span>

* <span data-ttu-id="5c661-194">**Prohlížeee** ERR_CONNECTION_REFUSED **--nebo--** nelze se připojit</span><span class="sxs-lookup"><span data-stu-id="5c661-194">**Browser:** ERR_CONNECTION_REFUSED **--OR--** Unable to connect</span></span>

* <span data-ttu-id="5c661-195">**Protokol aplikace:** Žádná položka</span><span class="sxs-lookup"><span data-stu-id="5c661-195">**Application Log:** No entry</span></span>

* <span data-ttu-id="5c661-196">**Protokol stdout modulu ASP.NET Core:** Soubor protokolu není vytvořen.</span><span class="sxs-lookup"><span data-stu-id="5c661-196">**ASP.NET Core Module stdout Log:** The log file isn't created.</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="5c661-197">**Protokol ladění ASP.NET Coreho modulu:** Soubor protokolu není vytvořen.</span><span class="sxs-lookup"><span data-stu-id="5c661-197">**ASP.NET Core Module Debug Log:** The log file isn't created.</span></span>

::: moniker-end

<span data-ttu-id="5c661-198">Řešení potíží:</span><span class="sxs-lookup"><span data-stu-id="5c661-198">Troubleshooting:</span></span>

* <span data-ttu-id="5c661-199">Potvrďte, že se pro aplikaci používá správný koncový bod identifikátoru URI.</span><span class="sxs-lookup"><span data-stu-id="5c661-199">Confirm the correct URI endpoint for the app is in use.</span></span> <span data-ttu-id="5c661-200">Ověřte vazby.</span><span class="sxs-lookup"><span data-stu-id="5c661-200">Check the bindings.</span></span>

* <span data-ttu-id="5c661-201">Ověřte, že web IIS není ve stavu *Zastaveno* .</span><span class="sxs-lookup"><span data-stu-id="5c661-201">Confirm that the IIS website isn't in the *Stopped* state.</span></span>

## <a name="corewebengine-or-w3svc-server-features-disabled"></a><span data-ttu-id="5c661-202">Funkce serveru CoreWebEngine nebo W3SVC jsou zakázané.</span><span class="sxs-lookup"><span data-stu-id="5c661-202">CoreWebEngine or W3SVC server features disabled</span></span>

<span data-ttu-id="5c661-203">**Výjimka OS:** Aby bylo možné používat modul ASP.NET Core, musí být nainstalovány funkce IIS 7,0 CoreWebEngine a W3SVC.</span><span class="sxs-lookup"><span data-stu-id="5c661-203">**OS Exception:** The IIS 7.0 CoreWebEngine and W3SVC features must be installed to use the ASP.NET Core Module.</span></span>

<span data-ttu-id="5c661-204">Řešení potíží:</span><span class="sxs-lookup"><span data-stu-id="5c661-204">Troubleshooting:</span></span>

<span data-ttu-id="5c661-205">Zkontrolujte, jestli jsou povolené správné role a funkce.</span><span class="sxs-lookup"><span data-stu-id="5c661-205">Confirm that the proper role and features are enabled.</span></span> <span data-ttu-id="5c661-206">Viz [Konfigurace služby IIS](xref:host-and-deploy/iis/index#iis-configuration).</span><span class="sxs-lookup"><span data-stu-id="5c661-206">See [IIS Configuration](xref:host-and-deploy/iis/index#iis-configuration).</span></span>

## <a name="incorrect-website-physical-path-or-app-missing"></a><span data-ttu-id="5c661-207">Chybí nesprávná fyzická cesta nebo aplikace na webu.</span><span class="sxs-lookup"><span data-stu-id="5c661-207">Incorrect website physical path or app missing</span></span>

* <span data-ttu-id="5c661-208">**Prohlížeee** 403 zakázán-přístup byl odepřen **--nebo--** 403,14 zakázán – webový server je nakonfigurován tak, aby neobsahoval seznam obsahu tohoto adresáře.</span><span class="sxs-lookup"><span data-stu-id="5c661-208">**Browser:** 403 Forbidden - Access is denied **--OR--** 403.14 Forbidden - The Web server is configured to not list the contents of this directory.</span></span>

* <span data-ttu-id="5c661-209">**Protokol aplikace:** Žádná položka</span><span class="sxs-lookup"><span data-stu-id="5c661-209">**Application Log:** No entry</span></span>

* <span data-ttu-id="5c661-210">**Protokol stdout modulu ASP.NET Core:** Soubor protokolu není vytvořen.</span><span class="sxs-lookup"><span data-stu-id="5c661-210">**ASP.NET Core Module stdout Log:** The log file isn't created.</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="5c661-211">**Protokol ladění ASP.NET Coreho modulu:** Soubor protokolu není vytvořen.</span><span class="sxs-lookup"><span data-stu-id="5c661-211">**ASP.NET Core Module Debug Log:** The log file isn't created.</span></span>

::: moniker-end

<span data-ttu-id="5c661-212">Řešení potíží:</span><span class="sxs-lookup"><span data-stu-id="5c661-212">Troubleshooting:</span></span>

<span data-ttu-id="5c661-213">Ověřte **základní nastavení** webu IIS a složku fyzické aplikace.</span><span class="sxs-lookup"><span data-stu-id="5c661-213">Check the IIS website **Basic Settings** and the physical app folder.</span></span> <span data-ttu-id="5c661-214">Ověřte, že je aplikace ve složce na **fyzické cestě**webu IIS.</span><span class="sxs-lookup"><span data-stu-id="5c661-214">Confirm that the app is in the folder at the IIS website **Physical path**.</span></span>

## <a name="incorrect-role-aspnet-core-module-not-installed-or-incorrect-permissions"></a><span data-ttu-id="5c661-215">Nesprávná role, ASP.NET Core modul není nainstalovaný nebo má nesprávná oprávnění.</span><span class="sxs-lookup"><span data-stu-id="5c661-215">Incorrect role, ASP.NET Core Module not installed, or incorrect permissions</span></span>

* <span data-ttu-id="5c661-216">**Prohlížeee** 500,19 interní chyba serveru – k požadované stránce nelze přistupovat, protože související konfigurační data stránky jsou neplatná.</span><span class="sxs-lookup"><span data-stu-id="5c661-216">**Browser:** 500.19 Internal Server Error - The requested page cannot be accessed because the related configuration data for the page is invalid.</span></span> <span data-ttu-id="5c661-217">**--Nebo--** Tuto stránku nejde zobrazit.</span><span class="sxs-lookup"><span data-stu-id="5c661-217">**--OR--** This page can't be displayed</span></span>

* <span data-ttu-id="5c661-218">**Protokol aplikace:** Žádná položka</span><span class="sxs-lookup"><span data-stu-id="5c661-218">**Application Log:** No entry</span></span>

* <span data-ttu-id="5c661-219">**Protokol stdout modulu ASP.NET Core:** Soubor protokolu není vytvořen.</span><span class="sxs-lookup"><span data-stu-id="5c661-219">**ASP.NET Core Module stdout Log:** The log file isn't created.</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="5c661-220">**Protokol ladění ASP.NET Coreho modulu:** Soubor protokolu není vytvořen.</span><span class="sxs-lookup"><span data-stu-id="5c661-220">**ASP.NET Core Module Debug Log:** The log file isn't created.</span></span>

::: moniker-end

<span data-ttu-id="5c661-221">Řešení potíží:</span><span class="sxs-lookup"><span data-stu-id="5c661-221">Troubleshooting:</span></span>

* <span data-ttu-id="5c661-222">Potvrďte, že je povolená správná role.</span><span class="sxs-lookup"><span data-stu-id="5c661-222">Confirm that the proper role is enabled.</span></span> <span data-ttu-id="5c661-223">Viz [Konfigurace služby IIS](xref:host-and-deploy/iis/index#iis-configuration).</span><span class="sxs-lookup"><span data-stu-id="5c661-223">See [IIS Configuration](xref:host-and-deploy/iis/index#iis-configuration).</span></span>

* <span data-ttu-id="5c661-224">Otevřete **programy & funkce** nebo **aplikace & funkce** a potvrďte, že je nainstalované **hostování Windows serveru** .</span><span class="sxs-lookup"><span data-stu-id="5c661-224">Open **Programs & Features** or **Apps & features** and confirm that **Windows Server Hosting** is installed.</span></span> <span data-ttu-id="5c661-225">Pokud se v seznamu nainstalovaných programů nenachází **hostování Windows serveru** , Stáhněte a nainstalujte hostující sadu .NET Core.</span><span class="sxs-lookup"><span data-stu-id="5c661-225">If **Windows Server Hosting** isn't present in the list of installed programs, download and install the .NET Core Hosting Bundle.</span></span>

  [<span data-ttu-id="5c661-226">Aktuální instalační program sady hostování rozhraní .NET Core (s přímým přístupem ke stažení)</span><span class="sxs-lookup"><span data-stu-id="5c661-226">Current .NET Core Hosting Bundle installer (direct download)</span></span>](https://www.microsoft.com/net/permalink/dotnetcore-current-windows-runtime-bundle-installer)

  <span data-ttu-id="5c661-227">Další informace najdete v tématu [instalace hostující sady .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span><span class="sxs-lookup"><span data-stu-id="5c661-227">For more information, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

* <span data-ttu-id="5c661-228">Ujistěte se, že**Identita** **modelu** > procesu **fondu** > aplikací je nastavená na **ApplicationPoolIdentity** nebo že vlastní identita má správná oprávnění pro přístup ke složce pro nasazení aplikace.</span><span class="sxs-lookup"><span data-stu-id="5c661-228">Make sure that the **Application Pool** > **Process Model** > **Identity** is set to **ApplicationPoolIdentity** or the custom identity has the correct permissions to access the app's deployment folder.</span></span>

* <span data-ttu-id="5c661-229">Pokud jste odinstalovali sadu hostující sadu ASP.NET Core a nainstalovali jste starší verzi hostujícího balíčku, soubor *ApplicationHost. config* neobsahuje oddíl pro modul ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5c661-229">If you uninstalled the ASP.NET Core Hosting Bundle and installed an earlier version of the hosting bundle, the *applicationHost.config* file doesn't include a section for the ASP.NET Core Module.</span></span> <span data-ttu-id="5c661-230">Otevřete soubor *ApplicationHost. config* v umístění *% windir%/system32/Inetsrv/config* `<configuration><configSections><sectionGroup name="system.webServer">` a vyhledejte skupinu oddílů.</span><span class="sxs-lookup"><span data-stu-id="5c661-230">Open *applicationHost.config* at *%windir%/System32/inetsrv/config* and find the `<configuration><configSections><sectionGroup name="system.webServer">` section group.</span></span> <span data-ttu-id="5c661-231">Pokud ve skupině oddílů chybí oddíl ASP.NET Core modulu, přidejte element section:</span><span class="sxs-lookup"><span data-stu-id="5c661-231">If the section for the ASP.NET Core Module is missing from the section group, add the section element:</span></span>

  ```xml
  <section name="aspNetCore" overrideModeDefault="Allow" />
  ```

  <span data-ttu-id="5c661-232">Případně nainstalujte nejnovější verzi sady ASP.NET Core hostující sada.</span><span class="sxs-lookup"><span data-stu-id="5c661-232">Alternatively, install the latest version of the ASP.NET Core Hosting Bundle.</span></span> <span data-ttu-id="5c661-233">Nejnovější verze je zpětně kompatibilní s podporovanými ASP.NET Core aplikacemi.</span><span class="sxs-lookup"><span data-stu-id="5c661-233">The latest version is backwards-compatible with supported ASP.NET Core apps.</span></span>

## <a name="incorrect-processpath-missing-path-variable-hosting-bundle-not-installed-systemiis-not-restarted-vc-redistributable-not-installed-or-dotnetexe-access-violation"></a><span data-ttu-id="5c661-234">Nesprávná processPath, chybějící Proměnná cesty, hostující sada není nainstalovaná, systém/IIS se nerestartuje, VC + + Redistributable není nainstalovaný nebo došlo k narušení přístupu dotnet. exe.</span><span class="sxs-lookup"><span data-stu-id="5c661-234">Incorrect processPath, missing PATH variable, Hosting Bundle not installed, system/IIS not restarted, VC++ Redistributable not installed, or dotnet.exe access violation</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="5c661-235">**Prohlížeee** Chyba protokolu HTTP 500,0 – selhání načtení obslužné rutiny ANCM v procesu</span><span class="sxs-lookup"><span data-stu-id="5c661-235">**Browser:** HTTP Error 500.0 - ANCM In-Process Handler Load Failure</span></span>

* <span data-ttu-id="5c661-236">**Protokol aplikace:** Nepovedlo se spustit proces pomocí příkazového řádku {...} aplikace Machine/\{Webroot/\' apphost/{Assembly} s fyzickým kořenem C: Path}.</span><span class="sxs-lookup"><span data-stu-id="5c661-236">**Application Log:** Application 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' with physical root 'C:\{PATH}\' failed to start process with commandline '"{...}"</span></span> <span data-ttu-id="5c661-237">', ErrorCode = ' 0x80070002: 0.</span><span class="sxs-lookup"><span data-stu-id="5c661-237">', ErrorCode = '0x80070002 : 0.</span></span> <span data-ttu-id="5c661-238">Aplikaci {PATH} se nepodařilo spustit.</span><span class="sxs-lookup"><span data-stu-id="5c661-238">Application '{PATH}' wasn't able to start.</span></span> <span data-ttu-id="5c661-239">Spustitelný soubor se nenašel v cestě {PATH}.</span><span class="sxs-lookup"><span data-stu-id="5c661-239">Executable was not found at '{PATH}'.</span></span> <span data-ttu-id="5c661-240">Nepodařilo se spustit aplikaci '/LM/W3SVC/2/ROOT ', ErrorCode ' 0x8007023e '.</span><span class="sxs-lookup"><span data-stu-id="5c661-240">Failed to start application '/LM/W3SVC/2/ROOT', ErrorCode '0x8007023e'.</span></span>

* <span data-ttu-id="5c661-241">**Protokol stdout modulu ASP.NET Core:** Soubor protokolu není vytvořen.</span><span class="sxs-lookup"><span data-stu-id="5c661-241">**ASP.NET Core Module stdout Log:** The log file isn't created.</span></span>

* <span data-ttu-id="5c661-242">**Protokol ladění ASP.NET Coreho modulu:** Protokol událostí: Aplikaci {PATH} se nepodařilo spustit.</span><span class="sxs-lookup"><span data-stu-id="5c661-242">**ASP.NET Core Module Debug Log:** Event Log: 'Application '{PATH}' wasn't able to start.</span></span> <span data-ttu-id="5c661-243">Spustitelný soubor se nenašel v cestě {PATH}.</span><span class="sxs-lookup"><span data-stu-id="5c661-243">Executable was not found at '{PATH}'.</span></span> <span data-ttu-id="5c661-244">Vrátila se neúspěšná hodnota HRESULT: 0x8007023e</span><span class="sxs-lookup"><span data-stu-id="5c661-244">Failed HRESULT returned: 0x8007023e</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* <span data-ttu-id="5c661-245">**Prohlížeee** Chyba protokolu HTTP 502,5 – chyba procesu</span><span class="sxs-lookup"><span data-stu-id="5c661-245">**Browser:** HTTP Error 502.5 - Process Failure</span></span>

* <span data-ttu-id="5c661-246">**Protokol aplikace:** Nepovedlo se spustit proces pomocí příkazového řádku {...} aplikace Machine/\{Webroot/\' apphost/{Assembly} s fyzickým kořenem C: Path}.</span><span class="sxs-lookup"><span data-stu-id="5c661-246">**Application Log:** Application 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' with physical root 'C:\{PATH}\' failed to start process with commandline '"{...}"</span></span> <span data-ttu-id="5c661-247">', ErrorCode = ' 0x80070002: 0.</span><span class="sxs-lookup"><span data-stu-id="5c661-247">', ErrorCode = '0x80070002 : 0.</span></span>

* <span data-ttu-id="5c661-248">**Protokol stdout modulu ASP.NET Core:** Soubor protokolu je vytvořen, ale je prázdný.</span><span class="sxs-lookup"><span data-stu-id="5c661-248">**ASP.NET Core Module stdout Log:** The log file is created but empty.</span></span>

::: moniker-end

<span data-ttu-id="5c661-249">Řešení potíží:</span><span class="sxs-lookup"><span data-stu-id="5c661-249">Troubleshooting:</span></span>

* <span data-ttu-id="5c661-250">Ověřte, že se aplikace spouští místně na Kestrel.</span><span class="sxs-lookup"><span data-stu-id="5c661-250">Confirm that the app runs locally on Kestrel.</span></span> <span data-ttu-id="5c661-251">Selhání procesu může být výsledkem problému v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="5c661-251">A process failure might be the result of a problem within the app.</span></span> <span data-ttu-id="5c661-252">Další informace naleznete v tématu <xref:test/troubleshoot-azure-iis>.</span><span class="sxs-lookup"><span data-stu-id="5c661-252">For more information, see <xref:test/troubleshoot-azure-iis>.</span></span>

* <span data-ttu-id="5c661-253">Zkontrolujte `<aspNetCore>` atribut *processPath* v elementu v *souboru Web. config* a potvrďte, že `dotnet` je pro nasazení závislé na rozhraní (FDD) nebo `.\{ASSEMBLY}.exe` pro samostatné [nasazení (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span><span class="sxs-lookup"><span data-stu-id="5c661-253">Check the *processPath* attribute on the `<aspNetCore>` element in *web.config* to confirm that it's `dotnet` for a framework-dependent deployment (FDD) or `.\{ASSEMBLY}.exe` for a [self-contained deployment (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>

* <span data-ttu-id="5c661-254">V případě FDD nemusí být příkaz *dotnet. exe* přístupný prostřednictvím nastavení cesty.</span><span class="sxs-lookup"><span data-stu-id="5c661-254">For an FDD, *dotnet.exe* might not be accessible via the PATH settings.</span></span> <span data-ttu-id="5c661-255">Ověřte, že v nastavení systémové cesty existuje *C:\Program Files\dotnet\\*  .</span><span class="sxs-lookup"><span data-stu-id="5c661-255">Confirm that *C:\Program Files\dotnet\\* exists in the System PATH settings.</span></span>

* <span data-ttu-id="5c661-256">Pro FDD nemusí být příkaz *dotnet. exe* dostupný pro identitu uživatele fondu aplikací.</span><span class="sxs-lookup"><span data-stu-id="5c661-256">For an FDD, *dotnet.exe* might not be accessible for the user identity of the app pool.</span></span> <span data-ttu-id="5c661-257">Potvrďte, že identita uživatele fondu aplikací má přístup k adresáři *C:\Program Files\dotnet* .</span><span class="sxs-lookup"><span data-stu-id="5c661-257">Confirm that the app pool user identity has access to the *C:\Program Files\dotnet* directory.</span></span> <span data-ttu-id="5c661-258">Ověřte, že v adresáři *C:\Program Files\dotnet* a App Directory nejsou nakonfigurovaná žádná pravidla odepření pro identitu uživatele fondu aplikací.</span><span class="sxs-lookup"><span data-stu-id="5c661-258">Confirm that there are no deny rules configured for the app pool user identity on the *C:\Program Files\dotnet* and app directories.</span></span>

* <span data-ttu-id="5c661-259">FDD mohla být nasazena a .NET Core nainstalována bez restartování služby IIS.</span><span class="sxs-lookup"><span data-stu-id="5c661-259">An FDD may have been deployed and .NET Core installed without restarting IIS.</span></span> <span data-ttu-id="5c661-260">Buď restartujte server, nebo restartujte službu IIS spuštěním příkazu **net stop** , který následuje po příkazu **net start w3svc** z příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="5c661-260">Either restart the server or restart IIS by executing **net stop was /y** followed by **net start w3svc** from a command prompt.</span></span>

* <span data-ttu-id="5c661-261">FDD mohla být nasazena bez instalace modulu runtime .NET Core v hostitelském systému.</span><span class="sxs-lookup"><span data-stu-id="5c661-261">An FDD may have been deployed without installing the .NET Core runtime on the hosting system.</span></span> <span data-ttu-id="5c661-262">Pokud modul runtime .NET Core není nainstalovaný, spusťte v systému **instalační program sady hostitelských svazků .NET Core** .</span><span class="sxs-lookup"><span data-stu-id="5c661-262">If the .NET Core runtime hasn't been installed, run the **.NET Core Hosting Bundle installer** on the system.</span></span>

  [<span data-ttu-id="5c661-263">Aktuální instalační program sady hostování rozhraní .NET Core (s přímým přístupem ke stažení)</span><span class="sxs-lookup"><span data-stu-id="5c661-263">Current .NET Core Hosting Bundle installer (direct download)</span></span>](https://www.microsoft.com/net/permalink/dotnetcore-current-windows-runtime-bundle-installer)

  <span data-ttu-id="5c661-264">Další informace najdete v tématu [instalace hostující sady .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span><span class="sxs-lookup"><span data-stu-id="5c661-264">For more information, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

  <span data-ttu-id="5c661-265">Pokud je nutný konkrétní modul runtime, Stáhněte si modul runtime z [archivu stahování v rozhraní .NET](https://dotnet.microsoft.com/download/archives) a nainstalujte ho do systému.</span><span class="sxs-lookup"><span data-stu-id="5c661-265">If a specific runtime is required, download the runtime from the [.NET Download Archives](https://dotnet.microsoft.com/download/archives) and install it on the system.</span></span> <span data-ttu-id="5c661-266">Dokončete instalaci restartováním systému nebo restartováním služby IIS spuštěním příkazu **net stop** , který následuje po příkazu **net start w3svc** z příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="5c661-266">Complete the installation by restarting the system or restarting IIS by executing **net stop was /y** followed by **net start w3svc** from a command prompt.</span></span>

* <span data-ttu-id="5c661-267">Mohla být nasazena FDD a *Microsoft Visual C++ 2015 Redistributable (x64)* není v systému nainstalován.</span><span class="sxs-lookup"><span data-stu-id="5c661-267">An FDD may have been deployed and the *Microsoft Visual C++ 2015 Redistributable (x64)* isn't installed on the system.</span></span> <span data-ttu-id="5c661-268">Získejte instalační program z [webu Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=53840).</span><span class="sxs-lookup"><span data-stu-id="5c661-268">Obtain an installer from the [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=53840).</span></span>

## <a name="incorrect-arguments-of-aspnetcore-element"></a><span data-ttu-id="5c661-269">Nesprávné argumenty \<prvku aspNetCore ></span><span class="sxs-lookup"><span data-stu-id="5c661-269">Incorrect arguments of \<aspNetCore> element</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="5c661-270">**Prohlížeee** Chyba protokolu HTTP 500,0 – selhání načtení obslužné rutiny ANCM v procesu</span><span class="sxs-lookup"><span data-stu-id="5c661-270">**Browser:** HTTP Error 500.0 - ANCM In-Process Handler Load Failure</span></span>

* <span data-ttu-id="5c661-271">**Protokol aplikace:** Vyvolání hostfxr k nalezení obslužné rutiny žádosti o zpracování se nezdařilo bez nalezení nativních závislostí.</span><span class="sxs-lookup"><span data-stu-id="5c661-271">**Application Log:** Invoking hostfxr to find the inprocess request handler failed without finding any native dependencies.</span></span> <span data-ttu-id="5c661-272">To pravděpodobně znamená, že aplikace je nesprávně nakonfigurovaná. Zkontrolujte prosím verze Microsoft. NetCore. app a Microsoft. AspNetCore. app, na které cílí aplikace a které jsou nainstalované v počítači.</span><span class="sxs-lookup"><span data-stu-id="5c661-272">This most likely means the app is misconfigured, please check the versions of Microsoft.NetCore.App and Microsoft.AspNetCore.App that are targeted by the application and are installed on the machine.</span></span> <span data-ttu-id="5c661-273">Nepovedlo se najít obslužnou rutinu žádosti o zpracování.</span><span class="sxs-lookup"><span data-stu-id="5c661-273">Could not find inprocess request handler.</span></span> <span data-ttu-id="5c661-274">Zachycený výstup z vyvolání hostfxr: Měli jste v úmyslu spustit příkazy sady dotnet SDK?</span><span class="sxs-lookup"><span data-stu-id="5c661-274">Captured output from invoking hostfxr: Did you mean to run dotnet SDK commands?</span></span> <span data-ttu-id="5c661-275">Nainstalujte prosím sadu dotnet SDK z: https://go.microsoft.com/fwlink/?LinkID=798306&clcid=0x409 Nepodařilo se spustit aplikaci '/LM/W3SVC/3/ROOT ', ErrorCode ' 0x8000FFFF '.</span><span class="sxs-lookup"><span data-stu-id="5c661-275">Please install dotnet SDK from: https://go.microsoft.com/fwlink/?LinkID=798306&clcid=0x409 Failed to start application '/LM/W3SVC/3/ROOT', ErrorCode '0x8000ffff'.</span></span>

* <span data-ttu-id="5c661-276">**Protokol stdout modulu ASP.NET Core:** Měli jste v úmyslu spustit příkazy sady dotnet SDK?</span><span class="sxs-lookup"><span data-stu-id="5c661-276">**ASP.NET Core Module stdout Log:** Did you mean to run dotnet SDK commands?</span></span> <span data-ttu-id="5c661-277">Nainstalujte prosím sadu dotnet SDK z: https://go.microsoft.com/fwlink/?LinkID=798306&clcid=0x409</span><span class="sxs-lookup"><span data-stu-id="5c661-277">Please install dotnet SDK from: https://go.microsoft.com/fwlink/?LinkID=798306&clcid=0x409</span></span>

* <span data-ttu-id="5c661-278">**Protokol ladění ASP.NET Coreho modulu:** Vyvolání hostfxr k nalezení obslužné rutiny žádosti o zpracování se nezdařilo bez nalezení nativních závislostí.</span><span class="sxs-lookup"><span data-stu-id="5c661-278">**ASP.NET Core Module Debug Log:** Invoking hostfxr to find the inprocess request handler failed without finding any native dependencies.</span></span> <span data-ttu-id="5c661-279">To pravděpodobně znamená, že aplikace je nesprávně nakonfigurovaná. Zkontrolujte prosím verze Microsoft. NetCore. app a Microsoft. AspNetCore. app, na které cílí aplikace a které jsou nainstalované v počítači.</span><span class="sxs-lookup"><span data-stu-id="5c661-279">This most likely means the app is misconfigured, please check the versions of Microsoft.NetCore.App and Microsoft.AspNetCore.App that are targeted by the application and are installed on the machine.</span></span> <span data-ttu-id="5c661-280">Vrátila se neúspěšná hodnota HRESULT: 0x8000FFFF nemůže najít obslužnou rutinu žádosti o zpracování.</span><span class="sxs-lookup"><span data-stu-id="5c661-280">Failed HRESULT returned: 0x8000ffff Could not find inprocess request handler.</span></span> <span data-ttu-id="5c661-281">Zachycený výstup z vyvolání hostfxr: Měli jste v úmyslu spustit příkazy sady dotnet SDK?</span><span class="sxs-lookup"><span data-stu-id="5c661-281">Captured output from invoking hostfxr: Did you mean to run dotnet SDK commands?</span></span> <span data-ttu-id="5c661-282">Nainstalujte prosím sadu dotnet SDK z: https://go.microsoft.com/fwlink/?LinkID=798306&clcid=0x409 Vrátila se neúspěšná hodnota HRESULT: 0x8000ffff</span><span class="sxs-lookup"><span data-stu-id="5c661-282">Please install dotnet SDK from: https://go.microsoft.com/fwlink/?LinkID=798306&clcid=0x409 Failed HRESULT returned: 0x8000ffff</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* <span data-ttu-id="5c661-283">**Prohlížeee** Chyba protokolu HTTP 502,5 – chyba procesu</span><span class="sxs-lookup"><span data-stu-id="5c661-283">**Browser:** HTTP Error 502.5 - Process Failure</span></span>

* <span data-ttu-id="5c661-284">**Protokol aplikace:** Nepodařilo se spustit proces pomocí příkazového řádku dotnet aplikace Machine/Webroot/apphost/{Assembly\{} s\' fyzickým kořenovým adresářem C: Path}.\{ SESTAVENÍ}. dll, ErrorCode = ' 0x80004005: 80008081.</span><span class="sxs-lookup"><span data-stu-id="5c661-284">**Application Log:** Application 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' with physical root 'C:\{PATH}\' failed to start process with commandline '"dotnet" .\{ASSEMBLY}.dll', ErrorCode = '0x80004005 : 80008081.</span></span>

* <span data-ttu-id="5c661-285">**Protokol stdout modulu ASP.NET Core:** Aplikace, která má být spuštěna, neexistuje: Cesta\{Assembly}. dll</span><span class="sxs-lookup"><span data-stu-id="5c661-285">**ASP.NET Core Module stdout Log:** The application to execute does not exist: 'PATH\{ASSEMBLY}.dll'</span></span>

::: moniker-end

<span data-ttu-id="5c661-286">Řešení potíží:</span><span class="sxs-lookup"><span data-stu-id="5c661-286">Troubleshooting:</span></span>

* <span data-ttu-id="5c661-287">Ověřte, že se aplikace spouští místně na Kestrel.</span><span class="sxs-lookup"><span data-stu-id="5c661-287">Confirm that the app runs locally on Kestrel.</span></span> <span data-ttu-id="5c661-288">Selhání procesu může být výsledkem problému v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="5c661-288">A process failure might be the result of a problem within the app.</span></span> <span data-ttu-id="5c661-289">Další informace naleznete v tématu <xref:test/troubleshoot-azure-iis>.</span><span class="sxs-lookup"><span data-stu-id="5c661-289">For more information, see <xref:test/troubleshoot-azure-iis>.</span></span>

* <span data-ttu-id="5c661-290">Zkontrolujte atribut *arguments* u `<aspNetCore>` elementu v *souboru Web. config* a potvrďte tak, že je buď ( `.\{ASSEMBLY}.dll` a) pro nasazení závislé na rozhraní (FDD); nebo (b) není k dispozici, prázdný`arguments=""`řetězec () nebo seznam argumenty aplikace (`arguments="{ARGUMENT_1}, {ARGUMENT_2}, ... {ARGUMENT_X}"`) pro samostatně uzavřené nasazení (SCD).</span><span class="sxs-lookup"><span data-stu-id="5c661-290">Examine the *arguments* attribute on the `<aspNetCore>` element in *web.config* to confirm that it's either (a) `.\{ASSEMBLY}.dll` for a framework-dependent deployment (FDD); or (b) not present, an empty string (`arguments=""`), or a list of the app's arguments (`arguments="{ARGUMENT_1}, {ARGUMENT_2}, ... {ARGUMENT_X}"`) for a self-contained deployment (SCD).</span></span>

::: moniker range=">= aspnetcore-2.2"

## <a name="missing-net-core-shared-framework"></a><span data-ttu-id="5c661-291">Chybějící sdílené rozhraní .NET Core</span><span class="sxs-lookup"><span data-stu-id="5c661-291">Missing .NET Core shared framework</span></span>

* <span data-ttu-id="5c661-292">**Prohlížeee** Chyba protokolu HTTP 500,0 – selhání načtení obslužné rutiny ANCM v procesu</span><span class="sxs-lookup"><span data-stu-id="5c661-292">**Browser:** HTTP Error 500.0 - ANCM In-Process Handler Load Failure</span></span>

* <span data-ttu-id="5c661-293">**Protokol aplikace:** Vyvolání hostfxr k nalezení obslužné rutiny žádosti o zpracování se nezdařilo bez nalezení nativních závislostí.</span><span class="sxs-lookup"><span data-stu-id="5c661-293">**Application Log:** Invoking hostfxr to find the inprocess request handler failed without finding any native dependencies.</span></span> <span data-ttu-id="5c661-294">To pravděpodobně znamená, že aplikace je nesprávně nakonfigurovaná. Zkontrolujte prosím verze Microsoft. NetCore. app a Microsoft. AspNetCore. app, na které cílí aplikace a které jsou nainstalované v počítači.</span><span class="sxs-lookup"><span data-stu-id="5c661-294">This most likely means the app is misconfigured, please check the versions of Microsoft.NetCore.App and Microsoft.AspNetCore.App that are targeted by the application and are installed on the machine.</span></span> <span data-ttu-id="5c661-295">Nepovedlo se najít obslužnou rutinu žádosti o zpracování.</span><span class="sxs-lookup"><span data-stu-id="5c661-295">Could not find inprocess request handler.</span></span> <span data-ttu-id="5c661-296">Zachycený výstup z vyvolání hostfxr: Nebylo možné najít žádnou kompatibilní verzi rozhraní.</span><span class="sxs-lookup"><span data-stu-id="5c661-296">Captured output from invoking hostfxr: It was not possible to find any compatible framework version.</span></span> <span data-ttu-id="5c661-297">Zadané rozhraní Microsoft. AspNetCore. app, verze {VERSION} se nenašlo.</span><span class="sxs-lookup"><span data-stu-id="5c661-297">The specified framework 'Microsoft.AspNetCore.App', version '{VERSION}' was not found.</span></span>

<span data-ttu-id="5c661-298">Nepodařilo se spustit aplikaci '/LM/W3SVC/5/ROOT ', ErrorCode ' 0x8000FFFF '.</span><span class="sxs-lookup"><span data-stu-id="5c661-298">Failed to start application '/LM/W3SVC/5/ROOT', ErrorCode '0x8000ffff'.</span></span>

* <span data-ttu-id="5c661-299">**Protokol stdout modulu ASP.NET Core:** Nebylo možné najít žádnou kompatibilní verzi rozhraní.</span><span class="sxs-lookup"><span data-stu-id="5c661-299">**ASP.NET Core Module stdout Log:** It was not possible to find any compatible framework version.</span></span> <span data-ttu-id="5c661-300">Zadané rozhraní Microsoft. AspNetCore. app, verze {VERSION} se nenašlo.</span><span class="sxs-lookup"><span data-stu-id="5c661-300">The specified framework 'Microsoft.AspNetCore.App', version '{VERSION}' was not found.</span></span>

* <span data-ttu-id="5c661-301">**Protokol ladění ASP.NET Coreho modulu:** Vrátila se neúspěšná hodnota HRESULT: 0x8000ffff</span><span class="sxs-lookup"><span data-stu-id="5c661-301">**ASP.NET Core Module Debug Log:** Failed HRESULT returned: 0x8000ffff</span></span>

::: moniker-end

<span data-ttu-id="5c661-302">Řešení potíží:</span><span class="sxs-lookup"><span data-stu-id="5c661-302">Troubleshooting:</span></span>

<span data-ttu-id="5c661-303">Pro nasazení závislé na rozhraní (FDD) Zkontrolujte, že je v systému nainstalovaný správný modul runtime.</span><span class="sxs-lookup"><span data-stu-id="5c661-303">For a framework-dependent deployment (FDD), confirm that the correct runtime installed on the system.</span></span>

## <a name="stopped-application-pool"></a><span data-ttu-id="5c661-304">Fond aplikací se zastavil.</span><span class="sxs-lookup"><span data-stu-id="5c661-304">Stopped Application Pool</span></span>

* <span data-ttu-id="5c661-305">**Prohlížeee** Služba 503 není dostupná.</span><span class="sxs-lookup"><span data-stu-id="5c661-305">**Browser:** 503 Service Unavailable</span></span>

* <span data-ttu-id="5c661-306">**Protokol aplikace:** Žádná položka</span><span class="sxs-lookup"><span data-stu-id="5c661-306">**Application Log:** No entry</span></span>

* <span data-ttu-id="5c661-307">**Protokol stdout modulu ASP.NET Core:** Soubor protokolu není vytvořen.</span><span class="sxs-lookup"><span data-stu-id="5c661-307">**ASP.NET Core Module stdout Log:** The log file isn't created.</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="5c661-308">**Protokol ladění ASP.NET Coreho modulu:** Soubor protokolu není vytvořen.</span><span class="sxs-lookup"><span data-stu-id="5c661-308">**ASP.NET Core Module Debug Log:** The log file isn't created.</span></span>

::: moniker-end

<span data-ttu-id="5c661-309">Řešení potíží:</span><span class="sxs-lookup"><span data-stu-id="5c661-309">Troubleshooting:</span></span>

<span data-ttu-id="5c661-310">Ověřte, že fond aplikací není v *zastaveném* stavu.</span><span class="sxs-lookup"><span data-stu-id="5c661-310">Confirm that the Application Pool isn't in the *Stopped* state.</span></span>

## <a name="sub-application-includes-a-handlers-section"></a><span data-ttu-id="5c661-311">Dílčí aplikace obsahuje \<> oddíl obslužných rutin.</span><span class="sxs-lookup"><span data-stu-id="5c661-311">Sub-application includes a \<handlers> section</span></span>

* <span data-ttu-id="5c661-312">**Prohlížeee** Chyba protokolu HTTP 500,19 – interní chyba serveru</span><span class="sxs-lookup"><span data-stu-id="5c661-312">**Browser:** HTTP Error 500.19 - Internal Server Error</span></span>

* <span data-ttu-id="5c661-313">**Protokol aplikace:** Žádná položka</span><span class="sxs-lookup"><span data-stu-id="5c661-313">**Application Log:** No entry</span></span>

* <span data-ttu-id="5c661-314">**Protokol stdout modulu ASP.NET Core:** Vytvoří se soubor protokolu kořenové aplikace a zobrazí se normální operace.</span><span class="sxs-lookup"><span data-stu-id="5c661-314">**ASP.NET Core Module stdout Log:** The root app's log file is created and shows normal operation.</span></span> <span data-ttu-id="5c661-315">Soubor protokolu dílčí aplikace se nevytvoří.</span><span class="sxs-lookup"><span data-stu-id="5c661-315">The sub-app's log file isn't created.</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="5c661-316">**Protokol ladění ASP.NET Coreho modulu:** Vytvoří se soubor protokolu kořenové aplikace a zobrazí se normální operace.</span><span class="sxs-lookup"><span data-stu-id="5c661-316">**ASP.NET Core Module Debug Log:** The root app's log file is created and shows normal operation.</span></span> <span data-ttu-id="5c661-317">Soubor protokolu dílčí aplikace se nevytvoří.</span><span class="sxs-lookup"><span data-stu-id="5c661-317">The sub-app's log file isn't created.</span></span>

::: moniker-end

<span data-ttu-id="5c661-318">Řešení potíží:</span><span class="sxs-lookup"><span data-stu-id="5c661-318">Troubleshooting:</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="5c661-319">Potvrďte, že soubor `<handlers>` *Web. config* dílčí aplikace neobsahuje oddíl nebo že dílčí aplikace nedědí obslužné rutiny nadřazené aplikace.</span><span class="sxs-lookup"><span data-stu-id="5c661-319">Confirm that the sub-app's *web.config* file doesn't include a `<handlers>` section or that the sub-app doesn't inherit the parent app's handlers.</span></span>

<span data-ttu-id="5c661-320">`<system.webServer>` Oddíl v souboru *Web. config* nadřazené aplikace je `<location>` umístěn uvnitř elementu.</span><span class="sxs-lookup"><span data-stu-id="5c661-320">The parent app's `<system.webServer>` section of *web.config* is placed inside of a `<location>` element.</span></span> <span data-ttu-id="5c661-321">Vlastnost je nastavená [ \<](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) tak, aby označovala, že nastavení zadaná v rámci umístění > elementu nejsou děděna aplikacemi, které se nacházejí v podadresáři nadřazené aplikace. `false` <xref:System.Configuration.SectionInformation.InheritInChildApplications*></span><span class="sxs-lookup"><span data-stu-id="5c661-321">The <xref:System.Configuration.SectionInformation.InheritInChildApplications*> property is set to `false` to indicate that the settings specified within the [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) element aren't inherited by apps that reside in a subdirectory of the parent app.</span></span> <span data-ttu-id="5c661-322">Další informace naleznete v tématu <xref:host-and-deploy/aspnet-core-module>.</span><span class="sxs-lookup"><span data-stu-id="5c661-322">For more information, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="5c661-323">Potvrďte, že soubor *Web. config* dílčí aplikace neobsahuje `<handlers>` oddíl.</span><span class="sxs-lookup"><span data-stu-id="5c661-323">Confirm that the sub-app's *web.config* file doesn't include a `<handlers>` section.</span></span>

::: moniker-end

## <a name="stdout-log-path-incorrect"></a><span data-ttu-id="5c661-324">nesprávná cesta protokolu STDOUT</span><span class="sxs-lookup"><span data-stu-id="5c661-324">stdout log path incorrect</span></span>

* <span data-ttu-id="5c661-325">**Prohlížeee** Aplikace reaguje normálně.</span><span class="sxs-lookup"><span data-stu-id="5c661-325">**Browser:** The app responds normally.</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="5c661-326">**Protokol aplikace:** Nelze spustit přesměrování stdout v adresáři C:\Program Files\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll.</span><span class="sxs-lookup"><span data-stu-id="5c661-326">**Application Log:** Could not start stdout redirection in C:\Program Files\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll.</span></span> <span data-ttu-id="5c661-327">Zpráva o výjimce: HRESULT 0x80070005 vrácený v {PATH} \aspnetcoremodulev2\commonlib\fileoutputmanager.cpp: 84.</span><span class="sxs-lookup"><span data-stu-id="5c661-327">Exception message: HRESULT 0x80070005 returned at {PATH}\aspnetcoremodulev2\commonlib\fileoutputmanager.cpp:84.</span></span> <span data-ttu-id="5c661-328">Nejde zastavit přesměrování stdout v adresáři C:\Program Files\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll.</span><span class="sxs-lookup"><span data-stu-id="5c661-328">Could not stop stdout redirection in C:\Program Files\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll.</span></span> <span data-ttu-id="5c661-329">Zpráva o výjimce: HRESULT 0x80070002 vráceno v {PATH}.</span><span class="sxs-lookup"><span data-stu-id="5c661-329">Exception message: HRESULT 0x80070002 returned at {PATH}.</span></span> <span data-ttu-id="5c661-330">Nejde spustit přesměrování stdout v cestě {PATH} \aspnetcorev2_inprocess.dll.</span><span class="sxs-lookup"><span data-stu-id="5c661-330">Could not start stdout redirection in {PATH}\aspnetcorev2_inprocess.dll.</span></span>

* <span data-ttu-id="5c661-331">**Protokol stdout modulu ASP.NET Core:** Soubor protokolu není vytvořen.</span><span class="sxs-lookup"><span data-stu-id="5c661-331">**ASP.NET Core Module stdout Log:** The log file isn't created.</span></span>

* <span data-ttu-id="5c661-332">**Protokol ladění ASP.NET Coreho modulu:** Nelze spustit přesměrování stdout v adresáři C:\Program Files\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll.</span><span class="sxs-lookup"><span data-stu-id="5c661-332">**ASP.NET Core Module debug Log:** Could not start stdout redirection in C:\Program Files\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll.</span></span> <span data-ttu-id="5c661-333">Zpráva o výjimce: HRESULT 0x80070005 vrácený v {PATH} \aspnetcoremodulev2\commonlib\fileoutputmanager.cpp: 84.</span><span class="sxs-lookup"><span data-stu-id="5c661-333">Exception message: HRESULT 0x80070005 returned at {PATH}\aspnetcoremodulev2\commonlib\fileoutputmanager.cpp:84.</span></span> <span data-ttu-id="5c661-334">Nejde zastavit přesměrování stdout v adresáři C:\Program Files\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll.</span><span class="sxs-lookup"><span data-stu-id="5c661-334">Could not stop stdout redirection in C:\Program Files\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll.</span></span> <span data-ttu-id="5c661-335">Zpráva o výjimce: HRESULT 0x80070002 vráceno v {PATH}.</span><span class="sxs-lookup"><span data-stu-id="5c661-335">Exception message: HRESULT 0x80070002 returned at {PATH}.</span></span> <span data-ttu-id="5c661-336">Nejde spustit přesměrování stdout v cestě {PATH} \aspnetcorev2_inprocess.dll.</span><span class="sxs-lookup"><span data-stu-id="5c661-336">Could not start stdout redirection in {PATH}\aspnetcorev2_inprocess.dll.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* <span data-ttu-id="5c661-337">**Protokol aplikace:** Upozornění: Nepovedlo se \\vytvořit\{ stdoutLogFile? CESTA} ID \path_doesnt_exist\stdout_{PROCESS} _ {TIMESTAMP}. log, ErrorCode =-2147024893.</span><span class="sxs-lookup"><span data-stu-id="5c661-337">**Application Log:** Warning: Could not create stdoutLogFile \\?\{PATH}\path_doesnt_exist\stdout_{PROCESS ID}_{TIMESTAMP}.log, ErrorCode = -2147024893.</span></span>

* <span data-ttu-id="5c661-338">**Protokol stdout modulu ASP.NET Core:** Soubor protokolu není vytvořen.</span><span class="sxs-lookup"><span data-stu-id="5c661-338">**ASP.NET Core Module stdout Log:** The log file isn't created.</span></span>

::: moniker-end

<span data-ttu-id="5c661-339">Řešení potíží:</span><span class="sxs-lookup"><span data-stu-id="5c661-339">Troubleshooting:</span></span>

* <span data-ttu-id="5c661-340">Cesta zadaná v elementu *Web. config neexistuje.* `<aspNetCore>` `stdoutLogFile`</span><span class="sxs-lookup"><span data-stu-id="5c661-340">The `stdoutLogFile` path specified in the `<aspNetCore>` element of *web.config* doesn't exist.</span></span> <span data-ttu-id="5c661-341">Další informace najdete v tématu [ASP.NET Core modul: Vytváření a přesměrování](xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection)protokolu.</span><span class="sxs-lookup"><span data-stu-id="5c661-341">For more information, see [ASP.NET Core Module: Log creation and redirection](xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection).</span></span>

* <span data-ttu-id="5c661-342">Uživatel fondu aplikací nemá přístup pro zápis do cesty protokolu STDOUT.</span><span class="sxs-lookup"><span data-stu-id="5c661-342">The app pool user doesn't have write access to the stdout log path.</span></span>

## <a name="application-configuration-general-issue"></a><span data-ttu-id="5c661-343">Obecný problém konfigurace aplikace</span><span class="sxs-lookup"><span data-stu-id="5c661-343">Application configuration general issue</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="5c661-344">**Prohlížeee** Chyba protokolu HTTP 500,0 – Chyba při načítání obslužné rutiny ANCM v procesu **--nebo--** Chyba protokolu HTTP 500,30-ANCM v procesu spuštění při selhání</span><span class="sxs-lookup"><span data-stu-id="5c661-344">**Browser:** HTTP Error 500.0 - ANCM In-Process Handler Load Failure **--OR--** HTTP Error 500.30 - ANCM In-Process Start Failure</span></span>

* <span data-ttu-id="5c661-345">**Protokol aplikace:** Proměnná</span><span class="sxs-lookup"><span data-stu-id="5c661-345">**Application Log:** Variable</span></span>

* <span data-ttu-id="5c661-346">**Protokol stdout modulu ASP.NET Core:** Soubor protokolu se vytvoří, ale vyprázdní nebo vytvoří s normálními položkami, dokud neselže bod aplikace.</span><span class="sxs-lookup"><span data-stu-id="5c661-346">**ASP.NET Core Module stdout Log:** The log file is created but empty or created with normal entries until the point of the app failing.</span></span>

* <span data-ttu-id="5c661-347">**Protokol ladění ASP.NET Coreho modulu:** Proměnná</span><span class="sxs-lookup"><span data-stu-id="5c661-347">**ASP.NET Core Module Debug Log:** Variable</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* <span data-ttu-id="5c661-348">**Prohlížeee** Chyba protokolu HTTP 502,5 – chyba procesu</span><span class="sxs-lookup"><span data-stu-id="5c661-348">**Browser:** HTTP Error 502.5 - Process Failure</span></span>

* <span data-ttu-id="5c661-349">**Protokol aplikace:** Aplikace ' Machine/Webroot/apphost/{Assembly} ' se vytvořeným fyzickým kořenovým '\' C:\{Path} a procesem CommandLine '\{"c\{: path} Assembly}. { exe | dll} "", ale buď došlo k chybě, nebo nereagoval nebo neposlouchal na daném portu "{PORT}", ErrorCode = "{kód chyby}"</span><span class="sxs-lookup"><span data-stu-id="5c661-349">**Application Log:** Application 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' with physical root 'C:\{PATH}\' created process with commandline '"C:\{PATH}\{ASSEMBLY}.{exe|dll}" ' but either crashed or did not respond or did not listen on the given port '{PORT}', ErrorCode = '{ERROR CODE}'</span></span>

* <span data-ttu-id="5c661-350">**Protokol stdout modulu ASP.NET Core:** Soubor protokolu je vytvořen, ale je prázdný.</span><span class="sxs-lookup"><span data-stu-id="5c661-350">**ASP.NET Core Module stdout Log:** The log file is created but empty.</span></span>

::: moniker-end

<span data-ttu-id="5c661-351">Řešení potíží:</span><span class="sxs-lookup"><span data-stu-id="5c661-351">Troubleshooting:</span></span>

<span data-ttu-id="5c661-352">Proces se nepovedlo spustit, pravděpodobně v důsledku konfigurace aplikace nebo problému s programováním.</span><span class="sxs-lookup"><span data-stu-id="5c661-352">The process failed to start, most likely due to an app configuration or programming issue.</span></span>

<span data-ttu-id="5c661-353">Další informace naleznete v následujících tématech:</span><span class="sxs-lookup"><span data-stu-id="5c661-353">For more information, see the following topics:</span></span>

* <xref:test/troubleshoot-azure-iis>
* <xref:test/troubleshoot>
