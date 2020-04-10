---
title: Začínáme s ASP.NET CoreSignalR
author: bradygaster
description: V tomto kurzu vytvoříte chatovací aplikaci, která používá ASP.NET Core SignalR.
ms.author: bradyg
ms.custom: mvc
ms.date: 11/21/2019
no-loc:
- SignalR
uid: tutorials/signalr
ms.openlocfilehash: 869eb325ee95a78e4b16c61c5b0573bb094292e3
ms.sourcegitcommit: 9a46e78c79d167e5fa0cddf89c1ef584e5fe1779
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2020
ms.locfileid: "80994623"
---
# <a name="tutorial-get-started-with-aspnet-core-signalr"></a>Kurz: Začínáme s ASP.NET Core SignalR

::: moniker range=">= aspnetcore-3.0"

Tento kurz učí základy vytváření aplikace v reálném čase pomocí SignalR. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvořte webový projekt.
> * Přidejte klientskou knihovnu SignalR.
> * Vytvořte rozbočovač SignalR.
> * Nakonfigurujte projekt tak, aby používal SignalR.
> * Přidejte kód, který odesílá zprávy z libovolného klienta všem připojeným klientům.

Na konci budete mít funkční chatovací aplikaci:

![Ukázková aplikace SignalR](signalr/_static/3.x/signalr-get-started-finished.png)

## <a name="prerequisites"></a>Požadavky

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="create-a-web-app-project"></a>Vytvoření projektu webové aplikace

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio/)

* V nabídce vyberte **Soubor > Nový projekt**.

* V **dialogovém** okně Vytvořit nový projekt vyberte **ASP.NET Základní webová aplikace**a pak vyberte **Další**.

* V dialogovém **okně Konfigurovat nový projekt pojmenujte** projekt *SignalRChat*a pak vyberte **Vytvořit**.

* V **dialogovém okně Vytvořit novou ASP.NET základní webovou aplikaci** vyberte **.NET Core** a **ASP.NET Core 3.0**. 

* Výběrem **možnosti Webová aplikace** vytvořte projekt, který používá Razor Pages, a pak vyberte **Vytvořit**.

  ![Dialogové okno Nový projekt v sadě Visual Studio](signalr/_static/3.x/signalr-new-project-dialog.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code/)

* Otevřete [integrovaný terminál](https://code.visualstudio.com/docs/editor/integrated-terminal) do složky, ve které bude vytvořena nová složka projektu.

* Spusťte následující příkazy:

   ```dotnetcli
   dotnet new webapp -o SignalRChat
   code -r SignalRChat
   ```

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

* V nabídce vyberte **Soubor > nové řešení**.

* Vyberte **> aplikace > webové aplikace (Nevybírejte** **webovou aplikaci (Model-View-Controller)** a pak vyberte **Další**.

* Zkontrolujte, zda je **cílová architektura** nastavena na **.NET Core 3.0**, a pak vyberte **Další**.

* Pojmenujte projekt *SignalRChat*a pak vyberte **vytvořit**.

---

## <a name="add-the-signalr-client-library"></a>Přidání klientské knihovny SignalR

Knihovna serveru SignalR je součástí ASP.NET sdíleného rozhraní Core 3.0. Klientská knihovna JavaScriptu není automaticky zahrnuta do projektu. V tomto kurzu pomocí Správce knihovny (LibMan) získat klientskou knihovnu z *unpkg*. unpkg je síť pro doručování obsahu (CDN), která může dodat cokoli, co se nachází v npm, správce balíčků Node.js.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio/)

* V **Průzkumníku řešení**klepněte pravým tlačítkem myši na projekt a vyberte **přidat** > **knihovnu na straně klienta**.

* V dialogovém okně **Přidat knihovnu na straně klienta** vyberte pro **zprostředkovatele** **hodnotu nepkg**.

* Do **knihovny**zadejte . `@microsoft/signalr@latest`

* Vyberte **Vybrat konkrétní soubory**, rozbalte složku *dist/browser* a vyberte *signalr.js* a *signalr.min.js*.

* Nastavte **cílové umístění** na *wwwroot/js/signalr/* a vyberte **Instalovat**.

  ![Dialogové okno Přidat knihovnu na straně klienta – vybrat knihovnu](signalr/_static/3.x/find-signalr-client-libs-select-files.png)

  LibMan vytvoří složku *wwwroot/js/signalr* a zkopíruje do ní vybrané soubory.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code/)

* V integrovaném terminálu spusťte následující příkaz pro instalaci LibMan.

  ```dotnetcli
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli
  ```

* Spusťte následující příkaz, abyste získali klientskou knihovnu SignalR pomocí LibMan. Možná budete muset počkat několik sekund, než se zzobrazuje výstup.

  ```console
  libman install @microsoft/signalr@latest -p unpkg -d wwwroot/js/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js
  ```

  Parametry určují následující možnosti:
  * Použijte zprostředkovatele unpkg.
  * Zkopírujte soubory do cíle *wwwroot/js/signalr.*
  * Zkopírujte pouze zadané soubory.

  Výstup vypadá takto:

  ```console
  wwwroot/js/signalr/dist/browser/signalr.js written to disk
  wwwroot/js/signalr/dist/browser/signalr.min.js written to disk
  Installed library "@microsoft/signalr@latest" to "wwwroot/js/signalr"
  ```

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

* V **terminálu**spusťte následující příkaz pro instalaci LibMan.

  ```dotnetcli
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli
  ```

* Přejděte do složky projektu (ten, který obsahuje soubor *SignalRChat.csproj).*

* Spusťte následující příkaz, abyste získali klientskou knihovnu SignalR pomocí LibMan.

  ```console
  libman install @microsoft/signalr@latest -p unpkg -d wwwroot/js/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js
  ```

  Parametry určují následující možnosti:
  * Použijte zprostředkovatele unpkg.
  * Zkopírujte soubory do cíle *wwwroot/js/signalr.*
  * Zkopírujte pouze zadané soubory.

  Výstup vypadá takto:

  ```console
  wwwroot/js/signalr/dist/browser/signalr.js written to disk
  wwwroot/js/signalr/dist/browser/signalr.min.js written to disk
  Installed library "@microsoft/signalr@latest" to "wwwroot/js/signalr"
  ```

---

## <a name="create-a-signalr-hub"></a>Vytvoření rozbočovače SignalR

*Rozbočovač* je třída, která slouží jako kanál vysoké úrovně, který zpracovává komunikaci mezi klientem a serverem.

* Ve složce projektu SignalRChat vytvořte složku *Hubs.*

* Ve složce *Rozbočovače* vytvořte *soubor ChatHub.cs* s následujícím kódem:

  [!code-csharp[ChatHub](signalr/sample-snapshot/3.x/ChatHub.cs)]

  Třída `ChatHub` dědí z SignalR `Hub` třídy. Třída `Hub` spravuje připojení, skupiny a zasílání zpráv.

  Metodu `SendMessage` může volat připojený klient a odeslat zprávu všem klientům. JavaScript klientský kód, který volá metodu je zobrazen později v kurzu. SignalR kód je asynchronní poskytnout maximální škálovatelnost.

## <a name="configure-signalr"></a>Konfigurace signalismu

Server SignalR musí být nakonfigurován tak, aby předává požadavky SignalR signalr.

* Přidejte do *souboru Startup.cs* následující zvýrazněný kód.

  [!code-csharp[Startup](signalr/sample-snapshot/3.x/Startup.cs?highlight=11,28,55)]

  Tyto změny přidat SignalR do ASP.NET systémy vkládání závislostí jádra a směrování.

## <a name="add-signalr-client-code"></a>Přidat klientský kód SignalR

* Nahraďte obsah *stránky\Index.cshtml* následujícím kódem:

  [!code-cshtml[Index](signalr/sample-snapshot/3.x/Index.cshtml)]

  Předcházející kód:

  * Vytvoří textová pole pro text názvu a zprávy a tlačítko odeslat.
  * Vytvoří seznam `id="messagesList"` s pro zobrazení zpráv, které jsou přijímány z rozbočovače SignalR.
  * Zahrnuje odkazy na skripty SignalR a kód aplikace *chat.js,* který vytvoříte v dalším kroku.

* Ve složce *wwwroot/js* vytvořte soubor *chat.js* s následujícím kódem:

  [!code-javascript[chat](signalr/sample-snapshot/3.x/chat.js)]

  Předcházející kód:

  * Vytvoří a spustí připojení.
  * Přidá k tlačítku odeslat obslužnou rutinu, která odesílá zprávy do centra.
  * Přidá k objektu připojení obslužnou rutinu, která přijímá zprávy z rozbočovače a přidá je do seznamu.

## <a name="run-the-app"></a>Spuštění aplikace

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Stisknutím **kláves CTRL+F5** spusťte aplikaci bez ladění.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* V integrovaném terminálu spusťte následující příkaz:

  ```dotnetcli
  dotnet watch run -p SignalRChat.csproj
  ```

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

* V nabídce vyberte **Spustit > start bez ladění**.

---

* Zkopírujte adresu URL z adresního řádku, otevřete jinou instanci prohlížeče nebo kartu a vložte ji do adresního řádku.

* Zvolte některý z prohlížečů, zadejte jméno a zprávu a vyberte tlačítko **Odeslat zprávu.**

  Název a zpráva se zobrazí na obou stránkách okamžitě.

  ![Ukázková aplikace SignalR](signalr/_static/3.x/signalr-get-started-finished.png)

> [!TIP]
> * Pokud aplikace nefunguje, otevřete nástroje pro vývojáře prohlížeče (F12) a přejděte do konzole. Mohou se zobrazit chyby související s kódem HTML a JavaScript. Předpokládejme například, že vložíte *soubor signalr.js* do jiné složky, než bylo nařízeno. V takovém případě nebude odkaz na tento soubor fungovat a v konzole se zobrazí chyba 404.
>   ![soubor signalr.js nebyl nalezen chyba](signalr/_static/3.x/f12-console.png)
> * Pokud se v Chromu zobrazí chyba ERR_SPDY_INADEQUATE_TRANSPORT_SECURITY, aktualizujte vývojový certifikát těmito příkazy:
>
>   ```dotnetcli
>   dotnet dev-certs https --clean
>   dotnet dev-certs https --trust
>   ```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Tento kurz učí základy vytváření aplikace v reálném čase pomocí SignalR. Získáte informace o těchto tématech:   

> [!div class="checklist"]  
> * Vytvořte webový projekt.   
> * Přidejte klientskou knihovnu SignalR. 
> * Vytvořte rozbočovač SignalR.   
> * Nakonfigurujte projekt tak, aby používal SignalR.   
> * Přidejte kód, který odesílá zprávy z libovolného klienta všem připojeným klientům.  
Na konci budete mít funkční chatovou ![aplikaci: Ukázková aplikace SignalR](signalr/_static/2.x/signalr-get-started-finished.png) 

## <a name="prerequisites"></a>Požadavky    

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)   

[!INCLUDE[](~/includes/net-core-prereqs-vs2017-2.2.md)] 

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code) 

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]    

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)   

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]    

--- 

## <a name="create-a-web-project"></a>Vytvoření webového projektu 

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio/)  

* V nabídce vyberte **Soubor > Nový projekt**. 

* V dialogovém okně **Nový projekt** vyberte **možnost Nainstalováno > jazyce Visual C# > Web > ASP.NET základní webová aplikace**. Název projektu *SignalRChat*. 

  ![Dialogové okno Nový projekt v sadě Visual Studio](signalr/_static/2.x/signalr-new-project-dialog.png)    

* Vyberte **webovou aplikaci** a vytvořte projekt, který používá Razor Pages. 

* Vyberte cílovou architekturu **rozhraní .NET Core**, vyberte **ASP.NET Core 2.2**a klepněte na tlačítko **OK**.    

  ![Dialogové okno Nový projekt v sadě Visual Studio](signalr/_static/2.x/signalr-new-project-choose-type.png)   

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code/)    

* Otevřete [integrovaný terminál](https://code.visualstudio.com/docs/editor/integrated-terminal) do složky, ve které bude vytvořena nová složka projektu.  

* Spusťte následující příkazy:   

   ```dotnetcli 
   dotnet new webapp -o SignalRChat 
   code -r SignalRChat  
   ```  

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)   

* V nabídce vyberte **Soubor > nové řešení**.    

* Vyberte **> > .NET Core > App ASP.NET Core Web App** (Nevybírejte ASP.NET Core Web App **(MVC).**  

* Vyberte **další**.  

* Pojmenujte projekt *SignalRChat*a pak vyberte **vytvořit**.   

--- 

## <a name="add-the-signalr-client-library"></a>Přidání klientské knihovny SignalR   

Knihovna serveru SignalR je `Microsoft.AspNetCore.App` součástí metabalíčku. Klientská knihovna JavaScriptu není automaticky zahrnuta do projektu. V tomto kurzu pomocí Správce knihovny (LibMan) získat klientskou knihovnu z *unpkg*. unpkg je síť pro doručování obsahu (CDN), která může dodat cokoli, co se nachází v npm, správce balíčků Node.js. 

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio/)  

* V **Průzkumníku řešení**klepněte pravým tlačítkem myši na projekt a vyberte **přidat** > **knihovnu na straně klienta**.  

* V dialogovém okně **Přidat knihovnu na straně klienta** vyberte pro **zprostředkovatele** **hodnotu nepkg**. 

* Do **knihovny**zadejte `@microsoft/signalr@3`a vyberte nejnovější verzi, která není v náhledu.  

  ![Dialogové okno Přidat knihovnu na straně klienta – vybrat knihovnu](signalr/_static/2.x/libman1.png)   

* Vyberte **Vybrat konkrétní soubory**, rozbalte složku *dist/browser* a vyberte *signalr.js* a *signalr.min.js*. 

* Nastavte **cílové umístění** na *wwwroot/lib/signalr/* a vyberte **Instalovat**.    

  ![Dialogové okno Přidat knihovnu na straně klienta – výběr souborů a cíl](signalr/_static/2.x/libman2.png) 

  LibMan vytvoří složku *wwwroot/lib/signalr* a zkopíruje do ní vybrané soubory.    

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code/)    

* V integrovaném terminálu spusťte následující příkaz pro instalaci LibMan.  

  ```dotnetcli  
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli   
  ```   

* Spusťte následující příkaz, abyste získali klientskou knihovnu SignalR pomocí LibMan. Možná budete muset počkat několik sekund, než se zzobrazuje výstup.   

  ```console    
  libman install @microsoft/signalr -p unpkg -d wwwroot/lib/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js 
  ```   

  Parametry určují následující možnosti: 
  * Použijte zprostředkovatele unpkg. 
  * Zkopírujte soubory do cíle *wwwroot/lib/signalr.*    
  * Zkopírujte pouze zadané soubory.  

  Výstup vypadá takto:  

  ```console    
  wwwroot/lib/signalr/dist/browser/signalr.js written to disk   
  wwwroot/lib/signalr/dist/browser/signalr.min.js written to disk   
  Installed library "@microsoft/signalr@3.0.1" to "wwwroot/lib/signalr" 
  ```   

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)   

* V **terminálu**spusťte následující příkaz pro instalaci LibMan. 

  ```dotnetcli  
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli   
  ```   

* Přejděte do složky projektu (ten, který obsahuje soubor *SignalRChat.csproj).* 

* Spusťte následující příkaz, abyste získali klientskou knihovnu SignalR pomocí LibMan.  

  ```console    
  libman install @microsoft/signalr -p unpkg -d wwwroot/lib/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js 
  ```   

  Parametry určují následující možnosti: 
  * Použijte zprostředkovatele unpkg. 
  * Zkopírujte soubory do cíle *wwwroot/lib/signalr.*    
  * Zkopírujte pouze zadané soubory.  

  Výstup vypadá takto:  

  ```console    
  wwwroot/lib/signalr/dist/browser/signalr.js written to disk   
  wwwroot/lib/signalr/dist/browser/signalr.min.js written to disk   
  Installed library "@microsoft/signalr@3.x.x" to "wwwroot/lib/signalr" 
  ```   

--- 

## <a name="create-a-signalr-hub"></a>Vytvoření rozbočovače SignalR 

*Rozbočovač* je třída, která slouží jako kanál vysoké úrovně, který zpracovává komunikaci mezi klientem a serverem.   

* Ve složce projektu SignalRChat vytvořte složku *Hubs.*    

* Ve složce *Rozbočovače* vytvořte *soubor ChatHub.cs* s následujícím kódem: 

  [!code-csharp[Startup](signalr/sample-snapshot/2.x/ChatHub.cs)]   

  Třída `ChatHub` dědí z SignalR `Hub` třídy. Třída `Hub` spravuje připojení, skupiny a zasílání zpráv.    

  Metodu `SendMessage` může volat připojený klient a odeslat zprávu všem klientům. JavaScript klientský kód, který volá metodu je zobrazen později v kurzu. SignalR kód je asynchronní poskytnout maximální škálovatelnost.  

## <a name="configure-signalr"></a>Konfigurace signalismu    

Server SignalR musí být nakonfigurován tak, aby předává požadavky SignalR signalr.  

* Přidejte do *souboru Startup.cs* následující zvýrazněný kód.  

  [!code-csharp[Startup](signalr/sample-snapshot/2.x/Startup.cs?highlight=7,33,52-55)]  

  Tyto změny přidat SignalR do systému vkládání závislostí ASP.NET jádra a middleware kanálu.    

## <a name="add-signalr-client-code"></a>Přidat klientský kód SignalR  

* Nahraďte obsah *stránky\Index.cshtml* následujícím kódem:  

  [!code-cshtml[Index](signalr/sample-snapshot/2.x/Index.cshtml)]   

  Předcházející kód:   

  * Vytvoří textová pole pro text názvu a zprávy a tlačítko odeslat.  
  * Vytvoří seznam `id="messagesList"` s pro zobrazení zpráv, které jsou přijímány z rozbočovače SignalR. 
  * Zahrnuje odkazy na skripty SignalR a kód aplikace *chat.js,* který vytvoříte v dalším kroku.  

* Ve složce *wwwroot/js* vytvořte soubor *chat.js* s následujícím kódem:  

  [!code-javascript[Index](signalr/sample-snapshot/2.x/chat.js)]    

  Předcházející kód:   

  * Vytvoří a spustí připojení.    
  * Přidá k tlačítku odeslat obslužnou rutinu, která odesílá zprávy do centra. 
  * Přidá k objektu připojení obslužnou rutinu, která přijímá zprávy z rozbočovače a přidá je do seznamu.  

## <a name="run-the-app"></a>Spuštění aplikace  

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)   

* Stisknutím **kláves CTRL+F5** spusťte aplikaci bez ladění.   

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code) 

* V integrovaném terminálu spusťte následující příkaz:    

  ```dotnetcli
  dotnet run -p SignalRChat.csproj
  ```

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

* V nabídce vyberte **Spustit > start bez ladění**.

---

* Zkopírujte adresu URL z adresního řádku, otevřete jinou instanci prohlížeče nebo kartu a vložte ji do adresního řádku.

* Zvolte některý z prohlížečů, zadejte jméno a zprávu a vyberte tlačítko **Odeslat zprávu.**  

  Název a zpráva se zobrazí na obou stránkách okamžitě.   

  ![SignalRukázková aplikace](signalr/_static/2.x/signalr-get-started-finished.png) 

> [!TIP]    
> Pokud aplikace nefunguje, otevřete nástroje pro vývojáře prohlížeče (F12) a přejděte do konzole. Mohou se zobrazit chyby související s kódem HTML a JavaScript. Předpokládejme například, že vložíte *soubor signalr.js* do jiné složky, než bylo nařízeno. V takovém případě nebude odkaz na tento soubor fungovat a v konzole se zobrazí chyba 404.   
> ![soubor signalr.js nebyl nalezen chyba](signalr/_static/2.x/f12-console.png)    
## <a name="additional-resources"></a>Další zdroje 
* [Youtube verze tohoto výukového programu](https://www.youtube.com/watch?v=iKlVmu-r0JQ)   

::: moniker-end
