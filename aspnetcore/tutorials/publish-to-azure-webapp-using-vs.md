---
title: Publikování aplikace ASP.NET Core do Azure pomocí sady Visual Studio
author: rick-anderson
description: Naučte se publikovat aplikaci ASP.NET Core pro Azure App Service pomocí sady Visual Studio.
ms.author: riande
ms.custom: devx-track-csharp, mvc
ms.date: 07/10/2019
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
uid: tutorials/publish-to-azure-webapp-using-vs
ms.openlocfilehash: 1fced12700fcd5910c1484ebb9190c7652b2646e
ms.sourcegitcommit: ba4872dd5a93780fe6cfacb2711ec1e69e0df92c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/12/2020
ms.locfileid: "88130701"
---
# <a name="publish-an-aspnet-core-app-to-azure-with-visual-studio"></a>Publikování aplikace ASP.NET Core do Azure pomocí sady Visual Studio

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)
::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [Azure App Service Preview Notice](../includes/azure-apps-preview-notice.md)]

::: moniker-end


Pokud pracujete na macOS, přečtěte si téma [publikování webové aplikace pro Azure App Service používání Visual Studio pro Mac](https://docs.microsoft.com/visualstudio/mac/publish-app-svc?view=vsmac-2019) .

Problémy s nasazením App Service najdete v tématu <xref:test/troubleshoot-azure-iis> .

## <a name="set-up"></a>Nastavení

* Pokud ho nemáte, otevřete [bezplatný účet Azure](https://azure.microsoft.com/free/dotnet/) . 

## <a name="create-a-web-app"></a>Vytvoření webové aplikace

Na úvodní stránce sady Visual Studio vyberte **soubor > nový > projekt...**

![Nabídka Soubor](publish-to-azure-webapp-using-vs/_static/file_new_project.png)

Dokončete dialog **nového projektu** :

* Vyberte **ASP.NET Core webové aplikace**.
* Vyberte **Další**.

![Dialogové okno Nový projekt](publish-to-azure-webapp-using-vs/_static/new_prj.png)

V dialogovém okně **nový ASP.NET Core webové aplikace** :

* Vyberte **Webová aplikace**.
* V části ověřování vyberte **změnit** .

![Dialogové okno Nový ASP.NET Core Web](publish-to-azure-webapp-using-vs/_static/new_prj_2.png)

Zobrazí se dialogové okno **změnit ověřování** . 

* Vyberte **jednotlivé uživatelské účty**.
* Kliknutím na **tlačítko OK** se vraťte do **nové ASP.NET Core webové aplikace**a pak vyberte **vytvořit**.

![Dialog nové ASP.NET Core webového ověřování](publish-to-azure-webapp-using-vs/_static/new_prj_auth.png) 

Visual Studio vytvoří řešení.

## <a name="run-the-app"></a>Spuštění aplikace

* Stisknutím kombinace kláves CTRL + F5 spusťte projekt.
* Otestujte odkaz na **ochranu osobních údajů** .

![Webová aplikace otevřená v Microsoft Edge na místním hostiteli](publish-to-azure-webapp-using-vs/_static/show.png)

### <a name="register-a-user"></a>Registrace uživatele

* Vyberte **Registrovat** a registrovat nového uživatele. Můžete použít fiktivní e-mailovou adresu. Při odeslání se na stránce zobrazí následující chyba:

    *Při zpracování požadavku se databázová operace nezdařila. Tento problém může vyřešit použití existujících migrací pro kontext aplikační databáze. "*
* Vyberte možnost **použít migrace** a po aktualizaci stránky aktualizujte stránku.

![Databázová operace se při zpracování požadavku nezdařila. Tento problém může vyřešit použití existujících migrací pro kontext aplikační databáze.](publish-to-azure-webapp-using-vs/_static/mig.png)

Aplikace zobrazí e-mail, který se používá k registraci nového uživatele a odkaz na **odhlášení** .

![Webová aplikace je otevřená v Microsoft Edge. Odkaz na registraci je nahrazen textem Hello user1@example.com !](publish-to-azure-webapp-using-vs/_static/hello.png)

## <a name="deploy-the-app-to-azure"></a>Nasadit aplikaci do Azure

Klikněte pravým tlačítkem na projekt v Průzkumník řešení a vyberte **publikovat...**.

![Kontextová nabídka otevřená se zvýrazněným odkazem pro publikování](publish-to-azure-webapp-using-vs/_static/pub.png)

V dialogovém okně **publikovat** :

* Vyberte **Azure**.
* Vyberte **Další**.

![Dialog publikovat](publish-to-azure-webapp-using-vs/_static/maas1.png)

V dialogovém okně **publikovat** :

* Vyberte **Azure App Service (Linux)**.
* Vyberte **Další**.

![Dialog publikovat: výběr služby Azure](publish-to-azure-webapp-using-vs/_static/maas2.png)

V dialogovém okně **publikovat** vyberte **vytvořit novou Azure App Service...**

![Dialog publikovat: výběr instance služby Azure](publish-to-azure-webapp-using-vs/_static/maas3.png)

Zobrazí se dialogové okno **vytvořit App Service** :

* Vyplní se pole **název aplikace**, **Skupina prostředků**a položka **plánu App Service** . Tyto názvy můžete zachovat nebo je změnit.
* Vyberte **Vytvořit**.

![Dialogové okno Vytvoření služby App Service](publish-to-azure-webapp-using-vs/_static/newrg1.png)

Po vytvoření se dialogové okno automaticky zavře a dialog **publikovat** se znovu stane aktivním:

* Automaticky se vybrala nová instance, kterou jste právě vytvořili.
* Vyberte **Dokončit**.

![Dialog publikovat: Vyberte instanci App Service.](publish-to-azure-webapp-using-vs/_static/select_as.png)

V dalším kroku se zobrazí stránka **Souhrn profilu publikování** . Visual Studio zjistilo, že tato aplikace vyžaduje databázi SQL Server, a žádá vás, abyste ji nakonfigurovali. Vyberte **Konfigurovat**.

![Stránka souhrnu profilu publikování: Konfigurace závislosti SQL Server](publish-to-azure-webapp-using-vs/_static/sql.png)

Zobrazí se dialogové okno **Konfigurace závislosti** :

* Vyberte **Azure SQL Database**.
* Vyberte **Další**.

![Dialogové okno Konfigurace SQL Server závislosti](publish-to-azure-webapp-using-vs/_static/sql1.png)

V dialogovém okně **Konfigurace služby Azure SQL Database** vyberte **vytvořit SQL Database**

![Dialogové okno konfigurace Azure SQL Database](publish-to-azure-webapp-using-vs/_static/sql2.png)

Zobrazí se **Azure SQL Database pro vytvoření** :

* Vyplní se pole **název databáze**, **Skupina prostředků**, **databázový server** a **plán App Serviceho** záznamu. Tyto hodnoty můžete ponechat nebo změnit.
* Zadejte **uživatelské jméno správce databáze** a **heslo správce databáze** pro vybraný **databázový server** (Všimněte si, že účet, který použijete, musí mít potřebná oprávnění k vytvoření nové databáze SQL Azure).
* Vyberte **Vytvořit**.

![Dialog Nový Azure SQL Database](publish-to-azure-webapp-using-vs/_static/sql_create.png)

Po vytvoření se dialogové okno automaticky zavře a dialog **konfigurace Azure SQL Database** se znovu stane fokusem:

* Automaticky se vybrala nová instance, kterou jste právě vytvořili.
* Vyberte **Další**.

![Dialogové okno konfigurace Azure SQL Database](publish-to-azure-webapp-using-vs/_static/sql_select.png)

V dalším kroku dialogového okna **konfigurovat Azure SQL Database** :

* Zadejte pole pro **uživatelské jméno připojení databáze** a **heslo připojení databáze** . Toto jsou podrobnosti, které aplikace použije pro připojení k databázi za běhu. Osvědčeným postupem je nepoužívejte stejné podrobnosti jako uživatelské jméno správce & heslo použité v předchozím kroku.
* Vyberte **Dokončit**.

![Dialogové okno konfigurace Azure SQL Database, podrobnosti o připojovacím řetězci](publish-to-azure-webapp-using-vs/_static/sql_connection.png)

Na stránce **Souhrn publikačního profilu** vyberte **Nastavení**:

![Stránka souhrnu publikačního profilu: upravit nastavení](publish-to-azure-webapp-using-vs/_static/pp_configured.png)

Na stránce **Nastavení** dialogového okna **publikovat** :

* Rozbalte položku **databáze** a potom **pomocí tohoto připojovacího řetězce za běhu**.
* Rozbalte **Entity Framework migrace** a **při publikování zaškrtnout možnost použít tuto migraci**.

* Vyberte **Uložit**. Visual Studio se vrátí do dialogového okna **publikovat** . 

![Dialog publikovat: panel nastavení](publish-to-azure-webapp-using-vs/_static/pp_settings.png)

Klikněte na **Publikovat**. Visual Studio publikuje vaši aplikaci do Azure. Po dokončení nasazení se aplikace otevře v prohlížeči.

![Dialog publikovat: panel nastavení](publish-to-azure-webapp-using-vs/_static/pp_publish.png)

### <a name="update-the-app"></a>Aktualizace aplikace

* Upravte stránku *pages/index. cshtml* Razor a změňte její obsah. Například můžete změnit odstavec a vyslovit "Hello ASP.NET Core!":

    [!code-html[Index](publish-to-azure-webapp-using-vs/sample/index.cshtml?highlight=10&range=1-12)]

* Znovu vyberte **publikovat** na stránce se **souhrnem profilu publikování** .

![Stránka souhrnu profilu publikování](publish-to-azure-webapp-using-vs/_static/pp_publish.png)

* Po publikování aplikace ověřte, že provedené změny jsou k dispozici v Azure.

![Ověření dokončení úlohy](publish-to-azure-webapp-using-vs/_static/final.png)

### <a name="clean-up"></a>Vyčištění

Po dokončení testování aplikace přejdete na [Azure Portal](https://portal.azure.com/) a aplikaci odstraníte.

* Vyberte **skupiny prostředků**a pak vyberte skupinu prostředků, kterou jste vytvořili.

![Azure Portal: skupiny prostředků v nabídce bočního panelu](publish-to-azure-webapp-using-vs/_static/portalrg.png)

* Na stránce **skupiny prostředků** vyberte **Odstranit**.

![Azure Portal: stránka skupin prostředků](publish-to-azure-webapp-using-vs/_static/rgd.png)

* Zadejte název skupiny prostředků a vyberte **Odstranit**. Vaše aplikace a všechny ostatní prostředky vytvořené v tomto kurzu se teď z Azure odstraní.

### <a name="next-steps"></a>Další kroky

* <xref:host-and-deploy/azure-apps/azure-continuous-deployment>

## <a name="additional-resources"></a>Další zdroje

* Visual Studio Code najdete v tématu [publikování profilů](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles).
* [Azure App Service](/azure/app-service/app-service-web-overview)
* [Skupiny prostředků Azure](/azure/azure-resource-manager/resource-group-overview#resource-groups)
* [Azure SQL Database](/azure/sql-database/)
* <xref:host-and-deploy/visual-studio-publish-profiles>
* <xref:test/troubleshoot-azure-iis>
