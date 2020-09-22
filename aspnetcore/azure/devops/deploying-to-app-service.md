---
title: Nasazení aplikace pro App Service DevOps s využitím ASP.NET Core a Azure
author: CamSoper
description: Nasaďte aplikaci ASP.NET Core do Azure App Service, což je první krok pro DevOps s ASP.NET Core a Azure.
ms.author: casoper
ms.custom: devx-track-csharp, mvc, seodec18, devx-track-azurecli
ms.date: 10/24/2018
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: azure/devops/deploy-to-app-service
ms.openlocfilehash: e6d8b4bcbbbe909fde971a8c706287654fcc98ba
ms.sourcegitcommit: 62cc131969b2379f7a45c286a751e22d961dfbdb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90847621"
---
# <a name="deploy-an-app-to-app-service"></a>Nasazení aplikace pro App Service

[Azure App Service](/azure/app-service/) platforma pro hostování webů v Azure. Nasazení webové aplikace do Azure App Service lze provést ručně nebo pomocí automatizovaného procesu. Tato část příručky popisuje metody nasazení, které lze aktivovat ručně nebo pomocí skriptu pomocí příkazového řádku nebo aktivované ručně pomocí sady Visual Studio.

V této části budete provádět následující úlohy:

* Stáhněte si a sestavte ukázkovou aplikaci.
* Vytvořte Azure App Service webové aplikace pomocí Azure Cloud Shell.
* Nasaďte ukázkovou aplikaci do Azure pomocí Gitu.
* Nasaďte do aplikace změnu pomocí sady Visual Studio.
* Přidejte do webové aplikace pracovní slot.
* Nasaďte aktualizaci do přípravného slotu.
* Prohoďte přípravný a produkční slot.

## <a name="download-and-test-the-app"></a>Stažení a otestování aplikace

Aplikace použitá v tomto průvodci je předem vytvořená aplikace ASP.NET Core, [Jednoduchá čtečka kanálů](https://github.com/Azure-Samples/simple-feed-reader/). Jedná se o Razor stránku aplikace, která používá `Microsoft.SyndicationFeed.ReaderWriter` rozhraní API k načtení informačního kanálu RSS/Atom a zobrazení položek zpráv v seznamu.

Tento kód si můžete prohlédnout, ale je důležité pochopit, že tato aplikace nemá nic zvláštní. Je to jenom jednoduchá ASP.NET Core aplikace pro ilustrativní účely.

Z příkazového prostředí Stáhněte kód, sestavte projekt a spusťte jej následujícím způsobem.

> *Poznámka: uživatelé systému Linux/macOS by měli provádět vhodné změny cest, např. pomocí lomítka ( `/` ) místo zpětného lomítka ( `\` ).*

1. Naklonujte kód do složky na místním počítači.

    ```console
    git clone https://github.com/Azure-Samples/simple-feed-reader/
    ```

2. Změňte pracovní složku na vytvořenou složku s *jednoduchým kanálem pro čtení* .

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

    ![Příkaz dotnet Run je úspěšný.](./media/deploying-to-app-service/dotnet-run.png)

5. Otevřete prohlížeč a přejděte na adresu `http://localhost:5000`. Aplikace umožňuje zadat nebo vložit adresu URL informačního kanálu syndikace a zobrazit seznam položek zpráv.

     ![Aplikace, která zobrazuje obsah informačního kanálu RSS](./media/deploying-to-app-service/app-in-browser.png)

6. Jakmile budete spokojeni s tím, že aplikace funguje správně, vypněte ji stisknutím **kombinace kláves CTRL +** + **C** v příkazovém prostředí.

## <a name="create-the-azure-app-service-web-app"></a>Vytvoření webové aplikace v Azure App Service

K nasazení aplikace budete muset vytvořit [webovou aplikaci](/azure/app-service/app-service-web-overview)App Service. Po vytvoření webové aplikace ji nasadíte z místního počítače pomocí Gitu.

1. Přihlaste se k [Azure Cloud Shell](https://shell.azure.com/bash). Poznámka: když se poprvé přihlásíte, Cloud Shell se zobrazí výzva k vytvoření účtu úložiště pro konfigurační soubory. Přijměte výchozí hodnoty nebo zadejte jedinečný název.

2. Následující postup použijte Cloud Shell.

    a. Deklarujte proměnnou pro uložení názvu vaší webové aplikace. Název musí být jedinečný, aby bylo možné použít výchozí adresu URL. Použití `$RANDOM` funkce bash k vytvoření názvu garantuje jedinečnost a výsledky ve formátu `webappname99999` .

    ```console
    webappname=mywebapp$RANDOM
    ```

    b. Vytvořte skupinu prostředků. Skupiny prostředků poskytují prostředky pro agregaci prostředků Azure, které se mají spravovat jako skupina.

    ```azurecli
    az group create --location centralus --name AzureTutorial
    ```

    `az`Příkaz vyvolá [Azure CLI](/cli/azure/). Rozhraní příkazového řádku lze spustit místně, ale jeho použití v Cloud Shell šetří čas a konfiguraci.

    c. Vytvořte plán App Service v úrovni S1. Plán App Service je seskupení webových aplikací, které sdílejí stejnou cenovou úroveň. Vrstva S1 není volná, ale vyžaduje se pro funkci přípravného slotu.

    ```azurecli
    az appservice plan create --name $webappname --resource-group AzureTutorial --sku S1
    ```

    d. Vytvořte prostředek webové aplikace pomocí App Serviceho plánu ve stejné skupině prostředků.

    ```azurecli
    az webapp create --name $webappname --resource-group AzureTutorial --plan $webappname
    ```

    e. Nastavte přihlašovací údaje pro nasazení. Tyto přihlašovací údaje pro nasazení platí pro všechny webové aplikace v rámci vašeho předplatného. V uživatelském jméně Nepoužívejte speciální znaky.

    ```azurecli
    az webapp deployment user set --user-name REPLACE_WITH_USER_NAME --password REPLACE_WITH_PASSWORD
    ```

    f. Nakonfigurujte webovou aplikaci tak, aby přijímala nasazení z místního Gitu a zobrazila *adresu URL nasazení Git*. **Poznamenejte si tuto adresu URL pro referenci později**.

    ```azurecli
    echo Git deployment URL: $(az webapp deployment source config-local-git --name $webappname --resource-group AzureTutorial --query url --output tsv)
    ```

    například Zobrazí *adresu URL webové aplikace*. Pokud chcete zobrazit prázdnou webovou aplikaci, přejděte na tuto adresu URL. **Poznamenejte si tuto adresu URL pro referenci později**.

    ```console
    echo Web app URL: http://$webappname.azurewebsites.net
    ```

3. Pomocí příkazového prostředí v místním počítači přejděte do složky projektu webové aplikace (například `.\simple-feed-reader\SimpleFeedReader` ). Spusťte následující příkazy a nastavte Git tak, aby se nastavila na adresu URL nasazení:

    a. Přidejte vzdálenou adresu URL do místního úložiště.

    ```console
    git remote add azure-prod GIT_DEPLOYMENT_URL
    ```

    b. Vložení místní *Hlavní* větve do *hlavní* větve *Azure-prod* Remote 's.

    ```console
    git push azure-prod master
    ```

    Zobrazí se výzva k zadání přihlašovacích údajů pro nasazení, které jste vytvořili dříve. Sledujte výstup v příkazovém prostředí. Azure vytvoří aplikaci ASP.NET Core vzdáleně.

4. V prohlížeči přejděte na *adresu URL webové aplikace* a Všimněte si, že je aplikace sestavená a nasazená. Další změny můžete zapsat do místního úložiště Git pomocí `git commit` . Tyto změny se vloží do Azure pomocí předchozího `git push` příkazu.

## <a name="deployment-with-visual-studio"></a>Nasazení pomocí sady Visual Studio

> *Poznámka: Tato část se vztahuje pouze na systém Windows. Uživatelé Linux a macOS by měli provést změnu popsanou v kroku 2 níže. Uložte soubor a potvrďte změnu v místním úložišti pomocí `git commit` . Nakonec nahrajte změnu pomocí `git push` , jako v první části.*

Aplikace už je nasazená z příkazového prostředí. Pojďme k nasazení aktualizace do aplikace použít integrované nástroje sady Visual Studio. Aplikace Visual Studio na pozadí dosáhne stejného účelu jako nástroj příkazového řádku, ale ve známém uživatelském rozhraní sady Visual Studio.

1. Otevřete *SimpleFeedReader. sln* v aplikaci Visual Studio.
2. V Průzkumník řešení otevřete *Pages\Index.cshtml*. Změňte `<h2>Simple Feed Reader</h2>` na `<h2>Simple Feed Reader - V2</h2>` .
3. Stisknutím **kombinace kláves CTRL** + **+ SHIFT** + **B** sestavíte aplikaci.
4. V Průzkumník řešení klikněte pravým tlačítkem na projekt a klikněte na **publikovat**.

    ![Snímek obrazovky, který ukazuje kliknutí pravým tlačítkem, publikování](./media/deploying-to-app-service/publish.png)
5. Visual Studio může vytvořit nový prostředek App Service, ale tato aktualizace se publikuje v rámci stávajícího nasazení. V dialogovém okně **vybrat cíl publikování** vyberte v seznamu na levé straně položku **App Service** a pak vyberte **Vybrat existující**. Klikněte na **Publikovat**.
6. V dialogovém okně **App Service** potvrďte, že se v pravém horním rohu zobrazuje účet Microsoft nebo organizace, který se používá k vytvoření vašeho předplatného Azure. Pokud není, klikněte na rozevírací nabídku a přidejte ji.
7. Zkontrolujte, jestli je vybrané správné **předplatné** Azure. V **zobrazení**vyberte **Skupina prostředků**. Rozbalte skupinu prostředků **AzureTutorial** a pak vyberte existující webovou aplikaci. Klikněte na **OK**.

    ![Snímek obrazovky s dialogem App Service pro publikování](./media/deploying-to-app-service/publish-dialog.png)

Visual Studio sestaví a nasadí aplikaci do Azure. Přejděte na adresu URL webové aplikace. Ověřte, zda `<h2>` je úprava prvku živá.

![Aplikace se změněným názvem](./media/deploying-to-app-service/app-v2.png)

## <a name="deployment-slots"></a>Nasazovací sloty

Sloty nasazení podporují přípravu změn bez ovlivnění aplikace spuštěné v produkčním prostředí. Jakmile se dvoufázové verze aplikace ověří týmem zabezpečování kvality, produkční a přípravné sloty je možné prohodit. Aplikace v pracovním prostředí se tímto způsobem převýší na produkční prostředí. V následujících krocích se vytvoří přípravný slot, nasadí se nějaké změny a pracovní slot se po ověření zahodí k produkci.

1. Přihlaste se k [Azure Cloud Shell](https://shell.azure.com/bash), pokud ještě není přihlášený.
2. Vytvořte pracovní slot.

    a. Vytvořte slot nasazení s názvem *Příprava*.

    ```azurecli
    az webapp deployment slot create --name $webappname --resource-group AzureTutorial --slot staging
    ```

    b. Nakonfigurujte pracovní slot pro použití nasazení z místního Gitu a získejte adresu URL **pracovního** nasazení. **Poznamenejte si tuto adresu URL pro referenci později**.

    ```azurecli
    echo Git deployment URL for staging: $(az webapp deployment source config-local-git --name $webappname --resource-group AzureTutorial --slot staging --query url --output tsv)
    ```

    c. Zobrazit adresu URL přípravného slotu. Přejděte na adresu URL, abyste viděli prázdný přípravný slot. **Poznamenejte si tuto adresu URL pro referenci později**.

    ```console
    echo Staging web app URL: http://$webappname-staging.azurewebsites.net
    ```

3. V textovém editoru nebo v aplikaci Visual Studio upravte *stránky nebo index. cshtml* tak, aby `<h2>` element četl `<h2>Simple Feed Reader - V3</h2>` a uložil soubor.

4. Potvrďte soubor do místního úložiště Git, a to pomocí stránky **změny** na kartě *Team Explorer* sady Visual Studio nebo zadáním následujícího příkazu pomocí příkazového prostředí místního počítače:

    ```console
    git commit -a -m "upgraded to V3"
    ```

5. Pomocí příkazového prostředí místního počítače přidejte adresu URL pracovního nasazení jako vzdálené úložiště Git a odešlete potvrzené změny:

    a. Přidejte vzdálenou adresu URL pro přípravu do místního úložiště Git.

    ```console
    git remote add azure-staging <Git_staging_deployment_URL>
    ```

    b. Nahrajte místní *Hlavní* větev do *hlavní* větve vzdálené údržby *Azure* .

    ```console
    git push azure-staging master
    ```

    Počkejte, než Azure sestaví a nasadí aplikaci.

6. Pokud chcete ověřit, že je hodnota V3 nasazená do přípravného slotu, otevřete dvě okna prohlížeče. V jednom okně přejděte na původní adresu URL webové aplikace. V druhém okně přejděte na adresu URL pracovní webové aplikace. Produkční adresa URL slouží jako v2 aplikace. Pracovní adresa URL slouží jako V3 aplikace.

    ![Snímek obrazovky s porovnáním oken prohlížeče](./media/deploying-to-app-service/ready-to-swap.png)

7. V Cloud Shell Proměňte v produkčním prostředí ověřené/teplé pracovní sloty.

    ```azurecli
    az webapp deployment slot swap --name $webappname --resource-group AzureTutorial --slot staging
    ```

8. Pomocí obnovení dvou oken prohlížeče ověřte, zda došlo k výměně.

    ![Porovnání oken prohlížeče po prohození](./media/deploying-to-app-service/swapped.png)

## <a name="summary"></a>Shrnutí

V této části byly dokončeny následující úkoly:

* Stáhli a vytvořili jste ukázkovou aplikaci.
* Vytvořili Azure App Service webovou aplikaci pomocí Azure Cloud Shell.
* Nasadili jste ukázkovou aplikaci do Azure pomocí Gitu.
* Nasadili jste do aplikace změnu pomocí sady Visual Studio.
* Do webové aplikace se přidal pracovní slot.
* Nasazení aktualizace do přípravného slotu.
* Pracovní a produkční sloty se vyměnily.

V další části se dozvíte, jak vytvořit kanál DevOps pomocí Azure Pipelines.

## <a name="additional-reading"></a>Další materiály ke čtení

* [Přehled Web Apps](/azure/app-service/app-service-web-overview)
* [Vytvoření webové aplikace .NET Core využívající SQL Database ve službě Azure App Service](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)
* [Nakonfigurovat přihlašovací údaje nasazení pro Azure App Service](/azure/app-service/app-service-deployment-credentials)
* [Nastavení přípravných prostředí ve službě Azure App Service](/azure/app-service/web-sites-staged-publishing)
