---
title: Použití ASP.NET SignalR Blazor jádra s webovou sestavou
author: guardrex
description: Vytvořte chatovací aplikaci, která používá ASP.NET Core SignalR s Blazor WebAssembly.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/21/2020
no-loc:
- Blazor
- SignalR
uid: tutorials/signalr-blazor-webassembly
ms.openlocfilehash: 03db8b48bdacec1d6877a4ea09f97c242761c42d
ms.sourcegitcommit: f976dce28ad887bbd31720c318fd4a97cf96cc6d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81738014"
---
# <a name="use-aspnet-core-signalr-with-blazor-webassembly"></a>Použití ASP.NET core signalr s Blazor WebAssembly

[Daniel Roth](https://github.com/danroth27) a [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Tento kurz učí základy vytváření aplikace v reálném čase pomocí SignalR s Blazor WebAssembly. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvoření projektu aplikace Hostované na webových shromažďovacích sestaveních Blazor
> * Přidání klientské knihovny SignalR
> * Přidání rozbočovače SignalR
> * Přidání služeb SignalR a koncového bodu pro rozbočovač SignalR
> * Přidat kód komponenty Razor pro chat

Na konci tohoto kurzu budete mít pracovní chatovací aplikaci.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) [(jak stáhnout)](xref:index#how-to-download-a-sample)

## <a name="prerequisites"></a>Požadavky

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

# <a name="net-core-cli"></a>[Rozhraní příkazového řádku .NET Core](#tab/netcore-cli/)

[!INCLUDE[](~/includes/3.1-SDK.md)]

---

## <a name="create-a-hosted-blazor-webassembly-app-project"></a>Vytvoření hostovaného projektu aplikace Blazor WebAssembly

Pokud nepoužíváte Visual Studio verze 16.6 Preview 2 nebo novější, nainstalujte šablonu [Blazor WebAssembly.](xref:blazor/hosting-models#blazor-webassembly) Balíček [Microsoft.AspNetCore.Components.WebAssembly.Templates](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Templates/) má verzi preview, zatímco Blazor WebAssembly je ve verzi preview. V příkazovém prostředí proveďte následující příkaz:

```dotnetcli
dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-preview4.20210.8
```

Postupujte podle pokynů pro výběr nástrojů:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Vytvoření nového projektu

1. Vyberte **Blazor App** a vyberte **Další**.

1. Do pole **Název projektu** zadejte "BlazorSignalRApp". Potvrďte, že položka **Umístění** je správná, nebo zadejte umístění projektu. Vyberte **Vytvořit**.

1. Zvolte šablonu **aplikace Blazor WebAssembly.**

1. V části **Upřesnit**zaškrtněte **políčko ASP.NET Jádro hostované.**

1. Vyberte **Vytvořit**.

> [!NOTE]
> Pokud jste upgradovali nebo nainstalovali novou verzi sady Visual Studio a šablona Blazor WebAssembly se v `dotnet new` uživatelském jazyce VS nezobrazí, přeinstalujte šablonu pomocí dříve zobrazeného příkazu.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. V příkazovém prostředí proveďte následující příkaz:

   ```dotnetcli
   dotnet new blazorwasm --hosted --output BlazorSignalRApp
   ```

1. V kódu Visual Studia otevřete složku projektu aplikace.

1. Když se zobrazí dialogové okno pro přidání datových zdrojů k sestavení a ladění aplikace, vyberte **Ano**. Visual Studio Code automaticky přidá složku *.vscode* s generovanými soubory *launch.json* a *tasks.json.*

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

1. V příkazovém prostředí proveďte následující příkaz:

   ```dotnetcli
   dotnet new blazorwasm --hosted --output BlazorSignalRApp
   ```

1. V sadě Visual Studio for Mac otevřete projekt tak, že přejdete do složky projektu a otevřete soubor řešení projektu (*.sln*).

# <a name="net-core-cli"></a>[Rozhraní příkazového řádku .NET Core](#tab/netcore-cli/)

V příkazovém prostředí proveďte následující příkaz:

```dotnetcli
dotnet new blazorwasm --hosted --output BlazorSignalRApp
```

---

## <a name="add-the-signalr-client-library"></a>Přidání klientské knihovny SignalR

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio/)

1. V **Průzkumníku řešení**klepněte pravým tlačítkem myši na projekt **BlazorSignalRApp.Client** a vyberte **příkaz Spravovat balíčky NuGet**.

1. V dialogovém okně **Spravovat balíčky NuGet** zkontrolujte, zda je **zdroj balíčku** nastaven na *nuget.org*.

1. S **vybranou možností Procházet** zadejte do vyhledávacího pole "Microsoft.AspNetCore.SignalR.Client".

1. Ve výsledcích hledání `Microsoft.AspNetCore.SignalR.Client` vyberte balíček a vyberte **Instalovat**.

1. Pokud se zobrazí dialogové okno **Náhled změn,** vyberte **OK**.

1. Pokud se zobrazí dialogové okno **Přijetí licence,** vyberte **Souhlasím,** pokud souhlasíte s licenčními podmínkami.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code/)

V **integrovaném terminálu** (**Zobrazit** > **terminál** z panelu nástrojů) proveďte následující příkazy:

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

1. V postranním panelu **Řešení** klepněte pravým tlačítkem myši na projekt **BlazorSignalRApp.Client** a vyberte **spravovat balíčky NuGet**.

1. V dialogovém okně **Spravovat balíčky NuGet** zkontrolujte, zda je zdrojový rozevírací seznam nastaven na *nuget.org*.

1. S **vybranou možností Procházet** zadejte do vyhledávacího pole "Microsoft.AspNetCore.SignalR.Client".

1. Ve výsledcích hledání zaškrtněte políčko `Microsoft.AspNetCore.SignalR.Client` vedle balíčku a vyberte **Přidat balíček**.

1. Pokud se zobrazí dialogové okno **Přijetí licence,** vyberte **Přijmout,** pokud s licenčními podmínkami souhlasíte.

# <a name="net-core-cli"></a>[Rozhraní příkazového řádku .NET Core](#tab/netcore-cli/)

V příkazovém prostředí proveďte následující příkazy:

```dotnetcli
cd BlazorSignalRApp
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

---

## <a name="add-a-signalr-hub"></a>Přidání rozbočovače SignalR

V projektu **BlazorSignalRApp.Server** vytvořte složku *Hubs* (plural) `ChatHub` a přidejte následující třídu (*Hubs/ChatHub.cs*):

[!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Hubs/ChatHub.cs)]

## <a name="add-services-and-an-endpoint-for-the-signalr-hub"></a>Přidání služeb a koncového bodu pro rozbočovač SignalR

1. V projektu **BlazorSignalRApp.Server** otevřete soubor *Startup.cs.*

1. Přidejte obor názvů `ChatHub` pro třídu na začátek souboru:

   ```csharp
   using BlazorSignalRApp.Server.Hubs;
   ```

1. Přidat SignalR a komprese odezvy Middleware služby `Startup.ConfigureServices`:

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,5-9)]

1. Mezi `Startup.Configure` koncové body pro řadiče a záložní straně klienta přidejte koncový bod pro rozbočovač:

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_UseEndpoints&highlight=4)]

## <a name="add-razor-component-code-for-chat"></a>Přidat kód komponenty Razor pro chat

1. V projektu **BlazorSignalRApp.Client** otevřete soubor *Pages/Index.razor.*

1. Nahraďte značku následujícím kódem:

[!code-razor[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Client/Pages/Index.razor)]

## <a name="run-the-app"></a>Spuštění aplikace

1. Postupujte podle pokynů pro vaše nástroje:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. V **Průzkumníku řešení**vyberte projekt **BlazorSignalRApp.Server.** Stisknutím <kbd>klávesy F5</kbd> spusťte aplikaci s laděním nebo <kbd>klávesou Ctrl</kbd>+<kbd>F5,</kbd> chcete-li aplikaci spustit bez ladění.

1. Zkopírujte adresu URL z adresního řádku, otevřete jinou instanci prohlížeče nebo kartu a vložte ji do adresního řádku.

1. Zvolte některý z prohlížečů, zadejte jméno a zprávu a vyberte tlačítko **Odeslat.** Jméno a zpráva se zobrazí na obou stránkách okamžitě:

   ![SignalR Blazor WebAssembly ukázková aplikace otevřena ve dvou oknech prohlížeče zobrazujících vyměňované zprávy.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   Citáty: *Star Trek VI: Neobjevená země* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. Když VS Code nabízí vytvoření profilu spuštění aplikace Server (*.vscode/launch.json*), `program` položka se zobrazí podobně`{APPLICATION NAME}.Server.dll`jako následující, aby ukazovala na sestavení aplikace ( ):

   ```json
   "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/{APPLICATION NAME}.Server.dll"
   ```

1. Stisknutím <kbd>klávesy F5</kbd> spusťte aplikaci s laděním nebo <kbd>klávesou Ctrl</kbd>+<kbd>F5,</kbd> chcete-li aplikaci spustit bez ladění.

1. Zkopírujte adresu URL z adresního řádku, otevřete jinou instanci prohlížeče nebo kartu a vložte ji do adresního řádku.

1. Zvolte některý z prohlížečů, zadejte jméno a zprávu a vyberte tlačítko **Odeslat.** Jméno a zpráva se zobrazí na obou stránkách okamžitě:

   ![SignalR Blazor WebAssembly ukázková aplikace otevřena ve dvou oknech prohlížeče zobrazujících vyměňované zprávy.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   Citáty: *Star Trek VI: Neobjevená země* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

1. V postranním panelu **Řešení** vyberte projekt **BlazorSignalRApp.Server.** Stisknutím <kbd>tlačítka</kbd>+<kbd>↩</kbd>** spustíte aplikaci s laděním nebo <kbd>⌥</kbd>+<kbd>↩</kbd>+<kbd>↩</kbd> pro spuštění aplikace bez ladění.

1. Zkopírujte adresu URL z adresního řádku, otevřete jinou instanci prohlížeče nebo kartu a vložte ji do adresního řádku.

1. Zvolte některý z prohlížečů, zadejte jméno a zprávu a vyberte tlačítko **Odeslat.** Jméno a zpráva se zobrazí na obou stránkách okamžitě:

   ![SignalR Blazor WebAssembly ukázková aplikace otevřena ve dvou oknech prohlížeče zobrazujících vyměňované zprávy.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   Citáty: *Star Trek VI: Neobjevená země* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="net-core-cli"></a>[Rozhraní příkazového řádku .NET Core](#tab/netcore-cli/)

1. V příkazovém prostředí proveďte následující příkazy:

   ```dotnetcli
   cd Server
   dotnet run
   ```

1. Zkopírujte adresu URL z adresního řádku, otevřete jinou instanci prohlížeče nebo kartu a vložte ji do adresního řádku.

1. Zvolte některý z prohlížečů, zadejte jméno a zprávu a vyberte tlačítko **Odeslat.** Jméno a zpráva se zobrazí na obou stránkách okamžitě:

   ![SignalR Blazor WebAssembly ukázková aplikace otevřena ve dvou oknech prohlížeče zobrazujících vyměňované zprávy.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   Citáty: *Star Trek VI: Neobjevená země* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

---

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Vytvoření Blazor projektu hostované webové sestavy
> * Přidání SignalR klientské knihovny
> * Přidání SignalR rozbočovače
> * Přidání SignalR služeb a koncového SignalR bodu pro rozbočovač
> * Přidat kód komponenty Razor pro chat

Další informace o Blazor vytváření aplikací Blazor najdete v dokumentaci:

> [!div class="nextstepaction"]
> <xref:blazor/index>

## <a name="additional-resources"></a>Další zdroje

* <xref:signalr/introduction>
