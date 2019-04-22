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
# <a name="blazor-routing"></a><span data-ttu-id="b8a9d-103">Blazor směrování</span><span class="sxs-lookup"><span data-stu-id="b8a9d-103">Blazor routing</span></span>

<span data-ttu-id="b8a9d-104">Podle [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="b8a9d-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="b8a9d-105">Zjistěte, jak směrovat požadavky v aplikacích a o NavLink komponentě.</span><span class="sxs-lookup"><span data-stu-id="b8a9d-105">Learn how to route requests in apps and about the NavLink component.</span></span>

## <a name="aspnet-core-endpoint-routing-integration"></a><span data-ttu-id="b8a9d-106">Integrace směrování koncových bodů ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b8a9d-106">ASP.NET Core endpoint routing integration</span></span>

<span data-ttu-id="b8a9d-107">Je součástí Blazor [směrování ASP.NET Core](xref:fundamentals/routing).</span><span class="sxs-lookup"><span data-stu-id="b8a9d-107">Blazor is integrated into [ASP.NET Core routing](xref:fundamentals/routing).</span></span> <span data-ttu-id="b8a9d-108">Aplikace ASP.NET Core je nakonfigurovaný tak, aby přijímal příchozí připojení pro interaktivní součásti s `MapComponentHub<TComponent>` v `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="b8a9d-108">An ASP.NET Core app is configured to accept incoming connections for interactive components with `MapComponentHub<TComponent>` in `Startup.Configure`.</span></span> <span data-ttu-id="b8a9d-109">`MapComponentHub` Určuje, že kořenová součást `App` má být vykreslen v rámci modelu DOM element odpovídající modulu pro výběr `app`:</span><span class="sxs-lookup"><span data-stu-id="b8a9d-109">`MapComponentHub` specifies that the root component `App` should be rendered within a DOM element matching the selector `app`:</span></span>

```csharp
app.UseRouting(routes =>
{
    routes.MapRazorPages();
    routes.MapComponentHub<App>("app");
});
```

## <a name="route-templates"></a><span data-ttu-id="b8a9d-110">Šablony trasy</span><span class="sxs-lookup"><span data-stu-id="b8a9d-110">Route templates</span></span>

<span data-ttu-id="b8a9d-111">`<Router>` Součást umožňuje směrování a je k dispozici šablona trasy pro jednotlivé dostupné komponenty.</span><span class="sxs-lookup"><span data-stu-id="b8a9d-111">The `<Router>` component enables routing, and a route template is provided to each accessible component.</span></span> <span data-ttu-id="b8a9d-112">`<Router>` Součást se zobrazí v *Components/App.razor* souboru:</span><span class="sxs-lookup"><span data-stu-id="b8a9d-112">The `<Router>` component appears in the *Components/App.razor* file:</span></span>

```cshtml
<Router AppAssembly="typeof(Program).Assembly" />
```

<span data-ttu-id="b8a9d-113">Při *.razor* nebo *.cshtml* soubor s `@page` – direktiva je zkompilován, dostane generované třídy <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> zadání šablonu trasy.</span><span class="sxs-lookup"><span data-stu-id="b8a9d-113">When a *.razor* or *.cshtml* file with an `@page` directive is compiled, the generated class is given a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> specifying the route template.</span></span> <span data-ttu-id="b8a9d-114">Za běhu, směrovač hledá komponentní třídy s `RouteAttribute` a vykreslí podle toho, která komponenta má šablona trasy, která odpovídá požadovanou adresu URL.</span><span class="sxs-lookup"><span data-stu-id="b8a9d-114">At runtime, the router looks for component classes with a `RouteAttribute` and renders whichever component has a route template that matches the requested URL.</span></span>

<span data-ttu-id="b8a9d-115">Více šablon trasy můžete použít pro komponentu.</span><span class="sxs-lookup"><span data-stu-id="b8a9d-115">Multiple route templates can be applied to a component.</span></span> <span data-ttu-id="b8a9d-116">Následující komponenty jsou reaguje na požadavky pro `/BlazorRoute` a `/DifferentBlazorRoute`:</span><span class="sxs-lookup"><span data-stu-id="b8a9d-116">The following component responds to requests for `/BlazorRoute` and `/DifferentBlazorRoute`:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/BlazorRoute.cshtml?name=snippet_BlazorRoute)]

<span data-ttu-id="b8a9d-117">`<Router>` podporuje nastavení záložního součásti pro vykreslení při požadované trase není vyřešený.</span><span class="sxs-lookup"><span data-stu-id="b8a9d-117">`<Router>` supports setting a fallback component for rendering when a requested route isn't resolved.</span></span> <span data-ttu-id="b8a9d-118">Povolit tento scénář vyjádřit výslovný souhlas tím, že nastavíte `FallbackComponent` parametru na typ třídy záložní komponenty.</span><span class="sxs-lookup"><span data-stu-id="b8a9d-118">Enable this opt-in scenario by setting the `FallbackComponent` parameter to the type of the fallback component class.</span></span>

<span data-ttu-id="b8a9d-119">Následující příklad nastaví komponentu podle *Pages/MyFallbackRazorComponent.cshtml* záložní součástí `<Router>`:</span><span class="sxs-lookup"><span data-stu-id="b8a9d-119">The following example sets a component defined in *Pages/MyFallbackRazorComponent.cshtml* as the fallback component for a `<Router>`:</span></span>

```cshtml
<Router ... FallbackComponent="typeof(Pages.MyFallbackRazorComponent)" />
```

> [!IMPORTANT]
> <span data-ttu-id="b8a9d-120">Ke generování tras správně, musí aplikace obsahovat `<base>` označení na jeho *wwwroot/index.html* soubor s základní cesty aplikace určené `href` atribut (`<base href="/">`).</span><span class="sxs-lookup"><span data-stu-id="b8a9d-120">To generate routes properly, the app must include a `<base>` tag in its *wwwroot/index.html* file with the app base path specified in the `href` attribute (`<base href="/">`).</span></span> <span data-ttu-id="b8a9d-121">Další informace naleznete v tématu <xref:host-and-deploy/blazor/client-side#app-base-path>.</span><span class="sxs-lookup"><span data-stu-id="b8a9d-121">For more information, see <xref:host-and-deploy/blazor/client-side#app-base-path>.</span></span>

## <a name="route-parameters"></a><span data-ttu-id="b8a9d-122">Parametry trasy</span><span class="sxs-lookup"><span data-stu-id="b8a9d-122">Route parameters</span></span>

<span data-ttu-id="b8a9d-123">Směrovač používá parametry trasy k naplnění odpovídajících parametrů součásti se stejným názvem (malých písmen):</span><span class="sxs-lookup"><span data-stu-id="b8a9d-123">The router uses route parameters to populate the corresponding component parameters with the same name (case insensitive):</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/RouteParameter.cshtml?name=snippet_RouteParameter&highlight=2,7-8)]

<span data-ttu-id="b8a9d-124">Volitelné parametry nejsou podporovány, tedy dvě `@page` direktivy se použijí v předchozím příkladu.</span><span class="sxs-lookup"><span data-stu-id="b8a9d-124">Optional parameters aren't supported yet, so two `@page` directives are applied in the example above.</span></span> <span data-ttu-id="b8a9d-125">První umožňuje přechod na komponenty bez parametrů.</span><span class="sxs-lookup"><span data-stu-id="b8a9d-125">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="b8a9d-126">Druhá `@page` trvá – direktiva `{text}` parametr trasa a přiřadí hodnotu do proměnné `Text` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="b8a9d-126">The second `@page` directive takes the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

## <a name="route-constraints"></a><span data-ttu-id="b8a9d-127">Omezení trasy</span><span class="sxs-lookup"><span data-stu-id="b8a9d-127">Route constraints</span></span>

<span data-ttu-id="b8a9d-128">Omezení trasy vynucuje typ odpovídající v segmentu směrování do komponenty.</span><span class="sxs-lookup"><span data-stu-id="b8a9d-128">A route constraint enforces type matching on a route segment to a component.</span></span>

<span data-ttu-id="b8a9d-129">V následujícím příkladu trasy, která má součásti uživatelé pouze odpovídá, pokud:</span><span class="sxs-lookup"><span data-stu-id="b8a9d-129">In the following example, the route to the Users component only matches if:</span></span>

* <span data-ttu-id="b8a9d-130">`Id` Segment směrování je k dispozici na adrese URL požadavku.</span><span class="sxs-lookup"><span data-stu-id="b8a9d-130">An `Id` route segment is present on the request URL.</span></span>
* <span data-ttu-id="b8a9d-131">`Id` Segmentu je celé číslo (`int`).</span><span class="sxs-lookup"><span data-stu-id="b8a9d-131">The `Id` segment is an integer (`int`).</span></span>

[!code-cshtml[](routing/samples_snapshot/3.x/Constraint.cshtml?highlight=1)]

<span data-ttu-id="b8a9d-132">Omezení trasy, které jsou uvedeny v následující tabulce jsou k dispozici.</span><span class="sxs-lookup"><span data-stu-id="b8a9d-132">The route constraints shown in the following table are available.</span></span> <span data-ttu-id="b8a9d-133">Omezení trasy, které odpovídají neutrální jazykové verzi najdete v upozornění níže uvedená tabulka pro další informace.</span><span class="sxs-lookup"><span data-stu-id="b8a9d-133">For the route constraints that match with the invariant culture, see the warning below the table for more information.</span></span>

| <span data-ttu-id="b8a9d-134">Omezení</span><span class="sxs-lookup"><span data-stu-id="b8a9d-134">Constraint</span></span> | <span data-ttu-id="b8a9d-135">Příklad</span><span class="sxs-lookup"><span data-stu-id="b8a9d-135">Example</span></span>           | <span data-ttu-id="b8a9d-136">Příklad shody</span><span class="sxs-lookup"><span data-stu-id="b8a9d-136">Example Matches</span></span>                                                                  | <span data-ttu-id="b8a9d-137">Invariantní</span><span class="sxs-lookup"><span data-stu-id="b8a9d-137">Invariant</span></span><br><span data-ttu-id="b8a9d-138">jazyková verze</span><span class="sxs-lookup"><span data-stu-id="b8a9d-138">culture</span></span><br><span data-ttu-id="b8a9d-139">shoda</span><span class="sxs-lookup"><span data-stu-id="b8a9d-139">matching</span></span> |
| ---------- | ----------------- | -------------------------------------------------------------------------------- | :------------------------------: |
| `bool`     | `{active:bool}`   | <span data-ttu-id="b8a9d-140">`true`, `FALSE`</span><span class="sxs-lookup"><span data-stu-id="b8a9d-140">`true`, `FALSE`</span></span>                                                                  | <span data-ttu-id="b8a9d-141">Ne</span><span class="sxs-lookup"><span data-stu-id="b8a9d-141">No</span></span>                               |
| `datetime` | `{dob:datetime}`  | <span data-ttu-id="b8a9d-142">`2016-12-31`, `2016-12-31 7:32pm`</span><span class="sxs-lookup"><span data-stu-id="b8a9d-142">`2016-12-31`, `2016-12-31 7:32pm`</span></span>                                                | <span data-ttu-id="b8a9d-143">Ano</span><span class="sxs-lookup"><span data-stu-id="b8a9d-143">Yes</span></span>                              |
| `decimal`  | `{price:decimal}` | <span data-ttu-id="b8a9d-144">`49.99`, `-1,000.01`</span><span class="sxs-lookup"><span data-stu-id="b8a9d-144">`49.99`, `-1,000.01`</span></span>                                                             | <span data-ttu-id="b8a9d-145">Ano</span><span class="sxs-lookup"><span data-stu-id="b8a9d-145">Yes</span></span>                              |
| `double`   | `{weight:double}` | <span data-ttu-id="b8a9d-146">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="b8a9d-146">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="b8a9d-147">Ano</span><span class="sxs-lookup"><span data-stu-id="b8a9d-147">Yes</span></span>                              |
| `float`    | `{weight:float}`  | <span data-ttu-id="b8a9d-148">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="b8a9d-148">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="b8a9d-149">Ano</span><span class="sxs-lookup"><span data-stu-id="b8a9d-149">Yes</span></span>                              |
| `guid`     | `{id:guid}`       | <span data-ttu-id="b8a9d-150">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span><span class="sxs-lookup"><span data-stu-id="b8a9d-150">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span></span> | <span data-ttu-id="b8a9d-151">Ne</span><span class="sxs-lookup"><span data-stu-id="b8a9d-151">No</span></span>                               |
| `int`      | `{id:int}`        | <span data-ttu-id="b8a9d-152">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="b8a9d-152">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="b8a9d-153">Ano</span><span class="sxs-lookup"><span data-stu-id="b8a9d-153">Yes</span></span>                              |
| `long`     | `{ticks:long}`    | <span data-ttu-id="b8a9d-154">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="b8a9d-154">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="b8a9d-155">Ano</span><span class="sxs-lookup"><span data-stu-id="b8a9d-155">Yes</span></span>                              |

> [!WARNING]
> <span data-ttu-id="b8a9d-156">Směrovat omezení, která Ověřte adresu URL a jsou převedeny na typ CLR (například `int` nebo `DateTime`) vždy používejte neutrální jazykovou verzi.</span><span class="sxs-lookup"><span data-stu-id="b8a9d-156">Route constraints that verify the URL and are converted to a CLR type (such as `int` or `DateTime`) always use the invariant culture.</span></span> <span data-ttu-id="b8a9d-157">Tato omezení předpokládá, že adresa URL je nepřekládá.</span><span class="sxs-lookup"><span data-stu-id="b8a9d-157">These constraints assume that the URL is non-localizable.</span></span>

## <a name="navlink-component"></a><span data-ttu-id="b8a9d-158">Komponenta NavLink</span><span class="sxs-lookup"><span data-stu-id="b8a9d-158">NavLink component</span></span>

<span data-ttu-id="b8a9d-159">Použít komponentu NavLink namísto HTML `<a>` prvky při vytváření navigačních odkazů.</span><span class="sxs-lookup"><span data-stu-id="b8a9d-159">Use a NavLink component in place of HTML `<a>` elements when creating navigation links.</span></span> <span data-ttu-id="b8a9d-160">Komponentu NavLink se chová jako `<a>` elementu, s výjimkou přepíná `active` třídu šablony stylů CSS podle toho, jestli jeho `href` odpovídá aktuální adresa URL.</span><span class="sxs-lookup"><span data-stu-id="b8a9d-160">A NavLink component behaves like an `<a>` element, except it toggles an `active` CSS class based on whether its `href` matches the current URL.</span></span> <span data-ttu-id="b8a9d-161">`active` Třídy pomáhá uživateli vědět, na stránce, které je aktivní stránkou. mezi navigační odkazy zobrazí.</span><span class="sxs-lookup"><span data-stu-id="b8a9d-161">The `active` class helps a user understand which page is the active page among the navigation links displayed.</span></span>

<span data-ttu-id="b8a9d-162">Vytvoří následující komponenty NavMenu [Bootstrap](https://getbootstrap.com/docs/) navigační panel, který ukazuje, jak používat NavLink komponenty:</span><span class="sxs-lookup"><span data-stu-id="b8a9d-162">The following NavMenu component creates a [Bootstrap](https://getbootstrap.com/docs/) navigation bar that demonstrates how to use NavLink components:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Shared/NavMenu.cshtml?name=snippet_NavLinks&highlight=4-6,9-11)]

<span data-ttu-id="b8a9d-163">Existují dva `NavLinkMatch` možnosti:</span><span class="sxs-lookup"><span data-stu-id="b8a9d-163">There are two `NavLinkMatch` options:</span></span>

* <span data-ttu-id="b8a9d-164">`NavLinkMatch.All` &ndash; Určuje, že NavLink musí být v případě, že odpovídá celou adresu URL aktuální aktivní.</span><span class="sxs-lookup"><span data-stu-id="b8a9d-164">`NavLinkMatch.All` &ndash; Specifies that the NavLink should be active when it matches the entire current URL.</span></span>
* <span data-ttu-id="b8a9d-165">`NavLinkMatch.Prefix` &ndash; Určuje, že NavLink musí být v případě, že odpovídá jakoukoli předponu adresy URL aktuální aktivní.</span><span class="sxs-lookup"><span data-stu-id="b8a9d-165">`NavLinkMatch.Prefix` &ndash; Specifies that the NavLink should be active when it matches any prefix of the current URL.</span></span>

<span data-ttu-id="b8a9d-166">V předchozím příkladu Home NavLink (`href=""`) odpovídá všem adresám URL a vždy přijímá `active` třídu šablony stylů CSS.</span><span class="sxs-lookup"><span data-stu-id="b8a9d-166">In the preceding example, the Home NavLink (`href=""`) matches all URLs and always receives the `active` CSS class.</span></span> <span data-ttu-id="b8a9d-167">Druhý NavLink přijímá pouze `active` třídy, když uživatel navštíví komponentu Blazor trasy (`href="BlazorRoute"`).</span><span class="sxs-lookup"><span data-stu-id="b8a9d-167">The second NavLink only receives the `active` class when the user visits the Blazor Route component (`href="BlazorRoute"`).</span></span>