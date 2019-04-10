---
title: Směrování součásti syntaxe Razor
author: guardrex
description: Zjistěte, jak směrovat požadavky v aplikacích a o NavLink komponentě.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 04/07/2019
uid: razor-components/routing
ms.openlocfilehash: ef82fa7e0d571979a43fd8ce712bf4f22c06f9a7
ms.sourcegitcommit: 948e533e02c2a7cb6175ada20b2c9cabb7786d0b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/10/2019
ms.locfileid: "59468825"
---
# <a name="razor-components-routing"></a><span data-ttu-id="fa9a7-103">Směrování součásti syntaxe Razor</span><span class="sxs-lookup"><span data-stu-id="fa9a7-103">Razor Components routing</span></span>

<span data-ttu-id="fa9a7-104">Podle [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="fa9a7-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="fa9a7-105">Zjistěte, jak směrovat požadavky v aplikacích a o NavLink komponentě.</span><span class="sxs-lookup"><span data-stu-id="fa9a7-105">Learn how to route requests in apps and about the NavLink component.</span></span>

## <a name="aspnet-core-endpoint-routing-integration"></a><span data-ttu-id="fa9a7-106">Integrace směrování koncových bodů ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="fa9a7-106">ASP.NET Core endpoint routing integration</span></span>

<span data-ttu-id="fa9a7-107">Razor komponenty jsou integrované do [směrování ASP.NET Core](xref:fundamentals/routing).</span><span class="sxs-lookup"><span data-stu-id="fa9a7-107">Razor Components are integrated into [ASP.NET Core routing](xref:fundamentals/routing).</span></span> <span data-ttu-id="fa9a7-108">Aplikace ASP.NET Core je nakonfigurovaný tak, aby přijímal příchozí připojení pro interaktivní součásti Razor s `MapComponentHub<TComponent>` v `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="fa9a7-108">An ASP.NET Core app is configured to accept incoming connections for interactive Razor Components with `MapComponentHub<TComponent>` in `Startup.Configure`.</span></span> `MapComponentHub` <span data-ttu-id="fa9a7-109">Určuje, že kořenová součást `App` má být vykreslen v rámci modelu DOM element odpovídající modulu pro výběr `app`:</span><span class="sxs-lookup"><span data-stu-id="fa9a7-109">specifies that the root component `App` should be rendered within a DOM element matching the selector `app`:</span></span>

```csharp
app.UseRouting(routes =>
{
    routes.MapRazorPages();
    routes.MapComponentHub<App>("app");
});
```

## <a name="route-templates"></a><span data-ttu-id="fa9a7-110">Šablony trasy</span><span class="sxs-lookup"><span data-stu-id="fa9a7-110">Route templates</span></span>

<span data-ttu-id="fa9a7-111">`<Router>` Součást umožňuje směrování a je k dispozici šablona trasy pro jednotlivé dostupné komponenty.</span><span class="sxs-lookup"><span data-stu-id="fa9a7-111">The `<Router>` component enables routing, and a route template is provided to each accessible component.</span></span> <span data-ttu-id="fa9a7-112">`<Router>` Součást se zobrazí v *Components/App.razor* souboru:</span><span class="sxs-lookup"><span data-stu-id="fa9a7-112">The `<Router>` component appears in the *Components/App.razor* file:</span></span>

```cshtml
<Router AppAssembly="typeof(Program).Assembly" />
```

<span data-ttu-id="fa9a7-113">Při *.razor* nebo *.cshtml* soubor s `@page` – direktiva je zkompilován, dostane generované třídy <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> zadání šablonu trasy.</span><span class="sxs-lookup"><span data-stu-id="fa9a7-113">When a *.razor* or *.cshtml* file with an `@page` directive is compiled, the generated class is given a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> specifying the route template.</span></span> <span data-ttu-id="fa9a7-114">Za běhu, směrovač hledá komponentní třídy s `RouteAttribute` a vykreslí podle toho, která komponenta má šablona trasy, která odpovídá požadovanou adresu URL.</span><span class="sxs-lookup"><span data-stu-id="fa9a7-114">At runtime, the router looks for component classes with a `RouteAttribute` and renders whichever component has a route template that matches the requested URL.</span></span>

<span data-ttu-id="fa9a7-115">Více šablon trasy můžete použít pro komponentu.</span><span class="sxs-lookup"><span data-stu-id="fa9a7-115">Multiple route templates can be applied to a component.</span></span> <span data-ttu-id="fa9a7-116">Následující komponenty jsou reaguje na požadavky pro `/BlazorRoute` a `/DifferentBlazorRoute`:</span><span class="sxs-lookup"><span data-stu-id="fa9a7-116">The following component responds to requests for `/BlazorRoute` and `/DifferentBlazorRoute`:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/BlazorRoute.cshtml?name=snippet_BlazorRoute)]

`<Router>` <span data-ttu-id="fa9a7-117">podporuje nastavení záložního součásti pro vykreslení při požadované trase není vyřešený.</span><span class="sxs-lookup"><span data-stu-id="fa9a7-117">supports setting a fallback component for rendering when a requested route isn't resolved.</span></span> <span data-ttu-id="fa9a7-118">Povolit tento scénář vyjádřit výslovný souhlas tím, že nastavíte `FallbackComponent` parametru na typ třídy záložní komponenty.</span><span class="sxs-lookup"><span data-stu-id="fa9a7-118">Enable this opt-in scenario by setting the `FallbackComponent` parameter to the type of the fallback component class.</span></span>

<span data-ttu-id="fa9a7-119">Následující příklad nastaví komponentu podle *Pages/MyFallbackRazorComponent.cshtml* záložní součástí `<Router>`:</span><span class="sxs-lookup"><span data-stu-id="fa9a7-119">The following example sets a component defined in *Pages/MyFallbackRazorComponent.cshtml* as the fallback component for a `<Router>`:</span></span>

```cshtml
<Router ... FallbackComponent="typeof(Pages.MyFallbackRazorComponent)" />
```

> [!IMPORTANT]
> <span data-ttu-id="fa9a7-120">Ke generování tras správně, musí aplikace obsahovat `<base>` označení na jeho *wwwroot/index.html* soubor s základní cesty aplikace určené `href` atribut (`<base href="/">`).</span><span class="sxs-lookup"><span data-stu-id="fa9a7-120">To generate routes properly, the app must include a `<base>` tag in its *wwwroot/index.html* file with the app base path specified in the `href` attribute (`<base href="/">`).</span></span> <span data-ttu-id="fa9a7-121">Další informace naleznete v tématu <xref:host-and-deploy/razor-components-blazor/blazor#app-base-path>.</span><span class="sxs-lookup"><span data-stu-id="fa9a7-121">For more information, see <xref:host-and-deploy/razor-components-blazor/blazor#app-base-path>.</span></span>

## <a name="route-parameters"></a><span data-ttu-id="fa9a7-122">Parametry trasy</span><span class="sxs-lookup"><span data-stu-id="fa9a7-122">Route parameters</span></span>

<span data-ttu-id="fa9a7-123">Směrovač používá parametry trasy k naplnění odpovídajících parametrů součásti se stejným názvem (malých písmen):</span><span class="sxs-lookup"><span data-stu-id="fa9a7-123">The router uses route parameters to populate the corresponding component parameters with the same name (case insensitive):</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/RouteParameter.cshtml?name=snippet_RouteParameter&highlight=2,7-8)]

<span data-ttu-id="fa9a7-124">Volitelné parametry nejsou podporovány, tedy dvě `@page` direktivy se použijí v předchozím příkladu.</span><span class="sxs-lookup"><span data-stu-id="fa9a7-124">Optional parameters aren't supported yet, so two `@page` directives are applied in the example above.</span></span> <span data-ttu-id="fa9a7-125">První umožňuje přechod na komponenty bez parametrů.</span><span class="sxs-lookup"><span data-stu-id="fa9a7-125">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="fa9a7-126">Druhá `@page` trvá – direktiva `{text}` parametr trasa a přiřadí hodnotu do proměnné `Text` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="fa9a7-126">The second `@page` directive takes the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

## <a name="route-constraints"></a><span data-ttu-id="fa9a7-127">Omezení trasy</span><span class="sxs-lookup"><span data-stu-id="fa9a7-127">Route constraints</span></span>

<span data-ttu-id="fa9a7-128">Omezení trasy vynucuje typ odpovídající v segmentu směrování do komponenty.</span><span class="sxs-lookup"><span data-stu-id="fa9a7-128">A route constraint enforces type matching on a route segment to a component.</span></span>

<span data-ttu-id="fa9a7-129">V následujícím příkladu trasy, která má součásti uživatelé pouze odpovídá, pokud:</span><span class="sxs-lookup"><span data-stu-id="fa9a7-129">In the following example, the route to the Users component only matches if:</span></span>

* <span data-ttu-id="fa9a7-130">`Id` Segment směrování je k dispozici na adrese URL požadavku.</span><span class="sxs-lookup"><span data-stu-id="fa9a7-130">An `Id` route segment is present on the request URL.</span></span>
* <span data-ttu-id="fa9a7-131">`Id` Segmentu je celé číslo (`int`).</span><span class="sxs-lookup"><span data-stu-id="fa9a7-131">The `Id` segment is an integer (`int`).</span></span>

[!code-cshtml[](routing/samples_snapshot/3.x/Constraint.cshtml?highlight=1)]

<span data-ttu-id="fa9a7-132">Omezení trasy, které jsou uvedeny v následující tabulce jsou k dispozici.</span><span class="sxs-lookup"><span data-stu-id="fa9a7-132">The route constraints shown in the following table are available.</span></span> <span data-ttu-id="fa9a7-133">Omezení trasy, které odpovídají neutrální jazykové verzi najdete v upozornění níže uvedená tabulka pro další informace.</span><span class="sxs-lookup"><span data-stu-id="fa9a7-133">For the route constraints that match with the invariant culture, see the warning below the table for more information.</span></span>

| <span data-ttu-id="fa9a7-134">Omezení</span><span class="sxs-lookup"><span data-stu-id="fa9a7-134">Constraint</span></span> | <span data-ttu-id="fa9a7-135">Příklad</span><span class="sxs-lookup"><span data-stu-id="fa9a7-135">Example</span></span>           | <span data-ttu-id="fa9a7-136">Příklad shody</span><span class="sxs-lookup"><span data-stu-id="fa9a7-136">Example Matches</span></span>                                                                  | <span data-ttu-id="fa9a7-137">Invariantní</span><span class="sxs-lookup"><span data-stu-id="fa9a7-137">Invariant</span></span><br><span data-ttu-id="fa9a7-138">jazyková verze</span><span class="sxs-lookup"><span data-stu-id="fa9a7-138">culture</span></span><br><span data-ttu-id="fa9a7-139">shoda</span><span class="sxs-lookup"><span data-stu-id="fa9a7-139">matching</span></span> |
| ---------- | ----------------- | -------------------------------------------------------------------------------- | :------------------------------: |
| `bool`     | `{active:bool}`   | `true`<span data-ttu-id="fa9a7-140">, </span><span class="sxs-lookup"><span data-stu-id="fa9a7-140">,</span></span> `FALSE`                                                                  | <span data-ttu-id="fa9a7-141">Ne</span><span class="sxs-lookup"><span data-stu-id="fa9a7-141">No</span></span>                               |
| `datetime` | `{dob:datetime}`  | `2016-12-31`<span data-ttu-id="fa9a7-142">, </span><span class="sxs-lookup"><span data-stu-id="fa9a7-142">,</span></span> `2016-12-31 7:32pm`                                                | <span data-ttu-id="fa9a7-143">Ano</span><span class="sxs-lookup"><span data-stu-id="fa9a7-143">Yes</span></span>                              |
| `decimal`  | `{price:decimal}` | `49.99`<span data-ttu-id="fa9a7-144">, </span><span class="sxs-lookup"><span data-stu-id="fa9a7-144">,</span></span> `-1,000.01`                                                             | <span data-ttu-id="fa9a7-145">Ano</span><span class="sxs-lookup"><span data-stu-id="fa9a7-145">Yes</span></span>                              |
| `double`   | `{weight:double}` | `1.234`<span data-ttu-id="fa9a7-146">, </span><span class="sxs-lookup"><span data-stu-id="fa9a7-146">,</span></span> `-1,001.01e8`                                                           | <span data-ttu-id="fa9a7-147">Ano</span><span class="sxs-lookup"><span data-stu-id="fa9a7-147">Yes</span></span>                              |
| `float`    | `{weight:float}`  | `1.234`<span data-ttu-id="fa9a7-148">, </span><span class="sxs-lookup"><span data-stu-id="fa9a7-148">,</span></span> `-1,001.01e8`                                                           | <span data-ttu-id="fa9a7-149">Ano</span><span class="sxs-lookup"><span data-stu-id="fa9a7-149">Yes</span></span>                              |
| `guid`     | `{id:guid}`       | `CD2C1638-1638-72D5-1638-DEADBEEF1638`<span data-ttu-id="fa9a7-150">, </span><span class="sxs-lookup"><span data-stu-id="fa9a7-150">,</span></span> `{CD2C1638-1638-72D5-1638-DEADBEEF1638}` | <span data-ttu-id="fa9a7-151">Ne</span><span class="sxs-lookup"><span data-stu-id="fa9a7-151">No</span></span>                               |
| `int`      | `{id:int}`        | `123456789`<span data-ttu-id="fa9a7-152">, </span><span class="sxs-lookup"><span data-stu-id="fa9a7-152">,</span></span> `-123456789`                                                        | <span data-ttu-id="fa9a7-153">Ano</span><span class="sxs-lookup"><span data-stu-id="fa9a7-153">Yes</span></span>                              |
| `long`     | `{ticks:long}`    | `123456789`<span data-ttu-id="fa9a7-154">, </span><span class="sxs-lookup"><span data-stu-id="fa9a7-154">,</span></span> `-123456789`                                                        | <span data-ttu-id="fa9a7-155">Ano</span><span class="sxs-lookup"><span data-stu-id="fa9a7-155">Yes</span></span>                              |

> [!WARNING]
> <span data-ttu-id="fa9a7-156">Směrovat omezení, která Ověřte adresu URL a jsou převedeny na typ CLR (například `int` nebo `DateTime`) vždy používejte neutrální jazykovou verzi.</span><span class="sxs-lookup"><span data-stu-id="fa9a7-156">Route constraints that verify the URL and are converted to a CLR type (such as `int` or `DateTime`) always use the invariant culture.</span></span> <span data-ttu-id="fa9a7-157">Tato omezení předpokládá, že adresa URL je nepřekládá.</span><span class="sxs-lookup"><span data-stu-id="fa9a7-157">These constraints assume that the URL is non-localizable.</span></span>

## <a name="navlink-component"></a><span data-ttu-id="fa9a7-158">Komponenta NavLink</span><span class="sxs-lookup"><span data-stu-id="fa9a7-158">NavLink component</span></span>

<span data-ttu-id="fa9a7-159">Použít komponentu NavLink namísto HTML `<a>` prvky při vytváření navigačních odkazů.</span><span class="sxs-lookup"><span data-stu-id="fa9a7-159">Use a NavLink component in place of HTML `<a>` elements when creating navigation links.</span></span> <span data-ttu-id="fa9a7-160">Komponentu NavLink se chová jako `<a>` elementu, s výjimkou přepíná `active` třídu šablony stylů CSS podle toho, jestli jeho `href` odpovídá aktuální adresa URL.</span><span class="sxs-lookup"><span data-stu-id="fa9a7-160">A NavLink component behaves like an `<a>` element, except it toggles an `active` CSS class based on whether its `href` matches the current URL.</span></span> <span data-ttu-id="fa9a7-161">`active` Třídy pomáhá uživateli vědět, na stránce, které je aktivní stránkou. mezi navigační odkazy zobrazí.</span><span class="sxs-lookup"><span data-stu-id="fa9a7-161">The `active` class helps a user understand which page is the active page among the navigation links displayed.</span></span>

<span data-ttu-id="fa9a7-162">Vytvoří následující komponenty NavMenu [Bootstrap](https://getbootstrap.com/docs/) navigační panel, který ukazuje, jak používat NavLink komponenty:</span><span class="sxs-lookup"><span data-stu-id="fa9a7-162">The following NavMenu component creates a [Bootstrap](https://getbootstrap.com/docs/) navigation bar that demonstrates how to use NavLink components:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Shared/NavMenu.cshtml?name=snippet_NavLinks&highlight=4-6,9-11)]

<span data-ttu-id="fa9a7-163">Existují dva `NavLinkMatch` možnosti:</span><span class="sxs-lookup"><span data-stu-id="fa9a7-163">There are two `NavLinkMatch` options:</span></span>

* `NavLinkMatch.All` <span data-ttu-id="fa9a7-164">&ndash; Určuje, že NavLink musí být v případě, že odpovídá celou adresu URL aktuální aktivní.</span><span class="sxs-lookup"><span data-stu-id="fa9a7-164">&ndash; Specifies that the NavLink should be active when it matches the entire current URL.</span></span>
* `NavLinkMatch.Prefix` <span data-ttu-id="fa9a7-165">&ndash; Určuje, že NavLink musí být v případě, že odpovídá jakoukoli předponu adresy URL aktuální aktivní.</span><span class="sxs-lookup"><span data-stu-id="fa9a7-165">&ndash; Specifies that the NavLink should be active when it matches any prefix of the current URL.</span></span>

<span data-ttu-id="fa9a7-166">V předchozím příkladu Home NavLink (`href=""`) odpovídá všem adresám URL a vždy přijímá `active` třídu šablony stylů CSS.</span><span class="sxs-lookup"><span data-stu-id="fa9a7-166">In the preceding example, the Home NavLink (`href=""`) matches all URLs and always receives the `active` CSS class.</span></span> <span data-ttu-id="fa9a7-167">Druhý NavLink přijímá pouze `active` třídy, když uživatel navštíví komponentu Blazor trasy (`href="BlazorRoute"`).</span><span class="sxs-lookup"><span data-stu-id="fa9a7-167">The second NavLink only receives the `active` class when the user visits the Blazor Route component (`href="BlazorRoute"`).</span></span>
