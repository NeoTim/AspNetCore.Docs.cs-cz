---
title: DevOps s využitím ASP.NET Core a Azure | Průběžná integrace a nasazování
author: CamSoper
description: Průvodce, který poskytuje pokyny k začátku do konce na vytváření procesních toků pro DevOps pro aplikace ASP.NET Core hostované v Azure.
ms.author: scaddie
ms.date: 10/24/2018
uid: azure/devops/cicd
ms.openlocfilehash: edaf2c2e1428e5e82104786d94584a4ef08f9ee3
ms.sourcegitcommit: 408921a932448f66cb46fd53c307a864f5323fe5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2018
ms.locfileid: "51570084"
---
# <a name="continuous-integration-and-deployment"></a><span data-ttu-id="fb71a-103">Průběžná integrace a nasazování</span><span class="sxs-lookup"><span data-stu-id="fb71a-103">Continuous integration and deployment</span></span>

<span data-ttu-id="fb71a-104">V předchozích kapitol vytvoříte místní úložiště Git pro aplikaci jednoduché Reader informačního kanálu.</span><span class="sxs-lookup"><span data-stu-id="fb71a-104">In the previous chapter, you created a local Git repository for the Simple Feed Reader app.</span></span> <span data-ttu-id="fb71a-105">V této kapitole budete publikovat tento kód do úložiště GitHub a vytvořit kanál DevOps služby Azure pomocí Azure kanálů.</span><span class="sxs-lookup"><span data-stu-id="fb71a-105">In this chapter, you'll publish that code to a GitHub repository and construct an Azure DevOps Services pipeline using Azure Pipelines.</span></span> <span data-ttu-id="fb71a-106">Kanál umožňuje průběžné vytváření buildů a nasazení aplikace.</span><span class="sxs-lookup"><span data-stu-id="fb71a-106">The pipeline enables continuous builds and deployments of the app.</span></span> <span data-ttu-id="fb71a-107">Každé potvrzení do úložiště GitHub se aktivuje sestavení a nasazení do přípravného slotu webové aplikace Azure.</span><span class="sxs-lookup"><span data-stu-id="fb71a-107">Any commit to the GitHub repository triggers a build and a deployment to the Azure Web App's staging slot.</span></span>

<span data-ttu-id="fb71a-108">V této části budete provádět následující úlohy:</span><span class="sxs-lookup"><span data-stu-id="fb71a-108">In this section, you'll complete the following tasks:</span></span>

* <span data-ttu-id="fb71a-109">Publikování aplikace kódu na Githubu</span><span class="sxs-lookup"><span data-stu-id="fb71a-109">Publish the app's code to GitHub</span></span>
* <span data-ttu-id="fb71a-110">Odpojit místní nasazení přes Git</span><span class="sxs-lookup"><span data-stu-id="fb71a-110">Disconnect local Git deployment</span></span>
* <span data-ttu-id="fb71a-111">Vytvořit organizaci Azure DevOps</span><span class="sxs-lookup"><span data-stu-id="fb71a-111">Create an Azure DevOps organization</span></span>
* <span data-ttu-id="fb71a-112">Vytvořit týmový projekt ve službách Azure DevOps</span><span class="sxs-lookup"><span data-stu-id="fb71a-112">Create a team project in Azure DevOps Services</span></span>
* <span data-ttu-id="fb71a-113">Vytvořte definici sestavení</span><span class="sxs-lookup"><span data-stu-id="fb71a-113">Create a build definition</span></span>
* <span data-ttu-id="fb71a-114">Vytvořit kanál pro vydávání verzí</span><span class="sxs-lookup"><span data-stu-id="fb71a-114">Create a release pipeline</span></span>
* <span data-ttu-id="fb71a-115">Potvrzení změn na Githubu a automaticky nasadit do Azure</span><span class="sxs-lookup"><span data-stu-id="fb71a-115">Commit changes to GitHub and automatically deploy to Azure</span></span>
* <span data-ttu-id="fb71a-116">Prozkoumejte Azure kanály kanálu</span><span class="sxs-lookup"><span data-stu-id="fb71a-116">Examine the Azure Pipelines pipeline</span></span>

## <a name="publish-the-apps-code-to-github"></a><span data-ttu-id="fb71a-117">Publikování aplikace kódu na Githubu</span><span class="sxs-lookup"><span data-stu-id="fb71a-117">Publish the app's code to GitHub</span></span>

1. <span data-ttu-id="fb71a-118">Otevřete okno prohlížeče a přejděte do `https://github.com`.</span><span class="sxs-lookup"><span data-stu-id="fb71a-118">Open a browser window, and navigate to `https://github.com`.</span></span>
1. <span data-ttu-id="fb71a-119">Klikněte na tlačítko **+** rozevírací seznam v záhlaví a vyberte **nové úložiště**:</span><span class="sxs-lookup"><span data-stu-id="fb71a-119">Click the **+** drop-down in the header, and select **New repository**:</span></span>

    ![Možnost Nový úložiště GitHub](media/cicd/github-new-repo.png)

1. <span data-ttu-id="fb71a-121">Vyberte svůj účet v **vlastníka** rozevíracího seznamu a zadejte *jednoduchý kanálu čtečky* v **název úložiště** textového pole.</span><span class="sxs-lookup"><span data-stu-id="fb71a-121">Select your account in the **Owner** drop-down, and enter *simple-feed-reader* in the **Repository name** textbox.</span></span>
1. <span data-ttu-id="fb71a-122">Klikněte na tlačítko **vytvoření úložiště** tlačítko.</span><span class="sxs-lookup"><span data-stu-id="fb71a-122">Click the **Create repository** button.</span></span>
1. <span data-ttu-id="fb71a-123">Otevřete příkazové okno místního počítače.</span><span class="sxs-lookup"><span data-stu-id="fb71a-123">Open your local machine's command shell.</span></span> <span data-ttu-id="fb71a-124">Přejděte do adresáře, ve kterém *jednoduchý kanálu čtečky* uložená v úložišti Git.</span><span class="sxs-lookup"><span data-stu-id="fb71a-124">Navigate to the directory in which the *simple-feed-reader* Git repository is stored.</span></span>
1. <span data-ttu-id="fb71a-125">Přejmenovat stávající *původu* do vzdáleného úložiště *nadřazeného*.</span><span class="sxs-lookup"><span data-stu-id="fb71a-125">Rename the existing *origin* remote to *upstream*.</span></span> <span data-ttu-id="fb71a-126">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="fb71a-126">Execute the following command:</span></span>
    ```console
    git remote rename origin upstream
    ```
1. <span data-ttu-id="fb71a-127">Přidat nový *původu* vzdálené odkazuje na kopii úložišti na Githubu.</span><span class="sxs-lookup"><span data-stu-id="fb71a-127">Add a new *origin* remote pointing to your copy of the repository on GitHub.</span></span> <span data-ttu-id="fb71a-128">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="fb71a-128">Execute the following command:</span></span>
    ```console
    git remote add origin https://github.com/<GitHub_username>/simple-feed-reader/
    ```
1. <span data-ttu-id="fb71a-129">Publikování místního úložiště Git do nově vytvořené úložiště GitHub.</span><span class="sxs-lookup"><span data-stu-id="fb71a-129">Publish your local Git repository to the newly created GitHub repository.</span></span> <span data-ttu-id="fb71a-130">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="fb71a-130">Execute the following command:</span></span>
    ```console
    git push -u origin master
    ```
1. <span data-ttu-id="fb71a-131">Otevřete okno prohlížeče a přejděte do `https://github.com/<GitHub_username>/simple-feed-reader/`.</span><span class="sxs-lookup"><span data-stu-id="fb71a-131">Open a browser window, and navigate to `https://github.com/<GitHub_username>/simple-feed-reader/`.</span></span> <span data-ttu-id="fb71a-132">Ověřte, že váš kód se zobrazí v úložišti GitHub.</span><span class="sxs-lookup"><span data-stu-id="fb71a-132">Validate that your code appears in the GitHub repository.</span></span>

## <a name="disconnect-local-git-deployment"></a><span data-ttu-id="fb71a-133">Odpojit místní nasazení přes Git</span><span class="sxs-lookup"><span data-stu-id="fb71a-133">Disconnect local Git deployment</span></span>

<span data-ttu-id="fb71a-134">Odeberte místní nasazení přes Git pomocí následujícího postupu.</span><span class="sxs-lookup"><span data-stu-id="fb71a-134">Remove the local Git deployment with the following steps.</span></span> <span data-ttu-id="fb71a-135">Kanály Azure (služby Azure DevOps) nahrazuje a argumentech, které tuto funkci.</span><span class="sxs-lookup"><span data-stu-id="fb71a-135">Azure Pipelines (an Azure DevOps service) both replaces and augments that functionality.</span></span>

1. <span data-ttu-id="fb71a-136">Otevřít [webu Azure portal](https://portal.azure.com/)a přejděte *pracovní (mywebapp\<unique_number\>/pracovní)* webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="fb71a-136">Open the [Azure portal](https://portal.azure.com/), and navigate to the *staging (mywebapp\<unique_number\>/staging)* Web App.</span></span> <span data-ttu-id="fb71a-137">Webové aplikace můžete rychle umístěný tak, že zadáte *pracovní* vyhledávacího pole na portálu:</span><span class="sxs-lookup"><span data-stu-id="fb71a-137">The Web App can be quickly located by entering *staging* in the portal's search box:</span></span>

    ![pracovní webové aplikace hledaný termín](media/cicd/portal-search-box.png)

1. <span data-ttu-id="fb71a-139">Klikněte na tlačítko **možnosti nasazení**.</span><span class="sxs-lookup"><span data-stu-id="fb71a-139">Click **Deployment options**.</span></span> <span data-ttu-id="fb71a-140">Otevře se nový panel.</span><span class="sxs-lookup"><span data-stu-id="fb71a-140">A new panel appears.</span></span> <span data-ttu-id="fb71a-141">Klikněte na tlačítko **odpojit** místní Git konfigurace správy zdrojového kódu, který byl přidán v předchozích kapitol odebrat.</span><span class="sxs-lookup"><span data-stu-id="fb71a-141">Click **Disconnect** to remove the local Git source control configuration that was added in the previous chapter.</span></span> <span data-ttu-id="fb71a-142">Operace odstranění potvrďte kliknutím **Ano** tlačítko.</span><span class="sxs-lookup"><span data-stu-id="fb71a-142">Confirm the removal operation by clicking the **Yes** button.</span></span>
1. <span data-ttu-id="fb71a-143">Přejděte *mywebapp < unique_number >* služby App Service.</span><span class="sxs-lookup"><span data-stu-id="fb71a-143">Navigate to the *mywebapp<unique_number>* App Service.</span></span> <span data-ttu-id="fb71a-144">Připomínáme je možné k rychlému vyhledání služby App Service na portálu vyhledávacího pole.</span><span class="sxs-lookup"><span data-stu-id="fb71a-144">As a reminder, the portal's search box can be used to quickly locate the App Service.</span></span>
1. <span data-ttu-id="fb71a-145">Klikněte na tlačítko **možnosti nasazení**.</span><span class="sxs-lookup"><span data-stu-id="fb71a-145">Click **Deployment options**.</span></span> <span data-ttu-id="fb71a-146">Otevře se nový panel.</span><span class="sxs-lookup"><span data-stu-id="fb71a-146">A new panel appears.</span></span> <span data-ttu-id="fb71a-147">Klikněte na tlačítko **odpojit** místní Git konfigurace správy zdrojového kódu, který byl přidán v předchozích kapitol odebrat.</span><span class="sxs-lookup"><span data-stu-id="fb71a-147">Click **Disconnect** to remove the local Git source control configuration that was added in the previous chapter.</span></span> <span data-ttu-id="fb71a-148">Operace odstranění potvrďte kliknutím **Ano** tlačítko.</span><span class="sxs-lookup"><span data-stu-id="fb71a-148">Confirm the removal operation by clicking the **Yes** button.</span></span>

## <a name="create-an-azure-devops-organization"></a><span data-ttu-id="fb71a-149">Vytvořit organizaci Azure DevOps</span><span class="sxs-lookup"><span data-stu-id="fb71a-149">Create an Azure DevOps organization</span></span>

1. <span data-ttu-id="fb71a-150">Otevřete prohlížeč a přejděte [stránce pro vytvoření organizace Azure DevOps](https://go.microsoft.com/fwlink/?LinkId=307137).</span><span class="sxs-lookup"><span data-stu-id="fb71a-150">Open a browser, and navigate to the [Azure DevOps organization creation page](https://go.microsoft.com/fwlink/?LinkId=307137).</span></span>
1. <span data-ttu-id="fb71a-151">Zadejte jedinečný název do **vyberte si snadno zapamatovatelné jméno** textového pole a vytvoří adresu URL pro přístup k vaší organizaci Azure DevOps.</span><span class="sxs-lookup"><span data-stu-id="fb71a-151">Type a unique name into the **Pick a memorable name** textbox to form the URL for accessing your Azure DevOps organization.</span></span>
1. <span data-ttu-id="fb71a-152">Vyberte **Git** přepínač, protože je kód hostovaný v úložišti GitHub.</span><span class="sxs-lookup"><span data-stu-id="fb71a-152">Select the **Git** radio button, since the code is hosted in a GitHub repository.</span></span>
1. <span data-ttu-id="fb71a-153">Klikněte na tlačítko **pokračovat** tlačítko.</span><span class="sxs-lookup"><span data-stu-id="fb71a-153">Click the **Continue** button.</span></span> <span data-ttu-id="fb71a-154">Po krátkém čekání, účet a týmový projekt s názvem *MyFirstProject*, jsou vytvořeny.</span><span class="sxs-lookup"><span data-stu-id="fb71a-154">After a short wait, an account and a team project, named *MyFirstProject*, are created.</span></span>

    ![Stránka Vytvořit organizaci Azure DevOps](media/cicd/vsts-account-creation.png)

1. <span data-ttu-id="fb71a-156">Otevřete potvrzení e-mailu označující, že organizaci Azure DevOps a projektu jsou připravené k použití.</span><span class="sxs-lookup"><span data-stu-id="fb71a-156">Open the confirmation email indicating that the Azure DevOps organization and project are ready for use.</span></span> <span data-ttu-id="fb71a-157">Klikněte na tlačítko **začněte svůj projekt** tlačítka:</span><span class="sxs-lookup"><span data-stu-id="fb71a-157">Click the **Start your project** button:</span></span>

    ![Váš projekt tlačítko Start](media/cicd/vsts-start-project.png)

1. <span data-ttu-id="fb71a-159">V prohlížeči se otevře  *\<account_name\>. visualstudio.com*.</span><span class="sxs-lookup"><span data-stu-id="fb71a-159">A browser opens to *\<account_name\>.visualstudio.com*.</span></span> <span data-ttu-id="fb71a-160">Klikněte na tlačítko *MyFirstProject* odkaz se začne konfigurace projektu kanálu DevOps.</span><span class="sxs-lookup"><span data-stu-id="fb71a-160">Click the *MyFirstProject* link to begin configuring the project's DevOps pipeline.</span></span>

## <a name="configure-the-azure-pipelines-pipeline"></a><span data-ttu-id="fb71a-161">Nakonfigurujte kanál kanály Azure</span><span class="sxs-lookup"><span data-stu-id="fb71a-161">Configure the Azure Pipelines pipeline</span></span>

<span data-ttu-id="fb71a-162">Existují tři samostatné kroky k dokončení.</span><span class="sxs-lookup"><span data-stu-id="fb71a-162">There are three distinct steps to complete.</span></span> <span data-ttu-id="fb71a-163">Dokončením kroků v následujících třech částech vede provozní kanál DevOps.</span><span class="sxs-lookup"><span data-stu-id="fb71a-163">Completing the steps in the following three sections results in an operational DevOps pipeline.</span></span>

### <a name="grant-azure-devops-access-to-the-github-repository"></a><span data-ttu-id="fb71a-164">Azure DevOps udělit přístup k úložišti GitHub</span><span class="sxs-lookup"><span data-stu-id="fb71a-164">Grant Azure DevOps access to the GitHub repository</span></span>

1. <span data-ttu-id="fb71a-165">Rozbalte **nebo vytváření kódu z externího úložiště** prvku typu accordion.</span><span class="sxs-lookup"><span data-stu-id="fb71a-165">Expand the **or build code from an external repository** accordion.</span></span> <span data-ttu-id="fb71a-166">Klikněte na tlačítko **nastavení sestavení** tlačítka:</span><span class="sxs-lookup"><span data-stu-id="fb71a-166">Click the **Setup Build** button:</span></span>

    ![Tlačítko Nastavení sestavení](media/cicd/vsts-setup-build.png)

1. <span data-ttu-id="fb71a-168">Vyberte **Githubu** možnost **vyberte zdroj** části:</span><span class="sxs-lookup"><span data-stu-id="fb71a-168">Select the **GitHub** option from the **Select a source** section:</span></span>

    ![Vyberte zdroje – GitHub](media/cicd/vsts-select-source.png)

1. <span data-ttu-id="fb71a-170">Vyžaduje se autorizace, než Azure DevOps můžete získat přístup k úložišti GitHub.</span><span class="sxs-lookup"><span data-stu-id="fb71a-170">Authorization is required before Azure DevOps can access your GitHub repository.</span></span> <span data-ttu-id="fb71a-171">Zadejte *< GitHub_username > Githubu připojení* v **název připojení** textového pole.</span><span class="sxs-lookup"><span data-stu-id="fb71a-171">Enter *<GitHub_username> GitHub connection* in the **Connection name** textbox.</span></span> <span data-ttu-id="fb71a-172">Příklad:</span><span class="sxs-lookup"><span data-stu-id="fb71a-172">For example:</span></span>

    ![Název připojení Githubu](media/cicd/vsts-repo-authz.png)

1. <span data-ttu-id="fb71a-174">Pokud na vašem účtu GitHub je povoleno dvoufaktorové ověřování, osobní přístupový token je povinný.</span><span class="sxs-lookup"><span data-stu-id="fb71a-174">If two-factor authentication is enabled on your GitHub account, a personal access token is required.</span></span> <span data-ttu-id="fb71a-175">V takovém případě klikněte na tlačítko **autorizovat pomocí osobního přístupového tokenu Githubu** odkaz.</span><span class="sxs-lookup"><span data-stu-id="fb71a-175">In that case, click the **Authorize with a GitHub personal access token** link.</span></span> <span data-ttu-id="fb71a-176">Zobrazit [oficiální pokyny k vytvoření tokenu pat Githubu](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/) nápovědu.</span><span class="sxs-lookup"><span data-stu-id="fb71a-176">See the [official GitHub personal access token creation instructions](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/) for help.</span></span> <span data-ttu-id="fb71a-177">Pouze *úložiště* obor oprávnění je potřeba.</span><span class="sxs-lookup"><span data-stu-id="fb71a-177">Only the *repo* scope of permissions is needed.</span></span> <span data-ttu-id="fb71a-178">V opačném případě klikněte na tlačítko **autorizovat pomocí OAuth** tlačítko.</span><span class="sxs-lookup"><span data-stu-id="fb71a-178">Otherwise, click the **Authorize using OAuth** button.</span></span>
1. <span data-ttu-id="fb71a-179">Po zobrazení výzvy, přihlaste se k vašemu účtu GitHub.</span><span class="sxs-lookup"><span data-stu-id="fb71a-179">When prompted, sign in to your GitHub account.</span></span> <span data-ttu-id="fb71a-180">Vyberte Authorize k udělení přístupu k vaší organizaci Azure DevOps.</span><span class="sxs-lookup"><span data-stu-id="fb71a-180">Then select Authorize to grant access to your Azure DevOps organization.</span></span> <span data-ttu-id="fb71a-181">V případě úspěchu, je vytvořen nový koncový bod služby.</span><span class="sxs-lookup"><span data-stu-id="fb71a-181">If successful, a new service endpoint is created.</span></span>
1. <span data-ttu-id="fb71a-182">Klikněte na tlačítko se třemi tečkami vedle **úložiště** tlačítko.</span><span class="sxs-lookup"><span data-stu-id="fb71a-182">Click the ellipsis button next to the **Repository** button.</span></span> <span data-ttu-id="fb71a-183">Vyberte *< GitHub_username > / jednoduchý kanálu čtečky* úložiště ze seznamu.</span><span class="sxs-lookup"><span data-stu-id="fb71a-183">Select the *<GitHub_username>/simple-feed-reader* repository from the list.</span></span> <span data-ttu-id="fb71a-184">Klikněte na tlačítko **vyberte** tlačítko.</span><span class="sxs-lookup"><span data-stu-id="fb71a-184">Click the **Select** button.</span></span>
1. <span data-ttu-id="fb71a-185">Vyberte *hlavní* vytvářet větve z **výchozí větev pro ruční a plánovaná sestavení** rozevíracího seznamu.</span><span class="sxs-lookup"><span data-stu-id="fb71a-185">Select the *master* branch from the **Default branch for manual and scheduled builds** drop-down.</span></span> <span data-ttu-id="fb71a-186">Klikněte na tlačítko **pokračovat** tlačítko.</span><span class="sxs-lookup"><span data-stu-id="fb71a-186">Click the **Continue** button.</span></span> <span data-ttu-id="fb71a-187">Zobrazí se stránka pro výběr šablony.</span><span class="sxs-lookup"><span data-stu-id="fb71a-187">The template selection page appears.</span></span>

### <a name="create-the-build-definition"></a><span data-ttu-id="fb71a-188">Vytvořte definici sestavení</span><span class="sxs-lookup"><span data-stu-id="fb71a-188">Create the build definition</span></span>

1. <span data-ttu-id="fb71a-189">Na stránce Výběr šablony zadejte *ASP.NET Core* do vyhledávacího pole:</span><span class="sxs-lookup"><span data-stu-id="fb71a-189">From the template selection page, enter *ASP.NET Core* in the search box:</span></span>

    ![ASP.NET Core hledání na stránce šablony](media/cicd/vsts-template-selection.png)

1. <span data-ttu-id="fb71a-191">Šablona výsledky hledání zobrazeny.</span><span class="sxs-lookup"><span data-stu-id="fb71a-191">The template search results appear.</span></span> <span data-ttu-id="fb71a-192">Najeďte myší **ASP.NET Core** šablony a kliknutím **použít** tlačítko.</span><span class="sxs-lookup"><span data-stu-id="fb71a-192">Hover over the **ASP.NET Core** template, and click the **Apply** button.</span></span>
1. <span data-ttu-id="fb71a-193">**Úlohy** se zobrazí karta definice sestavení.</span><span class="sxs-lookup"><span data-stu-id="fb71a-193">The **Tasks** tab of the build definition appears.</span></span> <span data-ttu-id="fb71a-194">Klikněte na tlačítko **triggery** kartu.</span><span class="sxs-lookup"><span data-stu-id="fb71a-194">Click the **Triggers** tab.</span></span>
1. <span data-ttu-id="fb71a-195">Zkontrolujte, **aktivovat nepřetržitou integraci** pole.</span><span class="sxs-lookup"><span data-stu-id="fb71a-195">Check the **Enable continuous integration** box.</span></span> <span data-ttu-id="fb71a-196">V části **filtry větví** části, ujistěte se, že **typ** rozevíracího seznamu je nastavena na *zahrnout*.</span><span class="sxs-lookup"><span data-stu-id="fb71a-196">Under the **Branch filters** section, confirm that the **Type** drop-down is set to *Include*.</span></span> <span data-ttu-id="fb71a-197">Nastavte **větev specifikace** rozevíracího seznamu *hlavní*.</span><span class="sxs-lookup"><span data-stu-id="fb71a-197">Set the **Branch specification** drop-down to *master*.</span></span>

    ![Povolit nastavení průběžné integrace](media/cicd/vsts-enable-ci.png)

    <span data-ttu-id="fb71a-199">Toto nastavení způsobí sestavení aktivovat při každé změně se vloží do *hlavní* větev úložiště GitHub.</span><span class="sxs-lookup"><span data-stu-id="fb71a-199">These settings cause a build to trigger when any change is pushed to the *master* branch of the GitHub repository.</span></span> <span data-ttu-id="fb71a-200">Nepřetržitá integrace je testován v [potvrzení změn na Githubu a automaticky nasadit do Azure](#commit-changes-to-github-and-automatically-deploy-to-azure) oddílu.</span><span class="sxs-lookup"><span data-stu-id="fb71a-200">Continuous integration is tested in the [Commit changes to GitHub and automatically deploy to Azure](#commit-changes-to-github-and-automatically-deploy-to-azure) section.</span></span>

1. <span data-ttu-id="fb71a-201">Klikněte na tlačítko **Uložit & frontu** tlačítko a vyberte **Uložit** možnost:</span><span class="sxs-lookup"><span data-stu-id="fb71a-201">Click the **Save & queue** button, and select the **Save** option:</span></span>

    ![Tlačítko Uložit](media/cicd/vsts-save-build.png)

1. <span data-ttu-id="fb71a-203">Zobrazí se následující modální dialogové okno:</span><span class="sxs-lookup"><span data-stu-id="fb71a-203">The following modal dialog appears:</span></span>

    ![Modální dialogové okno Uložit definici sestavení-](media/cicd/vsts-save-modal.png)

    <span data-ttu-id="fb71a-205">Použít výchozí složky *\\* a klikněte na tlačítko **Uložit** tlačítko.</span><span class="sxs-lookup"><span data-stu-id="fb71a-205">Use the default folder of *\\*, and click the **Save** button.</span></span>

### <a name="create-the-release-pipeline"></a><span data-ttu-id="fb71a-206">Vytvořit kanál pro vydávání verzí</span><span class="sxs-lookup"><span data-stu-id="fb71a-206">Create the release pipeline</span></span>

1. <span data-ttu-id="fb71a-207">Klikněte na tlačítko **verze** kartu týmového projektu.</span><span class="sxs-lookup"><span data-stu-id="fb71a-207">Click the **Releases** tab of your team project.</span></span> <span data-ttu-id="fb71a-208">Klikněte na tlačítko **nový kanál** tlačítko.</span><span class="sxs-lookup"><span data-stu-id="fb71a-208">Click the **New pipeline** button.</span></span>

    ![Karta – tlačítko Nová definice verze](media/cicd/vsts-new-release-definition.png)

    <span data-ttu-id="fb71a-210">Otevře se podokno výběr šablony.</span><span class="sxs-lookup"><span data-stu-id="fb71a-210">The template selection pane appears.</span></span>

1. <span data-ttu-id="fb71a-211">Na stránce Výběr šablony zadejte *služby App Service* do vyhledávacího pole:</span><span class="sxs-lookup"><span data-stu-id="fb71a-211">From the template selection page, enter *App Service* in the search box:</span></span>

    ![Verze kanálu šablony vyhledávacího pole](media/cicd/vsts-release-template-search.png)

1. <span data-ttu-id="fb71a-213">Šablona výsledky hledání zobrazeny.</span><span class="sxs-lookup"><span data-stu-id="fb71a-213">The template search results appear.</span></span> <span data-ttu-id="fb71a-214">Najeďte myší **nasazení služby Azure App Service se slotem** šablony a kliknutím **použít** tlačítko.</span><span class="sxs-lookup"><span data-stu-id="fb71a-214">Hover over the **Azure App Service Deployment with Slot** template, and click the **Apply** button.</span></span> <span data-ttu-id="fb71a-215">**Kanálu** se zobrazí karta kanál pro vydávání verzí.</span><span class="sxs-lookup"><span data-stu-id="fb71a-215">The **Pipeline** tab of the release pipeline appears.</span></span>

    ![Kanál pro vydávání verzí kartu kanálu](media/cicd/vsts-release-definition-pipeline.png)

1. <span data-ttu-id="fb71a-217">Klikněte na tlačítko **přidat** tlačítko **artefakty** pole.</span><span class="sxs-lookup"><span data-stu-id="fb71a-217">Click the **Add** button in the **Artifacts** box.</span></span> <span data-ttu-id="fb71a-218">**Přidání artefaktu** panelu se zobrazí:</span><span class="sxs-lookup"><span data-stu-id="fb71a-218">The **Add artifact** panel appears:</span></span>

    ![Kanál pro vydávání verzí – přidání artefaktu panelu](media/cicd/vsts-release-add-artifact.png)

1. <span data-ttu-id="fb71a-220">Vyberte **sestavení** dlaždici z **typ zdroje** oddílu.</span><span class="sxs-lookup"><span data-stu-id="fb71a-220">Select the **Build** tile from the **Source type** section.</span></span> <span data-ttu-id="fb71a-221">Tento typ umožňuje nastavit odkazy kanál pro vydávání verzí této definici sestavení.</span><span class="sxs-lookup"><span data-stu-id="fb71a-221">This type allows for the linking of the release pipeline to the build definition.</span></span>
1. <span data-ttu-id="fb71a-222">Vyberte *MyFirstProject* z **projektu** rozevíracího seznamu.</span><span class="sxs-lookup"><span data-stu-id="fb71a-222">Select *MyFirstProject* from the **Project** drop-down.</span></span>
1. <span data-ttu-id="fb71a-223">Název definice sestavení vyberte *MyFirstProject ASP.NET Core-CI*, z **zdroj (definice sestavení)** rozevíracího seznamu.</span><span class="sxs-lookup"><span data-stu-id="fb71a-223">Select the build definition name, *MyFirstProject-ASP.NET Core-CI*, from the **Source (Build definition)** drop-down.</span></span>
1. <span data-ttu-id="fb71a-224">Vyberte *nejnovější* z **výchozí verze** rozevíracího seznamu.</span><span class="sxs-lookup"><span data-stu-id="fb71a-224">Select *Latest* from the **Default version** drop-down.</span></span> <span data-ttu-id="fb71a-225">Tato možnost sestavení artefakty vytvořené spuštěním nejnovější definice sestavení.</span><span class="sxs-lookup"><span data-stu-id="fb71a-225">This option builds the artifacts produced by the latest run of the build definition.</span></span>
1. <span data-ttu-id="fb71a-226">Nahradit text **alias zdroje** textové pole s *vyřadit*.</span><span class="sxs-lookup"><span data-stu-id="fb71a-226">Replace the text in the **Source alias** textbox with *Drop*.</span></span>
1. <span data-ttu-id="fb71a-227">Klikněte na tlačítko **přidat** tlačítko.</span><span class="sxs-lookup"><span data-stu-id="fb71a-227">Click the **Add** button.</span></span> <span data-ttu-id="fb71a-228">**Artefakty** části aktualizací zobrazíte změny.</span><span class="sxs-lookup"><span data-stu-id="fb71a-228">The **Artifacts** section updates to display the changes.</span></span>
1. <span data-ttu-id="fb71a-229">Klikněte na ikonu blesku povolit nepřetržité nasazení:</span><span class="sxs-lookup"><span data-stu-id="fb71a-229">Click the lightning bolt icon to enable continuous deployments:</span></span>

    ![Kanál pro vydávání verzí artefakty – ikona blesku](media/cicd/vsts-artifacts-lightning-bolt.png)

    <span data-ttu-id="fb71a-231">Tato možnost povolená dojde k nasazení pokaždé, když je k dispozici nové sestavení.</span><span class="sxs-lookup"><span data-stu-id="fb71a-231">With this option enabled, a deployment occurs each time a new build is available.</span></span>
1. <span data-ttu-id="fb71a-232">A **trigger průběžného nasazování** panelu se zobrazí na pravé straně.</span><span class="sxs-lookup"><span data-stu-id="fb71a-232">A **Continuous deployment trigger** panel appears to the right.</span></span> <span data-ttu-id="fb71a-233">Klikněte na přepínací tlačítko k povolení této funkce.</span><span class="sxs-lookup"><span data-stu-id="fb71a-233">Click the toggle button to enable the feature.</span></span> <span data-ttu-id="fb71a-234">Není nutná pro povolení **triggeru žádosti o přijetí změn**.</span><span class="sxs-lookup"><span data-stu-id="fb71a-234">It isn't necessary to enable the **Pull request trigger**.</span></span>
1. <span data-ttu-id="fb71a-235">Klikněte na tlačítko **přidat** rozevírací seznam v **vytvářet filtry větví** oddílu.</span><span class="sxs-lookup"><span data-stu-id="fb71a-235">Click the **Add** drop-down in the **Build branch filters** section.</span></span> <span data-ttu-id="fb71a-236">Zvolte **Build Definition výchozí větev** možnost.</span><span class="sxs-lookup"><span data-stu-id="fb71a-236">Choose the **Build Definition's default branch** option.</span></span> <span data-ttu-id="fb71a-237">Tento filtr způsobí, že verze aktivovat pouze pro sestavení z úložiště GitHub *hlavní* větve.</span><span class="sxs-lookup"><span data-stu-id="fb71a-237">This filter causes the release to trigger only for a build from the GitHub repository's *master* branch.</span></span>
1. <span data-ttu-id="fb71a-238">Klikněte na tlačítko **Uložit** tlačítko.</span><span class="sxs-lookup"><span data-stu-id="fb71a-238">Click the **Save** button.</span></span> <span data-ttu-id="fb71a-239">Klikněte na tlačítko **OK** tlačítko ve výsledné **Uložit** modální dialogové okno.</span><span class="sxs-lookup"><span data-stu-id="fb71a-239">Click the **OK** button in the resulting **Save** modal dialog.</span></span>
1. <span data-ttu-id="fb71a-240">Klikněte na tlačítko **prostředí 1** pole.</span><span class="sxs-lookup"><span data-stu-id="fb71a-240">Click the **Environment 1** box.</span></span> <span data-ttu-id="fb71a-241">**Prostředí** panelu se zobrazí na pravé straně.</span><span class="sxs-lookup"><span data-stu-id="fb71a-241">An **Environment** panel appears to the right.</span></span> <span data-ttu-id="fb71a-242">Změnit *prostředí 1* textu v **název prostředí** testovém poli *produkční*.</span><span class="sxs-lookup"><span data-stu-id="fb71a-242">Change the *Environment 1* text in the **Environment name** textbox to *Production*.</span></span>

   ![Kanál pro vydávání verzí – textové pole pro název prostředí](media/cicd/vsts-environment-name-textbox.png)

1. <span data-ttu-id="fb71a-244">Klikněte na tlačítko **fáze 1, 2 úlohy** odkaz v **produkční** pole:</span><span class="sxs-lookup"><span data-stu-id="fb71a-244">Click the **1 phase, 2 tasks** link in the **Production** box:</span></span>

    ![Kanál pro vydávání verzí - link.png produkční prostředí](media/cicd/vsts-production-link.png)

    <span data-ttu-id="fb71a-246">**Úlohy** se zobrazí karta prostředí.</span><span class="sxs-lookup"><span data-stu-id="fb71a-246">The **Tasks** tab of the environment appears.</span></span>
1. <span data-ttu-id="fb71a-247">Klikněte na tlačítko **nasazení služby Azure App Service do slotu** úloh.</span><span class="sxs-lookup"><span data-stu-id="fb71a-247">Click the **Deploy Azure App Service to Slot** task.</span></span> <span data-ttu-id="fb71a-248">Nastavení se zobrazí v panelu napravo.</span><span class="sxs-lookup"><span data-stu-id="fb71a-248">Its settings appear in a panel to the right.</span></span>
1. <span data-ttu-id="fb71a-249">Vyberte předplatné Azure spojené s App Service z **předplatného Azure** rozevíracího seznamu.</span><span class="sxs-lookup"><span data-stu-id="fb71a-249">Select the Azure subscription associated with the App Service from the **Azure subscription** drop-down.</span></span> <span data-ttu-id="fb71a-250">Po výběru, klikněte na tlačítko **Authorize** tlačítko.</span><span class="sxs-lookup"><span data-stu-id="fb71a-250">Once selected, click the **Authorize** button.</span></span>
1. <span data-ttu-id="fb71a-251">Vyberte *webovou aplikaci* z **typ aplikace** rozevíracího seznamu.</span><span class="sxs-lookup"><span data-stu-id="fb71a-251">Select *Web App* from the **App type** drop-down.</span></span>
1. <span data-ttu-id="fb71a-252">Vyberte *mywebapp / < unique_number / >* z **název služby App service** rozevíracího seznamu.</span><span class="sxs-lookup"><span data-stu-id="fb71a-252">Select *mywebapp/<unique_number/>* from the **App service name** drop-down.</span></span>
1. <span data-ttu-id="fb71a-253">Vyberte *AzureTutorial* z **skupiny prostředků** rozevíracího seznamu.</span><span class="sxs-lookup"><span data-stu-id="fb71a-253">Select *AzureTutorial* from the **Resource group** drop-down.</span></span>
1. <span data-ttu-id="fb71a-254">Vyberte *pracovní* z **slotu** rozevíracího seznamu.</span><span class="sxs-lookup"><span data-stu-id="fb71a-254">Select *staging* from the **Slot** drop-down.</span></span>
1. <span data-ttu-id="fb71a-255">Klikněte na tlačítko **Uložit** tlačítko.</span><span class="sxs-lookup"><span data-stu-id="fb71a-255">Click the **Save** button.</span></span>
1. <span data-ttu-id="fb71a-256">Najeďte myší výchozí název kanálu vydané verze.</span><span class="sxs-lookup"><span data-stu-id="fb71a-256">Hover over the default release pipeline name.</span></span> <span data-ttu-id="fb71a-257">Klikněte na ikonu tužky a upravte ho.</span><span class="sxs-lookup"><span data-stu-id="fb71a-257">Click the pencil icon to edit it.</span></span> <span data-ttu-id="fb71a-258">Použití *MyFirstProject ASP.NET Core-CD* jako název.</span><span class="sxs-lookup"><span data-stu-id="fb71a-258">Use *MyFirstProject-ASP.NET Core-CD* as the name.</span></span>

    ![Název kanálu vydané verze](media/cicd/vsts-release-definition-name.png)

1. <span data-ttu-id="fb71a-260">Klikněte na tlačítko **Uložit** tlačítko.</span><span class="sxs-lookup"><span data-stu-id="fb71a-260">Click the **Save** button.</span></span>

## <a name="commit-changes-to-github-and-automatically-deploy-to-azure"></a><span data-ttu-id="fb71a-261">Potvrzení změn na Githubu a automaticky nasadit do Azure</span><span class="sxs-lookup"><span data-stu-id="fb71a-261">Commit changes to GitHub and automatically deploy to Azure</span></span>

1. <span data-ttu-id="fb71a-262">Otevřít *SimpleFeedReader.sln* v sadě Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="fb71a-262">Open *SimpleFeedReader.sln* in Visual Studio.</span></span>
1. <span data-ttu-id="fb71a-263">V Průzkumníku řešení otevřete *Pages\Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="fb71a-263">In Solution Explorer, open *Pages\Index.cshtml*.</span></span> <span data-ttu-id="fb71a-264">Změna `<h2>Simple Feed Reader - V3</h2>` k `<h2>Simple Feed Reader - V4</h2>`.</span><span class="sxs-lookup"><span data-stu-id="fb71a-264">Change `<h2>Simple Feed Reader - V3</h2>` to `<h2>Simple Feed Reader - V4</h2>`.</span></span>
1. <span data-ttu-id="fb71a-265">Stisknutím klávesy **Ctrl**+**Shift**+**B** k sestavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="fb71a-265">Press **Ctrl**+**Shift**+**B** to build the app.</span></span>
1. <span data-ttu-id="fb71a-266">Potvrďte souboru do úložiště GitHub.</span><span class="sxs-lookup"><span data-stu-id="fb71a-266">Commit the file to the GitHub repository.</span></span> <span data-ttu-id="fb71a-267">Použijte buď **změny** stránky v sadě Visual Studio *Team Exploreru* kartu, nebo spuštěním následujících pomocí příkazového prostředí služby místním počítači:</span><span class="sxs-lookup"><span data-stu-id="fb71a-267">Use either the **Changes** page in Visual Studio's *Team Explorer* tab, or execute the following using the local machine's command shell:</span></span>

    ```console
    git commit -a -m "upgraded to V4"
    ```
1. <span data-ttu-id="fb71a-268">Nahrát změnu *hlavní* větvit do *původu* vzdálené úložiště GitHub:</span><span class="sxs-lookup"><span data-stu-id="fb71a-268">Push the change in the *master* branch to the *origin* remote of your GitHub repository:</span></span>

    ```console
    git push origin master
    ```

    <span data-ttu-id="fb71a-269">Zobrazí se potvrzení změn v úložišti GitHub *hlavní* větev:</span><span class="sxs-lookup"><span data-stu-id="fb71a-269">The commit appears in the GitHub repository's *master* branch:</span></span>

    ![Potvrzení Githubu v hlavní větvi](media/cicd/github-commit.png)

    <span data-ttu-id="fb71a-271">Sestavení se aktivuje, protože v definici sestavení je povolená Nepřetržitá integrace **triggery** kartu:</span><span class="sxs-lookup"><span data-stu-id="fb71a-271">The build is triggered, since continuous integration is enabled in the build definition's **Triggers** tab:</span></span>

    ![Povolit průběžnou integraci](media/cicd/enable-ci.png)

1. <span data-ttu-id="fb71a-273">Přejděte **zařazeno do fronty** kartě **kanály Azure** > **sestavení** stránku služby Azure DevOps.</span><span class="sxs-lookup"><span data-stu-id="fb71a-273">Navigate to the **Queued** tab of the **Azure Pipelines** > **Builds** page in Azure DevOps Services.</span></span> <span data-ttu-id="fb71a-274">Sestavení zařazené do fronty ukazuje větve a potvrzení změn, které aktivuje sestavení:</span><span class="sxs-lookup"><span data-stu-id="fb71a-274">The queued build shows the branch and commit that triggered the build:</span></span>

    ![sestavení zařazené do fronty](media/cicd/build-queued.png)

1. <span data-ttu-id="fb71a-276">Po úspěšném sestavení, dojde k nasazení do Azure.</span><span class="sxs-lookup"><span data-stu-id="fb71a-276">Once the build succeeds, a deployment to Azure occurs.</span></span> <span data-ttu-id="fb71a-277">Přejděte do aplikace v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="fb71a-277">Navigate to the app in the browser.</span></span> <span data-ttu-id="fb71a-278">Všimněte si, že se zobrazí text "V4" v nadpisu:</span><span class="sxs-lookup"><span data-stu-id="fb71a-278">Notice that the "V4" text appears in the heading:</span></span>

    ![aktualizovaná aplikace](media/cicd/updated-app-v4.png)

## <a name="examine-the-azure-pipelines-pipeline"></a><span data-ttu-id="fb71a-280">Prozkoumejte Azure kanály kanálu</span><span class="sxs-lookup"><span data-stu-id="fb71a-280">Examine the Azure Pipelines pipeline</span></span>

### <a name="build-definition"></a><span data-ttu-id="fb71a-281">Definice sestavení</span><span class="sxs-lookup"><span data-stu-id="fb71a-281">Build definition</span></span>

<span data-ttu-id="fb71a-282">Definice sestavení byla vytvořena s názvem *MyFirstProject ASP.NET Core-CI*.</span><span class="sxs-lookup"><span data-stu-id="fb71a-282">A build definition was created with the name *MyFirstProject-ASP.NET Core-CI*.</span></span> <span data-ttu-id="fb71a-283">Po dokončení sestavení vytváří *ZIP* souboru, včetně prostředků má být publikován.</span><span class="sxs-lookup"><span data-stu-id="fb71a-283">Upon completion, the build produces a *.zip* file including the assets to be published.</span></span> <span data-ttu-id="fb71a-284">Kanál pro vydávání verzí nasadí tyto prostředky do Azure.</span><span class="sxs-lookup"><span data-stu-id="fb71a-284">The release pipeline deploys those assets to Azure.</span></span>

<span data-ttu-id="fb71a-285">Definice sestavení **úlohy** karta obsahuje seznam jednotlivých kroků, které se používají.</span><span class="sxs-lookup"><span data-stu-id="fb71a-285">The build definition's **Tasks** tab lists the individual steps being used.</span></span> <span data-ttu-id="fb71a-286">Existuje pět úloh sestavení.</span><span class="sxs-lookup"><span data-stu-id="fb71a-286">There are five build tasks.</span></span>

![definice úlohy sestavení](media/cicd/build-definition-tasks.png)

1. <span data-ttu-id="fb71a-288">**Obnovení** &mdash; Executes `dotnet restore` příkaz k obnovení balíčků NuGet aplikace.</span><span class="sxs-lookup"><span data-stu-id="fb71a-288">**Restore** &mdash; Executes the `dotnet restore` command to restore the app's NuGet packages.</span></span> <span data-ttu-id="fb71a-289">Výchozí balíček informační kanál používá je nuget.org.</span><span class="sxs-lookup"><span data-stu-id="fb71a-289">The default package feed used is nuget.org.</span></span>
1. <span data-ttu-id="fb71a-290">**Sestavení** &mdash; Executes `dotnet build --configuration release` příkaz pro kompilaci kódu aplikace.</span><span class="sxs-lookup"><span data-stu-id="fb71a-290">**Build** &mdash; Executes the `dotnet build --configuration release` command to compile the app's code.</span></span> <span data-ttu-id="fb71a-291">To `--configuration` možnost se používá k vytvoření optimalizované verzi kódu, který je vhodný pro nasazení do produkčního prostředí.</span><span class="sxs-lookup"><span data-stu-id="fb71a-291">This `--configuration` option is used to produce an optimized version of the code, which is suitable for deployment to a production environment.</span></span> <span data-ttu-id="fb71a-292">Upravit *BuildConfiguration* proměnné na definici sestavení **proměnné** kartu podle potřeby, například konfigurace ladění je.</span><span class="sxs-lookup"><span data-stu-id="fb71a-292">Modify the *BuildConfiguration* variable on the build definition's **Variables** tab if, for example, a debug configuration is needed.</span></span>
1. <span data-ttu-id="fb71a-293">**Test** &mdash; Executes `dotnet test --configuration release --logger trx --results-directory <local_path_on_build_agent>` příkaz pro spuštění testů jednotek aplikace.</span><span class="sxs-lookup"><span data-stu-id="fb71a-293">**Test** &mdash; Executes the `dotnet test --configuration release --logger trx --results-directory <local_path_on_build_agent>` command to run the app's unit tests.</span></span> <span data-ttu-id="fb71a-294">Jednotkové testy jsou spouštěny v rámci jakékoli C# projekt odpovídající `**/*Tests/*.csproj` glob vzor.</span><span class="sxs-lookup"><span data-stu-id="fb71a-294">Unit tests are executed within any C# project matching the `**/*Tests/*.csproj` glob pattern.</span></span> <span data-ttu-id="fb71a-295">Výsledky testu jsou uloženy v *.trx* soubor v místě určeném `--results-directory` možnost.</span><span class="sxs-lookup"><span data-stu-id="fb71a-295">Test results are saved in a *.trx* file at the location specified by the `--results-directory` option.</span></span> <span data-ttu-id="fb71a-296">Pokud selžou i všechny testy, sestavení selže a není nasazený.</span><span class="sxs-lookup"><span data-stu-id="fb71a-296">If any tests fail, the build fails and isn't deployed.</span></span>

    > [!NOTE]
    > <span data-ttu-id="fb71a-297">Chcete-li ověřit pracovní jednotky testů, upravte *SimpleFeedReader.Tests\Services\NewsServiceTests.cs* záměrně přerušení jednoho z testů.</span><span class="sxs-lookup"><span data-stu-id="fb71a-297">To verify the unit tests work, modify *SimpleFeedReader.Tests\Services\NewsServiceTests.cs* to purposefully break one of the tests.</span></span> <span data-ttu-id="fb71a-298">Například změnit `Assert.True(result.Count > 0);` k `Assert.False(result.Count > 0);` v `Returns_News_Stories_Given_Valid_Uri` metody.</span><span class="sxs-lookup"><span data-stu-id="fb71a-298">For example, change `Assert.True(result.Count > 0);` to `Assert.False(result.Count > 0);` in the `Returns_News_Stories_Given_Valid_Uri` method.</span></span> <span data-ttu-id="fb71a-299">Potvrďte a odešlete změny na Githubu.</span><span class="sxs-lookup"><span data-stu-id="fb71a-299">Commit and push the change to GitHub.</span></span> <span data-ttu-id="fb71a-300">Sestavení se aktivuje a selže.</span><span class="sxs-lookup"><span data-stu-id="fb71a-300">The build is triggered and fails.</span></span> <span data-ttu-id="fb71a-301">Stav kanálu sestavení se změní na **nepovedlo**.</span><span class="sxs-lookup"><span data-stu-id="fb71a-301">The build pipeline status changes to **failed**.</span></span> <span data-ttu-id="fb71a-302">Vrácení změn, potvrzení a nabízených oznámení znovu.</span><span class="sxs-lookup"><span data-stu-id="fb71a-302">Revert the change, commit, and push again.</span></span> <span data-ttu-id="fb71a-303">Sestavení úspěšné.</span><span class="sxs-lookup"><span data-stu-id="fb71a-303">The build succeeds.</span></span>

1. <span data-ttu-id="fb71a-304">**Publikování** &mdash; Executes `dotnet publish --configuration release --output <local_path_on_build_agent>` příkazu *ZIP* soubor s artefakty, které mají být nasazeny.</span><span class="sxs-lookup"><span data-stu-id="fb71a-304">**Publish** &mdash; Executes the `dotnet publish --configuration release --output <local_path_on_build_agent>` command to produce a *.zip* file with the artifacts to be deployed.</span></span> <span data-ttu-id="fb71a-305">`--output` Určuje umístění pro publikování aplikace *ZIP* souboru.</span><span class="sxs-lookup"><span data-stu-id="fb71a-305">The `--output` option specifies the publish location of the *.zip* file.</span></span> <span data-ttu-id="fb71a-306">Zda je zadáno umístění předáním [předdefinované proměnné](/azure/devops/pipelines/build/variables) s názvem `$(build.artifactstagingdirectory)`.</span><span class="sxs-lookup"><span data-stu-id="fb71a-306">That location is specified by passing a [predefined variable](/azure/devops/pipelines/build/variables) named `$(build.artifactstagingdirectory)`.</span></span> <span data-ttu-id="fb71a-307">Tato proměnná rozšíří na místní cestu, například *c:\agent\_work\1\a*, agenta sestavení.</span><span class="sxs-lookup"><span data-stu-id="fb71a-307">That variable expands to a local path, such as *c:\agent\_work\1\a*, on the build agent.</span></span>
1. <span data-ttu-id="fb71a-308">**Publikování artefaktů** &mdash; Publishes *ZIP* vytvářených souborů **publikovat** úloh.</span><span class="sxs-lookup"><span data-stu-id="fb71a-308">**Publish Artifact** &mdash; Publishes the *.zip* file produced by the **Publish** task.</span></span> <span data-ttu-id="fb71a-309">Úloha přijímá *ZIP* umístění jako parametr, což je předdefinovaná proměnná souboru `$(build.artifactstagingdirectory)`.</span><span class="sxs-lookup"><span data-stu-id="fb71a-309">The task accepts the *.zip* file location as a parameter, which is the predefined variable `$(build.artifactstagingdirectory)`.</span></span> <span data-ttu-id="fb71a-310">*ZIP* soubor je publikován jako složku s názvem *vyřadit*.</span><span class="sxs-lookup"><span data-stu-id="fb71a-310">The *.zip* file is published as a folder named *drop*.</span></span>

<span data-ttu-id="fb71a-311">Klikněte na definici sestavení **Souhrn** odkaz k zobrazení historie sestavení s definicí:</span><span class="sxs-lookup"><span data-stu-id="fb71a-311">Click the build definition's **Summary** link to view a history of builds with the definition:</span></span>

![v historii definic sestavení](media/cicd/build-definition-summary.png)

<span data-ttu-id="fb71a-313">Na stránce výsledný kliknutím na odkaz odpovídající číslu jedinečný sestavení:</span><span class="sxs-lookup"><span data-stu-id="fb71a-313">On the resulting page, click the link corresponding to the unique build number:</span></span>

![Stránka souhrnu definice sestavení](media/cicd/build-definition-completed.png)

<span data-ttu-id="fb71a-315">Zobrazí se přehled tohoto konkrétního sestavení.</span><span class="sxs-lookup"><span data-stu-id="fb71a-315">A summary of this specific build is displayed.</span></span> <span data-ttu-id="fb71a-316">Klikněte na tlačítko **artefakty** kartu a Všimněte si, že *vyřadit* vytvořený sestavením složka se zobrazí:</span><span class="sxs-lookup"><span data-stu-id="fb71a-316">Click the **Artifacts** tab, and notice the *drop* folder produced by the build is listed:</span></span>

![definice artefaktů - odkládací složky sestavení](media/cicd/build-definition-artifacts.png)

<span data-ttu-id="fb71a-318">Použití **Stáhnout** a **prozkoumat** odkazů ke kontrole publikované artefakty.</span><span class="sxs-lookup"><span data-stu-id="fb71a-318">Use the **Download** and **Explore** links to inspect the published artifacts.</span></span>

### <a name="release-pipeline"></a><span data-ttu-id="fb71a-319">Kanál pro vydávání verzí</span><span class="sxs-lookup"><span data-stu-id="fb71a-319">Release pipeline</span></span>

<span data-ttu-id="fb71a-320">Kanál pro vydávání verzí byl vytvořen s názvem *MyFirstProject ASP.NET Core-CD*:</span><span class="sxs-lookup"><span data-stu-id="fb71a-320">A release pipeline was created with the name *MyFirstProject-ASP.NET Core-CD*:</span></span>

![Přehled profilace vydaných verzí](media/cicd/release-definition-overview.png)

<span data-ttu-id="fb71a-322">Jsou dvě hlavní součásti procesu vydávání verzí **artefakty** a **prostředí**.</span><span class="sxs-lookup"><span data-stu-id="fb71a-322">The two major components of the release pipeline are the **Artifacts** and the **Environments**.</span></span> <span data-ttu-id="fb71a-323">Kliknutím na pole v **artefakty** odhalí panelu následující části:</span><span class="sxs-lookup"><span data-stu-id="fb71a-323">Clicking the box in the **Artifacts** section reveals the following panel:</span></span>

![kanál artefaktům vydané verze](media/cicd/release-definition-artifacts.png)

<span data-ttu-id="fb71a-325">**Zdroj (definice sestavení)** hodnota představuje definici sestavení, se kterým je spojen tento kanál pro vydávání verzí.</span><span class="sxs-lookup"><span data-stu-id="fb71a-325">The **Source (Build definition)** value represents the build definition to which this release pipeline is linked.</span></span> <span data-ttu-id="fb71a-326">*ZIP* soubor vytvořený úspěšného spuštění definice sestavení se poskytuje *produkční* prostředí pro nasazení do Azure.</span><span class="sxs-lookup"><span data-stu-id="fb71a-326">The *.zip* file produced by a successful run of the build definition is provided to the *Production* environment for deployment to Azure.</span></span> <span data-ttu-id="fb71a-327">Klikněte na tlačítko *fáze 1, 2 úlohy* odkaz v *produkční* pole prostředí zobrazíte uvolnění úloh kanálu:</span><span class="sxs-lookup"><span data-stu-id="fb71a-327">Click the *1 phase, 2 tasks* link in the *Production* environment box to view the release pipeline tasks:</span></span>

![úkoly uvolnění kanálu](media/cicd/release-definition-tasks.png)

<span data-ttu-id="fb71a-329">Kanál pro vydávání verzí se skládá ze dvou úloh: *nasazení služby Azure App Service do slotu* a *Správa služby Azure App Service – Prohodit Slot*.</span><span class="sxs-lookup"><span data-stu-id="fb71a-329">The release pipeline consists of two tasks: *Deploy Azure App Service to Slot* and *Manage Azure App Service - Slot Swap*.</span></span> <span data-ttu-id="fb71a-330">Kliknutím na první úkol zobrazí následující konfigurace úlohy:</span><span class="sxs-lookup"><span data-stu-id="fb71a-330">Clicking the first task reveals the following task configuration:</span></span>

![Úloha nasazení kanálu pro vydávání verzí](media/cicd/release-definition-task1.png)

<span data-ttu-id="fb71a-332">Předplatné Azure, typ služby, název webové aplikace, skupiny prostředků a slot pro nasazení jsou definovány v úlohu nasazení.</span><span class="sxs-lookup"><span data-stu-id="fb71a-332">The Azure subscription, service type, web app name, resource group, and deployment slot are defined in the deployment task.</span></span> <span data-ttu-id="fb71a-333">**Balíčku nebo složky** obsahuje textové pole *ZIP* cesta k souboru extrahována a nasazené do *pracovní* pozici *mywebapp\<jedinečný sez_namu posledních použitých\>*  webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="fb71a-333">The **Package or folder** textbox holds the *.zip* file path to be extracted and deployed to the *staging* slot of the *mywebapp\<unique_number\>* web app.</span></span>

<span data-ttu-id="fb71a-334">Klepnutím na úkol, slot swap, zobrazí se následující konfigurace úlohy:</span><span class="sxs-lookup"><span data-stu-id="fb71a-334">Clicking the slot swap task reveals the following task configuration:</span></span>

![verze kanálu slotu prohození úloh](media/cicd/release-definition-task2.png)

<span data-ttu-id="fb71a-336">Předplatné, skupinu prostředků, typ služby, název webové aplikace a podrobnosti o slot nasazení jsou k dispozici.</span><span class="sxs-lookup"><span data-stu-id="fb71a-336">The subscription, resource group, service type, web app name, and deployment slot details are provided.</span></span> <span data-ttu-id="fb71a-337">**Prohodit s produkčním** zaškrtávací políčko zaškrtnuto.</span><span class="sxs-lookup"><span data-stu-id="fb71a-337">The **Swap with Production** check box is checked.</span></span> <span data-ttu-id="fb71a-338">V důsledku toho nasazené bity *pracovní* do produkčního prostředí se Prohodit slot.</span><span class="sxs-lookup"><span data-stu-id="fb71a-338">Consequently, the bits deployed to the *staging* slot are swapped into the production environment.</span></span>

## <a name="additional-reading"></a><span data-ttu-id="fb71a-339">Další čtení</span><span class="sxs-lookup"><span data-stu-id="fb71a-339">Additional reading</span></span>

* [<span data-ttu-id="fb71a-340">Vytvořit svůj první kanál s kanály Azure</span><span class="sxs-lookup"><span data-stu-id="fb71a-340">Create your first pipeline with Azure Pipelines</span></span>](/azure/devops/pipelines/get-started-yaml)
* [<span data-ttu-id="fb71a-341">Projekt pro sestavení a .NET Core</span><span class="sxs-lookup"><span data-stu-id="fb71a-341">Build and .NET Core project</span></span>](/azure/devops/pipelines/languages/dotnet-core)
* [<span data-ttu-id="fb71a-342">Nasazení webové aplikace s Azure kanály</span><span class="sxs-lookup"><span data-stu-id="fb71a-342">Deploy a web app with Azure Pipelines</span></span>](/azure/devops/pipelines/targets/webapp)
