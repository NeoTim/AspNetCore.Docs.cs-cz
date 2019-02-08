---
title: Začínáme s Razor komponenty
author: guardrex
description: Zjistěte, jak začít pracovat s rozhraní komponenty Razor.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/03/2019
uid: razor-components/get-started
ms.openlocfilehash: d35245135139adbb5c0cb63b7d8e309a74948da8
ms.sourcegitcommit: b72bbc9ae91e4bd37c9ea9b2d09ebf47afb25dd7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/08/2019
ms.locfileid: "55958106"
---
# <a name="get-started-with-razor-components"></a>Začínáme s Razor komponenty

Podle [Daniel Roth](https://github.com/danroth27) a [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/razor-components-preview-notice.md)]

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Požadavky:

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

Chcete-li vytvořit svůj první projekt Razor komponenty v sadě Visual Studio:

1. Vyberte **souboru** > **nový projekt** > **webové** > **webová aplikace ASP.NET Core**.
1. Ujistěte se, že **.NET Core** a **ASP.NET Core 3.0** jsou vybrány v horní části.
1. Zvolte **Razor komponenty** šablony a vyberte **OK**.

   ![Dialogové okno nové aplikace](https://msdnshared.blob.core.windows.net/media/2019/01/razor-components-template.png)

1. Stisknutím klávesy **F5** ke spuštění aplikace.

Blahopřejeme! Právě jste spustili svou první aplikaci Razor součásti!

<!--

# [Visual Studio Code](#tab/visual-studio-code)

Prerequisites:

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

To create your first Razor Components project in Visual Studio Code:

1. Execute the following command from a command shell:

   ```console
   dotnet new razorcomponents -o WebApplication1
   ```

1. Open the *WebApplication1* folder in Visual Studio Code.

1. Add a *.vscode* folder.

1. Add a *tasks.json* file to the *.vscode* folder with the following content:

   [!code-json[](get-started/samples_snapshot/3.x/tasks.json)]

1. Add a *launch.json* file to the *.vscode* folder with the following content:

   [!code-json[](get-started/samples_snapshot/3.x/launch.json)]

1. Execute the app using the Visual Studio Code debugger.

1. In a browser, navigate to `https://localhost:5001`.

Congrats! You just ran your first Razor Components app!

# [Visual Studio for Mac](#tab/visual-studio-mac)

.NET Core 3.0 will be supported with Visual Studio for Mac version 8.0 or later. Visual Studio for Mac version 8.0 Preview isn't available at this time.

Use the [.NET Core CLI version of this topic](xref:razor-components/get-started?tabs=netcore-cli) on macOS.


[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

To create your first project Razor Components project in Visual Studio for Mac:

1. Select **File** > **New Solution** or **New Project**.
1. In the sidebar, select **.NET Core** > **App**.
1. Select **ASP.NET Core Razor Components** and select **Next**.
1. The **Target Framework** defaults to **.NET Core 3.0**. Select **Next**.
1. In the **Project Name** field, enter `WebApplication1`. Select **Create**.
1. Select **Run** > **Run Without Debugging** to run the app *without the debugger*. Running with the debugger isn't supported at this time.

Congratulations! You just ran your first Razor Components app!
-->

# <a name="net-core-clitabnetcore-cli"></a>[Rozhraní příkazového řádku .NET Core](#tab/netcore-cli/)

Požadavky:

* [.NET core SDK 3.0 ve verzi Preview](https://dotnet.microsoft.com/download/dotnet-core/3.0)

Chcete vytvořit svůj první projekt Razor součásti z příkazové prostředí:

```console
dotnet new razorcomponents -o WebApplication1
cd WebApplication1
dotnet run
```

Blahopřejeme! Právě jste spustili svou první aplikaci Razor součásti!

---

## <a name="razor-components-project"></a>Projekt součásti syntaxe Razor

Vytvořený pomocí šablony Razor komponenty řešení obsahuje dva projekty:

* *WebApplication1.Server* &ndash; serverový projekt je projekt ASP.NET Core nastavení pro hostování aplikace součásti syntaxe Razor.
* *WebApplication1.App* &ndash; na straně klienta webového projektu uživatelského rozhraní, která používá součásti syntaxe Razor.

Logika uživatelského rozhraní *WebApplication1.App* projektu je oddělené od zbytku aplikace kvůli technická omezení v ASP.NET Core 3.0 ve verzi Preview 2. Přípona souboru Razor (*.cshtml*) použít pro Razor komponenty se také používá pro zobrazení Razor Pages a MVC. V současné době Razor součásti a Razor Pages/MVC mají různé kompilace modely, tak soubory Razor Razor komponenty jsou udržovány odděleně. V budoucí verzi preview, plánujeme zavést novou příponu souboru pro Razor součásti (*.razor*). Součásti, stránky a zobrazení se hostovat *ve stejném projektu*.

Při spuštění aplikace jsou k dispozici z karty na bočním panelu více stránek:

* Domů
* Čítač
* Načtení dat

Na stránce čítače, vyberte **klikněte na mě** tlačítka se zvýší čítač bez aktualizace stránky. Zvyšování hodnoty čítače na webové stránce obvykle vyžaduje zadání jazyka JavaScript, ale součásti Razor poskytuje lepší přístup pomocí C#.

*WebApplication1.App/Pages/Counter.cshtml*:

[!code-cshtml[](get-started/samples_snapshot/3.x/Counter1.cshtml)]

Žádost o `/counter` v prohlížeči, jak jsou určené `@page` – direktiva v horní části stránky, způsobí, že součást čítače pro vykreslení jeho obsah. Komponenty vykreslování do reprezentaci v paměti, který lze použít k aktualizaci uživatelského rozhraní v flexibilní a efektivní způsob vykreslení stromu.

Pokaždé, když **klikněte na mě** výběru tlačítka:

* `onclick` Událost se aktivuje.
* `IncrementCount` Metoda je volána.
* `currentCount` Se zvýší.
* Komponenta se znovu vykreslí.

Modul runtime porovnává nový obsah na předchozí obsah a platí pouze změněný obsah do modelu Document Object Model (DOM).

Přidáte součást do jiné součásti pomocí syntaxe HTML. Komponenta parametry jsou určeny pomocí atributů nebo podřízený obsah. Například můžete přidat součást čítače na domovskou stránku aplikace tak, že přidáte `<Counter />` – element pro součást indexu.

*WebApplication1.App/Pages/Index.cshtml*:

[!code-cshtml[](get-started/samples_snapshot/3.x/Index1.cshtml?highlight=7)]

Spusťte aplikaci. Na domovskou stránku má svůj vlastní čítače.

Přidání parametru do komponenty čítače, aktualizovat součásti `@functions` blok:

* Přidání vlastnosti pro `IncrementAmount` dekorován `[Parameter]` atribut.
* Změnit `IncrementCount` metoda se má použít `IncrementAmount` při zvýšit hodnotu `currentCount`.

*WebApplication1.App/Pages/Counter.cshtml*:

[!code-cshtml[](get-started/samples_snapshot/3.x/Counter2.cshtml?highlight=4,8)]

Zadejte `IncrementAmount` parametr v komponentě domovské `<Counter>` pomocí atributu element.

*WebApplication1.App/Pages/Index.cshtml*:

[!code-cshtml[](get-started/samples_snapshot/3.x/Index2.cshtml)]

Spusťte aplikaci. Na domovskou stránku má svůj vlastní čítač, který zvýší o 10 pokaždé, když **klikněte na mě** výběru tlačítka.

## <a name="next-steps"></a>Další kroky

<xref:tutorials/first-razor-components-app>
