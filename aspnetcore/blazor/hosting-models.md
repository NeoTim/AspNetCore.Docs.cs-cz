---
title: ASP.NET Core Blazor hostování modelů
author: guardrex
description: Seznamte se s na straně klienta i stranu serveru Blazor hostování modely.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 06/14/2019
uid: blazor/hosting-models
ms.openlocfilehash: c794daf6f33138c57500a04116a3d172f0201bd5
ms.sourcegitcommit: 4ef0362ef8b6e5426fc5af18f22734158fe587e1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/17/2019
ms.locfileid: "67152798"
---
# <a name="aspnet-core-blazor-hosting-models"></a>ASP.NET Core Blazor hostování modelů

Podle [Daniel Roth](https://github.com/danroth27)

Blazor je webové rozhraní navržené ke spuštění na straně klienta v prohlížeči na [WebAssembly](http://webassembly.org/)– na základě modulu runtime .NET (*Blazor na straně klienta*) nebo na serveru ASP.NET Core (*Blazor na straně serveru* ). Bez ohledu na modelech hostování modelu, aplikace a komponenty *jsou stejné*.

Vytvoření projektu pro modelech hostování popsané v tomto článku najdete v tématu <xref:blazor/get-started>.

## <a name="client-side"></a>Na straně klienta

Hlavní model hostingu pro Blazor je spuštěné v prohlížeči na WebAssembly straně klienta. Aplikace Blazor, jeho závislosti a modul .NET runtime se stáhnou do prohlížeče. Aplikace je proveden přímo v prohlížeči vlákno uživatelského rozhraní. Aktualizace uživatelského rozhraní a zpracování událostí dochází v rámci stejného procesu. Prostředky aplikace se nasadí jako statické soubory na webový server nebo služba je schopná obsluhuje statický obsah, na klienty.

![Blazor straně klienta: Blazor aplikace běží na vlákně uživatelského rozhraní v prohlížeči.](hosting-models/_static/client-side.png)

Chcete-li vytvořit aplikaci Blazor používá model hostování na straně klienta, použijte jednu z následujících šablon:

* **Blazor (na straně klienta)** ([nové blazor dotnet](/dotnet/core/tools/dotnet-new)) &ndash; nasazený jako sada statické soubory.
* **Blazor (ASP.NET Core v prostředí)** ([nové blazorhosted dotnet](/dotnet/core/tools/dotnet-new)) &ndash; hostované serveru ASP.NET Core. Aplikace ASP.NET Core obsluhuje Blazor aplikaci pro klienty. Aplikace na straně klienta Blazor můžete spolupracovat se serverem přes síť pomocí volání webového rozhraní API nebo [SignalR](xref:signalr/introduction).

Šablony zahrnují *blazor.webassembly.js* skript, který zpracovává:

* Stahuje se modul .NET runtime, aplikace a závislostí aplikace.
* Inicializace modulu runtime a spusťte tak aplikaci.

Model hostingu na straně klienta nabízí několik výhod. Blazor na straně klienta:

* Nemá žádné závislosti .NET na straně serveru.
* Plně využívá prostředky klienta a možnosti.
* Snižování zátěže pracovat ze serveru do klienta.
* Podporuje scénáře v režimu offline.

Existují nevýhody hostování na straně klienta. Blazor na straně klienta:

* Omezí aplikace funkcí v prohlížeči.
* Vyžaduje klienta s podporou, hardware a software (třeba podporu WebAssembly).
* Má větší velikost ke stažení a delší dobu načítání aplikace.
* Má menší modul .NET runtime a podpora nástrojů pro dospělé (například omezení [.NET Standard](/dotnet/standard/net-standard) podpory a ladění).

## <a name="server-side"></a>Na straně serveru

S model hostingu na straně serveru aplikace spuštěné na serveru z v rámci aplikace ASP.NET Core. Aktualizace uživatelského rozhraní, zpracování událostí a volání jazyka JavaScript jsou zpracovány prostřednictvím [SignalR](xref:signalr/introduction) připojení.

![V prohlížeči komunikuje s aplikaci (už je hostovaná v rámci aplikace ASP.NET Core) na serveru pomocí připojení SignalR.](hosting-models/_static/server-side.png)

Chcete-li vytvořit aplikaci Blazor používá model hostování na straně serveru, použijte ASP.NET Core **Blazor (serverové)** šablony ([nové blazorserverside dotnet](/dotnet/core/tools/dotnet-new)). Aplikace ASP.NET Core hostitelem aplikace na straně serveru a nastaví koncových bodů SignalR, ve kterém se klienti připojují.

Aplikace ASP.NET Core odkazuje aplikaci `Startup` třídy přidejte:

* Služby na straně serveru.
* Aplikace na žádost o zpracování kanálu.

*Blazor.server.js* skript&dagger; naváže připojení klienta. Je zodpovědností aplikace k zachování a obnovení stavu aplikace podle potřeby (například v případě ztráty připojení).

Model hostingu na straně serveru nabízí několik výhod:

* Má velikost výrazně menší aplikace, než aplikace na straně klienta a načte mnohem rychleji.
* Využití výhod funkce serveru, včetně použití libovolné rozhraní API .NET Core kompatibilní.
* V rozhraní .NET Core běží na serveru, takže existující .NET nástrojů, jako je ladění, funguje podle očekávání.
* Funguje s tenké klienty. Například funguje v prohlížečích, které nepodporují WebAssembly a prostředků omezené zařízení.
* .NET /C# základu kódu, včetně kódu komponenty aplikace, není poskytováni, na klienty.

Existují nevýhody hostování na straně serveru:

* Vyšší latence: Každá interakce uživatele zahrnuje směrování v síti.
* Žádná podpora pro offline: Pokud klienta nepovede, aplikace přestane fungovat.
* Snížení škálovatelnosti: Server musí spravovat připojení více klientů a zpracování stavu klienta.
* Server služby ASP.NET Core je zapotřebí pro zpracování aplikace. Nasazení bez serveru (například ze sítě CDN) není možné.

&dagger;*Blazor.server.js* skript pochází z vloženého prostředku v rámci sdílené ASP.NET Core.

### <a name="reconnection-to-the-same-server"></a>Opětovné připojení ke stejnému serveru

Blazor serverové aplikace vyžadují aktivní připojení k serveru funkce SignalR. Pokud dojde ke ztrátě připojení, aplikace se pokusí znovu připojit k serveru. Za předpokladu, stav klienta je stále v paměti, bude pokračovat bez ztráty stavu relace klienta.
 
Když klient zjistí, že připojení bylo ztraceno, je výchozí uživatelské rozhraní a klient se pokusí znovu připojit, zobrazit uživateli. Pokud opětovného připojení selže, uživateli se nabídnou možnost opakovat. Chcete-li přizpůsobit uživatelské rozhraní, definujte element s `components-reconnect-modal` jako jeho `id`. Klient aktualizuje tento element s jedním z následujících tříd šablon stylů CSS na základě stavu připojení:
 
* `components-reconnect-show` &ndash; Zobrazit uživatelské rozhraní, které označíte, se ztratí připojení a klient se pokouší o obnovení připojení.
* `components-reconnect-hide` &ndash; Klient má aktivní připojení, Skrýt uživatelské rozhraní.
* `components-reconnect-failed` &ndash; Opětovné připojení se nezdařilo. Pokusu o opětovné připojení znovu, volání `window.Blazor.reconnect()`.

### <a name="stateful-reconnection-after-prerendering"></a>Stavové opětovného připojení po dokončení fáze před vykreslením
 
Aplikace na straně serveru Blazor nastavení jsou ve výchozím nastavení prerender uživatelského rozhraní na serveru před navázáním připojení klienta k serveru. Nastavuje se to *_Host.cshtml* stránky Razor:
 
```cshtml
<body>
    <app>@(await Html.RenderComponentAsync<App>())</app>
 
    <script src="_framework/blazor.server.js"></script>
</body>
```
 
Klient znovu připojí k serveru pomocí stejného stavu, který se použil k prerender aplikace. Pokud se stav aplikace je stále v paměti, není stav komponenty rerendered po navázání připojení SignalR.

### <a name="render-stateful-interactive-components-from-razor-pages-and-views"></a>Vykreslení stavové interaktivní součásti ze stránky Razor a zobrazení
 
Stavové interaktivní komponenty lze přidat stránky Razor nebo zobrazení. Při vykreslení stránky nebo zobrazení, komponenta je předkreslených s ním. Aplikace potom se znovu připojí k stav komponenty po připojení klienta pokládáme stav, stav je stále v paměti.
 
Například následující stránky Razor vykreslí součást čítače s počáteční počet, který je určen pomocí formuláře:
 
```cshtml
<h1>My Razor Page</h1>

<form>
    <input type="number" asp-for="InitialCount" />
    <button type="submit">Set initial count</button>
</form>
 
@(await Html.RenderComponentAsync<Counter>(new { InitialCount = InitialCount }))
 
@code {
    [BindProperty(SupportsGet=true)]
    public int InitialCount { get; set; }
}
```

### <a name="detect-when-the-app-is-prerendering"></a>Rozpoznat, kdy aplikace je před vykreslením
 
[!INCLUDE[](~/includes/blazor-prerendering.md)]

### <a name="configure-the-signalr-client-for-blazor-server-side-apps"></a>Konfigurace klienta SignalR pro Blazor serverové aplikace
 
V některých případech budete muset nakonfigurovat klienta SignalR používat Blazor aplikace na straně serveru. Například můžete chtít konfigurovat protokolování na straně klienta SignalR pro diagnostiku problému připojení.
 
Abyste mohli nakonfigurovat klienta SignalR v *stránek /\_Host.cshtml* souboru:

* Přidat `autostart="false"` atribut `<script>` značky *blazor.server.js* skriptu.
* Volání `Blazor.start` a předat objekt konfigurace, který určuje Tvůrce SignalR.
 
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
