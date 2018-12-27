---
title: Začínáme s ASP.NET Core MVC
author: rick-anderson
monikerRange: '>= aspnetcore-2.2'
description: Zjistěte, jak začít pracovat s ASP.NET Core MVC.
ms.author: riande
ms.date: 12/12/2018
uid: tutorials/first-mvc-app/start-mvc
ms.openlocfilehash: cfce3b5792a5d0673bae5ddbba9e2d4d515a6279
ms.sourcegitcommit: 4e87712029de2aceb1cf2c52e9e3dda8195a5b8e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/14/2018
ms.locfileid: "53381800"
---
# <a name="get-started-with-aspnet-core-mvc"></a>Začínáme s ASP.NET Core MVC

Podle [Rick Anderson](https://twitter.com/RickAndMSFT)

[!INCLUDE [consider RP](~/includes/razor.md)]

https://docs.microsoft.com/en-us/visualstudio/ide/visual-studio-ide?view=vs-2017

V tomto kurzu se naučíte se základy vytváření webovou aplikaci ASP.NET Core MVC.

Aplikaci spravuje databáze filmů produktů. Získáte informace o následujících postupech:

> [!div class="checklist"]
> * Vytvoření webové aplikace.
> * Přidat a generování uživatelského rozhraní modelu.
> * Práce s databází.
> * Přidáte vyhledávání a ověřování.

Na konci budete mít aplikaci, která můžete spravovat a zobrazit data o filmech.

[!INCLUDE[](~/includes/mvc-intro/download.md)]

> [!NOTE]
> Testujeme použitelnost navrhované nové struktury pro obsah ASP.NET Core.  Pokud máte pár minut a chcete si vyzkoušet cvičení, které spočívá ve vyhledání 7 různých témat v aktuálním nebo navrhovaném obsahu, [klikněte prosím sem a zúčastněte se studie](https://dpk4xbh5.optimalworkshop.com/treejack/aa11wn82).

[!INCLUDE[](~/includes/net-core-prereqs-all-2.2.md)]

## <a name="create-a-web-app"></a>Vytvoření webové aplikace

<!-- VS -------------------------->
# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Ze sady Visual Studio, vyberte **soubor > Nový > projekt**.

![Soubor > Nový > Projekt](start-mvc/_static/alt_new_project.png)

Dokončení **nový projekt** dialogové okno:

* V levém podokně vyberte **.NET Core**
* V prostředním podokně vyberte **webová aplikace ASP.NET Core (.NET Core)**
* Pojmenujte projekt "MvcMovie" (je třeba název projektu "MvcMovie", takže při kopírování kódu bude odpovídat oboru názvů.)
* Vyberte **OK**

![Dialogové okno Nový projekt, .net core v levém podokně, web ASP.NET Core ](start-mvc/_static/new_project2-21.png)

Dokončení **nové základní webové aplikace ASP.NET (.NET Core) – MvcMovie** dialogové okno:

* V poli verze selektoru rozevíracího seznamu vyberte **2.2 technologie ASP.NET Core**
* Vyberte **webová aplikace (Model-View-Controller)**
* Vyberte **OK**.

![Dialogové okno Nový projekt, .net core v levém podokně, web ASP.NET Core ](start-mvc/_static/new_project22-21.png)

Visual Studio používá výchozí šablonu pro projekt MVC, který jste právě vytvořili. Zadáním názvu projektu a výběr několika možností Teď máte funkční aplikaci. Toto je základní počáteční projekt a je dobrým začátkem.

Vyberte **Ctrl-F5** ke spuštění aplikace v režimu bez ladění.

* Visual Studio spustí [služby IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) a spouští vaše aplikace. Všimněte si, že do adresního řádku ukazuje `localhost:port#` a nemít něco podobného `example.com`. Důvodem je, že `localhost` je standardní název hostitele místního počítače. Když Visual Studio vytvoří webový projekt, náhodný port se používá pro webový server. Na obrázku výše je číslo portu 5000. Adresa URL v prohlížeči zobrazí `localhost:5000`. Při spuštění aplikace se zobrazí jiné číslo portu.
* Spuštění aplikace s **Ctrl + F5** (bez ladění režim) umožňuje provádět změny kódu, uložte soubor, aktualizujte prohlížeč a zobrazení změn kódu. Mnoho vývojářů dávají přednost používání režimu bez ladění k rychlému spusťte aplikaci a podívejte se na změny.
* Spustíte ji v ladění nebo v režimu bez ladění z **ladění** položky nabídky:

![Ladění nabídky](start-mvc/_static/debug_menu.png)

* Můžete ladit aplikaci tak, že vyberete **služby IIS Express** tlačítko

![Služba IIS Express](start-mvc/_static/iis_express.png)

<!-- Code -------------------------->
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

### <a name="launch-the-app"></a>Spuštění aplikace

* Stisknutím klávesy **Ctrl-F5** spustit bez ladicího programu.

  Visual Studio Code spuštění spustí [Kestrel](xref:fundamentals/servers/kestrel), spustí se prohlížeč a přejde na `http://localhost:5001`. Zobrazí se panel Adresa `localhost:port:5001` a nemít něco podobného `example.com`. Důvodem je, že `localhost` je standardní název hostitele místního počítače. Localhost slouží pouze webové požadavky z místního počítače.

  Spuštění aplikace s **Ctrl + F5** (bez ladění režim) umožňuje provádět změny kódu, uložte soubor, aktualizujte prohlížeč a zobrazení změn kódu. Mnoho vývojářů dávají přednost používání režimu bez ladění a aktualizujte stránku, podívejte se na změny.

<!-- Mac -------------------------->
# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

* Vyberte **souboru** > **nové řešení**.

  ![macOS nové řešení](~/tutorials/first-web-api-mac/_static/sln.png)

* Vyberte **aplikace .NET Core** > **ASP.NET Core** > **webové aplikace ASP.NET Core (MVC)** > **Další**.

  ![macOS dialogové okno nového projektu](~/tutorials/first-mvc-app-mac/start-mvc/1.png)

* V **nakonfigurovat nové technologie ASP.NET Core webové rozhraní API** dialogového okna, přijměte výchozí nastavení **Cílová architektura** z **.NET Core 2.2*.

* Pojmenujte projekt **MvcMovie**a pak vyberte **vytvořit**.

### <a name="launch-the-app"></a>Spuštění aplikace

Vyberte **spustit** > **spustit bez ladění** aplikaci spustit. Visual Studio pro Mac spustí [Kestrel](xref:fundamentals/servers/index#kestrel) serveru, spustí se prohlížeč a přejde na `http://localhost:port`, kde *port* je číslo portu náhodně vybrané.

* Zobrazí se panel Adresa `localhost:port#` a nemít něco podobného `example.com`. Důvodem je, že `localhost` je standardní název hostitele místního počítače. Když Visual Studio vytvoří webový projekt, náhodný port se používá pro webový server. Při spuštění aplikace se zobrazí jiné číslo portu.
* Spustíte ji v ladění nebo v režimu bez ladění z **spustit** nabídky.

---  
<!-- End of VS tabs -->

* Vyberte **přijmout** souhlas sledování. Tato aplikace nesleduje osobní údaje. Tento kód vygenerovanou šablonu obsahuje prostředky, které vám pomohou splnit [obecného Regulation (GDPR)](xref:security/gdpr).

  ![Index nebo Domovská stránka](start-mvc/_static/privacy.png)

  Následující obrázek znázorňuje aplikaci po přijetí sledování:

  ![Index nebo Domovská stránka](start-mvc/_static/home2.2.png)

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

V další části tohoto kurzu přečtěte si o MVC a začít psát kód.

> [!div class="step-by-step"]
> [Next](adding-controller.md)  
