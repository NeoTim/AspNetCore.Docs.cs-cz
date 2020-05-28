---
<span data-ttu-id="c18d9-101">title: ' ASP.NET Core Blazor Routing ' autora: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c18d9-101">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c18d9-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c18d9-102">'Blazor'</span></span>
- <span data-ttu-id="c18d9-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c18d9-103">'Identity'</span></span>
- <span data-ttu-id="c18d9-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c18d9-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="c18d9-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c18d9-105">'Razor'</span></span>
- <span data-ttu-id="c18d9-106">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c18d9-106">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-blazor-routing"></a><span data-ttu-id="c18d9-107">BlazorSměrování ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c18d9-107">ASP.NET Core Blazor routing</span></span>

<span data-ttu-id="c18d9-108">Od [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="c18d9-108">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="c18d9-109">Naučte se směrovat požadavky a jak používat <xref:Microsoft.AspNetCore.Components.Routing.NavLink> komponentu k vytváření navigačních odkazů v Blazor aplikacích.</span><span class="sxs-lookup"><span data-stu-id="c18d9-109">Learn how to route requests and how to use the <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component to create navigation links in Blazor apps.</span></span>

## <a name="aspnet-core-endpoint-routing-integration"></a><span data-ttu-id="c18d9-110">Integrace směrování ASP.NET Core Endpoint</span><span class="sxs-lookup"><span data-stu-id="c18d9-110">ASP.NET Core endpoint routing integration</span></span>

Blazor<span data-ttu-id="c18d9-111">Server je integrovaný do [Směrování koncového bodu ASP.NET Core](xref:fundamentals/routing).</span><span class="sxs-lookup"><span data-stu-id="c18d9-111"> Server is integrated into [ASP.NET Core Endpoint Routing](xref:fundamentals/routing).</span></span> <span data-ttu-id="c18d9-112">Aplikace ASP.NET Core je nakonfigurovaná tak, aby přijímala příchozí připojení pro interaktivní komponenty <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> v `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="c18d9-112">An ASP.NET Core app is configured to accept incoming connections for interactive components with <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> in `Startup.Configure`:</span></span>

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

<span data-ttu-id="c18d9-113">Nejtypickou konfigurací je směrování všech požadavků na Razor stránku, která funguje jako hostitel serverové součásti Blazor serverové aplikace.</span><span class="sxs-lookup"><span data-stu-id="c18d9-113">The most typical configuration is to route all requests to a Razor page, which acts as the host for the server-side part of the Blazor Server app.</span></span> <span data-ttu-id="c18d9-114">Podle konvence je stránka *hostitele* obvykle pojmenována *_Host. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="c18d9-114">By convention, the *host* page is usually named *_Host.cshtml*.</span></span> <span data-ttu-id="c18d9-115">Trasa zadaná v hostitelském souboru se nazývá *záložní trasa* , protože v porovnání s trasou funguje s nízkou prioritou.</span><span class="sxs-lookup"><span data-stu-id="c18d9-115">The route specified in the host file is called a *fallback route* because it operates with a low priority in route matching.</span></span> <span data-ttu-id="c18d9-116">Záložní trasa se bere v úvahu v případě, že se jiné trasy neshodují.</span><span class="sxs-lookup"><span data-stu-id="c18d9-116">The fallback route is considered when other routes don't match.</span></span> <span data-ttu-id="c18d9-117">To umožňuje aplikaci používat jiné řadiče a stránky bez narušování Blazor aplikace serveru.</span><span class="sxs-lookup"><span data-stu-id="c18d9-117">This allows the app to use others controllers and pages without interfering with the Blazor Server app.</span></span>

## <a name="route-templates"></a><span data-ttu-id="c18d9-118">Šablony směrování</span><span class="sxs-lookup"><span data-stu-id="c18d9-118">Route templates</span></span>

<span data-ttu-id="c18d9-119">Tato <xref:Microsoft.AspNetCore.Components.Routing.Router> součást umožňuje směrování na jednotlivé komponenty se zadanou trasou.</span><span class="sxs-lookup"><span data-stu-id="c18d9-119">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component enables routing to each component with a specified route.</span></span> <span data-ttu-id="c18d9-120"><xref:Microsoft.AspNetCore.Components.Routing.Router>Komponenta se zobrazí v souboru *App. Razor* :</span><span class="sxs-lookup"><span data-stu-id="c18d9-120">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component appears in the *App.razor* file:</span></span>

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

<span data-ttu-id="c18d9-121">Je-li soubor *. Razor* s `@page` direktivou kompilován, je k dispozici vygenerovaná třída, která <xref:Microsoft.AspNetCore.Components.RouteAttribute> Určuje šablonu trasy.</span><span class="sxs-lookup"><span data-stu-id="c18d9-121">When a *.razor* file with an `@page` directive is compiled, the generated class is provided a <xref:Microsoft.AspNetCore.Components.RouteAttribute> specifying the route template.</span></span>

<span data-ttu-id="c18d9-122">Za běhu <xref:Microsoft.AspNetCore.Components.RouteView> komponenty:</span><span class="sxs-lookup"><span data-stu-id="c18d9-122">At runtime, the <xref:Microsoft.AspNetCore.Components.RouteView> component:</span></span>

* <span data-ttu-id="c18d9-123">Přijímá <xref:Microsoft.AspNetCore.Components.RouteData> od <xref:Microsoft.AspNetCore.Components.Routing.Router> spolu s požadovanými parametry.</span><span class="sxs-lookup"><span data-stu-id="c18d9-123">Receives the <xref:Microsoft.AspNetCore.Components.RouteData> from the <xref:Microsoft.AspNetCore.Components.Routing.Router> along with any desired parameters.</span></span>
* <span data-ttu-id="c18d9-124">Vykreslí určenou komponentu pomocí jejího rozložení (nebo volitelného výchozího rozložení) pomocí zadaných parametrů.</span><span class="sxs-lookup"><span data-stu-id="c18d9-124">Renders the specified component with its layout (or an optional default layout) using the specified parameters.</span></span>

<span data-ttu-id="c18d9-125">Volitelně můžete zadat <xref:Microsoft.AspNetCore.Components.RouteView.DefaultLayout> parametr s třídou rozložení, který se má použít pro součásti, které neurčují rozložení.</span><span class="sxs-lookup"><span data-stu-id="c18d9-125">You can optionally specify a <xref:Microsoft.AspNetCore.Components.RouteView.DefaultLayout> parameter with a layout class to use for components that don't specify a layout.</span></span> <span data-ttu-id="c18d9-126">Výchozí Blazor šablony určují `MainLayout` komponentu.</span><span class="sxs-lookup"><span data-stu-id="c18d9-126">The default Blazor templates specify the `MainLayout` component.</span></span> <span data-ttu-id="c18d9-127">*MainLayout. Razor* se nachází ve *sdílené* složce projektu šablony.</span><span class="sxs-lookup"><span data-stu-id="c18d9-127">*MainLayout.razor* is in the template project's *Shared* folder.</span></span> <span data-ttu-id="c18d9-128">Další informace o rozložení najdete v tématu <xref:blazor/layouts> .</span><span class="sxs-lookup"><span data-stu-id="c18d9-128">For more information on layouts, see <xref:blazor/layouts>.</span></span>

<span data-ttu-id="c18d9-129">Pro komponentu lze použít více šablon směrování.</span><span class="sxs-lookup"><span data-stu-id="c18d9-129">Multiple route templates can be applied to a component.</span></span> <span data-ttu-id="c18d9-130">Následující komponenta reaguje na požadavky pro `/BlazorRoute` a `/DifferentBlazorRoute` :</span><span class="sxs-lookup"><span data-stu-id="c18d9-130">The following component responds to requests for `/BlazorRoute` and `/DifferentBlazorRoute`:</span></span>

```razor
@page "/BlazorRoute"
@page "/DifferentBlazorRoute"

<h1>Blazor routing</h1>
```

> [!IMPORTANT]
> <span data-ttu-id="c18d9-131">Aby adresy URL byly správně přeloženy, musí aplikace obsahovat `<base>` značku v souboru *wwwroot/index.html* ( Blazor WebAssembly) nebo *Pages/_Host. cshtml* ( Blazor Server) se základní cestou aplikace zadanou v `href` atributu ( `<base href="/">` ).</span><span class="sxs-lookup"><span data-stu-id="c18d9-131">For URLs to resolve correctly, the app must include a `<base>` tag in its *wwwroot/index.html* file (Blazor WebAssembly) or *Pages/_Host.cshtml* file (Blazor Server) with the app base path specified in the `href` attribute (`<base href="/">`).</span></span> <span data-ttu-id="c18d9-132">Další informace naleznete v tématu <xref:host-and-deploy/blazor/index#app-base-path>.</span><span class="sxs-lookup"><span data-stu-id="c18d9-132">For more information, see <xref:host-and-deploy/blazor/index#app-base-path>.</span></span>

## <a name="provide-custom-content-when-content-isnt-found"></a><span data-ttu-id="c18d9-133">Poskytnutí vlastního obsahu, když se nenalezne obsah</span><span class="sxs-lookup"><span data-stu-id="c18d9-133">Provide custom content when content isn't found</span></span>

<span data-ttu-id="c18d9-134"><xref:Microsoft.AspNetCore.Components.Routing.Router>Komponenta umožňuje aplikaci zadat vlastní obsah, pokud se pro požadovanou trasu nenajde obsah.</span><span class="sxs-lookup"><span data-stu-id="c18d9-134">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component allows the app to specify custom content if content isn't found for the requested route.</span></span>

<span data-ttu-id="c18d9-135">V souboru *App. Razor* nastavte vlastní obsah v <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> parametru šablony <xref:Microsoft.AspNetCore.Components.Routing.Router> součásti:</span><span class="sxs-lookup"><span data-stu-id="c18d9-135">In the *App.razor* file, set custom content in the <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> template parameter of the <xref:Microsoft.AspNetCore.Components.Routing.Router> component:</span></span>

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

<span data-ttu-id="c18d9-136">Obsah `<NotFound>` značek může zahrnovat libovolné položky, jako jsou například jiné interaktivní součásti.</span><span class="sxs-lookup"><span data-stu-id="c18d9-136">The content of `<NotFound>` tags can include arbitrary items, such as other interactive components.</span></span> <span data-ttu-id="c18d9-137">Chcete-li použít výchozí rozložení <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> obsahu, přečtěte si téma <xref:blazor/layouts> .</span><span class="sxs-lookup"><span data-stu-id="c18d9-137">To apply a default layout to <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> content, see <xref:blazor/layouts>.</span></span>

## <a name="route-to-components-from-multiple-assemblies"></a><span data-ttu-id="c18d9-138">Směrování na součásti z více sestavení</span><span class="sxs-lookup"><span data-stu-id="c18d9-138">Route to components from multiple assemblies</span></span>

<span data-ttu-id="c18d9-139">Použijte <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> parametr k určení dalších sestavení, <xref:Microsoft.AspNetCore.Components.Routing.Router> která má součást při hledání směrovatelných komponent zvážit.</span><span class="sxs-lookup"><span data-stu-id="c18d9-139">Use the <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> parameter to specify additional assemblies for the <xref:Microsoft.AspNetCore.Components.Routing.Router> component to consider when searching for routable components.</span></span> <span data-ttu-id="c18d9-140">Zadaná sestavení jsou kromě zadaného sestavení považována za `AppAssembly` .</span><span class="sxs-lookup"><span data-stu-id="c18d9-140">Specified assemblies are considered in addition to the `AppAssembly`-specified assembly.</span></span> <span data-ttu-id="c18d9-141">V následujícím příkladu `Component1` je směrovatelný komponenta definovaná v odkazované knihovně tříd.</span><span class="sxs-lookup"><span data-stu-id="c18d9-141">In the following example, `Component1` is a routable component defined in a referenced class library.</span></span> <span data-ttu-id="c18d9-142">Následující <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> příklad vede k podpoře směrování pro `Component1` :</span><span class="sxs-lookup"><span data-stu-id="c18d9-142">The following <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> example results in routing support for `Component1`:</span></span>

```razor
<Router
    AppAssembly="typeof(Program).Assembly"
    AdditionalAssemblies="new[] { typeof(Component1).Assembly }">
    ...
</Router>
```

## <a name="route-parameters"></a><span data-ttu-id="c18d9-143">Parametry směrování</span><span class="sxs-lookup"><span data-stu-id="c18d9-143">Route parameters</span></span>

<span data-ttu-id="c18d9-144">Směrovač používá parametry směrování k naplnění odpovídajících parametrů komponenty se stejným názvem (bez rozlišení velkých a malých písmen):</span><span class="sxs-lookup"><span data-stu-id="c18d9-144">The router uses route parameters to populate the corresponding component parameters with the same name (case insensitive):</span></span>

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

<span data-ttu-id="c18d9-145">Volitelné parametry nejsou podporovány.</span><span class="sxs-lookup"><span data-stu-id="c18d9-145">Optional parameters aren't supported.</span></span> <span data-ttu-id="c18d9-146">`@page`V předchozím příkladu jsou aplikovány dvě direktivy.</span><span class="sxs-lookup"><span data-stu-id="c18d9-146">Two `@page` directives are applied in the previous example.</span></span> <span data-ttu-id="c18d9-147">První umožňuje navigaci na součást bez parametru.</span><span class="sxs-lookup"><span data-stu-id="c18d9-147">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="c18d9-148">Druhá `@page` direktiva převezme `{text}` parametr Route a přiřadí hodnotu `Text` Vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="c18d9-148">The second `@page` directive takes the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

## <a name="route-constraints"></a><span data-ttu-id="c18d9-149">Omezení trasy</span><span class="sxs-lookup"><span data-stu-id="c18d9-149">Route constraints</span></span>

<span data-ttu-id="c18d9-150">Omezení trasy vynutilo typ shodný s typem pro segment směrování do součásti.</span><span class="sxs-lookup"><span data-stu-id="c18d9-150">A route constraint enforces type matching on a route segment to a component.</span></span>

<span data-ttu-id="c18d9-151">V následujícím příkladu trasa k `Users` součásti odpovídá pouze v případě, že:</span><span class="sxs-lookup"><span data-stu-id="c18d9-151">In the following example, the route to the `Users` component only matches if:</span></span>

* <span data-ttu-id="c18d9-152">`Id`V adrese URL požadavku je přítomen segment směrování.</span><span class="sxs-lookup"><span data-stu-id="c18d9-152">An `Id` route segment is present on the request URL.</span></span>
* <span data-ttu-id="c18d9-153">`Id`Segment je celé číslo ( `int` ).</span><span class="sxs-lookup"><span data-stu-id="c18d9-153">The `Id` segment is an integer (`int`).</span></span>

[!code-razor[](routing/samples_snapshot/3.x/Constraint.razor?highlight=1)]

<span data-ttu-id="c18d9-154">K dispozici jsou omezení tras uvedená v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="c18d9-154">The route constraints shown in the following table are available.</span></span> <span data-ttu-id="c18d9-155">Pro omezení trasy, která se shodují s invariantní jazykovou verzí, se podívejte na upozornění pod tabulkou, kde najdete další informace.</span><span class="sxs-lookup"><span data-stu-id="c18d9-155">For the route constraints that match with the invariant culture, see the warning below the table for more information.</span></span>

| <span data-ttu-id="c18d9-156">Jedinečn</span><span class="sxs-lookup"><span data-stu-id="c18d9-156">Constraint</span></span> | <span data-ttu-id="c18d9-157">Příklad</span><span class="sxs-lookup"><span data-stu-id="c18d9-157">Example</span></span>           | <span data-ttu-id="c18d9-158">Příklady shody</span><span class="sxs-lookup"><span data-stu-id="c18d9-158">Example Matches</span></span>                                                                  | <span data-ttu-id="c18d9-159">Invariantní</span><span class="sxs-lookup"><span data-stu-id="c18d9-159">Invariant</span></span><br><span data-ttu-id="c18d9-160">jazyková verze</span><span class="sxs-lookup"><span data-stu-id="c18d9-160">culture</span></span><br><span data-ttu-id="c18d9-161">shoda</span><span class="sxs-lookup"><span data-stu-id="c18d9-161">matching</span></span> |
| ---
<span data-ttu-id="c18d9-162">title: ' ASP.NET Core Blazor Routing ' autora: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c18d9-162">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c18d9-163">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c18d9-163">'Blazor'</span></span>
- <span data-ttu-id="c18d9-164">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c18d9-164">'Identity'</span></span>
- <span data-ttu-id="c18d9-165">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c18d9-165">'Let's Encrypt'</span></span>
- <span data-ttu-id="c18d9-166">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c18d9-166">'Razor'</span></span>
- <span data-ttu-id="c18d9-167">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c18d9-167">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c18d9-168">title: ' ASP.NET Core Blazor Routing ' autora: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c18d9-168">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c18d9-169">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c18d9-169">'Blazor'</span></span>
- <span data-ttu-id="c18d9-170">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c18d9-170">'Identity'</span></span>
- <span data-ttu-id="c18d9-171">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c18d9-171">'Let's Encrypt'</span></span>
- <span data-ttu-id="c18d9-172">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c18d9-172">'Razor'</span></span>
- <span data-ttu-id="c18d9-173">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c18d9-173">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c18d9-174">title: ' ASP.NET Core Blazor Routing ' autora: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c18d9-174">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c18d9-175">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c18d9-175">'Blazor'</span></span>
- <span data-ttu-id="c18d9-176">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c18d9-176">'Identity'</span></span>
- <span data-ttu-id="c18d9-177">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c18d9-177">'Let's Encrypt'</span></span>
- <span data-ttu-id="c18d9-178">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c18d9-178">'Razor'</span></span>
- <span data-ttu-id="c18d9-179">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c18d9-179">'SignalR' uid:</span></span> 

<span data-ttu-id="c18d9-180">----- | ---Název: ' ASP.NET Core Blazor Směrování ' Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c18d9-180">----- | --- title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c18d9-181">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c18d9-181">'Blazor'</span></span>
- <span data-ttu-id="c18d9-182">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c18d9-182">'Identity'</span></span>
- <span data-ttu-id="c18d9-183">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c18d9-183">'Let's Encrypt'</span></span>
- <span data-ttu-id="c18d9-184">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c18d9-184">'Razor'</span></span>
- <span data-ttu-id="c18d9-185">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c18d9-185">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c18d9-186">title: ' ASP.NET Core Blazor Routing ' autora: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c18d9-186">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c18d9-187">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c18d9-187">'Blazor'</span></span>
- <span data-ttu-id="c18d9-188">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c18d9-188">'Identity'</span></span>
- <span data-ttu-id="c18d9-189">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c18d9-189">'Let's Encrypt'</span></span>
- <span data-ttu-id="c18d9-190">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c18d9-190">'Razor'</span></span>
- <span data-ttu-id="c18d9-191">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c18d9-191">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c18d9-192">title: ' ASP.NET Core Blazor Routing ' autora: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c18d9-192">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c18d9-193">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c18d9-193">'Blazor'</span></span>
- <span data-ttu-id="c18d9-194">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c18d9-194">'Identity'</span></span>
- <span data-ttu-id="c18d9-195">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c18d9-195">'Let's Encrypt'</span></span>
- <span data-ttu-id="c18d9-196">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c18d9-196">'Razor'</span></span>
- <span data-ttu-id="c18d9-197">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c18d9-197">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c18d9-198">title: ' ASP.NET Core Blazor Routing ' autora: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c18d9-198">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c18d9-199">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c18d9-199">'Blazor'</span></span>
- <span data-ttu-id="c18d9-200">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c18d9-200">'Identity'</span></span>
- <span data-ttu-id="c18d9-201">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c18d9-201">'Let's Encrypt'</span></span>
- <span data-ttu-id="c18d9-202">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c18d9-202">'Razor'</span></span>
- <span data-ttu-id="c18d9-203">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c18d9-203">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c18d9-204">title: ' ASP.NET Core Blazor Routing ' autora: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c18d9-204">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c18d9-205">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c18d9-205">'Blazor'</span></span>
- <span data-ttu-id="c18d9-206">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c18d9-206">'Identity'</span></span>
- <span data-ttu-id="c18d9-207">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c18d9-207">'Let's Encrypt'</span></span>
- <span data-ttu-id="c18d9-208">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c18d9-208">'Razor'</span></span>
- <span data-ttu-id="c18d9-209">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c18d9-209">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c18d9-210">title: ' ASP.NET Core Blazor Routing ' autora: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c18d9-210">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c18d9-211">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c18d9-211">'Blazor'</span></span>
- <span data-ttu-id="c18d9-212">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c18d9-212">'Identity'</span></span>
- <span data-ttu-id="c18d9-213">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c18d9-213">'Let's Encrypt'</span></span>
- <span data-ttu-id="c18d9-214">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c18d9-214">'Razor'</span></span>
- <span data-ttu-id="c18d9-215">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c18d9-215">'SignalR' uid:</span></span> 

<span data-ttu-id="c18d9-216">--------- | ---Název: ' ASP.NET Core Blazor Směrování ' Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c18d9-216">--------- | --- title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c18d9-217">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c18d9-217">'Blazor'</span></span>
- <span data-ttu-id="c18d9-218">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c18d9-218">'Identity'</span></span>
- <span data-ttu-id="c18d9-219">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c18d9-219">'Let's Encrypt'</span></span>
- <span data-ttu-id="c18d9-220">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c18d9-220">'Razor'</span></span>
- <span data-ttu-id="c18d9-221">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c18d9-221">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c18d9-222">title: ' ASP.NET Core Blazor Routing ' autora: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c18d9-222">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c18d9-223">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c18d9-223">'Blazor'</span></span>
- <span data-ttu-id="c18d9-224">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c18d9-224">'Identity'</span></span>
- <span data-ttu-id="c18d9-225">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c18d9-225">'Let's Encrypt'</span></span>
- <span data-ttu-id="c18d9-226">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c18d9-226">'Razor'</span></span>
- <span data-ttu-id="c18d9-227">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c18d9-227">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c18d9-228">title: ' ASP.NET Core Blazor Routing ' autora: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c18d9-228">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c18d9-229">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c18d9-229">'Blazor'</span></span>
- <span data-ttu-id="c18d9-230">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c18d9-230">'Identity'</span></span>
- <span data-ttu-id="c18d9-231">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c18d9-231">'Let's Encrypt'</span></span>
- <span data-ttu-id="c18d9-232">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c18d9-232">'Razor'</span></span>
- <span data-ttu-id="c18d9-233">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c18d9-233">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c18d9-234">title: ' ASP.NET Core Blazor Routing ' autora: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c18d9-234">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c18d9-235">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c18d9-235">'Blazor'</span></span>
- <span data-ttu-id="c18d9-236">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c18d9-236">'Identity'</span></span>
- <span data-ttu-id="c18d9-237">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c18d9-237">'Let's Encrypt'</span></span>
- <span data-ttu-id="c18d9-238">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c18d9-238">'Razor'</span></span>
- <span data-ttu-id="c18d9-239">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c18d9-239">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c18d9-240">title: ' ASP.NET Core Blazor Routing ' autora: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c18d9-240">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c18d9-241">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c18d9-241">'Blazor'</span></span>
- <span data-ttu-id="c18d9-242">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c18d9-242">'Identity'</span></span>
- <span data-ttu-id="c18d9-243">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c18d9-243">'Let's Encrypt'</span></span>
- <span data-ttu-id="c18d9-244">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c18d9-244">'Razor'</span></span>
- <span data-ttu-id="c18d9-245">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c18d9-245">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c18d9-246">title: ' ASP.NET Core Blazor Routing ' autora: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c18d9-246">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c18d9-247">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c18d9-247">'Blazor'</span></span>
- <span data-ttu-id="c18d9-248">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c18d9-248">'Identity'</span></span>
- <span data-ttu-id="c18d9-249">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c18d9-249">'Let's Encrypt'</span></span>
- <span data-ttu-id="c18d9-250">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c18d9-250">'Razor'</span></span>
- <span data-ttu-id="c18d9-251">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c18d9-251">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c18d9-252">title: ' ASP.NET Core Blazor Routing ' autora: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c18d9-252">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c18d9-253">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c18d9-253">'Blazor'</span></span>
- <span data-ttu-id="c18d9-254">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c18d9-254">'Identity'</span></span>
- <span data-ttu-id="c18d9-255">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c18d9-255">'Let's Encrypt'</span></span>
- <span data-ttu-id="c18d9-256">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c18d9-256">'Razor'</span></span>
- <span data-ttu-id="c18d9-257">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c18d9-257">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c18d9-258">title: ' ASP.NET Core Blazor Routing ' autora: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c18d9-258">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c18d9-259">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c18d9-259">'Blazor'</span></span>
- <span data-ttu-id="c18d9-260">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c18d9-260">'Identity'</span></span>
- <span data-ttu-id="c18d9-261">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c18d9-261">'Let's Encrypt'</span></span>
- <span data-ttu-id="c18d9-262">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c18d9-262">'Razor'</span></span>
- <span data-ttu-id="c18d9-263">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c18d9-263">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c18d9-264">title: ' ASP.NET Core Blazor Routing ' autora: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c18d9-264">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c18d9-265">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c18d9-265">'Blazor'</span></span>
- <span data-ttu-id="c18d9-266">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c18d9-266">'Identity'</span></span>
- <span data-ttu-id="c18d9-267">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c18d9-267">'Let's Encrypt'</span></span>
- <span data-ttu-id="c18d9-268">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c18d9-268">'Razor'</span></span>
- <span data-ttu-id="c18d9-269">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c18d9-269">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c18d9-270">title: ' ASP.NET Core Blazor Routing ' autora: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c18d9-270">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c18d9-271">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c18d9-271">'Blazor'</span></span>
- <span data-ttu-id="c18d9-272">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c18d9-272">'Identity'</span></span>
- <span data-ttu-id="c18d9-273">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c18d9-273">'Let's Encrypt'</span></span>
- <span data-ttu-id="c18d9-274">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c18d9-274">'Razor'</span></span>
- <span data-ttu-id="c18d9-275">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c18d9-275">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c18d9-276">title: ' ASP.NET Core Blazor Routing ' autora: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c18d9-276">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c18d9-277">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c18d9-277">'Blazor'</span></span>
- <span data-ttu-id="c18d9-278">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c18d9-278">'Identity'</span></span>
- <span data-ttu-id="c18d9-279">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c18d9-279">'Let's Encrypt'</span></span>
- <span data-ttu-id="c18d9-280">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c18d9-280">'Razor'</span></span>
- <span data-ttu-id="c18d9-281">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c18d9-281">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c18d9-282">title: ' ASP.NET Core Blazor Routing ' autora: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c18d9-282">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c18d9-283">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c18d9-283">'Blazor'</span></span>
- <span data-ttu-id="c18d9-284">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c18d9-284">'Identity'</span></span>
- <span data-ttu-id="c18d9-285">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c18d9-285">'Let's Encrypt'</span></span>
- <span data-ttu-id="c18d9-286">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c18d9-286">'Razor'</span></span>
- <span data-ttu-id="c18d9-287">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c18d9-287">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c18d9-288">title: ' ASP.NET Core Blazor Routing ' autora: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c18d9-288">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c18d9-289">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c18d9-289">'Blazor'</span></span>
- <span data-ttu-id="c18d9-290">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c18d9-290">'Identity'</span></span>
- <span data-ttu-id="c18d9-291">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c18d9-291">'Let's Encrypt'</span></span>
- <span data-ttu-id="c18d9-292">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c18d9-292">'Razor'</span></span>
- <span data-ttu-id="c18d9-293">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c18d9-293">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c18d9-294">title: ' ASP.NET Core Blazor Routing ' autora: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c18d9-294">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c18d9-295">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c18d9-295">'Blazor'</span></span>
- <span data-ttu-id="c18d9-296">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c18d9-296">'Identity'</span></span>
- <span data-ttu-id="c18d9-297">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c18d9-297">'Let's Encrypt'</span></span>
- <span data-ttu-id="c18d9-298">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c18d9-298">'Razor'</span></span>
- <span data-ttu-id="c18d9-299">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c18d9-299">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c18d9-300">title: ' ASP.NET Core Blazor Routing ' autora: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c18d9-300">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c18d9-301">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c18d9-301">'Blazor'</span></span>
- <span data-ttu-id="c18d9-302">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c18d9-302">'Identity'</span></span>
- <span data-ttu-id="c18d9-303">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c18d9-303">'Let's Encrypt'</span></span>
- <span data-ttu-id="c18d9-304">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c18d9-304">'Razor'</span></span>
- <span data-ttu-id="c18d9-305">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c18d9-305">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c18d9-306">title: ' ASP.NET Core Blazor Routing ' autora: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c18d9-306">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c18d9-307">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c18d9-307">'Blazor'</span></span>
- <span data-ttu-id="c18d9-308">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c18d9-308">'Identity'</span></span>
- <span data-ttu-id="c18d9-309">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c18d9-309">'Let's Encrypt'</span></span>
- <span data-ttu-id="c18d9-310">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c18d9-310">'Razor'</span></span>
- <span data-ttu-id="c18d9-311">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c18d9-311">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c18d9-312">title: ' ASP.NET Core Blazor Routing ' autora: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c18d9-312">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c18d9-313">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c18d9-313">'Blazor'</span></span>
- <span data-ttu-id="c18d9-314">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c18d9-314">'Identity'</span></span>
- <span data-ttu-id="c18d9-315">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c18d9-315">'Let's Encrypt'</span></span>
- <span data-ttu-id="c18d9-316">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c18d9-316">'Razor'</span></span>
- <span data-ttu-id="c18d9-317">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c18d9-317">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c18d9-318">title: ' ASP.NET Core Blazor Routing ' autora: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c18d9-318">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c18d9-319">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c18d9-319">'Blazor'</span></span>
- <span data-ttu-id="c18d9-320">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c18d9-320">'Identity'</span></span>
- <span data-ttu-id="c18d9-321">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c18d9-321">'Let's Encrypt'</span></span>
- <span data-ttu-id="c18d9-322">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c18d9-322">'Razor'</span></span>
- <span data-ttu-id="c18d9-323">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c18d9-323">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c18d9-324">title: ' ASP.NET Core Blazor Routing ' autora: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c18d9-324">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c18d9-325">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c18d9-325">'Blazor'</span></span>
- <span data-ttu-id="c18d9-326">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c18d9-326">'Identity'</span></span>
- <span data-ttu-id="c18d9-327">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c18d9-327">'Let's Encrypt'</span></span>
- <span data-ttu-id="c18d9-328">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c18d9-328">'Razor'</span></span>
- <span data-ttu-id="c18d9-329">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c18d9-329">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c18d9-330">title: ' ASP.NET Core Blazor Routing ' autora: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c18d9-330">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c18d9-331">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c18d9-331">'Blazor'</span></span>
- <span data-ttu-id="c18d9-332">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c18d9-332">'Identity'</span></span>
- <span data-ttu-id="c18d9-333">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c18d9-333">'Let's Encrypt'</span></span>
- <span data-ttu-id="c18d9-334">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c18d9-334">'Razor'</span></span>
- <span data-ttu-id="c18d9-335">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c18d9-335">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c18d9-336">title: ' ASP.NET Core Blazor Routing ' autora: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c18d9-336">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c18d9-337">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c18d9-337">'Blazor'</span></span>
- <span data-ttu-id="c18d9-338">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c18d9-338">'Identity'</span></span>
- <span data-ttu-id="c18d9-339">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c18d9-339">'Let's Encrypt'</span></span>
- <span data-ttu-id="c18d9-340">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c18d9-340">'Razor'</span></span>
- <span data-ttu-id="c18d9-341">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c18d9-341">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c18d9-342">title: ' ASP.NET Core Blazor Routing ' autora: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c18d9-342">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c18d9-343">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c18d9-343">'Blazor'</span></span>
- <span data-ttu-id="c18d9-344">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c18d9-344">'Identity'</span></span>
- <span data-ttu-id="c18d9-345">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c18d9-345">'Let's Encrypt'</span></span>
- <span data-ttu-id="c18d9-346">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c18d9-346">'Razor'</span></span>
- <span data-ttu-id="c18d9-347">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c18d9-347">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c18d9-348">title: ' ASP.NET Core Blazor Routing ' autora: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c18d9-348">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c18d9-349">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c18d9-349">'Blazor'</span></span>
- <span data-ttu-id="c18d9-350">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c18d9-350">'Identity'</span></span>
- <span data-ttu-id="c18d9-351">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c18d9-351">'Let's Encrypt'</span></span>
- <span data-ttu-id="c18d9-352">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c18d9-352">'Razor'</span></span>
- <span data-ttu-id="c18d9-353">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c18d9-353">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c18d9-354">title: ' ASP.NET Core Blazor Routing ' autora: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c18d9-354">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c18d9-355">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c18d9-355">'Blazor'</span></span>
- <span data-ttu-id="c18d9-356">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c18d9-356">'Identity'</span></span>
- <span data-ttu-id="c18d9-357">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c18d9-357">'Let's Encrypt'</span></span>
- <span data-ttu-id="c18d9-358">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c18d9-358">'Razor'</span></span>
- <span data-ttu-id="c18d9-359">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c18d9-359">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c18d9-360">title: ' ASP.NET Core Blazor Routing ' autora: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c18d9-360">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c18d9-361">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c18d9-361">'Blazor'</span></span>
- <span data-ttu-id="c18d9-362">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c18d9-362">'Identity'</span></span>
- <span data-ttu-id="c18d9-363">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c18d9-363">'Let's Encrypt'</span></span>
- <span data-ttu-id="c18d9-364">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c18d9-364">'Razor'</span></span>
- <span data-ttu-id="c18d9-365">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c18d9-365">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c18d9-366">title: ' ASP.NET Core Blazor Routing ' autora: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c18d9-366">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c18d9-367">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c18d9-367">'Blazor'</span></span>
- <span data-ttu-id="c18d9-368">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c18d9-368">'Identity'</span></span>
- <span data-ttu-id="c18d9-369">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c18d9-369">'Let's Encrypt'</span></span>
- <span data-ttu-id="c18d9-370">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c18d9-370">'Razor'</span></span>
- <span data-ttu-id="c18d9-371">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c18d9-371">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c18d9-372">title: ' ASP.NET Core Blazor Routing ' autora: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c18d9-372">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c18d9-373">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c18d9-373">'Blazor'</span></span>
- <span data-ttu-id="c18d9-374">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c18d9-374">'Identity'</span></span>
- <span data-ttu-id="c18d9-375">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c18d9-375">'Let's Encrypt'</span></span>
- <span data-ttu-id="c18d9-376">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c18d9-376">'Razor'</span></span>
- <span data-ttu-id="c18d9-377">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c18d9-377">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c18d9-378">title: ' ASP.NET Core Blazor Routing ' autora: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c18d9-378">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c18d9-379">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c18d9-379">'Blazor'</span></span>
- <span data-ttu-id="c18d9-380">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c18d9-380">'Identity'</span></span>
- <span data-ttu-id="c18d9-381">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c18d9-381">'Let's Encrypt'</span></span>
- <span data-ttu-id="c18d9-382">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c18d9-382">'Razor'</span></span>
- <span data-ttu-id="c18d9-383">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c18d9-383">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c18d9-384">title: ' ASP.NET Core Blazor Routing ' autora: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c18d9-384">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c18d9-385">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c18d9-385">'Blazor'</span></span>
- <span data-ttu-id="c18d9-386">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c18d9-386">'Identity'</span></span>
- <span data-ttu-id="c18d9-387">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c18d9-387">'Let's Encrypt'</span></span>
- <span data-ttu-id="c18d9-388">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c18d9-388">'Razor'</span></span>
- <span data-ttu-id="c18d9-389">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c18d9-389">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c18d9-390">title: ' ASP.NET Core Blazor Routing ' autora: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c18d9-390">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c18d9-391">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c18d9-391">'Blazor'</span></span>
- <span data-ttu-id="c18d9-392">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c18d9-392">'Identity'</span></span>
- <span data-ttu-id="c18d9-393">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c18d9-393">'Let's Encrypt'</span></span>
- <span data-ttu-id="c18d9-394">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c18d9-394">'Razor'</span></span>
- <span data-ttu-id="c18d9-395">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c18d9-395">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c18d9-396">title: ' ASP.NET Core Blazor Routing ' autora: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c18d9-396">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c18d9-397">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c18d9-397">'Blazor'</span></span>
- <span data-ttu-id="c18d9-398">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c18d9-398">'Identity'</span></span>
- <span data-ttu-id="c18d9-399">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c18d9-399">'Let's Encrypt'</span></span>
- <span data-ttu-id="c18d9-400">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c18d9-400">'Razor'</span></span>
- <span data-ttu-id="c18d9-401">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c18d9-401">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c18d9-402">title: ' ASP.NET Core Blazor Routing ' autora: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c18d9-402">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c18d9-403">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c18d9-403">'Blazor'</span></span>
- <span data-ttu-id="c18d9-404">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c18d9-404">'Identity'</span></span>
- <span data-ttu-id="c18d9-405">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c18d9-405">'Let's Encrypt'</span></span>
- <span data-ttu-id="c18d9-406">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c18d9-406">'Razor'</span></span>
- <span data-ttu-id="c18d9-407">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c18d9-407">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c18d9-408">title: ' ASP.NET Core Blazor Routing ' autora: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c18d9-408">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c18d9-409">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c18d9-409">'Blazor'</span></span>
- <span data-ttu-id="c18d9-410">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c18d9-410">'Identity'</span></span>
- <span data-ttu-id="c18d9-411">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c18d9-411">'Let's Encrypt'</span></span>
- <span data-ttu-id="c18d9-412">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c18d9-412">'Razor'</span></span>
- <span data-ttu-id="c18d9-413">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c18d9-413">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c18d9-414">title: ' ASP.NET Core Blazor Routing ' autora: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c18d9-414">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c18d9-415">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c18d9-415">'Blazor'</span></span>
- <span data-ttu-id="c18d9-416">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c18d9-416">'Identity'</span></span>
- <span data-ttu-id="c18d9-417">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c18d9-417">'Let's Encrypt'</span></span>
- <span data-ttu-id="c18d9-418">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c18d9-418">'Razor'</span></span>
- <span data-ttu-id="c18d9-419">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c18d9-419">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c18d9-420">title: ' ASP.NET Core Blazor Routing ' autora: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c18d9-420">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c18d9-421">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c18d9-421">'Blazor'</span></span>
- <span data-ttu-id="c18d9-422">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c18d9-422">'Identity'</span></span>
- <span data-ttu-id="c18d9-423">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c18d9-423">'Let's Encrypt'</span></span>
- <span data-ttu-id="c18d9-424">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c18d9-424">'Razor'</span></span>
- <span data-ttu-id="c18d9-425">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c18d9-425">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c18d9-426">title: ' ASP.NET Core Blazor Routing ' autora: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c18d9-426">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c18d9-427">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c18d9-427">'Blazor'</span></span>
- <span data-ttu-id="c18d9-428">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c18d9-428">'Identity'</span></span>
- <span data-ttu-id="c18d9-429">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c18d9-429">'Let's Encrypt'</span></span>
- <span data-ttu-id="c18d9-430">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c18d9-430">'Razor'</span></span>
- <span data-ttu-id="c18d9-431">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c18d9-431">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c18d9-432">title: ' ASP.NET Core Blazor Routing ' autora: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c18d9-432">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c18d9-433">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c18d9-433">'Blazor'</span></span>
- <span data-ttu-id="c18d9-434">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c18d9-434">'Identity'</span></span>
- <span data-ttu-id="c18d9-435">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c18d9-435">'Let's Encrypt'</span></span>
- <span data-ttu-id="c18d9-436">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c18d9-436">'Razor'</span></span>
- <span data-ttu-id="c18d9-437">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c18d9-437">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c18d9-438">title: ' ASP.NET Core Blazor Routing ' autora: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c18d9-438">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c18d9-439">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c18d9-439">'Blazor'</span></span>
- <span data-ttu-id="c18d9-440">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c18d9-440">'Identity'</span></span>
- <span data-ttu-id="c18d9-441">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c18d9-441">'Let's Encrypt'</span></span>
- <span data-ttu-id="c18d9-442">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c18d9-442">'Razor'</span></span>
- <span data-ttu-id="c18d9-443">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c18d9-443">'SignalR' uid:</span></span> 

<span data-ttu-id="c18d9-444">---------------------------------------- | :---název: ' ASP.NET Core Blazor Směrování ' Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c18d9-444">---------------------------------------- | :--- title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c18d9-445">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c18d9-445">'Blazor'</span></span>
- <span data-ttu-id="c18d9-446">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c18d9-446">'Identity'</span></span>
- <span data-ttu-id="c18d9-447">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c18d9-447">'Let's Encrypt'</span></span>
- <span data-ttu-id="c18d9-448">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c18d9-448">'Razor'</span></span>
- <span data-ttu-id="c18d9-449">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c18d9-449">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c18d9-450">title: ' ASP.NET Core Blazor Routing ' autora: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c18d9-450">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c18d9-451">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c18d9-451">'Blazor'</span></span>
- <span data-ttu-id="c18d9-452">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c18d9-452">'Identity'</span></span>
- <span data-ttu-id="c18d9-453">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c18d9-453">'Let's Encrypt'</span></span>
- <span data-ttu-id="c18d9-454">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c18d9-454">'Razor'</span></span>
- <span data-ttu-id="c18d9-455">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c18d9-455">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c18d9-456">title: ' ASP.NET Core Blazor Routing ' autora: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c18d9-456">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c18d9-457">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c18d9-457">'Blazor'</span></span>
- <span data-ttu-id="c18d9-458">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c18d9-458">'Identity'</span></span>
- <span data-ttu-id="c18d9-459">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c18d9-459">'Let's Encrypt'</span></span>
- <span data-ttu-id="c18d9-460">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c18d9-460">'Razor'</span></span>
- <span data-ttu-id="c18d9-461">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c18d9-461">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c18d9-462">title: ' ASP.NET Core Blazor Routing ' autora: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c18d9-462">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c18d9-463">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c18d9-463">'Blazor'</span></span>
- <span data-ttu-id="c18d9-464">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c18d9-464">'Identity'</span></span>
- <span data-ttu-id="c18d9-465">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c18d9-465">'Let's Encrypt'</span></span>
- <span data-ttu-id="c18d9-466">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c18d9-466">'Razor'</span></span>
- <span data-ttu-id="c18d9-467">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c18d9-467">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c18d9-468">title: ' ASP.NET Core Blazor Routing ' autora: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c18d9-468">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c18d9-469">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c18d9-469">'Blazor'</span></span>
- <span data-ttu-id="c18d9-470">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c18d9-470">'Identity'</span></span>
- <span data-ttu-id="c18d9-471">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c18d9-471">'Let's Encrypt'</span></span>
- <span data-ttu-id="c18d9-472">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c18d9-472">'Razor'</span></span>
- <span data-ttu-id="c18d9-473">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c18d9-473">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c18d9-474">title: ' ASP.NET Core Blazor Routing ' autora: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c18d9-474">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c18d9-475">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c18d9-475">'Blazor'</span></span>
- <span data-ttu-id="c18d9-476">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c18d9-476">'Identity'</span></span>
- <span data-ttu-id="c18d9-477">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c18d9-477">'Let's Encrypt'</span></span>
- <span data-ttu-id="c18d9-478">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c18d9-478">'Razor'</span></span>
- <span data-ttu-id="c18d9-479">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c18d9-479">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c18d9-480">title: ' ASP.NET Core Blazor Routing ' autora: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c18d9-480">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c18d9-481">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c18d9-481">'Blazor'</span></span>
- <span data-ttu-id="c18d9-482">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c18d9-482">'Identity'</span></span>
- <span data-ttu-id="c18d9-483">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c18d9-483">'Let's Encrypt'</span></span>
- <span data-ttu-id="c18d9-484">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c18d9-484">'Razor'</span></span>
- <span data-ttu-id="c18d9-485">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c18d9-485">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c18d9-486">title: ' ASP.NET Core Blazor Routing ' autora: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c18d9-486">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c18d9-487">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c18d9-487">'Blazor'</span></span>
- <span data-ttu-id="c18d9-488">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c18d9-488">'Identity'</span></span>
- <span data-ttu-id="c18d9-489">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c18d9-489">'Let's Encrypt'</span></span>
- <span data-ttu-id="c18d9-490">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c18d9-490">'Razor'</span></span>
- <span data-ttu-id="c18d9-491">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c18d9-491">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c18d9-492">title: ' ASP.NET Core Blazor Routing ' autora: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c18d9-492">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c18d9-493">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c18d9-493">'Blazor'</span></span>
- <span data-ttu-id="c18d9-494">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c18d9-494">'Identity'</span></span>
- <span data-ttu-id="c18d9-495">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c18d9-495">'Let's Encrypt'</span></span>
- <span data-ttu-id="c18d9-496">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c18d9-496">'Razor'</span></span>
- <span data-ttu-id="c18d9-497">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c18d9-497">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c18d9-498">title: ' ASP.NET Core Blazor Routing ' autora: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c18d9-498">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c18d9-499">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c18d9-499">'Blazor'</span></span>
- <span data-ttu-id="c18d9-500">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c18d9-500">'Identity'</span></span>
- <span data-ttu-id="c18d9-501">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c18d9-501">'Let's Encrypt'</span></span>
- <span data-ttu-id="c18d9-502">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c18d9-502">'Razor'</span></span>
- <span data-ttu-id="c18d9-503">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c18d9-503">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c18d9-504">title: ' ASP.NET Core Blazor Routing ' autora: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c18d9-504">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c18d9-505">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c18d9-505">'Blazor'</span></span>
- <span data-ttu-id="c18d9-506">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c18d9-506">'Identity'</span></span>
- <span data-ttu-id="c18d9-507">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c18d9-507">'Let's Encrypt'</span></span>
- <span data-ttu-id="c18d9-508">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c18d9-508">'Razor'</span></span>
- <span data-ttu-id="c18d9-509">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c18d9-509">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c18d9-510">title: ' ASP.NET Core Blazor Routing ' autora: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c18d9-510">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c18d9-511">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c18d9-511">'Blazor'</span></span>
- <span data-ttu-id="c18d9-512">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c18d9-512">'Identity'</span></span>
- <span data-ttu-id="c18d9-513">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c18d9-513">'Let's Encrypt'</span></span>
- <span data-ttu-id="c18d9-514">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c18d9-514">'Razor'</span></span>
- <span data-ttu-id="c18d9-515">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c18d9-515">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c18d9-516">title: ' ASP.NET Core Blazor Routing ' autora: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c18d9-516">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c18d9-517">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c18d9-517">'Blazor'</span></span>
- <span data-ttu-id="c18d9-518">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c18d9-518">'Identity'</span></span>
- <span data-ttu-id="c18d9-519">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c18d9-519">'Let's Encrypt'</span></span>
- <span data-ttu-id="c18d9-520">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c18d9-520">'Razor'</span></span>
- <span data-ttu-id="c18d9-521">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c18d9-521">'SignalR' uid:</span></span> 

<span data-ttu-id="c18d9-522">---------------: | | `bool`     | `{active:bool}`   | `true`, `FALSE`                                                                  | Žádné | | `datetime` | `{dob:datetime}`  | `2016-12-31`, `2016-12-31 7:32pm`                                                | Ano | | `decimal`  | `{price:decimal}` | `49.99`, `-1,000.01`                                                             | Ano | | `double`   | `{weight:double}` | `1.234`, `-1,001.01e8`                                                           | Ano | | `float`    | `{weight:float}`  | `1.234`, `-1,001.01e8`                                                           | Ano | | `guid`     | `{id:guid}`       | `CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}` | Žádné | | `int`      | `{id:int}`        | `123456789`, `-123456789`                                                        | Ano | | `long`     | `{ticks:long}`    | `123456789`, `-123456789`                                                        | Ano |</span><span class="sxs-lookup"><span data-stu-id="c18d9-522">---------------: | | `bool`     | `{active:bool}`   | `true`, `FALSE`                                                                  | No                               | | `datetime` | `{dob:datetime}`  | `2016-12-31`, `2016-12-31 7:32pm`                                                | Yes                              | | `decimal`  | `{price:decimal}` | `49.99`, `-1,000.01`                                                             | Yes                              | | `double`   | `{weight:double}` | `1.234`, `-1,001.01e8`                                                           | Yes                              | | `float`    | `{weight:float}`  | `1.234`, `-1,001.01e8`                                                           | Yes                              | | `guid`     | `{id:guid}`       | `CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}` | No                               | | `int`      | `{id:int}`        | `123456789`, `-123456789`                                                        | Yes                              | | `long`     | `{ticks:long}`    | `123456789`, `-123456789`                                                        | Yes                              |</span></span>

> [!WARNING]
> <span data-ttu-id="c18d9-523">Omezení směrování, která ověřují adresu URL a jsou převedena na typ CLR (například `int` nebo <xref:System.DateTime> ), vždy používají invariantní jazykovou verzi.</span><span class="sxs-lookup"><span data-stu-id="c18d9-523">Route constraints that verify the URL and are converted to a CLR type (such as `int` or <xref:System.DateTime>) always use the invariant culture.</span></span> <span data-ttu-id="c18d9-524">Tato omezení předpokládají, že adresa URL nelze lokalizovat.</span><span class="sxs-lookup"><span data-stu-id="c18d9-524">These constraints assume that the URL is non-localizable.</span></span>

### <a name="routing-with-urls-that-contain-dots"></a><span data-ttu-id="c18d9-525">Směrování s adresami URL, které obsahují tečky</span><span class="sxs-lookup"><span data-stu-id="c18d9-525">Routing with URLs that contain dots</span></span>

<span data-ttu-id="c18d9-526">V Blazor serverových aplikacích je výchozí trasa v *_Host. cshtml* `/` ( `@page "/"` ).</span><span class="sxs-lookup"><span data-stu-id="c18d9-526">In Blazor Server apps, the default route in *_Host.cshtml* is `/` (`@page "/"`).</span></span> <span data-ttu-id="c18d9-527">Adresa URL požadavku, která obsahuje tečku ( `.` ), není shodná s výchozí cestou, protože adresa URL se zobrazí pro vyžádání souboru.</span><span class="sxs-lookup"><span data-stu-id="c18d9-527">A request URL that contains a dot (`.`) isn't matched by the default route because the URL appears to request a file.</span></span> <span data-ttu-id="c18d9-528">BlazorAplikace vrátí odpověď na *404, která nebyla nalezena* pro statický soubor, který neexistuje.</span><span class="sxs-lookup"><span data-stu-id="c18d9-528">A Blazor app returns a *404 - Not Found* response for a static file that doesn't exist.</span></span> <span data-ttu-id="c18d9-529">Chcete-li použít trasy, které obsahují tečku, nakonfigurujte *_Host. cshtml* s následující šablonou směrování:</span><span class="sxs-lookup"><span data-stu-id="c18d9-529">To use routes that contain a dot, configure *_Host.cshtml* with the following route template:</span></span>

```cshtml
@page "/{**path}"
```

<span data-ttu-id="c18d9-530">`"/{**path}"`Šablona obsahuje:</span><span class="sxs-lookup"><span data-stu-id="c18d9-530">The `"/{**path}"` template includes:</span></span>

* <span data-ttu-id="c18d9-531">Dvojitá hvězdička *– veškerá* syntaxe ( `**` ) pro zachycení cesty mezi více hranicemi složek bez kódování lomítka ( `/` ).</span><span class="sxs-lookup"><span data-stu-id="c18d9-531">Double-asterisk *catch-all* syntax (`**`) to capture the path across multiple folder boundaries without encoding forward slashes (`/`).</span></span>
* <span data-ttu-id="c18d9-532">`path`název parametru trasy.</span><span class="sxs-lookup"><span data-stu-id="c18d9-532">`path` route parameter name.</span></span>

> [!NOTE]
> <span data-ttu-id="c18d9-533">*Catch-All* parametr Syntax ( `*` / `**` ) není **not** v Razor součástech (*. Razor*) podporován.</span><span class="sxs-lookup"><span data-stu-id="c18d9-533">*Catch-all* parameter syntax (`*`/`**`) is **not** supported in Razor components (*.razor*).</span></span>

<span data-ttu-id="c18d9-534">Další informace naleznete v tématu <xref:fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="c18d9-534">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="navlink-component"></a><span data-ttu-id="c18d9-535">Komponenta NavLink</span><span class="sxs-lookup"><span data-stu-id="c18d9-535">NavLink component</span></span>

<span data-ttu-id="c18d9-536">Použít <xref:Microsoft.AspNetCore.Components.Routing.NavLink> komponentu namísto prvků hypertextového odkazu HTML ( `<a>` ) při vytváření navigačních odkazů.</span><span class="sxs-lookup"><span data-stu-id="c18d9-536">Use a <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component in place of HTML hyperlink elements (`<a>`) when creating navigation links.</span></span> <span data-ttu-id="c18d9-537"><xref:Microsoft.AspNetCore.Components.Routing.NavLink>Komponenta se chová jako `<a>` element s tím rozdílem, že přepíná `active` třídu CSS na základě toho, zda `href` odpovídá aktuální adrese URL.</span><span class="sxs-lookup"><span data-stu-id="c18d9-537">A <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component behaves like an `<a>` element, except it toggles an `active` CSS class based on whether its `href` matches the current URL.</span></span> <span data-ttu-id="c18d9-538">`active`Třída pomáhá uživateli pochopit, která stránka je aktivní stránkou mezi zobrazenými navigačními odkazy.</span><span class="sxs-lookup"><span data-stu-id="c18d9-538">The `active` class helps a user understand which page is the active page among the navigation links displayed.</span></span>

<span data-ttu-id="c18d9-539">Následující `NavMenu` Komponenta vytvoří navigační panel [bootstrap](https://getbootstrap.com/docs/) , který ukazuje, jak používat <xref:Microsoft.AspNetCore.Components.Routing.NavLink> komponenty:</span><span class="sxs-lookup"><span data-stu-id="c18d9-539">The following `NavMenu` component creates a [Bootstrap](https://getbootstrap.com/docs/) navigation bar that demonstrates how to use <xref:Microsoft.AspNetCore.Components.Routing.NavLink> components:</span></span>

[!code-razor[](routing/samples_snapshot/3.x/NavMenu.razor?highlight=4,9)]

<span data-ttu-id="c18d9-540">Existují dvě <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch> Možnosti, které lze přiřadit k `Match` atributu `<NavLink>` elementu:</span><span class="sxs-lookup"><span data-stu-id="c18d9-540">There are two <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch> options that you can assign to the `Match` attribute of the `<NavLink>` element:</span></span>

* <span data-ttu-id="c18d9-541"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.All?displayProperty=nameWithType>: <xref:Microsoft.AspNetCore.Components.Routing.NavLink> Je aktivní, pokud odpovídá celé aktuální adrese URL.</span><span class="sxs-lookup"><span data-stu-id="c18d9-541"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.All?displayProperty=nameWithType>: The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> is active when it matches the entire current URL.</span></span>
* <span data-ttu-id="c18d9-542"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.Prefix?displayProperty=nameWithType>(*výchozí*): <xref:Microsoft.AspNetCore.Components.Routing.NavLink> je aktivní, pokud odpovídá libovolné PŘEDPONĚ aktuální adresy URL.</span><span class="sxs-lookup"><span data-stu-id="c18d9-542"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.Prefix?displayProperty=nameWithType> (*default*): The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> is active when it matches any prefix of the current URL.</span></span>

<span data-ttu-id="c18d9-543">V předchozím příkladu se Domovská stránka <xref:Microsoft.AspNetCore.Components.Routing.NavLink> `href=""` shoduje s adresou URL domů a přijímá pouze `active` třídu CSS ve výchozí základní cestě URL aplikace (například `https://localhost:5001/` ).</span><span class="sxs-lookup"><span data-stu-id="c18d9-543">In the preceding example, the Home <xref:Microsoft.AspNetCore.Components.Routing.NavLink> `href=""` matches the home URL and only receives the `active` CSS class at the app's default base path URL (for example, `https://localhost:5001/`).</span></span> <span data-ttu-id="c18d9-544">Druhá <xref:Microsoft.AspNetCore.Components.Routing.NavLink> Získá třídu, `active` když uživatel navštíví libovolnou adresu URL s `MyComponent` předponou (například `https://localhost:5001/MyComponent` a `https://localhost:5001/MyComponent/AnotherSegment` ).</span><span class="sxs-lookup"><span data-stu-id="c18d9-544">The second <xref:Microsoft.AspNetCore.Components.Routing.NavLink> receives the `active` class when the user visits any URL with a `MyComponent` prefix (for example, `https://localhost:5001/MyComponent` and `https://localhost:5001/MyComponent/AnotherSegment`).</span></span>

<span data-ttu-id="c18d9-545">Další <xref:Microsoft.AspNetCore.Components.Routing.NavLink> atributy komponenty jsou předány do vykreslené značky ukotvení.</span><span class="sxs-lookup"><span data-stu-id="c18d9-545">Additional <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component attributes are passed through to the rendered anchor tag.</span></span> <span data-ttu-id="c18d9-546">V následujícím příkladu <xref:Microsoft.AspNetCore.Components.Routing.NavLink> Komponenta zahrnuje `target` atribut:</span><span class="sxs-lookup"><span data-stu-id="c18d9-546">In the following example, the <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component includes the `target` attribute:</span></span>

```razor
<NavLink href="my-page" target="_blank">My page</NavLink>
```

<span data-ttu-id="c18d9-547">Vykresluje se následující kód HTML:</span><span class="sxs-lookup"><span data-stu-id="c18d9-547">The following HTML markup is rendered:</span></span>

```html
<a href="my-page" target="_blank" rel="noopener noreferrer">My page</a>
```

## <a name="uri-and-navigation-state-helpers"></a><span data-ttu-id="c18d9-548">Identifikátory URI a pomocníka pro stav navigace</span><span class="sxs-lookup"><span data-stu-id="c18d9-548">URI and navigation state helpers</span></span>

<span data-ttu-id="c18d9-549">Použijte <xref:Microsoft.AspNetCore.Components.NavigationManager> pro práci s identifikátory URI a navigací v kódu jazyka C#.</span><span class="sxs-lookup"><span data-stu-id="c18d9-549">Use <xref:Microsoft.AspNetCore.Components.NavigationManager> to work with URIs and navigation in C# code.</span></span> <span data-ttu-id="c18d9-550"><xref:Microsoft.AspNetCore.Components.NavigationManager>poskytuje událost a metody uvedené v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="c18d9-550"><xref:Microsoft.AspNetCore.Components.NavigationManager> provides the event and methods shown in the following table.</span></span>

| <span data-ttu-id="c18d9-551">Člen</span><span class="sxs-lookup"><span data-stu-id="c18d9-551">Member</span></span> | <span data-ttu-id="c18d9-552">Description</span><span class="sxs-lookup"><span data-stu-id="c18d9-552">Description</span></span> |
| ---
<span data-ttu-id="c18d9-553">title: ' ASP.NET Core Blazor Routing ' autora: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c18d9-553">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c18d9-554">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c18d9-554">'Blazor'</span></span>
- <span data-ttu-id="c18d9-555">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c18d9-555">'Identity'</span></span>
- <span data-ttu-id="c18d9-556">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c18d9-556">'Let's Encrypt'</span></span>
- <span data-ttu-id="c18d9-557">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c18d9-557">'Razor'</span></span>
- <span data-ttu-id="c18d9-558">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c18d9-558">'SignalR' uid:</span></span> 

<span data-ttu-id="c18d9-559">--- | ---Název: ' ASP.NET Core Blazor Směrování ' Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c18d9-559">--- | --- title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c18d9-560">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c18d9-560">'Blazor'</span></span>
- <span data-ttu-id="c18d9-561">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c18d9-561">'Identity'</span></span>
- <span data-ttu-id="c18d9-562">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c18d9-562">'Let's Encrypt'</span></span>
- <span data-ttu-id="c18d9-563">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c18d9-563">'Razor'</span></span>
- <span data-ttu-id="c18d9-564">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c18d9-564">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c18d9-565">title: ' ASP.NET Core Blazor Routing ' autora: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c18d9-565">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c18d9-566">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c18d9-566">'Blazor'</span></span>
- <span data-ttu-id="c18d9-567">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c18d9-567">'Identity'</span></span>
- <span data-ttu-id="c18d9-568">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c18d9-568">'Let's Encrypt'</span></span>
- <span data-ttu-id="c18d9-569">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c18d9-569">'Razor'</span></span>
- <span data-ttu-id="c18d9-570">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c18d9-570">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c18d9-571">title: ' ASP.NET Core Blazor Routing ' autora: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c18d9-571">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c18d9-572">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c18d9-572">'Blazor'</span></span>
- <span data-ttu-id="c18d9-573">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c18d9-573">'Identity'</span></span>
- <span data-ttu-id="c18d9-574">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c18d9-574">'Let's Encrypt'</span></span>
- <span data-ttu-id="c18d9-575">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c18d9-575">'Razor'</span></span>
- <span data-ttu-id="c18d9-576">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c18d9-576">'SignalR' uid:</span></span> 

<span data-ttu-id="c18d9-577">------ | | <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri> | Získá aktuální absolutní identifikátor URI.</span><span class="sxs-lookup"><span data-stu-id="c18d9-577">------ | | <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri> | Gets the current absolute URI.</span></span> <span data-ttu-id="c18d9-578">| | <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> | Získá základní identifikátor URI (s koncovým lomítkem), který může být součástí relativních cest URI pro vytvoření absolutního identifikátoru URI.</span><span class="sxs-lookup"><span data-stu-id="c18d9-578">| | <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> | Gets the base URI (with a trailing slash) that can be prepended to relative URI paths to produce an absolute URI.</span></span> <span data-ttu-id="c18d9-579">Obvykle <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> odpovídá `href` atributům `<base>` prvku dokumentu v *wwwroot/index.html* ( Blazor WebAssembly) nebo *Pages/_Host. cshtml* ( Blazor Server).</span><span class="sxs-lookup"><span data-stu-id="c18d9-579">Typically, <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> corresponds to the `href` attribute on the document's `<base>` element in *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server).</span></span> <span data-ttu-id="c18d9-580">| | <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A> | Přejde k zadanému identifikátoru URI.</span><span class="sxs-lookup"><span data-stu-id="c18d9-580">| | <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A> | Navigates to the specified URI.</span></span> <span data-ttu-id="c18d9-581">Pokud `forceLoad` je `true` :</span><span class="sxs-lookup"><span data-stu-id="c18d9-581">If `forceLoad` is `true`:</span></span><ul><li><span data-ttu-id="c18d9-582">Směrování na straně klienta se nepoužívá.</span><span class="sxs-lookup"><span data-stu-id="c18d9-582">Client-side routing is bypassed.</span></span></li><li><span data-ttu-id="c18d9-583">Prohlížeč je nucen načíst novou stránku ze serveru, bez ohledu na to, zda je identifikátor URI obvykle zpracováván směrovačem na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="c18d9-583">The browser is forced to load the new page from the server, whether or not the URI is normally handled by the client-side router.</span></span></li></ul> <span data-ttu-id="c18d9-584">| | <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged> | Událost, která se aktivuje, když se změní navigační umístění</span><span class="sxs-lookup"><span data-stu-id="c18d9-584">| | <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged> | An event that fires when the navigation location has changed.</span></span> <span data-ttu-id="c18d9-585">| | <xref:Microsoft.AspNetCore.Components.NavigationManager.ToAbsoluteUri%2A> | Převede relativní identifikátor URI na absolutní identifikátor URI.</span><span class="sxs-lookup"><span data-stu-id="c18d9-585">| | <xref:Microsoft.AspNetCore.Components.NavigationManager.ToAbsoluteUri%2A> | Converts a relative URI into an absolute URI.</span></span> <span data-ttu-id="c18d9-586">| | <span style="word-break:normal;word-wrap:normal"><xref:Microsoft.AspNetCore.Components.NavigationManager.ToBaseRelativePath%2A></span> | Vzhledem k základnímu identifikátoru URI (například identifikátor URI, který dřív vrátil <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> ), převede absolutní identifikátor URI na URI relativně k základní předponě identifikátoru URI.</span><span class="sxs-lookup"><span data-stu-id="c18d9-586">| | <span style="word-break:normal;word-wrap:normal"><xref:Microsoft.AspNetCore.Components.NavigationManager.ToBaseRelativePath%2A></span> | Given a base URI (for example, a URI previously returned by <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri>), converts an absolute URI into a URI relative to the base URI prefix.</span></span> |

<span data-ttu-id="c18d9-587">`Counter`Pokud je vybráno tlačítko, následující komponenta přejde na součást aplikace:</span><span class="sxs-lookup"><span data-stu-id="c18d9-587">The following component navigates to the app's `Counter` component when the button is selected:</span></span>

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

<span data-ttu-id="c18d9-588">Následující komponenta zpracovává událost změny umístění nastavením <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="c18d9-588">The following component handles a location changed event by setting <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged?displayProperty=nameWithType>.</span></span> <span data-ttu-id="c18d9-589">`HandleLocationChanged`Metoda je nepřipojená, pokud `Dispose` je volána rozhraním.</span><span class="sxs-lookup"><span data-stu-id="c18d9-589">The `HandleLocationChanged` method is unhooked when `Dispose` is called by the framework.</span></span> <span data-ttu-id="c18d9-590">Odpojování metody umožňuje uvolňování paměti komponenty.</span><span class="sxs-lookup"><span data-stu-id="c18d9-590">Unhooking the method permits garbage collection of the component.</span></span>

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

<span data-ttu-id="c18d9-591"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs>poskytuje následující informace o události:</span><span class="sxs-lookup"><span data-stu-id="c18d9-591"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs> provides the following information about the event:</span></span>

* <span data-ttu-id="c18d9-592"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>: Adresa URL nového umístění.</span><span class="sxs-lookup"><span data-stu-id="c18d9-592"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>: The URL of the new location.</span></span>
* <span data-ttu-id="c18d9-593"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>: Pokud `true` Blazor byla zachycena navigace z prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="c18d9-593"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>: If `true`, Blazor intercepted the navigation from the browser.</span></span> <span data-ttu-id="c18d9-594">`false` <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> V důsledku toho došlo k navigaci.</span><span class="sxs-lookup"><span data-stu-id="c18d9-594">If `false`, <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> caused the navigation to occur.</span></span>

<span data-ttu-id="c18d9-595">Další informace o vyřazení součástí najdete v tématu <xref:blazor/lifecycle#component-disposal-with-idisposable> .</span><span class="sxs-lookup"><span data-stu-id="c18d9-595">For more information on component disposal, see <xref:blazor/lifecycle#component-disposal-with-idisposable>.</span></span>
