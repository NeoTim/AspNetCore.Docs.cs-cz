---
title: ASP.NET Core směrování Blazor
author: guardrex
description: Naučte se směrovat požadavky v aplikacích a o komponentě NavLink.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 08/23/2019
uid: blazor/routing
ms.openlocfilehash: 067dad657c1e89a31fac45fdfa095cce4b10798d
ms.sourcegitcommit: e6bd2bbe5683e9a7dbbc2f2eab644986e6dc8a87
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/03/2019
ms.locfileid: "70238060"
---
# <a name="aspnet-core-blazor-routing"></a><span data-ttu-id="aaa50-103">ASP.NET Core směrování Blazor</span><span class="sxs-lookup"><span data-stu-id="aaa50-103">ASP.NET Core Blazor routing</span></span>

<span data-ttu-id="aaa50-104">Podle [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="aaa50-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="aaa50-105">Naučte se směrovat požadavky a používat `NavLink` komponentu k vytváření navigačních odkazů v aplikacích Blazor.</span><span class="sxs-lookup"><span data-stu-id="aaa50-105">Learn how to route requests and how to use the `NavLink` component to create navigation links in Blazor apps.</span></span>

## <a name="aspnet-core-endpoint-routing-integration"></a><span data-ttu-id="aaa50-106">Integrace směrování ASP.NET Core Endpoint</span><span class="sxs-lookup"><span data-stu-id="aaa50-106">ASP.NET Core endpoint routing integration</span></span>

<span data-ttu-id="aaa50-107">Blazor na straně serveru je integrovaná do [Směrování koncového bodu ASP.NET Core](xref:fundamentals/routing).</span><span class="sxs-lookup"><span data-stu-id="aaa50-107">Blazor server-side is integrated into [ASP.NET Core Endpoint Routing](xref:fundamentals/routing).</span></span> <span data-ttu-id="aaa50-108">Aplikace ASP.NET Core je nakonfigurovaná tak, aby přijímala příchozí připojení pro `MapBlazorHub` interaktivní `Startup.Configure`komponenty v:</span><span class="sxs-lookup"><span data-stu-id="aaa50-108">An ASP.NET Core app is configured to accept incoming connections for interactive components with `MapBlazorHub` in `Startup.Configure`:</span></span>

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

## <a name="route-templates"></a><span data-ttu-id="aaa50-109">Šablony směrování</span><span class="sxs-lookup"><span data-stu-id="aaa50-109">Route templates</span></span>

<span data-ttu-id="aaa50-110">`Router` Komponenta povolí směrování a pro každou dostupnou součást je k dispozici šablona směrování.</span><span class="sxs-lookup"><span data-stu-id="aaa50-110">The `Router` component enables routing, and a route template is provided to each accessible component.</span></span> <span data-ttu-id="aaa50-111">Komponenta se zobrazí v souboru *App. Razor:* `Router`</span><span class="sxs-lookup"><span data-stu-id="aaa50-111">The `Router` component appears in the *App.razor* file:</span></span>

<span data-ttu-id="aaa50-112">V aplikaci Blazor na straně serveru:</span><span class="sxs-lookup"><span data-stu-id="aaa50-112">In a Blazor server-side app:</span></span>

```cshtml
<Router AppAssembly="typeof(Startup).Assembly" />
```

<span data-ttu-id="aaa50-113">V aplikaci Blazor na straně klienta:</span><span class="sxs-lookup"><span data-stu-id="aaa50-113">In a Blazor client-side app:</span></span>

```cshtml
<Router AppAssembly="typeof(Program).Assembly" />
```

<span data-ttu-id="aaa50-114">Je-li soubor *. Razor* s `@page` direktivou kompilován, je k dispozici vygenerovaná <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> třída, která určuje šablonu trasy.</span><span class="sxs-lookup"><span data-stu-id="aaa50-114">When a *.razor* file with an `@page` directive is compiled, the generated class is provided a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> specifying the route template.</span></span> <span data-ttu-id="aaa50-115">V době běhu Směrovač vyhledává třídy komponent pomocí `RouteAttribute` a a vykresluje komponentu se šablonou směrování, která odpovídá požadované adrese URL.</span><span class="sxs-lookup"><span data-stu-id="aaa50-115">At runtime, the router looks for component classes with a `RouteAttribute` and renders the component with a route template that matches the requested URL.</span></span>

<span data-ttu-id="aaa50-116">Pro komponentu lze použít více šablon směrování.</span><span class="sxs-lookup"><span data-stu-id="aaa50-116">Multiple route templates can be applied to a component.</span></span> <span data-ttu-id="aaa50-117">Následující komponenta reaguje na požadavky pro `/BlazorRoute` a: `/DifferentBlazorRoute`</span><span class="sxs-lookup"><span data-stu-id="aaa50-117">The following component responds to requests for `/BlazorRoute` and `/DifferentBlazorRoute`:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/BlazorRoute.razor?name=snippet_BlazorRoute)]

> [!IMPORTANT]
> <span data-ttu-id="aaa50-118">Aby bylo možné správně Generovat trasy, musí aplikace zahrnovat `<base>` značku v souboru *wwwroot/index.html* (Blazor na straně klienta) nebo *stránky/_Host. cshtml* (Blazor na straně serveru) se základní cestou `href` aplikace zadanou v atributu ( `<base href="/">`).</span><span class="sxs-lookup"><span data-stu-id="aaa50-118">To generate routes properly, the app must include a `<base>` tag in its *wwwroot/index.html* file (Blazor client-side) or *Pages/_Host.cshtml* file (Blazor server-side) with the app base path specified in the `href` attribute (`<base href="/">`).</span></span> <span data-ttu-id="aaa50-119">Další informace naleznete v tématu <xref:host-and-deploy/blazor/client-side#app-base-path>.</span><span class="sxs-lookup"><span data-stu-id="aaa50-119">For more information, see <xref:host-and-deploy/blazor/client-side#app-base-path>.</span></span>

## <a name="provide-custom-content-when-content-isnt-found"></a><span data-ttu-id="aaa50-120">Poskytnutí vlastního obsahu, když se nenalezne obsah</span><span class="sxs-lookup"><span data-stu-id="aaa50-120">Provide custom content when content isn't found</span></span>

<span data-ttu-id="aaa50-121">`Router` Komponenta umožňuje aplikaci zadat vlastní obsah, pokud se pro požadovanou trasu nenajde obsah.</span><span class="sxs-lookup"><span data-stu-id="aaa50-121">The `Router` component allows the app to specify custom content if content isn't found for the requested route.</span></span>

<span data-ttu-id="aaa50-122">V souboru *App. Razor* nastavte vlastní obsah v `<NotFoundContent>` elementu `Router` komponenty:</span><span class="sxs-lookup"><span data-stu-id="aaa50-122">In the *App.razor* file, set custom content in the `<NotFoundContent>` element of the `Router` component:</span></span>

```cshtml
<Router AppAssembly="typeof(Startup).Assembly">
    <NotFoundContent>
        <h1>Sorry</h1>
        <p>Sorry, there's nothing at this address.</p> b
    </NotFoundContent>
</Router>
```

<span data-ttu-id="aaa50-123">Obsah `<NotFoundContent>` může zahrnovat libovolné položky, jako jsou například jiné interaktivní součásti.</span><span class="sxs-lookup"><span data-stu-id="aaa50-123">The content of `<NotFoundContent>` can include arbitrary items, such as other interactive components.</span></span>

## <a name="route-parameters"></a><span data-ttu-id="aaa50-124">Parametry směrování</span><span class="sxs-lookup"><span data-stu-id="aaa50-124">Route parameters</span></span>

<span data-ttu-id="aaa50-125">Směrovač používá parametry směrování k naplnění odpovídajících parametrů komponenty se stejným názvem (bez rozlišení velkých a malých písmen):</span><span class="sxs-lookup"><span data-stu-id="aaa50-125">The router uses route parameters to populate the corresponding component parameters with the same name (case insensitive):</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/RouteParameter.razor?name=snippet_RouteParameter&highlight=2,7-8)]

<span data-ttu-id="aaa50-126">Volitelné parametry nejsou podporované pro aplikace Blazor ve verzi ASP.NET Core 3,0 Preview.</span><span class="sxs-lookup"><span data-stu-id="aaa50-126">Optional parameters aren't supported for Blazor apps in ASP.NET Core 3.0 Preview.</span></span> <span data-ttu-id="aaa50-127">V `@page` předchozím příkladu jsou aplikovány dvě direktivy.</span><span class="sxs-lookup"><span data-stu-id="aaa50-127">Two `@page` directives are applied in the previous example.</span></span> <span data-ttu-id="aaa50-128">První umožňuje navigaci na součást bez parametru.</span><span class="sxs-lookup"><span data-stu-id="aaa50-128">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="aaa50-129">Druhá `@page` direktiva `{text}` převezme parametr Route a `Text` přiřadí hodnotu vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="aaa50-129">The second `@page` directive takes the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

## <a name="route-constraints"></a><span data-ttu-id="aaa50-130">Omezení trasy</span><span class="sxs-lookup"><span data-stu-id="aaa50-130">Route constraints</span></span>

<span data-ttu-id="aaa50-131">Omezení trasy vynutilo typ shodný s typem pro segment směrování do součásti.</span><span class="sxs-lookup"><span data-stu-id="aaa50-131">A route constraint enforces type matching on a route segment to a component.</span></span>

<span data-ttu-id="aaa50-132">V následujícím příkladu trasa k `Users` součásti odpovídá pouze v případě, že:</span><span class="sxs-lookup"><span data-stu-id="aaa50-132">In the following example, the route to the `Users` component only matches if:</span></span>

* <span data-ttu-id="aaa50-133">V adrese URL požadavku je přítomen segment směrování.`Id`</span><span class="sxs-lookup"><span data-stu-id="aaa50-133">An `Id` route segment is present on the request URL.</span></span>
* <span data-ttu-id="aaa50-134">Segment je celé číslo (`int`). `Id`</span><span class="sxs-lookup"><span data-stu-id="aaa50-134">The `Id` segment is an integer (`int`).</span></span>

[!code-cshtml[](routing/samples_snapshot/3.x/Constraint.razor?highlight=1)]

<span data-ttu-id="aaa50-135">K dispozici jsou omezení tras uvedená v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="aaa50-135">The route constraints shown in the following table are available.</span></span> <span data-ttu-id="aaa50-136">Pro omezení trasy, která se shodují s invariantní jazykovou verzí, se podívejte na upozornění pod tabulkou, kde najdete další informace.</span><span class="sxs-lookup"><span data-stu-id="aaa50-136">For the route constraints that match with the invariant culture, see the warning below the table for more information.</span></span>

| <span data-ttu-id="aaa50-137">Jedinečn</span><span class="sxs-lookup"><span data-stu-id="aaa50-137">Constraint</span></span> | <span data-ttu-id="aaa50-138">Příklad</span><span class="sxs-lookup"><span data-stu-id="aaa50-138">Example</span></span>           | <span data-ttu-id="aaa50-139">Příklady shody</span><span class="sxs-lookup"><span data-stu-id="aaa50-139">Example Matches</span></span>                                                                  | <span data-ttu-id="aaa50-140">Invariantní</span><span class="sxs-lookup"><span data-stu-id="aaa50-140">Invariant</span></span><br><span data-ttu-id="aaa50-141">jazyková verze</span><span class="sxs-lookup"><span data-stu-id="aaa50-141">culture</span></span><br><span data-ttu-id="aaa50-142">shoda</span><span class="sxs-lookup"><span data-stu-id="aaa50-142">matching</span></span> |
| ---------- | ----------------- | -------------------------------------------------------------------------------- | :------------------------------: |
| `bool`     | `{active:bool}`   | <span data-ttu-id="aaa50-143">`true`, `FALSE`</span><span class="sxs-lookup"><span data-stu-id="aaa50-143">`true`, `FALSE`</span></span>                                                                  | <span data-ttu-id="aaa50-144">Ne</span><span class="sxs-lookup"><span data-stu-id="aaa50-144">No</span></span>                               |
| `datetime` | `{dob:datetime}`  | <span data-ttu-id="aaa50-145">`2016-12-31`, `2016-12-31 7:32pm`</span><span class="sxs-lookup"><span data-stu-id="aaa50-145">`2016-12-31`, `2016-12-31 7:32pm`</span></span>                                                | <span data-ttu-id="aaa50-146">Ano</span><span class="sxs-lookup"><span data-stu-id="aaa50-146">Yes</span></span>                              |
| `decimal`  | `{price:decimal}` | <span data-ttu-id="aaa50-147">`49.99`, `-1,000.01`</span><span class="sxs-lookup"><span data-stu-id="aaa50-147">`49.99`, `-1,000.01`</span></span>                                                             | <span data-ttu-id="aaa50-148">Ano</span><span class="sxs-lookup"><span data-stu-id="aaa50-148">Yes</span></span>                              |
| `double`   | `{weight:double}` | <span data-ttu-id="aaa50-149">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="aaa50-149">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="aaa50-150">Ano</span><span class="sxs-lookup"><span data-stu-id="aaa50-150">Yes</span></span>                              |
| `float`    | `{weight:float}`  | <span data-ttu-id="aaa50-151">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="aaa50-151">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="aaa50-152">Ano</span><span class="sxs-lookup"><span data-stu-id="aaa50-152">Yes</span></span>                              |
| `guid`     | `{id:guid}`       | <span data-ttu-id="aaa50-153">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span><span class="sxs-lookup"><span data-stu-id="aaa50-153">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span></span> | <span data-ttu-id="aaa50-154">Ne</span><span class="sxs-lookup"><span data-stu-id="aaa50-154">No</span></span>                               |
| `int`      | `{id:int}`        | <span data-ttu-id="aaa50-155">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="aaa50-155">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="aaa50-156">Ano</span><span class="sxs-lookup"><span data-stu-id="aaa50-156">Yes</span></span>                              |
| `long`     | `{ticks:long}`    | <span data-ttu-id="aaa50-157">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="aaa50-157">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="aaa50-158">Ano</span><span class="sxs-lookup"><span data-stu-id="aaa50-158">Yes</span></span>                              |

> [!WARNING]
> <span data-ttu-id="aaa50-159">Omezení směrování, která ověřují adresu URL a jsou převedena na typ CLR (například `int` nebo `DateTime`), vždy používají invariantní jazykovou verzi.</span><span class="sxs-lookup"><span data-stu-id="aaa50-159">Route constraints that verify the URL and are converted to a CLR type (such as `int` or `DateTime`) always use the invariant culture.</span></span> <span data-ttu-id="aaa50-160">Tato omezení předpokládají, že adresa URL nelze lokalizovat.</span><span class="sxs-lookup"><span data-stu-id="aaa50-160">These constraints assume that the URL is non-localizable.</span></span>

### <a name="routing-with-urls-that-contain-dots"></a><span data-ttu-id="aaa50-161">Směrování s adresami URL, které obsahují tečky</span><span class="sxs-lookup"><span data-stu-id="aaa50-161">Routing with URLs that contain dots</span></span>

<span data-ttu-id="aaa50-162">V Blazorch aplikacích na straně serveru je `/` výchozí trasa v *_Host. cshtml* (`@page "/"`).</span><span class="sxs-lookup"><span data-stu-id="aaa50-162">In Blazor server-side apps, the default route in *_Host.cshtml* is `/` (`@page "/"`).</span></span> <span data-ttu-id="aaa50-163">Adresa URL požadavku, která obsahuje tečku`.`(), není shodná s výchozí cestou, protože adresa URL se zobrazí pro vyžádání souboru.</span><span class="sxs-lookup"><span data-stu-id="aaa50-163">A request URL that contains a dot (`.`) isn't matched by the default route because the URL appears to request a file.</span></span> <span data-ttu-id="aaa50-164">Aplikace Blazor vrací odpověď na *404, která nebyla nalezena* pro statický soubor, který neexistuje.</span><span class="sxs-lookup"><span data-stu-id="aaa50-164">A Blazor app returns a *404 - Not Found* response for a static file that doesn't exist.</span></span> <span data-ttu-id="aaa50-165">Chcete-li použít trasy, které obsahují tečku, nakonfigurujte *_Host. cshtml* s následující šablonou směrování:</span><span class="sxs-lookup"><span data-stu-id="aaa50-165">To use routes that contain a dot, configure *_Host.cshtml* with the following route template:</span></span>

```cshtml
@page "/{**path}"
```

<span data-ttu-id="aaa50-166">`"/{**path}"` Šablona obsahuje:</span><span class="sxs-lookup"><span data-stu-id="aaa50-166">The `"/{**path}"` template includes:</span></span>

* <span data-ttu-id="aaa50-167">Dvojitá hvězdička *– veškerá* syntaxe (`**`) pro zachycení cesty mezi více hranicemi složek bez kódování lomítka (`/`).</span><span class="sxs-lookup"><span data-stu-id="aaa50-167">Double-asterisk *catch-all* syntax (`**`) to capture the path across multiple folder boundaries without encoding forward slashes (`/`).</span></span>
* <span data-ttu-id="aaa50-168">Název `path` parametru trasy.</span><span class="sxs-lookup"><span data-stu-id="aaa50-168">A `path` route parameter name.</span></span>

<span data-ttu-id="aaa50-169">Další informace naleznete v tématu <xref:fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="aaa50-169">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="navlink-component"></a><span data-ttu-id="aaa50-170">Komponenta NavLink</span><span class="sxs-lookup"><span data-stu-id="aaa50-170">NavLink component</span></span>

<span data-ttu-id="aaa50-171">Použít komponentu namísto prvků hypertextového odkazu HTML (`<a>`) při vytváření navigačních odkazů. `NavLink`</span><span class="sxs-lookup"><span data-stu-id="aaa50-171">Use a `NavLink` component in place of HTML hyperlink elements (`<a>`) when creating navigation links.</span></span> <span data-ttu-id="aaa50-172">Komponenta se chová `<a>` jako element `active` s tím rozdílem, že přepíná třídu CSS na základě toho, `href` zda odpovídá aktuální adrese URL. `NavLink`</span><span class="sxs-lookup"><span data-stu-id="aaa50-172">A `NavLink` component behaves like an `<a>` element, except it toggles an `active` CSS class based on whether its `href` matches the current URL.</span></span> <span data-ttu-id="aaa50-173">`active` Třída pomáhá uživateli pochopit, která stránka je aktivní stránkou mezi zobrazenými navigačními odkazy.</span><span class="sxs-lookup"><span data-stu-id="aaa50-173">The `active` class helps a user understand which page is the active page among the navigation links displayed.</span></span>

<span data-ttu-id="aaa50-174">Následující `NavMenu` komponenta vytvoří navigační panel [bootstrap](https://getbootstrap.com/docs/) , který ukazuje, jak používat `NavLink` komponenty:</span><span class="sxs-lookup"><span data-stu-id="aaa50-174">The following `NavMenu` component creates a [Bootstrap](https://getbootstrap.com/docs/) navigation bar that demonstrates how to use `NavLink` components:</span></span>

[!code-cshtml[](routing/samples_snapshot/3.x/NavMenu.razor?highlight=4,9)]

<span data-ttu-id="aaa50-175">Existují dvě `NavLinkMatch` možnosti, které lze přiřadit `Match` k atributu `<NavLink>` elementu:</span><span class="sxs-lookup"><span data-stu-id="aaa50-175">There are two `NavLinkMatch` options that you can assign to the `Match` attribute of the `<NavLink>` element:</span></span>

* <span data-ttu-id="aaa50-176">`NavLinkMatch.All`&ndash; Jeaktivní,pokudodpovídá`NavLink` celé aktuální adrese URL.</span><span class="sxs-lookup"><span data-stu-id="aaa50-176">`NavLinkMatch.All` &ndash; The `NavLink` is active when it matches the entire current URL.</span></span>
* <span data-ttu-id="aaa50-177">`NavLinkMatch.Prefix`(*výchozí*) &ndash; Jeaktivní,pokudodpovídálibovolnépředponě`NavLink` aktuální adresy URL.</span><span class="sxs-lookup"><span data-stu-id="aaa50-177">`NavLinkMatch.Prefix` (*default*) &ndash; The `NavLink` is active when it matches any prefix of the current URL.</span></span>

<span data-ttu-id="aaa50-178">V předchozím příkladu se Domovská stránka `NavLink` `href=""` shoduje s `active` adresou URL domů a přijímá pouze třídu CSS ve `https://localhost:5001/`výchozí základní cestě URL aplikace (například).</span><span class="sxs-lookup"><span data-stu-id="aaa50-178">In the preceding example, the Home `NavLink` `href=""` matches the home URL and only receives the `active` CSS class at the app's default base path URL (for example, `https://localhost:5001/`).</span></span> <span data-ttu-id="aaa50-179">Druhá `NavLink` Získá třídu, `active` když uživatel `MyComponent` navštíví libovolnou adresu URL s předponou (například `https://localhost:5001/MyComponent` a `https://localhost:5001/MyComponent/AnotherSegment`).</span><span class="sxs-lookup"><span data-stu-id="aaa50-179">The second `NavLink` receives the `active` class when the user visits any URL with a `MyComponent` prefix (for example, `https://localhost:5001/MyComponent` and `https://localhost:5001/MyComponent/AnotherSegment`).</span></span>

<span data-ttu-id="aaa50-180">Další `NavLink` atributy komponenty jsou předány do vykreslené značky ukotvení.</span><span class="sxs-lookup"><span data-stu-id="aaa50-180">Additional `NavLink` component attributes are passed through to the rendered anchor tag.</span></span> <span data-ttu-id="aaa50-181">V následujícím příkladu `NavLink` komponenta `target` zahrnuje atribut:</span><span class="sxs-lookup"><span data-stu-id="aaa50-181">In the following example, the `NavLink` component includes the `target` attribute:</span></span>

```cshtml
<NavLink href="my-page" target="_blank">My page</NavLink>
```

<span data-ttu-id="aaa50-182">Vykresluje se následující kód HTML:</span><span class="sxs-lookup"><span data-stu-id="aaa50-182">The following HTML markup is rendered:</span></span>

```html
<a href="my-page" target="_blank" rel="noopener noreferrer">My page</a>
```

## <a name="uri-and-navigation-state-helpers"></a><span data-ttu-id="aaa50-183">Identifikátory URI a pomocníka pro stav navigace</span><span class="sxs-lookup"><span data-stu-id="aaa50-183">URI and navigation state helpers</span></span>

<span data-ttu-id="aaa50-184">Použijte `Microsoft.AspNetCore.Components.IUriHelper` pro práci s identifikátory URI a navigací v C# kódu.</span><span class="sxs-lookup"><span data-stu-id="aaa50-184">Use `Microsoft.AspNetCore.Components.IUriHelper` to work with URIs and navigation in C# code.</span></span> <span data-ttu-id="aaa50-185">`IUriHelper`poskytuje událost a metody uvedené v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="aaa50-185">`IUriHelper` provides the event and methods shown in the following table.</span></span>

| <span data-ttu-id="aaa50-186">Člen</span><span class="sxs-lookup"><span data-stu-id="aaa50-186">Member</span></span> | <span data-ttu-id="aaa50-187">Popis</span><span class="sxs-lookup"><span data-stu-id="aaa50-187">Description</span></span> |
| ------ | ----------- |
| `GetAbsoluteUri` | <span data-ttu-id="aaa50-188">Získá aktuální absolutní identifikátor URI.</span><span class="sxs-lookup"><span data-stu-id="aaa50-188">Gets the current absolute URI.</span></span> |
| `GetBaseUri` | <span data-ttu-id="aaa50-189">Získá základní identifikátor URI (s koncovým lomítkem), který může být součástí relativních cest URI pro vytvoření absolutního identifikátoru URI.</span><span class="sxs-lookup"><span data-stu-id="aaa50-189">Gets the base URI (with a trailing slash) that can be prepended to relative URI paths to produce an absolute URI.</span></span> <span data-ttu-id="aaa50-190">`GetBaseUri` Obvykle odpovídá `href` *atributu v* prvku dokumentu v wwwroot/index.html (Blazor na straně klienta) nebo na *stránkách/_Host. cshtml* (Blazor na straně serveru). `<base>`</span><span class="sxs-lookup"><span data-stu-id="aaa50-190">Typically, `GetBaseUri` corresponds to the `href` attribute on the document's `<base>` element in *wwwroot/index.html* (Blazor client-side) or *Pages/_Host.cshtml* (Blazor server-side).</span></span> |
| `NavigateTo` | <span data-ttu-id="aaa50-191">Přejde k zadanému identifikátoru URI.</span><span class="sxs-lookup"><span data-stu-id="aaa50-191">Navigates to the specified URI.</span></span> <span data-ttu-id="aaa50-192">Pokud `forceLoad` je `true`:</span><span class="sxs-lookup"><span data-stu-id="aaa50-192">If `forceLoad` is `true`:</span></span><ul><li><span data-ttu-id="aaa50-193">Směrování na straně klienta se nepoužívá.</span><span class="sxs-lookup"><span data-stu-id="aaa50-193">Client-side routing is bypassed.</span></span></li><li><span data-ttu-id="aaa50-194">Prohlížeč je nucen načíst novou stránku ze serveru, bez ohledu na to, zda je identifikátor URI obvykle zpracováván směrovačem na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="aaa50-194">The browser is forced to load the new page from the server, whether or not the URI is normally handled by the client-side router.</span></span></li></ul> |
| `OnLocationChanged` | <span data-ttu-id="aaa50-195">Událost, která se aktivuje, když se změní navigační umístění</span><span class="sxs-lookup"><span data-stu-id="aaa50-195">An event that fires when the navigation location has changed.</span></span> |
| `ToAbsoluteUri` | <span data-ttu-id="aaa50-196">Převede relativní identifikátor URI na absolutní identifikátor URI.</span><span class="sxs-lookup"><span data-stu-id="aaa50-196">Converts a relative URI into an absolute URI.</span></span> |
| `ToBaseRelativePath` | <span data-ttu-id="aaa50-197">Vzhledem k základnímu identifikátoru URI (například identifikátor URI, který `GetBaseUri`dřív vrátil), převede absolutní identifikátor URI na URI relativně k základní předponě identifikátoru URI.</span><span class="sxs-lookup"><span data-stu-id="aaa50-197">Given a base URI (for example, a URI previously returned by `GetBaseUri`), converts an absolute URI into a URI relative to the base URI prefix.</span></span> |

<span data-ttu-id="aaa50-198">Pokud je vybráno tlačítko, následující komponenta přejde `Counter` na součást aplikace:</span><span class="sxs-lookup"><span data-stu-id="aaa50-198">The following component navigates to the app's `Counter` component when the button is selected:</span></span>

```cshtml
@page "/navigate"
@using Microsoft.AspNetCore.Components
@inject IUriHelper UriHelper

<h1>Navigate in Code Example</h1>

<button class="btn btn-primary" @onclick="NavigateToCounterComponent">
    Navigate to the Counter component
</button>

@code {
    private void NavigateToCounterComponent()
    {
        UriHelper.NavigateTo("counter");
    }
}
```

