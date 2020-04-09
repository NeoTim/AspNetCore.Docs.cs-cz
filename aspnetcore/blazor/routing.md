---
title: ASP.NET Blazor základní směrování
author: guardrex
description: Přečtěte si, jak směrovat požadavky v aplikacích a o komponentě NavLink.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/17/2020
no-loc:
- Blazor
- SignalR
uid: blazor/routing
ms.openlocfilehash: 87579c88a37e0258921e199db2b5d8c7627f5499
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80218892"
---
# <a name="aspnet-core-blazor-routing"></a>ASP.NET Core Blazor směrování

Podle [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Přečtěte si, jak směrovat požadavky `NavLink` a jak pomocí komponenty vytvářet navigační odkazy v aplikacích Blazor.

## <a name="aspnet-core-endpoint-routing-integration"></a>ASP.NET integrace směrování koncových bodů jádra

Blazor Server je integrován do [ASP.NET směrování koncových bodů](xref:fundamentals/routing). Aplikace ASP.NET Core je nakonfigurována tak, aby `MapBlazorHub` `Startup.Configure`přijímala příchozí připojení pro interaktivní součásti s aplikací v :

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

Nejtypičtější konfigurace je směrovat všechny požadavky na stránku Razor, která funguje jako hostitel pro část aplikace Blazor Server na straně serveru. Podle konvence je stránka *hostitele* obvykle pojmenována *_Host.cshtml*. Trasa zadaná v hostitelském souboru se nazývá *záložní trasa,* protože pracuje s nízkou prioritou při porovnávání tras. Záložní trasa je považována za jiné trasy, které se neshodují. To umožňuje aplikaci používat jiné řadiče a stránky bez zasahování do aplikace Blazor Server.

## <a name="route-templates"></a>Šablony tras

Součást `Router` umožňuje směrování ke každé součásti se zadanou trasou. Komponenta `Router` se zobrazí v souboru *App.razor:*

```razor
<Router AppAssembly="typeof(Startup).Assembly">
    <Found Context="routeData">
        <RouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
    </Found>
    <NotFound>
        <p>Sorry, there's nothing at this address.</p>
    </NotFound>
</Router>
```

Při kompilaci souboru `@page` *.razor* se směrnicí je vygenerovaná třída poskytnuta <xref:Microsoft.AspNetCore.Components.RouteAttribute> určující šablonu trasy.

Za běhu komponenta: `RouteView`

* Přijímá `RouteData` od `Router` spolu s požadovanými parametry.
* Vykreslí zadanou komponentu s jejím rozložením (nebo volitelným výchozím rozložením) pomocí zadaných parametrů.

Volitelně můžete zadat `DefaultLayout` parametr s třídou rozvržení, který se použije pro součásti, které neurčují rozložení. Výchozí šablony Blazor určují `MainLayout` komponentu. *MainLayout.razor* je ve *sdílené* složce projektu šablony. Další informace o rozloženích <xref:blazor/layouts>naleznete v tématu .

Na komponentu lze použít více šablon tras. Následující složka reaguje na požadavky `/BlazorRoute` `/DifferentBlazorRoute`a:

```razor
@page "/BlazorRoute"
@page "/DifferentBlazorRoute"

<h1>Blazor routing</h1>
```

> [!IMPORTANT]
> Aby se adresy URL správně vyřešily, `<base>` musí aplikace do svého *souboru wwwroot/index.html* (Blazor WebAssembly) nebo *stránky/_Host.cshtml* (Blazor Server) obsahovat značku se základní cestou aplikace zadanou v atributu `href` (`<base href="/">`). Další informace naleznete v tématu <xref:host-and-deploy/blazor/index#app-base-path>.

## <a name="provide-custom-content-when-content-isnt-found"></a>Poskytnutí vlastního obsahu v případě, že obsah není nalezen

Komponenta `Router` umožňuje aplikaci určit vlastní obsah, pokud obsah není nalezen pro požadovanou trasu.

V souboru *App.razor* nastavte vlastní `NotFound` obsah v `Router` parametru šablony komponenty:

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

Obsah `<NotFound>` značek může obsahovat libovolné položky, například jiné interaktivní součásti. Pokud chcete použít `NotFound` výchozí rozložení <xref:blazor/layouts>obsahu, přečtěte si další informace o použití.

## <a name="route-to-components-from-multiple-assemblies"></a>Směrovat na součásti z více sestav

`AdditionalAssemblies` Parametr slouží k určení dalších `Router` sestav pro komponentu, která je třeba vzít v úvahu při hledání směrovatelných součástí. Zadaná sestavení jsou zvažována vedle `AppAssembly`-specifikované sestavy. V následujícím příkladu `Component1` je směrovatelná komponenta definovaná v knihovně odkazovaných tříd. Následující `AdditionalAssemblies` příklad má za `Component1`následek podporu směrování pro :

```razor
<Router
    AppAssembly="typeof(Program).Assembly"
    AdditionalAssemblies="new[] { typeof(Component1).Assembly }">
    ...
</Router>
```

## <a name="route-parameters"></a>Parametry trasy

Směrovač používá parametry trasy k naplnění odpovídajících parametrů komponenty se stejným názvem (malá a velká písmena):

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

Volitelné parametry nejsou podporovány. V `@page` předchozím příkladu se používají dvě direktivy. První umožňuje navigaci do komponenty bez parametru. Druhá `@page` směrnice přebírá `{text}` parametr trasy a přiřazuje hodnotu vlastnosti. `Text`

## <a name="route-constraints"></a>Omezení trasy

Omezení trasy vynucuje shodu typů v segmentu trasy s komponentou.

V následujícím příkladu se `Users` trasa k komponentě shoduje pouze v případě, že:

* Segment `Id` trasy je k dispozici na adrese URL požadavku.
* Segment `Id` je celé číslo`int`( ).

[!code-razor[](routing/samples_snapshot/3.x/Constraint.razor?highlight=1)]

Omezení trasy uvedená v následující tabulce jsou k dispozici. Omezení trasy, která odpovídají invariantní jazykové verzi, naleznete v upozornění pod tabulkou další informace.

| Omezení | Příklad           | Příklady shod                                                                  | Invariantní<br>jazyková verze<br>shoda |
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
> Omezení trasy, která ověřují adresu URL a jsou `int` `DateTime`převedena na typ CLR (například nebo ) vždy používají invariantní jazykovou verzi. Tato omezení předpokládají, že adresu URL je nelokalizovatelné.

### <a name="routing-with-urls-that-contain-dots"></a>Směrování pomocí adres URL obsahujících tečky

V aplikacích Blazor Server je `/` výchozí trasa`@page "/"`v *_Host.cshtml* ( ). Adresa URL požadavku, která`.`obsahuje tečku ( ), není ve výchozím směru spárována, protože se zobrazí adresa URL, která požaduje soubor. Aplikace Blazor vrátí odpověď *404 - Not Found* pro statický soubor, který neexistuje. Chcete-li použít trasy, které obsahují tečku, nakonfigurujte *_Host.cshtml* pomocí následující šablony trasy:

```cshtml
@page "/{**path}"
```

Šablona `"/{**path}"` obsahuje:

* Syntaxe *catch-all* s dvojitou hvězdičkou (`**`) pro zachycení cesty přes`/`hranice více složek bez kódování lomítka ( ).
* `path`název parametru trasy.

> [!NOTE]
> *Syntaxe parametrů catch-all* `*`/`**`( ) **není** podporována v komponentách Razor (*.razor*).

Další informace naleznete v tématu <xref:fundamentals/routing>.

## <a name="navlink-component"></a>Komponenta NavLink

Při `NavLink` vytváření navigačních odkazů použijte`<a>`komponentu místo elementů hypertextového odkazu HTML ( ). Komponenta `NavLink` se chová `<a>` jako prvek, s výjimkou `active` přepíná třídu `href` CSS na základě toho, zda odpovídá aktuální adrese URL. Třída `active` pomáhá uživateli pochopit, která stránka je aktivní stránka mezi zobrazené navigační odkazy.

Následující `NavMenu` komponenta vytvoří navigační panel [Bootstrap,](https://getbootstrap.com/docs/) `NavLink` který ukazuje, jak používat součásti:

[!code-razor[](routing/samples_snapshot/3.x/NavMenu.razor?highlight=4,9)]

Atributu `NavLinkMatch` prvku můžete přiřadit `Match` dvě možnosti: `<NavLink>`

* `NavLinkMatch.All`&ndash; Je `NavLink` aktivní, pokud odpovídá celé aktuální adrese URL.
* `NavLinkMatch.Prefix`(*výchozí*) &ndash; Je `NavLink` aktivní, pokud odpovídá libovolné předponě aktuální adresy URL.

V předchozím příkladu home `NavLink` `href=""` odpovídá domácí adrese URL `active` a obdrží pouze třídu CSS na výchozí `https://localhost:5001/`adrese URL základní cesty aplikace (například). Druhý `NavLink` obdrží `active` třídu, když uživatel navštíví `MyComponent` libovolnou adresu URL `https://localhost:5001/MyComponent` `https://localhost:5001/MyComponent/AnotherSegment`s předponou (například a ).

Další `NavLink` atributy komponenty jsou předány do vykreslené značky ukotvení. V následujícím příkladu `NavLink` komponenta `target` obsahuje atribut:

```razor
<NavLink href="my-page" target="_blank">My page</NavLink>
```

Vykreslují se následující značky HTML:

```html
<a href="my-page" target="_blank" rel="noopener noreferrer">My page</a>
```

## <a name="uri-and-navigation-state-helpers"></a>Pomocníci stavu uri a navigace

Slouží <xref:Microsoft.AspNetCore.Components.NavigationManager> k práci s identifikátory URI a navigace v kódu Jazyka C#. `NavigationManager`obsahuje událost a metody uvedené v následující tabulce.

| Člen | Popis |
| ------ | ----------- |
| Uri | Získá aktuální absolutní identifikátor URI. |
| Baseuri | Získá základní identifikátor URI (s koncovým lomítkem), který lze předřadit k relativním cestám IDENTIFIKÁTORURI k vytvoření absolutního identifikátoru URI. `BaseUri` Obvykle odpovídá atributu `href` prvku dokumentu `<base>` v *wwwroot/index.html* (WebAssembly)Blazor nebo *Pages/_Host.cshtml* (Server).Blazor |
| Přejít | Přejde na zadaný identifikátor URI. Pokud `forceLoad` `true`je:<ul><li>Směrování na straně klienta je vynecháno.</li><li>Prohlížeč je nucen načíst novou stránku ze serveru bez ohledu na to, zda je identifikátor URI normálně zpracován směrovačem na straně klienta.</li></ul> |
| UmístěníZměněno | Událost, která se aktivuje při změně umístění navigace. |
| ToAbsoluteUri | Převede relativní identifikátor URI na absolutní identifikátor URI. |
| <span style="word-break:normal;word-wrap:normal">ToBaseRelativePath</span> | Vzhledem k tomu, základní URI (například URI dříve vrácena `GetBaseUri`) převede absolutní IDENTIFIKÁTOR URI na identifikátor URI vzhledem k základní předponě URI. |

Následující komponenta přejde na `Counter` komponentu aplikace, když je tlačítko vybrané:

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

Následující součást zpracovává událost změněné umístění. Metoda `HandleLocationChanged` je unhooked `Dispose` při volání rámci. Uvolnění maze metoda umožňuje uvolnění paměti komponenty.

```razor
@implement IDisposable
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

* <xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>&ndash; Adresa URL nového umístění.
* <xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>&ndash; Pokud `true` Blazor , zachytil navigaci z prohlížeče. Pokud `false`, [NavigationManager.NavigateTo](xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A) způsobil navigaci dojít.

Další informace o likvidaci <xref:blazor/lifecycle#component-disposal-with-idisposable>součástí naleznete v tématu .
