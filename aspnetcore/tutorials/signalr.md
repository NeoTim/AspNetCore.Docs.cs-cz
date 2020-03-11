---
title: Začínáme s ASP.NET Core SignalR
author: bradygaster
description: V tomto kurzu vytvoříte aplikaci Chat, která používá ASP.NET Core SignalR.
ms.author: bradyg
ms.custom: mvc
ms.date: 11/21/2019
no-loc:
- SignalR
uid: tutorials/signalr
ms.openlocfilehash: 55ebdbfa4556deca74a6cdf0638307425cd1a01a
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78666745"
---
# <a name="tutorial-get-started-with-aspnet-core-signalr"></a>Kurz: Začínáme s nástrojem ASP.NET Core Signal

::: moniker range=">= aspnetcore-3.0"

V tomto kurzu se naučíte základy vytváření aplikací v reálném čase pomocí nástroje Signal. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvoření webového projektu.
> * Přidejte klientskou knihovnu signalizace.
> * Vytvořte centrum signalizace.
> * Nakonfigurujte projekt tak, aby používal signál.
> * Přidejte kód, který odesílá zprávy od libovolného klienta ke všem připojeným klientům.

Na konci budete mít funkční chatovací aplikaci:

![Ukázková aplikace pro Signal](signalr/_static/3.x/signalr-get-started-finished.png)

## <a name="prerequisites"></a>Předpoklady

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="create-a-web-app-project"></a>Vytvoření projektu webové aplikace

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio/)

* V nabídce vyberte **soubor > nový projekt**.

* V dialogovém okně **vytvořit nový projekt** vyberte **ASP.NET Core webová aplikace**a pak vyberte **Další**.

* V dialogovém okně **Konfigurovat nový projekt** pojmenujte projekt *SignalRChat*a pak vyberte **vytvořit**.

* V dialogovém okně **vytvořit novou webovou aplikaci ASP.NET Core** vyberte **.net Core** a **ASP.NET Core 3,0**. 

* Vyberte **Webová aplikace** , chcete-li vytvořit projekt, který používá Razor Pages a pak vyberte **vytvořit**.

  ![Dialogové okno Nový projekt v aplikaci Visual Studio](signalr/_static/3.x/signalr-new-project-dialog.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code/)

* Otevřete [integrovaný terminál](https://code.visualstudio.com/docs/editor/integrated-terminal) do složky, ve které bude vytvořena nová složka projektu.

* Spusťte následující příkazy:

   ```dotnetcli
   dotnet new webapp -o SignalRChat
   code -r SignalRChat
   ```

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

* V nabídce vyberte **soubor > nové řešení**.

* Vyberte **.NET Core > App > Web Application** (nevybírejte možnost **Webová aplikace (Model-View-Controller)** ) a pak vyberte **Další**.

* Ujistěte se, že je **Cílová architektura** nastavená na **.NET Core 3,0**, a pak vyberte **Další**.

* Pojmenujte projekt *SignalRChat*a pak vyberte **vytvořit**.

---

## <a name="add-the-signalr-client-library"></a>Přidat klientskou knihovnu signálů

Knihovna serveru signalizace je součástí sdílené architektury ASP.NET Core 3,0. Klientská knihovna pro JavaScript není automaticky obsažena v projektu. Pro tento kurz použijte Správce knihovny (LibMan) k získání klientské knihovny z *unpkg*. unpkg je síť pro doručování obsahu (CDN), která může doručovat cokoli, co najdete v NPM, správce balíčků Node. js.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio/)

* V **Průzkumník řešení**klikněte pravým tlačítkem myši na projekt a vyberte **Přidat** > **knihovny na straně klienta**.

* V dialogovém okně **přidat knihovnu na straně klienta** vyberte pro **poskytovatele** možnost **unpkg**.

* V případě **knihovny**zadejte `@microsoft/signalr@latest`.

* Vyberte možnost **zvolit konkrétní soubory**, rozbalte složku *DIST/prohlížeč* a vyberte možnost *signaler. js* a *Signal. min. js*.

* Nastavte **cílové umístění** na *wwwroot/js/signál/* a vyberte **nainstalovat**.

  ![Dialogové okno Přidat knihovnu na straně klienta – výběr knihovny](signalr/_static/3.x/find-signalr-client-libs-select-files.png)

  LibMan vytvoří složku *wwwroot/js/signaler* a zkopíruje na ni vybrané soubory.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code/)

* V integrovaném terminálu spusťte následující příkaz pro instalaci LibMan.

  ```dotnetcli
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli
  ```

* Spusťte následující příkaz, který načte klientskou knihovnu pro signalizaci pomocí LibMan. Než začnete výstup zobrazovat, možná budete muset počkat několik sekund.

  ```console
  libman install @microsoft/signalr@latest -p unpkg -d wwwroot/js/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js
  ```

  Parametry určují následující možnosti:
  * Použijte poskytovatele unpkg.
  * Zkopírujte soubory do cílového umístění *wwwroot/js/Signal* .
  * Zkopírujte pouze zadané soubory.

  Výstup bude vypadat jako v následujícím příkladu:

  ```console
  wwwroot/js/signalr/dist/browser/signalr.js written to disk
  wwwroot/js/signalr/dist/browser/signalr.min.js written to disk
  Installed library "@microsoft/signalr@latest" to "wwwroot/js/signalr"
  ```

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

* V **terminálu**spusťte následující příkaz, kterým nainstalujete LibMan.

  ```dotnetcli
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli
  ```

* Přejděte do složky projektu (ta, která obsahuje soubor *SignalRChat. csproj* ).

* Spusťte následující příkaz, který načte klientskou knihovnu pro signalizaci pomocí LibMan.

  ```console
  libman install @microsoft/signalr@latest -p unpkg -d wwwroot/js/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js
  ```

  Parametry určují následující možnosti:
  * Použijte poskytovatele unpkg.
  * Zkopírujte soubory do cílového umístění *wwwroot/js/Signal* .
  * Zkopírujte pouze zadané soubory.

  Výstup bude vypadat jako v následujícím příkladu:

  ```console
  wwwroot/js/signalr/dist/browser/signalr.js written to disk
  wwwroot/js/signalr/dist/browser/signalr.min.js written to disk
  Installed library "@microsoft/signalr@latest" to "wwwroot/js/signalr"
  ```

---

## <a name="create-a-signalr-hub"></a>Vytvoření centra signalizace

*Centrum* je třída, která slouží jako kanál vysoké úrovně, který zpracovává komunikaci mezi klientem a serverem.

* Ve složce projektu SignalRChat vytvořte složku *Centers* .

* Ve složce *Centers* vytvořte soubor *ChatHub.cs* s následujícím kódem:

  [!code-csharp[ChatHub](signalr/sample-snapshot/3.x/ChatHub.cs)]

  Třída `ChatHub` dědí z třídy `Hub` signálu. Třída `Hub` spravuje připojení, skupiny a zasílání zpráv.

  Metodu `SendMessage` lze volat připojeným klientem pro odeslání zprávy všem klientům. JavaScriptový kód klienta, který volá metodu, je uveden dále v tomto kurzu. Kód signalizace je asynchronní pro zajištění maximální škálovatelnosti.

## <a name="configure-signalr"></a>Konfigurovat signál

Server signalizace musí být nakonfigurován tak, aby předával požadavky na signalizaci.

* Do souboru *Startup.cs* přidejte následující zvýrazněný kód.

  [!code-csharp[Startup](signalr/sample-snapshot/3.x/Startup.cs?highlight=11,28,55)]

  Tyto změny přidávají signál k systémům injektáže a směrování závislostí ASP.NET Core.

## <a name="add-signalr-client-code"></a>Přidat kód klienta signalizace

* Nahraďte obsah v *Pages\Index.cshtml* následujícím kódem:

  [!code-cshtml[Index](signalr/sample-snapshot/3.x/Index.cshtml)]

  Předchozí kód:

  * Vytvoří textová pole pro text jméno a zprávu a tlačítko Odeslat.
  * Vytvoří seznam s `id="messagesList"` pro zobrazování zpráv, které byly přijaty z centra signalizace.
  * Obsahuje odkazy na skripty pro signál a kód aplikace *chat. js* , který vytvoříte v dalším kroku.

* Ve složce *wwwroot/js* vytvořte soubor *chat. js* s následujícím kódem:

  [!code-javascript[chat](signalr/sample-snapshot/3.x/chat.js)]

  Předchozí kód:

  * Vytvoří a spustí připojení.
  * Přidá k tlačítku Odeslat obslužnou rutinu, která odesílá zprávy do centra.
  * Přidá do objektu Connection obslužnou rutinu, která přijímá zprávy z centra a přidá je do seznamu.

## <a name="run-the-app"></a>Spuštění aplikace

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Stisknutím **kombinace kláves CTRL + F5** aplikaci spusťte bez ladění.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* V integrovaném terminálu spusťte následující příkaz:

  ```dotnetcli
  dotnet watch run -p SignalRChat.csproj
  ```

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

* V nabídce vyberte **spustit > spustit bez ladění**.

---

* Zkopírujte adresu URL z panelu Adresa, otevřete jinou instanci nebo kartu prohlížeče a vložte adresu URL do panelu Adresa.

* Zvolte buď prohlížeč, zadejte jméno a zprávu a klikněte na tlačítko **Odeslat zprávu** .

  Název a zpráva se okamžitě zobrazí na obou stránkách.

  ![Ukázková aplikace pro Signal](signalr/_static/3.x/signalr-get-started-finished.png)

> [!TIP]
> * Pokud aplikace nefunguje, otevřete nástroje pro vývojáře v prohlížeči (F12) a pokračujte na konzolu. Můžou se zobrazit chyby týkající se kódu HTML a JavaScriptu. Předpokládejme například, že umístíte *signál. js* do jiné složky než směrovaná. V takovém případě odkaz na tento soubor nebude fungovat a v konzole se zobrazí chyba 404.
>   Chyba ![signaler. js nebyla nalezena](signalr/_static/3.x/f12-console.png)
> * Pokud se zobrazí chyba ERR_SPDY_INADEQUATE_TRANSPORT_SECURITY v Chrome, spusťte tyto příkazy a aktualizujte svůj vývojový certifikát:
>
>   ```dotnetcli
>   dotnet dev-certs https --clean
>   dotnet dev-certs https --trust
>   ```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

V tomto kurzu se naučíte základy vytváření aplikací v reálném čase pomocí nástroje Signal. Získáte informace o těchto tématech:   

> [!div class="checklist"]  
> * Vytvoření webového projektu.   
> * Přidejte klientskou knihovnu signalizace. 
> * Vytvořte centrum signalizace.   
> * Nakonfigurujte projekt tak, aby používal signál.   
> * Přidejte kód, který odesílá zprávy od libovolného klienta ke všem připojeným klientům.  
Na konci budete mít funkční chatovací aplikaci: Ukázková aplikace ![Signal](signalr/_static/2.x/signalr-get-started-finished.png) 

## <a name="prerequisites"></a>Předpoklady    

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)   

[!INCLUDE[](~/includes/net-core-prereqs-vs2017-2.2.md)] 

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code) 

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]    

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)   

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]    

--- 

## <a name="create-a-web-project"></a>Vytvoření webového projektu 

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio/)  

* V nabídce vyberte **soubor > nový projekt**. 

* V dialogovém okně **Nový projekt** vyberte možnost **nainstalováno > C# webové aplikace Visual > Web > ASP.NET Core**. Pojmenujte projekt *SignalRChat*. 

  ![Dialogové okno Nový projekt v aplikaci Visual Studio](signalr/_static/2.x/signalr-new-project-dialog.png)    

* Vyberte **Webová aplikace** a vytvořte tak projekt, který používá Razor Pages. 

* Vyberte cílovou architekturu **.NET Core**, vyberte **ASP.NET Core 2,2**a klikněte na **OK**.    

  ![Dialogové okno Nový projekt v aplikaci Visual Studio](signalr/_static/2.x/signalr-new-project-choose-type.png)   

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code/)    

* Otevřete [integrovaný terminál](https://code.visualstudio.com/docs/editor/integrated-terminal) do složky, ve které bude vytvořena nová složka projektu.  

* Spusťte následující příkazy:   

   ```dotnetcli 
   dotnet new webapp -o SignalRChat 
   code -r SignalRChat  
   ```  

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)   

* V nabídce vyberte **soubor > nové řešení**.    

* Vyberte **.NET Core > App > ASP.NET Core Web App** (nevybírejte **ASP.NET Core Web App (MVC)** ).  

* Vyberte **Další**.  

* Pojmenujte projekt *SignalRChat*a pak vyberte **vytvořit**.   

--- 

## <a name="add-the-signalr-client-library"></a>Přidat klientskou knihovnu signálů   

Knihovna serveru signalizace je součástí `Microsoft.AspNetCore.App` Metapackage. Klientská knihovna pro JavaScript není automaticky obsažena v projektu. Pro tento kurz použijte Správce knihovny (LibMan) k získání klientské knihovny z *unpkg*. unpkg je síť pro doručování obsahu (CDN), která může doručovat cokoli, co najdete v NPM, správce balíčků Node. js.    

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio/)  

* V **Průzkumník řešení**klikněte pravým tlačítkem myši na projekt a vyberte **Přidat** > **knihovny na straně klienta**.  

* V dialogovém okně **přidat knihovnu na straně klienta** vyberte pro **poskytovatele** možnost **unpkg**. 

* V případě **knihovny**zadejte `@microsoft/signalr@3`a vyberte nejnovější verzi, která není ve verzi Preview.  

  ![Dialogové okno Přidat knihovnu na straně klienta – výběr knihovny](signalr/_static/2.x/libman1.png)   

* Vyberte možnost **zvolit konkrétní soubory**, rozbalte složku *DIST/prohlížeč* a vyberte možnost *signaler. js* a *Signal. min. js*. 

* Nastavte **cílové umístění** na *wwwroot/lib/Signal/* a vyberte **nainstalovat**.    

  ![Dialogové okno Přidat knihovnu na straně klienta – výběr souborů a cíle](signalr/_static/2.x/libman2.png) 

  LibMan vytvoří složku *wwwroot/lib/signaler* a zkopíruje na ni vybrané soubory.    

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code/)    

* V integrovaném terminálu spusťte následující příkaz pro instalaci LibMan.  

  ```dotnetcli  
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli   
  ```   

* Spusťte následující příkaz, který načte klientskou knihovnu pro signalizaci pomocí LibMan. Než začnete výstup zobrazovat, možná budete muset počkat několik sekund.   

  ```console    
  libman install @microsoft/signalr -p unpkg -d wwwroot/lib/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js 
  ```   

  Parametry určují následující možnosti: 
  * Použijte poskytovatele unpkg. 
  * Zkopírujte soubory do cílového umístění *wwwroot/lib/Signal* .    
  * Zkopírujte pouze zadané soubory.  

  Výstup bude vypadat jako v následujícím příkladu:  

  ```console    
  wwwroot/lib/signalr/dist/browser/signalr.js written to disk   
  wwwroot/lib/signalr/dist/browser/signalr.min.js written to disk   
  Installed library "@microsoft/signalr@3.0.1" to "wwwroot/lib/signalr" 
  ```   

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)   

* V **terminálu**spusťte následující příkaz, kterým nainstalujete LibMan. 

  ```dotnetcli  
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli   
  ```   

* Přejděte do složky projektu (ta, která obsahuje soubor *SignalRChat. csproj* ). 

* Spusťte následující příkaz, který načte klientskou knihovnu pro signalizaci pomocí LibMan.  

  ```console    
  libman install @microsoft/signalr -p unpkg -d wwwroot/lib/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js 
  ```   

  Parametry určují následující možnosti: 
  * Použijte poskytovatele unpkg. 
  * Zkopírujte soubory do cílového umístění *wwwroot/lib/Signal* .    
  * Zkopírujte pouze zadané soubory.  

  Výstup bude vypadat jako v následujícím příkladu:  

  ```console    
  wwwroot/lib/signalr/dist/browser/signalr.js written to disk   
  wwwroot/lib/signalr/dist/browser/signalr.min.js written to disk   
  Installed library "@microsoft/signalr@3.x.x" to "wwwroot/lib/signalr" 
  ```   

--- 

## <a name="create-a-signalr-hub"></a>Vytvoření centra signalizace 

*Centrum* je třída, která slouží jako kanál vysoké úrovně, který zpracovává komunikaci mezi klientem a serverem.   

* Ve složce projektu SignalRChat vytvořte složku *Centers* .    

* Ve složce *Centers* vytvořte soubor *ChatHub.cs* s následujícím kódem: 

  [!code-csharp[Startup](signalr/sample-snapshot/2.x/ChatHub.cs)]   

  Třída `ChatHub` dědí z třídy `Hub` signálu. Třída `Hub` spravuje připojení, skupiny a zasílání zpráv.    

  Metodu `SendMessage` lze volat připojeným klientem pro odeslání zprávy všem klientům. JavaScriptový kód klienta, který volá metodu, je uveden dále v tomto kurzu. Kód signalizace je asynchronní pro zajištění maximální škálovatelnosti.  

## <a name="configure-signalr"></a>Konfigurovat signál    

Server signalizace musí být nakonfigurován tak, aby předával požadavky na signalizaci.  

* Do souboru *Startup.cs* přidejte následující zvýrazněný kód.  

  [!code-csharp[Startup](signalr/sample-snapshot/2.x/Startup.cs?highlight=7,33,52-55)]  

  Tyto změny přidávají signál do systému vkládání závislostí ASP.NET Core a do kanálu middlewaru.    

## <a name="add-signalr-client-code"></a>Přidat kód klienta signalizace  

* Nahraďte obsah v *Pages\Index.cshtml* následujícím kódem:  

  [!code-cshtml[Index](signalr/sample-snapshot/2.x/Index.cshtml)]   

  Předchozí kód:   

  * Vytvoří textová pole pro text jméno a zprávu a tlačítko Odeslat.  
  * Vytvoří seznam s `id="messagesList"` pro zobrazování zpráv, které byly přijaty z centra signalizace. 
  * Obsahuje odkazy na skripty pro signál a kód aplikace *chat. js* , který vytvoříte v dalším kroku.  

* Ve složce *wwwroot/js* vytvořte soubor *chat. js* s následujícím kódem:  

  [!code-javascript[Index](signalr/sample-snapshot/2.x/chat.js)]    

  Předchozí kód:   

  * Vytvoří a spustí připojení.    
  * Přidá k tlačítku Odeslat obslužnou rutinu, která odesílá zprávy do centra. 
  * Přidá do objektu Connection obslužnou rutinu, která přijímá zprávy z centra a přidá je do seznamu.  

## <a name="run-the-app"></a>Spuštění aplikace  

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)   

* Stisknutím **kombinace kláves CTRL + F5** aplikaci spusťte bez ladění.   

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code) 

* V integrovaném terminálu spusťte následující příkaz:    

  ```dotnetcli
  dotnet run -p SignalRChat.csproj
  ```

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

* V nabídce vyberte **spustit > spustit bez ladění**.

---

* Zkopírujte adresu URL z panelu Adresa, otevřete jinou instanci nebo kartu prohlížeče a vložte adresu URL do panelu Adresa.

* Zvolte buď prohlížeč, zadejte jméno a zprávu a klikněte na tlačítko **Odeslat zprávu** .  

  Název a zpráva se okamžitě zobrazí na obou stránkách.   

  Ukázková aplikace ![SignalR](signalr/_static/2.x/signalr-get-started-finished.png) 

> [!TIP]    
> Pokud aplikace nefunguje, otevřete nástroje pro vývojáře v prohlížeči (F12) a pokračujte na konzolu. Můžou se zobrazit chyby týkající se kódu HTML a JavaScriptu. Předpokládejme například, že umístíte *signál. js* do jiné složky než směrovaná. V takovém případě odkaz na tento soubor nebude fungovat a v konzole se zobrazí chyba 404.   
> Chyba ![signaler. js nebyla nalezena](signalr/_static/2.x/f12-console.png)    
## <a name="additional-resources"></a>Další zdroje 
* [Verze YouTube tohoto kurzu](https://www.youtube.com/watch?v=iKlVmu-r0JQ)   

::: moniker-end
