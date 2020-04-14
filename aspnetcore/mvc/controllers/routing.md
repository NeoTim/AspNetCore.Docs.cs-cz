---
title: Směrování k akcím řadiče v ASP.NET jádra
author: rick-anderson
description: Zjistěte, jak ASP.NET Core MVC používá middlewar směrování k přizpůsobení adres URL příchozích požadavků a jejich mapování na akce.
ms.author: riande
ms.date: 3/25/2020
uid: mvc/controllers/routing
ms.openlocfilehash: 9f7a26a482cb115697a0a3d7439c14a062677c92
ms.sourcegitcommit: 5af16166977da598953f82da3ed3b7712d38f6cb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81277129"
---
# <a name="routing-to-controller-actions-in-aspnet-core"></a><span data-ttu-id="77c30-103">Směrování k akcím řadiče v ASP.NET jádra</span><span class="sxs-lookup"><span data-stu-id="77c30-103">Routing to controller actions in ASP.NET Core</span></span>

<span data-ttu-id="77c30-104">[Ryan Nowak](https://github.com/rynowak), [Kirk Larkin](https://twitter.com/serpent5), a [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="77c30-104">By [Ryan Nowak](https://github.com/rynowak), [Kirk Larkin](https://twitter.com/serpent5), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="77c30-105">ASP.NET řadiče Core používají middleware směrování [tak,](xref:fundamentals/middleware/index) aby odpovídal adresám URL příchozích požadavků a mapoval je na [akce](#action).</span><span class="sxs-lookup"><span data-stu-id="77c30-105">ASP.NET Core controllers use the Routing [middleware](xref:fundamentals/middleware/index) to match the URLs of incoming requests and map them to [actions](#action).</span></span>  <span data-ttu-id="77c30-106">Šablony tras:</span><span class="sxs-lookup"><span data-stu-id="77c30-106">Routes templates:</span></span>

* <span data-ttu-id="77c30-107">Jsou definovány v spouštěcím kódu nebo atributech.</span><span class="sxs-lookup"><span data-stu-id="77c30-107">Are defined in startup code or attributes.</span></span>
* <span data-ttu-id="77c30-108">Popište, jak jsou cesty adres URL spárovány s [akcemi](#action).</span><span class="sxs-lookup"><span data-stu-id="77c30-108">Describe how URL paths are matched to [actions](#action).</span></span>
* <span data-ttu-id="77c30-109">Slouží ke generování adres URL pro odkazy.</span><span class="sxs-lookup"><span data-stu-id="77c30-109">Are used to generate URLs for links.</span></span> <span data-ttu-id="77c30-110">Generované odkazy jsou obvykle vráceny v odpovědích.</span><span class="sxs-lookup"><span data-stu-id="77c30-110">The generated links are typically returned in responses.</span></span>

<span data-ttu-id="77c30-111">Akce jsou buď [konvenčně směrované](#cr) nebo [atributsměrované](#ar).</span><span class="sxs-lookup"><span data-stu-id="77c30-111">Actions are either [conventionally-routed](#cr) or [attribute-routed](#ar).</span></span> <span data-ttu-id="77c30-112">Umístění trasy na řadič nebo [akci](#action) způsobí, že je směrována do atributu.</span><span class="sxs-lookup"><span data-stu-id="77c30-112">Placing a route on the controller or [action](#action) makes it attribute-routed.</span></span> <span data-ttu-id="77c30-113">Další informace naleznete [v tématu Smíšené směrování.](#routing-mixed-ref-label)</span><span class="sxs-lookup"><span data-stu-id="77c30-113">See [Mixed routing](#routing-mixed-ref-label) for more information.</span></span>

<span data-ttu-id="77c30-114">Tento dokument:</span><span class="sxs-lookup"><span data-stu-id="77c30-114">This document:</span></span>

* <span data-ttu-id="77c30-115">Vysvětluje interakce mezi MVC a směrování:</span><span class="sxs-lookup"><span data-stu-id="77c30-115">Explains the interactions between MVC and routing:</span></span>
  * <span data-ttu-id="77c30-116">Jak typické aplikace MVC využívají funkce směrování.</span><span class="sxs-lookup"><span data-stu-id="77c30-116">How typical MVC apps make use of routing features.</span></span>
  * <span data-ttu-id="77c30-117">Zahrnuje obojí:</span><span class="sxs-lookup"><span data-stu-id="77c30-117">Covers both:</span></span>
    * <span data-ttu-id="77c30-118">[Konvenčně směrování](#cr) se obvykle používá s řadiči a zobrazeními.</span><span class="sxs-lookup"><span data-stu-id="77c30-118">[Conventionally routing](#cr) typically used with controllers and views.</span></span>
    * <span data-ttu-id="77c30-119">*Směrování atributů* používané s rest API.</span><span class="sxs-lookup"><span data-stu-id="77c30-119">*Attribute routing* used with REST APIs.</span></span> <span data-ttu-id="77c30-120">Pokud máte primárně zájem o směrování pro REST API, přejděte na [oddíl Směrování atributů pro rest API.](#ar)</span><span class="sxs-lookup"><span data-stu-id="77c30-120">If you're primarily interested in routing for REST APIs, jump to the [Attribute routing for REST APIs](#ar) section.</span></span>
  * <span data-ttu-id="77c30-121">Rozšířené podrobnosti o směrování naleznete v [tématu Směrování.](xref:fundamentals/routing)</span><span class="sxs-lookup"><span data-stu-id="77c30-121">See [Routing](xref:fundamentals/routing) for advanced routing details.</span></span>
* <span data-ttu-id="77c30-122">Odkazuje na výchozí systém směrování přidaný v ASP.NET core 3.0, nazývané směrování koncových bodů.</span><span class="sxs-lookup"><span data-stu-id="77c30-122">Refers to the default routing system added in ASP.NET Core 3.0, called endpoint routing.</span></span> <span data-ttu-id="77c30-123">Pro účely kompatibility je možné používat řadiče s předchozí verzí směrování.</span><span class="sxs-lookup"><span data-stu-id="77c30-123">It's possible to use controllers with the previous version of routing for compatibility purposes.</span></span> <span data-ttu-id="77c30-124">Pokyny naleznete v [průvodci migrací 2.2-3.0.](xref:migration/22-to-30)</span><span class="sxs-lookup"><span data-stu-id="77c30-124">See the [2.2-3.0 migration guide](xref:migration/22-to-30) for instructions.</span></span> <span data-ttu-id="77c30-125">Referenční materiál staršího systému směrování naleznete ve [verzi 2.2 tohoto dokumentu.](xref:mvc/controllers/routing?view=aspnetcore-2.2)</span><span class="sxs-lookup"><span data-stu-id="77c30-125">Refer to the [2.2 version of this document](xref:mvc/controllers/routing?view=aspnetcore-2.2) for reference material on the legacy routing system.</span></span>

<a name="cr"></a>

## <a name="set-up-conventional-route"></a><span data-ttu-id="77c30-126">Nastavit konvenční trasu</span><span class="sxs-lookup"><span data-stu-id="77c30-126">Set up conventional route</span></span>

<span data-ttu-id="77c30-127">`Startup.Configure`obvykle má kód podobný následujícímu při použití [konvenčního směrování](#crd):</span><span class="sxs-lookup"><span data-stu-id="77c30-127">`Startup.Configure` typically has code similar to the following when using [conventional routing](#crd):</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupDefaultMVC.cs?name=snippet)]

<span data-ttu-id="77c30-128">Uvnitř volání <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*>do <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> , se používá k vytvoření jedné trasy.</span><span class="sxs-lookup"><span data-stu-id="77c30-128">Inside the call to <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*>, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> is used to create a single route.</span></span> <span data-ttu-id="77c30-129">Jedna trasa `default` se nazývá trasa.</span><span class="sxs-lookup"><span data-stu-id="77c30-129">The single route is named `default` route.</span></span> <span data-ttu-id="77c30-130">Většina aplikací s ovladači a zobrazeními `default` používá šablonu trasy podobnou trase.</span><span class="sxs-lookup"><span data-stu-id="77c30-130">Most apps with controllers and views use a route template similar to the `default` route.</span></span> <span data-ttu-id="77c30-131">REST API by měl používat [směrování atributů](#ar).</span><span class="sxs-lookup"><span data-stu-id="77c30-131">REST APIs should use [attribute routing](#ar).</span></span>

<span data-ttu-id="77c30-132">Šablona `"{controller=Home}/{action=Index}/{id?}"`trasy :</span><span class="sxs-lookup"><span data-stu-id="77c30-132">The route template `"{controller=Home}/{action=Index}/{id?}"`:</span></span>

* <span data-ttu-id="77c30-133">Odpovídá cestě URL, jako je`/Products/Details/5`</span><span class="sxs-lookup"><span data-stu-id="77c30-133">Matches a URL path like `/Products/Details/5`</span></span>
* <span data-ttu-id="77c30-134">Extrahuje hodnoty `{ controller = Products, action = Details, id = 5 }` trasy tokenizací cesty.</span><span class="sxs-lookup"><span data-stu-id="77c30-134">Extracts the route values `{ controller = Products, action = Details, id = 5 }` by tokenizing the path.</span></span> <span data-ttu-id="77c30-135">Extrakce hodnot trasy má za následek shodu, pokud má aplikace pojmenovanou `ProductsController` kontroleru a `Details` akci:</span><span class="sxs-lookup"><span data-stu-id="77c30-135">The extraction of route values results in a match if the app has a controller named `ProductsController` and a `Details` action:</span></span>

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippetA)]

  [!INCLUDE[](~/includes/MyDisplayRouteInfo.md)]

* <span data-ttu-id="77c30-136">`/Products/Details/5`model váže hodnotu `id = 5` pro `id` nastavení `5`parametru na .</span><span class="sxs-lookup"><span data-stu-id="77c30-136">`/Products/Details/5` model binds the value of `id = 5` to set the `id` parameter to `5`.</span></span> <span data-ttu-id="77c30-137">Další podrobnosti najdete v tématu [Vazby modelu.](xref:mvc/models/model-binding)</span><span class="sxs-lookup"><span data-stu-id="77c30-137">See [Model Binding](xref:mvc/models/model-binding) for more details.</span></span>
* <span data-ttu-id="77c30-138">`{controller=Home}`definuje `Home` jako výchozí `controller`.</span><span class="sxs-lookup"><span data-stu-id="77c30-138">`{controller=Home}` defines `Home` as the default `controller`.</span></span>
* <span data-ttu-id="77c30-139">`{action=Index}`definuje `Index` jako výchozí `action`.</span><span class="sxs-lookup"><span data-stu-id="77c30-139">`{action=Index}` defines `Index` as the default `action`.</span></span>
*  <span data-ttu-id="77c30-140">Znak `?` v `{id?}` definuje `id` jako volitelné.</span><span class="sxs-lookup"><span data-stu-id="77c30-140">The `?` character in `{id?}` defines `id` as optional.</span></span>
  * <span data-ttu-id="77c30-141">Výchozí a volitelné parametry trasy nemusí být v cestě adresy URL pro shodu k dispozici.</span><span class="sxs-lookup"><span data-stu-id="77c30-141">Default and optional route parameters don't need to be present in the URL path for a match.</span></span> <span data-ttu-id="77c30-142">Podrobný popis syntaxe šablony trasy najdete v tématu [Odkaz na šablonu](xref:fundamentals/routing#route-template-reference) trasy.</span><span class="sxs-lookup"><span data-stu-id="77c30-142">See [Route Template Reference](xref:fundamentals/routing#route-template-reference) for a detailed description of route template syntax.</span></span>
* <span data-ttu-id="77c30-143">Odpovídá cestě `/`url .</span><span class="sxs-lookup"><span data-stu-id="77c30-143">Matches the URL path `/`.</span></span>
* <span data-ttu-id="77c30-144">Vytvoří hodnoty `{ controller = Home, action = Index }`postupu .</span><span class="sxs-lookup"><span data-stu-id="77c30-144">Produces the route values `{ controller = Home, action = Index }`.</span></span>

<span data-ttu-id="77c30-145">Hodnoty pro `controller` `action` a využít výchozí hodnoty.</span><span class="sxs-lookup"><span data-stu-id="77c30-145">The values for `controller` and `action` make use of the default values.</span></span> <span data-ttu-id="77c30-146">`id`nevytváří hodnotu, protože v cestě URL není žádný odpovídající segment.</span><span class="sxs-lookup"><span data-stu-id="77c30-146">`id` doesn't produce a value since there's no corresponding segment in the URL path.</span></span> <span data-ttu-id="77c30-147">`/`pouze odpovídá, pokud `HomeController` existuje `Index` a akce:</span><span class="sxs-lookup"><span data-stu-id="77c30-147">`/` only matches if there exists a `HomeController` and `Index` action:</span></span>

```csharp
public class HomeController : Controller
{
  public IActionResult Index() { ... }
}
```

<span data-ttu-id="77c30-148">Pomocí předchozí definice řadiče a `HomeController.Index` šablony trasy je akce spuštěna pro následující cesty adres URL:</span><span class="sxs-lookup"><span data-stu-id="77c30-148">Using the preceding controller definition and route template, the `HomeController.Index` action is run for the following URL paths:</span></span>

* `/Home/Index/17`
* `/Home/Index`
* `/Home`
* `/`

<span data-ttu-id="77c30-149">Cesta URL `/` používá výchozí `Home` řadiče `Index` a akci šablony trasy.</span><span class="sxs-lookup"><span data-stu-id="77c30-149">The URL path `/` uses the route template default `Home` controllers and `Index` action.</span></span> <span data-ttu-id="77c30-150">Cesta URL `/Home` používá výchozí `Index` akci šablony trasy.</span><span class="sxs-lookup"><span data-stu-id="77c30-150">The URL path `/Home` uses the route template default `Index` action.</span></span>

<span data-ttu-id="77c30-151">Komfortní metoda <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute*>:</span><span class="sxs-lookup"><span data-stu-id="77c30-151">The convenience method <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute*>:</span></span>

```csharp
endpoints.MapDefaultControllerRoute();
```

<span data-ttu-id="77c30-152">Nahradí:</span><span class="sxs-lookup"><span data-stu-id="77c30-152">Replaces:</span></span>

```csharp
endpoints.MapControllerRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

> [!IMPORTANT]
> <span data-ttu-id="77c30-153">Směrování je konfigurováno pomocí middlewaru <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting*> a. <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*></span><span class="sxs-lookup"><span data-stu-id="77c30-153">Routing is configured using the <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting*> and <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> middleware.</span></span> <span data-ttu-id="77c30-154">Použití regulátorů:</span><span class="sxs-lookup"><span data-stu-id="77c30-154">To use controllers:</span></span>
>
> * <span data-ttu-id="77c30-155">Volání <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*> `UseEndpoints` uvnitř mapovat [atribut směrované](#ar) řadiče.</span><span class="sxs-lookup"><span data-stu-id="77c30-155">Call <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*> inside `UseEndpoints` to map [attribute routed](#ar) controllers.</span></span>
> * <span data-ttu-id="77c30-156">Volání <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*>nebo , mapovat [konvenčně směrované](#cr) řadiče.</span><span class="sxs-lookup"><span data-stu-id="77c30-156">Call <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> or <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*>, to map [conventionally routed](#cr) controllers.</span></span>

<a name="routing-conventional-ref-label"></a>
<a name="crd"></a>

## <a name="conventional-routing"></a><span data-ttu-id="77c30-157">Konvenční směrování</span><span class="sxs-lookup"><span data-stu-id="77c30-157">Conventional routing</span></span>

<span data-ttu-id="77c30-158">Konvenční směrování se používá s ovladači a zobrazeními.</span><span class="sxs-lookup"><span data-stu-id="77c30-158">Conventional routing is used with controllers and views.</span></span> <span data-ttu-id="77c30-159">Trasa: `default`</span><span class="sxs-lookup"><span data-stu-id="77c30-159">The `default` route:</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupDefaultMVC.cs?name=snippet2)]

<span data-ttu-id="77c30-160">je příkladem *konvenčního směrování*.</span><span class="sxs-lookup"><span data-stu-id="77c30-160">is an example of a *conventional routing*.</span></span> <span data-ttu-id="77c30-161">Nazývá se *konvenční směrování,* protože vytváří *konvenci* pro cesty adres URL:</span><span class="sxs-lookup"><span data-stu-id="77c30-161">It's called *conventional routing* because it establishes a *convention* for URL paths:</span></span>

* <span data-ttu-id="77c30-162">První segment cesty `{controller=Home}`, mapuje na název řadiče.</span><span class="sxs-lookup"><span data-stu-id="77c30-162">The first path segment, `{controller=Home}`, maps to the controller name.</span></span>
* <span data-ttu-id="77c30-163">Druhý segment `{action=Index}`, mapuje na název [akce.](#action)</span><span class="sxs-lookup"><span data-stu-id="77c30-163">The second segment, `{action=Index}`, maps to the [action](#action) name.</span></span>
* <span data-ttu-id="77c30-164">Třetí segment `{id?}` se používá pro `id`volitelné .</span><span class="sxs-lookup"><span data-stu-id="77c30-164">The third segment, `{id?}` is used for an optional `id`.</span></span> <span data-ttu-id="77c30-165">In `?` `{id?}` je volitelný.</span><span class="sxs-lookup"><span data-stu-id="77c30-165">The `?` in `{id?}` makes it optional.</span></span> <span data-ttu-id="77c30-166">`id`se používá k mapování na entitu modelu.</span><span class="sxs-lookup"><span data-stu-id="77c30-166">`id` is used to map to a model entity.</span></span>

<span data-ttu-id="77c30-167">Pomocí `default` této trasy cesta url:</span><span class="sxs-lookup"><span data-stu-id="77c30-167">Using this `default` route, the URL path:</span></span>

* <span data-ttu-id="77c30-168">`/Products/List`mapy k `ProductsController.List` akci.</span><span class="sxs-lookup"><span data-stu-id="77c30-168">`/Products/List` maps to the `ProductsController.List` action.</span></span>
* <span data-ttu-id="77c30-169">`/Blog/Article/17`mapuje `BlogController.Article` a obvykle model `id` váže parametr na 17.</span><span class="sxs-lookup"><span data-stu-id="77c30-169">`/Blog/Article/17` maps to `BlogController.Article` and typically model binds the `id` parameter to 17.</span></span>

<span data-ttu-id="77c30-170">Toto mapování:</span><span class="sxs-lookup"><span data-stu-id="77c30-170">This mapping:</span></span>

* <span data-ttu-id="77c30-171">Je založen pouze na **názvech**řadiče a [akcí](#action) .</span><span class="sxs-lookup"><span data-stu-id="77c30-171">Is based on the controller and [action](#action) names **only**.</span></span>
* <span data-ttu-id="77c30-172">Není založen na oborech názvů, umístění zdrojových souborů nebo parametrech metody.</span><span class="sxs-lookup"><span data-stu-id="77c30-172">Isn't based on namespaces, source file locations, or method parameters.</span></span>

<span data-ttu-id="77c30-173">Použití konvenčního směrování s výchozí trasou umožňuje vytvoření aplikace bez nutnosti přijít s novým vzorem adresy URL pro každou akci.</span><span class="sxs-lookup"><span data-stu-id="77c30-173">Using conventional routing with the default route allows creating the app without having to come up with a new URL pattern for each action.</span></span> <span data-ttu-id="77c30-174">U aplikace s akcemi stylu [CRUD,](https://wikipedia.org/wiki/Create,_read,_update_and_delete) které mají konzistenci pro adresy URL napříč řadiči:</span><span class="sxs-lookup"><span data-stu-id="77c30-174">For an app with [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) style actions, having consistency for the URLs across controllers:</span></span>

* <span data-ttu-id="77c30-175">Pomáhá zjednodušit kód.</span><span class="sxs-lookup"><span data-stu-id="77c30-175">Helps simplify the code.</span></span>
* <span data-ttu-id="77c30-176">Díky tomu, že je uI předvídatelnější.</span><span class="sxs-lookup"><span data-stu-id="77c30-176">Makes the UI more predictable.</span></span>

> [!WARNING]
> <span data-ttu-id="77c30-177">V `id` předchozím kódu je definován jako volitelné šablony trasy.</span><span class="sxs-lookup"><span data-stu-id="77c30-177">The `id` in the preceding code is defined as optional by the route template.</span></span> <span data-ttu-id="77c30-178">Akce lze provést bez volitelného ID k dispozici jako součást adresy URL.</span><span class="sxs-lookup"><span data-stu-id="77c30-178">Actions can execute without the optional ID provided as part of the URL.</span></span> <span data-ttu-id="77c30-179">Obecně platí, že když`id` je vynechán z adresy URL:</span><span class="sxs-lookup"><span data-stu-id="77c30-179">Generally, when`id` is omitted from the URL:</span></span>
>
> * <span data-ttu-id="77c30-180">`id`je nastavena na `0` podle vazby modelu.</span><span class="sxs-lookup"><span data-stu-id="77c30-180">`id` is set to `0` by model binding.</span></span>
> * <span data-ttu-id="77c30-181">V odpovídající databázi nebyla nalezena žádná entita `id == 0`.</span><span class="sxs-lookup"><span data-stu-id="77c30-181">No entity is found in the database matching `id == 0`.</span></span>
>
> <span data-ttu-id="77c30-182">[Směrování atributů](#ar) poskytuje jemně odstupňovaný ovládací prvek, aby id požadované pro některé akce a ne pro ostatní.</span><span class="sxs-lookup"><span data-stu-id="77c30-182">[Attribute routing](#ar) provides fine-grained control to make the ID required for some actions and not for others.</span></span> <span data-ttu-id="77c30-183">Podle konvence dokumentace obsahuje volitelné `id` parametry, jako když je pravděpodobné, že se zobrazí ve správném použití.</span><span class="sxs-lookup"><span data-stu-id="77c30-183">By convention, the documentation includes optional parameters like `id` when they're likely to appear in correct usage.</span></span>

<span data-ttu-id="77c30-184">Většina aplikací by měla zvolit základní a popisné schéma směrování, aby byly adresy URL čitelné a smysluplné.</span><span class="sxs-lookup"><span data-stu-id="77c30-184">Most apps should choose a basic and descriptive routing scheme so that URLs are readable and meaningful.</span></span> <span data-ttu-id="77c30-185">Výchozí konvenční `{controller=Home}/{action=Index}/{id?}`trasa :</span><span class="sxs-lookup"><span data-stu-id="77c30-185">The default conventional route `{controller=Home}/{action=Index}/{id?}`:</span></span>

* <span data-ttu-id="77c30-186">Podporuje základní a popisné schéma směrování.</span><span class="sxs-lookup"><span data-stu-id="77c30-186">Supports a basic and descriptive routing scheme.</span></span>
* <span data-ttu-id="77c30-187">Je užitečným výchozím bodem pro aplikace založené na uznatého.</span><span class="sxs-lookup"><span data-stu-id="77c30-187">Is a useful starting point for UI-based apps.</span></span>
* <span data-ttu-id="77c30-188">Je jediná šablona trasy potřebná pro mnoho webových aplikací uživatelského uživatelského nastavení.</span><span class="sxs-lookup"><span data-stu-id="77c30-188">Is the only route template needed for many web UI apps.</span></span> <span data-ttu-id="77c30-189">U větších webových aplikací uživatelského uživatelského nastavení, další trasa pomocí [oblasti,](#areas) pokud často vše, co je potřeba.</span><span class="sxs-lookup"><span data-stu-id="77c30-189">For larger web UI apps, another route using [Areas](#areas) if frequently all that's needed.</span></span>

<span data-ttu-id="77c30-190"><xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*>a: <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*></span><span class="sxs-lookup"><span data-stu-id="77c30-190"><xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> and <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> :</span></span>

* <span data-ttu-id="77c30-191">Automaticky přiřaďte hodnotu **objednávky** jejich koncovým bodům na základě pořadí, ve které jsou vyvolány.</span><span class="sxs-lookup"><span data-stu-id="77c30-191">Automatically assign an **order** value to their endpoints based on the order they are invoked.</span></span>

<span data-ttu-id="77c30-192">Směrování koncového bodu v ASP.NET jádrem 3.0 a novějším:</span><span class="sxs-lookup"><span data-stu-id="77c30-192">Endpoint routing in ASP.NET Core 3.0 and later:</span></span>

* <span data-ttu-id="77c30-193">Nemá pojem trasy.</span><span class="sxs-lookup"><span data-stu-id="77c30-193">Doesn't have a concept of routes.</span></span>
* <span data-ttu-id="77c30-194">Neposkytuje záruky řazení pro provádění rozšiřitelnosti, všechny koncové body jsou zpracovány najednou.</span><span class="sxs-lookup"><span data-stu-id="77c30-194">Doesn't provide ordering guarantees for the execution of extensibility,  all endpoints are processed at once.</span></span>

<span data-ttu-id="77c30-195">Povolte [protokolování,](xref:fundamentals/logging/index) abyste viděli, jak integrované <xref:Microsoft.AspNetCore.Routing.Route>implementace směrování, například , shodu jsou požadavky na shodu.</span><span class="sxs-lookup"><span data-stu-id="77c30-195">Enable [Logging](xref:fundamentals/logging/index) to see how the built-in routing implementations, such as <xref:Microsoft.AspNetCore.Routing.Route>, match requests.</span></span>

<span data-ttu-id="77c30-196">[Směrování atributů](#ar) je vysvětleno dále v tomto dokumentu.</span><span class="sxs-lookup"><span data-stu-id="77c30-196">[Attribute routing](#ar) is explained later in this document.</span></span>

<a name="mr"></a>

### <a name="multiple-conventional-routes"></a><span data-ttu-id="77c30-197">Více konvenčních tras</span><span class="sxs-lookup"><span data-stu-id="77c30-197">Multiple conventional routes</span></span>

<span data-ttu-id="77c30-198">Více [konvenčních tras](#cr) lze `UseEndpoints` přidat uvnitř <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> přidáním další volání a <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*>.</span><span class="sxs-lookup"><span data-stu-id="77c30-198">Multiple [conventional routes](#cr) can be added inside `UseEndpoints` by adding more calls to <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> and <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*>.</span></span> <span data-ttu-id="77c30-199">To umožňuje definování více konvencí nebo přidání konvenčních tras, které jsou vyhrazeny pro konkrétní [akci](#action), například:</span><span class="sxs-lookup"><span data-stu-id="77c30-199">Doing so allows defining multiple conventions, or to adding conventional routes that are dedicated to a specific [action](#action), such as:</span></span>

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

<a name="dcr"></a>

<span data-ttu-id="77c30-200">Trasa `blog` v předchozím kódu je **vyhrazená konvenční trasa**.</span><span class="sxs-lookup"><span data-stu-id="77c30-200">The `blog` route in the preceding code is a **dedicated conventional route**.</span></span> <span data-ttu-id="77c30-201">Říká se tomu vyhrazená konvenční trasa, protože:</span><span class="sxs-lookup"><span data-stu-id="77c30-201">It's called a dedicated conventional route because:</span></span>

* <span data-ttu-id="77c30-202">Používá [konvenční směrování](#cr).</span><span class="sxs-lookup"><span data-stu-id="77c30-202">It uses [conventional routing](#cr).</span></span>
* <span data-ttu-id="77c30-203">Je věnován konkrétní [akci](#action).</span><span class="sxs-lookup"><span data-stu-id="77c30-203">It's dedicated to a specific [action](#action).</span></span>

<span data-ttu-id="77c30-204">Protože `controller` `action` a nezobrazují se v `"blog/{*article}"` šabloně trasy jako parametry:</span><span class="sxs-lookup"><span data-stu-id="77c30-204">Because `controller` and `action` don't appear in the route template `"blog/{*article}"` as parameters:</span></span>

* <span data-ttu-id="77c30-205">Mohou mít pouze výchozí `{ controller = "Blog", action = "Article" }`hodnoty .</span><span class="sxs-lookup"><span data-stu-id="77c30-205">They can only have the default values `{ controller = "Blog", action = "Article" }`.</span></span>
* <span data-ttu-id="77c30-206">Tato trasa se `BlogController.Article`vždy mapuje na akci .</span><span class="sxs-lookup"><span data-stu-id="77c30-206">This route always maps to the action `BlogController.Article`.</span></span>

<span data-ttu-id="77c30-207">`/Blog`, `/Blog/Article`a `/Blog/{any-string}` jsou jedinými cestami url, které odpovídají trase blogu.</span><span class="sxs-lookup"><span data-stu-id="77c30-207">`/Blog`, `/Blog/Article`, and `/Blog/{any-string}` are the only URL paths that match the blog route.</span></span>

<span data-ttu-id="77c30-208">Předchozí příklad:</span><span class="sxs-lookup"><span data-stu-id="77c30-208">The preceding example:</span></span>

* <span data-ttu-id="77c30-209">`blog`trasa má vyšší prioritu `default` pro shody než trasa, protože je přidána jako první.</span><span class="sxs-lookup"><span data-stu-id="77c30-209">`blog` route has a higher priority for matches than the `default` route because it is added first.</span></span>
* <span data-ttu-id="77c30-210">Je a příkladem směrování stylu [slimáka,](https://developer.mozilla.org/docs/Glossary/Slug) kde je typické mít název článku jako součást adresy URL.</span><span class="sxs-lookup"><span data-stu-id="77c30-210">Is and example of [Slug](https://developer.mozilla.org/docs/Glossary/Slug) style routing where it's typical to have an article name as part of the URL.</span></span>

> [!WARNING]
> <span data-ttu-id="77c30-211">V ASP.NET jádrem 3.0 a novějším směrování ne:</span><span class="sxs-lookup"><span data-stu-id="77c30-211">In ASP.NET Core 3.0 and later, routing doesn't:</span></span>
> * <span data-ttu-id="77c30-212">Definujte koncept nazývaný *trasa*.</span><span class="sxs-lookup"><span data-stu-id="77c30-212">Define a concept called a *route*.</span></span> <span data-ttu-id="77c30-213">`UseRouting`přidá odpovídající trasu do kanálu middlewaru.</span><span class="sxs-lookup"><span data-stu-id="77c30-213">`UseRouting` adds route matching to the middleware pipeline.</span></span> <span data-ttu-id="77c30-214">Middleware `UseRouting` se podívá na sadu koncových bodů definovaných v aplikaci a vybere nejlepší shodu koncového bodu na základě požadavku.</span><span class="sxs-lookup"><span data-stu-id="77c30-214">The `UseRouting` middleware looks at the set of endpoints defined in the app, and selects the best endpoint match based on the request.</span></span>
> * <span data-ttu-id="77c30-215">Poskytnout záruky o pořadí provádění rozšiřitelnosti jako <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> nebo <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint>.</span><span class="sxs-lookup"><span data-stu-id="77c30-215">Provide guarantees about the execution order of extensibility like <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> or <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint>.</span></span>
>
><span data-ttu-id="77c30-216">Referenční materiál na směrování naleznete v [tématu Směrování.](xref:fundamentals/routing)</span><span class="sxs-lookup"><span data-stu-id="77c30-216">See [Routing](xref:fundamentals/routing) for reference material on routing.</span></span>

<a name="cro"></a>

### <a name="conventional-routing-order"></a><span data-ttu-id="77c30-217">Konvenční pořadí směrování</span><span class="sxs-lookup"><span data-stu-id="77c30-217">Conventional routing order</span></span>

<span data-ttu-id="77c30-218">Konvenční směrování odpovídá pouze kombinaci akce a kontroleru, které jsou definovány aplikací.</span><span class="sxs-lookup"><span data-stu-id="77c30-218">Conventional routing only matches a combination of action and controller that are defined by the app.</span></span> <span data-ttu-id="77c30-219">Cílem je zjednodušit případy, kdy se konvenční trasy překrývají.</span><span class="sxs-lookup"><span data-stu-id="77c30-219">This is intended to simplify cases where conventional routes overlap.</span></span>
<span data-ttu-id="77c30-220">Přidání tras <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*>pomocí <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute*>aplikace <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> , a automaticky přiřadit hodnotu objednávky jejich koncovým bodům na základě pořadí, ve které jsou vyvolány.</span><span class="sxs-lookup"><span data-stu-id="77c30-220">Adding routes using <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*>, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute*>, and <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> automatically assign an order value to their endpoints based on the order they are invoked.</span></span> <span data-ttu-id="77c30-221">Shody z trasy, která se zobrazí dříve, mají vyšší prioritu.</span><span class="sxs-lookup"><span data-stu-id="77c30-221">Matches from a route that appears earlier have a higher priority.</span></span> <span data-ttu-id="77c30-222">Konvenční směrování je závislé na objednávce.</span><span class="sxs-lookup"><span data-stu-id="77c30-222">Conventional routing is order-dependent.</span></span> <span data-ttu-id="77c30-223">Obecně platí, že trasy s oblastmi by měly být umístěny dříve, protože jsou konkrétnější než trasy bez oblasti.</span><span class="sxs-lookup"><span data-stu-id="77c30-223">In general, routes with areas should be placed earlier as they're more specific than routes without an area.</span></span> <span data-ttu-id="77c30-224">[Vyhrazené konvenční trasy](#dcr) s catch všechny `{*article}` parametry trasy, jako může trasa příliš [chamtivý](xref:fundamentals/routing#greedy), což znamená, že odpovídá adresy URL, které jste chtěli, aby odpovídaly jiné trasy.</span><span class="sxs-lookup"><span data-stu-id="77c30-224">[Dedicated conventional routes](#dcr) with catch all route parameters like `{*article}` can make a route too [greedy](xref:fundamentals/routing#greedy), meaning that it matches URLs that you intended to be matched by other routes.</span></span> <span data-ttu-id="77c30-225">Chcete-li zabránit chamtivým shodám, vložte chamtivé trasy později do tabulky tras.</span><span class="sxs-lookup"><span data-stu-id="77c30-225">Put the greedy routes later in the route table to prevent greedy matches.</span></span>

<a name="best"></a>

### <a name="resolving-ambiguous-actions"></a><span data-ttu-id="77c30-226">Řešení nejednoznačných akcí</span><span class="sxs-lookup"><span data-stu-id="77c30-226">Resolving ambiguous actions</span></span>

<span data-ttu-id="77c30-227">Pokud se dva koncové body shodují prostřednictvím směrování, musí směrování provést jednu z následujících akcí:</span><span class="sxs-lookup"><span data-stu-id="77c30-227">When two endpoints match through routing, routing must do one of the following:</span></span>

* <span data-ttu-id="77c30-228">Vyberte si nejlepšího kandidáta.</span><span class="sxs-lookup"><span data-stu-id="77c30-228">Choose the best candidate.</span></span>
* <span data-ttu-id="77c30-229">Vyvolat výjimku.</span><span class="sxs-lookup"><span data-stu-id="77c30-229">Throw an exception.</span></span>

<span data-ttu-id="77c30-230">Příklad:</span><span class="sxs-lookup"><span data-stu-id="77c30-230">For example:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet9)]

<span data-ttu-id="77c30-231">Předchozí řadič definuje dvě akce, které odpovídají:</span><span class="sxs-lookup"><span data-stu-id="77c30-231">The preceding controller defines two actions that match:</span></span>

* <span data-ttu-id="77c30-232">Cesta url`/Products33/Edit/17`</span><span class="sxs-lookup"><span data-stu-id="77c30-232">The URL path `/Products33/Edit/17`</span></span>
* <span data-ttu-id="77c30-233">Údaje `{ controller = Products33, action = Edit, id = 17 }`o trase .</span><span class="sxs-lookup"><span data-stu-id="77c30-233">Route data `{ controller = Products33, action = Edit, id = 17 }`.</span></span>

<span data-ttu-id="77c30-234">Toto je typický vzor pro řadiče MVC:</span><span class="sxs-lookup"><span data-stu-id="77c30-234">This is a typical pattern for MVC controllers:</span></span>

* <span data-ttu-id="77c30-235">`Edit(int)`zobrazí formulář pro úpravu produktu.</span><span class="sxs-lookup"><span data-stu-id="77c30-235">`Edit(int)` displays a form to edit a product.</span></span>
* <span data-ttu-id="77c30-236">`Edit(int, Product)`zpracuje zaúčtovaný formulář.</span><span class="sxs-lookup"><span data-stu-id="77c30-236">`Edit(int, Product)` processes  the posted form.</span></span>

<span data-ttu-id="77c30-237">Postup vyřešení správné trasy:</span><span class="sxs-lookup"><span data-stu-id="77c30-237">To resolve the correct route:</span></span>

* <span data-ttu-id="77c30-238">`Edit(int, Product)`je vybrána, pokud `POST`je požadavek HTTP .</span><span class="sxs-lookup"><span data-stu-id="77c30-238">`Edit(int, Product)` is selected when the request is an HTTP `POST`.</span></span>
* <span data-ttu-id="77c30-239">`Edit(int)`je vybrána, pokud je [sloveso HTTP](#verb) cokoli jiného.</span><span class="sxs-lookup"><span data-stu-id="77c30-239">`Edit(int)` is selected when the [HTTP verb](#verb) is anything else.</span></span> <span data-ttu-id="77c30-240">`Edit(int)`se obecně `GET`nazývá přes .</span><span class="sxs-lookup"><span data-stu-id="77c30-240">`Edit(int)` is generally called via `GET`.</span></span>

<span data-ttu-id="77c30-241">V <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute> `[HttpPost]`, , je k dispozici pro směrování tak, aby jej můžete zvolit na základě metody HTTP požadavku.</span><span class="sxs-lookup"><span data-stu-id="77c30-241">The <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute>, `[HttpPost]`, is provided to routing so that it can choose based on the HTTP method of the request.</span></span> <span data-ttu-id="77c30-242">Dělá `HttpPostAttribute` `Edit(int, Product)` lepší zápas `Edit(int)`než .</span><span class="sxs-lookup"><span data-stu-id="77c30-242">The `HttpPostAttribute` makes `Edit(int, Product)` a better match than `Edit(int)`.</span></span>

<span data-ttu-id="77c30-243">Je důležité pochopit roli atributů, `HttpPostAttribute`jako je .</span><span class="sxs-lookup"><span data-stu-id="77c30-243">It's important to understand the role of attributes like `HttpPostAttribute`.</span></span> <span data-ttu-id="77c30-244">Podobné atributy jsou definovány pro ostatní [http slovesa](#verb).</span><span class="sxs-lookup"><span data-stu-id="77c30-244">Similar attributes are defined for other [HTTP verbs](#verb).</span></span> <span data-ttu-id="77c30-245">V [běžném směrování](#cr)je běžné, že akce používají stejný název akce, když jsou součástí zobrazovaného formuláře, odešle pracovní postup formuláře.</span><span class="sxs-lookup"><span data-stu-id="77c30-245">In [conventional routing](#cr), it's common for actions to use the same action name when they're part of a show form, submit form workflow.</span></span> <span data-ttu-id="77c30-246">Například najdete [v tématu Prozkoumání dvou metod akce úprav](xref:tutorials/first-mvc-app/controller-methods-views#get-post).</span><span class="sxs-lookup"><span data-stu-id="77c30-246">For example, see [Examine the two Edit action methods](xref:tutorials/first-mvc-app/controller-methods-views#get-post).</span></span>

<span data-ttu-id="77c30-247">Pokud směrování nemůže vybrat nejlepšího <xref:System.Reflection.AmbiguousMatchException> kandidáta, je vyvolána, výpis více odpovídajícíkoncové body.</span><span class="sxs-lookup"><span data-stu-id="77c30-247">If routing can't choose a best candidate, an <xref:System.Reflection.AmbiguousMatchException> is thrown, listing the multiple matched endpoints.</span></span>

<a name="routing-route-name-ref-label"></a>

### <a name="conventional-route-names"></a><span data-ttu-id="77c30-248">Konvenční názvy tras</span><span class="sxs-lookup"><span data-stu-id="77c30-248">Conventional route names</span></span>

<span data-ttu-id="77c30-249">Řetězce `"blog"` a `"default"` v následujících příkladech jsou konvenční názvy tras:</span><span class="sxs-lookup"><span data-stu-id="77c30-249">The strings  `"blog"` and `"default"` in the following examples are conventional route names:</span></span>

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

<span data-ttu-id="77c30-250">Názvy tras dávají cestě logický název.</span><span class="sxs-lookup"><span data-stu-id="77c30-250">The route names give the route a logical name.</span></span> <span data-ttu-id="77c30-251">Pojmenovanou trasu lze použít pro generování adresy URL.</span><span class="sxs-lookup"><span data-stu-id="77c30-251">The named route can be used for URL generation.</span></span> <span data-ttu-id="77c30-252">Použití pojmenované trasy zjednodušuje vytváření adres URL, pokud by řazení tras mohlo zkomplikovat generování adres URL.</span><span class="sxs-lookup"><span data-stu-id="77c30-252">Using a named route simplifies URL creation when the ordering of routes could make URL generation complicated.</span></span> <span data-ttu-id="77c30-253">Názvy tras musí být jedinečné v široké masivu.</span><span class="sxs-lookup"><span data-stu-id="77c30-253">Route names must be unique application wide.</span></span>

<span data-ttu-id="77c30-254">Názvy tras:</span><span class="sxs-lookup"><span data-stu-id="77c30-254">Route names:</span></span>

* <span data-ttu-id="77c30-255">Nemají žádný vliv na párování adres URL nebo zpracování požadavků.</span><span class="sxs-lookup"><span data-stu-id="77c30-255">Have no impact on URL matching or handling of requests.</span></span>
* <span data-ttu-id="77c30-256">Používají se pouze pro generování adres URL.</span><span class="sxs-lookup"><span data-stu-id="77c30-256">Are used only for URL generation.</span></span>

<span data-ttu-id="77c30-257">Koncept názvu trasy je v směrování reprezentován jako [IEndpointNameMetadata](xref:Microsoft.AspNetCore.Routing.IEndpointNameMetadata).</span><span class="sxs-lookup"><span data-stu-id="77c30-257">The route name concept is represented in routing as [IEndpointNameMetadata](xref:Microsoft.AspNetCore.Routing.IEndpointNameMetadata).</span></span> <span data-ttu-id="77c30-258">Termíny **název trasy** a název **koncového bodu**:</span><span class="sxs-lookup"><span data-stu-id="77c30-258">The terms **route name** and **endpoint name**:</span></span>

* <span data-ttu-id="77c30-259">Jsou zaměnitelné.</span><span class="sxs-lookup"><span data-stu-id="77c30-259">Are interchangeable.</span></span>
* <span data-ttu-id="77c30-260">Který z nich se používá v dokumentaci a kód závisí na rozhraní API je popsáno.</span><span class="sxs-lookup"><span data-stu-id="77c30-260">Which one is used in documentation and code depends on the API being described.</span></span>

<a name="attribute-routing-ref-label"></a>
<a name="ar"></a>

## <a name="attribute-routing-for-rest-apis"></a><span data-ttu-id="77c30-261">Směrování atributů pro rest API</span><span class="sxs-lookup"><span data-stu-id="77c30-261">Attribute routing for REST APIs</span></span>

<span data-ttu-id="77c30-262">REST API by měl použít směrování atributů k modelování funkce aplikace jako sadu prostředků, kde jsou operace reprezentovány [http slovesa](#verb).</span><span class="sxs-lookup"><span data-stu-id="77c30-262">REST APIs should use attribute routing to model the app's functionality as a set of resources where operations are represented by [HTTP verbs](#verb).</span></span>

<span data-ttu-id="77c30-263">Směrování atributů používá sadu atributů k mapování akcí přímo na směrování šablon.</span><span class="sxs-lookup"><span data-stu-id="77c30-263">Attribute routing uses a set of attributes to map actions directly to route templates.</span></span> <span data-ttu-id="77c30-264">Následující `StartUp.Configure` kód je typický pro rozhraní REST API a používá se v další ukázce:</span><span class="sxs-lookup"><span data-stu-id="77c30-264">The following `StartUp.Configure` code is typical for a REST API and is used in the next sample:</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupApi.cs?name=snippet)]

<span data-ttu-id="77c30-265">V předchozím kódu <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*> je volána uvnitř `UseEndpoints` mapovat atribut směrované řadiče.</span><span class="sxs-lookup"><span data-stu-id="77c30-265">In the preceding code, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*> is called inside `UseEndpoints` to map attribute routed controllers.</span></span>

<span data-ttu-id="77c30-266">V následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="77c30-266">In the following example:</span></span>

* <span data-ttu-id="77c30-267">Používá se `Configure` předchozí metoda.</span><span class="sxs-lookup"><span data-stu-id="77c30-267">The preceding `Configure` method is used.</span></span>
* <span data-ttu-id="77c30-268">`HomeController`odpovídá sadě adres URL podobných tomu, `{controller=Home}/{action=Index}/{id?}` co odpovídá výchozí konvenční trase.</span><span class="sxs-lookup"><span data-stu-id="77c30-268">`HomeController` matches a set of URLs similar to what the default conventional route `{controller=Home}/{action=Index}/{id?}` matches.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet2)]

<span data-ttu-id="77c30-269">Akce `HomeController.Index` je spuštěna pro některou `/` `/Home`z `/Home/Index`cest `/Home/Index/3`url , , nebo .</span><span class="sxs-lookup"><span data-stu-id="77c30-269">The `HomeController.Index` action is run for any of the URL paths `/`, `/Home`, `/Home/Index`, or `/Home/Index/3`.</span></span>

<span data-ttu-id="77c30-270">Tento příklad zvýrazní klíčový programovací rozdíl mezi směrováním atributů a [konvenčním směrováním](#cr).</span><span class="sxs-lookup"><span data-stu-id="77c30-270">This example highlights a key programming difference between attribute routing and [conventional routing](#cr).</span></span> <span data-ttu-id="77c30-271">Směrování atributů vyžaduje k určení trasy více vstupů.</span><span class="sxs-lookup"><span data-stu-id="77c30-271">Attribute routing requires more input to specify a route.</span></span> <span data-ttu-id="77c30-272">Konvenční výchozí trasa zpracovává trasy stručněji.</span><span class="sxs-lookup"><span data-stu-id="77c30-272">The conventional default route handles routes more succinctly.</span></span> <span data-ttu-id="77c30-273">Směrování atributů však umožňuje a vyžaduje přesné řízení, které šablony tras se vztahují na každou [akci](#action).</span><span class="sxs-lookup"><span data-stu-id="77c30-273">However, attribute routing allows and requires precise control of which route templates apply to each [action](#action).</span></span>

<span data-ttu-id="77c30-274">Při směrování atributů nehrají názvy názvů řadičů a akcí **žádnou** roli, ve které je akce spárována.</span><span class="sxs-lookup"><span data-stu-id="77c30-274">With attribute routing, the controller name and action names play **no** role in which action is matched.</span></span> <span data-ttu-id="77c30-275">Následující příklad odpovídá stejným adresám URL jako v předchozím příkladu:</span><span class="sxs-lookup"><span data-stu-id="77c30-275">The following example matches the same URLs as the previous example:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemoController.cs?name=snippet)]

<span data-ttu-id="77c30-276">Následující kód používá token `action` `controller`nahrazení a :</span><span class="sxs-lookup"><span data-stu-id="77c30-276">The following code uses token replacement for `action` and `controller`:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet22)]

<span data-ttu-id="77c30-277">Pro regulátor platí `[Route("[controller]/[action]")]` následující kód:</span><span class="sxs-lookup"><span data-stu-id="77c30-277">The following code applies `[Route("[controller]/[action]")]` to the controller:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet24)]

<span data-ttu-id="77c30-278">V předchozím kódu musí `Index` šablony metody předřadit `/` nebo `~/` k šablonám postupu.</span><span class="sxs-lookup"><span data-stu-id="77c30-278">In the preceding code, the `Index` method templates must prepend `/` or `~/` to the route templates.</span></span> <span data-ttu-id="77c30-279">Šablony trasy použité na akci, `/` `~/` která začíná nebo nezkombinují se šablonami tras aplikovanými na kontroler.</span><span class="sxs-lookup"><span data-stu-id="77c30-279">Route templates applied to an action that begin with `/` or `~/` don't get combined with route templates applied to the controller.</span></span>

<span data-ttu-id="77c30-280">Informace o výběru šablony trasy najdete v [tématu Priorita šablony trasy.](xref:fundamentals/routing#rtp)</span><span class="sxs-lookup"><span data-stu-id="77c30-280">See [Route template precedence](xref:fundamentals/routing#rtp) for information on route template selection.</span></span>

## <a name="reserved-routing-names"></a><span data-ttu-id="77c30-281">Rezervované názvy směrování</span><span class="sxs-lookup"><span data-stu-id="77c30-281">Reserved routing names</span></span>

<span data-ttu-id="77c30-282">Následující klíčová slova jsou vyhrazené názvy parametrů trasy při použití řadičů nebo stránek Razor:</span><span class="sxs-lookup"><span data-stu-id="77c30-282">The following keywords are reserved route parameter names when using Controllers or Razor Pages:</span></span>

* `action`
* `area`
* `controller`
* `handler`
* `page`

<span data-ttu-id="77c30-283">Použití `page` parametru trasy s postupem atributu je běžnou chybou.</span><span class="sxs-lookup"><span data-stu-id="77c30-283">Using `page` as a route parameter with attribute routing is a common error.</span></span> <span data-ttu-id="77c30-284">To má za následek nekonzistentní a matoucí chování s generováním adresy URL.</span><span class="sxs-lookup"><span data-stu-id="77c30-284">Doing that results in inconsistent and confusing behavior with URL generation.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemo2Controller.cs?name=snippet)]

<span data-ttu-id="77c30-285">Názvy speciálních parametrů jsou používány generování adresy URL k určení, pokud operace generování adresy URL odkazuje na Razor Page nebo řadič.</span><span class="sxs-lookup"><span data-stu-id="77c30-285">The special parameter names are used by the URL generation to determine if a URL generation operation refers to a Razor Page or to a Controller.</span></span>

<a name="verb"></a>

## <a name="http-verb-templates"></a><span data-ttu-id="77c30-286">Šablony sloves PROTOKOLU HTTP</span><span class="sxs-lookup"><span data-stu-id="77c30-286">HTTP verb templates</span></span>

<span data-ttu-id="77c30-287">ASP.NET Core má následující šablony sloves PROTOKOLU HTTP:</span><span class="sxs-lookup"><span data-stu-id="77c30-287">ASP.NET Core has the following HTTP verb templates:</span></span>

* <span data-ttu-id="77c30-288">[[HttpGet]](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)</span><span class="sxs-lookup"><span data-stu-id="77c30-288">[[HttpGet]](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)</span></span>
* <span data-ttu-id="77c30-289">[[HttpPost]](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute)</span><span class="sxs-lookup"><span data-stu-id="77c30-289">[[HttpPost]](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute)</span></span>
* <span data-ttu-id="77c30-290">[[HttpPut]](xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute)</span><span class="sxs-lookup"><span data-stu-id="77c30-290">[[HttpPut]](xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute)</span></span>
* <span data-ttu-id="77c30-291">[[HttpDelete]](xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute)</span><span class="sxs-lookup"><span data-stu-id="77c30-291">[[HttpDelete]](xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute)</span></span>
* <span data-ttu-id="77c30-292">[To je v pořádku.](xref:Microsoft.AspNetCore.Mvc.HttpHeadAttribute)</span><span class="sxs-lookup"><span data-stu-id="77c30-292">[[HttpHead]](xref:Microsoft.AspNetCore.Mvc.HttpHeadAttribute)</span></span>
* <span data-ttu-id="77c30-293">[[HttpPatch]](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)</span><span class="sxs-lookup"><span data-stu-id="77c30-293">[[HttpPatch]](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)</span></span>

<a name="rt"></a>

### <a name="route-templates"></a><span data-ttu-id="77c30-294">Šablony tras</span><span class="sxs-lookup"><span data-stu-id="77c30-294">Route templates</span></span>

<span data-ttu-id="77c30-295">ASP.NET Core má následující šablony tras:</span><span class="sxs-lookup"><span data-stu-id="77c30-295">ASP.NET Core has the following route templates:</span></span>

* <span data-ttu-id="77c30-296">Všechny [šablony sloves PROTOKOLU HTTP](#verb) jsou šablony tras.</span><span class="sxs-lookup"><span data-stu-id="77c30-296">All the [HTTP verb templates](#verb) are route templates.</span></span>
* <span data-ttu-id="77c30-297">[To je v pořádku.](xref:Microsoft.AspNetCore.Mvc.RouteAttribute)</span><span class="sxs-lookup"><span data-stu-id="77c30-297">[[Route]](xref:Microsoft.AspNetCore.Mvc.RouteAttribute)</span></span>

<a name="arx"></a>

### <a name="attribute-routing-with-http-verb-attributes"></a><span data-ttu-id="77c30-298">Směrování atributů s atributy http slovesa</span><span class="sxs-lookup"><span data-stu-id="77c30-298">Attribute routing with Http verb attributes</span></span>

<span data-ttu-id="77c30-299">Zvažte následující ovladač:</span><span class="sxs-lookup"><span data-stu-id="77c30-299">Consider the following controller:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet)]

<span data-ttu-id="77c30-300">V předchozím kódu:</span><span class="sxs-lookup"><span data-stu-id="77c30-300">In the preceding code:</span></span>

* <span data-ttu-id="77c30-301">Každá akce `[HttpGet]` obsahuje atribut, který omezuje párování pouze požadavků HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="77c30-301">Each action contains the `[HttpGet]` attribute, which constrains matching to HTTP GET requests only.</span></span>
* <span data-ttu-id="77c30-302">Akce `GetProduct` obsahuje `"{id}"` šablonu, `id` proto je `"api/[controller]"` připojen k šabloně na řadiči.</span><span class="sxs-lookup"><span data-stu-id="77c30-302">The `GetProduct` action includes the `"{id}"` template, therefore `id` is appended to the `"api/[controller]"` template on the controller.</span></span> <span data-ttu-id="77c30-303">Šablona metod `"api/[controller]/"{id}""`je .</span><span class="sxs-lookup"><span data-stu-id="77c30-303">The methods template is `"api/[controller]/"{id}""`.</span></span> <span data-ttu-id="77c30-304">Proto tato akce odpovídá pouze GET `/api/test2/xyz`požadavky`/api/test2/123``/api/test2/{any string}`pro formulář , , , atd.</span><span class="sxs-lookup"><span data-stu-id="77c30-304">Therefore this action only matches GET requests of for the form `/api/test2/xyz`,`/api/test2/123`,`/api/test2/{any string}`, etc.</span></span>
  [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet2)]
* <span data-ttu-id="77c30-305">Akce `GetIntProduct` obsahuje `"int/{id:int}")` šablonu.</span><span class="sxs-lookup"><span data-stu-id="77c30-305">The `GetIntProduct` action contains the `"int/{id:int}")` template.</span></span> <span data-ttu-id="77c30-306">Část `:int` šablony omezuje hodnoty `id` trasy na řetězce, které lze převést na celé číslo.</span><span class="sxs-lookup"><span data-stu-id="77c30-306">The `:int` portion of the template constrains the `id` route values to strings that can be converted to an integer.</span></span> <span data-ttu-id="77c30-307">Požadavek GET `/api/test2/int/abc`na :</span><span class="sxs-lookup"><span data-stu-id="77c30-307">A GET request to `/api/test2/int/abc`:</span></span>
  * <span data-ttu-id="77c30-308">Neodpovídá téhle akci.</span><span class="sxs-lookup"><span data-stu-id="77c30-308">Doesn't match this action.</span></span>
  * <span data-ttu-id="77c30-309">Vrátí chybu [404 Not Found.](https://developer.mozilla.org/docs/Web/HTTP/Status/404)</span><span class="sxs-lookup"><span data-stu-id="77c30-309">Returns a [404 Not Found](https://developer.mozilla.org/docs/Web/HTTP/Status/404) error.</span></span>
    [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet3)]
* <span data-ttu-id="77c30-310">Akce `GetInt2Product` obsahuje `{id}` v šabloně, ale neomezuje `id` na hodnoty, které lze převést na celé číslo.</span><span class="sxs-lookup"><span data-stu-id="77c30-310">The `GetInt2Product` action contains `{id}` in the template, but doesn't constrain `id` to values that can be converted to an integer.</span></span> <span data-ttu-id="77c30-311">Požadavek GET `/api/test2/int2/abc`na :</span><span class="sxs-lookup"><span data-stu-id="77c30-311">A GET request to `/api/test2/int2/abc`:</span></span>
  * <span data-ttu-id="77c30-312">Odpovídá této trase.</span><span class="sxs-lookup"><span data-stu-id="77c30-312">Matches this route.</span></span>
  * <span data-ttu-id="77c30-313">Vazba modelu `abc` se nepodařilo převést na celé číslo.</span><span class="sxs-lookup"><span data-stu-id="77c30-313">Model binding fails to convert `abc` to an integer.</span></span> <span data-ttu-id="77c30-314">Parametr `id` metody je celé číslo.</span><span class="sxs-lookup"><span data-stu-id="77c30-314">The `id` parameter of the method is integer.</span></span>
  * <span data-ttu-id="77c30-315">Vrátí [400 Chybný požadavek,](https://developer.mozilla.org/docs/Web/HTTP/Status/400) `abc` protože vazba modelu se nepodařilo převést na celé číslo.</span><span class="sxs-lookup"><span data-stu-id="77c30-315">Returns a [400 Bad Request](https://developer.mozilla.org/docs/Web/HTTP/Status/400) because model binding failed to convert`abc` to an integer.</span></span>
      [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet4)]

<span data-ttu-id="77c30-316">Směrování atributů <xref:Microsoft.AspNetCore.Mvc.Routing.HttpMethodAttribute> může používat <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute> <xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute>atributy, jako jsou například , a <xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute>.</span><span class="sxs-lookup"><span data-stu-id="77c30-316">Attribute routing can use <xref:Microsoft.AspNetCore.Mvc.Routing.HttpMethodAttribute> attributes such as <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute>, <xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute>, and <xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute>.</span></span> <span data-ttu-id="77c30-317">Všechny atributy [slovesa HTTP](#verb) přijímají šablonu trasy.</span><span class="sxs-lookup"><span data-stu-id="77c30-317">All of the [HTTP verb](#verb) attributes accept a route template.</span></span> <span data-ttu-id="77c30-318">Následující příklad ukazuje dvě akce, které odpovídají stejné šabloně trasy:</span><span class="sxs-lookup"><span data-stu-id="77c30-318">The following example shows two actions that match the same route template:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyProductsController.cs?name=snippet1)]

<span data-ttu-id="77c30-319">Pomocí cesty `/products3`URL :</span><span class="sxs-lookup"><span data-stu-id="77c30-319">Using the URL path `/products3`:</span></span>

* <span data-ttu-id="77c30-320">Akce `MyProductsController.ListProducts` se spustí, když `GET`je [sloveso HTTP](#verb) .</span><span class="sxs-lookup"><span data-stu-id="77c30-320">The `MyProductsController.ListProducts` action runs when the [HTTP verb](#verb) is `GET`.</span></span>
* <span data-ttu-id="77c30-321">Akce `MyProductsController.CreateProduct` se spustí, když `POST`je [sloveso HTTP](#verb) .</span><span class="sxs-lookup"><span data-stu-id="77c30-321">The `MyProductsController.CreateProduct` action runs when the [HTTP verb](#verb) is `POST`.</span></span>

<span data-ttu-id="77c30-322">Při vytváření rozhraní REST API, je vzácné, že `[Route(...)]` budete muset použít na metodu akce, protože akce přijímá všechny metody HTTP.</span><span class="sxs-lookup"><span data-stu-id="77c30-322">When building a REST API, it's rare that you'll need to use `[Route(...)]` on an action method because the action accepts all HTTP methods.</span></span> <span data-ttu-id="77c30-323">Je lepší použít konkrétnější [atribut http sloveso,](#verb) abyste byli přesní o tom, co vaše rozhraní API podporuje.</span><span class="sxs-lookup"><span data-stu-id="77c30-323">It's better to use the more specific [HTTP verb attribute](#verb) to be precise about what your API supports.</span></span> <span data-ttu-id="77c30-324">Očekává se, že klienti rozhraní REST API budou vědět, jaké cesty a http slovesa jsou mapována na konkrétní logické operace.</span><span class="sxs-lookup"><span data-stu-id="77c30-324">Clients of REST APIs are expected to know what paths and HTTP verbs map to specific logical operations.</span></span>

<span data-ttu-id="77c30-325">REST API by měl použít směrování atributů k modelování funkce aplikace jako sadu prostředků, kde jsou operace reprezentovány http slovesa.</span><span class="sxs-lookup"><span data-stu-id="77c30-325">REST APIs should use attribute routing to model the app's functionality as a set of resources where operations are represented by HTTP verbs.</span></span> <span data-ttu-id="77c30-326">To znamená, že mnoho operací, například GET a POST na stejném logickém prostředku, používá stejnou adresu URL.</span><span class="sxs-lookup"><span data-stu-id="77c30-326">This means that many operations, for example, GET and POST on the same logical resource use the same URL.</span></span> <span data-ttu-id="77c30-327">Směrování atributů poskytuje úroveň řízení, která je potřeba pečlivě navrhnout rozložení veřejného koncového bodu rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="77c30-327">Attribute routing provides a level of control that's needed to carefully design an API's public endpoint layout.</span></span>

<span data-ttu-id="77c30-328">Vzhledem k tomu, že trasa atributu se vztahuje na konkrétní akci, je snadné nastavit parametry požadované jako součást definice šablony trasy.</span><span class="sxs-lookup"><span data-stu-id="77c30-328">Since an attribute route applies to a specific action, it's easy to make parameters required as part of the route template definition.</span></span> <span data-ttu-id="77c30-329">V následujícím příkladu `id` je požadováno jako součást cesty URL:</span><span class="sxs-lookup"><span data-stu-id="77c30-329">In the following example, `id` is required as part of the URL path:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet2)]

<span data-ttu-id="77c30-330">Akce: `Products2ApiController.GetProduct(int)`</span><span class="sxs-lookup"><span data-stu-id="77c30-330">The `Products2ApiController.GetProduct(int)` action:</span></span>

* <span data-ttu-id="77c30-331">Je spuštěn s adresou URL cestu, jako`/products2/3`</span><span class="sxs-lookup"><span data-stu-id="77c30-331">Is run with URL path like `/products2/3`</span></span>
* <span data-ttu-id="77c30-332">Není spuštěn s adresou `/products2`URL cestu .</span><span class="sxs-lookup"><span data-stu-id="77c30-332">Isn't run with the URL path `/products2`.</span></span>

<span data-ttu-id="77c30-333">Atribut [[Spotřebovává]](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>) umožňuje akci omezit typy obsahu podporovaných požadavků.</span><span class="sxs-lookup"><span data-stu-id="77c30-333">The [[Consumes]](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>) attribute allows an action to limit the supported request content types.</span></span> <span data-ttu-id="77c30-334">Další informace naleznete [v tématu Definování typů obsahu podporovaných požadavků pomocí atributu Spotřebovává](xref:web-api/index#consumes).</span><span class="sxs-lookup"><span data-stu-id="77c30-334">For more information, see [Define supported request content types with the Consumes attribute](xref:web-api/index#consumes).</span></span>

 <span data-ttu-id="77c30-335">Úplný popis šablon tras a souvisejících možností naleznete v tématu [Směrování.](xref:fundamentals/routing)</span><span class="sxs-lookup"><span data-stu-id="77c30-335">See [Routing](xref:fundamentals/routing) for a full description of route templates and related options.</span></span>

<span data-ttu-id="77c30-336">Další informace `[ApiController]`naleznete v [tématu ApiController atribut](xref:web-api/index##apicontroller-attribute).</span><span class="sxs-lookup"><span data-stu-id="77c30-336">For more information on `[ApiController]`, see [ApiController attribute](xref:web-api/index##apicontroller-attribute).</span></span>

## <a name="route-name"></a><span data-ttu-id="77c30-337">Název trasy</span><span class="sxs-lookup"><span data-stu-id="77c30-337">Route name</span></span>

<span data-ttu-id="77c30-338">Následující kód definuje název postupu `Products_List`:</span><span class="sxs-lookup"><span data-stu-id="77c30-338">The following code  defines a route name of `Products_List`:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet2)]

<span data-ttu-id="77c30-339">Názvy tras lze použít ke generování adresy URL na základě konkrétní trasy.</span><span class="sxs-lookup"><span data-stu-id="77c30-339">Route names can be used to generate a URL based on a specific route.</span></span> <span data-ttu-id="77c30-340">Názvy tras:</span><span class="sxs-lookup"><span data-stu-id="77c30-340">Route names:</span></span>

* <span data-ttu-id="77c30-341">Nemají žádný vliv na chování přiřazování adres URL směrování.</span><span class="sxs-lookup"><span data-stu-id="77c30-341">Have no impact on the URL matching behavior of routing.</span></span>
* <span data-ttu-id="77c30-342">Používají se pouze pro generování adres URL.</span><span class="sxs-lookup"><span data-stu-id="77c30-342">Are only used for URL generation.</span></span>

<span data-ttu-id="77c30-343">Názvy tras musí být jedinečné pro celou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="77c30-343">Route names must be unique application-wide.</span></span>

<span data-ttu-id="77c30-344">Porovnejte předchozí kód s konvenční výchozí `id` trasou,`{id?}`která definuje parametr jako volitelný ( ).</span><span class="sxs-lookup"><span data-stu-id="77c30-344">Contrast the preceding code with the conventional default route, which defines the `id` parameter as optional (`{id?}`).</span></span> <span data-ttu-id="77c30-345">Možnost přesně určit api má výhody, jako `/products` `/products/5` je například povolení a odeslání do různých akcí.</span><span class="sxs-lookup"><span data-stu-id="77c30-345">The ability to precisely specify APIs has advantages, such as  allowing `/products` and `/products/5` to be dispatched to different actions.</span></span>

<a name="routing-combining-ref-label"></a>

## <a name="combining-attribute-routes"></a><span data-ttu-id="77c30-346">Kombinování tras atributů</span><span class="sxs-lookup"><span data-stu-id="77c30-346">Combining attribute routes</span></span>

<span data-ttu-id="77c30-347">Chcete-li, aby směrování atributů bylo méně opakované, jsou atributy trasy na řadiči kombinovány s atributy trasy u jednotlivých akcí.</span><span class="sxs-lookup"><span data-stu-id="77c30-347">To make attribute routing less repetitive, route attributes on the controller are combined with route attributes on the individual actions.</span></span> <span data-ttu-id="77c30-348">Všechny šablony tras definované na řadiči jsou předřazeny k směrování šablon akcí.</span><span class="sxs-lookup"><span data-stu-id="77c30-348">Any route templates defined on the controller are prepended to route templates on the actions.</span></span> <span data-ttu-id="77c30-349">Umístění atributu trasy na řadič provede **všechny** akce v řadiči pomocí směrování atributu.</span><span class="sxs-lookup"><span data-stu-id="77c30-349">Placing a route attribute on the controller makes **all** actions in the controller use attribute routing.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet)]

<span data-ttu-id="77c30-350">V předchozím příkladu:</span><span class="sxs-lookup"><span data-stu-id="77c30-350">In the preceding example:</span></span>

* <span data-ttu-id="77c30-351">Cesta `/products` URL se může shodovat`ProductsApi.ListProducts`</span><span class="sxs-lookup"><span data-stu-id="77c30-351">The URL path `/products` can match `ProductsApi.ListProducts`</span></span>
* <span data-ttu-id="77c30-352">Cesta `/products/5` url se `ProductsApi.GetProduct(int)`může shodovat .</span><span class="sxs-lookup"><span data-stu-id="77c30-352">The URL path `/products/5` can match `ProductsApi.GetProduct(int)`.</span></span>

<span data-ttu-id="77c30-353">Obě tyto akce odpovídají `GET` pouze protokolu HTTP, `[HttpGet]` protože jsou označeny atributem.</span><span class="sxs-lookup"><span data-stu-id="77c30-353">Both of these actions only match HTTP `GET` because they're marked with the `[HttpGet]` attribute.</span></span>

<span data-ttu-id="77c30-354">Šablony trasy použité na akci, `/` `~/` která začíná nebo nezkombinují se šablonami tras aplikovanými na kontroler.</span><span class="sxs-lookup"><span data-stu-id="77c30-354">Route templates applied to an action that begin with `/` or `~/` don't get combined with route templates applied to the controller.</span></span> <span data-ttu-id="77c30-355">Následující příklad odpovídá sadě cest URL podobné výchozí trase.</span><span class="sxs-lookup"><span data-stu-id="77c30-355">The following example matches a set of URL paths similar to the default route.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet)]

<span data-ttu-id="77c30-356">Následující tabulka vysvětluje `[Route]` atributy v předchozím kódu:</span><span class="sxs-lookup"><span data-stu-id="77c30-356">The following table explains the `[Route]` attributes in the preceding code:</span></span>

| <span data-ttu-id="77c30-357">Atribut</span><span class="sxs-lookup"><span data-stu-id="77c30-357">Attribute</span></span>               | <span data-ttu-id="77c30-358">Kombinuje s`[Route("Home")]`</span><span class="sxs-lookup"><span data-stu-id="77c30-358">Combines with `[Route("Home")]`</span></span> | <span data-ttu-id="77c30-359">Definuje šablonu trasy.</span><span class="sxs-lookup"><span data-stu-id="77c30-359">Defines route template</span></span> |
| ----------------- | ------------ | --------- |
| `[Route("")]` | <span data-ttu-id="77c30-360">Ano</span><span class="sxs-lookup"><span data-stu-id="77c30-360">Yes</span></span> | `"Home"` |
| `[Route("Index")]` | <span data-ttu-id="77c30-361">Ano</span><span class="sxs-lookup"><span data-stu-id="77c30-361">Yes</span></span> | `"Home/Index"` |
| `[Route("/")]` | <span data-ttu-id="77c30-362">**Ne**</span><span class="sxs-lookup"><span data-stu-id="77c30-362">**No**</span></span> | `""` |
| `[Route("About")]` | <span data-ttu-id="77c30-363">Ano</span><span class="sxs-lookup"><span data-stu-id="77c30-363">Yes</span></span> | `"Home/About"` |

<a name="routing-ordering-ref-label"></a>
<a name="oar"></a>

### <a name="attribute-route-order"></a><span data-ttu-id="77c30-364">Pořadí postupu atributu</span><span class="sxs-lookup"><span data-stu-id="77c30-364">Attribute route order</span></span>

<span data-ttu-id="77c30-365">Směrování vytvoří strom a odpovídá všem koncovým bodům současně:</span><span class="sxs-lookup"><span data-stu-id="77c30-365">Routing builds a tree and matches all endpoints simultaneously:</span></span>

* <span data-ttu-id="77c30-366">Položky trasy se chovají, jako by byly umístěny v ideálním pořadí.</span><span class="sxs-lookup"><span data-stu-id="77c30-366">The route entries behave as if placed in an ideal ordering.</span></span>
* <span data-ttu-id="77c30-367">Nejkonkrétnější trasy mají šanci provést před obecnější trasy.</span><span class="sxs-lookup"><span data-stu-id="77c30-367">The most specific routes have a chance to execute before the more general routes.</span></span>

<span data-ttu-id="77c30-368">Například trasa atributu jako `blog/search/{topic}` je konkrétnější `blog/{*article}`než trasa atributu jako .</span><span class="sxs-lookup"><span data-stu-id="77c30-368">For example, an attribute route like `blog/search/{topic}` is more specific than an attribute route like `blog/{*article}`.</span></span> <span data-ttu-id="77c30-369">Trasa `blog/search/{topic}` má ve výchozím nastavení vyšší prioritu, protože je konkrétnější.</span><span class="sxs-lookup"><span data-stu-id="77c30-369">The `blog/search/{topic}` route has higher priority, by default, because it's more specific.</span></span> <span data-ttu-id="77c30-370">Pomocí [konvenčního směrování](#cr)je vývojář zodpovědný za umístění tras v požadovaném pořadí.</span><span class="sxs-lookup"><span data-stu-id="77c30-370">Using [conventional routing](#cr), the developer is responsible for placing routes in the desired order.</span></span>

<span data-ttu-id="77c30-371">Trasy atributů mohou konfigurovat <xref:Microsoft.AspNetCore.Mvc.RouteAttribute.Order> objednávku pomocí vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="77c30-371">Attribute routes can configure an order using the <xref:Microsoft.AspNetCore.Mvc.RouteAttribute.Order> property.</span></span> <span data-ttu-id="77c30-372">Všechny atributy trasy za předpokladu, [patří](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) `Order` .</span><span class="sxs-lookup"><span data-stu-id="77c30-372">All of the framework provided [route attributes](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) include `Order` .</span></span> <span data-ttu-id="77c30-373">Trasy jsou zpracovávány podle vzestupného druhu `Order` vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="77c30-373">Routes are processed according to an ascending sort of the `Order` property.</span></span> <span data-ttu-id="77c30-374">Výchozí pořadí `0`je .</span><span class="sxs-lookup"><span data-stu-id="77c30-374">The default order is `0`.</span></span> <span data-ttu-id="77c30-375">Nastavení trasy `Order = -1` pomocí spuštění před trasami, které nenastavují pořadí.</span><span class="sxs-lookup"><span data-stu-id="77c30-375">Setting a route using `Order = -1` runs before routes that don't set an order.</span></span> <span data-ttu-id="77c30-376">Nastavení trasy `Order = 1` pomocí spuštění po výchozím pořadí trasy.</span><span class="sxs-lookup"><span data-stu-id="77c30-376">Setting a route using `Order = 1` runs after default route ordering.</span></span>

<span data-ttu-id="77c30-377">**Vzávislosti** `Order`na .</span><span class="sxs-lookup"><span data-stu-id="77c30-377">**Avoid** depending on `Order`.</span></span> <span data-ttu-id="77c30-378">Pokud místo url aplikace vyžaduje explicitní hodnoty objednávek pro správné směrování, je pravděpodobně matoucí i pro klienty.</span><span class="sxs-lookup"><span data-stu-id="77c30-378">If an app's URL-space requires explicit order values to route correctly, then it's likely confusing to clients as well.</span></span> <span data-ttu-id="77c30-379">Obecně platí, že směrování atributů vybere správnou trasu s párováním adres URL.</span><span class="sxs-lookup"><span data-stu-id="77c30-379">In general, attribute routing selects the correct route with URL matching.</span></span> <span data-ttu-id="77c30-380">Pokud výchozí pořadí použité pro generování adresy URL nefunguje, použití názvu trasy jako `Order` přepsání je obvykle jednodušší než použití vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="77c30-380">If the default order used for URL generation isn't working, using a route name as an override is usually simpler than applying the `Order` property.</span></span>

<span data-ttu-id="77c30-381">Zvažte následující dva řadiče, které `/home`oba definují párování trasy :</span><span class="sxs-lookup"><span data-stu-id="77c30-381">Consider the following two controllers which both define the route matching `/home`:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet2)]

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemoController.cs?name=snippet)]

<span data-ttu-id="77c30-382">Požadavek `/home` s předchozím kódem vyvolá výjimku podobnou následující:</span><span class="sxs-lookup"><span data-stu-id="77c30-382">Requesting `/home` with the preceding code throws an exception similar to the following:</span></span>

```text
AmbiguousMatchException: The request matched multiple endpoints. Matches:

 WebMvcRouting.Controllers.HomeController.Index
 WebMvcRouting.Controllers.MyDemoController.MyIndex
```

<span data-ttu-id="77c30-383">Přidání `Order` majek do jednoho z atributů trasy vyřeší nejednoznačnost:</span><span class="sxs-lookup"><span data-stu-id="77c30-383">Adding `Order` to one of the route attributes resolves the ambiguity:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemo3Controller.cs?name=snippet3& highlight=2)]

<span data-ttu-id="77c30-384">S předchozím kódem `/home` spustí `HomeController.Index` koncový bod.</span><span class="sxs-lookup"><span data-stu-id="77c30-384">With the preceding code, `/home` runs the `HomeController.Index` endpoint.</span></span> <span data-ttu-id="77c30-385">Chcete-li `MyDemoController.MyIndex`se `/home/MyIndex`dostat do , žádost .</span><span class="sxs-lookup"><span data-stu-id="77c30-385">To get to the `MyDemoController.MyIndex`, request `/home/MyIndex`.</span></span> <span data-ttu-id="77c30-386">**Poznámka**:</span><span class="sxs-lookup"><span data-stu-id="77c30-386">**Note**:</span></span>

* <span data-ttu-id="77c30-387">Předchozí kód je příklad nebo špatný návrh směrování.</span><span class="sxs-lookup"><span data-stu-id="77c30-387">The preceding code is an example or poor routing design.</span></span> <span data-ttu-id="77c30-388">To bylo použito `Order` k ilustraci majetku.</span><span class="sxs-lookup"><span data-stu-id="77c30-388">It was used to illustrate the `Order` property.</span></span>
* <span data-ttu-id="77c30-389">Vlastnost `Order` pouze řeší nejednoznačnost, že šablona nemůže být uzavřeno.</span><span class="sxs-lookup"><span data-stu-id="77c30-389">The `Order` property only resolves the ambiguity, that template cannot be matched.</span></span> <span data-ttu-id="77c30-390">Bylo by lepší odstranit `[Route("Home")]` šablonu.</span><span class="sxs-lookup"><span data-stu-id="77c30-390">It would be better to remove the `[Route("Home")]` template.</span></span>

<span data-ttu-id="77c30-391">Viz [Konvence tras a aplikací Razor Pages: Pořadí tras](xref:razor-pages/razor-pages-conventions#route-order) pro informace o pořadí tras pomocí Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="77c30-391">See [Razor Pages route and app conventions: Route order](xref:razor-pages/razor-pages-conventions#route-order) for information on route order with Razor Pages.</span></span>

<span data-ttu-id="77c30-392">V některých případech je vrácena chyba HTTP 500 s nejednoznačnými cestami.</span><span class="sxs-lookup"><span data-stu-id="77c30-392">In some cases, an HTTP 500 error is returned with ambiguous routes.</span></span> <span data-ttu-id="77c30-393">Pomocí [protokolování](xref:fundamentals/logging/index) můžete zjistit, `AmbiguousMatchException`které koncové body způsobily .</span><span class="sxs-lookup"><span data-stu-id="77c30-393">Use [logging](xref:fundamentals/logging/index) to see which endpoints caused the `AmbiguousMatchException`.</span></span>

<a name="routing-token-replacement-templates-ref-label"></a>

## <a name="token-replacement-in-route-templates-controller-action-area"></a><span data-ttu-id="77c30-394">Nahrazení tokenu v šablonách tras [kontroler], [akce], [oblast]</span><span class="sxs-lookup"><span data-stu-id="77c30-394">Token replacement in route templates [controller], [action], [area]</span></span>

<span data-ttu-id="77c30-395">Pro větší pohodlí, atribut trasy podporu token nahrazení parametry vyhrazené trasy ohraničující token uzavřením tokenu v jedné z následujících:</span><span class="sxs-lookup"><span data-stu-id="77c30-395">For convenience, attribute routes support token replacement for reserved route parameters by enclosing a token in one of the following:</span></span>

* <span data-ttu-id="77c30-396">Čtvercové závorky:`[]`</span><span class="sxs-lookup"><span data-stu-id="77c30-396">Square braces: `[]`</span></span>
* <span data-ttu-id="77c30-397">Složené závorky:`{}`</span><span class="sxs-lookup"><span data-stu-id="77c30-397">Curly braces: `{}`</span></span>

<span data-ttu-id="77c30-398">Tokeny `[action]`, `[area]`a `[controller]` jsou nahrazeny hodnotami názvu akce, názvu oblasti a názvu řadiče z akce, kde je trasa definována:</span><span class="sxs-lookup"><span data-stu-id="77c30-398">The tokens `[action]`, `[area]`, and `[controller]` are replaced with the values of the action name, area name, and controller name from the action where the route is defined:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet)]

<span data-ttu-id="77c30-399">V předchozím kódu:</span><span class="sxs-lookup"><span data-stu-id="77c30-399">In the preceding code:</span></span>

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet10)]

  * <span data-ttu-id="77c30-400">Odpovídá`/Products0/List`</span><span class="sxs-lookup"><span data-stu-id="77c30-400">Matches `/Products0/List`</span></span>

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet11)]

  * <span data-ttu-id="77c30-401">Odpovídá`/Products0/Edit/{id}`</span><span class="sxs-lookup"><span data-stu-id="77c30-401">Matches `/Products0/Edit/{id}`</span></span>

<span data-ttu-id="77c30-402">Nahrazení tokenu dochází jako poslední krok vytváření trasy atributu.</span><span class="sxs-lookup"><span data-stu-id="77c30-402">Token replacement occurs as the last step of building the attribute routes.</span></span> <span data-ttu-id="77c30-403">Předchozí příklad se chová stejně jako následující kód:</span><span class="sxs-lookup"><span data-stu-id="77c30-403">The preceding example behaves the same as the following code:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet20)]

[!INCLUDE[](~/includes/MTcomments.md)]

<span data-ttu-id="77c30-404">Trasy atributů lze také kombinovat s dědičností.</span><span class="sxs-lookup"><span data-stu-id="77c30-404">Attribute routes can also be combined with inheritance.</span></span> <span data-ttu-id="77c30-405">To je silný v kombinaci s token nahrazení.</span><span class="sxs-lookup"><span data-stu-id="77c30-405">This is powerful combined with token replacement.</span></span> <span data-ttu-id="77c30-406">Nahrazení tokenu platí také pro názvy tras definované postupy atributů.</span><span class="sxs-lookup"><span data-stu-id="77c30-406">Token replacement also applies to route names defined by attribute routes.</span></span>
<span data-ttu-id="77c30-407">`[Route("[controller]/[action]", Name="[controller]_[action]")]`generuje jedinečný název trasy pro každou akci:</span><span class="sxs-lookup"><span data-stu-id="77c30-407">`[Route("[controller]/[action]", Name="[controller]_[action]")]`generates a unique route name for each action:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet5)]

<span data-ttu-id="77c30-408">Nahrazení tokenu platí také pro názvy tras definované postupy atributů.</span><span class="sxs-lookup"><span data-stu-id="77c30-408">Token replacement also applies to route names defined by attribute routes.</span></span>
`[Route("[controller]/[action]", Name="[controller]_[action]")]`
<span data-ttu-id="77c30-409">generuje jedinečný název trasy pro každou akci.</span><span class="sxs-lookup"><span data-stu-id="77c30-409">generates a unique route name for each action.</span></span>

<span data-ttu-id="77c30-410">Chcete-li odpovídat oddělovač `[` `]`nahrazení literálu tokenu`[[` `]]`nebo , uniknout opakováním znaku ( nebo ).</span><span class="sxs-lookup"><span data-stu-id="77c30-410">To match the literal token replacement delimiter `[` or  `]`, escape it by repeating the character (`[[` or `]]`).</span></span>

<a name="routing-token-replacement-transformers-ref-label"></a>

### <a name="use-a-parameter-transformer-to-customize-token-replacement"></a><span data-ttu-id="77c30-411">Přizpůsobení nahrazení tokenu pomocí transformátoru parametrů</span><span class="sxs-lookup"><span data-stu-id="77c30-411">Use a parameter transformer to customize token replacement</span></span>

<span data-ttu-id="77c30-412">Token nahrazení lze přizpůsobit pomocí parametrtransformátoru.</span><span class="sxs-lookup"><span data-stu-id="77c30-412">Token replacement can be customized using a parameter transformer.</span></span> <span data-ttu-id="77c30-413">Parametr transformátor <xref:Microsoft.AspNetCore.Routing.IOutboundParameterTransformer> implementuje a transformuje hodnotu parametrů.</span><span class="sxs-lookup"><span data-stu-id="77c30-413">A parameter transformer implements <xref:Microsoft.AspNetCore.Routing.IOutboundParameterTransformer> and transforms the value of parameters.</span></span> <span data-ttu-id="77c30-414">Například vlastní `SlugifyParameterTransformer` parametr transformátor `SubscriptionManagement` změní `subscription-management`hodnotu trasy na :</span><span class="sxs-lookup"><span data-stu-id="77c30-414">For example, a custom `SlugifyParameterTransformer` parameter transformer changes the `SubscriptionManagement` route value to `subscription-management`:</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupSlugifyParamTransformer.cs?name=snippet2)]

<span data-ttu-id="77c30-415">Jedná <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention> se o konvence modelu aplikace, která:</span><span class="sxs-lookup"><span data-stu-id="77c30-415">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention> is an application model convention that:</span></span>

* <span data-ttu-id="77c30-416">Aplikuje parametr transformátor u všech tras atributů v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="77c30-416">Applies a parameter transformer to all attribute routes in an application.</span></span>
* <span data-ttu-id="77c30-417">Přizpůsobí hodnoty tokenu postupu atributu při jejich nahrazení.</span><span class="sxs-lookup"><span data-stu-id="77c30-417">Customizes the attribute route token values as they are replaced.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/SubscriptionManagementController.cs?name=snippet)]

<span data-ttu-id="77c30-418">Předchozí `ListAll` metoda odpovídá `/subscription-management/list-all`.</span><span class="sxs-lookup"><span data-stu-id="77c30-418">The preceding `ListAll` method matches `/subscription-management/list-all`.</span></span>

<span data-ttu-id="77c30-419">Je `RouteTokenTransformerConvention` registrován jako možnost `ConfigureServices`v .</span><span class="sxs-lookup"><span data-stu-id="77c30-419">The `RouteTokenTransformerConvention` is registered as an option in `ConfigureServices`.</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupSlugifyParamTransformer.cs?name=snippet)]

<span data-ttu-id="77c30-420">Viz [MDN webové dokumenty na Slimák](https://developer.mozilla.org/docs/Glossary/Slug) pro definici Slimák.</span><span class="sxs-lookup"><span data-stu-id="77c30-420">See [MDN web docs on Slug](https://developer.mozilla.org/docs/Glossary/Slug) for the definition of Slug.</span></span>

[!INCLUDE[](~/includes/regex.md)]
<a name="routing-multiple-routes-ref-label"></a>

### <a name="multiple-attribute-routes"></a><span data-ttu-id="77c30-421">Více tras atributů</span><span class="sxs-lookup"><span data-stu-id="77c30-421">Multiple attribute routes</span></span>

<span data-ttu-id="77c30-422">Směrování atributů podporuje definování více tras, které dosáhnou stejné akce.</span><span class="sxs-lookup"><span data-stu-id="77c30-422">Attribute routing supports defining multiple routes that reach the same action.</span></span> <span data-ttu-id="77c30-423">Nejběžnější použití tohoto je napodobovat chování výchozí konvenční trasy, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="77c30-423">The most common usage of this is to mimic the behavior of the default conventional route as shown in the following example:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet6x)]

<span data-ttu-id="77c30-424">Umístění více atributů trasy na kontroleru znamená, že každý z nich kombinuje s každým z atributů trasy na metody akce:</span><span class="sxs-lookup"><span data-stu-id="77c30-424">Putting multiple route attributes on the controller means that each one combines with each of the route attributes on the action methods:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet6)]

<span data-ttu-id="77c30-425">Implementují všechna omezení `IActionConstraint`trasy [slovesa HTTP](#verb) .</span><span class="sxs-lookup"><span data-stu-id="77c30-425">All the [HTTP verb](#verb) route constraints implement `IActionConstraint`.</span></span>

<span data-ttu-id="77c30-426">Při více atributy <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint> trasy, které implementují jsou umístěny na akci:</span><span class="sxs-lookup"><span data-stu-id="77c30-426">When multiple route attributes that implement <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint> are placed on an action:</span></span>

* <span data-ttu-id="77c30-427">Každé omezení akce se kombinuje se šablonou trasy použitou na řadič.</span><span class="sxs-lookup"><span data-stu-id="77c30-427">Each action constraint combines with the route template applied to the controller.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet7)]

<span data-ttu-id="77c30-428">Použití více tras na akce se může zdát užitečné a výkonné, je lepší zachovat adresu URL aplikace základní a dobře definované.</span><span class="sxs-lookup"><span data-stu-id="77c30-428">Using multiple routes on actions might seem useful and powerful, it's better to keep your app's URL space basic and well defined.</span></span> <span data-ttu-id="77c30-429">Více tras na akce **používejte pouze** v případě potřeby, například pro podporu stávajících klientů.</span><span class="sxs-lookup"><span data-stu-id="77c30-429">Use multiple routes on actions **only** where needed, for example, to support existing clients.</span></span>

<a name="routing-attr-options"></a>

### <a name="specifying-attribute-route-optional-parameters-default-values-and-constraints"></a><span data-ttu-id="77c30-430">Určení volitelných parametrů, výchozích hodnot a omezení postupu atributu</span><span class="sxs-lookup"><span data-stu-id="77c30-430">Specifying attribute route optional parameters, default values, and constraints</span></span>

<span data-ttu-id="77c30-431">Trasy atributů podporují stejnou vložkovou syntaxi jako konvenční trasy pro určení volitelných parametrů, výchozích hodnot a omezení.</span><span class="sxs-lookup"><span data-stu-id="77c30-431">Attribute routes support the same inline syntax as conventional routes to specify optional parameters, default values, and constraints.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet8&highlight=3)]

<span data-ttu-id="77c30-432">V předchozím kódu `[HttpPost("product/{id:int}")]` použije omezení postupu.</span><span class="sxs-lookup"><span data-stu-id="77c30-432">In the preceding code, `[HttpPost("product/{id:int}")]` applies a route constraint.</span></span> <span data-ttu-id="77c30-433">Akce `ProductsController.ShowProduct` je porovnána pouze podle `/product/3`adres URL, jako je .</span><span class="sxs-lookup"><span data-stu-id="77c30-433">The `ProductsController.ShowProduct` action is matched only by URL paths like `/product/3`.</span></span> <span data-ttu-id="77c30-434">Část `{id:int}` šablony trasy omezuje tento segment pouze na celá čísla.</span><span class="sxs-lookup"><span data-stu-id="77c30-434">The route template portion `{id:int}` constrains that segment to only integers.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet24)]

<span data-ttu-id="77c30-435">Podrobný popis syntaxe šablony trasy najdete v tématu [Odkaz na šablonu](xref:fundamentals/routing#route-template-reference) trasy.</span><span class="sxs-lookup"><span data-stu-id="77c30-435">See [Route Template Reference](xref:fundamentals/routing#route-template-reference) for a detailed description of route template syntax.</span></span>

<a name="routing-cust-rt-attr-irt-ref-label"></a>

### <a name="custom-route-attributes-using-iroutetemplateprovider"></a><span data-ttu-id="77c30-436">Vlastní atributy postupu pomocí iRouteTemplateProvider</span><span class="sxs-lookup"><span data-stu-id="77c30-436">Custom route attributes using IRouteTemplateProvider</span></span>

<span data-ttu-id="77c30-437">Všechny [atributy trasy](#rt) <xref:Microsoft.AspNetCore.Mvc.Routing.IRouteTemplateProvider>implementovat .</span><span class="sxs-lookup"><span data-stu-id="77c30-437">All of the [route attributes](#rt) implement <xref:Microsoft.AspNetCore.Mvc.Routing.IRouteTemplateProvider>.</span></span> <span data-ttu-id="77c30-438">ASP.NET Core runtime:</span><span class="sxs-lookup"><span data-stu-id="77c30-438">The ASP.NET Core runtime:</span></span>

* <span data-ttu-id="77c30-439">Vyhledá atributy na třídy kontroleru a metody akce při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="77c30-439">Looks for attributes on controller classes and action methods when the app starts.</span></span>
* <span data-ttu-id="77c30-440">Používá atributy, `IRouteTemplateProvider` které implementují k sestavení počáteční sady tras.</span><span class="sxs-lookup"><span data-stu-id="77c30-440">Uses the attributes that implement `IRouteTemplateProvider` to build the initial set of routes.</span></span>

<span data-ttu-id="77c30-441">Implementovat `IRouteTemplateProvider` definovat vlastní atributy trasy.</span><span class="sxs-lookup"><span data-stu-id="77c30-441">Implement `IRouteTemplateProvider` to define custom route attributes.</span></span> <span data-ttu-id="77c30-442">Každý `IRouteTemplateProvider` z nich umožňuje definovat jednu trasu s vlastní šablonou trasy, pořadím a názvem:</span><span class="sxs-lookup"><span data-stu-id="77c30-442">Each `IRouteTemplateProvider` allows you to define a single route with a custom route template, order, and name:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyTestApiController.cs?name=snippet&highlight=1-10)]

<span data-ttu-id="77c30-443">Předchozí `Get` metoda vrátí `Order = 2, Template = api/MyTestApi`.</span><span class="sxs-lookup"><span data-stu-id="77c30-443">The preceding `Get` method returns `Order = 2, Template = api/MyTestApi`.</span></span>

<a name="routing-app-model-ref-label"></a>

### <a name="use-application-model-to-customize-attribute-routes"></a><span data-ttu-id="77c30-444">Přizpůsobení tras atributů pomocí modelu aplikace</span><span class="sxs-lookup"><span data-stu-id="77c30-444">Use application model to customize attribute routes</span></span>

<span data-ttu-id="77c30-445">Aplikační model:</span><span class="sxs-lookup"><span data-stu-id="77c30-445">The application model:</span></span>

* <span data-ttu-id="77c30-446">Je objektový model vytvořen při spuštění.</span><span class="sxs-lookup"><span data-stu-id="77c30-446">Is an object model created at startup.</span></span>
* <span data-ttu-id="77c30-447">Obsahuje všechna metadata používaná ASP.NET Core k směrování a provádění akcí v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="77c30-447">Contains all of the metadata used by ASP.NET Core to route and execute the actions in an app.</span></span>

<span data-ttu-id="77c30-448">Aplikační model obsahuje všechna data shromážděná z atributů postupu.</span><span class="sxs-lookup"><span data-stu-id="77c30-448">The application model includes all of the data gathered from route attributes.</span></span> <span data-ttu-id="77c30-449">Data z atributů postupu jsou `IRouteTemplateProvider` poskytována implementací.</span><span class="sxs-lookup"><span data-stu-id="77c30-449">The data from route attributes is provided by the `IRouteTemplateProvider` implementation.</span></span> <span data-ttu-id="77c30-450">Úmluv:</span><span class="sxs-lookup"><span data-stu-id="77c30-450">Conventions:</span></span>

* <span data-ttu-id="77c30-451">Lze zapsat upravit model aplikace přizpůsobit, jak se chová směrování.</span><span class="sxs-lookup"><span data-stu-id="77c30-451">Can be written to modify the application model to customize how routing behaves.</span></span>
* <span data-ttu-id="77c30-452">Jsou čteny při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="77c30-452">Are read at app startup.</span></span>

<span data-ttu-id="77c30-453">Tato část ukazuje základní příklad přizpůsobení směrování pomocí aplikačního modelu.</span><span class="sxs-lookup"><span data-stu-id="77c30-453">This section shows a basic example of customizing routing using application model.</span></span> <span data-ttu-id="77c30-454">Následující kód umožňuje trasy zhruba zaokřovány se strukturou složek projektu.</span><span class="sxs-lookup"><span data-stu-id="77c30-454">The following code makes routes roughly line up with the folder structure of the project.</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/NamespaceRoutingConvention.cs?name=snippet)]

<span data-ttu-id="77c30-455">Následující kód zabraňuje `namespace` použití konvence pro řadiče, které jsou směrovány atribut:</span><span class="sxs-lookup"><span data-stu-id="77c30-455">The following code prevents the `namespace` convention from being applied to controllers that are attribute routed:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/NamespaceRoutingConvention.cs?name=snippet2)]

<span data-ttu-id="77c30-456">Například následující řadič nepoužívá `NamespaceRoutingConvention`:</span><span class="sxs-lookup"><span data-stu-id="77c30-456">For example, the following controller doesn't use `NamespaceRoutingConvention`:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/ManagersController.cs?name=snippet&highlight=1)]

<span data-ttu-id="77c30-457">Metoda: `NamespaceRoutingConvention.Apply`</span><span class="sxs-lookup"><span data-stu-id="77c30-457">The `NamespaceRoutingConvention.Apply` method:</span></span>

* <span data-ttu-id="77c30-458">Pokud je atribut směrován, neprovede nic.</span><span class="sxs-lookup"><span data-stu-id="77c30-458">Does nothing if the controller is attribute routed.</span></span>
* <span data-ttu-id="77c30-459">Nastaví šablonu řadičů `namespace`na základě `namespace` , přičemž základna je odebrána.</span><span class="sxs-lookup"><span data-stu-id="77c30-459">Sets the controllers template based on the `namespace`, with the base `namespace` removed.</span></span>

<span data-ttu-id="77c30-460">Lze `NamespaceRoutingConvention` použít v `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="77c30-460">The `NamespaceRoutingConvention` can be applied in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/Startup.cs?name=snippet&highlight=1,14-18)]

<span data-ttu-id="77c30-461">Zvažte například následující řadič:</span><span class="sxs-lookup"><span data-stu-id="77c30-461">For example, consider the following controller:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/UsersController.cs)]

<span data-ttu-id="77c30-462">V předchozím kódu:</span><span class="sxs-lookup"><span data-stu-id="77c30-462">In the preceding code:</span></span>

* <span data-ttu-id="77c30-463">Základna `namespace` `My.Application`je .</span><span class="sxs-lookup"><span data-stu-id="77c30-463">The base `namespace` is `My.Application`.</span></span>
* <span data-ttu-id="77c30-464">Úplný název předchozího řadiče `My.Application.Admin.Controllers.UsersController`je .</span><span class="sxs-lookup"><span data-stu-id="77c30-464">The full name of the preceding controller is `My.Application.Admin.Controllers.UsersController`.</span></span>
* <span data-ttu-id="77c30-465">Nastaví `NamespaceRoutingConvention` šablonu řadičů na `Admin/Controllers/Users/[action]/{id?`.</span><span class="sxs-lookup"><span data-stu-id="77c30-465">The `NamespaceRoutingConvention` sets the controllers template to `Admin/Controllers/Users/[action]/{id?`.</span></span>

<span data-ttu-id="77c30-466">Lze `NamespaceRoutingConvention` také použít jako atribut na řadiči:</span><span class="sxs-lookup"><span data-stu-id="77c30-466">The `NamespaceRoutingConvention` can also be applied as an attribute on a controller:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/TestController.cs?name=snippet&highlight=1)]

<a name="routing-mixed-ref-label"></a>

## <a name="mixed-routing-attribute-routing-vs-conventional-routing"></a><span data-ttu-id="77c30-467">Smíšené směrování: Směrování atributů vs konvenční směrování</span><span class="sxs-lookup"><span data-stu-id="77c30-467">Mixed routing: Attribute routing vs conventional routing</span></span>

<span data-ttu-id="77c30-468">ASP.NET aplikace Core mohou kombinovat použití konvenčního směrování a směrování atributů.</span><span class="sxs-lookup"><span data-stu-id="77c30-468">ASP.NET Core apps can mix the use of conventional routing and attribute routing.</span></span> <span data-ttu-id="77c30-469">Je typické používat konvenční trasy pro řadiče obsluhující stránky HTML pro prohlížeče a směrování atributů pro řadiče obsluhující rest API.</span><span class="sxs-lookup"><span data-stu-id="77c30-469">It's typical to use conventional routes for controllers serving HTML pages for browsers, and attribute routing for controllers serving REST APIs.</span></span>

<span data-ttu-id="77c30-470">Akce jsou buď konvenčně směrovány nebo atribut směrovány.</span><span class="sxs-lookup"><span data-stu-id="77c30-470">Actions are either conventionally routed or attribute routed.</span></span> <span data-ttu-id="77c30-471">Umístění trasy na řadič nebo akce způsobí, že atribut směrovány.</span><span class="sxs-lookup"><span data-stu-id="77c30-471">Placing a route on the controller or the action makes it attribute routed.</span></span> <span data-ttu-id="77c30-472">Akce, které definují trasy atributů nelze dosáhnout prostřednictvím konvenční trasy a naopak.</span><span class="sxs-lookup"><span data-stu-id="77c30-472">Actions that define attribute routes cannot be reached through the conventional routes and vice-versa.</span></span> <span data-ttu-id="77c30-473">**Libovolný** atribut trasy na řadiči provede **všechny** akce v atributu kontroleru směrovány.</span><span class="sxs-lookup"><span data-stu-id="77c30-473">**Any** route attribute on the controller makes **all** actions in the controller attribute routed.</span></span>

<span data-ttu-id="77c30-474">Směrování atributů a konvenční směrování používají stejný směrovací modul.</span><span class="sxs-lookup"><span data-stu-id="77c30-474">Attribute routing and conventional routing use the same routing engine.</span></span>

<a name="routing-url-gen-ref-label"></a>
<a name="ambient"></a>

## <a name="url-generation-and-ambient-values"></a><span data-ttu-id="77c30-475">Generování adres URL a okolní hodnoty</span><span class="sxs-lookup"><span data-stu-id="77c30-475">URL Generation and ambient values</span></span>

<span data-ttu-id="77c30-476">Aplikace můžou pomocí funkcí generování adres URL směrování generovat odkazy na adresy URL na akce.</span><span class="sxs-lookup"><span data-stu-id="77c30-476">Apps can use routing URL generation features to generate URL links to actions.</span></span> <span data-ttu-id="77c30-477">Generování adres URL eliminuje pevné kódovací adresy URL, takže kód je robustnější a udržovatelný.</span><span class="sxs-lookup"><span data-stu-id="77c30-477">Generating URLs eliminates hardcoding URLs, making code more robust and maintainable.</span></span> <span data-ttu-id="77c30-478">Tato část se zaměřuje na funkce generování adres URL poskytované MVC a pouze pokrývají základy fungování generování adres URL.</span><span class="sxs-lookup"><span data-stu-id="77c30-478">This section focuses on the URL generation features provided by MVC and only cover basics of how URL generation works.</span></span> <span data-ttu-id="77c30-479">Podrobný popis generování adres URL naleznete v tématu [Směrování.](xref:fundamentals/routing)</span><span class="sxs-lookup"><span data-stu-id="77c30-479">See [Routing](xref:fundamentals/routing) for a detailed description of URL generation.</span></span>

<span data-ttu-id="77c30-480">Rozhraní <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> je základním prvkem infrastruktury mezi MVC a směrování pro generování adres URL.</span><span class="sxs-lookup"><span data-stu-id="77c30-480">The <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> interface is the underlying element of infrastructure between MVC and routing for URL generation.</span></span> <span data-ttu-id="77c30-481">Instance je `IUrlHelper` k dispozici `Url` prostřednictvím vlastnosti v kontrolecích, zobrazeních a součástech zobrazení.</span><span class="sxs-lookup"><span data-stu-id="77c30-481">An instance of `IUrlHelper` is available through the `Url` property in controllers, views, and view components.</span></span>

<span data-ttu-id="77c30-482">V následujícím příkladu `IUrlHelper` rozhraní se `Controller.Url` používá prostřednictvím vlastnosti ke generování adresy URL na jinou akci.</span><span class="sxs-lookup"><span data-stu-id="77c30-482">In the following example, the `IUrlHelper` interface is used through the `Controller.Url` property to generate a URL to another action.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGenerationController.cs?name=snippet_1)]

<span data-ttu-id="77c30-483">Pokud aplikace používá výchozí konvenční trasu, `url` hodnota proměnné je `/UrlGeneration/Destination`řetězec cesty URL .</span><span class="sxs-lookup"><span data-stu-id="77c30-483">If the app is using the default conventional route, the value of the `url` variable is the URL path string `/UrlGeneration/Destination`.</span></span> <span data-ttu-id="77c30-484">Tato cesta URL je vytvořena směrováním kombinací:</span><span class="sxs-lookup"><span data-stu-id="77c30-484">This URL path is created by routing by combining:</span></span>

* <span data-ttu-id="77c30-485">Hodnoty trasy z aktuálního požadavku, které se nazývají **hodnoty okolí**.</span><span class="sxs-lookup"><span data-stu-id="77c30-485">The route values from the current request, which are called **ambient values**.</span></span>
* <span data-ttu-id="77c30-486">Hodnoty předané `Url.Action` a nahrazující tyto hodnoty do šablony postupu:</span><span class="sxs-lookup"><span data-stu-id="77c30-486">The values passed to `Url.Action` and substituting those values into the route template:</span></span>

``` text
ambient values: { controller = "UrlGeneration", action = "Source" }
values passed to Url.Action: { controller = "UrlGeneration", action = "Destination" }
route template: {controller}/{action}/{id?}

result: /UrlGeneration/Destination
```

<span data-ttu-id="77c30-487">Každý parametr trasy v šabloně trasy má svou hodnotu nahrazenou sladěnými názvy s hodnotami a hodnotami okolí.</span><span class="sxs-lookup"><span data-stu-id="77c30-487">Each route parameter in the route template has its value substituted by matching names with the values and ambient values.</span></span> <span data-ttu-id="77c30-488">Parametr trasy, který nemá hodnotu, může:</span><span class="sxs-lookup"><span data-stu-id="77c30-488">A route parameter that doesn't have a value can:</span></span>

* <span data-ttu-id="77c30-489">Výchozí hodnotu použijte, pokud ji obsahuje.</span><span class="sxs-lookup"><span data-stu-id="77c30-489">Use a default value if it has one.</span></span>
* <span data-ttu-id="77c30-490">Pokud je to volitelné, nechte se přeskočit.</span><span class="sxs-lookup"><span data-stu-id="77c30-490">Be skipped if it's optional.</span></span> <span data-ttu-id="77c30-491">Například šablona `id` z `{controller}/{action}/{id?}`trasy .</span><span class="sxs-lookup"><span data-stu-id="77c30-491">For example, the `id` from the  route template `{controller}/{action}/{id?}`.</span></span>

<span data-ttu-id="77c30-492">Generování adresy URL se nezdaří, pokud žádný parametr požadované trasy nemá odpovídající hodnotu.</span><span class="sxs-lookup"><span data-stu-id="77c30-492">URL generation fails if any required route parameter doesn't have a corresponding value.</span></span> <span data-ttu-id="77c30-493">Pokud se generování adresy URL nezdaří pro trasu, další trasa je vyzkoušena, dokud nebyly vyzkoušeny všechny trasy nebo nalezena shoda.</span><span class="sxs-lookup"><span data-stu-id="77c30-493">If URL generation fails for a route, the next route is tried until all routes have been tried or a match is found.</span></span>

<span data-ttu-id="77c30-494">Předchozí příklad `Url.Action` předpokládá konvenční [směrování](#cr).</span><span class="sxs-lookup"><span data-stu-id="77c30-494">The preceding example of `Url.Action` assumes [conventional routing](#cr).</span></span> <span data-ttu-id="77c30-495">Generování adres URL funguje podobně s [směrováním atributů](#ar), i když koncepty se liší.</span><span class="sxs-lookup"><span data-stu-id="77c30-495">URL generation works similarly with [attribute routing](#ar), though the concepts are different.</span></span> <span data-ttu-id="77c30-496">S konvenčním směrováním:</span><span class="sxs-lookup"><span data-stu-id="77c30-496">With conventional routing:</span></span>

* <span data-ttu-id="77c30-497">Hodnoty trasy se používají k rozbalení šablony.</span><span class="sxs-lookup"><span data-stu-id="77c30-497">The route values are used to expand a template.</span></span>
* <span data-ttu-id="77c30-498">Hodnoty trasy `controller` pro `action` a obvykle se zobrazí v této šabloně.</span><span class="sxs-lookup"><span data-stu-id="77c30-498">The route values for `controller` and `action` usually appear in that template.</span></span> <span data-ttu-id="77c30-499">To funguje, protože adresy URL odpovídající směrování dodržovat konvence.</span><span class="sxs-lookup"><span data-stu-id="77c30-499">This works because the URLs matched by routing adhere to a convention.</span></span>

<span data-ttu-id="77c30-500">Následující příklad používá směrování atributů:</span><span class="sxs-lookup"><span data-stu-id="77c30-500">The following example uses attribute routing:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGenerationAttrController.cs?name=snippet_1)]

<span data-ttu-id="77c30-501">Akce `Source` v předchozím kódu generuje `custom/url/to/destination`.</span><span class="sxs-lookup"><span data-stu-id="77c30-501">The `Source` action in the preceding code generates `custom/url/to/destination`.</span></span>

<span data-ttu-id="77c30-502"><xref:Microsoft.AspNetCore.Routing.LinkGenerator>byla přidána v ASP.NET Core 3.0 jako alternativu k `IUrlHelper`.</span><span class="sxs-lookup"><span data-stu-id="77c30-502"><xref:Microsoft.AspNetCore.Routing.LinkGenerator> was added in ASP.NET Core 3.0 as an alternative to `IUrlHelper`.</span></span> <span data-ttu-id="77c30-503">`LinkGenerator`nabízí podobné, ale flexibilnější funkce.</span><span class="sxs-lookup"><span data-stu-id="77c30-503">`LinkGenerator` offers similar but more flexible functionality.</span></span> <span data-ttu-id="77c30-504">Každá metoda `IUrlHelper` má odpovídající rodinu `LinkGenerator` metod na stejně.</span><span class="sxs-lookup"><span data-stu-id="77c30-504">Each method on `IUrlHelper` has a corresponding family of methods on `LinkGenerator` as well.</span></span>

### <a name="generating-urls-by-action-name"></a><span data-ttu-id="77c30-505">Generování adres URL podle názvu akce</span><span class="sxs-lookup"><span data-stu-id="77c30-505">Generating URLs by action name</span></span>

<span data-ttu-id="77c30-506">[Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*), [LinkGenerator.GetPathByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetPathByAction*)a všechna související přetížení jsou navrženy tak, aby generovaly cílový koncový bod zadáním názvu řadiče a názvu akce.</span><span class="sxs-lookup"><span data-stu-id="77c30-506">[Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*), [LinkGenerator.GetPathByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetPathByAction*), and all related overloads all are designed to generate the target endpoint by specifying a controller name and action name.</span></span>

<span data-ttu-id="77c30-507">Při `Url.Action`použití jsou aktuální `controller` hodnoty `action` trasy pro runtime a jsou poskytovány:</span><span class="sxs-lookup"><span data-stu-id="77c30-507">When using `Url.Action`, the current route values for `controller` and `action` are provided by the runtime:</span></span>

* <span data-ttu-id="77c30-508">Hodnota `controller` a `action` jsou součástí [hodnot okolí](#ambient) i hodnot.</span><span class="sxs-lookup"><span data-stu-id="77c30-508">The value of `controller` and `action` are part of both [ambient values](#ambient) and values.</span></span> <span data-ttu-id="77c30-509">Metoda `Url.Action` vždy používá aktuální `action` hodnoty `controller` a generuje cestu URL, která vede k aktuální akci.</span><span class="sxs-lookup"><span data-stu-id="77c30-509">The method `Url.Action` always uses the current values of `action` and `controller` and generates a URL path that routes to the current action.</span></span>

<span data-ttu-id="77c30-510">Směrování se pokusí použít hodnoty v okolních hodnotách k vyplnění informací, které nebyly poskytnuty při generování adresy URL.</span><span class="sxs-lookup"><span data-stu-id="77c30-510">Routing attempts to use the values in ambient values to fill in information that wasn't provided when generating a URL.</span></span> <span data-ttu-id="77c30-511">Zvažte trasu `{a}/{b}/{c}/{d}` jako `{ a = Alice, b = Bob, c = Carol, d = David }`s okolními hodnotami :</span><span class="sxs-lookup"><span data-stu-id="77c30-511">Consider a route like `{a}/{b}/{c}/{d}` with ambient values `{ a = Alice, b = Bob, c = Carol, d = David }`:</span></span>

* <span data-ttu-id="77c30-512">Směrování má dostatek informací pro generování adresy URL bez dalších hodnot.</span><span class="sxs-lookup"><span data-stu-id="77c30-512">Routing has enough information to generate a URL without any additional values.</span></span>
* <span data-ttu-id="77c30-513">Směrování má dostatek informací, protože všechny parametry trasy mají hodnotu.</span><span class="sxs-lookup"><span data-stu-id="77c30-513">Routing has enough information because all route parameters have a value.</span></span>

<span data-ttu-id="77c30-514">Pokud je `{ d = Donovan }` přidaná hodnota:</span><span class="sxs-lookup"><span data-stu-id="77c30-514">If the value `{ d = Donovan }` is added:</span></span>

* <span data-ttu-id="77c30-515">Hodnota `{ d = David }` je ignorována.</span><span class="sxs-lookup"><span data-stu-id="77c30-515">The value `{ d = David }` is ignored.</span></span>
* <span data-ttu-id="77c30-516">Vygenerovaná `Alice/Bob/Carol/Donovan`cesta URL je .</span><span class="sxs-lookup"><span data-stu-id="77c30-516">The generated URL path is `Alice/Bob/Carol/Donovan`.</span></span>

<span data-ttu-id="77c30-517">**Upozornění**: Cesty adres URL jsou hierarchické.</span><span class="sxs-lookup"><span data-stu-id="77c30-517">**Warning**: URL paths are hierarchical.</span></span> <span data-ttu-id="77c30-518">V předchozím příkladu, pokud `{ c = Cheryl }` je přidána hodnota:</span><span class="sxs-lookup"><span data-stu-id="77c30-518">In the preceding example, if the value `{ c = Cheryl }` is added:</span></span>

* <span data-ttu-id="77c30-519">Obě hodnoty `{ c = Carol, d = David }` jsou ignorovány.</span><span class="sxs-lookup"><span data-stu-id="77c30-519">Both of the values `{ c = Carol, d = David }` are ignored.</span></span>
* <span data-ttu-id="77c30-520">Již neexistuje hodnota pro `d` a generování adresy URL se nezdaří.</span><span class="sxs-lookup"><span data-stu-id="77c30-520">There is no longer a value for `d` and URL generation fails.</span></span>
* <span data-ttu-id="77c30-521">Požadované hodnoty `c` a `d` musí být zadány pro generování adresy URL.</span><span class="sxs-lookup"><span data-stu-id="77c30-521">The desired values of `c` and `d` must be specified to generate a URL.</span></span>  

<span data-ttu-id="77c30-522">Můžete očekávat, že tento problém `{controller}/{action}/{id?}`s výchozí trasou .</span><span class="sxs-lookup"><span data-stu-id="77c30-522">You might expect to hit this problem with the default route `{controller}/{action}/{id?}`.</span></span> <span data-ttu-id="77c30-523">Tento problém je v `Url.Action` praxi vzácný, `controller` `action` protože vždy explicitně určuje hodnotu a.</span><span class="sxs-lookup"><span data-stu-id="77c30-523">This problem is rare in practice because `Url.Action` always explicitly specifies a `controller` and `action` value.</span></span>

<span data-ttu-id="77c30-524">Několik přetížení [Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) trvat objekt hodnoty trasy poskytnout hodnoty `controller` pro `action`parametry trasy než a .</span><span class="sxs-lookup"><span data-stu-id="77c30-524">Several overloads of [Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) take a route values object to provide values for route parameters other than `controller` and `action`.</span></span> <span data-ttu-id="77c30-525">Objekt hodnot trasy se často `id`používá s .</span><span class="sxs-lookup"><span data-stu-id="77c30-525">The route values object is frequently used with `id`.</span></span> <span data-ttu-id="77c30-526">Například, `Url.Action("Buy", "Products", new { id = 17 })`.</span><span class="sxs-lookup"><span data-stu-id="77c30-526">For example, `Url.Action("Buy", "Products", new { id = 17 })`.</span></span> <span data-ttu-id="77c30-527">Objekt hodnot trasy:</span><span class="sxs-lookup"><span data-stu-id="77c30-527">The route values object:</span></span>

* <span data-ttu-id="77c30-528">Podle konvence je obvykle objekt anonymního typu.</span><span class="sxs-lookup"><span data-stu-id="77c30-528">By convention is usually an object of anonymous type.</span></span>
* <span data-ttu-id="77c30-529">Může být `IDictionary<>` nebo [POCO](https://wikipedia.org/wiki/Plain_old_CLR_object)).</span><span class="sxs-lookup"><span data-stu-id="77c30-529">Can be an `IDictionary<>` or a [POCO](https://wikipedia.org/wiki/Plain_old_CLR_object)).</span></span>

<span data-ttu-id="77c30-530">Všechny další hodnoty trasy, které neodpovídají parametrům trasy, jsou vloženy do řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="77c30-530">Any additional route values that don't match route parameters are put in the query string.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/TestController.cs?name=snippet)]

<span data-ttu-id="77c30-531">Předchozí kód generuje `/Products/Buy/17?color=red`.</span><span class="sxs-lookup"><span data-stu-id="77c30-531">The preceding code generates `/Products/Buy/17?color=red`.</span></span>

<span data-ttu-id="77c30-532">Následující kód generuje absolutní adresu URL:</span><span class="sxs-lookup"><span data-stu-id="77c30-532">The following code generates an absolute URL:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/TestController.cs?name=snippet2)]

<span data-ttu-id="77c30-533">Chcete-li vytvořit absolutní adresu URL, použijte jednu z následujících možností:</span><span class="sxs-lookup"><span data-stu-id="77c30-533">To create an absolute URL, use one of the following:</span></span>

* <span data-ttu-id="77c30-534">Přetížení, který přijímá `protocol`.</span><span class="sxs-lookup"><span data-stu-id="77c30-534">An overload that accepts a `protocol`.</span></span> <span data-ttu-id="77c30-535">Například předchozí kód.</span><span class="sxs-lookup"><span data-stu-id="77c30-535">For example, the preceding code.</span></span>
* <span data-ttu-id="77c30-536">[LinkGenerator.GetUriByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetUriByAction*), který ve výchozím nastavení generuje absolutní identifikátory URI.</span><span class="sxs-lookup"><span data-stu-id="77c30-536">[LinkGenerator.GetUriByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetUriByAction*), which generates absolute URIs by default.</span></span>

<a name="routing-gen-urls-route-ref-label"></a>

### <a name="generate-urls-by-route"></a><span data-ttu-id="77c30-537">Generovat adresy URL podle trasy</span><span class="sxs-lookup"><span data-stu-id="77c30-537">Generate URLs by route</span></span>

<span data-ttu-id="77c30-538">Předchozí kód demonstroval generování adresy URL předáním v řadiči a název akce.</span><span class="sxs-lookup"><span data-stu-id="77c30-538">The preceding code demonstrated generating a URL by passing in the controller and action name.</span></span> <span data-ttu-id="77c30-539">`IUrlHelper`poskytuje také [url.RouteUrl](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.RouteUrl*) rodina metod.</span><span class="sxs-lookup"><span data-stu-id="77c30-539">`IUrlHelper` also provides the [Url.RouteUrl](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.RouteUrl*) family of methods.</span></span> <span data-ttu-id="77c30-540">Tyto metody jsou podobné [Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*), ale nekopírují aktuální hodnoty `action` a `controller` hodnoty postupu.</span><span class="sxs-lookup"><span data-stu-id="77c30-540">These methods are similar to [Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*), but they don't copy the current values of `action` and `controller` to the route values.</span></span> <span data-ttu-id="77c30-541">Nejběžnější použití `Url.RouteUrl`:</span><span class="sxs-lookup"><span data-stu-id="77c30-541">The most common usage of `Url.RouteUrl`:</span></span>

* <span data-ttu-id="77c30-542">Určuje název trasy pro generování adresy URL.</span><span class="sxs-lookup"><span data-stu-id="77c30-542">Specifies a route name to generate the URL.</span></span>
* <span data-ttu-id="77c30-543">Obecně neurčuje název řadiče nebo akce.</span><span class="sxs-lookup"><span data-stu-id="77c30-543">Generally doesn't specify a controller or action name.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGeneration2Controller.cs?name=snippet_1)]

<span data-ttu-id="77c30-544">Následující soubor Razor generuje odkaz HTML `Destination_Route`na :</span><span class="sxs-lookup"><span data-stu-id="77c30-544">The following Razor file generates an HTML link to the `Destination_Route`:</span></span>

[!code-cshtml[](routing/samples/3.x/main/Views/Shared/MyLink.cshtml)]

<a name="routing-gen-urls-html-ref-label"></a>

### <a name="generate-urls-in-html-and-razor"></a><span data-ttu-id="77c30-545">Generování adres URL v HTML a Razor</span><span class="sxs-lookup"><span data-stu-id="77c30-545">Generate URLs in HTML and Razor</span></span>

<span data-ttu-id="77c30-546"><xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper>poskytuje <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.HtmlHelper> metody [Html.BeginForm](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.BeginForm*) a [Html.ActionLink](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.ActionLink*) pro generování `<form>` a `<a>` prvky v uvedeném pořadí.</span><span class="sxs-lookup"><span data-stu-id="77c30-546"><xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper> provides the <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.HtmlHelper> methods [Html.BeginForm](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.BeginForm*) and [Html.ActionLink](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.ActionLink*) to generate `<form>` and `<a>` elements respectively.</span></span> <span data-ttu-id="77c30-547">Tyto metody používají metodu [Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) ke generování adresy URL a přijímají podobné argumenty.</span><span class="sxs-lookup"><span data-stu-id="77c30-547">These methods use the [Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) method to generate a URL and they accept similar arguments.</span></span> <span data-ttu-id="77c30-548">`Url.RouteUrl` Společníci `HtmlHelper` `Html.BeginRouteForm` pro `Html.RouteLink` jsou a které mají podobné funkce.</span><span class="sxs-lookup"><span data-stu-id="77c30-548">The `Url.RouteUrl` companions for `HtmlHelper` are `Html.BeginRouteForm` and `Html.RouteLink` which have similar functionality.</span></span>

<span data-ttu-id="77c30-549">TagHelpers generovat adresy URL `form` prostřednictvím TagHelper a `<a>` TagHelper.</span><span class="sxs-lookup"><span data-stu-id="77c30-549">TagHelpers generate URLs through the `form` TagHelper and the `<a>` TagHelper.</span></span> <span data-ttu-id="77c30-550">Obě tyto `IUrlHelper` použití pro jejich realizaci.</span><span class="sxs-lookup"><span data-stu-id="77c30-550">Both of these use `IUrlHelper` for their implementation.</span></span> <span data-ttu-id="77c30-551">Další informace naleznete [v tématu Tag Helpers ve formulářích.](xref:mvc/views/working-with-forms)</span><span class="sxs-lookup"><span data-stu-id="77c30-551">See [Tag Helpers in forms](xref:mvc/views/working-with-forms) for more information.</span></span>

<span data-ttu-id="77c30-552">Vnitřní zobrazení, `IUrlHelper` je k `Url` dispozici prostřednictvím vlastnosti pro všechny ad-hoc URL generace, které nejsou zahrnuty výše.</span><span class="sxs-lookup"><span data-stu-id="77c30-552">Inside views, the `IUrlHelper` is available through the `Url` property for any ad-hoc URL generation not covered by the above.</span></span>

<a name="routing-gen-urls-action-ref-label"></a>

### <a name="url-generation-in-action-results"></a><span data-ttu-id="77c30-553">Generování adresy URL ve výsledcích akce</span><span class="sxs-lookup"><span data-stu-id="77c30-553">URL generation in Action Results</span></span>

<span data-ttu-id="77c30-554">Předchozí příklady ukázaly `IUrlHelper` použití v kontroleru.</span><span class="sxs-lookup"><span data-stu-id="77c30-554">The preceding examples showed using `IUrlHelper` in a controller.</span></span> <span data-ttu-id="77c30-555">Nejběžnější použití v řadiči je generovat adresu URL jako součást výsledku akce.</span><span class="sxs-lookup"><span data-stu-id="77c30-555">The most common usage in a controller is to generate a URL as part of an action result.</span></span>

<span data-ttu-id="77c30-556">A <xref:Microsoft.AspNetCore.Mvc.ControllerBase> <xref:Microsoft.AspNetCore.Mvc.Controller> základní třídy poskytují komfortní metody pro výsledky akce, které odkazují na jinou akci.</span><span class="sxs-lookup"><span data-stu-id="77c30-556">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase> and <xref:Microsoft.AspNetCore.Mvc.Controller> base classes provide convenience methods for action results that reference another action.</span></span> <span data-ttu-id="77c30-557">Jedním z typických použití je přesměrování po přijetí vstupu uživatele:</span><span class="sxs-lookup"><span data-stu-id="77c30-557">One typical usage is to redirect after accepting user input:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/CustomerController.cs?name=snippet)]

<span data-ttu-id="77c30-558">Akce výsledky factory metody, jako <xref:Microsoft.AspNetCore.Mvc.ControllerBase.RedirectToAction*> je například a <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> postupujte podobný vzor metody na `IUrlHelper`.</span><span class="sxs-lookup"><span data-stu-id="77c30-558">The action results factory methods such as <xref:Microsoft.AspNetCore.Mvc.ControllerBase.RedirectToAction*> and <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> follow a similar pattern to the methods on `IUrlHelper`.</span></span>

<a name="routing-dedicated-ref-label"></a>

### <a name="special-case-for-dedicated-conventional-routes"></a><span data-ttu-id="77c30-559">Zvláštní případ pro vyhrazené konvenční trasy</span><span class="sxs-lookup"><span data-stu-id="77c30-559">Special case for dedicated conventional routes</span></span>

<span data-ttu-id="77c30-560">[Konvenční trasa](#cr) může používat speciální druh definice trasy nazývanou [vyhrazená konvenční trasa](#dcr).</span><span class="sxs-lookup"><span data-stu-id="77c30-560">[Conventional routing](#cr) can use a special kind of route definition called a [dedicated conventional route](#dcr).</span></span> <span data-ttu-id="77c30-561">V následujícím příkladu je `blog` trasa s názvem vyhrazená konvenční trasa:</span><span class="sxs-lookup"><span data-stu-id="77c30-561">In the following example, the route named `blog` is a dedicated conventional route:</span></span>

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

<span data-ttu-id="77c30-562">Pomocí předchozích definic tras `Url.Action("Index", "Home")` vygeneruje `/` cestu `default` URL pomocí trasy, ale proč?</span><span class="sxs-lookup"><span data-stu-id="77c30-562">Using the preceding route definitions, `Url.Action("Index", "Home")` generates the URL path `/` using the `default` route, but why?</span></span> <span data-ttu-id="77c30-563">Můžete uhodnout, `{ controller = Home, action = Index }` že hodnoty trasy by `blog`stačily ke generování `/blog?action=Index&controller=Home`adresy URL pomocí a výsledkem by bylo .</span><span class="sxs-lookup"><span data-stu-id="77c30-563">You might guess the route values `{ controller = Home, action = Index }` would be enough to generate a URL using `blog`, and the result would be `/blog?action=Index&controller=Home`.</span></span>

<span data-ttu-id="77c30-564">[Vyhrazené konvenční trasy](#dcr) spoléhají na zvláštní chování výchozích hodnot, které nemají odpovídající parametr trasy, který brání tomu, aby trasa byla příliš hladová s [generováním](xref:fundamentals/routing#greedy) adresy URL.</span><span class="sxs-lookup"><span data-stu-id="77c30-564">[Dedicated conventional routes](#dcr) rely on a special behavior of default values that don't have a corresponding route parameter that prevents the route from being too [greedy](xref:fundamentals/routing#greedy) with URL generation.</span></span> <span data-ttu-id="77c30-565">V tomto případě jsou `{ controller = Blog, action = Article }`výchozí hodnoty `controller` `action` a jako parametr trasy se nezobrazují ani se nezobrazují.</span><span class="sxs-lookup"><span data-stu-id="77c30-565">In this case the default values are `{ controller = Blog, action = Article }`, and neither `controller` nor `action` appears as a route parameter.</span></span> <span data-ttu-id="77c30-566">Při směrování provádí generování adresy URL, zadaná hodnota musí odpovídat výchozí hodnoty.</span><span class="sxs-lookup"><span data-stu-id="77c30-566">When routing performs URL generation, the values provided must match the default values.</span></span> <span data-ttu-id="77c30-567">Generování adresy `blog` URL pomocí `{ controller = Home, action = Index }` selže, `{ controller = Blog, action = Article }`protože hodnoty se neshodují .</span><span class="sxs-lookup"><span data-stu-id="77c30-567">URL generation using `blog` fails because the values `{ controller = Home, action = Index }` don't match `{ controller = Blog, action = Article }`.</span></span> <span data-ttu-id="77c30-568">Směrování pak přejde `default`zpět vyzkoušet , který je úspěšný.</span><span class="sxs-lookup"><span data-stu-id="77c30-568">Routing then falls back to try `default`, which succeeds.</span></span>

<a name="routing-areas-ref-label"></a>

## <a name="areas"></a><span data-ttu-id="77c30-569">Oblasti</span><span class="sxs-lookup"><span data-stu-id="77c30-569">Areas</span></span>

<span data-ttu-id="77c30-570">[Oblasti](xref:mvc/controllers/areas) jsou funkce MVC, která slouží k uspořádání souvisejících funkcí do skupiny jako samostatná:</span><span class="sxs-lookup"><span data-stu-id="77c30-570">[Areas](xref:mvc/controllers/areas) are an MVC feature used to organize related functionality into a group as a separate:</span></span>

* <span data-ttu-id="77c30-571">Směrování oboru názvů pro akce řadiče.</span><span class="sxs-lookup"><span data-stu-id="77c30-571">Routing namespace for controller actions.</span></span>
* <span data-ttu-id="77c30-572">Struktura složek pro zobrazení.</span><span class="sxs-lookup"><span data-stu-id="77c30-572">Folder structure for views.</span></span>

<span data-ttu-id="77c30-573">Použití oblastí umožňuje aplikaci mít více řadičů se stejným názvem, pokud mají různé oblasti.</span><span class="sxs-lookup"><span data-stu-id="77c30-573">Using areas allows an app to have multiple controllers with the same name, as long as they have different areas.</span></span> <span data-ttu-id="77c30-574">Použití oblastí vytvoří hierarchii pro účely směrování přidáním `controller` `action`dalšího parametru trasy `area` do a .</span><span class="sxs-lookup"><span data-stu-id="77c30-574">Using areas creates a hierarchy for the purpose of routing by adding another route parameter, `area` to `controller` and `action`.</span></span> <span data-ttu-id="77c30-575">Tato část popisuje, jak směrování spolupracuje s oblastmi.</span><span class="sxs-lookup"><span data-stu-id="77c30-575">This section discusses how routing interacts with areas.</span></span> <span data-ttu-id="77c30-576">Podrobnosti o tom, jak se oblasti používají s pohledy, najdete v článku [Oblasti.](xref:mvc/controllers/areas)</span><span class="sxs-lookup"><span data-stu-id="77c30-576">See [Areas](xref:mvc/controllers/areas) for details about how areas are used with views.</span></span>

<span data-ttu-id="77c30-577">Následující příklad konfiguruje mvc pro `area` použití výchozí `area` `Blog`konvenční trasy a trasu pro pojmenované :</span><span class="sxs-lookup"><span data-stu-id="77c30-577">The following example configures MVC to use the default conventional route and an `area` route for an `area` named `Blog`:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet1)]

<span data-ttu-id="77c30-578">V předchozím kódu <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> je volána `"blog_route"`k vytvoření .</span><span class="sxs-lookup"><span data-stu-id="77c30-578">In the preceding code, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> is called to create the `"blog_route"`.</span></span> <span data-ttu-id="77c30-579">Druhý parametr `"Blog"`, je název oblasti.</span><span class="sxs-lookup"><span data-stu-id="77c30-579">The second parameter, `"Blog"`, is the area name.</span></span>

<span data-ttu-id="77c30-580">Při porovnávání cesty `/Manage/Users/AddUser`URL `"blog_route"` , jako je `{ area = Blog, controller = Users, action = AddUser }`trasa, vygeneruje hodnoty trasy .</span><span class="sxs-lookup"><span data-stu-id="77c30-580">When matching a URL path like `/Manage/Users/AddUser`, the `"blog_route"` route generates the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span> <span data-ttu-id="77c30-581">Hodnota `area` postupu je vytvořena výchozí `area`hodnotou pro aplikace .</span><span class="sxs-lookup"><span data-stu-id="77c30-581">The `area` route value is produced by a default value for `area`.</span></span> <span data-ttu-id="77c30-582">Trasa vytvořená pomocí `MapAreaControllerRoute` je ekvivalentní následujícímu:</span><span class="sxs-lookup"><span data-stu-id="77c30-582">The route created by `MapAreaControllerRoute` is equivalent to the following:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup2.cs?name=snippet2)]

<span data-ttu-id="77c30-583">`MapAreaControllerRoute`vytvoří trasu pomocí výchozí hodnoty `area` i omezení pro použití zadaný `Blog`název oblasti, v tomto případě .</span><span class="sxs-lookup"><span data-stu-id="77c30-583">`MapAreaControllerRoute` creates a route using both a default value and constraint for `area` using the provided area name, in this case `Blog`.</span></span> <span data-ttu-id="77c30-584">Výchozí hodnota zajišťuje, že trasa `{ area = Blog, ... }`vždy vytváří , `{ area = Blog, ... }` omezení vyžaduje hodnotu pro generování adresy URL.</span><span class="sxs-lookup"><span data-stu-id="77c30-584">The default value ensures that the route always produces `{ area = Blog, ... }`, the constraint requires the value `{ area = Blog, ... }` for URL generation.</span></span>

<span data-ttu-id="77c30-585">Konvenční směrování je závislé na objednávce.</span><span class="sxs-lookup"><span data-stu-id="77c30-585">Conventional routing is order-dependent.</span></span> <span data-ttu-id="77c30-586">Obecně platí, že trasy s oblastmi by měly být umístěny dříve, protože jsou konkrétnější než trasy bez oblasti.</span><span class="sxs-lookup"><span data-stu-id="77c30-586">In general, routes with areas should be placed earlier as they're more specific than routes without an area.</span></span>

<span data-ttu-id="77c30-587">V předchozím příkladu odpovídají `{ area = Blog, controller = Users, action = AddUser }` hodnoty trasy následující akci:</span><span class="sxs-lookup"><span data-stu-id="77c30-587">Using the preceding example, the route values `{ area = Blog, controller = Users, action = AddUser }` match the following action:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

<span data-ttu-id="77c30-588">Atribut [[Oblast]](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) je to, co označuje řadič jako součást oblasti.</span><span class="sxs-lookup"><span data-stu-id="77c30-588">The [[Area]](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) attribute is what denotes a controller as part of an area.</span></span> <span data-ttu-id="77c30-589">Tento ovladač je `Blog` v oblasti.</span><span class="sxs-lookup"><span data-stu-id="77c30-589">This controller is in the `Blog` area.</span></span> <span data-ttu-id="77c30-590">Řadiče bez `[Area]` atributu nejsou členy žádné oblasti a `area` **neshodují** se, pokud je hodnota trasy poskytnuta směrováním.</span><span class="sxs-lookup"><span data-stu-id="77c30-590">Controllers without an `[Area]` attribute are not members of any area, and do **not** match when the `area` route value is provided by routing.</span></span> <span data-ttu-id="77c30-591">V následujícím příkladu může hodnoty `{ area = Blog, controller = Users, action = AddUser }`trasy odpovídat pouze prvnímu uvedenému řadiči .</span><span class="sxs-lookup"><span data-stu-id="77c30-591">In the following example, only the first controller listed can match the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Zebra/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/UsersController.cs)]

<span data-ttu-id="77c30-592">Obor názvů každého řadiče je zde zobrazen pro úplnost.</span><span class="sxs-lookup"><span data-stu-id="77c30-592">The namespace of each controller is shown here for completeness.</span></span> <span data-ttu-id="77c30-593">Pokud předchozí řadiče používá stejný obor názvů, bude generována chyba kompilátoru.</span><span class="sxs-lookup"><span data-stu-id="77c30-593">If the preceding controllers uses the same namespace, a compiler error would be generated.</span></span> <span data-ttu-id="77c30-594">Obory názvů tříd nemají žádný vliv na směrování MVC.</span><span class="sxs-lookup"><span data-stu-id="77c30-594">Class namespaces have no effect on MVC's routing.</span></span>

<span data-ttu-id="77c30-595">První dva řadiče jsou členy oblastí a shodují se pouze v `area` případě, že jejich název oblasti je zajišťován hodnotou trasy.</span><span class="sxs-lookup"><span data-stu-id="77c30-595">The first two controllers are members of areas, and only match when their respective area name is provided by the `area` route value.</span></span> <span data-ttu-id="77c30-596">Třetí řadič není členem žádné oblasti a může se shodovat pouze v případě, že směrováním neposkytuje žádnou hodnotu pro. `area`</span><span class="sxs-lookup"><span data-stu-id="77c30-596">The third controller isn't a member of any area, and can only match when no value for `area` is provided by routing.</span></span>

<a name="aa"></a>

<span data-ttu-id="77c30-597">Pokud jde o odpovídající *žádnou hodnotu*, absence `area` hodnoty `area` je stejná, jako kdyby hodnota pro byly null nebo prázdný řetězec.</span><span class="sxs-lookup"><span data-stu-id="77c30-597">In terms of matching *no value*, the absence of the `area` value is the same as if the value for `area` were null or the empty string.</span></span>

<span data-ttu-id="77c30-598">Při provádění akce uvnitř oblasti je hodnota `area` trasy pro k dispozici jako [okolní hodnota](#ambient) pro směrování, která se má použít pro generování adresy URL.</span><span class="sxs-lookup"><span data-stu-id="77c30-598">When executing an action inside an area, the route value for `area` is available as an [ambient value](#ambient) for routing to use for URL generation.</span></span> <span data-ttu-id="77c30-599">To znamená, že ve výchozím nastavení oblasti jednat *sticky* pro generování adresy URL, jak ukazuje následující ukázka.</span><span class="sxs-lookup"><span data-stu-id="77c30-599">This means that by default areas act *sticky* for URL generation as demonstrated by the following sample.</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup3.cs?name=snippet3)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Duck/Controllers/UsersController.cs)]

<span data-ttu-id="77c30-600">Následující kód generuje adresu `/Zebra/Users/AddUser`URL na adresu :</span><span class="sxs-lookup"><span data-stu-id="77c30-600">The following code generates a URL to `/Zebra/Users/AddUser`:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/HomeController.cs?name=snippet)]

<a name="action"></a>

## <a name="action-definition"></a><span data-ttu-id="77c30-601">Definice akce</span><span class="sxs-lookup"><span data-stu-id="77c30-601">Action definition</span></span>

<span data-ttu-id="77c30-602">Veřejné metody na řadiči, s výjimkou těch s [NonAction](xref:Microsoft.AspNetCore.Mvc.NonActionAttribute) atribut, jsou akce.</span><span class="sxs-lookup"><span data-stu-id="77c30-602">Public methods on a controller, except those with the [NonAction](xref:Microsoft.AspNetCore.Mvc.NonActionAttribute) attribute, are actions.</span></span>

## <a name="sample-code"></a><span data-ttu-id="77c30-603">Ukázka kódu</span><span class="sxs-lookup"><span data-stu-id="77c30-603">Sample code</span></span>

 * <span data-ttu-id="77c30-604">Metoda [MyDisplayRouteInfo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x/main/Extensions/ControllerContextExtensions.cs) je součástí [ukázkového stahování](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) a slouží k zobrazení informací o směrování.</span><span class="sxs-lookup"><span data-stu-id="77c30-604">The [MyDisplayRouteInfo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x/main/Extensions/ControllerContextExtensions.cs) method is included in the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) and is used to display routing information.</span></span>
* <span data-ttu-id="77c30-605">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) [(jak stáhnout)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="77c30-605">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) ([how to download](xref:index#how-to-download-a-sample))</span></span>

[!INCLUDE[](~/includes/dbg-route.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="77c30-606">ASP.NET Core MVC používá middleware směrování [tak,](xref:fundamentals/middleware/index) aby odpovídal adresám URL příchozích požadavků a mapoval je na akce.</span><span class="sxs-lookup"><span data-stu-id="77c30-606">ASP.NET Core MVC uses the Routing [middleware](xref:fundamentals/middleware/index) to match the URLs of incoming requests and map them to actions.</span></span> <span data-ttu-id="77c30-607">Trasy jsou definovány v kódu spuštění nebo atributy.</span><span class="sxs-lookup"><span data-stu-id="77c30-607">Routes are defined in startup code or attributes.</span></span> <span data-ttu-id="77c30-608">Trasy popisují, jak by měly být cesty adres URL přizpůsobeny akcím.</span><span class="sxs-lookup"><span data-stu-id="77c30-608">Routes describe how URL paths should be matched to actions.</span></span> <span data-ttu-id="77c30-609">Trasy se také používají ke generování adres URL (pro odkazy) rozesílaných v odpovědích.</span><span class="sxs-lookup"><span data-stu-id="77c30-609">Routes are also used to generate URLs (for links) sent out in responses.</span></span>

<span data-ttu-id="77c30-610">Akce jsou buď konvenčně směrovány nebo atribut směrovány.</span><span class="sxs-lookup"><span data-stu-id="77c30-610">Actions are either conventionally routed or attribute routed.</span></span> <span data-ttu-id="77c30-611">Umístění trasy na řadič nebo akce způsobí, že atribut směrovány.</span><span class="sxs-lookup"><span data-stu-id="77c30-611">Placing a route on the controller or the action makes it attribute routed.</span></span> <span data-ttu-id="77c30-612">Další informace naleznete [v tématu Smíšené směrování.](#routing-mixed-ref-label)</span><span class="sxs-lookup"><span data-stu-id="77c30-612">See [Mixed routing](#routing-mixed-ref-label) for more information.</span></span>

<span data-ttu-id="77c30-613">Tento dokument vysvětlí interakce mezi MVC a směrováním a způsob, jakým typické aplikace MVC využívají funkce směrování.</span><span class="sxs-lookup"><span data-stu-id="77c30-613">This document will explain the interactions between MVC and routing, and how typical MVC apps make use of routing features.</span></span> <span data-ttu-id="77c30-614">Podrobnosti o rozšířeném směrování naleznete v [tématu Směrování.](xref:fundamentals/routing)</span><span class="sxs-lookup"><span data-stu-id="77c30-614">See [Routing](xref:fundamentals/routing) for details on advanced routing.</span></span>

## <a name="setting-up-routing-middleware"></a><span data-ttu-id="77c30-615">Nastavení middlewaru směrování</span><span class="sxs-lookup"><span data-stu-id="77c30-615">Setting up Routing Middleware</span></span>

<span data-ttu-id="77c30-616">V *metodě Configure* se může zobrazit kód podobný:</span><span class="sxs-lookup"><span data-stu-id="77c30-616">In your *Configure* method you may see code similar to:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="77c30-617">Uvnitř volání `UseMvc`do `MapRoute` , se používá k vytvoření jedné trasy, `default` kterou budeme označovat jako trasu.</span><span class="sxs-lookup"><span data-stu-id="77c30-617">Inside the call to `UseMvc`, `MapRoute` is used to create a single route, which we'll refer to as the `default` route.</span></span> <span data-ttu-id="77c30-618">Většina aplikací MVC bude používat trasu `default` se šablonou podobnou trase.</span><span class="sxs-lookup"><span data-stu-id="77c30-618">Most MVC apps will use a route with a template similar to the `default` route.</span></span>

<span data-ttu-id="77c30-619">Šablona `"{controller=Home}/{action=Index}/{id?}"` trasy může odpovídat `/Products/Details/5` cestě URL jako `{ controller = Products, action = Details, id = 5 }` a extrahuje hodnoty trasy tokenizací cesty.</span><span class="sxs-lookup"><span data-stu-id="77c30-619">The route template `"{controller=Home}/{action=Index}/{id?}"` can match a URL path like `/Products/Details/5` and will extract the route values `{ controller = Products, action = Details, id = 5 }` by tokenizing the path.</span></span> <span data-ttu-id="77c30-620">MVC se pokusí vyhledat `ProductsController` řadič s `Details`názvem a spustit akci :</span><span class="sxs-lookup"><span data-stu-id="77c30-620">MVC will attempt to locate a controller named `ProductsController` and run the action `Details`:</span></span>

```csharp
public class ProductsController : Controller
{
   public IActionResult Details(int id) { ... }
}
```

<span data-ttu-id="77c30-621">Všimněte si, že v tomto příkladu by vazba modelu použít hodnotu `id = 5` k nastavení parametru `id` `5` při vyvolání této akce.</span><span class="sxs-lookup"><span data-stu-id="77c30-621">Note that in this example, model binding would use the value of `id = 5` to set the `id` parameter to `5` when invoking this action.</span></span> <span data-ttu-id="77c30-622">Další podrobnosti najdete v [části Vazby modelu.](../models/model-binding.md)</span><span class="sxs-lookup"><span data-stu-id="77c30-622">See the [Model Binding](../models/model-binding.md) for more details.</span></span>

<span data-ttu-id="77c30-623">Použití `default` trasy:</span><span class="sxs-lookup"><span data-stu-id="77c30-623">Using the `default` route:</span></span>

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="77c30-624">Šablona trasy:</span><span class="sxs-lookup"><span data-stu-id="77c30-624">The route template:</span></span>

* <span data-ttu-id="77c30-625">`{controller=Home}`definuje `Home` jako výchozí`controller`</span><span class="sxs-lookup"><span data-stu-id="77c30-625">`{controller=Home}` defines `Home` as the default `controller`</span></span>

* <span data-ttu-id="77c30-626">`{action=Index}`definuje `Index` jako výchozí`action`</span><span class="sxs-lookup"><span data-stu-id="77c30-626">`{action=Index}` defines `Index` as the default `action`</span></span>

* <span data-ttu-id="77c30-627">`{id?}`definuje `id` jako volitelné</span><span class="sxs-lookup"><span data-stu-id="77c30-627">`{id?}` defines `id` as optional</span></span>

<span data-ttu-id="77c30-628">Výchozí a volitelné parametry trasy nemusí být v cestě adresy URL pro shodu k dispozici.</span><span class="sxs-lookup"><span data-stu-id="77c30-628">Default and optional route parameters don't need to be present in the URL path for a match.</span></span> <span data-ttu-id="77c30-629">Podrobný popis syntaxe šablony trasy najdete v tématu [Odkaz na šablonu](xref:fundamentals/routing#route-template-reference) trasy.</span><span class="sxs-lookup"><span data-stu-id="77c30-629">See [Route Template Reference](xref:fundamentals/routing#route-template-reference) for a detailed description of route template syntax.</span></span>

<span data-ttu-id="77c30-630">`"{controller=Home}/{action=Index}/{id?}"`může odpovídat `/` cestě URL a `{ controller = Home, action = Index }`vytvoří hodnoty trasy .</span><span class="sxs-lookup"><span data-stu-id="77c30-630">`"{controller=Home}/{action=Index}/{id?}"` can match the URL path `/` and will produce the route values `{ controller = Home, action = Index }`.</span></span> <span data-ttu-id="77c30-631">Hodnoty pro `controller` `action` výchozí hodnoty a jejich `id` využití nevytvářejí hodnotu, protože v cestě url není žádný odpovídající segment.</span><span class="sxs-lookup"><span data-stu-id="77c30-631">The values for `controller` and `action` make use of the default values, `id` doesn't produce a value since there's no corresponding segment in the URL path.</span></span> <span data-ttu-id="77c30-632">MVC by použít tyto hodnoty `HomeController` `Index` trasy k výběru a akce:</span><span class="sxs-lookup"><span data-stu-id="77c30-632">MVC would use these route values to select the `HomeController` and `Index` action:</span></span>

```csharp
public class HomeController : Controller
{
  public IActionResult Index() { ... }
}
```

<span data-ttu-id="77c30-633">Pomocí této definice řadiče `HomeController.Index` a šablony trasy by byla akce provedena pro některou z následujících cest adresy URL:</span><span class="sxs-lookup"><span data-stu-id="77c30-633">Using this controller definition and route template, the `HomeController.Index` action would be executed for any of the following URL paths:</span></span>

* `/Home/Index/17`

* `/Home/Index`

* `/Home`

* `/`

<span data-ttu-id="77c30-634">Komfortní metoda `UseMvcWithDefaultRoute`:</span><span class="sxs-lookup"><span data-stu-id="77c30-634">The convenience method `UseMvcWithDefaultRoute`:</span></span>

```csharp
app.UseMvcWithDefaultRoute();
```

<span data-ttu-id="77c30-635">Lze použít k nahrazení:</span><span class="sxs-lookup"><span data-stu-id="77c30-635">Can be used to replace:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="77c30-636">`UseMvc`a `UseMvcWithDefaultRoute` přidejte `RouterMiddleware` instanci kanálu middleware.</span><span class="sxs-lookup"><span data-stu-id="77c30-636">`UseMvc` and `UseMvcWithDefaultRoute` add an instance of `RouterMiddleware` to the middleware pipeline.</span></span> <span data-ttu-id="77c30-637">MVC nespolupracuje přímo s middleware a používá směrování pro zpracování požadavků.</span><span class="sxs-lookup"><span data-stu-id="77c30-637">MVC doesn't interact directly with middleware, and uses routing to handle requests.</span></span> <span data-ttu-id="77c30-638">MVC je připojen k trase `MvcRouteHandler`prostřednictvím instance .</span><span class="sxs-lookup"><span data-stu-id="77c30-638">MVC is connected to the routes through an instance of `MvcRouteHandler`.</span></span> <span data-ttu-id="77c30-639">Kód uvnitř `UseMvc` je podobný následujícímu:</span><span class="sxs-lookup"><span data-stu-id="77c30-639">The code inside of `UseMvc` is similar to the following:</span></span>

```csharp
var routes = new RouteBuilder(app);

// Add connection to MVC, will be hooked up by calls to MapRoute.
routes.DefaultHandler = new MvcRouteHandler(...);

// Execute callback to register routes.
// routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");

// Create route collection and add the middleware.
app.UseRouter(routes.Build());
```

<span data-ttu-id="77c30-640">`UseMvc`nedefinuje přímo žádné trasy, přidá zástupný symbol do kolekce `attribute` tras pro trasu.</span><span class="sxs-lookup"><span data-stu-id="77c30-640">`UseMvc` doesn't directly define any routes, it adds a placeholder to the route collection for the `attribute` route.</span></span> <span data-ttu-id="77c30-641">Přetížení `UseMvc(Action<IRouteBuilder>)` umožňuje přidat vlastní trasy a také podporuje směrování atributů.</span><span class="sxs-lookup"><span data-stu-id="77c30-641">The overload `UseMvc(Action<IRouteBuilder>)` lets you add your own routes and also supports attribute routing.</span></span>  <span data-ttu-id="77c30-642">`UseMvc`a všechny jeho varianty přidávají zástupný symbol pro trasu atributu – `UseMvc`směrování atributů je vždy k dispozici bez ohledu na to, jak nakonfigurujete .</span><span class="sxs-lookup"><span data-stu-id="77c30-642">`UseMvc` and all of its variations add a placeholder for the attribute route - attribute routing is always available regardless of how you configure `UseMvc`.</span></span> <span data-ttu-id="77c30-643">`UseMvcWithDefaultRoute`definuje výchozí trasu a podporuje směrování atributů.</span><span class="sxs-lookup"><span data-stu-id="77c30-643">`UseMvcWithDefaultRoute` defines a default route and supports attribute routing.</span></span> <span data-ttu-id="77c30-644">Oddíl [Směrování atributů](#attribute-routing-ref-label) obsahuje další podrobnosti o směrování atributů.</span><span class="sxs-lookup"><span data-stu-id="77c30-644">The [Attribute Routing](#attribute-routing-ref-label) section includes more details on attribute routing.</span></span>

<a name="routing-conventional-ref-label"></a>

## <a name="conventional-routing"></a><span data-ttu-id="77c30-645">Konvenční směrování</span><span class="sxs-lookup"><span data-stu-id="77c30-645">Conventional routing</span></span>

<span data-ttu-id="77c30-646">Trasa: `default`</span><span class="sxs-lookup"><span data-stu-id="77c30-646">The `default` route:</span></span>

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="77c30-647">Předchozí kód je příkladem konvenčního směrování.</span><span class="sxs-lookup"><span data-stu-id="77c30-647">The preceding code is an example of a conventional routing.</span></span> <span data-ttu-id="77c30-648">Tento styl se nazývá konvenční směrování, protože vytváří *konvenci* pro cesty adres URL:</span><span class="sxs-lookup"><span data-stu-id="77c30-648">This style is called conventional routing because it establishes a *convention* for URL paths:</span></span>

* <span data-ttu-id="77c30-649">První segment cesty se mapuje na název řadiče.</span><span class="sxs-lookup"><span data-stu-id="77c30-649">The first path segment maps to the controller name.</span></span>
* <span data-ttu-id="77c30-650">Druhý mapuje na název akce.</span><span class="sxs-lookup"><span data-stu-id="77c30-650">The second maps to the action name.</span></span>
* <span data-ttu-id="77c30-651">Třetí segment se používá `id`pro volitelné .</span><span class="sxs-lookup"><span data-stu-id="77c30-651">The third segment is used for an optional `id`.</span></span> <span data-ttu-id="77c30-652">`id`mapuje na entitu modelu.</span><span class="sxs-lookup"><span data-stu-id="77c30-652">`id` maps to a model entity.</span></span>

<span data-ttu-id="77c30-653">Pomocí `default` této trasy se `/Products/List` cesta `ProductsController.List` url mapuje na akci a `/Blog/Article/17` mapuje na `BlogController.Article`.</span><span class="sxs-lookup"><span data-stu-id="77c30-653">Using this `default` route, the URL path `/Products/List` maps to the `ProductsController.List` action, and `/Blog/Article/17` maps to `BlogController.Article`.</span></span> <span data-ttu-id="77c30-654">Toto mapování je založeno **pouze** na názvech řadiče a akcí a není založeno na oborech názvů, umístěních zdrojových souborů nebo parametrech metody.</span><span class="sxs-lookup"><span data-stu-id="77c30-654">This mapping is based on the controller and action names **only** and isn't based on namespaces, source file locations, or method parameters.</span></span>

> [!TIP]
> <span data-ttu-id="77c30-655">Použití konvenčního směrování s výchozí trasou umožňuje rychle sestavit aplikaci, aniž byste museli přijít s novým vzorem adresy URL pro každou akci, kterou definujete.</span><span class="sxs-lookup"><span data-stu-id="77c30-655">Using conventional routing with the default route allows you to build the application quickly without having to come up with a new URL pattern for each action you define.</span></span> <span data-ttu-id="77c30-656">U aplikace s akcemi stylu CRUD může konzistence adres URL napříč ovladači zjednodušit váš kód a učinit vaše ui předvídatelnější.</span><span class="sxs-lookup"><span data-stu-id="77c30-656">For an application with CRUD style actions, having consistency for the URLs across your controllers can help simplify your code and make your UI more predictable.</span></span>

> [!WARNING]
> <span data-ttu-id="77c30-657">Šablona `id` trasy je definována jako volitelná, což znamená, že vaše akce lze provést bez ID poskytnutého jako součást adresy URL.</span><span class="sxs-lookup"><span data-stu-id="77c30-657">The `id` is defined as optional by the route template, meaning that your actions can execute without the ID provided as part of the URL.</span></span> <span data-ttu-id="77c30-658">Obvykle co se `id` stane, pokud je vynechán z adresy `0` URL je, že bude nastavena na model `id == 0`vazby a v důsledku toho žádná entita bude nalezena v databázi odpovídající .</span><span class="sxs-lookup"><span data-stu-id="77c30-658">Usually what will happen if `id` is omitted from the URL is that it will be set to `0` by model binding, and as a result no entity will be found in the database matching `id == 0`.</span></span> <span data-ttu-id="77c30-659">Směrování atributů vám může poskytnout jemně odstupňovanou kontrolu, aby id bylo vyžadováno pro některé akce a ne pro jiné.</span><span class="sxs-lookup"><span data-stu-id="77c30-659">Attribute routing can give you fine-grained control to make the ID required for some actions and not for others.</span></span> <span data-ttu-id="77c30-660">Podle konvence dokumentace bude obsahovat `id` volitelné parametry, jako když je pravděpodobné, že se zobrazí ve správném použití.</span><span class="sxs-lookup"><span data-stu-id="77c30-660">By convention the documentation will include optional parameters like `id` when they're likely to appear in correct usage.</span></span>

## <a name="multiple-routes"></a><span data-ttu-id="77c30-661">Více tras</span><span class="sxs-lookup"><span data-stu-id="77c30-661">Multiple routes</span></span>

<span data-ttu-id="77c30-662">Do ní můžete přidat `UseMvc` více tras `MapRoute`přidáním dalších volání do .</span><span class="sxs-lookup"><span data-stu-id="77c30-662">You can add multiple routes inside `UseMvc` by adding more calls to `MapRoute`.</span></span> <span data-ttu-id="77c30-663">To umožňuje definovat více konvencí nebo přidat konvenční trasy, které jsou vyhrazeny pro konkrétní akci, například:</span><span class="sxs-lookup"><span data-stu-id="77c30-663">Doing so allows you to define multiple conventions, or to add conventional routes that are dedicated to a specific action, such as:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("blog", "blog/{*article}",
            defaults: new { controller = "Blog", action = "Article" });
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="77c30-664">Trasa `blog` je zde *vyhrazená konvenční trasa*, což znamená, že používá konvenční směrovací systém, ale je určena pro konkrétní akci.</span><span class="sxs-lookup"><span data-stu-id="77c30-664">The `blog` route here is a *dedicated conventional route*, meaning that it uses the conventional routing system, but is dedicated to a specific action.</span></span> <span data-ttu-id="77c30-665">Vzhledem k tomu, `controller` že se v šabloně trasy nezobrazují a `action` nezobrazují se jako parametry, mohou mít pouze výchozí hodnoty, a proto bude tato trasa vždy mapována na akci `BlogController.Article`.</span><span class="sxs-lookup"><span data-stu-id="77c30-665">Since `controller` and `action` don't appear in the route template as parameters, they can only have the default values, and thus this route will always map to the action `BlogController.Article`.</span></span>

<span data-ttu-id="77c30-666">Trasy v kolekci tras jsou objednány a budou zpracovány v pořadí, v jakém jsou přidány.</span><span class="sxs-lookup"><span data-stu-id="77c30-666">Routes in the route collection are ordered, and will be processed in the order they're added.</span></span> <span data-ttu-id="77c30-667">Takže v tomto `blog` příkladu bude trasa vyzkoušena před trasou. `default`</span><span class="sxs-lookup"><span data-stu-id="77c30-667">So in this example, the `blog` route will be tried before the `default` route.</span></span>

> [!NOTE]
> <span data-ttu-id="77c30-668">*Vyhrazené konvenční trasy* často používají parametry `{*article}` trasy **catch-all,** jako je zachycení zbývající části cesty URL.</span><span class="sxs-lookup"><span data-stu-id="77c30-668">*Dedicated conventional routes* often use **catch-all** route parameters like `{*article}` to capture the remaining portion of the URL path.</span></span> <span data-ttu-id="77c30-669">To může způsobit, že trasa bude příliš chamtivá, což znamená, že odpovídá adresám URL, které jste zamýšleli, aby byly spárovány s jinými trasami.</span><span class="sxs-lookup"><span data-stu-id="77c30-669">This can make a route 'too greedy' meaning that it matches URLs that you intended to be matched by other routes.</span></span> <span data-ttu-id="77c30-670">Chcete-li tento problém vyřešit, vložte "chamtivé" trasy později do tabulky tras.</span><span class="sxs-lookup"><span data-stu-id="77c30-670">Put the 'greedy' routes later in the route table to solve this.</span></span>

### <a name="fallback"></a><span data-ttu-id="77c30-671">Záložní volba</span><span class="sxs-lookup"><span data-stu-id="77c30-671">Fallback</span></span>

<span data-ttu-id="77c30-672">V rámci zpracování požadavků MVC ověří, že hodnoty postupu lze použít k nalezení řadiče a akce ve vaší aplikaci.</span><span class="sxs-lookup"><span data-stu-id="77c30-672">As part of request processing, MVC will verify that the route values can be used to find a controller and action in your application.</span></span> <span data-ttu-id="77c30-673">Pokud hodnoty trasy neodpovídají akci, nebude trasa považována za shodu a další trasa bude vyzkoušena.</span><span class="sxs-lookup"><span data-stu-id="77c30-673">If the route values don't match an action then the route isn't considered a match, and the next route will be tried.</span></span> <span data-ttu-id="77c30-674">Tento postup se nazývá *záložní*a má zjednodušit případy, kdy se konvenční trasy překrývají.</span><span class="sxs-lookup"><span data-stu-id="77c30-674">This is called *fallback*, and it's intended to simplify cases where conventional routes overlap.</span></span>

### <a name="disambiguating-actions"></a><span data-ttu-id="77c30-675">Nejednoznačná opatření</span><span class="sxs-lookup"><span data-stu-id="77c30-675">Disambiguating actions</span></span>

<span data-ttu-id="77c30-676">Když dvě akce odpovídají prostřednictvím směrování, MVC musí disambiguate vybrat 'nejlepší' kandidáta nebo jinak vyvolat výjimku.</span><span class="sxs-lookup"><span data-stu-id="77c30-676">When two actions match through routing, MVC must disambiguate to choose the 'best' candidate or else throw an exception.</span></span> <span data-ttu-id="77c30-677">Příklad:</span><span class="sxs-lookup"><span data-stu-id="77c30-677">For example:</span></span>

```csharp
public class ProductsController : Controller
{
   public IActionResult Edit(int id) { ... }

   [HttpPost]
   public IActionResult Edit(int id, Product product) { ... }
}
```

<span data-ttu-id="77c30-678">Tento řadič definuje dvě akce, které `/Products/Edit/17` by `{ controller = Products, action = Edit, id = 17 }`odpovídaly cestě url a datům trasy .</span><span class="sxs-lookup"><span data-stu-id="77c30-678">This controller defines two actions that would match the URL path `/Products/Edit/17` and route data `{ controller = Products, action = Edit, id = 17 }`.</span></span> <span data-ttu-id="77c30-679">Toto je typický vzor pro řadiče MVC, kde `Edit(int)` `Edit(int, Product)` se zobrazuje formulář pro úpravu produktu a zpracovává zaúčtovaný formulář.</span><span class="sxs-lookup"><span data-stu-id="77c30-679">This is a typical pattern for MVC controllers where `Edit(int)` shows a form to edit a product, and `Edit(int, Product)` processes  the posted form.</span></span> <span data-ttu-id="77c30-680">Chcete-li to možné MVC `Edit(int, Product)` bude muset zvolit, kdy je požadavek HTTP `POST` a `Edit(int)` když http sloveso je něco jiného.</span><span class="sxs-lookup"><span data-stu-id="77c30-680">To make this possible MVC would need to choose `Edit(int, Product)` when the request is an HTTP `POST` and `Edit(int)` when the HTTP verb is anything else.</span></span>

<span data-ttu-id="77c30-681">`HttpPostAttribute` ( `[HttpPost]` ) je `IActionConstraint` implementace, která umožní pouze akci, která má `POST`být vybrána, pokud je sloveso HTTP .</span><span class="sxs-lookup"><span data-stu-id="77c30-681">The `HttpPostAttribute` ( `[HttpPost]` ) is an implementation of `IActionConstraint` that will only allow the action to be selected when the HTTP verb is `POST`.</span></span> <span data-ttu-id="77c30-682">Přítomnost `IActionConstraint` dělá `Edit(int, Product)` 'lepší' zápas než `Edit(int)`, `Edit(int, Product)` takže bude souzen jako první.</span><span class="sxs-lookup"><span data-stu-id="77c30-682">The presence of an `IActionConstraint` makes the `Edit(int, Product)` a 'better' match than `Edit(int)`, so `Edit(int, Product)` will be tried first.</span></span>

<span data-ttu-id="77c30-683">Budete muset psát pouze `IActionConstraint` vlastní implementace ve specializovaných scénářích, ale je důležité pochopit `HttpPostAttribute` roli atributů, jako je - podobné atributy jsou definovány pro ostatní slovesa HTTP.</span><span class="sxs-lookup"><span data-stu-id="77c30-683">You will only need to write custom `IActionConstraint` implementations in specialized scenarios, but it's important to understand the role of attributes like `HttpPostAttribute`  - similar attributes are defined for other HTTP verbs.</span></span> <span data-ttu-id="77c30-684">V běžném směrování je běžné, že akce používají stejný název `show form -> submit form` akce, když jsou součástí pracovního postupu.</span><span class="sxs-lookup"><span data-stu-id="77c30-684">In conventional routing it's common for actions to use the same action name when they're part of a `show form -> submit form` workflow.</span></span> <span data-ttu-id="77c30-685">Pohodlí tohoto vzoru bude více zřejmé po kontrole [principiu IActionConstraint](#understanding-iactionconstraint) části.</span><span class="sxs-lookup"><span data-stu-id="77c30-685">The convenience of this pattern will become more apparent after reviewing the [Understanding IActionConstraint](#understanding-iactionconstraint) section.</span></span>

<span data-ttu-id="77c30-686">Pokud se více tras shoduje a MVC nemůže najít "nejlepší" `AmbiguousActionException`trasu, vyvolá .</span><span class="sxs-lookup"><span data-stu-id="77c30-686">If multiple routes match, and MVC can't find a 'best' route, it will throw an `AmbiguousActionException`.</span></span>

<a name="routing-route-name-ref-label"></a>

### <a name="route-names"></a><span data-ttu-id="77c30-687">Názvy tras</span><span class="sxs-lookup"><span data-stu-id="77c30-687">Route names</span></span>

<span data-ttu-id="77c30-688">Řetězce `"blog"` a `"default"` v následujících příkladech jsou názvy tras:</span><span class="sxs-lookup"><span data-stu-id="77c30-688">The strings  `"blog"` and `"default"` in the following examples are route names:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("blog", "blog/{*article}",
               defaults: new { controller = "Blog", action = "Article" });
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="77c30-689">Názvy tras dávají trase logický název, takže pojmenovanou trasu lze použít pro generování adresy URL.</span><span class="sxs-lookup"><span data-stu-id="77c30-689">The route names give the route a logical name so that the named route can be used for URL generation.</span></span> <span data-ttu-id="77c30-690">To značně zjednodušuje vytváření adres URL, když by řazení tras mohlo zkomplikovat generování adres URL.</span><span class="sxs-lookup"><span data-stu-id="77c30-690">This greatly simplifies URL creation when the ordering of routes could make URL generation complicated.</span></span> <span data-ttu-id="77c30-691">Názvy tras musí být jedinečné pro celou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="77c30-691">Route names must be unique application-wide.</span></span>

<span data-ttu-id="77c30-692">Názvy tras nemají žádný vliv na párování adres URL nebo zpracování požadavků. používají se pouze pro generování adres URL.</span><span class="sxs-lookup"><span data-stu-id="77c30-692">Route names have no impact on URL matching or handling of requests; they're used only for URL generation.</span></span> <span data-ttu-id="77c30-693">[Směrování](xref:fundamentals/routing) má podrobnější informace o generování adres URL, včetně generování adres URL v pomocných souběhem specifických pro MVC.</span><span class="sxs-lookup"><span data-stu-id="77c30-693">[Routing](xref:fundamentals/routing) has more detailed information on URL generation including URL generation in MVC-specific helpers.</span></span>

<a name="attribute-routing-ref-label"></a>

## <a name="attribute-routing"></a><span data-ttu-id="77c30-694">Směrování atributů</span><span class="sxs-lookup"><span data-stu-id="77c30-694">Attribute routing</span></span>

<span data-ttu-id="77c30-695">Směrování atributů používá sadu atributů k mapování akcí přímo na směrování šablon.</span><span class="sxs-lookup"><span data-stu-id="77c30-695">Attribute routing uses a set of attributes to map actions directly to route templates.</span></span> <span data-ttu-id="77c30-696">V následujícím příkladu se `app.UseMvc();` `Configure` používá v metodě a není předán žádný postup.</span><span class="sxs-lookup"><span data-stu-id="77c30-696">In the following example, `app.UseMvc();` is used in the `Configure` method and no route is passed.</span></span> <span data-ttu-id="77c30-697">Bude `HomeController` odpovídat sadě adres URL podobných tomu, co by odpovídalo výchozí trase: `{controller=Home}/{action=Index}/{id?}`</span><span class="sxs-lookup"><span data-stu-id="77c30-697">The `HomeController` will match a set of URLs similar to what the default route `{controller=Home}/{action=Index}/{id?}` would match:</span></span>

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

<span data-ttu-id="77c30-698">Akce `HomeController.Index()` bude provedena pro libovolnou cestu `/`url `/Home`, `/Home/Index`nebo .</span><span class="sxs-lookup"><span data-stu-id="77c30-698">The `HomeController.Index()` action will be executed for any of the URL paths `/`, `/Home`, or `/Home/Index`.</span></span>

> [!NOTE]
> <span data-ttu-id="77c30-699">Tento příklad zvýrazní klíčový programovací rozdíl mezi směrováním atributů a konvenčním směrováním.</span><span class="sxs-lookup"><span data-stu-id="77c30-699">This example highlights a key programming difference between attribute routing and conventional routing.</span></span> <span data-ttu-id="77c30-700">Směrování atributů vyžaduje více vstupů k určení trasy; konvenční výchozí trasa zpracovává trasy stručněji.</span><span class="sxs-lookup"><span data-stu-id="77c30-700">Attribute routing requires more input to specify a route; the conventional default route handles routes more succinctly.</span></span> <span data-ttu-id="77c30-701">Směrování atributů však umožňuje (a vyžaduje) přesné řízení, které šablony tras se vztahují na každou akci.</span><span class="sxs-lookup"><span data-stu-id="77c30-701">However, attribute routing allows (and requires) precise control of which route templates apply to each action.</span></span>

<span data-ttu-id="77c30-702">Při směrování atributů nehrají názvy názvů řadičů a akcí **žádnou** roli ve vybrané akci.</span><span class="sxs-lookup"><span data-stu-id="77c30-702">With attribute routing the controller name and action names play **no** role in which action is selected.</span></span> <span data-ttu-id="77c30-703">Tento příklad bude odpovídat stejným adresám URL jako v předchozím příkladu.</span><span class="sxs-lookup"><span data-stu-id="77c30-703">This example will match the same URLs as the previous example.</span></span>

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
> <span data-ttu-id="77c30-704">Výše uvedené šablony tras nedefinují `action`parametry trasy pro , `area`, a `controller`.</span><span class="sxs-lookup"><span data-stu-id="77c30-704">The route templates above don't define route parameters for `action`, `area`, and `controller`.</span></span> <span data-ttu-id="77c30-705">Ve skutečnosti tyto parametry trasy nejsou povoleny v atributových trasách.</span><span class="sxs-lookup"><span data-stu-id="77c30-705">In fact, these route parameters are not allowed in attribute routes.</span></span> <span data-ttu-id="77c30-706">Vzhledem k tomu, že šablona trasy je již přidružena k akci, nemá smysl analyzovat název akce z adresy URL.</span><span class="sxs-lookup"><span data-stu-id="77c30-706">Since the route template is already associated with an action, it wouldn't make sense to parse the action name from the URL.</span></span>

## <a name="attribute-routing-with-httpverb-attributes"></a><span data-ttu-id="77c30-707">Směrování atributů s atributy Http[Verb]</span><span class="sxs-lookup"><span data-stu-id="77c30-707">Attribute routing with Http[Verb] attributes</span></span>

<span data-ttu-id="77c30-708">Směrování atributů může také `Http[Verb]` využít atributy, jako `HttpPostAttribute`je například .</span><span class="sxs-lookup"><span data-stu-id="77c30-708">Attribute routing can also make use of the `Http[Verb]` attributes such as `HttpPostAttribute`.</span></span> <span data-ttu-id="77c30-709">Všechny tyto atributy mohou přijmout šablonu trasy.</span><span class="sxs-lookup"><span data-stu-id="77c30-709">All of these attributes can accept a route template.</span></span> <span data-ttu-id="77c30-710">Tento příklad ukazuje dvě akce, které odpovídají stejné šabloně trasy:</span><span class="sxs-lookup"><span data-stu-id="77c30-710">This example shows two actions that match the same route template:</span></span>

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

<span data-ttu-id="77c30-711">Pro cestu URL, jako `/products` je `ProductsApi.ListProducts` akce, bude `GET` provedena, když je a `ProductsApi.CreateProduct` bude `POST`provedeno sloveso HTTP, když je sloveso HTTP .</span><span class="sxs-lookup"><span data-stu-id="77c30-711">For a URL path like `/products` the `ProductsApi.ListProducts` action will be executed when the HTTP verb is `GET` and `ProductsApi.CreateProduct` will be executed when the HTTP verb is `POST`.</span></span> <span data-ttu-id="77c30-712">Nejprve směrovat směr atributu odpovídá adrese URL se sadou šablon tras definovaných atributy trasy.</span><span class="sxs-lookup"><span data-stu-id="77c30-712">Attribute routing first matches the URL against the set of route templates defined by route attributes.</span></span> <span data-ttu-id="77c30-713">Jakmile se šablona `IActionConstraint` trasy shoduje, použijí se omezení k určení, které akce lze provést.</span><span class="sxs-lookup"><span data-stu-id="77c30-713">Once a route template matches, `IActionConstraint` constraints are applied to determine which actions can be executed.</span></span>

> [!TIP]
> <span data-ttu-id="77c30-714">Při vytváření rozhraní REST API, je vzácné, `[Route(...)]` že budete chtít použít na metodu akce jako akce bude akce přijímat všechny metody HTTP.</span><span class="sxs-lookup"><span data-stu-id="77c30-714">When building a REST API, it's rare that you will want to use `[Route(...)]` on an action method as the action will accept all HTTP methods.</span></span> <span data-ttu-id="77c30-715">Je lepší použít konkrétnější, `Http*Verb*Attributes` abyste byli přesní o tom, co vaše rozhraní API podporuje.</span><span class="sxs-lookup"><span data-stu-id="77c30-715">It's better to use the more specific `Http*Verb*Attributes` to be precise about what your API supports.</span></span> <span data-ttu-id="77c30-716">Očekává se, že klienti rozhraní REST API budou vědět, jaké cesty a http slovesa jsou mapována na konkrétní logické operace.</span><span class="sxs-lookup"><span data-stu-id="77c30-716">Clients of REST APIs are expected to know what paths and HTTP verbs map to specific logical operations.</span></span>

<span data-ttu-id="77c30-717">Vzhledem k tomu, že trasa atributu se vztahuje na konkrétní akci, je snadné nastavit parametry požadované jako součást definice šablony trasy.</span><span class="sxs-lookup"><span data-stu-id="77c30-717">Since an attribute route applies to a specific action, it's easy to make parameters required as part of the route template definition.</span></span> <span data-ttu-id="77c30-718">V tomto `id` příkladu je požadováno jako součást cesty URL.</span><span class="sxs-lookup"><span data-stu-id="77c30-718">In this example, `id` is required as part of the URL path.</span></span>

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

<span data-ttu-id="77c30-719">Akce `ProductsApi.GetProduct(int)` bude provedena pro cestu URL, jako `/products/3` je, `/products`ale ne pro cestu URL, jako je .</span><span class="sxs-lookup"><span data-stu-id="77c30-719">The `ProductsApi.GetProduct(int)` action will be executed for a URL path like `/products/3` but not for a URL path like `/products`.</span></span> <span data-ttu-id="77c30-720">Úplný popis šablon tras a souvisejících možností naleznete v tématu [Směrování.](xref:fundamentals/routing)</span><span class="sxs-lookup"><span data-stu-id="77c30-720">See [Routing](xref:fundamentals/routing) for a full description of route templates and related options.</span></span>

## <a name="route-name"></a><span data-ttu-id="77c30-721">Název postupu</span><span class="sxs-lookup"><span data-stu-id="77c30-721">Route Name</span></span>

<span data-ttu-id="77c30-722">Následující kód definuje *název* postupu `Products_List`:</span><span class="sxs-lookup"><span data-stu-id="77c30-722">The following code  defines a *route name* of `Products_List`:</span></span>

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

<span data-ttu-id="77c30-723">Názvy tras lze použít ke generování adresy URL na základě konkrétní trasy.</span><span class="sxs-lookup"><span data-stu-id="77c30-723">Route names can be used to generate a URL based on a specific route.</span></span> <span data-ttu-id="77c30-724">Názvy tras nemají žádný vliv na chování směrování při porovnávání adres URL a používají se pouze pro generování adres URL.</span><span class="sxs-lookup"><span data-stu-id="77c30-724">Route names have no impact on the URL matching behavior of routing and are only used for URL generation.</span></span> <span data-ttu-id="77c30-725">Názvy tras musí být jedinečné pro celou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="77c30-725">Route names must be unique application-wide.</span></span>

> [!NOTE]
> <span data-ttu-id="77c30-726">Porovnejte to s konvenční *výchozí trasou* `id` `{id?}`, která definuje parametr jako volitelný ( ).</span><span class="sxs-lookup"><span data-stu-id="77c30-726">Contrast this with the conventional *default route*, which defines the `id` parameter as optional (`{id?}`).</span></span> <span data-ttu-id="77c30-727">Tato schopnost přesně určit, že api má `/products` `/products/5` výhody, jako je například povolení a odeslání do různých akcí.</span><span class="sxs-lookup"><span data-stu-id="77c30-727">This ability to precisely specify APIs has advantages, such as  allowing `/products` and `/products/5` to be dispatched to different actions.</span></span>

<a name="routing-combining-ref-label"></a>

### <a name="combining-routes"></a><span data-ttu-id="77c30-728">Kombinování tras</span><span class="sxs-lookup"><span data-stu-id="77c30-728">Combining routes</span></span>

<span data-ttu-id="77c30-729">Chcete-li, aby směrování atributů bylo méně opakované, jsou atributy trasy na řadiči kombinovány s atributy trasy u jednotlivých akcí.</span><span class="sxs-lookup"><span data-stu-id="77c30-729">To make attribute routing less repetitive, route attributes on the controller are combined with route attributes on the individual actions.</span></span> <span data-ttu-id="77c30-730">Všechny šablony tras definované na řadiči jsou předřazeny k směrování šablon akcí.</span><span class="sxs-lookup"><span data-stu-id="77c30-730">Any route templates defined on the controller are prepended to route templates on the actions.</span></span> <span data-ttu-id="77c30-731">Umístění atributu trasy na řadič provede **všechny** akce v řadiči pomocí směrování atributu.</span><span class="sxs-lookup"><span data-stu-id="77c30-731">Placing a route attribute on the controller makes **all** actions in the controller use attribute routing.</span></span>

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

<span data-ttu-id="77c30-732">V tomto příkladu `/products` se `ProductsApi.ListProducts`může cesta `/products/5` url `ProductsApi.GetProduct(int)`shodovat a cesta adresy URL se může shodovat .</span><span class="sxs-lookup"><span data-stu-id="77c30-732">In this example the URL path `/products` can match `ProductsApi.ListProducts`, and the URL path `/products/5` can match `ProductsApi.GetProduct(int)`.</span></span> <span data-ttu-id="77c30-733">Obě tyto akce odpovídají `GET` pouze protokolu HTTP, `HttpGetAttribute`protože jsou označeny písmenem .</span><span class="sxs-lookup"><span data-stu-id="77c30-733">Both of these actions only match HTTP `GET` because they're marked with the `HttpGetAttribute`.</span></span>

<span data-ttu-id="77c30-734">Šablony trasy použité na akci, `/` `~/` která začíná nebo nezkombinují se šablonami tras aplikovanými na kontroler.</span><span class="sxs-lookup"><span data-stu-id="77c30-734">Route templates applied to an action that begin with `/` or `~/` don't get combined with route templates applied to the controller.</span></span> <span data-ttu-id="77c30-735">Tento příklad odpovídá sadě cest URL podobné *výchozí trase*.</span><span class="sxs-lookup"><span data-stu-id="77c30-735">This example matches a set of URL paths similar to the *default route*.</span></span>

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

### <a name="ordering-attribute-routes"></a><span data-ttu-id="77c30-736">Řazení tras atributů</span><span class="sxs-lookup"><span data-stu-id="77c30-736">Ordering attribute routes</span></span>

<span data-ttu-id="77c30-737">Na rozdíl od konvenčních tras, které se provádějí v definovaném pořadí, směrování atributů vytvoří strom a porovná všechny trasy současně.</span><span class="sxs-lookup"><span data-stu-id="77c30-737">In contrast to conventional routes, which execute in a defined order, attribute routing builds a tree and matches all routes simultaneously.</span></span> <span data-ttu-id="77c30-738">To se chová jako-pokud položky trasy byly umístěny v ideálním pořadí; nejkonkrétnější trasy mají šanci provést před obecnějšími trasami.</span><span class="sxs-lookup"><span data-stu-id="77c30-738">This behaves as-if the route entries were placed in an ideal ordering; the most specific routes have a chance to execute before the more general routes.</span></span>

<span data-ttu-id="77c30-739">Například trasa `blog/search/{topic}` jako je konkrétnější než `blog/{*article}`trasa jako .</span><span class="sxs-lookup"><span data-stu-id="77c30-739">For example, a route like `blog/search/{topic}` is more specific than a route like `blog/{*article}`.</span></span> <span data-ttu-id="77c30-740">Logicky řečeno `blog/search/{topic}` trasa 'běží' první, ve výchozím nastavení, protože to je jediný rozumný řazení.</span><span class="sxs-lookup"><span data-stu-id="77c30-740">Logically speaking the `blog/search/{topic}` route 'runs' first, by default, because that's the only sensible ordering.</span></span> <span data-ttu-id="77c30-741">Pomocí konvenčního směrování je vývojář zodpovědný za umístění tras v požadovaném pořadí.</span><span class="sxs-lookup"><span data-stu-id="77c30-741">Using conventional routing, the developer is  responsible for placing routes in the desired order.</span></span>

<span data-ttu-id="77c30-742">Trasy atributů můžete nakonfigurovat `Order` pořadí, pomocí vlastnosti všech atributů trasy za předpokladu, že rozhraní.</span><span class="sxs-lookup"><span data-stu-id="77c30-742">Attribute routes can configure an order, using the `Order` property of all of the framework provided route attributes.</span></span> <span data-ttu-id="77c30-743">Trasy jsou zpracovávány podle vzestupného druhu `Order` vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="77c30-743">Routes are processed according to an ascending sort of the `Order` property.</span></span> <span data-ttu-id="77c30-744">Výchozí pořadí `0`je .</span><span class="sxs-lookup"><span data-stu-id="77c30-744">The default order is `0`.</span></span> <span data-ttu-id="77c30-745">Nastavení trasy `Order = -1` pomocí bude spuštěno před trasami, které nenastavují objednávku.</span><span class="sxs-lookup"><span data-stu-id="77c30-745">Setting a route using `Order = -1` will run before routes that don't set an order.</span></span> <span data-ttu-id="77c30-746">Nastavení trasy `Order = 1` pomocí bude spuštěno po výchozím pořadí trasy.</span><span class="sxs-lookup"><span data-stu-id="77c30-746">Setting a route using `Order = 1` will run after default route ordering.</span></span>

> [!TIP]
> <span data-ttu-id="77c30-747">Vzávislosti `Order`na .</span><span class="sxs-lookup"><span data-stu-id="77c30-747">Avoid depending on `Order`.</span></span> <span data-ttu-id="77c30-748">Pokud vaše URL-space vyžaduje explicitní hodnoty objednávky směrovat správně, pak je to pravděpodobně matoucí i pro klienty.</span><span class="sxs-lookup"><span data-stu-id="77c30-748">If your URL-space requires explicit order values to route correctly, then it's likely confusing to clients as well.</span></span> <span data-ttu-id="77c30-749">Obecně atribut směrování vybere správný postup s URL odpovídající.</span><span class="sxs-lookup"><span data-stu-id="77c30-749">In general attribute routing will select the correct route with URL matching.</span></span> <span data-ttu-id="77c30-750">Pokud výchozí pořadí použité pro generování adresy URL nefunguje, použití názvu trasy `Order` jako přepsání je obvykle jednodušší než použití vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="77c30-750">If the default order used for URL generation isn't working, using route name as an override is usually simpler than applying the `Order` property.</span></span>

<span data-ttu-id="77c30-751">Razor Pages směrování a MVC řadič směrování sdílet implementaci.</span><span class="sxs-lookup"><span data-stu-id="77c30-751">Razor Pages routing and MVC controller routing share an implementation.</span></span> <span data-ttu-id="77c30-752">Informace o pořadí tras v tématech Razor Pages jsou k dispozici na [trase Razor Pages a konvencích aplikací: Pořadí tras](xref:razor-pages/razor-pages-conventions#route-order).</span><span class="sxs-lookup"><span data-stu-id="77c30-752">Information on route order in the Razor Pages topics is available at [Razor Pages route and app conventions: Route order](xref:razor-pages/razor-pages-conventions#route-order).</span></span>

<a name="routing-token-replacement-templates-ref-label"></a>

## <a name="token-replacement-in-route-templates-controller-action-area"></a><span data-ttu-id="77c30-753">Nahrazení tokenu v šablonách tras ([kontrolor], [akce], [oblast])</span><span class="sxs-lookup"><span data-stu-id="77c30-753">Token replacement in route templates ([controller], [action], [area])</span></span>

<span data-ttu-id="77c30-754">Pro větší pohodlí atribut trasy podporují *nahrazení tokenu* ohraničující token v čtvercových závorkách (`[`, `]`).</span><span class="sxs-lookup"><span data-stu-id="77c30-754">For convenience, attribute routes support *token replacement* by enclosing a token in square-braces (`[`, `]`).</span></span> <span data-ttu-id="77c30-755">Tokeny `[action]`, `[area]`a `[controller]` jsou nahrazeny hodnotami názvu akce, názvu oblasti a názvu řadiče z akce, kde je trasa definována.</span><span class="sxs-lookup"><span data-stu-id="77c30-755">The tokens `[action]`, `[area]`, and `[controller]` are replaced with the values of the action name, area name, and controller name from the action where the route is defined.</span></span> <span data-ttu-id="77c30-756">V následujícím příkladu akce odpovídají adresám URL, jak je popsáno v komentářích:</span><span class="sxs-lookup"><span data-stu-id="77c30-756">In the following example, the actions match URL paths as described in the comments:</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/ProductsController.cs?range=7-11,13-17,20-22)]

<span data-ttu-id="77c30-757">Nahrazení tokenu dochází jako poslední krok vytváření trasy atributu.</span><span class="sxs-lookup"><span data-stu-id="77c30-757">Token replacement occurs as the last step of building the attribute routes.</span></span> <span data-ttu-id="77c30-758">Výše uvedený příklad se bude chovat stejně jako následující kód:</span><span class="sxs-lookup"><span data-stu-id="77c30-758">The above example will behave the same as the following code:</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/ProductsController2.cs?range=7-11,13-17,20-22)]

<span data-ttu-id="77c30-759">Trasy atributů lze také kombinovat s dědičností.</span><span class="sxs-lookup"><span data-stu-id="77c30-759">Attribute routes can also be combined with inheritance.</span></span> <span data-ttu-id="77c30-760">To je obzvláště silný v kombinaci s token nahrazení.</span><span class="sxs-lookup"><span data-stu-id="77c30-760">This is particularly powerful combined with token replacement.</span></span>

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

<span data-ttu-id="77c30-761">Nahrazení tokenu platí také pro názvy tras definované postupy atributů.</span><span class="sxs-lookup"><span data-stu-id="77c30-761">Token replacement also applies to route names defined by attribute routes.</span></span> <span data-ttu-id="77c30-762">`[Route("[controller]/[action]", Name="[controller]_[action]")]`generuje jedinečný název trasy pro každou akci.</span><span class="sxs-lookup"><span data-stu-id="77c30-762">`[Route("[controller]/[action]", Name="[controller]_[action]")]` generates a unique route name for each action.</span></span>

<span data-ttu-id="77c30-763">Chcete-li odpovídat oddělovač `[` `]`nahrazení literálu tokenu`[[` `]]`nebo , uniknout opakováním znaku ( nebo ).</span><span class="sxs-lookup"><span data-stu-id="77c30-763">To match the literal token replacement delimiter `[` or  `]`, escape it by repeating the character (`[[` or `]]`).</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<a name="routing-token-replacement-transformers-ref-label"></a>

### <a name="use-a-parameter-transformer-to-customize-token-replacement"></a><span data-ttu-id="77c30-764">Přizpůsobení nahrazení tokenu pomocí transformátoru parametrů</span><span class="sxs-lookup"><span data-stu-id="77c30-764">Use a parameter transformer to customize token replacement</span></span>

<span data-ttu-id="77c30-765">Token nahrazení lze přizpůsobit pomocí parametrtransformátoru.</span><span class="sxs-lookup"><span data-stu-id="77c30-765">Token replacement can be customized using a parameter transformer.</span></span> <span data-ttu-id="77c30-766">Parametr transformátor `IOutboundParameterTransformer` implementuje a transformuje hodnotu parametrů.</span><span class="sxs-lookup"><span data-stu-id="77c30-766">A parameter transformer implements `IOutboundParameterTransformer` and transforms the value of parameters.</span></span> <span data-ttu-id="77c30-767">Například vlastní `SlugifyParameterTransformer` parametr transformátor `SubscriptionManagement` změní `subscription-management`hodnotu trasy na .</span><span class="sxs-lookup"><span data-stu-id="77c30-767">For example, a custom `SlugifyParameterTransformer` parameter transformer changes the `SubscriptionManagement` route value to `subscription-management`.</span></span>

<span data-ttu-id="77c30-768">Jedná `RouteTokenTransformerConvention` se o konvence modelu aplikace, která:</span><span class="sxs-lookup"><span data-stu-id="77c30-768">The `RouteTokenTransformerConvention` is an application model convention that:</span></span>

* <span data-ttu-id="77c30-769">Aplikuje parametr transformátor u všech tras atributů v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="77c30-769">Applies a parameter transformer to all attribute routes in an application.</span></span>
* <span data-ttu-id="77c30-770">Přizpůsobí hodnoty tokenu postupu atributu při jejich nahrazení.</span><span class="sxs-lookup"><span data-stu-id="77c30-770">Customizes the attribute route token values as they are replaced.</span></span>

```csharp
public class SubscriptionManagementController : Controller
{
    [HttpGet("[controller]/[action]")] // Matches '/subscription-management/list-all'
    public IActionResult ListAll() { ... }
}
```

<span data-ttu-id="77c30-771">Je `RouteTokenTransformerConvention` registrován jako možnost `ConfigureServices`v .</span><span class="sxs-lookup"><span data-stu-id="77c30-771">The `RouteTokenTransformerConvention` is registered as an option in `ConfigureServices`.</span></span>

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

### <a name="multiple-routes"></a><span data-ttu-id="77c30-772">Více tras</span><span class="sxs-lookup"><span data-stu-id="77c30-772">Multiple Routes</span></span>

<span data-ttu-id="77c30-773">Směrování atributů podporuje definování více tras, které dosáhnou stejné akce.</span><span class="sxs-lookup"><span data-stu-id="77c30-773">Attribute routing supports defining multiple routes that reach the same action.</span></span> <span data-ttu-id="77c30-774">Nejběžnější použití tohoto je napodobovat chování *výchozí konvenční trasy,* jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="77c30-774">The most common usage of this is to mimic the behavior of the *default conventional route* as shown in the following example:</span></span>

```csharp
[Route("[controller]")]
public class ProductsController : Controller
{
   [Route("")]     // Matches 'Products'
   [Route("Index")] // Matches 'Products/Index'
   public IActionResult Index()
}
```

<span data-ttu-id="77c30-775">Uvedení více atributů trasy na kontroleru znamená, že každý z nich bude kombinovat s každým z atributů trasy na metody akce.</span><span class="sxs-lookup"><span data-stu-id="77c30-775">Putting multiple route attributes on the controller means that each one will combine with each of the route attributes on the action methods.</span></span>

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

<span data-ttu-id="77c30-776">Pokud je do akce `IActionConstraint`umístěno více atributů trasy (které implementují), každé omezení akce se zkombinuje se šablonou trasy z atributu, který ji definoval.</span><span class="sxs-lookup"><span data-stu-id="77c30-776">When multiple route attributes (that implement `IActionConstraint`) are placed on an action, then each action constraint combines with the route template from the attribute that defined it.</span></span>

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
> <span data-ttu-id="77c30-777">Při použití více tras na akce se může zdát silný, je lepší, aby vaše aplikace URL prostor jednoduchý a dobře definovaný.</span><span class="sxs-lookup"><span data-stu-id="77c30-777">While using multiple routes on actions can seem powerful, it's better to keep your application's URL space simple and well-defined.</span></span> <span data-ttu-id="77c30-778">Více tras na akce používejte pouze v případě potřeby, například pro podporu stávajících klientů.</span><span class="sxs-lookup"><span data-stu-id="77c30-778">Use multiple routes on actions only where needed, for example to support existing clients.</span></span>

<a name="routing-attr-options"></a>

### <a name="specifying-attribute-route-optional-parameters-default-values-and-constraints"></a><span data-ttu-id="77c30-779">Určení volitelných parametrů, výchozích hodnot a omezení postupu atributu</span><span class="sxs-lookup"><span data-stu-id="77c30-779">Specifying attribute route optional parameters, default values, and constraints</span></span>

<span data-ttu-id="77c30-780">Trasy atributů podporují stejnou vložkovou syntaxi jako konvenční trasy pro určení volitelných parametrů, výchozích hodnot a omezení.</span><span class="sxs-lookup"><span data-stu-id="77c30-780">Attribute routes support the same inline syntax as conventional routes to specify optional parameters, default values, and constraints.</span></span>

```csharp
[HttpPost("product/{id:int}")]
public IActionResult ShowProduct(int id)
{
   // ...
}
```

<span data-ttu-id="77c30-781">Podrobný popis syntaxe šablony trasy najdete v tématu [Odkaz na šablonu](xref:fundamentals/routing#route-template-reference) trasy.</span><span class="sxs-lookup"><span data-stu-id="77c30-781">See [Route Template Reference](xref:fundamentals/routing#route-template-reference) for a detailed description of route template syntax.</span></span>

<a name="routing-cust-rt-attr-irt-ref-label"></a>

### <a name="custom-route-attributes-using-iroutetemplateprovider"></a><span data-ttu-id="77c30-782">Vlastní atributy trasy pomocí`IRouteTemplateProvider`</span><span class="sxs-lookup"><span data-stu-id="77c30-782">Custom route attributes using `IRouteTemplateProvider`</span></span>

<span data-ttu-id="77c30-783">Rozhraní implementují `[Route(...)]` `IRouteTemplateProvider` všechny atributy trasy `[HttpGet(...)]` uvedené v rámci ( , , atd.).</span><span class="sxs-lookup"><span data-stu-id="77c30-783">All of the route attributes provided in the framework ( `[Route(...)]`, `[HttpGet(...)]` , etc.) implement the `IRouteTemplateProvider` interface.</span></span> <span data-ttu-id="77c30-784">MVC hledá atributy na třídy kontroleru a metody akce při `IRouteTemplateProvider` spuštění aplikace a používá ty, které implementují k vytvoření počáteční sadu tras.</span><span class="sxs-lookup"><span data-stu-id="77c30-784">MVC looks for attributes on controller classes and action methods when the app starts and uses the ones that implement `IRouteTemplateProvider` to build the initial set of routes.</span></span>

<span data-ttu-id="77c30-785">Můžete implementovat `IRouteTemplateProvider` definovat vlastní atributy trasy.</span><span class="sxs-lookup"><span data-stu-id="77c30-785">You can implement `IRouteTemplateProvider` to define your own route attributes.</span></span> <span data-ttu-id="77c30-786">Každý `IRouteTemplateProvider` z nich umožňuje definovat jednu trasu s vlastní šablonou trasy, pořadím a názvem:</span><span class="sxs-lookup"><span data-stu-id="77c30-786">Each `IRouteTemplateProvider` allows you to define a single route with a custom route template, order, and name:</span></span>

```csharp
public class MyApiControllerAttribute : Attribute, IRouteTemplateProvider
{
   public string Template => "api/[controller]";

   public int? Order { get; set; }

   public string Name { get; set; }
}
```

<span data-ttu-id="77c30-787">Atribut z výše uvedeného `Template` příkladu automaticky nastaví na, `"api/[controller]"` kdy `[MyApiController]` je použita.</span><span class="sxs-lookup"><span data-stu-id="77c30-787">The attribute from the above example automatically sets the `Template` to `"api/[controller]"` when `[MyApiController]` is applied.</span></span>

<a name="routing-app-model-ref-label"></a>

### <a name="using-application-model-to-customize-attribute-routes"></a><span data-ttu-id="77c30-788">Přizpůsobení tras atributů pomocí modelu aplikace</span><span class="sxs-lookup"><span data-stu-id="77c30-788">Using Application Model to customize attribute routes</span></span>

<span data-ttu-id="77c30-789">*Aplikační model* je objektový model vytvořený při spuštění se všemi metadaty používanými mvc ke směrování a provádění vašich akcí.</span><span class="sxs-lookup"><span data-stu-id="77c30-789">The *application model* is an object model created at startup with all of the metadata used by MVC to route and execute your actions.</span></span> <span data-ttu-id="77c30-790">Aplikační *model* obsahuje všechna data shromážděná z `IRouteTemplateProvider`atributů trasy (přes).</span><span class="sxs-lookup"><span data-stu-id="77c30-790">The *application model* includes all of the data gathered from route attributes (through `IRouteTemplateProvider`).</span></span> <span data-ttu-id="77c30-791">Můžete napsat *konvence* upravit model aplikace při spuštění přizpůsobit, jak se chová směrování.</span><span class="sxs-lookup"><span data-stu-id="77c30-791">You can write *conventions* to modify the application model at startup time to customize how routing behaves.</span></span> <span data-ttu-id="77c30-792">Tato část ukazuje jednoduchý příklad přizpůsobení směrování pomocí aplikačního modelu.</span><span class="sxs-lookup"><span data-stu-id="77c30-792">This section shows a simple example of customizing routing using application model.</span></span>

[!code-csharp[](routing/samples/2.x/main/NamespaceRoutingConvention.cs)]

<a name="routing-mixed-ref-label"></a>

## <a name="mixed-routing-attribute-routing-vs-conventional-routing"></a><span data-ttu-id="77c30-793">Smíšené směrování: Směrování atributů vs konvenční směrování</span><span class="sxs-lookup"><span data-stu-id="77c30-793">Mixed routing: Attribute routing vs conventional routing</span></span>

<span data-ttu-id="77c30-794">Aplikace MVC mohou kombinovat použití konvenčního směrování a směrování atributů.</span><span class="sxs-lookup"><span data-stu-id="77c30-794">MVC applications can mix the use of conventional routing and attribute routing.</span></span> <span data-ttu-id="77c30-795">Je typické používat konvenční trasy pro řadiče obsluhující stránky HTML pro prohlížeče a směrování atributů pro řadiče obsluhující rest API.</span><span class="sxs-lookup"><span data-stu-id="77c30-795">It's typical to use conventional routes for controllers serving HTML pages for browsers, and attribute routing for controllers serving REST APIs.</span></span>

<span data-ttu-id="77c30-796">Akce jsou buď konvenčně směrovány nebo atribut směrovány.</span><span class="sxs-lookup"><span data-stu-id="77c30-796">Actions are either conventionally routed or attribute routed.</span></span> <span data-ttu-id="77c30-797">Umístění trasy na řadič nebo akce způsobí, že atribut směrovány.</span><span class="sxs-lookup"><span data-stu-id="77c30-797">Placing a route on the controller or the action makes it attribute routed.</span></span> <span data-ttu-id="77c30-798">Akce, které definují trasy atributů nelze dosáhnout prostřednictvím konvenční trasy a naopak.</span><span class="sxs-lookup"><span data-stu-id="77c30-798">Actions that define attribute routes cannot be reached through the conventional routes and vice-versa.</span></span> <span data-ttu-id="77c30-799">**Libovolný** atribut trasy na řadiči provede všechny akce v atributu kontroleru směrovány.</span><span class="sxs-lookup"><span data-stu-id="77c30-799">**Any** route attribute on the controller makes all actions in the controller attribute routed.</span></span>

> [!NOTE]
> <span data-ttu-id="77c30-800">Odlišuje dva typy systémů směrování proces použitý poté, co adresa URL odpovídá šabloně trasy.</span><span class="sxs-lookup"><span data-stu-id="77c30-800">What distinguishes the two types of routing systems is the process applied after a URL matches a route template.</span></span> <span data-ttu-id="77c30-801">V konvenčním směrování se hodnoty trasy ze shody používají k výběru akce a kontroleru z vyhledávací tabulky všech konvenčních směrovaných akcí.</span><span class="sxs-lookup"><span data-stu-id="77c30-801">In conventional routing, the route values from the match are used to choose the action and controller from a lookup table of all conventional routed actions.</span></span> <span data-ttu-id="77c30-802">V směrování atributů je každá šablona již přidružena k akci a není potřeba žádné další vyhledávání.</span><span class="sxs-lookup"><span data-stu-id="77c30-802">In attribute routing, each template is already associated with an action, and no further lookup is needed.</span></span>

## <a name="complex-segments"></a><span data-ttu-id="77c30-803">Složité segmenty</span><span class="sxs-lookup"><span data-stu-id="77c30-803">Complex segments</span></span>

<span data-ttu-id="77c30-804">Složité segmenty (například `[Route("/dog{token}cat")]`), jsou zpracovány porovnáním literály zprava doleva nechamtivým způsobem.</span><span class="sxs-lookup"><span data-stu-id="77c30-804">Complex segments (for example, `[Route("/dog{token}cat")]`), are processed by matching up literals from right to left in a non-greedy way.</span></span> <span data-ttu-id="77c30-805">Popis [naleznete ve zdrojovém kódu.](https://github.com/aspnet/Routing/blob/9cea167cfac36cf034dbb780e3f783114ef94780/src/Microsoft.AspNetCore.Routing/Patterns/RoutePatternMatcher.cs#L296)</span><span class="sxs-lookup"><span data-stu-id="77c30-805">See [the source code](https://github.com/aspnet/Routing/blob/9cea167cfac36cf034dbb780e3f783114ef94780/src/Microsoft.AspNetCore.Routing/Patterns/RoutePatternMatcher.cs#L296) for a description.</span></span> <span data-ttu-id="77c30-806">Další informace naleznete v [tomto problému](https://github.com/dotnet/AspNetCore.Docs/issues/8197).</span><span class="sxs-lookup"><span data-stu-id="77c30-806">For more information, see [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/8197).</span></span>

<a name="routing-url-gen-ref-label"></a>

## <a name="url-generation"></a><span data-ttu-id="77c30-807">Generování adresy URL</span><span class="sxs-lookup"><span data-stu-id="77c30-807">URL Generation</span></span>

<span data-ttu-id="77c30-808">Aplikace MVC mohou pomocí funkcí generování adres URL směrování generovat odkazy na adresy URL na akce.</span><span class="sxs-lookup"><span data-stu-id="77c30-808">MVC applications can use routing's URL generation features to generate URL links to actions.</span></span> <span data-ttu-id="77c30-809">Generování adres URL eliminuje pevné kódování adres URL, takže váš kód robustnější a udržovatelné.</span><span class="sxs-lookup"><span data-stu-id="77c30-809">Generating URLs eliminates hardcoding URLs, making your code more robust and maintainable.</span></span> <span data-ttu-id="77c30-810">Tato část se zaměřuje na funkce generování adres URL poskytované MVC a bude se týkat pouze základy fungování generování adres URL.</span><span class="sxs-lookup"><span data-stu-id="77c30-810">This section focuses on the URL generation features provided by MVC and will only cover basics of how URL generation works.</span></span> <span data-ttu-id="77c30-811">Podrobný popis generování adres URL naleznete v tématu [Směrování.](xref:fundamentals/routing)</span><span class="sxs-lookup"><span data-stu-id="77c30-811">See [Routing](xref:fundamentals/routing) for a detailed description of URL generation.</span></span>

<span data-ttu-id="77c30-812">Rozhraní `IUrlHelper` je základní část infrastruktury mezi MVC a směrování pro generování adres URL.</span><span class="sxs-lookup"><span data-stu-id="77c30-812">The `IUrlHelper` interface is the underlying piece of infrastructure between MVC and routing for URL generation.</span></span> <span data-ttu-id="77c30-813">Najdete instanci `IUrlHelper` k dispozici `Url` prostřednictvím vlastnosti v kontrolerně, zobrazení a komponenty zobrazení.</span><span class="sxs-lookup"><span data-stu-id="77c30-813">You'll find an instance of `IUrlHelper` available through the `Url` property in controllers, views, and view components.</span></span>

<span data-ttu-id="77c30-814">V tomto příkladu `IUrlHelper` rozhraní se `Controller.Url` používá prostřednictvím vlastnosti ke generování adresy URL na jinou akci.</span><span class="sxs-lookup"><span data-stu-id="77c30-814">In this example, the `IUrlHelper` interface is used through the `Controller.Url` property to generate a URL to another action.</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationController.cs?name=snippet_1)]

<span data-ttu-id="77c30-815">Pokud aplikace používá výchozí konvenční trasu, `url` bude hodnotou proměnné `/UrlGeneration/Destination`řetězec cesty URL .</span><span class="sxs-lookup"><span data-stu-id="77c30-815">If the application is using the default conventional route, the value of the `url` variable will be the URL path string `/UrlGeneration/Destination`.</span></span> <span data-ttu-id="77c30-816">Tato cesta URL je vytvořena směrováním kombinací hodnot trasy z aktuálního požadavku `Url.Action` (hodnoty okolí) s hodnotami předanými a nahrazením těchto hodnot do šablony postupu:</span><span class="sxs-lookup"><span data-stu-id="77c30-816">This URL path is created by routing by combining the route values from the current request (ambient values), with the values passed to `Url.Action` and substituting those values into the route template:</span></span>

```
ambient values: { controller = "UrlGeneration", action = "Source" }
values passed to Url.Action: { controller = "UrlGeneration", action = "Destination" }
route template: {controller}/{action}/{id?}

result: /UrlGeneration/Destination
```

<span data-ttu-id="77c30-817">Každý parametr trasy v šabloně trasy má svou hodnotu nahrazenou sladěnými názvy s hodnotami a hodnotami okolí.</span><span class="sxs-lookup"><span data-stu-id="77c30-817">Each route parameter in the route template has its value substituted by matching names with the values and ambient values.</span></span> <span data-ttu-id="77c30-818">Parametr trasy, který nemá hodnotu, může použít výchozí hodnotu, pokud ji má, nebo být přeskočen, pokud je volitelný (jako v tomto příkladu). `id`</span><span class="sxs-lookup"><span data-stu-id="77c30-818">A route parameter that doesn't have a value can use a default value if it has one, or be skipped if it's optional (as in the case of `id` in this example).</span></span> <span data-ttu-id="77c30-819">Generování adresy URL se nezdaří, pokud žádný parametr požadované trasy nemá odpovídající hodnotu.</span><span class="sxs-lookup"><span data-stu-id="77c30-819">URL generation will fail if any required route parameter doesn't have a corresponding value.</span></span> <span data-ttu-id="77c30-820">Pokud se generování adresy URL nezdaří pro trasu, další trasa je vyzkoušena, dokud nebyly vyzkoušeny všechny trasy nebo nalezena shoda.</span><span class="sxs-lookup"><span data-stu-id="77c30-820">If URL generation fails for a route, the next route is tried until all routes have been tried or a match is found.</span></span>

<span data-ttu-id="77c30-821">Příklad `Url.Action` výše předpokládá konvenční směrování, ale generování adres URL funguje podobně s směrováním atributů, i když koncepty se liší.</span><span class="sxs-lookup"><span data-stu-id="77c30-821">The example of `Url.Action` above assumes conventional routing, but URL generation works similarly with attribute routing, though the concepts are different.</span></span> <span data-ttu-id="77c30-822">Při konvenčním směrování se hodnoty postupu používají k rozbalení `controller` `action` šablony a hodnoty postupu pro tuto šablonu a obvykle se v ní zobrazují – funguje to proto, že adresy URL odpovídající směrování dodržují *konvence*.</span><span class="sxs-lookup"><span data-stu-id="77c30-822">With conventional routing, the route values are used to expand a template, and the route values for `controller` and `action` usually appear in that template - this works because the URLs matched by routing adhere to a *convention*.</span></span> <span data-ttu-id="77c30-823">V směrování atributů se `controller` `action` hodnoty postupu pro šablonu a nejsou v ní nesmí zobrazovat – místo toho se používají k vyhledání šablony, která se má použít.</span><span class="sxs-lookup"><span data-stu-id="77c30-823">In attribute routing, the route values for `controller` and `action` are not allowed to appear in the template - they're instead used to look up which template to use.</span></span>

<span data-ttu-id="77c30-824">Tento příklad používá směrování atributů:</span><span class="sxs-lookup"><span data-stu-id="77c30-824">This example uses attribute routing:</span></span>

[!code-csharp[](routing/samples/2.x/main/StartupUseMvc.cs?name=snippet_1)]

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationControllerAttr.cs?name=snippet_1)]

<span data-ttu-id="77c30-825">MVC vytvoří vyhledávací tabulku všech směrovaných akcí atributu `controller` `action` a bude odpovídat hodnotám a, které mají být vyvoleny, aby se šablona trasy použila pro generování adresy URL.</span><span class="sxs-lookup"><span data-stu-id="77c30-825">MVC builds a lookup table of all attribute routed actions and will match the `controller` and `action` values to select the route template to use for URL generation.</span></span> <span data-ttu-id="77c30-826">Ve výše uvedeném vzorku `custom/url/to/destination` je generován.</span><span class="sxs-lookup"><span data-stu-id="77c30-826">In the sample above,   `custom/url/to/destination` is generated.</span></span>

### <a name="generating-urls-by-action-name"></a><span data-ttu-id="77c30-827">Generování adres URL podle názvu akce</span><span class="sxs-lookup"><span data-stu-id="77c30-827">Generating URLs by action name</span></span>

<span data-ttu-id="77c30-828">`Url.Action` (`IUrlHelper` .</span><span class="sxs-lookup"><span data-stu-id="77c30-828">`Url.Action` (`IUrlHelper` .</span></span> <span data-ttu-id="77c30-829">`Action`) a všechna související přetížení jsou založena na této myšlence, že chcete určit, na co odkazujete zadáním názvu řadiče a názvu akce.</span><span class="sxs-lookup"><span data-stu-id="77c30-829">`Action`) and all related overloads all are based on that idea that you want to specify what you're linking to by specifying a controller name and action name.</span></span>

> [!NOTE]
> <span data-ttu-id="77c30-830">Při `Url.Action`použití jsou aktuální `controller` hodnoty `action` trasy pro vás zadány a jsou určeny `controller` pro vás - hodnota a `action` jsou součástí hodnot *okolí* **i** *hodnot*.</span><span class="sxs-lookup"><span data-stu-id="77c30-830">When using `Url.Action`, the current route values for `controller` and `action` are specified for you - the value of `controller` and `action` are part of both *ambient values* **and** *values*.</span></span> <span data-ttu-id="77c30-831">Metoda `Url.Action`, vždy používá aktuální `action` `controller` hodnoty a a bude generovat cestu URL, která vede k aktuální akci.</span><span class="sxs-lookup"><span data-stu-id="77c30-831">The method `Url.Action`, always uses the current values of `action` and `controller` and will generate a URL path that routes to the current action.</span></span>

<span data-ttu-id="77c30-832">Směrování se pokusí použít hodnoty v okolních hodnotách k vyplnění informací, které jste neposkytli při generování adresy URL.</span><span class="sxs-lookup"><span data-stu-id="77c30-832">Routing attempts to use the values in ambient values to fill in information that you didn't provide when generating a URL.</span></span> <span data-ttu-id="77c30-833">Pomocí trasy `{a}/{b}/{c}/{d}` jako a `{ a = Alice, b = Bob, c = Carol, d = David }`okolní hodnoty , směrování má dostatek informací pro generování URL bez dalších hodnot - protože všechny parametry trasy mají hodnotu.</span><span class="sxs-lookup"><span data-stu-id="77c30-833">Using a route like `{a}/{b}/{c}/{d}` and ambient values `{ a = Alice, b = Bob, c = Carol, d = David }`, routing has enough information to generate a URL without any additional values - since all route parameters have a value.</span></span> <span data-ttu-id="77c30-834">Pokud jste přidali hodnotu `{ d = Donovan }`, bude hodnota `{ d = David }` ignorována a `Alice/Bob/Carol/Donovan`vygenerovaná cesta url bude .</span><span class="sxs-lookup"><span data-stu-id="77c30-834">If you added the value `{ d = Donovan }`, the value `{ d = David }` would be ignored, and the generated URL path would be `Alice/Bob/Carol/Donovan`.</span></span>

> [!WARNING]
> <span data-ttu-id="77c30-835">Cesty adres URL jsou hierarchické.</span><span class="sxs-lookup"><span data-stu-id="77c30-835">URL paths are hierarchical.</span></span> <span data-ttu-id="77c30-836">Ve výše uvedeném příkladu, `{ c = Cheryl }`pokud jste `{ c = Carol, d = David }` přidali hodnotu , budou obě hodnoty ignorovány.</span><span class="sxs-lookup"><span data-stu-id="77c30-836">In the example above, if you added the value `{ c = Cheryl }`, both of the values `{ c = Carol, d = David }` would be ignored.</span></span> <span data-ttu-id="77c30-837">V tomto případě již nemáme `d` hodnotu pro a generování adresy URL se nezdaří.</span><span class="sxs-lookup"><span data-stu-id="77c30-837">In this case we no longer have a value for `d` and URL generation will fail.</span></span> <span data-ttu-id="77c30-838">Budete muset zadat požadovanou `c` hodnotu a `d`.</span><span class="sxs-lookup"><span data-stu-id="77c30-838">You would need to specify the desired value of `c` and `d`.</span></span>  <span data-ttu-id="77c30-839">Můžete očekávat, že tento problém s`{controller}/{action}/{id?}`výchozí trasu ( ) - ale `Url.Action` zřídka narazíte `controller` `action` na toto chování v praxi, jak bude vždy explicitně zadat a hodnotu.</span><span class="sxs-lookup"><span data-stu-id="77c30-839">You might expect to hit this problem with the default route (`{controller}/{action}/{id?}`) - but you will rarely encounter this behavior in practice as `Url.Action` will always explicitly specify a `controller` and `action` value.</span></span>

<span data-ttu-id="77c30-840">Delší přetížení `Url.Action` také trvat další *hodnoty trasy* objektu poskytnout `controller` hodnoty `action`pro parametry trasy než a .</span><span class="sxs-lookup"><span data-stu-id="77c30-840">Longer overloads of `Url.Action` also take an additional *route values* object to provide values for route parameters other than `controller` and `action`.</span></span> <span data-ttu-id="77c30-841">Nejčastěji se zobrazí toto `id` `Url.Action("Buy", "Products", new { id = 17 })`používá s like .</span><span class="sxs-lookup"><span data-stu-id="77c30-841">You will most commonly see this used with `id` like `Url.Action("Buy", "Products", new { id = 17 })`.</span></span> <span data-ttu-id="77c30-842">Podle konvence je objekt *hodnot trasy* obvykle objektem anonymního `IDictionary<>` typu, ale může se také jedná o prostý *starý objekt .NET*.</span><span class="sxs-lookup"><span data-stu-id="77c30-842">By convention the *route values* object is usually an object of anonymous type, but it can also be an `IDictionary<>` or a *plain old .NET object*.</span></span> <span data-ttu-id="77c30-843">Všechny další hodnoty trasy, které neodpovídají parametrům trasy, jsou vloženy do řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="77c30-843">Any additional route values that don't match route parameters are put in the query string.</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/TestController.cs)]

> [!TIP]
> <span data-ttu-id="77c30-844">Chcete-li vytvořit absolutní adresu URL, `protocol`použijte přetížení, které přijímá :`Url.Action("Buy", "Products", new { id = 17 }, protocol: Request.Scheme)`</span><span class="sxs-lookup"><span data-stu-id="77c30-844">To create an absolute URL, use an overload that accepts a `protocol`: `Url.Action("Buy", "Products", new { id = 17 }, protocol: Request.Scheme)`</span></span>

<a name="routing-gen-urls-route-ref-label"></a>

### <a name="generating-urls-by-route"></a><span data-ttu-id="77c30-845">Generování adres URL podle trasy</span><span class="sxs-lookup"><span data-stu-id="77c30-845">Generating URLs by route</span></span>

<span data-ttu-id="77c30-846">Výše uvedený kód demonstroval generování adresy URL předáním v řadiči a název akce.</span><span class="sxs-lookup"><span data-stu-id="77c30-846">The code above demonstrated generating a URL by passing in the controller and action name.</span></span> <span data-ttu-id="77c30-847">`IUrlHelper`také poskytuje `Url.RouteUrl` rodinu metod.</span><span class="sxs-lookup"><span data-stu-id="77c30-847">`IUrlHelper` also provides the `Url.RouteUrl` family of methods.</span></span> <span data-ttu-id="77c30-848">Tyto metody jsou `Url.Action`podobné , ale nekopírují `action` `controller` aktuální hodnoty a hodnoty postupu.</span><span class="sxs-lookup"><span data-stu-id="77c30-848">These methods are similar to `Url.Action`, but they don't copy the current values of `action` and `controller` to the route values.</span></span> <span data-ttu-id="77c30-849">Nejběžnější použití je zadat název trasy pro použití určité trasy pro generování adresy URL, obecně *bez* zadání názvu řadiče nebo akce.</span><span class="sxs-lookup"><span data-stu-id="77c30-849">The most common usage is to specify a route name to use a specific route to generate the URL, generally *without* specifying a controller or action name.</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationControllerRouting.cs?name=snippet_1)]

<a name="routing-gen-urls-html-ref-label"></a>

### <a name="generating-urls-in-html"></a><span data-ttu-id="77c30-850">Generování adres URL v HTML</span><span class="sxs-lookup"><span data-stu-id="77c30-850">Generating URLs in HTML</span></span>

<span data-ttu-id="77c30-851">`IHtmlHelper`poskytuje `HtmlHelper` metody `Html.BeginForm` `Html.ActionLink` a `<form>` vytvářet a `<a>` prvky.</span><span class="sxs-lookup"><span data-stu-id="77c30-851">`IHtmlHelper` provides the `HtmlHelper` methods `Html.BeginForm` and `Html.ActionLink` to generate `<form>` and `<a>` elements respectively.</span></span> <span data-ttu-id="77c30-852">Tyto metody `Url.Action` používají metodu ke generování adresy URL a přijímají podobné argumenty.</span><span class="sxs-lookup"><span data-stu-id="77c30-852">These methods use the `Url.Action` method to generate a URL and they accept similar arguments.</span></span> <span data-ttu-id="77c30-853">`Url.RouteUrl` Společníci `HtmlHelper` `Html.BeginRouteForm` pro `Html.RouteLink` jsou a které mají podobné funkce.</span><span class="sxs-lookup"><span data-stu-id="77c30-853">The `Url.RouteUrl` companions for `HtmlHelper` are `Html.BeginRouteForm` and `Html.RouteLink` which have similar functionality.</span></span>

<span data-ttu-id="77c30-854">TagHelpers generovat adresy URL `form` prostřednictvím TagHelper a `<a>` TagHelper.</span><span class="sxs-lookup"><span data-stu-id="77c30-854">TagHelpers generate URLs through the `form` TagHelper and the `<a>` TagHelper.</span></span> <span data-ttu-id="77c30-855">Obě tyto `IUrlHelper` použití pro jejich realizaci.</span><span class="sxs-lookup"><span data-stu-id="77c30-855">Both of these use `IUrlHelper` for their implementation.</span></span> <span data-ttu-id="77c30-856">Další informace naleznete [v tématu Práce s formuláři.](../views/working-with-forms.md)</span><span class="sxs-lookup"><span data-stu-id="77c30-856">See [Working with Forms](../views/working-with-forms.md) for more information.</span></span>

<span data-ttu-id="77c30-857">Vnitřní zobrazení, `IUrlHelper` je k `Url` dispozici prostřednictvím vlastnosti pro všechny ad-hoc URL generace, které nejsou zahrnuty výše.</span><span class="sxs-lookup"><span data-stu-id="77c30-857">Inside views, the `IUrlHelper` is available through the `Url` property for any ad-hoc URL generation not covered by the above.</span></span>

<a name="routing-gen-urls-action-ref-label"></a>

### <a name="generating-urls-in-action-results"></a><span data-ttu-id="77c30-858">Generování adres URL ve výsledcích akce</span><span class="sxs-lookup"><span data-stu-id="77c30-858">Generating URLS in Action Results</span></span>

<span data-ttu-id="77c30-859">Výše uvedené příklady `IUrlHelper` ukázaly použití v řadiči, zatímco nejběžnější použití v řadiči je generovat adresu URL jako součást výsledku akce.</span><span class="sxs-lookup"><span data-stu-id="77c30-859">The examples above have shown using `IUrlHelper` in a controller, while the most common usage in a controller is to generate a URL as part of an action result.</span></span>

<span data-ttu-id="77c30-860">A `ControllerBase` `Controller` základní třídy poskytují komfortní metody pro výsledky akce, které odkazují na jinou akci.</span><span class="sxs-lookup"><span data-stu-id="77c30-860">The `ControllerBase` and `Controller` base classes provide convenience methods for action results that reference another action.</span></span> <span data-ttu-id="77c30-861">Jedním z typických použití je přesměrování po přijetí vstupu uživatele.</span><span class="sxs-lookup"><span data-stu-id="77c30-861">One typical usage is to redirect after accepting user input.</span></span>

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

<span data-ttu-id="77c30-862">Výsledky akce factory metody postupujte podobný `IUrlHelper`vzor metody na .</span><span class="sxs-lookup"><span data-stu-id="77c30-862">The action results factory methods follow a similar pattern to the methods on `IUrlHelper`.</span></span>

<a name="routing-dedicated-ref-label"></a>

### <a name="special-case-for-dedicated-conventional-routes"></a><span data-ttu-id="77c30-863">Zvláštní případ pro vyhrazené konvenční trasy</span><span class="sxs-lookup"><span data-stu-id="77c30-863">Special case for dedicated conventional routes</span></span>

<span data-ttu-id="77c30-864">Konvenční trasa může používat speciální druh definice trasy nazývanou *vyhrazená konvenční trasa*.</span><span class="sxs-lookup"><span data-stu-id="77c30-864">Conventional routing can use a special kind of route definition called a *dedicated conventional route*.</span></span> <span data-ttu-id="77c30-865">V níže uvedeném příkladu je trasa s názvem `blog` vyhrazená konvenční trasa.</span><span class="sxs-lookup"><span data-stu-id="77c30-865">In the example below, the route named `blog` is a dedicated conventional route.</span></span>

```csharp
app.UseMvc(routes =>
{
    routes.MapRoute("blog", "blog/{*article}",
        defaults: new { controller = "Blog", action = "Article" });
    routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="77c30-866">Pomocí těchto definic `Url.Action("Index", "Home")` tras vygeneruje `/` cestu `default` URL s trasou, ale proč?</span><span class="sxs-lookup"><span data-stu-id="77c30-866">Using these route definitions, `Url.Action("Index", "Home")` will generate the URL path `/` with the `default` route, but why?</span></span> <span data-ttu-id="77c30-867">Můžete uhodnout, `{ controller = Home, action = Index }` že hodnoty trasy by `blog`stačily ke generování `/blog?action=Index&controller=Home`adresy URL pomocí a výsledkem by bylo .</span><span class="sxs-lookup"><span data-stu-id="77c30-867">You might guess the route values `{ controller = Home, action = Index }` would be enough to generate a URL using `blog`, and the result would be `/blog?action=Index&controller=Home`.</span></span>

<span data-ttu-id="77c30-868">Vyhrazené konvenční trasy spoléhají na speciální chování výchozích hodnot, které nemají odpovídající parametr trasy, který brání tomu, aby trasa byla "příliš hladová" s generováním adresy URL.</span><span class="sxs-lookup"><span data-stu-id="77c30-868">Dedicated conventional routes rely on a special behavior of default values that don't have a corresponding route parameter that prevents the route from being "too greedy" with URL generation.</span></span> <span data-ttu-id="77c30-869">V tomto případě jsou `{ controller = Blog, action = Article }`výchozí hodnoty `controller` `action` a jako parametr trasy se nezobrazují ani se nezobrazují.</span><span class="sxs-lookup"><span data-stu-id="77c30-869">In this case the default values are `{ controller = Blog, action = Article }`, and neither `controller` nor `action` appears as a route parameter.</span></span> <span data-ttu-id="77c30-870">Při směrování provádí generování adresy URL, zadaná hodnota musí odpovídat výchozí hodnoty.</span><span class="sxs-lookup"><span data-stu-id="77c30-870">When routing performs URL generation, the values provided must match the default values.</span></span> <span data-ttu-id="77c30-871">Generování adresy `blog` URL pomocí `{ controller = Home, action = Index }` se nezdaří, protože hodnoty se neshodují `{ controller = Blog, action = Article }`.</span><span class="sxs-lookup"><span data-stu-id="77c30-871">URL generation using `blog` will fail because the values `{ controller = Home, action = Index }` don't match `{ controller = Blog, action = Article }`.</span></span> <span data-ttu-id="77c30-872">Směrování pak přejde `default`zpět vyzkoušet , který je úspěšný.</span><span class="sxs-lookup"><span data-stu-id="77c30-872">Routing then falls back to try `default`, which succeeds.</span></span>

<a name="routing-areas-ref-label"></a>

## <a name="areas"></a><span data-ttu-id="77c30-873">Oblasti</span><span class="sxs-lookup"><span data-stu-id="77c30-873">Areas</span></span>

<span data-ttu-id="77c30-874">[Oblasti](areas.md) jsou funkce MVC, která slouží k uspořádání souvisejících funkcí do skupiny jako samostatný obor názvů směrování (pro akce řadiče) a strukturu složek (pro zobrazení).</span><span class="sxs-lookup"><span data-stu-id="77c30-874">[Areas](areas.md) are an MVC feature used to organize related functionality into a group as a separate routing-namespace (for controller actions) and folder structure (for views).</span></span> <span data-ttu-id="77c30-875">Použití oblastí umožňuje aplikaci mít více řadičů se stejným názvem - pokud mají různé *oblasti*.</span><span class="sxs-lookup"><span data-stu-id="77c30-875">Using areas allows an application to have multiple controllers with the same name - as long as they have different *areas*.</span></span> <span data-ttu-id="77c30-876">Použití oblastí vytvoří hierarchii pro účely směrování přidáním `controller` `action`dalšího parametru trasy `area` do a .</span><span class="sxs-lookup"><span data-stu-id="77c30-876">Using areas creates a hierarchy for the purpose of routing by adding another route parameter, `area` to `controller` and `action`.</span></span> <span data-ttu-id="77c30-877">Tato část bude pojednávat o tom, jak směrování spolupracuje s oblastmi – podrobnosti o tom, jak se oblasti používají se zobrazeními, najdete v článku [Oblasti.](areas.md)</span><span class="sxs-lookup"><span data-stu-id="77c30-877">This section will discuss how routing interacts with areas - see [Areas](areas.md) for details about how areas are used with views.</span></span>

<span data-ttu-id="77c30-878">Následující příklad konfiguruje mvc pro použití výchozí konvenční `Blog`trasy a trasy *oblasti* pro oblast s názvem :</span><span class="sxs-lookup"><span data-stu-id="77c30-878">The following example configures MVC to use the default conventional route and an *area route* for an area named `Blog`:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet1)]

<span data-ttu-id="77c30-879">Při porovnávání cesty `/Manage/Users/AddUser`URL , jako je `{ area = Blog, controller = Users, action = AddUser }`první trasa, vytvoří hodnoty trasy .</span><span class="sxs-lookup"><span data-stu-id="77c30-879">When matching a URL path like `/Manage/Users/AddUser`, the first route will produce the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span> <span data-ttu-id="77c30-880">Hodnota `area` postupu je vytvořena výchozí `area`hodnotou pro aplikaci `MapAreaRoute` , ve skutečnosti je trasa vytvořená ekvivalentní následujícímu:</span><span class="sxs-lookup"><span data-stu-id="77c30-880">The `area` route value is produced by a default value for `area`, in fact the route created by `MapAreaRoute` is equivalent to the following:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet2)]

<span data-ttu-id="77c30-881">`MapAreaRoute`vytvoří trasu pomocí výchozí hodnoty `area` i omezení pro použití zadaný `Blog`název oblasti, v tomto případě .</span><span class="sxs-lookup"><span data-stu-id="77c30-881">`MapAreaRoute` creates a route using both a default value and constraint for `area` using the provided area name, in this case `Blog`.</span></span> <span data-ttu-id="77c30-882">Výchozí hodnota zajišťuje, že trasa `{ area = Blog, ... }`vždy vytváří , `{ area = Blog, ... }` omezení vyžaduje hodnotu pro generování adresy URL.</span><span class="sxs-lookup"><span data-stu-id="77c30-882">The default value ensures that the route always produces `{ area = Blog, ... }`, the constraint requires the value `{ area = Blog, ... }` for URL generation.</span></span>

> [!TIP]
> <span data-ttu-id="77c30-883">Konvenční směrování je závislé na objednávce.</span><span class="sxs-lookup"><span data-stu-id="77c30-883">Conventional routing is order-dependent.</span></span> <span data-ttu-id="77c30-884">Obecně platí, že trasy s oblastmi by měly být umístěny dříve v tabulce tras, protože jsou konkrétnější než trasy bez oblasti.</span><span class="sxs-lookup"><span data-stu-id="77c30-884">In general, routes with areas should be placed earlier in the route table as they're more specific than routes without an area.</span></span>

<span data-ttu-id="77c30-885">Pomocí výše uvedeného příkladu by hodnoty trasy odpovídaly následující akci:</span><span class="sxs-lookup"><span data-stu-id="77c30-885">Using the above example, the route values would match the following action:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

<span data-ttu-id="77c30-886">To `AreaAttribute` je to, co označuje regulátor jako součást oblasti, říkáme, že tento regulátor je v `Blog` této oblasti.</span><span class="sxs-lookup"><span data-stu-id="77c30-886">The `AreaAttribute` is what denotes a controller as part of an area, we say that this controller is in the `Blog` area.</span></span> <span data-ttu-id="77c30-887">Kontroloři `[Area]` bez atributu nejsou členy žádné oblasti a `area` **nebudou** odpovídat, pokud je hodnota postupu poskytnuta směrováním.</span><span class="sxs-lookup"><span data-stu-id="77c30-887">Controllers without an `[Area]` attribute are not members of any area, and will **not** match when the `area` route value is provided by routing.</span></span> <span data-ttu-id="77c30-888">V následujícím příkladu může hodnoty `{ area = Blog, controller = Users, action = AddUser }`trasy odpovídat pouze prvnímu uvedenému řadiči .</span><span class="sxs-lookup"><span data-stu-id="77c30-888">In the following example, only the first controller listed can match the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Zebra/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/UsersController.cs)]

> [!NOTE]
> <span data-ttu-id="77c30-889">Obor názvů každého řadiče je zde zobrazen pro úplnost - jinak řadiče by měly konflikt názvů a generovaly chybu kompilátoru.</span><span class="sxs-lookup"><span data-stu-id="77c30-889">The namespace of each controller is shown here for completeness - otherwise the controllers would have a naming conflict and generate a compiler error.</span></span> <span data-ttu-id="77c30-890">Obory názvů tříd nemají žádný vliv na směrování MVC.</span><span class="sxs-lookup"><span data-stu-id="77c30-890">Class namespaces have no effect on MVC's routing.</span></span>

<span data-ttu-id="77c30-891">První dva řadiče jsou členy oblastí a shodují se pouze v `area` případě, že jejich název oblasti je zajišťován hodnotou trasy.</span><span class="sxs-lookup"><span data-stu-id="77c30-891">The first two controllers are members of areas, and only match when their respective area name is provided by the `area` route value.</span></span> <span data-ttu-id="77c30-892">Třetí řadič není členem žádné oblasti a může se shodovat pouze v případě, že směrováním neposkytuje žádnou hodnotu pro. `area`</span><span class="sxs-lookup"><span data-stu-id="77c30-892">The third controller isn't a member of any area, and can only match when no value for `area` is provided by routing.</span></span>

> [!NOTE]
> <span data-ttu-id="77c30-893">Pokud jde o odpovídající *žádnou hodnotu*, absence `area` hodnoty `area` je stejná, jako kdyby hodnota pro byly null nebo prázdný řetězec.</span><span class="sxs-lookup"><span data-stu-id="77c30-893">In terms of matching *no value*, the absence of the `area` value is the same as if the value for `area` were null or the empty string.</span></span>

<span data-ttu-id="77c30-894">Při provádění akce uvnitř oblasti bude hodnota `area` trasy pro k dispozici jako *okolní hodnota* pro směrování, která se použije pro generování adresy URL.</span><span class="sxs-lookup"><span data-stu-id="77c30-894">When executing an action inside an area, the route value for `area` will be available as an *ambient value* for routing to use for URL generation.</span></span> <span data-ttu-id="77c30-895">To znamená, že ve výchozím nastavení oblasti jednat *sticky* pro generování adresy URL, jak ukazuje následující ukázka.</span><span class="sxs-lookup"><span data-stu-id="77c30-895">This means that by default areas act *sticky* for URL generation as demonstrated by the following sample.</span></span>
[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet3)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Duck/Controllers/UsersController.cs)]

<a name="iactionconstraint-ref-label"></a>

## <a name="understanding-iactionconstraint"></a><span data-ttu-id="77c30-896">Principy omezení iaction</span><span class="sxs-lookup"><span data-stu-id="77c30-896">Understanding IActionConstraint</span></span>

> [!NOTE]
> <span data-ttu-id="77c30-897">Tato část je podrobné prolomení na vnitřní rozhraní a jak MVC vybere akci k provedení.</span><span class="sxs-lookup"><span data-stu-id="77c30-897">This section is a deep-dive on framework internals and how MVC chooses an action to execute.</span></span> <span data-ttu-id="77c30-898">Typická aplikace nebude potřebovat vlastní`IActionConstraint`</span><span class="sxs-lookup"><span data-stu-id="77c30-898">A typical application won't need a custom `IActionConstraint`</span></span>

<span data-ttu-id="77c30-899">Pravděpodobně jste již `IActionConstraint` použili, i když nejste obeznámeni s rozhraním.</span><span class="sxs-lookup"><span data-stu-id="77c30-899">You have likely already used `IActionConstraint` even if you're not familiar with the interface.</span></span> <span data-ttu-id="77c30-900">Atribut `[HttpGet]` a `[Http-VERB]` podobné atributy implementovat `IActionConstraint` s cílem omezit provádění metody akce.</span><span class="sxs-lookup"><span data-stu-id="77c30-900">The `[HttpGet]` Attribute and similar `[Http-VERB]` attributes implement `IActionConstraint` in order to limit the execution of an action method.</span></span>

```csharp
public class ProductsController : Controller
{
    [HttpGet]
    public IActionResult Edit() { }

    public IActionResult Edit(...) { }
}
```

<span data-ttu-id="77c30-901">Za předpokladu výchozí konvenční `/Products/Edit` trasy by `{ controller = Products, action = Edit }`cesta url vytvořila hodnoty , které by odpovídaly **oběma** zde zobrazených akcím.</span><span class="sxs-lookup"><span data-stu-id="77c30-901">Assuming the default conventional route, the URL path `/Products/Edit` would produce the values `{ controller = Products, action = Edit }`, which would match **both** of the actions shown here.</span></span> <span data-ttu-id="77c30-902">V `IActionConstraint` terminologii bychom řekli, že obě tyto akce jsou považovány za kandidáty - protože obě odpovídají údajům o trase.</span><span class="sxs-lookup"><span data-stu-id="77c30-902">In `IActionConstraint` terminology we would say that both of these actions are considered candidates - as they both match the route data.</span></span>

<span data-ttu-id="77c30-903">Při `HttpGetAttribute` spuštění, bude říkat, že *Edit()* je shoda pro *GET* a není shoda pro jiné sloveso HTTP.</span><span class="sxs-lookup"><span data-stu-id="77c30-903">When the `HttpGetAttribute` executes, it will say that *Edit()* is a match for *GET* and isn't a match for any other HTTP verb.</span></span> <span data-ttu-id="77c30-904">Akce `Edit(...)` nemá definována žádná omezení, a proto bude odpovídat libovolnému slovesu HTTP.</span><span class="sxs-lookup"><span data-stu-id="77c30-904">The `Edit(...)` action doesn't have any constraints defined, and so will match any HTTP verb.</span></span> <span data-ttu-id="77c30-905">Takže za `POST` předpokladu, že - pouze `Edit(...)` zápasy.</span><span class="sxs-lookup"><span data-stu-id="77c30-905">So assuming a `POST` - only `Edit(...)` matches.</span></span> <span data-ttu-id="77c30-906">Ale pro `GET` obě akce může ještě zápas - `IActionConstraint` nicméně, akce s je vždy považován za *lepší* než akce bez.</span><span class="sxs-lookup"><span data-stu-id="77c30-906">But, for a `GET` both actions can still match - however, an action with an `IActionConstraint` is always considered *better* than an action without.</span></span> <span data-ttu-id="77c30-907">Takže `Edit()` protože `[HttpGet]` je to považováno za konkrétnější a bude vybráno, pokud se obě akce mohou shodovat.</span><span class="sxs-lookup"><span data-stu-id="77c30-907">So because `Edit()` has `[HttpGet]` it's considered more specific, and will be selected if both actions can match.</span></span>

<span data-ttu-id="77c30-908">Koncepčně `IActionConstraint` je forma *přetížení*, ale místo přetížení metody se stejným názvem, je přetížení mezi akcemi, které odpovídají stejné adrese URL.</span><span class="sxs-lookup"><span data-stu-id="77c30-908">Conceptually, `IActionConstraint` is a form of *overloading*, but instead of overloading methods with the same name, it's overloading between actions that match the same URL.</span></span> <span data-ttu-id="77c30-909">Směrování atributů `IActionConstraint` také používá a může mít za následek akce z různých řadičů oba považovány za kandidáty.</span><span class="sxs-lookup"><span data-stu-id="77c30-909">Attribute routing also uses `IActionConstraint` and can result in actions from different controllers both being considered candidates.</span></span>

<a name="iactionconstraint-impl-ref-label"></a>

### <a name="implementing-iactionconstraint"></a><span data-ttu-id="77c30-910">Implementace omezení akce IActionConstraint</span><span class="sxs-lookup"><span data-stu-id="77c30-910">Implementing IActionConstraint</span></span>

<span data-ttu-id="77c30-911">Nejjednodušší způsob, jak `IActionConstraint` implementovat je vytvořit třídu odvozenou od `System.Attribute` a umístit ji na vaše akce a řadiče.</span><span class="sxs-lookup"><span data-stu-id="77c30-911">The simplest way to implement an `IActionConstraint` is to create a class derived from `System.Attribute` and place it on your actions and controllers.</span></span> <span data-ttu-id="77c30-912">MVC automaticky zjišťuje všechny, `IActionConstraint` které jsou použity jako atributy.</span><span class="sxs-lookup"><span data-stu-id="77c30-912">MVC will automatically discover any `IActionConstraint` that are applied as attributes.</span></span> <span data-ttu-id="77c30-913">Model aplikace můžete použít omezení a to to je pravděpodobně nejflexibilnější přístup, protože umožňuje metaprogram, jak jsou použity.</span><span class="sxs-lookup"><span data-stu-id="77c30-913">You can use the application model to apply constraints, and this is probably the most flexible approach as it allows you to metaprogram how they're applied.</span></span>

<span data-ttu-id="77c30-914">V následujícím příkladu omezení vybere akci na základě *kódu země* z dat trasy.</span><span class="sxs-lookup"><span data-stu-id="77c30-914">In the following example, a constraint chooses an action based on a *country code* from the route data.</span></span> <span data-ttu-id="77c30-915">[Úplná ukázka na GitHubu](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.ActionConstraintSample.Web/CountrySpecificAttribute.cs).</span><span class="sxs-lookup"><span data-stu-id="77c30-915">The [full sample on GitHub](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.ActionConstraintSample.Web/CountrySpecificAttribute.cs).</span></span>

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

<span data-ttu-id="77c30-916">Jste zodpovědní za `Accept` implementaci metody a výběru "Objednávka" pro omezení spustit.</span><span class="sxs-lookup"><span data-stu-id="77c30-916">You are responsible for implementing the `Accept` method and choosing an 'Order' for the constraint to execute.</span></span> <span data-ttu-id="77c30-917">V tomto případě `Accept` se `true` metoda vrátí k označení akce `country` je shoda, když hodnota trasy odpovídá.</span><span class="sxs-lookup"><span data-stu-id="77c30-917">In this case, the `Accept` method returns `true` to denote the action is a match when the `country` route value matches.</span></span> <span data-ttu-id="77c30-918">To se liší `RouteValueAttribute` od v tom, že umožňuje záložní k akci bez atributu.</span><span class="sxs-lookup"><span data-stu-id="77c30-918">This is different from a `RouteValueAttribute` in that it allows fallback to a non-attributed action.</span></span> <span data-ttu-id="77c30-919">Ukázka ukazuje, že `en-US` pokud definujete akci, kód země jako `fr-FR` se vrátí k `[CountrySpecific(...)]` obecnějšímu řadiči, který se nepoužije.</span><span class="sxs-lookup"><span data-stu-id="77c30-919">The sample shows that if you define an `en-US` action then a country code like `fr-FR` will fall back to a more generic controller that doesn't have `[CountrySpecific(...)]` applied.</span></span>

<span data-ttu-id="77c30-920">Vlastnost `Order` rozhodne, ve *které fázi* je omezení součástí.</span><span class="sxs-lookup"><span data-stu-id="77c30-920">The `Order` property decides which *stage* the constraint is part of.</span></span> <span data-ttu-id="77c30-921">Omezení akce jsou spuštěna `Order`ve skupinách na základě .</span><span class="sxs-lookup"><span data-stu-id="77c30-921">Action constraints run in groups based on the `Order`.</span></span> <span data-ttu-id="77c30-922">Například všechny atributy framework za předpokladu, `Order` http metody použít stejnou hodnotu tak, aby byly spuštěny ve stejné fázi.</span><span class="sxs-lookup"><span data-stu-id="77c30-922">For example, all of the framework provided HTTP method attributes use the same `Order` value so that they run in the same stage.</span></span> <span data-ttu-id="77c30-923">Můžete mít tolik fází, kolik potřebujete k implementaci požadovaných zásad.</span><span class="sxs-lookup"><span data-stu-id="77c30-923">You can have as many stages as you need to implement your desired policies.</span></span>

> [!TIP]
> <span data-ttu-id="77c30-924">Chcete-li rozhodnout `Order` o hodnotě pro přemýšlet o tom, zda vaše omezení by měla být použita před metodami HTTP.</span><span class="sxs-lookup"><span data-stu-id="77c30-924">To decide on a value for `Order` think about whether or not your constraint should be applied before HTTP methods.</span></span> <span data-ttu-id="77c30-925">Nižší čísla spustit jako první.</span><span class="sxs-lookup"><span data-stu-id="77c30-925">Lower numbers run first.</span></span>

::: moniker-end
