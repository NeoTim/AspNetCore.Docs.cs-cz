---
title: Začínáme s ASP.NET Core MVC
author: rick-anderson
description: Naučte se, jak začít s ASP.NET Core MVC.
ms.author: riande
ms.date: 10/16/2019
uid: tutorials/first-mvc-app/start-mvc
ms.openlocfilehash: e70384a6f20f3ef06059ed6b51c76e923187c317
ms.sourcegitcommit: 2cb857f0de774df421e35289662ba92cfe56ffd1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75354928"
---
# <a name="get-started-with-aspnet-core-mvc"></a>Začínáme s ASP.NET Core MVC

Podle [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

V tomto kurzu se naučíte základy vytváření ASP.NET Core webové aplikace MVC.

Aplikace spravuje databázi titulů filmů. Získáte informace o následujících postupech:

> [!div class="checklist"]
> * Vytvořte webovou aplikaci.
> * Přidání a vytvoření uživatelského rozhraní modelu.
> * Práce s databází
> * Přidejte vyhledávání a ověřování.

Na konci máte aplikaci, která může spravovat a zobrazovat data filmů.

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a>Požadavky

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-app"></a>Vytvoření webové aplikace

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* V aplikaci Visual Studio vyberte **vytvořit nový projekt**.

* Vyberte **ASP.NET Core webová aplikace** a pak vyberte **Další**.

![Nová ASP.NET Core webové aplikace](start-mvc/_static/np_2.1.png)

* Pojmenujte projekt **MvcMovie** a vyberte **vytvořit**. Je důležité pojmenovat projekt **MvcMovie** , takže když zkopírujete kód, obor názvů se bude shodovat.

  ![Nová ASP.NET Core webové aplikace](start-mvc/_static/config.png)

* Vyberte možnost **Webová aplikace (model-zobrazení-kontroler)** a pak vyberte **vytvořit**.

![Dialog Nový projekt, .NET Core v levém podokně, ASP.NET Core Web ](start-mvc/_static/new_project30.png)

Aplikace Visual Studio použila pro projekt MVC, který jste právě vytvořili, výchozí šablonu. Máte funkční aplikaci hned teď zadáním názvu projektu a výběrem několika možností. Toto je základní počáteční projekt.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Kurz předpokládá familarity s VS Code. Další informace najdete v tématu [Začínáme s vs Code](https://code.visualstudio.com/docs) a [Visual Studio Code nápovědě](#visual-studio-code-help) .

* Otevřít [integrovaný terminál](https://code.visualstudio.com/docs/editor/integrated-terminal).
* Změňte adresáře (`cd`) na složku, která bude obsahovat projekt.
* Spusťte následující příkaz:

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * Zobrazí se dialogové okno s **požadovanými prostředky pro sestavení a ladění chybí v ' MvcMovie '. Přidat je?**  Vyberte **Ano**

  * `dotnet new mvc -o MvcMovie`: vytvoří nový projekt ASP.NET Core MVC ve složce *MvcMovie* .
  * `code -r MvcMovie`: načte do Visual Studio Code soubor projektu *MvcMovie. csproj* .

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

* Vyberte **soubor** > **nové řešení**.

  ![macOS nové řešení](./start-mvc/_static/new_project_vsmac.png)

* Vyberte možnost **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Další**.

  ![macOS dialogové okno nového projektu](./start-mvc/_static/new_project_mvc_vsmac.png)

* V dialogovém okně **Konfigurace nového ASP.NET Core webového rozhraní API** nastavte **cílovou architekturu** **.NET Core 3,1**.

<!-- 
  ![macOS .NET Core 2.2 selection](./start-mvc/_static/new_project_22_vsmac.png)
-->

* Pojmenujte projekt **MvcMovie**a pak vyberte **vytvořit**.

---

### <a name="run-the-app"></a>Spuštění aplikace

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Vyberte **CTRL + F5** a spusťte aplikaci v režimu bez ladění.

[!INCLUDE[](~/includes/trustCertVS.md)]

* Visual Studio spustí [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) a spustí aplikaci. Všimněte si, že se v adresním řádku zobrazuje `localhost:port#` a ne něco jako `example.com`. Důvodem je, že `localhost` je standardní název hostitele pro váš místní počítač. Když Visual Studio vytvoří webový projekt, použije se pro webový server náhodný port.
* Spuštění aplikace pomocí CTRL + F5 (režim bez ladění) umožňuje provádět změny kódu, uložit soubor, aktualizovat prohlížeč a zobrazit změny kódu. Řada vývojářů dává přednost používání režimu bez ladění, aby mohli aplikaci rychle spustit a podívat se na změny.
* Aplikaci můžete spustit v režimu ladění nebo bez ladění z položky nabídky **ladění** :

  ![Nabídka ladění](start-mvc/_static/debug_menu.png)

* Aplikaci můžete ladit tak, že vyberete tlačítko **IIS Express** .

  ![IIS Express](start-mvc/_static/iis_express.png)

  Následující obrázek znázorňuje aplikaci:

  ![Stránka domů nebo index](start-mvc/_static/home2.2.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Stisknutím kombinace kláves CTRL + F5 spustíte bez ladicího programu.

[!INCLUDE[](~/includes/trustCertVSC.md)]

  Visual Studio Code spustí [Kestrel](xref:fundamentals/servers/kestrel), spustí prohlížeč a přejde na `https://localhost:5001`. V adresním řádku se zobrazí `localhost:port:5001` a ne něco jako `example.com`. Důvodem je, že `localhost` je standardní název hostitele pro místní počítač. Localhost obsluhuje pouze webové požadavky z místního počítače.

  Spuštění aplikace pomocí CTRL + F5 (režim bez ladění) umožňuje provádět změny kódu, uložit soubor, aktualizovat prohlížeč a zobrazit změny kódu. Mnoho vývojářů upřednostňuje použití režimu bez ladění k aktualizaci stránky a zobrazení změn.

  ![Stránka domů nebo index](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

Pokud chcete spustit aplikaci, vyberte **spustit** > **Spustit bez ladění** . Visual Studio pro Mac spustí [Kestrel](xref:fundamentals/servers/index#kestrel) Server, spustí prohlížeč a přejde na `http://localhost:port`, kde *port* je náhodně zvolené číslo portu.

[!INCLUDE[](~/includes/trustCertMac.md)]

* V adresním řádku se zobrazí `localhost:port#` a ne něco jako `example.com`. Důvodem je, že `localhost` je standardní název hostitele pro váš místní počítač. Když Visual Studio vytvoří webový projekt, použije se pro webový server náhodný port. Po spuštění aplikace se zobrazí jiné číslo portu.
* Aplikaci můžete spustit v režimu ladění nebo bez ladění z nabídky **Spustit** .

  Následující obrázek znázorňuje aplikaci:

  ![Stránka domů nebo index](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

V další části tohoto kurzu se dozvíte o MVC a začněte psát kód.

> [!div class="step-by-step"]
> [Next](adding-controller.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

V tomto kurzu se naučíte základy vytváření ASP.NET Core webové aplikace MVC.

Aplikace spravuje databázi titulů filmů. Získáte informace o následujících postupech:

> [!div class="checklist"]
> * Vytvořte webovou aplikaci.
> * Přidání a vytvoření uživatelského rozhraní modelu.
> * Práce s databází
> * Přidejte vyhledávání a ověřování.

Na konci máte aplikaci, která může spravovat a zobrazovat data filmů.

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a>Požadavky

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---
## <a name="create-a-web-app"></a>Vytvoření webové aplikace

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* V aplikaci Visual Studio vyberte **vytvořit nový projekt**.

* Vyberte **ASP.NET Core webová aplikace** a pak vyberte **Další**.

![Nová ASP.NET Core webové aplikace](start-mvc/_static/np_2.1.png)

* Pojmenujte projekt **MvcMovie** a vyberte **vytvořit**. Je důležité pojmenovat projekt **MvcMovie** , takže když zkopírujete kód, obor názvů se bude shodovat.

  ![Nová ASP.NET Core webové aplikace](start-mvc/_static/config.png)


* Vyberte možnost **Webová aplikace (model-zobrazení-kontroler)** a pak vyberte **vytvořit**.

![Dialog Nový projekt, .NET Core v levém podokně, ASP.NET Core Web ](start-mvc/_static/new_project22-21.png)

Aplikace Visual Studio použila pro projekt MVC, který jste právě vytvořili, výchozí šablonu. Máte funkční aplikaci hned teď zadáním názvu projektu a výběrem několika možností. Toto je základní počáteční projekt a je dobrým místem, kde začít.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Kurz předpokládá familarity s VS Code. Další informace najdete v tématu [Začínáme s vs Code](https://code.visualstudio.com/docs) a [Visual Studio Code nápovědě](#visual-studio-code-help) .

* Otevřít [integrovaný terminál](https://code.visualstudio.com/docs/editor/integrated-terminal).
* Změňte adresáře (`cd`) na složku, která bude obsahovat projekt.
* Spusťte následující příkaz:

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * Zobrazí se dialogové okno s **požadovanými prostředky pro sestavení a ladění chybí v ' MvcMovie '. Přidat je?**  Vyberte **Ano**

  * `dotnet new mvc -o MvcMovie`: vytvoří nový projekt ASP.NET Core MVC ve složce *MvcMovie* .
  * `code -r MvcMovie`: načte do Visual Studio Code soubor projektu *MvcMovie. csproj* .

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

* Vyberte **soubor** > **nové řešení**.

  ![macOS nové řešení](./start-mvc/_static/new_project_vsmac.png)

* Vyberte možnost **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Další**.

  ![macOS dialogové okno nového projektu](./start-mvc/_static/new_project_mvc_vsmac.png)

* V dialogovém okně **Konfigurace nového ASP.NET Core webového rozhraní API** přijměte výchozí **cílovou architekturu** **.NET Core 2,2**.

  ![macOS .NET Core 2,2 – výběr](./start-mvc/_static/new_project_22_vsmac.png)

* Pojmenujte projekt **MvcMovie**a pak vyberte **vytvořit**.

---

### <a name="run-the-app"></a>Spuštění aplikace

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Vyberte **CTRL + F5** a spusťte aplikaci v režimu bez ladění.

[!INCLUDE[](~/includes/trustCertVS.md)]

* Visual Studio spustí [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) a spustí aplikaci. Všimněte si, že se v adresním řádku zobrazuje `localhost:port#` a ne něco jako `example.com`. Důvodem je, že `localhost` je standardní název hostitele pro váš místní počítač. Když Visual Studio vytvoří webový projekt, použije se pro webový server náhodný port.
* Spuštění aplikace pomocí CTRL + F5 (režim bez ladění) umožňuje provádět změny kódu, uložit soubor, aktualizovat prohlížeč a zobrazit změny kódu. Řada vývojářů dává přednost používání režimu bez ladění, aby mohli aplikaci rychle spustit a podívat se na změny.
* Aplikaci můžete spustit v režimu ladění nebo bez ladění z položky nabídky **ladění** :

  ![Nabídka ladění](start-mvc/_static/debug_menu.png)

* Aplikaci můžete ladit tak, že vyberete tlačítko **IIS Express** .

  ![IIS Express](start-mvc/_static/iis_express.png)

* Kliknutím na tlačítko **Přijmout** vyjádříte souhlas se sledováním. Tato aplikace nesleduje osobní údaje. Kód vygenerovaný šablonou zahrnuje prostředky, které pomůžou splnit [Obecné nařízení o ochraně osobních údajů (GDPR)](xref:security/gdpr).

  ![Stránka domů nebo index](start-mvc/_static/privacy.png)

  Následující obrázek ukazuje aplikaci po přijetí sledování:

  ![Stránka domů nebo index](start-mvc/_static/home2.2.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Stisknutím kombinace kláves CTRL + F5 spustíte bez ladicího programu.

[!INCLUDE[](~/includes/trustCertVSC.md)]

  Visual Studio Code spustí [Kestrel](xref:fundamentals/servers/kestrel), spustí prohlížeč a přejde na `https://localhost:5001`. V adresním řádku se zobrazí `localhost:port:5001` a ne něco jako `example.com`. Důvodem je, že `localhost` je standardní název hostitele pro místní počítač. Localhost obsluhuje pouze webové požadavky z místního počítače.

  Spuštění aplikace pomocí CTRL + F5 (režim bez ladění) umožňuje provádět změny kódu, uložit soubor, aktualizovat prohlížeč a zobrazit změny kódu. Mnoho vývojářů upřednostňuje použití režimu bez ladění k aktualizaci stránky a zobrazení změn.

* Kliknutím na tlačítko **Přijmout** vyjádříte souhlas se sledováním. Tato aplikace nesleduje osobní údaje. Kód vygenerovaný šablonou zahrnuje prostředky, které pomůžou splnit [Obecné nařízení o ochraně osobních údajů (GDPR)](xref:security/gdpr).

  ![Stránka domů nebo index](start-mvc/_static/privacy.png)

  Následující obrázek ukazuje aplikaci po přijetí sledování:

  ![Stránka domů nebo index](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

Pokud chcete spustit aplikaci, vyberte **spustit** > **Spustit bez ladění** . Visual Studio pro Mac spustí [Kestrel](xref:fundamentals/servers/index#kestrel) Server, spustí prohlížeč a přejde na `http://localhost:port`, kde *port* je náhodně zvolené číslo portu.

[!INCLUDE[](~/includes/trustCertMac.md)]

* V adresním řádku se zobrazí `localhost:port#` a ne něco jako `example.com`. Důvodem je, že `localhost` je standardní název hostitele pro váš místní počítač. Když Visual Studio vytvoří webový projekt, použije se pro webový server náhodný port. Po spuštění aplikace se zobrazí jiné číslo portu.
* Aplikaci můžete spustit v režimu ladění nebo bez ladění z nabídky **Spustit** .

* Kliknutím na tlačítko **Přijmout** vyjádříte souhlas se sledováním. Tato aplikace nesleduje osobní údaje. Kód vygenerovaný šablonou zahrnuje prostředky, které pomůžou splnit [Obecné nařízení o ochraně osobních údajů (GDPR)](xref:security/gdpr).

  ![Stránka domů nebo index](./start-mvc/_static/output_privacy_macos.png)

  Následující obrázek ukazuje aplikaci po přijetí sledování:

  ![Stránka domů nebo index](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

V další části tohoto kurzu se dozvíte o MVC a začněte psát kód.

> [!div class="step-by-step"]
> [Next](adding-controller.md)

::: moniker-end
