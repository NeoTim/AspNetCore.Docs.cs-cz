---
title: Začínáme s Blazor
author: guardrex
description: Začínáme s Blazor vytvořením Blazor aplikace pomocí nástrojů podle vašeho výběru.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 05/06/2019
uid: blazor/get-started
ms.openlocfilehash: 09613f5d8a4d130f7dca53f31bdd33de527fc776
ms.sourcegitcommit: b4ef2b00f3e1eb287138f8b43c811cb35a100d3e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/21/2019
ms.locfileid: "65969863"
---
# <a name="get-started-with-blazor"></a>Začínáme s Blazor

Podle [Daniel Roth](https://github.com/danroth27) a [Luke Latham](https://github.com/guardrex)

Začínáme s Blazor:

1. Nainstalujte nejnovější [.NET Core 3.0 ve verzi Preview SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0) release.

1. Instalace šablony Blazor spuštěním následujícího příkazu v příkazovém řádku:

   ```console
   dotnet new -i Microsoft.AspNetCore.Blazor.Templates::3.0.0-preview5-19227-01
   ```

1. Postupujte podle pokynů podle vaší volby nástrojů:

   # <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

   1.&nbsp;nainstalujte nejnovější [ve verzi preview sady Visual Studio](https://visualstudio.com/preview) s **vývoj pro ASP.NET a web** pracovního vytížení.

   2.&nbsp;nainstalujte nejnovější [Blazor rozšíření](https://go.microsoft.com/fwlink/?linkid=870389) z webu Visual Studio Marketplace. Tento krok zpřístupní Blazor šablony sady Visual Studio.

   3.&nbsp;Pokud používáte nejnovější stabilní verze sady Visual Studio (nikoli verze preview), povolte sadě Visual Studio pomocí sady SDK ve verzi preview: Otevřít **nástroje** > **možnosti** v panelu nabídek. Otevřít **projekty a řešení** uzlu. Otevřít **.NET Core** kartu. Zaškrtněte políčko u **pomocí verze Preview sady .NET Core SDK**. Vyberte **OK**.

   4.&nbsp;vytvořte nový projekt.

   5.&nbsp;vyberte **webová aplikace ASP.NET Core**. Vyberte **Další**.

   6.&nbsp;zadejte název projektu **název projektu** pole nebo přijměte výchozí název projektu. Potvrďte **umístění** položka je správný, a zadejte umístění pro projekt. Vyberte **Vytvořit**.

   7.&nbsp;v **vytvořit novou webovou aplikaci ASP.NET Core** dialogového okna, ujistěte se, že **.NET Core** a **ASP.NET Core 3.0** jsou vybrány.

   8.&nbsp;Blazor prostředí na straně klienta, vyberte **Blazor (na straně klienta)** šablony. Blazor prostředí na straně serveru, zvolte **Blazor (serverové)** šablony. Vyberte **Vytvořit**. Informace o dvou modelech hostování Blazor a na straně serveru a na straně klienta najdete v tématu <xref:blazor/hosting-models>.

   9.&nbsp;stiskněte **F5** ke spuštění aplikace.

   # <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)
   
   1.&nbsp;nainstalovat [Visual Studio Code](https://code.visualstudio.com/).

   2.&nbsp;nainstalujte nejnovější [ C# pro Visual Studio Code příponou](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).

   3.&nbsp;Blazor prostředí na straně klienta, spusťte následující příkaz z příkazového prostředí:

      ```console
      dotnet new blazor -o WebApplication1
      ```

      Blazor prostředí na straně serveru spusťte následující příkaz z příkazového prostředí:

      ```console
      dotnet new blazorserverside -o WebApplication1
      ```

      Informace o dvou modelech hostování Blazor a na straně serveru a na straně klienta najdete v tématu <xref:blazor/hosting-models>.

   4.&nbsp;otevřít *WebApplication1* složky ve Visual Studio Code.

   5.&nbsp;projektu pro Blazor na straně serveru, integrovaného vývojového prostředí požádá, abyste přidali prostředky pro sestavení a ladění projektu. Vyberte **Ano**.

   6.&nbsp;při použití aplikace na straně serveru Blazor aplikaci spustíme s použitím ladicího programu Visual Studio Code. Pokud používáte aplikaci na straně klienta Blazor, spusťte `dotnet run` ze složky projektu aplikace.

   <!--

   # [Visual Studio for Mac](#tab/visual-studio-mac)

   1.&nbsp;Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/). Switch the [Update channel to Preview](/visualstudio/mac/install-preview).

   2.&nbsp;Select **File** > **New Solution** or **New Project**.

   3.&nbsp;In the sidebar, select **.NET Core** > **App**.

   4.&nbsp;For a Blazor server-side experience, select the **ASP.NET Core Blazor (server-side)** template. For a Blazor client-side experience, select the **ASP.NET Core Blazor (client-side)** template. Select **Next**. For information on the two Blazor hosting models, server-side and client-side, see <xref:blazor/hosting-models>.

   5.&nbsp;The **Target Framework** defaults to **.NET Core 3.0**. Select **Next**.

   6.&nbsp;In the **Project Name** field, enter `WebApplication1`. Select **Create**.

   7.&nbsp;Select **Run** > **Run Without Debugging** to run the app *without the debugger*. Running with the debugger isn't supported at this time.

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

   ---

V prohlížeči přejděte na `https://localhost:5001`.

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

Přidáte součást do jiné součásti pomocí syntaxe pro HTML. Komponenta parametry jsou určeny pomocí atributů nebo podřízený obsah. Například můžete přidat součást čítače na domovskou stránku aplikace tak, že přidáte `<Counter />` – element pro součást indexu.

*Pages/Index.razor*:

[!code-cshtml[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

Spusťte aplikaci. Na domovskou stránku má svůj vlastní čítače poskytované součást čítače.

Přidání parametru do komponenty čítače, aktualizovat součásti `@functions` blok:

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
