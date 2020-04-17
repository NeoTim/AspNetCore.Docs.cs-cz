---
title: Ladění ASP.NET Blazor základní webové sestavy
author: guardrex
description: Přečtěte si, Blazor jak ladit aplikace.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/16/2020
no-loc:
- Blazor
- SignalR
uid: blazor/debug
ms.openlocfilehash: 8b63444ba5c8cd45e64e722c8978ba4e6d90af36
ms.sourcegitcommit: 77c046331f3d633d7cc247ba77e58b89e254f487
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81488745"
---
# <a name="debug-aspnet-core-opno-locblazor-webassembly"></a>Ladění ASP.NET Blazor základní webové sestavy

[Daniel Roth](https://github.com/danroth27)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

BlazorAplikace WebAssembly lze ladit pomocí nástrojů pro vývoj prohlížeče v prohlížečích založených na chromu (Edge/Chrome).  Případně můžete ladit aplikaci pomocí Visual Studia nebo Visual Studio Code.

Mezi dostupné scénáře patří:

* Nastavte a odeberte zarážky.
* Spusťte aplikaci s podporou ladění v sadě Visual Studio a Visual Studio Code (podpora<kbd>F5).</kbd>
* Jeden krok (<kbd>F10</kbd>) prostřednictvím kódu.
* Pokračovat spuštění kódu s <kbd>F8</kbd> v prohlížeči nebo <kbd>F5</kbd> v sadě Visual Studio nebo Visual Studio Code.
* V *locals* zobrazení, dodržovat hodnoty místních proměnných.
* Podívejte se na zásobník volání, včetně řetězců volání, které přejdou z JavaScriptu do rozhraní .NET a z rozhraní .NET do Jazyka JavaScript.

Pro tuto chvíli, *nemůžete:*

* Zkontrolujte pole.
* Najeďte na členy.
* Krok ladění do nebo ze spravovaného kódu.
* Mít plnou podporu pro kontrolu typů hodnot.
* Přerušit na neošetřené výjimky.
* Při spuštění aplikace byly zarážky přístupů.
* Ladění aplikace s pracovníkem služby.

Budeme i nadále zlepšovat ladění v nadcházejících verzích.

## <a name="prerequisites"></a>Požadavky

Ladění vyžaduje některý z následujících prohlížečů:

* Microsoft Edge (verze 80 nebo novější)
* Google Chrome (verze 70 nebo novější)

## <a name="enable-debugging-for-visual-studio-and-visual-studio-code"></a>Povolení ladění pro visual studio a kód sady Visual Studio

Ladění je povoleno automaticky pro nové projekty, které jsou vytvořeny pomocí Blazor ASP.NET Core 3.2 Preview 3 nebo novější šablona projektu WebAssembly[(aktuální verze je 3.2 Náhled 4](xref:blazor/get-started)).

Chcete-li povolit ladění Blazor pro existující aplikaci WebAssembly, aktualizujte soubor *launchSettings.json* v projektu spuštění tak, aby do každého profilu spuštění zahrnul následující `inspectUri` vlastnosti:

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/_framework/debug/ws-proxy?browser={browserInspectUri}"
```

Po aktualizaci by měl soubor *launchSettings.json* vypadat podobně jako v následujícím příkladu:

[!code-json[](debug/launchSettings.json?highlight=14,22)]

Nemovitost: `inspectUri`

* Umožňuje ide zjistit, že aplikace Blazor je aplikace WebAssembly.
* Instruuje infrastrukturu ladění skriptů, aby Blazorse připojila k prohlížeči prostřednictvím proxy serveru pro ladění programu .

## <a name="visual-studio"></a>Visual Studio

Ladění aplikace Blazor WebAssembly v Sadě Visual Studio:

1. Ujistěte se, že jste [nainstalovali nejnovější verzi Preview Visual Studia 2019 16.6](https://visualstudio.com/preview) (náhled 2 nebo novější).
1. Vytvořte novou aplikaci Blazor ASP.NET hostovaho webu Core.
1. Stisknutím <kbd>klávesy F5</kbd> spusťte aplikaci v ladicím programu.
1. Nastavte zarážku v *Counter.razor* v metodě. `IncrementCount`
1. Přejděte na kartu **Čítadlo** a vyberte tlačítko pro stisknutí zarážky:

   ![Čítač ladění](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-counter.png)

1. Podívejte se na `currentCount` hodnotu pole v místním okně:

   ![Zobrazit místní obyvatele](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-locals.png)

1. Chcete-li pokračovat v provádění, stiskněte klávesu <kbd>F5.</kbd>

Při ladění aplikace Blazor WebAssembly můžete také ladit kód serveru:

1. Nastavte zarážku na stránce *FetchData.razor* v . `OnInitializedAsync`
1. Nastavte zarážku `WeatherForecastController` v `Get` metodě akce.
1. Přejděte na kartu **Načíst data** a `FetchData` klikněte na první zarážku v komponentě těsně před tím, než server vydá požadavek HTTP:

   ![Ladění dat načtení](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-fetch-data.png)

1. Stisknutím <kbd>klávesy F5</kbd> pokračujte v provádění a `WeatherForecastController`poté klepněte na zarážku na serveru v :

   ![Ladicí server](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-server.png)

1. Dalším stisknutím <kbd>klávesy F5</kbd> umožníte pokračování provedení a zobrazí se vykreslená tabulka předpovědi počasí.

## <a name="visual-studio-code"></a>Visual Studio Code

Ladění aplikace Blazor WebAssembly v kódu Sady Visual Studio:
 
1. Nainstalujte [rozšíření C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) a rozšíření [debuggeru JavaScriptu (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) s `debug.javascript.usePreview` nastavenou na `true`.

   ![Rozšíření](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-extensions.png)

   ![Ladicí program náhledu JS](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-js-use-preview.png)

1. Otevřete Blazor existující aplikaci WebAssembly s povoleným laděním.

   * Pokud se zobrazí následující oznámení, že k povolení ladění je nutné provést další nastavení, zkontrolujte, zda máte nainstalovaná správná rozšíření a je povoleno ladění náhledu JavaScriptu, a potom okno znovu načtěte:

     ![Další nastavení requried](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-additional-setup.png)

   * Oznámení nabízí přidat požadované prostředky do aplikace pro vytváření a ladění. Vyberte **Ano**:

     ![Přidání požadovaných datových zdrojů](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-required-assets.png)

1. Spuštění aplikace v ladicím programu je dvoustupňový proces:

   1\. **Nejprve**spusťte aplikaci pomocí konfigurace spuštění **.NET Core Launch (Standalone).Blazor **

   2\. **Po spuštění aplikace**spusťte prohlížeč pomocí konfigurace spuštění **.NET Core Blazor Debug Web Assembly v Chromu** (vyžaduje Chrome). Chcete-li místo Chromu `type` používat Edge, změňte konfiguraci spuštění v *souboru .vscode/launch.json* z `pwa-chrome` na . `pwa-msedge`

1. Nastavte zarážku `IncrementCount` v `Counter` metodě v komponentě a pak vyberte tlačítko pro stisknutí zarážky:

   ![Čítač ladění v kódu VS](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-debug-counter.png)

## <a name="debug-in-the-browser"></a>Ladění v prohlížeči

1. Spusťte sestavení ladění aplikace ve vývojovém prostředí.

1. Stiskněte <kbd>klávesu Shift</kbd>+<kbd>Alt</kbd>+<kbd>D</kbd>.

1. Prohlížeč musí být spuštěn s povoleným vzdáleným laděním. Pokud je zakázáno vzdálené ladění, je generována chybová stránka nelze **najít ladnou kartu prohlížeče.** Chybová stránka obsahuje pokyny pro spuštění prohlížeče s otevřeným ladicím portem, aby se Blazor ladicí proxy server mohl připojit k aplikaci. *Zavřete všechny instance prohlížeče* a restartujte prohlížeč podle pokynů.

Jakmile je prohlížeč spuštěn s povoleným vzdáleným laděním, ladicí klávesová zkratka otevře novou kartu ladicího programu. Po chvíli se na kartě **Zdroje** zobrazí seznam sestavení .NET v aplikaci. Rozbalte každé sestavení a najděte zdrojové soubory *.cs*/*.razor,* které jsou k dispozici pro ladění. Nastavte zarážky, přepněte zpět na kartu aplikace a zarážky jsou přístupů při spuštění kódu. Po zásahu zarážky, jeden krok (<kbd>F10</kbd>) prostřednictvím kódu nebo pokračovat<kbd>(F8)</kbd>spuštění kódu normálně.

BlazorPoskytuje ladicí proxy server, který implementuje [protokol Chrome DevTools](https://chromedevtools.github.io/devtools-protocol/) a rozšiřuje protokol o soubor . NET-specifické informace. Při ladění klávesové zkratky Blazor najede Chrome DevTools na proxy server. Proxy se připojí k oknu prohlížeče, které se snažíte ladit (proto je třeba povolit vzdálené ladění).

## <a name="browser-source-maps"></a>Mapy zdroje prohlížeče

Mapy zdroje prohlížeče umožňují prohlížeči mapovat zkompilované soubory zpět na původní zdrojové soubory a běžně se používají pro ladění na straně klienta. Vsoučasné Blazor době však není mapovat C# přímo do JavaScriptu / WASM. Místo Blazor toho il interpretace v prohlížeči, takže zdrojové mapy nejsou relevantní.

## <a name="troubleshoot"></a>Řešení potíží

Pokud dochází k chybám, může vám pomoci následující tip:

Na kartě **Debugger** otevřete vývojářské nástroje v prohlížeči. V konzole, `localStorage.clear()` spusťte odebrat všechny zarážky.
