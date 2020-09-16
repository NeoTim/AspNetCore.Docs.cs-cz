---
title: BlazorSměrování ASP.NET Core
author: guardrex
description: Naučte se směrovat požadavky v aplikacích a o komponentě NavLink.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/02/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/fundamentals/routing
ms.openlocfilehash: 09e7ca9c03103de116c566352496174e97fbc3ce
ms.sourcegitcommit: a07f83b00db11f32313045b3492e5d1ff83c4437
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2020
ms.locfileid: "90593005"
---
# <a name="aspnet-core-no-locblazor-routing"></a><span data-ttu-id="d96c8-103">BlazorSměrování ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d96c8-103">ASP.NET Core Blazor routing</span></span>

<span data-ttu-id="d96c8-104">Od [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="d96c8-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="d96c8-105">Naučte se směrovat požadavky a jak používat <xref:Microsoft.AspNetCore.Components.Routing.NavLink> komponentu k vytváření navigačních odkazů v Blazor aplikacích.</span><span class="sxs-lookup"><span data-stu-id="d96c8-105">Learn how to route requests and how to use the <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component to create navigation links in Blazor apps.</span></span>

## <a name="aspnet-core-endpoint-routing-integration"></a><span data-ttu-id="d96c8-106">Integrace směrování ASP.NET Core Endpoint</span><span class="sxs-lookup"><span data-stu-id="d96c8-106">ASP.NET Core endpoint routing integration</span></span>

<span data-ttu-id="d96c8-107">Blazor Server je integrován do [Směrování koncového bodu ASP.NET Core](xref:fundamentals/routing).</span><span class="sxs-lookup"><span data-stu-id="d96c8-107">Blazor Server is integrated into [ASP.NET Core Endpoint Routing](xref:fundamentals/routing).</span></span> <span data-ttu-id="d96c8-108">Aplikace ASP.NET Core je nakonfigurovaná tak, aby přijímala příchozí připojení pro interaktivní komponenty <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> v `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="d96c8-108">An ASP.NET Core app is configured to accept incoming connections for interactive components with <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> in `Startup.Configure`:</span></span>

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

<span data-ttu-id="d96c8-109">Nejtypickou konfigurací je směrování všech požadavků na Razor stránku, která funguje jako hostitel pro součást aplikace na straně serveru Blazor Server .</span><span class="sxs-lookup"><span data-stu-id="d96c8-109">The most typical configuration is to route all requests to a Razor page, which acts as the host for the server-side part of the Blazor Server app.</span></span> <span data-ttu-id="d96c8-110">Podle konvence je stránka *hostitele* obvykle pojmenována `_Host.cshtml` .</span><span class="sxs-lookup"><span data-stu-id="d96c8-110">By convention, the *host* page is usually named `_Host.cshtml`.</span></span> <span data-ttu-id="d96c8-111">Trasa zadaná v hostitelském souboru se nazývá *záložní trasa* , protože v porovnání s trasou funguje s nízkou prioritou.</span><span class="sxs-lookup"><span data-stu-id="d96c8-111">The route specified in the host file is called a *fallback route* because it operates with a low priority in route matching.</span></span> <span data-ttu-id="d96c8-112">Záložní trasa se bere v úvahu v případě, že se jiné trasy neshodují.</span><span class="sxs-lookup"><span data-stu-id="d96c8-112">The fallback route is considered when other routes don't match.</span></span> <span data-ttu-id="d96c8-113">Díky tomu může aplikace používat jiné řadiče a stránky, aniž by to mělo vliv na Blazor Server aplikaci.</span><span class="sxs-lookup"><span data-stu-id="d96c8-113">This allows the app to use others controllers and pages without interfering with the Blazor Server app.</span></span>

<span data-ttu-id="d96c8-114">Informace o konfiguraci <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A> pro hostování nekořenového serveru URL najdete v tématu <xref:blazor/host-and-deploy/index#app-base-path> .</span><span class="sxs-lookup"><span data-stu-id="d96c8-114">For information on configuring <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A> for non-root URL server hosting, see <xref:blazor/host-and-deploy/index#app-base-path>.</span></span>

## <a name="route-templates"></a><span data-ttu-id="d96c8-115">Šablony směrování</span><span class="sxs-lookup"><span data-stu-id="d96c8-115">Route templates</span></span>

<span data-ttu-id="d96c8-116">Tato <xref:Microsoft.AspNetCore.Components.Routing.Router> součást umožňuje směrování na jednotlivé komponenty se zadanou trasou.</span><span class="sxs-lookup"><span data-stu-id="d96c8-116">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component enables routing to each component with a specified route.</span></span> <span data-ttu-id="d96c8-117"><xref:Microsoft.AspNetCore.Components.Routing.Router>Komponenta se zobrazí v `App.razor` souboru:</span><span class="sxs-lookup"><span data-stu-id="d96c8-117">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component appears in the `App.razor` file:</span></span>

```razor
<Router AppAssembly="@typeof(Startup).Assembly">
    <Found Context="routeData">
        <RouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
    </Found>
    <NotFound>
        <p>Sorry, there's nothing at this address.</p>
    </NotFound>
</Router>
```

<span data-ttu-id="d96c8-118">Když `.razor` je zkompilován soubor s `@page` direktivou, je k dispozici vygenerovaná třída <xref:Microsoft.AspNetCore.Components.RouteAttribute> určující šablonu trasy.</span><span class="sxs-lookup"><span data-stu-id="d96c8-118">When a `.razor` file with an `@page` directive is compiled, the generated class is provided a <xref:Microsoft.AspNetCore.Components.RouteAttribute> specifying the route template.</span></span>

<span data-ttu-id="d96c8-119">Za běhu <xref:Microsoft.AspNetCore.Components.RouteView> komponenty:</span><span class="sxs-lookup"><span data-stu-id="d96c8-119">At runtime, the <xref:Microsoft.AspNetCore.Components.RouteView> component:</span></span>

* <span data-ttu-id="d96c8-120">Přijímá <xref:Microsoft.AspNetCore.Components.RouteData> od <xref:Microsoft.AspNetCore.Components.Routing.Router> spolu s požadovanými parametry.</span><span class="sxs-lookup"><span data-stu-id="d96c8-120">Receives the <xref:Microsoft.AspNetCore.Components.RouteData> from the <xref:Microsoft.AspNetCore.Components.Routing.Router> along with any desired parameters.</span></span>
* <span data-ttu-id="d96c8-121">Vykreslí určenou komponentu pomocí jejího rozložení (nebo volitelného výchozího rozložení) pomocí zadaných parametrů.</span><span class="sxs-lookup"><span data-stu-id="d96c8-121">Renders the specified component with its layout (or an optional default layout) using the specified parameters.</span></span>

<span data-ttu-id="d96c8-122">Volitelně můžete zadat <xref:Microsoft.AspNetCore.Components.RouteView.DefaultLayout> parametr s třídou rozložení, který se má použít pro součásti, které neurčují rozložení.</span><span class="sxs-lookup"><span data-stu-id="d96c8-122">You can optionally specify a <xref:Microsoft.AspNetCore.Components.RouteView.DefaultLayout> parameter with a layout class to use for components that don't specify a layout.</span></span> <span data-ttu-id="d96c8-123">Výchozí Blazor šablony určují `MainLayout` komponentu.</span><span class="sxs-lookup"><span data-stu-id="d96c8-123">The default Blazor templates specify the `MainLayout` component.</span></span> <span data-ttu-id="d96c8-124">`MainLayout.razor` je ve složce projektu šablony `Shared` .</span><span class="sxs-lookup"><span data-stu-id="d96c8-124">`MainLayout.razor` is in the template project's `Shared` folder.</span></span> <span data-ttu-id="d96c8-125">Další informace o rozložení najdete v tématu <xref:blazor/layouts> .</span><span class="sxs-lookup"><span data-stu-id="d96c8-125">For more information on layouts, see <xref:blazor/layouts>.</span></span>

<span data-ttu-id="d96c8-126">Pro komponentu lze použít více šablon směrování.</span><span class="sxs-lookup"><span data-stu-id="d96c8-126">Multiple route templates can be applied to a component.</span></span> <span data-ttu-id="d96c8-127">Následující komponenta reaguje na požadavky pro `/BlazorRoute` a `/DifferentBlazorRoute` :</span><span class="sxs-lookup"><span data-stu-id="d96c8-127">The following component responds to requests for `/BlazorRoute` and `/DifferentBlazorRoute`:</span></span>

```razor
@page "/BlazorRoute"
@page "/DifferentBlazorRoute"

<h1>Blazor routing</h1>
```

> [!IMPORTANT]
> <span data-ttu-id="d96c8-128">Aby adresy URL byly správně přeloženy, musí aplikace obsahovat `<base>` značku v `wwwroot/index.html` souboru ( Blazor WebAssembly ) nebo `Pages/_Host.cshtml` souboru ( Blazor Server ) se základní cestou aplikace zadanou v `href` atributu ( `<base href="/">` ).</span><span class="sxs-lookup"><span data-stu-id="d96c8-128">For URLs to resolve correctly, the app must include a `<base>` tag in its `wwwroot/index.html` file (Blazor WebAssembly) or `Pages/_Host.cshtml` file (Blazor Server) with the app base path specified in the `href` attribute (`<base href="/">`).</span></span> <span data-ttu-id="d96c8-129">Další informace naleznete v tématu <xref:blazor/host-and-deploy/index#app-base-path>.</span><span class="sxs-lookup"><span data-stu-id="d96c8-129">For more information, see <xref:blazor/host-and-deploy/index#app-base-path>.</span></span>

## <a name="provide-custom-content-when-content-isnt-found"></a><span data-ttu-id="d96c8-130">Poskytnutí vlastního obsahu, když se nenalezne obsah</span><span class="sxs-lookup"><span data-stu-id="d96c8-130">Provide custom content when content isn't found</span></span>

<span data-ttu-id="d96c8-131"><xref:Microsoft.AspNetCore.Components.Routing.Router>Komponenta umožňuje aplikaci zadat vlastní obsah, pokud se pro požadovanou trasu nenajde obsah.</span><span class="sxs-lookup"><span data-stu-id="d96c8-131">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component allows the app to specify custom content if content isn't found for the requested route.</span></span>

<span data-ttu-id="d96c8-132">V `App.razor` souboru nastavte vlastní obsah v <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> parametru šablony <xref:Microsoft.AspNetCore.Components.Routing.Router> součásti:</span><span class="sxs-lookup"><span data-stu-id="d96c8-132">In the `App.razor` file, set custom content in the <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> template parameter of the <xref:Microsoft.AspNetCore.Components.Routing.Router> component:</span></span>

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

<span data-ttu-id="d96c8-133">Obsah `<NotFound>` značek může zahrnovat libovolné položky, jako jsou například jiné interaktivní součásti.</span><span class="sxs-lookup"><span data-stu-id="d96c8-133">The content of `<NotFound>` tags can include arbitrary items, such as other interactive components.</span></span> <span data-ttu-id="d96c8-134">Chcete-li použít výchozí rozložení <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> obsahu, přečtěte si téma <xref:blazor/layouts> .</span><span class="sxs-lookup"><span data-stu-id="d96c8-134">To apply a default layout to <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> content, see <xref:blazor/layouts>.</span></span>

## <a name="route-to-components-from-multiple-assemblies"></a><span data-ttu-id="d96c8-135">Směrování na součásti z více sestavení</span><span class="sxs-lookup"><span data-stu-id="d96c8-135">Route to components from multiple assemblies</span></span>

<span data-ttu-id="d96c8-136">Použijte <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> parametr k určení dalších sestavení, <xref:Microsoft.AspNetCore.Components.Routing.Router> která má součást při hledání směrovatelných komponent zvážit.</span><span class="sxs-lookup"><span data-stu-id="d96c8-136">Use the <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> parameter to specify additional assemblies for the <xref:Microsoft.AspNetCore.Components.Routing.Router> component to consider when searching for routable components.</span></span> <span data-ttu-id="d96c8-137">Zadaná sestavení jsou kromě zadaného sestavení považována za `AppAssembly` .</span><span class="sxs-lookup"><span data-stu-id="d96c8-137">Specified assemblies are considered in addition to the `AppAssembly`-specified assembly.</span></span> <span data-ttu-id="d96c8-138">V následujícím příkladu `Component1` je směrovatelný komponenta definovaná v odkazované knihovně tříd.</span><span class="sxs-lookup"><span data-stu-id="d96c8-138">In the following example, `Component1` is a routable component defined in a referenced class library.</span></span> <span data-ttu-id="d96c8-139">Následující <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> příklad vede k podpoře směrování pro `Component1` :</span><span class="sxs-lookup"><span data-stu-id="d96c8-139">The following <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> example results in routing support for `Component1`:</span></span>

```razor
<Router
    AppAssembly="@typeof(Program).Assembly"
    AdditionalAssemblies="new[] { typeof(Component1).Assembly }">
    ...
</Router>
```

## <a name="route-parameters"></a><span data-ttu-id="d96c8-140">Parametry směrování</span><span class="sxs-lookup"><span data-stu-id="d96c8-140">Route parameters</span></span>

<span data-ttu-id="d96c8-141">Směrovač používá parametry směrování k naplnění odpovídajících parametrů komponenty se stejným názvem (bez rozlišení velkých a malých písmen):</span><span class="sxs-lookup"><span data-stu-id="d96c8-141">The router uses route parameters to populate the corresponding component parameters with the same name (case insensitive):</span></span>

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

<span data-ttu-id="d96c8-142">Volitelné parametry nejsou podporovány.</span><span class="sxs-lookup"><span data-stu-id="d96c8-142">Optional parameters aren't supported.</span></span> <span data-ttu-id="d96c8-143">`@page`V předchozím příkladu jsou aplikovány dvě direktivy.</span><span class="sxs-lookup"><span data-stu-id="d96c8-143">Two `@page` directives are applied in the previous example.</span></span> <span data-ttu-id="d96c8-144">První umožňuje navigaci na součást bez parametru.</span><span class="sxs-lookup"><span data-stu-id="d96c8-144">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="d96c8-145">Druhá `@page` direktiva převezme `{text}` parametr Route a přiřadí hodnotu `Text` Vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="d96c8-145">The second `@page` directive takes the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

## <a name="route-constraints"></a><span data-ttu-id="d96c8-146">Omezení trasy</span><span class="sxs-lookup"><span data-stu-id="d96c8-146">Route constraints</span></span>

<span data-ttu-id="d96c8-147">Omezení trasy vynutilo typ shodný s typem pro segment směrování do součásti.</span><span class="sxs-lookup"><span data-stu-id="d96c8-147">A route constraint enforces type matching on a route segment to a component.</span></span>

<span data-ttu-id="d96c8-148">V následujícím příkladu trasa k `Users` součásti odpovídá pouze v případě, že:</span><span class="sxs-lookup"><span data-stu-id="d96c8-148">In the following example, the route to the `Users` component only matches if:</span></span>

* <span data-ttu-id="d96c8-149">`Id`V adrese URL požadavku je přítomen segment směrování.</span><span class="sxs-lookup"><span data-stu-id="d96c8-149">An `Id` route segment is present on the request URL.</span></span>
* <span data-ttu-id="d96c8-150">`Id`Segment je celé číslo ( `int` ).</span><span class="sxs-lookup"><span data-stu-id="d96c8-150">The `Id` segment is an integer (`int`).</span></span>

[!code-razor[](routing/samples_snapshot/3.x/Constraint.razor?highlight=1)]

<span data-ttu-id="d96c8-151">K dispozici jsou omezení tras uvedená v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="d96c8-151">The route constraints shown in the following table are available.</span></span> <span data-ttu-id="d96c8-152">Pro omezení trasy, která se shodují s invariantní jazykovou verzí, se podívejte na upozornění pod tabulkou, kde najdete další informace.</span><span class="sxs-lookup"><span data-stu-id="d96c8-152">For the route constraints that match with the invariant culture, see the warning below the table for more information.</span></span>

| <span data-ttu-id="d96c8-153">Jedinečn</span><span class="sxs-lookup"><span data-stu-id="d96c8-153">Constraint</span></span> | <span data-ttu-id="d96c8-154">Příklad</span><span class="sxs-lookup"><span data-stu-id="d96c8-154">Example</span></span>           | <span data-ttu-id="d96c8-155">Příklady shody</span><span class="sxs-lookup"><span data-stu-id="d96c8-155">Example Matches</span></span>                                                                  | <span data-ttu-id="d96c8-156">Invariantní</span><span class="sxs-lookup"><span data-stu-id="d96c8-156">Invariant</span></span><br><span data-ttu-id="d96c8-157">jazyková verze</span><span class="sxs-lookup"><span data-stu-id="d96c8-157">culture</span></span><br><span data-ttu-id="d96c8-158">shoda</span><span class="sxs-lookup"><span data-stu-id="d96c8-158">matching</span></span> |
| ---------- | ----------------- | -------------------------------------------------------------------------------- | :------------------------------: |
| `bool`     | `{active:bool}`   | <span data-ttu-id="d96c8-159">`true`, `FALSE`</span><span class="sxs-lookup"><span data-stu-id="d96c8-159">`true`, `FALSE`</span></span>                                                                  | <span data-ttu-id="d96c8-160">No</span><span class="sxs-lookup"><span data-stu-id="d96c8-160">No</span></span>                               |
| `datetime` | `{dob:datetime}`  | <span data-ttu-id="d96c8-161">`2016-12-31`, `2016-12-31 7:32pm`</span><span class="sxs-lookup"><span data-stu-id="d96c8-161">`2016-12-31`, `2016-12-31 7:32pm`</span></span>                                                | <span data-ttu-id="d96c8-162">Yes</span><span class="sxs-lookup"><span data-stu-id="d96c8-162">Yes</span></span>                              |
| `decimal`  | `{price:decimal}` | <span data-ttu-id="d96c8-163">`49.99`, `-1,000.01`</span><span class="sxs-lookup"><span data-stu-id="d96c8-163">`49.99`, `-1,000.01`</span></span>                                                             | <span data-ttu-id="d96c8-164">Yes</span><span class="sxs-lookup"><span data-stu-id="d96c8-164">Yes</span></span>                              |
| `double`   | `{weight:double}` | <span data-ttu-id="d96c8-165">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="d96c8-165">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="d96c8-166">Yes</span><span class="sxs-lookup"><span data-stu-id="d96c8-166">Yes</span></span>                              |
| `float`    | `{weight:float}`  | <span data-ttu-id="d96c8-167">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="d96c8-167">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="d96c8-168">Yes</span><span class="sxs-lookup"><span data-stu-id="d96c8-168">Yes</span></span>                              |
| `guid`     | `{id:guid}`       | <span data-ttu-id="d96c8-169">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span><span class="sxs-lookup"><span data-stu-id="d96c8-169">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span></span> | <span data-ttu-id="d96c8-170">No</span><span class="sxs-lookup"><span data-stu-id="d96c8-170">No</span></span>                               |
| `int`      | `{id:int}`        | <span data-ttu-id="d96c8-171">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="d96c8-171">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="d96c8-172">Yes</span><span class="sxs-lookup"><span data-stu-id="d96c8-172">Yes</span></span>                              |
| `long`     | `{ticks:long}`    | <span data-ttu-id="d96c8-173">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="d96c8-173">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="d96c8-174">Yes</span><span class="sxs-lookup"><span data-stu-id="d96c8-174">Yes</span></span>                              |

> [!WARNING]
> <span data-ttu-id="d96c8-175">Omezení směrování, která ověřují adresu URL a jsou převedena na typ CLR (například `int` nebo <xref:System.DateTime> ), vždy používají invariantní jazykovou verzi.</span><span class="sxs-lookup"><span data-stu-id="d96c8-175">Route constraints that verify the URL and are converted to a CLR type (such as `int` or <xref:System.DateTime>) always use the invariant culture.</span></span> <span data-ttu-id="d96c8-176">Tato omezení předpokládají, že adresa URL nelze lokalizovat.</span><span class="sxs-lookup"><span data-stu-id="d96c8-176">These constraints assume that the URL is non-localizable.</span></span>

### <a name="routing-with-urls-that-contain-dots"></a><span data-ttu-id="d96c8-177">Směrování s adresami URL, které obsahují tečky</span><span class="sxs-lookup"><span data-stu-id="d96c8-177">Routing with URLs that contain dots</span></span>

<span data-ttu-id="d96c8-178">U hostovaných Blazor WebAssembly a Blazor Server aplikací předpokládá šablona výchozí trasy na straně serveru, že pokud poslední segment adresy URL požadavku obsahuje tečku ( `.` ), kterou soubor požaduje (například `https://localhost.com:5001/example/some.thing` ).</span><span class="sxs-lookup"><span data-stu-id="d96c8-178">For hosted Blazor WebAssembly and Blazor Server apps, the server-side default route template assumes that if the last segment of a request URL contains a dot (`.`) that a file is requested (for example, `https://localhost.com:5001/example/some.thing`).</span></span> <span data-ttu-id="d96c8-179">Bez další konfigurace aplikace vrátí odpověď na 404, která *nebyla nalezena* , pokud by to bylo určeno pro směrování do komponenty.</span><span class="sxs-lookup"><span data-stu-id="d96c8-179">Without additional configuration, an app returns a *404 - Not Found* response if this was meant to route to a component.</span></span> <span data-ttu-id="d96c8-180">Aby bylo možné použít trasu s jedním nebo více parametry, které obsahují tečku, musí aplikace nakonfigurovat trasu s vlastní šablonou.</span><span class="sxs-lookup"><span data-stu-id="d96c8-180">To use a route with one or more parameters that contains a dot, the app must configure the route with a custom template.</span></span>

<span data-ttu-id="d96c8-181">Vezměte v úvahu následující `Example` součást, která může přijmout parametr trasy z posledního segmentu adresy URL:</span><span class="sxs-lookup"><span data-stu-id="d96c8-181">Consider the following `Example` component that can receive a route parameter from the last segment of the URL:</span></span>

```razor
@page "/example"
@page "/example/{param}"

<p>
    Param: @Param
</p>

@code {
    [Parameter]
    public string Param { get; set; }
}
```

<span data-ttu-id="d96c8-182">Chcete-li aplikaci *serveru* hostovaného Blazor WebAssembly řešení povolit směrování požadavku s tečkou v `param` parametru, přidejte šablonu trasy záložního souboru s volitelným parametrem v `Startup.Configure` ( `Startup.cs` ):</span><span class="sxs-lookup"><span data-stu-id="d96c8-182">To permit the *Server* app of a hosted Blazor WebAssembly solution to route the request with a dot in the `param` parameter, add a fallback file route template with the optional parameter in `Startup.Configure` (`Startup.cs`):</span></span>

```csharp
endpoints.MapFallbackToFile("/example/{param?}", "index.html");
```

<span data-ttu-id="d96c8-183">Pokud chcete Blazor Server aplikaci nakonfigurovat tak, aby směrovala požadavek s tečkou v `param` parametru, přidejte šablonu trasy záložní stránky s volitelným parametrem v `Startup.Configure` ( `Startup.cs` ):</span><span class="sxs-lookup"><span data-stu-id="d96c8-183">To configure a Blazor Server app to route the request with a dot in the `param` parameter, add a fallback page route template with the optional parameter in `Startup.Configure` (`Startup.cs`):</span></span>

```csharp
endpoints.MapFallbackToPage("/example/{param?}", "/_Host");
```

<span data-ttu-id="d96c8-184">Další informace naleznete v tématu <xref:fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="d96c8-184">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="catch-all-route-parameters"></a><span data-ttu-id="d96c8-185">Catch – všechny parametry tras</span><span class="sxs-lookup"><span data-stu-id="d96c8-185">Catch-all route parameters</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="d96c8-186">*Tato část se týká ASP.NET Core v rozhraní .NET 5 Release Candidate 1 (RC1) nebo novějším.*</span><span class="sxs-lookup"><span data-stu-id="d96c8-186">*This section applies to ASP.NET Core in .NET 5 Release Candidate 1 (RC1) or later.*</span></span>

<span data-ttu-id="d96c8-187">Catch – všechny parametry tras, které zachycují cesty mezi více hranicemi složek, jsou podporovány v součástech.</span><span class="sxs-lookup"><span data-stu-id="d96c8-187">Catch-all route parameters, which capture paths across multiple folder boundaries, are supported in components.</span></span> <span data-ttu-id="d96c8-188">Parametr trasy catch-all musí být:</span><span class="sxs-lookup"><span data-stu-id="d96c8-188">The catch-all route parameter must be:</span></span>

* <span data-ttu-id="d96c8-189">Název se shoduje s názvem segmentu směrování.</span><span class="sxs-lookup"><span data-stu-id="d96c8-189">Named to match the route segment name.</span></span> <span data-ttu-id="d96c8-190">Při pojmenovávání nezáleží na velikosti písmen.</span><span class="sxs-lookup"><span data-stu-id="d96c8-190">Naming isn't case sensitive.</span></span>
* <span data-ttu-id="d96c8-191">`string`Typ.</span><span class="sxs-lookup"><span data-stu-id="d96c8-191">A `string` type.</span></span> <span data-ttu-id="d96c8-192">Rozhraní neposkytuje automatické přetypování.</span><span class="sxs-lookup"><span data-stu-id="d96c8-192">The framework doesn't provide automatic casting.</span></span>
* <span data-ttu-id="d96c8-193">Na konci adresy URL.</span><span class="sxs-lookup"><span data-stu-id="d96c8-193">At the end of the URL.</span></span>

```razor
@page "/page/{*pageRoute}"

@code {
    [Parameter]
    public string PageRoute { get; set; }
}
```

<span data-ttu-id="d96c8-194">Pro adresu URL `/page/this/is/a/test` s šablonou směrování pro `/page/{*pageRoute}` `PageRoute` je hodnota nastavená na `this/is/a/test` .</span><span class="sxs-lookup"><span data-stu-id="d96c8-194">For the URL `/page/this/is/a/test` with a route template of `/page/{*pageRoute}`, the value of `PageRoute` is set to `this/is/a/test`.</span></span>

<span data-ttu-id="d96c8-195">Lomítka a segmenty zachycené cesty jsou Dekódovatelné.</span><span class="sxs-lookup"><span data-stu-id="d96c8-195">Slashes and segments of the captured path are decoded.</span></span> <span data-ttu-id="d96c8-196">Pro šablonu trasy je `/page/{*pageRoute}` výsledkem adresa URL `/page/this/is/a%2Ftest%2A` `this/is/a/test*` .</span><span class="sxs-lookup"><span data-stu-id="d96c8-196">For a route template of `/page/{*pageRoute}`, the URL `/page/this/is/a%2Ftest%2A` yields `this/is/a/test*`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="d96c8-197">Catch – všechny parametry tras jsou podporované v ASP.NET Core v rozhraní .NET 5 Release Candidate 1 (RC1) nebo novějším. \*</span><span class="sxs-lookup"><span data-stu-id="d96c8-197">Catch-all route parameters are supported in ASP.NET Core in .NET 5 Release Candidate 1 (RC1) or later.\*</span></span>

::: moniker-end

## <a name="navlink-component"></a><span data-ttu-id="d96c8-198">Komponenta NavLink</span><span class="sxs-lookup"><span data-stu-id="d96c8-198">NavLink component</span></span>

<span data-ttu-id="d96c8-199">Použít <xref:Microsoft.AspNetCore.Components.Routing.NavLink> komponentu namísto prvků hypertextového odkazu HTML ( `<a>` ) při vytváření navigačních odkazů.</span><span class="sxs-lookup"><span data-stu-id="d96c8-199">Use a <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component in place of HTML hyperlink elements (`<a>`) when creating navigation links.</span></span> <span data-ttu-id="d96c8-200"><xref:Microsoft.AspNetCore.Components.Routing.NavLink>Komponenta se chová jako `<a>` element s tím rozdílem, že přepíná `active` třídu CSS na základě toho, zda `href` odpovídá aktuální adrese URL.</span><span class="sxs-lookup"><span data-stu-id="d96c8-200">A <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component behaves like an `<a>` element, except it toggles an `active` CSS class based on whether its `href` matches the current URL.</span></span> <span data-ttu-id="d96c8-201">`active`Třída pomáhá uživateli pochopit, která stránka je aktivní stránkou mezi zobrazenými navigačními odkazy.</span><span class="sxs-lookup"><span data-stu-id="d96c8-201">The `active` class helps a user understand which page is the active page among the navigation links displayed.</span></span> <span data-ttu-id="d96c8-202">Volitelně můžete přiřadit název třídy šablony stylů CSS k <xref:Microsoft.AspNetCore.Components.Routing.NavLink.ActiveClass?displayProperty=nameWithType> použití vlastní třídy CSS pro vykreslený odkaz, když aktuální trasa odpovídá `href` .</span><span class="sxs-lookup"><span data-stu-id="d96c8-202">Optionally, assign a CSS class name to <xref:Microsoft.AspNetCore.Components.Routing.NavLink.ActiveClass?displayProperty=nameWithType> to apply a custom CSS class to the rendered link when the current route matches the `href`.</span></span>

<span data-ttu-id="d96c8-203">Následující `NavMenu` Komponenta vytvoří [`Bootstrap`](https://getbootstrap.com/docs/) navigační panel, který ukazuje, jak používat <xref:Microsoft.AspNetCore.Components.Routing.NavLink> komponenty:</span><span class="sxs-lookup"><span data-stu-id="d96c8-203">The following `NavMenu` component creates a [`Bootstrap`](https://getbootstrap.com/docs/) navigation bar that demonstrates how to use <xref:Microsoft.AspNetCore.Components.Routing.NavLink> components:</span></span>

[!code-razor[](routing/samples_snapshot/3.x/NavMenu.razor?highlight=4,9)]

<span data-ttu-id="d96c8-204">Existují dvě <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch> Možnosti, které lze přiřadit k `Match` atributu `<NavLink>` elementu:</span><span class="sxs-lookup"><span data-stu-id="d96c8-204">There are two <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch> options that you can assign to the `Match` attribute of the `<NavLink>` element:</span></span>

* <span data-ttu-id="d96c8-205"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.All?displayProperty=nameWithType>: <xref:Microsoft.AspNetCore.Components.Routing.NavLink> Je aktivní, pokud odpovídá celé aktuální adrese URL.</span><span class="sxs-lookup"><span data-stu-id="d96c8-205"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.All?displayProperty=nameWithType>: The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> is active when it matches the entire current URL.</span></span>
* <span data-ttu-id="d96c8-206"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.Prefix?displayProperty=nameWithType> (*výchozí*): <xref:Microsoft.AspNetCore.Components.Routing.NavLink> je aktivní, pokud odpovídá libovolné PŘEDPONĚ aktuální adresy URL.</span><span class="sxs-lookup"><span data-stu-id="d96c8-206"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.Prefix?displayProperty=nameWithType> (*default*): The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> is active when it matches any prefix of the current URL.</span></span>

<span data-ttu-id="d96c8-207">V předchozím příkladu se Domovská stránka <xref:Microsoft.AspNetCore.Components.Routing.NavLink> `href=""` shoduje s adresou URL domů a přijímá pouze `active` třídu CSS ve výchozí základní cestě URL aplikace (například `https://localhost:5001/` ).</span><span class="sxs-lookup"><span data-stu-id="d96c8-207">In the preceding example, the Home <xref:Microsoft.AspNetCore.Components.Routing.NavLink> `href=""` matches the home URL and only receives the `active` CSS class at the app's default base path URL (for example, `https://localhost:5001/`).</span></span> <span data-ttu-id="d96c8-208">Druhá <xref:Microsoft.AspNetCore.Components.Routing.NavLink> Získá třídu, `active` když uživatel navštíví libovolnou adresu URL s `MyComponent` předponou (například `https://localhost:5001/MyComponent` a `https://localhost:5001/MyComponent/AnotherSegment` ).</span><span class="sxs-lookup"><span data-stu-id="d96c8-208">The second <xref:Microsoft.AspNetCore.Components.Routing.NavLink> receives the `active` class when the user visits any URL with a `MyComponent` prefix (for example, `https://localhost:5001/MyComponent` and `https://localhost:5001/MyComponent/AnotherSegment`).</span></span>

<span data-ttu-id="d96c8-209">Další <xref:Microsoft.AspNetCore.Components.Routing.NavLink> atributy komponenty jsou předány do vykreslené značky ukotvení.</span><span class="sxs-lookup"><span data-stu-id="d96c8-209">Additional <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component attributes are passed through to the rendered anchor tag.</span></span> <span data-ttu-id="d96c8-210">V následujícím příkladu <xref:Microsoft.AspNetCore.Components.Routing.NavLink> Komponenta zahrnuje `target` atribut:</span><span class="sxs-lookup"><span data-stu-id="d96c8-210">In the following example, the <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component includes the `target` attribute:</span></span>

```razor
<NavLink href="my-page" target="_blank">My page</NavLink>
```

<span data-ttu-id="d96c8-211">Vykresluje se následující kód HTML:</span><span class="sxs-lookup"><span data-stu-id="d96c8-211">The following HTML markup is rendered:</span></span>

```html
<a href="my-page" target="_blank">My page</a>
```

> [!WARNING]
> <span data-ttu-id="d96c8-212">Vzhledem k způsobu, jakým Blazor vykresluje podřízený obsah, vykreslování `NavLink` komponent uvnitř `for` smyčky vyžaduje proměnnou místního indexu, pokud je proměnná incrementing použita v `NavLink` obsahu komponenty (podřízené):</span><span class="sxs-lookup"><span data-stu-id="d96c8-212">Due to the way that Blazor renders child content, rendering `NavLink` components inside a `for` loop requires a local index variable if the incrementing loop variable is used in the `NavLink` (child) component's content:</span></span>
>
> ```razor
> @for (int c = 0; c < 10; c++)
> {
>     var current = c;
>     <li ...>
>         <NavLink ... href="@c">
>             <span ...></span> @current
>         </NavLink>
>     </li>
> }
> ```
>
> <span data-ttu-id="d96c8-213">Použití proměnné indexu v tomto scénáři je požadavek pro **všechny** podřízené komponenty, které používají proměnnou smyčky v jejím [podřízeném obsahu](xref:blazor/components/index#child-content), nikoli pouze `NavLink` komponentu.</span><span class="sxs-lookup"><span data-stu-id="d96c8-213">Using an index variable in this scenario is a requirement for **any** child component that uses a loop variable in its [child content](xref:blazor/components/index#child-content), not just the `NavLink` component.</span></span>
>
> <span data-ttu-id="d96c8-214">Alternativně můžete použít `foreach` smyčku s <xref:System.Linq.Enumerable.Range%2A?displayProperty=nameWithType> :</span><span class="sxs-lookup"><span data-stu-id="d96c8-214">Alternatively, use a `foreach` loop with <xref:System.Linq.Enumerable.Range%2A?displayProperty=nameWithType>:</span></span>
>
> ```razor
> @foreach(var c in Enumerable.Range(0,10))
> {
>     <li ...>
>         <NavLink ... href="@c">
>             <span ...></span> @c
>         </NavLink>
>     </li>
> }
> ```

## <a name="uri-and-navigation-state-helpers"></a><span data-ttu-id="d96c8-215">Identifikátory URI a pomocníka pro stav navigace</span><span class="sxs-lookup"><span data-stu-id="d96c8-215">URI and navigation state helpers</span></span>

<span data-ttu-id="d96c8-216">Použijte <xref:Microsoft.AspNetCore.Components.NavigationManager> pro práci s identifikátory URI a navigací v kódu jazyka C#.</span><span class="sxs-lookup"><span data-stu-id="d96c8-216">Use <xref:Microsoft.AspNetCore.Components.NavigationManager> to work with URIs and navigation in C# code.</span></span> <span data-ttu-id="d96c8-217"><xref:Microsoft.AspNetCore.Components.NavigationManager> poskytuje událost a metody uvedené v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="d96c8-217"><xref:Microsoft.AspNetCore.Components.NavigationManager> provides the event and methods shown in the following table.</span></span>

| <span data-ttu-id="d96c8-218">Člen</span><span class="sxs-lookup"><span data-stu-id="d96c8-218">Member</span></span> | <span data-ttu-id="d96c8-219">Description</span><span class="sxs-lookup"><span data-stu-id="d96c8-219">Description</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri> | <span data-ttu-id="d96c8-220">Získá aktuální absolutní identifikátor URI.</span><span class="sxs-lookup"><span data-stu-id="d96c8-220">Gets the current absolute URI.</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> | <span data-ttu-id="d96c8-221">Získá základní identifikátor URI (s koncovým lomítkem), který může být součástí relativních cest URI pro vytvoření absolutního identifikátoru URI.</span><span class="sxs-lookup"><span data-stu-id="d96c8-221">Gets the base URI (with a trailing slash) that can be prepended to relative URI paths to produce an absolute URI.</span></span> <span data-ttu-id="d96c8-222">Obvykle <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> odpovídá `href` atributu v `<base>` prvku dokumentu v `wwwroot/index.html` ( Blazor WebAssembly ) nebo `Pages/_Host.cshtml` ( Blazor Server ).</span><span class="sxs-lookup"><span data-stu-id="d96c8-222">Typically, <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> corresponds to the `href` attribute on the document's `<base>` element in `wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server).</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A> | <span data-ttu-id="d96c8-223">Přejde k zadanému identifikátoru URI.</span><span class="sxs-lookup"><span data-stu-id="d96c8-223">Navigates to the specified URI.</span></span> <span data-ttu-id="d96c8-224">Pokud `forceLoad` je `true` :</span><span class="sxs-lookup"><span data-stu-id="d96c8-224">If `forceLoad` is `true`:</span></span><ul><li><span data-ttu-id="d96c8-225">Směrování na straně klienta se nepoužívá.</span><span class="sxs-lookup"><span data-stu-id="d96c8-225">Client-side routing is bypassed.</span></span></li><li><span data-ttu-id="d96c8-226">Prohlížeč je nucen načíst novou stránku ze serveru, bez ohledu na to, zda je identifikátor URI obvykle zpracováván směrovačem na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="d96c8-226">The browser is forced to load the new page from the server, whether or not the URI is normally handled by the client-side router.</span></span></li></ul> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged> | <span data-ttu-id="d96c8-227">Událost, která se aktivuje, když se změní navigační umístění</span><span class="sxs-lookup"><span data-stu-id="d96c8-227">An event that fires when the navigation location has changed.</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.ToAbsoluteUri%2A> | <span data-ttu-id="d96c8-228">Převede relativní identifikátor URI na absolutní identifikátor URI.</span><span class="sxs-lookup"><span data-stu-id="d96c8-228">Converts a relative URI into an absolute URI.</span></span> |
| <span style="word-break:normal;word-wrap:normal"><xref:Microsoft.AspNetCore.Components.NavigationManager.ToBaseRelativePath%2A></span> | <span data-ttu-id="d96c8-229">Vzhledem k základnímu identifikátoru URI (například identifikátor URI, který dřív vrátil <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> ), převede absolutní identifikátor URI na URI relativně k základní předponě identifikátoru URI.</span><span class="sxs-lookup"><span data-stu-id="d96c8-229">Given a base URI (for example, a URI previously returned by <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri>), converts an absolute URI into a URI relative to the base URI prefix.</span></span> |

<span data-ttu-id="d96c8-230">`Counter`Pokud je vybráno tlačítko, následující komponenta přejde na součást aplikace:</span><span class="sxs-lookup"><span data-stu-id="d96c8-230">The following component navigates to the app's `Counter` component when the button is selected:</span></span>

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

<span data-ttu-id="d96c8-231">Následující komponenta zpracovává událost změny umístění při přihlášení k odběru <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="d96c8-231">The following component handles a location changed event by subscribing to <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged?displayProperty=nameWithType>.</span></span> <span data-ttu-id="d96c8-232">`HandleLocationChanged`Metoda je nepřipojená, pokud `Dispose` je volána rozhraním.</span><span class="sxs-lookup"><span data-stu-id="d96c8-232">The `HandleLocationChanged` method is unhooked when `Dispose` is called by the framework.</span></span> <span data-ttu-id="d96c8-233">Odpojování metody umožňuje uvolňování paměti komponenty.</span><span class="sxs-lookup"><span data-stu-id="d96c8-233">Unhooking the method permits garbage collection of the component.</span></span>

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

<span data-ttu-id="d96c8-234"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs> poskytuje následující informace o události:</span><span class="sxs-lookup"><span data-stu-id="d96c8-234"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs> provides the following information about the event:</span></span>

* <span data-ttu-id="d96c8-235"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>: Adresa URL nového umístění.</span><span class="sxs-lookup"><span data-stu-id="d96c8-235"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>: The URL of the new location.</span></span>
* <span data-ttu-id="d96c8-236"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>: Pokud `true` Blazor byla zachycena navigace z prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="d96c8-236"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>: If `true`, Blazor intercepted the navigation from the browser.</span></span> <span data-ttu-id="d96c8-237">`false` <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> V důsledku toho došlo k navigaci.</span><span class="sxs-lookup"><span data-stu-id="d96c8-237">If `false`, <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> caused the navigation to occur.</span></span>

<span data-ttu-id="d96c8-238">Další informace o vyřazení součástí najdete v tématu <xref:blazor/components/lifecycle#component-disposal-with-idisposable> .</span><span class="sxs-lookup"><span data-stu-id="d96c8-238">For more information on component disposal, see <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span></span>

## <a name="query-string-and-parse-parameters"></a><span data-ttu-id="d96c8-239">Parametry řetězce dotazu a analýzy</span><span class="sxs-lookup"><span data-stu-id="d96c8-239">Query string and parse parameters</span></span>

<span data-ttu-id="d96c8-240">Řetězec dotazu požadavku lze získat z <xref:Microsoft.AspNetCore.Components.NavigationManager> <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri> vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="d96c8-240">The query string of a request can be obtained from the <xref:Microsoft.AspNetCore.Components.NavigationManager>'s <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri> property:</span></span>

```razor
@inject NavigationManager Navigation

...

var query = new Uri(Navigation.Uri).Query;
```

<span data-ttu-id="d96c8-241">Postup analýzy parametrů řetězce dotazu:</span><span class="sxs-lookup"><span data-stu-id="d96c8-241">To parse a query string's parameters:</span></span>

* <span data-ttu-id="d96c8-242">Přidejte odkaz na balíček pro [Microsoft. AspNetCore. webutilities](https://www.nuget.org/packages/Microsoft.AspNetCore.WebUtilities).</span><span class="sxs-lookup"><span data-stu-id="d96c8-242">Add a package reference for [Microsoft.AspNetCore.WebUtilities](https://www.nuget.org/packages/Microsoft.AspNetCore.WebUtilities).</span></span>
* <span data-ttu-id="d96c8-243">Získejte hodnotu po analýze řetězce dotazu pomocí <xref:Microsoft.AspNetCore.WebUtilities.QueryHelpers.ParseQuery%2A?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="d96c8-243">Obtain the value after parsing the query string with <xref:Microsoft.AspNetCore.WebUtilities.QueryHelpers.ParseQuery%2A?displayProperty=nameWithType>.</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.WebUtilities
@inject NavigationManager NavigationManager

<h1>Query string parse example</h1>

<p>Value: @queryValue</p>

@code {
    private string queryValue = "Not set";

    protected override void OnInitialized()
    {
        var query = new Uri(NavigationManager.Uri).Query;

        if (QueryHelpers.ParseQuery(query).TryGetValue("{KEY}", out var value))
        {
            queryValue = value;
        }
    }
}
```

<span data-ttu-id="d96c8-244">Zástupný symbol `{KEY}` v předchozím příkladu je klíč parametrů řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="d96c8-244">The placeholder `{KEY}` in the preceding example is the query string parameter key.</span></span> <span data-ttu-id="d96c8-245">Například dvojice klíč-hodnota adresy URL `?ship=Tardis` používá klíč `ship` .</span><span class="sxs-lookup"><span data-stu-id="d96c8-245">For example, the URL key-value pair `?ship=Tardis` uses a key of `ship`.</span></span>
