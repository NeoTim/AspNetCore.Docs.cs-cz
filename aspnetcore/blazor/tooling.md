---
title: Nástroje pro ASP.NET CoreBlazor
author: guardrex
description: Přečtěte si informace o nástrojích dostupných pro sestavování Blazor aplikací.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/02/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/tooling
ms.openlocfilehash: 3ff610225c798624b7ead7d365924ae3d193829d
ms.sourcegitcommit: 66fca14611eba141d455fe0bd2c37803062e439c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2020
ms.locfileid: "85944950"
---
<!-- zone_pivot_groups: operating-systems -->

# <a name="tooling-for-aspnet-core-blazor"></a>Nástroje pro ASP.NET CoreBlazor

Od [Daniel Skořepa](https://github.com/danroth27) a [Luke Latham](https://github.com/guardrex)

Vyberte nástroje pro vaši platformu:

* Windows: vyberte kartu sady **Visual Studio** .
* Linux: vyberte kartu **Visual Studio Code** .
* macOS: vyberte kartu **Visual Studio pro Mac** .

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Nainstalujte nejnovější verzi sady [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) s úlohou **vývoje ASP.NET a webu** .

1. Vytvoření nového projektu

1. Vyberte možnost ** Blazor aplikace**. Vyberte **Další**.

1. Do pole **název projektu** zadejte název projektu nebo přijměte výchozí název projektu. Potvrďte správnost záznamu **umístění** nebo zadejte umístění projektu. Vyberte **Vytvořit**.

1. Pro Blazor WebAssembly prostředí vyberte šablonu ** Blazor WebAssembly aplikace** . Pro Blazor Server prostředí vyberte šablonu ** Blazor Server aplikace** . Vyberte **Vytvořit**.

   Informace o těchto dvou Blazor modelech hostování *Blazor WebAssembly* a naleznete *Blazor Server* v tématu <xref:blazor/hosting-models> .

1. Spusťte aplikaci stisknutím klávesy <kbd>CTRL</kbd> + <kbd>F5</kbd> .

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. Nainstalujte nejnovější verzi sady [.NET Core 3,1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1). Pokud jste dříve nainstalovali sadu SDK, můžete zjistit nainstalovanou verzi spuštěním následujícího příkazu v příkazovém prostředí:

   ```dotnetcli
   dotnet --version
   ```

1. Nainstalujte nejnovější verzi [Visual Studio Code](https://code.visualstudio.com/).

1. Nainstalujte nejnovější [rozšíření C# for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) a [ladicí program JavaScript (v noci)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) s `debug.javascript.usePreview` nastavením na `true` .

   Pokud chcete `debug.javascript.usePreview` nastavit `true` použití uživatelského rozhraní vs Code, otevřete **File**  >  **Preferences**  >  **Nastavení** předvoleb souborů a vyhledejte `debug javascript use preview` . Zaškrtněte políčko pro **použití novinového ladicího programu JavaScriptu v Preview pro Node.js a Chrome**.

1. Pro Blazor WebAssembly prostředí spusťte následující příkaz v příkazovém prostředí:

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

   Pro Blazor Server prostředí spusťte následující příkaz v příkazovém prostředí:

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   Informace o těchto dvou Blazor modelech hostování *Blazor WebAssembly* a naleznete *Blazor Server* v tématu <xref:blazor/hosting-models> .

1. Otevřete `WebApplication1` složku v Visual Studio Code.

1. Rozhraní IDE požaduje, abyste přidali prostředky pro sestavení a ladění projektu. Vyberte **Ano**.

1. Spusťte aplikaci stisknutím klávesy <kbd>CTRL</kbd> + <kbd>F5</kbd> .

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

1. Nainstalujte [Visual Studio pro Mac](https://visualstudio.microsoft.com/vs/mac/).

1. Vyberte **soubor**  >  **nové řešení** nebo vytvořte **Nový** projekt z **okna Start**.

1. Na bočním panelu vyberte **Webová a konzolová**  >  **aplikace**.

   Pro Blazor WebAssembly prostředí vyberte šablonu ** Blazor WebAssembly aplikace** . Pro Blazor Server prostředí vyberte šablonu ** Blazor Server aplikace** . Vyberte **Další**.

   Informace o těchto dvou Blazor modelech hostování *Blazor WebAssembly* a naleznete *Blazor Server* v tématu <xref:blazor/hosting-models> .

1. Potvrďte následující konfigurace:

   * **Cílová architektura** je nastavená na **.NET Core 3,1**.
   * **Ověřování** nastaveno na **bez ověřování**.
   
   Vyberte **Další**.

1. Do pole **název projektu** název aplikace `WebApplication1` . Vyberte **Vytvořit**.

1. **Run**  >  Chcete-li spustit aplikaci *bez ladicího programu*, vyberte možnost spustit**bez ladění** . Spusťte aplikaci pomocí **příkazového**  >  **počátečního ladění** nebo tlačítka Spustit (&#9654;), aby se aplikace spustila *s ladicím programem*.

Pokud se zobrazí výzva k důvěřování vývojovým certifikátům, důvěřujete certifikátu a pokračujte. Certifikát uživatele a řetězce klíčů je nutný k důvěřování certifikátu.

---

<!--

::: zone pivot="os-windows"

1. Install the latest version of [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) with the **ASP.NET and web development** workload.

1. Create a new project.

1. Select **Blazor App**. Select **Next**.

1. Provide a project name in the **Project name** field or accept the default project name. Confirm the **Location** entry is correct or provide a location for the project. Select **Create**.

1. For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template. For a Blazor Server experience, choose the **Blazor Server App** template. Select **Create**.

   For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.

1. Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.

::: zone-end

::: zone pivot="os-linux"

1. Install the latest version of the [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1). If you previously installed the SDK, you can determine your installed version by executing the following command in a command shell:

   ```dotnetcli
   dotnet --version
   ```

1. Install the latest version of [Visual Studio Code](https://code.visualstudio.com/).

1. Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) and the [JavaScript Debugger (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) extension with `debug.javascript.usePreview` set to `true`.

   To set `debug.javascript.usePreview` to `true` using the VS Code UI, open **File** > **Preferences** > **Settings** and search for `debug javascript use preview`. Select the check box for **Use the new in-preview JavaScript debugger for Node.js and Chrome**.

1. For a Blazor WebAssembly experience, execute the following command in a command shell:

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

   For a Blazor Server experience, execute the following command in a command shell:

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.

1. Open the `WebApplication1` folder in Visual Studio Code.

1. The IDE requests that you add assets to build and debug the project. Select **Yes**.

1. Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.

::: zone-end

::: zone pivot="os-macos"

1. Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).

1. Select **File** > **New Solution** or create a **New** project from the **Start Window**.

1. In the sidebar, select **Web and Console** > **App**.

   For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template. For a Blazor Server experience, choose the **Blazor Server App** template. Select **Next**.

   For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.

1. Confirm the following configurations:

   * **Target Framework** set to **.NET Core 3.1**.
   * **Authentication** set to **No Authentication**.
   
   Select **Next**.

1. In the **Project Name** field, name the app `WebApplication1`. Select **Create**.

1. Select **Run** > **Start Without Debugging** to run the app *without the debugger*. Run the app with **Run** > **Start Debugging** or the Run (&#9654;) button to run the app *with the debugger*.

If a prompt appears to trust the development certificate, trust the certificate and continue. The user and keychain passwords are required to trust the certificate.

::: zone-end

-->
