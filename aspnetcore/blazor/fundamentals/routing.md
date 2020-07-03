---
title: BlazorSměrování ASP.NET Core
author: guardrex
description: Naučte se směrovat požadavky v aplikacích a o komponentě NavLink.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/01/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/fundamentals/routing
ms.openlocfilehash: c41736e7c5a3e59a08b579de54f9810381c8df1c
ms.sourcegitcommit: 66fca14611eba141d455fe0bd2c37803062e439c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2020
ms.locfileid: "85944175"
---
# <a name="aspnet-core-blazor-routing"></a><span data-ttu-id="77df8-103">BlazorSměrování ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="77df8-103">ASP.NET Core Blazor routing</span></span>

<span data-ttu-id="77df8-104">Od [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="77df8-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="77df8-105">Naučte se směrovat požadavky a jak používat <xref:Microsoft.AspNetCore.Components.Routing.NavLink> komponentu k vytváření navigačních odkazů v Blazor aplikacích.</span><span class="sxs-lookup"><span data-stu-id="77df8-105">Learn how to route requests and how to use the <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component to create navigation links in Blazor apps.</span></span>

## <a name="aspnet-core-endpoint-routing-integration"></a><span data-ttu-id="77df8-106">Integrace směrování ASP.NET Core Endpoint</span><span class="sxs-lookup"><span data-stu-id="77df8-106">ASP.NET Core endpoint routing integration</span></span>

Blazor Server<span data-ttu-id="77df8-107">je integrován do [Směrování koncového bodu ASP.NET Core](xref:fundamentals/routing).</span><span class="sxs-lookup"><span data-stu-id="77df8-107"> is integrated into [ASP.NET Core Endpoint Routing](xref:fundamentals/routing).</span></span> <span data-ttu-id="77df8-108">Aplikace ASP.NET Core je nakonfigurovaná tak, aby přijímala příchozí připojení pro interaktivní komponenty <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> v `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="77df8-108">An ASP.NET Core app is configured to accept incoming connections for interactive components with <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> in `Startup.Configure`:</span></span>

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

<span data-ttu-id="77df8-109">Nejtypickou konfigurací je směrování všech požadavků na Razor stránku, která funguje jako hostitel pro součást aplikace na straně serveru Blazor Server .</span><span class="sxs-lookup"><span data-stu-id="77df8-109">The most typical configuration is to route all requests to a Razor page, which acts as the host for the server-side part of the Blazor Server app.</span></span> <span data-ttu-id="77df8-110">Podle konvence je stránka *hostitele* obvykle pojmenována `_Host.cshtml` .</span><span class="sxs-lookup"><span data-stu-id="77df8-110">By convention, the *host* page is usually named `_Host.cshtml`.</span></span> <span data-ttu-id="77df8-111">Trasa zadaná v hostitelském souboru se nazývá *záložní trasa* , protože v porovnání s trasou funguje s nízkou prioritou.</span><span class="sxs-lookup"><span data-stu-id="77df8-111">The route specified in the host file is called a *fallback route* because it operates with a low priority in route matching.</span></span> <span data-ttu-id="77df8-112">Záložní trasa se bere v úvahu v případě, že se jiné trasy neshodují.</span><span class="sxs-lookup"><span data-stu-id="77df8-112">The fallback route is considered when other routes don't match.</span></span> <span data-ttu-id="77df8-113">Díky tomu může aplikace používat jiné řadiče a stránky, aniž by to mělo vliv na Blazor Server aplikaci.</span><span class="sxs-lookup"><span data-stu-id="77df8-113">This allows the app to use others controllers and pages without interfering with the Blazor Server app.</span></span>

## <a name="route-templates"></a><span data-ttu-id="77df8-114">Šablony směrování</span><span class="sxs-lookup"><span data-stu-id="77df8-114">Route templates</span></span>

<span data-ttu-id="77df8-115">Tato <xref:Microsoft.AspNetCore.Components.Routing.Router> součást umožňuje směrování na jednotlivé komponenty se zadanou trasou.</span><span class="sxs-lookup"><span data-stu-id="77df8-115">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component enables routing to each component with a specified route.</span></span> <span data-ttu-id="77df8-116"><xref:Microsoft.AspNetCore.Components.Routing.Router>Komponenta se zobrazí v `App.razor` souboru:</span><span class="sxs-lookup"><span data-stu-id="77df8-116">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component appears in the `App.razor` file:</span></span>

```razor
<Router AppAssembly="typeof(Startup).Assembly">
    <Found Context="routeData">
        <RouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
    </Found>
    <NotFound>
        <p>Sorry, there's nothing at this address.</p>
    </NotFound>
</Router>
```

<span data-ttu-id="77df8-117">Když `.razor` je zkompilován soubor s `@page` direktivou, je k dispozici vygenerovaná třída <xref:Microsoft.AspNetCore.Components.RouteAttribute> určující šablonu trasy.</span><span class="sxs-lookup"><span data-stu-id="77df8-117">When a `.razor` file with an `@page` directive is compiled, the generated class is provided a <xref:Microsoft.AspNetCore.Components.RouteAttribute> specifying the route template.</span></span>

<span data-ttu-id="77df8-118">Za běhu <xref:Microsoft.AspNetCore.Components.RouteView> komponenty:</span><span class="sxs-lookup"><span data-stu-id="77df8-118">At runtime, the <xref:Microsoft.AspNetCore.Components.RouteView> component:</span></span>

* <span data-ttu-id="77df8-119">Přijímá <xref:Microsoft.AspNetCore.Components.RouteData> od <xref:Microsoft.AspNetCore.Components.Routing.Router> spolu s požadovanými parametry.</span><span class="sxs-lookup"><span data-stu-id="77df8-119">Receives the <xref:Microsoft.AspNetCore.Components.RouteData> from the <xref:Microsoft.AspNetCore.Components.Routing.Router> along with any desired parameters.</span></span>
* <span data-ttu-id="77df8-120">Vykreslí určenou komponentu pomocí jejího rozložení (nebo volitelného výchozího rozložení) pomocí zadaných parametrů.</span><span class="sxs-lookup"><span data-stu-id="77df8-120">Renders the specified component with its layout (or an optional default layout) using the specified parameters.</span></span>

<span data-ttu-id="77df8-121">Volitelně můžete zadat <xref:Microsoft.AspNetCore.Components.RouteView.DefaultLayout> parametr s třídou rozložení, který se má použít pro součásti, které neurčují rozložení.</span><span class="sxs-lookup"><span data-stu-id="77df8-121">You can optionally specify a <xref:Microsoft.AspNetCore.Components.RouteView.DefaultLayout> parameter with a layout class to use for components that don't specify a layout.</span></span> <span data-ttu-id="77df8-122">Výchozí Blazor šablony určují `MainLayout` komponentu.</span><span class="sxs-lookup"><span data-stu-id="77df8-122">The default Blazor templates specify the `MainLayout` component.</span></span> <span data-ttu-id="77df8-123">`MainLayout.razor`je ve složce projektu šablony `Shared` .</span><span class="sxs-lookup"><span data-stu-id="77df8-123">`MainLayout.razor` is in the template project's `Shared` folder.</span></span> <span data-ttu-id="77df8-124">Další informace o rozložení najdete v tématu <xref:blazor/layouts> .</span><span class="sxs-lookup"><span data-stu-id="77df8-124">For more information on layouts, see <xref:blazor/layouts>.</span></span>

<span data-ttu-id="77df8-125">Pro komponentu lze použít více šablon směrování.</span><span class="sxs-lookup"><span data-stu-id="77df8-125">Multiple route templates can be applied to a component.</span></span> <span data-ttu-id="77df8-126">Následující komponenta reaguje na požadavky pro `/BlazorRoute` a `/DifferentBlazorRoute` :</span><span class="sxs-lookup"><span data-stu-id="77df8-126">The following component responds to requests for `/BlazorRoute` and `/DifferentBlazorRoute`:</span></span>

```razor
@page "/BlazorRoute"
@page "/DifferentBlazorRoute"

<h1>Blazor routing</h1>
```

> [!IMPORTANT]
> <span data-ttu-id="77df8-127">Aby adresy URL byly správně přeloženy, musí aplikace obsahovat `<base>` značku v `wwwroot/index.html` souboru ( Blazor WebAssembly ) nebo `Pages/_Host.cshtml` souboru ( Blazor Server ) se základní cestou aplikace zadanou v `href` atributu ( `<base href="/">` ).</span><span class="sxs-lookup"><span data-stu-id="77df8-127">For URLs to resolve correctly, the app must include a `<base>` tag in its `wwwroot/index.html` file (Blazor WebAssembly) or `Pages/_Host.cshtml` file (Blazor Server) with the app base path specified in the `href` attribute (`<base href="/">`).</span></span> <span data-ttu-id="77df8-128">Další informace naleznete v tématu <xref:blazor/host-and-deploy/index#app-base-path>.</span><span class="sxs-lookup"><span data-stu-id="77df8-128">For more information, see <xref:blazor/host-and-deploy/index#app-base-path>.</span></span>

## <a name="provide-custom-content-when-content-isnt-found"></a><span data-ttu-id="77df8-129">Poskytnutí vlastního obsahu, když se nenalezne obsah</span><span class="sxs-lookup"><span data-stu-id="77df8-129">Provide custom content when content isn't found</span></span>

<span data-ttu-id="77df8-130"><xref:Microsoft.AspNetCore.Components.Routing.Router>Komponenta umožňuje aplikaci zadat vlastní obsah, pokud se pro požadovanou trasu nenajde obsah.</span><span class="sxs-lookup"><span data-stu-id="77df8-130">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component allows the app to specify custom content if content isn't found for the requested route.</span></span>

<span data-ttu-id="77df8-131">V `App.razor` souboru nastavte vlastní obsah v <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> parametru šablony <xref:Microsoft.AspNetCore.Components.Routing.Router> součásti:</span><span class="sxs-lookup"><span data-stu-id="77df8-131">In the `App.razor` file, set custom content in the <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> template parameter of the <xref:Microsoft.AspNetCore.Components.Routing.Router> component:</span></span>

```razor
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

<span data-ttu-id="77df8-132">Obsah `<NotFound>` značek může zahrnovat libovolné položky, jako jsou například jiné interaktivní součásti.</span><span class="sxs-lookup"><span data-stu-id="77df8-132">The content of `<NotFound>` tags can include arbitrary items, such as other interactive components.</span></span> <span data-ttu-id="77df8-133">Chcete-li použít výchozí rozložení <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> obsahu, přečtěte si téma <xref:blazor/layouts> .</span><span class="sxs-lookup"><span data-stu-id="77df8-133">To apply a default layout to <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> content, see <xref:blazor/layouts>.</span></span>

## <a name="route-to-components-from-multiple-assemblies"></a><span data-ttu-id="77df8-134">Směrování na součásti z více sestavení</span><span class="sxs-lookup"><span data-stu-id="77df8-134">Route to components from multiple assemblies</span></span>

<span data-ttu-id="77df8-135">Použijte <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> parametr k určení dalších sestavení, <xref:Microsoft.AspNetCore.Components.Routing.Router> která má součást při hledání směrovatelných komponent zvážit.</span><span class="sxs-lookup"><span data-stu-id="77df8-135">Use the <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> parameter to specify additional assemblies for the <xref:Microsoft.AspNetCore.Components.Routing.Router> component to consider when searching for routable components.</span></span> <span data-ttu-id="77df8-136">Zadaná sestavení jsou kromě zadaného sestavení považována za `AppAssembly` .</span><span class="sxs-lookup"><span data-stu-id="77df8-136">Specified assemblies are considered in addition to the `AppAssembly`-specified assembly.</span></span> <span data-ttu-id="77df8-137">V následujícím příkladu `Component1` je směrovatelný komponenta definovaná v odkazované knihovně tříd.</span><span class="sxs-lookup"><span data-stu-id="77df8-137">In the following example, `Component1` is a routable component defined in a referenced class library.</span></span> <span data-ttu-id="77df8-138">Následující <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> příklad vede k podpoře směrování pro `Component1` :</span><span class="sxs-lookup"><span data-stu-id="77df8-138">The following <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> example results in routing support for `Component1`:</span></span>

```razor
<Router
    AppAssembly="typeof(Program).Assembly"
    AdditionalAssemblies="new[] { typeof(Component1).Assembly }">
    ...
</Router>
```

## <a name="route-parameters"></a><span data-ttu-id="77df8-139">Parametry směrování</span><span class="sxs-lookup"><span data-stu-id="77df8-139">Route parameters</span></span>

<span data-ttu-id="77df8-140">Směrovač používá parametry směrování k naplnění odpovídajících parametrů komponenty se stejným názvem (bez rozlišení velkých a malých písmen):</span><span class="sxs-lookup"><span data-stu-id="77df8-140">The router uses route parameters to populate the corresponding component parameters with the same name (case insensitive):</span></span>

```razor
@page "/RouteParameter"
@page "/RouteParameter/{text}"

<h1>Blazor is @Text!</h1>

@code {
    [Parameter]
    public string Text { get; set; }

    protected override void OnInitialized()
    {
        Text = Text ?? "fantastic";
    }
}
```

<span data-ttu-id="77df8-141">Volitelné parametry nejsou podporovány.</span><span class="sxs-lookup"><span data-stu-id="77df8-141">Optional parameters aren't supported.</span></span> <span data-ttu-id="77df8-142">`@page`V předchozím příkladu jsou aplikovány dvě direktivy.</span><span class="sxs-lookup"><span data-stu-id="77df8-142">Two `@page` directives are applied in the previous example.</span></span> <span data-ttu-id="77df8-143">První umožňuje navigaci na součást bez parametru.</span><span class="sxs-lookup"><span data-stu-id="77df8-143">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="77df8-144">Druhá `@page` direktiva převezme `{text}` parametr Route a přiřadí hodnotu `Text` Vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="77df8-144">The second `@page` directive takes the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

## <a name="route-constraints"></a><span data-ttu-id="77df8-145">Omezení trasy</span><span class="sxs-lookup"><span data-stu-id="77df8-145">Route constraints</span></span>

<span data-ttu-id="77df8-146">Omezení trasy vynutilo typ shodný s typem pro segment směrování do součásti.</span><span class="sxs-lookup"><span data-stu-id="77df8-146">A route constraint enforces type matching on a route segment to a component.</span></span>

<span data-ttu-id="77df8-147">V následujícím příkladu trasa k `Users` součásti odpovídá pouze v případě, že:</span><span class="sxs-lookup"><span data-stu-id="77df8-147">In the following example, the route to the `Users` component only matches if:</span></span>

* <span data-ttu-id="77df8-148">`Id`V adrese URL požadavku je přítomen segment směrování.</span><span class="sxs-lookup"><span data-stu-id="77df8-148">An `Id` route segment is present on the request URL.</span></span>
* <span data-ttu-id="77df8-149">`Id`Segment je celé číslo ( `int` ).</span><span class="sxs-lookup"><span data-stu-id="77df8-149">The `Id` segment is an integer (`int`).</span></span>

[!code-razor[](routing/samples_snapshot/3.x/Constraint.razor?highlight=1)]

<span data-ttu-id="77df8-150">K dispozici jsou omezení tras uvedená v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="77df8-150">The route constraints shown in the following table are available.</span></span> <span data-ttu-id="77df8-151">Pro omezení trasy, která se shodují s invariantní jazykovou verzí, se podívejte na upozornění pod tabulkou, kde najdete další informace.</span><span class="sxs-lookup"><span data-stu-id="77df8-151">For the route constraints that match with the invariant culture, see the warning below the table for more information.</span></span>

| <span data-ttu-id="77df8-152">Jedinečn</span><span class="sxs-lookup"><span data-stu-id="77df8-152">Constraint</span></span> | <span data-ttu-id="77df8-153">Příklad</span><span class="sxs-lookup"><span data-stu-id="77df8-153">Example</span></span>           | <span data-ttu-id="77df8-154">Příklady shody</span><span class="sxs-lookup"><span data-stu-id="77df8-154">Example Matches</span></span>                                                                  | <span data-ttu-id="77df8-155">Invariantní</span><span class="sxs-lookup"><span data-stu-id="77df8-155">Invariant</span></span><br><span data-ttu-id="77df8-156">jazyková verze</span><span class="sxs-lookup"><span data-stu-id="77df8-156">culture</span></span><br><span data-ttu-id="77df8-157">shoda</span><span class="sxs-lookup"><span data-stu-id="77df8-157">matching</span></span> |
| ---------- | ----------------- | -------------------------------------------------------------------------------- | :------------------------------: |
| `bool`     | `{active:bool}`   | <span data-ttu-id="77df8-158">`true`, `FALSE`</span><span class="sxs-lookup"><span data-stu-id="77df8-158">`true`, `FALSE`</span></span>                                                                  | <span data-ttu-id="77df8-159">No</span><span class="sxs-lookup"><span data-stu-id="77df8-159">No</span></span>                               |
| `datetime` | `{dob:datetime}`  | <span data-ttu-id="77df8-160">`2016-12-31`, `2016-12-31 7:32pm`</span><span class="sxs-lookup"><span data-stu-id="77df8-160">`2016-12-31`, `2016-12-31 7:32pm`</span></span>                                                | <span data-ttu-id="77df8-161">Yes</span><span class="sxs-lookup"><span data-stu-id="77df8-161">Yes</span></span>                              |
| `decimal`  | `{price:decimal}` | <span data-ttu-id="77df8-162">`49.99`, `-1,000.01`</span><span class="sxs-lookup"><span data-stu-id="77df8-162">`49.99`, `-1,000.01`</span></span>                                                             | <span data-ttu-id="77df8-163">Yes</span><span class="sxs-lookup"><span data-stu-id="77df8-163">Yes</span></span>                              |
| `double`   | `{weight:double}` | <span data-ttu-id="77df8-164">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="77df8-164">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="77df8-165">Yes</span><span class="sxs-lookup"><span data-stu-id="77df8-165">Yes</span></span>                              |
| `float`    | `{weight:float}`  | <span data-ttu-id="77df8-166">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="77df8-166">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="77df8-167">Yes</span><span class="sxs-lookup"><span data-stu-id="77df8-167">Yes</span></span>                              |
| `guid`     | `{id:guid}`       | <span data-ttu-id="77df8-168">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span><span class="sxs-lookup"><span data-stu-id="77df8-168">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span></span> | <span data-ttu-id="77df8-169">No</span><span class="sxs-lookup"><span data-stu-id="77df8-169">No</span></span>                               |
| `int`      | `{id:int}`        | <span data-ttu-id="77df8-170">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="77df8-170">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="77df8-171">Yes</span><span class="sxs-lookup"><span data-stu-id="77df8-171">Yes</span></span>                              |
| `long`     | `{ticks:long}`    | <span data-ttu-id="77df8-172">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="77df8-172">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="77df8-173">Yes</span><span class="sxs-lookup"><span data-stu-id="77df8-173">Yes</span></span>                              |

> [!WARNING]
> <span data-ttu-id="77df8-174">Omezení směrování, která ověřují adresu URL a jsou převedena na typ CLR (například `int` nebo <xref:System.DateTime> ), vždy používají invariantní jazykovou verzi.</span><span class="sxs-lookup"><span data-stu-id="77df8-174">Route constraints that verify the URL and are converted to a CLR type (such as `int` or <xref:System.DateTime>) always use the invariant culture.</span></span> <span data-ttu-id="77df8-175">Tato omezení předpokládají, že adresa URL nelze lokalizovat.</span><span class="sxs-lookup"><span data-stu-id="77df8-175">These constraints assume that the URL is non-localizable.</span></span>

### <a name="routing-with-urls-that-contain-dots"></a><span data-ttu-id="77df8-176">Směrování s adresami URL, které obsahují tečky</span><span class="sxs-lookup"><span data-stu-id="77df8-176">Routing with URLs that contain dots</span></span>

<span data-ttu-id="77df8-177">V Blazor Server části aplikace je výchozí trasa v `_Host.cshtml` `/` : ( `@page "/"` ).</span><span class="sxs-lookup"><span data-stu-id="77df8-177">In Blazor Server apps, the default route in `_Host.cshtml` is `/` (`@page "/"`).</span></span> <span data-ttu-id="77df8-178">Adresa URL požadavku, která obsahuje tečku ( `.` ), není shodná s výchozí cestou, protože adresa URL se zobrazí pro vyžádání souboru.</span><span class="sxs-lookup"><span data-stu-id="77df8-178">A request URL that contains a dot (`.`) isn't matched by the default route because the URL appears to request a file.</span></span> <span data-ttu-id="77df8-179">BlazorAplikace vrátí odpověď na *404, která nebyla nalezena* pro statický soubor, který neexistuje.</span><span class="sxs-lookup"><span data-stu-id="77df8-179">A Blazor app returns a *404 - Not Found* response for a static file that doesn't exist.</span></span> <span data-ttu-id="77df8-180">Pokud chcete použít trasy, které obsahují tečku, nakonfigurujte `_Host.cshtml` ji pomocí následující šablony trasy:</span><span class="sxs-lookup"><span data-stu-id="77df8-180">To use routes that contain a dot, configure `_Host.cshtml` with the following route template:</span></span>

```cshtml
@page "/{**path}"
```

<span data-ttu-id="77df8-181">`"/{**path}"`Šablona obsahuje:</span><span class="sxs-lookup"><span data-stu-id="77df8-181">The `"/{**path}"` template includes:</span></span>

* <span data-ttu-id="77df8-182">Dvojitá hvězdička *– veškerá* syntaxe ( `**` ) pro zachycení cesty mezi více hranicemi složek bez kódování lomítka ( `/` ).</span><span class="sxs-lookup"><span data-stu-id="77df8-182">Double-asterisk *catch-all* syntax (`**`) to capture the path across multiple folder boundaries without encoding forward slashes (`/`).</span></span>
* <span data-ttu-id="77df8-183">`path`název parametru trasy.</span><span class="sxs-lookup"><span data-stu-id="77df8-183">`path` route parameter name.</span></span>

> [!NOTE]
> <span data-ttu-id="77df8-184">*Catch-All* parametr Syntax ( `*` / `**` ) není **not** v Razor součástech ( `.razor` ) podporován.</span><span class="sxs-lookup"><span data-stu-id="77df8-184">*Catch-all* parameter syntax (`*`/`**`) is **not** supported in Razor components (`.razor`).</span></span>

<span data-ttu-id="77df8-185">Další informace naleznete v tématu <xref:fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="77df8-185">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="navlink-component"></a><span data-ttu-id="77df8-186">Komponenta NavLink</span><span class="sxs-lookup"><span data-stu-id="77df8-186">NavLink component</span></span>

<span data-ttu-id="77df8-187">Použít <xref:Microsoft.AspNetCore.Components.Routing.NavLink> komponentu namísto prvků hypertextového odkazu HTML ( `<a>` ) při vytváření navigačních odkazů.</span><span class="sxs-lookup"><span data-stu-id="77df8-187">Use a <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component in place of HTML hyperlink elements (`<a>`) when creating navigation links.</span></span> <span data-ttu-id="77df8-188"><xref:Microsoft.AspNetCore.Components.Routing.NavLink>Komponenta se chová jako `<a>` element s tím rozdílem, že přepíná `active` třídu CSS na základě toho, zda `href` odpovídá aktuální adrese URL.</span><span class="sxs-lookup"><span data-stu-id="77df8-188">A <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component behaves like an `<a>` element, except it toggles an `active` CSS class based on whether its `href` matches the current URL.</span></span> <span data-ttu-id="77df8-189">`active`Třída pomáhá uživateli pochopit, která stránka je aktivní stránkou mezi zobrazenými navigačními odkazy.</span><span class="sxs-lookup"><span data-stu-id="77df8-189">The `active` class helps a user understand which page is the active page among the navigation links displayed.</span></span> <span data-ttu-id="77df8-190">Volitelně můžete přiřadit název třídy šablony stylů CSS k <xref:Microsoft.AspNetCore.Components.Routing.NavLink.ActiveClass?displayProperty=nameWithType> použití vlastní třídy CSS pro vykreslený odkaz, když aktuální trasa odpovídá `href` .</span><span class="sxs-lookup"><span data-stu-id="77df8-190">Optionally, assign a CSS class name to <xref:Microsoft.AspNetCore.Components.Routing.NavLink.ActiveClass?displayProperty=nameWithType> to apply a custom CSS class to the rendered link when the current route matches the `href`.</span></span>

<span data-ttu-id="77df8-191">Následující `NavMenu` Komponenta vytvoří [`Bootstrap`](https://getbootstrap.com/docs/) navigační panel, který ukazuje, jak používat <xref:Microsoft.AspNetCore.Components.Routing.NavLink> komponenty:</span><span class="sxs-lookup"><span data-stu-id="77df8-191">The following `NavMenu` component creates a [`Bootstrap`](https://getbootstrap.com/docs/) navigation bar that demonstrates how to use <xref:Microsoft.AspNetCore.Components.Routing.NavLink> components:</span></span>

[!code-razor[](routing/samples_snapshot/3.x/NavMenu.razor?highlight=4,9)]

<span data-ttu-id="77df8-192">Existují dvě <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch> Možnosti, které lze přiřadit k `Match` atributu `<NavLink>` elementu:</span><span class="sxs-lookup"><span data-stu-id="77df8-192">There are two <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch> options that you can assign to the `Match` attribute of the `<NavLink>` element:</span></span>

* <span data-ttu-id="77df8-193"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.All?displayProperty=nameWithType>: <xref:Microsoft.AspNetCore.Components.Routing.NavLink> Je aktivní, pokud odpovídá celé aktuální adrese URL.</span><span class="sxs-lookup"><span data-stu-id="77df8-193"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.All?displayProperty=nameWithType>: The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> is active when it matches the entire current URL.</span></span>
* <span data-ttu-id="77df8-194"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.Prefix?displayProperty=nameWithType>(*výchozí*): <xref:Microsoft.AspNetCore.Components.Routing.NavLink> je aktivní, pokud odpovídá libovolné PŘEDPONĚ aktuální adresy URL.</span><span class="sxs-lookup"><span data-stu-id="77df8-194"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.Prefix?displayProperty=nameWithType> (*default*): The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> is active when it matches any prefix of the current URL.</span></span>

<span data-ttu-id="77df8-195">V předchozím příkladu se Domovská stránka <xref:Microsoft.AspNetCore.Components.Routing.NavLink> `href=""` shoduje s adresou URL domů a přijímá pouze `active` třídu CSS ve výchozí základní cestě URL aplikace (například `https://localhost:5001/` ).</span><span class="sxs-lookup"><span data-stu-id="77df8-195">In the preceding example, the Home <xref:Microsoft.AspNetCore.Components.Routing.NavLink> `href=""` matches the home URL and only receives the `active` CSS class at the app's default base path URL (for example, `https://localhost:5001/`).</span></span> <span data-ttu-id="77df8-196">Druhá <xref:Microsoft.AspNetCore.Components.Routing.NavLink> Získá třídu, `active` když uživatel navštíví libovolnou adresu URL s `MyComponent` předponou (například `https://localhost:5001/MyComponent` a `https://localhost:5001/MyComponent/AnotherSegment` ).</span><span class="sxs-lookup"><span data-stu-id="77df8-196">The second <xref:Microsoft.AspNetCore.Components.Routing.NavLink> receives the `active` class when the user visits any URL with a `MyComponent` prefix (for example, `https://localhost:5001/MyComponent` and `https://localhost:5001/MyComponent/AnotherSegment`).</span></span>

<span data-ttu-id="77df8-197">Další <xref:Microsoft.AspNetCore.Components.Routing.NavLink> atributy komponenty jsou předány do vykreslené značky ukotvení.</span><span class="sxs-lookup"><span data-stu-id="77df8-197">Additional <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component attributes are passed through to the rendered anchor tag.</span></span> <span data-ttu-id="77df8-198">V následujícím příkladu <xref:Microsoft.AspNetCore.Components.Routing.NavLink> Komponenta zahrnuje `target` atribut:</span><span class="sxs-lookup"><span data-stu-id="77df8-198">In the following example, the <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component includes the `target` attribute:</span></span>

```razor
<NavLink href="my-page" target="_blank">My page</NavLink>
```

<span data-ttu-id="77df8-199">Vykresluje se následující kód HTML:</span><span class="sxs-lookup"><span data-stu-id="77df8-199">The following HTML markup is rendered:</span></span>

```html
<a href="my-page" target="_blank">My page</a>
```

## <a name="uri-and-navigation-state-helpers"></a><span data-ttu-id="77df8-200">Identifikátory URI a pomocníka pro stav navigace</span><span class="sxs-lookup"><span data-stu-id="77df8-200">URI and navigation state helpers</span></span>

<span data-ttu-id="77df8-201">Použijte <xref:Microsoft.AspNetCore.Components.NavigationManager> pro práci s identifikátory URI a navigací v kódu jazyka C#.</span><span class="sxs-lookup"><span data-stu-id="77df8-201">Use <xref:Microsoft.AspNetCore.Components.NavigationManager> to work with URIs and navigation in C# code.</span></span> <span data-ttu-id="77df8-202"><xref:Microsoft.AspNetCore.Components.NavigationManager>poskytuje událost a metody uvedené v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="77df8-202"><xref:Microsoft.AspNetCore.Components.NavigationManager> provides the event and methods shown in the following table.</span></span>

| <span data-ttu-id="77df8-203">Člen</span><span class="sxs-lookup"><span data-stu-id="77df8-203">Member</span></span> | <span data-ttu-id="77df8-204">Description</span><span class="sxs-lookup"><span data-stu-id="77df8-204">Description</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri> | <span data-ttu-id="77df8-205">Získá aktuální absolutní identifikátor URI.</span><span class="sxs-lookup"><span data-stu-id="77df8-205">Gets the current absolute URI.</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> | <span data-ttu-id="77df8-206">Získá základní identifikátor URI (s koncovým lomítkem), který může být součástí relativních cest URI pro vytvoření absolutního identifikátoru URI.</span><span class="sxs-lookup"><span data-stu-id="77df8-206">Gets the base URI (with a trailing slash) that can be prepended to relative URI paths to produce an absolute URI.</span></span> <span data-ttu-id="77df8-207">Obvykle <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> odpovídá `href` atributu v `<base>` prvku dokumentu v `wwwroot/index.html` ( Blazor WebAssembly ) nebo `Pages/_Host.cshtml` ( Blazor Server ).</span><span class="sxs-lookup"><span data-stu-id="77df8-207">Typically, <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> corresponds to the `href` attribute on the document's `<base>` element in `wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server).</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A> | <span data-ttu-id="77df8-208">Přejde k zadanému identifikátoru URI.</span><span class="sxs-lookup"><span data-stu-id="77df8-208">Navigates to the specified URI.</span></span> <span data-ttu-id="77df8-209">Pokud `forceLoad` je `true` :</span><span class="sxs-lookup"><span data-stu-id="77df8-209">If `forceLoad` is `true`:</span></span><ul><li><span data-ttu-id="77df8-210">Směrování na straně klienta se nepoužívá.</span><span class="sxs-lookup"><span data-stu-id="77df8-210">Client-side routing is bypassed.</span></span></li><li><span data-ttu-id="77df8-211">Prohlížeč je nucen načíst novou stránku ze serveru, bez ohledu na to, zda je identifikátor URI obvykle zpracováván směrovačem na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="77df8-211">The browser is forced to load the new page from the server, whether or not the URI is normally handled by the client-side router.</span></span></li></ul> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged> | <span data-ttu-id="77df8-212">Událost, která se aktivuje, když se změní navigační umístění</span><span class="sxs-lookup"><span data-stu-id="77df8-212">An event that fires when the navigation location has changed.</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.ToAbsoluteUri%2A> | <span data-ttu-id="77df8-213">Převede relativní identifikátor URI na absolutní identifikátor URI.</span><span class="sxs-lookup"><span data-stu-id="77df8-213">Converts a relative URI into an absolute URI.</span></span> |
| <span style="word-break:normal;word-wrap:normal"><xref:Microsoft.AspNetCore.Components.NavigationManager.ToBaseRelativePath%2A></span> | <span data-ttu-id="77df8-214">Vzhledem k základnímu identifikátoru URI (například identifikátor URI, který dřív vrátil <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> ), převede absolutní identifikátor URI na URI relativně k základní předponě identifikátoru URI.</span><span class="sxs-lookup"><span data-stu-id="77df8-214">Given a base URI (for example, a URI previously returned by <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri>), converts an absolute URI into a URI relative to the base URI prefix.</span></span> |

<span data-ttu-id="77df8-215">`Counter`Pokud je vybráno tlačítko, následující komponenta přejde na součást aplikace:</span><span class="sxs-lookup"><span data-stu-id="77df8-215">The following component navigates to the app's `Counter` component when the button is selected:</span></span>

```razor
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

<span data-ttu-id="77df8-216">Následující komponenta zpracovává událost změny umístění při přihlášení k odběru <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="77df8-216">The following component handles a location changed event by subscribing to <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged?displayProperty=nameWithType>.</span></span> <span data-ttu-id="77df8-217">`HandleLocationChanged`Metoda je nepřipojená, pokud `Dispose` je volána rozhraním.</span><span class="sxs-lookup"><span data-stu-id="77df8-217">The `HandleLocationChanged` method is unhooked when `Dispose` is called by the framework.</span></span> <span data-ttu-id="77df8-218">Odpojování metody umožňuje uvolňování paměti komponenty.</span><span class="sxs-lookup"><span data-stu-id="77df8-218">Unhooking the method permits garbage collection of the component.</span></span>

```razor
@implements IDisposable
@inject NavigationManager NavigationManager

...

protected override void OnInitialized()
{
    NavigationManager.LocationChanged += HandleLocationChanged;
}

private void HandleLocationChanged(object sender, LocationChangedEventArgs e)
{
    ...
}

public void Dispose()
{
    NavigationManager.LocationChanged -= HandleLocationChanged;
}
```

<span data-ttu-id="77df8-219"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs>poskytuje následující informace o události:</span><span class="sxs-lookup"><span data-stu-id="77df8-219"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs> provides the following information about the event:</span></span>

* <span data-ttu-id="77df8-220"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>: Adresa URL nového umístění.</span><span class="sxs-lookup"><span data-stu-id="77df8-220"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>: The URL of the new location.</span></span>
* <span data-ttu-id="77df8-221"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>: Pokud `true` Blazor byla zachycena navigace z prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="77df8-221"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>: If `true`, Blazor intercepted the navigation from the browser.</span></span> <span data-ttu-id="77df8-222">`false` <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> V důsledku toho došlo k navigaci.</span><span class="sxs-lookup"><span data-stu-id="77df8-222">If `false`, <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> caused the navigation to occur.</span></span>

<span data-ttu-id="77df8-223">Další informace o vyřazení součástí najdete v tématu <xref:blazor/components/lifecycle#component-disposal-with-idisposable> .</span><span class="sxs-lookup"><span data-stu-id="77df8-223">For more information on component disposal, see <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span></span>
