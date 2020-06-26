---
title: Začínáme s ASP.NET CoreBlazor
author: guardrex
description: Začněte Blazor tím, že sestavíte Blazor aplikaci s vámi zvoleným nástrojem.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/31/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/get-started
ms.openlocfilehash: 1eabc35175d1b696de99488981b1382d231f5544
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2020
ms.locfileid: "85402777"
---
# <a name="get-started-with-aspnet-core-blazor"></a>Začínáme s ASP.NET CoreBlazor

Od [Daniel Skořepa](https://github.com/danroth27) a [Luke Latham](https://github.com/guardrex)

Pokud chcete začít používat Blazor , postupujte podle pokynů pro výběr nástrojů:

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

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli/)

1. Nainstalujte nejnovější verzi sady [.NET Core 3,1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1). Pokud jste dříve nainstalovali sadu SDK, můžete zjistit nainstalovanou verzi spuštěním následujícího příkazu v příkazovém prostředí:

   ```dotnetcli
   dotnet --version
   ```

1. Pro Blazor WebAssembly prostředí spusťte následující příkazy v příkazovém prostředí:

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   Pro Blazor Server prostředí spusťte následující příkazy v příkazovém prostředí:

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   Informace o těchto dvou Blazor modelech hostování *Blazor WebAssembly* a naleznete *Blazor Server* v tématu <xref:blazor/hosting-models> .

1. V prohlížeči přejděte na `https://localhost:5001` .

---

Na kartách na bočním panelu je k dispozici více stránek:

* Domů
* Čítač
* Načíst data

Na stránce čítač vyberte tlačítko pro zvýšení čítače bez aktualizace stránky. Zvýšení čítače na webové stránce obvykle vyžaduje psaní JavaScriptu, ale s Blazor můžete použít jazyk C#.

`Pages/Counter.razor`:

[!code-razor[](get-started/samples_snapshot/3.x/Counter1.razor?highlight=7,12-15)]

Požadavek na `/counter` v prohlížeči, jak je uveden v `@page` direktivě nahoře, způsobí, že `Counter` Komponenta vykreslí svůj obsah. Komponenty vykreslí do reprezentace v paměti stromu vykreslování, který lze poté použít k flexibilnímu a efektivnímu způsobu aktualizace uživatelského rozhraní.

Pokaždé, když je vybráno tlačítko:

* `onclick`Událost je aktivována.
* `IncrementCount`Metoda je volána.
* `currentCount`Dojde k zvýšení.
* Komponenta se znovu vykreslí.

Modul runtime porovná nový obsah s předchozím obsahem a na model DOM (Document Object Model) (DOM) použije pouze změněný obsah.

Přidejte komponentu do jiné komponenty pomocí syntaxe jazyka HTML. Například přidejte `Counter` komponentu do domovské stránky aplikace přidáním `<Counter />` elementu do `Index` komponenty.

`Pages/Index.razor`:

[!code-razor[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

Spusťte aplikaci. Domovská stránka má vlastní počítadlo poskytované `Counter` komponentou.

Parametry komponenty jsou zadány pomocí atributů nebo [podřízeného obsahu](xref:blazor/components/index#child-content), který umožňuje nastavit vlastnosti pro podřízenou komponentu. Chcete-li přidat parametr do `Counter` komponenty, aktualizujte blok komponenty `@code` :

* Přidejte veřejnou vlastnost pro `IncrementAmount` s [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) atributem.
* Změňte `IncrementCount` metodu na použití `IncrementAmount` při zvyšování hodnoty `currentCount` .

`Pages/Counter.razor`:

[!code-razor[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=12-13,17)]

Zadejte `IncrementAmount` v `Index` `<Counter>` elementu komponenty pomocí atributu.

`Pages/Index.razor`:

[!code-razor[](get-started/samples_snapshot/3.x/Index2.razor?highlight=7)]

Spusťte aplikaci. `Index`Komponenta má vlastní čítač, který se zvýší o deset pokaždé, když je vybráno tlačítko. `Counter`Komponenta () se v této části `Pages/Counter.razor` `/counter` stále zvyšuje o jednu.

## <a name="next-steps"></a>Další kroky

Vytvoření Blazor aplikace krok za krokem:

> [!div class="nextstepaction"]
> <xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a>Další zdroje

* <xref:blazor/templates>
* <xref:signalr/introduction>
