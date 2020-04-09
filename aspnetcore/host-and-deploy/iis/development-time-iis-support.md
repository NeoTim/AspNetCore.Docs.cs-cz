---
title: Podpora služby IIS při vývoji v sadě Visual Studio pro ASP.NET Core
author: rick-anderson
description: Objevte podporu pro ladění aplikací ASP.NET Core při spuštění služby IIS ve Windows Serveru.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
uid: host-and-deploy/iis/development-time-iis-support
ms.openlocfilehash: f87a1d8cf41248f14932908c0633f98a7198853f
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78664043"
---
# <a name="development-time-iis-support-in-visual-studio-for-aspnet-core"></a><span data-ttu-id="74642-103">Podpora služby IIS při vývoji v sadě Visual Studio pro ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="74642-103">Development-time IIS support in Visual Studio for ASP.NET Core</span></span>

<span data-ttu-id="74642-104">Podle [Sourabh Shirhatti](https://twitter.com/sshirhatti)</span><span class="sxs-lookup"><span data-stu-id="74642-104">By [Sourabh Shirhatti](https://twitter.com/sshirhatti)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="74642-105">Tento článek popisuje podporu [sady Visual Studio](https://visualstudio.microsoft.com) pro ladění aplikací ASP.NET Core spuštěných se službou IIS v systému Windows Server.</span><span class="sxs-lookup"><span data-stu-id="74642-105">This article describes [Visual Studio](https://visualstudio.microsoft.com) support for debugging ASP.NET Core apps running with IIS on Windows Server.</span></span> <span data-ttu-id="74642-106">Toto téma vás provede povolením tohoto scénáře a nastavením projektu.</span><span class="sxs-lookup"><span data-stu-id="74642-106">This topic walks through enabling this scenario and setting up a project.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="74642-107">Požadavky</span><span class="sxs-lookup"><span data-stu-id="74642-107">Prerequisites</span></span>

* [<span data-ttu-id="74642-108">Visual Studio pro Windows</span><span class="sxs-lookup"><span data-stu-id="74642-108">Visual Studio for Windows</span></span>](https://visualstudio.microsoft.com/downloads/)
* <span data-ttu-id="74642-109">**ASP.NET a pracovní zátěž pro vývoj webových** aplikací</span><span class="sxs-lookup"><span data-stu-id="74642-109">**ASP.NET and web development** workload</span></span>
* <span data-ttu-id="74642-110">Pracovní **vytížení napříč platformami .NET Core**</span><span class="sxs-lookup"><span data-stu-id="74642-110">**.NET Core cross-platform development** workload</span></span>
* <span data-ttu-id="74642-111">Certifikát zabezpečení X.509 (pro podporu HTTPS)</span><span class="sxs-lookup"><span data-stu-id="74642-111">X.509 security certificate (for HTTPS support)</span></span>

## <a name="enable-iis"></a><span data-ttu-id="74642-112">Povolení iis</span><span class="sxs-lookup"><span data-stu-id="74642-112">Enable IIS</span></span>

1. <span data-ttu-id="74642-113">V systému Windows přejděte na **Ovládací panely** > **Programy** > **a funkce** > **Zapněte nebo vypněte funkce systému Windows** (levá strana obrazovky).</span><span class="sxs-lookup"><span data-stu-id="74642-113">In Windows, navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="74642-114">Zaškrtněte políčko **Internetová informační služba.**</span><span class="sxs-lookup"><span data-stu-id="74642-114">Select the **Internet Information Services** check box.</span></span> <span data-ttu-id="74642-115">Vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="74642-115">Select **OK**.</span></span>

<span data-ttu-id="74642-116">Instalace služby IIS může vyžadovat restartování systému.</span><span class="sxs-lookup"><span data-stu-id="74642-116">The IIS installation may require a system restart.</span></span>

## <a name="configure-iis"></a><span data-ttu-id="74642-117">Konfigurace služby IIS</span><span class="sxs-lookup"><span data-stu-id="74642-117">Configure IIS</span></span>

<span data-ttu-id="74642-118">IIS musí mít web nakonfigurovaný s následujícími:</span><span class="sxs-lookup"><span data-stu-id="74642-118">IIS must have a website configured with the following:</span></span>

* <span data-ttu-id="74642-119">**Název** &ndash; hostitele Výchozí **web** se obvykle používá s `localhost`názvem **hostitele** aplikace .</span><span class="sxs-lookup"><span data-stu-id="74642-119">**Host name** &ndash; Typically, the **Default Web Site** is used with a **Host name** of `localhost`.</span></span> <span data-ttu-id="74642-120">Všechny platné webové stránky iis s jedinečným názvem hostitele však fungují.</span><span class="sxs-lookup"><span data-stu-id="74642-120">However, any valid IIS website with a unique host name works.</span></span>
* <span data-ttu-id="74642-121">**Vazba webu**</span><span class="sxs-lookup"><span data-stu-id="74642-121">**Site Binding**</span></span>
  * <span data-ttu-id="74642-122">Pro aplikace, které vyžadují protokol HTTPS, vytvořte vazbu na port 443 s certifikátem.</span><span class="sxs-lookup"><span data-stu-id="74642-122">For apps that require HTTPS, create a binding to port 443 with a certificate.</span></span> <span data-ttu-id="74642-123">Obvykle se používá **certifikát IIS Express Development Certificate,** ale funguje všechny platné certifikáty.</span><span class="sxs-lookup"><span data-stu-id="74642-123">Typically, the **IIS Express Development Certificate** is used, but any valid certificate works.</span></span>
  * <span data-ttu-id="74642-124">Pro aplikace, které používají protokol HTTP, potvrďte existenci vazby pro zaúčtování 80 nebo vytvořte vazbu na port 80 pro nový web.</span><span class="sxs-lookup"><span data-stu-id="74642-124">For apps that use HTTP, confirm the existence of a binding to post 80 or create a binding to port 80 for a new site.</span></span>
  * <span data-ttu-id="74642-125">Použijte jednu vazbu pro protokol HTTP nebo HTTPS.</span><span class="sxs-lookup"><span data-stu-id="74642-125">Use a single binding for either HTTP or HTTPS.</span></span> <span data-ttu-id="74642-126">**Vazba na porty HTTP i HTTPS současně není podporována.**</span><span class="sxs-lookup"><span data-stu-id="74642-126">**Binding to both HTTP and HTTPS ports simultaneously isn't supported.**</span></span>

## <a name="enable-development-time-iis-support-in-visual-studio"></a><span data-ttu-id="74642-127">Povolení podpory služby IIS v době vývoje v sadě Visual Studio</span><span class="sxs-lookup"><span data-stu-id="74642-127">Enable development-time IIS support in Visual Studio</span></span>

1. <span data-ttu-id="74642-128">Spusťte instalační program sady Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="74642-128">Launch the Visual Studio installer.</span></span>
1. <span data-ttu-id="74642-129">Vyberte **Změnit** pro instalaci sady Visual Studio, kterou chcete použít pro podporu vývoje služby IIS.</span><span class="sxs-lookup"><span data-stu-id="74642-129">Select **Modify** for the Visual Studio installation that you plan to use for IIS development-time support.</span></span>
1. <span data-ttu-id="74642-130">Pro **ASP.NET a vývoj webových** úloh vyhledejte a nainstalujte součást **podpory služby IIS pro vývoj času vývoje.**</span><span class="sxs-lookup"><span data-stu-id="74642-130">For the **ASP.NET and web development** workload, locate and install the **Development time IIS support** component.</span></span>

   <span data-ttu-id="74642-131">Součást je uvedena v části **Volitelné** **čas podpory služby IIS** v panelu **Podrobnosti instalace** vpravo od úloh.</span><span class="sxs-lookup"><span data-stu-id="74642-131">The component is listed in the **Optional** section under **Development time IIS support** in the **Installation details** panel to the right of the workloads.</span></span> <span data-ttu-id="74642-132">Součást nainstaluje [ASP.NET základní modul](xref:host-and-deploy/aspnet-core-module), což je nativní modul služby IIS potřebný ke spuštění aplikací ASP.NET Core se službou IIS.</span><span class="sxs-lookup"><span data-stu-id="74642-132">The component installs the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), which is a native IIS module required to run ASP.NET Core apps with IIS.</span></span>

## <a name="configure-the-project"></a><span data-ttu-id="74642-133">Konfigurace projektu</span><span class="sxs-lookup"><span data-stu-id="74642-133">Configure the project</span></span>

### <a name="https-redirection"></a><span data-ttu-id="74642-134">Přesměrování https</span><span class="sxs-lookup"><span data-stu-id="74642-134">HTTPS redirection</span></span>

<span data-ttu-id="74642-135">U nového projektu, který vyžaduje protokol HTTPS, zaškrtněte políčko **Konfigurovat protokol HTTPS** v okně Vytvořit nový ASP.NET základní webové **aplikace.**</span><span class="sxs-lookup"><span data-stu-id="74642-135">For a new project that requires HTTPS, select the check box to **Configure for HTTPS** in the **Create a new ASP.NET Core Web Application** window.</span></span> <span data-ttu-id="74642-136">Zaškrtnutím políčka přidáte do aplikace přesměrování [HTTPS a HSTS Middleware,](xref:security/enforcing-ssl) když se vytvoří.</span><span class="sxs-lookup"><span data-stu-id="74642-136">Selecting the check box adds [HTTPS Redirection and HSTS Middleware](xref:security/enforcing-ssl) to the app when it's created.</span></span>

<span data-ttu-id="74642-137">Pro existující projekt, který vyžaduje protokol HTTPS, použijte v `Startup.Configure`aplikaci příkazy přesměrování HTTPS a Middleware HSTS .</span><span class="sxs-lookup"><span data-stu-id="74642-137">For an existing project that requires HTTPS, use HTTPS Redirection and HSTS Middleware in `Startup.Configure`.</span></span> <span data-ttu-id="74642-138">Další informace naleznete v tématu <xref:security/enforcing-ssl>.</span><span class="sxs-lookup"><span data-stu-id="74642-138">For more information, see <xref:security/enforcing-ssl>.</span></span>

<span data-ttu-id="74642-139">Pro projekt, který používá HTTP, [https přesměrování a HSTS Middleware](xref:security/enforcing-ssl) nejsou přidány do aplikace.</span><span class="sxs-lookup"><span data-stu-id="74642-139">For a project that uses HTTP, [HTTPS Redirection and HSTS Middleware](xref:security/enforcing-ssl) aren't added to the app.</span></span> <span data-ttu-id="74642-140">Není vyžadována žádná konfigurace aplikace.</span><span class="sxs-lookup"><span data-stu-id="74642-140">No app configuration is required.</span></span>

### <a name="iis-launch-profile"></a><span data-ttu-id="74642-141">Profil spuštění iis</span><span class="sxs-lookup"><span data-stu-id="74642-141">IIS launch profile</span></span>

<span data-ttu-id="74642-142">Vytvořte nový spouštěcí profil pro přidání podpory služby IIS v době vývoje:</span><span class="sxs-lookup"><span data-stu-id="74642-142">Create a new launch profile to add development-time IIS support:</span></span>

1. <span data-ttu-id="74642-143">Klepněte pravým tlačítkem myši na projekt v **Průzkumníku řešení**.</span><span class="sxs-lookup"><span data-stu-id="74642-143">Right-click the project in **Solution Explorer**.</span></span> <span data-ttu-id="74642-144">Vyberte **vlastnosti**.</span><span class="sxs-lookup"><span data-stu-id="74642-144">Select **Properties**.</span></span> <span data-ttu-id="74642-145">Otevřete kartu **Ladění.**</span><span class="sxs-lookup"><span data-stu-id="74642-145">Open the **Debug** tab.</span></span>
1. <span data-ttu-id="74642-146">V **části Profil**vyberte tlačítko **Nový.**</span><span class="sxs-lookup"><span data-stu-id="74642-146">For **Profile**, select the **New** button.</span></span> <span data-ttu-id="74642-147">V automaticky otevíraném okně pojmenujte profil IIS.</span><span class="sxs-lookup"><span data-stu-id="74642-147">Name the profile "IIS" in the popup window.</span></span> <span data-ttu-id="74642-148">Chcete-li vytvořit profil, vyberte **OK.**</span><span class="sxs-lookup"><span data-stu-id="74642-148">Select **OK** to create the profile.</span></span>
1. <span data-ttu-id="74642-149">Pro **nastavení Spuštění** vyberte **iis** ze seznamu.</span><span class="sxs-lookup"><span data-stu-id="74642-149">For the **Launch** setting, select **IIS** from the list.</span></span>
1. <span data-ttu-id="74642-150">Zaškrtněte políčko **spustit prohlížeč** a zadejte adresu URL koncového bodu.</span><span class="sxs-lookup"><span data-stu-id="74642-150">Select the check box for **Launch browser** and provide the endpoint URL.</span></span>

   <span data-ttu-id="74642-151">Když aplikace vyžaduje HTTPS, použijte koncový`https://`bod HTTPS ( ).</span><span class="sxs-lookup"><span data-stu-id="74642-151">When the app requires HTTPS, use an HTTPS endpoint (`https://`).</span></span> <span data-ttu-id="74642-152">Pro protokol HTTP použijte`http://`koncový bod HTTP ( ).</span><span class="sxs-lookup"><span data-stu-id="74642-152">For HTTP, use an HTTP (`http://`) endpoint.</span></span>

   <span data-ttu-id="74642-153">Zadejte stejný název hostitele a port jako [konfigurace služby IIS zadaná dříve ,](#configure-iis)obvykle . `localhost`</span><span class="sxs-lookup"><span data-stu-id="74642-153">Provide the same host name and port as the [IIS configuration specified earlier uses](#configure-iis), typically `localhost`.</span></span>

   <span data-ttu-id="74642-154">Na konci adresy URL zadejte název aplikace.</span><span class="sxs-lookup"><span data-stu-id="74642-154">Provide the name of the app at the end of the URL.</span></span>

   <span data-ttu-id="74642-155">Například `https://localhost/WebApplication1` (HTTPS) `http://localhost/WebApplication1` nebo (HTTP) jsou platné adresy URL koncového bodu.</span><span class="sxs-lookup"><span data-stu-id="74642-155">For example, `https://localhost/WebApplication1` (HTTPS) or `http://localhost/WebApplication1` (HTTP) are valid endpoint URLs.</span></span>
1. <span data-ttu-id="74642-156">V části **Proměnné prostředí** vyberte tlačítko **Přidat.**</span><span class="sxs-lookup"><span data-stu-id="74642-156">In the **Environment variables** section, select the **Add** button.</span></span> <span data-ttu-id="74642-157">Poskytněte proměnné prostředí `ASPNETCORE_ENVIRONMENT` s názvem `Development` **a** **hodnotou** .</span><span class="sxs-lookup"><span data-stu-id="74642-157">Provide an environment variable with a **Name** of `ASPNETCORE_ENVIRONMENT` and a **Value** of `Development`.</span></span>
1. <span data-ttu-id="74642-158">V oblasti **Nastavení webového serveru** nastavte **adresu URL aplikace** na stejnou hodnotu, která se používá pro adresu URL koncového bodu **prohlížeče launch.**</span><span class="sxs-lookup"><span data-stu-id="74642-158">In the **Web Server Settings** area, set the **App URL** to the same value used for the **Launch browser** endpoint URL.</span></span>
1. <span data-ttu-id="74642-159">Pro nastavení **hostování modelu** v sadě Visual Studio 2019 nebo novější vyberte **Výchozí,** chcete-li použít hostitelský model používaný v projektu.</span><span class="sxs-lookup"><span data-stu-id="74642-159">For the **Hosting Model** setting in Visual Studio 2019 or later, select **Default** to use the hosting model used by the project.</span></span> <span data-ttu-id="74642-160">Pokud projekt nastaví `<AspNetCoreHostingModel>` vlastnost v souboru projektu,`InProcess` použije `OutOfProcess`se hodnota vlastnosti ( nebo ).</span><span class="sxs-lookup"><span data-stu-id="74642-160">If the project sets the `<AspNetCoreHostingModel>` property in its project file, the value of the property (`InProcess` or `OutOfProcess`) is used.</span></span> <span data-ttu-id="74642-161">Pokud vlastnost není k dispozici, použije se výchozí model hostování aplikace, který je v procesu.</span><span class="sxs-lookup"><span data-stu-id="74642-161">If the property isn't present, the default hosting model of the app is used, which is in-process.</span></span> <span data-ttu-id="74642-162">Pokud aplikace vyžaduje explicitní nastavení hostitelského modelu odlišné od normálního hostitelského `In Process` modelu `Out Of Process` aplikace, nastavte **model hostování** na buď nebo podle potřeby.</span><span class="sxs-lookup"><span data-stu-id="74642-162">If the app requires an explicit hosting model setting different from the app's normal hosting model, set the **Hosting Model** to either `In Process` or `Out Of Process` as needed.</span></span>
1. <span data-ttu-id="74642-163">Uložte profil.</span><span class="sxs-lookup"><span data-stu-id="74642-163">Save the profile.</span></span>

<span data-ttu-id="74642-164">Pokud nepoužíváte Visual Studio, ručně přidejte spouštěcí profil do souboru [launchSettings.json](https://json.schemastore.org/launchsettings) ve složce *Vlastnosti.*</span><span class="sxs-lookup"><span data-stu-id="74642-164">When not using Visual Studio, manually add a launch profile to the [launchSettings.json](https://json.schemastore.org/launchsettings) file in the *Properties* folder.</span></span> <span data-ttu-id="74642-165">Následující příklad konfiguruje profil tak, aby používal protokol HTTPS:</span><span class="sxs-lookup"><span data-stu-id="74642-165">The following example configures the profile to use the HTTPS protocol:</span></span>

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

<span data-ttu-id="74642-166">Zkontrolujte, `applicationUrl` `launchUrl` zda koncové body a odpovídají a používají stejný protokol jako konfigurace vazby služby IIS, a to buď HTTP, nebo HTTPS.</span><span class="sxs-lookup"><span data-stu-id="74642-166">Confirm that the `applicationUrl` and `launchUrl` endpoints match and use the same protocol as the IIS binding configuration, either HTTP or HTTPS.</span></span>

## <a name="run-the-project"></a><span data-ttu-id="74642-167">Spuštění projektu</span><span class="sxs-lookup"><span data-stu-id="74642-167">Run the project</span></span>

<span data-ttu-id="74642-168">Spusťte Visual Studio jako správce:</span><span class="sxs-lookup"><span data-stu-id="74642-168">Run Visual Studio as an administrator:</span></span>

* <span data-ttu-id="74642-169">Zkontrolujte, zda je rozevírací seznam konfigurace sestavení nastaven na **ladit**.</span><span class="sxs-lookup"><span data-stu-id="74642-169">Confirm that the build configuration drop-down list is set to **Debug**.</span></span>
* <span data-ttu-id="74642-170">Nastavte [tlačítko Spustit ladění](/visualstudio/debugger/debugger-feature-tour) na profil **iis** a vyberte tlačítko pro spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="74642-170">Set the [Start Debugging button](/visualstudio/debugger/debugger-feature-tour) to the **IIS** profile and select the button to start the app.</span></span>

<span data-ttu-id="74642-171">Visual Studio může vyzvat k restartování, pokud není spuštěn jako správce.</span><span class="sxs-lookup"><span data-stu-id="74642-171">Visual Studio may prompt a restart if not running as an administrator.</span></span> <span data-ttu-id="74642-172">Po zobrazení výzvy restartujte visual studio.</span><span class="sxs-lookup"><span data-stu-id="74642-172">If prompted, restart Visual Studio.</span></span>

<span data-ttu-id="74642-173">Pokud je použit nedůvěryhodný vývojový certifikát, prohlížeč může vyžadovat vytvoření výjimky pro nedůvěryhodný certifikát.</span><span class="sxs-lookup"><span data-stu-id="74642-173">If an untrusted development certificate is used, the browser may require you to create an exception for the untrusted certificate.</span></span>

> [!NOTE]
> <span data-ttu-id="74642-174">Ladění konfigurace sestavení verze s [optimalizací](/visualstudio/debugger/just-my-code) pouze můj kód a kompilátoru má za následek zhoršené prostředí.</span><span class="sxs-lookup"><span data-stu-id="74642-174">Debugging a Release build configuration with [Just My Code](/visualstudio/debugger/just-my-code) and compiler optimizations results in a degraded experience.</span></span> <span data-ttu-id="74642-175">Například body přerušení nejsou přístupů.</span><span class="sxs-lookup"><span data-stu-id="74642-175">For example, break points aren't hit.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="74642-176">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="74642-176">Additional resources</span></span>

* [<span data-ttu-id="74642-177">Začínáme se Správcem služby IIS ve službách IIS</span><span class="sxs-lookup"><span data-stu-id="74642-177">Getting Started with the IIS Manager in IIS</span></span>](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* <xref:security/enforcing-ssl>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="74642-178">Tento článek popisuje podporu [sady Visual Studio](https://visualstudio.microsoft.com) pro ladění aplikací ASP.NET Core spuštěných se službou IIS v systému Windows Server.</span><span class="sxs-lookup"><span data-stu-id="74642-178">This article describes [Visual Studio](https://visualstudio.microsoft.com) support for debugging ASP.NET Core apps running with IIS on Windows Server.</span></span> <span data-ttu-id="74642-179">Toto téma vás provede povolením tohoto scénáře a nastavením projektu.</span><span class="sxs-lookup"><span data-stu-id="74642-179">This topic walks through enabling this scenario and setting up a project.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="74642-180">Požadavky</span><span class="sxs-lookup"><span data-stu-id="74642-180">Prerequisites</span></span>

* [<span data-ttu-id="74642-181">Visual Studio pro Windows</span><span class="sxs-lookup"><span data-stu-id="74642-181">Visual Studio for Windows</span></span>](https://visualstudio.microsoft.com/downloads/)
* <span data-ttu-id="74642-182">**ASP.NET a pracovní zátěž pro vývoj webových** aplikací</span><span class="sxs-lookup"><span data-stu-id="74642-182">**ASP.NET and web development** workload</span></span>
* <span data-ttu-id="74642-183">Pracovní **vytížení napříč platformami .NET Core**</span><span class="sxs-lookup"><span data-stu-id="74642-183">**.NET Core cross-platform development** workload</span></span>
* <span data-ttu-id="74642-184">Certifikát zabezpečení X.509 (pro podporu HTTPS)</span><span class="sxs-lookup"><span data-stu-id="74642-184">X.509 security certificate (for HTTPS support)</span></span>

## <a name="enable-iis"></a><span data-ttu-id="74642-185">Povolení iis</span><span class="sxs-lookup"><span data-stu-id="74642-185">Enable IIS</span></span>

1. <span data-ttu-id="74642-186">V systému Windows přejděte na **Ovládací panely** > **Programy** > **a funkce** > **Zapněte nebo vypněte funkce systému Windows** (levá strana obrazovky).</span><span class="sxs-lookup"><span data-stu-id="74642-186">In Windows, navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="74642-187">Zaškrtněte políčko **Internetová informační služba.**</span><span class="sxs-lookup"><span data-stu-id="74642-187">Select the **Internet Information Services** check box.</span></span> <span data-ttu-id="74642-188">Vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="74642-188">Select **OK**.</span></span>

<span data-ttu-id="74642-189">Instalace služby IIS může vyžadovat restartování systému.</span><span class="sxs-lookup"><span data-stu-id="74642-189">The IIS installation may require a system restart.</span></span>

## <a name="configure-iis"></a><span data-ttu-id="74642-190">Konfigurace služby IIS</span><span class="sxs-lookup"><span data-stu-id="74642-190">Configure IIS</span></span>

<span data-ttu-id="74642-191">IIS musí mít web nakonfigurovaný s následujícími:</span><span class="sxs-lookup"><span data-stu-id="74642-191">IIS must have a website configured with the following:</span></span>

* <span data-ttu-id="74642-192">**Název** &ndash; hostitele Výchozí **web** se obvykle používá s `localhost`názvem **hostitele** aplikace .</span><span class="sxs-lookup"><span data-stu-id="74642-192">**Host name** &ndash; Typically, the **Default Web Site** is used with a **Host name** of `localhost`.</span></span> <span data-ttu-id="74642-193">Všechny platné webové stránky iis s jedinečným názvem hostitele však fungují.</span><span class="sxs-lookup"><span data-stu-id="74642-193">However, any valid IIS website with a unique host name works.</span></span>
* <span data-ttu-id="74642-194">**Vazba webu**</span><span class="sxs-lookup"><span data-stu-id="74642-194">**Site Binding**</span></span>
  * <span data-ttu-id="74642-195">Pro aplikace, které vyžadují protokol HTTPS, vytvořte vazbu na port 443 s certifikátem.</span><span class="sxs-lookup"><span data-stu-id="74642-195">For apps that require HTTPS, create a binding to port 443 with a certificate.</span></span> <span data-ttu-id="74642-196">Obvykle se používá **certifikát IIS Express Development Certificate,** ale funguje všechny platné certifikáty.</span><span class="sxs-lookup"><span data-stu-id="74642-196">Typically, the **IIS Express Development Certificate** is used, but any valid certificate works.</span></span>
  * <span data-ttu-id="74642-197">Pro aplikace, které používají protokol HTTP, potvrďte existenci vazby pro zaúčtování 80 nebo vytvořte vazbu na port 80 pro nový web.</span><span class="sxs-lookup"><span data-stu-id="74642-197">For apps that use HTTP, confirm the existence of a binding to post 80 or create a binding to port 80 for a new site.</span></span>
  * <span data-ttu-id="74642-198">Použijte jednu vazbu pro protokol HTTP nebo HTTPS.</span><span class="sxs-lookup"><span data-stu-id="74642-198">Use a single binding for either HTTP or HTTPS.</span></span> <span data-ttu-id="74642-199">**Vazba na porty HTTP i HTTPS současně není podporována.**</span><span class="sxs-lookup"><span data-stu-id="74642-199">**Binding to both HTTP and HTTPS ports simultaneously isn't supported.**</span></span>

## <a name="enable-development-time-iis-support-in-visual-studio"></a><span data-ttu-id="74642-200">Povolení podpory služby IIS v době vývoje v sadě Visual Studio</span><span class="sxs-lookup"><span data-stu-id="74642-200">Enable development-time IIS support in Visual Studio</span></span>

1. <span data-ttu-id="74642-201">Spusťte instalační program sady Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="74642-201">Launch the Visual Studio installer.</span></span>
1. <span data-ttu-id="74642-202">Vyberte **Změnit** pro instalaci sady Visual Studio, kterou chcete použít pro podporu vývoje služby IIS.</span><span class="sxs-lookup"><span data-stu-id="74642-202">Select **Modify** for the Visual Studio installation that you plan to use for IIS development-time support.</span></span>
1. <span data-ttu-id="74642-203">Pro **ASP.NET a vývoj webových** úloh vyhledejte a nainstalujte součást **podpory služby IIS pro vývoj času vývoje.**</span><span class="sxs-lookup"><span data-stu-id="74642-203">For the **ASP.NET and web development** workload, locate and install the **Development time IIS support** component.</span></span>

   <span data-ttu-id="74642-204">Součást je uvedena v části **Volitelné** **čas podpory služby IIS** v panelu **Podrobnosti instalace** vpravo od úloh.</span><span class="sxs-lookup"><span data-stu-id="74642-204">The component is listed in the **Optional** section under **Development time IIS support** in the **Installation details** panel to the right of the workloads.</span></span> <span data-ttu-id="74642-205">Součást nainstaluje [ASP.NET základní modul](xref:host-and-deploy/aspnet-core-module), což je nativní modul služby IIS potřebný ke spuštění aplikací ASP.NET Core se službou IIS.</span><span class="sxs-lookup"><span data-stu-id="74642-205">The component installs the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), which is a native IIS module required to run ASP.NET Core apps with IIS.</span></span>

## <a name="configure-the-project"></a><span data-ttu-id="74642-206">Konfigurace projektu</span><span class="sxs-lookup"><span data-stu-id="74642-206">Configure the project</span></span>

### <a name="https-redirection"></a><span data-ttu-id="74642-207">Přesměrování https</span><span class="sxs-lookup"><span data-stu-id="74642-207">HTTPS redirection</span></span>

<span data-ttu-id="74642-208">U nového projektu, který vyžaduje protokol HTTPS, zaškrtněte políčko **Konfigurovat protokol HTTPS** v okně Vytvořit nový ASP.NET základní webové **aplikace.**</span><span class="sxs-lookup"><span data-stu-id="74642-208">For a new project that requires HTTPS, select the check box to **Configure for HTTPS** in the **Create a new ASP.NET Core Web Application** window.</span></span> <span data-ttu-id="74642-209">Zaškrtnutím políčka přidáte do aplikace přesměrování [HTTPS a HSTS Middleware,](xref:security/enforcing-ssl) když se vytvoří.</span><span class="sxs-lookup"><span data-stu-id="74642-209">Selecting the check box adds [HTTPS Redirection and HSTS Middleware](xref:security/enforcing-ssl) to the app when it's created.</span></span>

<span data-ttu-id="74642-210">Pro existující projekt, který vyžaduje protokol HTTPS, použijte v `Startup.Configure`aplikaci příkazy přesměrování HTTPS a Middleware HSTS .</span><span class="sxs-lookup"><span data-stu-id="74642-210">For an existing project that requires HTTPS, use HTTPS Redirection and HSTS Middleware in `Startup.Configure`.</span></span> <span data-ttu-id="74642-211">Další informace naleznete v tématu <xref:security/enforcing-ssl>.</span><span class="sxs-lookup"><span data-stu-id="74642-211">For more information, see <xref:security/enforcing-ssl>.</span></span>

<span data-ttu-id="74642-212">Pro projekt, který používá HTTP, [https přesměrování a HSTS Middleware](xref:security/enforcing-ssl) nejsou přidány do aplikace.</span><span class="sxs-lookup"><span data-stu-id="74642-212">For a project that uses HTTP, [HTTPS Redirection and HSTS Middleware](xref:security/enforcing-ssl) aren't added to the app.</span></span> <span data-ttu-id="74642-213">Není vyžadována žádná konfigurace aplikace.</span><span class="sxs-lookup"><span data-stu-id="74642-213">No app configuration is required.</span></span>

### <a name="iis-launch-profile"></a><span data-ttu-id="74642-214">Profil spuštění iis</span><span class="sxs-lookup"><span data-stu-id="74642-214">IIS launch profile</span></span>

<span data-ttu-id="74642-215">Vytvořte nový spouštěcí profil pro přidání podpory služby IIS v době vývoje:</span><span class="sxs-lookup"><span data-stu-id="74642-215">Create a new launch profile to add development-time IIS support:</span></span>

1. <span data-ttu-id="74642-216">Klepněte pravým tlačítkem myši na projekt v **Průzkumníku řešení**.</span><span class="sxs-lookup"><span data-stu-id="74642-216">Right-click the project in **Solution Explorer**.</span></span> <span data-ttu-id="74642-217">Vyberte **vlastnosti**.</span><span class="sxs-lookup"><span data-stu-id="74642-217">Select **Properties**.</span></span> <span data-ttu-id="74642-218">Otevřete kartu **Ladění.**</span><span class="sxs-lookup"><span data-stu-id="74642-218">Open the **Debug** tab.</span></span>
1. <span data-ttu-id="74642-219">V **části Profil**vyberte tlačítko **Nový.**</span><span class="sxs-lookup"><span data-stu-id="74642-219">For **Profile**, select the **New** button.</span></span> <span data-ttu-id="74642-220">V automaticky otevíraném okně pojmenujte profil IIS.</span><span class="sxs-lookup"><span data-stu-id="74642-220">Name the profile "IIS" in the popup window.</span></span> <span data-ttu-id="74642-221">Chcete-li vytvořit profil, vyberte **OK.**</span><span class="sxs-lookup"><span data-stu-id="74642-221">Select **OK** to create the profile.</span></span>
1. <span data-ttu-id="74642-222">Pro **nastavení Spuštění** vyberte **iis** ze seznamu.</span><span class="sxs-lookup"><span data-stu-id="74642-222">For the **Launch** setting, select **IIS** from the list.</span></span>
1. <span data-ttu-id="74642-223">Zaškrtněte políčko **spustit prohlížeč** a zadejte adresu URL koncového bodu.</span><span class="sxs-lookup"><span data-stu-id="74642-223">Select the check box for **Launch browser** and provide the endpoint URL.</span></span>

   <span data-ttu-id="74642-224">Když aplikace vyžaduje HTTPS, použijte koncový`https://`bod HTTPS ( ).</span><span class="sxs-lookup"><span data-stu-id="74642-224">When the app requires HTTPS, use an HTTPS endpoint (`https://`).</span></span> <span data-ttu-id="74642-225">Pro protokol HTTP použijte`http://`koncový bod HTTP ( ).</span><span class="sxs-lookup"><span data-stu-id="74642-225">For HTTP, use an HTTP (`http://`) endpoint.</span></span>

   <span data-ttu-id="74642-226">Zadejte stejný název hostitele a port jako [konfigurace služby IIS zadaná dříve ,](#configure-iis)obvykle . `localhost`</span><span class="sxs-lookup"><span data-stu-id="74642-226">Provide the same host name and port as the [IIS configuration specified earlier uses](#configure-iis), typically `localhost`.</span></span>

   <span data-ttu-id="74642-227">Na konci adresy URL zadejte název aplikace.</span><span class="sxs-lookup"><span data-stu-id="74642-227">Provide the name of the app at the end of the URL.</span></span>

   <span data-ttu-id="74642-228">Například `https://localhost/WebApplication1` (HTTPS) `http://localhost/WebApplication1` nebo (HTTP) jsou platné adresy URL koncového bodu.</span><span class="sxs-lookup"><span data-stu-id="74642-228">For example, `https://localhost/WebApplication1` (HTTPS) or `http://localhost/WebApplication1` (HTTP) are valid endpoint URLs.</span></span>
1. <span data-ttu-id="74642-229">V části **Proměnné prostředí** vyberte tlačítko **Přidat.**</span><span class="sxs-lookup"><span data-stu-id="74642-229">In the **Environment variables** section, select the **Add** button.</span></span> <span data-ttu-id="74642-230">Poskytněte proměnné prostředí `ASPNETCORE_ENVIRONMENT` s názvem `Development` **a** **hodnotou** .</span><span class="sxs-lookup"><span data-stu-id="74642-230">Provide an environment variable with a **Name** of `ASPNETCORE_ENVIRONMENT` and a **Value** of `Development`.</span></span>
1. <span data-ttu-id="74642-231">V oblasti **Nastavení webového serveru** nastavte **adresu URL aplikace** na stejnou hodnotu, která se používá pro adresu URL koncového bodu **prohlížeče launch.**</span><span class="sxs-lookup"><span data-stu-id="74642-231">In the **Web Server Settings** area, set the **App URL** to the same value used for the **Launch browser** endpoint URL.</span></span>
1. <span data-ttu-id="74642-232">Pro nastavení **hostování modelu** v sadě Visual Studio 2019 nebo novější vyberte **Výchozí,** chcete-li použít hostitelský model používaný v projektu.</span><span class="sxs-lookup"><span data-stu-id="74642-232">For the **Hosting Model** setting in Visual Studio 2019 or later, select **Default** to use the hosting model used by the project.</span></span> <span data-ttu-id="74642-233">Pokud projekt nastaví `<AspNetCoreHostingModel>` vlastnost v souboru projektu,`InProcess` použije `OutOfProcess`se hodnota vlastnosti ( nebo ).</span><span class="sxs-lookup"><span data-stu-id="74642-233">If the project sets the `<AspNetCoreHostingModel>` property in its project file, the value of the property (`InProcess` or `OutOfProcess`) is used.</span></span> <span data-ttu-id="74642-234">Pokud vlastnost není k dispozici, použije se výchozí model hostování aplikace, který je mimo proces.</span><span class="sxs-lookup"><span data-stu-id="74642-234">If the property isn't present, the default hosting model of the app is used, which is out-of-process.</span></span> <span data-ttu-id="74642-235">Pokud aplikace vyžaduje explicitní nastavení hostitelského modelu odlišné od normálního hostitelského `In Process` modelu `Out Of Process` aplikace, nastavte **model hostování** na buď nebo podle potřeby.</span><span class="sxs-lookup"><span data-stu-id="74642-235">If the app requires an explicit hosting model setting different from the app's normal hosting model, set the **Hosting Model** to either `In Process` or `Out Of Process` as needed.</span></span>
1. <span data-ttu-id="74642-236">Uložte profil.</span><span class="sxs-lookup"><span data-stu-id="74642-236">Save the profile.</span></span>

<span data-ttu-id="74642-237">Pokud nepoužíváte Visual Studio, ručně přidejte spouštěcí profil do souboru [launchSettings.json](https://json.schemastore.org/launchsettings) ve složce *Vlastnosti.*</span><span class="sxs-lookup"><span data-stu-id="74642-237">When not using Visual Studio, manually add a launch profile to the [launchSettings.json](https://json.schemastore.org/launchsettings) file in the *Properties* folder.</span></span> <span data-ttu-id="74642-238">Následující příklad konfiguruje profil tak, aby používal protokol HTTPS:</span><span class="sxs-lookup"><span data-stu-id="74642-238">The following example configures the profile to use the HTTPS protocol:</span></span>

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

<span data-ttu-id="74642-239">Zkontrolujte, `applicationUrl` `launchUrl` zda koncové body a odpovídají a používají stejný protokol jako konfigurace vazby služby IIS, a to buď HTTP, nebo HTTPS.</span><span class="sxs-lookup"><span data-stu-id="74642-239">Confirm that the `applicationUrl` and `launchUrl` endpoints match and use the same protocol as the IIS binding configuration, either HTTP or HTTPS.</span></span>

## <a name="run-the-project"></a><span data-ttu-id="74642-240">Spuštění projektu</span><span class="sxs-lookup"><span data-stu-id="74642-240">Run the project</span></span>

<span data-ttu-id="74642-241">Spusťte Visual Studio jako správce:</span><span class="sxs-lookup"><span data-stu-id="74642-241">Run Visual Studio as an administrator:</span></span>

* <span data-ttu-id="74642-242">Zkontrolujte, zda je rozevírací seznam konfigurace sestavení nastaven na **ladit**.</span><span class="sxs-lookup"><span data-stu-id="74642-242">Confirm that the build configuration drop-down list is set to **Debug**.</span></span>
* <span data-ttu-id="74642-243">Nastavte [tlačítko Spustit ladění](/visualstudio/debugger/debugger-feature-tour) na profil **iis** a vyberte tlačítko pro spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="74642-243">Set the [Start Debugging button](/visualstudio/debugger/debugger-feature-tour) to the **IIS** profile and select the button to start the app.</span></span>

<span data-ttu-id="74642-244">Visual Studio může vyzvat k restartování, pokud není spuštěn jako správce.</span><span class="sxs-lookup"><span data-stu-id="74642-244">Visual Studio may prompt a restart if not running as an administrator.</span></span> <span data-ttu-id="74642-245">Po zobrazení výzvy restartujte visual studio.</span><span class="sxs-lookup"><span data-stu-id="74642-245">If prompted, restart Visual Studio.</span></span>

<span data-ttu-id="74642-246">Pokud je použit nedůvěryhodný vývojový certifikát, prohlížeč může vyžadovat vytvoření výjimky pro nedůvěryhodný certifikát.</span><span class="sxs-lookup"><span data-stu-id="74642-246">If an untrusted development certificate is used, the browser may require you to create an exception for the untrusted certificate.</span></span>

> [!NOTE]
> <span data-ttu-id="74642-247">Ladění konfigurace sestavení verze s [optimalizací](/visualstudio/debugger/just-my-code) pouze můj kód a kompilátoru má za následek zhoršené prostředí.</span><span class="sxs-lookup"><span data-stu-id="74642-247">Debugging a Release build configuration with [Just My Code](/visualstudio/debugger/just-my-code) and compiler optimizations results in a degraded experience.</span></span> <span data-ttu-id="74642-248">Například body přerušení nejsou přístupů.</span><span class="sxs-lookup"><span data-stu-id="74642-248">For example, break points aren't hit.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="74642-249">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="74642-249">Additional resources</span></span>

* [<span data-ttu-id="74642-250">Začínáme se Správcem služby IIS ve službách IIS</span><span class="sxs-lookup"><span data-stu-id="74642-250">Getting Started with the IIS Manager in IIS</span></span>](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* <xref:security/enforcing-ssl>

::: moniker-end
