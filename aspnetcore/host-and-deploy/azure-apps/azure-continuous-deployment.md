---
title: Průběžné nasazování do Azure pomocí sady Visual Studio a Gitu s ASP.NET Core
author: rick-anderson
description: Zjistěte, jak vytvořit webovou aplikaci ASP.NET Core pomocí sady Visual Studio a nasaďte ji do služby Azure App Service pro průběžné nasazování pomocí Gitu.
ms.author: riande
ms.custom: mvc
ms.date: 12/06/2018
uid: host-and-deploy/azure-apps/azure-continuous-deployment
ms.openlocfilehash: e12c2ee0b78db105b431770e8644e7d19d915765
ms.sourcegitcommit: b34b25da2ab68e6495b2460ff570468f16a9bf0d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53284421"
---
# <a name="continuous-deployment-to-azure-with-visual-studio-and-git-with-aspnet-core"></a><span data-ttu-id="c1eda-103">Průběžné nasazování do Azure pomocí sady Visual Studio a Gitu s ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c1eda-103">Continuous deployment to Azure with Visual Studio and Git with ASP.NET Core</span></span>

<span data-ttu-id="c1eda-104">podle [Erik Reitan](https://github.com/Erikre)</span><span class="sxs-lookup"><span data-stu-id="c1eda-104">By [Erik Reitan](https://github.com/Erikre)</span></span>

[!INCLUDE [Azure App Service Preview Notice](../../includes/azure-apps-preview-notice.md)]

<span data-ttu-id="c1eda-105">Tento kurz ukazuje, jak vytvořit webovou aplikaci ASP.NET Core pomocí sady Visual Studio a nasadit ho z Visual Studio do služby Azure App Service pomocí průběžného nasazování.</span><span class="sxs-lookup"><span data-stu-id="c1eda-105">This tutorial shows how to create an ASP.NET Core web app using Visual Studio and deploy it from Visual Studio to Azure App Service using continuous deployment.</span></span>

<span data-ttu-id="c1eda-106">Viz také [vytvořit svůj první kanál s kanály Azure](/azure/devops/pipelines/get-started-yaml), který ukazuje postup při konfiguraci pracovního postupu průběžné doručování (CD) pro [služby Azure App Service](/azure/app-service/app-service-web-overview) pomocí služby Azure DevOps.</span><span class="sxs-lookup"><span data-stu-id="c1eda-106">See also [Create your first pipeline with Azure Pipelines](/azure/devops/pipelines/get-started-yaml), which shows how to configure a continuous delivery (CD) workflow for [Azure App Service](/azure/app-service/app-service-web-overview) using Azure DevOps Services.</span></span> <span data-ttu-id="c1eda-107">Kanály Azure (služby Azure DevOps služby), zjednodušuje zřízení robustního kanálu nasazení publikovat aktualizace pro aplikace hostované ve službě Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="c1eda-107">Azure Pipelines (an Azure DevOps Services service) simplifies setting up a robust deployment pipeline to publish updates for apps hosted in Azure App Service.</span></span> <span data-ttu-id="c1eda-108">Kanál je možné nakonfigurovat z portálu Azure portal k vytvoření, spuštění testů, nasazení do přípravného slotu a pak nasadit do produkčního prostředí.</span><span class="sxs-lookup"><span data-stu-id="c1eda-108">The pipeline can be configured from the Azure portal to build, run tests, deploy to a staging slot, and then deploy to production.</span></span>

> [!NOTE]
> <span data-ttu-id="c1eda-109">K dokončení tohoto kurzu je nutné účet Microsoft Azure.</span><span class="sxs-lookup"><span data-stu-id="c1eda-109">To complete this tutorial, a Microsoft Azure account is required.</span></span> <span data-ttu-id="c1eda-110">Získat účet služby se [aktivovat výhody pro předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A261C142F) nebo [zaregistrujte si bezplatnou zkušební verzi](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).</span><span class="sxs-lookup"><span data-stu-id="c1eda-110">To obtain an account, [activate MSDN subscriber benefits](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A261C142F) or [sign up for a free trial](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="c1eda-111">Požadavky</span><span class="sxs-lookup"><span data-stu-id="c1eda-111">Prerequisites</span></span>

<span data-ttu-id="c1eda-112">Tento kurz předpokládá, že je nainstalovaný následující software:</span><span class="sxs-lookup"><span data-stu-id="c1eda-112">This tutorial assumes the following software is installed:</span></span>

* [<span data-ttu-id="c1eda-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c1eda-113">Visual Studio</span></span>](https://www.visualstudio.com)
* [!INCLUDE [](~/includes/net-core-sdk-download-link.md)]
* <span data-ttu-id="c1eda-114">[Git](https://git-scm.com/downloads) pro Windows</span><span class="sxs-lookup"><span data-stu-id="c1eda-114">[Git](https://git-scm.com/downloads) for Windows</span></span>

## <a name="create-an-aspnet-core-web-app"></a><span data-ttu-id="c1eda-115">Vytvoření webové aplikace ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c1eda-115">Create an ASP.NET Core web app</span></span>

1. <span data-ttu-id="c1eda-116">Spusťte Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="c1eda-116">Start Visual Studio.</span></span>

1. <span data-ttu-id="c1eda-117">Z **souboru** nabídce vyberte možnost **nový** > **projektu**.</span><span class="sxs-lookup"><span data-stu-id="c1eda-117">From the **File** menu, select **New** > **Project**.</span></span>

1. <span data-ttu-id="c1eda-118">Vyberte **webové aplikace ASP.NET Core** šablony projektu.</span><span class="sxs-lookup"><span data-stu-id="c1eda-118">Select the **ASP.NET Core Web Application** project template.</span></span> <span data-ttu-id="c1eda-119">Zobrazí se pod **nainstalováno** > **šablony** > **Visual C#** > **.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="c1eda-119">It appears under **Installed** > **Templates** > **Visual C#** > **.NET Core**.</span></span> <span data-ttu-id="c1eda-120">Pojmenujte projekt `SampleWebAppDemo`.</span><span class="sxs-lookup"><span data-stu-id="c1eda-120">Name the project `SampleWebAppDemo`.</span></span> <span data-ttu-id="c1eda-121">Vyberte **vytvořit nové úložiště Git** možnost a klikněte na tlačítko **OK**.</span><span class="sxs-lookup"><span data-stu-id="c1eda-121">Select the **Create new Git repository** option and click **OK**.</span></span>

   ![Dialogové okno nového projektu](azure-continuous-deployment/_static/01-new-project.png)

1. <span data-ttu-id="c1eda-123">V **nový projekt ASP.NET Core** dialogového okna, vyberte ASP.NET Core **prázdný** šablony, klikněte na **OK**.</span><span class="sxs-lookup"><span data-stu-id="c1eda-123">In the **New ASP.NET Core Project** dialog, select the ASP.NET Core **Empty** template, then click **OK**.</span></span>

   ![Dialogové okno Nový projekt ASP.NET Core](azure-continuous-deployment/_static/02-web-site-template.png)

> [!NOTE]
> <span data-ttu-id="c1eda-125">Nejnovější verzi sady .NET Core je 2.0.</span><span class="sxs-lookup"><span data-stu-id="c1eda-125">The most recent release of .NET Core is 2.0.</span></span>

### <a name="running-the-web-app-locally"></a><span data-ttu-id="c1eda-126">Místní spuštění webové aplikace</span><span class="sxs-lookup"><span data-stu-id="c1eda-126">Running the web app locally</span></span>

1. <span data-ttu-id="c1eda-127">Jakmile sada Visual Studio dokončí vytváření aplikace, spusťte aplikaci tak, že vyberete **ladění** > **spustit ladění**.</span><span class="sxs-lookup"><span data-stu-id="c1eda-127">Once Visual Studio finishes creating the app, run the app by selecting **Debug** > **Start Debugging**.</span></span> <span data-ttu-id="c1eda-128">Jako alternativu, stiskněte klávesu **F5**.</span><span class="sxs-lookup"><span data-stu-id="c1eda-128">As an alternative, press **F5**.</span></span>

   <span data-ttu-id="c1eda-129">Může trvat dobu k inicializaci sady Visual Studio a novou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="c1eda-129">It may take time to initialize Visual Studio and the new app.</span></span> <span data-ttu-id="c1eda-130">Po dokončení zobrazí v prohlížeči spuštěné aplikaci.</span><span class="sxs-lookup"><span data-stu-id="c1eda-130">Once it's complete, the browser shows the running app.</span></span>

   ![Zobrazení okna prohlížeče spuštěnou aplikaci, která se zobrazí "Hello World!"](azure-continuous-deployment/_static/04-browser-runapp.png)

1. <span data-ttu-id="c1eda-132">Po zkontrolování funkční webovou aplikaci, ukončete prohlížeč a vyberte ikonu "Zastavit ladění" v panelu nástrojů sady Visual Studio a zastavte tak aplikace.</span><span class="sxs-lookup"><span data-stu-id="c1eda-132">After reviewing the running Web app, close the browser and select the "Stop Debugging" icon in the toolbar of Visual Studio to stop the app.</span></span>

## <a name="create-a-web-app-in-the-azure-portal"></a><span data-ttu-id="c1eda-133">Vytvoření webové aplikace na webu Azure Portal</span><span class="sxs-lookup"><span data-stu-id="c1eda-133">Create a web app in the Azure Portal</span></span>

<span data-ttu-id="c1eda-134">Následující postup vytvoření webové aplikace na webu Azure Portal:</span><span class="sxs-lookup"><span data-stu-id="c1eda-134">The following steps create a web app in the Azure Portal:</span></span>

1. <span data-ttu-id="c1eda-135">Přihlaste se k [webu Azure Portal](https://portal.azure.com).</span><span class="sxs-lookup"><span data-stu-id="c1eda-135">Log in to the [Azure Portal](https://portal.azure.com).</span></span>

1. <span data-ttu-id="c1eda-136">Vyberte **nový** v levém horním rohu portálu rozhraní.</span><span class="sxs-lookup"><span data-stu-id="c1eda-136">Select **NEW** at the top left of the portal interface.</span></span>

1. <span data-ttu-id="c1eda-137">Vyberte **Web + mobilní zařízení** > **webová aplikace**.</span><span class="sxs-lookup"><span data-stu-id="c1eda-137">Select **Web + Mobile** > **Web App**.</span></span>

   ![Portál Microsoft Azure: Tlačítko Nová: Web + mobilní zařízení v části Marketplace: Tlačítko webové aplikace v rámci vybrané aplikace](azure-continuous-deployment/_static/05-azure-newwebapp.png)

1. <span data-ttu-id="c1eda-139">V **webovou aplikaci** okně zadejte jedinečnou hodnotu **název služby App Service**.</span><span class="sxs-lookup"><span data-stu-id="c1eda-139">In the **Web App** blade, enter a unique value for the **App Service Name**.</span></span>

   ![Okně webové aplikace](azure-continuous-deployment/_static/06-azure-newappblade.png)

   > [!NOTE]
   > <span data-ttu-id="c1eda-141">**Název služby App Service** název musí být jedinečný.</span><span class="sxs-lookup"><span data-stu-id="c1eda-141">The **App Service Name** name must be unique.</span></span> <span data-ttu-id="c1eda-142">Na portálu vynutí toto pravidlo, pokud je zadaný název.</span><span class="sxs-lookup"><span data-stu-id="c1eda-142">The portal enforces this rule when the name is provided.</span></span> <span data-ttu-id="c1eda-143">Pokud zadáte jiné hodnoty, nahraďte tuto hodnotu pro každý výskyt **SampleWebAppDemo** v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="c1eda-143">If providing a different value, substitute that value for each occurrence of **SampleWebAppDemo** in this tutorial.</span></span>

   <span data-ttu-id="c1eda-144">Také v **webovou aplikaci** okno, vyberte existující **plán App Service/umístění** nebo vytvořte novou.</span><span class="sxs-lookup"><span data-stu-id="c1eda-144">Also in the **Web App** blade, select an existing **App Service Plan/Location** or create a new one.</span></span> <span data-ttu-id="c1eda-145">Pokud vytváříte nový plán, vyberte cenovou úroveň, umístění a další možnosti.</span><span class="sxs-lookup"><span data-stu-id="c1eda-145">If creating a new plan, select the pricing tier, location, and other options.</span></span> <span data-ttu-id="c1eda-146">Další informace o plánech služby App Service najdete v tématu [podrobný přehled plánů služby Azure App Service](/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview).</span><span class="sxs-lookup"><span data-stu-id="c1eda-146">For more information on App Service plans, see [Azure App Service plans in-depth overview](/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview).</span></span>

1. <span data-ttu-id="c1eda-147">Vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="c1eda-147">Select **Create**.</span></span> <span data-ttu-id="c1eda-148">Azure bude zřídit a spustit webovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="c1eda-148">Azure will provision and start the web app.</span></span>

   ![Azure Portal: Okno ukázkové webové aplikace ukázku 01 Essentials](azure-continuous-deployment/_static/07-azure-webappblade.png)

## <a name="enable-git-publishing-for-the-new-web-app"></a><span data-ttu-id="c1eda-150">Povolení publikování Git pro novou webovou aplikaci</span><span class="sxs-lookup"><span data-stu-id="c1eda-150">Enable Git publishing for the new web app</span></span>

<span data-ttu-id="c1eda-151">Git je distribuovaný systém správy verzí, který slouží k nasazení webové aplikace v Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="c1eda-151">Git is a distributed version control system that can be used to deploy an Azure App Service web app.</span></span> <span data-ttu-id="c1eda-152">Kód webové aplikace je uložena v místním úložišti Git a kód se nasazuje do Azure s doručením (push) do vzdáleného úložiště.</span><span class="sxs-lookup"><span data-stu-id="c1eda-152">Web app code is stored in a local Git repository, and the code is deployed to Azure by pushing to a remote repository.</span></span>

1. <span data-ttu-id="c1eda-153">Přihlaste se [webu Azure Portal](https://portal.azure.com).</span><span class="sxs-lookup"><span data-stu-id="c1eda-153">Log into the [Azure Portal](https://portal.azure.com).</span></span>

1. <span data-ttu-id="c1eda-154">Vyberte **App Services** zobrazíte seznam služeb aplikací přidružených k předplatnému Azure.</span><span class="sxs-lookup"><span data-stu-id="c1eda-154">Select **App Services** to view a list of the app services associated with the Azure subscription.</span></span>

1. <span data-ttu-id="c1eda-155">Vyberte webovou aplikaci vytvořenou v předchozí části tohoto kurzu.</span><span class="sxs-lookup"><span data-stu-id="c1eda-155">Select the web app created in the previous section of this tutorial.</span></span>

1. <span data-ttu-id="c1eda-156">V **nasazení** okně vyberte **možnosti nasazení** > **zvolit zdroj** > **místní úložiště Git**.</span><span class="sxs-lookup"><span data-stu-id="c1eda-156">In the **Deployment** blade, select **Deployment options** > **Choose Source** > **Local Git Repository**.</span></span>

   ![Okno nastavení: Okno zdroje nasazení: Vyberte okno zdroje](azure-continuous-deployment/_static/deployment-options.png)

1. <span data-ttu-id="c1eda-158">Vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="c1eda-158">Select **OK**.</span></span>

1. <span data-ttu-id="c1eda-159">Pokud přihlašovací údaje nasazení pro publikování webové aplikace nebo jiné aplikace služby App Service ještě dříve nastavený, nastavte je nyní:</span><span class="sxs-lookup"><span data-stu-id="c1eda-159">If deployment credentials for publishing a web app or other App Service app haven't previously been set up, set them up now:</span></span>

   * <span data-ttu-id="c1eda-160">Vyberte **nastavení** > **přihlašovací údaje pro nasazení**.</span><span class="sxs-lookup"><span data-stu-id="c1eda-160">Select **Settings** > **Deployment credentials**.</span></span> <span data-ttu-id="c1eda-161">**Přihlašovací údaje pro nasazení** zobrazí se okno.</span><span class="sxs-lookup"><span data-stu-id="c1eda-161">The **Set deployment credentials** blade is displayed.</span></span>
   * <span data-ttu-id="c1eda-162">Vytvořte uživatelské jméno a heslo.</span><span class="sxs-lookup"><span data-stu-id="c1eda-162">Create a user name and password.</span></span> <span data-ttu-id="c1eda-163">Uložte heslo pro pozdější použití. při nastavování Git.</span><span class="sxs-lookup"><span data-stu-id="c1eda-163">Save the password for later use when setting up Git.</span></span>
   * <span data-ttu-id="c1eda-164">Vyberte **Uložit**.</span><span class="sxs-lookup"><span data-stu-id="c1eda-164">Select **Save**.</span></span>

1. <span data-ttu-id="c1eda-165">V **webovou aplikaci** okně vyberte **nastavení** > **vlastnosti**.</span><span class="sxs-lookup"><span data-stu-id="c1eda-165">In the **Web App** blade, select **Settings** > **Properties**.</span></span> <span data-ttu-id="c1eda-166">Adresa URL vzdáleného úložiště Git pro nasazení do je zobrazen pod **adresa URL pro GIT**.</span><span class="sxs-lookup"><span data-stu-id="c1eda-166">The URL of the remote Git repository to deploy to is shown under **GIT URL**.</span></span>

1. <span data-ttu-id="c1eda-167">Kopírovat **adresa URL pro GIT** hodnoty pro pozdější použití v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="c1eda-167">Copy the **GIT URL** value for later use in the tutorial.</span></span>

   ![Webu Azure Portal: okno Vlastnosti aplikací](azure-continuous-deployment/_static/09-azure-giturl.png)

## <a name="publish-the-web-app-to-azure-app-service"></a><span data-ttu-id="c1eda-169">Publikování webové aplikace do služby Azure App Service</span><span class="sxs-lookup"><span data-stu-id="c1eda-169">Publish the web app to Azure App Service</span></span>

<span data-ttu-id="c1eda-170">V této části se vytvořte místní úložiště Git pomocí sady Visual Studio a nabízených oznámení z tohoto úložiště do Azure k nasazení webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="c1eda-170">In this section, create a local Git repository using Visual Studio and push from that repository to Azure to deploy the web app.</span></span> <span data-ttu-id="c1eda-171">Následující kroky:</span><span class="sxs-lookup"><span data-stu-id="c1eda-171">The steps involved include the following:</span></span>

* <span data-ttu-id="c1eda-172">Přidáte nastavení vzdáleného úložiště pomocí adresy URL pro GIT hodnoty, tak v místním úložišti je možné nasadit do Azure.</span><span class="sxs-lookup"><span data-stu-id="c1eda-172">Add the remote repository setting using the GIT URL value, so the local repository can be deployed to Azure.</span></span>
* <span data-ttu-id="c1eda-173">Potvrďte změny v projektu.</span><span class="sxs-lookup"><span data-stu-id="c1eda-173">Commit project changes.</span></span>
* <span data-ttu-id="c1eda-174">Odešlete změny projektu z místního úložiště do vzdáleného úložiště v Azure.</span><span class="sxs-lookup"><span data-stu-id="c1eda-174">Push project changes from the local repository to the remote repository on Azure.</span></span>

1. <span data-ttu-id="c1eda-175">V **Průzkumníka řešení** klikněte pravým tlačítkem na **řešení "SampleWebAppDemo"** a vyberte **potvrzení**.</span><span class="sxs-lookup"><span data-stu-id="c1eda-175">In **Solution Explorer** right-click **Solution 'SampleWebAppDemo'** and select **Commit**.</span></span> <span data-ttu-id="c1eda-176">**Team Exploreru** se zobrazí.</span><span class="sxs-lookup"><span data-stu-id="c1eda-176">The **Team Explorer** is displayed.</span></span>

   ![Team Explorer Connect kartu](azure-continuous-deployment/_static/10-team-explorer.png)

1. <span data-ttu-id="c1eda-178">V **Team Exploreru**, vyberte **domácí** (Ikona Domovská stránka) > **nastavení** > **nastavení úložiště**.</span><span class="sxs-lookup"><span data-stu-id="c1eda-178">In **Team Explorer**, select the **Home** (home icon) > **Settings** > **Repository Settings**.</span></span>

1. <span data-ttu-id="c1eda-179">V **vzdálených úložišť** část **nastavení úložiště**vyberte **přidat**.</span><span class="sxs-lookup"><span data-stu-id="c1eda-179">In the **Remotes** section of the **Repository Settings**, select **Add**.</span></span> <span data-ttu-id="c1eda-180">**Přidat vzdálené úložiště** se zobrazí dialogové okno.</span><span class="sxs-lookup"><span data-stu-id="c1eda-180">The **Add Remote** dialog box is displayed.</span></span>

1. <span data-ttu-id="c1eda-181">Nastavte **název** vzdáleného k **Azure – ukázková aplikace**.</span><span class="sxs-lookup"><span data-stu-id="c1eda-181">Set the **Name** of the remote to **Azure-SampleApp**.</span></span>

1. <span data-ttu-id="c1eda-182">Nastavit hodnotu pro **načíst** k **adresa URL pro Git** , který se zkopíruje z Azure dříve v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="c1eda-182">Set the value for **Fetch** to the **Git URL** that copied from Azure earlier in this tutorial.</span></span> <span data-ttu-id="c1eda-183">Všimněte si, že se jedná o adresu URL, která končí **.git**.</span><span class="sxs-lookup"><span data-stu-id="c1eda-183">Note that this is the URL that ends with **.git**.</span></span>

   ![Upravit vzdálené dialogového okna](azure-continuous-deployment/_static/11-add-remote.png)

   > [!NOTE]
   > <span data-ttu-id="c1eda-185">Jako alternativu, zadejte ze vzdáleného úložiště **příkazové okno** tak, že otevřete **příkazové okno**, změna k adresáři projektu a zadáním příkazu.</span><span class="sxs-lookup"><span data-stu-id="c1eda-185">As an alternative, specify the remote repository from the **Command Window** by opening the **Command Window**, changing to the project directory, and entering the command.</span></span> <span data-ttu-id="c1eda-186">Příklad:</span><span class="sxs-lookup"><span data-stu-id="c1eda-186">Example:</span></span>
   >
   > `git remote add Azure-SampleApp https://me@sampleapp.scm.azurewebsites.net:443/SampleApp.git`

1. <span data-ttu-id="c1eda-187">Vyberte **domácí** (Ikona Domovská stránka) > **nastavení** > **globální nastavení**.</span><span class="sxs-lookup"><span data-stu-id="c1eda-187">Select the **Home** (home icon) > **Settings** > **Global Settings**.</span></span> <span data-ttu-id="c1eda-188">Zkontrolujte, jestli jsou nastavené jméno a e-mailovou adresu.</span><span class="sxs-lookup"><span data-stu-id="c1eda-188">Confirm that the name and email address are set.</span></span> <span data-ttu-id="c1eda-189">Vyberte **aktualizace** podle potřeby.</span><span class="sxs-lookup"><span data-stu-id="c1eda-189">Select **Update** if required.</span></span>

1. <span data-ttu-id="c1eda-190">Vyberte **Domů** > **změny** se vrátíte **změny** zobrazení.</span><span class="sxs-lookup"><span data-stu-id="c1eda-190">Select **Home** > **Changes** to return to the **Changes** view.</span></span>

1. <span data-ttu-id="c1eda-191">Zadejte zprávu potvrzení změn, jako například **počáteční Push č. 1** a vyberte **potvrzení**.</span><span class="sxs-lookup"><span data-stu-id="c1eda-191">Enter a commit message, such as **Initial Push #1** and select **Commit**.</span></span> <span data-ttu-id="c1eda-192">Tato akce vytvoří *potvrzení* místně.</span><span class="sxs-lookup"><span data-stu-id="c1eda-192">This action creates a *commit* locally.</span></span>

   ![Team Explorer Connect kartu](azure-continuous-deployment/_static/12-initial-commit.png)

   > [!NOTE]
   > <span data-ttu-id="c1eda-194">Jako alternativu potvrzení změny z **příkazové okno** tak, že otevřete **příkazové okno**, změna k adresáři projektu a zadáte příkazy gitu.</span><span class="sxs-lookup"><span data-stu-id="c1eda-194">As an alternative, commit changes from the **Command Window** by opening the **Command Window**, changing to the project directory, and entering the git commands.</span></span> <span data-ttu-id="c1eda-195">Příklad:</span><span class="sxs-lookup"><span data-stu-id="c1eda-195">Example:</span></span>
   >
   > `git add .`
   >
   > `git commit -am "Initial Push #1"`

1. <span data-ttu-id="c1eda-196">Vyberte **Domů** > **synchronizace** > **akce** > **otevřete příkazový řádek**.</span><span class="sxs-lookup"><span data-stu-id="c1eda-196">Select **Home** > **Sync** > **Actions** > **Open Command Prompt**.</span></span> <span data-ttu-id="c1eda-197">Příkazový řádek se otevře adresář projektu.</span><span class="sxs-lookup"><span data-stu-id="c1eda-197">The command prompt opens to the project directory.</span></span>

1. <span data-ttu-id="c1eda-198">V příkazovém okně zadejte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="c1eda-198">Enter the following command in the command window:</span></span>

   `git push -u Azure-SampleApp master`

1. <span data-ttu-id="c1eda-199">Zadejte Azure **přihlašovací údaje pro nasazení** heslo, které vytvořili dříve v Azure.</span><span class="sxs-lookup"><span data-stu-id="c1eda-199">Enter the Azure **deployment credentials** password created earlier in Azure.</span></span>

   <span data-ttu-id="c1eda-200">Tento příkaz spustí proces odesílání souborů místní projekt do Azure.</span><span class="sxs-lookup"><span data-stu-id="c1eda-200">This command starts the process of pushing the local project files to Azure.</span></span> <span data-ttu-id="c1eda-201">Výstup z výše uvedeného příkazu ukončí a zobrazí se zpráva, že nasazení bylo úspěšné.</span><span class="sxs-lookup"><span data-stu-id="c1eda-201">The output from the above command ends with a message that the deployment was successful.</span></span>

   ```
   remote: Finished successfully.
   remote: Running post deployment command(s)...
   remote: Deployment successful.
   To https://username@samplewebappdemo01.scm.azurewebsites.net:443/SampleWebAppDemo01.git
   * [new branch]      master -> master
   Branch master set up to track remote branch master from Azure-SampleApp.
   ```

   > [!NOTE]
   > <span data-ttu-id="c1eda-202">Pokud se vyžaduje spolupráci na projekt, vezměte v úvahu doručením (push) do [Githubu](https://github.com) před doručením (push) do Azure.</span><span class="sxs-lookup"><span data-stu-id="c1eda-202">If collaboration on the project is required, consider pushing to [GitHub](https://github.com) before pushing to Azure.</span></span>
 
### <a name="verify-the-active-deployment"></a><span data-ttu-id="c1eda-203">Ověření aktivní nasazení</span><span class="sxs-lookup"><span data-stu-id="c1eda-203">Verify the Active Deployment</span></span>

<span data-ttu-id="c1eda-204">Ověřte, jestli přenos webové aplikace z místního prostředí do Azure se úspěšně dokončila.</span><span class="sxs-lookup"><span data-stu-id="c1eda-204">Verify that the web app transfer from the local environment to Azure is successful.</span></span>

<span data-ttu-id="c1eda-205">V [webu Azure Portal](https://portal.azure.com), vyberte webovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="c1eda-205">In the [Azure Portal](https://portal.azure.com), select the web app.</span></span> <span data-ttu-id="c1eda-206">Vyberte **nasazení** > **možnosti nasazení**.</span><span class="sxs-lookup"><span data-stu-id="c1eda-206">Select **Deployment** > **Deployment options**.</span></span>

![Azure Portal: Okno nastavení: Nasazení okno zobrazující úspěšné nasazení](azure-continuous-deployment/_static/13-verify-deployment.png)

## <a name="run-the-app-in-azure"></a><span data-ttu-id="c1eda-208">Spusťte aplikaci v Azure</span><span class="sxs-lookup"><span data-stu-id="c1eda-208">Run the app in Azure</span></span>

<span data-ttu-id="c1eda-209">Teď, když webová aplikace je nasazená do Azure, spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="c1eda-209">Now that the web app is deployed to Azure, run the app.</span></span>

<span data-ttu-id="c1eda-210">Můžete to provést dvěma způsoby:</span><span class="sxs-lookup"><span data-stu-id="c1eda-210">This can be accomplished in two ways:</span></span>

* <span data-ttu-id="c1eda-211">Na webu Azure Portal vyhledejte okně webové aplikace pro webovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="c1eda-211">In the Azure Portal, locate the web app blade for the web app.</span></span> <span data-ttu-id="c1eda-212">Vyberte **Procházet** a zobrazte aplikaci ve výchozím prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="c1eda-212">Select **Browse** to view the app in the default browser.</span></span>
* <span data-ttu-id="c1eda-213">Otevřete prohlížeč a zadejte adresu URL pro webovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="c1eda-213">Open a browser and enter the URL for the web app.</span></span> <span data-ttu-id="c1eda-214">Příklad: `http://SampleWebAppDemo.azurewebsites.net`</span><span class="sxs-lookup"><span data-stu-id="c1eda-214">Example: `http://SampleWebAppDemo.azurewebsites.net`</span></span>

## <a name="update-the-web-app-and-republish"></a><span data-ttu-id="c1eda-215">Aktualizace webové aplikace a opětovné publikování</span><span class="sxs-lookup"><span data-stu-id="c1eda-215">Update the web app and republish</span></span>

<span data-ttu-id="c1eda-216">Po provedení změn na místní kód, znovu publikujte:</span><span class="sxs-lookup"><span data-stu-id="c1eda-216">After making changes to the local code, republish:</span></span>

1. <span data-ttu-id="c1eda-217">V **Průzkumníka řešení** sady Visual Studio, otevřete *Startup.cs* souboru.</span><span class="sxs-lookup"><span data-stu-id="c1eda-217">In **Solution Explorer** of Visual Studio, open the *Startup.cs* file.</span></span>

1. <span data-ttu-id="c1eda-218">V `Configure` metody změnit `Response.WriteAsync` metodu tak, že se zobrazí takto:</span><span class="sxs-lookup"><span data-stu-id="c1eda-218">In the `Configure` method, modify the `Response.WriteAsync` method so that it appears as follows:</span></span>

   ```csharp
   await context.Response.WriteAsync("Hello World! Deploy to Azure.");
   ```

1. <span data-ttu-id="c1eda-219">Uložit změny do *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="c1eda-219">Save the changes to *Startup.cs*.</span></span>

1. <span data-ttu-id="c1eda-220">V **Průzkumníka řešení**, klikněte pravým tlačítkem na **řešení "SampleWebAppDemo"** a vyberte **potvrzení**.</span><span class="sxs-lookup"><span data-stu-id="c1eda-220">In **Solution Explorer**, right-click **Solution 'SampleWebAppDemo'** and select **Commit**.</span></span> <span data-ttu-id="c1eda-221">**Team Exploreru** se zobrazí.</span><span class="sxs-lookup"><span data-stu-id="c1eda-221">The **Team Explorer** is displayed.</span></span>

1. <span data-ttu-id="c1eda-222">Zadejte zprávu potvrzení změn, jako například `Update #2`.</span><span class="sxs-lookup"><span data-stu-id="c1eda-222">Enter a commit message, such as `Update #2`.</span></span>

1. <span data-ttu-id="c1eda-223">Stisknutím klávesy **potvrzení** tlačítko potvrďte změny projektu.</span><span class="sxs-lookup"><span data-stu-id="c1eda-223">Press the **Commit** button to commit the project changes.</span></span>

1. <span data-ttu-id="c1eda-224">Vyberte **Domů** > **synchronizace** > **akce** > **Push**.</span><span class="sxs-lookup"><span data-stu-id="c1eda-224">Select **Home** > **Sync** > **Actions** > **Push**.</span></span>

> [!NOTE]
> <span data-ttu-id="c1eda-225">Jako alternativu nasdílení změn z **příkazové okno** tak, že otevřete **příkazové okno**, změna k adresáři projektu a zadáním příkazu gitu.</span><span class="sxs-lookup"><span data-stu-id="c1eda-225">As an alternative, push the changes from the **Command Window** by opening the **Command Window**, changing to the project directory, and entering a git command.</span></span> <span data-ttu-id="c1eda-226">Příklad:</span><span class="sxs-lookup"><span data-stu-id="c1eda-226">Example:</span></span>
> 
> `git push -u Azure-SampleApp master`

## <a name="view-the-updated-web-app-in-azure"></a><span data-ttu-id="c1eda-227">Zobrazení aktualizované webové aplikace v Azure</span><span class="sxs-lookup"><span data-stu-id="c1eda-227">View the updated web app in Azure</span></span>

<span data-ttu-id="c1eda-228">Zobrazení aktualizované webové aplikace tak, že vyberete **Procházet** v okně webové aplikace na webu Azure Portal nebo otevřete prohlížeč a zadat adresu URL pro webovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="c1eda-228">View the updated web app by selecting **Browse** from the web app blade in the Azure Portal or by opening a browser and entering the URL for the web app.</span></span> <span data-ttu-id="c1eda-229">Příklad: `http://SampleWebAppDemo.azurewebsites.net`</span><span class="sxs-lookup"><span data-stu-id="c1eda-229">Example: `http://SampleWebAppDemo.azurewebsites.net`</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c1eda-230">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="c1eda-230">Additional resources</span></span>

* [<span data-ttu-id="c1eda-231">Vytvořit svůj první kanál s kanály Azure</span><span class="sxs-lookup"><span data-stu-id="c1eda-231">Create your first pipeline with Azure Pipelines</span></span>](/azure/devops/pipelines/get-started-yaml)
* [<span data-ttu-id="c1eda-232">Projekt Kudu</span><span class="sxs-lookup"><span data-stu-id="c1eda-232">Project Kudu</span></span>](https://github.com/projectkudu/kudu/wiki)
* <xref:host-and-deploy/visual-studio-publish-profiles>
