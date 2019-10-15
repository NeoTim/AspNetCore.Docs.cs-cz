---
title: ASP.NET Core směrování Blazor
author: guardrex
description: Naučte se směrovat požadavky v aplikacích a o komponentě NavLink.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 10/09/2019
uid: blazor/routing
ms.openlocfilehash: 31c9fd171271b5c2e0af64c3cfb0805a64252fa0
ms.sourcegitcommit: 07d98ada57f2a5f6d809d44bdad7a15013109549
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2019
ms.locfileid: "72333598"
---
# <a name="aspnet-core-blazor-routing"></a>ASP.NET Core směrování Blazor

Od [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Naučte se směrovat požadavky a používat komponentu `NavLink` k vytváření navigačních odkazů v aplikacích Blazor.

## <a name="aspnet-core-endpoint-routing-integration"></a>Integrace směrování ASP.NET Core Endpoint

Server Blazor je integrovaný do [Směrování koncového bodu ASP.NET Core](xref:fundamentals/routing). ASP.NET Core aplikace je nakonfigurovaná tak, aby přijímala příchozí připojení pro interaktivní součásti s `MapBlazorHub` v `Startup.Configure`:

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

Nejtypickou konfigurací je směrování všech požadavků na stránku Razor, která funguje jako hostitel pro součást aplikace Blazor Server na straně serveru. Podle konvence se stránka *hostitele* obvykle nazývá *_Host. cshtml*. Trasa zadaná v hostitelském souboru se nazývá *záložní trasa* , protože v porovnání s trasou funguje s nízkou prioritou. Záložní trasa se bere v úvahu v případě, že se jiné trasy neshodují. Díky tomu může aplikace používat jiné řadiče a stránky bez narušování aplikace Blazor Server.

## <a name="route-templates"></a>Šablony směrování

Komponenta `Router` umožňuje směrování do každé součásti se zadanou trasou. Komponenta `Router` se zobrazí v souboru *App. Razor* :

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

Když je zkompilován soubor *. Razor* s direktivou `@page`, vygenerovaná třída poskytuje <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> určující šablonu trasy.

Za běhu součást `RouteView`:

* Přijímá `RouteData` od `Router` spolu s požadovanými parametry.
* Vykreslí určenou komponentu pomocí jejího rozložení (nebo volitelného výchozího rozložení) pomocí zadaných parametrů.

Volitelně můžete zadat parametr `DefaultLayout` s třídou rozložení, která se má použít pro součásti, které neurčují rozložení. Výchozí šablony Blazor určují součást `MainLayout`. *MainLayout. Razor* se nachází ve *sdílené* složce projektu šablony. Další informace o rozloženích naleznete v tématu <xref:blazor/layouts>.

Pro komponentu lze použít více šablon směrování. Následující komponenta reaguje na požadavky na `/BlazorRoute` a `/DifferentBlazorRoute`:

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/BlazorRoute.razor?name=snippet_BlazorRoute)]

> [!IMPORTANT]
> Aby adresy URL byly správně přeloženy, musí aplikace zahrnovat značku `<base>` v souboru *wwwroot/index.html* (Blazor WebAssembly) nebo *Pages/_Host. cshtml* (Server Blazor) se základní cestou aplikace zadanou v atributu `href` (`<base href="/">`). Další informace najdete v tématu <xref:host-and-deploy/blazor/index#app-base-path>.

## <a name="provide-custom-content-when-content-isnt-found"></a>Poskytnutí vlastního obsahu, když se nenalezne obsah

Komponenta `Router` umožňuje aplikaci zadat vlastní obsah, pokud se pro požadovanou trasu nenajde obsah.

V souboru *App. Razor* nastavte vlastní obsah v parametru šablony `NotFound` komponenty `Router`:

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

Obsah značek `<NotFound>` může obsahovat libovolné položky, jako jsou například jiné interaktivní součásti. Pokud chcete použít výchozí rozložení pro @no__t obsahu s hodnotou 0, přečtěte si téma <xref:blazor/layouts>.

## <a name="route-to-components-from-multiple-assemblies"></a>Směrování na součásti z více sestavení

Použijte parametr `AdditionalAssemblies` k určení dalších sestavení pro komponentu `Router`, která se má při hledání směrovatelných komponent vzít v úvahu. Zadaná sestavení jsou kromě @no__tho -0-zadaného sestavení považována za. V následujícím příkladu je `Component1` směrovatelné komponenty definované v odkazované knihovně tříd. Následující příklad `AdditionalAssemblies` vede k podpoře směrování pro `Component1`:

```cshtml
<Router
    AppAssembly="typeof(Program).Assembly"
    AdditionalAssemblies="new[] { typeof(Component1).Assembly }">
    ...
</Router>
```

## <a name="route-parameters"></a>Parametry směrování

Směrovač používá parametry směrování k naplnění odpovídajících parametrů komponenty se stejným názvem (bez rozlišení velkých a malých písmen):

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/RouteParameter.razor?name=snippet_RouteParameter&highlight=2,7-8)]

Volitelné parametry nejsou podporované pro aplikace Blazor v ASP.NET Core 3,0. V předchozím příkladu jsou aplikovány dvě direktivy `@page`. První umožňuje navigaci na součást bez parametru. Druhá direktiva `@page` přijímá parametr trasy `{text}` a přiřazuje hodnotu vlastnosti `Text`.

## <a name="route-constraints"></a>Omezení trasy

Omezení trasy vynutilo typ shodný s typem pro segment směrování do součásti.

V následujícím příkladu trasa k součásti `Users` odpovídá pouze v případě, že:

* V adrese URL požadavku je přítomen segment směrování `Id`.
* Segment `Id` je celé číslo (`int`).

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
> Omezení směrování, která ověřují adresu URL a jsou převedena na typ CLR (například `int` nebo `DateTime`) vždy používají invariantní jazykovou verzi. Tato omezení předpokládají, že adresa URL nelze lokalizovat.

### <a name="routing-with-urls-that-contain-dots"></a>Směrování s adresami URL, které obsahují tečky

V aplikacích Blazor Server jsou výchozí trasy v *_Host. cshtml* `/` (`@page "/"`). Adresa URL požadavku, která obsahuje tečku (`.`), není shodná s výchozí cestou, protože adresa URL se zobrazí pro vyžádání souboru. Aplikace Blazor vrací odpověď na *404, která nebyla nalezena* pro statický soubor, který neexistuje. Chcete-li použít trasy, které obsahují tečku, nakonfigurujte *_Host. cshtml* s následující šablonou směrování:

```cshtml
@page "/{**path}"
```

Šablona `"/{**path}"` zahrnuje:

* Dvojitá hvězdička *– veškerá* syntaxe (`**`) pro zachycení cesty mezi více hranicemi složek bez kódování lomítka (`/`).
* Název parametru trasy `path`.

Další informace najdete v tématu <xref:fundamentals/routing>.

## <a name="navlink-component"></a>Komponenta NavLink

Při vytváření navigačních odkazů použijte komponentu `NavLink` místo prvků hypertextového odkazu HTML (`<a>`). Komponenta `NavLink` se chová jako prvek `<a>` s tím rozdílem, že přepíná třídu CSS `active` na základě toho, zda `href` odpovídá aktuální adrese URL. Třída `active` pomáhá uživateli pochopit, kterou stránku tvoří aktivní stránka mezi zobrazenými navigačními odkazy.

Následující součást `NavMenu` vytvoří navigační panel [bootstrap](https://getbootstrap.com/docs/) , který ukazuje, jak používat komponenty `NavLink`:

[!code-cshtml[](routing/samples_snapshot/3.x/NavMenu.razor?highlight=4,9)]

Existují dvě možnosti @no__t 0, které lze přiřadit k atributu `Match` prvku `<NavLink>`:

* `NavLinkMatch.All` &ndash; `NavLink` je aktivní, pokud odpovídá celé aktuální adrese URL.
* `NavLinkMatch.Prefix` (*výchozí*) &ndash; `NavLink` je aktivní, pokud odpovídá libovolné předponě aktuální adresy URL.

V předchozím příkladu Home `NavLink` `href=""` odpovídá domovské adrese URL a přijímá pouze třídu CSS `active` na výchozí základní cestě URL aplikace (například `https://localhost:5001/`). Druhá `NavLink` přijímá třídu `active`, pokud uživatel navštíví libovolnou adresu URL s předponou `MyComponent` (například `https://localhost:5001/MyComponent` a `https://localhost:5001/MyComponent/AnotherSegment`).

Další atributy komponenty `NavLink` jsou předány do vykreslené značky ukotvení. V následujícím příkladu obsahuje součást `NavLink` atribut `target`:

```cshtml
<NavLink href="my-page" target="_blank">My page</NavLink>
```

Vykresluje se následující kód HTML:

```html
<a href="my-page" target="_blank" rel="noopener noreferrer">My page</a>
```

## <a name="uri-and-navigation-state-helpers"></a>Identifikátory URI a pomocníka pro stav navigace

Pro práci s identifikátory URI a navigací v C# kódu použijte `Microsoft.AspNetCore.Components.NavigationManager`. `NavigationManager` poskytuje událost a metody uvedené v následující tabulce.

| Člen | Popis |
| ------ | ----------- |
| `Uri` | Získá aktuální absolutní identifikátor URI. |
| `BaseUri` | Získá základní identifikátor URI (s koncovým lomítkem), který může být součástí relativních cest URI pro vytvoření absolutního identifikátoru URI. @No__t-0 obvykle odpovídá atributu `href` na elementu `<base>` dokumentu v *wwwroot/index.html* (Blazor WebAssembly) nebo *Pages/_Host. cshtml* (Server Blazor). |
| `NavigateTo` | Přejde k zadanému identifikátoru URI. Pokud je `forceLoad` `true`:<ul><li>Směrování na straně klienta se nepoužívá.</li><li>Prohlížeč je nucen načíst novou stránku ze serveru, bez ohledu na to, zda je identifikátor URI obvykle zpracováván směrovačem na straně klienta.</li></ul> |
| `LocationChanged` | Událost, která se aktivuje, když se změní navigační umístění |
| `ToAbsoluteUri` | Převede relativní identifikátor URI na absolutní identifikátor URI. |
| `ToBaseRelativePath` | Vzhledem k základnímu identifikátoru URI (například identifikátor URI, který dříve vrátil `GetBaseUri`), převede absolutní identifikátor URI na identifikátor URI relativní k předponě základního identifikátoru URI. |

Pokud je vybráno tlačítko, přejde následující komponenta na součást `Counter` aplikace:

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
