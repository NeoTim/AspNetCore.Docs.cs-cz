---
title: 'ASP.NET Core ladění:::no-loc(Blazor WebAssembly):::'
author: guardrex
description: 'Naučte se ladit :::no-loc(Blazor)::: aplikace.'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/15/2020
no-loc:
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: blazor/debug
ms.openlocfilehash: 14943b9f7847ac9144addfdf16a003f6fc8c340c
ms.sourcegitcommit: cc845634a490c49ff869c89b6e422b6d65d0e886
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/24/2020
ms.locfileid: "87159700"
---
# <a name="debug-aspnet-core-no-locblazor-webassembly"></a>ASP.NET Core ladění:::no-loc(Blazor WebAssembly):::

[Daniel Skořepa](https://github.com/danroth27)

:::no-loc(Blazor WebAssembly):::aplikace se dají ladit pomocí vývojářských nástrojů pro vývoj v prohlížečích založených na chromu (Edge/Chrome). Alternativně můžete aplikaci ladit pomocí sady Visual Studio nebo Visual Studio Code.

K dispozici jsou tyto scénáře:

* Nastavení a odebrání zarážek.
* Spusťte aplikaci s podporou ladění v aplikaci Visual Studio a Visual Studio Code (podpora<kbd>F5</kbd> ).
* Jednoduchý krok (<kbd>F10</kbd>) prostřednictvím kódu.
* Pokračuje v provádění kódu pomocí <kbd>F8</kbd> v prohlížeči nebo <kbd>F5</kbd> v aplikaci Visual Studio nebo Visual Studio Code.
* V zobrazení *místních* hodnot Sledujte hodnoty místních proměnných.
* Podívejte se do zásobníku volání, včetně řetězů volání, které přecházejí z JavaScriptu do .NET a z .NET do JavaScriptu.

Teď *nemůžete*:

* Přerušit při neošetřených výjimkách.
* Při spuštění aplikace se zarážky volání.

V nadcházejících vydáních budeme dál zlepšovat možnosti ladění.

## <a name="prerequisites"></a>Předpoklady

Ladění vyžaduje některý z následujících prohlížečů:

* Google Chrome (verze 70 nebo novější) (výchozí)
* Microsoft Edge (verze 80 nebo novější)

## <a name="enable-debugging-for-visual-studio-and-visual-studio-code"></a>Povolit ladění pro Visual Studio a Visual Studio Code

Chcete-li povolit ladění pro existující :::no-loc(Blazor WebAssembly)::: aplikaci, aktualizujte `launchSettings.json` soubor v spouštěném projektu tak, aby do `inspectUri` každého spouštěcího profilu zahrnoval následující vlastnost:

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/_framework/debug/ws-proxy?browser={browserInspectUri}"
```

Po aktualizaci `launchSettings.json` by měl soubor vypadat podobně jako v následujícím příkladu:

[!code-json[](debug/launchSettings.json?highlight=14,22)]

`inspectUri`Vlastnost:

* Umožňuje rozhraní IDE zjistit, že aplikace je :::no-loc(Blazor WebAssembly)::: aplikace.
* Dá pokyn k tomu, aby se infrastruktura ladění skriptů připojovala k prohlížeči prostřednictvím :::no-loc(Blazor)::: ladicího proxy serveru.

Zástupný text pro protokol WebSockets ( `wsProtocol` ), hostitele ( `url.hostname` ), port ( `url.port` ) a identifikátor URI pro inspektor v spuštěném prohlížeči ( `browserInspectUri` ) jsou poskytovány rozhraním.

## <a name="visual-studio"></a>Visual Studio

Ladění :::no-loc(Blazor WebAssembly)::: aplikace v aplikaci Visual Studio:

1. Vytvořte novou ASP.NET Core hostovanou :::no-loc(Blazor WebAssembly)::: aplikaci.
1. Stisknutím klávesy <kbd>F5</kbd> spusťte aplikaci v ladicím programu.
1. Nastavte zarážku v `Pages/Counter.razor` `IncrementCount` metodě.
1. Přejděte na **`Counter`** kartu a vyberte tlačítko, kde se má zarážka opakovat:

   ![Čítač ladění](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-counter.png)

1. Podívejte se na hodnotu `currentCount` pole v okně místní hodnoty:

   ![Zobrazit místní hodnoty](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-locals.png)

1. Pokračujte v provádění stisknutím klávesy <kbd>F5</kbd> .

Při ladění :::no-loc(Blazor WebAssembly)::: aplikace můžete také ladit kód serveru:

1. Nastavte zarážku na `Pages/FetchData.razor` stránce v <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> .
1. Nastavte zarážku v `WeatherForecastController` `Get` metodě Action.
1. Přejděte na **`Fetch Data`** kartu a vyberte první zarážku v `FetchData` součásti těsně předtím, než VYDÁ požadavek HTTP na server:

   ![Ladit načtení dat](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-fetch-data.png)

1. Stisknutím klávesy <kbd>F5</kbd> pokračujte v provádění a potom stiskněte zarážku na serveru v `WeatherForecastController` :

   ![Server ladění](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-server.png)

1. Stiskněte znovu <kbd>F5</kbd> , aby bylo možné pokračovat v provádění, a podívejte se na vykreslenou tabulku předpovědi počasí.

<a id="vscode"></a>

## <a name="visual-studio-code"></a>Visual Studio Code

### <a name="debug-standalone-no-locblazor-webassembly"></a>Samostatně ladit:::no-loc(Blazor WebAssembly):::

1. Otevřete samostatnou :::no-loc(Blazor WebAssembly)::: aplikaci v vs Code.

   Pokud se zobrazí následující oznámení, že pro povolení ladění je potřeba další nastavení:
   
   * Potvrďte, že máte nainstalované správné rozšíření.
   * Potvrďte, že je povolené ladění v jazyce JavaScript verze Preview.
   * Znovu načtěte okno.

   ![Vyžaduje se další instalace.](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-additional-setup.png)

1. Spusťte ladění pomocí klávesových zkratek <kbd>F5</kbd> nebo položky nabídky.

1. Po zobrazení výzvy vyberte možnost ** :::no-loc(Blazor WebAssembly)::: ladění** pro spuštění ladění.

   ![Seznam dostupných možností ladění](index/_static/blazor-vscode-debugtypes.png)

1. Spustí se samostatná aplikace a otevře se prohlížeč ladění.

1. Nastavte zarážku v `IncrementCount` metodě v `Counter` součásti a potom vyberte tlačítko, kterým se má zarážka opakovat:

   ![Čítač ladění v VS Code](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-debug-counter.png)

### <a name="debug-hosted-no-locblazor-webassembly"></a>Ladění je hostované:::no-loc(Blazor WebAssembly):::

1. Otevřete :::no-loc(Blazor WebAssembly)::: složku řešení hostované aplikace v vs Code.

1. Pokud pro projekt neexistuje konfigurační sada pro spuštění, zobrazí se následující oznámení. Vyberte **Ano**.

   ![Přidat požadované prostředky](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-required-assets.png)

1. V paletě příkazů v horní části okna vyberte *serverový* projekt v hostovaném řešení.

`launch.json`Soubor se vygeneruje s konfigurací spuštění pro spuštění ladicího programu.

### <a name="attach-to-an-existing-debugging-session"></a>Připojit k existující relaci ladění

Pokud se chcete připojit ke spuštěné :::no-loc(Blazor)::: aplikaci, vytvořte `launch.json` soubor s následující konfigurací:

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach to Existing :::no-loc(Blazor WebAssembly)::: Application"
}
```

> [!NOTE]
> Připojení k relaci ladění se podporuje jenom pro samostatné aplikace. Chcete-li použít ladění v plném zásobníku, musíte aplikaci spustit z VS Code.

### <a name="launch-configuration-options"></a>Spustit možnosti konfigurace

Pro `blazorwasm` typ ladění () jsou podporovány následující možnosti konfigurace spuštění `.vscode/launch.json` .

| Možnost    | Popis |
| --------- | ----------- |
| `request` | Pomocí `launch` můžete spustit a připojit relaci ladění k :::no-loc(Blazor WebAssembly)::: aplikaci nebo `attach` připojit relaci ladění k již spuštěné aplikaci. |
| `url`     | Adresa URL, která se má otevřít v prohlížeči při ladění. Výchozí hodnota je `https://localhost:5001` . |
| `browser` | Prohlížeč, který se má spustit pro relaci ladění. Nastavte na `edge` nebo `chrome`. Výchozí hodnota je `chrome` . |
| `trace`   | Slouží ke generování protokolů z ladicího programu JS. Nastavte na `true` Generovat protokoly. |
| `hosted`  | Musí být nastavené na hodnotu `true` při spuštění a ladění hostované :::no-loc(Blazor WebAssembly)::: aplikace. |
| `webRoot` | Určuje absolutní cestu webového serveru. By měla být nastavena, pokud je aplikace obsluhována z dílčího směrování. |
| `timeout` | Počet milisekund, po které se má čekat na připojení relace ladění Výchozí hodnota je 30 000 milisekund (30 sekund). |
| `program` | Odkaz na spustitelný soubor pro spuštění serveru hostované aplikace. Musí být nastaven `hosted` , pokud je `true` . |
| `cwd`     | Pracovní adresář, ve kterém se má spustit aplikace Musí být nastaven `hosted` , pokud je `true` . |
| `env`     | Proměnné prostředí, které se mají poskytnout spuštěnému procesu. Platí pouze v případě `hosted` , že je nastavena na `true` . |

### <a name="example-launch-configurations"></a>Příklad konfigurací spuštění

#### <a name="launch-and-debug-a-standalone-no-locblazor-webassembly-app"></a>Spuštění a ladění samostatné :::no-loc(Blazor WebAssembly)::: aplikace

```json
{
  "type": "blazorwasm",
  "request": "launch",
  "name": "Launch and Debug"
}
```

#### <a name="attach-to-a-running-app-at-a-specified-url"></a>Připojit se k běžící aplikaci v zadané adrese URL

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach and Debug",
  "url": "http://localhost:5000"
}
```

#### <a name="launch-and-debug-a-hosted-no-locblazor-webassembly-app-with-microsoft-edge"></a>Spuštění a ladění hostované :::no-loc(Blazor WebAssembly)::: aplikace pomocí Microsoft Edge

Konfigurace prohlížeče je ve výchozím nastavení Google Chrome. Při použití Microsoft Edge pro ladění nastavte `browser` na `edge` . Pokud chcete používat Google Chrome, buď nenastavujte `browser` možnost, nebo nastavte hodnotu možnosti na `chrome` .

```json
{
  "name": "Launch and Debug Hosted :::no-loc(Blazor WebAssembly)::: App",
  "type": "blazorwasm",
  "request": "launch",
  "hosted": true,
  "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/MyHostedApp.Server.dll",
  "cwd": "${workspaceFolder}/Server",
  "browser": "edge"
}
```

V předchozím příkladu `MyHostedApp.Server.dll` je sestavení *serverové* aplikace. `.vscode`Složka se nachází ve složce řešení vedle `Client` `Server` složek, a `Shared` .

## <a name="debug-in-the-browser"></a>Ladit v prohlížeči

1. Spusťte ladicí sestavení aplikace ve vývojovém prostředí.

1. Spusťte prohlížeč a přejděte na adresu URL aplikace (například `https://localhost:5001` ).

1. V prohlížeči se pokuste zahájit vzdálené ladění stisknutím klávesy <kbd>SHIFT</kbd> + <kbd>ALT</kbd> + <kbd>D</kbd>.

   Prohlížeč musí běžet se zapnutým vzdáleným laděním, což není výchozí nastavení. Pokud je vzdálené ladění zakázané, zobrazí se stránka s informací o tom, že se pro spuštění prohlížeče s otevřeným portem ladění **nedaří najít** chybovou stránku karty prohlížeče. Postupujte podle pokynů pro prohlížeč a otevřete nové okno prohlížeče. Zavřete předchozí okno prohlížeče.

1. Po spuštění prohlížeče se zapnutým vzdáleným laděním se otevře klávesová zkratka (<kbd>SHIFT</kbd> + <kbd>ALT</kbd> + <kbd>D</kbd>) s novou kartou ladicího programu.

1. Po chvíli se na kartě **zdroje** zobrazí seznam sestavení .NET aplikace v rámci `file://` uzlu.

1. V kódu komponenty ( `.razor` soubory) a souborech kódu jazyka C# ( `.cs` ) jsou zarážky, které jste nastavili, zasaženy při spuštění kódu. Po stisknutí zarážky se provede krok (<kbd>F10</kbd>) prostřednictvím kódu nebo obnovení kódu (<kbd>F8</kbd>) normálně.

:::no-loc(Blazor):::poskytuje ladicí proxy, který implementuje [protokol Chrome DevTools](https://chromedevtools.github.io/devtools-protocol/) a rozšiřuje protokol pomocí. Informace specifické pro síť. Když se stiskne klávesová zkratka ladění, navede :::no-loc(Blazor)::: devtools Chrome na proxy serveru. Proxy server se připojí k oknu prohlížeče, které se pokoušíte ladit (takže je potřeba povolit vzdálené ladění).

## <a name="browser-source-maps"></a>Mapy zdroje prohlížeče

Mapování zdrojového kódu prohlížeče umožňují prohlížeči mapovat zkompilované soubory zpátky na původní zdrojové soubory a často se používají pro ladění na straně klienta. V :::no-loc(Blazor)::: současné době ale nemapuje C# přímo na JavaScript/WASM. Místo toho :::no-loc(Blazor)::: provede výklad il v prohlížeči, takže zdrojové mapy nejsou relevantní.

## <a name="troubleshoot"></a>Řešení potíží

Pokud dochází k chybám, může vám pomáhat následující tipy:

* Na kartě **ladicí program** otevřete nástroje pro vývojáře v prohlížeči. V konzole nástroje spusťte příkaz `localStorage.clear()` k odebrání všech zarážek.
* Potvrďte, že jste nainstalovali a důvěřujete ASP.NET Core certifikát pro vývoj HTTPS. Další informace naleznete v tématu <xref:security/enforcing-ssl#troubleshoot-certificate-problems>.
* Visual Studio vyžaduje možnost **Povolit ladění JavaScriptu pro ASP.NET (Chrome, Edge a IE)** v možnostech **nástrojů**  >  **Options**  >  **Debugging**  >  **Obecné**ladění. Toto je výchozí nastavení pro Visual Studio. Pokud ladění nefunguje, potvrďte, že je vybraná možnost.
