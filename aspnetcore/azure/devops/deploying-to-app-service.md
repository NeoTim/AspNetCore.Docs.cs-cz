---
title: Nasazení aplikace do služby App Service – DevOps s ASP.NET core a Azure
author: CamSoper
description: Nasazení aplikace ASP.NET Core do služby Azure App Service, což je první krok pro DevOps s ASP.NET Core a Azure.
ms.author: casoper
ms.custom: mvc, seodec18
ms.date: 10/24/2018
uid: azure/devops/deploy-to-app-service
ms.openlocfilehash: df41f296e9c4e1eff6e31d45b29ec30ee1e20cf4
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78657743"
---
# <a name="deploy-an-app-to-app-service"></a>Nasazení aplikace do služby App Service

[Azure App Service](/azure/app-service/) je webhostingová platforma Azure. Nasazení webové aplikace do služby Azure App Service lze provést ručně nebo automatizovaným procesem. Tato část příručky popisuje metody nasazení, které lze aktivovat ručně nebo skriptem pomocí příkazového řádku nebo se aktivují ručně pomocí sady Visual Studio.

V této části budete provádět následující úkoly:

* Stáhněte a sestavte ukázkovou aplikaci.
* Vytvořte webovou aplikaci služby Azure App Service pomocí Azure Cloud Shell.
* Nasaďte ukázkovou aplikaci do Azure pomocí Gitu.
* Nasaďte změnu do aplikace pomocí Visual Studia.
* Přidejte pracovní slot do webové aplikace.
* Nasaďte aktualizaci do pracovního slotu.
* Prohoďte přípravný a produkční slot.

## <a name="download-and-test-the-app"></a>Stažení a testování aplikace

Aplikace použitá v této příručce je předem vytvořená aplikace ASP.NET Core, [Simple Feed Reader](https://github.com/Azure-Samples/simple-feed-reader/). Je to aplikace Razor Pages, `Microsoft.SyndicationFeed.ReaderWriter` která používá rozhraní API k načtení informačního kanálu RSS/Atom a zobrazení novinek v seznamu.

Nebojte se přečíst kód, ale je důležité si uvědomit, že na této aplikaci není nic zvláštního. Je to jen jednoduchá aplikace ASP.NET Core pro ilustrační účely.

Z příkazového prostředí stáhněte kód, vytvořte projekt a spusťte jej následujícím způsobem.

> *Poznámka: Uživatelé Linuxu/macOS by měli provést příslušné změny pro`/`cesty, například`\`pomocí lomítka ( ) spíše než zpětný lomítko ( ).*

1. Klonujte kód do složky v místním počítači.

    ```console
    git clone https://github.com/Azure-Samples/simple-feed-reader/
    ```

2. Změňte pracovní složku na složku *s jednoduchou čtečkou,* která byla vytvořena.

    ```console
    cd .\simple-feed-reader\SimpleFeedReader
    ```

3. Obnovte balíčky a vytvořte řešení.

    ```dotnetcli
    dotnet build
    ```

4. Spusťte aplikaci.

    ```dotnetcli
    dotnet run
    ```

    ![Příkaz dotnet run je úspěšný.](./media/deploying-to-app-service/dotnet-run.png)

5. Otevřete prohlížeč a přejděte na adresu `http://localhost:5000`. Aplikace umožňuje zadat nebo vložit adresu URL zdroje syndikace a zobrazit seznam diskusních příspěvků.

     ![Aplikace zobrazující obsah informačního kanálu RSS](./media/deploying-to-app-service/app-in-browser.png)

6. Jakmile jste spokojeni, že aplikace funguje správně, vypněte ji stisknutím **ctrl**+**c** v příkazovém prostředí.

## <a name="create-the-azure-app-service-web-app"></a>Vytvoření webové aplikace Služby aplikací Azure

Chcete-li aplikaci nasadit, budete muset vytvořit [webovou aplikaci](/azure/app-service/app-service-web-overview)služby App Service . Po vytvoření webové aplikace se do ní nasadíte z místního počítače pomocí Gitu.

1. Přihlaste se do [prostředí Azure Cloud Shell](https://shell.azure.com/bash). Poznámka: Při prvním přihlášení cloudové prostředí vyzve k vytvoření účtu úložiště pro konfigurační soubory. Přijměte výchozí hodnoty nebo zadejte jedinečný název.

2. Pro následující kroky použijte prostředí Cloud Shell.

    a. Deklarujte proměnnou pro uložení názvu webové aplikace. Název musí být jedinečný, aby mohl být použit ve výchozí adrese URL. Použití `$RANDOM` Bash funkce k vytvoření názvu zaručuje jedinečnost `webappname99999`a výsledky ve formátu .

    ```console
    webappname=mywebapp$RANDOM
    ```

    b. Vytvořte skupinu prostředků. Skupiny prostředků poskytují prostředky k agregaci prostředků Azure, které mají být spravovány jako skupina.

    ```azure-cli
    az group create --location centralus --name AzureTutorial
    ```

    Příkaz `az` vyvolá [příkaz cli Azure](/cli/azure/). ClI lze spustit místně, ale jeho použití v prostředí Cloud Šetří čas a konfiguraci.

    c. Vytvořte plán služby App Service ve vrstvě S1. Plán služby App Service je seskupení webových aplikací, které sdílejí stejnou cenovou úroveň. Úroveň S1 není zdarma, ale je vyžadována pro funkci pracovních slotů.

    ```azure-cli
    az appservice plan create --name $webappname --resource-group AzureTutorial --sku S1
    ```

    d. Vytvořte prostředek webové aplikace pomocí plánu služby App Service ve stejné skupině prostředků.

    ```azure-cli
    az webapp create --name $webappname --resource-group AzureTutorial --plan $webappname
    ```

    e. Nastavte přihlašovací údaje nasazení. Tato pověření nasazení platí pro všechny webové aplikace ve vašem předplatném. V uživatelském jménu nepoužívejte speciální znaky.

    ```azure-cli
    az webapp deployment user set --user-name REPLACE_WITH_USER_NAME --password REPLACE_WITH_PASSWORD
    ```

    f. Nakonfigurujte webovou aplikaci tak, aby přijímala nasazení z místního Gitu a zobrazovala *adresu URL nasazení Gitu*. **Poznámka: Tato adresa URL pro pozdější odkaz**.

    ```azure-cli
    echo Git deployment URL: $(az webapp deployment source config-local-git --name $webappname --resource-group AzureTutorial --query url --output tsv)
    ```

    g. Zobrazí *adresu URL webové aplikace*. Přejděte na tuto adresu URL a podívejte se na prázdnou webovou aplikaci. **Poznámka: Tato adresa URL pro pozdější odkaz**.

    ```console
    echo Web app URL: http://$webappname.azurewebsites.net
    ```

3. Pomocí příkazového prostředí v místním počítači přejděte do složky `.\simple-feed-reader\SimpleFeedReader`projektu webové aplikace (například). Chcete-li nastavit Git tak, aby se přemisťuje na adresu URL nasazení, proveďte následující příkazy:

    a. Přidejte vzdálenou adresu URL do místního úložiště.

    ```console
    git remote add azure-prod GIT_DEPLOYMENT_URL
    ```

    b. Posuňte místní *hlavní* větev do *hlavní* větve *vzdáleného zařízení azure-prod.*

    ```console
    git push azure-prod master
    ```

    Budete vyzváni k zadání přihlašovacích údajů pro nasazení, které jste vytvořili dříve. Sledujte výstup v příkazovém prostředí. Azure staví aplikaci ASP.NET Core na dálku.

4. V prohlížeči přejděte na *adresu URL webové aplikace* a všimněte si, že aplikace byla vytvořena a nasazena. Další změny mohou být potvrzeny do `git commit`místního úložiště Git s . Tyto změny jsou zasunuty `git push` do Azure pomocí předchozího příkazu.

## <a name="deployment-with-visual-studio"></a>Nasazení v sadě Visual Studio

> *Poznámka: Tato část se vztahuje pouze na Windows. Uživatelé Linuxu a macOS by měli provést změnu popsanou v kroku 2 níže. Uložte soubor a potvrďte změnu `git commit`v místním úložišti pomocí aplikace . Nakonec zatlačte `git push`změnu s , jako v první části.*

Aplikace již byla nasazena z příkazového prostředí. K nasazení aktualizace do aplikace použijeme integrované nástroje visual studia. Na pozadí Visual Studio dosahuje stejnou věc jako nástroje příkazového řádku, ale v rámci známého ui sady Visual Studio.

1. Otevřete *soubor SimpleFeedReader.sln* v sadě Visual Studio.
2. V Průzkumníku řešení otevřete *Stránky\Index.cshtml*. Změnit `<h2>Simple Feed Reader</h2>` `<h2>Simple Feed Reader - V2</h2>`na .
3. Stisknutím **klávesy Ctrl**+**Shift**+**B** vytvořte aplikaci.
4. V Průzkumníku řešení klikněte pravým tlačítkem myši na projekt a klikněte na **Publikovat**.

    ![Snímek obrazovky s pravým tlačítkem myši, publikování](./media/deploying-to-app-service/publish.png)
5. Visual Studio můžete vytvořit nový prostředek služby App Service, ale tato aktualizace bude publikována přes existující nasazení. V **dialogovém** okně Vybrat cíl publikování vyberte **službu App Service** ze seznamu vlevo a pak **vyberte Vybrat existující**. Klikněte na **Publikovat**.
6. V dialogovém okně **Služba aplikace** zkontrolujte, že účet Microsoft nebo organizace použitý k vytvoření předplatného Azure se zobrazí v pravém horním okně. Pokud tomu tak není, klikněte na rozevírací soubor a přidejte ho.
7. Zkontrolujte, že je vybráno správné **předplatné** Azure. V **popřípadě zobrazit**vyberte **položku Skupina prostředků**. Rozbalte skupinu prostředků **AzureTutorial** a vyberte existující webovou aplikaci. Klikněte na tlačítko **OK**.

    ![Snímek obrazovky s dialogovým oknem Publikovat službu aplikace](./media/deploying-to-app-service/publish-dialog.png)

Visual Studio sestavuje a nasazuje aplikaci do Azure. Přejděte na adresu URL webové aplikace. Ověřte, zda je změna `<h2>` prvku aktivní.

![Aplikace se změněným názvem](./media/deploying-to-app-service/app-v2.png)

## <a name="deployment-slots"></a>Nasazovací sloty

Sloty pro nasazení podporují přípravu změn bez dopadu na aplikaci spuštěnou v produkčním prostředí. Jakmile je připravená verze aplikace ověřena týmem pro zajištění kvality, produkční a pracovní sloty mohou být vyměněny. Aplikace v pracovní min. je tímto způsobem povýšena na produkční. Následující kroky vytvoří pracovní slot, nasadí některé změny a zamění pracovní slot s produkčním prostředím po ověření.

1. Pokud už nejste přihlášeni, přihlaste se do [prostředí Azure Cloud Shell.](https://shell.azure.com/bash)
2. Vytvořte pracovní slot.

    a. Vytvořte slot nasazení s *názvem pracovní*.

    ```azure-cli
    az webapp deployment slot create --name $webappname --resource-group AzureTutorial --slot staging
    ```

    b. Nakonfigurujte pracovní slot tak, aby používal nasazení z místního Gitu a získal adresu URL **testovacího** nasazení. **Poznámka: Tato adresa URL pro pozdější odkaz**.

    ```azure-cli
    echo Git deployment URL for staging: $(az webapp deployment source config-local-git --name $webappname --resource-group AzureTutorial --slot staging --query url --output tsv)
    ```

    c. Zobrazí adresu URL pracovního slotu. Přejděte na adresu URL a podívejte se na prázdný pracovní slot. **Poznámka: Tato adresa URL pro pozdější odkaz**.

    ```console
    echo Staging web app URL: http://$webappname-staging.azurewebsites.net
    ```

3. V textovém editoru nebo v sadě Visual Studio znovu `<h2>` upravte *pages/index.cshtml* tak, aby element přečte `<h2>Simple Feed Reader - V3</h2>` a uloží soubor.

4. Potvrdíme soubor do místního úložiště Git pomocí stránky **Změny** na kartě *Průzkumník týmu* sady Visual Studio nebo zadáním následujícího příkazového prostředí místního počítače:

    ```console
    git commit -a -m "upgraded to V3"
    ```

5. Pomocí příkazového prostředí místního počítače přidejte adresu URL pracovního nasazení jako vzdálený ovladač Gitu a zatlačte potvrzené změny:

    a. Přidejte vzdálenou adresu URL pro přípravu do místního úložiště Git.

    ```console
    git remote add azure-staging <Git_staging_deployment_URL>
    ```

    b. Posuňte místní *hlavní* větev do *hlavní* větve *vzdáleného azure-staging.*

    ```console
    git push azure-staging master
    ```

    Počkejte, než Azure vytvoří a nasadí aplikaci.

6. Chcete-li ověřit, že V3 byl nasazen do pracovního slotu, otevřete dvě okna prohlížeče. V jednom okně přejděte na původní adresu URL webové aplikace. V druhém okně přejděte na adresu URL pracovní webové aplikace. Produkční adresa URL obsluhuje V2 aplikace. Pracovní adresa URL obsluhuje V3 aplikace.

    ![Snímek obrazovky se srovnáním oken prohlížeče](./media/deploying-to-app-service/ready-to-swap.png)

7. V prostředí Cloud Shell vyměňte ověřený/zahřátý pracovní slot do produkčního prostředí.

    ```azure-cli
    az webapp deployment slot swap --name $webappname --resource-group AzureTutorial --slot staging
    ```

8. Ověřte, zda k prohození došlo aktualizací dvou oken prohlížeče.

    ![Porovnání oken prohlížeče po prohození](./media/deploying-to-app-service/swapped.png)

## <a name="summary"></a>Souhrn

V této části byly dokončeny následující úkoly:

* Stáhli a vytvořili ukázkovou aplikaci.
* Pomocí Azure Cloud Shellu jste vytvořili webovou aplikaci služby Azure App Service.
* Nasazené ukázkové aplikace do Azure pomocí Gitu.
* Nasadí změnu do aplikace pomocí Visual Studia.
* Do webové aplikace byl přidán pracovní slot.
* Nasadí aktualizaci pracovního slotu.
* Vyměnil pracovní a produkční sloty.

V další části se dozvíte, jak vytvořit kanál DevOps s Azure Pipelines.

## <a name="additional-reading"></a>Dodatečné čtení

* [Přehled webových aplikací](/azure/app-service/app-service-web-overview)
* [Vytvoření webové aplikace .NET Core využívající SQL Database ve službě Azure App Service](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)
* [Konfigurace přihlašovacích údajů pro nasazení služby Azure App Service](/azure/app-service/app-service-deployment-credentials)
* [Nastavení přípravných prostředí ve službě Azure App Service](/azure/app-service/web-sites-staged-publishing)
