---
title: Blazor směrování
author: guardrex
description: Zjistěte, jak směrovat požadavky v aplikacích a o NavLink komponentě.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 05/14/2019
uid: blazor/routing
ms.openlocfilehash: b2a703a8dda87812fce1e52b165ad6de901393a7
ms.sourcegitcommit: 335a88c1b6e7f0caa8a3a27db57c56664d676d34
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/12/2019
ms.locfileid: "67034697"
---
# <a name="blazor-routing"></a><span data-ttu-id="aa070-103">Blazor směrování</span><span class="sxs-lookup"><span data-stu-id="aa070-103">Blazor routing</span></span>

<span data-ttu-id="aa070-104">Podle [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="aa070-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="aa070-105">Zjistěte, jak směrovat požadavky v aplikacích a o NavLink komponentě.</span><span class="sxs-lookup"><span data-stu-id="aa070-105">Learn how to route requests in apps and about the NavLink component.</span></span>

## <a name="aspnet-core-endpoint-routing-integration"></a><span data-ttu-id="aa070-106">Integrace směrování koncových bodů ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="aa070-106">ASP.NET Core endpoint routing integration</span></span>

<span data-ttu-id="aa070-107">Blazor na straně serveru je integrovaná do [směrování ASP.NET Core koncový bod](xref:fundamentals/routing).</span><span class="sxs-lookup"><span data-stu-id="aa070-107">Blazor server-side is integrated into [ASP.NET Core Endpoint Routing](xref:fundamentals/routing).</span></span> <span data-ttu-id="aa070-108">Aplikace ASP.NET Core je nakonfigurovaný tak, aby přijímal příchozí připojení pro interaktivní součásti s `MapBlazorHub` v `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="aa070-108">An ASP.NET Core app is configured to accept incoming connections for interactive components with `MapBlazorHub` in `Startup.Configure`:</span></span>

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

## <a name="route-templates"></a><span data-ttu-id="aa070-109">Šablony trasy</span><span class="sxs-lookup"><span data-stu-id="aa070-109">Route templates</span></span>

<span data-ttu-id="aa070-110">`<Router>` Součást umožňuje směrování a je k dispozici šablona trasy pro jednotlivé dostupné komponenty.</span><span class="sxs-lookup"><span data-stu-id="aa070-110">The `<Router>` component enables routing, and a route template is provided to each accessible component.</span></span> <span data-ttu-id="aa070-111">`<Router>` Součást se zobrazí v *App.razor* souboru:</span><span class="sxs-lookup"><span data-stu-id="aa070-111">The `<Router>` component appears in the *App.razor* file:</span></span>

<span data-ttu-id="aa070-112">V aplikaci na straně serveru Blazor:</span><span class="sxs-lookup"><span data-stu-id="aa070-112">In a Blazor server-side app:</span></span>

```cshtml
<Router AppAssembly="typeof(Startup).Assembly" />
```

<span data-ttu-id="aa070-113">V aplikaci na straně klienta Blazor:</span><span class="sxs-lookup"><span data-stu-id="aa070-113">In a Blazor client-side app:</span></span>

```cshtml
<Router AppAssembly="typeof(Program).Assembly" />
```

<span data-ttu-id="aa070-114">Při *.razor* soubor s `@page` – direktiva je zkompilován, je k dispozici generované třídy <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> zadání šablonu trasy.</span><span class="sxs-lookup"><span data-stu-id="aa070-114">When a *.razor* file with an `@page` directive is compiled, the generated class is provided a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> specifying the route template.</span></span> <span data-ttu-id="aa070-115">Za běhu, směrovač hledá komponentní třídy s `RouteAttribute` a vykreslí komponentu šablonou trasy, která odpovídá požadovanou adresu URL.</span><span class="sxs-lookup"><span data-stu-id="aa070-115">At runtime, the router looks for component classes with a `RouteAttribute` and renders the component with a route template that matches the requested URL.</span></span>

<span data-ttu-id="aa070-116">Více šablon trasy můžete použít pro komponentu.</span><span class="sxs-lookup"><span data-stu-id="aa070-116">Multiple route templates can be applied to a component.</span></span> <span data-ttu-id="aa070-117">Následující komponenty jsou reaguje na požadavky pro `/BlazorRoute` a `/DifferentBlazorRoute`:</span><span class="sxs-lookup"><span data-stu-id="aa070-117">The following component responds to requests for `/BlazorRoute` and `/DifferentBlazorRoute`:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/BlazorRoute.razor?name=snippet_BlazorRoute)]

<span data-ttu-id="aa070-118">`<Router>` podporuje nastavení záložního součásti pro vykreslení při požadované trase není vyřešený.</span><span class="sxs-lookup"><span data-stu-id="aa070-118">`<Router>` supports setting a fallback component to render when a requested route isn't resolved.</span></span> <span data-ttu-id="aa070-119">Povolit tento scénář vyjádřit výslovný souhlas tím, že nastavíte `FallbackComponent` parametru na typ třídy záložní komponenty.</span><span class="sxs-lookup"><span data-stu-id="aa070-119">Enable this opt-in scenario by setting the `FallbackComponent` parameter to the type of the fallback component class.</span></span>

<span data-ttu-id="aa070-120">Následující příklad nastaví komponentu podle *Pages/MyFallbackRazorComponent.razor* záložní součástí `<Router>`:</span><span class="sxs-lookup"><span data-stu-id="aa070-120">The following example sets a component defined in *Pages/MyFallbackRazorComponent.razor* as the fallback component for a `<Router>`:</span></span>

```cshtml
<Router ... FallbackComponent="typeof(Pages.MyFallbackRazorComponent)" />
```

> [!IMPORTANT]
> <span data-ttu-id="aa070-121">Ke generování tras správně, musí aplikace obsahovat `<base>` označení na jeho *wwwroot/index.html* souboru (Blazor straně klienta) nebo *stránek /\_Host.cshtml* (Blazor serverové) soubor s Základní cesta aplikace podle `href` atribut (`<base href="/">`).</span><span class="sxs-lookup"><span data-stu-id="aa070-121">To generate routes properly, the app must include a `<base>` tag in its *wwwroot/index.html* file (Blazor client-side) or *Pages/\_Host.cshtml* file (Blazor server-side) with the app base path specified in the `href` attribute (`<base href="/">`).</span></span> <span data-ttu-id="aa070-122">Další informace naleznete v tématu <xref:host-and-deploy/blazor/client-side#app-base-path>.</span><span class="sxs-lookup"><span data-stu-id="aa070-122">For more information, see <xref:host-and-deploy/blazor/client-side#app-base-path>.</span></span>

## <a name="route-parameters"></a><span data-ttu-id="aa070-123">Parametry trasy</span><span class="sxs-lookup"><span data-stu-id="aa070-123">Route parameters</span></span>

<span data-ttu-id="aa070-124">Směrovač používá parametry trasy k naplnění odpovídajících parametrů součásti se stejným názvem (malých písmen):</span><span class="sxs-lookup"><span data-stu-id="aa070-124">The router uses route parameters to populate the corresponding component parameters with the same name (case insensitive):</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/RouteParameter.razor?name=snippet_RouteParameter&highlight=2,7-8)]

<span data-ttu-id="aa070-125">Volitelné parametry nejsou podporovány pro Blazor aplikace v ASP.NET Core 3.0 ve verzi Preview.</span><span class="sxs-lookup"><span data-stu-id="aa070-125">Optional parameters aren't supported for Blazor apps in ASP.NET Core 3.0 Preview.</span></span> <span data-ttu-id="aa070-126">Dvě `@page` direktivy se použijí v předchozím příkladu.</span><span class="sxs-lookup"><span data-stu-id="aa070-126">Two `@page` directives are applied in the previous example.</span></span> <span data-ttu-id="aa070-127">První umožňuje přechod na komponenty bez parametrů.</span><span class="sxs-lookup"><span data-stu-id="aa070-127">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="aa070-128">Druhá `@page` trvá – direktiva `{text}` parametr trasa a přiřadí hodnotu do proměnné `Text` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="aa070-128">The second `@page` directive takes the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

## <a name="route-constraints"></a><span data-ttu-id="aa070-129">Omezení trasy</span><span class="sxs-lookup"><span data-stu-id="aa070-129">Route constraints</span></span>

<span data-ttu-id="aa070-130">Omezení trasy vynucuje typ odpovídající v segmentu směrování do komponenty.</span><span class="sxs-lookup"><span data-stu-id="aa070-130">A route constraint enforces type matching on a route segment to a component.</span></span>

<span data-ttu-id="aa070-131">V následujícím příkladu trasy, která má součásti uživatelé pouze odpovídá, pokud:</span><span class="sxs-lookup"><span data-stu-id="aa070-131">In the following example, the route to the Users component only matches if:</span></span>

* <span data-ttu-id="aa070-132">`Id` Segment směrování je k dispozici na adrese URL požadavku.</span><span class="sxs-lookup"><span data-stu-id="aa070-132">An `Id` route segment is present on the request URL.</span></span>
* <span data-ttu-id="aa070-133">`Id` Segmentu je celé číslo (`int`).</span><span class="sxs-lookup"><span data-stu-id="aa070-133">The `Id` segment is an integer (`int`).</span></span>

[!code-cshtml[](routing/samples_snapshot/3.x/Constraint.razor?highlight=1)]

<span data-ttu-id="aa070-134">Omezení trasy, které jsou uvedeny v následující tabulce jsou k dispozici.</span><span class="sxs-lookup"><span data-stu-id="aa070-134">The route constraints shown in the following table are available.</span></span> <span data-ttu-id="aa070-135">Omezení trasy, které odpovídají neutrální jazykové verzi najdete v upozornění níže uvedená tabulka pro další informace.</span><span class="sxs-lookup"><span data-stu-id="aa070-135">For the route constraints that match with the invariant culture, see the warning below the table for more information.</span></span>

| <span data-ttu-id="aa070-136">Omezení</span><span class="sxs-lookup"><span data-stu-id="aa070-136">Constraint</span></span> | <span data-ttu-id="aa070-137">Příklad</span><span class="sxs-lookup"><span data-stu-id="aa070-137">Example</span></span>           | <span data-ttu-id="aa070-138">Příklad shody</span><span class="sxs-lookup"><span data-stu-id="aa070-138">Example Matches</span></span>                                                                  | <span data-ttu-id="aa070-139">Invariantní</span><span class="sxs-lookup"><span data-stu-id="aa070-139">Invariant</span></span><br><span data-ttu-id="aa070-140">jazyková verze</span><span class="sxs-lookup"><span data-stu-id="aa070-140">culture</span></span><br><span data-ttu-id="aa070-141">shoda</span><span class="sxs-lookup"><span data-stu-id="aa070-141">matching</span></span> |
| ---------- | ----------------- | -------------------------------------------------------------------------------- | :------------------------------: |
| `bool`     | `{active:bool}`   | <span data-ttu-id="aa070-142">`true`, `FALSE`</span><span class="sxs-lookup"><span data-stu-id="aa070-142">`true`, `FALSE`</span></span>                                                                  | <span data-ttu-id="aa070-143">Ne</span><span class="sxs-lookup"><span data-stu-id="aa070-143">No</span></span>                               |
| `datetime` | `{dob:datetime}`  | <span data-ttu-id="aa070-144">`2016-12-31`, `2016-12-31 7:32pm`</span><span class="sxs-lookup"><span data-stu-id="aa070-144">`2016-12-31`, `2016-12-31 7:32pm`</span></span>                                                | <span data-ttu-id="aa070-145">Ano</span><span class="sxs-lookup"><span data-stu-id="aa070-145">Yes</span></span>                              |
| `decimal`  | `{price:decimal}` | <span data-ttu-id="aa070-146">`49.99`, `-1,000.01`</span><span class="sxs-lookup"><span data-stu-id="aa070-146">`49.99`, `-1,000.01`</span></span>                                                             | <span data-ttu-id="aa070-147">Ano</span><span class="sxs-lookup"><span data-stu-id="aa070-147">Yes</span></span>                              |
| `double`   | `{weight:double}` | <span data-ttu-id="aa070-148">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="aa070-148">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="aa070-149">Ano</span><span class="sxs-lookup"><span data-stu-id="aa070-149">Yes</span></span>                              |
| `float`    | `{weight:float}`  | <span data-ttu-id="aa070-150">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="aa070-150">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="aa070-151">Ano</span><span class="sxs-lookup"><span data-stu-id="aa070-151">Yes</span></span>                              |
| `guid`     | `{id:guid}`       | <span data-ttu-id="aa070-152">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span><span class="sxs-lookup"><span data-stu-id="aa070-152">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span></span> | <span data-ttu-id="aa070-153">Ne</span><span class="sxs-lookup"><span data-stu-id="aa070-153">No</span></span>                               |
| `int`      | `{id:int}`        | <span data-ttu-id="aa070-154">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="aa070-154">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="aa070-155">Ano</span><span class="sxs-lookup"><span data-stu-id="aa070-155">Yes</span></span>                              |
| `long`     | `{ticks:long}`    | <span data-ttu-id="aa070-156">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="aa070-156">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="aa070-157">Ano</span><span class="sxs-lookup"><span data-stu-id="aa070-157">Yes</span></span>                              |

> [!WARNING]
> <span data-ttu-id="aa070-158">Směrovat omezení, která Ověřte adresu URL a jsou převedeny na typ CLR (například `int` nebo `DateTime`) vždy používejte neutrální jazykovou verzi.</span><span class="sxs-lookup"><span data-stu-id="aa070-158">Route constraints that verify the URL and are converted to a CLR type (such as `int` or `DateTime`) always use the invariant culture.</span></span> <span data-ttu-id="aa070-159">Tato omezení předpokládá, že adresa URL je nepřekládá.</span><span class="sxs-lookup"><span data-stu-id="aa070-159">These constraints assume that the URL is non-localizable.</span></span>

## <a name="navlink-component"></a><span data-ttu-id="aa070-160">Komponenta NavLink</span><span class="sxs-lookup"><span data-stu-id="aa070-160">NavLink component</span></span>

<span data-ttu-id="aa070-161">Použít komponentu NavLink namísto HTML `<a>` prvky při vytváření navigačních odkazů.</span><span class="sxs-lookup"><span data-stu-id="aa070-161">Use a NavLink component in place of HTML `<a>` elements when creating navigation links.</span></span> <span data-ttu-id="aa070-162">Komponentu NavLink se chová jako `<a>` elementu, s výjimkou přepíná `active` třídu šablony stylů CSS podle toho, jestli jeho `href` odpovídá aktuální adresa URL.</span><span class="sxs-lookup"><span data-stu-id="aa070-162">A NavLink component behaves like an `<a>` element, except it toggles an `active` CSS class based on whether its `href` matches the current URL.</span></span> <span data-ttu-id="aa070-163">`active` Třídy pomáhá uživateli vědět, na stránce, které je aktivní stránkou. mezi navigační odkazy zobrazí.</span><span class="sxs-lookup"><span data-stu-id="aa070-163">The `active` class helps a user understand which page is the active page among the navigation links displayed.</span></span>

<span data-ttu-id="aa070-164">Vytvoří následující komponenty NavMenu [Bootstrap](https://getbootstrap.com/docs/) navigační panel, který ukazuje, jak používat NavLink komponenty:</span><span class="sxs-lookup"><span data-stu-id="aa070-164">The following NavMenu component creates a [Bootstrap](https://getbootstrap.com/docs/) navigation bar that demonstrates how to use NavLink components:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Shared/NavMenu.razor?name=snippet_NavLinks&highlight=4-6,9-11)]

<span data-ttu-id="aa070-165">Existují dva `NavLinkMatch` možnosti:</span><span class="sxs-lookup"><span data-stu-id="aa070-165">There are two `NavLinkMatch` options:</span></span>

* <span data-ttu-id="aa070-166">`NavLinkMatch.All` &ndash; Určuje, že NavLink musí být v případě, že odpovídá celou adresu URL aktuální aktivní.</span><span class="sxs-lookup"><span data-stu-id="aa070-166">`NavLinkMatch.All` &ndash; Specifies that the NavLink should be active when it matches the entire current URL.</span></span>
* <span data-ttu-id="aa070-167">`NavLinkMatch.Prefix` &ndash; Určuje, že NavLink musí být v případě, že odpovídá jakoukoli předponu adresy URL aktuální aktivní.</span><span class="sxs-lookup"><span data-stu-id="aa070-167">`NavLinkMatch.Prefix` &ndash; Specifies that the NavLink should be active when it matches any prefix of the current URL.</span></span>

<span data-ttu-id="aa070-168">V předchozím příkladu Home NavLink (`href=""`) odpovídá všem adresám URL a vždy přijímá `active` třídu šablony stylů CSS.</span><span class="sxs-lookup"><span data-stu-id="aa070-168">In the preceding example, the Home NavLink (`href=""`) matches all URLs and always receives the `active` CSS class.</span></span> <span data-ttu-id="aa070-169">Druhý NavLink přijímá pouze `active` třídy, když uživatel navštíví komponentu Blazor trasy (`href="BlazorRoute"`).</span><span class="sxs-lookup"><span data-stu-id="aa070-169">The second NavLink only receives the `active` class when the user visits the Blazor Route component (`href="BlazorRoute"`).</span></span>

## <a name="uri-and-navigation-state-helpers"></a><span data-ttu-id="aa070-170">Identifikátor URI a navigační stav pomocné rutiny</span><span class="sxs-lookup"><span data-stu-id="aa070-170">URI and navigation state helpers</span></span>

<span data-ttu-id="aa070-171">Použití `Microsoft.AspNetCore.Components.IUriHelper` pro práci s identifikátory URI a navigace ve C# kódu.</span><span class="sxs-lookup"><span data-stu-id="aa070-171">Use `Microsoft.AspNetCore.Components.IUriHelper` to work with URIs and navigation in C# code.</span></span> <span data-ttu-id="aa070-172">`IUriHelper` poskytuje události a metody uvedené v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="aa070-172">`IUriHelper` provides the event and methods shown in the following table.</span></span>

| <span data-ttu-id="aa070-173">Člen</span><span class="sxs-lookup"><span data-stu-id="aa070-173">Member</span></span> | <span data-ttu-id="aa070-174">Popis</span><span class="sxs-lookup"><span data-stu-id="aa070-174">Description</span></span> |
| ------ | ----------- |
| `GetAbsoluteUri` | <span data-ttu-id="aa070-175">Získá aktuální absolutní identifikátor URI.</span><span class="sxs-lookup"><span data-stu-id="aa070-175">Gets the current absolute URI.</span></span> |
| `GetBaseUri` | <span data-ttu-id="aa070-176">Získá základní identifikátor URI (s koncovým lomítkem), který může být před relativní cesty URI k vytvoření absolutního identifikátoru URI.</span><span class="sxs-lookup"><span data-stu-id="aa070-176">Gets the base URI (with a trailing slash) that can be prepended to relative URI paths to produce an absolute URI.</span></span> <span data-ttu-id="aa070-177">Obvykle `GetBaseUri` odpovídá `href` atribut v dokumentu `<base>` prvek *wwwroot/index.html* (Blazor straně klienta) nebo *stránek /\_Host.cshtml* (Blazor – na straně serveru).</span><span class="sxs-lookup"><span data-stu-id="aa070-177">Typically, `GetBaseUri` corresponds to the `href` attribute on the document's `<base>` element in *wwwroot/index.html* (Blazor client-side) or *Pages/\_Host.cshtml* (Blazor server-side).</span></span> |
| `NavigateTo` | <span data-ttu-id="aa070-178">Přejde na zadaný identifikátor URI.</span><span class="sxs-lookup"><span data-stu-id="aa070-178">Navigates to the specified URI.</span></span> <span data-ttu-id="aa070-179">Pokud `forceLoad` je `true`:</span><span class="sxs-lookup"><span data-stu-id="aa070-179">If `forceLoad` is `true`:</span></span><ul><li><span data-ttu-id="aa070-180">Směrování na straně klienta se přeskočí.</span><span class="sxs-lookup"><span data-stu-id="aa070-180">Client-side routing is bypassed.</span></span></li><li><span data-ttu-id="aa070-181">Prohlížeč musí načíst nová stránka ze serveru, zda identifikátor URI obvykle zpracovává směrovače na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="aa070-181">The browser is forced to load the new page from the server, whether or not the URI is normally handled by the client-side router.</span></span></li></ul> |
| `OnLocationChanged` | <span data-ttu-id="aa070-182">Událost, která je vyvoláno, když se změní umístění navigace.</span><span class="sxs-lookup"><span data-stu-id="aa070-182">An event that fires when the navigation location has changed.</span></span> |
| `ToAbsoluteUri` | <span data-ttu-id="aa070-183">Převede relativní identifikátor URI na absolutní adresu URI.</span><span class="sxs-lookup"><span data-stu-id="aa070-183">Converts a relative URI into an absolute URI.</span></span> |
| `ToBaseRelativePath` | <span data-ttu-id="aa070-184">Zadaný základní identifikátor URI (například identifikátor URI předtím vrátila rutina `GetBaseUri`), převede na identifikátor URI relativní k základní identifikátor URI předponu absolutní identifikátor URI.</span><span class="sxs-lookup"><span data-stu-id="aa070-184">Given a base URI (for example, a URI previously returned by `GetBaseUri`), converts an absolute URI into a URI relative to the base URI prefix.</span></span> |

<span data-ttu-id="aa070-185">Následující komponenty přejde na součást aplikace čítače, při výběru tlačítka:</span><span class="sxs-lookup"><span data-stu-id="aa070-185">The following component navigates to the app's Counter component when the button is selected:</span></span>

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
