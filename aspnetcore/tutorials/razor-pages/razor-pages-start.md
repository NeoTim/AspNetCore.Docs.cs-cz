---
title: Začínáme se stránkami Razor v ASP.NET Core
author: rick-anderson
monikerRange: '>= aspnetcore-2.2'
description: Tato série kurzů ukazuje, jak používat v ASP.NET Core Razor Pages. Zjistěte, jak vytvořit model, generování kódu pro stránky Razor, použít pro přístup k datům Entity Framework Core a SQL Server, vyhledávání, přidat ověřování vstupu a použití migrace k aktualizaci modelu.
ms.author: riande
ms.date: 12/5/2018
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: 1152ebfcee48a46ecd28c941fce32d3fc1e05c41
ms.sourcegitcommit: 9bb58d7c8dad4bbd03419bcc183d027667fefa20
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52861625"
---
# <a name="tutorial-get-started-with-razor-pages-in-aspnet-core"></a>Kurz: Začínáme se stránkami Razor v ASP.NET Core

Podle [Rick Anderson](https://twitter.com/RickAndMSFT)

V tomto kurzu se naučíte se základy vytváření webové aplikace ASP.NET Core Razor Pages.

Aplikaci spravuje databáze filmů produktů. Získáte informace o následujících postupech:

> [!div class="checklist"]
> * Vytvoření webové aplikace stránky Razor.
> * Přidat a generování uživatelského rozhraní modelu.
> * Práce s databází.
> * Přidáte vyhledávání a ověřování.

Na konci máte aplikaci, která můžete spravovat a zobrazit film názvy položek.

[!INCLUDE[](~/includes/rp/download.md)]

## <a name="prerequisites"></a>Požadavky

[!INCLUDE[](~/includes/net-core-prereqs-all-2.2.md)]

## <a name="create-a-razor-web-app"></a>Vytvoření webové aplikace Razor

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Ze sady Visual Studio **souboru** nabídce vyberte možnost **nový** > **projektu**.
* Vytvořte novou webovou aplikaci ASP.NET Core. Pojmenujte projekt **RazorPagesMovie**. Je důležité projekt pojmenujte *RazorPagesMovie* tak obory názvů budou porovnávány názvy při zkopírujete/vložíte kód.
 ![Nová webová aplikace ASP.NET Core](razor-pages-start/_static/np_2.1.png)

* Vyberte **2.2 technologie ASP.NET Core** v rozevíracím seznamu a pak vyberte **webovou aplikaci**.

  ![Nová webová aplikace ASP.NET Core](razor-pages-start/_static/np_2_2.2.png)

  Následující počáteční projekt je vytvořen:

  ![Průzkumník řešení](razor-pages-start/_static/se2.2.png)

* Stisknutím klávesy **Ctrl-F5** spustit bez ladicího programu.

  Visual Studio spustí [služby IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) a spustí aplikaci. Zobrazí se panel Adresa `localhost:port#` a nemít něco podobného `example.com`. Důvodem je, že `localhost` je standardní název hostitele místního počítače. Localhost slouží pouze webové požadavky z místního počítače. Když Visual Studio vytvoří webový projekt, náhodný port se používá pro webový server. Na předchozím obrázku číslo portu je 5001. Při spuštění aplikace se zobrazí jiné číslo portu.

  Spuštění aplikace s **Ctrl + F5** (bez ladění režim) umožňuje provádět změny kódu, uložte soubor, aktualizujte prohlížeč a zobrazení změn kódu. Mnoho vývojářů dávají přednost používání režimu bez ladění a aktualizujte stránku, podívejte se na změny.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Otevřít [integrovaný terminál](https://code.visualstudio.com/docs/editor/integrated-terminal).
* Změňte adresář (`cd`) do složky, která bude obsahovat projektu.
* Spusťte následující příkaz:

   ```console
   dotnet new webapp -o RazorPagesMovie
   code -r RazorPagesMovie
   ```

  * Zobrazí se dialogové okno s **'RazorPagesMovie' chybí požadované prostředky pro sestavení a ladění. Přidat?**  Vyberte **Ano**

  * `dotnet new webapp -o RazorPagesMovie`: vytvoří nový projekt v Razor Pages *RazorPagesMovie* složky.
  * `code -r RazorPagesMovie`: Načte *RazorPagesMovie.csproj* soubor projektu ve Visual Studio Code.

### <a name="launch-the-app"></a>Spuštění aplikace

* Stisknutím klávesy **Ctrl-F5** spustit bez ladicího programu.

  Visual Studio Code spuštění spustí [Kestrel](xref:fundamentals/servers/kestrel), spustí se prohlížeč a přejde na `http://localhost:5001`. Zobrazí se panel Adresa `localhost:port:5001` a nemít něco podobného `example.com`. Důvodem je, že `localhost` je standardní název hostitele místního počítače. Localhost slouží pouze webové požadavky z místního počítače.

  Spuštění aplikace s **Ctrl + F5** (bez ladění režim) umožňuje provádět změny kódu, uložte soubor, aktualizujte prohlížeč a zobrazení změn kódu. Mnoho vývojářů dávají přednost používání režimu bez ladění a aktualizujte stránku, podívejte se na změny.

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

Z terminálu spusťte následující příkazy:

<!-- TODO: update these instruction once mac support 2.2 projects -->

```console
dotnet new webapp -o RazorPagesMovie
cd RazorPagesMovie
dotnet run
```

Předchozí příkazy použití [rozhraní příkazového řádku .NET Core](/dotnet/core/tools/dotnet) k vytvoření a spuštění projektu pro stránky Razor. Otevřete prohlížeč na http://localhost:5000 zobrazení aplikace.

## <a name="open-the-project"></a>Otevřete projekt

Stisknutím kláves Ctrl + C vypnout aplikaci.

Ze sady Visual Studio, vyberte **soubor > Otevřít**a pak vyberte *RazorPagesMovie.csproj* souboru.

### <a name="launch-the-app"></a>Spuštění aplikace

Vyberte **spuštění > Spustit bez ladění** aplikaci spustit. Visual Studio spustí [Kestrel](xref:fundamentals/servers/kestrel), spustí se prohlížeč a přejde na `http://localhost:5001`.

<!-- End of VS tabs -->

---

* Vyberte **přijmout** souhlas sledování. Tato aplikace nesleduje osobní údaje. Tento kód vygenerovanou šablonu obsahuje prostředky, které vám pomohou splnit [obecného Regulation (GDPR)](xref:security/gdpr).

  ![Index nebo Domovská stránka](razor-pages-start/_static/homeGDPR2.2.png)

  Následující obrázek znázorňuje aplikaci po přijetí sledování:

  ![Index nebo Domovská stránka](razor-pages-start/_static/home2.2.png)

## <a name="project-files-and-folders"></a>Projektových souborů a složek

Následující tabulka uvádí soubory a složky v projektu. V tuto chvíli v tomto kurzu *Startup.cs* soubor je nejdůležitější pochopit. Není nutné zkontrolovat každý odkaz níže uvedené. Odkazy jsou uvedeny jako odkaz, pokud potřebujete další informace o souboru nebo složky v projektu.

| Soubor nebo složku              | Účel |
| ----------------- | ------------ |
| *wwwroot* | Obsahuje statické soubory. Zobrazit [statické soubory](xref:fundamentals/static-files). |
| *Stránky* | Složka pro [stránky Razor](xref:razor-pages/index). |
| *appsettings.json* | [Konfigurace](xref:fundamentals/configuration/index) |
| *Program.cs* | [Hostitelé](xref:fundamentals/host/index) aplikace ASP.NET Core.|
| *Startup.cs* | Nakonfiguruje služby a kanál žádosti. Zobrazit [spuštění](xref:fundamentals/startup).|

### <a name="the-pages-folder"></a>Složka stránek

*_Layout.cshtml* soubor obsahuje společné elementy HTML (skripty a šablony stylů) a nastaví rozložení pro aplikaci. Například když kliknete na **RazorPagesMovie**, **Domů**, nebo **ochrany osobních údajů**, se zobrazí stejné prvky. Společné prvky patří navigační nabídce na horní a záhlaví v dolní části okna. Zobrazit [rozložení](xref:mvc/views/layout) Další informace.

*_ViewImports.cshtml* soubor obsahuje direktivy Razor, které jsou importovány do každé stránky Razor. Zobrazit [direktivy import sdílených](xref:mvc/views/layout#importing-shared-directives) Další informace.

*Soubor _ViewStart.cshtml* nastaví stránky Razor `Layout` použít vlastnost *_Layout.cshtml* souboru. Zobrazit [rozložení](xref:mvc/views/layout) Další informace.

*_ValidationScriptsPartial.cshtml* soubor obsahuje odkaz na [jQuery](https://jquery.com/) skripty pro ověření. Když `Create` a `Edit` stránky jsou přidány později v tomto kurzu *_ValidationScriptsPartial.cshtml* soubor se použije.

`Index`, `Error`, a `Privacy` stránky jsou k dispozici:

* `Index`: Spusťte aplikaci.
* `Error`: Zobrazí informace o chybě.
* `Privacy`: Zadejte podrobnosti o zásadách ochrany osobních údajů webu.

Pro účely tohoto kurzu nejsou použity předchozí stránky.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

<a name="f7"></a>
### <a name="use-f7-to-toggle-between-a-razor-page-and-the-pagemodel"></a>Použít F7 přepínat mezi stránky Razor a PageModel

F7 přepíná mezi stránky Razor (*\*.cshtml* soubor) a C# souboru (*\*. cshtml.cs*).

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

<!-- TODO review  Need something in these tabs -->

Podle konvence, stránka Razor (*\*.cshtml* soubor) a přidružené `PageModel` mají stejný název kořenového souboru.

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

Podle konvence, stránka Razor (*\*.cshtml* soubor) a přidružené `PageModel` mají stejný název kořenového souboru.

---

> [!div class="step-by-step"]
> [Další krok: Přidání modelu](xref:tutorials/razor-pages/model)