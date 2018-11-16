---
title: Začínáme s knihovnou SignalR technologie ASP.NET Core
author: tdykstra
description: V tomto kurzu vytvoříte aplikaci chatu, který používá funkce SignalR technologie ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: tdykstra
ms.custom: mvc
ms.date: 11/13/2018
uid: tutorials/signalr
ms.openlocfilehash: 8916b3659250c1bcbbc2dc9b3d466586f98bcc7e
ms.sourcegitcommit: d3392f688cfebc1f25616da7489664d69c6ee330
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/16/2018
ms.locfileid: "51818379"
---
# <a name="tutorial-get-started-with-aspnet-core-signalr"></a>Kurz: Začínáme s knihovnou SignalR technologie ASP.NET Core

V tomto kurzu se naučíte se základy vytváření aplikace v reálném čase pomocí nástroje SignalR. Získáte informace o následujících postupech:

> [!div class="checklist"]
> * Vytvoření webového projektu.
> * Přidání knihovny klienta SignalR.
> * Vytvoření rozbočovače SignalR.
> * Nakonfigurujte projekt tak, aby používaly SignalR.
> * Přidejte kód, který odesílá zprávy z libovolného klienta na všechny připojené klienty.

Na konci budete mít funkční aplikaci chatu:

![Ukázková aplikace SignalR](signalr/_static/signalr-get-started-finished.png)

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/signalr/sample) ([stažení](xref:index#how-to-download-a-sample)).

## <a name="prerequisites"></a>Požadavky

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* [Visual Studio 2017 verze 15,8 nebo novější](https://www.visualstudio.com/downloads/) s **vývoj pro ASP.NET a web** pracovního vytížení
* [.NET core SDK 2.1 nebo novější](https://www.microsoft.com/net/download/all)

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* [Visual Studio Code](https://code.visualstudio.com/download)
* [.NET core SDK 2.1 nebo novější](https://www.microsoft.com/net/download/all)
* [C# pro Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

* [Visual Studio pro Mac verze 7.5.4 nebo novější](https://www.visualstudio.com/downloads/)
* [.NET core SDK 2.1 nebo novější](https://www.microsoft.com/net/download/all) (zahrnuty v instalaci sady Visual Studio)

---

## <a name="create-a-web-project"></a>Vytvoření webového projektu

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio/)

* V nabídce vyberte **soubor > Nový projekt**.

* V **nový projekt** dialogového okna, vyberte **nainstalováno > Visual C# > Web > Webová aplikace ASP.NET Core**. Pojmenujte projekt *SignalRChat*.

  ![Dialogové okno nového projektu v sadě Visual Studio](signalr/_static/signalr-new-project-dialog.png)

* Vyberte **webovou aplikaci** vytvořit projekt, který se používá pro stránky Razor.

* Vyberte cílovou architekturu **.NET Core**vyberte **ASP.NET Core 2.1**a klikněte na tlačítko **OK**.

  ![Dialogové okno nového projektu v sadě Visual Studio](signalr/_static/signalr-new-project-choose-type.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code/)

* Otevřít [integrovaný terminál](https://code.visualstudio.com/docs/editor/integrated-terminal) do složky, ve kterém bude vytvořena nová složka projektu.

* Spusťte následující příkazy:

   ```console
   dotnet new webapp -o SignalRChat
   code -r SignalRChat
   ```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

* V nabídce vyberte **soubor > Nový řešení**.

* Vyberte **.NET Core > aplikace > webové aplikace ASP.NET Core** (nevybírejte **ASP.NET Core webové aplikace (MVC)**).

* Vyberte **Další**.

* Pojmenujte projekt *SignalRChat*a pak vyberte **vytvořit**.

---

## <a name="add-the-signalr-client-library"></a>Přidat klientské knihovně SignalR

Je součástí serveru knihovny SignalR `Microsoft.AspNetCore.App` Microsoft.aspnetcore.all. Knihovny JavaScript klienta není automaticky zahrnut v projektu. V tomto kurzu použijete Správce knihovny (LibMan) k získání klientské knihovny z *unpkg*. unpkg je síť pro doručování obsahu (CDN)), která může doručovat cokoli, co je součástí npm, Správce balíčků Node.js.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio/)

* V **Průzkumníka řešení**, klikněte pravým tlačítkem na projekt a vyberte **přidat** > **knihoven na straně klienta**.

* V **přidat knihovnu na straně klienta** dialogovém okně pro **poskytovatele** vyberte **unpkg**. 

* Pro **knihovny**, zadejte `@aspnet/signalr@1`a vyberte nejnovější verzi, která není ve verzi preview.

  ![Přidat dialog knihoven na straně klienta - knihovně](signalr/_static/libman1.png)

* Vyberte **zvolte konkrétní soubory**, rozbalte *dist a prohlížeče* a pak zvolte položku *signalr.js* a *signalr.min.js*.

* Nastavte **cílové umístění** k *wwwroot/lib/signalr/* a vyberte **nainstalovat**.

  ![Přidat dialog knihoven na straně klienta – vyberte soubory a cíl](signalr/_static/libman2.png)

  LibMan vytvoří *wwwroot/lib/signalr* složky a kopie vybrané soubory.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code/)

* V integrovaném terminálu spusťte následující příkaz k instalaci LibMan.

  ```console
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli
  ```

* Spusťte následující příkaz s použitím LibMan získat klientské knihovně SignalR. Budete muset počkat několik sekund, než výstup.

  ```console
  libman install @aspnet/signalr -p unpkg -d wwwroot/lib/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js
  ```

  Parametry určete následující možnosti:
  * Použití zprostředkovatele unpkg.
  * Kopírovat soubory *wwwroot/lib/signalr* cíl.
  * Zkopírujte pouze zadané soubory.

  Výstup bude vypadat jako v následujícím příkladu:

  ```console
  wwwroot/lib/signalr/dist/browser/signalr.js written to disk
  wwwroot/lib/signalr/dist/browser/signalr.min.js written to disk
  Installed library "@aspnet/signalr@1.0.3" to "wwwroot/lib/signalr"
  ```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

* V **terminálu**, spusťte následující příkaz k instalaci LibMan.

  ```console
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli
  ```

* Přejděte do složky projektu (ten, který obsahuje *SignalRChat.csproj* souboru).

* Spusťte následující příkaz s použitím LibMan získat klientské knihovně SignalR.

  ```console
  libman install @aspnet/signalr -p unpkg -d wwwroot/lib/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js
  ```

  Parametry určete následující možnosti:
  * Použití zprostředkovatele unpkg.
  * Kopírovat soubory *wwwroot/lib/signalr* cíl.
  * Zkopírujte pouze zadané soubory.

  Výstup bude vypadat jako v následujícím příkladu:

  ```console
  wwwroot/lib/signalr/dist/browser/signalr.js written to disk
  wwwroot/lib/signalr/dist/browser/signalr.min.js written to disk
  Installed library "@aspnet/signalr@1.0.3" to "wwwroot/lib/signalr"
  ```

---

## <a name="create-a-signalr-hub"></a>Vytvoření rozbočovače SignalR

A *centra* je třída, která slouží jako základní kanál, který zpracovává vnitřní komunikaci klient server.

* Ve složce projektu SignalRChat vytvořit *rozbočovače* složky.

* V *rozbočovače* složku, vytvořte *ChatHub.cs* souboru následujícím kódem:

  [!code-csharp[Startup](signalr/sample/Hubs/ChatHub.cs)]

  `ChatHub` Třída dědí z funkce SignalR `Hub` třídy. `Hub` Třída spravuje připojení, skupiny a zasílání zpráv.

  `SendMessage` Metoda může být volán všech připojených klientů. Přijaté zprávy odešle všem klientům. Kód SignalR je asynchronní zajistit maximální škálovatelnosti.

## <a name="configure-signalr"></a>Konfigurace funkce SignalR

Na serveru funkce SignalR nastavené předat požadavky SignalR SignalR.

* Přidejte následující zvýrazněný kód do *Startup.cs* souboru.

  [!code-csharp[Startup](signalr/sample/Startup.cs?highlight=7,33,52-55)]

  Tyto změny přidejte do systém injektáž závislostí ASP.NET Core a middleware kanálu SignalR.

## <a name="add-signalr-client-code"></a>Přidejte kód klienta SignalR

* Nahraďte obsah *Pages\Index.cshtml* následujícím kódem:

  [!code-cshtml[Index](signalr/sample/Pages/Index.cshtml)]

  Předchozí kód:

  * Vytvoří textová pole pro název a text zprávy a tlačítka Odeslat.
  * Vytvoří seznam s `id="messagesList"` pro zobrazení zprávy, které byly přijaty z rozbočovače SignalR.
  * Obsahuje odkazy na skript ke knihovně SignalR a *chat.js* aplikační kód, který vytvoříte v dalším kroku.

* V *wwwroot/js* složku, vytvořte *chat.js* souboru následujícím kódem:

  [!code-javascript[Index](signalr/sample/wwwroot/js/chat.js)]

  Předchozí kód:

  * Vytvoří a spustí připojení.
  * Přidá tlačítko Odeslat obslužnou rutinu, která odesílá zprávy do centra.
  * Přidá objekt připojení obslužnou rutinu, která přijímá zprávy z centra a přidá je do seznamu.

## <a name="run-the-app"></a>Spuštění aplikace

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Stisknutím klávesy **CTRL + F5** a spusťte tak aplikaci bez ladění.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* V integrovaném terminálu spusťte následující příkaz:

  ```console
  dotnet run -p SignalRChat
  ```
  
# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

* V nabídce vyberte **spuštění > Spustit bez ladění**.

---

* Zkopírujte adresu URL z adresního řádku, otevřete jinou instanci prohlížeče nebo karty a vložte adresu URL do adresního řádku.

* Zvolte buď prohlížeče, zadejte název a zprávu a vybrat **poslat zprávu** tlačítko.

  Název a zpráva se zobrazí na obě stránky okamžitě.

  ![Ukázková aplikace SignalR](signalr/_static/signalr-get-started-finished.png)

> [!TIP]
> Pokud aplikace nebude fungovat, otevřete prohlížeč vývojářské nástroje (F12) a přejděte do konzoly. Můžou se zobrazovat chyby související s kódem HTML a JavaScript. Předpokládejme například, kam si ukládáte *signalr.js* v jiné složce než řízené. V takovém případě nebude fungovat odkaz na tento soubor a uvidíte v konzole chybu 404.
> ![Chyba nenalezení signalr.js](signalr/_static/f12-console.png)

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zjistili, jak:

> [!div class="checklist"]
> * Vytvoření projektu webové aplikace.
> * Přidání knihovny klienta SignalR.
> * Vytvoření rozbočovače SignalR.
> * Nakonfigurujte projekt tak, aby používaly SignalR.
> * Přidejte kód, který používá Centrum pro odesílání zpráv z libovolného klienta na všechny připojené klienty.

Další informace o funkci SignalR naleznete v úvodu:

> [!div class="nextstepaction"]
> [Úvod do ASP.NET Core SignalR](xref:signalr/introduction)
