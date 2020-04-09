---
title: Nasazení aplikace do služby App Service – DevOps s ASP.NET core a Azure
author: CamSoper
description: Nasazení aplikace ASP.NET Core do služby Azure App Service, což je první krok pro DevOps s ASP.NET Core a Azure.
ms.author: casoper
ms.custom: mvc, seodec18
ms.date: 10/24/2018
uid: azure/devops/deploy-to-app-service
ms.openlocfilehash: df41f296e9c4e1eff6e31d45b29ec30ee1e20cf4
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78657743"
---
# <a name="deploy-an-app-to-app-service"></a><span data-ttu-id="8a8e7-103">Nasazení aplikace do služby App Service</span><span class="sxs-lookup"><span data-stu-id="8a8e7-103">Deploy an app to App Service</span></span>

<span data-ttu-id="8a8e7-104">[Azure App Service](/azure/app-service/) je webhostingová platforma Azure.</span><span class="sxs-lookup"><span data-stu-id="8a8e7-104">[Azure App Service](/azure/app-service/) is Azure's web hosting platform.</span></span> <span data-ttu-id="8a8e7-105">Nasazení webové aplikace do služby Azure App Service lze provést ručně nebo automatizovaným procesem.</span><span class="sxs-lookup"><span data-stu-id="8a8e7-105">Deploying a web app to Azure App Service can be done manually or by an automated process.</span></span> <span data-ttu-id="8a8e7-106">Tato část příručky popisuje metody nasazení, které lze aktivovat ručně nebo skriptem pomocí příkazového řádku nebo se aktivují ručně pomocí sady Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="8a8e7-106">This section of the guide discusses deployment methods that can be triggered manually or by script using the command line, or triggered manually using Visual Studio.</span></span>

<span data-ttu-id="8a8e7-107">V této části budete provádět následující úkoly:</span><span class="sxs-lookup"><span data-stu-id="8a8e7-107">In this section, you'll accomplish the following tasks:</span></span>

* <span data-ttu-id="8a8e7-108">Stáhněte a sestavte ukázkovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="8a8e7-108">Download and build the sample app.</span></span>
* <span data-ttu-id="8a8e7-109">Vytvořte webovou aplikaci služby Azure App Service pomocí Azure Cloud Shell.</span><span class="sxs-lookup"><span data-stu-id="8a8e7-109">Create an Azure App Service Web App using the Azure Cloud Shell.</span></span>
* <span data-ttu-id="8a8e7-110">Nasaďte ukázkovou aplikaci do Azure pomocí Gitu.</span><span class="sxs-lookup"><span data-stu-id="8a8e7-110">Deploy the sample app to Azure using Git.</span></span>
* <span data-ttu-id="8a8e7-111">Nasaďte změnu do aplikace pomocí Visual Studia.</span><span class="sxs-lookup"><span data-stu-id="8a8e7-111">Deploy a change to the app using Visual Studio.</span></span>
* <span data-ttu-id="8a8e7-112">Přidejte pracovní slot do webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="8a8e7-112">Add a staging slot to the web app.</span></span>
* <span data-ttu-id="8a8e7-113">Nasaďte aktualizaci do pracovního slotu.</span><span class="sxs-lookup"><span data-stu-id="8a8e7-113">Deploy an update to the staging slot.</span></span>
* <span data-ttu-id="8a8e7-114">Prohoďte přípravný a produkční slot.</span><span class="sxs-lookup"><span data-stu-id="8a8e7-114">Swap the staging and production slots.</span></span>

## <a name="download-and-test-the-app"></a><span data-ttu-id="8a8e7-115">Stažení a testování aplikace</span><span class="sxs-lookup"><span data-stu-id="8a8e7-115">Download and test the app</span></span>

<span data-ttu-id="8a8e7-116">Aplikace použitá v této příručce je předem vytvořená aplikace ASP.NET Core, [Simple Feed Reader](https://github.com/Azure-Samples/simple-feed-reader/).</span><span class="sxs-lookup"><span data-stu-id="8a8e7-116">The app used in this guide is a pre-built ASP.NET Core app, [Simple Feed Reader](https://github.com/Azure-Samples/simple-feed-reader/).</span></span> <span data-ttu-id="8a8e7-117">Je to aplikace Razor Pages, `Microsoft.SyndicationFeed.ReaderWriter` která používá rozhraní API k načtení informačního kanálu RSS/Atom a zobrazení novinek v seznamu.</span><span class="sxs-lookup"><span data-stu-id="8a8e7-117">It's a Razor Pages app that uses the `Microsoft.SyndicationFeed.ReaderWriter` API to retrieve an RSS/Atom feed and display the news items in a list.</span></span>

<span data-ttu-id="8a8e7-118">Nebojte se přečíst kód, ale je důležité si uvědomit, že na této aplikaci není nic zvláštního.</span><span class="sxs-lookup"><span data-stu-id="8a8e7-118">Feel free to review the code, but it's important to understand that there's nothing special about this app.</span></span> <span data-ttu-id="8a8e7-119">Je to jen jednoduchá aplikace ASP.NET Core pro ilustrační účely.</span><span class="sxs-lookup"><span data-stu-id="8a8e7-119">It's just a simple ASP.NET Core app for illustrative purposes.</span></span>

<span data-ttu-id="8a8e7-120">Z příkazového prostředí stáhněte kód, vytvořte projekt a spusťte jej následujícím způsobem.</span><span class="sxs-lookup"><span data-stu-id="8a8e7-120">From a command shell, download the code, build the project, and run it as follows.</span></span>

> <span data-ttu-id="8a8e7-121">*Poznámka: Uživatelé Linuxu/macOS by měli provést příslušné změny pro`/`cesty, například`\`pomocí lomítka ( ) spíše než zpětný lomítko ( ).*</span><span class="sxs-lookup"><span data-stu-id="8a8e7-121">*Note: Linux/macOS users should make appropriate changes for paths, e.g., using forward slash (`/`) rather than back slash (`\`).*</span></span>

1. <span data-ttu-id="8a8e7-122">Klonujte kód do složky v místním počítači.</span><span class="sxs-lookup"><span data-stu-id="8a8e7-122">Clone the code to a folder on your local machine.</span></span>

    ```console
    git clone https://github.com/Azure-Samples/simple-feed-reader/
    ```

2. <span data-ttu-id="8a8e7-123">Změňte pracovní složku na složku *s jednoduchou čtečkou,* která byla vytvořena.</span><span class="sxs-lookup"><span data-stu-id="8a8e7-123">Change your working folder to the *simple-feed-reader* folder that was created.</span></span>

    ```console
    cd .\simple-feed-reader\SimpleFeedReader
    ```

3. <span data-ttu-id="8a8e7-124">Obnovte balíčky a vytvořte řešení.</span><span class="sxs-lookup"><span data-stu-id="8a8e7-124">Restore the packages, and build the solution.</span></span>

    ```dotnetcli
    dotnet build
    ```

4. <span data-ttu-id="8a8e7-125">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="8a8e7-125">Run the app.</span></span>

    ```dotnetcli
    dotnet run
    ```

    ![Příkaz dotnet run je úspěšný.](./media/deploying-to-app-service/dotnet-run.png)

5. <span data-ttu-id="8a8e7-127">Otevřete prohlížeč a přejděte na adresu `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="8a8e7-127">Open a browser and navigate to `http://localhost:5000`.</span></span> <span data-ttu-id="8a8e7-128">Aplikace umožňuje zadat nebo vložit adresu URL zdroje syndikace a zobrazit seznam diskusních příspěvků.</span><span class="sxs-lookup"><span data-stu-id="8a8e7-128">The app allows you to type or paste a syndication feed URL and view a list of news items.</span></span>

     ![Aplikace zobrazující obsah informačního kanálu RSS](./media/deploying-to-app-service/app-in-browser.png)

6. <span data-ttu-id="8a8e7-130">Jakmile jste spokojeni, že aplikace funguje správně, vypněte ji stisknutím **ctrl**+**c** v příkazovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="8a8e7-130">Once you're satisfied the app is working correctly, shut it down by pressing **Ctrl**+**C** in the command shell.</span></span>

## <a name="create-the-azure-app-service-web-app"></a><span data-ttu-id="8a8e7-131">Vytvoření webové aplikace Služby aplikací Azure</span><span class="sxs-lookup"><span data-stu-id="8a8e7-131">Create the Azure App Service Web App</span></span>

<span data-ttu-id="8a8e7-132">Chcete-li aplikaci nasadit, budete muset vytvořit [webovou aplikaci](/azure/app-service/app-service-web-overview)služby App Service .</span><span class="sxs-lookup"><span data-stu-id="8a8e7-132">To deploy the app, you'll need to create an App Service [Web App](/azure/app-service/app-service-web-overview).</span></span> <span data-ttu-id="8a8e7-133">Po vytvoření webové aplikace se do ní nasadíte z místního počítače pomocí Gitu.</span><span class="sxs-lookup"><span data-stu-id="8a8e7-133">After creation of the Web App, you'll deploy to it from your local machine using Git.</span></span>

1. <span data-ttu-id="8a8e7-134">Přihlaste se do [prostředí Azure Cloud Shell](https://shell.azure.com/bash).</span><span class="sxs-lookup"><span data-stu-id="8a8e7-134">Sign in to the [Azure Cloud Shell](https://shell.azure.com/bash).</span></span> <span data-ttu-id="8a8e7-135">Poznámka: Při prvním přihlášení cloudové prostředí vyzve k vytvoření účtu úložiště pro konfigurační soubory.</span><span class="sxs-lookup"><span data-stu-id="8a8e7-135">Note: When you sign in for the first time, Cloud Shell prompts to create a storage account for configuration files.</span></span> <span data-ttu-id="8a8e7-136">Přijměte výchozí hodnoty nebo zadejte jedinečný název.</span><span class="sxs-lookup"><span data-stu-id="8a8e7-136">Accept the defaults or provide a unique name.</span></span>

2. <span data-ttu-id="8a8e7-137">Pro následující kroky použijte prostředí Cloud Shell.</span><span class="sxs-lookup"><span data-stu-id="8a8e7-137">Use the Cloud Shell for the following steps.</span></span>

    <span data-ttu-id="8a8e7-138">a.</span><span class="sxs-lookup"><span data-stu-id="8a8e7-138">a.</span></span> <span data-ttu-id="8a8e7-139">Deklarujte proměnnou pro uložení názvu webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="8a8e7-139">Declare a variable to store your web app's name.</span></span> <span data-ttu-id="8a8e7-140">Název musí být jedinečný, aby mohl být použit ve výchozí adrese URL.</span><span class="sxs-lookup"><span data-stu-id="8a8e7-140">The name must be unique to be used in the default URL.</span></span> <span data-ttu-id="8a8e7-141">Použití `$RANDOM` Bash funkce k vytvoření názvu zaručuje jedinečnost `webappname99999`a výsledky ve formátu .</span><span class="sxs-lookup"><span data-stu-id="8a8e7-141">Using the `$RANDOM` Bash function to construct the name guarantees uniqueness and results in the format `webappname99999`.</span></span>

    ```console
    webappname=mywebapp$RANDOM
    ```

    <span data-ttu-id="8a8e7-142">b.</span><span class="sxs-lookup"><span data-stu-id="8a8e7-142">b.</span></span> <span data-ttu-id="8a8e7-143">Vytvořte skupinu prostředků.</span><span class="sxs-lookup"><span data-stu-id="8a8e7-143">Create a resource group.</span></span> <span data-ttu-id="8a8e7-144">Skupiny prostředků poskytují prostředky k agregaci prostředků Azure, které mají být spravovány jako skupina.</span><span class="sxs-lookup"><span data-stu-id="8a8e7-144">Resource groups provide a means to aggregate Azure resources to be managed as a group.</span></span>

    ```azure-cli
    az group create --location centralus --name AzureTutorial
    ```

    <span data-ttu-id="8a8e7-145">Příkaz `az` vyvolá [příkaz cli Azure](/cli/azure/).</span><span class="sxs-lookup"><span data-stu-id="8a8e7-145">The `az` command invokes the [Azure CLI](/cli/azure/).</span></span> <span data-ttu-id="8a8e7-146">ClI lze spustit místně, ale jeho použití v prostředí Cloud Šetří čas a konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="8a8e7-146">The CLI can be run locally, but using it in the Cloud Shell saves time and configuration.</span></span>

    <span data-ttu-id="8a8e7-147">c.</span><span class="sxs-lookup"><span data-stu-id="8a8e7-147">c.</span></span> <span data-ttu-id="8a8e7-148">Vytvořte plán služby App Service ve vrstvě S1.</span><span class="sxs-lookup"><span data-stu-id="8a8e7-148">Create an App Service plan in the S1 tier.</span></span> <span data-ttu-id="8a8e7-149">Plán služby App Service je seskupení webových aplikací, které sdílejí stejnou cenovou úroveň.</span><span class="sxs-lookup"><span data-stu-id="8a8e7-149">An App Service plan is a grouping of web apps that share the same pricing tier.</span></span> <span data-ttu-id="8a8e7-150">Úroveň S1 není zdarma, ale je vyžadována pro funkci pracovních slotů.</span><span class="sxs-lookup"><span data-stu-id="8a8e7-150">The S1 tier isn't free, but it's required for the staging slots feature.</span></span>

    ```azure-cli
    az appservice plan create --name $webappname --resource-group AzureTutorial --sku S1
    ```

    <span data-ttu-id="8a8e7-151">d.</span><span class="sxs-lookup"><span data-stu-id="8a8e7-151">d.</span></span> <span data-ttu-id="8a8e7-152">Vytvořte prostředek webové aplikace pomocí plánu služby App Service ve stejné skupině prostředků.</span><span class="sxs-lookup"><span data-stu-id="8a8e7-152">Create the web app resource using the App Service plan in the same resource group.</span></span>

    ```azure-cli
    az webapp create --name $webappname --resource-group AzureTutorial --plan $webappname
    ```

    <span data-ttu-id="8a8e7-153">e.</span><span class="sxs-lookup"><span data-stu-id="8a8e7-153">e.</span></span> <span data-ttu-id="8a8e7-154">Nastavte přihlašovací údaje nasazení.</span><span class="sxs-lookup"><span data-stu-id="8a8e7-154">Set the deployment credentials.</span></span> <span data-ttu-id="8a8e7-155">Tato pověření nasazení platí pro všechny webové aplikace ve vašem předplatném.</span><span class="sxs-lookup"><span data-stu-id="8a8e7-155">These deployment credentials apply to all the web apps in your subscription.</span></span> <span data-ttu-id="8a8e7-156">V uživatelském jménu nepoužívejte speciální znaky.</span><span class="sxs-lookup"><span data-stu-id="8a8e7-156">Don't use special characters in the user name.</span></span>

    ```azure-cli
    az webapp deployment user set --user-name REPLACE_WITH_USER_NAME --password REPLACE_WITH_PASSWORD
    ```

    <span data-ttu-id="8a8e7-157">f.</span><span class="sxs-lookup"><span data-stu-id="8a8e7-157">f.</span></span> <span data-ttu-id="8a8e7-158">Nakonfigurujte webovou aplikaci tak, aby přijímala nasazení z místního Gitu a zobrazovala *adresu URL nasazení Gitu*.</span><span class="sxs-lookup"><span data-stu-id="8a8e7-158">Configure the web app to accept deployments from local Git and display the *Git deployment URL*.</span></span> <span data-ttu-id="8a8e7-159">**Poznámka: Tato adresa URL pro pozdější odkaz**.</span><span class="sxs-lookup"><span data-stu-id="8a8e7-159">**Note this URL for reference later**.</span></span>

    ```azure-cli
    echo Git deployment URL: $(az webapp deployment source config-local-git --name $webappname --resource-group AzureTutorial --query url --output tsv)
    ```

    <span data-ttu-id="8a8e7-160">g.</span><span class="sxs-lookup"><span data-stu-id="8a8e7-160">g.</span></span> <span data-ttu-id="8a8e7-161">Zobrazí *adresu URL webové aplikace*.</span><span class="sxs-lookup"><span data-stu-id="8a8e7-161">Display the *web app URL*.</span></span> <span data-ttu-id="8a8e7-162">Přejděte na tuto adresu URL a podívejte se na prázdnou webovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="8a8e7-162">Browse to this URL to see the blank web app.</span></span> <span data-ttu-id="8a8e7-163">**Poznámka: Tato adresa URL pro pozdější odkaz**.</span><span class="sxs-lookup"><span data-stu-id="8a8e7-163">**Note this URL for reference later**.</span></span>

    ```console
    echo Web app URL: http://$webappname.azurewebsites.net
    ```

3. <span data-ttu-id="8a8e7-164">Pomocí příkazového prostředí v místním počítači přejděte do složky `.\simple-feed-reader\SimpleFeedReader`projektu webové aplikace (například).</span><span class="sxs-lookup"><span data-stu-id="8a8e7-164">Using a command shell on your local machine, navigate to the web app's project folder (for example, `.\simple-feed-reader\SimpleFeedReader`).</span></span> <span data-ttu-id="8a8e7-165">Chcete-li nastavit Git tak, aby se přemisťuje na adresu URL nasazení, proveďte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="8a8e7-165">Execute the following commands to set up Git to push to the deployment URL:</span></span>

    <span data-ttu-id="8a8e7-166">a.</span><span class="sxs-lookup"><span data-stu-id="8a8e7-166">a.</span></span> <span data-ttu-id="8a8e7-167">Přidejte vzdálenou adresu URL do místního úložiště.</span><span class="sxs-lookup"><span data-stu-id="8a8e7-167">Add the remote URL to the local repository.</span></span>

    ```console
    git remote add azure-prod GIT_DEPLOYMENT_URL
    ```

    <span data-ttu-id="8a8e7-168">b.</span><span class="sxs-lookup"><span data-stu-id="8a8e7-168">b.</span></span> <span data-ttu-id="8a8e7-169">Posuňte místní *hlavní* větev do *hlavní* větve *vzdáleného zařízení azure-prod.*</span><span class="sxs-lookup"><span data-stu-id="8a8e7-169">Push the local *master* branch to the *azure-prod* remote's *master* branch.</span></span>

    ```console
    git push azure-prod master
    ```

    <span data-ttu-id="8a8e7-170">Budete vyzváni k zadání přihlašovacích údajů pro nasazení, které jste vytvořili dříve.</span><span class="sxs-lookup"><span data-stu-id="8a8e7-170">You'll be prompted for the deployment credentials you created earlier.</span></span> <span data-ttu-id="8a8e7-171">Sledujte výstup v příkazovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="8a8e7-171">Observe the output in the command shell.</span></span> <span data-ttu-id="8a8e7-172">Azure staví aplikaci ASP.NET Core na dálku.</span><span class="sxs-lookup"><span data-stu-id="8a8e7-172">Azure builds the ASP.NET Core app remotely.</span></span>

4. <span data-ttu-id="8a8e7-173">V prohlížeči přejděte na *adresu URL webové aplikace* a všimněte si, že aplikace byla vytvořena a nasazena.</span><span class="sxs-lookup"><span data-stu-id="8a8e7-173">In a browser, navigate to the *Web app URL* and note the app has been built and deployed.</span></span> <span data-ttu-id="8a8e7-174">Další změny mohou být potvrzeny do `git commit`místního úložiště Git s .</span><span class="sxs-lookup"><span data-stu-id="8a8e7-174">Additional changes can be committed to the local Git repository with `git commit`.</span></span> <span data-ttu-id="8a8e7-175">Tyto změny jsou zasunuty `git push` do Azure pomocí předchozího příkazu.</span><span class="sxs-lookup"><span data-stu-id="8a8e7-175">These changes are pushed to Azure with the preceding `git push` command.</span></span>

## <a name="deployment-with-visual-studio"></a><span data-ttu-id="8a8e7-176">Nasazení v sadě Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8a8e7-176">Deployment with Visual Studio</span></span>

> <span data-ttu-id="8a8e7-177">*Poznámka: Tato část se vztahuje pouze na Windows. Uživatelé Linuxu a macOS by měli provést změnu popsanou v kroku 2 níže. Uložte soubor a potvrďte změnu `git commit`v místním úložišti pomocí aplikace . Nakonec zatlačte `git push`změnu s , jako v první části.*</span><span class="sxs-lookup"><span data-stu-id="8a8e7-177">*Note: This section applies to Windows only. Linux and macOS users should make the change described in step 2 below. Save the file, and commit the change to the local repository with `git commit`. Finally, push the change with `git push`, as in the first section.*</span></span>

<span data-ttu-id="8a8e7-178">Aplikace již byla nasazena z příkazového prostředí.</span><span class="sxs-lookup"><span data-stu-id="8a8e7-178">The app has already been deployed from the command shell.</span></span> <span data-ttu-id="8a8e7-179">K nasazení aktualizace do aplikace použijeme integrované nástroje visual studia.</span><span class="sxs-lookup"><span data-stu-id="8a8e7-179">Let's use Visual Studio's integrated tools to deploy an update to the app.</span></span> <span data-ttu-id="8a8e7-180">Na pozadí Visual Studio dosahuje stejnou věc jako nástroje příkazového řádku, ale v rámci známého ui sady Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="8a8e7-180">Behind the scenes, Visual Studio accomplishes the same thing as the command line tooling, but within Visual Studio's familiar UI.</span></span>

1. <span data-ttu-id="8a8e7-181">Otevřete *soubor SimpleFeedReader.sln* v sadě Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="8a8e7-181">Open *SimpleFeedReader.sln* in Visual Studio.</span></span>
2. <span data-ttu-id="8a8e7-182">V Průzkumníku řešení otevřete *Stránky\Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="8a8e7-182">In Solution Explorer, open *Pages\Index.cshtml*.</span></span> <span data-ttu-id="8a8e7-183">Změnit `<h2>Simple Feed Reader</h2>` `<h2>Simple Feed Reader - V2</h2>`na .</span><span class="sxs-lookup"><span data-stu-id="8a8e7-183">Change `<h2>Simple Feed Reader</h2>` to `<h2>Simple Feed Reader - V2</h2>`.</span></span>
3. <span data-ttu-id="8a8e7-184">Stisknutím **klávesy Ctrl**+**Shift**+**B** vytvořte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="8a8e7-184">Press **Ctrl**+**Shift**+**B** to build the app.</span></span>
4. <span data-ttu-id="8a8e7-185">V Průzkumníku řešení klikněte pravým tlačítkem myši na projekt a klikněte na **Publikovat**.</span><span class="sxs-lookup"><span data-stu-id="8a8e7-185">In Solution Explorer, right-click on the project and click **Publish**.</span></span>

    ![Snímek obrazovky s pravým tlačítkem myši, publikování](./media/deploying-to-app-service/publish.png)
5. <span data-ttu-id="8a8e7-187">Visual Studio můžete vytvořit nový prostředek služby App Service, ale tato aktualizace bude publikována přes existující nasazení.</span><span class="sxs-lookup"><span data-stu-id="8a8e7-187">Visual Studio can create a new App Service resource, but this update will be published over the existing deployment.</span></span> <span data-ttu-id="8a8e7-188">V **dialogovém** okně Vybrat cíl publikování vyberte **službu App Service** ze seznamu vlevo a pak **vyberte Vybrat existující**.</span><span class="sxs-lookup"><span data-stu-id="8a8e7-188">In the **Pick a publish target** dialog, select **App Service** from the list on the left, and then select **Select Existing**.</span></span> <span data-ttu-id="8a8e7-189">Klikněte na **Publikovat**.</span><span class="sxs-lookup"><span data-stu-id="8a8e7-189">Click **Publish**.</span></span>
6. <span data-ttu-id="8a8e7-190">V dialogovém okně **Služba aplikace** zkontrolujte, že účet Microsoft nebo organizace použitý k vytvoření předplatného Azure se zobrazí v pravém horním okně.</span><span class="sxs-lookup"><span data-stu-id="8a8e7-190">In the **App Service** dialog, confirm that the Microsoft or Organizational account used to create your Azure subscription is displayed in the upper right.</span></span> <span data-ttu-id="8a8e7-191">Pokud tomu tak není, klikněte na rozevírací soubor a přidejte ho.</span><span class="sxs-lookup"><span data-stu-id="8a8e7-191">If it's not, click the drop-down and add it.</span></span>
7. <span data-ttu-id="8a8e7-192">Zkontrolujte, že je vybráno správné **předplatné** Azure.</span><span class="sxs-lookup"><span data-stu-id="8a8e7-192">Confirm that the correct Azure **Subscription** is selected.</span></span> <span data-ttu-id="8a8e7-193">V **popřípadě zobrazit**vyberte **položku Skupina prostředků**.</span><span class="sxs-lookup"><span data-stu-id="8a8e7-193">For **View**, select **Resource Group**.</span></span> <span data-ttu-id="8a8e7-194">Rozbalte skupinu prostředků **AzureTutorial** a vyberte existující webovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="8a8e7-194">Expand the **AzureTutorial** resource group and then select the existing web app.</span></span> <span data-ttu-id="8a8e7-195">Klikněte na tlačítko **OK**.</span><span class="sxs-lookup"><span data-stu-id="8a8e7-195">Click **OK**.</span></span>

    ![Snímek obrazovky s dialogovým oknem Publikovat službu aplikace](./media/deploying-to-app-service/publish-dialog.png)

<span data-ttu-id="8a8e7-197">Visual Studio sestavuje a nasazuje aplikaci do Azure.</span><span class="sxs-lookup"><span data-stu-id="8a8e7-197">Visual Studio builds and deploys the app to Azure.</span></span> <span data-ttu-id="8a8e7-198">Přejděte na adresu URL webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="8a8e7-198">Browse to the web app URL.</span></span> <span data-ttu-id="8a8e7-199">Ověřte, zda je změna `<h2>` prvku aktivní.</span><span class="sxs-lookup"><span data-stu-id="8a8e7-199">Validate that the `<h2>` element modification is live.</span></span>

![Aplikace se změněným názvem](./media/deploying-to-app-service/app-v2.png)

## <a name="deployment-slots"></a><span data-ttu-id="8a8e7-201">Nasazovací sloty</span><span class="sxs-lookup"><span data-stu-id="8a8e7-201">Deployment slots</span></span>

<span data-ttu-id="8a8e7-202">Sloty pro nasazení podporují přípravu změn bez dopadu na aplikaci spuštěnou v produkčním prostředí.</span><span class="sxs-lookup"><span data-stu-id="8a8e7-202">Deployment slots support the staging of changes without impacting the app running in production.</span></span> <span data-ttu-id="8a8e7-203">Jakmile je připravená verze aplikace ověřena týmem pro zajištění kvality, produkční a pracovní sloty mohou být vyměněny.</span><span class="sxs-lookup"><span data-stu-id="8a8e7-203">Once the staged version of the app is validated by a quality assurance team, the production and staging slots can be swapped.</span></span> <span data-ttu-id="8a8e7-204">Aplikace v pracovní min. je tímto způsobem povýšena na produkční.</span><span class="sxs-lookup"><span data-stu-id="8a8e7-204">The app in staging is promoted to production in this manner.</span></span> <span data-ttu-id="8a8e7-205">Následující kroky vytvoří pracovní slot, nasadí některé změny a zamění pracovní slot s produkčním prostředím po ověření.</span><span class="sxs-lookup"><span data-stu-id="8a8e7-205">The following steps create a staging slot, deploy some changes to it, and swap the staging slot with production after verification.</span></span>

1. <span data-ttu-id="8a8e7-206">Pokud už nejste přihlášeni, přihlaste se do [prostředí Azure Cloud Shell.](https://shell.azure.com/bash)</span><span class="sxs-lookup"><span data-stu-id="8a8e7-206">Sign in to the [Azure Cloud Shell](https://shell.azure.com/bash), if not already signed in.</span></span>
2. <span data-ttu-id="8a8e7-207">Vytvořte pracovní slot.</span><span class="sxs-lookup"><span data-stu-id="8a8e7-207">Create the staging slot.</span></span>

    <span data-ttu-id="8a8e7-208">a.</span><span class="sxs-lookup"><span data-stu-id="8a8e7-208">a.</span></span> <span data-ttu-id="8a8e7-209">Vytvořte slot nasazení s *názvem pracovní*.</span><span class="sxs-lookup"><span data-stu-id="8a8e7-209">Create a deployment slot with the name *staging*.</span></span>

    ```azure-cli
    az webapp deployment slot create --name $webappname --resource-group AzureTutorial --slot staging
    ```

    <span data-ttu-id="8a8e7-210">b.</span><span class="sxs-lookup"><span data-stu-id="8a8e7-210">b.</span></span> <span data-ttu-id="8a8e7-211">Nakonfigurujte pracovní slot tak, aby používal nasazení z místního Gitu a získal adresu URL **testovacího** nasazení.</span><span class="sxs-lookup"><span data-stu-id="8a8e7-211">Configure the staging slot to use deployment from local Git and get the **staging** deployment URL.</span></span> <span data-ttu-id="8a8e7-212">**Poznámka: Tato adresa URL pro pozdější odkaz**.</span><span class="sxs-lookup"><span data-stu-id="8a8e7-212">**Note this URL for reference later**.</span></span>

    ```azure-cli
    echo Git deployment URL for staging: $(az webapp deployment source config-local-git --name $webappname --resource-group AzureTutorial --slot staging --query url --output tsv)
    ```

    <span data-ttu-id="8a8e7-213">c.</span><span class="sxs-lookup"><span data-stu-id="8a8e7-213">c.</span></span> <span data-ttu-id="8a8e7-214">Zobrazí adresu URL pracovního slotu.</span><span class="sxs-lookup"><span data-stu-id="8a8e7-214">Display the staging slot's URL.</span></span> <span data-ttu-id="8a8e7-215">Přejděte na adresu URL a podívejte se na prázdný pracovní slot.</span><span class="sxs-lookup"><span data-stu-id="8a8e7-215">Browse to the URL to see the empty staging slot.</span></span> <span data-ttu-id="8a8e7-216">**Poznámka: Tato adresa URL pro pozdější odkaz**.</span><span class="sxs-lookup"><span data-stu-id="8a8e7-216">**Note this URL for reference later**.</span></span>

    ```console
    echo Staging web app URL: http://$webappname-staging.azurewebsites.net
    ```

3. <span data-ttu-id="8a8e7-217">V textovém editoru nebo v sadě Visual Studio znovu `<h2>` upravte *pages/index.cshtml* tak, aby element přečte `<h2>Simple Feed Reader - V3</h2>` a uloží soubor.</span><span class="sxs-lookup"><span data-stu-id="8a8e7-217">In a text editor or Visual Studio, modify *Pages/Index.cshtml* again so that the `<h2>` element reads `<h2>Simple Feed Reader - V3</h2>` and save the file.</span></span>

4. <span data-ttu-id="8a8e7-218">Potvrdíme soubor do místního úložiště Git pomocí stránky **Změny** na kartě *Průzkumník týmu* sady Visual Studio nebo zadáním následujícího příkazového prostředí místního počítače:</span><span class="sxs-lookup"><span data-stu-id="8a8e7-218">Commit the file to the local Git repository, using either the **Changes** page in Visual Studio's *Team Explorer* tab, or by entering the following using the local machine's command shell:</span></span>

    ```console
    git commit -a -m "upgraded to V3"
    ```

5. <span data-ttu-id="8a8e7-219">Pomocí příkazového prostředí místního počítače přidejte adresu URL pracovního nasazení jako vzdálený ovladač Gitu a zatlačte potvrzené změny:</span><span class="sxs-lookup"><span data-stu-id="8a8e7-219">Using the local machine's command shell, add the staging deployment URL as a Git remote and push the committed changes:</span></span>

    <span data-ttu-id="8a8e7-220">a.</span><span class="sxs-lookup"><span data-stu-id="8a8e7-220">a.</span></span> <span data-ttu-id="8a8e7-221">Přidejte vzdálenou adresu URL pro přípravu do místního úložiště Git.</span><span class="sxs-lookup"><span data-stu-id="8a8e7-221">Add the remote URL for staging to the local Git repository.</span></span>

    ```console
    git remote add azure-staging <Git_staging_deployment_URL>
    ```

    <span data-ttu-id="8a8e7-222">b.</span><span class="sxs-lookup"><span data-stu-id="8a8e7-222">b.</span></span> <span data-ttu-id="8a8e7-223">Posuňte místní *hlavní* větev do *hlavní* větve *vzdáleného azure-staging.*</span><span class="sxs-lookup"><span data-stu-id="8a8e7-223">Push the local *master* branch to the *azure-staging* remote's *master* branch.</span></span>

    ```console
    git push azure-staging master
    ```

    <span data-ttu-id="8a8e7-224">Počkejte, než Azure vytvoří a nasadí aplikaci.</span><span class="sxs-lookup"><span data-stu-id="8a8e7-224">Wait while Azure builds and deploys the app.</span></span>

6. <span data-ttu-id="8a8e7-225">Chcete-li ověřit, že V3 byl nasazen do pracovního slotu, otevřete dvě okna prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="8a8e7-225">To verify that V3 has been deployed to the staging slot, open two browser windows.</span></span> <span data-ttu-id="8a8e7-226">V jednom okně přejděte na původní adresu URL webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="8a8e7-226">In one window, navigate to the original web app URL.</span></span> <span data-ttu-id="8a8e7-227">V druhém okně přejděte na adresu URL pracovní webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="8a8e7-227">In the other window, navigate to the staging web app URL.</span></span> <span data-ttu-id="8a8e7-228">Produkční adresa URL obsluhuje V2 aplikace.</span><span class="sxs-lookup"><span data-stu-id="8a8e7-228">The production URL serves V2 of the app.</span></span> <span data-ttu-id="8a8e7-229">Pracovní adresa URL obsluhuje V3 aplikace.</span><span class="sxs-lookup"><span data-stu-id="8a8e7-229">The staging URL serves V3 of the app.</span></span>

    ![Snímek obrazovky se srovnáním oken prohlížeče](./media/deploying-to-app-service/ready-to-swap.png)

7. <span data-ttu-id="8a8e7-231">V prostředí Cloud Shell vyměňte ověřený/zahřátý pracovní slot do produkčního prostředí.</span><span class="sxs-lookup"><span data-stu-id="8a8e7-231">In the Cloud Shell, swap the verified/warmed-up staging slot into production.</span></span>

    ```azure-cli
    az webapp deployment slot swap --name $webappname --resource-group AzureTutorial --slot staging
    ```

8. <span data-ttu-id="8a8e7-232">Ověřte, zda k prohození došlo aktualizací dvou oken prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="8a8e7-232">Verify that the swap occurred by refreshing the two browser windows.</span></span>

    ![Porovnání oken prohlížeče po prohození](./media/deploying-to-app-service/swapped.png)

## <a name="summary"></a><span data-ttu-id="8a8e7-234">Souhrn</span><span class="sxs-lookup"><span data-stu-id="8a8e7-234">Summary</span></span>

<span data-ttu-id="8a8e7-235">V této části byly dokončeny následující úkoly:</span><span class="sxs-lookup"><span data-stu-id="8a8e7-235">In this section, the following tasks were completed:</span></span>

* <span data-ttu-id="8a8e7-236">Stáhli a vytvořili ukázkovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="8a8e7-236">Downloaded and built the sample app.</span></span>
* <span data-ttu-id="8a8e7-237">Pomocí Azure Cloud Shellu jste vytvořili webovou aplikaci služby Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="8a8e7-237">Created an Azure App Service Web App using the Azure Cloud Shell.</span></span>
* <span data-ttu-id="8a8e7-238">Nasazené ukázkové aplikace do Azure pomocí Gitu.</span><span class="sxs-lookup"><span data-stu-id="8a8e7-238">Deployed the sample app to Azure using Git.</span></span>
* <span data-ttu-id="8a8e7-239">Nasadí změnu do aplikace pomocí Visual Studia.</span><span class="sxs-lookup"><span data-stu-id="8a8e7-239">Deployed a change to the app using Visual Studio.</span></span>
* <span data-ttu-id="8a8e7-240">Do webové aplikace byl přidán pracovní slot.</span><span class="sxs-lookup"><span data-stu-id="8a8e7-240">Added a staging slot to the web app.</span></span>
* <span data-ttu-id="8a8e7-241">Nasadí aktualizaci pracovního slotu.</span><span class="sxs-lookup"><span data-stu-id="8a8e7-241">Deployed an update to the staging slot.</span></span>
* <span data-ttu-id="8a8e7-242">Vyměnil pracovní a produkční sloty.</span><span class="sxs-lookup"><span data-stu-id="8a8e7-242">Swapped the staging and production slots.</span></span>

<span data-ttu-id="8a8e7-243">V další části se dozvíte, jak vytvořit kanál DevOps s Azure Pipelines.</span><span class="sxs-lookup"><span data-stu-id="8a8e7-243">In the next section, you'll learn how to build a DevOps pipeline with Azure Pipelines.</span></span>

## <a name="additional-reading"></a><span data-ttu-id="8a8e7-244">Dodatečné čtení</span><span class="sxs-lookup"><span data-stu-id="8a8e7-244">Additional reading</span></span>

* [<span data-ttu-id="8a8e7-245">Přehled webových aplikací</span><span class="sxs-lookup"><span data-stu-id="8a8e7-245">Web Apps overview</span></span>](/azure/app-service/app-service-web-overview)
* [<span data-ttu-id="8a8e7-246">Vytvoření webové aplikace .NET Core využívající SQL Database ve službě Azure App Service</span><span class="sxs-lookup"><span data-stu-id="8a8e7-246">Build a .NET Core and SQL Database web app in Azure App Service</span></span>](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)
* [<span data-ttu-id="8a8e7-247">Konfigurace přihlašovacích údajů pro nasazení služby Azure App Service</span><span class="sxs-lookup"><span data-stu-id="8a8e7-247">Configure deployment credentials for Azure App Service</span></span>](/azure/app-service/app-service-deployment-credentials)
* [<span data-ttu-id="8a8e7-248">Nastavení přípravných prostředí ve službě Azure App Service</span><span class="sxs-lookup"><span data-stu-id="8a8e7-248">Set up staging environments in Azure App Service</span></span>](/azure/app-service/web-sites-staged-publishing)
