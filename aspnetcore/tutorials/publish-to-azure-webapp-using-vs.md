---
title: Publikování aplikace ASP.NET Core do Azure pomocí sady Visual Studio
author: rick-anderson
description: Naučte se publikovat aplikaci ASP.NET Core pro Azure App Service pomocí sady Visual Studio.
ms.author: riande
ms.custom: mvc
ms.date: 07/10/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/publish-to-azure-webapp-using-vs
ms.openlocfilehash: d805d57fd1e2d83d0148900993e4bf6108a13028
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2020
ms.locfileid: "85408406"
---
# <a name="publish-an-aspnet-core-app-to-azure-with-visual-studio"></a><span data-ttu-id="5fc91-103">Publikování aplikace ASP.NET Core do Azure pomocí sady Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5fc91-103">Publish an ASP.NET Core app to Azure with Visual Studio</span></span>

<span data-ttu-id="5fc91-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="5fc91-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>
::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [Azure App Service Preview Notice](../includes/azure-apps-preview-notice.md)]

::: moniker-end


<span data-ttu-id="5fc91-105">Pokud pracujete na macOS, přečtěte si téma [publikování webové aplikace pro Azure App Service používání Visual Studio pro Mac](https://docs.microsoft.com/visualstudio/mac/publish-app-svc?view=vsmac-2019) .</span><span class="sxs-lookup"><span data-stu-id="5fc91-105">See [Publish a Web app to Azure App Service using Visual Studio for Mac](https://docs.microsoft.com/visualstudio/mac/publish-app-svc?view=vsmac-2019) if you are working on macOS.</span></span>

<span data-ttu-id="5fc91-106">Problémy s nasazením App Service najdete v tématu <xref:test/troubleshoot-azure-iis> .</span><span class="sxs-lookup"><span data-stu-id="5fc91-106">To troubleshoot an App Service deployment issue, see <xref:test/troubleshoot-azure-iis>.</span></span>

## <a name="set-up"></a><span data-ttu-id="5fc91-107">Nastavení</span><span class="sxs-lookup"><span data-stu-id="5fc91-107">Set up</span></span>

* <span data-ttu-id="5fc91-108">Pokud ho nemáte, otevřete [bezplatný účet Azure](https://azure.microsoft.com/free/dotnet/) .</span><span class="sxs-lookup"><span data-stu-id="5fc91-108">Open a [free Azure account](https://azure.microsoft.com/free/dotnet/) if you don't have one.</span></span> 

## <a name="create-a-web-app"></a><span data-ttu-id="5fc91-109">Vytvoření webové aplikace</span><span class="sxs-lookup"><span data-stu-id="5fc91-109">Create a web app</span></span>

<span data-ttu-id="5fc91-110">Na úvodní stránce sady Visual Studio vyberte **soubor > nový > projekt...**</span><span class="sxs-lookup"><span data-stu-id="5fc91-110">In the Visual Studio Start Page, select **File > New > Project...**</span></span>

![Nabídka Soubor](publish-to-azure-webapp-using-vs/_static/file_new_project.png)

<span data-ttu-id="5fc91-112">Dokončete dialog **nového projektu** :</span><span class="sxs-lookup"><span data-stu-id="5fc91-112">Complete the **New Project** dialog:</span></span>

* <span data-ttu-id="5fc91-113">Vyberte **ASP.NET Core webové aplikace**.</span><span class="sxs-lookup"><span data-stu-id="5fc91-113">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="5fc91-114">Vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="5fc91-114">Select **Next**.</span></span>

![Dialogové okno Nový projekt](publish-to-azure-webapp-using-vs/_static/new_prj.png)

<span data-ttu-id="5fc91-116">V dialogovém okně **nový ASP.NET Core webové aplikace** :</span><span class="sxs-lookup"><span data-stu-id="5fc91-116">In the **New ASP.NET Core Web Application** dialog:</span></span>

* <span data-ttu-id="5fc91-117">Vyberte **Webová aplikace**.</span><span class="sxs-lookup"><span data-stu-id="5fc91-117">Select **Web Application**.</span></span>
* <span data-ttu-id="5fc91-118">V části ověřování vyberte **změnit** .</span><span class="sxs-lookup"><span data-stu-id="5fc91-118">Select **Change** under Authentication.</span></span>

![Dialogové okno Nový ASP.NET Core Web](publish-to-azure-webapp-using-vs/_static/new_prj_2.png)

<span data-ttu-id="5fc91-120">Zobrazí se dialogové okno **změnit ověřování** .</span><span class="sxs-lookup"><span data-stu-id="5fc91-120">The **Change Authentication** dialog appears.</span></span> 

* <span data-ttu-id="5fc91-121">Vyberte **jednotlivé uživatelské účty**.</span><span class="sxs-lookup"><span data-stu-id="5fc91-121">Select **Individual User Accounts**.</span></span>
* <span data-ttu-id="5fc91-122">Kliknutím na **tlačítko OK** se vraťte do **nové ASP.NET Core webové aplikace**a pak vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="5fc91-122">Select **OK** to return to the **New ASP.NET Core Web Application**, then select **Create**.</span></span>

![Dialog nové ASP.NET Core webového ověřování](publish-to-azure-webapp-using-vs/_static/new_prj_auth.png) 

<span data-ttu-id="5fc91-124">Visual Studio vytvoří řešení.</span><span class="sxs-lookup"><span data-stu-id="5fc91-124">Visual Studio creates the solution.</span></span>

## <a name="run-the-app"></a><span data-ttu-id="5fc91-125">Spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="5fc91-125">Run the app</span></span>

* <span data-ttu-id="5fc91-126">Stisknutím kombinace kláves CTRL + F5 spusťte projekt.</span><span class="sxs-lookup"><span data-stu-id="5fc91-126">Press CTRL+F5 to run the project.</span></span>
* <span data-ttu-id="5fc91-127">Otestujte odkaz na **ochranu osobních údajů** .</span><span class="sxs-lookup"><span data-stu-id="5fc91-127">Test the **Privacy** link.</span></span>

![Webová aplikace otevřená v Microsoft Edge na místním hostiteli](publish-to-azure-webapp-using-vs/_static/show.png)

### <a name="register-a-user"></a><span data-ttu-id="5fc91-129">Registrace uživatele</span><span class="sxs-lookup"><span data-stu-id="5fc91-129">Register a user</span></span>

* <span data-ttu-id="5fc91-130">Vyberte **Registrovat** a registrovat nového uživatele.</span><span class="sxs-lookup"><span data-stu-id="5fc91-130">Select **Register** and register a new user.</span></span> <span data-ttu-id="5fc91-131">Můžete použít fiktivní e-mailovou adresu.</span><span class="sxs-lookup"><span data-stu-id="5fc91-131">You can use a fictitious email address.</span></span> <span data-ttu-id="5fc91-132">Při odeslání se na stránce zobrazí následující chyba:</span><span class="sxs-lookup"><span data-stu-id="5fc91-132">When you submit, the page displays the following error:</span></span>

    <span data-ttu-id="5fc91-133">*Při zpracování požadavku se databázová operace nezdařila. Tento problém může vyřešit použití existujících migrací pro kontext aplikační databáze. "*</span><span class="sxs-lookup"><span data-stu-id="5fc91-133">*"A database operation failed while processing the request. Applying existing migrations for Application DB context may resolve this issue."*</span></span>
* <span data-ttu-id="5fc91-134">Vyberte možnost **použít migrace** a po aktualizaci stránky aktualizujte stránku.</span><span class="sxs-lookup"><span data-stu-id="5fc91-134">Select **Apply Migrations** and, once the page updates, refresh the page.</span></span>

![Databázová operace se při zpracování požadavku nezdařila.](publish-to-azure-webapp-using-vs/_static/mig.png)

<span data-ttu-id="5fc91-137">Aplikace zobrazí e-mail, který se používá k registraci nového uživatele a odkaz na **odhlášení** .</span><span class="sxs-lookup"><span data-stu-id="5fc91-137">The app displays the email used to register the new user and a **Logout** link.</span></span>

![Webová aplikace je otevřená v Microsoft Edge.](publish-to-azure-webapp-using-vs/_static/hello.png)

## <a name="deploy-the-app-to-azure"></a><span data-ttu-id="5fc91-140">Nasadit aplikaci do Azure</span><span class="sxs-lookup"><span data-stu-id="5fc91-140">Deploy the app to Azure</span></span>

<span data-ttu-id="5fc91-141">Klikněte pravým tlačítkem na projekt v Průzkumník řešení a vyberte **publikovat...**.</span><span class="sxs-lookup"><span data-stu-id="5fc91-141">Right-click on the project in Solution Explorer and select **Publish...**.</span></span>

![Kontextová nabídka otevřená se zvýrazněným odkazem pro publikování](publish-to-azure-webapp-using-vs/_static/pub.png)

<span data-ttu-id="5fc91-143">V dialogovém okně **publikovat** :</span><span class="sxs-lookup"><span data-stu-id="5fc91-143">In the **Publish** dialog:</span></span>

* <span data-ttu-id="5fc91-144">Vyberte **Azure**.</span><span class="sxs-lookup"><span data-stu-id="5fc91-144">Select **Azure**.</span></span>
* <span data-ttu-id="5fc91-145">Vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="5fc91-145">Select **Next**.</span></span>

![Dialog publikovat](publish-to-azure-webapp-using-vs/_static/maas1.png)

<span data-ttu-id="5fc91-147">V dialogovém okně **publikovat** :</span><span class="sxs-lookup"><span data-stu-id="5fc91-147">In the **Publish** dialog:</span></span>

* <span data-ttu-id="5fc91-148">Vyberte **Azure App Service (Linux)**.</span><span class="sxs-lookup"><span data-stu-id="5fc91-148">Select **Azure App Service (Linux)**.</span></span>
* <span data-ttu-id="5fc91-149">Vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="5fc91-149">Select **Next**.</span></span>

![Dialog publikovat: výběr služby Azure](publish-to-azure-webapp-using-vs/_static/maas2.png)

<span data-ttu-id="5fc91-151">V dialogovém okně **publikovat** vyberte **vytvořit novou Azure App Service...**</span><span class="sxs-lookup"><span data-stu-id="5fc91-151">In the **Publish** dialog select **Create a new Azure App Service...**</span></span>

![Dialog publikovat: výběr instance služby Azure](publish-to-azure-webapp-using-vs/_static/maas3.png)

<span data-ttu-id="5fc91-153">Zobrazí se dialogové okno **vytvořit App Service** :</span><span class="sxs-lookup"><span data-stu-id="5fc91-153">The **Create App Service** dialog appears:</span></span>

* <span data-ttu-id="5fc91-154">Vyplní se pole **název aplikace**, **Skupina prostředků**a položka **plánu App Service** .</span><span class="sxs-lookup"><span data-stu-id="5fc91-154">The **App Name**, **Resource Group**, and **App Service Plan** entry fields are populated.</span></span> <span data-ttu-id="5fc91-155">Tyto názvy můžete zachovat nebo je změnit.</span><span class="sxs-lookup"><span data-stu-id="5fc91-155">You can keep these names or change them.</span></span>
* <span data-ttu-id="5fc91-156">Vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="5fc91-156">Select **Create**.</span></span>

![Dialogové okno Vytvoření služby App Service](publish-to-azure-webapp-using-vs/_static/newrg1.png)

<span data-ttu-id="5fc91-158">Po vytvoření se dialogové okno automaticky zavře a dialog **publikovat** se znovu stane aktivním:</span><span class="sxs-lookup"><span data-stu-id="5fc91-158">After creation is completed the dialog is automatically closed and the **Publish** dialog gets focus again:</span></span>

* <span data-ttu-id="5fc91-159">Automaticky se vybrala nová instance, kterou jste právě vytvořili.</span><span class="sxs-lookup"><span data-stu-id="5fc91-159">The new instance that was just created is automatically selected.</span></span>
* <span data-ttu-id="5fc91-160">Vyberte **Dokončit**.</span><span class="sxs-lookup"><span data-stu-id="5fc91-160">Select **Finish**.</span></span>

![Dialog publikovat: Vyberte instanci App Service.](publish-to-azure-webapp-using-vs/_static/select_as.png)

<span data-ttu-id="5fc91-162">V dalším kroku se zobrazí stránka **Souhrn profilu publikování** .</span><span class="sxs-lookup"><span data-stu-id="5fc91-162">Next you see the **Publish Profile summary** page.</span></span> <span data-ttu-id="5fc91-163">Visual Studio zjistilo, že tato aplikace vyžaduje databázi SQL Server, a žádá vás, abyste ji nakonfigurovali.</span><span class="sxs-lookup"><span data-stu-id="5fc91-163">Visual Studio has detected that this application requires a SQL Server database and it's asking you to configure it.</span></span> <span data-ttu-id="5fc91-164">Vyberte **Konfigurovat**.</span><span class="sxs-lookup"><span data-stu-id="5fc91-164">Select **Configure**.</span></span>

![Stránka souhrnu profilu publikování: Konfigurace závislosti SQL Server](publish-to-azure-webapp-using-vs/_static/sql.png)

<span data-ttu-id="5fc91-166">Zobrazí se dialogové okno **Konfigurace závislosti** :</span><span class="sxs-lookup"><span data-stu-id="5fc91-166">The **Configure dependency** dialog appears:</span></span>

* <span data-ttu-id="5fc91-167">Vyberte **Azure SQL Database**.</span><span class="sxs-lookup"><span data-stu-id="5fc91-167">Select **Azure SQL Database**.</span></span>
* <span data-ttu-id="5fc91-168">Vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="5fc91-168">Select **Next**.</span></span>

![Dialogové okno Konfigurace SQL Server závislosti](publish-to-azure-webapp-using-vs/_static/sql1.png)

<span data-ttu-id="5fc91-170">V dialogovém okně **Konfigurace služby Azure SQL Database** vyberte **vytvořit SQL Database**</span><span class="sxs-lookup"><span data-stu-id="5fc91-170">In the **Configure Azure SQL database** dialog select **Create a SQL Database**</span></span>

![Dialogové okno konfigurace Azure SQL Database](publish-to-azure-webapp-using-vs/_static/sql2.png)

<span data-ttu-id="5fc91-172">Zobrazí se **Azure SQL Database pro vytvoření** :</span><span class="sxs-lookup"><span data-stu-id="5fc91-172">The **Create Azure SQL Database** appears:</span></span>

* <span data-ttu-id="5fc91-173">Vyplní se pole **název databáze**, **Skupina prostředků**, **databázový server** a **plán App Serviceho** záznamu.</span><span class="sxs-lookup"><span data-stu-id="5fc91-173">The **Database name**, **Resource Group**, **Database server** and **App Service Plan** entry fields are populated.</span></span> <span data-ttu-id="5fc91-174">Tyto hodnoty můžete ponechat nebo změnit.</span><span class="sxs-lookup"><span data-stu-id="5fc91-174">You can keep these values or change them.</span></span>
* <span data-ttu-id="5fc91-175">Zadejte **uživatelské jméno správce databáze** a **heslo správce databáze** pro vybraný **databázový server** (Všimněte si, že účet, který použijete, musí mít potřebná oprávnění k vytvoření nové databáze SQL Azure).</span><span class="sxs-lookup"><span data-stu-id="5fc91-175">Enter the **Database administrator username** and **Database administrator password** for the selected **Database server** (note the account you use must have the necessary permissions to create the new Azure SQL database)</span></span>
* <span data-ttu-id="5fc91-176">Vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="5fc91-176">Select **Create**.</span></span>

![Dialog Nový Azure SQL Database](publish-to-azure-webapp-using-vs/_static/sql_create.png)

<span data-ttu-id="5fc91-178">Po vytvoření se dialogové okno automaticky zavře a dialog **konfigurace Azure SQL Database** se znovu stane fokusem:</span><span class="sxs-lookup"><span data-stu-id="5fc91-178">After creation is completed the dialog is automatically closed and the **Configure Azure SQL Database** dialog gets focus again:</span></span>

* <span data-ttu-id="5fc91-179">Automaticky se vybrala nová instance, kterou jste právě vytvořili.</span><span class="sxs-lookup"><span data-stu-id="5fc91-179">The new instance that was just created is automatically selected.</span></span>
* <span data-ttu-id="5fc91-180">Vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="5fc91-180">Select **Next**.</span></span>

![Dialogové okno konfigurace Azure SQL Database](publish-to-azure-webapp-using-vs/_static/sql_select.png)

<span data-ttu-id="5fc91-182">V dalším kroku dialogového okna **konfigurovat Azure SQL Database** :</span><span class="sxs-lookup"><span data-stu-id="5fc91-182">In the next step of the **Configure Azure SQL Database** dialog:</span></span>

* <span data-ttu-id="5fc91-183">Zadejte pole pro **uživatelské jméno připojení databáze** a **heslo připojení databáze** .</span><span class="sxs-lookup"><span data-stu-id="5fc91-183">Enter the **Database connection user name** and **Database connection password** fields.</span></span> <span data-ttu-id="5fc91-184">Toto jsou podrobnosti, které aplikace použije pro připojení k databázi za běhu.</span><span class="sxs-lookup"><span data-stu-id="5fc91-184">These are the details your application will use to connect to the database at runtime.</span></span> <span data-ttu-id="5fc91-185">Osvědčeným postupem je nepoužívejte stejné podrobnosti jako uživatelské jméno správce & heslo použité v předchozím kroku.</span><span class="sxs-lookup"><span data-stu-id="5fc91-185">Best practice is to avoid using the same details as the admin username & password used in the previous step.</span></span>
* <span data-ttu-id="5fc91-186">Vyberte **Dokončit**.</span><span class="sxs-lookup"><span data-stu-id="5fc91-186">Select **Finish**.</span></span>

![Dialogové okno konfigurace Azure SQL Database, podrobnosti o připojovacím řetězci](publish-to-azure-webapp-using-vs/_static/sql_connection.png)

<span data-ttu-id="5fc91-188">Na stránce **Souhrn publikačního profilu** vyberte **Nastavení**:</span><span class="sxs-lookup"><span data-stu-id="5fc91-188">In the **Publish Profile summary** page select **Settings**:</span></span>

![Stránka souhrnu publikačního profilu: upravit nastavení](publish-to-azure-webapp-using-vs/_static/pp_configured.png)

<span data-ttu-id="5fc91-190">Na stránce **Nastavení** dialogového okna **publikovat** :</span><span class="sxs-lookup"><span data-stu-id="5fc91-190">On the **Settings** page of the **Publish** dialog:</span></span>

* <span data-ttu-id="5fc91-191">Rozbalte položku **databáze** a potom **pomocí tohoto připojovacího řetězce za běhu**.</span><span class="sxs-lookup"><span data-stu-id="5fc91-191">Expand **Databases** and check **Use this connection string at runtime**.</span></span>
* <span data-ttu-id="5fc91-192">Rozbalte **Entity Framework migrace** a **při publikování zaškrtnout možnost použít tuto migraci**.</span><span class="sxs-lookup"><span data-stu-id="5fc91-192">Expand **Entity Framework Migrations** and check **Apply this migration on publish**.</span></span>

* <span data-ttu-id="5fc91-193">Vyberte **Uložit**.</span><span class="sxs-lookup"><span data-stu-id="5fc91-193">Select **Save**.</span></span> <span data-ttu-id="5fc91-194">Visual Studio se vrátí do dialogového okna **publikovat** .</span><span class="sxs-lookup"><span data-stu-id="5fc91-194">Visual Studio returns to the **Publish** dialog.</span></span> 

![Dialog publikovat: panel nastavení](publish-to-azure-webapp-using-vs/_static/pp_settings.png)

<span data-ttu-id="5fc91-196">Klikněte na **Publikovat**.</span><span class="sxs-lookup"><span data-stu-id="5fc91-196">Click **Publish**.</span></span> <span data-ttu-id="5fc91-197">Visual Studio publikuje vaši aplikaci do Azure.</span><span class="sxs-lookup"><span data-stu-id="5fc91-197">Visual Studio publishes your app to Azure.</span></span> <span data-ttu-id="5fc91-198">Po dokončení nasazení se aplikace otevře v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="5fc91-198">When the deployment completes, the app is opened in a browser.</span></span>

![Dialog publikovat: panel nastavení](publish-to-azure-webapp-using-vs/_static/pp_publish.png)

### <a name="update-the-app"></a><span data-ttu-id="5fc91-200">Aktualizace aplikace</span><span class="sxs-lookup"><span data-stu-id="5fc91-200">Update the app</span></span>

* <span data-ttu-id="5fc91-201">Upravte stránku *pages/index. cshtml* Razor a změňte její obsah.</span><span class="sxs-lookup"><span data-stu-id="5fc91-201">Edit the *Pages/Index.cshtml* Razor page and change its contents.</span></span> <span data-ttu-id="5fc91-202">Například můžete změnit odstavec a vyslovit "Hello ASP.NET Core!":</span><span class="sxs-lookup"><span data-stu-id="5fc91-202">For example, you can modify the paragraph to say "Hello ASP.NET Core!":</span></span>

    [!code-html[Index](publish-to-azure-webapp-using-vs/sample/index.cshtml?highlight=10&range=1-12)]

* <span data-ttu-id="5fc91-203">Znovu vyberte **publikovat** na stránce se **souhrnem profilu publikování** .</span><span class="sxs-lookup"><span data-stu-id="5fc91-203">Select **Publish** from the **Publish Profile summary** page again.</span></span>

![Stránka souhrnu profilu publikování](publish-to-azure-webapp-using-vs/_static/pp_publish.png)

* <span data-ttu-id="5fc91-205">Po publikování aplikace ověřte, že provedené změny jsou k dispozici v Azure.</span><span class="sxs-lookup"><span data-stu-id="5fc91-205">After the app is published, verify the changes you made are available on Azure.</span></span>

![Ověření dokončení úlohy](publish-to-azure-webapp-using-vs/_static/final.png)

### <a name="clean-up"></a><span data-ttu-id="5fc91-207">Vyčištění</span><span class="sxs-lookup"><span data-stu-id="5fc91-207">Clean up</span></span>

<span data-ttu-id="5fc91-208">Po dokončení testování aplikace přejdete na [Azure Portal](https://portal.azure.com/) a aplikaci odstraníte.</span><span class="sxs-lookup"><span data-stu-id="5fc91-208">When you have finished testing the app, go to the [Azure portal](https://portal.azure.com/) and delete the app.</span></span>

* <span data-ttu-id="5fc91-209">Vyberte **skupiny prostředků**a pak vyberte skupinu prostředků, kterou jste vytvořili.</span><span class="sxs-lookup"><span data-stu-id="5fc91-209">Select **Resource groups**, then select the resource group you created.</span></span>

![Azure Portal: skupiny prostředků v nabídce bočního panelu](publish-to-azure-webapp-using-vs/_static/portalrg.png)

* <span data-ttu-id="5fc91-211">Na stránce **skupiny prostředků** vyberte **Odstranit**.</span><span class="sxs-lookup"><span data-stu-id="5fc91-211">In the **Resource groups** page, select **Delete**.</span></span>

![Azure Portal: stránka skupin prostředků](publish-to-azure-webapp-using-vs/_static/rgd.png)

* <span data-ttu-id="5fc91-213">Zadejte název skupiny prostředků a vyberte **Odstranit**.</span><span class="sxs-lookup"><span data-stu-id="5fc91-213">Enter the name of the resource group and select **Delete**.</span></span> <span data-ttu-id="5fc91-214">Vaše aplikace a všechny ostatní prostředky vytvořené v tomto kurzu se teď z Azure odstraní.</span><span class="sxs-lookup"><span data-stu-id="5fc91-214">Your app and all other resources created in this tutorial are now deleted from Azure.</span></span>

### <a name="next-steps"></a><span data-ttu-id="5fc91-215">Další kroky</span><span class="sxs-lookup"><span data-stu-id="5fc91-215">Next steps</span></span>

* <xref:host-and-deploy/azure-apps/azure-continuous-deployment>

## <a name="additional-resources"></a><span data-ttu-id="5fc91-216">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="5fc91-216">Additional resources</span></span>

* <span data-ttu-id="5fc91-217">Visual Studio Code najdete v tématu [publikování profilů](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles).</span><span class="sxs-lookup"><span data-stu-id="5fc91-217">For Visual Studio Code, see [Publish profiles](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles).</span></span>
* [<span data-ttu-id="5fc91-218">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="5fc91-218">Azure App Service</span></span>](/azure/app-service/app-service-web-overview)
* [<span data-ttu-id="5fc91-219">Skupiny prostředků Azure</span><span class="sxs-lookup"><span data-stu-id="5fc91-219">Azure resource groups</span></span>](/azure/azure-resource-manager/resource-group-overview#resource-groups)
* [<span data-ttu-id="5fc91-220">Azure SQL Database</span><span class="sxs-lookup"><span data-stu-id="5fc91-220">Azure SQL Database</span></span>](/azure/sql-database/)
* <xref:host-and-deploy/visual-studio-publish-profiles>
* <xref:test/troubleshoot-azure-iis>
