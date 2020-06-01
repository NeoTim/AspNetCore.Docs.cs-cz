---
title: ' ladění ASP.NET Core Blazor WebAssembly ' Autor: guardrex Description: ' Naučte se ladit Blazor aplikace. '
monikerRange: ' >= aspnetcore-3,1 ' MS. Author: Riande MS. Custom: MVC MS. Date: 05/31/2020 No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID: blazor/Debug

---
# <a name="debug-aspnet-core-blazor-webassembly"></a>Ladění ASP.NET Core Blazor WebAssembly

[Daniel Skořepa](https://github.com/danroth27)

BlazorAplikace WebAssembly se dají ladit pomocí nástrojů pro vývoj v prohlížeči v prohlížečích založených na chromu (Edge/Chrome). Alternativně můžete aplikaci ladit pomocí sady Visual Studio nebo Visual Studio Code.

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

## <a name="prerequisites"></a>Požadavky

Ladění vyžaduje některý z následujících prohlížečů:

* Microsoft Edge (verze 80 nebo novější)
* Google Chrome (verze 70 nebo novější)

## <a name="enable-debugging-for-visual-studio-and-visual-studio-code"></a>Povolit ladění pro Visual Studio a Visual Studio Code

Chcete-li povolit ladění pro existující Blazor aplikaci WebAssembly, aktualizujte soubor *launchSettings. JSON* ve složce spouštěný projekt tak, aby do `inspectUri` každého spouštěcího profilu zahrnoval následující vlastnost:

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/_framework/debug/ws-proxy?browser={browserInspectUri}"
```

Po aktualizaci by soubor *launchSettings. JSON* měl vypadat podobně jako v následujícím příkladu:

[!code-json[](debug/launchSettings.json?highlight=14,22)]

`inspectUri`Vlastnost:

* Umožňuje rozhraní IDE zjistit, že aplikace je Blazor aplikace WebAssembly.
* Dá pokyn k tomu, aby se infrastruktura ladění skriptů připojovala k prohlížeči prostřednictvím Blazor ladicího proxy serveru.

Zástupný text pro protokol WebSockets ( `wsProtocol` ), hostitele ( `url.hostname` ), port ( `url.port` ) a identifikátor URI pro inspektor v spuštěném prohlížeči ( `browserInspectUri` ) jsou poskytovány rozhraním.

## <a name="visual-studio"></a>Visual Studio

Ladění Blazor aplikace WebAssembly v aplikaci Visual Studio:

1. Vytvoří novou ASP.NET Core hostovanou Blazor aplikaci WebAssembly.
1. Stisknutím klávesy <kbd>F5</kbd> spusťte aplikaci v ladicím programu.
1. V metodě nastavte zarážku v *čítači. Razor* `IncrementCount` .
1. Přejděte na kartu **čítač** a vyberte tlačítko, kde se má zarážka opakovat:

   ![Čítač ladění](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-counter.png)

1. Podívejte se na hodnotu `currentCount` pole v okně místní hodnoty:

   ![Zobrazit místní hodnoty](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-locals.png)

1. Pokračujte v provádění stisknutím klávesy <kbd>F5</kbd> .

Při ladění Blazor aplikace pro WebAssembly můžete také ladit kód serveru:

1. Nastavte zarážku na stránce *FetchData. Razor* v <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> .
1. Nastavte zarážku v `WeatherForecastController` `Get` metodě Action.
1. Přejděte na kartu **načíst data** , abyste narazili na první zarážku v `FetchData` součásti těsně předtím, než vydá požadavek HTTP na server:

   ![Ladit načtení dat](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-fetch-data.png)

1. Stisknutím klávesy <kbd>F5</kbd> pokračujte v provádění a potom stiskněte zarážku na serveru v `WeatherForecastController` :

   ![Server ladění](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-server.png)

1. Stiskněte znovu <kbd>F5</kbd> , aby bylo možné pokračovat v provádění, a podívejte se na vykreslenou tabulku předpovědi počasí.

<a id="vscode"></a>

## <a name="visual-studio-code"></a>Visual Studio Code

Ladění Blazor aplikace WebAssembly v Visual Studio Code:
 
Nainstalujte [rozšíření C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) a rozšíření [ladicí program JavaScriptu (v noci)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) s `debug.javascript.usePreview` nastavením na `true` .

![Rozšíření](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-extensions.png)

![JS Preview ladicího programu](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-js-use-preview.png)

### <a name="debug-standalone-blazor-webassembly"></a>Ladit samostatné Blazor WebAssembly

1. Otevřete samostatnou Blazor aplikaci WebAssembly v vs Code.

   Pokud se zobrazí následující oznámení, že pro povolení ladění je potřeba další nastavení:
   
   * Potvrďte, že máte nainstalované správné rozšíření.
   * Potvrďte, že je povolené ladění v jazyce JavaScript verze Preview.
   * Znovu načtěte okno.

   ![Vyžaduje se další instalace.](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-additional-setup.png)

1. Spusťte ladění pomocí klávesových zkratek <kbd>F5</kbd> nebo položky nabídky.

1. Po zobrazení výzvy vyberte možnost ** Blazor ladění WebAssembly** a spusťte ladění.

   ![Seznam dostupných možností ladění](index/_static/blazor-vscode-debugtypes.png)

1. Spustí se samostatná aplikace a otevře se prohlížeč ladění.

1. Nastavte zarážku v `IncrementCount` metodě v `Counter` součásti a potom vyberte tlačítko, kterým se má zarážka opakovat:

   ![Čítač ladění v VS Code](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-debug-counter.png)

### <a name="debug-hosted-blazor-webassembly"></a>Ladit hostované Blazor WebAssembly

1. Otevřete hostovanou Blazor aplikaci WebAssembly v vs Code.

1. Pokud pro projekt neexistuje konfigurační sada pro spuštění, zobrazí se následující oznámení. Vyberte **Ano**.

   ![Přidat požadované prostředky](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-required-assets.png)

1. V okně Výběr vyberte *serverový* projekt v hostovaném řešení.

Soubor *Launch. JSON* se vygeneruje s konfigurací spuštění pro spuštění ladicího programu.

### <a name="attach-to-an-existing-debugging-session"></a>Připojit k existující relaci ladění

Pokud se chcete připojit ke spuštěné Blazor aplikaci, vytvořte soubor *Launch. JSON* s následující konfigurací:

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach to Existing Blazor WebAssembly Application"
}
```

> [!NOTE]
> Připojení k relaci ladění se podporuje jenom pro samostatné aplikace. Chcete-li použít ladění v plném zásobníku, musíte aplikaci spustit z VS Code.

### <a name="launch-configuration-options"></a>Spustit možnosti konfigurace

Pro typ ladění jsou podporovány následující možnosti konfigurace spuštění `blazorwasm` .

| Možnost    | Popis |
| --------- | ----------- |
| `request` | Použijte `launch` ke spuštění a připojení relace ladění k Blazor aplikaci WebAssembly nebo `attach` k připojení relace ladění k již spuštěné aplikaci. |
| `url`     | Adresa URL, která se má otevřít v prohlížeči při ladění. Výchozí hodnota je `https://localhost:5001` . |
| `browser` | Prohlížeč, který se má spustit pro relaci ladění. Nastavte na `edge` nebo `chrome`. Výchozí hodnota je `chrome` . |
| `trace`   | Slouží ke generování protokolů z ladicího programu JS. Nastavte na `true` Generovat protokoly. |
| `hosted`  | Musí být nastaven na hodnotu `true` při spuštění a ladění hostované Blazor aplikace WebAssembly. |
| `webRoot` | Určuje absolutní cestu webového serveru. By měla být nastavena, pokud je aplikace obsluhována z dílčího směrování. |
| `timeout` | Počet milisekund, po které se má čekat na připojení relace ladění Výchozí hodnota je 30 000 milisekund (30 sekund). |
| `program` | Odkaz na spustitelný soubor pro spuštění serveru hostované aplikace. Musí být nastaven `hosted` , pokud je `true` . |
| `cwd`     | Pracovní adresář, ve kterém se má spustit aplikace Musí být nastaven `hosted` , pokud je `true` . |
| `env`     | Proměnné prostředí, které se mají poskytnout spuštěnému procesu. Platí pouze v případě `hosted` , že je nastavena na `true` . |

### <a name="example-launch-configurations"></a>Příklad konfigurací spuštění

#### <a name="launch-and-debug-a-standalone-blazor-webassembly-app"></a>Spuštění a ladění samostatné Blazor aplikace WebAssembly

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

#### <a name="launch-and-debug-a-hosted-blazor-webassembly-app"></a>Spuštění a ladění hostované Blazor aplikace WebAssembly

```json
{
  "type": "blazorwasm",
  "request": "launch",
  "name": "Launch and Debug Hosted App",
  "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/MyHostedApp.Server.dll",
  "cwd": "${workspaceFolder}"
}
```

## <a name="debug-in-the-browser"></a>Ladit v prohlížeči

1. Spusťte ladicí sestavení aplikace ve vývojovém prostředí.

1. Stiskněte <kbd>SHIFT</kbd> + <kbd>ALT</kbd> + <kbd>D</kbd>.

1. Prohlížeč musí být spuštěn s povoleným vzdáleným laděním. Pokud je vzdálené ladění zakázané, vygeneruje se chybová stránka karty prohlížeče, která se **nedá najít** . Chybová stránka obsahuje pokyny pro spuštění prohlížeče s otevřeným portem pro ladění, aby se Blazor ladicí proxy server mohl připojit k aplikaci. *Zavřete všechny instance prohlížeče* a restartujte prohlížeč podle pokynů.

Po spuštění prohlížeče se zapnutým vzdáleným laděním otevře klávesovou zkratku ladění novou kartu ladicího programu. Po chvíli se na kartě **zdroje** zobrazí seznam sestavení .NET v aplikaci. Rozbalte jednotlivá sestavení a vyhledejte zdrojové soubory *. cs* / *. Razor* k dispozici pro ladění. Nastavte zarážky, přepněte zpět na kartu aplikace a zarážky se spustí při spuštění kódu. Po stisknutí zarážky se provede krok (<kbd>F10</kbd>) prostřednictvím kódu nebo obnovení kódu (<kbd>F8</kbd>) normálně.

Blazorposkytuje ladicí proxy, který implementuje [protokol Chrome DevTools](https://chromedevtools.github.io/devtools-protocol/) a rozšiřuje protokol pomocí. Informace specifické pro síť. Když se stiskne klávesová zkratka ladění, navede Blazor devtools Chrome na proxy serveru. Proxy server se připojí k oknu prohlížeče, které se pokoušíte ladit (takže je potřeba povolit vzdálené ladění).

## <a name="browser-source-maps"></a>Mapy zdroje prohlížeče

Mapování zdrojového kódu prohlížeče umožňují prohlížeči mapovat zkompilované soubory zpátky na původní zdrojové soubory a často se používají pro ladění na straně klienta. V Blazor současné době ale nemapuje C# přímo na JavaScript/WASM. Místo toho Blazor provede výklad il v prohlížeči, takže zdrojové mapy nejsou relevantní.

## <a name="troubleshoot"></a>Řešení potíží

Pokud dochází k chybám, může vám pomáhat následující tipy:

* Na kartě **ladicí program** otevřete nástroje pro vývojáře v prohlížeči. V konzole nástroje spusťte příkaz `localStorage.clear()` k odebrání všech zarážek.
* Potvrďte, že jste nainstalovali a důvěřujete ASP.NET Core certifikát pro vývoj HTTPS. Další informace naleznete v tématu <xref:security/enforcing-ssl#troubleshoot-certificate-problems>.
