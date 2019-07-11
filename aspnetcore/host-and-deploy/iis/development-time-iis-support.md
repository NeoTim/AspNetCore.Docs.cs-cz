---
title: Podpora služby IIS při vývoji v sadě Visual Studio pro ASP.NET Core
author: guardrex
description: Podívejte se na podporu pro ladění aplikací ASP.NET Core, když se službou IIS a systémem Windows Server.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/08/2019
uid: host-and-deploy/iis/development-time-iis-support
ms.openlocfilehash: f2d5dbbdc80eec035616ddea234ee5d3343eeae8
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67815182"
---
# <a name="development-time-iis-support-in-visual-studio-for-aspnet-core"></a><span data-ttu-id="ce130-103">Podpora služby IIS při vývoji v sadě Visual Studio pro ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ce130-103">Development-time IIS support in Visual Studio for ASP.NET Core</span></span>

<span data-ttu-id="ce130-104">Podle [Sourabh Shirhatti](https://twitter.com/sshirhatti) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="ce130-104">By [Sourabh Shirhatti](https://twitter.com/sshirhatti) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="ce130-105">Tento článek popisuje [sady Visual Studio](https://visualstudio.microsoft.com) podporuje ladění aplikací ASP.NET Core se službou IIS a systémem Windows Server.</span><span class="sxs-lookup"><span data-stu-id="ce130-105">This article describes [Visual Studio](https://visualstudio.microsoft.com) support for debugging ASP.NET Core apps running with IIS on Windows Server.</span></span> <span data-ttu-id="ce130-106">Toto téma vás provede povolením tento scénář a nastavení projektu.</span><span class="sxs-lookup"><span data-stu-id="ce130-106">This topic walks through enabling this scenario and setting up a project.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="ce130-107">Požadavky</span><span class="sxs-lookup"><span data-stu-id="ce130-107">Prerequisites</span></span>

* [<span data-ttu-id="ce130-108">Visual Studio for Windows</span><span class="sxs-lookup"><span data-stu-id="ce130-108">Visual Studio for Windows</span></span>](https://visualstudio.microsoft.com/downloads/)
* <span data-ttu-id="ce130-109">**Vývoj pro ASP.NET a web** pracovního vytížení</span><span class="sxs-lookup"><span data-stu-id="ce130-109">**ASP.NET and web development** workload</span></span>
* <span data-ttu-id="ce130-110">**Vývoj pro různé platformy .NET core** pracovního vytížení</span><span class="sxs-lookup"><span data-stu-id="ce130-110">**.NET Core cross-platform development** workload</span></span>
* <span data-ttu-id="ce130-111">Certifikát X.509 zabezpečení (pro podporu protokolu HTTPS)</span><span class="sxs-lookup"><span data-stu-id="ce130-111">X.509 security certificate (for HTTPS support)</span></span>

## <a name="enable-iis"></a><span data-ttu-id="ce130-112">Povolte službu IIS</span><span class="sxs-lookup"><span data-stu-id="ce130-112">Enable IIS</span></span>

1. <span data-ttu-id="ce130-113">Ve Windows, přejděte na **ovládací panely** > **programy** > **programy a funkce** > **zapnout Windows zapnout nebo vypnout funkce** (levé straně obrazovky).</span><span class="sxs-lookup"><span data-stu-id="ce130-113">In Windows, navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="ce130-114">Vyberte **Internetová informační služba** zaškrtávací políčko.</span><span class="sxs-lookup"><span data-stu-id="ce130-114">Select the **Internet Information Services** check box.</span></span> <span data-ttu-id="ce130-115">Vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="ce130-115">Select **OK**.</span></span>

<span data-ttu-id="ce130-116">Instalace služby IIS může vyžadovat restartování systému.</span><span class="sxs-lookup"><span data-stu-id="ce130-116">The IIS installation may require a system restart.</span></span>

## <a name="configure-iis"></a><span data-ttu-id="ce130-117">Konfigurace služby IIS</span><span class="sxs-lookup"><span data-stu-id="ce130-117">Configure IIS</span></span>

<span data-ttu-id="ce130-118">Služba IIS musí mít web nakonfigurované tyto parametry:</span><span class="sxs-lookup"><span data-stu-id="ce130-118">IIS must have a website configured with the following:</span></span>

* <span data-ttu-id="ce130-119">**Název hostitele** &ndash; obvykle **výchozí webový server** se používá s **název hostitele** z `localhost`.</span><span class="sxs-lookup"><span data-stu-id="ce130-119">**Host name** &ndash; Typically, the **Default Web Site** is used with a **Host name** of `localhost`.</span></span> <span data-ttu-id="ce130-120">Libovolný platný web služby IIS s názvem hostitele však funguje.</span><span class="sxs-lookup"><span data-stu-id="ce130-120">However, any valid IIS website with a unique host name works.</span></span>
* <span data-ttu-id="ce130-121">**Vazby webu**</span><span class="sxs-lookup"><span data-stu-id="ce130-121">**Site Binding**</span></span>
  * <span data-ttu-id="ce130-122">Pro aplikace, které vyžadují protokol HTTPS vytvořte vazbu na port 443 pomocí certifikátu.</span><span class="sxs-lookup"><span data-stu-id="ce130-122">For apps that require HTTPS, create a binding to port 443 with a certificate.</span></span> <span data-ttu-id="ce130-123">Obvykle **služby IIS Express vývojářský certifikát, kterým** se používá, ale libovolný platný certifikát funguje.</span><span class="sxs-lookup"><span data-stu-id="ce130-123">Typically, the **IIS Express Development Certificate** is used, but any valid certificate works.</span></span>
  * <span data-ttu-id="ce130-124">Pro aplikace, které používají protokol HTTP, zkontrolujte existenci vazbu na příspěvek 80 nebo vytvořit vazbu na port 80 pro nový web.</span><span class="sxs-lookup"><span data-stu-id="ce130-124">For apps that use HTTP, confirm the existence of a binding to post 80 or create a binding to port 80 for a new site.</span></span>
  * <span data-ttu-id="ce130-125">Použití jedné vazby pro protokol HTTP nebo HTTPS.</span><span class="sxs-lookup"><span data-stu-id="ce130-125">Use a single binding for either HTTP or HTTPS.</span></span> <span data-ttu-id="ce130-126">**Vytvoření vazby na portech HTTP a HTTPS současně není podporováno.**</span><span class="sxs-lookup"><span data-stu-id="ce130-126">**Binding to both HTTP and HTTPS ports simultaneously isn't supported.**</span></span>

## <a name="enable-development-time-iis-support-in-visual-studio"></a><span data-ttu-id="ce130-127">Povolit podporu služby IIS dobu vývoje v sadě Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ce130-127">Enable development-time IIS support in Visual Studio</span></span>

1. <span data-ttu-id="ce130-128">Spusťte instalační program sady Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="ce130-128">Launch the Visual Studio installer.</span></span>
1. <span data-ttu-id="ce130-129">Vyberte **změnit** pro instalaci sady Visual Studio, který chcete použít pro podporu vývoje služby IIS.</span><span class="sxs-lookup"><span data-stu-id="ce130-129">Select **Modify** for the Visual Studio installation that you plan to use for IIS development-time support.</span></span>
1. <span data-ttu-id="ce130-130">Pro **vývoj pro ASP.NET a web** pracovního vytížení, vyhledejte a nainstalujte **dobu vývoje podpora služby IIS** komponenty.</span><span class="sxs-lookup"><span data-stu-id="ce130-130">For the **ASP.NET and web development** workload, locate and install the **Development time IIS support** component.</span></span>

   <span data-ttu-id="ce130-131">Součást je uveden v **volitelné** části **dobu vývoje podpora služby IIS** v **podrobné informace o instalaci** panelu napravo od úlohy.</span><span class="sxs-lookup"><span data-stu-id="ce130-131">The component is listed in the **Optional** section under **Development time IIS support** in the **Installation details** panel to the right of the workloads.</span></span> <span data-ttu-id="ce130-132">Nainstaluje komponenty [modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module), což je nativní modul IIS potřebné ke spuštění aplikace ASP.NET Core se službou IIS.</span><span class="sxs-lookup"><span data-stu-id="ce130-132">The component installs the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), which is a native IIS module required to run ASP.NET Core apps with IIS.</span></span>

## <a name="configure-the-project"></a><span data-ttu-id="ce130-133">Konfigurace projektu</span><span class="sxs-lookup"><span data-stu-id="ce130-133">Configure the project</span></span>

### <a name="https-redirection"></a><span data-ttu-id="ce130-134">Přesměrování protokolu HTTPS</span><span class="sxs-lookup"><span data-stu-id="ce130-134">HTTPS redirection</span></span>

<span data-ttu-id="ce130-135">Nový projekt, který vyžaduje protokol HTTPS, zaškrtněte políčko pro **konfigurace pro protokol HTTPS** v **vytvořit novou webovou aplikaci ASP.NET Core** okna.</span><span class="sxs-lookup"><span data-stu-id="ce130-135">For a new project that requires HTTPS, select the check box to **Configure for HTTPS** in the **Create a new ASP.NET Core Web Application** window.</span></span> <span data-ttu-id="ce130-136">Zaškrtnutím políčka přidá [přesměrování protokolu HTTPS a Middlewarem HSTS](xref:security/enforcing-ssl) do aplikace při jeho vytvoření.</span><span class="sxs-lookup"><span data-stu-id="ce130-136">Selecting the check box adds [HTTPS Redirection and HSTS Middleware](xref:security/enforcing-ssl) to the app when it's created.</span></span>

<span data-ttu-id="ce130-137">Pro existující projekt, který vyžaduje protokol HTTPS, použijte přesměrování protokolu HTTPS a HSTS middlewaru v `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="ce130-137">For an existing project that requires HTTPS, use HTTPS Redirection and HSTS Middleware in `Startup.Configure`.</span></span> <span data-ttu-id="ce130-138">Další informace naleznete v tématu <xref:security/enforcing-ssl>.</span><span class="sxs-lookup"><span data-stu-id="ce130-138">For more information, see <xref:security/enforcing-ssl>.</span></span>

<span data-ttu-id="ce130-139">V projektu, který používá protokol HTTP [přesměrování protokolu HTTPS a Middlewarem HSTS](xref:security/enforcing-ssl) nejsou přidány do aplikace.</span><span class="sxs-lookup"><span data-stu-id="ce130-139">For a project that uses HTTP, [HTTPS Redirection and HSTS Middleware](xref:security/enforcing-ssl) aren't added to the app.</span></span> <span data-ttu-id="ce130-140">Není nutná žádná konfigurace aplikace.</span><span class="sxs-lookup"><span data-stu-id="ce130-140">No app configuration is required.</span></span>

### <a name="iis-launch-profile"></a><span data-ttu-id="ce130-141">Profil spuštění služby IIS</span><span class="sxs-lookup"><span data-stu-id="ce130-141">IIS launch profile</span></span>

<span data-ttu-id="ce130-142">Vytvoření nového profilu spuštění přidává dobu vývoje služby IIS:</span><span class="sxs-lookup"><span data-stu-id="ce130-142">Create a new launch profile to add development-time IIS support:</span></span>

::: moniker range=">= aspnetcore-3.0"

1. <span data-ttu-id="ce130-143">Klikněte pravým tlačítkem na projekt v **Průzkumníka řešení**.</span><span class="sxs-lookup"><span data-stu-id="ce130-143">Right-click the project in **Solution Explorer**.</span></span> <span data-ttu-id="ce130-144">Vyberte **vlastnosti**.</span><span class="sxs-lookup"><span data-stu-id="ce130-144">Select **Properties**.</span></span> <span data-ttu-id="ce130-145">Otevřít **ladění** kartu.</span><span class="sxs-lookup"><span data-stu-id="ce130-145">Open the **Debug** tab.</span></span>
1. <span data-ttu-id="ce130-146">Pro **profilu**, vyberte **nový** tlačítko.</span><span class="sxs-lookup"><span data-stu-id="ce130-146">For **Profile**, select the **New** button.</span></span> <span data-ttu-id="ce130-147">Název profilu "IIS" v automaticky otevíraném okně.</span><span class="sxs-lookup"><span data-stu-id="ce130-147">Name the profile "IIS" in the popup window.</span></span> <span data-ttu-id="ce130-148">Vyberte **OK** vytvořte profil.</span><span class="sxs-lookup"><span data-stu-id="ce130-148">Select **OK** to create the profile.</span></span>
1. <span data-ttu-id="ce130-149">Pro **spuštění** vyberte **IIS** ze seznamu.</span><span class="sxs-lookup"><span data-stu-id="ce130-149">For the **Launch** setting, select **IIS** from the list.</span></span>
1. <span data-ttu-id="ce130-150">Zaškrtněte políčko pro **spuštění prohlížeče** a zadejte adresu URL koncového bodu.</span><span class="sxs-lookup"><span data-stu-id="ce130-150">Select the check box for **Launch browser** and provide the endpoint URL.</span></span>

   <span data-ttu-id="ce130-151">Pokud aplikace vyžaduje protokol HTTPS, použijte koncový bod HTTPS (`https://`).</span><span class="sxs-lookup"><span data-stu-id="ce130-151">When the app requires HTTPS, use an HTTPS endpoint (`https://`).</span></span> <span data-ttu-id="ce130-152">Pro protokol HTTP, používat HTTP (`http://`) koncového bodu.</span><span class="sxs-lookup"><span data-stu-id="ce130-152">For HTTP, use an HTTP (`http://`) endpoint.</span></span>

   <span data-ttu-id="ce130-153">Zadejte stejný název hostitele a portu, jako [zadána konfigurace služby IIS používá starší](#configure-iis), obvykle `localhost`.</span><span class="sxs-lookup"><span data-stu-id="ce130-153">Provide the same host name and port as the [IIS configuration specified earlier uses](#configure-iis), typically `localhost`.</span></span>

   <span data-ttu-id="ce130-154">Zadejte název aplikace na konci adresy URL.</span><span class="sxs-lookup"><span data-stu-id="ce130-154">Provide the name of the app at the end of the URL.</span></span>

   <span data-ttu-id="ce130-155">Například `https://localhost/WebApplication1` (HTTPS) nebo `http://localhost/WebApplication1` (HTTP) jsou platný koncový bod adresy URL.</span><span class="sxs-lookup"><span data-stu-id="ce130-155">For example, `https://localhost/WebApplication1` (HTTPS) or `http://localhost/WebApplication1` (HTTP) are valid endpoint URLs.</span></span>
1. <span data-ttu-id="ce130-156">V **proměnné prostředí** vyberte **přidat** tlačítko.</span><span class="sxs-lookup"><span data-stu-id="ce130-156">In the **Environment variables** section, select the **Add** button.</span></span> <span data-ttu-id="ce130-157">Zadat proměnné prostředí s **název** z `ASPNETCORE_ENVIRONMENT` a **hodnotu** z `Development`.</span><span class="sxs-lookup"><span data-stu-id="ce130-157">Provide an environment variable with a **Name** of `ASPNETCORE_ENVIRONMENT` and a **Value** of `Development`.</span></span>
1. <span data-ttu-id="ce130-158">V **nastavení webového serveru** nastavení oblasti **adresa URL aplikace** na stejnou hodnotu pro **spuštění prohlížeče** adresu URL koncového bodu.</span><span class="sxs-lookup"><span data-stu-id="ce130-158">In the **Web Server Settings** area, set the **App URL** to the same value used for the **Launch browser** endpoint URL.</span></span>
1. <span data-ttu-id="ce130-159">Pro **Model hostování** nastavení v aplikaci Visual Studio 2019 nebo novější, vyberte **výchozí** používat model hostingu používané v projektu.</span><span class="sxs-lookup"><span data-stu-id="ce130-159">For the **Hosting Model** setting in Visual Studio 2019 or later, select **Default** to use the hosting model used by the project.</span></span> <span data-ttu-id="ce130-160">Pokud je projekt nastaví `<AspNetCoreHostingModel>` vlastnost v souboru projektu, hodnota vlastnosti (`InProcess` nebo `OutOfProcess`) se používá.</span><span class="sxs-lookup"><span data-stu-id="ce130-160">If the project sets the `<AspNetCoreHostingModel>` property in its project file, the value of the property (`InProcess` or `OutOfProcess`) is used.</span></span> <span data-ttu-id="ce130-161">Pokud vlastnost není k dispozici, použije se výchozí hostování modelu aplikace, což je v procesu.</span><span class="sxs-lookup"><span data-stu-id="ce130-161">If the property isn't present, the default hosting model of the app is used, which is in-process.</span></span> <span data-ttu-id="ce130-162">Pokud aplikace vyžaduje explicitní model hostingu nastavení liší od normální model hostingu aplikace, nastavte **Model hostování** buď `In Process` nebo `Out Of Process` podle potřeby.</span><span class="sxs-lookup"><span data-stu-id="ce130-162">If the app requires an explicit hosting model setting different from the app's normal hosting model, set the **Hosting Model** to either `In Process` or `Out Of Process` as needed.</span></span>
1. <span data-ttu-id="ce130-163">Uložte profil.</span><span class="sxs-lookup"><span data-stu-id="ce130-163">Save the profile.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

1. <span data-ttu-id="ce130-164">Klikněte pravým tlačítkem na projekt v **Průzkumníka řešení**.</span><span class="sxs-lookup"><span data-stu-id="ce130-164">Right-click the project in **Solution Explorer**.</span></span> <span data-ttu-id="ce130-165">Vyberte **vlastnosti**.</span><span class="sxs-lookup"><span data-stu-id="ce130-165">Select **Properties**.</span></span> <span data-ttu-id="ce130-166">Otevřít **ladění** kartu.</span><span class="sxs-lookup"><span data-stu-id="ce130-166">Open the **Debug** tab.</span></span>
1. <span data-ttu-id="ce130-167">Pro **profilu**, vyberte **nový** tlačítko.</span><span class="sxs-lookup"><span data-stu-id="ce130-167">For **Profile**, select the **New** button.</span></span> <span data-ttu-id="ce130-168">Název profilu "IIS" v automaticky otevíraném okně.</span><span class="sxs-lookup"><span data-stu-id="ce130-168">Name the profile "IIS" in the popup window.</span></span> <span data-ttu-id="ce130-169">Vyberte **OK** vytvořte profil.</span><span class="sxs-lookup"><span data-stu-id="ce130-169">Select **OK** to create the profile.</span></span>
1. <span data-ttu-id="ce130-170">Pro **spuštění** vyberte **IIS** ze seznamu.</span><span class="sxs-lookup"><span data-stu-id="ce130-170">For the **Launch** setting, select **IIS** from the list.</span></span>
1. <span data-ttu-id="ce130-171">Zaškrtněte políčko pro **spuštění prohlížeče** a zadejte adresu URL koncového bodu.</span><span class="sxs-lookup"><span data-stu-id="ce130-171">Select the check box for **Launch browser** and provide the endpoint URL.</span></span>

   <span data-ttu-id="ce130-172">Pokud aplikace vyžaduje protokol HTTPS, použijte koncový bod HTTPS (`https://`).</span><span class="sxs-lookup"><span data-stu-id="ce130-172">When the app requires HTTPS, use an HTTPS endpoint (`https://`).</span></span> <span data-ttu-id="ce130-173">Pro protokol HTTP, používat HTTP (`http://`) koncového bodu.</span><span class="sxs-lookup"><span data-stu-id="ce130-173">For HTTP, use an HTTP (`http://`) endpoint.</span></span>

   <span data-ttu-id="ce130-174">Zadejte stejný název hostitele a portu, jako [zadána konfigurace služby IIS používá starší](#configure-iis), obvykle `localhost`.</span><span class="sxs-lookup"><span data-stu-id="ce130-174">Provide the same host name and port as the [IIS configuration specified earlier uses](#configure-iis), typically `localhost`.</span></span>

   <span data-ttu-id="ce130-175">Zadejte název aplikace na konci adresy URL.</span><span class="sxs-lookup"><span data-stu-id="ce130-175">Provide the name of the app at the end of the URL.</span></span>

   <span data-ttu-id="ce130-176">Například `https://localhost/WebApplication1` (HTTPS) nebo `http://localhost/WebApplication1` (HTTP) jsou platný koncový bod adresy URL.</span><span class="sxs-lookup"><span data-stu-id="ce130-176">For example, `https://localhost/WebApplication1` (HTTPS) or `http://localhost/WebApplication1` (HTTP) are valid endpoint URLs.</span></span>
1. <span data-ttu-id="ce130-177">V **proměnné prostředí** vyberte **přidat** tlačítko.</span><span class="sxs-lookup"><span data-stu-id="ce130-177">In the **Environment variables** section, select the **Add** button.</span></span> <span data-ttu-id="ce130-178">Zadat proměnné prostředí s **název** z `ASPNETCORE_ENVIRONMENT` a **hodnotu** z `Development`.</span><span class="sxs-lookup"><span data-stu-id="ce130-178">Provide an environment variable with a **Name** of `ASPNETCORE_ENVIRONMENT` and a **Value** of `Development`.</span></span>
1. <span data-ttu-id="ce130-179">V **nastavení webového serveru** nastavení oblasti **adresa URL aplikace** na stejnou hodnotu pro **spuštění prohlížeče** adresu URL koncového bodu.</span><span class="sxs-lookup"><span data-stu-id="ce130-179">In the **Web Server Settings** area, set the **App URL** to the same value used for the **Launch browser** endpoint URL.</span></span>
1. <span data-ttu-id="ce130-180">Pro **Model hostování** nastavení v aplikaci Visual Studio 2019 nebo novější, vyberte **výchozí** používat model hostingu používané v projektu.</span><span class="sxs-lookup"><span data-stu-id="ce130-180">For the **Hosting Model** setting in Visual Studio 2019 or later, select **Default** to use the hosting model used by the project.</span></span> <span data-ttu-id="ce130-181">Pokud je projekt nastaví `<AspNetCoreHostingModel>` vlastnost v souboru projektu, hodnota vlastnosti (`InProcess` nebo `OutOfProcess`) se používá.</span><span class="sxs-lookup"><span data-stu-id="ce130-181">If the project sets the `<AspNetCoreHostingModel>` property in its project file, the value of the property (`InProcess` or `OutOfProcess`) is used.</span></span> <span data-ttu-id="ce130-182">Pokud vlastnost není k dispozici, použije se výchozí hostování modelu aplikace, což je mimo proces.</span><span class="sxs-lookup"><span data-stu-id="ce130-182">If the property isn't present, the default hosting model of the app is used, which is out-of-process.</span></span> <span data-ttu-id="ce130-183">Pokud aplikace vyžaduje explicitní model hostingu nastavení liší od normální model hostingu aplikace, nastavte **Model hostování** buď `In Process` nebo `Out Of Process` podle potřeby.</span><span class="sxs-lookup"><span data-stu-id="ce130-183">If the app requires an explicit hosting model setting different from the app's normal hosting model, set the **Hosting Model** to either `In Process` or `Out Of Process` as needed.</span></span>
1. <span data-ttu-id="ce130-184">Uložte profil.</span><span class="sxs-lookup"><span data-stu-id="ce130-184">Save the profile.</span></span>

::: moniker-end

<span data-ttu-id="ce130-185">Pokud není pomocí sady Visual Studio, ručně přidat profil spuštění, který [launchSettings.json](https://json.schemastore.org/launchsettings) ve *vlastnosti* složky.</span><span class="sxs-lookup"><span data-stu-id="ce130-185">When not using Visual Studio, manually add a launch profile to the [launchSettings.json](https://json.schemastore.org/launchsettings) file in the *Properties* folder.</span></span> <span data-ttu-id="ce130-186">Následující příklad nastaví profil, který chcete používat protokol HTTPS:</span><span class="sxs-lookup"><span data-stu-id="ce130-186">The following example configures the profile to use the HTTPS protocol:</span></span>

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

<span data-ttu-id="ce130-187">Ujistěte se, že `applicationUrl` a `launchUrl` koncové body odpovídat a používají stejný protokol jako konfigurace vazby služby IIS, HTTP nebo HTTPS.</span><span class="sxs-lookup"><span data-stu-id="ce130-187">Confirm that the `applicationUrl` and `launchUrl` endpoints match and use the same protocol as the IIS binding configuration, either HTTP or HTTPS.</span></span>

## <a name="run-the-project"></a><span data-ttu-id="ce130-188">Spusťte projekt</span><span class="sxs-lookup"><span data-stu-id="ce130-188">Run the project</span></span>

<span data-ttu-id="ce130-189">Spuštění sady Visual Studio jako správce:</span><span class="sxs-lookup"><span data-stu-id="ce130-189">Run Visual Studio as an administrator:</span></span>

* <span data-ttu-id="ce130-190">Potvrďte, že je nastavena rozevíracím seznamu konfigurací sestavení **ladění**.</span><span class="sxs-lookup"><span data-stu-id="ce130-190">Confirm that the build configuration drop-down list is set to **Debug**.</span></span>
* <span data-ttu-id="ce130-191">Nastavte na tlačítko Spustit **IIS** profilu a klikněte na tlačítko a spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="ce130-191">Set the Run button to the **IIS** profile and select the button to start the app.</span></span>

<span data-ttu-id="ce130-192">Visual Studio může výzvu restartování v případě není spuštěn jako správce.</span><span class="sxs-lookup"><span data-stu-id="ce130-192">Visual Studio may prompt a restart if not running as an administrator.</span></span> <span data-ttu-id="ce130-193">Pokud se zobrazí výzva, restartujte aplikaci Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="ce130-193">If prompted, restart Visual Studio.</span></span>

<span data-ttu-id="ce130-194">Pokud se používá nedůvěryhodný vývojářský certifikát, prohlížeče mohou vyžadovat vytvořte výjimku pro nedůvěryhodný certifikát.</span><span class="sxs-lookup"><span data-stu-id="ce130-194">If an untrusted development certificate is used, the browser may require you to create an exception for the untrusted certificate.</span></span>

> [!NOTE]
> <span data-ttu-id="ce130-195">Ladění verze sestavení konfigurace s [pouze můj kód](/visualstudio/debugger/just-my-code) a optimalizace kompilátoru za následek sníženými možnostmi.</span><span class="sxs-lookup"><span data-stu-id="ce130-195">Debugging a Release build configuration with [Just My Code](/visualstudio/debugger/just-my-code) and compiler optimizations results in a degraded experience.</span></span> <span data-ttu-id="ce130-196">Například nejsou přístupů bodech rozdělení.</span><span class="sxs-lookup"><span data-stu-id="ce130-196">For example, break points aren't hit.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ce130-197">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="ce130-197">Additional resources</span></span>

* [<span data-ttu-id="ce130-198">Začínáme se službou Správce služby IIS ve službě IIS</span><span class="sxs-lookup"><span data-stu-id="ce130-198">Getting Started with the IIS Manager in IIS</span></span>](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* [<span data-ttu-id="ce130-199">Hostitele ASP.NET Core ve Windows se službou IIS</span><span class="sxs-lookup"><span data-stu-id="ce130-199">Host ASP.NET Core on Windows with IIS</span></span>](xref:host-and-deploy/iis/index)
* [<span data-ttu-id="ce130-200">Úvod k modulu ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ce130-200">Introduction to ASP.NET Core Module</span></span>](xref:host-and-deploy/aspnet-core-module)
* [<span data-ttu-id="ce130-201">Referenční dokumentace k modulu ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ce130-201">ASP.NET Core Module configuration reference</span></span>](xref:host-and-deploy/aspnet-core-module)
* [<span data-ttu-id="ce130-202">Vynucení protokolu HTTPS</span><span class="sxs-lookup"><span data-stu-id="ce130-202">Enforce HTTPS</span></span>](xref:security/enforcing-ssl)
