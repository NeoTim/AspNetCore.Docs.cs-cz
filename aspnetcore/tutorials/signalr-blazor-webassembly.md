---
title: Použití ASP.NET Core SignalR s Blazor WebAssembly
author: guardrex
description: Vytvořte aplikaci Chat, která používá ASP.NET Core SignalR s Blazor WebAssembly.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/30/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/signalr-blazor-webassembly
ms.openlocfilehash: 1579b92dbc9db08bfdc5572e5d4245bd18d50590
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82773785"
---
# <a name="use-aspnet-core-signalr-with-blazor-webassembly"></a>Použití ASP.NET Coreového signálu s Blazor WebAssembly

Od [Daniel Skořepa](https://github.com/danroth27) a [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

V tomto kurzu se naučíte základy vytváření aplikací v reálném čase pomocí nástroje Signaler s Blazor WebAssembly. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvoření projektu hostované aplikace Blazor WebAssembly
> * Přidat klientskou knihovnu signálů
> * Přidat centrum signálů
> * Přidat služby signalizace a koncový bod pro centrum signalizace
> * Přidat kód komponenty Razor pro chat

Na konci tohoto kurzu budete mít funkční chatovací aplikaci.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample))

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

Pokud nepoužíváte Visual Studio verze 16,6 Preview 2 nebo novější, nainstalujte šablonu [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) . Balíček [Microsoft. AspNetCore. Components. WebAssembly. Templates](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Templates/) obsahuje verzi Preview, zatímco Blazor WebAssembly je ve verzi Preview. V příkazovém prostředí spusťte následující příkaz:

```dotnetcli
dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-rc1.20223.4
```

Postupujte podle pokynů pro výběr nástrojů:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Vytvoření nového projektu

1. Vyberte **aplikace Blazor** a vyberte **Další**.

1. Do pole **název projektu** zadejte "BlazorSignalRApp". Potvrďte správnost záznamu **umístění** nebo zadejte umístění projektu. Vyberte **Vytvořit**.

1. Vyberte šablonu **aplikace WebAssembly pro Blazor** .

1. V části **Upřesnit**zaškrtněte políčko **ASP.NET Core hostované** .

1. Vyberte **Vytvořit**.

> [!NOTE]
> Pokud jste provedli upgrade nebo instalaci nové verze sady Visual Studio a šablona WebAssembly Blazor se nezobrazí v uživatelském rozhraní VS, přeinstalujte ji pomocí `dotnet new` příkazu uvedeného výše.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. V příkazovém prostředí spusťte následující příkaz:

   ```dotnetcli
   dotnet new blazorwasm --hosted --output BlazorSignalRApp
   ```

1. V Visual Studio Code otevřete složku projektu aplikace.

1. Když se zobrazí dialogové okno s přidáním assetů pro sestavení a ladění aplikace, vyberte **Ano**. Visual Studio Code automaticky přidá složku *. VSCode* pomocí vygenerovaných souborů *Launch. JSON* a *Tasks. JSON* .

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

1. V příkazovém prostředí spusťte následující příkaz:

   ```dotnetcli
   dotnet new blazorwasm --hosted --output BlazorSignalRApp
   ```

1. V Visual Studio pro Mac otevřete projekt tak, že přejdete do složky projektu a otevřete soubor řešení projektu (*. sln*).

# <a name="net-core-cli"></a>[Rozhraní příkazového řádku .NET Core](#tab/netcore-cli/)

V příkazovém prostředí spusťte následující příkaz:

```dotnetcli
dotnet new blazorwasm --hosted --output BlazorSignalRApp
```

---

## <a name="add-the-signalr-client-library"></a>Přidat klientskou knihovnu signálů

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio/)

1. V **Průzkumník řešení**klikněte pravým tlačítkem myši na projekt **BlazorSignalRApp. Client** a vyberte možnost **Spravovat balíčky NuGet**.

1. V dialogovém okně **Spravovat balíčky NuGet** ověřte, že je **zdroj balíčku** nastavený na *NuGet.org*.

1. Když je vybrána možnost **Procházet** , do vyhledávacího pole zadejte "Microsoft. AspNetCore. signaler. Client".

1. Ve výsledcích hledání vyberte `Microsoft.AspNetCore.SignalR.Client` balíček a vyberte **nainstalovat**.

1. Pokud se zobrazí dialogové okno **Náhled změn** , vyberte **OK**.

1. Pokud se zobrazí dialogové okno pro **přijetí licence** , **Vyberte možnost Souhlasím** , pokud souhlasíte s licenčními podmínkami.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code/)

V **integrovaném terminálu** (**zobrazení** > **terminálu** z panelu nástrojů) spusťte následující příkazy:

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

1. Na bočním panelu **řešení** klikněte pravým tlačítkem myši na projekt **BlazorSignalRApp. Client** a vyberte možnost **Spravovat balíčky NuGet**.

1. V dialogovém okně **Spravovat balíčky NuGet** potvrďte, že je rozevírací seznam zdroj nastavený na *NuGet.org*.

1. Když je vybrána možnost **Procházet** , do vyhledávacího pole zadejte "Microsoft. AspNetCore. signaler. Client".

1. Ve výsledcích hledání zaškrtněte políčko vedle `Microsoft.AspNetCore.SignalR.Client` balíčku a vyberte **Přidat balíček**.

1. Pokud se zobrazí dialogové okno pro **přijetí licence** , vyberte **přijmout** , pokud souhlasíte s licenčními podmínkami.

# <a name="net-core-cli"></a>[Rozhraní příkazového řádku .NET Core](#tab/netcore-cli/)

V příkazovém prostředí spusťte následující příkazy:

```dotnetcli
cd BlazorSignalRApp
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

---

## <a name="add-a-signalr-hub"></a>Přidat centrum signálů

V projektu **BlazorSignalRApp. Server** vytvořte složku *Centers* (plural) a přidejte následující `ChatHub` třídu (*Centers/ChatHub. cs*):

[!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Hubs/ChatHub.cs)]

## <a name="add-services-and-an-endpoint-for-the-signalr-hub"></a>Přidání služeb a koncového bodu pro centrum signalizace

1. V projektu **BlazorSignalRApp. Server** otevřete soubor *Startup.cs* .

1. Přidejte do horní části souboru `ChatHub` obor názvů pro třídu:

   ```csharp
   using BlazorSignalRApp.Server.Hubs;
   ```

1. Přidejte služby middlewaru a služby pro kompresi odpovědí `Startup.ConfigureServices`do:

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,5-9)]

1. Do `Startup.Configure` mezi koncovými body řadičů a záložním serverem na straně klienta přidejte koncový bod pro centrum:

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_UseEndpoints&highlight=4)]

## <a name="add-razor-component-code-for-chat"></a>Přidat kód komponenty Razor pro chat

1. V projektu **BlazorSignalRApp. Client** otevřete soubor *pages/index. Razor* .

1. Nahraďte kód následujícím kódem:

[!code-razor[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Client/Pages/Index.razor)]

## <a name="run-the-app"></a>Spuštění aplikace

1. Postupujte podle pokynů pro vaše nástroje:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. V **Průzkumník řešení**vyberte projekt **BlazorSignalRApp. Server** . Stiskněte klávesu <kbd>F5</kbd> ke spuštění aplikace s laděním nebo <kbd>stisknutím klávesy CTRL</kbd>+<kbd>F5</kbd> pro spuštění aplikace bez ladění.

1. Zkopírujte adresu URL z panelu Adresa, otevřete jinou instanci nebo kartu prohlížeče a vložte adresu URL do panelu Adresa.

1. Zvolte buď prohlížeč, zadejte jméno a zprávu a klikněte na tlačítko **Odeslat** . Název a zpráva se okamžitě zobrazí na obou stránkách:

   ![Ukázková aplikace pro WebAssembly Blazor, která je otevřená ve dvou oknech prohlížeče, zobrazuje vyměňované zprávy.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   Quotes: *Star Trek VI:* &copy;nezjištěná země 1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country) – nezjištěná

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. Když VS Code nabídky vytvoří profil spuštění pro serverovou aplikaci (*. VSCode/Launch. JSON*), zobrazí se `program` položka podobně jako v následujícím příkladu, aby odkazovala na sestavení aplikace (`{APPLICATION NAME}.Server.dll`):

   ```json
   "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/{APPLICATION NAME}.Server.dll"
   ```

1. Stiskněte klávesu <kbd>F5</kbd> ke spuštění aplikace s laděním nebo <kbd>stisknutím klávesy CTRL</kbd>+<kbd>F5</kbd> pro spuštění aplikace bez ladění.

1. Zkopírujte adresu URL z panelu Adresa, otevřete jinou instanci nebo kartu prohlížeče a vložte adresu URL do panelu Adresa.

1. Zvolte buď prohlížeč, zadejte jméno a zprávu a klikněte na tlačítko **Odeslat** . Název a zpráva se okamžitě zobrazí na obou stránkách:

   ![Ukázková aplikace pro WebAssembly Blazor, která je otevřená ve dvou oknech prohlížeče, zobrazuje vyměňované zprávy.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   Quotes: *Star Trek VI:* &copy;nezjištěná země 1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country) – nezjištěná

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

1. Na bočním panelu **řešení** vyberte projekt **BlazorSignalRApp. Server** . Stiskněte <kbd>⌘</kbd>+<kbd>↩</kbd>* * ke spuštění aplikace s laděním nebo <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd> ke spuštění aplikace bez ladění.

1. Zkopírujte adresu URL z panelu Adresa, otevřete jinou instanci nebo kartu prohlížeče a vložte adresu URL do panelu Adresa.

1. Zvolte buď prohlížeč, zadejte jméno a zprávu a klikněte na tlačítko **Odeslat** . Název a zpráva se okamžitě zobrazí na obou stránkách:

   ![Ukázková aplikace pro WebAssembly Blazor, která je otevřená ve dvou oknech prohlížeče, zobrazuje vyměňované zprávy.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   Quotes: *Star Trek VI:* &copy;nezjištěná země 1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country) – nezjištěná

# <a name="net-core-cli"></a>[Rozhraní příkazového řádku .NET Core](#tab/netcore-cli/)

1. V příkazovém prostředí spusťte následující příkazy:

   ```dotnetcli
   cd Server
   dotnet run
   ```

1. Zkopírujte adresu URL z panelu Adresa, otevřete jinou instanci nebo kartu prohlížeče a vložte adresu URL do panelu Adresa.

1. Zvolte buď prohlížeč, zadejte jméno a zprávu a klikněte na tlačítko **Odeslat** . Název a zpráva se okamžitě zobrazí na obou stránkách:

   ![Ukázková aplikace pro WebAssembly Blazor, která je otevřená ve dvou oknech prohlížeče, zobrazuje vyměňované zprávy.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   Quotes: *Star Trek VI:* &copy;nezjištěná země 1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country) – nezjištěná

---

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Blazor Vytvoření projektu hostované aplikace WebAssembly
> * Přidat SignalR klientskou knihovnu
> * Přidání SignalR centra
> * Přidání SignalR služeb a koncového bodu pro SignalR centrum
> * Přidat Razor kód komponenty pro chat

Další informace o vytváření Blazor aplikací najdete v Blazor dokumentaci:

> [!div class="nextstepaction"]
> <xref:blazor/index>

## <a name="additional-resources"></a>Další zdroje

* <xref:signalr/introduction>
