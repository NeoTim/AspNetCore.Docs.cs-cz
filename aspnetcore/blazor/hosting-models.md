---
title: ASP.NET Core modely hostování Blazor
author: guardrex
description: Pochopení Blazor modelů hostování na straně klienta a na straně serveru.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 07/01/2019
uid: blazor/hosting-models
ms.openlocfilehash: bf2bce4f89e8bfe6e5aeeb4860c85a60c5eb4b7c
ms.sourcegitcommit: 8b36f75b8931ae3f656e2a8e63572080adc78513
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/05/2019
ms.locfileid: "70310397"
---
# <a name="aspnet-core-blazor-hosting-models"></a>ASP.NET Core modely hostování Blazor

Od [Daniel Skořepa](https://github.com/danroth27)

Blazor je webová architektura navržená tak, aby běžela na straně klienta v prohlížeči na bázi .NET runtime založené na webovém [sestavení](https://webassembly.org/)(*Blazor na straně klienta*) nebo na straně serveru v ASP.NET Core (*Blazor Server*). Bez ohledu na model hostování jsou modely aplikací a komponent *stejné*.

Chcete-li vytvořit projekt pro modely hostování popsané v tomto článku, přečtěte si téma <xref:blazor/get-started>.

## <a name="client-side"></a>Na straně klienta

Hlavní hostující model pro Blazor je spuštěn na straně klienta v prohlížeči na webovém sestavení. Do prohlížeče se stáhnou aplikace Blazor, její závislosti a modul runtime .NET. Aplikace se spustí přímo ve vlákně uživatelského rozhraní prohlížeče. Aktualizace uživatelského rozhraní a zpracování událostí se vyskytují v rámci stejného procesu. Prostředky aplikace se nasazují jako statické soubory na webový server nebo službu, která je schopná obsluhovat statický obsah klientům.

![Blazor na straně klienta: Aplikace Blazor běží ve vlákně uživatelského rozhraní v prohlížeči.](hosting-models/_static/client-side.png)

Chcete-li vytvořit aplikaci Blazor pomocí modelu hostování na straně klienta, použijte šablonu **aplikace Blazor WebAssembly** ([dotnet New blazorwasm](/dotnet/core/tools/dotnet-new)).

Po výběru šablony **aplikace Blazor WebAssembly** máte možnost konfigurovat aplikaci tak, aby používala ASP.NET Core back-end, a to tak, že vyberete zaškrtávací políčko **ASP.NET Core hostované** ([dotnet New blazorwasm--Hosted](/dotnet/core/tools/dotnet-new)). Aplikace ASP.NET Core obsluhuje aplikaci Blazor klientům. Klientská aplikace Blazor může komunikovat se serverem přes síť pomocí volání webového rozhraní API nebo [signalizace](xref:signalr/introduction).

Šablony obsahují skript *blazor. WebAssembly. js* , který zpracovává:

* Stažení modulu runtime .NET, aplikace a závislostí aplikace.
* Inicializace modulu runtime pro spuštění aplikace.

Model hostování na straně klienta nabízí několik výhod:

* Neexistuje žádná závislost na straně serveru .NET. Aplikace po stažení do klienta plně funguje.
* Prostředky a možnosti klienta jsou plně využité.
* Práce je ze serveru převedena na klienta.
* Pro hostování aplikace není vyžadován ASP.NET Core webový server. Jsou možné scénáře nasazení bez serveru (například poskytování aplikace z CDN).

Downsides hostování na straně klienta:

* Aplikace je omezená na možnosti prohlížeče.
* Je vyžadován klientský hardware a software (například podpora WebAssembly).
* Velikost ke stažení je větší a aplikace trvá déle, než se načtou.
* Podpora modulu runtime .NET a nástrojů je méně vyspělá. Například omezení existují v [.NET Standard](/dotnet/standard/net-standard) podpoře a ladění.

## <a name="server-side"></a>Na straně serveru

S modelem hostování na straně serveru se aplikace spouští na serveru z aplikace ASP.NET Core. Aktualizace uživatelského rozhraní, zpracování událostí a volání JavaScriptu se zpracovávají přes připojení k [signalizaci](xref:signalr/introduction) .

![Prohlížeč komunikuje s aplikací (hostovanou v aplikaci ASP.NET Core) na serveru přes připojení k signalizaci.](hosting-models/_static/server-side.png)

K vytvoření aplikace Blazor pomocí modelu hostování na straně serveru použijte šablonu **aplikace serveru** pro ASP.NET Core Blazor ([dotnet New blazorserver](/dotnet/core/tools/dotnet-new)). Aplikace ASP.NET Core hostuje serverovou aplikaci a vytvoří koncový bod signalizace, kde se klienti připojují.

Aplikace ASP.NET Core odkazuje na `Startup` třídu aplikace, která se má přidat:

* Služby na straně serveru.
* Aplikace do kanálu pro zpracování požadavků.

Skript&dagger; *blazor. Server. js* vytvoří připojení klienta. Je zodpovědností aplikace zachovat a obnovit stav aplikace podle potřeby (například v případě ztraceného síťového připojení).

Model hostování na straně serveru nabízí několik výhod:

* Velikost ke stažení je výrazně menší než aplikace na straně klienta a aplikace se načítá mnohem rychleji.
* Aplikace plně využívá možnosti serveru, včetně použití všech rozhraní API kompatibilních s rozhraním .NET Core.
* Rozhraní .NET Core na serveru se používá ke spuštění aplikace, takže stávající nástroje .NET, jako je ladění, fungují podle očekávání.
* Podporují se tenké klienty. Například aplikace na straně serveru fungují s prohlížeči, které nepodporují WebAssembly a na zařízeních s omezením prostředků.
* Základ pro .NET/C# kód aplikace, včetně kódu komponenty aplikace, není obsluhován klientům.

Existuje downsides hostování na straně serveru:

* Vyšší latence obvykle existuje. Každá interakce uživatele zahrnuje směrování sítě.
* Neexistuje žádná podpora offline. Pokud připojení klienta neproběhne úspěšně, aplikace přestane fungovat.
* Pro aplikace s mnoha uživateli je škálovatelnost náročná. Server musí spravovat více připojení klientů a zpracovávat stav klienta.
* Pro obsluhu aplikace je vyžadován ASP.NET Core Server. Scénáře nasazení bez serveru nejsou možné (například poskytování aplikace z CDN).

&dagger;Skript *blazor. Server. js* se obsluhuje z vloženého prostředku v ASP.NET Core sdíleném rozhraní.

### <a name="reconnection-to-the-same-server"></a>Opětovné připojení ke stejnému serveru

Blazor aplikace na straně serveru vyžadují, aby se k serveru mohl připojit aktivní signál. Pokud dojde ke ztrátě připojení, aplikace se pokusí znovu připojit k serveru. Pokud je stav klienta stále v paměti, klientská relace pokračuje bez ztráty stavu.
 
Když klient zjistí, že došlo ke ztrátě připojení, zobrazí se uživateli výchozí uživatelské rozhraní, zatímco se klient pokusí znovu připojit. Pokud se opětovné připojení nepovede, uživateli se zobrazí možnost opakovat akci. Chcete-li přizpůsobit uživatelské rozhraní, definujte element `components-reconnect-modal` s jako `id` svůj na stránce *_Host. cshtml* Razor. Klient aktualizuje tento prvek pomocí jedné z následujících tříd šablony stylů CSS na základě stavu připojení:
 
* `components-reconnect-show`&ndash; Zobrazit uživatelské rozhraní, které indikuje, že připojení bylo ztraceno a klient se pokouší znovu připojit.
* `components-reconnect-hide`&ndash; Klient má aktivní připojení, skryje uživatelské rozhraní.
* `components-reconnect-failed`&ndash; Opětovné připojení se nezdařilo. Chcete-li se znovu pokusit `window.Blazor.reconnect()`o opětovné připojení, zavolejte.

### <a name="stateful-reconnection-after-prerendering"></a>Stav opětovného připojení po předvykreslování
 
Blazor aplikace na straně serveru jsou nastavené ve výchozím nastavení tak, aby se před vytvořením připojení klienta k serveru předvedlo uživatelské rozhraní na serveru. To je nastavené na stránce *_Host. cshtml* Razor:
 
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
| `ServerPrerendered` | Vykreslí komponentu do statického HTML a obsahuje značku pro Blazor aplikaci na straně serveru. Po spuštění agenta uživatele se tato značka používá ke spuštění aplikace v Blazor. Parametry nejsou podporovány. |
| `Server`            | Vykreslí značku pro Blazor aplikaci na straně serveru. Výstup komponenty není zahrnutý. Po spuštění agenta uživatele se tato značka používá ke spuštění aplikace v Blazor. Parametry nejsou podporovány. |
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

### <a name="configure-the-signalr-client-for-blazor-server-side-apps"></a>Konfigurace klienta nástroje Signal pro aplikace Blazor na straně serveru
 
V některých případech je třeba nakonfigurovat klienta nástroje Signal používaného Blazor aplikacemi na straně serveru. Například můžete chtít nakonfigurovat protokolování na straně klienta signalizace, aby bylo možné diagnostikovat problém s připojením.
 
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
