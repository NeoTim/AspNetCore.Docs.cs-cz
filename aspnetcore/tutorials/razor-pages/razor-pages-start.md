---
title: 'Kurz: Začínáme se stránkami Razor v ASP.NET Core'
author: rick-anderson
description: Tato série kurzů ukazuje, jak používat v ASP.NET Core Razor Pages. Zjistěte, jak vytvořit model, generování kódu pro stránky Razor, použít pro přístup k datům Entity Framework Core a SQL Server, vyhledávání, přidat ověřování vstupu a použití migrace k aktualizaci modelu.
ms.author: riande
ms.date: 12/5/2018
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: 929bc72b16e302a5018038bc449704b7078dd33a
ms.sourcegitcommit: 6548c19f345850ee22b50f7ef9fca732895d9e08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/14/2018
ms.locfileid: "53425078"
---
# <a name="tutorial-get-started-with-razor-pages-in-aspnet-core"></a>Kurz: Začínáme se stránkami Razor v ASP.NET Core

Podle [Rick Anderson](https://twitter.com/RickAndMSFT)

Toto je první kurz z řady. [Série](xref:tutorials/razor-pages/index) vás naučí základy vytváření webové aplikace ASP.NET Core Razor Pages. Na konci série budete mít aplikaci, která spravuje databázi videa.  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření webové aplikace stránky Razor.
> * Spusťte aplikaci.
> * Zkontrolujte soubory projektu.

Na konci tohoto kurzu budete mít funkční webové aplikace stránky Razor, na kterém budete stavět v budoucích kurzech.

[Index nebo Domovská stránka](razor-pages-start/_static/home2.2.png)

[!INCLUDE[](~/includes/net-core-prereqs-all-2.2.md)]

## <a name="create-a-razor-pages-web-app"></a>Vytvoření webové aplikace stránky Razor

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Ze sady Visual Studio **souboru** nabídce vyberte možnost **nový** > **projektu**.

* Vytvořte novou webovou aplikaci ASP.NET Core. Pojmenujte projekt **RazorPagesMovie**. Je důležité projekt pojmenujte *RazorPagesMovie* tak obory názvů budou porovnávány názvy při zkopírujte a vložte kód.

  ![Nová webová aplikace ASP.NET Core](razor-pages-start/_static/np_2.1.png)

* Vyberte **2.2 technologie ASP.NET Core** v rozevíracím seznamu a pak vyberte **webovou aplikaci**.

  ![Nová webová aplikace ASP.NET Core](razor-pages-start/_static/np_2_2.2.png)

  Následující počáteční projekt je vytvořen:

  ![Průzkumník řešení](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Otevřít [integrovaný terminál](https://code.visualstudio.com/docs/editor/integrated-terminal).

* Změňte adresář (`cd`) do složky, která bude obsahovat projektu.

* Spusťte následující příkazy:

  ```console
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * `dotnet new` Příkaz vytvoří nový projekt v Razor Pages *RazorPagesMovie* složky.
  * `code` Příkaz otevře *RazorPagesMovie* složky v nové instanci sady Visual Studio Code.

  Zobrazí se dialogové okno s **'RazorPagesMovie' chybí požadované prostředky pro sestavení a ladění. Přidat?**

* Vyberte **Ano**

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

Z terminálu spusťte následující příkazy:

<!-- TODO: update these instruction once mac support 2.2 projects -->

```console
dotnet new webapp -o RazorPagesMovie
cd RazorPagesMovie
```

Předchozí příkazy použití [rozhraní příkazového řádku .NET Core](/dotnet/core/tools/dotnet) vytvoření projektu pro stránky Razor.

## <a name="open-the-project"></a>Otevřete projekt

Ze sady Visual Studio, vyberte **soubor > Otevřít**a pak vyberte *RazorPagesMovie.csproj* souboru.

<!-- End of VS tabs -->

---

## <a name="run-the-web-app"></a>Spuštění webové aplikace

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Stisknutím kláves Ctrl + F5 ke spuštění bez ladicího programu.

  Visual Studio spustí [služby IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) a spustí aplikaci. Zobrazí se panel Adresa `localhost:port#` a nemít něco podobného `example.com`. Důvodem je, že `localhost` je standardní název hostitele místního počítače. Localhost slouží pouze webové požadavky z místního počítače. Když Visual Studio vytvoří webový projekt, náhodný port se používá pro webový server. Na předchozím obrázku číslo portu je 5001. Při spuštění aplikace se zobrazí jiné číslo portu.
  
# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Stisknutím klávesy **Ctrl-F5** spustit bez ladicího programu.

  Visual Studio Code spuštění spustí [Kestrel](xref:fundamentals/servers/kestrel), spustí se prohlížeč a přejde na `http://localhost:5001`. Zobrazí se panel Adresa `localhost:port#` a nemít něco podobného `example.com`. Důvodem je, že `localhost` je standardní název hostitele místního počítače. Localhost slouží pouze webové požadavky z místního počítače.
  
# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

Vyberte **spuštění > Spustit bez ladění** aplikaci spustit. Visual Studio spustí [Kestrel](xref:fundamentals/servers/kestrel), spustí se prohlížeč a přejde na `http://localhost:5001`.

<!-- End of VS tabs -->

---

* Na domovské stránce aplikace vyberte **přijmout** souhlas sledování.

  Tato aplikace nesleduje osobní údaje, ale šablona projektu zahrnuje funkci pro vyjádření souhlasu v případě, že potřebujete v souladu s Evropské unie [obecného Regulation (GDPR)](xref:security/gdpr).

  ![Index nebo Domovská stránka](razor-pages-start/_static/homeGDPR2.2.png)

  Následující obrázek znázorňuje aplikaci po udělení souhlasu doplňku sledování:

  ![Index nebo Domovská stránka](razor-pages-start/_static/home2.2.png)

## <a name="examine-the-project-files"></a>Zkontrolujte soubory projektu

Tady je přehled hlavní projekt složek a souborů, které budete pracovat v budoucích kurzech.

### <a name="pages-folder"></a>Složka stránek

Obsahuje Razor pages a podpůrné soubory. Každá stránka Razor je pár souborů:

* A *.cshtml* soubor, který obsahuje kód HTML s C# kód používající syntaxi Razor.
* A *. cshtml.cs* soubor, který obsahuje C# kód, který zpracovává události stránky.

Podpůrné soubory mají názvy, které začínají znakem podtržítka. Například *_Layout.cshtml* soubor nastaví prvky uživatelského rozhraní, které jsou společné pro všechny stránky. Tento soubor nastaví navigační nabídce v horní části stránky a o autorských právech v dolní části stránky. Další informace naleznete v tématu <xref:mvc/views/layout>.


### <a name="wwwroot-folder"></a>složku Wwwroot

Obsahuje statické soubory, jako jsou soubory HTML, soubory jazyka JavaScript a soubory šablon stylů CSS. Další informace naleznete v tématu <xref:fundamentals/static-files>.

### <a name="appsettingsjson"></a>appSettings.json

Obsahuje konfigurační data, jako je například připojovací řetězce. Další informace naleznete v tématu <xref:fundamentals/configuration/index>.

### <a name="programcs"></a>Program.cs

Obsahuje vstupní bod programu. Další informace naleznete v tématu <xref:fundamentals/host/web-host>.

### <a name="startupcs"></a>Startup.cs

Obsahuje kód, který nakonfiguruje chování aplikace, jako je například jestli vyžaduje souhlas pro soubory cookie. Další informace naleznete v tématu <xref:fundamentals/startup>.

## <a name="next-steps"></a>Další kroky

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření webové aplikace stránky Razor.
> * Spuštění aplikace.
> * Prozkoumat soubory projektu.

Přejděte k dalšímu kurzu v této sérii:

> [!div class="step-by-step"]
> [Přidání modelu](xref:tutorials/razor-pages/model)
