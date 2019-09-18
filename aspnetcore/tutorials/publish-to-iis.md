---
title: Publikování aplikace ASP.NET Core ve službě IIS
author: guardrex
description: Naučte se hostovat aplikaci ASP.NET Core na serveru IIS.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/08/2019
uid: tutorials/publish-to-iis
ms.openlocfilehash: 4ac7b3a2f738e443263dd888f556e0aff7c8099b
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2019
ms.locfileid: "71082381"
---
# <a name="publish-an-aspnet-core-app-to-iis"></a><span data-ttu-id="e58d0-103">Publikování aplikace ASP.NET Core ve službě IIS</span><span class="sxs-lookup"><span data-stu-id="e58d0-103">Publish an ASP.NET Core app to IIS</span></span>

<span data-ttu-id="e58d0-104">Podle [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="e58d0-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="e58d0-105">V tomto kurzu se dozvíte, jak hostovat aplikaci ASP.NET Core na serveru IIS.</span><span class="sxs-lookup"><span data-stu-id="e58d0-105">This tutorial shows how to host an ASP.NET Core app on an IIS server.</span></span>

<span data-ttu-id="e58d0-106">Tento kurz se zabývá následujícími tématy:</span><span class="sxs-lookup"><span data-stu-id="e58d0-106">This tutorial covers the following subjects:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="e58d0-107">Nainstalujte hostující sadu .NET Core na Windows Server.</span><span class="sxs-lookup"><span data-stu-id="e58d0-107">Install the .NET Core Hosting Bundle on Windows Server.</span></span>
> * <span data-ttu-id="e58d0-108">Vytvořte web IIS ve Správci služby IIS.</span><span class="sxs-lookup"><span data-stu-id="e58d0-108">Create an IIS site in IIS Manager.</span></span>
> * <span data-ttu-id="e58d0-109">Nasaďte aplikaci ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e58d0-109">Deploy an ASP.NET Core app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="e58d0-110">Požadavky</span><span class="sxs-lookup"><span data-stu-id="e58d0-110">Prerequisites</span></span>

* <span data-ttu-id="e58d0-111">[.NET Core SDK](/dotnet/core/sdk) nainstalované na vývojovém počítači.</span><span class="sxs-lookup"><span data-stu-id="e58d0-111">[.NET Core SDK](/dotnet/core/sdk) installed on the development machine.</span></span>
* <span data-ttu-id="e58d0-112">Windows Server nakonfigurovaný pomocí role serveru **webový server (IIS)** .</span><span class="sxs-lookup"><span data-stu-id="e58d0-112">Windows Server configured with the **Web Server (IIS)** server role.</span></span> <span data-ttu-id="e58d0-113">Pokud váš server není nakonfigurovaný na hostování webů se službou IIS, postupujte podle pokynů v části <xref:host-and-deploy/iis/index#iis-configuration> *Konfigurace služby IIS* v článku a pak se vraťte k tomuto kurzu.</span><span class="sxs-lookup"><span data-stu-id="e58d0-113">If your server isn't configured to host websites with IIS, follow the guidance in the *IIS configuration* section of the <xref:host-and-deploy/iis/index#iis-configuration> article and then return to this tutorial.</span></span>

> [!WARNING]
> <span data-ttu-id="e58d0-114">**Konfigurace služby IIS a zabezpečení webu zahrnují koncepty, na které se tento kurz nevztahuje.**</span><span class="sxs-lookup"><span data-stu-id="e58d0-114">**IIS configuration and website security involve concepts that aren't covered by this tutorial.**</span></span> <span data-ttu-id="e58d0-115">Další informace najdete v dokumentaci ke službě IIS v dokumentaci ke službě [Microsoft IIS](https://www.iis.net/) a v [ASP.NET Core článku o hostování se službou IIS](xref:host-and-deploy/iis/index) před hostováním produkčních aplikací ve službě IIS.</span><span class="sxs-lookup"><span data-stu-id="e58d0-115">Consult the IIS guidance in the [Microsoft IIS documentation](https://www.iis.net/) and the [ASP.NET Core article on hosting with IIS](xref:host-and-deploy/iis/index) before hosting production apps on IIS.</span></span>
>
> <span data-ttu-id="e58d0-116">Důležité scénáře pro hostování služby IIS, na které se nevztahuje tento kurz, zahrnují:</span><span class="sxs-lookup"><span data-stu-id="e58d0-116">Important scenarios for IIS hosting not covered by this tutorial include:</span></span>
>
> * [<span data-ttu-id="e58d0-117">Vytvoření podregistru pro ochranu ASP.NET Core dat</span><span class="sxs-lookup"><span data-stu-id="e58d0-117">Creation of a registry hive for ASP.NET Core Data Protection</span></span>](xref:host-and-deploy/iis/index#data-protection)
> * [<span data-ttu-id="e58d0-118">Konfigurace seznamu Access Control fondu aplikací (ACL)</span><span class="sxs-lookup"><span data-stu-id="e58d0-118">Configuration of the app pool's Access Control List (ACL)</span></span>](xref:host-and-deploy/iis/index#application-pool-identity)
> * <span data-ttu-id="e58d0-119">V tomto kurzu nasadíte v rámci konceptů nasazení služby IIS aplikaci bez zabezpečení HTTPS nakonfigurovanou ve službě IIS.</span><span class="sxs-lookup"><span data-stu-id="e58d0-119">To focus on IIS deployment concepts, this tutorial deploys an app without HTTPS security configured in IIS.</span></span> <span data-ttu-id="e58d0-120">Další informace o hostování aplikace, která je povolená pro protokol HTTPS, najdete v tématech zabezpečení v části [Další zdroje](#additional-resources) tohoto článku.</span><span class="sxs-lookup"><span data-stu-id="e58d0-120">For more information on hosting an app enabled for HTTPS protocol, see the security topics in the [Additional resources](#additional-resources) section of this article.</span></span> <span data-ttu-id="e58d0-121">Další pokyny pro hostování ASP.NET Core aplikací jsou uvedené v <xref:host-and-deploy/iis/index> článku.</span><span class="sxs-lookup"><span data-stu-id="e58d0-121">Further guidance for hosting ASP.NET Core apps is provided in the <xref:host-and-deploy/iis/index> article.</span></span>

## <a name="install-the-net-core-hosting-bundle"></a><span data-ttu-id="e58d0-122">Instalace .NET Core, který je hostitelem svazku</span><span class="sxs-lookup"><span data-stu-id="e58d0-122">Install the .NET Core Hosting Bundle</span></span>

<span data-ttu-id="e58d0-123">Na server IIS nainstalujte *hostující sadu .NET Core* .</span><span class="sxs-lookup"><span data-stu-id="e58d0-123">Install the *.NET Core Hosting Bundle* on the IIS server.</span></span> <span data-ttu-id="e58d0-124">Nainstaluje sady .NET Core Runtime, knihovny .NET Core a [modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="e58d0-124">The bundle installs the .NET Core Runtime, .NET Core Library, and the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="e58d0-125">Povolí modul ASP.NET Core aplikací ke spuštění za služby IIS.</span><span class="sxs-lookup"><span data-stu-id="e58d0-125">The module allows ASP.NET Core apps to run behind IIS.</span></span> <span data-ttu-id="e58d0-126">Pokud systém nemá připojení k Internetu, získejte a nainstalujte [Microsoft Visual C++ 2015 Redistributable](https://www.microsoft.com/download/details.aspx?id=53840) před instalací sady hostování rozhraní .NET Core.</span><span class="sxs-lookup"><span data-stu-id="e58d0-126">If the system doesn't have an Internet connection, obtain and install the [Microsoft Visual C++ 2015 Redistributable](https://www.microsoft.com/download/details.aspx?id=53840) before installing the .NET Core Hosting Bundle.</span></span>

<span data-ttu-id="e58d0-127">Stažení instalačního programu pomocí následujícího odkazu:</span><span class="sxs-lookup"><span data-stu-id="e58d0-127">Download the installer using the following link:</span></span>

[<span data-ttu-id="e58d0-128">Aktuální instalační program sady hostování rozhraní .NET Core (s přímým přístupem ke stažení)</span><span class="sxs-lookup"><span data-stu-id="e58d0-128">Current .NET Core Hosting Bundle installer (direct download)</span></span>](https://www.microsoft.com/net/permalink/dotnetcore-current-windows-runtime-bundle-installer)

1. <span data-ttu-id="e58d0-129">Spusťte instalační program na serveru IIS.</span><span class="sxs-lookup"><span data-stu-id="e58d0-129">Run the installer on the IIS server.</span></span>

1. <span data-ttu-id="e58d0-130">Restartujte server nebo spusťte příkaz **net stop** s příponou " **net start w3svc** " v příkazovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="e58d0-130">Restart the server or execute **net stop was /y** followed by **net start w3svc** in a command shell.</span></span>

## <a name="create-the-iis-site"></a><span data-ttu-id="e58d0-131">Vytvořte web služby IIS</span><span class="sxs-lookup"><span data-stu-id="e58d0-131">Create the IIS site</span></span>

1. <span data-ttu-id="e58d0-132">Na serveru služby IIS vytvořte složku, která bude obsahovat publikované složky a soubory aplikace.</span><span class="sxs-lookup"><span data-stu-id="e58d0-132">On the IIS server, create a folder to contain the app's published folders and files.</span></span> <span data-ttu-id="e58d0-133">V následujícím kroku je jako fyzická cesta k aplikaci služba IIS poskytována jako cesta k této složce.</span><span class="sxs-lookup"><span data-stu-id="e58d0-133">In a following step, the folder's path is provided to IIS as the physical path to the app.</span></span>

1. <span data-ttu-id="e58d0-134">Ve Správci služby IIS otevřete uzel serveru na panelu **připojení** .</span><span class="sxs-lookup"><span data-stu-id="e58d0-134">In IIS Manager, open the server's node in the **Connections** panel.</span></span> <span data-ttu-id="e58d0-135">Klikněte pravým tlačítkem myši **lokality** složky.</span><span class="sxs-lookup"><span data-stu-id="e58d0-135">Right-click the **Sites** folder.</span></span> <span data-ttu-id="e58d0-136">Vyberte **přidat web** z kontextové nabídky.</span><span class="sxs-lookup"><span data-stu-id="e58d0-136">Select **Add Website** from the contextual menu.</span></span>

1. <span data-ttu-id="e58d0-137">Zadejte **název lokality** a nastavte **fyzickou cestu** ke složce pro nasazení aplikace, kterou jste vytvořili.</span><span class="sxs-lookup"><span data-stu-id="e58d0-137">Provide a **Site name** and set the **Physical path** to the app's deployment folder that you created.</span></span> <span data-ttu-id="e58d0-138">Zadejte konfiguraci **vazby** a vytvořte web výběrem **OK**.</span><span class="sxs-lookup"><span data-stu-id="e58d0-138">Provide the **Binding** configuration and create the website by selecting **OK**.</span></span>

## <a name="create-an-aspnet-core-razor-pages-app"></a><span data-ttu-id="e58d0-139">Vytvoření aplikace ASP.NET Core Razor Pages</span><span class="sxs-lookup"><span data-stu-id="e58d0-139">Create an ASP.NET Core Razor Pages app</span></span>

<span data-ttu-id="e58d0-140">Pokud chcete vytvořit aplikaci Razor Pages, postupujte podle kurzu.<xref:getting-started></span><span class="sxs-lookup"><span data-stu-id="e58d0-140">Follow the <xref:getting-started> tutorial to create a Razor Pages app.</span></span>

## <a name="publish-and-deploy-the-app"></a><span data-ttu-id="e58d0-141">Publikování a nasazení aplikace</span><span class="sxs-lookup"><span data-stu-id="e58d0-141">Publish and deploy the app</span></span>

<span data-ttu-id="e58d0-142">*Publikování aplikace* znamená, že se vytvoří kompilovaná aplikace, která může být hostována serverem.</span><span class="sxs-lookup"><span data-stu-id="e58d0-142">*Publish an app* means to produce a compiled app that can be hosted by a server.</span></span> <span data-ttu-id="e58d0-143">*Nasazení aplikace* znamená přesunutí publikované aplikace do hostitelského systému.</span><span class="sxs-lookup"><span data-stu-id="e58d0-143">*Deploy an app* means to move the published app to a hosting system.</span></span> <span data-ttu-id="e58d0-144">Krok publikování je zpracováván [.NET Core SDK](/dotnet/core/sdk), zatímco krok nasazení může být zpracován řadou přístupů.</span><span class="sxs-lookup"><span data-stu-id="e58d0-144">The publish step is handled by the [.NET Core SDK](/dotnet/core/sdk), while the deployment step can be handled by a variety of approaches.</span></span> <span data-ttu-id="e58d0-145">Tento kurz přijme přístup k nasazení *složky* , kde:</span><span class="sxs-lookup"><span data-stu-id="e58d0-145">This tutorial adopts the *folder* deployment approach, where:</span></span>

* <span data-ttu-id="e58d0-146">Aplikace se publikuje do složky.</span><span class="sxs-lookup"><span data-stu-id="e58d0-146">The app is published to a folder.</span></span>
* <span data-ttu-id="e58d0-147">Obsah složky se přesune do složky webu IIS ( **fyzická cesta** k lokalitě ve Správci služby IIS).</span><span class="sxs-lookup"><span data-stu-id="e58d0-147">The folder's contents are moved to the IIS site's folder (the **Physical path** to the site in IIS Manager).</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="e58d0-148">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e58d0-148">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="e58d0-149">V **Průzkumník řešení** klikněte pravým tlačítkem na projekt a vyberte **publikovat**.</span><span class="sxs-lookup"><span data-stu-id="e58d0-149">Right-click on the project in **Solution Explorer** and select **Publish**.</span></span>
1. <span data-ttu-id="e58d0-150">V dialogovém okně **vybrat cíl publikování** vyberte možnost publikování **složky** .</span><span class="sxs-lookup"><span data-stu-id="e58d0-150">In the **Pick a publish target** dialog, select the **Folder** publish option.</span></span>
1. <span data-ttu-id="e58d0-151">Nastavte **složku nebo cestu sdílení souborů** .</span><span class="sxs-lookup"><span data-stu-id="e58d0-151">Set the **Folder or File Share** path.</span></span>
   * <span data-ttu-id="e58d0-152">Pokud jste vytvořili složku pro web IIS, která je k dispozici ve vývojovém počítači jako sdílená síťová složka, zadejte cestu ke sdílené složce.</span><span class="sxs-lookup"><span data-stu-id="e58d0-152">If you created a folder for the IIS site that's available on the development machine as a network share, provide the path to the share.</span></span> <span data-ttu-id="e58d0-153">Aktuální uživatel musí mít oprávnění k zápisu pro publikování do sdílené složky.</span><span class="sxs-lookup"><span data-stu-id="e58d0-153">The current user must have write access to publish to the share.</span></span>
   * <span data-ttu-id="e58d0-154">Pokud se nemůžete přímo nasadit do složky webu IIS na serveru služby IIS, proveďte publikování do složky na neodstranitelné médium a fyzicky přesuňte publikovanou aplikaci do složky webu IIS na serveru, což je **fyzická cesta** k webu ve Správci služby IIS.</span><span class="sxs-lookup"><span data-stu-id="e58d0-154">If you're unable to deploy directly to the IIS site folder on the IIS server, publish to a folder on removeable media and physically move the published app to the IIS site folder on the server, which is the site's **Physical path** in IIS Manager.</span></span> <span data-ttu-id="e58d0-155">Přesuňte obsah složky *bin/Release/{Target Framework}/Publish* do složky webu IIS na serveru, který je **fyzickou cestou** lokality ve Správci služby IIS.</span><span class="sxs-lookup"><span data-stu-id="e58d0-155">Move the contents of the *bin/Release/{TARGET FRAMEWORK}/publish* folder to the IIS site folder on the server, which is the site's **Physical path** in IIS Manager.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="e58d0-156">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="e58d0-156">.NET Core CLI</span></span>](#tab/netcore-cli)

1. <span data-ttu-id="e58d0-157">V příkazovém prostředí publikujte aplikaci v konfiguraci vydaných verzí pomocí příkazu [dotnet Publish](/dotnet/core/tools/dotnet-publish) :</span><span class="sxs-lookup"><span data-stu-id="e58d0-157">In a command shell, publish the app in Release configuration with the [dotnet publish](/dotnet/core/tools/dotnet-publish) command:</span></span>

   ```dotnetcli
   dotnet publish --configuration Release
   ```

1. <span data-ttu-id="e58d0-158">Přesuňte obsah složky *bin/Release/{Target Framework}/Publish* do složky webu IIS na serveru, který je **fyzickou cestou** lokality ve Správci služby IIS.</span><span class="sxs-lookup"><span data-stu-id="e58d0-158">Move the contents of the *bin/Release/{TARGET FRAMEWORK}/publish* folder to the IIS site folder on the server, which is the site's **Physical path** in IIS Manager.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="e58d0-159">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="e58d0-159">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="e58d0-160">Klikněte pravým tlačítkem na projekt v **řešení** a vyberte **publikovat** > **publikovat do složky**.</span><span class="sxs-lookup"><span data-stu-id="e58d0-160">Right-click on the project in **Solution** and select **Publish** > **Publish to Folder**.</span></span>
1. <span data-ttu-id="e58d0-161">Nastavte cestu ke **složce** .</span><span class="sxs-lookup"><span data-stu-id="e58d0-161">Set the **Choose a folder** path.</span></span>
   * <span data-ttu-id="e58d0-162">Pokud jste vytvořili složku pro web IIS, která je k dispozici ve vývojovém počítači jako sdílená síťová složka, zadejte cestu ke sdílené složce.</span><span class="sxs-lookup"><span data-stu-id="e58d0-162">If you created a folder for the IIS site that's available on the development machine as a network share, provide the path to the share.</span></span> <span data-ttu-id="e58d0-163">Aktuální uživatel musí mít oprávnění k zápisu pro publikování do sdílené složky.</span><span class="sxs-lookup"><span data-stu-id="e58d0-163">The current user must have write access to publish to the share.</span></span>
   * <span data-ttu-id="e58d0-164">Pokud se nemůžete přímo nasadit do složky webu IIS na serveru služby IIS, proveďte publikování do složky na neodstranitelné médium a fyzicky přesuňte publikovanou aplikaci do složky webu IIS na serveru, což je **fyzická cesta** k webu ve Správci služby IIS.</span><span class="sxs-lookup"><span data-stu-id="e58d0-164">If you're unable to deploy directly to the IIS site folder on the IIS server, publish to a folder on removeable media and physically move the published app to the IIS site folder on the server, which is the site's **Physical path** in IIS Manager.</span></span> <span data-ttu-id="e58d0-165">Přesuňte obsah složky *bin/Release/{Target Framework}/Publish* do složky webu IIS na serveru, který je **fyzickou cestou** lokality ve Správci služby IIS.</span><span class="sxs-lookup"><span data-stu-id="e58d0-165">Move the contents of the *bin/Release/{TARGET FRAMEWORK}/publish* folder to the IIS site folder on the server, which is the site's **Physical path** in IIS Manager.</span></span>

---

## <a name="browse-the-website"></a><span data-ttu-id="e58d0-166">Přejděte na web</span><span class="sxs-lookup"><span data-stu-id="e58d0-166">Browse the website</span></span>

<span data-ttu-id="e58d0-167">Aplikace je v prohlížeči přístupná, až obdrží první požadavek.</span><span class="sxs-lookup"><span data-stu-id="e58d0-167">The app is accessible in a browser after it receives the first request.</span></span> <span data-ttu-id="e58d0-168">Vyžádejte aplikaci na vazbu koncového bodu, kterou jste navázali ve Správci služby IIS pro danou lokalitu.</span><span class="sxs-lookup"><span data-stu-id="e58d0-168">Make a request to the app at the endpoint binding that you established in IIS Manager for the site.</span></span>

## <a name="next-steps"></a><span data-ttu-id="e58d0-169">Další kroky</span><span class="sxs-lookup"><span data-stu-id="e58d0-169">Next steps</span></span>

<span data-ttu-id="e58d0-170">V tomto kurzu jste se naučili:</span><span class="sxs-lookup"><span data-stu-id="e58d0-170">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="e58d0-171">Nainstalujte hostující sadu .NET Core na Windows Server.</span><span class="sxs-lookup"><span data-stu-id="e58d0-171">Install the .NET Core Hosting Bundle on Windows Server.</span></span>
> * <span data-ttu-id="e58d0-172">Vytvořte web IIS ve Správci služby IIS.</span><span class="sxs-lookup"><span data-stu-id="e58d0-172">Create an IIS site in IIS Manager.</span></span>
> * <span data-ttu-id="e58d0-173">Nasaďte aplikaci ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e58d0-173">Deploy an ASP.NET Core app.</span></span>

<span data-ttu-id="e58d0-174">Další informace o hostování ASP.NET Corech aplikací ve službě IIS najdete v článku Přehled služby IIS:</span><span class="sxs-lookup"><span data-stu-id="e58d0-174">To learn more about hosting ASP.NET Core apps on IIS, see the IIS Overview article:</span></span>

> [!div class="nextstepaction"]
> <xref:host-and-deploy/iis/index>

## <a name="additional-resources"></a><span data-ttu-id="e58d0-175">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="e58d0-175">Additional resources</span></span>

### <a name="articles-in-the-aspnet-core-documentation-set"></a><span data-ttu-id="e58d0-176">Články v sadě dokumentace k ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e58d0-176">Articles in the ASP.NET Core documentation set</span></span>

* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:test/troubleshoot-azure-iis>
* <xref:security/enforcing-ssl>

### <a name="articles-pertaining-to-aspnet-core-app-deployment"></a><span data-ttu-id="e58d0-177">Články týkající se ASP.NET Coreho nasazení aplikace</span><span class="sxs-lookup"><span data-stu-id="e58d0-177">Articles pertaining to ASP.NET Core app deployment</span></span>

* <xref:tutorials/publish-to-azure-webapp-using-vs>
* <xref:tutorials/publish-to-azure-webapp-using-vscode>
* <xref:host-and-deploy/visual-studio-publish-profiles>
* [<span data-ttu-id="e58d0-178">Publikování webové aplikace do složky pomocí Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="e58d0-178">Publish a Web app to a folder using Visual Studio for Mac</span></span>](/visualstudio/mac/publish-folder)

### <a name="articles-on-iis-https-configuration"></a><span data-ttu-id="e58d0-179">Články týkající se konfigurace protokolu HTTPS služby IIS</span><span class="sxs-lookup"><span data-stu-id="e58d0-179">Articles on IIS HTTPS configuration</span></span>

* [<span data-ttu-id="e58d0-180">Konfigurace SSL ve Správci služby IIS</span><span class="sxs-lookup"><span data-stu-id="e58d0-180">Configuring SSL in IIS Manager</span></span>](/iis/manage/configuring-security/configuring-ssl-in-iis-manager)
* [<span data-ttu-id="e58d0-181">Jak nastavit SSL v IIS</span><span class="sxs-lookup"><span data-stu-id="e58d0-181">How to Set Up SSL on IIS</span></span>](/iis/manage/configuring-security/how-to-set-up-ssl-on-iis)

### <a name="articles-on-iis-and-windows-server"></a><span data-ttu-id="e58d0-182">Články na IIS a Windows serveru</span><span class="sxs-lookup"><span data-stu-id="e58d0-182">Articles on IIS and Windows Server</span></span>

* [<span data-ttu-id="e58d0-183">Lokality oficiální Microsoft IIS</span><span class="sxs-lookup"><span data-stu-id="e58d0-183">The Official Microsoft IIS Site</span></span>](https://www.iis.net/)
* [<span data-ttu-id="e58d0-184">Technická knihovna obsahu Windows serveru</span><span class="sxs-lookup"><span data-stu-id="e58d0-184">Windows Server technical content library</span></span>](/windows-server/windows-server)
