---
title: ASP.NET Core směrování Blazor
author: guardrex
description: Naučte se směrovat požadavky v aplikacích a o komponentě NavLink.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 08/23/2019
uid: blazor/routing
ms.openlocfilehash: ae3d7ab01185dd6f2e8e0f59b78c2e693fe464b0
ms.sourcegitcommit: 8b36f75b8931ae3f656e2a8e63572080adc78513
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/05/2019
ms.locfileid: "70310355"
---
# <a name="aspnet-core-blazor-routing"></a><span data-ttu-id="df3d6-103">ASP.NET Core směrování Blazor</span><span class="sxs-lookup"><span data-stu-id="df3d6-103">ASP.NET Core Blazor routing</span></span>

<span data-ttu-id="df3d6-104">Podle [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="df3d6-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="df3d6-105">Naučte se směrovat požadavky a používat `NavLink` komponentu k vytváření navigačních odkazů v aplikacích Blazor.</span><span class="sxs-lookup"><span data-stu-id="df3d6-105">Learn how to route requests and how to use the `NavLink` component to create navigation links in Blazor apps.</span></span>

## <a name="aspnet-core-endpoint-routing-integration"></a><span data-ttu-id="df3d6-106">Integrace směrování ASP.NET Core Endpoint</span><span class="sxs-lookup"><span data-stu-id="df3d6-106">ASP.NET Core endpoint routing integration</span></span>

<span data-ttu-id="df3d6-107">Blazor na straně serveru je integrovaná do [Směrování koncového bodu ASP.NET Core](xref:fundamentals/routing).</span><span class="sxs-lookup"><span data-stu-id="df3d6-107">Blazor server-side is integrated into [ASP.NET Core Endpoint Routing](xref:fundamentals/routing).</span></span> <span data-ttu-id="df3d6-108">Aplikace ASP.NET Core je nakonfigurovaná tak, aby přijímala příchozí připojení pro `MapBlazorHub` interaktivní `Startup.Configure`komponenty v:</span><span class="sxs-lookup"><span data-stu-id="df3d6-108">An ASP.NET Core app is configured to accept incoming connections for interactive components with `MapBlazorHub` in `Startup.Configure`:</span></span>

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

## <a name="route-templates"></a><span data-ttu-id="df3d6-109">Šablony směrování</span><span class="sxs-lookup"><span data-stu-id="df3d6-109">Route templates</span></span>

<span data-ttu-id="df3d6-110">`Router` Komponenta povolí směrování a pro každou dostupnou součást je k dispozici šablona směrování.</span><span class="sxs-lookup"><span data-stu-id="df3d6-110">The `Router` component enables routing, and a route template is provided to each accessible component.</span></span> <span data-ttu-id="df3d6-111">Komponenta se zobrazí v souboru *App. Razor:* `Router`</span><span class="sxs-lookup"><span data-stu-id="df3d6-111">The `Router` component appears in the *App.razor* file:</span></span>

<span data-ttu-id="df3d6-112">V Blazor na straně serveru nebo v aplikaci na straně klienta:</span><span class="sxs-lookup"><span data-stu-id="df3d6-112">In a Blazor server-side or client-side app:</span></span>

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

<span data-ttu-id="df3d6-113">Je-li soubor *. Razor* s `@page` direktivou kompilován, je k dispozici vygenerovaná <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> třída, která určuje šablonu trasy.</span><span class="sxs-lookup"><span data-stu-id="df3d6-113">When a *.razor* file with an `@page` directive is compiled, the generated class is provided a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> specifying the route template.</span></span> <span data-ttu-id="df3d6-114">V době běhu Směrovač vyhledává třídy komponent pomocí `RouteAttribute` a a vykresluje komponentu se šablonou směrování, která odpovídá požadované adrese URL.</span><span class="sxs-lookup"><span data-stu-id="df3d6-114">At runtime, the router looks for component classes with a `RouteAttribute` and renders the component with a route template that matches the requested URL.</span></span>

<span data-ttu-id="df3d6-115">Pro komponentu lze použít více šablon směrování.</span><span class="sxs-lookup"><span data-stu-id="df3d6-115">Multiple route templates can be applied to a component.</span></span> <span data-ttu-id="df3d6-116">Následující komponenta reaguje na požadavky pro `/BlazorRoute` a: `/DifferentBlazorRoute`</span><span class="sxs-lookup"><span data-stu-id="df3d6-116">The following component responds to requests for `/BlazorRoute` and `/DifferentBlazorRoute`:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/BlazorRoute.razor?name=snippet_BlazorRoute)]

> [!IMPORTANT]
> <span data-ttu-id="df3d6-117">Aby adresy URL bylo možné správně přeložit, musí aplikace v `<base>` souboru *wwwroot/index.html* obsahovat značku (Blazor na straně klienta) nebo *stránky/_Host. cshtml* (Blazor na straně serveru) se základní cestou `href` aplikace zadanou v atributu ( `<base href="/">`).</span><span class="sxs-lookup"><span data-stu-id="df3d6-117">For URLs to resolve correctly, the app must include a `<base>` tag in its *wwwroot/index.html* file (Blazor client-side) or *Pages/_Host.cshtml* file (Blazor server-side) with the app base path specified in the `href` attribute (`<base href="/">`).</span></span> <span data-ttu-id="df3d6-118">Další informace naleznete v tématu <xref:host-and-deploy/blazor/client-side#app-base-path>.</span><span class="sxs-lookup"><span data-stu-id="df3d6-118">For more information, see <xref:host-and-deploy/blazor/client-side#app-base-path>.</span></span>

## <a name="provide-custom-content-when-content-isnt-found"></a><span data-ttu-id="df3d6-119">Poskytnutí vlastního obsahu, když se nenalezne obsah</span><span class="sxs-lookup"><span data-stu-id="df3d6-119">Provide custom content when content isn't found</span></span>

<span data-ttu-id="df3d6-120">`Router` Komponenta umožňuje aplikaci zadat vlastní obsah, pokud se pro požadovanou trasu nenajde obsah.</span><span class="sxs-lookup"><span data-stu-id="df3d6-120">The `Router` component allows the app to specify custom content if content isn't found for the requested route.</span></span>

<span data-ttu-id="df3d6-121">V souboru *App. Razor* nastavte vlastní obsah v `<NotFound>` parametru `Router` šablony součásti:</span><span class="sxs-lookup"><span data-stu-id="df3d6-121">In the *App.razor* file, set custom content in the `<NotFound>` template parameter of the `Router` component:</span></span>

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

<span data-ttu-id="df3d6-122">Obsah `<NotFound>` může zahrnovat libovolné položky, jako jsou například jiné interaktivní součásti.</span><span class="sxs-lookup"><span data-stu-id="df3d6-122">The content of `<NotFound>` can include arbitrary items, such as other interactive components.</span></span>

## <a name="route-parameters"></a><span data-ttu-id="df3d6-123">Parametry směrování</span><span class="sxs-lookup"><span data-stu-id="df3d6-123">Route parameters</span></span>

<span data-ttu-id="df3d6-124">Směrovač používá parametry směrování k naplnění odpovídajících parametrů komponenty se stejným názvem (bez rozlišení velkých a malých písmen):</span><span class="sxs-lookup"><span data-stu-id="df3d6-124">The router uses route parameters to populate the corresponding component parameters with the same name (case insensitive):</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/RouteParameter.razor?name=snippet_RouteParameter&highlight=2,7-8)]

<span data-ttu-id="df3d6-125">Volitelné parametry nejsou podporované pro aplikace Blazor ve verzi ASP.NET Core 3,0 Preview.</span><span class="sxs-lookup"><span data-stu-id="df3d6-125">Optional parameters aren't supported for Blazor apps in ASP.NET Core 3.0 Preview.</span></span> <span data-ttu-id="df3d6-126">V `@page` předchozím příkladu jsou aplikovány dvě direktivy.</span><span class="sxs-lookup"><span data-stu-id="df3d6-126">Two `@page` directives are applied in the previous example.</span></span> <span data-ttu-id="df3d6-127">První umožňuje navigaci na součást bez parametru.</span><span class="sxs-lookup"><span data-stu-id="df3d6-127">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="df3d6-128">Druhá `@page` direktiva `{text}` převezme parametr Route a `Text` přiřadí hodnotu vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="df3d6-128">The second `@page` directive takes the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

## <a name="route-constraints"></a><span data-ttu-id="df3d6-129">Omezení trasy</span><span class="sxs-lookup"><span data-stu-id="df3d6-129">Route constraints</span></span>

<span data-ttu-id="df3d6-130">Omezení trasy vynutilo typ shodný s typem pro segment směrování do součásti.</span><span class="sxs-lookup"><span data-stu-id="df3d6-130">A route constraint enforces type matching on a route segment to a component.</span></span>

<span data-ttu-id="df3d6-131">V následujícím příkladu trasa k `Users` součásti odpovídá pouze v případě, že:</span><span class="sxs-lookup"><span data-stu-id="df3d6-131">In the following example, the route to the `Users` component only matches if:</span></span>

* <span data-ttu-id="df3d6-132">V adrese URL požadavku je přítomen segment směrování.`Id`</span><span class="sxs-lookup"><span data-stu-id="df3d6-132">An `Id` route segment is present on the request URL.</span></span>
* <span data-ttu-id="df3d6-133">Segment je celé číslo (`int`). `Id`</span><span class="sxs-lookup"><span data-stu-id="df3d6-133">The `Id` segment is an integer (`int`).</span></span>

[!code-cshtml[](routing/samples_snapshot/3.x/Constraint.razor?highlight=1)]

<span data-ttu-id="df3d6-134">K dispozici jsou omezení tras uvedená v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="df3d6-134">The route constraints shown in the following table are available.</span></span> <span data-ttu-id="df3d6-135">Pro omezení trasy, která se shodují s invariantní jazykovou verzí, se podívejte na upozornění pod tabulkou, kde najdete další informace.</span><span class="sxs-lookup"><span data-stu-id="df3d6-135">For the route constraints that match with the invariant culture, see the warning below the table for more information.</span></span>

| <span data-ttu-id="df3d6-136">Jedinečn</span><span class="sxs-lookup"><span data-stu-id="df3d6-136">Constraint</span></span> | <span data-ttu-id="df3d6-137">Příklad</span><span class="sxs-lookup"><span data-stu-id="df3d6-137">Example</span></span>           | <span data-ttu-id="df3d6-138">Příklady shody</span><span class="sxs-lookup"><span data-stu-id="df3d6-138">Example Matches</span></span>                                                                  | <span data-ttu-id="df3d6-139">Invariantní</span><span class="sxs-lookup"><span data-stu-id="df3d6-139">Invariant</span></span><br><span data-ttu-id="df3d6-140">jazyková verze</span><span class="sxs-lookup"><span data-stu-id="df3d6-140">culture</span></span><br><span data-ttu-id="df3d6-141">shoda</span><span class="sxs-lookup"><span data-stu-id="df3d6-141">matching</span></span> |
| ---------- | ----------------- | -------------------------------------------------------------------------------- | :------------------------------: |
| `bool`     | `{active:bool}`   | <span data-ttu-id="df3d6-142">`true`, `FALSE`</span><span class="sxs-lookup"><span data-stu-id="df3d6-142">`true`, `FALSE`</span></span>                                                                  | <span data-ttu-id="df3d6-143">Ne</span><span class="sxs-lookup"><span data-stu-id="df3d6-143">No</span></span>                               |
| `datetime` | `{dob:datetime}`  | <span data-ttu-id="df3d6-144">`2016-12-31`, `2016-12-31 7:32pm`</span><span class="sxs-lookup"><span data-stu-id="df3d6-144">`2016-12-31`, `2016-12-31 7:32pm`</span></span>                                                | <span data-ttu-id="df3d6-145">Ano</span><span class="sxs-lookup"><span data-stu-id="df3d6-145">Yes</span></span>                              |
| `decimal`  | `{price:decimal}` | <span data-ttu-id="df3d6-146">`49.99`, `-1,000.01`</span><span class="sxs-lookup"><span data-stu-id="df3d6-146">`49.99`, `-1,000.01`</span></span>                                                             | <span data-ttu-id="df3d6-147">Ano</span><span class="sxs-lookup"><span data-stu-id="df3d6-147">Yes</span></span>                              |
| `double`   | `{weight:double}` | <span data-ttu-id="df3d6-148">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="df3d6-148">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="df3d6-149">Ano</span><span class="sxs-lookup"><span data-stu-id="df3d6-149">Yes</span></span>                              |
| `float`    | `{weight:float}`  | <span data-ttu-id="df3d6-150">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="df3d6-150">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="df3d6-151">Ano</span><span class="sxs-lookup"><span data-stu-id="df3d6-151">Yes</span></span>                              |
| `guid`     | `{id:guid}`       | <span data-ttu-id="df3d6-152">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span><span class="sxs-lookup"><span data-stu-id="df3d6-152">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span></span> | <span data-ttu-id="df3d6-153">Ne</span><span class="sxs-lookup"><span data-stu-id="df3d6-153">No</span></span>                               |
| `int`      | `{id:int}`        | <span data-ttu-id="df3d6-154">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="df3d6-154">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="df3d6-155">Ano</span><span class="sxs-lookup"><span data-stu-id="df3d6-155">Yes</span></span>                              |
| `long`     | `{ticks:long}`    | <span data-ttu-id="df3d6-156">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="df3d6-156">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="df3d6-157">Ano</span><span class="sxs-lookup"><span data-stu-id="df3d6-157">Yes</span></span>                              |

> [!WARNING]
> <span data-ttu-id="df3d6-158">Omezení směrování, která ověřují adresu URL a jsou převedena na typ CLR (například `int` nebo `DateTime`), vždy používají invariantní jazykovou verzi.</span><span class="sxs-lookup"><span data-stu-id="df3d6-158">Route constraints that verify the URL and are converted to a CLR type (such as `int` or `DateTime`) always use the invariant culture.</span></span> <span data-ttu-id="df3d6-159">Tato omezení předpokládají, že adresa URL nelze lokalizovat.</span><span class="sxs-lookup"><span data-stu-id="df3d6-159">These constraints assume that the URL is non-localizable.</span></span>

### <a name="routing-with-urls-that-contain-dots"></a><span data-ttu-id="df3d6-160">Směrování s adresami URL, které obsahují tečky</span><span class="sxs-lookup"><span data-stu-id="df3d6-160">Routing with URLs that contain dots</span></span>

<span data-ttu-id="df3d6-161">V Blazorch aplikacích na straně serveru je `/` výchozí trasa v *_Host. cshtml* (`@page "/"`).</span><span class="sxs-lookup"><span data-stu-id="df3d6-161">In Blazor server-side apps, the default route in *_Host.cshtml* is `/` (`@page "/"`).</span></span> <span data-ttu-id="df3d6-162">Adresa URL požadavku, která obsahuje tečku`.`(), není shodná s výchozí cestou, protože adresa URL se zobrazí pro vyžádání souboru.</span><span class="sxs-lookup"><span data-stu-id="df3d6-162">A request URL that contains a dot (`.`) isn't matched by the default route because the URL appears to request a file.</span></span> <span data-ttu-id="df3d6-163">Aplikace Blazor vrací odpověď na *404, která nebyla nalezena* pro statický soubor, který neexistuje.</span><span class="sxs-lookup"><span data-stu-id="df3d6-163">A Blazor app returns a *404 - Not Found* response for a static file that doesn't exist.</span></span> <span data-ttu-id="df3d6-164">Chcete-li použít trasy, které obsahují tečku, nakonfigurujte *_Host. cshtml* s následující šablonou směrování:</span><span class="sxs-lookup"><span data-stu-id="df3d6-164">To use routes that contain a dot, configure *_Host.cshtml* with the following route template:</span></span>

```cshtml
@page "/{**path}"
```

<span data-ttu-id="df3d6-165">`"/{**path}"` Šablona obsahuje:</span><span class="sxs-lookup"><span data-stu-id="df3d6-165">The `"/{**path}"` template includes:</span></span>

* <span data-ttu-id="df3d6-166">Dvojitá hvězdička *– veškerá* syntaxe (`**`) pro zachycení cesty mezi více hranicemi složek bez kódování lomítka (`/`).</span><span class="sxs-lookup"><span data-stu-id="df3d6-166">Double-asterisk *catch-all* syntax (`**`) to capture the path across multiple folder boundaries without encoding forward slashes (`/`).</span></span>
* <span data-ttu-id="df3d6-167">Název `path` parametru trasy.</span><span class="sxs-lookup"><span data-stu-id="df3d6-167">A `path` route parameter name.</span></span>

<span data-ttu-id="df3d6-168">Další informace naleznete v tématu <xref:fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="df3d6-168">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="navlink-component"></a><span data-ttu-id="df3d6-169">Komponenta NavLink</span><span class="sxs-lookup"><span data-stu-id="df3d6-169">NavLink component</span></span>

<span data-ttu-id="df3d6-170">Použít komponentu namísto prvků hypertextového odkazu HTML (`<a>`) při vytváření navigačních odkazů. `NavLink`</span><span class="sxs-lookup"><span data-stu-id="df3d6-170">Use a `NavLink` component in place of HTML hyperlink elements (`<a>`) when creating navigation links.</span></span> <span data-ttu-id="df3d6-171">Komponenta se chová `<a>` jako element `active` s tím rozdílem, že přepíná třídu CSS na základě toho, `href` zda odpovídá aktuální adrese URL. `NavLink`</span><span class="sxs-lookup"><span data-stu-id="df3d6-171">A `NavLink` component behaves like an `<a>` element, except it toggles an `active` CSS class based on whether its `href` matches the current URL.</span></span> <span data-ttu-id="df3d6-172">`active` Třída pomáhá uživateli pochopit, která stránka je aktivní stránkou mezi zobrazenými navigačními odkazy.</span><span class="sxs-lookup"><span data-stu-id="df3d6-172">The `active` class helps a user understand which page is the active page among the navigation links displayed.</span></span>

<span data-ttu-id="df3d6-173">Následující `NavMenu` komponenta vytvoří navigační panel [bootstrap](https://getbootstrap.com/docs/) , který ukazuje, jak používat `NavLink` komponenty:</span><span class="sxs-lookup"><span data-stu-id="df3d6-173">The following `NavMenu` component creates a [Bootstrap](https://getbootstrap.com/docs/) navigation bar that demonstrates how to use `NavLink` components:</span></span>

[!code-cshtml[](routing/samples_snapshot/3.x/NavMenu.razor?highlight=4,9)]

<span data-ttu-id="df3d6-174">Existují dvě `NavLinkMatch` možnosti, které lze přiřadit `Match` k atributu `<NavLink>` elementu:</span><span class="sxs-lookup"><span data-stu-id="df3d6-174">There are two `NavLinkMatch` options that you can assign to the `Match` attribute of the `<NavLink>` element:</span></span>

* <span data-ttu-id="df3d6-175">`NavLinkMatch.All`&ndash; Jeaktivní,pokudodpovídá`NavLink` celé aktuální adrese URL.</span><span class="sxs-lookup"><span data-stu-id="df3d6-175">`NavLinkMatch.All` &ndash; The `NavLink` is active when it matches the entire current URL.</span></span>
* <span data-ttu-id="df3d6-176">`NavLinkMatch.Prefix`(*výchozí*) &ndash; Jeaktivní,pokudodpovídálibovolnépředponě`NavLink` aktuální adresy URL.</span><span class="sxs-lookup"><span data-stu-id="df3d6-176">`NavLinkMatch.Prefix` (*default*) &ndash; The `NavLink` is active when it matches any prefix of the current URL.</span></span>

<span data-ttu-id="df3d6-177">V předchozím příkladu se Domovská stránka `NavLink` `href=""` shoduje s `active` adresou URL domů a přijímá pouze třídu CSS ve `https://localhost:5001/`výchozí základní cestě URL aplikace (například).</span><span class="sxs-lookup"><span data-stu-id="df3d6-177">In the preceding example, the Home `NavLink` `href=""` matches the home URL and only receives the `active` CSS class at the app's default base path URL (for example, `https://localhost:5001/`).</span></span> <span data-ttu-id="df3d6-178">Druhá `NavLink` Získá třídu, `active` když uživatel `MyComponent` navštíví libovolnou adresu URL s předponou (například `https://localhost:5001/MyComponent` a `https://localhost:5001/MyComponent/AnotherSegment`).</span><span class="sxs-lookup"><span data-stu-id="df3d6-178">The second `NavLink` receives the `active` class when the user visits any URL with a `MyComponent` prefix (for example, `https://localhost:5001/MyComponent` and `https://localhost:5001/MyComponent/AnotherSegment`).</span></span>

<span data-ttu-id="df3d6-179">Další `NavLink` atributy komponenty jsou předány do vykreslené značky ukotvení.</span><span class="sxs-lookup"><span data-stu-id="df3d6-179">Additional `NavLink` component attributes are passed through to the rendered anchor tag.</span></span> <span data-ttu-id="df3d6-180">V následujícím příkladu `NavLink` komponenta `target` zahrnuje atribut:</span><span class="sxs-lookup"><span data-stu-id="df3d6-180">In the following example, the `NavLink` component includes the `target` attribute:</span></span>

```cshtml
<NavLink href="my-page" target="_blank">My page</NavLink>
```

<span data-ttu-id="df3d6-181">Vykresluje se následující kód HTML:</span><span class="sxs-lookup"><span data-stu-id="df3d6-181">The following HTML markup is rendered:</span></span>

```html
<a href="my-page" target="_blank" rel="noopener noreferrer">My page</a>
```

## <a name="uri-and-navigation-state-helpers"></a><span data-ttu-id="df3d6-182">Identifikátory URI a pomocníka pro stav navigace</span><span class="sxs-lookup"><span data-stu-id="df3d6-182">URI and navigation state helpers</span></span>

<span data-ttu-id="df3d6-183">Použijte `Microsoft.AspNetCore.Components.NavigationManager` pro práci s identifikátory URI a navigací v C# kódu.</span><span class="sxs-lookup"><span data-stu-id="df3d6-183">Use `Microsoft.AspNetCore.Components.NavigationManager` to work with URIs and navigation in C# code.</span></span> <span data-ttu-id="df3d6-184">`NavigationManager`poskytuje událost a metody uvedené v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="df3d6-184">`NavigationManager` provides the event and methods shown in the following table.</span></span>

| <span data-ttu-id="df3d6-185">Člen</span><span class="sxs-lookup"><span data-stu-id="df3d6-185">Member</span></span> | <span data-ttu-id="df3d6-186">Popis</span><span class="sxs-lookup"><span data-stu-id="df3d6-186">Description</span></span> |
| ------ | ----------- |
| `Uri` | <span data-ttu-id="df3d6-187">Získá aktuální absolutní identifikátor URI.</span><span class="sxs-lookup"><span data-stu-id="df3d6-187">Gets the current absolute URI.</span></span> |
| `BaseUri` | <span data-ttu-id="df3d6-188">Získá základní identifikátor URI (s koncovým lomítkem), který může být součástí relativních cest URI pro vytvoření absolutního identifikátoru URI.</span><span class="sxs-lookup"><span data-stu-id="df3d6-188">Gets the base URI (with a trailing slash) that can be prepended to relative URI paths to produce an absolute URI.</span></span> <span data-ttu-id="df3d6-189">`BaseUri` Obvykle odpovídá `href` *atributu v* prvku dokumentu v wwwroot/index.html (Blazor na straně klienta) nebo na *stránkách/_Host. cshtml* (Blazor na straně serveru). `<base>`</span><span class="sxs-lookup"><span data-stu-id="df3d6-189">Typically, `BaseUri` corresponds to the `href` attribute on the document's `<base>` element in *wwwroot/index.html* (Blazor client-side) or *Pages/_Host.cshtml* (Blazor server-side).</span></span> |
| `NavigateTo` | <span data-ttu-id="df3d6-190">Přejde k zadanému identifikátoru URI.</span><span class="sxs-lookup"><span data-stu-id="df3d6-190">Navigates to the specified URI.</span></span> <span data-ttu-id="df3d6-191">Pokud `forceLoad` je `true`:</span><span class="sxs-lookup"><span data-stu-id="df3d6-191">If `forceLoad` is `true`:</span></span><ul><li><span data-ttu-id="df3d6-192">Směrování na straně klienta se nepoužívá.</span><span class="sxs-lookup"><span data-stu-id="df3d6-192">Client-side routing is bypassed.</span></span></li><li><span data-ttu-id="df3d6-193">Prohlížeč je nucen načíst novou stránku ze serveru, bez ohledu na to, zda je identifikátor URI obvykle zpracováván směrovačem na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="df3d6-193">The browser is forced to load the new page from the server, whether or not the URI is normally handled by the client-side router.</span></span></li></ul> |
| `LocationChanged` | <span data-ttu-id="df3d6-194">Událost, která se aktivuje, když se změní navigační umístění</span><span class="sxs-lookup"><span data-stu-id="df3d6-194">An event that fires when the navigation location has changed.</span></span> |
| `ToAbsoluteUri` | <span data-ttu-id="df3d6-195">Převede relativní identifikátor URI na absolutní identifikátor URI.</span><span class="sxs-lookup"><span data-stu-id="df3d6-195">Converts a relative URI into an absolute URI.</span></span> |
| `ToBaseRelativePath` | <span data-ttu-id="df3d6-196">Vzhledem k základnímu identifikátoru URI (například identifikátor URI, který `GetBaseUri`dřív vrátil), převede absolutní identifikátor URI na URI relativně k základní předponě identifikátoru URI.</span><span class="sxs-lookup"><span data-stu-id="df3d6-196">Given a base URI (for example, a URI previously returned by `GetBaseUri`), converts an absolute URI into a URI relative to the base URI prefix.</span></span> |

<span data-ttu-id="df3d6-197">Pokud je vybráno tlačítko, následující komponenta přejde `Counter` na součást aplikace:</span><span class="sxs-lookup"><span data-stu-id="df3d6-197">The following component navigates to the app's `Counter` component when the button is selected:</span></span>

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

