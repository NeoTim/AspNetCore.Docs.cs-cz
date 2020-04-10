---
title: Začínáme s ASP.NET CoreBlazor
author: guardrex
description: Můžete začít Blazor s Blazor tím, že vytvoříte aplikaci s nástroji, které si vyberete.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/09/2020
no-loc:
- Blazor
- SignalR
uid: blazor/get-started
ms.openlocfilehash: e9e6eeeb1d29aa529e43d75f5d3951d2c4384d7e
ms.sourcegitcommit: 4506a8f71ece921010ad6b7edebc8b200618f40d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2020
ms.locfileid: "81002909"
---
# <a name="get-started-with-aspnet-core-blazor"></a>Začínáme s ASP.NET Core Blazor

[Daniel Roth](https://github.com/danroth27) a [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Chcete-li začít s Blazorem, postupujte podle pokynů pro výběr nástrojů:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Chcete-li vytvářet aplikace Blazor Server, nainstalujte nejnovější verzi [Visual Studia 2019](https://visualstudio.microsoft.com/downloads/) s **ASP.NET a zatížením vývoje webu.**

   Chcete-li vytvořit aplikace Blazor Server a Blazor WebAssembly, nainstalujte nejnovější náhled [Visual Studia 2019](https://visualstudio.microsoft.com/vs/preview/) s **ASP.NET a zatížením vývoje webu.**

   Informace o dvou modelech Blazor hosting, *Blazor WebAssembly* <xref:blazor/hosting-models>a *Blazor Server*, naleznete v tématu .

1. Vytvoření nového projektu

1. Vyberte **Aplikaci Blazor**. Vyberte **další**.

1. Zadejte název projektu do pole **Název projektu** nebo přijměte výchozí název projektu. Potvrďte, že položka **Umístění** je správná, nebo zadejte umístění projektu. Vyberte **Vytvořit**.

1. Pro prostředí Blazor WebAssembly (Visual Studio 16.6 Preview 2 nebo novější) zvolte šablonu **aplikace Blazor WebAssembly.** Pro prostředí Blazor Server (Visual Studio 16.4 nebo novější) zvolte šablonu **aplikace Blazor Server.** Vyberte **Vytvořit**.

1. Stisknutím <kbd>klávesy Ctrl</kbd>+<kbd>F5</kbd> aplikaci spusťte.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. Nainstalujte sadu [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).

1. Volitelně nainstalujte šablonu náhledu [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) spuštěním následujícího příkazu:

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-preview3.20168.3
   ```

   > [!NOTE]
   > K použití šablony 3.2 Preview 3 Blazor WebAssembly je **vyžadována** sada [.NET Core SDK verze 3.1.201 nebo novější.](https://dotnet.microsoft.com/download/dotnet-core/3.1) Potvrďte nainstalovanou verzi sady .NET Core SDK spuštěním `dotnet --version` v příkazovém prostředí.

1. Nainstalujte [kód sady Visual Studio](https://code.visualstudio.com/).

1. Nainstalujte nejnovější [c# pro rozšíření kódu Sady Visual Studio](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) a rozšíření [debugger u v jazyce JavaScript (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) s `debug.javascript.usePreview` nastavenou na `true`.

1. Pro prostředí Serveru Blazor proveďte následující příkaz v příkazovém prostředí:

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   Pro prostředí Blazor WebAssembly proveďte následující příkaz v příkazovém prostředí:

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

   Informace o dvou modelech Blazor hosting, *Blazor Server* a <xref:blazor/hosting-models> *Blazor WebAssembly*, naleznete v tématu .

1. Otevřete složku *WebApplication1* v kódu sady Visual Studio.

1. IDE požadavky, které přidáte prostředky k sestavení a ladění projektu. Vyberte **ano**.

1. S Blazor Server, spusťte aplikaci pomocí ladicího programu Visual Studio Kód.

   S Blazor WebAssembly spusťte aplikaci pomocí konfigurace spuštění **.NET Core Launch (Blazor Standalone)** a poté spusťte prohlížeč pomocí **webové sestavy .NET Core Debug Blazor v** konfiguraci spuštění Chromu (vyžaduje Chrome). Další informace naleznete v tématu <xref:blazor/debug#visual-studio-code>.

1. V prohlížeči přejděte na . `https://localhost:5001`

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

Blazor Server je podporován v Sadě Visual Studio pro Mac. Blazor WebAssembly není v tuto chvíli podporována. Chcete-li vytvořit aplikace Blazor WebAssembly v systému macOS, postupujte podle pokynů na kartě **.NET Core CLI.**

1. Nainstalujte [Visual Studio pro Mac](https://visualstudio.microsoft.com/vs/mac/).

1. Vyberte **Soubor** > **nové řešení** nebo vytvořit nový **projekt**.

1. Na postranním panelu vyberte **.NET Core** > **App**.

1. Vyberte šablonu **aplikace Blazor Server.** Vyberte **Vytvořit**.

   Informace o hostitelském modelu serveru Blazor naleznete v tématu <xref:blazor/hosting-models>.

1. Nastavte **cílovou architekturu** na **.NET Core 3.1** a vyberte **Další**.

1. V poli **Název projektu** pojmenujte aplikaci `WebApplication1`. Vyberte **Vytvořit**.

1. Chcete-li spustit aplikaci *bez ladicího programu ,* vyberte **spustit** > **spustit bez ladění** . Spusťte aplikaci s **startem ladění** spustit aplikaci *s ladicím programem*.

Pokud se zobrazí výzva, která důvěryhodně důvěřuje certifikátu vývoje, důvěřujte certifikátu a pokračujte.

# <a name="net-core-cli"></a>[Rozhraní příkazového řádku .NET Core](#tab/netcore-cli/)

1. Nainstalujte sadu [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).

1. Volitelně nainstalujte šablonu náhledu [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) spuštěním následujícího příkazu:

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-preview3.20168.3
   ```

   > [!NOTE]
   > K použití šablony 3.2 Preview 3 Blazor WebAssembly je **vyžadována** sada [.NET Core SDK verze 3.1.201 nebo novější.](https://dotnet.microsoft.com/download/dotnet-core/3.1) Potvrďte nainstalovanou verzi sady .NET Core SDK spuštěním `dotnet --version` v příkazovém prostředí.

1. Pro prostředí Serveru Blazor proveďte v příkazovém prostředí následující příkazy:

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   Pro prostředí Blazor WebAssembly proveďte následující příkazy v příkazovém prostředí:

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   Informace o dvou modelech Blazor hosting, *Blazor Server* a <xref:blazor/hosting-models> *Blazor WebAssembly*, naleznete v tématu .

1. V prohlížeči přejděte na . `https://localhost:5001`

---

Více stránek je k dispozici na kartách v postranním panelu:

* Domů
* Čítač
* Načtení dat

Na stránce Čítač vyberte tlačítko Klikněte na **mě,** chcete-li čítač narůst bez aktualizace stránky. Zvýšení čítače na webové stránce obvykle vyžaduje psaní Blazor JavaScriptu, ale s můžete použít C#.

*Stránky/Counter.razor*:

[!code-razor[](get-started/samples_snapshot/3.x/Counter1.razor?highlight=7,12-15)]

Požadavek `/counter` v prohlížeči, jak `@page` je určeno směrnice v `Counter` horní části, způsobí, že komponenta vykreslit jeho obsah. Součásti vykreslování do reprezentace v paměti stromu vykreslení, které pak lze použít k aktualizaci ui flexibilní majestátu a efektivním způsobem.

Pokaždé, když je vybráno tlačítko **Klikni na mě:**

* Událost `onclick` je aktivována.
* Metoda `IncrementCount` je volána.
* Je `currentCount` se zpřísněn.
* Součást je vykreslenznovu.

Runtime porovná nový obsah s předchozím obsahem a použije pouze změněný obsah na objektový model dokumentu (DOM).

Přidejte komponentu do jiné součásti pomocí syntaxe HTML. Můžete například `Counter` přidat komponentu na domovskou stránku aplikace přidáním `<Counter />` prvku do komponenty. `Index`

*Stránky/Index.holicí strojek*:

[!code-razor[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

Spusťte aplikaci. Domovská stránka má svůj vlastní `Counter` čítač poskytovaný komponentou.

Parametry komponenty jsou určeny pomocí atributů nebo [podřízeného obsahu](xref:blazor/components#child-content), které umožňují nastavit vlastnosti podřízené součásti. Chcete-li do `Counter` komponenty přidat parametr, `@code` aktualizujte blok komponenty:

* Přidejte veřejnou `IncrementAmount` vlastnost `[Parameter]` s atributem.
* Změňte `IncrementCount` metodu, `IncrementAmount` která se `currentCount`má použít při zvyšování hodnoty .

*Stránky/Counter.razor*:

[!code-razor[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=12-13,17)]

Zadejte `IncrementAmount` prvek `Index` v `<Counter>` elementu komponenty pomocí atributu.

*Stránky/Index.holicí strojek*:

[!code-razor[](get-started/samples_snapshot/3.x/Index2.razor?highlight=7)]

Spusťte aplikaci. Komponenta `Index` má svůj vlastní čítač, který se pohybuje o deset pokaždé, když je vybráno tlačítko **Klikněte na mě.** Komponenta `Counter` (*Counter.razor*) pokračuje `/counter` v přírůstcích o jednu.

## <a name="next-steps"></a>Další kroky

<xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a>Další zdroje

* <xref:blazor/templates>
* <xref:signalr/introduction>
