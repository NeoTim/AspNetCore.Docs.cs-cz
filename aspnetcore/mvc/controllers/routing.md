---
title: Směrování na akce kontroleru v ASP.NET Core
author: rick-anderson
description: Přečtěte si, jak ASP.NET Core MVC používá middleware směrování k párování adres URL příchozích požadavků a jejich mapování na akce.
ms.author: riande
ms.date: 3/25/2020
uid: mvc/controllers/routing
ms.openlocfilehash: c1c0d978714718af1de0f627e50a54f66ed391ed
ms.sourcegitcommit: 4b166b49ec557a03f99f872dd069ca5e56faa524
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80362658"
---
# <a name="routing-to-controller-actions-in-aspnet-core"></a><span data-ttu-id="2517c-103">Směrování na akce kontroleru v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2517c-103">Routing to controller actions in ASP.NET Core</span></span>

<span data-ttu-id="2517c-104">Služba [Ryan Nowak](https://github.com/rynowak), [Kirka Larkin](https://twitter.com/serpent5)a [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="2517c-104">By [Ryan Nowak](https://github.com/rynowak), [Kirk Larkin](https://twitter.com/serpent5), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="2517c-105">Řadiče ASP.NET Core používají [middleware](xref:fundamentals/middleware/index) směrování ke spárování adres URL příchozích požadavků a jejich mapování na [Akce](#action).</span><span class="sxs-lookup"><span data-stu-id="2517c-105">ASP.NET Core controllers use the Routing [middleware](xref:fundamentals/middleware/index) to match the URLs of incoming requests and map them to [actions](#action).</span></span>  <span data-ttu-id="2517c-106">Šablony tras:</span><span class="sxs-lookup"><span data-stu-id="2517c-106">Routes templates:</span></span>

* <span data-ttu-id="2517c-107">Jsou definovány ve spouštěcím kódu nebo atributech.</span><span class="sxs-lookup"><span data-stu-id="2517c-107">Are defined in startup code or attributes.</span></span>
* <span data-ttu-id="2517c-108">Popisuje, jak se shodují cesty URL k [akcím](#action).</span><span class="sxs-lookup"><span data-stu-id="2517c-108">Describe how URL paths are matched to [actions](#action).</span></span>
* <span data-ttu-id="2517c-109">Slouží ke generování adres URL pro odkazy.</span><span class="sxs-lookup"><span data-stu-id="2517c-109">Are used to generate URLs for links.</span></span> <span data-ttu-id="2517c-110">Vygenerované odkazy jsou obvykle vraceny v odpovědích.</span><span class="sxs-lookup"><span data-stu-id="2517c-110">The generated links are typically returned in responses.</span></span>

<span data-ttu-id="2517c-111">Akce jsou směrovány buď podle [konvence](#cr) , nebo podle [atributů](#ar).</span><span class="sxs-lookup"><span data-stu-id="2517c-111">Actions are either [conventionally-routed](#cr) or [attribute-routed](#ar).</span></span> <span data-ttu-id="2517c-112">Umístěním trasy do kontroleru nebo [Akce](#action) se nastaví směrování atributů.</span><span class="sxs-lookup"><span data-stu-id="2517c-112">Placing a route on the controller or [action](#action) makes it attribute-routed.</span></span> <span data-ttu-id="2517c-113">Další informace najdete v tématu [smíšená směrování](#routing-mixed-ref-label) .</span><span class="sxs-lookup"><span data-stu-id="2517c-113">See [Mixed routing](#routing-mixed-ref-label) for more information.</span></span>

<span data-ttu-id="2517c-114">Tento dokument:</span><span class="sxs-lookup"><span data-stu-id="2517c-114">This document:</span></span>

* <span data-ttu-id="2517c-115">Vysvětluje interakce mezi MVC a směrováním:</span><span class="sxs-lookup"><span data-stu-id="2517c-115">Explains the interactions between MVC and routing:</span></span>
  * <span data-ttu-id="2517c-116">Jak typické aplikace MVC využívají funkce směrování.</span><span class="sxs-lookup"><span data-stu-id="2517c-116">How typical MVC apps make use of routing features.</span></span>
  * <span data-ttu-id="2517c-117">Pokrývá obojí:</span><span class="sxs-lookup"><span data-stu-id="2517c-117">Covers both:</span></span>
    * <span data-ttu-id="2517c-118">[Směrování](#cr) se obvykle používá s řadiči a zobrazeními.</span><span class="sxs-lookup"><span data-stu-id="2517c-118">[Conventionally routing](#cr) typically used with controllers and views.</span></span>
    * <span data-ttu-id="2517c-119">*Směrování atributů* používané s rozhraními REST API.</span><span class="sxs-lookup"><span data-stu-id="2517c-119">*Attribute routing* used with REST APIs.</span></span> <span data-ttu-id="2517c-120">Pokud se primárně zajímáte o směrování pro rozhraní REST API, přejděte na oddíl [Směrování atributů pro rozhraní REST API](#ar) .</span><span class="sxs-lookup"><span data-stu-id="2517c-120">If you're primarily interested in routing for REST APIs, jump to the [Attribute routing for REST APIs](#ar) section.</span></span>
  * <span data-ttu-id="2517c-121">Podrobnosti o rozšířeném směrování najdete v tématu [Směrování](xref:fundamentals/routing) .</span><span class="sxs-lookup"><span data-stu-id="2517c-121">See [Routing](xref:fundamentals/routing) for advanced routing details.</span></span>
* <span data-ttu-id="2517c-122">Odkazuje na výchozí systém směrování přidaný v ASP.NET Core 3,0, který se označuje jako směrování koncového bodu.</span><span class="sxs-lookup"><span data-stu-id="2517c-122">Refers to the default routing system added in ASP.NET Core 3.0, called endpoint routing.</span></span> <span data-ttu-id="2517c-123">Pro účely kompatibility je možné použít řadiče s předchozí verzí směrování.</span><span class="sxs-lookup"><span data-stu-id="2517c-123">It's possible to use controllers with the previous version of routing for compatibility purposes.</span></span> <span data-ttu-id="2517c-124">Pokyny najdete v [příručce k migraci 2.2 – 3.0](xref:migration/22-to-30) .</span><span class="sxs-lookup"><span data-stu-id="2517c-124">See the [2.2-3.0 migration guide](xref:migration/22-to-30) for instructions.</span></span> <span data-ttu-id="2517c-125">Referenční materiály ke staršímu systému směrování najdete v [tomto dokumentu ve verzi 2,2](xref:mvc/controllers/routing?view=aspnetcore-2.2) .</span><span class="sxs-lookup"><span data-stu-id="2517c-125">Refer to the [2.2 version of this document](xref:mvc/controllers/routing?view=aspnetcore-2.2) for reference material on the legacy routing system.</span></span>

<a name="cr"></a>

## <a name="set-up-conventional-route"></a><span data-ttu-id="2517c-126">Nastavení konvenční trasy</span><span class="sxs-lookup"><span data-stu-id="2517c-126">Set up conventional route</span></span>

<span data-ttu-id="2517c-127">`Startup.Configure` obvykle má při použití [konvenčního směrování](#crd)podobný kód jako v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="2517c-127">`Startup.Configure` typically has code similar to the following when using [conventional routing](#crd):</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupDefaultMVC.cs?name=snippet)]

<span data-ttu-id="2517c-128">Uvnitř volání <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*>se <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> používá k vytvoření jedné trasy.</span><span class="sxs-lookup"><span data-stu-id="2517c-128">Inside the call to <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*>, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> is used to create a single route.</span></span> <span data-ttu-id="2517c-129">Jedna trasa má název `default` Route.</span><span class="sxs-lookup"><span data-stu-id="2517c-129">The single route is named `default` route.</span></span> <span data-ttu-id="2517c-130">Většina aplikací s řadiči a zobrazeními používá šablonu směrování podobnou trase `default`.</span><span class="sxs-lookup"><span data-stu-id="2517c-130">Most apps with controllers and views use a route template similar to the `default` route.</span></span> <span data-ttu-id="2517c-131">Rozhraní REST API by měly používat [Směrování atributů](#ar).</span><span class="sxs-lookup"><span data-stu-id="2517c-131">REST APIs should use [attribute routing](#ar).</span></span>

<span data-ttu-id="2517c-132">`"{controller=Home}/{action=Index}/{id?}"`šablony trasy:</span><span class="sxs-lookup"><span data-stu-id="2517c-132">The route template `"{controller=Home}/{action=Index}/{id?}"`:</span></span>

* <span data-ttu-id="2517c-133">Odpovídá cestě URL, jako je `/Products/Details/5`</span><span class="sxs-lookup"><span data-stu-id="2517c-133">Matches a URL path like `/Products/Details/5`</span></span>
* <span data-ttu-id="2517c-134">Extrahuje hodnoty tras `{ controller = Products, action = Details, id = 5 }` cestou tokenizací.</span><span class="sxs-lookup"><span data-stu-id="2517c-134">Extracts the route values `{ controller = Products, action = Details, id = 5 }` by tokenizing the path.</span></span> <span data-ttu-id="2517c-135">Výsledkem extrakce hodnot tras je shoda, pokud má aplikace kontroler s názvem `ProductsController` a akci `Details`:</span><span class="sxs-lookup"><span data-stu-id="2517c-135">The extraction of route values results in a match if the app has a controller named `ProductsController` and a `Details` action:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippetA)]

 <span data-ttu-id="2517c-136">Metoda [MyDisplayRouteInfo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x/main/Extensions/ControllerContextExtensions.cs) je obsažena v [ukázce stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) a slouží k zobrazení informací o směrování.</span><span class="sxs-lookup"><span data-stu-id="2517c-136">The [MyDisplayRouteInfo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x/main/Extensions/ControllerContextExtensions.cs) method is included in the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) and is used to display routing information.</span></span>

  * <span data-ttu-id="2517c-137">`/Products/Details/5` model váže hodnotu `id = 5` a nastavte parametr `id` na `5`.</span><span class="sxs-lookup"><span data-stu-id="2517c-137">`/Products/Details/5` model binds the value of `id = 5` to set the `id` parameter to `5`.</span></span> <span data-ttu-id="2517c-138">Další podrobnosti najdete v tématu [vazba modelu](xref:mvc/models/model-binding) .</span><span class="sxs-lookup"><span data-stu-id="2517c-138">See [Model Binding](xref:mvc/models/model-binding) for more details.</span></span>
* <span data-ttu-id="2517c-139">`{controller=Home}` definuje jako výchozí `controller``Home`.</span><span class="sxs-lookup"><span data-stu-id="2517c-139">`{controller=Home}` defines `Home` as the default `controller`.</span></span>
* <span data-ttu-id="2517c-140">`{action=Index}` definuje jako výchozí `action``Index`.</span><span class="sxs-lookup"><span data-stu-id="2517c-140">`{action=Index}` defines `Index` as the default `action`.</span></span>
*  <span data-ttu-id="2517c-141">`?` znak v `{id?}` definuje `id` jako volitelné.</span><span class="sxs-lookup"><span data-stu-id="2517c-141">The `?` character in `{id?}` defines `id` as optional.</span></span>
  * <span data-ttu-id="2517c-142">Výchozí a volitelné parametry směrování nemusejí být v cestě URL pro porovnávání k dispozici.</span><span class="sxs-lookup"><span data-stu-id="2517c-142">Default and optional route parameters don't need to be present in the URL path for a match.</span></span> <span data-ttu-id="2517c-143">Podrobný popis syntaxe šablony směrování naleznete v tématu Referenční dokumentace k [šabloně směrování](xref:fundamentals/routing#route-template-reference) .</span><span class="sxs-lookup"><span data-stu-id="2517c-143">See [Route Template Reference](xref:fundamentals/routing#route-template-reference) for a detailed description of route template syntax.</span></span>
* <span data-ttu-id="2517c-144">Odpovídá cestě URL `/`.</span><span class="sxs-lookup"><span data-stu-id="2517c-144">Matches the URL path `/`.</span></span>
* <span data-ttu-id="2517c-145">Vytvoří hodnoty trasy `{ controller = Home, action = Index }`.</span><span class="sxs-lookup"><span data-stu-id="2517c-145">Produces the route values `{ controller = Home, action = Index }`.</span></span>
* <span data-ttu-id="2517c-146">Metoda [MyDisplayRouteInfo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x/main/Extensions/ControllerContextExtensions.cs) je obsažena v [ukázce stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) a slouží k zobrazení informací o směrování.</span><span class="sxs-lookup"><span data-stu-id="2517c-146">The [MyDisplayRouteInfo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x/main/Extensions/ControllerContextExtensions.cs) method is included in the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) and is used to display routing information.</span></span>

<span data-ttu-id="2517c-147">Hodnoty pro `controller` a `action` využívají výchozí hodnoty.</span><span class="sxs-lookup"><span data-stu-id="2517c-147">The values for `controller` and `action` make use of the default values.</span></span> <span data-ttu-id="2517c-148">`id` nevytváří hodnotu, protože v cestě URL není žádný odpovídající segment.</span><span class="sxs-lookup"><span data-stu-id="2517c-148">`id` doesn't produce a value since there's no corresponding segment in the URL path.</span></span> <span data-ttu-id="2517c-149">`/` odpovídá pouze v případě, že existuje `HomeController` a akce `Index`:</span><span class="sxs-lookup"><span data-stu-id="2517c-149">`/` only matches if there exists a `HomeController` and `Index` action:</span></span>

```csharp
public class HomeController : Controller
{
  public IActionResult Index() { ... }
}
```

<span data-ttu-id="2517c-150">Pomocí předchozí definice kontroleru a šablony trasy se spustí akce `HomeController.Index` pro následující cesty URL:</span><span class="sxs-lookup"><span data-stu-id="2517c-150">Using the preceding controller definition and route template, the `HomeController.Index` action is run for the following URL paths:</span></span>

* `/Home/Index/17`
* `/Home/Index`
* `/Home`
* `/`

<span data-ttu-id="2517c-151">Cesta URL `/` používá výchozí `Home` řadičů šablony směrování a `Index` akci.</span><span class="sxs-lookup"><span data-stu-id="2517c-151">The URL path `/` uses the route template default `Home` controllers and `Index` action.</span></span> <span data-ttu-id="2517c-152">Cesta URL `/Home` používá akci výchozí `Index` šablony směrování.</span><span class="sxs-lookup"><span data-stu-id="2517c-152">The URL path `/Home` uses the route template default `Index` action.</span></span>

<span data-ttu-id="2517c-153">Metoda usnadnění <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute*>:</span><span class="sxs-lookup"><span data-stu-id="2517c-153">The convenience method <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute*>:</span></span>

```csharp
endpoints.MapDefaultControllerRoute();
```

<span data-ttu-id="2517c-154">LPRMON</span><span class="sxs-lookup"><span data-stu-id="2517c-154">Replaces:</span></span>

```csharp
endpoints.MapControllerRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="2517c-155">Směrování je nakonfigurované pomocí middlewaru <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting*> a <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*>.</span><span class="sxs-lookup"><span data-stu-id="2517c-155">Routing is configured using the <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting*> and <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> middleware.</span></span> <span data-ttu-id="2517c-156">Použití řadičů:</span><span class="sxs-lookup"><span data-stu-id="2517c-156">To use controllers:</span></span>

* <span data-ttu-id="2517c-157">Pro mapování řadičů [směrovaných na atributy](#ar) volejte <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*> uvnitř `UseEndpoints`.</span><span class="sxs-lookup"><span data-stu-id="2517c-157">Call <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*> inside `UseEndpoints` to map [attribute routed](#ar) controllers.</span></span>
* <span data-ttu-id="2517c-158">K namapování konvence, která je [směrována](#cr) na řadiče, můžete volat <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> nebo <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*>.</span><span class="sxs-lookup"><span data-stu-id="2517c-158">Call <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> or <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*>, to map [conventionally routed](#cr) controllers.</span></span>

<a name="routing-conventional-ref-label"></a>
<a name="crd"></a>

## <a name="conventional-routing"></a><span data-ttu-id="2517c-159">Konvenční směrování</span><span class="sxs-lookup"><span data-stu-id="2517c-159">Conventional routing</span></span>

<span data-ttu-id="2517c-160">Konvenční směrování se používá s řadiči a zobrazeními.</span><span class="sxs-lookup"><span data-stu-id="2517c-160">Conventional routing is used with controllers and views.</span></span> <span data-ttu-id="2517c-161">Trasa `default`:</span><span class="sxs-lookup"><span data-stu-id="2517c-161">The `default` route:</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupDefaultMVC.cs?name=snippet2)]

<span data-ttu-id="2517c-162">je příkladem *konvenčního směrování*.</span><span class="sxs-lookup"><span data-stu-id="2517c-162">is an example of a *conventional routing*.</span></span> <span data-ttu-id="2517c-163">Nazývá se *konvenční směrování* , protože vytváří *konvenci* pro cesty URL:</span><span class="sxs-lookup"><span data-stu-id="2517c-163">It's called *conventional routing* because it establishes a *convention* for URL paths:</span></span>

* <span data-ttu-id="2517c-164">První segment cesty, `{controller=Home}`, se mapuje na název kontroleru.</span><span class="sxs-lookup"><span data-stu-id="2517c-164">The first path segment, `{controller=Home}`, maps to the controller name.</span></span>
* <span data-ttu-id="2517c-165">Druhý segment, `{action=Index}`, se mapuje na název [Akce](#action) .</span><span class="sxs-lookup"><span data-stu-id="2517c-165">The second segment, `{action=Index}`, maps to the [action](#action) name.</span></span>
* <span data-ttu-id="2517c-166">Třetí segment `{id?}` slouží k volitelnému `id`.</span><span class="sxs-lookup"><span data-stu-id="2517c-166">The third segment, `{id?}` is used for an optional `id`.</span></span> <span data-ttu-id="2517c-167">`?` v `{id?}` je volitelná.</span><span class="sxs-lookup"><span data-stu-id="2517c-167">The `?` in `{id?}` makes it optional.</span></span> <span data-ttu-id="2517c-168">`id` slouží k mapování na entitu modelu.</span><span class="sxs-lookup"><span data-stu-id="2517c-168">`id` is used to map to a model entity.</span></span>

<span data-ttu-id="2517c-169">Tato `default` trasa používá cestu URL:</span><span class="sxs-lookup"><span data-stu-id="2517c-169">Using this `default` route, the URL path:</span></span>

* <span data-ttu-id="2517c-170">`/Products/List` se mapuje na akci `ProductsController.List`.</span><span class="sxs-lookup"><span data-stu-id="2517c-170">`/Products/List` maps to the `ProductsController.List` action.</span></span>
* <span data-ttu-id="2517c-171">`/Blog/Article/17` se mapuje na `BlogController.Article` a obvykle model váže parametr `id` na 17.</span><span class="sxs-lookup"><span data-stu-id="2517c-171">`/Blog/Article/17` maps to `BlogController.Article` and typically model binds the `id` parameter to 17.</span></span>

<span data-ttu-id="2517c-172">Toto mapování:</span><span class="sxs-lookup"><span data-stu-id="2517c-172">This mapping:</span></span>

* <span data-ttu-id="2517c-173">Je založena **pouze**na názvech kontroléru a [Akce](#action) .</span><span class="sxs-lookup"><span data-stu-id="2517c-173">Is based on the controller and [action](#action) names **only**.</span></span>
* <span data-ttu-id="2517c-174">Není založen na oborech názvů, umístěních zdrojového souboru nebo parametrech metody.</span><span class="sxs-lookup"><span data-stu-id="2517c-174">Isn't based on namespaces, source file locations, or method parameters.</span></span>

<span data-ttu-id="2517c-175">Použití konvenčního směrování s výchozí trasou umožňuje vytvoření aplikace bez nutnosti sestavovat nový vzor adresy URL pro každou akci.</span><span class="sxs-lookup"><span data-stu-id="2517c-175">Using conventional routing with the default route allows creating the app without having to come up with a new URL pattern for each action.</span></span> <span data-ttu-id="2517c-176">Pro aplikaci s akcemi stylu [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) s konzistencí pro adresy URL napříč řadiči:</span><span class="sxs-lookup"><span data-stu-id="2517c-176">For an app with [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) style actions, having consistency for the URLs across controllers:</span></span>

* <span data-ttu-id="2517c-177">Pomáhá zjednodušit kód.</span><span class="sxs-lookup"><span data-stu-id="2517c-177">Helps simplify the code.</span></span>
* <span data-ttu-id="2517c-178">Provede více předvídatelného uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="2517c-178">Makes the UI more predictable.</span></span>

> [!WARNING]
> <span data-ttu-id="2517c-179">`id` v předchozím kódu je šablona trasy definována jako volitelná.</span><span class="sxs-lookup"><span data-stu-id="2517c-179">The `id` in the preceding code is defined as optional by the route template.</span></span> <span data-ttu-id="2517c-180">Akce se můžou provádět bez volitelného ID, které jste zadali jako součást adresy URL.</span><span class="sxs-lookup"><span data-stu-id="2517c-180">Actions can execute without the optional ID provided as part of the URL.</span></span> <span data-ttu-id="2517c-181">Obecně platí, že pokud je`id` vynecháno z adresy URL:</span><span class="sxs-lookup"><span data-stu-id="2517c-181">Generally, when`id` is omitted from the URL:</span></span>
>
> * <span data-ttu-id="2517c-182">`id` je nastavené na `0` podle vazby modelu.</span><span class="sxs-lookup"><span data-stu-id="2517c-182">`id` is set to `0` by model binding.</span></span>
> * <span data-ttu-id="2517c-183">V databázi, která odpovídá `id == 0`, nebyla nalezena žádná entita.</span><span class="sxs-lookup"><span data-stu-id="2517c-183">No entity is found in the database matching `id == 0`.</span></span>
>
> <span data-ttu-id="2517c-184">[Směrování atributů](#ar) poskytuje jemně odstupňovaný ovládací prvek, který umožňuje, aby se ID vyžadovalo u některých akcí, a ne pro ostatní.</span><span class="sxs-lookup"><span data-stu-id="2517c-184">[Attribute routing](#ar) provides fine-grained control to make the ID required for some actions and not for others.</span></span> <span data-ttu-id="2517c-185">V dokumentaci podle konvence obsahuje volitelné parametry, jako je `id`, když se budou pravděpodobně zobrazovat ve správném použití.</span><span class="sxs-lookup"><span data-stu-id="2517c-185">By convention, the documentation includes optional parameters like `id` when they're likely to appear in correct usage.</span></span>

<span data-ttu-id="2517c-186">Většina aplikací by měla zvolit základní a popisné schéma směrování, aby byly adresy URL čitelné a smysluplné.</span><span class="sxs-lookup"><span data-stu-id="2517c-186">Most apps should choose a basic and descriptive routing scheme so that URLs are readable and meaningful.</span></span> <span data-ttu-id="2517c-187">Výchozí konvenční trasa `{controller=Home}/{action=Index}/{id?}`:</span><span class="sxs-lookup"><span data-stu-id="2517c-187">The default conventional route `{controller=Home}/{action=Index}/{id?}`:</span></span>

* <span data-ttu-id="2517c-188">Podporuje základní a popisné schéma směrování.</span><span class="sxs-lookup"><span data-stu-id="2517c-188">Supports a basic and descriptive routing scheme.</span></span>
* <span data-ttu-id="2517c-189">Je užitečným výchozím bodem pro aplikace založené na uživatelském rozhraní.</span><span class="sxs-lookup"><span data-stu-id="2517c-189">Is a useful starting point for UI-based apps.</span></span>
* <span data-ttu-id="2517c-190">Je jedinou šablonou směrování, která je nutná pro mnoho webových aplikací uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="2517c-190">Is the only route template needed for many web UI apps.</span></span> <span data-ttu-id="2517c-191">U větších webových aplikací uživatelského rozhraní je další trasa s použitím [oblastí](#areas) , pokud je to potřeba.</span><span class="sxs-lookup"><span data-stu-id="2517c-191">For larger web UI apps, another route using [Areas](#areas) if frequently all that's needed.</span></span>

<span data-ttu-id="2517c-192"><xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> a <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*>:</span><span class="sxs-lookup"><span data-stu-id="2517c-192"><xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> and <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> :</span></span>

* <span data-ttu-id="2517c-193">Automatické přiřazení hodnoty **objednávky** ke svým koncovým bodům podle pořadí, ve kterém jsou vyvolány.</span><span class="sxs-lookup"><span data-stu-id="2517c-193">Automatically assign an **order** value to their endpoints based on the order they are invoked.</span></span>

<span data-ttu-id="2517c-194">Směrování koncových bodů v ASP.NET Core 3,0 a novějším:</span><span class="sxs-lookup"><span data-stu-id="2517c-194">Endpoint routing in ASP.NET Core 3.0 and later:</span></span>

* <span data-ttu-id="2517c-195">Nemá koncept tras.</span><span class="sxs-lookup"><span data-stu-id="2517c-195">Doesn't have a concept of routes.</span></span>
* <span data-ttu-id="2517c-196">Neposkytuje zaručené řazení pro provádění rozšiřitelnosti, všechny koncové body jsou zpracovávány současně.</span><span class="sxs-lookup"><span data-stu-id="2517c-196">Doesn't provide ordering guarantees for the execution of extensibility,  all endpoints are processed at once.</span></span>

<span data-ttu-id="2517c-197">Povolte [protokolování](xref:fundamentals/logging/index) , abyste viděli, jak se zabudovaná implementace směrování, například <xref:Microsoft.AspNetCore.Routing.Route>, odpovídá požadavkům.</span><span class="sxs-lookup"><span data-stu-id="2517c-197">Enable [Logging](xref:fundamentals/logging/index) to see how the built-in routing implementations, such as <xref:Microsoft.AspNetCore.Routing.Route>, match requests.</span></span>

<span data-ttu-id="2517c-198">[Směrování atributů](#ar) je vysvětleno dále v tomto dokumentu.</span><span class="sxs-lookup"><span data-stu-id="2517c-198">[Attribute routing](#ar) is explained later in this document.</span></span>

<a name="mr"></a>

### <a name="multiple-conventional-routes"></a><span data-ttu-id="2517c-199">Několik konvenčních tras</span><span class="sxs-lookup"><span data-stu-id="2517c-199">Multiple conventional routes</span></span>

<span data-ttu-id="2517c-200">Do `UseEndpoints` lze přidat více [konvenčních tras](#cr) přidáním dalších volání do <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> a <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*>.</span><span class="sxs-lookup"><span data-stu-id="2517c-200">Multiple [conventional routes](#cr) can be added inside `UseEndpoints` by adding more calls to <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> and <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*>.</span></span> <span data-ttu-id="2517c-201">To umožňuje definovat více konvencí nebo přidávat konvenční trasy, které jsou vyhrazeny určité [akci](#action), například:</span><span class="sxs-lookup"><span data-stu-id="2517c-201">Doing so allows defining multiple conventions, or to adding conventional routes that are dedicated to a specific [action](#action), such as:</span></span>

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

<a name="dcr"></a>

<span data-ttu-id="2517c-202">Trasa `blog` v předchozím kódu je **vyhrazená konvenční trasa**.</span><span class="sxs-lookup"><span data-stu-id="2517c-202">The `blog` route in the preceding code is a **dedicated conventional route**.</span></span> <span data-ttu-id="2517c-203">Nazývá se vyhrazená konvenční trasa z těchto důvodů:</span><span class="sxs-lookup"><span data-stu-id="2517c-203">It's called a dedicated conventional route because:</span></span>

* <span data-ttu-id="2517c-204">Používá [konvenční směrování](#cr).</span><span class="sxs-lookup"><span data-stu-id="2517c-204">It uses [conventional routing](#cr).</span></span>
* <span data-ttu-id="2517c-205">Je vyhrazený pro konkrétní [akci](#action).</span><span class="sxs-lookup"><span data-stu-id="2517c-205">It's dedicated to a specific [action](#action).</span></span>

<span data-ttu-id="2517c-206">Protože `controller` a `action` se neobjeví v šabloně trasy `"blog/{*article}"` jako parametry:</span><span class="sxs-lookup"><span data-stu-id="2517c-206">Because `controller` and `action` don't appear in the route template `"blog/{*article}"` as parameters:</span></span>

* <span data-ttu-id="2517c-207">Můžou mít jenom výchozí hodnoty `{ controller = "Blog", action = "Article" }`.</span><span class="sxs-lookup"><span data-stu-id="2517c-207">They can only have the default values `{ controller = "Blog", action = "Article" }`.</span></span>
* <span data-ttu-id="2517c-208">Tato trasa se vždy mapuje na `BlogController.Article`akcí.</span><span class="sxs-lookup"><span data-stu-id="2517c-208">This route always maps to the action `BlogController.Article`.</span></span>

<span data-ttu-id="2517c-209">jediným cestám URL, které se shodují s trasou blogu, jsou `/Blog`, `/Blog/Article`a `/Blog/{any-string}`.</span><span class="sxs-lookup"><span data-stu-id="2517c-209">`/Blog`, `/Blog/Article`, and `/Blog/{any-string}` are the only URL paths that match the blog route.</span></span>

<span data-ttu-id="2517c-210">Předchozí příklad:</span><span class="sxs-lookup"><span data-stu-id="2517c-210">The preceding example:</span></span>

* <span data-ttu-id="2517c-211">`blog` trasa má vyšší prioritu pro shody, než je `default` trasy, protože je přidána jako první.</span><span class="sxs-lookup"><span data-stu-id="2517c-211">`blog` route has a higher priority for matches than the `default` route because it is added first.</span></span>
* <span data-ttu-id="2517c-212">Je a příkladem směrování stylu [popisu](https://developer.mozilla.org/docs/Glossary/Slug) , kde je typický název článku jako součást adresy URL.</span><span class="sxs-lookup"><span data-stu-id="2517c-212">Is and example of [Slug](https://developer.mozilla.org/docs/Glossary/Slug) style routing where it's typical to have an article name as part of the URL.</span></span>

> [!WARNING]
> <span data-ttu-id="2517c-213">V ASP.NET Core 3,0 a novějších směrování:</span><span class="sxs-lookup"><span data-stu-id="2517c-213">In ASP.NET Core 3.0 and later, routing doesn't:</span></span>
> * <span data-ttu-id="2517c-214">Definujte koncept nazvaný *trase*.</span><span class="sxs-lookup"><span data-stu-id="2517c-214">Define a concept called a *route*.</span></span> <span data-ttu-id="2517c-215">`UseRouting` přidává směrování do kanálu middlewaru.</span><span class="sxs-lookup"><span data-stu-id="2517c-215">`UseRouting` adds route matching to the middleware pipeline.</span></span> <span data-ttu-id="2517c-216">Middleware `UseRouting` prohlíží sadu koncových bodů definovaných v aplikaci a vybere nejlepší shodu koncového bodu na základě požadavku.</span><span class="sxs-lookup"><span data-stu-id="2517c-216">The `UseRouting` middleware looks at the set of endpoints defined in the app, and selects the best endpoint match based on the request.</span></span>
> * <span data-ttu-id="2517c-217">Poskytněte záruky týkající se pořadí spouštění rozšiřitelnosti, jako je <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> nebo <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint>.</span><span class="sxs-lookup"><span data-stu-id="2517c-217">Provide guarantees about the execution order of extensibility like <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> or <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint>.</span></span>
>
><span data-ttu-id="2517c-218">Přečtěte si téma [Směrování](xref:fundamentals/routing) pro referenční materiál při směrování.</span><span class="sxs-lookup"><span data-stu-id="2517c-218">See [Routing](xref:fundamentals/routing) for reference material on routing.</span></span>

<a name="cro"></a>

### <a name="conventional-routing-order"></a><span data-ttu-id="2517c-219">Konvenční pořadí směrování</span><span class="sxs-lookup"><span data-stu-id="2517c-219">Conventional routing order</span></span>

<span data-ttu-id="2517c-220">Konvenční směrování odpovídá pouze kombinaci akcí a kontrolérů, které jsou definovány aplikací.</span><span class="sxs-lookup"><span data-stu-id="2517c-220">Conventional routing only matches a combination of action and controller that are defined by the app.</span></span> <span data-ttu-id="2517c-221">Cílem je zjednodušit případy, kdy se konvenční trasy překrývají.</span><span class="sxs-lookup"><span data-stu-id="2517c-221">This is intended to simplify cases where conventional routes overlap.</span></span>
<span data-ttu-id="2517c-222">Přidávání tras pomocí <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*>, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute*>a <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> automaticky přiřazují hodnoty objednávky na jejich koncové body podle pořadí, ve kterém jsou vyvolány.</span><span class="sxs-lookup"><span data-stu-id="2517c-222">Adding routes using <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*>, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute*>, and <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> automatically assign an order value to their endpoints based on the order they are invoked.</span></span> <span data-ttu-id="2517c-223">Shody z trasy, která se zobrazí dříve, mají vyšší prioritu.</span><span class="sxs-lookup"><span data-stu-id="2517c-223">Matches from a route that appears earlier have a higher priority.</span></span> <span data-ttu-id="2517c-224">Konvenční směrování je závislé na pořadí.</span><span class="sxs-lookup"><span data-stu-id="2517c-224">Conventional routing is order-dependent.</span></span> <span data-ttu-id="2517c-225">Obecně platí, že trasy s oblastmi by měly být umístěny dříve, protože jsou konkrétnější než trasy bez oblasti.</span><span class="sxs-lookup"><span data-stu-id="2517c-225">In general, routes with areas should be placed earlier as they're more specific than routes without an area.</span></span> <span data-ttu-id="2517c-226">[Vyhrazené konvenční trasy](#dcr) s zachycením všech parametrů tras, jako je `{*article}`, mohou vytvořit trasu příliš [hladce](xref:fundamentals/routing#greedy), což znamená, že odpovídají adresám URL, které mají být porovnány s jinými trasami.</span><span class="sxs-lookup"><span data-stu-id="2517c-226">[Dedicated conventional routes](#dcr) with catch all route parameters like `{*article}` can make a route too [greedy](xref:fundamentals/routing#greedy), meaning that it matches URLs that you intended to be matched by other routes.</span></span> <span data-ttu-id="2517c-227">Tyto hladové trasy umístěte později do směrovací tabulky, aby se zabránilo hladce porovnávání.</span><span class="sxs-lookup"><span data-stu-id="2517c-227">Put the greedy routes later in the route table to prevent greedy matches.</span></span>

<a name="best"></a>

### <a name="resolving-ambiguous-actions"></a><span data-ttu-id="2517c-228">Řešení nejednoznačných akcí</span><span class="sxs-lookup"><span data-stu-id="2517c-228">Resolving ambiguous actions</span></span>

<span data-ttu-id="2517c-229">Pokud se dva koncové body shodují přes směrování, musí směrování provést jednu z následujících akcí:</span><span class="sxs-lookup"><span data-stu-id="2517c-229">When two endpoints match through routing, routing must do one of the following:</span></span>

* <span data-ttu-id="2517c-230">Vyberte nejlepší kandidáta.</span><span class="sxs-lookup"><span data-stu-id="2517c-230">Choose the best candidate.</span></span>
* <span data-ttu-id="2517c-231">Vyvolejte výjimku.</span><span class="sxs-lookup"><span data-stu-id="2517c-231">Throw an exception.</span></span>

<span data-ttu-id="2517c-232">Příklad:</span><span class="sxs-lookup"><span data-stu-id="2517c-232">For example:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet9)]

<span data-ttu-id="2517c-233">Předchozí kontroler definuje dvě akce, které se shodují:</span><span class="sxs-lookup"><span data-stu-id="2517c-233">The preceding controller defines two actions that match:</span></span>

* <span data-ttu-id="2517c-234">Cesta URL `/Products33/Edit/17`</span><span class="sxs-lookup"><span data-stu-id="2517c-234">The URL path `/Products33/Edit/17`</span></span>
* <span data-ttu-id="2517c-235">Směrování dat `{ controller = Products33, action = Edit, id = 17 }`.</span><span class="sxs-lookup"><span data-stu-id="2517c-235">Route data `{ controller = Products33, action = Edit, id = 17 }`.</span></span>

<span data-ttu-id="2517c-236">Toto je typický vzor pro řadiče MVC:</span><span class="sxs-lookup"><span data-stu-id="2517c-236">This is a typical pattern for MVC controllers:</span></span>

* <span data-ttu-id="2517c-237">`Edit(int)` zobrazí formulář pro úpravu produktu.</span><span class="sxs-lookup"><span data-stu-id="2517c-237">`Edit(int)` displays a form to edit a product.</span></span>
* <span data-ttu-id="2517c-238">`Edit(int, Product)` zpracuje publikovaný formulář.</span><span class="sxs-lookup"><span data-stu-id="2517c-238">`Edit(int, Product)` processes  the posted form.</span></span>

<span data-ttu-id="2517c-239">Postup při řešení správné trasy:</span><span class="sxs-lookup"><span data-stu-id="2517c-239">To resolve the correct route:</span></span>

* <span data-ttu-id="2517c-240">`Edit(int, Product)` je vybrána, pokud je žádost `POST`HTTP.</span><span class="sxs-lookup"><span data-stu-id="2517c-240">`Edit(int, Product)` is selected when the request is an HTTP `POST`.</span></span>
* <span data-ttu-id="2517c-241">`Edit(int)` je vybrána, pokud je [příkaz HTTP](#verb) cokoliv jiného.</span><span class="sxs-lookup"><span data-stu-id="2517c-241">`Edit(int)` is selected when the [HTTP verb](#verb) is anything else.</span></span> <span data-ttu-id="2517c-242">`Edit(int)` se obvykle volá prostřednictvím `GET`.</span><span class="sxs-lookup"><span data-stu-id="2517c-242">`Edit(int)` is generally called via `GET`.</span></span>

<span data-ttu-id="2517c-243">Služba <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute>, `[HttpPost]`, je k dispozici ke směrování, aby se mohla zvolit na základě metody HTTP žádosti.</span><span class="sxs-lookup"><span data-stu-id="2517c-243">The <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute>, `[HttpPost]`, is provided to routing so that it can choose based on the HTTP method of the request.</span></span> <span data-ttu-id="2517c-244">`HttpPostAttribute` zajišťuje `Edit(int, Product)` lepší shodu než `Edit(int)`.</span><span class="sxs-lookup"><span data-stu-id="2517c-244">The `HttpPostAttribute` makes `Edit(int, Product)` a better match than `Edit(int)`.</span></span>

<span data-ttu-id="2517c-245">Je důležité pochopit role atributů, jako je `HttpPostAttribute`.</span><span class="sxs-lookup"><span data-stu-id="2517c-245">It's important to understand the role of attributes like `HttpPostAttribute`.</span></span> <span data-ttu-id="2517c-246">Podobné atributy jsou definovány pro jiné [příkazy HTTP](#verb).</span><span class="sxs-lookup"><span data-stu-id="2517c-246">Similar attributes are defined for other [HTTP verbs](#verb).</span></span> <span data-ttu-id="2517c-247">V případě [konvenčního směrování](#cr)je běžné, že akce používají stejný název akce, když jsou součástí formuláře zobrazení, odeslání pracovního postupu formuláře.</span><span class="sxs-lookup"><span data-stu-id="2517c-247">In [conventional routing](#cr), it's common for actions to use the same action name when they're part of a show form, submit form workflow.</span></span> <span data-ttu-id="2517c-248">Příklad naleznete v tématu [prostudování dvou metod Edit Action](xref:tutorials/first-mvc-app/controller-methods-views#get-post).</span><span class="sxs-lookup"><span data-stu-id="2517c-248">For example, see [Examine the two Edit action methods](xref:tutorials/first-mvc-app/controller-methods-views#get-post).</span></span>

<span data-ttu-id="2517c-249">Pokud směrování nemůže zvolit nejlepší kandidáta, vyvolá se <xref:System.Reflection.AmbiguousMatchException> a zobrazí se seznam více odpovídajících koncových bodů.</span><span class="sxs-lookup"><span data-stu-id="2517c-249">If routing can't choose a best candidate, an <xref:System.Reflection.AmbiguousMatchException> is thrown, listing the multiple matched endpoints.</span></span>

<a name="routing-route-name-ref-label"></a>

### <a name="conventional-route-names"></a><span data-ttu-id="2517c-250">Názvy konvenčních tras</span><span class="sxs-lookup"><span data-stu-id="2517c-250">Conventional route names</span></span>

<span data-ttu-id="2517c-251">Řetězce `"blog"` a `"default"` v následujících příkladech jsou konvenční názvy tras:</span><span class="sxs-lookup"><span data-stu-id="2517c-251">The strings  `"blog"` and `"default"` in the following examples are conventional route names:</span></span>

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

<span data-ttu-id="2517c-252">Názvy tras udávají logický název trasy.</span><span class="sxs-lookup"><span data-stu-id="2517c-252">The route names give the route a logical name.</span></span> <span data-ttu-id="2517c-253">Pojmenovanou trasu lze použít pro generování adresy URL.</span><span class="sxs-lookup"><span data-stu-id="2517c-253">The named route can be used for URL generation.</span></span> <span data-ttu-id="2517c-254">Použití pojmenované trasy zjednodušuje vytváření adresy URL, když řazení tras může způsobit komplikované generování adresy URL.</span><span class="sxs-lookup"><span data-stu-id="2517c-254">Using a named route simplifies URL creation when the ordering of routes could make URL generation complicated.</span></span> <span data-ttu-id="2517c-255">Názvy tras musí být jedinečné aplikace v širším rozsahu.</span><span class="sxs-lookup"><span data-stu-id="2517c-255">Route names must be unique application wide.</span></span>

<span data-ttu-id="2517c-256">Názvy tras:</span><span class="sxs-lookup"><span data-stu-id="2517c-256">Route names:</span></span>

* <span data-ttu-id="2517c-257">Nemusíte mít žádný vliv na adresu URL ani manipulaci s požadavky.</span><span class="sxs-lookup"><span data-stu-id="2517c-257">Have no impact on URL matching or handling of requests.</span></span>
* <span data-ttu-id="2517c-258">Jsou používány pouze pro generování adresy URL.</span><span class="sxs-lookup"><span data-stu-id="2517c-258">Are used only for URL generation.</span></span>

<span data-ttu-id="2517c-259">Koncept názvu trasy je reprezentován ve směrování jako [IEndpointNameMetadata](xref:Microsoft.AspNetCore.Routing.IEndpointNameMetadata).</span><span class="sxs-lookup"><span data-stu-id="2517c-259">The route name concept is represented in routing as [IEndpointNameMetadata](xref:Microsoft.AspNetCore.Routing.IEndpointNameMetadata).</span></span> <span data-ttu-id="2517c-260">Název **Směrování** a název **koncového bodu**:</span><span class="sxs-lookup"><span data-stu-id="2517c-260">The terms **route name** and **endpoint name**:</span></span>

* <span data-ttu-id="2517c-261">Jsou zaměnitelné.</span><span class="sxs-lookup"><span data-stu-id="2517c-261">Are interchangeable.</span></span>
* <span data-ttu-id="2517c-262">Který je používán v dokumentaci a kód závisí na popisovaném rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="2517c-262">Which one is used in documentation and code depends on the API being described.</span></span>

<a name="attribute-routing-ref-label"></a>
<a name="ar"></a>

## <a name="attribute-routing-for-rest-apis"></a><span data-ttu-id="2517c-263">Směrování atributů pro rozhraní REST API</span><span class="sxs-lookup"><span data-stu-id="2517c-263">Attribute routing for REST APIs</span></span>

<span data-ttu-id="2517c-264">Rozhraní REST API by měly používat směrování atributů k modelování funkčnosti aplikace jako sady prostředků, ve kterých jsou operace reprezentované [příkazy HTTP](#verb).</span><span class="sxs-lookup"><span data-stu-id="2517c-264">REST APIs should use attribute routing to model the app's functionality as a set of resources where operations are represented by [HTTP verbs](#verb).</span></span>

<span data-ttu-id="2517c-265">Směrování atributů používá sadu atributů k mapování akcí přímo na šablony směrování.</span><span class="sxs-lookup"><span data-stu-id="2517c-265">Attribute routing uses a set of attributes to map actions directly to route templates.</span></span> <span data-ttu-id="2517c-266">Následující kód `StartUp.Configure` je typický pro REST API a používá se v další ukázce:</span><span class="sxs-lookup"><span data-stu-id="2517c-266">The following `StartUp.Configure` code is typical for a REST API and is used in the next sample:</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupApi.cs?name=snippet)]

<span data-ttu-id="2517c-267">V předchozím kódu je <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*> volána uvnitř `UseEndpoints` k mapování řadičů směrovaných na atributy.</span><span class="sxs-lookup"><span data-stu-id="2517c-267">In the preceding code, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*> is called inside `UseEndpoints` to map attribute routed controllers.</span></span>

<span data-ttu-id="2517c-268">V následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="2517c-268">In the following example:</span></span>

* <span data-ttu-id="2517c-269">Je použita předchozí metoda `Configure`.</span><span class="sxs-lookup"><span data-stu-id="2517c-269">The preceding `Configure` method is used.</span></span>
* <span data-ttu-id="2517c-270">`HomeController` se shoduje se sadou adres URL podobnou tomu, že výchozí konvenční trasa `{controller=Home}/{action=Index}/{id?}` shodná.</span><span class="sxs-lookup"><span data-stu-id="2517c-270">`HomeController` matches a set of URLs similar to what the default conventional route `{controller=Home}/{action=Index}/{id?}` matches.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet2)]

<span data-ttu-id="2517c-271">Akce `HomeController.Index` se spustí pro všechny cesty URL `/`, `/Home`, `/Home/Index`nebo `/Home/Index/3`.</span><span class="sxs-lookup"><span data-stu-id="2517c-271">The `HomeController.Index` action is run for any of the URL paths `/`, `/Home`, `/Home/Index`, or `/Home/Index/3`.</span></span>

<span data-ttu-id="2517c-272">V tomto příkladu se zvýrazní klíčový rozdíl mezi směrováním atributů a [konvenčním směrováním](#cr).</span><span class="sxs-lookup"><span data-stu-id="2517c-272">This example highlights a key programming difference between attribute routing and [conventional routing](#cr).</span></span> <span data-ttu-id="2517c-273">Směrování atributů vyžaduje více vstupu pro určení trasy.</span><span class="sxs-lookup"><span data-stu-id="2517c-273">Attribute routing requires more input to specify a route.</span></span> <span data-ttu-id="2517c-274">Konvenční výchozí trasa zpracovává trasy více stručně.</span><span class="sxs-lookup"><span data-stu-id="2517c-274">The conventional default route handles routes more succinctly.</span></span> <span data-ttu-id="2517c-275">Směrování atributů ale umožňuje a vyžaduje přesnou kontrolu nad tím, které šablony směrování se vztahují na každou [akci](#action).</span><span class="sxs-lookup"><span data-stu-id="2517c-275">However, attribute routing allows and requires precise control of which route templates apply to each [action](#action).</span></span>

<span data-ttu-id="2517c-276">Při směrování atributů název kontroleru a názvy akcí nehrají **žádnou** roli, v rámci které se akce shodují.</span><span class="sxs-lookup"><span data-stu-id="2517c-276">With attribute routing, the controller name and action names play **no** role in which action is matched.</span></span> <span data-ttu-id="2517c-277">V následujícím příkladu se shodují stejné adresy URL jako v předchozím příkladu:</span><span class="sxs-lookup"><span data-stu-id="2517c-277">The following example matches the same URLs as the previous example:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemoController.cs?name=snippet)]

<span data-ttu-id="2517c-278">Následující kód používá nahrazení tokenu pro `action` a `controller`:</span><span class="sxs-lookup"><span data-stu-id="2517c-278">The following code uses token replacement for `action` and `controller`:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet22)]

<span data-ttu-id="2517c-279">Následující kód platí `[Route("[controller]/[action]")]` pro kontroler:</span><span class="sxs-lookup"><span data-stu-id="2517c-279">The following code applies `[Route("[controller]/[action]")]` to the controller:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet24)]

<span data-ttu-id="2517c-280">V předchozím kódu musí šablony metody `Index` v šablonách směrování dodávat `/` nebo `~/`.</span><span class="sxs-lookup"><span data-stu-id="2517c-280">In the preceding code, the `Index` method templates must prepend `/` or `~/` to the route templates.</span></span> <span data-ttu-id="2517c-281">Šablony směrování použité pro akci, která začíná na `/` nebo `~/` se nekombinují se šablonami směrování použitými pro kontroler.</span><span class="sxs-lookup"><span data-stu-id="2517c-281">Route templates applied to an action that begin with `/` or `~/` don't get combined with route templates applied to the controller.</span></span>

<span data-ttu-id="2517c-282">Informace o výběru šablony směrování najdete v tématu [Priorita šablony směrování](xref:fundamentals/routing#rtp) .</span><span class="sxs-lookup"><span data-stu-id="2517c-282">See [Route template precedence](xref:fundamentals/routing#rtp) for information on route template selection.</span></span>

## <a name="reserved-routing-names"></a><span data-ttu-id="2517c-283">Názvy rezervovaných směrování</span><span class="sxs-lookup"><span data-stu-id="2517c-283">Reserved routing names</span></span>

<span data-ttu-id="2517c-284">Následující klíčová slova jsou rezervované názvy parametrů tras při použití řadičů nebo Razor Pages:</span><span class="sxs-lookup"><span data-stu-id="2517c-284">The following keywords are reserved route parameter names when using Controllers or Razor Pages:</span></span>

* `action`
* `area`
* `controller`
* `handler`
* `page`

<span data-ttu-id="2517c-285">Běžnou chybou je použití `page` jako parametru trasy se směrováním atributů.</span><span class="sxs-lookup"><span data-stu-id="2517c-285">Using `page` as a route parameter with attribute routing is a common error.</span></span> <span data-ttu-id="2517c-286">Výsledkem je nekonzistentní a matoucí chování s generováním adresy URL.</span><span class="sxs-lookup"><span data-stu-id="2517c-286">Doing that results in inconsistent and confusing behavior with URL generation.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemo2Controller.cs?name=snippet)]

<span data-ttu-id="2517c-287">Speciální názvy parametrů jsou používány generováním adresy URL k určení, zda operace generování adresy URL odkazuje na stránku Razor nebo na kontroler.</span><span class="sxs-lookup"><span data-stu-id="2517c-287">The special parameter names are used by the URL generation to determine if a URL generation operation refers to a Razor Page or to a Controller.</span></span>

<a name="verb"></a>

## <a name="http-verb-templates"></a><span data-ttu-id="2517c-288">Šablony příkazů HTTP</span><span class="sxs-lookup"><span data-stu-id="2517c-288">HTTP verb templates</span></span>

<span data-ttu-id="2517c-289">ASP.NET Core má následující šablony příkazů HTTP:</span><span class="sxs-lookup"><span data-stu-id="2517c-289">ASP.NET Core has the following HTTP verb templates:</span></span>

* <span data-ttu-id="2517c-290">[HttpGet](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)</span><span class="sxs-lookup"><span data-stu-id="2517c-290">[[HttpGet]](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)</span></span>
* <span data-ttu-id="2517c-291">[HTTPPOST](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute)</span><span class="sxs-lookup"><span data-stu-id="2517c-291">[[HttpPost]](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute)</span></span>
* <span data-ttu-id="2517c-292">[[HttpPut]](xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute)</span><span class="sxs-lookup"><span data-stu-id="2517c-292">[[HttpPut]](xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute)</span></span>
* <span data-ttu-id="2517c-293">[[HttpDelete]](xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute)</span><span class="sxs-lookup"><span data-stu-id="2517c-293">[[HttpDelete]](xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute)</span></span>
* <span data-ttu-id="2517c-294">[[HttpHead]](xref:Microsoft.AspNetCore.Mvc.HttpHeadAttribute)</span><span class="sxs-lookup"><span data-stu-id="2517c-294">[[HttpHead]](xref:Microsoft.AspNetCore.Mvc.HttpHeadAttribute)</span></span>
* <span data-ttu-id="2517c-295">[[HttpPatch]](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)</span><span class="sxs-lookup"><span data-stu-id="2517c-295">[[HttpPatch]](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)</span></span>

<a name="rt"></a>

### <a name="route-templates"></a><span data-ttu-id="2517c-296">Šablony směrování</span><span class="sxs-lookup"><span data-stu-id="2517c-296">Route templates</span></span>

<span data-ttu-id="2517c-297">ASP.NET Core má následující šablony tras:</span><span class="sxs-lookup"><span data-stu-id="2517c-297">ASP.NET Core has the following route templates:</span></span>

* <span data-ttu-id="2517c-298">Všechny [šablony operací HTTP](#verb) jsou šablony směrování.</span><span class="sxs-lookup"><span data-stu-id="2517c-298">All the [HTTP verb templates](#verb) are route templates.</span></span>
* <span data-ttu-id="2517c-299">[Cestě](xref:Microsoft.AspNetCore.Mvc.RouteAttribute)</span><span class="sxs-lookup"><span data-stu-id="2517c-299">[[Route]](xref:Microsoft.AspNetCore.Mvc.RouteAttribute)</span></span>

<a name="arx"></a>

### <a name="attribute-routing-with-http-verb-attributes"></a><span data-ttu-id="2517c-300">Směrování atributů s atributy příkazu http</span><span class="sxs-lookup"><span data-stu-id="2517c-300">Attribute routing with Http verb attributes</span></span>

<span data-ttu-id="2517c-301">Vezměte v úvahu následující kontroler:</span><span class="sxs-lookup"><span data-stu-id="2517c-301">Consider the following controller:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet)]

<span data-ttu-id="2517c-302">V předchozím kódu:</span><span class="sxs-lookup"><span data-stu-id="2517c-302">In the preceding code:</span></span>

* <span data-ttu-id="2517c-303">Každá akce obsahuje atribut `[HttpGet]`, který omezuje porovnání pouze s požadavky HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="2517c-303">Each action contains the `[HttpGet]` attribute, which constrains matching to HTTP GET requests only.</span></span>
* <span data-ttu-id="2517c-304">Akce `GetProduct` obsahuje šablonu `"{id}"`, proto `id` připojená k šabloně `"api/[controller]"` na řadiči.</span><span class="sxs-lookup"><span data-stu-id="2517c-304">The `GetProduct` action includes the `"{id}"` template, therefore `id` is appended to the `"api/[controller]"` template on the controller.</span></span> <span data-ttu-id="2517c-305">Šablona metod je `"api/[controller]/"{id}""`.</span><span class="sxs-lookup"><span data-stu-id="2517c-305">The methods template is `"api/[controller]/"{id}""`.</span></span> <span data-ttu-id="2517c-306">Proto tato akce odpovídá pouze požadavkům GET pro formulář `/api/test2/xyz`,`/api/test2/123`,`/api/test2/{any string}`atd.</span><span class="sxs-lookup"><span data-stu-id="2517c-306">Therefore this action only matches GET requests of for the form `/api/test2/xyz`,`/api/test2/123`,`/api/test2/{any string}`, etc.</span></span>
  [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet2)]
* <span data-ttu-id="2517c-307">Akce `GetIntProduct` obsahuje šablonu `"int/{id:int}")`.</span><span class="sxs-lookup"><span data-stu-id="2517c-307">The `GetIntProduct` action contains the `"int/{id:int}")` template.</span></span> <span data-ttu-id="2517c-308">`:int` část šablony omezuje hodnoty `id` směrování na řetězce, které lze převést na celé číslo.</span><span class="sxs-lookup"><span data-stu-id="2517c-308">The `:int` portion of the template constrains the `id` route values to strings that can be converted to an integer.</span></span> <span data-ttu-id="2517c-309">Požadavek GET na `/api/test2/int/abc`:</span><span class="sxs-lookup"><span data-stu-id="2517c-309">A GET request to `/api/test2/int/abc`:</span></span>
  * <span data-ttu-id="2517c-310">Neodpovídá této akci.</span><span class="sxs-lookup"><span data-stu-id="2517c-310">Doesn't match this action.</span></span>
  * <span data-ttu-id="2517c-311">Vrátí chybu typu 404, která [nebyla nalezena](https://developer.mozilla.org/docs/Web/HTTP/Status/404) .</span><span class="sxs-lookup"><span data-stu-id="2517c-311">Returns a [404 Not Found](https://developer.mozilla.org/docs/Web/HTTP/Status/404) error.</span></span>
    [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet3)]
* <span data-ttu-id="2517c-312">Akce `GetInt2Product` obsahuje `{id}` v šabloně, ale neomezí `id` na hodnoty, které lze převést na celé číslo.</span><span class="sxs-lookup"><span data-stu-id="2517c-312">The `GetInt2Product` action contains `{id}` in the template, but doesn't constrain `id` to values that can be converted to an integer.</span></span> <span data-ttu-id="2517c-313">Požadavek GET na `/api/test2/int2/abc`:</span><span class="sxs-lookup"><span data-stu-id="2517c-313">A GET request to `/api/test2/int2/abc`:</span></span>
  * <span data-ttu-id="2517c-314">Odpovídá této trase.</span><span class="sxs-lookup"><span data-stu-id="2517c-314">Matches this route.</span></span>
  * <span data-ttu-id="2517c-315">Vazba modelu nemůže převést `abc` na celé číslo.</span><span class="sxs-lookup"><span data-stu-id="2517c-315">Model binding fails to convert `abc` to an integer.</span></span> <span data-ttu-id="2517c-316">Parametr `id` metody je celé číslo.</span><span class="sxs-lookup"><span data-stu-id="2517c-316">The `id` parameter of the method is integer.</span></span>
  * <span data-ttu-id="2517c-317">Vrátí [chybný požadavek 400](https://developer.mozilla.org/docs/Web/HTTP/Status/400) , protože vazba modelu selhala při převodu`abc` na celé číslo.</span><span class="sxs-lookup"><span data-stu-id="2517c-317">Returns a [400 Bad Request](https://developer.mozilla.org/docs/Web/HTTP/Status/400) because model binding failed to convert`abc` to an integer.</span></span>
      [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet4)]

<span data-ttu-id="2517c-318">Směrování atributů může používat atributy <xref:Microsoft.AspNetCore.Mvc.Routing.HttpMethodAttribute>, jako jsou <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute>, <xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute>a <xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute>.</span><span class="sxs-lookup"><span data-stu-id="2517c-318">Attribute routing can use <xref:Microsoft.AspNetCore.Mvc.Routing.HttpMethodAttribute> attributes such as <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute>, <xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute>, and <xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute>.</span></span> <span data-ttu-id="2517c-319">Všechny atributy [příkazu http](#verb) přijímají šablonu směrování.</span><span class="sxs-lookup"><span data-stu-id="2517c-319">All of the [HTTP verb](#verb) attributes accept a route template.</span></span> <span data-ttu-id="2517c-320">Následující příklad ukazuje dvě akce, které odpovídají stejné šabloně směrování:</span><span class="sxs-lookup"><span data-stu-id="2517c-320">The following example shows two actions that match the same route template:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyProductsController.cs?name=snippet1)]

<span data-ttu-id="2517c-321">Použití cesty URL `/products3`:</span><span class="sxs-lookup"><span data-stu-id="2517c-321">Using the URL path `/products3`:</span></span>

* <span data-ttu-id="2517c-322">Akce `MyProductsController.ListProducts` se spustí, když je `GET`[příkaz HTTP](#verb) .</span><span class="sxs-lookup"><span data-stu-id="2517c-322">The `MyProductsController.ListProducts` action runs when the [HTTP verb](#verb) is `GET`.</span></span>
* <span data-ttu-id="2517c-323">Akce `MyProductsController.CreateProduct` se spustí, když je `POST`[příkaz HTTP](#verb) .</span><span class="sxs-lookup"><span data-stu-id="2517c-323">The `MyProductsController.CreateProduct` action runs when the [HTTP verb](#verb) is `POST`.</span></span>

<span data-ttu-id="2517c-324">Při sestavování REST API je zřídka nutné použít `[Route(...)]` na metodě Action, protože akce akceptuje všechny metody HTTP.</span><span class="sxs-lookup"><span data-stu-id="2517c-324">When building a REST API, it's rare that you'll need to use `[Route(...)]` on an action method because the action accepts all HTTP methods.</span></span> <span data-ttu-id="2517c-325">Je lepší použít konkrétnější [atribut příkazu http](#verb) , abyste mohli přesně zjistit, co vaše rozhraní API podporuje.</span><span class="sxs-lookup"><span data-stu-id="2517c-325">It's better to use the more specific [HTTP verb attribute](#verb) to be precise about what your API supports.</span></span> <span data-ttu-id="2517c-326">Očekává se, že klienti rozhraní REST API znají, které cesty a příkazy HTTP se mapují na konkrétní logické operace.</span><span class="sxs-lookup"><span data-stu-id="2517c-326">Clients of REST APIs are expected to know what paths and HTTP verbs map to specific logical operations.</span></span>

<span data-ttu-id="2517c-327">Rozhraní REST API by měly používat směrování atributů k modelování funkčnosti aplikace jako sady prostředků, ve kterých jsou operace reprezentované příkazy HTTP.</span><span class="sxs-lookup"><span data-stu-id="2517c-327">REST APIs should use attribute routing to model the app's functionality as a set of resources where operations are represented by HTTP verbs.</span></span> <span data-ttu-id="2517c-328">To znamená, že mnohé operace, například GET a POST u stejného logického prostředku, používají stejnou adresu URL.</span><span class="sxs-lookup"><span data-stu-id="2517c-328">This means that many operations, for example, GET and POST on the same logical resource use the same URL.</span></span> <span data-ttu-id="2517c-329">Směrování atributů poskytuje úroveň řízení, která je nutná k pečlivému návrhu rozložení veřejného koncového bodu rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="2517c-329">Attribute routing provides a level of control that's needed to carefully design an API's public endpoint layout.</span></span>

<span data-ttu-id="2517c-330">Vzhledem k tomu, že trasa atributu se vztahuje na konkrétní akci, je snadné vytvořit parametry požadované v rámci definice šablony trasy.</span><span class="sxs-lookup"><span data-stu-id="2517c-330">Since an attribute route applies to a specific action, it's easy to make parameters required as part of the route template definition.</span></span> <span data-ttu-id="2517c-331">V následujícím příkladu je `id` nutné jako součást cesty URL:</span><span class="sxs-lookup"><span data-stu-id="2517c-331">In the following example, `id` is required as part of the URL path:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet2)]

<span data-ttu-id="2517c-332">Akce `Products2ApiController.GetProduct(int)`:</span><span class="sxs-lookup"><span data-stu-id="2517c-332">The `Products2ApiController.GetProduct(int)` action:</span></span>

* <span data-ttu-id="2517c-333">Se spouští s cestou URL, jako je `/products2/3`</span><span class="sxs-lookup"><span data-stu-id="2517c-333">Is run with URL path like `/products2/3`</span></span>
* <span data-ttu-id="2517c-334">Není spuštěn s cestou adresy URL `/products2`.</span><span class="sxs-lookup"><span data-stu-id="2517c-334">Isn't run with the URL path `/products2`.</span></span>

<span data-ttu-id="2517c-335">Atribut [[](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>) ] umožňuje akci omezit podporované typy obsahu požadavků.</span><span class="sxs-lookup"><span data-stu-id="2517c-335">The [[Consumes]](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>) attribute allows an action to limit the supported request content types.</span></span> <span data-ttu-id="2517c-336">Další informace najdete v tématu [Definování podporovaných typů obsahu požadavků pomocí atributu spotřebes](xref:web-api/index#consumes).</span><span class="sxs-lookup"><span data-stu-id="2517c-336">For more information, see [Define supported request content types with the Consumes attribute](xref:web-api/index#consumes).</span></span>

 <span data-ttu-id="2517c-337">Úplný popis šablon směrování a souvisejících možností najdete v tématu věnovaném [Směrování](xref:fundamentals/routing) .</span><span class="sxs-lookup"><span data-stu-id="2517c-337">See [Routing](xref:fundamentals/routing) for a full description of route templates and related options.</span></span>

<span data-ttu-id="2517c-338">Další informace o `[ApiController]`naleznete v tématu [ApiController Attribute](xref:web-api/index##apicontroller-attribute).</span><span class="sxs-lookup"><span data-stu-id="2517c-338">For more information on `[ApiController]`, see [ApiController attribute](xref:web-api/index##apicontroller-attribute).</span></span>

## <a name="route-name"></a><span data-ttu-id="2517c-339">Název trasy</span><span class="sxs-lookup"><span data-stu-id="2517c-339">Route name</span></span>

<span data-ttu-id="2517c-340">Následující kód definuje název trasy `Products_List`:</span><span class="sxs-lookup"><span data-stu-id="2517c-340">The following code  defines a route name of `Products_List`:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet2)]

<span data-ttu-id="2517c-341">Názvy tras se dají použít k vygenerování adresy URL na základě konkrétní trasy.</span><span class="sxs-lookup"><span data-stu-id="2517c-341">Route names can be used to generate a URL based on a specific route.</span></span> <span data-ttu-id="2517c-342">Názvy tras:</span><span class="sxs-lookup"><span data-stu-id="2517c-342">Route names:</span></span>

* <span data-ttu-id="2517c-343">Nemá žádný vliv na chování směrování, které odpovídá adrese URL.</span><span class="sxs-lookup"><span data-stu-id="2517c-343">Have no impact on the URL matching behavior of routing.</span></span>
* <span data-ttu-id="2517c-344">Používá se pouze pro generování adresy URL.</span><span class="sxs-lookup"><span data-stu-id="2517c-344">Are only used for URL generation.</span></span>

<span data-ttu-id="2517c-345">Názvy tras musí být jedinečné aplikace v rozsahu.</span><span class="sxs-lookup"><span data-stu-id="2517c-345">Route names must be unique application-wide.</span></span>

<span data-ttu-id="2517c-346">Porovnejte předchozí kód s konvenční výchozí trasou, která definuje parametr `id` jako nepovinný (`{id?}`).</span><span class="sxs-lookup"><span data-stu-id="2517c-346">Contrast the preceding code with the conventional default route, which defines the `id` parameter as optional (`{id?}`).</span></span> <span data-ttu-id="2517c-347">Možnost přesně zadat rozhraní API má výhody, jako je například povolení `/products` a `/products/5` odeslání do různých akcí.</span><span class="sxs-lookup"><span data-stu-id="2517c-347">The ability to precisely specify APIs has advantages, such as  allowing `/products` and `/products/5` to be dispatched to different actions.</span></span>

<a name="routing-combining-ref-label"></a>

## <a name="combining-attribute-routes"></a><span data-ttu-id="2517c-348">Kombinování tras atributů</span><span class="sxs-lookup"><span data-stu-id="2517c-348">Combining attribute routes</span></span>

<span data-ttu-id="2517c-349">Aby bylo směrování atributů méně opakované, jsou atributy směrování na kontroleru kombinovány s atributy směrování na jednotlivých akcích.</span><span class="sxs-lookup"><span data-stu-id="2517c-349">To make attribute routing less repetitive, route attributes on the controller are combined with route attributes on the individual actions.</span></span> <span data-ttu-id="2517c-350">Každá šablona trasy definovaná na řadiči je předá směrování šablon na akcích.</span><span class="sxs-lookup"><span data-stu-id="2517c-350">Any route templates defined on the controller are prepended to route templates on the actions.</span></span> <span data-ttu-id="2517c-351">Umístěním atributu směrování na řadiči se vytvoří **všechny** akce v řadiči použít směrování atributů.</span><span class="sxs-lookup"><span data-stu-id="2517c-351">Placing a route attribute on the controller makes **all** actions in the controller use attribute routing.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet)]

<span data-ttu-id="2517c-352">V předchozím příkladu:</span><span class="sxs-lookup"><span data-stu-id="2517c-352">In the preceding example:</span></span>

* <span data-ttu-id="2517c-353">Cesta URL `/products` může odpovídat `ProductsApi.ListProducts`</span><span class="sxs-lookup"><span data-stu-id="2517c-353">The URL path `/products` can match `ProductsApi.ListProducts`</span></span>
* <span data-ttu-id="2517c-354">Cesta URL `/products/5` může odpovídat `ProductsApi.GetProduct(int)`.</span><span class="sxs-lookup"><span data-stu-id="2517c-354">The URL path `/products/5` can match `ProductsApi.GetProduct(int)`.</span></span>

<span data-ttu-id="2517c-355">Obě tyto akce odpovídají pouze `GET` HTTP, protože jsou označeny atributem `[HttpGet]`.</span><span class="sxs-lookup"><span data-stu-id="2517c-355">Both of these actions only match HTTP `GET` because they're marked with the `[HttpGet]` attribute.</span></span>

<span data-ttu-id="2517c-356">Šablony směrování použité pro akci, která začíná na `/` nebo `~/` se nekombinují se šablonami směrování použitými pro kontroler.</span><span class="sxs-lookup"><span data-stu-id="2517c-356">Route templates applied to an action that begin with `/` or `~/` don't get combined with route templates applied to the controller.</span></span> <span data-ttu-id="2517c-357">Následující příklad odpovídá sadě cest URL podobně jako výchozí trasa.</span><span class="sxs-lookup"><span data-stu-id="2517c-357">The following example matches a set of URL paths similar to the default route.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet)]

<span data-ttu-id="2517c-358">Následující tabulka popisuje atributy `[Route]` v předchozím kódu:</span><span class="sxs-lookup"><span data-stu-id="2517c-358">The following table explains the `[Route]` attributes in the preceding code:</span></span>

| <span data-ttu-id="2517c-359">Atribut</span><span class="sxs-lookup"><span data-stu-id="2517c-359">Attribute</span></span>               | <span data-ttu-id="2517c-360">Kombinuje s `[Route("Home")]`</span><span class="sxs-lookup"><span data-stu-id="2517c-360">Combines with `[Route("Home")]`</span></span> | <span data-ttu-id="2517c-361">Definuje šablonu směrování.</span><span class="sxs-lookup"><span data-stu-id="2517c-361">Defines route template</span></span> |
| ----------------- | ------------ | --------- |
| `[Route("")]` | <span data-ttu-id="2517c-362">Ano</span><span class="sxs-lookup"><span data-stu-id="2517c-362">Yes</span></span> | `"Home"` |
| `[Route("Index")]` | <span data-ttu-id="2517c-363">Ano</span><span class="sxs-lookup"><span data-stu-id="2517c-363">Yes</span></span> | `"Home/Index"` |
| `[Route("/")]` | <span data-ttu-id="2517c-364">**Ne**</span><span class="sxs-lookup"><span data-stu-id="2517c-364">**No**</span></span> | `""` |
| `[Route("About")]` | <span data-ttu-id="2517c-365">Ano</span><span class="sxs-lookup"><span data-stu-id="2517c-365">Yes</span></span> | `"Home/About"` |

<a name="routing-ordering-ref-label"></a>
<a name="oar"></a>

### <a name="attribute-route-order"></a><span data-ttu-id="2517c-366">Pořadí směrování atributu</span><span class="sxs-lookup"><span data-stu-id="2517c-366">Attribute route order</span></span>

<span data-ttu-id="2517c-367">Směrování sestaví strom a porovnává se všemi koncovými body současně:</span><span class="sxs-lookup"><span data-stu-id="2517c-367">Routing builds a tree and matches all endpoints simultaneously:</span></span>

* <span data-ttu-id="2517c-368">Položky směrování se chovají stejně, jako kdyby byly umístěny v ideálním pořadí.</span><span class="sxs-lookup"><span data-stu-id="2517c-368">The route entries behave as if placed in an ideal ordering.</span></span>
* <span data-ttu-id="2517c-369">Nejvíce konkrétní trasy mají možnost provést před obecnější trasy.</span><span class="sxs-lookup"><span data-stu-id="2517c-369">The most specific routes have a chance to execute before the more general routes.</span></span>

<span data-ttu-id="2517c-370">Například trasa atributu jako `blog/search/{topic}` je konkrétnější než trasa atributu jako `blog/{*article}`.</span><span class="sxs-lookup"><span data-stu-id="2517c-370">For example, an attribute route like `blog/search/{topic}` is more specific than an attribute route like `blog/{*article}`.</span></span> <span data-ttu-id="2517c-371">`blog/search/{topic}` trasa má ve výchozím nastavení vyšší prioritu, protože je konkrétnější.</span><span class="sxs-lookup"><span data-stu-id="2517c-371">The `blog/search/{topic}` route has higher priority, by default, because it's more specific.</span></span> <span data-ttu-id="2517c-372">Pomocí [konvenčního směrování](#cr)zodpovídá vývojář za umístění tras v požadovaném pořadí.</span><span class="sxs-lookup"><span data-stu-id="2517c-372">Using [conventional routing](#cr), the developer is responsible for placing routes in the desired order.</span></span>

<span data-ttu-id="2517c-373">Trasy atributů mohou konfigurovat objednávku pomocí vlastnosti <xref:Microsoft.AspNetCore.Mvc.RouteAttribute.Order>.</span><span class="sxs-lookup"><span data-stu-id="2517c-373">Attribute routes can configure an order using the <xref:Microsoft.AspNetCore.Mvc.RouteAttribute.Order> property.</span></span> <span data-ttu-id="2517c-374">Všechny [atributy trasy](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) , které jsou součástí rozhraní, zahrnují `Order`.</span><span class="sxs-lookup"><span data-stu-id="2517c-374">All of the framework provided [route attributes](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) include `Order` .</span></span> <span data-ttu-id="2517c-375">Trasy jsou zpracovávány podle vzestupného řazení vlastnosti `Order`.</span><span class="sxs-lookup"><span data-stu-id="2517c-375">Routes are processed according to an ascending sort of the `Order` property.</span></span> <span data-ttu-id="2517c-376">Výchozí pořadí je `0`.</span><span class="sxs-lookup"><span data-stu-id="2517c-376">The default order is `0`.</span></span> <span data-ttu-id="2517c-377">Nastavení trasy pomocí `Order = -1` spustí před trasami, které nenastaví objednávku.</span><span class="sxs-lookup"><span data-stu-id="2517c-377">Setting a route using `Order = -1` runs before routes that don't set an order.</span></span> <span data-ttu-id="2517c-378">Nastavení trasy pomocí `Order = 1` spustí se po výchozím řazení trasy.</span><span class="sxs-lookup"><span data-stu-id="2517c-378">Setting a route using `Order = 1` runs after default route ordering.</span></span>

<span data-ttu-id="2517c-379">**Nepoužívejte** v závislosti na `Order`.</span><span class="sxs-lookup"><span data-stu-id="2517c-379">**Avoid** depending on `Order`.</span></span> <span data-ttu-id="2517c-380">Pokud je místo na adrese URL aplikace nutné správně směrovat hodnoty pořadí, je pravděpodobné, že budou i u klientů matoucí.</span><span class="sxs-lookup"><span data-stu-id="2517c-380">If an app's URL-space requires explicit order values to route correctly, then it's likely confusing to clients as well.</span></span> <span data-ttu-id="2517c-381">Obecně platí, že směrování atributů vybere správnou trasu s odpovídající adresou URL.</span><span class="sxs-lookup"><span data-stu-id="2517c-381">In general, attribute routing selects the correct route with URL matching.</span></span> <span data-ttu-id="2517c-382">Pokud výchozí pořadí použité pro generování adresy URL nefunguje, je použití názvu trasy jako přepsání obvykle jednodušší než použití vlastnosti `Order`.</span><span class="sxs-lookup"><span data-stu-id="2517c-382">If the default order used for URL generation isn't working, using a route name as an override is usually simpler than applying the `Order` property.</span></span>

<span data-ttu-id="2517c-383">Vezměte v úvahu následující dva řadiče, které definují obě trasy, které odpovídají `/home`:</span><span class="sxs-lookup"><span data-stu-id="2517c-383">Consider the following two controllers which both define the route matching `/home`:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet2)]

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemoController.cs?name=snippet)]

<span data-ttu-id="2517c-384">Požadavek `/home` s předchozím kódem vyvolá výjimku, která je podobná následující:</span><span class="sxs-lookup"><span data-stu-id="2517c-384">Requesting `/home` with the preceding code throws an exception similar to the following:</span></span>

```text
AmbiguousMatchException: The request matched multiple endpoints. Matches:

 WebMvcRouting.Controllers.HomeController.Index
 WebMvcRouting.Controllers.MyDemoController.MyIndex
```

<span data-ttu-id="2517c-385">Přidání `Order` k jednomu z atributů trasy řeší nejednoznačnost:</span><span class="sxs-lookup"><span data-stu-id="2517c-385">Adding `Order` to one of the route attributes resolves the ambiguity:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemo3Controller.cs?name=snippet3& highlight=2)]

<span data-ttu-id="2517c-386">V předchozím kódu `/home` spouští koncový bod `HomeController.Index`.</span><span class="sxs-lookup"><span data-stu-id="2517c-386">With the preceding code, `/home` runs the `HomeController.Index` endpoint.</span></span> <span data-ttu-id="2517c-387">Chcete-li získat `MyDemoController.MyIndex`, požádejte o `/home/MyIndex`.</span><span class="sxs-lookup"><span data-stu-id="2517c-387">To get to the `MyDemoController.MyIndex`, request `/home/MyIndex`.</span></span> <span data-ttu-id="2517c-388">**Poznámka:**</span><span class="sxs-lookup"><span data-stu-id="2517c-388">**Note**:</span></span>

* <span data-ttu-id="2517c-389">Předchozí kód je příkladem nebo nekvalitní návrh směrování.</span><span class="sxs-lookup"><span data-stu-id="2517c-389">The preceding code is an example or poor routing design.</span></span> <span data-ttu-id="2517c-390">Byla použita k ilustraci vlastnosti `Order`.</span><span class="sxs-lookup"><span data-stu-id="2517c-390">It was used to illustrate the `Order` property.</span></span>
* <span data-ttu-id="2517c-391">Vlastnost `Order` jenom vyřeší nejednoznačnost, takže se tato šablona nedá spárovat.</span><span class="sxs-lookup"><span data-stu-id="2517c-391">The `Order` property only resolves the ambiguity, that template cannot be matched.</span></span> <span data-ttu-id="2517c-392">Je lepší odebrat šablonu `[Route("Home")]`.</span><span class="sxs-lookup"><span data-stu-id="2517c-392">It would be better to remove the `[Route("Home")]` template.</span></span>

<span data-ttu-id="2517c-393">Informace o pořadí směrování pomocí Razor Pages najdete v tématu [Razor Pages konvence směrování a aplikací: pořadí směrování](xref:razor-pages/razor-pages-conventions#route-order) .</span><span class="sxs-lookup"><span data-stu-id="2517c-393">See [Razor Pages route and app conventions: Route order](xref:razor-pages/razor-pages-conventions#route-order) for information on route order with Razor Pages.</span></span>

<span data-ttu-id="2517c-394">V některých případech se k chybě HTTP 500 vrátí nejednoznačné trasy.</span><span class="sxs-lookup"><span data-stu-id="2517c-394">In some cases, an HTTP 500 error is returned with ambiguous routes.</span></span> <span data-ttu-id="2517c-395">Pomocí [protokolování](xref:fundamentals/logging/index) zjistíte, které koncové body způsobily `AmbiguousMatchException`.</span><span class="sxs-lookup"><span data-stu-id="2517c-395">Use [logging](xref:fundamentals/logging/index) to see which endpoints caused the `AmbiguousMatchException`.</span></span>

<a name="routing-token-replacement-templates-ref-label"></a>

## <a name="token-replacement-in-route-templates-controller-action-area"></a><span data-ttu-id="2517c-396">Nahrazení tokenu v šablonách směrování [Controller], [akce], [oblast]</span><span class="sxs-lookup"><span data-stu-id="2517c-396">Token replacement in route templates [controller], [action], [area]</span></span>

<span data-ttu-id="2517c-397">Pro usnadnění práce, trasy atributů podporují nahrazení tokenů pro rezervované parametry tras uzavřením tokenu v jedné z následujících možností:</span><span class="sxs-lookup"><span data-stu-id="2517c-397">For convenience, attribute routes support token replacement for reserved route parameters by enclosing a token in one of the following:</span></span>

* <span data-ttu-id="2517c-398">Hranaté závorky: `[]`</span><span class="sxs-lookup"><span data-stu-id="2517c-398">Square braces: `[]`</span></span>
* <span data-ttu-id="2517c-399">Složené závorky: `{}`</span><span class="sxs-lookup"><span data-stu-id="2517c-399">Curly braces: `{}`</span></span>

<span data-ttu-id="2517c-400">Tokeny `[action]`, `[area]`a `[controller]` budou nahrazeny hodnotami názvu akce, názvu oblasti a názvu kontroleru z akce, kde je trasa definována:</span><span class="sxs-lookup"><span data-stu-id="2517c-400">The tokens `[action]`, `[area]`, and `[controller]` are replaced with the values of the action name, area name, and controller name from the action where the route is defined:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet)]

<span data-ttu-id="2517c-401">V předchozím kódu:</span><span class="sxs-lookup"><span data-stu-id="2517c-401">In the preceding code:</span></span>

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet10)]

  * <span data-ttu-id="2517c-402">Odpovídá `/Products0/List`</span><span class="sxs-lookup"><span data-stu-id="2517c-402">Matches `/Products0/List`</span></span>

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet11)]

  * <span data-ttu-id="2517c-403">Odpovídá `/Products0/Edit/{id}`</span><span class="sxs-lookup"><span data-stu-id="2517c-403">Matches `/Products0/Edit/{id}`</span></span>

<span data-ttu-id="2517c-404">Nahrazení tokenu probíhá jako poslední krok při vytváření tras atributů.</span><span class="sxs-lookup"><span data-stu-id="2517c-404">Token replacement occurs as the last step of building the attribute routes.</span></span> <span data-ttu-id="2517c-405">Předchozí příklad se chová stejně jako následující kód:</span><span class="sxs-lookup"><span data-stu-id="2517c-405">The preceding example behaves the same as the following code:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet20)]

[!INCLUDE[](~/includes/MTcomments.md)]

<span data-ttu-id="2517c-406">Trasy atributů je také možné kombinovat s dědičností.</span><span class="sxs-lookup"><span data-stu-id="2517c-406">Attribute routes can also be combined with inheritance.</span></span> <span data-ttu-id="2517c-407">Toto je výkonné v kombinaci s náhradou tokenu.</span><span class="sxs-lookup"><span data-stu-id="2517c-407">This is powerful combined with token replacement.</span></span> <span data-ttu-id="2517c-408">Nahrazení tokenu platí také pro názvy tras definované směrováními atributů.</span><span class="sxs-lookup"><span data-stu-id="2517c-408">Token replacement also applies to route names defined by attribute routes.</span></span>
<span data-ttu-id="2517c-409">`[Route("[controller]/[action]", Name="[controller]_[action]")]`pro každou akci vygeneruje jedinečný název trasy:</span><span class="sxs-lookup"><span data-stu-id="2517c-409">`[Route("[controller]/[action]", Name="[controller]_[action]")]`generates a unique route name for each action:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet5)]

<span data-ttu-id="2517c-410">Nahrazení tokenu platí také pro názvy tras definované směrováními atributů.</span><span class="sxs-lookup"><span data-stu-id="2517c-410">Token replacement also applies to route names defined by attribute routes.</span></span>
`[Route("[controller]/[action]", Name="[controller]_[action]")]`
<span data-ttu-id="2517c-411">vygeneruje jedinečný název trasy pro každou akci.</span><span class="sxs-lookup"><span data-stu-id="2517c-411">generates a unique route name for each action.</span></span>

<span data-ttu-id="2517c-412">Chcete-li porovnat oddělovač nahrazení tokenu literálu `[` nebo `]`, vydejte ho opakováním znaku (`[[` nebo `]]`).</span><span class="sxs-lookup"><span data-stu-id="2517c-412">To match the literal token replacement delimiter `[` or  `]`, escape it by repeating the character (`[[` or `]]`).</span></span>

<a name="routing-token-replacement-transformers-ref-label"></a>

### <a name="use-a-parameter-transformer-to-customize-token-replacement"></a><span data-ttu-id="2517c-413">Použití transformátoru parametrů k přizpůsobení náhrady tokenu</span><span class="sxs-lookup"><span data-stu-id="2517c-413">Use a parameter transformer to customize token replacement</span></span>

<span data-ttu-id="2517c-414">Nahrazení tokenu se dá přizpůsobit pomocí transformátoru parametrů.</span><span class="sxs-lookup"><span data-stu-id="2517c-414">Token replacement can be customized using a parameter transformer.</span></span> <span data-ttu-id="2517c-415">Transformátor parametru implementuje <xref:Microsoft.AspNetCore.Routing.IOutboundParameterTransformer> a transformuje hodnotu parametrů.</span><span class="sxs-lookup"><span data-stu-id="2517c-415">A parameter transformer implements <xref:Microsoft.AspNetCore.Routing.IOutboundParameterTransformer> and transforms the value of parameters.</span></span> <span data-ttu-id="2517c-416">Například vlastní parametr `SlugifyParameterTransformer` Transformer změní hodnotu trasy `SubscriptionManagement` na `subscription-management`:</span><span class="sxs-lookup"><span data-stu-id="2517c-416">For example, a custom `SlugifyParameterTransformer` parameter transformer changes the `SubscriptionManagement` route value to `subscription-management`:</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupSlugifyParamTransformer.cs?name=snippet2)]

<span data-ttu-id="2517c-417"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention> je konvence aplikačního modelu, kterou:</span><span class="sxs-lookup"><span data-stu-id="2517c-417">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention> is an application model convention that:</span></span>

* <span data-ttu-id="2517c-418">Aplikuje transformátor parametrů na všechny trasy atributů v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="2517c-418">Applies a parameter transformer to all attribute routes in an application.</span></span>
* <span data-ttu-id="2517c-419">Přizpůsobí hodnoty tokenu trasy při jejich nahrazení.</span><span class="sxs-lookup"><span data-stu-id="2517c-419">Customizes the attribute route token values as they are replaced.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/SubscriptionManagementController.cs?name=snippet)]

<span data-ttu-id="2517c-420">Předchozí metoda `ListAll` odpovídá `/subscription-management/list-all`.</span><span class="sxs-lookup"><span data-stu-id="2517c-420">The preceding `ListAll` method matches `/subscription-management/list-all`.</span></span>

<span data-ttu-id="2517c-421">`RouteTokenTransformerConvention` je v `ConfigureServices`zaregistrován jako možnost.</span><span class="sxs-lookup"><span data-stu-id="2517c-421">The `RouteTokenTransformerConvention` is registered as an option in `ConfigureServices`.</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupSlugifyParamTransformer.cs?name=snippet)]

<span data-ttu-id="2517c-422">Definice popisu najdete v tématu [MDN web docs on](https://developer.mozilla.org/docs/Glossary/Slug) .</span><span class="sxs-lookup"><span data-stu-id="2517c-422">See [MDN web docs on Slug](https://developer.mozilla.org/docs/Glossary/Slug) for the definition of Slug.</span></span>

<a name="routing-multiple-routes-ref-label"></a>

### <a name="multiple-attribute-routes"></a><span data-ttu-id="2517c-423">Trasy s více atributy</span><span class="sxs-lookup"><span data-stu-id="2517c-423">Multiple attribute routes</span></span>

<span data-ttu-id="2517c-424">Směrování atributů podporuje definování více tras, které dosáhnou stejné akce.</span><span class="sxs-lookup"><span data-stu-id="2517c-424">Attribute routing supports defining multiple routes that reach the same action.</span></span> <span data-ttu-id="2517c-425">Nejběžnějším využitím je napodobení chování výchozí konvenční trasy, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="2517c-425">The most common usage of this is to mimic the behavior of the default conventional route as shown in the following example:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet6x)]

<span data-ttu-id="2517c-426">Vložení více atributů trasy na řadič znamená, že každý z nich kombinuje s každým atributem směrování na metodách akcí:</span><span class="sxs-lookup"><span data-stu-id="2517c-426">Putting multiple route attributes on the controller means that each one combines with each of the route attributes on the action methods:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet6)]

<span data-ttu-id="2517c-427">Všechna omezení trasy [příkazů http](#verb) implementují `IActionConstraint`.</span><span class="sxs-lookup"><span data-stu-id="2517c-427">All the [HTTP verb](#verb) route constraints implement `IActionConstraint`.</span></span>

<span data-ttu-id="2517c-428">Když je na akci nasazených několik atributů tras, které implementují <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint>:</span><span class="sxs-lookup"><span data-stu-id="2517c-428">When multiple route attributes that implement <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint> are placed on an action:</span></span>

* <span data-ttu-id="2517c-429">Každé omezení akce kombinuje se šablonou směrování použitou pro kontroler.</span><span class="sxs-lookup"><span data-stu-id="2517c-429">Each action constraint combines with the route template applied to the controller.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet7)]

<span data-ttu-id="2517c-430">Použití více tras na akcích může vypadat jako užitečné a výkonné, takže je lepší zachovat místo na adrese URL vaší aplikace základní a dobře definovaný.</span><span class="sxs-lookup"><span data-stu-id="2517c-430">Using multiple routes on actions might seem useful and powerful, it's better to keep your app's URL space basic and well defined.</span></span> <span data-ttu-id="2517c-431">V případě potřeby použijte více tras **na akcích** , například pro podporu stávajících klientů.</span><span class="sxs-lookup"><span data-stu-id="2517c-431">Use multiple routes on actions **only** where needed, for example, to support existing clients.</span></span>

<a name="routing-attr-options"></a>

### <a name="specifying-attribute-route-optional-parameters-default-values-and-constraints"></a><span data-ttu-id="2517c-432">Určení volitelných parametrů trasy atributu, výchozích hodnot a omezení</span><span class="sxs-lookup"><span data-stu-id="2517c-432">Specifying attribute route optional parameters, default values, and constraints</span></span>

<span data-ttu-id="2517c-433">Trasy atributů podporují stejnou vloženou syntaxi jako konvenční trasy, aby bylo možné zadat volitelné parametry, výchozí hodnoty a omezení.</span><span class="sxs-lookup"><span data-stu-id="2517c-433">Attribute routes support the same inline syntax as conventional routes to specify optional parameters, default values, and constraints.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet8&highlight=3)]

<span data-ttu-id="2517c-434">V předchozím kódu `[HttpPost("product/{id:int}")]` použije omezení trasy.</span><span class="sxs-lookup"><span data-stu-id="2517c-434">In the preceding code, `[HttpPost("product/{id:int}")]` applies a route constraint.</span></span> <span data-ttu-id="2517c-435">Akce `ProductsController.ShowProduct` odpovídá pouze cestou URL jako `/product/3`.</span><span class="sxs-lookup"><span data-stu-id="2517c-435">The `ProductsController.ShowProduct` action is matched only by URL paths like `/product/3`.</span></span> <span data-ttu-id="2517c-436">Část šablony trasy `{id:int}` omezuje segment jenom na celá čísla.</span><span class="sxs-lookup"><span data-stu-id="2517c-436">The route template portion `{id:int}` constrains that segment to only integers.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet24)]

<span data-ttu-id="2517c-437">Podrobný popis syntaxe šablony směrování naleznete v tématu Referenční dokumentace k [šabloně směrování](xref:fundamentals/routing#route-template-reference) .</span><span class="sxs-lookup"><span data-stu-id="2517c-437">See [Route Template Reference](xref:fundamentals/routing#route-template-reference) for a detailed description of route template syntax.</span></span>

<a name="routing-cust-rt-attr-irt-ref-label"></a>

### <a name="custom-route-attributes-using-iroutetemplateprovider"></a><span data-ttu-id="2517c-438">Vlastní atributy směrování pomocí IRouteTemplateProvider</span><span class="sxs-lookup"><span data-stu-id="2517c-438">Custom route attributes using IRouteTemplateProvider</span></span>

<span data-ttu-id="2517c-439">Všechny [atributy trasy](#rt) implementují <xref:Microsoft.AspNetCore.Mvc.Routing.IRouteTemplateProvider>.</span><span class="sxs-lookup"><span data-stu-id="2517c-439">All of the [route attributes](#rt) implement <xref:Microsoft.AspNetCore.Mvc.Routing.IRouteTemplateProvider>.</span></span> <span data-ttu-id="2517c-440">Modul runtime ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="2517c-440">The ASP.NET Core runtime:</span></span>

* <span data-ttu-id="2517c-441">Vyhledá atributy tříd kontroleru a metody akcí při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="2517c-441">Looks for attributes on controller classes and action methods when the app starts.</span></span>
* <span data-ttu-id="2517c-442">Používá atributy, které implementují `IRouteTemplateProvider` k sestavení počáteční sady tras.</span><span class="sxs-lookup"><span data-stu-id="2517c-442">Uses the attributes that implement `IRouteTemplateProvider` to build the initial set of routes.</span></span>

<span data-ttu-id="2517c-443">Implementujte `IRouteTemplateProvider`, abyste definovali vlastní atributy směrování.</span><span class="sxs-lookup"><span data-stu-id="2517c-443">Implement `IRouteTemplateProvider` to define custom route attributes.</span></span> <span data-ttu-id="2517c-444">Každý `IRouteTemplateProvider` umožňuje definovat jednu trasu s vlastní šablonou směrování, objednávkou a názvem:</span><span class="sxs-lookup"><span data-stu-id="2517c-444">Each `IRouteTemplateProvider` allows you to define a single route with a custom route template, order, and name:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyTestApiController.cs?name=snippet&highlight=1-10)]

<span data-ttu-id="2517c-445">Předchozí metoda `Get` vrátí `Order = 2, Template = api/MyTestApi`.</span><span class="sxs-lookup"><span data-stu-id="2517c-445">The preceding `Get` method returns `Order = 2, Template = api/MyTestApi`.</span></span>

<a name="routing-app-model-ref-label"></a>

### <a name="use-application-model-to-customize-attribute-routes"></a><span data-ttu-id="2517c-446">Použití aplikačního modelu k přizpůsobení tras atributů</span><span class="sxs-lookup"><span data-stu-id="2517c-446">Use application model to customize attribute routes</span></span>

<span data-ttu-id="2517c-447">Aplikační model:</span><span class="sxs-lookup"><span data-stu-id="2517c-447">The application model:</span></span>

* <span data-ttu-id="2517c-448">Je objektovým modelem vytvořeným při spuštění.</span><span class="sxs-lookup"><span data-stu-id="2517c-448">Is an object model created at startup.</span></span>
* <span data-ttu-id="2517c-449">Obsahuje všechna metadata používaná ASP.NET Core ke směrování a provádění akcí v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="2517c-449">Contains all of the metadata used by ASP.NET Core to route and execute the actions in an app.</span></span>

<span data-ttu-id="2517c-450">Aplikační model zahrnuje všechna data získaná z atributů směrování.</span><span class="sxs-lookup"><span data-stu-id="2517c-450">The application model includes all of the data gathered from route attributes.</span></span> <span data-ttu-id="2517c-451">Data z atributů tras poskytuje implementace `IRouteTemplateProvider`.</span><span class="sxs-lookup"><span data-stu-id="2517c-451">The data from route attributes is provided by the `IRouteTemplateProvider` implementation.</span></span> <span data-ttu-id="2517c-452">Úmluvy</span><span class="sxs-lookup"><span data-stu-id="2517c-452">Conventions:</span></span>

* <span data-ttu-id="2517c-453">Lze zapsat pro úpravu modelu aplikace, aby bylo možné přizpůsobit způsob, jakým se směrování chová.</span><span class="sxs-lookup"><span data-stu-id="2517c-453">Can be written to modify the application model to customize how routing behaves.</span></span>
* <span data-ttu-id="2517c-454">Jsou čteny při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="2517c-454">Are read at app startup.</span></span>

<span data-ttu-id="2517c-455">V této části najdete základní příklad přizpůsobení směrování pomocí aplikačního modelu.</span><span class="sxs-lookup"><span data-stu-id="2517c-455">This section shows a basic example of customizing routing using application model.</span></span> <span data-ttu-id="2517c-456">Následující kód vytvoří trasy přibližně v souladu se strukturou složek v projektu.</span><span class="sxs-lookup"><span data-stu-id="2517c-456">The following code makes routes roughly line up with the folder structure of the project.</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/NamespaceRoutingConvention.cs?name=snippet)]

<span data-ttu-id="2517c-457">Následující kód brání použití konvence `namespace` u řadičů, které jsou směrovány atributem:</span><span class="sxs-lookup"><span data-stu-id="2517c-457">The following code prevents the `namespace` convention from being applied to controllers that are attribute routed:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/NamespaceRoutingConvention.cs?name=snippet2)]

<span data-ttu-id="2517c-458">Například následující kontroler nepoužívá `NamespaceRoutingConvention`:</span><span class="sxs-lookup"><span data-stu-id="2517c-458">For example, the following controller doesn't use `NamespaceRoutingConvention`:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/ManagersController.cs?name=snippet&highlight=1)]

<span data-ttu-id="2517c-459">Metoda `NamespaceRoutingConvention.Apply`:</span><span class="sxs-lookup"><span data-stu-id="2517c-459">The `NamespaceRoutingConvention.Apply` method:</span></span>

* <span data-ttu-id="2517c-460">Neprovede žádnou akci, pokud je řadič směrován do atributu.</span><span class="sxs-lookup"><span data-stu-id="2517c-460">Does nothing if the controller is attribute routed.</span></span>
* <span data-ttu-id="2517c-461">Nastaví šablonu řadičů na základě `namespace`se základní `namespace`ou odebraný.</span><span class="sxs-lookup"><span data-stu-id="2517c-461">Sets the controllers template based on the `namespace`, with the base `namespace` removed.</span></span>

<span data-ttu-id="2517c-462">`NamespaceRoutingConvention` lze použít v `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="2517c-462">The `NamespaceRoutingConvention` can be applied in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/Startup.cs?name=snippet&highlight=1,14-18)]

<span data-ttu-id="2517c-463">Zvažte například následující kontroler:</span><span class="sxs-lookup"><span data-stu-id="2517c-463">For example, consider the following controller:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/UsersController.cs)]

<span data-ttu-id="2517c-464">V předchozím kódu:</span><span class="sxs-lookup"><span data-stu-id="2517c-464">In the preceding code:</span></span>

* <span data-ttu-id="2517c-465">Základní `namespace` je `My.Application`.</span><span class="sxs-lookup"><span data-stu-id="2517c-465">The base `namespace` is `My.Application`.</span></span>
* <span data-ttu-id="2517c-466">Úplný název předchozího kontroleru je `My.Application.Admin.Controllers.UsersController`.</span><span class="sxs-lookup"><span data-stu-id="2517c-466">The full name of the preceding controller is `My.Application.Admin.Controllers.UsersController`.</span></span>
* <span data-ttu-id="2517c-467">`NamespaceRoutingConvention` nastaví šablonu řadičů na `Admin/Controllers/Users/[action]/{id?`.</span><span class="sxs-lookup"><span data-stu-id="2517c-467">The `NamespaceRoutingConvention` sets the controllers template to `Admin/Controllers/Users/[action]/{id?`.</span></span>

<span data-ttu-id="2517c-468">`NamespaceRoutingConvention` lze použít také jako atribut na řadiči:</span><span class="sxs-lookup"><span data-stu-id="2517c-468">The `NamespaceRoutingConvention` can also be applied as an attribute on a controller:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/TestController.cs?name=snippet&highlight=1)]

<a name="routing-mixed-ref-label"></a>

## <a name="mixed-routing-attribute-routing-vs-conventional-routing"></a><span data-ttu-id="2517c-469">Kombinované směrování: směrování atributů vs. konvenční směrování</span><span class="sxs-lookup"><span data-stu-id="2517c-469">Mixed routing: Attribute routing vs conventional routing</span></span>

<span data-ttu-id="2517c-470">Aplikace ASP.NET Core můžou kombinovat použití konvenčního směrování a směrování atributů.</span><span class="sxs-lookup"><span data-stu-id="2517c-470">ASP.NET Core apps can mix the use of conventional routing and attribute routing.</span></span> <span data-ttu-id="2517c-471">Pro řadiče, které obsluhují stránky HTML pro prohlížeče, a směrování atributů pro řadiče obsluhující rozhraní REST API se obvykle používá konvenční trasy.</span><span class="sxs-lookup"><span data-stu-id="2517c-471">It's typical to use conventional routes for controllers serving HTML pages for browsers, and attribute routing for controllers serving REST APIs.</span></span>

<span data-ttu-id="2517c-472">Akce jsou směrovány buď podle konvence nebo směrovaného atributu.</span><span class="sxs-lookup"><span data-stu-id="2517c-472">Actions are either conventionally routed or attribute routed.</span></span> <span data-ttu-id="2517c-473">Umístěním trasy do kontroleru nebo akce se nasměruje atribut IT.</span><span class="sxs-lookup"><span data-stu-id="2517c-473">Placing a route on the controller or the action makes it attribute routed.</span></span> <span data-ttu-id="2517c-474">Akce definující trasy atributů nemůžou být dostupné prostřednictvím konvenčních tras a naopak.</span><span class="sxs-lookup"><span data-stu-id="2517c-474">Actions that define attribute routes cannot be reached through the conventional routes and vice-versa.</span></span> <span data-ttu-id="2517c-475">**Libovolný** atribut směrování v řadiči provádí směrování **všech** akcí v atributu kontroleru.</span><span class="sxs-lookup"><span data-stu-id="2517c-475">**Any** route attribute on the controller makes **all** actions in the controller attribute routed.</span></span>

<span data-ttu-id="2517c-476">Směrování atributů a konvenční směrování používají stejný modul směrování.</span><span class="sxs-lookup"><span data-stu-id="2517c-476">Attribute routing and conventional routing use the same routing engine.</span></span>

<a name="routing-url-gen-ref-label"></a>
<a name="ambient"></a>

## <a name="url-generation-and-ambient-values"></a><span data-ttu-id="2517c-477">Generování adresy URL a okolní hodnoty</span><span class="sxs-lookup"><span data-stu-id="2517c-477">URL Generation and ambient values</span></span>

<span data-ttu-id="2517c-478">Aplikace můžou použít funkce generování adresy URL směrování k vygenerování odkazů na akce v adresách URL.</span><span class="sxs-lookup"><span data-stu-id="2517c-478">Apps can use routing URL generation features to generate URL links to actions.</span></span> <span data-ttu-id="2517c-479">Generování adres URL eliminuje adresy URL zakódujeme, takže je kód robustnější a udržovatelný.</span><span class="sxs-lookup"><span data-stu-id="2517c-479">Generating URLs eliminates hardcoding URLs, making code more robust and maintainable.</span></span> <span data-ttu-id="2517c-480">Tato část se zaměřuje na funkce generování adresy URL poskytované MVC a jenom základní informace o tom, jak generování adresy URL funguje.</span><span class="sxs-lookup"><span data-stu-id="2517c-480">This section focuses on the URL generation features provided by MVC and only cover basics of how URL generation works.</span></span> <span data-ttu-id="2517c-481">Podrobný popis generování adresy URL najdete v tématu věnovaném [Směrování](xref:fundamentals/routing) .</span><span class="sxs-lookup"><span data-stu-id="2517c-481">See [Routing](xref:fundamentals/routing) for a detailed description of URL generation.</span></span>

<span data-ttu-id="2517c-482">Rozhraní <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> je základním prvkem infrastruktury mezi MVC a směrováním pro generování adresy URL.</span><span class="sxs-lookup"><span data-stu-id="2517c-482">The <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> interface is the underlying element of infrastructure between MVC and routing for URL generation.</span></span> <span data-ttu-id="2517c-483">Instance `IUrlHelper` je k dispozici prostřednictvím vlastnosti `Url` v řadičích, zobrazeních a zobrazení komponent.</span><span class="sxs-lookup"><span data-stu-id="2517c-483">An instance of `IUrlHelper` is available through the `Url` property in controllers, views, and view components.</span></span>

<span data-ttu-id="2517c-484">V následujícím příkladu je `IUrlHelper` rozhraní použito prostřednictvím vlastnosti `Controller.Url` k vygenerování adresy URL jiné akci.</span><span class="sxs-lookup"><span data-stu-id="2517c-484">In the following example, the `IUrlHelper` interface is used through the `Controller.Url` property to generate a URL to another action.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGenerationController.cs?name=snippet_1)]

<span data-ttu-id="2517c-485">Pokud aplikace používá výchozí konvenční trasu, hodnota `url` proměnné je řetězec cesty URL `/UrlGeneration/Destination`.</span><span class="sxs-lookup"><span data-stu-id="2517c-485">If the app is using the default conventional route, the value of the `url` variable is the URL path string `/UrlGeneration/Destination`.</span></span> <span data-ttu-id="2517c-486">Tato cesta URL se vytvoří směrováním kombinováním:</span><span class="sxs-lookup"><span data-stu-id="2517c-486">This URL path is created by routing by combining:</span></span>

* <span data-ttu-id="2517c-487">Hodnoty tras z aktuální žádosti, které se nazývají **okolní hodnoty**.</span><span class="sxs-lookup"><span data-stu-id="2517c-487">The route values from the current request, which are called **ambient values**.</span></span>
* <span data-ttu-id="2517c-488">Hodnoty předané do `Url.Action` a nahrazování těchto hodnot do šablony trasy:</span><span class="sxs-lookup"><span data-stu-id="2517c-488">The values passed to `Url.Action` and substituting those values into the route template:</span></span>

``` text
ambient values: { controller = "UrlGeneration", action = "Source" }
values passed to Url.Action: { controller = "UrlGeneration", action = "Destination" }
route template: {controller}/{action}/{id?}

result: /UrlGeneration/Destination
```

<span data-ttu-id="2517c-489">Každý parametr trasy v šabloně směrování má jeho hodnotu nahrazenou odpovídajícími názvy hodnotami a okolními hodnotami.</span><span class="sxs-lookup"><span data-stu-id="2517c-489">Each route parameter in the route template has its value substituted by matching names with the values and ambient values.</span></span> <span data-ttu-id="2517c-490">Parametr trasy, který nemá hodnotu, může:</span><span class="sxs-lookup"><span data-stu-id="2517c-490">A route parameter that doesn't have a value can:</span></span>

* <span data-ttu-id="2517c-491">Pokud má jednu z těchto hodnot, použijte výchozí hodnotu.</span><span class="sxs-lookup"><span data-stu-id="2517c-491">Use a default value if it has one.</span></span>
* <span data-ttu-id="2517c-492">Přeskočeno, pokud je volitelné.</span><span class="sxs-lookup"><span data-stu-id="2517c-492">Be skipped if it's optional.</span></span> <span data-ttu-id="2517c-493">Například `id` ze šablony směrování `{controller}/{action}/{id?}`.</span><span class="sxs-lookup"><span data-stu-id="2517c-493">For example, the `id` from the  route template `{controller}/{action}/{id?}`.</span></span>

<span data-ttu-id="2517c-494">Generování adresy URL se nepovede, pokud libovolný požadovaný parametr trasy nemá odpovídající hodnotu.</span><span class="sxs-lookup"><span data-stu-id="2517c-494">URL generation fails if any required route parameter doesn't have a corresponding value.</span></span> <span data-ttu-id="2517c-495">Pokud se generování adresy URL u trasy nezdařilo, bude se zkoušet další trasa, dokud nebudou všechny trasy vyzkoušeny nebo nalezena shoda.</span><span class="sxs-lookup"><span data-stu-id="2517c-495">If URL generation fails for a route, the next route is tried until all routes have been tried or a match is found.</span></span>

<span data-ttu-id="2517c-496">Předchozí příklad `Url.Action` předpokládá [konvenční směrování](#cr).</span><span class="sxs-lookup"><span data-stu-id="2517c-496">The preceding example of `Url.Action` assumes [conventional routing](#cr).</span></span> <span data-ttu-id="2517c-497">Generování adresy URL funguje podobně jako [Směrování atributů](#ar), i když se koncepty liší.</span><span class="sxs-lookup"><span data-stu-id="2517c-497">URL generation works similarly with [attribute routing](#ar), though the concepts are different.</span></span> <span data-ttu-id="2517c-498">S konvenčním směrováním:</span><span class="sxs-lookup"><span data-stu-id="2517c-498">With conventional routing:</span></span>

* <span data-ttu-id="2517c-499">Hodnoty tras slouží k rozbalení šablony.</span><span class="sxs-lookup"><span data-stu-id="2517c-499">The route values are used to expand a template.</span></span>
* <span data-ttu-id="2517c-500">Hodnoty směrování pro `controller` a `action` obvykle se zobrazí v této šabloně.</span><span class="sxs-lookup"><span data-stu-id="2517c-500">The route values for `controller` and `action` usually appear in that template.</span></span> <span data-ttu-id="2517c-501">To se hodí, protože adresy URL, které odpovídají směrování, dodržují konvenci.</span><span class="sxs-lookup"><span data-stu-id="2517c-501">This works because the URLs matched by routing adhere to a convention.</span></span>

<span data-ttu-id="2517c-502">Následující příklad používá směrování atributů:</span><span class="sxs-lookup"><span data-stu-id="2517c-502">The following example uses attribute routing:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGenerationAttrController.cs?name=snippet_1)]

<span data-ttu-id="2517c-503">Akce `Source` v předchozím kódu generuje `custom/url/to/destination`.</span><span class="sxs-lookup"><span data-stu-id="2517c-503">The `Source` action in the preceding code generates `custom/url/to/destination`.</span></span>

<span data-ttu-id="2517c-504"><xref:Microsoft.AspNetCore.Routing.LinkGenerator> byla do ASP.NET Core 3,0 přidána jako alternativa k `IUrlHelper`.</span><span class="sxs-lookup"><span data-stu-id="2517c-504"><xref:Microsoft.AspNetCore.Routing.LinkGenerator> was added in ASP.NET Core 3.0 as an alternative to `IUrlHelper`.</span></span> <span data-ttu-id="2517c-505">`LinkGenerator` nabízí podobné, ale flexibilnější funkce.</span><span class="sxs-lookup"><span data-stu-id="2517c-505">`LinkGenerator` offers similar but more flexible functionality.</span></span> <span data-ttu-id="2517c-506">Každá druhá metoda na `IUrlHelper` má také odpovídající rodinu metod `LinkGenerator`.</span><span class="sxs-lookup"><span data-stu-id="2517c-506">Each other the methods on `IUrlHelper` has a corresponding family of methods on `LinkGenerator` as well.</span></span>

### <a name="generating-urls-by-action-name"></a><span data-ttu-id="2517c-507">Generování adres URL podle názvu akce</span><span class="sxs-lookup"><span data-stu-id="2517c-507">Generating URLs by action name</span></span>

<span data-ttu-id="2517c-508">[Adresa URL. Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*), [LinkGenerator. GetPathByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetPathByAction*)a všechny související přetížení jsou navržené tak, aby vygenerovaly cílový koncový bod zadáním názvu kontroleru a názvu akce.</span><span class="sxs-lookup"><span data-stu-id="2517c-508">[Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*), [LinkGenerator.GetPathByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetPathByAction*), and all related overloads all are designed to generate the target endpoint by specifying a controller name and action name.</span></span>

<span data-ttu-id="2517c-509">Při použití `Url.Action`jsou aktuální hodnoty trasy pro `controller` a `action` poskytovány modulem runtime:</span><span class="sxs-lookup"><span data-stu-id="2517c-509">When using `Url.Action`, the current route values for `controller` and `action` are provided by the runtime:</span></span>

* <span data-ttu-id="2517c-510">Hodnota `controller` a `action` jsou součástí [okolních hodnot](#ambient) a hodnot.</span><span class="sxs-lookup"><span data-stu-id="2517c-510">The value of `controller` and `action` are part of both [ambient values](#ambient) and values.</span></span> <span data-ttu-id="2517c-511">Metoda `Url.Action` vždy používá aktuální hodnoty `action` a `controller` a generuje cestu URL, která směruje na aktuální akci.</span><span class="sxs-lookup"><span data-stu-id="2517c-511">The method `Url.Action` always uses the current values of `action` and `controller` and generates a URL path that routes to the current action.</span></span>

<span data-ttu-id="2517c-512">Směrování se pokusí použít hodnoty v ambientních hodnotách k vyplnění informací, které nebyly poskytnuty při generování adresy URL.</span><span class="sxs-lookup"><span data-stu-id="2517c-512">Routing attempts to use the values in ambient values to fill in information that wasn't provided when generating a URL.</span></span> <span data-ttu-id="2517c-513">Vezměte v úvahu trasu, jako je `{a}/{b}/{c}/{d}` s okolními hodnotami `{ a = Alice, b = Bob, c = Carol, d = David }`:</span><span class="sxs-lookup"><span data-stu-id="2517c-513">Consider a route like `{a}/{b}/{c}/{d}` with ambient values `{ a = Alice, b = Bob, c = Carol, d = David }`:</span></span>

* <span data-ttu-id="2517c-514">Směrování má dostatek informací pro vygenerování adresy URL bez dalších hodnot.</span><span class="sxs-lookup"><span data-stu-id="2517c-514">Routing has enough information to generate a URL without any additional values.</span></span>
* <span data-ttu-id="2517c-515">Směrování má dostatek informací, protože všechny parametry tras mají hodnotu.</span><span class="sxs-lookup"><span data-stu-id="2517c-515">Routing has enough information because all route parameters have a value.</span></span>

<span data-ttu-id="2517c-516">Pokud je přidána hodnota `{ d = Donovan }`:</span><span class="sxs-lookup"><span data-stu-id="2517c-516">If the value `{ d = Donovan }` is added:</span></span>

* <span data-ttu-id="2517c-517">Hodnota `{ d = David }` je ignorována.</span><span class="sxs-lookup"><span data-stu-id="2517c-517">The value `{ d = David }` is ignored.</span></span>
* <span data-ttu-id="2517c-518">Vygenerovaná cesta URL je `Alice/Bob/Carol/Donovan`.</span><span class="sxs-lookup"><span data-stu-id="2517c-518">The generated URL path is `Alice/Bob/Carol/Donovan`.</span></span>

<span data-ttu-id="2517c-519">**Upozornění**: cesty URL jsou hierarchické.</span><span class="sxs-lookup"><span data-stu-id="2517c-519">**Warning**: URL paths are hierarchical.</span></span> <span data-ttu-id="2517c-520">V předchozím příkladu, pokud je přidána hodnota `{ c = Cheryl }`:</span><span class="sxs-lookup"><span data-stu-id="2517c-520">In the preceding example, if the value `{ c = Cheryl }` is added:</span></span>

* <span data-ttu-id="2517c-521">Obě hodnoty `{ c = Carol, d = David }` jsou ignorovány.</span><span class="sxs-lookup"><span data-stu-id="2517c-521">Both of the values `{ c = Carol, d = David }` are ignored.</span></span>
* <span data-ttu-id="2517c-522">Pro `d` se už nepoužívá žádná hodnota a generování adresy URL se nezdařilo.</span><span class="sxs-lookup"><span data-stu-id="2517c-522">There is no longer a value for `d` and URL generation fails.</span></span>
* <span data-ttu-id="2517c-523">Aby se vygenerovala adresa URL, musí být zadaná požadovaná hodnota `c` a `d`.</span><span class="sxs-lookup"><span data-stu-id="2517c-523">The desired values of `c` and `d` must be specified to generate a URL.</span></span>  

<span data-ttu-id="2517c-524">Můžete očekávat, že se tento problém bude narazit s výchozí `{controller}/{action}/{id?}`trasy.</span><span class="sxs-lookup"><span data-stu-id="2517c-524">You might expect to hit this problem with the default route `{controller}/{action}/{id?}`.</span></span> <span data-ttu-id="2517c-525">K tomuto problému dochází v praxi zřídka, protože `Url.Action` vždy explicitně určuje `controller` a `action` hodnotu.</span><span class="sxs-lookup"><span data-stu-id="2517c-525">This problem is rare in practice because `Url.Action` always explicitly specifies a `controller` and `action` value.</span></span>

<span data-ttu-id="2517c-526">Několik přetížení [adresy URL. akce](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) převezmou objekt hodnot směrování, aby poskytovala hodnoty pro parametry směrování jiné než `controller` a `action`.</span><span class="sxs-lookup"><span data-stu-id="2517c-526">Several overloads of [Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) take a route values object to provide values for route parameters other than `controller` and `action`.</span></span> <span data-ttu-id="2517c-527">Objekt hodnoty trasy se často používá s `id`.</span><span class="sxs-lookup"><span data-stu-id="2517c-527">The route values object is frequently used with `id`.</span></span> <span data-ttu-id="2517c-528">například `Url.Action("Buy", "Products", new { id = 17 })`.</span><span class="sxs-lookup"><span data-stu-id="2517c-528">For example, `Url.Action("Buy", "Products", new { id = 17 })`.</span></span> <span data-ttu-id="2517c-529">Objekt hodnot směrování:</span><span class="sxs-lookup"><span data-stu-id="2517c-529">The route values object:</span></span>

* <span data-ttu-id="2517c-530">Podle konvence je obvykle objekt anonymního typu.</span><span class="sxs-lookup"><span data-stu-id="2517c-530">By convention is usually an object of anonymous type.</span></span>
* <span data-ttu-id="2517c-531">Může se jednat o `IDictionary<>` nebo [POCO](https://wikipedia.org/wiki/Plain_old_CLR_object)).</span><span class="sxs-lookup"><span data-stu-id="2517c-531">Can be an `IDictionary<>` or a [POCO](https://wikipedia.org/wiki/Plain_old_CLR_object)).</span></span>

<span data-ttu-id="2517c-532">Všechny další hodnoty tras, které neodpovídají parametrům směrování, jsou vloženy do řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="2517c-532">Any additional route values that don't match route parameters are put in the query string.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/TestController.cs?name=snippet)]

<span data-ttu-id="2517c-533">Předchozí kód generuje `/Products/Buy/17?color=red`.</span><span class="sxs-lookup"><span data-stu-id="2517c-533">The preceding code generates `/Products/Buy/17?color=red`.</span></span>

<span data-ttu-id="2517c-534">Následující kód vygeneruje absolutní adresu URL:</span><span class="sxs-lookup"><span data-stu-id="2517c-534">The following code generates an absolute URL:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/TestController.cs?name=snippet2)]

<span data-ttu-id="2517c-535">Chcete-li vytvořit absolutní adresu URL, použijte jednu z následujících možností:</span><span class="sxs-lookup"><span data-stu-id="2517c-535">To create an absolute URL, use one of the following:</span></span>

* <span data-ttu-id="2517c-536">Přetížení, které přijímá `protocol`.</span><span class="sxs-lookup"><span data-stu-id="2517c-536">An overload that accepts a `protocol`.</span></span> <span data-ttu-id="2517c-537">Například předchozí kód.</span><span class="sxs-lookup"><span data-stu-id="2517c-537">For example, the preceding code.</span></span>
* <span data-ttu-id="2517c-538">[LinkGenerator. GetUriByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetUriByAction*), který ve výchozím nastavení generuje absolutní identifikátory URI.</span><span class="sxs-lookup"><span data-stu-id="2517c-538">[LinkGenerator.GetUriByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetUriByAction*), which generates absolute URIs by default.</span></span>

<a name="routing-gen-urls-route-ref-label"></a>

### <a name="generate-urls-by-route"></a><span data-ttu-id="2517c-539">Generovat adresy URL podle směrování</span><span class="sxs-lookup"><span data-stu-id="2517c-539">Generate URLs by route</span></span>

<span data-ttu-id="2517c-540">Předchozí kód ukázal generování adresy URL předáním do kontroleru a názvu akce.</span><span class="sxs-lookup"><span data-stu-id="2517c-540">The preceding code demonstrated generating a URL by passing in the controller and action name.</span></span> <span data-ttu-id="2517c-541">`IUrlHelper` také poskytuje [adresu URL. RouteUrl](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.RouteUrl*) pro řadu metod.</span><span class="sxs-lookup"><span data-stu-id="2517c-541">`IUrlHelper` also provides the [Url.RouteUrl](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.RouteUrl*) family of methods.</span></span> <span data-ttu-id="2517c-542">Tyto metody jsou podobné jako [URL. Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*), ale nekopírují aktuální hodnoty `action` a `controller` na hodnoty trasy.</span><span class="sxs-lookup"><span data-stu-id="2517c-542">These methods are similar to [Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*), but they don't copy the current values of `action` and `controller` to the route values.</span></span> <span data-ttu-id="2517c-543">Nejběžnější využití `Url.RouteUrl`:</span><span class="sxs-lookup"><span data-stu-id="2517c-543">The most common usage of `Url.RouteUrl`:</span></span>

* <span data-ttu-id="2517c-544">Určuje název trasy pro vygenerování adresy URL.</span><span class="sxs-lookup"><span data-stu-id="2517c-544">Specifies a route name to generate the URL.</span></span>
* <span data-ttu-id="2517c-545">Obecně neurčuje kontrolér nebo název akce.</span><span class="sxs-lookup"><span data-stu-id="2517c-545">Generally doesn't specify a controller or action name.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGeneration2Controller.cs?name=snippet_1)]

<span data-ttu-id="2517c-546">Následující soubor Razor vygeneruje odkaz HTML na `Destination_Route`:</span><span class="sxs-lookup"><span data-stu-id="2517c-546">The following Razor file generates an HTML link to the `Destination_Route`:</span></span>

[!code-cshtml[](routing/samples/3.x/main/Views/Shared/MyLink.cshtml)]

<a name="routing-gen-urls-html-ref-label"></a>

### <a name="generate-urls-in-html-and-razor"></a><span data-ttu-id="2517c-547">Generování adres URL ve formátu HTML a Razor</span><span class="sxs-lookup"><span data-stu-id="2517c-547">Generate URLs in HTML and Razor</span></span>

<span data-ttu-id="2517c-548"><xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper> poskytuje <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.HtmlHelper> metody [HTML. BeginForm](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.BeginForm*) a [HTML. ActionLink](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.ActionLink*) k vygenerování `<form>` a `<a>` prvků v uvedeném pořadí.</span><span class="sxs-lookup"><span data-stu-id="2517c-548"><xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper> provides the <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.HtmlHelper> methods [Html.BeginForm](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.BeginForm*) and [Html.ActionLink](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.ActionLink*) to generate `<form>` and `<a>` elements respectively.</span></span> <span data-ttu-id="2517c-549">Tyto metody používají metodu [URL. Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) pro VYGENEROVÁNÍ adresy URL a přijímající podobné argumenty.</span><span class="sxs-lookup"><span data-stu-id="2517c-549">These methods use the [Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) method to generate a URL and they accept similar arguments.</span></span> <span data-ttu-id="2517c-550">`Url.RouteUrl` doprovodné funkce pro `HtmlHelper` jsou `Html.BeginRouteForm` a `Html.RouteLink` s podobnými funkcemi.</span><span class="sxs-lookup"><span data-stu-id="2517c-550">The `Url.RouteUrl` companions for `HtmlHelper` are `Html.BeginRouteForm` and `Html.RouteLink` which have similar functionality.</span></span>

<span data-ttu-id="2517c-551">TagHelpers vygeneruje adresy URL prostřednictvím `form` Taghelperu a `<a>` Taghelperu.</span><span class="sxs-lookup"><span data-stu-id="2517c-551">TagHelpers generate URLs through the `form` TagHelper and the `<a>` TagHelper.</span></span> <span data-ttu-id="2517c-552">Obě tato použití `IUrlHelper` pro jejich implementaci.</span><span class="sxs-lookup"><span data-stu-id="2517c-552">Both of these use `IUrlHelper` for their implementation.</span></span> <span data-ttu-id="2517c-553">Další informace najdete v tématu věnovaném [pomocníkům značek ve formulářích](xref:mvc/views/working-with-forms) .</span><span class="sxs-lookup"><span data-stu-id="2517c-553">See [Tag Helpers in forms](xref:mvc/views/working-with-forms) for more information.</span></span>

<span data-ttu-id="2517c-554">V zobrazeních jsou `IUrlHelper` k dispozici prostřednictvím vlastnosti `Url` pro všechny generace adres URL ad-hoc, které nejsou pokryté výše.</span><span class="sxs-lookup"><span data-stu-id="2517c-554">Inside views, the `IUrlHelper` is available through the `Url` property for any ad-hoc URL generation not covered by the above.</span></span>

<a name="routing-gen-urls-action-ref-label"></a>

### <a name="url-generation-in-action-results"></a><span data-ttu-id="2517c-555">Generování adresy URL ve výsledcích akce</span><span class="sxs-lookup"><span data-stu-id="2517c-555">URL generation in Action Results</span></span>

<span data-ttu-id="2517c-556">Předchozí příklady ukázaly použití `IUrlHelper` v kontroleru.</span><span class="sxs-lookup"><span data-stu-id="2517c-556">The preceding examples showed using `IUrlHelper` in a controller.</span></span> <span data-ttu-id="2517c-557">Nejběžnějším využitím v kontroleru je vygenerování adresy URL jako součást výsledku akce.</span><span class="sxs-lookup"><span data-stu-id="2517c-557">The most common usage in a controller is to generate a URL as part of an action result.</span></span>

<span data-ttu-id="2517c-558">Základní třídy <xref:Microsoft.AspNetCore.Mvc.ControllerBase> a <xref:Microsoft.AspNetCore.Mvc.Controller> poskytují pohodlný způsob pro výsledky akcí, které odkazují na jinou akci.</span><span class="sxs-lookup"><span data-stu-id="2517c-558">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase> and <xref:Microsoft.AspNetCore.Mvc.Controller> base classes provide convenience methods for action results that reference another action.</span></span> <span data-ttu-id="2517c-559">Jedním z typických použití je přesměrování po přijetí vstupu uživatele:</span><span class="sxs-lookup"><span data-stu-id="2517c-559">One typical usage is to redirect after accepting user input:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/CustomerController.cs?name=snippet)]

<span data-ttu-id="2517c-560">Metody pro vytváření výsledků, například <xref:Microsoft.AspNetCore.Mvc.ControllerBase.RedirectToAction*> a <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*>, následují podobně jako metody v `IUrlHelper`.</span><span class="sxs-lookup"><span data-stu-id="2517c-560">The action results factory methods such as <xref:Microsoft.AspNetCore.Mvc.ControllerBase.RedirectToAction*> and <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> follow a similar pattern to the methods on `IUrlHelper`.</span></span>

<a name="routing-dedicated-ref-label"></a>

### <a name="special-case-for-dedicated-conventional-routes"></a><span data-ttu-id="2517c-561">Zvláštní případ pro vyhrazené konvenční trasy</span><span class="sxs-lookup"><span data-stu-id="2517c-561">Special case for dedicated conventional routes</span></span>

<span data-ttu-id="2517c-562">[Konvenční směrování](#cr) může používat speciální druh definice trasy, která se označuje jako [vyhrazená konvenční trasa](#dcr).</span><span class="sxs-lookup"><span data-stu-id="2517c-562">[Conventional routing](#cr) can use a special kind of route definition called a [dedicated conventional route](#dcr).</span></span> <span data-ttu-id="2517c-563">V následujícím příkladu je trasa s názvem `blog` vyhrazenou konvenční trasou:</span><span class="sxs-lookup"><span data-stu-id="2517c-563">In the following example, the route named `blog` is a dedicated conventional route:</span></span>

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

<span data-ttu-id="2517c-564">Pomocí předchozích definic tras `Url.Action("Index", "Home")` generuje cestu k adrese URL `/` pomocí `default` trasy, ale proč?</span><span class="sxs-lookup"><span data-stu-id="2517c-564">Using the preceding route definitions, `Url.Action("Index", "Home")` generates the URL path `/` using the `default` route, but why?</span></span> <span data-ttu-id="2517c-565">Můžete odhadnout hodnoty tras, `{ controller = Home, action = Index }` by bylo dostatečné pro vygenerování adresy URL pomocí `blog`a výsledek by byl `/blog?action=Index&controller=Home`.</span><span class="sxs-lookup"><span data-stu-id="2517c-565">You might guess the route values `{ controller = Home, action = Index }` would be enough to generate a URL using `blog`, and the result would be `/blog?action=Index&controller=Home`.</span></span>

<span data-ttu-id="2517c-566">[Vyhrazené konvenční trasy](#dcr) spoléhají na speciální chování výchozích hodnot, které nemají odpovídající parametr trasy, který brání tomu, aby byla trasa příliš [hladkou](xref:fundamentals/routing#greedy) při generování adresy URL.</span><span class="sxs-lookup"><span data-stu-id="2517c-566">[Dedicated conventional routes](#dcr) rely on a special behavior of default values that don't have a corresponding route parameter that prevents the route from being too [greedy](xref:fundamentals/routing#greedy) with URL generation.</span></span> <span data-ttu-id="2517c-567">V tomto případě jsou `{ controller = Blog, action = Article }`výchozí hodnoty a ani `controller` ani `action` se nezobrazí jako parametr trasy.</span><span class="sxs-lookup"><span data-stu-id="2517c-567">In this case the default values are `{ controller = Blog, action = Article }`, and neither `controller` nor `action` appears as a route parameter.</span></span> <span data-ttu-id="2517c-568">Když směrování provádí generování adresy URL, zadané hodnoty musí odpovídat výchozím hodnotám.</span><span class="sxs-lookup"><span data-stu-id="2517c-568">When routing performs URL generation, the values provided must match the default values.</span></span> <span data-ttu-id="2517c-569">Generování adresy URL pomocí `blog` se nezdařilo, protože hodnoty `{ controller = Home, action = Index }` neodpovídají `{ controller = Blog, action = Article }`.</span><span class="sxs-lookup"><span data-stu-id="2517c-569">URL generation using `blog` fails because the values `{ controller = Home, action = Index }` don't match `{ controller = Blog, action = Article }`.</span></span> <span data-ttu-id="2517c-570">Směrování se pak vrátí k pokusu o `default`, který je úspěšný.</span><span class="sxs-lookup"><span data-stu-id="2517c-570">Routing then falls back to try `default`, which succeeds.</span></span>

<a name="routing-areas-ref-label"></a>

## <a name="areas"></a><span data-ttu-id="2517c-571">Oblasti</span><span class="sxs-lookup"><span data-stu-id="2517c-571">Areas</span></span>

<span data-ttu-id="2517c-572">[Oblasti](xref:mvc/controllers/areas) představují funkci MVC, která slouží k uspořádání souvisejících funkcí do skupiny jako samostatného:</span><span class="sxs-lookup"><span data-stu-id="2517c-572">[Areas](xref:mvc/controllers/areas) are an MVC feature used to organize related functionality into a group as a separate:</span></span>

* <span data-ttu-id="2517c-573">Obor názvů směrování pro akce kontroleru.</span><span class="sxs-lookup"><span data-stu-id="2517c-573">Routing namespace for controller actions.</span></span>
* <span data-ttu-id="2517c-574">Struktura složek pro zobrazení.</span><span class="sxs-lookup"><span data-stu-id="2517c-574">Folder structure for views.</span></span>

<span data-ttu-id="2517c-575">Použití oblastí umožňuje aplikaci mít více řadičů se stejným názvem, pokud mají různé oblasti.</span><span class="sxs-lookup"><span data-stu-id="2517c-575">Using areas allows an app to have multiple controllers with the same name, as long as they have different areas.</span></span> <span data-ttu-id="2517c-576">Použití oblastí vytvoří hierarchii pro účely směrování přidáním dalšího parametru trasy `area` `controller` a `action`.</span><span class="sxs-lookup"><span data-stu-id="2517c-576">Using areas creates a hierarchy for the purpose of routing by adding another route parameter, `area` to `controller` and `action`.</span></span> <span data-ttu-id="2517c-577">Tato část popisuje, jak směrování komunikuje s oblastmi.</span><span class="sxs-lookup"><span data-stu-id="2517c-577">This section discusses how routing interacts with areas.</span></span> <span data-ttu-id="2517c-578">Podrobnosti o tom, jak se používají oblasti se zobrazeními, najdete v části [oblasti](xref:mvc/controllers/areas) .</span><span class="sxs-lookup"><span data-stu-id="2517c-578">See [Areas](xref:mvc/controllers/areas) for details about how areas are used with views.</span></span>

<span data-ttu-id="2517c-579">V následujícím příkladu je nakonfiguruje MVC pro použití výchozí konvenční trasy a `area` trasy pro `area` s názvem `Blog`:</span><span class="sxs-lookup"><span data-stu-id="2517c-579">The following example configures MVC to use the default conventional route and an `area` route for an `area` named `Blog`:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet1)]

<span data-ttu-id="2517c-580">V předchozím kódu je volána <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> k vytvoření `"blog_route"`.</span><span class="sxs-lookup"><span data-stu-id="2517c-580">In the preceding code, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> is called to create the `"blog_route"`.</span></span> <span data-ttu-id="2517c-581">Druhý parametr, `"Blog"`, je název oblasti.</span><span class="sxs-lookup"><span data-stu-id="2517c-581">The second parameter, `"Blog"`, is the area name.</span></span>

<span data-ttu-id="2517c-582">Pokud odpovídá cesta URL jako `/Manage/Users/AddUser`, vygeneruje `"blog_route"` trasy `{ area = Blog, controller = Users, action = AddUser }`hodnoty trasy.</span><span class="sxs-lookup"><span data-stu-id="2517c-582">When matching a URL path like `/Manage/Users/AddUser`, the `"blog_route"` route generates the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span> <span data-ttu-id="2517c-583">Hodnota `area` trasy je vytvořena výchozí hodnotou pro `area`.</span><span class="sxs-lookup"><span data-stu-id="2517c-583">The `area` route value is produced by a default value for `area`.</span></span> <span data-ttu-id="2517c-584">Trasa vytvořená pomocí `MapAreaControllerRoute` je ekvivalentní následujícímu:</span><span class="sxs-lookup"><span data-stu-id="2517c-584">The route created by `MapAreaControllerRoute` is equivalent to the following:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup2.cs?name=snippet2)]

<span data-ttu-id="2517c-585">`MapAreaControllerRoute` vytvoří trasu pomocí výchozí hodnoty i omezení pro `area` pomocí zadaného názvu oblasti v tomto případě `Blog`.</span><span class="sxs-lookup"><span data-stu-id="2517c-585">`MapAreaControllerRoute` creates a route using both a default value and constraint for `area` using the provided area name, in this case `Blog`.</span></span> <span data-ttu-id="2517c-586">Výchozí hodnota zajistí, že trasa vždy vytvoří `{ area = Blog, ... }`, omezení vyžaduje `{ area = Blog, ... }` hodnoty pro generování adresy URL.</span><span class="sxs-lookup"><span data-stu-id="2517c-586">The default value ensures that the route always produces `{ area = Blog, ... }`, the constraint requires the value `{ area = Blog, ... }` for URL generation.</span></span>

<span data-ttu-id="2517c-587">Konvenční směrování je závislé na pořadí.</span><span class="sxs-lookup"><span data-stu-id="2517c-587">Conventional routing is order-dependent.</span></span> <span data-ttu-id="2517c-588">Obecně platí, že trasy s oblastmi by měly být umístěny dříve, protože jsou konkrétnější než trasy bez oblasti.</span><span class="sxs-lookup"><span data-stu-id="2517c-588">In general, routes with areas should be placed earlier as they're more specific than routes without an area.</span></span>

<span data-ttu-id="2517c-589">Pomocí předchozího příkladu hodnoty trasy `{ area = Blog, controller = Users, action = AddUser }` odpovídat následující akci:</span><span class="sxs-lookup"><span data-stu-id="2517c-589">Using the preceding example, the route values `{ area = Blog, controller = Users, action = AddUser }` match the following action:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

<span data-ttu-id="2517c-590">Atribut [[Area]](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) je tím, že označuje kontroler jako součást oblasti.</span><span class="sxs-lookup"><span data-stu-id="2517c-590">The [[Area]](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) attribute is what denotes a controller as part of an area.</span></span> <span data-ttu-id="2517c-591">Tento kontroler je v oblasti `Blog`.</span><span class="sxs-lookup"><span data-stu-id="2517c-591">This controller is in the `Blog` area.</span></span> <span data-ttu-id="2517c-592">Řadiče bez atributu `[Area]` nejsou členy žádné oblasti a **neodpovídají,** Pokud je hodnota `area` trasy k dispozici v směrování.</span><span class="sxs-lookup"><span data-stu-id="2517c-592">Controllers without an `[Area]` attribute are not members of any area, and do **not** match when the `area` route value is provided by routing.</span></span> <span data-ttu-id="2517c-593">V následujícím příkladu se může shodovat jenom první kontroler, který bude odpovídat hodnotám tras `{ area = Blog, controller = Users, action = AddUser }`.</span><span class="sxs-lookup"><span data-stu-id="2517c-593">In the following example, only the first controller listed can match the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Zebra/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/UsersController.cs)]

<span data-ttu-id="2517c-594">Pro úplnost se zobrazí obor názvů každého kontroleru.</span><span class="sxs-lookup"><span data-stu-id="2517c-594">The namespace of each controller is shown here for completeness.</span></span> <span data-ttu-id="2517c-595">Pokud předchozí řadiče používají stejný obor názvů, vygeneruje se chyba kompilátoru.</span><span class="sxs-lookup"><span data-stu-id="2517c-595">If the preceding controllers uses the same namespace, a compiler error would be generated.</span></span> <span data-ttu-id="2517c-596">Obory názvů třídy nemají žádný vliv na směrování MVC.</span><span class="sxs-lookup"><span data-stu-id="2517c-596">Class namespaces have no effect on MVC's routing.</span></span>

<span data-ttu-id="2517c-597">První dva řadiče jsou členy oblastí a shodují se pouze v případě, že je jejich název příslušné oblasti poskytovaný hodnotou `area` trasy.</span><span class="sxs-lookup"><span data-stu-id="2517c-597">The first two controllers are members of areas, and only match when their respective area name is provided by the `area` route value.</span></span> <span data-ttu-id="2517c-598">Třetí kontroler není členem žádné oblasti a může odpovídat jenom v případě, že směrováním neposkytuje žádnou hodnotu pro `area`.</span><span class="sxs-lookup"><span data-stu-id="2517c-598">The third controller isn't a member of any area, and can only match when no value for `area` is provided by routing.</span></span>

<a name="aa"></a>

<span data-ttu-id="2517c-599">V souvislosti s neshodnou *hodnotou*je absence hodnoty `area` stejná, jako kdyby hodnota `area` měla hodnotu null nebo prázdný řetězec.</span><span class="sxs-lookup"><span data-stu-id="2517c-599">In terms of matching *no value*, the absence of the `area` value is the same as if the value for `area` were null or the empty string.</span></span>

<span data-ttu-id="2517c-600">Při provádění akce uvnitř oblasti je hodnota trasy pro `area` k dispozici jako [ambientní hodnota](#ambient) pro směrování, která se má použít pro generování adresy URL.</span><span class="sxs-lookup"><span data-stu-id="2517c-600">When executing an action inside an area, the route value for `area` is available as an [ambient value](#ambient) for routing to use for URL generation.</span></span> <span data-ttu-id="2517c-601">To znamená, že ve výchozím nastavení jsou tyto oblasti pro generování adresy URL *rychlé* , jak je znázorněno v následující ukázce.</span><span class="sxs-lookup"><span data-stu-id="2517c-601">This means that by default areas act *sticky* for URL generation as demonstrated by the following sample.</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup3.cs?name=snippet3)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Duck/Controllers/UsersController.cs)]

<span data-ttu-id="2517c-602">Následující kód vygeneruje adresu URL pro `/Zebra/Users/AddUser`:</span><span class="sxs-lookup"><span data-stu-id="2517c-602">The following code generates a URL to `/Zebra/Users/AddUser`:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/HomeController.cs?name=snippet)]

<a name="action"></a>

## <a name="action-definition"></a><span data-ttu-id="2517c-603">Definice akce</span><span class="sxs-lookup"><span data-stu-id="2517c-603">Action definition</span></span>

<span data-ttu-id="2517c-604">Veřejné metody na řadiči, s výjimkou atributu [neaction](xref:Microsoft.AspNetCore.Mvc.NonActionAttribute) , jsou akce.</span><span class="sxs-lookup"><span data-stu-id="2517c-604">Public methods on a controller, except those with the [NonAction](xref:Microsoft.AspNetCore.Mvc.NonActionAttribute) attribute, are actions.</span></span>

## <a name="sample-code"></a><span data-ttu-id="2517c-605">Ukázka kódu</span><span class="sxs-lookup"><span data-stu-id="2517c-605">Sample code</span></span>

 * <span data-ttu-id="2517c-606">Metoda [MyDisplayRouteInfo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x/main/Extensions/ControllerContextExtensions.cs) je obsažena v [ukázce stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) a slouží k zobrazení informací o směrování.</span><span class="sxs-lookup"><span data-stu-id="2517c-606">The [MyDisplayRouteInfo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x/main/Extensions/ControllerContextExtensions.cs) method is included in the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) and is used to display routing information.</span></span>
* <span data-ttu-id="2517c-607">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="2517c-607">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) ([how to download](xref:index#how-to-download-a-sample))</span></span>

[!INCLUDE[](~/includes/dbg-route.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="2517c-608">ASP.NET Core MVC používá [middleware](xref:fundamentals/middleware/index) směrování ke spárování adres URL příchozích požadavků a jejich mapování na akce.</span><span class="sxs-lookup"><span data-stu-id="2517c-608">ASP.NET Core MVC uses the Routing [middleware](xref:fundamentals/middleware/index) to match the URLs of incoming requests and map them to actions.</span></span> <span data-ttu-id="2517c-609">Trasy jsou definovány ve spouštěcím kódu nebo v atributech.</span><span class="sxs-lookup"><span data-stu-id="2517c-609">Routes are defined in startup code or attributes.</span></span> <span data-ttu-id="2517c-610">Trasy popisují, jak by měly být cesty URL odpovídat akcím.</span><span class="sxs-lookup"><span data-stu-id="2517c-610">Routes describe how URL paths should be matched to actions.</span></span> <span data-ttu-id="2517c-611">Trasy se také používají ke generování adres URL (pro odkazy) odesílaných v odpovědích.</span><span class="sxs-lookup"><span data-stu-id="2517c-611">Routes are also used to generate URLs (for links) sent out in responses.</span></span>

<span data-ttu-id="2517c-612">Akce jsou směrovány buď podle konvence nebo směrovaného atributu.</span><span class="sxs-lookup"><span data-stu-id="2517c-612">Actions are either conventionally routed or attribute routed.</span></span> <span data-ttu-id="2517c-613">Umístěním trasy do kontroleru nebo akce se nasměruje atribut IT.</span><span class="sxs-lookup"><span data-stu-id="2517c-613">Placing a route on the controller or the action makes it attribute routed.</span></span> <span data-ttu-id="2517c-614">Další informace najdete v tématu [smíšená směrování](#routing-mixed-ref-label) .</span><span class="sxs-lookup"><span data-stu-id="2517c-614">See [Mixed routing](#routing-mixed-ref-label) for more information.</span></span>

<span data-ttu-id="2517c-615">Tento dokument vysvětluje interakce mezi MVC a směrováními a jak běžné aplikace MVC využívají funkce směrování.</span><span class="sxs-lookup"><span data-stu-id="2517c-615">This document will explain the interactions between MVC and routing, and how typical MVC apps make use of routing features.</span></span> <span data-ttu-id="2517c-616">Podrobnosti o pokročilém směrování najdete v tématu [Směrování](xref:fundamentals/routing) .</span><span class="sxs-lookup"><span data-stu-id="2517c-616">See [Routing](xref:fundamentals/routing) for details on advanced routing.</span></span>

## <a name="setting-up-routing-middleware"></a><span data-ttu-id="2517c-617">Nastavení middlewaru směrování</span><span class="sxs-lookup"><span data-stu-id="2517c-617">Setting up Routing Middleware</span></span>

<span data-ttu-id="2517c-618">V metodě *Konfigurace* se může zobrazit kód podobný tomuto:</span><span class="sxs-lookup"><span data-stu-id="2517c-618">In your *Configure* method you may see code similar to:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="2517c-619">Uvnitř volání `UseMvc`se `MapRoute` používá k vytvoření jedné trasy, na kterou odkazujeme jako na `default` trase.</span><span class="sxs-lookup"><span data-stu-id="2517c-619">Inside the call to `UseMvc`, `MapRoute` is used to create a single route, which we'll refer to as the `default` route.</span></span> <span data-ttu-id="2517c-620">Většina aplikací MVC bude používat trasu se šablonou podobnou `default` trase.</span><span class="sxs-lookup"><span data-stu-id="2517c-620">Most MVC apps will use a route with a template similar to the `default` route.</span></span>

<span data-ttu-id="2517c-621">`"{controller=Home}/{action=Index}/{id?}"` šablony trasy se může shodovat s cestou URL jako `/Products/Details/5` a extrahuje hodnoty trasy `{ controller = Products, action = Details, id = 5 }` cestou tokenizací.</span><span class="sxs-lookup"><span data-stu-id="2517c-621">The route template `"{controller=Home}/{action=Index}/{id?}"` can match a URL path like `/Products/Details/5` and will extract the route values `{ controller = Products, action = Details, id = 5 }` by tokenizing the path.</span></span> <span data-ttu-id="2517c-622">MVC se pokusí vyhledat kontroler s názvem `ProductsController` a spustit akci `Details`:</span><span class="sxs-lookup"><span data-stu-id="2517c-622">MVC will attempt to locate a controller named `ProductsController` and run the action `Details`:</span></span>

```csharp
public class ProductsController : Controller
{
   public IActionResult Details(int id) { ... }
}
```

<span data-ttu-id="2517c-623">Všimněte si, že v tomto příkladu vazba modelu používá hodnotu `id = 5` k nastavení parametru `id` na `5` při vyvolání této akce.</span><span class="sxs-lookup"><span data-stu-id="2517c-623">Note that in this example, model binding would use the value of `id = 5` to set the `id` parameter to `5` when invoking this action.</span></span> <span data-ttu-id="2517c-624">Další podrobnosti najdete v tématu [vazba modelu](../models/model-binding.md) .</span><span class="sxs-lookup"><span data-stu-id="2517c-624">See the [Model Binding](../models/model-binding.md) for more details.</span></span>

<span data-ttu-id="2517c-625">Použití trasy `default`:</span><span class="sxs-lookup"><span data-stu-id="2517c-625">Using the `default` route:</span></span>

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="2517c-626">Šablona trasy:</span><span class="sxs-lookup"><span data-stu-id="2517c-626">The route template:</span></span>

* <span data-ttu-id="2517c-627">`{controller=Home}` definuje jako výchozí `controller` `Home`.</span><span class="sxs-lookup"><span data-stu-id="2517c-627">`{controller=Home}` defines `Home` as the default `controller`</span></span>

* <span data-ttu-id="2517c-628">`{action=Index}` definuje jako výchozí `action` `Index`.</span><span class="sxs-lookup"><span data-stu-id="2517c-628">`{action=Index}` defines `Index` as the default `action`</span></span>

* <span data-ttu-id="2517c-629">`{id?}` definuje `id` jako volitelné.</span><span class="sxs-lookup"><span data-stu-id="2517c-629">`{id?}` defines `id` as optional</span></span>

<span data-ttu-id="2517c-630">Výchozí a volitelné parametry směrování nemusejí být v cestě URL pro porovnávání k dispozici.</span><span class="sxs-lookup"><span data-stu-id="2517c-630">Default and optional route parameters don't need to be present in the URL path for a match.</span></span> <span data-ttu-id="2517c-631">Podrobný popis syntaxe šablony směrování naleznete v tématu Referenční dokumentace k [šabloně směrování](xref:fundamentals/routing#route-template-reference) .</span><span class="sxs-lookup"><span data-stu-id="2517c-631">See [Route Template Reference](xref:fundamentals/routing#route-template-reference) for a detailed description of route template syntax.</span></span>

<span data-ttu-id="2517c-632">`"{controller=Home}/{action=Index}/{id?}"` se může shodovat s cestou adresy URL `/` a vytvoří hodnoty trasy `{ controller = Home, action = Index }`.</span><span class="sxs-lookup"><span data-stu-id="2517c-632">`"{controller=Home}/{action=Index}/{id?}"` can match the URL path `/` and will produce the route values `{ controller = Home, action = Index }`.</span></span> <span data-ttu-id="2517c-633">Hodnoty pro `controller` a `action` využívají výchozí hodnoty, `id` nevytvoří hodnotu, protože v cestě URL není žádný odpovídající segment.</span><span class="sxs-lookup"><span data-stu-id="2517c-633">The values for `controller` and `action` make use of the default values, `id` doesn't produce a value since there's no corresponding segment in the URL path.</span></span> <span data-ttu-id="2517c-634">MVC použije tyto hodnoty tras k výběru `HomeController` a `Index` akci:</span><span class="sxs-lookup"><span data-stu-id="2517c-634">MVC would use these route values to select the `HomeController` and `Index` action:</span></span>

```csharp
public class HomeController : Controller
{
  public IActionResult Index() { ... }
}
```

<span data-ttu-id="2517c-635">Pomocí této definice kontroleru a šablony trasy se spustí akce `HomeController.Index` pro některou z následujících cest URL:</span><span class="sxs-lookup"><span data-stu-id="2517c-635">Using this controller definition and route template, the `HomeController.Index` action would be executed for any of the following URL paths:</span></span>

* `/Home/Index/17`

* `/Home/Index`

* `/Home`

* `/`

<span data-ttu-id="2517c-636">Metoda usnadnění `UseMvcWithDefaultRoute`:</span><span class="sxs-lookup"><span data-stu-id="2517c-636">The convenience method `UseMvcWithDefaultRoute`:</span></span>

```csharp
app.UseMvcWithDefaultRoute();
```

<span data-ttu-id="2517c-637">Dá se použít k nahrazení:</span><span class="sxs-lookup"><span data-stu-id="2517c-637">Can be used to replace:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="2517c-638">`UseMvc` a `UseMvcWithDefaultRoute` přidat instanci `RouterMiddleware` do kanálu middlewaru.</span><span class="sxs-lookup"><span data-stu-id="2517c-638">`UseMvc` and `UseMvcWithDefaultRoute` add an instance of `RouterMiddleware` to the middleware pipeline.</span></span> <span data-ttu-id="2517c-639">MVC nekomunikuje přímo se middlewarem a používá směrování pro zpracování požadavků.</span><span class="sxs-lookup"><span data-stu-id="2517c-639">MVC doesn't interact directly with middleware, and uses routing to handle requests.</span></span> <span data-ttu-id="2517c-640">MVC je připojeno k trasám prostřednictvím instance `MvcRouteHandler`.</span><span class="sxs-lookup"><span data-stu-id="2517c-640">MVC is connected to the routes through an instance of `MvcRouteHandler`.</span></span> <span data-ttu-id="2517c-641">Kód uvnitř `UseMvc` je podobný následujícímu:</span><span class="sxs-lookup"><span data-stu-id="2517c-641">The code inside of `UseMvc` is similar to the following:</span></span>

```csharp
var routes = new RouteBuilder(app);

// Add connection to MVC, will be hooked up by calls to MapRoute.
routes.DefaultHandler = new MvcRouteHandler(...);

// Execute callback to register routes.
// routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");

// Create route collection and add the middleware.
app.UseRouter(routes.Build());
```

<span data-ttu-id="2517c-642">`UseMvc` přímo nedefinuje žádné trasy, přidá zástupný symbol do kolekce směrování pro trasu `attribute`.</span><span class="sxs-lookup"><span data-stu-id="2517c-642">`UseMvc` doesn't directly define any routes, it adds a placeholder to the route collection for the `attribute` route.</span></span> <span data-ttu-id="2517c-643">Přetížení `UseMvc(Action<IRouteBuilder>)` umožňuje přidat vlastní trasy a zároveň podporuje směrování atributů.</span><span class="sxs-lookup"><span data-stu-id="2517c-643">The overload `UseMvc(Action<IRouteBuilder>)` lets you add your own routes and also supports attribute routing.</span></span>  <span data-ttu-id="2517c-644">`UseMvc` a všechny jeho variace přidají zástupný symbol pro atribut směrování atributu směrování je vždycky dostupný bez ohledu na to, jak nakonfigurujete `UseMvc`.</span><span class="sxs-lookup"><span data-stu-id="2517c-644">`UseMvc` and all of its variations add a placeholder for the attribute route - attribute routing is always available regardless of how you configure `UseMvc`.</span></span> <span data-ttu-id="2517c-645">`UseMvcWithDefaultRoute` definuje výchozí trasu a podporuje směrování atributů.</span><span class="sxs-lookup"><span data-stu-id="2517c-645">`UseMvcWithDefaultRoute` defines a default route and supports attribute routing.</span></span> <span data-ttu-id="2517c-646">Oddíl [Směrování atributů](#attribute-routing-ref-label) obsahuje další podrobnosti o směrování atributů.</span><span class="sxs-lookup"><span data-stu-id="2517c-646">The [Attribute Routing](#attribute-routing-ref-label) section includes more details on attribute routing.</span></span>

<a name="routing-conventional-ref-label"></a>

## <a name="conventional-routing"></a><span data-ttu-id="2517c-647">Konvenční směrování</span><span class="sxs-lookup"><span data-stu-id="2517c-647">Conventional routing</span></span>

<span data-ttu-id="2517c-648">Trasa `default`:</span><span class="sxs-lookup"><span data-stu-id="2517c-648">The `default` route:</span></span>

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="2517c-649">Předchozí kód je příkladem konvenčního směrování.</span><span class="sxs-lookup"><span data-stu-id="2517c-649">The preceding code is an example of a conventional routing.</span></span> <span data-ttu-id="2517c-650">Tento styl se nazývá konvenční směrování, protože vytváří *konvenci* pro cesty URL:</span><span class="sxs-lookup"><span data-stu-id="2517c-650">This style is called conventional routing because it establishes a *convention* for URL paths:</span></span>

* <span data-ttu-id="2517c-651">První segment cesty se mapuje na název kontroleru.</span><span class="sxs-lookup"><span data-stu-id="2517c-651">The first path segment maps to the controller name.</span></span>
* <span data-ttu-id="2517c-652">druhý se mapuje na název akce.</span><span class="sxs-lookup"><span data-stu-id="2517c-652">The second maps to the action name.</span></span>
* <span data-ttu-id="2517c-653">Třetí segment se používá pro volitelné `id`.</span><span class="sxs-lookup"><span data-stu-id="2517c-653">The third segment is used for an optional `id`.</span></span> <span data-ttu-id="2517c-654">`id` se mapuje na entitu modelu.</span><span class="sxs-lookup"><span data-stu-id="2517c-654">`id` maps to a model entity.</span></span>

<span data-ttu-id="2517c-655">Pomocí této `default` trasy adresa URL `/Products/List` mapuje na `ProductsController.List` akci a `/Blog/Article/17` mapuje na `BlogController.Article`.</span><span class="sxs-lookup"><span data-stu-id="2517c-655">Using this `default` route, the URL path `/Products/List` maps to the `ProductsController.List` action, and `/Blog/Article/17` maps to `BlogController.Article`.</span></span> <span data-ttu-id="2517c-656">Toto mapování je založené **pouze** na názvech kontroléru a akce a není založené na oborech názvů, umístěních zdrojového souboru nebo parametrech metody.</span><span class="sxs-lookup"><span data-stu-id="2517c-656">This mapping is based on the controller and action names **only** and isn't based on namespaces, source file locations, or method parameters.</span></span>

> [!TIP]
> <span data-ttu-id="2517c-657">Použití konvenčního směrování s výchozí trasou vám umožní rychle vytvořit aplikaci, aniž by se musela používat nový vzor adresy URL pro každou akci, kterou definujete.</span><span class="sxs-lookup"><span data-stu-id="2517c-657">Using conventional routing with the default route allows you to build the application quickly without having to come up with a new URL pattern for each action you define.</span></span> <span data-ttu-id="2517c-658">Pro aplikace s akcemi stylu CRUD, které mají konzistenci pro adresy URL napříč řadiči, může zjednodušit váš kód a zajistit větší předvídatelné uživatelské rozhraní.</span><span class="sxs-lookup"><span data-stu-id="2517c-658">For an application with CRUD style actions, having consistency for the URLs across your controllers can help simplify your code and make your UI more predictable.</span></span>

> [!WARNING]
> <span data-ttu-id="2517c-659">`id` je definována jako volitelná šablonou směrování, což znamená, že se akce můžou provádět bez ID, které je součástí adresy URL.</span><span class="sxs-lookup"><span data-stu-id="2517c-659">The `id` is defined as optional by the route template, meaning that your actions can execute without the ID provided as part of the URL.</span></span> <span data-ttu-id="2517c-660">Obvykle k tomu, co se stane, když se `id` z adresy URL vynechá, znamená to, že bude nastavená na `0` podle vazby modelu a v důsledku toho se v databázi odpovídající `id == 0`nenajde žádná entita.</span><span class="sxs-lookup"><span data-stu-id="2517c-660">Usually what will happen if `id` is omitted from the URL is that it will be set to `0` by model binding, and as a result no entity will be found in the database matching `id == 0`.</span></span> <span data-ttu-id="2517c-661">Směrování atributů vám může poskytnout jemně odstupňovaný ovládací prvek, který umožňuje, aby se ID vyžadovalo u některých akcí, a ne pro ostatní.</span><span class="sxs-lookup"><span data-stu-id="2517c-661">Attribute routing can give you fine-grained control to make the ID required for some actions and not for others.</span></span> <span data-ttu-id="2517c-662">Podle konvence bude dokumentace obsahovat volitelné parametry, jako `id`, když se budou pravděpodobně zobrazovat ve správném použití.</span><span class="sxs-lookup"><span data-stu-id="2517c-662">By convention the documentation will include optional parameters like `id` when they're likely to appear in correct usage.</span></span>

## <a name="multiple-routes"></a><span data-ttu-id="2517c-663">Více tras</span><span class="sxs-lookup"><span data-stu-id="2517c-663">Multiple routes</span></span>

<span data-ttu-id="2517c-664">Přidáním dalších volání do `MapRoute`můžete do `UseMvc` přidat více tras.</span><span class="sxs-lookup"><span data-stu-id="2517c-664">You can add multiple routes inside `UseMvc` by adding more calls to `MapRoute`.</span></span> <span data-ttu-id="2517c-665">V takovém případě můžete definovat více konvencí nebo přidat konvenční trasy, které jsou vyhrazeny určité akci, například:</span><span class="sxs-lookup"><span data-stu-id="2517c-665">Doing so allows you to define multiple conventions, or to add conventional routes that are dedicated to a specific action, such as:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("blog", "blog/{*article}",
            defaults: new { controller = "Blog", action = "Article" });
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="2517c-666">`blog` trasa je to *vyhrazená konvenční trasa*, což znamená, že používá konvenční směrovací systém, ale je vyhrazená konkrétní akci.</span><span class="sxs-lookup"><span data-stu-id="2517c-666">The `blog` route here is a *dedicated conventional route*, meaning that it uses the conventional routing system, but is dedicated to a specific action.</span></span> <span data-ttu-id="2517c-667">Vzhledem k tomu, že `controller` a `action` se v šabloně směrování nezobrazují jako parametry, můžou mít jenom výchozí hodnoty, takže tato trasa se vždycky namapuje na `BlogController.Article`akcí.</span><span class="sxs-lookup"><span data-stu-id="2517c-667">Since `controller` and `action` don't appear in the route template as parameters, they can only have the default values, and thus this route will always map to the action `BlogController.Article`.</span></span>

<span data-ttu-id="2517c-668">Trasy v kolekci tras jsou seřazené a budou zpracovávány v pořadí, v jakém byly přidány.</span><span class="sxs-lookup"><span data-stu-id="2517c-668">Routes in the route collection are ordered, and will be processed in the order they're added.</span></span> <span data-ttu-id="2517c-669">Takže v tomto příkladu bude `blog` trasa vyzkoušena před trasou `default`.</span><span class="sxs-lookup"><span data-stu-id="2517c-669">So in this example, the `blog` route will be tried before the `default` route.</span></span>

> [!NOTE]
> <span data-ttu-id="2517c-670">*Vyhrazené konvenční trasy* často používají **zachycení všech** parametrů tras, jako je `{*article}`, k zachycení zbývající části cesty URL.</span><span class="sxs-lookup"><span data-stu-id="2517c-670">*Dedicated conventional routes* often use **catch-all** route parameters like `{*article}` to capture the remaining portion of the URL path.</span></span> <span data-ttu-id="2517c-671">To může vést k tomu, že trasa "je příliš hlade", což znamená, že odpovídá adresám URL, které mají odpovídat jiným trasám.</span><span class="sxs-lookup"><span data-stu-id="2517c-671">This can make a route 'too greedy' meaning that it matches URLs that you intended to be matched by other routes.</span></span> <span data-ttu-id="2517c-672">Pokud to chcete vyřešit, umístěte trasy "hladce" do směrovací tabulky později.</span><span class="sxs-lookup"><span data-stu-id="2517c-672">Put the 'greedy' routes later in the route table to solve this.</span></span>

### <a name="fallback"></a><span data-ttu-id="2517c-673">Nalezení</span><span class="sxs-lookup"><span data-stu-id="2517c-673">Fallback</span></span>

<span data-ttu-id="2517c-674">V rámci zpracování žádosti MVC ověří, že hodnoty trasy lze použít k vyhledání kontroleru a akce v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="2517c-674">As part of request processing, MVC will verify that the route values can be used to find a controller and action in your application.</span></span> <span data-ttu-id="2517c-675">Pokud se hodnoty trasy neshodují s akcí, trasa není považována za shodnou a bude se opakovat další trasa.</span><span class="sxs-lookup"><span data-stu-id="2517c-675">If the route values don't match an action then the route isn't considered a match, and the next route will be tried.</span></span> <span data-ttu-id="2517c-676">Označuje se jako *záložní*a je určený pro zjednodušení případů, kdy se konvenční trasy překrývají.</span><span class="sxs-lookup"><span data-stu-id="2517c-676">This is called *fallback*, and it's intended to simplify cases where conventional routes overlap.</span></span>

### <a name="disambiguating-actions"></a><span data-ttu-id="2517c-677">Nejednoznačnost akcí</span><span class="sxs-lookup"><span data-stu-id="2517c-677">Disambiguating actions</span></span>

<span data-ttu-id="2517c-678">Pokud se dvě akce shodují přes směrování, MVC musí nejednoznačně vybrat kandidáta na nejlepší nebo jinak vyvolat výjimku.</span><span class="sxs-lookup"><span data-stu-id="2517c-678">When two actions match through routing, MVC must disambiguate to choose the 'best' candidate or else throw an exception.</span></span> <span data-ttu-id="2517c-679">Příklad:</span><span class="sxs-lookup"><span data-stu-id="2517c-679">For example:</span></span>

```csharp
public class ProductsController : Controller
{
   public IActionResult Edit(int id) { ... }

   [HttpPost]
   public IActionResult Edit(int id, Product product) { ... }
}
```

<span data-ttu-id="2517c-680">Tento kontroler definuje dvě akce, které by odpovídaly cestě URL `/Products/Edit/17` a `{ controller = Products, action = Edit, id = 17 }`dat směrování.</span><span class="sxs-lookup"><span data-stu-id="2517c-680">This controller defines two actions that would match the URL path `/Products/Edit/17` and route data `{ controller = Products, action = Edit, id = 17 }`.</span></span> <span data-ttu-id="2517c-681">Toto je typický vzor pro řadiče MVC, kde `Edit(int)` zobrazuje formulář pro úpravu produktu a `Edit(int, Product)` zpracovává vystavený formulář.</span><span class="sxs-lookup"><span data-stu-id="2517c-681">This is a typical pattern for MVC controllers where `Edit(int)` shows a form to edit a product, and `Edit(int, Product)` processes  the posted form.</span></span> <span data-ttu-id="2517c-682">Aby tato možnost MVC mohla být v případě, že se jedná o jinou akci než příkaz HTTP, musela zvolit `Edit(int, Product)`, když je žádost `POST` HTTP a `Edit(int)`.</span><span class="sxs-lookup"><span data-stu-id="2517c-682">To make this possible MVC would need to choose `Edit(int, Product)` when the request is an HTTP `POST` and `Edit(int)` when the HTTP verb is anything else.</span></span>

<span data-ttu-id="2517c-683">`HttpPostAttribute` (`[HttpPost]`) je implementace `IActionConstraint`, která umožňuje, aby byla akce vybrána pouze v případě, že je `POST`příkaz HTTP.</span><span class="sxs-lookup"><span data-stu-id="2517c-683">The `HttpPostAttribute` ( `[HttpPost]` ) is an implementation of `IActionConstraint` that will only allow the action to be selected when the HTTP verb is `POST`.</span></span> <span data-ttu-id="2517c-684">Přítomnost `IActionConstraint` nastaví `Edit(int, Product)` "lepší", než `Edit(int)`, takže se `Edit(int, Product)` vyzkouší jako první.</span><span class="sxs-lookup"><span data-stu-id="2517c-684">The presence of an `IActionConstraint` makes the `Edit(int, Product)` a 'better' match than `Edit(int)`, so `Edit(int, Product)` will be tried first.</span></span>

<span data-ttu-id="2517c-685">Stačí napsat vlastní implementace `IActionConstraint` ve specializovaných scénářích, ale je důležité pochopit, že role atributů, jako jsou `HttpPostAttribute` podobné atributy, jsou definovány pro jiné příkazy HTTP.</span><span class="sxs-lookup"><span data-stu-id="2517c-685">You will only need to write custom `IActionConstraint` implementations in specialized scenarios, but it's important to understand the role of attributes like `HttpPostAttribute`  - similar attributes are defined for other HTTP verbs.</span></span> <span data-ttu-id="2517c-686">V konvenčním směrování je běžné, že pokud jsou součástí pracovního postupu `show form -> submit form`, je běžné použít stejný název akce.</span><span class="sxs-lookup"><span data-stu-id="2517c-686">In conventional routing it's common for actions to use the same action name when they're part of a `show form -> submit form` workflow.</span></span> <span data-ttu-id="2517c-687">Pohodlí tohoto vzoru se po kontrole části [porozumění IActionConstraint](#understanding-iactionconstraint) projeví.</span><span class="sxs-lookup"><span data-stu-id="2517c-687">The convenience of this pattern will become more apparent after reviewing the [Understanding IActionConstraint](#understanding-iactionconstraint) section.</span></span>

<span data-ttu-id="2517c-688">Pokud se více tras shodují a MVC nemůže najít nejlepší trasu, vyvolá `AmbiguousActionException`.</span><span class="sxs-lookup"><span data-stu-id="2517c-688">If multiple routes match, and MVC can't find a 'best' route, it will throw an `AmbiguousActionException`.</span></span>

<a name="routing-route-name-ref-label"></a>

### <a name="route-names"></a><span data-ttu-id="2517c-689">Názvy tras</span><span class="sxs-lookup"><span data-stu-id="2517c-689">Route names</span></span>

<span data-ttu-id="2517c-690">Řetězce `"blog"` a `"default"` v následujících příkladech jsou názvy tras:</span><span class="sxs-lookup"><span data-stu-id="2517c-690">The strings  `"blog"` and `"default"` in the following examples are route names:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("blog", "blog/{*article}",
               defaults: new { controller = "Blog", action = "Article" });
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="2517c-691">Názvy tras směrují logický název tak, aby se pojmenovaná trasa dala použít pro generování adresy URL.</span><span class="sxs-lookup"><span data-stu-id="2517c-691">The route names give the route a logical name so that the named route can be used for URL generation.</span></span> <span data-ttu-id="2517c-692">Tím se značně zjednodušuje vytváření adres URL, když řazení tras může způsobit komplikované generování adresy URL.</span><span class="sxs-lookup"><span data-stu-id="2517c-692">This greatly simplifies URL creation when the ordering of routes could make URL generation complicated.</span></span> <span data-ttu-id="2517c-693">Názvy tras musí být jedinečné aplikace v rozsahu.</span><span class="sxs-lookup"><span data-stu-id="2517c-693">Route names must be unique application-wide.</span></span>

<span data-ttu-id="2517c-694">Názvy tras nemají žádný vliv na adresy URL, které by odpovídaly ani manipulaci s požadavky. používají se pouze pro generování adresy URL.</span><span class="sxs-lookup"><span data-stu-id="2517c-694">Route names have no impact on URL matching or handling of requests; they're used only for URL generation.</span></span> <span data-ttu-id="2517c-695">[Směrování](xref:fundamentals/routing) obsahuje podrobnější informace o generování adresy URL včetně generování adresy URL v pomocníkech specifických pro MVC.</span><span class="sxs-lookup"><span data-stu-id="2517c-695">[Routing](xref:fundamentals/routing) has more detailed information on URL generation including URL generation in MVC-specific helpers.</span></span>

<a name="attribute-routing-ref-label"></a>

## <a name="attribute-routing"></a><span data-ttu-id="2517c-696">Směrování atributů</span><span class="sxs-lookup"><span data-stu-id="2517c-696">Attribute routing</span></span>

<span data-ttu-id="2517c-697">Směrování atributů používá sadu atributů k mapování akcí přímo na šablony směrování.</span><span class="sxs-lookup"><span data-stu-id="2517c-697">Attribute routing uses a set of attributes to map actions directly to route templates.</span></span> <span data-ttu-id="2517c-698">V následujícím příkladu se `app.UseMvc();` používá v metodě `Configure` a není předána žádná trasa.</span><span class="sxs-lookup"><span data-stu-id="2517c-698">In the following example, `app.UseMvc();` is used in the `Configure` method and no route is passed.</span></span> <span data-ttu-id="2517c-699">`HomeController` se bude shodovat s množinou adres URL podobných tomu, co by se výchozí trasa `{controller=Home}/{action=Index}/{id?}` shodovala:</span><span class="sxs-lookup"><span data-stu-id="2517c-699">The `HomeController` will match a set of URLs similar to what the default route `{controller=Home}/{action=Index}/{id?}` would match:</span></span>

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

<span data-ttu-id="2517c-700">Akce `HomeController.Index()` se spustí pro všechny cesty URL `/`, `/Home`nebo `/Home/Index`.</span><span class="sxs-lookup"><span data-stu-id="2517c-700">The `HomeController.Index()` action will be executed for any of the URL paths `/`, `/Home`, or `/Home/Index`.</span></span>

> [!NOTE]
> <span data-ttu-id="2517c-701">V tomto příkladu se zvýrazní klíčový rozdíl mezi směrováním atributů a konvenčním směrováním.</span><span class="sxs-lookup"><span data-stu-id="2517c-701">This example highlights a key programming difference between attribute routing and conventional routing.</span></span> <span data-ttu-id="2517c-702">Směrování atributů vyžaduje více vstupu pro určení trasy; konvenční výchozí trasa zpracovává trasy více stručně.</span><span class="sxs-lookup"><span data-stu-id="2517c-702">Attribute routing requires more input to specify a route; the conventional default route handles routes more succinctly.</span></span> <span data-ttu-id="2517c-703">Směrování atributů ale povoluje (a vyžaduje) přesnou kontrolu nad tím, které šablony směrování se vztahují na každou akci.</span><span class="sxs-lookup"><span data-stu-id="2517c-703">However, attribute routing allows (and requires) precise control of which route templates apply to each action.</span></span>

<span data-ttu-id="2517c-704">Při směrování atributů název kontroleru a názvy akcí nehraje **žádná** role, ve které je vybrána akce.</span><span class="sxs-lookup"><span data-stu-id="2517c-704">With attribute routing the controller name and action names play **no** role in which action is selected.</span></span> <span data-ttu-id="2517c-705">Tento příklad bude odpovídat stejným adresám URL jako v předchozím příkladu.</span><span class="sxs-lookup"><span data-stu-id="2517c-705">This example will match the same URLs as the previous example.</span></span>

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
> <span data-ttu-id="2517c-706">Výše uvedené šablony tras nedefinují parametry směrování pro `action`, `area`a `controller`.</span><span class="sxs-lookup"><span data-stu-id="2517c-706">The route templates above don't define route parameters for `action`, `area`, and `controller`.</span></span> <span data-ttu-id="2517c-707">Ve skutečnosti nejsou tyto parametry trasy v trasách atributů povoleny.</span><span class="sxs-lookup"><span data-stu-id="2517c-707">In fact, these route parameters are not allowed in attribute routes.</span></span> <span data-ttu-id="2517c-708">Vzhledem k tomu, že šablona trasy je již k akci přidružena, by nedošlo k tomu, že by bylo vhodné analyzovat název akce z adresy URL.</span><span class="sxs-lookup"><span data-stu-id="2517c-708">Since the route template is already associated with an action, it wouldn't make sense to parse the action name from the URL.</span></span>

## <a name="attribute-routing-with-httpverb-attributes"></a><span data-ttu-id="2517c-709">Směrování atributů pomocí atributů http [příkaz]</span><span class="sxs-lookup"><span data-stu-id="2517c-709">Attribute routing with Http[Verb] attributes</span></span>

<span data-ttu-id="2517c-710">Směrování atributů může také používat atributy `Http[Verb]`, jako je například `HttpPostAttribute`.</span><span class="sxs-lookup"><span data-stu-id="2517c-710">Attribute routing can also make use of the `Http[Verb]` attributes such as `HttpPostAttribute`.</span></span> <span data-ttu-id="2517c-711">Všechny tyto atributy mohou přijmout šablonu směrování.</span><span class="sxs-lookup"><span data-stu-id="2517c-711">All of these attributes can accept a route template.</span></span> <span data-ttu-id="2517c-712">Tento příklad ukazuje dvě akce, které odpovídají stejné šabloně směrování:</span><span class="sxs-lookup"><span data-stu-id="2517c-712">This example shows two actions that match the same route template:</span></span>

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

<span data-ttu-id="2517c-713">V případě cesty URL, jako je `/products` akce `ProductsApi.ListProducts` se spustí, když je příkaz HTTP `GET` a `ProductsApi.CreateProduct` se spustí, když se příkaz HTTP `POST`.</span><span class="sxs-lookup"><span data-stu-id="2517c-713">For a URL path like `/products` the `ProductsApi.ListProducts` action will be executed when the HTTP verb is `GET` and `ProductsApi.CreateProduct` will be executed when the HTTP verb is `POST`.</span></span> <span data-ttu-id="2517c-714">Směrování atributů první odpovídá adrese URL oproti sadě šablon směrování definovaných atributy směrování.</span><span class="sxs-lookup"><span data-stu-id="2517c-714">Attribute routing first matches the URL against the set of route templates defined by route attributes.</span></span> <span data-ttu-id="2517c-715">Po porovnání šablony trasy se použijí omezení `IActionConstraint` pro určení akcí, které lze provést.</span><span class="sxs-lookup"><span data-stu-id="2517c-715">Once a route template matches, `IActionConstraint` constraints are applied to determine which actions can be executed.</span></span>

> [!TIP]
> <span data-ttu-id="2517c-716">Při sestavování REST API je zřídka pravděpodobné, že budete chtít použít `[Route(...)]` na metodě akce, protože akce přijme všechny metody HTTP.</span><span class="sxs-lookup"><span data-stu-id="2517c-716">When building a REST API, it's rare that you will want to use `[Route(...)]` on an action method as the action will accept all HTTP methods.</span></span> <span data-ttu-id="2517c-717">Je lepší používat přesnější `Http*Verb*Attributes`, abyste měli přesně informace o tom, co vaše rozhraní API podporuje.</span><span class="sxs-lookup"><span data-stu-id="2517c-717">It's better to use the more specific `Http*Verb*Attributes` to be precise about what your API supports.</span></span> <span data-ttu-id="2517c-718">Očekává se, že klienti rozhraní REST API znají, které cesty a příkazy HTTP se mapují na konkrétní logické operace.</span><span class="sxs-lookup"><span data-stu-id="2517c-718">Clients of REST APIs are expected to know what paths and HTTP verbs map to specific logical operations.</span></span>

<span data-ttu-id="2517c-719">Vzhledem k tomu, že trasa atributu se vztahuje na konkrétní akci, je snadné vytvořit parametry požadované v rámci definice šablony trasy.</span><span class="sxs-lookup"><span data-stu-id="2517c-719">Since an attribute route applies to a specific action, it's easy to make parameters required as part of the route template definition.</span></span> <span data-ttu-id="2517c-720">V tomto příkladu je `id` nutné jako součást cesty URL.</span><span class="sxs-lookup"><span data-stu-id="2517c-720">In this example, `id` is required as part of the URL path.</span></span>

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

<span data-ttu-id="2517c-721">Akce `ProductsApi.GetProduct(int)` se spustí pro cestu URL, jako je `/products/3`, ale ne pro cestu URL, jako je `/products`.</span><span class="sxs-lookup"><span data-stu-id="2517c-721">The `ProductsApi.GetProduct(int)` action will be executed for a URL path like `/products/3` but not for a URL path like `/products`.</span></span> <span data-ttu-id="2517c-722">Úplný popis šablon směrování a souvisejících možností najdete v tématu věnovaném [Směrování](xref:fundamentals/routing) .</span><span class="sxs-lookup"><span data-stu-id="2517c-722">See [Routing](xref:fundamentals/routing) for a full description of route templates and related options.</span></span>

## <a name="route-name"></a><span data-ttu-id="2517c-723">Název trasy</span><span class="sxs-lookup"><span data-stu-id="2517c-723">Route Name</span></span>

<span data-ttu-id="2517c-724">Následující kód definuje *název trasy* `Products_List`:</span><span class="sxs-lookup"><span data-stu-id="2517c-724">The following code  defines a *route name* of `Products_List`:</span></span>

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

<span data-ttu-id="2517c-725">Názvy tras se dají použít k vygenerování adresy URL na základě konkrétní trasy.</span><span class="sxs-lookup"><span data-stu-id="2517c-725">Route names can be used to generate a URL based on a specific route.</span></span> <span data-ttu-id="2517c-726">Názvy tras nemají žádný vliv na adresu URL, která odpovídá chování směrování a používají se jenom pro generování adresy URL.</span><span class="sxs-lookup"><span data-stu-id="2517c-726">Route names have no impact on the URL matching behavior of routing and are only used for URL generation.</span></span> <span data-ttu-id="2517c-727">Názvy tras musí být jedinečné aplikace v rozsahu.</span><span class="sxs-lookup"><span data-stu-id="2517c-727">Route names must be unique application-wide.</span></span>

> [!NOTE]
> <span data-ttu-id="2517c-728">Na rozdíl od konvenční *výchozí trasy*definuje parametr `id` jako volitelné (`{id?}`).</span><span class="sxs-lookup"><span data-stu-id="2517c-728">Contrast this with the conventional *default route*, which defines the `id` parameter as optional (`{id?}`).</span></span> <span data-ttu-id="2517c-729">Tato možnost přesného určení rozhraní API má výhody, jako je například povolení `/products` a `/products/5` odeslání do různých akcí.</span><span class="sxs-lookup"><span data-stu-id="2517c-729">This ability to precisely specify APIs has advantages, such as  allowing `/products` and `/products/5` to be dispatched to different actions.</span></span>

<a name="routing-combining-ref-label"></a>

### <a name="combining-routes"></a><span data-ttu-id="2517c-730">Kombinování tras</span><span class="sxs-lookup"><span data-stu-id="2517c-730">Combining routes</span></span>

<span data-ttu-id="2517c-731">Aby bylo směrování atributů méně opakované, jsou atributy směrování na kontroleru kombinovány s atributy směrování na jednotlivých akcích.</span><span class="sxs-lookup"><span data-stu-id="2517c-731">To make attribute routing less repetitive, route attributes on the controller are combined with route attributes on the individual actions.</span></span> <span data-ttu-id="2517c-732">Každá šablona trasy definovaná na řadiči je předá směrování šablon na akcích.</span><span class="sxs-lookup"><span data-stu-id="2517c-732">Any route templates defined on the controller are prepended to route templates on the actions.</span></span> <span data-ttu-id="2517c-733">Umístěním atributu směrování na řadiči se vytvoří **všechny** akce v řadiči použít směrování atributů.</span><span class="sxs-lookup"><span data-stu-id="2517c-733">Placing a route attribute on the controller makes **all** actions in the controller use attribute routing.</span></span>

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

<span data-ttu-id="2517c-734">V tomto příkladu se cesta URL `/products` může shodovat `ProductsApi.ListProducts`a cesta URL `/products/5` může odpovídat `ProductsApi.GetProduct(int)`.</span><span class="sxs-lookup"><span data-stu-id="2517c-734">In this example the URL path `/products` can match `ProductsApi.ListProducts`, and the URL path `/products/5` can match `ProductsApi.GetProduct(int)`.</span></span> <span data-ttu-id="2517c-735">Obě tyto akce odpovídají pouze `GET` HTTP, protože jsou označeny `HttpGetAttribute`.</span><span class="sxs-lookup"><span data-stu-id="2517c-735">Both of these actions only match HTTP `GET` because they're marked with the `HttpGetAttribute`.</span></span>

<span data-ttu-id="2517c-736">Šablony směrování použité pro akci, která začíná na `/` nebo `~/` se nekombinují se šablonami směrování použitými pro kontroler.</span><span class="sxs-lookup"><span data-stu-id="2517c-736">Route templates applied to an action that begin with `/` or `~/` don't get combined with route templates applied to the controller.</span></span> <span data-ttu-id="2517c-737">Tento příklad odpovídá sadě cest URL podobně jako *výchozí trasa*.</span><span class="sxs-lookup"><span data-stu-id="2517c-737">This example matches a set of URL paths similar to the *default route*.</span></span>

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

### <a name="ordering-attribute-routes"></a><span data-ttu-id="2517c-738">Směrování atributů řazení</span><span class="sxs-lookup"><span data-stu-id="2517c-738">Ordering attribute routes</span></span>

<span data-ttu-id="2517c-739">Na rozdíl od konvenčních tras, které se spouštějí v definovaném pořadí, směrování atributů vytvoří strom a vyhledá současně všechny trasy.</span><span class="sxs-lookup"><span data-stu-id="2517c-739">In contrast to conventional routes, which execute in a defined order, attribute routing builds a tree and matches all routes simultaneously.</span></span> <span data-ttu-id="2517c-740">To se chová jako – Pokud byly položky trasy umístěny v ideálním pořadí; nejvíce konkrétní trasy mají možnost provést před obecnější trasy.</span><span class="sxs-lookup"><span data-stu-id="2517c-740">This behaves as-if the route entries were placed in an ideal ordering; the most specific routes have a chance to execute before the more general routes.</span></span>

<span data-ttu-id="2517c-741">Například trasa jako `blog/search/{topic}` je konkrétnější než trasa, jako je `blog/{*article}`.</span><span class="sxs-lookup"><span data-stu-id="2517c-741">For example, a route like `blog/search/{topic}` is more specific than a route like `blog/{*article}`.</span></span> <span data-ttu-id="2517c-742">Logicky mluví nejprve `blog/search/{topic}` Route "běžel", ve výchozím nastavení, protože se jedná o jediné řazení rozumné.</span><span class="sxs-lookup"><span data-stu-id="2517c-742">Logically speaking the `blog/search/{topic}` route 'runs' first, by default, because that's the only sensible ordering.</span></span> <span data-ttu-id="2517c-743">Pomocí konvenčního směrování zodpovídá vývojář za umístění tras v požadovaném pořadí.</span><span class="sxs-lookup"><span data-stu-id="2517c-743">Using conventional routing, the developer is  responsible for placing routes in the desired order.</span></span>

<span data-ttu-id="2517c-744">Trasy atributů mohou konfigurovat objednávku pomocí vlastnosti `Order` všech rozhraní, které poskytují atributy směrování.</span><span class="sxs-lookup"><span data-stu-id="2517c-744">Attribute routes can configure an order, using the `Order` property of all of the framework provided route attributes.</span></span> <span data-ttu-id="2517c-745">Trasy jsou zpracovávány podle vzestupného řazení vlastnosti `Order`.</span><span class="sxs-lookup"><span data-stu-id="2517c-745">Routes are processed according to an ascending sort of the `Order` property.</span></span> <span data-ttu-id="2517c-746">Výchozí pořadí je `0`.</span><span class="sxs-lookup"><span data-stu-id="2517c-746">The default order is `0`.</span></span> <span data-ttu-id="2517c-747">Nastavení trasy pomocí `Order = -1` spustí před trasami, které nenastaví objednávku.</span><span class="sxs-lookup"><span data-stu-id="2517c-747">Setting a route using `Order = -1` will run before routes that don't set an order.</span></span> <span data-ttu-id="2517c-748">Nastavení trasy pomocí `Order = 1` spustí se po výchozím řazení trasy.</span><span class="sxs-lookup"><span data-stu-id="2517c-748">Setting a route using `Order = 1` will run after default route ordering.</span></span>

> [!TIP]
> <span data-ttu-id="2517c-749">Nepoužívejte v závislosti na `Order`.</span><span class="sxs-lookup"><span data-stu-id="2517c-749">Avoid depending on `Order`.</span></span> <span data-ttu-id="2517c-750">Pokud vaše adresa URL vyžaduje explicitní směrování hodnot pořadí, je pravděpodobné, že budou i u klientů matoucí.</span><span class="sxs-lookup"><span data-stu-id="2517c-750">If your URL-space requires explicit order values to route correctly, then it's likely confusing to clients as well.</span></span> <span data-ttu-id="2517c-751">V části Obecné směrování atributů výběr správné trasy s odpovídající adresou URL.</span><span class="sxs-lookup"><span data-stu-id="2517c-751">In general attribute routing will select the correct route with URL matching.</span></span> <span data-ttu-id="2517c-752">Pokud výchozí pořadí použité pro generování adresy URL nefunguje, je použití názvu trasy jako přepsání obvykle jednodušší než použití vlastnosti `Order`.</span><span class="sxs-lookup"><span data-stu-id="2517c-752">If the default order used for URL generation isn't working, using route name as an override is usually simpler than applying the `Order` property.</span></span>

<span data-ttu-id="2517c-753">Směrování Razor Pages směrování a řadiče MVC sdílí implementaci.</span><span class="sxs-lookup"><span data-stu-id="2517c-753">Razor Pages routing and MVC controller routing share an implementation.</span></span> <span data-ttu-id="2517c-754">Informace o pořadí směrování v Razor Pages tématech jsou k dispozici v tématu [Razor Pages konvence směrování a aplikace: pořadí směrování](xref:razor-pages/razor-pages-conventions#route-order).</span><span class="sxs-lookup"><span data-stu-id="2517c-754">Information on route order in the Razor Pages topics is available at [Razor Pages route and app conventions: Route order](xref:razor-pages/razor-pages-conventions#route-order).</span></span>

<a name="routing-token-replacement-templates-ref-label"></a>

## <a name="token-replacement-in-route-templates-controller-action-area"></a><span data-ttu-id="2517c-755">Nahrazení tokenu v šablonách směrování ([Controller], [Action], [oblast])</span><span class="sxs-lookup"><span data-stu-id="2517c-755">Token replacement in route templates ([controller], [action], [area])</span></span>

<span data-ttu-id="2517c-756">Pro usnadnění pohodlí trasy atributů podporují *Nahrazení tokenu* uzavřením tokenu do hranatých závorek (`[`, `]`).</span><span class="sxs-lookup"><span data-stu-id="2517c-756">For convenience, attribute routes support *token replacement* by enclosing a token in square-braces (`[`, `]`).</span></span> <span data-ttu-id="2517c-757">Tokeny `[action]`, `[area]`a `[controller]` jsou nahrazeny hodnotami názvu akce, názvu oblasti a názvu kontroleru z akce, kde je trasa definována.</span><span class="sxs-lookup"><span data-stu-id="2517c-757">The tokens `[action]`, `[area]`, and `[controller]` are replaced with the values of the action name, area name, and controller name from the action where the route is defined.</span></span> <span data-ttu-id="2517c-758">V následujícím příkladu akce odpovídají cestám adresy URL, jak je popsáno v komentářích:</span><span class="sxs-lookup"><span data-stu-id="2517c-758">In the following example, the actions match URL paths as described in the comments:</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/ProductsController.cs?range=7-11,13-17,20-22)]

<span data-ttu-id="2517c-759">Nahrazení tokenu probíhá jako poslední krok při vytváření tras atributů.</span><span class="sxs-lookup"><span data-stu-id="2517c-759">Token replacement occurs as the last step of building the attribute routes.</span></span> <span data-ttu-id="2517c-760">Výše uvedený příklad se chová stejně jako následující kód:</span><span class="sxs-lookup"><span data-stu-id="2517c-760">The above example will behave the same as the following code:</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/ProductsController2.cs?range=7-11,13-17,20-22)]

<span data-ttu-id="2517c-761">Trasy atributů je také možné kombinovat s dědičností.</span><span class="sxs-lookup"><span data-stu-id="2517c-761">Attribute routes can also be combined with inheritance.</span></span> <span data-ttu-id="2517c-762">To je zvlášť výkonné v kombinaci s náhradou tokenu.</span><span class="sxs-lookup"><span data-stu-id="2517c-762">This is particularly powerful combined with token replacement.</span></span>

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

<span data-ttu-id="2517c-763">Nahrazení tokenu platí také pro názvy tras definované směrováními atributů.</span><span class="sxs-lookup"><span data-stu-id="2517c-763">Token replacement also applies to route names defined by attribute routes.</span></span> <span data-ttu-id="2517c-764">`[Route("[controller]/[action]", Name="[controller]_[action]")]` vygeneruje jedinečný název trasy pro každou akci.</span><span class="sxs-lookup"><span data-stu-id="2517c-764">`[Route("[controller]/[action]", Name="[controller]_[action]")]` generates a unique route name for each action.</span></span>

<span data-ttu-id="2517c-765">Chcete-li porovnat oddělovač nahrazení tokenu literálu `[` nebo `]`, vydejte ho opakováním znaku (`[[` nebo `]]`).</span><span class="sxs-lookup"><span data-stu-id="2517c-765">To match the literal token replacement delimiter `[` or  `]`, escape it by repeating the character (`[[` or `]]`).</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<a name="routing-token-replacement-transformers-ref-label"></a>

### <a name="use-a-parameter-transformer-to-customize-token-replacement"></a><span data-ttu-id="2517c-766">Použití transformátoru parametrů k přizpůsobení náhrady tokenu</span><span class="sxs-lookup"><span data-stu-id="2517c-766">Use a parameter transformer to customize token replacement</span></span>

<span data-ttu-id="2517c-767">Nahrazení tokenu se dá přizpůsobit pomocí transformátoru parametrů.</span><span class="sxs-lookup"><span data-stu-id="2517c-767">Token replacement can be customized using a parameter transformer.</span></span> <span data-ttu-id="2517c-768">Transformátor parametru implementuje `IOutboundParameterTransformer` a transformuje hodnotu parametrů.</span><span class="sxs-lookup"><span data-stu-id="2517c-768">A parameter transformer implements `IOutboundParameterTransformer` and transforms the value of parameters.</span></span> <span data-ttu-id="2517c-769">Například vlastní parametr `SlugifyParameterTransformer` Transformer změní hodnotu trasy `SubscriptionManagement` na `subscription-management`.</span><span class="sxs-lookup"><span data-stu-id="2517c-769">For example, a custom `SlugifyParameterTransformer` parameter transformer changes the `SubscriptionManagement` route value to `subscription-management`.</span></span>

<span data-ttu-id="2517c-770">`RouteTokenTransformerConvention` je konvence aplikačního modelu, kterou:</span><span class="sxs-lookup"><span data-stu-id="2517c-770">The `RouteTokenTransformerConvention` is an application model convention that:</span></span>

* <span data-ttu-id="2517c-771">Aplikuje transformátor parametrů na všechny trasy atributů v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="2517c-771">Applies a parameter transformer to all attribute routes in an application.</span></span>
* <span data-ttu-id="2517c-772">Přizpůsobí hodnoty tokenu trasy při jejich nahrazení.</span><span class="sxs-lookup"><span data-stu-id="2517c-772">Customizes the attribute route token values as they are replaced.</span></span>

```csharp
public class SubscriptionManagementController : Controller
{
    [HttpGet("[controller]/[action]")] // Matches '/subscription-management/list-all'
    public IActionResult ListAll() { ... }
}
```

<span data-ttu-id="2517c-773">`RouteTokenTransformerConvention` je v `ConfigureServices`zaregistrován jako možnost.</span><span class="sxs-lookup"><span data-stu-id="2517c-773">The `RouteTokenTransformerConvention` is registered as an option in `ConfigureServices`.</span></span>

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

### <a name="multiple-routes"></a><span data-ttu-id="2517c-774">Více tras</span><span class="sxs-lookup"><span data-stu-id="2517c-774">Multiple Routes</span></span>

<span data-ttu-id="2517c-775">Směrování atributů podporuje definování více tras, které dosáhnou stejné akce.</span><span class="sxs-lookup"><span data-stu-id="2517c-775">Attribute routing supports defining multiple routes that reach the same action.</span></span> <span data-ttu-id="2517c-776">Nejběžnějším využitím je napodobení chování *výchozí konvenční trasy* , jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="2517c-776">The most common usage of this is to mimic the behavior of the *default conventional route* as shown in the following example:</span></span>

```csharp
[Route("[controller]")]
public class ProductsController : Controller
{
   [Route("")]     // Matches 'Products'
   [Route("Index")] // Matches 'Products/Index'
   public IActionResult Index()
}
```

<span data-ttu-id="2517c-777">Vložení více atributů trasy na řadič znamená, že každá z nich bude kombinována s každým z atributů tras na metodách akcí.</span><span class="sxs-lookup"><span data-stu-id="2517c-777">Putting multiple route attributes on the controller means that each one will combine with each of the route attributes on the action methods.</span></span>

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

<span data-ttu-id="2517c-778">Pokud jsou na akci umístěny více atributů trasy (které implementují `IActionConstraint`), pak každé omezení akce kombinuje s šablonou směrování z atributu, který ji definoval.</span><span class="sxs-lookup"><span data-stu-id="2517c-778">When multiple route attributes (that implement `IActionConstraint`) are placed on an action, then each action constraint combines with the route template from the attribute that defined it.</span></span>

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
> <span data-ttu-id="2517c-779">Zatímco používání více tras na akcích může vypadat jako účinné, je lepší zachovat jednoduché a jasně definované místo URL vaší aplikace.</span><span class="sxs-lookup"><span data-stu-id="2517c-779">While using multiple routes on actions can seem powerful, it's better to keep your application's URL space simple and well-defined.</span></span> <span data-ttu-id="2517c-780">V případě potřeby použijte více tras na akcích, například pro podporu stávajících klientů.</span><span class="sxs-lookup"><span data-stu-id="2517c-780">Use multiple routes on actions only where needed, for example to support existing clients.</span></span>

<a name="routing-attr-options"></a>

### <a name="specifying-attribute-route-optional-parameters-default-values-and-constraints"></a><span data-ttu-id="2517c-781">Určení volitelných parametrů trasy atributu, výchozích hodnot a omezení</span><span class="sxs-lookup"><span data-stu-id="2517c-781">Specifying attribute route optional parameters, default values, and constraints</span></span>

<span data-ttu-id="2517c-782">Trasy atributů podporují stejnou vloženou syntaxi jako konvenční trasy, aby bylo možné zadat volitelné parametry, výchozí hodnoty a omezení.</span><span class="sxs-lookup"><span data-stu-id="2517c-782">Attribute routes support the same inline syntax as conventional routes to specify optional parameters, default values, and constraints.</span></span>

```csharp
[HttpPost("product/{id:int}")]
public IActionResult ShowProduct(int id)
{
   // ...
}
```

<span data-ttu-id="2517c-783">Podrobný popis syntaxe šablony směrování naleznete v tématu Referenční dokumentace k [šabloně směrování](xref:fundamentals/routing#route-template-reference) .</span><span class="sxs-lookup"><span data-stu-id="2517c-783">See [Route Template Reference](xref:fundamentals/routing#route-template-reference) for a detailed description of route template syntax.</span></span>

<a name="routing-cust-rt-attr-irt-ref-label"></a>

### <a name="custom-route-attributes-using-iroutetemplateprovider"></a><span data-ttu-id="2517c-784">Vlastní atributy směrování pomocí `IRouteTemplateProvider`</span><span class="sxs-lookup"><span data-stu-id="2517c-784">Custom route attributes using `IRouteTemplateProvider`</span></span>

<span data-ttu-id="2517c-785">Všechny atributy trasy, které jsou k dispozici v rozhraní (`[Route(...)]`, `[HttpGet(...)]` atd.) implementují rozhraní `IRouteTemplateProvider`.</span><span class="sxs-lookup"><span data-stu-id="2517c-785">All of the route attributes provided in the framework ( `[Route(...)]`, `[HttpGet(...)]` , etc.) implement the `IRouteTemplateProvider` interface.</span></span> <span data-ttu-id="2517c-786">MVC vyhledává atributy tříd kontroleru a metody akcí při spuštění aplikace a používá ty, které implementují `IRouteTemplateProvider` k sestavení počáteční sady tras.</span><span class="sxs-lookup"><span data-stu-id="2517c-786">MVC looks for attributes on controller classes and action methods when the app starts and uses the ones that implement `IRouteTemplateProvider` to build the initial set of routes.</span></span>

<span data-ttu-id="2517c-787">Můžete implementovat `IRouteTemplateProvider` k definování vlastních atributů tras.</span><span class="sxs-lookup"><span data-stu-id="2517c-787">You can implement `IRouteTemplateProvider` to define your own route attributes.</span></span> <span data-ttu-id="2517c-788">Každý `IRouteTemplateProvider` umožňuje definovat jednu trasu s vlastní šablonou směrování, objednávkou a názvem:</span><span class="sxs-lookup"><span data-stu-id="2517c-788">Each `IRouteTemplateProvider` allows you to define a single route with a custom route template, order, and name:</span></span>

```csharp
public class MyApiControllerAttribute : Attribute, IRouteTemplateProvider
{
   public string Template => "api/[controller]";

   public int? Order { get; set; }

   public string Name { get; set; }
}
```

<span data-ttu-id="2517c-789">Atribut z výše uvedeného příkladu automaticky nastaví `Template` na `"api/[controller]"` při použití `[MyApiController]`.</span><span class="sxs-lookup"><span data-stu-id="2517c-789">The attribute from the above example automatically sets the `Template` to `"api/[controller]"` when `[MyApiController]` is applied.</span></span>

<a name="routing-app-model-ref-label"></a>

### <a name="using-application-model-to-customize-attribute-routes"></a><span data-ttu-id="2517c-790">Přizpůsobení tras atributů pomocí aplikačního modelu</span><span class="sxs-lookup"><span data-stu-id="2517c-790">Using Application Model to customize attribute routes</span></span>

<span data-ttu-id="2517c-791">*Aplikační model* je objektový model vytvořený při spuštění se všemi metadaty, které MVC používá ke směrování a provádění akcí.</span><span class="sxs-lookup"><span data-stu-id="2517c-791">The *application model* is an object model created at startup with all of the metadata used by MVC to route and execute your actions.</span></span> <span data-ttu-id="2517c-792">*Aplikační model* zahrnuje všechna data získaná z atributů směrování (prostřednictvím `IRouteTemplateProvider`).</span><span class="sxs-lookup"><span data-stu-id="2517c-792">The *application model* includes all of the data gathered from route attributes (through `IRouteTemplateProvider`).</span></span> <span data-ttu-id="2517c-793">Můžete napsat *konvence* pro úpravu aplikačního modelu v době spuštění, abyste mohli přizpůsobit způsob, jakým se směrování chová.</span><span class="sxs-lookup"><span data-stu-id="2517c-793">You can write *conventions* to modify the application model at startup time to customize how routing behaves.</span></span> <span data-ttu-id="2517c-794">V této části se dozvíte jednoduchý příklad přizpůsobení směrování pomocí aplikačního modelu.</span><span class="sxs-lookup"><span data-stu-id="2517c-794">This section shows a simple example of customizing routing using application model.</span></span>

[!code-csharp[](routing/samples/2.x/main/NamespaceRoutingConvention.cs)]

<a name="routing-mixed-ref-label"></a>

## <a name="mixed-routing-attribute-routing-vs-conventional-routing"></a><span data-ttu-id="2517c-795">Kombinované směrování: směrování atributů vs. konvenční směrování</span><span class="sxs-lookup"><span data-stu-id="2517c-795">Mixed routing: Attribute routing vs conventional routing</span></span>

<span data-ttu-id="2517c-796">Aplikace MVC můžou kombinovat použití konvenčního směrování a směrování atributů.</span><span class="sxs-lookup"><span data-stu-id="2517c-796">MVC applications can mix the use of conventional routing and attribute routing.</span></span> <span data-ttu-id="2517c-797">Pro řadiče, které obsluhují stránky HTML pro prohlížeče, a směrování atributů pro řadiče obsluhující rozhraní REST API se obvykle používá konvenční trasy.</span><span class="sxs-lookup"><span data-stu-id="2517c-797">It's typical to use conventional routes for controllers serving HTML pages for browsers, and attribute routing for controllers serving REST APIs.</span></span>

<span data-ttu-id="2517c-798">Akce jsou směrovány buď podle konvence nebo směrovaného atributu.</span><span class="sxs-lookup"><span data-stu-id="2517c-798">Actions are either conventionally routed or attribute routed.</span></span> <span data-ttu-id="2517c-799">Umístěním trasy do kontroleru nebo akce se nasměruje atribut IT.</span><span class="sxs-lookup"><span data-stu-id="2517c-799">Placing a route on the controller or the action makes it attribute routed.</span></span> <span data-ttu-id="2517c-800">Akce definující trasy atributů nemůžou být dostupné prostřednictvím konvenčních tras a naopak.</span><span class="sxs-lookup"><span data-stu-id="2517c-800">Actions that define attribute routes cannot be reached through the conventional routes and vice-versa.</span></span> <span data-ttu-id="2517c-801">**Libovolný** atribut směrování v řadiči provádí směrování všech akcí v atributu kontroleru.</span><span class="sxs-lookup"><span data-stu-id="2517c-801">**Any** route attribute on the controller makes all actions in the controller attribute routed.</span></span>

> [!NOTE]
> <span data-ttu-id="2517c-802">Způsob, jakým se rozlišují dva typy systémů směrování, je proces, který se použije po tom, co adresa URL odpovídá šabloně směrování.</span><span class="sxs-lookup"><span data-stu-id="2517c-802">What distinguishes the two types of routing systems is the process applied after a URL matches a route template.</span></span> <span data-ttu-id="2517c-803">V části konvenční směrování se při výběru akce a kontroleru z vyhledávací tabulky všech konvenčních akcí směrování používají hodnoty tras z shody.</span><span class="sxs-lookup"><span data-stu-id="2517c-803">In conventional routing, the route values from the match are used to choose the action and controller from a lookup table of all conventional routed actions.</span></span> <span data-ttu-id="2517c-804">V směrování atributů je Každá šablona již přidružena k akci a není nutné žádné další vyhledávání.</span><span class="sxs-lookup"><span data-stu-id="2517c-804">In attribute routing, each template is already associated with an action, and no further lookup is needed.</span></span>

## <a name="complex-segments"></a><span data-ttu-id="2517c-805">Komplexní segmenty</span><span class="sxs-lookup"><span data-stu-id="2517c-805">Complex segments</span></span>

<span data-ttu-id="2517c-806">Komplexní segmenty (například `[Route("/dog{token}cat")]`) jsou zpracovávány porovnáním koncových literálů zprava doleva nehladým způsobem.</span><span class="sxs-lookup"><span data-stu-id="2517c-806">Complex segments (for example, `[Route("/dog{token}cat")]`), are processed by matching up literals from right to left in a non-greedy way.</span></span> <span data-ttu-id="2517c-807">Popis najdete [ve zdrojovém kódu](https://github.com/aspnet/Routing/blob/9cea167cfac36cf034dbb780e3f783114ef94780/src/Microsoft.AspNetCore.Routing/Patterns/RoutePatternMatcher.cs#L296) .</span><span class="sxs-lookup"><span data-stu-id="2517c-807">See [the source code](https://github.com/aspnet/Routing/blob/9cea167cfac36cf034dbb780e3f783114ef94780/src/Microsoft.AspNetCore.Routing/Patterns/RoutePatternMatcher.cs#L296) for a description.</span></span> <span data-ttu-id="2517c-808">Další informace najdete v [tomto problému](https://github.com/dotnet/AspNetCore.Docs/issues/8197).</span><span class="sxs-lookup"><span data-stu-id="2517c-808">For more information, see [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/8197).</span></span>

<a name="routing-url-gen-ref-label"></a>

## <a name="url-generation"></a><span data-ttu-id="2517c-809">Generování adresy URL</span><span class="sxs-lookup"><span data-stu-id="2517c-809">URL Generation</span></span>

<span data-ttu-id="2517c-810">Aplikace MVC můžou použít funkce generování adresy URL směrování k vygenerování odkazů na akce v adresách URL.</span><span class="sxs-lookup"><span data-stu-id="2517c-810">MVC applications can use routing's URL generation features to generate URL links to actions.</span></span> <span data-ttu-id="2517c-811">Generování adres URL eliminuje adresy URL zakódujeme, takže je váš kód robustnější a udržovatelný.</span><span class="sxs-lookup"><span data-stu-id="2517c-811">Generating URLs eliminates hardcoding URLs, making your code more robust and maintainable.</span></span> <span data-ttu-id="2517c-812">Tato část se zaměřuje na funkce generování adresy URL poskytované MVC a bude se týkat jenom základních informací o tom, jak generování adresy URL funguje.</span><span class="sxs-lookup"><span data-stu-id="2517c-812">This section focuses on the URL generation features provided by MVC and will only cover basics of how URL generation works.</span></span> <span data-ttu-id="2517c-813">Podrobný popis generování adresy URL najdete v tématu věnovaném [Směrování](xref:fundamentals/routing) .</span><span class="sxs-lookup"><span data-stu-id="2517c-813">See [Routing](xref:fundamentals/routing) for a detailed description of URL generation.</span></span>

<span data-ttu-id="2517c-814">Rozhraní `IUrlHelper` je základní částí infrastruktury mezi MVC a směrováním pro generování adresy URL.</span><span class="sxs-lookup"><span data-stu-id="2517c-814">The `IUrlHelper` interface is the underlying piece of infrastructure between MVC and routing for URL generation.</span></span> <span data-ttu-id="2517c-815">V části řadiče, zobrazení a zobrazení komponent najdete instanci `IUrlHelper` k dispozici prostřednictvím vlastnosti `Url`.</span><span class="sxs-lookup"><span data-stu-id="2517c-815">You'll find an instance of `IUrlHelper` available through the `Url` property in controllers, views, and view components.</span></span>

<span data-ttu-id="2517c-816">V tomto příkladu je `IUrlHelper` rozhraní použito prostřednictvím vlastnosti `Controller.Url` pro vygenerování adresy URL jiné akci.</span><span class="sxs-lookup"><span data-stu-id="2517c-816">In this example, the `IUrlHelper` interface is used through the `Controller.Url` property to generate a URL to another action.</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationController.cs?name=snippet_1)]

<span data-ttu-id="2517c-817">Pokud aplikace používá výchozí konvenční trasu, hodnota proměnné `url` bude řetězec cesty adresy URL `/UrlGeneration/Destination`.</span><span class="sxs-lookup"><span data-stu-id="2517c-817">If the application is using the default conventional route, the value of the `url` variable will be the URL path string `/UrlGeneration/Destination`.</span></span> <span data-ttu-id="2517c-818">Tato cesta URL se vytvoří směrováním kombinováním hodnot tras z aktuální žádosti (okolní hodnoty) s hodnotami předanými do `Url.Action` a nahrazením těchto hodnot do šablony trasy:</span><span class="sxs-lookup"><span data-stu-id="2517c-818">This URL path is created by routing by combining the route values from the current request (ambient values), with the values passed to `Url.Action` and substituting those values into the route template:</span></span>

```
ambient values: { controller = "UrlGeneration", action = "Source" }
values passed to Url.Action: { controller = "UrlGeneration", action = "Destination" }
route template: {controller}/{action}/{id?}

result: /UrlGeneration/Destination
```

<span data-ttu-id="2517c-819">Každý parametr trasy v šabloně směrování má jeho hodnotu nahrazenou odpovídajícími názvy hodnotami a okolními hodnotami.</span><span class="sxs-lookup"><span data-stu-id="2517c-819">Each route parameter in the route template has its value substituted by matching names with the values and ambient values.</span></span> <span data-ttu-id="2517c-820">Parametr trasy, který nemá hodnotu, může používat výchozí hodnotu, pokud má jednu, nebo může být vynechána, pokud je volitelná (jako v případě `id` v tomto příkladu).</span><span class="sxs-lookup"><span data-stu-id="2517c-820">A route parameter that doesn't have a value can use a default value if it has one, or be skipped if it's optional (as in the case of `id` in this example).</span></span> <span data-ttu-id="2517c-821">Generování adresy URL selže, pokud žádný požadovaný parametr trasy nemá odpovídající hodnotu.</span><span class="sxs-lookup"><span data-stu-id="2517c-821">URL generation will fail if any required route parameter doesn't have a corresponding value.</span></span> <span data-ttu-id="2517c-822">Pokud se generování adresy URL u trasy nezdařilo, bude se zkoušet další trasa, dokud nebudou všechny trasy vyzkoušeny nebo nalezena shoda.</span><span class="sxs-lookup"><span data-stu-id="2517c-822">If URL generation fails for a route, the next route is tried until all routes have been tried or a match is found.</span></span>

<span data-ttu-id="2517c-823">Příklad `Url.Action` výše předpokládá konvenční směrování, ale generování adresy URL funguje podobně jako při směrování atributů, i když se koncepty liší.</span><span class="sxs-lookup"><span data-stu-id="2517c-823">The example of `Url.Action` above assumes conventional routing, but URL generation works similarly with attribute routing, though the concepts are different.</span></span> <span data-ttu-id="2517c-824">Při použití konvenčního směrování se hodnoty tras používají k rozbalení šablony a hodnoty tras pro `controller` a `action` obvykle se v této šabloně zobrazují – to funguje, protože adresy URL, které odpovídají směrování, dodržují *konvence*.</span><span class="sxs-lookup"><span data-stu-id="2517c-824">With conventional routing, the route values are used to expand a template, and the route values for `controller` and `action` usually appear in that template - this works because the URLs matched by routing adhere to a *convention*.</span></span> <span data-ttu-id="2517c-825">V případě směrování atributů se v šabloně nemůžou zobrazovat hodnoty tras pro `controller` a `action`, které se místo toho používají k vyhledání šablony, která se má použít.</span><span class="sxs-lookup"><span data-stu-id="2517c-825">In attribute routing, the route values for `controller` and `action` are not allowed to appear in the template - they're instead used to look up which template to use.</span></span>

<span data-ttu-id="2517c-826">V tomto příkladu se používá směrování atributů:</span><span class="sxs-lookup"><span data-stu-id="2517c-826">This example uses attribute routing:</span></span>

[!code-csharp[](routing/samples/2.x/main/StartupUseMvc.cs?name=snippet_1)]

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationControllerAttr.cs?name=snippet_1)]

<span data-ttu-id="2517c-827">MVC vytvoří vyhledávací tabulku všech akcí směrovaného atributu a bude odpovídat hodnotám `controller` a `action` a vybrat šablonu směrování, která se má použít pro generování adresy URL.</span><span class="sxs-lookup"><span data-stu-id="2517c-827">MVC builds a lookup table of all attribute routed actions and will match the `controller` and `action` values to select the route template to use for URL generation.</span></span> <span data-ttu-id="2517c-828">V ukázce výše se vygeneruje `custom/url/to/destination`.</span><span class="sxs-lookup"><span data-stu-id="2517c-828">In the sample above,   `custom/url/to/destination` is generated.</span></span>

### <a name="generating-urls-by-action-name"></a><span data-ttu-id="2517c-829">Generování adres URL podle názvu akce</span><span class="sxs-lookup"><span data-stu-id="2517c-829">Generating URLs by action name</span></span>

<span data-ttu-id="2517c-830">`Url.Action` (`IUrlHelper`.</span><span class="sxs-lookup"><span data-stu-id="2517c-830">`Url.Action` (`IUrlHelper` .</span></span> <span data-ttu-id="2517c-831">`Action`) a všechna související přetížení jsou založena na této myšlence, se kterou chcete určit, k čemu se odkazuje, zadáním názvu kontroleru a názvu akce.</span><span class="sxs-lookup"><span data-stu-id="2517c-831">`Action`) and all related overloads all are based on that idea that you want to specify what you're linking to by specifying a controller name and action name.</span></span>

> [!NOTE]
> <span data-ttu-id="2517c-832">Při použití `Url.Action`jsou aktuální hodnoty trasy pro `controller` a `action` určené pro vás – hodnota `controller` a `action` jsou součástí obou *okolních hodnot* **a** *hodnot*.</span><span class="sxs-lookup"><span data-stu-id="2517c-832">When using `Url.Action`, the current route values for `controller` and `action` are specified for you - the value of `controller` and `action` are part of both *ambient values* **and** *values*.</span></span> <span data-ttu-id="2517c-833">Metoda `Url.Action`, vždy používá aktuální hodnoty `action` a `controller` a vygeneruje cestu URL, která bude směrovat na aktuální akci.</span><span class="sxs-lookup"><span data-stu-id="2517c-833">The method `Url.Action`, always uses the current values of `action` and `controller` and will generate a URL path that routes to the current action.</span></span>

<span data-ttu-id="2517c-834">Směrování se pokusí použít hodnoty v ambientních hodnotách k vyplnění informací, které jste nezadali při generování adresy URL.</span><span class="sxs-lookup"><span data-stu-id="2517c-834">Routing attempts to use the values in ambient values to fill in information that you didn't provide when generating a URL.</span></span> <span data-ttu-id="2517c-835">Při použití trasy, jako je `{a}/{b}/{c}/{d}` a ambientních hodnot `{ a = Alice, b = Bob, c = Carol, d = David }`, směrování obsahuje dostatek informací pro vygenerování adresy URL bez dalších hodnot – protože všechny parametry tras mají hodnotu.</span><span class="sxs-lookup"><span data-stu-id="2517c-835">Using a route like `{a}/{b}/{c}/{d}` and ambient values `{ a = Alice, b = Bob, c = Carol, d = David }`, routing has enough information to generate a URL without any additional values - since all route parameters have a value.</span></span> <span data-ttu-id="2517c-836">Pokud jste přidali hodnotu `{ d = Donovan }`, hodnota `{ d = David }` by byla ignorována a vygenerovaná cesta URL bude `Alice/Bob/Carol/Donovan`.</span><span class="sxs-lookup"><span data-stu-id="2517c-836">If you added the value `{ d = Donovan }`, the value `{ d = David }` would be ignored, and the generated URL path would be `Alice/Bob/Carol/Donovan`.</span></span>

> [!WARNING]
> <span data-ttu-id="2517c-837">Cesty URL jsou hierarchické.</span><span class="sxs-lookup"><span data-stu-id="2517c-837">URL paths are hierarchical.</span></span> <span data-ttu-id="2517c-838">Pokud jste v předchozím příkladu přidali hodnotu `{ c = Cheryl }`, budou obě hodnoty `{ c = Carol, d = David }` ignorovány.</span><span class="sxs-lookup"><span data-stu-id="2517c-838">In the example above, if you added the value `{ c = Cheryl }`, both of the values `{ c = Carol, d = David }` would be ignored.</span></span> <span data-ttu-id="2517c-839">V tomto případě již nemusíte mít hodnotu pro `d` a generování adresy URL selže.</span><span class="sxs-lookup"><span data-stu-id="2517c-839">In this case we no longer have a value for `d` and URL generation will fail.</span></span> <span data-ttu-id="2517c-840">Je nutné zadat požadovanou hodnotu `c` a `d`.</span><span class="sxs-lookup"><span data-stu-id="2517c-840">You would need to specify the desired value of `c` and `d`.</span></span>  <span data-ttu-id="2517c-841">Můžete očekávat, že se tento problém bude nacházet s výchozí trasou (`{controller}/{action}/{id?}`), ale toto chování se bude v praxi chovat zřídka, protože `Url.Action` vždy explicitně specifikuje `controller` a `action` hodnotu.</span><span class="sxs-lookup"><span data-stu-id="2517c-841">You might expect to hit this problem with the default route (`{controller}/{action}/{id?}`) - but you will rarely encounter this behavior in practice as `Url.Action` will always explicitly specify a `controller` and `action` value.</span></span>

<span data-ttu-id="2517c-842">Delší přetížení `Url.Action` také přijímají další objekt *hodnoty trasy* , který poskytuje hodnoty pro parametry směrování jiné než `controller` a `action`.</span><span class="sxs-lookup"><span data-stu-id="2517c-842">Longer overloads of `Url.Action` also take an additional *route values* object to provide values for route parameters other than `controller` and `action`.</span></span> <span data-ttu-id="2517c-843">Nejčastěji to vidíte, že se používá `id` jako `Url.Action("Buy", "Products", new { id = 17 })`.</span><span class="sxs-lookup"><span data-stu-id="2517c-843">You will most commonly see this used with `id` like `Url.Action("Buy", "Products", new { id = 17 })`.</span></span> <span data-ttu-id="2517c-844">Podle konvence objekt *hodnoty trasy* je obvykle objektem anonymního typu, ale může se také jednat o `IDictionary<>` nebo o *prostý starý objekt .NET*.</span><span class="sxs-lookup"><span data-stu-id="2517c-844">By convention the *route values* object is usually an object of anonymous type, but it can also be an `IDictionary<>` or a *plain old .NET object*.</span></span> <span data-ttu-id="2517c-845">Všechny další hodnoty tras, které neodpovídají parametrům směrování, jsou vloženy do řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="2517c-845">Any additional route values that don't match route parameters are put in the query string.</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/TestController.cs)]

> [!TIP]
> <span data-ttu-id="2517c-846">Chcete-li vytvořit absolutní adresu URL, použijte přetížení, které přijímá `protocol`: `Url.Action("Buy", "Products", new { id = 17 }, protocol: Request.Scheme)`</span><span class="sxs-lookup"><span data-stu-id="2517c-846">To create an absolute URL, use an overload that accepts a `protocol`: `Url.Action("Buy", "Products", new { id = 17 }, protocol: Request.Scheme)`</span></span>

<a name="routing-gen-urls-route-ref-label"></a>

### <a name="generating-urls-by-route"></a><span data-ttu-id="2517c-847">Generování adres URL podle směrování</span><span class="sxs-lookup"><span data-stu-id="2517c-847">Generating URLs by route</span></span>

<span data-ttu-id="2517c-848">Výše uvedený kód ukázal generování adresy URL předáním do kontroleru a názvu akce.</span><span class="sxs-lookup"><span data-stu-id="2517c-848">The code above demonstrated generating a URL by passing in the controller and action name.</span></span> <span data-ttu-id="2517c-849">`IUrlHelper` také poskytuje `Url.RouteUrl` rodinu metod.</span><span class="sxs-lookup"><span data-stu-id="2517c-849">`IUrlHelper` also provides the `Url.RouteUrl` family of methods.</span></span> <span data-ttu-id="2517c-850">Tyto metody jsou podobné `Url.Action`, ale nekopírují aktuální hodnoty `action` a `controller` na hodnoty tras.</span><span class="sxs-lookup"><span data-stu-id="2517c-850">These methods are similar to `Url.Action`, but they don't copy the current values of `action` and `controller` to the route values.</span></span> <span data-ttu-id="2517c-851">Nejběžnějším využitím je zadání názvu trasy pro použití konkrétní trasy pro vygenerování adresy URL, a to obecně *bez* zadání kontroleru nebo názvu akce.</span><span class="sxs-lookup"><span data-stu-id="2517c-851">The most common usage is to specify a route name to use a specific route to generate the URL, generally *without* specifying a controller or action name.</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationControllerRouting.cs?name=snippet_1)]

<a name="routing-gen-urls-html-ref-label"></a>

### <a name="generating-urls-in-html"></a><span data-ttu-id="2517c-852">Generování adres URL v HTML</span><span class="sxs-lookup"><span data-stu-id="2517c-852">Generating URLs in HTML</span></span>

<span data-ttu-id="2517c-853">`IHtmlHelper` poskytuje `HtmlHelper` metody `Html.BeginForm` a `Html.ActionLink` k vygenerování `<form>` a `<a>` prvků v uvedeném pořadí.</span><span class="sxs-lookup"><span data-stu-id="2517c-853">`IHtmlHelper` provides the `HtmlHelper` methods `Html.BeginForm` and `Html.ActionLink` to generate `<form>` and `<a>` elements respectively.</span></span> <span data-ttu-id="2517c-854">Tyto metody používají metodu `Url.Action` k vygenerování adresy URL a akceptuje podobné argumenty.</span><span class="sxs-lookup"><span data-stu-id="2517c-854">These methods use the `Url.Action` method to generate a URL and they accept similar arguments.</span></span> <span data-ttu-id="2517c-855">`Url.RouteUrl` doprovodné funkce pro `HtmlHelper` jsou `Html.BeginRouteForm` a `Html.RouteLink` s podobnými funkcemi.</span><span class="sxs-lookup"><span data-stu-id="2517c-855">The `Url.RouteUrl` companions for `HtmlHelper` are `Html.BeginRouteForm` and `Html.RouteLink` which have similar functionality.</span></span>

<span data-ttu-id="2517c-856">TagHelpers vygeneruje adresy URL prostřednictvím `form` Taghelperu a `<a>` Taghelperu.</span><span class="sxs-lookup"><span data-stu-id="2517c-856">TagHelpers generate URLs through the `form` TagHelper and the `<a>` TagHelper.</span></span> <span data-ttu-id="2517c-857">Obě tato použití `IUrlHelper` pro jejich implementaci.</span><span class="sxs-lookup"><span data-stu-id="2517c-857">Both of these use `IUrlHelper` for their implementation.</span></span> <span data-ttu-id="2517c-858">Další informace najdete v tématu [práce s formuláři](../views/working-with-forms.md) .</span><span class="sxs-lookup"><span data-stu-id="2517c-858">See [Working with Forms](../views/working-with-forms.md) for more information.</span></span>

<span data-ttu-id="2517c-859">V zobrazeních jsou `IUrlHelper` k dispozici prostřednictvím vlastnosti `Url` pro všechny generace adres URL ad-hoc, které nejsou pokryté výše.</span><span class="sxs-lookup"><span data-stu-id="2517c-859">Inside views, the `IUrlHelper` is available through the `Url` property for any ad-hoc URL generation not covered by the above.</span></span>

<a name="routing-gen-urls-action-ref-label"></a>

### <a name="generating-urls-in-action-results"></a><span data-ttu-id="2517c-860">Generování adres URL ve výsledcích akcí</span><span class="sxs-lookup"><span data-stu-id="2517c-860">Generating URLS in Action Results</span></span>

<span data-ttu-id="2517c-861">Výše uvedené příklady ukazují použití `IUrlHelper` v kontroleru, zatímco nejběžnější využití v kontroleru vygeneruje adresu URL jako součást výsledku akce.</span><span class="sxs-lookup"><span data-stu-id="2517c-861">The examples above have shown using `IUrlHelper` in a controller, while the most common usage in a controller is to generate a URL as part of an action result.</span></span>

<span data-ttu-id="2517c-862">Základní třídy `ControllerBase` a `Controller` poskytují pohodlný způsob pro výsledky akcí, které odkazují na jinou akci.</span><span class="sxs-lookup"><span data-stu-id="2517c-862">The `ControllerBase` and `Controller` base classes provide convenience methods for action results that reference another action.</span></span> <span data-ttu-id="2517c-863">Jedním z typických použití je přesměrování po přijetí vstupu uživatele.</span><span class="sxs-lookup"><span data-stu-id="2517c-863">One typical usage is to redirect after accepting user input.</span></span>

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

<span data-ttu-id="2517c-864">Metody pro vytváření výsledků pro zpracování akcí dodrží podobný vzor pro metody `IUrlHelper`.</span><span class="sxs-lookup"><span data-stu-id="2517c-864">The action results factory methods follow a similar pattern to the methods on `IUrlHelper`.</span></span>

<a name="routing-dedicated-ref-label"></a>

### <a name="special-case-for-dedicated-conventional-routes"></a><span data-ttu-id="2517c-865">Zvláštní případ pro vyhrazené konvenční trasy</span><span class="sxs-lookup"><span data-stu-id="2517c-865">Special case for dedicated conventional routes</span></span>

<span data-ttu-id="2517c-866">Konvenční směrování může používat speciální druh definice trasy, která se označuje jako *vyhrazená konvenční trasa*.</span><span class="sxs-lookup"><span data-stu-id="2517c-866">Conventional routing can use a special kind of route definition called a *dedicated conventional route*.</span></span> <span data-ttu-id="2517c-867">V následujícím příkladu je trasa s názvem `blog` vyhrazenou konvenční trasou.</span><span class="sxs-lookup"><span data-stu-id="2517c-867">In the example below, the route named `blog` is a dedicated conventional route.</span></span>

```csharp
app.UseMvc(routes =>
{
    routes.MapRoute("blog", "blog/{*article}",
        defaults: new { controller = "Blog", action = "Article" });
    routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="2517c-868">Pomocí těchto definic tras `Url.Action("Index", "Home")` vygeneruje cestu URL `/` s `default` trasou, ale proč?</span><span class="sxs-lookup"><span data-stu-id="2517c-868">Using these route definitions, `Url.Action("Index", "Home")` will generate the URL path `/` with the `default` route, but why?</span></span> <span data-ttu-id="2517c-869">Můžete odhadnout hodnoty tras, `{ controller = Home, action = Index }` by bylo dostatečné pro vygenerování adresy URL pomocí `blog`a výsledek by byl `/blog?action=Index&controller=Home`.</span><span class="sxs-lookup"><span data-stu-id="2517c-869">You might guess the route values `{ controller = Home, action = Index }` would be enough to generate a URL using `blog`, and the result would be `/blog?action=Index&controller=Home`.</span></span>

<span data-ttu-id="2517c-870">Vyhrazené konvenční trasy spoléhají na speciální chování výchozích hodnot, které nemají odpovídající parametr trasy, který brání tomu, aby trasa byla "příliš hlade" s generováním adresy URL.</span><span class="sxs-lookup"><span data-stu-id="2517c-870">Dedicated conventional routes rely on a special behavior of default values that don't have a corresponding route parameter that prevents the route from being "too greedy" with URL generation.</span></span> <span data-ttu-id="2517c-871">V tomto případě jsou `{ controller = Blog, action = Article }`výchozí hodnoty a ani `controller` ani `action` se nezobrazí jako parametr trasy.</span><span class="sxs-lookup"><span data-stu-id="2517c-871">In this case the default values are `{ controller = Blog, action = Article }`, and neither `controller` nor `action` appears as a route parameter.</span></span> <span data-ttu-id="2517c-872">Když směrování provádí generování adresy URL, zadané hodnoty musí odpovídat výchozím hodnotám.</span><span class="sxs-lookup"><span data-stu-id="2517c-872">When routing performs URL generation, the values provided must match the default values.</span></span> <span data-ttu-id="2517c-873">Generování adresy URL pomocí `blog` se nezdaří, protože hodnoty `{ controller = Home, action = Index }` neodpovídají `{ controller = Blog, action = Article }`.</span><span class="sxs-lookup"><span data-stu-id="2517c-873">URL generation using `blog` will fail because the values `{ controller = Home, action = Index }` don't match `{ controller = Blog, action = Article }`.</span></span> <span data-ttu-id="2517c-874">Směrování se pak vrátí k pokusu o `default`, který je úspěšný.</span><span class="sxs-lookup"><span data-stu-id="2517c-874">Routing then falls back to try `default`, which succeeds.</span></span>

<a name="routing-areas-ref-label"></a>

## <a name="areas"></a><span data-ttu-id="2517c-875">Oblasti</span><span class="sxs-lookup"><span data-stu-id="2517c-875">Areas</span></span>

<span data-ttu-id="2517c-876">[Oblasti](areas.md) jsou funkce MVC, která slouží k uspořádání souvisejících funkcí do skupiny jako samostatného oboru názvů směrování (pro akce kontroleru) a struktury složek (pro zobrazení).</span><span class="sxs-lookup"><span data-stu-id="2517c-876">[Areas](areas.md) are an MVC feature used to organize related functionality into a group as a separate routing-namespace (for controller actions) and folder structure (for views).</span></span> <span data-ttu-id="2517c-877">Použití oblastí umožňuje aplikaci mít více řadičů se stejným názvem, pokud mají různé *oblasti*.</span><span class="sxs-lookup"><span data-stu-id="2517c-877">Using areas allows an application to have multiple controllers with the same name - as long as they have different *areas*.</span></span> <span data-ttu-id="2517c-878">Použití oblastí vytvoří hierarchii pro účely směrování přidáním dalšího parametru trasy `area` `controller` a `action`.</span><span class="sxs-lookup"><span data-stu-id="2517c-878">Using areas creates a hierarchy for the purpose of routing by adding another route parameter, `area` to `controller` and `action`.</span></span> <span data-ttu-id="2517c-879">V této části se dozvíte, jak směrování komunikuje s oblastmi – viz [oblasti](areas.md) pro podrobnosti o použití oblastí se zobrazeními.</span><span class="sxs-lookup"><span data-stu-id="2517c-879">This section will discuss how routing interacts with areas - see [Areas](areas.md) for details about how areas are used with views.</span></span>

<span data-ttu-id="2517c-880">V následujícím příkladu je nakonfiguruje MVC pro použití výchozí konvenční trasy a *trasy oblasti* pro oblast s názvem `Blog`:</span><span class="sxs-lookup"><span data-stu-id="2517c-880">The following example configures MVC to use the default conventional route and an *area route* for an area named `Blog`:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet1)]

<span data-ttu-id="2517c-881">Když se porovná cesta URL jako `/Manage/Users/AddUser`, první trasa vytvoří hodnoty trasy `{ area = Blog, controller = Users, action = AddUser }`.</span><span class="sxs-lookup"><span data-stu-id="2517c-881">When matching a URL path like `/Manage/Users/AddUser`, the first route will produce the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span> <span data-ttu-id="2517c-882">Hodnota `area` trasy je vytvořena výchozí hodnotou pro `area`, ve skutečnosti je trasa vytvořená v `MapAreaRoute` shodná s následujícím:</span><span class="sxs-lookup"><span data-stu-id="2517c-882">The `area` route value is produced by a default value for `area`, in fact the route created by `MapAreaRoute` is equivalent to the following:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet2)]

<span data-ttu-id="2517c-883">`MapAreaRoute` vytvoří trasu pomocí výchozí hodnoty i omezení pro `area` pomocí zadaného názvu oblasti v tomto případě `Blog`.</span><span class="sxs-lookup"><span data-stu-id="2517c-883">`MapAreaRoute` creates a route using both a default value and constraint for `area` using the provided area name, in this case `Blog`.</span></span> <span data-ttu-id="2517c-884">Výchozí hodnota zajistí, že trasa vždy vytvoří `{ area = Blog, ... }`, omezení vyžaduje `{ area = Blog, ... }` hodnoty pro generování adresy URL.</span><span class="sxs-lookup"><span data-stu-id="2517c-884">The default value ensures that the route always produces `{ area = Blog, ... }`, the constraint requires the value `{ area = Blog, ... }` for URL generation.</span></span>

> [!TIP]
> <span data-ttu-id="2517c-885">Konvenční směrování je závislé na pořadí.</span><span class="sxs-lookup"><span data-stu-id="2517c-885">Conventional routing is order-dependent.</span></span> <span data-ttu-id="2517c-886">Obecně platí, že trasy s oblastmi by měly být umístěny dříve v tabulce směrování, protože jsou konkrétnější než trasy bez oblasti.</span><span class="sxs-lookup"><span data-stu-id="2517c-886">In general, routes with areas should be placed earlier in the route table as they're more specific than routes without an area.</span></span>

<span data-ttu-id="2517c-887">Pomocí výše uvedeného příkladu budou hodnoty směrování odpovídat následující akci:</span><span class="sxs-lookup"><span data-stu-id="2517c-887">Using the above example, the route values would match the following action:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

<span data-ttu-id="2517c-888">`AreaAttribute` je to, že jako součást oblasti označuje kontroler, říkáme, že tento kontroler je v oblasti `Blog`.</span><span class="sxs-lookup"><span data-stu-id="2517c-888">The `AreaAttribute` is what denotes a controller as part of an area, we say that this controller is in the `Blog` area.</span></span> <span data-ttu-id="2517c-889">Řadiče bez atributu `[Area]` nejsou členy žádné oblasti **a nebudou se shodovat,** Pokud je hodnota `area` trasy poskytnuta směrováním.</span><span class="sxs-lookup"><span data-stu-id="2517c-889">Controllers without an `[Area]` attribute are not members of any area, and will **not** match when the `area` route value is provided by routing.</span></span> <span data-ttu-id="2517c-890">V následujícím příkladu se může shodovat jenom první kontroler, který bude odpovídat hodnotám tras `{ area = Blog, controller = Users, action = AddUser }`.</span><span class="sxs-lookup"><span data-stu-id="2517c-890">In the following example, only the first controller listed can match the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Zebra/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/UsersController.cs)]

> [!NOTE]
> <span data-ttu-id="2517c-891">Obor názvů každého kontroleru se tady zobrazuje pro úplnost – jinak by řadiče vyžadovaly konflikt názvů a generovaly chybu kompilátoru.</span><span class="sxs-lookup"><span data-stu-id="2517c-891">The namespace of each controller is shown here for completeness - otherwise the controllers would have a naming conflict and generate a compiler error.</span></span> <span data-ttu-id="2517c-892">Obory názvů třídy nemají žádný vliv na směrování MVC.</span><span class="sxs-lookup"><span data-stu-id="2517c-892">Class namespaces have no effect on MVC's routing.</span></span>

<span data-ttu-id="2517c-893">První dva řadiče jsou členy oblastí a shodují se pouze v případě, že je jejich název příslušné oblasti poskytovaný hodnotou `area` trasy.</span><span class="sxs-lookup"><span data-stu-id="2517c-893">The first two controllers are members of areas, and only match when their respective area name is provided by the `area` route value.</span></span> <span data-ttu-id="2517c-894">Třetí kontroler není členem žádné oblasti a může odpovídat jenom v případě, že směrováním neposkytuje žádnou hodnotu pro `area`.</span><span class="sxs-lookup"><span data-stu-id="2517c-894">The third controller isn't a member of any area, and can only match when no value for `area` is provided by routing.</span></span>

> [!NOTE]
> <span data-ttu-id="2517c-895">V souvislosti s neshodnou *hodnotou*je absence hodnoty `area` stejná, jako kdyby hodnota `area` měla hodnotu null nebo prázdný řetězec.</span><span class="sxs-lookup"><span data-stu-id="2517c-895">In terms of matching *no value*, the absence of the `area` value is the same as if the value for `area` were null or the empty string.</span></span>

<span data-ttu-id="2517c-896">Při provádění akce uvnitř oblasti bude hodnota trasy pro `area` k dispozici jako *ambientní hodnota* pro směrování, která se má použít pro generování adresy URL.</span><span class="sxs-lookup"><span data-stu-id="2517c-896">When executing an action inside an area, the route value for `area` will be available as an *ambient value* for routing to use for URL generation.</span></span> <span data-ttu-id="2517c-897">To znamená, že ve výchozím nastavení jsou tyto oblasti pro generování adresy URL *rychlé* , jak je znázorněno v následující ukázce.</span><span class="sxs-lookup"><span data-stu-id="2517c-897">This means that by default areas act *sticky* for URL generation as demonstrated by the following sample.</span></span>
[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet3)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Duck/Controllers/UsersController.cs)]

<a name="iactionconstraint-ref-label"></a>

## <a name="understanding-iactionconstraint"></a><span data-ttu-id="2517c-898">Principy IActionConstraint</span><span class="sxs-lookup"><span data-stu-id="2517c-898">Understanding IActionConstraint</span></span>

> [!NOTE]
> <span data-ttu-id="2517c-899">Tato část je hlubokým podrobněem interních platforem a způsobu, jakým MVC vybírá akci, která se má provést.</span><span class="sxs-lookup"><span data-stu-id="2517c-899">This section is a deep-dive on framework internals and how MVC chooses an action to execute.</span></span> <span data-ttu-id="2517c-900">Typická aplikace nebude potřebovat vlastní `IActionConstraint`</span><span class="sxs-lookup"><span data-stu-id="2517c-900">A typical application won't need a custom `IActionConstraint`</span></span>

<span data-ttu-id="2517c-901">Pravděpodobně jste již použili `IActionConstraint`, i když nejste obeznámeni s rozhraním.</span><span class="sxs-lookup"><span data-stu-id="2517c-901">You have likely already used `IActionConstraint` even if you're not familiar with the interface.</span></span> <span data-ttu-id="2517c-902">Atribut `[HttpGet]` a podobné atributy `[Http-VERB]` implementují `IActionConstraint`, aby bylo možné omezit provádění metody akce.</span><span class="sxs-lookup"><span data-stu-id="2517c-902">The `[HttpGet]` Attribute and similar `[Http-VERB]` attributes implement `IActionConstraint` in order to limit the execution of an action method.</span></span>

```csharp
public class ProductsController : Controller
{
    [HttpGet]
    public IActionResult Edit() { }

    public IActionResult Edit(...) { }
}
```

<span data-ttu-id="2517c-903">Za předpokladu, že výchozí konvenční trasa, adresa URL `/Products/Edit` by vytvořila `{ controller = Products, action = Edit }`hodnoty, které odpovídají **oběma** akcím, které jsou zde uvedeny.</span><span class="sxs-lookup"><span data-stu-id="2517c-903">Assuming the default conventional route, the URL path `/Products/Edit` would produce the values `{ controller = Products, action = Edit }`, which would match **both** of the actions shown here.</span></span> <span data-ttu-id="2517c-904">V `IActionConstraint` terminologii bychom znamenali, že obě tyto akce se považují za kandidáty, protože obě odpovídají datům směrování.</span><span class="sxs-lookup"><span data-stu-id="2517c-904">In `IActionConstraint` terminology we would say that both of these actions are considered candidates - as they both match the route data.</span></span>

<span data-ttu-id="2517c-905">Pokud se `HttpGetAttribute` spustí, znamená to, že *Edit ()* je odpovídající pro *Get* a neshoduje se s žádným jiným příkazem http.</span><span class="sxs-lookup"><span data-stu-id="2517c-905">When the `HttpGetAttribute` executes, it will say that *Edit()* is a match for *GET* and isn't a match for any other HTTP verb.</span></span> <span data-ttu-id="2517c-906">Akce `Edit(...)` nemá definovány žádné omezení, a proto bude odpovídat jakémukoli příkazu HTTP.</span><span class="sxs-lookup"><span data-stu-id="2517c-906">The `Edit(...)` action doesn't have any constraints defined, and so will match any HTTP verb.</span></span> <span data-ttu-id="2517c-907">Proto předpokládáme, že `Edit(...)` odpovídá pouze `POST`.</span><span class="sxs-lookup"><span data-stu-id="2517c-907">So assuming a `POST` - only `Edit(...)` matches.</span></span> <span data-ttu-id="2517c-908">Ale u `GET` se obě akce stále shodují – ale akce s `IActionConstraint` je vždy považována za *lepší* , než je akce bez.</span><span class="sxs-lookup"><span data-stu-id="2517c-908">But, for a `GET` both actions can still match - however, an action with an `IActionConstraint` is always considered *better* than an action without.</span></span> <span data-ttu-id="2517c-909">Proto vzhledem k tomu, že `Edit()` má `[HttpGet]` považuje za konkrétnější, a vybere se, pokud se obě akce mohou shodovat.</span><span class="sxs-lookup"><span data-stu-id="2517c-909">So because `Edit()` has `[HttpGet]` it's considered more specific, and will be selected if both actions can match.</span></span>

<span data-ttu-id="2517c-910">Koncepční, `IActionConstraint` je forma *přetížení*, ale místo přetížení metod se stejným názvem, je přetížení mezi akcemi, které odpovídají stejné adrese URL.</span><span class="sxs-lookup"><span data-stu-id="2517c-910">Conceptually, `IActionConstraint` is a form of *overloading*, but instead of overloading methods with the same name, it's overloading between actions that match the same URL.</span></span> <span data-ttu-id="2517c-911">Směrování atributů používá taky `IActionConstraint` a může mít za následek, že se z různých řadičů považují i kandidáti.</span><span class="sxs-lookup"><span data-stu-id="2517c-911">Attribute routing also uses `IActionConstraint` and can result in actions from different controllers both being considered candidates.</span></span>

<a name="iactionconstraint-impl-ref-label"></a>

### <a name="implementing-iactionconstraint"></a><span data-ttu-id="2517c-912">Implementace IActionConstraint</span><span class="sxs-lookup"><span data-stu-id="2517c-912">Implementing IActionConstraint</span></span>

<span data-ttu-id="2517c-913">Nejjednodušší způsob, jak implementovat `IActionConstraint`, je vytvořit třídu odvozenou z `System.Attribute` a umístit ji na vaše akce a řadiče.</span><span class="sxs-lookup"><span data-stu-id="2517c-913">The simplest way to implement an `IActionConstraint` is to create a class derived from `System.Attribute` and place it on your actions and controllers.</span></span> <span data-ttu-id="2517c-914">MVC automaticky zjistí všechny `IActionConstraint`, které se použijí jako atributy.</span><span class="sxs-lookup"><span data-stu-id="2517c-914">MVC will automatically discover any `IActionConstraint` that are applied as attributes.</span></span> <span data-ttu-id="2517c-915">Pomocí modelu aplikace můžete použít omezení, což je pravděpodobně nejpružnější přístup, protože umožňuje Metaprogram způsob jejich použití.</span><span class="sxs-lookup"><span data-stu-id="2517c-915">You can use the application model to apply constraints, and this is probably the most flexible approach as it allows you to metaprogram how they're applied.</span></span>

<span data-ttu-id="2517c-916">V následujícím příkladu omezení zvolí akci na základě *kódu země* z údajů o trasách.</span><span class="sxs-lookup"><span data-stu-id="2517c-916">In the following example, a constraint chooses an action based on a *country code* from the route data.</span></span> <span data-ttu-id="2517c-917">[Úplná ukázka na GitHubu](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.ActionConstraintSample.Web/CountrySpecificAttribute.cs).</span><span class="sxs-lookup"><span data-stu-id="2517c-917">The [full sample on GitHub](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.ActionConstraintSample.Web/CountrySpecificAttribute.cs).</span></span>

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

<span data-ttu-id="2517c-918">Zodpovídáte za implementaci `Accept` metody a výběrem příkazu Order, aby se omezení spustilo.</span><span class="sxs-lookup"><span data-stu-id="2517c-918">You are responsible for implementing the `Accept` method and choosing an 'Order' for the constraint to execute.</span></span> <span data-ttu-id="2517c-919">V tomto případě metoda `Accept` vrátí `true` k tomu, že se jedná o shodu, když hodnota `country` trasy odpovídá hodnotě.</span><span class="sxs-lookup"><span data-stu-id="2517c-919">In this case, the `Accept` method returns `true` to denote the action is a match when the `country` route value matches.</span></span> <span data-ttu-id="2517c-920">To se liší od `RouteValueAttribute` v tom, že umožňuje přechod na akci bez atributu.</span><span class="sxs-lookup"><span data-stu-id="2517c-920">This is different from a `RouteValueAttribute` in that it allows fallback to a non-attributed action.</span></span> <span data-ttu-id="2517c-921">Ukázka ukazuje, že pokud definujete `en-US` akci, směrové číslo země, jako je `fr-FR`, se vrátí do obecnější kontroler, který nemá `[CountrySpecific(...)]` použití.</span><span class="sxs-lookup"><span data-stu-id="2517c-921">The sample shows that if you define an `en-US` action then a country code like `fr-FR` will fall back to a more generic controller that doesn't have `[CountrySpecific(...)]` applied.</span></span>

<span data-ttu-id="2517c-922">Vlastnost `Order` určuje, ve které *fázi* je omezení součástí.</span><span class="sxs-lookup"><span data-stu-id="2517c-922">The `Order` property decides which *stage* the constraint is part of.</span></span> <span data-ttu-id="2517c-923">Omezení akcí se spouští ve skupinách na základě `Order`.</span><span class="sxs-lookup"><span data-stu-id="2517c-923">Action constraints run in groups based on the `Order`.</span></span> <span data-ttu-id="2517c-924">Například všechny atributy, které poskytuje rozhraní HTTP, používají stejnou `Order` hodnotu tak, aby běžely ve stejné fázi.</span><span class="sxs-lookup"><span data-stu-id="2517c-924">For example, all of the framework provided HTTP method attributes use the same `Order` value so that they run in the same stage.</span></span> <span data-ttu-id="2517c-925">Můžete mít tolik fází, kolik potřebujete k implementaci požadovaných zásad.</span><span class="sxs-lookup"><span data-stu-id="2517c-925">You can have as many stages as you need to implement your desired policies.</span></span>

> [!TIP]
> <span data-ttu-id="2517c-926">Chcete-li se rozhodnout o hodnotě pro `Order` zamyslete nad tím, zda by mělo být vaše omezení použito před metodami HTTP.</span><span class="sxs-lookup"><span data-stu-id="2517c-926">To decide on a value for `Order` think about whether or not your constraint should be applied before HTTP methods.</span></span> <span data-ttu-id="2517c-927">Nižší čísla se spouštějí jako první.</span><span class="sxs-lookup"><span data-stu-id="2517c-927">Lower numbers run first.</span></span>

::: moniker-end
