---
title: Začínáme s ASP.NET Core MVC
author: rick-anderson
description: Přečtěte si, jak začít s ASP.NET Core MVC.
ms.author: riande
ms.date: 10/16/2019
uid: tutorials/first-mvc-app/start-mvc
ms.openlocfilehash: 901257efdfbc7b36249233745175f5ed253da2c7
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78662475"
---
# <a name="get-started-with-aspnet-core-mvc"></a>Začínáme s ASP.NET Core MVC

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

Tento kurz učí základy vytváření ASP.NET webové aplikace Core MVC.

Aplikace spravuje databázi filmových titulů. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvořte webovou aplikaci.
> * Přidejte a lešení modelu.
> * Práce s databází.
> * Přidejte vyhledávání a ověřování.

Na konci máte aplikaci, která může spravovat a zobrazovat filmová data.

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a>Požadavky

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-app"></a>Vytvoření webové aplikace

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* V sadě Visual Studio vyberte **Vytvořit nový projekt**.

* Vyberte **ASP.NET základní webovou aplikaci** a pak vyberte **další**.

![nová ASP.NET základní webová aplikace](start-mvc/_static/np_2.1.png)

* Pojmenujte projekt **MvcMovie** a vyberte **vytvořit**. Je důležité pojmenovat projekt **MvcMovie,** takže při kopírování kódu se bude shodovat jmenovcí prostor.

  ![nová ASP.NET základní webová aplikace](start-mvc/_static/config.png)

* Vyberte **webovou aplikaci (model-view-controller)** a pak vyberte **Vytvořit**.

![Dialogové okno Nový projekt, .NET Core v levém podokně, ASP.NET web Core ](start-mvc/_static/new_project30.png)

Visual Studio použilo výchozí šablonu pro projekt MVC, který jste právě vytvořili. Máte pracovní aplikaci právě teď zadáním názvu projektu a výběrem několika možností. Jedná se o základní startovací projekt.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Kurz předpokládá familaritu s VS Code. Další informace najdete [v tématu Začínáme s nápovědou VS Code](https://code.visualstudio.com/docs) a [Visual Studio Code.](#visual-studio-code-help)

* Otevřete [integrovanou svorku](https://code.visualstudio.com/docs/editor/integrated-terminal).
* Změňte adresáře (`cd`) na složku, která bude projekt obsahovat.
* Spusťte následující příkaz:

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * V programu MvcMovie se zobrazí dialogové okno s **požadovanými datovými zdroji k sestavení a laděním. Přidat?**  Vybrat **ano**

  * `dotnet new mvc -o MvcMovie`: vytvoří nový ASP.NET core mvc projektu ve složce *MvcMovie.*
  * `code -r MvcMovie`: Načte soubor projektu *MvcMovie.csproj* v kódu sady Visual Studio.

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

* Vyberte **možnost Nové řešení souboru** > **New Solution**.

  ![macOS Nové řešení](./start-mvc/_static/new_project_vsmac.png)

* Vyberte webovou **aplikaci** > **.NET Core** > App **Web Application (Model-View-Controller)** > **Next**.

  ![macOS Dialogové okno Nový projekt](./start-mvc/_static/new_project_mvc_vsmac.png)

* V dialogovém **okně Konfigurovat nové webové rozhraní API ASP.NET** nastavte **cílovou architekturu** **rozhraní .NET Core 3.1**.

  ![výběr macOS .NET Core 3.1](./start-mvc/_static/new_project_31_vsmac.png)

* Pojmenujte projekt **MvcMovie**a pak vyberte **vytvořit**.

---

### <a name="run-the-app"></a>Spuštění aplikace

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Chcete-li aplikaci spustit v režimu bez ladění, vyberte **Ctrl-F5.**

[!INCLUDE[](~/includes/trustCertVS.md)]

* Visual Studio spustí [iis express](/iis/extensions/introduction-to-iis-express/iis-express-overview) a spustí aplikaci. Všimněte si, `localhost:port#` že se `example.com`zobrazuje adresní řádek a ne něco jako . Je to `localhost` proto, že je standardní název hostitele pro místní počítač. Když Visual Studio vytvoří webový projekt, použije se pro webový server náhodný port.
* Spuštění aplikace s Ctrl + F5 (non-ladění režimu) umožňuje provádět změny kódu, uložit soubor, aktualizovat prohlížeč, a vidět změny kódu. Řada vývojářů dává přednost používání režimu bez ladění, aby mohli aplikaci rychle spustit a podívat se na změny.
* Aplikaci můžete spustit v režimu ladění nebo bez ladění z položky nabídky **Ladění:**

  ![Nabídka ladění](start-mvc/_static/debug_menu.png)

* Aplikaci můžete ladit výběrem tlačítka **IIS Express**

  ![IIS Express](start-mvc/_static/iis_express.png)

  Následující obrázek znázorňuje aplikaci:

  ![Domovská stránka nebo stránka Rejstříku](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Stisknutím kláves Ctrl+F5 spusťte bez ladicího programu.

[!INCLUDE[](~/includes/trustCertVSC.md)]

  Visual Studio Code spustí [Kestrel](xref:fundamentals/servers/kestrel), spustí prohlížeč `https://localhost:5001`a přejde na . Adresní řádek `localhost:port:5001` zobrazuje a `example.com`ne něco jako . To proto, `localhost` že je standardní název hostitele pro místní počítač. Localhost slouží pouze webové požadavky z místního počítače.

  Spuštění aplikace s Ctrl + F5 (non-ladění režimu) umožňuje provádět změny kódu, uložit soubor, aktualizovat prohlížeč, a vidět změny kódu. Mnoho vývojářů dává přednost použití režimu bez ladění k aktualizaci stránky a zobrazení změn.

  ![Domovská stránka nebo stránka Rejstříku](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

Chcete-li aplikaci spustit spustit bez ladění, vyberte spustit**spustit bez ladění.** **Run** >  Visual Studio for Mac spustí [server Kestrel,](xref:fundamentals/servers/index#kestrel) spustí `http://localhost:port`prohlížeč a přejde na , kde *port* je náhodně vybrané číslo portu.

[!INCLUDE[](~/includes/trustCertMac.md)]

* Adresní řádek `localhost:port#` zobrazuje a `example.com`ne něco jako . Je to `localhost` proto, že je standardní název hostitele pro místní počítač. Když Visual Studio vytvoří webový projekt, použije se pro webový server náhodný port. Když aplikaci spustíte, zobrazí se jiné číslo portu.
* Aplikaci můžete spustit v režimu ladění nebo bez ladění z nabídky **Spustit.**

  Následující obrázek znázorňuje aplikaci:

  ![Domovská stránka nebo stránka Rejstříku](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

V další části tohoto kurzu se dozvíte o MVC a začnete psát nějaký kód.

> [!div class="step-by-step"]
> [Další](adding-controller.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

Tento kurz učí základy vytváření ASP.NET webové aplikace Core MVC.

Aplikace spravuje databázi filmových titulů. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvořte webovou aplikaci.
> * Přidejte a lešení modelu.
> * Práce s databází.
> * Přidejte vyhledávání a ověřování.

Na konci máte aplikaci, která může spravovat a zobrazovat filmová data.

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a>Požadavky

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---
## <a name="create-a-web-app"></a>Vytvoření webové aplikace

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* V sadě Visual Studio vyberte **Vytvořit nový projekt**.

* Vyberte **ASP.NET základní webovou aplikaci** a pak vyberte **další**.

![nová ASP.NET základní webová aplikace](start-mvc/_static/np_2.1.png)

* Pojmenujte projekt **MvcMovie** a vyberte **vytvořit**. Je důležité pojmenovat projekt **MvcMovie,** takže při kopírování kódu se bude shodovat jmenovcí prostor.

  ![nová ASP.NET základní webová aplikace](start-mvc/_static/config.png)


* Vyberte **webovou aplikaci (model-view-controller)** a pak vyberte **Vytvořit**.

![Dialogové okno Nový projekt, .NET Core v levém podokně, ASP.NET web Core ](start-mvc/_static/new_project22-21.png)

Visual Studio použilo výchozí šablonu pro projekt MVC, který jste právě vytvořili. Máte pracovní aplikaci právě teď zadáním názvu projektu a výběrem několika možností. Jedná se o základní startovací projekt a je to dobré místo pro začátek.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Kurz předpokládá familaritu s VS Code. Další informace najdete [v tématu Začínáme s nápovědou VS Code](https://code.visualstudio.com/docs) a [Visual Studio Code.](#visual-studio-code-help)

* Otevřete [integrovanou svorku](https://code.visualstudio.com/docs/editor/integrated-terminal).
* Změňte adresáře (`cd`) na složku, která bude projekt obsahovat.
* Spusťte následující příkaz:

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * V programu MvcMovie se zobrazí dialogové okno s **požadovanými datovými zdroji k sestavení a laděním. Přidat?**  Vybrat **ano**

  * `dotnet new mvc -o MvcMovie`: vytvoří nový ASP.NET core mvc projektu ve složce *MvcMovie.*
  * `code -r MvcMovie`: Načte soubor projektu *MvcMovie.csproj* v kódu sady Visual Studio.

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

* Vyberte **možnost Nové řešení souboru** > **New Solution**.

  ![macOS Nové řešení](./start-mvc/_static/new_project_vsmac.png)

* Vyberte webovou **aplikaci** > **.NET Core** > App **Web Application (Model-View-Controller)** > **Next**.

  ![macOS Dialogové okno Nový projekt](./start-mvc/_static/new_project_mvc_vsmac.png)

* V dialogovém **okně Konfigurovat nové ASP.NET základní webové rozhraní API** přijměte výchozí **cílovou architekturu** **rozhraní .NET Core 2.2**.

  ![výběr macOS .NET Core 2.2](./start-mvc/_static/new_project_22_vsmac.png)

* Pojmenujte projekt **MvcMovie**a pak vyberte **vytvořit**.

---

### <a name="run-the-app"></a>Spuštění aplikace

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Chcete-li aplikaci spustit v režimu bez ladění, vyberte **Ctrl-F5.**

[!INCLUDE[](~/includes/trustCertVS.md)]

* Visual Studio spustí [iis express](/iis/extensions/introduction-to-iis-express/iis-express-overview) a spustí aplikaci. Všimněte si, `localhost:port#` že se `example.com`zobrazuje adresní řádek a ne něco jako . Je to `localhost` proto, že je standardní název hostitele pro místní počítač. Když Visual Studio vytvoří webový projekt, použije se pro webový server náhodný port.
* Spuštění aplikace s Ctrl + F5 (non-ladění režimu) umožňuje provádět změny kódu, uložit soubor, aktualizovat prohlížeč, a vidět změny kódu. Řada vývojářů dává přednost používání režimu bez ladění, aby mohli aplikaci rychle spustit a podívat se na změny.
* Aplikaci můžete spustit v režimu ladění nebo bez ladění z položky nabídky **Ladění:**

  ![Nabídka ladění](start-mvc/_static/debug_menu.png)

* Aplikaci můžete ladit výběrem tlačítka **IIS Express**

  ![IIS Express](start-mvc/_static/iis_express.png)

* Chcete-li souhlasit se sledováním, vyberte **možnost Přijmout.** Tato aplikace nesleduje osobní údaje. Šablona generovaný kód obsahuje aktiva, která pomáhají splnit [obecné nařízení o ochraně osobních údajů (GDPR).](xref:security/gdpr)

  ![Domovská stránka nebo stránka Rejstříku](start-mvc/_static/privacy.png)

  Následující obrázek znázorňuje aplikaci po přijetí sledování:

  ![Domovská stránka nebo stránka Rejstříku](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Stisknutím kláves Ctrl+F5 spusťte bez ladicího programu.

[!INCLUDE[](~/includes/trustCertVSC.md)]

  Visual Studio Code spustí [Kestrel](xref:fundamentals/servers/kestrel), spustí prohlížeč `https://localhost:5001`a přejde na . Adresní řádek `localhost:port:5001` zobrazuje a `example.com`ne něco jako . To proto, `localhost` že je standardní název hostitele pro místní počítač. Localhost slouží pouze webové požadavky z místního počítače.

  Spuštění aplikace s Ctrl + F5 (non-ladění režimu) umožňuje provádět změny kódu, uložit soubor, aktualizovat prohlížeč, a vidět změny kódu. Mnoho vývojářů dává přednost použití režimu bez ladění k aktualizaci stránky a zobrazení změn.

* Chcete-li souhlasit se sledováním, vyberte **možnost Přijmout.** Tato aplikace nesleduje osobní údaje. Šablona generovaný kód obsahuje aktiva, která pomáhají splnit [obecné nařízení o ochraně osobních údajů (GDPR).](xref:security/gdpr)

  ![Domovská stránka nebo stránka Rejstříku](start-mvc/_static/privacy.png)

  Následující obrázek znázorňuje aplikaci po přijetí sledování:

  ![Domovská stránka nebo stránka Rejstříku](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

Chcete-li aplikaci spustit spustit bez ladění, vyberte spustit**spustit bez ladění.** **Run** >  Visual Studio for Mac spustí [server Kestrel,](xref:fundamentals/servers/index#kestrel) spustí `http://localhost:port`prohlížeč a přejde na , kde *port* je náhodně vybrané číslo portu.

[!INCLUDE[](~/includes/trustCertMac.md)]

* Adresní řádek `localhost:port#` zobrazuje a `example.com`ne něco jako . Je to `localhost` proto, že je standardní název hostitele pro místní počítač. Když Visual Studio vytvoří webový projekt, použije se pro webový server náhodný port. Když aplikaci spustíte, zobrazí se jiné číslo portu.
* Aplikaci můžete spustit v režimu ladění nebo bez ladění z nabídky **Spustit.**

* Chcete-li souhlasit se sledováním, vyberte **možnost Přijmout.** Tato aplikace nesleduje osobní údaje. Šablona generovaný kód obsahuje aktiva, která pomáhají splnit [obecné nařízení o ochraně osobních údajů (GDPR).](xref:security/gdpr)

  ![Domovská stránka nebo stránka Rejstříku](./start-mvc/_static/output_privacy_macos.png)

  Následující obrázek znázorňuje aplikaci po přijetí sledování:

  ![Domovská stránka nebo stránka Rejstříku](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

V další části tohoto kurzu se dozvíte o MVC a začnete psát nějaký kód.

> [!div class="step-by-step"]
> [Další](adding-controller.md)

::: moniker-end
