---
title: Začínáme s ASP.NET Core MVC
author: rick-anderson
description: Zjistěte, jak začít pracovat s ASP.NET Core MVC.
ms.author: riande
ms.date: 04/24/2019
uid: tutorials/first-mvc-app/start-mvc
ms.openlocfilehash: dc3499c89860190b76d6be7b8abeeaef827880d6
ms.sourcegitcommit: a1364109d11d414121a6337b611bee61d6e489e9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2019
ms.locfileid: "66491245"
---
# <a name="get-started-with-aspnet-core-mvc"></a>Začínáme s ASP.NET Core MVC

Podle [Rick Anderson](https://twitter.com/RickAndMSFT)

[!INCLUDE [consider RP](~/includes/razor.md)]

V tomto kurzu se naučíte se základy vytváření webovou aplikaci ASP.NET Core MVC.

Aplikaci spravuje databáze filmů produktů. Získáte informace o následujících postupech:

> [!div class="checklist"]
> * Vytvoření webové aplikace.
> * Přidat a generování uživatelského rozhraní modelu.
> * Práce s databází.
> * Přidáte vyhledávání a ověřování.

Na konci budete mít aplikaci, která můžete spravovat a zobrazit data o filmech.

[!INCLUDE[](~/includes/mvc-intro/download.md)]

[!INCLUDE[](~/includes/net-core-prereqs-all-2.2.md)]

## <a name="create-a-web-app"></a>Vytvoření webové aplikace

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* V sadě Visual Studio vyberte **vytvořte nový projekt**.

* Selecct **webové aplikace ASP.NET Core** a pak vyberte **Další**.

![Nová webová aplikace ASP.NET Core](start-mvc/_static/np_2.1.png)

* Pojmenujte projekt **MvcMovie** a vyberte **vytvořit**. Je důležité projekt pojmenujte **MvcMovie** tak při kopírování kódu budou odpovídat oboru názvů.

  ![Nová webová aplikace ASP.NET Core](start-mvc/_static/config.png)


* Vyberte **webové Application(Model-View-Controller)** a pak vyberte **vytvořit**.

![Dialogové okno Nový projekt, .NET Core v levém podokně, web ASP.NET Core ](start-mvc/_static/new_project22-21.png)

Visual Studio používá výchozí šablonu pro projekt MVC, který jste právě vytvořili. Zadáním názvu projektu a výběr několika možností Teď máte funkční aplikaci. Toto je základní počáteční projekt a je dobrým začátkem.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Kurz předpokládá familarity s VS Code. Zobrazit [Začínáme s VS Code](https://code.visualstudio.com/docs) a [nápovědy Visual Studio Code](#visual-studio-code-help) Další informace.

* Otevřít [integrovaný terminál](https://code.visualstudio.com/docs/editor/integrated-terminal).
* Změňte adresář (`cd`) do složky, která bude obsahovat projektu.
* Spusťte následující příkaz:

   ```console
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * Zobrazí se dialogové okno s **'MvcMovie' chybí požadované prostředky pro sestavení a ladění. Přidat?**  Vyberte **Ano**

  * `dotnet new mvc -o MvcMovie`: vytvoří nový projekt ASP.NET Core MVC v *MvcMovie* složky.
  * `code -r MvcMovie`: Načtení *MvcMovie.csproj* soubor projektu ve Visual Studio Code.

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

* Vyberte **souboru** > **nové řešení**.

  ![macOS nové řešení](./start-mvc/_static/new_project_vsmac.png)

* Vyberte **.NET Core** > **aplikace** > **webová aplikace (Model-View-Controller)**  > **Další**.

  ![macOS dialogové okno nového projektu](./start-mvc/_static/new_project_mvc_vsmac.png)

* V **nakonfigurovat nové technologie ASP.NET Core webové rozhraní API** dialogového okna, přijměte výchozí nastavení **Cílová architektura** z **.NET Core 2.2**.

  ![macOS výběr .NET Core 2.2](./start-mvc/_static/new_project_22_vsmac.png)

* Pojmenujte projekt **MvcMovie**a pak vyberte **vytvořit**.

---

### <a name="run-the-app"></a>Spuštění aplikace

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Vyberte **Ctrl-F5** ke spuštění aplikace v režimu bez ladění.

[!INCLUDE[](~/includes/trustCertVS.md)]

* Visual Studio spustí [služby IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) a spustí aplikaci. Všimněte si, že do adresního řádku ukazuje `localhost:port#` a nemít něco podobného `example.com`. Důvodem je, že `localhost` je standardní název hostitele místního počítače. Když Visual Studio vytvoří webový projekt, náhodný port se používá pro webový server.
* Spuštění aplikace pomocí kombinace kláves Ctrl + F5 (bez ladění režim) umožňuje provádět změny kódu, uložte soubor, aktualizujte prohlížeč a zobrazení změn kódu. Mnoho vývojářů dávají přednost používání režimu bez ladění k rychlému spusťte aplikaci a podívejte se na změny.
* Spustíte ji v ladění nebo v režimu bez ladění z **ladění** položky nabídky:

  ![Ladění nabídky](start-mvc/_static/debug_menu.png)

* Můžete ladit aplikaci tak, že vyberete **služby IIS Express** tlačítko

  ![Služba IIS Express](start-mvc/_static/iis_express.png)

* Vyberte **přijmout** souhlas sledování. Tato aplikace nesleduje osobní údaje. Tento kód vygenerovanou šablonu obsahuje prostředky, které vám pomohou splnit [obecného Regulation (GDPR)](xref:security/gdpr).

  ![Index nebo Domovská stránka](start-mvc/_static/privacy.png)

  Následující obrázek znázorňuje aplikaci po přijetí sledování:

  ![Index nebo Domovská stránka](start-mvc/_static/home2.2.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Stisknutím kláves Ctrl + F5 ke spuštění bez ladicího programu.

[!INCLUDE[](~/includes/trustCertVSC.md)]

  Spustí Visual Studio Code [Kestrel](xref:fundamentals/servers/kestrel), spustí se prohlížeč a přejde na `https://localhost:5001`. Zobrazí se panel Adresa `localhost:port:5001` a nemít něco podobného `example.com`. Důvodem je, že `localhost` je standardní název hostitele místního počítače. Localhost slouží pouze webové požadavky z místního počítače.

  Spuštění aplikace pomocí kombinace kláves Ctrl + F5 (bez ladění režim) umožňuje provádět změny kódu, uložte soubor, aktualizujte prohlížeč a zobrazení změn kódu. Mnoho vývojářů dávají přednost používání režimu bez ladění a aktualizujte stránku, podívejte se na změny.

* Vyberte **přijmout** souhlas sledování. Tato aplikace nesleduje osobní údaje. Tento kód vygenerovanou šablonu obsahuje prostředky, které vám pomohou splnit [obecného Regulation (GDPR)](xref:security/gdpr).

  ![Index nebo Domovská stránka](start-mvc/_static/privacy.png)

  Následující obrázek znázorňuje aplikaci po přijetí sledování:

  ![Index nebo Domovská stránka](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

Vyberte **spustit** > **spustit bez ladění** aplikaci spustit. Visual Studio pro Mac spustí [Kestrel](xref:fundamentals/servers/index#kestrel) serveru, spustí se prohlížeč a přejde na `http://localhost:port`, kde *port* je číslo portu náhodně vybrané.

[!INCLUDE[](~/includes/trustCertMac.md)]

* Zobrazí se panel Adresa `localhost:port#` a nemít něco podobného `example.com`. Důvodem je, že `localhost` je standardní název hostitele místního počítače. Když Visual Studio vytvoří webový projekt, náhodný port se používá pro webový server. Při spuštění aplikace se zobrazí jiné číslo portu.
* Spustíte ji v ladění nebo v režimu bez ladění z **spustit** nabídky.

* Vyberte **přijmout** souhlas sledování. Tato aplikace nesleduje osobní údaje. Tento kód vygenerovanou šablonu obsahuje prostředky, které vám pomohou splnit [obecného Regulation (GDPR)](xref:security/gdpr).

  ![Index nebo Domovská stránka](./start-mvc/_static/output_privacy_macos.png)

  Následující obrázek znázorňuje aplikaci po přijetí sledování:

  ![Index nebo Domovská stránka](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

V další části tohoto kurzu přečtěte si o MVC a začít psát kód.

> [!div class="step-by-step"]
> [Next](adding-controller.md)
