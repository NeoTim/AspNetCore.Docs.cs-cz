---
title: Ladění ASP.NET Core Blazor sestavení
author: guardrex
description: Naučte se ladit aplikace Blazor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/26/2020
no-loc:
- Blazor
- SignalR
uid: blazor/debug
ms.openlocfilehash: 5dbc900ab68682068a7f9e3ffdaabef89a0c7798
ms.sourcegitcommit: 6ffb583991d6689326605a24565130083a28ef85
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "80306474"
---
# <a name="debug-aspnet-core-opno-locblazor-webassembly"></a>Ladění ASP.NET Core Blazor sestavení

[Daniel Skořepa](https://github.com/danroth27)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

aplikace Blazor WebAssembly je možné ladit pomocí nástrojů pro vývoj v prohlížeči v prohlížečích založených na chromu (Edge/Chrome).  Alternativně můžete aplikaci ladit pomocí sady Visual Studio nebo Visual Studio Code.

K dispozici jsou tyto scénáře:

* Nastavení a odebrání zarážek.
* Spusťte aplikaci s podporou ladění v aplikaci Visual Studio a Visual Studio Code (podpora<kbd>F5</kbd> ).
* Jednoduchý krok (<kbd>F10</kbd>) prostřednictvím kódu.
* Pokračuje v provádění kódu pomocí <kbd>F8</kbd> v prohlížeči nebo <kbd>F5</kbd> v aplikaci Visual Studio nebo Visual Studio Code.
* V zobrazení *místních* hodnot Sledujte hodnoty místních proměnných.
* Podívejte se do zásobníku volání, včetně řetězů volání, které přecházejí z JavaScriptu do .NET a z .NET do JavaScriptu.

Teď *nemůžete*:

* Zkontrolujte pole.
* Najeďte myší na kontrolu členů.
* Krok ladění do spravovaného kódu nebo z něj.
* Má plnou podporu pro kontrolu hodnotových typů.
* Přerušit při neošetřených výjimkách.
* Při spuštění aplikace se zarážky volání.
* Ladění aplikace pomocí pracovního procesu služby.

V nadcházejících vydáních budeme dál zlepšovat možnosti ladění.

## <a name="prerequisites"></a>Požadavky

Ladění vyžaduje některý z následujících prohlížečů:

* Microsoft Edge (verze 80 nebo novější)
* Google Chrome (verze 70 nebo novější)

## <a name="enable-debugging-for-visual-studio-and-visual-studio-code"></a>Povolit ladění pro Visual Studio a Visual Studio Code

Ladění je povoleno automaticky pro nové projekty, které jsou vytvořeny pomocí šablony projektu ASP.NET Core 3,2 Preview 3 nebo vyšší Blazor.

Chcete-li povolit ladění pro existující aplikaci Blazor WebAssembly, aktualizujte soubor *launchSettings. JSON* ve složce spouštěný projekt tak, aby do každého spouštěcího profilu zahrnoval následující vlastnost `inspectUri`:

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/_framework/debug/ws-proxy?browser={browserInspectUri}"
```

Po aktualizaci by soubor *launchSettings. JSON* měl vypadat podobně jako v následujícím příkladu:

[!code-json[](debug/launchSettings.json?highlight=14,22)]

Vlastnost `inspectUri`:

* Umožňuje rozhraní IDE zjistit, že aplikace je Blazor aplikace WebAssembly.
* Dá pokyn k tomu, aby se infrastruktura ladění skriptů připojovala k prohlížeči prostřednictvím proxy serveru pro ladění Blazor.

## <a name="visual-studio"></a>Visual Studio

Ladění aplikace Blazor WebAssembly v aplikaci Visual Studio:

1. Ujistěte se, že máte [nainstalovanou nejnovější verzi Preview sady Visual Studio 2019 16,6](https://visualstudio.com/preview) (Preview 2 nebo novější).
1. Vytvořte novou ASP.NET Core hostovanou Blazor aplikaci WebAssembly.
1. Stisknutím klávesy <kbd>F5</kbd> spusťte aplikaci v ladicím programu.
1. V metodě `IncrementCount` nastavte zarážku v *čítači. Razor* .
1. Přejděte na kartu **čítač** a vyberte tlačítko, kde se má zarážka opakovat:

   ![Čítač ladění](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-counter.png)

1. Podívejte se na hodnotu pole `currentCount` v okně místní hodnoty:

   ![Zobrazit místní hodnoty](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-locals.png)

1. Pokračujte v provádění stisknutím klávesy <kbd>F5</kbd> .

Při ladění aplikace Blazor WebAssembly můžete také ladit kód serveru:

1. Nastavte zarážku na stránce *FetchData. Razor* v `OnInitializedAsync`.
1. Nastavte zarážku v `WeatherForecastController` v metodě `Get` Action.
1. Přejděte na kartu **načíst data** , abyste narazili na první zarážku v součásti `FetchData` těsně předtím, než VYDÁ požadavek HTTP na server:

   ![Ladit načtení dat](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-fetch-data.png)

1. Stisknutím klávesy <kbd>F5</kbd> pokračujte v provádění a pak na serveru stiskněte zarážku na `WeatherForecastController`:

   ![Server ladění](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-server.png)

1. Stiskněte znovu <kbd>F5</kbd> , aby bylo možné pokračovat v provádění, a podívejte se na vykreslenou tabulku předpovědi počasí.

## <a name="visual-studio-code"></a>Visual Studio Code

Ladění aplikace Blazor WebAssembly v Visual Studio Code:
 
1. Nainstalujte rozšíření [a rozšíření ladicí program JavaScriptu (v noci) s `debug.javascript.usePreview` nastavenou na `true`. C# ](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) [JavaScript Debugger (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly)

   ![Rozšíření](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-extensions.png)

   ![JS Preview ladicího programu](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-js-use-preview.png)

1. Otevře existující aplikaci Blazor WebAssembly s povoleným laděním.

   * Pokud obdržíte následující oznámení, že pro povolení ladění je potřeba další nastavení, zkontrolujte, že máte nainstalované správné rozšíření a že je povolené ladění JavaScriptu ve verzi Preview, a pak znovu načtěte okno:

     ![Další požadovaným klíčovým slovům nastavení](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-additional-setup.png)

   * Oznámení nabízí přidání požadovaných prostředků do aplikace pro sestavování a ladění. Vyberte **Ano**:

     ![Přidat požadované prostředky](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-required-assets.png)

1. Spuštění aplikace v ladicím programu je proces se dvěma kroky:

   1\. **Nejdřív**spusťte aplikaci pomocí konfigurace spuštění **.net Core (Blazor Standalone)** .

   2\. **Po spuštění aplikace**spusťte prohlížeč pomocí **ladění .NET Core Blazor webové sestavení v** konfiguraci spouštění Chrome (vyžaduje Chrome). Pokud chcete místo Chromu použít Edge, změňte `type` konfigurace spuštění v *. VSCode/Launch. JSON* z `pwa-chrome` na `pwa-edge`.

1. Nastavte zarážku v metodě `IncrementCount` v součásti `Counter` a potom vyberte tlačítko, kterým se má zarážka opakovat:

   ![Čítač ladění v VS Code](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-debug-counter.png)

## <a name="debug-in-the-browser"></a>Ladit v prohlížeči

1. Spusťte ladicí sestavení aplikace ve vývojovém prostředí.

1. Stiskněte <kbd>Shift</kbd>+<kbd>ALT</kbd>+<kbd>D</kbd>.

1. Prohlížeč musí být spuštěn s povoleným vzdáleným laděním. Pokud je vzdálené ladění zakázané, vygeneruje se chybová stránka karty prohlížeče, která se **nedá najít** . Chybová stránka obsahuje pokyny pro spuštění prohlížeče s otevřeným portem pro ladění, aby se proxy Blazor ladicího programu mohl připojit k aplikaci. *Zavřete všechny instance prohlížeče* a restartujte prohlížeč podle pokynů.

Po spuštění prohlížeče se zapnutým vzdáleným laděním otevře klávesovou zkratku ladění novou kartu ladicího programu. Po chvíli se na kartě **zdroje** zobrazí seznam sestavení .NET v aplikaci. Rozbalte jednotlivá sestavení a vyhledejte soubory *. cs*/ *. Razor* , které jsou k dispozici pro ladění. Nastavte zarážky, přepněte zpět na kartu aplikace a zarážky se spustí při spuštění kódu. Po stisknutí zarážky se provede krok (<kbd>F10</kbd>) prostřednictvím kódu nebo obnovení kódu (<kbd>F8</kbd>) normálně.

Blazor poskytuje ladicí proxy, který implementuje [protokol Chrome DevTools](https://chromedevtools.github.io/devtools-protocol/) a rozšiřuje protokol pomocí. Informace specifické pro síť. Při Blazor stisknutí klávesových zkratek ladění DevTools odkazuje na rozhraní Chrome na proxy serveru. Proxy server se připojí k oknu prohlížeče, které se pokoušíte ladit (takže je potřeba povolit vzdálené ladění).

## <a name="browser-source-maps"></a>Mapy zdroje prohlížeče

Mapování zdrojového kódu prohlížeče umožňují prohlížeči mapovat zkompilované soubory zpátky na původní zdrojové soubory a často se používají pro ladění na straně klienta. Blazor ale v tuto chvíli není C# přímo namapována na JavaScript/WASM. Místo toho Blazor provede interpretaci IL v prohlížeči, takže zdrojová mapování nejsou relevantní.

## <a name="troubleshoot"></a>Řešení potíží

Pokud pracujete s chybami, může vám následující Tip:

Na kartě **ladicí program** otevřete nástroje pro vývojáře v prohlížeči. V konzole spusťte `localStorage.clear()` a odeberte všechny zarážky.
