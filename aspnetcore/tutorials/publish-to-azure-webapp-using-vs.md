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
# <a name="publish-an-aspnet-core-app-to-azure-with-visual-studio"></a>Publikování aplikace ASP.NET Core do Azure pomocí sady Visual Studio

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)
::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [Azure App Service Preview Notice](../includes/azure-apps-preview-notice.md)]

::: moniker-end


Pokud pracujete na macOS, přečtěte si téma [publikování webové aplikace pro Azure App Service používání Visual Studio pro Mac](https://docs.microsoft.com/visualstudio/mac/publish-app-svc?view=vsmac-2019) .

Řešení potíží s nasazením App Service najdete v tématu <xref:test/troubleshoot-azure-iis>.

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

![Nové dialogové okno ověřování webové aplikace ASP.NET Core](publish-to-azure-webapp-using-vs/_static/new_prj_auth.png) 

Visual Studio vytvoří řešení.

## <a name="run-the-app"></a>Spuštění aplikace

* Stiskněte kombinaci kláves CTRL + F5 ke spuštění projektu.
* Otestujte odkazy **na adresu** a **kontakt** .

![Otevření webové aplikace v Microsoft Edge na místního hostitele](publish-to-azure-webapp-using-vs/_static/show.png)

### <a name="register-a-user"></a>Registrace uživatele

* Vyberte **Registrovat** a registrovat nového uživatele. Můžete použít fiktivní e-mailovou adresu. Když odešlete, na stránce se zobrazí následující chyba:

    *"Došlo k vnitřní chybě serveru: databázová operace se při zpracování požadavku nezdařila. Výjimka SQL: databázi nelze otevřít. Tento problém může vyřešit použití existujících migrací pro kontext aplikační databáze. "*
* Vyberte možnost **použít migrace** a po aktualizaci stránky aktualizujte stránku.

![Vnitřní chyba serveru: Databázová operace selhala při zpracování požadavku. Výjimka SQL: databázi nelze otevřít. Použití existující migrace pro kontext databáze aplikace může tento problém vyřešit.](publish-to-azure-webapp-using-vs/_static/mig.png)

Aplikace zobrazí e-mail, který se používá k registraci nového uživatele a odkaz na **odhlášení** .

![Webová aplikace otevřít v Microsoft Edge. Odkaz na registraci je nahrazen textem Hello email@domain.com!](publish-to-azure-webapp-using-vs/_static/hello.png)

## <a name="deploy-the-app-to-azure"></a>Nasadit aplikaci do Azure

Klikněte pravým tlačítkem na projekt v Průzkumník řešení a vyberte **publikovat...** .

![Kontextová nabídka otevřít se zvýrazněným odkazem publikovat](publish-to-azure-webapp-using-vs/_static/pub.png)

V dialogovém okně **publikovat** :

* Vyberte **Microsoft Azure App Service**.
* Vyberte ikonu ozubeného kolečka a pak vyberte **vytvořit profil**.
* Vyberte **vytvořit profil**.

![Dialogové okno pro publikování](publish-to-azure-webapp-using-vs/_static/maas1.png)

### <a name="create-azure-resources"></a>Vytvoření prostředků Azure

Zobrazí se dialogové okno **vytvořit App Service** :

* Zadejte vaše předplatné.
* Vyplní se pole **název aplikace**, **Skupina prostředků**a položka **plánu App Service** . Můžete ponechat tyto názvy nebo je změnit.

![Dialogové okno služby App Service](publish-to-azure-webapp-using-vs/_static/newrg1.png)

* Vyberte kartu **služby** a vytvořte novou databázi.

* Vyberte zelenou **+** ikonu pro vytvoření nového SQL Database

![Nová databáze SQL](publish-to-azure-webapp-using-vs/_static/sql.png)

* Vyberte **Nový...** v dialogovém okně **Konfigurace SQL Database** vytvořte novou databázi.

![Nová databáze SQL a server](publish-to-azure-webapp-using-vs/_static/conf.png)

Zobrazí se dialogové okno **konfigurace SQL Server** .

* Zadejte uživatelské jméno a heslo správce a pak vyberte **OK**. Můžete ponechat výchozí **název serveru**. 

> [!NOTE]
> "admin" není povolen jako uživatelské jméno správce.

![Konfigurace systému SQL Server dialogového okna](publish-to-azure-webapp-using-vs/_static/conf_servername.png)

* Vyberte **OK**.

Visual Studio se vrátí do dialogového okna **vytvořit App Service** .

* V dialogovém okně **vytvořit App Service** vyberte **vytvořit** .

![Konfigurace SQL Database dialogového okna](publish-to-azure-webapp-using-vs/_static/conf_final.png)

Visual Studio vytvoří webovou aplikaci a SQL Server v Azure. Tento krok může trvat několik minut. Informace o vytvořených prostředcích najdete v tématu [Další zdroje](#additional-resources)informací.

Po dokončení nasazení vyberte **Nastavení**:

![Konfigurace systému SQL Server dialogového okna](publish-to-azure-webapp-using-vs/_static/set.png)

Na stránce **Nastavení** dialogového okna **publikovat** :

* Rozbalte položku **databáze** a potom **pomocí tohoto připojovacího řetězce za běhu**.
* Rozbalte **Entity Framework migrace** a **při publikování zaškrtnout možnost použít tuto migraci**.

* Vyberte **Save** (Uložit). Visual Studio se vrátí do dialogového okna **publikovat** . 

![Dialogové okno pro publikování: panel nastavení](publish-to-azure-webapp-using-vs/_static/pubs.png)

Klikněte na **Publikovat**. Visual Studio publikuje vaši aplikaci do Azure. Po dokončení nasazení aplikace se otevře v prohlížeči.

### <a name="test-your-app-in-azure"></a>Testování aplikace v Azure

* Testování odkazů **About** a **kontakt**

* Registrace nového uživatele

![Otevřít v Microsoft Edge ve službě Azure App Service webovou aplikaci](publish-to-azure-webapp-using-vs/_static/register.png)

### <a name="update-the-app"></a>Aktualizace aplikace

* Upravte stránku *stránky/About. cshtml* Razor a změňte její obsah. Například můžete změnit odstavec a vyslovit "Hello ASP.NET Core!":

    [!code-html[About](publish-to-azure-webapp-using-vs/sample/about.cshtml?highlight=9&range=1-9)]

* Klikněte pravým tlačítkem na projekt a vyberte **publikovat...** znovu.

![Kontextová nabídka otevřít se zvýrazněným odkazem publikovat](publish-to-azure-webapp-using-vs/_static/pub.png)

* Po publikování aplikace, ověřte, zda jsou k dispozici v Azure, provedené změny.

![Ověřte, zda je úkol dokončený](publish-to-azure-webapp-using-vs/_static/final.png)

### <a name="clean-up"></a>Vyčištění

Po dokončení testování aplikace přejdete na [Azure Portal](https://portal.azure.com/) a aplikaci odstraníte.

* Vyberte **skupiny prostředků**a pak vyberte skupinu prostředků, kterou jste vytvořili.

![Azure Portal: Skupiny prostředků v nabídce bočního panelu](publish-to-azure-webapp-using-vs/_static/portalrg.png)

* Na stránce **skupiny prostředků** vyberte **Odstranit**.

![Azure Portal: Stránce skupiny prostředků](publish-to-azure-webapp-using-vs/_static/rgd.png)

* Zadejte název skupiny prostředků a vyberte **Odstranit**. Vaše aplikace a všechny další prostředky vytvořené v tomto kurzu se teď odstraní z Azure.

### <a name="next-steps"></a>Další kroky

* <xref:host-and-deploy/azure-apps/azure-continuous-deployment>

## <a name="additional-resources"></a>Další zdroje

* Visual Studio Code najdete v tématu [publikování profilů](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles).
* [Azure App Service](/azure/app-service/app-service-web-overview)
* [Skupiny prostředků Azure](/azure/azure-resource-manager/resource-group-overview#resource-groups)
* [Azure SQL Database](/azure/sql-database/)
* <xref:host-and-deploy/visual-studio-publish-profiles>
* <xref:test/troubleshoot-azure-iis>
