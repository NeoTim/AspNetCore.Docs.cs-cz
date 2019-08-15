---
title: Začínáme s ASP.NET Core MVC
author: rick-anderson
description: Naučte se, jak začít s ASP.NET Core MVC.
ms.author: riande
ms.date: 08/05/2019
uid: tutorials/first-mvc-app/start-mvc
ms.openlocfilehash: 36f4811f876a6e35440445103a1f86ae06b31b6a
ms.sourcegitcommit: 476ea5ad86a680b7b017c6f32098acd3414c0f6c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/14/2019
ms.locfileid: "69022521"
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

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

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

Aplikace Visual Studio použila pro projekt MVC, který jste právě vytvořili, výchozí šablonu. Máte funkční aplikaci hned teď zadáním názvu projektu a výběrem několika možností. Toto je základní počáteční projekt.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Kurz předpokládá familarity s VS Code. Další informace najdete v tématu [Začínáme s vs Code](https://code.visualstudio.com/docs) a [Visual Studio Code nápovědě](#visual-studio-code-help) .

* Otevřít [integrovaný terminál](https://code.visualstudio.com/docs/editor/integrated-terminal).
* Změňte adresáře (`cd`) na složku, která bude obsahovat projekt.
* Spusťte následující příkaz:

   ```console
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * Zobrazí se dialogové okno s **požadovanými prostředky pro sestavení a ladění chybí v ' MvcMovie '. Přidat je?**  Vyberte **Ano**

  * `dotnet new mvc -o MvcMovie`: vytvoří nový projekt ASP.NET Core MVC ve složce *MvcMovie* .
  * `code -r MvcMovie`: Načte soubor projektu *MvcMovie. csproj* do Visual Studio Code.

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

* Vyberte **soubor** > **nové řešení**.

  ![macOS nové řešení](./start-mvc/_static/new_project_vsmac.png)

* Vyberte možnost Webová aplikace **.NET Core** > **App** > **(Model-View-Controller)** > **Next**.

  ![macOS dialogové okno nového projektu](./start-mvc/_static/new_project_mvc_vsmac.png)

* V dialogovém okně **Konfigurace nového ASP.NET Core webového rozhraní API** nastavte **cílovou architekturu** **.NET Core 3,0**.

<!-- 
  ![macOS .NET Core 2.2 selection](./start-mvc/_static/new_project_22_vsmac.png)
-->

* Pojmenujte projekt **MvcMovie**a pak vyberte **vytvořit**.

---

### <a name="run-the-app"></a>Spuštění aplikace

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Vyberte **CTRL + F5** a spusťte aplikaci v režimu bez ladění.

[!INCLUDE[](~/includes/trustCertVS.md)]

* Visual Studio spustí [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) a spustí aplikaci. Všimněte si, že se v `localhost:port#` adresním řádku zobrazuje `example.com`, a ne něco podobného. Důvodem `localhost` je, že je standardní název hostitele pro váš místní počítač. Když Visual Studio vytvoří webový projekt, pro webový server se použije náhodný port.
* Spuštění aplikace pomocí CTRL + F5 (režim bez ladění) umožňuje provádět změny kódu, uložit soubor, aktualizovat prohlížeč a zobrazit změny kódu. Mnoho vývojářů upřednostňuje použít režim bez ladění pro rychlé spuštění aplikace a zobrazení změn.
* Aplikaci můžete spustit v režimu ladění nebo bez ladění z položky nabídky **ladění** :

  ![Nabídka ladění](start-mvc/_static/debug_menu.png)

* Aplikaci můžete ladit tak, že vyberete tlačítko **IIS Express** .

  ![Služba IIS Express](start-mvc/_static/iis_express.png)

  Následující obrázek znázorňuje aplikaci:

  ![Stránka domů nebo index](start-mvc/_static/home2.2.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Stisknutím kombinace kláves CTRL + F5 spustíte bez ladicího programu.

[!INCLUDE[](~/includes/trustCertVSC.md)]

  Visual Studio Code spustí [Kestrel](xref:fundamentals/servers/kestrel), spustí prohlížeč a přejde na `https://localhost:5001`. Panel Adresa se zobrazí `localhost:port:5001` a ne něco jako `example.com`. To je proto `localhost` , že se jedná o standardní název hostitele pro místní počítač. Localhost obsluhuje pouze webové požadavky z místního počítače.

  Spuštění aplikace pomocí CTRL + F5 (režim bez ladění) umožňuje provádět změny kódu, uložit soubor, aktualizovat prohlížeč a zobrazit změny kódu. Mnoho vývojářů upřednostňuje použití režimu bez ladění k aktualizaci stránky a zobrazení změn.

  ![Stránka domů nebo index](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

Kliknutím na **Spustit** > **Spustit bez ladění** spusťte aplikaci. Visual Studio pro Mac spustí [Kestrel](xref:fundamentals/servers/index#kestrel) Server, spustí prohlížeč a přejde na `http://localhost:port`místo, kde *port* je náhodně zvolené číslo portu.

[!INCLUDE[](~/includes/trustCertMac.md)]

* Panel Adresa se zobrazí `localhost:port#` a ne něco jako `example.com`. Důvodem `localhost` je, že je standardní název hostitele pro váš místní počítač. Když Visual Studio vytvoří webový projekt, pro webový server se použije náhodný port. Po spuštění aplikace se zobrazí jiné číslo portu.
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

* Selecct **ASP.NET Core webové aplikace** a pak vyberte **Další**.

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

   ```console
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * Zobrazí se dialogové okno s **požadovanými prostředky pro sestavení a ladění chybí v ' MvcMovie '. Přidat je?**  Vyberte **Ano**

  * `dotnet new mvc -o MvcMovie`: vytvoří nový projekt ASP.NET Core MVC ve složce *MvcMovie* .
  * `code -r MvcMovie`: Načte soubor projektu *MvcMovie. csproj* do Visual Studio Code.

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

* Vyberte **soubor** > **nové řešení**.

  ![macOS nové řešení](./start-mvc/_static/new_project_vsmac.png)

* Vyberte možnost Webová aplikace **.NET Core** > **App** > **(Model-View-Controller)** > **Next**.

  ![macOS dialogové okno nového projektu](./start-mvc/_static/new_project_mvc_vsmac.png)

* V dialogovém okně **Konfigurace nového ASP.NET Core webového rozhraní API** přijměte výchozí **cílovou architekturu** **.NET Core 2,2**.

  ![macOS .NET Core 2,2 – výběr](./start-mvc/_static/new_project_22_vsmac.png)

* Pojmenujte projekt **MvcMovie**a pak vyberte **vytvořit**.

---

### <a name="run-the-app"></a>Spuštění aplikace

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Vyberte **CTRL + F5** a spusťte aplikaci v režimu bez ladění.

[!INCLUDE[](~/includes/trustCertVS.md)]

* Visual Studio spustí [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) a spustí aplikaci. Všimněte si, že se v `localhost:port#` adresním řádku zobrazuje `example.com`, a ne něco podobného. Důvodem `localhost` je, že je standardní název hostitele pro váš místní počítač. Když Visual Studio vytvoří webový projekt, pro webový server se použije náhodný port.
* Spuštění aplikace pomocí CTRL + F5 (režim bez ladění) umožňuje provádět změny kódu, uložit soubor, aktualizovat prohlížeč a zobrazit změny kódu. Mnoho vývojářů upřednostňuje použít režim bez ladění pro rychlé spuštění aplikace a zobrazení změn.
* Aplikaci můžete spustit v režimu ladění nebo bez ladění z položky nabídky **ladění** :

  ![Nabídka ladění](start-mvc/_static/debug_menu.png)

* Aplikaci můžete ladit tak, že vyberete tlačítko **IIS Express** .

  ![Služba IIS Express](start-mvc/_static/iis_express.png)

* Vyberte **přijmout** pro vyjádření souhlasu se sledováním. Tato aplikace nesleduje osobní údaje. Kód vygenerovaný šablonou zahrnuje prostředky, které pomůžou splnit [Obecné nařízení o ochraně osobních údajů (GDPR)](xref:security/gdpr).

  ![Stránka domů nebo index](start-mvc/_static/privacy.png)

  Následující obrázek ukazuje aplikaci po přijetí sledování:

  ![Stránka domů nebo index](start-mvc/_static/home2.2.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Stisknutím kombinace kláves CTRL + F5 spustíte bez ladicího programu.

[!INCLUDE[](~/includes/trustCertVSC.md)]

  Visual Studio Code spustí [Kestrel](xref:fundamentals/servers/kestrel), spustí prohlížeč a přejde na `https://localhost:5001`. Panel Adresa se zobrazí `localhost:port:5001` a ne něco jako `example.com`. To je proto `localhost` , že se jedná o standardní název hostitele pro místní počítač. Localhost obsluhuje pouze webové požadavky z místního počítače.

  Spuštění aplikace pomocí CTRL + F5 (režim bez ladění) umožňuje provádět změny kódu, uložit soubor, aktualizovat prohlížeč a zobrazit změny kódu. Mnoho vývojářů upřednostňuje použití režimu bez ladění k aktualizaci stránky a zobrazení změn.

* Vyberte **přijmout** pro vyjádření souhlasu se sledováním. Tato aplikace nesleduje osobní údaje. Kód vygenerovaný šablonou zahrnuje prostředky, které pomůžou splnit [Obecné nařízení o ochraně osobních údajů (GDPR)](xref:security/gdpr).

  ![Stránka domů nebo index](start-mvc/_static/privacy.png)

  Následující obrázek ukazuje aplikaci po přijetí sledování:

  ![Stránka domů nebo index](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

Kliknutím na **Spustit** > **Spustit bez ladění** spusťte aplikaci. Visual Studio pro Mac spustí [Kestrel](xref:fundamentals/servers/index#kestrel) Server, spustí prohlížeč a přejde na `http://localhost:port`místo, kde *port* je náhodně zvolené číslo portu.

[!INCLUDE[](~/includes/trustCertMac.md)]

* Panel Adresa se zobrazí `localhost:port#` a ne něco jako `example.com`. Důvodem `localhost` je, že je standardní název hostitele pro váš místní počítač. Když Visual Studio vytvoří webový projekt, pro webový server se použije náhodný port. Po spuštění aplikace se zobrazí jiné číslo portu.
* Aplikaci můžete spustit v režimu ladění nebo bez ladění z nabídky **Spustit** .

* Vyberte **přijmout** pro vyjádření souhlasu se sledováním. Tato aplikace nesleduje osobní údaje. Kód vygenerovaný šablonou zahrnuje prostředky, které pomůžou splnit [Obecné nařízení o ochraně osobních údajů (GDPR)](xref:security/gdpr).

  ![Stránka domů nebo index](./start-mvc/_static/output_privacy_macos.png)

  Následující obrázek ukazuje aplikaci po přijetí sledování:

  ![Stránka domů nebo index](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

V další části tohoto kurzu se dozvíte o MVC a začněte psát kód.

> [!div class="step-by-step"]
> [Next](adding-controller.md)

::: moniker-end
