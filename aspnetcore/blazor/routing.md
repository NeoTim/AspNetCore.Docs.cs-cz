---
title: ASP.NET Core Blazor směrování
author: guardrex
description: Zjistěte, jak směrovat požadavky v aplikacích a o NavLink komponentě.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 07/02/2019
uid: blazor/routing
ms.openlocfilehash: d2f0ce608d7368871f508754d7bbe4f75cc9701f
ms.sourcegitcommit: 0b9e767a09beaaaa4301915cdda9ef69daaf3ff2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2019
ms.locfileid: "67538524"
---
# <a name="aspnet-core-blazor-routing"></a>ASP.NET Core Blazor směrování

Podle [Luke Latham](https://github.com/guardrex)

Naučte se směrovat požadavky a použít `NavLink` komponentu pro vytvoření navigační odkazy v aplikacích Blazor.

## <a name="aspnet-core-endpoint-routing-integration"></a>Integrace směrování koncových bodů ASP.NET Core

Blazor na straně serveru je integrovaná do [směrování ASP.NET Core koncový bod](xref:fundamentals/routing). Aplikace ASP.NET Core je nakonfigurovaný tak, aby přijímal příchozí připojení pro interaktivní součásti s `MapBlazorHub` v `Startup.Configure`:

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

## <a name="route-templates"></a>Šablony trasy

`Router` Součást umožňuje směrování a je k dispozici šablona trasy pro jednotlivé dostupné komponenty. `Router` Součást se zobrazí v *App.razor* souboru:

V aplikaci na straně serveru Blazor:

```cshtml
<Router AppAssembly="typeof(Startup).Assembly" />
```

V aplikaci na straně klienta Blazor:

```cshtml
<Router AppAssembly="typeof(Program).Assembly" />
```

Při *.razor* soubor s `@page` – direktiva je zkompilován, je k dispozici generované třídy <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> zadání šablonu trasy. Za běhu, směrovač hledá komponentní třídy s `RouteAttribute` a vykreslí komponentu šablonou trasy, která odpovídá požadovanou adresu URL.

Více šablon trasy můžete použít pro komponentu. Následující komponenty jsou reaguje na požadavky pro `/BlazorRoute` a `/DifferentBlazorRoute`:

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/BlazorRoute.razor?name=snippet_BlazorRoute)]

> [!IMPORTANT]
> Ke generování tras správně, musí aplikace obsahovat `<base>` označení na jeho *wwwroot/index.html* souboru (Blazor straně klienta) nebo *Pages/_Host.cshtml* (Blazor serverové) soubor s základní cesta aplikace zadané v poli `href` atribut (`<base href="/">`). Další informace naleznete v tématu <xref:host-and-deploy/blazor/client-side#app-base-path>.

## <a name="provide-custom-content-when-content-isnt-found"></a>Zadejte vlastní obsah, když obsah nebyl nalezen

`Router` Komponenta umožňuje aplikaci určit vlastní obsah, pokud není nalezen obsah pro požadované trase.

V *App.razor* sady vlastní obsah v souboru `<NotFoundContent>` elementu `Router` komponenty:

```cshtml
<Router AppAssembly="typeof(Startup).Assembly">
    <NotFoundContent>
        <h1>Sorry</h1>
        <p>Sorry, there's nothing at this address.</p> b
    </NotFoundContent>
</Router>
```

Obsah `<NotFoundContent>` může obsahovat libovolné položky, jako další interaktivní komponenty.

## <a name="route-parameters"></a>Parametry trasy

Směrovač používá parametry trasy k naplnění odpovídajících parametrů součásti se stejným názvem (malých písmen):

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/RouteParameter.razor?name=snippet_RouteParameter&highlight=2,7-8)]

Volitelné parametry nejsou podporovány pro Blazor aplikace v ASP.NET Core 3.0 ve verzi Preview. Dvě `@page` direktivy se použijí v předchozím příkladu. První umožňuje přechod na komponenty bez parametrů. Druhá `@page` trvá – direktiva `{text}` parametr trasa a přiřadí hodnotu do proměnné `Text` vlastnost.

## <a name="route-constraints"></a>Omezení trasy

Omezení trasy vynucuje typ odpovídající v segmentu směrování do komponenty.

V následujícím příkladu, trasy, která má `Users` komponenta odpovídá pouze, pokud:

* `Id` Segment směrování je k dispozici na adrese URL požadavku.
* `Id` Segmentu je celé číslo (`int`).

[!code-cshtml[](routing/samples_snapshot/3.x/Constraint.razor?highlight=1)]

Omezení trasy, které jsou uvedeny v následující tabulce jsou k dispozici. Omezení trasy, které odpovídají neutrální jazykové verzi najdete v upozornění níže uvedená tabulka pro další informace.

| Omezení | Příklad           | Příklad shody                                                                  | Invariantní<br>jazyková verze<br>shoda |
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
> Směrovat omezení, která Ověřte adresu URL a jsou převedeny na typ CLR (například `int` nebo `DateTime`) vždy používejte neutrální jazykovou verzi. Tato omezení předpokládá, že adresa URL je nepřekládá.

## <a name="navlink-component"></a>Komponenta NavLink

Použití `NavLink` komponentu místo elementů HTML hypertextový odkaz (`<a>`) při vytváření navigačních odkazů. A `NavLink` se chová jako součást `<a>` elementu, s výjimkou přepíná `active` třídu šablony stylů CSS podle toho, jestli jeho `href` odpovídá aktuální adresa URL. `active` Třídy pomáhá uživateli vědět, na stránce, které je aktivní stránkou. mezi navigační odkazy zobrazí.

Následující `NavMenu` vytvoří komponentu [Bootstrap](https://getbootstrap.com/docs/) navigační panel, který ukazuje, jak používat `NavLink` komponenty:

[!code-cshtml[](routing/samples_snapshot/3.x/NavMenu.razor?highlight=4,9)]

Existují dva `NavLinkMatch` možnosti, které můžete přiřadit `Match` atribut `<NavLink>` element:

* `NavLinkMatch.All` &ndash; `NavLink` Je aktivní v případě, že odpovídá celou aktuální adresu URL.
* `NavLinkMatch.Prefix` (*výchozí*) &ndash; `NavLink` je aktivní v případě, že odpovídá jakoukoli předponu adresy URL aktuální.

V předchozím příkladu, domovská stránka `NavLink` `href=""` odpovídá domovské adresy URL a přijímá pouze `active` třídu šablony stylů CSS v aplikace výchozí základní cesta URL (například `https://localhost:5001/`). Druhá `NavLink` přijímá `active` třídy, když uživatel navštíví libovolnou adresu URL s `MyComponent` předpona (například `https://localhost:5001/MyComponent` a `https://localhost:5001/MyComponent/AnotherSegment`).

## <a name="uri-and-navigation-state-helpers"></a>Identifikátor URI a navigační stav pomocné rutiny

Použití `Microsoft.AspNetCore.Components.IUriHelper` pro práci s identifikátory URI a navigace ve C# kódu. `IUriHelper` poskytuje události a metody uvedené v následující tabulce.

| Člen | Popis |
| ------ | ----------- |
| `GetAbsoluteUri` | Získá aktuální absolutní identifikátor URI. |
| `GetBaseUri` | Získá základní identifikátor URI (s koncovým lomítkem), který může být před relativní cesty URI k vytvoření absolutního identifikátoru URI. Obvykle `GetBaseUri` odpovídá `href` atribut v dokumentu `<base>` prvek *wwwroot/index.html* (Blazor straně klienta) nebo *Pages/_Host.cshtml* () Blazor-na straně serveru). |
| `NavigateTo` | Přejde na zadaný identifikátor URI. Pokud `forceLoad` je `true`:<ul><li>Směrování na straně klienta se přeskočí.</li><li>Prohlížeč musí načíst nová stránka ze serveru, zda identifikátor URI obvykle zpracovává směrovače na straně klienta.</li></ul> |
| `OnLocationChanged` | Událost, která je vyvoláno, když se změní umístění navigace. |
| `ToAbsoluteUri` | Převede relativní identifikátor URI na absolutní adresu URI. |
| `ToBaseRelativePath` | Zadaný základní identifikátor URI (například identifikátor URI předtím vrátila rutina `GetBaseUri`), převede na identifikátor URI relativní k základní identifikátor URI předponu absolutní identifikátor URI. |

Následující komponenty přejde na aplikaci `Counter` součásti při výběru tlačítka:

```cshtml
@page "/navigate"
@using Microsoft.AspNetCore.Components
@inject IUriHelper UriHelper

<h1>Navigate in Code Example</h1>

<button class="btn btn-primary" @onclick="@NavigateToCounterComponent">
    Navigate to the Counter component
</button>

@code {
    private void NavigateToCounterComponent()
    {
        UriHelper.NavigateTo("counter");
    }
}
```
