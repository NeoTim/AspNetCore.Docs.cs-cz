---
title: Začínáme s ASP.NET Core Blazor
author: guardrex
description: Začněte s Blazor vytvořením Blazor aplikace pomocí nástrojů podle vašeho výběru.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 12/09/2019
no-loc:
- Blazor
uid: blazor/get-started
ms.openlocfilehash: e368ecaf931d392de7e52ec2d5a2dfd171c2c86f
ms.sourcegitcommit: 851b921080fe8d719f54871770ccf6f78052584e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/09/2019
ms.locfileid: "74943759"
---
# <a name="get-started-with-aspnet-core-opno-locblazor"></a>Začínáme s ASP.NET Core Blazor

Od [Daniel Skořepa](https://github.com/danroth27) a [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Začínáme s Blazor:

::: moniker range=">= aspnetcore-3.1"

1. Nainstalujte [sadu .NET Core 3,1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).

1. Volitelně nainstalujte šablonu [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) :
   * Nainstalujte [sadu SDK .NET Core 3,1 nebo novější (Preview)](https://dotnet.microsoft.com/download/dotnet-core/3.1).
   * Spusťte následující příkaz v příkazovém prostředí. [Microsoft. AspNetCore.Blazor. Balíček šablon](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.Templates/) má verzi Preview, zatímco Blazor WebAssembly je ve verzi Preview.

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Blazor.Templates::3.1.0-preview4.19579.2
   ```

1. Postupujte podle pokynů pro výběr nástrojů:

   # <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

   1\. Nainstalujte [Visual Studio 16,4 nebo novější](https://visualstudio.microsoft.com/vs/preview/) s úlohou **vývoje ASP.NET a webu** .

   2\. Vytvořte nový projekt.

   3\. Vyberte **Blazor aplikace**. Vyberte **Další**.

   4\. Do pole **název projektu** zadejte název projektu nebo přijměte výchozí název projektu. Potvrďte správnost záznamu **umístění** nebo zadejte umístění projektu. Vyberte **Vytvořit**.

   5\. Pro Blazor prostředí WebAssembly vyberte šablonu **aplikaceBlazor WebAssembly** . Pro prostředí Blazor serveru vyberte šablonu **aplikaceBlazor Server** . Vyberte **Vytvořit**. Informace o těchto dvou Blazor modelech hostování, *Blazor serveru* a *Blazor WebAssembly*naleznete v tématu <xref:blazor/hosting-models>.

   6\. Spusťte aplikaci stisknutím klávesy **Ctrl**+**F5** .

   > [!NOTE]
   > Pokud jste nainstalovali rozšíření Blazor sady Visual Studio pro předchozí verzi Preview služby ASP.NET Core Blazor (Preview 6 nebo starší), můžete rozšíření odinstalovat. Instalace šablon Blazor v příkazovém prostředí je teď dostačující pro povrchování šablon v aplikaci Visual Studio.

   # <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

   1\. Nainstalujte [Visual Studio Code](https://code.visualstudio.com/).

   2\. Nainstalujte nejnovější verzi [ C# pro Visual Studio Code rozšíření](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).

   3\. Pro Blazor prostředí WebAssembly spusťte v příkazovém prostředí následující příkaz:

      ```dotnetcli
      dotnet new blazorwasm -o WebApplication1
      ```

      Pro prostředí Blazor serveru spusťte v příkazovém prostředí následující příkaz:

      ```dotnetcli
      dotnet new blazorserver -o WebApplication1
      ```

      Informace o těchto dvou Blazor modelech hostování, *Blazor serveru* a *Blazor WebAssembly*naleznete v tématu <xref:blazor/hosting-models>.

   4\. Otevřete složku *WebApplication1* v Visual Studio Code.

   5\. V případě projektu Blazor serveru rozhraní IDE požaduje, abyste přidali prostředky pro sestavení a ladění projektu. Vyberte **Ano**.

   6\. Pokud používáte aplikaci Blazor serveru, spusťte aplikaci pomocí ladicího programu Visual Studio Code. Pokud používáte aplikaci Blazor WebAssembly, spusťte `dotnet run` ze složky projektu aplikace.

   7\. V prohlížeči přejděte na `https://localhost:5001`.

   # <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

   1\. Nainstalujte [Visual Studio pro Mac](https://visualstudio.microsoft.com/vs/mac/). Přepněte [kanál aktualizace na verzi Preview](/visualstudio/mac/install-preview).

   2\. Vyberte **soubor** > **nové řešení** nebo vytvořte **Nový projekt**.

   3\. Na bočním panelu vyberte **.NET Core** > **App**.

   4\. Vyberte šablonu **aplikaceBlazorového serveru** . V tuto chvíli je v Visual Studio pro Mac dostupná jenom šablona serveru Blazor. Pro Blazor prostředí WebAssembly postupujte podle pokynů na kartě **.NET Core CLI** . Po výběru šablony Blazor serveru vyberte **Další**. Informace o těchto dvou Blazor modelech hostování, *Blazor serveru* a *Blazor WebAssembly*naleznete v tématu <xref:blazor/hosting-models>.

   <!-- For a Blazor WebAssembly experience, select the **Blazor WebAssembly App** template. Select **Next**. -->

   5\. Nastavte **cílovou architekturu** na **.NET Core 3,1** a vyberte **Další**.

   6\. Do pole **název projektu** název aplikace `WebApplication1`. Vyberte **Vytvořit**.

   7\. Vyberte **spustit** > **Spustit bez ladění** pro spuštění aplikace *bez ladicího programu*. Spusťte aplikaci s použitím **Spustit ladění** a spusťte aplikaci *pomocí ladicího programu*.

       If a prompt appears to trust the development certificate, trust the certificate and continue.

   # <a name="net-core-clitabnetcore-cli"></a>[Rozhraní příkazového řádku .NET Core](#tab/netcore-cli/)

   Pro Blazor prostředí WebAssembly spusťte v příkazovém prostředí následující příkazy:

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   Pro prostředí Blazor serveru spusťte v příkazovém prostředí následující příkazy:

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   Informace o těchto dvou Blazor modelech hostování, *Blazor serveru* a *Blazor WebAssembly*naleznete v tématu <xref:blazor/hosting-models>.

   V prohlížeči přejděte na `https://localhost:5001`.

   ---

::: moniker-end

::: moniker range="< aspnetcore-3.1"

1. Nainstalujte si nejnovější verzi [sady .NET Core 3,0 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0).

1. Volitelně nainstalujte šablonu [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) :
   * Nainstalujte [sadu SDK .NET Core 3,1 nebo novější (Preview)](https://dotnet.microsoft.com/download/dotnet-core/3.1).
   * Spusťte následující příkaz v příkazovém prostředí. [Microsoft. AspNetCore.Blazor. Balíček šablon](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.Templates/) má verzi Preview, zatímco Blazor WebAssembly je ve verzi Preview.

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Blazor.Templates::3.1.0-preview4.19579.2
   ```

1. Postupujte podle pokynů pro výběr nástrojů:

   # <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

   1\. Nainstalujte si nejnovější verzi sady [Visual Studio](https://visualstudio.com/vs/) s úlohou **vývoje ASP.NET a webu** .

   2\. Volitelně můžete nainstalovat [Visual Studio 16,4 Preview 2 nebo novější](https://visualstudio.microsoft.com/vs/preview/) s úlohou **vývoje ASP.NET a webu** pro Blazor vývoj aplikací pro WebAssembly.

   3\. Vytvořte nový projekt.

   4\. Vyberte **Blazor aplikace**. Vyberte **Další**.

   5\. Do pole **název projektu** zadejte název projektu nebo přijměte výchozí název projektu. Potvrďte správnost záznamu **umístění** nebo zadejte umístění projektu. Vyberte **Vytvořit**.

   6\. Pro Blazor prostředí WebAssembly vyberte šablonu **aplikaceBlazor WebAssembly** . Pro prostředí Blazor serveru vyberte šablonu **aplikaceBlazor Server** . Vyberte **Vytvořit**. Informace o těchto dvou Blazor modelech hostování, *Blazor serveru* a *Blazor WebAssembly*naleznete v tématu <xref:blazor/hosting-models>.

   7\. Stisknutím klávesy **F5** spusťte aplikaci.

   > [!NOTE]
   > Pokud jste nainstalovali rozšíření Blazor sady Visual Studio pro předchozí verzi Preview služby ASP.NET Core Blazor (Preview 6 nebo starší), můžete rozšíření odinstalovat. Instalace šablon Blazor v příkazovém prostředí je teď dostačující pro povrchování šablon v aplikaci Visual Studio.

   # <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

   1\. Nainstalujte [Visual Studio Code](https://code.visualstudio.com/).

   2\. Nainstalujte nejnovější verzi [ C# pro Visual Studio Code rozšíření](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).

   3\. Pro Blazor prostředí WebAssembly spusťte v příkazovém prostředí následující příkaz:

      ```dotnetcli
      dotnet new blazorwasm -o WebApplication1
      ```

      Pro prostředí Blazor serveru spusťte v příkazovém prostředí následující příkaz:

      ```dotnetcli
      dotnet new blazorserver -o WebApplication1
      ```

      Informace o těchto dvou Blazor modelech hostování, *Blazor serveru* a *Blazor WebAssembly*naleznete v tématu <xref:blazor/hosting-models>.

   4\. Otevřete složku *WebApplication1* v Visual Studio Code.

   5\. V případě projektu Blazor serveru rozhraní IDE požaduje, abyste přidali prostředky pro sestavení a ladění projektu. Vyberte **Ano**.

   6\. Pokud používáte aplikaci Blazor serveru, spusťte aplikaci pomocí ladicího programu Visual Studio Code. Pokud používáte aplikaci Blazor WebAssembly, spusťte `dotnet run` ze složky projektu aplikace.

   7\. V prohlížeči přejděte na `https://localhost:5001`.

   # <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

   1\. Nainstalujte [Visual Studio pro Mac](https://visualstudio.microsoft.com/vs/mac/). Přepněte [kanál aktualizace na verzi Preview](/visualstudio/mac/install-preview).

   2\. Vyberte **soubor** > **nové řešení** nebo vytvořte **Nový projekt**.

   3\. Na bočním panelu vyberte **.NET Core** > **App**.

   4\. Vyberte šablonu **aplikaceBlazorového serveru** . V tuto chvíli je v Visual Studio pro Mac dostupná jenom šablona serveru Blazor. Pro Blazor prostředí WebAssembly postupujte podle pokynů na kartě **.NET Core CLI** . Po výběru šablony Blazor serveru vyberte **Další**. Informace o těchto dvou Blazor modelech hostování, *Blazor serveru* a *Blazor WebAssembly*naleznete v tématu <xref:blazor/hosting-models>.

   <!-- For a Blazor WebAssembly experience, select the **Blazor WebAssembly App** template. Select **Next**. -->

   5\. Nastavte **cílovou architekturu** na **.NET Core 3,0** a vyberte **Další**.

   6\. Do pole **název projektu** název aplikace `WebApplication1`. Vyberte **Vytvořit**.

   7\. Vyberte **spustit** > **Spustit bez ladění** pro spuštění aplikace *bez ladicího programu*. Spusťte aplikaci s použitím **Spustit ladění** a spusťte aplikaci *pomocí ladicího programu*.

       If a prompt appears to trust the development certificate, trust the certificate and continue.

   # <a name="net-core-clitabnetcore-cli"></a>[Rozhraní příkazového řádku .NET Core](#tab/netcore-cli/)

   Pro Blazor prostředí WebAssembly spusťte v příkazovém prostředí následující příkazy:

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   Pro prostředí Blazor serveru spusťte v příkazovém prostředí následující příkazy:

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   Informace o těchto dvou Blazor modelech hostování, *Blazor serveru* a *Blazor WebAssembly*naleznete v tématu <xref:blazor/hosting-models>.

   V prohlížeči přejděte na `https://localhost:5001`.

   ---

::: moniker-end

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

## <a name="next-steps"></a>Další postup

<xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a>Další zdroje

* <xref:blazor/templates>
* <xref:signalr/introduction>
