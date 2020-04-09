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
# <a name="publish-an-aspnet-core-app-to-azure-with-visual-studio"></a>Publikování aplikace ASP.NET Core do Azure pomocí Visual Studia

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)
::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [Azure App Service Preview Notice](../includes/azure-apps-preview-notice.md)]

::: moniker-end


Pokud pracujete na macOS, přečtěte si [informace o publikování webové aplikace do služby Azure App Service pomocí Visual Studia pro Mac.](https://docs.microsoft.com/visualstudio/mac/publish-app-svc?view=vsmac-2019)

Informace o řešení problému s <xref:test/troubleshoot-azure-iis>nasazením služby App Service naleznete v tématu .

## <a name="set-up"></a>Nastavit

* Pokud ho nemáte, otevřete si [bezplatný účet Azure.](https://azure.microsoft.com/free/dotnet/) 

## <a name="create-a-web-app"></a>Vytvoření webové aplikace

Na úvodní stránce Visual Studia vyberte **Soubor > Nový > project...**

![Nabídka Soubor](publish-to-azure-webapp-using-vs/_static/file_new_project.png)

Dokončete dialogové okno **Nový projekt:**

* V levém podokně vyberte **položku .NET Core**.
* V prostředním podokně vyberte **ASP.NET Základní webová aplikace**.
* Vyberte **OK**.

![Dialogové okno Nový projekt](publish-to-azure-webapp-using-vs/_static/new_prj.png)

V dialogovém **okně Nová ASP.NET základní webová aplikace:**

* Vyberte **webovou aplikaci**.
* Vyberte **možnost Změnit ověřování**.

![Dialogové okno Nový projekt](publish-to-azure-webapp-using-vs/_static/new_prj_2.png)

Zobrazí se dialogové okno **Změnit ověřování.** 

* Vyberte **jednotlivé uživatelské účty**.
* Výběrem **možnosti OK** se vrátíte do **nové ASP.NET základní webové aplikace**a pak znovu vyberte **OK.**

![Dialogové okno nového ASP.NET základního webového ověřování](publish-to-azure-webapp-using-vs/_static/new_prj_auth.png) 

Visual Studio vytvoří řešení.

## <a name="run-the-app"></a>Spuštění aplikace

* Stisknutím kláves CTRL+F5 spusťte projekt.
* Otestujte odkazy **O a** **Kontakt.**

![Webová aplikace otevřená v Microsoft Edge na localhost](publish-to-azure-webapp-using-vs/_static/show.png)

### <a name="register-a-user"></a>Registrace uživatele

* Vyberte **Registrovat** a zaregistrujte nového uživatele. Můžete použít fiktivní e-mailovou adresu. Při odeslání se na stránce zobrazí následující chyba:

    *"Vnitřní chyba serveru: Při zpracování požadavku se nezdařila operace databáze. Výjimka SQL: Databázi nelze otevřít. Použití existující migrace pro kontext aplikační db může vyřešit tento problém."*
* Vyberte **Použít migrace** a po aktualizaci stránky stránku aktualizujte.

![Vnitřní chyba serveru: Při zpracování požadavku se nezdařila operace databáze. Výjimka SQL: Databázi nelze otevřít. Použití existující migrace pro kontext databáze aplikace může vyřešit tento problém.](publish-to-azure-webapp-using-vs/_static/mig.png)

Aplikace zobrazí e-mail použitý k registraci nového uživatele a odkaz **odhlásit.**

![Webová aplikace otevřena v aplikaci Microsoft Edge. Odkaz Registr je nahrazen textem email@domain.comDobrý den !](publish-to-azure-webapp-using-vs/_static/hello.png)

## <a name="deploy-the-app-to-azure"></a>Nasadit aplikaci do Azure

Klikněte pravým tlačítkem myši na projekt v Průzkumníku řešení a vyberte **publikovat...**.

![Otevřená kontextová nabídka se zvýrazněným odkazem Publikovat](publish-to-azure-webapp-using-vs/_static/pub.png)

V dialogovém okně **Publikovat:**

* Vyberte **službu Microsoft Azure App Service**.
* Vyberte ikonu ozubeného kola a pak vyberte **Vytvořit profil**.
* Vyberte **Vytvořit profil**.

![Dialogové okno Publikovat](publish-to-azure-webapp-using-vs/_static/maas1.png)

### <a name="create-azure-resources"></a>Vytvoření prostředků Azure

Zobrazí se dialogové okno **Vytvořit službu App Service:**

* Zadejte své předplatné.
* Jsou vyplněna pole **Název aplikace**, **Skupina prostředků**a **Plán služby App** Service. Můžete si tyto názvy ponechat nebo je změnit.

![Dialogové okno Služba Aplikace](publish-to-azure-webapp-using-vs/_static/newrg1.png)

* Chcete-li vytvořit novou databázi, vyberte kartu **Služby.**

* Výběrzelené **+** ikony pro vytvoření nové databáze SQL

![Nová databáze SQL](publish-to-azure-webapp-using-vs/_static/sql.png)

* **Vdialogovéce Konfigurovat databázi SQL** vyberte **Nový...** a vytvořte novou databázi.

![Nová databáze sql a server](publish-to-azure-webapp-using-vs/_static/conf.png)

Zobrazí se dialogové okno **Konfigurovat sql server.**

* Zadejte uživatelské jméno a heslo správce a vyberte **ok**. Můžete zachovat výchozí **název serveru**. 

> [!NOTE]
> "admin" není povoleno jako uživatelské jméno správce.

![Dialogové okno Konfigurovat server SQL Server](publish-to-azure-webapp-using-vs/_static/conf_servername.png)

* Vyberte **OK**.

Visual Studio se vrátí do dialogového okna **Vytvořit službu aplikace.**

* V dialogovém okně **Vytvořit službu aplikace** vyberte **Vytvořit.**

![Dialogové okno Konfigurovat databázi SQL](publish-to-azure-webapp-using-vs/_static/conf_final.png)

Visual Studio vytvoří webovou aplikaci a SQL Server v Azure. Tento krok může trvat několik minut. Informace o vytvořených zdrojích naleznete v tématu [Další zdroje](#additional-resources).

Po dokončení nasazení vyberte **Nastavení**:

![Dialogové okno Konfigurovat server SQL Server](publish-to-azure-webapp-using-vs/_static/set.png)

Na stránce **Nastavení** dialogového okna **Publikovat:**

* Rozbalte **položku Databáze** a **zaškrtněte políčko Použít tento připojovací řetězec za běhu**.
* Rozbalte **migrace entity framework u** publikování . **Apply this migration on publish**

* Vyberte **Uložit**. Visual Studio se vrátí do dialogového okna **Publikovat.** 

![Dialogové okno Publikovat: Panel Nastavení](publish-to-azure-webapp-using-vs/_static/pubs.png)

Klikněte na **Publikovat**. Visual Studio publikuje vaši aplikaci do Azure. Po dokončení nasazení se aplikace otevře v prohlížeči.

### <a name="test-your-app-in-azure"></a>Testování aplikace v Azure

* Testování odkazů **O o** **kontaktech** a Kontakt

* Registrace nového uživatele

![Webová aplikace otevřená v Microsoft Edge ve službě Azure App Service](publish-to-azure-webapp-using-vs/_static/register.png)

### <a name="update-the-app"></a>Aktualizace aplikace

* Upravte stránku *Pages/About.cshtml* Razor a změňte její obsah. Můžete například upravit odstavec tak, aby vyslovoval "Hello ASP.NET Core!":

    [!code-html[About](publish-to-azure-webapp-using-vs/sample/about.cshtml?highlight=9&range=1-9)]

* Klikněte pravým tlačítkem myši na projekt a vyberte **publikovat...** znovu.

![Otevřená kontextová nabídka se zvýrazněným odkazem Publikovat](publish-to-azure-webapp-using-vs/_static/pub.png)

* Po publikování aplikace ověřte, zda jsou provedené změny dostupné v Azure.

![Ověření dokončení úkolu](publish-to-azure-webapp-using-vs/_static/final.png)

### <a name="clean-up"></a>Vyčištění

Až dokončíte testování aplikace, přejděte na [portál Azure](https://portal.azure.com/) a aplikaci odstraňte.

* Vyberte **skupiny prostředků**a vyberte skupinu prostředků, kterou jste vytvořili.

![Portál Azure: Skupiny prostředků v postranním panelu](publish-to-azure-webapp-using-vs/_static/portalrg.png)

* Na stránce **Skupiny prostředků** vyberte **Odstranit**.

![Azure Portal: Stránka Skupiny prostředků](publish-to-azure-webapp-using-vs/_static/rgd.png)

* Zadejte název skupiny prostředků a vyberte **Odstranit**. Vaše aplikace a všechny ostatní prostředky vytvořené v tomto kurzu se teď odstraní z Azure.

### <a name="next-steps"></a>Další kroky

* <xref:host-and-deploy/azure-apps/azure-continuous-deployment>

## <a name="additional-resources"></a>Další zdroje

* Kód sady Visual Studio najdete [v tématu Publikování profilů](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles).
* [Azure App Service](/azure/app-service/app-service-web-overview)
* [Skupiny prostředků Azure](/azure/azure-resource-manager/resource-group-overview#resource-groups)
* [Azure SQL Database](/azure/sql-database/)
* <xref:host-and-deploy/visual-studio-publish-profiles>
* <xref:test/troubleshoot-azure-iis>
