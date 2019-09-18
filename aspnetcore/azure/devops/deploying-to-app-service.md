---
title: Nasazení aplikace do App Service – DevOps s využitím ASP.NET Core a Azure
author: CamSoper
description: Nasazení aplikace ASP.NET Core do služby Azure App Service, prvním krokem pro vývoj a provoz s ASP.NET Core a Azure.
ms.author: casoper
ms.custom: mvc, seodec18
ms.date: 10/24/2018
uid: azure/devops/deploy-to-app-service
ms.openlocfilehash: df41f296e9c4e1eff6e31d45b29ec30ee1e20cf4
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2019
ms.locfileid: "71080439"
---
# <a name="deploy-an-app-to-app-service"></a>Nasazení aplikace do služby App Service

[Azure App Service](/azure/app-service/) je Azure web hostitelskou platformu. Nasazení webové aplikace do služby Azure App Service můžete provést ručně nebo pomocí automatizovaného procesu. Tato část průvodce popisuje metody nasazení, které můžete aktivovat ručně nebo pomocí příkazového řádku skriptu nebo aktivuje ručně pomocí sady Visual Studio.

V této části budete provádět následující úlohy:

* Stáhněte si a sestavte ukázkové aplikace.
* Vytvoření Azure webové aplikace služby App Service pomocí Azure Cloud Shell.
* Nasazení ukázkové aplikace do Azure pomocí Gitu.
* Nasazení změny do aplikace pomocí sady Visual Studio.
* Přidáte přípravný slot webové aplikace.
* Aktualizace nasazení do přípravného slotu.
* Prohození slotů pracovního a produkčního prostředí.

## <a name="download-and-test-the-app"></a>Stáhněte si a testování aplikace

Aplikace použitá v tomto průvodci je předem připravené aplikace ASP.NET Core, [jednoduchý kanál čtečky](https://github.com/Azure-Samples/simple-feed-reader/). Jedná se o Razor Pages aplikaci, která používá `Microsoft.SyndicationFeed.ReaderWriter` rozhraní API k načtení informačního kanálu RSS/Atom a zobrazení zprávy položek v seznamu.

Nebojte se podívejte kódu, ale je důležité pochopit, že není nic zvláštního o této aplikaci. Pro ilustraci je stejně jednoduché aplikace ASP.NET Core.

Z příkazové okno stáhnout kód, sestavte projekt a spusťte následujícím způsobem.

> *Poznámka: Uživatelé Linux/MacOS by měli provádět vhodné změny cest, např. pomocí lomítka (`/`) místo zpětného lomítka (`\`).*

1. Klonování kódu do složky na místním počítači.

    ```console
    git clone https://github.com/Azure-Samples/simple-feed-reader/
    ```

2. Změnit pracovní složky do *jednoduchý kanálu čtečky* složku, která byla vytvořena.

    ```console
    cd .\simple-feed-reader\SimpleFeedReader
    ```

3. Obnovte balíčky a sestavte řešení.

    ```dotnetcli
    dotnet build
    ```

4. Spusťte aplikaci.

    ```dotnetcli
    dotnet run
    ```

    ![Spusťte příkaz dotnet je úspěšné](./media/deploying-to-app-service/dotnet-run.png)

5. Otevřete prohlížeč a přejděte do `http://localhost:5000`. Aplikace umožňuje zadejte nebo vložte adresu URL informačního kanálu syndikace a zobrazení seznamu položek zpráv.

     ![Aplikace zobrazení obsahu informačního kanálu RSS](./media/deploying-to-app-service/app-in-browser.png)

6. Jakmile budete spokojeni aplikace pracuje správně, vypněte ho stisknutím kombinace kláves **Ctrl**+**C** v příkazovém prostředí.

## <a name="create-the-azure-app-service-web-app"></a>Vytvoření webové aplikace Azure App Service

Pokud chcete nasadit aplikaci, bude nutné k vytvoření služby App Service [webovou aplikaci](/azure/app-service/app-service-web-overview). Po vytvoření webové aplikace nasadíte do něj z místního počítače pomocí Gitu.

1. Přihlaste se k [Azure Cloud Shell](https://shell.azure.com/bash). Poznámka: Při prvním přihlášení Cloud Shell vyzve k vytvoření účtu úložiště pro konfigurační soubory. Přijměte výchozí hodnoty nebo zadejte jedinečný název.

2. Pomocí služby Cloud Shell pro následující kroky.

    a. Deklarujte proměnnou pro uložení název webové aplikace. Název musí být jedinečný pro výchozí adresy URL. Použití `$RANDOM` funkce jazyka Bash pro vytvoření názvu zaručuje jedinečnost a výsledky ve formátu `webappname99999`.

    ```console
    webappname=mywebapp$RANDOM
    ```

    b. Vytvořte skupinu prostředků. Skupiny prostředků umožňují agregovat prostředky Azure, které jde spravovat jako skupinu.

    ```azure-cli
    az group create --location centralus --name AzureTutorial
    ```

    `az` Vyvolá příkaz [rozhraní příkazového řádku Azure](/cli/azure/). Rozhraní příkazového řádku můžete spustit místně, ale použití ve službě Cloud Shell šetří čas a konfigurace.

    c. Vytvoření plánu služby App Service na úrovni S1. Plán služby App Service je seskupení webové aplikace, které sdílejí stejnou cenovou úroveň. Úroveň S1 není zdarma, ale vyžaduje se pro funkci přípravné sloty.

    ```azure-cli
    az appservice plan create --name $webappname --resource-group AzureTutorial --sku S1
    ```

    d. Vytvoření prostředku webové aplikace pomocí plán služby App Service ve stejné skupině prostředků.

    ```azure-cli
    az webapp create --name $webappname --resource-group AzureTutorial --plan $webappname
    ```

    e. Nastavte přihlašovací údaje pro nasazení. Tyto přihlašovací údaje pro nasazení platí pro všechny webové aplikace ve vašem předplatném. Nepoužívejte speciální znaky v uživatelské jméno.

    ```azure-cli
    az webapp deployment user set --user-name REPLACE_WITH_USER_NAME --password REPLACE_WITH_PASSWORD
    ```

    f. Konfigurace webové aplikace tak, aby přijímal nasazení z místního Gitu a zobrazení *adresa URL pro Git nasazení*. **Mějte na paměti tato adresa URL pro pozdější**.

    ```azure-cli
    echo Git deployment URL: $(az webapp deployment source config-local-git --name $webappname --resource-group AzureTutorial --query url --output tsv)
    ```

    g. Zobrazení *adresa URL webové aplikace*. Přejděte na tuto adresu URL a zobrazte prázdnou webovou aplikaci. **Mějte na paměti tato adresa URL pro pozdější**.

    ```console
    echo Web app URL: http://$webappname.azurewebsites.net
    ```

3. Pomocí příkazového prostředí v místním počítači, přejděte do složky projektu webové aplikace (například `.\simple-feed-reader\SimpleFeedReader`). Spusťte následující příkazy a nastavení Gitu tak, aby nabízel na adresu URL nasazení:

    a. Přidáte vzdálené adresy URL do místního úložiště.

    ```console
    git remote add azure-prod GIT_DEPLOYMENT_URL
    ```

    b. Push místní *hlavní* větvit do *azure prod* na vzdálené *hlavní* větve.

    ```console
    git push azure-prod master
    ```

    Budete vyzváni, pro přihlašovací údaje nasazení, který jste vytvořili dříve. Sledujte ve výstupu v příkazovém prostředí. Azure vytvoří aplikace ASP.NET Core vzdáleně.

4. V prohlížeči přejděte *adresa URL webové aplikace* a poznamenejte si aplikace byla sestavena a nasadit. Další změny mohla být zapsána do místního úložiště Git s `git commit`. Tyto změny jsou vloženy do Azure s předchozím `git push` příkazu.

## <a name="deployment-with-visual-studio"></a>Nasazení pomocí sady Visual Studio

> *Poznámka: Tato část se vztahuje pouze na systém Windows. Uživatelé Linuxu a macOS by měl provést změnu je popsáno v kroku 2 níže. Soubor uložte a potvrďte změnu do místního úložiště s `git commit`. Nakonec push změny s `git push`, protože v první části.*

Z příkazového okna již byla nasazena aplikace. S použitím integrovaných nástrojů sady Visual Studio nasadit aktualizace do aplikace. Na pozadí Visual Studio totéž jako nástrojů příkazového řádku, ale v rámci známé uživatelské rozhraní sady Visual Studio.

1. Otevřít *SimpleFeedReader.sln* v sadě Visual Studio.
2. V Průzkumníku řešení otevřete *Pages\Index.cshtml*. Změna `<h2>Simple Feed Reader</h2>` k `<h2>Simple Feed Reader - V2</h2>`.
3. Stisknutím klávesy **Ctrl**+**Shift**+**B** k sestavení aplikace.
4. V Průzkumníku řešení klikněte pravým tlačítkem na projekt a klikněte na tlačítko **publikovat**.

    ![Snímek obrazovky znázorňující kliknutí pravým tlačítkem myši, publikovat](./media/deploying-to-app-service/publish.png)
5. Visual Studio můžete vytvořit nový prostředek služby App Service, ale tato aktualizace bude publikován přes existující nasazení. V **vyberte cíl publikování** dialogového okna, vyberte **služby App Service** ze seznamu na levé straně a pak vyberte **vybrat existující**. Klikněte na tlačítko **publikovat**.
6. V **služby App Service** dialogovém okně potvrďte, že Microsoft nebo účet organizace použitý při vytvoření vašeho předplatného Azure se zobrazí v pravém horním rohu. Pokud není, klikněte na rozevírací seznam a přidejte ji.
7. Ujistěte se, že správné Azure **předplatné** zaškrtnuto. Pro **zobrazení**vyberte **skupiny prostředků**. Rozbalte **AzureTutorial** skupinu prostředků a potom vyberte existující webovou aplikaci. Klikněte na **OK**.

    ![Snímek obrazovky zobrazující dialogovém okně Publikovat App Service](./media/deploying-to-app-service/publish-dialog.png)

Visual Studio vytvoří a nasadí aplikaci do Azure. Přejděte na adresu URL webové aplikace. Ověřit, zda `<h2>` úpravu elementu je v provozu.

![Aplikaci se změnili jsme název](./media/deploying-to-app-service/app-v2.png)

## <a name="deployment-slots"></a>Nasazovací sloty

Sloty nasazení podporují pracovní změny bez dopadu na aplikace běžící v produkčním prostředí. Jakmile tým pro zajištění kvality vyhodnocuje připravenou verzi aplikace, je možné Prohodit produkční a přípravné sloty. Aplikace v testovacím prostředí je propagována do produkčního prostředí tímto způsobem. Následující kroky vytvořit přípravný slot, nasadíme do ní nějaké změny a Prohodit s produkčním prostředí po ověření přípravný slot.

1. Přihlaste se k [Azure Cloud Shell](https://shell.azure.com/bash), pokud ještě nejste přihlášení.
2. Vytvořte přípravný slot.

    a. Vytvoří slot nasazení s názvem *pracovní*.

    ```azure-cli
    az webapp deployment slot create --name $webappname --resource-group AzureTutorial --slot staging
    ```

    b. Konfigurace přípravného slotu nasazení z místního Gitu a získejte používat **pracovní** adresa URL nasazení. **Mějte na paměti tato adresa URL pro pozdější**.

    ```azure-cli
    echo Git deployment URL for staging: $(az webapp deployment source config-local-git --name $webappname --resource-group AzureTutorial --slot staging --query url --output tsv)
    ```

    c. Zobrazte adresu URL přípravný slot. Přejděte na adresu URL, pokud chcete zobrazit prázdné přípravný slot. **Mějte na paměti tato adresa URL pro pozdější**.

    ```console
    echo Staging web app URL: http://$webappname-staging.azurewebsites.net
    ```

3. V textovém editoru nebo sadě Visual Studio, upravte *Pages/Index.cshtml* znovu tak, aby `<h2>` prvek čte `<h2>Simple Feed Reader - V3</h2>` a soubor uložte.

4. Potvrdit do místního úložiště Git, a to buď pomocí souboru **změny** stránky v sadě Visual Studio *Team Exploreru* kartu, nebo tak, že zadáte následující pomocí příkazového prostředí služby místním počítači:

    ```console
    git commit -a -m "upgraded to V3"
    ```

5. Pomocí příkazového prostředí služby místním počítači, přidejte adresu URL pracovní nasazení jako vzdálené úložiště Git a push potvrzené změny:

    a. Přidáte vzdálené adresy URL pro pracovní do místního úložiště Git.

    ```console
    git remote add azure-staging <Git_staging_deployment_URL>
    ```

    b. Push místní *hlavní* větvit do *azure pracovní* na vzdálené *hlavní* větve.

    ```console
    git push azure-staging master
    ```

    Počkejte, Azure vytvoří a nasadí aplikaci.

6. Chcete-li ověřit, že V3 nasazení do přípravného slotu, otevřete dvě okna prohlížeče. V jednom okně přejděte na adresu URL původní webové aplikace. V druhém okně přejděte na adresu URL pracovní webové aplikace. Adresa URL výroby slouží V2 aplikace. Přípravnou adresu URL slouží V3 aplikace.

    ![Snímek obrazovky okna prohlížeče porovnání](./media/deploying-to-app-service/ready-to-swap.png)

7. Ve službě Cloud Shell Prohodit slot pro fázi ověření/zahřátého do produkčního prostředí.

    ```azure-cli
    az webapp deployment slot swap --name $webappname --resource-group AzureTutorial --slot staging
    ```

8. Ověřte, že došlo k prohození aktualizací okna prohlížeče dvě.

    ![Porovnání okna prohlížeče po prohození](./media/deploying-to-app-service/swapped.png)

## <a name="summary"></a>Souhrn

V této části dokončili jste následující úlohy:

* Stáhnout a sestaven ukázkovou aplikaci.
* Vytvořit Azure webové aplikace služby App Service pomocí Azure Cloud Shell.
* Nasazení ukázkové aplikace do Azure pomocí Gitu.
* Změna nasazené do aplikace pomocí sady Visual Studio.
* Přidat přípravný slot webové aplikace.
* Aktualizace nasadit do přípravného slotu.
* Prohodit sloty přípravným a produkčním prostředím.

V další části se dozvíte, jak vytvořit kanál DevOps s kanály Azure.

## <a name="additional-reading"></a>Další čtení

* [Přehled Web Apps](/azure/app-service/app-service-web-overview)
* [Vytvoření webové aplikace .NET Core využívající SQL Database ve službě Azure App Service](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)
* [Nakonfigurujte přihlašovací údaje nasazení pro službu Azure App Service](/azure/app-service/app-service-deployment-credentials)
* [Nastavení přípravných prostředí ve službě Azure App Service](/azure/app-service/web-sites-staged-publishing)
