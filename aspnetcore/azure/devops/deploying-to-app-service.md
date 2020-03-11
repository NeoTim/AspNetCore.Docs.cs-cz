---
title: Nasazení aplikace do App Service – DevOps s využitím ASP.NET Core a Azure
author: CamSoper
description: Nasazení aplikace ASP.NET Core do služby Azure App Service, prvním krokem pro vývoj a provoz s ASP.NET Core a Azure.
ms.author: casoper
ms.custom: mvc, seodec18
ms.date: 10/24/2018
uid: azure/devops/deploy-to-app-service
ms.openlocfilehash: df41f296e9c4e1eff6e31d45b29ec30ee1e20cf4
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78657743"
---
# <a name="deploy-an-app-to-app-service"></a><span data-ttu-id="9b9ca-103">Nasazení aplikace do služby App Service</span><span class="sxs-lookup"><span data-stu-id="9b9ca-103">Deploy an app to App Service</span></span>

<span data-ttu-id="9b9ca-104">[Azure App Service](/azure/app-service/) platforma pro hostování webů v Azure.</span><span class="sxs-lookup"><span data-stu-id="9b9ca-104">[Azure App Service](/azure/app-service/) is Azure's web hosting platform.</span></span> <span data-ttu-id="9b9ca-105">Nasazení webové aplikace do služby Azure App Service můžete provést ručně nebo pomocí automatizovaného procesu.</span><span class="sxs-lookup"><span data-stu-id="9b9ca-105">Deploying a web app to Azure App Service can be done manually or by an automated process.</span></span> <span data-ttu-id="9b9ca-106">Tato část průvodce popisuje metody nasazení, které můžete aktivovat ručně nebo pomocí příkazového řádku skriptu nebo aktivuje ručně pomocí sady Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="9b9ca-106">This section of the guide discusses deployment methods that can be triggered manually or by script using the command line, or triggered manually using Visual Studio.</span></span>

<span data-ttu-id="9b9ca-107">V této části budete provádět následující úlohy:</span><span class="sxs-lookup"><span data-stu-id="9b9ca-107">In this section, you'll accomplish the following tasks:</span></span>

* <span data-ttu-id="9b9ca-108">Stáhněte si a sestavte ukázkové aplikace.</span><span class="sxs-lookup"><span data-stu-id="9b9ca-108">Download and build the sample app.</span></span>
* <span data-ttu-id="9b9ca-109">Vytvoření Azure webové aplikace služby App Service pomocí Azure Cloud Shell.</span><span class="sxs-lookup"><span data-stu-id="9b9ca-109">Create an Azure App Service Web App using the Azure Cloud Shell.</span></span>
* <span data-ttu-id="9b9ca-110">Nasazení ukázkové aplikace do Azure pomocí Gitu.</span><span class="sxs-lookup"><span data-stu-id="9b9ca-110">Deploy the sample app to Azure using Git.</span></span>
* <span data-ttu-id="9b9ca-111">Nasazení změny do aplikace pomocí sady Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="9b9ca-111">Deploy a change to the app using Visual Studio.</span></span>
* <span data-ttu-id="9b9ca-112">Přidáte přípravný slot webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="9b9ca-112">Add a staging slot to the web app.</span></span>
* <span data-ttu-id="9b9ca-113">Aktualizace nasazení do přípravného slotu.</span><span class="sxs-lookup"><span data-stu-id="9b9ca-113">Deploy an update to the staging slot.</span></span>
* <span data-ttu-id="9b9ca-114">Prohození slotů pracovního a produkčního prostředí.</span><span class="sxs-lookup"><span data-stu-id="9b9ca-114">Swap the staging and production slots.</span></span>

## <a name="download-and-test-the-app"></a><span data-ttu-id="9b9ca-115">Stáhněte si a testování aplikace</span><span class="sxs-lookup"><span data-stu-id="9b9ca-115">Download and test the app</span></span>

<span data-ttu-id="9b9ca-116">Aplikace použitá v tomto průvodci je předem vytvořená aplikace ASP.NET Core, [Jednoduchá čtečka kanálů](https://github.com/Azure-Samples/simple-feed-reader/).</span><span class="sxs-lookup"><span data-stu-id="9b9ca-116">The app used in this guide is a pre-built ASP.NET Core app, [Simple Feed Reader](https://github.com/Azure-Samples/simple-feed-reader/).</span></span> <span data-ttu-id="9b9ca-117">Jedná se o Razor Pages aplikaci, která používá rozhraní `Microsoft.SyndicationFeed.ReaderWriter` API k načtení informačního kanálu RSS/Atom a zobrazení položek zpráv v seznamu.</span><span class="sxs-lookup"><span data-stu-id="9b9ca-117">It's a Razor Pages app that uses the `Microsoft.SyndicationFeed.ReaderWriter` API to retrieve an RSS/Atom feed and display the news items in a list.</span></span>

<span data-ttu-id="9b9ca-118">Nebojte se podívejte kódu, ale je důležité pochopit, že není nic zvláštního o této aplikaci.</span><span class="sxs-lookup"><span data-stu-id="9b9ca-118">Feel free to review the code, but it's important to understand that there's nothing special about this app.</span></span> <span data-ttu-id="9b9ca-119">Pro ilustraci je stejně jednoduché aplikace ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="9b9ca-119">It's just a simple ASP.NET Core app for illustrative purposes.</span></span>

<span data-ttu-id="9b9ca-120">Z příkazové okno stáhnout kód, sestavte projekt a spusťte následujícím způsobem.</span><span class="sxs-lookup"><span data-stu-id="9b9ca-120">From a command shell, download the code, build the project, and run it as follows.</span></span>

> <span data-ttu-id="9b9ca-121">*Poznámka: uživatelé systému Linux/macOS by měli provádět vhodné změny cest, například pomocí lomítka (`/`) místo zpětného lomítka (`\`).*</span><span class="sxs-lookup"><span data-stu-id="9b9ca-121">*Note: Linux/macOS users should make appropriate changes for paths, e.g., using forward slash (`/`) rather than back slash (`\`).*</span></span>

1. <span data-ttu-id="9b9ca-122">Klonování kódu do složky na místním počítači.</span><span class="sxs-lookup"><span data-stu-id="9b9ca-122">Clone the code to a folder on your local machine.</span></span>

    ```console
    git clone https://github.com/Azure-Samples/simple-feed-reader/
    ```

2. <span data-ttu-id="9b9ca-123">Změňte pracovní složku na vytvořenou složku s *jednoduchým kanálem pro čtení* .</span><span class="sxs-lookup"><span data-stu-id="9b9ca-123">Change your working folder to the *simple-feed-reader* folder that was created.</span></span>

    ```console
    cd .\simple-feed-reader\SimpleFeedReader
    ```

3. <span data-ttu-id="9b9ca-124">Obnovte balíčky a sestavte řešení.</span><span class="sxs-lookup"><span data-stu-id="9b9ca-124">Restore the packages, and build the solution.</span></span>

    ```dotnetcli
    dotnet build
    ```

4. <span data-ttu-id="9b9ca-125">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="9b9ca-125">Run the app.</span></span>

    ```dotnetcli
    dotnet run
    ```

    ![Spusťte příkaz dotnet je úspěšné](./media/deploying-to-app-service/dotnet-run.png)

5. <span data-ttu-id="9b9ca-127">Otevřete prohlížeč a přejděte na adresu `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="9b9ca-127">Open a browser and navigate to `http://localhost:5000`.</span></span> <span data-ttu-id="9b9ca-128">Aplikace umožňuje zadejte nebo vložte adresu URL informačního kanálu syndikace a zobrazení seznamu položek zpráv.</span><span class="sxs-lookup"><span data-stu-id="9b9ca-128">The app allows you to type or paste a syndication feed URL and view a list of news items.</span></span>

     ![Aplikace zobrazení obsahu informačního kanálu RSS](./media/deploying-to-app-service/app-in-browser.png)

6. <span data-ttu-id="9b9ca-130">Jakmile budete spokojeni s tím, že aplikace funguje správně, vypněte ji stisknutím **kombinace kláves Ctrl**+**C** v příkazovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="9b9ca-130">Once you're satisfied the app is working correctly, shut it down by pressing **Ctrl**+**C** in the command shell.</span></span>

## <a name="create-the-azure-app-service-web-app"></a><span data-ttu-id="9b9ca-131">Vytvoření webové aplikace Azure App Service</span><span class="sxs-lookup"><span data-stu-id="9b9ca-131">Create the Azure App Service Web App</span></span>

<span data-ttu-id="9b9ca-132">K nasazení aplikace budete muset vytvořit [webovou aplikaci](/azure/app-service/app-service-web-overview)App Service.</span><span class="sxs-lookup"><span data-stu-id="9b9ca-132">To deploy the app, you'll need to create an App Service [Web App](/azure/app-service/app-service-web-overview).</span></span> <span data-ttu-id="9b9ca-133">Po vytvoření webové aplikace nasadíte do něj z místního počítače pomocí Gitu.</span><span class="sxs-lookup"><span data-stu-id="9b9ca-133">After creation of the Web App, you'll deploy to it from your local machine using Git.</span></span>

1. <span data-ttu-id="9b9ca-134">Přihlaste se do služby [Azure Cloud Shell](https://shell.azure.com/bash).</span><span class="sxs-lookup"><span data-stu-id="9b9ca-134">Sign in to the [Azure Cloud Shell](https://shell.azure.com/bash).</span></span> <span data-ttu-id="9b9ca-135">Poznámka: Při přihlášení poprvé Cloud Shell zobrazí výzvu k vytvoření účtu úložiště pro konfigurační soubory.</span><span class="sxs-lookup"><span data-stu-id="9b9ca-135">Note: When you sign in for the first time, Cloud Shell prompts to create a storage account for configuration files.</span></span> <span data-ttu-id="9b9ca-136">Přijměte výchozí hodnoty nebo zadejte jedinečný název.</span><span class="sxs-lookup"><span data-stu-id="9b9ca-136">Accept the defaults or provide a unique name.</span></span>

2. <span data-ttu-id="9b9ca-137">Pomocí služby Cloud Shell pro následující kroky.</span><span class="sxs-lookup"><span data-stu-id="9b9ca-137">Use the Cloud Shell for the following steps.</span></span>

    <span data-ttu-id="9b9ca-138">a.</span><span class="sxs-lookup"><span data-stu-id="9b9ca-138">a.</span></span> <span data-ttu-id="9b9ca-139">Deklarujte proměnnou pro uložení název webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="9b9ca-139">Declare a variable to store your web app's name.</span></span> <span data-ttu-id="9b9ca-140">Název musí být jedinečný pro výchozí adresy URL.</span><span class="sxs-lookup"><span data-stu-id="9b9ca-140">The name must be unique to be used in the default URL.</span></span> <span data-ttu-id="9b9ca-141">Použití funkce `$RANDOM` bash k vytvoření názvu jedinečnosti a výsledků ve formátu `webappname99999`.</span><span class="sxs-lookup"><span data-stu-id="9b9ca-141">Using the `$RANDOM` Bash function to construct the name guarantees uniqueness and results in the format `webappname99999`.</span></span>

    ```console
    webappname=mywebapp$RANDOM
    ```

    <span data-ttu-id="9b9ca-142">b.</span><span class="sxs-lookup"><span data-stu-id="9b9ca-142">b.</span></span> <span data-ttu-id="9b9ca-143">Vytvořte skupinu prostředků.</span><span class="sxs-lookup"><span data-stu-id="9b9ca-143">Create a resource group.</span></span> <span data-ttu-id="9b9ca-144">Skupiny prostředků umožňují agregovat prostředky Azure, které jde spravovat jako skupinu.</span><span class="sxs-lookup"><span data-stu-id="9b9ca-144">Resource groups provide a means to aggregate Azure resources to be managed as a group.</span></span>

    ```azure-cli
    az group create --location centralus --name AzureTutorial
    ```

    <span data-ttu-id="9b9ca-145">Příkaz `az` vyvolá rozhraní příkazového [řádku Azure CLI](/cli/azure/).</span><span class="sxs-lookup"><span data-stu-id="9b9ca-145">The `az` command invokes the [Azure CLI](/cli/azure/).</span></span> <span data-ttu-id="9b9ca-146">Rozhraní příkazového řádku můžete spustit místně, ale použití ve službě Cloud Shell šetří čas a konfigurace.</span><span class="sxs-lookup"><span data-stu-id="9b9ca-146">The CLI can be run locally, but using it in the Cloud Shell saves time and configuration.</span></span>

    <span data-ttu-id="9b9ca-147">c.</span><span class="sxs-lookup"><span data-stu-id="9b9ca-147">c.</span></span> <span data-ttu-id="9b9ca-148">Vytvoření plánu služby App Service na úrovni S1.</span><span class="sxs-lookup"><span data-stu-id="9b9ca-148">Create an App Service plan in the S1 tier.</span></span> <span data-ttu-id="9b9ca-149">Plán služby App Service je seskupení webové aplikace, které sdílejí stejnou cenovou úroveň.</span><span class="sxs-lookup"><span data-stu-id="9b9ca-149">An App Service plan is a grouping of web apps that share the same pricing tier.</span></span> <span data-ttu-id="9b9ca-150">Úroveň S1 není zdarma, ale vyžaduje se pro funkci přípravné sloty.</span><span class="sxs-lookup"><span data-stu-id="9b9ca-150">The S1 tier isn't free, but it's required for the staging slots feature.</span></span>

    ```azure-cli
    az appservice plan create --name $webappname --resource-group AzureTutorial --sku S1
    ```

    <span data-ttu-id="9b9ca-151">d.</span><span class="sxs-lookup"><span data-stu-id="9b9ca-151">d.</span></span> <span data-ttu-id="9b9ca-152">Vytvoření prostředku webové aplikace pomocí plán služby App Service ve stejné skupině prostředků.</span><span class="sxs-lookup"><span data-stu-id="9b9ca-152">Create the web app resource using the App Service plan in the same resource group.</span></span>

    ```azure-cli
    az webapp create --name $webappname --resource-group AzureTutorial --plan $webappname
    ```

    <span data-ttu-id="9b9ca-153">e.</span><span class="sxs-lookup"><span data-stu-id="9b9ca-153">e.</span></span> <span data-ttu-id="9b9ca-154">Nastavte přihlašovací údaje pro nasazení.</span><span class="sxs-lookup"><span data-stu-id="9b9ca-154">Set the deployment credentials.</span></span> <span data-ttu-id="9b9ca-155">Tyto přihlašovací údaje pro nasazení platí pro všechny webové aplikace ve vašem předplatném.</span><span class="sxs-lookup"><span data-stu-id="9b9ca-155">These deployment credentials apply to all the web apps in your subscription.</span></span> <span data-ttu-id="9b9ca-156">Nepoužívejte speciální znaky v uživatelské jméno.</span><span class="sxs-lookup"><span data-stu-id="9b9ca-156">Don't use special characters in the user name.</span></span>

    ```azure-cli
    az webapp deployment user set --user-name REPLACE_WITH_USER_NAME --password REPLACE_WITH_PASSWORD
    ```

    <span data-ttu-id="9b9ca-157">f.</span><span class="sxs-lookup"><span data-stu-id="9b9ca-157">f.</span></span> <span data-ttu-id="9b9ca-158">Nakonfigurujte webovou aplikaci tak, aby přijímala nasazení z místního Gitu a zobrazila *adresu URL nasazení Git*.</span><span class="sxs-lookup"><span data-stu-id="9b9ca-158">Configure the web app to accept deployments from local Git and display the *Git deployment URL*.</span></span> <span data-ttu-id="9b9ca-159">**Poznamenejte si tuto adresu URL pro referenci později**.</span><span class="sxs-lookup"><span data-stu-id="9b9ca-159">**Note this URL for reference later**.</span></span>

    ```azure-cli
    echo Git deployment URL: $(az webapp deployment source config-local-git --name $webappname --resource-group AzureTutorial --query url --output tsv)
    ```

    <span data-ttu-id="9b9ca-160">g.</span><span class="sxs-lookup"><span data-stu-id="9b9ca-160">g.</span></span> <span data-ttu-id="9b9ca-161">Zobrazí *adresu URL webové aplikace*.</span><span class="sxs-lookup"><span data-stu-id="9b9ca-161">Display the *web app URL*.</span></span> <span data-ttu-id="9b9ca-162">Přejděte na tuto adresu URL a zobrazte prázdnou webovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="9b9ca-162">Browse to this URL to see the blank web app.</span></span> <span data-ttu-id="9b9ca-163">**Poznamenejte si tuto adresu URL pro referenci později**.</span><span class="sxs-lookup"><span data-stu-id="9b9ca-163">**Note this URL for reference later**.</span></span>

    ```console
    echo Web app URL: http://$webappname.azurewebsites.net
    ```

3. <span data-ttu-id="9b9ca-164">Pomocí příkazového prostředí v místním počítači přejděte do složky projektu webové aplikace (například `.\simple-feed-reader\SimpleFeedReader`).</span><span class="sxs-lookup"><span data-stu-id="9b9ca-164">Using a command shell on your local machine, navigate to the web app's project folder (for example, `.\simple-feed-reader\SimpleFeedReader`).</span></span> <span data-ttu-id="9b9ca-165">Spusťte následující příkazy a nastavení Gitu tak, aby nabízel na adresu URL nasazení:</span><span class="sxs-lookup"><span data-stu-id="9b9ca-165">Execute the following commands to set up Git to push to the deployment URL:</span></span>

    <span data-ttu-id="9b9ca-166">a.</span><span class="sxs-lookup"><span data-stu-id="9b9ca-166">a.</span></span> <span data-ttu-id="9b9ca-167">Přidáte vzdálené adresy URL do místního úložiště.</span><span class="sxs-lookup"><span data-stu-id="9b9ca-167">Add the remote URL to the local repository.</span></span>

    ```console
    git remote add azure-prod GIT_DEPLOYMENT_URL
    ```

    <span data-ttu-id="9b9ca-168">b.</span><span class="sxs-lookup"><span data-stu-id="9b9ca-168">b.</span></span> <span data-ttu-id="9b9ca-169">Vložení místní *Hlavní* větve do *hlavní* větve *Azure-prod* Remote 's.</span><span class="sxs-lookup"><span data-stu-id="9b9ca-169">Push the local *master* branch to the *azure-prod* remote's *master* branch.</span></span>

    ```console
    git push azure-prod master
    ```

    <span data-ttu-id="9b9ca-170">Budete vyzváni, pro přihlašovací údaje nasazení, který jste vytvořili dříve.</span><span class="sxs-lookup"><span data-stu-id="9b9ca-170">You'll be prompted for the deployment credentials you created earlier.</span></span> <span data-ttu-id="9b9ca-171">Sledujte ve výstupu v příkazovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="9b9ca-171">Observe the output in the command shell.</span></span> <span data-ttu-id="9b9ca-172">Azure vytvoří aplikace ASP.NET Core vzdáleně.</span><span class="sxs-lookup"><span data-stu-id="9b9ca-172">Azure builds the ASP.NET Core app remotely.</span></span>

4. <span data-ttu-id="9b9ca-173">V prohlížeči přejděte na *adresu URL webové aplikace* a Všimněte si, že je aplikace sestavená a nasazená.</span><span class="sxs-lookup"><span data-stu-id="9b9ca-173">In a browser, navigate to the *Web app URL* and note the app has been built and deployed.</span></span> <span data-ttu-id="9b9ca-174">Další změny můžete zapsat do místního úložiště Git s `git commit`.</span><span class="sxs-lookup"><span data-stu-id="9b9ca-174">Additional changes can be committed to the local Git repository with `git commit`.</span></span> <span data-ttu-id="9b9ca-175">Tyto změny se odešlou do Azure pomocí předchozího příkazu `git push`.</span><span class="sxs-lookup"><span data-stu-id="9b9ca-175">These changes are pushed to Azure with the preceding `git push` command.</span></span>

## <a name="deployment-with-visual-studio"></a><span data-ttu-id="9b9ca-176">Nasazení pomocí sady Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9b9ca-176">Deployment with Visual Studio</span></span>

> <span data-ttu-id="9b9ca-177">*Poznámka: Tato část se vztahuje pouze na systém Windows. Uživatelé Linux a macOS by měli provést změnu popsanou v kroku 2 níže. Uložte soubor a potvrďte změnu v místním úložišti pomocí `git commit`. Nakonec nahrajte změnu pomocí `git push`, jako v první části.*</span><span class="sxs-lookup"><span data-stu-id="9b9ca-177">*Note: This section applies to Windows only. Linux and macOS users should make the change described in step 2 below. Save the file, and commit the change to the local repository with `git commit`. Finally, push the change with `git push`, as in the first section.*</span></span>

<span data-ttu-id="9b9ca-178">Z příkazového okna již byla nasazena aplikace.</span><span class="sxs-lookup"><span data-stu-id="9b9ca-178">The app has already been deployed from the command shell.</span></span> <span data-ttu-id="9b9ca-179">S použitím integrovaných nástrojů sady Visual Studio nasadit aktualizace do aplikace.</span><span class="sxs-lookup"><span data-stu-id="9b9ca-179">Let's use Visual Studio's integrated tools to deploy an update to the app.</span></span> <span data-ttu-id="9b9ca-180">Na pozadí Visual Studio totéž jako nástrojů příkazového řádku, ale v rámci známé uživatelské rozhraní sady Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="9b9ca-180">Behind the scenes, Visual Studio accomplishes the same thing as the command line tooling, but within Visual Studio's familiar UI.</span></span>

1. <span data-ttu-id="9b9ca-181">Otevřete *SimpleFeedReader. sln* v aplikaci Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="9b9ca-181">Open *SimpleFeedReader.sln* in Visual Studio.</span></span>
2. <span data-ttu-id="9b9ca-182">V Průzkumník řešení otevřete *Pages\Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="9b9ca-182">In Solution Explorer, open *Pages\Index.cshtml*.</span></span> <span data-ttu-id="9b9ca-183">Změňte `<h2>Simple Feed Reader</h2>` na `<h2>Simple Feed Reader - V2</h2>`.</span><span class="sxs-lookup"><span data-stu-id="9b9ca-183">Change `<h2>Simple Feed Reader</h2>` to `<h2>Simple Feed Reader - V2</h2>`.</span></span>
3. <span data-ttu-id="9b9ca-184">Stisknutím **kombinace kláves Ctrl**+**SHIFT**+**B** sestavte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="9b9ca-184">Press **Ctrl**+**Shift**+**B** to build the app.</span></span>
4. <span data-ttu-id="9b9ca-185">V Průzkumník řešení klikněte pravým tlačítkem na projekt a klikněte na **publikovat**.</span><span class="sxs-lookup"><span data-stu-id="9b9ca-185">In Solution Explorer, right-click on the project and click **Publish**.</span></span>

    ![Snímek obrazovky znázorňující kliknutí pravým tlačítkem myši, publikovat](./media/deploying-to-app-service/publish.png)
5. <span data-ttu-id="9b9ca-187">Visual Studio můžete vytvořit nový prostředek služby App Service, ale tato aktualizace bude publikován přes existující nasazení.</span><span class="sxs-lookup"><span data-stu-id="9b9ca-187">Visual Studio can create a new App Service resource, but this update will be published over the existing deployment.</span></span> <span data-ttu-id="9b9ca-188">V dialogovém okně **vybrat cíl publikování** vyberte v seznamu na levé straně položku **App Service** a pak vyberte **Vybrat existující**.</span><span class="sxs-lookup"><span data-stu-id="9b9ca-188">In the **Pick a publish target** dialog, select **App Service** from the list on the left, and then select **Select Existing**.</span></span> <span data-ttu-id="9b9ca-189">Klikněte na **Publikovat**.</span><span class="sxs-lookup"><span data-stu-id="9b9ca-189">Click **Publish**.</span></span>
6. <span data-ttu-id="9b9ca-190">V dialogovém okně **App Service** potvrďte, že se v pravém horním rohu zobrazuje účet Microsoft nebo organizace, který se používá k vytvoření vašeho předplatného Azure.</span><span class="sxs-lookup"><span data-stu-id="9b9ca-190">In the **App Service** dialog, confirm that the Microsoft or Organizational account used to create your Azure subscription is displayed in the upper right.</span></span> <span data-ttu-id="9b9ca-191">Pokud není, klikněte na rozevírací seznam a přidejte ji.</span><span class="sxs-lookup"><span data-stu-id="9b9ca-191">If it's not, click the drop-down and add it.</span></span>
7. <span data-ttu-id="9b9ca-192">Zkontrolujte, jestli je vybrané správné **předplatné** Azure.</span><span class="sxs-lookup"><span data-stu-id="9b9ca-192">Confirm that the correct Azure **Subscription** is selected.</span></span> <span data-ttu-id="9b9ca-193">V **zobrazení**vyberte **Skupina prostředků**.</span><span class="sxs-lookup"><span data-stu-id="9b9ca-193">For **View**, select **Resource Group**.</span></span> <span data-ttu-id="9b9ca-194">Rozbalte skupinu prostředků **AzureTutorial** a pak vyberte existující webovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="9b9ca-194">Expand the **AzureTutorial** resource group and then select the existing web app.</span></span> <span data-ttu-id="9b9ca-195">Klikněte na tlačítko **OK**.</span><span class="sxs-lookup"><span data-stu-id="9b9ca-195">Click **OK**.</span></span>

    ![Snímek obrazovky zobrazující dialogovém okně Publikovat App Service](./media/deploying-to-app-service/publish-dialog.png)

<span data-ttu-id="9b9ca-197">Visual Studio vytvoří a nasadí aplikaci do Azure.</span><span class="sxs-lookup"><span data-stu-id="9b9ca-197">Visual Studio builds and deploys the app to Azure.</span></span> <span data-ttu-id="9b9ca-198">Přejděte na adresu URL webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="9b9ca-198">Browse to the web app URL.</span></span> <span data-ttu-id="9b9ca-199">Ověřte, zda je úprava prvku `<h2>` aktivní.</span><span class="sxs-lookup"><span data-stu-id="9b9ca-199">Validate that the `<h2>` element modification is live.</span></span>

![Aplikaci se změnili jsme název](./media/deploying-to-app-service/app-v2.png)

## <a name="deployment-slots"></a><span data-ttu-id="9b9ca-201">Nasazovací sloty</span><span class="sxs-lookup"><span data-stu-id="9b9ca-201">Deployment slots</span></span>

<span data-ttu-id="9b9ca-202">Sloty nasazení podporují pracovní změny bez dopadu na aplikace běžící v produkčním prostředí.</span><span class="sxs-lookup"><span data-stu-id="9b9ca-202">Deployment slots support the staging of changes without impacting the app running in production.</span></span> <span data-ttu-id="9b9ca-203">Jakmile tým pro zajištění kvality vyhodnocuje připravenou verzi aplikace, je možné Prohodit produkční a přípravné sloty.</span><span class="sxs-lookup"><span data-stu-id="9b9ca-203">Once the staged version of the app is validated by a quality assurance team, the production and staging slots can be swapped.</span></span> <span data-ttu-id="9b9ca-204">Aplikace v testovacím prostředí je propagována do produkčního prostředí tímto způsobem.</span><span class="sxs-lookup"><span data-stu-id="9b9ca-204">The app in staging is promoted to production in this manner.</span></span> <span data-ttu-id="9b9ca-205">Následující kroky vytvořit přípravný slot, nasadíme do ní nějaké změny a Prohodit s produkčním prostředí po ověření přípravný slot.</span><span class="sxs-lookup"><span data-stu-id="9b9ca-205">The following steps create a staging slot, deploy some changes to it, and swap the staging slot with production after verification.</span></span>

1. <span data-ttu-id="9b9ca-206">Přihlaste se k [Azure Cloud Shell](https://shell.azure.com/bash), pokud ještě není přihlášený.</span><span class="sxs-lookup"><span data-stu-id="9b9ca-206">Sign in to the [Azure Cloud Shell](https://shell.azure.com/bash), if not already signed in.</span></span>
2. <span data-ttu-id="9b9ca-207">Vytvořte přípravný slot.</span><span class="sxs-lookup"><span data-stu-id="9b9ca-207">Create the staging slot.</span></span>

    <span data-ttu-id="9b9ca-208">a.</span><span class="sxs-lookup"><span data-stu-id="9b9ca-208">a.</span></span> <span data-ttu-id="9b9ca-209">Vytvořte slot nasazení s názvem *Příprava*.</span><span class="sxs-lookup"><span data-stu-id="9b9ca-209">Create a deployment slot with the name *staging*.</span></span>

    ```azure-cli
    az webapp deployment slot create --name $webappname --resource-group AzureTutorial --slot staging
    ```

    <span data-ttu-id="9b9ca-210">b.</span><span class="sxs-lookup"><span data-stu-id="9b9ca-210">b.</span></span> <span data-ttu-id="9b9ca-211">Nakonfigurujte pracovní slot pro použití nasazení z místního Gitu a získejte adresu URL **pracovního** nasazení.</span><span class="sxs-lookup"><span data-stu-id="9b9ca-211">Configure the staging slot to use deployment from local Git and get the **staging** deployment URL.</span></span> <span data-ttu-id="9b9ca-212">**Poznamenejte si tuto adresu URL pro referenci později**.</span><span class="sxs-lookup"><span data-stu-id="9b9ca-212">**Note this URL for reference later**.</span></span>

    ```azure-cli
    echo Git deployment URL for staging: $(az webapp deployment source config-local-git --name $webappname --resource-group AzureTutorial --slot staging --query url --output tsv)
    ```

    <span data-ttu-id="9b9ca-213">c.</span><span class="sxs-lookup"><span data-stu-id="9b9ca-213">c.</span></span> <span data-ttu-id="9b9ca-214">Zobrazte adresu URL přípravný slot.</span><span class="sxs-lookup"><span data-stu-id="9b9ca-214">Display the staging slot's URL.</span></span> <span data-ttu-id="9b9ca-215">Přejděte na adresu URL, pokud chcete zobrazit prázdné přípravný slot.</span><span class="sxs-lookup"><span data-stu-id="9b9ca-215">Browse to the URL to see the empty staging slot.</span></span> <span data-ttu-id="9b9ca-216">**Poznamenejte si tuto adresu URL pro referenci později**.</span><span class="sxs-lookup"><span data-stu-id="9b9ca-216">**Note this URL for reference later**.</span></span>

    ```console
    echo Staging web app URL: http://$webappname-staging.azurewebsites.net
    ```

3. <span data-ttu-id="9b9ca-217">V textovém editoru nebo v aplikaci Visual Studio upravte *stránky nebo index. cshtml* tak, aby `<h2>` element četl `<h2>Simple Feed Reader - V3</h2>` a soubor uložte.</span><span class="sxs-lookup"><span data-stu-id="9b9ca-217">In a text editor or Visual Studio, modify *Pages/Index.cshtml* again so that the `<h2>` element reads `<h2>Simple Feed Reader - V3</h2>` and save the file.</span></span>

4. <span data-ttu-id="9b9ca-218">Potvrďte soubor do místního úložiště Git, a to pomocí stránky **změny** na kartě *Team Explorer* sady Visual Studio nebo zadáním následujícího příkazu pomocí příkazového prostředí místního počítače:</span><span class="sxs-lookup"><span data-stu-id="9b9ca-218">Commit the file to the local Git repository, using either the **Changes** page in Visual Studio's *Team Explorer* tab, or by entering the following using the local machine's command shell:</span></span>

    ```console
    git commit -a -m "upgraded to V3"
    ```

5. <span data-ttu-id="9b9ca-219">Pomocí příkazového prostředí služby místním počítači, přidejte adresu URL pracovní nasazení jako vzdálené úložiště Git a push potvrzené změny:</span><span class="sxs-lookup"><span data-stu-id="9b9ca-219">Using the local machine's command shell, add the staging deployment URL as a Git remote and push the committed changes:</span></span>

    <span data-ttu-id="9b9ca-220">a.</span><span class="sxs-lookup"><span data-stu-id="9b9ca-220">a.</span></span> <span data-ttu-id="9b9ca-221">Přidáte vzdálené adresy URL pro pracovní do místního úložiště Git.</span><span class="sxs-lookup"><span data-stu-id="9b9ca-221">Add the remote URL for staging to the local Git repository.</span></span>

    ```console
    git remote add azure-staging <Git_staging_deployment_URL>
    ```

    <span data-ttu-id="9b9ca-222">b.</span><span class="sxs-lookup"><span data-stu-id="9b9ca-222">b.</span></span> <span data-ttu-id="9b9ca-223">Nahrajte místní *Hlavní* větev do *hlavní* větve vzdálené údržby *Azure* .</span><span class="sxs-lookup"><span data-stu-id="9b9ca-223">Push the local *master* branch to the *azure-staging* remote's *master* branch.</span></span>

    ```console
    git push azure-staging master
    ```

    <span data-ttu-id="9b9ca-224">Počkejte, Azure vytvoří a nasadí aplikaci.</span><span class="sxs-lookup"><span data-stu-id="9b9ca-224">Wait while Azure builds and deploys the app.</span></span>

6. <span data-ttu-id="9b9ca-225">Chcete-li ověřit, že V3 nasazení do přípravného slotu, otevřete dvě okna prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="9b9ca-225">To verify that V3 has been deployed to the staging slot, open two browser windows.</span></span> <span data-ttu-id="9b9ca-226">V jednom okně přejděte na adresu URL původní webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="9b9ca-226">In one window, navigate to the original web app URL.</span></span> <span data-ttu-id="9b9ca-227">V druhém okně přejděte na adresu URL pracovní webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="9b9ca-227">In the other window, navigate to the staging web app URL.</span></span> <span data-ttu-id="9b9ca-228">Adresa URL výroby slouží V2 aplikace.</span><span class="sxs-lookup"><span data-stu-id="9b9ca-228">The production URL serves V2 of the app.</span></span> <span data-ttu-id="9b9ca-229">Přípravnou adresu URL slouží V3 aplikace.</span><span class="sxs-lookup"><span data-stu-id="9b9ca-229">The staging URL serves V3 of the app.</span></span>

    ![Snímek obrazovky okna prohlížeče porovnání](./media/deploying-to-app-service/ready-to-swap.png)

7. <span data-ttu-id="9b9ca-231">Ve službě Cloud Shell Prohodit slot pro fázi ověření/zahřátého do produkčního prostředí.</span><span class="sxs-lookup"><span data-stu-id="9b9ca-231">In the Cloud Shell, swap the verified/warmed-up staging slot into production.</span></span>

    ```azure-cli
    az webapp deployment slot swap --name $webappname --resource-group AzureTutorial --slot staging
    ```

8. <span data-ttu-id="9b9ca-232">Ověřte, že došlo k prohození aktualizací okna prohlížeče dvě.</span><span class="sxs-lookup"><span data-stu-id="9b9ca-232">Verify that the swap occurred by refreshing the two browser windows.</span></span>

    ![Porovnání okna prohlížeče po prohození](./media/deploying-to-app-service/swapped.png)

## <a name="summary"></a><span data-ttu-id="9b9ca-234">Souhrn</span><span class="sxs-lookup"><span data-stu-id="9b9ca-234">Summary</span></span>

<span data-ttu-id="9b9ca-235">V této části dokončili jste následující úlohy:</span><span class="sxs-lookup"><span data-stu-id="9b9ca-235">In this section, the following tasks were completed:</span></span>

* <span data-ttu-id="9b9ca-236">Stáhnout a sestaven ukázkovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="9b9ca-236">Downloaded and built the sample app.</span></span>
* <span data-ttu-id="9b9ca-237">Vytvořit Azure webové aplikace služby App Service pomocí Azure Cloud Shell.</span><span class="sxs-lookup"><span data-stu-id="9b9ca-237">Created an Azure App Service Web App using the Azure Cloud Shell.</span></span>
* <span data-ttu-id="9b9ca-238">Nasazení ukázkové aplikace do Azure pomocí Gitu.</span><span class="sxs-lookup"><span data-stu-id="9b9ca-238">Deployed the sample app to Azure using Git.</span></span>
* <span data-ttu-id="9b9ca-239">Změna nasazené do aplikace pomocí sady Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="9b9ca-239">Deployed a change to the app using Visual Studio.</span></span>
* <span data-ttu-id="9b9ca-240">Přidat přípravný slot webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="9b9ca-240">Added a staging slot to the web app.</span></span>
* <span data-ttu-id="9b9ca-241">Aktualizace nasadit do přípravného slotu.</span><span class="sxs-lookup"><span data-stu-id="9b9ca-241">Deployed an update to the staging slot.</span></span>
* <span data-ttu-id="9b9ca-242">Prohodit sloty přípravným a produkčním prostředím.</span><span class="sxs-lookup"><span data-stu-id="9b9ca-242">Swapped the staging and production slots.</span></span>

<span data-ttu-id="9b9ca-243">V další části se dozvíte, jak vytvořit kanál DevOps s kanály Azure.</span><span class="sxs-lookup"><span data-stu-id="9b9ca-243">In the next section, you'll learn how to build a DevOps pipeline with Azure Pipelines.</span></span>

## <a name="additional-reading"></a><span data-ttu-id="9b9ca-244">Další čtení</span><span class="sxs-lookup"><span data-stu-id="9b9ca-244">Additional reading</span></span>

* [<span data-ttu-id="9b9ca-245">Web Apps – přehled</span><span class="sxs-lookup"><span data-stu-id="9b9ca-245">Web Apps overview</span></span>](/azure/app-service/app-service-web-overview)
* [<span data-ttu-id="9b9ca-246">Vytvoření webové aplikace .NET Core a SQL Database v Azure App Service</span><span class="sxs-lookup"><span data-stu-id="9b9ca-246">Build a .NET Core and SQL Database web app in Azure App Service</span></span>](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)
* [<span data-ttu-id="9b9ca-247">Nakonfigurovat přihlašovací údaje nasazení pro Azure App Service</span><span class="sxs-lookup"><span data-stu-id="9b9ca-247">Configure deployment credentials for Azure App Service</span></span>](/azure/app-service/app-service-deployment-credentials)
* [<span data-ttu-id="9b9ca-248">Nastavení přípravných prostředí ve službě Azure App Service</span><span class="sxs-lookup"><span data-stu-id="9b9ca-248">Set up staging environments in Azure App Service</span></span>](/azure/app-service/web-sites-staged-publishing)
