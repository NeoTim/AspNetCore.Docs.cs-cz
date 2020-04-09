---
title: Průběžná integrace a nasazování – DevOps s ASP.NET Core a Azure
author: CamSoper
description: Průběžná integrace a nasazení ve DevOps s ASP.NET Core a Azure
ms.author: scaddie
ms.date: 10/24/2018
ms.custom: mvc, seodec18
uid: azure/devops/cicd
ms.openlocfilehash: 5fdf52235b49119503885f92c370dc588e809ffe
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78655832"
---
# <a name="continuous-integration-and-deployment"></a>Průběžná integrace a nasazování

V předchozí kapitole jste vytvořili místní úložiště Git pro aplikaci Simple Feed Reader. V této kapitole tento kód publikujete do úložiště GitHub a vytvoříte kanál služby Azure DevOps Services pomocí Azure Pipelines. Kanál umožňuje nepřetržitá sestavení a nasazení aplikace. Jakékoli potvrzení úložiště GitHub aktivuje sestavení a nasazení do pracovního slotu Azure Web Appu.

V této části dokončíte následující úkoly:

* Publikování kódu aplikace na GitHubu
* Odpojení místního nasazení Gitu
* Vytvoření organizace Azure DevOps
* Vytvoření týmového projektu ve službách Azure DevOps Services
* Vytvoření definice sestavení
* Vytvoření kanálu verze
* Potvrzení změn na GitHubu a automatické nasazení do Azure
* Zkontrolujte kanály Azure

## <a name="publish-the-apps-code-to-github"></a>Publikování kódu aplikace na GitHubu

1. Otevřete okno prohlížeče a `https://github.com`přejděte na .
1. Klikněte **+** na rozevírací soubor v záhlaví a vyberte **Nové úložiště**:

    ![GitHub Nové úložiště, volba](media/cicd/github-new-repo.png)

1. Vrozené vrozené okno **Vlastník** vyberte svůj účet a do textového pole **název úložiště** zadejte *čtečku jednoduchých zdrojů.*
1. Klikněte na tlačítko **Vytvořit úložiště.**
1. Otevřete příkazové prostředí místního počítače. Přejděte do adresáře, ve kterém je uloženo úložiště Git *s jednoduchou čtečkou.*
1. Přejmenujte existující *vzdálený původ* na *protiproud*. Spusťte následující příkaz:

    ```console
    git remote rename origin upstream
    ```

1. Přidejte nový *vzdálený odkaz* na vaši kopii úložiště na GitHubu. Spusťte následující příkaz:

    ```console
    git remote add origin https://github.com/<GitHub_username>/simple-feed-reader/
    ```

1. Publikujte místní úložiště Git do nově vytvořeného úložiště GitHubu. Spusťte následující příkaz:

    ```console
    git push -u origin master
    ```

1. Otevřete okno prohlížeče a `https://github.com/<GitHub_username>/simple-feed-reader/`přejděte na . Ověřte, zda se váš kód zobrazí v úložišti GitHub.

## <a name="disconnect-local-git-deployment"></a>Odpojení místního nasazení Gitu

Odejměte místní nasazení Gitu pomocí následujících kroků. Azure Pipelines (služba Azure DevOps) tuto funkci nahradí i rozšíří.

1. Otevřete [portál Azure](https://portal.azure.com/)a přejděte na pracovní *\<(mywebapp unique_number\>/staging)* Web App. WebOvou aplikaci lze rychle najít zadáním *pracovní hodu* do vyhledávacího pole portálu:

    ![pracovní hledaný termín webové aplikace](media/cicd/portal-search-box.png)

1. Klepněte na **položku Centrum nasazení**. Zobrazí se nový panel. Kliknutím na **Odpojit** odeberete místní konfiguraci správy zdrojového kódu Git, která byla přidána v předchozí kapitole. Potvrďte operaci odebrání kliknutím na tlačítko **Ano.**
1. Přejděte na *mywebapp<unique_number>* App Service. Připomínáme, že vyhledávací pole portálu lze použít k rychlému vyhledání služby App Service.
1. Klepněte na **položku Centrum nasazení**. Zobrazí se nový panel. Kliknutím na **Odpojit** odeberete místní konfiguraci správy zdrojového kódu Git, která byla přidána v předchozí kapitole. Potvrďte operaci odebrání kliknutím na tlačítko **Ano.**

## <a name="create-an-azure-devops-organization"></a>Vytvoření organizace Azure DevOps

1. Otevřete prohlížeč a přejděte na [stránku vytvoření organizace Azure DevOps](https://go.microsoft.com/fwlink/?LinkId=307137).
1. Zadejte jedinečný název do textového pole **Vyberte si zapamatovatelný název** a vytvořte adresu URL pro přístup k vaší organizaci Azure DevOps.
1. Vyberte přepínací tlačítko **Git,** protože kód je hostován v úložišti GitHub.
1. Klepněte na tlačítko **Pokračovat.** Po krátké čekání jsou vytvořeny účet a týmový projekt s názvem *MyFirstProject*.

    ![Stránka vytvoření organizace Azure DevOps](media/cicd/vsts-account-creation.png)

1. Otevřete potvrzovací e-mail s oznámením, že organizace a projekt Azure DevOps jsou připravené k použití. Klikněte na tlačítko **Spustit projekt:**

    ![Tlačítko Zahájit projekt](media/cicd/vsts-start-project.png)

1. Otevře se prohlížeč * \<pro\>account_name .visualstudio.com*. Kliknutím na odkaz *MyFirstProject* zahájíte konfiguraci kanálu DevOps projektu.

## <a name="configure-the-azure-pipelines-pipeline"></a>Konfigurace kanálu Azure Pipelines

Existují tři různé kroky k dokončení. Dokončení kroků v následujících třech částech má za následek provozní kanál DevOps.

### <a name="grant-azure-devops-access-to-the-github-repository"></a>Udělení přístupu Azure DevOps k úložišti GitHub

1. Rozbalte **nebo sestavení kódu z externího úložiště** akordeon. Klepněte na tlačítko **Sestavení instalace:**

    ![Tlačítko Sestavení instalačního programu](media/cicd/vsts-setup-build.png)

1. Vyberte možnost **GitHub** z **oddílu Vybrat zdroj:**

    ![Výběr zdroje - GitHub](media/cicd/vsts-select-source.png)

1. Před tím, než azure devops bude mít přístup k úložišti GitHub, je vyžadována autorizace. Do textového pole Název **připojení** zadejte *<GitHub_username> připojení GitHub.* Příklad:

    ![Název připojení GitHub](media/cicd/vsts-repo-authz.png)

1. Pokud je na vašem účtu GitHub povoleno dvoufaktorové ověřování, je vyžadován osobní přístupový token. V takovém případě klikněte na odkaz **Autorizovat s tokenem osobního přístupu GitHub.** Nápovědu najdete v [oficiálních pokynech pro vytváření tokenů osobního přístupu GitHub.](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/) Je potřeba pouze rozsah *repo* oprávnění. V opačném případě klepněte na tlačítko **Autorizovat pomocí oauth.**
1. Po zobrazení výzvy se přihlaste ke svému účtu GitHub. Pak vyberte Autorizovat, abyste udělili přístup k organizaci Azure DevOps. Pokud je úspěšná, je vytvořen nový koncový bod služby.
1. Klikněte na tlačítko se třemi tečkami vedle tlačítka **Úložiště.** Vyberte *<GitHub_username>/simple-feed-reader* úložiště ze seznamu. Klepněte na tlačítko **Vybrat.**
1. Vyberte *hlavní* větev z rozbalovací ho souboru **Výchozí větev pro ruční a naplánované sestavení.** Klepněte na tlačítko **Pokračovat.** Zobrazí se stránka výběru šablony.

### <a name="create-the-build-definition"></a>Vytvoření definice sestavení

1. Na stránce výběru šablony zadejte do vyhledávacího pole *ASP.NET Jádro:*

    ![hledání ASP.NET jádra na stránce šablony](media/cicd/vsts-template-selection.png)

1. Zobrazí se výsledky hledání šablony. Najeďte na **ASP.NET šablonu jádra** a klikněte na **tlačítko Použít.**
1. Zobrazí se karta **Úkoly** definice sestavení. Klikněte na kartu **Aktivační události.**
1. Zaškrtněte políčko **Povolit průběžnou integraci.** V části **Filtry větve** zkontrolujte, zda je rozevírací soubor **Typ** nastaven na *Zahrnout*. Nastavte rozevírací soubor **specifikace větve** na *hlavní server*.

    ![Povolení nastavení průběžné integrace](media/cicd/vsts-enable-ci.png)

    Tato nastavení způsobit sestavení aktivovat při jakékoli změny je posunut *a hlavní* větev úložiště GitHub. Průběžná integrace se testuje ve [změnách potvrzení githubu a automaticky se nasazuje do](#commit-changes-to-github-and-automatically-deploy-to-azure) sekce Azure.

1. Klepněte na tlačítko **Uložit & fronty** a vyberte možnost **Uložit:**

    ![Tlačítko Uložit](media/cicd/vsts-save-build.png)

1. Zobrazí se následující modální dialogové okno:

    ![Uložit definici sestavení – modální dialog](media/cicd/vsts-save-modal.png)

    Použijte výchozí složku *\\*aplikace a klepněte na tlačítko **Uložit.**

### <a name="create-the-release-pipeline"></a>Vytvoření kanálu vydání

1. Klikněte na kartu **Zprávy** týmového projektu. Klikněte na tlačítko **Nový kanál.**

    ![Karta Zprávy – tlačítko Nová definice](media/cicd/vsts-new-release-definition.png)

    Zobrazí se podokno výběru šablony.

1. Na stránce výběru šablony zadejte *službu App Service* do vyhledávacího pole:

    ![Uvolnit vyhledávací pole šablony kanálu](media/cicd/vsts-release-template-search.png)

1. Zobrazí se výsledky hledání šablony. Najeďte na šablonu **Azure App Service Deployment with Slot** a klikněte na tlačítko **Použít.** Zobrazí se karta **Kanál** kanálu vydání.

    ![Karta Uvolnit kanál potrubí](media/cicd/vsts-release-definition-pipeline.png)

1. Klepněte na tlačítko **Přidat** v poli **Artefakty.** Zobrazí se panel **Přidat artefakt:**

    ![Kanál vydání – panel Přidat artefakty](media/cicd/vsts-release-add-artifact.png)

1. V části Typ **zdroje** vyberte dlaždici **Sestavení.** Tento typ umožňuje propojení kanálu vydání k definici sestavení.
1. V rozevíracím souboru **Project** vyberte *MyFirstProject.*
1. V rozevíracím seznamu **Zdroj (definice sestavení)** vyberte název definice sestavení *MyFirstProject-ASP.NET Core-CI*.
1. V rozevíracím seznamu **Výchozí verze** vyberte *Nejnovější.* Tato možnost vytvoří artefakty vytvořené nejnovější spuštění definice sestavení.
1. Nahraďte text v textovém poli **Zdrojového aliasu** *přetažením*.
1. Klikněte na tlačítko **Přidat.** Sekce **Artefakty** se aktualizuje a zobrazí změny.
1. Kliknutím na ikonu blesku povolíte nepřetržité nasazení:

    ![Uvolnit potrubí Artefakty - ikona blesku](media/cicd/vsts-artifacts-lightning-bolt.png)

    Pokud je tato možnost povolena, dojde k nasazení pokaždé, když je k dispozici nové sestavení.
1. Vpravo se zobrazí spouštěcí panel **průběžného nasazení.** Klepnutím na přepínací tlačítko tuto funkci povolíte. Není nutné povolit aktivační událost požadavku na **přijetí stisknutí .**
1. Klikněte na rozevírací rozbalovací tlačítko **Přidat** v části **Filtry větev sestavení.** Zvolte výchozí možnost **větve definice sestavení.** Tento filtr způsobí, že vydání aktivační pouze pro sestavení z *hlavní* větve úložiště GitHub.
1. Klikněte na tlačítko **Uložit**. Klepněte na tlačítko **OK** ve výsledném dialogovém okně **Uložit** modální.
1. Klikněte na pole **Prostředí 1.** Vpravo se zobrazí panel **Prostředí.** Změňte text *prostředí 1* v textovém poli **Název prostředí** na *Produkční*.

   ![Kanál vydání – textové pole název prostředí](media/cicd/vsts-environment-name-textbox.png)

1. Klikněte na odkaz **1 fáze, 2 úkoly** v poli **Výroba:**

    ![Kanál vydání – odkaz produkčního prostředí.png](media/cicd/vsts-production-link.png)

    Zobrazí se karta **Úkoly** prostředí.
1. Klikněte na úlohu **Nasadit službu Azure App Service na slot.** Jeho nastavení se zobrazí v panelu vpravo.
1. V rozevíracím období **předplatného Azure** vyberte předplatné Azure přidružené ke službě App Service. Po výběru klikněte na tlačítko **Autorizovat.**
1. V rozevíracím souboru **Typ aplikace** vyberte *Web App.*
1. V rozevíracím souboru Název **služby App** vyberte položku *mywebapp/<unique_number/>.*
1. V rozevíracím seznamu **skupiny prostředků** vyberte *AzureTutorial.*
1. V rozevíracím souboru **Slot** vyberte *pracovní.*
1. Klikněte na tlačítko **Uložit**.
1. Najeďte na výchozí název kanálu vydání. Klikněte na ikonu tužky a upravte ji. Jako název použijte *MyFirstProject-ASP.NET Core-CD.*

    ![Uvolnit název kanálu](media/cicd/vsts-release-definition-name.png)

1. Klikněte na tlačítko **Uložit**.

## <a name="commit-changes-to-github-and-automatically-deploy-to-azure"></a>Potvrzení změn na GitHubu a automatické nasazení do Azure

1. Otevřete *soubor SimpleFeedReader.sln* v sadě Visual Studio.
1. V Průzkumníku řešení otevřete *Stránky\Index.cshtml*. Změnit `<h2>Simple Feed Reader - V3</h2>` `<h2>Simple Feed Reader - V4</h2>`na .
1. Stisknutím **klávesy Ctrl**+**Shift**+**B** vytvořte aplikaci.
1. Pozmiřte soubor do úložiště GitHub. Použijte stránku **Změny** na kartě *Průzkumník týmu* sady Visual Studio nebo pomocí příkazového prostředí místního počítače proveďte následující:

    ```console
    git commit -a -m "upgraded to V4"
    ```

1. Posuňte změnu v *hlavní* větvi *na* výchozí dálkové ovládání úložiště GitHub:

    ```console
    git push origin master
    ```

    Potvrzení se zobrazí v *hlavní* větvi úložiště GitHub:

    ![Potvrzení GitHubu v hlavní větvi](media/cicd/github-commit.png)

    Sestavení se aktivuje, protože průběžná integrace je povolena na kartě **Aktivační události** definice sestavení:

    ![umožňují průběžnou integraci](media/cicd/enable-ci.png)

1. Přejděte na kartu **Ve frontě** na stránce**Sestavení** **Azure Pipelines** > ve službách Azure DevOps Services. Sestavení ve frontě zobrazuje větev a potvrzení, které spustilo sestavení:

    ![sestavení ve frontě](media/cicd/build-queued.png)

1. Jakmile sestavení proběhne úspěšně, dojde k nasazení do Azure. Přejděte do aplikace v prohlížeči. Všimněte si, že text "V4" se zobrazí v záhlaví:

    ![aktualizovaná aplikace](media/cicd/updated-app-v4.png)

## <a name="examine-the-azure-pipelines-pipeline"></a>Zkontrolujte kanály Azure

### <a name="build-definition"></a>Definice sestavení

Definice sestavení byla vytvořena s názvem *MyFirstProject-ASP.NET Core-CI*. Po dokončení sestavení vytvoří soubor *ZIP* včetně datových zdrojů, které mají být publikovány. Kanál vydání nasazuje tyto prostředky do Azure.

Na kartě **Úkoly** definice sestavení jsou uvedeny jednotlivé používané kroky. Existuje pět úloh sestavení.

![sestavení úloh definice](media/cicd/build-definition-tasks.png)

1. **Obnovení** &mdash; provede `dotnet restore` příkaz k obnovení nugetových balíčků aplikace. Výchozí balíček je použit nuget.org.
1. **Sestavení** &mdash; Provede `dotnet build --configuration release` příkaz pro kompilaci kódu aplikace. Tato `--configuration` možnost se používá k vytvoření optimalizované verze kódu, která je vhodná pro nasazení do produkčního prostředí. Pokud je například potřeba konfigurace ladění, upravte proměnnou *BuildConfiguration* na kartě **Proměnné** definice sestavení.
1. **Test** &mdash; provede `dotnet test --configuration release --logger trx --results-directory <local_path_on_build_agent>` příkaz ke spuštění testů částí aplikace. Testy částí jsou prováděny v rámci `**/*Tests/*.csproj` libovolného projektu C# odpovídající glob vzor. Výsledky testu jsou uloženy v souboru *.trx* v umístění určeném `--results-directory` volbou. Pokud se všechny testy nezdaří, sestavení se nezdaří a není nasazen.

    > [!NOTE]
    > Chcete-li ověřit práce testů částí, upravte *SimpleFeedReader.Tests\Services\NewsServiceTests.cs* záměrně přerušit jeden z testů. Například změnit `Assert.True(result.Count > 0);` `Assert.False(result.Count > 0);` na `Returns_News_Stories_Given_Valid_Uri` v metodě. Potvrdí meze a posune změnu na GitHub. Sestavení se aktivuje a selže. Stav kanálu sestavení se změní na **neúspěšnou**. Vrátit změnu, potvrdit a znovu zatlačit. Sestavení úspěšné.

1. **Publikování** &mdash; Provede `dotnet publish --configuration release --output <local_path_on_build_agent>` příkaz k vytvoření *souboru ZIP* s artefakty, které mají být nasazeny. Tato `--output` možnost určuje umístění publikování souboru *ZIP.* Toto umístění je určeno předáním `$(build.artifactstagingdirectory)` [předdefinované proměnné](/azure/devops/pipelines/build/variables) s názvem . Tato proměnná se rozbalí na místní cestu, například *c:\agent\_work\1\a*, na agenta sestavení.
1. **Publikování artefaktu** &mdash; publikuje soubor *ZIP* vytvořený úlohou **Publikovat.** Úloha přijme umístění souboru *ZIP* jako parametr, což `$(build.artifactstagingdirectory)`je předdefinovaná proměnná . Soubor *ZIP* je publikován jako složka s názvem *drop*.

Kliknutím na odkaz **Souhrn** definice sestavení zobrazíte historii sestavení s definicí:

![Snímek obrazovky s historií definice sestavení](media/cicd/build-definition-summary.png)

Na výsledné stránce klikněte na odkaz odpovídající jedinečnému číslu sestavení:

![Snímek obrazovky se stránkou souhrnu definice sestavení](media/cicd/build-definition-completed.png)

Zobrazí se souhrn tohoto konkrétního sestavení. Klikněte na kartu **Artefakty** a všimněte si, že složka *přetažení* vytvořená sestavením je uvedena:

![Snímek obrazovky s artefakty definice sestavení – složka přetažení](media/cicd/build-definition-artifacts.png)

Pomocí odkazů **Stáhnout** a **prozkoumat** zkontrolujte publikované artefakty.

### <a name="release-pipeline"></a>Kanál verze

Byl vytvořen kanál vydání s názvem *MyFirstProject-ASP.NET Core-CD*:

![Snímek obrazovky s přehledem kanálu vydání](media/cicd/release-definition-overview.png)

Dvě hlavní součásti kanálu vydání jsou **artefakty** a **prostředí**. Kliknutím na pole v sekci **Artefakty** se zobrazí následující panel:

![Snímek obrazovky s artefakty kanálu vydání](media/cicd/release-definition-artifacts.png)

Source **(Build definice)** hodnota představuje definici sestavení, ke kterému je propojen tento kanál vydání. Soubor *ZIP* vytvořený úspěšným spuštěním definice sestavení je k dispozici *produkčnímu* prostředí pro nasazení do Azure. Kliknutím na odkaz *1 fáze, 2 úkoly* v poli *Produkční* prostředí zobrazíte úlohy kanálu vydání:

![Snímek obrazovky s úkoly kanálu vydání](media/cicd/release-definition-tasks.png)

Kanál vydání se skládá ze dvou úloh: *Nasazení služby Azure App Service do slotu* a *správa služby Azure App Service – prohození slotů*. Kliknutím na první úkol zobrazíte následující konfiguraci úkolu:

![Snímek obrazovky s úlohou nasazení kanálu vydání](media/cicd/release-definition-task1.png)

Předplatné Azure, typ služby, název webové aplikace, skupina prostředků a slot nasazení jsou definovány v úloze nasazení. Textové pole **Balíček nebo složka** obsahuje cestu souboru *ZIP,* která má být extrahována a nasazena do *pracovního* slotu webové aplikace *mywebapp\<unique_number.\> *

Kliknutím na úlohu odměnění slotů se zobrazí následující konfigurace úlohy:

![Snímek obrazovky s úlohou odkládání slotů pro uvolnění kanálu](media/cicd/release-definition-task2.png)

K dispozici jsou podrobnosti o předplatném, skupině prostředků, typu služby, názvu webové aplikace a podrobnostech o slotu nasazení. Zaškrtávací políčko **Zaměnit s výrobou** je zaškrtnuto. V důsledku toho bity nasazené do *pracovního* slotu jsou převedeny do produkčního prostředí.

## <a name="additional-reading"></a>Dodatečné čtení

* [Vytvoření prvního kanálu pomocí Azure Pipelines](/azure/devops/pipelines/get-started-yaml)
* [Sestavení a základní projekt .NET](/azure/devops/pipelines/languages/dotnet-core)
* [Nasazení webové aplikace pomocí Azure Pipelines](/azure/devops/pipelines/targets/webapp)
