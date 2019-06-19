---
title: Začínáme s ASP.NET Core Blazor
author: guardrex
description: Začínáme s Blazor vytvořením Blazor aplikace pomocí nástrojů podle vašeho výběru.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 06/18/2019
uid: blazor/get-started
ms.openlocfilehash: c614ff52600434158c75e288e0b15985c0eb8e68
ms.sourcegitcommit: a1283d486ac1dcedfc7ea302e1cc882833e2c515
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2019
ms.locfileid: "67207657"
---
# <a name="get-started-with-aspnet-core-blazor"></a>Začínáme s ASP.NET Core Blazor

Podle [Daniel Roth](https://github.com/danroth27) a [Luke Latham](https://github.com/guardrex)

Začínáme s Blazor:

1. Nainstalujte nejnovější [.NET Core 3.0 ve verzi Preview SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0) release.

1. Instalace šablony Blazor spuštěním následujícího příkazu v příkazovém řádku:

   ```console
   dotnet new -i Microsoft.AspNetCore.Blazor.Templates::3.0.0-preview6.19307.2
   ```

1. Postupujte podle pokynů podle vaší volby nástrojů:

   # <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

   1\. Nainstalujte nejnovější [ve verzi preview sady Visual Studio](https://visualstudio.com/vs/preview) s **vývoj pro ASP.NET a web** pracovního vytížení.

   2\. Nainstalujte nejnovější [Blazor rozšíření](https://go.microsoft.com/fwlink/?linkid=870389) z webu Visual Studio Marketplace. Tento krok zpřístupní Blazor šablony sady Visual Studio.

   3\. Vytvořte nový projekt.

   4\. Vyberte **webová aplikace ASP.NET Core**. Vyberte **Další**.

   5\. Zadejte název projektu **název projektu** pole nebo přijměte výchozí název projektu. Potvrďte **umístění** položka je správný, a zadejte umístění pro projekt. Vyberte **Vytvořit**.

   6\. V **vytvořit novou webovou aplikaci ASP.NET Core** dialogového okna, ujistěte se, že **.NET Core** a **ASP.NET Core 3.0** jsou vybrány.

   7\. Blazor prostředí na straně klienta, vyberte **Blazor (na straně klienta)** šablony. Blazor prostředí na straně serveru, zvolte **Blazor (serverové)** šablony. Vyberte **Vytvořit**. Informace o dvou modelech hostování Blazor a na straně serveru a na straně klienta najdete v tématu <xref:blazor/hosting-models>.

   8\. Stisknutím klávesy **F5** ke spuštění aplikace.

   # <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)
   
   1\. Nainstalujte [Visual Studio Code](https://code.visualstudio.com/).

   2\. Nainstalujte nejnovější [ C# pro Visual Studio Code příponou](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).

   3\. Blazor prostředí na straně klienta spusťte následující příkaz z příkazového prostředí:

      ```console
      dotnet new blazor -o WebApplication1
      ```

      Blazor prostředí na straně serveru spusťte následující příkaz z příkazového prostředí:

      ```console
      dotnet new blazorserverside -o WebApplication1
      ```

      Informace o dvou modelech hostování Blazor a na straně serveru a na straně klienta najdete v tématu <xref:blazor/hosting-models>.

   4\. Otevřít *WebApplication1* složky ve Visual Studio Code.

   5\. Projekt Blazor na straně serveru požádá o integrovaného vývojového prostředí, abyste přidali prostředky pro sestavení a ladění projektu. Vyberte **Ano**.

   6\. Pokud používáte aplikaci na straně serveru Blazor aplikaci spustíme s použitím ladicího programu Visual Studio Code. Pokud používáte aplikaci na straně klienta Blazor, spusťte `dotnet run` ze složky projektu aplikace.

   7\. V prohlížeči přejděte na `https://localhost:5001`.

   <!--

   # [Visual Studio for Mac](#tab/visual-studio-mac)

   1\. Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/). Switch the [Update channel to Preview](/visualstudio/mac/install-preview).

   2\. Select **File** > **New Solution** or **New Project**.

   3\. In the sidebar, select **.NET Core** > **App**.

   4\. For a Blazor server-side experience, select the **ASP.NET Core Blazor (server-side)** template. For a Blazor client-side experience, select the **ASP.NET Core Blazor (client-side)** template. Select **Next**. For information on the two Blazor hosting models, server-side and client-side, see <xref:blazor/hosting-models>.

   5\. The **Target Framework** defaults to **.NET Core 3.0**. Select **Next**.

   6\. In the **Project Name** field, enter `WebApplication1`. Select **Create**.

   7\. Select **Run** > **Run Without Debugging** to run the app *without the debugger*. Running with the debugger isn't supported at this time.

   -->

   # <a name="net-core-clitabnetcore-cli"></a>[Rozhraní příkazového řádku .NET Core](#tab/netcore-cli/)

   Blazor prostředí na straně klienta spusťte následující příkazy z příkazového prostředí:

   ```console
   dotnet new blazor -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   Blazor prostředí na straně serveru spusťte následující příkazy z příkazového prostředí:

   ```console
   dotnet new blazorserverside -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   Informace o dvou modelech hostování Blazor a na straně serveru a na straně klienta najdete v tématu <xref:blazor/hosting-models>.

   V prohlížeči přejděte na `https://localhost:5001`.

   ---

Více stránek jsou k dispozici z karty na bočním panelu:

* Domů
* Čítač
* Načtení dat

Na stránce čítače, vyberte **klikněte na mě** tlačítka se zvýší čítač bez aktualizace stránky. Zvyšování hodnoty čítače na webové stránce obvykle vyžaduje zadání jazyka JavaScript, ale součásti syntaxe Razor poskytují lepší přístup pomocí C#.

*Pages/Counter.razor*:

[!code-cshtml[](get-started/samples_snapshot/3.x/Counter1.razor?highlight=7,12-15)]

Žádost o `/counter` v prohlížeči, jak jsou určené `@page` – direktiva v horní části stránky, způsobí, že součást čítače pro vykreslení jeho obsah. Komponenty vykreslování do reprezentaci v paměti, který lze použít k aktualizaci uživatelského rozhraní v flexibilní a efektivní způsob vykreslení stromu.

Pokaždé, když **klikněte na mě** výběru tlačítka:

* `onclick` Událost se aktivuje.
* `IncrementCount` Metoda je volána.
* `currentCount` Se zvýší.
* Komponenta se znovu vykreslí.

Modul runtime porovnává nový obsah na předchozí obsah a platí pouze změněný obsah do modelu Document Object Model (DOM).

Přidáte součást do jiné součásti pomocí syntaxe kódu HTML. Třeba přidat součást čítače na domovskou stránku aplikace tak, že přidáte `<Counter />` – element pro součást indexu.

*Pages/Index.razor*:

[!code-cshtml[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

Spusťte aplikaci. Na domovskou stránku má svůj vlastní čítače poskytované součást čítače.

Jsou zadány parametry komponenty pomocí atributů nebo [podřízený obsah](xref:blazor/components#child-content), které umožňují nastavit vlastnosti v podřízené součásti. Přidání parametru do komponenty čítače, aktualizovat součásti `@code` blok:

* Přidání vlastnosti pro `IncrementAmount` s `[Parameter]` atribut.
* Změnit `IncrementCount` metoda se má použít `IncrementAmount` při zvýšit hodnotu `currentCount`.

*Pages/Counter.razor*:

[!code-cshtml[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=12-13,17)]

Zadejte `IncrementAmount` v komponentě Index `<Counter>` pomocí atributu element.

*Pages/Index.razor*:

[!code-cshtml[](get-started/samples_snapshot/3.x/Index2.razor?highlight=7)]

Spusťte aplikaci. Součást indexu má svůj vlastní čítač, který zvýší o 10 pokaždé, když **klikněte na mě** výběru tlačítka. Součást čítače (*Counter.razor*) na `/counter` pořád postupně zvyšuje o jednu.

## <a name="next-steps"></a>Další kroky

<xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a>Další zdroje

* <xref:signalr/introduction>
