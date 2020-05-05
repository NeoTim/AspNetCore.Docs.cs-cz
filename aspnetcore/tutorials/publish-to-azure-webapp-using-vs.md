---
title: Publikování aplikace ASP.NET Core do Azure pomocí sady Visual Studio
author: rick-anderson
description: Naučte se publikovat aplikaci ASP.NET Core pro Azure App Service pomocí sady Visual Studio.
ms.author: riande
ms.custom: mvc
ms.date: 07/10/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/publish-to-azure-webapp-using-vs
ms.openlocfilehash: 634fb821ef0478dbcf57fdbb991a2e8bbb9402f1
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82777082"
---
# <a name="publish-an-aspnet-core-app-to-azure-with-visual-studio"></a>Publikování aplikace ASP.NET Core do Azure pomocí sady Visual Studio

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)
::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [Azure App Service Preview Notice](../includes/azure-apps-preview-notice.md)]

::: moniker-end


Pokud pracujete na macOS, přečtěte si téma [publikování webové aplikace pro Azure App Service používání Visual Studio pro Mac](https://docs.microsoft.com/visualstudio/mac/publish-app-svc?view=vsmac-2019) .

Problémy s nasazením App Service najdete v tématu <xref:test/troubleshoot-azure-iis>.

## <a name="set-up"></a>Nastavení

* Pokud ho nemáte, otevřete [bezplatný účet Azure](https://azure.microsoft.com/free/dotnet/) . 

## <a name="create-a-web-app"></a>Vytvoření webové aplikace

Na úvodní stránce sady Visual Studio vyberte **soubor > nový > projekt...**

![Nabídka Soubor](publish-to-azure-webapp-using-vs/_static/file_new_project.png)

Dokončete dialog **nového projektu** :

* V levém podokně vyberte **.NET Core**.
* V prostředním podokně vyberte **ASP.NET Core webová aplikace**.
* Vyberte **OK**.

![Dialogové okno Nový projekt](publish-to-azure-webapp-using-vs/_static/new_prj.png)

V dialogovém okně **nový ASP.NET Core webové aplikace** :

* Vyberte **Webová aplikace**.
* Vyberte **změnit ověřování**.

![Dialogové okno Nový projekt](publish-to-azure-webapp-using-vs/_static/new_prj_2.png)

Zobrazí se dialogové okno **změnit ověřování** . 

* Vyberte **jednotlivé uživatelské účty**.
* Kliknutím na **tlačítko OK** se vraťte do **nové ASP.NET Core webové aplikace**a pak znovu vyberte **OK** .

![Dialog nové ASP.NET Core webového ověřování](publish-to-azure-webapp-using-vs/_static/new_prj_auth.png) 

Visual Studio vytvoří řešení.

## <a name="run-the-app"></a>Spuštění aplikace

* Stisknutím kombinace kláves CTRL + F5 spusťte projekt.
* Otestujte odkazy **na adresu** a **kontakt** .

![Webová aplikace otevřená v Microsoft Edge na místním hostiteli](publish-to-azure-webapp-using-vs/_static/show.png)

### <a name="register-a-user"></a>Registrace uživatele

* Vyberte **Registrovat** a registrovat nového uživatele. Můžete použít fiktivní e-mailovou adresu. Při odeslání se na stránce zobrazí následující chyba:

    *"Došlo k vnitřní chybě serveru: databázová operace se při zpracování požadavku nezdařila. Výjimka SQL: databázi nelze otevřít. Tento problém může vyřešit použití existujících migrací pro kontext aplikační databáze. "*
* Vyberte možnost **použít migrace** a po aktualizaci stránky aktualizujte stránku.

![Došlo k vnitřní chybě serveru: databázová operace se při zpracování požadavku nezdařila. Výjimka SQL: databázi nelze otevřít. Tento problém může vyřešit použití existujících migrací pro kontext aplikační databáze.](publish-to-azure-webapp-using-vs/_static/mig.png)

Aplikace zobrazí e-mail, který se používá k registraci nového uživatele a odkaz na **odhlášení** .

![Webová aplikace je otevřená v Microsoft Edge. Odkaz na registraci je nahrazen textem Hello email@domain.com!](publish-to-azure-webapp-using-vs/_static/hello.png)

## <a name="deploy-the-app-to-azure"></a>Nasadit aplikaci do Azure

Klikněte pravým tlačítkem na projekt v Průzkumník řešení a vyberte **publikovat...**.

![Kontextová nabídka otevřená se zvýrazněným odkazem pro publikování](publish-to-azure-webapp-using-vs/_static/pub.png)

V dialogovém okně **publikovat** :

* Vyberte **Microsoft Azure App Service**.
* Vyberte ikonu ozubeného kolečka a pak vyberte **vytvořit profil**.
* Vyberte **Vytvořit profil**.

![Dialog publikovat](publish-to-azure-webapp-using-vs/_static/maas1.png)

### <a name="create-azure-resources"></a>Vytvoření prostředků Azure

Zobrazí se dialogové okno **vytvořit App Service** :

* Zadejte své předplatné.
* Vyplní se pole **název aplikace**, **Skupina prostředků**a položka **plánu App Service** . Tyto názvy můžete zachovat nebo je změnit.

![Dialog App Service](publish-to-azure-webapp-using-vs/_static/newrg1.png)

* Vyberte kartu **služby** a vytvořte novou databázi.

* Vyberte zelenou **+** ikonu pro vytvoření nového SQL Database

![Nová databáze SQL](publish-to-azure-webapp-using-vs/_static/sql.png)

* Vyberte **Nový...** v dialogovém okně **Konfigurace SQL Database** vytvořte novou databázi.

![Nové SQL Database a Server](publish-to-azure-webapp-using-vs/_static/conf.png)

Zobrazí se dialogové okno **konfigurace SQL Server** .

* Zadejte uživatelské jméno a heslo správce a pak vyberte **OK**. Můžete ponechat výchozí **název serveru**. 

> [!NOTE]
> správce není povolený jako uživatelské jméno správce.

![Dialogové okno Konfigurace SQL Server](publish-to-azure-webapp-using-vs/_static/conf_servername.png)

* Vyberte **OK**.

Visual Studio se vrátí do dialogového okna **vytvořit App Service** .

* V dialogovém okně **vytvořit App Service** vyberte **vytvořit** .

![Dialogové okno Konfigurace SQL Database](publish-to-azure-webapp-using-vs/_static/conf_final.png)

Visual Studio vytvoří webovou aplikaci a SQL Server v Azure. Tento krok může trvat několik minut. Informace o vytvořených prostředcích najdete v tématu [Další zdroje](#additional-resources)informací.

Po dokončení nasazení vyberte **Nastavení**:

![Dialogové okno Konfigurace SQL Server](publish-to-azure-webapp-using-vs/_static/set.png)

Na stránce **Nastavení** dialogového okna **publikovat** :

* Rozbalte položku **databáze** a potom **pomocí tohoto připojovacího řetězce za běhu**.
* Rozbalte **Entity Framework migrace** a **při publikování zaškrtnout možnost použít tuto migraci**.

* Vyberte **Uložit**. Visual Studio se vrátí do dialogového okna **publikovat** . 

![Dialog publikovat: panel nastavení](publish-to-azure-webapp-using-vs/_static/pubs.png)

Klikněte na **Publikovat**. Visual Studio publikuje vaši aplikaci do Azure. Po dokončení nasazení se aplikace otevře v prohlížeči.

### <a name="test-your-app-in-azure"></a>Testování aplikace v Azure

* Testování odkazů **About** a **kontakt**

* Registrace nového uživatele

![Webová aplikace otevřená v Microsoft Edge na Azure App Service](publish-to-azure-webapp-using-vs/_static/register.png)

### <a name="update-the-app"></a>Aktualizace aplikace

* Upravte stránku *stránky/About. cshtml* Razor a změňte její obsah. Například můžete změnit odstavec a vyslovit "Hello ASP.NET Core!":

    [!code-html[About](publish-to-azure-webapp-using-vs/sample/about.cshtml?highlight=9&range=1-9)]

* Klikněte pravým tlačítkem na projekt a vyberte **publikovat...** znovu.

![Kontextová nabídka otevřená se zvýrazněným odkazem pro publikování](publish-to-azure-webapp-using-vs/_static/pub.png)

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

## <a name="additional-resources"></a>Další materiály a zdroje informací

* Visual Studio Code najdete v tématu [publikování profilů](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles).
* [Azure App Service](/azure/app-service/app-service-web-overview)
* [Skupiny prostředků Azure](/azure/azure-resource-manager/resource-group-overview#resource-groups)
* [Azure SQL Database](/azure/sql-database/)
* <xref:host-and-deploy/visual-studio-publish-profiles>
* <xref:test/troubleshoot-azure-iis>
