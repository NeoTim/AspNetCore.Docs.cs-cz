---
title: Podpora služby IIS při vývoji v sadě Visual Studio pro ASP.NET Core
author: rick-anderson
description: Pokud používáte službu IIS v systému Windows Server, můžete zjišťovat podporu pro ladění aplikací ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/iis/development-time-iis-support
ms.openlocfilehash: d922b2eb4d00a252b3e7da2f00de8b4571359b20
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2020
ms.locfileid: "85406430"
---
# <a name="development-time-iis-support-in-visual-studio-for-aspnet-core"></a><span data-ttu-id="0d5a8-103">Podpora služby IIS při vývoji v sadě Visual Studio pro ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0d5a8-103">Development-time IIS support in Visual Studio for ASP.NET Core</span></span>

<span data-ttu-id="0d5a8-104">Od [sourabh Shirhatti](https://twitter.com/sshirhatti)</span><span class="sxs-lookup"><span data-stu-id="0d5a8-104">By [Sourabh Shirhatti](https://twitter.com/sshirhatti)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="0d5a8-105">Tento článek popisuje podporu sady [Visual Studio](https://visualstudio.microsoft.com) pro ladění ASP.NET Core aplikací běžících na Windows serveru se službou IIS.</span><span class="sxs-lookup"><span data-stu-id="0d5a8-105">This article describes [Visual Studio](https://visualstudio.microsoft.com) support for debugging ASP.NET Core apps running with IIS on Windows Server.</span></span> <span data-ttu-id="0d5a8-106">Toto téma vás provede povolením tohoto scénáře a nastavením projektu.</span><span class="sxs-lookup"><span data-stu-id="0d5a8-106">This topic walks through enabling this scenario and setting up a project.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="0d5a8-107">Požadavky</span><span class="sxs-lookup"><span data-stu-id="0d5a8-107">Prerequisites</span></span>

* [<span data-ttu-id="0d5a8-108">Visual Studio pro Windows</span><span class="sxs-lookup"><span data-stu-id="0d5a8-108">Visual Studio for Windows</span></span>](https://visualstudio.microsoft.com/downloads/)
* <span data-ttu-id="0d5a8-109">**ASP.NET a webové vývojové** úlohy</span><span class="sxs-lookup"><span data-stu-id="0d5a8-109">**ASP.NET and web development** workload</span></span>
* <span data-ttu-id="0d5a8-110">**Vývojová úloha .NET Core pro různé platformy**</span><span class="sxs-lookup"><span data-stu-id="0d5a8-110">**.NET Core cross-platform development** workload</span></span>
* <span data-ttu-id="0d5a8-111">Certifikát zabezpečení X. 509 (pro podporu protokolu HTTPS)</span><span class="sxs-lookup"><span data-stu-id="0d5a8-111">X.509 security certificate (for HTTPS support)</span></span>

## <a name="enable-iis"></a><span data-ttu-id="0d5a8-112">Povolení služby IIS</span><span class="sxs-lookup"><span data-stu-id="0d5a8-112">Enable IIS</span></span>

1. <span data-ttu-id="0d5a8-113">V systému Windows přejděte do **ovládacích panelů** > **programy** programy > **a funkce** > **zapnout nebo vypnout funkce systému Windows** (levá strana obrazovky).</span><span class="sxs-lookup"><span data-stu-id="0d5a8-113">In Windows, navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="0d5a8-114">Zaškrtněte políčko **Internetová informační služba** .</span><span class="sxs-lookup"><span data-stu-id="0d5a8-114">Select the **Internet Information Services** check box.</span></span> <span data-ttu-id="0d5a8-115">Vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="0d5a8-115">Select **OK**.</span></span>

<span data-ttu-id="0d5a8-116">Instalace služby IIS může vyžadovat restartování systému.</span><span class="sxs-lookup"><span data-stu-id="0d5a8-116">The IIS installation may require a system restart.</span></span>

## <a name="configure-iis"></a><span data-ttu-id="0d5a8-117">Konfigurace služby IIS</span><span class="sxs-lookup"><span data-stu-id="0d5a8-117">Configure IIS</span></span>

<span data-ttu-id="0d5a8-118">Služba IIS musí mít nakonfigurovaný web s následujícím:</span><span class="sxs-lookup"><span data-stu-id="0d5a8-118">IIS must have a website configured with the following:</span></span>

* <span data-ttu-id="0d5a8-119">**Název hostitele**: obvykle se používá **výchozí web** s **názvem hostitele** `localhost` .</span><span class="sxs-lookup"><span data-stu-id="0d5a8-119">**Host name**: Typically, the **Default Web Site** is used with a **Host name** of `localhost`.</span></span> <span data-ttu-id="0d5a8-120">Libovolný platný web služby IIS, který má jedinečný název hostitele, ale funguje.</span><span class="sxs-lookup"><span data-stu-id="0d5a8-120">However, any valid IIS website with a unique host name works.</span></span>
* <span data-ttu-id="0d5a8-121">**Vazba webu**</span><span class="sxs-lookup"><span data-stu-id="0d5a8-121">**Site Binding**</span></span>
  * <span data-ttu-id="0d5a8-122">Pro aplikace, které vyžadují protokol HTTPS, vytvořte vazbu na port 443 s certifikátem.</span><span class="sxs-lookup"><span data-stu-id="0d5a8-122">For apps that require HTTPS, create a binding to port 443 with a certificate.</span></span> <span data-ttu-id="0d5a8-123">Obvykle se používá **IIS Express certifikát pro vývoj** , ale jakýkoli platný certifikát funguje.</span><span class="sxs-lookup"><span data-stu-id="0d5a8-123">Typically, the **IIS Express Development Certificate** is used, but any valid certificate works.</span></span>
  * <span data-ttu-id="0d5a8-124">U aplikací, které používají protokol HTTP, potvrďte existenci vazby k odeslání 80 nebo vytvořte vazbu na port 80 pro novou lokalitu.</span><span class="sxs-lookup"><span data-stu-id="0d5a8-124">For apps that use HTTP, confirm the existence of a binding to post 80 or create a binding to port 80 for a new site.</span></span>
  * <span data-ttu-id="0d5a8-125">Použijte jednu vazbu pro HTTP nebo HTTPS.</span><span class="sxs-lookup"><span data-stu-id="0d5a8-125">Use a single binding for either HTTP or HTTPS.</span></span> <span data-ttu-id="0d5a8-126">**Vazba na oba porty HTTP i HTTPS současně není podporována.**</span><span class="sxs-lookup"><span data-stu-id="0d5a8-126">**Binding to both HTTP and HTTPS ports simultaneously isn't supported.**</span></span>

## <a name="enable-development-time-iis-support-in-visual-studio"></a><span data-ttu-id="0d5a8-127">Povolení podpory služby IIS v době vývoje v aplikaci Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0d5a8-127">Enable development-time IIS support in Visual Studio</span></span>

1. <span data-ttu-id="0d5a8-128">Spusťte instalační program sady Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="0d5a8-128">Launch the Visual Studio installer.</span></span>
1. <span data-ttu-id="0d5a8-129">Vyberte možnost **Upravit** pro instalaci sady Visual Studio, kterou chcete použít pro podporu při vývoji služby IIS.</span><span class="sxs-lookup"><span data-stu-id="0d5a8-129">Select **Modify** for the Visual Studio installation that you plan to use for IIS development-time support.</span></span>
1. <span data-ttu-id="0d5a8-130">Pro úlohu **vývoje ASP.NET a webu** vyhledejte a nainstalujte komponentu **podpory služby IIS pro vývoj** .</span><span class="sxs-lookup"><span data-stu-id="0d5a8-130">For the **ASP.NET and web development** workload, locate and install the **Development time IIS support** component.</span></span>

   <span data-ttu-id="0d5a8-131">Tato součást je uvedena v části **volitelný** oddíl v části **čas vývoje** , který je podporován na panelu **podrobností o instalaci** v napravo od úloh.</span><span class="sxs-lookup"><span data-stu-id="0d5a8-131">The component is listed in the **Optional** section under **Development time IIS support** in the **Installation details** panel to the right of the workloads.</span></span> <span data-ttu-id="0d5a8-132">Komponenta nainstaluje [modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module), což je NATIVNÍ modul IIS vyžadovaný ke spouštění ASP.NET Core aplikací se službou IIS.</span><span class="sxs-lookup"><span data-stu-id="0d5a8-132">The component installs the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), which is a native IIS module required to run ASP.NET Core apps with IIS.</span></span>

## <a name="configure-the-project"></a><span data-ttu-id="0d5a8-133">Konfigurace projektu</span><span class="sxs-lookup"><span data-stu-id="0d5a8-133">Configure the project</span></span>

### <a name="https-redirection"></a><span data-ttu-id="0d5a8-134">Přesměrování HTTPS</span><span class="sxs-lookup"><span data-stu-id="0d5a8-134">HTTPS redirection</span></span>

<span data-ttu-id="0d5a8-135">Pro nový projekt, který vyžaduje protokol HTTPS, zaškrtněte políčko pro **konfiguraci HTTPS** v okně **vytvořit novou ASP.NET Core webovou aplikaci** .</span><span class="sxs-lookup"><span data-stu-id="0d5a8-135">For a new project that requires HTTPS, select the check box to **Configure for HTTPS** in the **Create a new ASP.NET Core Web Application** window.</span></span> <span data-ttu-id="0d5a8-136">Zaškrtnutím políčka přidáte [Přesměrování protokolu HTTPS a HSTS middleware](xref:security/enforcing-ssl) do aplikace, když se vytvoří.</span><span class="sxs-lookup"><span data-stu-id="0d5a8-136">Selecting the check box adds [HTTPS Redirection and HSTS Middleware](xref:security/enforcing-ssl) to the app when it's created.</span></span>

<span data-ttu-id="0d5a8-137">U existujícího projektu, který vyžaduje HTTPS, použijte přesměrování HTTPS a middleware HSTS v `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="0d5a8-137">For an existing project that requires HTTPS, use HTTPS Redirection and HSTS Middleware in `Startup.Configure`.</span></span> <span data-ttu-id="0d5a8-138">Další informace naleznete v tématu <xref:security/enforcing-ssl>.</span><span class="sxs-lookup"><span data-stu-id="0d5a8-138">For more information, see <xref:security/enforcing-ssl>.</span></span>

<span data-ttu-id="0d5a8-139">Pro projekt, který používá protokol HTTP, [přesměrování https a middleware HSTS](xref:security/enforcing-ssl) nejsou přidány do aplikace.</span><span class="sxs-lookup"><span data-stu-id="0d5a8-139">For a project that uses HTTP, [HTTPS Redirection and HSTS Middleware](xref:security/enforcing-ssl) aren't added to the app.</span></span> <span data-ttu-id="0d5a8-140">Není nutná žádná konfigurace aplikace.</span><span class="sxs-lookup"><span data-stu-id="0d5a8-140">No app configuration is required.</span></span>

### <a name="iis-launch-profile"></a><span data-ttu-id="0d5a8-141">Profil spuštění služby IIS</span><span class="sxs-lookup"><span data-stu-id="0d5a8-141">IIS launch profile</span></span>

<span data-ttu-id="0d5a8-142">Vytvoření nového spouštěcího profilu pro přidání podpory služby IIS v době vývoje:</span><span class="sxs-lookup"><span data-stu-id="0d5a8-142">Create a new launch profile to add development-time IIS support:</span></span>

1. <span data-ttu-id="0d5a8-143">Klikněte pravým tlačítkem na projekt v **Průzkumník řešení**.</span><span class="sxs-lookup"><span data-stu-id="0d5a8-143">Right-click the project in **Solution Explorer**.</span></span> <span data-ttu-id="0d5a8-144">Vyberte **Vlastnosti**.</span><span class="sxs-lookup"><span data-stu-id="0d5a8-144">Select **Properties**.</span></span> <span data-ttu-id="0d5a8-145">Otevřete kartu **ladění** .</span><span class="sxs-lookup"><span data-stu-id="0d5a8-145">Open the **Debug** tab.</span></span>
1. <span data-ttu-id="0d5a8-146">Pro možnost **profil**vyberte tlačítko **Nový** .</span><span class="sxs-lookup"><span data-stu-id="0d5a8-146">For **Profile**, select the **New** button.</span></span> <span data-ttu-id="0d5a8-147">Pojmenujte profil "IIS" v místním okně.</span><span class="sxs-lookup"><span data-stu-id="0d5a8-147">Name the profile "IIS" in the popup window.</span></span> <span data-ttu-id="0d5a8-148">Vyberte **OK** a vytvořte profil.</span><span class="sxs-lookup"><span data-stu-id="0d5a8-148">Select **OK** to create the profile.</span></span>
1. <span data-ttu-id="0d5a8-149">V nastavení **spuštění** vyberte ze seznamu **službu IIS** .</span><span class="sxs-lookup"><span data-stu-id="0d5a8-149">For the **Launch** setting, select **IIS** from the list.</span></span>
1. <span data-ttu-id="0d5a8-150">Zaškrtněte políčko pro **spuštění prohlížeče** a zadejte adresu URL koncového bodu.</span><span class="sxs-lookup"><span data-stu-id="0d5a8-150">Select the check box for **Launch browser** and provide the endpoint URL.</span></span>

   <span data-ttu-id="0d5a8-151">Když aplikace vyžaduje HTTPS, použijte koncový bod HTTPS ( `https://` ).</span><span class="sxs-lookup"><span data-stu-id="0d5a8-151">When the app requires HTTPS, use an HTTPS endpoint (`https://`).</span></span> <span data-ttu-id="0d5a8-152">Pro protokol HTTP použijte `http://` koncový bod HTTP ().</span><span class="sxs-lookup"><span data-stu-id="0d5a8-152">For HTTP, use an HTTP (`http://`) endpoint.</span></span>

   <span data-ttu-id="0d5a8-153">Zadejte stejný název hostitele a port, protože [Konfigurace služby IIS zadaná dříve používá](#configure-iis)obvykle `localhost` .</span><span class="sxs-lookup"><span data-stu-id="0d5a8-153">Provide the same host name and port as the [IIS configuration specified earlier uses](#configure-iis), typically `localhost`.</span></span>

   <span data-ttu-id="0d5a8-154">Zadejte název aplikace na konci adresy URL.</span><span class="sxs-lookup"><span data-stu-id="0d5a8-154">Provide the name of the app at the end of the URL.</span></span>

   <span data-ttu-id="0d5a8-155">Například `https://localhost/WebApplication1` (https) nebo `http://localhost/WebApplication1` (http) jsou platné adresy URL koncového bodu.</span><span class="sxs-lookup"><span data-stu-id="0d5a8-155">For example, `https://localhost/WebApplication1` (HTTPS) or `http://localhost/WebApplication1` (HTTP) are valid endpoint URLs.</span></span>
1. <span data-ttu-id="0d5a8-156">V části **proměnné prostředí** vyberte tlačítko **Přidat** .</span><span class="sxs-lookup"><span data-stu-id="0d5a8-156">In the **Environment variables** section, select the **Add** button.</span></span> <span data-ttu-id="0d5a8-157">Zadejte proměnnou prostředí s **názvem** `ASPNETCORE_ENVIRONMENT` a **hodnotou** `Development` .</span><span class="sxs-lookup"><span data-stu-id="0d5a8-157">Provide an environment variable with a **Name** of `ASPNETCORE_ENVIRONMENT` and a **Value** of `Development`.</span></span>
1. <span data-ttu-id="0d5a8-158">V oblasti **nastavení webového serveru** nastavte **adresu URL aplikace** na stejnou hodnotu, která se používá pro adresu URL koncového bodu **spouštěcího prohlížeče** .</span><span class="sxs-lookup"><span data-stu-id="0d5a8-158">In the **Web Server Settings** area, set the **App URL** to the same value used for the **Launch browser** endpoint URL.</span></span>
1. <span data-ttu-id="0d5a8-159">Pro nastavení **modelu hostování** v aplikaci Visual Studio 2019 nebo novější vyberte možnost **výchozí** pro použití modelu hostování používaného projektem.</span><span class="sxs-lookup"><span data-stu-id="0d5a8-159">For the **Hosting Model** setting in Visual Studio 2019 or later, select **Default** to use the hosting model used by the project.</span></span> <span data-ttu-id="0d5a8-160">Pokud projekt nastaví `<AspNetCoreHostingModel>` vlastnost v jeho souboru projektu, je použita hodnota vlastnosti ( `InProcess` nebo `OutOfProcess` ).</span><span class="sxs-lookup"><span data-stu-id="0d5a8-160">If the project sets the `<AspNetCoreHostingModel>` property in its project file, the value of the property (`InProcess` or `OutOfProcess`) is used.</span></span> <span data-ttu-id="0d5a8-161">Pokud vlastnost není k dispozici, použije se výchozí hostující model aplikace, který je v procesu.</span><span class="sxs-lookup"><span data-stu-id="0d5a8-161">If the property isn't present, the default hosting model of the app is used, which is in-process.</span></span> <span data-ttu-id="0d5a8-162">Pokud aplikace vyžaduje explicitní nastavení modelu hostování, které se liší od normálního hostujícího modelu aplikace, nastavte **hostující model** na buď `In Process` nebo `Out Of Process` podle potřeby.</span><span class="sxs-lookup"><span data-stu-id="0d5a8-162">If the app requires an explicit hosting model setting different from the app's normal hosting model, set the **Hosting Model** to either `In Process` or `Out Of Process` as needed.</span></span>
1. <span data-ttu-id="0d5a8-163">Uložte profil.</span><span class="sxs-lookup"><span data-stu-id="0d5a8-163">Save the profile.</span></span>

<span data-ttu-id="0d5a8-164">Pokud nepoužíváte aplikaci Visual Studio, ručně přidejte profil spuštění do [launchSettings.js](https://json.schemastore.org/launchsettings) v souboru ve složce *Properties (vlastnosti* ).</span><span class="sxs-lookup"><span data-stu-id="0d5a8-164">When not using Visual Studio, manually add a launch profile to the [launchSettings.json](https://json.schemastore.org/launchsettings) file in the *Properties* folder.</span></span> <span data-ttu-id="0d5a8-165">Následující příklad nakonfiguruje profil pro použití protokolu HTTPS:</span><span class="sxs-lookup"><span data-stu-id="0d5a8-165">The following example configures the profile to use the HTTPS protocol:</span></span>

```json
{
  "iisSettings": {
    "windowsAuthentication": false,
    "anonymousAuthentication": true,
    "iis": {
      "applicationUrl": "https://localhost/WebApplication1",
      "sslPort": 0
    }
  },
  "profiles": {
    "IIS": {
      "commandName": "IIS",
      "launchBrowser": true,
      "launchUrl": "https://localhost/WebApplication1",
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      }
    }
  }
}
```

<span data-ttu-id="0d5a8-166">Potvrďte, `applicationUrl` že `launchUrl` koncové body a používají stejný protokol jako konfigurace vazby služby IIS, buď http, nebo HTTPS.</span><span class="sxs-lookup"><span data-stu-id="0d5a8-166">Confirm that the `applicationUrl` and `launchUrl` endpoints match and use the same protocol as the IIS binding configuration, either HTTP or HTTPS.</span></span>

## <a name="run-the-project"></a><span data-ttu-id="0d5a8-167">Spuštění projektu</span><span class="sxs-lookup"><span data-stu-id="0d5a8-167">Run the project</span></span>

<span data-ttu-id="0d5a8-168">Spusťte Visual Studio jako správce:</span><span class="sxs-lookup"><span data-stu-id="0d5a8-168">Run Visual Studio as an administrator:</span></span>

* <span data-ttu-id="0d5a8-169">Potvrďte, že rozevírací seznam konfigurace sestavení je nastavený na **ladit**.</span><span class="sxs-lookup"><span data-stu-id="0d5a8-169">Confirm that the build configuration drop-down list is set to **Debug**.</span></span>
* <span data-ttu-id="0d5a8-170">Nastavte [tlačítko Spustit ladění](/visualstudio/debugger/debugger-feature-tour) na profil **služby IIS** a kliknutím na tlačítko aplikaci spusťte.</span><span class="sxs-lookup"><span data-stu-id="0d5a8-170">Set the [Start Debugging button](/visualstudio/debugger/debugger-feature-tour) to the **IIS** profile and select the button to start the app.</span></span>

<span data-ttu-id="0d5a8-171">Visual Studio se může vyzvat k restartování, pokud není spuštěný jako správce.</span><span class="sxs-lookup"><span data-stu-id="0d5a8-171">Visual Studio may prompt a restart if not running as an administrator.</span></span> <span data-ttu-id="0d5a8-172">Pokud se zobrazí výzva, restartujte Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="0d5a8-172">If prompted, restart Visual Studio.</span></span>

<span data-ttu-id="0d5a8-173">Pokud se používá nedůvěryhodný vývojový certifikát, prohlížeč může vyžadovat, abyste vytvořili výjimku pro nedůvěryhodný certifikát.</span><span class="sxs-lookup"><span data-stu-id="0d5a8-173">If an untrusted development certificate is used, the browser may require you to create an exception for the untrusted certificate.</span></span>

> [!NOTE]
> <span data-ttu-id="0d5a8-174">Ladění konfigurace sestavení pro vydání pomocí [pouze můj kód](/visualstudio/debugger/just-my-code) a optimalizace kompilátoru mají za následek zhoršené prostředí.</span><span class="sxs-lookup"><span data-stu-id="0d5a8-174">Debugging a Release build configuration with [Just My Code](/visualstudio/debugger/just-my-code) and compiler optimizations results in a degraded experience.</span></span> <span data-ttu-id="0d5a8-175">Například body přerušení nejsou k dispozice.</span><span class="sxs-lookup"><span data-stu-id="0d5a8-175">For example, break points aren't hit.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="0d5a8-176">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="0d5a8-176">Additional resources</span></span>

* [<span data-ttu-id="0d5a8-177">Začínáme ve službě IIS pomocí Správce služby IIS</span><span class="sxs-lookup"><span data-stu-id="0d5a8-177">Getting Started with the IIS Manager in IIS</span></span>](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* <xref:security/enforcing-ssl>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="0d5a8-178">Tento článek popisuje podporu sady [Visual Studio](https://visualstudio.microsoft.com) pro ladění ASP.NET Core aplikací běžících na Windows serveru se službou IIS.</span><span class="sxs-lookup"><span data-stu-id="0d5a8-178">This article describes [Visual Studio](https://visualstudio.microsoft.com) support for debugging ASP.NET Core apps running with IIS on Windows Server.</span></span> <span data-ttu-id="0d5a8-179">Toto téma vás provede povolením tohoto scénáře a nastavením projektu.</span><span class="sxs-lookup"><span data-stu-id="0d5a8-179">This topic walks through enabling this scenario and setting up a project.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="0d5a8-180">Požadavky</span><span class="sxs-lookup"><span data-stu-id="0d5a8-180">Prerequisites</span></span>

* [<span data-ttu-id="0d5a8-181">Visual Studio pro Windows</span><span class="sxs-lookup"><span data-stu-id="0d5a8-181">Visual Studio for Windows</span></span>](https://visualstudio.microsoft.com/downloads/)
* <span data-ttu-id="0d5a8-182">**ASP.NET a webové vývojové** úlohy</span><span class="sxs-lookup"><span data-stu-id="0d5a8-182">**ASP.NET and web development** workload</span></span>
* <span data-ttu-id="0d5a8-183">**Vývojová úloha .NET Core pro různé platformy**</span><span class="sxs-lookup"><span data-stu-id="0d5a8-183">**.NET Core cross-platform development** workload</span></span>
* <span data-ttu-id="0d5a8-184">Certifikát zabezpečení X. 509 (pro podporu protokolu HTTPS)</span><span class="sxs-lookup"><span data-stu-id="0d5a8-184">X.509 security certificate (for HTTPS support)</span></span>

## <a name="enable-iis"></a><span data-ttu-id="0d5a8-185">Povolení služby IIS</span><span class="sxs-lookup"><span data-stu-id="0d5a8-185">Enable IIS</span></span>

1. <span data-ttu-id="0d5a8-186">V systému Windows přejděte do **ovládacích panelů** > **programy** programy > **a funkce** > **zapnout nebo vypnout funkce systému Windows** (levá strana obrazovky).</span><span class="sxs-lookup"><span data-stu-id="0d5a8-186">In Windows, navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="0d5a8-187">Zaškrtněte políčko **Internetová informační služba** .</span><span class="sxs-lookup"><span data-stu-id="0d5a8-187">Select the **Internet Information Services** check box.</span></span> <span data-ttu-id="0d5a8-188">Vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="0d5a8-188">Select **OK**.</span></span>

<span data-ttu-id="0d5a8-189">Instalace služby IIS může vyžadovat restartování systému.</span><span class="sxs-lookup"><span data-stu-id="0d5a8-189">The IIS installation may require a system restart.</span></span>

## <a name="configure-iis"></a><span data-ttu-id="0d5a8-190">Konfigurace služby IIS</span><span class="sxs-lookup"><span data-stu-id="0d5a8-190">Configure IIS</span></span>

<span data-ttu-id="0d5a8-191">Služba IIS musí mít nakonfigurovaný web s následujícím:</span><span class="sxs-lookup"><span data-stu-id="0d5a8-191">IIS must have a website configured with the following:</span></span>

* <span data-ttu-id="0d5a8-192">**Název hostitele**: obvykle se používá **výchozí web** s **názvem hostitele** `localhost` .</span><span class="sxs-lookup"><span data-stu-id="0d5a8-192">**Host name**: Typically, the **Default Web Site** is used with a **Host name** of `localhost`.</span></span> <span data-ttu-id="0d5a8-193">Libovolný platný web služby IIS, který má jedinečný název hostitele, ale funguje.</span><span class="sxs-lookup"><span data-stu-id="0d5a8-193">However, any valid IIS website with a unique host name works.</span></span>
* <span data-ttu-id="0d5a8-194">**Vazba webu**</span><span class="sxs-lookup"><span data-stu-id="0d5a8-194">**Site Binding**</span></span>
  * <span data-ttu-id="0d5a8-195">Pro aplikace, které vyžadují protokol HTTPS, vytvořte vazbu na port 443 s certifikátem.</span><span class="sxs-lookup"><span data-stu-id="0d5a8-195">For apps that require HTTPS, create a binding to port 443 with a certificate.</span></span> <span data-ttu-id="0d5a8-196">Obvykle se používá **IIS Express certifikát pro vývoj** , ale jakýkoli platný certifikát funguje.</span><span class="sxs-lookup"><span data-stu-id="0d5a8-196">Typically, the **IIS Express Development Certificate** is used, but any valid certificate works.</span></span>
  * <span data-ttu-id="0d5a8-197">U aplikací, které používají protokol HTTP, potvrďte existenci vazby k odeslání 80 nebo vytvořte vazbu na port 80 pro novou lokalitu.</span><span class="sxs-lookup"><span data-stu-id="0d5a8-197">For apps that use HTTP, confirm the existence of a binding to post 80 or create a binding to port 80 for a new site.</span></span>
  * <span data-ttu-id="0d5a8-198">Použijte jednu vazbu pro HTTP nebo HTTPS.</span><span class="sxs-lookup"><span data-stu-id="0d5a8-198">Use a single binding for either HTTP or HTTPS.</span></span> <span data-ttu-id="0d5a8-199">**Vazba na oba porty HTTP i HTTPS současně není podporována.**</span><span class="sxs-lookup"><span data-stu-id="0d5a8-199">**Binding to both HTTP and HTTPS ports simultaneously isn't supported.**</span></span>

## <a name="enable-development-time-iis-support-in-visual-studio"></a><span data-ttu-id="0d5a8-200">Povolení podpory služby IIS v době vývoje v aplikaci Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0d5a8-200">Enable development-time IIS support in Visual Studio</span></span>

1. <span data-ttu-id="0d5a8-201">Spusťte instalační program sady Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="0d5a8-201">Launch the Visual Studio installer.</span></span>
1. <span data-ttu-id="0d5a8-202">Vyberte možnost **Upravit** pro instalaci sady Visual Studio, kterou chcete použít pro podporu při vývoji služby IIS.</span><span class="sxs-lookup"><span data-stu-id="0d5a8-202">Select **Modify** for the Visual Studio installation that you plan to use for IIS development-time support.</span></span>
1. <span data-ttu-id="0d5a8-203">Pro úlohu **vývoje ASP.NET a webu** vyhledejte a nainstalujte komponentu **podpory služby IIS pro vývoj** .</span><span class="sxs-lookup"><span data-stu-id="0d5a8-203">For the **ASP.NET and web development** workload, locate and install the **Development time IIS support** component.</span></span>

   <span data-ttu-id="0d5a8-204">Tato součást je uvedena v části **volitelný** oddíl v části **čas vývoje** , který je podporován na panelu **podrobností o instalaci** v napravo od úloh.</span><span class="sxs-lookup"><span data-stu-id="0d5a8-204">The component is listed in the **Optional** section under **Development time IIS support** in the **Installation details** panel to the right of the workloads.</span></span> <span data-ttu-id="0d5a8-205">Komponenta nainstaluje [modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module), což je NATIVNÍ modul IIS vyžadovaný ke spouštění ASP.NET Core aplikací se službou IIS.</span><span class="sxs-lookup"><span data-stu-id="0d5a8-205">The component installs the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), which is a native IIS module required to run ASP.NET Core apps with IIS.</span></span>

## <a name="configure-the-project"></a><span data-ttu-id="0d5a8-206">Konfigurace projektu</span><span class="sxs-lookup"><span data-stu-id="0d5a8-206">Configure the project</span></span>

### <a name="https-redirection"></a><span data-ttu-id="0d5a8-207">Přesměrování HTTPS</span><span class="sxs-lookup"><span data-stu-id="0d5a8-207">HTTPS redirection</span></span>

<span data-ttu-id="0d5a8-208">Pro nový projekt, který vyžaduje protokol HTTPS, zaškrtněte políčko pro **konfiguraci HTTPS** v okně **vytvořit novou ASP.NET Core webovou aplikaci** .</span><span class="sxs-lookup"><span data-stu-id="0d5a8-208">For a new project that requires HTTPS, select the check box to **Configure for HTTPS** in the **Create a new ASP.NET Core Web Application** window.</span></span> <span data-ttu-id="0d5a8-209">Zaškrtnutím políčka přidáte [Přesměrování protokolu HTTPS a HSTS middleware](xref:security/enforcing-ssl) do aplikace, když se vytvoří.</span><span class="sxs-lookup"><span data-stu-id="0d5a8-209">Selecting the check box adds [HTTPS Redirection and HSTS Middleware](xref:security/enforcing-ssl) to the app when it's created.</span></span>

<span data-ttu-id="0d5a8-210">U existujícího projektu, který vyžaduje HTTPS, použijte přesměrování HTTPS a middleware HSTS v `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="0d5a8-210">For an existing project that requires HTTPS, use HTTPS Redirection and HSTS Middleware in `Startup.Configure`.</span></span> <span data-ttu-id="0d5a8-211">Další informace naleznete v tématu <xref:security/enforcing-ssl>.</span><span class="sxs-lookup"><span data-stu-id="0d5a8-211">For more information, see <xref:security/enforcing-ssl>.</span></span>

<span data-ttu-id="0d5a8-212">Pro projekt, který používá protokol HTTP, [přesměrování https a middleware HSTS](xref:security/enforcing-ssl) nejsou přidány do aplikace.</span><span class="sxs-lookup"><span data-stu-id="0d5a8-212">For a project that uses HTTP, [HTTPS Redirection and HSTS Middleware](xref:security/enforcing-ssl) aren't added to the app.</span></span> <span data-ttu-id="0d5a8-213">Není nutná žádná konfigurace aplikace.</span><span class="sxs-lookup"><span data-stu-id="0d5a8-213">No app configuration is required.</span></span>

### <a name="iis-launch-profile"></a><span data-ttu-id="0d5a8-214">Profil spuštění služby IIS</span><span class="sxs-lookup"><span data-stu-id="0d5a8-214">IIS launch profile</span></span>

<span data-ttu-id="0d5a8-215">Vytvoření nového spouštěcího profilu pro přidání podpory služby IIS v době vývoje:</span><span class="sxs-lookup"><span data-stu-id="0d5a8-215">Create a new launch profile to add development-time IIS support:</span></span>

1. <span data-ttu-id="0d5a8-216">Klikněte pravým tlačítkem na projekt v **Průzkumník řešení**.</span><span class="sxs-lookup"><span data-stu-id="0d5a8-216">Right-click the project in **Solution Explorer**.</span></span> <span data-ttu-id="0d5a8-217">Vyberte **Vlastnosti**.</span><span class="sxs-lookup"><span data-stu-id="0d5a8-217">Select **Properties**.</span></span> <span data-ttu-id="0d5a8-218">Otevřete kartu **ladění** .</span><span class="sxs-lookup"><span data-stu-id="0d5a8-218">Open the **Debug** tab.</span></span>
1. <span data-ttu-id="0d5a8-219">Pro možnost **profil**vyberte tlačítko **Nový** .</span><span class="sxs-lookup"><span data-stu-id="0d5a8-219">For **Profile**, select the **New** button.</span></span> <span data-ttu-id="0d5a8-220">Pojmenujte profil "IIS" v místním okně.</span><span class="sxs-lookup"><span data-stu-id="0d5a8-220">Name the profile "IIS" in the popup window.</span></span> <span data-ttu-id="0d5a8-221">Vyberte **OK** a vytvořte profil.</span><span class="sxs-lookup"><span data-stu-id="0d5a8-221">Select **OK** to create the profile.</span></span>
1. <span data-ttu-id="0d5a8-222">V nastavení **spuštění** vyberte ze seznamu **službu IIS** .</span><span class="sxs-lookup"><span data-stu-id="0d5a8-222">For the **Launch** setting, select **IIS** from the list.</span></span>
1. <span data-ttu-id="0d5a8-223">Zaškrtněte políčko pro **spuštění prohlížeče** a zadejte adresu URL koncového bodu.</span><span class="sxs-lookup"><span data-stu-id="0d5a8-223">Select the check box for **Launch browser** and provide the endpoint URL.</span></span>

   <span data-ttu-id="0d5a8-224">Když aplikace vyžaduje HTTPS, použijte koncový bod HTTPS ( `https://` ).</span><span class="sxs-lookup"><span data-stu-id="0d5a8-224">When the app requires HTTPS, use an HTTPS endpoint (`https://`).</span></span> <span data-ttu-id="0d5a8-225">Pro protokol HTTP použijte `http://` koncový bod HTTP ().</span><span class="sxs-lookup"><span data-stu-id="0d5a8-225">For HTTP, use an HTTP (`http://`) endpoint.</span></span>

   <span data-ttu-id="0d5a8-226">Zadejte stejný název hostitele a port, protože [Konfigurace služby IIS zadaná dříve používá](#configure-iis)obvykle `localhost` .</span><span class="sxs-lookup"><span data-stu-id="0d5a8-226">Provide the same host name and port as the [IIS configuration specified earlier uses](#configure-iis), typically `localhost`.</span></span>

   <span data-ttu-id="0d5a8-227">Zadejte název aplikace na konci adresy URL.</span><span class="sxs-lookup"><span data-stu-id="0d5a8-227">Provide the name of the app at the end of the URL.</span></span>

   <span data-ttu-id="0d5a8-228">Například `https://localhost/WebApplication1` (https) nebo `http://localhost/WebApplication1` (http) jsou platné adresy URL koncového bodu.</span><span class="sxs-lookup"><span data-stu-id="0d5a8-228">For example, `https://localhost/WebApplication1` (HTTPS) or `http://localhost/WebApplication1` (HTTP) are valid endpoint URLs.</span></span>
1. <span data-ttu-id="0d5a8-229">V části **proměnné prostředí** vyberte tlačítko **Přidat** .</span><span class="sxs-lookup"><span data-stu-id="0d5a8-229">In the **Environment variables** section, select the **Add** button.</span></span> <span data-ttu-id="0d5a8-230">Zadejte proměnnou prostředí s **názvem** `ASPNETCORE_ENVIRONMENT` a **hodnotou** `Development` .</span><span class="sxs-lookup"><span data-stu-id="0d5a8-230">Provide an environment variable with a **Name** of `ASPNETCORE_ENVIRONMENT` and a **Value** of `Development`.</span></span>
1. <span data-ttu-id="0d5a8-231">V oblasti **nastavení webového serveru** nastavte **adresu URL aplikace** na stejnou hodnotu, která se používá pro adresu URL koncového bodu **spouštěcího prohlížeče** .</span><span class="sxs-lookup"><span data-stu-id="0d5a8-231">In the **Web Server Settings** area, set the **App URL** to the same value used for the **Launch browser** endpoint URL.</span></span>
1. <span data-ttu-id="0d5a8-232">Pro nastavení **modelu hostování** v aplikaci Visual Studio 2019 nebo novější vyberte možnost **výchozí** pro použití modelu hostování používaného projektem.</span><span class="sxs-lookup"><span data-stu-id="0d5a8-232">For the **Hosting Model** setting in Visual Studio 2019 or later, select **Default** to use the hosting model used by the project.</span></span> <span data-ttu-id="0d5a8-233">Pokud projekt nastaví `<AspNetCoreHostingModel>` vlastnost v jeho souboru projektu, je použita hodnota vlastnosti ( `InProcess` nebo `OutOfProcess` ).</span><span class="sxs-lookup"><span data-stu-id="0d5a8-233">If the project sets the `<AspNetCoreHostingModel>` property in its project file, the value of the property (`InProcess` or `OutOfProcess`) is used.</span></span> <span data-ttu-id="0d5a8-234">Pokud vlastnost není k dispozici, použije se výchozí hostující model aplikace, který je mimo proces.</span><span class="sxs-lookup"><span data-stu-id="0d5a8-234">If the property isn't present, the default hosting model of the app is used, which is out-of-process.</span></span> <span data-ttu-id="0d5a8-235">Pokud aplikace vyžaduje explicitní nastavení modelu hostování, které se liší od normálního hostujícího modelu aplikace, nastavte **hostující model** na buď `In Process` nebo `Out Of Process` podle potřeby.</span><span class="sxs-lookup"><span data-stu-id="0d5a8-235">If the app requires an explicit hosting model setting different from the app's normal hosting model, set the **Hosting Model** to either `In Process` or `Out Of Process` as needed.</span></span>
1. <span data-ttu-id="0d5a8-236">Uložte profil.</span><span class="sxs-lookup"><span data-stu-id="0d5a8-236">Save the profile.</span></span>

<span data-ttu-id="0d5a8-237">Pokud nepoužíváte aplikaci Visual Studio, ručně přidejte profil spuštění do [launchSettings.js](https://json.schemastore.org/launchsettings) v souboru ve složce *Properties (vlastnosti* ).</span><span class="sxs-lookup"><span data-stu-id="0d5a8-237">When not using Visual Studio, manually add a launch profile to the [launchSettings.json](https://json.schemastore.org/launchsettings) file in the *Properties* folder.</span></span> <span data-ttu-id="0d5a8-238">Následující příklad nakonfiguruje profil pro použití protokolu HTTPS:</span><span class="sxs-lookup"><span data-stu-id="0d5a8-238">The following example configures the profile to use the HTTPS protocol:</span></span>

```json
{
  "iisSettings": {
    "windowsAuthentication": false,
    "anonymousAuthentication": true,
    "iis": {
      "applicationUrl": "https://localhost/WebApplication1",
      "sslPort": 0
    }
  },
  "profiles": {
    "IIS": {
      "commandName": "IIS",
      "launchBrowser": true,
      "launchUrl": "https://localhost/WebApplication1",
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      }
    }
  }
}
```

<span data-ttu-id="0d5a8-239">Potvrďte, `applicationUrl` že `launchUrl` koncové body a používají stejný protokol jako konfigurace vazby služby IIS, buď http, nebo HTTPS.</span><span class="sxs-lookup"><span data-stu-id="0d5a8-239">Confirm that the `applicationUrl` and `launchUrl` endpoints match and use the same protocol as the IIS binding configuration, either HTTP or HTTPS.</span></span>

## <a name="run-the-project"></a><span data-ttu-id="0d5a8-240">Spuštění projektu</span><span class="sxs-lookup"><span data-stu-id="0d5a8-240">Run the project</span></span>

<span data-ttu-id="0d5a8-241">Spusťte Visual Studio jako správce:</span><span class="sxs-lookup"><span data-stu-id="0d5a8-241">Run Visual Studio as an administrator:</span></span>

* <span data-ttu-id="0d5a8-242">Potvrďte, že rozevírací seznam konfigurace sestavení je nastavený na **ladit**.</span><span class="sxs-lookup"><span data-stu-id="0d5a8-242">Confirm that the build configuration drop-down list is set to **Debug**.</span></span>
* <span data-ttu-id="0d5a8-243">Nastavte [tlačítko Spustit ladění](/visualstudio/debugger/debugger-feature-tour) na profil **služby IIS** a kliknutím na tlačítko aplikaci spusťte.</span><span class="sxs-lookup"><span data-stu-id="0d5a8-243">Set the [Start Debugging button](/visualstudio/debugger/debugger-feature-tour) to the **IIS** profile and select the button to start the app.</span></span>

<span data-ttu-id="0d5a8-244">Visual Studio se může vyzvat k restartování, pokud není spuštěný jako správce.</span><span class="sxs-lookup"><span data-stu-id="0d5a8-244">Visual Studio may prompt a restart if not running as an administrator.</span></span> <span data-ttu-id="0d5a8-245">Pokud se zobrazí výzva, restartujte Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="0d5a8-245">If prompted, restart Visual Studio.</span></span>

<span data-ttu-id="0d5a8-246">Pokud se používá nedůvěryhodný vývojový certifikát, prohlížeč může vyžadovat, abyste vytvořili výjimku pro nedůvěryhodný certifikát.</span><span class="sxs-lookup"><span data-stu-id="0d5a8-246">If an untrusted development certificate is used, the browser may require you to create an exception for the untrusted certificate.</span></span>

> [!NOTE]
> <span data-ttu-id="0d5a8-247">Ladění konfigurace sestavení pro vydání pomocí [pouze můj kód](/visualstudio/debugger/just-my-code) a optimalizace kompilátoru mají za následek zhoršené prostředí.</span><span class="sxs-lookup"><span data-stu-id="0d5a8-247">Debugging a Release build configuration with [Just My Code](/visualstudio/debugger/just-my-code) and compiler optimizations results in a degraded experience.</span></span> <span data-ttu-id="0d5a8-248">Například body přerušení nejsou k dispozice.</span><span class="sxs-lookup"><span data-stu-id="0d5a8-248">For example, break points aren't hit.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="0d5a8-249">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="0d5a8-249">Additional resources</span></span>

* [<span data-ttu-id="0d5a8-250">Začínáme ve službě IIS pomocí Správce služby IIS</span><span class="sxs-lookup"><span data-stu-id="0d5a8-250">Getting Started with the IIS Manager in IIS</span></span>](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* <xref:security/enforcing-ssl>

::: moniker-end
