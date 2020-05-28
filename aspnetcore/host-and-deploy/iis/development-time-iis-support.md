---
<span data-ttu-id="54295-101">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="54295-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54295-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54295-102">'Blazor'</span></span>
- <span data-ttu-id="54295-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54295-103">'Identity'</span></span>
- <span data-ttu-id="54295-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54295-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="54295-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54295-105">'Razor'</span></span>
- <span data-ttu-id="54295-106">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="54295-106">'SignalR' uid:</span></span> 

---
# <a name="development-time-iis-support-in-visual-studio-for-aspnet-core"></a><span data-ttu-id="54295-107">Podpora služby IIS při vývoji v sadě Visual Studio pro ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="54295-107">Development-time IIS support in Visual Studio for ASP.NET Core</span></span>

<span data-ttu-id="54295-108">Od [sourabh Shirhatti](https://twitter.com/sshirhatti)</span><span class="sxs-lookup"><span data-stu-id="54295-108">By [Sourabh Shirhatti](https://twitter.com/sshirhatti)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="54295-109">Tento článek popisuje podporu sady [Visual Studio](https://visualstudio.microsoft.com) pro ladění ASP.NET Core aplikací běžících na Windows serveru se službou IIS.</span><span class="sxs-lookup"><span data-stu-id="54295-109">This article describes [Visual Studio](https://visualstudio.microsoft.com) support for debugging ASP.NET Core apps running with IIS on Windows Server.</span></span> <span data-ttu-id="54295-110">Toto téma vás provede povolením tohoto scénáře a nastavením projektu.</span><span class="sxs-lookup"><span data-stu-id="54295-110">This topic walks through enabling this scenario and setting up a project.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="54295-111">Požadavky</span><span class="sxs-lookup"><span data-stu-id="54295-111">Prerequisites</span></span>

* [<span data-ttu-id="54295-112">Visual Studio pro Windows</span><span class="sxs-lookup"><span data-stu-id="54295-112">Visual Studio for Windows</span></span>](https://visualstudio.microsoft.com/downloads/)
* <span data-ttu-id="54295-113">**ASP.NET a webové vývojové** úlohy</span><span class="sxs-lookup"><span data-stu-id="54295-113">**ASP.NET and web development** workload</span></span>
* <span data-ttu-id="54295-114">**Vývojová úloha .NET Core pro různé platformy**</span><span class="sxs-lookup"><span data-stu-id="54295-114">**.NET Core cross-platform development** workload</span></span>
* <span data-ttu-id="54295-115">Certifikát zabezpečení X. 509 (pro podporu protokolu HTTPS)</span><span class="sxs-lookup"><span data-stu-id="54295-115">X.509 security certificate (for HTTPS support)</span></span>

## <a name="enable-iis"></a><span data-ttu-id="54295-116">Povolení služby IIS</span><span class="sxs-lookup"><span data-stu-id="54295-116">Enable IIS</span></span>

1. <span data-ttu-id="54295-117">V systému Windows přejděte do **ovládacích panelů** > **programy** programy > **a funkce** > **zapnout nebo vypnout funkce systému Windows** (levá strana obrazovky).</span><span class="sxs-lookup"><span data-stu-id="54295-117">In Windows, navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="54295-118">Zaškrtněte políčko **Internetová informační služba** .</span><span class="sxs-lookup"><span data-stu-id="54295-118">Select the **Internet Information Services** check box.</span></span> <span data-ttu-id="54295-119">Vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="54295-119">Select **OK**.</span></span>

<span data-ttu-id="54295-120">Instalace služby IIS může vyžadovat restartování systému.</span><span class="sxs-lookup"><span data-stu-id="54295-120">The IIS installation may require a system restart.</span></span>

## <a name="configure-iis"></a><span data-ttu-id="54295-121">Konfigurace služby IIS</span><span class="sxs-lookup"><span data-stu-id="54295-121">Configure IIS</span></span>

<span data-ttu-id="54295-122">Služba IIS musí mít nakonfigurovaný web s následujícím:</span><span class="sxs-lookup"><span data-stu-id="54295-122">IIS must have a website configured with the following:</span></span>

* <span data-ttu-id="54295-123">**Název hostitele**: obvykle se používá **výchozí web** s **názvem hostitele** `localhost` .</span><span class="sxs-lookup"><span data-stu-id="54295-123">**Host name**: Typically, the **Default Web Site** is used with a **Host name** of `localhost`.</span></span> <span data-ttu-id="54295-124">Libovolný platný web služby IIS, který má jedinečný název hostitele, ale funguje.</span><span class="sxs-lookup"><span data-stu-id="54295-124">However, any valid IIS website with a unique host name works.</span></span>
* <span data-ttu-id="54295-125">**Vazba webu**</span><span class="sxs-lookup"><span data-stu-id="54295-125">**Site Binding**</span></span>
  * <span data-ttu-id="54295-126">Pro aplikace, které vyžadují protokol HTTPS, vytvořte vazbu na port 443 s certifikátem.</span><span class="sxs-lookup"><span data-stu-id="54295-126">For apps that require HTTPS, create a binding to port 443 with a certificate.</span></span> <span data-ttu-id="54295-127">Obvykle se používá **IIS Express certifikát pro vývoj** , ale jakýkoli platný certifikát funguje.</span><span class="sxs-lookup"><span data-stu-id="54295-127">Typically, the **IIS Express Development Certificate** is used, but any valid certificate works.</span></span>
  * <span data-ttu-id="54295-128">U aplikací, které používají protokol HTTP, potvrďte existenci vazby k odeslání 80 nebo vytvořte vazbu na port 80 pro novou lokalitu.</span><span class="sxs-lookup"><span data-stu-id="54295-128">For apps that use HTTP, confirm the existence of a binding to post 80 or create a binding to port 80 for a new site.</span></span>
  * <span data-ttu-id="54295-129">Použijte jednu vazbu pro HTTP nebo HTTPS.</span><span class="sxs-lookup"><span data-stu-id="54295-129">Use a single binding for either HTTP or HTTPS.</span></span> <span data-ttu-id="54295-130">**Vazba na oba porty HTTP i HTTPS současně není podporována.**</span><span class="sxs-lookup"><span data-stu-id="54295-130">**Binding to both HTTP and HTTPS ports simultaneously isn't supported.**</span></span>

## <a name="enable-development-time-iis-support-in-visual-studio"></a><span data-ttu-id="54295-131">Povolení podpory služby IIS v době vývoje v aplikaci Visual Studio</span><span class="sxs-lookup"><span data-stu-id="54295-131">Enable development-time IIS support in Visual Studio</span></span>

1. <span data-ttu-id="54295-132">Spusťte instalační program sady Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="54295-132">Launch the Visual Studio installer.</span></span>
1. <span data-ttu-id="54295-133">Vyberte možnost **Upravit** pro instalaci sady Visual Studio, kterou chcete použít pro podporu při vývoji služby IIS.</span><span class="sxs-lookup"><span data-stu-id="54295-133">Select **Modify** for the Visual Studio installation that you plan to use for IIS development-time support.</span></span>
1. <span data-ttu-id="54295-134">Pro úlohu **vývoje ASP.NET a webu** vyhledejte a nainstalujte komponentu **podpory služby IIS pro vývoj** .</span><span class="sxs-lookup"><span data-stu-id="54295-134">For the **ASP.NET and web development** workload, locate and install the **Development time IIS support** component.</span></span>

   <span data-ttu-id="54295-135">Tato součást je uvedena v části **volitelný** oddíl v části **čas vývoje** , který je podporován na panelu **podrobností o instalaci** v napravo od úloh.</span><span class="sxs-lookup"><span data-stu-id="54295-135">The component is listed in the **Optional** section under **Development time IIS support** in the **Installation details** panel to the right of the workloads.</span></span> <span data-ttu-id="54295-136">Komponenta nainstaluje [modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module), což je NATIVNÍ modul IIS vyžadovaný ke spouštění ASP.NET Core aplikací se službou IIS.</span><span class="sxs-lookup"><span data-stu-id="54295-136">The component installs the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), which is a native IIS module required to run ASP.NET Core apps with IIS.</span></span>

## <a name="configure-the-project"></a><span data-ttu-id="54295-137">Konfigurace projektu</span><span class="sxs-lookup"><span data-stu-id="54295-137">Configure the project</span></span>

### <a name="https-redirection"></a><span data-ttu-id="54295-138">Přesměrování HTTPS</span><span class="sxs-lookup"><span data-stu-id="54295-138">HTTPS redirection</span></span>

<span data-ttu-id="54295-139">Pro nový projekt, který vyžaduje protokol HTTPS, zaškrtněte políčko pro **konfiguraci HTTPS** v okně **vytvořit novou ASP.NET Core webovou aplikaci** .</span><span class="sxs-lookup"><span data-stu-id="54295-139">For a new project that requires HTTPS, select the check box to **Configure for HTTPS** in the **Create a new ASP.NET Core Web Application** window.</span></span> <span data-ttu-id="54295-140">Zaškrtnutím políčka přidáte [Přesměrování protokolu HTTPS a HSTS middleware](xref:security/enforcing-ssl) do aplikace, když se vytvoří.</span><span class="sxs-lookup"><span data-stu-id="54295-140">Selecting the check box adds [HTTPS Redirection and HSTS Middleware](xref:security/enforcing-ssl) to the app when it's created.</span></span>

<span data-ttu-id="54295-141">U existujícího projektu, který vyžaduje HTTPS, použijte přesměrování HTTPS a middleware HSTS v `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="54295-141">For an existing project that requires HTTPS, use HTTPS Redirection and HSTS Middleware in `Startup.Configure`.</span></span> <span data-ttu-id="54295-142">Další informace naleznete v tématu <xref:security/enforcing-ssl>.</span><span class="sxs-lookup"><span data-stu-id="54295-142">For more information, see <xref:security/enforcing-ssl>.</span></span>

<span data-ttu-id="54295-143">Pro projekt, který používá protokol HTTP, [přesměrování https a middleware HSTS](xref:security/enforcing-ssl) nejsou přidány do aplikace.</span><span class="sxs-lookup"><span data-stu-id="54295-143">For a project that uses HTTP, [HTTPS Redirection and HSTS Middleware](xref:security/enforcing-ssl) aren't added to the app.</span></span> <span data-ttu-id="54295-144">Není nutná žádná konfigurace aplikace.</span><span class="sxs-lookup"><span data-stu-id="54295-144">No app configuration is required.</span></span>

### <a name="iis-launch-profile"></a><span data-ttu-id="54295-145">Profil spuštění služby IIS</span><span class="sxs-lookup"><span data-stu-id="54295-145">IIS launch profile</span></span>

<span data-ttu-id="54295-146">Vytvoření nového spouštěcího profilu pro přidání podpory služby IIS v době vývoje:</span><span class="sxs-lookup"><span data-stu-id="54295-146">Create a new launch profile to add development-time IIS support:</span></span>

1. <span data-ttu-id="54295-147">Klikněte pravým tlačítkem na projekt v **Průzkumník řešení**.</span><span class="sxs-lookup"><span data-stu-id="54295-147">Right-click the project in **Solution Explorer**.</span></span> <span data-ttu-id="54295-148">Vyberte **Vlastnosti**.</span><span class="sxs-lookup"><span data-stu-id="54295-148">Select **Properties**.</span></span> <span data-ttu-id="54295-149">Otevřete kartu **ladění** .</span><span class="sxs-lookup"><span data-stu-id="54295-149">Open the **Debug** tab.</span></span>
1. <span data-ttu-id="54295-150">Pro možnost **profil**vyberte tlačítko **Nový** .</span><span class="sxs-lookup"><span data-stu-id="54295-150">For **Profile**, select the **New** button.</span></span> <span data-ttu-id="54295-151">Pojmenujte profil "IIS" v místním okně.</span><span class="sxs-lookup"><span data-stu-id="54295-151">Name the profile "IIS" in the popup window.</span></span> <span data-ttu-id="54295-152">Vyberte **OK** a vytvořte profil.</span><span class="sxs-lookup"><span data-stu-id="54295-152">Select **OK** to create the profile.</span></span>
1. <span data-ttu-id="54295-153">V nastavení **spuštění** vyberte ze seznamu **službu IIS** .</span><span class="sxs-lookup"><span data-stu-id="54295-153">For the **Launch** setting, select **IIS** from the list.</span></span>
1. <span data-ttu-id="54295-154">Zaškrtněte políčko pro **spuštění prohlížeče** a zadejte adresu URL koncového bodu.</span><span class="sxs-lookup"><span data-stu-id="54295-154">Select the check box for **Launch browser** and provide the endpoint URL.</span></span>

   <span data-ttu-id="54295-155">Když aplikace vyžaduje HTTPS, použijte koncový bod HTTPS ( `https://` ).</span><span class="sxs-lookup"><span data-stu-id="54295-155">When the app requires HTTPS, use an HTTPS endpoint (`https://`).</span></span> <span data-ttu-id="54295-156">Pro protokol HTTP použijte `http://` koncový bod HTTP ().</span><span class="sxs-lookup"><span data-stu-id="54295-156">For HTTP, use an HTTP (`http://`) endpoint.</span></span>

   <span data-ttu-id="54295-157">Zadejte stejný název hostitele a port, protože [Konfigurace služby IIS zadaná dříve používá](#configure-iis)obvykle `localhost` .</span><span class="sxs-lookup"><span data-stu-id="54295-157">Provide the same host name and port as the [IIS configuration specified earlier uses](#configure-iis), typically `localhost`.</span></span>

   <span data-ttu-id="54295-158">Zadejte název aplikace na konci adresy URL.</span><span class="sxs-lookup"><span data-stu-id="54295-158">Provide the name of the app at the end of the URL.</span></span>

   <span data-ttu-id="54295-159">Například `https://localhost/WebApplication1` (https) nebo `http://localhost/WebApplication1` (http) jsou platné adresy URL koncového bodu.</span><span class="sxs-lookup"><span data-stu-id="54295-159">For example, `https://localhost/WebApplication1` (HTTPS) or `http://localhost/WebApplication1` (HTTP) are valid endpoint URLs.</span></span>
1. <span data-ttu-id="54295-160">V části **proměnné prostředí** vyberte tlačítko **Přidat** .</span><span class="sxs-lookup"><span data-stu-id="54295-160">In the **Environment variables** section, select the **Add** button.</span></span> <span data-ttu-id="54295-161">Zadejte proměnnou prostředí s **názvem** `ASPNETCORE_ENVIRONMENT` a **hodnotou** `Development` .</span><span class="sxs-lookup"><span data-stu-id="54295-161">Provide an environment variable with a **Name** of `ASPNETCORE_ENVIRONMENT` and a **Value** of `Development`.</span></span>
1. <span data-ttu-id="54295-162">V oblasti **nastavení webového serveru** nastavte **adresu URL aplikace** na stejnou hodnotu, která se používá pro adresu URL koncového bodu **spouštěcího prohlížeče** .</span><span class="sxs-lookup"><span data-stu-id="54295-162">In the **Web Server Settings** area, set the **App URL** to the same value used for the **Launch browser** endpoint URL.</span></span>
1. <span data-ttu-id="54295-163">Pro nastavení **modelu hostování** v aplikaci Visual Studio 2019 nebo novější vyberte možnost **výchozí** pro použití modelu hostování používaného projektem.</span><span class="sxs-lookup"><span data-stu-id="54295-163">For the **Hosting Model** setting in Visual Studio 2019 or later, select **Default** to use the hosting model used by the project.</span></span> <span data-ttu-id="54295-164">Pokud projekt nastaví `<AspNetCoreHostingModel>` vlastnost v jeho souboru projektu, je použita hodnota vlastnosti ( `InProcess` nebo `OutOfProcess` ).</span><span class="sxs-lookup"><span data-stu-id="54295-164">If the project sets the `<AspNetCoreHostingModel>` property in its project file, the value of the property (`InProcess` or `OutOfProcess`) is used.</span></span> <span data-ttu-id="54295-165">Pokud vlastnost není k dispozici, použije se výchozí hostující model aplikace, který je v procesu.</span><span class="sxs-lookup"><span data-stu-id="54295-165">If the property isn't present, the default hosting model of the app is used, which is in-process.</span></span> <span data-ttu-id="54295-166">Pokud aplikace vyžaduje explicitní nastavení modelu hostování, které se liší od normálního hostujícího modelu aplikace, nastavte **hostující model** na buď `In Process` nebo `Out Of Process` podle potřeby.</span><span class="sxs-lookup"><span data-stu-id="54295-166">If the app requires an explicit hosting model setting different from the app's normal hosting model, set the **Hosting Model** to either `In Process` or `Out Of Process` as needed.</span></span>
1. <span data-ttu-id="54295-167">Uložte profil.</span><span class="sxs-lookup"><span data-stu-id="54295-167">Save the profile.</span></span>

<span data-ttu-id="54295-168">Pokud nepoužíváte aplikaci Visual Studio, ručně přidejte profil spuštění do souboru [launchSettings. JSON](https://json.schemastore.org/launchsettings) ve složce *Properties (vlastnosti* ).</span><span class="sxs-lookup"><span data-stu-id="54295-168">When not using Visual Studio, manually add a launch profile to the [launchSettings.json](https://json.schemastore.org/launchsettings) file in the *Properties* folder.</span></span> <span data-ttu-id="54295-169">Následující příklad nakonfiguruje profil pro použití protokolu HTTPS:</span><span class="sxs-lookup"><span data-stu-id="54295-169">The following example configures the profile to use the HTTPS protocol:</span></span>

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

<span data-ttu-id="54295-170">Potvrďte, `applicationUrl` že `launchUrl` koncové body a používají stejný protokol jako konfigurace vazby služby IIS, buď http, nebo HTTPS.</span><span class="sxs-lookup"><span data-stu-id="54295-170">Confirm that the `applicationUrl` and `launchUrl` endpoints match and use the same protocol as the IIS binding configuration, either HTTP or HTTPS.</span></span>

## <a name="run-the-project"></a><span data-ttu-id="54295-171">Spuštění projektu</span><span class="sxs-lookup"><span data-stu-id="54295-171">Run the project</span></span>

<span data-ttu-id="54295-172">Spusťte Visual Studio jako správce:</span><span class="sxs-lookup"><span data-stu-id="54295-172">Run Visual Studio as an administrator:</span></span>

* <span data-ttu-id="54295-173">Potvrďte, že rozevírací seznam konfigurace sestavení je nastavený na **ladit**.</span><span class="sxs-lookup"><span data-stu-id="54295-173">Confirm that the build configuration drop-down list is set to **Debug**.</span></span>
* <span data-ttu-id="54295-174">Nastavte [tlačítko Spustit ladění](/visualstudio/debugger/debugger-feature-tour) na profil **služby IIS** a kliknutím na tlačítko aplikaci spusťte.</span><span class="sxs-lookup"><span data-stu-id="54295-174">Set the [Start Debugging button](/visualstudio/debugger/debugger-feature-tour) to the **IIS** profile and select the button to start the app.</span></span>

<span data-ttu-id="54295-175">Visual Studio se může vyzvat k restartování, pokud není spuštěný jako správce.</span><span class="sxs-lookup"><span data-stu-id="54295-175">Visual Studio may prompt a restart if not running as an administrator.</span></span> <span data-ttu-id="54295-176">Pokud se zobrazí výzva, restartujte Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="54295-176">If prompted, restart Visual Studio.</span></span>

<span data-ttu-id="54295-177">Pokud se používá nedůvěryhodný vývojový certifikát, prohlížeč může vyžadovat, abyste vytvořili výjimku pro nedůvěryhodný certifikát.</span><span class="sxs-lookup"><span data-stu-id="54295-177">If an untrusted development certificate is used, the browser may require you to create an exception for the untrusted certificate.</span></span>

> [!NOTE]
> <span data-ttu-id="54295-178">Ladění konfigurace sestavení pro vydání pomocí [pouze můj kód](/visualstudio/debugger/just-my-code) a optimalizace kompilátoru mají za následek zhoršené prostředí.</span><span class="sxs-lookup"><span data-stu-id="54295-178">Debugging a Release build configuration with [Just My Code](/visualstudio/debugger/just-my-code) and compiler optimizations results in a degraded experience.</span></span> <span data-ttu-id="54295-179">Například body přerušení nejsou k dispozice.</span><span class="sxs-lookup"><span data-stu-id="54295-179">For example, break points aren't hit.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="54295-180">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="54295-180">Additional resources</span></span>

* [<span data-ttu-id="54295-181">Začínáme ve službě IIS pomocí Správce služby IIS</span><span class="sxs-lookup"><span data-stu-id="54295-181">Getting Started with the IIS Manager in IIS</span></span>](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* <xref:security/enforcing-ssl>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="54295-182">Tento článek popisuje podporu sady [Visual Studio](https://visualstudio.microsoft.com) pro ladění ASP.NET Core aplikací běžících na Windows serveru se službou IIS.</span><span class="sxs-lookup"><span data-stu-id="54295-182">This article describes [Visual Studio](https://visualstudio.microsoft.com) support for debugging ASP.NET Core apps running with IIS on Windows Server.</span></span> <span data-ttu-id="54295-183">Toto téma vás provede povolením tohoto scénáře a nastavením projektu.</span><span class="sxs-lookup"><span data-stu-id="54295-183">This topic walks through enabling this scenario and setting up a project.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="54295-184">Požadavky</span><span class="sxs-lookup"><span data-stu-id="54295-184">Prerequisites</span></span>

* [<span data-ttu-id="54295-185">Visual Studio pro Windows</span><span class="sxs-lookup"><span data-stu-id="54295-185">Visual Studio for Windows</span></span>](https://visualstudio.microsoft.com/downloads/)
* <span data-ttu-id="54295-186">**ASP.NET a webové vývojové** úlohy</span><span class="sxs-lookup"><span data-stu-id="54295-186">**ASP.NET and web development** workload</span></span>
* <span data-ttu-id="54295-187">**Vývojová úloha .NET Core pro různé platformy**</span><span class="sxs-lookup"><span data-stu-id="54295-187">**.NET Core cross-platform development** workload</span></span>
* <span data-ttu-id="54295-188">Certifikát zabezpečení X. 509 (pro podporu protokolu HTTPS)</span><span class="sxs-lookup"><span data-stu-id="54295-188">X.509 security certificate (for HTTPS support)</span></span>

## <a name="enable-iis"></a><span data-ttu-id="54295-189">Povolení služby IIS</span><span class="sxs-lookup"><span data-stu-id="54295-189">Enable IIS</span></span>

1. <span data-ttu-id="54295-190">V systému Windows přejděte do **ovládacích panelů** > **programy** programy > **a funkce** > **zapnout nebo vypnout funkce systému Windows** (levá strana obrazovky).</span><span class="sxs-lookup"><span data-stu-id="54295-190">In Windows, navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="54295-191">Zaškrtněte políčko **Internetová informační služba** .</span><span class="sxs-lookup"><span data-stu-id="54295-191">Select the **Internet Information Services** check box.</span></span> <span data-ttu-id="54295-192">Vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="54295-192">Select **OK**.</span></span>

<span data-ttu-id="54295-193">Instalace služby IIS může vyžadovat restartování systému.</span><span class="sxs-lookup"><span data-stu-id="54295-193">The IIS installation may require a system restart.</span></span>

## <a name="configure-iis"></a><span data-ttu-id="54295-194">Konfigurace služby IIS</span><span class="sxs-lookup"><span data-stu-id="54295-194">Configure IIS</span></span>

<span data-ttu-id="54295-195">Služba IIS musí mít nakonfigurovaný web s následujícím:</span><span class="sxs-lookup"><span data-stu-id="54295-195">IIS must have a website configured with the following:</span></span>

* <span data-ttu-id="54295-196">**Název hostitele**: obvykle se používá **výchozí web** s **názvem hostitele** `localhost` .</span><span class="sxs-lookup"><span data-stu-id="54295-196">**Host name**: Typically, the **Default Web Site** is used with a **Host name** of `localhost`.</span></span> <span data-ttu-id="54295-197">Libovolný platný web služby IIS, který má jedinečný název hostitele, ale funguje.</span><span class="sxs-lookup"><span data-stu-id="54295-197">However, any valid IIS website with a unique host name works.</span></span>
* <span data-ttu-id="54295-198">**Vazba webu**</span><span class="sxs-lookup"><span data-stu-id="54295-198">**Site Binding**</span></span>
  * <span data-ttu-id="54295-199">Pro aplikace, které vyžadují protokol HTTPS, vytvořte vazbu na port 443 s certifikátem.</span><span class="sxs-lookup"><span data-stu-id="54295-199">For apps that require HTTPS, create a binding to port 443 with a certificate.</span></span> <span data-ttu-id="54295-200">Obvykle se používá **IIS Express certifikát pro vývoj** , ale jakýkoli platný certifikát funguje.</span><span class="sxs-lookup"><span data-stu-id="54295-200">Typically, the **IIS Express Development Certificate** is used, but any valid certificate works.</span></span>
  * <span data-ttu-id="54295-201">U aplikací, které používají protokol HTTP, potvrďte existenci vazby k odeslání 80 nebo vytvořte vazbu na port 80 pro novou lokalitu.</span><span class="sxs-lookup"><span data-stu-id="54295-201">For apps that use HTTP, confirm the existence of a binding to post 80 or create a binding to port 80 for a new site.</span></span>
  * <span data-ttu-id="54295-202">Použijte jednu vazbu pro HTTP nebo HTTPS.</span><span class="sxs-lookup"><span data-stu-id="54295-202">Use a single binding for either HTTP or HTTPS.</span></span> <span data-ttu-id="54295-203">**Vazba na oba porty HTTP i HTTPS současně není podporována.**</span><span class="sxs-lookup"><span data-stu-id="54295-203">**Binding to both HTTP and HTTPS ports simultaneously isn't supported.**</span></span>

## <a name="enable-development-time-iis-support-in-visual-studio"></a><span data-ttu-id="54295-204">Povolení podpory služby IIS v době vývoje v aplikaci Visual Studio</span><span class="sxs-lookup"><span data-stu-id="54295-204">Enable development-time IIS support in Visual Studio</span></span>

1. <span data-ttu-id="54295-205">Spusťte instalační program sady Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="54295-205">Launch the Visual Studio installer.</span></span>
1. <span data-ttu-id="54295-206">Vyberte možnost **Upravit** pro instalaci sady Visual Studio, kterou chcete použít pro podporu při vývoji služby IIS.</span><span class="sxs-lookup"><span data-stu-id="54295-206">Select **Modify** for the Visual Studio installation that you plan to use for IIS development-time support.</span></span>
1. <span data-ttu-id="54295-207">Pro úlohu **vývoje ASP.NET a webu** vyhledejte a nainstalujte komponentu **podpory služby IIS pro vývoj** .</span><span class="sxs-lookup"><span data-stu-id="54295-207">For the **ASP.NET and web development** workload, locate and install the **Development time IIS support** component.</span></span>

   <span data-ttu-id="54295-208">Tato součást je uvedena v části **volitelný** oddíl v části **čas vývoje** , který je podporován na panelu **podrobností o instalaci** v napravo od úloh.</span><span class="sxs-lookup"><span data-stu-id="54295-208">The component is listed in the **Optional** section under **Development time IIS support** in the **Installation details** panel to the right of the workloads.</span></span> <span data-ttu-id="54295-209">Komponenta nainstaluje [modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module), což je NATIVNÍ modul IIS vyžadovaný ke spouštění ASP.NET Core aplikací se službou IIS.</span><span class="sxs-lookup"><span data-stu-id="54295-209">The component installs the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), which is a native IIS module required to run ASP.NET Core apps with IIS.</span></span>

## <a name="configure-the-project"></a><span data-ttu-id="54295-210">Konfigurace projektu</span><span class="sxs-lookup"><span data-stu-id="54295-210">Configure the project</span></span>

### <a name="https-redirection"></a><span data-ttu-id="54295-211">Přesměrování HTTPS</span><span class="sxs-lookup"><span data-stu-id="54295-211">HTTPS redirection</span></span>

<span data-ttu-id="54295-212">Pro nový projekt, který vyžaduje protokol HTTPS, zaškrtněte políčko pro **konfiguraci HTTPS** v okně **vytvořit novou ASP.NET Core webovou aplikaci** .</span><span class="sxs-lookup"><span data-stu-id="54295-212">For a new project that requires HTTPS, select the check box to **Configure for HTTPS** in the **Create a new ASP.NET Core Web Application** window.</span></span> <span data-ttu-id="54295-213">Zaškrtnutím políčka přidáte [Přesměrování protokolu HTTPS a HSTS middleware](xref:security/enforcing-ssl) do aplikace, když se vytvoří.</span><span class="sxs-lookup"><span data-stu-id="54295-213">Selecting the check box adds [HTTPS Redirection and HSTS Middleware](xref:security/enforcing-ssl) to the app when it's created.</span></span>

<span data-ttu-id="54295-214">U existujícího projektu, který vyžaduje HTTPS, použijte přesměrování HTTPS a middleware HSTS v `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="54295-214">For an existing project that requires HTTPS, use HTTPS Redirection and HSTS Middleware in `Startup.Configure`.</span></span> <span data-ttu-id="54295-215">Další informace naleznete v tématu <xref:security/enforcing-ssl>.</span><span class="sxs-lookup"><span data-stu-id="54295-215">For more information, see <xref:security/enforcing-ssl>.</span></span>

<span data-ttu-id="54295-216">Pro projekt, který používá protokol HTTP, [přesměrování https a middleware HSTS](xref:security/enforcing-ssl) nejsou přidány do aplikace.</span><span class="sxs-lookup"><span data-stu-id="54295-216">For a project that uses HTTP, [HTTPS Redirection and HSTS Middleware](xref:security/enforcing-ssl) aren't added to the app.</span></span> <span data-ttu-id="54295-217">Není nutná žádná konfigurace aplikace.</span><span class="sxs-lookup"><span data-stu-id="54295-217">No app configuration is required.</span></span>

### <a name="iis-launch-profile"></a><span data-ttu-id="54295-218">Profil spuštění služby IIS</span><span class="sxs-lookup"><span data-stu-id="54295-218">IIS launch profile</span></span>

<span data-ttu-id="54295-219">Vytvoření nového spouštěcího profilu pro přidání podpory služby IIS v době vývoje:</span><span class="sxs-lookup"><span data-stu-id="54295-219">Create a new launch profile to add development-time IIS support:</span></span>

1. <span data-ttu-id="54295-220">Klikněte pravým tlačítkem na projekt v **Průzkumník řešení**.</span><span class="sxs-lookup"><span data-stu-id="54295-220">Right-click the project in **Solution Explorer**.</span></span> <span data-ttu-id="54295-221">Vyberte **Vlastnosti**.</span><span class="sxs-lookup"><span data-stu-id="54295-221">Select **Properties**.</span></span> <span data-ttu-id="54295-222">Otevřete kartu **ladění** .</span><span class="sxs-lookup"><span data-stu-id="54295-222">Open the **Debug** tab.</span></span>
1. <span data-ttu-id="54295-223">Pro možnost **profil**vyberte tlačítko **Nový** .</span><span class="sxs-lookup"><span data-stu-id="54295-223">For **Profile**, select the **New** button.</span></span> <span data-ttu-id="54295-224">Pojmenujte profil "IIS" v místním okně.</span><span class="sxs-lookup"><span data-stu-id="54295-224">Name the profile "IIS" in the popup window.</span></span> <span data-ttu-id="54295-225">Vyberte **OK** a vytvořte profil.</span><span class="sxs-lookup"><span data-stu-id="54295-225">Select **OK** to create the profile.</span></span>
1. <span data-ttu-id="54295-226">V nastavení **spuštění** vyberte ze seznamu **službu IIS** .</span><span class="sxs-lookup"><span data-stu-id="54295-226">For the **Launch** setting, select **IIS** from the list.</span></span>
1. <span data-ttu-id="54295-227">Zaškrtněte políčko pro **spuštění prohlížeče** a zadejte adresu URL koncového bodu.</span><span class="sxs-lookup"><span data-stu-id="54295-227">Select the check box for **Launch browser** and provide the endpoint URL.</span></span>

   <span data-ttu-id="54295-228">Když aplikace vyžaduje HTTPS, použijte koncový bod HTTPS ( `https://` ).</span><span class="sxs-lookup"><span data-stu-id="54295-228">When the app requires HTTPS, use an HTTPS endpoint (`https://`).</span></span> <span data-ttu-id="54295-229">Pro protokol HTTP použijte `http://` koncový bod HTTP ().</span><span class="sxs-lookup"><span data-stu-id="54295-229">For HTTP, use an HTTP (`http://`) endpoint.</span></span>

   <span data-ttu-id="54295-230">Zadejte stejný název hostitele a port, protože [Konfigurace služby IIS zadaná dříve používá](#configure-iis)obvykle `localhost` .</span><span class="sxs-lookup"><span data-stu-id="54295-230">Provide the same host name and port as the [IIS configuration specified earlier uses](#configure-iis), typically `localhost`.</span></span>

   <span data-ttu-id="54295-231">Zadejte název aplikace na konci adresy URL.</span><span class="sxs-lookup"><span data-stu-id="54295-231">Provide the name of the app at the end of the URL.</span></span>

   <span data-ttu-id="54295-232">Například `https://localhost/WebApplication1` (https) nebo `http://localhost/WebApplication1` (http) jsou platné adresy URL koncového bodu.</span><span class="sxs-lookup"><span data-stu-id="54295-232">For example, `https://localhost/WebApplication1` (HTTPS) or `http://localhost/WebApplication1` (HTTP) are valid endpoint URLs.</span></span>
1. <span data-ttu-id="54295-233">V části **proměnné prostředí** vyberte tlačítko **Přidat** .</span><span class="sxs-lookup"><span data-stu-id="54295-233">In the **Environment variables** section, select the **Add** button.</span></span> <span data-ttu-id="54295-234">Zadejte proměnnou prostředí s **názvem** `ASPNETCORE_ENVIRONMENT` a **hodnotou** `Development` .</span><span class="sxs-lookup"><span data-stu-id="54295-234">Provide an environment variable with a **Name** of `ASPNETCORE_ENVIRONMENT` and a **Value** of `Development`.</span></span>
1. <span data-ttu-id="54295-235">V oblasti **nastavení webového serveru** nastavte **adresu URL aplikace** na stejnou hodnotu, která se používá pro adresu URL koncového bodu **spouštěcího prohlížeče** .</span><span class="sxs-lookup"><span data-stu-id="54295-235">In the **Web Server Settings** area, set the **App URL** to the same value used for the **Launch browser** endpoint URL.</span></span>
1. <span data-ttu-id="54295-236">Pro nastavení **modelu hostování** v aplikaci Visual Studio 2019 nebo novější vyberte možnost **výchozí** pro použití modelu hostování používaného projektem.</span><span class="sxs-lookup"><span data-stu-id="54295-236">For the **Hosting Model** setting in Visual Studio 2019 or later, select **Default** to use the hosting model used by the project.</span></span> <span data-ttu-id="54295-237">Pokud projekt nastaví `<AspNetCoreHostingModel>` vlastnost v jeho souboru projektu, je použita hodnota vlastnosti ( `InProcess` nebo `OutOfProcess` ).</span><span class="sxs-lookup"><span data-stu-id="54295-237">If the project sets the `<AspNetCoreHostingModel>` property in its project file, the value of the property (`InProcess` or `OutOfProcess`) is used.</span></span> <span data-ttu-id="54295-238">Pokud vlastnost není k dispozici, použije se výchozí hostující model aplikace, který je mimo proces.</span><span class="sxs-lookup"><span data-stu-id="54295-238">If the property isn't present, the default hosting model of the app is used, which is out-of-process.</span></span> <span data-ttu-id="54295-239">Pokud aplikace vyžaduje explicitní nastavení modelu hostování, které se liší od normálního hostujícího modelu aplikace, nastavte **hostující model** na buď `In Process` nebo `Out Of Process` podle potřeby.</span><span class="sxs-lookup"><span data-stu-id="54295-239">If the app requires an explicit hosting model setting different from the app's normal hosting model, set the **Hosting Model** to either `In Process` or `Out Of Process` as needed.</span></span>
1. <span data-ttu-id="54295-240">Uložte profil.</span><span class="sxs-lookup"><span data-stu-id="54295-240">Save the profile.</span></span>

<span data-ttu-id="54295-241">Pokud nepoužíváte aplikaci Visual Studio, ručně přidejte profil spuštění do souboru [launchSettings. JSON](https://json.schemastore.org/launchsettings) ve složce *Properties (vlastnosti* ).</span><span class="sxs-lookup"><span data-stu-id="54295-241">When not using Visual Studio, manually add a launch profile to the [launchSettings.json](https://json.schemastore.org/launchsettings) file in the *Properties* folder.</span></span> <span data-ttu-id="54295-242">Následující příklad nakonfiguruje profil pro použití protokolu HTTPS:</span><span class="sxs-lookup"><span data-stu-id="54295-242">The following example configures the profile to use the HTTPS protocol:</span></span>

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

<span data-ttu-id="54295-243">Potvrďte, `applicationUrl` že `launchUrl` koncové body a používají stejný protokol jako konfigurace vazby služby IIS, buď http, nebo HTTPS.</span><span class="sxs-lookup"><span data-stu-id="54295-243">Confirm that the `applicationUrl` and `launchUrl` endpoints match and use the same protocol as the IIS binding configuration, either HTTP or HTTPS.</span></span>

## <a name="run-the-project"></a><span data-ttu-id="54295-244">Spuštění projektu</span><span class="sxs-lookup"><span data-stu-id="54295-244">Run the project</span></span>

<span data-ttu-id="54295-245">Spusťte Visual Studio jako správce:</span><span class="sxs-lookup"><span data-stu-id="54295-245">Run Visual Studio as an administrator:</span></span>

* <span data-ttu-id="54295-246">Potvrďte, že rozevírací seznam konfigurace sestavení je nastavený na **ladit**.</span><span class="sxs-lookup"><span data-stu-id="54295-246">Confirm that the build configuration drop-down list is set to **Debug**.</span></span>
* <span data-ttu-id="54295-247">Nastavte [tlačítko Spustit ladění](/visualstudio/debugger/debugger-feature-tour) na profil **služby IIS** a kliknutím na tlačítko aplikaci spusťte.</span><span class="sxs-lookup"><span data-stu-id="54295-247">Set the [Start Debugging button](/visualstudio/debugger/debugger-feature-tour) to the **IIS** profile and select the button to start the app.</span></span>

<span data-ttu-id="54295-248">Visual Studio se může vyzvat k restartování, pokud není spuštěný jako správce.</span><span class="sxs-lookup"><span data-stu-id="54295-248">Visual Studio may prompt a restart if not running as an administrator.</span></span> <span data-ttu-id="54295-249">Pokud se zobrazí výzva, restartujte Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="54295-249">If prompted, restart Visual Studio.</span></span>

<span data-ttu-id="54295-250">Pokud se používá nedůvěryhodný vývojový certifikát, prohlížeč může vyžadovat, abyste vytvořili výjimku pro nedůvěryhodný certifikát.</span><span class="sxs-lookup"><span data-stu-id="54295-250">If an untrusted development certificate is used, the browser may require you to create an exception for the untrusted certificate.</span></span>

> [!NOTE]
> <span data-ttu-id="54295-251">Ladění konfigurace sestavení pro vydání pomocí [pouze můj kód](/visualstudio/debugger/just-my-code) a optimalizace kompilátoru mají za následek zhoršené prostředí.</span><span class="sxs-lookup"><span data-stu-id="54295-251">Debugging a Release build configuration with [Just My Code](/visualstudio/debugger/just-my-code) and compiler optimizations results in a degraded experience.</span></span> <span data-ttu-id="54295-252">Například body přerušení nejsou k dispozice.</span><span class="sxs-lookup"><span data-stu-id="54295-252">For example, break points aren't hit.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="54295-253">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="54295-253">Additional resources</span></span>

* [<span data-ttu-id="54295-254">Začínáme ve službě IIS pomocí Správce služby IIS</span><span class="sxs-lookup"><span data-stu-id="54295-254">Getting Started with the IIS Manager in IIS</span></span>](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* <xref:security/enforcing-ssl>

::: moniker-end
