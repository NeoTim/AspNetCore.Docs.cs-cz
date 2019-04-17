---
title: Blazor směrování
author: guardrex
description: Zjistěte, jak směrovat požadavky v aplikacích a o NavLink komponentě.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 04/15/2019
uid: blazor/routing
ms.openlocfilehash: bf2a2f6218b0fb4637d60565711c08aa31cebeef
ms.sourcegitcommit: 017b673b3c700d2976b77201d0ac30172e2abc87
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2019
ms.locfileid: "59614817"
---
# <a name="blazor-routing"></a>Blazor směrování

Podle [Luke Latham](https://github.com/guardrex)

Zjistěte, jak směrovat požadavky v aplikacích a o NavLink komponentě.

## <a name="aspnet-core-endpoint-routing-integration"></a>Integrace směrování koncových bodů ASP.NET Core

Je součástí Blazor [směrování ASP.NET Core](xref:fundamentals/routing). Aplikace ASP.NET Core je nakonfigurovaný tak, aby přijímal příchozí připojení pro interaktivní součásti s `MapComponentHub<TComponent>` v `Startup.Configure`. `MapComponentHub` Určuje, že kořenová součást `App` má být vykreslen v rámci modelu DOM element odpovídající modulu pro výběr `app`:

```csharp
app.UseRouting(routes =>
{
    routes.MapRazorPages();
    routes.MapComponentHub<App>("app");
});
```

## <a name="route-templates"></a>Šablony trasy

`<Router>` Součást umožňuje směrování a je k dispozici šablona trasy pro jednotlivé dostupné komponenty. `<Router>` Součást se zobrazí v *Components/App.razor* souboru:

```cshtml
<Router AppAssembly="typeof(Program).Assembly" />
```

Při *.razor* nebo *.cshtml* soubor s `@page` – direktiva je zkompilován, dostane generované třídy <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> zadání šablonu trasy. Za běhu, směrovač hledá komponentní třídy s `RouteAttribute` a vykreslí podle toho, která komponenta má šablona trasy, která odpovídá požadovanou adresu URL.

Více šablon trasy můžete použít pro komponentu. Následující komponenty jsou reaguje na požadavky pro `/BlazorRoute` a `/DifferentBlazorRoute`:

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/BlazorRoute.cshtml?name=snippet_BlazorRoute)]

`<Router>` podporuje nastavení záložního součásti pro vykreslení při požadované trase není vyřešený. Povolit tento scénář vyjádřit výslovný souhlas tím, že nastavíte `FallbackComponent` parametru na typ třídy záložní komponenty.

Následující příklad nastaví komponentu podle *Pages/MyFallbackRazorComponent.cshtml* záložní součástí `<Router>`:

```cshtml
<Router ... FallbackComponent="typeof(Pages.MyFallbackRazorComponent)" />
```

> [!IMPORTANT]
> Ke generování tras správně, musí aplikace obsahovat `<base>` označení na jeho *wwwroot/index.html* soubor s základní cesty aplikace určené `href` atribut (`<base href="/">`). Další informace naleznete v tématu <xref:host-and-deploy/blazor/client-side#app-base-path>.

## <a name="route-parameters"></a>Parametry trasy

Směrovač používá parametry trasy k naplnění odpovídajících parametrů součásti se stejným názvem (malých písmen):

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/RouteParameter.cshtml?name=snippet_RouteParameter&highlight=2,7-8)]

Volitelné parametry nejsou podporovány, tedy dvě `@page` direktivy se použijí v předchozím příkladu. První umožňuje přechod na komponenty bez parametrů. Druhá `@page` trvá – direktiva `{text}` parametr trasa a přiřadí hodnotu do proměnné `Text` vlastnost.

## <a name="route-constraints"></a>Omezení trasy

Omezení trasy vynucuje typ odpovídající v segmentu směrování do komponenty.

V následujícím příkladu trasy, která má součásti uživatelé pouze odpovídá, pokud:

* `Id` Segment směrování je k dispozici na adrese URL požadavku.
* `Id` Segmentu je celé číslo (`int`).

[!code-cshtml[](routing/samples_snapshot/3.x/Constraint.cshtml?highlight=1)]

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

[!code-cshtml[](common/samples/3.x/BlazorSample/Shared/NavMenu.cshtml?name=snippet_NavLinks&highlight=4-6,9-11)]

Existují dva `NavLinkMatch` možnosti:

* `NavLinkMatch.All` &ndash; Určuje, že NavLink musí být v případě, že odpovídá celou adresu URL aktuální aktivní.
* `NavLinkMatch.Prefix` &ndash; Určuje, že NavLink musí být v případě, že odpovídá jakoukoli předponu adresy URL aktuální aktivní.

V předchozím příkladu Home NavLink (`href=""`) odpovídá všem adresám URL a vždy přijímá `active` třídu šablony stylů CSS. Druhý NavLink přijímá pouze `active` třídy, když uživatel navštíví komponentu Blazor trasy (`href="BlazorRoute"`).
