---
title: Publikování aplikace ASP.NET Core do Azure pomocí sady Visual Studio
author: rick-anderson
description: Zjistěte, jak publikovat aplikace ASP.NET Core do služby Azure App Service pomocí sady Visual Studio.
ms.author: riande
ms.custom: mvc
ms.date: 07/10/2019
uid: tutorials/publish-to-azure-webapp-using-vs
ms.openlocfilehash: 7fc3644df3dcb957f2537538aaa9506c6b38a480
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78662202"
---
# <a name="publish-an-aspnet-core-app-to-azure-with-visual-studio"></a><span data-ttu-id="b2ae6-103">Publikování aplikace ASP.NET Core do Azure pomocí sady Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b2ae6-103">Publish an ASP.NET Core app to Azure with Visual Studio</span></span>

<span data-ttu-id="b2ae6-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="b2ae6-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>
::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [Azure App Service Preview Notice](../includes/azure-apps-preview-notice.md)]

::: moniker-end


<span data-ttu-id="b2ae6-105">Pokud pracujete na macOS, přečtěte si téma [publikování webové aplikace pro Azure App Service používání Visual Studio pro Mac](https://docs.microsoft.com/visualstudio/mac/publish-app-svc?view=vsmac-2019) .</span><span class="sxs-lookup"><span data-stu-id="b2ae6-105">See [Publish a Web app to Azure App Service using Visual Studio for Mac](https://docs.microsoft.com/visualstudio/mac/publish-app-svc?view=vsmac-2019) if you are working on macOS.</span></span>

<span data-ttu-id="b2ae6-106">Řešení potíží s nasazením App Service najdete v tématu <xref:test/troubleshoot-azure-iis>.</span><span class="sxs-lookup"><span data-stu-id="b2ae6-106">To troubleshoot an App Service deployment issue, see <xref:test/troubleshoot-azure-iis>.</span></span>

## <a name="set-up"></a><span data-ttu-id="b2ae6-107">Nastavení</span><span class="sxs-lookup"><span data-stu-id="b2ae6-107">Set up</span></span>

* <span data-ttu-id="b2ae6-108">Pokud ho nemáte, otevřete [bezplatný účet Azure](https://azure.microsoft.com/free/dotnet/) .</span><span class="sxs-lookup"><span data-stu-id="b2ae6-108">Open a [free Azure account](https://azure.microsoft.com/free/dotnet/) if you don't have one.</span></span> 

## <a name="create-a-web-app"></a><span data-ttu-id="b2ae6-109">Vytvoření webové aplikace</span><span class="sxs-lookup"><span data-stu-id="b2ae6-109">Create a web app</span></span>

<span data-ttu-id="b2ae6-110">Na úvodní stránce sady Visual Studio vyberte **soubor > nový > projekt...**</span><span class="sxs-lookup"><span data-stu-id="b2ae6-110">In the Visual Studio Start Page, select **File > New > Project...**</span></span>

![Nabídka Soubor](publish-to-azure-webapp-using-vs/_static/file_new_project.png)

<span data-ttu-id="b2ae6-112">Dokončete dialog **nového projektu** :</span><span class="sxs-lookup"><span data-stu-id="b2ae6-112">Complete the **New Project** dialog:</span></span>

* <span data-ttu-id="b2ae6-113">V levém podokně vyberte **.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="b2ae6-113">In the left pane, select **.NET Core**.</span></span>
* <span data-ttu-id="b2ae6-114">V prostředním podokně vyberte **ASP.NET Core webová aplikace**.</span><span class="sxs-lookup"><span data-stu-id="b2ae6-114">In the center pane, select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="b2ae6-115">Vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="b2ae6-115">Select **OK**.</span></span>

![Dialogové okno Nový projekt](publish-to-azure-webapp-using-vs/_static/new_prj.png)

<span data-ttu-id="b2ae6-117">V dialogovém okně **nový ASP.NET Core webové aplikace** :</span><span class="sxs-lookup"><span data-stu-id="b2ae6-117">In the **New ASP.NET Core Web Application** dialog:</span></span>

* <span data-ttu-id="b2ae6-118">Vyberte **Webová aplikace**.</span><span class="sxs-lookup"><span data-stu-id="b2ae6-118">Select **Web Application**.</span></span>
* <span data-ttu-id="b2ae6-119">Vyberte **změnit ověřování**.</span><span class="sxs-lookup"><span data-stu-id="b2ae6-119">Select **Change Authentication**.</span></span>

![Dialogové okno Nový projekt](publish-to-azure-webapp-using-vs/_static/new_prj_2.png)

<span data-ttu-id="b2ae6-121">Zobrazí se dialogové okno **změnit ověřování** .</span><span class="sxs-lookup"><span data-stu-id="b2ae6-121">The **Change Authentication** dialog appears.</span></span> 

* <span data-ttu-id="b2ae6-122">Vyberte **jednotlivé uživatelské účty**.</span><span class="sxs-lookup"><span data-stu-id="b2ae6-122">Select **Individual User Accounts**.</span></span>
* <span data-ttu-id="b2ae6-123">Kliknutím na **tlačítko OK** se vraťte do **nové ASP.NET Core webové aplikace**a pak znovu vyberte **OK** .</span><span class="sxs-lookup"><span data-stu-id="b2ae6-123">Select **OK** to return to the **New ASP.NET Core Web Application**, then select **OK** again.</span></span>

![Nové dialogové okno ověřování webové aplikace ASP.NET Core](publish-to-azure-webapp-using-vs/_static/new_prj_auth.png) 

<span data-ttu-id="b2ae6-125">Visual Studio vytvoří řešení.</span><span class="sxs-lookup"><span data-stu-id="b2ae6-125">Visual Studio creates the solution.</span></span>

## <a name="run-the-app"></a><span data-ttu-id="b2ae6-126">Spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="b2ae6-126">Run the app</span></span>

* <span data-ttu-id="b2ae6-127">Stiskněte kombinaci kláves CTRL + F5 ke spuštění projektu.</span><span class="sxs-lookup"><span data-stu-id="b2ae6-127">Press CTRL+F5 to run the project.</span></span>
* <span data-ttu-id="b2ae6-128">Otestujte odkazy **na adresu** a **kontakt** .</span><span class="sxs-lookup"><span data-stu-id="b2ae6-128">Test the **About** and **Contact** links.</span></span>

![Otevření webové aplikace v Microsoft Edge na místního hostitele](publish-to-azure-webapp-using-vs/_static/show.png)

### <a name="register-a-user"></a><span data-ttu-id="b2ae6-130">Registrace uživatele</span><span class="sxs-lookup"><span data-stu-id="b2ae6-130">Register a user</span></span>

* <span data-ttu-id="b2ae6-131">Vyberte **Registrovat** a registrovat nového uživatele.</span><span class="sxs-lookup"><span data-stu-id="b2ae6-131">Select **Register** and register a new user.</span></span> <span data-ttu-id="b2ae6-132">Můžete použít fiktivní e-mailovou adresu.</span><span class="sxs-lookup"><span data-stu-id="b2ae6-132">You can use a fictitious email address.</span></span> <span data-ttu-id="b2ae6-133">Když odešlete, na stránce se zobrazí následující chyba:</span><span class="sxs-lookup"><span data-stu-id="b2ae6-133">When you submit, the page displays the following error:</span></span>

    <span data-ttu-id="b2ae6-134">*"Došlo k vnitřní chybě serveru: databázová operace se při zpracování požadavku nezdařila. Výjimka SQL: databázi nelze otevřít. Tento problém může vyřešit použití existujících migrací pro kontext aplikační databáze. "*</span><span class="sxs-lookup"><span data-stu-id="b2ae6-134">*"Internal Server Error: A database operation failed while processing the request. SQL exception: Cannot open the database. Applying existing migrations for Application DB context may resolve this issue."*</span></span>
* <span data-ttu-id="b2ae6-135">Vyberte možnost **použít migrace** a po aktualizaci stránky aktualizujte stránku.</span><span class="sxs-lookup"><span data-stu-id="b2ae6-135">Select **Apply Migrations** and, once the page updates, refresh the page.</span></span>

![Vnitřní chyba serveru: Databázová operace selhala při zpracování požadavku.](publish-to-azure-webapp-using-vs/_static/mig.png)

<span data-ttu-id="b2ae6-139">Aplikace zobrazí e-mail, který se používá k registraci nového uživatele a odkaz na **odhlášení** .</span><span class="sxs-lookup"><span data-stu-id="b2ae6-139">The app displays the email used to register the new user and a **Log out** link.</span></span>

![Webová aplikace otevřít v Microsoft Edge.](publish-to-azure-webapp-using-vs/_static/hello.png)

## <a name="deploy-the-app-to-azure"></a><span data-ttu-id="b2ae6-142">Nasadit aplikaci do Azure</span><span class="sxs-lookup"><span data-stu-id="b2ae6-142">Deploy the app to Azure</span></span>

<span data-ttu-id="b2ae6-143">Klikněte pravým tlačítkem na projekt v Průzkumník řešení a vyberte **publikovat...** .</span><span class="sxs-lookup"><span data-stu-id="b2ae6-143">Right-click on the project in Solution Explorer and select **Publish...**.</span></span>

![Kontextová nabídka otevřít se zvýrazněným odkazem publikovat](publish-to-azure-webapp-using-vs/_static/pub.png)

<span data-ttu-id="b2ae6-145">V dialogovém okně **publikovat** :</span><span class="sxs-lookup"><span data-stu-id="b2ae6-145">In the **Publish** dialog:</span></span>

* <span data-ttu-id="b2ae6-146">Vyberte **Microsoft Azure App Service**.</span><span class="sxs-lookup"><span data-stu-id="b2ae6-146">Select **Microsoft Azure App Service**.</span></span>
* <span data-ttu-id="b2ae6-147">Vyberte ikonu ozubeného kolečka a pak vyberte **vytvořit profil**.</span><span class="sxs-lookup"><span data-stu-id="b2ae6-147">Select the gear icon and then select **Create Profile**.</span></span>
* <span data-ttu-id="b2ae6-148">Vyberte **vytvořit profil**.</span><span class="sxs-lookup"><span data-stu-id="b2ae6-148">Select **Create Profile**.</span></span>

![Dialogové okno pro publikování](publish-to-azure-webapp-using-vs/_static/maas1.png)

### <a name="create-azure-resources"></a><span data-ttu-id="b2ae6-150">Vytvoření prostředků Azure</span><span class="sxs-lookup"><span data-stu-id="b2ae6-150">Create Azure resources</span></span>

<span data-ttu-id="b2ae6-151">Zobrazí se dialogové okno **vytvořit App Service** :</span><span class="sxs-lookup"><span data-stu-id="b2ae6-151">The **Create App Service** dialog appears:</span></span>

* <span data-ttu-id="b2ae6-152">Zadejte vaše předplatné.</span><span class="sxs-lookup"><span data-stu-id="b2ae6-152">Enter your subscription.</span></span>
* <span data-ttu-id="b2ae6-153">Vyplní se pole **název aplikace**, **Skupina prostředků**a položka **plánu App Service** .</span><span class="sxs-lookup"><span data-stu-id="b2ae6-153">The **App Name**, **Resource Group**, and **App Service Plan** entry fields are populated.</span></span> <span data-ttu-id="b2ae6-154">Můžete ponechat tyto názvy nebo je změnit.</span><span class="sxs-lookup"><span data-stu-id="b2ae6-154">You can keep these names or change them.</span></span>

![Dialogové okno služby App Service](publish-to-azure-webapp-using-vs/_static/newrg1.png)

* <span data-ttu-id="b2ae6-156">Vyberte kartu **služby** a vytvořte novou databázi.</span><span class="sxs-lookup"><span data-stu-id="b2ae6-156">Select the **Services** tab to create a new database.</span></span>

* <span data-ttu-id="b2ae6-157">Vyberte zelenou **+** ikonu pro vytvoření nového SQL Database</span><span class="sxs-lookup"><span data-stu-id="b2ae6-157">Select the green **+** icon to create a new SQL Database</span></span>

![Nová databáze SQL](publish-to-azure-webapp-using-vs/_static/sql.png)

* <span data-ttu-id="b2ae6-159">Vyberte **Nový...** v dialogovém okně **Konfigurace SQL Database** vytvořte novou databázi.</span><span class="sxs-lookup"><span data-stu-id="b2ae6-159">Select **New...** on the **Configure SQL Database** dialog to create a new database.</span></span>

![Nová databáze SQL a server](publish-to-azure-webapp-using-vs/_static/conf.png)

<span data-ttu-id="b2ae6-161">Zobrazí se dialogové okno **konfigurace SQL Server** .</span><span class="sxs-lookup"><span data-stu-id="b2ae6-161">The **Configure SQL Server** dialog appears.</span></span>

* <span data-ttu-id="b2ae6-162">Zadejte uživatelské jméno a heslo správce a pak vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="b2ae6-162">Enter an administrator user name and password, and then select **OK**.</span></span> <span data-ttu-id="b2ae6-163">Můžete ponechat výchozí **název serveru**.</span><span class="sxs-lookup"><span data-stu-id="b2ae6-163">You can keep the default **Server Name**.</span></span> 

> [!NOTE]
> <span data-ttu-id="b2ae6-164">"admin" není povolen jako uživatelské jméno správce.</span><span class="sxs-lookup"><span data-stu-id="b2ae6-164">"admin" isn't allowed as the administrator user name.</span></span>

![Konfigurace systému SQL Server dialogového okna](publish-to-azure-webapp-using-vs/_static/conf_servername.png)

* <span data-ttu-id="b2ae6-166">Vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="b2ae6-166">Select **OK**.</span></span>

<span data-ttu-id="b2ae6-167">Visual Studio se vrátí do dialogového okna **vytvořit App Service** .</span><span class="sxs-lookup"><span data-stu-id="b2ae6-167">Visual Studio returns to the **Create App Service** dialog.</span></span>

* <span data-ttu-id="b2ae6-168">V dialogovém okně **vytvořit App Service** vyberte **vytvořit** .</span><span class="sxs-lookup"><span data-stu-id="b2ae6-168">Select **Create** on the **Create App Service** dialog.</span></span>

![Konfigurace SQL Database dialogového okna](publish-to-azure-webapp-using-vs/_static/conf_final.png)

<span data-ttu-id="b2ae6-170">Visual Studio vytvoří webovou aplikaci a SQL Server v Azure.</span><span class="sxs-lookup"><span data-stu-id="b2ae6-170">Visual Studio creates the Web app and SQL Server on Azure.</span></span> <span data-ttu-id="b2ae6-171">Tento krok může trvat několik minut.</span><span class="sxs-lookup"><span data-stu-id="b2ae6-171">This step can take a few minutes.</span></span> <span data-ttu-id="b2ae6-172">Informace o vytvořených prostředcích najdete v tématu [Další zdroje](#additional-resources)informací.</span><span class="sxs-lookup"><span data-stu-id="b2ae6-172">For information on the resources created, see [Additional resources](#additional-resources).</span></span>

<span data-ttu-id="b2ae6-173">Po dokončení nasazení vyberte **Nastavení**:</span><span class="sxs-lookup"><span data-stu-id="b2ae6-173">When deployment completes, select **Settings**:</span></span>

![Konfigurace systému SQL Server dialogového okna](publish-to-azure-webapp-using-vs/_static/set.png)

<span data-ttu-id="b2ae6-175">Na stránce **Nastavení** dialogového okna **publikovat** :</span><span class="sxs-lookup"><span data-stu-id="b2ae6-175">On the **Settings** page of the **Publish** dialog:</span></span>

* <span data-ttu-id="b2ae6-176">Rozbalte položku **databáze** a potom **pomocí tohoto připojovacího řetězce za běhu**.</span><span class="sxs-lookup"><span data-stu-id="b2ae6-176">Expand **Databases** and check **Use this connection string at runtime**.</span></span>
* <span data-ttu-id="b2ae6-177">Rozbalte **Entity Framework migrace** a **při publikování zaškrtnout možnost použít tuto migraci**.</span><span class="sxs-lookup"><span data-stu-id="b2ae6-177">Expand **Entity Framework Migrations** and check **Apply this migration on publish**.</span></span>

* <span data-ttu-id="b2ae6-178">Vyberte **Save** (Uložit).</span><span class="sxs-lookup"><span data-stu-id="b2ae6-178">Select **Save**.</span></span> <span data-ttu-id="b2ae6-179">Visual Studio se vrátí do dialogového okna **publikovat** .</span><span class="sxs-lookup"><span data-stu-id="b2ae6-179">Visual Studio returns to the **Publish** dialog.</span></span> 

![Dialogové okno pro publikování: panel nastavení](publish-to-azure-webapp-using-vs/_static/pubs.png)

<span data-ttu-id="b2ae6-181">Klikněte na **Publikovat**.</span><span class="sxs-lookup"><span data-stu-id="b2ae6-181">Click **Publish**.</span></span> <span data-ttu-id="b2ae6-182">Visual Studio publikuje vaši aplikaci do Azure.</span><span class="sxs-lookup"><span data-stu-id="b2ae6-182">Visual Studio publishes your app to Azure.</span></span> <span data-ttu-id="b2ae6-183">Po dokončení nasazení aplikace se otevře v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="b2ae6-183">When the deployment completes, the app is opened in a browser.</span></span>

### <a name="test-your-app-in-azure"></a><span data-ttu-id="b2ae6-184">Testování aplikace v Azure</span><span class="sxs-lookup"><span data-stu-id="b2ae6-184">Test your app in Azure</span></span>

* <span data-ttu-id="b2ae6-185">Testování odkazů **About** a **kontakt**</span><span class="sxs-lookup"><span data-stu-id="b2ae6-185">Test the **About** and **Contact** links</span></span>

* <span data-ttu-id="b2ae6-186">Registrace nového uživatele</span><span class="sxs-lookup"><span data-stu-id="b2ae6-186">Register a new user</span></span>

![Otevřít v Microsoft Edge ve službě Azure App Service webovou aplikaci](publish-to-azure-webapp-using-vs/_static/register.png)

### <a name="update-the-app"></a><span data-ttu-id="b2ae6-188">Aktualizace aplikace</span><span class="sxs-lookup"><span data-stu-id="b2ae6-188">Update the app</span></span>

* <span data-ttu-id="b2ae6-189">Upravte stránku *stránky/About. cshtml* Razor a změňte její obsah.</span><span class="sxs-lookup"><span data-stu-id="b2ae6-189">Edit the *Pages/About.cshtml* Razor page and change its contents.</span></span> <span data-ttu-id="b2ae6-190">Například můžete změnit odstavec a vyslovit "Hello ASP.NET Core!":</span><span class="sxs-lookup"><span data-stu-id="b2ae6-190">For example, you can modify the paragraph to say "Hello ASP.NET Core!":</span></span>

    [!code-html[About](publish-to-azure-webapp-using-vs/sample/about.cshtml?highlight=9&range=1-9)]

* <span data-ttu-id="b2ae6-191">Klikněte pravým tlačítkem na projekt a vyberte **publikovat...** znovu.</span><span class="sxs-lookup"><span data-stu-id="b2ae6-191">Right-click on the project and select **Publish...** again.</span></span>

![Kontextová nabídka otevřít se zvýrazněným odkazem publikovat](publish-to-azure-webapp-using-vs/_static/pub.png)

* <span data-ttu-id="b2ae6-193">Po publikování aplikace, ověřte, zda jsou k dispozici v Azure, provedené změny.</span><span class="sxs-lookup"><span data-stu-id="b2ae6-193">After the app is published, verify the changes you made are available on Azure.</span></span>

![Ověřte, zda je úkol dokončený](publish-to-azure-webapp-using-vs/_static/final.png)

### <a name="clean-up"></a><span data-ttu-id="b2ae6-195">Vyčištění</span><span class="sxs-lookup"><span data-stu-id="b2ae6-195">Clean up</span></span>

<span data-ttu-id="b2ae6-196">Po dokončení testování aplikace přejdete na [Azure Portal](https://portal.azure.com/) a aplikaci odstraníte.</span><span class="sxs-lookup"><span data-stu-id="b2ae6-196">When you have finished testing the app, go to the [Azure portal](https://portal.azure.com/) and delete the app.</span></span>

* <span data-ttu-id="b2ae6-197">Vyberte **skupiny prostředků**a pak vyberte skupinu prostředků, kterou jste vytvořili.</span><span class="sxs-lookup"><span data-stu-id="b2ae6-197">Select **Resource groups**, then select the resource group you created.</span></span>

![Azure Portal: Skupiny prostředků v nabídce bočního panelu](publish-to-azure-webapp-using-vs/_static/portalrg.png)

* <span data-ttu-id="b2ae6-199">Na stránce **skupiny prostředků** vyberte **Odstranit**.</span><span class="sxs-lookup"><span data-stu-id="b2ae6-199">In the **Resource groups** page, select **Delete**.</span></span>

![Azure Portal: Stránce skupiny prostředků](publish-to-azure-webapp-using-vs/_static/rgd.png)

* <span data-ttu-id="b2ae6-201">Zadejte název skupiny prostředků a vyberte **Odstranit**.</span><span class="sxs-lookup"><span data-stu-id="b2ae6-201">Enter the name of the resource group and select **Delete**.</span></span> <span data-ttu-id="b2ae6-202">Vaše aplikace a všechny další prostředky vytvořené v tomto kurzu se teď odstraní z Azure.</span><span class="sxs-lookup"><span data-stu-id="b2ae6-202">Your app and all other resources created in this tutorial are now deleted from Azure.</span></span>

### <a name="next-steps"></a><span data-ttu-id="b2ae6-203">Další kroky</span><span class="sxs-lookup"><span data-stu-id="b2ae6-203">Next steps</span></span>

* <xref:host-and-deploy/azure-apps/azure-continuous-deployment>

## <a name="additional-resources"></a><span data-ttu-id="b2ae6-204">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="b2ae6-204">Additional resources</span></span>

* <span data-ttu-id="b2ae6-205">Visual Studio Code najdete v tématu [publikování profilů](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles).</span><span class="sxs-lookup"><span data-stu-id="b2ae6-205">For Visual Studio Code, see [Publish profiles](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles).</span></span>
* [<span data-ttu-id="b2ae6-206">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="b2ae6-206">Azure App Service</span></span>](/azure/app-service/app-service-web-overview)
* [<span data-ttu-id="b2ae6-207">Skupiny prostředků Azure</span><span class="sxs-lookup"><span data-stu-id="b2ae6-207">Azure resource groups</span></span>](/azure/azure-resource-manager/resource-group-overview#resource-groups)
* [<span data-ttu-id="b2ae6-208">Azure SQL Database</span><span class="sxs-lookup"><span data-stu-id="b2ae6-208">Azure SQL Database</span></span>](/azure/sql-database/)
* <xref:host-and-deploy/visual-studio-publish-profiles>
* <xref:test/troubleshoot-azure-iis>
