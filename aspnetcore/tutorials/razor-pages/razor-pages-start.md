---
title: "\"Kurz: Začínáme se Razor stránkami v ASP.NET Core\""
author: rick-anderson
description: V této sérii kurzů se dozvíte, jak používat Razor stránky v ASP.NET Core. Naučte se, jak vytvořit model, vygenerovat kód pro Razor stránky, používat Entity Framework Core a SQL Server pro přístup k datům, přidat funkce hledání, přidat ověření vstupu a použít migrace k aktualizaci modelu.
ms.author: riande
ms.date: 11/12/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: 3b8ccf639bb91234f81c67750fffa170e52d636f
ms.sourcegitcommit: cd73744bd75fdefb31d25ab906df237f07ee7a0a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/05/2020
ms.locfileid: "84452335"
---
# <a name="tutorial-get-started-with-razor-pages-in-aspnet-core"></a>Kurz: Začínáme se Razor stránkami v ASP.NET Core

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"
Toto je první kurz série, který se učí základy vytvoření Razor webové aplikace ASP.NET Core Pages.

[!INCLUDE[](~/includes/advancedRP.md)]

Na konci série budete mít aplikaci, která spravuje databázi filmů.  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

V tomto kurzu jste:

> [!div class="checklist"]
> * Vytvoření Razor webové aplikace Pages
> * Spusťte aplikaci.
> * Prověřte soubory projektu.

Na konci tohoto kurzu budete mít Razor webovou aplikaci pracovní stránky, kterou vytvoříte v pozdějších kurzech.

![Stránka domů nebo index](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a>Požadavky

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-razor-pages-web-app"></a>Vytvoření Razor webové aplikace Pages

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* V nabídce **soubor** sady Visual Studio vyberte **Nový** > **projekt**.
* Vytvořte novou ASP.NET Core webovou aplikaci a vyberte **Další**.
  ![Nová ASP.NET Core webové aplikace](razor-pages-start/_static/np_2.1.png)
* Pojmenujte projekt **RazorPagesMovie**. Je důležité pojmenovat projekt *RazorPagesMovie* , aby se obory názvů shodovaly při kopírování a vkládání kódu.
  ![Nová ASP.NET Core webové aplikace](razor-pages-start/_static/config.png)

* V rozevíracím seznamu vyberte položku **ASP.NET Core 3,1** **a potom**vyberte **vytvořit**.

![Nová ASP.NET Core webové aplikace](razor-pages-start/_static/3/npx.png)

  Vytvoří se následující počáteční projekt:

  ![Průzkumník řešení](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Otevřete [integrovaný terminál](https://code.visualstudio.com/docs/editor/integrated-terminal).

* Přejděte do adresáře ( `cd` ), který bude obsahovat projekt.

* Spusťte následující příkazy:

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * `dotnet new`Příkaz vytvoří nový Razor projekt stránky ve složce *RazorPagesMovie* .
  * `code`Příkaz otevře složku *RazorPagesMovie* v aktuální instanci Visual Studio Code.

* Až se ikona plamene u OmniSharp stavového řádku změní na zelenou, dialogové okno požádá **o požadované prostředky k sestavení a ladění chybí v ' RazorPagesMovie '. Přidat je?** Vyberte **Ano**.

  Do kořenového adresáře projektu se přidá adresář *. VSCode* obsahující soubory *Launch. JSON* a *Tasks. JSON* .

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

* Vyberte **soubor** > **nové řešení**.

  ![macOS nové řešení](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* V Visual Studio pro Mac starší než verze 8,6 vyberte možnost Webová aplikace **.NET Core**  >  **App**  >  **Web aplikace**  >  **Next**. V části verze 8,6 nebo novější vyberte webová aplikace **Webová aplikace a**  >  **aplikace**konzoly webové  >  **aplikace**  >  **Next**.

  ![Výběr šablony webové aplikace v macOS](razor-pages-start/_static/web_app_template_vsmac.png)

* Potvrďte následující konfigurace:

  * **Cílová architektura** je nastavená na **.NET Core 3,1**.
  * **Ověřování** nastaveno na **bez ověřování**.
   
  Vyberte **Další**.

  ![macOS .NET Core 3,1 – výběr](razor-pages-start/_static/targetframework3.png)

* Pojmenujte projekt **RazorPagesMovie**a pak vyberte **vytvořit**.

  ![macOS název projektu](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a>Spuštění aplikace

  [!INCLUDE[](~/includes/run-the-app.md)]

## <a name="examine-the-project-files"></a>Prověřte soubory projektu

Tady je přehled hlavních složek projektu a souborů, se kterými budete pracovat v pozdějších kurzech.

### <a name="pages-folder"></a>Složka stránky

Obsahuje Razor stránky a podpůrné soubory. Každá Razor stránka je pár souborů:

* Soubor *. cshtml* , který obsahuje značky HTML s kódem jazyka C# pomocí Razor syntaxe.
* Soubor *. cshtml.cs* , který obsahuje kód jazyka C#, který zpracovává události stránky.

Podpůrné soubory mají názvy začínající podtržítkem. Například soubor *_Layout. cshtml* nakonfiguruje prvky uživatelského rozhraní společné pro všechny stránky. Tento soubor nastaví navigační nabídku v horní části stránky a oznámení o autorských právech v dolní části stránky. Další informace naleznete v tématu <xref:mvc/views/layout>.

### <a name="wwwroot-folder"></a>Složka wwwroot

Obsahuje statické soubory, jako jsou soubory HTML, JavaScriptové soubory a soubory CSS. Další informace naleznete v tématu <xref:fundamentals/static-files>.

### <a name="appsettingsjson"></a>appSettings. JSON

Obsahuje konfigurační data, například připojovací řetězce. Další informace naleznete v tématu <xref:fundamentals/configuration/index>.

### <a name="programcs"></a>Program.cs

Obsahuje vstupní bod pro program. Další informace naleznete v tématu <xref:fundamentals/host/generic-host>.

### <a name="startupcs"></a>Startup.cs

Obsahuje kód, který konfiguruje chování aplikace. Další informace naleznete v tématu <xref:fundamentals/startup>.

## <a name="next-steps"></a>Další kroky

Přejděte k dalšímu kurzu v řadě:

> [!div class="step-by-step"]
> [Přidání modelu](xref:tutorials/razor-pages/model)

::: moniker-end

<!--::: moniker range=">= aspnetcore-3.0" -->

::: moniker range="< aspnetcore-3.0"

Toto je první kurz řady. [Série](xref:tutorials/razor-pages/index) se učí základy vytvoření Razor webové aplikace ASP.NET Core Pages.

[!INCLUDE[](~/includes/advancedRP.md)]

Na konci série budete mít aplikaci, která spravuje databázi filmů.  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

V tomto kurzu jste:

> [!div class="checklist"]
> * Vytvoření Razor webové aplikace Pages
> * Spusťte aplikaci.
> * Prověřte soubory projektu.

Na konci tohoto kurzu budete mít Razor webovou aplikaci pracovní stránky, kterou vytvoříte v pozdějších kurzech.

![Stránka domů nebo index](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a>Požadavky

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-razor-pages-web-app"></a>Vytvoření Razor webové aplikace Pages

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* V nabídce **soubor** sady Visual Studio vyberte **Nový** > **projekt**.

* Vytvořte novou ASP.NET Core webovou aplikaci a vyberte **Další**.

  ![Nová ASP.NET Core webové aplikace](razor-pages-start/_static/np_2.1.png)

* Pojmenujte projekt **RazorPagesMovie**. Je důležité pojmenovat projekt *RazorPagesMovie* , aby se obory názvů shodovaly při kopírování a vkládání kódu.

  ![Nová ASP.NET Core webové aplikace](razor-pages-start/_static/config.png)

* V rozevíracím seznamu vyberte položku **ASP.NET Core 2,2** **a potom**vyberte **vytvořit**.

![Nová ASP.NET Core webové aplikace](razor-pages-start/_static/np_2_2.2.png)

  Vytvoří se následující počáteční projekt:

  ![Průzkumník řešení](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Otevřete [integrovaný terminál](https://code.visualstudio.com/docs/editor/integrated-terminal).

* Přejděte do adresáře ( `cd` ), který bude obsahovat projekt.

* Spusťte následující příkazy:

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * `dotnet new`Příkaz vytvoří nový Razor projekt stránky ve složce *RazorPagesMovie* .
  * `code`Příkaz otevře složku *RazorPagesMovie* v aktuální instanci Visual Studio Code.

* Až se ikona plamene u OmniSharp stavového řádku změní na zelenou, dialogové okno požádá **o požadované prostředky k sestavení a ladění chybí v ' RazorPagesMovie '. Přidat je?** Vyberte **Ano**.

  Do kořenového adresáře projektu se přidá adresář *. VSCode* obsahující soubory *Launch. JSON* a *Tasks. JSON* .

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

* Vyberte **soubor** > **nové řešení**.

![macOS nové řešení](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* V Visual Studio pro Mac starší než verze 8,6 vyberte možnost Webová aplikace **.NET Core**  >  **App**  >  **Web aplikace**  >  **Next**. V části verze 8,6 nebo novější vyberte webová aplikace **Webová aplikace a**  >  **aplikace**konzoly webové  >  **aplikace**  >  **Next**.

* V dialogovém okně **Konfigurace nového ASP.NET Core webového rozhraní API** nastavte **cílové** rozhraní **.NET Core 3,1**.

  ![macOS .NET Core 3,0 – výběr](razor-pages-start/_static/targetframework3.png)

* Pojmenujte projekt **RazorPagesMovie**a pak vyberte **vytvořit**.

  ![nameproj](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a>Spuštění aplikace

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Stisknutím kombinace kláves CTRL + F5 spustíte bez ladicího programu.

  [!INCLUDE[](~/includes/trustCertVS.md)]

  Visual Studio spustí [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) a spustí aplikaci. Panel Adresa se zobrazí `localhost:port#` a ne něco jako `example.com` . Důvodem je, že `localhost` je standardní název hostitele pro místní počítač. Localhost obsluhuje pouze webové požadavky z místního počítače. Když Visual Studio vytvoří webový projekt, použije se pro webový server náhodný port.

* Na domovské stránce aplikace vyberte **přijmout** pro vyjádření souhlasu se sledováním.

  Tato aplikace nesleduje osobní údaje, ale šablona projektu zahrnuje funkci souhlasu pro případ, že ji budete potřebovat pro splnění Obecné nařízení o ochraně osobních údajů Evropské unie [(GDPR)](xref:security/gdpr).

  ![Stránka domů nebo index](razor-pages-start/_static/homeGDPR2.2.png)

  Následující obrázek zobrazí aplikaci po poskytnutí souhlasu se sledováním:

  ![Stránka domů nebo index](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* Stisknutím **kombinace kláves CTRL + F5** spustíte bez ladicího programu.

  Visual Studio Code spustí [Kestrel](xref:fundamentals/servers/kestrel), spustí prohlížeč a přejde na `http://localhost:5001` . Panel Adresa se zobrazí `localhost:port#` a ne něco jako `example.com` . To je proto, že `localhost` se jedná o standardní název hostitele pro místní počítač. Localhost obsluhuje pouze webové požadavky z místního počítače.

* Na domovské stránce aplikace vyberte **přijmout** pro vyjádření souhlasu se sledováním.

  Tato aplikace nesleduje osobní údaje, ale šablona projektu zahrnuje funkci souhlasu pro případ, že ji budete potřebovat pro splnění Obecné nařízení o ochraně osobních údajů Evropské unie [(GDPR)](xref:security/gdpr).

  ![Stránka domů nebo index](razor-pages-start/_static/homeGDPR2.2.png)

  Následující obrázek zobrazí aplikaci po poskytnutí souhlasu se sledováním:

  ![Stránka domů nebo index](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* Stisknutím klávesy **cmd a opt-F5** spusťte bez ladicího programu.

  Visual Studio spustí [Kestrel](xref:fundamentals/servers/kestrel), spustí prohlížeč a přejde na `http://localhost:5001` .

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

Obsahuje Razor stránky a podpůrné soubory. Každá Razor stránka je pár souborů:

* Soubor *. cshtml* , který obsahuje značky HTML s kódem jazyka C# pomocí Razor syntaxe.
* Soubor *. cshtml.cs* , který obsahuje kód jazyka C#, který zpracovává události stránky.

Podpůrné soubory mají názvy začínající podtržítkem. Například soubor *_Layout. cshtml* nakonfiguruje prvky uživatelského rozhraní společné pro všechny stránky. Tento soubor nastaví navigační nabídku v horní části stránky a oznámení o autorských právech v dolní části stránky. Další informace naleznete v tématu <xref:mvc/views/layout>.

### <a name="wwwroot-folder"></a>Složka wwwroot

Obsahuje statické soubory, jako jsou soubory HTML, JavaScriptové soubory a soubory CSS. Další informace naleznete v tématu <xref:fundamentals/static-files>.

### <a name="appsettingsjson"></a>appSettings. JSON

Obsahuje konfigurační data, například připojovací řetězce. Další informace naleznete v tématu <xref:fundamentals/configuration/index>.

### <a name="programcs"></a>Program.cs

Obsahuje vstupní bod pro program. Další informace naleznete v tématu <xref:fundamentals/host/generic-host>.

### <a name="startupcs"></a>Startup.cs

Obsahuje kód, který konfiguruje chování aplikace, například zda vyžaduje souhlas s soubory cookie. Další informace naleznete v tématu <xref:fundamentals/startup>.

## <a name="additional-resources"></a>Další zdroje

* [Verze YouTube tohoto kurzu](https://www.youtube.com/watch?v=F0SP7Ry4flQ&feature=youtu.be)

## <a name="next-steps"></a>Další kroky

Přejděte k dalšímu kurzu v řadě:

> [!div class="step-by-step"]
> [Přidání modelu](xref:tutorials/razor-pages/model)

::: moniker-end
