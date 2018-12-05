---
title: Podpora služby IIS při vývoji v sadě Visual Studio pro ASP.NET Core
author: shirhatti
description: Podívejte se na podporu pro ladění aplikací ASP.NET Core, když za služby IIS a systémem Windows Server.
ms.author: riande
ms.custom: mvc
ms.date: 11/30/2018
uid: host-and-deploy/iis/development-time-iis-support
ms.openlocfilehash: 51375e6a6bb25a469d467ca97a151abd305c1ece
ms.sourcegitcommit: 9bb58d7c8dad4bbd03419bcc183d027667fefa20
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52862379"
---
# <a name="development-time-iis-support-in-visual-studio-for-aspnet-core"></a><span data-ttu-id="9a6a8-103">Podpora služby IIS při vývoji v sadě Visual Studio pro ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9a6a8-103">Development-time IIS support in Visual Studio for ASP.NET Core</span></span>

<span data-ttu-id="9a6a8-104">Podle [Sourabh Shirhatti](https://twitter.com/sshirhatti) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="9a6a8-104">By [Sourabh Shirhatti](https://twitter.com/sshirhatti) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="9a6a8-105">Tento článek popisuje [sady Visual Studio](https://www.visualstudio.com/vs/) podporuje ladění aplikací ASP.NET Core spuštěné za nástrojem služby IIS v systému Windows Server.</span><span class="sxs-lookup"><span data-stu-id="9a6a8-105">This article describes [Visual Studio](https://www.visualstudio.com/vs/) support for debugging ASP.NET Core apps running behind IIS on Windows Server.</span></span> <span data-ttu-id="9a6a8-106">Toto téma vás provede povolením této funkce a nastavení projektu.</span><span class="sxs-lookup"><span data-stu-id="9a6a8-106">This topic walks through enabling this feature and setting up a project.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="9a6a8-107">Požadavky</span><span class="sxs-lookup"><span data-stu-id="9a6a8-107">Prerequisites</span></span>

* [<span data-ttu-id="9a6a8-108">Visual Studio pro Windows</span><span class="sxs-lookup"><span data-stu-id="9a6a8-108">Visual Studio for Windows</span></span>](https://www.microsoft.com/net/download/windows)
* <span data-ttu-id="9a6a8-109">**Vývoj pro ASP.NET a web** pracovního vytížení</span><span class="sxs-lookup"><span data-stu-id="9a6a8-109">**ASP.NET and web development** workload</span></span>
* <span data-ttu-id="9a6a8-110">**Vývoj pro různé platformy .NET core** pracovního vytížení</span><span class="sxs-lookup"><span data-stu-id="9a6a8-110">**.NET Core cross-platform development** workload</span></span>
* <span data-ttu-id="9a6a8-111">Bezpečnostní certifikát X.509</span><span class="sxs-lookup"><span data-stu-id="9a6a8-111">X.509 security certificate</span></span>

## <a name="enable-iis"></a><span data-ttu-id="9a6a8-112">Povolte službu IIS</span><span class="sxs-lookup"><span data-stu-id="9a6a8-112">Enable IIS</span></span>

1. <span data-ttu-id="9a6a8-113">Přejděte do **ovládací panely** > **programy** > **programy a funkce** > **zapnout Windows funkce na nebo vypnout** (levé straně obrazovky).</span><span class="sxs-lookup"><span data-stu-id="9a6a8-113">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="9a6a8-114">Vyberte **Internetová informační služba** zaškrtávací políčko.</span><span class="sxs-lookup"><span data-stu-id="9a6a8-114">Select the **Internet Information Services** check box.</span></span>

![Funkce Windows zobrazující Internetová informační služba zaškrtávací políčko zaškrtnuto jako černá čtverec (ne zaškrtnutí) označující, že jsou povolené některé z funkcí služby IIS](development-time-iis-support/_static/enable_iis.png)

<span data-ttu-id="9a6a8-116">Instalace služby IIS může vyžadovat restartování systému.</span><span class="sxs-lookup"><span data-stu-id="9a6a8-116">The IIS installation may require a system restart.</span></span>

## <a name="configure-iis"></a><span data-ttu-id="9a6a8-117">Konfigurace služby IIS</span><span class="sxs-lookup"><span data-stu-id="9a6a8-117">Configure IIS</span></span>

<span data-ttu-id="9a6a8-118">Služba IIS musí mít web nakonfigurované tyto parametry:</span><span class="sxs-lookup"><span data-stu-id="9a6a8-118">IIS must have a website configured with the following:</span></span>

* <span data-ttu-id="9a6a8-119">Název hostitele, který odpovídá název hostitele adresy URL profilu spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="9a6a8-119">A host name that matches the app's launch profile URL host name.</span></span>
* <span data-ttu-id="9a6a8-120">Vazby portu 443 s certifikátem přiřazené.</span><span class="sxs-lookup"><span data-stu-id="9a6a8-120">Binding for port 443 with an assigned certificate.</span></span>

<span data-ttu-id="9a6a8-121">Například **název hostitele** pro přidání webu je nastaven na hodnotu "localhost" (profil spuštění také používat "localhost" dále v tomto tématu).</span><span class="sxs-lookup"><span data-stu-id="9a6a8-121">For example, the **Host name** for an added website is set to "localhost" (the launch profile will also use "localhost" later in this topic).</span></span> <span data-ttu-id="9a6a8-122">Port je nastavena na hodnotu "443" (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="9a6a8-122">The port is set to "443" (HTTPS).</span></span> <span data-ttu-id="9a6a8-123">**Služby IIS Express vývojářský certifikát, kterým** je přiřazen k webu, ale libovolný platný certifikát funguje:</span><span class="sxs-lookup"><span data-stu-id="9a6a8-123">The **IIS Express Development Certificate** is assigned to the website, but any valid certificate works:</span></span>

![Přidáte okno webu ve službě IIS, zobrazuje se sada vazby pro místního hostitele na portu 443 s certifikátem přiřazené.](development-time-iis-support/_static/add-website-window.png)

<span data-ttu-id="9a6a8-125">Pokud má již instalace služby IIS **výchozí webový server** s názvem hostitele, který odpovídá název hostitele adresy URL profilu spuštění aplikace:</span><span class="sxs-lookup"><span data-stu-id="9a6a8-125">If the IIS installation already has a **Default Web Site** with a host name that matches the app's launch profile URL host name:</span></span>

* <span data-ttu-id="9a6a8-126">Přidáte vazbu na port pro port 443 (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="9a6a8-126">Add a port binding for port 443 (HTTPS).</span></span>
* <span data-ttu-id="9a6a8-127">Přiřaďte platného certifikátu k webu.</span><span class="sxs-lookup"><span data-stu-id="9a6a8-127">Assign a valid certificate to the website.</span></span>

## <a name="enable-development-time-iis-support-in-visual-studio"></a><span data-ttu-id="9a6a8-128">Povolit podporu služby IIS dobu vývoje v sadě Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9a6a8-128">Enable development-time IIS support in Visual Studio</span></span>

1. <span data-ttu-id="9a6a8-129">Spusťte instalační program sady Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="9a6a8-129">Launch the Visual Studio installer.</span></span>
1. <span data-ttu-id="9a6a8-130">Vyberte **dobu vývoje podpora služby IIS** komponenty.</span><span class="sxs-lookup"><span data-stu-id="9a6a8-130">Select the **Development time IIS support** component.</span></span> <span data-ttu-id="9a6a8-131">Součást je uveden jako volitelný v **Souhrn** panelu **vývoj pro ASP.NET a web** pracovního vytížení.</span><span class="sxs-lookup"><span data-stu-id="9a6a8-131">The component is listed as optional in the **Summary** panel for the **ASP.NET and web development** workload.</span></span> <span data-ttu-id="9a6a8-132">Nainstaluje komponenty [modul ASP.NET Core](xref:fundamentals/servers/aspnet-core-module), což je nativní modul IIS potřebné ke spuštění aplikace ASP.NET Core se službou IIS.</span><span class="sxs-lookup"><span data-stu-id="9a6a8-132">The component installs the [ASP.NET Core Module](xref:fundamentals/servers/aspnet-core-module), which is a native IIS module required to run ASP.NET Core apps with IIS.</span></span>

![Úpravy funkce aplikace Visual Studio: je vybraná karta The úlohy.](development-time-iis-support/_static/development_time_support.png)

## <a name="configure-the-project"></a><span data-ttu-id="9a6a8-136">Konfigurace projektu</span><span class="sxs-lookup"><span data-stu-id="9a6a8-136">Configure the project</span></span>

### <a name="https-redirection"></a><span data-ttu-id="9a6a8-137">Přesměrování protokolu HTTPS</span><span class="sxs-lookup"><span data-stu-id="9a6a8-137">HTTPS redirection</span></span>

<span data-ttu-id="9a6a8-138">Nový projekt, zaškrtněte políčko pro **konfigurace pro protokol HTTPS** v **nová webová aplikace ASP.NET Core** okno:</span><span class="sxs-lookup"><span data-stu-id="9a6a8-138">For a new project, select the check box to **Configure for HTTPS** in the **New ASP.NET Core Web Application** window:</span></span>

![Nové okno webové aplikace ASP.NET Core s konfigurací pro zaškrtnutým políčkem HTTPS.](development-time-iis-support/_static/new-app.png)

<span data-ttu-id="9a6a8-140">V existujícím projektu, použijte protokol HTTPS přesměrování middlewaru v `Startup.Configure` voláním [UseHttpsRedirection](/dotnet/api/microsoft.aspnetcore.builder.httpspolicybuilderextensions.usehttpsredirection) – metoda rozšíření:</span><span class="sxs-lookup"><span data-stu-id="9a6a8-140">In an existing project, use HTTPS Redirection Middleware in `Startup.Configure` by calling the [UseHttpsRedirection](/dotnet/api/microsoft.aspnetcore.builder.httpspolicybuilderextensions.usehttpsredirection) extension method:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
    }
    else
    {
        app.UseExceptionHandler("/Error");
        app.UseHsts();
    }

    app.UseHttpsRedirection();
    app.UseStaticFiles();
    app.UseCookiePolicy();

    app.UseMvc();
}
```

### <a name="iis-launch-profile"></a><span data-ttu-id="9a6a8-141">Profil spuštění služby IIS</span><span class="sxs-lookup"><span data-stu-id="9a6a8-141">IIS launch profile</span></span>

<span data-ttu-id="9a6a8-142">Vytvoření nového profilu spuštění přidává dobu vývoje služby IIS:</span><span class="sxs-lookup"><span data-stu-id="9a6a8-142">Create a new launch profile to add development-time IIS support:</span></span>

1. <span data-ttu-id="9a6a8-143">Pro **profilu**, vyberte **nový** tlačítko.</span><span class="sxs-lookup"><span data-stu-id="9a6a8-143">For **Profile**, select the **New** button.</span></span> <span data-ttu-id="9a6a8-144">Název profilu "IIS" v automaticky otevíraném okně.</span><span class="sxs-lookup"><span data-stu-id="9a6a8-144">Name the profile "IIS" in the popup window.</span></span> <span data-ttu-id="9a6a8-145">Vyberte **OK** vytvořte profil.</span><span class="sxs-lookup"><span data-stu-id="9a6a8-145">Select **OK** to create the profile.</span></span>
1. <span data-ttu-id="9a6a8-146">Pro **spuštění** vyberte **IIS** ze seznamu.</span><span class="sxs-lookup"><span data-stu-id="9a6a8-146">For the **Launch** setting, select **IIS** from the list.</span></span>
1. <span data-ttu-id="9a6a8-147">Zaškrtněte políčko pro **spuštění prohlížeče** a zadejte adresu URL koncového bodu.</span><span class="sxs-lookup"><span data-stu-id="9a6a8-147">Select the check box for **Launch browser** and provide the endpoint URL.</span></span> <span data-ttu-id="9a6a8-148">Použijte protokol HTTPS.</span><span class="sxs-lookup"><span data-stu-id="9a6a8-148">Use the HTTPS protocol.</span></span> <span data-ttu-id="9a6a8-149">Tento příklad používá `https://localhost/WebApplication1`.</span><span class="sxs-lookup"><span data-stu-id="9a6a8-149">This example uses `https://localhost/WebApplication1`.</span></span>
1. <span data-ttu-id="9a6a8-150">V **proměnné prostředí** vyberte **přidat** tlačítko.</span><span class="sxs-lookup"><span data-stu-id="9a6a8-150">In the **Environment variables** section, select the **Add** button.</span></span> <span data-ttu-id="9a6a8-151">Zadejte proměnnou prostředí s klíčem `ASPNETCORE_ENVIRONMENT` a hodnotu `Development`.</span><span class="sxs-lookup"><span data-stu-id="9a6a8-151">Provide an environment variable with a key of `ASPNETCORE_ENVIRONMENT` and a value of `Development`.</span></span>
1. <span data-ttu-id="9a6a8-152">V **nastavení webového serveru** nastavení oblasti **adresa URL aplikace**.</span><span class="sxs-lookup"><span data-stu-id="9a6a8-152">In the **Web Server Settings** area, set the **App URL**.</span></span> <span data-ttu-id="9a6a8-153">Tento příklad používá `https://localhost/WebApplication1`.</span><span class="sxs-lookup"><span data-stu-id="9a6a8-153">This example uses `https://localhost/WebApplication1`.</span></span>
1. <span data-ttu-id="9a6a8-154">Uložte profil.</span><span class="sxs-lookup"><span data-stu-id="9a6a8-154">Save the profile.</span></span>

![V okně vlastností projektu s vybranou kartou ladění.](development-time-iis-support/_static/project_properties.png)

<span data-ttu-id="9a6a8-159">Můžete také ručně přidat profil spuštění, který [launchSettings.json](http://json.schemastore.org/launchsettings) souboru v aplikaci:</span><span class="sxs-lookup"><span data-stu-id="9a6a8-159">Alternatively, manually add a launch profile to the [launchSettings.json](http://json.schemastore.org/launchsettings) file in the app:</span></span>

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

## <a name="run-the-project"></a><span data-ttu-id="9a6a8-160">Spusťte projekt</span><span class="sxs-lookup"><span data-stu-id="9a6a8-160">Run the project</span></span>

<span data-ttu-id="9a6a8-161">V sadě Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="9a6a8-161">In Visual Studio:</span></span>

* <span data-ttu-id="9a6a8-162">Potvrďte, že je nastavena rozevíracím seznamu konfigurací sestavení **ladění**.</span><span class="sxs-lookup"><span data-stu-id="9a6a8-162">Confirm that the build configuration drop-down list is set to **Debug**.</span></span>
* <span data-ttu-id="9a6a8-163">Nastavte na tlačítko Spustit **IIS** profilu a klikněte na tlačítko a spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="9a6a8-163">Set the Run button to the **IIS** profile and select the button to start the app.</span></span>

![Tlačítko spustit na panelu nástrojů VS je nastavena na profilaci služby IIS s rozevíracím seznamu konfigurací sestavení nastavená na vydání.](development-time-iis-support/_static/toolbar.png)

<span data-ttu-id="9a6a8-165">Visual Studio může výzvu restartování v případě není spuštěn jako správce.</span><span class="sxs-lookup"><span data-stu-id="9a6a8-165">Visual Studio may prompt a restart if not running as an administrator.</span></span> <span data-ttu-id="9a6a8-166">Pokud se zobrazí výzva, restartujte aplikaci Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="9a6a8-166">If prompted, restart Visual Studio.</span></span>

<span data-ttu-id="9a6a8-167">Pokud se používá nedůvěryhodný vývojářský certifikát, prohlížeče mohou vyžadovat vytvořte výjimku pro nedůvěryhodný certifikát.</span><span class="sxs-lookup"><span data-stu-id="9a6a8-167">If an untrusted development certificate is used, the browser may require you to create an exception for the untrusted certificate.</span></span>

> [!NOTE]
> <span data-ttu-id="9a6a8-168">Ladění verze sestavení konfigurace s [pouze můj kód](/visualstudio/debugger/just-my-code) a optimalizace kompilátoru za následek sníženými možnostmi.</span><span class="sxs-lookup"><span data-stu-id="9a6a8-168">Debugging a Release build configuration with [Just My Code](/visualstudio/debugger/just-my-code) and compiler optimizations results in a degraded experience.</span></span> <span data-ttu-id="9a6a8-169">Například nejsou přístupů bodech rozdělení.</span><span class="sxs-lookup"><span data-stu-id="9a6a8-169">For example, break points aren't hit.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9a6a8-170">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="9a6a8-170">Additional resources</span></span>

* [<span data-ttu-id="9a6a8-171">Hostitele ASP.NET Core ve Windows se službou IIS</span><span class="sxs-lookup"><span data-stu-id="9a6a8-171">Host ASP.NET Core on Windows with IIS</span></span>](xref:host-and-deploy/iis/index)
* [<span data-ttu-id="9a6a8-172">Úvod k modulu ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9a6a8-172">Introduction to ASP.NET Core Module</span></span>](xref:fundamentals/servers/aspnet-core-module)
* [<span data-ttu-id="9a6a8-173">Referenční dokumentace k modulu ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9a6a8-173">ASP.NET Core Module configuration reference</span></span>](xref:host-and-deploy/aspnet-core-module)
* [<span data-ttu-id="9a6a8-174">Vynucení protokolu HTTPS</span><span class="sxs-lookup"><span data-stu-id="9a6a8-174">Enforce HTTPS</span></span>](xref:security/enforcing-ssl)
