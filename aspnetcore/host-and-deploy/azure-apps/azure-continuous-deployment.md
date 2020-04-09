---
title: Průběžné nasazování do Azure pomocí sady Visual Studio a Gitu s ASP.NET Core
author: rick-anderson
description: Zjistěte, jak pomocí Sady Visual Studio vytvořit ASP.NET webovou aplikaci Core a nasadit ji do služby Azure App Service pomocí Gitu pro průběžné nasazení.
ms.author: riande
ms.custom: mvc
ms.date: 12/06/2018
uid: host-and-deploy/azure-apps/azure-continuous-deployment
ms.openlocfilehash: 3b344505739bb4292ed1683c73ff314b6e4e01e9
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78660851"
---
# <a name="continuous-deployment-to-azure-with-visual-studio-and-git-with-aspnet-core"></a>Průběžné nasazování do Azure pomocí sady Visual Studio a Gitu s ASP.NET Core

Podle [Erik Reitan](https://github.com/Erikre)

[!INCLUDE [Azure App Service Preview Notice](../../includes/azure-apps-preview-notice.md)]

Tento kurz ukazuje, jak vytvořit ASP.NET webovou aplikaci Core pomocí Visual Studia a nasadit ji z Visual Studia do služby Azure App Service pomocí průběžného nasazení.

Viz taky [Vytvoření prvního kanálu pomocí Azure Pipelines](/azure/devops/pipelines/get-started-yaml), který ukazuje, jak nakonfigurovat pracovní postup průběžného doručování (CD) pro [službu Azure App Service](/azure/app-service/app-service-web-overview) pomocí služby Azure DevOps. Azure Pipelines (služba Azure DevOps Services) zjednodušuje nastavení kanálu robustního nasazení pro publikování aktualizací pro aplikace hostované ve službě Azure App Service. Kanál lze nakonfigurovat z portálu Azure k sestavení, spuštění testů, nasazení do pracovního slotu a následnénasazení do produkčního prostředí.

> [!NOTE]
> K dokončení tohoto kurzu je vyžadován účet Microsoft Azure. Chcete-li získat účet, [aktivujte výhody pro předplatitele msdn](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A261C142F) nebo [se zaregistrujte k bezplatné zkušební verzi](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Požadavky

Tento kurz předpokládá, že je nainstalován následující software:

* [Visual Studio](https://visualstudio.microsoft.com)
* [!INCLUDE [](~/includes/net-core-sdk-download-link.md)]
* [Git](https://git-scm.com/downloads) pro Windows

## <a name="create-an-aspnet-core-web-app"></a>Vytvoření webové aplikace ASP.NET Core

1. Spusťte Visual Studio.

1. V nabídce **Soubor** vyberte **Nový** > **projekt**.

1. Vyberte šablonu projektu **základní webové aplikace ASP.NET.** Zobrazí se v části **Nainstalované** > **šablony** > **Visual C#** > **.NET Core**. Pojmenujte `SampleWebAppDemo`projekt . Vyberte možnost **Vytvořit nové úložiště Git** a klepněte na **OK**.

   ![Dialogové okno Nový projekt](azure-continuous-deployment/_static/01-new-project.png)

1. V dialogovém **okně Nový ASP.NET základní projekt** vyberte šablonu ASP.NET Jádro **prázdné** a klepněte na tlačítko **OK**.

   ![Dialogové okno Nový ASP.NET základní projekt](azure-continuous-deployment/_static/02-web-site-template.png)

> [!NOTE]
> Nejnovější verze rozhraní .NET Core je 2.0.

### <a name="running-the-web-app-locally"></a>Místní spuštění webové aplikace

1. Po dokončení vytvoření aplikace visual studio, spusťte aplikaci výběrem **ladění začít** > **ladění**. Alternativně stiskněte **klávesu F5**.

   Inicializaci sady Visual Studio a nové aplikace může nějakou dobu trvat. Po dokončení prohlížeč zobrazí spuštěnou aplikaci.

   ![Okno prohlížeče zobrazující spuštěnou aplikaci, která zobrazuje "Hello World!".](azure-continuous-deployment/_static/04-browser-runapp.png)

1. Po kontrole spuštěné webové aplikace zavřete prohlížeč a vyberte ikonu Zastavit ladění na panelu nástrojů sady Visual Studio, chcete-li aplikaci zastavit.

## <a name="create-a-web-app-in-the-azure-portal"></a>Vytvoření webové aplikace na Webu Azure Portal

Následující kroky vytvoří webovou aplikaci na webu Azure Portal:

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com).

1. V levém horním rohu rozhraní portálu vyberte **NOVÝ.**

1. Vyberte **možnost Web + Mobilní** > **webová aplikace**.

   ![Portál Microsoft Azure: Nové tlačítko: Web + Mobilní pod marketplace: Tlačítko Web App v části Doporučené aplikace](azure-continuous-deployment/_static/05-azure-newwebapp.png)

1. V okně **Web App** zadejte jedinečnou hodnotu pro **název služby App Service**.

   ![Okno webové aplikace](azure-continuous-deployment/_static/06-azure-newappblade.png)

   > [!NOTE]
   > Název **služby App Service** musí být jedinečný. Portál vynucuje toto pravidlo, když je uveden název. Pokud poskytuje jinou hodnotu, nahradit tuto hodnotu pro každý výskyt **SampleWebAppDemo** v tomto kurzu.

   Také v okně **Web App** vyberte existující **plán/umístění služby App Service** nebo vytvořte nový. Pokud vytváříte nový plán, vyberte cenovou úroveň, umístění a další možnosti. Další informace o plánech služby App Service najdete v článku [plány azure app service podrobný přehled](/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview).

1. Vyberte **Vytvořit**. Azure zřídí a spustí webovou aplikaci.

   ![Portál Azure: Ukázková ukázková ukázková ukázková ukázka ukázkového okna 01 Essentials](azure-continuous-deployment/_static/07-azure-webappblade.png)

## <a name="enable-git-publishing-for-the-new-web-app"></a>Povolení publikování Gitu pro novou webovou aplikaci

Git je distribuovaný systém správy verzí, který se dá použít k nasazení webové aplikace Azure App Service. Kód webové aplikace se ukládá v místním úložišti Git a kód se nasadí do Azure odesláním do vzdáleného úložiště.

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com).

1. Výběrem **možnosti Služby aplikací** zobrazíte seznam aplikačních služeb přidružených k předplatnému Azure.

1. Vyberte webovou aplikaci vytvořenou v předchozí části tohoto kurzu.

1. V okně **Nasazení** vyberte **možnosti** > nasazení**Zvolit zdrojové** > **místní úložiště Git**.

   ![Nastavení čepele: Okno zdroje nasazení: Zvolte zdrojový nůž](azure-continuous-deployment/_static/deployment-options.png)

1. Vyberte **OK**.

1. Pokud přihlašovací údaje pro nasazení pro publikování webové aplikace nebo jiné aplikace Služby App Service ještě nebyly nastaveny, nastavte je teď:

   * Vyberte **nastavení** > **přihlašovacích údajů pro nasazení**. Zobrazí se okno **Nastavit přihlašovací údaje pro nasazení.**
   * Vytvořte uživatelské jméno a heslo. Heslo si uložte pro pozdější použití při nastavování Gitu.
   * Vyberte **Uložit**.

1. V okně **Web Appu** vyberte **Vlastnosti** > **nastavení**. Adresa URL vzdáleného úložiště Git, do které se má nasadit, se zobrazí pod **adresou GIT URL**.

1. Zkopírujte hodnotu **adresy URL GIT** pro pozdější použití v kurzu.

   ![Portál Azure: okno Vlastnosti aplikace](azure-continuous-deployment/_static/09-azure-giturl.png)

## <a name="publish-the-web-app-to-azure-app-service"></a>Publikování webové aplikace do služby Azure App Service

V této části vytvořte místní úložiště Git pomocí Visual Studia a přemisťujte z tohoto úložiště do Azure k nasazení webové aplikace. K následujícím krokům patří:

* Přidejte nastavení vzdáleného úložiště pomocí hodnoty ADRESY URL GIT, aby bylo možné místní úložiště nasadit do Azure.
* Potvrzení změn projektu.
* Nabízení změn projektu z místního úložiště do vzdáleného úložiště v Azure.

1. V **Průzkumníku řešení** klepněte pravým tlačítkem myši na **položku Řešení SampleWebAppDemo** a vyberte **příkaz Potvrdit**. Zobrazí se **Team Explorer**.

   ![Karta Připojení Průzkumníka týmu](azure-continuous-deployment/_static/10-team-explorer.png)

1. V **Průzkumníkovi týmu**vyberte **domovskou** (domovskou ikonu) >**nastavení úložiště** **nastavení** > .

1. V části **Vzdálená úložiště** vyberte v **Nastavení úložiště****Přidat**. Zobrazí se dialogové okno **Přidat vzdálené úložiště**.

1. Nastavte **název** vzdáleného na **Azure-SampleApp**.

1. Nastavte hodnotu pro **načtení** na **adresu URL Git,** která se zkopírovala z Azure dříve v tomto kurzu. Všimněte si, že se jedná o adresu URL, která končí **.git**.

   ![Dialogové okno Upravit vzdálený](azure-continuous-deployment/_static/11-add-remote.png)

   > [!NOTE]
   > Jako alternativu určete vzdálené úložiště z **příkazového okna** otevřením **příkazového okna**, změnou adresáře projektu a zadáním příkazu. Příklad:
   >
   > `git remote add Azure-SampleApp https://me@sampleapp.scm.azurewebsites.net:443/SampleApp.git`

1. Vyberte globální nastavení nastavení **Settings** >  **>** domovské (domovské)**.** Zkontrolujte, jestli je zadané jméno a e-mailová adresa. V případě potřeby vyberte **Aktualizovat.**

1. Vyberte **Domácí** > **změny,** chcete-li se vrátit do zobrazení **Změny.**

1. Zadejte zprávu o potvrzení, například **Počáteční nabízenou #1,** a vyberte **Potvrdit**. Tato akce vytvoří *potvrzení* místně.

   ![Karta Připojení Průzkumníka týmu](azure-continuous-deployment/_static/12-initial-commit.png)

   > [!NOTE]
   > Alternativně potvrďte změny z **příkazového okna** otevřením **příkazového okna**, změnou adresáře projektu a zadáním příkazů git. Příklad:
   >
   > `git add .`
   >
   > `git commit -am "Initial Push #1"`

1. Vyberte **položku Akce** > **domácí synchronizace** > **Actions** > **Otevřete příkazový řádek**. Příkazový řádek se otevře do adresáře projektu.

1. Do příkazového okna zadejte následující příkaz:

   `git push -u Azure-SampleApp master`

1. Zadejte heslo **přihlašovacích údajů k nasazení** Azure vytvořené dříve v Azure.

   Tento příkaz spustí proces odesílání souborů místního projektu do Azure. Výstup z výše uvedeného příkazu končí zprávou, že nasazení bylo úspěšné.

   ```
   remote: Finished successfully.
   remote: Running post deployment command(s)...
   remote: Deployment successful.
   To https://username@samplewebappdemo01.scm.azurewebsites.net:443/SampleWebAppDemo01.git
   * [new branch]      master -> master
   Branch master set up to track remote branch master from Azure-SampleApp.
   ```

   > [!NOTE]
   > Pokud je nutná spolupráce na projektu, zvažte odeslání na [GitHub](https://github.com) před odesláním do Azure.
 
### <a name="verify-the-active-deployment"></a>Ověření aktivního nasazení

Ověřte, zda je přenos webové aplikace z místního prostředí do Azure úspěšný.

Na [webu Azure Portal](https://portal.azure.com)vyberte webovou aplikaci. Vyberte **možnosti** > **nasazení**.

![Azure Portal: Okno Nastavení: Okno nasazení zobrazující úspěšné nasazení](azure-continuous-deployment/_static/13-verify-deployment.png)

## <a name="run-the-app-in-azure"></a>Spuštění aplikace v Azure

Teď, když se webová aplikace nasadí do Azure, spusťte ji.

Toho lze dosáhnout dvěma způsoby:

* Na webu Azure Portal vyhledejte okno webové aplikace pro webovou aplikaci. Výběrem **možnosti Procházet** zobrazíte aplikaci ve výchozím prohlížeči.
* Otevřete prohlížeč a zadejte adresu URL webové aplikace. Příklad: `http://SampleWebAppDemo.azurewebsites.net`

## <a name="update-the-web-app-and-republish"></a>Aktualizace webové aplikace a opětovné publikování

Po provedení změn v místním kódu znovu publikujte:

1. V **Průzkumníku řešení** sady Visual Studio otevřete *soubor Startup.cs.*

1. V `Configure` metodě upravte metodu `Response.WriteAsync` tak, aby se zjevná takto:

   ```csharp
   await context.Response.WriteAsync("Hello World! Deploy to Azure.");
   ```

1. Uložte změny do *Startup.cs*.

1. V **Průzkumníku řešení**klepněte pravým tlačítkem myši na **položku Řešení SampleWebAppDemo** a vyberte **příkaz Potvrdit**. Zobrazí se **Team Explorer**.

1. Zadejte zprávu o `Update #2`potvrzení, například .

1. Stisknutím tlačítka **Potvrdit** potvrďte změny projektu.

1. Vyberte **položku Akce** > **domácí synchronizace** > **Actions** > **Push**.

> [!NOTE]
> Jako alternativu posunuji změny z **příkazového okna** otevřením **příkazového okna**, změnou adresáře projektu a zadáním příkazu git. Příklad:
> 
> `git push -u Azure-SampleApp master`

## <a name="view-the-updated-web-app-in-azure"></a>Zobrazení aktualizované webové aplikace v Azure

Aktualizovanou webovou aplikaci si můžete zobrazit tak, že **vyberete Procházet** z okna webové aplikace na Webu Azure Portal nebo otevřete prohlížeč a zajděte do adresy URL webové aplikace. Příklad: `http://SampleWebAppDemo.azurewebsites.net`

## <a name="additional-resources"></a>Další zdroje

* [Vytvoření prvního kanálu pomocí Azure Pipelines](/azure/devops/pipelines/get-started-yaml)
* [Projekt Kudu](https://github.com/projectkudu/kudu/wiki)
* <xref:host-and-deploy/visual-studio-publish-profiles>
