---
title: Začínáme s Blazor
author: guardrex
description: Zjistěte, jak začít pracovat s Blazor.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 04/19/2019
uid: blazor/get-started
ms.openlocfilehash: 45ae0acc6aaee433cce4eddb2fe9c59c306581d7
ms.sourcegitcommit: eb784a68219b4829d8e50c8a334c38d4b94e0cfa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2019
ms.locfileid: "59982669"
---
# <a name="get-started-with-blazor"></a>Začínáme s Blazor

Podle [Daniel Roth](https://github.com/danroth27) a [Luke Latham](https://github.com/guardrex)

V několika krocích Blazor začněte:

1. Nainstalujte nejnovější [.NET Core 3.0 ve verzi Preview SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0) release.

1. Instalace šablony Blazor spuštěním následujícího příkazu v příkazovém řádku:

   ```console
   dotnet new -i Microsoft.AspNetCore.Blazor.Templates::3.0.0-preview4-19216-03
   ```

1. Postupujte podle pokynů podle vaší volby nástrojů:

   # <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

   1.&nbsp;nainstalovat verzi preview [Visual Studio 2019](https://visualstudio.com/preview) s **vývoj pro ASP.NET a web** pracovního vytížení.

   2.&nbsp;nainstalujte nejnovější [Blazor rozšíření](https://go.microsoft.com/fwlink/?linkid=870389) z webu Visual Studio Marketplace. Tento krok zpřístupní Blazor šablony sady Visual Studio.

   3.&nbsp;povolit sady Visual Studio pro použití ve verzi preview sady SDK: Otevřít **nástroje** > **možnosti** v panelu nabídek. Otevřít **projekty a řešení** uzlu. Otevřít **.NET Core** kartu. Zaškrtněte políčko u **pomocí verze Preview sady .NET Core SDK**. Vyberte **OK**.

   4.&nbsp;vytvořte nový projekt.

   5.&nbsp;vyberte **webová aplikace ASP.NET Core**. Vyberte **Další**.

   6.&nbsp;zadejte název **název projektu** pole. Potvrďte **umístění** položka je správný, a zadejte umístění pro projekt. Vyberte **Vytvořit**.

   7.&nbsp;Ujistěte se, že **.NET Core** a **ASP.NET Core 3.0** jsou vybrány v horní části.

   8.&nbsp;prostředí Blazor na straně klienta, vyberte **Blazor (na straně klienta)** šablony. Pro prostředí Blazor na straně serveru, zvolte **Blazor (serverové)** šablony. Vyberte **Vytvořit**.

   9.&nbsp;stiskněte **F5** ke spuštění aplikace.

   # <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)
   
   1.&nbsp;nainstalovat [Visual Studio Code](https://code.visualstudio.com/).

   2.&nbsp;nainstalujte nejnovější [ C# pro Visual Studio Code příponou](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).

   3.&nbsp;prostředí Blazor na straně klienta, spusťte následující příkaz z příkazového prostředí:

      ```console
      dotnet new blazor -o WebApplication1
      ```

      Pro prostředí Blazor na straně serveru spusťte následující příkaz z příkazového prostředí:

      ```console
      dotnet new blazorserverside -o WebApplication1
      ```

      > [!NOTE]
      > V systému macOS v technologii ASP.NET Core 3.0 ve verzi Preview 4 se podporuje jenom Blazor straně klienta. Další informace najdete v tématu [Blazor na straně serveru: dotnet spustit selže s InvalidOperationException v systému MacOS](https://github.com/aspnet/AspNetCore/issues/9402).

   4.&nbsp;otevřít *WebApplication1* složky ve Visual Studio Code.

   5.&nbsp;po zobrazení výzvy Visual Studio Code pro projekt Blazor na straně serveru, chcete-li přidat požadované prostředky pro sestavení a ladění projektu, vyberte **Ano**.

   6.&nbsp;při použití aplikace na straně serveru Blazor aplikaci spustíme s použitím ladicího programu Visual Studio Code. Pokud používáte aplikaci na straně klienta Blazor, spusťte `dotnet run` ze složky projektu aplikace.

   <!--

   # [Visual Studio for Mac](#tab/visual-studio-mac)

   1.&nbsp;Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/). Switch the [Update channel to Preview](/visualstudio/mac/install-preview).

   2.&nbsp;Select **File** > **New Solution** or **New Project**.

   3.&nbsp;In the sidebar, select **.NET Core** > **App**.

   4.&nbsp;For an experience with Blazor server-side, select the **ASP.NET Core Blazor (server-side)** template. For an experience with Blazor server-side, select the **ASP.NET Core Blazor (client-side)** template. Select **Next**.

   5.&nbsp;The **Target Framework** defaults to **.NET Core 3.0**. Select **Next**.

   6.&nbsp;In the **Project Name** field, enter `WebApplication1`. Select **Create**.

   7.&nbsp;Select **Run** > **Run Without Debugging** to run the app *without the debugger*. Running with the debugger isn't supported at this time.

   -->

   # <a name="net-core-clitabnetcore-cli"></a>[Rozhraní příkazového řádku .NET Core](#tab/netcore-cli/)

   Pro prostředí Blazor na straně klienta spusťte následující příkazy z příkazového prostředí:

   ```console
   dotnet new blazor -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   Pro prostředí Blazor na straně serveru spusťte následující příkazy z příkazového prostředí:

   ```console
   dotnet new blazorserverside -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   > [!NOTE]
   > V systému macOS použití Blazor aplikace na straně klienta. Blazor na straně serveru se nepodporuje pro macOS na technologii ASP.NET Core 3.0 ve verzi Preview 4. Další informace najdete v tématu [Blazor na straně serveru: dotnet spustit selže s InvalidOperationException v systému MacOS](https://github.com/aspnet/AspNetCore/issues/9402).

   ---

V prohlížeči přejděte na `https://localhost:5001`.

Více stránek jsou k dispozici z karty na bočním panelu:

* Domů
* Čítač
* Načtení dat

Na stránce čítače, vyberte **klikněte na mě** tlačítka se zvýší čítač bez aktualizace stránky. Zvyšování hodnoty čítače na webové stránce obvykle vyžaduje zadání jazyka JavaScript, ale součásti syntaxe Razor poskytují lepší přístup pomocí C#.

*Pages/Counter.razor*:

[!code-cshtml[](get-started/samples_snapshot/3.x/Counter1.razor)]

Žádost o `/counter` v prohlížeči, jak jsou určené `@page` – direktiva v horní části stránky, způsobí, že součást čítače pro vykreslení jeho obsah. Komponenty vykreslování do reprezentaci v paměti, který lze použít k aktualizaci uživatelského rozhraní v flexibilní a efektivní způsob vykreslení stromu.

Pokaždé, když **klikněte na mě** výběru tlačítka:

* `onclick` Událost se aktivuje.
* `IncrementCount` Metoda je volána.
* `currentCount` Se zvýší.
* Komponenta se znovu vykreslí.

Modul runtime porovnává nový obsah na předchozí obsah a platí pouze změněný obsah do modelu Document Object Model (DOM).

Přidáte součást do jiné součásti pomocí syntaxe HTML. Komponenta parametry jsou určeny pomocí atributů nebo podřízený obsah. Například můžete přidat součást čítače na domovskou stránku aplikace tak, že přidáte `<Counter />` – element pro součást indexu.

*Pages/Index.razor*:

[!code-cshtml[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

Spusťte aplikaci. Na domovskou stránku má svůj vlastní čítače.

Přidání parametru do komponenty čítače, aktualizovat součásti `@functions` blok:

* Přidání vlastnosti pro `IncrementAmount` dekorován `[Parameter]` atribut.
* Změnit `IncrementCount` metoda se má použít `IncrementAmount` při zvýšit hodnotu `currentCount`.

*Pages/Counter.razor*:

[!code-cshtml[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=4-5,9)]

Zadejte `IncrementAmount` parametr v komponentě domovské `<Counter>` pomocí atributu element.

*Pages/Index.razor*:

[!code-cshtml[](get-started/samples_snapshot/3.x/Index2.razor)]

Spusťte aplikaci. Na domovskou stránku má svůj vlastní čítač, který zvýší o 10 pokaždé, když **klikněte na mě** výběru tlačítka.

## <a name="next-steps"></a>Další kroky

<xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a>Další zdroje

* <xref:signalr/introduction>
