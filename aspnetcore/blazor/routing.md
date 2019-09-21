---
title: ASP.NET Core směrování Blazor
author: guardrex
description: Naučte se směrovat požadavky v aplikacích a o komponentě NavLink.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/06/2019
uid: blazor/routing
ms.openlocfilehash: 6d9d1614b6e0cc9f4711de0db4513ada4841809f
ms.sourcegitcommit: e5a74f882c14eaa0e5639ff082355e130559ba83
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/20/2019
ms.locfileid: "71168186"
---
# <a name="aspnet-core-blazor-routing"></a>ASP.NET Core směrování Blazor

Podle [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Naučte se směrovat požadavky a používat `NavLink` komponentu k vytváření navigačních odkazů v aplikacích Blazor.

## <a name="aspnet-core-endpoint-routing-integration"></a>Integrace směrování ASP.NET Core Endpoint

Server Blazor je integrovaný do [Směrování koncového bodu ASP.NET Core](xref:fundamentals/routing). Aplikace ASP.NET Core je nakonfigurovaná tak, aby přijímala příchozí připojení pro `MapBlazorHub` interaktivní `Startup.Configure`komponenty v:

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

## <a name="route-templates"></a>Šablony směrování

Tato `Router` součást umožňuje směrování na jednotlivé komponenty se zadanou trasou. Komponenta se zobrazí v souboru *App. Razor:* `Router`

```cshtml
<Router AppAssembly="typeof(Startup).Assembly">
    <Found Context="routeData">
        <RouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
    </Found>
    <NotFound>
        <p>Sorry, there's nothing at this address.</p>
    </NotFound>
</Router>
```

Je-li soubor *. Razor* s `@page` direktivou kompilován, je k dispozici vygenerovaná <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> třída, která určuje šablonu trasy.

Za běhu `RouteView` komponenty:

* `RouteData` Přijímá`Router` od spolu s požadovanými parametry.
* Vykreslí určenou komponentu pomocí jejího rozložení (nebo volitelného výchozího rozložení) pomocí zadaných parametrů.

Volitelně můžete zadat `DefaultLayout` parametr s třídou rozložení, který se má použít pro součásti, které neurčují rozložení. Výchozí šablony Blazor určují `MainLayout` komponentu. *MainLayout. Razor* se nachází ve *sdílené* složce projektu šablony. Další informace o rozložení najdete v tématu <xref:blazor/layouts>.

Pro komponentu lze použít více šablon směrování. Následující komponenta reaguje na požadavky pro `/BlazorRoute` a: `/DifferentBlazorRoute`

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/BlazorRoute.razor?name=snippet_BlazorRoute)]

> [!IMPORTANT]
> Aby adresy URL byly správně přeloženy, musí aplikace zahrnovat `<base>` značku v souboru *wwwroot/index.html* (Blazor WebAssembly) nebo *Pages/_Host. cshtml* (Server Blazor) se základní cestou `href` aplikace zadanou v atributu (`<base href="/">`). Další informace naleznete v tématu <xref:host-and-deploy/blazor/index#app-base-path>.

## <a name="provide-custom-content-when-content-isnt-found"></a>Poskytnutí vlastního obsahu, když se nenalezne obsah

`Router` Komponenta umožňuje aplikaci zadat vlastní obsah, pokud se pro požadovanou trasu nenajde obsah.

V souboru *App. Razor* nastavte vlastní obsah v `NotFound` parametru `Router` šablony součásti:

```cshtml
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

Obsah `<NotFound>` značek může zahrnovat libovolné položky, jako jsou například jiné interaktivní součásti. Chcete-li použít výchozí rozložení `NotFound` obsahu, přečtěte si téma. <xref:blazor/layouts>

## <a name="route-to-components-from-multiple-assemblies"></a>Směrování na součásti z více sestavení

Použijte parametr k určení dalších sestavení, `Router` která má součást při hledání směrovatelných komponent zvážit. `AdditionalAssemblies` Zadaná sestavení jsou kromě `AppAssembly`zadaného sestavení považována za. V následujícím příkladu `Component1` je směrovatelný komponenta definovaná v odkazované knihovně tříd. Následující `AdditionalAssemblies` příklad vede k podpoře směrování pro `Component1`:

< router AppAssembly = "typeof (program). Assembly "AdditionalAssemblies =" New [] {typeof (Component1). Sestavení} >...</Router>

## <a name="route-parameters"></a>Parametry směrování

Směrovač používá parametry směrování k naplnění odpovídajících parametrů komponenty se stejným názvem (bez rozlišení velkých a malých písmen):

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/RouteParameter.razor?name=snippet_RouteParameter&highlight=2,7-8)]

Volitelné parametry nejsou podporované pro aplikace Blazor ve verzi ASP.NET Core 3,0 Preview. V `@page` předchozím příkladu jsou aplikovány dvě direktivy. První umožňuje navigaci na součást bez parametru. Druhá `@page` direktiva `{text}` převezme parametr Route a `Text` přiřadí hodnotu vlastnosti.

## <a name="route-constraints"></a>Omezení trasy

Omezení trasy vynutilo typ shodný s typem pro segment směrování do součásti.

V následujícím příkladu trasa k `Users` součásti odpovídá pouze v případě, že:

* V adrese URL požadavku je přítomen segment směrování.`Id`
* Segment je celé číslo (`int`). `Id`

[!code-cshtml[](routing/samples_snapshot/3.x/Constraint.razor?highlight=1)]

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
> Omezení směrování, která ověřují adresu URL a jsou převedena na typ CLR (například `int` nebo `DateTime`), vždy používají invariantní jazykovou verzi. Tato omezení předpokládají, že adresa URL nelze lokalizovat.

### <a name="routing-with-urls-that-contain-dots"></a>Směrování s adresami URL, které obsahují tečky

V aplikacích Blazor Server jsou výchozí trasy v *_Host. cshtml* `/` (`@page "/"`). Adresa URL požadavku, která obsahuje tečku`.`(), není shodná s výchozí cestou, protože adresa URL se zobrazí pro vyžádání souboru. Aplikace Blazor vrací odpověď na *404, která nebyla nalezena* pro statický soubor, který neexistuje. Chcete-li použít trasy, které obsahují tečku, nakonfigurujte *_Host. cshtml* s následující šablonou směrování:

```cshtml
@page "/{**path}"
```

`"/{**path}"` Šablona obsahuje:

* Dvojitá hvězdička *– veškerá* syntaxe (`**`) pro zachycení cesty mezi více hranicemi složek bez kódování lomítka (`/`).
* Název `path` parametru trasy.

Další informace naleznete v tématu <xref:fundamentals/routing>.

## <a name="navlink-component"></a>Komponenta NavLink

Použít komponentu namísto prvků hypertextového odkazu HTML (`<a>`) při vytváření navigačních odkazů. `NavLink` Komponenta se chová `<a>` jako element `active` s tím rozdílem, že přepíná třídu CSS na základě toho, `href` zda odpovídá aktuální adrese URL. `NavLink` `active` Třída pomáhá uživateli pochopit, která stránka je aktivní stránkou mezi zobrazenými navigačními odkazy.

Následující `NavMenu` komponenta vytvoří navigační panel [bootstrap](https://getbootstrap.com/docs/) , který ukazuje, jak používat `NavLink` komponenty:

[!code-cshtml[](routing/samples_snapshot/3.x/NavMenu.razor?highlight=4,9)]

Existují dvě `NavLinkMatch` možnosti, které lze přiřadit `Match` k atributu `<NavLink>` elementu:

* `NavLinkMatch.All`&ndash; Jeaktivní,pokudodpovídá`NavLink` celé aktuální adrese URL.
* `NavLinkMatch.Prefix`(*výchozí*) &ndash; Jeaktivní,pokudodpovídálibovolnépředponě`NavLink` aktuální adresy URL.

V předchozím příkladu se Domovská stránka `NavLink` `href=""` shoduje s `active` adresou URL domů a přijímá pouze třídu CSS ve `https://localhost:5001/`výchozí základní cestě URL aplikace (například). Druhá `NavLink` Získá třídu, `active` když uživatel `MyComponent` navštíví libovolnou adresu URL s předponou (například `https://localhost:5001/MyComponent` a `https://localhost:5001/MyComponent/AnotherSegment`).

Další `NavLink` atributy komponenty jsou předány do vykreslené značky ukotvení. V následujícím příkladu `NavLink` komponenta `target` zahrnuje atribut:

```cshtml
<NavLink href="my-page" target="_blank">My page</NavLink>
```

Vykresluje se následující kód HTML:

```html
<a href="my-page" target="_blank" rel="noopener noreferrer">My page</a>
```

## <a name="uri-and-navigation-state-helpers"></a>Identifikátory URI a pomocníka pro stav navigace

Použijte `Microsoft.AspNetCore.Components.NavigationManager` pro práci s identifikátory URI a navigací v C# kódu. `NavigationManager`poskytuje událost a metody uvedené v následující tabulce.

| Člen | Popis |
| ------ | ----------- |
| `Uri` | Získá aktuální absolutní identifikátor URI. |
| `BaseUri` | Získá základní identifikátor URI (s koncovým lomítkem), který může být součástí relativních cest URI pro vytvoření absolutního identifikátoru URI. `BaseUri` Obvykle odpovídá `href` *atributu v* prvku dokumentu v wwwroot/index.html (Blazor WebAssembly) nebo *Pages/_Host. cshtml* (Server Blazor). `<base>` |
| `NavigateTo` | Přejde k zadanému identifikátoru URI. Pokud `forceLoad` je `true`:<ul><li>Směrování na straně klienta se nepoužívá.</li><li>Prohlížeč je nucen načíst novou stránku ze serveru, bez ohledu na to, zda je identifikátor URI obvykle zpracováván směrovačem na straně klienta.</li></ul> |
| `LocationChanged` | Událost, která se aktivuje, když se změní navigační umístění |
| `ToAbsoluteUri` | Převede relativní identifikátor URI na absolutní identifikátor URI. |
| `ToBaseRelativePath` | Vzhledem k základnímu identifikátoru URI (například identifikátor URI, který `GetBaseUri`dřív vrátil), převede absolutní identifikátor URI na URI relativně k základní předponě identifikátoru URI. |

Pokud je vybráno tlačítko, následující komponenta přejde `Counter` na součást aplikace:

```cshtml
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
