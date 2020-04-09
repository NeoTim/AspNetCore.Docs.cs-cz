---
title: Průběžná integrace a nasazování – DevOps s ASP.NET Core a Azure
author: CamSoper
description: Průběžná integrace a nasazení ve DevOps s ASP.NET Core a Azure
ms.author: scaddie
ms.date: 10/24/2018
ms.custom: mvc, seodec18
uid: azure/devops/cicd
ms.openlocfilehash: 5fdf52235b49119503885f92c370dc588e809ffe
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78655832"
---
# <a name="continuous-integration-and-deployment"></a><span data-ttu-id="66498-103">Průběžná integrace a nasazování</span><span class="sxs-lookup"><span data-stu-id="66498-103">Continuous integration and deployment</span></span>

<span data-ttu-id="66498-104">V předchozí kapitole jste vytvořili místní úložiště Git pro aplikaci Simple Feed Reader.</span><span class="sxs-lookup"><span data-stu-id="66498-104">In the previous chapter, you created a local Git repository for the Simple Feed Reader app.</span></span> <span data-ttu-id="66498-105">V této kapitole tento kód publikujete do úložiště GitHub a vytvoříte kanál služby Azure DevOps Services pomocí Azure Pipelines.</span><span class="sxs-lookup"><span data-stu-id="66498-105">In this chapter, you'll publish that code to a GitHub repository and construct an Azure DevOps Services pipeline using Azure Pipelines.</span></span> <span data-ttu-id="66498-106">Kanál umožňuje nepřetržitá sestavení a nasazení aplikace.</span><span class="sxs-lookup"><span data-stu-id="66498-106">The pipeline enables continuous builds and deployments of the app.</span></span> <span data-ttu-id="66498-107">Jakékoli potvrzení úložiště GitHub aktivuje sestavení a nasazení do pracovního slotu Azure Web Appu.</span><span class="sxs-lookup"><span data-stu-id="66498-107">Any commit to the GitHub repository triggers a build and a deployment to the Azure Web App's staging slot.</span></span>

<span data-ttu-id="66498-108">V této části dokončíte následující úkoly:</span><span class="sxs-lookup"><span data-stu-id="66498-108">In this section, you'll complete the following tasks:</span></span>

* <span data-ttu-id="66498-109">Publikování kódu aplikace na GitHubu</span><span class="sxs-lookup"><span data-stu-id="66498-109">Publish the app's code to GitHub</span></span>
* <span data-ttu-id="66498-110">Odpojení místního nasazení Gitu</span><span class="sxs-lookup"><span data-stu-id="66498-110">Disconnect local Git deployment</span></span>
* <span data-ttu-id="66498-111">Vytvoření organizace Azure DevOps</span><span class="sxs-lookup"><span data-stu-id="66498-111">Create an Azure DevOps organization</span></span>
* <span data-ttu-id="66498-112">Vytvoření týmového projektu ve službách Azure DevOps Services</span><span class="sxs-lookup"><span data-stu-id="66498-112">Create a team project in Azure DevOps Services</span></span>
* <span data-ttu-id="66498-113">Vytvoření definice sestavení</span><span class="sxs-lookup"><span data-stu-id="66498-113">Create a build definition</span></span>
* <span data-ttu-id="66498-114">Vytvoření kanálu verze</span><span class="sxs-lookup"><span data-stu-id="66498-114">Create a release pipeline</span></span>
* <span data-ttu-id="66498-115">Potvrzení změn na GitHubu a automatické nasazení do Azure</span><span class="sxs-lookup"><span data-stu-id="66498-115">Commit changes to GitHub and automatically deploy to Azure</span></span>
* <span data-ttu-id="66498-116">Zkontrolujte kanály Azure</span><span class="sxs-lookup"><span data-stu-id="66498-116">Examine the Azure Pipelines pipeline</span></span>

## <a name="publish-the-apps-code-to-github"></a><span data-ttu-id="66498-117">Publikování kódu aplikace na GitHubu</span><span class="sxs-lookup"><span data-stu-id="66498-117">Publish the app's code to GitHub</span></span>

1. <span data-ttu-id="66498-118">Otevřete okno prohlížeče a `https://github.com`přejděte na .</span><span class="sxs-lookup"><span data-stu-id="66498-118">Open a browser window, and navigate to `https://github.com`.</span></span>
1. <span data-ttu-id="66498-119">Klikněte **+** na rozevírací soubor v záhlaví a vyberte **Nové úložiště**:</span><span class="sxs-lookup"><span data-stu-id="66498-119">Click the **+** drop-down in the header, and select **New repository**:</span></span>

    ![GitHub Nové úložiště, volba](media/cicd/github-new-repo.png)

1. <span data-ttu-id="66498-121">Vrozené vrozené okno **Vlastník** vyberte svůj účet a do textového pole **název úložiště** zadejte *čtečku jednoduchých zdrojů.*</span><span class="sxs-lookup"><span data-stu-id="66498-121">Select your account in the **Owner** drop-down, and enter *simple-feed-reader* in the **Repository name** textbox.</span></span>
1. <span data-ttu-id="66498-122">Klikněte na tlačítko **Vytvořit úložiště.**</span><span class="sxs-lookup"><span data-stu-id="66498-122">Click the **Create repository** button.</span></span>
1. <span data-ttu-id="66498-123">Otevřete příkazové prostředí místního počítače.</span><span class="sxs-lookup"><span data-stu-id="66498-123">Open your local machine's command shell.</span></span> <span data-ttu-id="66498-124">Přejděte do adresáře, ve kterém je uloženo úložiště Git *s jednoduchou čtečkou.*</span><span class="sxs-lookup"><span data-stu-id="66498-124">Navigate to the directory in which the *simple-feed-reader* Git repository is stored.</span></span>
1. <span data-ttu-id="66498-125">Přejmenujte existující *vzdálený původ* na *protiproud*.</span><span class="sxs-lookup"><span data-stu-id="66498-125">Rename the existing *origin* remote to *upstream*.</span></span> <span data-ttu-id="66498-126">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="66498-126">Execute the following command:</span></span>

    ```console
    git remote rename origin upstream
    ```

1. <span data-ttu-id="66498-127">Přidejte nový *vzdálený odkaz* na vaši kopii úložiště na GitHubu.</span><span class="sxs-lookup"><span data-stu-id="66498-127">Add a new *origin* remote pointing to your copy of the repository on GitHub.</span></span> <span data-ttu-id="66498-128">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="66498-128">Execute the following command:</span></span>

    ```console
    git remote add origin https://github.com/<GitHub_username>/simple-feed-reader/
    ```

1. <span data-ttu-id="66498-129">Publikujte místní úložiště Git do nově vytvořeného úložiště GitHubu.</span><span class="sxs-lookup"><span data-stu-id="66498-129">Publish your local Git repository to the newly created GitHub repository.</span></span> <span data-ttu-id="66498-130">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="66498-130">Execute the following command:</span></span>

    ```console
    git push -u origin master
    ```

1. <span data-ttu-id="66498-131">Otevřete okno prohlížeče a `https://github.com/<GitHub_username>/simple-feed-reader/`přejděte na .</span><span class="sxs-lookup"><span data-stu-id="66498-131">Open a browser window, and navigate to `https://github.com/<GitHub_username>/simple-feed-reader/`.</span></span> <span data-ttu-id="66498-132">Ověřte, zda se váš kód zobrazí v úložišti GitHub.</span><span class="sxs-lookup"><span data-stu-id="66498-132">Validate that your code appears in the GitHub repository.</span></span>

## <a name="disconnect-local-git-deployment"></a><span data-ttu-id="66498-133">Odpojení místního nasazení Gitu</span><span class="sxs-lookup"><span data-stu-id="66498-133">Disconnect local Git deployment</span></span>

<span data-ttu-id="66498-134">Odejměte místní nasazení Gitu pomocí následujících kroků.</span><span class="sxs-lookup"><span data-stu-id="66498-134">Remove the local Git deployment with the following steps.</span></span> <span data-ttu-id="66498-135">Azure Pipelines (služba Azure DevOps) tuto funkci nahradí i rozšíří.</span><span class="sxs-lookup"><span data-stu-id="66498-135">Azure Pipelines (an Azure DevOps service) both replaces and augments that functionality.</span></span>

1. <span data-ttu-id="66498-136">Otevřete [portál Azure](https://portal.azure.com/)a přejděte na pracovní *\<(mywebapp unique_number\>/staging)* Web App.</span><span class="sxs-lookup"><span data-stu-id="66498-136">Open the [Azure portal](https://portal.azure.com/), and navigate to the *staging (mywebapp\<unique_number\>/staging)* Web App.</span></span> <span data-ttu-id="66498-137">WebOvou aplikaci lze rychle najít zadáním *pracovní hodu* do vyhledávacího pole portálu:</span><span class="sxs-lookup"><span data-stu-id="66498-137">The Web App can be quickly located by entering *staging* in the portal's search box:</span></span>

    ![pracovní hledaný termín webové aplikace](media/cicd/portal-search-box.png)

1. <span data-ttu-id="66498-139">Klepněte na **položku Centrum nasazení**.</span><span class="sxs-lookup"><span data-stu-id="66498-139">Click **Deployment Center**.</span></span> <span data-ttu-id="66498-140">Zobrazí se nový panel.</span><span class="sxs-lookup"><span data-stu-id="66498-140">A new panel appears.</span></span> <span data-ttu-id="66498-141">Kliknutím na **Odpojit** odeberete místní konfiguraci správy zdrojového kódu Git, která byla přidána v předchozí kapitole.</span><span class="sxs-lookup"><span data-stu-id="66498-141">Click **Disconnect** to remove the local Git source control configuration that was added in the previous chapter.</span></span> <span data-ttu-id="66498-142">Potvrďte operaci odebrání kliknutím na tlačítko **Ano.**</span><span class="sxs-lookup"><span data-stu-id="66498-142">Confirm the removal operation by clicking the **Yes** button.</span></span>
1. <span data-ttu-id="66498-143">Přejděte na *mywebapp<unique_number>* App Service.</span><span class="sxs-lookup"><span data-stu-id="66498-143">Navigate to the *mywebapp<unique_number>* App Service.</span></span> <span data-ttu-id="66498-144">Připomínáme, že vyhledávací pole portálu lze použít k rychlému vyhledání služby App Service.</span><span class="sxs-lookup"><span data-stu-id="66498-144">As a reminder, the portal's search box can be used to quickly locate the App Service.</span></span>
1. <span data-ttu-id="66498-145">Klepněte na **položku Centrum nasazení**.</span><span class="sxs-lookup"><span data-stu-id="66498-145">Click **Deployment Center**.</span></span> <span data-ttu-id="66498-146">Zobrazí se nový panel.</span><span class="sxs-lookup"><span data-stu-id="66498-146">A new panel appears.</span></span> <span data-ttu-id="66498-147">Kliknutím na **Odpojit** odeberete místní konfiguraci správy zdrojového kódu Git, která byla přidána v předchozí kapitole.</span><span class="sxs-lookup"><span data-stu-id="66498-147">Click **Disconnect** to remove the local Git source control configuration that was added in the previous chapter.</span></span> <span data-ttu-id="66498-148">Potvrďte operaci odebrání kliknutím na tlačítko **Ano.**</span><span class="sxs-lookup"><span data-stu-id="66498-148">Confirm the removal operation by clicking the **Yes** button.</span></span>

## <a name="create-an-azure-devops-organization"></a><span data-ttu-id="66498-149">Vytvoření organizace Azure DevOps</span><span class="sxs-lookup"><span data-stu-id="66498-149">Create an Azure DevOps organization</span></span>

1. <span data-ttu-id="66498-150">Otevřete prohlížeč a přejděte na [stránku vytvoření organizace Azure DevOps](https://go.microsoft.com/fwlink/?LinkId=307137).</span><span class="sxs-lookup"><span data-stu-id="66498-150">Open a browser, and navigate to the [Azure DevOps organization creation page](https://go.microsoft.com/fwlink/?LinkId=307137).</span></span>
1. <span data-ttu-id="66498-151">Zadejte jedinečný název do textového pole **Vyberte si zapamatovatelný název** a vytvořte adresu URL pro přístup k vaší organizaci Azure DevOps.</span><span class="sxs-lookup"><span data-stu-id="66498-151">Type a unique name into the **Pick a memorable name** textbox to form the URL for accessing your Azure DevOps organization.</span></span>
1. <span data-ttu-id="66498-152">Vyberte přepínací tlačítko **Git,** protože kód je hostován v úložišti GitHub.</span><span class="sxs-lookup"><span data-stu-id="66498-152">Select the **Git** radio button, since the code is hosted in a GitHub repository.</span></span>
1. <span data-ttu-id="66498-153">Klepněte na tlačítko **Pokračovat.**</span><span class="sxs-lookup"><span data-stu-id="66498-153">Click the **Continue** button.</span></span> <span data-ttu-id="66498-154">Po krátké čekání jsou vytvořeny účet a týmový projekt s názvem *MyFirstProject*.</span><span class="sxs-lookup"><span data-stu-id="66498-154">After a short wait, an account and a team project, named *MyFirstProject*, are created.</span></span>

    ![Stránka vytvoření organizace Azure DevOps](media/cicd/vsts-account-creation.png)

1. <span data-ttu-id="66498-156">Otevřete potvrzovací e-mail s oznámením, že organizace a projekt Azure DevOps jsou připravené k použití.</span><span class="sxs-lookup"><span data-stu-id="66498-156">Open the confirmation email indicating that the Azure DevOps organization and project are ready for use.</span></span> <span data-ttu-id="66498-157">Klikněte na tlačítko **Spustit projekt:**</span><span class="sxs-lookup"><span data-stu-id="66498-157">Click the **Start your project** button:</span></span>

    ![Tlačítko Zahájit projekt](media/cicd/vsts-start-project.png)

1. <span data-ttu-id="66498-159">Otevře se prohlížeč \* \<pro\>account_name .visualstudio.com\*.</span><span class="sxs-lookup"><span data-stu-id="66498-159">A browser opens to *\<account_name\>.visualstudio.com*.</span></span> <span data-ttu-id="66498-160">Kliknutím na odkaz *MyFirstProject* zahájíte konfiguraci kanálu DevOps projektu.</span><span class="sxs-lookup"><span data-stu-id="66498-160">Click the *MyFirstProject* link to begin configuring the project's DevOps pipeline.</span></span>

## <a name="configure-the-azure-pipelines-pipeline"></a><span data-ttu-id="66498-161">Konfigurace kanálu Azure Pipelines</span><span class="sxs-lookup"><span data-stu-id="66498-161">Configure the Azure Pipelines pipeline</span></span>

<span data-ttu-id="66498-162">Existují tři různé kroky k dokončení.</span><span class="sxs-lookup"><span data-stu-id="66498-162">There are three distinct steps to complete.</span></span> <span data-ttu-id="66498-163">Dokončení kroků v následujících třech částech má za následek provozní kanál DevOps.</span><span class="sxs-lookup"><span data-stu-id="66498-163">Completing the steps in the following three sections results in an operational DevOps pipeline.</span></span>

### <a name="grant-azure-devops-access-to-the-github-repository"></a><span data-ttu-id="66498-164">Udělení přístupu Azure DevOps k úložišti GitHub</span><span class="sxs-lookup"><span data-stu-id="66498-164">Grant Azure DevOps access to the GitHub repository</span></span>

1. <span data-ttu-id="66498-165">Rozbalte **nebo sestavení kódu z externího úložiště** akordeon.</span><span class="sxs-lookup"><span data-stu-id="66498-165">Expand the **or build code from an external repository** accordion.</span></span> <span data-ttu-id="66498-166">Klepněte na tlačítko **Sestavení instalace:**</span><span class="sxs-lookup"><span data-stu-id="66498-166">Click the **Setup Build** button:</span></span>

    ![Tlačítko Sestavení instalačního programu](media/cicd/vsts-setup-build.png)

1. <span data-ttu-id="66498-168">Vyberte možnost **GitHub** z **oddílu Vybrat zdroj:**</span><span class="sxs-lookup"><span data-stu-id="66498-168">Select the **GitHub** option from the **Select a source** section:</span></span>

    ![Výběr zdroje - GitHub](media/cicd/vsts-select-source.png)

1. <span data-ttu-id="66498-170">Před tím, než azure devops bude mít přístup k úložišti GitHub, je vyžadována autorizace.</span><span class="sxs-lookup"><span data-stu-id="66498-170">Authorization is required before Azure DevOps can access your GitHub repository.</span></span> <span data-ttu-id="66498-171">Do textového pole Název **připojení** zadejte *<GitHub_username> připojení GitHub.*</span><span class="sxs-lookup"><span data-stu-id="66498-171">Enter *<GitHub_username> GitHub connection* in the **Connection name** textbox.</span></span> <span data-ttu-id="66498-172">Příklad:</span><span class="sxs-lookup"><span data-stu-id="66498-172">For example:</span></span>

    ![Název připojení GitHub](media/cicd/vsts-repo-authz.png)

1. <span data-ttu-id="66498-174">Pokud je na vašem účtu GitHub povoleno dvoufaktorové ověřování, je vyžadován osobní přístupový token.</span><span class="sxs-lookup"><span data-stu-id="66498-174">If two-factor authentication is enabled on your GitHub account, a personal access token is required.</span></span> <span data-ttu-id="66498-175">V takovém případě klikněte na odkaz **Autorizovat s tokenem osobního přístupu GitHub.**</span><span class="sxs-lookup"><span data-stu-id="66498-175">In that case, click the **Authorize with a GitHub personal access token** link.</span></span> <span data-ttu-id="66498-176">Nápovědu najdete v [oficiálních pokynech pro vytváření tokenů osobního přístupu GitHub.](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/)</span><span class="sxs-lookup"><span data-stu-id="66498-176">See the [official GitHub personal access token creation instructions](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/) for help.</span></span> <span data-ttu-id="66498-177">Je potřeba pouze rozsah *repo* oprávnění.</span><span class="sxs-lookup"><span data-stu-id="66498-177">Only the *repo* scope of permissions is needed.</span></span> <span data-ttu-id="66498-178">V opačném případě klepněte na tlačítko **Autorizovat pomocí oauth.**</span><span class="sxs-lookup"><span data-stu-id="66498-178">Otherwise, click the **Authorize using OAuth** button.</span></span>
1. <span data-ttu-id="66498-179">Po zobrazení výzvy se přihlaste ke svému účtu GitHub.</span><span class="sxs-lookup"><span data-stu-id="66498-179">When prompted, sign in to your GitHub account.</span></span> <span data-ttu-id="66498-180">Pak vyberte Autorizovat, abyste udělili přístup k organizaci Azure DevOps.</span><span class="sxs-lookup"><span data-stu-id="66498-180">Then select Authorize to grant access to your Azure DevOps organization.</span></span> <span data-ttu-id="66498-181">Pokud je úspěšná, je vytvořen nový koncový bod služby.</span><span class="sxs-lookup"><span data-stu-id="66498-181">If successful, a new service endpoint is created.</span></span>
1. <span data-ttu-id="66498-182">Klikněte na tlačítko se třemi tečkami vedle tlačítka **Úložiště.**</span><span class="sxs-lookup"><span data-stu-id="66498-182">Click the ellipsis button next to the **Repository** button.</span></span> <span data-ttu-id="66498-183">Vyberte *<GitHub_username>/simple-feed-reader* úložiště ze seznamu.</span><span class="sxs-lookup"><span data-stu-id="66498-183">Select the *<GitHub_username>/simple-feed-reader* repository from the list.</span></span> <span data-ttu-id="66498-184">Klepněte na tlačítko **Vybrat.**</span><span class="sxs-lookup"><span data-stu-id="66498-184">Click the **Select** button.</span></span>
1. <span data-ttu-id="66498-185">Vyberte *hlavní* větev z rozbalovací ho souboru **Výchozí větev pro ruční a naplánované sestavení.**</span><span class="sxs-lookup"><span data-stu-id="66498-185">Select the *master* branch from the **Default branch for manual and scheduled builds** drop-down.</span></span> <span data-ttu-id="66498-186">Klepněte na tlačítko **Pokračovat.**</span><span class="sxs-lookup"><span data-stu-id="66498-186">Click the **Continue** button.</span></span> <span data-ttu-id="66498-187">Zobrazí se stránka výběru šablony.</span><span class="sxs-lookup"><span data-stu-id="66498-187">The template selection page appears.</span></span>

### <a name="create-the-build-definition"></a><span data-ttu-id="66498-188">Vytvoření definice sestavení</span><span class="sxs-lookup"><span data-stu-id="66498-188">Create the build definition</span></span>

1. <span data-ttu-id="66498-189">Na stránce výběru šablony zadejte do vyhledávacího pole *ASP.NET Jádro:*</span><span class="sxs-lookup"><span data-stu-id="66498-189">From the template selection page, enter *ASP.NET Core* in the search box:</span></span>

    ![hledání ASP.NET jádra na stránce šablony](media/cicd/vsts-template-selection.png)

1. <span data-ttu-id="66498-191">Zobrazí se výsledky hledání šablony.</span><span class="sxs-lookup"><span data-stu-id="66498-191">The template search results appear.</span></span> <span data-ttu-id="66498-192">Najeďte na **ASP.NET šablonu jádra** a klikněte na **tlačítko Použít.**</span><span class="sxs-lookup"><span data-stu-id="66498-192">Hover over the **ASP.NET Core** template, and click the **Apply** button.</span></span>
1. <span data-ttu-id="66498-193">Zobrazí se karta **Úkoly** definice sestavení.</span><span class="sxs-lookup"><span data-stu-id="66498-193">The **Tasks** tab of the build definition appears.</span></span> <span data-ttu-id="66498-194">Klikněte na kartu **Aktivační události.**</span><span class="sxs-lookup"><span data-stu-id="66498-194">Click the **Triggers** tab.</span></span>
1. <span data-ttu-id="66498-195">Zaškrtněte políčko **Povolit průběžnou integraci.**</span><span class="sxs-lookup"><span data-stu-id="66498-195">Check the **Enable continuous integration** box.</span></span> <span data-ttu-id="66498-196">V části **Filtry větve** zkontrolujte, zda je rozevírací soubor **Typ** nastaven na *Zahrnout*.</span><span class="sxs-lookup"><span data-stu-id="66498-196">Under the **Branch filters** section, confirm that the **Type** drop-down is set to *Include*.</span></span> <span data-ttu-id="66498-197">Nastavte rozevírací soubor **specifikace větve** na *hlavní server*.</span><span class="sxs-lookup"><span data-stu-id="66498-197">Set the **Branch specification** drop-down to *master*.</span></span>

    ![Povolení nastavení průběžné integrace](media/cicd/vsts-enable-ci.png)

    <span data-ttu-id="66498-199">Tato nastavení způsobit sestavení aktivovat při jakékoli změny je posunut *a hlavní* větev úložiště GitHub.</span><span class="sxs-lookup"><span data-stu-id="66498-199">These settings cause a build to trigger when any change is pushed to the *master* branch of the GitHub repository.</span></span> <span data-ttu-id="66498-200">Průběžná integrace se testuje ve [změnách potvrzení githubu a automaticky se nasazuje do](#commit-changes-to-github-and-automatically-deploy-to-azure) sekce Azure.</span><span class="sxs-lookup"><span data-stu-id="66498-200">Continuous integration is tested in the [Commit changes to GitHub and automatically deploy to Azure](#commit-changes-to-github-and-automatically-deploy-to-azure) section.</span></span>

1. <span data-ttu-id="66498-201">Klepněte na tlačítko **Uložit & fronty** a vyberte možnost **Uložit:**</span><span class="sxs-lookup"><span data-stu-id="66498-201">Click the **Save & queue** button, and select the **Save** option:</span></span>

    ![Tlačítko Uložit](media/cicd/vsts-save-build.png)

1. <span data-ttu-id="66498-203">Zobrazí se následující modální dialogové okno:</span><span class="sxs-lookup"><span data-stu-id="66498-203">The following modal dialog appears:</span></span>

    ![Uložit definici sestavení – modální dialog](media/cicd/vsts-save-modal.png)

    <span data-ttu-id="66498-205">Použijte výchozí složku *\\*aplikace a klepněte na tlačítko **Uložit.**</span><span class="sxs-lookup"><span data-stu-id="66498-205">Use the default folder of *\\*, and click the **Save** button.</span></span>

### <a name="create-the-release-pipeline"></a><span data-ttu-id="66498-206">Vytvoření kanálu vydání</span><span class="sxs-lookup"><span data-stu-id="66498-206">Create the release pipeline</span></span>

1. <span data-ttu-id="66498-207">Klikněte na kartu **Zprávy** týmového projektu.</span><span class="sxs-lookup"><span data-stu-id="66498-207">Click the **Releases** tab of your team project.</span></span> <span data-ttu-id="66498-208">Klikněte na tlačítko **Nový kanál.**</span><span class="sxs-lookup"><span data-stu-id="66498-208">Click the **New pipeline** button.</span></span>

    ![Karta Zprávy – tlačítko Nová definice](media/cicd/vsts-new-release-definition.png)

    <span data-ttu-id="66498-210">Zobrazí se podokno výběru šablony.</span><span class="sxs-lookup"><span data-stu-id="66498-210">The template selection pane appears.</span></span>

1. <span data-ttu-id="66498-211">Na stránce výběru šablony zadejte *službu App Service* do vyhledávacího pole:</span><span class="sxs-lookup"><span data-stu-id="66498-211">From the template selection page, enter *App Service* in the search box:</span></span>

    ![Uvolnit vyhledávací pole šablony kanálu](media/cicd/vsts-release-template-search.png)

1. <span data-ttu-id="66498-213">Zobrazí se výsledky hledání šablony.</span><span class="sxs-lookup"><span data-stu-id="66498-213">The template search results appear.</span></span> <span data-ttu-id="66498-214">Najeďte na šablonu **Azure App Service Deployment with Slot** a klikněte na tlačítko **Použít.**</span><span class="sxs-lookup"><span data-stu-id="66498-214">Hover over the **Azure App Service Deployment with Slot** template, and click the **Apply** button.</span></span> <span data-ttu-id="66498-215">Zobrazí se karta **Kanál** kanálu vydání.</span><span class="sxs-lookup"><span data-stu-id="66498-215">The **Pipeline** tab of the release pipeline appears.</span></span>

    ![Karta Uvolnit kanál potrubí](media/cicd/vsts-release-definition-pipeline.png)

1. <span data-ttu-id="66498-217">Klepněte na tlačítko **Přidat** v poli **Artefakty.**</span><span class="sxs-lookup"><span data-stu-id="66498-217">Click the **Add** button in the **Artifacts** box.</span></span> <span data-ttu-id="66498-218">Zobrazí se panel **Přidat artefakt:**</span><span class="sxs-lookup"><span data-stu-id="66498-218">The **Add artifact** panel appears:</span></span>

    ![Kanál vydání – panel Přidat artefakty](media/cicd/vsts-release-add-artifact.png)

1. <span data-ttu-id="66498-220">V části Typ **zdroje** vyberte dlaždici **Sestavení.**</span><span class="sxs-lookup"><span data-stu-id="66498-220">Select the **Build** tile from the **Source type** section.</span></span> <span data-ttu-id="66498-221">Tento typ umožňuje propojení kanálu vydání k definici sestavení.</span><span class="sxs-lookup"><span data-stu-id="66498-221">This type allows for the linking of the release pipeline to the build definition.</span></span>
1. <span data-ttu-id="66498-222">V rozevíracím souboru **Project** vyberte *MyFirstProject.*</span><span class="sxs-lookup"><span data-stu-id="66498-222">Select *MyFirstProject* from the **Project** drop-down.</span></span>
1. <span data-ttu-id="66498-223">V rozevíracím seznamu **Zdroj (definice sestavení)** vyberte název definice sestavení *MyFirstProject-ASP.NET Core-CI*.</span><span class="sxs-lookup"><span data-stu-id="66498-223">Select the build definition name, *MyFirstProject-ASP.NET Core-CI*, from the **Source (Build definition)** drop-down.</span></span>
1. <span data-ttu-id="66498-224">V rozevíracím seznamu **Výchozí verze** vyberte *Nejnovější.*</span><span class="sxs-lookup"><span data-stu-id="66498-224">Select *Latest* from the **Default version** drop-down.</span></span> <span data-ttu-id="66498-225">Tato možnost vytvoří artefakty vytvořené nejnovější spuštění definice sestavení.</span><span class="sxs-lookup"><span data-stu-id="66498-225">This option builds the artifacts produced by the latest run of the build definition.</span></span>
1. <span data-ttu-id="66498-226">Nahraďte text v textovém poli **Zdrojového aliasu** *přetažením*.</span><span class="sxs-lookup"><span data-stu-id="66498-226">Replace the text in the **Source alias** textbox with *Drop*.</span></span>
1. <span data-ttu-id="66498-227">Klikněte na tlačítko **Přidat.**</span><span class="sxs-lookup"><span data-stu-id="66498-227">Click the **Add** button.</span></span> <span data-ttu-id="66498-228">Sekce **Artefakty** se aktualizuje a zobrazí změny.</span><span class="sxs-lookup"><span data-stu-id="66498-228">The **Artifacts** section updates to display the changes.</span></span>
1. <span data-ttu-id="66498-229">Kliknutím na ikonu blesku povolíte nepřetržité nasazení:</span><span class="sxs-lookup"><span data-stu-id="66498-229">Click the lightning bolt icon to enable continuous deployments:</span></span>

    ![Uvolnit potrubí Artefakty - ikona blesku](media/cicd/vsts-artifacts-lightning-bolt.png)

    <span data-ttu-id="66498-231">Pokud je tato možnost povolena, dojde k nasazení pokaždé, když je k dispozici nové sestavení.</span><span class="sxs-lookup"><span data-stu-id="66498-231">With this option enabled, a deployment occurs each time a new build is available.</span></span>
1. <span data-ttu-id="66498-232">Vpravo se zobrazí spouštěcí panel **průběžného nasazení.**</span><span class="sxs-lookup"><span data-stu-id="66498-232">A **Continuous deployment trigger** panel appears to the right.</span></span> <span data-ttu-id="66498-233">Klepnutím na přepínací tlačítko tuto funkci povolíte.</span><span class="sxs-lookup"><span data-stu-id="66498-233">Click the toggle button to enable the feature.</span></span> <span data-ttu-id="66498-234">Není nutné povolit aktivační událost požadavku na **přijetí stisknutí .**</span><span class="sxs-lookup"><span data-stu-id="66498-234">It isn't necessary to enable the **Pull request trigger**.</span></span>
1. <span data-ttu-id="66498-235">Klikněte na rozevírací rozbalovací tlačítko **Přidat** v části **Filtry větev sestavení.**</span><span class="sxs-lookup"><span data-stu-id="66498-235">Click the **Add** drop-down in the **Build branch filters** section.</span></span> <span data-ttu-id="66498-236">Zvolte výchozí možnost **větve definice sestavení.**</span><span class="sxs-lookup"><span data-stu-id="66498-236">Choose the **Build Definition's default branch** option.</span></span> <span data-ttu-id="66498-237">Tento filtr způsobí, že vydání aktivační pouze pro sestavení z *hlavní* větve úložiště GitHub.</span><span class="sxs-lookup"><span data-stu-id="66498-237">This filter causes the release to trigger only for a build from the GitHub repository's *master* branch.</span></span>
1. <span data-ttu-id="66498-238">Klikněte na tlačítko **Uložit**.</span><span class="sxs-lookup"><span data-stu-id="66498-238">Click the **Save** button.</span></span> <span data-ttu-id="66498-239">Klepněte na tlačítko **OK** ve výsledném dialogovém okně **Uložit** modální.</span><span class="sxs-lookup"><span data-stu-id="66498-239">Click the **OK** button in the resulting **Save** modal dialog.</span></span>
1. <span data-ttu-id="66498-240">Klikněte na pole **Prostředí 1.**</span><span class="sxs-lookup"><span data-stu-id="66498-240">Click the **Environment 1** box.</span></span> <span data-ttu-id="66498-241">Vpravo se zobrazí panel **Prostředí.**</span><span class="sxs-lookup"><span data-stu-id="66498-241">An **Environment** panel appears to the right.</span></span> <span data-ttu-id="66498-242">Změňte text *prostředí 1* v textovém poli **Název prostředí** na *Produkční*.</span><span class="sxs-lookup"><span data-stu-id="66498-242">Change the *Environment 1* text in the **Environment name** textbox to *Production*.</span></span>

   ![Kanál vydání – textové pole název prostředí](media/cicd/vsts-environment-name-textbox.png)

1. <span data-ttu-id="66498-244">Klikněte na odkaz **1 fáze, 2 úkoly** v poli **Výroba:**</span><span class="sxs-lookup"><span data-stu-id="66498-244">Click the **1 phase, 2 tasks** link in the **Production** box:</span></span>

    ![Kanál vydání – odkaz produkčního prostředí.png](media/cicd/vsts-production-link.png)

    <span data-ttu-id="66498-246">Zobrazí se karta **Úkoly** prostředí.</span><span class="sxs-lookup"><span data-stu-id="66498-246">The **Tasks** tab of the environment appears.</span></span>
1. <span data-ttu-id="66498-247">Klikněte na úlohu **Nasadit službu Azure App Service na slot.**</span><span class="sxs-lookup"><span data-stu-id="66498-247">Click the **Deploy Azure App Service to Slot** task.</span></span> <span data-ttu-id="66498-248">Jeho nastavení se zobrazí v panelu vpravo.</span><span class="sxs-lookup"><span data-stu-id="66498-248">Its settings appear in a panel to the right.</span></span>
1. <span data-ttu-id="66498-249">V rozevíracím období **předplatného Azure** vyberte předplatné Azure přidružené ke službě App Service.</span><span class="sxs-lookup"><span data-stu-id="66498-249">Select the Azure subscription associated with the App Service from the **Azure subscription** drop-down.</span></span> <span data-ttu-id="66498-250">Po výběru klikněte na tlačítko **Autorizovat.**</span><span class="sxs-lookup"><span data-stu-id="66498-250">Once selected, click the **Authorize** button.</span></span>
1. <span data-ttu-id="66498-251">V rozevíracím souboru **Typ aplikace** vyberte *Web App.*</span><span class="sxs-lookup"><span data-stu-id="66498-251">Select *Web App* from the **App type** drop-down.</span></span>
1. <span data-ttu-id="66498-252">V rozevíracím souboru Název **služby App** vyberte položku *mywebapp/<unique_number/>.*</span><span class="sxs-lookup"><span data-stu-id="66498-252">Select *mywebapp/<unique_number/>* from the **App service name** drop-down.</span></span>
1. <span data-ttu-id="66498-253">V rozevíracím seznamu **skupiny prostředků** vyberte *AzureTutorial.*</span><span class="sxs-lookup"><span data-stu-id="66498-253">Select *AzureTutorial* from the **Resource group** drop-down.</span></span>
1. <span data-ttu-id="66498-254">V rozevíracím souboru **Slot** vyberte *pracovní.*</span><span class="sxs-lookup"><span data-stu-id="66498-254">Select *staging* from the **Slot** drop-down.</span></span>
1. <span data-ttu-id="66498-255">Klikněte na tlačítko **Uložit**.</span><span class="sxs-lookup"><span data-stu-id="66498-255">Click the **Save** button.</span></span>
1. <span data-ttu-id="66498-256">Najeďte na výchozí název kanálu vydání.</span><span class="sxs-lookup"><span data-stu-id="66498-256">Hover over the default release pipeline name.</span></span> <span data-ttu-id="66498-257">Klikněte na ikonu tužky a upravte ji.</span><span class="sxs-lookup"><span data-stu-id="66498-257">Click the pencil icon to edit it.</span></span> <span data-ttu-id="66498-258">Jako název použijte *MyFirstProject-ASP.NET Core-CD.*</span><span class="sxs-lookup"><span data-stu-id="66498-258">Use *MyFirstProject-ASP.NET Core-CD* as the name.</span></span>

    ![Uvolnit název kanálu](media/cicd/vsts-release-definition-name.png)

1. <span data-ttu-id="66498-260">Klikněte na tlačítko **Uložit**.</span><span class="sxs-lookup"><span data-stu-id="66498-260">Click the **Save** button.</span></span>

## <a name="commit-changes-to-github-and-automatically-deploy-to-azure"></a><span data-ttu-id="66498-261">Potvrzení změn na GitHubu a automatické nasazení do Azure</span><span class="sxs-lookup"><span data-stu-id="66498-261">Commit changes to GitHub and automatically deploy to Azure</span></span>

1. <span data-ttu-id="66498-262">Otevřete *soubor SimpleFeedReader.sln* v sadě Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="66498-262">Open *SimpleFeedReader.sln* in Visual Studio.</span></span>
1. <span data-ttu-id="66498-263">V Průzkumníku řešení otevřete *Stránky\Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="66498-263">In Solution Explorer, open *Pages\Index.cshtml*.</span></span> <span data-ttu-id="66498-264">Změnit `<h2>Simple Feed Reader - V3</h2>` `<h2>Simple Feed Reader - V4</h2>`na .</span><span class="sxs-lookup"><span data-stu-id="66498-264">Change `<h2>Simple Feed Reader - V3</h2>` to `<h2>Simple Feed Reader - V4</h2>`.</span></span>
1. <span data-ttu-id="66498-265">Stisknutím **klávesy Ctrl**+**Shift**+**B** vytvořte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="66498-265">Press **Ctrl**+**Shift**+**B** to build the app.</span></span>
1. <span data-ttu-id="66498-266">Pozmiřte soubor do úložiště GitHub.</span><span class="sxs-lookup"><span data-stu-id="66498-266">Commit the file to the GitHub repository.</span></span> <span data-ttu-id="66498-267">Použijte stránku **Změny** na kartě *Průzkumník týmu* sady Visual Studio nebo pomocí příkazového prostředí místního počítače proveďte následující:</span><span class="sxs-lookup"><span data-stu-id="66498-267">Use either the **Changes** page in Visual Studio's *Team Explorer* tab, or execute the following using the local machine's command shell:</span></span>

    ```console
    git commit -a -m "upgraded to V4"
    ```

1. <span data-ttu-id="66498-268">Posuňte změnu v *hlavní* větvi *na* výchozí dálkové ovládání úložiště GitHub:</span><span class="sxs-lookup"><span data-stu-id="66498-268">Push the change in the *master* branch to the *origin* remote of your GitHub repository:</span></span>

    ```console
    git push origin master
    ```

    <span data-ttu-id="66498-269">Potvrzení se zobrazí v *hlavní* větvi úložiště GitHub:</span><span class="sxs-lookup"><span data-stu-id="66498-269">The commit appears in the GitHub repository's *master* branch:</span></span>

    ![Potvrzení GitHubu v hlavní větvi](media/cicd/github-commit.png)

    <span data-ttu-id="66498-271">Sestavení se aktivuje, protože průběžná integrace je povolena na kartě **Aktivační události** definice sestavení:</span><span class="sxs-lookup"><span data-stu-id="66498-271">The build is triggered, since continuous integration is enabled in the build definition's **Triggers** tab:</span></span>

    ![umožňují průběžnou integraci](media/cicd/enable-ci.png)

1. <span data-ttu-id="66498-273">Přejděte na kartu **Ve frontě** na stránce**Sestavení** **Azure Pipelines** > ve službách Azure DevOps Services.</span><span class="sxs-lookup"><span data-stu-id="66498-273">Navigate to the **Queued** tab of the **Azure Pipelines** > **Builds** page in Azure DevOps Services.</span></span> <span data-ttu-id="66498-274">Sestavení ve frontě zobrazuje větev a potvrzení, které spustilo sestavení:</span><span class="sxs-lookup"><span data-stu-id="66498-274">The queued build shows the branch and commit that triggered the build:</span></span>

    ![sestavení ve frontě](media/cicd/build-queued.png)

1. <span data-ttu-id="66498-276">Jakmile sestavení proběhne úspěšně, dojde k nasazení do Azure.</span><span class="sxs-lookup"><span data-stu-id="66498-276">Once the build succeeds, a deployment to Azure occurs.</span></span> <span data-ttu-id="66498-277">Přejděte do aplikace v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="66498-277">Navigate to the app in the browser.</span></span> <span data-ttu-id="66498-278">Všimněte si, že text "V4" se zobrazí v záhlaví:</span><span class="sxs-lookup"><span data-stu-id="66498-278">Notice that the "V4" text appears in the heading:</span></span>

    ![aktualizovaná aplikace](media/cicd/updated-app-v4.png)

## <a name="examine-the-azure-pipelines-pipeline"></a><span data-ttu-id="66498-280">Zkontrolujte kanály Azure</span><span class="sxs-lookup"><span data-stu-id="66498-280">Examine the Azure Pipelines pipeline</span></span>

### <a name="build-definition"></a><span data-ttu-id="66498-281">Definice sestavení</span><span class="sxs-lookup"><span data-stu-id="66498-281">Build definition</span></span>

<span data-ttu-id="66498-282">Definice sestavení byla vytvořena s názvem *MyFirstProject-ASP.NET Core-CI*.</span><span class="sxs-lookup"><span data-stu-id="66498-282">A build definition was created with the name *MyFirstProject-ASP.NET Core-CI*.</span></span> <span data-ttu-id="66498-283">Po dokončení sestavení vytvoří soubor *ZIP* včetně datových zdrojů, které mají být publikovány.</span><span class="sxs-lookup"><span data-stu-id="66498-283">Upon completion, the build produces a *.zip* file including the assets to be published.</span></span> <span data-ttu-id="66498-284">Kanál vydání nasazuje tyto prostředky do Azure.</span><span class="sxs-lookup"><span data-stu-id="66498-284">The release pipeline deploys those assets to Azure.</span></span>

<span data-ttu-id="66498-285">Na kartě **Úkoly** definice sestavení jsou uvedeny jednotlivé používané kroky.</span><span class="sxs-lookup"><span data-stu-id="66498-285">The build definition's **Tasks** tab lists the individual steps being used.</span></span> <span data-ttu-id="66498-286">Existuje pět úloh sestavení.</span><span class="sxs-lookup"><span data-stu-id="66498-286">There are five build tasks.</span></span>

![sestavení úloh definice](media/cicd/build-definition-tasks.png)

1. <span data-ttu-id="66498-288">**Obnovení** &mdash; provede `dotnet restore` příkaz k obnovení nugetových balíčků aplikace.</span><span class="sxs-lookup"><span data-stu-id="66498-288">**Restore** &mdash; Executes the `dotnet restore` command to restore the app's NuGet packages.</span></span> <span data-ttu-id="66498-289">Výchozí balíček je použit nuget.org.</span><span class="sxs-lookup"><span data-stu-id="66498-289">The default package feed used is nuget.org.</span></span>
1. <span data-ttu-id="66498-290">**Sestavení** &mdash; Provede `dotnet build --configuration release` příkaz pro kompilaci kódu aplikace.</span><span class="sxs-lookup"><span data-stu-id="66498-290">**Build** &mdash; Executes the `dotnet build --configuration release` command to compile the app's code.</span></span> <span data-ttu-id="66498-291">Tato `--configuration` možnost se používá k vytvoření optimalizované verze kódu, která je vhodná pro nasazení do produkčního prostředí.</span><span class="sxs-lookup"><span data-stu-id="66498-291">This `--configuration` option is used to produce an optimized version of the code, which is suitable for deployment to a production environment.</span></span> <span data-ttu-id="66498-292">Pokud je například potřeba konfigurace ladění, upravte proměnnou *BuildConfiguration* na kartě **Proměnné** definice sestavení.</span><span class="sxs-lookup"><span data-stu-id="66498-292">Modify the *BuildConfiguration* variable on the build definition's **Variables** tab if, for example, a debug configuration is needed.</span></span>
1. <span data-ttu-id="66498-293">**Test** &mdash; provede `dotnet test --configuration release --logger trx --results-directory <local_path_on_build_agent>` příkaz ke spuštění testů částí aplikace.</span><span class="sxs-lookup"><span data-stu-id="66498-293">**Test** &mdash; Executes the `dotnet test --configuration release --logger trx --results-directory <local_path_on_build_agent>` command to run the app's unit tests.</span></span> <span data-ttu-id="66498-294">Testy částí jsou prováděny v rámci `**/*Tests/*.csproj` libovolného projektu C# odpovídající glob vzor.</span><span class="sxs-lookup"><span data-stu-id="66498-294">Unit tests are executed within any C# project matching the `**/*Tests/*.csproj` glob pattern.</span></span> <span data-ttu-id="66498-295">Výsledky testu jsou uloženy v souboru *.trx* v umístění určeném `--results-directory` volbou.</span><span class="sxs-lookup"><span data-stu-id="66498-295">Test results are saved in a *.trx* file at the location specified by the `--results-directory` option.</span></span> <span data-ttu-id="66498-296">Pokud se všechny testy nezdaří, sestavení se nezdaří a není nasazen.</span><span class="sxs-lookup"><span data-stu-id="66498-296">If any tests fail, the build fails and isn't deployed.</span></span>

    > [!NOTE]
    > <span data-ttu-id="66498-297">Chcete-li ověřit práce testů částí, upravte *SimpleFeedReader.Tests\Services\NewsServiceTests.cs* záměrně přerušit jeden z testů.</span><span class="sxs-lookup"><span data-stu-id="66498-297">To verify the unit tests work, modify *SimpleFeedReader.Tests\Services\NewsServiceTests.cs* to purposefully break one of the tests.</span></span> <span data-ttu-id="66498-298">Například změnit `Assert.True(result.Count > 0);` `Assert.False(result.Count > 0);` na `Returns_News_Stories_Given_Valid_Uri` v metodě.</span><span class="sxs-lookup"><span data-stu-id="66498-298">For example, change `Assert.True(result.Count > 0);` to `Assert.False(result.Count > 0);` in the `Returns_News_Stories_Given_Valid_Uri` method.</span></span> <span data-ttu-id="66498-299">Potvrdí meze a posune změnu na GitHub.</span><span class="sxs-lookup"><span data-stu-id="66498-299">Commit and push the change to GitHub.</span></span> <span data-ttu-id="66498-300">Sestavení se aktivuje a selže.</span><span class="sxs-lookup"><span data-stu-id="66498-300">The build is triggered and fails.</span></span> <span data-ttu-id="66498-301">Stav kanálu sestavení se změní na **neúspěšnou**.</span><span class="sxs-lookup"><span data-stu-id="66498-301">The build pipeline status changes to **failed**.</span></span> <span data-ttu-id="66498-302">Vrátit změnu, potvrdit a znovu zatlačit.</span><span class="sxs-lookup"><span data-stu-id="66498-302">Revert the change, commit, and push again.</span></span> <span data-ttu-id="66498-303">Sestavení úspěšné.</span><span class="sxs-lookup"><span data-stu-id="66498-303">The build succeeds.</span></span>

1. <span data-ttu-id="66498-304">**Publikování** &mdash; Provede `dotnet publish --configuration release --output <local_path_on_build_agent>` příkaz k vytvoření *souboru ZIP* s artefakty, které mají být nasazeny.</span><span class="sxs-lookup"><span data-stu-id="66498-304">**Publish** &mdash; Executes the `dotnet publish --configuration release --output <local_path_on_build_agent>` command to produce a *.zip* file with the artifacts to be deployed.</span></span> <span data-ttu-id="66498-305">Tato `--output` možnost určuje umístění publikování souboru *ZIP.*</span><span class="sxs-lookup"><span data-stu-id="66498-305">The `--output` option specifies the publish location of the *.zip* file.</span></span> <span data-ttu-id="66498-306">Toto umístění je určeno předáním `$(build.artifactstagingdirectory)` [předdefinované proměnné](/azure/devops/pipelines/build/variables) s názvem .</span><span class="sxs-lookup"><span data-stu-id="66498-306">That location is specified by passing a [predefined variable](/azure/devops/pipelines/build/variables) named `$(build.artifactstagingdirectory)`.</span></span> <span data-ttu-id="66498-307">Tato proměnná se rozbalí na místní cestu, například *c:\agent\_work\1\a*, na agenta sestavení.</span><span class="sxs-lookup"><span data-stu-id="66498-307">That variable expands to a local path, such as *c:\agent\_work\1\a*, on the build agent.</span></span>
1. <span data-ttu-id="66498-308">**Publikování artefaktu** &mdash; publikuje soubor *ZIP* vytvořený úlohou **Publikovat.**</span><span class="sxs-lookup"><span data-stu-id="66498-308">**Publish Artifact** &mdash; Publishes the *.zip* file produced by the **Publish** task.</span></span> <span data-ttu-id="66498-309">Úloha přijme umístění souboru *ZIP* jako parametr, což `$(build.artifactstagingdirectory)`je předdefinovaná proměnná .</span><span class="sxs-lookup"><span data-stu-id="66498-309">The task accepts the *.zip* file location as a parameter, which is the predefined variable `$(build.artifactstagingdirectory)`.</span></span> <span data-ttu-id="66498-310">Soubor *ZIP* je publikován jako složka s názvem *drop*.</span><span class="sxs-lookup"><span data-stu-id="66498-310">The *.zip* file is published as a folder named *drop*.</span></span>

<span data-ttu-id="66498-311">Kliknutím na odkaz **Souhrn** definice sestavení zobrazíte historii sestavení s definicí:</span><span class="sxs-lookup"><span data-stu-id="66498-311">Click the build definition's **Summary** link to view a history of builds with the definition:</span></span>

![Snímek obrazovky s historií definice sestavení](media/cicd/build-definition-summary.png)

<span data-ttu-id="66498-313">Na výsledné stránce klikněte na odkaz odpovídající jedinečnému číslu sestavení:</span><span class="sxs-lookup"><span data-stu-id="66498-313">On the resulting page, click the link corresponding to the unique build number:</span></span>

![Snímek obrazovky se stránkou souhrnu definice sestavení](media/cicd/build-definition-completed.png)

<span data-ttu-id="66498-315">Zobrazí se souhrn tohoto konkrétního sestavení.</span><span class="sxs-lookup"><span data-stu-id="66498-315">A summary of this specific build is displayed.</span></span> <span data-ttu-id="66498-316">Klikněte na kartu **Artefakty** a všimněte si, že složka *přetažení* vytvořená sestavením je uvedena:</span><span class="sxs-lookup"><span data-stu-id="66498-316">Click the **Artifacts** tab, and notice the *drop* folder produced by the build is listed:</span></span>

![Snímek obrazovky s artefakty definice sestavení – složka přetažení](media/cicd/build-definition-artifacts.png)

<span data-ttu-id="66498-318">Pomocí odkazů **Stáhnout** a **prozkoumat** zkontrolujte publikované artefakty.</span><span class="sxs-lookup"><span data-stu-id="66498-318">Use the **Download** and **Explore** links to inspect the published artifacts.</span></span>

### <a name="release-pipeline"></a><span data-ttu-id="66498-319">Kanál verze</span><span class="sxs-lookup"><span data-stu-id="66498-319">Release pipeline</span></span>

<span data-ttu-id="66498-320">Byl vytvořen kanál vydání s názvem *MyFirstProject-ASP.NET Core-CD*:</span><span class="sxs-lookup"><span data-stu-id="66498-320">A release pipeline was created with the name *MyFirstProject-ASP.NET Core-CD*:</span></span>

![Snímek obrazovky s přehledem kanálu vydání](media/cicd/release-definition-overview.png)

<span data-ttu-id="66498-322">Dvě hlavní součásti kanálu vydání jsou **artefakty** a **prostředí**.</span><span class="sxs-lookup"><span data-stu-id="66498-322">The two major components of the release pipeline are the **Artifacts** and the **Environments**.</span></span> <span data-ttu-id="66498-323">Kliknutím na pole v sekci **Artefakty** se zobrazí následující panel:</span><span class="sxs-lookup"><span data-stu-id="66498-323">Clicking the box in the **Artifacts** section reveals the following panel:</span></span>

![Snímek obrazovky s artefakty kanálu vydání](media/cicd/release-definition-artifacts.png)

<span data-ttu-id="66498-325">Source **(Build definice)** hodnota představuje definici sestavení, ke kterému je propojen tento kanál vydání.</span><span class="sxs-lookup"><span data-stu-id="66498-325">The **Source (Build definition)** value represents the build definition to which this release pipeline is linked.</span></span> <span data-ttu-id="66498-326">Soubor *ZIP* vytvořený úspěšným spuštěním definice sestavení je k dispozici *produkčnímu* prostředí pro nasazení do Azure.</span><span class="sxs-lookup"><span data-stu-id="66498-326">The *.zip* file produced by a successful run of the build definition is provided to the *Production* environment for deployment to Azure.</span></span> <span data-ttu-id="66498-327">Kliknutím na odkaz *1 fáze, 2 úkoly* v poli *Produkční* prostředí zobrazíte úlohy kanálu vydání:</span><span class="sxs-lookup"><span data-stu-id="66498-327">Click the *1 phase, 2 tasks* link in the *Production* environment box to view the release pipeline tasks:</span></span>

![Snímek obrazovky s úkoly kanálu vydání](media/cicd/release-definition-tasks.png)

<span data-ttu-id="66498-329">Kanál vydání se skládá ze dvou úloh: *Nasazení služby Azure App Service do slotu* a *správa služby Azure App Service – prohození slotů*.</span><span class="sxs-lookup"><span data-stu-id="66498-329">The release pipeline consists of two tasks: *Deploy Azure App Service to Slot* and *Manage Azure App Service - Slot Swap*.</span></span> <span data-ttu-id="66498-330">Kliknutím na první úkol zobrazíte následující konfiguraci úkolu:</span><span class="sxs-lookup"><span data-stu-id="66498-330">Clicking the first task reveals the following task configuration:</span></span>

![Snímek obrazovky s úlohou nasazení kanálu vydání](media/cicd/release-definition-task1.png)

<span data-ttu-id="66498-332">Předplatné Azure, typ služby, název webové aplikace, skupina prostředků a slot nasazení jsou definovány v úloze nasazení.</span><span class="sxs-lookup"><span data-stu-id="66498-332">The Azure subscription, service type, web app name, resource group, and deployment slot are defined in the deployment task.</span></span> <span data-ttu-id="66498-333">Textové pole **Balíček nebo složka** obsahuje cestu souboru *ZIP,* která má být extrahována a nasazena do *pracovního* slotu webové aplikace \*mywebapp\<unique_number.\> \*</span><span class="sxs-lookup"><span data-stu-id="66498-333">The **Package or folder** textbox holds the *.zip* file path to be extracted and deployed to the *staging* slot of the *mywebapp\<unique_number\>* web app.</span></span>

<span data-ttu-id="66498-334">Kliknutím na úlohu odměnění slotů se zobrazí následující konfigurace úlohy:</span><span class="sxs-lookup"><span data-stu-id="66498-334">Clicking the slot swap task reveals the following task configuration:</span></span>

![Snímek obrazovky s úlohou odkládání slotů pro uvolnění kanálu](media/cicd/release-definition-task2.png)

<span data-ttu-id="66498-336">K dispozici jsou podrobnosti o předplatném, skupině prostředků, typu služby, názvu webové aplikace a podrobnostech o slotu nasazení.</span><span class="sxs-lookup"><span data-stu-id="66498-336">The subscription, resource group, service type, web app name, and deployment slot details are provided.</span></span> <span data-ttu-id="66498-337">Zaškrtávací políčko **Zaměnit s výrobou** je zaškrtnuto.</span><span class="sxs-lookup"><span data-stu-id="66498-337">The **Swap with Production** check box is checked.</span></span> <span data-ttu-id="66498-338">V důsledku toho bity nasazené do *pracovního* slotu jsou převedeny do produkčního prostředí.</span><span class="sxs-lookup"><span data-stu-id="66498-338">Consequently, the bits deployed to the *staging* slot are swapped into the production environment.</span></span>

## <a name="additional-reading"></a><span data-ttu-id="66498-339">Dodatečné čtení</span><span class="sxs-lookup"><span data-stu-id="66498-339">Additional reading</span></span>

* [<span data-ttu-id="66498-340">Vytvoření prvního kanálu pomocí Azure Pipelines</span><span class="sxs-lookup"><span data-stu-id="66498-340">Create your first pipeline with Azure Pipelines</span></span>](/azure/devops/pipelines/get-started-yaml)
* [<span data-ttu-id="66498-341">Sestavení a základní projekt .NET</span><span class="sxs-lookup"><span data-stu-id="66498-341">Build and .NET Core project</span></span>](/azure/devops/pipelines/languages/dotnet-core)
* [<span data-ttu-id="66498-342">Nasazení webové aplikace pomocí Azure Pipelines</span><span class="sxs-lookup"><span data-stu-id="66498-342">Deploy a web app with Azure Pipelines</span></span>](/azure/devops/pipelines/targets/webapp)
