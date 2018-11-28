---
title: Začínáme se stránkami Razor v ASP.NET Core
author: rick-anderson
description: Tato série kurzů ukazuje, jak používat v ASP.NET Core Razor Pages. Zjistěte, jak vytvořit model, generování kódu pro stránky Razor, použít pro přístup k datům Entity Framework Core a SQL Server, vyhledávání, přidat ověřování vstupu a použití migrace k aktualizaci modelu.
ms.author: riande
ms.date: 05/30/2018
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: ca5b134aaa0a9218bc3b0466c3448bd41e8cef8b
ms.sourcegitcommit: e9b99854b0a8021dafabee0db5e1338067f250a9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/28/2018
ms.locfileid: "52450733"
---
# <a name="tutorial-get-started-with-razor-pages-in-aspnet-core"></a>Kurz: Začínáme se stránkami Razor v ASP.NET Core

Podle [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range="= aspnetcore-2.0"

Doporučujeme, abyste že podle ASP.NET Core 2.1 verzi tohoto kurzu. Má **většinu** usnadňuje její sledování a zahrnuje další funkce. Vyberte **ASP.NET Core 2.1** ve výběru verze.

![Volič verze obsahu](razor-pages-start/_static/v21.png)

::: moniker-end

V tomto kurzu se naučíte se základy vytváření webové aplikace ASP.NET Core Razor Pages. Stránky Razor je doporučený postup pro vytváření uživatelského rozhraní pro webové aplikace v ASP.NET Core.

Existují tři verze tohoto kurzu:

* Windows: V tomto kurzu
* MacOS: [Začínáme se stránkami Razor pomocí sady Visual Studio pro Mac](xref:tutorials/razor-pages-mac/razor-pages-start)
* macOS, Linux a Windows: [Začínáme s ASP.NET Core Razor Pages ve Visual Studio Code](xref:tutorials/razor-pages-vsc/razor-pages-start)

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample) ([stažení](xref:index#how-to-download-a-sample))

> [!NOTE]
> Testujeme použitelnost navrhované nové struktury pro obsah ASP.NET Core.  Pokud máte pár minut a chcete si vyzkoušet cvičení, které spočívá ve vyhledání 7 různých témat v aktuálním nebo navrhovaném obsahu, [klikněte prosím sem a zúčastněte se studie](https://dpk4xbh5.optimalworkshop.com/treejack/rps16hd5).

::: moniker range=">= aspnetcore-2.1"

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [Prerequisites](~/includes/net-core-prereqs-windows.md)]

## <a name="create-a-razor-web-app"></a>Vytvoření webové aplikace Razor

* Ze sady Visual Studio **souboru** nabídce vyberte možnost **nový** > **projektu**.
* Vytvořte novou webovou aplikaci ASP.NET Core. Pojmenujte projekt **RazorPagesMovie**. Je důležité projekt pojmenujte *RazorPagesMovie* tak obory názvů budou porovnávány názvy při zkopírujete/vložíte kód.
 ![Nová webová aplikace ASP.NET Core](razor-pages-start/_static/np_2.1.png)
* Vyberte **ASP.NET Core 2.1** v rozevíracím seznamu a pak vyberte **webovou aplikaci**.

 ![Nová webová aplikace ASP.NET Core](razor-pages-start/_static/np_2_2.1.png)

Šablony sady Visual Studio vytvoří počáteční projekt:

![Průzkumník řešení](razor-pages-start/_static/se2.1.png)

Stisknutím klávesy **F5** ke spuštění aplikace v režimu ladění nebo **Ctrl-F5** spustit bez připojení ladicího programu. Vyberte **přijmout** souhlas sledování. Tato aplikace nesleduje osobní údaje. Tento kód vygenerovanou šablonu obsahuje prostředky, které vám pomohou splnit [obecného Regulation (GDPR)](xref:security/gdpr).

![Index nebo Domovská stránka](razor-pages-start/_static/homeGDPR.png)

Následující obrázek znázorňuje aplikaci po přijetí sledování:

![Index nebo Domovská stránka](razor-pages-start/_static/home2.1.png)

* Visual Studio spustí [služby IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) a spustí aplikaci. Zobrazí se panel Adresa `localhost:port#` a nemít něco podobného `example.com`. Důvodem je, že `localhost` je standardní název hostitele místního počítače. Localhost slouží pouze webové požadavky z místního počítače. Když Visual Studio vytvoří webový projekt, náhodný port se používá pro webový server. Na předchozím obrázku číslo portu je 5001. Při spuštění aplikace se zobrazí jiné číslo portu.
* Spuštění aplikace s **Ctrl + F5** (bez ladění režim) umožňuje provádět změny kódu, uložte soubor, aktualizujte prohlížeč a zobrazení změn kódu. Mnoho vývojářů dávají přednost používání režimu bez ladění k rychlému spusťte aplikaci a podívejte se na změny.

[!INCLUDE [razor-pages-start](~/includes/RP/2.1/razor-pages-start.md)]

[!INCLUDE [F7](~/includes/RP/F7.md)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [Prerequisites](~/includes/net-core-prereqs-windows.md)]

## <a name="create-a-razor-web-app"></a>Vytvoření webové aplikace Razor

* Ze sady Visual Studio **souboru** nabídce vyberte možnost **nový** > **projektu**.
* Vytvořte novou webovou aplikaci ASP.NET Core. Pojmenujte projekt **RazorPagesMovie**. Je důležité projekt pojmenujte *RazorPagesMovie* tak obory názvů budou porovnávány názvy při zkopírujete/vložíte kód.
  ![Nová webová aplikace ASP.NET Core](../../razor-pages/index/_static/np.png)
* Vyberte **ASP.NET Core 2.0** v rozevíracím seznamu a pak vyberte **webovou aplikaci**.

  [!INCLUDE [install 2.0](~/includes/dotnetcore-on-dotnetfx-vs.md)]

Šablony sady Visual Studio vytvoří počáteční projekt:

![Průzkumník řešení](razor-pages-start/_static/se.png)

Stisknutím klávesy **F5** ke spuštění aplikace v režimu ladění nebo **Ctrl-F5** spustit bez připojení ladicího programu

![Index nebo Domovská stránka](razor-pages-start/_static/home.png)

* Visual Studio spustí [služby IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) a spouští vaše aplikace. Zobrazí se panel Adresa `localhost:port#` a nemít něco podobného `example.com`. Důvodem je, že `localhost` je standardní název hostitele místního počítače. Localhost slouží pouze webové požadavky z místního počítače. Když Visual Studio vytvoří webový projekt, náhodný port se používá pro webový server. Na předchozím obrázku je číslo portu 5000. Při spuštění aplikace se zobrazí jiné číslo portu.
* Spuštění aplikace s **Ctrl + F5** (bez ladění režim) umožňuje provádět změny kódu, uložte soubor, aktualizujte prohlížeč a zobrazení změn kódu. Mnoho vývojářů dávají přednost používání režimu bez ladění k rychlému spusťte aplikaci a podívejte se na změny.

[!INCLUDE [razor-pages-start](~/includes/RP/razor-pages-start.md)]

::: moniker-end

> [!div class="step-by-step"]
> [Další krok: Přidání modelu](xref:tutorials/razor-pages/model)
