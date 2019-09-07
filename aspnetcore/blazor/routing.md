---
title: ASP.NET Core směrování Blazor
author: guardrex
description: Naučte se směrovat požadavky v aplikacích a o komponentě NavLink.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/06/2019
uid: blazor/routing
ms.openlocfilehash: d348908261c51b477aa698a407266d05c0df5a33
ms.sourcegitcommit: 43c6335b5859282f64d66a7696c5935a2bcdf966
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/07/2019
ms.locfileid: "70800341"
---
# <a name="aspnet-core-blazor-routing"></a><span data-ttu-id="e9cce-103">ASP.NET Core směrování Blazor</span><span class="sxs-lookup"><span data-stu-id="e9cce-103">ASP.NET Core Blazor routing</span></span>

<span data-ttu-id="e9cce-104">Podle [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="e9cce-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="e9cce-105">Naučte se směrovat požadavky a používat `NavLink` komponentu k vytváření navigačních odkazů v aplikacích Blazor.</span><span class="sxs-lookup"><span data-stu-id="e9cce-105">Learn how to route requests and how to use the `NavLink` component to create navigation links in Blazor apps.</span></span>

## <a name="aspnet-core-endpoint-routing-integration"></a><span data-ttu-id="e9cce-106">Integrace směrování ASP.NET Core Endpoint</span><span class="sxs-lookup"><span data-stu-id="e9cce-106">ASP.NET Core endpoint routing integration</span></span>

<span data-ttu-id="e9cce-107">Blazor na straně serveru je integrovaná do [Směrování koncového bodu ASP.NET Core](xref:fundamentals/routing).</span><span class="sxs-lookup"><span data-stu-id="e9cce-107">Blazor server-side is integrated into [ASP.NET Core Endpoint Routing](xref:fundamentals/routing).</span></span> <span data-ttu-id="e9cce-108">Aplikace ASP.NET Core je nakonfigurovaná tak, aby přijímala příchozí připojení pro `MapBlazorHub` interaktivní `Startup.Configure`komponenty v:</span><span class="sxs-lookup"><span data-stu-id="e9cce-108">An ASP.NET Core app is configured to accept incoming connections for interactive components with `MapBlazorHub` in `Startup.Configure`:</span></span>

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

## <a name="route-templates"></a><span data-ttu-id="e9cce-109">Šablony směrování</span><span class="sxs-lookup"><span data-stu-id="e9cce-109">Route templates</span></span>

<span data-ttu-id="e9cce-110">Tato `Router` součást umožňuje směrování na jednotlivé komponenty se zadanou trasou.</span><span class="sxs-lookup"><span data-stu-id="e9cce-110">The `Router` component enables routing to each component with a specified route.</span></span> <span data-ttu-id="e9cce-111">Komponenta se zobrazí v souboru *App. Razor:* `Router`</span><span class="sxs-lookup"><span data-stu-id="e9cce-111">The `Router` component appears in the *App.razor* file:</span></span>

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

<span data-ttu-id="e9cce-112">Je-li soubor *. Razor* s `@page` direktivou kompilován, je k dispozici vygenerovaná <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> třída, která určuje šablonu trasy.</span><span class="sxs-lookup"><span data-stu-id="e9cce-112">When a *.razor* file with an `@page` directive is compiled, the generated class is provided a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> specifying the route template.</span></span>

<span data-ttu-id="e9cce-113">Za běhu `RouteView` komponenty:</span><span class="sxs-lookup"><span data-stu-id="e9cce-113">At runtime, the `RouteView` component:</span></span>

* <span data-ttu-id="e9cce-114">`RouteData` Přijímá`Router` od spolu s požadovanými parametry.</span><span class="sxs-lookup"><span data-stu-id="e9cce-114">Receives the `RouteData` from the `Router` along with any desired parameters.</span></span>
* <span data-ttu-id="e9cce-115">Vykreslí určenou komponentu pomocí jejího rozložení (nebo volitelného výchozího rozložení) pomocí zadaných parametrů.</span><span class="sxs-lookup"><span data-stu-id="e9cce-115">Renders the specified component with its layout (or an optional default layout) using the specified parameters.</span></span>

<span data-ttu-id="e9cce-116">Volitelně můžete zadat `DefaultLayout` parametr s třídou rozložení, který se má použít pro součásti, které neurčují rozložení.</span><span class="sxs-lookup"><span data-stu-id="e9cce-116">You can optionally specify a `DefaultLayout` parameter with a layout class to use for components that don't specify a layout.</span></span> <span data-ttu-id="e9cce-117">Výchozí šablony Blazor určují `MainLayout` komponentu.</span><span class="sxs-lookup"><span data-stu-id="e9cce-117">The default Blazor templates specify the `MainLayout` component.</span></span> <span data-ttu-id="e9cce-118">*MainLayout. Razor* se nachází ve *sdílené* složce projektu šablony.</span><span class="sxs-lookup"><span data-stu-id="e9cce-118">*MainLayout.razor* is in the template project's *Shared* folder.</span></span> <span data-ttu-id="e9cce-119">Další informace o rozložení najdete v tématu <xref:blazor/layouts>.</span><span class="sxs-lookup"><span data-stu-id="e9cce-119">For more information on layouts, see <xref:blazor/layouts>.</span></span>

<span data-ttu-id="e9cce-120">Pro komponentu lze použít více šablon směrování.</span><span class="sxs-lookup"><span data-stu-id="e9cce-120">Multiple route templates can be applied to a component.</span></span> <span data-ttu-id="e9cce-121">Následující komponenta reaguje na požadavky pro `/BlazorRoute` a: `/DifferentBlazorRoute`</span><span class="sxs-lookup"><span data-stu-id="e9cce-121">The following component responds to requests for `/BlazorRoute` and `/DifferentBlazorRoute`:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/BlazorRoute.razor?name=snippet_BlazorRoute)]

> [!IMPORTANT]
> <span data-ttu-id="e9cce-122">Aby adresy URL bylo možné správně přeložit, musí aplikace v `<base>` souboru *wwwroot/index.html* obsahovat značku (Blazor na straně klienta) nebo *stránky/_Host. cshtml* (Blazor na straně serveru) se základní cestou `href` aplikace zadanou v atributu ( `<base href="/">`).</span><span class="sxs-lookup"><span data-stu-id="e9cce-122">For URLs to resolve correctly, the app must include a `<base>` tag in its *wwwroot/index.html* file (Blazor client-side) or *Pages/_Host.cshtml* file (Blazor server-side) with the app base path specified in the `href` attribute (`<base href="/">`).</span></span> <span data-ttu-id="e9cce-123">Další informace naleznete v tématu <xref:host-and-deploy/blazor/index#app-base-path>.</span><span class="sxs-lookup"><span data-stu-id="e9cce-123">For more information, see <xref:host-and-deploy/blazor/index#app-base-path>.</span></span>

## <a name="provide-custom-content-when-content-isnt-found"></a><span data-ttu-id="e9cce-124">Poskytnutí vlastního obsahu, když se nenalezne obsah</span><span class="sxs-lookup"><span data-stu-id="e9cce-124">Provide custom content when content isn't found</span></span>

<span data-ttu-id="e9cce-125">`Router` Komponenta umožňuje aplikaci zadat vlastní obsah, pokud se pro požadovanou trasu nenajde obsah.</span><span class="sxs-lookup"><span data-stu-id="e9cce-125">The `Router` component allows the app to specify custom content if content isn't found for the requested route.</span></span>

<span data-ttu-id="e9cce-126">V souboru *App. Razor* nastavte vlastní obsah v `NotFound` parametru `Router` šablony součásti:</span><span class="sxs-lookup"><span data-stu-id="e9cce-126">In the *App.razor* file, set custom content in the `NotFound` template parameter of the `Router` component:</span></span>

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

<span data-ttu-id="e9cce-127">Obsah `<NotFound>` značek může zahrnovat libovolné položky, jako jsou například jiné interaktivní součásti.</span><span class="sxs-lookup"><span data-stu-id="e9cce-127">The content of `<NotFound>` tags can include arbitrary items, such as other interactive components.</span></span> <span data-ttu-id="e9cce-128">Chcete-li použít výchozí rozložení `NotFound` obsahu, přečtěte si téma. <xref:blazor/layouts></span><span class="sxs-lookup"><span data-stu-id="e9cce-128">To apply a default layout to `NotFound` content, see <xref:blazor/layouts>.</span></span>

## <a name="route-to-components-from-multiple-assemblies"></a><span data-ttu-id="e9cce-129">Směrování na součásti z více sestavení</span><span class="sxs-lookup"><span data-stu-id="e9cce-129">Route to components from multiple assemblies</span></span>

<span data-ttu-id="e9cce-130">Použijte parametr k určení dalších sestavení, `Router` která má součást při hledání směrovatelných komponent zvážit. `AdditionalAssemblies`</span><span class="sxs-lookup"><span data-stu-id="e9cce-130">Use the `AdditionalAssemblies` parameter to specify additional assemblies for the `Router` component to consider when searching for routable components.</span></span> <span data-ttu-id="e9cce-131">Zadaná sestavení jsou kromě `AppAssembly`zadaného sestavení považována za.</span><span class="sxs-lookup"><span data-stu-id="e9cce-131">Specified assemblies are considered in addition to the `AppAssembly`-specified assembly.</span></span> <span data-ttu-id="e9cce-132">V následujícím příkladu `Component1` je směrovatelný komponenta definovaná v odkazované knihovně tříd.</span><span class="sxs-lookup"><span data-stu-id="e9cce-132">In the following example, `Component1` is a routable component defined in a referenced class library.</span></span> <span data-ttu-id="e9cce-133">Následující `AdditionalAssemblies` příklad vede k podpoře směrování pro `Component1`:</span><span class="sxs-lookup"><span data-stu-id="e9cce-133">The following `AdditionalAssemblies` example results in routing support for `Component1`:</span></span>

<span data-ttu-id="e9cce-134">< router AppAssembly = "typeof (program). Assembly "AdditionalAssemblies =" New [] {typeof (Component1). Sestavení} >...</Router></span><span class="sxs-lookup"><span data-stu-id="e9cce-134"><Router AppAssembly="typeof(Program).Assembly" AdditionalAssemblies="new[] { typeof(Component1).Assembly }> ... </Router></span></span>

## <a name="route-parameters"></a><span data-ttu-id="e9cce-135">Parametry směrování</span><span class="sxs-lookup"><span data-stu-id="e9cce-135">Route parameters</span></span>

<span data-ttu-id="e9cce-136">Směrovač používá parametry směrování k naplnění odpovídajících parametrů komponenty se stejným názvem (bez rozlišení velkých a malých písmen):</span><span class="sxs-lookup"><span data-stu-id="e9cce-136">The router uses route parameters to populate the corresponding component parameters with the same name (case insensitive):</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/RouteParameter.razor?name=snippet_RouteParameter&highlight=2,7-8)]

<span data-ttu-id="e9cce-137">Volitelné parametry nejsou podporované pro aplikace Blazor ve verzi ASP.NET Core 3,0 Preview.</span><span class="sxs-lookup"><span data-stu-id="e9cce-137">Optional parameters aren't supported for Blazor apps in ASP.NET Core 3.0 Preview.</span></span> <span data-ttu-id="e9cce-138">V `@page` předchozím příkladu jsou aplikovány dvě direktivy.</span><span class="sxs-lookup"><span data-stu-id="e9cce-138">Two `@page` directives are applied in the previous example.</span></span> <span data-ttu-id="e9cce-139">První umožňuje navigaci na součást bez parametru.</span><span class="sxs-lookup"><span data-stu-id="e9cce-139">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="e9cce-140">Druhá `@page` direktiva `{text}` převezme parametr Route a `Text` přiřadí hodnotu vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="e9cce-140">The second `@page` directive takes the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

## <a name="route-constraints"></a><span data-ttu-id="e9cce-141">Omezení trasy</span><span class="sxs-lookup"><span data-stu-id="e9cce-141">Route constraints</span></span>

<span data-ttu-id="e9cce-142">Omezení trasy vynutilo typ shodný s typem pro segment směrování do součásti.</span><span class="sxs-lookup"><span data-stu-id="e9cce-142">A route constraint enforces type matching on a route segment to a component.</span></span>

<span data-ttu-id="e9cce-143">V následujícím příkladu trasa k `Users` součásti odpovídá pouze v případě, že:</span><span class="sxs-lookup"><span data-stu-id="e9cce-143">In the following example, the route to the `Users` component only matches if:</span></span>

* <span data-ttu-id="e9cce-144">V adrese URL požadavku je přítomen segment směrování.`Id`</span><span class="sxs-lookup"><span data-stu-id="e9cce-144">An `Id` route segment is present on the request URL.</span></span>
* <span data-ttu-id="e9cce-145">Segment je celé číslo (`int`). `Id`</span><span class="sxs-lookup"><span data-stu-id="e9cce-145">The `Id` segment is an integer (`int`).</span></span>

[!code-cshtml[](routing/samples_snapshot/3.x/Constraint.razor?highlight=1)]

<span data-ttu-id="e9cce-146">K dispozici jsou omezení tras uvedená v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="e9cce-146">The route constraints shown in the following table are available.</span></span> <span data-ttu-id="e9cce-147">Pro omezení trasy, která se shodují s invariantní jazykovou verzí, se podívejte na upozornění pod tabulkou, kde najdete další informace.</span><span class="sxs-lookup"><span data-stu-id="e9cce-147">For the route constraints that match with the invariant culture, see the warning below the table for more information.</span></span>

| <span data-ttu-id="e9cce-148">Jedinečn</span><span class="sxs-lookup"><span data-stu-id="e9cce-148">Constraint</span></span> | <span data-ttu-id="e9cce-149">Příklad</span><span class="sxs-lookup"><span data-stu-id="e9cce-149">Example</span></span>           | <span data-ttu-id="e9cce-150">Příklady shody</span><span class="sxs-lookup"><span data-stu-id="e9cce-150">Example Matches</span></span>                                                                  | <span data-ttu-id="e9cce-151">Invariantní</span><span class="sxs-lookup"><span data-stu-id="e9cce-151">Invariant</span></span><br><span data-ttu-id="e9cce-152">jazyková verze</span><span class="sxs-lookup"><span data-stu-id="e9cce-152">culture</span></span><br><span data-ttu-id="e9cce-153">shoda</span><span class="sxs-lookup"><span data-stu-id="e9cce-153">matching</span></span> |
| ---------- | ----------------- | -------------------------------------------------------------------------------- | :------------------------------: |
| `bool`     | `{active:bool}`   | <span data-ttu-id="e9cce-154">`true`, `FALSE`</span><span class="sxs-lookup"><span data-stu-id="e9cce-154">`true`, `FALSE`</span></span>                                                                  | <span data-ttu-id="e9cce-155">Ne</span><span class="sxs-lookup"><span data-stu-id="e9cce-155">No</span></span>                               |
| `datetime` | `{dob:datetime}`  | <span data-ttu-id="e9cce-156">`2016-12-31`, `2016-12-31 7:32pm`</span><span class="sxs-lookup"><span data-stu-id="e9cce-156">`2016-12-31`, `2016-12-31 7:32pm`</span></span>                                                | <span data-ttu-id="e9cce-157">Ano</span><span class="sxs-lookup"><span data-stu-id="e9cce-157">Yes</span></span>                              |
| `decimal`  | `{price:decimal}` | <span data-ttu-id="e9cce-158">`49.99`, `-1,000.01`</span><span class="sxs-lookup"><span data-stu-id="e9cce-158">`49.99`, `-1,000.01`</span></span>                                                             | <span data-ttu-id="e9cce-159">Ano</span><span class="sxs-lookup"><span data-stu-id="e9cce-159">Yes</span></span>                              |
| `double`   | `{weight:double}` | <span data-ttu-id="e9cce-160">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="e9cce-160">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="e9cce-161">Ano</span><span class="sxs-lookup"><span data-stu-id="e9cce-161">Yes</span></span>                              |
| `float`    | `{weight:float}`  | <span data-ttu-id="e9cce-162">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="e9cce-162">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="e9cce-163">Ano</span><span class="sxs-lookup"><span data-stu-id="e9cce-163">Yes</span></span>                              |
| `guid`     | `{id:guid}`       | <span data-ttu-id="e9cce-164">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span><span class="sxs-lookup"><span data-stu-id="e9cce-164">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span></span> | <span data-ttu-id="e9cce-165">Ne</span><span class="sxs-lookup"><span data-stu-id="e9cce-165">No</span></span>                               |
| `int`      | `{id:int}`        | <span data-ttu-id="e9cce-166">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="e9cce-166">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="e9cce-167">Ano</span><span class="sxs-lookup"><span data-stu-id="e9cce-167">Yes</span></span>                              |
| `long`     | `{ticks:long}`    | <span data-ttu-id="e9cce-168">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="e9cce-168">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="e9cce-169">Ano</span><span class="sxs-lookup"><span data-stu-id="e9cce-169">Yes</span></span>                              |

> [!WARNING]
> <span data-ttu-id="e9cce-170">Omezení směrování, která ověřují adresu URL a jsou převedena na typ CLR (například `int` nebo `DateTime`), vždy používají invariantní jazykovou verzi.</span><span class="sxs-lookup"><span data-stu-id="e9cce-170">Route constraints that verify the URL and are converted to a CLR type (such as `int` or `DateTime`) always use the invariant culture.</span></span> <span data-ttu-id="e9cce-171">Tato omezení předpokládají, že adresa URL nelze lokalizovat.</span><span class="sxs-lookup"><span data-stu-id="e9cce-171">These constraints assume that the URL is non-localizable.</span></span>

### <a name="routing-with-urls-that-contain-dots"></a><span data-ttu-id="e9cce-172">Směrování s adresami URL, které obsahují tečky</span><span class="sxs-lookup"><span data-stu-id="e9cce-172">Routing with URLs that contain dots</span></span>

<span data-ttu-id="e9cce-173">V Blazorch aplikacích na straně serveru je `/` výchozí trasa v *_Host. cshtml* (`@page "/"`).</span><span class="sxs-lookup"><span data-stu-id="e9cce-173">In Blazor server-side apps, the default route in *_Host.cshtml* is `/` (`@page "/"`).</span></span> <span data-ttu-id="e9cce-174">Adresa URL požadavku, která obsahuje tečku`.`(), není shodná s výchozí cestou, protože adresa URL se zobrazí pro vyžádání souboru.</span><span class="sxs-lookup"><span data-stu-id="e9cce-174">A request URL that contains a dot (`.`) isn't matched by the default route because the URL appears to request a file.</span></span> <span data-ttu-id="e9cce-175">Aplikace Blazor vrací odpověď na *404, která nebyla nalezena* pro statický soubor, který neexistuje.</span><span class="sxs-lookup"><span data-stu-id="e9cce-175">A Blazor app returns a *404 - Not Found* response for a static file that doesn't exist.</span></span> <span data-ttu-id="e9cce-176">Chcete-li použít trasy, které obsahují tečku, nakonfigurujte *_Host. cshtml* s následující šablonou směrování:</span><span class="sxs-lookup"><span data-stu-id="e9cce-176">To use routes that contain a dot, configure *_Host.cshtml* with the following route template:</span></span>

```cshtml
@page "/{**path}"
```

<span data-ttu-id="e9cce-177">`"/{**path}"` Šablona obsahuje:</span><span class="sxs-lookup"><span data-stu-id="e9cce-177">The `"/{**path}"` template includes:</span></span>

* <span data-ttu-id="e9cce-178">Dvojitá hvězdička *– veškerá* syntaxe (`**`) pro zachycení cesty mezi více hranicemi složek bez kódování lomítka (`/`).</span><span class="sxs-lookup"><span data-stu-id="e9cce-178">Double-asterisk *catch-all* syntax (`**`) to capture the path across multiple folder boundaries without encoding forward slashes (`/`).</span></span>
* <span data-ttu-id="e9cce-179">Název `path` parametru trasy.</span><span class="sxs-lookup"><span data-stu-id="e9cce-179">A `path` route parameter name.</span></span>

<span data-ttu-id="e9cce-180">Další informace naleznete v tématu <xref:fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="e9cce-180">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="navlink-component"></a><span data-ttu-id="e9cce-181">Komponenta NavLink</span><span class="sxs-lookup"><span data-stu-id="e9cce-181">NavLink component</span></span>

<span data-ttu-id="e9cce-182">Použít komponentu namísto prvků hypertextového odkazu HTML (`<a>`) při vytváření navigačních odkazů. `NavLink`</span><span class="sxs-lookup"><span data-stu-id="e9cce-182">Use a `NavLink` component in place of HTML hyperlink elements (`<a>`) when creating navigation links.</span></span> <span data-ttu-id="e9cce-183">Komponenta se chová `<a>` jako element `active` s tím rozdílem, že přepíná třídu CSS na základě toho, `href` zda odpovídá aktuální adrese URL. `NavLink`</span><span class="sxs-lookup"><span data-stu-id="e9cce-183">A `NavLink` component behaves like an `<a>` element, except it toggles an `active` CSS class based on whether its `href` matches the current URL.</span></span> <span data-ttu-id="e9cce-184">`active` Třída pomáhá uživateli pochopit, která stránka je aktivní stránkou mezi zobrazenými navigačními odkazy.</span><span class="sxs-lookup"><span data-stu-id="e9cce-184">The `active` class helps a user understand which page is the active page among the navigation links displayed.</span></span>

<span data-ttu-id="e9cce-185">Následující `NavMenu` komponenta vytvoří navigační panel [bootstrap](https://getbootstrap.com/docs/) , který ukazuje, jak používat `NavLink` komponenty:</span><span class="sxs-lookup"><span data-stu-id="e9cce-185">The following `NavMenu` component creates a [Bootstrap](https://getbootstrap.com/docs/) navigation bar that demonstrates how to use `NavLink` components:</span></span>

[!code-cshtml[](routing/samples_snapshot/3.x/NavMenu.razor?highlight=4,9)]

<span data-ttu-id="e9cce-186">Existují dvě `NavLinkMatch` možnosti, které lze přiřadit `Match` k atributu `<NavLink>` elementu:</span><span class="sxs-lookup"><span data-stu-id="e9cce-186">There are two `NavLinkMatch` options that you can assign to the `Match` attribute of the `<NavLink>` element:</span></span>

* <span data-ttu-id="e9cce-187">`NavLinkMatch.All`&ndash; Jeaktivní,pokudodpovídá`NavLink` celé aktuální adrese URL.</span><span class="sxs-lookup"><span data-stu-id="e9cce-187">`NavLinkMatch.All` &ndash; The `NavLink` is active when it matches the entire current URL.</span></span>
* <span data-ttu-id="e9cce-188">`NavLinkMatch.Prefix`(*výchozí*) &ndash; Jeaktivní,pokudodpovídálibovolnépředponě`NavLink` aktuální adresy URL.</span><span class="sxs-lookup"><span data-stu-id="e9cce-188">`NavLinkMatch.Prefix` (*default*) &ndash; The `NavLink` is active when it matches any prefix of the current URL.</span></span>

<span data-ttu-id="e9cce-189">V předchozím příkladu se Domovská stránka `NavLink` `href=""` shoduje s `active` adresou URL domů a přijímá pouze třídu CSS ve `https://localhost:5001/`výchozí základní cestě URL aplikace (například).</span><span class="sxs-lookup"><span data-stu-id="e9cce-189">In the preceding example, the Home `NavLink` `href=""` matches the home URL and only receives the `active` CSS class at the app's default base path URL (for example, `https://localhost:5001/`).</span></span> <span data-ttu-id="e9cce-190">Druhá `NavLink` Získá třídu, `active` když uživatel `MyComponent` navštíví libovolnou adresu URL s předponou (například `https://localhost:5001/MyComponent` a `https://localhost:5001/MyComponent/AnotherSegment`).</span><span class="sxs-lookup"><span data-stu-id="e9cce-190">The second `NavLink` receives the `active` class when the user visits any URL with a `MyComponent` prefix (for example, `https://localhost:5001/MyComponent` and `https://localhost:5001/MyComponent/AnotherSegment`).</span></span>

<span data-ttu-id="e9cce-191">Další `NavLink` atributy komponenty jsou předány do vykreslené značky ukotvení.</span><span class="sxs-lookup"><span data-stu-id="e9cce-191">Additional `NavLink` component attributes are passed through to the rendered anchor tag.</span></span> <span data-ttu-id="e9cce-192">V následujícím příkladu `NavLink` komponenta `target` zahrnuje atribut:</span><span class="sxs-lookup"><span data-stu-id="e9cce-192">In the following example, the `NavLink` component includes the `target` attribute:</span></span>

```cshtml
<NavLink href="my-page" target="_blank">My page</NavLink>
```

<span data-ttu-id="e9cce-193">Vykresluje se následující kód HTML:</span><span class="sxs-lookup"><span data-stu-id="e9cce-193">The following HTML markup is rendered:</span></span>

```html
<a href="my-page" target="_blank" rel="noopener noreferrer">My page</a>
```

## <a name="uri-and-navigation-state-helpers"></a><span data-ttu-id="e9cce-194">Identifikátory URI a pomocníka pro stav navigace</span><span class="sxs-lookup"><span data-stu-id="e9cce-194">URI and navigation state helpers</span></span>

<span data-ttu-id="e9cce-195">Použijte `Microsoft.AspNetCore.Components.NavigationManager` pro práci s identifikátory URI a navigací v C# kódu.</span><span class="sxs-lookup"><span data-stu-id="e9cce-195">Use `Microsoft.AspNetCore.Components.NavigationManager` to work with URIs and navigation in C# code.</span></span> <span data-ttu-id="e9cce-196">`NavigationManager`poskytuje událost a metody uvedené v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="e9cce-196">`NavigationManager` provides the event and methods shown in the following table.</span></span>

| <span data-ttu-id="e9cce-197">Člen</span><span class="sxs-lookup"><span data-stu-id="e9cce-197">Member</span></span> | <span data-ttu-id="e9cce-198">Popis</span><span class="sxs-lookup"><span data-stu-id="e9cce-198">Description</span></span> |
| ------ | ----------- |
| `Uri` | <span data-ttu-id="e9cce-199">Získá aktuální absolutní identifikátor URI.</span><span class="sxs-lookup"><span data-stu-id="e9cce-199">Gets the current absolute URI.</span></span> |
| `BaseUri` | <span data-ttu-id="e9cce-200">Získá základní identifikátor URI (s koncovým lomítkem), který může být součástí relativních cest URI pro vytvoření absolutního identifikátoru URI.</span><span class="sxs-lookup"><span data-stu-id="e9cce-200">Gets the base URI (with a trailing slash) that can be prepended to relative URI paths to produce an absolute URI.</span></span> <span data-ttu-id="e9cce-201">`BaseUri` Obvykle odpovídá `href` *atributu v* prvku dokumentu v wwwroot/index.html (Blazor na straně klienta) nebo na *stránkách/_Host. cshtml* (Blazor na straně serveru). `<base>`</span><span class="sxs-lookup"><span data-stu-id="e9cce-201">Typically, `BaseUri` corresponds to the `href` attribute on the document's `<base>` element in *wwwroot/index.html* (Blazor client-side) or *Pages/_Host.cshtml* (Blazor server-side).</span></span> |
| `NavigateTo` | <span data-ttu-id="e9cce-202">Přejde k zadanému identifikátoru URI.</span><span class="sxs-lookup"><span data-stu-id="e9cce-202">Navigates to the specified URI.</span></span> <span data-ttu-id="e9cce-203">Pokud `forceLoad` je `true`:</span><span class="sxs-lookup"><span data-stu-id="e9cce-203">If `forceLoad` is `true`:</span></span><ul><li><span data-ttu-id="e9cce-204">Směrování na straně klienta se nepoužívá.</span><span class="sxs-lookup"><span data-stu-id="e9cce-204">Client-side routing is bypassed.</span></span></li><li><span data-ttu-id="e9cce-205">Prohlížeč je nucen načíst novou stránku ze serveru, bez ohledu na to, zda je identifikátor URI obvykle zpracováván směrovačem na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="e9cce-205">The browser is forced to load the new page from the server, whether or not the URI is normally handled by the client-side router.</span></span></li></ul> |
| `LocationChanged` | <span data-ttu-id="e9cce-206">Událost, která se aktivuje, když se změní navigační umístění</span><span class="sxs-lookup"><span data-stu-id="e9cce-206">An event that fires when the navigation location has changed.</span></span> |
| `ToAbsoluteUri` | <span data-ttu-id="e9cce-207">Převede relativní identifikátor URI na absolutní identifikátor URI.</span><span class="sxs-lookup"><span data-stu-id="e9cce-207">Converts a relative URI into an absolute URI.</span></span> |
| `ToBaseRelativePath` | <span data-ttu-id="e9cce-208">Vzhledem k základnímu identifikátoru URI (například identifikátor URI, který `GetBaseUri`dřív vrátil), převede absolutní identifikátor URI na URI relativně k základní předponě identifikátoru URI.</span><span class="sxs-lookup"><span data-stu-id="e9cce-208">Given a base URI (for example, a URI previously returned by `GetBaseUri`), converts an absolute URI into a URI relative to the base URI prefix.</span></span> |

<span data-ttu-id="e9cce-209">Pokud je vybráno tlačítko, následující komponenta přejde `Counter` na součást aplikace:</span><span class="sxs-lookup"><span data-stu-id="e9cce-209">The following component navigates to the app's `Counter` component when the button is selected:</span></span>

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
