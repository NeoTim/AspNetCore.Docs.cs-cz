---
title: Publikování aplikace ASP.NET Core do Azure pomocí sady Visual Studio
author: rick-anderson
description: Zjistěte, jak publikovat aplikace ASP.NET Core do služby Azure App Service pomocí sady Visual Studio.
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
uid: tutorials/publish-to-azure-webapp-using-vs
ms.openlocfilehash: a5a02112d87563a47d5e2dab355359fa39da9a89
ms.sourcegitcommit: c4572be5ebb301013a5698caf9b5572b76cb2e34
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2018
ms.locfileid: "50090352"
---
# <a name="publish-an-aspnet-core-app-to-azure-with-visual-studio"></a>Publikování aplikace ASP.NET Core do Azure pomocí sady Visual Studio

Podle [Rick Anderson](https://twitter.com/RickAndMSFT), [Cesarovi Blum Silveira](https://github.com/cesarbs), a [Rachel Appel](https://twitter.com/rachelappel)

[!INCLUDE [Azure App Service Preview Notice](../includes/azure-apps-preview-notice.md)]

Zobrazit [publikovat do Azure ze sady Visual Studio pro Mac](https://blog.xamarin.com/publish-azure-visual-studio-mac/) Pokud pracujete v systému macOS.

Poradce při potížích chybu nasazení služby App Service, najdete v článku <xref:host-and-deploy/azure-apps/troubleshoot>.

## <a name="set-up"></a>Nastavení

* Otevřít [bezplatný účet Azure](https://azure.microsoft.com/free/dotnet/) pokud ho nemáte. 

## <a name="create-a-web-app"></a>Vytvoření webové aplikace

V aplikaci Visual Studio úvodní stránky, vyberte **soubor > Nový > projekt...**

![nabídka Soubor](publish-to-azure-webapp-using-vs/_static/file_new_project.png)

Dokončení **nový projekt** dialogové okno:

* V levém podokně vyberte **.NET Core**.
* V prostředním podokně vyberte **webové aplikace ASP.NET Core**.
* Vyberte **OK**.

![Dialogové okno nového projektu](publish-to-azure-webapp-using-vs/_static/new_prj.png)

V **nová webová aplikace ASP.NET Core** dialogové okno:

* Vyberte **webovou aplikaci**.
* Vyberte **změnit ověřování**.

![Dialogové okno nového projektu](publish-to-azure-webapp-using-vs/_static/new_prj_2.png)

**Změna ověřování** se zobrazí dialogové okno. 

* Vyberte **jednotlivých uživatelských účtů**.
* Vyberte **OK** se vrátíte **nová webová aplikace ASP.NET Core**a pak vyberte **OK** znovu.

![Nové dialogové okno ověřování webové aplikace ASP.NET Core](publish-to-azure-webapp-using-vs/_static/new_prj_auth.png) 

Visual Studio vytvoří řešení.

## <a name="run-the-app"></a>Spuštění aplikace

* Stiskněte kombinaci kláves CTRL + F5 ke spuštění projektu.
* Test **o** a **kontakt** odkazy.

![Otevření webové aplikace v Microsoft Edge na místního hostitele](publish-to-azure-webapp-using-vs/_static/show.png)

### <a name="register-a-user"></a>Registrace uživatele

* Vyberte **zaregistrovat** a registraci nového uživatele. Můžete použít fiktivní e-mailovou adresu. Když odešlete, na stránce se zobrazí následující chyba:

    *"Vnitřní chyba serveru: Databázová operace selhala při zpracování požadavku. Výjimka SQL: databázi nelze otevřít. Použití existující migrace pro kontext databáze aplikace může tento problém vyřešit."*
* Vyberte **použít migrace** a jakmile se aktualizace stránky, aktualizujte stránku.

![Vnitřní chyba serveru: Databázová operace selhala při zpracování požadavku. Výjimka SQL: databázi nelze otevřít. Použití existující migrace pro kontext databáze aplikace může tento problém vyřešit.](publish-to-azure-webapp-using-vs/_static/mig.png)

Aplikace zobrazí e-mail použitý k registraci nového uživatele a **Odhlásit** odkaz.

![Webová aplikace otevřít v Microsoft Edge. Odkaz registrovat se nahrazuje text Hello email@domain.com!](publish-to-azure-webapp-using-vs/_static/hello.png)

## <a name="deploy-the-app-to-azure"></a>Nasazení aplikace do Azure

Klikněte pravým tlačítkem na projekt v Průzkumníku řešení a vyberte **publikování...** .

![Kontextová nabídka otevřít se zvýrazněným odkazem publikovat](publish-to-azure-webapp-using-vs/_static/pub.png)

V **publikovat** dialogové okno:

* Vyberte **služby Microsoft Azure App Service**.
* Vyberte ikonu ozubeného kola a pak vyberte **vytvořit profil**.
* Vyberte **vytvořit profil**.

![Dialogové okno pro publikování](publish-to-azure-webapp-using-vs/_static/maas1.png)

### <a name="create-azure-resources"></a>Vytvoření prostředků Azure

**Vytvořit službu App Service** se zobrazí dialogové okno:

* Zadejte vaše předplatné.
* **Název aplikace**, **skupiny prostředků**, a **plán služby App Service** zaplnění vstupní pole. Můžete ponechat tyto názvy nebo je změnit.

![Dialogové okno služby App Service](publish-to-azure-webapp-using-vs/_static/newrg1.png)

* Vyberte **služby** kartu a vytvořte novou databázi.

* Vyberte zelené **+** ikonu pro vytvoření nové databáze SQL

![Nová databáze SQL](publish-to-azure-webapp-using-vs/_static/sql.png)

* Vyberte **nové...**  na **konfigurace služby SQL Database** dialogové okno pro vytvoření nové databáze.

![Nová databáze SQL a server](publish-to-azure-webapp-using-vs/_static/conf.png)

**Nakonfigurujte systém SQL Server** se zobrazí dialogové okno.

* Zadejte uživatelské jméno správce a heslo a pak vyberte **OK**. Můžete ponechat výchozí **název serveru**. 

> [!NOTE]
> "admin" není povolen jako uživatelské jméno správce.

![Konfigurace systému SQL Server dialogového okna](publish-to-azure-webapp-using-vs/_static/conf_servername.png)

* Vyberte **OK**.

Vrátí sady Visual Studio **vytvořit službu App Service** dialogového okna.

* Vyberte **vytvořit** na **vytvořit službu App Service** dialogového okna.

![Konfigurace SQL Database dialogového okna](publish-to-azure-webapp-using-vs/_static/conf_final.png)

Visual Studio vytvoří webovou aplikaci a SQL Server v Azure. Tento krok může trvat několik minut. Informace o prostředky vytvořené v tématu [další prostředky](#additonal-resources).

Po dokončení nasazení vyberte **nastavení**:

![Konfigurace systému SQL Server dialogového okna](publish-to-azure-webapp-using-vs/_static/set.png)

Na **nastavení** stránku **publikovat** dialogové okno:

  * Rozbalte **databází** a zkontrolujte **použít tento připojovací řetězec za běhu**.
  * Rozbalte **migrace rozhraní Entity Framework** a zkontrolujte **použít tuto migraci na publikování**.

* Vyberte **Uložit**. Vrátí sady Visual Studio **publikovat** dialogového okna. 

![Dialogové okno pro publikování: panel nastavení](publish-to-azure-webapp-using-vs/_static/pubs.png)

Klikněte na tlačítko **publikovat**. Visual Studio publikuje vaši aplikaci do Azure. Po dokončení nasazení aplikace se otevře v prohlížeči.

### <a name="test-your-app-in-azure"></a>Testování aplikace v Azure

* Test **o** a **kontakt** odkazy

* Registrace nového uživatele

![Otevřít v Microsoft Edge ve službě Azure App Service webovou aplikaci](publish-to-azure-webapp-using-vs/_static/register.png)

### <a name="update-the-app"></a>Aktualizace aplikace

* Upravit *Pages/About.cshtml* Razor stránce a změňte jeho obsah. Například můžete upravit odstavec říct "Dobrý den ASP.NET Core!": [!code-html[About](publish-to-azure-webapp-using-vs/sample/about.cshtml?highlight=9&range=1-9)]

* Klikněte pravým tlačítkem na projekt a vyberte **publikování...**  znovu.

![Kontextová nabídka otevřít se zvýrazněným odkazem publikovat](publish-to-azure-webapp-using-vs/_static/pub.png)

* Po publikování aplikace, ověřte, zda jsou k dispozici v Azure, provedené změny.

![Ověřte, zda je úkol dokončený](publish-to-azure-webapp-using-vs/_static/final.png)

### <a name="clean-up"></a>Vyčistit

Po dokončení testování aplikace, přejděte [webu Azure portal](https://portal.azure.com/) a odstranit ji.

* Vyberte **skupiny prostředků**, pak vyberte skupinu prostředků, kterou jste vytvořili.

![Azure Portal: Skupiny prostředků v nabídce bočního panelu](publish-to-azure-webapp-using-vs/_static/portalrg.png)

* V **skupiny prostředků** stránce **odstranit**.

![Azure Portal: Stránce skupiny prostředků](publish-to-azure-webapp-using-vs/_static/rgd.png)

* Zadejte název skupiny prostředků a vyberte **odstranit**. Vaše aplikace a všechny další prostředky vytvořené v tomto kurzu se teď odstraní z Azure.

### <a name="next-steps"></a>Další kroky

* <xref:host-and-deploy/azure-apps/azure-continuous-deployment>

## <a name="additonal-resources"></a>Další prostředky

* [Azure App Service](/azure/app-service/app-service-web-overview)
* [Skupiny prostředků Azure](/azure/azure-resource-manager/resource-group-overview#resource-groups)
* [Azure SQL Database](/azure/sql-database/)
* <xref:host-and-deploy/azure-apps/troubleshoot>
