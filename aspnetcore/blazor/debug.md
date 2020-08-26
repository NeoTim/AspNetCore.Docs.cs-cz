---
title: ASP.NET Core ladění Blazor WebAssembly
author: guardrex
description: Naučte se ladit Blazor aplikace.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/26/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/debug
ms.openlocfilehash: 7681deb70610a8fbc27ccda7317b73921646794a
ms.sourcegitcommit: 4df148cbbfae9ec8d377283ee71394944a284051
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/26/2020
ms.locfileid: "88876773"
---
# <a name="debug-aspnet-core-no-locblazor-webassembly"></a>ASP.NET Core ladění Blazor WebAssembly

[Daniel Skořepa](https://github.com/danroth27)

Blazor WebAssembly aplikace se dají ladit pomocí vývojářských nástrojů pro vývoj v prohlížečích založených na chromu (Edge/Chrome). Aplikaci můžete také ladit pomocí následujících integrovaných vývojových prostředí (IDEs):

* Visual Studio
* Visual Studio pro Mac
* Visual Studio Code

K dispozici jsou tyto scénáře:

* Nastavení a odebrání zarážek.
* Spusťte aplikaci s podporou ladění v prostředích IDEs.
* Jednoduchý krok kódu.
* Pokračuje v provádění kódu pomocí klávesové zkratky v prostředích IDEs.
* V okně *místní* hodnoty Sledujte hodnoty místních proměnných.
* Podívejte se do zásobníku volání, včetně řetězců volání mezi JavaScript a .NET.

Teď *nemůžete*:

* Přerušit při neošetřených výjimkách.
* Zarážky volání během spouštění aplikace před spuštěním ladicího proxy serveru. To zahrnuje zarážky v `Program.Main` ( `Program.cs` ) a zarážky v [ `OnInitialized{Async}` metodách](xref:blazor/components/lifecycle#component-initialization-methods) komponent, které jsou načteny první stránkou požadovanou z aplikace.

## <a name="prerequisites"></a>Předpoklady

Ladění vyžaduje některý z následujících prohlížečů:

* Google Chrome (verze 70 nebo novější) (výchozí)
* Microsoft Edge (verze 80 nebo novější)

Visual Studio pro Mac vyžaduje verzi 8,8 (Build 1532) nebo novější:

1. Nejnovější verzi Visual Studio pro Mac nainstalujete tak, že vyberete tlačítko **stáhnout Visual Studio pro Mac** v [Microsoft: Visual Studio pro Mac](https://visualstudio.microsoft.com/vs/mac/).
1. V sadě Visual Studio vyberte kanál *verze Preview* . Další informace najdete v tématu [instalace verze preview Visual Studio pro Mac](/visualstudio/mac/install-preview).

> [!NOTE]
> Apple Safari na macOS se v tuto chvíli nepodporuje.

## <a name="enable-debugging"></a>Povolení ladění

Chcete-li povolit ladění pro existující Blazor WebAssembly aplikaci, aktualizujte `launchSettings.json` soubor v spouštěném projektu tak, aby do `inspectUri` každého spouštěcího profilu zahrnoval následující vlastnost:

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/_framework/debug/ws-proxy?browser={browserInspectUri}"
```

Po aktualizaci `launchSettings.json` by měl soubor vypadat podobně jako v následujícím příkladu:

[!code-json[](debug/launchSettings.json?highlight=14,22)]

`inspectUri`Vlastnost:

* Umožňuje rozhraní IDE zjistit, že aplikace je Blazor WebAssembly aplikace.
* Dá pokyn k tomu, aby se infrastruktura ladění skriptů připojovala k prohlížeči prostřednictvím Blazor ladicího proxy serveru.

Zástupný text pro protokol WebSockets ( `wsProtocol` ), hostitele ( `url.hostname` ), port ( `url.port` ) a identifikátor URI pro inspektor v spuštěném prohlížeči ( `browserInspectUri` ) jsou poskytovány rozhraním.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Ladění Blazor WebAssembly aplikace v aplikaci Visual Studio:

1. Vytvořte novou ASP.NET Core hostovanou Blazor WebAssembly aplikaci.
1. Stisknutím klávesy <kbd>F5</kbd> spusťte aplikaci v ladicím programu.

   > [!NOTE]
   > **Spuštění bez ladění** (<kbd>CTRL</kbd> + <kbd>F5</kbd>) není podporováno. Když je aplikace spuštěna v konfiguraci ladění, režie ladění vždy vede k omezení malého výkonu.

1. V *klientské* aplikaci nastavte zarážku na `currentCount++;` řádku v `Pages/Counter.razor` .
1. V prohlížeči přejděte na `Counter` stránku a kliknutím na tlačítko **klikněte na tlačítko Zobrazit** zarážku.
1. V aplikaci Visual Studio zkontrolujte hodnotu `currentCount` pole v okně **místní** hodnoty.
1. Pokračujte v provádění stisknutím klávesy <kbd>F5</kbd> .

Při ladění Blazor WebAssembly aplikace můžete také ladit kód serveru:

1. Nastavte zarážku na `Pages/FetchData.razor` stránce v <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> .
1. Nastavte zarážku v `WeatherForecastController` `Get` metodě Action.
1. Přejděte na `Fetch Data` stránku a vyberte první zarážku v `FetchData` součásti těsně předtím, než VYDÁ požadavek HTTP na server.
1. Stisknutím klávesy <kbd>F5</kbd> pokračujte v provádění a potom stiskněte zarážku na serveru v `WeatherForecastController` .
1. Opakovaným stisknutím klávesy <kbd>F5</kbd> můžete pokračovat v provádění a zobrazit tabulku předpovědi počasí vygenerovanou v prohlížeči.

> [!NOTE]
> Zarážky se během spouštění aplikace **neprojeví** před spuštěním ladicího proxy serveru. To zahrnuje zarážky v `Program.Main` ( `Program.cs` ) a zarážky v [ `OnInitialized{Async}` metodách](xref:blazor/components/lifecycle#component-initialization-methods) komponent, které jsou načteny první stránkou požadovanou z aplikace.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

<a id="vscode"></a>

## <a name="debug-standalone-no-locblazor-webassembly"></a>Samostatně ladit Blazor WebAssembly

1. Otevřete samostatnou Blazor WebAssembly aplikaci v vs Code.

   Může se zobrazit oznámení, že pro povolení ladění je potřeba další nastavení:

   > Pro ladění aplikací je potřeba další nastavení Blazor WebAssembly .

   Pokud obdržíte oznámení:

   * Potvrďte, že je nainstalovaná nejnovější [rozšíření C# pro Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) . Pokud chcete zkontrolovat nainstalovaná rozšíření, **View**otevřete  >  **rozšíření** zobrazení z řádku nabídek nebo vyberte ikonu **rozšíření** na postranním panelu **aktivita** .
   * Potvrďte, že je povolené ladění v jazyce JavaScript verze Preview. Otevřete nastavení z řádku nabídek (**File**  >  **Preferences**  >  **Nastavení**předvoleb souborů). Hledání pomocí klíčových slov `debug preview` . Ve výsledcích hledání potvrďte, že je zaškrtnuto políčko pro **ladění > JavaScript: použít verzi Preview** . Pokud není k dispozici možnost Povolit ladění ve verzi Preview, upgradujte na nejnovější verzi VS Code nebo nainstalujte [rozšíření ladicího programu jazyka JavaScript](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) (VS Code verze 1,46 nebo starší).
   * Znovu načtěte okno.

1. Spusťte ladění pomocí klávesových zkratek <kbd>F5</kbd> nebo položky nabídky.

   > [!NOTE]
   > **Spuštění bez ladění** (<kbd>CTRL</kbd> + <kbd>F5</kbd>) není podporováno. Když je aplikace spuštěna v konfiguraci ladění, režie ladění vždy vede k omezení malého výkonu.

1. Po zobrazení výzvy vyberte možnost ** Blazor WebAssembly ladění** pro spuštění ladění.

1. Spustí se samostatná aplikace a otevře se prohlížeč ladění.

1. V *klientské* aplikaci nastavte zarážku na `currentCount++;` řádku v `Pages/Counter.razor` .

1. V prohlížeči přejděte na `Counter` stránku a kliknutím na tlačítko **klikněte na tlačítko Zobrazit** zarážku.

> [!NOTE]
> Zarážky se během spouštění aplikace **neprojeví** před spuštěním ladicího proxy serveru. To zahrnuje zarážky v `Program.Main` ( `Program.cs` ) a zarážky v [ `OnInitialized{Async}` metodách](xref:blazor/components/lifecycle#component-initialization-methods) komponent, které jsou načteny první stránkou požadovanou z aplikace.

## <a name="debug-hosted-no-locblazor-webassembly"></a>Ladění je hostované Blazor WebAssembly

1. Otevřete Blazor WebAssembly složku řešení hostované aplikace v vs Code.

1. Pokud pro projekt neexistuje konfigurační sada pro spuštění, zobrazí se následující oznámení. Vyberte **Ano**.

   > Požadované prostředky pro sestavení a ladění chybí v {APPLICATION NAME}. Přidat je?

1. V paletě příkazů v horní části okna vyberte *serverový* projekt v hostovaném řešení.

`launch.json`Soubor se vygeneruje s konfigurací spuštění pro spuštění ladicího programu.

## <a name="attach-to-an-existing-debugging-session"></a>Připojit k existující relaci ladění

Pokud se chcete připojit ke spuštěné Blazor aplikaci, vytvořte `launch.json` soubor s následující konfigurací:

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach to Existing Blazor WebAssembly Application"
}
```

> [!NOTE]
> Připojení k relaci ladění se podporuje jenom pro samostatné aplikace. Chcete-li použít ladění v plném zásobníku, musíte aplikaci spustit z VS Code.

## <a name="launch-configuration-options"></a>Spustit možnosti konfigurace

Pro `blazorwasm` typ ladění () jsou podporovány následující možnosti konfigurace spuštění `.vscode/launch.json` .

| Možnost    | Popis |
| --------- | ----------- |
| `request` | Pomocí `launch` můžete spustit a připojit relaci ladění k Blazor WebAssembly aplikaci nebo `attach` připojit relaci ladění k již spuštěné aplikaci. |
| `url`     | Adresa URL, která se má otevřít v prohlížeči při ladění. Výchozí hodnota je `https://localhost:5001` . |
| `browser` | Prohlížeč, který se má spustit pro relaci ladění. Nastavte na `edge` nebo `chrome`. Výchozí hodnota je `chrome` . |
| `trace`   | Slouží ke generování protokolů z ladicího programu JS. Nastavte na `true` Generovat protokoly. |
| `hosted`  | Musí být nastavené na hodnotu `true` při spuštění a ladění hostované Blazor WebAssembly aplikace. |
| `webRoot` | Určuje absolutní cestu webového serveru. By měla být nastavena, pokud je aplikace obsluhována z dílčího směrování. |
| `timeout` | Počet milisekund, po které se má čekat na připojení relace ladění Výchozí hodnota je 30 000 milisekund (30 sekund). |
| `program` | Odkaz na spustitelný soubor pro spuštění serveru hostované aplikace. Musí být nastaven `hosted` , pokud je `true` . |
| `cwd`     | Pracovní adresář, ve kterém se má spustit aplikace Musí být nastaven `hosted` , pokud je `true` . |
| `env`     | Proměnné prostředí, které se mají poskytnout spuštěnému procesu. Platí pouze v případě `hosted` , že je nastavena na `true` . |

## <a name="example-launch-configurations"></a>Příklad konfigurací spuštění

### <a name="launch-and-debug-a-standalone-no-locblazor-webassembly-app"></a>Spuštění a ladění samostatné Blazor WebAssembly aplikace

```json
{
  "type": "blazorwasm",
  "request": "launch",
  "name": "Launch and Debug"
}
```

### <a name="attach-to-a-running-app-at-a-specified-url"></a>Připojit se k běžící aplikaci v zadané adrese URL

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach and Debug",
  "url": "http://localhost:5000"
}
```

### <a name="launch-and-debug-a-hosted-no-locblazor-webassembly-app-with-microsoft-edge"></a>Spuštění a ladění hostované Blazor WebAssembly aplikace pomocí Microsoft Edge

Konfigurace prohlížeče je ve výchozím nastavení Google Chrome. Při použití Microsoft Edge pro ladění nastavte `browser` na `edge` . Pokud chcete používat Google Chrome, buď nenastavujte `browser` možnost, nebo nastavte hodnotu možnosti na `chrome` .

```json
{
  "name": "Launch and Debug Hosted Blazor WebAssembly App",
  "type": "blazorwasm",
  "request": "launch",
  "hosted": true,
  "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/MyHostedApp.Server.dll",
  "cwd": "${workspaceFolder}/Server",
  "browser": "edge"
}
```

V předchozím příkladu `MyHostedApp.Server.dll` je sestavení *serverové* aplikace. `.vscode`Složka se nachází ve složce řešení vedle `Client` `Server` složek, a `Shared` .

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

Ladění Blazor WebAssembly aplikace v Visual Studio pro Mac:

1. Vytvořte novou ASP.NET Core hostovanou Blazor WebAssembly aplikaci.
1. Stisknutím <kbd>&#8984;</kbd> + <kbd>&#8617;</kbd> spusťte aplikaci v ladicím programu.

   > [!NOTE]
   > **Spuštění bez ladění** (<kbd>&#8997;</kbd> + <kbd>&#8984;</kbd> + <kbd>&#8617;</kbd>) není podporováno. Když je aplikace spuštěna v konfiguraci ladění, režie ladění vždy vede k omezení malého výkonu.

   > [!IMPORTANT]
   > Google Chrome nebo Microsoft Edge musí být vybraným prohlížečem pro relaci ladění.

1. V *klientské* aplikaci nastavte zarážku na `currentCount++;` řádku v `Pages/Counter.razor` .
1. V prohlížeči přejděte na `Counter` stránku a kliknutím na tlačítko **kliknutím na tlačítko** spustit zarážku stiskněte klávesu:
1. V aplikaci Visual Studio zkontrolujte hodnotu `currentCount` pole v okně **místní** hodnoty.
1. Pokračujte v provádění stisknutím <kbd>&#8984;</kbd> + <kbd>&#8617;</kbd> .

Při ladění Blazor WebAssembly aplikace můžete také ladit kód serveru:

1. Nastavte zarážku na `Pages/FetchData.razor` stránce v <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> .
1. Nastavte zarážku v `WeatherForecastController` `Get` metodě Action.
1. Přejděte na `Fetch Data` stránku a vyberte první zarážku v `FetchData` součásti těsně předtím, než VYDÁ požadavek HTTP na server.
1. Pokračujte v provádění stisknutím <kbd>&#8984;</kbd> + <kbd>&#8617;</kbd> a potom stiskněte zarážku na serveru v `WeatherForecastController` .
1. Znovu stiskněte <kbd>&#8984;</kbd> + <kbd>&#8617;</kbd> , aby bylo možné pokračovat v provádění, a podívejte se na tabulku předpovědi počasí vykreslenou v prohlížeči.

> [!NOTE]
> Zarážky se během spouštění aplikace **neprojeví** před spuštěním ladicího proxy serveru. To zahrnuje zarážky v `Program.Main` ( `Program.cs` ) a zarážky v [ `OnInitialized{Async}` metodách](xref:blazor/components/lifecycle#component-initialization-methods) komponent, které jsou načteny první stránkou požadovanou z aplikace.

Další informace naleznete v tématu [ladění pomocí Visual Studio pro Mac](/visualstudio/mac/debugging?view=vsmac-2019).

---

## <a name="debug-in-the-browser"></a>Ladit v prohlížeči

*Pokyny v této části se vztahují na Google Chrome a Microsoft Edge běžící ve Windows.*

1. Spusťte ladicí sestavení aplikace ve vývojovém prostředí.

1. Spusťte prohlížeč a přejděte na adresu URL aplikace (například `https://localhost:5001` ).

1. V prohlížeči se pokuste zahájit vzdálené ladění stisknutím klávesy <kbd>SHIFT</kbd> + <kbd>ALT</kbd> + <kbd>d</kbd>.

   Prohlížeč musí běžet se zapnutým vzdáleným laděním, což není výchozí nastavení. Pokud je vzdálené ladění zakázané, zobrazí se stránka s informací o tom, že se pro spuštění prohlížeče s otevřeným portem ladění **nedaří najít** chybovou stránku karty prohlížeče. Postupujte podle pokynů pro prohlížeč a otevřete nové okno prohlížeče. Zavřete předchozí okno prohlížeče.

<!-- HOLD 
1. In the browser, attempt to commence remote debugging by pressing:

   * <kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>d</kbd> on Windows.
   * <kbd>Shift</kbd>+<kbd>&#8984;</kbd>+<kbd>d</kbd> on macOS.

   The browser must be running with remote debugging enabled, which isn't the default. If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is rendered with instructions for launching the browser with the debugging port open. Follow the instructions for your browser, which opens a new browser window. Close the previous browser window.
-->

1. Po spuštění prohlížeče se zapnutým vzdáleným laděním otevře klávesovou zkratku pro ladění v předchozím kroku novou kartu ladicího programu.

1. Po chvíli se na kartě **zdroje** zobrazí seznam sestavení .NET aplikace v rámci `file://` uzlu.

1. V kódu komponenty ( `.razor` soubory) a souborech kódu jazyka C# ( `.cs` ) jsou zarážky, které jste nastavili, zasaženy při spuštění kódu. Po stisknutí zarážky se provede krok (<kbd>F10</kbd>) prostřednictvím kódu nebo obnovení kódu (<kbd>F8</kbd>) normálně.

Blazor poskytuje ladicí proxy, který implementuje [protokol Chrome DevTools](https://chromedevtools.github.io/devtools-protocol/) a rozšiřuje protokol pomocí. Informace specifické pro síť. Když se stiskne klávesová zkratka ladění, navede Blazor devtools Chrome na proxy serveru. Proxy server se připojí k oknu prohlížeče, které se pokoušíte ladit (takže je potřeba povolit vzdálené ladění).

## <a name="browser-source-maps"></a>Mapy zdroje prohlížeče

Mapování zdrojového kódu prohlížeče umožňují prohlížeči mapovat zkompilované soubory zpátky na původní zdrojové soubory a často se používají pro ladění na straně klienta. V Blazor současné době ale nemapuje C# přímo na JavaScript/WASM. Místo toho Blazor provede výklad il v prohlížeči, takže zdrojové mapy nejsou relevantní.

## <a name="troubleshoot"></a>Řešení potíží

Pokud dochází k chybám, může vám pomáhat následující tipy:

* Na kartě **ladicí program** otevřete nástroje pro vývojáře v prohlížeči. V konzole nástroje spusťte příkaz `localStorage.clear()` k odebrání všech zarážek.
* Potvrďte, že jste nainstalovali a důvěřujete ASP.NET Core certifikát pro vývoj HTTPS. Další informace naleznete v tématu <xref:security/enforcing-ssl#troubleshoot-certificate-problems>.
* Visual Studio vyžaduje možnost **Povolit ladění JavaScriptu pro ASP.NET (Chrome, Edge a IE)** v možnostech **nástrojů**  >  **Options**  >  **Debugging**  >  **Obecné**ladění. Toto je výchozí nastavení pro Visual Studio. Pokud ladění nefunguje, potvrďte, že je vybraná možnost.

### <a name="breakpoints-in-oninitializedasync-not-hit"></a>Zarážky `OnInitialized{Async}` nejsou v neúspěšných.

BlazorSpuštění ladicího proxy serveru pro rozhraní trvá krátkou dobu, takže zarážky v [ `OnInitialized{Async}` metodě životního cyklu](xref:blazor/components/lifecycle#component-initialization-methods) nemusí být k dispozice. Doporučujeme přidat zpoždění na začátek těla metody a umožnit tak spuštění ladicího proxy serveru nějakou dobu, než se zarážka zasáhne. Můžete zahrnout zpoždění na základě [ `if` direktivy kompilátoru](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) , aby se zajistilo, že zpoždění není k dispozici pro sestavení pro vydání aplikace.

<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>:

```csharp
protected override void OnInitialized()
{
#if DEBUG
    Thread.Sleep(10000)
#endif

    ...
}
```

<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>:

```csharp
protected override async Task OnInitializedAsync()
{
#if DEBUG
    await Task.Delay(10000)
#endif

    ...
}
```

### <a name="visual-studio-windows-timeout"></a>Časový limit sady Visual Studio (Windows)

Pokud Visual Studio vyvolá výjimku, že se ladicímu adaptéru nepovedlo spustit zmínku o tom, že byl dosažen časový limit, můžete nastavit časový limit pomocí nastavení registru:

```console
VsRegEdit.exe set "<VSInstallFolder>" HKCU JSDebugger\Options\Debugging "BlazorTimeoutInMilliseconds" dword {TIMEOUT}
```

`{TIMEOUT}`Zástupný symbol v předchozím příkazu je v milisekundách. Například jedna minuta je přiřazena jako `60000` .
