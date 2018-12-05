---
title: Začínáme s ASP.NET Core MVC a sady Visual Studio
author: rick-anderson
description: Zjistěte, jak začít pracovat s ASP.NET Core MVC a sady Visual Studio.
ms.author: riande
ms.date: 10/07/2017
uid: tutorials/first-mvc-app/start-mvc
ms.openlocfilehash: 738c49272c2ae2b075866001f06ad09fe73969f9
ms.sourcegitcommit: 9bb58d7c8dad4bbd03419bcc183d027667fefa20
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52862197"
---
# <a name="get-started-with-aspnet-core-mvc-and-visual-studio"></a>Začínáme s ASP.NET Core MVC a sady Visual Studio

Podle [Rick Anderson](https://twitter.com/RickAndMSFT)

[!INCLUDE [consider RP](~/includes/razor.md)]

Existují 3 verze tohoto kurzu:

* macOS: [vytvoření aplikace ASP.NET Core MVC se sadou Visual Studio pro Mac](xref:tutorials/first-mvc-app-mac/start-mvc)
* Windows: [vytvořit v aplikaci MVC ASP.NET Core pomocí sady Visual Studio](xref:tutorials/first-mvc-app/start-mvc)
* macOS, Linux a Windows: [vytvoření aplikace ASP.NET Core MVC pomocí Visual Studio Code](xref:tutorials/first-mvc-app-xplat/start-mvc)

> [!NOTE]
> Testujeme použitelnost navrhované nové struktury pro obsah ASP.NET Core.  Pokud máte pár minut a chcete si vyzkoušet cvičení, které spočívá ve vyhledání 7 různých témat v aktuálním nebo navrhovaném obsahu, [klikněte prosím sem a zúčastněte se studie](https://dpk4xbh5.optimalworkshop.com/treejack/aa11wn82).

## <a name="install-visual-studio-and-net-core"></a>Instalace sady Visual Studio a .NET Core

::: moniker range=">= aspnetcore-2.1"

[!INCLUDE [](~/includes/net-core-prereqs-windows.md)]

## <a name="create-a-web-app"></a>Vytvoření webové aplikace

Ze sady Visual Studio, vyberte **soubor > Nový > projekt**.

![Soubor > Nový > Projekt](start-mvc/_static/alt_new_project.png)

Dokončení **nový projekt** dialogové okno:

* V levém podokně, klepněte na **.NET Core**
* V prostředním podokně, klepněte na **webová aplikace ASP.NET Core (.NET Core)**
* Pojmenujte projekt "MvcMovie" (je třeba název projektu "MvcMovie", takže při kopírování kódu bude odpovídat oboru názvů.)
* Klepněte na **OK**

![Dialogové okno Nový projekt, .net core v levém podokně, web ASP.NET Core ](start-mvc/_static/new_project2-21.png)

Dokončení **nové základní webové aplikace ASP.NET (.NET Core) – MvcMovie** dialogové okno:

* V poli verze selektoru rozevíracího seznamu vyberte **ASP.NET Core 2.1**
* Vyberte **webová aplikace (Model-View-Controller)**
* Klepněte na **OK**.

![Dialogové okno Nový projekt, .net core v levém podokně, web ASP.NET Core ](start-mvc/_static/new_project22-21.png)

Visual Studio používá výchozí šablonu pro projekt MVC, který jste právě vytvořili. Zadáním názvu projektu a výběr několika možností Teď máte funkční aplikaci. Toto je základní počáteční projekt a je dobrým začátkem.

Klepněte na **F5** ke spuštění aplikace v režimu ladění nebo **Ctrl-F5** v režimu bez ladění.
<!-- These images are also used by uid: tutorials/first-mvc-app-xplat/start-mvc -->
![spuštění aplikace](start-mvc/_static/1.png)

* Visual Studio spustí [služby IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) a spouští vaše aplikace. Všimněte si, že do adresního řádku ukazuje `localhost:port#` a nemít něco podobného `example.com`. Důvodem je, že `localhost` je standardní název hostitele místního počítače. Když Visual Studio vytvoří webový projekt, náhodný port se používá pro webový server. Na obrázku výše je číslo portu 5000. Adresa URL v prohlížeči zobrazí `localhost:5000`. Při spuštění aplikace se zobrazí jiné číslo portu.
* Spuštění aplikace s **Ctrl + F5** (bez ladění režim) umožňuje provádět změny kódu, uložte soubor, aktualizujte prohlížeč a zobrazení změn kódu. Mnoho vývojářů dávají přednost používání režimu bez ladění k rychlému spusťte aplikaci a podívejte se na změny.
* Spustíte ji v ladění nebo v režimu bez ladění z **ladění** položky nabídky:

![Ladění nabídky](start-mvc/_static/debug_menu.png)

* Můžete ladit aplikaci klepnutím **služby IIS Express** tlačítko

![Služba IIS Express](start-mvc/_static/iis_express.png)

Výchozí šablony vám práci **domácí o** a **kontakt** odkazy. Výše uvedené prohlížeče obraz se nezobrazí těchto odkazů. V závislosti na velikosti vašeho prohlížeče mohou muset kliknout na ikonu navigace se budou zobrazovat.

![Ikona navigace v vpravo nahoře](start-mvc/_static/2.png)

Pokud jsou spuštěné v režimu ladění, klepněte na **Shift + F5** chcete zastavit ladění.

V další části tohoto kurzu přidáme další informace o MVC a začít psát kód.

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!INCLUDE [](~/includes/net-core-prereqs.md)]

## <a name="create-a-web-app"></a>Vytvoření webové aplikace

Ze sady Visual Studio, vyberte **soubor > Nový > projekt**.

![Soubor > Nový > Projekt](start-mvc/_static/alt_new_project.png)

Dokončení **nový projekt** dialogové okno:

* V levém podokně, klepněte na **.NET Core**
* V prostředním podokně, klepněte na **webová aplikace ASP.NET Core (.NET Core)**
* Pojmenujte projekt "MvcMovie" (je třeba název projektu "MvcMovie", takže při kopírování kódu bude odpovídat oboru názvů.)
* Klepněte na **OK**

![Dialogové okno Nový projekt, .net core v levém podokně, web ASP.NET Core ](start-mvc/_static/new_project2.png)

Dokončení **nové základní webové aplikace ASP.NET (.NET Core) – MvcMovie** dialogové okno:

* V poli verze selektoru rozevíracího seznamu vyberte **ASP.NET Core 2.-**
* Vyberte **webové Application(Model-View-Controller)**
* Klepněte na **OK**.

![Dialogové okno Nový projekt, .net core v levém podokně, web ASP.NET Core ](start-mvc/_static/new_project22.png)

Visual Studio používá výchozí šablonu pro projekt MVC, který jste právě vytvořili. Zadáním názvu projektu a výběr několika možností Teď máte funkční aplikaci. Toto je základní počáteční projekt, a je vhodné oddělení na zahájení,

Klepněte na **F5** ke spuštění aplikace v režimu ladění nebo **Ctrl-F5** v režimu bez ladění.
<!-- These images are also used by uid: tutorials/first-mvc-app-xplat/start-mvc -->
![spuštění aplikace](start-mvc/_static/1.png)

* Visual Studio spustí [služby IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) a spouští vaše aplikace. Všimněte si, že do adresního řádku ukazuje `localhost:port#` a nemít něco podobného `example.com`. Důvodem je, že `localhost` je standardní název hostitele místního počítače. Když Visual Studio vytvoří webový projekt, náhodný port se používá pro webový server. Na obrázku výše je číslo portu 5000. Adresa URL v prohlížeči zobrazí `localhost:5000`. Při spuštění aplikace se zobrazí jiné číslo portu.
* Spuštění aplikace s **Ctrl + F5** (bez ladění režim) umožňuje provádět změny kódu, uložte soubor, aktualizujte prohlížeč a zobrazení změn kódu. Mnoho vývojářů dávají přednost používání režimu bez ladění k rychlému spusťte aplikaci a podívejte se na změny.
* Spustíte ji v ladění nebo v režimu bez ladění z **ladění** položky nabídky:

![Ladění nabídky](start-mvc/_static/debug_menu.png)

* Můžete ladit aplikaci klepnutím **služby IIS Express** tlačítko

![Služba IIS Express](start-mvc/_static/iis_express.png)

Výchozí šablony vám práci **domácí o** a **kontakt** odkazy. Výše uvedené prohlížeče obraz se nezobrazí těchto odkazů. V závislosti na velikosti vašeho prohlížeče mohou muset kliknout na ikonu navigace se budou zobrazovat.

![Ikona navigace v vpravo nahoře](start-mvc/_static/2.png)

Pokud jsou spuštěné v režimu ladění, klepněte na **Shift + F5** chcete zastavit ladění.

V další části tohoto kurzu přidáme další informace o MVC a začít psát kód.

::: moniker-end

> [!div class="step-by-step"]
> [Next](adding-controller.md)  
