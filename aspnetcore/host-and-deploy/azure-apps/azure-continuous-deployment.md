---
title: Průběžné nasazování do Azure pomocí sady Visual Studio a Gitu s ASP.NET Core
author: rick-anderson
description: Naučte se, jak vytvořit webovou aplikaci ASP.NET Core pomocí sady Visual Studio a nasadit ji do Azure App Service pomocí Gitu pro průběžné nasazování.
ms.author: riande
ms.custom: mvc
ms.date: 12/06/2018
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/azure-apps/azure-continuous-deployment
ms.openlocfilehash: 3a865c2c42cb71e109331675460456a27dc500fc
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775307"
---
# <a name="continuous-deployment-to-azure-with-visual-studio-and-git-with-aspnet-core"></a><span data-ttu-id="4a861-103">Průběžné nasazování do Azure pomocí sady Visual Studio a Gitu s ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4a861-103">Continuous deployment to Azure with Visual Studio and Git with ASP.NET Core</span></span>

<span data-ttu-id="4a861-104">Od [Erik Reitan](https://github.com/Erikre)</span><span class="sxs-lookup"><span data-stu-id="4a861-104">By [Erik Reitan](https://github.com/Erikre)</span></span>

[!INCLUDE [Azure App Service Preview Notice](../../includes/azure-apps-preview-notice.md)]

<span data-ttu-id="4a861-105">V tomto kurzu se dozvíte, jak vytvořit webovou aplikaci ASP.NET Core pomocí sady Visual Studio a nasadit ji ze sady Visual Studio na Azure App Service pomocí průběžného nasazování.</span><span class="sxs-lookup"><span data-stu-id="4a861-105">This tutorial shows how to create an ASP.NET Core web app using Visual Studio and deploy it from Visual Studio to Azure App Service using continuous deployment.</span></span>

<span data-ttu-id="4a861-106">Viz také [Vytvoření prvního kanálu pomocí Azure Pipelines](/azure/devops/pipelines/get-started-yaml), který ukazuje, jak nakonfigurovat pracovní postup pro průběžné doručování (CD) pro [Azure App Service](/azure/app-service/app-service-web-overview) pomocí Azure DevOps Services.</span><span class="sxs-lookup"><span data-stu-id="4a861-106">See also [Create your first pipeline with Azure Pipelines](/azure/devops/pipelines/get-started-yaml), which shows how to configure a continuous delivery (CD) workflow for [Azure App Service](/azure/app-service/app-service-web-overview) using Azure DevOps Services.</span></span> <span data-ttu-id="4a861-107">Azure Pipelines (služba Azure DevOps Services) zjednodušuje nastavení robustního kanálu nasazení pro publikování aktualizací pro aplikace hostované v Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="4a861-107">Azure Pipelines (an Azure DevOps Services service) simplifies setting up a robust deployment pipeline to publish updates for apps hosted in Azure App Service.</span></span> <span data-ttu-id="4a861-108">Kanál se dá nakonfigurovat z Azure Portal k sestavení, spuštění testů, nasazování do přípravného slotu a následnému nasazení do produkčního prostředí.</span><span class="sxs-lookup"><span data-stu-id="4a861-108">The pipeline can be configured from the Azure portal to build, run tests, deploy to a staging slot, and then deploy to production.</span></span>

> [!NOTE]
> <span data-ttu-id="4a861-109">K dokončení tohoto kurzu se vyžaduje účet Microsoft Azure.</span><span class="sxs-lookup"><span data-stu-id="4a861-109">To complete this tutorial, a Microsoft Azure account is required.</span></span> <span data-ttu-id="4a861-110">Pokud chcete získat účet, [Aktivujte si výhody pro předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A261C142F) nebo [si zaregistrujte bezplatnou zkušební verzi](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).</span><span class="sxs-lookup"><span data-stu-id="4a861-110">To obtain an account, [activate MSDN subscriber benefits](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A261C142F) or [sign up for a free trial](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="4a861-111">Požadavky</span><span class="sxs-lookup"><span data-stu-id="4a861-111">Prerequisites</span></span>

<span data-ttu-id="4a861-112">V tomto kurzu se předpokládá, že je nainstalovaný následující software:</span><span class="sxs-lookup"><span data-stu-id="4a861-112">This tutorial assumes the following software is installed:</span></span>

* [<span data-ttu-id="4a861-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4a861-113">Visual Studio</span></span>](https://visualstudio.microsoft.com)
* [!INCLUDE [](~/includes/net-core-sdk-download-link.md)]
* <span data-ttu-id="4a861-114">[Git](https://git-scm.com/downloads) pro Windows</span><span class="sxs-lookup"><span data-stu-id="4a861-114">[Git](https://git-scm.com/downloads) for Windows</span></span>

## <a name="create-an-aspnet-core-web-app"></a><span data-ttu-id="4a861-115">Vytvoření webové aplikace ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4a861-115">Create an ASP.NET Core web app</span></span>

1. <span data-ttu-id="4a861-116">Spusťte Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="4a861-116">Start Visual Studio.</span></span>

1. <span data-ttu-id="4a861-117">V nabídce **soubor** vyberte možnost **Nový** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="4a861-117">From the **File** menu, select **New** > **Project**.</span></span>

1. <span data-ttu-id="4a861-118">Vyberte šablonu projektu **ASP.NET Core webové aplikace** .</span><span class="sxs-lookup"><span data-stu-id="4a861-118">Select the **ASP.NET Core Web Application** project template.</span></span> <span data-ttu-id="4a861-119">Zobrazí se v části **nainstalované** > **šablony** > **Visual C#** > **.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="4a861-119">It appears under **Installed** > **Templates** > **Visual C#** > **.NET Core**.</span></span> <span data-ttu-id="4a861-120">Pojmenujte `SampleWebAppDemo`projekt.</span><span class="sxs-lookup"><span data-stu-id="4a861-120">Name the project `SampleWebAppDemo`.</span></span> <span data-ttu-id="4a861-121">Vyberte možnost **vytvořit novou Git úložiště** a klikněte na **OK**.</span><span class="sxs-lookup"><span data-stu-id="4a861-121">Select the **Create new Git repository** option and click **OK**.</span></span>

   ![Dialogové okno Nový projekt](azure-continuous-deployment/_static/01-new-project.png)

1. <span data-ttu-id="4a861-123">V dialogovém okně **Nový projekt ASP.NET Core** vyberte ASP.NET Core **prázdnou** šablonu a pak klikněte na **OK**.</span><span class="sxs-lookup"><span data-stu-id="4a861-123">In the **New ASP.NET Core Project** dialog, select the ASP.NET Core **Empty** template, then click **OK**.</span></span>

   ![Dialog nového projektu ASP.NET Core](azure-continuous-deployment/_static/02-web-site-template.png)

> [!NOTE]
> <span data-ttu-id="4a861-125">Nejnovější verze .NET Core je 2,0.</span><span class="sxs-lookup"><span data-stu-id="4a861-125">The most recent release of .NET Core is 2.0.</span></span>

### <a name="running-the-web-app-locally"></a><span data-ttu-id="4a861-126">Místní spuštění webové aplikace</span><span class="sxs-lookup"><span data-stu-id="4a861-126">Running the web app locally</span></span>

1. <span data-ttu-id="4a861-127">Jakmile Visual Studio dokončí vytváření aplikace **, spusťte aplikaci výběrem** > ladění**Spustit ladění**.</span><span class="sxs-lookup"><span data-stu-id="4a861-127">Once Visual Studio finishes creating the app, run the app by selecting **Debug** > **Start Debugging**.</span></span> <span data-ttu-id="4a861-128">Jako alternativu stiskněte klávesu **F5**.</span><span class="sxs-lookup"><span data-stu-id="4a861-128">As an alternative, press **F5**.</span></span>

   <span data-ttu-id="4a861-129">Inicializace sady Visual Studio a nové aplikace může chvíli trvat.</span><span class="sxs-lookup"><span data-stu-id="4a861-129">It may take time to initialize Visual Studio and the new app.</span></span> <span data-ttu-id="4a861-130">Po dokončení se v prohlížeči zobrazí spuštěná aplikace.</span><span class="sxs-lookup"><span data-stu-id="4a861-130">Once it's complete, the browser shows the running app.</span></span>

   ![Okno prohlížeče zobrazující běžící aplikaci, která zobrazuje Hello World!](azure-continuous-deployment/_static/04-browser-runapp.png)

1. <span data-ttu-id="4a861-132">Po kontrole běžící webové aplikace zavřete prohlížeč a na panelu nástrojů sady Visual Studio vyberte ikonu Zastavit ladění a zastavte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="4a861-132">After reviewing the running Web app, close the browser and select the "Stop Debugging" icon in the toolbar of Visual Studio to stop the app.</span></span>

## <a name="create-a-web-app-in-the-azure-portal"></a><span data-ttu-id="4a861-133">Vytvoření webové aplikace na webu Azure Portal</span><span class="sxs-lookup"><span data-stu-id="4a861-133">Create a web app in the Azure Portal</span></span>

<span data-ttu-id="4a861-134">Pomocí následujících kroků vytvoříte webovou aplikaci na webu Azure Portal:</span><span class="sxs-lookup"><span data-stu-id="4a861-134">The following steps create a web app in the Azure Portal:</span></span>

1. <span data-ttu-id="4a861-135">Přihlaste se k webu [Azure Portal](https://portal.azure.com).</span><span class="sxs-lookup"><span data-stu-id="4a861-135">Log in to the [Azure Portal](https://portal.azure.com).</span></span>

1. <span data-ttu-id="4a861-136">Vyberte **Nový** v levém horním rohu rozhraní portálu.</span><span class="sxs-lookup"><span data-stu-id="4a861-136">Select **NEW** at the top left of the portal interface.</span></span>

1. <span data-ttu-id="4a861-137">Vyberte **web a mobilní zařízení** > **webové aplikace**.</span><span class="sxs-lookup"><span data-stu-id="4a861-137">Select **Web + Mobile** > **Web App**.</span></span>

   ![Microsoft Azure Portal: nové tlačítko: Web a mobilní zařízení na webu Marketplace: tlačítko webové aplikace v oblasti vybrané aplikace](azure-continuous-deployment/_static/05-azure-newwebapp.png)

1. <span data-ttu-id="4a861-139">V okně **Webová aplikace** zadejte jedinečnou hodnotu **názvu App Service**.</span><span class="sxs-lookup"><span data-stu-id="4a861-139">In the **Web App** blade, enter a unique value for the **App Service Name**.</span></span>

   ![Okno webové aplikace](azure-continuous-deployment/_static/06-azure-newappblade.png)

   > [!NOTE]
   > <span data-ttu-id="4a861-141">Název **App Service** musí být jedinečný.</span><span class="sxs-lookup"><span data-stu-id="4a861-141">The **App Service Name** name must be unique.</span></span> <span data-ttu-id="4a861-142">Portál toto pravidlo vynutil, pokud je zadán název.</span><span class="sxs-lookup"><span data-stu-id="4a861-142">The portal enforces this rule when the name is provided.</span></span> <span data-ttu-id="4a861-143">Pokud zadáte jinou hodnotu, nahraďte tuto hodnotu pro každý výskyt **SampleWebAppDemo** v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="4a861-143">If providing a different value, substitute that value for each occurrence of **SampleWebAppDemo** in this tutorial.</span></span>

   <span data-ttu-id="4a861-144">V okně **Webová aplikace** vyberte existující **App Service plán/umístění** nebo vytvořte nový.</span><span class="sxs-lookup"><span data-stu-id="4a861-144">Also in the **Web App** blade, select an existing **App Service Plan/Location** or create a new one.</span></span> <span data-ttu-id="4a861-145">Pokud vytváříte nový plán, vyberte cenovou úroveň, umístění a další možnosti.</span><span class="sxs-lookup"><span data-stu-id="4a861-145">If creating a new plan, select the pricing tier, location, and other options.</span></span> <span data-ttu-id="4a861-146">Další informace o plánech App Service najdete [v podrobných informacích o Azure App Servicech plánech](/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview).</span><span class="sxs-lookup"><span data-stu-id="4a861-146">For more information on App Service plans, see [Azure App Service plans in-depth overview](/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview).</span></span>

1. <span data-ttu-id="4a861-147">Vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="4a861-147">Select **Create**.</span></span> <span data-ttu-id="4a861-148">Azure zřídí a spustí webovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="4a861-148">Azure will provision and start the web app.</span></span>

   ![Portál Azure Portal: Ukázka webové aplikace ukázka 01 základní okno](azure-continuous-deployment/_static/07-azure-webappblade.png)

## <a name="enable-git-publishing-for-the-new-web-app"></a><span data-ttu-id="4a861-150">Povolit publikování Gitu pro novou webovou aplikaci</span><span class="sxs-lookup"><span data-stu-id="4a861-150">Enable Git publishing for the new web app</span></span>

<span data-ttu-id="4a861-151">Git je distribuovaný systém správy verzí, který se dá použít k nasazení Azure App Service webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="4a861-151">Git is a distributed version control system that can be used to deploy an Azure App Service web app.</span></span> <span data-ttu-id="4a861-152">Kód webové aplikace je uložený v místním úložišti Git a kód se nasadí do Azure tím, že se uloží do vzdáleného úložiště.</span><span class="sxs-lookup"><span data-stu-id="4a861-152">Web app code is stored in a local Git repository, and the code is deployed to Azure by pushing to a remote repository.</span></span>

1. <span data-ttu-id="4a861-153">Přihlaste se k webu [Azure Portal](https://portal.azure.com).</span><span class="sxs-lookup"><span data-stu-id="4a861-153">Log into the [Azure Portal](https://portal.azure.com).</span></span>

1. <span data-ttu-id="4a861-154">Výběrem **App Services** zobrazíte seznam aplikačních služeb přidružených k předplatnému Azure.</span><span class="sxs-lookup"><span data-stu-id="4a861-154">Select **App Services** to view a list of the app services associated with the Azure subscription.</span></span>

1. <span data-ttu-id="4a861-155">Vyberte webovou aplikaci vytvořenou v předchozí části tohoto kurzu.</span><span class="sxs-lookup"><span data-stu-id="4a861-155">Select the web app created in the previous section of this tutorial.</span></span>

1. <span data-ttu-id="4a861-156">V okně **nasazení** vyberte **Možnosti** > nasazení**zvolit zdrojové** > **místní úložiště Git**.</span><span class="sxs-lookup"><span data-stu-id="4a861-156">In the **Deployment** blade, select **Deployment options** > **Choose Source** > **Local Git Repository**.</span></span>

   ![Okno nastavení: okno zdroje nasazení: zvolit zdrojový okno](azure-continuous-deployment/_static/deployment-options.png)

1. <span data-ttu-id="4a861-158">Vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="4a861-158">Select **OK**.</span></span>

1. <span data-ttu-id="4a861-159">Pokud se přihlašovací údaje nasazení pro publikování webové aplikace nebo jiné aplikace App Service ještě neudělaly, nastavte je teď:</span><span class="sxs-lookup"><span data-stu-id="4a861-159">If deployment credentials for publishing a web app or other App Service app haven't previously been set up, set them up now:</span></span>

   * <span data-ttu-id="4a861-160">Vyberte **Nastavení** > **přihlašovací údaje pro nasazení**.</span><span class="sxs-lookup"><span data-stu-id="4a861-160">Select **Settings** > **Deployment credentials**.</span></span> <span data-ttu-id="4a861-161">Zobrazí se okno **nastavit přihlašovací údaje nasazení** .</span><span class="sxs-lookup"><span data-stu-id="4a861-161">The **Set deployment credentials** blade is displayed.</span></span>
   * <span data-ttu-id="4a861-162">Vytvořte uživatelské jméno a heslo.</span><span class="sxs-lookup"><span data-stu-id="4a861-162">Create a user name and password.</span></span> <span data-ttu-id="4a861-163">Uložte heslo pro pozdější použití při nastavování Gitu.</span><span class="sxs-lookup"><span data-stu-id="4a861-163">Save the password for later use when setting up Git.</span></span>
   * <span data-ttu-id="4a861-164">Vyberte **Uložit**.</span><span class="sxs-lookup"><span data-stu-id="4a861-164">Select **Save**.</span></span>

1. <span data-ttu-id="4a861-165">V okně **Webová aplikace** vyberte **Nastavení** > **vlastnosti**.</span><span class="sxs-lookup"><span data-stu-id="4a861-165">In the **Web App** blade, select **Settings** > **Properties**.</span></span> <span data-ttu-id="4a861-166">Adresa URL vzdáleného úložiště Git, na který se má nasadit, se zobrazí v části **Adresa URL Gitu**.</span><span class="sxs-lookup"><span data-stu-id="4a861-166">The URL of the remote Git repository to deploy to is shown under **GIT URL**.</span></span>

1. <span data-ttu-id="4a861-167">Zkopírujte hodnotu **adresy URL Gitu** pro pozdější použití v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="4a861-167">Copy the **GIT URL** value for later use in the tutorial.</span></span>

   ![Portál Azure Portal: okno vlastností aplikace](azure-continuous-deployment/_static/09-azure-giturl.png)

## <a name="publish-the-web-app-to-azure-app-service"></a><span data-ttu-id="4a861-169">Publikování webové aplikace do služby Azure App Service</span><span class="sxs-lookup"><span data-stu-id="4a861-169">Publish the web app to Azure App Service</span></span>

<span data-ttu-id="4a861-170">V této části vytvoříte místní úložiště Git pomocí sady Visual Studio a nahrajete ho z tohoto úložiště do Azure a nasadíte webovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="4a861-170">In this section, create a local Git repository using Visual Studio and push from that repository to Azure to deploy the web app.</span></span> <span data-ttu-id="4a861-171">Mezi zahrnuté kroky patří následující:</span><span class="sxs-lookup"><span data-stu-id="4a861-171">The steps involved include the following:</span></span>

* <span data-ttu-id="4a861-172">Přidejte nastavení vzdáleného úložiště pomocí hodnoty adresy URL GITU, aby bylo možné místní úložiště nasadit do Azure.</span><span class="sxs-lookup"><span data-stu-id="4a861-172">Add the remote repository setting using the GIT URL value, so the local repository can be deployed to Azure.</span></span>
* <span data-ttu-id="4a861-173">Potvrďte změny projektu.</span><span class="sxs-lookup"><span data-stu-id="4a861-173">Commit project changes.</span></span>
* <span data-ttu-id="4a861-174">Vložení změn projektu z místního úložiště do vzdáleného úložiště v Azure</span><span class="sxs-lookup"><span data-stu-id="4a861-174">Push project changes from the local repository to the remote repository on Azure.</span></span>

1. <span data-ttu-id="4a861-175">V **Průzkumník řešení** klikněte pravým tlačítkem myši na **řešení SampleWebAppDemo** a vyberte **Potvrdit**.</span><span class="sxs-lookup"><span data-stu-id="4a861-175">In **Solution Explorer** right-click **Solution 'SampleWebAppDemo'** and select **Commit**.</span></span> <span data-ttu-id="4a861-176">Zobrazí se **Team Explorer**.</span><span class="sxs-lookup"><span data-stu-id="4a861-176">The **Team Explorer** is displayed.</span></span>

   ![Karta Team Explorer připojit](azure-continuous-deployment/_static/10-team-explorer.png)

1. <span data-ttu-id="4a861-178">V **Team Explorer**vyberte **Domů** (ikona domů **) >** > nastavení**úložiště**.</span><span class="sxs-lookup"><span data-stu-id="4a861-178">In **Team Explorer**, select the **Home** (home icon) > **Settings** > **Repository Settings**.</span></span>

1. <span data-ttu-id="4a861-179">V části **Vzdálená úložiště** vyberte v **Nastavení úložiště\*\*\*\*Přidat**.</span><span class="sxs-lookup"><span data-stu-id="4a861-179">In the **Remotes** section of the **Repository Settings**, select **Add**.</span></span> <span data-ttu-id="4a861-180">Zobrazí se dialogové okno **Přidat vzdálené úložiště**.</span><span class="sxs-lookup"><span data-stu-id="4a861-180">The **Add Remote** dialog box is displayed.</span></span>

1. <span data-ttu-id="4a861-181">Nastavte **název** vzdáleného na **Azure-dotazů**.</span><span class="sxs-lookup"><span data-stu-id="4a861-181">Set the **Name** of the remote to **Azure-SampleApp**.</span></span>

1. <span data-ttu-id="4a861-182">Nastavte hodnotu pro **načíst** na **adresu URL Gitu** zkopírovanou z Azure dřív v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="4a861-182">Set the value for **Fetch** to the **Git URL** that copied from Azure earlier in this tutorial.</span></span> <span data-ttu-id="4a861-183">Všimněte si, že toto je adresa URL, která končí na **. Git**.</span><span class="sxs-lookup"><span data-stu-id="4a861-183">Note that this is the URL that ends with **.git**.</span></span>

   ![Upravit vzdálené dialogové okno](azure-continuous-deployment/_static/11-add-remote.png)

   > [!NOTE]
   > <span data-ttu-id="4a861-185">Jako alternativu určete vzdálené úložiště z **příkazového okna** otevřením **okna příkazového**řádku, přepnutím do adresáře projektu a zadáním příkazu.</span><span class="sxs-lookup"><span data-stu-id="4a861-185">As an alternative, specify the remote repository from the **Command Window** by opening the **Command Window**, changing to the project directory, and entering the command.</span></span> <span data-ttu-id="4a861-186">Příklad:</span><span class="sxs-lookup"><span data-stu-id="4a861-186">Example:</span></span>
   >
   > `git remote add Azure-SampleApp https://me@sampleapp.scm.azurewebsites.net:443/SampleApp.git`

1. <span data-ttu-id="4a861-187">Vyberte **Domů** (ikona domů) > **Nastavení** > **globální nastavení**.</span><span class="sxs-lookup"><span data-stu-id="4a861-187">Select the **Home** (home icon) > **Settings** > **Global Settings**.</span></span> <span data-ttu-id="4a861-188">Zkontrolujte, jestli je zadané jméno a e-mailová adresa.</span><span class="sxs-lookup"><span data-stu-id="4a861-188">Confirm that the name and email address are set.</span></span> <span data-ttu-id="4a861-189">V případě potřeby vyberte **aktualizovat** .</span><span class="sxs-lookup"><span data-stu-id="4a861-189">Select **Update** if required.</span></span>

1. <span data-ttu-id="4a861-190">Vyberte možnost **domovské** > **změny** a vraťte se do zobrazení **změny** .</span><span class="sxs-lookup"><span data-stu-id="4a861-190">Select **Home** > **Changes** to return to the **Changes** view.</span></span>

1. <span data-ttu-id="4a861-191">Zadejte potvrzovací zprávu, například **počáteční #1 nabízených oznámení** , a vyberte **Potvrdit**.</span><span class="sxs-lookup"><span data-stu-id="4a861-191">Enter a commit message, such as **Initial Push #1** and select **Commit**.</span></span> <span data-ttu-id="4a861-192">Tato akce vytvoří místní *potvrzení* .</span><span class="sxs-lookup"><span data-stu-id="4a861-192">This action creates a *commit* locally.</span></span>

   ![Karta Team Explorer připojit](azure-continuous-deployment/_static/12-initial-commit.png)

   > [!NOTE]
   > <span data-ttu-id="4a861-194">Jako alternativu potvrďte změny z **příkazového okna** tak, že otevřete **okno příkazového**řádku, změníte adresář projektu a zadáte příkazy Gitu.</span><span class="sxs-lookup"><span data-stu-id="4a861-194">As an alternative, commit changes from the **Command Window** by opening the **Command Window**, changing to the project directory, and entering the git commands.</span></span> <span data-ttu-id="4a861-195">Příklad:</span><span class="sxs-lookup"><span data-stu-id="4a861-195">Example:</span></span>
   >
   > `git add .`
   >
   > `git commit -am "Initial Push #1"`

1. <span data-ttu-id="4a861-196">Vyberte > **Akce**pro **domovskou** > **synchronizaci** > **otevřít příkazový řádek**.</span><span class="sxs-lookup"><span data-stu-id="4a861-196">Select **Home** > **Sync** > **Actions** > **Open Command Prompt**.</span></span> <span data-ttu-id="4a861-197">Příkazový řádek se otevře v adresáři projektu.</span><span class="sxs-lookup"><span data-stu-id="4a861-197">The command prompt opens to the project directory.</span></span>

1. <span data-ttu-id="4a861-198">V příkazovém okně zadejte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="4a861-198">Enter the following command in the command window:</span></span>

   `git push -u Azure-SampleApp master`

1. <span data-ttu-id="4a861-199">Zadejte heslo k **přihlašovacím údajům nasazení** Azure, které jste vytvořili dříve v Azure.</span><span class="sxs-lookup"><span data-stu-id="4a861-199">Enter the Azure **deployment credentials** password created earlier in Azure.</span></span>

   <span data-ttu-id="4a861-200">Tento příkaz spustí proces vložení místních souborů projektu do Azure.</span><span class="sxs-lookup"><span data-stu-id="4a861-200">This command starts the process of pushing the local project files to Azure.</span></span> <span data-ttu-id="4a861-201">Výstup z výše uvedeného příkazu končí zprávou, že nasazení proběhlo úspěšně.</span><span class="sxs-lookup"><span data-stu-id="4a861-201">The output from the above command ends with a message that the deployment was successful.</span></span>

   ```
   remote: Finished successfully.
   remote: Running post deployment command(s)...
   remote: Deployment successful.
   To https://username@samplewebappdemo01.scm.azurewebsites.net:443/SampleWebAppDemo01.git
   * [new branch]      master -> master
   Branch master set up to track remote branch master from Azure-SampleApp.
   ```

   > [!NOTE]
   > <span data-ttu-id="4a861-202">Pokud je potřeba spolupráce na projektu, zvažte, že před nahráním do Azure budete moct přecházet do [GitHubu](https://github.com) .</span><span class="sxs-lookup"><span data-stu-id="4a861-202">If collaboration on the project is required, consider pushing to [GitHub](https://github.com) before pushing to Azure.</span></span>
 
### <a name="verify-the-active-deployment"></a><span data-ttu-id="4a861-203">Ověřit aktivní nasazení</span><span class="sxs-lookup"><span data-stu-id="4a861-203">Verify the Active Deployment</span></span>

<span data-ttu-id="4a861-204">Ověřte, jestli je webová aplikace přenesená z místního prostředí do Azure úspěšná.</span><span class="sxs-lookup"><span data-stu-id="4a861-204">Verify that the web app transfer from the local environment to Azure is successful.</span></span>

<span data-ttu-id="4a861-205">Na webu [Azure Portal](https://portal.azure.com)vyberte webovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="4a861-205">In the [Azure Portal](https://portal.azure.com), select the web app.</span></span> <span data-ttu-id="4a861-206">Vyberte **Deployment** > **Možnosti nasazení**nasazení.</span><span class="sxs-lookup"><span data-stu-id="4a861-206">Select **Deployment** > **Deployment options**.</span></span>

![Azure Portal: okno nastavení: okno nasazení znázorňující úspěšné nasazení](azure-continuous-deployment/_static/13-verify-deployment.png)

## <a name="run-the-app-in-azure"></a><span data-ttu-id="4a861-208">Spuštění aplikace v Azure</span><span class="sxs-lookup"><span data-stu-id="4a861-208">Run the app in Azure</span></span>

<span data-ttu-id="4a861-209">Teď, když je webová aplikace nasazená do Azure, spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="4a861-209">Now that the web app is deployed to Azure, run the app.</span></span>

<span data-ttu-id="4a861-210">Toho lze dosáhnout dvěma způsoby:</span><span class="sxs-lookup"><span data-stu-id="4a861-210">This can be accomplished in two ways:</span></span>

* <span data-ttu-id="4a861-211">Na webu Azure Portal vyhledejte okno webová aplikace pro webovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="4a861-211">In the Azure Portal, locate the web app blade for the web app.</span></span> <span data-ttu-id="4a861-212">Vyberte **Procházet** a zobrazte aplikaci ve výchozím prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="4a861-212">Select **Browse** to view the app in the default browser.</span></span>
* <span data-ttu-id="4a861-213">Otevřete prohlížeč a zadejte adresu URL webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="4a861-213">Open a browser and enter the URL for the web app.</span></span> <span data-ttu-id="4a861-214">Příklad: `http://SampleWebAppDemo.azurewebsites.net`</span><span class="sxs-lookup"><span data-stu-id="4a861-214">Example: `http://SampleWebAppDemo.azurewebsites.net`</span></span>

## <a name="update-the-web-app-and-republish"></a><span data-ttu-id="4a861-215">Aktualizace webové aplikace a opětovné publikování</span><span class="sxs-lookup"><span data-stu-id="4a861-215">Update the web app and republish</span></span>

<span data-ttu-id="4a861-216">Po provedení změn v místním kódu znovu publikujte:</span><span class="sxs-lookup"><span data-stu-id="4a861-216">After making changes to the local code, republish:</span></span>

1. <span data-ttu-id="4a861-217">V **Průzkumník řešení** sady Visual Studio otevřete soubor *Startup.cs* .</span><span class="sxs-lookup"><span data-stu-id="4a861-217">In **Solution Explorer** of Visual Studio, open the *Startup.cs* file.</span></span>

1. <span data-ttu-id="4a861-218">V `Configure` metodě upravte `Response.WriteAsync` metodu tak, aby se zobrazila takto:</span><span class="sxs-lookup"><span data-stu-id="4a861-218">In the `Configure` method, modify the `Response.WriteAsync` method so that it appears as follows:</span></span>

   ```csharp
   await context.Response.WriteAsync("Hello World! Deploy to Azure.");
   ```

1. <span data-ttu-id="4a861-219">Uložte změny do *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="4a861-219">Save the changes to *Startup.cs*.</span></span>

1. <span data-ttu-id="4a861-220">V **Průzkumník řešení**klikněte pravým tlačítkem na **řešení "SampleWebAppDemo"** a vyberte **Potvrdit**.</span><span class="sxs-lookup"><span data-stu-id="4a861-220">In **Solution Explorer**, right-click **Solution 'SampleWebAppDemo'** and select **Commit**.</span></span> <span data-ttu-id="4a861-221">Zobrazí se **Team Explorer**.</span><span class="sxs-lookup"><span data-stu-id="4a861-221">The **Team Explorer** is displayed.</span></span>

1. <span data-ttu-id="4a861-222">Zadejte potvrzovací zprávu, například `Update #2`.</span><span class="sxs-lookup"><span data-stu-id="4a861-222">Enter a commit message, such as `Update #2`.</span></span>

1. <span data-ttu-id="4a861-223">Kliknutím na tlačítko **Potvrdit** potvrďte změny projektu.</span><span class="sxs-lookup"><span data-stu-id="4a861-223">Press the **Commit** button to commit the project changes.</span></span>

1. <span data-ttu-id="4a861-224">Vyberte akce pro **domovskou** > **synchronizaci** > **Akce** > **push**.</span><span class="sxs-lookup"><span data-stu-id="4a861-224">Select **Home** > **Sync** > **Actions** > **Push**.</span></span>

> [!NOTE]
> <span data-ttu-id="4a861-225">Alternativně můžete vložit změny z **příkazového okna** otevřením **okna příkazového**řádku, přepnutím do adresáře projektu a zadáním příkazu git.</span><span class="sxs-lookup"><span data-stu-id="4a861-225">As an alternative, push the changes from the **Command Window** by opening the **Command Window**, changing to the project directory, and entering a git command.</span></span> <span data-ttu-id="4a861-226">Příklad:</span><span class="sxs-lookup"><span data-stu-id="4a861-226">Example:</span></span>
> 
> `git push -u Azure-SampleApp master`

## <a name="view-the-updated-web-app-in-azure"></a><span data-ttu-id="4a861-227">Zobrazení aktualizované webové aplikace v Azure</span><span class="sxs-lookup"><span data-stu-id="4a861-227">View the updated web app in Azure</span></span>

<span data-ttu-id="4a861-228">Kliknutím na **Procházet** v okně webové aplikace na webu Azure Portal nebo otevřením prohlížeče a zadáním adresy URL webové aplikace Zobrazte aktualizovanou webovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="4a861-228">View the updated web app by selecting **Browse** from the web app blade in the Azure Portal or by opening a browser and entering the URL for the web app.</span></span> <span data-ttu-id="4a861-229">Příklad: `http://SampleWebAppDemo.azurewebsites.net`</span><span class="sxs-lookup"><span data-stu-id="4a861-229">Example: `http://SampleWebAppDemo.azurewebsites.net`</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4a861-230">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="4a861-230">Additional resources</span></span>

* [<span data-ttu-id="4a861-231">Vytvoření prvního kanálu pomocí Azure Pipelines</span><span class="sxs-lookup"><span data-stu-id="4a861-231">Create your first pipeline with Azure Pipelines</span></span>](/azure/devops/pipelines/get-started-yaml)
* [<span data-ttu-id="4a861-232">Projekt Kudu</span><span class="sxs-lookup"><span data-stu-id="4a861-232">Project Kudu</span></span>](https://github.com/projectkudu/kudu/wiki)
* <xref:host-and-deploy/visual-studio-publish-profiles>
