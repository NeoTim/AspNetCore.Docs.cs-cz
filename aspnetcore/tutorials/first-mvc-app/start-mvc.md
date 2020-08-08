---
title: Začínáme s ASP.NET Core MVC
author: rick-anderson
description: Naučte se, jak začít s ASP.NET Core MVC.
ms.author: riande
ms.date: 10/16/2019
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/first-mvc-app/start-mvc
ms.openlocfilehash: 3df00af8e6bca0dbf2d7871f383dd67b465aa5da
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2020
ms.locfileid: "88021169"
---
# <a name="get-started-with-aspnet-core-mvc"></a>Začínáme s ASP.NET Core MVC

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

V tomto kurzu se naučíte základy vytváření ASP.NET Core webové aplikace MVC.

Aplikace spravuje databázi titulů filmů. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvořte webovou aplikaci.
> * Přidání a vytvoření uživatelského rozhraní modelu.
> * Práce s databází
> * Přidejte vyhledávání a ověřování.

Na konci máte aplikaci, která může spravovat a zobrazovat data filmů.

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

* V aplikaci Visual Studio vyberte **vytvořit nový projekt**.

* Vyberte **ASP.NET Core webová aplikace** a pak vyberte **Další**.

![Nová ASP.NET Core webové aplikace](start-mvc/_static/np_2.1.png)

* Pojmenujte projekt **MvcMovie** a vyberte **vytvořit**. Je důležité pojmenovat projekt **MvcMovie** , takže když zkopírujete kód, obor názvů se bude shodovat.

  ![Nová ASP.NET Core webové aplikace](start-mvc/_static/config.png)

* Vyberte možnost **Webová aplikace (model-zobrazení-kontroler)** a pak vyberte **vytvořit**.

![Dialog Nový projekt, .NET Core v levém podokně, ASP.NET Core Web ](start-mvc/_static/new_project30.png)

Aplikace Visual Studio použila pro projekt MVC, který jste právě vytvořili, výchozí šablonu. Máte funkční aplikaci hned teď zadáním názvu projektu a výběrem několika možností. Toto je základní počáteční projekt.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Kurz předpokládá familarity s VS Code. Další informace najdete v tématu [Začínáme s vs Code](https://code.visualstudio.com/docs) a [Visual Studio Code nápovědě](#visual-studio-code-help) .

* Otevřete [integrovaný terminál](https://code.visualstudio.com/docs/editor/integrated-terminal).
* Změňte adresáře ( `cd` ) na složku, která bude obsahovat projekt.
* Spusťte následující příkaz:

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * Zobrazí se dialogové okno s **požadovanými prostředky pro sestavení a ladění chybí v ' MvcMovie '. Přidat je?**  Vyberte **Ano**

  * `dotnet new mvc -o MvcMovie`: vytvoří nový projekt ASP.NET Core MVC ve složce *MvcMovie* .
  * `code -r MvcMovie`: Načte soubor projektu *MvcMovie. csproj* do Visual Studio Code.

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

* Vyberte **soubor** > **nové řešení**.

  ![macOS nové řešení](start-mvc/_static/new_project_vsmac.png)

* V Visual Studio pro Mac starší než verze 8,6 vyberte možnost Webová aplikace **.NET Core**  >  **App**  >  **Web (Model-View-Controller)**  >  **Next**. Ve verzi 8,6 nebo novější vyberte možnost Webová aplikace **webové a konzolová**  >  **aplikace**  >  **(model-zobrazení-kontroler)**  >  **Další**.

  ![Výběr šablony webové aplikace v macOS](start-mvc/_static/web_app_template_vsmac.png)

* V dialogovém okně **Konfigurace nové webové aplikace** :

  * Potvrďte, že **ověřování** je nastaveno na **bez ověřování**.
  * Pokud se zobrazí možnost výběru **cílové platformy**, vyberte nejnovější verzi 3. x.

  Vyberte **Další**.

* Pojmenujte projekt **MvcMovie**a pak vyberte **vytvořit**.

  ![macOS název projektu](start-mvc/_static/MvcMovie.png)

---

### <a name="run-the-app"></a>Spuštění aplikace

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Vyberte **CTRL + F5** a spusťte aplikaci v režimu bez ladění.

[!INCLUDE[](~/includes/trustCertVS.md)]

* Visual Studio spustí [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) a spustí aplikaci. Všimněte si, že se v adresním řádku zobrazuje, `localhost:port#` a ne něco podobného `example.com` . Důvodem je, že `localhost` je standardní název hostitele pro váš místní počítač. Když Visual Studio vytvoří webový projekt, použije se pro webový server náhodný port.
* Spuštění aplikace pomocí CTRL + F5 (režim bez ladění) umožňuje provádět změny kódu, uložit soubor, aktualizovat prohlížeč a zobrazit změny kódu. Řada vývojářů dává přednost používání režimu bez ladění, aby mohli aplikaci rychle spustit a podívat se na změny.
* Aplikaci můžete spustit v režimu ladění nebo bez ladění z položky nabídky **ladění** :

  ![Nabídka ladění](start-mvc/_static/debug_menu.png)

* Aplikaci můžete ladit tak, že vyberete tlačítko **IIS Express** .

  ![IIS Express](start-mvc/_static/iis_express.png)

  Následující obrázek znázorňuje aplikaci:

  ![Stránka domů nebo index](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Stisknutím kombinace kláves CTRL + F5 spustíte bez ladicího programu.

[!INCLUDE[](~/includes/trustCertVSC.md)]

  Visual Studio Code spustí [Kestrel](xref:fundamentals/servers/kestrel), spustí prohlížeč a přejde na `https://localhost:5001` . Panel Adresa se zobrazí `localhost:port:5001` a ne něco jako `example.com` . To je proto, že `localhost` se jedná o standardní název hostitele pro místní počítač. Localhost obsluhuje pouze webové požadavky z místního počítače.

  Spuštění aplikace pomocí CTRL + F5 (režim bez ladění) umožňuje provádět změny kódu, uložit soubor, aktualizovat prohlížeč a zobrazit změny kódu. Mnoho vývojářů upřednostňuje použití režimu bez ladění k aktualizaci stránky a zobrazení změn.

  ![Stránka domů nebo index](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

Kliknutím na **Spustit**  >  **Spustit bez ladění** spusťte aplikaci. Visual Studio pro Mac spustí [Kestrel](xref:fundamentals/servers/index#kestrel) Server, spustí prohlížeč a přejde na `http://localhost:port` místo, kde *port* je náhodně zvolené číslo portu.

[!INCLUDE[](~/includes/trustCertMac.md)]

* Panel Adresa se zobrazí `localhost:port#` a ne něco jako `example.com` . Důvodem je, že `localhost` je standardní název hostitele pro váš místní počítač. Když Visual Studio vytvoří webový projekt, použije se pro webový server náhodný port. Po spuštění aplikace se zobrazí jiné číslo portu.
* Aplikaci můžete spustit v režimu ladění nebo bez ladění z nabídky **Spustit** .

  Následující obrázek znázorňuje aplikaci:

  ![Stránka domů nebo index](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

V další části tohoto kurzu se dozvíte o MVC a začněte psát kód.

> [!div class="step-by-step"]
> [Další](adding-controller.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

V tomto kurzu se naučíte základy vytváření ASP.NET Core webové aplikace MVC.

Aplikace spravuje databázi titulů filmů. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvořte webovou aplikaci.
> * Přidání a vytvoření uživatelského rozhraní modelu.
> * Práce s databází
> * Přidejte vyhledávání a ověřování.

Na konci máte aplikaci, která může spravovat a zobrazovat data filmů.

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

* V aplikaci Visual Studio vyberte **vytvořit nový projekt**.

* Vyberte **ASP.NET Core webová aplikace** a pak vyberte **Další**.

![Nová ASP.NET Core webové aplikace](start-mvc/_static/np_2.1.png)

* Pojmenujte projekt **MvcMovie** a vyberte **vytvořit**. Je důležité pojmenovat projekt **MvcMovie** , takže když zkopírujete kód, obor názvů se bude shodovat.

  ![Nová ASP.NET Core webové aplikace](start-mvc/_static/config.png)


* Vyberte možnost **Webová aplikace (model-zobrazení-kontroler)** a pak vyberte **vytvořit**.

![Dialog Nový projekt, .NET Core v levém podokně, ASP.NET Core Web ](start-mvc/_static/new_project22-21.png)

Aplikace Visual Studio použila pro projekt MVC, který jste právě vytvořili, výchozí šablonu. Máte funkční aplikaci hned teď zadáním názvu projektu a výběrem několika možností. Toto je základní počáteční projekt a je dobrým místem, kde začít.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Kurz předpokládá familarity s VS Code. Další informace najdete v tématu [Začínáme s vs Code](https://code.visualstudio.com/docs) a [Visual Studio Code nápovědě](#visual-studio-code-help) .

* Otevřete [integrovaný terminál](https://code.visualstudio.com/docs/editor/integrated-terminal).
* Změňte adresáře ( `cd` ) na složku, která bude obsahovat projekt.
* Spusťte následující příkaz:

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * Zobrazí se dialogové okno s **požadovanými prostředky pro sestavení a ladění chybí v ' MvcMovie '. Přidat je?**  Vyberte **Ano**

  * `dotnet new mvc -o MvcMovie`: vytvoří nový projekt ASP.NET Core MVC ve složce *MvcMovie* .
  * `code -r MvcMovie`: Načte soubor projektu *MvcMovie. csproj* do Visual Studio Code.

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

* Vyberte **soubor** > **nové řešení**.

  ![macOS nové řešení](./start-mvc/_static/new_project_vsmac.png)

* V Visual Studio pro Mac starší než verze 8,6 vyberte možnost Webová aplikace **.NET Core**  >  **App**  >  **Web (Model-View-Controller)**  >  **Next**. Ve verzi 8,6 nebo novější vyberte možnost Webová aplikace **webové a konzolová**  >  **aplikace**  >  **(model-zobrazení-kontroler)**  >  **Další**.

* V dialogovém okně **Konfigurace nové webové aplikace** :

  * Potvrďte, že **ověřování** je nastaveno na **bez ověřování**.
  * Pokud se zobrazí možnost výběru **cílové platformy**, vyberte nejnovější verzi 2. x.

  Vyberte **Další**.

* Pojmenujte projekt **MvcMovie**a pak vyberte **vytvořit**.

---

### <a name="run-the-app"></a>Spuštění aplikace

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Vyberte **CTRL + F5** a spusťte aplikaci v režimu bez ladění.

[!INCLUDE[](~/includes/trustCertVS.md)]

* Visual Studio spustí [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) a spustí aplikaci. Všimněte si, že se v adresním řádku zobrazuje, `localhost:port#` a ne něco podobného `example.com` . Důvodem je, že `localhost` je standardní název hostitele pro váš místní počítač. Když Visual Studio vytvoří webový projekt, použije se pro webový server náhodný port.
* Spuštění aplikace pomocí CTRL + F5 (režim bez ladění) umožňuje provádět změny kódu, uložit soubor, aktualizovat prohlížeč a zobrazit změny kódu. Řada vývojářů dává přednost používání režimu bez ladění, aby mohli aplikaci rychle spustit a podívat se na změny.
* Aplikaci můžete spustit v režimu ladění nebo bez ladění z položky nabídky **ladění** :

  ![Nabídka ladění](start-mvc/_static/debug_menu.png)

* Aplikaci můžete ladit tak, že vyberete tlačítko **IIS Express** .

  ![IIS Express](start-mvc/_static/iis_express.png)

* Kliknutím na tlačítko **Přijmout** vyjádříte souhlas se sledováním. Tato aplikace nesleduje osobní údaje. Kód vygenerovaný šablonou zahrnuje prostředky, které pomůžou splnit [Obecné nařízení o ochraně osobních údajů (GDPR)](xref:security/gdpr).

  ![Stránka domů nebo index](start-mvc/_static/privacy.png)

  Následující obrázek ukazuje aplikaci po přijetí sledování:

  ![Stránka domů nebo index](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Stisknutím kombinace kláves CTRL + F5 spustíte bez ladicího programu.

[!INCLUDE[](~/includes/trustCertVSC.md)]

  Visual Studio Code spustí [Kestrel](xref:fundamentals/servers/kestrel), spustí prohlížeč a přejde na `https://localhost:5001` . Panel Adresa se zobrazí `localhost:port:5001` a ne něco jako `example.com` . To je proto, že `localhost` se jedná o standardní název hostitele pro místní počítač. Localhost obsluhuje pouze webové požadavky z místního počítače.

  Spuštění aplikace pomocí CTRL + F5 (režim bez ladění) umožňuje provádět změny kódu, uložit soubor, aktualizovat prohlížeč a zobrazit změny kódu. Mnoho vývojářů upřednostňuje použití režimu bez ladění k aktualizaci stránky a zobrazení změn.

* Kliknutím na tlačítko **Přijmout** vyjádříte souhlas se sledováním. Tato aplikace nesleduje osobní údaje. Kód vygenerovaný šablonou zahrnuje prostředky, které pomůžou splnit [Obecné nařízení o ochraně osobních údajů (GDPR)](xref:security/gdpr).

  ![Stránka domů nebo index](start-mvc/_static/privacy.png)

  Následující obrázek ukazuje aplikaci po přijetí sledování:

  ![Stránka domů nebo index](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

Kliknutím na **Spustit**  >  **Spustit bez ladění** spusťte aplikaci. Visual Studio pro Mac spustí [Kestrel](xref:fundamentals/servers/index#kestrel) Server, spustí prohlížeč a přejde na `http://localhost:port` místo, kde *port* je náhodně zvolené číslo portu.

[!INCLUDE[](~/includes/trustCertMac.md)]

* Panel Adresa se zobrazí `localhost:port#` a ne něco jako `example.com` . Důvodem je, že `localhost` je standardní název hostitele pro váš místní počítač. Když Visual Studio vytvoří webový projekt, použije se pro webový server náhodný port. Po spuštění aplikace se zobrazí jiné číslo portu.
* Aplikaci můžete spustit v režimu ladění nebo bez ladění z nabídky **Spustit** .

* Kliknutím na tlačítko **Přijmout** vyjádříte souhlas se sledováním. Tato aplikace nesleduje osobní údaje. Kód vygenerovaný šablonou zahrnuje prostředky, které pomůžou splnit [Obecné nařízení o ochraně osobních údajů (GDPR)](xref:security/gdpr).

  ![Stránka domů nebo index](./start-mvc/_static/output_privacy_macos.png)

  Následující obrázek ukazuje aplikaci po přijetí sledování:

  ![Stránka domů nebo index](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

V další části tohoto kurzu se dozvíte o MVC a začněte psát kód.

> [!div class="step-by-step"]
> [Další](adding-controller.md)

::: moniker-end
