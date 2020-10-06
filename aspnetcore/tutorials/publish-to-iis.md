---
title: Publikování aplikace ASP.NET Core ve službě IIS
author: rick-anderson
description: Naučte se hostovat aplikaci ASP.NET Core na serveru IIS.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/03/2019
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/publish-to-iis
ms.openlocfilehash: 40c47da472257862414ba33be582eb19d3f0b29c
ms.sourcegitcommit: d60bfd52bfb559e805abd654b87a2a0c7eb69cf8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/06/2020
ms.locfileid: "91754551"
---
# <a name="publish-an-aspnet-core-app-to-iis"></a><span data-ttu-id="8f6eb-103">Publikování aplikace ASP.NET Core ve službě IIS</span><span class="sxs-lookup"><span data-stu-id="8f6eb-103">Publish an ASP.NET Core app to IIS</span></span>

<span data-ttu-id="8f6eb-104">V tomto kurzu se dozvíte, jak hostovat aplikaci ASP.NET Core na serveru IIS.</span><span class="sxs-lookup"><span data-stu-id="8f6eb-104">This tutorial shows how to host an ASP.NET Core app on an IIS server.</span></span>

<span data-ttu-id="8f6eb-105">Tento kurz se zabývá následujícími tématy:</span><span class="sxs-lookup"><span data-stu-id="8f6eb-105">This tutorial covers the following subjects:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="8f6eb-106">Nainstalujte hostující sadu .NET Core na Windows Server.</span><span class="sxs-lookup"><span data-stu-id="8f6eb-106">Install the .NET Core Hosting Bundle on Windows Server.</span></span>
> * <span data-ttu-id="8f6eb-107">Vytvořte web IIS ve Správci služby IIS.</span><span class="sxs-lookup"><span data-stu-id="8f6eb-107">Create an IIS site in IIS Manager.</span></span>
> * <span data-ttu-id="8f6eb-108">Nasaďte aplikaci ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="8f6eb-108">Deploy an ASP.NET Core app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="8f6eb-109">Předpoklady</span><span class="sxs-lookup"><span data-stu-id="8f6eb-109">Prerequisites</span></span>

* <span data-ttu-id="8f6eb-110">[.NET Core SDK](/dotnet/core/sdk) nainstalované na vývojovém počítači.</span><span class="sxs-lookup"><span data-stu-id="8f6eb-110">[.NET Core SDK](/dotnet/core/sdk) installed on the development machine.</span></span>
* <span data-ttu-id="8f6eb-111">Windows Server nakonfigurovaný pomocí role serveru **webový server (IIS)** .</span><span class="sxs-lookup"><span data-stu-id="8f6eb-111">Windows Server configured with the **Web Server (IIS)** server role.</span></span> <span data-ttu-id="8f6eb-112">Pokud váš server není nakonfigurovaný na hostování webů se službou IIS, postupujte podle pokynů v části *Konfigurace služby IIS* v <xref:host-and-deploy/iis/index#iis-configuration> článku a pak se vraťte k tomuto kurzu.</span><span class="sxs-lookup"><span data-stu-id="8f6eb-112">If your server isn't configured to host websites with IIS, follow the guidance in the *IIS configuration* section of the <xref:host-and-deploy/iis/index#iis-configuration> article and then return to this tutorial.</span></span>

> [!WARNING]
> <span data-ttu-id="8f6eb-113">**Konfigurace služby IIS a zabezpečení webu zahrnují koncepty, na které se tento kurz nevztahuje.**</span><span class="sxs-lookup"><span data-stu-id="8f6eb-113">**IIS configuration and website security involve concepts that aren't covered by this tutorial.**</span></span> <span data-ttu-id="8f6eb-114">Další informace najdete v dokumentaci ke službě IIS v dokumentaci ke službě [Microsoft IIS](https://www.iis.net/) a v [ASP.NET Core článku o hostování se službou IIS](xref:host-and-deploy/iis/index) před hostováním produkčních aplikací ve službě IIS.</span><span class="sxs-lookup"><span data-stu-id="8f6eb-114">Consult the IIS guidance in the [Microsoft IIS documentation](https://www.iis.net/) and the [ASP.NET Core article on hosting with IIS](xref:host-and-deploy/iis/index) before hosting production apps on IIS.</span></span>
>
> <span data-ttu-id="8f6eb-115">Důležité scénáře pro hostování služby IIS, na které se nevztahuje tento kurz, zahrnují:</span><span class="sxs-lookup"><span data-stu-id="8f6eb-115">Important scenarios for IIS hosting not covered by this tutorial include:</span></span>
>
> * [<span data-ttu-id="8f6eb-116">Vytvoření podregistru pro ochranu ASP.NET Core dat</span><span class="sxs-lookup"><span data-stu-id="8f6eb-116">Creation of a registry hive for ASP.NET Core Data Protection</span></span>](xref:host-and-deploy/iis/index#data-protection)
> * [<span data-ttu-id="8f6eb-117">Konfigurace seznamu Access Control fondu aplikací (ACL)</span><span class="sxs-lookup"><span data-stu-id="8f6eb-117">Configuration of the app pool's Access Control List (ACL)</span></span>](xref:host-and-deploy/iis/index#application-pool-identity)
> * <span data-ttu-id="8f6eb-118">V tomto kurzu nasadíte v rámci konceptů nasazení služby IIS aplikaci bez zabezpečení HTTPS nakonfigurovanou ve službě IIS.</span><span class="sxs-lookup"><span data-stu-id="8f6eb-118">To focus on IIS deployment concepts, this tutorial deploys an app without HTTPS security configured in IIS.</span></span> <span data-ttu-id="8f6eb-119">Další informace o hostování aplikace, která je povolená pro protokol HTTPS, najdete v tématech zabezpečení v části [Další zdroje](#additional-resources) tohoto článku.</span><span class="sxs-lookup"><span data-stu-id="8f6eb-119">For more information on hosting an app enabled for HTTPS protocol, see the security topics in the [Additional resources](#additional-resources) section of this article.</span></span> <span data-ttu-id="8f6eb-120">Další pokyny pro hostování ASP.NET Core aplikací jsou uvedené v <xref:host-and-deploy/iis/index> článku.</span><span class="sxs-lookup"><span data-stu-id="8f6eb-120">Further guidance for hosting ASP.NET Core apps is provided in the <xref:host-and-deploy/iis/index> article.</span></span>

## <a name="install-the-net-core-hosting-bundle"></a><span data-ttu-id="8f6eb-121">Instalace hostující sady .NET Core</span><span class="sxs-lookup"><span data-stu-id="8f6eb-121">Install the .NET Core Hosting Bundle</span></span>

<span data-ttu-id="8f6eb-122">Na server IIS nainstalujte *hostující sadu .NET Core* .</span><span class="sxs-lookup"><span data-stu-id="8f6eb-122">Install the *.NET Core Hosting Bundle* on the IIS server.</span></span> <span data-ttu-id="8f6eb-123">Svazek nainstaluje modul runtime .NET Core, knihovnu .NET Core a [modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="8f6eb-123">The bundle installs the .NET Core Runtime, .NET Core Library, and the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="8f6eb-124">Modul umožňuje, aby aplikace ASP.NET Core běžely za službou IIS.</span><span class="sxs-lookup"><span data-stu-id="8f6eb-124">The module allows ASP.NET Core apps to run behind IIS.</span></span>

<span data-ttu-id="8f6eb-125">Stáhněte instalační program pomocí následujícího odkazu:</span><span class="sxs-lookup"><span data-stu-id="8f6eb-125">Download the installer using the following link:</span></span>

[<span data-ttu-id="8f6eb-126">Aktuální instalační program sady hostujících sad .NET Core (přímé stahování)</span><span class="sxs-lookup"><span data-stu-id="8f6eb-126">Current .NET Core Hosting Bundle installer (direct download)</span></span>](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

1. <span data-ttu-id="8f6eb-127">Spusťte instalační program na serveru IIS.</span><span class="sxs-lookup"><span data-stu-id="8f6eb-127">Run the installer on the IIS server.</span></span>

1. <span data-ttu-id="8f6eb-128">Restartujte server nebo spusťte `net stop was /y` příkaz a potom `net start w3svc` v příkazovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="8f6eb-128">Restart the server or execute `net stop was /y` followed by `net start w3svc` in a command shell.</span></span>

## <a name="create-the-iis-site"></a><span data-ttu-id="8f6eb-129">Vytvoření webu služby IIS</span><span class="sxs-lookup"><span data-stu-id="8f6eb-129">Create the IIS site</span></span>

1. <span data-ttu-id="8f6eb-130">Na serveru služby IIS vytvořte složku, která bude obsahovat publikované složky a soubory aplikace.</span><span class="sxs-lookup"><span data-stu-id="8f6eb-130">On the IIS server, create a folder to contain the app's published folders and files.</span></span> <span data-ttu-id="8f6eb-131">V následujícím kroku je jako fyzická cesta k aplikaci služba IIS poskytována jako cesta k této složce.</span><span class="sxs-lookup"><span data-stu-id="8f6eb-131">In a following step, the folder's path is provided to IIS as the physical path to the app.</span></span> <span data-ttu-id="8f6eb-132">Další informace o složce nasazení aplikace a rozložení souborů naleznete v tématu <xref:host-and-deploy/directory-structure> .</span><span class="sxs-lookup"><span data-stu-id="8f6eb-132">For more information on an app's deployment folder and file layout, see <xref:host-and-deploy/directory-structure>.</span></span>

1. <span data-ttu-id="8f6eb-133">Ve Správci služby IIS otevřete uzel serveru na panelu **připojení** .</span><span class="sxs-lookup"><span data-stu-id="8f6eb-133">In IIS Manager, open the server's node in the **Connections** panel.</span></span> <span data-ttu-id="8f6eb-134">Klikněte pravým tlačítkem na složku **weby** .</span><span class="sxs-lookup"><span data-stu-id="8f6eb-134">Right-click the **Sites** folder.</span></span> <span data-ttu-id="8f6eb-135">V místní nabídce vyberte **Přidat web** .</span><span class="sxs-lookup"><span data-stu-id="8f6eb-135">Select **Add Website** from the contextual menu.</span></span>

1. <span data-ttu-id="8f6eb-136">Zadejte **název lokality** a nastavte **fyzickou cestu** ke složce pro nasazení aplikace, kterou jste vytvořili.</span><span class="sxs-lookup"><span data-stu-id="8f6eb-136">Provide a **Site name** and set the **Physical path** to the app's deployment folder that you created.</span></span> <span data-ttu-id="8f6eb-137">Zadejte konfiguraci **vazby** a vytvořte web výběrem **OK**.</span><span class="sxs-lookup"><span data-stu-id="8f6eb-137">Provide the **Binding** configuration and create the website by selecting **OK**.</span></span>

   > [!WARNING]
   > <span data-ttu-id="8f6eb-138">Nelze použít vazby zástupných znaků na nejvyšší úrovni ( `http://*:80/` a `http://+:80` ). **not**</span><span class="sxs-lookup"><span data-stu-id="8f6eb-138">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="8f6eb-139">Vazby zástupných znaků nejvyšší úrovně můžou aplikaci otevřít pro slabá místa zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="8f6eb-139">Top-level wildcard bindings can open up your app to security vulnerabilities.</span></span> <span data-ttu-id="8f6eb-140">To platí pro silné i slabé zástupné znaky.</span><span class="sxs-lookup"><span data-stu-id="8f6eb-140">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="8f6eb-141">Místo zástupných znaků použijte explicitní názvy hostitelů.</span><span class="sxs-lookup"><span data-stu-id="8f6eb-141">Use explicit host names rather than wildcards.</span></span> <span data-ttu-id="8f6eb-142">Vazba zástupných znaků subdomény (například `*.mysub.com` ) nemá toto bezpečnostní riziko, pokud ovládáte celou nadřazenou doménu (na rozdíl od `*.com` , která je zranitelná).</span><span class="sxs-lookup"><span data-stu-id="8f6eb-142">Subdomain wildcard binding (for example, `*.mysub.com`) doesn't have this security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="8f6eb-143">Další informace najdete v [části rfc7230 část-5,4](https://tools.ietf.org/html/rfc7230#section-5.4) .</span><span class="sxs-lookup"><span data-stu-id="8f6eb-143">See [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) for more information.</span></span>

1. <span data-ttu-id="8f6eb-144">Potvrďte, že identita modelu procesu má správná oprávnění.</span><span class="sxs-lookup"><span data-stu-id="8f6eb-144">Confirm the process model identity has the proper permissions.</span></span>

   <span data-ttu-id="8f6eb-145">Pokud se výchozí identita fondu aplikací (**modelu procesu**  >  **Identity** ) změní z `ApplicationPoolIdentity` na jinou identitu, ověřte, že Nová identita má požadovaná oprávnění pro přístup ke složce, databázi a dalším požadovaným prostředkům aplikace.</span><span class="sxs-lookup"><span data-stu-id="8f6eb-145">If the default identity of the app pool (**Process Model** > **Identity**) is changed from `ApplicationPoolIdentity` to another identity, verify that the new identity has the required permissions to access the app's folder, database, and other required resources.</span></span> <span data-ttu-id="8f6eb-146">Například fond aplikací vyžaduje přístup pro čtení a zápis ke složkám, kde aplikace čte a zapisuje soubory.</span><span class="sxs-lookup"><span data-stu-id="8f6eb-146">For example, the app pool requires read and write access to folders where the app reads and writes files.</span></span>

## <a name="create-an-aspnet-core-no-locrazor-pages-app"></a><span data-ttu-id="8f6eb-147">Vytvoření aplikace ASP.NET Core Razor Pages</span><span class="sxs-lookup"><span data-stu-id="8f6eb-147">Create an ASP.NET Core Razor Pages app</span></span>

<span data-ttu-id="8f6eb-148">Pokud <xref:getting-started> chcete vytvořit aplikaci Pages, postupujte podle kurzu Razor .</span><span class="sxs-lookup"><span data-stu-id="8f6eb-148">Follow the <xref:getting-started> tutorial to create a Razor Pages app.</span></span>

## <a name="publish-and-deploy-the-app"></a><span data-ttu-id="8f6eb-149">Publikování a nasazení aplikace</span><span class="sxs-lookup"><span data-stu-id="8f6eb-149">Publish and deploy the app</span></span>

<span data-ttu-id="8f6eb-150">*Publikování aplikace* znamená, že se vytvoří kompilovaná aplikace, která může být hostována serverem.</span><span class="sxs-lookup"><span data-stu-id="8f6eb-150">*Publish an app* means to produce a compiled app that can be hosted by a server.</span></span> <span data-ttu-id="8f6eb-151">*Nasazení aplikace* znamená přesunutí publikované aplikace do hostitelského systému.</span><span class="sxs-lookup"><span data-stu-id="8f6eb-151">*Deploy an app* means to move the published app to a hosting system.</span></span> <span data-ttu-id="8f6eb-152">Krok publikování je zpracováván [.NET Core SDK](/dotnet/core/sdk), zatímco krok nasazení může být zpracován řadou přístupů.</span><span class="sxs-lookup"><span data-stu-id="8f6eb-152">The publish step is handled by the [.NET Core SDK](/dotnet/core/sdk), while the deployment step can be handled by a variety of approaches.</span></span> <span data-ttu-id="8f6eb-153">Tento kurz přijme přístup k nasazení *složky* , kde:</span><span class="sxs-lookup"><span data-stu-id="8f6eb-153">This tutorial adopts the *folder* deployment approach, where:</span></span>
 
* <span data-ttu-id="8f6eb-154">Aplikace se publikuje do složky.</span><span class="sxs-lookup"><span data-stu-id="8f6eb-154">The app is published to a folder.</span></span>
* <span data-ttu-id="8f6eb-155">Obsah složky se přesune do složky webu IIS ( **fyzická cesta** k lokalitě ve Správci služby IIS).</span><span class="sxs-lookup"><span data-stu-id="8f6eb-155">The folder's contents are moved to the IIS site's folder (the **Physical path** to the site in IIS Manager).</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8f6eb-156">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8f6eb-156">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="8f6eb-157">V **Průzkumník řešení** klikněte pravým tlačítkem na projekt a vyberte **publikovat**.</span><span class="sxs-lookup"><span data-stu-id="8f6eb-157">Right-click on the project in **Solution Explorer** and select **Publish**.</span></span>
1. <span data-ttu-id="8f6eb-158">V dialogovém okně **vybrat cíl publikování** vyberte možnost publikování **složky** .</span><span class="sxs-lookup"><span data-stu-id="8f6eb-158">In the **Pick a publish target** dialog, select the **Folder** publish option.</span></span>
1. <span data-ttu-id="8f6eb-159">Nastavte **složku nebo cestu sdílení souborů** .</span><span class="sxs-lookup"><span data-stu-id="8f6eb-159">Set the **Folder or File Share** path.</span></span>
   * <span data-ttu-id="8f6eb-160">Pokud jste vytvořili složku pro web IIS, která je k dispozici ve vývojovém počítači jako sdílená síťová složka, zadejte cestu ke sdílené složce.</span><span class="sxs-lookup"><span data-stu-id="8f6eb-160">If you created a folder for the IIS site that's available on the development machine as a network share, provide the path to the share.</span></span> <span data-ttu-id="8f6eb-161">Aktuální uživatel musí mít oprávnění k zápisu pro publikování do sdílené složky.</span><span class="sxs-lookup"><span data-stu-id="8f6eb-161">The current user must have write access to publish to the share.</span></span>
   * <span data-ttu-id="8f6eb-162">Pokud se nemůžete přímo nasadit do složky webu IIS na serveru služby IIS, můžete publikovat do složky na vyměnitelném médiu a fyzicky přesunout publikovanou aplikaci do složky webu IIS na serveru, což je **fyzická cesta** lokality ve Správci služby IIS.</span><span class="sxs-lookup"><span data-stu-id="8f6eb-162">If you're unable to deploy directly to the IIS site folder on the IIS server, publish to a folder on removable media and physically move the published app to the IIS site folder on the server, which is the site's **Physical path** in IIS Manager.</span></span> <span data-ttu-id="8f6eb-163">Přesuňte obsah `bin/Release/{TARGET FRAMEWORK}/publish` složky do složky webu IIS na serveru, což je **fyzická cesta** lokality ve Správci služby IIS.</span><span class="sxs-lookup"><span data-stu-id="8f6eb-163">Move the contents of the `bin/Release/{TARGET FRAMEWORK}/publish` folder to the IIS site folder on the server, which is the site's **Physical path** in IIS Manager.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="8f6eb-164">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="8f6eb-164">.NET Core CLI</span></span>](#tab/netcore-cli)

1. <span data-ttu-id="8f6eb-165">V příkazovém prostředí publikujte aplikaci v konfiguraci vydaných verzí pomocí příkazu [dotnet Publish](/dotnet/core/tools/dotnet-publish) :</span><span class="sxs-lookup"><span data-stu-id="8f6eb-165">In a command shell, publish the app in Release configuration with the [dotnet publish](/dotnet/core/tools/dotnet-publish) command:</span></span>

   ```dotnetcli
   dotnet publish --configuration Release
   ```

1. <span data-ttu-id="8f6eb-166">Přesuňte obsah `bin/Release/{TARGET FRAMEWORK}/publish` složky do složky webu IIS na serveru, což je **fyzická cesta** lokality ve Správci služby IIS.</span><span class="sxs-lookup"><span data-stu-id="8f6eb-166">Move the contents of the `bin/Release/{TARGET FRAMEWORK}/publish` folder to the IIS site folder on the server, which is the site's **Physical path** in IIS Manager.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="8f6eb-167">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="8f6eb-167">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="8f6eb-168">Klikněte pravým tlačítkem na projekt v **řešení** a vyberte **publikovat**  >  **publikovat do složky**.</span><span class="sxs-lookup"><span data-stu-id="8f6eb-168">Right-click on the project in **Solution** and select **Publish** > **Publish to Folder**.</span></span>
1. <span data-ttu-id="8f6eb-169">Nastavte cestu ke **složce** .</span><span class="sxs-lookup"><span data-stu-id="8f6eb-169">Set the **Choose a folder** path.</span></span>
   * <span data-ttu-id="8f6eb-170">Pokud jste vytvořili složku pro web IIS, která je k dispozici ve vývojovém počítači jako sdílená síťová složka, zadejte cestu ke sdílené složce.</span><span class="sxs-lookup"><span data-stu-id="8f6eb-170">If you created a folder for the IIS site that's available on the development machine as a network share, provide the path to the share.</span></span> <span data-ttu-id="8f6eb-171">Aktuální uživatel musí mít oprávnění k zápisu pro publikování do sdílené složky.</span><span class="sxs-lookup"><span data-stu-id="8f6eb-171">The current user must have write access to publish to the share.</span></span>
   * <span data-ttu-id="8f6eb-172">Pokud se nemůžete přímo nasadit do složky webu IIS na serveru služby IIS, proveďte publikování do složky na neodstranitelné médium a fyzicky přesuňte publikovanou aplikaci do složky webu IIS na serveru, což je **fyzická cesta** k webu ve Správci služby IIS.</span><span class="sxs-lookup"><span data-stu-id="8f6eb-172">If you're unable to deploy directly to the IIS site folder on the IIS server, publish to a folder on removeable media and physically move the published app to the IIS site folder on the server, which is the site's **Physical path** in IIS Manager.</span></span> <span data-ttu-id="8f6eb-173">Přesuňte obsah `bin/Release/{TARGET FRAMEWORK}/publish` složky do složky webu IIS na serveru, což je **fyzická cesta** lokality ve Správci služby IIS.</span><span class="sxs-lookup"><span data-stu-id="8f6eb-173">Move the contents of the `bin/Release/{TARGET FRAMEWORK}/publish` folder to the IIS site folder on the server, which is the site's **Physical path** in IIS Manager.</span></span>

---

## <a name="browse-the-website"></a><span data-ttu-id="8f6eb-174">Procházet web</span><span class="sxs-lookup"><span data-stu-id="8f6eb-174">Browse the website</span></span>

<span data-ttu-id="8f6eb-175">Aplikace je v prohlížeči přístupná, až obdrží první požadavek.</span><span class="sxs-lookup"><span data-stu-id="8f6eb-175">The app is accessible in a browser after it receives the first request.</span></span> <span data-ttu-id="8f6eb-176">Vyžádejte aplikaci na vazbu koncového bodu, kterou jste navázali ve Správci služby IIS pro danou lokalitu.</span><span class="sxs-lookup"><span data-stu-id="8f6eb-176">Make a request to the app at the endpoint binding that you established in IIS Manager for the site.</span></span>

## <a name="next-steps"></a><span data-ttu-id="8f6eb-177">Další kroky</span><span class="sxs-lookup"><span data-stu-id="8f6eb-177">Next steps</span></span>

<span data-ttu-id="8f6eb-178">V tomto kurzu jste se naučili:</span><span class="sxs-lookup"><span data-stu-id="8f6eb-178">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="8f6eb-179">Nainstalujte hostující sadu .NET Core na Windows Server.</span><span class="sxs-lookup"><span data-stu-id="8f6eb-179">Install the .NET Core Hosting Bundle on Windows Server.</span></span>
> * <span data-ttu-id="8f6eb-180">Vytvořte web IIS ve Správci služby IIS.</span><span class="sxs-lookup"><span data-stu-id="8f6eb-180">Create an IIS site in IIS Manager.</span></span>
> * <span data-ttu-id="8f6eb-181">Nasaďte aplikaci ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="8f6eb-181">Deploy an ASP.NET Core app.</span></span>

<span data-ttu-id="8f6eb-182">Další informace o hostování ASP.NET Corech aplikací ve službě IIS najdete v článku Přehled služby IIS:</span><span class="sxs-lookup"><span data-stu-id="8f6eb-182">To learn more about hosting ASP.NET Core apps on IIS, see the IIS Overview article:</span></span>

> [!div class="nextstepaction"]
> <xref:host-and-deploy/iis/index>

## <a name="additional-resources"></a><span data-ttu-id="8f6eb-183">Další zdroje informací</span><span class="sxs-lookup"><span data-stu-id="8f6eb-183">Additional resources</span></span>

### <a name="articles-in-the-aspnet-core-documentation-set"></a><span data-ttu-id="8f6eb-184">Články v sadě dokumentace k ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8f6eb-184">Articles in the ASP.NET Core documentation set</span></span>

* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:test/troubleshoot-azure-iis>
* <xref:security/enforcing-ssl>

### <a name="articles-pertaining-to-aspnet-core-app-deployment"></a><span data-ttu-id="8f6eb-185">Články týkající se ASP.NET Coreho nasazení aplikace</span><span class="sxs-lookup"><span data-stu-id="8f6eb-185">Articles pertaining to ASP.NET Core app deployment</span></span>

* <xref:tutorials/publish-to-azure-webapp-using-vs>
* <xref:tutorials/publish-to-azure-webapp-using-vscode>
* <xref:host-and-deploy/visual-studio-publish-profiles>
* [<span data-ttu-id="8f6eb-186">Publikování webové aplikace do složky pomocí Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="8f6eb-186">Publish a Web app to a folder using Visual Studio for Mac</span></span>](/visualstudio/mac/publish-folder)

### <a name="articles-on-iis-https-configuration"></a><span data-ttu-id="8f6eb-187">Články týkající se konfigurace protokolu HTTPS služby IIS</span><span class="sxs-lookup"><span data-stu-id="8f6eb-187">Articles on IIS HTTPS configuration</span></span>

* [<span data-ttu-id="8f6eb-188">Konfigurace SSL ve Správci služby IIS</span><span class="sxs-lookup"><span data-stu-id="8f6eb-188">Configuring SSL in IIS Manager</span></span>](/iis/manage/configuring-security/configuring-ssl-in-iis-manager)
* [<span data-ttu-id="8f6eb-189">Jak nastavit SSL v IIS</span><span class="sxs-lookup"><span data-stu-id="8f6eb-189">How to Set Up SSL on IIS</span></span>](/iis/manage/configuring-security/how-to-set-up-ssl-on-iis)

### <a name="articles-on-iis-and-windows-server"></a><span data-ttu-id="8f6eb-190">Články na IIS a Windows serveru</span><span class="sxs-lookup"><span data-stu-id="8f6eb-190">Articles on IIS and Windows Server</span></span>

* [<span data-ttu-id="8f6eb-191">Oficiální web Microsoft IIS</span><span class="sxs-lookup"><span data-stu-id="8f6eb-191">The Official Microsoft IIS Site</span></span>](https://www.iis.net/)
* [<span data-ttu-id="8f6eb-192">Knihovna technických obsahu pro Windows Server</span><span class="sxs-lookup"><span data-stu-id="8f6eb-192">Windows Server technical content library</span></span>](/windows-server/windows-server)

### <a name="deployment-resources-for-iis-administrators"></a><span data-ttu-id="8f6eb-193">Prostředky nasazení pro správce služby IIS</span><span class="sxs-lookup"><span data-stu-id="8f6eb-193">Deployment resources for IIS administrators</span></span>

* [<span data-ttu-id="8f6eb-194">Dokumentace ke službě IIS</span><span class="sxs-lookup"><span data-stu-id="8f6eb-194">IIS documentation</span></span>](/iis)
* [<span data-ttu-id="8f6eb-195">Začínáme ve službě IIS pomocí Správce služby IIS</span><span class="sxs-lookup"><span data-stu-id="8f6eb-195">Getting Started with the IIS Manager in IIS</span></span>](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* [<span data-ttu-id="8f6eb-196">Nasazení aplikace .NET Core</span><span class="sxs-lookup"><span data-stu-id="8f6eb-196">.NET Core application deployment</span></span>](/dotnet/core/deploying/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:host-and-deploy/iis/modules>
* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

