---
<span data-ttu-id="e7e54-101">Název: Autor: Popis: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="e7e54-101">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="e7e54-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e7e54-102">'Blazor'</span></span>
- <span data-ttu-id="e7e54-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e7e54-103">'Identity'</span></span>
- <span data-ttu-id="e7e54-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e7e54-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="e7e54-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e7e54-105">'Razor'</span></span>
- <span data-ttu-id="e7e54-106">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="e7e54-106">'SignalR' uid:</span></span> 

---
# <a name="routing-to-controller-actions-in-aspnet-core"></a><span data-ttu-id="e7e54-107">Směrování na akce kontroleru v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e7e54-107">Routing to controller actions in ASP.NET Core</span></span>

<span data-ttu-id="e7e54-108">Služba [Ryan Nowak](https://github.com/rynowak), [Kirka Larkin](https://twitter.com/serpent5)a [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="e7e54-108">By [Ryan Nowak](https://github.com/rynowak), [Kirk Larkin](https://twitter.com/serpent5), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="e7e54-109">Řadiče ASP.NET Core používají [middleware](xref:fundamentals/middleware/index) směrování ke spárování adres URL příchozích požadavků a jejich mapování na [Akce](#action).</span><span class="sxs-lookup"><span data-stu-id="e7e54-109">ASP.NET Core controllers use the Routing [middleware](xref:fundamentals/middleware/index) to match the URLs of incoming requests and map them to [actions](#action).</span></span>  <span data-ttu-id="e7e54-110">Šablony tras:</span><span class="sxs-lookup"><span data-stu-id="e7e54-110">Routes templates:</span></span>

* <span data-ttu-id="e7e54-111">Jsou definovány ve spouštěcím kódu nebo atributech.</span><span class="sxs-lookup"><span data-stu-id="e7e54-111">Are defined in startup code or attributes.</span></span>
* <span data-ttu-id="e7e54-112">Popisuje, jak se shodují cesty URL k [akcím](#action).</span><span class="sxs-lookup"><span data-stu-id="e7e54-112">Describe how URL paths are matched to [actions](#action).</span></span>
* <span data-ttu-id="e7e54-113">Slouží ke generování adres URL pro odkazy.</span><span class="sxs-lookup"><span data-stu-id="e7e54-113">Are used to generate URLs for links.</span></span> <span data-ttu-id="e7e54-114">Vygenerované odkazy jsou obvykle vraceny v odpovědích.</span><span class="sxs-lookup"><span data-stu-id="e7e54-114">The generated links are typically returned in responses.</span></span>

<span data-ttu-id="e7e54-115">Akce jsou směrovány buď podle [konvence](#cr) , nebo podle [atributů](#ar).</span><span class="sxs-lookup"><span data-stu-id="e7e54-115">Actions are either [conventionally-routed](#cr) or [attribute-routed](#ar).</span></span> <span data-ttu-id="e7e54-116">Umístěním trasy do kontroleru nebo [Akce](#action) se nastaví směrování atributů.</span><span class="sxs-lookup"><span data-stu-id="e7e54-116">Placing a route on the controller or [action](#action) makes it attribute-routed.</span></span> <span data-ttu-id="e7e54-117">Další informace najdete v tématu [smíšená směrování](#routing-mixed-ref-label) .</span><span class="sxs-lookup"><span data-stu-id="e7e54-117">See [Mixed routing](#routing-mixed-ref-label) for more information.</span></span>

<span data-ttu-id="e7e54-118">Tento dokument:</span><span class="sxs-lookup"><span data-stu-id="e7e54-118">This document:</span></span>

* <span data-ttu-id="e7e54-119">Vysvětluje interakce mezi MVC a směrováním:</span><span class="sxs-lookup"><span data-stu-id="e7e54-119">Explains the interactions between MVC and routing:</span></span>
  * <span data-ttu-id="e7e54-120">Jak typické aplikace MVC využívají funkce směrování.</span><span class="sxs-lookup"><span data-stu-id="e7e54-120">How typical MVC apps make use of routing features.</span></span>
  * <span data-ttu-id="e7e54-121">Pokrývá obojí:</span><span class="sxs-lookup"><span data-stu-id="e7e54-121">Covers both:</span></span>
    * <span data-ttu-id="e7e54-122">[Směrování](#cr) se obvykle používá s řadiči a zobrazeními.</span><span class="sxs-lookup"><span data-stu-id="e7e54-122">[Conventionally routing](#cr) typically used with controllers and views.</span></span>
    * <span data-ttu-id="e7e54-123">*Směrování atributů* používané s rozhraními REST API.</span><span class="sxs-lookup"><span data-stu-id="e7e54-123">*Attribute routing* used with REST APIs.</span></span> <span data-ttu-id="e7e54-124">Pokud se primárně zajímáte o směrování pro rozhraní REST API, přejděte na oddíl [Směrování atributů pro rozhraní REST API](#ar) .</span><span class="sxs-lookup"><span data-stu-id="e7e54-124">If you're primarily interested in routing for REST APIs, jump to the [Attribute routing for REST APIs](#ar) section.</span></span>
  * <span data-ttu-id="e7e54-125">Podrobnosti o rozšířeném směrování najdete v tématu [Směrování](xref:fundamentals/routing) .</span><span class="sxs-lookup"><span data-stu-id="e7e54-125">See [Routing](xref:fundamentals/routing) for advanced routing details.</span></span>
* <span data-ttu-id="e7e54-126">Odkazuje na výchozí systém směrování přidaný v ASP.NET Core 3,0, který se označuje jako směrování koncového bodu.</span><span class="sxs-lookup"><span data-stu-id="e7e54-126">Refers to the default routing system added in ASP.NET Core 3.0, called endpoint routing.</span></span> <span data-ttu-id="e7e54-127">Pro účely kompatibility je možné použít řadiče s předchozí verzí směrování.</span><span class="sxs-lookup"><span data-stu-id="e7e54-127">It's possible to use controllers with the previous version of routing for compatibility purposes.</span></span> <span data-ttu-id="e7e54-128">Pokyny najdete v [příručce k migraci 2.2 – 3.0](xref:migration/22-to-30) .</span><span class="sxs-lookup"><span data-stu-id="e7e54-128">See the [2.2-3.0 migration guide](xref:migration/22-to-30) for instructions.</span></span> <span data-ttu-id="e7e54-129">Referenční materiály ke staršímu systému směrování najdete v [tomto dokumentu ve verzi 2,2](xref:mvc/controllers/routing?view=aspnetcore-2.2) .</span><span class="sxs-lookup"><span data-stu-id="e7e54-129">Refer to the [2.2 version of this document](xref:mvc/controllers/routing?view=aspnetcore-2.2) for reference material on the legacy routing system.</span></span>

<a name="cr"></a>

## <a name="set-up-conventional-route"></a><span data-ttu-id="e7e54-130">Nastavení konvenční trasy</span><span class="sxs-lookup"><span data-stu-id="e7e54-130">Set up conventional route</span></span>

<span data-ttu-id="e7e54-131">`Startup.Configure`obvykle má při použití [konvenčního směrování](#crd)kód podobný následujícímu:</span><span class="sxs-lookup"><span data-stu-id="e7e54-131">`Startup.Configure` typically has code similar to the following when using [conventional routing](#crd):</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupDefaultMVC.cs?name=snippet)]

<span data-ttu-id="e7e54-132">Uvnitř volání metody <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> se používá k vytvoření jedné trasy.</span><span class="sxs-lookup"><span data-stu-id="e7e54-132">Inside the call to <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*>, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> is used to create a single route.</span></span> <span data-ttu-id="e7e54-133">Jedna trasa má název `default` Route.</span><span class="sxs-lookup"><span data-stu-id="e7e54-133">The single route is named `default` route.</span></span> <span data-ttu-id="e7e54-134">Většina aplikací s řadiči a zobrazeními používá šablonu směrování podobnou `default` trase.</span><span class="sxs-lookup"><span data-stu-id="e7e54-134">Most apps with controllers and views use a route template similar to the `default` route.</span></span> <span data-ttu-id="e7e54-135">Rozhraní REST API by měly používat [Směrování atributů](#ar).</span><span class="sxs-lookup"><span data-stu-id="e7e54-135">REST APIs should use [attribute routing](#ar).</span></span>

<span data-ttu-id="e7e54-136">Šablona trasy `"{controller=Home}/{action=Index}/{id?}"` :</span><span class="sxs-lookup"><span data-stu-id="e7e54-136">The route template `"{controller=Home}/{action=Index}/{id?}"`:</span></span>

* <span data-ttu-id="e7e54-137">Odpovídá cestě URL jako`/Products/Details/5`</span><span class="sxs-lookup"><span data-stu-id="e7e54-137">Matches a URL path like `/Products/Details/5`</span></span>
* <span data-ttu-id="e7e54-138">Extrahuje hodnoty tras `{ controller = Products, action = Details, id = 5 }` tím, že tokenizací cestu.</span><span class="sxs-lookup"><span data-stu-id="e7e54-138">Extracts the route values `{ controller = Products, action = Details, id = 5 }` by tokenizing the path.</span></span> <span data-ttu-id="e7e54-139">Výsledkem extrakce hodnot tras je shoda, pokud má aplikace kontroler s názvem `ProductsController` a `Details` akci:</span><span class="sxs-lookup"><span data-stu-id="e7e54-139">The extraction of route values results in a match if the app has a controller named `ProductsController` and a `Details` action:</span></span>

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippetA)]

  [!INCLUDE[](~/includes/MyDisplayRouteInfo.md)]

* <span data-ttu-id="e7e54-140">`/Products/Details/5`model váže hodnotu `id = 5` pro nastavení `id` parametru `5` .</span><span class="sxs-lookup"><span data-stu-id="e7e54-140">`/Products/Details/5` model binds the value of `id = 5` to set the `id` parameter to `5`.</span></span> <span data-ttu-id="e7e54-141">Další podrobnosti najdete v tématu [vazba modelu](xref:mvc/models/model-binding) .</span><span class="sxs-lookup"><span data-stu-id="e7e54-141">See [Model Binding](xref:mvc/models/model-binding) for more details.</span></span>
* <span data-ttu-id="e7e54-142">`{controller=Home}`definuje `Home` jako výchozí `controller` .</span><span class="sxs-lookup"><span data-stu-id="e7e54-142">`{controller=Home}` defines `Home` as the default `controller`.</span></span>
* <span data-ttu-id="e7e54-143">`{action=Index}`definuje `Index` jako výchozí `action` .</span><span class="sxs-lookup"><span data-stu-id="e7e54-143">`{action=Index}` defines `Index` as the default `action`.</span></span>
*  <span data-ttu-id="e7e54-144">`?`Znak v `{id?}` definuje `id` jako volitelné.</span><span class="sxs-lookup"><span data-stu-id="e7e54-144">The `?` character in `{id?}` defines `id` as optional.</span></span>
  * <span data-ttu-id="e7e54-145">Výchozí a volitelné parametry směrování nemusejí být v cestě URL pro porovnávání k dispozici.</span><span class="sxs-lookup"><span data-stu-id="e7e54-145">Default and optional route parameters don't need to be present in the URL path for a match.</span></span> <span data-ttu-id="e7e54-146">Podrobný popis syntaxe šablony směrování naleznete v tématu Referenční dokumentace k [šabloně směrování](xref:fundamentals/routing#route-template-reference) .</span><span class="sxs-lookup"><span data-stu-id="e7e54-146">See [Route Template Reference](xref:fundamentals/routing#route-template-reference) for a detailed description of route template syntax.</span></span>
* <span data-ttu-id="e7e54-147">Odpovídá cestě URL `/` .</span><span class="sxs-lookup"><span data-stu-id="e7e54-147">Matches the URL path `/`.</span></span>
* <span data-ttu-id="e7e54-148">Vytvoří hodnoty trasy `{ controller = Home, action = Index }` .</span><span class="sxs-lookup"><span data-stu-id="e7e54-148">Produces the route values `{ controller = Home, action = Index }`.</span></span>

<span data-ttu-id="e7e54-149">Hodnoty pro `controller` a `action` využívají výchozí hodnoty.</span><span class="sxs-lookup"><span data-stu-id="e7e54-149">The values for `controller` and `action` make use of the default values.</span></span> <span data-ttu-id="e7e54-150">`id`nevytváří hodnotu, protože v cestě URL není žádný odpovídající segment.</span><span class="sxs-lookup"><span data-stu-id="e7e54-150">`id` doesn't produce a value since there's no corresponding segment in the URL path.</span></span> <span data-ttu-id="e7e54-151">`/`odpovídá pouze v případě, že `HomeController` existuje `Index` akce a:</span><span class="sxs-lookup"><span data-stu-id="e7e54-151">`/` only matches if there exists a `HomeController` and `Index` action:</span></span>

```csharp
public class HomeController : Controller
{
  public IActionResult Index() { ... }
}
```

<span data-ttu-id="e7e54-152">Pomocí předchozí definice kontroleru a šablony trasy se `HomeController.Index` akce spustí pro následující cesty URL:</span><span class="sxs-lookup"><span data-stu-id="e7e54-152">Using the preceding controller definition and route template, the `HomeController.Index` action is run for the following URL paths:</span></span>

* `/Home/Index/17`
* `/Home/Index`
* `/Home`
* `/`

<span data-ttu-id="e7e54-153">Cesta URL `/` používá výchozí `Home` řadiče a akci šablony směrování `Index` .</span><span class="sxs-lookup"><span data-stu-id="e7e54-153">The URL path `/` uses the route template default `Home` controllers and `Index` action.</span></span> <span data-ttu-id="e7e54-154">Cesta URL `/Home` používá výchozí akci šablony směrování `Index` .</span><span class="sxs-lookup"><span data-stu-id="e7e54-154">The URL path `/Home` uses the route template default `Index` action.</span></span>

<span data-ttu-id="e7e54-155">Způsob usnadnění <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute*> :</span><span class="sxs-lookup"><span data-stu-id="e7e54-155">The convenience method <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute*>:</span></span>

```csharp
endpoints.MapDefaultControllerRoute();
```

<span data-ttu-id="e7e54-156">LPRMON</span><span class="sxs-lookup"><span data-stu-id="e7e54-156">Replaces:</span></span>

```csharp
endpoints.MapControllerRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

> [!IMPORTANT]
> <span data-ttu-id="e7e54-157">Směrování je nakonfigurované pomocí <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting*> <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> middlewaru a.</span><span class="sxs-lookup"><span data-stu-id="e7e54-157">Routing is configured using the <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting*> and <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> middleware.</span></span> <span data-ttu-id="e7e54-158">Použití řadičů:</span><span class="sxs-lookup"><span data-stu-id="e7e54-158">To use controllers:</span></span>
>
> * <span data-ttu-id="e7e54-159">Zavolejte <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*> uvnitř `UseEndpoints` pro mapování řadičů [směrovaných na atributy](#ar) .</span><span class="sxs-lookup"><span data-stu-id="e7e54-159">Call <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*> inside `UseEndpoints` to map [attribute routed](#ar) controllers.</span></span>
> * <span data-ttu-id="e7e54-160">Zavolejte <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> nebo <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> , pokud chcete namapovat obě [konvence směrované](#cr) na řadiče a ovladače [směrovaného atributu](#ar) .</span><span class="sxs-lookup"><span data-stu-id="e7e54-160">Call <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> or <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*>, to map both [conventionally routed](#cr) controllers and [attribute routed](#ar) controllers.</span></span>

<a name="routing-conventional-ref-label"></a>
<a name="crd"></a>

## <a name="conventional-routing"></a><span data-ttu-id="e7e54-161">Konvenční směrování</span><span class="sxs-lookup"><span data-stu-id="e7e54-161">Conventional routing</span></span>

<span data-ttu-id="e7e54-162">Konvenční směrování se používá s řadiči a zobrazeními.</span><span class="sxs-lookup"><span data-stu-id="e7e54-162">Conventional routing is used with controllers and views.</span></span> <span data-ttu-id="e7e54-163">`default`Trasa:</span><span class="sxs-lookup"><span data-stu-id="e7e54-163">The `default` route:</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupDefaultMVC.cs?name=snippet2)]

<span data-ttu-id="e7e54-164">je příkladem *konvenčního směrování*.</span><span class="sxs-lookup"><span data-stu-id="e7e54-164">is an example of a *conventional routing*.</span></span> <span data-ttu-id="e7e54-165">Nazývá se *konvenční směrování* , protože vytváří *konvenci* pro cesty URL:</span><span class="sxs-lookup"><span data-stu-id="e7e54-165">It's called *conventional routing* because it establishes a *convention* for URL paths:</span></span>

* <span data-ttu-id="e7e54-166">První segment cesty, `{controller=Home}` , mapuje na název kontroleru.</span><span class="sxs-lookup"><span data-stu-id="e7e54-166">The first path segment, `{controller=Home}`, maps to the controller name.</span></span>
* <span data-ttu-id="e7e54-167">Druhý segment, `{action=Index}` , mapuje na název [Akce](#action) .</span><span class="sxs-lookup"><span data-stu-id="e7e54-167">The second segment, `{action=Index}`, maps to the [action](#action) name.</span></span>
* <span data-ttu-id="e7e54-168">Třetí segment `{id?}` je použit pro volitelné `id` .</span><span class="sxs-lookup"><span data-stu-id="e7e54-168">The third segment, `{id?}` is used for an optional `id`.</span></span> <span data-ttu-id="e7e54-169">V nástroji je `?` `{id?}` volitelné.</span><span class="sxs-lookup"><span data-stu-id="e7e54-169">The `?` in `{id?}` makes it optional.</span></span> <span data-ttu-id="e7e54-170">`id`slouží k mapování na entitu modelu.</span><span class="sxs-lookup"><span data-stu-id="e7e54-170">`id` is used to map to a model entity.</span></span>

<span data-ttu-id="e7e54-171">Pomocí této `default` trasy adresa URL:</span><span class="sxs-lookup"><span data-stu-id="e7e54-171">Using this `default` route, the URL path:</span></span>

* <span data-ttu-id="e7e54-172">`/Products/List`provede mapování na `ProductsController.List` akci.</span><span class="sxs-lookup"><span data-stu-id="e7e54-172">`/Products/List` maps to the `ProductsController.List` action.</span></span>
* <span data-ttu-id="e7e54-173">`/Blog/Article/17`mapuje na `BlogController.Article` model a obvykle model váže `id` parametr na 17.</span><span class="sxs-lookup"><span data-stu-id="e7e54-173">`/Blog/Article/17` maps to `BlogController.Article` and typically model binds the `id` parameter to 17.</span></span>

<span data-ttu-id="e7e54-174">Toto mapování:</span><span class="sxs-lookup"><span data-stu-id="e7e54-174">This mapping:</span></span>

* <span data-ttu-id="e7e54-175">Je založena **pouze**na názvech kontroléru a [Akce](#action) .</span><span class="sxs-lookup"><span data-stu-id="e7e54-175">Is based on the controller and [action](#action) names **only**.</span></span>
* <span data-ttu-id="e7e54-176">Není založen na oborech názvů, umístěních zdrojového souboru nebo parametrech metody.</span><span class="sxs-lookup"><span data-stu-id="e7e54-176">Isn't based on namespaces, source file locations, or method parameters.</span></span>

<span data-ttu-id="e7e54-177">Použití konvenčního směrování s výchozí trasou umožňuje vytvoření aplikace bez nutnosti sestavovat nový vzor adresy URL pro každou akci.</span><span class="sxs-lookup"><span data-stu-id="e7e54-177">Using conventional routing with the default route allows creating the app without having to come up with a new URL pattern for each action.</span></span> <span data-ttu-id="e7e54-178">Pro aplikaci s akcemi stylu [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) s konzistencí pro adresy URL napříč řadiči:</span><span class="sxs-lookup"><span data-stu-id="e7e54-178">For an app with [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) style actions, having consistency for the URLs across controllers:</span></span>

* <span data-ttu-id="e7e54-179">Pomáhá zjednodušit kód.</span><span class="sxs-lookup"><span data-stu-id="e7e54-179">Helps simplify the code.</span></span>
* <span data-ttu-id="e7e54-180">Provede více předvídatelného uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="e7e54-180">Makes the UI more predictable.</span></span>

> [!WARNING]
> <span data-ttu-id="e7e54-181">`id`V předchozím kódu je šablona trasy definována jako volitelná.</span><span class="sxs-lookup"><span data-stu-id="e7e54-181">The `id` in the preceding code is defined as optional by the route template.</span></span> <span data-ttu-id="e7e54-182">Akce se můžou provádět bez volitelného ID, které jste zadali jako součást adresy URL.</span><span class="sxs-lookup"><span data-stu-id="e7e54-182">Actions can execute without the optional ID provided as part of the URL.</span></span> <span data-ttu-id="e7e54-183">Obecně platí, že pokud `id` je vynechána adresa URL:</span><span class="sxs-lookup"><span data-stu-id="e7e54-183">Generally, when`id` is omitted from the URL:</span></span>
>
> * <span data-ttu-id="e7e54-184">`id`je nastaven na `0` základě vazby modelu.</span><span class="sxs-lookup"><span data-stu-id="e7e54-184">`id` is set to `0` by model binding.</span></span>
> * <span data-ttu-id="e7e54-185">V porovnání s databází nebyla nalezena žádná entita `id == 0` .</span><span class="sxs-lookup"><span data-stu-id="e7e54-185">No entity is found in the database matching `id == 0`.</span></span>
>
> <span data-ttu-id="e7e54-186">[Směrování atributů](#ar) poskytuje jemně odstupňovaný ovládací prvek, který umožňuje, aby se ID vyžadovalo u některých akcí, a ne pro ostatní.</span><span class="sxs-lookup"><span data-stu-id="e7e54-186">[Attribute routing](#ar) provides fine-grained control to make the ID required for some actions and not for others.</span></span> <span data-ttu-id="e7e54-187">V dokumentaci podle konvence obsahuje volitelné parametry, jako `id` když se pravděpodobně budou zobrazovat ve správném použití.</span><span class="sxs-lookup"><span data-stu-id="e7e54-187">By convention, the documentation includes optional parameters like `id` when they're likely to appear in correct usage.</span></span>

<span data-ttu-id="e7e54-188">Většina aplikací by měla zvolit základní a popisné schéma směrování, aby byly adresy URL čitelné a smysluplné.</span><span class="sxs-lookup"><span data-stu-id="e7e54-188">Most apps should choose a basic and descriptive routing scheme so that URLs are readable and meaningful.</span></span> <span data-ttu-id="e7e54-189">Výchozí konvenční trasa `{controller=Home}/{action=Index}/{id?}` :</span><span class="sxs-lookup"><span data-stu-id="e7e54-189">The default conventional route `{controller=Home}/{action=Index}/{id?}`:</span></span>

* <span data-ttu-id="e7e54-190">Podporuje základní a popisné schéma směrování.</span><span class="sxs-lookup"><span data-stu-id="e7e54-190">Supports a basic and descriptive routing scheme.</span></span>
* <span data-ttu-id="e7e54-191">Je užitečným výchozím bodem pro aplikace založené na uživatelském rozhraní.</span><span class="sxs-lookup"><span data-stu-id="e7e54-191">Is a useful starting point for UI-based apps.</span></span>
* <span data-ttu-id="e7e54-192">Je jedinou šablonou směrování, která je nutná pro mnoho webových aplikací uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="e7e54-192">Is the only route template needed for many web UI apps.</span></span> <span data-ttu-id="e7e54-193">U větších webových aplikací uživatelského rozhraní je další trasa s použitím [oblastí](#areas) , pokud je to potřeba.</span><span class="sxs-lookup"><span data-stu-id="e7e54-193">For larger web UI apps, another route using [Areas](#areas) if frequently all that's needed.</span></span>

<span data-ttu-id="e7e54-194"><xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*>a <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> :</span><span class="sxs-lookup"><span data-stu-id="e7e54-194"><xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> and <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> :</span></span>

* <span data-ttu-id="e7e54-195">Automatické přiřazení hodnoty **objednávky** ke svým koncovým bodům podle pořadí, ve kterém jsou vyvolány.</span><span class="sxs-lookup"><span data-stu-id="e7e54-195">Automatically assign an **order** value to their endpoints based on the order they are invoked.</span></span>

<span data-ttu-id="e7e54-196">Směrování koncových bodů v ASP.NET Core 3,0 a novějším:</span><span class="sxs-lookup"><span data-stu-id="e7e54-196">Endpoint routing in ASP.NET Core 3.0 and later:</span></span>

* <span data-ttu-id="e7e54-197">Nemá koncept tras.</span><span class="sxs-lookup"><span data-stu-id="e7e54-197">Doesn't have a concept of routes.</span></span>
* <span data-ttu-id="e7e54-198">Neposkytuje zaručené řazení pro provádění rozšiřitelnosti, všechny koncové body jsou zpracovávány současně.</span><span class="sxs-lookup"><span data-stu-id="e7e54-198">Doesn't provide ordering guarantees for the execution of extensibility,  all endpoints are processed at once.</span></span>

<span data-ttu-id="e7e54-199">Povolte [protokolování](xref:fundamentals/logging/index) , abyste viděli, jak integrované implementace směrování, například <xref:Microsoft.AspNetCore.Routing.Route> , odpovídají požadavkům.</span><span class="sxs-lookup"><span data-stu-id="e7e54-199">Enable [Logging](xref:fundamentals/logging/index) to see how the built-in routing implementations, such as <xref:Microsoft.AspNetCore.Routing.Route>, match requests.</span></span>

<span data-ttu-id="e7e54-200">[Směrování atributů](#ar) je vysvětleno dále v tomto dokumentu.</span><span class="sxs-lookup"><span data-stu-id="e7e54-200">[Attribute routing](#ar) is explained later in this document.</span></span>

<a name="mr"></a>

### <a name="multiple-conventional-routes"></a><span data-ttu-id="e7e54-201">Několik konvenčních tras</span><span class="sxs-lookup"><span data-stu-id="e7e54-201">Multiple conventional routes</span></span>

<span data-ttu-id="e7e54-202">V rámci lze přidat více [konvenčních tras](#cr) `UseEndpoints` přidáním dalších volání do <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> a <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> .</span><span class="sxs-lookup"><span data-stu-id="e7e54-202">Multiple [conventional routes](#cr) can be added inside `UseEndpoints` by adding more calls to <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> and <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*>.</span></span> <span data-ttu-id="e7e54-203">To umožňuje definovat více konvencí nebo přidávat konvenční trasy, které jsou vyhrazeny určité [akci](#action), například:</span><span class="sxs-lookup"><span data-stu-id="e7e54-203">Doing so allows defining multiple conventions, or to adding conventional routes that are dedicated to a specific [action](#action), such as:</span></span>

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

<a name="dcr"></a>

<span data-ttu-id="e7e54-204">`blog`Trasa v předchozím kódu je **vyhrazená konvenční trasa**.</span><span class="sxs-lookup"><span data-stu-id="e7e54-204">The `blog` route in the preceding code is a **dedicated conventional route**.</span></span> <span data-ttu-id="e7e54-205">Nazývá se vyhrazená konvenční trasa z těchto důvodů:</span><span class="sxs-lookup"><span data-stu-id="e7e54-205">It's called a dedicated conventional route because:</span></span>

* <span data-ttu-id="e7e54-206">Používá [konvenční směrování](#cr).</span><span class="sxs-lookup"><span data-stu-id="e7e54-206">It uses [conventional routing](#cr).</span></span>
* <span data-ttu-id="e7e54-207">Je vyhrazený pro konkrétní [akci](#action).</span><span class="sxs-lookup"><span data-stu-id="e7e54-207">It's dedicated to a specific [action](#action).</span></span>

<span data-ttu-id="e7e54-208">Protože `controller` se `action` nezobrazuje v šabloně trasy `"blog/{*article}"` jako parametry:</span><span class="sxs-lookup"><span data-stu-id="e7e54-208">Because `controller` and `action` don't appear in the route template `"blog/{*article}"` as parameters:</span></span>

* <span data-ttu-id="e7e54-209">Můžou mít jenom výchozí hodnoty `{ controller = "Blog", action = "Article" }` .</span><span class="sxs-lookup"><span data-stu-id="e7e54-209">They can only have the default values `{ controller = "Blog", action = "Article" }`.</span></span>
* <span data-ttu-id="e7e54-210">Tato trasa se vždy mapuje na akci `BlogController.Article` .</span><span class="sxs-lookup"><span data-stu-id="e7e54-210">This route always maps to the action `BlogController.Article`.</span></span>

<span data-ttu-id="e7e54-211">`/Blog`, `/Blog/Article` a `/Blog/{any-string}` jsou jediné cesty URL, které odpovídají trase na blogu.</span><span class="sxs-lookup"><span data-stu-id="e7e54-211">`/Blog`, `/Blog/Article`, and `/Blog/{any-string}` are the only URL paths that match the blog route.</span></span>

<span data-ttu-id="e7e54-212">Předchozí příklad:</span><span class="sxs-lookup"><span data-stu-id="e7e54-212">The preceding example:</span></span>

* <span data-ttu-id="e7e54-213">`blog`trasa má vyšší prioritu pro shody, než je `default` trasa, protože je přidána jako první.</span><span class="sxs-lookup"><span data-stu-id="e7e54-213">`blog` route has a higher priority for matches than the `default` route because it is added first.</span></span>
* <span data-ttu-id="e7e54-214">Je a příkladem směrování stylu [popisu](https://developer.mozilla.org/docs/Glossary/Slug) , kde je typický název článku jako součást adresy URL.</span><span class="sxs-lookup"><span data-stu-id="e7e54-214">Is and example of [Slug](https://developer.mozilla.org/docs/Glossary/Slug) style routing where it's typical to have an article name as part of the URL.</span></span>

> [!WARNING]
> <span data-ttu-id="e7e54-215">V ASP.NET Core 3,0 a novějších směrování:</span><span class="sxs-lookup"><span data-stu-id="e7e54-215">In ASP.NET Core 3.0 and later, routing doesn't:</span></span>
> * <span data-ttu-id="e7e54-216">Definujte koncept nazvaný *trase*.</span><span class="sxs-lookup"><span data-stu-id="e7e54-216">Define a concept called a *route*.</span></span> <span data-ttu-id="e7e54-217">`UseRouting`Přidá směrování do kanálu middlewaru.</span><span class="sxs-lookup"><span data-stu-id="e7e54-217">`UseRouting` adds route matching to the middleware pipeline.</span></span> <span data-ttu-id="e7e54-218">`UseRouting`Middleware prohlíží sadu koncových bodů definovaných v aplikaci a vybere nejlepší shodu koncového bodu na základě požadavku.</span><span class="sxs-lookup"><span data-stu-id="e7e54-218">The `UseRouting` middleware looks at the set of endpoints defined in the app, and selects the best endpoint match based on the request.</span></span>
> * <span data-ttu-id="e7e54-219">Poskytněte záruky týkající se pořadí spouštění rozšiřitelnosti, například <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> nebo <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint> .</span><span class="sxs-lookup"><span data-stu-id="e7e54-219">Provide guarantees about the execution order of extensibility like <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> or <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint>.</span></span>
>
><span data-ttu-id="e7e54-220">Přečtěte si téma [Směrování](xref:fundamentals/routing) pro referenční materiál při směrování.</span><span class="sxs-lookup"><span data-stu-id="e7e54-220">See [Routing](xref:fundamentals/routing) for reference material on routing.</span></span>

<a name="cro"></a>

### <a name="conventional-routing-order"></a><span data-ttu-id="e7e54-221">Konvenční pořadí směrování</span><span class="sxs-lookup"><span data-stu-id="e7e54-221">Conventional routing order</span></span>

<span data-ttu-id="e7e54-222">Konvenční směrování odpovídá pouze kombinaci akcí a kontrolérů, které jsou definovány aplikací.</span><span class="sxs-lookup"><span data-stu-id="e7e54-222">Conventional routing only matches a combination of action and controller that are defined by the app.</span></span> <span data-ttu-id="e7e54-223">Cílem je zjednodušit případy, kdy se konvenční trasy překrývají.</span><span class="sxs-lookup"><span data-stu-id="e7e54-223">This is intended to simplify cases where conventional routes overlap.</span></span>
<span data-ttu-id="e7e54-224">Přidání tras pomocí <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> , <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute*> a <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> Automatické přiřazení hodnoty objednávky ke svým koncovým bodům na základě pořadí, ve kterém jsou vyvolány.</span><span class="sxs-lookup"><span data-stu-id="e7e54-224">Adding routes using <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*>, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute*>, and <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> automatically assign an order value to their endpoints based on the order they are invoked.</span></span> <span data-ttu-id="e7e54-225">Shody z trasy, která se zobrazí dříve, mají vyšší prioritu.</span><span class="sxs-lookup"><span data-stu-id="e7e54-225">Matches from a route that appears earlier have a higher priority.</span></span> <span data-ttu-id="e7e54-226">Konvenční směrování je závislé na pořadí.</span><span class="sxs-lookup"><span data-stu-id="e7e54-226">Conventional routing is order-dependent.</span></span> <span data-ttu-id="e7e54-227">Obecně platí, že trasy s oblastmi by měly být umístěny dříve, protože jsou konkrétnější než trasy bez oblasti.</span><span class="sxs-lookup"><span data-stu-id="e7e54-227">In general, routes with areas should be placed earlier as they're more specific than routes without an area.</span></span> <span data-ttu-id="e7e54-228">[Vyhrazené konvenční trasy](#dcr) se zachycením všech parametrů tras, jako je například `{*article}` , mohou vytvořit trasu příliš [hladce](xref:fundamentals/routing#greedy), což znamená, že odpovídají adresám URL, které mají být porovnány s jinými trasami.</span><span class="sxs-lookup"><span data-stu-id="e7e54-228">[Dedicated conventional routes](#dcr) with catch-all route parameters like `{*article}` can make a route too [greedy](xref:fundamentals/routing#greedy), meaning that it matches URLs that you intended to be matched by other routes.</span></span> <span data-ttu-id="e7e54-229">Tyto hladové trasy umístěte později do směrovací tabulky, aby se zabránilo hladce porovnávání.</span><span class="sxs-lookup"><span data-stu-id="e7e54-229">Put the greedy routes later in the route table to prevent greedy matches.</span></span>

[!INCLUDE[](~/includes/catchall.md)]

<a name="best"></a>

### <a name="resolving-ambiguous-actions"></a><span data-ttu-id="e7e54-230">Řešení nejednoznačných akcí</span><span class="sxs-lookup"><span data-stu-id="e7e54-230">Resolving ambiguous actions</span></span>

<span data-ttu-id="e7e54-231">Pokud se dva koncové body shodují přes směrování, musí směrování provést jednu z následujících akcí:</span><span class="sxs-lookup"><span data-stu-id="e7e54-231">When two endpoints match through routing, routing must do one of the following:</span></span>

* <span data-ttu-id="e7e54-232">Vyberte nejlepší kandidáta.</span><span class="sxs-lookup"><span data-stu-id="e7e54-232">Choose the best candidate.</span></span>
* <span data-ttu-id="e7e54-233">Vyvolejte výjimku.</span><span class="sxs-lookup"><span data-stu-id="e7e54-233">Throw an exception.</span></span>

<span data-ttu-id="e7e54-234">Příklad:</span><span class="sxs-lookup"><span data-stu-id="e7e54-234">For example:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet9)]

<span data-ttu-id="e7e54-235">Předchozí kontroler definuje dvě akce, které se shodují:</span><span class="sxs-lookup"><span data-stu-id="e7e54-235">The preceding controller defines two actions that match:</span></span>

* <span data-ttu-id="e7e54-236">Cesta URL`/Products33/Edit/17`</span><span class="sxs-lookup"><span data-stu-id="e7e54-236">The URL path `/Products33/Edit/17`</span></span>
* <span data-ttu-id="e7e54-237">Směrování dat `{ controller = Products33, action = Edit, id = 17 }` .</span><span class="sxs-lookup"><span data-stu-id="e7e54-237">Route data `{ controller = Products33, action = Edit, id = 17 }`.</span></span>

<span data-ttu-id="e7e54-238">Toto je typický vzor pro řadiče MVC:</span><span class="sxs-lookup"><span data-stu-id="e7e54-238">This is a typical pattern for MVC controllers:</span></span>

* <span data-ttu-id="e7e54-239">`Edit(int)`zobrazí formulář pro úpravu produktu.</span><span class="sxs-lookup"><span data-stu-id="e7e54-239">`Edit(int)` displays a form to edit a product.</span></span>
* <span data-ttu-id="e7e54-240">`Edit(int, Product)`zpracuje publikovaný formulář.</span><span class="sxs-lookup"><span data-stu-id="e7e54-240">`Edit(int, Product)` processes  the posted form.</span></span>

<span data-ttu-id="e7e54-241">Postup při řešení správné trasy:</span><span class="sxs-lookup"><span data-stu-id="e7e54-241">To resolve the correct route:</span></span>

* <span data-ttu-id="e7e54-242">`Edit(int, Product)`je vybrána, pokud je požadavek HTTP `POST` .</span><span class="sxs-lookup"><span data-stu-id="e7e54-242">`Edit(int, Product)` is selected when the request is an HTTP `POST`.</span></span>
* <span data-ttu-id="e7e54-243">`Edit(int)`je vybrána, pokud je [příkaz HTTP](#verb) cokoliv jiného.</span><span class="sxs-lookup"><span data-stu-id="e7e54-243">`Edit(int)` is selected when the [HTTP verb](#verb) is anything else.</span></span> <span data-ttu-id="e7e54-244">`Edit(int)`obvykle se volá prostřednictvím `GET` .</span><span class="sxs-lookup"><span data-stu-id="e7e54-244">`Edit(int)` is generally called via `GET`.</span></span>

<span data-ttu-id="e7e54-245"><xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute> `[HttpPost]` Služba poskytuje směrování, aby se mohla zvolit na základě metody HTTP žádosti.</span><span class="sxs-lookup"><span data-stu-id="e7e54-245">The <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute>, `[HttpPost]`, is provided to routing so that it can choose based on the HTTP method of the request.</span></span> <span data-ttu-id="e7e54-246">Díky tomu je `HttpPostAttribute` `Edit(int, Product)` lepší shoda než `Edit(int)` .</span><span class="sxs-lookup"><span data-stu-id="e7e54-246">The `HttpPostAttribute` makes `Edit(int, Product)` a better match than `Edit(int)`.</span></span>

<span data-ttu-id="e7e54-247">Je důležité pochopit role, jako je například `HttpPostAttribute` .</span><span class="sxs-lookup"><span data-stu-id="e7e54-247">It's important to understand the role of attributes like `HttpPostAttribute`.</span></span> <span data-ttu-id="e7e54-248">Podobné atributy jsou definovány pro jiné [příkazy HTTP](#verb).</span><span class="sxs-lookup"><span data-stu-id="e7e54-248">Similar attributes are defined for other [HTTP verbs](#verb).</span></span> <span data-ttu-id="e7e54-249">V případě [konvenčního směrování](#cr)je běžné, že akce používají stejný název akce, když jsou součástí formuláře zobrazení, odeslání pracovního postupu formuláře.</span><span class="sxs-lookup"><span data-stu-id="e7e54-249">In [conventional routing](#cr), it's common for actions to use the same action name when they're part of a show form, submit form workflow.</span></span> <span data-ttu-id="e7e54-250">Příklad naleznete v tématu [prostudování dvou metod Edit Action](xref:tutorials/first-mvc-app/controller-methods-views#get-post).</span><span class="sxs-lookup"><span data-stu-id="e7e54-250">For example, see [Examine the two Edit action methods](xref:tutorials/first-mvc-app/controller-methods-views#get-post).</span></span>

<span data-ttu-id="e7e54-251">Pokud směrování nemůže zvolit nejlepší kandidáta, <xref:System.Reflection.AmbiguousMatchException> je vyvolána výjimka, která obsahuje seznam více odpovídajících koncových bodů.</span><span class="sxs-lookup"><span data-stu-id="e7e54-251">If routing can't choose a best candidate, an <xref:System.Reflection.AmbiguousMatchException> is thrown, listing the multiple matched endpoints.</span></span>

<a name="routing-route-name-ref-label"></a>

### <a name="conventional-route-names"></a><span data-ttu-id="e7e54-252">Názvy konvenčních tras</span><span class="sxs-lookup"><span data-stu-id="e7e54-252">Conventional route names</span></span>

<span data-ttu-id="e7e54-253">Řetězce `"blog"` a `"default"` v následujících příkladech jsou konvenční názvy tras:</span><span class="sxs-lookup"><span data-stu-id="e7e54-253">The strings  `"blog"` and `"default"` in the following examples are conventional route names:</span></span>

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

<span data-ttu-id="e7e54-254">Názvy tras udávají logický název trasy.</span><span class="sxs-lookup"><span data-stu-id="e7e54-254">The route names give the route a logical name.</span></span> <span data-ttu-id="e7e54-255">Pojmenovanou trasu lze použít pro generování adresy URL.</span><span class="sxs-lookup"><span data-stu-id="e7e54-255">The named route can be used for URL generation.</span></span> <span data-ttu-id="e7e54-256">Použití pojmenované trasy zjednodušuje vytváření adresy URL, když řazení tras může způsobit komplikované generování adresy URL.</span><span class="sxs-lookup"><span data-stu-id="e7e54-256">Using a named route simplifies URL creation when the ordering of routes could make URL generation complicated.</span></span> <span data-ttu-id="e7e54-257">Názvy tras musí být jedinečné aplikace v širším rozsahu.</span><span class="sxs-lookup"><span data-stu-id="e7e54-257">Route names must be unique application wide.</span></span>

<span data-ttu-id="e7e54-258">Názvy tras:</span><span class="sxs-lookup"><span data-stu-id="e7e54-258">Route names:</span></span>

* <span data-ttu-id="e7e54-259">Nemusíte mít žádný vliv na adresu URL ani manipulaci s požadavky.</span><span class="sxs-lookup"><span data-stu-id="e7e54-259">Have no impact on URL matching or handling of requests.</span></span>
* <span data-ttu-id="e7e54-260">Jsou používány pouze pro generování adresy URL.</span><span class="sxs-lookup"><span data-stu-id="e7e54-260">Are used only for URL generation.</span></span>

<span data-ttu-id="e7e54-261">Koncept názvu trasy je reprezentován ve směrování jako [IEndpointNameMetadata](xref:Microsoft.AspNetCore.Routing.IEndpointNameMetadata).</span><span class="sxs-lookup"><span data-stu-id="e7e54-261">The route name concept is represented in routing as [IEndpointNameMetadata](xref:Microsoft.AspNetCore.Routing.IEndpointNameMetadata).</span></span> <span data-ttu-id="e7e54-262">Název **Směrování** a název **koncového bodu**:</span><span class="sxs-lookup"><span data-stu-id="e7e54-262">The terms **route name** and **endpoint name**:</span></span>

* <span data-ttu-id="e7e54-263">Jsou zaměnitelné.</span><span class="sxs-lookup"><span data-stu-id="e7e54-263">Are interchangeable.</span></span>
* <span data-ttu-id="e7e54-264">Který je používán v dokumentaci a kód závisí na popisovaném rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="e7e54-264">Which one is used in documentation and code depends on the API being described.</span></span>

<a name="attribute-routing-ref-label"></a>
<a name="ar"></a>

## <a name="attribute-routing-for-rest-apis"></a><span data-ttu-id="e7e54-265">Směrování atributů pro rozhraní REST API</span><span class="sxs-lookup"><span data-stu-id="e7e54-265">Attribute routing for REST APIs</span></span>

<span data-ttu-id="e7e54-266">Rozhraní REST API by měly používat směrování atributů k modelování funkčnosti aplikace jako sady prostředků, ve kterých jsou operace reprezentované [příkazy HTTP](#verb).</span><span class="sxs-lookup"><span data-stu-id="e7e54-266">REST APIs should use attribute routing to model the app's functionality as a set of resources where operations are represented by [HTTP verbs](#verb).</span></span>

<span data-ttu-id="e7e54-267">Směrování atributů používá sadu atributů k mapování akcí přímo na šablony směrování.</span><span class="sxs-lookup"><span data-stu-id="e7e54-267">Attribute routing uses a set of attributes to map actions directly to route templates.</span></span> <span data-ttu-id="e7e54-268">Následující `StartUp.Configure` kód je typický pro REST API a používá se v další ukázce:</span><span class="sxs-lookup"><span data-stu-id="e7e54-268">The following `StartUp.Configure` code is typical for a REST API and is used in the next sample:</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupAPI.cs?name=snippet)]

<span data-ttu-id="e7e54-269">V předchozím kódu <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*> je volána uvnitř `UseEndpoints` pro mapování řadičů směrovaných na atribut.</span><span class="sxs-lookup"><span data-stu-id="e7e54-269">In the preceding code, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*> is called inside `UseEndpoints` to map attribute routed controllers.</span></span>

<span data-ttu-id="e7e54-270">V následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="e7e54-270">In the following example:</span></span>

* <span data-ttu-id="e7e54-271">Předchozí `Configure` metoda se používá.</span><span class="sxs-lookup"><span data-stu-id="e7e54-271">The preceding `Configure` method is used.</span></span>
* <span data-ttu-id="e7e54-272">`HomeController`vyhledá shodu se sadou adres URL podobných tomu, co výchozí konvenční trasa `{controller=Home}/{action=Index}/{id?}` odpovídá.</span><span class="sxs-lookup"><span data-stu-id="e7e54-272">`HomeController` matches a set of URLs similar to what the default conventional route `{controller=Home}/{action=Index}/{id?}` matches.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet2)]

<span data-ttu-id="e7e54-273">`HomeController.Index`Akce se spustí pro všechny cesty URL `/` , `/Home` , `/Home/Index` nebo `/Home/Index/3` .</span><span class="sxs-lookup"><span data-stu-id="e7e54-273">The `HomeController.Index` action is run for any of the URL paths `/`, `/Home`, `/Home/Index`, or `/Home/Index/3`.</span></span>

<span data-ttu-id="e7e54-274">V tomto příkladu se zvýrazní klíčový rozdíl mezi směrováním atributů a [konvenčním směrováním](#cr).</span><span class="sxs-lookup"><span data-stu-id="e7e54-274">This example highlights a key programming difference between attribute routing and [conventional routing](#cr).</span></span> <span data-ttu-id="e7e54-275">Směrování atributů vyžaduje více vstupu pro určení trasy.</span><span class="sxs-lookup"><span data-stu-id="e7e54-275">Attribute routing requires more input to specify a route.</span></span> <span data-ttu-id="e7e54-276">Konvenční výchozí trasa zpracovává trasy více stručně.</span><span class="sxs-lookup"><span data-stu-id="e7e54-276">The conventional default route handles routes more succinctly.</span></span> <span data-ttu-id="e7e54-277">Směrování atributů ale umožňuje a vyžaduje přesnou kontrolu nad tím, které šablony směrování se vztahují na každou [akci](#action).</span><span class="sxs-lookup"><span data-stu-id="e7e54-277">However, attribute routing allows and requires precise control of which route templates apply to each [action](#action).</span></span>

<span data-ttu-id="e7e54-278">Při směrování atributů nehraje název kontroleru a akce žádnou část, ve které se akce shoduje, pokud se nepoužije [náhrada tokenu](#routing-token-replacement-templates-ref-label) .</span><span class="sxs-lookup"><span data-stu-id="e7e54-278">With attribute routing, the controller and action names play no part in which action is matched, unless [token replacement](#routing-token-replacement-templates-ref-label) is used.</span></span> <span data-ttu-id="e7e54-279">V následujícím příkladu se shodují stejné adresy URL jako v předchozím příkladu:</span><span class="sxs-lookup"><span data-stu-id="e7e54-279">The following example matches the same URLs as the previous example:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemoController.cs?name=snippet)]

<span data-ttu-id="e7e54-280">Následující kód používá nahrazení tokenu pro `action` a `controller` :</span><span class="sxs-lookup"><span data-stu-id="e7e54-280">The following code uses token replacement for `action` and `controller`:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet22)]

<span data-ttu-id="e7e54-281">Následující kód platí `[Route("[controller]/[action]")]` pro kontroler:</span><span class="sxs-lookup"><span data-stu-id="e7e54-281">The following code applies `[Route("[controller]/[action]")]` to the controller:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet24)]

<span data-ttu-id="e7e54-282">V předchozím kódu `Index` musí šablony metody předřadit `/` nebo `~/` do šablon směrování.</span><span class="sxs-lookup"><span data-stu-id="e7e54-282">In the preceding code, the `Index` method templates must prepend `/` or `~/` to the route templates.</span></span> <span data-ttu-id="e7e54-283">Šablony směrování použité pro akci, která začíná `/` nebo `~/` není kombinována s šablonami směrování použitými pro kontroler.</span><span class="sxs-lookup"><span data-stu-id="e7e54-283">Route templates applied to an action that begin with `/` or `~/` don't get combined with route templates applied to the controller.</span></span>

<span data-ttu-id="e7e54-284">Informace o výběru šablony směrování najdete v tématu [Priorita šablony směrování](xref:fundamentals/routing#rtp) .</span><span class="sxs-lookup"><span data-stu-id="e7e54-284">See [Route template precedence](xref:fundamentals/routing#rtp) for information on route template selection.</span></span>

## <a name="reserved-routing-names"></a><span data-ttu-id="e7e54-285">Názvy rezervovaných směrování</span><span class="sxs-lookup"><span data-stu-id="e7e54-285">Reserved routing names</span></span>

<span data-ttu-id="e7e54-286">Následující klíčová slova jsou rezervované názvy parametrů tras při použití řadičů nebo Razor stránek:</span><span class="sxs-lookup"><span data-stu-id="e7e54-286">The following keywords are reserved route parameter names when using Controllers or Razor Pages:</span></span>

* `action`
* `area`
* `controller`
* `handler`
* `page`

<span data-ttu-id="e7e54-287">Použití `page` jako parametr trasy se směrováním atributů je běžná chyba.</span><span class="sxs-lookup"><span data-stu-id="e7e54-287">Using `page` as a route parameter with attribute routing is a common error.</span></span> <span data-ttu-id="e7e54-288">Výsledkem je nekonzistentní a matoucí chování s generováním adresy URL.</span><span class="sxs-lookup"><span data-stu-id="e7e54-288">Doing that results in inconsistent and confusing behavior with URL generation.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemo2Controller.cs?name=snippet)]

<span data-ttu-id="e7e54-289">Speciální názvy parametrů jsou používány generováním adresy URL k určení, zda operace generování adresy URL odkazuje na Razor stránku nebo na kontroler.</span><span class="sxs-lookup"><span data-stu-id="e7e54-289">The special parameter names are used by the URL generation to determine if a URL generation operation refers to a Razor Page or to a Controller.</span></span>

<a name="verb"></a>

## <a name="http-verb-templates"></a><span data-ttu-id="e7e54-290">Šablony příkazů HTTP</span><span class="sxs-lookup"><span data-stu-id="e7e54-290">HTTP verb templates</span></span>

<span data-ttu-id="e7e54-291">ASP.NET Core má následující šablony příkazů HTTP:</span><span class="sxs-lookup"><span data-stu-id="e7e54-291">ASP.NET Core has the following HTTP verb templates:</span></span>

* <span data-ttu-id="e7e54-292">[HttpGet](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)</span><span class="sxs-lookup"><span data-stu-id="e7e54-292">[[HttpGet]](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)</span></span>
* <span data-ttu-id="e7e54-293">[HTTPPOST](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute)</span><span class="sxs-lookup"><span data-stu-id="e7e54-293">[[HttpPost]](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute)</span></span>
* <span data-ttu-id="e7e54-294">[[HttpPut]](xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute)</span><span class="sxs-lookup"><span data-stu-id="e7e54-294">[[HttpPut]](xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute)</span></span>
* <span data-ttu-id="e7e54-295">[[HttpDelete]](xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute)</span><span class="sxs-lookup"><span data-stu-id="e7e54-295">[[HttpDelete]](xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute)</span></span>
* <span data-ttu-id="e7e54-296">[[HttpHead]](xref:Microsoft.AspNetCore.Mvc.HttpHeadAttribute)</span><span class="sxs-lookup"><span data-stu-id="e7e54-296">[[HttpHead]](xref:Microsoft.AspNetCore.Mvc.HttpHeadAttribute)</span></span>
* <span data-ttu-id="e7e54-297">[[HttpPatch]](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)</span><span class="sxs-lookup"><span data-stu-id="e7e54-297">[[HttpPatch]](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)</span></span>

<a name="rt"></a>

### <a name="route-templates"></a><span data-ttu-id="e7e54-298">Šablony směrování</span><span class="sxs-lookup"><span data-stu-id="e7e54-298">Route templates</span></span>

<span data-ttu-id="e7e54-299">ASP.NET Core má následující šablony tras:</span><span class="sxs-lookup"><span data-stu-id="e7e54-299">ASP.NET Core has the following route templates:</span></span>

* <span data-ttu-id="e7e54-300">Všechny [šablony operací HTTP](#verb) jsou šablony směrování.</span><span class="sxs-lookup"><span data-stu-id="e7e54-300">All the [HTTP verb templates](#verb) are route templates.</span></span>
* <span data-ttu-id="e7e54-301">[Cestě](xref:Microsoft.AspNetCore.Mvc.RouteAttribute)</span><span class="sxs-lookup"><span data-stu-id="e7e54-301">[[Route]](xref:Microsoft.AspNetCore.Mvc.RouteAttribute)</span></span>

<a name="arx"></a>

### <a name="attribute-routing-with-http-verb-attributes"></a><span data-ttu-id="e7e54-302">Směrování atributů s atributy příkazu http</span><span class="sxs-lookup"><span data-stu-id="e7e54-302">Attribute routing with Http verb attributes</span></span>

<span data-ttu-id="e7e54-303">Vezměte v úvahu následující kontroler:</span><span class="sxs-lookup"><span data-stu-id="e7e54-303">Consider the following controller:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet)]

<span data-ttu-id="e7e54-304">V předchozím kódu:</span><span class="sxs-lookup"><span data-stu-id="e7e54-304">In the preceding code:</span></span>

* <span data-ttu-id="e7e54-305">Každá akce obsahuje `[HttpGet]` atribut, který omezuje porovnání pouze s požadavky HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="e7e54-305">Each action contains the `[HttpGet]` attribute, which constrains matching to HTTP GET requests only.</span></span>
* <span data-ttu-id="e7e54-306">Tato `GetProduct` akce zahrnuje `"{id}"` šablonu, proto `id` je připojená k `"api/[controller]"` šabloně na řadiči.</span><span class="sxs-lookup"><span data-stu-id="e7e54-306">The `GetProduct` action includes the `"{id}"` template, therefore `id` is appended to the `"api/[controller]"` template on the controller.</span></span> <span data-ttu-id="e7e54-307">Šablona metod je `"api/[controller]/"{id}""` .</span><span class="sxs-lookup"><span data-stu-id="e7e54-307">The methods template is `"api/[controller]/"{id}""`.</span></span> <span data-ttu-id="e7e54-308">Proto tato akce odpovídá pouze požadavkům get pro formulář `/api/test2/xyz` , `/api/test2/123` , `/api/test2/{any string}` atd.</span><span class="sxs-lookup"><span data-stu-id="e7e54-308">Therefore this action only matches GET requests of for the form `/api/test2/xyz`,`/api/test2/123`,`/api/test2/{any string}`, etc.</span></span>
  [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet2)]
* <span data-ttu-id="e7e54-309">`GetIntProduct`Akce obsahuje `"int/{id:int}")` šablonu.</span><span class="sxs-lookup"><span data-stu-id="e7e54-309">The `GetIntProduct` action contains the `"int/{id:int}")` template.</span></span> <span data-ttu-id="e7e54-310">`:int`Část šablony omezuje `id` hodnoty směrování na řetězce, které lze převést na celé číslo.</span><span class="sxs-lookup"><span data-stu-id="e7e54-310">The `:int` portion of the template constrains the `id` route values to strings that can be converted to an integer.</span></span> <span data-ttu-id="e7e54-311">Požadavek GET na `/api/test2/int/abc` :</span><span class="sxs-lookup"><span data-stu-id="e7e54-311">A GET request to `/api/test2/int/abc`:</span></span>
  * <span data-ttu-id="e7e54-312">Neodpovídá této akci.</span><span class="sxs-lookup"><span data-stu-id="e7e54-312">Doesn't match this action.</span></span>
  * <span data-ttu-id="e7e54-313">Vrátí chybu typu 404, která [nebyla nalezena](https://developer.mozilla.org/docs/Web/HTTP/Status/404) .</span><span class="sxs-lookup"><span data-stu-id="e7e54-313">Returns a [404 Not Found](https://developer.mozilla.org/docs/Web/HTTP/Status/404) error.</span></span>
    [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet3)]
* <span data-ttu-id="e7e54-314">`GetInt2Product`Akce obsahuje `{id}` v šabloně, ale neomezí `id` na hodnoty, které lze převést na celé číslo.</span><span class="sxs-lookup"><span data-stu-id="e7e54-314">The `GetInt2Product` action contains `{id}` in the template, but doesn't constrain `id` to values that can be converted to an integer.</span></span> <span data-ttu-id="e7e54-315">Požadavek GET na `/api/test2/int2/abc` :</span><span class="sxs-lookup"><span data-stu-id="e7e54-315">A GET request to `/api/test2/int2/abc`:</span></span>
  * <span data-ttu-id="e7e54-316">Odpovídá této trase.</span><span class="sxs-lookup"><span data-stu-id="e7e54-316">Matches this route.</span></span>
  * <span data-ttu-id="e7e54-317">Vazbu modelu se nepodařilo převést `abc` na celé číslo.</span><span class="sxs-lookup"><span data-stu-id="e7e54-317">Model binding fails to convert `abc` to an integer.</span></span> <span data-ttu-id="e7e54-318">`id`Parametr metody je celé číslo.</span><span class="sxs-lookup"><span data-stu-id="e7e54-318">The `id` parameter of the method is integer.</span></span>
  * <span data-ttu-id="e7e54-319">Vrátí [chybný požadavek 400](https://developer.mozilla.org/docs/Web/HTTP/Status/400) , protože vazba modelu selhala při převodu `abc` na celé číslo.</span><span class="sxs-lookup"><span data-stu-id="e7e54-319">Returns a [400 Bad Request](https://developer.mozilla.org/docs/Web/HTTP/Status/400) because model binding failed to convert`abc` to an integer.</span></span>
      [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet4)]

<span data-ttu-id="e7e54-320">Směrování atributů může používat <xref:Microsoft.AspNetCore.Mvc.Routing.HttpMethodAttribute> atributy jako <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute> , <xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute> a <xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute> .</span><span class="sxs-lookup"><span data-stu-id="e7e54-320">Attribute routing can use <xref:Microsoft.AspNetCore.Mvc.Routing.HttpMethodAttribute> attributes such as <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute>, <xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute>, and <xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute>.</span></span> <span data-ttu-id="e7e54-321">Všechny atributy [příkazu http](#verb) přijímají šablonu směrování.</span><span class="sxs-lookup"><span data-stu-id="e7e54-321">All of the [HTTP verb](#verb) attributes accept a route template.</span></span> <span data-ttu-id="e7e54-322">Následující příklad ukazuje dvě akce, které odpovídají stejné šabloně směrování:</span><span class="sxs-lookup"><span data-stu-id="e7e54-322">The following example shows two actions that match the same route template:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyProductsController.cs?name=snippet1)]

<span data-ttu-id="e7e54-323">Pomocí cesty URL `/products3` :</span><span class="sxs-lookup"><span data-stu-id="e7e54-323">Using the URL path `/products3`:</span></span>

* <span data-ttu-id="e7e54-324">`MyProductsController.ListProducts`Akce se spustí, když je [příkaz HTTP](#verb) `GET` .</span><span class="sxs-lookup"><span data-stu-id="e7e54-324">The `MyProductsController.ListProducts` action runs when the [HTTP verb](#verb) is `GET`.</span></span>
* <span data-ttu-id="e7e54-325">`MyProductsController.CreateProduct`Akce se spustí, když je [příkaz HTTP](#verb) `POST` .</span><span class="sxs-lookup"><span data-stu-id="e7e54-325">The `MyProductsController.CreateProduct` action runs when the [HTTP verb](#verb) is `POST`.</span></span>

<span data-ttu-id="e7e54-326">Při sestavování REST API je zřídka nutné použít `[Route(...)]` metodu akce, protože akce akceptuje všechny metody HTTP.</span><span class="sxs-lookup"><span data-stu-id="e7e54-326">When building a REST API, it's rare that you'll need to use `[Route(...)]` on an action method because the action accepts all HTTP methods.</span></span> <span data-ttu-id="e7e54-327">Je lepší použít konkrétnější [atribut příkazu http](#verb) , abyste mohli přesně zjistit, co vaše rozhraní API podporuje.</span><span class="sxs-lookup"><span data-stu-id="e7e54-327">It's better to use the more specific [HTTP verb attribute](#verb) to be precise about what your API supports.</span></span> <span data-ttu-id="e7e54-328">Očekává se, že klienti rozhraní REST API znají, které cesty a příkazy HTTP se mapují na konkrétní logické operace.</span><span class="sxs-lookup"><span data-stu-id="e7e54-328">Clients of REST APIs are expected to know what paths and HTTP verbs map to specific logical operations.</span></span>

<span data-ttu-id="e7e54-329">Rozhraní REST API by měly používat směrování atributů k modelování funkčnosti aplikace jako sady prostředků, ve kterých jsou operace reprezentované příkazy HTTP.</span><span class="sxs-lookup"><span data-stu-id="e7e54-329">REST APIs should use attribute routing to model the app's functionality as a set of resources where operations are represented by HTTP verbs.</span></span> <span data-ttu-id="e7e54-330">To znamená, že mnohé operace, například GET a POST u stejného logického prostředku, používají stejnou adresu URL.</span><span class="sxs-lookup"><span data-stu-id="e7e54-330">This means that many operations, for example, GET and POST on the same logical resource use the same URL.</span></span> <span data-ttu-id="e7e54-331">Směrování atributů poskytuje úroveň řízení, která je nutná k pečlivému návrhu rozložení veřejného koncového bodu rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="e7e54-331">Attribute routing provides a level of control that's needed to carefully design an API's public endpoint layout.</span></span>

<span data-ttu-id="e7e54-332">Vzhledem k tomu, že trasa atributu se vztahuje na konkrétní akci, je snadné vytvořit parametry požadované v rámci definice šablony trasy.</span><span class="sxs-lookup"><span data-stu-id="e7e54-332">Since an attribute route applies to a specific action, it's easy to make parameters required as part of the route template definition.</span></span> <span data-ttu-id="e7e54-333">V následujícím příkladu `id` je vyžadována jako součást cesty URL:</span><span class="sxs-lookup"><span data-stu-id="e7e54-333">In the following example, `id` is required as part of the URL path:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet2)]

<span data-ttu-id="e7e54-334">`Products2ApiController.GetProduct(int)`Akce:</span><span class="sxs-lookup"><span data-stu-id="e7e54-334">The `Products2ApiController.GetProduct(int)` action:</span></span>

* <span data-ttu-id="e7e54-335">Se spouští s cestou URL jako`/products2/3`</span><span class="sxs-lookup"><span data-stu-id="e7e54-335">Is run with URL path like `/products2/3`</span></span>
* <span data-ttu-id="e7e54-336">Není spuštěn s cestou URL `/products2` .</span><span class="sxs-lookup"><span data-stu-id="e7e54-336">Isn't run with the URL path `/products2`.</span></span>

<span data-ttu-id="e7e54-337">Atribut [[](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>) ] umožňuje akci omezit podporované typy obsahu požadavků.</span><span class="sxs-lookup"><span data-stu-id="e7e54-337">The [[Consumes]](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>) attribute allows an action to limit the supported request content types.</span></span> <span data-ttu-id="e7e54-338">Další informace najdete v tématu [Definování podporovaných typů obsahu požadavků pomocí atributu spotřebes](xref:web-api/index#consumes).</span><span class="sxs-lookup"><span data-stu-id="e7e54-338">For more information, see [Define supported request content types with the Consumes attribute](xref:web-api/index#consumes).</span></span>

 <span data-ttu-id="e7e54-339">Úplný popis šablon směrování a souvisejících možností najdete v tématu věnovaném [Směrování](xref:fundamentals/routing) .</span><span class="sxs-lookup"><span data-stu-id="e7e54-339">See [Routing](xref:fundamentals/routing) for a full description of route templates and related options.</span></span>

<span data-ttu-id="e7e54-340">Další informace o naleznete v `[ApiController]` tématu [atribut ApiController](xref:web-api/index##apicontroller-attribute).</span><span class="sxs-lookup"><span data-stu-id="e7e54-340">For more information on `[ApiController]`, see [ApiController attribute](xref:web-api/index##apicontroller-attribute).</span></span>

## <a name="route-name"></a><span data-ttu-id="e7e54-341">Název trasy</span><span class="sxs-lookup"><span data-stu-id="e7e54-341">Route name</span></span>

<span data-ttu-id="e7e54-342">Následující kód definuje název trasy `Products_List` :</span><span class="sxs-lookup"><span data-stu-id="e7e54-342">The following code  defines a route name of `Products_List`:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet2)]

<span data-ttu-id="e7e54-343">Názvy tras se dají použít k vygenerování adresy URL na základě konkrétní trasy.</span><span class="sxs-lookup"><span data-stu-id="e7e54-343">Route names can be used to generate a URL based on a specific route.</span></span> <span data-ttu-id="e7e54-344">Názvy tras:</span><span class="sxs-lookup"><span data-stu-id="e7e54-344">Route names:</span></span>

* <span data-ttu-id="e7e54-345">Nemá žádný vliv na chování směrování, které odpovídá adrese URL.</span><span class="sxs-lookup"><span data-stu-id="e7e54-345">Have no impact on the URL matching behavior of routing.</span></span>
* <span data-ttu-id="e7e54-346">Používá se pouze pro generování adresy URL.</span><span class="sxs-lookup"><span data-stu-id="e7e54-346">Are only used for URL generation.</span></span>

<span data-ttu-id="e7e54-347">Názvy tras musí být jedinečné aplikace v rozsahu.</span><span class="sxs-lookup"><span data-stu-id="e7e54-347">Route names must be unique application-wide.</span></span>

<span data-ttu-id="e7e54-348">Na rozdíl od předchozího kódu s konvenční výchozí trasou, která definuje `id` parametr jako volitelné ( `{id?}` ).</span><span class="sxs-lookup"><span data-stu-id="e7e54-348">Contrast the preceding code with the conventional default route, which defines the `id` parameter as optional (`{id?}`).</span></span> <span data-ttu-id="e7e54-349">Možnost přesně zadat rozhraní API má výhody, jako je povolení `/products` a `/products/5` odeslání do různých akcí.</span><span class="sxs-lookup"><span data-stu-id="e7e54-349">The ability to precisely specify APIs has advantages, such as  allowing `/products` and `/products/5` to be dispatched to different actions.</span></span>

<a name="routing-combining-ref-label"></a>

## <a name="combining-attribute-routes"></a><span data-ttu-id="e7e54-350">Kombinování tras atributů</span><span class="sxs-lookup"><span data-stu-id="e7e54-350">Combining attribute routes</span></span>

<span data-ttu-id="e7e54-351">Aby bylo směrování atributů méně opakované, jsou atributy směrování na kontroleru kombinovány s atributy směrování na jednotlivých akcích.</span><span class="sxs-lookup"><span data-stu-id="e7e54-351">To make attribute routing less repetitive, route attributes on the controller are combined with route attributes on the individual actions.</span></span> <span data-ttu-id="e7e54-352">Každá šablona trasy definovaná na řadiči je předá směrování šablon na akcích.</span><span class="sxs-lookup"><span data-stu-id="e7e54-352">Any route templates defined on the controller are prepended to route templates on the actions.</span></span> <span data-ttu-id="e7e54-353">Umístěním atributu směrování na řadiči se vytvoří **všechny** akce v řadiči použít směrování atributů.</span><span class="sxs-lookup"><span data-stu-id="e7e54-353">Placing a route attribute on the controller makes **all** actions in the controller use attribute routing.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet)]

<span data-ttu-id="e7e54-354">V předchozím příkladu:</span><span class="sxs-lookup"><span data-stu-id="e7e54-354">In the preceding example:</span></span>

* <span data-ttu-id="e7e54-355">Cesta URL se `/products` může shodovat.`ProductsApi.ListProducts`</span><span class="sxs-lookup"><span data-stu-id="e7e54-355">The URL path `/products` can match `ProductsApi.ListProducts`</span></span>
* <span data-ttu-id="e7e54-356">Cesta URL se `/products/5` může shodovat `ProductsApi.GetProduct(int)` .</span><span class="sxs-lookup"><span data-stu-id="e7e54-356">The URL path `/products/5` can match `ProductsApi.GetProduct(int)`.</span></span>

<span data-ttu-id="e7e54-357">Obě tyto akce odpovídají pouze HTTP, `GET` protože jsou označeny `[HttpGet]` atributem.</span><span class="sxs-lookup"><span data-stu-id="e7e54-357">Both of these actions only match HTTP `GET` because they're marked with the `[HttpGet]` attribute.</span></span>

<span data-ttu-id="e7e54-358">Šablony směrování použité pro akci, která začíná `/` nebo `~/` není kombinována s šablonami směrování použitými pro kontroler.</span><span class="sxs-lookup"><span data-stu-id="e7e54-358">Route templates applied to an action that begin with `/` or `~/` don't get combined with route templates applied to the controller.</span></span> <span data-ttu-id="e7e54-359">Následující příklad odpovídá sadě cest URL podobně jako výchozí trasa.</span><span class="sxs-lookup"><span data-stu-id="e7e54-359">The following example matches a set of URL paths similar to the default route.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet)]

<span data-ttu-id="e7e54-360">Následující tabulka popisuje `[Route]` atributy v předchozím kódu:</span><span class="sxs-lookup"><span data-stu-id="e7e54-360">The following table explains the `[Route]` attributes in the preceding code:</span></span>

| <span data-ttu-id="e7e54-361">Atribut</span><span class="sxs-lookup"><span data-stu-id="e7e54-361">Attribute</span></span>               | <span data-ttu-id="e7e54-362">Kombinuje s`[Route("Home")]`</span><span class="sxs-lookup"><span data-stu-id="e7e54-362">Combines with `[Route("Home")]`</span></span> | <span data-ttu-id="e7e54-363">Definuje šablonu směrování.</span><span class="sxs-lookup"><span data-stu-id="e7e54-363">Defines route template</span></span> |
| ---
<span data-ttu-id="e7e54-364">Název: Autor: Popis: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="e7e54-364">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="e7e54-365">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e7e54-365">'Blazor'</span></span>
- <span data-ttu-id="e7e54-366">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e7e54-366">'Identity'</span></span>
- <span data-ttu-id="e7e54-367">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e7e54-367">'Let's Encrypt'</span></span>
- <span data-ttu-id="e7e54-368">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e7e54-368">'Razor'</span></span>
- <span data-ttu-id="e7e54-369">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="e7e54-369">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e7e54-370">Název: Autor: Popis: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="e7e54-370">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="e7e54-371">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e7e54-371">'Blazor'</span></span>
- <span data-ttu-id="e7e54-372">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e7e54-372">'Identity'</span></span>
- <span data-ttu-id="e7e54-373">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e7e54-373">'Let's Encrypt'</span></span>
- <span data-ttu-id="e7e54-374">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e7e54-374">'Razor'</span></span>
- <span data-ttu-id="e7e54-375">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="e7e54-375">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e7e54-376">Název: Autor: Popis: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="e7e54-376">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="e7e54-377">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e7e54-377">'Blazor'</span></span>
- <span data-ttu-id="e7e54-378">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e7e54-378">'Identity'</span></span>
- <span data-ttu-id="e7e54-379">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e7e54-379">'Let's Encrypt'</span></span>
- <span data-ttu-id="e7e54-380">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e7e54-380">'Razor'</span></span>
- <span data-ttu-id="e7e54-381">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="e7e54-381">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e7e54-382">Název: Autor: Popis: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="e7e54-382">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="e7e54-383">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e7e54-383">'Blazor'</span></span>
- <span data-ttu-id="e7e54-384">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e7e54-384">'Identity'</span></span>
- <span data-ttu-id="e7e54-385">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e7e54-385">'Let's Encrypt'</span></span>
- <span data-ttu-id="e7e54-386">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e7e54-386">'Razor'</span></span>
- <span data-ttu-id="e7e54-387">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="e7e54-387">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e7e54-388">Název: Autor: Popis: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="e7e54-388">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="e7e54-389">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e7e54-389">'Blazor'</span></span>
- <span data-ttu-id="e7e54-390">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e7e54-390">'Identity'</span></span>
- <span data-ttu-id="e7e54-391">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e7e54-391">'Let's Encrypt'</span></span>
- <span data-ttu-id="e7e54-392">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e7e54-392">'Razor'</span></span>
- <span data-ttu-id="e7e54-393">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="e7e54-393">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e7e54-394">Název: Autor: Popis: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="e7e54-394">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="e7e54-395">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e7e54-395">'Blazor'</span></span>
- <span data-ttu-id="e7e54-396">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e7e54-396">'Identity'</span></span>
- <span data-ttu-id="e7e54-397">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e7e54-397">'Let's Encrypt'</span></span>
- <span data-ttu-id="e7e54-398">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e7e54-398">'Razor'</span></span>
- <span data-ttu-id="e7e54-399">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="e7e54-399">'SignalR' uid:</span></span> 

<span data-ttu-id="e7e54-400">--------- | ---Název: Autor: Popis: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="e7e54-400">--------- | --- title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="e7e54-401">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e7e54-401">'Blazor'</span></span>
- <span data-ttu-id="e7e54-402">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e7e54-402">'Identity'</span></span>
- <span data-ttu-id="e7e54-403">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e7e54-403">'Let's Encrypt'</span></span>
- <span data-ttu-id="e7e54-404">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e7e54-404">'Razor'</span></span>
- <span data-ttu-id="e7e54-405">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="e7e54-405">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e7e54-406">Název: Autor: Popis: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="e7e54-406">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="e7e54-407">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e7e54-407">'Blazor'</span></span>
- <span data-ttu-id="e7e54-408">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e7e54-408">'Identity'</span></span>
- <span data-ttu-id="e7e54-409">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e7e54-409">'Let's Encrypt'</span></span>
- <span data-ttu-id="e7e54-410">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e7e54-410">'Razor'</span></span>
- <span data-ttu-id="e7e54-411">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="e7e54-411">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e7e54-412">Název: Autor: Popis: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="e7e54-412">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="e7e54-413">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e7e54-413">'Blazor'</span></span>
- <span data-ttu-id="e7e54-414">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e7e54-414">'Identity'</span></span>
- <span data-ttu-id="e7e54-415">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e7e54-415">'Let's Encrypt'</span></span>
- <span data-ttu-id="e7e54-416">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e7e54-416">'Razor'</span></span>
- <span data-ttu-id="e7e54-417">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="e7e54-417">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e7e54-418">Název: Autor: Popis: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="e7e54-418">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="e7e54-419">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e7e54-419">'Blazor'</span></span>
- <span data-ttu-id="e7e54-420">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e7e54-420">'Identity'</span></span>
- <span data-ttu-id="e7e54-421">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e7e54-421">'Let's Encrypt'</span></span>
- <span data-ttu-id="e7e54-422">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e7e54-422">'Razor'</span></span>
- <span data-ttu-id="e7e54-423">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="e7e54-423">'SignalR' uid:</span></span> 

<span data-ttu-id="e7e54-424">------ | ---Název: Autor: Popis: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="e7e54-424">------ | --- title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="e7e54-425">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e7e54-425">'Blazor'</span></span>
- <span data-ttu-id="e7e54-426">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e7e54-426">'Identity'</span></span>
- <span data-ttu-id="e7e54-427">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e7e54-427">'Let's Encrypt'</span></span>
- <span data-ttu-id="e7e54-428">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e7e54-428">'Razor'</span></span>
- <span data-ttu-id="e7e54-429">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="e7e54-429">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e7e54-430">Název: Autor: Popis: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="e7e54-430">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="e7e54-431">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e7e54-431">'Blazor'</span></span>
- <span data-ttu-id="e7e54-432">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e7e54-432">'Identity'</span></span>
- <span data-ttu-id="e7e54-433">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e7e54-433">'Let's Encrypt'</span></span>
- <span data-ttu-id="e7e54-434">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e7e54-434">'Razor'</span></span>
- <span data-ttu-id="e7e54-435">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="e7e54-435">'SignalR' uid:</span></span> 

<span data-ttu-id="e7e54-436">----- | | `[Route("")]` | Ano | `"Home"` |
| `[Route("Index")]` | Ano | `"Home/Index"` |
| `[Route("/")]` | **Žádné** | `""` |
 | `[Route("About")]` | Ano | `"Home/About"`|</span><span class="sxs-lookup"><span data-stu-id="e7e54-436">----- | | `[Route("")]` | Yes | `"Home"` |
| `[Route("Index")]` | Yes | `"Home/Index"` |
| `[Route("/")]` | **No** | `""` |
| `[Route("About")]` | Yes | `"Home/About"` |</span></span>

<a name="routing-ordering-ref-label"></a>
<a name="oar"></a>

### <a name="attribute-route-order"></a><span data-ttu-id="e7e54-437">Pořadí směrování atributu</span><span class="sxs-lookup"><span data-stu-id="e7e54-437">Attribute route order</span></span>

<span data-ttu-id="e7e54-438">Směrování sestaví strom a porovnává se všemi koncovými body současně:</span><span class="sxs-lookup"><span data-stu-id="e7e54-438">Routing builds a tree and matches all endpoints simultaneously:</span></span>

* <span data-ttu-id="e7e54-439">Položky směrování se chovají stejně, jako kdyby byly umístěny v ideálním pořadí.</span><span class="sxs-lookup"><span data-stu-id="e7e54-439">The route entries behave as if placed in an ideal ordering.</span></span>
* <span data-ttu-id="e7e54-440">Nejvíce konkrétní trasy mají možnost provést před obecnější trasy.</span><span class="sxs-lookup"><span data-stu-id="e7e54-440">The most specific routes have a chance to execute before the more general routes.</span></span>

<span data-ttu-id="e7e54-441">Například trasa atributu jako `blog/search/{topic}` je konkrétnější než trasa atributu jako `blog/{*article}` .</span><span class="sxs-lookup"><span data-stu-id="e7e54-441">For example, an attribute route like `blog/search/{topic}` is more specific than an attribute route like `blog/{*article}`.</span></span> <span data-ttu-id="e7e54-442">`blog/search/{topic}`Ve výchozím nastavení tras má vyšší prioritu, protože je konkrétnější.</span><span class="sxs-lookup"><span data-stu-id="e7e54-442">The `blog/search/{topic}` route has higher priority, by default, because it's more specific.</span></span> <span data-ttu-id="e7e54-443">Pomocí [konvenčního směrování](#cr)zodpovídá vývojář za umístění tras v požadovaném pořadí.</span><span class="sxs-lookup"><span data-stu-id="e7e54-443">Using [conventional routing](#cr), the developer is responsible for placing routes in the desired order.</span></span>

<span data-ttu-id="e7e54-444">Trasy atributů mohou konfigurovat objednávku pomocí <xref:Microsoft.AspNetCore.Mvc.RouteAttribute.Order> Vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="e7e54-444">Attribute routes can configure an order using the <xref:Microsoft.AspNetCore.Mvc.RouteAttribute.Order> property.</span></span> <span data-ttu-id="e7e54-445">Součástí jsou všechny [atributy tras](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) , které poskytuje rozhraní `Order` .</span><span class="sxs-lookup"><span data-stu-id="e7e54-445">All of the framework provided [route attributes](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) include `Order` .</span></span> <span data-ttu-id="e7e54-446">Trasy jsou zpracovávány podle vzestupného řazení `Order` Vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="e7e54-446">Routes are processed according to an ascending sort of the `Order` property.</span></span> <span data-ttu-id="e7e54-447">Výchozí pořadí je `0` .</span><span class="sxs-lookup"><span data-stu-id="e7e54-447">The default order is `0`.</span></span> <span data-ttu-id="e7e54-448">Nastavování trasy pomocí `Order = -1` spuštění před trasami, které nenastaví objednávku.</span><span class="sxs-lookup"><span data-stu-id="e7e54-448">Setting a route using `Order = -1` runs before routes that don't set an order.</span></span> <span data-ttu-id="e7e54-449">Nastavení trasy pomocí `Order = 1` spuštění po výchozím řazení směrování.</span><span class="sxs-lookup"><span data-stu-id="e7e54-449">Setting a route using `Order = 1` runs after default route ordering.</span></span>

<span data-ttu-id="e7e54-450">**Nepoužívejte** v závislosti `Order` .</span><span class="sxs-lookup"><span data-stu-id="e7e54-450">**Avoid** depending on `Order`.</span></span> <span data-ttu-id="e7e54-451">Pokud je místo na adrese URL aplikace nutné správně směrovat hodnoty pořadí, je pravděpodobné, že budou i u klientů matoucí.</span><span class="sxs-lookup"><span data-stu-id="e7e54-451">If an app's URL-space requires explicit order values to route correctly, then it's likely confusing to clients as well.</span></span> <span data-ttu-id="e7e54-452">Obecně platí, že směrování atributů vybere správnou trasu s odpovídající adresou URL.</span><span class="sxs-lookup"><span data-stu-id="e7e54-452">In general, attribute routing selects the correct route with URL matching.</span></span> <span data-ttu-id="e7e54-453">Pokud výchozí pořadí použité pro generování adresy URL nefunguje, je použití názvu trasy jako přepsání obvykle jednodušší než použití `Order` Vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="e7e54-453">If the default order used for URL generation isn't working, using a route name as an override is usually simpler than applying the `Order` property.</span></span>

<span data-ttu-id="e7e54-454">Vezměte v úvahu následující dva řadiče, které definují obě trasy `/home` :</span><span class="sxs-lookup"><span data-stu-id="e7e54-454">Consider the following two controllers which both define the route matching `/home`:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet2)]

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemoController.cs?name=snippet)]

<span data-ttu-id="e7e54-455">Požadavek `/home` s předchozím kódem vyvolá výjimku, která je podobná následující:</span><span class="sxs-lookup"><span data-stu-id="e7e54-455">Requesting `/home` with the preceding code throws an exception similar to the following:</span></span>

```text
AmbiguousMatchException: The request matched multiple endpoints. Matches:

 WebMvcRouting.Controllers.HomeController.Index
 WebMvcRouting.Controllers.MyDemoController.MyIndex
```

<span data-ttu-id="e7e54-456">Přidání `Order` do jednoho z atributů trasy řeší nejednoznačnost:</span><span class="sxs-lookup"><span data-stu-id="e7e54-456">Adding `Order` to one of the route attributes resolves the ambiguity:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemo3Controller.cs?name=snippet3& highlight=2)]

<span data-ttu-id="e7e54-457">S předchozím kódem `/home` spustí `HomeController.Index` koncový bod.</span><span class="sxs-lookup"><span data-stu-id="e7e54-457">With the preceding code, `/home` runs the `HomeController.Index` endpoint.</span></span> <span data-ttu-id="e7e54-458">Pro získání `MyDemoController.MyIndex` žádosti `/home/MyIndex` .</span><span class="sxs-lookup"><span data-stu-id="e7e54-458">To get to the `MyDemoController.MyIndex`, request `/home/MyIndex`.</span></span> <span data-ttu-id="e7e54-459">**Poznámka**:</span><span class="sxs-lookup"><span data-stu-id="e7e54-459">**Note**:</span></span>

* <span data-ttu-id="e7e54-460">Předchozí kód je příkladem nebo nekvalitní návrh směrování.</span><span class="sxs-lookup"><span data-stu-id="e7e54-460">The preceding code is an example or poor routing design.</span></span> <span data-ttu-id="e7e54-461">Byla použita k ilustraci `Order` Vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="e7e54-461">It was used to illustrate the `Order` property.</span></span>
* <span data-ttu-id="e7e54-462">`Order`Vlastnost pouze vyřeší nejednoznačnost, tuto šablonu nelze spárovat.</span><span class="sxs-lookup"><span data-stu-id="e7e54-462">The `Order` property only resolves the ambiguity, that template cannot be matched.</span></span> <span data-ttu-id="e7e54-463">Je lepší odebrat `[Route("Home")]` šablonu.</span><span class="sxs-lookup"><span data-stu-id="e7e54-463">It would be better to remove the `[Route("Home")]` template.</span></span>

<span data-ttu-id="e7e54-464">Podívejte se na [ Razor stránky směrování a konvence aplikace: pořadí tras](xref:razor-pages/razor-pages-conventions#route-order) pro informace o pořadí směrování se Razor stránkami.</span><span class="sxs-lookup"><span data-stu-id="e7e54-464">See [Razor Pages route and app conventions: Route order](xref:razor-pages/razor-pages-conventions#route-order) for information on route order with Razor Pages.</span></span>

<span data-ttu-id="e7e54-465">V některých případech se k chybě HTTP 500 vrátí nejednoznačné trasy.</span><span class="sxs-lookup"><span data-stu-id="e7e54-465">In some cases, an HTTP 500 error is returned with ambiguous routes.</span></span> <span data-ttu-id="e7e54-466">Pomocí [protokolování](xref:fundamentals/logging/index) zjistíte, které koncové body způsobily `AmbiguousMatchException` .</span><span class="sxs-lookup"><span data-stu-id="e7e54-466">Use [logging](xref:fundamentals/logging/index) to see which endpoints caused the `AmbiguousMatchException`.</span></span>

<a name="routing-token-replacement-templates-ref-label"></a>

## <a name="token-replacement-in-route-templates-controller-action-area"></a><span data-ttu-id="e7e54-467">Nahrazení tokenu v šablonách směrování [Controller], [akce], [oblast]</span><span class="sxs-lookup"><span data-stu-id="e7e54-467">Token replacement in route templates [controller], [action], [area]</span></span>

<span data-ttu-id="e7e54-468">Pro usnadnění práce, trasy atributů podporují nahrazení tokenů pro rezervované parametry tras uzavřením tokenu v jedné z následujících možností:</span><span class="sxs-lookup"><span data-stu-id="e7e54-468">For convenience, attribute routes support token replacement for reserved route parameters by enclosing a token in one of the following:</span></span>

* <span data-ttu-id="e7e54-469">Hranaté závorky:`[]`</span><span class="sxs-lookup"><span data-stu-id="e7e54-469">Square brackets: `[]`</span></span>
* <span data-ttu-id="e7e54-470">Složené závorky:`{}`</span><span class="sxs-lookup"><span data-stu-id="e7e54-470">Curly braces: `{}`</span></span>

<span data-ttu-id="e7e54-471">Tokeny `[action]` , `[area]` a `[controller]` jsou nahrazeny hodnotami názvu akce, názvu oblasti a názvu kontroleru z akce, kde je trasa definována:</span><span class="sxs-lookup"><span data-stu-id="e7e54-471">The tokens `[action]`, `[area]`, and `[controller]` are replaced with the values of the action name, area name, and controller name from the action where the route is defined:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet)]

<span data-ttu-id="e7e54-472">V předchozím kódu:</span><span class="sxs-lookup"><span data-stu-id="e7e54-472">In the preceding code:</span></span>

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet10)]

  * <span data-ttu-id="e7e54-473">Vyhovují`/Products0/List`</span><span class="sxs-lookup"><span data-stu-id="e7e54-473">Matches `/Products0/List`</span></span>

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet11)]

  * <span data-ttu-id="e7e54-474">Vyhovují`/Products0/Edit/{id}`</span><span class="sxs-lookup"><span data-stu-id="e7e54-474">Matches `/Products0/Edit/{id}`</span></span>

<span data-ttu-id="e7e54-475">Nahrazení tokenu probíhá jako poslední krok při vytváření tras atributů.</span><span class="sxs-lookup"><span data-stu-id="e7e54-475">Token replacement occurs as the last step of building the attribute routes.</span></span> <span data-ttu-id="e7e54-476">Předchozí příklad se chová stejně jako následující kód:</span><span class="sxs-lookup"><span data-stu-id="e7e54-476">The preceding example behaves the same as the following code:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet20)]

[!INCLUDE[](~/includes/MTcomments.md)]

<span data-ttu-id="e7e54-477">Trasy atributů je také možné kombinovat s dědičností.</span><span class="sxs-lookup"><span data-stu-id="e7e54-477">Attribute routes can also be combined with inheritance.</span></span> <span data-ttu-id="e7e54-478">Toto je výkonné v kombinaci s náhradou tokenu.</span><span class="sxs-lookup"><span data-stu-id="e7e54-478">This is powerful combined with token replacement.</span></span> <span data-ttu-id="e7e54-479">Nahrazení tokenu platí také pro názvy tras definované směrováními atributů.</span><span class="sxs-lookup"><span data-stu-id="e7e54-479">Token replacement also applies to route names defined by attribute routes.</span></span>
<span data-ttu-id="e7e54-480">`[Route("[controller]/[action]", Name="[controller]_[action]")]`vygeneruje jedinečný název trasy pro každou akci:</span><span class="sxs-lookup"><span data-stu-id="e7e54-480">`[Route("[controller]/[action]", Name="[controller]_[action]")]`generates a unique route name for each action:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet5)]

<span data-ttu-id="e7e54-481">Nahrazení tokenu platí také pro názvy tras definované směrováními atributů.</span><span class="sxs-lookup"><span data-stu-id="e7e54-481">Token replacement also applies to route names defined by attribute routes.</span></span>
`[Route("[controller]/[action]", Name="[controller]_[action]")]`
<span data-ttu-id="e7e54-482">vygeneruje jedinečný název trasy pro každou akci.</span><span class="sxs-lookup"><span data-stu-id="e7e54-482">generates a unique route name for each action.</span></span>

<span data-ttu-id="e7e54-483">Chcete-li spárovat oddělovač nahrazení tokenu literálu `[` nebo `]` , zastavte ho opakováním znaku ( `[[` nebo `]]` ).</span><span class="sxs-lookup"><span data-stu-id="e7e54-483">To match the literal token replacement delimiter `[` or  `]`, escape it by repeating the character (`[[` or `]]`).</span></span>

<a name="routing-token-replacement-transformers-ref-label"></a>

### <a name="use-a-parameter-transformer-to-customize-token-replacement"></a><span data-ttu-id="e7e54-484">Použití transformátoru parametrů k přizpůsobení náhrady tokenu</span><span class="sxs-lookup"><span data-stu-id="e7e54-484">Use a parameter transformer to customize token replacement</span></span>

<span data-ttu-id="e7e54-485">Nahrazení tokenu se dá přizpůsobit pomocí transformátoru parametrů.</span><span class="sxs-lookup"><span data-stu-id="e7e54-485">Token replacement can be customized using a parameter transformer.</span></span> <span data-ttu-id="e7e54-486">Parametr Transformer implementuje <xref:Microsoft.AspNetCore.Routing.IOutboundParameterTransformer> a transformuje hodnotu parametrů.</span><span class="sxs-lookup"><span data-stu-id="e7e54-486">A parameter transformer implements <xref:Microsoft.AspNetCore.Routing.IOutboundParameterTransformer> and transforms the value of parameters.</span></span> <span data-ttu-id="e7e54-487">Například vlastní `SlugifyParameterTransformer` parametr Transformer změní `SubscriptionManagement` hodnotu trasy na `subscription-management` :</span><span class="sxs-lookup"><span data-stu-id="e7e54-487">For example, a custom `SlugifyParameterTransformer` parameter transformer changes the `SubscriptionManagement` route value to `subscription-management`:</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupSlugifyParamTransformer.cs?name=snippet2)]

<span data-ttu-id="e7e54-488"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention>Je konvence aplikačního modelu, která:</span><span class="sxs-lookup"><span data-stu-id="e7e54-488">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention> is an application model convention that:</span></span>

* <span data-ttu-id="e7e54-489">Aplikuje transformátor parametrů na všechny trasy atributů v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="e7e54-489">Applies a parameter transformer to all attribute routes in an application.</span></span>
* <span data-ttu-id="e7e54-490">Přizpůsobí hodnoty tokenu trasy při jejich nahrazení.</span><span class="sxs-lookup"><span data-stu-id="e7e54-490">Customizes the attribute route token values as they are replaced.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/SubscriptionManagementController.cs?name=snippet)]

<span data-ttu-id="e7e54-491">Předchozí `ListAll` Metoda odpovídá `/subscription-management/list-all` .</span><span class="sxs-lookup"><span data-stu-id="e7e54-491">The preceding `ListAll` method matches `/subscription-management/list-all`.</span></span>

<span data-ttu-id="e7e54-492">`RouteTokenTransformerConvention`Je zaregistrován jako možnost v `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="e7e54-492">The `RouteTokenTransformerConvention` is registered as an option in `ConfigureServices`.</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupSlugifyParamTransformer.cs?name=snippet)]

<span data-ttu-id="e7e54-493">Definice popisu najdete v tématu [MDN web docs on](https://developer.mozilla.org/docs/Glossary/Slug) .</span><span class="sxs-lookup"><span data-stu-id="e7e54-493">See [MDN web docs on Slug](https://developer.mozilla.org/docs/Glossary/Slug) for the definition of Slug.</span></span>

[!INCLUDE[](~/includes/regex.md)]
<a name="routing-multiple-routes-ref-label"></a>

### <a name="multiple-attribute-routes"></a><span data-ttu-id="e7e54-494">Trasy s více atributy</span><span class="sxs-lookup"><span data-stu-id="e7e54-494">Multiple attribute routes</span></span>

<span data-ttu-id="e7e54-495">Směrování atributů podporuje definování více tras, které dosáhnou stejné akce.</span><span class="sxs-lookup"><span data-stu-id="e7e54-495">Attribute routing supports defining multiple routes that reach the same action.</span></span> <span data-ttu-id="e7e54-496">Nejběžnějším využitím je napodobení chování výchozí konvenční trasy, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="e7e54-496">The most common usage of this is to mimic the behavior of the default conventional route as shown in the following example:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet6x)]

<span data-ttu-id="e7e54-497">Vložení více atributů trasy na řadič znamená, že každý z nich kombinuje s každým atributem směrování na metodách akcí:</span><span class="sxs-lookup"><span data-stu-id="e7e54-497">Putting multiple route attributes on the controller means that each one combines with each of the route attributes on the action methods:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet6)]

<span data-ttu-id="e7e54-498">Všechna omezení trasy [příkazů http](#verb) implementují `IActionConstraint` .</span><span class="sxs-lookup"><span data-stu-id="e7e54-498">All the [HTTP verb](#verb) route constraints implement `IActionConstraint`.</span></span>

<span data-ttu-id="e7e54-499">Pokud <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint> je pro akci umístěno více atributů tras, které implementují:</span><span class="sxs-lookup"><span data-stu-id="e7e54-499">When multiple route attributes that implement <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint> are placed on an action:</span></span>

* <span data-ttu-id="e7e54-500">Každé omezení akce kombinuje se šablonou směrování použitou pro kontroler.</span><span class="sxs-lookup"><span data-stu-id="e7e54-500">Each action constraint combines with the route template applied to the controller.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet7)]

<span data-ttu-id="e7e54-501">Použití více tras na akcích může vypadat jako užitečné a výkonné, takže je lepší zachovat místo na adrese URL vaší aplikace základní a dobře definovaný.</span><span class="sxs-lookup"><span data-stu-id="e7e54-501">Using multiple routes on actions might seem useful and powerful, it's better to keep your app's URL space basic and well defined.</span></span> <span data-ttu-id="e7e54-502">V případě potřeby použijte více tras **na akcích** , například pro podporu stávajících klientů.</span><span class="sxs-lookup"><span data-stu-id="e7e54-502">Use multiple routes on actions **only** where needed, for example, to support existing clients.</span></span>

<a name="routing-attr-options"></a>

### <a name="specifying-attribute-route-optional-parameters-default-values-and-constraints"></a><span data-ttu-id="e7e54-503">Určení volitelných parametrů trasy atributu, výchozích hodnot a omezení</span><span class="sxs-lookup"><span data-stu-id="e7e54-503">Specifying attribute route optional parameters, default values, and constraints</span></span>

<span data-ttu-id="e7e54-504">Trasy atributů podporují stejnou vloženou syntaxi jako konvenční trasy, aby bylo možné zadat volitelné parametry, výchozí hodnoty a omezení.</span><span class="sxs-lookup"><span data-stu-id="e7e54-504">Attribute routes support the same inline syntax as conventional routes to specify optional parameters, default values, and constraints.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet8&highlight=3)]

<span data-ttu-id="e7e54-505">V předchozím kódu `[HttpPost("product/{id:int}")]` platí omezení trasy.</span><span class="sxs-lookup"><span data-stu-id="e7e54-505">In the preceding code, `[HttpPost("product/{id:int}")]` applies a route constraint.</span></span> <span data-ttu-id="e7e54-506">Tato `ProductsController.ShowProduct` akce odpovídá pouze cestou URL jako `/product/3` .</span><span class="sxs-lookup"><span data-stu-id="e7e54-506">The `ProductsController.ShowProduct` action is matched only by URL paths like `/product/3`.</span></span> <span data-ttu-id="e7e54-507">Část šablony trasy `{id:int}` omezuje segment na pouze celá čísla.</span><span class="sxs-lookup"><span data-stu-id="e7e54-507">The route template portion `{id:int}` constrains that segment to only integers.</span></span>

<span data-ttu-id="e7e54-508">Podrobný popis syntaxe šablony směrování naleznete v tématu Referenční dokumentace k [šabloně směrování](xref:fundamentals/routing#route-template-reference) .</span><span class="sxs-lookup"><span data-stu-id="e7e54-508">See [Route Template Reference](xref:fundamentals/routing#route-template-reference) for a detailed description of route template syntax.</span></span>

<a name="routing-cust-rt-attr-irt-ref-label"></a>

### <a name="custom-route-attributes-using-iroutetemplateprovider"></a><span data-ttu-id="e7e54-509">Vlastní atributy směrování pomocí IRouteTemplateProvider</span><span class="sxs-lookup"><span data-stu-id="e7e54-509">Custom route attributes using IRouteTemplateProvider</span></span>

<span data-ttu-id="e7e54-510">Všechny [atributy směrování](#rt) implementují <xref:Microsoft.AspNetCore.Mvc.Routing.IRouteTemplateProvider> .</span><span class="sxs-lookup"><span data-stu-id="e7e54-510">All of the [route attributes](#rt) implement <xref:Microsoft.AspNetCore.Mvc.Routing.IRouteTemplateProvider>.</span></span> <span data-ttu-id="e7e54-511">Modul runtime ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="e7e54-511">The ASP.NET Core runtime:</span></span>

* <span data-ttu-id="e7e54-512">Vyhledá atributy tříd kontroleru a metody akcí při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="e7e54-512">Looks for attributes on controller classes and action methods when the app starts.</span></span>
* <span data-ttu-id="e7e54-513">Používá atributy, které implementují `IRouteTemplateProvider` k sestavení počáteční sady tras.</span><span class="sxs-lookup"><span data-stu-id="e7e54-513">Uses the attributes that implement `IRouteTemplateProvider` to build the initial set of routes.</span></span>

<span data-ttu-id="e7e54-514">Implementujte `IRouteTemplateProvider` pro definování vlastních atributů směrování.</span><span class="sxs-lookup"><span data-stu-id="e7e54-514">Implement `IRouteTemplateProvider` to define custom route attributes.</span></span> <span data-ttu-id="e7e54-515">Každá `IRouteTemplateProvider` z nich umožňuje definovat jednu trasu s vlastní šablonou směrování, objednávkou a názvem:</span><span class="sxs-lookup"><span data-stu-id="e7e54-515">Each `IRouteTemplateProvider` allows you to define a single route with a custom route template, order, and name:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyTestApiController.cs?name=snippet&highlight=1-10)]

<span data-ttu-id="e7e54-516">Předchozí `Get` Metoda vrátí `Order = 2, Template = api/MyTestApi` .</span><span class="sxs-lookup"><span data-stu-id="e7e54-516">The preceding `Get` method returns `Order = 2, Template = api/MyTestApi`.</span></span>

<a name="routing-app-model-ref-label"></a>

### <a name="use-application-model-to-customize-attribute-routes"></a><span data-ttu-id="e7e54-517">Použití aplikačního modelu k přizpůsobení tras atributů</span><span class="sxs-lookup"><span data-stu-id="e7e54-517">Use application model to customize attribute routes</span></span>

<span data-ttu-id="e7e54-518">Aplikační model:</span><span class="sxs-lookup"><span data-stu-id="e7e54-518">The application model:</span></span>

* <span data-ttu-id="e7e54-519">Je objektovým modelem vytvořeným při spuštění.</span><span class="sxs-lookup"><span data-stu-id="e7e54-519">Is an object model created at startup.</span></span>
* <span data-ttu-id="e7e54-520">Obsahuje všechna metadata používaná ASP.NET Core ke směrování a provádění akcí v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="e7e54-520">Contains all of the metadata used by ASP.NET Core to route and execute the actions in an app.</span></span>

<span data-ttu-id="e7e54-521">Aplikační model zahrnuje všechna data získaná z atributů směrování.</span><span class="sxs-lookup"><span data-stu-id="e7e54-521">The application model includes all of the data gathered from route attributes.</span></span> <span data-ttu-id="e7e54-522">Data z atributů trasy jsou k dispozici v `IRouteTemplateProvider` implementaci.</span><span class="sxs-lookup"><span data-stu-id="e7e54-522">The data from route attributes is provided by the `IRouteTemplateProvider` implementation.</span></span> <span data-ttu-id="e7e54-523">Úmluvy</span><span class="sxs-lookup"><span data-stu-id="e7e54-523">Conventions:</span></span>

* <span data-ttu-id="e7e54-524">Lze zapsat pro úpravu modelu aplikace, aby bylo možné přizpůsobit způsob, jakým se směrování chová.</span><span class="sxs-lookup"><span data-stu-id="e7e54-524">Can be written to modify the application model to customize how routing behaves.</span></span>
* <span data-ttu-id="e7e54-525">Jsou čteny při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="e7e54-525">Are read at app startup.</span></span>

<span data-ttu-id="e7e54-526">V této části najdete základní příklad přizpůsobení směrování pomocí aplikačního modelu.</span><span class="sxs-lookup"><span data-stu-id="e7e54-526">This section shows a basic example of customizing routing using application model.</span></span> <span data-ttu-id="e7e54-527">Následující kód vytvoří trasy přibližně v souladu se strukturou složek v projektu.</span><span class="sxs-lookup"><span data-stu-id="e7e54-527">The following code makes routes roughly line up with the folder structure of the project.</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/NamespaceRoutingConvention.cs?name=snippet)]

<span data-ttu-id="e7e54-528">Následující kód brání `namespace` použití úmluvy na řadičích, které jsou směrovány atributem:</span><span class="sxs-lookup"><span data-stu-id="e7e54-528">The following code prevents the `namespace` convention from being applied to controllers that are attribute routed:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/NamespaceRoutingConvention.cs?name=snippet2)]

<span data-ttu-id="e7e54-529">Například následující kontroler nepoužívá `NamespaceRoutingConvention` :</span><span class="sxs-lookup"><span data-stu-id="e7e54-529">For example, the following controller doesn't use `NamespaceRoutingConvention`:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/ManagersController.cs?name=snippet&highlight=1)]

<span data-ttu-id="e7e54-530">`NamespaceRoutingConvention.Apply`Metoda:</span><span class="sxs-lookup"><span data-stu-id="e7e54-530">The `NamespaceRoutingConvention.Apply` method:</span></span>

* <span data-ttu-id="e7e54-531">Neprovede žádnou akci, pokud je řadič směrován do atributu.</span><span class="sxs-lookup"><span data-stu-id="e7e54-531">Does nothing if the controller is attribute routed.</span></span>
* <span data-ttu-id="e7e54-532">Nastaví šablonu řadičů založenou na `namespace` , se základním `namespace` odebráním.</span><span class="sxs-lookup"><span data-stu-id="e7e54-532">Sets the controllers template based on the `namespace`, with the base `namespace` removed.</span></span>

<span data-ttu-id="e7e54-533">`NamespaceRoutingConvention`Lze použít v `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="e7e54-533">The `NamespaceRoutingConvention` can be applied in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/Startup.cs?name=snippet&highlight=1,14-18)]

<span data-ttu-id="e7e54-534">Zvažte například následující kontroler:</span><span class="sxs-lookup"><span data-stu-id="e7e54-534">For example, consider the following controller:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/UsersController.cs)]

<span data-ttu-id="e7e54-535">V předchozím kódu:</span><span class="sxs-lookup"><span data-stu-id="e7e54-535">In the preceding code:</span></span>

* <span data-ttu-id="e7e54-536">Základem `namespace` je `My.Application` .</span><span class="sxs-lookup"><span data-stu-id="e7e54-536">The base `namespace` is `My.Application`.</span></span>
* <span data-ttu-id="e7e54-537">Úplný název předchozího kontroleru je `My.Application.Admin.Controllers.UsersController` .</span><span class="sxs-lookup"><span data-stu-id="e7e54-537">The full name of the preceding controller is `My.Application.Admin.Controllers.UsersController`.</span></span>
* <span data-ttu-id="e7e54-538">`NamespaceRoutingConvention`Nastaví šablonu Controllers na `Admin/Controllers/Users/[action]/{id?` .</span><span class="sxs-lookup"><span data-stu-id="e7e54-538">The `NamespaceRoutingConvention` sets the controllers template to `Admin/Controllers/Users/[action]/{id?`.</span></span>

<span data-ttu-id="e7e54-539">`NamespaceRoutingConvention`Lze také použít jako atribut na řadiči:</span><span class="sxs-lookup"><span data-stu-id="e7e54-539">The `NamespaceRoutingConvention` can also be applied as an attribute on a controller:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/TestController.cs?name=snippet&highlight=1)]

<a name="routing-mixed-ref-label"></a>

## <a name="mixed-routing-attribute-routing-vs-conventional-routing"></a><span data-ttu-id="e7e54-540">Kombinované směrování: směrování atributů vs. konvenční směrování</span><span class="sxs-lookup"><span data-stu-id="e7e54-540">Mixed routing: Attribute routing vs conventional routing</span></span>

<span data-ttu-id="e7e54-541">Aplikace ASP.NET Core můžou kombinovat použití konvenčního směrování a směrování atributů.</span><span class="sxs-lookup"><span data-stu-id="e7e54-541">ASP.NET Core apps can mix the use of conventional routing and attribute routing.</span></span> <span data-ttu-id="e7e54-542">Pro řadiče, které obsluhují stránky HTML pro prohlížeče, a směrování atributů pro řadiče obsluhující rozhraní REST API se obvykle používá konvenční trasy.</span><span class="sxs-lookup"><span data-stu-id="e7e54-542">It's typical to use conventional routes for controllers serving HTML pages for browsers, and attribute routing for controllers serving REST APIs.</span></span>

<span data-ttu-id="e7e54-543">Akce jsou směrovány buď podle konvence nebo směrovaného atributu.</span><span class="sxs-lookup"><span data-stu-id="e7e54-543">Actions are either conventionally routed or attribute routed.</span></span> <span data-ttu-id="e7e54-544">Umístěním trasy do kontroleru nebo akce se nasměruje atribut IT.</span><span class="sxs-lookup"><span data-stu-id="e7e54-544">Placing a route on the controller or the action makes it attribute routed.</span></span> <span data-ttu-id="e7e54-545">Akce definující trasy atributů nemůžou být dostupné prostřednictvím konvenčních tras a naopak.</span><span class="sxs-lookup"><span data-stu-id="e7e54-545">Actions that define attribute routes cannot be reached through the conventional routes and vice-versa.</span></span> <span data-ttu-id="e7e54-546">**Libovolný** atribut směrování v řadiči provádí směrování **všech** akcí v atributu kontroleru.</span><span class="sxs-lookup"><span data-stu-id="e7e54-546">**Any** route attribute on the controller makes **all** actions in the controller attribute routed.</span></span>

<span data-ttu-id="e7e54-547">Směrování atributů a konvenční směrování používají stejný modul směrování.</span><span class="sxs-lookup"><span data-stu-id="e7e54-547">Attribute routing and conventional routing use the same routing engine.</span></span>

<a name="routing-url-gen-ref-label"></a>
<a name="ambient"></a>

## <a name="url-generation-and-ambient-values"></a><span data-ttu-id="e7e54-548">Generování adresy URL a okolní hodnoty</span><span class="sxs-lookup"><span data-stu-id="e7e54-548">URL Generation and ambient values</span></span>

<span data-ttu-id="e7e54-549">Aplikace můžou použít funkce generování adresy URL směrování k vygenerování odkazů na akce v adresách URL.</span><span class="sxs-lookup"><span data-stu-id="e7e54-549">Apps can use routing URL generation features to generate URL links to actions.</span></span> <span data-ttu-id="e7e54-550">Generování adres URL eliminuje adresy URL zakódujeme, takže je kód robustnější a udržovatelný.</span><span class="sxs-lookup"><span data-stu-id="e7e54-550">Generating URLs eliminates hardcoding URLs, making code more robust and maintainable.</span></span> <span data-ttu-id="e7e54-551">Tato část se zaměřuje na funkce generování adresy URL poskytované MVC a jenom základní informace o tom, jak generování adresy URL funguje.</span><span class="sxs-lookup"><span data-stu-id="e7e54-551">This section focuses on the URL generation features provided by MVC and only cover basics of how URL generation works.</span></span> <span data-ttu-id="e7e54-552">Podrobný popis generování adresy URL najdete v tématu věnovaném [Směrování](xref:fundamentals/routing) .</span><span class="sxs-lookup"><span data-stu-id="e7e54-552">See [Routing](xref:fundamentals/routing) for a detailed description of URL generation.</span></span>

<span data-ttu-id="e7e54-553"><xref:Microsoft.AspNetCore.Mvc.IUrlHelper>Rozhraní je základní prvek infrastruktury mezi MVC a směrováním pro generování adresy URL.</span><span class="sxs-lookup"><span data-stu-id="e7e54-553">The <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> interface is the underlying element of infrastructure between MVC and routing for URL generation.</span></span> <span data-ttu-id="e7e54-554">Instance `IUrlHelper` je k dispozici prostřednictvím `Url` vlastnosti v řadičích, zobrazeních a zobrazení komponent.</span><span class="sxs-lookup"><span data-stu-id="e7e54-554">An instance of `IUrlHelper` is available through the `Url` property in controllers, views, and view components.</span></span>

<span data-ttu-id="e7e54-555">V následujícím příkladu se `IUrlHelper` rozhraní používá přes `Controller.Url` vlastnost k VYGENEROVÁNÍ adresy URL jiné akci.</span><span class="sxs-lookup"><span data-stu-id="e7e54-555">In the following example, the `IUrlHelper` interface is used through the `Controller.Url` property to generate a URL to another action.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGenerationController.cs?name=snippet_1)]

<span data-ttu-id="e7e54-556">Pokud aplikace používá výchozí konvenční trasu, hodnota `url` proměnné je řetězec cesty adresy URL `/UrlGeneration/Destination` .</span><span class="sxs-lookup"><span data-stu-id="e7e54-556">If the app is using the default conventional route, the value of the `url` variable is the URL path string `/UrlGeneration/Destination`.</span></span> <span data-ttu-id="e7e54-557">Tato cesta URL se vytvoří směrováním kombinováním:</span><span class="sxs-lookup"><span data-stu-id="e7e54-557">This URL path is created by routing by combining:</span></span>

* <span data-ttu-id="e7e54-558">Hodnoty tras z aktuální žádosti, které se nazývají **okolní hodnoty**.</span><span class="sxs-lookup"><span data-stu-id="e7e54-558">The route values from the current request, which are called **ambient values**.</span></span>
* <span data-ttu-id="e7e54-559">Hodnoty předané do `Url.Action` a nahrazování těchto hodnot do šablony trasy:</span><span class="sxs-lookup"><span data-stu-id="e7e54-559">The values passed to `Url.Action` and substituting those values into the route template:</span></span>

``` text
ambient values: { controller = "UrlGeneration", action = "Source" }
values passed to Url.Action: { controller = "UrlGeneration", action = "Destination" }
route template: {controller}/{action}/{id?}

result: /UrlGeneration/Destination
```

<span data-ttu-id="e7e54-560">Každý parametr trasy v šabloně směrování má jeho hodnotu nahrazenou odpovídajícími názvy hodnotami a okolními hodnotami.</span><span class="sxs-lookup"><span data-stu-id="e7e54-560">Each route parameter in the route template has its value substituted by matching names with the values and ambient values.</span></span> <span data-ttu-id="e7e54-561">Parametr trasy, který nemá hodnotu, může:</span><span class="sxs-lookup"><span data-stu-id="e7e54-561">A route parameter that doesn't have a value can:</span></span>

* <span data-ttu-id="e7e54-562">Pokud má jednu z těchto hodnot, použijte výchozí hodnotu.</span><span class="sxs-lookup"><span data-stu-id="e7e54-562">Use a default value if it has one.</span></span>
* <span data-ttu-id="e7e54-563">Přeskočeno, pokud je volitelné.</span><span class="sxs-lookup"><span data-stu-id="e7e54-563">Be skipped if it's optional.</span></span> <span data-ttu-id="e7e54-564">Například `id` ze šablony trasy `{controller}/{action}/{id?}` .</span><span class="sxs-lookup"><span data-stu-id="e7e54-564">For example, the `id` from the  route template `{controller}/{action}/{id?}`.</span></span>

<span data-ttu-id="e7e54-565">Generování adresy URL se nepovede, pokud libovolný požadovaný parametr trasy nemá odpovídající hodnotu.</span><span class="sxs-lookup"><span data-stu-id="e7e54-565">URL generation fails if any required route parameter doesn't have a corresponding value.</span></span> <span data-ttu-id="e7e54-566">Pokud se generování adresy URL u trasy nezdařilo, bude se zkoušet další trasa, dokud nebudou všechny trasy vyzkoušeny nebo nalezena shoda.</span><span class="sxs-lookup"><span data-stu-id="e7e54-566">If URL generation fails for a route, the next route is tried until all routes have been tried or a match is found.</span></span>

<span data-ttu-id="e7e54-567">Předchozí příklad `Url.Action` předpokládá [konvenční směrování](#cr).</span><span class="sxs-lookup"><span data-stu-id="e7e54-567">The preceding example of `Url.Action` assumes [conventional routing](#cr).</span></span> <span data-ttu-id="e7e54-568">Generování adresy URL funguje podobně jako [Směrování atributů](#ar), i když se koncepty liší.</span><span class="sxs-lookup"><span data-stu-id="e7e54-568">URL generation works similarly with [attribute routing](#ar), though the concepts are different.</span></span> <span data-ttu-id="e7e54-569">S konvenčním směrováním:</span><span class="sxs-lookup"><span data-stu-id="e7e54-569">With conventional routing:</span></span>

* <span data-ttu-id="e7e54-570">Hodnoty tras slouží k rozbalení šablony.</span><span class="sxs-lookup"><span data-stu-id="e7e54-570">The route values are used to expand a template.</span></span>
* <span data-ttu-id="e7e54-571">Hodnoty směrování pro `controller` a `action` obvykle se zobrazí v této šabloně.</span><span class="sxs-lookup"><span data-stu-id="e7e54-571">The route values for `controller` and `action` usually appear in that template.</span></span> <span data-ttu-id="e7e54-572">To se hodí, protože adresy URL, které odpovídají směrování, dodržují konvenci.</span><span class="sxs-lookup"><span data-stu-id="e7e54-572">This works because the URLs matched by routing adhere to a convention.</span></span>

<span data-ttu-id="e7e54-573">Následující příklad používá směrování atributů:</span><span class="sxs-lookup"><span data-stu-id="e7e54-573">The following example uses attribute routing:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGenerationAttrController.cs?name=snippet_1)]

<span data-ttu-id="e7e54-574">`Source`Akce v předchozím kódu generuje `custom/url/to/destination` .</span><span class="sxs-lookup"><span data-stu-id="e7e54-574">The `Source` action in the preceding code generates `custom/url/to/destination`.</span></span>

<span data-ttu-id="e7e54-575"><xref:Microsoft.AspNetCore.Routing.LinkGenerator>byl přidán v ASP.NET Core 3,0 jako alternativa k `IUrlHelper` .</span><span class="sxs-lookup"><span data-stu-id="e7e54-575"><xref:Microsoft.AspNetCore.Routing.LinkGenerator> was added in ASP.NET Core 3.0 as an alternative to `IUrlHelper`.</span></span> <span data-ttu-id="e7e54-576">`LinkGenerator`nabízí podobné, ale flexibilnější funkce.</span><span class="sxs-lookup"><span data-stu-id="e7e54-576">`LinkGenerator` offers similar but more flexible functionality.</span></span> <span data-ttu-id="e7e54-577">Každá metoda on `IUrlHelper` má také odpovídající rodinu metod `LinkGenerator` .</span><span class="sxs-lookup"><span data-stu-id="e7e54-577">Each method on `IUrlHelper` has a corresponding family of methods on `LinkGenerator` as well.</span></span>

### <a name="generating-urls-by-action-name"></a><span data-ttu-id="e7e54-578">Generování adres URL podle názvu akce</span><span class="sxs-lookup"><span data-stu-id="e7e54-578">Generating URLs by action name</span></span>

<span data-ttu-id="e7e54-579">[Adresa URL. Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*), [LinkGenerator. GetPathByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetPathByAction*)a všechny související přetížení jsou navržené tak, aby vygenerovaly cílový koncový bod zadáním názvu kontroleru a názvu akce.</span><span class="sxs-lookup"><span data-stu-id="e7e54-579">[Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*), [LinkGenerator.GetPathByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetPathByAction*), and all related overloads all are designed to generate the target endpoint by specifying a controller name and action name.</span></span>

<span data-ttu-id="e7e54-580">Při použití nástroje `Url.Action` jsou aktuální hodnoty trasy pro `controller` a `action` poskytovány modulem runtime:</span><span class="sxs-lookup"><span data-stu-id="e7e54-580">When using `Url.Action`, the current route values for `controller` and `action` are provided by the runtime:</span></span>

* <span data-ttu-id="e7e54-581">Hodnota `controller` a `action` je součástí obou [okolních hodnot](#ambient) a hodnot.</span><span class="sxs-lookup"><span data-stu-id="e7e54-581">The value of `controller` and `action` are part of both [ambient values](#ambient) and values.</span></span> <span data-ttu-id="e7e54-582">Metoda `Url.Action` vždy používá aktuální hodnoty `action` a `controller` a generuje cestu adresy URL, která směruje na aktuální akci.</span><span class="sxs-lookup"><span data-stu-id="e7e54-582">The method `Url.Action` always uses the current values of `action` and `controller` and generates a URL path that routes to the current action.</span></span>

<span data-ttu-id="e7e54-583">Směrování se pokusí použít hodnoty v ambientních hodnotách k vyplnění informací, které nebyly poskytnuty při generování adresy URL.</span><span class="sxs-lookup"><span data-stu-id="e7e54-583">Routing attempts to use the values in ambient values to fill in information that wasn't provided when generating a URL.</span></span> <span data-ttu-id="e7e54-584">Vezměte v úvahu trasu jako `{a}/{b}/{c}/{d}` u okolních hodnot `{ a = Alice, b = Bob, c = Carol, d = David }` :</span><span class="sxs-lookup"><span data-stu-id="e7e54-584">Consider a route like `{a}/{b}/{c}/{d}` with ambient values `{ a = Alice, b = Bob, c = Carol, d = David }`:</span></span>

* <span data-ttu-id="e7e54-585">Směrování má dostatek informací pro vygenerování adresy URL bez dalších hodnot.</span><span class="sxs-lookup"><span data-stu-id="e7e54-585">Routing has enough information to generate a URL without any additional values.</span></span>
* <span data-ttu-id="e7e54-586">Směrování má dostatek informací, protože všechny parametry tras mají hodnotu.</span><span class="sxs-lookup"><span data-stu-id="e7e54-586">Routing has enough information because all route parameters have a value.</span></span>

<span data-ttu-id="e7e54-587">Pokud `{ d = Donovan }` se přidá hodnota:</span><span class="sxs-lookup"><span data-stu-id="e7e54-587">If the value `{ d = Donovan }` is added:</span></span>

* <span data-ttu-id="e7e54-588">Hodnota `{ d = David }` se ignoruje.</span><span class="sxs-lookup"><span data-stu-id="e7e54-588">The value `{ d = David }` is ignored.</span></span>
* <span data-ttu-id="e7e54-589">Vygenerovaná cesta URL je `Alice/Bob/Carol/Donovan` .</span><span class="sxs-lookup"><span data-stu-id="e7e54-589">The generated URL path is `Alice/Bob/Carol/Donovan`.</span></span>

<span data-ttu-id="e7e54-590">**Upozornění**: cesty URL jsou hierarchické.</span><span class="sxs-lookup"><span data-stu-id="e7e54-590">**Warning**: URL paths are hierarchical.</span></span> <span data-ttu-id="e7e54-591">V předchozím příkladu, pokud `{ c = Cheryl }` je přidána hodnota:</span><span class="sxs-lookup"><span data-stu-id="e7e54-591">In the preceding example, if the value `{ c = Cheryl }` is added:</span></span>

* <span data-ttu-id="e7e54-592">Obě hodnoty `{ c = Carol, d = David }` jsou ignorovány.</span><span class="sxs-lookup"><span data-stu-id="e7e54-592">Both of the values `{ c = Carol, d = David }` are ignored.</span></span>
* <span data-ttu-id="e7e54-593">Již není hodnota `d` a generování adresy URL se nezdařilo.</span><span class="sxs-lookup"><span data-stu-id="e7e54-593">There is no longer a value for `d` and URL generation fails.</span></span>
* <span data-ttu-id="e7e54-594">`c` `d` Aby bylo možné vygenerovat adresu URL, je nutné zadat požadované hodnoty a.</span><span class="sxs-lookup"><span data-stu-id="e7e54-594">The desired values of `c` and `d` must be specified to generate a URL.</span></span>  

<span data-ttu-id="e7e54-595">Můžete očekávat, že se tento problém bude narazit u výchozí trasy `{controller}/{action}/{id?}` .</span><span class="sxs-lookup"><span data-stu-id="e7e54-595">You might expect to hit this problem with the default route `{controller}/{action}/{id?}`.</span></span> <span data-ttu-id="e7e54-596">Tento problém je v praxi zřídka, protože `Url.Action` vždy explicitně určuje `controller` `action` hodnotu a.</span><span class="sxs-lookup"><span data-stu-id="e7e54-596">This problem is rare in practice because `Url.Action` always explicitly specifies a `controller` and `action` value.</span></span>

<span data-ttu-id="e7e54-597">Několik přetížení [URL. akce](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) přebírají objekt hodnot směrování, aby poskytovala hodnoty pro parametry směrování jiné než `controller` a `action` .</span><span class="sxs-lookup"><span data-stu-id="e7e54-597">Several overloads of [Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) take a route values object to provide values for route parameters other than `controller` and `action`.</span></span> <span data-ttu-id="e7e54-598">Objekt hodnoty trasy se často používá s `id` .</span><span class="sxs-lookup"><span data-stu-id="e7e54-598">The route values object is frequently used with `id`.</span></span> <span data-ttu-id="e7e54-599">Například, `Url.Action("Buy", "Products", new { id = 17 })`.</span><span class="sxs-lookup"><span data-stu-id="e7e54-599">For example, `Url.Action("Buy", "Products", new { id = 17 })`.</span></span> <span data-ttu-id="e7e54-600">Objekt hodnot směrování:</span><span class="sxs-lookup"><span data-stu-id="e7e54-600">The route values object:</span></span>

* <span data-ttu-id="e7e54-601">Podle konvence je obvykle objekt anonymního typu.</span><span class="sxs-lookup"><span data-stu-id="e7e54-601">By convention is usually an object of anonymous type.</span></span>
* <span data-ttu-id="e7e54-602">Může to být `IDictionary<>` nebo [POCO](https://wikipedia.org/wiki/Plain_old_CLR_object)).</span><span class="sxs-lookup"><span data-stu-id="e7e54-602">Can be an `IDictionary<>` or a [POCO](https://wikipedia.org/wiki/Plain_old_CLR_object)).</span></span>

<span data-ttu-id="e7e54-603">Všechny další hodnoty tras, které neodpovídají parametrům směrování, jsou vloženy do řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="e7e54-603">Any additional route values that don't match route parameters are put in the query string.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/TestController.cs?name=snippet)]

<span data-ttu-id="e7e54-604">Předchozí kód generuje `/Products/Buy/17?color=red` .</span><span class="sxs-lookup"><span data-stu-id="e7e54-604">The preceding code generates `/Products/Buy/17?color=red`.</span></span>

<span data-ttu-id="e7e54-605">Následující kód vygeneruje absolutní adresu URL:</span><span class="sxs-lookup"><span data-stu-id="e7e54-605">The following code generates an absolute URL:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/TestController.cs?name=snippet2)]

<span data-ttu-id="e7e54-606">Chcete-li vytvořit absolutní adresu URL, použijte jednu z následujících možností:</span><span class="sxs-lookup"><span data-stu-id="e7e54-606">To create an absolute URL, use one of the following:</span></span>

* <span data-ttu-id="e7e54-607">Přetížení, které přijímá `protocol` .</span><span class="sxs-lookup"><span data-stu-id="e7e54-607">An overload that accepts a `protocol`.</span></span> <span data-ttu-id="e7e54-608">Například předchozí kód.</span><span class="sxs-lookup"><span data-stu-id="e7e54-608">For example, the preceding code.</span></span>
* <span data-ttu-id="e7e54-609">[LinkGenerator. GetUriByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetUriByAction*), který ve výchozím nastavení generuje absolutní identifikátory URI.</span><span class="sxs-lookup"><span data-stu-id="e7e54-609">[LinkGenerator.GetUriByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetUriByAction*), which generates absolute URIs by default.</span></span>

<a name="routing-gen-urls-route-ref-label"></a>

### <a name="generate-urls-by-route"></a><span data-ttu-id="e7e54-610">Generovat adresy URL podle směrování</span><span class="sxs-lookup"><span data-stu-id="e7e54-610">Generate URLs by route</span></span>

<span data-ttu-id="e7e54-611">Předchozí kód ukázal generování adresy URL předáním do kontroleru a názvu akce.</span><span class="sxs-lookup"><span data-stu-id="e7e54-611">The preceding code demonstrated generating a URL by passing in the controller and action name.</span></span> <span data-ttu-id="e7e54-612">`IUrlHelper`také poskytuje [adresu URL. RouteUrl](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.RouteUrl*) řady metod.</span><span class="sxs-lookup"><span data-stu-id="e7e54-612">`IUrlHelper` also provides the [Url.RouteUrl](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.RouteUrl*) family of methods.</span></span> <span data-ttu-id="e7e54-613">Tyto metody jsou podobné jako [URL. Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*), ale nekopírují aktuální hodnoty `action` a `controller` do hodnot tras.</span><span class="sxs-lookup"><span data-stu-id="e7e54-613">These methods are similar to [Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*), but they don't copy the current values of `action` and `controller` to the route values.</span></span> <span data-ttu-id="e7e54-614">Nejběžnější využití `Url.RouteUrl` :</span><span class="sxs-lookup"><span data-stu-id="e7e54-614">The most common usage of `Url.RouteUrl`:</span></span>

* <span data-ttu-id="e7e54-615">Určuje název trasy pro vygenerování adresy URL.</span><span class="sxs-lookup"><span data-stu-id="e7e54-615">Specifies a route name to generate the URL.</span></span>
* <span data-ttu-id="e7e54-616">Obecně neurčuje kontrolér nebo název akce.</span><span class="sxs-lookup"><span data-stu-id="e7e54-616">Generally doesn't specify a controller or action name.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGeneration2Controller.cs?name=snippet_1)]

<span data-ttu-id="e7e54-617">Následující Razor soubor generuje odkaz HTML na `Destination_Route` :</span><span class="sxs-lookup"><span data-stu-id="e7e54-617">The following Razor file generates an HTML link to the `Destination_Route`:</span></span>

[!code-cshtml[](routing/samples/3.x/main/Views/Shared/MyLink.cshtml)]

<a name="routing-gen-urls-html-ref-label"></a>

### <a name="generate-urls-in-html-and-razor"></a><span data-ttu-id="e7e54-618">Generování adres URL ve formátu HTML aRazor</span><span class="sxs-lookup"><span data-stu-id="e7e54-618">Generate URLs in HTML and Razor</span></span>

<span data-ttu-id="e7e54-619"><xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper>poskytuje <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.HtmlHelper> metody [HTML. BeginForm](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.BeginForm*) a [HTML. ActionLink](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.ActionLink*) ke generování `<form>` a `<a>` prvkům v tomto pořadí.</span><span class="sxs-lookup"><span data-stu-id="e7e54-619"><xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper> provides the <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.HtmlHelper> methods [Html.BeginForm](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.BeginForm*) and [Html.ActionLink](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.ActionLink*) to generate `<form>` and `<a>` elements respectively.</span></span> <span data-ttu-id="e7e54-620">Tyto metody používají metodu [URL. Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) pro VYGENEROVÁNÍ adresy URL a přijímající podobné argumenty.</span><span class="sxs-lookup"><span data-stu-id="e7e54-620">These methods use the [Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) method to generate a URL and they accept similar arguments.</span></span> <span data-ttu-id="e7e54-621">`Url.RouteUrl`Doprovodníci pro `HtmlHelper` jsou `Html.BeginRouteForm` a `Html.RouteLink` mají podobné funkce.</span><span class="sxs-lookup"><span data-stu-id="e7e54-621">The `Url.RouteUrl` companions for `HtmlHelper` are `Html.BeginRouteForm` and `Html.RouteLink` which have similar functionality.</span></span>

<span data-ttu-id="e7e54-622">TagHelpers generuje adresy URL prostřednictvím `form` taghelperu a `<a>` taghelperu.</span><span class="sxs-lookup"><span data-stu-id="e7e54-622">TagHelpers generate URLs through the `form` TagHelper and the `<a>` TagHelper.</span></span> <span data-ttu-id="e7e54-623">Oba tyto použití `IUrlHelper` při jejich implementaci.</span><span class="sxs-lookup"><span data-stu-id="e7e54-623">Both of these use `IUrlHelper` for their implementation.</span></span> <span data-ttu-id="e7e54-624">Další informace najdete v tématu věnovaném [pomocníkům značek ve formulářích](xref:mvc/views/working-with-forms) .</span><span class="sxs-lookup"><span data-stu-id="e7e54-624">See [Tag Helpers in forms](xref:mvc/views/working-with-forms) for more information.</span></span>

<span data-ttu-id="e7e54-625">Uvnitř zobrazení `IUrlHelper` je k dispozici prostřednictvím `Url` vlastnosti pro jakoukoli generaci adres URL ad hoc, která není pokrytá výše.</span><span class="sxs-lookup"><span data-stu-id="e7e54-625">Inside views, the `IUrlHelper` is available through the `Url` property for any ad-hoc URL generation not covered by the above.</span></span>

<a name="routing-gen-urls-action-ref-label"></a>

### <a name="url-generation-in-action-results"></a><span data-ttu-id="e7e54-626">Generování adresy URL ve výsledcích akce</span><span class="sxs-lookup"><span data-stu-id="e7e54-626">URL generation in Action Results</span></span>

<span data-ttu-id="e7e54-627">Předchozí příklady ukázaly použití `IUrlHelper` v kontroleru.</span><span class="sxs-lookup"><span data-stu-id="e7e54-627">The preceding examples showed using `IUrlHelper` in a controller.</span></span> <span data-ttu-id="e7e54-628">Nejběžnějším využitím v kontroleru je vygenerování adresy URL jako součást výsledku akce.</span><span class="sxs-lookup"><span data-stu-id="e7e54-628">The most common usage in a controller is to generate a URL as part of an action result.</span></span>

<span data-ttu-id="e7e54-629"><xref:Microsoft.AspNetCore.Mvc.ControllerBase> <xref:Microsoft.AspNetCore.Mvc.Controller> Základní třídy a poskytují pohodlný způsob pro výsledky akcí, které odkazují na jinou akci.</span><span class="sxs-lookup"><span data-stu-id="e7e54-629">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase> and <xref:Microsoft.AspNetCore.Mvc.Controller> base classes provide convenience methods for action results that reference another action.</span></span> <span data-ttu-id="e7e54-630">Jedním z typických použití je přesměrování po přijetí vstupu uživatele:</span><span class="sxs-lookup"><span data-stu-id="e7e54-630">One typical usage is to redirect after accepting user input:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/CustomerController.cs?name=snippet)]

<span data-ttu-id="e7e54-631">Akce pro metody vytváření výsledků, jako je například <xref:Microsoft.AspNetCore.Mvc.ControllerBase.RedirectToAction*> a <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> následují podobně jako metody v `IUrlHelper` .</span><span class="sxs-lookup"><span data-stu-id="e7e54-631">The action results factory methods such as <xref:Microsoft.AspNetCore.Mvc.ControllerBase.RedirectToAction*> and <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> follow a similar pattern to the methods on `IUrlHelper`.</span></span>

<a name="routing-dedicated-ref-label"></a>

### <a name="special-case-for-dedicated-conventional-routes"></a><span data-ttu-id="e7e54-632">Zvláštní případ pro vyhrazené konvenční trasy</span><span class="sxs-lookup"><span data-stu-id="e7e54-632">Special case for dedicated conventional routes</span></span>

<span data-ttu-id="e7e54-633">[Konvenční směrování](#cr) může používat speciální druh definice trasy, která se označuje jako [vyhrazená konvenční trasa](#dcr).</span><span class="sxs-lookup"><span data-stu-id="e7e54-633">[Conventional routing](#cr) can use a special kind of route definition called a [dedicated conventional route](#dcr).</span></span> <span data-ttu-id="e7e54-634">V následujícím příkladu je trasa s názvem `blog` vyhrazená konvenční trasa:</span><span class="sxs-lookup"><span data-stu-id="e7e54-634">In the following example, the route named `blog` is a dedicated conventional route:</span></span>

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

<span data-ttu-id="e7e54-635">Když použijete předchozí definice tras, `Url.Action("Index", "Home")` vygeneruje cestu URL `/` pomocí `default` trasy, ale proč?</span><span class="sxs-lookup"><span data-stu-id="e7e54-635">Using the preceding route definitions, `Url.Action("Index", "Home")` generates the URL path `/` using the `default` route, but why?</span></span> <span data-ttu-id="e7e54-636">Můžete odhadnout, že hodnoty tras `{ controller = Home, action = Index }` by byly dostatečné pro vygenerování adresy URL pomocí `blog` a výsledek by byl `/blog?action=Index&controller=Home` .</span><span class="sxs-lookup"><span data-stu-id="e7e54-636">You might guess the route values `{ controller = Home, action = Index }` would be enough to generate a URL using `blog`, and the result would be `/blog?action=Index&controller=Home`.</span></span>

<span data-ttu-id="e7e54-637">[Vyhrazené konvenční trasy](#dcr) spoléhají na speciální chování výchozích hodnot, které nemají odpovídající parametr trasy, který brání tomu, aby byla trasa příliš [hladkou](xref:fundamentals/routing#greedy) při generování adresy URL.</span><span class="sxs-lookup"><span data-stu-id="e7e54-637">[Dedicated conventional routes](#dcr) rely on a special behavior of default values that don't have a corresponding route parameter that prevents the route from being too [greedy](xref:fundamentals/routing#greedy) with URL generation.</span></span> <span data-ttu-id="e7e54-638">V tomto případě výchozí hodnoty jsou `{ controller = Blog, action = Article }` a ani `controller` `action` se neobjeví jako parametr trasy.</span><span class="sxs-lookup"><span data-stu-id="e7e54-638">In this case the default values are `{ controller = Blog, action = Article }`, and neither `controller` nor `action` appears as a route parameter.</span></span> <span data-ttu-id="e7e54-639">Když směrování provádí generování adresy URL, zadané hodnoty musí odpovídat výchozím hodnotám.</span><span class="sxs-lookup"><span data-stu-id="e7e54-639">When routing performs URL generation, the values provided must match the default values.</span></span> <span data-ttu-id="e7e54-640">Generování adresy URL pomocí `blog` neproběhne úspěšně, protože hodnoty se `{ controller = Home, action = Index }` neshodují `{ controller = Blog, action = Article }` .</span><span class="sxs-lookup"><span data-stu-id="e7e54-640">URL generation using `blog` fails because the values `{ controller = Home, action = Index }` don't match `{ controller = Blog, action = Article }`.</span></span> <span data-ttu-id="e7e54-641">Směrování pak vrátí zpět k akci `default` , která bude úspěšná.</span><span class="sxs-lookup"><span data-stu-id="e7e54-641">Routing then falls back to try `default`, which succeeds.</span></span>

<a name="routing-areas-ref-label"></a>

## <a name="areas"></a><span data-ttu-id="e7e54-642">Oblasti</span><span class="sxs-lookup"><span data-stu-id="e7e54-642">Areas</span></span>

<span data-ttu-id="e7e54-643">[Oblasti](xref:mvc/controllers/areas) představují funkci MVC, která slouží k uspořádání souvisejících funkcí do skupiny jako samostatného:</span><span class="sxs-lookup"><span data-stu-id="e7e54-643">[Areas](xref:mvc/controllers/areas) are an MVC feature used to organize related functionality into a group as a separate:</span></span>

* <span data-ttu-id="e7e54-644">Obor názvů směrování pro akce kontroleru.</span><span class="sxs-lookup"><span data-stu-id="e7e54-644">Routing namespace for controller actions.</span></span>
* <span data-ttu-id="e7e54-645">Struktura složek pro zobrazení.</span><span class="sxs-lookup"><span data-stu-id="e7e54-645">Folder structure for views.</span></span>

<span data-ttu-id="e7e54-646">Použití oblastí umožňuje aplikaci mít více řadičů se stejným názvem, pokud mají různé oblasti.</span><span class="sxs-lookup"><span data-stu-id="e7e54-646">Using areas allows an app to have multiple controllers with the same name, as long as they have different areas.</span></span> <span data-ttu-id="e7e54-647">Použití oblastí vytvoří hierarchii pro účely směrování přidáním dalšího parametru směrování `area` do `controller` a `action` .</span><span class="sxs-lookup"><span data-stu-id="e7e54-647">Using areas creates a hierarchy for the purpose of routing by adding another route parameter, `area` to `controller` and `action`.</span></span> <span data-ttu-id="e7e54-648">Tato část popisuje, jak směrování komunikuje s oblastmi.</span><span class="sxs-lookup"><span data-stu-id="e7e54-648">This section discusses how routing interacts with areas.</span></span> <span data-ttu-id="e7e54-649">Podrobnosti o tom, jak se používají oblasti se zobrazeními, najdete v části [oblasti](xref:mvc/controllers/areas) .</span><span class="sxs-lookup"><span data-stu-id="e7e54-649">See [Areas](xref:mvc/controllers/areas) for details about how areas are used with views.</span></span>

<span data-ttu-id="e7e54-650">Následující příklad nakonfiguruje MVC tak, aby používala výchozí konvenční trasu a `area` trasu pro `area` název `Blog` :</span><span class="sxs-lookup"><span data-stu-id="e7e54-650">The following example configures MVC to use the default conventional route and an `area` route for an `area` named `Blog`:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet1)]

<span data-ttu-id="e7e54-651">V předchozím kódu <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> je volána k vytvoření `"blog_route"` .</span><span class="sxs-lookup"><span data-stu-id="e7e54-651">In the preceding code, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> is called to create the `"blog_route"`.</span></span> <span data-ttu-id="e7e54-652">Druhým parametrem `"Blog"` je název oblasti.</span><span class="sxs-lookup"><span data-stu-id="e7e54-652">The second parameter, `"Blog"`, is the area name.</span></span>

<span data-ttu-id="e7e54-653">Při porovnání cesty URL jako `/Manage/Users/AddUser` se v `"blog_route"` trase vygeneruje hodnoty trasy `{ area = Blog, controller = Users, action = AddUser }` .</span><span class="sxs-lookup"><span data-stu-id="e7e54-653">When matching a URL path like `/Manage/Users/AddUser`, the `"blog_route"` route generates the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span> <span data-ttu-id="e7e54-654">`area`Hodnota trasy je vytvořena výchozí hodnotou pro `area` .</span><span class="sxs-lookup"><span data-stu-id="e7e54-654">The `area` route value is produced by a default value for `area`.</span></span> <span data-ttu-id="e7e54-655">Trasa, kterou vytvořil, `MapAreaControllerRoute` je ekvivalentní následujícímu:</span><span class="sxs-lookup"><span data-stu-id="e7e54-655">The route created by `MapAreaControllerRoute` is equivalent to the following:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup2.cs?name=snippet2)]

<span data-ttu-id="e7e54-656">`MapAreaControllerRoute`Vytvoří trasu s použitím výchozí hodnoty a omezení pro `area` použití poskytnutého názvu oblasti v tomto případě `Blog` .</span><span class="sxs-lookup"><span data-stu-id="e7e54-656">`MapAreaControllerRoute` creates a route using both a default value and constraint for `area` using the provided area name, in this case `Blog`.</span></span> <span data-ttu-id="e7e54-657">Výchozí hodnota zajistí, že trasa vždy vytvoří `{ area = Blog, ... }` , omezení vyžaduje hodnotu `{ area = Blog, ... }` pro generování adresy URL.</span><span class="sxs-lookup"><span data-stu-id="e7e54-657">The default value ensures that the route always produces `{ area = Blog, ... }`, the constraint requires the value `{ area = Blog, ... }` for URL generation.</span></span>

<span data-ttu-id="e7e54-658">Konvenční směrování je závislé na pořadí.</span><span class="sxs-lookup"><span data-stu-id="e7e54-658">Conventional routing is order-dependent.</span></span> <span data-ttu-id="e7e54-659">Obecně platí, že trasy s oblastmi by měly být umístěny dříve, protože jsou konkrétnější než trasy bez oblasti.</span><span class="sxs-lookup"><span data-stu-id="e7e54-659">In general, routes with areas should be placed earlier as they're more specific than routes without an area.</span></span>

<span data-ttu-id="e7e54-660">Pomocí předchozího příkladu se hodnoty tras `{ area = Blog, controller = Users, action = AddUser }` shodují s touto akcí:</span><span class="sxs-lookup"><span data-stu-id="e7e54-660">Using the preceding example, the route values `{ area = Blog, controller = Users, action = AddUser }` match the following action:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

<span data-ttu-id="e7e54-661">Atribut [[Area]](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) je tím, že označuje kontroler jako součást oblasti.</span><span class="sxs-lookup"><span data-stu-id="e7e54-661">The [[Area]](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) attribute is what denotes a controller as part of an area.</span></span> <span data-ttu-id="e7e54-662">Tento kontroler je v `Blog` oblasti.</span><span class="sxs-lookup"><span data-stu-id="e7e54-662">This controller is in the `Blog` area.</span></span> <span data-ttu-id="e7e54-663">Řadiče bez `[Area]` atributu nejsou členy žádné oblasti a neodpovídají, pokud **not** `area` je hodnota směrování poskytována směrováním.</span><span class="sxs-lookup"><span data-stu-id="e7e54-663">Controllers without an `[Area]` attribute are not members of any area, and do **not** match when the `area` route value is provided by routing.</span></span> <span data-ttu-id="e7e54-664">V následujícím příkladu se může shodovat jenom první kontroler, který bude odpovídat hodnotám tras `{ area = Blog, controller = Users, action = AddUser }` .</span><span class="sxs-lookup"><span data-stu-id="e7e54-664">In the following example, only the first controller listed can match the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Zebra/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/UsersController.cs)]

<span data-ttu-id="e7e54-665">Pro úplnost se zobrazí obor názvů každého kontroleru.</span><span class="sxs-lookup"><span data-stu-id="e7e54-665">The namespace of each controller is shown here for completeness.</span></span> <span data-ttu-id="e7e54-666">Pokud předchozí řadiče používají stejný obor názvů, vygeneruje se chyba kompilátoru.</span><span class="sxs-lookup"><span data-stu-id="e7e54-666">If the preceding controllers uses the same namespace, a compiler error would be generated.</span></span> <span data-ttu-id="e7e54-667">Obory názvů třídy nemají žádný vliv na směrování MVC.</span><span class="sxs-lookup"><span data-stu-id="e7e54-667">Class namespaces have no effect on MVC's routing.</span></span>

<span data-ttu-id="e7e54-668">První dva řadiče jsou členy oblastí a odpovídají pouze v případě, že je jejich název příslušné oblasti poskytovaný `area` hodnotou trasy.</span><span class="sxs-lookup"><span data-stu-id="e7e54-668">The first two controllers are members of areas, and only match when their respective area name is provided by the `area` route value.</span></span> <span data-ttu-id="e7e54-669">Třetí kontroler není členem žádné oblasti a může odpovídat jenom v případě, že `area` směrováním neposkytuje žádnou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="e7e54-669">The third controller isn't a member of any area, and can only match when no value for `area` is provided by routing.</span></span>

<a name="aa"></a>

<span data-ttu-id="e7e54-670">V souvislosti s neshodnou *hodnotou* `area` je absence hodnoty stejná, jako kdyby hodnota `area` null nebo prázdný řetězec.</span><span class="sxs-lookup"><span data-stu-id="e7e54-670">In terms of matching *no value*, the absence of the `area` value is the same as if the value for `area` were null or the empty string.</span></span>

<span data-ttu-id="e7e54-671">Při provádění akce uvnitř oblasti je hodnota trasy `area` dostupná jako [ambientní hodnota](#ambient) pro směrování, která se má použít pro generování adresy URL.</span><span class="sxs-lookup"><span data-stu-id="e7e54-671">When executing an action inside an area, the route value for `area` is available as an [ambient value](#ambient) for routing to use for URL generation.</span></span> <span data-ttu-id="e7e54-672">To znamená, že ve výchozím nastavení jsou tyto oblasti pro generování adresy URL *rychlé* , jak je znázorněno v následující ukázce.</span><span class="sxs-lookup"><span data-stu-id="e7e54-672">This means that by default areas act *sticky* for URL generation as demonstrated by the following sample.</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup3.cs?name=snippet3)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Duck/Controllers/UsersController.cs)]

<span data-ttu-id="e7e54-673">Následující kód vygeneruje adresu URL pro `/Zebra/Users/AddUser` :</span><span class="sxs-lookup"><span data-stu-id="e7e54-673">The following code generates a URL to `/Zebra/Users/AddUser`:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/HomeController.cs?name=snippet)]

<a name="action"></a>

## <a name="action-definition"></a><span data-ttu-id="e7e54-674">Definice akce</span><span class="sxs-lookup"><span data-stu-id="e7e54-674">Action definition</span></span>

<span data-ttu-id="e7e54-675">Veřejné metody na řadiči, s výjimkou atributu [neaction](xref:Microsoft.AspNetCore.Mvc.NonActionAttribute) , jsou akce.</span><span class="sxs-lookup"><span data-stu-id="e7e54-675">Public methods on a controller, except those with the [NonAction](xref:Microsoft.AspNetCore.Mvc.NonActionAttribute) attribute, are actions.</span></span>

## <a name="sample-code"></a><span data-ttu-id="e7e54-676">Ukázka kódu</span><span class="sxs-lookup"><span data-stu-id="e7e54-676">Sample code</span></span>

 * <span data-ttu-id="e7e54-677">Metoda [MyDisplayRouteInfo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x/main/Extensions/ControllerContextExtensions.cs) je obsažena v [ukázce stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) a slouží k zobrazení informací o směrování.</span><span class="sxs-lookup"><span data-stu-id="e7e54-677">The [MyDisplayRouteInfo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x/main/Extensions/ControllerContextExtensions.cs) method is included in the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) and is used to display routing information.</span></span>
* <span data-ttu-id="e7e54-678">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="e7e54-678">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) ([how to download](xref:index#how-to-download-a-sample))</span></span>

[!INCLUDE[](~/includes/dbg-route.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="e7e54-679">ASP.NET Core MVC používá [middleware](xref:fundamentals/middleware/index) směrování ke spárování adres URL příchozích požadavků a jejich mapování na akce.</span><span class="sxs-lookup"><span data-stu-id="e7e54-679">ASP.NET Core MVC uses the Routing [middleware](xref:fundamentals/middleware/index) to match the URLs of incoming requests and map them to actions.</span></span> <span data-ttu-id="e7e54-680">Trasy jsou definovány ve spouštěcím kódu nebo v atributech.</span><span class="sxs-lookup"><span data-stu-id="e7e54-680">Routes are defined in startup code or attributes.</span></span> <span data-ttu-id="e7e54-681">Trasy popisují, jak by měly být cesty URL odpovídat akcím.</span><span class="sxs-lookup"><span data-stu-id="e7e54-681">Routes describe how URL paths should be matched to actions.</span></span> <span data-ttu-id="e7e54-682">Trasy se také používají ke generování adres URL (pro odkazy) odesílaných v odpovědích.</span><span class="sxs-lookup"><span data-stu-id="e7e54-682">Routes are also used to generate URLs (for links) sent out in responses.</span></span>

<span data-ttu-id="e7e54-683">Akce jsou směrovány buď podle konvence nebo směrovaného atributu.</span><span class="sxs-lookup"><span data-stu-id="e7e54-683">Actions are either conventionally routed or attribute routed.</span></span> <span data-ttu-id="e7e54-684">Umístěním trasy do kontroleru nebo akce se nasměruje atribut IT.</span><span class="sxs-lookup"><span data-stu-id="e7e54-684">Placing a route on the controller or the action makes it attribute routed.</span></span> <span data-ttu-id="e7e54-685">Další informace najdete v tématu [smíšená směrování](#routing-mixed-ref-label) .</span><span class="sxs-lookup"><span data-stu-id="e7e54-685">See [Mixed routing](#routing-mixed-ref-label) for more information.</span></span>

<span data-ttu-id="e7e54-686">Tento dokument vysvětluje interakce mezi MVC a směrováními a jak běžné aplikace MVC využívají funkce směrování.</span><span class="sxs-lookup"><span data-stu-id="e7e54-686">This document will explain the interactions between MVC and routing, and how typical MVC apps make use of routing features.</span></span> <span data-ttu-id="e7e54-687">Podrobnosti o pokročilém směrování najdete v tématu [Směrování](xref:fundamentals/routing) .</span><span class="sxs-lookup"><span data-stu-id="e7e54-687">See [Routing](xref:fundamentals/routing) for details on advanced routing.</span></span>

## <a name="setting-up-routing-middleware"></a><span data-ttu-id="e7e54-688">Nastavení middlewaru směrování</span><span class="sxs-lookup"><span data-stu-id="e7e54-688">Setting up Routing Middleware</span></span>

<span data-ttu-id="e7e54-689">V metodě *Konfigurace* se může zobrazit kód podobný tomuto:</span><span class="sxs-lookup"><span data-stu-id="e7e54-689">In your *Configure* method you may see code similar to:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="e7e54-690">Uvnitř volání metody `UseMvc` `MapRoute` se používá k vytvoření jedné trasy, na kterou odkazujeme jako na `default` trase.</span><span class="sxs-lookup"><span data-stu-id="e7e54-690">Inside the call to `UseMvc`, `MapRoute` is used to create a single route, which we'll refer to as the `default` route.</span></span> <span data-ttu-id="e7e54-691">Většina aplikací MVC bude používat trasu se šablonou podobnou této `default` trase.</span><span class="sxs-lookup"><span data-stu-id="e7e54-691">Most MVC apps will use a route with a template similar to the `default` route.</span></span>

<span data-ttu-id="e7e54-692">Šablona trasy se `"{controller=Home}/{action=Index}/{id?}"` může shodovat s cestou adresy URL `/Products/Details/5` , jako je, a extrahuje hodnoty tras tím, že `{ controller = Products, action = Details, id = 5 }` tokenizací cestu.</span><span class="sxs-lookup"><span data-stu-id="e7e54-692">The route template `"{controller=Home}/{action=Index}/{id?}"` can match a URL path like `/Products/Details/5` and will extract the route values `{ controller = Products, action = Details, id = 5 }` by tokenizing the path.</span></span> <span data-ttu-id="e7e54-693">MVC se pokusí vyhledat kontroler s názvem `ProductsController` a spustit akci `Details` :</span><span class="sxs-lookup"><span data-stu-id="e7e54-693">MVC will attempt to locate a controller named `ProductsController` and run the action `Details`:</span></span>

```csharp
public class ProductsController : Controller
{
   public IActionResult Details(int id) { ... }
}
```

<span data-ttu-id="e7e54-694">Všimněte si, že v tomto příkladu vazba modelu použije hodnotu `id = 5` pro nastavení `id` parametru `5` při vyvolání této akce.</span><span class="sxs-lookup"><span data-stu-id="e7e54-694">Note that in this example, model binding would use the value of `id = 5` to set the `id` parameter to `5` when invoking this action.</span></span> <span data-ttu-id="e7e54-695">Další podrobnosti najdete v tématu [vazba modelu](../models/model-binding.md) .</span><span class="sxs-lookup"><span data-stu-id="e7e54-695">See the [Model Binding](../models/model-binding.md) for more details.</span></span>

<span data-ttu-id="e7e54-696">Pomocí `default` trasy:</span><span class="sxs-lookup"><span data-stu-id="e7e54-696">Using the `default` route:</span></span>

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="e7e54-697">Šablona trasy:</span><span class="sxs-lookup"><span data-stu-id="e7e54-697">The route template:</span></span>

* <span data-ttu-id="e7e54-698">`{controller=Home}`definuje `Home` jako výchozí.`controller`</span><span class="sxs-lookup"><span data-stu-id="e7e54-698">`{controller=Home}` defines `Home` as the default `controller`</span></span>

* <span data-ttu-id="e7e54-699">`{action=Index}`definuje `Index` jako výchozí.`action`</span><span class="sxs-lookup"><span data-stu-id="e7e54-699">`{action=Index}` defines `Index` as the default `action`</span></span>

* <span data-ttu-id="e7e54-700">`{id?}`definuje `id` jako volitelné.</span><span class="sxs-lookup"><span data-stu-id="e7e54-700">`{id?}` defines `id` as optional</span></span>

<span data-ttu-id="e7e54-701">Výchozí a volitelné parametry směrování nemusejí být v cestě URL pro porovnávání k dispozici.</span><span class="sxs-lookup"><span data-stu-id="e7e54-701">Default and optional route parameters don't need to be present in the URL path for a match.</span></span> <span data-ttu-id="e7e54-702">Podrobný popis syntaxe šablony směrování naleznete v tématu Referenční dokumentace k [šabloně směrování](xref:fundamentals/routing#route-template-reference) .</span><span class="sxs-lookup"><span data-stu-id="e7e54-702">See [Route Template Reference](xref:fundamentals/routing#route-template-reference) for a detailed description of route template syntax.</span></span>

<span data-ttu-id="e7e54-703">`"{controller=Home}/{action=Index}/{id?}"`může odpovídat cestě URL `/` a vytvoří hodnoty tras `{ controller = Home, action = Index }` .</span><span class="sxs-lookup"><span data-stu-id="e7e54-703">`"{controller=Home}/{action=Index}/{id?}"` can match the URL path `/` and will produce the route values `{ controller = Home, action = Index }`.</span></span> <span data-ttu-id="e7e54-704">Hodnoty pro `controller` a `action` využívají výchozí hodnoty `id` nevytvářejí hodnotu, protože v cestě URL není žádný odpovídající segment.</span><span class="sxs-lookup"><span data-stu-id="e7e54-704">The values for `controller` and `action` make use of the default values, `id` doesn't produce a value since there's no corresponding segment in the URL path.</span></span> <span data-ttu-id="e7e54-705">MVC by tyto hodnoty trasy použily k výběru `HomeController` `Index` akce a:</span><span class="sxs-lookup"><span data-stu-id="e7e54-705">MVC would use these route values to select the `HomeController` and `Index` action:</span></span>

```csharp
public class HomeController : Controller
{
  public IActionResult Index() { ... }
}
```

<span data-ttu-id="e7e54-706">Pomocí této definice kontroleru a šablony trasy se `HomeController.Index` akce spustí pro libovolnou z následujících cest URL:</span><span class="sxs-lookup"><span data-stu-id="e7e54-706">Using this controller definition and route template, the `HomeController.Index` action would be executed for any of the following URL paths:</span></span>

* `/Home/Index/17`

* `/Home/Index`

* `/Home`

* `/`

<span data-ttu-id="e7e54-707">Způsob usnadnění `UseMvcWithDefaultRoute` :</span><span class="sxs-lookup"><span data-stu-id="e7e54-707">The convenience method `UseMvcWithDefaultRoute`:</span></span>

```csharp
app.UseMvcWithDefaultRoute();
```

<span data-ttu-id="e7e54-708">Dá se použít k nahrazení:</span><span class="sxs-lookup"><span data-stu-id="e7e54-708">Can be used to replace:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="e7e54-709">`UseMvc`a `UseMvcWithDefaultRoute` přidejte instanci `RouterMiddleware` do kanálu middlewaru.</span><span class="sxs-lookup"><span data-stu-id="e7e54-709">`UseMvc` and `UseMvcWithDefaultRoute` add an instance of `RouterMiddleware` to the middleware pipeline.</span></span> <span data-ttu-id="e7e54-710">MVC nekomunikuje přímo se middlewarem a používá směrování pro zpracování požadavků.</span><span class="sxs-lookup"><span data-stu-id="e7e54-710">MVC doesn't interact directly with middleware, and uses routing to handle requests.</span></span> <span data-ttu-id="e7e54-711">MVC je připojeno k trasám prostřednictvím instance `MvcRouteHandler` .</span><span class="sxs-lookup"><span data-stu-id="e7e54-711">MVC is connected to the routes through an instance of `MvcRouteHandler`.</span></span> <span data-ttu-id="e7e54-712">Kód uvnitř `UseMvc` je podobný následujícímu:</span><span class="sxs-lookup"><span data-stu-id="e7e54-712">The code inside of `UseMvc` is similar to the following:</span></span>

```csharp
var routes = new RouteBuilder(app);

// Add connection to MVC, will be hooked up by calls to MapRoute.
routes.DefaultHandler = new MvcRouteHandler(...);

// Execute callback to register routes.
// routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");

// Create route collection and add the middleware.
app.UseRouter(routes.Build());
```

<span data-ttu-id="e7e54-713">`UseMvc`přímo nedefinuje žádné trasy, přidá zástupný symbol do kolekce směrování pro `attribute` trasu.</span><span class="sxs-lookup"><span data-stu-id="e7e54-713">`UseMvc` doesn't directly define any routes, it adds a placeholder to the route collection for the `attribute` route.</span></span> <span data-ttu-id="e7e54-714">Přetížení `UseMvc(Action<IRouteBuilder>)` umožňuje přidat vlastní trasy a zároveň podporuje směrování atributů.</span><span class="sxs-lookup"><span data-stu-id="e7e54-714">The overload `UseMvc(Action<IRouteBuilder>)` lets you add your own routes and also supports attribute routing.</span></span>  <span data-ttu-id="e7e54-715">`UseMvc`a všechny jeho variace přidávají zástupný symbol pro atribut směrování atributu směrování je vždycky dostupný bez ohledu na to, jak nakonfigurujete `UseMvc` .</span><span class="sxs-lookup"><span data-stu-id="e7e54-715">`UseMvc` and all of its variations add a placeholder for the attribute route - attribute routing is always available regardless of how you configure `UseMvc`.</span></span> <span data-ttu-id="e7e54-716">`UseMvcWithDefaultRoute`definuje výchozí trasu a podporuje směrování atributů.</span><span class="sxs-lookup"><span data-stu-id="e7e54-716">`UseMvcWithDefaultRoute` defines a default route and supports attribute routing.</span></span> <span data-ttu-id="e7e54-717">Oddíl [Směrování atributů](#attribute-routing-ref-label) obsahuje další podrobnosti o směrování atributů.</span><span class="sxs-lookup"><span data-stu-id="e7e54-717">The [Attribute Routing](#attribute-routing-ref-label) section includes more details on attribute routing.</span></span>

<a name="routing-conventional-ref-label"></a>

## <a name="conventional-routing"></a><span data-ttu-id="e7e54-718">Konvenční směrování</span><span class="sxs-lookup"><span data-stu-id="e7e54-718">Conventional routing</span></span>

<span data-ttu-id="e7e54-719">`default`Trasa:</span><span class="sxs-lookup"><span data-stu-id="e7e54-719">The `default` route:</span></span>

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="e7e54-720">Předchozí kód je příkladem konvenčního směrování.</span><span class="sxs-lookup"><span data-stu-id="e7e54-720">The preceding code is an example of a conventional routing.</span></span> <span data-ttu-id="e7e54-721">Tento styl se nazývá konvenční směrování, protože vytváří *konvenci* pro cesty URL:</span><span class="sxs-lookup"><span data-stu-id="e7e54-721">This style is called conventional routing because it establishes a *convention* for URL paths:</span></span>

* <span data-ttu-id="e7e54-722">První segment cesty se mapuje na název kontroleru.</span><span class="sxs-lookup"><span data-stu-id="e7e54-722">The first path segment maps to the controller name.</span></span>
* <span data-ttu-id="e7e54-723">Druhý se mapuje na název akce.</span><span class="sxs-lookup"><span data-stu-id="e7e54-723">The second maps to the action name.</span></span>
* <span data-ttu-id="e7e54-724">Třetí segment se používá pro volitelné `id` .</span><span class="sxs-lookup"><span data-stu-id="e7e54-724">The third segment is used for an optional `id`.</span></span> <span data-ttu-id="e7e54-725">`id`provede mapování na entitu modelu.</span><span class="sxs-lookup"><span data-stu-id="e7e54-725">`id` maps to a model entity.</span></span>

<span data-ttu-id="e7e54-726">Pomocí této `default` trasy se cesta URL `/Products/List` mapuje na `ProductsController.List` akci a `/Blog/Article/17` mapuje se na `BlogController.Article` .</span><span class="sxs-lookup"><span data-stu-id="e7e54-726">Using this `default` route, the URL path `/Products/List` maps to the `ProductsController.List` action, and `/Blog/Article/17` maps to `BlogController.Article`.</span></span> <span data-ttu-id="e7e54-727">Toto mapování je založené **pouze** na názvech kontroléru a akce a není založené na oborech názvů, umístěních zdrojového souboru nebo parametrech metody.</span><span class="sxs-lookup"><span data-stu-id="e7e54-727">This mapping is based on the controller and action names **only** and isn't based on namespaces, source file locations, or method parameters.</span></span>

> [!TIP]
> <span data-ttu-id="e7e54-728">Použití konvenčního směrování s výchozí trasou vám umožní rychle vytvořit aplikaci, aniž by se musela používat nový vzor adresy URL pro každou akci, kterou definujete.</span><span class="sxs-lookup"><span data-stu-id="e7e54-728">Using conventional routing with the default route allows you to build the application quickly without having to come up with a new URL pattern for each action you define.</span></span> <span data-ttu-id="e7e54-729">Pro aplikace s akcemi stylu CRUD, které mají konzistenci pro adresy URL napříč řadiči, může zjednodušit váš kód a zajistit větší předvídatelné uživatelské rozhraní.</span><span class="sxs-lookup"><span data-stu-id="e7e54-729">For an application with CRUD style actions, having consistency for the URLs across your controllers can help simplify your code and make your UI more predictable.</span></span>

> [!WARNING]
> <span data-ttu-id="e7e54-730">`id`Šablona trasy je definována jako volitelná, což znamená, že vaše akce mohou být provedeny bez ID, které je součástí adresy URL.</span><span class="sxs-lookup"><span data-stu-id="e7e54-730">The `id` is defined as optional by the route template, meaning that your actions can execute without the ID provided as part of the URL.</span></span> <span data-ttu-id="e7e54-731">K tomu, co se stane `id` , když se z adresy URL vynechá, znamená to, že se nastaví na `0` základě vazby modelů, a v důsledku toho se v porovnání databáze nenajde žádná entita `id == 0` .</span><span class="sxs-lookup"><span data-stu-id="e7e54-731">Usually what will happen if `id` is omitted from the URL is that it will be set to `0` by model binding, and as a result no entity will be found in the database matching `id == 0`.</span></span> <span data-ttu-id="e7e54-732">Směrování atributů vám může poskytnout jemně odstupňovaný ovládací prvek, který umožňuje, aby se ID vyžadovalo u některých akcí, a ne pro ostatní.</span><span class="sxs-lookup"><span data-stu-id="e7e54-732">Attribute routing can give you fine-grained control to make the ID required for some actions and not for others.</span></span> <span data-ttu-id="e7e54-733">Podle konvence bude dokumentace obsahovat volitelné parametry, jako když se budou `id` pravděpodobně zobrazovat ve správném použití.</span><span class="sxs-lookup"><span data-stu-id="e7e54-733">By convention the documentation will include optional parameters like `id` when they're likely to appear in correct usage.</span></span>

## <a name="multiple-routes"></a><span data-ttu-id="e7e54-734">Více tras</span><span class="sxs-lookup"><span data-stu-id="e7e54-734">Multiple routes</span></span>

<span data-ttu-id="e7e54-735">Do můžete přidat více tras v rámci `UseMvc` přidáním dalších volání do `MapRoute` .</span><span class="sxs-lookup"><span data-stu-id="e7e54-735">You can add multiple routes inside `UseMvc` by adding more calls to `MapRoute`.</span></span> <span data-ttu-id="e7e54-736">V takovém případě můžete definovat více konvencí nebo přidat konvenční trasy, které jsou vyhrazeny určité akci, například:</span><span class="sxs-lookup"><span data-stu-id="e7e54-736">Doing so allows you to define multiple conventions, or to add conventional routes that are dedicated to a specific action, such as:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("blog", "blog/{*article}",
            defaults: new { controller = "Blog", action = "Article" });
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="e7e54-737">`blog`Trasa je *vyhrazená konvenční trasa*, což znamená, že používá konvenční směrovací systém, ale je vyhrazená pro konkrétní akci.</span><span class="sxs-lookup"><span data-stu-id="e7e54-737">The `blog` route here is a *dedicated conventional route*, meaning that it uses the conventional routing system, but is dedicated to a specific action.</span></span> <span data-ttu-id="e7e54-738">Vzhledem `controller` k tomu, že se `action` nezobrazuje v šabloně směrování jako parametry, můžou mít jenom výchozí hodnoty, takže tato trasa se vždycky namapuje na akci `BlogController.Article` .</span><span class="sxs-lookup"><span data-stu-id="e7e54-738">Since `controller` and `action` don't appear in the route template as parameters, they can only have the default values, and thus this route will always map to the action `BlogController.Article`.</span></span>

<span data-ttu-id="e7e54-739">Trasy v kolekci tras jsou seřazené a budou zpracovávány v pořadí, v jakém byly přidány.</span><span class="sxs-lookup"><span data-stu-id="e7e54-739">Routes in the route collection are ordered, and will be processed in the order they're added.</span></span> <span data-ttu-id="e7e54-740">V tomto příkladu `blog` bude trasa vyzkoušena před `default` trasou.</span><span class="sxs-lookup"><span data-stu-id="e7e54-740">So in this example, the `blog` route will be tried before the `default` route.</span></span>

> [!NOTE]
> <span data-ttu-id="e7e54-741">*Vyhrazené konvenční trasy* často používají **zachycení všech** parametrů tras, jako `{*article}` je zachycení zbývající části cesty URL.</span><span class="sxs-lookup"><span data-stu-id="e7e54-741">*Dedicated conventional routes* often use **catch-all** route parameters like `{*article}` to capture the remaining portion of the URL path.</span></span> <span data-ttu-id="e7e54-742">To může vést k tomu, že trasa "je příliš hlade", což znamená, že odpovídá adresám URL, které mají odpovídat jiným trasám.</span><span class="sxs-lookup"><span data-stu-id="e7e54-742">This can make a route 'too greedy' meaning that it matches URLs that you intended to be matched by other routes.</span></span> <span data-ttu-id="e7e54-743">Pokud to chcete vyřešit, umístěte trasy "hladce" do směrovací tabulky později.</span><span class="sxs-lookup"><span data-stu-id="e7e54-743">Put the 'greedy' routes later in the route table to solve this.</span></span>

### <a name="fallback"></a><span data-ttu-id="e7e54-744">Záložní volba</span><span class="sxs-lookup"><span data-stu-id="e7e54-744">Fallback</span></span>

<span data-ttu-id="e7e54-745">V rámci zpracování žádosti MVC ověří, že hodnoty trasy lze použít k vyhledání kontroleru a akce v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="e7e54-745">As part of request processing, MVC will verify that the route values can be used to find a controller and action in your application.</span></span> <span data-ttu-id="e7e54-746">Pokud se hodnoty trasy neshodují s akcí, trasa není považována za shodnou a bude se opakovat další trasa.</span><span class="sxs-lookup"><span data-stu-id="e7e54-746">If the route values don't match an action then the route isn't considered a match, and the next route will be tried.</span></span> <span data-ttu-id="e7e54-747">Označuje se jako *záložní*a je určený pro zjednodušení případů, kdy se konvenční trasy překrývají.</span><span class="sxs-lookup"><span data-stu-id="e7e54-747">This is called *fallback*, and it's intended to simplify cases where conventional routes overlap.</span></span>

### <a name="disambiguating-actions"></a><span data-ttu-id="e7e54-748">Nejednoznačnost akcí</span><span class="sxs-lookup"><span data-stu-id="e7e54-748">Disambiguating actions</span></span>

<span data-ttu-id="e7e54-749">Pokud se dvě akce shodují přes směrování, MVC musí nejednoznačně vybrat kandidáta na nejlepší nebo jinak vyvolat výjimku.</span><span class="sxs-lookup"><span data-stu-id="e7e54-749">When two actions match through routing, MVC must disambiguate to choose the 'best' candidate or else throw an exception.</span></span> <span data-ttu-id="e7e54-750">Příklad:</span><span class="sxs-lookup"><span data-stu-id="e7e54-750">For example:</span></span>

```csharp
public class ProductsController : Controller
{
   public IActionResult Edit(int id) { ... }

   [HttpPost]
   public IActionResult Edit(int id, Product product) { ... }
}
```

<span data-ttu-id="e7e54-751">Tento kontroler definuje dvě akce, které by odpovídaly cestě URL `/Products/Edit/17` a datům směrování `{ controller = Products, action = Edit, id = 17 }` .</span><span class="sxs-lookup"><span data-stu-id="e7e54-751">This controller defines two actions that would match the URL path `/Products/Edit/17` and route data `{ controller = Products, action = Edit, id = 17 }`.</span></span> <span data-ttu-id="e7e54-752">Toto je typický vzor pro řadiče MVC, kde se `Edit(int)` zobrazuje formulář pro úpravu produktu a `Edit(int, Product)` zpracovává se tam publikovaný formulář.</span><span class="sxs-lookup"><span data-stu-id="e7e54-752">This is a typical pattern for MVC controllers where `Edit(int)` shows a form to edit a product, and `Edit(int, Product)` processes  the posted form.</span></span> <span data-ttu-id="e7e54-753">Aby tato možnost MVC mohla být v případě, že je `Edit(int, Product)` požadavek HTTP `POST` , a `Edit(int)` Pokud je příkaz HTTP cokoli jiného, musí si vybrat, když je žádost http.</span><span class="sxs-lookup"><span data-stu-id="e7e54-753">To make this possible MVC would need to choose `Edit(int, Product)` when the request is an HTTP `POST` and `Edit(int)` when the HTTP verb is anything else.</span></span>

<span data-ttu-id="e7e54-754">Rozhraní `HttpPostAttribute` ( `[HttpPost]` ) je implementací `IActionConstraint` , která umožňuje, aby byla akce vybrána pouze v případě, že je příkaz http `POST` .</span><span class="sxs-lookup"><span data-stu-id="e7e54-754">The `HttpPostAttribute` ( `[HttpPost]` ) is an implementation of `IActionConstraint` that will only allow the action to be selected when the HTTP verb is `POST`.</span></span> <span data-ttu-id="e7e54-755">Přítomnost `IActionConstraint` `Edit(int, Product)` a "lepší" se shoduje s tím, že se `Edit(int)` bude zkoušet jako `Edit(int, Product)` první.</span><span class="sxs-lookup"><span data-stu-id="e7e54-755">The presence of an `IActionConstraint` makes the `Edit(int, Product)` a 'better' match than `Edit(int)`, so `Edit(int, Product)` will be tried first.</span></span>

<span data-ttu-id="e7e54-756">Budete muset psát pouze vlastní `IActionConstraint` implementace ve specializovaných scénářích, ale je důležité pochopit, že role `HttpPostAttribute` podobných atributů je definována pro jiné příkazy HTTP.</span><span class="sxs-lookup"><span data-stu-id="e7e54-756">You will only need to write custom `IActionConstraint` implementations in specialized scenarios, but it's important to understand the role of attributes like `HttpPostAttribute`  - similar attributes are defined for other HTTP verbs.</span></span> <span data-ttu-id="e7e54-757">V případě konvenčního směrování je běžné, že akce budou používat stejný název akce, i když jsou součástí `show form -> submit form` pracovního postupu.</span><span class="sxs-lookup"><span data-stu-id="e7e54-757">In conventional routing it's common for actions to use the same action name when they're part of a `show form -> submit form` workflow.</span></span> <span data-ttu-id="e7e54-758">Pohodlí tohoto vzoru se po kontrole části [porozumění IActionConstraint](#understanding-iactionconstraint) projeví.</span><span class="sxs-lookup"><span data-stu-id="e7e54-758">The convenience of this pattern will become more apparent after reviewing the [Understanding IActionConstraint](#understanding-iactionconstraint) section.</span></span>

<span data-ttu-id="e7e54-759">Pokud se více tras shodují a MVC nemůže najít nejlepší trasu, vyvolá výjimku `AmbiguousActionException` .</span><span class="sxs-lookup"><span data-stu-id="e7e54-759">If multiple routes match, and MVC can't find a 'best' route, it will throw an `AmbiguousActionException`.</span></span>

<a name="routing-route-name-ref-label"></a>

### <a name="route-names"></a><span data-ttu-id="e7e54-760">Názvy tras</span><span class="sxs-lookup"><span data-stu-id="e7e54-760">Route names</span></span>

<span data-ttu-id="e7e54-761">Řetězce `"blog"` a `"default"` v následujících příkladech jsou názvy tras:</span><span class="sxs-lookup"><span data-stu-id="e7e54-761">The strings  `"blog"` and `"default"` in the following examples are route names:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("blog", "blog/{*article}",
               defaults: new { controller = "Blog", action = "Article" });
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="e7e54-762">Názvy tras směrují logický název tak, aby se pojmenovaná trasa dala použít pro generování adresy URL.</span><span class="sxs-lookup"><span data-stu-id="e7e54-762">The route names give the route a logical name so that the named route can be used for URL generation.</span></span> <span data-ttu-id="e7e54-763">Tím se značně zjednodušuje vytváření adres URL, když řazení tras může způsobit komplikované generování adresy URL.</span><span class="sxs-lookup"><span data-stu-id="e7e54-763">This greatly simplifies URL creation when the ordering of routes could make URL generation complicated.</span></span> <span data-ttu-id="e7e54-764">Názvy tras musí být jedinečné aplikace v rozsahu.</span><span class="sxs-lookup"><span data-stu-id="e7e54-764">Route names must be unique application-wide.</span></span>

<span data-ttu-id="e7e54-765">Názvy tras nemají žádný vliv na adresy URL, které by odpovídaly ani manipulaci s požadavky. používají se pouze pro generování adresy URL.</span><span class="sxs-lookup"><span data-stu-id="e7e54-765">Route names have no impact on URL matching or handling of requests; they're used only for URL generation.</span></span> <span data-ttu-id="e7e54-766">[Směrování](xref:fundamentals/routing) obsahuje podrobnější informace o generování adresy URL včetně generování adresy URL v pomocníkech specifických pro MVC.</span><span class="sxs-lookup"><span data-stu-id="e7e54-766">[Routing](xref:fundamentals/routing) has more detailed information on URL generation including URL generation in MVC-specific helpers.</span></span>

<a name="attribute-routing-ref-label"></a>

## <a name="attribute-routing"></a><span data-ttu-id="e7e54-767">Směrování atributů</span><span class="sxs-lookup"><span data-stu-id="e7e54-767">Attribute routing</span></span>

<span data-ttu-id="e7e54-768">Směrování atributů používá sadu atributů k mapování akcí přímo na šablony směrování.</span><span class="sxs-lookup"><span data-stu-id="e7e54-768">Attribute routing uses a set of attributes to map actions directly to route templates.</span></span> <span data-ttu-id="e7e54-769">V následujícím příkladu `app.UseMvc();` se používá v `Configure` metodě a není předána žádná trasa.</span><span class="sxs-lookup"><span data-stu-id="e7e54-769">In the following example, `app.UseMvc();` is used in the `Configure` method and no route is passed.</span></span> <span data-ttu-id="e7e54-770">`HomeController`Bude odpovídat sadě adres URL podobně jako výchozí trasa, která odpovídá `{controller=Home}/{action=Index}/{id?}` :</span><span class="sxs-lookup"><span data-stu-id="e7e54-770">The `HomeController` will match a set of URLs similar to what the default route `{controller=Home}/{action=Index}/{id?}` would match:</span></span>

```csharp
public class HomeController : Controller
{
   [Route("")]
   [Route("Home")]
   [Route("Home/Index")]
   public IActionResult Index()
   {
      return View();
   }
   [Route("Home/About")]
   public IActionResult About()
   {
      return View();
   }
   [Route("Home/Contact")]
   public IActionResult Contact()
   {
      return View();
   }
}
```

<span data-ttu-id="e7e54-771">`HomeController.Index()`Akce se spustí pro všechny cesty URL `/` , `/Home` nebo `/Home/Index` .</span><span class="sxs-lookup"><span data-stu-id="e7e54-771">The `HomeController.Index()` action will be executed for any of the URL paths `/`, `/Home`, or `/Home/Index`.</span></span>

> [!NOTE]
> <span data-ttu-id="e7e54-772">V tomto příkladu se zvýrazní klíčový rozdíl mezi směrováním atributů a konvenčním směrováním.</span><span class="sxs-lookup"><span data-stu-id="e7e54-772">This example highlights a key programming difference between attribute routing and conventional routing.</span></span> <span data-ttu-id="e7e54-773">Směrování atributů vyžaduje více vstupu pro určení trasy; konvenční výchozí trasa zpracovává trasy více stručně.</span><span class="sxs-lookup"><span data-stu-id="e7e54-773">Attribute routing requires more input to specify a route; the conventional default route handles routes more succinctly.</span></span> <span data-ttu-id="e7e54-774">Směrování atributů ale povoluje (a vyžaduje) přesnou kontrolu nad tím, které šablony směrování se vztahují na každou akci.</span><span class="sxs-lookup"><span data-stu-id="e7e54-774">However, attribute routing allows (and requires) precise control of which route templates apply to each action.</span></span>

<span data-ttu-id="e7e54-775">Při směrování atributů název kontroleru a názvy akcí nehraje **žádná** role, ve které je vybrána akce.</span><span class="sxs-lookup"><span data-stu-id="e7e54-775">With attribute routing the controller name and action names play **no** role in which action is selected.</span></span> <span data-ttu-id="e7e54-776">Tento příklad bude odpovídat stejným adresám URL jako v předchozím příkladu.</span><span class="sxs-lookup"><span data-stu-id="e7e54-776">This example will match the same URLs as the previous example.</span></span>

```csharp
public class MyDemoController : Controller
{
   [Route("")]
   [Route("Home")]
   [Route("Home/Index")]
   public IActionResult MyIndex()
   {
      return View("Index");
   }
   [Route("Home/About")]
   public IActionResult MyAbout()
   {
      return View("About");
   }
   [Route("Home/Contact")]
   public IActionResult MyContact()
   {
      return View("Contact");
   }
}
```

> [!NOTE]
> <span data-ttu-id="e7e54-777">Výše uvedené šablony tras nedefinují parametry směrování pro `action` , `area` a `controller` .</span><span class="sxs-lookup"><span data-stu-id="e7e54-777">The route templates above don't define route parameters for `action`, `area`, and `controller`.</span></span> <span data-ttu-id="e7e54-778">Ve skutečnosti nejsou tyto parametry trasy v trasách atributů povoleny.</span><span class="sxs-lookup"><span data-stu-id="e7e54-778">In fact, these route parameters are not allowed in attribute routes.</span></span> <span data-ttu-id="e7e54-779">Vzhledem k tomu, že šablona trasy je již k akci přidružena, by nedošlo k tomu, že by bylo vhodné analyzovat název akce z adresy URL.</span><span class="sxs-lookup"><span data-stu-id="e7e54-779">Since the route template is already associated with an action, it wouldn't make sense to parse the action name from the URL.</span></span>

## <a name="attribute-routing-with-httpverb-attributes"></a><span data-ttu-id="e7e54-780">Směrování atributů pomocí atributů http [příkaz]</span><span class="sxs-lookup"><span data-stu-id="e7e54-780">Attribute routing with Http[Verb] attributes</span></span>

<span data-ttu-id="e7e54-781">Směrování atributů může také používat atributy, jako je například `Http[Verb]` `HttpPostAttribute` .</span><span class="sxs-lookup"><span data-stu-id="e7e54-781">Attribute routing can also make use of the `Http[Verb]` attributes such as `HttpPostAttribute`.</span></span> <span data-ttu-id="e7e54-782">Všechny tyto atributy mohou přijmout šablonu směrování.</span><span class="sxs-lookup"><span data-stu-id="e7e54-782">All of these attributes can accept a route template.</span></span> <span data-ttu-id="e7e54-783">Tento příklad ukazuje dvě akce, které odpovídají stejné šabloně směrování:</span><span class="sxs-lookup"><span data-stu-id="e7e54-783">This example shows two actions that match the same route template:</span></span>

```csharp
[HttpGet("/products")]
public IActionResult ListProducts()
{
   // ...
}

[HttpPost("/products")]
public IActionResult CreateProduct(...)
{
   // ...
}
```

<span data-ttu-id="e7e54-784">V případě cesty URL, jako je akce, se spustí `/products` `ProductsApi.ListProducts` , když je příkaz http `GET` a spustí se, `ProductsApi.CreateProduct` když je příkaz http `POST` .</span><span class="sxs-lookup"><span data-stu-id="e7e54-784">For a URL path like `/products` the `ProductsApi.ListProducts` action will be executed when the HTTP verb is `GET` and `ProductsApi.CreateProduct` will be executed when the HTTP verb is `POST`.</span></span> <span data-ttu-id="e7e54-785">Směrování atributů první odpovídá adrese URL oproti sadě šablon směrování definovaných atributy směrování.</span><span class="sxs-lookup"><span data-stu-id="e7e54-785">Attribute routing first matches the URL against the set of route templates defined by route attributes.</span></span> <span data-ttu-id="e7e54-786">Po porovnání šablony trasy `IActionConstraint` se použijí omezení pro určení akcí, které lze provést.</span><span class="sxs-lookup"><span data-stu-id="e7e54-786">Once a route template matches, `IActionConstraint` constraints are applied to determine which actions can be executed.</span></span>

> [!TIP]
> <span data-ttu-id="e7e54-787">Při sestavování REST API je zřídka pravděpodobné, že budete chtít použít `[Route(...)]` metodu akce, protože akce bude přijímat všechny metody HTTP.</span><span class="sxs-lookup"><span data-stu-id="e7e54-787">When building a REST API, it's rare that you will want to use `[Route(...)]` on an action method as the action will accept all HTTP methods.</span></span> <span data-ttu-id="e7e54-788">Je lepší použít konkrétnější a `Http*Verb*Attributes` přesnější informace o tom, co vaše rozhraní API podporuje.</span><span class="sxs-lookup"><span data-stu-id="e7e54-788">It's better to use the more specific `Http*Verb*Attributes` to be precise about what your API supports.</span></span> <span data-ttu-id="e7e54-789">Očekává se, že klienti rozhraní REST API znají, které cesty a příkazy HTTP se mapují na konkrétní logické operace.</span><span class="sxs-lookup"><span data-stu-id="e7e54-789">Clients of REST APIs are expected to know what paths and HTTP verbs map to specific logical operations.</span></span>

<span data-ttu-id="e7e54-790">Vzhledem k tomu, že trasa atributu se vztahuje na konkrétní akci, je snadné vytvořit parametry požadované v rámci definice šablony trasy.</span><span class="sxs-lookup"><span data-stu-id="e7e54-790">Since an attribute route applies to a specific action, it's easy to make parameters required as part of the route template definition.</span></span> <span data-ttu-id="e7e54-791">V tomto příkladu `id` je vyžadována jako součást cesty URL.</span><span class="sxs-lookup"><span data-stu-id="e7e54-791">In this example, `id` is required as part of the URL path.</span></span>

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

<span data-ttu-id="e7e54-792">`ProductsApi.GetProduct(int)`Akce se spustí pro cestu URL, například `/products/3` ne pro cestu URL `/products` .</span><span class="sxs-lookup"><span data-stu-id="e7e54-792">The `ProductsApi.GetProduct(int)` action will be executed for a URL path like `/products/3` but not for a URL path like `/products`.</span></span> <span data-ttu-id="e7e54-793">Úplný popis šablon směrování a souvisejících možností najdete v tématu věnovaném [Směrování](xref:fundamentals/routing) .</span><span class="sxs-lookup"><span data-stu-id="e7e54-793">See [Routing](xref:fundamentals/routing) for a full description of route templates and related options.</span></span>

## <a name="route-name"></a><span data-ttu-id="e7e54-794">Název trasy</span><span class="sxs-lookup"><span data-stu-id="e7e54-794">Route Name</span></span>

<span data-ttu-id="e7e54-795">Následující kód definuje *název trasy* `Products_List` :</span><span class="sxs-lookup"><span data-stu-id="e7e54-795">The following code  defines a *route name* of `Products_List`:</span></span>

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

<span data-ttu-id="e7e54-796">Názvy tras se dají použít k vygenerování adresy URL na základě konkrétní trasy.</span><span class="sxs-lookup"><span data-stu-id="e7e54-796">Route names can be used to generate a URL based on a specific route.</span></span> <span data-ttu-id="e7e54-797">Názvy tras nemají žádný vliv na adresu URL, která odpovídá chování směrování a používají se jenom pro generování adresy URL.</span><span class="sxs-lookup"><span data-stu-id="e7e54-797">Route names have no impact on the URL matching behavior of routing and are only used for URL generation.</span></span> <span data-ttu-id="e7e54-798">Názvy tras musí být jedinečné aplikace v rozsahu.</span><span class="sxs-lookup"><span data-stu-id="e7e54-798">Route names must be unique application-wide.</span></span>

> [!NOTE]
> <span data-ttu-id="e7e54-799">Na rozdíl od konvenční *výchozí trasy*definuje `id` parametr jako volitelné ( `{id?}` ).</span><span class="sxs-lookup"><span data-stu-id="e7e54-799">Contrast this with the conventional *default route*, which defines the `id` parameter as optional (`{id?}`).</span></span> <span data-ttu-id="e7e54-800">Tato možnost přesného určení rozhraní API má výhody, jako je povolení `/products` a `/products/5` odeslání do různých akcí.</span><span class="sxs-lookup"><span data-stu-id="e7e54-800">This ability to precisely specify APIs has advantages, such as  allowing `/products` and `/products/5` to be dispatched to different actions.</span></span>

<a name="routing-combining-ref-label"></a>

### <a name="combining-routes"></a><span data-ttu-id="e7e54-801">Kombinování tras</span><span class="sxs-lookup"><span data-stu-id="e7e54-801">Combining routes</span></span>

<span data-ttu-id="e7e54-802">Aby bylo směrování atributů méně opakované, jsou atributy směrování na kontroleru kombinovány s atributy směrování na jednotlivých akcích.</span><span class="sxs-lookup"><span data-stu-id="e7e54-802">To make attribute routing less repetitive, route attributes on the controller are combined with route attributes on the individual actions.</span></span> <span data-ttu-id="e7e54-803">Každá šablona trasy definovaná na řadiči je předá směrování šablon na akcích.</span><span class="sxs-lookup"><span data-stu-id="e7e54-803">Any route templates defined on the controller are prepended to route templates on the actions.</span></span> <span data-ttu-id="e7e54-804">Umístěním atributu směrování na řadiči se vytvoří **všechny** akce v řadiči použít směrování atributů.</span><span class="sxs-lookup"><span data-stu-id="e7e54-804">Placing a route attribute on the controller makes **all** actions in the controller use attribute routing.</span></span>

```csharp
[Route("products")]
public class ProductsApiController : Controller
{
   [HttpGet]
   public IActionResult ListProducts() { ... }

   [HttpGet("{id}")]
   public ActionResult GetProduct(int id) { ... }
}
```

<span data-ttu-id="e7e54-805">V tomto příkladu se cesta URL `/products` může shodovat `ProductsApi.ListProducts` a cesta k adrese URL se `/products/5` může shodovat `ProductsApi.GetProduct(int)` .</span><span class="sxs-lookup"><span data-stu-id="e7e54-805">In this example the URL path `/products` can match `ProductsApi.ListProducts`, and the URL path `/products/5` can match `ProductsApi.GetProduct(int)`.</span></span> <span data-ttu-id="e7e54-806">Obě tyto akce odpovídají pouze HTTP, `GET` protože jsou označeny pomocí `HttpGetAttribute` .</span><span class="sxs-lookup"><span data-stu-id="e7e54-806">Both of these actions only match HTTP `GET` because they're marked with the `HttpGetAttribute`.</span></span>

<span data-ttu-id="e7e54-807">Šablony směrování použité pro akci, která začíná `/` nebo `~/` není kombinována s šablonami směrování použitými pro kontroler.</span><span class="sxs-lookup"><span data-stu-id="e7e54-807">Route templates applied to an action that begin with `/` or `~/` don't get combined with route templates applied to the controller.</span></span> <span data-ttu-id="e7e54-808">Tento příklad odpovídá sadě cest URL podobně jako *výchozí trasa*.</span><span class="sxs-lookup"><span data-stu-id="e7e54-808">This example matches a set of URL paths similar to the *default route*.</span></span>

```csharp
[Route("Home")]
public class HomeController : Controller
{
    [Route("")]      // Combines to define the route template "Home"
    [Route("Index")] // Combines to define the route template "Home/Index"
    [Route("/")]     // Doesn't combine, defines the route template ""
    public IActionResult Index()
    {
        ViewData["Message"] = "Home index";
        var url = Url.Action("Index", "Home");
        ViewData["Message"] = "Home index" + "var url = Url.Action; =  " + url;
        return View();
    }

    [Route("About")] // Combines to define the route template "Home/About"
    public IActionResult About()
    {
        return View();
    }   
}
```

<a name="routing-ordering-ref-label"></a>

### <a name="ordering-attribute-routes"></a><span data-ttu-id="e7e54-809">Směrování atributů řazení</span><span class="sxs-lookup"><span data-stu-id="e7e54-809">Ordering attribute routes</span></span>

<span data-ttu-id="e7e54-810">Na rozdíl od konvenčních tras, které se spouštějí v definovaném pořadí, směrování atributů vytvoří strom a vyhledá současně všechny trasy.</span><span class="sxs-lookup"><span data-stu-id="e7e54-810">In contrast to conventional routes, which execute in a defined order, attribute routing builds a tree and matches all routes simultaneously.</span></span> <span data-ttu-id="e7e54-811">To se chová jako – Pokud byly položky trasy umístěny v ideálním pořadí; nejvíce konkrétní trasy mají možnost provést před obecnější trasy.</span><span class="sxs-lookup"><span data-stu-id="e7e54-811">This behaves as-if the route entries were placed in an ideal ordering; the most specific routes have a chance to execute before the more general routes.</span></span>

<span data-ttu-id="e7e54-812">Například trasa, jako je konkrétnější, `blog/search/{topic}` než trasa, jako je třeba `blog/{*article}` .</span><span class="sxs-lookup"><span data-stu-id="e7e54-812">For example, a route like `blog/search/{topic}` is more specific than a route like `blog/{*article}`.</span></span> <span data-ttu-id="e7e54-813">Logicky mluví `blog/search/{topic}` nejprve Route "běh", protože se jedná o jediné řazení rozumné.</span><span class="sxs-lookup"><span data-stu-id="e7e54-813">Logically speaking the `blog/search/{topic}` route 'runs' first, by default, because that's the only sensible ordering.</span></span> <span data-ttu-id="e7e54-814">Pomocí konvenčního směrování zodpovídá vývojář za umístění tras v požadovaném pořadí.</span><span class="sxs-lookup"><span data-stu-id="e7e54-814">Using conventional routing, the developer is  responsible for placing routes in the desired order.</span></span>

<span data-ttu-id="e7e54-815">Trasy atributů mohou konfigurovat objednávku pomocí `Order` vlastnosti všech rozhraní, které poskytují atributy směrování.</span><span class="sxs-lookup"><span data-stu-id="e7e54-815">Attribute routes can configure an order, using the `Order` property of all of the framework provided route attributes.</span></span> <span data-ttu-id="e7e54-816">Trasy jsou zpracovávány podle vzestupného řazení `Order` Vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="e7e54-816">Routes are processed according to an ascending sort of the `Order` property.</span></span> <span data-ttu-id="e7e54-817">Výchozí pořadí je `0` .</span><span class="sxs-lookup"><span data-stu-id="e7e54-817">The default order is `0`.</span></span> <span data-ttu-id="e7e54-818">Nastavení trasy `Order = -1` , která se používá, se spustí před trasami, které nenastaví objednávku.</span><span class="sxs-lookup"><span data-stu-id="e7e54-818">Setting a route using `Order = -1` will run before routes that don't set an order.</span></span> <span data-ttu-id="e7e54-819">Nastavování trasy pomocí `Order = 1` se spustí po výchozím řazení směrování.</span><span class="sxs-lookup"><span data-stu-id="e7e54-819">Setting a route using `Order = 1` will run after default route ordering.</span></span>

> [!TIP]
> <span data-ttu-id="e7e54-820">Nepoužívejte v závislosti `Order` .</span><span class="sxs-lookup"><span data-stu-id="e7e54-820">Avoid depending on `Order`.</span></span> <span data-ttu-id="e7e54-821">Pokud vaše adresa URL vyžaduje explicitní směrování hodnot pořadí, je pravděpodobné, že budou i u klientů matoucí.</span><span class="sxs-lookup"><span data-stu-id="e7e54-821">If your URL-space requires explicit order values to route correctly, then it's likely confusing to clients as well.</span></span> <span data-ttu-id="e7e54-822">V části Obecné směrování atributů výběr správné trasy s odpovídající adresou URL.</span><span class="sxs-lookup"><span data-stu-id="e7e54-822">In general attribute routing will select the correct route with URL matching.</span></span> <span data-ttu-id="e7e54-823">Pokud výchozí pořadí použité pro generování adresy URL nefunguje, je použití názvu trasy jako přepsání obvykle jednodušší než použití `Order` Vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="e7e54-823">If the default order used for URL generation isn't working, using route name as an override is usually simpler than applying the `Order` property.</span></span>

Razor<span data-ttu-id="e7e54-824">Směrování stránek a směrování kontroléru MVC sdílí implementaci.</span><span class="sxs-lookup"><span data-stu-id="e7e54-824"> Pages routing and MVC controller routing share an implementation.</span></span> <span data-ttu-id="e7e54-825">Informace o pořadí směrování v Razor tématech stránky jsou k dispozici na [ Razor stránkách směrování a konvence aplikace: pořadí směrování](xref:razor-pages/razor-pages-conventions#route-order).</span><span class="sxs-lookup"><span data-stu-id="e7e54-825">Information on route order in the Razor Pages topics is available at [Razor Pages route and app conventions: Route order](xref:razor-pages/razor-pages-conventions#route-order).</span></span>

<a name="routing-token-replacement-templates-ref-label"></a>

## <a name="token-replacement-in-route-templates-controller-action-area"></a><span data-ttu-id="e7e54-826">Nahrazení tokenu v šablonách směrování ([Controller], [Action], [oblast])</span><span class="sxs-lookup"><span data-stu-id="e7e54-826">Token replacement in route templates ([controller], [action], [area])</span></span>

<span data-ttu-id="e7e54-827">Pro usnadnění pohodlí trasy atributů podporují *Nahrazení tokenu* uzavřením tokenu do hranatých závorek ( `[` , `]` ).</span><span class="sxs-lookup"><span data-stu-id="e7e54-827">For convenience, attribute routes support *token replacement* by enclosing a token in square-brackets (`[`, `]`).</span></span> <span data-ttu-id="e7e54-828">Tokeny `[action]` , `[area]` a `[controller]` jsou nahrazeny hodnotami názvu akce, názvu oblasti a názvu kontroleru z akce, kde je trasa definována.</span><span class="sxs-lookup"><span data-stu-id="e7e54-828">The tokens `[action]`, `[area]`, and `[controller]` are replaced with the values of the action name, area name, and controller name from the action where the route is defined.</span></span> <span data-ttu-id="e7e54-829">V následujícím příkladu akce odpovídají cestám adresy URL, jak je popsáno v komentářích:</span><span class="sxs-lookup"><span data-stu-id="e7e54-829">In the following example, the actions match URL paths as described in the comments:</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/ProductsController.cs?range=7-11,13-17,20-22)]

<span data-ttu-id="e7e54-830">Nahrazení tokenu probíhá jako poslední krok při vytváření tras atributů.</span><span class="sxs-lookup"><span data-stu-id="e7e54-830">Token replacement occurs as the last step of building the attribute routes.</span></span> <span data-ttu-id="e7e54-831">Výše uvedený příklad se chová stejně jako následující kód:</span><span class="sxs-lookup"><span data-stu-id="e7e54-831">The above example will behave the same as the following code:</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/ProductsController2.cs?range=7-11,13-17,20-22)]

<span data-ttu-id="e7e54-832">Trasy atributů je také možné kombinovat s dědičností.</span><span class="sxs-lookup"><span data-stu-id="e7e54-832">Attribute routes can also be combined with inheritance.</span></span> <span data-ttu-id="e7e54-833">To je zvlášť výkonné v kombinaci s náhradou tokenu.</span><span class="sxs-lookup"><span data-stu-id="e7e54-833">This is particularly powerful combined with token replacement.</span></span>

```csharp
[Route("api/[controller]")]
public abstract class MyBaseController : Controller { ... }

public class ProductsController : MyBaseController
{
   [HttpGet] // Matches '/api/Products'
   public IActionResult List() { ... }

   [HttpPut("{id}")] // Matches '/api/Products/{id}'
   public IActionResult Edit(int id) { ... }
}
```

<span data-ttu-id="e7e54-834">Nahrazení tokenu platí také pro názvy tras definované směrováními atributů.</span><span class="sxs-lookup"><span data-stu-id="e7e54-834">Token replacement also applies to route names defined by attribute routes.</span></span> <span data-ttu-id="e7e54-835">`[Route("[controller]/[action]", Name="[controller]_[action]")]`vygeneruje jedinečný název trasy pro každou akci.</span><span class="sxs-lookup"><span data-stu-id="e7e54-835">`[Route("[controller]/[action]", Name="[controller]_[action]")]` generates a unique route name for each action.</span></span>

<span data-ttu-id="e7e54-836">Chcete-li spárovat oddělovač nahrazení tokenu literálu `[` nebo `]` , zastavte ho opakováním znaku ( `[[` nebo `]]` ).</span><span class="sxs-lookup"><span data-stu-id="e7e54-836">To match the literal token replacement delimiter `[` or  `]`, escape it by repeating the character (`[[` or `]]`).</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<a name="routing-token-replacement-transformers-ref-label"></a>

### <a name="use-a-parameter-transformer-to-customize-token-replacement"></a><span data-ttu-id="e7e54-837">Použití transformátoru parametrů k přizpůsobení náhrady tokenu</span><span class="sxs-lookup"><span data-stu-id="e7e54-837">Use a parameter transformer to customize token replacement</span></span>

<span data-ttu-id="e7e54-838">Nahrazení tokenu se dá přizpůsobit pomocí transformátoru parametrů.</span><span class="sxs-lookup"><span data-stu-id="e7e54-838">Token replacement can be customized using a parameter transformer.</span></span> <span data-ttu-id="e7e54-839">Parametr Transformer implementuje `IOutboundParameterTransformer` a transformuje hodnotu parametrů.</span><span class="sxs-lookup"><span data-stu-id="e7e54-839">A parameter transformer implements `IOutboundParameterTransformer` and transforms the value of parameters.</span></span> <span data-ttu-id="e7e54-840">Například vlastní `SlugifyParameterTransformer` parametr Transformer změní `SubscriptionManagement` hodnotu Route na `subscription-management` .</span><span class="sxs-lookup"><span data-stu-id="e7e54-840">For example, a custom `SlugifyParameterTransformer` parameter transformer changes the `SubscriptionManagement` route value to `subscription-management`.</span></span>

<span data-ttu-id="e7e54-841">`RouteTokenTransformerConvention`Je konvence aplikačního modelu, která:</span><span class="sxs-lookup"><span data-stu-id="e7e54-841">The `RouteTokenTransformerConvention` is an application model convention that:</span></span>

* <span data-ttu-id="e7e54-842">Aplikuje transformátor parametrů na všechny trasy atributů v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="e7e54-842">Applies a parameter transformer to all attribute routes in an application.</span></span>
* <span data-ttu-id="e7e54-843">Přizpůsobí hodnoty tokenu trasy při jejich nahrazení.</span><span class="sxs-lookup"><span data-stu-id="e7e54-843">Customizes the attribute route token values as they are replaced.</span></span>

```csharp
public class SubscriptionManagementController : Controller
{
    [HttpGet("[controller]/[action]")] // Matches '/subscription-management/list-all'
    public IActionResult ListAll() { ... }
}
```

<span data-ttu-id="e7e54-844">`RouteTokenTransformerConvention`Je zaregistrován jako možnost v `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="e7e54-844">The `RouteTokenTransformerConvention` is registered as an option in `ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc(options =>
    {
        options.Conventions.Add(new RouteTokenTransformerConvention(
                                     new SlugifyParameterTransformer()));
    });
}

public class SlugifyParameterTransformer : IOutboundParameterTransformer
{
    public string TransformOutbound(object value)
    {
        if (value == null) { return null; }

        // Slugify value
        return Regex.Replace(value.ToString(), "([a-z])([A-Z])", "$1-$2").ToLower();
    }
}
```

::: moniker-end


::: moniker range="< aspnetcore-3.0"
<a name="routing-multiple-routes-ref-label"></a>

### <a name="multiple-routes"></a><span data-ttu-id="e7e54-845">Více tras</span><span class="sxs-lookup"><span data-stu-id="e7e54-845">Multiple Routes</span></span>

<span data-ttu-id="e7e54-846">Směrování atributů podporuje definování více tras, které dosáhnou stejné akce.</span><span class="sxs-lookup"><span data-stu-id="e7e54-846">Attribute routing supports defining multiple routes that reach the same action.</span></span> <span data-ttu-id="e7e54-847">Nejběžnějším využitím je napodobení chování *výchozí konvenční trasy* , jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="e7e54-847">The most common usage of this is to mimic the behavior of the *default conventional route* as shown in the following example:</span></span>

```csharp
[Route("[controller]")]
public class ProductsController : Controller
{
   [Route("")]     // Matches 'Products'
   [Route("Index")] // Matches 'Products/Index'
   public IActionResult Index()
}
```

<span data-ttu-id="e7e54-848">Vložení více atributů trasy na řadič znamená, že každá z nich bude kombinována s každým z atributů tras na metodách akcí.</span><span class="sxs-lookup"><span data-stu-id="e7e54-848">Putting multiple route attributes on the controller means that each one will combine with each of the route attributes on the action methods.</span></span>

```csharp
[Route("Store")]
[Route("[controller]")]
public class ProductsController : Controller
{
   [HttpPost("Buy")]     // Matches 'Products/Buy' and 'Store/Buy'
   [HttpPost("Checkout")] // Matches 'Products/Checkout' and 'Store/Checkout'
   public IActionResult Buy()
}
```

<span data-ttu-id="e7e54-849">Pokud jsou na akci umístěny různé atributy trasy (implementující `IActionConstraint` ), pak každé omezení akce kombinuje s šablonou směrování z atributu, který ji definoval.</span><span class="sxs-lookup"><span data-stu-id="e7e54-849">When multiple route attributes (that implement `IActionConstraint`) are placed on an action, then each action constraint combines with the route template from the attribute that defined it.</span></span>

```csharp
[Route("api/[controller]")]
public class ProductsController : Controller
{
   [HttpPut("Buy")]      // Matches PUT 'api/Products/Buy'
   [HttpPost("Checkout")] // Matches POST 'api/Products/Checkout'
   public IActionResult Buy()
}
```

> [!TIP]
> <span data-ttu-id="e7e54-850">Zatímco používání více tras na akcích může vypadat jako účinné, je lepší zachovat jednoduché a jasně definované místo URL vaší aplikace.</span><span class="sxs-lookup"><span data-stu-id="e7e54-850">While using multiple routes on actions can seem powerful, it's better to keep your application's URL space simple and well-defined.</span></span> <span data-ttu-id="e7e54-851">V případě potřeby použijte více tras na akcích, například pro podporu stávajících klientů.</span><span class="sxs-lookup"><span data-stu-id="e7e54-851">Use multiple routes on actions only where needed, for example to support existing clients.</span></span>

<a name="routing-attr-options"></a>

### <a name="specifying-attribute-route-optional-parameters-default-values-and-constraints"></a><span data-ttu-id="e7e54-852">Určení volitelných parametrů trasy atributu, výchozích hodnot a omezení</span><span class="sxs-lookup"><span data-stu-id="e7e54-852">Specifying attribute route optional parameters, default values, and constraints</span></span>

<span data-ttu-id="e7e54-853">Trasy atributů podporují stejnou vloženou syntaxi jako konvenční trasy, aby bylo možné zadat volitelné parametry, výchozí hodnoty a omezení.</span><span class="sxs-lookup"><span data-stu-id="e7e54-853">Attribute routes support the same inline syntax as conventional routes to specify optional parameters, default values, and constraints.</span></span>

```csharp
[HttpPost("product/{id:int}")]
public IActionResult ShowProduct(int id)
{
   // ...
}
```

<span data-ttu-id="e7e54-854">Podrobný popis syntaxe šablony směrování naleznete v tématu Referenční dokumentace k [šabloně směrování](xref:fundamentals/routing#route-template-reference) .</span><span class="sxs-lookup"><span data-stu-id="e7e54-854">See [Route Template Reference](xref:fundamentals/routing#route-template-reference) for a detailed description of route template syntax.</span></span>

<a name="routing-cust-rt-attr-irt-ref-label"></a>

### <a name="custom-route-attributes-using-iroutetemplateprovider"></a><span data-ttu-id="e7e54-855">Vlastní atributy směrování pomocí`IRouteTemplateProvider`</span><span class="sxs-lookup"><span data-stu-id="e7e54-855">Custom route attributes using `IRouteTemplateProvider`</span></span>

<span data-ttu-id="e7e54-856">Všechny atributy směrování, které jsou k dispozici v rozhraní ( `[Route(...)]` , `[HttpGet(...)]` atd.) implementují `IRouteTemplateProvider` rozhraní.</span><span class="sxs-lookup"><span data-stu-id="e7e54-856">All of the route attributes provided in the framework ( `[Route(...)]`, `[HttpGet(...)]` , etc.) implement the `IRouteTemplateProvider` interface.</span></span> <span data-ttu-id="e7e54-857">MVC vyhledává atributy tříd kontroleru a metody akcí při spuštění aplikace a používá ty, které implementují `IRouteTemplateProvider` k sestavení počáteční sady tras.</span><span class="sxs-lookup"><span data-stu-id="e7e54-857">MVC looks for attributes on controller classes and action methods when the app starts and uses the ones that implement `IRouteTemplateProvider` to build the initial set of routes.</span></span>

<span data-ttu-id="e7e54-858">Můžete implementovat `IRouteTemplateProvider` k definování vlastních atributů tras.</span><span class="sxs-lookup"><span data-stu-id="e7e54-858">You can implement `IRouteTemplateProvider` to define your own route attributes.</span></span> <span data-ttu-id="e7e54-859">Každá `IRouteTemplateProvider` z nich umožňuje definovat jednu trasu s vlastní šablonou směrování, objednávkou a názvem:</span><span class="sxs-lookup"><span data-stu-id="e7e54-859">Each `IRouteTemplateProvider` allows you to define a single route with a custom route template, order, and name:</span></span>

```csharp
public class MyApiControllerAttribute : Attribute, IRouteTemplateProvider
{
   public string Template => "api/[controller]";

   public int? Order { get; set; }

   public string Name { get; set; }
}
```

<span data-ttu-id="e7e54-860">Atribut z výše uvedeného příkladu automaticky nastaví na `Template` `"api/[controller]"` hodnotu when při `[MyApiController]` použití.</span><span class="sxs-lookup"><span data-stu-id="e7e54-860">The attribute from the above example automatically sets the `Template` to `"api/[controller]"` when `[MyApiController]` is applied.</span></span>

<a name="routing-app-model-ref-label"></a>

### <a name="using-application-model-to-customize-attribute-routes"></a><span data-ttu-id="e7e54-861">Přizpůsobení tras atributů pomocí aplikačního modelu</span><span class="sxs-lookup"><span data-stu-id="e7e54-861">Using Application Model to customize attribute routes</span></span>

<span data-ttu-id="e7e54-862">*Aplikační model* je objektový model vytvořený při spuštění se všemi metadaty, které MVC používá ke směrování a provádění akcí.</span><span class="sxs-lookup"><span data-stu-id="e7e54-862">The *application model* is an object model created at startup with all of the metadata used by MVC to route and execute your actions.</span></span> <span data-ttu-id="e7e54-863">*Aplikační model* zahrnuje všechna data získaná z atributů trasy (prostřednictvím `IRouteTemplateProvider` ).</span><span class="sxs-lookup"><span data-stu-id="e7e54-863">The *application model* includes all of the data gathered from route attributes (through `IRouteTemplateProvider`).</span></span> <span data-ttu-id="e7e54-864">Můžete napsat *konvence* pro úpravu aplikačního modelu v době spuštění, abyste mohli přizpůsobit způsob, jakým se směrování chová.</span><span class="sxs-lookup"><span data-stu-id="e7e54-864">You can write *conventions* to modify the application model at startup time to customize how routing behaves.</span></span> <span data-ttu-id="e7e54-865">V této části se dozvíte jednoduchý příklad přizpůsobení směrování pomocí aplikačního modelu.</span><span class="sxs-lookup"><span data-stu-id="e7e54-865">This section shows a simple example of customizing routing using application model.</span></span>

[!code-csharp[](routing/samples/2.x/main/NamespaceRoutingConvention.cs)]

<a name="routing-mixed-ref-label"></a>

## <a name="mixed-routing-attribute-routing-vs-conventional-routing"></a><span data-ttu-id="e7e54-866">Kombinované směrování: směrování atributů vs. konvenční směrování</span><span class="sxs-lookup"><span data-stu-id="e7e54-866">Mixed routing: Attribute routing vs conventional routing</span></span>

<span data-ttu-id="e7e54-867">Aplikace MVC můžou kombinovat použití konvenčního směrování a směrování atributů.</span><span class="sxs-lookup"><span data-stu-id="e7e54-867">MVC applications can mix the use of conventional routing and attribute routing.</span></span> <span data-ttu-id="e7e54-868">Pro řadiče, které obsluhují stránky HTML pro prohlížeče, a směrování atributů pro řadiče obsluhující rozhraní REST API se obvykle používá konvenční trasy.</span><span class="sxs-lookup"><span data-stu-id="e7e54-868">It's typical to use conventional routes for controllers serving HTML pages for browsers, and attribute routing for controllers serving REST APIs.</span></span>

<span data-ttu-id="e7e54-869">Akce jsou směrovány buď podle konvence nebo směrovaného atributu.</span><span class="sxs-lookup"><span data-stu-id="e7e54-869">Actions are either conventionally routed or attribute routed.</span></span> <span data-ttu-id="e7e54-870">Umístěním trasy do kontroleru nebo akce se nasměruje atribut IT.</span><span class="sxs-lookup"><span data-stu-id="e7e54-870">Placing a route on the controller or the action makes it attribute routed.</span></span> <span data-ttu-id="e7e54-871">Akce definující trasy atributů nemůžou být dostupné prostřednictvím konvenčních tras a naopak.</span><span class="sxs-lookup"><span data-stu-id="e7e54-871">Actions that define attribute routes cannot be reached through the conventional routes and vice-versa.</span></span> <span data-ttu-id="e7e54-872">**Libovolný** atribut směrování v řadiči provádí směrování všech akcí v atributu kontroleru.</span><span class="sxs-lookup"><span data-stu-id="e7e54-872">**Any** route attribute on the controller makes all actions in the controller attribute routed.</span></span>

> [!NOTE]
> <span data-ttu-id="e7e54-873">Způsob, jakým se rozlišují dva typy systémů směrování, je proces, který se použije po tom, co adresa URL odpovídá šabloně směrování.</span><span class="sxs-lookup"><span data-stu-id="e7e54-873">What distinguishes the two types of routing systems is the process applied after a URL matches a route template.</span></span> <span data-ttu-id="e7e54-874">V části konvenční směrování se při výběru akce a kontroleru z vyhledávací tabulky všech konvenčních akcí směrování používají hodnoty tras z shody.</span><span class="sxs-lookup"><span data-stu-id="e7e54-874">In conventional routing, the route values from the match are used to choose the action and controller from a lookup table of all conventional routed actions.</span></span> <span data-ttu-id="e7e54-875">V směrování atributů je Každá šablona již přidružena k akci a není nutné žádné další vyhledávání.</span><span class="sxs-lookup"><span data-stu-id="e7e54-875">In attribute routing, each template is already associated with an action, and no further lookup is needed.</span></span>

## <a name="complex-segments"></a><span data-ttu-id="e7e54-876">Komplexní segmenty</span><span class="sxs-lookup"><span data-stu-id="e7e54-876">Complex segments</span></span>

<span data-ttu-id="e7e54-877">Komplexní segmenty (například `[Route("/dog{token}cat")]` ) jsou zpracovávány porovnáním koncových literálů zprava doleva nehladým způsobem.</span><span class="sxs-lookup"><span data-stu-id="e7e54-877">Complex segments (for example, `[Route("/dog{token}cat")]`), are processed by matching up literals from right to left in a non-greedy way.</span></span> <span data-ttu-id="e7e54-878">Popis najdete [ve zdrojovém kódu](https://github.com/aspnet/Routing/blob/9cea167cfac36cf034dbb780e3f783114ef94780/src/Microsoft.AspNetCore.Routing/Patterns/RoutePatternMatcher.cs#L296) .</span><span class="sxs-lookup"><span data-stu-id="e7e54-878">See [the source code](https://github.com/aspnet/Routing/blob/9cea167cfac36cf034dbb780e3f783114ef94780/src/Microsoft.AspNetCore.Routing/Patterns/RoutePatternMatcher.cs#L296) for a description.</span></span> <span data-ttu-id="e7e54-879">Další informace najdete v [tomto problému](https://github.com/dotnet/AspNetCore.Docs/issues/8197).</span><span class="sxs-lookup"><span data-stu-id="e7e54-879">For more information, see [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/8197).</span></span>

<a name="routing-url-gen-ref-label"></a>

## <a name="url-generation"></a><span data-ttu-id="e7e54-880">Generování adresy URL</span><span class="sxs-lookup"><span data-stu-id="e7e54-880">URL Generation</span></span>

<span data-ttu-id="e7e54-881">Aplikace MVC můžou použít funkce generování adresy URL směrování k vygenerování odkazů na akce v adresách URL.</span><span class="sxs-lookup"><span data-stu-id="e7e54-881">MVC applications can use routing's URL generation features to generate URL links to actions.</span></span> <span data-ttu-id="e7e54-882">Generování adres URL eliminuje adresy URL zakódujeme, takže je váš kód robustnější a udržovatelný.</span><span class="sxs-lookup"><span data-stu-id="e7e54-882">Generating URLs eliminates hardcoding URLs, making your code more robust and maintainable.</span></span> <span data-ttu-id="e7e54-883">Tato část se zaměřuje na funkce generování adresy URL poskytované MVC a bude se týkat jenom základních informací o tom, jak generování adresy URL funguje.</span><span class="sxs-lookup"><span data-stu-id="e7e54-883">This section focuses on the URL generation features provided by MVC and will only cover basics of how URL generation works.</span></span> <span data-ttu-id="e7e54-884">Podrobný popis generování adresy URL najdete v tématu věnovaném [Směrování](xref:fundamentals/routing) .</span><span class="sxs-lookup"><span data-stu-id="e7e54-884">See [Routing](xref:fundamentals/routing) for a detailed description of URL generation.</span></span>

<span data-ttu-id="e7e54-885">`IUrlHelper`Rozhraní je základní částí infrastruktury mezi MVC a směrováním pro generování adresy URL.</span><span class="sxs-lookup"><span data-stu-id="e7e54-885">The `IUrlHelper` interface is the underlying piece of infrastructure between MVC and routing for URL generation.</span></span> <span data-ttu-id="e7e54-886">`IUrlHelper`K dispozici je instance dostupná prostřednictvím `Url` vlastnosti v části řadiče, zobrazení a zobrazení komponent.</span><span class="sxs-lookup"><span data-stu-id="e7e54-886">You'll find an instance of `IUrlHelper` available through the `Url` property in controllers, views, and view components.</span></span>

<span data-ttu-id="e7e54-887">V tomto příkladu se `IUrlHelper` rozhraní používá přes `Controller.Url` vlastnost k VYGENEROVÁNÍ adresy URL jiné akci.</span><span class="sxs-lookup"><span data-stu-id="e7e54-887">In this example, the `IUrlHelper` interface is used through the `Controller.Url` property to generate a URL to another action.</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationController.cs?name=snippet_1)]

<span data-ttu-id="e7e54-888">Pokud aplikace používá výchozí konvenční trasu, hodnota `url` proměnné bude řetězec cesty adresy URL `/UrlGeneration/Destination` .</span><span class="sxs-lookup"><span data-stu-id="e7e54-888">If the application is using the default conventional route, the value of the `url` variable will be the URL path string `/UrlGeneration/Destination`.</span></span> <span data-ttu-id="e7e54-889">Tato cesta URL se vytvoří směrováním kombinováním hodnot tras z aktuální žádosti (okolní hodnoty) s hodnotami předanými do `Url.Action` a nahrazením těchto hodnot do šablony trasy:</span><span class="sxs-lookup"><span data-stu-id="e7e54-889">This URL path is created by routing by combining the route values from the current request (ambient values), with the values passed to `Url.Action` and substituting those values into the route template:</span></span>

```
ambient values: { controller = "UrlGeneration", action = "Source" }
values passed to Url.Action: { controller = "UrlGeneration", action = "Destination" }
route template: {controller}/{action}/{id?}

result: /UrlGeneration/Destination
```

<span data-ttu-id="e7e54-890">Každý parametr trasy v šabloně směrování má jeho hodnotu nahrazenou odpovídajícími názvy hodnotami a okolními hodnotami.</span><span class="sxs-lookup"><span data-stu-id="e7e54-890">Each route parameter in the route template has its value substituted by matching names with the values and ambient values.</span></span> <span data-ttu-id="e7e54-891">Parametr trasy, který nemá hodnotu, může používat výchozí hodnotu, pokud má jednu, nebo může být vynechána, pokud je volitelná (jako v případě `id` v tomto příkladu).</span><span class="sxs-lookup"><span data-stu-id="e7e54-891">A route parameter that doesn't have a value can use a default value if it has one, or be skipped if it's optional (as in the case of `id` in this example).</span></span> <span data-ttu-id="e7e54-892">Generování adresy URL selže, pokud žádný požadovaný parametr trasy nemá odpovídající hodnotu.</span><span class="sxs-lookup"><span data-stu-id="e7e54-892">URL generation will fail if any required route parameter doesn't have a corresponding value.</span></span> <span data-ttu-id="e7e54-893">Pokud se generování adresy URL u trasy nezdařilo, bude se zkoušet další trasa, dokud nebudou všechny trasy vyzkoušeny nebo nalezena shoda.</span><span class="sxs-lookup"><span data-stu-id="e7e54-893">If URL generation fails for a route, the next route is tried until all routes have been tried or a match is found.</span></span>

<span data-ttu-id="e7e54-894">`Url.Action`Výše uvedený příklad předpokládá konvenční směrování, ale generování adresy URL funguje podobně jako při směrování atributů, i když se koncepty liší.</span><span class="sxs-lookup"><span data-stu-id="e7e54-894">The example of `Url.Action` above assumes conventional routing, but URL generation works similarly with attribute routing, though the concepts are different.</span></span> <span data-ttu-id="e7e54-895">Při použití konvenčního směrování se hodnoty tras používají k rozšíření šablony a hodnoty tras pro `controller` a `action` obvykle se v této šabloně zobrazují – to funguje, protože adresy URL, které odpovídají směrování, dodržují *konvence*.</span><span class="sxs-lookup"><span data-stu-id="e7e54-895">With conventional routing, the route values are used to expand a template, and the route values for `controller` and `action` usually appear in that template - this works because the URLs matched by routing adhere to a *convention*.</span></span> <span data-ttu-id="e7e54-896">V případě směrování atributů se v `controller` šabloně nemůžou zobrazovat hodnoty tras pro a, které se používají `action` k vyhledání šablony, která se má použít.</span><span class="sxs-lookup"><span data-stu-id="e7e54-896">In attribute routing, the route values for `controller` and `action` are not allowed to appear in the template - they're instead used to look up which template to use.</span></span>

<span data-ttu-id="e7e54-897">V tomto příkladu se používá směrování atributů:</span><span class="sxs-lookup"><span data-stu-id="e7e54-897">This example uses attribute routing:</span></span>

[!code-csharp[](routing/samples/2.x/main/StartupUseMvc.cs?name=snippet_1)]

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationControllerAttr.cs?name=snippet_1)]

<span data-ttu-id="e7e54-898">MVC sestaví vyhledávací tabulku všech akcí směrovaného atributu a bude odpovídat `controller` `action` hodnotám a vybrat šablonu směrování, která se má použít pro generování adresy URL.</span><span class="sxs-lookup"><span data-stu-id="e7e54-898">MVC builds a lookup table of all attribute routed actions and will match the `controller` and `action` values to select the route template to use for URL generation.</span></span> <span data-ttu-id="e7e54-899">V ukázce výše `custom/url/to/destination` je vygenerována.</span><span class="sxs-lookup"><span data-stu-id="e7e54-899">In the sample above,   `custom/url/to/destination` is generated.</span></span>

### <a name="generating-urls-by-action-name"></a><span data-ttu-id="e7e54-900">Generování adres URL podle názvu akce</span><span class="sxs-lookup"><span data-stu-id="e7e54-900">Generating URLs by action name</span></span>

<span data-ttu-id="e7e54-901">`Url.Action` (`IUrlHelper` .</span><span class="sxs-lookup"><span data-stu-id="e7e54-901">`Url.Action` (`IUrlHelper` .</span></span> <span data-ttu-id="e7e54-902">`Action`) a všechna související přetížení jsou založena na této myšlence, se kterou chcete určit, k čemu se odkazujete zadáním názvu kontroleru a názvu akce.</span><span class="sxs-lookup"><span data-stu-id="e7e54-902">`Action`) and all related overloads all are based on that idea that you want to specify what you're linking to by specifying a controller name and action name.</span></span>

> [!NOTE]
> <span data-ttu-id="e7e54-903">Při použití `Url.Action` aplikace jsou aktuální hodnoty trasy pro `controller` a `action` určeny pro vás – hodnota `controller` a `action` jsou součástí obou *okolních hodnot* **a** *hodnot*.</span><span class="sxs-lookup"><span data-stu-id="e7e54-903">When using `Url.Action`, the current route values for `controller` and `action` are specified for you - the value of `controller` and `action` are part of both *ambient values* **and** *values*.</span></span> <span data-ttu-id="e7e54-904">Metoda `Url.Action` vždy používá aktuální hodnoty `action` a `controller` a generuje cestu URL, která směruje na aktuální akci.</span><span class="sxs-lookup"><span data-stu-id="e7e54-904">The method `Url.Action`, always uses the current values of `action` and `controller` and will generate a URL path that routes to the current action.</span></span>

<span data-ttu-id="e7e54-905">Směrování se pokusí použít hodnoty v ambientních hodnotách k vyplnění informací, které jste nezadali při generování adresy URL.</span><span class="sxs-lookup"><span data-stu-id="e7e54-905">Routing attempts to use the values in ambient values to fill in information that you didn't provide when generating a URL.</span></span> <span data-ttu-id="e7e54-906">`{a}/{b}/{c}/{d}` `{ a = Alice, b = Bob, c = Carol, d = David }` Směrování má dostatek informací, aby vygenerovala adresu URL bez dalších hodnot, a to s využitím trasy jako a okolních hodnot – protože všechny parametry tras mají hodnotu.</span><span class="sxs-lookup"><span data-stu-id="e7e54-906">Using a route like `{a}/{b}/{c}/{d}` and ambient values `{ a = Alice, b = Bob, c = Carol, d = David }`, routing has enough information to generate a URL without any additional values - since all route parameters have a value.</span></span> <span data-ttu-id="e7e54-907">Pokud jste přidali hodnotu `{ d = Donovan }` , hodnota by byla `{ d = David }` ignorována a vygenerovaná cesta URL bude `Alice/Bob/Carol/Donovan` .</span><span class="sxs-lookup"><span data-stu-id="e7e54-907">If you added the value `{ d = Donovan }`, the value `{ d = David }` would be ignored, and the generated URL path would be `Alice/Bob/Carol/Donovan`.</span></span>

> [!WARNING]
> <span data-ttu-id="e7e54-908">Cesty URL jsou hierarchické.</span><span class="sxs-lookup"><span data-stu-id="e7e54-908">URL paths are hierarchical.</span></span> <span data-ttu-id="e7e54-909">Pokud jste v předchozím příkladu přidali hodnotu `{ c = Cheryl }` , obě hodnoty `{ c = Carol, d = David }` budou ignorovány.</span><span class="sxs-lookup"><span data-stu-id="e7e54-909">In the example above, if you added the value `{ c = Cheryl }`, both of the values `{ c = Carol, d = David }` would be ignored.</span></span> <span data-ttu-id="e7e54-910">V tomto případě už nemusíte mít hodnotu `d` a generování adresy URL selže.</span><span class="sxs-lookup"><span data-stu-id="e7e54-910">In this case we no longer have a value for `d` and URL generation will fail.</span></span> <span data-ttu-id="e7e54-911">Je nutné zadat požadovanou hodnotu `c` a `d` .</span><span class="sxs-lookup"><span data-stu-id="e7e54-911">You would need to specify the desired value of `c` and `d`.</span></span>  <span data-ttu-id="e7e54-912">Můžete očekávat, že se tento problém bude nacházet s výchozí trasou ( `{controller}/{action}/{id?}` ), ale v praxi se toto chování bude chovat zřídka, jako `Url.Action` vždycky explicitně zadáte `controller` `action` hodnotu a.</span><span class="sxs-lookup"><span data-stu-id="e7e54-912">You might expect to hit this problem with the default route (`{controller}/{action}/{id?}`) - but you will rarely encounter this behavior in practice as `Url.Action` will always explicitly specify a `controller` and `action` value.</span></span>

<span data-ttu-id="e7e54-913">Delší přetížení `Url.Action` také přijímají další objekt *hodnoty trasy* , který poskytuje hodnoty pro parametry směrování jiné než `controller` a `action` .</span><span class="sxs-lookup"><span data-stu-id="e7e54-913">Longer overloads of `Url.Action` also take an additional *route values* object to provide values for route parameters other than `controller` and `action`.</span></span> <span data-ttu-id="e7e54-914">Nejčastěji se to zobrazí s `id` podobným způsobem `Url.Action("Buy", "Products", new { id = 17 })` .</span><span class="sxs-lookup"><span data-stu-id="e7e54-914">You will most commonly see this used with `id` like `Url.Action("Buy", "Products", new { id = 17 })`.</span></span> <span data-ttu-id="e7e54-915">Podle konvence objekt *hodnoty trasy* je obvykle objektem anonymního typu, ale může to být také objekt typu " `IDictionary<>` nebo" v *podobě prostého starého objektu .NET*.</span><span class="sxs-lookup"><span data-stu-id="e7e54-915">By convention the *route values* object is usually an object of anonymous type, but it can also be an `IDictionary<>` or a *plain old .NET object*.</span></span> <span data-ttu-id="e7e54-916">Všechny další hodnoty tras, které neodpovídají parametrům směrování, jsou vloženy do řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="e7e54-916">Any additional route values that don't match route parameters are put in the query string.</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/TestController.cs)]

> [!TIP]
> <span data-ttu-id="e7e54-917">Chcete-li vytvořit absolutní adresu URL, použijte přetížení, které přijímá `protocol` :`Url.Action("Buy", "Products", new { id = 17 }, protocol: Request.Scheme)`</span><span class="sxs-lookup"><span data-stu-id="e7e54-917">To create an absolute URL, use an overload that accepts a `protocol`: `Url.Action("Buy", "Products", new { id = 17 }, protocol: Request.Scheme)`</span></span>

<a name="routing-gen-urls-route-ref-label"></a>

### <a name="generating-urls-by-route"></a><span data-ttu-id="e7e54-918">Generování adres URL podle směrování</span><span class="sxs-lookup"><span data-stu-id="e7e54-918">Generating URLs by route</span></span>

<span data-ttu-id="e7e54-919">Výše uvedený kód ukázal generování adresy URL předáním do kontroleru a názvu akce.</span><span class="sxs-lookup"><span data-stu-id="e7e54-919">The code above demonstrated generating a URL by passing in the controller and action name.</span></span> <span data-ttu-id="e7e54-920">`IUrlHelper`poskytuje také `Url.RouteUrl` rodinu metod.</span><span class="sxs-lookup"><span data-stu-id="e7e54-920">`IUrlHelper` also provides the `Url.RouteUrl` family of methods.</span></span> <span data-ttu-id="e7e54-921">Tyto metody jsou podobné `Url.Action` , ale nekopírují aktuální hodnoty `action` a `controller` do hodnot tras.</span><span class="sxs-lookup"><span data-stu-id="e7e54-921">These methods are similar to `Url.Action`, but they don't copy the current values of `action` and `controller` to the route values.</span></span> <span data-ttu-id="e7e54-922">Nejběžnějším využitím je zadání názvu trasy pro použití konkrétní trasy pro vygenerování adresy URL, a to obecně *bez* zadání kontroleru nebo názvu akce.</span><span class="sxs-lookup"><span data-stu-id="e7e54-922">The most common usage is to specify a route name to use a specific route to generate the URL, generally *without* specifying a controller or action name.</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationControllerRouting.cs?name=snippet_1)]

<a name="routing-gen-urls-html-ref-label"></a>

### <a name="generating-urls-in-html"></a><span data-ttu-id="e7e54-923">Generování adres URL v HTML</span><span class="sxs-lookup"><span data-stu-id="e7e54-923">Generating URLs in HTML</span></span>

<span data-ttu-id="e7e54-924">`IHtmlHelper`poskytuje `HtmlHelper` metody `Html.BeginForm` a `Html.ActionLink` ke generování `<form>` a `<a>` prvkům v uvedeném pořadí.</span><span class="sxs-lookup"><span data-stu-id="e7e54-924">`IHtmlHelper` provides the `HtmlHelper` methods `Html.BeginForm` and `Html.ActionLink` to generate `<form>` and `<a>` elements respectively.</span></span> <span data-ttu-id="e7e54-925">Tyto metody používají `Url.Action` metodu pro vygenerování adresy URL a přijímající podobné argumenty.</span><span class="sxs-lookup"><span data-stu-id="e7e54-925">These methods use the `Url.Action` method to generate a URL and they accept similar arguments.</span></span> <span data-ttu-id="e7e54-926">`Url.RouteUrl`Doprovodníci pro `HtmlHelper` jsou `Html.BeginRouteForm` a `Html.RouteLink` mají podobné funkce.</span><span class="sxs-lookup"><span data-stu-id="e7e54-926">The `Url.RouteUrl` companions for `HtmlHelper` are `Html.BeginRouteForm` and `Html.RouteLink` which have similar functionality.</span></span>

<span data-ttu-id="e7e54-927">TagHelpers generuje adresy URL prostřednictvím `form` taghelperu a `<a>` taghelperu.</span><span class="sxs-lookup"><span data-stu-id="e7e54-927">TagHelpers generate URLs through the `form` TagHelper and the `<a>` TagHelper.</span></span> <span data-ttu-id="e7e54-928">Oba tyto použití `IUrlHelper` při jejich implementaci.</span><span class="sxs-lookup"><span data-stu-id="e7e54-928">Both of these use `IUrlHelper` for their implementation.</span></span> <span data-ttu-id="e7e54-929">Další informace najdete v tématu [práce s formuláři](../views/working-with-forms.md) .</span><span class="sxs-lookup"><span data-stu-id="e7e54-929">See [Working with Forms](../views/working-with-forms.md) for more information.</span></span>

<span data-ttu-id="e7e54-930">Uvnitř zobrazení `IUrlHelper` je k dispozici prostřednictvím `Url` vlastnosti pro jakoukoli generaci adres URL ad hoc, která není pokrytá výše.</span><span class="sxs-lookup"><span data-stu-id="e7e54-930">Inside views, the `IUrlHelper` is available through the `Url` property for any ad-hoc URL generation not covered by the above.</span></span>

<a name="routing-gen-urls-action-ref-label"></a>

### <a name="generating-urls-in-action-results"></a><span data-ttu-id="e7e54-931">Generování adres URL ve výsledcích akcí</span><span class="sxs-lookup"><span data-stu-id="e7e54-931">Generating URLS in Action Results</span></span>

<span data-ttu-id="e7e54-932">Výše uvedené příklady ukazují použití `IUrlHelper` v kontroleru, zatímco nejběžnější využití v řadiči je vygenerování adresy URL jako součást výsledku akce.</span><span class="sxs-lookup"><span data-stu-id="e7e54-932">The examples above have shown using `IUrlHelper` in a controller, while the most common usage in a controller is to generate a URL as part of an action result.</span></span>

<span data-ttu-id="e7e54-933">`ControllerBase` `Controller` Základní třídy a poskytují pohodlný způsob pro výsledky akcí, které odkazují na jinou akci.</span><span class="sxs-lookup"><span data-stu-id="e7e54-933">The `ControllerBase` and `Controller` base classes provide convenience methods for action results that reference another action.</span></span> <span data-ttu-id="e7e54-934">Jedním z typických použití je přesměrování po přijetí vstupu uživatele.</span><span class="sxs-lookup"><span data-stu-id="e7e54-934">One typical usage is to redirect after accepting user input.</span></span>

```csharp
public IActionResult Edit(int id, Customer customer)
{
    if (ModelState.IsValid)
    {
        // Update DB with new details.
        return RedirectToAction("Index");
    }
    return View(customer);
}
```

<span data-ttu-id="e7e54-935">Metody pro vytváření výsledků pro zpracování akcí dodrží podobný vzor pro metody na `IUrlHelper` .</span><span class="sxs-lookup"><span data-stu-id="e7e54-935">The action results factory methods follow a similar pattern to the methods on `IUrlHelper`.</span></span>

<a name="routing-dedicated-ref-label"></a>

### <a name="special-case-for-dedicated-conventional-routes"></a><span data-ttu-id="e7e54-936">Zvláštní případ pro vyhrazené konvenční trasy</span><span class="sxs-lookup"><span data-stu-id="e7e54-936">Special case for dedicated conventional routes</span></span>

<span data-ttu-id="e7e54-937">Konvenční směrování může používat speciální druh definice trasy, která se označuje jako *vyhrazená konvenční trasa*.</span><span class="sxs-lookup"><span data-stu-id="e7e54-937">Conventional routing can use a special kind of route definition called a *dedicated conventional route*.</span></span> <span data-ttu-id="e7e54-938">V následujícím příkladu je trasa s názvem `blog` vyhrazená konvenční trasa.</span><span class="sxs-lookup"><span data-stu-id="e7e54-938">In the example below, the route named `blog` is a dedicated conventional route.</span></span>

```csharp
app.UseMvc(routes =>
{
    routes.MapRoute("blog", "blog/{*article}",
        defaults: new { controller = "Blog", action = "Article" });
    routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="e7e54-939">Pomocí těchto definic tras se `Url.Action("Index", "Home")` vygeneruje cesta URL `/` s `default` trasou, ale proč?</span><span class="sxs-lookup"><span data-stu-id="e7e54-939">Using these route definitions, `Url.Action("Index", "Home")` will generate the URL path `/` with the `default` route, but why?</span></span> <span data-ttu-id="e7e54-940">Můžete odhadnout, že hodnoty tras `{ controller = Home, action = Index }` by byly dostatečné pro vygenerování adresy URL pomocí `blog` a výsledek by byl `/blog?action=Index&controller=Home` .</span><span class="sxs-lookup"><span data-stu-id="e7e54-940">You might guess the route values `{ controller = Home, action = Index }` would be enough to generate a URL using `blog`, and the result would be `/blog?action=Index&controller=Home`.</span></span>

<span data-ttu-id="e7e54-941">Vyhrazené konvenční trasy spoléhají na speciální chování výchozích hodnot, které nemají odpovídající parametr trasy, který brání tomu, aby trasa byla "příliš hlade" s generováním adresy URL.</span><span class="sxs-lookup"><span data-stu-id="e7e54-941">Dedicated conventional routes rely on a special behavior of default values that don't have a corresponding route parameter that prevents the route from being "too greedy" with URL generation.</span></span> <span data-ttu-id="e7e54-942">V tomto případě výchozí hodnoty jsou `{ controller = Blog, action = Article }` a ani `controller` `action` se neobjeví jako parametr trasy.</span><span class="sxs-lookup"><span data-stu-id="e7e54-942">In this case the default values are `{ controller = Blog, action = Article }`, and neither `controller` nor `action` appears as a route parameter.</span></span> <span data-ttu-id="e7e54-943">Když směrování provádí generování adresy URL, zadané hodnoty musí odpovídat výchozím hodnotám.</span><span class="sxs-lookup"><span data-stu-id="e7e54-943">When routing performs URL generation, the values provided must match the default values.</span></span> <span data-ttu-id="e7e54-944">Generování adresy URL pomocí `blog` se nezdaří, protože se hodnoty `{ controller = Home, action = Index }` neshodují `{ controller = Blog, action = Article }` .</span><span class="sxs-lookup"><span data-stu-id="e7e54-944">URL generation using `blog` will fail because the values `{ controller = Home, action = Index }` don't match `{ controller = Blog, action = Article }`.</span></span> <span data-ttu-id="e7e54-945">Směrování pak vrátí zpět k akci `default` , která bude úspěšná.</span><span class="sxs-lookup"><span data-stu-id="e7e54-945">Routing then falls back to try `default`, which succeeds.</span></span>

<a name="routing-areas-ref-label"></a>

## <a name="areas"></a><span data-ttu-id="e7e54-946">Oblasti</span><span class="sxs-lookup"><span data-stu-id="e7e54-946">Areas</span></span>

<span data-ttu-id="e7e54-947">[Oblasti](areas.md) jsou funkce MVC, která slouží k uspořádání souvisejících funkcí do skupiny jako samostatného oboru názvů směrování (pro akce kontroleru) a struktury složek (pro zobrazení).</span><span class="sxs-lookup"><span data-stu-id="e7e54-947">[Areas](areas.md) are an MVC feature used to organize related functionality into a group as a separate routing-namespace (for controller actions) and folder structure (for views).</span></span> <span data-ttu-id="e7e54-948">Použití oblastí umožňuje aplikaci mít více řadičů se stejným názvem, pokud mají různé *oblasti*.</span><span class="sxs-lookup"><span data-stu-id="e7e54-948">Using areas allows an application to have multiple controllers with the same name - as long as they have different *areas*.</span></span> <span data-ttu-id="e7e54-949">Použití oblastí vytvoří hierarchii pro účely směrování přidáním dalšího parametru směrování `area` do `controller` a `action` .</span><span class="sxs-lookup"><span data-stu-id="e7e54-949">Using areas creates a hierarchy for the purpose of routing by adding another route parameter, `area` to `controller` and `action`.</span></span> <span data-ttu-id="e7e54-950">V této části se dozvíte, jak směrování komunikuje s oblastmi – viz [oblasti](areas.md) pro podrobnosti o použití oblastí se zobrazeními.</span><span class="sxs-lookup"><span data-stu-id="e7e54-950">This section will discuss how routing interacts with areas - see [Areas](areas.md) for details about how areas are used with views.</span></span>

<span data-ttu-id="e7e54-951">V následujícím příkladu je nakonfiguruje MVC na použití výchozí konvenční trasy a *trasy oblasti* pro oblast s názvem `Blog` :</span><span class="sxs-lookup"><span data-stu-id="e7e54-951">The following example configures MVC to use the default conventional route and an *area route* for an area named `Blog`:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet1)]

<span data-ttu-id="e7e54-952">Při porovnání cesty URL jako `/Manage/Users/AddUser` se v první trase vytvoří hodnoty trasy `{ area = Blog, controller = Users, action = AddUser }` .</span><span class="sxs-lookup"><span data-stu-id="e7e54-952">When matching a URL path like `/Manage/Users/AddUser`, the first route will produce the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span> <span data-ttu-id="e7e54-953">`area`Hodnota trasy je vytvořena výchozí hodnotou pro `area` , ve skutečnosti je trasa vytvořená pomocí `MapAreaRoute` následujícího ekvivalentu:</span><span class="sxs-lookup"><span data-stu-id="e7e54-953">The `area` route value is produced by a default value for `area`, in fact the route created by `MapAreaRoute` is equivalent to the following:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet2)]

<span data-ttu-id="e7e54-954">`MapAreaRoute`Vytvoří trasu s použitím výchozí hodnoty a omezení pro `area` použití poskytnutého názvu oblasti v tomto případě `Blog` .</span><span class="sxs-lookup"><span data-stu-id="e7e54-954">`MapAreaRoute` creates a route using both a default value and constraint for `area` using the provided area name, in this case `Blog`.</span></span> <span data-ttu-id="e7e54-955">Výchozí hodnota zajistí, že trasa vždy vytvoří `{ area = Blog, ... }` , omezení vyžaduje hodnotu `{ area = Blog, ... }` pro generování adresy URL.</span><span class="sxs-lookup"><span data-stu-id="e7e54-955">The default value ensures that the route always produces `{ area = Blog, ... }`, the constraint requires the value `{ area = Blog, ... }` for URL generation.</span></span>

> [!TIP]
> <span data-ttu-id="e7e54-956">Konvenční směrování je závislé na pořadí.</span><span class="sxs-lookup"><span data-stu-id="e7e54-956">Conventional routing is order-dependent.</span></span> <span data-ttu-id="e7e54-957">Obecně platí, že trasy s oblastmi by měly být umístěny dříve v tabulce směrování, protože jsou konkrétnější než trasy bez oblasti.</span><span class="sxs-lookup"><span data-stu-id="e7e54-957">In general, routes with areas should be placed earlier in the route table as they're more specific than routes without an area.</span></span>

<span data-ttu-id="e7e54-958">Pomocí výše uvedeného příkladu budou hodnoty směrování odpovídat následující akci:</span><span class="sxs-lookup"><span data-stu-id="e7e54-958">Using the above example, the route values would match the following action:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

<span data-ttu-id="e7e54-959">V této `AreaAttribute` části je uvedeno, že kontroler je v rámci oblasti, říkáme, že tento kontroler je v `Blog` oblasti.</span><span class="sxs-lookup"><span data-stu-id="e7e54-959">The `AreaAttribute` is what denotes a controller as part of an area, we say that this controller is in the `Blog` area.</span></span> <span data-ttu-id="e7e54-960">Řadiče bez `[Area]` atributu nejsou členy žádné oblasti a nebudou se shodovat s tím **not** , kdy `area` je hodnota směrování poskytována směrováním.</span><span class="sxs-lookup"><span data-stu-id="e7e54-960">Controllers without an `[Area]` attribute are not members of any area, and will **not** match when the `area` route value is provided by routing.</span></span> <span data-ttu-id="e7e54-961">V následujícím příkladu se může shodovat jenom první kontroler, který bude odpovídat hodnotám tras `{ area = Blog, controller = Users, action = AddUser }` .</span><span class="sxs-lookup"><span data-stu-id="e7e54-961">In the following example, only the first controller listed can match the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Zebra/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/UsersController.cs)]

> [!NOTE]
> <span data-ttu-id="e7e54-962">Obor názvů každého kontroleru se tady zobrazuje pro úplnost – jinak by řadiče vyžadovaly konflikt názvů a generovaly chybu kompilátoru.</span><span class="sxs-lookup"><span data-stu-id="e7e54-962">The namespace of each controller is shown here for completeness - otherwise the controllers would have a naming conflict and generate a compiler error.</span></span> <span data-ttu-id="e7e54-963">Obory názvů třídy nemají žádný vliv na směrování MVC.</span><span class="sxs-lookup"><span data-stu-id="e7e54-963">Class namespaces have no effect on MVC's routing.</span></span>

<span data-ttu-id="e7e54-964">První dva řadiče jsou členy oblastí a odpovídají pouze v případě, že je jejich název příslušné oblasti poskytovaný `area` hodnotou trasy.</span><span class="sxs-lookup"><span data-stu-id="e7e54-964">The first two controllers are members of areas, and only match when their respective area name is provided by the `area` route value.</span></span> <span data-ttu-id="e7e54-965">Třetí kontroler není členem žádné oblasti a může odpovídat jenom v případě, že `area` směrováním neposkytuje žádnou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="e7e54-965">The third controller isn't a member of any area, and can only match when no value for `area` is provided by routing.</span></span>

> [!NOTE]
> <span data-ttu-id="e7e54-966">V souvislosti s neshodnou *hodnotou* `area` je absence hodnoty stejná, jako kdyby hodnota `area` null nebo prázdný řetězec.</span><span class="sxs-lookup"><span data-stu-id="e7e54-966">In terms of matching *no value*, the absence of the `area` value is the same as if the value for `area` were null or the empty string.</span></span>

<span data-ttu-id="e7e54-967">Při provádění akce uvnitř oblasti bude hodnota trasy pro `area` směrování k dispozici jako *ambientní hodnota* pro směrování, která se má použít pro generování adresy URL.</span><span class="sxs-lookup"><span data-stu-id="e7e54-967">When executing an action inside an area, the route value for `area` will be available as an *ambient value* for routing to use for URL generation.</span></span> <span data-ttu-id="e7e54-968">To znamená, že ve výchozím nastavení jsou tyto oblasti pro generování adresy URL *rychlé* , jak je znázorněno v následující ukázce.</span><span class="sxs-lookup"><span data-stu-id="e7e54-968">This means that by default areas act *sticky* for URL generation as demonstrated by the following sample.</span></span>
[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet3)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Duck/Controllers/UsersController.cs)]

<a name="iactionconstraint-ref-label"></a>

## <a name="understanding-iactionconstraint"></a><span data-ttu-id="e7e54-969">Principy IActionConstraint</span><span class="sxs-lookup"><span data-stu-id="e7e54-969">Understanding IActionConstraint</span></span>

> [!NOTE]
> <span data-ttu-id="e7e54-970">Tato část je hlubokým podrobněem interních platforem a způsobu, jakým MVC vybírá akci, která se má provést.</span><span class="sxs-lookup"><span data-stu-id="e7e54-970">This section is a deep-dive on framework internals and how MVC chooses an action to execute.</span></span> <span data-ttu-id="e7e54-971">Typická aplikace nebude potřebovat vlastní.`IActionConstraint`</span><span class="sxs-lookup"><span data-stu-id="e7e54-971">A typical application won't need a custom `IActionConstraint`</span></span>

<span data-ttu-id="e7e54-972">Pravděpodobně jste již použili, `IActionConstraint` i když nejste obeznámeni s rozhraním.</span><span class="sxs-lookup"><span data-stu-id="e7e54-972">You have likely already used `IActionConstraint` even if you're not familiar with the interface.</span></span> <span data-ttu-id="e7e54-973">`[HttpGet]`Atribut a podobné `[Http-VERB]` atributy implementují `IActionConstraint` , aby bylo možné omezit provádění metody akce.</span><span class="sxs-lookup"><span data-stu-id="e7e54-973">The `[HttpGet]` Attribute and similar `[Http-VERB]` attributes implement `IActionConstraint` in order to limit the execution of an action method.</span></span>

```csharp
public class ProductsController : Controller
{
    [HttpGet]
    public IActionResult Edit() { }

    public IActionResult Edit(...) { }
}
```

<span data-ttu-id="e7e54-974">Za předpokladu, že výchozí konvenční trasa, cesta URL `/Products/Edit` by vytvořila hodnoty `{ controller = Products, action = Edit }` , které by odpovídaly **oběma** akcím, které jsou zde uvedeny.</span><span class="sxs-lookup"><span data-stu-id="e7e54-974">Assuming the default conventional route, the URL path `/Products/Edit` would produce the values `{ controller = Products, action = Edit }`, which would match **both** of the actions shown here.</span></span> <span data-ttu-id="e7e54-975">V `IActionConstraint` terminologii bychom znamenali, že obě tyto akce se považují za kandidáty, protože obě odpovídají datům směrování.</span><span class="sxs-lookup"><span data-stu-id="e7e54-975">In `IActionConstraint` terminology we would say that both of these actions are considered candidates - as they both match the route data.</span></span>

<span data-ttu-id="e7e54-976">Když se `HttpGetAttribute` spustí, znamená to, že *Edit ()* je odpovídající pro *Get* a neshoduje se s žádným jiným příkazem http.</span><span class="sxs-lookup"><span data-stu-id="e7e54-976">When the `HttpGetAttribute` executes, it will say that *Edit()* is a match for *GET* and isn't a match for any other HTTP verb.</span></span> <span data-ttu-id="e7e54-977">`Edit(...)`Akce nemá definovány žádné omezení, a proto bude odpovídat jakémukoli příkazu http.</span><span class="sxs-lookup"><span data-stu-id="e7e54-977">The `Edit(...)` action doesn't have any constraints defined, and so will match any HTTP verb.</span></span> <span data-ttu-id="e7e54-978">Proto za předpokladu, že `POST` `Edit(...)` odpovídají pouze.</span><span class="sxs-lookup"><span data-stu-id="e7e54-978">So assuming a `POST` - only `Edit(...)` matches.</span></span> <span data-ttu-id="e7e54-979">V případě, že `GET` obě akce stále můžou souhlasit, ale akce s `IActionConstraint` je vždy považována za *lepší* , než je akce bez.</span><span class="sxs-lookup"><span data-stu-id="e7e54-979">But, for a `GET` both actions can still match - however, an action with an `IActionConstraint` is always considered *better* than an action without.</span></span> <span data-ttu-id="e7e54-980">Proto je to proto, že se `Edit()` `[HttpGet]` považuje za konkrétnější a vybere se, pokud se obě akce můžou shodovat.</span><span class="sxs-lookup"><span data-stu-id="e7e54-980">So because `Edit()` has `[HttpGet]` it's considered more specific, and will be selected if both actions can match.</span></span>

<span data-ttu-id="e7e54-981">Koncepčně `IActionConstraint` je forma *přetížení*, ale místo přetížení metod se stejným názvem, je přetížení mezi akcemi, které odpovídají stejné adrese URL.</span><span class="sxs-lookup"><span data-stu-id="e7e54-981">Conceptually, `IActionConstraint` is a form of *overloading*, but instead of overloading methods with the same name, it's overloading between actions that match the same URL.</span></span> <span data-ttu-id="e7e54-982">Směrování atributů používá také `IActionConstraint` a může mít za následek, že se z různých řadičů považují za kandidáty.</span><span class="sxs-lookup"><span data-stu-id="e7e54-982">Attribute routing also uses `IActionConstraint` and can result in actions from different controllers both being considered candidates.</span></span>

<a name="iactionconstraint-impl-ref-label"></a>

### <a name="implementing-iactionconstraint"></a><span data-ttu-id="e7e54-983">Implementace IActionConstraint</span><span class="sxs-lookup"><span data-stu-id="e7e54-983">Implementing IActionConstraint</span></span>

<span data-ttu-id="e7e54-984">Nejjednodušší způsob, jak implementovat, `IActionConstraint` je vytvořit třídu odvozenou z `System.Attribute` a umístit ji na vaše akce a řadiče.</span><span class="sxs-lookup"><span data-stu-id="e7e54-984">The simplest way to implement an `IActionConstraint` is to create a class derived from `System.Attribute` and place it on your actions and controllers.</span></span> <span data-ttu-id="e7e54-985">MVC bude automaticky zjišťovat všechny `IActionConstraint` , které jsou aplikovány jako atributy.</span><span class="sxs-lookup"><span data-stu-id="e7e54-985">MVC will automatically discover any `IActionConstraint` that are applied as attributes.</span></span> <span data-ttu-id="e7e54-986">Pomocí modelu aplikace můžete použít omezení, což je pravděpodobně nejpružnější přístup, protože umožňuje Metaprogram způsob jejich použití.</span><span class="sxs-lookup"><span data-stu-id="e7e54-986">You can use the application model to apply constraints, and this is probably the most flexible approach as it allows you to metaprogram how they're applied.</span></span>

<span data-ttu-id="e7e54-987">V následujícím příkladu omezení zvolí akci na základě *kódu země* z údajů o trasách.</span><span class="sxs-lookup"><span data-stu-id="e7e54-987">In the following example, a constraint chooses an action based on a *country code* from the route data.</span></span> <span data-ttu-id="e7e54-988">[Úplná ukázka na GitHubu](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.ActionConstraintSample.Web/CountrySpecificAttribute.cs).</span><span class="sxs-lookup"><span data-stu-id="e7e54-988">The [full sample on GitHub](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.ActionConstraintSample.Web/CountrySpecificAttribute.cs).</span></span>

```csharp
public class CountrySpecificAttribute : Attribute, IActionConstraint
{
    private readonly string _countryCode;

    public CountrySpecificAttribute(string countryCode)
    {
        _countryCode = countryCode;
    }

    public int Order
    {
        get
        {
            return 0;
        }
    }

    public bool Accept(ActionConstraintContext context)
    {
        return string.Equals(
            context.RouteContext.RouteData.Values["country"].ToString(),
            _countryCode,
            StringComparison.OrdinalIgnoreCase);
    }
}
```

<span data-ttu-id="e7e54-989">Zodpovídáte za implementaci `Accept` metody a výběru možnosti Order, aby bylo omezení spuštěno.</span><span class="sxs-lookup"><span data-stu-id="e7e54-989">You are responsible for implementing the `Accept` method and choosing an 'Order' for the constraint to execute.</span></span> <span data-ttu-id="e7e54-990">V tomto případě se `Accept` Metoda vrátí `true` k označení, že akce odpovídá `country` hodnotě trasy.</span><span class="sxs-lookup"><span data-stu-id="e7e54-990">In this case, the `Accept` method returns `true` to denote the action is a match when the `country` route value matches.</span></span> <span data-ttu-id="e7e54-991">To se liší od a `RouteValueAttribute` v tom, že umožňuje přechod na akci bez atributu.</span><span class="sxs-lookup"><span data-stu-id="e7e54-991">This is different from a `RouteValueAttribute` in that it allows fallback to a non-attributed action.</span></span> <span data-ttu-id="e7e54-992">Ukázka ukazuje, že pokud zadáte `en-US` akci, směrové číslo země jako `fr-FR` se vrátí k obecnější kontroleru, který se `[CountrySpecific(...)]` nepoužívá.</span><span class="sxs-lookup"><span data-stu-id="e7e54-992">The sample shows that if you define an `en-US` action then a country code like `fr-FR` will fall back to a more generic controller that doesn't have `[CountrySpecific(...)]` applied.</span></span>

<span data-ttu-id="e7e54-993">`Order`Vlastnost určuje, ve které *fázi* je omezení součástí.</span><span class="sxs-lookup"><span data-stu-id="e7e54-993">The `Order` property decides which *stage* the constraint is part of.</span></span> <span data-ttu-id="e7e54-994">Omezení akcí se spouští ve skupinách na základě `Order` .</span><span class="sxs-lookup"><span data-stu-id="e7e54-994">Action constraints run in groups based on the `Order`.</span></span> <span data-ttu-id="e7e54-995">Například všechny atributy, které poskytuje rozhraní HTTP, používají stejnou `Order` hodnotu tak, aby běžely ve stejné fázi.</span><span class="sxs-lookup"><span data-stu-id="e7e54-995">For example, all of the framework provided HTTP method attributes use the same `Order` value so that they run in the same stage.</span></span> <span data-ttu-id="e7e54-996">Můžete mít tolik fází, kolik potřebujete k implementaci požadovaných zásad.</span><span class="sxs-lookup"><span data-stu-id="e7e54-996">You can have as many stages as you need to implement your desired policies.</span></span>

> [!TIP]
> <span data-ttu-id="e7e54-997">Chcete-li se rozhodnout o hodnotě pro `Order` zamyslení nad tím, zda by mělo být vaše omezení použito před metodami http.</span><span class="sxs-lookup"><span data-stu-id="e7e54-997">To decide on a value for `Order` think about whether or not your constraint should be applied before HTTP methods.</span></span> <span data-ttu-id="e7e54-998">Nižší čísla se spouštějí jako první.</span><span class="sxs-lookup"><span data-stu-id="e7e54-998">Lower numbers run first.</span></span>

::: moniker-end
