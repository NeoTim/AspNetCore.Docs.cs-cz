---
title: ASP.NET Core modely hostování Blazor
author: guardrex
description: Pochopení modelů hostování serverů Blazor a Blazor pro WebAssembly
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 10/03/2019
uid: blazor/hosting-models
ms.openlocfilehash: bc3ad9c7c4731b685fc161844d9f55e51722c0ea
ms.sourcegitcommit: 73e255e846e414821b8cc20ffa3aec946735cd4e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/03/2019
ms.locfileid: "71924676"
---
# <a name="aspnet-core-blazor-hosting-models"></a>ASP.NET Core modely hostování Blazor

Od [Daniel Skořepa](https://github.com/danroth27)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazor je webová architektura navržená tak, aby běžela na straně klienta v prohlížeči v prostředí .NET runtime založeném na webovém [sestavení](https://webassembly.org/)(*Blazor WebAssembly*) nebo na straně serveru v ASP.NET Core (*Server Blazor*). Bez ohledu na model hostování jsou modely aplikací a komponent *stejné*.

Chcete-li vytvořit projekt pro modely hostování popsané v tomto článku, přečtěte si téma <xref:blazor/get-started>.

## <a name="blazor-webassembly"></a>Blazor WebAssembly

Hlavní hostující model pro Blazor je spuštěn na straně klienta v prohlížeči na webovém sestavení. Do prohlížeče se stáhnou aplikace Blazor, její závislosti a modul runtime .NET. Aplikace se spustí přímo ve vlákně uživatelského rozhraní prohlížeče. Aktualizace uživatelského rozhraní a zpracování událostí se vyskytují v rámci stejného procesu. Prostředky aplikace se nasazují jako statické soubory na webový server nebo službu, která je schopná obsluhovat statický obsah klientům.

![Blazor WebAssembly: Aplikace Blazor běží ve vlákně uživatelského rozhraní v prohlížeči.](hosting-models/_static/blazor-webassembly.png)

Chcete-li vytvořit aplikaci Blazor pomocí modelu hostování na straně klienta, použijte šablonu **aplikace Blazor WebAssembly** ([dotnet New blazorwasm](/dotnet/core/tools/dotnet-new)).

Po výběru šablony **aplikace Blazor WebAssembly** máte možnost konfigurovat aplikaci tak, aby používala ASP.NET Core back-end, a to tak, že vyberete zaškrtávací políčko **ASP.NET Core hostované** ([dotnet New blazorwasm--Hosted](/dotnet/core/tools/dotnet-new)). Aplikace ASP.NET Core obsluhuje aplikaci Blazor klientům. Aplikace WebAssembly v Blazor může komunikovat se serverem přes síť pomocí volání webového rozhraní API nebo [signalizace](xref:signalr/introduction).

Šablony obsahují skript *blazor. WebAssembly. js* , který zpracovává:

* Stažení modulu runtime .NET, aplikace a závislostí aplikace.
* Inicializace modulu runtime pro spuštění aplikace.

Model hostování WebAssembly Blazor nabízí několik výhod:

* Neexistuje žádná závislost na straně serveru .NET. Aplikace po stažení do klienta plně funguje.
* Prostředky a možnosti klienta jsou plně využité.
* Práce je ze serveru převedena na klienta.
* Pro hostování aplikace není vyžadován ASP.NET Core webový server. Jsou možné scénáře nasazení bez serveru (například poskytování aplikace z CDN).

Existuje downsides hostování WebAssembly Blazor:

* Aplikace je omezená na možnosti prohlížeče.
* Je vyžadován klientský hardware a software (například podpora WebAssembly).
* Velikost ke stažení je větší a aplikace trvá déle, než se načtou.
* Podpora modulu runtime .NET a nástrojů je méně vyspělá. Například omezení existují v [.NET Standard](/dotnet/standard/net-standard) podpoře a ladění.

## <a name="blazor-server"></a>Blazor Server

S modelem hostování serveru Blazor je aplikace spuštěná na serveru z aplikace ASP.NET Core. Aktualizace uživatelského rozhraní, zpracování událostí a volání JavaScriptu se zpracovávají přes připojení [](xref:signalr/introduction) k signalizaci.

![Prohlížeč komunikuje s aplikací (hostovanou v aplikaci ASP.NET Core) na serveru přes připojení k signalizaci.](hosting-models/_static/blazor-server.png)

Pokud chcete vytvořit aplikaci Blazor pomocí modelu hostování serveru Blazor, použijte šablonu **aplikace serveru Blazor** pro ASP.NET Core ([dotnet New blazorserver](/dotnet/core/tools/dotnet-new)). Aplikace ASP.NET Core hostuje aplikaci serveru Blazor a vytvoří koncový bod signalizace, kde se klienti připojují.

Aplikace ASP.NET Core odkazuje na `Startup` třídu aplikace, která se má přidat:

* Služby na straně serveru.
* Aplikace do kanálu pro zpracování požadavků.

Skript&dagger; *blazor. Server. js* vytvoří připojení klienta. Je zodpovědností aplikace zachovat a obnovit stav aplikace podle potřeby (například v případě ztraceného síťového připojení).

Model hostování serveru Blazor nabízí několik výhod:

* Velikost ke stažení je výrazně menší než Blazor aplikace WebAssembly a aplikace se načte mnohem rychleji.
* Aplikace plně využívá možnosti serveru, včetně použití všech rozhraní API kompatibilních s rozhraním .NET Core.
* Rozhraní .NET Core na serveru se používá ke spuštění aplikace, takže stávající nástroje .NET, jako je ladění, fungují podle očekávání.
* Podporují se tenké klienty. Například aplikace serveru Blazor fungují s prohlížeči, které nepodporují WebAssembly a na zařízeních s omezením prostředků.
* Základ pro .NET/C# kód aplikace, včetně kódu komponenty aplikace, není obsluhován klientům.

Downsides hostování serveru Blazor:

* Vyšší latence obvykle existuje. Každá interakce uživatele zahrnuje směrování sítě.
* Neexistuje žádná podpora offline. Pokud připojení klienta neproběhne úspěšně, aplikace přestane fungovat.
* Pro aplikace s mnoha uživateli je škálovatelnost náročná. Server musí spravovat více připojení klientů a zpracovávat stav klienta.
* Pro obsluhu aplikace je vyžadován ASP.NET Core Server. Scénáře nasazení bez serveru nejsou možné (například poskytování aplikace z CDN).

&dagger;Skript *blazor. Server. js* se obsluhuje z vloženého prostředku v ASP.NET Core sdíleném rozhraní.

### <a name="comparison-to-server-rendered-ui"></a>Porovnání s uživatelským rozhraním vykresleným serverem

Jedním ze způsobů, jak pochopit aplikace Blazor serveru, je pochopit, jak se liší od tradičních modelů pro vykreslování uživatelského rozhraní v aplikacích ASP.NET Core pomocí zobrazení Razor nebo Razor Pages. Oba modely používají jazyk Razor k popisu obsahu HTML, ale významně se liší v tom, jak se vykreslují kód.

Když je vykreslena stránka nebo zobrazení Razor, každý řádek kódu Razor generuje kód HTML v textovém formátu. Po vykreslení Server uvolní instanci stránky nebo zobrazení, včetně veškerého stavu, který byl vytvořen. Pokud dojde k jiné žádosti na stránku, například když se ověření serveru nepovede a zobrazí se shrnutí ověření:

* Celá stránka se znovu vykreslí do textu HTML.
* Stránka je odeslána klientovi.

Aplikace Blazor se skládá z opakovaně použitelných prvků uživatelského rozhraní s názvem *Components*. Komponenta obsahuje C# kód, značku a další komponenty. Při vykreslení komponenty Blazor vytvoří graf zahrnutých komponent podobně jako HTML nebo XML model DOM (Document Object Model) (DOM). Tento graf obsahuje stav součásti uložený ve vlastnostech a polích. Blazor vyhodnotí graf komponent pro vytvoření binární reprezentace kódu. Binární formát může být:

* Byl převeden na text HTML (během předvykreslování).
* Slouží k efektivní aktualizaci značek během pravidelného vykreslování.

Aktualizace uživatelského rozhraní v Blazor se spouští:

* Interakce s uživatelem, například výběr tlačítka.
* Aktivační události aplikace, jako je například časovač.

Graf se znovu vykreslí a počítá se *rozdíl* v uživatelském rozhraní (rozdíl). Tento rozdíl je nejmenší sada úprav modelu DOM, která je nutná k aktualizaci uživatelského rozhraní na klientovi. Rozdíl se pošle klientovi v binárním formátu a použije ho prohlížeč.

Komponenta je uvolněna poté, co uživatel z něj přejde na klienta. I když uživatel pracuje s komponentou, musí se stav komponenty (služby, prostředky) uchovávat v paměti serveru. Vzhledem k tomu, že stav mnoha součástí může být serverem současně udržován, vyčerpání paměti je problém, který je třeba řešit. Pokyny k vytvoření aplikace serveru Blazor pro zajištění nejlepšího využití paměti serveru najdete v tématu <xref:security/blazor/server>.

### <a name="circuits"></a>Spoj

Serverová aplikace Blazor je postavená na [ASP.NET Core signalizaci](xref:signalr/introduction). Každý klient komunikuje se serverem přes jedno nebo více připojení k signalizaci označovaného jako *okruh*. Okruh je Blazor abstrakce přes připojení k signalizaci, která umožňuje tolerovat dočasné síťové přerušení. Když klient Blazor uvidí, že připojení k signalizaci je odpojené, pokusí se znovu připojit k serveru pomocí nového připojení k signalizaci.

Každá obrazovka prohlížeče (karta prohlížeče nebo IFRAME), která je připojená k aplikaci Blazor Server, používá připojení k signalizaci. Toto je ještě další důležité rozdíly ve srovnání s typickými aplikacemi vygenerovanými serverem. V aplikaci vykreslené serverem se při otevření stejné aplikace v několika obrazovkách prohlížeče obvykle nepřevádí na další požadavky na prostředky na serveru. V aplikaci Blazor serveru vyžaduje Každá obrazovka prohlížeče samostatný okruh a samostatné instance stavu součásti, které se mají spravovat serverem.

Blazor má za to, že se má zavřít karta prohlížeče nebo přejít na externí adresu URL s *řádným* ukončením. V případě řádného ukončení se okruh a přidružené prostředky ihned uvolňují. Klient se může také bez problémů odpojit, například kvůli přerušení sítě. Server Blazor ukládá odpojené okruhy pro Konfigurovatelný interval, aby se mohl klient znovu připojit. Další informace najdete v části [opětovné připojení ke stejnému serveru](#reconnection-to-the-same-server) .

### <a name="ui-latency"></a>Latence uživatelského rozhraní

Latence uživatelského rozhraní je doba, kterou trvá od iniciované akce až do doby, kdy je uživatelské rozhraní aktualizováno. Menší hodnoty latence uživatelského rozhraní jsou pro aplikaci pro reakci na uživatele naprosto nezbytné. V aplikaci Blazor serveru se každou akci pošle na server, zpracováno a rozdíl v uživatelském rozhraní se pošle zpátky. V důsledku toho je latence uživatelského rozhraní součtem latence sítě a latencí serveru při zpracování akce.

Pro podnikovou aplikaci, která je omezená jenom na soukromou podnikovou síť, se většinou neprojeví vlivu na vnímání uživatele latence kvůli latenci sítě. V případě aplikace nasazené přes Internet může latence znamenat uživatele, zejména v případě, že uživatelé jsou geograficky distribuováni podrobněji.

Využití paměti může také přispět k latenci aplikace. Zvýšení využití paměti vede k častému uvolňování paměti nebo paměti stránkování na disk, přičemž obě tyto čítače zvyšují výkon aplikace a následně zvyšují latenci uživatelského rozhraní. Další informace naleznete v tématu <xref:security/blazor/server>.

Blazor serverové aplikace by měly být optimalizované pro minimalizaci latence uživatelského rozhraní tím, že se sníží latence sítě a využití paměti. Přístup k měření latence sítě najdete v tématu <xref:host-and-deploy/blazor/server#measure-network-latency>. Další informace o signalizaci a Blazor najdete v tématech:

* <xref:host-and-deploy/blazor/server>
* <xref:security/blazor/server>

### <a name="reconnection-to-the-same-server"></a>Opětovné připojení ke stejnému serveru

Serverové aplikace Blazor vyžadují připojení aktivního signálu k serveru. Pokud dojde ke ztrátě připojení, aplikace se pokusí znovu připojit k serveru. Pokud je stav klienta stále v paměti, klientská relace pokračuje bez ztráty stavu.

Když klient zjistí, že došlo ke ztrátě připojení, zobrazí se uživateli výchozí uživatelské rozhraní, zatímco se klient pokusí znovu připojit. Pokud se opětovné připojení nepovede, uživateli se zobrazí možnost opakovat akci. Chcete-li přizpůsobit uživatelské rozhraní, definujte element s `components-reconnect-modal` jako jeho `id` na stránce *_Host. cshtml* Razor. Klient aktualizuje tento prvek pomocí jedné z následujících tříd šablony stylů CSS na základě stavu připojení:

* `components-reconnect-show` &ndash; zobrazí uživatelské rozhraní pro indikaci ztraceného připojení a klient se pokouší znovu připojit.
* `components-reconnect-hide`&ndash; Klient má aktivní připojení, skryje uživatelské rozhraní.
* `components-reconnect-failed` &ndash; opětovné připojení selhalo, pravděpodobně kvůli selhání sítě. Chcete-li se pokusit o opětovné připojení, zavolejte `window.Blazor.reconnect()`.
* `components-reconnect-rejected` &ndash; opětovné připojení bylo zamítnuto. Server byl dosažen, ale odmítl připojení a stav uživatele na serveru zmizí. Chcete-li aplikaci znovu načíst, zavolejte `location.reload()`. Tento stav připojení může mít za následek:
  * Dojde k chybě v okruhu (kód na straně serveru).
  * Klient je dostatečně odpojený, aby server vynechal stav uživatele. Instance komponent, se kterými uživatel spolupracuje, jsou vyřazeny.

### <a name="stateful-reconnection-after-prerendering"></a>Stav opětovného připojení po předvykreslování

Aplikace Blazor serveru se ve výchozím nastavení nastavují tak, aby se před vytvořením připojení klienta k serveru předvedlo uživatelské rozhraní na serveru. To je nastavené na stránce *_Host. cshtml* Razor:

```cshtml
<body>
    <app>@(await Html.RenderComponentAsync<App>(RenderMode.ServerPrerendered))</app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

`RenderMode`nakonfiguruje, jestli součást:

* Je předem vykreslen na stránku.
* Je vykreslen jako statický kód HTML na stránce nebo pokud obsahuje nezbytné informace pro spuštění aplikace Blazor z uživatelského agenta.

| `RenderMode`        | Popis |
| ------------------- | ----------- |
| `ServerPrerendered` | Vykreslí komponentu do statického HTML a obsahuje značku pro Blazor serverovou aplikaci. Po spuštění agenta uživatele se tato značka používá ke spuštění aplikace v Blazor. Parametry nejsou podporovány. |
| `Server`            | Vykreslí značku pro aplikaci Blazor serveru. Výstup komponenty není zahrnutý. Po spuštění agenta uživatele se tato značka používá ke spuštění aplikace v Blazor. Parametry nejsou podporovány. |
| `Static`            | Vykreslí komponentu do statického HTML. Jsou podporovány parametry. |

Vykreslování součástí serveru ze statické stránky HTML není podporováno.

Klient se znovu připojí k serveru se stejným stavem, který se použil k proprerender aplikace. Pokud je stav aplikace stále v paměti, stav součásti nebude po navázání připojení k signalizaci znovu vykreslen.

### <a name="render-stateful-interactive-components-from-razor-pages-and-views"></a>Vykreslovat stavově interaktivní komponenty ze stránek a zobrazení Razor

Stavové interaktivní komponenty lze přidat na stránku nebo zobrazení Razor.

Při vykreslení stránky nebo zobrazení:

* Komponenta je předem vykreslena se stránkou nebo zobrazením.
* Počáteční stav součásti, který se používá pro předvykreslování, bude ztracen.
* Po navázání připojení k signalizaci se vytvoří nový stav součásti.

Následující stránka Razor vykresluje `Counter` komponentu:

```cshtml
<h1>My Razor Page</h1>

@(await Html.RenderComponentAsync<Counter>(RenderMode.ServerPrerendered))
```

### <a name="render-noninteractive-components-from-razor-pages-and-views"></a>Vykreslování neinteraktivních komponent ze stránek a zobrazení Razor

Na následující stránce `MyComponent` Razor je komponenta staticky vykreslena s počáteční hodnotou zadanou pomocí formuláře:

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

Vzhledem `MyComponent` k tomu, že se staticky vykreslují, komponenta nemůže být interaktivní.

### <a name="detect-when-the-app-is-prerendering"></a>Rozpoznat, kdy se aplikace předvykresluje

[!INCLUDE[](~/includes/blazor-prerendering.md)]

### <a name="configure-the-signalr-client-for-blazor-server-apps"></a>Konfigurace klienta nástroje Signal pro aplikace serveru Blazor

V některých případech je třeba nakonfigurovat klienta nástroje Signal, který používají aplikace Blazor Server. Například můžete chtít nakonfigurovat protokolování na straně klienta signalizace, aby bylo možné diagnostikovat problém s připojením.

Konfigurace klienta signalizace v souboru *Pages/_Host. cshtml* :

* Přidejte atribut do značky pro skript *blazor. Server. js.* `<script>` `autostart="false"`
* Zavolejte `Blazor.start` a předejte do konfiguračního objektu, který určuje tvůrce signálu.

```html
<script src="_framework/blazor.server.js" autostart="false"></script>
<script>
  Blazor.start({
    configureSignalR: function (builder) {
      builder.configureLogging(2); // LogLevel.Information
    }
  });
</script>
```

## <a name="additional-resources"></a>Další zdroje

* <xref:blazor/get-started>
* <xref:signalr/introduction>
