---
title: Modely hostování ASP.NET Core Blazor
author: guardrex
description: Pochopení Blazor modelů hostování serverů a Blazor serveru.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 11/21/2019
no-loc:
- Blazor
- SignalR
uid: blazor/hosting-models
ms.openlocfilehash: a017737eacd93ac776afe7ee8024eed602d7edcc
ms.sourcegitcommit: 3e503ef510008e77be6dd82ee79213c9f7b97607
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/22/2019
ms.locfileid: "74317217"
---
# <a name="aspnet-core-opno-locblazor-hosting-models"></a>Modely hostování ASP.NET Core Blazor

Od [Daniel Skořepa](https://github.com/danroth27)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazor je webová architektura navržená tak, aby na straně klienta běžela na klientském počítači .NET runtime založeném na webovém [sestavení](https://webassembly.org/)( *Blazor webové sestavení*) nebo na straně serveru v ASP.NET Core ( *ServerBlazor* ). Bez ohledu na model hostování jsou modely aplikací a komponent *stejné*.

Chcete-li vytvořit projekt pro modely hostování popsané v tomto článku, přečtěte si téma <xref:blazor/get-started>.

## <a name="opno-locblazor-webassembly"></a>Blazor WebAssembly

Hlavní hostující model pro Blazor spouští klientskou stranu v prohlížeči na webovém sestavení. Aplikace Blazor, její závislosti a modul runtime .NET se stáhnou do prohlížeče. Aplikace se spustí přímo ve vlákně uživatelského rozhraní prohlížeče. Aktualizace uživatelského rozhraní a zpracování událostí se vyskytují v rámci stejného procesu. Prostředky aplikace se nasazují jako statické soubory na webový server nebo službu, která je schopná obsluhovat statický obsah klientům.

![[! Evřít. Rozhraní Blazor (NO-LOC)] WebAssembly: [! Evřít. Aplikace NO-LOC (Blazor)] běží ve vlákně uživatelského rozhraní v prohlížeči.](hosting-models/_static/blazor-webassembly.png)

Chcete-li vytvořit aplikaci Blazor pomocí modelu hostování na straně klienta, použijte šablonu **aplikaceBlazor WebAssembly** ([dotnet New blazorwasm](/dotnet/core/tools/dotnet-new)).

Po výběru šablony **aplikaceBlazor WebAssembly** máte možnost konfigurovat aplikaci tak, aby používala ASP.NET Core back-end, a to tak, že vyberete zaškrtávací políčko **ASP.NET Core hostované** ([dotnet New blazorwasm--Hosted](/dotnet/core/tools/dotnet-new)). Aplikace ASP.NET Core obsluhuje aplikaci Blazor klientům. Aplikace Blazor WebAssembly může komunikovat se serverem přes síť pomocí volání webového rozhraní API nebo [SignalR](xref:signalr/introduction).

Šablony obsahují skript *blazor. WebAssembly. js* , který zpracovává:

* Stažení modulu runtime .NET, aplikace a závislostí aplikace.
* Inicializace modulu runtime pro spuštění aplikace.

Model hostování Blazor WebAssembly nabízí několik výhod:

* Neexistuje žádná závislost na straně serveru .NET. Aplikace po stažení do klienta plně funguje.
* Prostředky a možnosti klienta jsou plně využité.
* Práce je ze serveru převedena na klienta.
* Pro hostování aplikace není vyžadován ASP.NET Core webový server. Jsou možné scénáře nasazení bez serveru (například poskytování aplikace z CDN).

K Blazor hostování WebAssembly je downsides:

* Aplikace je omezená na možnosti prohlížeče.
* Je vyžadován klientský hardware a software (například podpora WebAssembly).
* Velikost ke stažení je větší a aplikace trvá déle, než se načtou.
* Podpora modulu runtime .NET a nástrojů je méně vyspělá. Například omezení existují v [.NET Standard](/dotnet/standard/net-standard) podpoře a ladění.

## <a name="opno-locblazor-server"></a>Server Blazor

S modelem hostování serveru Blazor se aplikace spouští na serveru z aplikace ASP.NET Core. Aktualizace uživatelského rozhraní, zpracování událostí a volání JavaScriptu jsou zpracovávána prostřednictvím [SignalRho](xref:signalr/introduction) připojení.

![Prohlížeč komunikuje s aplikací (hostovanou v aplikaci ASP.NET Core) na serveru přes [! Evřít. Připojení NO-LOC (Signal)]](hosting-models/_static/blazor-server.png)

Pokud chcete vytvořit aplikaci Blazor pomocí modelu hostování Blazor serveru, použijte šablonu aplikace ASP.NET Core **Blazor Server** ([dotnet New blazorserver](/dotnet/core/tools/dotnet-new)). Aplikace ASP.NET Core hostuje aplikaci Blazor serveru a vytvoří SignalR koncový bod, ke kterému se klienti připojují.

ASP.NET Core aplikace odkazuje na třídu `Startup` aplikace, která se má přidat:

* Služby na straně serveru.
* Aplikace do kanálu pro zpracování požadavků.

Skript *blazor. Server. js*&dagger; vytvoří připojení klienta. Je zodpovědností aplikace zachovat a obnovit stav aplikace podle potřeby (například v případě ztraceného síťového připojení).

Model hostování serveru Blazor nabízí několik výhod:

* Velikost ke stažení je výrazně menší než Blazor aplikace WebAssembly a aplikace se načte mnohem rychleji.
* Aplikace plně využívá možnosti serveru, včetně použití všech rozhraní API kompatibilních s rozhraním .NET Core.
* Rozhraní .NET Core na serveru se používá ke spuštění aplikace, takže stávající nástroje .NET, jako je ladění, fungují podle očekávání.
* Podporují se tenké klienty. Například Blazor serverové aplikace fungují s prohlížeči, které nepodporují WebAssembly a na zařízeních s omezením prostředků.
* Základ pro .NET/C# kód aplikace, včetně kódu komponenty aplikace, není obsluhován klientům.

K dispozici je downsides Blazor hostování serveru:

* Vyšší latence obvykle existuje. Každá interakce uživatele zahrnuje směrování sítě.
* Neexistuje žádná podpora offline. Pokud připojení klienta neproběhne úspěšně, aplikace přestane fungovat.
* Pro aplikace s mnoha uživateli je škálovatelnost náročná. Server musí spravovat více připojení klientů a zpracovávat stav klienta.
* Pro obsluhu aplikace je vyžadován ASP.NET Core Server. Scénáře nasazení bez serveru nejsou možné (například poskytování aplikace z CDN).

&dagger;skript *blazor. Server. js* se obsluhuje z vloženého prostředku ve ASP.NET Core sdíleném rozhraní.

### <a name="comparison-to-server-rendered-ui"></a>Porovnání s uživatelským rozhraním vykresleným serverem

Jedním ze způsobů, jak pochopit Blazor serverových aplikací, je pochopit, jak se liší od tradičních modelů pro vykreslování uživatelského rozhraní v ASP.NET Core aplikacích, které používají zobrazení Razor nebo Razor Pages. Oba modely používají jazyk Razor k popisu obsahu HTML, ale významně se liší v tom, jak se vykreslují kód.

Když je vykreslena stránka nebo zobrazení Razor, každý řádek kódu Razor generuje kód HTML v textovém formátu. Po vykreslení Server uvolní instanci stránky nebo zobrazení, včetně veškerého stavu, který byl vytvořen. Pokud dojde k jiné žádosti na stránku, například když se ověření serveru nepovede a zobrazí se shrnutí ověření:

* Celá stránka se znovu vykreslí do textu HTML.
* Stránka je odeslána klientovi.

Blazor aplikace se skládá z opakovaně použitelných prvků uživatelského rozhraní s názvem *Components*. Komponenta obsahuje C# kód, značku a další komponenty. Když je vykreslena komponenta, Blazor vytvoří graf zahrnutých součástí, podobně jako HTML nebo XML model DOM (Document Object Model) (DOM). Tento graf obsahuje stav součásti uložený ve vlastnostech a polích. Blazor vyhodnotí graf komponent pro vytvoření binární reprezentace kódu. Binární formát může být:

* Byl převeden na text HTML (během předvykreslování).
* Slouží k efektivní aktualizaci značek během pravidelného vykreslování.

Aktualizace uživatelského rozhraní v Blazor je aktivována pomocí:

* Interakce s uživatelem, například výběr tlačítka.
* Aktivační události aplikace, jako je například časovač.

Graf se znovu vykreslí a počítá se *rozdíl* v uživatelském rozhraní (rozdíl). Tento rozdíl je nejmenší sada úprav modelu DOM, která je nutná k aktualizaci uživatelského rozhraní na klientovi. Rozdíl se pošle klientovi v binárním formátu a použije ho prohlížeč.

Komponenta je uvolněna poté, co uživatel z něj přejde na klienta. I když uživatel pracuje s komponentou, musí se stav komponenty (služby, prostředky) uchovávat v paměti serveru. Vzhledem k tomu, že stav mnoha součástí může být serverem současně udržován, vyčerpání paměti je problém, který je třeba řešit. Pokyny k vytvoření aplikace Blazor serveru pro zajištění nejlepšího využití paměti serveru najdete v tématu <xref:security/blazor/server>.

### <a name="circuits"></a>Spoj

Aplikace Blazor serveru je postavená nad [ASP.NET Core SignalR](xref:signalr/introduction). Každý klient komunikuje se serverem přes jedno nebo více SignalR připojení s názvem *okruh*. Okruh je Blazorabstrakce prostřednictvím SignalR připojení, která mohou tolerovat dočasné síťové přerušení. Když klient Blazor uvidí, že připojení SignalR je odpojené, pokusí se znovu připojit k serveru pomocí nového SignalR připojení.

Každá obrazovka prohlížeče (karta prohlížeče nebo IFRAME), která je připojená k aplikaci Blazor serveru, používá SignalR připojení. Toto je ještě další důležité rozdíly ve srovnání s typickými aplikacemi vygenerovanými serverem. V aplikaci vykreslené serverem se při otevření stejné aplikace v několika obrazovkách prohlížeče obvykle nepřevádí na další požadavky na prostředky na serveru. V aplikaci Blazor serveru vyžaduje Každá obrazovka prohlížeče samostatný okruh a samostatné instance stavu součásti, které má server spravovat.

Blazor se domnívá zavřít kartu prohlížeče nebo přejít na externí adresu URL s *řádným* ukončením. V případě řádného ukončení se okruh a přidružené prostředky ihned uvolňují. Klient se může také bez problémů odpojit, například kvůli přerušení sítě. Blazor server ukládá odpojené okruhy po Konfigurovatelný interval, aby se klient mohl znovu připojit. Další informace najdete v části [opětovné připojení ke stejnému serveru](#reconnection-to-the-same-server) .

### <a name="ui-latency"></a>Latence uživatelského rozhraní

Latence uživatelského rozhraní je doba, kterou trvá od iniciované akce až do doby, kdy je uživatelské rozhraní aktualizováno. Menší hodnoty latence uživatelského rozhraní jsou pro aplikaci pro reakci na uživatele naprosto nezbytné. V aplikaci Blazor serveru se všechny akce odesílají na server, zpracováno a rozdíl v uživatelském rozhraní se pošle zpátky. V důsledku toho je latence uživatelského rozhraní součtem latence sítě a latencí serveru při zpracování akce.

Pro podnikovou aplikaci, která je omezená jenom na soukromou podnikovou síť, se většinou neprojeví vlivu na vnímání uživatele latence kvůli latenci sítě. V případě aplikace nasazené přes Internet může latence znamenat uživatele, zejména v případě, že uživatelé jsou geograficky distribuováni podrobněji.

Využití paměti může také přispět k latenci aplikace. Zvýšení využití paměti vede k častému uvolňování paměti nebo paměti stránkování na disk, přičemž obě tyto čítače zvyšují výkon aplikace a následně zvyšují latenci uživatelského rozhraní. Další informace najdete v tématu <xref:security/blazor/server>.

aplikace Blazor Server by měly být optimalizované pro minimalizaci latence uživatelského rozhraní tím, že se sníží latence sítě a využití paměti. Přístup k měření latence sítě najdete v tématu <xref:host-and-deploy/blazor/server#measure-network-latency>. Další informace o SignalR a Blazornajdete v těchto tématech:

* <xref:host-and-deploy/blazor/server>
* <xref:security/blazor/server>

### <a name="connection-to-the-server"></a>Připojení k serveru

aplikace Blazor serveru vyžadují připojení k serveru aktivní SignalR. Pokud dojde ke ztrátě připojení, aplikace se pokusí znovu připojit k serveru. Pokud je stav klienta stále v paměti, klientská relace pokračuje bez ztráty stavu.

#### <a name="reconnection-to-the-same-server"></a>Opětovné připojení ke stejnému serveru

Aplikace Blazor serveru se předem vykreslí v reakci na první požadavek klienta, který nastaví stav uživatelského rozhraní na serveru. Když se klient pokusí vytvořit připojení SignalR, klient se musí znovu připojit ke stejnému serveru. aplikace Blazor serveru, které používají více než jeden back-end Server, by měly implementovat *rychlé relace* pro SignalR připojení.

Pro Blazor serverových aplikací doporučujeme používat [službu Azure SignalR](/azure/azure-signalr) . Služba umožňuje horizontální navýšení kapacity aplikace Blazor serveru na velký počet souběžných připojení SignalR. Rychlé relace jsou povolené pro službu Azure SignalR tím, že nastavíte možnost `ServerStickyMode` služby nebo hodnotu konfigurace na `Required`. Další informace najdete v tématu <xref:host-and-deploy/blazor/server#signalr-configuration>.

Při použití služby IIS jsou v rámci směrování žádostí na aplikace povoleny rychlé relace. Další informace najdete v tématu [Vyrovnávání zatížení HTTP pomocí směrování žádostí na aplikace](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing).

#### <a name="reflect-the-connection-state-in-the-ui"></a>Odrážet stav připojení v uživatelském rozhraní

Když klient zjistí, že došlo ke ztrátě připojení, zobrazí se uživateli výchozí uživatelské rozhraní, zatímco se klient pokusí znovu připojit. Pokud se opětovné připojení nepovede, uživateli se zobrazí možnost opakovat akci.

Chcete-li přizpůsobit uživatelské rozhraní, definujte element s `id` `components-reconnect-modal` v `<body>` stránky *_Host. cshtml* Razor:

```html
<div id="components-reconnect-modal">
    ...
</div>
```

Následující tabulka popisuje třídy CSS použité pro `components-reconnect-modal` element.

| CSS – třída                       | Indikuje&hellip; |
| ------------------------------- | ------------------------- |
| `components-reconnect-show`     | Ztracené připojení. Klient se pokouší znovu připojit. Zobrazit modální okno. |
| `components-reconnect-hide`     | K serveru se znovu naváže aktivní připojení. Skryje modální okno. |
| `components-reconnect-failed`   | Opětovné připojení se nezdařilo, pravděpodobně kvůli selhání sítě. Chcete-li se pokusit o opětovné připojení, zavolejte `window.Blazor.reconnect()`. |
| `components-reconnect-rejected` | Opětovné připojení bylo zamítnuto. Server byl dosažen, ale odmítl připojení a stav uživatele na serveru je ztracen. Chcete-li aplikaci znovu načíst, zavolejte `location.reload()`. Tento stav připojení může mít za následek:<ul><li>Dojde k chybě okruhu na straně serveru.</li><li>Klient je dostatečně odpojený, aby server vynechal stav uživatele. Instance komponent, se kterými uživatel pracuje, jsou vyřazeny.</li><li>Server se restartuje nebo se pracovní proces aplikace recykluje.</li></ul> |

### <a name="stateful-reconnection-after-prerendering"></a>Stav opětovného připojení po předvykreslování

aplikace Blazor serveru se ve výchozím nastavení nastavují tak, aby se před vytvořením připojení klienta k serveru předvedlo uživatelské rozhraní na serveru. To je nastaveno na stránce *_Host. cshtml* Razor:

::: moniker range=">= aspnetcore-3.1"

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

::: moniker-end

::: moniker range="< aspnetcore-3.1"

```cshtml
<body>
    <app>@(await Html.RenderComponentAsync<App>(RenderMode.ServerPrerendered))</app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

::: moniker-end

`RenderMode` nakonfiguruje, jestli součást:

* Je předem vykreslen na stránku.
* Je vykreslen jako statický kód HTML na stránce nebo pokud obsahuje nezbytné informace pro spuštění aplikace Blazor od uživatelského agenta.

::: moniker range=">= aspnetcore-3.1"

| `RenderMode`        | Popis |
| ------------------- | ----------- |
| `ServerPrerendered` | Vykreslí komponentu do statického HTML a obsahuje značku pro aplikaci Blazor Server. Když se spustí uživatelský agent, tato značka se použije k zavedení Blazor aplikace. |
| `Server`            | Vykreslí značku pro aplikaci Blazor serveru. Výstup komponenty není zahrnutý. Když se spustí uživatelský agent, tato značka se použije k zavedení Blazor aplikace. |
| `Static`            | Vykreslí komponentu do statického HTML. |

::: moniker-end

::: moniker range="< aspnetcore-3.1"

| `RenderMode`        | Popis |
| ------------------- | ----------- |
| `ServerPrerendered` | Vykreslí komponentu do statického HTML a obsahuje značku pro aplikaci Blazor Server. Když se spustí uživatelský agent, tato značka se použije k zavedení Blazor aplikace. Parametry nejsou podporovány. |
| `Server`            | Vykreslí značku pro aplikaci Blazor serveru. Výstup komponenty není zahrnutý. Když se spustí uživatelský agent, tato značka se použije k zavedení Blazor aplikace. Parametry nejsou podporovány. |
| `Static`            | Vykreslí komponentu do statického HTML. Jsou podporovány parametry. |

::: moniker-end

Vykreslování součástí serveru ze statické stránky HTML není podporováno.

Když je `ServerPrerendered``RenderMode`, komponenta se zpočátku generuje jako součást stránky staticky. Jakmile prohlížeč vytvoří připojení zpátky k serveru, komponenta se *znovu*vykreslí a komponenta je teď interaktivní. Pokud je k dispozici [Metoda životního cyklu](xref:blazor/components#lifecycle-methods) pro inicializaci komponenty (`OnInitialized{Async}`), metoda je provedena *dvakrát*:

* Když se komponenta předem vykreslí.
* Po navázání připojení k serveru.

Výsledkem může být znatelné změny v datech zobrazených v uživatelském rozhraní, když je komponenta nakonec vykreslena.

Abyste se vyhnuli scénáři dvojího vykreslování v aplikaci Blazor serveru:

* Předejte identifikátor, který se dá použít k ukládání stavu do mezipaměti během předgenerování a načtení stavu po restartování aplikace.
* Použijte identifikátor při předvykreslování k uložení stavu součásti.
* Pro načtení stavu uloženého v mezipaměti použijte identifikátor po předvykreslování.

Následující kód demonstruje aktualizované `WeatherForecastService` v aplikaci Blazor serveru založeném na šablonách, která vylučuje dvojité vykreslování:

```csharp
public class WeatherForecastService
{
    private static readonly string[] Summaries = new[]
    {
        "Freezing", "Bracing", "Chilly", "Cool", "Mild",
        "Warm", "Balmy", "Hot", "Sweltering", "Scorching"
    };
    
    public WeatherForecastService(IMemoryCache memoryCache)
    {
        MemoryCache = memoryCache;
    }
    
    public IMemoryCache MemoryCache { get; }

    public Task<WeatherForecast[]> GetForecastAsync(DateTime startDate)
    {
        return MemoryCache.GetOrCreateAsync(startDate, async e =>
        {
            e.SetOptions(new MemoryCacheEntryOptions
            {
                AbsoluteExpirationRelativeToNow = 
                    TimeSpan.FromSeconds(30)
            });

            var rng = new Random();

            await Task.Delay(TimeSpan.FromSeconds(10));

            return Enumerable.Range(1, 5).Select(index => new WeatherForecast
            {
                Date = startDate.AddDays(index),
                TemperatureC = rng.Next(-20, 55),
                Summary = Summaries[rng.Next(Summaries.Length)]
            }).ToArray();
        });
    }
}
```

### <a name="render-stateful-interactive-components-from-razor-pages-and-views"></a>Vykreslovat stavově interaktivní komponenty ze stránek a zobrazení Razor

Stavové interaktivní komponenty lze přidat na stránku nebo zobrazení Razor.

Při vykreslení stránky nebo zobrazení:

* Komponenta je předem vykreslena se stránkou nebo zobrazením.
* Počáteční stav součásti, který se používá pro předvykreslování, bude ztracen.
* Po navázání SignalRho připojení se vytvoří nový stav součásti.

Následující stránka Razor vykresluje komponentu `Counter`:

::: moniker range=">= aspnetcore-3.1"

```cshtml
<h1>My Razor Page</h1>

<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-InitialValue="InitialValue" />

@code {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

::: moniker-end

::: moniker range="< aspnetcore-3.1"

```cshtml
<h1>My Razor Page</h1>

@(await Html.RenderComponentAsync<Counter>(RenderMode.ServerPrerendered))

@code {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

::: moniker-end

### <a name="render-noninteractive-components-from-razor-pages-and-views"></a>Vykreslování neinteraktivních komponent ze stránek a zobrazení Razor

Na následující stránce Razor je součást `MyComponent` staticky vykreslena s počáteční hodnotou zadanou pomocí formuláře:

::: moniker range=">= aspnetcore-3.1"

```cshtml
<h1>My Razor Page</h1>

<form>
    <input type="number" asp-for="InitialValue" />
    <button type="submit">Set initial value</button>
</form>

<component type="typeof(Counter)" render-mode="Static" 
    param-InitialValue="InitialValue" />

@code {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

::: moniker-end

::: moniker range="< aspnetcore-3.1"

```cshtml
<h1>My Razor Page</h1>

<form>
    <input type="number" asp-for="InitialValue" />
    <button type="submit">Set initial value</button>
</form>

@(await Html.RenderComponentAsync<MyComponent>(RenderMode.Static, 
    new { InitialValue = InitialValue }))

@code {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

::: moniker-end

Vzhledem k tomu, že `MyComponent` jsou staticky vykresleny, nemůže být komponenta interaktivní.

### <a name="detect-when-the-app-is-prerendering"></a>Rozpoznat, kdy se aplikace předvykresluje

[!INCLUDE[](~/includes/blazor-prerendering.md)]

### <a name="configure-the-opno-locsignalr-client-for-opno-locblazor-server-apps"></a>Konfigurace klienta SignalR pro aplikace Blazor serveru

V některých případech je třeba nakonfigurovat klienta SignalR používaného serverovými aplikacemi Blazor. Například můžete chtít nakonfigurovat protokolování klienta SignalR pro diagnostiku potíží s připojením.

Konfigurace klienta SignalR v souboru *Pages/_Host. cshtml* :

* Přidejte atribut `autostart="false"` do značky `<script>` pro skript *blazor. Server. js* .
* Zavolejte `Blazor.start` a předejte objekt konfigurace, který určuje SignalR Builder.

```html
<script src="_framework/blazor.server.js" autostart="false"></script>
<script>
  Blazor.start({
    configureSignalR: function (builder) {
      builder.configureLogging("information"); // LogLevel.Information
    }
  });
</script>
```

## <a name="additional-resources"></a>Další zdroje informací:

* <xref:blazor/get-started>
* <xref:signalr/introduction>
