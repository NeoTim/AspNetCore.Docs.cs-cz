---
title: Průběžná integrace a nasazování – DevOps s využitím ASP.NET Core a Azure
author: CamSoper
description: Průběžná integrace a nasazování v DevOps s ASP.NET Core a Azure
ms.author: scaddie
ms.date: 10/24/2018
ms.custom: seodec18
uid: azure/devops/cicd
ms.openlocfilehash: e5bddde41291c9573f58d749bbf830de9ea9319d
ms.sourcegitcommit: 49faca2644590fc081d86db46ea5e29edfc28b7b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/09/2018
ms.locfileid: "53121586"
---
# <a name="continuous-integration-and-deployment"></a>Průběžná integrace a nasazování

V předchozích kapitol vytvoříte místní úložiště Git pro aplikaci jednoduché Reader informačního kanálu. V této kapitole budete publikovat tento kód do úložiště GitHub a vytvořit kanál DevOps služby Azure pomocí Azure kanálů. Kanál umožňuje průběžné vytváření buildů a nasazení aplikace. Každé potvrzení do úložiště GitHub se aktivuje sestavení a nasazení do přípravného slotu webové aplikace Azure.

V této části budete provádět následující úlohy:

* Publikování aplikace kódu na Githubu
* Odpojit místní nasazení přes Git
* Vytvořit organizaci Azure DevOps
* Vytvořit týmový projekt ve službách Azure DevOps
* Vytvořte definici sestavení
* Vytvoření kanálu verze
* Potvrzení změn na Githubu a automaticky nasadit do Azure
* Prozkoumejte Azure kanály kanálu

## <a name="publish-the-apps-code-to-github"></a>Publikování aplikace kódu na Githubu

1. Otevřete okno prohlížeče a přejděte do `https://github.com`.
1. Klikněte na tlačítko **+** rozevírací seznam v záhlaví a vyberte **nové úložiště**:

    ![Možnost Nový úložiště GitHub](media/cicd/github-new-repo.png)

1. Vyberte svůj účet v **vlastníka** rozevíracího seznamu a zadejte *jednoduchý kanálu čtečky* v **název úložiště** textového pole.
1. Klikněte na tlačítko **vytvoření úložiště** tlačítko.
1. Otevřete příkazové okno místního počítače. Přejděte do adresáře, ve kterém *jednoduchý kanálu čtečky* uložená v úložišti Git.
1. Přejmenovat stávající *původu* do vzdáleného úložiště *nadřazeného*. Spusťte následující příkaz:
    ```console
    git remote rename origin upstream
    ```
1. Přidat nový *původu* vzdálené odkazuje na kopii úložišti na Githubu. Spusťte následující příkaz:
    ```console
    git remote add origin https://github.com/<GitHub_username>/simple-feed-reader/
    ```
1. Publikování místního úložiště Git do nově vytvořené úložiště GitHub. Spusťte následující příkaz:
    ```console
    git push -u origin master
    ```
1. Otevřete okno prohlížeče a přejděte do `https://github.com/<GitHub_username>/simple-feed-reader/`. Ověřte, že váš kód se zobrazí v úložišti GitHub.

## <a name="disconnect-local-git-deployment"></a>Odpojit místní nasazení přes Git

Odeberte místní nasazení přes Git pomocí následujícího postupu. Kanály Azure (služby Azure DevOps) nahrazuje a argumentech, které tuto funkci.

1. Otevřít [webu Azure portal](https://portal.azure.com/)a přejděte *pracovní (mywebapp\<unique_number\>/pracovní)* webové aplikace. Webové aplikace můžete rychle umístěný tak, že zadáte *pracovní* vyhledávacího pole na portálu:

    ![pracovní webové aplikace hledaný termín](media/cicd/portal-search-box.png)

1. Klikněte na tlačítko **možnosti nasazení**. Otevře se nový panel. Klikněte na tlačítko **odpojit** místní Git konfigurace správy zdrojového kódu, který byl přidán v předchozích kapitol odebrat. Operace odstranění potvrďte kliknutím **Ano** tlačítko.
1. Přejděte *mywebapp < unique_number >* služby App Service. Připomínáme je možné k rychlému vyhledání služby App Service na portálu vyhledávacího pole.
1. Klikněte na tlačítko **možnosti nasazení**. Otevře se nový panel. Klikněte na tlačítko **odpojit** místní Git konfigurace správy zdrojového kódu, který byl přidán v předchozích kapitol odebrat. Operace odstranění potvrďte kliknutím **Ano** tlačítko.

## <a name="create-an-azure-devops-organization"></a>Vytvořit organizaci Azure DevOps

1. Otevřete prohlížeč a přejděte [stránce pro vytvoření organizace Azure DevOps](https://go.microsoft.com/fwlink/?LinkId=307137).
1. Zadejte jedinečný název do **vyberte si snadno zapamatovatelné jméno** textového pole a vytvoří adresu URL pro přístup k vaší organizaci Azure DevOps.
1. Vyberte **Git** přepínač, protože je kód hostovaný v úložišti GitHub.
1. Klikněte na tlačítko **pokračovat** tlačítko. Po krátkém čekání, účet a týmový projekt s názvem *MyFirstProject*, jsou vytvořeny.

    ![Stránka Vytvořit organizaci Azure DevOps](media/cicd/vsts-account-creation.png)

1. Otevřete potvrzení e-mailu označující, že organizaci Azure DevOps a projektu jsou připravené k použití. Klikněte na tlačítko **začněte svůj projekt** tlačítka:

    ![Váš projekt tlačítko Start](media/cicd/vsts-start-project.png)

1. V prohlížeči se otevře  *\<account_name\>. visualstudio.com*. Klikněte na tlačítko *MyFirstProject* odkaz se začne konfigurace projektu kanálu DevOps.

## <a name="configure-the-azure-pipelines-pipeline"></a>Nakonfigurujte kanál kanály Azure

Existují tři samostatné kroky k dokončení. Dokončením kroků v následujících třech částech vede provozní kanál DevOps.

### <a name="grant-azure-devops-access-to-the-github-repository"></a>Azure DevOps udělit přístup k úložišti GitHub

1. Rozbalte **nebo vytváření kódu z externího úložiště** prvku typu accordion. Klikněte na tlačítko **nastavení sestavení** tlačítka:

    ![Tlačítko Nastavení sestavení](media/cicd/vsts-setup-build.png)

1. Vyberte **Githubu** možnost **vyberte zdroj** části:

    ![Vyberte zdroje – GitHub](media/cicd/vsts-select-source.png)

1. Vyžaduje se autorizace, než Azure DevOps můžete získat přístup k úložišti GitHub. Zadejte *< GitHub_username > Githubu připojení* v **název připojení** textového pole. Příklad:

    ![Název připojení Githubu](media/cicd/vsts-repo-authz.png)

1. Pokud na vašem účtu GitHub je povoleno dvoufaktorové ověřování, osobní přístupový token je povinný. V takovém případě klikněte na tlačítko **autorizovat pomocí osobního přístupového tokenu Githubu** odkaz. Zobrazit [oficiální pokyny k vytvoření tokenu pat Githubu](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/) nápovědu. Pouze *úložiště* obor oprávnění je potřeba. V opačném případě klikněte na tlačítko **autorizovat pomocí OAuth** tlačítko.
1. Po zobrazení výzvy, přihlaste se k vašemu účtu GitHub. Vyberte Authorize k udělení přístupu k vaší organizaci Azure DevOps. V případě úspěchu, je vytvořen nový koncový bod služby.
1. Klikněte na tlačítko se třemi tečkami vedle **úložiště** tlačítko. Vyberte *< GitHub_username > / jednoduchý kanálu čtečky* úložiště ze seznamu. Klikněte na tlačítko **vyberte** tlačítko.
1. Vyberte *hlavní* vytvářet větve z **výchozí větev pro ruční a plánovaná sestavení** rozevíracího seznamu. Klikněte na tlačítko **pokračovat** tlačítko. Zobrazí se stránka pro výběr šablony.

### <a name="create-the-build-definition"></a>Vytvořte definici sestavení

1. Na stránce Výběr šablony zadejte *ASP.NET Core* do vyhledávacího pole:

    ![ASP.NET Core hledání na stránce šablony](media/cicd/vsts-template-selection.png)

1. Šablona výsledky hledání zobrazeny. Najeďte myší **ASP.NET Core** šablony a kliknutím **použít** tlačítko.
1. **Úlohy** se zobrazí karta definice sestavení. Klikněte na tlačítko **triggery** kartu.
1. Zkontrolujte, **aktivovat nepřetržitou integraci** pole. V části **filtry větví** části, ujistěte se, že **typ** rozevíracího seznamu je nastavena na *zahrnout*. Nastavte **větev specifikace** rozevíracího seznamu *hlavní*.

    ![Povolit nastavení průběžné integrace](media/cicd/vsts-enable-ci.png)

    Toto nastavení způsobí sestavení aktivovat při každé změně se vloží do *hlavní* větev úložiště GitHub. Nepřetržitá integrace je testován v [potvrzení změn na Githubu a automaticky nasadit do Azure](#commit-changes-to-github-and-automatically-deploy-to-azure) oddílu.

1. Klikněte na tlačítko **Uložit & frontu** tlačítko a vyberte **Uložit** možnost:

    ![Tlačítko Uložit](media/cicd/vsts-save-build.png)

1. Zobrazí se následující modální dialogové okno:

    ![Modální dialogové okno Uložit definici sestavení-](media/cicd/vsts-save-modal.png)

    Použít výchozí složky *\\* a klikněte na tlačítko **Uložit** tlačítko.

### <a name="create-the-release-pipeline"></a>Vytvořit kanál pro vydávání verzí

1. Klikněte na tlačítko **verze** kartu týmového projektu. Klikněte na tlačítko **nový kanál** tlačítko.

    ![Karta – tlačítko Nová definice verze](media/cicd/vsts-new-release-definition.png)

    Otevře se podokno výběr šablony.

1. Na stránce Výběr šablony zadejte *služby App Service* do vyhledávacího pole:

    ![Verze kanálu šablony vyhledávacího pole](media/cicd/vsts-release-template-search.png)

1. Šablona výsledky hledání zobrazeny. Najeďte myší **nasazení služby Azure App Service se slotem** šablony a kliknutím **použít** tlačítko. **Kanálu** se zobrazí karta kanál pro vydávání verzí.

    ![Kanál pro vydávání verzí kartu kanálu](media/cicd/vsts-release-definition-pipeline.png)

1. Klikněte na tlačítko **přidat** tlačítko **artefakty** pole. **Přidání artefaktu** panelu se zobrazí:

    ![Kanál pro vydávání verzí – přidání artefaktu panelu](media/cicd/vsts-release-add-artifact.png)

1. Vyberte **sestavení** dlaždici z **typ zdroje** oddílu. Tento typ umožňuje nastavit odkazy kanál pro vydávání verzí této definici sestavení.
1. Vyberte *MyFirstProject* z **projektu** rozevíracího seznamu.
1. Název definice sestavení vyberte *MyFirstProject ASP.NET Core-CI*, z **zdroj (definice sestavení)** rozevíracího seznamu.
1. Vyberte *nejnovější* z **výchozí verze** rozevíracího seznamu. Tato možnost sestavení artefakty vytvořené spuštěním nejnovější definice sestavení.
1. Nahradit text **alias zdroje** textové pole s *vyřadit*.
1. Klikněte na tlačítko **Přidat**. **Artefakty** části aktualizací zobrazíte změny.
1. Klikněte na ikonu blesku povolit nepřetržité nasazení:

    ![Kanál pro vydávání verzí artefakty – ikona blesku](media/cicd/vsts-artifacts-lightning-bolt.png)

    Tato možnost povolená dojde k nasazení pokaždé, když je k dispozici nové sestavení.
1. A **trigger průběžného nasazování** panelu se zobrazí na pravé straně. Klikněte na přepínací tlačítko k povolení této funkce. Není nutná pro povolení **triggeru žádosti o přijetí změn**.
1. Klikněte na tlačítko **přidat** rozevírací seznam v **vytvářet filtry větví** oddílu. Zvolte **Build Definition výchozí větev** možnost. Tento filtr způsobí, že verze aktivovat pouze pro sestavení z úložiště GitHub *hlavní* větve.
1. Klikněte na tlačítko **Uložit**. Klikněte na tlačítko **OK** tlačítko ve výsledné **Uložit** modální dialogové okno.
1. Klikněte na tlačítko **prostředí 1** pole. **Prostředí** panelu se zobrazí na pravé straně. Změnit *prostředí 1* textu v **název prostředí** testovém poli *produkční*.

   ![Kanál pro vydávání verzí – textové pole pro název prostředí](media/cicd/vsts-environment-name-textbox.png)

1. Klikněte na tlačítko **fáze 1, 2 úlohy** odkaz v **produkční** pole:

    ![Kanál pro vydávání verzí - link.png produkční prostředí](media/cicd/vsts-production-link.png)

    **Úlohy** se zobrazí karta prostředí.
1. Klikněte na tlačítko **nasazení služby Azure App Service do slotu** úloh. Nastavení se zobrazí v panelu napravo.
1. Vyberte předplatné Azure spojené s App Service z **předplatného Azure** rozevíracího seznamu. Po výběru, klikněte na tlačítko **Authorize** tlačítko.
1. Vyberte *webovou aplikaci* z **typ aplikace** rozevíracího seznamu.
1. Vyberte *mywebapp / < unique_number / >* z **název služby App service** rozevíracího seznamu.
1. Vyberte *AzureTutorial* z **skupiny prostředků** rozevíracího seznamu.
1. Vyberte *pracovní* z **slotu** rozevíracího seznamu.
1. Klikněte na tlačítko **Uložit**.
1. Najeďte myší výchozí název kanálu vydané verze. Klikněte na ikonu tužky a upravte ho. Použití *MyFirstProject ASP.NET Core-CD* jako název.

    ![Název kanálu vydané verze](media/cicd/vsts-release-definition-name.png)

1. Klikněte na tlačítko **Uložit**.

## <a name="commit-changes-to-github-and-automatically-deploy-to-azure"></a>Potvrzení změn na Githubu a automaticky nasadit do Azure

1. Otevřít *SimpleFeedReader.sln* v sadě Visual Studio.
1. V Průzkumníku řešení otevřete *Pages\Index.cshtml*. Změna `<h2>Simple Feed Reader - V3</h2>` k `<h2>Simple Feed Reader - V4</h2>`.
1. Stisknutím klávesy **Ctrl**+**Shift**+**B** k sestavení aplikace.
1. Potvrďte souboru do úložiště GitHub. Použijte buď **změny** stránky v sadě Visual Studio *Team Exploreru* kartu, nebo spuštěním následujících pomocí příkazového prostředí služby místním počítači:

    ```console
    git commit -a -m "upgraded to V4"
    ```
1. Nahrát změnu *hlavní* větvit do *původu* vzdálené úložiště GitHub:

    ```console
    git push origin master
    ```

    Zobrazí se potvrzení změn v úložišti GitHub *hlavní* větev:

    ![Potvrzení Githubu v hlavní větvi](media/cicd/github-commit.png)

    Sestavení se aktivuje, protože v definici sestavení je povolená Nepřetržitá integrace **triggery** kartu:

    ![Povolit průběžnou integraci](media/cicd/enable-ci.png)

1. Přejděte **zařazeno do fronty** kartě **kanály Azure** > **sestavení** stránku služby Azure DevOps. Sestavení zařazené do fronty ukazuje větve a potvrzení změn, které aktivuje sestavení:

    ![sestavení zařazené do fronty](media/cicd/build-queued.png)

1. Po úspěšném sestavení, dojde k nasazení do Azure. Přejděte do aplikace v prohlížeči. Všimněte si, že se zobrazí text "V4" v nadpisu:

    ![aktualizovaná aplikace](media/cicd/updated-app-v4.png)

## <a name="examine-the-azure-pipelines-pipeline"></a>Prozkoumejte Azure kanály kanálu

### <a name="build-definition"></a>Definice sestavení

Definice sestavení byla vytvořena s názvem *MyFirstProject ASP.NET Core-CI*. Po dokončení sestavení vytváří *ZIP* souboru, včetně prostředků má být publikován. Kanál pro vydávání verzí nasadí tyto prostředky do Azure.

Definice sestavení **úlohy** karta obsahuje seznam jednotlivých kroků, které se používají. Existuje pět úloh sestavení.

![definice úlohy sestavení](media/cicd/build-definition-tasks.png)

1. **Obnovení** &mdash; Executes `dotnet restore` příkaz k obnovení balíčků NuGet aplikace. Výchozí balíček informační kanál používá je nuget.org.
1. **Sestavení** &mdash; Executes `dotnet build --configuration release` příkaz pro kompilaci kódu aplikace. To `--configuration` možnost se používá k vytvoření optimalizované verzi kódu, který je vhodný pro nasazení do produkčního prostředí. Upravit *BuildConfiguration* proměnné na definici sestavení **proměnné** kartu podle potřeby, například konfigurace ladění je.
1. **Test** &mdash; Executes `dotnet test --configuration release --logger trx --results-directory <local_path_on_build_agent>` příkaz pro spuštění testů jednotek aplikace. Jednotkové testy jsou spouštěny v rámci jakékoli C# projekt odpovídající `**/*Tests/*.csproj` glob vzor. Výsledky testu jsou uloženy v *.trx* soubor v místě určeném `--results-directory` možnost. Pokud selžou i všechny testy, sestavení selže a není nasazený.

    > [!NOTE]
    > Chcete-li ověřit pracovní jednotky testů, upravte *SimpleFeedReader.Tests\Services\NewsServiceTests.cs* záměrně přerušení jednoho z testů. Například změnit `Assert.True(result.Count > 0);` k `Assert.False(result.Count > 0);` v `Returns_News_Stories_Given_Valid_Uri` metody. Potvrďte a odešlete změny na Githubu. Sestavení se aktivuje a selže. Stav kanálu sestavení se změní na **nepovedlo**. Vrácení změn, potvrzení a nabízených oznámení znovu. Sestavení úspěšné.

1. **Publikování** &mdash; Executes `dotnet publish --configuration release --output <local_path_on_build_agent>` příkazu *ZIP* soubor s artefakty, které mají být nasazeny. `--output` Určuje umístění pro publikování aplikace *ZIP* souboru. Zda je zadáno umístění předáním [předdefinované proměnné](/azure/devops/pipelines/build/variables) s názvem `$(build.artifactstagingdirectory)`. Tato proměnná rozšíří na místní cestu, například *c:\agent\_work\1\a*, agenta sestavení.
1. **Publikování artefaktů** &mdash; Publishes *ZIP* vytvářených souborů **publikovat** úloh. Úloha přijímá *ZIP* umístění jako parametr, což je předdefinovaná proměnná souboru `$(build.artifactstagingdirectory)`. *ZIP* soubor je publikován jako složku s názvem *vyřadit*.

Klikněte na definici sestavení **Souhrn** odkaz k zobrazení historie sestavení s definicí:

![Snímek obrazovky znázorňující v historii definic sestavení](media/cicd/build-definition-summary.png)

Na stránce výsledný kliknutím na odkaz odpovídající číslu jedinečný sestavení:

![Snímek obrazovky znázorňující definice stránce se souhrnem sestavení](media/cicd/build-definition-completed.png)

Zobrazí se přehled tohoto konkrétního sestavení. Klikněte na tlačítko **artefakty** kartu a Všimněte si, že *vyřadit* vytvořený sestavením složka se zobrazí:

![Snímek obrazovky s artefakty definice sestavení - odkládací složky](media/cicd/build-definition-artifacts.png)

Použití **Stáhnout** a **prozkoumat** odkazů ke kontrole publikované artefakty.

### <a name="release-pipeline"></a>Kanál pro vydávání verzí

Kanál pro vydávání verzí byl vytvořen s názvem *MyFirstProject ASP.NET Core-CD*:

![Snímek obrazovky znázorňující verze kanálu přehled](media/cicd/release-definition-overview.png)

Jsou dvě hlavní součásti procesu vydávání verzí **artefakty** a **prostředí**. Kliknutím na pole v **artefakty** odhalí panelu následující části:

![Snímek obrazovky znázorňující verze kanálu artefaktů](media/cicd/release-definition-artifacts.png)

**Zdroj (definice sestavení)** hodnota představuje definici sestavení, se kterým je spojen tento kanál pro vydávání verzí. *ZIP* soubor vytvořený úspěšného spuštění definice sestavení se poskytuje *produkční* prostředí pro nasazení do Azure. Klikněte na tlačítko *fáze 1, 2 úlohy* odkaz v *produkční* pole prostředí zobrazíte uvolnění úloh kanálu:

![Snímek obrazovky znázorňující verze kanálu úlohy](media/cicd/release-definition-tasks.png)

Kanál pro vydávání verzí se skládá ze dvou úloh: *nasazení služby Azure App Service do slotu* a *Správa služby Azure App Service – Prohodit Slot*. Kliknutím na první úkol zobrazí následující konfigurace úlohy:

![Úloha nasazení kanálu pro vydávání verzí – snímek obrazovky znázorňující](media/cicd/release-definition-task1.png)

Předplatné Azure, typ služby, název webové aplikace, skupiny prostředků a slot pro nasazení jsou definovány v úlohu nasazení. **Balíčku nebo složky** obsahuje textové pole *ZIP* cesta k souboru extrahována a nasazené do *pracovní* pozici *mywebapp\<jedinečný sez_namu posledních použitých\>*  webové aplikace.

Klepnutím na úkol, slot swap, zobrazí se následující konfigurace úlohy:

![Snímek obrazovky znázorňující uvolnění kanálu slotu prohození úlohy](media/cicd/release-definition-task2.png)

Předplatné, skupinu prostředků, typ služby, název webové aplikace a podrobnosti o slot nasazení jsou k dispozici. **Prohodit s produkčním** zaškrtávací políčko zaškrtnuto. V důsledku toho nasazené bity *pracovní* do produkčního prostředí se Prohodit slot.

## <a name="additional-reading"></a>Další čtení

* [Vytvořit svůj první kanál s kanály Azure](/azure/devops/pipelines/get-started-yaml)
* [Projekt pro sestavení a .NET Core](/azure/devops/pipelines/languages/dotnet-core)
* [Nasazení webové aplikace s Azure kanály](/azure/devops/pipelines/targets/webapp)
