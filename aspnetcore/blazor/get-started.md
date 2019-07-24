---
title: Začínáme s ASP.NET Core Blazor
author: guardrex
description: Začněte s Blazor vytvořením aplikace Blazor pomocí nástrojů podle vašeho výběru.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 07/23/2019
uid: blazor/get-started
ms.openlocfilehash: 9ebe1e2d36f96bfa214c2389535c28db5db5020a
ms.sourcegitcommit: f30b18442ed12831c7e86b0db249183ccd749f59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2019
ms.locfileid: "68412410"
---
# <a name="get-started-with-aspnet-core-blazor"></a>Začínáme s ASP.NET Core Blazor

Od [Daniel Skořepa](https://github.com/danroth27) a [Luke Latham](https://github.com/guardrex)

Začínáme s Blazor:

1. Nainstalujte nejnovější verzi [sady .NET Core 3,0 Preview SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0) .

1. Nainstalujte šablony Blazor spuštěním následujícího příkazu v příkazovém prostředí:

   ```console
   dotnet new -i Microsoft.AspNetCore.Blazor.Templates::3.0.0-preview7.19365.7
   ```

1. Postupujte podle pokynů pro výběr nástrojů:

   # <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

   1\. Nainstalujte si nejnovější verzi sady [Visual Studio Preview](https://visualstudio.com/vs/preview) s využitím úlohy **vývoje ASP.NET a webu** .

   2\. Nainstalujte nejnovější [rozšíření Blazor](https://go.microsoft.com/fwlink/?linkid=870389) z Visual Studio Marketplace. Tento krok zpřístupňuje šablony Blazor k dispozici pro Visual Studio.

   3\. Vytvořte nový projekt.

   4\. Vyberte **webová aplikace ASP.NET Core**. Vyberte **Další**.

   5\. Do pole **název projektu** zadejte název projektu nebo přijměte výchozí název projektu. Potvrďte správnost záznamu **umístění** nebo zadejte umístění projektu. Vyberte **Vytvořit**.

   6\. V dialogovém okně **vytvořit novou webovou aplikaci ASP.NET Core** potvrďte, že je vybrána možnost **.net Core** a **ASP.NET Core 3,0** .

   7\. Pro Blazor prostředí na straně klienta vyberte šablonu **aplikace Blazor WebAssembly** . Pro Blazor prostředí na straně serveru vyberte šablonu **aplikace serveru Blazor** . Vyberte **Vytvořit**. Informace o dvou modelech hostování Blazor, na straně serveru a na straně klienta, najdete v <xref:blazor/hosting-models>tématu.

   8\. Stisknutím klávesy **F5** spusťte aplikaci.

   # <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

   1\. Nainstalujte [Visual Studio Code](https://code.visualstudio.com/).

   2\. Nainstalujte nejnovější verzi [ C# pro Visual Studio Code rozšíření](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).

   3\. Pro Blazor prostředí na straně klienta spusťte v příkazovém prostředí následující příkaz:

      ```console
      dotnet new blazor -o WebApplication1
      ```

      Pro Blazor prostředí na straně serveru spusťte v příkazovém prostředí následující příkaz:

      ```console
      dotnet new blazorserverside -o WebApplication1
      ```

      Informace o dvou modelech hostování Blazor, na straně serveru a na straně klienta, najdete v <xref:blazor/hosting-models>tématu.

   4\. Otevřete složku *WebApplication1* v Visual Studio Code.

   5\. Pro Blazor projekt na straně serveru rozhraní IDE požaduje, abyste přidali prostředky pro sestavení a ladění projektu. Vyberte **Ano**.

   6\. Pokud používáte aplikaci Blazor na straně serveru, spusťte aplikaci pomocí ladicího programu Visual Studio Code. Pokud používáte aplikaci Blazor na straně klienta, spusťte `dotnet run` ji ze složky projektu aplikace.

   7\. V prohlížeči přejděte na `https://localhost:5001`.

   <!--

   # [Visual Studio for Mac](#tab/visual-studio-mac)

   1\. Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/). Switch the [Update channel to Preview](/visualstudio/mac/install-preview).

   2\. Select **File** > **New Solution** or **New Project**.

   3\. In the sidebar, select **.NET Core** > **App**.

   4\. For a Blazor server-side experience, select the **ASP.NET Core Blazor Server App** template. For a Blazor client-side experience, select the **ASP.NET Core Blazor WebAssembly App** template. Select **Next**. For information on the two Blazor hosting models, server-side and client-side, see <xref:blazor/hosting-models>.

   5\. The **Target Framework** defaults to **.NET Core 3.0**. Select **Next**.

   6\. In the **Project Name** field, enter `WebApplication1`. Select **Create**.

   7\. Select **Run** > **Run Without Debugging** to run the app *without the debugger*. Running with the debugger isn't supported at this time.

   -->

   # <a name="net-core-clitabnetcore-cli"></a>[Rozhraní příkazového řádku .NET Core](#tab/netcore-cli/)

   Pro Blazor prostředí na straně klienta spusťte v příkazovém prostředí následující příkazy:

   ```console
   dotnet new blazor -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   Pro Blazor prostředí na straně serveru spusťte v příkazovém prostředí následující příkazy:

   ```console
   dotnet new blazorserverside -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   Informace o dvou modelech hostování Blazor, na straně serveru a na straně klienta, najdete v <xref:blazor/hosting-models>tématu.

   V prohlížeči přejděte na `https://localhost:5001`.

   ---

Na kartách na bočním panelu je k dispozici více stránek:

* Domů
* Čítač
* Načíst data

Na stránce čítač můžete **kliknutím** na tlačítko pro zvýšit hodnotu čítače bez aktualizace stránky. Zvýšení čítače na webové stránce obvykle vyžaduje psaní JavaScriptu, ale komponenty Razor poskytují lepší přístup pomocí C#.

*Stránky/čítač. Razor*:

[!code-cshtml[](get-started/samples_snapshot/3.x/Counter1.razor?highlight=7,12-15)]

Požadavek `/counter` na v prohlížeči, jak je uveden `@page` v direktivě nahoře, způsobí, že `Counter` komponenta vykreslí svůj obsah. Komponenty vykreslí do reprezentace v paměti stromu vykreslování, který lze poté použít k flexibilnímu a efektivnímu způsobu aktualizace uživatelského rozhraní.

Pokaždé, když je vybráno tlačítko pro **kliknutí na tlačítko Další** :

* `onclick` Událost je aktivována.
* `IncrementCount` Metoda je volána.
* Dojde `currentCount` k zvýšení.
* Komponenta se znovu vykreslí.

Modul runtime porovná nový obsah s předchozím obsahem a na model DOM (Document Object Model) (DOM) použije pouze změněný obsah.

Přidejte komponentu do jiné komponenty pomocí syntaxe jazyka HTML. Například přidejte `Counter` komponentu do domovské stránky aplikace `<Counter />` přidáním elementu do `Index` komponenty.

*Pages/index. Razor*:

[!code-cshtml[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

Spusťte aplikaci. Domovská stránka má vlastní počítadlo poskytované `Counter` komponentou.

Parametry komponenty jsou zadány pomocí atributů nebo [podřízeného obsahu](xref:blazor/components#child-content), který umožňuje nastavit vlastnosti pro podřízenou komponentu. Chcete-li přidat parametr do `Counter` komponenty, aktualizujte `@code` blok komponenty:

* Přidejte vlastnost pro `IncrementAmount` `[Parameter]` s atributem.
* Změňte metodu na `IncrementAmount` použití`currentCount`při zvyšování hodnoty. `IncrementCount`

*Stránky/čítač. Razor*:

[!code-cshtml[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=12-13,17)]

`IncrementAmount` Zadejte velementu`<Counter>`komponentypomocíatributu. `Index`

*Pages/index. Razor*:

[!code-cshtml[](get-started/samples_snapshot/3.x/Index2.razor?highlight=7)]

Spusťte aplikaci. Komponenta má vlastní čítač, který se zvýší o deset pokaždé, když je vybráno tlačítko pro **kliknutí na tlačítko Další.** `Index` Komponenta `Counter` ( `/counter` *Counter. Razor*) se stále zvyšuje o jednu.

## <a name="next-steps"></a>Další postup

<xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a>Další zdroje

* <xref:signalr/introduction>
