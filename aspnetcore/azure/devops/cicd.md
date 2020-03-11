---
title: Průběžná integrace a nasazování – DevOps s využitím ASP.NET Core a Azure
author: CamSoper
description: Průběžná integrace a nasazování v DevOps s ASP.NET Core a Azure
ms.author: scaddie
ms.date: 10/24/2018
ms.custom: mvc, seodec18
uid: azure/devops/cicd
ms.openlocfilehash: 5fdf52235b49119503885f92c370dc588e809ffe
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78655832"
---
# <a name="continuous-integration-and-deployment"></a><span data-ttu-id="2249c-103">Průběžná integrace a nasazování</span><span class="sxs-lookup"><span data-stu-id="2249c-103">Continuous integration and deployment</span></span>

<span data-ttu-id="2249c-104">V předchozích kapitol vytvoříte místní úložiště Git pro aplikaci jednoduché Reader informačního kanálu.</span><span class="sxs-lookup"><span data-stu-id="2249c-104">In the previous chapter, you created a local Git repository for the Simple Feed Reader app.</span></span> <span data-ttu-id="2249c-105">V této kapitole budete publikovat tento kód do úložiště GitHub a vytvořit kanál DevOps služby Azure pomocí Azure kanálů.</span><span class="sxs-lookup"><span data-stu-id="2249c-105">In this chapter, you'll publish that code to a GitHub repository and construct an Azure DevOps Services pipeline using Azure Pipelines.</span></span> <span data-ttu-id="2249c-106">Kanál umožňuje průběžné vytváření buildů a nasazení aplikace.</span><span class="sxs-lookup"><span data-stu-id="2249c-106">The pipeline enables continuous builds and deployments of the app.</span></span> <span data-ttu-id="2249c-107">Každé potvrzení do úložiště GitHub se aktivuje sestavení a nasazení do přípravného slotu webové aplikace Azure.</span><span class="sxs-lookup"><span data-stu-id="2249c-107">Any commit to the GitHub repository triggers a build and a deployment to the Azure Web App's staging slot.</span></span>

<span data-ttu-id="2249c-108">V této části budete provádět následující úlohy:</span><span class="sxs-lookup"><span data-stu-id="2249c-108">In this section, you'll complete the following tasks:</span></span>

* <span data-ttu-id="2249c-109">Publikování aplikace kódu na Githubu</span><span class="sxs-lookup"><span data-stu-id="2249c-109">Publish the app's code to GitHub</span></span>
* <span data-ttu-id="2249c-110">Odpojit místní nasazení přes Git</span><span class="sxs-lookup"><span data-stu-id="2249c-110">Disconnect local Git deployment</span></span>
* <span data-ttu-id="2249c-111">Vytvořit organizaci Azure DevOps</span><span class="sxs-lookup"><span data-stu-id="2249c-111">Create an Azure DevOps organization</span></span>
* <span data-ttu-id="2249c-112">Vytvořit týmový projekt ve službách Azure DevOps</span><span class="sxs-lookup"><span data-stu-id="2249c-112">Create a team project in Azure DevOps Services</span></span>
* <span data-ttu-id="2249c-113">Vytvoření definice sestavení</span><span class="sxs-lookup"><span data-stu-id="2249c-113">Create a build definition</span></span>
* <span data-ttu-id="2249c-114">Vytvoření kanálu verze</span><span class="sxs-lookup"><span data-stu-id="2249c-114">Create a release pipeline</span></span>
* <span data-ttu-id="2249c-115">Potvrzení změn na GitHubu a automatické nasazení do Azure</span><span class="sxs-lookup"><span data-stu-id="2249c-115">Commit changes to GitHub and automatically deploy to Azure</span></span>
* <span data-ttu-id="2249c-116">Prozkoumejte Azure kanály kanálu</span><span class="sxs-lookup"><span data-stu-id="2249c-116">Examine the Azure Pipelines pipeline</span></span>

## <a name="publish-the-apps-code-to-github"></a><span data-ttu-id="2249c-117">Publikování aplikace kódu na Githubu</span><span class="sxs-lookup"><span data-stu-id="2249c-117">Publish the app's code to GitHub</span></span>

1. <span data-ttu-id="2249c-118">Otevřete okno prohlížeče a přejděte na `https://github.com`.</span><span class="sxs-lookup"><span data-stu-id="2249c-118">Open a browser window, and navigate to `https://github.com`.</span></span>
1. <span data-ttu-id="2249c-119">V hlavičce klikněte na rozevírací seznam **+** a vyberte **nové úložiště**:</span><span class="sxs-lookup"><span data-stu-id="2249c-119">Click the **+** drop-down in the header, and select **New repository**:</span></span>

    ![Možnost Nový úložiště GitHub](media/cicd/github-new-repo.png)

1. <span data-ttu-id="2249c-121">V rozevíracím seznamu **vlastník** vyberte svůj účet a do textového pole **název úložiště** zadejte *Simple-feed-Reader* .</span><span class="sxs-lookup"><span data-stu-id="2249c-121">Select your account in the **Owner** drop-down, and enter *simple-feed-reader* in the **Repository name** textbox.</span></span>
1. <span data-ttu-id="2249c-122">Klikněte na tlačítko **vytvořit úložiště** .</span><span class="sxs-lookup"><span data-stu-id="2249c-122">Click the **Create repository** button.</span></span>
1. <span data-ttu-id="2249c-123">Otevřete příkazové okno místního počítače.</span><span class="sxs-lookup"><span data-stu-id="2249c-123">Open your local machine's command shell.</span></span> <span data-ttu-id="2249c-124">Přejděte do adresáře, ve kterém je uloženo úložiště Git s *jednoduchým kanálem pro čtení* .</span><span class="sxs-lookup"><span data-stu-id="2249c-124">Navigate to the directory in which the *simple-feed-reader* Git repository is stored.</span></span>
1. <span data-ttu-id="2249c-125">Přejmenujte stávající vzdálený *zdroj* na *nadřazený*.</span><span class="sxs-lookup"><span data-stu-id="2249c-125">Rename the existing *origin* remote to *upstream*.</span></span> <span data-ttu-id="2249c-126">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="2249c-126">Execute the following command:</span></span>

    ```console
    git remote rename origin upstream
    ```

1. <span data-ttu-id="2249c-127">Přidejte na GitHubu nového *zdroje* , který se vzdáleně odkazuje na vaši kopii úložiště.</span><span class="sxs-lookup"><span data-stu-id="2249c-127">Add a new *origin* remote pointing to your copy of the repository on GitHub.</span></span> <span data-ttu-id="2249c-128">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="2249c-128">Execute the following command:</span></span>

    ```console
    git remote add origin https://github.com/<GitHub_username>/simple-feed-reader/
    ```

1. <span data-ttu-id="2249c-129">Publikování místního úložiště Git do nově vytvořené úložiště GitHub.</span><span class="sxs-lookup"><span data-stu-id="2249c-129">Publish your local Git repository to the newly created GitHub repository.</span></span> <span data-ttu-id="2249c-130">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="2249c-130">Execute the following command:</span></span>

    ```console
    git push -u origin master
    ```

1. <span data-ttu-id="2249c-131">Otevřete okno prohlížeče a přejděte na `https://github.com/<GitHub_username>/simple-feed-reader/`.</span><span class="sxs-lookup"><span data-stu-id="2249c-131">Open a browser window, and navigate to `https://github.com/<GitHub_username>/simple-feed-reader/`.</span></span> <span data-ttu-id="2249c-132">Ověřte, že váš kód se zobrazí v úložišti GitHub.</span><span class="sxs-lookup"><span data-stu-id="2249c-132">Validate that your code appears in the GitHub repository.</span></span>

## <a name="disconnect-local-git-deployment"></a><span data-ttu-id="2249c-133">Odpojit místní nasazení přes Git</span><span class="sxs-lookup"><span data-stu-id="2249c-133">Disconnect local Git deployment</span></span>

<span data-ttu-id="2249c-134">Odeberte místní nasazení přes Git pomocí následujícího postupu.</span><span class="sxs-lookup"><span data-stu-id="2249c-134">Remove the local Git deployment with the following steps.</span></span> <span data-ttu-id="2249c-135">Kanály Azure (služby Azure DevOps) nahrazuje a argumentech, které tuto funkci.</span><span class="sxs-lookup"><span data-stu-id="2249c-135">Azure Pipelines (an Azure DevOps service) both replaces and augments that functionality.</span></span>

1. <span data-ttu-id="2249c-136">Otevřete [Azure Portal](https://portal.azure.com/)a přejděte do pracovní webové aplikace *(mywebapp\<unique_number\>/staging)* .</span><span class="sxs-lookup"><span data-stu-id="2249c-136">Open the [Azure portal](https://portal.azure.com/), and navigate to the *staging (mywebapp\<unique_number\>/staging)* Web App.</span></span> <span data-ttu-id="2249c-137">Webovou aplikaci můžete rychle najít tak, že do vyhledávacího pole portálu zadáte *fázování* :</span><span class="sxs-lookup"><span data-stu-id="2249c-137">The Web App can be quickly located by entering *staging* in the portal's search box:</span></span>

    ![pracovní webové aplikace hledaný termín](media/cicd/portal-search-box.png)

1. <span data-ttu-id="2249c-139">Klikněte na **centrum nasazení**.</span><span class="sxs-lookup"><span data-stu-id="2249c-139">Click **Deployment Center**.</span></span> <span data-ttu-id="2249c-140">Otevře se nový panel.</span><span class="sxs-lookup"><span data-stu-id="2249c-140">A new panel appears.</span></span> <span data-ttu-id="2249c-141">Kliknutím na **Odpojit** odeberte konfiguraci místního řízení zdrojů Git, která se přidala do předchozí kapitoly.</span><span class="sxs-lookup"><span data-stu-id="2249c-141">Click **Disconnect** to remove the local Git source control configuration that was added in the previous chapter.</span></span> <span data-ttu-id="2249c-142">Kliknutím na tlačítko **Ano** potvrďte operaci odebrání.</span><span class="sxs-lookup"><span data-stu-id="2249c-142">Confirm the removal operation by clicking the **Yes** button.</span></span>
1. <span data-ttu-id="2249c-143">Přejděte do *mywebapp < unique_number >* App Service.</span><span class="sxs-lookup"><span data-stu-id="2249c-143">Navigate to the *mywebapp<unique_number>* App Service.</span></span> <span data-ttu-id="2249c-144">Připomínáme je možné k rychlému vyhledání služby App Service na portálu vyhledávacího pole.</span><span class="sxs-lookup"><span data-stu-id="2249c-144">As a reminder, the portal's search box can be used to quickly locate the App Service.</span></span>
1. <span data-ttu-id="2249c-145">Klikněte na **centrum nasazení**.</span><span class="sxs-lookup"><span data-stu-id="2249c-145">Click **Deployment Center**.</span></span> <span data-ttu-id="2249c-146">Otevře se nový panel.</span><span class="sxs-lookup"><span data-stu-id="2249c-146">A new panel appears.</span></span> <span data-ttu-id="2249c-147">Kliknutím na **Odpojit** odeberte konfiguraci místního řízení zdrojů Git, která se přidala do předchozí kapitoly.</span><span class="sxs-lookup"><span data-stu-id="2249c-147">Click **Disconnect** to remove the local Git source control configuration that was added in the previous chapter.</span></span> <span data-ttu-id="2249c-148">Kliknutím na tlačítko **Ano** potvrďte operaci odebrání.</span><span class="sxs-lookup"><span data-stu-id="2249c-148">Confirm the removal operation by clicking the **Yes** button.</span></span>

## <a name="create-an-azure-devops-organization"></a><span data-ttu-id="2249c-149">Vytvořit organizaci Azure DevOps</span><span class="sxs-lookup"><span data-stu-id="2249c-149">Create an Azure DevOps organization</span></span>

1. <span data-ttu-id="2249c-150">Otevřete prohlížeč a přejděte na [stránku pro vytvoření organizace Azure DevOps](https://go.microsoft.com/fwlink/?LinkId=307137).</span><span class="sxs-lookup"><span data-stu-id="2249c-150">Open a browser, and navigate to the [Azure DevOps organization creation page](https://go.microsoft.com/fwlink/?LinkId=307137).</span></span>
1. <span data-ttu-id="2249c-151">Do textového pole **Vybrat zapamatovatelné jméno** zadejte jedinečný název, který bude tvořit adresu URL pro přístup ke svojí organizaci Azure DevOps.</span><span class="sxs-lookup"><span data-stu-id="2249c-151">Type a unique name into the **Pick a memorable name** textbox to form the URL for accessing your Azure DevOps organization.</span></span>
1. <span data-ttu-id="2249c-152">Vyberte přepínač **Git** , protože je tento kód hostovaný v úložišti GitHubu.</span><span class="sxs-lookup"><span data-stu-id="2249c-152">Select the **Git** radio button, since the code is hosted in a GitHub repository.</span></span>
1. <span data-ttu-id="2249c-153">Klikněte na tlačítko **Pokračovat**.</span><span class="sxs-lookup"><span data-stu-id="2249c-153">Click the **Continue** button.</span></span> <span data-ttu-id="2249c-154">Po krátkém čekání se vytvoří účet a týmový projekt s názvem *MyFirstProject*.</span><span class="sxs-lookup"><span data-stu-id="2249c-154">After a short wait, an account and a team project, named *MyFirstProject*, are created.</span></span>

    ![Stránka Vytvořit organizaci Azure DevOps](media/cicd/vsts-account-creation.png)

1. <span data-ttu-id="2249c-156">Otevřete potvrzení e-mailu označující, že organizaci Azure DevOps a projektu jsou připravené k použití.</span><span class="sxs-lookup"><span data-stu-id="2249c-156">Open the confirmation email indicating that the Azure DevOps organization and project are ready for use.</span></span> <span data-ttu-id="2249c-157">Klikněte na tlačítko **spustit projekt** :</span><span class="sxs-lookup"><span data-stu-id="2249c-157">Click the **Start your project** button:</span></span>

    ![Váš projekt tlačítko Start](media/cicd/vsts-start-project.png)

1. <span data-ttu-id="2249c-159">Otevře se prohlížeč *\<account_name\>. VisualStudio.com*.</span><span class="sxs-lookup"><span data-stu-id="2249c-159">A browser opens to *\<account_name\>.visualstudio.com*.</span></span> <span data-ttu-id="2249c-160">Kliknutím na odkaz *MyFirstProject* zahajte konfiguraci kanálu DevOps projektu.</span><span class="sxs-lookup"><span data-stu-id="2249c-160">Click the *MyFirstProject* link to begin configuring the project's DevOps pipeline.</span></span>

## <a name="configure-the-azure-pipelines-pipeline"></a><span data-ttu-id="2249c-161">Nakonfigurujte kanál kanály Azure</span><span class="sxs-lookup"><span data-stu-id="2249c-161">Configure the Azure Pipelines pipeline</span></span>

<span data-ttu-id="2249c-162">Existují tři samostatné kroky k dokončení.</span><span class="sxs-lookup"><span data-stu-id="2249c-162">There are three distinct steps to complete.</span></span> <span data-ttu-id="2249c-163">Dokončením kroků v následujících třech částech vede provozní kanál DevOps.</span><span class="sxs-lookup"><span data-stu-id="2249c-163">Completing the steps in the following three sections results in an operational DevOps pipeline.</span></span>

### <a name="grant-azure-devops-access-to-the-github-repository"></a><span data-ttu-id="2249c-164">Azure DevOps udělit přístup k úložišti GitHub</span><span class="sxs-lookup"><span data-stu-id="2249c-164">Grant Azure DevOps access to the GitHub repository</span></span>

1. <span data-ttu-id="2249c-165">Rozbalte **nebo sestavte kód z přidaných externích úložišť** .</span><span class="sxs-lookup"><span data-stu-id="2249c-165">Expand the **or build code from an external repository** accordion.</span></span> <span data-ttu-id="2249c-166">Klikněte na tlačítko **sestavení pro instalaci** :</span><span class="sxs-lookup"><span data-stu-id="2249c-166">Click the **Setup Build** button:</span></span>

    ![Tlačítko Nastavení sestavení](media/cicd/vsts-setup-build.png)

1. <span data-ttu-id="2249c-168">V části **Vybrat zdroj** vyberte možnost **GitHub** :</span><span class="sxs-lookup"><span data-stu-id="2249c-168">Select the **GitHub** option from the **Select a source** section:</span></span>

    ![Vyberte zdroje – GitHub](media/cicd/vsts-select-source.png)

1. <span data-ttu-id="2249c-170">Vyžaduje se autorizace, než Azure DevOps můžete získat přístup k úložišti GitHub.</span><span class="sxs-lookup"><span data-stu-id="2249c-170">Authorization is required before Azure DevOps can access your GitHub repository.</span></span> <span data-ttu-id="2249c-171">Do textového pole **název připojení** zadejte *< GitHub_username > připojení GitHubu* .</span><span class="sxs-lookup"><span data-stu-id="2249c-171">Enter *<GitHub_username> GitHub connection* in the **Connection name** textbox.</span></span> <span data-ttu-id="2249c-172">Příklad:</span><span class="sxs-lookup"><span data-stu-id="2249c-172">For example:</span></span>

    ![Název připojení Githubu](media/cicd/vsts-repo-authz.png)

1. <span data-ttu-id="2249c-174">Pokud na vašem účtu GitHub je povoleno dvoufaktorové ověřování, osobní přístupový token je povinný.</span><span class="sxs-lookup"><span data-stu-id="2249c-174">If two-factor authentication is enabled on your GitHub account, a personal access token is required.</span></span> <span data-ttu-id="2249c-175">V takovém případě klikněte na odkaz **autorizovat pomocí osobního přístupového tokenu GitHubu** .</span><span class="sxs-lookup"><span data-stu-id="2249c-175">In that case, click the **Authorize with a GitHub personal access token** link.</span></span> <span data-ttu-id="2249c-176">Nápovědu najdete v [oficiálních pokynech k vytvoření tokenu GitHubu pro GitHub](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/) .</span><span class="sxs-lookup"><span data-stu-id="2249c-176">See the [official GitHub personal access token creation instructions](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/) for help.</span></span> <span data-ttu-id="2249c-177">Je potřeba jenom obor *úložiště* oprávnění.</span><span class="sxs-lookup"><span data-stu-id="2249c-177">Only the *repo* scope of permissions is needed.</span></span> <span data-ttu-id="2249c-178">V opačném případě klikněte na tlačítko **autorizovat pomocí OAuth** .</span><span class="sxs-lookup"><span data-stu-id="2249c-178">Otherwise, click the **Authorize using OAuth** button.</span></span>
1. <span data-ttu-id="2249c-179">Po zobrazení výzvy, přihlaste se k vašemu účtu GitHub.</span><span class="sxs-lookup"><span data-stu-id="2249c-179">When prompted, sign in to your GitHub account.</span></span> <span data-ttu-id="2249c-180">Vyberte Authorize k udělení přístupu k vaší organizaci Azure DevOps.</span><span class="sxs-lookup"><span data-stu-id="2249c-180">Then select Authorize to grant access to your Azure DevOps organization.</span></span> <span data-ttu-id="2249c-181">V případě úspěchu, je vytvořen nový koncový bod služby.</span><span class="sxs-lookup"><span data-stu-id="2249c-181">If successful, a new service endpoint is created.</span></span>
1. <span data-ttu-id="2249c-182">Klikněte na tlačítko se třemi tečkami vedle tlačítka **úložiště** .</span><span class="sxs-lookup"><span data-stu-id="2249c-182">Click the ellipsis button next to the **Repository** button.</span></span> <span data-ttu-id="2249c-183">V seznamu vyberte *< GitHub_username > úložiště/Simple-feed-Reader* .</span><span class="sxs-lookup"><span data-stu-id="2249c-183">Select the *<GitHub_username>/simple-feed-reader* repository from the list.</span></span> <span data-ttu-id="2249c-184">Klikněte na tlačítko **Vybrat** .</span><span class="sxs-lookup"><span data-stu-id="2249c-184">Click the **Select** button.</span></span>
1. <span data-ttu-id="2249c-185">Vyberte *Hlavní* větev z rozevíracího seznamu **výchozí větev pro ruční a plánovaná sestavení** .</span><span class="sxs-lookup"><span data-stu-id="2249c-185">Select the *master* branch from the **Default branch for manual and scheduled builds** drop-down.</span></span> <span data-ttu-id="2249c-186">Klikněte na tlačítko **Pokračovat**.</span><span class="sxs-lookup"><span data-stu-id="2249c-186">Click the **Continue** button.</span></span> <span data-ttu-id="2249c-187">Zobrazí se stránka pro výběr šablony.</span><span class="sxs-lookup"><span data-stu-id="2249c-187">The template selection page appears.</span></span>

### <a name="create-the-build-definition"></a><span data-ttu-id="2249c-188">Vytvořte definici sestavení</span><span class="sxs-lookup"><span data-stu-id="2249c-188">Create the build definition</span></span>

1. <span data-ttu-id="2249c-189">Na stránce Výběr šablony zadejte do vyhledávacího pole *ASP.NET Core* :</span><span class="sxs-lookup"><span data-stu-id="2249c-189">From the template selection page, enter *ASP.NET Core* in the search box:</span></span>

    ![ASP.NET Core hledání na stránce šablony](media/cicd/vsts-template-selection.png)

1. <span data-ttu-id="2249c-191">Šablona výsledky hledání zobrazeny.</span><span class="sxs-lookup"><span data-stu-id="2249c-191">The template search results appear.</span></span> <span data-ttu-id="2249c-192">Najeďte myší na šablonu **ASP.NET Core** a klikněte na tlačítko **použít** .</span><span class="sxs-lookup"><span data-stu-id="2249c-192">Hover over the **ASP.NET Core** template, and click the **Apply** button.</span></span>
1. <span data-ttu-id="2249c-193">Zobrazí se karta **úkoly** v definici sestavení.</span><span class="sxs-lookup"><span data-stu-id="2249c-193">The **Tasks** tab of the build definition appears.</span></span> <span data-ttu-id="2249c-194">Klikněte na kartu **triggery** .</span><span class="sxs-lookup"><span data-stu-id="2249c-194">Click the **Triggers** tab.</span></span>
1. <span data-ttu-id="2249c-195">Zaškrtněte políčko **Povolit průběžnou integraci** .</span><span class="sxs-lookup"><span data-stu-id="2249c-195">Check the **Enable continuous integration** box.</span></span> <span data-ttu-id="2249c-196">V části **filtry větví** potvrďte, že rozevírací seznam **typ** je nastavený na *Zahrnout*.</span><span class="sxs-lookup"><span data-stu-id="2249c-196">Under the **Branch filters** section, confirm that the **Type** drop-down is set to *Include*.</span></span> <span data-ttu-id="2249c-197">Nastavte rozevírací seznam **specifikace větve** na *hlavní*.</span><span class="sxs-lookup"><span data-stu-id="2249c-197">Set the **Branch specification** drop-down to *master*.</span></span>

    ![Povolit nastavení průběžné integrace](media/cicd/vsts-enable-ci.png)

    <span data-ttu-id="2249c-199">Tato nastavení způsobí, že se sestavení aktivuje při vložení jakékoli změny do *Hlavní* větve úložiště GitHub.</span><span class="sxs-lookup"><span data-stu-id="2249c-199">These settings cause a build to trigger when any change is pushed to the *master* branch of the GitHub repository.</span></span> <span data-ttu-id="2249c-200">Nepřetržitá integrace je testována v části [Potvrdit změny do GitHubu a automaticky se nasazuje do Azure](#commit-changes-to-github-and-automatically-deploy-to-azure) .</span><span class="sxs-lookup"><span data-stu-id="2249c-200">Continuous integration is tested in the [Commit changes to GitHub and automatically deploy to Azure](#commit-changes-to-github-and-automatically-deploy-to-azure) section.</span></span>

1. <span data-ttu-id="2249c-201">Klikněte na tlačítko **uložit & frontu** a vyberte možnost **Uložit** :</span><span class="sxs-lookup"><span data-stu-id="2249c-201">Click the **Save & queue** button, and select the **Save** option:</span></span>

    ![Tlačítko Uložit](media/cicd/vsts-save-build.png)

1. <span data-ttu-id="2249c-203">Zobrazí se následující modální dialogové okno:</span><span class="sxs-lookup"><span data-stu-id="2249c-203">The following modal dialog appears:</span></span>

    ![Modální dialogové okno Uložit definici sestavení-](media/cicd/vsts-save-modal.png)

    <span data-ttu-id="2249c-205">Použijte výchozí složku *\\* a klikněte na tlačítko **Uložit** .</span><span class="sxs-lookup"><span data-stu-id="2249c-205">Use the default folder of *\\*, and click the **Save** button.</span></span>

### <a name="create-the-release-pipeline"></a><span data-ttu-id="2249c-206">Vytvořit kanál pro vydávání verzí</span><span class="sxs-lookup"><span data-stu-id="2249c-206">Create the release pipeline</span></span>

1. <span data-ttu-id="2249c-207">Klikněte na kartu **vydání** v týmovém projektu.</span><span class="sxs-lookup"><span data-stu-id="2249c-207">Click the **Releases** tab of your team project.</span></span> <span data-ttu-id="2249c-208">Klikněte na tlačítko **Nový kanál** .</span><span class="sxs-lookup"><span data-stu-id="2249c-208">Click the **New pipeline** button.</span></span>

    ![Karta – tlačítko Nová definice verze](media/cicd/vsts-new-release-definition.png)

    <span data-ttu-id="2249c-210">Otevře se podokno výběr šablony.</span><span class="sxs-lookup"><span data-stu-id="2249c-210">The template selection pane appears.</span></span>

1. <span data-ttu-id="2249c-211">Na stránce Výběr šablony zadejte do vyhledávacího pole *App Service* :</span><span class="sxs-lookup"><span data-stu-id="2249c-211">From the template selection page, enter *App Service* in the search box:</span></span>

    ![Verze kanálu šablony vyhledávacího pole](media/cicd/vsts-release-template-search.png)

1. <span data-ttu-id="2249c-213">Šablona výsledky hledání zobrazeny.</span><span class="sxs-lookup"><span data-stu-id="2249c-213">The template search results appear.</span></span> <span data-ttu-id="2249c-214">Najeďte myší na **nasazení Azure App Service se šablonou slotu** a klikněte na tlačítko **použít** .</span><span class="sxs-lookup"><span data-stu-id="2249c-214">Hover over the **Azure App Service Deployment with Slot** template, and click the **Apply** button.</span></span> <span data-ttu-id="2249c-215">Zobrazí se karta **kanál** kanálu vydání.</span><span class="sxs-lookup"><span data-stu-id="2249c-215">The **Pipeline** tab of the release pipeline appears.</span></span>

    ![Kanál pro vydávání verzí kartu kanálu](media/cicd/vsts-release-definition-pipeline.png)

1. <span data-ttu-id="2249c-217">Klikněte na tlačítko **Přidat** v poli **artefakty** .</span><span class="sxs-lookup"><span data-stu-id="2249c-217">Click the **Add** button in the **Artifacts** box.</span></span> <span data-ttu-id="2249c-218">Zobrazí se panel **Přidat artefakt** :</span><span class="sxs-lookup"><span data-stu-id="2249c-218">The **Add artifact** panel appears:</span></span>

    ![Kanál pro vydávání verzí – přidání artefaktu panelu](media/cicd/vsts-release-add-artifact.png)

1. <span data-ttu-id="2249c-220">Vyberte dlaždici **sestavení** z oddílu **typ zdroje** .</span><span class="sxs-lookup"><span data-stu-id="2249c-220">Select the **Build** tile from the **Source type** section.</span></span> <span data-ttu-id="2249c-221">Tento typ umožňuje nastavit odkazy kanál pro vydávání verzí této definici sestavení.</span><span class="sxs-lookup"><span data-stu-id="2249c-221">This type allows for the linking of the release pipeline to the build definition.</span></span>
1. <span data-ttu-id="2249c-222">V rozevíracím seznamu **projekt** vyberte *MyFirstProject* .</span><span class="sxs-lookup"><span data-stu-id="2249c-222">Select *MyFirstProject* from the **Project** drop-down.</span></span>
1. <span data-ttu-id="2249c-223">Z rozevíracího seznamu **zdroj (definice sestavení)** vyberte název definice sestavení, *MYFIRSTPROJECT-ASP.NET Core-CI*.</span><span class="sxs-lookup"><span data-stu-id="2249c-223">Select the build definition name, *MyFirstProject-ASP.NET Core-CI*, from the **Source (Build definition)** drop-down.</span></span>
1. <span data-ttu-id="2249c-224">Z rozevíracího seznamu **výchozí verze** vyberte *nejnovější* .</span><span class="sxs-lookup"><span data-stu-id="2249c-224">Select *Latest* from the **Default version** drop-down.</span></span> <span data-ttu-id="2249c-225">Tato možnost sestavení artefakty vytvořené spuštěním nejnovější definice sestavení.</span><span class="sxs-lookup"><span data-stu-id="2249c-225">This option builds the artifacts produced by the latest run of the build definition.</span></span>
1. <span data-ttu-id="2249c-226">Text v textovém poli **aliasu zdroje** nahraďte textem *drop*.</span><span class="sxs-lookup"><span data-stu-id="2249c-226">Replace the text in the **Source alias** textbox with *Drop*.</span></span>
1. <span data-ttu-id="2249c-227">Klikněte na tlačítko **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="2249c-227">Click the **Add** button.</span></span> <span data-ttu-id="2249c-228">Část **artefakty** aktualizuje, aby se zobrazily změny.</span><span class="sxs-lookup"><span data-stu-id="2249c-228">The **Artifacts** section updates to display the changes.</span></span>
1. <span data-ttu-id="2249c-229">Klikněte na ikonu blesku povolit nepřetržité nasazení:</span><span class="sxs-lookup"><span data-stu-id="2249c-229">Click the lightning bolt icon to enable continuous deployments:</span></span>

    ![Kanál pro vydávání verzí artefakty – ikona blesku](media/cicd/vsts-artifacts-lightning-bolt.png)

    <span data-ttu-id="2249c-231">Tato možnost povolená dojde k nasazení pokaždé, když je k dispozici nové sestavení.</span><span class="sxs-lookup"><span data-stu-id="2249c-231">With this option enabled, a deployment occurs each time a new build is available.</span></span>
1. <span data-ttu-id="2249c-232">Napravo se zobrazí panel **Trigger průběžného nasazování** .</span><span class="sxs-lookup"><span data-stu-id="2249c-232">A **Continuous deployment trigger** panel appears to the right.</span></span> <span data-ttu-id="2249c-233">Klikněte na přepínací tlačítko k povolení této funkce.</span><span class="sxs-lookup"><span data-stu-id="2249c-233">Click the toggle button to enable the feature.</span></span> <span data-ttu-id="2249c-234">Není nutné povolit **Trigger žádosti o získání dat**.</span><span class="sxs-lookup"><span data-stu-id="2249c-234">It isn't necessary to enable the **Pull request trigger**.</span></span>
1. <span data-ttu-id="2249c-235">V části **filtry větve sestavení** klikněte na rozevírací nabídku **Přidat** .</span><span class="sxs-lookup"><span data-stu-id="2249c-235">Click the **Add** drop-down in the **Build branch filters** section.</span></span> <span data-ttu-id="2249c-236">Vyberte možnost **výchozí větev definice sestavení** .</span><span class="sxs-lookup"><span data-stu-id="2249c-236">Choose the **Build Definition's default branch** option.</span></span> <span data-ttu-id="2249c-237">Tento filtr způsobí, že se vydaná verze aktivuje jenom pro sestavení z *Hlavní* větve úložiště GitHub.</span><span class="sxs-lookup"><span data-stu-id="2249c-237">This filter causes the release to trigger only for a build from the GitHub repository's *master* branch.</span></span>
1. <span data-ttu-id="2249c-238">Klikněte na tlačítko **Uložit**.</span><span class="sxs-lookup"><span data-stu-id="2249c-238">Click the **Save** button.</span></span> <span data-ttu-id="2249c-239">Klikněte na tlačítko **OK** v dialogovém okně výsledný dialog **Uložit** modální.</span><span class="sxs-lookup"><span data-stu-id="2249c-239">Click the **OK** button in the resulting **Save** modal dialog.</span></span>
1. <span data-ttu-id="2249c-240">Klikněte na pole **prostředí 1** .</span><span class="sxs-lookup"><span data-stu-id="2249c-240">Click the **Environment 1** box.</span></span> <span data-ttu-id="2249c-241">Napravo se zobrazí panel **prostředí** .</span><span class="sxs-lookup"><span data-stu-id="2249c-241">An **Environment** panel appears to the right.</span></span> <span data-ttu-id="2249c-242">Změňte text *prostředí 1* v textovém poli **Název prostředí** na *produkční*.</span><span class="sxs-lookup"><span data-stu-id="2249c-242">Change the *Environment 1* text in the **Environment name** textbox to *Production*.</span></span>

   ![Kanál pro vydávání verzí – textové pole pro název prostředí](media/cicd/vsts-environment-name-textbox.png)

1. <span data-ttu-id="2249c-244">V **produkčním** poli klikněte na odkaz **1 fáze, 2 úlohy** :</span><span class="sxs-lookup"><span data-stu-id="2249c-244">Click the **1 phase, 2 tasks** link in the **Production** box:</span></span>

    ![Kanál pro vydávání verzí - link.png produkční prostředí](media/cicd/vsts-production-link.png)

    <span data-ttu-id="2249c-246">Zobrazí se karta **úlohy** v prostředí.</span><span class="sxs-lookup"><span data-stu-id="2249c-246">The **Tasks** tab of the environment appears.</span></span>
1. <span data-ttu-id="2249c-247">Klikněte na úlohu **nasadit Azure App Service pro slot** .</span><span class="sxs-lookup"><span data-stu-id="2249c-247">Click the **Deploy Azure App Service to Slot** task.</span></span> <span data-ttu-id="2249c-248">Nastavení se zobrazí v panelu napravo.</span><span class="sxs-lookup"><span data-stu-id="2249c-248">Its settings appear in a panel to the right.</span></span>
1. <span data-ttu-id="2249c-249">Z rozevíracího seznamu **předplatné Azure** vyberte předplatné Azure přidružené k tomuto App Service.</span><span class="sxs-lookup"><span data-stu-id="2249c-249">Select the Azure subscription associated with the App Service from the **Azure subscription** drop-down.</span></span> <span data-ttu-id="2249c-250">Po výběru klikněte na tlačítko **autorizovat** .</span><span class="sxs-lookup"><span data-stu-id="2249c-250">Once selected, click the **Authorize** button.</span></span>
1. <span data-ttu-id="2249c-251">V rozevíracím seznamu **Typ aplikace** vyberte možnost *Webová aplikace* .</span><span class="sxs-lookup"><span data-stu-id="2249c-251">Select *Web App* from the **App type** drop-down.</span></span>
1. <span data-ttu-id="2249c-252">V rozevíracím seznamu **název služby App Service** vyberte *mywebapp/< unique_number/>* .</span><span class="sxs-lookup"><span data-stu-id="2249c-252">Select *mywebapp/<unique_number/>* from the **App service name** drop-down.</span></span>
1. <span data-ttu-id="2249c-253">V rozevíracím seznamu **Skupina prostředků** vyberte *AzureTutorial* .</span><span class="sxs-lookup"><span data-stu-id="2249c-253">Select *AzureTutorial* from the **Resource group** drop-down.</span></span>
1. <span data-ttu-id="2249c-254">V rozevíracím seznamu **slot** vyberte *fázování* .</span><span class="sxs-lookup"><span data-stu-id="2249c-254">Select *staging* from the **Slot** drop-down.</span></span>
1. <span data-ttu-id="2249c-255">Klikněte na tlačítko **Uložit**.</span><span class="sxs-lookup"><span data-stu-id="2249c-255">Click the **Save** button.</span></span>
1. <span data-ttu-id="2249c-256">Najeďte myší výchozí název kanálu vydané verze.</span><span class="sxs-lookup"><span data-stu-id="2249c-256">Hover over the default release pipeline name.</span></span> <span data-ttu-id="2249c-257">Klikněte na ikonu tužky a upravte ho.</span><span class="sxs-lookup"><span data-stu-id="2249c-257">Click the pencil icon to edit it.</span></span> <span data-ttu-id="2249c-258">Jako název použijte *MyFirstProject-ASP.NET Core-CD* .</span><span class="sxs-lookup"><span data-stu-id="2249c-258">Use *MyFirstProject-ASP.NET Core-CD* as the name.</span></span>

    ![Název kanálu vydané verze](media/cicd/vsts-release-definition-name.png)

1. <span data-ttu-id="2249c-260">Klikněte na tlačítko **Uložit**.</span><span class="sxs-lookup"><span data-stu-id="2249c-260">Click the **Save** button.</span></span>

## <a name="commit-changes-to-github-and-automatically-deploy-to-azure"></a><span data-ttu-id="2249c-261">Potvrzení změn na GitHubu a automatické nasazení do Azure</span><span class="sxs-lookup"><span data-stu-id="2249c-261">Commit changes to GitHub and automatically deploy to Azure</span></span>

1. <span data-ttu-id="2249c-262">Otevřete *SimpleFeedReader. sln* v aplikaci Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="2249c-262">Open *SimpleFeedReader.sln* in Visual Studio.</span></span>
1. <span data-ttu-id="2249c-263">V Průzkumník řešení otevřete *Pages\Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="2249c-263">In Solution Explorer, open *Pages\Index.cshtml*.</span></span> <span data-ttu-id="2249c-264">Změňte `<h2>Simple Feed Reader - V3</h2>` na `<h2>Simple Feed Reader - V4</h2>`.</span><span class="sxs-lookup"><span data-stu-id="2249c-264">Change `<h2>Simple Feed Reader - V3</h2>` to `<h2>Simple Feed Reader - V4</h2>`.</span></span>
1. <span data-ttu-id="2249c-265">Stisknutím **kombinace kláves Ctrl**+**SHIFT**+**B** sestavte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="2249c-265">Press **Ctrl**+**Shift**+**B** to build the app.</span></span>
1. <span data-ttu-id="2249c-266">Potvrďte souboru do úložiště GitHub.</span><span class="sxs-lookup"><span data-stu-id="2249c-266">Commit the file to the GitHub repository.</span></span> <span data-ttu-id="2249c-267">Na kartě *Team Explorer* v aplikaci Visual Studio použijte stránku **změny** nebo pomocí příkazového prostředí místního počítače spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="2249c-267">Use either the **Changes** page in Visual Studio's *Team Explorer* tab, or execute the following using the local machine's command shell:</span></span>

    ```console
    git commit -a -m "upgraded to V4"
    ```

1. <span data-ttu-id="2249c-268">Nahrajte změnu v *Hlavní* větvi do *počátečního* vzdáleného úložiště GitHubu:</span><span class="sxs-lookup"><span data-stu-id="2249c-268">Push the change in the *master* branch to the *origin* remote of your GitHub repository:</span></span>

    ```console
    git push origin master
    ```

    <span data-ttu-id="2249c-269">Potvrzení se zobrazí v *Hlavní* větvi úložiště GitHub:</span><span class="sxs-lookup"><span data-stu-id="2249c-269">The commit appears in the GitHub repository's *master* branch:</span></span>

    ![Potvrzení Githubu v hlavní větvi](media/cicd/github-commit.png)

    <span data-ttu-id="2249c-271">Sestavení je aktivováno, protože je povolena průběžná integrace na kartě **aktivační události** definice sestavení:</span><span class="sxs-lookup"><span data-stu-id="2249c-271">The build is triggered, since continuous integration is enabled in the build definition's **Triggers** tab:</span></span>

    ![Povolit průběžnou integraci](media/cicd/enable-ci.png)

1. <span data-ttu-id="2249c-273">Na stránce **Azure Pipelines** > **sestavení** v Azure DevOps Services přejděte na kartu **zařazené do fronty** .</span><span class="sxs-lookup"><span data-stu-id="2249c-273">Navigate to the **Queued** tab of the **Azure Pipelines** > **Builds** page in Azure DevOps Services.</span></span> <span data-ttu-id="2249c-274">Sestavení zařazené do fronty ukazuje větve a potvrzení změn, které aktivuje sestavení:</span><span class="sxs-lookup"><span data-stu-id="2249c-274">The queued build shows the branch and commit that triggered the build:</span></span>

    ![sestavení zařazené do fronty](media/cicd/build-queued.png)

1. <span data-ttu-id="2249c-276">Po úspěšném sestavení, dojde k nasazení do Azure.</span><span class="sxs-lookup"><span data-stu-id="2249c-276">Once the build succeeds, a deployment to Azure occurs.</span></span> <span data-ttu-id="2249c-277">Přejděte do aplikace v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="2249c-277">Navigate to the app in the browser.</span></span> <span data-ttu-id="2249c-278">Všimněte si, že se zobrazí text "V4" v nadpisu:</span><span class="sxs-lookup"><span data-stu-id="2249c-278">Notice that the "V4" text appears in the heading:</span></span>

    ![aktualizovaná aplikace](media/cicd/updated-app-v4.png)

## <a name="examine-the-azure-pipelines-pipeline"></a><span data-ttu-id="2249c-280">Prozkoumejte Azure kanály kanálu</span><span class="sxs-lookup"><span data-stu-id="2249c-280">Examine the Azure Pipelines pipeline</span></span>

### <a name="build-definition"></a><span data-ttu-id="2249c-281">Definice sestavení</span><span class="sxs-lookup"><span data-stu-id="2249c-281">Build definition</span></span>

<span data-ttu-id="2249c-282">Byla vytvořena definice sestavení s názvem *MyFirstProject-ASP.NET Core-CI*.</span><span class="sxs-lookup"><span data-stu-id="2249c-282">A build definition was created with the name *MyFirstProject-ASP.NET Core-CI*.</span></span> <span data-ttu-id="2249c-283">Po dokončení sestavení vytvoří soubor *. zip* , včetně prostředků, které mají být publikovány.</span><span class="sxs-lookup"><span data-stu-id="2249c-283">Upon completion, the build produces a *.zip* file including the assets to be published.</span></span> <span data-ttu-id="2249c-284">Kanál pro vydávání verzí nasadí tyto prostředky do Azure.</span><span class="sxs-lookup"><span data-stu-id="2249c-284">The release pipeline deploys those assets to Azure.</span></span>

<span data-ttu-id="2249c-285">Na kartě **úkoly** definice sestavení jsou uvedeny jednotlivé kroky, které se používají.</span><span class="sxs-lookup"><span data-stu-id="2249c-285">The build definition's **Tasks** tab lists the individual steps being used.</span></span> <span data-ttu-id="2249c-286">Existuje pět úloh sestavení.</span><span class="sxs-lookup"><span data-stu-id="2249c-286">There are five build tasks.</span></span>

![definice úlohy sestavení](media/cicd/build-definition-tasks.png)

1. <span data-ttu-id="2249c-288">**Restore** &mdash; spustí příkaz `dotnet restore`, který obnoví balíčky NuGet aplikace.</span><span class="sxs-lookup"><span data-stu-id="2249c-288">**Restore** &mdash; Executes the `dotnet restore` command to restore the app's NuGet packages.</span></span> <span data-ttu-id="2249c-289">Výchozí balíček informační kanál používá je nuget.org.</span><span class="sxs-lookup"><span data-stu-id="2249c-289">The default package feed used is nuget.org.</span></span>
1. <span data-ttu-id="2249c-290">**Sestavování** &mdash; spustí příkaz `dotnet build --configuration release` pro zkompilování kódu aplikace.</span><span class="sxs-lookup"><span data-stu-id="2249c-290">**Build** &mdash; Executes the `dotnet build --configuration release` command to compile the app's code.</span></span> <span data-ttu-id="2249c-291">Tato možnost `--configuration` slouží k vytvoření optimalizované verze kódu, který je vhodný pro nasazení do produkčního prostředí.</span><span class="sxs-lookup"><span data-stu-id="2249c-291">This `--configuration` option is used to produce an optimized version of the code, which is suitable for deployment to a production environment.</span></span> <span data-ttu-id="2249c-292">Pokud je například nutná konfigurace ladění, upravte proměnnou *BuildConfiguration* na kartě **proměnné** v definici sestavení.</span><span class="sxs-lookup"><span data-stu-id="2249c-292">Modify the *BuildConfiguration* variable on the build definition's **Variables** tab if, for example, a debug configuration is needed.</span></span>
1. <span data-ttu-id="2249c-293">**Test** &mdash; spustí příkaz `dotnet test --configuration release --logger trx --results-directory <local_path_on_build_agent>` ke spuštění testů jednotek aplikace.</span><span class="sxs-lookup"><span data-stu-id="2249c-293">**Test** &mdash; Executes the `dotnet test --configuration release --logger trx --results-directory <local_path_on_build_agent>` command to run the app's unit tests.</span></span> <span data-ttu-id="2249c-294">Testy jednotek se spustí v rámci C# libovolného projektu, který odpovídá glob vzoru pro `**/*Tests/*.csproj`.</span><span class="sxs-lookup"><span data-stu-id="2249c-294">Unit tests are executed within any C# project matching the `**/*Tests/*.csproj` glob pattern.</span></span> <span data-ttu-id="2249c-295">Výsledky testů jsou uloženy v souboru *. TRX* v umístění určeném možností `--results-directory`.</span><span class="sxs-lookup"><span data-stu-id="2249c-295">Test results are saved in a *.trx* file at the location specified by the `--results-directory` option.</span></span> <span data-ttu-id="2249c-296">Pokud selžou i všechny testy, sestavení selže a není nasazený.</span><span class="sxs-lookup"><span data-stu-id="2249c-296">If any tests fail, the build fails and isn't deployed.</span></span>

    > [!NOTE]
    > <span data-ttu-id="2249c-297">Chcete-li ověřit testy jednotek, upravte *SimpleFeedReader. Tests\Services\NewsServiceTests.cs* tak, aby záměrně přerušení jednoho testu.</span><span class="sxs-lookup"><span data-stu-id="2249c-297">To verify the unit tests work, modify *SimpleFeedReader.Tests\Services\NewsServiceTests.cs* to purposefully break one of the tests.</span></span> <span data-ttu-id="2249c-298">Například změňte `Assert.True(result.Count > 0);` na `Assert.False(result.Count > 0);` v metodě `Returns_News_Stories_Given_Valid_Uri`.</span><span class="sxs-lookup"><span data-stu-id="2249c-298">For example, change `Assert.True(result.Count > 0);` to `Assert.False(result.Count > 0);` in the `Returns_News_Stories_Given_Valid_Uri` method.</span></span> <span data-ttu-id="2249c-299">Potvrďte a odešlete změny na Githubu.</span><span class="sxs-lookup"><span data-stu-id="2249c-299">Commit and push the change to GitHub.</span></span> <span data-ttu-id="2249c-300">Sestavení se aktivuje a selže.</span><span class="sxs-lookup"><span data-stu-id="2249c-300">The build is triggered and fails.</span></span> <span data-ttu-id="2249c-301">Stav kanálu sestavení se změní na **neúspěch**.</span><span class="sxs-lookup"><span data-stu-id="2249c-301">The build pipeline status changes to **failed**.</span></span> <span data-ttu-id="2249c-302">Vrácení změn, potvrzení a nabízených oznámení znovu.</span><span class="sxs-lookup"><span data-stu-id="2249c-302">Revert the change, commit, and push again.</span></span> <span data-ttu-id="2249c-303">Sestavení úspěšné.</span><span class="sxs-lookup"><span data-stu-id="2249c-303">The build succeeds.</span></span>

1. <span data-ttu-id="2249c-304">**Publikování** &mdash; spustí příkaz `dotnet publish --configuration release --output <local_path_on_build_agent>` a vytvoří soubor *. zip* s artefakty, které mají být nasazeny.</span><span class="sxs-lookup"><span data-stu-id="2249c-304">**Publish** &mdash; Executes the `dotnet publish --configuration release --output <local_path_on_build_agent>` command to produce a *.zip* file with the artifacts to be deployed.</span></span> <span data-ttu-id="2249c-305">Možnost `--output` určuje umístění publikování souboru *. zip* .</span><span class="sxs-lookup"><span data-stu-id="2249c-305">The `--output` option specifies the publish location of the *.zip* file.</span></span> <span data-ttu-id="2249c-306">Toto umístění je určeno předáním [předdefinované proměnné](/azure/devops/pipelines/build/variables) s názvem `$(build.artifactstagingdirectory)`.</span><span class="sxs-lookup"><span data-stu-id="2249c-306">That location is specified by passing a [predefined variable](/azure/devops/pipelines/build/variables) named `$(build.artifactstagingdirectory)`.</span></span> <span data-ttu-id="2249c-307">Tato proměnná se v agentovi sestavení rozšíří na místní cestu, například *c:\agent\_work\1\a*.</span><span class="sxs-lookup"><span data-stu-id="2249c-307">That variable expands to a local path, such as *c:\agent\_work\1\a*, on the build agent.</span></span>
1. <span data-ttu-id="2249c-308">**Publikování artefaktu** &mdash; publikuje soubor *. zip* vytvářený úlohou **Publish** .</span><span class="sxs-lookup"><span data-stu-id="2249c-308">**Publish Artifact** &mdash; Publishes the *.zip* file produced by the **Publish** task.</span></span> <span data-ttu-id="2249c-309">Úkol přijme umístění souboru *. zip* jako parametr, což je předdefinovaná proměnná `$(build.artifactstagingdirectory)`.</span><span class="sxs-lookup"><span data-stu-id="2249c-309">The task accepts the *.zip* file location as a parameter, which is the predefined variable `$(build.artifactstagingdirectory)`.</span></span> <span data-ttu-id="2249c-310">Soubor *. zip* je publikován jako složka s názvem *drop*.</span><span class="sxs-lookup"><span data-stu-id="2249c-310">The *.zip* file is published as a folder named *drop*.</span></span>

<span data-ttu-id="2249c-311">Kliknutím na **souhrnný** odkaz definice sestavení zobrazíte historii sestavení s definicí:</span><span class="sxs-lookup"><span data-stu-id="2249c-311">Click the build definition's **Summary** link to view a history of builds with the definition:</span></span>

![Snímek obrazovky znázorňující v historii definic sestavení](media/cicd/build-definition-summary.png)

<span data-ttu-id="2249c-313">Na stránce výsledný kliknutím na odkaz odpovídající číslu jedinečný sestavení:</span><span class="sxs-lookup"><span data-stu-id="2249c-313">On the resulting page, click the link corresponding to the unique build number:</span></span>

![Snímek obrazovky znázorňující definice stránce se souhrnem sestavení](media/cicd/build-definition-completed.png)

<span data-ttu-id="2249c-315">Zobrazí se přehled tohoto konkrétního sestavení.</span><span class="sxs-lookup"><span data-stu-id="2249c-315">A summary of this specific build is displayed.</span></span> <span data-ttu-id="2249c-316">Klikněte na kartu **artefakty** a Všimněte si, že je uvedena *ukládací* složka vytvořená sestavením:</span><span class="sxs-lookup"><span data-stu-id="2249c-316">Click the **Artifacts** tab, and notice the *drop* folder produced by the build is listed:</span></span>

![Snímek obrazovky s artefakty definice sestavení - odkládací složky](media/cicd/build-definition-artifacts.png)

<span data-ttu-id="2249c-318">Pomocí odkazů **Stáhnout** a **prozkoumat** můžete zkontrolovat publikované artefakty.</span><span class="sxs-lookup"><span data-stu-id="2249c-318">Use the **Download** and **Explore** links to inspect the published artifacts.</span></span>

### <a name="release-pipeline"></a><span data-ttu-id="2249c-319">Kanál verze</span><span class="sxs-lookup"><span data-stu-id="2249c-319">Release pipeline</span></span>

<span data-ttu-id="2249c-320">Byl vytvořen kanál vydané verze s názvem *MyFirstProject-ASP.NET Core-CD*:</span><span class="sxs-lookup"><span data-stu-id="2249c-320">A release pipeline was created with the name *MyFirstProject-ASP.NET Core-CD*:</span></span>

![Snímek obrazovky znázorňující verze kanálu přehled](media/cicd/release-definition-overview.png)

<span data-ttu-id="2249c-322">Mezi dvě hlavní součásti kanálu pro vydávání verzí patří **artefakty** a **prostředí**.</span><span class="sxs-lookup"><span data-stu-id="2249c-322">The two major components of the release pipeline are the **Artifacts** and the **Environments**.</span></span> <span data-ttu-id="2249c-323">Kliknutím na pole v části **artefakty** se odhalí následující panel:</span><span class="sxs-lookup"><span data-stu-id="2249c-323">Clicking the box in the **Artifacts** section reveals the following panel:</span></span>

![Snímek obrazovky znázorňující verze kanálu artefaktů](media/cicd/release-definition-artifacts.png)

<span data-ttu-id="2249c-325">Hodnota **zdroj (definice sestavení)** představuje definici sestavení, ke které je tento kanál verze propojený.</span><span class="sxs-lookup"><span data-stu-id="2249c-325">The **Source (Build definition)** value represents the build definition to which this release pipeline is linked.</span></span> <span data-ttu-id="2249c-326">Soubor *. zip* , který vygenerovalo úspěšné spuštění definice sestavení, je poskytován *provoznímu* prostředí pro nasazení do Azure.</span><span class="sxs-lookup"><span data-stu-id="2249c-326">The *.zip* file produced by a successful run of the build definition is provided to the *Production* environment for deployment to Azure.</span></span> <span data-ttu-id="2249c-327">Úkoly kanálu vydaných verzí zobrazíte kliknutím na odkaz *1 fáze, 2 úlohy* v poli *provozní* prostředí:</span><span class="sxs-lookup"><span data-stu-id="2249c-327">Click the *1 phase, 2 tasks* link in the *Production* environment box to view the release pipeline tasks:</span></span>

![Snímek obrazovky znázorňující verze kanálu úlohy](media/cicd/release-definition-tasks.png)

<span data-ttu-id="2249c-329">Kanál vydané verze se skládá ze dvou úloh: *nasazení Azure App Service do slotu* a *správa swapu Azure App Serviceho slotu*.</span><span class="sxs-lookup"><span data-stu-id="2249c-329">The release pipeline consists of two tasks: *Deploy Azure App Service to Slot* and *Manage Azure App Service - Slot Swap*.</span></span> <span data-ttu-id="2249c-330">Kliknutím na první úkol zobrazí následující konfigurace úlohy:</span><span class="sxs-lookup"><span data-stu-id="2249c-330">Clicking the first task reveals the following task configuration:</span></span>

![Úloha nasazení kanálu pro vydávání verzí – snímek obrazovky znázorňující](media/cicd/release-definition-task1.png)

<span data-ttu-id="2249c-332">Předplatné Azure, typ služby, název webové aplikace, skupiny prostředků a slot pro nasazení jsou definovány v úlohu nasazení.</span><span class="sxs-lookup"><span data-stu-id="2249c-332">The Azure subscription, service type, web app name, resource group, and deployment slot are defined in the deployment task.</span></span> <span data-ttu-id="2249c-333">Textové pole **balíčku nebo složky** obsahuje cestu k souboru *. zip* , která se má extrahovat a nasadit do *přípravného* slotu *MyWebApp\<unique_number\>* webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="2249c-333">The **Package or folder** textbox holds the *.zip* file path to be extracted and deployed to the *staging* slot of the *mywebapp\<unique_number\>* web app.</span></span>

<span data-ttu-id="2249c-334">Klepnutím na úkol, slot swap, zobrazí se následující konfigurace úlohy:</span><span class="sxs-lookup"><span data-stu-id="2249c-334">Clicking the slot swap task reveals the following task configuration:</span></span>

![Snímek obrazovky znázorňující uvolnění kanálu slotu prohození úlohy](media/cicd/release-definition-task2.png)

<span data-ttu-id="2249c-336">Předplatné, skupinu prostředků, typ služby, název webové aplikace a podrobnosti o slot nasazení jsou k dispozici.</span><span class="sxs-lookup"><span data-stu-id="2249c-336">The subscription, resource group, service type, web app name, and deployment slot details are provided.</span></span> <span data-ttu-id="2249c-337">Zaškrtávací políčko **swap s výrobou** je zaškrtnuté.</span><span class="sxs-lookup"><span data-stu-id="2249c-337">The **Swap with Production** check box is checked.</span></span> <span data-ttu-id="2249c-338">V důsledku toho se bity nasazené do *přípravného* slotu odsadí do produkčního prostředí.</span><span class="sxs-lookup"><span data-stu-id="2249c-338">Consequently, the bits deployed to the *staging* slot are swapped into the production environment.</span></span>

## <a name="additional-reading"></a><span data-ttu-id="2249c-339">Další čtení</span><span class="sxs-lookup"><span data-stu-id="2249c-339">Additional reading</span></span>

* [<span data-ttu-id="2249c-340">Vytvoření prvního kanálu pomocí Azure Pipelines</span><span class="sxs-lookup"><span data-stu-id="2249c-340">Create your first pipeline with Azure Pipelines</span></span>](/azure/devops/pipelines/get-started-yaml)
* [<span data-ttu-id="2249c-341">Projekt sestavení a .NET Core</span><span class="sxs-lookup"><span data-stu-id="2249c-341">Build and .NET Core project</span></span>](/azure/devops/pipelines/languages/dotnet-core)
* [<span data-ttu-id="2249c-342">Nasazení webové aplikace pomocí Azure Pipelines</span><span class="sxs-lookup"><span data-stu-id="2249c-342">Deploy a web app with Azure Pipelines</span></span>](/azure/devops/pipelines/targets/webapp)
