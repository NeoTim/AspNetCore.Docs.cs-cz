---
title: 'Kurz: Začínáme s Razor Pages v ASP.NET Core'
author: rick-anderson
description: V této sérii kurzů se dozvíte, jak používat Razor Pages v ASP.NET Core. Naučte se, jak vytvořit model, generovat kód pro stránky Razor, použít Entity Framework Core a SQL Server pro přístup k datům, přidat funkce hledání, přidat ověření vstupu a použít migrace k aktualizaci modelu.
ms.author: riande
ms.date: 07/25/2019
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: 1605197188d97f27a884739a72400da2d5818b1a
ms.sourcegitcommit: 849af69ee3c94cdb9fd8fa1f1bb8f5a5dda7b9eb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/22/2019
ms.locfileid: "68371997"
---
# <a name="tutorial-get-started-with-razor-pages-in-aspnet-core"></a>Kurz: Začínáme s Razor Pages v ASP.NET Core

Podle [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"
Toto je první kurz série, který se učí základy vytváření ASP.NET Core webové aplikace Razor Pages.

[!INCLUDE[](~/includes/advancedRP.md)]

Na konci série budete mít aplikaci, která spravuje databázi filmů.  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvořte Razor Pages webovou aplikaci.
> * Spusťte aplikaci.
> * Prověřte soubory projektu.

Na konci tohoto kurzu budete mít funkční Razor Pages webovou aplikaci, kterou vytvoříte v pozdějších kurzech.

![Stránka domů nebo index](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a>Požadavky

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="create-a-razor-pages-web-app"></a>Vytvoření webové aplikace v Razor Pages

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Ze sady Visual Studio **souboru** nabídce vyberte možnost **nový** > **projektu**.
* Vytvořte novou ASP.NET Core webovou aplikaci a vyberte **Další**.
  ![Nová ASP.NET Core webové aplikace](razor-pages-start/_static/np_2.1.png)
* Pojmenujte projekt **RazorPagesMovie**. Je důležité pojmenovat projekt *RazorPagesMovie* , aby se obory názvů shodovaly při kopírování a vkládání kódu.
  ![Nová ASP.NET Core webové aplikace](razor-pages-start/_static/config.png)

* V rozevíracím seznamu vyberte položku **ASP.NET Core 3,0** a potom vyberte **vytvořit**.

![Nová ASP.NET Core webové aplikace](razor-pages-start/_static/3/npx.png)

  Vytvoří se následující počáteční projekt:

  ![Průzkumník řešení](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Otevřít [integrovaný terminál](https://code.visualstudio.com/docs/editor/integrated-terminal).

* Přejděte do adresáře (`cd`), který bude obsahovat projekt.

* Spusťte následující příkazy:

  ```console
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * Příkaz vytvoří nový projekt Razor Pages ve složce RazorPagesMovie.  `dotnet new`
  * Příkaz otevře složku RazorPagesMovie v aktuální instanci Visual Studio Code.  `code`

* Až se ikona plamene u OmniSharp stavového řádku změní na zelenou **, dialogové okno požádá o požadované prostředky k sestavení a ladění chybí v ' RazorPagesMovie '. Přidat je?** Vyberte **Ano**.

  Do kořenového adresáře projektu se přidá adresář *. VSCode* obsahující soubory *Launch. JSON* a *Tasks. JSON* .

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

Z terminálu spusťte následující příkaz:

<!-- TODO: update these instruction once mac support 2.2 projects -->

```console
dotnet new webapp -o RazorPagesMovie
```

Předchozí příkazy používají [.NET Core CLI](/dotnet/core/tools/dotnet) k vytvoření projektu Razor Pages.

## <a name="open-the-project"></a>Otevřít projekt

V aplikaci Visual Studio vyberte **soubor > otevřít**a potom vyberte soubor *RazorPagesMovie. csproj* .

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a>Spuštění aplikace

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Stisknutím kombinace kláves CTRL + F5 spustíte bez ladicího programu.

  [!INCLUDE[](~/includes/trustCertVS.md)]

  Visual Studio spustí [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) a spustí aplikaci. Panel Adresa se zobrazí `localhost:port#` a ne něco jako `example.com`. Důvodem `localhost` je, že je standardní název hostitele pro místní počítač. Localhost obsluhuje pouze webové požadavky z místního počítače. Když Visual Studio vytvoří webový projekt, pro webový server se použije náhodný port.
 
# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* Stisknutím **kombinace kláves CTRL + F5** spustíte bez ladicího programu.

  Visual Studio Code spustí [Kestrel](xref:fundamentals/servers/kestrel), spustí prohlížeč a přejde na `http://localhost:5001`. Panel Adresa se zobrazí `localhost:port#` a ne něco jako `example.com`. To je proto `localhost` , že se jedná o standardní název hostitele pro místní počítač. Localhost obsluhuje pouze webové požadavky z místního počítače.

  
# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* Stisknutím klávesy **cmd a opt-F5** spusťte bez ladicího programu.

  Visual Studio spustí [Kestrel](xref:fundamentals/servers/kestrel), spustí prohlížeč a přejde na `http://localhost:5001`.

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a>Prověřte soubory projektu

Tady je přehled hlavních složek projektu a souborů, se kterými budete pracovat v pozdějších kurzech.

### <a name="pages-folder"></a>Složka stránky

Obsahuje stránky Razor a podpůrné soubory. Každá stránka Razor je pár souborů:

* Soubor *. cshtml* , který obsahuje značku HTML s C# kódem pomocí syntaxe Razor.
* Soubor *. cshtml.cs* obsahující C# kód, který zpracovává události stránky.

Podpůrné soubory mají názvy začínající podtržítkem. Například soubor *_Layout. cshtml* nakonfiguruje prvky uživatelského rozhraní společné pro všechny stránky. Tento soubor nastaví navigační nabídku v horní části stránky a oznámení o autorských právech v dolní části stránky. Další informace naleznete v tématu <xref:mvc/views/layout>.

### <a name="wwwroot-folder"></a>Složka wwwroot

Obsahuje statické soubory, jako jsou soubory HTML, JavaScriptové soubory a soubory CSS. Další informace naleznete v tématu <xref:fundamentals/static-files>.

### <a name="appsettingsjson"></a>appSettings.json

Obsahuje konfigurační data, například připojovací řetězce. Další informace naleznete v tématu <xref:fundamentals/configuration/index>.

### <a name="programcs"></a>Program.cs

Obsahuje vstupní bod pro program. Další informace naleznete v tématu <xref:fundamentals/host/generic-host>.

### <a name="startupcs"></a>Startup.cs

Obsahuje kód, který konfiguruje chování aplikace. Další informace naleznete v tématu <xref:fundamentals/startup>.

## <a name="next-steps"></a>Další postup

Přejděte k dalšímu kurzu v řadě:

> [!div class="step-by-step"]
> [Přidání modelu](xref:tutorials/razor-pages/model)

::: moniker-end

<!--::: moniker range=">= aspnetcore-3.0" -->

::: moniker range="< aspnetcore-3.0"

Toto je první kurz řady. [Série](xref:tutorials/razor-pages/index) se učí základy vytváření ASP.NET Core webové aplikace Razor Pages.

[!INCLUDE[](~/includes/advancedRP.md)]

Na konci série budete mít aplikaci, která spravuje databázi filmů.  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvořte Razor Pages webovou aplikaci.
> * Spusťte aplikaci.
> * Prověřte soubory projektu.

Na konci tohoto kurzu budete mít funkční Razor Pages webovou aplikaci, kterou vytvoříte v pozdějších kurzech.

![Stránka domů nebo index](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a>Požadavky

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-razor-pages-web-app"></a>Vytvoření webové aplikace v Razor Pages

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Ze sady Visual Studio **souboru** nabídce vyberte možnost **nový** > **projektu**.

* Vytvořte novou ASP.NET Core webovou aplikaci a vyberte **Další**.

  ![Nová ASP.NET Core webové aplikace](razor-pages-start/_static/np_2.1.png)

* Pojmenujte projekt **RazorPagesMovie**. Je důležité pojmenovat projekt *RazorPagesMovie* , aby se obory názvů shodovaly při kopírování a vkládání kódu.

  ![Nová ASP.NET Core webové aplikace](razor-pages-start/_static/config.png)

* V rozevíracím seznamu vyberte položku **ASP.NET Core 2,2** a potom vyberte **vytvořit**.

![Nová ASP.NET Core webové aplikace](razor-pages-start/_static/np_2_2.2.png)

  Vytvoří se následující počáteční projekt:

  ![Průzkumník řešení](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Otevřít [integrovaný terminál](https://code.visualstudio.com/docs/editor/integrated-terminal).

* Přejděte do adresáře (`cd`), který bude obsahovat projekt.

* Spusťte následující příkazy:

  ```console
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * Příkaz vytvoří nový projekt Razor Pages ve složce RazorPagesMovie.  `dotnet new`
  * Příkaz otevře složku RazorPagesMovie v aktuální instanci Visual Studio Code.  `code`

* Až se ikona plamene u OmniSharp stavového řádku změní na zelenou **, dialogové okno požádá o požadované prostředky k sestavení a ladění chybí v ' RazorPagesMovie '. Přidat je?** Vyberte **Ano**.

  Do kořenového adresáře projektu se přidá adresář *. VSCode* obsahující soubory *Launch. JSON* a *Tasks. JSON* .

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

Z terminálu spusťte následující příkaz:

<!-- TODO: update these instruction once mac support 2.2 projects -->

```console
dotnet new webapp -o RazorPagesMovie
```

Předchozí příkazy používají [.NET Core CLI](/dotnet/core/tools/dotnet) k vytvoření projektu Razor Pages.

## <a name="open-the-project"></a>Otevřít projekt

V aplikaci Visual Studio vyberte **soubor > otevřít**a potom vyberte soubor *RazorPagesMovie. csproj* .

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a>Spuštění aplikace

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Stisknutím kombinace kláves CTRL + F5 spustíte bez ladicího programu.

  [!INCLUDE[](~/includes/trustCertVS.md)]

  Visual Studio spustí [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) a spustí aplikaci. Panel Adresa se zobrazí `localhost:port#` a ne něco jako `example.com`. Důvodem `localhost` je, že je standardní název hostitele pro místní počítač. Localhost obsluhuje pouze webové požadavky z místního počítače. Když Visual Studio vytvoří webový projekt, pro webový server se použije náhodný port.

* Na domovské stránce aplikace vyberte **přijmout** pro vyjádření souhlasu se sledováním.

  Tato aplikace nesleduje osobní údaje, ale šablona projektu zahrnuje funkci souhlasu pro případ, že ji budete potřebovat pro splnění Obecné nařízení o ochraně osobních údajů Evropské unie [(GDPR)](xref:security/gdpr).

  ![Stránka domů nebo index](razor-pages-start/_static/homeGDPR2.2.png)

  Následující obrázek zobrazí aplikaci po poskytnutí souhlasu se sledováním:

  ![Stránka domů nebo index](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* Stisknutím **kombinace kláves CTRL + F5** spustíte bez ladicího programu.

  Visual Studio Code spustí [Kestrel](xref:fundamentals/servers/kestrel), spustí prohlížeč a přejde na `http://localhost:5001`. Panel Adresa se zobrazí `localhost:port#` a ne něco jako `example.com`. To je proto `localhost` , že se jedná o standardní název hostitele pro místní počítač. Localhost obsluhuje pouze webové požadavky z místního počítače.

* Na domovské stránce aplikace vyberte **přijmout** pro vyjádření souhlasu se sledováním.

  Tato aplikace nesleduje osobní údaje, ale šablona projektu zahrnuje funkci souhlasu pro případ, že ji budete potřebovat pro splnění Obecné nařízení o ochraně osobních údajů Evropské unie [(GDPR)](xref:security/gdpr).

  ![Stránka domů nebo index](razor-pages-start/_static/homeGDPR2.2.png)

  Následující obrázek zobrazí aplikaci po poskytnutí souhlasu se sledováním:

  ![Stránka domů nebo index](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* Stisknutím klávesy **cmd a opt-F5** spusťte bez ladicího programu.

  Visual Studio spustí [Kestrel](xref:fundamentals/servers/kestrel), spustí prohlížeč a přejde na `http://localhost:5001`.

* Na domovské stránce aplikace vyberte **přijmout** pro vyjádření souhlasu se sledováním.

  Tato aplikace nesleduje osobní údaje, ale šablona projektu zahrnuje funkci souhlasu pro případ, že ji budete potřebovat pro splnění Obecné nařízení o ochraně osobních údajů Evropské unie [(GDPR)](xref:security/gdpr).

  ![Stránka domů nebo index](razor-pages-start/_static/homeGDPR2.2_safari.png)

  Následující obrázek zobrazí aplikaci po poskytnutí souhlasu se sledováním:

  ![Stránka domů nebo index](razor-pages-start/_static/home2.2_safari.png)

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a>Prověřte soubory projektu

Tady je přehled hlavních složek projektu a souborů, se kterými budete pracovat v pozdějších kurzech.

### <a name="pages-folder"></a>Složka stránky

Obsahuje stránky Razor a podpůrné soubory. Každá stránka Razor je pár souborů:

* Soubor *. cshtml* , který obsahuje značku HTML s C# kódem pomocí syntaxe Razor.
* Soubor *. cshtml.cs* obsahující C# kód, který zpracovává události stránky.

Podpůrné soubory mají názvy začínající podtržítkem. Například soubor *_Layout. cshtml* nakonfiguruje prvky uživatelského rozhraní společné pro všechny stránky. Tento soubor nastaví navigační nabídku v horní části stránky a oznámení o autorských právech v dolní části stránky. Další informace naleznete v tématu <xref:mvc/views/layout>.

### <a name="wwwroot-folder"></a>Složka wwwroot

Obsahuje statické soubory, jako jsou soubory HTML, JavaScriptové soubory a soubory CSS. Další informace naleznete v tématu <xref:fundamentals/static-files>.

### <a name="appsettingsjson"></a>appSettings.json

Obsahuje konfigurační data, například připojovací řetězce. Další informace naleznete v tématu <xref:fundamentals/configuration/index>.

### <a name="programcs"></a>Program.cs

Obsahuje vstupní bod pro program. Další informace naleznete v tématu <xref:fundamentals/host/generic-host>.

### <a name="startupcs"></a>Startup.cs

Obsahuje kód, který konfiguruje chování aplikace, například zda vyžaduje souhlas s soubory cookie. Další informace naleznete v tématu <xref:fundamentals/startup>.

## <a name="additional-resources"></a>Další zdroje

* [Verze YouTube tohoto kurzu](https://www.youtube.com/watch?v=F0SP7Ry4flQ&feature=youtu.be)

## <a name="next-steps"></a>Další postup

Přejděte k dalšímu kurzu v řadě:

> [!div class="step-by-step"]
> [Přidání modelu](xref:tutorials/razor-pages/model)

::: moniker-end