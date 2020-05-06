---
title: Průběžné nasazování do Azure pomocí sady Visual Studio a Gitu s ASP.NET Core
author: rick-anderson
description: Naučte se, jak vytvořit webovou aplikaci ASP.NET Core pomocí sady Visual Studio a nasadit ji do Azure App Service pomocí Gitu pro průběžné nasazování.
ms.author: riande
ms.custom: mvc
ms.date: 12/06/2018
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/azure-apps/azure-continuous-deployment
ms.openlocfilehash: 3a865c2c42cb71e109331675460456a27dc500fc
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775307"
---
# <a name="continuous-deployment-to-azure-with-visual-studio-and-git-with-aspnet-core"></a>Průběžné nasazování do Azure pomocí sady Visual Studio a Gitu s ASP.NET Core

Od [Erik Reitan](https://github.com/Erikre)

[!INCLUDE [Azure App Service Preview Notice](../../includes/azure-apps-preview-notice.md)]

V tomto kurzu se dozvíte, jak vytvořit webovou aplikaci ASP.NET Core pomocí sady Visual Studio a nasadit ji ze sady Visual Studio na Azure App Service pomocí průběžného nasazování.

Viz také [Vytvoření prvního kanálu pomocí Azure Pipelines](/azure/devops/pipelines/get-started-yaml), který ukazuje, jak nakonfigurovat pracovní postup pro průběžné doručování (CD) pro [Azure App Service](/azure/app-service/app-service-web-overview) pomocí Azure DevOps Services. Azure Pipelines (služba Azure DevOps Services) zjednodušuje nastavení robustního kanálu nasazení pro publikování aktualizací pro aplikace hostované v Azure App Service. Kanál se dá nakonfigurovat z Azure Portal k sestavení, spuštění testů, nasazování do přípravného slotu a následnému nasazení do produkčního prostředí.

> [!NOTE]
> K dokončení tohoto kurzu se vyžaduje účet Microsoft Azure. Pokud chcete získat účet, [Aktivujte si výhody pro předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A261C142F) nebo [si zaregistrujte bezplatnou zkušební verzi](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Požadavky

V tomto kurzu se předpokládá, že je nainstalovaný následující software:

* [Visual Studio](https://visualstudio.microsoft.com)
* [!INCLUDE [](~/includes/net-core-sdk-download-link.md)]
* [Git](https://git-scm.com/downloads) pro Windows

## <a name="create-an-aspnet-core-web-app"></a>Vytvoření webové aplikace ASP.NET Core

1. Spusťte Visual Studio.

1. V nabídce **soubor** vyberte možnost **Nový** > **projekt**.

1. Vyberte šablonu projektu **ASP.NET Core webové aplikace** . Zobrazí se v části **nainstalované** > **šablony** > **Visual C#** > **.NET Core**. Pojmenujte `SampleWebAppDemo`projekt. Vyberte možnost **vytvořit novou Git úložiště** a klikněte na **OK**.

   ![Dialogové okno Nový projekt](azure-continuous-deployment/_static/01-new-project.png)

1. V dialogovém okně **Nový projekt ASP.NET Core** vyberte ASP.NET Core **prázdnou** šablonu a pak klikněte na **OK**.

   ![Dialog nového projektu ASP.NET Core](azure-continuous-deployment/_static/02-web-site-template.png)

> [!NOTE]
> Nejnovější verze .NET Core je 2,0.

### <a name="running-the-web-app-locally"></a>Místní spuštění webové aplikace

1. Jakmile Visual Studio dokončí vytváření aplikace **, spusťte aplikaci výběrem** > ladění**Spustit ladění**. Jako alternativu stiskněte klávesu **F5**.

   Inicializace sady Visual Studio a nové aplikace může chvíli trvat. Po dokončení se v prohlížeči zobrazí spuštěná aplikace.

   ![Okno prohlížeče zobrazující běžící aplikaci, která zobrazuje Hello World!](azure-continuous-deployment/_static/04-browser-runapp.png)

1. Po kontrole běžící webové aplikace zavřete prohlížeč a na panelu nástrojů sady Visual Studio vyberte ikonu Zastavit ladění a zastavte aplikaci.

## <a name="create-a-web-app-in-the-azure-portal"></a>Vytvoření webové aplikace na webu Azure Portal

Pomocí následujících kroků vytvoříte webovou aplikaci na webu Azure Portal:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

1. Vyberte **Nový** v levém horním rohu rozhraní portálu.

1. Vyberte **web a mobilní zařízení** > **webové aplikace**.

   ![Microsoft Azure Portal: nové tlačítko: Web a mobilní zařízení na webu Marketplace: tlačítko webové aplikace v oblasti vybrané aplikace](azure-continuous-deployment/_static/05-azure-newwebapp.png)

1. V okně **Webová aplikace** zadejte jedinečnou hodnotu **názvu App Service**.

   ![Okno webové aplikace](azure-continuous-deployment/_static/06-azure-newappblade.png)

   > [!NOTE]
   > Název **App Service** musí být jedinečný. Portál toto pravidlo vynutil, pokud je zadán název. Pokud zadáte jinou hodnotu, nahraďte tuto hodnotu pro každý výskyt **SampleWebAppDemo** v tomto kurzu.

   V okně **Webová aplikace** vyberte existující **App Service plán/umístění** nebo vytvořte nový. Pokud vytváříte nový plán, vyberte cenovou úroveň, umístění a další možnosti. Další informace o plánech App Service najdete [v podrobných informacích o Azure App Servicech plánech](/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview).

1. Vyberte **Vytvořit**. Azure zřídí a spustí webovou aplikaci.

   ![Portál Azure Portal: Ukázka webové aplikace ukázka 01 základní okno](azure-continuous-deployment/_static/07-azure-webappblade.png)

## <a name="enable-git-publishing-for-the-new-web-app"></a>Povolit publikování Gitu pro novou webovou aplikaci

Git je distribuovaný systém správy verzí, který se dá použít k nasazení Azure App Service webové aplikace. Kód webové aplikace je uložený v místním úložišti Git a kód se nasadí do Azure tím, že se uloží do vzdáleného úložiště.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

1. Výběrem **App Services** zobrazíte seznam aplikačních služeb přidružených k předplatnému Azure.

1. Vyberte webovou aplikaci vytvořenou v předchozí části tohoto kurzu.

1. V okně **nasazení** vyberte **Možnosti** > nasazení**zvolit zdrojové** > **místní úložiště Git**.

   ![Okno nastavení: okno zdroje nasazení: zvolit zdrojový okno](azure-continuous-deployment/_static/deployment-options.png)

1. Vyberte **OK**.

1. Pokud se přihlašovací údaje nasazení pro publikování webové aplikace nebo jiné aplikace App Service ještě neudělaly, nastavte je teď:

   * Vyberte **Nastavení** > **přihlašovací údaje pro nasazení**. Zobrazí se okno **nastavit přihlašovací údaje nasazení** .
   * Vytvořte uživatelské jméno a heslo. Uložte heslo pro pozdější použití při nastavování Gitu.
   * Vyberte **Uložit**.

1. V okně **Webová aplikace** vyberte **Nastavení** > **vlastnosti**. Adresa URL vzdáleného úložiště Git, na který se má nasadit, se zobrazí v části **Adresa URL Gitu**.

1. Zkopírujte hodnotu **adresy URL Gitu** pro pozdější použití v tomto kurzu.

   ![Portál Azure Portal: okno vlastností aplikace](azure-continuous-deployment/_static/09-azure-giturl.png)

## <a name="publish-the-web-app-to-azure-app-service"></a>Publikování webové aplikace do služby Azure App Service

V této části vytvoříte místní úložiště Git pomocí sady Visual Studio a nahrajete ho z tohoto úložiště do Azure a nasadíte webovou aplikaci. Mezi zahrnuté kroky patří následující:

* Přidejte nastavení vzdáleného úložiště pomocí hodnoty adresy URL GITU, aby bylo možné místní úložiště nasadit do Azure.
* Potvrďte změny projektu.
* Vložení změn projektu z místního úložiště do vzdáleného úložiště v Azure

1. V **Průzkumník řešení** klikněte pravým tlačítkem myši na **řešení SampleWebAppDemo** a vyberte **Potvrdit**. Zobrazí se **Team Explorer**.

   ![Karta Team Explorer připojit](azure-continuous-deployment/_static/10-team-explorer.png)

1. V **Team Explorer**vyberte **Domů** (ikona domů **) >** > nastavení**úložiště**.

1. V části **Vzdálená úložiště** vyberte v **Nastavení úložiště****Přidat**. Zobrazí se dialogové okno **Přidat vzdálené úložiště**.

1. Nastavte **název** vzdáleného na **Azure-dotazů**.

1. Nastavte hodnotu pro **načíst** na **adresu URL Gitu** zkopírovanou z Azure dřív v tomto kurzu. Všimněte si, že toto je adresa URL, která končí na **. Git**.

   ![Upravit vzdálené dialogové okno](azure-continuous-deployment/_static/11-add-remote.png)

   > [!NOTE]
   > Jako alternativu určete vzdálené úložiště z **příkazového okna** otevřením **okna příkazového**řádku, přepnutím do adresáře projektu a zadáním příkazu. Příklad:
   >
   > `git remote add Azure-SampleApp https://me@sampleapp.scm.azurewebsites.net:443/SampleApp.git`

1. Vyberte **Domů** (ikona domů) > **Nastavení** > **globální nastavení**. Zkontrolujte, jestli je zadané jméno a e-mailová adresa. V případě potřeby vyberte **aktualizovat** .

1. Vyberte možnost **domovské** > **změny** a vraťte se do zobrazení **změny** .

1. Zadejte potvrzovací zprávu, například **počáteční #1 nabízených oznámení** , a vyberte **Potvrdit**. Tato akce vytvoří místní *potvrzení* .

   ![Karta Team Explorer připojit](azure-continuous-deployment/_static/12-initial-commit.png)

   > [!NOTE]
   > Jako alternativu potvrďte změny z **příkazového okna** tak, že otevřete **okno příkazového**řádku, změníte adresář projektu a zadáte příkazy Gitu. Příklad:
   >
   > `git add .`
   >
   > `git commit -am "Initial Push #1"`

1. Vyberte > **Akce**pro **domovskou** > **synchronizaci** > **otevřít příkazový řádek**. Příkazový řádek se otevře v adresáři projektu.

1. V příkazovém okně zadejte následující příkaz:

   `git push -u Azure-SampleApp master`

1. Zadejte heslo k **přihlašovacím údajům nasazení** Azure, které jste vytvořili dříve v Azure.

   Tento příkaz spustí proces vložení místních souborů projektu do Azure. Výstup z výše uvedeného příkazu končí zprávou, že nasazení proběhlo úspěšně.

   ```
   remote: Finished successfully.
   remote: Running post deployment command(s)...
   remote: Deployment successful.
   To https://username@samplewebappdemo01.scm.azurewebsites.net:443/SampleWebAppDemo01.git
   * [new branch]      master -> master
   Branch master set up to track remote branch master from Azure-SampleApp.
   ```

   > [!NOTE]
   > Pokud je potřeba spolupráce na projektu, zvažte, že před nahráním do Azure budete moct přecházet do [GitHubu](https://github.com) .
 
### <a name="verify-the-active-deployment"></a>Ověřit aktivní nasazení

Ověřte, jestli je webová aplikace přenesená z místního prostředí do Azure úspěšná.

Na webu [Azure Portal](https://portal.azure.com)vyberte webovou aplikaci. Vyberte **Deployment** > **Možnosti nasazení**nasazení.

![Azure Portal: okno nastavení: okno nasazení znázorňující úspěšné nasazení](azure-continuous-deployment/_static/13-verify-deployment.png)

## <a name="run-the-app-in-azure"></a>Spuštění aplikace v Azure

Teď, když je webová aplikace nasazená do Azure, spusťte aplikaci.

Toho lze dosáhnout dvěma způsoby:

* Na webu Azure Portal vyhledejte okno webová aplikace pro webovou aplikaci. Vyberte **Procházet** a zobrazte aplikaci ve výchozím prohlížeči.
* Otevřete prohlížeč a zadejte adresu URL webové aplikace. Příklad: `http://SampleWebAppDemo.azurewebsites.net`

## <a name="update-the-web-app-and-republish"></a>Aktualizace webové aplikace a opětovné publikování

Po provedení změn v místním kódu znovu publikujte:

1. V **Průzkumník řešení** sady Visual Studio otevřete soubor *Startup.cs* .

1. V `Configure` metodě upravte `Response.WriteAsync` metodu tak, aby se zobrazila takto:

   ```csharp
   await context.Response.WriteAsync("Hello World! Deploy to Azure.");
   ```

1. Uložte změny do *Startup.cs*.

1. V **Průzkumník řešení**klikněte pravým tlačítkem na **řešení "SampleWebAppDemo"** a vyberte **Potvrdit**. Zobrazí se **Team Explorer**.

1. Zadejte potvrzovací zprávu, například `Update #2`.

1. Kliknutím na tlačítko **Potvrdit** potvrďte změny projektu.

1. Vyberte akce pro **domovskou** > **synchronizaci** > **Akce** > **push**.

> [!NOTE]
> Alternativně můžete vložit změny z **příkazového okna** otevřením **okna příkazového**řádku, přepnutím do adresáře projektu a zadáním příkazu git. Příklad:
> 
> `git push -u Azure-SampleApp master`

## <a name="view-the-updated-web-app-in-azure"></a>Zobrazení aktualizované webové aplikace v Azure

Kliknutím na **Procházet** v okně webové aplikace na webu Azure Portal nebo otevřením prohlížeče a zadáním adresy URL webové aplikace Zobrazte aktualizovanou webovou aplikaci. Příklad: `http://SampleWebAppDemo.azurewebsites.net`

## <a name="additional-resources"></a>Další zdroje

* [Vytvoření prvního kanálu pomocí Azure Pipelines](/azure/devops/pipelines/get-started-yaml)
* [Projekt Kudu](https://github.com/projectkudu/kudu/wiki)
* <xref:host-and-deploy/visual-studio-publish-profiles>
