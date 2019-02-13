---
title: Začínáme s Blazor
author: guardrex
description: Zjistěte, jak začít pracovat s Blazor vytvořením a úpravou Blazor projektu.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2019
uid: spa/blazor/get-started
ms.openlocfilehash: 8c984bab8a13b4fc2d87fd1a7e0b285dfa25ba09
ms.sourcegitcommit: af8a6eb5375ef547a52ffae22465e265837aa82b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2019
ms.locfileid: "56159621"
---
# <a name="get-started-with-blazor"></a>Začínáme s Blazor

Podle [Daniel Roth](https://github.com/danroth27) a [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/razor-components-preview-notice.md)]

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Požadavky:

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

Chcete-li vytvořit svůj první projekt Blazor v sadě Visual Studio:

1. Nainstalujte nejnovější [rozšíření služeb jazyka Blazor](https://go.microsoft.com/fwlink/?linkid=870389) z webu Visual Studio Marketplace. Tento krok zpřístupní Blazor šablony sady Visual Studio.
1. Šablony Blazor zpřístupníte pro použití s .NET Core CLI spuštěním následujícího příkazu v příkazovém řádku:

   ```console
   dotnet new -i Microsoft.AspNetCore.Blazor.Templates
   ```

1. Vyberte **souboru** > **nový projekt** > **webové** > **webová aplikace ASP.NET Core**.
1. Ujistěte se, že **.NET Core** a **ASP.NET Core 3.0** jsou vybrány v horní části.
1. Zvolte **Blazor** šablony a vyberte **OK**.
1. Stisknutím klávesy **F5** ke spuštění aplikace.

Blahopřejeme! Právě jste spustili svou první aplikaci Blazor!

<!--

# [Visual Studio Code](#tab/visual-studio-code)

Prerequisites:

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

To create your first Blazor project in Visual Studio Code:

1. Execute the following command in a command shell:

   ```console
   dotnet new blazor -o WebApplication1
   ```

1. Open the *WebApplication1* folder in Visual Studio Code.

1. Visual Studio code offers to create assets to build and debug the app, which includes the *tasks.json* and *launch.json* files. Select **Yes** to add the assets.

1. Execute the app using the Visual Studio Code debugger.

1. In a browser, navigate to `https://localhost:5001`.

Congratulations! You just ran your first Blazor app!

# [Visual Studio for Mac](#tab/visual-studio-mac)

.NET Core 3.0 will be supported with Visual Studio for Mac version 8.0 or later. Visual Studio for Mac version 8.0 Preview isn't available at this time.

Use the [.NET Core CLI version of this topic](xref:razor-components/get-started?tabs=netcore-cli) on macOS.

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

To create your first project Blazor project in Visual Studio for Mac:

1. Select **File** > **New Solution** or **New Project**.
1. In the sidebar, select **.NET Core** > **App**.
1. Select **Blazor** and select **Next**.
1. The **Target Framework** defaults to **.NET Core 3.0**. Select **Next**.
1. In the **Project Name** field, enter `WebApplication1`. Select **Create**.
1. Select **Run** > **Run Without Debugging** to run the app *without the debugger*. Running with the debugger isn't supported at this time.

Congratulations! You just ran your first Blazor app!
-->

# <a name="net-core-clitabnetcore-cli"></a>[Rozhraní příkazového řádku .NET Core](#tab/netcore-cli/)

Požadavky:

* [.NET core SDK 3.0 ve verzi Preview](https://dotnet.microsoft.com/download/dotnet-core/3.0)

1. Přidejte do ní šablony Blazor spuštěním následujícího příkazu v příkazovém řádku:

   ```console
   dotnet new -i Microsoft.AspNetCore.Blazor.Templates
   ```

1. Vytvořte svůj první projekt Blazor v příkazovém řádku:

   ```console
   dotnet new blazor -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

1. V prohlížeči přejděte na `https://localhost:5001`.

Blahopřejeme! Právě jste spustili svou první aplikaci Blazor!

---

## <a name="blazor-project"></a>Blazor projektu

Při spuštění aplikace jsou k dispozici z karty na bočním panelu více stránek:

* Domů
* Čítač
* Načtení dat

Na stránce čítače, vyberte **klikněte na mě** tlačítka se zvýší čítač bez aktualizace stránky. Zvyšování hodnoty čítače na webové stránce obvykle vyžaduje zadání jazyka JavaScript, ale Blazor poskytuje lepší přístup pomocí C#.

*Pages/Counter.cshtml*:

[!code-cshtml[](get-started/samples_snapshot/3.x/Counter1.cshtml)]

Žádost o `/counter` v prohlížeči, jak jsou určené `@page` – direktiva v horní části stránky, způsobí, že součást čítače pro vykreslení jeho obsah. Komponenty vykreslování do reprezentaci v paměti, který lze použít k aktualizaci uživatelského rozhraní v flexibilní a efektivní způsob vykreslení stromu.

Pokaždé, když **klikněte na mě** výběru tlačítka:

* `onclick` Událost se aktivuje.
* `IncrementCount` Metoda je volána.
* `currentCount` Se zvýší.
* Komponenta se znovu vykreslí.

Modul runtime porovnává nový obsah na předchozí obsah a platí pouze změněný obsah do modelu Document Object Model (DOM).

Přidáte součást do jiné součásti pomocí syntaxe HTML. Komponenta parametry jsou určeny pomocí atributů nebo podřízený obsah. Například můžete přidat součást čítače na domovskou stránku aplikace tak, že přidáte `<Counter />` – element pro součást indexu.

V *Pages/Index.cshtml*, nahraďte výzvy průzkumu s komponentou čítače:

[!code-cshtml[](get-started/samples_snapshot/3.x/Index1.cshtml?highlight=7)]

Spusťte aplikaci. Na domovskou stránku má svůj vlastní čítače.

Přidání parametru do komponenty čítače, aktualizovat součásti `@functions` blok:

* Přidání vlastnosti pro `IncrementAmount` dekorován `[Parameter]` atribut.
* Změnit `IncrementCount` metoda se má použít `IncrementAmount` při zvýšit hodnotu `currentCount`.

*Pages/Counter.cshtml*:

[!code-cshtml[](get-started/samples_snapshot/3.x/Counter2.cshtml?highlight=4,8)]

Zadejte `IncrementAmount` parametr v komponentě domovské `<Counter>` pomocí atributu element.

*Pages/Index.cshtml*:

[!code-cshtml[](get-started/samples_snapshot/3.x/Index2.cshtml)]

Spusťte aplikaci. Na domovskou stránku má svůj vlastní čítač, který zvýší o 10 pokaždé, když **klikněte na mě** výběru tlačítka.

## <a name="next-steps"></a>Další kroky

<xref:tutorials/first-razor-components-app>
