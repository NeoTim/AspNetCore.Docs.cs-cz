---
<span data-ttu-id="4d39d-101">title: ' ASP.NET Core Blazor Routing ' autora: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="4d39d-101">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4d39d-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4d39d-102">'Blazor'</span></span>
- <span data-ttu-id="4d39d-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4d39d-103">'Identity'</span></span>
- <span data-ttu-id="4d39d-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4d39d-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="4d39d-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4d39d-105">'Razor'</span></span>
- <span data-ttu-id="4d39d-106">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="4d39d-106">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-blazor-routing"></a><span data-ttu-id="4d39d-107">BlazorSměrování ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4d39d-107">ASP.NET Core Blazor routing</span></span>

<span data-ttu-id="4d39d-108">Od [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="4d39d-108">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="4d39d-109">Naučte se směrovat požadavky a jak používat `NavLink` komponentu k vytváření navigačních odkazů v Blazor aplikacích.</span><span class="sxs-lookup"><span data-stu-id="4d39d-109">Learn how to route requests and how to use the `NavLink` component to create navigation links in Blazor apps.</span></span>

## <a name="aspnet-core-endpoint-routing-integration"></a><span data-ttu-id="4d39d-110">Integrace směrování ASP.NET Core Endpoint</span><span class="sxs-lookup"><span data-stu-id="4d39d-110">ASP.NET Core endpoint routing integration</span></span>

Blazor<span data-ttu-id="4d39d-111">Server je integrovaný do [Směrování koncového bodu ASP.NET Core](xref:fundamentals/routing).</span><span class="sxs-lookup"><span data-stu-id="4d39d-111"> Server is integrated into [ASP.NET Core Endpoint Routing](xref:fundamentals/routing).</span></span> <span data-ttu-id="4d39d-112">Aplikace ASP.NET Core je nakonfigurovaná tak, aby přijímala příchozí připojení pro interaktivní komponenty `MapBlazorHub` v `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="4d39d-112">An ASP.NET Core app is configured to accept incoming connections for interactive components with `MapBlazorHub` in `Startup.Configure`:</span></span>

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

<span data-ttu-id="4d39d-113">Nejtypickou konfigurací je směrování všech požadavků na Razor stránku, která funguje jako hostitel serverové součásti Blazor serverové aplikace.</span><span class="sxs-lookup"><span data-stu-id="4d39d-113">The most typical configuration is to route all requests to a Razor page, which acts as the host for the server-side part of the Blazor Server app.</span></span> <span data-ttu-id="4d39d-114">Podle konvence je stránka *hostitele* obvykle pojmenována *_Host. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="4d39d-114">By convention, the *host* page is usually named *_Host.cshtml*.</span></span> <span data-ttu-id="4d39d-115">Trasa zadaná v hostitelském souboru se nazývá *záložní trasa* , protože v porovnání s trasou funguje s nízkou prioritou.</span><span class="sxs-lookup"><span data-stu-id="4d39d-115">The route specified in the host file is called a *fallback route* because it operates with a low priority in route matching.</span></span> <span data-ttu-id="4d39d-116">Záložní trasa se bere v úvahu v případě, že se jiné trasy neshodují.</span><span class="sxs-lookup"><span data-stu-id="4d39d-116">The fallback route is considered when other routes don't match.</span></span> <span data-ttu-id="4d39d-117">To umožňuje aplikaci používat jiné řadiče a stránky bez narušování Blazor aplikace serveru.</span><span class="sxs-lookup"><span data-stu-id="4d39d-117">This allows the app to use others controllers and pages without interfering with the Blazor Server app.</span></span>

## <a name="route-templates"></a><span data-ttu-id="4d39d-118">Šablony směrování</span><span class="sxs-lookup"><span data-stu-id="4d39d-118">Route templates</span></span>

<span data-ttu-id="4d39d-119">Tato `Router` součást umožňuje směrování na jednotlivé komponenty se zadanou trasou.</span><span class="sxs-lookup"><span data-stu-id="4d39d-119">The `Router` component enables routing to each component with a specified route.</span></span> <span data-ttu-id="4d39d-120">`Router`Komponenta se zobrazí v souboru *App. Razor* :</span><span class="sxs-lookup"><span data-stu-id="4d39d-120">The `Router` component appears in the *App.razor* file:</span></span>

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

<span data-ttu-id="4d39d-121">Je-li soubor *. Razor* s `@page` direktivou kompilován, je k dispozici vygenerovaná třída, která <xref:Microsoft.AspNetCore.Components.RouteAttribute> Určuje šablonu trasy.</span><span class="sxs-lookup"><span data-stu-id="4d39d-121">When a *.razor* file with an `@page` directive is compiled, the generated class is provided a <xref:Microsoft.AspNetCore.Components.RouteAttribute> specifying the route template.</span></span>

<span data-ttu-id="4d39d-122">Za běhu `RouteView` komponenty:</span><span class="sxs-lookup"><span data-stu-id="4d39d-122">At runtime, the `RouteView` component:</span></span>

* <span data-ttu-id="4d39d-123">Přijímá `RouteData` od `Router` spolu s požadovanými parametry.</span><span class="sxs-lookup"><span data-stu-id="4d39d-123">Receives the `RouteData` from the `Router` along with any desired parameters.</span></span>
* <span data-ttu-id="4d39d-124">Vykreslí určenou komponentu pomocí jejího rozložení (nebo volitelného výchozího rozložení) pomocí zadaných parametrů.</span><span class="sxs-lookup"><span data-stu-id="4d39d-124">Renders the specified component with its layout (or an optional default layout) using the specified parameters.</span></span>

<span data-ttu-id="4d39d-125">Volitelně můžete zadat `DefaultLayout` parametr s třídou rozložení, který se má použít pro součásti, které neurčují rozložení.</span><span class="sxs-lookup"><span data-stu-id="4d39d-125">You can optionally specify a `DefaultLayout` parameter with a layout class to use for components that don't specify a layout.</span></span> <span data-ttu-id="4d39d-126">Výchozí Blazor šablony určují `MainLayout` komponentu.</span><span class="sxs-lookup"><span data-stu-id="4d39d-126">The default Blazor templates specify the `MainLayout` component.</span></span> <span data-ttu-id="4d39d-127">*MainLayout. Razor* se nachází ve *sdílené* složce projektu šablony.</span><span class="sxs-lookup"><span data-stu-id="4d39d-127">*MainLayout.razor* is in the template project's *Shared* folder.</span></span> <span data-ttu-id="4d39d-128">Další informace o rozložení najdete v tématu <xref:blazor/layouts> .</span><span class="sxs-lookup"><span data-stu-id="4d39d-128">For more information on layouts, see <xref:blazor/layouts>.</span></span>

<span data-ttu-id="4d39d-129">Pro komponentu lze použít více šablon směrování.</span><span class="sxs-lookup"><span data-stu-id="4d39d-129">Multiple route templates can be applied to a component.</span></span> <span data-ttu-id="4d39d-130">Následující komponenta reaguje na požadavky pro `/BlazorRoute` a `/DifferentBlazorRoute` :</span><span class="sxs-lookup"><span data-stu-id="4d39d-130">The following component responds to requests for `/BlazorRoute` and `/DifferentBlazorRoute`:</span></span>

```razor
@page "/BlazorRoute"
@page "/DifferentBlazorRoute"

<h1>Blazor routing</h1>
```

> [!IMPORTANT]
> <span data-ttu-id="4d39d-131">Aby adresy URL byly správně přeloženy, musí aplikace obsahovat `<base>` značku v souboru *wwwroot/index.html* ( Blazor WebAssembly) nebo *Pages/_Host. cshtml* ( Blazor Server) se základní cestou aplikace zadanou v `href` atributu ( `<base href="/">` ).</span><span class="sxs-lookup"><span data-stu-id="4d39d-131">For URLs to resolve correctly, the app must include a `<base>` tag in its *wwwroot/index.html* file (Blazor WebAssembly) or *Pages/_Host.cshtml* file (Blazor Server) with the app base path specified in the `href` attribute (`<base href="/">`).</span></span> <span data-ttu-id="4d39d-132">Další informace naleznete v tématu <xref:host-and-deploy/blazor/index#app-base-path>.</span><span class="sxs-lookup"><span data-stu-id="4d39d-132">For more information, see <xref:host-and-deploy/blazor/index#app-base-path>.</span></span>

## <a name="provide-custom-content-when-content-isnt-found"></a><span data-ttu-id="4d39d-133">Poskytnutí vlastního obsahu, když se nenalezne obsah</span><span class="sxs-lookup"><span data-stu-id="4d39d-133">Provide custom content when content isn't found</span></span>

<span data-ttu-id="4d39d-134">`Router`Komponenta umožňuje aplikaci zadat vlastní obsah, pokud se pro požadovanou trasu nenajde obsah.</span><span class="sxs-lookup"><span data-stu-id="4d39d-134">The `Router` component allows the app to specify custom content if content isn't found for the requested route.</span></span>

<span data-ttu-id="4d39d-135">V souboru *App. Razor* nastavte vlastní obsah v `NotFound` parametru šablony `Router` součásti:</span><span class="sxs-lookup"><span data-stu-id="4d39d-135">In the *App.razor* file, set custom content in the `NotFound` template parameter of the `Router` component:</span></span>

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

<span data-ttu-id="4d39d-136">Obsah `<NotFound>` značek může zahrnovat libovolné položky, jako jsou například jiné interaktivní součásti.</span><span class="sxs-lookup"><span data-stu-id="4d39d-136">The content of `<NotFound>` tags can include arbitrary items, such as other interactive components.</span></span> <span data-ttu-id="4d39d-137">Chcete-li použít výchozí rozložení `NotFound` obsahu, přečtěte si téma <xref:blazor/layouts> .</span><span class="sxs-lookup"><span data-stu-id="4d39d-137">To apply a default layout to `NotFound` content, see <xref:blazor/layouts>.</span></span>

## <a name="route-to-components-from-multiple-assemblies"></a><span data-ttu-id="4d39d-138">Směrování na součásti z více sestavení</span><span class="sxs-lookup"><span data-stu-id="4d39d-138">Route to components from multiple assemblies</span></span>

<span data-ttu-id="4d39d-139">Použijte `AdditionalAssemblies` parametr k určení dalších sestavení, `Router` která má součást při hledání směrovatelných komponent zvážit.</span><span class="sxs-lookup"><span data-stu-id="4d39d-139">Use the `AdditionalAssemblies` parameter to specify additional assemblies for the `Router` component to consider when searching for routable components.</span></span> <span data-ttu-id="4d39d-140">Zadaná sestavení jsou kromě zadaného sestavení považována za `AppAssembly` .</span><span class="sxs-lookup"><span data-stu-id="4d39d-140">Specified assemblies are considered in addition to the `AppAssembly`-specified assembly.</span></span> <span data-ttu-id="4d39d-141">V následujícím příkladu `Component1` je směrovatelný komponenta definovaná v odkazované knihovně tříd.</span><span class="sxs-lookup"><span data-stu-id="4d39d-141">In the following example, `Component1` is a routable component defined in a referenced class library.</span></span> <span data-ttu-id="4d39d-142">Následující `AdditionalAssemblies` příklad vede k podpoře směrování pro `Component1` :</span><span class="sxs-lookup"><span data-stu-id="4d39d-142">The following `AdditionalAssemblies` example results in routing support for `Component1`:</span></span>

```razor
<Router
    AppAssembly="typeof(Program).Assembly"
    AdditionalAssemblies="new[] { typeof(Component1).Assembly }">
    ...
</Router>
```

## <a name="route-parameters"></a><span data-ttu-id="4d39d-143">Parametry směrování</span><span class="sxs-lookup"><span data-stu-id="4d39d-143">Route parameters</span></span>

<span data-ttu-id="4d39d-144">Směrovač používá parametry směrování k naplnění odpovídajících parametrů komponenty se stejným názvem (bez rozlišení velkých a malých písmen):</span><span class="sxs-lookup"><span data-stu-id="4d39d-144">The router uses route parameters to populate the corresponding component parameters with the same name (case insensitive):</span></span>

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

<span data-ttu-id="4d39d-145">Volitelné parametry nejsou podporovány.</span><span class="sxs-lookup"><span data-stu-id="4d39d-145">Optional parameters aren't supported.</span></span> <span data-ttu-id="4d39d-146">`@page`V předchozím příkladu jsou aplikovány dvě direktivy.</span><span class="sxs-lookup"><span data-stu-id="4d39d-146">Two `@page` directives are applied in the previous example.</span></span> <span data-ttu-id="4d39d-147">První umožňuje navigaci na součást bez parametru.</span><span class="sxs-lookup"><span data-stu-id="4d39d-147">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="4d39d-148">Druhá `@page` direktiva převezme `{text}` parametr Route a přiřadí hodnotu `Text` Vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="4d39d-148">The second `@page` directive takes the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

## <a name="route-constraints"></a><span data-ttu-id="4d39d-149">Omezení trasy</span><span class="sxs-lookup"><span data-stu-id="4d39d-149">Route constraints</span></span>

<span data-ttu-id="4d39d-150">Omezení trasy vynutilo typ shodný s typem pro segment směrování do součásti.</span><span class="sxs-lookup"><span data-stu-id="4d39d-150">A route constraint enforces type matching on a route segment to a component.</span></span>

<span data-ttu-id="4d39d-151">V následujícím příkladu trasa k `Users` součásti odpovídá pouze v případě, že:</span><span class="sxs-lookup"><span data-stu-id="4d39d-151">In the following example, the route to the `Users` component only matches if:</span></span>

* <span data-ttu-id="4d39d-152">`Id`V adrese URL požadavku je přítomen segment směrování.</span><span class="sxs-lookup"><span data-stu-id="4d39d-152">An `Id` route segment is present on the request URL.</span></span>
* <span data-ttu-id="4d39d-153">`Id`Segment je celé číslo ( `int` ).</span><span class="sxs-lookup"><span data-stu-id="4d39d-153">The `Id` segment is an integer (`int`).</span></span>

[!code-razor[](routing/samples_snapshot/3.x/Constraint.razor?highlight=1)]

<span data-ttu-id="4d39d-154">K dispozici jsou omezení tras uvedená v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="4d39d-154">The route constraints shown in the following table are available.</span></span> <span data-ttu-id="4d39d-155">Pro omezení trasy, která se shodují s invariantní jazykovou verzí, se podívejte na upozornění pod tabulkou, kde najdete další informace.</span><span class="sxs-lookup"><span data-stu-id="4d39d-155">For the route constraints that match with the invariant culture, see the warning below the table for more information.</span></span>

| <span data-ttu-id="4d39d-156">Jedinečn</span><span class="sxs-lookup"><span data-stu-id="4d39d-156">Constraint</span></span> | <span data-ttu-id="4d39d-157">Příklad</span><span class="sxs-lookup"><span data-stu-id="4d39d-157">Example</span></span>           | <span data-ttu-id="4d39d-158">Příklady shody</span><span class="sxs-lookup"><span data-stu-id="4d39d-158">Example Matches</span></span>                                                                  | <span data-ttu-id="4d39d-159">Invariantní</span><span class="sxs-lookup"><span data-stu-id="4d39d-159">Invariant</span></span><br><span data-ttu-id="4d39d-160">jazyková verze</span><span class="sxs-lookup"><span data-stu-id="4d39d-160">culture</span></span><br><span data-ttu-id="4d39d-161">shoda</span><span class="sxs-lookup"><span data-stu-id="4d39d-161">matching</span></span> |
| ---
<span data-ttu-id="4d39d-162">title: ' ASP.NET Core Blazor Routing ' autora: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="4d39d-162">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4d39d-163">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4d39d-163">'Blazor'</span></span>
- <span data-ttu-id="4d39d-164">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4d39d-164">'Identity'</span></span>
- <span data-ttu-id="4d39d-165">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4d39d-165">'Let's Encrypt'</span></span>
- <span data-ttu-id="4d39d-166">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4d39d-166">'Razor'</span></span>
- <span data-ttu-id="4d39d-167">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="4d39d-167">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4d39d-168">title: ' ASP.NET Core Blazor Routing ' autora: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="4d39d-168">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4d39d-169">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4d39d-169">'Blazor'</span></span>
- <span data-ttu-id="4d39d-170">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4d39d-170">'Identity'</span></span>
- <span data-ttu-id="4d39d-171">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4d39d-171">'Let's Encrypt'</span></span>
- <span data-ttu-id="4d39d-172">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4d39d-172">'Razor'</span></span>
- <span data-ttu-id="4d39d-173">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="4d39d-173">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4d39d-174">title: ' ASP.NET Core Blazor Routing ' autora: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="4d39d-174">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4d39d-175">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4d39d-175">'Blazor'</span></span>
- <span data-ttu-id="4d39d-176">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4d39d-176">'Identity'</span></span>
- <span data-ttu-id="4d39d-177">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4d39d-177">'Let's Encrypt'</span></span>
- <span data-ttu-id="4d39d-178">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4d39d-178">'Razor'</span></span>
- <span data-ttu-id="4d39d-179">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="4d39d-179">'SignalR' uid:</span></span> 

<span data-ttu-id="4d39d-180">----- | ---Název: ' ASP.NET Core Blazor Směrování ' Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="4d39d-180">----- | --- title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4d39d-181">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4d39d-181">'Blazor'</span></span>
- <span data-ttu-id="4d39d-182">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4d39d-182">'Identity'</span></span>
- <span data-ttu-id="4d39d-183">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4d39d-183">'Let's Encrypt'</span></span>
- <span data-ttu-id="4d39d-184">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4d39d-184">'Razor'</span></span>
- <span data-ttu-id="4d39d-185">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="4d39d-185">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4d39d-186">title: ' ASP.NET Core Blazor Routing ' autora: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="4d39d-186">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4d39d-187">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4d39d-187">'Blazor'</span></span>
- <span data-ttu-id="4d39d-188">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4d39d-188">'Identity'</span></span>
- <span data-ttu-id="4d39d-189">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4d39d-189">'Let's Encrypt'</span></span>
- <span data-ttu-id="4d39d-190">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4d39d-190">'Razor'</span></span>
- <span data-ttu-id="4d39d-191">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="4d39d-191">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4d39d-192">title: ' ASP.NET Core Blazor Routing ' autora: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="4d39d-192">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4d39d-193">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4d39d-193">'Blazor'</span></span>
- <span data-ttu-id="4d39d-194">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4d39d-194">'Identity'</span></span>
- <span data-ttu-id="4d39d-195">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4d39d-195">'Let's Encrypt'</span></span>
- <span data-ttu-id="4d39d-196">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4d39d-196">'Razor'</span></span>
- <span data-ttu-id="4d39d-197">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="4d39d-197">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4d39d-198">title: ' ASP.NET Core Blazor Routing ' autora: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="4d39d-198">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4d39d-199">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4d39d-199">'Blazor'</span></span>
- <span data-ttu-id="4d39d-200">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4d39d-200">'Identity'</span></span>
- <span data-ttu-id="4d39d-201">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4d39d-201">'Let's Encrypt'</span></span>
- <span data-ttu-id="4d39d-202">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4d39d-202">'Razor'</span></span>
- <span data-ttu-id="4d39d-203">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="4d39d-203">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4d39d-204">title: ' ASP.NET Core Blazor Routing ' autora: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="4d39d-204">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4d39d-205">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4d39d-205">'Blazor'</span></span>
- <span data-ttu-id="4d39d-206">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4d39d-206">'Identity'</span></span>
- <span data-ttu-id="4d39d-207">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4d39d-207">'Let's Encrypt'</span></span>
- <span data-ttu-id="4d39d-208">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4d39d-208">'Razor'</span></span>
- <span data-ttu-id="4d39d-209">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="4d39d-209">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4d39d-210">title: ' ASP.NET Core Blazor Routing ' autora: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="4d39d-210">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4d39d-211">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4d39d-211">'Blazor'</span></span>
- <span data-ttu-id="4d39d-212">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4d39d-212">'Identity'</span></span>
- <span data-ttu-id="4d39d-213">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4d39d-213">'Let's Encrypt'</span></span>
- <span data-ttu-id="4d39d-214">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4d39d-214">'Razor'</span></span>
- <span data-ttu-id="4d39d-215">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="4d39d-215">'SignalR' uid:</span></span> 

<span data-ttu-id="4d39d-216">--------- | ---Název: ' ASP.NET Core Blazor Směrování ' Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="4d39d-216">--------- | --- title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4d39d-217">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4d39d-217">'Blazor'</span></span>
- <span data-ttu-id="4d39d-218">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4d39d-218">'Identity'</span></span>
- <span data-ttu-id="4d39d-219">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4d39d-219">'Let's Encrypt'</span></span>
- <span data-ttu-id="4d39d-220">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4d39d-220">'Razor'</span></span>
- <span data-ttu-id="4d39d-221">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="4d39d-221">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4d39d-222">title: ' ASP.NET Core Blazor Routing ' autora: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="4d39d-222">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4d39d-223">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4d39d-223">'Blazor'</span></span>
- <span data-ttu-id="4d39d-224">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4d39d-224">'Identity'</span></span>
- <span data-ttu-id="4d39d-225">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4d39d-225">'Let's Encrypt'</span></span>
- <span data-ttu-id="4d39d-226">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4d39d-226">'Razor'</span></span>
- <span data-ttu-id="4d39d-227">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="4d39d-227">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4d39d-228">title: ' ASP.NET Core Blazor Routing ' autora: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="4d39d-228">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4d39d-229">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4d39d-229">'Blazor'</span></span>
- <span data-ttu-id="4d39d-230">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4d39d-230">'Identity'</span></span>
- <span data-ttu-id="4d39d-231">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4d39d-231">'Let's Encrypt'</span></span>
- <span data-ttu-id="4d39d-232">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4d39d-232">'Razor'</span></span>
- <span data-ttu-id="4d39d-233">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="4d39d-233">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4d39d-234">title: ' ASP.NET Core Blazor Routing ' autora: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="4d39d-234">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4d39d-235">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4d39d-235">'Blazor'</span></span>
- <span data-ttu-id="4d39d-236">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4d39d-236">'Identity'</span></span>
- <span data-ttu-id="4d39d-237">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4d39d-237">'Let's Encrypt'</span></span>
- <span data-ttu-id="4d39d-238">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4d39d-238">'Razor'</span></span>
- <span data-ttu-id="4d39d-239">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="4d39d-239">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4d39d-240">title: ' ASP.NET Core Blazor Routing ' autora: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="4d39d-240">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4d39d-241">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4d39d-241">'Blazor'</span></span>
- <span data-ttu-id="4d39d-242">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4d39d-242">'Identity'</span></span>
- <span data-ttu-id="4d39d-243">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4d39d-243">'Let's Encrypt'</span></span>
- <span data-ttu-id="4d39d-244">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4d39d-244">'Razor'</span></span>
- <span data-ttu-id="4d39d-245">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="4d39d-245">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4d39d-246">title: ' ASP.NET Core Blazor Routing ' autora: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="4d39d-246">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4d39d-247">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4d39d-247">'Blazor'</span></span>
- <span data-ttu-id="4d39d-248">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4d39d-248">'Identity'</span></span>
- <span data-ttu-id="4d39d-249">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4d39d-249">'Let's Encrypt'</span></span>
- <span data-ttu-id="4d39d-250">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4d39d-250">'Razor'</span></span>
- <span data-ttu-id="4d39d-251">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="4d39d-251">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4d39d-252">title: ' ASP.NET Core Blazor Routing ' autora: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="4d39d-252">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4d39d-253">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4d39d-253">'Blazor'</span></span>
- <span data-ttu-id="4d39d-254">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4d39d-254">'Identity'</span></span>
- <span data-ttu-id="4d39d-255">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4d39d-255">'Let's Encrypt'</span></span>
- <span data-ttu-id="4d39d-256">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4d39d-256">'Razor'</span></span>
- <span data-ttu-id="4d39d-257">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="4d39d-257">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4d39d-258">title: ' ASP.NET Core Blazor Routing ' autora: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="4d39d-258">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4d39d-259">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4d39d-259">'Blazor'</span></span>
- <span data-ttu-id="4d39d-260">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4d39d-260">'Identity'</span></span>
- <span data-ttu-id="4d39d-261">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4d39d-261">'Let's Encrypt'</span></span>
- <span data-ttu-id="4d39d-262">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4d39d-262">'Razor'</span></span>
- <span data-ttu-id="4d39d-263">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="4d39d-263">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4d39d-264">title: ' ASP.NET Core Blazor Routing ' autora: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="4d39d-264">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4d39d-265">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4d39d-265">'Blazor'</span></span>
- <span data-ttu-id="4d39d-266">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4d39d-266">'Identity'</span></span>
- <span data-ttu-id="4d39d-267">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4d39d-267">'Let's Encrypt'</span></span>
- <span data-ttu-id="4d39d-268">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4d39d-268">'Razor'</span></span>
- <span data-ttu-id="4d39d-269">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="4d39d-269">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4d39d-270">title: ' ASP.NET Core Blazor Routing ' autora: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="4d39d-270">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4d39d-271">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4d39d-271">'Blazor'</span></span>
- <span data-ttu-id="4d39d-272">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4d39d-272">'Identity'</span></span>
- <span data-ttu-id="4d39d-273">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4d39d-273">'Let's Encrypt'</span></span>
- <span data-ttu-id="4d39d-274">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4d39d-274">'Razor'</span></span>
- <span data-ttu-id="4d39d-275">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="4d39d-275">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4d39d-276">title: ' ASP.NET Core Blazor Routing ' autora: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="4d39d-276">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4d39d-277">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4d39d-277">'Blazor'</span></span>
- <span data-ttu-id="4d39d-278">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4d39d-278">'Identity'</span></span>
- <span data-ttu-id="4d39d-279">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4d39d-279">'Let's Encrypt'</span></span>
- <span data-ttu-id="4d39d-280">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4d39d-280">'Razor'</span></span>
- <span data-ttu-id="4d39d-281">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="4d39d-281">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4d39d-282">title: ' ASP.NET Core Blazor Routing ' autora: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="4d39d-282">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4d39d-283">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4d39d-283">'Blazor'</span></span>
- <span data-ttu-id="4d39d-284">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4d39d-284">'Identity'</span></span>
- <span data-ttu-id="4d39d-285">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4d39d-285">'Let's Encrypt'</span></span>
- <span data-ttu-id="4d39d-286">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4d39d-286">'Razor'</span></span>
- <span data-ttu-id="4d39d-287">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="4d39d-287">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4d39d-288">title: ' ASP.NET Core Blazor Routing ' autora: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="4d39d-288">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4d39d-289">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4d39d-289">'Blazor'</span></span>
- <span data-ttu-id="4d39d-290">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4d39d-290">'Identity'</span></span>
- <span data-ttu-id="4d39d-291">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4d39d-291">'Let's Encrypt'</span></span>
- <span data-ttu-id="4d39d-292">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4d39d-292">'Razor'</span></span>
- <span data-ttu-id="4d39d-293">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="4d39d-293">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4d39d-294">title: ' ASP.NET Core Blazor Routing ' autora: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="4d39d-294">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4d39d-295">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4d39d-295">'Blazor'</span></span>
- <span data-ttu-id="4d39d-296">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4d39d-296">'Identity'</span></span>
- <span data-ttu-id="4d39d-297">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4d39d-297">'Let's Encrypt'</span></span>
- <span data-ttu-id="4d39d-298">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4d39d-298">'Razor'</span></span>
- <span data-ttu-id="4d39d-299">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="4d39d-299">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4d39d-300">title: ' ASP.NET Core Blazor Routing ' autora: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="4d39d-300">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4d39d-301">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4d39d-301">'Blazor'</span></span>
- <span data-ttu-id="4d39d-302">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4d39d-302">'Identity'</span></span>
- <span data-ttu-id="4d39d-303">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4d39d-303">'Let's Encrypt'</span></span>
- <span data-ttu-id="4d39d-304">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4d39d-304">'Razor'</span></span>
- <span data-ttu-id="4d39d-305">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="4d39d-305">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4d39d-306">title: ' ASP.NET Core Blazor Routing ' autora: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="4d39d-306">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4d39d-307">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4d39d-307">'Blazor'</span></span>
- <span data-ttu-id="4d39d-308">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4d39d-308">'Identity'</span></span>
- <span data-ttu-id="4d39d-309">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4d39d-309">'Let's Encrypt'</span></span>
- <span data-ttu-id="4d39d-310">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4d39d-310">'Razor'</span></span>
- <span data-ttu-id="4d39d-311">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="4d39d-311">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4d39d-312">title: ' ASP.NET Core Blazor Routing ' autora: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="4d39d-312">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4d39d-313">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4d39d-313">'Blazor'</span></span>
- <span data-ttu-id="4d39d-314">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4d39d-314">'Identity'</span></span>
- <span data-ttu-id="4d39d-315">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4d39d-315">'Let's Encrypt'</span></span>
- <span data-ttu-id="4d39d-316">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4d39d-316">'Razor'</span></span>
- <span data-ttu-id="4d39d-317">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="4d39d-317">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4d39d-318">title: ' ASP.NET Core Blazor Routing ' autora: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="4d39d-318">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4d39d-319">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4d39d-319">'Blazor'</span></span>
- <span data-ttu-id="4d39d-320">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4d39d-320">'Identity'</span></span>
- <span data-ttu-id="4d39d-321">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4d39d-321">'Let's Encrypt'</span></span>
- <span data-ttu-id="4d39d-322">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4d39d-322">'Razor'</span></span>
- <span data-ttu-id="4d39d-323">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="4d39d-323">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4d39d-324">title: ' ASP.NET Core Blazor Routing ' autora: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="4d39d-324">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4d39d-325">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4d39d-325">'Blazor'</span></span>
- <span data-ttu-id="4d39d-326">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4d39d-326">'Identity'</span></span>
- <span data-ttu-id="4d39d-327">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4d39d-327">'Let's Encrypt'</span></span>
- <span data-ttu-id="4d39d-328">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4d39d-328">'Razor'</span></span>
- <span data-ttu-id="4d39d-329">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="4d39d-329">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4d39d-330">title: ' ASP.NET Core Blazor Routing ' autora: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="4d39d-330">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4d39d-331">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4d39d-331">'Blazor'</span></span>
- <span data-ttu-id="4d39d-332">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4d39d-332">'Identity'</span></span>
- <span data-ttu-id="4d39d-333">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4d39d-333">'Let's Encrypt'</span></span>
- <span data-ttu-id="4d39d-334">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4d39d-334">'Razor'</span></span>
- <span data-ttu-id="4d39d-335">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="4d39d-335">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4d39d-336">title: ' ASP.NET Core Blazor Routing ' autora: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="4d39d-336">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4d39d-337">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4d39d-337">'Blazor'</span></span>
- <span data-ttu-id="4d39d-338">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4d39d-338">'Identity'</span></span>
- <span data-ttu-id="4d39d-339">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4d39d-339">'Let's Encrypt'</span></span>
- <span data-ttu-id="4d39d-340">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4d39d-340">'Razor'</span></span>
- <span data-ttu-id="4d39d-341">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="4d39d-341">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4d39d-342">title: ' ASP.NET Core Blazor Routing ' autora: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="4d39d-342">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4d39d-343">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4d39d-343">'Blazor'</span></span>
- <span data-ttu-id="4d39d-344">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4d39d-344">'Identity'</span></span>
- <span data-ttu-id="4d39d-345">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4d39d-345">'Let's Encrypt'</span></span>
- <span data-ttu-id="4d39d-346">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4d39d-346">'Razor'</span></span>
- <span data-ttu-id="4d39d-347">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="4d39d-347">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4d39d-348">title: ' ASP.NET Core Blazor Routing ' autora: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="4d39d-348">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4d39d-349">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4d39d-349">'Blazor'</span></span>
- <span data-ttu-id="4d39d-350">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4d39d-350">'Identity'</span></span>
- <span data-ttu-id="4d39d-351">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4d39d-351">'Let's Encrypt'</span></span>
- <span data-ttu-id="4d39d-352">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4d39d-352">'Razor'</span></span>
- <span data-ttu-id="4d39d-353">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="4d39d-353">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4d39d-354">title: ' ASP.NET Core Blazor Routing ' autora: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="4d39d-354">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4d39d-355">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4d39d-355">'Blazor'</span></span>
- <span data-ttu-id="4d39d-356">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4d39d-356">'Identity'</span></span>
- <span data-ttu-id="4d39d-357">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4d39d-357">'Let's Encrypt'</span></span>
- <span data-ttu-id="4d39d-358">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4d39d-358">'Razor'</span></span>
- <span data-ttu-id="4d39d-359">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="4d39d-359">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4d39d-360">title: ' ASP.NET Core Blazor Routing ' autora: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="4d39d-360">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4d39d-361">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4d39d-361">'Blazor'</span></span>
- <span data-ttu-id="4d39d-362">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4d39d-362">'Identity'</span></span>
- <span data-ttu-id="4d39d-363">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4d39d-363">'Let's Encrypt'</span></span>
- <span data-ttu-id="4d39d-364">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4d39d-364">'Razor'</span></span>
- <span data-ttu-id="4d39d-365">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="4d39d-365">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4d39d-366">title: ' ASP.NET Core Blazor Routing ' autora: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="4d39d-366">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4d39d-367">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4d39d-367">'Blazor'</span></span>
- <span data-ttu-id="4d39d-368">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4d39d-368">'Identity'</span></span>
- <span data-ttu-id="4d39d-369">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4d39d-369">'Let's Encrypt'</span></span>
- <span data-ttu-id="4d39d-370">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4d39d-370">'Razor'</span></span>
- <span data-ttu-id="4d39d-371">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="4d39d-371">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4d39d-372">title: ' ASP.NET Core Blazor Routing ' autora: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="4d39d-372">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4d39d-373">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4d39d-373">'Blazor'</span></span>
- <span data-ttu-id="4d39d-374">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4d39d-374">'Identity'</span></span>
- <span data-ttu-id="4d39d-375">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4d39d-375">'Let's Encrypt'</span></span>
- <span data-ttu-id="4d39d-376">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4d39d-376">'Razor'</span></span>
- <span data-ttu-id="4d39d-377">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="4d39d-377">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4d39d-378">title: ' ASP.NET Core Blazor Routing ' autora: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="4d39d-378">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4d39d-379">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4d39d-379">'Blazor'</span></span>
- <span data-ttu-id="4d39d-380">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4d39d-380">'Identity'</span></span>
- <span data-ttu-id="4d39d-381">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4d39d-381">'Let's Encrypt'</span></span>
- <span data-ttu-id="4d39d-382">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4d39d-382">'Razor'</span></span>
- <span data-ttu-id="4d39d-383">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="4d39d-383">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4d39d-384">title: ' ASP.NET Core Blazor Routing ' autora: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="4d39d-384">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4d39d-385">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4d39d-385">'Blazor'</span></span>
- <span data-ttu-id="4d39d-386">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4d39d-386">'Identity'</span></span>
- <span data-ttu-id="4d39d-387">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4d39d-387">'Let's Encrypt'</span></span>
- <span data-ttu-id="4d39d-388">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4d39d-388">'Razor'</span></span>
- <span data-ttu-id="4d39d-389">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="4d39d-389">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4d39d-390">title: ' ASP.NET Core Blazor Routing ' autora: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="4d39d-390">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4d39d-391">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4d39d-391">'Blazor'</span></span>
- <span data-ttu-id="4d39d-392">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4d39d-392">'Identity'</span></span>
- <span data-ttu-id="4d39d-393">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4d39d-393">'Let's Encrypt'</span></span>
- <span data-ttu-id="4d39d-394">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4d39d-394">'Razor'</span></span>
- <span data-ttu-id="4d39d-395">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="4d39d-395">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4d39d-396">title: ' ASP.NET Core Blazor Routing ' autora: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="4d39d-396">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4d39d-397">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4d39d-397">'Blazor'</span></span>
- <span data-ttu-id="4d39d-398">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4d39d-398">'Identity'</span></span>
- <span data-ttu-id="4d39d-399">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4d39d-399">'Let's Encrypt'</span></span>
- <span data-ttu-id="4d39d-400">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4d39d-400">'Razor'</span></span>
- <span data-ttu-id="4d39d-401">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="4d39d-401">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4d39d-402">title: ' ASP.NET Core Blazor Routing ' autora: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="4d39d-402">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4d39d-403">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4d39d-403">'Blazor'</span></span>
- <span data-ttu-id="4d39d-404">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4d39d-404">'Identity'</span></span>
- <span data-ttu-id="4d39d-405">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4d39d-405">'Let's Encrypt'</span></span>
- <span data-ttu-id="4d39d-406">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4d39d-406">'Razor'</span></span>
- <span data-ttu-id="4d39d-407">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="4d39d-407">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4d39d-408">title: ' ASP.NET Core Blazor Routing ' autora: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="4d39d-408">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4d39d-409">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4d39d-409">'Blazor'</span></span>
- <span data-ttu-id="4d39d-410">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4d39d-410">'Identity'</span></span>
- <span data-ttu-id="4d39d-411">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4d39d-411">'Let's Encrypt'</span></span>
- <span data-ttu-id="4d39d-412">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4d39d-412">'Razor'</span></span>
- <span data-ttu-id="4d39d-413">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="4d39d-413">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4d39d-414">title: ' ASP.NET Core Blazor Routing ' autora: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="4d39d-414">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4d39d-415">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4d39d-415">'Blazor'</span></span>
- <span data-ttu-id="4d39d-416">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4d39d-416">'Identity'</span></span>
- <span data-ttu-id="4d39d-417">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4d39d-417">'Let's Encrypt'</span></span>
- <span data-ttu-id="4d39d-418">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4d39d-418">'Razor'</span></span>
- <span data-ttu-id="4d39d-419">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="4d39d-419">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4d39d-420">title: ' ASP.NET Core Blazor Routing ' autora: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="4d39d-420">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4d39d-421">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4d39d-421">'Blazor'</span></span>
- <span data-ttu-id="4d39d-422">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4d39d-422">'Identity'</span></span>
- <span data-ttu-id="4d39d-423">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4d39d-423">'Let's Encrypt'</span></span>
- <span data-ttu-id="4d39d-424">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4d39d-424">'Razor'</span></span>
- <span data-ttu-id="4d39d-425">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="4d39d-425">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4d39d-426">title: ' ASP.NET Core Blazor Routing ' autora: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="4d39d-426">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4d39d-427">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4d39d-427">'Blazor'</span></span>
- <span data-ttu-id="4d39d-428">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4d39d-428">'Identity'</span></span>
- <span data-ttu-id="4d39d-429">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4d39d-429">'Let's Encrypt'</span></span>
- <span data-ttu-id="4d39d-430">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4d39d-430">'Razor'</span></span>
- <span data-ttu-id="4d39d-431">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="4d39d-431">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4d39d-432">title: ' ASP.NET Core Blazor Routing ' autora: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="4d39d-432">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4d39d-433">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4d39d-433">'Blazor'</span></span>
- <span data-ttu-id="4d39d-434">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4d39d-434">'Identity'</span></span>
- <span data-ttu-id="4d39d-435">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4d39d-435">'Let's Encrypt'</span></span>
- <span data-ttu-id="4d39d-436">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4d39d-436">'Razor'</span></span>
- <span data-ttu-id="4d39d-437">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="4d39d-437">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4d39d-438">title: ' ASP.NET Core Blazor Routing ' autora: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="4d39d-438">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4d39d-439">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4d39d-439">'Blazor'</span></span>
- <span data-ttu-id="4d39d-440">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4d39d-440">'Identity'</span></span>
- <span data-ttu-id="4d39d-441">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4d39d-441">'Let's Encrypt'</span></span>
- <span data-ttu-id="4d39d-442">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4d39d-442">'Razor'</span></span>
- <span data-ttu-id="4d39d-443">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="4d39d-443">'SignalR' uid:</span></span> 

<span data-ttu-id="4d39d-444">---------------------------------------- | :---název: ' ASP.NET Core Blazor Směrování ' Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="4d39d-444">---------------------------------------- | :--- title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4d39d-445">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4d39d-445">'Blazor'</span></span>
- <span data-ttu-id="4d39d-446">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4d39d-446">'Identity'</span></span>
- <span data-ttu-id="4d39d-447">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4d39d-447">'Let's Encrypt'</span></span>
- <span data-ttu-id="4d39d-448">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4d39d-448">'Razor'</span></span>
- <span data-ttu-id="4d39d-449">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="4d39d-449">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4d39d-450">title: ' ASP.NET Core Blazor Routing ' autora: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="4d39d-450">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4d39d-451">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4d39d-451">'Blazor'</span></span>
- <span data-ttu-id="4d39d-452">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4d39d-452">'Identity'</span></span>
- <span data-ttu-id="4d39d-453">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4d39d-453">'Let's Encrypt'</span></span>
- <span data-ttu-id="4d39d-454">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4d39d-454">'Razor'</span></span>
- <span data-ttu-id="4d39d-455">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="4d39d-455">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4d39d-456">title: ' ASP.NET Core Blazor Routing ' autora: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="4d39d-456">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4d39d-457">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4d39d-457">'Blazor'</span></span>
- <span data-ttu-id="4d39d-458">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4d39d-458">'Identity'</span></span>
- <span data-ttu-id="4d39d-459">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4d39d-459">'Let's Encrypt'</span></span>
- <span data-ttu-id="4d39d-460">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4d39d-460">'Razor'</span></span>
- <span data-ttu-id="4d39d-461">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="4d39d-461">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4d39d-462">title: ' ASP.NET Core Blazor Routing ' autora: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="4d39d-462">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4d39d-463">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4d39d-463">'Blazor'</span></span>
- <span data-ttu-id="4d39d-464">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4d39d-464">'Identity'</span></span>
- <span data-ttu-id="4d39d-465">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4d39d-465">'Let's Encrypt'</span></span>
- <span data-ttu-id="4d39d-466">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4d39d-466">'Razor'</span></span>
- <span data-ttu-id="4d39d-467">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="4d39d-467">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4d39d-468">title: ' ASP.NET Core Blazor Routing ' autora: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="4d39d-468">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4d39d-469">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4d39d-469">'Blazor'</span></span>
- <span data-ttu-id="4d39d-470">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4d39d-470">'Identity'</span></span>
- <span data-ttu-id="4d39d-471">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4d39d-471">'Let's Encrypt'</span></span>
- <span data-ttu-id="4d39d-472">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4d39d-472">'Razor'</span></span>
- <span data-ttu-id="4d39d-473">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="4d39d-473">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4d39d-474">title: ' ASP.NET Core Blazor Routing ' autora: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="4d39d-474">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4d39d-475">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4d39d-475">'Blazor'</span></span>
- <span data-ttu-id="4d39d-476">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4d39d-476">'Identity'</span></span>
- <span data-ttu-id="4d39d-477">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4d39d-477">'Let's Encrypt'</span></span>
- <span data-ttu-id="4d39d-478">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4d39d-478">'Razor'</span></span>
- <span data-ttu-id="4d39d-479">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="4d39d-479">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4d39d-480">title: ' ASP.NET Core Blazor Routing ' autora: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="4d39d-480">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4d39d-481">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4d39d-481">'Blazor'</span></span>
- <span data-ttu-id="4d39d-482">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4d39d-482">'Identity'</span></span>
- <span data-ttu-id="4d39d-483">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4d39d-483">'Let's Encrypt'</span></span>
- <span data-ttu-id="4d39d-484">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4d39d-484">'Razor'</span></span>
- <span data-ttu-id="4d39d-485">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="4d39d-485">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4d39d-486">title: ' ASP.NET Core Blazor Routing ' autora: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="4d39d-486">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4d39d-487">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4d39d-487">'Blazor'</span></span>
- <span data-ttu-id="4d39d-488">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4d39d-488">'Identity'</span></span>
- <span data-ttu-id="4d39d-489">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4d39d-489">'Let's Encrypt'</span></span>
- <span data-ttu-id="4d39d-490">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4d39d-490">'Razor'</span></span>
- <span data-ttu-id="4d39d-491">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="4d39d-491">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4d39d-492">title: ' ASP.NET Core Blazor Routing ' autora: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="4d39d-492">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4d39d-493">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4d39d-493">'Blazor'</span></span>
- <span data-ttu-id="4d39d-494">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4d39d-494">'Identity'</span></span>
- <span data-ttu-id="4d39d-495">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4d39d-495">'Let's Encrypt'</span></span>
- <span data-ttu-id="4d39d-496">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4d39d-496">'Razor'</span></span>
- <span data-ttu-id="4d39d-497">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="4d39d-497">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4d39d-498">title: ' ASP.NET Core Blazor Routing ' autora: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="4d39d-498">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4d39d-499">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4d39d-499">'Blazor'</span></span>
- <span data-ttu-id="4d39d-500">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4d39d-500">'Identity'</span></span>
- <span data-ttu-id="4d39d-501">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4d39d-501">'Let's Encrypt'</span></span>
- <span data-ttu-id="4d39d-502">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4d39d-502">'Razor'</span></span>
- <span data-ttu-id="4d39d-503">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="4d39d-503">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4d39d-504">title: ' ASP.NET Core Blazor Routing ' autora: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="4d39d-504">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4d39d-505">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4d39d-505">'Blazor'</span></span>
- <span data-ttu-id="4d39d-506">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4d39d-506">'Identity'</span></span>
- <span data-ttu-id="4d39d-507">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4d39d-507">'Let's Encrypt'</span></span>
- <span data-ttu-id="4d39d-508">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4d39d-508">'Razor'</span></span>
- <span data-ttu-id="4d39d-509">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="4d39d-509">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4d39d-510">title: ' ASP.NET Core Blazor Routing ' autora: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="4d39d-510">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4d39d-511">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4d39d-511">'Blazor'</span></span>
- <span data-ttu-id="4d39d-512">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4d39d-512">'Identity'</span></span>
- <span data-ttu-id="4d39d-513">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4d39d-513">'Let's Encrypt'</span></span>
- <span data-ttu-id="4d39d-514">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4d39d-514">'Razor'</span></span>
- <span data-ttu-id="4d39d-515">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="4d39d-515">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4d39d-516">title: ' ASP.NET Core Blazor Routing ' autora: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="4d39d-516">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4d39d-517">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4d39d-517">'Blazor'</span></span>
- <span data-ttu-id="4d39d-518">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4d39d-518">'Identity'</span></span>
- <span data-ttu-id="4d39d-519">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4d39d-519">'Let's Encrypt'</span></span>
- <span data-ttu-id="4d39d-520">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4d39d-520">'Razor'</span></span>
- <span data-ttu-id="4d39d-521">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="4d39d-521">'SignalR' uid:</span></span> 

<span data-ttu-id="4d39d-522">---------------: | | `bool`     | `{active:bool}`   | `true`, `FALSE`                                                                  | Žádné | | `datetime` | `{dob:datetime}`  | `2016-12-31`, `2016-12-31 7:32pm`                                                | Ano | | `decimal`  | `{price:decimal}` | `49.99`, `-1,000.01`                                                             | Ano | | `double`   | `{weight:double}` | `1.234`, `-1,001.01e8`                                                           | Ano | | `float`    | `{weight:float}`  | `1.234`, `-1,001.01e8`                                                           | Ano | | `guid`     | `{id:guid}`       | `CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}` | Žádné | | `int`      | `{id:int}`        | `123456789`, `-123456789`                                                        | Ano | | `long`     | `{ticks:long}`    | `123456789`, `-123456789`                                                        | Ano |</span><span class="sxs-lookup"><span data-stu-id="4d39d-522">---------------: | | `bool`     | `{active:bool}`   | `true`, `FALSE`                                                                  | No                               | | `datetime` | `{dob:datetime}`  | `2016-12-31`, `2016-12-31 7:32pm`                                                | Yes                              | | `decimal`  | `{price:decimal}` | `49.99`, `-1,000.01`                                                             | Yes                              | | `double`   | `{weight:double}` | `1.234`, `-1,001.01e8`                                                           | Yes                              | | `float`    | `{weight:float}`  | `1.234`, `-1,001.01e8`                                                           | Yes                              | | `guid`     | `{id:guid}`       | `CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}` | No                               | | `int`      | `{id:int}`        | `123456789`, `-123456789`                                                        | Yes                              | | `long`     | `{ticks:long}`    | `123456789`, `-123456789`                                                        | Yes                              |</span></span>

> [!WARNING]
> <span data-ttu-id="4d39d-523">Omezení směrování, která ověřují adresu URL a jsou převedena na typ CLR (například `int` nebo `DateTime` ), vždy používají invariantní jazykovou verzi.</span><span class="sxs-lookup"><span data-stu-id="4d39d-523">Route constraints that verify the URL and are converted to a CLR type (such as `int` or `DateTime`) always use the invariant culture.</span></span> <span data-ttu-id="4d39d-524">Tato omezení předpokládají, že adresa URL nelze lokalizovat.</span><span class="sxs-lookup"><span data-stu-id="4d39d-524">These constraints assume that the URL is non-localizable.</span></span>

### <a name="routing-with-urls-that-contain-dots"></a><span data-ttu-id="4d39d-525">Směrování s adresami URL, které obsahují tečky</span><span class="sxs-lookup"><span data-stu-id="4d39d-525">Routing with URLs that contain dots</span></span>

<span data-ttu-id="4d39d-526">V Blazor serverových aplikacích je výchozí trasa v *_Host. cshtml* `/` ( `@page "/"` ).</span><span class="sxs-lookup"><span data-stu-id="4d39d-526">In Blazor Server apps, the default route in *_Host.cshtml* is `/` (`@page "/"`).</span></span> <span data-ttu-id="4d39d-527">Adresa URL požadavku, která obsahuje tečku ( `.` ), není shodná s výchozí cestou, protože adresa URL se zobrazí pro vyžádání souboru.</span><span class="sxs-lookup"><span data-stu-id="4d39d-527">A request URL that contains a dot (`.`) isn't matched by the default route because the URL appears to request a file.</span></span> <span data-ttu-id="4d39d-528">BlazorAplikace vrátí odpověď na *404, která nebyla nalezena* pro statický soubor, který neexistuje.</span><span class="sxs-lookup"><span data-stu-id="4d39d-528">A Blazor app returns a *404 - Not Found* response for a static file that doesn't exist.</span></span> <span data-ttu-id="4d39d-529">Chcete-li použít trasy, které obsahují tečku, nakonfigurujte *_Host. cshtml* s následující šablonou směrování:</span><span class="sxs-lookup"><span data-stu-id="4d39d-529">To use routes that contain a dot, configure *_Host.cshtml* with the following route template:</span></span>

```cshtml
@page "/{**path}"
```

<span data-ttu-id="4d39d-530">`"/{**path}"`Šablona obsahuje:</span><span class="sxs-lookup"><span data-stu-id="4d39d-530">The `"/{**path}"` template includes:</span></span>

* <span data-ttu-id="4d39d-531">Dvojitá hvězdička *– veškerá* syntaxe ( `**` ) pro zachycení cesty mezi více hranicemi složek bez kódování lomítka ( `/` ).</span><span class="sxs-lookup"><span data-stu-id="4d39d-531">Double-asterisk *catch-all* syntax (`**`) to capture the path across multiple folder boundaries without encoding forward slashes (`/`).</span></span>
* <span data-ttu-id="4d39d-532">`path`název parametru trasy.</span><span class="sxs-lookup"><span data-stu-id="4d39d-532">`path` route parameter name.</span></span>

> [!NOTE]
> <span data-ttu-id="4d39d-533">*Catch-All* parametr Syntax ( `*` / `**` ) není **not** v Razor součástech (*. Razor*) podporován.</span><span class="sxs-lookup"><span data-stu-id="4d39d-533">*Catch-all* parameter syntax (`*`/`**`) is **not** supported in Razor components (*.razor*).</span></span>

<span data-ttu-id="4d39d-534">Další informace naleznete v tématu <xref:fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="4d39d-534">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="navlink-component"></a><span data-ttu-id="4d39d-535">Komponenta NavLink</span><span class="sxs-lookup"><span data-stu-id="4d39d-535">NavLink component</span></span>

<span data-ttu-id="4d39d-536">Použít `NavLink` komponentu namísto prvků hypertextového odkazu HTML ( `<a>` ) při vytváření navigačních odkazů.</span><span class="sxs-lookup"><span data-stu-id="4d39d-536">Use a `NavLink` component in place of HTML hyperlink elements (`<a>`) when creating navigation links.</span></span> <span data-ttu-id="4d39d-537">`NavLink`Komponenta se chová jako `<a>` element s tím rozdílem, že přepíná `active` třídu CSS na základě toho, zda `href` odpovídá aktuální adrese URL.</span><span class="sxs-lookup"><span data-stu-id="4d39d-537">A `NavLink` component behaves like an `<a>` element, except it toggles an `active` CSS class based on whether its `href` matches the current URL.</span></span> <span data-ttu-id="4d39d-538">`active`Třída pomáhá uživateli pochopit, která stránka je aktivní stránkou mezi zobrazenými navigačními odkazy.</span><span class="sxs-lookup"><span data-stu-id="4d39d-538">The `active` class helps a user understand which page is the active page among the navigation links displayed.</span></span>

<span data-ttu-id="4d39d-539">Následující `NavMenu` Komponenta vytvoří navigační panel [bootstrap](https://getbootstrap.com/docs/) , který ukazuje, jak používat `NavLink` komponenty:</span><span class="sxs-lookup"><span data-stu-id="4d39d-539">The following `NavMenu` component creates a [Bootstrap](https://getbootstrap.com/docs/) navigation bar that demonstrates how to use `NavLink` components:</span></span>

[!code-razor[](routing/samples_snapshot/3.x/NavMenu.razor?highlight=4,9)]

<span data-ttu-id="4d39d-540">Existují dvě `NavLinkMatch` Možnosti, které lze přiřadit k `Match` atributu `<NavLink>` elementu:</span><span class="sxs-lookup"><span data-stu-id="4d39d-540">There are two `NavLinkMatch` options that you can assign to the `Match` attribute of the `<NavLink>` element:</span></span>

* <span data-ttu-id="4d39d-541">`NavLinkMatch.All`&ndash; `NavLink` Je aktivní, pokud odpovídá celé aktuální adrese URL.</span><span class="sxs-lookup"><span data-stu-id="4d39d-541">`NavLinkMatch.All` &ndash; The `NavLink` is active when it matches the entire current URL.</span></span>
* <span data-ttu-id="4d39d-542">`NavLinkMatch.Prefix`(*výchozí*) &ndash; `NavLink`Je aktivní, pokud odpovídá libovolné předponě aktuální adresy URL.</span><span class="sxs-lookup"><span data-stu-id="4d39d-542">`NavLinkMatch.Prefix` (*default*) &ndash; The `NavLink` is active when it matches any prefix of the current URL.</span></span>

<span data-ttu-id="4d39d-543">V předchozím příkladu se Domovská stránka `NavLink` `href=""` shoduje s adresou URL domů a přijímá pouze `active` třídu CSS ve výchozí základní cestě URL aplikace (například `https://localhost:5001/` ).</span><span class="sxs-lookup"><span data-stu-id="4d39d-543">In the preceding example, the Home `NavLink` `href=""` matches the home URL and only receives the `active` CSS class at the app's default base path URL (for example, `https://localhost:5001/`).</span></span> <span data-ttu-id="4d39d-544">Druhá `NavLink` Získá třídu, `active` když uživatel navštíví libovolnou adresu URL s `MyComponent` předponou (například `https://localhost:5001/MyComponent` a `https://localhost:5001/MyComponent/AnotherSegment` ).</span><span class="sxs-lookup"><span data-stu-id="4d39d-544">The second `NavLink` receives the `active` class when the user visits any URL with a `MyComponent` prefix (for example, `https://localhost:5001/MyComponent` and `https://localhost:5001/MyComponent/AnotherSegment`).</span></span>

<span data-ttu-id="4d39d-545">Další `NavLink` atributy komponenty jsou předány do vykreslené značky ukotvení.</span><span class="sxs-lookup"><span data-stu-id="4d39d-545">Additional `NavLink` component attributes are passed through to the rendered anchor tag.</span></span> <span data-ttu-id="4d39d-546">V následujícím příkladu `NavLink` Komponenta zahrnuje `target` atribut:</span><span class="sxs-lookup"><span data-stu-id="4d39d-546">In the following example, the `NavLink` component includes the `target` attribute:</span></span>

```razor
<NavLink href="my-page" target="_blank">My page</NavLink>
```

<span data-ttu-id="4d39d-547">Vykresluje se následující kód HTML:</span><span class="sxs-lookup"><span data-stu-id="4d39d-547">The following HTML markup is rendered:</span></span>

```html
<a href="my-page" target="_blank" rel="noopener noreferrer">My page</a>
```

## <a name="uri-and-navigation-state-helpers"></a><span data-ttu-id="4d39d-548">Identifikátory URI a pomocníka pro stav navigace</span><span class="sxs-lookup"><span data-stu-id="4d39d-548">URI and navigation state helpers</span></span>

<span data-ttu-id="4d39d-549">Použijte <xref:Microsoft.AspNetCore.Components.NavigationManager> pro práci s identifikátory URI a navigací v kódu jazyka C#.</span><span class="sxs-lookup"><span data-stu-id="4d39d-549">Use <xref:Microsoft.AspNetCore.Components.NavigationManager> to work with URIs and navigation in C# code.</span></span> <span data-ttu-id="4d39d-550">`NavigationManager`poskytuje událost a metody uvedené v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="4d39d-550">`NavigationManager` provides the event and methods shown in the following table.</span></span>

| <span data-ttu-id="4d39d-551">Člen</span><span class="sxs-lookup"><span data-stu-id="4d39d-551">Member</span></span> | <span data-ttu-id="4d39d-552">Popis</span><span class="sxs-lookup"><span data-stu-id="4d39d-552">Description</span></span> |
| ---
<span data-ttu-id="4d39d-553">title: ' ASP.NET Core Blazor Routing ' autora: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="4d39d-553">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4d39d-554">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4d39d-554">'Blazor'</span></span>
- <span data-ttu-id="4d39d-555">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4d39d-555">'Identity'</span></span>
- <span data-ttu-id="4d39d-556">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4d39d-556">'Let's Encrypt'</span></span>
- <span data-ttu-id="4d39d-557">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4d39d-557">'Razor'</span></span>
- <span data-ttu-id="4d39d-558">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="4d39d-558">'SignalR' uid:</span></span> 

<span data-ttu-id="4d39d-559">--- | ---Název: ' ASP.NET Core Blazor Směrování ' Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="4d39d-559">--- | --- title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4d39d-560">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4d39d-560">'Blazor'</span></span>
- <span data-ttu-id="4d39d-561">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4d39d-561">'Identity'</span></span>
- <span data-ttu-id="4d39d-562">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4d39d-562">'Let's Encrypt'</span></span>
- <span data-ttu-id="4d39d-563">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4d39d-563">'Razor'</span></span>
- <span data-ttu-id="4d39d-564">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="4d39d-564">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4d39d-565">title: ' ASP.NET Core Blazor Routing ' autora: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="4d39d-565">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4d39d-566">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4d39d-566">'Blazor'</span></span>
- <span data-ttu-id="4d39d-567">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4d39d-567">'Identity'</span></span>
- <span data-ttu-id="4d39d-568">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4d39d-568">'Let's Encrypt'</span></span>
- <span data-ttu-id="4d39d-569">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4d39d-569">'Razor'</span></span>
- <span data-ttu-id="4d39d-570">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="4d39d-570">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4d39d-571">title: ' ASP.NET Core Blazor Routing ' autora: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="4d39d-571">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4d39d-572">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4d39d-572">'Blazor'</span></span>
- <span data-ttu-id="4d39d-573">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4d39d-573">'Identity'</span></span>
- <span data-ttu-id="4d39d-574">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4d39d-574">'Let's Encrypt'</span></span>
- <span data-ttu-id="4d39d-575">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4d39d-575">'Razor'</span></span>
- <span data-ttu-id="4d39d-576">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="4d39d-576">'SignalR' uid:</span></span> 

<span data-ttu-id="4d39d-577">------ | | Identifikátor URI | Získá aktuální absolutní identifikátor URI.</span><span class="sxs-lookup"><span data-stu-id="4d39d-577">------ | | Uri | Gets the current absolute URI.</span></span> <span data-ttu-id="4d39d-578">| | BaseUri | Získá základní identifikátor URI (s koncovým lomítkem), který může být součástí relativních cest URI pro vytvoření absolutního identifikátoru URI.</span><span class="sxs-lookup"><span data-stu-id="4d39d-578">| | BaseUri | Gets the base URI (with a trailing slash) that can be prepended to relative URI paths to produce an absolute URI.</span></span> <span data-ttu-id="4d39d-579">Obvykle `BaseUri` odpovídá `href` atributům `<base>` prvku dokumentu v *wwwroot/index.html* ( Blazor WebAssembly) nebo *Pages/_Host. cshtml* ( Blazor Server).</span><span class="sxs-lookup"><span data-stu-id="4d39d-579">Typically, `BaseUri` corresponds to the `href` attribute on the document's `<base>` element in *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server).</span></span> <span data-ttu-id="4d39d-580">| | NavigateTo | Přejde k zadanému identifikátoru URI.</span><span class="sxs-lookup"><span data-stu-id="4d39d-580">| | NavigateTo | Navigates to the specified URI.</span></span> <span data-ttu-id="4d39d-581">Pokud `forceLoad` je `true` :</span><span class="sxs-lookup"><span data-stu-id="4d39d-581">If `forceLoad` is `true`:</span></span><ul><li><span data-ttu-id="4d39d-582">Směrování na straně klienta se nepoužívá.</span><span class="sxs-lookup"><span data-stu-id="4d39d-582">Client-side routing is bypassed.</span></span></li><li><span data-ttu-id="4d39d-583">Prohlížeč je nucen načíst novou stránku ze serveru, bez ohledu na to, zda je identifikátor URI obvykle zpracováván směrovačem na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="4d39d-583">The browser is forced to load the new page from the server, whether or not the URI is normally handled by the client-side router.</span></span></li></ul> <span data-ttu-id="4d39d-584">| | LocationChanged | Událost, která se aktivuje, když se změní navigační umístění</span><span class="sxs-lookup"><span data-stu-id="4d39d-584">| | LocationChanged | An event that fires when the navigation location has changed.</span></span> <span data-ttu-id="4d39d-585">| | ToAbsoluteUri | Převede relativní identifikátor URI na absolutní identifikátor URI.</span><span class="sxs-lookup"><span data-stu-id="4d39d-585">| | ToAbsoluteUri | Converts a relative URI into an absolute URI.</span></span> <span data-ttu-id="4d39d-586">| | <span style="word-break:normal;word-wrap:normal">ToBaseRelativePath</span> | Vzhledem k základnímu identifikátoru URI (například identifikátor URI, který dřív vrátil `GetBaseUri` ), převede absolutní identifikátor URI na URI relativně k základní předponě identifikátoru URI.</span><span class="sxs-lookup"><span data-stu-id="4d39d-586">| | <span style="word-break:normal;word-wrap:normal">ToBaseRelativePath</span> | Given a base URI (for example, a URI previously returned by `GetBaseUri`), converts an absolute URI into a URI relative to the base URI prefix.</span></span> |

<span data-ttu-id="4d39d-587">`Counter`Pokud je vybráno tlačítko, následující komponenta přejde na součást aplikace:</span><span class="sxs-lookup"><span data-stu-id="4d39d-587">The following component navigates to the app's `Counter` component when the button is selected:</span></span>

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

<span data-ttu-id="4d39d-588">Následující komponenta zpracovává událost změny umístění.</span><span class="sxs-lookup"><span data-stu-id="4d39d-588">The following component handles a location changed event.</span></span> <span data-ttu-id="4d39d-589">`HandleLocationChanged`Metoda je nepřipojená, pokud `Dispose` je volána rozhraním.</span><span class="sxs-lookup"><span data-stu-id="4d39d-589">The `HandleLocationChanged` method is unhooked when `Dispose` is called by the framework.</span></span> <span data-ttu-id="4d39d-590">Odpojování metody umožňuje uvolňování paměti komponenty.</span><span class="sxs-lookup"><span data-stu-id="4d39d-590">Unhooking the method permits garbage collection of the component.</span></span>

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

<span data-ttu-id="4d39d-591"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs>poskytuje následující informace o události:</span><span class="sxs-lookup"><span data-stu-id="4d39d-591"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs> provides the following information about the event:</span></span>

* <span data-ttu-id="4d39d-592"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>&ndash;Adresa URL nového umístění</span><span class="sxs-lookup"><span data-stu-id="4d39d-592"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location> &ndash; The URL of the new location.</span></span>
* <span data-ttu-id="4d39d-593"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>&ndash;Pokud `true` Blazor byla zachycena navigace z prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="4d39d-593"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted> &ndash; If `true`, Blazor intercepted the navigation from the browser.</span></span> <span data-ttu-id="4d39d-594">Pokud `false` [NavigationManager. NavigateTo](xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A) způsobila, že dojde k navigaci.</span><span class="sxs-lookup"><span data-stu-id="4d39d-594">If `false`, [NavigationManager.NavigateTo](xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A) caused the navigation to occur.</span></span>

<span data-ttu-id="4d39d-595">Další informace o vyřazení součástí najdete v tématu <xref:blazor/lifecycle#component-disposal-with-idisposable> .</span><span class="sxs-lookup"><span data-stu-id="4d39d-595">For more information on component disposal, see <xref:blazor/lifecycle#component-disposal-with-idisposable>.</span></span>
