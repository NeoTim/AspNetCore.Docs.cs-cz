---
title: 'Výuka: Začínáme se stránkami Razor pages v ASP.NET Core'
author: rick-anderson
description: Tato série výukových programů ukazuje, jak používat Razor Pages v ASP.NET Core. Naučte se, jak vytvořit model, generovat kód pro stránky Razor, použít Entity Framework Core a SQL Server pro přístup k datům, přidat funkci vyhledávání, přidat ověření vstupu a použít migrace k aktualizaci modelu.
ms.author: riande
ms.date: 11/12/2019
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: 6e1d58ccd83f7d7c1083dc2bf9ce7476650812a1
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78658541"
---
# <a name="tutorial-get-started-with-razor-pages-in-aspnet-core"></a>Výuka: Začínáme se stránkami Razor pages v ASP.NET Core

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"
Toto je první kurz série, který učí základy budování webové aplikace ASP.NET Core Razor Pages.

[!INCLUDE[](~/includes/advancedRP.md)]

Na konci série budete mít aplikaci, která spravuje databázi filmů.  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

V tomto kurzu jste:

> [!div class="checklist"]
> * Vytvořte webovou aplikaci Razor Pages.
> * Spusťte aplikaci.
> * Zkontrolujte soubory projektu.

Na konci tohoto kurzu budete mít funkční webovou aplikaci Razor Pages, na které budete stavět v pozdějších kurzech.

![Domovská stránka nebo stránka Rejstříku](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a>Požadavky

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-razor-pages-web-app"></a>Vytvoření webové aplikace Stránky Razor

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* V nabídce **Soubor** sady Visual Studio vyberte **Nový** > **projekt**.
* Vytvořte novou ASP.NET základní webovou aplikaci a vyberte **možnost Další**.
  ![nová ASP.NET základní webová aplikace](razor-pages-start/_static/np_2.1.png)
* Název projektu **RazorPagesMovie**. Je důležité pojmenovat projekt *RazorPagesMovie,* aby se jmenné prostory shodovaly při kopírování a vkládání kódu.
  ![nová ASP.NET základní webová aplikace](razor-pages-start/_static/config.png)

* V rozevíracím souboru **Webová aplikace**vyberte **ASP.NET jádra 3.1** a pak vyberte **Vytvořit**.

![nová ASP.NET základní webová aplikace](razor-pages-start/_static/3/npx.png)

  Je vytvořen následující počáteční projekt:

  ![Průzkumník řešení](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Otevřete [integrovanou svorku](https://code.visualstudio.com/docs/editor/integrated-terminal).

* Změna adresáře`cd`( ), který bude obsahovat projekt.

* Spusťte následující příkazy:

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * Příkaz `dotnet new` vytvoří nový projekt Razor Pages ve složce *RazorPagesMovie.*
  * Příkaz `code` otevře složku *RazorPagesMovie* v aktuální instanci kódu sady Visual Studio.

* Poté, co ikona plamene OmniSharp na stavovém řádku zezelenaje, dialogové okno požádá **požadované datové zdroje k sestavení a ladění v nástroji RazorPagesMovie chybí. Přidat?** Vyberte **ano**.

  Do kořenového adresáře projektu je přidán adresář *.vscode* obsahující soubory *launch.json* a *tasks.json.*

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

* Vyberte **možnost Nové řešení souboru** > **New Solution**.

![macOS Nové řešení](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* Vyberte **.NET Core** > **App** > **Web Application** > **Next**.

  ![macOS Dialogové okno Nový projekt](razor-pages-start/_static/webapp.png)

* V **dialogovém okně Konfigurovat novou webovou aplikaci** nastavte **cílovou architekturu** na **.NET Core 3.1**.

  ![výběr macOS .NET Core 3.1](razor-pages-start/_static/targetframework3.png)

* Pojmenujte projekt **RazorPagesMovie**a pak vyberte **Vytvořit**.

  ![názevproj](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a>Spuštění aplikace

  [!INCLUDE[](~/includes/run-the-app.md)]

## <a name="examine-the-project-files"></a>Prohlédněte si soubory projektu

Tady je přehled hlavních složek projektu a souborů, se kterými budete pracovat v pozdějších kurzech.

### <a name="pages-folder"></a>Složka Stránky

Obsahuje razor stránky a podpůrné soubory. Každá stránka Razor Page je dvojicí souborů:

* Soubor *.cshtml,* který obsahuje značky HTML s kódem Jazyka C# pomocí syntaxe Razor.
* Soubor *.cshtml.cs,* který obsahuje kód Jazyka C#, který zpracovává události stránky.

Podpůrné soubory mají názvy, které začínají podtržítkem. Například soubor *_Layout.cshtml* konfiguruje prvky uživatelského rozhraní společné pro všechny stránky. Tento soubor nastaví navigační nabídku v horní části stránky a oznámení o autorských právech v dolní části stránky. Další informace naleznete v tématu <xref:mvc/views/layout>.

### <a name="wwwroot-folder"></a>wwwkořenová složka

Obsahuje statické soubory, jako jsou soubory HTML, soubory JavaScript a soubory CSS. Další informace naleznete v tématu <xref:fundamentals/static-files>.

### <a name="appsettingsjson"></a>appSettings.json

Obsahuje konfigurační data, například připojovací řetězce. Další informace naleznete v tématu <xref:fundamentals/configuration/index>.

### <a name="programcs"></a>Program.cs

Obsahuje vstupní bod programu. Další informace naleznete v tématu <xref:fundamentals/host/generic-host>.

### <a name="startupcs"></a>Startup.cs

Obsahuje kód, který konfiguruje chování aplikace. Další informace naleznete v tématu <xref:fundamentals/startup>.

## <a name="next-steps"></a>Další kroky

Převést na další výukový program v sérii:

> [!div class="step-by-step"]
> [Přidání modelu](xref:tutorials/razor-pages/model)

::: moniker-end

<!--::: moniker range=">= aspnetcore-3.0" -->

::: moniker range="< aspnetcore-3.0"

Toto je první kurz série. [Série](xref:tutorials/razor-pages/index) učí základy budování webové aplikace ASP.NET Core Razor Pages.

[!INCLUDE[](~/includes/advancedRP.md)]

Na konci série budete mít aplikaci, která spravuje databázi filmů.  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

V tomto kurzu jste:

> [!div class="checklist"]
> * Vytvořte webovou aplikaci Razor Pages.
> * Spusťte aplikaci.
> * Zkontrolujte soubory projektu.

Na konci tohoto kurzu budete mít funkční webovou aplikaci Razor Pages, na které budete stavět v pozdějších kurzech.

![Domovská stránka nebo stránka Rejstříku](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a>Požadavky

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-razor-pages-web-app"></a>Vytvoření webové aplikace Stránky Razor

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* V nabídce **Soubor** sady Visual Studio vyberte **Nový** > **projekt**.

* Vytvořte novou ASP.NET základní webovou aplikaci a vyberte **možnost Další**.

  ![nová ASP.NET základní webová aplikace](razor-pages-start/_static/np_2.1.png)

* Název projektu **RazorPagesMovie**. Je důležité pojmenovat projekt *RazorPagesMovie,* aby se jmenné prostory shodovaly při kopírování a vkládání kódu.

  ![nová ASP.NET základní webová aplikace](razor-pages-start/_static/config.png)

* V rozevíracím souboru **Webová aplikace**vyberte **ASP.NET Core 2.2** a pak vyberte **Vytvořit**.

![nová ASP.NET základní webová aplikace](razor-pages-start/_static/np_2_2.2.png)

  Je vytvořen následující počáteční projekt:

  ![Průzkumník řešení](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Otevřete [integrovanou svorku](https://code.visualstudio.com/docs/editor/integrated-terminal).

* Změna adresáře`cd`( ), který bude obsahovat projekt.

* Spusťte následující příkazy:

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * Příkaz `dotnet new` vytvoří nový projekt Razor Pages ve složce *RazorPagesMovie.*
  * Příkaz `code` otevře složku *RazorPagesMovie* v aktuální instanci kódu sady Visual Studio.

* Poté, co ikona plamene OmniSharp na stavovém řádku zezelenaje, dialogové okno požádá **požadované datové zdroje k sestavení a ladění v nástroji RazorPagesMovie chybí. Přidat?** Vyberte **ano**.

  Do kořenového adresáře projektu je přidán adresář *.vscode* obsahující soubory *launch.json* a *tasks.json.*

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

* Vyberte **možnost Nové řešení souboru** > **New Solution**.

![macOS Nové řešení](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* Vyberte **.NET Core** > **App** > **Web Application** > **Next**.

  ![macOS Dialogové okno Nový projekt](razor-pages-start/_static/webapp.png)

* V dialogovém **okně Konfigurovat nové webové rozhraní ASP.NET Core** nastavte **cílovou architekturu** na **.NET Core 3.1**.

  ![výběr macOS .NET Core 3.0](razor-pages-start/_static/targetframework3.png)

* Pojmenujte projekt **RazorPagesMovie**a pak vyberte **Vytvořit**.

  ![názevproj](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a>Spuštění aplikace

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Stisknutím kláves Ctrl+F5 spusťte bez ladicího programu.

  [!INCLUDE[](~/includes/trustCertVS.md)]

  Visual Studio spustí [iis express](/iis/extensions/introduction-to-iis-express/iis-express-overview) a spustí aplikaci. Adresní řádek `localhost:port#` zobrazuje a `example.com`ne něco jako . To proto, `localhost` že je standardní název hostitele pro místní počítač. Localhost slouží pouze webové požadavky z místního počítače. Když Visual Studio vytvoří webový projekt, použije se pro webový server náhodný port.

* Na domovské stránce aplikace vyberte **Přijmout,** chcete-li souhlasit se sledováním.

  Tato aplikace nesleduje osobní údaje, ale šablona projektu obsahuje funkci souhlasu v případě, že ji potřebujete dodržovat obecné nařízení Evropské unie [o ochraně osobních údajů (GDPR).](xref:security/gdpr)

  ![Domovská stránka nebo stránka Rejstříku](razor-pages-start/_static/homeGDPR2.2.png)

  Následující obrázek znázorňuje aplikaci poté, co udělit souhlas se sledováním:

  ![Domovská stránka nebo stránka Rejstříku](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* Stisknutím **klávesCtrl-F5** spusťte bez ladicího programu.

  Visual Studio Code spustí [Kestrel](xref:fundamentals/servers/kestrel), spustí prohlížeč `http://localhost:5001`a přejde na . Adresní řádek `localhost:port#` zobrazuje a `example.com`ne něco jako . To proto, `localhost` že je standardní název hostitele pro místní počítač. Localhost slouží pouze webové požadavky z místního počítače.

* Na domovské stránce aplikace vyberte **Přijmout,** chcete-li souhlasit se sledováním.

  Tato aplikace nesleduje osobní údaje, ale šablona projektu obsahuje funkci souhlasu v případě, že ji potřebujete dodržovat obecné nařízení Evropské unie [o ochraně osobních údajů (GDPR).](xref:security/gdpr)

  ![Domovská stránka nebo stránka Rejstříku](razor-pages-start/_static/homeGDPR2.2.png)

  Následující obrázek znázorňuje aplikaci poté, co udělit souhlas se sledováním:

  ![Domovská stránka nebo stránka Rejstříku](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* Stisknutím **tlačítka Cmd-Opt-F5** spusťte bez ladicího programu.

  Visual Studio spustí [Kestrel](xref:fundamentals/servers/kestrel), spustí prohlížeč `http://localhost:5001`a přejde na .

* Na domovské stránce aplikace vyberte **Přijmout,** chcete-li souhlasit se sledováním.

  Tato aplikace nesleduje osobní údaje, ale šablona projektu obsahuje funkci souhlasu v případě, že ji potřebujete dodržovat obecné nařízení Evropské unie [o ochraně osobních údajů (GDPR).](xref:security/gdpr)

  ![Domovská stránka nebo stránka Rejstříku](razor-pages-start/_static/homeGDPR2.2_safari.png)

  Následující obrázek znázorňuje aplikaci poté, co udělit souhlas se sledováním:

  ![Domovská stránka nebo stránka Rejstříku](razor-pages-start/_static/home2.2_safari.png)

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a>Prohlédněte si soubory projektu

Tady je přehled hlavních složek projektu a souborů, se kterými budete pracovat v pozdějších kurzech.

### <a name="pages-folder"></a>Složka Stránky

Obsahuje razor stránky a podpůrné soubory. Každá stránka Razor Page je dvojicí souborů:

* Soubor *.cshtml,* který obsahuje značky HTML s kódem Jazyka C# pomocí syntaxe Razor.
* Soubor *.cshtml.cs,* který obsahuje kód Jazyka C#, který zpracovává události stránky.

Podpůrné soubory mají názvy, které začínají podtržítkem. Například soubor *_Layout.cshtml* konfiguruje prvky uživatelského rozhraní společné pro všechny stránky. Tento soubor nastaví navigační nabídku v horní části stránky a oznámení o autorských právech v dolní části stránky. Další informace naleznete v tématu <xref:mvc/views/layout>.

### <a name="wwwroot-folder"></a>wwwkořenová složka

Obsahuje statické soubory, jako jsou soubory HTML, soubory JavaScript a soubory CSS. Další informace naleznete v tématu <xref:fundamentals/static-files>.

### <a name="appsettingsjson"></a>appSettings.json

Obsahuje konfigurační data, například připojovací řetězce. Další informace naleznete v tématu <xref:fundamentals/configuration/index>.

### <a name="programcs"></a>Program.cs

Obsahuje vstupní bod programu. Další informace naleznete v tématu <xref:fundamentals/host/generic-host>.

### <a name="startupcs"></a>Startup.cs

Obsahuje kód, který konfiguruje chování aplikace, například zda vyžaduje souhlas pro soubory cookie. Další informace naleznete v tématu <xref:fundamentals/startup>.

## <a name="additional-resources"></a>Další zdroje

* [Youtube verze tohoto výukového programu](https://www.youtube.com/watch?v=F0SP7Ry4flQ&feature=youtu.be)

## <a name="next-steps"></a>Další kroky

Převést na další výukový program v sérii:

> [!div class="step-by-step"]
> [Přidání modelu](xref:tutorials/razor-pages/model)

::: moniker-end
