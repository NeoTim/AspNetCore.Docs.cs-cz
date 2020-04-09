---
title: ASP.NET Blazor základní směrování
author: guardrex
description: Přečtěte si, jak směrovat požadavky v aplikacích a o komponentě NavLink.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/17/2020
no-loc:
- Blazor
- SignalR
uid: blazor/routing
ms.openlocfilehash: 87579c88a37e0258921e199db2b5d8c7627f5499
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80218892"
---
# <a name="aspnet-core-blazor-routing"></a><span data-ttu-id="d19b0-103">ASP.NET Core Blazor směrování</span><span class="sxs-lookup"><span data-stu-id="d19b0-103">ASP.NET Core Blazor routing</span></span>

<span data-ttu-id="d19b0-104">Podle [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="d19b0-104">By [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="d19b0-105">Přečtěte si, jak směrovat požadavky `NavLink` a jak pomocí komponenty vytvářet navigační odkazy v aplikacích Blazor.</span><span class="sxs-lookup"><span data-stu-id="d19b0-105">Learn how to route requests and how to use the `NavLink` component to create navigation links in Blazor apps.</span></span>

## <a name="aspnet-core-endpoint-routing-integration"></a><span data-ttu-id="d19b0-106">ASP.NET integrace směrování koncových bodů jádra</span><span class="sxs-lookup"><span data-stu-id="d19b0-106">ASP.NET Core endpoint routing integration</span></span>

<span data-ttu-id="d19b0-107">Blazor Server je integrován do [ASP.NET směrování koncových bodů](xref:fundamentals/routing).</span><span class="sxs-lookup"><span data-stu-id="d19b0-107">Blazor Server is integrated into [ASP.NET Core Endpoint Routing](xref:fundamentals/routing).</span></span> <span data-ttu-id="d19b0-108">Aplikace ASP.NET Core je nakonfigurována tak, aby `MapBlazorHub` `Startup.Configure`přijímala příchozí připojení pro interaktivní součásti s aplikací v :</span><span class="sxs-lookup"><span data-stu-id="d19b0-108">An ASP.NET Core app is configured to accept incoming connections for interactive components with `MapBlazorHub` in `Startup.Configure`:</span></span>

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

<span data-ttu-id="d19b0-109">Nejtypičtější konfigurace je směrovat všechny požadavky na stránku Razor, která funguje jako hostitel pro část aplikace Blazor Server na straně serveru.</span><span class="sxs-lookup"><span data-stu-id="d19b0-109">The most typical configuration is to route all requests to a Razor page, which acts as the host for the server-side part of the Blazor Server app.</span></span> <span data-ttu-id="d19b0-110">Podle konvence je stránka *hostitele* obvykle pojmenována *_Host.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="d19b0-110">By convention, the *host* page is usually named *_Host.cshtml*.</span></span> <span data-ttu-id="d19b0-111">Trasa zadaná v hostitelském souboru se nazývá *záložní trasa,* protože pracuje s nízkou prioritou při porovnávání tras.</span><span class="sxs-lookup"><span data-stu-id="d19b0-111">The route specified in the host file is called a *fallback route* because it operates with a low priority in route matching.</span></span> <span data-ttu-id="d19b0-112">Záložní trasa je považována za jiné trasy, které se neshodují.</span><span class="sxs-lookup"><span data-stu-id="d19b0-112">The fallback route is considered when other routes don't match.</span></span> <span data-ttu-id="d19b0-113">To umožňuje aplikaci používat jiné řadiče a stránky bez zasahování do aplikace Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="d19b0-113">This allows the app to use others controllers and pages without interfering with the Blazor Server app.</span></span>

## <a name="route-templates"></a><span data-ttu-id="d19b0-114">Šablony tras</span><span class="sxs-lookup"><span data-stu-id="d19b0-114">Route templates</span></span>

<span data-ttu-id="d19b0-115">Součást `Router` umožňuje směrování ke každé součásti se zadanou trasou.</span><span class="sxs-lookup"><span data-stu-id="d19b0-115">The `Router` component enables routing to each component with a specified route.</span></span> <span data-ttu-id="d19b0-116">Komponenta `Router` se zobrazí v souboru *App.razor:*</span><span class="sxs-lookup"><span data-stu-id="d19b0-116">The `Router` component appears in the *App.razor* file:</span></span>

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

<span data-ttu-id="d19b0-117">Při kompilaci souboru `@page` *.razor* se směrnicí je vygenerovaná třída poskytnuta <xref:Microsoft.AspNetCore.Components.RouteAttribute> určující šablonu trasy.</span><span class="sxs-lookup"><span data-stu-id="d19b0-117">When a *.razor* file with an `@page` directive is compiled, the generated class is provided a <xref:Microsoft.AspNetCore.Components.RouteAttribute> specifying the route template.</span></span>

<span data-ttu-id="d19b0-118">Za běhu komponenta: `RouteView`</span><span class="sxs-lookup"><span data-stu-id="d19b0-118">At runtime, the `RouteView` component:</span></span>

* <span data-ttu-id="d19b0-119">Přijímá `RouteData` od `Router` spolu s požadovanými parametry.</span><span class="sxs-lookup"><span data-stu-id="d19b0-119">Receives the `RouteData` from the `Router` along with any desired parameters.</span></span>
* <span data-ttu-id="d19b0-120">Vykreslí zadanou komponentu s jejím rozložením (nebo volitelným výchozím rozložením) pomocí zadaných parametrů.</span><span class="sxs-lookup"><span data-stu-id="d19b0-120">Renders the specified component with its layout (or an optional default layout) using the specified parameters.</span></span>

<span data-ttu-id="d19b0-121">Volitelně můžete zadat `DefaultLayout` parametr s třídou rozvržení, který se použije pro součásti, které neurčují rozložení.</span><span class="sxs-lookup"><span data-stu-id="d19b0-121">You can optionally specify a `DefaultLayout` parameter with a layout class to use for components that don't specify a layout.</span></span> <span data-ttu-id="d19b0-122">Výchozí šablony Blazor určují `MainLayout` komponentu.</span><span class="sxs-lookup"><span data-stu-id="d19b0-122">The default Blazor templates specify the `MainLayout` component.</span></span> <span data-ttu-id="d19b0-123">*MainLayout.razor* je ve *sdílené* složce projektu šablony.</span><span class="sxs-lookup"><span data-stu-id="d19b0-123">*MainLayout.razor* is in the template project's *Shared* folder.</span></span> <span data-ttu-id="d19b0-124">Další informace o rozloženích <xref:blazor/layouts>naleznete v tématu .</span><span class="sxs-lookup"><span data-stu-id="d19b0-124">For more information on layouts, see <xref:blazor/layouts>.</span></span>

<span data-ttu-id="d19b0-125">Na komponentu lze použít více šablon tras.</span><span class="sxs-lookup"><span data-stu-id="d19b0-125">Multiple route templates can be applied to a component.</span></span> <span data-ttu-id="d19b0-126">Následující složka reaguje na požadavky `/BlazorRoute` `/DifferentBlazorRoute`a:</span><span class="sxs-lookup"><span data-stu-id="d19b0-126">The following component responds to requests for `/BlazorRoute` and `/DifferentBlazorRoute`:</span></span>

```razor
@page "/BlazorRoute"
@page "/DifferentBlazorRoute"

<h1>Blazor routing</h1>
```

> [!IMPORTANT]
> <span data-ttu-id="d19b0-127">Aby se adresy URL správně vyřešily, `<base>` musí aplikace do svého *souboru wwwroot/index.html* (Blazor WebAssembly) nebo *stránky/_Host.cshtml* (Blazor Server) obsahovat značku se základní cestou aplikace zadanou v atributu `href` (`<base href="/">`).</span><span class="sxs-lookup"><span data-stu-id="d19b0-127">For URLs to resolve correctly, the app must include a `<base>` tag in its *wwwroot/index.html* file (Blazor WebAssembly) or *Pages/_Host.cshtml* file (Blazor Server) with the app base path specified in the `href` attribute (`<base href="/">`).</span></span> <span data-ttu-id="d19b0-128">Další informace naleznete v tématu <xref:host-and-deploy/blazor/index#app-base-path>.</span><span class="sxs-lookup"><span data-stu-id="d19b0-128">For more information, see <xref:host-and-deploy/blazor/index#app-base-path>.</span></span>

## <a name="provide-custom-content-when-content-isnt-found"></a><span data-ttu-id="d19b0-129">Poskytnutí vlastního obsahu v případě, že obsah není nalezen</span><span class="sxs-lookup"><span data-stu-id="d19b0-129">Provide custom content when content isn't found</span></span>

<span data-ttu-id="d19b0-130">Komponenta `Router` umožňuje aplikaci určit vlastní obsah, pokud obsah není nalezen pro požadovanou trasu.</span><span class="sxs-lookup"><span data-stu-id="d19b0-130">The `Router` component allows the app to specify custom content if content isn't found for the requested route.</span></span>

<span data-ttu-id="d19b0-131">V souboru *App.razor* nastavte vlastní `NotFound` obsah v `Router` parametru šablony komponenty:</span><span class="sxs-lookup"><span data-stu-id="d19b0-131">In the *App.razor* file, set custom content in the `NotFound` template parameter of the `Router` component:</span></span>

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

<span data-ttu-id="d19b0-132">Obsah `<NotFound>` značek může obsahovat libovolné položky, například jiné interaktivní součásti.</span><span class="sxs-lookup"><span data-stu-id="d19b0-132">The content of `<NotFound>` tags can include arbitrary items, such as other interactive components.</span></span> <span data-ttu-id="d19b0-133">Pokud chcete použít `NotFound` výchozí rozložení <xref:blazor/layouts>obsahu, přečtěte si další informace o použití.</span><span class="sxs-lookup"><span data-stu-id="d19b0-133">To apply a default layout to `NotFound` content, see <xref:blazor/layouts>.</span></span>

## <a name="route-to-components-from-multiple-assemblies"></a><span data-ttu-id="d19b0-134">Směrovat na součásti z více sestav</span><span class="sxs-lookup"><span data-stu-id="d19b0-134">Route to components from multiple assemblies</span></span>

<span data-ttu-id="d19b0-135">`AdditionalAssemblies` Parametr slouží k určení dalších `Router` sestav pro komponentu, která je třeba vzít v úvahu při hledání směrovatelných součástí.</span><span class="sxs-lookup"><span data-stu-id="d19b0-135">Use the `AdditionalAssemblies` parameter to specify additional assemblies for the `Router` component to consider when searching for routable components.</span></span> <span data-ttu-id="d19b0-136">Zadaná sestavení jsou zvažována vedle `AppAssembly`-specifikované sestavy.</span><span class="sxs-lookup"><span data-stu-id="d19b0-136">Specified assemblies are considered in addition to the `AppAssembly`-specified assembly.</span></span> <span data-ttu-id="d19b0-137">V následujícím příkladu `Component1` je směrovatelná komponenta definovaná v knihovně odkazovaných tříd.</span><span class="sxs-lookup"><span data-stu-id="d19b0-137">In the following example, `Component1` is a routable component defined in a referenced class library.</span></span> <span data-ttu-id="d19b0-138">Následující `AdditionalAssemblies` příklad má za `Component1`následek podporu směrování pro :</span><span class="sxs-lookup"><span data-stu-id="d19b0-138">The following `AdditionalAssemblies` example results in routing support for `Component1`:</span></span>

```razor
<Router
    AppAssembly="typeof(Program).Assembly"
    AdditionalAssemblies="new[] { typeof(Component1).Assembly }">
    ...
</Router>
```

## <a name="route-parameters"></a><span data-ttu-id="d19b0-139">Parametry trasy</span><span class="sxs-lookup"><span data-stu-id="d19b0-139">Route parameters</span></span>

<span data-ttu-id="d19b0-140">Směrovač používá parametry trasy k naplnění odpovídajících parametrů komponenty se stejným názvem (malá a velká písmena):</span><span class="sxs-lookup"><span data-stu-id="d19b0-140">The router uses route parameters to populate the corresponding component parameters with the same name (case insensitive):</span></span>

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

<span data-ttu-id="d19b0-141">Volitelné parametry nejsou podporovány.</span><span class="sxs-lookup"><span data-stu-id="d19b0-141">Optional parameters aren't supported.</span></span> <span data-ttu-id="d19b0-142">V `@page` předchozím příkladu se používají dvě direktivy.</span><span class="sxs-lookup"><span data-stu-id="d19b0-142">Two `@page` directives are applied in the previous example.</span></span> <span data-ttu-id="d19b0-143">První umožňuje navigaci do komponenty bez parametru.</span><span class="sxs-lookup"><span data-stu-id="d19b0-143">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="d19b0-144">Druhá `@page` směrnice přebírá `{text}` parametr trasy a přiřazuje hodnotu vlastnosti. `Text`</span><span class="sxs-lookup"><span data-stu-id="d19b0-144">The second `@page` directive takes the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

## <a name="route-constraints"></a><span data-ttu-id="d19b0-145">Omezení trasy</span><span class="sxs-lookup"><span data-stu-id="d19b0-145">Route constraints</span></span>

<span data-ttu-id="d19b0-146">Omezení trasy vynucuje shodu typů v segmentu trasy s komponentou.</span><span class="sxs-lookup"><span data-stu-id="d19b0-146">A route constraint enforces type matching on a route segment to a component.</span></span>

<span data-ttu-id="d19b0-147">V následujícím příkladu se `Users` trasa k komponentě shoduje pouze v případě, že:</span><span class="sxs-lookup"><span data-stu-id="d19b0-147">In the following example, the route to the `Users` component only matches if:</span></span>

* <span data-ttu-id="d19b0-148">Segment `Id` trasy je k dispozici na adrese URL požadavku.</span><span class="sxs-lookup"><span data-stu-id="d19b0-148">An `Id` route segment is present on the request URL.</span></span>
* <span data-ttu-id="d19b0-149">Segment `Id` je celé číslo`int`( ).</span><span class="sxs-lookup"><span data-stu-id="d19b0-149">The `Id` segment is an integer (`int`).</span></span>

[!code-razor[](routing/samples_snapshot/3.x/Constraint.razor?highlight=1)]

<span data-ttu-id="d19b0-150">Omezení trasy uvedená v následující tabulce jsou k dispozici.</span><span class="sxs-lookup"><span data-stu-id="d19b0-150">The route constraints shown in the following table are available.</span></span> <span data-ttu-id="d19b0-151">Omezení trasy, která odpovídají invariantní jazykové verzi, naleznete v upozornění pod tabulkou další informace.</span><span class="sxs-lookup"><span data-stu-id="d19b0-151">For the route constraints that match with the invariant culture, see the warning below the table for more information.</span></span>

| <span data-ttu-id="d19b0-152">Omezení</span><span class="sxs-lookup"><span data-stu-id="d19b0-152">Constraint</span></span> | <span data-ttu-id="d19b0-153">Příklad</span><span class="sxs-lookup"><span data-stu-id="d19b0-153">Example</span></span>           | <span data-ttu-id="d19b0-154">Příklady shod</span><span class="sxs-lookup"><span data-stu-id="d19b0-154">Example Matches</span></span>                                                                  | <span data-ttu-id="d19b0-155">Invariantní</span><span class="sxs-lookup"><span data-stu-id="d19b0-155">Invariant</span></span><br><span data-ttu-id="d19b0-156">jazyková verze</span><span class="sxs-lookup"><span data-stu-id="d19b0-156">culture</span></span><br><span data-ttu-id="d19b0-157">shoda</span><span class="sxs-lookup"><span data-stu-id="d19b0-157">matching</span></span> |
| ---------- | ----------------- | -------------------------------------------------------------------------------- | :------------------------------: |
| `bool`     | `{active:bool}`   | <span data-ttu-id="d19b0-158">`true`, `FALSE`</span><span class="sxs-lookup"><span data-stu-id="d19b0-158">`true`, `FALSE`</span></span>                                                                  | <span data-ttu-id="d19b0-159">Ne</span><span class="sxs-lookup"><span data-stu-id="d19b0-159">No</span></span>                               |
| `datetime` | `{dob:datetime}`  | <span data-ttu-id="d19b0-160">`2016-12-31`, `2016-12-31 7:32pm`</span><span class="sxs-lookup"><span data-stu-id="d19b0-160">`2016-12-31`, `2016-12-31 7:32pm`</span></span>                                                | <span data-ttu-id="d19b0-161">Ano</span><span class="sxs-lookup"><span data-stu-id="d19b0-161">Yes</span></span>                              |
| `decimal`  | `{price:decimal}` | <span data-ttu-id="d19b0-162">`49.99`, `-1,000.01`</span><span class="sxs-lookup"><span data-stu-id="d19b0-162">`49.99`, `-1,000.01`</span></span>                                                             | <span data-ttu-id="d19b0-163">Ano</span><span class="sxs-lookup"><span data-stu-id="d19b0-163">Yes</span></span>                              |
| `double`   | `{weight:double}` | <span data-ttu-id="d19b0-164">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="d19b0-164">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="d19b0-165">Ano</span><span class="sxs-lookup"><span data-stu-id="d19b0-165">Yes</span></span>                              |
| `float`    | `{weight:float}`  | <span data-ttu-id="d19b0-166">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="d19b0-166">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="d19b0-167">Ano</span><span class="sxs-lookup"><span data-stu-id="d19b0-167">Yes</span></span>                              |
| `guid`     | `{id:guid}`       | <span data-ttu-id="d19b0-168">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span><span class="sxs-lookup"><span data-stu-id="d19b0-168">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span></span> | <span data-ttu-id="d19b0-169">Ne</span><span class="sxs-lookup"><span data-stu-id="d19b0-169">No</span></span>                               |
| `int`      | `{id:int}`        | <span data-ttu-id="d19b0-170">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="d19b0-170">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="d19b0-171">Ano</span><span class="sxs-lookup"><span data-stu-id="d19b0-171">Yes</span></span>                              |
| `long`     | `{ticks:long}`    | <span data-ttu-id="d19b0-172">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="d19b0-172">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="d19b0-173">Ano</span><span class="sxs-lookup"><span data-stu-id="d19b0-173">Yes</span></span>                              |

> [!WARNING]
> <span data-ttu-id="d19b0-174">Omezení trasy, která ověřují adresu URL a jsou `int` `DateTime`převedena na typ CLR (například nebo ) vždy používají invariantní jazykovou verzi.</span><span class="sxs-lookup"><span data-stu-id="d19b0-174">Route constraints that verify the URL and are converted to a CLR type (such as `int` or `DateTime`) always use the invariant culture.</span></span> <span data-ttu-id="d19b0-175">Tato omezení předpokládají, že adresu URL je nelokalizovatelné.</span><span class="sxs-lookup"><span data-stu-id="d19b0-175">These constraints assume that the URL is non-localizable.</span></span>

### <a name="routing-with-urls-that-contain-dots"></a><span data-ttu-id="d19b0-176">Směrování pomocí adres URL obsahujících tečky</span><span class="sxs-lookup"><span data-stu-id="d19b0-176">Routing with URLs that contain dots</span></span>

<span data-ttu-id="d19b0-177">V aplikacích Blazor Server je `/` výchozí trasa`@page "/"`v *_Host.cshtml* ( ).</span><span class="sxs-lookup"><span data-stu-id="d19b0-177">In Blazor Server apps, the default route in *_Host.cshtml* is `/` (`@page "/"`).</span></span> <span data-ttu-id="d19b0-178">Adresa URL požadavku, která`.`obsahuje tečku ( ), není ve výchozím směru spárována, protože se zobrazí adresa URL, která požaduje soubor.</span><span class="sxs-lookup"><span data-stu-id="d19b0-178">A request URL that contains a dot (`.`) isn't matched by the default route because the URL appears to request a file.</span></span> <span data-ttu-id="d19b0-179">Aplikace Blazor vrátí odpověď *404 - Not Found* pro statický soubor, který neexistuje.</span><span class="sxs-lookup"><span data-stu-id="d19b0-179">A Blazor app returns a *404 - Not Found* response for a static file that doesn't exist.</span></span> <span data-ttu-id="d19b0-180">Chcete-li použít trasy, které obsahují tečku, nakonfigurujte *_Host.cshtml* pomocí následující šablony trasy:</span><span class="sxs-lookup"><span data-stu-id="d19b0-180">To use routes that contain a dot, configure *_Host.cshtml* with the following route template:</span></span>

```cshtml
@page "/{**path}"
```

<span data-ttu-id="d19b0-181">Šablona `"/{**path}"` obsahuje:</span><span class="sxs-lookup"><span data-stu-id="d19b0-181">The `"/{**path}"` template includes:</span></span>

* <span data-ttu-id="d19b0-182">Syntaxe *catch-all* s dvojitou hvězdičkou (`**`) pro zachycení cesty přes`/`hranice více složek bez kódování lomítka ( ).</span><span class="sxs-lookup"><span data-stu-id="d19b0-182">Double-asterisk *catch-all* syntax (`**`) to capture the path across multiple folder boundaries without encoding forward slashes (`/`).</span></span>
* <span data-ttu-id="d19b0-183">`path`název parametru trasy.</span><span class="sxs-lookup"><span data-stu-id="d19b0-183">`path` route parameter name.</span></span>

> [!NOTE]
> <span data-ttu-id="d19b0-184">*Syntaxe parametrů catch-all* `*`/`**`( ) **není** podporována v komponentách Razor (*.razor*).</span><span class="sxs-lookup"><span data-stu-id="d19b0-184">*Catch-all* parameter syntax (`*`/`**`) is **not** supported in Razor components (*.razor*).</span></span>

<span data-ttu-id="d19b0-185">Další informace naleznete v tématu <xref:fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="d19b0-185">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="navlink-component"></a><span data-ttu-id="d19b0-186">Komponenta NavLink</span><span class="sxs-lookup"><span data-stu-id="d19b0-186">NavLink component</span></span>

<span data-ttu-id="d19b0-187">Při `NavLink` vytváření navigačních odkazů použijte`<a>`komponentu místo elementů hypertextového odkazu HTML ( ).</span><span class="sxs-lookup"><span data-stu-id="d19b0-187">Use a `NavLink` component in place of HTML hyperlink elements (`<a>`) when creating navigation links.</span></span> <span data-ttu-id="d19b0-188">Komponenta `NavLink` se chová `<a>` jako prvek, s výjimkou `active` přepíná třídu `href` CSS na základě toho, zda odpovídá aktuální adrese URL.</span><span class="sxs-lookup"><span data-stu-id="d19b0-188">A `NavLink` component behaves like an `<a>` element, except it toggles an `active` CSS class based on whether its `href` matches the current URL.</span></span> <span data-ttu-id="d19b0-189">Třída `active` pomáhá uživateli pochopit, která stránka je aktivní stránka mezi zobrazené navigační odkazy.</span><span class="sxs-lookup"><span data-stu-id="d19b0-189">The `active` class helps a user understand which page is the active page among the navigation links displayed.</span></span>

<span data-ttu-id="d19b0-190">Následující `NavMenu` komponenta vytvoří navigační panel [Bootstrap,](https://getbootstrap.com/docs/) `NavLink` který ukazuje, jak používat součásti:</span><span class="sxs-lookup"><span data-stu-id="d19b0-190">The following `NavMenu` component creates a [Bootstrap](https://getbootstrap.com/docs/) navigation bar that demonstrates how to use `NavLink` components:</span></span>

[!code-razor[](routing/samples_snapshot/3.x/NavMenu.razor?highlight=4,9)]

<span data-ttu-id="d19b0-191">Atributu `NavLinkMatch` prvku můžete přiřadit `Match` dvě možnosti: `<NavLink>`</span><span class="sxs-lookup"><span data-stu-id="d19b0-191">There are two `NavLinkMatch` options that you can assign to the `Match` attribute of the `<NavLink>` element:</span></span>

* <span data-ttu-id="d19b0-192">`NavLinkMatch.All`&ndash; Je `NavLink` aktivní, pokud odpovídá celé aktuální adrese URL.</span><span class="sxs-lookup"><span data-stu-id="d19b0-192">`NavLinkMatch.All` &ndash; The `NavLink` is active when it matches the entire current URL.</span></span>
* <span data-ttu-id="d19b0-193">`NavLinkMatch.Prefix`(*výchozí*) &ndash; Je `NavLink` aktivní, pokud odpovídá libovolné předponě aktuální adresy URL.</span><span class="sxs-lookup"><span data-stu-id="d19b0-193">`NavLinkMatch.Prefix` (*default*) &ndash; The `NavLink` is active when it matches any prefix of the current URL.</span></span>

<span data-ttu-id="d19b0-194">V předchozím příkladu home `NavLink` `href=""` odpovídá domácí adrese URL `active` a obdrží pouze třídu CSS na výchozí `https://localhost:5001/`adrese URL základní cesty aplikace (například).</span><span class="sxs-lookup"><span data-stu-id="d19b0-194">In the preceding example, the Home `NavLink` `href=""` matches the home URL and only receives the `active` CSS class at the app's default base path URL (for example, `https://localhost:5001/`).</span></span> <span data-ttu-id="d19b0-195">Druhý `NavLink` obdrží `active` třídu, když uživatel navštíví `MyComponent` libovolnou adresu URL `https://localhost:5001/MyComponent` `https://localhost:5001/MyComponent/AnotherSegment`s předponou (například a ).</span><span class="sxs-lookup"><span data-stu-id="d19b0-195">The second `NavLink` receives the `active` class when the user visits any URL with a `MyComponent` prefix (for example, `https://localhost:5001/MyComponent` and `https://localhost:5001/MyComponent/AnotherSegment`).</span></span>

<span data-ttu-id="d19b0-196">Další `NavLink` atributy komponenty jsou předány do vykreslené značky ukotvení.</span><span class="sxs-lookup"><span data-stu-id="d19b0-196">Additional `NavLink` component attributes are passed through to the rendered anchor tag.</span></span> <span data-ttu-id="d19b0-197">V následujícím příkladu `NavLink` komponenta `target` obsahuje atribut:</span><span class="sxs-lookup"><span data-stu-id="d19b0-197">In the following example, the `NavLink` component includes the `target` attribute:</span></span>

```razor
<NavLink href="my-page" target="_blank">My page</NavLink>
```

<span data-ttu-id="d19b0-198">Vykreslují se následující značky HTML:</span><span class="sxs-lookup"><span data-stu-id="d19b0-198">The following HTML markup is rendered:</span></span>

```html
<a href="my-page" target="_blank" rel="noopener noreferrer">My page</a>
```

## <a name="uri-and-navigation-state-helpers"></a><span data-ttu-id="d19b0-199">Pomocníci stavu uri a navigace</span><span class="sxs-lookup"><span data-stu-id="d19b0-199">URI and navigation state helpers</span></span>

<span data-ttu-id="d19b0-200">Slouží <xref:Microsoft.AspNetCore.Components.NavigationManager> k práci s identifikátory URI a navigace v kódu Jazyka C#.</span><span class="sxs-lookup"><span data-stu-id="d19b0-200">Use <xref:Microsoft.AspNetCore.Components.NavigationManager> to work with URIs and navigation in C# code.</span></span> <span data-ttu-id="d19b0-201">`NavigationManager`obsahuje událost a metody uvedené v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="d19b0-201">`NavigationManager` provides the event and methods shown in the following table.</span></span>

| <span data-ttu-id="d19b0-202">Člen</span><span class="sxs-lookup"><span data-stu-id="d19b0-202">Member</span></span> | <span data-ttu-id="d19b0-203">Popis</span><span class="sxs-lookup"><span data-stu-id="d19b0-203">Description</span></span> |
| ------ | ----------- |
| <span data-ttu-id="d19b0-204">Uri</span><span class="sxs-lookup"><span data-stu-id="d19b0-204">Uri</span></span> | <span data-ttu-id="d19b0-205">Získá aktuální absolutní identifikátor URI.</span><span class="sxs-lookup"><span data-stu-id="d19b0-205">Gets the current absolute URI.</span></span> |
| <span data-ttu-id="d19b0-206">Baseuri</span><span class="sxs-lookup"><span data-stu-id="d19b0-206">BaseUri</span></span> | <span data-ttu-id="d19b0-207">Získá základní identifikátor URI (s koncovým lomítkem), který lze předřadit k relativním cestám IDENTIFIKÁTORURI k vytvoření absolutního identifikátoru URI.</span><span class="sxs-lookup"><span data-stu-id="d19b0-207">Gets the base URI (with a trailing slash) that can be prepended to relative URI paths to produce an absolute URI.</span></span> <span data-ttu-id="d19b0-208">`BaseUri` Obvykle odpovídá atributu `href` prvku dokumentu `<base>` v *wwwroot/index.html* (WebAssembly)Blazor nebo *Pages/_Host.cshtml* (Server).Blazor</span><span class="sxs-lookup"><span data-stu-id="d19b0-208">Typically, `BaseUri` corresponds to the `href` attribute on the document's `<base>` element in *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server).</span></span> |
| <span data-ttu-id="d19b0-209">Přejít</span><span class="sxs-lookup"><span data-stu-id="d19b0-209">NavigateTo</span></span> | <span data-ttu-id="d19b0-210">Přejde na zadaný identifikátor URI.</span><span class="sxs-lookup"><span data-stu-id="d19b0-210">Navigates to the specified URI.</span></span> <span data-ttu-id="d19b0-211">Pokud `forceLoad` `true`je:</span><span class="sxs-lookup"><span data-stu-id="d19b0-211">If `forceLoad` is `true`:</span></span><ul><li><span data-ttu-id="d19b0-212">Směrování na straně klienta je vynecháno.</span><span class="sxs-lookup"><span data-stu-id="d19b0-212">Client-side routing is bypassed.</span></span></li><li><span data-ttu-id="d19b0-213">Prohlížeč je nucen načíst novou stránku ze serveru bez ohledu na to, zda je identifikátor URI normálně zpracován směrovačem na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="d19b0-213">The browser is forced to load the new page from the server, whether or not the URI is normally handled by the client-side router.</span></span></li></ul> |
| <span data-ttu-id="d19b0-214">UmístěníZměněno</span><span class="sxs-lookup"><span data-stu-id="d19b0-214">LocationChanged</span></span> | <span data-ttu-id="d19b0-215">Událost, která se aktivuje při změně umístění navigace.</span><span class="sxs-lookup"><span data-stu-id="d19b0-215">An event that fires when the navigation location has changed.</span></span> |
| <span data-ttu-id="d19b0-216">ToAbsoluteUri</span><span class="sxs-lookup"><span data-stu-id="d19b0-216">ToAbsoluteUri</span></span> | <span data-ttu-id="d19b0-217">Převede relativní identifikátor URI na absolutní identifikátor URI.</span><span class="sxs-lookup"><span data-stu-id="d19b0-217">Converts a relative URI into an absolute URI.</span></span> |
| <span data-ttu-id="d19b0-218"><span style="word-break:normal;word-wrap:normal">ToBaseRelativePath</span></span><span class="sxs-lookup"><span data-stu-id="d19b0-218"><span style="word-break:normal;word-wrap:normal">ToBaseRelativePath</span></span></span> | <span data-ttu-id="d19b0-219">Vzhledem k tomu, základní URI (například URI dříve vrácena `GetBaseUri`) převede absolutní IDENTIFIKÁTOR URI na identifikátor URI vzhledem k základní předponě URI.</span><span class="sxs-lookup"><span data-stu-id="d19b0-219">Given a base URI (for example, a URI previously returned by `GetBaseUri`), converts an absolute URI into a URI relative to the base URI prefix.</span></span> |

<span data-ttu-id="d19b0-220">Následující komponenta přejde na `Counter` komponentu aplikace, když je tlačítko vybrané:</span><span class="sxs-lookup"><span data-stu-id="d19b0-220">The following component navigates to the app's `Counter` component when the button is selected:</span></span>

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

<span data-ttu-id="d19b0-221">Následující součást zpracovává událost změněné umístění.</span><span class="sxs-lookup"><span data-stu-id="d19b0-221">The following component handles a location changed event.</span></span> <span data-ttu-id="d19b0-222">Metoda `HandleLocationChanged` je unhooked `Dispose` při volání rámci.</span><span class="sxs-lookup"><span data-stu-id="d19b0-222">The `HandleLocationChanged` method is unhooked when `Dispose` is called by the framework.</span></span> <span data-ttu-id="d19b0-223">Uvolnění maze metoda umožňuje uvolnění paměti komponenty.</span><span class="sxs-lookup"><span data-stu-id="d19b0-223">Unhooking the method permits garbage collection of the component.</span></span>

```razor
@implement IDisposable
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

<span data-ttu-id="d19b0-224"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs>poskytuje následující informace o události:</span><span class="sxs-lookup"><span data-stu-id="d19b0-224"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs> provides the following information about the event:</span></span>

* <span data-ttu-id="d19b0-225"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>&ndash; Adresa URL nového umístění.</span><span class="sxs-lookup"><span data-stu-id="d19b0-225"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location> &ndash; The URL of the new location.</span></span>
* <span data-ttu-id="d19b0-226"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>&ndash; Pokud `true` Blazor , zachytil navigaci z prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="d19b0-226"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted> &ndash; If `true`, Blazor intercepted the navigation from the browser.</span></span> <span data-ttu-id="d19b0-227">Pokud `false`, [NavigationManager.NavigateTo](xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A) způsobil navigaci dojít.</span><span class="sxs-lookup"><span data-stu-id="d19b0-227">If `false`, [NavigationManager.NavigateTo](xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A) caused the navigation to occur.</span></span>

<span data-ttu-id="d19b0-228">Další informace o likvidaci <xref:blazor/lifecycle#component-disposal-with-idisposable>součástí naleznete v tématu .</span><span class="sxs-lookup"><span data-stu-id="d19b0-228">For more information on component disposal, see <xref:blazor/lifecycle#component-disposal-with-idisposable>.</span></span>
