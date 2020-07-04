---
title: Nástroje pro ASP.NET CoreBlazor
author: guardrex
description: Přečtěte si informace o nástrojích dostupných pro sestavování Blazor aplikací.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/02/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/tooling
zone_pivot_groups: operating-systems-set-one
ms.openlocfilehash: 538257597ec5c6c705d8280a817c409debe22224
ms.sourcegitcommit: c6467f86c09b1f608b09d37d33c8c43de7ae8bc7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2020
ms.locfileid: "85946815"
---
# <a name="tooling-for-aspnet-core-blazor"></a>Nástroje pro ASP.NET CoreBlazor

Od [Daniel Skořepa](https://github.com/danroth27) a [Luke Latham](https://github.com/guardrex)

::: zone pivot="os-windows"

1. Nainstalujte nejnovější verzi sady [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) s úlohou **vývoje ASP.NET a webu** .

1. Vytvoření nového projektu

1. Vyberte možnost ** Blazor aplikace**. Vyberte **Další**.

1. Do pole **název projektu** zadejte název projektu nebo přijměte výchozí název projektu. Potvrďte správnost záznamu **umístění** nebo zadejte umístění projektu. Vyberte **Vytvořit**.

1. Pro Blazor WebAssembly prostředí vyberte šablonu ** Blazor WebAssembly aplikace** . Pro Blazor Server prostředí vyberte šablonu ** Blazor Server aplikace** . Vyberte **Vytvořit**.

   Informace o těchto dvou Blazor modelech hostování *Blazor WebAssembly* a naleznete *Blazor Server* v tématu <xref:blazor/hosting-models> .

1. Spusťte aplikaci stisknutím klávesy <kbd>CTRL</kbd> + <kbd>F5</kbd> .

::: zone-end

::: zone pivot="os-linux"

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

::: zone-end

::: zone pivot="os-macos"

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

::: zone-end
