---
title: Průběžné nasazování do Azure pomocí sady Visual Studio a Gitu s ASP.NET Core
author: rick-anderson
description: Zjistěte, jak vytvořit webovou aplikaci ASP.NET Core pomocí sady Visual Studio a nasaďte ji do služby Azure App Service pro průběžné nasazování pomocí Gitu.
ms.author: riande
ms.custom: mvc
ms.date: 12/06/2018
uid: host-and-deploy/azure-apps/azure-continuous-deployment
ms.openlocfilehash: e12c2ee0b78db105b431770e8644e7d19d915765
ms.sourcegitcommit: b34b25da2ab68e6495b2460ff570468f16a9bf0d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53284421"
---
# <a name="continuous-deployment-to-azure-with-visual-studio-and-git-with-aspnet-core"></a>Průběžné nasazování do Azure pomocí sady Visual Studio a Gitu s ASP.NET Core

podle [Erik Reitan](https://github.com/Erikre)

[!INCLUDE [Azure App Service Preview Notice](../../includes/azure-apps-preview-notice.md)]

Tento kurz ukazuje, jak vytvořit webovou aplikaci ASP.NET Core pomocí sady Visual Studio a nasadit ho z Visual Studio do služby Azure App Service pomocí průběžného nasazování.

Viz také [vytvořit svůj první kanál s kanály Azure](/azure/devops/pipelines/get-started-yaml), který ukazuje postup při konfiguraci pracovního postupu průběžné doručování (CD) pro [služby Azure App Service](/azure/app-service/app-service-web-overview) pomocí služby Azure DevOps. Kanály Azure (služby Azure DevOps služby), zjednodušuje zřízení robustního kanálu nasazení publikovat aktualizace pro aplikace hostované ve službě Azure App Service. Kanál je možné nakonfigurovat z portálu Azure portal k vytvoření, spuštění testů, nasazení do přípravného slotu a pak nasadit do produkčního prostředí.

> [!NOTE]
> K dokončení tohoto kurzu je nutné účet Microsoft Azure. Získat účet služby se [aktivovat výhody pro předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A261C142F) nebo [zaregistrujte si bezplatnou zkušební verzi](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Požadavky

Tento kurz předpokládá, že je nainstalovaný následující software:

* [Visual Studio](https://www.visualstudio.com)
* [!INCLUDE [](~/includes/net-core-sdk-download-link.md)]
* [Git](https://git-scm.com/downloads) pro Windows

## <a name="create-an-aspnet-core-web-app"></a>Vytvoření webové aplikace ASP.NET Core

1. Spusťte Visual Studio.

1. Z **souboru** nabídce vyberte možnost **nový** > **projektu**.

1. Vyberte **webové aplikace ASP.NET Core** šablony projektu. Zobrazí se pod **nainstalováno** > **šablony** > **Visual C#** > **.NET Core**. Pojmenujte projekt `SampleWebAppDemo`. Vyberte **vytvořit nové úložiště Git** možnost a klikněte na tlačítko **OK**.

   ![Dialogové okno nového projektu](azure-continuous-deployment/_static/01-new-project.png)

1. V **nový projekt ASP.NET Core** dialogového okna, vyberte ASP.NET Core **prázdný** šablony, klikněte na **OK**.

   ![Dialogové okno Nový projekt ASP.NET Core](azure-continuous-deployment/_static/02-web-site-template.png)

> [!NOTE]
> Nejnovější verzi sady .NET Core je 2.0.

### <a name="running-the-web-app-locally"></a>Místní spuštění webové aplikace

1. Jakmile sada Visual Studio dokončí vytváření aplikace, spusťte aplikaci tak, že vyberete **ladění** > **spustit ladění**. Jako alternativu, stiskněte klávesu **F5**.

   Může trvat dobu k inicializaci sady Visual Studio a novou aplikaci. Po dokončení zobrazí v prohlížeči spuštěné aplikaci.

   ![Zobrazení okna prohlížeče spuštěnou aplikaci, která se zobrazí "Hello World!"](azure-continuous-deployment/_static/04-browser-runapp.png)

1. Po zkontrolování funkční webovou aplikaci, ukončete prohlížeč a vyberte ikonu "Zastavit ladění" v panelu nástrojů sady Visual Studio a zastavte tak aplikace.

## <a name="create-a-web-app-in-the-azure-portal"></a>Vytvoření webové aplikace na webu Azure Portal

Následující postup vytvoření webové aplikace na webu Azure Portal:

1. Přihlaste se k [webu Azure Portal](https://portal.azure.com).

1. Vyberte **nový** v levém horním rohu portálu rozhraní.

1. Vyberte **Web + mobilní zařízení** > **webová aplikace**.

   ![Portál Microsoft Azure: Tlačítko Nová: Web + mobilní zařízení v části Marketplace: Tlačítko webové aplikace v rámci vybrané aplikace](azure-continuous-deployment/_static/05-azure-newwebapp.png)

1. V **webovou aplikaci** okně zadejte jedinečnou hodnotu **název služby App Service**.

   ![Okně webové aplikace](azure-continuous-deployment/_static/06-azure-newappblade.png)

   > [!NOTE]
   > **Název služby App Service** název musí být jedinečný. Na portálu vynutí toto pravidlo, pokud je zadaný název. Pokud zadáte jiné hodnoty, nahraďte tuto hodnotu pro každý výskyt **SampleWebAppDemo** v tomto kurzu.

   Také v **webovou aplikaci** okno, vyberte existující **plán App Service/umístění** nebo vytvořte novou. Pokud vytváříte nový plán, vyberte cenovou úroveň, umístění a další možnosti. Další informace o plánech služby App Service najdete v tématu [podrobný přehled plánů služby Azure App Service](/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview).

1. Vyberte **Vytvořit**. Azure bude zřídit a spustit webovou aplikaci.

   ![Azure Portal: Okno ukázkové webové aplikace ukázku 01 Essentials](azure-continuous-deployment/_static/07-azure-webappblade.png)

## <a name="enable-git-publishing-for-the-new-web-app"></a>Povolení publikování Git pro novou webovou aplikaci

Git je distribuovaný systém správy verzí, který slouží k nasazení webové aplikace v Azure App Service. Kód webové aplikace je uložena v místním úložišti Git a kód se nasazuje do Azure s doručením (push) do vzdáleného úložiště.

1. Přihlaste se [webu Azure Portal](https://portal.azure.com).

1. Vyberte **App Services** zobrazíte seznam služeb aplikací přidružených k předplatnému Azure.

1. Vyberte webovou aplikaci vytvořenou v předchozí části tohoto kurzu.

1. V **nasazení** okně vyberte **možnosti nasazení** > **zvolit zdroj** > **místní úložiště Git**.

   ![Okno nastavení: Okno zdroje nasazení: Vyberte okno zdroje](azure-continuous-deployment/_static/deployment-options.png)

1. Vyberte **OK**.

1. Pokud přihlašovací údaje nasazení pro publikování webové aplikace nebo jiné aplikace služby App Service ještě dříve nastavený, nastavte je nyní:

   * Vyberte **nastavení** > **přihlašovací údaje pro nasazení**. **Přihlašovací údaje pro nasazení** zobrazí se okno.
   * Vytvořte uživatelské jméno a heslo. Uložte heslo pro pozdější použití. při nastavování Git.
   * Vyberte **Uložit**.

1. V **webovou aplikaci** okně vyberte **nastavení** > **vlastnosti**. Adresa URL vzdáleného úložiště Git pro nasazení do je zobrazen pod **adresa URL pro GIT**.

1. Kopírovat **adresa URL pro GIT** hodnoty pro pozdější použití v tomto kurzu.

   ![Webu Azure Portal: okno Vlastnosti aplikací](azure-continuous-deployment/_static/09-azure-giturl.png)

## <a name="publish-the-web-app-to-azure-app-service"></a>Publikování webové aplikace do služby Azure App Service

V této části se vytvořte místní úložiště Git pomocí sady Visual Studio a nabízených oznámení z tohoto úložiště do Azure k nasazení webové aplikace. Následující kroky:

* Přidáte nastavení vzdáleného úložiště pomocí adresy URL pro GIT hodnoty, tak v místním úložišti je možné nasadit do Azure.
* Potvrďte změny v projektu.
* Odešlete změny projektu z místního úložiště do vzdáleného úložiště v Azure.

1. V **Průzkumníka řešení** klikněte pravým tlačítkem na **řešení "SampleWebAppDemo"** a vyberte **potvrzení**. **Team Exploreru** se zobrazí.

   ![Team Explorer Connect kartu](azure-continuous-deployment/_static/10-team-explorer.png)

1. V **Team Exploreru**, vyberte **domácí** (Ikona Domovská stránka) > **nastavení** > **nastavení úložiště**.

1. V **vzdálených úložišť** část **nastavení úložiště**vyberte **přidat**. **Přidat vzdálené úložiště** se zobrazí dialogové okno.

1. Nastavte **název** vzdáleného k **Azure – ukázková aplikace**.

1. Nastavit hodnotu pro **načíst** k **adresa URL pro Git** , který se zkopíruje z Azure dříve v tomto kurzu. Všimněte si, že se jedná o adresu URL, která končí **.git**.

   ![Upravit vzdálené dialogového okna](azure-continuous-deployment/_static/11-add-remote.png)

   > [!NOTE]
   > Jako alternativu, zadejte ze vzdáleného úložiště **příkazové okno** tak, že otevřete **příkazové okno**, změna k adresáři projektu a zadáním příkazu. Příklad:
   >
   > `git remote add Azure-SampleApp https://me@sampleapp.scm.azurewebsites.net:443/SampleApp.git`

1. Vyberte **domácí** (Ikona Domovská stránka) > **nastavení** > **globální nastavení**. Zkontrolujte, jestli jsou nastavené jméno a e-mailovou adresu. Vyberte **aktualizace** podle potřeby.

1. Vyberte **Domů** > **změny** se vrátíte **změny** zobrazení.

1. Zadejte zprávu potvrzení změn, jako například **počáteční Push č. 1** a vyberte **potvrzení**. Tato akce vytvoří *potvrzení* místně.

   ![Team Explorer Connect kartu](azure-continuous-deployment/_static/12-initial-commit.png)

   > [!NOTE]
   > Jako alternativu potvrzení změny z **příkazové okno** tak, že otevřete **příkazové okno**, změna k adresáři projektu a zadáte příkazy gitu. Příklad:
   >
   > `git add .`
   >
   > `git commit -am "Initial Push #1"`

1. Vyberte **Domů** > **synchronizace** > **akce** > **otevřete příkazový řádek**. Příkazový řádek se otevře adresář projektu.

1. V příkazovém okně zadejte následující příkaz:

   `git push -u Azure-SampleApp master`

1. Zadejte Azure **přihlašovací údaje pro nasazení** heslo, které vytvořili dříve v Azure.

   Tento příkaz spustí proces odesílání souborů místní projekt do Azure. Výstup z výše uvedeného příkazu ukončí a zobrazí se zpráva, že nasazení bylo úspěšné.

   ```
   remote: Finished successfully.
   remote: Running post deployment command(s)...
   remote: Deployment successful.
   To https://username@samplewebappdemo01.scm.azurewebsites.net:443/SampleWebAppDemo01.git
   * [new branch]      master -> master
   Branch master set up to track remote branch master from Azure-SampleApp.
   ```

   > [!NOTE]
   > Pokud se vyžaduje spolupráci na projekt, vezměte v úvahu doručením (push) do [Githubu](https://github.com) před doručením (push) do Azure.
 
### <a name="verify-the-active-deployment"></a>Ověření aktivní nasazení

Ověřte, jestli přenos webové aplikace z místního prostředí do Azure se úspěšně dokončila.

V [webu Azure Portal](https://portal.azure.com), vyberte webovou aplikaci. Vyberte **nasazení** > **možnosti nasazení**.

![Azure Portal: Okno nastavení: Nasazení okno zobrazující úspěšné nasazení](azure-continuous-deployment/_static/13-verify-deployment.png)

## <a name="run-the-app-in-azure"></a>Spusťte aplikaci v Azure

Teď, když webová aplikace je nasazená do Azure, spusťte aplikaci.

Můžete to provést dvěma způsoby:

* Na webu Azure Portal vyhledejte okně webové aplikace pro webovou aplikaci. Vyberte **Procházet** a zobrazte aplikaci ve výchozím prohlížeči.
* Otevřete prohlížeč a zadejte adresu URL pro webovou aplikaci. Příklad: `http://SampleWebAppDemo.azurewebsites.net`

## <a name="update-the-web-app-and-republish"></a>Aktualizace webové aplikace a opětovné publikování

Po provedení změn na místní kód, znovu publikujte:

1. V **Průzkumníka řešení** sady Visual Studio, otevřete *Startup.cs* souboru.

1. V `Configure` metody změnit `Response.WriteAsync` metodu tak, že se zobrazí takto:

   ```csharp
   await context.Response.WriteAsync("Hello World! Deploy to Azure.");
   ```

1. Uložit změny do *Startup.cs*.

1. V **Průzkumníka řešení**, klikněte pravým tlačítkem na **řešení "SampleWebAppDemo"** a vyberte **potvrzení**. **Team Exploreru** se zobrazí.

1. Zadejte zprávu potvrzení změn, jako například `Update #2`.

1. Stisknutím klávesy **potvrzení** tlačítko potvrďte změny projektu.

1. Vyberte **Domů** > **synchronizace** > **akce** > **Push**.

> [!NOTE]
> Jako alternativu nasdílení změn z **příkazové okno** tak, že otevřete **příkazové okno**, změna k adresáři projektu a zadáním příkazu gitu. Příklad:
> 
> `git push -u Azure-SampleApp master`

## <a name="view-the-updated-web-app-in-azure"></a>Zobrazení aktualizované webové aplikace v Azure

Zobrazení aktualizované webové aplikace tak, že vyberete **Procházet** v okně webové aplikace na webu Azure Portal nebo otevřete prohlížeč a zadat adresu URL pro webovou aplikaci. Příklad: `http://SampleWebAppDemo.azurewebsites.net`

## <a name="additional-resources"></a>Další zdroje

* [Vytvořit svůj první kanál s kanály Azure](/azure/devops/pipelines/get-started-yaml)
* [Projekt Kudu](https://github.com/projectkudu/kudu/wiki)
* <xref:host-and-deploy/visual-studio-publish-profiles>
