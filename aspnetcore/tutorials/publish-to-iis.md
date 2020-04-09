---
title: Publikování aplikace ASP.NET Core do iis
author: rick-anderson
description: Přečtěte si, jak hostovat aplikaci ASP.NET Core na serveru Služby IIS.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/03/2019
uid: tutorials/publish-to-iis
ms.openlocfilehash: 47f78ba78741a8e0175ce801c0c0e51f091273a8
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "79511389"
---
# <a name="publish-an-aspnet-core-app-to-iis"></a><span data-ttu-id="9875c-103">Publikování aplikace ASP.NET Core do iis</span><span class="sxs-lookup"><span data-stu-id="9875c-103">Publish an ASP.NET Core app to IIS</span></span>

<span data-ttu-id="9875c-104">Tento kurz ukazuje, jak hostovat aplikaci ASP.NET Core na serveru služby IIS.</span><span class="sxs-lookup"><span data-stu-id="9875c-104">This tutorial shows how to host an ASP.NET Core app on an IIS server.</span></span>

<span data-ttu-id="9875c-105">Tento kurz se zabývá následujícími tématy:</span><span class="sxs-lookup"><span data-stu-id="9875c-105">This tutorial covers the following subjects:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="9875c-106">Nainstalujte sadu .NET Core Hosting Na Windows Server.</span><span class="sxs-lookup"><span data-stu-id="9875c-106">Install the .NET Core Hosting Bundle on Windows Server.</span></span>
> * <span data-ttu-id="9875c-107">Vytvořte web služby IIS ve Správci služby IIS.</span><span class="sxs-lookup"><span data-stu-id="9875c-107">Create an IIS site in IIS Manager.</span></span>
> * <span data-ttu-id="9875c-108">Nasaďte aplikaci ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="9875c-108">Deploy an ASP.NET Core app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="9875c-109">Požadavky</span><span class="sxs-lookup"><span data-stu-id="9875c-109">Prerequisites</span></span>

* <span data-ttu-id="9875c-110">[Sada .NET Core SDK](/dotnet/core/sdk) nainstalovaná ve vývojovém počítači.</span><span class="sxs-lookup"><span data-stu-id="9875c-110">[.NET Core SDK](/dotnet/core/sdk) installed on the development machine.</span></span>
* <span data-ttu-id="9875c-111">Systém Windows Server je nakonfigurován s rolí serveru **Www Server (IIS).**</span><span class="sxs-lookup"><span data-stu-id="9875c-111">Windows Server configured with the **Web Server (IIS)** server role.</span></span> <span data-ttu-id="9875c-112">Pokud váš server není nakonfigurován tak, aby hostoval weby se službou IIS, postupujte podle pokynů v části *konfigurace služby IIS* v <xref:host-and-deploy/iis/index#iis-configuration> článku a vraťte se k tomuto kurzu.</span><span class="sxs-lookup"><span data-stu-id="9875c-112">If your server isn't configured to host websites with IIS, follow the guidance in the *IIS configuration* section of the <xref:host-and-deploy/iis/index#iis-configuration> article and then return to this tutorial.</span></span>

> [!WARNING]
> <span data-ttu-id="9875c-113">**Konfigurace služby IIS a zabezpečení webu zahrnují koncepty, na které se tento kurz nevztahuje.**</span><span class="sxs-lookup"><span data-stu-id="9875c-113">**IIS configuration and website security involve concepts that aren't covered by this tutorial.**</span></span> <span data-ttu-id="9875c-114">Před hostováním produkčních aplikací ve službě IIS naleznete pokyny služby IIS v [dokumentaci služby Microsoft IIS](https://www.iis.net/) a [v článku ASP.NET Core o hostování se službou IIS.](xref:host-and-deploy/iis/index)</span><span class="sxs-lookup"><span data-stu-id="9875c-114">Consult the IIS guidance in the [Microsoft IIS documentation](https://www.iis.net/) and the [ASP.NET Core article on hosting with IIS](xref:host-and-deploy/iis/index) before hosting production apps on IIS.</span></span>
>
> <span data-ttu-id="9875c-115">Mezi důležité scénáře pro hostování služby IIS, na které se tento kurz nevztahuje, patří:</span><span class="sxs-lookup"><span data-stu-id="9875c-115">Important scenarios for IIS hosting not covered by this tutorial include:</span></span>
>
> * [<span data-ttu-id="9875c-116">Vytvoření podregistru pro ASP.NET základní ochrany dat</span><span class="sxs-lookup"><span data-stu-id="9875c-116">Creation of a registry hive for ASP.NET Core Data Protection</span></span>](xref:host-and-deploy/iis/index#data-protection)
> * [<span data-ttu-id="9875c-117">Konfigurace seznamu řízení přístupu fondu aplikací (ACL)</span><span class="sxs-lookup"><span data-stu-id="9875c-117">Configuration of the app pool's Access Control List (ACL)</span></span>](xref:host-and-deploy/iis/index#application-pool-identity)
> * <span data-ttu-id="9875c-118">Chcete-li se zaměřit na koncepty nasazení služby IIS, tento kurz nasadí aplikaci bez zabezpečení HTTPS nakonfigurovaného ve službě IIS.</span><span class="sxs-lookup"><span data-stu-id="9875c-118">To focus on IIS deployment concepts, this tutorial deploys an app without HTTPS security configured in IIS.</span></span> <span data-ttu-id="9875c-119">Další informace o hostování aplikace povolené pro protokol HTTPS najdete v tématech zabezpečení v části [Další prostředky](#additional-resources) v tomto článku.</span><span class="sxs-lookup"><span data-stu-id="9875c-119">For more information on hosting an app enabled for HTTPS protocol, see the security topics in the [Additional resources](#additional-resources) section of this article.</span></span> <span data-ttu-id="9875c-120">Další pokyny pro hostování ASP.NET aplikace <xref:host-and-deploy/iis/index> Core jsou uvedeny v článku.</span><span class="sxs-lookup"><span data-stu-id="9875c-120">Further guidance for hosting ASP.NET Core apps is provided in the <xref:host-and-deploy/iis/index> article.</span></span>

## <a name="install-the-net-core-hosting-bundle"></a><span data-ttu-id="9875c-121">Instalace balíčku hostingu jádra .NET</span><span class="sxs-lookup"><span data-stu-id="9875c-121">Install the .NET Core Hosting Bundle</span></span>

<span data-ttu-id="9875c-122">Nainstalujte *sadu .NET Core Hosting Na* server služby IIS.</span><span class="sxs-lookup"><span data-stu-id="9875c-122">Install the *.NET Core Hosting Bundle* on the IIS server.</span></span> <span data-ttu-id="9875c-123">Balíček nainstaluje modul .NET Core Runtime, knihovnu .NET Core Library a [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="9875c-123">The bundle installs the .NET Core Runtime, .NET Core Library, and the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="9875c-124">Modul umožňuje ASP.NET aplikace Core běžet za službou IIS.</span><span class="sxs-lookup"><span data-stu-id="9875c-124">The module allows ASP.NET Core apps to run behind IIS.</span></span>

<span data-ttu-id="9875c-125">Stáhněte si instalační program pomocí následujícího odkazu:</span><span class="sxs-lookup"><span data-stu-id="9875c-125">Download the installer using the following link:</span></span>

[<span data-ttu-id="9875c-126">Aktuální instalační program .NET Core Hosting Bundle (přímé stahování)</span><span class="sxs-lookup"><span data-stu-id="9875c-126">Current .NET Core Hosting Bundle installer (direct download)</span></span>](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

1. <span data-ttu-id="9875c-127">Spusťte instalační program na serveru služby IIS.</span><span class="sxs-lookup"><span data-stu-id="9875c-127">Run the installer on the IIS server.</span></span>

1. <span data-ttu-id="9875c-128">Restartujte server nebo spusťte **net stop byl /y** následovaný **net start w3svc** v příkazovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="9875c-128">Restart the server or execute **net stop was /y** followed by **net start w3svc** in a command shell.</span></span>

## <a name="create-the-iis-site"></a><span data-ttu-id="9875c-129">Vytvoření webu iis</span><span class="sxs-lookup"><span data-stu-id="9875c-129">Create the IIS site</span></span>

1. <span data-ttu-id="9875c-130">Na serveru Služby IIS vytvořte složku obsahující publikované složky a soubory aplikace.</span><span class="sxs-lookup"><span data-stu-id="9875c-130">On the IIS server, create a folder to contain the app's published folders and files.</span></span> <span data-ttu-id="9875c-131">V následujícím kroku je cesta ke složce k dispozici službu IIS jako fyzická cesta k aplikaci.</span><span class="sxs-lookup"><span data-stu-id="9875c-131">In a following step, the folder's path is provided to IIS as the physical path to the app.</span></span>

1. <span data-ttu-id="9875c-132">Ve Správci služby IIS otevřete uzel serveru v panelu **Připojení.**</span><span class="sxs-lookup"><span data-stu-id="9875c-132">In IIS Manager, open the server's node in the **Connections** panel.</span></span> <span data-ttu-id="9875c-133">Klikněte pravým tlačítkem myši na složku **Weby.**</span><span class="sxs-lookup"><span data-stu-id="9875c-133">Right-click the **Sites** folder.</span></span> <span data-ttu-id="9875c-134">V kontextové nabídce vyberte **Přidat web.**</span><span class="sxs-lookup"><span data-stu-id="9875c-134">Select **Add Website** from the contextual menu.</span></span>

1. <span data-ttu-id="9875c-135">Zadejte **název webu** a nastavte **fyzickou cestu** ke složce nasazení aplikace, kterou jste vytvořili.</span><span class="sxs-lookup"><span data-stu-id="9875c-135">Provide a **Site name** and set the **Physical path** to the app's deployment folder that you created.</span></span> <span data-ttu-id="9875c-136">Zadejte konfiguraci **vazby** a vytvořte web výběrem **možnosti OK**.</span><span class="sxs-lookup"><span data-stu-id="9875c-136">Provide the **Binding** configuration and create the website by selecting **OK**.</span></span>

## <a name="create-an-aspnet-core-razor-pages-app"></a><span data-ttu-id="9875c-137">Vytvoření aplikace ASP.NET Core Razor Pages</span><span class="sxs-lookup"><span data-stu-id="9875c-137">Create an ASP.NET Core Razor Pages app</span></span>

<span data-ttu-id="9875c-138">Podle <xref:getting-started> kurzu vytvořte aplikaci Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="9875c-138">Follow the <xref:getting-started> tutorial to create a Razor Pages app.</span></span>

## <a name="publish-and-deploy-the-app"></a><span data-ttu-id="9875c-139">Publikování a nasazení aplikace</span><span class="sxs-lookup"><span data-stu-id="9875c-139">Publish and deploy the app</span></span>

<span data-ttu-id="9875c-140">*Publikování aplikace* znamená vytvoření kompilované aplikace, kterou může hostovat server.</span><span class="sxs-lookup"><span data-stu-id="9875c-140">*Publish an app* means to produce a compiled app that can be hosted by a server.</span></span> <span data-ttu-id="9875c-141">*Nasazení aplikace* znamená přesunout publikovanou aplikaci do hostitelského systému.</span><span class="sxs-lookup"><span data-stu-id="9875c-141">*Deploy an app* means to move the published app to a hosting system.</span></span> <span data-ttu-id="9875c-142">Krok publikování je zpracován sadou [.NET Core SDK](/dotnet/core/sdk), zatímco krok nasazení lze zpracovat pomocí různých přístupů.</span><span class="sxs-lookup"><span data-stu-id="9875c-142">The publish step is handled by the [.NET Core SDK](/dotnet/core/sdk), while the deployment step can be handled by a variety of approaches.</span></span> <span data-ttu-id="9875c-143">Tento kurz přijímá přístup nasazení *složky,* kde:</span><span class="sxs-lookup"><span data-stu-id="9875c-143">This tutorial adopts the *folder* deployment approach, where:</span></span>

* <span data-ttu-id="9875c-144">Aplikace se publikuje do složky.</span><span class="sxs-lookup"><span data-stu-id="9875c-144">The app is published to a folder.</span></span>
* <span data-ttu-id="9875c-145">Obsah složky je přesunut do složky webu služby IIS **(fyzická cesta** k webu ve Správci služby IIS).</span><span class="sxs-lookup"><span data-stu-id="9875c-145">The folder's contents are moved to the IIS site's folder (the **Physical path** to the site in IIS Manager).</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9875c-146">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9875c-146">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="9875c-147">Klikněte pravým tlačítkem myši na projekt v **Průzkumníku řešení** a vyberte **publikovat**.</span><span class="sxs-lookup"><span data-stu-id="9875c-147">Right-click on the project in **Solution Explorer** and select **Publish**.</span></span>
1. <span data-ttu-id="9875c-148">V **dialogovém** okně Vybrat cíl publikování vyberte možnost Publikovat **složku.**</span><span class="sxs-lookup"><span data-stu-id="9875c-148">In the **Pick a publish target** dialog, select the **Folder** publish option.</span></span>
1. <span data-ttu-id="9875c-149">Nastavte cestu **ke složce nebo sdílené složce.**</span><span class="sxs-lookup"><span data-stu-id="9875c-149">Set the **Folder or File Share** path.</span></span>
   * <span data-ttu-id="9875c-150">Pokud jste vytvořili složku pro web služby IIS, která je k dispozici ve vývojovém počítači jako sdílená síťová složka, zadejte cestu ke sdílené položce.</span><span class="sxs-lookup"><span data-stu-id="9875c-150">If you created a folder for the IIS site that's available on the development machine as a network share, provide the path to the share.</span></span> <span data-ttu-id="9875c-151">Aktuální uživatel musí mít přístup pro zápis, aby mohl publikovat do sdílené položky.</span><span class="sxs-lookup"><span data-stu-id="9875c-151">The current user must have write access to publish to the share.</span></span>
   * <span data-ttu-id="9875c-152">Pokud se vám nedaří nasadit přímo do složky webu služby IIS na serveru služby IIS, publikujte do složky na odstranitelném médiu a fyzicky přesuňte publikovanou aplikaci do složky webu služby IIS na serveru, což je **fyzická cesta** webu ve Správci služby IIS.</span><span class="sxs-lookup"><span data-stu-id="9875c-152">If you're unable to deploy directly to the IIS site folder on the IIS server, publish to a folder on removeable media and physically move the published app to the IIS site folder on the server, which is the site's **Physical path** in IIS Manager.</span></span> <span data-ttu-id="9875c-153">Přesuňte obsah složky *bin/Release/{TARGET FRAMEWORK}/publish* do složky webu služby IIS na serveru, což je **fyzická cesta** webu ve Správci služby IIS.</span><span class="sxs-lookup"><span data-stu-id="9875c-153">Move the contents of the *bin/Release/{TARGET FRAMEWORK}/publish* folder to the IIS site folder on the server, which is the site's **Physical path** in IIS Manager.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="9875c-154">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="9875c-154">.NET Core CLI</span></span>](#tab/netcore-cli)

1. <span data-ttu-id="9875c-155">V příkazovém prostředí publikujte aplikaci v konfiguraci release pomocí příkazu [dotnet publish:](/dotnet/core/tools/dotnet-publish)</span><span class="sxs-lookup"><span data-stu-id="9875c-155">In a command shell, publish the app in Release configuration with the [dotnet publish](/dotnet/core/tools/dotnet-publish) command:</span></span>

   ```dotnetcli
   dotnet publish --configuration Release
   ```

1. <span data-ttu-id="9875c-156">Přesuňte obsah složky *bin/Release/{TARGET FRAMEWORK}/publish* do složky webu služby IIS na serveru, což je **fyzická cesta** webu ve Správci služby IIS.</span><span class="sxs-lookup"><span data-stu-id="9875c-156">Move the contents of the *bin/Release/{TARGET FRAMEWORK}/publish* folder to the IIS site folder on the server, which is the site's **Physical path** in IIS Manager.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="9875c-157">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="9875c-157">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="9875c-158">Klikněte pravým tlačítkem myši na projekt v **části Řešení** a vyberte **publikovat** > **publikovat do složky**.</span><span class="sxs-lookup"><span data-stu-id="9875c-158">Right-click on the project in **Solution** and select **Publish** > **Publish to Folder**.</span></span>
1. <span data-ttu-id="9875c-159">Nastavte možnost Zvolit cestu **ke složce.**</span><span class="sxs-lookup"><span data-stu-id="9875c-159">Set the **Choose a folder** path.</span></span>
   * <span data-ttu-id="9875c-160">Pokud jste vytvořili složku pro web služby IIS, která je k dispozici ve vývojovém počítači jako sdílená síťová složka, zadejte cestu ke sdílené položce.</span><span class="sxs-lookup"><span data-stu-id="9875c-160">If you created a folder for the IIS site that's available on the development machine as a network share, provide the path to the share.</span></span> <span data-ttu-id="9875c-161">Aktuální uživatel musí mít přístup pro zápis, aby mohl publikovat do sdílené položky.</span><span class="sxs-lookup"><span data-stu-id="9875c-161">The current user must have write access to publish to the share.</span></span>
   * <span data-ttu-id="9875c-162">Pokud se vám nedaří nasadit přímo do složky webu služby IIS na serveru služby IIS, publikujte do složky na odstranitelném médiu a fyzicky přesuňte publikovanou aplikaci do složky webu služby IIS na serveru, což je **fyzická cesta** webu ve Správci služby IIS.</span><span class="sxs-lookup"><span data-stu-id="9875c-162">If you're unable to deploy directly to the IIS site folder on the IIS server, publish to a folder on removeable media and physically move the published app to the IIS site folder on the server, which is the site's **Physical path** in IIS Manager.</span></span> <span data-ttu-id="9875c-163">Přesuňte obsah složky *bin/Release/{TARGET FRAMEWORK}/publish* do složky webu služby IIS na serveru, což je **fyzická cesta** webu ve Správci služby IIS.</span><span class="sxs-lookup"><span data-stu-id="9875c-163">Move the contents of the *bin/Release/{TARGET FRAMEWORK}/publish* folder to the IIS site folder on the server, which is the site's **Physical path** in IIS Manager.</span></span>

---

## <a name="browse-the-website"></a><span data-ttu-id="9875c-164">Procházení webu</span><span class="sxs-lookup"><span data-stu-id="9875c-164">Browse the website</span></span>

<span data-ttu-id="9875c-165">Aplikace je přístupná v prohlížeči poté, co obdrží první požadavek.</span><span class="sxs-lookup"><span data-stu-id="9875c-165">The app is accessible in a browser after it receives the first request.</span></span> <span data-ttu-id="9875c-166">Požádejte o aplikaci na vazbě koncového bodu, kterou jste vytvořili ve Správci služby IIS pro web.</span><span class="sxs-lookup"><span data-stu-id="9875c-166">Make a request to the app at the endpoint binding that you established in IIS Manager for the site.</span></span>

## <a name="next-steps"></a><span data-ttu-id="9875c-167">Další kroky</span><span class="sxs-lookup"><span data-stu-id="9875c-167">Next steps</span></span>

<span data-ttu-id="9875c-168">V tomto kurzu jste se naučili:</span><span class="sxs-lookup"><span data-stu-id="9875c-168">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="9875c-169">Nainstalujte sadu .NET Core Hosting Na Windows Server.</span><span class="sxs-lookup"><span data-stu-id="9875c-169">Install the .NET Core Hosting Bundle on Windows Server.</span></span>
> * <span data-ttu-id="9875c-170">Vytvořte web služby IIS ve Správci služby IIS.</span><span class="sxs-lookup"><span data-stu-id="9875c-170">Create an IIS site in IIS Manager.</span></span>
> * <span data-ttu-id="9875c-171">Nasaďte aplikaci ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="9875c-171">Deploy an ASP.NET Core app.</span></span>

<span data-ttu-id="9875c-172">Další informace o hostování aplikací ASP.NET Core ve službě IIS naleznete v článku Přehled služby IIS:</span><span class="sxs-lookup"><span data-stu-id="9875c-172">To learn more about hosting ASP.NET Core apps on IIS, see the IIS Overview article:</span></span>

> [!div class="nextstepaction"]
> <xref:host-and-deploy/iis/index>

## <a name="additional-resources"></a><span data-ttu-id="9875c-173">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="9875c-173">Additional resources</span></span>

### <a name="articles-in-the-aspnet-core-documentation-set"></a><span data-ttu-id="9875c-174">Články v sadě dokumentace ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9875c-174">Articles in the ASP.NET Core documentation set</span></span>

* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:test/troubleshoot-azure-iis>
* <xref:security/enforcing-ssl>

### <a name="articles-pertaining-to-aspnet-core-app-deployment"></a><span data-ttu-id="9875c-175">Články týkající se nasazení aplikace ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9875c-175">Articles pertaining to ASP.NET Core app deployment</span></span>

* <xref:tutorials/publish-to-azure-webapp-using-vs>
* <xref:tutorials/publish-to-azure-webapp-using-vscode>
* <xref:host-and-deploy/visual-studio-publish-profiles>
* [<span data-ttu-id="9875c-176">Publikování webové aplikace do složky pomocí Visual Studia pro Mac</span><span class="sxs-lookup"><span data-stu-id="9875c-176">Publish a Web app to a folder using Visual Studio for Mac</span></span>](/visualstudio/mac/publish-folder)

### <a name="articles-on-iis-https-configuration"></a><span data-ttu-id="9875c-177">Články o konfiguraci protokolu HTTPS služby IIS</span><span class="sxs-lookup"><span data-stu-id="9875c-177">Articles on IIS HTTPS configuration</span></span>

* [<span data-ttu-id="9875c-178">Konfigurace ssl ve Správci služby IIS</span><span class="sxs-lookup"><span data-stu-id="9875c-178">Configuring SSL in IIS Manager</span></span>](/iis/manage/configuring-security/configuring-ssl-in-iis-manager)
* [<span data-ttu-id="9875c-179">Jak nastavit ssl ve službě IIS</span><span class="sxs-lookup"><span data-stu-id="9875c-179">How to Set Up SSL on IIS</span></span>](/iis/manage/configuring-security/how-to-set-up-ssl-on-iis)

### <a name="articles-on-iis-and-windows-server"></a><span data-ttu-id="9875c-180">Články o službě IIS a systému Windows Server</span><span class="sxs-lookup"><span data-stu-id="9875c-180">Articles on IIS and Windows Server</span></span>

* [<span data-ttu-id="9875c-181">Oficiální web služby Microsoft IIS</span><span class="sxs-lookup"><span data-stu-id="9875c-181">The Official Microsoft IIS Site</span></span>](https://www.iis.net/)
* [<span data-ttu-id="9875c-182">Knihovna technického obsahu systému Windows Server</span><span class="sxs-lookup"><span data-stu-id="9875c-182">Windows Server technical content library</span></span>](/windows-server/windows-server)
