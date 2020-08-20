---
title: Použít ASP.NET Core SignalR s Blazor WebAssembly
author: guardrex
description: Vytvořte aplikaci Chat, která používá ASP.NET Core SignalR s Blazor WebAssembly .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/10/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/signalr-blazor-webassembly
ms.openlocfilehash: 4d33e99ceb8273487144447eae324469df67c9ff
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2020
ms.locfileid: "88633380"
---
# <a name="use-aspnet-core-no-locsignalr-with-no-locblazor-webassembly"></a>Použít ASP.NET Core SignalR s Blazor WebAssembly

Od [Daniel Skořepa](https://github.com/danroth27) a [Luke Latham](https://github.com/guardrex)

V tomto kurzu se naučíte základy vytváření aplikací v reálném čase pomocí nástroje SignalR Blazor WebAssembly . Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvoření Blazor WebAssembly projektu hostované aplikace
> * Přidat SignalR klientskou knihovnu
> * Přidání SignalR centra
> * Přidání SignalR služeb a koncového bodu pro SignalR centrum
> * Přidat Razor kód komponenty pro chat

Na konci tohoto kurzu budete mít funkční chatovací aplikaci.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Předpoklady

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* [Visual Studio 2019 16,6 nebo novější](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) s úlohou **vývoje ASP.NET a webu**
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

* [Visual Studio pro Mac verze 8,6 nebo novější](https://visualstudio.microsoft.com/vs/mac/)
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli/)

[!INCLUDE[](~/includes/3.1-SDK.md)]

---

## <a name="create-a-hosted-no-locblazor-webassembly-app-project"></a>Vytvoření projektu hostované Blazor WebAssembly aplikace

Postupujte podle pokynů pro výběr nástrojů:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

> [!NOTE]
> Vyžaduje se Visual Studio 16,6 nebo novější a .NET Core SDK 3.1.300 nebo novější.

1. Vytvoření nového projektu

1. Vyberte ** Blazor aplikace** a vyberte **Další**.

1. `BlazorSignalRApp`Do pole **název projektu** zadejte. Potvrďte správnost záznamu **umístění** nebo zadejte umístění projektu. Vyberte **Vytvořit**.

1. Vyberte šablonu ** Blazor WebAssembly aplikace** .

1. V části **Upřesnit**zaškrtněte políčko **ASP.NET Core hostované** .

1. Vyberte **Vytvořit**.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. V příkazovém prostředí spusťte následující příkaz:

   ```dotnetcli
   dotnet new blazorwasm --hosted --output BlazorSignalRApp
   ```

1. V Visual Studio Code otevřete složku projektu aplikace.

1. Když se zobrazí dialogové okno s přidáním assetů pro sestavení a ladění aplikace, vyberte **Ano**. Visual Studio Code automaticky přidá `.vscode` složku s generovanými `launch.json` `tasks.json` soubory a.

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

1. Nainstalujte nejnovější verzi [Visual Studio pro Mac](https://visualstudio.microsoft.com/vs/mac/) a proveďte následující kroky:

1. Vyberte **soubor**  >  **nové řešení** nebo vytvořte **Nový** projekt z **okna Start**.

1. Na bočním panelu vyberte **Webová a konzolová**  >  **aplikace**.

1. Vyberte šablonu ** Blazor WebAssembly aplikace** . Vyberte **Další**.

1. Potvrďte, že **ověřování** je nastaveno na **bez ověřování**. Zaškrtněte políčko **ASP.NET Core hostované** . Vyberte **Další**.

1. Do pole **název projektu** název aplikace `BlazorSignalRApp` . Vyberte **Vytvořit**.

   Pokud se zobrazí výzva k důvěřování vývojovým certifikátům, důvěřujete certifikátu a pokračujte. Certifikát uživatele a řetězce klíčů je nutný k důvěřování certifikátu.

1. Otevřete projekt tak, že přejdete do složky projektu a otevřete soubor řešení projektu ( `.sln` ).

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli/)

V příkazovém prostředí spusťte následující příkaz:

```dotnetcli
dotnet new blazorwasm --hosted --output BlazorSignalRApp
```

---

## <a name="add-the-no-locsignalr-client-library"></a>Přidat SignalR klientskou knihovnu

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio/)

1. V **Průzkumník řešení**klikněte pravým tlačítkem na `BlazorSignalRApp.Client` projekt a vyberte **Spravovat balíčky NuGet**.

1. V dialogovém okně **Spravovat balíčky NuGet** potvrďte, že je **zdroj balíčku** nastavený na `nuget.org` .

1. Když je vybraná možnost **Procházet** , `Microsoft.AspNetCore.SignalR.Client` do vyhledávacího pole zadejte.

1. Ve výsledcích hledání vyberte [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) balíček a vyberte **nainstalovat**.

1. Pokud se zobrazí dialogové okno **Náhled změn** , vyberte **OK**.

1. Pokud se zobrazí dialogové okno pro **přijetí licence** , **Vyberte možnost Souhlasím** , pokud souhlasíte s licenčními podmínkami.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code/)

V **integrovaném terminálu** (**zobrazení**  >  **terminálu** z panelu nástrojů) spusťte následující příkazy:

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

1. Na bočním panelu **řešení** klikněte pravým tlačítkem na `BlazorSignalRApp.Client` projekt a vyberte **Spravovat balíčky NuGet**.

1. V dialogovém okně **Spravovat balíčky NuGet** potvrďte, že rozevírací seznam zdroj je nastavený na `nuget.org` .

1. Když je vybraná možnost **Procházet** , `Microsoft.AspNetCore.SignalR.Client` do vyhledávacího pole zadejte.

1. Ve výsledcích hledání zaškrtněte políčko vedle [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) balíčku a vyberte **Přidat balíček**.

1. Pokud se zobrazí dialogové okno pro **přijetí licence** , vyberte **přijmout** , pokud souhlasíte s licenčními podmínkami.

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli/)

V příkazovém prostředí spusťte následující příkazy:

```dotnetcli
cd BlazorSignalRApp
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

---

## <a name="add-a-no-locsignalr-hub"></a>Přidání SignalR centra

V `BlazorSignalRApp.Server` projektu vytvořte `Hubs` složku (plural) a přidejte následující `ChatHub` třídu ( `Hubs/ChatHub.cs` ):

[!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Hubs/ChatHub.cs)]

## <a name="add-services-and-an-endpoint-for-the-no-locsignalr-hub"></a>Přidání služeb a koncového bodu pro SignalR centrum

1. V `BlazorSignalRApp.Server` projektu otevřete `Startup.cs` soubor.

1. Přidejte do `ChatHub` horní části souboru obor názvů pro třídu:

   ```csharp
   using BlazorSignalRApp.Server.Hubs;
   ```

1. Přidejte SignalR služby middlewaru pro komprimaci a odezvu do `Startup.ConfigureServices` :

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,5-9)]

1. V `Startup.Configure`:

   * V horní části Konfigurace kanálu zpracování použijte middleware pro kompresi odpovědí.
   * Mezi koncovými body řadičů a Fallback na straně klienta přidejte koncový bod pro centrum.

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_Configure&highlight=3,25)]

## <a name="add-no-locrazor-component-code-for-chat"></a>Přidat Razor kód komponenty pro chat

1. V `BlazorSignalRApp.Client` projektu otevřete `Pages/Index.razor` soubor.

1. Nahraďte kód následujícím kódem:

[!code-razor[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Client/Pages/Index.razor)]

## <a name="run-the-app"></a>Spuštění aplikace

1. Postupujte podle pokynů pro vaše nástroje:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. V **Průzkumník řešení**vyberte `BlazorSignalRApp.Server` projekt. Stiskněte klávesu <kbd>F5</kbd> ke spuštění aplikace s laděním nebo <kbd>stisknutím klávesy CTRL</kbd> + <kbd>F5</kbd> pro spuštění aplikace bez ladění.

1. Zkopírujte adresu URL z panelu Adresa, otevřete jinou instanci nebo kartu prohlížeče a vložte adresu URL do panelu Adresa.

1. Zvolte buď prohlížeč, zadejte jméno a zprávu a vyberte tlačítko pro odeslání zprávy. Název a zpráva se okamžitě zobrazí na obou stránkách:

   ![::: No-Loc (Signal):::::: No-Loc (Blazor WebAssembly)::: Ukázková aplikace je otevřená ve dvou oknech prohlížeče, které zobrazují vyměňované zprávy.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   Quotes: *Star Trek VI:* nezjištěná země 1991 – nezjištěná &copy; [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. Když VS Code nabídky pro vytvoření profilu spuštění pro serverovou aplikaci ( `.vscode/launch.json` ), `program` zobrazí se položka podobná následujícímu, aby odkazovala na sestavení aplikace ( `{APPLICATION NAME}.Server.dll` ):

   ```json
   "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/{APPLICATION NAME}.Server.dll"
   ```

1. Stiskněte klávesu <kbd>F5</kbd> ke spuštění aplikace s laděním nebo <kbd>stisknutím klávesy CTRL</kbd> + <kbd>F5</kbd> pro spuštění aplikace bez ladění.

1. Zkopírujte adresu URL z panelu Adresa, otevřete jinou instanci nebo kartu prohlížeče a vložte adresu URL do panelu Adresa.

1. Zvolte buď prohlížeč, zadejte jméno a zprávu a vyberte tlačítko pro odeslání zprávy. Název a zpráva se okamžitě zobrazí na obou stránkách:

   ![::: No-Loc (Signal):::::: No-Loc (Blazor WebAssembly)::: Ukázková aplikace je otevřená ve dvou oknech prohlížeče, které zobrazují vyměňované zprávy.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   Quotes: *Star Trek VI:* nezjištěná země 1991 – nezjištěná &copy; [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

1. Na bočním panelu **řešení** vyberte `BlazorSignalRApp.Server` projekt. Stiskněte <kbd>⌘</kbd> + <kbd>↩</kbd> ke spuštění aplikace s laděním nebo <kbd>⌥</kbd> + <kbd>⌘</kbd> + <kbd>↩</kbd> ke spuštění aplikace bez ladění.

1. Zkopírujte adresu URL z panelu Adresa, otevřete jinou instanci nebo kartu prohlížeče a vložte adresu URL do panelu Adresa.

1. Zvolte buď prohlížeč, zadejte jméno a zprávu a vyberte tlačítko pro odeslání zprávy. Název a zpráva se okamžitě zobrazí na obou stránkách:

   ![::: No-Loc (Signal):::::: No-Loc (Blazor WebAssembly)::: Ukázková aplikace je otevřená ve dvou oknech prohlížeče, které zobrazují vyměňované zprávy.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   Quotes: *Star Trek VI:* nezjištěná země 1991 – nezjištěná &copy; [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli/)

1. V příkazovém prostředí spusťte následující příkazy:

   ```dotnetcli
   cd Server
   dotnet run
   ```

1. Zkopírujte adresu URL z panelu Adresa, otevřete jinou instanci nebo kartu prohlížeče a vložte adresu URL do panelu Adresa.

1. Zvolte buď prohlížeč, zadejte jméno a zprávu a vyberte tlačítko pro odeslání zprávy. Název a zpráva se okamžitě zobrazí na obou stránkách:

   ![::: No-Loc (Signal):::::: No-Loc (Blazor WebAssembly)::: Ukázková aplikace je otevřená ve dvou oknech prohlížeče, které zobrazují vyměňované zprávy.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   Quotes: *Star Trek VI:* nezjištěná země 1991 – nezjištěná &copy; [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

---

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Vytvoření Blazor WebAssembly projektu hostované aplikace
> * Přidat SignalR klientskou knihovnu
> * Přidání SignalR centra
> * Přidání SignalR služeb a koncového bodu pro SignalR centrum
> * Přidat Razor kód komponenty pro chat

Další informace o vytváření Blazor aplikací najdete v Blazor dokumentaci:

> [!div class="nextstepaction"]
> <xref:blazor/index>

## <a name="additional-resources"></a>Další zdroje informací

* <xref:signalr/introduction>
* [SignalR vyjednávání mezi zdroji pro ověřování](xref:blazor/fundamentals/additional-scenarios#signalr-cross-origin-negotiation-for-authentication)
