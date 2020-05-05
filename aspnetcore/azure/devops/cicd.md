---
title: Průběžná integrace a nasazování – DevOps s využitím ASP.NET Core a Azure
author: CamSoper
description: Průběžná integrace a nasazování v DevOps s využitím ASP.NET Core a Azure
ms.author: scaddie
ms.date: 10/24/2018
ms.custom: mvc, seodec18
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: azure/devops/cicd
ms.openlocfilehash: f5b0e0ee1c903de26188815c7dc01ed547cca97e
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82767132"
---
# <a name="continuous-integration-and-deployment"></a>Průběžná integrace a nasazování

V předchozí kapitole jste pro aplikaci pro čtení jednoduchých informačních kanálů vytvořili místní úložiště Git. V této kapitole publikujete tento kód do úložiště GitHub a vytvoříte kanál Azure DevOps Services pomocí Azure Pipelines. Kanál umožňuje průběžné sestavení a nasazení aplikace. Jakékoli potvrzení do úložiště GitHubu aktivuje sestavení a nasazení do přípravného slotu webové aplikace Azure.

V této části provedete následující úlohy:

* Publikování kódu aplikace do GitHubu
* Odpojit nasazení místního Gitu
* Vytvoření organizace Azure DevOps
* Vytvoření týmového projektu v Azure DevOps Services
* Vytvoření definice sestavení
* Vytvoření kanálu verze
* Potvrzení změn na GitHubu a automatické nasazení do Azure
* Kontrola kanálu Azure Pipelines

## <a name="publish-the-apps-code-to-github"></a>Publikování kódu aplikace do GitHubu

1. Otevřete okno prohlížeče a přejděte na `https://github.com`.
1. V záhlaví **+** klikněte na rozevírací nabídku a vyberte **nové úložiště**:

    ![Možnost nového úložiště GitHubu](media/cicd/github-new-repo.png)

1. V rozevíracím seznamu **vlastník** vyberte svůj účet a do textového pole **název úložiště** zadejte *Simple-feed-Reader* .
1. Klikněte na tlačítko **vytvořit úložiště** .
1. Otevřete příkazové prostředí místního počítače. Přejděte do adresáře, ve kterém je uloženo úložiště Git s *jednoduchým kanálem pro čtení* .
1. Přejmenujte stávající vzdálený *zdroj* na *nadřazený*. Spusťte následující příkaz:

    ```console
    git remote rename origin upstream
    ```

1. Přidejte na GitHubu nového *zdroje* , který se vzdáleně odkazuje na vaši kopii úložiště. Spusťte následující příkaz:

    ```console
    git remote add origin https://github.com/<GitHub_username>/simple-feed-reader/
    ```

1. Publikujte místní úložiště Git do nově vytvořeného úložiště GitHub. Spusťte následující příkaz:

    ```console
    git push -u origin master
    ```

1. Otevřete okno prohlížeče a přejděte na `https://github.com/<GitHub_username>/simple-feed-reader/`. Ověřte, že se váš kód zobrazuje v úložišti GitHubu.

## <a name="disconnect-local-git-deployment"></a>Odpojit nasazení místního Gitu

Odeberte místní nasazení Gitu pomocí následujících kroků. Azure Pipelines (služba Azure DevOps) nahrazuje i rozšiřuje tuto funkčnost.

1. Otevřete [Azure Portal](https://portal.azure.com/)a přejděte do pracovní webové aplikace *(MyWebApp\<\>unique_number/staging)* . Webovou aplikaci můžete rychle najít tak, že do vyhledávacího pole portálu zadáte *fázování* :

    ![pracovní termín pro hledání webové aplikace](media/cicd/portal-search-box.png)

1. Klikněte na **centrum nasazení**. Zobrazí se nový panel. Kliknutím na **Odpojit** odeberte konfiguraci místního řízení zdrojů Git, která se přidala do předchozí kapitoly. Kliknutím na tlačítko **Ano** potvrďte operaci odebrání.
1. Přejděte do *mywebapp<unique_number>* App Service. Jako připomenutí můžete použít vyhledávací pole na portálu k rychlému vyhledání App Service.
1. Klikněte na **centrum nasazení**. Zobrazí se nový panel. Kliknutím na **Odpojit** odeberte konfiguraci místního řízení zdrojů Git, která se přidala do předchozí kapitoly. Kliknutím na tlačítko **Ano** potvrďte operaci odebrání.

## <a name="create-an-azure-devops-organization"></a>Vytvoření organizace Azure DevOps

1. Otevřete prohlížeč a přejděte na [stránku pro vytvoření organizace Azure DevOps](https://go.microsoft.com/fwlink/?LinkId=307137).
1. Do textového pole **Vybrat zapamatovatelné jméno** zadejte jedinečný název, který bude tvořit adresu URL pro přístup ke svojí organizaci Azure DevOps.
1. Vyberte přepínač **Git** , protože je tento kód hostovaný v úložišti GitHubu.
1. Klikněte na tlačítko **pokračovat** . Po krátkém čekání se vytvoří účet a týmový projekt s názvem *MyFirstProject*.

    ![Stránka pro vytvoření organizace Azure DevOps](media/cicd/vsts-account-creation.png)

1. Otevřete potvrzovací e-mail s oznámením, že organizace a projekt Azure DevOps jsou připravené k použití. Klikněte na tlačítko **spustit projekt** :

    ![Tlačítko spustit projekt](media/cicd/vsts-start-project.png)

1. Otevře se prohlížeč * \<\>account_name. VisualStudio.com*. Kliknutím na odkaz *MyFirstProject* zahajte konfiguraci kanálu DevOps projektu.

## <a name="configure-the-azure-pipelines-pipeline"></a>Konfigurace kanálu Azure Pipelines

Existují tři samostatné kroky pro dokončení. Provedením kroků v následujících třech částech dojde k DevOps kanálu provozní služby.

### <a name="grant-azure-devops-access-to-the-github-repository"></a>Udělení přístupu Azure DevOps k úložišti GitHubu

1. Rozbalte **nebo sestavte kód z přidaných externích úložišť** . Klikněte na tlačítko **sestavení pro instalaci** :

    ![Tlačítko pro sestavení instalace](media/cicd/vsts-setup-build.png)

1. V části **Vybrat zdroj** vyberte možnost **GitHub** :

    ![Vybrat zdroj – GitHub](media/cicd/vsts-select-source.png)

1. Aby mohl Azure DevOps získat přístup k úložišti GitHubu, vyžaduje se autorizace. Do textového pole **název připojení** zadejte *<GitHub_username> připojení GitHubu* . Příklad:

    ![Název připojení GitHubu](media/cicd/vsts-repo-authz.png)

1. Pokud je ve vašem účtu GitHub povolený dvojúrovňové ověřování, vyžaduje se osobní přístupový token. V takovém případě klikněte na odkaz **autorizovat pomocí osobního přístupového tokenu GitHubu** . Nápovědu najdete v [oficiálních pokynech k vytvoření tokenu GitHubu pro GitHub](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/) . Je potřeba jenom obor *úložiště* oprávnění. V opačném případě klikněte na tlačítko **autorizovat pomocí OAuth** .
1. Po zobrazení výzvy se přihlaste ke svému účtu GitHub. Pak vyberte autorizovat pro udělení přístupu ke svojí organizaci Azure DevOps. V případě úspěchu se vytvoří nový koncový bod služby.
1. Klikněte na tlačítko se třemi tečkami vedle tlačítka **úložiště** . V seznamu vyberte *<GitHub_username> úložiště/Simple-feed-Reader* . Klikněte na tlačítko **Vybrat** .
1. Vyberte *Hlavní* větev z rozevíracího seznamu **výchozí větev pro ruční a plánovaná sestavení** . Klikněte na tlačítko **pokračovat** . Zobrazí se stránka Výběr šablony.

### <a name="create-the-build-definition"></a>Vytvoření definice sestavení

1. Na stránce Výběr šablony zadejte do vyhledávacího pole *ASP.NET Core* :

    ![ASP.NET Core hledání na stránce šablony](media/cicd/vsts-template-selection.png)

1. Zobrazí se výsledky hledání šablony. Najeďte myší na šablonu **ASP.NET Core** a klikněte na tlačítko **použít** .
1. Zobrazí se karta **úkoly** v definici sestavení. Klikněte na kartu **triggery** .
1. Zaškrtněte políčko **Povolit průběžnou integraci** . V části **filtry větví** potvrďte, že rozevírací seznam **typ** je nastavený na *Zahrnout*. Nastavte rozevírací seznam **specifikace větve** na *hlavní*.

    ![Povolit nastavení nepřetržité integrace](media/cicd/vsts-enable-ci.png)

    Tato nastavení způsobí, že se sestavení aktivuje při vložení jakékoli změny do *Hlavní* větve úložiště GitHub. Nepřetržitá integrace je testována v části [Potvrdit změny do GitHubu a automaticky se nasazuje do Azure](#commit-changes-to-github-and-automatically-deploy-to-azure) .

1. Klikněte na tlačítko **uložit & frontu** a vyberte možnost **Uložit** :

    ![Tlačítko Uložit](media/cicd/vsts-save-build.png)

1. Zobrazí se následující modální dialogové okno:

    ![Uložit definici sestavení – modální dialogové okno](media/cicd/vsts-save-modal.png)

    Použijte výchozí složku *\\*a klikněte na tlačítko **Uložit** .

### <a name="create-the-release-pipeline"></a>Vytvoření kanálu pro vydávání verzí

1. Klikněte na kartu **vydání** v týmovém projektu. Klikněte na tlačítko **Nový kanál** .

    ![Karta vydání – tlačítko Nová definice](media/cicd/vsts-new-release-definition.png)

    Zobrazí se podokno výběr šablony.

1. Na stránce Výběr šablony zadejte do vyhledávacího pole *App Service* :

    ![Vyhledávací pole šablony kanálu vydání](media/cicd/vsts-release-template-search.png)

1. Zobrazí se výsledky hledání šablony. Najeďte myší na **nasazení Azure App Service se šablonou slotu** a klikněte na tlačítko **použít** . Zobrazí se karta **kanál** kanálu vydání.

    ![Karta kanálu vydaných verzí](media/cicd/vsts-release-definition-pipeline.png)

1. Klikněte na tlačítko **Přidat** v poli **artefakty** . Zobrazí se panel **Přidat artefakt** :

    ![Kanál verze – přidat panel artefaktu](media/cicd/vsts-release-add-artifact.png)

1. Vyberte dlaždici **sestavení** z oddílu **typ zdroje** . Tento typ umožňuje propojit kanál verze s definicí sestavení.
1. V rozevíracím seznamu **projekt** vyberte *MyFirstProject* .
1. Z rozevíracího seznamu **zdroj (definice sestavení)** vyberte název definice sestavení, *MYFIRSTPROJECT-ASP.NET Core-CI*.
1. Z rozevíracího seznamu **výchozí verze** vyberte *nejnovější* . Tato možnost sestaví artefakty vytvořené pomocí posledního spuštění definice sestavení.
1. Text v textovém poli **aliasu zdroje** nahraďte textem *drop*.
1. Klikněte na tlačítko **Přidat** . Část **artefakty** aktualizuje, aby se zobrazily změny.
1. Pokud chcete povolit nepřetržitá nasazení, klikněte na ikonu bleskového šroubu:

    ![Artefakty kanálu vydání – Ikona blesku](media/cicd/vsts-artifacts-lightning-bolt.png)

    Pokud je tato možnost povolena, nasazení probíhá pokaždé, když je k dispozici nové sestavení.
1. Napravo se zobrazí panel **Trigger průběžného nasazování** . Pro povolení funkce klikněte na přepínací tlačítko. Není nutné povolit **Trigger žádosti o získání dat**.
1. V části **filtry větve sestavení** klikněte na rozevírací nabídku **Přidat** . Vyberte možnost **výchozí větev definice sestavení** . Tento filtr způsobí, že se vydaná verze aktivuje jenom pro sestavení z *Hlavní* větve úložiště GitHub.
1. Klikněte na tlačítko **Uložit**. Klikněte na tlačítko **OK** v dialogovém okně výsledný dialog **Uložit** modální.
1. Klikněte na pole **prostředí 1** . Napravo se zobrazí panel **prostředí** . Změňte text *prostředí 1* v textovém poli **Název prostředí** na *produkční*.

   ![Kanál vydané verze – textové pole názvu prostředí](media/cicd/vsts-environment-name-textbox.png)

1. V **produkčním** poli klikněte na odkaz **1 fáze, 2 úlohy** :

    ![Kanál verze – propojení produkčního prostředí. png](media/cicd/vsts-production-link.png)

    Zobrazí se karta **úlohy** v prostředí.
1. Klikněte na úlohu **nasadit Azure App Service pro slot** . Jeho nastavení se zobrazí na panelu vpravo.
1. Z rozevíracího seznamu **předplatné Azure** vyberte předplatné Azure přidružené k tomuto App Service. Po výběru klikněte na tlačítko **autorizovat** .
1. V rozevíracím seznamu **Typ aplikace** vyberte možnost *Webová aplikace* .
1. V rozevíracím seznamu **název služby App Service** vyberte *mywebapp/<unique_number/>* .
1. V rozevíracím seznamu **Skupina prostředků** vyberte *AzureTutorial* .
1. V rozevíracím seznamu **slot** vyberte *fázování* .
1. Klikněte na tlačítko **Uložit**.
1. Najeďte myší na výchozí název kanálu vydaných verzí. Klikněte na ikonu tužky a upravte ji. Jako název použijte *MyFirstProject-ASP.NET Core-CD* .

    ![Název kanálu verze](media/cicd/vsts-release-definition-name.png)

1. Klikněte na tlačítko **Uložit**.

## <a name="commit-changes-to-github-and-automatically-deploy-to-azure"></a>Potvrzení změn na GitHubu a automatické nasazení do Azure

1. Otevřete *SimpleFeedReader. sln* v aplikaci Visual Studio.
1. V Průzkumník řešení otevřete *Pages\Index.cshtml*. Změňte `<h2>Simple Feed Reader - V3</h2>` na `<h2>Simple Feed Reader - V4</h2>`.
1. Stisknutím **kombinace kláves CTRL**+**+ SHIFT**+**B** sestavíte aplikaci.
1. Potvrďte soubor do úložiště GitHub. Na kartě *Team Explorer* v aplikaci Visual Studio použijte stránku **změny** nebo pomocí příkazového prostředí místního počítače spusťte následující příkaz:

    ```console
    git commit -a -m "upgraded to V4"
    ```

1. Nahrajte změnu v *Hlavní* větvi do *počátečního* vzdáleného úložiště GitHubu:

    ```console
    git push origin master
    ```

    Potvrzení se zobrazí v *Hlavní* větvi úložiště GitHub:

    ![Potvrzení GitHubu v hlavní větvi](media/cicd/github-commit.png)

    Sestavení je aktivováno, protože je povolena průběžná integrace na kartě **aktivační události** definice sestavení:

    ![Povolit kontinuální integraci](media/cicd/enable-ci.png)

1. Na stránce **Azure Pipelines** > **sestavení** v Azure DevOps Services přejděte na kartu **zařazené do fronty** . Sestavení ve frontě zobrazuje větev a potvrzení, které spustilo sestavení:

    ![sestavení zařazené do fronty](media/cicd/build-queued.png)

1. Po úspěšném sestavení dojde k nasazení do Azure. V prohlížeči přejděte do aplikace. Všimněte si, že se v záhlaví zobrazí text "v4":

    ![aktualizovaná aplikace](media/cicd/updated-app-v4.png)

## <a name="examine-the-azure-pipelines-pipeline"></a>Kontrola kanálu Azure Pipelines

### <a name="build-definition"></a>Definice sestavení

Byla vytvořena definice sestavení s názvem *MyFirstProject-ASP.NET Core-CI*. Po dokončení sestavení vytvoří soubor *. zip* , včetně prostředků, které mají být publikovány. Kanál pro vydávání verzí tyto prostředky nasadí do Azure.

Na kartě **úkoly** definice sestavení jsou uvedeny jednotlivé kroky, které se používají. Existuje pět úkolů sestavení.

![úkoly definice sestavení](media/cicd/build-definition-tasks.png)

1. Při **obnovení** &mdash; se `dotnet restore` spustí příkaz pro obnovení balíčků NuGet aplikace. Výchozí použitý kanál balíčku je nuget.org.
1. Při **sestavení** &mdash; se `dotnet build --configuration release` spustí příkaz pro zkompilování kódu aplikace. Tato `--configuration` možnost slouží k vytvoření optimalizované verze kódu, který je vhodný pro nasazení do produkčního prostředí. Pokud je například nutná konfigurace ladění, upravte proměnnou *BuildConfiguration* na kartě **proměnné** v definici sestavení.
1. **Test** &mdash; spustí `dotnet test --configuration release --logger trx --results-directory <local_path_on_build_agent>` příkaz pro spuštění testů jednotek aplikace. Testy jednotek se spustí v rámci libovolného projektu C# odpovídajícího `**/*Tests/*.csproj` vzoru glob. Výsledky testů jsou uloženy v souboru *. TRX* v umístění určeném `--results-directory` možností. Pokud dojde k selhání nějakého testu, sestavení se nezdaří a není nasazeno.

    > [!NOTE]
    > Chcete-li ověřit testy jednotek, upravte *SimpleFeedReader. Tests\Services\NewsServiceTests.cs* tak, aby záměrně přerušení jednoho testu. Například změňte `Assert.True(result.Count > 0);` na `Assert.False(result.Count > 0);` v `Returns_News_Stories_Given_Valid_Uri` metodě. Potvrďte a nahrajte změnu do GitHubu. Sestavování se aktivuje a dojde k chybě. Stav kanálu sestavení se změní na **neúspěch**. Obnovte změnu, potvrďte akci a znovu ji nasaďte. Sestavení je úspěšné.

1. **Publikování** &mdash; spustí `dotnet publish --configuration release --output <local_path_on_build_agent>` příkaz pro vytvoření souboru *. zip* s artefakty, které mají být nasazeny. `--output` Možnost určuje umístění publikování souboru *. zip* . Toto umístění je určeno předáním [předdefinované proměnné](/azure/devops/pipelines/build/variables) s názvem `$(build.artifactstagingdirectory)`. Tato proměnná se rozbalí na místní cestu, například *c:\Agent\_work\1\a*, v agentovi sestavení.
1. Při **publikování artefaktu** &mdash; se publikuje soubor *. zip* vytvářený úlohou **Publish** . Úkol přijme umístění souboru *. zip* jako parametr, což je předdefinovaná proměnná `$(build.artifactstagingdirectory)`. Soubor *. zip* je publikován jako složka s názvem *drop*.

Kliknutím na **souhrnný** odkaz definice sestavení zobrazíte historii sestavení s definicí:

![Snímek obrazovky zobrazující historii definice sestavení](media/cicd/build-definition-summary.png)

Na výsledné stránce klikněte na odkaz odpovídající jedinečnému číslu sestavení:

![Snímek obrazovky se stránkou souhrnu definice sestavení](media/cicd/build-definition-completed.png)

Zobrazí se souhrn tohoto konkrétního sestavení. Klikněte na kartu **artefakty** a Všimněte si, že je uvedena *ukládací* složka vytvořená sestavením:

![Snímek obrazovky znázorňující artefakty definice sestavení – odkládací složka](media/cicd/build-definition-artifacts.png)

Pomocí odkazů **Stáhnout** a **prozkoumat** můžete zkontrolovat publikované artefakty.

### <a name="release-pipeline"></a>Kanál verze

Byl vytvořen kanál vydané verze s názvem *MyFirstProject-ASP.NET Core-CD*:

![Snímek obrazovky s přehledem kanálu vydání](media/cicd/release-definition-overview.png)

Mezi dvě hlavní součásti kanálu pro vydávání verzí patří **artefakty** a **prostředí**. Kliknutím na pole v části **artefakty** se odhalí následující panel:

![Snímek obrazovky zobrazující artefakty kanálu vydaných verzí](media/cicd/release-definition-artifacts.png)

Hodnota **zdroj (definice sestavení)** představuje definici sestavení, ke které je tento kanál verze propojený. Soubor *. zip* , který vygenerovalo úspěšné spuštění definice sestavení, je poskytován *provoznímu* prostředí pro nasazení do Azure. Úkoly kanálu vydaných verzí zobrazíte kliknutím na odkaz *1 fáze, 2 úlohy* v poli *provozní* prostředí:

![Snímek obrazovky znázorňující úlohy kanálu vydání](media/cicd/release-definition-tasks.png)

Kanál vydané verze se skládá ze dvou úloh: *nasazení Azure App Service do slotu* a *správa swapu Azure App Serviceho slotu*. Kliknutím na první úlohu se zobrazí následující konfigurace úlohy:

![Snímek obrazovky s úlohou nasazení kanálu vydaných verzí](media/cicd/release-definition-task1.png)

V úloze nasazení je definováno předplatné Azure, typ služby, název webové aplikace, skupina prostředků a slot nasazení. Textové pole **balíčku nebo složky** obsahuje cestu k souboru *. zip* , která se má extrahovat a nasadit do *přípravného* slotu *\<unique_number\> * webové aplikace MyWebApp.

Kliknutím na úlohu přepnutí slotu se odhalí Tato konfigurace úlohy:

![Snímek obrazovky s úlohou přepnutí slotu pro uvolnění kanálu](media/cicd/release-definition-task2.png)

K dispozici jsou údaje o předplatném, skupině prostředků, typu služby, názvu webové aplikace a slotu nasazení. Zaškrtávací políčko **swap s výrobou** je zaškrtnuté. V důsledku toho se bity nasazené do *přípravného* slotu odsadí do produkčního prostředí.

## <a name="additional-reading"></a>Další čtení

* [Vytvoření prvního kanálu pomocí Azure Pipelines](/azure/devops/pipelines/get-started-yaml)
* [Projekt sestavení a .NET Core](/azure/devops/pipelines/languages/dotnet-core)
* [Nasazení webové aplikace pomocí Azure Pipelines](/azure/devops/pipelines/targets/webapp)
