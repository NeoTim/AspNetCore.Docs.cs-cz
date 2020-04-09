---
title: Průběžné nasazování do Azure pomocí sady Visual Studio a Gitu s ASP.NET Core
author: rick-anderson
description: Zjistěte, jak pomocí Sady Visual Studio vytvořit ASP.NET webovou aplikaci Core a nasadit ji do služby Azure App Service pomocí Gitu pro průběžné nasazení.
ms.author: riande
ms.custom: mvc
ms.date: 12/06/2018
uid: host-and-deploy/azure-apps/azure-continuous-deployment
ms.openlocfilehash: 3b344505739bb4292ed1683c73ff314b6e4e01e9
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78660851"
---
# <a name="continuous-deployment-to-azure-with-visual-studio-and-git-with-aspnet-core"></a><span data-ttu-id="6a851-103">Průběžné nasazování do Azure pomocí sady Visual Studio a Gitu s ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6a851-103">Continuous deployment to Azure with Visual Studio and Git with ASP.NET Core</span></span>

<span data-ttu-id="6a851-104">Podle [Erik Reitan](https://github.com/Erikre)</span><span class="sxs-lookup"><span data-stu-id="6a851-104">By [Erik Reitan](https://github.com/Erikre)</span></span>

[!INCLUDE [Azure App Service Preview Notice](../../includes/azure-apps-preview-notice.md)]

<span data-ttu-id="6a851-105">Tento kurz ukazuje, jak vytvořit ASP.NET webovou aplikaci Core pomocí Visual Studia a nasadit ji z Visual Studia do služby Azure App Service pomocí průběžného nasazení.</span><span class="sxs-lookup"><span data-stu-id="6a851-105">This tutorial shows how to create an ASP.NET Core web app using Visual Studio and deploy it from Visual Studio to Azure App Service using continuous deployment.</span></span>

<span data-ttu-id="6a851-106">Viz taky [Vytvoření prvního kanálu pomocí Azure Pipelines](/azure/devops/pipelines/get-started-yaml), který ukazuje, jak nakonfigurovat pracovní postup průběžného doručování (CD) pro [službu Azure App Service](/azure/app-service/app-service-web-overview) pomocí služby Azure DevOps.</span><span class="sxs-lookup"><span data-stu-id="6a851-106">See also [Create your first pipeline with Azure Pipelines](/azure/devops/pipelines/get-started-yaml), which shows how to configure a continuous delivery (CD) workflow for [Azure App Service](/azure/app-service/app-service-web-overview) using Azure DevOps Services.</span></span> <span data-ttu-id="6a851-107">Azure Pipelines (služba Azure DevOps Services) zjednodušuje nastavení kanálu robustního nasazení pro publikování aktualizací pro aplikace hostované ve službě Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="6a851-107">Azure Pipelines (an Azure DevOps Services service) simplifies setting up a robust deployment pipeline to publish updates for apps hosted in Azure App Service.</span></span> <span data-ttu-id="6a851-108">Kanál lze nakonfigurovat z portálu Azure k sestavení, spuštění testů, nasazení do pracovního slotu a následnénasazení do produkčního prostředí.</span><span class="sxs-lookup"><span data-stu-id="6a851-108">The pipeline can be configured from the Azure portal to build, run tests, deploy to a staging slot, and then deploy to production.</span></span>

> [!NOTE]
> <span data-ttu-id="6a851-109">K dokončení tohoto kurzu je vyžadován účet Microsoft Azure.</span><span class="sxs-lookup"><span data-stu-id="6a851-109">To complete this tutorial, a Microsoft Azure account is required.</span></span> <span data-ttu-id="6a851-110">Chcete-li získat účet, [aktivujte výhody pro předplatitele msdn](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A261C142F) nebo [se zaregistrujte k bezplatné zkušební verzi](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).</span><span class="sxs-lookup"><span data-stu-id="6a851-110">To obtain an account, [activate MSDN subscriber benefits](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A261C142F) or [sign up for a free trial](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="6a851-111">Požadavky</span><span class="sxs-lookup"><span data-stu-id="6a851-111">Prerequisites</span></span>

<span data-ttu-id="6a851-112">Tento kurz předpokládá, že je nainstalován následující software:</span><span class="sxs-lookup"><span data-stu-id="6a851-112">This tutorial assumes the following software is installed:</span></span>

* [<span data-ttu-id="6a851-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6a851-113">Visual Studio</span></span>](https://visualstudio.microsoft.com)
* [!INCLUDE [](~/includes/net-core-sdk-download-link.md)]
* <span data-ttu-id="6a851-114">[Git](https://git-scm.com/downloads) pro Windows</span><span class="sxs-lookup"><span data-stu-id="6a851-114">[Git](https://git-scm.com/downloads) for Windows</span></span>

## <a name="create-an-aspnet-core-web-app"></a><span data-ttu-id="6a851-115">Vytvoření webové aplikace ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6a851-115">Create an ASP.NET Core web app</span></span>

1. <span data-ttu-id="6a851-116">Spusťte Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="6a851-116">Start Visual Studio.</span></span>

1. <span data-ttu-id="6a851-117">V nabídce **Soubor** vyberte **Nový** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="6a851-117">From the **File** menu, select **New** > **Project**.</span></span>

1. <span data-ttu-id="6a851-118">Vyberte šablonu projektu **základní webové aplikace ASP.NET.**</span><span class="sxs-lookup"><span data-stu-id="6a851-118">Select the **ASP.NET Core Web Application** project template.</span></span> <span data-ttu-id="6a851-119">Zobrazí se v části **Nainstalované** > **šablony** > **Visual C#** > **.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="6a851-119">It appears under **Installed** > **Templates** > **Visual C#** > **.NET Core**.</span></span> <span data-ttu-id="6a851-120">Pojmenujte `SampleWebAppDemo`projekt .</span><span class="sxs-lookup"><span data-stu-id="6a851-120">Name the project `SampleWebAppDemo`.</span></span> <span data-ttu-id="6a851-121">Vyberte možnost **Vytvořit nové úložiště Git** a klepněte na **OK**.</span><span class="sxs-lookup"><span data-stu-id="6a851-121">Select the **Create new Git repository** option and click **OK**.</span></span>

   ![Dialogové okno Nový projekt](azure-continuous-deployment/_static/01-new-project.png)

1. <span data-ttu-id="6a851-123">V dialogovém **okně Nový ASP.NET základní projekt** vyberte šablonu ASP.NET Jádro **prázdné** a klepněte na tlačítko **OK**.</span><span class="sxs-lookup"><span data-stu-id="6a851-123">In the **New ASP.NET Core Project** dialog, select the ASP.NET Core **Empty** template, then click **OK**.</span></span>

   ![Dialogové okno Nový ASP.NET základní projekt](azure-continuous-deployment/_static/02-web-site-template.png)

> [!NOTE]
> <span data-ttu-id="6a851-125">Nejnovější verze rozhraní .NET Core je 2.0.</span><span class="sxs-lookup"><span data-stu-id="6a851-125">The most recent release of .NET Core is 2.0.</span></span>

### <a name="running-the-web-app-locally"></a><span data-ttu-id="6a851-126">Místní spuštění webové aplikace</span><span class="sxs-lookup"><span data-stu-id="6a851-126">Running the web app locally</span></span>

1. <span data-ttu-id="6a851-127">Po dokončení vytvoření aplikace visual studio, spusťte aplikaci výběrem **ladění začít** > **ladění**.</span><span class="sxs-lookup"><span data-stu-id="6a851-127">Once Visual Studio finishes creating the app, run the app by selecting **Debug** > **Start Debugging**.</span></span> <span data-ttu-id="6a851-128">Alternativně stiskněte **klávesu F5**.</span><span class="sxs-lookup"><span data-stu-id="6a851-128">As an alternative, press **F5**.</span></span>

   <span data-ttu-id="6a851-129">Inicializaci sady Visual Studio a nové aplikace může nějakou dobu trvat.</span><span class="sxs-lookup"><span data-stu-id="6a851-129">It may take time to initialize Visual Studio and the new app.</span></span> <span data-ttu-id="6a851-130">Po dokončení prohlížeč zobrazí spuštěnou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="6a851-130">Once it's complete, the browser shows the running app.</span></span>

   ![Okno prohlížeče zobrazující spuštěnou aplikaci, která zobrazuje "Hello World!".](azure-continuous-deployment/_static/04-browser-runapp.png)

1. <span data-ttu-id="6a851-132">Po kontrole spuštěné webové aplikace zavřete prohlížeč a vyberte ikonu Zastavit ladění na panelu nástrojů sady Visual Studio, chcete-li aplikaci zastavit.</span><span class="sxs-lookup"><span data-stu-id="6a851-132">After reviewing the running Web app, close the browser and select the "Stop Debugging" icon in the toolbar of Visual Studio to stop the app.</span></span>

## <a name="create-a-web-app-in-the-azure-portal"></a><span data-ttu-id="6a851-133">Vytvoření webové aplikace na Webu Azure Portal</span><span class="sxs-lookup"><span data-stu-id="6a851-133">Create a web app in the Azure Portal</span></span>

<span data-ttu-id="6a851-134">Následující kroky vytvoří webovou aplikaci na webu Azure Portal:</span><span class="sxs-lookup"><span data-stu-id="6a851-134">The following steps create a web app in the Azure Portal:</span></span>

1. <span data-ttu-id="6a851-135">Přihlaste se k [portálu Azure Portal](https://portal.azure.com).</span><span class="sxs-lookup"><span data-stu-id="6a851-135">Log in to the [Azure Portal](https://portal.azure.com).</span></span>

1. <span data-ttu-id="6a851-136">V levém horním rohu rozhraní portálu vyberte **NOVÝ.**</span><span class="sxs-lookup"><span data-stu-id="6a851-136">Select **NEW** at the top left of the portal interface.</span></span>

1. <span data-ttu-id="6a851-137">Vyberte **možnost Web + Mobilní** > **webová aplikace**.</span><span class="sxs-lookup"><span data-stu-id="6a851-137">Select **Web + Mobile** > **Web App**.</span></span>

   ![Portál Microsoft Azure: Nové tlačítko: Web + Mobilní pod marketplace: Tlačítko Web App v části Doporučené aplikace](azure-continuous-deployment/_static/05-azure-newwebapp.png)

1. <span data-ttu-id="6a851-139">V okně **Web App** zadejte jedinečnou hodnotu pro **název služby App Service**.</span><span class="sxs-lookup"><span data-stu-id="6a851-139">In the **Web App** blade, enter a unique value for the **App Service Name**.</span></span>

   ![Okno webové aplikace](azure-continuous-deployment/_static/06-azure-newappblade.png)

   > [!NOTE]
   > <span data-ttu-id="6a851-141">Název **služby App Service** musí být jedinečný.</span><span class="sxs-lookup"><span data-stu-id="6a851-141">The **App Service Name** name must be unique.</span></span> <span data-ttu-id="6a851-142">Portál vynucuje toto pravidlo, když je uveden název.</span><span class="sxs-lookup"><span data-stu-id="6a851-142">The portal enforces this rule when the name is provided.</span></span> <span data-ttu-id="6a851-143">Pokud poskytuje jinou hodnotu, nahradit tuto hodnotu pro každý výskyt **SampleWebAppDemo** v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="6a851-143">If providing a different value, substitute that value for each occurrence of **SampleWebAppDemo** in this tutorial.</span></span>

   <span data-ttu-id="6a851-144">Také v okně **Web App** vyberte existující **plán/umístění služby App Service** nebo vytvořte nový.</span><span class="sxs-lookup"><span data-stu-id="6a851-144">Also in the **Web App** blade, select an existing **App Service Plan/Location** or create a new one.</span></span> <span data-ttu-id="6a851-145">Pokud vytváříte nový plán, vyberte cenovou úroveň, umístění a další možnosti.</span><span class="sxs-lookup"><span data-stu-id="6a851-145">If creating a new plan, select the pricing tier, location, and other options.</span></span> <span data-ttu-id="6a851-146">Další informace o plánech služby App Service najdete v článku [plány azure app service podrobný přehled](/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview).</span><span class="sxs-lookup"><span data-stu-id="6a851-146">For more information on App Service plans, see [Azure App Service plans in-depth overview](/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview).</span></span>

1. <span data-ttu-id="6a851-147">Vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="6a851-147">Select **Create**.</span></span> <span data-ttu-id="6a851-148">Azure zřídí a spustí webovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="6a851-148">Azure will provision and start the web app.</span></span>

   ![Portál Azure: Ukázková ukázková ukázková ukázková ukázka ukázkového okna 01 Essentials](azure-continuous-deployment/_static/07-azure-webappblade.png)

## <a name="enable-git-publishing-for-the-new-web-app"></a><span data-ttu-id="6a851-150">Povolení publikování Gitu pro novou webovou aplikaci</span><span class="sxs-lookup"><span data-stu-id="6a851-150">Enable Git publishing for the new web app</span></span>

<span data-ttu-id="6a851-151">Git je distribuovaný systém správy verzí, který se dá použít k nasazení webové aplikace Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="6a851-151">Git is a distributed version control system that can be used to deploy an Azure App Service web app.</span></span> <span data-ttu-id="6a851-152">Kód webové aplikace se ukládá v místním úložišti Git a kód se nasadí do Azure odesláním do vzdáleného úložiště.</span><span class="sxs-lookup"><span data-stu-id="6a851-152">Web app code is stored in a local Git repository, and the code is deployed to Azure by pushing to a remote repository.</span></span>

1. <span data-ttu-id="6a851-153">Přihlaste se k [portálu Azure Portal](https://portal.azure.com).</span><span class="sxs-lookup"><span data-stu-id="6a851-153">Log into the [Azure Portal](https://portal.azure.com).</span></span>

1. <span data-ttu-id="6a851-154">Výběrem **možnosti Služby aplikací** zobrazíte seznam aplikačních služeb přidružených k předplatnému Azure.</span><span class="sxs-lookup"><span data-stu-id="6a851-154">Select **App Services** to view a list of the app services associated with the Azure subscription.</span></span>

1. <span data-ttu-id="6a851-155">Vyberte webovou aplikaci vytvořenou v předchozí části tohoto kurzu.</span><span class="sxs-lookup"><span data-stu-id="6a851-155">Select the web app created in the previous section of this tutorial.</span></span>

1. <span data-ttu-id="6a851-156">V okně **Nasazení** vyberte **možnosti** > nasazení**Zvolit zdrojové** > **místní úložiště Git**.</span><span class="sxs-lookup"><span data-stu-id="6a851-156">In the **Deployment** blade, select **Deployment options** > **Choose Source** > **Local Git Repository**.</span></span>

   ![Nastavení čepele: Okno zdroje nasazení: Zvolte zdrojový nůž](azure-continuous-deployment/_static/deployment-options.png)

1. <span data-ttu-id="6a851-158">Vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="6a851-158">Select **OK**.</span></span>

1. <span data-ttu-id="6a851-159">Pokud přihlašovací údaje pro nasazení pro publikování webové aplikace nebo jiné aplikace Služby App Service ještě nebyly nastaveny, nastavte je teď:</span><span class="sxs-lookup"><span data-stu-id="6a851-159">If deployment credentials for publishing a web app or other App Service app haven't previously been set up, set them up now:</span></span>

   * <span data-ttu-id="6a851-160">Vyberte **nastavení** > **přihlašovacích údajů pro nasazení**.</span><span class="sxs-lookup"><span data-stu-id="6a851-160">Select **Settings** > **Deployment credentials**.</span></span> <span data-ttu-id="6a851-161">Zobrazí se okno **Nastavit přihlašovací údaje pro nasazení.**</span><span class="sxs-lookup"><span data-stu-id="6a851-161">The **Set deployment credentials** blade is displayed.</span></span>
   * <span data-ttu-id="6a851-162">Vytvořte uživatelské jméno a heslo.</span><span class="sxs-lookup"><span data-stu-id="6a851-162">Create a user name and password.</span></span> <span data-ttu-id="6a851-163">Heslo si uložte pro pozdější použití při nastavování Gitu.</span><span class="sxs-lookup"><span data-stu-id="6a851-163">Save the password for later use when setting up Git.</span></span>
   * <span data-ttu-id="6a851-164">Vyberte **Uložit**.</span><span class="sxs-lookup"><span data-stu-id="6a851-164">Select **Save**.</span></span>

1. <span data-ttu-id="6a851-165">V okně **Web Appu** vyberte **Vlastnosti** > **nastavení**.</span><span class="sxs-lookup"><span data-stu-id="6a851-165">In the **Web App** blade, select **Settings** > **Properties**.</span></span> <span data-ttu-id="6a851-166">Adresa URL vzdáleného úložiště Git, do které se má nasadit, se zobrazí pod **adresou GIT URL**.</span><span class="sxs-lookup"><span data-stu-id="6a851-166">The URL of the remote Git repository to deploy to is shown under **GIT URL**.</span></span>

1. <span data-ttu-id="6a851-167">Zkopírujte hodnotu **adresy URL GIT** pro pozdější použití v kurzu.</span><span class="sxs-lookup"><span data-stu-id="6a851-167">Copy the **GIT URL** value for later use in the tutorial.</span></span>

   ![Portál Azure: okno Vlastnosti aplikace](azure-continuous-deployment/_static/09-azure-giturl.png)

## <a name="publish-the-web-app-to-azure-app-service"></a><span data-ttu-id="6a851-169">Publikování webové aplikace do služby Azure App Service</span><span class="sxs-lookup"><span data-stu-id="6a851-169">Publish the web app to Azure App Service</span></span>

<span data-ttu-id="6a851-170">V této části vytvořte místní úložiště Git pomocí Visual Studia a přemisťujte z tohoto úložiště do Azure k nasazení webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="6a851-170">In this section, create a local Git repository using Visual Studio and push from that repository to Azure to deploy the web app.</span></span> <span data-ttu-id="6a851-171">K následujícím krokům patří:</span><span class="sxs-lookup"><span data-stu-id="6a851-171">The steps involved include the following:</span></span>

* <span data-ttu-id="6a851-172">Přidejte nastavení vzdáleného úložiště pomocí hodnoty ADRESY URL GIT, aby bylo možné místní úložiště nasadit do Azure.</span><span class="sxs-lookup"><span data-stu-id="6a851-172">Add the remote repository setting using the GIT URL value, so the local repository can be deployed to Azure.</span></span>
* <span data-ttu-id="6a851-173">Potvrzení změn projektu.</span><span class="sxs-lookup"><span data-stu-id="6a851-173">Commit project changes.</span></span>
* <span data-ttu-id="6a851-174">Nabízení změn projektu z místního úložiště do vzdáleného úložiště v Azure.</span><span class="sxs-lookup"><span data-stu-id="6a851-174">Push project changes from the local repository to the remote repository on Azure.</span></span>

1. <span data-ttu-id="6a851-175">V **Průzkumníku řešení** klepněte pravým tlačítkem myši na **položku Řešení SampleWebAppDemo** a vyberte **příkaz Potvrdit**.</span><span class="sxs-lookup"><span data-stu-id="6a851-175">In **Solution Explorer** right-click **Solution 'SampleWebAppDemo'** and select **Commit**.</span></span> <span data-ttu-id="6a851-176">Zobrazí se **Team Explorer**.</span><span class="sxs-lookup"><span data-stu-id="6a851-176">The **Team Explorer** is displayed.</span></span>

   ![Karta Připojení Průzkumníka týmu](azure-continuous-deployment/_static/10-team-explorer.png)

1. <span data-ttu-id="6a851-178">V **Průzkumníkovi týmu**vyberte **domovskou** (domovskou ikonu) >**nastavení úložiště** **nastavení** > .</span><span class="sxs-lookup"><span data-stu-id="6a851-178">In **Team Explorer**, select the **Home** (home icon) > **Settings** > **Repository Settings**.</span></span>

1. <span data-ttu-id="6a851-179">V části **Vzdálená úložiště** vyberte v **Nastavení úložiště\*\*\*\*Přidat**.</span><span class="sxs-lookup"><span data-stu-id="6a851-179">In the **Remotes** section of the **Repository Settings**, select **Add**.</span></span> <span data-ttu-id="6a851-180">Zobrazí se dialogové okno **Přidat vzdálené úložiště**.</span><span class="sxs-lookup"><span data-stu-id="6a851-180">The **Add Remote** dialog box is displayed.</span></span>

1. <span data-ttu-id="6a851-181">Nastavte **název** vzdáleného na **Azure-SampleApp**.</span><span class="sxs-lookup"><span data-stu-id="6a851-181">Set the **Name** of the remote to **Azure-SampleApp**.</span></span>

1. <span data-ttu-id="6a851-182">Nastavte hodnotu pro **načtení** na **adresu URL Git,** která se zkopírovala z Azure dříve v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="6a851-182">Set the value for **Fetch** to the **Git URL** that copied from Azure earlier in this tutorial.</span></span> <span data-ttu-id="6a851-183">Všimněte si, že se jedná o adresu URL, která končí **.git**.</span><span class="sxs-lookup"><span data-stu-id="6a851-183">Note that this is the URL that ends with **.git**.</span></span>

   ![Dialogové okno Upravit vzdálený](azure-continuous-deployment/_static/11-add-remote.png)

   > [!NOTE]
   > <span data-ttu-id="6a851-185">Jako alternativu určete vzdálené úložiště z **příkazového okna** otevřením **příkazového okna**, změnou adresáře projektu a zadáním příkazu.</span><span class="sxs-lookup"><span data-stu-id="6a851-185">As an alternative, specify the remote repository from the **Command Window** by opening the **Command Window**, changing to the project directory, and entering the command.</span></span> <span data-ttu-id="6a851-186">Příklad:</span><span class="sxs-lookup"><span data-stu-id="6a851-186">Example:</span></span>
   >
   > `git remote add Azure-SampleApp https://me@sampleapp.scm.azurewebsites.net:443/SampleApp.git`

1. <span data-ttu-id="6a851-187">Vyberte globální nastavení nastavení **Settings** >  **>** domovské (domovské)**.**</span><span class="sxs-lookup"><span data-stu-id="6a851-187">Select the **Home** (home icon) > **Settings** > **Global Settings**.</span></span> <span data-ttu-id="6a851-188">Zkontrolujte, jestli je zadané jméno a e-mailová adresa.</span><span class="sxs-lookup"><span data-stu-id="6a851-188">Confirm that the name and email address are set.</span></span> <span data-ttu-id="6a851-189">V případě potřeby vyberte **Aktualizovat.**</span><span class="sxs-lookup"><span data-stu-id="6a851-189">Select **Update** if required.</span></span>

1. <span data-ttu-id="6a851-190">Vyberte **Domácí** > **změny,** chcete-li se vrátit do zobrazení **Změny.**</span><span class="sxs-lookup"><span data-stu-id="6a851-190">Select **Home** > **Changes** to return to the **Changes** view.</span></span>

1. <span data-ttu-id="6a851-191">Zadejte zprávu o potvrzení, například **Počáteční nabízenou #1,** a vyberte **Potvrdit**.</span><span class="sxs-lookup"><span data-stu-id="6a851-191">Enter a commit message, such as **Initial Push #1** and select **Commit**.</span></span> <span data-ttu-id="6a851-192">Tato akce vytvoří *potvrzení* místně.</span><span class="sxs-lookup"><span data-stu-id="6a851-192">This action creates a *commit* locally.</span></span>

   ![Karta Připojení Průzkumníka týmu](azure-continuous-deployment/_static/12-initial-commit.png)

   > [!NOTE]
   > <span data-ttu-id="6a851-194">Alternativně potvrďte změny z **příkazového okna** otevřením **příkazového okna**, změnou adresáře projektu a zadáním příkazů git.</span><span class="sxs-lookup"><span data-stu-id="6a851-194">As an alternative, commit changes from the **Command Window** by opening the **Command Window**, changing to the project directory, and entering the git commands.</span></span> <span data-ttu-id="6a851-195">Příklad:</span><span class="sxs-lookup"><span data-stu-id="6a851-195">Example:</span></span>
   >
   > `git add .`
   >
   > `git commit -am "Initial Push #1"`

1. <span data-ttu-id="6a851-196">Vyberte **položku Akce** > **domácí synchronizace** > **Actions** > **Otevřete příkazový řádek**.</span><span class="sxs-lookup"><span data-stu-id="6a851-196">Select **Home** > **Sync** > **Actions** > **Open Command Prompt**.</span></span> <span data-ttu-id="6a851-197">Příkazový řádek se otevře do adresáře projektu.</span><span class="sxs-lookup"><span data-stu-id="6a851-197">The command prompt opens to the project directory.</span></span>

1. <span data-ttu-id="6a851-198">Do příkazového okna zadejte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="6a851-198">Enter the following command in the command window:</span></span>

   `git push -u Azure-SampleApp master`

1. <span data-ttu-id="6a851-199">Zadejte heslo **přihlašovacích údajů k nasazení** Azure vytvořené dříve v Azure.</span><span class="sxs-lookup"><span data-stu-id="6a851-199">Enter the Azure **deployment credentials** password created earlier in Azure.</span></span>

   <span data-ttu-id="6a851-200">Tento příkaz spustí proces odesílání souborů místního projektu do Azure.</span><span class="sxs-lookup"><span data-stu-id="6a851-200">This command starts the process of pushing the local project files to Azure.</span></span> <span data-ttu-id="6a851-201">Výstup z výše uvedeného příkazu končí zprávou, že nasazení bylo úspěšné.</span><span class="sxs-lookup"><span data-stu-id="6a851-201">The output from the above command ends with a message that the deployment was successful.</span></span>

   ```
   remote: Finished successfully.
   remote: Running post deployment command(s)...
   remote: Deployment successful.
   To https://username@samplewebappdemo01.scm.azurewebsites.net:443/SampleWebAppDemo01.git
   * [new branch]      master -> master
   Branch master set up to track remote branch master from Azure-SampleApp.
   ```

   > [!NOTE]
   > <span data-ttu-id="6a851-202">Pokud je nutná spolupráce na projektu, zvažte odeslání na [GitHub](https://github.com) před odesláním do Azure.</span><span class="sxs-lookup"><span data-stu-id="6a851-202">If collaboration on the project is required, consider pushing to [GitHub](https://github.com) before pushing to Azure.</span></span>
 
### <a name="verify-the-active-deployment"></a><span data-ttu-id="6a851-203">Ověření aktivního nasazení</span><span class="sxs-lookup"><span data-stu-id="6a851-203">Verify the Active Deployment</span></span>

<span data-ttu-id="6a851-204">Ověřte, zda je přenos webové aplikace z místního prostředí do Azure úspěšný.</span><span class="sxs-lookup"><span data-stu-id="6a851-204">Verify that the web app transfer from the local environment to Azure is successful.</span></span>

<span data-ttu-id="6a851-205">Na [webu Azure Portal](https://portal.azure.com)vyberte webovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="6a851-205">In the [Azure Portal](https://portal.azure.com), select the web app.</span></span> <span data-ttu-id="6a851-206">Vyberte **možnosti** > **nasazení**.</span><span class="sxs-lookup"><span data-stu-id="6a851-206">Select **Deployment** > **Deployment options**.</span></span>

![Azure Portal: Okno Nastavení: Okno nasazení zobrazující úspěšné nasazení](azure-continuous-deployment/_static/13-verify-deployment.png)

## <a name="run-the-app-in-azure"></a><span data-ttu-id="6a851-208">Spuštění aplikace v Azure</span><span class="sxs-lookup"><span data-stu-id="6a851-208">Run the app in Azure</span></span>

<span data-ttu-id="6a851-209">Teď, když se webová aplikace nasadí do Azure, spusťte ji.</span><span class="sxs-lookup"><span data-stu-id="6a851-209">Now that the web app is deployed to Azure, run the app.</span></span>

<span data-ttu-id="6a851-210">Toho lze dosáhnout dvěma způsoby:</span><span class="sxs-lookup"><span data-stu-id="6a851-210">This can be accomplished in two ways:</span></span>

* <span data-ttu-id="6a851-211">Na webu Azure Portal vyhledejte okno webové aplikace pro webovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="6a851-211">In the Azure Portal, locate the web app blade for the web app.</span></span> <span data-ttu-id="6a851-212">Výběrem **možnosti Procházet** zobrazíte aplikaci ve výchozím prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="6a851-212">Select **Browse** to view the app in the default browser.</span></span>
* <span data-ttu-id="6a851-213">Otevřete prohlížeč a zadejte adresu URL webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="6a851-213">Open a browser and enter the URL for the web app.</span></span> <span data-ttu-id="6a851-214">Příklad: `http://SampleWebAppDemo.azurewebsites.net`</span><span class="sxs-lookup"><span data-stu-id="6a851-214">Example: `http://SampleWebAppDemo.azurewebsites.net`</span></span>

## <a name="update-the-web-app-and-republish"></a><span data-ttu-id="6a851-215">Aktualizace webové aplikace a opětovné publikování</span><span class="sxs-lookup"><span data-stu-id="6a851-215">Update the web app and republish</span></span>

<span data-ttu-id="6a851-216">Po provedení změn v místním kódu znovu publikujte:</span><span class="sxs-lookup"><span data-stu-id="6a851-216">After making changes to the local code, republish:</span></span>

1. <span data-ttu-id="6a851-217">V **Průzkumníku řešení** sady Visual Studio otevřete *soubor Startup.cs.*</span><span class="sxs-lookup"><span data-stu-id="6a851-217">In **Solution Explorer** of Visual Studio, open the *Startup.cs* file.</span></span>

1. <span data-ttu-id="6a851-218">V `Configure` metodě upravte metodu `Response.WriteAsync` tak, aby se zjevná takto:</span><span class="sxs-lookup"><span data-stu-id="6a851-218">In the `Configure` method, modify the `Response.WriteAsync` method so that it appears as follows:</span></span>

   ```csharp
   await context.Response.WriteAsync("Hello World! Deploy to Azure.");
   ```

1. <span data-ttu-id="6a851-219">Uložte změny do *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="6a851-219">Save the changes to *Startup.cs*.</span></span>

1. <span data-ttu-id="6a851-220">V **Průzkumníku řešení**klepněte pravým tlačítkem myši na **položku Řešení SampleWebAppDemo** a vyberte **příkaz Potvrdit**.</span><span class="sxs-lookup"><span data-stu-id="6a851-220">In **Solution Explorer**, right-click **Solution 'SampleWebAppDemo'** and select **Commit**.</span></span> <span data-ttu-id="6a851-221">Zobrazí se **Team Explorer**.</span><span class="sxs-lookup"><span data-stu-id="6a851-221">The **Team Explorer** is displayed.</span></span>

1. <span data-ttu-id="6a851-222">Zadejte zprávu o `Update #2`potvrzení, například .</span><span class="sxs-lookup"><span data-stu-id="6a851-222">Enter a commit message, such as `Update #2`.</span></span>

1. <span data-ttu-id="6a851-223">Stisknutím tlačítka **Potvrdit** potvrďte změny projektu.</span><span class="sxs-lookup"><span data-stu-id="6a851-223">Press the **Commit** button to commit the project changes.</span></span>

1. <span data-ttu-id="6a851-224">Vyberte **položku Akce** > **domácí synchronizace** > **Actions** > **Push**.</span><span class="sxs-lookup"><span data-stu-id="6a851-224">Select **Home** > **Sync** > **Actions** > **Push**.</span></span>

> [!NOTE]
> <span data-ttu-id="6a851-225">Jako alternativu posunuji změny z **příkazového okna** otevřením **příkazového okna**, změnou adresáře projektu a zadáním příkazu git.</span><span class="sxs-lookup"><span data-stu-id="6a851-225">As an alternative, push the changes from the **Command Window** by opening the **Command Window**, changing to the project directory, and entering a git command.</span></span> <span data-ttu-id="6a851-226">Příklad:</span><span class="sxs-lookup"><span data-stu-id="6a851-226">Example:</span></span>
> 
> `git push -u Azure-SampleApp master`

## <a name="view-the-updated-web-app-in-azure"></a><span data-ttu-id="6a851-227">Zobrazení aktualizované webové aplikace v Azure</span><span class="sxs-lookup"><span data-stu-id="6a851-227">View the updated web app in Azure</span></span>

<span data-ttu-id="6a851-228">Aktualizovanou webovou aplikaci si můžete zobrazit tak, že **vyberete Procházet** z okna webové aplikace na Webu Azure Portal nebo otevřete prohlížeč a zajděte do adresy URL webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="6a851-228">View the updated web app by selecting **Browse** from the web app blade in the Azure Portal or by opening a browser and entering the URL for the web app.</span></span> <span data-ttu-id="6a851-229">Příklad: `http://SampleWebAppDemo.azurewebsites.net`</span><span class="sxs-lookup"><span data-stu-id="6a851-229">Example: `http://SampleWebAppDemo.azurewebsites.net`</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6a851-230">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="6a851-230">Additional resources</span></span>

* [<span data-ttu-id="6a851-231">Vytvoření prvního kanálu pomocí Azure Pipelines</span><span class="sxs-lookup"><span data-stu-id="6a851-231">Create your first pipeline with Azure Pipelines</span></span>](/azure/devops/pipelines/get-started-yaml)
* [<span data-ttu-id="6a851-232">Projekt Kudu</span><span class="sxs-lookup"><span data-stu-id="6a851-232">Project Kudu</span></span>](https://github.com/projectkudu/kudu/wiki)
* <xref:host-and-deploy/visual-studio-publish-profiles>
