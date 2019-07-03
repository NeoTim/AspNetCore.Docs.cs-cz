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
# <a name="aspnet-core-blazor-routing"></a><span data-ttu-id="28057-103">ASP.NET Core Blazor směrování</span><span class="sxs-lookup"><span data-stu-id="28057-103">ASP.NET Core Blazor routing</span></span>

<span data-ttu-id="28057-104">Podle [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="28057-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="28057-105">Naučte se směrovat požadavky a použít `NavLink` komponentu pro vytvoření navigační odkazy v aplikacích Blazor.</span><span class="sxs-lookup"><span data-stu-id="28057-105">Learn how to route requests and how to use the `NavLink` component to create navigation links in Blazor apps.</span></span>

## <a name="aspnet-core-endpoint-routing-integration"></a><span data-ttu-id="28057-106">Integrace směrování koncových bodů ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="28057-106">ASP.NET Core endpoint routing integration</span></span>

<span data-ttu-id="28057-107">Blazor na straně serveru je integrovaná do [směrování ASP.NET Core koncový bod](xref:fundamentals/routing).</span><span class="sxs-lookup"><span data-stu-id="28057-107">Blazor server-side is integrated into [ASP.NET Core Endpoint Routing](xref:fundamentals/routing).</span></span> <span data-ttu-id="28057-108">Aplikace ASP.NET Core je nakonfigurovaný tak, aby přijímal příchozí připojení pro interaktivní součásti s `MapBlazorHub` v `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="28057-108">An ASP.NET Core app is configured to accept incoming connections for interactive components with `MapBlazorHub` in `Startup.Configure`:</span></span>

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

## <a name="route-templates"></a><span data-ttu-id="28057-109">Šablony trasy</span><span class="sxs-lookup"><span data-stu-id="28057-109">Route templates</span></span>

<span data-ttu-id="28057-110">`Router` Součást umožňuje směrování a je k dispozici šablona trasy pro jednotlivé dostupné komponenty.</span><span class="sxs-lookup"><span data-stu-id="28057-110">The `Router` component enables routing, and a route template is provided to each accessible component.</span></span> <span data-ttu-id="28057-111">`Router` Součást se zobrazí v *App.razor* souboru:</span><span class="sxs-lookup"><span data-stu-id="28057-111">The `Router` component appears in the *App.razor* file:</span></span>

<span data-ttu-id="28057-112">V aplikaci na straně serveru Blazor:</span><span class="sxs-lookup"><span data-stu-id="28057-112">In a Blazor server-side app:</span></span>

```cshtml
<Router AppAssembly="typeof(Startup).Assembly" />
```

<span data-ttu-id="28057-113">V aplikaci na straně klienta Blazor:</span><span class="sxs-lookup"><span data-stu-id="28057-113">In a Blazor client-side app:</span></span>

```cshtml
<Router AppAssembly="typeof(Program).Assembly" />
```

<span data-ttu-id="28057-114">Při *.razor* soubor s `@page` – direktiva je zkompilován, je k dispozici generované třídy <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> zadání šablonu trasy.</span><span class="sxs-lookup"><span data-stu-id="28057-114">When a *.razor* file with an `@page` directive is compiled, the generated class is provided a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> specifying the route template.</span></span> <span data-ttu-id="28057-115">Za běhu, směrovač hledá komponentní třídy s `RouteAttribute` a vykreslí komponentu šablonou trasy, která odpovídá požadovanou adresu URL.</span><span class="sxs-lookup"><span data-stu-id="28057-115">At runtime, the router looks for component classes with a `RouteAttribute` and renders the component with a route template that matches the requested URL.</span></span>

<span data-ttu-id="28057-116">Více šablon trasy můžete použít pro komponentu.</span><span class="sxs-lookup"><span data-stu-id="28057-116">Multiple route templates can be applied to a component.</span></span> <span data-ttu-id="28057-117">Následující komponenty jsou reaguje na požadavky pro `/BlazorRoute` a `/DifferentBlazorRoute`:</span><span class="sxs-lookup"><span data-stu-id="28057-117">The following component responds to requests for `/BlazorRoute` and `/DifferentBlazorRoute`:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/BlazorRoute.razor?name=snippet_BlazorRoute)]

> [!IMPORTANT]
> <span data-ttu-id="28057-118">Ke generování tras správně, musí aplikace obsahovat `<base>` označení na jeho *wwwroot/index.html* souboru (Blazor straně klienta) nebo *Pages/_Host.cshtml* (Blazor serverové) soubor s základní cesta aplikace zadané v poli `href` atribut (`<base href="/">`).</span><span class="sxs-lookup"><span data-stu-id="28057-118">To generate routes properly, the app must include a `<base>` tag in its *wwwroot/index.html* file (Blazor client-side) or *Pages/_Host.cshtml* file (Blazor server-side) with the app base path specified in the `href` attribute (`<base href="/">`).</span></span> <span data-ttu-id="28057-119">Další informace naleznete v tématu <xref:host-and-deploy/blazor/client-side#app-base-path>.</span><span class="sxs-lookup"><span data-stu-id="28057-119">For more information, see <xref:host-and-deploy/blazor/client-side#app-base-path>.</span></span>

## <a name="provide-custom-content-when-content-isnt-found"></a><span data-ttu-id="28057-120">Zadejte vlastní obsah, když obsah nebyl nalezen</span><span class="sxs-lookup"><span data-stu-id="28057-120">Provide custom content when content isn't found</span></span>

<span data-ttu-id="28057-121">`Router` Komponenta umožňuje aplikaci určit vlastní obsah, pokud není nalezen obsah pro požadované trase.</span><span class="sxs-lookup"><span data-stu-id="28057-121">The `Router` component allows the app to specify custom content if content isn't found for the requested route.</span></span>

<span data-ttu-id="28057-122">V *App.razor* sady vlastní obsah v souboru `<NotFoundContent>` elementu `Router` komponenty:</span><span class="sxs-lookup"><span data-stu-id="28057-122">In the *App.razor* file, set custom content in the `<NotFoundContent>` element of the `Router` component:</span></span>

```cshtml
<Router AppAssembly="typeof(Startup).Assembly">
    <NotFoundContent>
        <h1>Sorry</h1>
        <p>Sorry, there's nothing at this address.</p> b
    </NotFoundContent>
</Router>
```

<span data-ttu-id="28057-123">Obsah `<NotFoundContent>` může obsahovat libovolné položky, jako další interaktivní komponenty.</span><span class="sxs-lookup"><span data-stu-id="28057-123">The content of `<NotFoundContent>` can include arbitrary items, such as other interactive components.</span></span>

## <a name="route-parameters"></a><span data-ttu-id="28057-124">Parametry trasy</span><span class="sxs-lookup"><span data-stu-id="28057-124">Route parameters</span></span>

<span data-ttu-id="28057-125">Směrovač používá parametry trasy k naplnění odpovídajících parametrů součásti se stejným názvem (malých písmen):</span><span class="sxs-lookup"><span data-stu-id="28057-125">The router uses route parameters to populate the corresponding component parameters with the same name (case insensitive):</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/RouteParameter.razor?name=snippet_RouteParameter&highlight=2,7-8)]

<span data-ttu-id="28057-126">Volitelné parametry nejsou podporovány pro Blazor aplikace v ASP.NET Core 3.0 ve verzi Preview.</span><span class="sxs-lookup"><span data-stu-id="28057-126">Optional parameters aren't supported for Blazor apps in ASP.NET Core 3.0 Preview.</span></span> <span data-ttu-id="28057-127">Dvě `@page` direktivy se použijí v předchozím příkladu.</span><span class="sxs-lookup"><span data-stu-id="28057-127">Two `@page` directives are applied in the previous example.</span></span> <span data-ttu-id="28057-128">První umožňuje přechod na komponenty bez parametrů.</span><span class="sxs-lookup"><span data-stu-id="28057-128">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="28057-129">Druhá `@page` trvá – direktiva `{text}` parametr trasa a přiřadí hodnotu do proměnné `Text` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="28057-129">The second `@page` directive takes the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

## <a name="route-constraints"></a><span data-ttu-id="28057-130">Omezení trasy</span><span class="sxs-lookup"><span data-stu-id="28057-130">Route constraints</span></span>

<span data-ttu-id="28057-131">Omezení trasy vynucuje typ odpovídající v segmentu směrování do komponenty.</span><span class="sxs-lookup"><span data-stu-id="28057-131">A route constraint enforces type matching on a route segment to a component.</span></span>

<span data-ttu-id="28057-132">V následujícím příkladu, trasy, která má `Users` komponenta odpovídá pouze, pokud:</span><span class="sxs-lookup"><span data-stu-id="28057-132">In the following example, the route to the `Users` component only matches if:</span></span>

* <span data-ttu-id="28057-133">`Id` Segment směrování je k dispozici na adrese URL požadavku.</span><span class="sxs-lookup"><span data-stu-id="28057-133">An `Id` route segment is present on the request URL.</span></span>
* <span data-ttu-id="28057-134">`Id` Segmentu je celé číslo (`int`).</span><span class="sxs-lookup"><span data-stu-id="28057-134">The `Id` segment is an integer (`int`).</span></span>

[!code-cshtml[](routing/samples_snapshot/3.x/Constraint.razor?highlight=1)]

<span data-ttu-id="28057-135">Omezení trasy, které jsou uvedeny v následující tabulce jsou k dispozici.</span><span class="sxs-lookup"><span data-stu-id="28057-135">The route constraints shown in the following table are available.</span></span> <span data-ttu-id="28057-136">Omezení trasy, které odpovídají neutrální jazykové verzi najdete v upozornění níže uvedená tabulka pro další informace.</span><span class="sxs-lookup"><span data-stu-id="28057-136">For the route constraints that match with the invariant culture, see the warning below the table for more information.</span></span>

| <span data-ttu-id="28057-137">Omezení</span><span class="sxs-lookup"><span data-stu-id="28057-137">Constraint</span></span> | <span data-ttu-id="28057-138">Příklad</span><span class="sxs-lookup"><span data-stu-id="28057-138">Example</span></span>           | <span data-ttu-id="28057-139">Příklad shody</span><span class="sxs-lookup"><span data-stu-id="28057-139">Example Matches</span></span>                                                                  | <span data-ttu-id="28057-140">Invariantní</span><span class="sxs-lookup"><span data-stu-id="28057-140">Invariant</span></span><br><span data-ttu-id="28057-141">jazyková verze</span><span class="sxs-lookup"><span data-stu-id="28057-141">culture</span></span><br><span data-ttu-id="28057-142">shoda</span><span class="sxs-lookup"><span data-stu-id="28057-142">matching</span></span> |
| ---------- | ----------------- | -------------------------------------------------------------------------------- | :------------------------------: |
| `bool`     | `{active:bool}`   | <span data-ttu-id="28057-143">`true`, `FALSE`</span><span class="sxs-lookup"><span data-stu-id="28057-143">`true`, `FALSE`</span></span>                                                                  | <span data-ttu-id="28057-144">Ne</span><span class="sxs-lookup"><span data-stu-id="28057-144">No</span></span>                               |
| `datetime` | `{dob:datetime}`  | <span data-ttu-id="28057-145">`2016-12-31`, `2016-12-31 7:32pm`</span><span class="sxs-lookup"><span data-stu-id="28057-145">`2016-12-31`, `2016-12-31 7:32pm`</span></span>                                                | <span data-ttu-id="28057-146">Ano</span><span class="sxs-lookup"><span data-stu-id="28057-146">Yes</span></span>                              |
| `decimal`  | `{price:decimal}` | <span data-ttu-id="28057-147">`49.99`, `-1,000.01`</span><span class="sxs-lookup"><span data-stu-id="28057-147">`49.99`, `-1,000.01`</span></span>                                                             | <span data-ttu-id="28057-148">Ano</span><span class="sxs-lookup"><span data-stu-id="28057-148">Yes</span></span>                              |
| `double`   | `{weight:double}` | <span data-ttu-id="28057-149">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="28057-149">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="28057-150">Ano</span><span class="sxs-lookup"><span data-stu-id="28057-150">Yes</span></span>                              |
| `float`    | `{weight:float}`  | <span data-ttu-id="28057-151">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="28057-151">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="28057-152">Ano</span><span class="sxs-lookup"><span data-stu-id="28057-152">Yes</span></span>                              |
| `guid`     | `{id:guid}`       | <span data-ttu-id="28057-153">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span><span class="sxs-lookup"><span data-stu-id="28057-153">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span></span> | <span data-ttu-id="28057-154">Ne</span><span class="sxs-lookup"><span data-stu-id="28057-154">No</span></span>                               |
| `int`      | `{id:int}`        | <span data-ttu-id="28057-155">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="28057-155">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="28057-156">Ano</span><span class="sxs-lookup"><span data-stu-id="28057-156">Yes</span></span>                              |
| `long`     | `{ticks:long}`    | <span data-ttu-id="28057-157">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="28057-157">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="28057-158">Ano</span><span class="sxs-lookup"><span data-stu-id="28057-158">Yes</span></span>                              |

> [!WARNING]
> <span data-ttu-id="28057-159">Směrovat omezení, která Ověřte adresu URL a jsou převedeny na typ CLR (například `int` nebo `DateTime`) vždy používejte neutrální jazykovou verzi.</span><span class="sxs-lookup"><span data-stu-id="28057-159">Route constraints that verify the URL and are converted to a CLR type (such as `int` or `DateTime`) always use the invariant culture.</span></span> <span data-ttu-id="28057-160">Tato omezení předpokládá, že adresa URL je nepřekládá.</span><span class="sxs-lookup"><span data-stu-id="28057-160">These constraints assume that the URL is non-localizable.</span></span>

## <a name="navlink-component"></a><span data-ttu-id="28057-161">Komponenta NavLink</span><span class="sxs-lookup"><span data-stu-id="28057-161">NavLink component</span></span>

<span data-ttu-id="28057-162">Použití `NavLink` komponentu místo elementů HTML hypertextový odkaz (`<a>`) při vytváření navigačních odkazů.</span><span class="sxs-lookup"><span data-stu-id="28057-162">Use a `NavLink` component in place of HTML hyperlink elements (`<a>`) when creating navigation links.</span></span> <span data-ttu-id="28057-163">A `NavLink` se chová jako součást `<a>` elementu, s výjimkou přepíná `active` třídu šablony stylů CSS podle toho, jestli jeho `href` odpovídá aktuální adresa URL.</span><span class="sxs-lookup"><span data-stu-id="28057-163">A `NavLink` component behaves like an `<a>` element, except it toggles an `active` CSS class based on whether its `href` matches the current URL.</span></span> <span data-ttu-id="28057-164">`active` Třídy pomáhá uživateli vědět, na stránce, které je aktivní stránkou. mezi navigační odkazy zobrazí.</span><span class="sxs-lookup"><span data-stu-id="28057-164">The `active` class helps a user understand which page is the active page among the navigation links displayed.</span></span>

<span data-ttu-id="28057-165">Následující `NavMenu` vytvoří komponentu [Bootstrap](https://getbootstrap.com/docs/) navigační panel, který ukazuje, jak používat `NavLink` komponenty:</span><span class="sxs-lookup"><span data-stu-id="28057-165">The following `NavMenu` component creates a [Bootstrap](https://getbootstrap.com/docs/) navigation bar that demonstrates how to use `NavLink` components:</span></span>

[!code-cshtml[](routing/samples_snapshot/3.x/NavMenu.razor?highlight=4,9)]

<span data-ttu-id="28057-166">Existují dva `NavLinkMatch` možnosti, které můžete přiřadit `Match` atribut `<NavLink>` element:</span><span class="sxs-lookup"><span data-stu-id="28057-166">There are two `NavLinkMatch` options that you can assign to the `Match` attribute of the `<NavLink>` element:</span></span>

* <span data-ttu-id="28057-167">`NavLinkMatch.All` &ndash; `NavLink` Je aktivní v případě, že odpovídá celou aktuální adresu URL.</span><span class="sxs-lookup"><span data-stu-id="28057-167">`NavLinkMatch.All` &ndash; The `NavLink` is active when it matches the entire current URL.</span></span>
* <span data-ttu-id="28057-168">`NavLinkMatch.Prefix` (*výchozí*) &ndash; `NavLink` je aktivní v případě, že odpovídá jakoukoli předponu adresy URL aktuální.</span><span class="sxs-lookup"><span data-stu-id="28057-168">`NavLinkMatch.Prefix` (*default*) &ndash; The `NavLink` is active when it matches any prefix of the current URL.</span></span>

<span data-ttu-id="28057-169">V předchozím příkladu, domovská stránka `NavLink` `href=""` odpovídá domovské adresy URL a přijímá pouze `active` třídu šablony stylů CSS v aplikace výchozí základní cesta URL (například `https://localhost:5001/`).</span><span class="sxs-lookup"><span data-stu-id="28057-169">In the preceding example, the Home `NavLink` `href=""` matches the home URL and only receives the `active` CSS class at the app's default base path URL (for example, `https://localhost:5001/`).</span></span> <span data-ttu-id="28057-170">Druhá `NavLink` přijímá `active` třídy, když uživatel navštíví libovolnou adresu URL s `MyComponent` předpona (například `https://localhost:5001/MyComponent` a `https://localhost:5001/MyComponent/AnotherSegment`).</span><span class="sxs-lookup"><span data-stu-id="28057-170">The second `NavLink` receives the `active` class when the user visits any URL with a `MyComponent` prefix (for example, `https://localhost:5001/MyComponent` and `https://localhost:5001/MyComponent/AnotherSegment`).</span></span>

## <a name="uri-and-navigation-state-helpers"></a><span data-ttu-id="28057-171">Identifikátor URI a navigační stav pomocné rutiny</span><span class="sxs-lookup"><span data-stu-id="28057-171">URI and navigation state helpers</span></span>

<span data-ttu-id="28057-172">Použití `Microsoft.AspNetCore.Components.IUriHelper` pro práci s identifikátory URI a navigace ve C# kódu.</span><span class="sxs-lookup"><span data-stu-id="28057-172">Use `Microsoft.AspNetCore.Components.IUriHelper` to work with URIs and navigation in C# code.</span></span> <span data-ttu-id="28057-173">`IUriHelper` poskytuje události a metody uvedené v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="28057-173">`IUriHelper` provides the event and methods shown in the following table.</span></span>

| <span data-ttu-id="28057-174">Člen</span><span class="sxs-lookup"><span data-stu-id="28057-174">Member</span></span> | <span data-ttu-id="28057-175">Popis</span><span class="sxs-lookup"><span data-stu-id="28057-175">Description</span></span> |
| ------ | ----------- |
| `GetAbsoluteUri` | <span data-ttu-id="28057-176">Získá aktuální absolutní identifikátor URI.</span><span class="sxs-lookup"><span data-stu-id="28057-176">Gets the current absolute URI.</span></span> |
| `GetBaseUri` | <span data-ttu-id="28057-177">Získá základní identifikátor URI (s koncovým lomítkem), který může být před relativní cesty URI k vytvoření absolutního identifikátoru URI.</span><span class="sxs-lookup"><span data-stu-id="28057-177">Gets the base URI (with a trailing slash) that can be prepended to relative URI paths to produce an absolute URI.</span></span> <span data-ttu-id="28057-178">Obvykle `GetBaseUri` odpovídá `href` atribut v dokumentu `<base>` prvek *wwwroot/index.html* (Blazor straně klienta) nebo *Pages/_Host.cshtml* () Blazor-na straně serveru).</span><span class="sxs-lookup"><span data-stu-id="28057-178">Typically, `GetBaseUri` corresponds to the `href` attribute on the document's `<base>` element in *wwwroot/index.html* (Blazor client-side) or *Pages/_Host.cshtml* (Blazor server-side).</span></span> |
| `NavigateTo` | <span data-ttu-id="28057-179">Přejde na zadaný identifikátor URI.</span><span class="sxs-lookup"><span data-stu-id="28057-179">Navigates to the specified URI.</span></span> <span data-ttu-id="28057-180">Pokud `forceLoad` je `true`:</span><span class="sxs-lookup"><span data-stu-id="28057-180">If `forceLoad` is `true`:</span></span><ul><li><span data-ttu-id="28057-181">Směrování na straně klienta se přeskočí.</span><span class="sxs-lookup"><span data-stu-id="28057-181">Client-side routing is bypassed.</span></span></li><li><span data-ttu-id="28057-182">Prohlížeč musí načíst nová stránka ze serveru, zda identifikátor URI obvykle zpracovává směrovače na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="28057-182">The browser is forced to load the new page from the server, whether or not the URI is normally handled by the client-side router.</span></span></li></ul> |
| `OnLocationChanged` | <span data-ttu-id="28057-183">Událost, která je vyvoláno, když se změní umístění navigace.</span><span class="sxs-lookup"><span data-stu-id="28057-183">An event that fires when the navigation location has changed.</span></span> |
| `ToAbsoluteUri` | <span data-ttu-id="28057-184">Převede relativní identifikátor URI na absolutní adresu URI.</span><span class="sxs-lookup"><span data-stu-id="28057-184">Converts a relative URI into an absolute URI.</span></span> |
| `ToBaseRelativePath` | <span data-ttu-id="28057-185">Zadaný základní identifikátor URI (například identifikátor URI předtím vrátila rutina `GetBaseUri`), převede na identifikátor URI relativní k základní identifikátor URI předponu absolutní identifikátor URI.</span><span class="sxs-lookup"><span data-stu-id="28057-185">Given a base URI (for example, a URI previously returned by `GetBaseUri`), converts an absolute URI into a URI relative to the base URI prefix.</span></span> |

<span data-ttu-id="28057-186">Následující komponenty přejde na aplikaci `Counter` součásti při výběru tlačítka:</span><span class="sxs-lookup"><span data-stu-id="28057-186">The following component navigates to the app's `Counter` component when the button is selected:</span></span>

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
