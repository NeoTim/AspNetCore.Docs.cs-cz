---
title: Publikování aplikace ASP.NET Core do Azure pomocí Visual Studia
author: rick-anderson
description: Zjistěte, jak publikovat aplikaci ASP.NET Core do služby Azure App Service pomocí Visual Studia.
ms.author: riande
ms.custom: mvc
ms.date: 07/10/2019
uid: tutorials/publish-to-azure-webapp-using-vs
ms.openlocfilehash: 7fc3644df3dcb957f2537538aaa9506c6b38a480
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78662202"
---
# <a name="publish-an-aspnet-core-app-to-azure-with-visual-studio"></a><span data-ttu-id="7591c-103">Publikování aplikace ASP.NET Core do Azure pomocí Visual Studia</span><span class="sxs-lookup"><span data-stu-id="7591c-103">Publish an ASP.NET Core app to Azure with Visual Studio</span></span>

<span data-ttu-id="7591c-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="7591c-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>
::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [Azure App Service Preview Notice](../includes/azure-apps-preview-notice.md)]

::: moniker-end


<span data-ttu-id="7591c-105">Pokud pracujete na macOS, přečtěte si [informace o publikování webové aplikace do služby Azure App Service pomocí Visual Studia pro Mac.](https://docs.microsoft.com/visualstudio/mac/publish-app-svc?view=vsmac-2019)</span><span class="sxs-lookup"><span data-stu-id="7591c-105">See [Publish a Web app to Azure App Service using Visual Studio for Mac](https://docs.microsoft.com/visualstudio/mac/publish-app-svc?view=vsmac-2019) if you are working on macOS.</span></span>

<span data-ttu-id="7591c-106">Informace o řešení problému s <xref:test/troubleshoot-azure-iis>nasazením služby App Service naleznete v tématu .</span><span class="sxs-lookup"><span data-stu-id="7591c-106">To troubleshoot an App Service deployment issue, see <xref:test/troubleshoot-azure-iis>.</span></span>

## <a name="set-up"></a><span data-ttu-id="7591c-107">Nastavit</span><span class="sxs-lookup"><span data-stu-id="7591c-107">Set up</span></span>

* <span data-ttu-id="7591c-108">Pokud ho nemáte, otevřete si [bezplatný účet Azure.](https://azure.microsoft.com/free/dotnet/)</span><span class="sxs-lookup"><span data-stu-id="7591c-108">Open a [free Azure account](https://azure.microsoft.com/free/dotnet/) if you don't have one.</span></span> 

## <a name="create-a-web-app"></a><span data-ttu-id="7591c-109">Vytvoření webové aplikace</span><span class="sxs-lookup"><span data-stu-id="7591c-109">Create a web app</span></span>

<span data-ttu-id="7591c-110">Na úvodní stránce Visual Studia vyberte **Soubor > Nový > project...**</span><span class="sxs-lookup"><span data-stu-id="7591c-110">In the Visual Studio Start Page, select **File > New > Project...**</span></span>

![Nabídka Soubor](publish-to-azure-webapp-using-vs/_static/file_new_project.png)

<span data-ttu-id="7591c-112">Dokončete dialogové okno **Nový projekt:**</span><span class="sxs-lookup"><span data-stu-id="7591c-112">Complete the **New Project** dialog:</span></span>

* <span data-ttu-id="7591c-113">V levém podokně vyberte **položku .NET Core**.</span><span class="sxs-lookup"><span data-stu-id="7591c-113">In the left pane, select **.NET Core**.</span></span>
* <span data-ttu-id="7591c-114">V prostředním podokně vyberte **ASP.NET Základní webová aplikace**.</span><span class="sxs-lookup"><span data-stu-id="7591c-114">In the center pane, select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="7591c-115">Vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="7591c-115">Select **OK**.</span></span>

![Dialogové okno Nový projekt](publish-to-azure-webapp-using-vs/_static/new_prj.png)

<span data-ttu-id="7591c-117">V dialogovém **okně Nová ASP.NET základní webová aplikace:**</span><span class="sxs-lookup"><span data-stu-id="7591c-117">In the **New ASP.NET Core Web Application** dialog:</span></span>

* <span data-ttu-id="7591c-118">Vyberte **webovou aplikaci**.</span><span class="sxs-lookup"><span data-stu-id="7591c-118">Select **Web Application**.</span></span>
* <span data-ttu-id="7591c-119">Vyberte **možnost Změnit ověřování**.</span><span class="sxs-lookup"><span data-stu-id="7591c-119">Select **Change Authentication**.</span></span>

![Dialogové okno Nový projekt](publish-to-azure-webapp-using-vs/_static/new_prj_2.png)

<span data-ttu-id="7591c-121">Zobrazí se dialogové okno **Změnit ověřování.**</span><span class="sxs-lookup"><span data-stu-id="7591c-121">The **Change Authentication** dialog appears.</span></span> 

* <span data-ttu-id="7591c-122">Vyberte **jednotlivé uživatelské účty**.</span><span class="sxs-lookup"><span data-stu-id="7591c-122">Select **Individual User Accounts**.</span></span>
* <span data-ttu-id="7591c-123">Výběrem **možnosti OK** se vrátíte do **nové ASP.NET základní webové aplikace**a pak znovu vyberte **OK.**</span><span class="sxs-lookup"><span data-stu-id="7591c-123">Select **OK** to return to the **New ASP.NET Core Web Application**, then select **OK** again.</span></span>

![Dialogové okno nového ASP.NET základního webového ověřování](publish-to-azure-webapp-using-vs/_static/new_prj_auth.png) 

<span data-ttu-id="7591c-125">Visual Studio vytvoří řešení.</span><span class="sxs-lookup"><span data-stu-id="7591c-125">Visual Studio creates the solution.</span></span>

## <a name="run-the-app"></a><span data-ttu-id="7591c-126">Spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="7591c-126">Run the app</span></span>

* <span data-ttu-id="7591c-127">Stisknutím kláves CTRL+F5 spusťte projekt.</span><span class="sxs-lookup"><span data-stu-id="7591c-127">Press CTRL+F5 to run the project.</span></span>
* <span data-ttu-id="7591c-128">Otestujte odkazy **O a** **Kontakt.**</span><span class="sxs-lookup"><span data-stu-id="7591c-128">Test the **About** and **Contact** links.</span></span>

![Webová aplikace otevřená v Microsoft Edge na localhost](publish-to-azure-webapp-using-vs/_static/show.png)

### <a name="register-a-user"></a><span data-ttu-id="7591c-130">Registrace uživatele</span><span class="sxs-lookup"><span data-stu-id="7591c-130">Register a user</span></span>

* <span data-ttu-id="7591c-131">Vyberte **Registrovat** a zaregistrujte nového uživatele.</span><span class="sxs-lookup"><span data-stu-id="7591c-131">Select **Register** and register a new user.</span></span> <span data-ttu-id="7591c-132">Můžete použít fiktivní e-mailovou adresu.</span><span class="sxs-lookup"><span data-stu-id="7591c-132">You can use a fictitious email address.</span></span> <span data-ttu-id="7591c-133">Při odeslání se na stránce zobrazí následující chyba:</span><span class="sxs-lookup"><span data-stu-id="7591c-133">When you submit, the page displays the following error:</span></span>

    <span data-ttu-id="7591c-134">*"Vnitřní chyba serveru: Při zpracování požadavku se nezdařila operace databáze. Výjimka SQL: Databázi nelze otevřít. Použití existující migrace pro kontext aplikační db může vyřešit tento problém."*</span><span class="sxs-lookup"><span data-stu-id="7591c-134">*"Internal Server Error: A database operation failed while processing the request. SQL exception: Cannot open the database. Applying existing migrations for Application DB context may resolve this issue."*</span></span>
* <span data-ttu-id="7591c-135">Vyberte **Použít migrace** a po aktualizaci stránky stránku aktualizujte.</span><span class="sxs-lookup"><span data-stu-id="7591c-135">Select **Apply Migrations** and, once the page updates, refresh the page.</span></span>

![Vnitřní chyba serveru: Při zpracování požadavku se nezdařila operace databáze.](publish-to-azure-webapp-using-vs/_static/mig.png)

<span data-ttu-id="7591c-139">Aplikace zobrazí e-mail použitý k registraci nového uživatele a odkaz **odhlásit.**</span><span class="sxs-lookup"><span data-stu-id="7591c-139">The app displays the email used to register the new user and a **Log out** link.</span></span>

![Webová aplikace otevřena v aplikaci Microsoft Edge.](publish-to-azure-webapp-using-vs/_static/hello.png)

## <a name="deploy-the-app-to-azure"></a><span data-ttu-id="7591c-142">Nasadit aplikaci do Azure</span><span class="sxs-lookup"><span data-stu-id="7591c-142">Deploy the app to Azure</span></span>

<span data-ttu-id="7591c-143">Klikněte pravým tlačítkem myši na projekt v Průzkumníku řešení a vyberte **publikovat...**.</span><span class="sxs-lookup"><span data-stu-id="7591c-143">Right-click on the project in Solution Explorer and select **Publish...**.</span></span>

![Otevřená kontextová nabídka se zvýrazněným odkazem Publikovat](publish-to-azure-webapp-using-vs/_static/pub.png)

<span data-ttu-id="7591c-145">V dialogovém okně **Publikovat:**</span><span class="sxs-lookup"><span data-stu-id="7591c-145">In the **Publish** dialog:</span></span>

* <span data-ttu-id="7591c-146">Vyberte **službu Microsoft Azure App Service**.</span><span class="sxs-lookup"><span data-stu-id="7591c-146">Select **Microsoft Azure App Service**.</span></span>
* <span data-ttu-id="7591c-147">Vyberte ikonu ozubeného kola a pak vyberte **Vytvořit profil**.</span><span class="sxs-lookup"><span data-stu-id="7591c-147">Select the gear icon and then select **Create Profile**.</span></span>
* <span data-ttu-id="7591c-148">Vyberte **Vytvořit profil**.</span><span class="sxs-lookup"><span data-stu-id="7591c-148">Select **Create Profile**.</span></span>

![Dialogové okno Publikovat](publish-to-azure-webapp-using-vs/_static/maas1.png)

### <a name="create-azure-resources"></a><span data-ttu-id="7591c-150">Vytvoření prostředků Azure</span><span class="sxs-lookup"><span data-stu-id="7591c-150">Create Azure resources</span></span>

<span data-ttu-id="7591c-151">Zobrazí se dialogové okno **Vytvořit službu App Service:**</span><span class="sxs-lookup"><span data-stu-id="7591c-151">The **Create App Service** dialog appears:</span></span>

* <span data-ttu-id="7591c-152">Zadejte své předplatné.</span><span class="sxs-lookup"><span data-stu-id="7591c-152">Enter your subscription.</span></span>
* <span data-ttu-id="7591c-153">Jsou vyplněna pole **Název aplikace**, **Skupina prostředků**a **Plán služby App** Service.</span><span class="sxs-lookup"><span data-stu-id="7591c-153">The **App Name**, **Resource Group**, and **App Service Plan** entry fields are populated.</span></span> <span data-ttu-id="7591c-154">Můžete si tyto názvy ponechat nebo je změnit.</span><span class="sxs-lookup"><span data-stu-id="7591c-154">You can keep these names or change them.</span></span>

![Dialogové okno Služba Aplikace](publish-to-azure-webapp-using-vs/_static/newrg1.png)

* <span data-ttu-id="7591c-156">Chcete-li vytvořit novou databázi, vyberte kartu **Služby.**</span><span class="sxs-lookup"><span data-stu-id="7591c-156">Select the **Services** tab to create a new database.</span></span>

* <span data-ttu-id="7591c-157">Výběrzelené **+** ikony pro vytvoření nové databáze SQL</span><span class="sxs-lookup"><span data-stu-id="7591c-157">Select the green **+** icon to create a new SQL Database</span></span>

![Nová databáze SQL](publish-to-azure-webapp-using-vs/_static/sql.png)

* <span data-ttu-id="7591c-159">**Vdialogovéce Konfigurovat databázi SQL** vyberte **Nový...** a vytvořte novou databázi.</span><span class="sxs-lookup"><span data-stu-id="7591c-159">Select **New...** on the **Configure SQL Database** dialog to create a new database.</span></span>

![Nová databáze sql a server](publish-to-azure-webapp-using-vs/_static/conf.png)

<span data-ttu-id="7591c-161">Zobrazí se dialogové okno **Konfigurovat sql server.**</span><span class="sxs-lookup"><span data-stu-id="7591c-161">The **Configure SQL Server** dialog appears.</span></span>

* <span data-ttu-id="7591c-162">Zadejte uživatelské jméno a heslo správce a vyberte **ok**.</span><span class="sxs-lookup"><span data-stu-id="7591c-162">Enter an administrator user name and password, and then select **OK**.</span></span> <span data-ttu-id="7591c-163">Můžete zachovat výchozí **název serveru**.</span><span class="sxs-lookup"><span data-stu-id="7591c-163">You can keep the default **Server Name**.</span></span> 

> [!NOTE]
> <span data-ttu-id="7591c-164">"admin" není povoleno jako uživatelské jméno správce.</span><span class="sxs-lookup"><span data-stu-id="7591c-164">"admin" isn't allowed as the administrator user name.</span></span>

![Dialogové okno Konfigurovat server SQL Server](publish-to-azure-webapp-using-vs/_static/conf_servername.png)

* <span data-ttu-id="7591c-166">Vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="7591c-166">Select **OK**.</span></span>

<span data-ttu-id="7591c-167">Visual Studio se vrátí do dialogového okna **Vytvořit službu aplikace.**</span><span class="sxs-lookup"><span data-stu-id="7591c-167">Visual Studio returns to the **Create App Service** dialog.</span></span>

* <span data-ttu-id="7591c-168">V dialogovém okně **Vytvořit službu aplikace** vyberte **Vytvořit.**</span><span class="sxs-lookup"><span data-stu-id="7591c-168">Select **Create** on the **Create App Service** dialog.</span></span>

![Dialogové okno Konfigurovat databázi SQL](publish-to-azure-webapp-using-vs/_static/conf_final.png)

<span data-ttu-id="7591c-170">Visual Studio vytvoří webovou aplikaci a SQL Server v Azure.</span><span class="sxs-lookup"><span data-stu-id="7591c-170">Visual Studio creates the Web app and SQL Server on Azure.</span></span> <span data-ttu-id="7591c-171">Tento krok může trvat několik minut.</span><span class="sxs-lookup"><span data-stu-id="7591c-171">This step can take a few minutes.</span></span> <span data-ttu-id="7591c-172">Informace o vytvořených zdrojích naleznete v tématu [Další zdroje](#additional-resources).</span><span class="sxs-lookup"><span data-stu-id="7591c-172">For information on the resources created, see [Additional resources](#additional-resources).</span></span>

<span data-ttu-id="7591c-173">Po dokončení nasazení vyberte **Nastavení**:</span><span class="sxs-lookup"><span data-stu-id="7591c-173">When deployment completes, select **Settings**:</span></span>

![Dialogové okno Konfigurovat server SQL Server](publish-to-azure-webapp-using-vs/_static/set.png)

<span data-ttu-id="7591c-175">Na stránce **Nastavení** dialogového okna **Publikovat:**</span><span class="sxs-lookup"><span data-stu-id="7591c-175">On the **Settings** page of the **Publish** dialog:</span></span>

* <span data-ttu-id="7591c-176">Rozbalte **položku Databáze** a **zaškrtněte políčko Použít tento připojovací řetězec za běhu**.</span><span class="sxs-lookup"><span data-stu-id="7591c-176">Expand **Databases** and check **Use this connection string at runtime**.</span></span>
* <span data-ttu-id="7591c-177">Rozbalte **migrace entity framework u** publikování . **Apply this migration on publish**</span><span class="sxs-lookup"><span data-stu-id="7591c-177">Expand **Entity Framework Migrations** and check **Apply this migration on publish**.</span></span>

* <span data-ttu-id="7591c-178">Vyberte **Uložit**.</span><span class="sxs-lookup"><span data-stu-id="7591c-178">Select **Save**.</span></span> <span data-ttu-id="7591c-179">Visual Studio se vrátí do dialogového okna **Publikovat.**</span><span class="sxs-lookup"><span data-stu-id="7591c-179">Visual Studio returns to the **Publish** dialog.</span></span> 

![Dialogové okno Publikovat: Panel Nastavení](publish-to-azure-webapp-using-vs/_static/pubs.png)

<span data-ttu-id="7591c-181">Klikněte na **Publikovat**.</span><span class="sxs-lookup"><span data-stu-id="7591c-181">Click **Publish**.</span></span> <span data-ttu-id="7591c-182">Visual Studio publikuje vaši aplikaci do Azure.</span><span class="sxs-lookup"><span data-stu-id="7591c-182">Visual Studio publishes your app to Azure.</span></span> <span data-ttu-id="7591c-183">Po dokončení nasazení se aplikace otevře v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="7591c-183">When the deployment completes, the app is opened in a browser.</span></span>

### <a name="test-your-app-in-azure"></a><span data-ttu-id="7591c-184">Testování aplikace v Azure</span><span class="sxs-lookup"><span data-stu-id="7591c-184">Test your app in Azure</span></span>

* <span data-ttu-id="7591c-185">Testování odkazů **O o** **kontaktech** a Kontakt</span><span class="sxs-lookup"><span data-stu-id="7591c-185">Test the **About** and **Contact** links</span></span>

* <span data-ttu-id="7591c-186">Registrace nového uživatele</span><span class="sxs-lookup"><span data-stu-id="7591c-186">Register a new user</span></span>

![Webová aplikace otevřená v Microsoft Edge ve službě Azure App Service](publish-to-azure-webapp-using-vs/_static/register.png)

### <a name="update-the-app"></a><span data-ttu-id="7591c-188">Aktualizace aplikace</span><span class="sxs-lookup"><span data-stu-id="7591c-188">Update the app</span></span>

* <span data-ttu-id="7591c-189">Upravte stránku *Pages/About.cshtml* Razor a změňte její obsah.</span><span class="sxs-lookup"><span data-stu-id="7591c-189">Edit the *Pages/About.cshtml* Razor page and change its contents.</span></span> <span data-ttu-id="7591c-190">Můžete například upravit odstavec tak, aby vyslovoval "Hello ASP.NET Core!":</span><span class="sxs-lookup"><span data-stu-id="7591c-190">For example, you can modify the paragraph to say "Hello ASP.NET Core!":</span></span>

    [!code-html[About](publish-to-azure-webapp-using-vs/sample/about.cshtml?highlight=9&range=1-9)]

* <span data-ttu-id="7591c-191">Klikněte pravým tlačítkem myši na projekt a vyberte **publikovat...** znovu.</span><span class="sxs-lookup"><span data-stu-id="7591c-191">Right-click on the project and select **Publish...** again.</span></span>

![Otevřená kontextová nabídka se zvýrazněným odkazem Publikovat](publish-to-azure-webapp-using-vs/_static/pub.png)

* <span data-ttu-id="7591c-193">Po publikování aplikace ověřte, zda jsou provedené změny dostupné v Azure.</span><span class="sxs-lookup"><span data-stu-id="7591c-193">After the app is published, verify the changes you made are available on Azure.</span></span>

![Ověření dokončení úkolu](publish-to-azure-webapp-using-vs/_static/final.png)

### <a name="clean-up"></a><span data-ttu-id="7591c-195">Vyčištění</span><span class="sxs-lookup"><span data-stu-id="7591c-195">Clean up</span></span>

<span data-ttu-id="7591c-196">Až dokončíte testování aplikace, přejděte na [portál Azure](https://portal.azure.com/) a aplikaci odstraňte.</span><span class="sxs-lookup"><span data-stu-id="7591c-196">When you have finished testing the app, go to the [Azure portal](https://portal.azure.com/) and delete the app.</span></span>

* <span data-ttu-id="7591c-197">Vyberte **skupiny prostředků**a vyberte skupinu prostředků, kterou jste vytvořili.</span><span class="sxs-lookup"><span data-stu-id="7591c-197">Select **Resource groups**, then select the resource group you created.</span></span>

![Portál Azure: Skupiny prostředků v postranním panelu](publish-to-azure-webapp-using-vs/_static/portalrg.png)

* <span data-ttu-id="7591c-199">Na stránce **Skupiny prostředků** vyberte **Odstranit**.</span><span class="sxs-lookup"><span data-stu-id="7591c-199">In the **Resource groups** page, select **Delete**.</span></span>

![Azure Portal: Stránka Skupiny prostředků](publish-to-azure-webapp-using-vs/_static/rgd.png)

* <span data-ttu-id="7591c-201">Zadejte název skupiny prostředků a vyberte **Odstranit**.</span><span class="sxs-lookup"><span data-stu-id="7591c-201">Enter the name of the resource group and select **Delete**.</span></span> <span data-ttu-id="7591c-202">Vaše aplikace a všechny ostatní prostředky vytvořené v tomto kurzu se teď odstraní z Azure.</span><span class="sxs-lookup"><span data-stu-id="7591c-202">Your app and all other resources created in this tutorial are now deleted from Azure.</span></span>

### <a name="next-steps"></a><span data-ttu-id="7591c-203">Další kroky</span><span class="sxs-lookup"><span data-stu-id="7591c-203">Next steps</span></span>

* <xref:host-and-deploy/azure-apps/azure-continuous-deployment>

## <a name="additional-resources"></a><span data-ttu-id="7591c-204">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="7591c-204">Additional resources</span></span>

* <span data-ttu-id="7591c-205">Kód sady Visual Studio najdete [v tématu Publikování profilů](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles).</span><span class="sxs-lookup"><span data-stu-id="7591c-205">For Visual Studio Code, see [Publish profiles](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles).</span></span>
* [<span data-ttu-id="7591c-206">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="7591c-206">Azure App Service</span></span>](/azure/app-service/app-service-web-overview)
* [<span data-ttu-id="7591c-207">Skupiny prostředků Azure</span><span class="sxs-lookup"><span data-stu-id="7591c-207">Azure resource groups</span></span>](/azure/azure-resource-manager/resource-group-overview#resource-groups)
* [<span data-ttu-id="7591c-208">Azure SQL Database</span><span class="sxs-lookup"><span data-stu-id="7591c-208">Azure SQL Database</span></span>](/azure/sql-database/)
* <xref:host-and-deploy/visual-studio-publish-profiles>
* <xref:test/troubleshoot-azure-iis>
