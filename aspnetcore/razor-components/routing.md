---
title: Směrování součásti syntaxe Razor
author: guardrex
description: Zjistěte, jak směrovat požadavky v aplikacích a o NavLink komponentě.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/01/2019
uid: razor-components/routing
ms.openlocfilehash: 5c648ba1bb3846f5baa515e808a98a3b33f81438
ms.sourcegitcommit: af8a6eb5375ef547a52ffae22465e265837aa82b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2019
ms.locfileid: "56159466"
---
# <a name="razor-components-routing"></a>Směrování součásti syntaxe Razor

Podle [Luke Latham](https://github.com/guardrex)

Zjistěte, jak směrovat požadavky v aplikacích a o NavLink komponentě.

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/razor-components/common/samples/) ([stažení](xref:index#how-to-download-a-sample)). Najdete v článku [Začínáme](xref:razor-components/get-started) tématu pro požadavky.

## <a name="route-templates"></a>Šablony trasy

`<Router>` Součást umožňuje směrování a je k dispozici šablona trasy pro jednotlivé dostupné komponenty. `<Router>` Součást se zobrazí v *App.cshtml* souboru:

```cshtml
<Router AppAssembly=typeof(Program).Assembly />
```

Při  *\*.cshtml* soubor s `@page` – direktiva je zkompilován, dostane generované třídy [RouteAttribute](/dotnet/api/microsoft.aspnetcore.mvc.routeattribute) zadání šablonu trasy. Za běhu, směrovač hledá komponentní třídy s `RouteAttribute` a vykreslí podle toho, která komponenta má šablona trasy, která odpovídá požadovanou adresu URL.

Více šablon trasy můžete použít pro komponentu. V [ukázkovou aplikaci](https://github.com/aspnet/Docs/tree/master/aspnetcore/razor-components/common/samples/), následující komponenty jsou reaguje na požadavky pro `/BlazorRoute` a `/DifferentBlazorRoute`.

*Pages/BlazorRoute.cshtml*:

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/BlazorRoute.cshtml?start=1&end=4)]

`<Router>` podporuje nastavení záložního součásti pro vykreslení při požadované trase není vyřešený. Povolit tento scénář vyjádřit výslovný souhlas tím, že nastavíte `FallbackComponent` parametru na typ třídy záložní komponenty.

Následující příklad nastaví komponentu podle *Pages/MyFallbackRazorComponent.cshtml* záložní součástí `<Router>`:

```cshtml
<Router ... FallbackComponent="typeof(Pages.MyFallbackRazorComponent)" />
```

> [!IMPORTANT]
> Ke generování tras správně, musí aplikace obsahovat `<base>` označení na jeho *wwwroot/index.html* soubor s základní cesty aplikace určené `href` atribut (`<base href="/" />`). Další informace najdete v tématu [hostitele a nasazení: Základní cesta aplikace](xref:host-and-deploy/razor-components/index#app-base-path).

## <a name="route-parameters"></a>Parametry trasy

Směrovač používá parametry trasy k naplnění odpovídajících parametrů součásti se stejným názvem (malých písmen).

*Pages/RouteParameter.cshtml*:

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/RouteParameter.cshtml?start=1&end=8)]

Volitelné parametry nejsou podporovány, tedy dvě `@page` direktivy se použijí v předchozím příkladu. První umožňuje přechod na komponenty bez parametrů. Druhá `@page` trvá – direktiva `{text}` parametr trasa a přiřadí hodnotu do proměnné `Text` vlastnost.

## <a name="route-constraints"></a>Omezení trasy

Omezení trasy vynucuje typ odpovídající v segmentu směrování do komponenty.

V následujícím příkladu trasy, která má součásti uživatelé pouze odpovídá, pokud:

* `Id` Segment směrování je k dispozici na adrese URL požadavku.
* `Id` Segmentu je celé číslo (`int`).

```cshtml
@page "/Users/{Id:int}"

<h1>The user Id is @Id!</h1>

@functions {
    [Parameter]
    private int Id { get; set; }
}
```

Omezení trasy, které jsou uvedeny v následující tabulce jsou k dispozici pro použití. Omezení trasy, které odpovídají neutrální jazykové verzi najdete v upozornění níže uvedená tabulka pro další informace.

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

Použít komponentu NavLink namísto HTML  **\<>** prvky při vytváření navigačních odkazů. Komponentu NavLink se chová jako  **\<>** elementu, s výjimkou přepíná `active` třídu šablony stylů CSS podle toho, jestli jeho `href` odpovídá aktuální adresa URL. `active` Třídy pomáhá uživateli vědět, na stránce, které je aktivní stránkou. mezi navigační odkazy zobrazí.

Součástí NavMenu [ukázkovou aplikaci](https://github.com/aspnet/Docs/tree/master/aspnetcore/razor-components/common/samples/) vytvoří [Bootstrap](https://getbootstrap.com/docs/) navigačního panelu, který ukazuje, jak používat NavLink komponenty. Následující kód ukazuje prvních dvou NavLinks v *Shared/NavMenu.cshtml* souboru.

[!code-cshtml[](common/samples/3.x/BlazorSample/Shared/NavMenu.cshtml?start=13&end=24&highlight=4-6,9-11)]

Existují dva `NavLinkMatch` možnosti:

* `NavLinkMatch.All` &ndash; Určuje, že NavLink musí být v případě, že odpovídá celou adresu URL aktuální aktivní.
* `NavLinkMatch.Prefix` &ndash; Určuje, že NavLink musí být v případě, že odpovídá jakoukoli předponu adresy URL aktuální aktivní.

V předchozím příkladu Home NavLink (`href=""`) odpovídá všem adresám URL a vždy přijímá `active` třídu šablony stylů CSS. Druhý NavLink přijímá pouze `active` třídy, když uživatel navštíví BlazorRoute součásti (`href="BlazorRoute"`).
