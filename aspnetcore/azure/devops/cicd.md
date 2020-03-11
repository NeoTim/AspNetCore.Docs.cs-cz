---
title: Průběžná integrace a nasazování – DevOps s využitím ASP.NET Core a Azure
author: CamSoper
description: Průběžná integrace a nasazování v DevOps s ASP.NET Core a Azure
ms.author: scaddie
ms.date: 10/24/2018
ms.custom: mvc, seodec18
uid: azure/devops/cicd
ms.openlocfilehash: 5fdf52235b49119503885f92c370dc588e809ffe
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78655832"
---
# <a name="continuous-integration-and-deployment"></a>Průběžná integrace a nasazování

V předchozích kapitol vytvoříte místní úložiště Git pro aplikaci jednoduché Reader informačního kanálu. V této kapitole budete publikovat tento kód do úložiště GitHub a vytvořit kanál DevOps služby Azure pomocí Azure kanálů. Kanál umožňuje průběžné vytváření buildů a nasazení aplikace. Každé potvrzení do úložiště GitHub se aktivuje sestavení a nasazení do přípravného slotu webové aplikace Azure.

V této části budete provádět následující úlohy:

* Publikování aplikace kódu na Githubu
* Odpojit místní nasazení přes Git
* Vytvořit organizaci Azure DevOps
* Vytvořit týmový projekt ve službách Azure DevOps
* Vytvoření definice sestavení
* Vytvoření kanálu verze
* Potvrzení změn na GitHubu a automatické nasazení do Azure
* Prozkoumejte Azure kanály kanálu

## <a name="publish-the-apps-code-to-github"></a>Publikování aplikace kódu na Githubu

1. Otevřete okno prohlížeče a přejděte na `https://github.com`.
1. V hlavičce klikněte na rozevírací seznam **+** a vyberte **nové úložiště**:

    ![Možnost Nový úložiště GitHub](media/cicd/github-new-repo.png)

1. V rozevíracím seznamu **vlastník** vyberte svůj účet a do textového pole **název úložiště** zadejte *Simple-feed-Reader* .
1. Klikněte na tlačítko **vytvořit úložiště** .
1. Otevřete příkazové okno místního počítače. Přejděte do adresáře, ve kterém je uloženo úložiště Git s *jednoduchým kanálem pro čtení* .
1. Přejmenujte stávající vzdálený *zdroj* na *nadřazený*. Spusťte následující příkaz:

    ```console
    git remote rename origin upstream
    ```

1. Přidejte na GitHubu nového *zdroje* , který se vzdáleně odkazuje na vaši kopii úložiště. Spusťte následující příkaz:

    ```console
    git remote add origin https://github.com/<GitHub_username>/simple-feed-reader/
    ```

1. Publikování místního úložiště Git do nově vytvořené úložiště GitHub. Spusťte následující příkaz:

    ```console
    git push -u origin master
    ```

1. Otevřete okno prohlížeče a přejděte na `https://github.com/<GitHub_username>/simple-feed-reader/`. Ověřte, že váš kód se zobrazí v úložišti GitHub.

## <a name="disconnect-local-git-deployment"></a>Odpojit místní nasazení přes Git

Odeberte místní nasazení přes Git pomocí následujícího postupu. Kanály Azure (služby Azure DevOps) nahrazuje a argumentech, které tuto funkci.

1. Otevřete [Azure Portal](https://portal.azure.com/)a přejděte do pracovní webové aplikace *(mywebapp\<unique_number\>/staging)* . Webovou aplikaci můžete rychle najít tak, že do vyhledávacího pole portálu zadáte *fázování* :

    ![pracovní webové aplikace hledaný termín](media/cicd/portal-search-box.png)

1. Klikněte na **centrum nasazení**. Otevře se nový panel. Kliknutím na **Odpojit** odeberte konfiguraci místního řízení zdrojů Git, která se přidala do předchozí kapitoly. Kliknutím na tlačítko **Ano** potvrďte operaci odebrání.
1. Přejděte do *mywebapp < unique_number >* App Service. Připomínáme je možné k rychlému vyhledání služby App Service na portálu vyhledávacího pole.
1. Klikněte na **centrum nasazení**. Otevře se nový panel. Kliknutím na **Odpojit** odeberte konfiguraci místního řízení zdrojů Git, která se přidala do předchozí kapitoly. Kliknutím na tlačítko **Ano** potvrďte operaci odebrání.

## <a name="create-an-azure-devops-organization"></a>Vytvořit organizaci Azure DevOps

1. Otevřete prohlížeč a přejděte na [stránku pro vytvoření organizace Azure DevOps](https://go.microsoft.com/fwlink/?LinkId=307137).
1. Do textového pole **Vybrat zapamatovatelné jméno** zadejte jedinečný název, který bude tvořit adresu URL pro přístup ke svojí organizaci Azure DevOps.
1. Vyberte přepínač **Git** , protože je tento kód hostovaný v úložišti GitHubu.
1. Klikněte na tlačítko **Pokračovat**. Po krátkém čekání se vytvoří účet a týmový projekt s názvem *MyFirstProject*.

    ![Stránka Vytvořit organizaci Azure DevOps](media/cicd/vsts-account-creation.png)

1. Otevřete potvrzení e-mailu označující, že organizaci Azure DevOps a projektu jsou připravené k použití. Klikněte na tlačítko **spustit projekt** :

    ![Váš projekt tlačítko Start](media/cicd/vsts-start-project.png)

1. Otevře se prohlížeč *\<account_name\>. VisualStudio.com*. Kliknutím na odkaz *MyFirstProject* zahajte konfiguraci kanálu DevOps projektu.

## <a name="configure-the-azure-pipelines-pipeline"></a>Nakonfigurujte kanál kanály Azure

Existují tři samostatné kroky k dokončení. Dokončením kroků v následujících třech částech vede provozní kanál DevOps.

### <a name="grant-azure-devops-access-to-the-github-repository"></a>Azure DevOps udělit přístup k úložišti GitHub

1. Rozbalte **nebo sestavte kód z přidaných externích úložišť** . Klikněte na tlačítko **sestavení pro instalaci** :

    ![Tlačítko Nastavení sestavení](media/cicd/vsts-setup-build.png)

1. V části **Vybrat zdroj** vyberte možnost **GitHub** :

    ![Vyberte zdroje – GitHub](media/cicd/vsts-select-source.png)

1. Vyžaduje se autorizace, než Azure DevOps můžete získat přístup k úložišti GitHub. Do textového pole **název připojení** zadejte *< GitHub_username > připojení GitHubu* . Příklad:

    ![Název připojení Githubu](media/cicd/vsts-repo-authz.png)

1. Pokud na vašem účtu GitHub je povoleno dvoufaktorové ověřování, osobní přístupový token je povinný. V takovém případě klikněte na odkaz **autorizovat pomocí osobního přístupového tokenu GitHubu** . Nápovědu najdete v [oficiálních pokynech k vytvoření tokenu GitHubu pro GitHub](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/) . Je potřeba jenom obor *úložiště* oprávnění. V opačném případě klikněte na tlačítko **autorizovat pomocí OAuth** .
1. Po zobrazení výzvy, přihlaste se k vašemu účtu GitHub. Vyberte Authorize k udělení přístupu k vaší organizaci Azure DevOps. V případě úspěchu, je vytvořen nový koncový bod služby.
1. Klikněte na tlačítko se třemi tečkami vedle tlačítka **úložiště** . V seznamu vyberte *< GitHub_username > úložiště/Simple-feed-Reader* . Klikněte na tlačítko **Vybrat** .
1. Vyberte *Hlavní* větev z rozevíracího seznamu **výchozí větev pro ruční a plánovaná sestavení** . Klikněte na tlačítko **Pokračovat**. Zobrazí se stránka pro výběr šablony.

### <a name="create-the-build-definition"></a>Vytvořte definici sestavení

1. Na stránce Výběr šablony zadejte do vyhledávacího pole *ASP.NET Core* :

    ![ASP.NET Core hledání na stránce šablony](media/cicd/vsts-template-selection.png)

1. Šablona výsledky hledání zobrazeny. Najeďte myší na šablonu **ASP.NET Core** a klikněte na tlačítko **použít** .
1. Zobrazí se karta **úkoly** v definici sestavení. Klikněte na kartu **triggery** .
1. Zaškrtněte políčko **Povolit průběžnou integraci** . V části **filtry větví** potvrďte, že rozevírací seznam **typ** je nastavený na *Zahrnout*. Nastavte rozevírací seznam **specifikace větve** na *hlavní*.

    ![Povolit nastavení průběžné integrace](media/cicd/vsts-enable-ci.png)

    Tato nastavení způsobí, že se sestavení aktivuje při vložení jakékoli změny do *Hlavní* větve úložiště GitHub. Nepřetržitá integrace je testována v části [Potvrdit změny do GitHubu a automaticky se nasazuje do Azure](#commit-changes-to-github-and-automatically-deploy-to-azure) .

1. Klikněte na tlačítko **uložit & frontu** a vyberte možnost **Uložit** :

    ![Tlačítko Uložit](media/cicd/vsts-save-build.png)

1. Zobrazí se následující modální dialogové okno:

    ![Modální dialogové okno Uložit definici sestavení-](media/cicd/vsts-save-modal.png)

    Použijte výchozí složku *\\* a klikněte na tlačítko **Uložit** .

### <a name="create-the-release-pipeline"></a>Vytvořit kanál pro vydávání verzí

1. Klikněte na kartu **vydání** v týmovém projektu. Klikněte na tlačítko **Nový kanál** .

    ![Karta – tlačítko Nová definice verze](media/cicd/vsts-new-release-definition.png)

    Otevře se podokno výběr šablony.

1. Na stránce Výběr šablony zadejte do vyhledávacího pole *App Service* :

    ![Verze kanálu šablony vyhledávacího pole](media/cicd/vsts-release-template-search.png)

1. Šablona výsledky hledání zobrazeny. Najeďte myší na **nasazení Azure App Service se šablonou slotu** a klikněte na tlačítko **použít** . Zobrazí se karta **kanál** kanálu vydání.

    ![Kanál pro vydávání verzí kartu kanálu](media/cicd/vsts-release-definition-pipeline.png)

1. Klikněte na tlačítko **Přidat** v poli **artefakty** . Zobrazí se panel **Přidat artefakt** :

    ![Kanál pro vydávání verzí – přidání artefaktu panelu](media/cicd/vsts-release-add-artifact.png)

1. Vyberte dlaždici **sestavení** z oddílu **typ zdroje** . Tento typ umožňuje nastavit odkazy kanál pro vydávání verzí této definici sestavení.
1. V rozevíracím seznamu **projekt** vyberte *MyFirstProject* .
1. Z rozevíracího seznamu **zdroj (definice sestavení)** vyberte název definice sestavení, *MYFIRSTPROJECT-ASP.NET Core-CI*.
1. Z rozevíracího seznamu **výchozí verze** vyberte *nejnovější* . Tato možnost sestavení artefakty vytvořené spuštěním nejnovější definice sestavení.
1. Text v textovém poli **aliasu zdroje** nahraďte textem *drop*.
1. Klikněte na tlačítko **Přidat**. Část **artefakty** aktualizuje, aby se zobrazily změny.
1. Klikněte na ikonu blesku povolit nepřetržité nasazení:

    ![Kanál pro vydávání verzí artefakty – ikona blesku](media/cicd/vsts-artifacts-lightning-bolt.png)

    Tato možnost povolená dojde k nasazení pokaždé, když je k dispozici nové sestavení.
1. Napravo se zobrazí panel **Trigger průběžného nasazování** . Klikněte na přepínací tlačítko k povolení této funkce. Není nutné povolit **Trigger žádosti o získání dat**.
1. V části **filtry větve sestavení** klikněte na rozevírací nabídku **Přidat** . Vyberte možnost **výchozí větev definice sestavení** . Tento filtr způsobí, že se vydaná verze aktivuje jenom pro sestavení z *Hlavní* větve úložiště GitHub.
1. Klikněte na tlačítko **Uložit**. Klikněte na tlačítko **OK** v dialogovém okně výsledný dialog **Uložit** modální.
1. Klikněte na pole **prostředí 1** . Napravo se zobrazí panel **prostředí** . Změňte text *prostředí 1* v textovém poli **Název prostředí** na *produkční*.

   ![Kanál pro vydávání verzí – textové pole pro název prostředí](media/cicd/vsts-environment-name-textbox.png)

1. V **produkčním** poli klikněte na odkaz **1 fáze, 2 úlohy** :

    ![Kanál pro vydávání verzí - link.png produkční prostředí](media/cicd/vsts-production-link.png)

    Zobrazí se karta **úlohy** v prostředí.
1. Klikněte na úlohu **nasadit Azure App Service pro slot** . Nastavení se zobrazí v panelu napravo.
1. Z rozevíracího seznamu **předplatné Azure** vyberte předplatné Azure přidružené k tomuto App Service. Po výběru klikněte na tlačítko **autorizovat** .
1. V rozevíracím seznamu **Typ aplikace** vyberte možnost *Webová aplikace* .
1. V rozevíracím seznamu **název služby App Service** vyberte *mywebapp/< unique_number/>* .
1. V rozevíracím seznamu **Skupina prostředků** vyberte *AzureTutorial* .
1. V rozevíracím seznamu **slot** vyberte *fázování* .
1. Klikněte na tlačítko **Uložit**.
1. Najeďte myší výchozí název kanálu vydané verze. Klikněte na ikonu tužky a upravte ho. Jako název použijte *MyFirstProject-ASP.NET Core-CD* .

    ![Název kanálu vydané verze](media/cicd/vsts-release-definition-name.png)

1. Klikněte na tlačítko **Uložit**.

## <a name="commit-changes-to-github-and-automatically-deploy-to-azure"></a>Potvrzení změn na GitHubu a automatické nasazení do Azure

1. Otevřete *SimpleFeedReader. sln* v aplikaci Visual Studio.
1. V Průzkumník řešení otevřete *Pages\Index.cshtml*. Změňte `<h2>Simple Feed Reader - V3</h2>` na `<h2>Simple Feed Reader - V4</h2>`.
1. Stisknutím **kombinace kláves Ctrl**+**SHIFT**+**B** sestavte aplikaci.
1. Potvrďte souboru do úložiště GitHub. Na kartě *Team Explorer* v aplikaci Visual Studio použijte stránku **změny** nebo pomocí příkazového prostředí místního počítače spusťte následující příkaz:

    ```console
    git commit -a -m "upgraded to V4"
    ```

1. Nahrajte změnu v *Hlavní* větvi do *počátečního* vzdáleného úložiště GitHubu:

    ```console
    git push origin master
    ```

    Potvrzení se zobrazí v *Hlavní* větvi úložiště GitHub:

    ![Potvrzení Githubu v hlavní větvi](media/cicd/github-commit.png)

    Sestavení je aktivováno, protože je povolena průběžná integrace na kartě **aktivační události** definice sestavení:

    ![Povolit průběžnou integraci](media/cicd/enable-ci.png)

1. Na stránce **Azure Pipelines** > **sestavení** v Azure DevOps Services přejděte na kartu **zařazené do fronty** . Sestavení zařazené do fronty ukazuje větve a potvrzení změn, které aktivuje sestavení:

    ![sestavení zařazené do fronty](media/cicd/build-queued.png)

1. Po úspěšném sestavení, dojde k nasazení do Azure. Přejděte do aplikace v prohlížeči. Všimněte si, že se zobrazí text "V4" v nadpisu:

    ![aktualizovaná aplikace](media/cicd/updated-app-v4.png)

## <a name="examine-the-azure-pipelines-pipeline"></a>Prozkoumejte Azure kanály kanálu

### <a name="build-definition"></a>Definice sestavení

Byla vytvořena definice sestavení s názvem *MyFirstProject-ASP.NET Core-CI*. Po dokončení sestavení vytvoří soubor *. zip* , včetně prostředků, které mají být publikovány. Kanál pro vydávání verzí nasadí tyto prostředky do Azure.

Na kartě **úkoly** definice sestavení jsou uvedeny jednotlivé kroky, které se používají. Existuje pět úloh sestavení.

![definice úlohy sestavení](media/cicd/build-definition-tasks.png)

1. **Restore** &mdash; spustí příkaz `dotnet restore`, který obnoví balíčky NuGet aplikace. Výchozí balíček informační kanál používá je nuget.org.
1. **Sestavování** &mdash; spustí příkaz `dotnet build --configuration release` pro zkompilování kódu aplikace. Tato možnost `--configuration` slouží k vytvoření optimalizované verze kódu, který je vhodný pro nasazení do produkčního prostředí. Pokud je například nutná konfigurace ladění, upravte proměnnou *BuildConfiguration* na kartě **proměnné** v definici sestavení.
1. **Test** &mdash; spustí příkaz `dotnet test --configuration release --logger trx --results-directory <local_path_on_build_agent>` ke spuštění testů jednotek aplikace. Testy jednotek se spustí v rámci C# libovolného projektu, který odpovídá glob vzoru pro `**/*Tests/*.csproj`. Výsledky testů jsou uloženy v souboru *. TRX* v umístění určeném možností `--results-directory`. Pokud selžou i všechny testy, sestavení selže a není nasazený.

    > [!NOTE]
    > Chcete-li ověřit testy jednotek, upravte *SimpleFeedReader. Tests\Services\NewsServiceTests.cs* tak, aby záměrně přerušení jednoho testu. Například změňte `Assert.True(result.Count > 0);` na `Assert.False(result.Count > 0);` v metodě `Returns_News_Stories_Given_Valid_Uri`. Potvrďte a odešlete změny na Githubu. Sestavení se aktivuje a selže. Stav kanálu sestavení se změní na **neúspěch**. Vrácení změn, potvrzení a nabízených oznámení znovu. Sestavení úspěšné.

1. **Publikování** &mdash; spustí příkaz `dotnet publish --configuration release --output <local_path_on_build_agent>` a vytvoří soubor *. zip* s artefakty, které mají být nasazeny. Možnost `--output` určuje umístění publikování souboru *. zip* . Toto umístění je určeno předáním [předdefinované proměnné](/azure/devops/pipelines/build/variables) s názvem `$(build.artifactstagingdirectory)`. Tato proměnná se v agentovi sestavení rozšíří na místní cestu, například *c:\agent\_work\1\a*.
1. **Publikování artefaktu** &mdash; publikuje soubor *. zip* vytvářený úlohou **Publish** . Úkol přijme umístění souboru *. zip* jako parametr, což je předdefinovaná proměnná `$(build.artifactstagingdirectory)`. Soubor *. zip* je publikován jako složka s názvem *drop*.

Kliknutím na **souhrnný** odkaz definice sestavení zobrazíte historii sestavení s definicí:

![Snímek obrazovky znázorňující v historii definic sestavení](media/cicd/build-definition-summary.png)

Na stránce výsledný kliknutím na odkaz odpovídající číslu jedinečný sestavení:

![Snímek obrazovky znázorňující definice stránce se souhrnem sestavení](media/cicd/build-definition-completed.png)

Zobrazí se přehled tohoto konkrétního sestavení. Klikněte na kartu **artefakty** a Všimněte si, že je uvedena *ukládací* složka vytvořená sestavením:

![Snímek obrazovky s artefakty definice sestavení - odkládací složky](media/cicd/build-definition-artifacts.png)

Pomocí odkazů **Stáhnout** a **prozkoumat** můžete zkontrolovat publikované artefakty.

### <a name="release-pipeline"></a>Kanál verze

Byl vytvořen kanál vydané verze s názvem *MyFirstProject-ASP.NET Core-CD*:

![Snímek obrazovky znázorňující verze kanálu přehled](media/cicd/release-definition-overview.png)

Mezi dvě hlavní součásti kanálu pro vydávání verzí patří **artefakty** a **prostředí**. Kliknutím na pole v části **artefakty** se odhalí následující panel:

![Snímek obrazovky znázorňující verze kanálu artefaktů](media/cicd/release-definition-artifacts.png)

Hodnota **zdroj (definice sestavení)** představuje definici sestavení, ke které je tento kanál verze propojený. Soubor *. zip* , který vygenerovalo úspěšné spuštění definice sestavení, je poskytován *provoznímu* prostředí pro nasazení do Azure. Úkoly kanálu vydaných verzí zobrazíte kliknutím na odkaz *1 fáze, 2 úlohy* v poli *provozní* prostředí:

![Snímek obrazovky znázorňující verze kanálu úlohy](media/cicd/release-definition-tasks.png)

Kanál vydané verze se skládá ze dvou úloh: *nasazení Azure App Service do slotu* a *správa swapu Azure App Serviceho slotu*. Kliknutím na první úkol zobrazí následující konfigurace úlohy:

![Úloha nasazení kanálu pro vydávání verzí – snímek obrazovky znázorňující](media/cicd/release-definition-task1.png)

Předplatné Azure, typ služby, název webové aplikace, skupiny prostředků a slot pro nasazení jsou definovány v úlohu nasazení. Textové pole **balíčku nebo složky** obsahuje cestu k souboru *. zip* , která se má extrahovat a nasadit do *přípravného* slotu *MyWebApp\<unique_number\>* webové aplikace.

Klepnutím na úkol, slot swap, zobrazí se následující konfigurace úlohy:

![Snímek obrazovky znázorňující uvolnění kanálu slotu prohození úlohy](media/cicd/release-definition-task2.png)

Předplatné, skupinu prostředků, typ služby, název webové aplikace a podrobnosti o slot nasazení jsou k dispozici. Zaškrtávací políčko **swap s výrobou** je zaškrtnuté. V důsledku toho se bity nasazené do *přípravného* slotu odsadí do produkčního prostředí.

## <a name="additional-reading"></a>Další čtení

* [Vytvoření prvního kanálu pomocí Azure Pipelines](/azure/devops/pipelines/get-started-yaml)
* [Projekt sestavení a .NET Core](/azure/devops/pipelines/languages/dotnet-core)
* [Nasazení webové aplikace pomocí Azure Pipelines](/azure/devops/pipelines/targets/webapp)
