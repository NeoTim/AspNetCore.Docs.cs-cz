---
title: Průběžná integrace a nasazování – DevOps s využitím ASP.NET Core a Azure
author: CamSoper
description: Průběžná integrace a nasazování v DevOps s využitím ASP.NET Core a Azure
ms.author: scaddie
ms.date: 10/24/2018
ms.custom: mvc, seodec18
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: azure/devops/cicd
ms.openlocfilehash: 0edded18d766d6f2af08f6be5dbecbfd52a14a35
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2020
ms.locfileid: "85400554"
---
# <a name="continuous-integration-and-deployment"></a><span data-ttu-id="eb08a-103">Průběžná integrace a nasazování</span><span class="sxs-lookup"><span data-stu-id="eb08a-103">Continuous integration and deployment</span></span>

<span data-ttu-id="eb08a-104">V předchozí kapitole jste pro aplikaci pro čtení jednoduchých informačních kanálů vytvořili místní úložiště Git.</span><span class="sxs-lookup"><span data-stu-id="eb08a-104">In the previous chapter, you created a local Git repository for the Simple Feed Reader app.</span></span> <span data-ttu-id="eb08a-105">V této kapitole publikujete tento kód do úložiště GitHub a vytvoříte kanál Azure DevOps Services pomocí Azure Pipelines.</span><span class="sxs-lookup"><span data-stu-id="eb08a-105">In this chapter, you'll publish that code to a GitHub repository and construct an Azure DevOps Services pipeline using Azure Pipelines.</span></span> <span data-ttu-id="eb08a-106">Kanál umožňuje průběžné sestavení a nasazení aplikace.</span><span class="sxs-lookup"><span data-stu-id="eb08a-106">The pipeline enables continuous builds and deployments of the app.</span></span> <span data-ttu-id="eb08a-107">Jakékoli potvrzení do úložiště GitHubu aktivuje sestavení a nasazení do přípravného slotu webové aplikace Azure.</span><span class="sxs-lookup"><span data-stu-id="eb08a-107">Any commit to the GitHub repository triggers a build and a deployment to the Azure Web App's staging slot.</span></span>

<span data-ttu-id="eb08a-108">V této části provedete následující úlohy:</span><span class="sxs-lookup"><span data-stu-id="eb08a-108">In this section, you'll complete the following tasks:</span></span>

* <span data-ttu-id="eb08a-109">Publikování kódu aplikace do GitHubu</span><span class="sxs-lookup"><span data-stu-id="eb08a-109">Publish the app's code to GitHub</span></span>
* <span data-ttu-id="eb08a-110">Odpojit nasazení místního Gitu</span><span class="sxs-lookup"><span data-stu-id="eb08a-110">Disconnect local Git deployment</span></span>
* <span data-ttu-id="eb08a-111">Vytvoření organizace Azure DevOps</span><span class="sxs-lookup"><span data-stu-id="eb08a-111">Create an Azure DevOps organization</span></span>
* <span data-ttu-id="eb08a-112">Vytvoření týmového projektu v Azure DevOps Services</span><span class="sxs-lookup"><span data-stu-id="eb08a-112">Create a team project in Azure DevOps Services</span></span>
* <span data-ttu-id="eb08a-113">Vytvoření definice sestavení</span><span class="sxs-lookup"><span data-stu-id="eb08a-113">Create a build definition</span></span>
* <span data-ttu-id="eb08a-114">Vytvoření kanálu verze</span><span class="sxs-lookup"><span data-stu-id="eb08a-114">Create a release pipeline</span></span>
* <span data-ttu-id="eb08a-115">Potvrzení změn na GitHubu a automatické nasazení do Azure</span><span class="sxs-lookup"><span data-stu-id="eb08a-115">Commit changes to GitHub and automatically deploy to Azure</span></span>
* <span data-ttu-id="eb08a-116">Kontrola kanálu Azure Pipelines</span><span class="sxs-lookup"><span data-stu-id="eb08a-116">Examine the Azure Pipelines pipeline</span></span>

## <a name="publish-the-apps-code-to-github"></a><span data-ttu-id="eb08a-117">Publikování kódu aplikace do GitHubu</span><span class="sxs-lookup"><span data-stu-id="eb08a-117">Publish the app's code to GitHub</span></span>

1. <span data-ttu-id="eb08a-118">Otevřete okno prohlížeče a přejděte na `https://github.com` .</span><span class="sxs-lookup"><span data-stu-id="eb08a-118">Open a browser window, and navigate to `https://github.com`.</span></span>
1. <span data-ttu-id="eb08a-119">**+** V záhlaví klikněte na rozevírací nabídku a vyberte **nové úložiště**:</span><span class="sxs-lookup"><span data-stu-id="eb08a-119">Click the **+** drop-down in the header, and select **New repository**:</span></span>

    ![Možnost nového úložiště GitHubu](media/cicd/github-new-repo.png)

1. <span data-ttu-id="eb08a-121">V rozevíracím seznamu **vlastník** vyberte svůj účet a do textového pole **název úložiště** zadejte *Simple-feed-Reader* .</span><span class="sxs-lookup"><span data-stu-id="eb08a-121">Select your account in the **Owner** drop-down, and enter *simple-feed-reader* in the **Repository name** textbox.</span></span>
1. <span data-ttu-id="eb08a-122">Klikněte na tlačítko **vytvořit úložiště** .</span><span class="sxs-lookup"><span data-stu-id="eb08a-122">Click the **Create repository** button.</span></span>
1. <span data-ttu-id="eb08a-123">Otevřete příkazové prostředí místního počítače.</span><span class="sxs-lookup"><span data-stu-id="eb08a-123">Open your local machine's command shell.</span></span> <span data-ttu-id="eb08a-124">Přejděte do adresáře, ve kterém je uloženo úložiště Git s *jednoduchým kanálem pro čtení* .</span><span class="sxs-lookup"><span data-stu-id="eb08a-124">Navigate to the directory in which the *simple-feed-reader* Git repository is stored.</span></span>
1. <span data-ttu-id="eb08a-125">Přejmenujte stávající vzdálený *zdroj* na *nadřazený*.</span><span class="sxs-lookup"><span data-stu-id="eb08a-125">Rename the existing *origin* remote to *upstream*.</span></span> <span data-ttu-id="eb08a-126">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="eb08a-126">Execute the following command:</span></span>

    ```console
    git remote rename origin upstream
    ```

1. <span data-ttu-id="eb08a-127">Přidejte na GitHubu nového *zdroje* , který se vzdáleně odkazuje na vaši kopii úložiště.</span><span class="sxs-lookup"><span data-stu-id="eb08a-127">Add a new *origin* remote pointing to your copy of the repository on GitHub.</span></span> <span data-ttu-id="eb08a-128">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="eb08a-128">Execute the following command:</span></span>

    ```console
    git remote add origin https://github.com/<GitHub_username>/simple-feed-reader/
    ```

1. <span data-ttu-id="eb08a-129">Publikujte místní úložiště Git do nově vytvořeného úložiště GitHub.</span><span class="sxs-lookup"><span data-stu-id="eb08a-129">Publish your local Git repository to the newly created GitHub repository.</span></span> <span data-ttu-id="eb08a-130">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="eb08a-130">Execute the following command:</span></span>

    ```console
    git push -u origin master
    ```

1. <span data-ttu-id="eb08a-131">Otevřete okno prohlížeče a přejděte na `https://github.com/<GitHub_username>/simple-feed-reader/` .</span><span class="sxs-lookup"><span data-stu-id="eb08a-131">Open a browser window, and navigate to `https://github.com/<GitHub_username>/simple-feed-reader/`.</span></span> <span data-ttu-id="eb08a-132">Ověřte, že se váš kód zobrazuje v úložišti GitHubu.</span><span class="sxs-lookup"><span data-stu-id="eb08a-132">Validate that your code appears in the GitHub repository.</span></span>

## <a name="disconnect-local-git-deployment"></a><span data-ttu-id="eb08a-133">Odpojit nasazení místního Gitu</span><span class="sxs-lookup"><span data-stu-id="eb08a-133">Disconnect local Git deployment</span></span>

<span data-ttu-id="eb08a-134">Odeberte místní nasazení Gitu pomocí následujících kroků.</span><span class="sxs-lookup"><span data-stu-id="eb08a-134">Remove the local Git deployment with the following steps.</span></span> <span data-ttu-id="eb08a-135">Azure Pipelines (služba Azure DevOps) nahrazuje i rozšiřuje tuto funkčnost.</span><span class="sxs-lookup"><span data-stu-id="eb08a-135">Azure Pipelines (an Azure DevOps service) both replaces and augments that functionality.</span></span>

1. <span data-ttu-id="eb08a-136">Otevřete [Azure Portal](https://portal.azure.com/)a přejděte do pracovní webové aplikace *(MyWebApp \<unique_number\> /staging)* .</span><span class="sxs-lookup"><span data-stu-id="eb08a-136">Open the [Azure portal](https://portal.azure.com/), and navigate to the *staging (mywebapp\<unique_number\>/staging)* Web App.</span></span> <span data-ttu-id="eb08a-137">Webovou aplikaci můžete rychle najít tak, že do vyhledávacího pole portálu zadáte *fázování* :</span><span class="sxs-lookup"><span data-stu-id="eb08a-137">The Web App can be quickly located by entering *staging* in the portal's search box:</span></span>

    ![pracovní termín pro hledání webové aplikace](media/cicd/portal-search-box.png)

1. <span data-ttu-id="eb08a-139">Klikněte na **centrum nasazení**.</span><span class="sxs-lookup"><span data-stu-id="eb08a-139">Click **Deployment Center**.</span></span> <span data-ttu-id="eb08a-140">Zobrazí se nový panel.</span><span class="sxs-lookup"><span data-stu-id="eb08a-140">A new panel appears.</span></span> <span data-ttu-id="eb08a-141">Kliknutím na **Odpojit** odeberte konfiguraci místního řízení zdrojů Git, která se přidala do předchozí kapitoly.</span><span class="sxs-lookup"><span data-stu-id="eb08a-141">Click **Disconnect** to remove the local Git source control configuration that was added in the previous chapter.</span></span> <span data-ttu-id="eb08a-142">Kliknutím na tlačítko **Ano** potvrďte operaci odebrání.</span><span class="sxs-lookup"><span data-stu-id="eb08a-142">Confirm the removal operation by clicking the **Yes** button.</span></span>
1. <span data-ttu-id="eb08a-143">Přejděte do *mywebapp<unique_number>* App Service.</span><span class="sxs-lookup"><span data-stu-id="eb08a-143">Navigate to the *mywebapp<unique_number>* App Service.</span></span> <span data-ttu-id="eb08a-144">Jako připomenutí můžete použít vyhledávací pole na portálu k rychlému vyhledání App Service.</span><span class="sxs-lookup"><span data-stu-id="eb08a-144">As a reminder, the portal's search box can be used to quickly locate the App Service.</span></span>
1. <span data-ttu-id="eb08a-145">Klikněte na **centrum nasazení**.</span><span class="sxs-lookup"><span data-stu-id="eb08a-145">Click **Deployment Center**.</span></span> <span data-ttu-id="eb08a-146">Zobrazí se nový panel.</span><span class="sxs-lookup"><span data-stu-id="eb08a-146">A new panel appears.</span></span> <span data-ttu-id="eb08a-147">Kliknutím na **Odpojit** odeberte konfiguraci místního řízení zdrojů Git, která se přidala do předchozí kapitoly.</span><span class="sxs-lookup"><span data-stu-id="eb08a-147">Click **Disconnect** to remove the local Git source control configuration that was added in the previous chapter.</span></span> <span data-ttu-id="eb08a-148">Kliknutím na tlačítko **Ano** potvrďte operaci odebrání.</span><span class="sxs-lookup"><span data-stu-id="eb08a-148">Confirm the removal operation by clicking the **Yes** button.</span></span>

## <a name="create-an-azure-devops-organization"></a><span data-ttu-id="eb08a-149">Vytvoření organizace Azure DevOps</span><span class="sxs-lookup"><span data-stu-id="eb08a-149">Create an Azure DevOps organization</span></span>

1. <span data-ttu-id="eb08a-150">Otevřete prohlížeč a přejděte na [stránku pro vytvoření organizace Azure DevOps](https://go.microsoft.com/fwlink/?LinkId=307137).</span><span class="sxs-lookup"><span data-stu-id="eb08a-150">Open a browser, and navigate to the [Azure DevOps organization creation page](https://go.microsoft.com/fwlink/?LinkId=307137).</span></span>
1. <span data-ttu-id="eb08a-151">Do textového pole **Vybrat zapamatovatelné jméno** zadejte jedinečný název, který bude tvořit adresu URL pro přístup ke svojí organizaci Azure DevOps.</span><span class="sxs-lookup"><span data-stu-id="eb08a-151">Type a unique name into the **Pick a memorable name** textbox to form the URL for accessing your Azure DevOps organization.</span></span>
1. <span data-ttu-id="eb08a-152">Vyberte přepínač **Git** , protože je tento kód hostovaný v úložišti GitHubu.</span><span class="sxs-lookup"><span data-stu-id="eb08a-152">Select the **Git** radio button, since the code is hosted in a GitHub repository.</span></span>
1. <span data-ttu-id="eb08a-153">Klikněte na tlačítko **pokračovat** .</span><span class="sxs-lookup"><span data-stu-id="eb08a-153">Click the **Continue** button.</span></span> <span data-ttu-id="eb08a-154">Po krátkém čekání se vytvoří účet a týmový projekt s názvem *MyFirstProject*.</span><span class="sxs-lookup"><span data-stu-id="eb08a-154">After a short wait, an account and a team project, named *MyFirstProject*, are created.</span></span>

    ![Stránka pro vytvoření organizace Azure DevOps](media/cicd/vsts-account-creation.png)

1. <span data-ttu-id="eb08a-156">Otevřete potvrzovací e-mail s oznámením, že organizace a projekt Azure DevOps jsou připravené k použití.</span><span class="sxs-lookup"><span data-stu-id="eb08a-156">Open the confirmation email indicating that the Azure DevOps organization and project are ready for use.</span></span> <span data-ttu-id="eb08a-157">Klikněte na tlačítko **spustit projekt** :</span><span class="sxs-lookup"><span data-stu-id="eb08a-157">Click the **Start your project** button:</span></span>

    ![Tlačítko spustit projekt](media/cicd/vsts-start-project.png)

1. <span data-ttu-id="eb08a-159">Otevře se prohlížeč s \* \<account_name\> příponou. VisualStudio.com\*.</span><span class="sxs-lookup"><span data-stu-id="eb08a-159">A browser opens to *\<account_name\>.visualstudio.com*.</span></span> <span data-ttu-id="eb08a-160">Kliknutím na odkaz *MyFirstProject* zahajte konfiguraci kanálu DevOps projektu.</span><span class="sxs-lookup"><span data-stu-id="eb08a-160">Click the *MyFirstProject* link to begin configuring the project's DevOps pipeline.</span></span>

## <a name="configure-the-azure-pipelines-pipeline"></a><span data-ttu-id="eb08a-161">Konfigurace kanálu Azure Pipelines</span><span class="sxs-lookup"><span data-stu-id="eb08a-161">Configure the Azure Pipelines pipeline</span></span>

<span data-ttu-id="eb08a-162">Existují tři samostatné kroky pro dokončení.</span><span class="sxs-lookup"><span data-stu-id="eb08a-162">There are three distinct steps to complete.</span></span> <span data-ttu-id="eb08a-163">Provedením kroků v následujících třech částech dojde k DevOps kanálu provozní služby.</span><span class="sxs-lookup"><span data-stu-id="eb08a-163">Completing the steps in the following three sections results in an operational DevOps pipeline.</span></span>

### <a name="grant-azure-devops-access-to-the-github-repository"></a><span data-ttu-id="eb08a-164">Udělení přístupu Azure DevOps k úložišti GitHubu</span><span class="sxs-lookup"><span data-stu-id="eb08a-164">Grant Azure DevOps access to the GitHub repository</span></span>

1. <span data-ttu-id="eb08a-165">Rozbalte **nebo sestavte kód z přidaných externích úložišť** .</span><span class="sxs-lookup"><span data-stu-id="eb08a-165">Expand the **or build code from an external repository** accordion.</span></span> <span data-ttu-id="eb08a-166">Klikněte na tlačítko **sestavení pro instalaci** :</span><span class="sxs-lookup"><span data-stu-id="eb08a-166">Click the **Setup Build** button:</span></span>

    ![Tlačítko pro sestavení instalace](media/cicd/vsts-setup-build.png)

1. <span data-ttu-id="eb08a-168">V části **Vybrat zdroj** vyberte možnost **GitHub** :</span><span class="sxs-lookup"><span data-stu-id="eb08a-168">Select the **GitHub** option from the **Select a source** section:</span></span>

    ![Vybrat zdroj – GitHub](media/cicd/vsts-select-source.png)

1. <span data-ttu-id="eb08a-170">Aby mohl Azure DevOps získat přístup k úložišti GitHubu, vyžaduje se autorizace.</span><span class="sxs-lookup"><span data-stu-id="eb08a-170">Authorization is required before Azure DevOps can access your GitHub repository.</span></span> <span data-ttu-id="eb08a-171">Do textového pole **název připojení** zadejte *<GitHub_username> připojení GitHubu* .</span><span class="sxs-lookup"><span data-stu-id="eb08a-171">Enter *<GitHub_username> GitHub connection* in the **Connection name** textbox.</span></span> <span data-ttu-id="eb08a-172">Například:</span><span class="sxs-lookup"><span data-stu-id="eb08a-172">For example:</span></span>

    ![Název připojení GitHubu](media/cicd/vsts-repo-authz.png)

1. <span data-ttu-id="eb08a-174">Pokud je ve vašem účtu GitHub povolený dvojúrovňové ověřování, vyžaduje se osobní přístupový token.</span><span class="sxs-lookup"><span data-stu-id="eb08a-174">If two-factor authentication is enabled on your GitHub account, a personal access token is required.</span></span> <span data-ttu-id="eb08a-175">V takovém případě klikněte na odkaz **autorizovat pomocí osobního přístupového tokenu GitHubu** .</span><span class="sxs-lookup"><span data-stu-id="eb08a-175">In that case, click the **Authorize with a GitHub personal access token** link.</span></span> <span data-ttu-id="eb08a-176">Nápovědu najdete v [oficiálních pokynech k vytvoření tokenu GitHubu pro GitHub](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/) .</span><span class="sxs-lookup"><span data-stu-id="eb08a-176">See the [official GitHub personal access token creation instructions](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/) for help.</span></span> <span data-ttu-id="eb08a-177">Je potřeba jenom obor *úložiště* oprávnění.</span><span class="sxs-lookup"><span data-stu-id="eb08a-177">Only the *repo* scope of permissions is needed.</span></span> <span data-ttu-id="eb08a-178">V opačném případě klikněte na tlačítko **autorizovat pomocí OAuth** .</span><span class="sxs-lookup"><span data-stu-id="eb08a-178">Otherwise, click the **Authorize using OAuth** button.</span></span>
1. <span data-ttu-id="eb08a-179">Po zobrazení výzvy se přihlaste ke svému účtu GitHub.</span><span class="sxs-lookup"><span data-stu-id="eb08a-179">When prompted, sign in to your GitHub account.</span></span> <span data-ttu-id="eb08a-180">Pak vyberte autorizovat pro udělení přístupu ke svojí organizaci Azure DevOps.</span><span class="sxs-lookup"><span data-stu-id="eb08a-180">Then select Authorize to grant access to your Azure DevOps organization.</span></span> <span data-ttu-id="eb08a-181">V případě úspěchu se vytvoří nový koncový bod služby.</span><span class="sxs-lookup"><span data-stu-id="eb08a-181">If successful, a new service endpoint is created.</span></span>
1. <span data-ttu-id="eb08a-182">Klikněte na tlačítko se třemi tečkami vedle tlačítka **úložiště** .</span><span class="sxs-lookup"><span data-stu-id="eb08a-182">Click the ellipsis button next to the **Repository** button.</span></span> <span data-ttu-id="eb08a-183">V seznamu vyberte *<GitHub_username> úložiště/Simple-feed-Reader* .</span><span class="sxs-lookup"><span data-stu-id="eb08a-183">Select the *<GitHub_username>/simple-feed-reader* repository from the list.</span></span> <span data-ttu-id="eb08a-184">Klikněte na tlačítko **Vybrat** .</span><span class="sxs-lookup"><span data-stu-id="eb08a-184">Click the **Select** button.</span></span>
1. <span data-ttu-id="eb08a-185">Vyberte *Hlavní* větev z rozevíracího seznamu **výchozí větev pro ruční a plánovaná sestavení** .</span><span class="sxs-lookup"><span data-stu-id="eb08a-185">Select the *master* branch from the **Default branch for manual and scheduled builds** drop-down.</span></span> <span data-ttu-id="eb08a-186">Klikněte na tlačítko **pokračovat** .</span><span class="sxs-lookup"><span data-stu-id="eb08a-186">Click the **Continue** button.</span></span> <span data-ttu-id="eb08a-187">Zobrazí se stránka Výběr šablony.</span><span class="sxs-lookup"><span data-stu-id="eb08a-187">The template selection page appears.</span></span>

### <a name="create-the-build-definition"></a><span data-ttu-id="eb08a-188">Vytvoření definice sestavení</span><span class="sxs-lookup"><span data-stu-id="eb08a-188">Create the build definition</span></span>

1. <span data-ttu-id="eb08a-189">Na stránce Výběr šablony zadejte do vyhledávacího pole *ASP.NET Core* :</span><span class="sxs-lookup"><span data-stu-id="eb08a-189">From the template selection page, enter *ASP.NET Core* in the search box:</span></span>

    ![ASP.NET Core hledání na stránce šablony](media/cicd/vsts-template-selection.png)

1. <span data-ttu-id="eb08a-191">Zobrazí se výsledky hledání šablony.</span><span class="sxs-lookup"><span data-stu-id="eb08a-191">The template search results appear.</span></span> <span data-ttu-id="eb08a-192">Najeďte myší na šablonu **ASP.NET Core** a klikněte na tlačítko **použít** .</span><span class="sxs-lookup"><span data-stu-id="eb08a-192">Hover over the **ASP.NET Core** template, and click the **Apply** button.</span></span>
1. <span data-ttu-id="eb08a-193">Zobrazí se karta **úkoly** v definici sestavení.</span><span class="sxs-lookup"><span data-stu-id="eb08a-193">The **Tasks** tab of the build definition appears.</span></span> <span data-ttu-id="eb08a-194">Klikněte na kartu **triggery** .</span><span class="sxs-lookup"><span data-stu-id="eb08a-194">Click the **Triggers** tab.</span></span>
1. <span data-ttu-id="eb08a-195">Zaškrtněte políčko **Povolit průběžnou integraci** .</span><span class="sxs-lookup"><span data-stu-id="eb08a-195">Check the **Enable continuous integration** box.</span></span> <span data-ttu-id="eb08a-196">V části **filtry větví** potvrďte, že rozevírací seznam **typ** je nastavený na *Zahrnout*.</span><span class="sxs-lookup"><span data-stu-id="eb08a-196">Under the **Branch filters** section, confirm that the **Type** drop-down is set to *Include*.</span></span> <span data-ttu-id="eb08a-197">Nastavte rozevírací seznam **specifikace větve** na *hlavní*.</span><span class="sxs-lookup"><span data-stu-id="eb08a-197">Set the **Branch specification** drop-down to *master*.</span></span>

    ![Povolit nastavení nepřetržité integrace](media/cicd/vsts-enable-ci.png)

    <span data-ttu-id="eb08a-199">Tato nastavení způsobí, že se sestavení aktivuje při vložení jakékoli změny do *Hlavní* větve úložiště GitHub.</span><span class="sxs-lookup"><span data-stu-id="eb08a-199">These settings cause a build to trigger when any change is pushed to the *master* branch of the GitHub repository.</span></span> <span data-ttu-id="eb08a-200">Nepřetržitá integrace je testována v části [Potvrdit změny do GitHubu a automaticky se nasazuje do Azure](#commit-changes-to-github-and-automatically-deploy-to-azure) .</span><span class="sxs-lookup"><span data-stu-id="eb08a-200">Continuous integration is tested in the [Commit changes to GitHub and automatically deploy to Azure](#commit-changes-to-github-and-automatically-deploy-to-azure) section.</span></span>

1. <span data-ttu-id="eb08a-201">Klikněte na tlačítko **uložit & frontu** a vyberte možnost **Uložit** :</span><span class="sxs-lookup"><span data-stu-id="eb08a-201">Click the **Save & queue** button, and select the **Save** option:</span></span>

    ![Tlačítko Uložit](media/cicd/vsts-save-build.png)

1. <span data-ttu-id="eb08a-203">Zobrazí se následující modální dialogové okno:</span><span class="sxs-lookup"><span data-stu-id="eb08a-203">The following modal dialog appears:</span></span>

    ![Uložit definici sestavení – modální dialogové okno](media/cicd/vsts-save-modal.png)

    <span data-ttu-id="eb08a-205">Použijte výchozí složku *\\* a klikněte na tlačítko **Uložit** .</span><span class="sxs-lookup"><span data-stu-id="eb08a-205">Use the default folder of *\\*, and click the **Save** button.</span></span>

### <a name="create-the-release-pipeline"></a><span data-ttu-id="eb08a-206">Vytvoření kanálu pro vydávání verzí</span><span class="sxs-lookup"><span data-stu-id="eb08a-206">Create the release pipeline</span></span>

1. <span data-ttu-id="eb08a-207">Klikněte na kartu **vydání** v týmovém projektu.</span><span class="sxs-lookup"><span data-stu-id="eb08a-207">Click the **Releases** tab of your team project.</span></span> <span data-ttu-id="eb08a-208">Klikněte na tlačítko **Nový kanál** .</span><span class="sxs-lookup"><span data-stu-id="eb08a-208">Click the **New pipeline** button.</span></span>

    ![Karta vydání – tlačítko Nová definice](media/cicd/vsts-new-release-definition.png)

    <span data-ttu-id="eb08a-210">Zobrazí se podokno výběr šablony.</span><span class="sxs-lookup"><span data-stu-id="eb08a-210">The template selection pane appears.</span></span>

1. <span data-ttu-id="eb08a-211">Na stránce Výběr šablony zadejte do vyhledávacího pole *App Service* :</span><span class="sxs-lookup"><span data-stu-id="eb08a-211">From the template selection page, enter *App Service* in the search box:</span></span>

    ![Vyhledávací pole šablony kanálu vydání](media/cicd/vsts-release-template-search.png)

1. <span data-ttu-id="eb08a-213">Zobrazí se výsledky hledání šablony.</span><span class="sxs-lookup"><span data-stu-id="eb08a-213">The template search results appear.</span></span> <span data-ttu-id="eb08a-214">Najeďte myší na **nasazení Azure App Service se šablonou slotu** a klikněte na tlačítko **použít** .</span><span class="sxs-lookup"><span data-stu-id="eb08a-214">Hover over the **Azure App Service Deployment with Slot** template, and click the **Apply** button.</span></span> <span data-ttu-id="eb08a-215">Zobrazí se karta **kanál** kanálu vydání.</span><span class="sxs-lookup"><span data-stu-id="eb08a-215">The **Pipeline** tab of the release pipeline appears.</span></span>

    ![Karta kanálu vydaných verzí](media/cicd/vsts-release-definition-pipeline.png)

1. <span data-ttu-id="eb08a-217">Klikněte na tlačítko **Přidat** v poli **artefakty** .</span><span class="sxs-lookup"><span data-stu-id="eb08a-217">Click the **Add** button in the **Artifacts** box.</span></span> <span data-ttu-id="eb08a-218">Zobrazí se panel **Přidat artefakt** :</span><span class="sxs-lookup"><span data-stu-id="eb08a-218">The **Add artifact** panel appears:</span></span>

    ![Kanál verze – přidat panel artefaktu](media/cicd/vsts-release-add-artifact.png)

1. <span data-ttu-id="eb08a-220">Vyberte dlaždici **sestavení** z oddílu **typ zdroje** .</span><span class="sxs-lookup"><span data-stu-id="eb08a-220">Select the **Build** tile from the **Source type** section.</span></span> <span data-ttu-id="eb08a-221">Tento typ umožňuje propojit kanál verze s definicí sestavení.</span><span class="sxs-lookup"><span data-stu-id="eb08a-221">This type allows for the linking of the release pipeline to the build definition.</span></span>
1. <span data-ttu-id="eb08a-222">V rozevíracím seznamu **projekt** vyberte *MyFirstProject* .</span><span class="sxs-lookup"><span data-stu-id="eb08a-222">Select *MyFirstProject* from the **Project** drop-down.</span></span>
1. <span data-ttu-id="eb08a-223">Z rozevíracího seznamu **zdroj (definice sestavení)** vyberte název definice sestavení, *MYFIRSTPROJECT-ASP.NET Core-CI*.</span><span class="sxs-lookup"><span data-stu-id="eb08a-223">Select the build definition name, *MyFirstProject-ASP.NET Core-CI*, from the **Source (Build definition)** drop-down.</span></span>
1. <span data-ttu-id="eb08a-224">Z rozevíracího seznamu **výchozí verze** vyberte *nejnovější* .</span><span class="sxs-lookup"><span data-stu-id="eb08a-224">Select *Latest* from the **Default version** drop-down.</span></span> <span data-ttu-id="eb08a-225">Tato možnost sestaví artefakty vytvořené pomocí posledního spuštění definice sestavení.</span><span class="sxs-lookup"><span data-stu-id="eb08a-225">This option builds the artifacts produced by the latest run of the build definition.</span></span>
1. <span data-ttu-id="eb08a-226">Text v textovém poli **aliasu zdroje** nahraďte textem *drop*.</span><span class="sxs-lookup"><span data-stu-id="eb08a-226">Replace the text in the **Source alias** textbox with *Drop*.</span></span>
1. <span data-ttu-id="eb08a-227">Klikněte na tlačítko **Přidat** .</span><span class="sxs-lookup"><span data-stu-id="eb08a-227">Click the **Add** button.</span></span> <span data-ttu-id="eb08a-228">Část **artefakty** aktualizuje, aby se zobrazily změny.</span><span class="sxs-lookup"><span data-stu-id="eb08a-228">The **Artifacts** section updates to display the changes.</span></span>
1. <span data-ttu-id="eb08a-229">Pokud chcete povolit nepřetržitá nasazení, klikněte na ikonu bleskového šroubu:</span><span class="sxs-lookup"><span data-stu-id="eb08a-229">Click the lightning bolt icon to enable continuous deployments:</span></span>

    ![Artefakty kanálu vydání – Ikona blesku](media/cicd/vsts-artifacts-lightning-bolt.png)

    <span data-ttu-id="eb08a-231">Pokud je tato možnost povolena, nasazení probíhá pokaždé, když je k dispozici nové sestavení.</span><span class="sxs-lookup"><span data-stu-id="eb08a-231">With this option enabled, a deployment occurs each time a new build is available.</span></span>
1. <span data-ttu-id="eb08a-232">Napravo se zobrazí panel **Trigger průběžného nasazování** .</span><span class="sxs-lookup"><span data-stu-id="eb08a-232">A **Continuous deployment trigger** panel appears to the right.</span></span> <span data-ttu-id="eb08a-233">Pro povolení funkce klikněte na přepínací tlačítko.</span><span class="sxs-lookup"><span data-stu-id="eb08a-233">Click the toggle button to enable the feature.</span></span> <span data-ttu-id="eb08a-234">Není nutné povolit **Trigger žádosti o získání dat**.</span><span class="sxs-lookup"><span data-stu-id="eb08a-234">It isn't necessary to enable the **Pull request trigger**.</span></span>
1. <span data-ttu-id="eb08a-235">V části **filtry větve sestavení** klikněte na rozevírací nabídku **Přidat** .</span><span class="sxs-lookup"><span data-stu-id="eb08a-235">Click the **Add** drop-down in the **Build branch filters** section.</span></span> <span data-ttu-id="eb08a-236">Vyberte možnost **výchozí větev definice sestavení** .</span><span class="sxs-lookup"><span data-stu-id="eb08a-236">Choose the **Build Definition's default branch** option.</span></span> <span data-ttu-id="eb08a-237">Tento filtr způsobí, že se vydaná verze aktivuje jenom pro sestavení z *Hlavní* větve úložiště GitHub.</span><span class="sxs-lookup"><span data-stu-id="eb08a-237">This filter causes the release to trigger only for a build from the GitHub repository's *master* branch.</span></span>
1. <span data-ttu-id="eb08a-238">Klikněte na tlačítko **Uložit**.</span><span class="sxs-lookup"><span data-stu-id="eb08a-238">Click the **Save** button.</span></span> <span data-ttu-id="eb08a-239">Klikněte na tlačítko **OK** v dialogovém okně výsledný dialog **Uložit** modální.</span><span class="sxs-lookup"><span data-stu-id="eb08a-239">Click the **OK** button in the resulting **Save** modal dialog.</span></span>
1. <span data-ttu-id="eb08a-240">Klikněte na pole **prostředí 1** .</span><span class="sxs-lookup"><span data-stu-id="eb08a-240">Click the **Environment 1** box.</span></span> <span data-ttu-id="eb08a-241">Napravo se zobrazí panel **prostředí** .</span><span class="sxs-lookup"><span data-stu-id="eb08a-241">An **Environment** panel appears to the right.</span></span> <span data-ttu-id="eb08a-242">Změňte text *prostředí 1* v textovém poli **Název prostředí** na *produkční*.</span><span class="sxs-lookup"><span data-stu-id="eb08a-242">Change the *Environment 1* text in the **Environment name** textbox to *Production*.</span></span>

   ![Kanál vydané verze – textové pole názvu prostředí](media/cicd/vsts-environment-name-textbox.png)

1. <span data-ttu-id="eb08a-244">V **produkčním** poli klikněte na odkaz **1 fáze, 2 úlohy** :</span><span class="sxs-lookup"><span data-stu-id="eb08a-244">Click the **1 phase, 2 tasks** link in the **Production** box:</span></span>

    ![Kanál vydání – produkční prostředí link.png](media/cicd/vsts-production-link.png)

    <span data-ttu-id="eb08a-246">Zobrazí se karta **úlohy** v prostředí.</span><span class="sxs-lookup"><span data-stu-id="eb08a-246">The **Tasks** tab of the environment appears.</span></span>
1. <span data-ttu-id="eb08a-247">Klikněte na úlohu **nasadit Azure App Service pro slot** .</span><span class="sxs-lookup"><span data-stu-id="eb08a-247">Click the **Deploy Azure App Service to Slot** task.</span></span> <span data-ttu-id="eb08a-248">Jeho nastavení se zobrazí na panelu vpravo.</span><span class="sxs-lookup"><span data-stu-id="eb08a-248">Its settings appear in a panel to the right.</span></span>
1. <span data-ttu-id="eb08a-249">Z rozevíracího seznamu **předplatné Azure** vyberte předplatné Azure přidružené k tomuto App Service.</span><span class="sxs-lookup"><span data-stu-id="eb08a-249">Select the Azure subscription associated with the App Service from the **Azure subscription** drop-down.</span></span> <span data-ttu-id="eb08a-250">Po výběru klikněte na tlačítko **autorizovat** .</span><span class="sxs-lookup"><span data-stu-id="eb08a-250">Once selected, click the **Authorize** button.</span></span>
1. <span data-ttu-id="eb08a-251">V rozevíracím seznamu **Typ aplikace** vyberte možnost *Webová aplikace* .</span><span class="sxs-lookup"><span data-stu-id="eb08a-251">Select *Web App* from the **App type** drop-down.</span></span>
1. <span data-ttu-id="eb08a-252">V rozevíracím seznamu **název služby App Service** vyberte *mywebapp/<unique_number/>* .</span><span class="sxs-lookup"><span data-stu-id="eb08a-252">Select *mywebapp/<unique_number/>* from the **App service name** drop-down.</span></span>
1. <span data-ttu-id="eb08a-253">V rozevíracím seznamu **Skupina prostředků** vyberte *AzureTutorial* .</span><span class="sxs-lookup"><span data-stu-id="eb08a-253">Select *AzureTutorial* from the **Resource group** drop-down.</span></span>
1. <span data-ttu-id="eb08a-254">V rozevíracím seznamu **slot** vyberte *fázování* .</span><span class="sxs-lookup"><span data-stu-id="eb08a-254">Select *staging* from the **Slot** drop-down.</span></span>
1. <span data-ttu-id="eb08a-255">Klikněte na tlačítko **Uložit**.</span><span class="sxs-lookup"><span data-stu-id="eb08a-255">Click the **Save** button.</span></span>
1. <span data-ttu-id="eb08a-256">Najeďte myší na výchozí název kanálu vydaných verzí.</span><span class="sxs-lookup"><span data-stu-id="eb08a-256">Hover over the default release pipeline name.</span></span> <span data-ttu-id="eb08a-257">Klikněte na ikonu tužky a upravte ji.</span><span class="sxs-lookup"><span data-stu-id="eb08a-257">Click the pencil icon to edit it.</span></span> <span data-ttu-id="eb08a-258">Jako název použijte *MyFirstProject-ASP.NET Core-CD* .</span><span class="sxs-lookup"><span data-stu-id="eb08a-258">Use *MyFirstProject-ASP.NET Core-CD* as the name.</span></span>

    ![Název kanálu verze](media/cicd/vsts-release-definition-name.png)

1. <span data-ttu-id="eb08a-260">Klikněte na tlačítko **Uložit**.</span><span class="sxs-lookup"><span data-stu-id="eb08a-260">Click the **Save** button.</span></span>

## <a name="commit-changes-to-github-and-automatically-deploy-to-azure"></a><span data-ttu-id="eb08a-261">Potvrzení změn na GitHubu a automatické nasazení do Azure</span><span class="sxs-lookup"><span data-stu-id="eb08a-261">Commit changes to GitHub and automatically deploy to Azure</span></span>

1. <span data-ttu-id="eb08a-262">Otevřete *SimpleFeedReader. sln* v aplikaci Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="eb08a-262">Open *SimpleFeedReader.sln* in Visual Studio.</span></span>
1. <span data-ttu-id="eb08a-263">V Průzkumník řešení otevřete *Pages\Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="eb08a-263">In Solution Explorer, open *Pages\Index.cshtml*.</span></span> <span data-ttu-id="eb08a-264">Změňte `<h2>Simple Feed Reader - V3</h2>` na `<h2>Simple Feed Reader - V4</h2>` .</span><span class="sxs-lookup"><span data-stu-id="eb08a-264">Change `<h2>Simple Feed Reader - V3</h2>` to `<h2>Simple Feed Reader - V4</h2>`.</span></span>
1. <span data-ttu-id="eb08a-265">Stisknutím **kombinace kláves CTRL** + **+ SHIFT** + **B** sestavíte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="eb08a-265">Press **Ctrl**+**Shift**+**B** to build the app.</span></span>
1. <span data-ttu-id="eb08a-266">Potvrďte soubor do úložiště GitHub.</span><span class="sxs-lookup"><span data-stu-id="eb08a-266">Commit the file to the GitHub repository.</span></span> <span data-ttu-id="eb08a-267">Na kartě *Team Explorer* v aplikaci Visual Studio použijte stránku **změny** nebo pomocí příkazového prostředí místního počítače spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="eb08a-267">Use either the **Changes** page in Visual Studio's *Team Explorer* tab, or execute the following using the local machine's command shell:</span></span>

    ```console
    git commit -a -m "upgraded to V4"
    ```

1. <span data-ttu-id="eb08a-268">Nahrajte změnu v *Hlavní* větvi do *počátečního* vzdáleného úložiště GitHubu:</span><span class="sxs-lookup"><span data-stu-id="eb08a-268">Push the change in the *master* branch to the *origin* remote of your GitHub repository:</span></span>

    ```console
    git push origin master
    ```

    <span data-ttu-id="eb08a-269">Potvrzení se zobrazí v *Hlavní* větvi úložiště GitHub:</span><span class="sxs-lookup"><span data-stu-id="eb08a-269">The commit appears in the GitHub repository's *master* branch:</span></span>

    ![Potvrzení GitHubu v hlavní větvi](media/cicd/github-commit.png)

    <span data-ttu-id="eb08a-271">Sestavení je aktivováno, protože je povolena průběžná integrace na kartě **aktivační události** definice sestavení:</span><span class="sxs-lookup"><span data-stu-id="eb08a-271">The build is triggered, since continuous integration is enabled in the build definition's **Triggers** tab:</span></span>

    ![Povolit kontinuální integraci](media/cicd/enable-ci.png)

1. <span data-ttu-id="eb08a-273">Na stránce **Azure Pipelines**sestavení v Azure DevOps Services přejděte na kartu **zařazené do fronty**  >  **Builds** .</span><span class="sxs-lookup"><span data-stu-id="eb08a-273">Navigate to the **Queued** tab of the **Azure Pipelines** > **Builds** page in Azure DevOps Services.</span></span> <span data-ttu-id="eb08a-274">Sestavení ve frontě zobrazuje větev a potvrzení, které spustilo sestavení:</span><span class="sxs-lookup"><span data-stu-id="eb08a-274">The queued build shows the branch and commit that triggered the build:</span></span>

    ![sestavení zařazené do fronty](media/cicd/build-queued.png)

1. <span data-ttu-id="eb08a-276">Po úspěšném sestavení dojde k nasazení do Azure.</span><span class="sxs-lookup"><span data-stu-id="eb08a-276">Once the build succeeds, a deployment to Azure occurs.</span></span> <span data-ttu-id="eb08a-277">V prohlížeči přejděte do aplikace.</span><span class="sxs-lookup"><span data-stu-id="eb08a-277">Navigate to the app in the browser.</span></span> <span data-ttu-id="eb08a-278">Všimněte si, že se v záhlaví zobrazí text "v4":</span><span class="sxs-lookup"><span data-stu-id="eb08a-278">Notice that the "V4" text appears in the heading:</span></span>

    ![aktualizovaná aplikace](media/cicd/updated-app-v4.png)

## <a name="examine-the-azure-pipelines-pipeline"></a><span data-ttu-id="eb08a-280">Kontrola kanálu Azure Pipelines</span><span class="sxs-lookup"><span data-stu-id="eb08a-280">Examine the Azure Pipelines pipeline</span></span>

### <a name="build-definition"></a><span data-ttu-id="eb08a-281">Definice sestavení</span><span class="sxs-lookup"><span data-stu-id="eb08a-281">Build definition</span></span>

<span data-ttu-id="eb08a-282">Byla vytvořena definice sestavení s názvem *MyFirstProject-ASP.NET Core-CI*.</span><span class="sxs-lookup"><span data-stu-id="eb08a-282">A build definition was created with the name *MyFirstProject-ASP.NET Core-CI*.</span></span> <span data-ttu-id="eb08a-283">Po dokončení sestavení vytvoří soubor *. zip* , včetně prostředků, které mají být publikovány.</span><span class="sxs-lookup"><span data-stu-id="eb08a-283">Upon completion, the build produces a *.zip* file including the assets to be published.</span></span> <span data-ttu-id="eb08a-284">Kanál pro vydávání verzí tyto prostředky nasadí do Azure.</span><span class="sxs-lookup"><span data-stu-id="eb08a-284">The release pipeline deploys those assets to Azure.</span></span>

<span data-ttu-id="eb08a-285">Na kartě **úkoly** definice sestavení jsou uvedeny jednotlivé kroky, které se používají.</span><span class="sxs-lookup"><span data-stu-id="eb08a-285">The build definition's **Tasks** tab lists the individual steps being used.</span></span> <span data-ttu-id="eb08a-286">Existuje pět úkolů sestavení.</span><span class="sxs-lookup"><span data-stu-id="eb08a-286">There are five build tasks.</span></span>

![úkoly definice sestavení](media/cicd/build-definition-tasks.png)

1. <span data-ttu-id="eb08a-288">**Obnovit** &mdash; Spustí `dotnet restore` příkaz pro obnovení balíčků NuGet aplikace.</span><span class="sxs-lookup"><span data-stu-id="eb08a-288">**Restore** &mdash; Executes the `dotnet restore` command to restore the app's NuGet packages.</span></span> <span data-ttu-id="eb08a-289">Výchozí použitý kanál balíčku je nuget.org.</span><span class="sxs-lookup"><span data-stu-id="eb08a-289">The default package feed used is nuget.org.</span></span>
1. <span data-ttu-id="eb08a-290">**Sestavení** &mdash; Spustí `dotnet build --configuration release` příkaz pro zkompilování kódu aplikace.</span><span class="sxs-lookup"><span data-stu-id="eb08a-290">**Build** &mdash; Executes the `dotnet build --configuration release` command to compile the app's code.</span></span> <span data-ttu-id="eb08a-291">Tato `--configuration` možnost slouží k vytvoření optimalizované verze kódu, který je vhodný pro nasazení do produkčního prostředí.</span><span class="sxs-lookup"><span data-stu-id="eb08a-291">This `--configuration` option is used to produce an optimized version of the code, which is suitable for deployment to a production environment.</span></span> <span data-ttu-id="eb08a-292">Pokud je například nutná konfigurace ladění, upravte proměnnou *BuildConfiguration* na kartě **proměnné** v definici sestavení.</span><span class="sxs-lookup"><span data-stu-id="eb08a-292">Modify the *BuildConfiguration* variable on the build definition's **Variables** tab if, for example, a debug configuration is needed.</span></span>
1. <span data-ttu-id="eb08a-293">**Test** &mdash; Spustí `dotnet test --configuration release --logger trx --results-directory <local_path_on_build_agent>` příkaz pro spuštění testů jednotek aplikace.</span><span class="sxs-lookup"><span data-stu-id="eb08a-293">**Test** &mdash; Executes the `dotnet test --configuration release --logger trx --results-directory <local_path_on_build_agent>` command to run the app's unit tests.</span></span> <span data-ttu-id="eb08a-294">Testy jednotek se spustí v rámci libovolného projektu C# odpovídajícího `**/*Tests/*.csproj` vzoru glob.</span><span class="sxs-lookup"><span data-stu-id="eb08a-294">Unit tests are executed within any C# project matching the `**/*Tests/*.csproj` glob pattern.</span></span> <span data-ttu-id="eb08a-295">Výsledky testů jsou uloženy v souboru *. TRX* v umístění určeném `--results-directory` možností.</span><span class="sxs-lookup"><span data-stu-id="eb08a-295">Test results are saved in a *.trx* file at the location specified by the `--results-directory` option.</span></span> <span data-ttu-id="eb08a-296">Pokud dojde k selhání nějakého testu, sestavení se nezdaří a není nasazeno.</span><span class="sxs-lookup"><span data-stu-id="eb08a-296">If any tests fail, the build fails and isn't deployed.</span></span>

    > [!NOTE]
    > <span data-ttu-id="eb08a-297">Chcete-li ověřit testy jednotek, upravte *SimpleFeedReader. Tests\Services\NewsServiceTests.cs* tak, aby záměrně přerušení jednoho testu.</span><span class="sxs-lookup"><span data-stu-id="eb08a-297">To verify the unit tests work, modify *SimpleFeedReader.Tests\Services\NewsServiceTests.cs* to purposefully break one of the tests.</span></span> <span data-ttu-id="eb08a-298">Například změňte `Assert.True(result.Count > 0);` na `Assert.False(result.Count > 0);` v `Returns_News_Stories_Given_Valid_Uri` metodě.</span><span class="sxs-lookup"><span data-stu-id="eb08a-298">For example, change `Assert.True(result.Count > 0);` to `Assert.False(result.Count > 0);` in the `Returns_News_Stories_Given_Valid_Uri` method.</span></span> <span data-ttu-id="eb08a-299">Potvrďte a nahrajte změnu do GitHubu.</span><span class="sxs-lookup"><span data-stu-id="eb08a-299">Commit and push the change to GitHub.</span></span> <span data-ttu-id="eb08a-300">Sestavování se aktivuje a dojde k chybě.</span><span class="sxs-lookup"><span data-stu-id="eb08a-300">The build is triggered and fails.</span></span> <span data-ttu-id="eb08a-301">Stav kanálu sestavení se změní na **neúspěch**.</span><span class="sxs-lookup"><span data-stu-id="eb08a-301">The build pipeline status changes to **failed**.</span></span> <span data-ttu-id="eb08a-302">Obnovte změnu, potvrďte akci a znovu ji nasaďte.</span><span class="sxs-lookup"><span data-stu-id="eb08a-302">Revert the change, commit, and push again.</span></span> <span data-ttu-id="eb08a-303">Sestavení je úspěšné.</span><span class="sxs-lookup"><span data-stu-id="eb08a-303">The build succeeds.</span></span>

1. <span data-ttu-id="eb08a-304">**Publikování** &mdash; Spustí `dotnet publish --configuration release --output <local_path_on_build_agent>` příkaz pro vytvoření souboru *. zip* s artefakty, které mají být nasazeny.</span><span class="sxs-lookup"><span data-stu-id="eb08a-304">**Publish** &mdash; Executes the `dotnet publish --configuration release --output <local_path_on_build_agent>` command to produce a *.zip* file with the artifacts to be deployed.</span></span> <span data-ttu-id="eb08a-305">`--output`Možnost určuje umístění publikování souboru *. zip* .</span><span class="sxs-lookup"><span data-stu-id="eb08a-305">The `--output` option specifies the publish location of the *.zip* file.</span></span> <span data-ttu-id="eb08a-306">Toto umístění je určeno předáním [předdefinované proměnné](/azure/devops/pipelines/build/variables) s názvem `$(build.artifactstagingdirectory)` .</span><span class="sxs-lookup"><span data-stu-id="eb08a-306">That location is specified by passing a [predefined variable](/azure/devops/pipelines/build/variables) named `$(build.artifactstagingdirectory)`.</span></span> <span data-ttu-id="eb08a-307">Tato proměnná se rozbalí na místní cestu, například *c:\Agent \_ work\1\a*, v agentovi sestavení.</span><span class="sxs-lookup"><span data-stu-id="eb08a-307">That variable expands to a local path, such as *c:\agent\_work\1\a*, on the build agent.</span></span>
1. <span data-ttu-id="eb08a-308">**Publikovat artefakt** &mdash; Publikuje soubor *. zip* vytvářený úlohou **Publish** .</span><span class="sxs-lookup"><span data-stu-id="eb08a-308">**Publish Artifact** &mdash; Publishes the *.zip* file produced by the **Publish** task.</span></span> <span data-ttu-id="eb08a-309">Úkol přijme umístění souboru *. zip* jako parametr, což je předdefinovaná proměnná `$(build.artifactstagingdirectory)` .</span><span class="sxs-lookup"><span data-stu-id="eb08a-309">The task accepts the *.zip* file location as a parameter, which is the predefined variable `$(build.artifactstagingdirectory)`.</span></span> <span data-ttu-id="eb08a-310">Soubor *. zip* je publikován jako složka s názvem *drop*.</span><span class="sxs-lookup"><span data-stu-id="eb08a-310">The *.zip* file is published as a folder named *drop*.</span></span>

<span data-ttu-id="eb08a-311">Kliknutím na **souhrnný** odkaz definice sestavení zobrazíte historii sestavení s definicí:</span><span class="sxs-lookup"><span data-stu-id="eb08a-311">Click the build definition's **Summary** link to view a history of builds with the definition:</span></span>

![Snímek obrazovky zobrazující historii definice sestavení](media/cicd/build-definition-summary.png)

<span data-ttu-id="eb08a-313">Na výsledné stránce klikněte na odkaz odpovídající jedinečnému číslu sestavení:</span><span class="sxs-lookup"><span data-stu-id="eb08a-313">On the resulting page, click the link corresponding to the unique build number:</span></span>

![Snímek obrazovky se stránkou souhrnu definice sestavení](media/cicd/build-definition-completed.png)

<span data-ttu-id="eb08a-315">Zobrazí se souhrn tohoto konkrétního sestavení.</span><span class="sxs-lookup"><span data-stu-id="eb08a-315">A summary of this specific build is displayed.</span></span> <span data-ttu-id="eb08a-316">Klikněte na kartu **artefakty** a Všimněte si, že je uvedena *ukládací* složka vytvořená sestavením:</span><span class="sxs-lookup"><span data-stu-id="eb08a-316">Click the **Artifacts** tab, and notice the *drop* folder produced by the build is listed:</span></span>

![Snímek obrazovky znázorňující artefakty definice sestavení – odkládací složka](media/cicd/build-definition-artifacts.png)

<span data-ttu-id="eb08a-318">Pomocí odkazů **Stáhnout** a **prozkoumat** můžete zkontrolovat publikované artefakty.</span><span class="sxs-lookup"><span data-stu-id="eb08a-318">Use the **Download** and **Explore** links to inspect the published artifacts.</span></span>

### <a name="release-pipeline"></a><span data-ttu-id="eb08a-319">Kanál verze</span><span class="sxs-lookup"><span data-stu-id="eb08a-319">Release pipeline</span></span>

<span data-ttu-id="eb08a-320">Byl vytvořen kanál vydané verze s názvem *MyFirstProject-ASP.NET Core-CD*:</span><span class="sxs-lookup"><span data-stu-id="eb08a-320">A release pipeline was created with the name *MyFirstProject-ASP.NET Core-CD*:</span></span>

![Snímek obrazovky s přehledem kanálu vydání](media/cicd/release-definition-overview.png)

<span data-ttu-id="eb08a-322">Mezi dvě hlavní součásti kanálu pro vydávání verzí patří **artefakty** a **prostředí**.</span><span class="sxs-lookup"><span data-stu-id="eb08a-322">The two major components of the release pipeline are the **Artifacts** and the **Environments**.</span></span> <span data-ttu-id="eb08a-323">Kliknutím na pole v části **artefakty** se odhalí následující panel:</span><span class="sxs-lookup"><span data-stu-id="eb08a-323">Clicking the box in the **Artifacts** section reveals the following panel:</span></span>

![Snímek obrazovky zobrazující artefakty kanálu vydaných verzí](media/cicd/release-definition-artifacts.png)

<span data-ttu-id="eb08a-325">Hodnota **zdroj (definice sestavení)** představuje definici sestavení, ke které je tento kanál verze propojený.</span><span class="sxs-lookup"><span data-stu-id="eb08a-325">The **Source (Build definition)** value represents the build definition to which this release pipeline is linked.</span></span> <span data-ttu-id="eb08a-326">Soubor *. zip* , který vygenerovalo úspěšné spuštění definice sestavení, je poskytován *provoznímu* prostředí pro nasazení do Azure.</span><span class="sxs-lookup"><span data-stu-id="eb08a-326">The *.zip* file produced by a successful run of the build definition is provided to the *Production* environment for deployment to Azure.</span></span> <span data-ttu-id="eb08a-327">Úkoly kanálu vydaných verzí zobrazíte kliknutím na odkaz *1 fáze, 2 úlohy* v poli *provozní* prostředí:</span><span class="sxs-lookup"><span data-stu-id="eb08a-327">Click the *1 phase, 2 tasks* link in the *Production* environment box to view the release pipeline tasks:</span></span>

![Snímek obrazovky znázorňující úlohy kanálu vydání](media/cicd/release-definition-tasks.png)

<span data-ttu-id="eb08a-329">Kanál vydané verze se skládá ze dvou úloh: *nasazení Azure App Service do slotu* a *správa swapu Azure App Serviceho slotu*.</span><span class="sxs-lookup"><span data-stu-id="eb08a-329">The release pipeline consists of two tasks: *Deploy Azure App Service to Slot* and *Manage Azure App Service - Slot Swap*.</span></span> <span data-ttu-id="eb08a-330">Kliknutím na první úlohu se zobrazí následující konfigurace úlohy:</span><span class="sxs-lookup"><span data-stu-id="eb08a-330">Clicking the first task reveals the following task configuration:</span></span>

![Snímek obrazovky s úlohou nasazení kanálu vydaných verzí](media/cicd/release-definition-task1.png)

<span data-ttu-id="eb08a-332">V úloze nasazení je definováno předplatné Azure, typ služby, název webové aplikace, skupina prostředků a slot nasazení.</span><span class="sxs-lookup"><span data-stu-id="eb08a-332">The Azure subscription, service type, web app name, resource group, and deployment slot are defined in the deployment task.</span></span> <span data-ttu-id="eb08a-333">Textové pole **balíčku nebo složky** obsahuje cestu k souboru *. zip* , která se má extrahovat a nasadit do *přípravného* slotu webové aplikace \*MyWebApp \<unique_number\> \* .</span><span class="sxs-lookup"><span data-stu-id="eb08a-333">The **Package or folder** textbox holds the *.zip* file path to be extracted and deployed to the *staging* slot of the *mywebapp\<unique_number\>* web app.</span></span>

<span data-ttu-id="eb08a-334">Kliknutím na úlohu přepnutí slotu se odhalí Tato konfigurace úlohy:</span><span class="sxs-lookup"><span data-stu-id="eb08a-334">Clicking the slot swap task reveals the following task configuration:</span></span>

![Snímek obrazovky s úlohou přepnutí slotu pro uvolnění kanálu](media/cicd/release-definition-task2.png)

<span data-ttu-id="eb08a-336">K dispozici jsou údaje o předplatném, skupině prostředků, typu služby, názvu webové aplikace a slotu nasazení.</span><span class="sxs-lookup"><span data-stu-id="eb08a-336">The subscription, resource group, service type, web app name, and deployment slot details are provided.</span></span> <span data-ttu-id="eb08a-337">Zaškrtávací políčko **swap s výrobou** je zaškrtnuté.</span><span class="sxs-lookup"><span data-stu-id="eb08a-337">The **Swap with Production** check box is checked.</span></span> <span data-ttu-id="eb08a-338">V důsledku toho se bity nasazené do *přípravného* slotu odsadí do produkčního prostředí.</span><span class="sxs-lookup"><span data-stu-id="eb08a-338">Consequently, the bits deployed to the *staging* slot are swapped into the production environment.</span></span>

## <a name="additional-reading"></a><span data-ttu-id="eb08a-339">Další čtení</span><span class="sxs-lookup"><span data-stu-id="eb08a-339">Additional reading</span></span>

* [<span data-ttu-id="eb08a-340">Vytvoření prvního kanálu pomocí Azure Pipelines</span><span class="sxs-lookup"><span data-stu-id="eb08a-340">Create your first pipeline with Azure Pipelines</span></span>](/azure/devops/pipelines/get-started-yaml)
* [<span data-ttu-id="eb08a-341">Projekt sestavení a .NET Core</span><span class="sxs-lookup"><span data-stu-id="eb08a-341">Build and .NET Core project</span></span>](/azure/devops/pipelines/languages/dotnet-core)
* [<span data-ttu-id="eb08a-342">Nasazení webové aplikace pomocí Azure Pipelines</span><span class="sxs-lookup"><span data-stu-id="eb08a-342">Deploy a web app with Azure Pipelines</span></span>](/azure/devops/pipelines/targets/webapp)
