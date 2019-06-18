---
title: ASP.NET Core Blazor směrování
author: guardrex
description: Zjistěte, jak směrovat požadavky v aplikacích a o NavLink komponentě.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 06/14/2019
uid: blazor/routing
ms.openlocfilehash: 4aba864c4d780591fb91b216eb128b9bf26a1662
ms.sourcegitcommit: 4ef0362ef8b6e5426fc5af18f22734158fe587e1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/17/2019
ms.locfileid: "67152765"
---
# <a name="aspnet-core-blazor-routing"></a>ASP.NET Core Blazor směrování

Podle [Luke Latham](https://github.com/guardrex)

Zjistěte, jak směrovat požadavky v aplikacích a o NavLink komponentě.

## <a name="aspnet-core-endpoint-routing-integration"></a>Integrace směrování koncových bodů ASP.NET Core

Blazor na straně serveru je integrovaná do [směrování ASP.NET Core koncový bod](xref:fundamentals/routing). Aplikace ASP.NET Core je nakonfigurovaný tak, aby přijímal příchozí připojení pro interaktivní součásti s `MapBlazorHub` v `Startup.Configure`:

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

## <a name="route-templates"></a>Šablony trasy

`<Router>` Součást umožňuje směrování a je k dispozici šablona trasy pro jednotlivé dostupné komponenty. `<Router>` Součást se zobrazí v *App.razor* souboru:

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

`<Router>` podporuje nastavení záložního součásti pro vykreslení při požadované trase není vyřešený. Povolit tento scénář vyjádřit výslovný souhlas tím, že nastavíte `FallbackComponent` parametru na typ třídy záložní komponenty.

Následující příklad nastaví komponentu podle *Pages/MyFallbackRazorComponent.razor* záložní součástí `<Router>`:

```cshtml
<Router ... FallbackComponent="typeof(Pages.MyFallbackRazorComponent)" />
```

> [!IMPORTANT]
> Ke generování tras správně, musí aplikace obsahovat `<base>` označení na jeho *wwwroot/index.html* souboru (Blazor straně klienta) nebo *stránek /\_Host.cshtml* (Blazor serverové) soubor s Základní cesta aplikace podle `href` atribut (`<base href="/">`). Další informace naleznete v tématu <xref:host-and-deploy/blazor/client-side#app-base-path>.

## <a name="route-parameters"></a>Parametry trasy

Směrovač používá parametry trasy k naplnění odpovídajících parametrů součásti se stejným názvem (malých písmen):

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/RouteParameter.razor?name=snippet_RouteParameter&highlight=2,7-8)]

Volitelné parametry nejsou podporovány pro Blazor aplikace v ASP.NET Core 3.0 ve verzi Preview. Dvě `@page` direktivy se použijí v předchozím příkladu. První umožňuje přechod na komponenty bez parametrů. Druhá `@page` trvá – direktiva `{text}` parametr trasa a přiřadí hodnotu do proměnné `Text` vlastnost.

## <a name="route-constraints"></a>Omezení trasy

Omezení trasy vynucuje typ odpovídající v segmentu směrování do komponenty.

V následujícím příkladu trasy, která má součásti uživatelé pouze odpovídá, pokud:

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

Použít komponentu NavLink namísto HTML `<a>` prvky při vytváření navigačních odkazů. Komponentu NavLink se chová jako `<a>` elementu, s výjimkou přepíná `active` třídu šablony stylů CSS podle toho, jestli jeho `href` odpovídá aktuální adresa URL. `active` Třídy pomáhá uživateli vědět, na stránce, které je aktivní stránkou. mezi navigační odkazy zobrazí.

Vytvoří následující komponenty NavMenu [Bootstrap](https://getbootstrap.com/docs/) navigační panel, který ukazuje, jak používat NavLink komponenty:

[!code-cshtml[](common/samples/3.x/BlazorSample/Shared/NavMenu.razor?name=snippet_NavLinks&highlight=4-6,9-11)]

Existují dva `NavLinkMatch` možnosti:

* `NavLinkMatch.All` &ndash; Určuje, že NavLink musí být v případě, že odpovídá celou adresu URL aktuální aktivní.
* `NavLinkMatch.Prefix` &ndash; Určuje, že NavLink musí být v případě, že odpovídá jakoukoli předponu adresy URL aktuální aktivní.

V předchozím příkladu Home NavLink (`href=""`) odpovídá všem adresám URL a vždy přijímá `active` třídu šablony stylů CSS. Druhý NavLink přijímá pouze `active` třídy, když uživatel navštíví komponentu Blazor trasy (`href="BlazorRoute"`).

## <a name="uri-and-navigation-state-helpers"></a>Identifikátor URI a navigační stav pomocné rutiny

Použití `Microsoft.AspNetCore.Components.IUriHelper` pro práci s identifikátory URI a navigace ve C# kódu. `IUriHelper` poskytuje události a metody uvedené v následující tabulce.

| Člen | Popis |
| ------ | ----------- |
| `GetAbsoluteUri` | Získá aktuální absolutní identifikátor URI. |
| `GetBaseUri` | Získá základní identifikátor URI (s koncovým lomítkem), který může být před relativní cesty URI k vytvoření absolutního identifikátoru URI. Obvykle `GetBaseUri` odpovídá `href` atribut v dokumentu `<base>` prvek *wwwroot/index.html* (Blazor straně klienta) nebo *stránek /\_Host.cshtml* (Blazor – na straně serveru). |
| `NavigateTo` | Přejde na zadaný identifikátor URI. Pokud `forceLoad` je `true`:<ul><li>Směrování na straně klienta se přeskočí.</li><li>Prohlížeč musí načíst nová stránka ze serveru, zda identifikátor URI obvykle zpracovává směrovače na straně klienta.</li></ul> |
| `OnLocationChanged` | Událost, která je vyvoláno, když se změní umístění navigace. |
| `ToAbsoluteUri` | Převede relativní identifikátor URI na absolutní adresu URI. |
| `ToBaseRelativePath` | Zadaný základní identifikátor URI (například identifikátor URI předtím vrátila rutina `GetBaseUri`), převede na identifikátor URI relativní k základní identifikátor URI předponu absolutní identifikátor URI. |

Následující komponenty přejde na součást aplikace čítače, při výběru tlačítka:

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
