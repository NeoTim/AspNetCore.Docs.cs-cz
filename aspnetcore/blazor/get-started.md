---
title: Začínáme s ASP.NET CoreBlazor
author: guardrex
description: Začněte Blazor tím, že sestavíte Blazor aplikaci s vámi zvoleným nástrojem.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/16/2020
no-loc:
- Blazor
- SignalR
uid: blazor/get-started
ms.openlocfilehash: 2f10b00adce31c020d46d107c087159c17341beb
ms.sourcegitcommit: 7bb14d005155a5044c7902a08694ee8ccb20c113
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/24/2020
ms.locfileid: "82111068"
---
# <a name="get-started-with-aspnet-core-blazor"></a>Začínáme s ASP.NET Core Blazor

Od [Daniel Skořepa](https://github.com/danroth27) a [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Pokud chcete začít používat Blazor, postupujte podle pokynů pro výběr nástrojů:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Chcete-li vytvořit aplikace serveru Blazor, nainstalujte nejnovější verzi sady [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) s úlohou **vývoje ASP.NET a webu** .

   Chcete-li vytvořit Blazor Server a Blazor aplikace WebAssembly, nainstalujte nejnovější verzi Preview sady [Visual Studio 2019](https://visualstudio.microsoft.com/vs/preview/) s úlohou **vývoj pro ASP.NET a web** .

   Informace o dvou modelech hostování Blazor, *Blazor WebAssembly* a *Blazor serveru*naleznete v tématu <xref:blazor/hosting-models>.

1. Nainstalujte šablonu [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) Preview spuštěním následujícího příkazu:

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-preview5.20216.8
   ```

1. Vytvoření nového projektu

1. Vyberte **aplikaci Blazor**. Vyberte **Další**.

1. Do pole **název projektu** zadejte název projektu nebo přijměte výchozí název projektu. Potvrďte správnost záznamu **umístění** nebo zadejte umístění projektu. Vyberte **Vytvořit**.

1. Pro prostředí WebAssembly Blazor (Visual Studio 16,6 Preview 2 nebo novější) vyberte šablonu **aplikace Blazor WebAssembly** . Pro prostředí serveru Blazor (Visual Studio 16,4 nebo novější) vyberte šablonu **aplikace serveru Blazor** . Vyberte **Vytvořit**.

1. Spusťte aplikaci stisknutím klávesy <kbd>CTRL</kbd>+<kbd>F5</kbd> .

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. Nainstalujte [sadu .NET Core 3,1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).

1. Volitelně můžete nainstalovat šablonu [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) Preview spuštěním následujícího příkazu:

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-preview5.20216.8
   ```

   > [!NOTE]
   > [.NET Core SDK verze 3.1.201 nebo novější](https://dotnet.microsoft.com/download/dotnet-core/3.1) je **vyžadována** pro použití šablony sestavení Blazor verze 3,2 Preview 4. Ověřte nainstalovanou verzi .NET Core SDK spuštěním `dotnet --version` příkazu v příkazovém prostředí.

1. Nainstalujte [Visual Studio Code](https://code.visualstudio.com/).

1. Nainstalujte nejnovější [rozšíření C# for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) a [ladicí program JavaScript (v noci)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) s `debug.javascript.usePreview` nastavením na `true`.

1. Pro prostředí Blazor serveru spusťte v příkazovém prostředí následující příkaz:

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   Pro prostředí WebAssembly Blazor spusťte následující příkaz v příkazovém prostředí:

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

   Informace o dvou modelech hostování Blazor, *serveru Blazor* a *Blazor WebAssembly*naleznete v tématu <xref:blazor/hosting-models>.

1. Otevřete složku *WebApplication1* v Visual Studio Code.

1. Rozhraní IDE požaduje, abyste přidali prostředky pro sestavení a ladění projektu. Vyberte **Ano**.

1. Se serverem Blazor spusťte aplikaci pomocí ladicího programu Visual Studio Code.

   V případě Blazor WebAssembly spusťte aplikaci pomocí **spouštěcího rozhraní .NET Core (Blazor Standalone)** a pak spusťte prohlížeč pomocí **webového sestavení .NET Core Blazor web Assembly v konfiguraci spuštění aplikace Chrome** (vyžaduje Chrome). Další informace naleznete v tématu <xref:blazor/debug#visual-studio-code>.

1. V prohlížeči přejděte na `https://localhost:5001`.

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

Blazor Server je podporován v Visual Studio pro Mac. WebAssembly Blazor se v tuto chvíli nepodporuje. Pokud chcete v macOS sestavovat Blazor aplikace WebAssembly, postupujte podle pokynů na kartě **.NET Core CLI** .

1. Nainstalujte [Visual Studio pro Mac](https://visualstudio.microsoft.com/vs/mac/).

1. Vyberte **soubor** > **nové řešení** nebo vytvořte **Nový projekt**.

1. Na bočním panelu vyberte**aplikace** **.NET Core** > .

1. Vyberte šablonu **aplikace Blazor serveru** . Vyberte **Vytvořit**.

   Informace o modelu hostování serveru Blazor naleznete v tématu <xref:blazor/hosting-models>.

1. Nastavte **cílovou architekturu** na **.NET Core 3,1** a vyberte **Další**.

1. Do pole **název projektu** název aplikace `WebApplication1`. Vyberte **Vytvořit**.

1. Chcete-li spustit aplikaci *bez ladicího programu*, vyberte možnost **Spustit** > **běh bez ladění** . Spusťte aplikaci s použitím **Spustit ladění** a spusťte aplikaci *pomocí ladicího programu*.

Pokud se zobrazí výzva k důvěřování vývojovým certifikátům, důvěřujete certifikátu a pokračujte.

# <a name="net-core-cli"></a>[Rozhraní příkazového řádku .NET Core](#tab/netcore-cli/)

1. Nainstalujte [sadu .NET Core 3,1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).

1. Volitelně můžete nainstalovat šablonu [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) Preview spuštěním následujícího příkazu:

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-preview5.20216.8
   ```

   > [!NOTE]
   > [.NET Core SDK verze 3.1.201 nebo novější](https://dotnet.microsoft.com/download/dotnet-core/3.1) je **vyžadována** pro použití šablony sestavení Blazor verze 3,2 Preview 4. Ověřte nainstalovanou verzi .NET Core SDK spuštěním `dotnet --version` příkazu v příkazovém prostředí.

1. Pro prostředí Blazor serveru spusťte v příkazovém prostředí následující příkazy:

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   Pro prostředí WebAssembly Blazor spusťte následující příkazy v příkazovém prostředí:

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   Informace o dvou modelech hostování Blazor, *serveru Blazor* a *Blazor WebAssembly*naleznete v tématu <xref:blazor/hosting-models>.

1. V prohlížeči přejděte na `https://localhost:5001`.

---

Na kartách na bočním panelu je k dispozici více stránek:

* Domů
* Čítač
* Načíst data

Na stránce čítač můžete **kliknutím** na tlačítko pro zvýšit hodnotu čítače bez aktualizace stránky. Zvýšení čítače na webové stránce obvykle vyžaduje psaní JavaScriptu, ale s Blazor můžete použít jazyk C#.

*Stránky/čítač. Razor*:

[!code-razor[](get-started/samples_snapshot/3.x/Counter1.razor?highlight=7,12-15)]

Požadavek na `/counter` v prohlížeči, jak je uveden v `@page` direktivě nahoře, způsobí, že `Counter` komponenta vykreslí svůj obsah. Komponenty vykreslí do reprezentace v paměti stromu vykreslování, který lze poté použít k flexibilnímu a efektivnímu způsobu aktualizace uživatelského rozhraní.

Pokaždé, když je vybráno tlačítko pro **kliknutí na tlačítko Další** :

* `onclick` Událost je aktivována.
* `IncrementCount` Metoda je volána.
* Dojde `currentCount` k zvýšení.
* Komponenta se znovu vykreslí.

Modul runtime porovná nový obsah s předchozím obsahem a na model DOM (Document Object Model) (DOM) použije pouze změněný obsah.

Přidejte komponentu do jiné komponenty pomocí syntaxe jazyka HTML. Například přidejte `Counter` komponentu do domovské stránky aplikace přidáním `<Counter />` elementu do `Index` komponenty.

*Pages/index. Razor*:

[!code-razor[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

Spusťte aplikaci. Domovská stránka má vlastní počítadlo poskytované `Counter` komponentou.

Parametry komponenty jsou zadány pomocí atributů nebo [podřízeného obsahu](xref:blazor/components#child-content), který umožňuje nastavit vlastnosti pro podřízenou komponentu. Chcete-li přidat parametr do `Counter` komponenty, aktualizujte `@code` blok komponenty:

* Přidejte veřejnou vlastnost pro `IncrementAmount` s `[Parameter]` atributem.
* Změňte `IncrementCount` metodu na použití `IncrementAmount` při zvyšování hodnoty. `currentCount`

*Stránky/čítač. Razor*:

[!code-razor[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=12-13,17)]

Zadejte `IncrementAmount` v `Index` `<Counter>` elementu komponenty pomocí atributu.

*Pages/index. Razor*:

[!code-razor[](get-started/samples_snapshot/3.x/Index2.razor?highlight=7)]

Spusťte aplikaci. `Index` Komponenta má vlastní čítač, který se zvýší o deset pokaždé, když je vybráno tlačítko pro **kliknutí na tlačítko Další** . Komponenta `Counter` (*Counter. Razor*) se `/counter` stále zvyšuje o jednu.

## <a name="next-steps"></a>Další kroky

<xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a>Další materiály a zdroje informací

* <xref:blazor/templates>
* <xref:signalr/introduction>
