---
title: Začínáme s ASP.NET Core Blazor
author: guardrex
description: Začněte s Blazor vytvořením Blazor aplikace pomocí nástrojů podle vašeho výběru.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/26/2020
no-loc:
- Blazor
- SignalR
uid: blazor/get-started
ms.openlocfilehash: 9ebeb57d2fad7e4c288d61a46911f2bf64cac2fb
ms.sourcegitcommit: f3b1bcfd108e5d53f73abc0bf2555890869d953b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "80320944"
---
# <a name="get-started-with-aspnet-core-blazor"></a>Začínáme s ASP.NET Core Blazor

Od [Daniel Skořepa](https://github.com/danroth27) a [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Pokud chcete začít používat Blazor, postupujte podle pokynů pro výběr nástrojů:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Pokud chcete vytvářet aplikace serveru Blazor, nainstalujte [Visual Studio 2019 verze 16,4 nebo novější](https://visualstudio.microsoft.com/vs/preview/) s úlohou **vývoje ASP.NET a webu** .

   Chcete-li vytvořit Blazor Server a Blazor aplikace WebAssembly, nainstalujte Visual Studio 2019 16,6 Preview 2 nebo novější s úlohou **vývoj pro ASP.NET a web** .

   Informace o dvou modelech hostování Blazor, *Blazor WebAssembly* a *Blazor serveru*naleznete v tématu <xref:blazor/hosting-models>.

1. Vytvoření nového projektu

1. Vyberte **aplikaci Blazor**. Vyberte **Další**.

1. Do pole **název projektu** zadejte název projektu nebo přijměte výchozí název projektu. Potvrďte správnost záznamu **umístění** nebo zadejte umístění projektu. Vyberte **Create** (Vytvořit).

1. Pro prostředí WebAssembly Blazor (Visual Studio 16,6 Preview 2 nebo novější) vyberte šablonu **aplikace Blazor WebAssembly** . Pro prostředí serveru Blazor (Visual Studio 16,4 nebo novější) vyberte šablonu **aplikace serveru Blazor** . Vyberte **Create** (Vytvořit).

1. Spusťte aplikaci stisknutím klávesy <kbd>Ctrl</kbd>+<kbd>F5</kbd> .

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. Nainstalujte [sadu .NET Core 3,1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).

1. Volitelně můžete nainstalovat šablonu [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) Preview spuštěním následujícího příkazu:

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-preview3.20168.3
   ```

   > [!NOTE]
   > [.NET Core SDK verze 3.1.201 nebo novější](https://dotnet.microsoft.com/download/dotnet-core/3.1) se **vyžaduje** pro použití šablony sestavení Blazor verze 3,2 Preview 3. Ověřte nainstalovanou verzi .NET Core SDK spuštěním `dotnet --version` v příkazovém prostředí.

1. Nainstalujte [Visual Studio Code](https://code.visualstudio.com/).

1. Nainstalujte nejnovější [ C# rozšíření pro Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) a rozšíření [JavaScript Debugger (v noci)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) s `debug.javascript.usePreview` nastavenou na `true`.

1. Pro prostředí Blazor serveru spusťte v příkazovém prostředí následující příkaz:

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   Pro prostředí WebAssembly Blazor spusťte následující příkaz v příkazovém prostředí:

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

   Informace o dvou modelech hostování Blazor, *serverech Blazor* a *Blazor WebAssembly*naleznete v tématu <xref:blazor/hosting-models>.

1. Otevřete složku *WebApplication1* v Visual Studio Code.

1. Rozhraní IDE požaduje, abyste přidali prostředky pro sestavení a ladění projektu. Vyberte **Ano**.

1. Spusťte aplikaci pomocí ladicího programu Visual Studio Code.

1. V prohlížeči přejděte na `https://localhost:5001`.

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

Blazor Server je podporován v Visual Studio pro Mac. WebAssembly Blazor se v tuto chvíli nepodporuje. Pokud chcete v macOS sestavovat Blazor aplikace WebAssembly, postupujte podle pokynů na kartě **.NET Core CLI** .

1. Nainstalujte [Visual Studio pro Mac](https://visualstudio.microsoft.com/vs/mac/).

1. Vyberte **soubor** > **nové řešení** nebo vytvořte **Nový projekt**.

1. Na bočním panelu vyberte **.NET Core** > **App**.

1. Vyberte šablonu **aplikace Blazor serveru** . Vyberte **Create** (Vytvořit).

   Informace o modelu hostování serveru Blazor naleznete v tématu <xref:blazor/hosting-models>.

1. Nastavte **cílovou architekturu** na **.NET Core 3,1** a vyberte **Další**.

1. Do pole **název projektu** název aplikace `WebApplication1`. Vyberte **Create** (Vytvořit).

1. Vyberte **spustit** > **Spustit bez ladění** pro spuštění aplikace *bez ladicího programu*. Spusťte aplikaci s použitím **Spustit ladění** a spusťte aplikaci *pomocí ladicího programu*.

Pokud se zobrazí výzva k důvěřování vývojovým certifikátům, důvěřujete certifikátu a pokračujte.

# <a name="net-core-cli"></a>[Rozhraní příkazového řádku .NET Core](#tab/netcore-cli/)

1. Nainstalujte [sadu .NET Core 3,1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).

1. Volitelně můžete nainstalovat šablonu [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) Preview spuštěním následujícího příkazu:

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-preview3.20168.3
   ```

   > [!NOTE]
   > [.NET Core SDK verze 3.1.201 nebo novější](https://dotnet.microsoft.com/download/dotnet-core/3.1) se **vyžaduje** pro použití šablony sestavení Blazor verze 3,2 Preview 3. Ověřte nainstalovanou verzi .NET Core SDK spuštěním `dotnet --version` v příkazovém prostředí.

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

   Informace o dvou modelech hostování Blazor, *serverech Blazor* a *Blazor WebAssembly*naleznete v tématu <xref:blazor/hosting-models>.

1. V prohlížeči přejděte na `https://localhost:5001`.

---

Na kartách na bočním panelu je k dispozici více stránek:

* Domů
* Čítač
* Načíst data

Na stránce čítač můžete **kliknutím** na tlačítko pro zvýšit hodnotu čítače bez aktualizace stránky. Zvýšení čítače na webové stránce obvykle vyžaduje psaní JavaScriptu, ale s Blazor můžete použít C#.

*Stránky/čítač. Razor*:

[!code-razor[](get-started/samples_snapshot/3.x/Counter1.razor?highlight=7,12-15)]

Žádost o `/counter` v prohlížeči, jak je uvedeno v direktivě `@page` v horní části, způsobí, že komponenta `Counter` vykreslí svůj obsah. Komponenty vykreslí do reprezentace v paměti stromu vykreslování, který lze poté použít k flexibilnímu a efektivnímu způsobu aktualizace uživatelského rozhraní.

Pokaždé, když je vybráno tlačítko pro **kliknutí na tlačítko Další** :

* Událost `onclick` je aktivována.
* Je volána metoda `IncrementCount`.
* `currentCount` se zvyšuje.
* Komponenta se znovu vykreslí.

Modul runtime porovná nový obsah s předchozím obsahem a na model DOM (Document Object Model) (DOM) použije pouze změněný obsah.

Přidejte komponentu do jiné komponenty pomocí syntaxe jazyka HTML. Přidejte například komponentu `Counter` do domovské stránky aplikace přidáním prvku `<Counter />` do komponenty `Index`.

*Pages/index. Razor*:

[!code-razor[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

Spusťte aplikaci. Domovská stránka má svůj vlastní čítač poskytovaný komponentou `Counter`.

Parametry komponenty jsou zadány pomocí atributů nebo [podřízeného obsahu](xref:blazor/components#child-content), který umožňuje nastavit vlastnosti pro podřízenou komponentu. Chcete-li přidat parametr do součásti `Counter`, aktualizujte blok `@code` součásti:

* Přidejte veřejnou vlastnost pro `IncrementAmount` s atributem `[Parameter]`.
* Změňte metodu `IncrementCount` na použití `IncrementAmount` při zvyšování hodnoty `currentCount`.

*Stránky/čítač. Razor*:

[!code-razor[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=12-13,17)]

Určete `IncrementAmount` v prvku `<Counter>` komponenty `Index` pomocí atributu.

*Pages/index. Razor*:

[!code-razor[](get-started/samples_snapshot/3.x/Index2.razor?highlight=7)]

Spusťte aplikaci. Komponenta `Index` má vlastní čítač, který při každém výběru tlačítka pro **kliknutí na tlačítko Další** zvyšuje o 10. Komponenta `Counter` (*Counter. Razor*) se v `/counter` nadále zvětšuje o jednu.

## <a name="next-steps"></a>Další kroky

<xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a>Další zdroje

* <xref:blazor/templates>
* <xref:signalr/introduction>
