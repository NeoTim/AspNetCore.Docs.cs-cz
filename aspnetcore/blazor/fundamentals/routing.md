---
title: BlazorSměrování ASP.NET Core
author: guardrex
description: Naučte se směrovat požadavky v aplikacích a o komponentě NavLink.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/14/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/fundamentals/routing
ms.openlocfilehash: 2e0c1a479554b704b4a8cd87bc177d0f76ddc7e2
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2020
ms.locfileid: "88014344"
---
# <a name="aspnet-core-no-locblazor-routing"></a>BlazorSměrování ASP.NET Core

Od [Luke Latham](https://github.com/guardrex)

Naučte se směrovat požadavky a jak používat <xref:Microsoft.AspNetCore.Components.Routing.NavLink> komponentu k vytváření navigačních odkazů v Blazor aplikacích.

## <a name="aspnet-core-endpoint-routing-integration"></a>Integrace směrování ASP.NET Core Endpoint

Blazor Serverje integrován do [Směrování koncového bodu ASP.NET Core](xref:fundamentals/routing). Aplikace ASP.NET Core je nakonfigurovaná tak, aby přijímala příchozí připojení pro interaktivní komponenty <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> v `Startup.Configure` :

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

Nejtypickou konfigurací je směrování všech požadavků na Razor stránku, která funguje jako hostitel pro součást aplikace na straně serveru Blazor Server . Podle konvence je stránka *hostitele* obvykle pojmenována `_Host.cshtml` . Trasa zadaná v hostitelském souboru se nazývá *záložní trasa* , protože v porovnání s trasou funguje s nízkou prioritou. Záložní trasa se bere v úvahu v případě, že se jiné trasy neshodují. Díky tomu může aplikace používat jiné řadiče a stránky, aniž by to mělo vliv na Blazor Server aplikaci.

Informace o konfiguraci <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A> pro hostování nekořenového serveru URL najdete v tématu <xref:blazor/host-and-deploy/index#app-base-path> .

## <a name="route-templates"></a>Šablony směrování

Tato <xref:Microsoft.AspNetCore.Components.Routing.Router> součást umožňuje směrování na jednotlivé komponenty se zadanou trasou. <xref:Microsoft.AspNetCore.Components.Routing.Router>Komponenta se zobrazí v `App.razor` souboru:

```razor
<Router AppAssembly="@typeof(Startup).Assembly">
    <Found Context="routeData">
        <RouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
    </Found>
    <NotFound>
        <p>Sorry, there's nothing at this address.</p>
    </NotFound>
</Router>
```

Když `.razor` je zkompilován soubor s `@page` direktivou, je k dispozici vygenerovaná třída <xref:Microsoft.AspNetCore.Components.RouteAttribute> určující šablonu trasy.

Za běhu <xref:Microsoft.AspNetCore.Components.RouteView> komponenty:

* Přijímá <xref:Microsoft.AspNetCore.Components.RouteData> od <xref:Microsoft.AspNetCore.Components.Routing.Router> spolu s požadovanými parametry.
* Vykreslí určenou komponentu pomocí jejího rozložení (nebo volitelného výchozího rozložení) pomocí zadaných parametrů.

Volitelně můžete zadat <xref:Microsoft.AspNetCore.Components.RouteView.DefaultLayout> parametr s třídou rozložení, který se má použít pro součásti, které neurčují rozložení. Výchozí Blazor šablony určují `MainLayout` komponentu. `MainLayout.razor`je ve složce projektu šablony `Shared` . Další informace o rozložení najdete v tématu <xref:blazor/layouts> .

Pro komponentu lze použít více šablon směrování. Následující komponenta reaguje na požadavky pro `/BlazorRoute` a `/DifferentBlazorRoute` :

```razor
@page "/BlazorRoute"
@page "/DifferentBlazorRoute"

<h1>Blazor routing</h1>
```

> [!IMPORTANT]
> Aby adresy URL byly správně přeloženy, musí aplikace obsahovat `<base>` značku v `wwwroot/index.html` souboru ( Blazor WebAssembly ) nebo `Pages/_Host.cshtml` souboru ( Blazor Server ) se základní cestou aplikace zadanou v `href` atributu ( `<base href="/">` ). Další informace naleznete v tématu <xref:blazor/host-and-deploy/index#app-base-path>.

## <a name="provide-custom-content-when-content-isnt-found"></a>Poskytnutí vlastního obsahu, když se nenalezne obsah

<xref:Microsoft.AspNetCore.Components.Routing.Router>Komponenta umožňuje aplikaci zadat vlastní obsah, pokud se pro požadovanou trasu nenajde obsah.

V `App.razor` souboru nastavte vlastní obsah v <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> parametru šablony <xref:Microsoft.AspNetCore.Components.Routing.Router> součásti:

```razor
<Router AppAssembly="typeof(Startup).Assembly">
    <Found Context="routeData">
        <RouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
    </Found>
    <NotFound>
        <h1>Sorry</h1>
        <p>Sorry, there's nothing at this address.</p> b
    </NotFound>
</Router>
```

Obsah `<NotFound>` značek může zahrnovat libovolné položky, jako jsou například jiné interaktivní součásti. Chcete-li použít výchozí rozložení <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> obsahu, přečtěte si téma <xref:blazor/layouts> .

## <a name="route-to-components-from-multiple-assemblies"></a>Směrování na součásti z více sestavení

Použijte <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> parametr k určení dalších sestavení, <xref:Microsoft.AspNetCore.Components.Routing.Router> která má součást při hledání směrovatelných komponent zvážit. Zadaná sestavení jsou kromě zadaného sestavení považována za `AppAssembly` . V následujícím příkladu `Component1` je směrovatelný komponenta definovaná v odkazované knihovně tříd. Následující <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> příklad vede k podpoře směrování pro `Component1` :

```razor
<Router
    AppAssembly="@typeof(Program).Assembly"
    AdditionalAssemblies="new[] { typeof(Component1).Assembly }">
    ...
</Router>
```

## <a name="route-parameters"></a>Parametry směrování

Směrovač používá parametry směrování k naplnění odpovídajících parametrů komponenty se stejným názvem (bez rozlišení velkých a malých písmen):

```razor
@page "/RouteParameter"
@page "/RouteParameter/{text}"

<h1>Blazor is @Text!</h1>

@code {
    [Parameter]
    public string Text { get; set; }

    protected override void OnInitialized()
    {
        Text = Text ?? "fantastic";
    }
}
```

Volitelné parametry nejsou podporovány. `@page`V předchozím příkladu jsou aplikovány dvě direktivy. První umožňuje navigaci na součást bez parametru. Druhá `@page` direktiva převezme `{text}` parametr Route a přiřadí hodnotu `Text` Vlastnosti.

## <a name="route-constraints"></a>Omezení trasy

Omezení trasy vynutilo typ shodný s typem pro segment směrování do součásti.

V následujícím příkladu trasa k `Users` součásti odpovídá pouze v případě, že:

* `Id`V adrese URL požadavku je přítomen segment směrování.
* `Id`Segment je celé číslo ( `int` ).

[!code-razor[](routing/samples_snapshot/3.x/Constraint.razor?highlight=1)]

K dispozici jsou omezení tras uvedená v následující tabulce. Pro omezení trasy, která se shodují s invariantní jazykovou verzí, se podívejte na upozornění pod tabulkou, kde najdete další informace.

| Jedinečn | Příklad           | Příklady shody                                                                  | Invariantní<br>jazyková verze<br>shoda |
| ---------- | ----------------- | -------------------------------------------------------------------------------- | :------------------------------: |
| `bool`     | `{active:bool}`   | `true`, `FALSE`                                                                  | Ne                               |
| `datetime` | `{dob:datetime}`  | `2016-12-31`, `2016-12-31 7:32pm`                                                | Ano                              |
| `decimal`  | `{price:decimal}` | `49.99`, `-1,000.01`                                                             | Ano                              |
| `double`   | `{weight:double}` | `1.234`, `-1,001.01e8`                                                           | Ano                              |
| `float`    | `{weight:float}`  | `1.234`, `-1,001.01e8`                                                           | Ano                              |
| `guid`     | `{id:guid}`       | `CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}` | Ne                               |
| `int`      | `{id:int}`        | `123456789`, `-123456789`                                                        | Ano                              |
| `long`     | `{ticks:long}`    | `123456789`, `-123456789`                                                        | Ano                              |

> [!WARNING]
> Omezení směrování, která ověřují adresu URL a jsou převedena na typ CLR (například `int` nebo <xref:System.DateTime> ), vždy používají invariantní jazykovou verzi. Tato omezení předpokládají, že adresa URL nelze lokalizovat.

### <a name="routing-with-urls-that-contain-dots"></a>Směrování s adresami URL, které obsahují tečky

V Blazor Server části aplikace je výchozí trasa v `_Host.cshtml` `/` : ( `@page "/"` ). Adresa URL požadavku, která obsahuje tečku ( `.` ), není shodná s výchozí cestou, protože adresa URL se zobrazí pro vyžádání souboru. BlazorAplikace vrátí odpověď na *404, která nebyla nalezena* pro statický soubor, který neexistuje. Pokud chcete použít trasy, které obsahují tečku, nakonfigurujte `_Host.cshtml` ji pomocí následující šablony trasy:

```cshtml
@page "/{**path}"
```

`"/{**path}"`Šablona obsahuje:

* Dvojitá hvězdička *– veškerá* syntaxe ( `**` ) pro zachycení cesty mezi více hranicemi složek bez kódování lomítka ( `/` ).
* `path`název parametru trasy.

> [!NOTE]
> *Catch-All* parametr Syntax ( `*` / `**` ) není **not** v Razor součástech ( `.razor` ) podporován.

Další informace naleznete v tématu <xref:fundamentals/routing>.

## <a name="navlink-component"></a>Komponenta NavLink

Použít <xref:Microsoft.AspNetCore.Components.Routing.NavLink> komponentu namísto prvků hypertextového odkazu HTML ( `<a>` ) při vytváření navigačních odkazů. <xref:Microsoft.AspNetCore.Components.Routing.NavLink>Komponenta se chová jako `<a>` element s tím rozdílem, že přepíná `active` třídu CSS na základě toho, zda `href` odpovídá aktuální adrese URL. `active`Třída pomáhá uživateli pochopit, která stránka je aktivní stránkou mezi zobrazenými navigačními odkazy. Volitelně můžete přiřadit název třídy šablony stylů CSS k <xref:Microsoft.AspNetCore.Components.Routing.NavLink.ActiveClass?displayProperty=nameWithType> použití vlastní třídy CSS pro vykreslený odkaz, když aktuální trasa odpovídá `href` .

Následující `NavMenu` Komponenta vytvoří [`Bootstrap`](https://getbootstrap.com/docs/) navigační panel, který ukazuje, jak používat <xref:Microsoft.AspNetCore.Components.Routing.NavLink> komponenty:

[!code-razor[](routing/samples_snapshot/3.x/NavMenu.razor?highlight=4,9)]

Existují dvě <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch> Možnosti, které lze přiřadit k `Match` atributu `<NavLink>` elementu:

* <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.All?displayProperty=nameWithType>: <xref:Microsoft.AspNetCore.Components.Routing.NavLink> Je aktivní, pokud odpovídá celé aktuální adrese URL.
* <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.Prefix?displayProperty=nameWithType>(*výchozí*): <xref:Microsoft.AspNetCore.Components.Routing.NavLink> je aktivní, pokud odpovídá libovolné PŘEDPONĚ aktuální adresy URL.

V předchozím příkladu se Domovská stránka <xref:Microsoft.AspNetCore.Components.Routing.NavLink> `href=""` shoduje s adresou URL domů a přijímá pouze `active` třídu CSS ve výchozí základní cestě URL aplikace (například `https://localhost:5001/` ). Druhá <xref:Microsoft.AspNetCore.Components.Routing.NavLink> Získá třídu, `active` když uživatel navštíví libovolnou adresu URL s `MyComponent` předponou (například `https://localhost:5001/MyComponent` a `https://localhost:5001/MyComponent/AnotherSegment` ).

Další <xref:Microsoft.AspNetCore.Components.Routing.NavLink> atributy komponenty jsou předány do vykreslené značky ukotvení. V následujícím příkladu <xref:Microsoft.AspNetCore.Components.Routing.NavLink> Komponenta zahrnuje `target` atribut:

```razor
<NavLink href="my-page" target="_blank">My page</NavLink>
```

Vykresluje se následující kód HTML:

```html
<a href="my-page" target="_blank">My page</a>
```

> [!WARNING]
> Vzhledem k způsobu, jakým Blazor vykresluje podřízený obsah, vykreslování `NavLink` komponent uvnitř `for` smyčky vyžaduje proměnnou místního indexu, pokud je proměnná incrementing použita v `NavLink` obsahu komponenty (podřízené):
>
> ```razor
> @for (int c = 0; c < 10; c++)
> {
>     var current = c;
>     <li ...>
>         <NavLink ... href="@c">
>             <span ...></span> @current
>         </NavLink>
>     </li>
> }
> ```
>
> Použití proměnné indexu v tomto scénáři je požadavek pro **všechny** podřízené komponenty, které používají proměnnou smyčky v jejím [podřízeném obsahu](xref:blazor/components/index#child-content), nikoli pouze `NavLink` komponentu.
>
> Alternativně můžete použít `foreach` smyčku s <xref:System.Linq.Enumerable.Range%2A?displayProperty=nameWithType> :
>
> ```razor
> @foreach(var c in Enumerable.Range(0,10))
> {
>     <li ...>
>         <NavLink ... href="@c">
>             <span ...></span> @c
>         </NavLink>
>     </li>
> }
> ```

## <a name="uri-and-navigation-state-helpers"></a>Identifikátory URI a pomocníka pro stav navigace

Použijte <xref:Microsoft.AspNetCore.Components.NavigationManager> pro práci s identifikátory URI a navigací v kódu jazyka C#. <xref:Microsoft.AspNetCore.Components.NavigationManager>poskytuje událost a metody uvedené v následující tabulce.

| Člen | Popis |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri> | Získá aktuální absolutní identifikátor URI. |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> | Získá základní identifikátor URI (s koncovým lomítkem), který může být součástí relativních cest URI pro vytvoření absolutního identifikátoru URI. Obvykle <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> odpovídá `href` atributu v `<base>` prvku dokumentu v `wwwroot/index.html` ( Blazor WebAssembly ) nebo `Pages/_Host.cshtml` ( Blazor Server ). |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A> | Přejde k zadanému identifikátoru URI. Pokud `forceLoad` je `true` :<ul><li>Směrování na straně klienta se nepoužívá.</li><li>Prohlížeč je nucen načíst novou stránku ze serveru, bez ohledu na to, zda je identifikátor URI obvykle zpracováván směrovačem na straně klienta.</li></ul> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged> | Událost, která se aktivuje, když se změní navigační umístění |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.ToAbsoluteUri%2A> | Převede relativní identifikátor URI na absolutní identifikátor URI. |
| <span style="word-break:normal;word-wrap:normal"><xref:Microsoft.AspNetCore.Components.NavigationManager.ToBaseRelativePath%2A></span> | Vzhledem k základnímu identifikátoru URI (například identifikátor URI, který dřív vrátil <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> ), převede absolutní identifikátor URI na URI relativně k základní předponě identifikátoru URI. |

`Counter`Pokud je vybráno tlačítko, následující komponenta přejde na součást aplikace:

```razor
@page "/navigate"
@inject NavigationManager NavigationManager

<h1>Navigate in Code Example</h1>

<button class="btn btn-primary" @onclick="NavigateToCounterComponent">
    Navigate to the Counter component
</button>

@code {
    private void NavigateToCounterComponent()
    {
        NavigationManager.NavigateTo("counter");
    }
}
```

Následující komponenta zpracovává událost změny umístění při přihlášení k odběru <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged?displayProperty=nameWithType> . `HandleLocationChanged`Metoda je nepřipojená, pokud `Dispose` je volána rozhraním. Odpojování metody umožňuje uvolňování paměti komponenty.

```razor
@implements IDisposable
@inject NavigationManager NavigationManager

...

protected override void OnInitialized()
{
    NavigationManager.LocationChanged += HandleLocationChanged;
}

private void HandleLocationChanged(object sender, LocationChangedEventArgs e)
{
    ...
}

public void Dispose()
{
    NavigationManager.LocationChanged -= HandleLocationChanged;
}
```

<xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs>poskytuje následující informace o události:

* <xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>: Adresa URL nového umístění.
* <xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>: Pokud `true` Blazor byla zachycena navigace z prohlížeče. `false` <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> V důsledku toho došlo k navigaci.

Další informace o vyřazení součástí najdete v tématu <xref:blazor/components/lifecycle#component-disposal-with-idisposable> .

## <a name="query-string-and-parse-parameters"></a>Parametry řetězce dotazu a analýzy

Řetězec dotazu požadavku lze získat z <xref:Microsoft.AspNetCore.Components.NavigationManager> <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri> vlastnosti:

```razor
@inject NavigationManager Navigation

...

var query = new Uri(Navigation.Uri).Query;
```

Postup analýzy parametrů řetězce dotazu:

* Přidejte odkaz na balíček pro [Microsoft. AspNetCore. webutilities](https://www.nuget.org/packages/Microsoft.AspNetCore.WebUtilities).
* Získejte hodnotu po analýze řetězce dotazu pomocí <xref:Microsoft.AspNetCore.WebUtilities.QueryHelpers.ParseQuery%2A?displayProperty=nameWithType> .

```razor
@page "/"
@using Microsoft.AspNetCore.WebUtilities
@inject NavigationManager NavigationManager

<h1>Query string parse example</h1>

<p>Value: @queryValue</p>

@code {
    private string queryValue = "Not set";

    protected override void OnInitialized()
    {
        var query = new Uri(NavigationManager.Uri).Query;

        if (QueryHelpers.ParseQuery(query).TryGetValue("{KEY}", out var value))
        {
            queryValue = value;
        }
    }
}
```

Zástupný symbol `{KEY}` v předchozím příkladu je klíč parametrů řetězce dotazu. Například dvojice klíč-hodnota adresy URL `?ship=Tardis` používá klíč `ship` .
