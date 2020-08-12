---
title: Nasazení aplikace pro App Service DevOps s využitím ASP.NET Core a Azure
author: CamSoper
description: Nasaďte aplikaci ASP.NET Core do Azure App Service, což je první krok pro DevOps s ASP.NET Core a Azure.
ms.author: casoper
ms.custom: devx-track-csharp, mvc, seodec18
ms.date: 10/24/2018
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: azure/devops/deploy-to-app-service
ms.openlocfilehash: f27c33d692512130ba0a36e9d00d46dffe90da16
ms.sourcegitcommit: ba4872dd5a93780fe6cfacb2711ec1e69e0df92c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/12/2020
ms.locfileid: "88130233"
---
# <a name="deploy-an-app-to-app-service"></a><span data-ttu-id="971fd-103">Nasazení aplikace pro App Service</span><span class="sxs-lookup"><span data-stu-id="971fd-103">Deploy an app to App Service</span></span>

<span data-ttu-id="971fd-104">[Azure App Service](/azure/app-service/) platforma pro hostování webů v Azure.</span><span class="sxs-lookup"><span data-stu-id="971fd-104">[Azure App Service](/azure/app-service/) is Azure's web hosting platform.</span></span> <span data-ttu-id="971fd-105">Nasazení webové aplikace do Azure App Service lze provést ručně nebo pomocí automatizovaného procesu.</span><span class="sxs-lookup"><span data-stu-id="971fd-105">Deploying a web app to Azure App Service can be done manually or by an automated process.</span></span> <span data-ttu-id="971fd-106">Tato část příručky popisuje metody nasazení, které lze aktivovat ručně nebo pomocí skriptu pomocí příkazového řádku nebo aktivované ručně pomocí sady Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="971fd-106">This section of the guide discusses deployment methods that can be triggered manually or by script using the command line, or triggered manually using Visual Studio.</span></span>

<span data-ttu-id="971fd-107">V této části budete provádět následující úlohy:</span><span class="sxs-lookup"><span data-stu-id="971fd-107">In this section, you'll accomplish the following tasks:</span></span>

* <span data-ttu-id="971fd-108">Stáhněte si a sestavte ukázkovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="971fd-108">Download and build the sample app.</span></span>
* <span data-ttu-id="971fd-109">Vytvořte Azure App Service webové aplikace pomocí Azure Cloud Shell.</span><span class="sxs-lookup"><span data-stu-id="971fd-109">Create an Azure App Service Web App using the Azure Cloud Shell.</span></span>
* <span data-ttu-id="971fd-110">Nasaďte ukázkovou aplikaci do Azure pomocí Gitu.</span><span class="sxs-lookup"><span data-stu-id="971fd-110">Deploy the sample app to Azure using Git.</span></span>
* <span data-ttu-id="971fd-111">Nasaďte do aplikace změnu pomocí sady Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="971fd-111">Deploy a change to the app using Visual Studio.</span></span>
* <span data-ttu-id="971fd-112">Přidejte do webové aplikace pracovní slot.</span><span class="sxs-lookup"><span data-stu-id="971fd-112">Add a staging slot to the web app.</span></span>
* <span data-ttu-id="971fd-113">Nasaďte aktualizaci do přípravného slotu.</span><span class="sxs-lookup"><span data-stu-id="971fd-113">Deploy an update to the staging slot.</span></span>
* <span data-ttu-id="971fd-114">Prohoďte přípravný a produkční slot.</span><span class="sxs-lookup"><span data-stu-id="971fd-114">Swap the staging and production slots.</span></span>

## <a name="download-and-test-the-app"></a><span data-ttu-id="971fd-115">Stažení a otestování aplikace</span><span class="sxs-lookup"><span data-stu-id="971fd-115">Download and test the app</span></span>

<span data-ttu-id="971fd-116">Aplikace použitá v tomto průvodci je předem vytvořená aplikace ASP.NET Core, [Jednoduchá čtečka kanálů](https://github.com/Azure-Samples/simple-feed-reader/).</span><span class="sxs-lookup"><span data-stu-id="971fd-116">The app used in this guide is a pre-built ASP.NET Core app, [Simple Feed Reader](https://github.com/Azure-Samples/simple-feed-reader/).</span></span> <span data-ttu-id="971fd-117">Jedná se o Razor stránku aplikace, která používá `Microsoft.SyndicationFeed.ReaderWriter` rozhraní API k načtení informačního kanálu RSS/Atom a zobrazení položek zpráv v seznamu.</span><span class="sxs-lookup"><span data-stu-id="971fd-117">It's a Razor Pages app that uses the `Microsoft.SyndicationFeed.ReaderWriter` API to retrieve an RSS/Atom feed and display the news items in a list.</span></span>

<span data-ttu-id="971fd-118">Tento kód si můžete prohlédnout, ale je důležité pochopit, že tato aplikace nemá nic zvláštní.</span><span class="sxs-lookup"><span data-stu-id="971fd-118">Feel free to review the code, but it's important to understand that there's nothing special about this app.</span></span> <span data-ttu-id="971fd-119">Je to jenom jednoduchá ASP.NET Core aplikace pro ilustrativní účely.</span><span class="sxs-lookup"><span data-stu-id="971fd-119">It's just a simple ASP.NET Core app for illustrative purposes.</span></span>

<span data-ttu-id="971fd-120">Z příkazového prostředí Stáhněte kód, sestavte projekt a spusťte jej následujícím způsobem.</span><span class="sxs-lookup"><span data-stu-id="971fd-120">From a command shell, download the code, build the project, and run it as follows.</span></span>

> <span data-ttu-id="971fd-121">*Poznámka: uživatelé systému Linux/macOS by měli provádět vhodné změny cest, např. pomocí lomítka ( `/` ) místo zpětného lomítka ( `\` ).*</span><span class="sxs-lookup"><span data-stu-id="971fd-121">*Note: Linux/macOS users should make appropriate changes for paths, e.g., using forward slash (`/`) rather than back slash (`\`).*</span></span>

1. <span data-ttu-id="971fd-122">Naklonujte kód do složky na místním počítači.</span><span class="sxs-lookup"><span data-stu-id="971fd-122">Clone the code to a folder on your local machine.</span></span>

    ```console
    git clone https://github.com/Azure-Samples/simple-feed-reader/
    ```

2. <span data-ttu-id="971fd-123">Změňte pracovní složku na vytvořenou složku s *jednoduchým kanálem pro čtení* .</span><span class="sxs-lookup"><span data-stu-id="971fd-123">Change your working folder to the *simple-feed-reader* folder that was created.</span></span>

    ```console
    cd .\simple-feed-reader\SimpleFeedReader
    ```

3. <span data-ttu-id="971fd-124">Obnovte balíčky a sestavte řešení.</span><span class="sxs-lookup"><span data-stu-id="971fd-124">Restore the packages, and build the solution.</span></span>

    ```dotnetcli
    dotnet build
    ```

4. <span data-ttu-id="971fd-125">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="971fd-125">Run the app.</span></span>

    ```dotnetcli
    dotnet run
    ```

    ![Příkaz dotnet Run je úspěšný.](./media/deploying-to-app-service/dotnet-run.png)

5. <span data-ttu-id="971fd-127">Otevřete prohlížeč a přejděte na adresu `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="971fd-127">Open a browser and navigate to `http://localhost:5000`.</span></span> <span data-ttu-id="971fd-128">Aplikace umožňuje zadat nebo vložit adresu URL informačního kanálu syndikace a zobrazit seznam položek zpráv.</span><span class="sxs-lookup"><span data-stu-id="971fd-128">The app allows you to type or paste a syndication feed URL and view a list of news items.</span></span>

     ![Aplikace, která zobrazuje obsah informačního kanálu RSS](./media/deploying-to-app-service/app-in-browser.png)

6. <span data-ttu-id="971fd-130">Jakmile budete spokojeni s tím, že aplikace funguje správně, vypněte ji stisknutím **kombinace kláves CTRL +** + **C** v příkazovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="971fd-130">Once you're satisfied the app is working correctly, shut it down by pressing **Ctrl**+**C** in the command shell.</span></span>

## <a name="create-the-azure-app-service-web-app"></a><span data-ttu-id="971fd-131">Vytvoření webové aplikace v Azure App Service</span><span class="sxs-lookup"><span data-stu-id="971fd-131">Create the Azure App Service Web App</span></span>

<span data-ttu-id="971fd-132">K nasazení aplikace budete muset vytvořit [webovou aplikaci](/azure/app-service/app-service-web-overview)App Service.</span><span class="sxs-lookup"><span data-stu-id="971fd-132">To deploy the app, you'll need to create an App Service [Web App](/azure/app-service/app-service-web-overview).</span></span> <span data-ttu-id="971fd-133">Po vytvoření webové aplikace ji nasadíte z místního počítače pomocí Gitu.</span><span class="sxs-lookup"><span data-stu-id="971fd-133">After creation of the Web App, you'll deploy to it from your local machine using Git.</span></span>

1. <span data-ttu-id="971fd-134">Přihlaste se k [Azure Cloud Shell](https://shell.azure.com/bash).</span><span class="sxs-lookup"><span data-stu-id="971fd-134">Sign in to the [Azure Cloud Shell](https://shell.azure.com/bash).</span></span> <span data-ttu-id="971fd-135">Poznámka: když se poprvé přihlásíte, Cloud Shell se zobrazí výzva k vytvoření účtu úložiště pro konfigurační soubory.</span><span class="sxs-lookup"><span data-stu-id="971fd-135">Note: When you sign in for the first time, Cloud Shell prompts to create a storage account for configuration files.</span></span> <span data-ttu-id="971fd-136">Přijměte výchozí hodnoty nebo zadejte jedinečný název.</span><span class="sxs-lookup"><span data-stu-id="971fd-136">Accept the defaults or provide a unique name.</span></span>

2. <span data-ttu-id="971fd-137">Následující postup použijte Cloud Shell.</span><span class="sxs-lookup"><span data-stu-id="971fd-137">Use the Cloud Shell for the following steps.</span></span>

    <span data-ttu-id="971fd-138">a.</span><span class="sxs-lookup"><span data-stu-id="971fd-138">a.</span></span> <span data-ttu-id="971fd-139">Deklarujte proměnnou pro uložení názvu vaší webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="971fd-139">Declare a variable to store your web app's name.</span></span> <span data-ttu-id="971fd-140">Název musí být jedinečný, aby bylo možné použít výchozí adresu URL.</span><span class="sxs-lookup"><span data-stu-id="971fd-140">The name must be unique to be used in the default URL.</span></span> <span data-ttu-id="971fd-141">Použití `$RANDOM` funkce bash k vytvoření názvu garantuje jedinečnost a výsledky ve formátu `webappname99999` .</span><span class="sxs-lookup"><span data-stu-id="971fd-141">Using the `$RANDOM` Bash function to construct the name guarantees uniqueness and results in the format `webappname99999`.</span></span>

    ```console
    webappname=mywebapp$RANDOM
    ```

    <span data-ttu-id="971fd-142">b.</span><span class="sxs-lookup"><span data-stu-id="971fd-142">b.</span></span> <span data-ttu-id="971fd-143">Vytvořte skupinu prostředků.</span><span class="sxs-lookup"><span data-stu-id="971fd-143">Create a resource group.</span></span> <span data-ttu-id="971fd-144">Skupiny prostředků poskytují prostředky pro agregaci prostředků Azure, které se mají spravovat jako skupina.</span><span class="sxs-lookup"><span data-stu-id="971fd-144">Resource groups provide a means to aggregate Azure resources to be managed as a group.</span></span>

    ```azurecli
    az group create --location centralus --name AzureTutorial
    ```

    <span data-ttu-id="971fd-145">`az`Příkaz vyvolá [Azure CLI](/cli/azure/).</span><span class="sxs-lookup"><span data-stu-id="971fd-145">The `az` command invokes the [Azure CLI](/cli/azure/).</span></span> <span data-ttu-id="971fd-146">Rozhraní příkazového řádku lze spustit místně, ale jeho použití v Cloud Shell šetří čas a konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="971fd-146">The CLI can be run locally, but using it in the Cloud Shell saves time and configuration.</span></span>

    <span data-ttu-id="971fd-147">c.</span><span class="sxs-lookup"><span data-stu-id="971fd-147">c.</span></span> <span data-ttu-id="971fd-148">Vytvořte plán App Service v úrovni S1.</span><span class="sxs-lookup"><span data-stu-id="971fd-148">Create an App Service plan in the S1 tier.</span></span> <span data-ttu-id="971fd-149">Plán App Service je seskupení webových aplikací, které sdílejí stejnou cenovou úroveň.</span><span class="sxs-lookup"><span data-stu-id="971fd-149">An App Service plan is a grouping of web apps that share the same pricing tier.</span></span> <span data-ttu-id="971fd-150">Vrstva S1 není volná, ale vyžaduje se pro funkci přípravného slotu.</span><span class="sxs-lookup"><span data-stu-id="971fd-150">The S1 tier isn't free, but it's required for the staging slots feature.</span></span>

    ```azurecli
    az appservice plan create --name $webappname --resource-group AzureTutorial --sku S1
    ```

    <span data-ttu-id="971fd-151">d.</span><span class="sxs-lookup"><span data-stu-id="971fd-151">d.</span></span> <span data-ttu-id="971fd-152">Vytvořte prostředek webové aplikace pomocí App Serviceho plánu ve stejné skupině prostředků.</span><span class="sxs-lookup"><span data-stu-id="971fd-152">Create the web app resource using the App Service plan in the same resource group.</span></span>

    ```azurecli
    az webapp create --name $webappname --resource-group AzureTutorial --plan $webappname
    ```

    <span data-ttu-id="971fd-153">e.</span><span class="sxs-lookup"><span data-stu-id="971fd-153">e.</span></span> <span data-ttu-id="971fd-154">Nastavte přihlašovací údaje pro nasazení.</span><span class="sxs-lookup"><span data-stu-id="971fd-154">Set the deployment credentials.</span></span> <span data-ttu-id="971fd-155">Tyto přihlašovací údaje pro nasazení platí pro všechny webové aplikace v rámci vašeho předplatného.</span><span class="sxs-lookup"><span data-stu-id="971fd-155">These deployment credentials apply to all the web apps in your subscription.</span></span> <span data-ttu-id="971fd-156">V uživatelském jméně Nepoužívejte speciální znaky.</span><span class="sxs-lookup"><span data-stu-id="971fd-156">Don't use special characters in the user name.</span></span>

    ```azurecli
    az webapp deployment user set --user-name REPLACE_WITH_USER_NAME --password REPLACE_WITH_PASSWORD
    ```

    <span data-ttu-id="971fd-157">f.</span><span class="sxs-lookup"><span data-stu-id="971fd-157">f.</span></span> <span data-ttu-id="971fd-158">Nakonfigurujte webovou aplikaci tak, aby přijímala nasazení z místního Gitu a zobrazila *adresu URL nasazení Git*.</span><span class="sxs-lookup"><span data-stu-id="971fd-158">Configure the web app to accept deployments from local Git and display the *Git deployment URL*.</span></span> <span data-ttu-id="971fd-159">**Poznamenejte si tuto adresu URL pro referenci později**.</span><span class="sxs-lookup"><span data-stu-id="971fd-159">**Note this URL for reference later**.</span></span>

    ```azurecli
    echo Git deployment URL: $(az webapp deployment source config-local-git --name $webappname --resource-group AzureTutorial --query url --output tsv)
    ```

    <span data-ttu-id="971fd-160">například</span><span class="sxs-lookup"><span data-stu-id="971fd-160">g.</span></span> <span data-ttu-id="971fd-161">Zobrazí *adresu URL webové aplikace*.</span><span class="sxs-lookup"><span data-stu-id="971fd-161">Display the *web app URL*.</span></span> <span data-ttu-id="971fd-162">Pokud chcete zobrazit prázdnou webovou aplikaci, přejděte na tuto adresu URL.</span><span class="sxs-lookup"><span data-stu-id="971fd-162">Browse to this URL to see the blank web app.</span></span> <span data-ttu-id="971fd-163">**Poznamenejte si tuto adresu URL pro referenci později**.</span><span class="sxs-lookup"><span data-stu-id="971fd-163">**Note this URL for reference later**.</span></span>

    ```console
    echo Web app URL: http://$webappname.azurewebsites.net
    ```

3. <span data-ttu-id="971fd-164">Pomocí příkazového prostředí v místním počítači přejděte do složky projektu webové aplikace (například `.\simple-feed-reader\SimpleFeedReader` ).</span><span class="sxs-lookup"><span data-stu-id="971fd-164">Using a command shell on your local machine, navigate to the web app's project folder (for example, `.\simple-feed-reader\SimpleFeedReader`).</span></span> <span data-ttu-id="971fd-165">Spusťte následující příkazy a nastavte Git tak, aby se nastavila na adresu URL nasazení:</span><span class="sxs-lookup"><span data-stu-id="971fd-165">Execute the following commands to set up Git to push to the deployment URL:</span></span>

    <span data-ttu-id="971fd-166">a.</span><span class="sxs-lookup"><span data-stu-id="971fd-166">a.</span></span> <span data-ttu-id="971fd-167">Přidejte vzdálenou adresu URL do místního úložiště.</span><span class="sxs-lookup"><span data-stu-id="971fd-167">Add the remote URL to the local repository.</span></span>

    ```console
    git remote add azure-prod GIT_DEPLOYMENT_URL
    ```

    <span data-ttu-id="971fd-168">b.</span><span class="sxs-lookup"><span data-stu-id="971fd-168">b.</span></span> <span data-ttu-id="971fd-169">Vložení místní *Hlavní* větve do *hlavní* větve *Azure-prod* Remote 's.</span><span class="sxs-lookup"><span data-stu-id="971fd-169">Push the local *master* branch to the *azure-prod* remote's *master* branch.</span></span>

    ```console
    git push azure-prod master
    ```

    <span data-ttu-id="971fd-170">Zobrazí se výzva k zadání přihlašovacích údajů pro nasazení, které jste vytvořili dříve.</span><span class="sxs-lookup"><span data-stu-id="971fd-170">You'll be prompted for the deployment credentials you created earlier.</span></span> <span data-ttu-id="971fd-171">Sledujte výstup v příkazovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="971fd-171">Observe the output in the command shell.</span></span> <span data-ttu-id="971fd-172">Azure vytvoří aplikaci ASP.NET Core vzdáleně.</span><span class="sxs-lookup"><span data-stu-id="971fd-172">Azure builds the ASP.NET Core app remotely.</span></span>

4. <span data-ttu-id="971fd-173">V prohlížeči přejděte na *adresu URL webové aplikace* a Všimněte si, že je aplikace sestavená a nasazená.</span><span class="sxs-lookup"><span data-stu-id="971fd-173">In a browser, navigate to the *Web app URL* and note the app has been built and deployed.</span></span> <span data-ttu-id="971fd-174">Další změny můžete zapsat do místního úložiště Git pomocí `git commit` .</span><span class="sxs-lookup"><span data-stu-id="971fd-174">Additional changes can be committed to the local Git repository with `git commit`.</span></span> <span data-ttu-id="971fd-175">Tyto změny se vloží do Azure pomocí předchozího `git push` příkazu.</span><span class="sxs-lookup"><span data-stu-id="971fd-175">These changes are pushed to Azure with the preceding `git push` command.</span></span>

## <a name="deployment-with-visual-studio"></a><span data-ttu-id="971fd-176">Nasazení pomocí sady Visual Studio</span><span class="sxs-lookup"><span data-stu-id="971fd-176">Deployment with Visual Studio</span></span>

> <span data-ttu-id="971fd-177">*Poznámka: Tato část se vztahuje pouze na systém Windows. Uživatelé Linux a macOS by měli provést změnu popsanou v kroku 2 níže. Uložte soubor a potvrďte změnu v místním úložišti pomocí `git commit` . Nakonec nahrajte změnu pomocí `git push` , jako v první části.*</span><span class="sxs-lookup"><span data-stu-id="971fd-177">*Note: This section applies to Windows only. Linux and macOS users should make the change described in step 2 below. Save the file, and commit the change to the local repository with `git commit`. Finally, push the change with `git push`, as in the first section.*</span></span>

<span data-ttu-id="971fd-178">Aplikace už je nasazená z příkazového prostředí.</span><span class="sxs-lookup"><span data-stu-id="971fd-178">The app has already been deployed from the command shell.</span></span> <span data-ttu-id="971fd-179">Pojďme k nasazení aktualizace do aplikace použít integrované nástroje sady Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="971fd-179">Let's use Visual Studio's integrated tools to deploy an update to the app.</span></span> <span data-ttu-id="971fd-180">Aplikace Visual Studio na pozadí dosáhne stejného účelu jako nástroj příkazového řádku, ale ve známém uživatelském rozhraní sady Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="971fd-180">Behind the scenes, Visual Studio accomplishes the same thing as the command line tooling, but within Visual Studio's familiar UI.</span></span>

1. <span data-ttu-id="971fd-181">Otevřete *SimpleFeedReader. sln* v aplikaci Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="971fd-181">Open *SimpleFeedReader.sln* in Visual Studio.</span></span>
2. <span data-ttu-id="971fd-182">V Průzkumník řešení otevřete *Pages\Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="971fd-182">In Solution Explorer, open *Pages\Index.cshtml*.</span></span> <span data-ttu-id="971fd-183">Změňte `<h2>Simple Feed Reader</h2>` na `<h2>Simple Feed Reader - V2</h2>` .</span><span class="sxs-lookup"><span data-stu-id="971fd-183">Change `<h2>Simple Feed Reader</h2>` to `<h2>Simple Feed Reader - V2</h2>`.</span></span>
3. <span data-ttu-id="971fd-184">Stisknutím **kombinace kláves CTRL** + **+ SHIFT** + **B** sestavíte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="971fd-184">Press **Ctrl**+**Shift**+**B** to build the app.</span></span>
4. <span data-ttu-id="971fd-185">V Průzkumník řešení klikněte pravým tlačítkem na projekt a klikněte na **publikovat**.</span><span class="sxs-lookup"><span data-stu-id="971fd-185">In Solution Explorer, right-click on the project and click **Publish**.</span></span>

    ![Snímek obrazovky, který ukazuje kliknutí pravým tlačítkem, publikování](./media/deploying-to-app-service/publish.png)
5. <span data-ttu-id="971fd-187">Visual Studio může vytvořit nový prostředek App Service, ale tato aktualizace se publikuje v rámci stávajícího nasazení.</span><span class="sxs-lookup"><span data-stu-id="971fd-187">Visual Studio can create a new App Service resource, but this update will be published over the existing deployment.</span></span> <span data-ttu-id="971fd-188">V dialogovém okně **vybrat cíl publikování** vyberte v seznamu na levé straně položku **App Service** a pak vyberte **Vybrat existující**.</span><span class="sxs-lookup"><span data-stu-id="971fd-188">In the **Pick a publish target** dialog, select **App Service** from the list on the left, and then select **Select Existing**.</span></span> <span data-ttu-id="971fd-189">Klikněte na **Publikovat**.</span><span class="sxs-lookup"><span data-stu-id="971fd-189">Click **Publish**.</span></span>
6. <span data-ttu-id="971fd-190">V dialogovém okně **App Service** potvrďte, že se v pravém horním rohu zobrazuje účet Microsoft nebo organizace, který se používá k vytvoření vašeho předplatného Azure.</span><span class="sxs-lookup"><span data-stu-id="971fd-190">In the **App Service** dialog, confirm that the Microsoft or Organizational account used to create your Azure subscription is displayed in the upper right.</span></span> <span data-ttu-id="971fd-191">Pokud není, klikněte na rozevírací nabídku a přidejte ji.</span><span class="sxs-lookup"><span data-stu-id="971fd-191">If it's not, click the drop-down and add it.</span></span>
7. <span data-ttu-id="971fd-192">Zkontrolujte, jestli je vybrané správné **předplatné** Azure.</span><span class="sxs-lookup"><span data-stu-id="971fd-192">Confirm that the correct Azure **Subscription** is selected.</span></span> <span data-ttu-id="971fd-193">V **zobrazení**vyberte **Skupina prostředků**.</span><span class="sxs-lookup"><span data-stu-id="971fd-193">For **View**, select **Resource Group**.</span></span> <span data-ttu-id="971fd-194">Rozbalte skupinu prostředků **AzureTutorial** a pak vyberte existující webovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="971fd-194">Expand the **AzureTutorial** resource group and then select the existing web app.</span></span> <span data-ttu-id="971fd-195">Klikněte na **OK**.</span><span class="sxs-lookup"><span data-stu-id="971fd-195">Click **OK**.</span></span>

    ![Snímek obrazovky s dialogem App Service pro publikování](./media/deploying-to-app-service/publish-dialog.png)

<span data-ttu-id="971fd-197">Visual Studio sestaví a nasadí aplikaci do Azure.</span><span class="sxs-lookup"><span data-stu-id="971fd-197">Visual Studio builds and deploys the app to Azure.</span></span> <span data-ttu-id="971fd-198">Přejděte na adresu URL webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="971fd-198">Browse to the web app URL.</span></span> <span data-ttu-id="971fd-199">Ověřte, zda `<h2>` je úprava prvku živá.</span><span class="sxs-lookup"><span data-stu-id="971fd-199">Validate that the `<h2>` element modification is live.</span></span>

![Aplikace se změněným názvem](./media/deploying-to-app-service/app-v2.png)

## <a name="deployment-slots"></a><span data-ttu-id="971fd-201">Nasazovací sloty</span><span class="sxs-lookup"><span data-stu-id="971fd-201">Deployment slots</span></span>

<span data-ttu-id="971fd-202">Sloty nasazení podporují přípravu změn bez ovlivnění aplikace spuštěné v produkčním prostředí.</span><span class="sxs-lookup"><span data-stu-id="971fd-202">Deployment slots support the staging of changes without impacting the app running in production.</span></span> <span data-ttu-id="971fd-203">Jakmile se dvoufázové verze aplikace ověří týmem zabezpečování kvality, produkční a přípravné sloty je možné prohodit.</span><span class="sxs-lookup"><span data-stu-id="971fd-203">Once the staged version of the app is validated by a quality assurance team, the production and staging slots can be swapped.</span></span> <span data-ttu-id="971fd-204">Aplikace v pracovním prostředí se tímto způsobem převýší na produkční prostředí.</span><span class="sxs-lookup"><span data-stu-id="971fd-204">The app in staging is promoted to production in this manner.</span></span> <span data-ttu-id="971fd-205">V následujících krocích se vytvoří přípravný slot, nasadí se nějaké změny a pracovní slot se po ověření zahodí k produkci.</span><span class="sxs-lookup"><span data-stu-id="971fd-205">The following steps create a staging slot, deploy some changes to it, and swap the staging slot with production after verification.</span></span>

1. <span data-ttu-id="971fd-206">Přihlaste se k [Azure Cloud Shell](https://shell.azure.com/bash), pokud ještě není přihlášený.</span><span class="sxs-lookup"><span data-stu-id="971fd-206">Sign in to the [Azure Cloud Shell](https://shell.azure.com/bash), if not already signed in.</span></span>
2. <span data-ttu-id="971fd-207">Vytvořte pracovní slot.</span><span class="sxs-lookup"><span data-stu-id="971fd-207">Create the staging slot.</span></span>

    <span data-ttu-id="971fd-208">a.</span><span class="sxs-lookup"><span data-stu-id="971fd-208">a.</span></span> <span data-ttu-id="971fd-209">Vytvořte slot nasazení s názvem *Příprava*.</span><span class="sxs-lookup"><span data-stu-id="971fd-209">Create a deployment slot with the name *staging*.</span></span>

    ```azurecli
    az webapp deployment slot create --name $webappname --resource-group AzureTutorial --slot staging
    ```

    <span data-ttu-id="971fd-210">b.</span><span class="sxs-lookup"><span data-stu-id="971fd-210">b.</span></span> <span data-ttu-id="971fd-211">Nakonfigurujte pracovní slot pro použití nasazení z místního Gitu a získejte adresu URL **pracovního** nasazení.</span><span class="sxs-lookup"><span data-stu-id="971fd-211">Configure the staging slot to use deployment from local Git and get the **staging** deployment URL.</span></span> <span data-ttu-id="971fd-212">**Poznamenejte si tuto adresu URL pro referenci později**.</span><span class="sxs-lookup"><span data-stu-id="971fd-212">**Note this URL for reference later**.</span></span>

    ```azurecli
    echo Git deployment URL for staging: $(az webapp deployment source config-local-git --name $webappname --resource-group AzureTutorial --slot staging --query url --output tsv)
    ```

    <span data-ttu-id="971fd-213">c.</span><span class="sxs-lookup"><span data-stu-id="971fd-213">c.</span></span> <span data-ttu-id="971fd-214">Zobrazit adresu URL přípravného slotu.</span><span class="sxs-lookup"><span data-stu-id="971fd-214">Display the staging slot's URL.</span></span> <span data-ttu-id="971fd-215">Přejděte na adresu URL, abyste viděli prázdný přípravný slot.</span><span class="sxs-lookup"><span data-stu-id="971fd-215">Browse to the URL to see the empty staging slot.</span></span> <span data-ttu-id="971fd-216">**Poznamenejte si tuto adresu URL pro referenci později**.</span><span class="sxs-lookup"><span data-stu-id="971fd-216">**Note this URL for reference later**.</span></span>

    ```console
    echo Staging web app URL: http://$webappname-staging.azurewebsites.net
    ```

3. <span data-ttu-id="971fd-217">V textovém editoru nebo v aplikaci Visual Studio upravte *stránky nebo index. cshtml* tak, aby `<h2>` element četl `<h2>Simple Feed Reader - V3</h2>` a uložil soubor.</span><span class="sxs-lookup"><span data-stu-id="971fd-217">In a text editor or Visual Studio, modify *Pages/Index.cshtml* again so that the `<h2>` element reads `<h2>Simple Feed Reader - V3</h2>` and save the file.</span></span>

4. <span data-ttu-id="971fd-218">Potvrďte soubor do místního úložiště Git, a to pomocí stránky **změny** na kartě *Team Explorer* sady Visual Studio nebo zadáním následujícího příkazu pomocí příkazového prostředí místního počítače:</span><span class="sxs-lookup"><span data-stu-id="971fd-218">Commit the file to the local Git repository, using either the **Changes** page in Visual Studio's *Team Explorer* tab, or by entering the following using the local machine's command shell:</span></span>

    ```console
    git commit -a -m "upgraded to V3"
    ```

5. <span data-ttu-id="971fd-219">Pomocí příkazového prostředí místního počítače přidejte adresu URL pracovního nasazení jako vzdálené úložiště Git a odešlete potvrzené změny:</span><span class="sxs-lookup"><span data-stu-id="971fd-219">Using the local machine's command shell, add the staging deployment URL as a Git remote and push the committed changes:</span></span>

    <span data-ttu-id="971fd-220">a.</span><span class="sxs-lookup"><span data-stu-id="971fd-220">a.</span></span> <span data-ttu-id="971fd-221">Přidejte vzdálenou adresu URL pro přípravu do místního úložiště Git.</span><span class="sxs-lookup"><span data-stu-id="971fd-221">Add the remote URL for staging to the local Git repository.</span></span>

    ```console
    git remote add azure-staging <Git_staging_deployment_URL>
    ```

    <span data-ttu-id="971fd-222">b.</span><span class="sxs-lookup"><span data-stu-id="971fd-222">b.</span></span> <span data-ttu-id="971fd-223">Nahrajte místní *Hlavní* větev do *hlavní* větve vzdálené údržby *Azure* .</span><span class="sxs-lookup"><span data-stu-id="971fd-223">Push the local *master* branch to the *azure-staging* remote's *master* branch.</span></span>

    ```console
    git push azure-staging master
    ```

    <span data-ttu-id="971fd-224">Počkejte, než Azure sestaví a nasadí aplikaci.</span><span class="sxs-lookup"><span data-stu-id="971fd-224">Wait while Azure builds and deploys the app.</span></span>

6. <span data-ttu-id="971fd-225">Pokud chcete ověřit, že je hodnota V3 nasazená do přípravného slotu, otevřete dvě okna prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="971fd-225">To verify that V3 has been deployed to the staging slot, open two browser windows.</span></span> <span data-ttu-id="971fd-226">V jednom okně přejděte na původní adresu URL webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="971fd-226">In one window, navigate to the original web app URL.</span></span> <span data-ttu-id="971fd-227">V druhém okně přejděte na adresu URL pracovní webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="971fd-227">In the other window, navigate to the staging web app URL.</span></span> <span data-ttu-id="971fd-228">Produkční adresa URL slouží jako v2 aplikace.</span><span class="sxs-lookup"><span data-stu-id="971fd-228">The production URL serves V2 of the app.</span></span> <span data-ttu-id="971fd-229">Pracovní adresa URL slouží jako V3 aplikace.</span><span class="sxs-lookup"><span data-stu-id="971fd-229">The staging URL serves V3 of the app.</span></span>

    ![Snímek obrazovky s porovnáním oken prohlížeče](./media/deploying-to-app-service/ready-to-swap.png)

7. <span data-ttu-id="971fd-231">V Cloud Shell Proměňte v produkčním prostředí ověřené/teplé pracovní sloty.</span><span class="sxs-lookup"><span data-stu-id="971fd-231">In the Cloud Shell, swap the verified/warmed-up staging slot into production.</span></span>

    ```azurecli
    az webapp deployment slot swap --name $webappname --resource-group AzureTutorial --slot staging
    ```

8. <span data-ttu-id="971fd-232">Pomocí obnovení dvou oken prohlížeče ověřte, zda došlo k výměně.</span><span class="sxs-lookup"><span data-stu-id="971fd-232">Verify that the swap occurred by refreshing the two browser windows.</span></span>

    ![Porovnání oken prohlížeče po prohození](./media/deploying-to-app-service/swapped.png)

## <a name="summary"></a><span data-ttu-id="971fd-234">Shrnutí</span><span class="sxs-lookup"><span data-stu-id="971fd-234">Summary</span></span>

<span data-ttu-id="971fd-235">V této části byly dokončeny následující úkoly:</span><span class="sxs-lookup"><span data-stu-id="971fd-235">In this section, the following tasks were completed:</span></span>

* <span data-ttu-id="971fd-236">Stáhli a vytvořili jste ukázkovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="971fd-236">Downloaded and built the sample app.</span></span>
* <span data-ttu-id="971fd-237">Vytvořili Azure App Service webovou aplikaci pomocí Azure Cloud Shell.</span><span class="sxs-lookup"><span data-stu-id="971fd-237">Created an Azure App Service Web App using the Azure Cloud Shell.</span></span>
* <span data-ttu-id="971fd-238">Nasadili jste ukázkovou aplikaci do Azure pomocí Gitu.</span><span class="sxs-lookup"><span data-stu-id="971fd-238">Deployed the sample app to Azure using Git.</span></span>
* <span data-ttu-id="971fd-239">Nasadili jste do aplikace změnu pomocí sady Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="971fd-239">Deployed a change to the app using Visual Studio.</span></span>
* <span data-ttu-id="971fd-240">Do webové aplikace se přidal pracovní slot.</span><span class="sxs-lookup"><span data-stu-id="971fd-240">Added a staging slot to the web app.</span></span>
* <span data-ttu-id="971fd-241">Nasazení aktualizace do přípravného slotu.</span><span class="sxs-lookup"><span data-stu-id="971fd-241">Deployed an update to the staging slot.</span></span>
* <span data-ttu-id="971fd-242">Pracovní a produkční sloty se vyměnily.</span><span class="sxs-lookup"><span data-stu-id="971fd-242">Swapped the staging and production slots.</span></span>

<span data-ttu-id="971fd-243">V další části se dozvíte, jak vytvořit kanál DevOps pomocí Azure Pipelines.</span><span class="sxs-lookup"><span data-stu-id="971fd-243">In the next section, you'll learn how to build a DevOps pipeline with Azure Pipelines.</span></span>

## <a name="additional-reading"></a><span data-ttu-id="971fd-244">Další materiály ke čtení</span><span class="sxs-lookup"><span data-stu-id="971fd-244">Additional reading</span></span>

* [<span data-ttu-id="971fd-245">Přehled Web Apps</span><span class="sxs-lookup"><span data-stu-id="971fd-245">Web Apps overview</span></span>](/azure/app-service/app-service-web-overview)
* [<span data-ttu-id="971fd-246">Vytvoření webové aplikace .NET Core využívající SQL Database ve službě Azure App Service</span><span class="sxs-lookup"><span data-stu-id="971fd-246">Build a .NET Core and SQL Database web app in Azure App Service</span></span>](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)
* [<span data-ttu-id="971fd-247">Nakonfigurovat přihlašovací údaje nasazení pro Azure App Service</span><span class="sxs-lookup"><span data-stu-id="971fd-247">Configure deployment credentials for Azure App Service</span></span>](/azure/app-service/app-service-deployment-credentials)
* [<span data-ttu-id="971fd-248">Nastavení přípravných prostředí ve službě Azure App Service</span><span class="sxs-lookup"><span data-stu-id="971fd-248">Set up staging environments in Azure App Service</span></span>](/azure/app-service/web-sites-staged-publishing)
