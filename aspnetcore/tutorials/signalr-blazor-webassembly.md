---
title: Použití ASP.NET Core SignalR s Blazorm WebAssembly
author: guardrex
description: Vytvořte aplikaci Chat, která používá ASP.NET Core SignalR s Blazorm WebAssembly.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/26/2020
no-loc:
- Blazor
- SignalR
uid: tutorials/signalr-blazor-webassembly
ms.openlocfilehash: c4843dc282e1978b39738e206ecc79ded87fcff9
ms.sourcegitcommit: 6ffb583991d6689326605a24565130083a28ef85
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "80306573"
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
dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-preview3.20168.3
```

Postupujte podle pokynů pro výběr nástrojů:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Vytvoření nového projektu

1. Vyberte **aplikace Blazor** a vyberte **Další**.

1. Do pole **název projektu** zadejte "BlazorSignalRApp". Potvrďte správnost záznamu **umístění** nebo zadejte umístění projektu. Vyberte **Create** (Vytvořit).

1. Vyberte šablonu **aplikace WebAssembly pro Blazor** .

1. V části **Upřesnit**zaškrtněte políčko **ASP.NET Core hostované** .

1. Vyberte **Create** (Vytvořit).

> [!NOTE]
> Pokud jste provedli upgrade nebo instalaci nové verze sady Visual Studio a šablona WebAssembly Blazor se nezobrazí v uživatelském rozhraní VS, přeinstalujte šablonu pomocí příkazu `dotnet new` uvedeného výše.

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

1. V Visual Studio pro Mac otevřete projekt tak, že přejdete do složky projektu a otevřete soubor řešení projektu ( *. sln*).

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

1. Ve výsledcích hledání vyberte balíček `Microsoft.AspNetCore.SignalR.Client` a vyberte **nainstalovat**.

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

1. Ve výsledcích hledání zaškrtněte políčko vedle balíčku `Microsoft.AspNetCore.SignalR.Client` a vyberte **Přidat balíček**.

1. Pokud se zobrazí dialogové okno pro **přijetí licence** , vyberte **přijmout** , pokud souhlasíte s licenčními podmínkami.

# <a name="net-core-cli"></a>[Rozhraní příkazového řádku .NET Core](#tab/netcore-cli/)

V příkazovém prostředí spusťte následující příkazy:

```dotnetcli
cd BlazorSignalRApp
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

---

## <a name="add-a-signalr-hub"></a>Přidat centrum signálů

V projektu **BlazorSignalRApp. Server** vytvořte složku *Centers* (plural) a přidejte následující třídu `ChatHub` (*centra/ChatHub. cs*):

[!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Hubs/ChatHub.cs)]

## <a name="add-signalr-services-and-an-endpoint-for-the-signalr-hub"></a>Přidat služby signalizace a koncový bod pro centrum signalizace

1. V projektu **BlazorSignalRApp. Server** otevřete soubor *Startup.cs* .

1. Přidejte do horní části souboru obor názvů pro třídu `ChatHub`:

   ```csharp
   using BlazorSignalRApp.Server.Hubs;
   ```

1. Přidejte služby signalizace do `Startup.ConfigureServices`:

   ```csharp
   services.AddSignalR();
   ```

1. Do `Startup.Configure` mezi koncovými body pro trasu výchozího kontroleru a záložním serverem na straně klienta přidejte koncový bod pro centrum:

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet&highlight=4)]

## <a name="add-razor-component-code-for-chat"></a>Přidat kód komponenty Razor pro chat

1. V projektu **BlazorSignalRApp. Client** otevřete soubor *pages/index. Razor* .

1. Nahraďte kód následujícím kódem:

[!code-razor[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Client/Pages/Index.razor)]

## <a name="run-the-app"></a>Spuštění aplikace

1. Postupujte podle pokynů pro vaše nástroje:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. V **Průzkumník řešení**vyberte projekt **BlazorSignalRApp. Server** . Stisknutím **kombinace kláves CTRL + F5** aplikaci spusťte bez ladění.

1. Zkopírujte adresu URL z panelu Adresa, otevřete jinou instanci nebo kartu prohlížeče a vložte adresu URL do panelu Adresa.

1. Zvolte buď prohlížeč, zadejte jméno a zprávu a klikněte na tlačítko **Odeslat** . Název a zpráva se okamžitě zobrazí na obou stránkách:

   ![Ukázková aplikace pro WebAssembly Blazor, která je otevřená ve dvou oknech prohlížeče, zobrazuje vyměňované zprávy.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   Quotes: *Star Trek VI:* nezjištěná země &copy;1991 – [prvořadý](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. Vyberte možnost **ladění** > **Spustit bez ladění** z panelu nástrojů.

1. Zkopírujte adresu URL z panelu Adresa, otevřete jinou instanci nebo kartu prohlížeče a vložte adresu URL do panelu Adresa.

1. Zvolte buď prohlížeč, zadejte jméno a zprávu a klikněte na tlačítko **Odeslat** . Název a zpráva se okamžitě zobrazí na obou stránkách:

   ![Ukázková aplikace pro WebAssembly Blazor, která je otevřená ve dvou oknech prohlížeče, zobrazuje vyměňované zprávy.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   Quotes: *Star Trek VI:* nezjištěná země &copy;1991 – [prvořadý](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

1. Na bočním panelu **řešení** vyberte projekt **BlazorSignalRApp. Server** . V nabídce vyberte **spustit** > **Spustit bez ladění**.

1. Zkopírujte adresu URL z panelu Adresa, otevřete jinou instanci nebo kartu prohlížeče a vložte adresu URL do panelu Adresa.

1. Zvolte buď prohlížeč, zadejte jméno a zprávu a klikněte na tlačítko **Odeslat** . Název a zpráva se okamžitě zobrazí na obou stránkách:

   ![Ukázková aplikace pro WebAssembly Blazor, která je otevřená ve dvou oknech prohlížeče, zobrazuje vyměňované zprávy.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   Quotes: *Star Trek VI:* nezjištěná země &copy;1991 – [prvořadý](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="net-core-cli"></a>[Rozhraní příkazového řádku .NET Core](#tab/netcore-cli/)

1. V příkazovém prostředí spusťte následující příkazy:

   ```dotnetcli
   cd Server
   dotnet run
   ```

1. Zkopírujte adresu URL z panelu Adresa, otevřete jinou instanci nebo kartu prohlížeče a vložte adresu URL do panelu Adresa.

1. Zvolte buď prohlížeč, zadejte jméno a zprávu a klikněte na tlačítko **Odeslat** . Název a zpráva se okamžitě zobrazí na obou stránkách:

   ![Ukázková aplikace pro WebAssembly Blazor, která je otevřená ve dvou oknech prohlížeče, zobrazuje vyměňované zprávy.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   Quotes: *Star Trek VI:* nezjištěná země &copy;1991 – [prvořadý](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

---

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Vytvoření projektu hostované aplikace Blazor WebAssembly
> * Přidání klientské knihovny SignalR
> * Přidání centra SignalR
> * Přidání služeb SignalR a koncového bodu pro centrum SignalR
> * Přidat kód komponenty Razor pro chat

Další informace o vytváření Blazorch aplikací najdete v dokumentaci k Blazor:

> [!div class="nextstepaction"]
> <xref:blazor/index>

## <a name="additional-resources"></a>Další zdroje informací:

* <xref:signalr/introduction>
