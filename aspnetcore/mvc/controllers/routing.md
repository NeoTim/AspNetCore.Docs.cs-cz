---
title: Směrování na akce kontroleru v ASP.NET Core
author: rick-anderson
description: Přečtěte si, jak ASP.NET Core MVC používá middleware směrování k párování adres URL příchozích požadavků a jejich mapování na akce.
ms.author: riande
ms.date: 3/25/2020
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
uid: mvc/controllers/routing
ms.openlocfilehash: 08cd6396cca78488827dfa7c2cca62a35c500dbd
ms.sourcegitcommit: 8fcb08312a59c37e3542e7a67dad25faf5bb8e76
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2020
ms.locfileid: "90009697"
---
# <a name="routing-to-controller-actions-in-aspnet-core"></a><span data-ttu-id="da86d-103">Směrování na akce kontroleru v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="da86d-103">Routing to controller actions in ASP.NET Core</span></span>

<span data-ttu-id="da86d-104">Služba [Ryan Nowak](https://github.com/rynowak), [Kirka Larkin](https://twitter.com/serpent5)a [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="da86d-104">By [Ryan Nowak](https://github.com/rynowak), [Kirk Larkin](https://twitter.com/serpent5), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="da86d-105">Řadiče ASP.NET Core používají [middleware](xref:fundamentals/middleware/index) směrování ke spárování adres URL příchozích požadavků a jejich mapování na [Akce](#action).</span><span class="sxs-lookup"><span data-stu-id="da86d-105">ASP.NET Core controllers use the Routing [middleware](xref:fundamentals/middleware/index) to match the URLs of incoming requests and map them to [actions](#action).</span></span>  <span data-ttu-id="da86d-106">Šablony tras:</span><span class="sxs-lookup"><span data-stu-id="da86d-106">Routes templates:</span></span>

* <span data-ttu-id="da86d-107">Jsou definovány ve spouštěcím kódu nebo atributech.</span><span class="sxs-lookup"><span data-stu-id="da86d-107">Are defined in startup code or attributes.</span></span>
* <span data-ttu-id="da86d-108">Popisuje, jak se shodují cesty URL k [akcím](#action).</span><span class="sxs-lookup"><span data-stu-id="da86d-108">Describe how URL paths are matched to [actions](#action).</span></span>
* <span data-ttu-id="da86d-109">Slouží ke generování adres URL pro odkazy.</span><span class="sxs-lookup"><span data-stu-id="da86d-109">Are used to generate URLs for links.</span></span> <span data-ttu-id="da86d-110">Vygenerované odkazy jsou obvykle vraceny v odpovědích.</span><span class="sxs-lookup"><span data-stu-id="da86d-110">The generated links are typically returned in responses.</span></span>

<span data-ttu-id="da86d-111">Akce jsou směrovány buď podle [konvence](#cr) , nebo podle [atributů](#ar).</span><span class="sxs-lookup"><span data-stu-id="da86d-111">Actions are either [conventionally-routed](#cr) or [attribute-routed](#ar).</span></span> <span data-ttu-id="da86d-112">Umístěním trasy do kontroleru nebo [Akce](#action) se nastaví směrování atributů.</span><span class="sxs-lookup"><span data-stu-id="da86d-112">Placing a route on the controller or [action](#action) makes it attribute-routed.</span></span> <span data-ttu-id="da86d-113">Další informace najdete v tématu [smíšená směrování](#routing-mixed-ref-label) .</span><span class="sxs-lookup"><span data-stu-id="da86d-113">See [Mixed routing](#routing-mixed-ref-label) for more information.</span></span>

<span data-ttu-id="da86d-114">Tento dokument:</span><span class="sxs-lookup"><span data-stu-id="da86d-114">This document:</span></span>

* <span data-ttu-id="da86d-115">Vysvětluje interakce mezi MVC a směrováním:</span><span class="sxs-lookup"><span data-stu-id="da86d-115">Explains the interactions between MVC and routing:</span></span>
  * <span data-ttu-id="da86d-116">Jak typické aplikace MVC využívají funkce směrování.</span><span class="sxs-lookup"><span data-stu-id="da86d-116">How typical MVC apps make use of routing features.</span></span>
  * <span data-ttu-id="da86d-117">Pokrývá obojí:</span><span class="sxs-lookup"><span data-stu-id="da86d-117">Covers both:</span></span>
    * <span data-ttu-id="da86d-118">[Směrování](#cr) se obvykle používá s řadiči a zobrazeními.</span><span class="sxs-lookup"><span data-stu-id="da86d-118">[Conventionally routing](#cr) typically used with controllers and views.</span></span>
    * <span data-ttu-id="da86d-119">*Směrování atributů* používané s rozhraními REST API.</span><span class="sxs-lookup"><span data-stu-id="da86d-119">*Attribute routing* used with REST APIs.</span></span> <span data-ttu-id="da86d-120">Pokud se primárně zajímáte o směrování pro rozhraní REST API, přejděte na oddíl [Směrování atributů pro rozhraní REST API](#ar) .</span><span class="sxs-lookup"><span data-stu-id="da86d-120">If you're primarily interested in routing for REST APIs, jump to the [Attribute routing for REST APIs](#ar) section.</span></span>
  * <span data-ttu-id="da86d-121">Podrobnosti o rozšířeném směrování najdete v tématu [Směrování](xref:fundamentals/routing) .</span><span class="sxs-lookup"><span data-stu-id="da86d-121">See [Routing](xref:fundamentals/routing) for advanced routing details.</span></span>
* <span data-ttu-id="da86d-122">Odkazuje na výchozí systém směrování přidaný v ASP.NET Core 3,0, který se označuje jako směrování koncového bodu.</span><span class="sxs-lookup"><span data-stu-id="da86d-122">Refers to the default routing system added in ASP.NET Core 3.0, called endpoint routing.</span></span> <span data-ttu-id="da86d-123">Pro účely kompatibility je možné použít řadiče s předchozí verzí směrování.</span><span class="sxs-lookup"><span data-stu-id="da86d-123">It's possible to use controllers with the previous version of routing for compatibility purposes.</span></span> <span data-ttu-id="da86d-124">Pokyny najdete v [příručce k migraci 2.2 – 3.0](xref:migration/22-to-30) .</span><span class="sxs-lookup"><span data-stu-id="da86d-124">See the [2.2-3.0 migration guide](xref:migration/22-to-30) for instructions.</span></span> <span data-ttu-id="da86d-125">Referenční materiály ke staršímu systému směrování najdete v [tomto dokumentu ve verzi 2,2](xref:mvc/controllers/routing?view=aspnetcore-2.2) .</span><span class="sxs-lookup"><span data-stu-id="da86d-125">Refer to the [2.2 version of this document](xref:mvc/controllers/routing?view=aspnetcore-2.2) for reference material on the legacy routing system.</span></span>

<a name="cr"></a>

## <a name="set-up-conventional-route"></a><span data-ttu-id="da86d-126">Nastavení konvenční trasy</span><span class="sxs-lookup"><span data-stu-id="da86d-126">Set up conventional route</span></span>

<span data-ttu-id="da86d-127">`Startup.Configure` obvykle má při použití [konvenčního směrování](#crd)kód podobný následujícímu:</span><span class="sxs-lookup"><span data-stu-id="da86d-127">`Startup.Configure` typically has code similar to the following when using [conventional routing](#crd):</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupDefaultMVC.cs?name=snippet)]

<span data-ttu-id="da86d-128">Uvnitř volání metody <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute%2A> se používá k vytvoření jedné trasy.</span><span class="sxs-lookup"><span data-stu-id="da86d-128">Inside the call to <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A>, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute%2A> is used to create a single route.</span></span> <span data-ttu-id="da86d-129">Jedna trasa má název `default` Route.</span><span class="sxs-lookup"><span data-stu-id="da86d-129">The single route is named `default` route.</span></span> <span data-ttu-id="da86d-130">Většina aplikací s řadiči a zobrazeními používá šablonu směrování podobnou `default` trase.</span><span class="sxs-lookup"><span data-stu-id="da86d-130">Most apps with controllers and views use a route template similar to the `default` route.</span></span> <span data-ttu-id="da86d-131">Rozhraní REST API by měly používat [Směrování atributů](#ar).</span><span class="sxs-lookup"><span data-stu-id="da86d-131">REST APIs should use [attribute routing](#ar).</span></span>

<span data-ttu-id="da86d-132">Šablona trasy `"{controller=Home}/{action=Index}/{id?}"` :</span><span class="sxs-lookup"><span data-stu-id="da86d-132">The route template `"{controller=Home}/{action=Index}/{id?}"`:</span></span>

* <span data-ttu-id="da86d-133">Odpovídá cestě URL jako `/Products/Details/5`</span><span class="sxs-lookup"><span data-stu-id="da86d-133">Matches a URL path like `/Products/Details/5`</span></span>
* <span data-ttu-id="da86d-134">Extrahuje hodnoty tras `{ controller = Products, action = Details, id = 5 }` tím, že tokenizací cestu.</span><span class="sxs-lookup"><span data-stu-id="da86d-134">Extracts the route values `{ controller = Products, action = Details, id = 5 }` by tokenizing the path.</span></span> <span data-ttu-id="da86d-135">Výsledkem extrakce hodnot tras je shoda, pokud má aplikace kontroler s názvem `ProductsController` a `Details` akci:</span><span class="sxs-lookup"><span data-stu-id="da86d-135">The extraction of route values results in a match if the app has a controller named `ProductsController` and a `Details` action:</span></span>

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippetA)]

  [!INCLUDE[](~/includes/MyDisplayRouteInfo.md)]

* <span data-ttu-id="da86d-136">`/Products/Details/5` model váže hodnotu `id = 5` pro nastavení `id` parametru `5` .</span><span class="sxs-lookup"><span data-stu-id="da86d-136">`/Products/Details/5` model binds the value of `id = 5` to set the `id` parameter to `5`.</span></span> <span data-ttu-id="da86d-137">Další podrobnosti najdete v tématu [vazba modelu](xref:mvc/models/model-binding) .</span><span class="sxs-lookup"><span data-stu-id="da86d-137">See [Model Binding](xref:mvc/models/model-binding) for more details.</span></span>
* <span data-ttu-id="da86d-138">`{controller=Home}` definuje `Home` jako výchozí `controller` .</span><span class="sxs-lookup"><span data-stu-id="da86d-138">`{controller=Home}` defines `Home` as the default `controller`.</span></span>
* <span data-ttu-id="da86d-139">`{action=Index}` definuje `Index` jako výchozí `action` .</span><span class="sxs-lookup"><span data-stu-id="da86d-139">`{action=Index}` defines `Index` as the default `action`.</span></span>
*  <span data-ttu-id="da86d-140">`?`Znak v `{id?}` definuje `id` jako volitelné.</span><span class="sxs-lookup"><span data-stu-id="da86d-140">The `?` character in `{id?}` defines `id` as optional.</span></span>
  * <span data-ttu-id="da86d-141">Výchozí a volitelné parametry směrování nemusejí být v cestě URL pro porovnávání k dispozici.</span><span class="sxs-lookup"><span data-stu-id="da86d-141">Default and optional route parameters don't need to be present in the URL path for a match.</span></span> <span data-ttu-id="da86d-142">Podrobný popis syntaxe šablony směrování naleznete v tématu Referenční dokumentace k [šabloně směrování](xref:fundamentals/routing#route-template-reference) .</span><span class="sxs-lookup"><span data-stu-id="da86d-142">See [Route Template Reference](xref:fundamentals/routing#route-template-reference) for a detailed description of route template syntax.</span></span>
* <span data-ttu-id="da86d-143">Odpovídá cestě URL `/` .</span><span class="sxs-lookup"><span data-stu-id="da86d-143">Matches the URL path `/`.</span></span>
* <span data-ttu-id="da86d-144">Vytvoří hodnoty trasy `{ controller = Home, action = Index }` .</span><span class="sxs-lookup"><span data-stu-id="da86d-144">Produces the route values `{ controller = Home, action = Index }`.</span></span>

<span data-ttu-id="da86d-145">Hodnoty pro `controller` a `action` využívají výchozí hodnoty.</span><span class="sxs-lookup"><span data-stu-id="da86d-145">The values for `controller` and `action` make use of the default values.</span></span> <span data-ttu-id="da86d-146">`id` nevytváří hodnotu, protože v cestě URL není žádný odpovídající segment.</span><span class="sxs-lookup"><span data-stu-id="da86d-146">`id` doesn't produce a value since there's no corresponding segment in the URL path.</span></span> <span data-ttu-id="da86d-147">`/` odpovídá pouze v případě, že `HomeController` existuje `Index` akce a:</span><span class="sxs-lookup"><span data-stu-id="da86d-147">`/` only matches if there exists a `HomeController` and `Index` action:</span></span>

```csharp
public class HomeController : Controller
{
  public IActionResult Index() { ... }
}
```

<span data-ttu-id="da86d-148">Pomocí předchozí definice kontroleru a šablony trasy se `HomeController.Index` akce spustí pro následující cesty URL:</span><span class="sxs-lookup"><span data-stu-id="da86d-148">Using the preceding controller definition and route template, the `HomeController.Index` action is run for the following URL paths:</span></span>

* `/Home/Index/17`
* `/Home/Index`
* `/Home`
* `/`

<span data-ttu-id="da86d-149">Cesta URL `/` používá výchozí `Home` řadiče a akci šablony směrování `Index` .</span><span class="sxs-lookup"><span data-stu-id="da86d-149">The URL path `/` uses the route template default `Home` controllers and `Index` action.</span></span> <span data-ttu-id="da86d-150">Cesta URL `/Home` používá výchozí akci šablony směrování `Index` .</span><span class="sxs-lookup"><span data-stu-id="da86d-150">The URL path `/Home` uses the route template default `Index` action.</span></span>

<span data-ttu-id="da86d-151">Způsob usnadnění <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute%2A> :</span><span class="sxs-lookup"><span data-stu-id="da86d-151">The convenience method <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute%2A>:</span></span>

```csharp
endpoints.MapDefaultControllerRoute();
```

<span data-ttu-id="da86d-152">LPRMON</span><span class="sxs-lookup"><span data-stu-id="da86d-152">Replaces:</span></span>

```csharp
endpoints.MapControllerRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

> [!IMPORTANT]
> <span data-ttu-id="da86d-153">Směrování je nakonfigurované pomocí <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting%2A> <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> middlewaru a.</span><span class="sxs-lookup"><span data-stu-id="da86d-153">Routing is configured using the <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting%2A> and <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> middleware.</span></span> <span data-ttu-id="da86d-154">Použití řadičů:</span><span class="sxs-lookup"><span data-stu-id="da86d-154">To use controllers:</span></span>
>
> * <span data-ttu-id="da86d-155">Zavolejte <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers%2A> uvnitř `UseEndpoints` pro mapování řadičů [směrovaných na atributy](#ar) .</span><span class="sxs-lookup"><span data-stu-id="da86d-155">Call <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers%2A> inside `UseEndpoints` to map [attribute routed](#ar) controllers.</span></span>
> * <span data-ttu-id="da86d-156">Zavolejte <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute%2A> nebo <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute%2A> , pokud chcete namapovat obě [konvence směrované](#cr) na řadiče a ovladače [směrovaného atributu](#ar) .</span><span class="sxs-lookup"><span data-stu-id="da86d-156">Call <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute%2A> or <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute%2A>, to map both [conventionally routed](#cr) controllers and [attribute routed](#ar) controllers.</span></span>

<a name="routing-conventional-ref-label"></a>
<a name="crd"></a>

## <a name="conventional-routing"></a><span data-ttu-id="da86d-157">Konvenční směrování</span><span class="sxs-lookup"><span data-stu-id="da86d-157">Conventional routing</span></span>

<span data-ttu-id="da86d-158">Konvenční směrování se používá s řadiči a zobrazeními.</span><span class="sxs-lookup"><span data-stu-id="da86d-158">Conventional routing is used with controllers and views.</span></span> <span data-ttu-id="da86d-159">`default`Trasa:</span><span class="sxs-lookup"><span data-stu-id="da86d-159">The `default` route:</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupDefaultMVC.cs?name=snippet2)]

<span data-ttu-id="da86d-160">je příkladem *konvenčního směrování*.</span><span class="sxs-lookup"><span data-stu-id="da86d-160">is an example of a *conventional routing*.</span></span> <span data-ttu-id="da86d-161">Nazývá se *konvenční směrování* , protože vytváří *konvenci* pro cesty URL:</span><span class="sxs-lookup"><span data-stu-id="da86d-161">It's called *conventional routing* because it establishes a *convention* for URL paths:</span></span>

* <span data-ttu-id="da86d-162">První segment cesty, `{controller=Home}` , mapuje na název kontroleru.</span><span class="sxs-lookup"><span data-stu-id="da86d-162">The first path segment, `{controller=Home}`, maps to the controller name.</span></span>
* <span data-ttu-id="da86d-163">Druhý segment, `{action=Index}` , mapuje na název [Akce](#action) .</span><span class="sxs-lookup"><span data-stu-id="da86d-163">The second segment, `{action=Index}`, maps to the [action](#action) name.</span></span>
* <span data-ttu-id="da86d-164">Třetí segment `{id?}` je použit pro volitelné `id` .</span><span class="sxs-lookup"><span data-stu-id="da86d-164">The third segment, `{id?}` is used for an optional `id`.</span></span> <span data-ttu-id="da86d-165">V nástroji je `?` `{id?}` volitelné.</span><span class="sxs-lookup"><span data-stu-id="da86d-165">The `?` in `{id?}` makes it optional.</span></span> <span data-ttu-id="da86d-166">`id` slouží k mapování na entitu modelu.</span><span class="sxs-lookup"><span data-stu-id="da86d-166">`id` is used to map to a model entity.</span></span>

<span data-ttu-id="da86d-167">Pomocí této `default` trasy adresa URL:</span><span class="sxs-lookup"><span data-stu-id="da86d-167">Using this `default` route, the URL path:</span></span>

* <span data-ttu-id="da86d-168">`/Products/List` provede mapování na `ProductsController.List` akci.</span><span class="sxs-lookup"><span data-stu-id="da86d-168">`/Products/List` maps to the `ProductsController.List` action.</span></span>
* <span data-ttu-id="da86d-169">`/Blog/Article/17` mapuje na `BlogController.Article` model a obvykle model váže `id` parametr na 17.</span><span class="sxs-lookup"><span data-stu-id="da86d-169">`/Blog/Article/17` maps to `BlogController.Article` and typically model binds the `id` parameter to 17.</span></span>

<span data-ttu-id="da86d-170">Toto mapování:</span><span class="sxs-lookup"><span data-stu-id="da86d-170">This mapping:</span></span>

* <span data-ttu-id="da86d-171">Je založena **pouze**na názvech kontroléru a [Akce](#action) .</span><span class="sxs-lookup"><span data-stu-id="da86d-171">Is based on the controller and [action](#action) names **only**.</span></span>
* <span data-ttu-id="da86d-172">Není založen na oborech názvů, umístěních zdrojového souboru nebo parametrech metody.</span><span class="sxs-lookup"><span data-stu-id="da86d-172">Isn't based on namespaces, source file locations, or method parameters.</span></span>

<span data-ttu-id="da86d-173">Použití konvenčního směrování s výchozí trasou umožňuje vytvoření aplikace bez nutnosti sestavovat nový vzor adresy URL pro každou akci.</span><span class="sxs-lookup"><span data-stu-id="da86d-173">Using conventional routing with the default route allows creating the app without having to come up with a new URL pattern for each action.</span></span> <span data-ttu-id="da86d-174">Pro aplikaci s akcemi stylu [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) s konzistencí pro adresy URL napříč řadiči:</span><span class="sxs-lookup"><span data-stu-id="da86d-174">For an app with [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) style actions, having consistency for the URLs across controllers:</span></span>

* <span data-ttu-id="da86d-175">Pomáhá zjednodušit kód.</span><span class="sxs-lookup"><span data-stu-id="da86d-175">Helps simplify the code.</span></span>
* <span data-ttu-id="da86d-176">Provede více předvídatelného uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="da86d-176">Makes the UI more predictable.</span></span>

> [!WARNING]
> <span data-ttu-id="da86d-177">`id`V předchozím kódu je šablona trasy definována jako volitelná.</span><span class="sxs-lookup"><span data-stu-id="da86d-177">The `id` in the preceding code is defined as optional by the route template.</span></span> <span data-ttu-id="da86d-178">Akce se můžou provádět bez volitelného ID, které jste zadali jako součást adresy URL.</span><span class="sxs-lookup"><span data-stu-id="da86d-178">Actions can execute without the optional ID provided as part of the URL.</span></span> <span data-ttu-id="da86d-179">Obecně platí, že pokud `id` je vynechána adresa URL:</span><span class="sxs-lookup"><span data-stu-id="da86d-179">Generally, when`id` is omitted from the URL:</span></span>
>
> * <span data-ttu-id="da86d-180">`id` je nastaven na `0` základě vazby modelu.</span><span class="sxs-lookup"><span data-stu-id="da86d-180">`id` is set to `0` by model binding.</span></span>
> * <span data-ttu-id="da86d-181">V porovnání s databází nebyla nalezena žádná entita `id == 0` .</span><span class="sxs-lookup"><span data-stu-id="da86d-181">No entity is found in the database matching `id == 0`.</span></span>
>
> <span data-ttu-id="da86d-182">[Směrování atributů](#ar) poskytuje jemně odstupňovaný ovládací prvek, který umožňuje, aby se ID vyžadovalo u některých akcí, a ne pro ostatní.</span><span class="sxs-lookup"><span data-stu-id="da86d-182">[Attribute routing](#ar) provides fine-grained control to make the ID required for some actions and not for others.</span></span> <span data-ttu-id="da86d-183">V dokumentaci podle konvence obsahuje volitelné parametry, jako `id` když se pravděpodobně budou zobrazovat ve správném použití.</span><span class="sxs-lookup"><span data-stu-id="da86d-183">By convention, the documentation includes optional parameters like `id` when they're likely to appear in correct usage.</span></span>

<span data-ttu-id="da86d-184">Většina aplikací by měla zvolit základní a popisné schéma směrování, aby byly adresy URL čitelné a smysluplné.</span><span class="sxs-lookup"><span data-stu-id="da86d-184">Most apps should choose a basic and descriptive routing scheme so that URLs are readable and meaningful.</span></span> <span data-ttu-id="da86d-185">Výchozí konvenční trasa `{controller=Home}/{action=Index}/{id?}` :</span><span class="sxs-lookup"><span data-stu-id="da86d-185">The default conventional route `{controller=Home}/{action=Index}/{id?}`:</span></span>

* <span data-ttu-id="da86d-186">Podporuje základní a popisné schéma směrování.</span><span class="sxs-lookup"><span data-stu-id="da86d-186">Supports a basic and descriptive routing scheme.</span></span>
* <span data-ttu-id="da86d-187">Je užitečným výchozím bodem pro aplikace založené na uživatelském rozhraní.</span><span class="sxs-lookup"><span data-stu-id="da86d-187">Is a useful starting point for UI-based apps.</span></span>
* <span data-ttu-id="da86d-188">Je jedinou šablonou směrování, která je nutná pro mnoho webových aplikací uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="da86d-188">Is the only route template needed for many web UI apps.</span></span> <span data-ttu-id="da86d-189">Pro větší webové uživatelské rozhraní jsou jiné trasy, které používají [oblasti](#areas) , často všechny potřebné.</span><span class="sxs-lookup"><span data-stu-id="da86d-189">For larger web UI apps, another route using [Areas](#areas) is frequently all that's needed.</span></span>

<span data-ttu-id="da86d-190"><xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute%2A> a <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute%2A> :</span><span class="sxs-lookup"><span data-stu-id="da86d-190"><xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute%2A> and <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute%2A> :</span></span>

* <span data-ttu-id="da86d-191">Automatické přiřazení hodnoty **objednávky** ke svým koncovým bodům podle pořadí, ve kterém jsou vyvolány.</span><span class="sxs-lookup"><span data-stu-id="da86d-191">Automatically assign an **order** value to their endpoints based on the order they are invoked.</span></span>

<span data-ttu-id="da86d-192">Směrování koncových bodů v ASP.NET Core 3,0 a novějším:</span><span class="sxs-lookup"><span data-stu-id="da86d-192">Endpoint routing in ASP.NET Core 3.0 and later:</span></span>

* <span data-ttu-id="da86d-193">Nemá koncept tras.</span><span class="sxs-lookup"><span data-stu-id="da86d-193">Doesn't have a concept of routes.</span></span>
* <span data-ttu-id="da86d-194">Neposkytuje zaručené řazení pro provádění rozšiřitelnosti, všechny koncové body jsou zpracovávány současně.</span><span class="sxs-lookup"><span data-stu-id="da86d-194">Doesn't provide ordering guarantees for the execution of extensibility,  all endpoints are processed at once.</span></span>

<span data-ttu-id="da86d-195">Povolte [protokolování](xref:fundamentals/logging/index) , abyste viděli, jak integrované implementace směrování, například <xref:Microsoft.AspNetCore.Routing.Route> , odpovídají požadavkům.</span><span class="sxs-lookup"><span data-stu-id="da86d-195">Enable [Logging](xref:fundamentals/logging/index) to see how the built-in routing implementations, such as <xref:Microsoft.AspNetCore.Routing.Route>, match requests.</span></span>

<span data-ttu-id="da86d-196">[Směrování atributů](#ar) je vysvětleno dále v tomto dokumentu.</span><span class="sxs-lookup"><span data-stu-id="da86d-196">[Attribute routing](#ar) is explained later in this document.</span></span>

<a name="mr"></a>

### <a name="multiple-conventional-routes"></a><span data-ttu-id="da86d-197">Několik konvenčních tras</span><span class="sxs-lookup"><span data-stu-id="da86d-197">Multiple conventional routes</span></span>

<span data-ttu-id="da86d-198">V rámci lze přidat více [konvenčních tras](#cr) `UseEndpoints` přidáním dalších volání do <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute%2A> a <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute%2A> .</span><span class="sxs-lookup"><span data-stu-id="da86d-198">Multiple [conventional routes](#cr) can be added inside `UseEndpoints` by adding more calls to <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute%2A> and <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute%2A>.</span></span> <span data-ttu-id="da86d-199">To umožňuje definovat více konvencí nebo přidávat konvenční trasy, které jsou vyhrazeny určité [akci](#action), například:</span><span class="sxs-lookup"><span data-stu-id="da86d-199">Doing so allows defining multiple conventions, or to adding conventional routes that are dedicated to a specific [action](#action), such as:</span></span>

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

<a name="dcr"></a>

<span data-ttu-id="da86d-200">`blog`Trasa v předchozím kódu je **vyhrazená konvenční trasa**.</span><span class="sxs-lookup"><span data-stu-id="da86d-200">The `blog` route in the preceding code is a **dedicated conventional route**.</span></span> <span data-ttu-id="da86d-201">Nazývá se vyhrazená konvenční trasa z těchto důvodů:</span><span class="sxs-lookup"><span data-stu-id="da86d-201">It's called a dedicated conventional route because:</span></span>

* <span data-ttu-id="da86d-202">Používá [konvenční směrování](#cr).</span><span class="sxs-lookup"><span data-stu-id="da86d-202">It uses [conventional routing](#cr).</span></span>
* <span data-ttu-id="da86d-203">Je vyhrazený pro konkrétní [akci](#action).</span><span class="sxs-lookup"><span data-stu-id="da86d-203">It's dedicated to a specific [action](#action).</span></span>

<span data-ttu-id="da86d-204">Protože `controller` se `action` nezobrazuje v šabloně trasy `"blog/{*article}"` jako parametry:</span><span class="sxs-lookup"><span data-stu-id="da86d-204">Because `controller` and `action` don't appear in the route template `"blog/{*article}"` as parameters:</span></span>

* <span data-ttu-id="da86d-205">Můžou mít jenom výchozí hodnoty `{ controller = "Blog", action = "Article" }` .</span><span class="sxs-lookup"><span data-stu-id="da86d-205">They can only have the default values `{ controller = "Blog", action = "Article" }`.</span></span>
* <span data-ttu-id="da86d-206">Tato trasa se vždy mapuje na akci `BlogController.Article` .</span><span class="sxs-lookup"><span data-stu-id="da86d-206">This route always maps to the action `BlogController.Article`.</span></span>

<span data-ttu-id="da86d-207">`/Blog`, `/Blog/Article` a `/Blog/{any-string}` jsou jediné cesty URL, které odpovídají trase na blogu.</span><span class="sxs-lookup"><span data-stu-id="da86d-207">`/Blog`, `/Blog/Article`, and `/Blog/{any-string}` are the only URL paths that match the blog route.</span></span>

<span data-ttu-id="da86d-208">Předchozí příklad:</span><span class="sxs-lookup"><span data-stu-id="da86d-208">The preceding example:</span></span>

* <span data-ttu-id="da86d-209">`blog` trasa má vyšší prioritu pro shody, než je `default` trasa, protože je přidána jako první.</span><span class="sxs-lookup"><span data-stu-id="da86d-209">`blog` route has a higher priority for matches than the `default` route because it is added first.</span></span>
* <span data-ttu-id="da86d-210">Je příkladem směrování stylu [popisu](https://developer.mozilla.org/docs/Glossary/Slug) , kde je typický jako součást adresy URL název článku.</span><span class="sxs-lookup"><span data-stu-id="da86d-210">Is an example of [Slug](https://developer.mozilla.org/docs/Glossary/Slug) style routing where it's typical to have an article name as part of the URL.</span></span>

> [!WARNING]
> <span data-ttu-id="da86d-211">V ASP.NET Core 3,0 a novějších směrování:</span><span class="sxs-lookup"><span data-stu-id="da86d-211">In ASP.NET Core 3.0 and later, routing doesn't:</span></span>
> * <span data-ttu-id="da86d-212">Definujte koncept nazvaný *trase*.</span><span class="sxs-lookup"><span data-stu-id="da86d-212">Define a concept called a *route*.</span></span> <span data-ttu-id="da86d-213">`UseRouting` Přidá směrování do kanálu middlewaru.</span><span class="sxs-lookup"><span data-stu-id="da86d-213">`UseRouting` adds route matching to the middleware pipeline.</span></span> <span data-ttu-id="da86d-214">`UseRouting`Middleware prohlíží sadu koncových bodů definovaných v aplikaci a vybere nejlepší shodu koncového bodu na základě požadavku.</span><span class="sxs-lookup"><span data-stu-id="da86d-214">The `UseRouting` middleware looks at the set of endpoints defined in the app, and selects the best endpoint match based on the request.</span></span>
> * <span data-ttu-id="da86d-215">Poskytněte záruky týkající se pořadí spouštění rozšiřitelnosti, například <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> nebo <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint> .</span><span class="sxs-lookup"><span data-stu-id="da86d-215">Provide guarantees about the execution order of extensibility like <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> or <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint>.</span></span>
>
><span data-ttu-id="da86d-216">Přečtěte si téma [Směrování](xref:fundamentals/routing) pro referenční materiál při směrování.</span><span class="sxs-lookup"><span data-stu-id="da86d-216">See [Routing](xref:fundamentals/routing) for reference material on routing.</span></span>

<a name="cro"></a>

### <a name="conventional-routing-order"></a><span data-ttu-id="da86d-217">Konvenční pořadí směrování</span><span class="sxs-lookup"><span data-stu-id="da86d-217">Conventional routing order</span></span>

<span data-ttu-id="da86d-218">Konvenční směrování odpovídá pouze kombinaci akcí a kontrolérů, které jsou definovány aplikací.</span><span class="sxs-lookup"><span data-stu-id="da86d-218">Conventional routing only matches a combination of action and controller that are defined by the app.</span></span> <span data-ttu-id="da86d-219">Cílem je zjednodušit případy, kdy se konvenční trasy překrývají.</span><span class="sxs-lookup"><span data-stu-id="da86d-219">This is intended to simplify cases where conventional routes overlap.</span></span>
<span data-ttu-id="da86d-220">Přidání tras pomocí <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute%2A> , <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute%2A> a <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute%2A> Automatické přiřazení hodnoty objednávky ke svým koncovým bodům na základě pořadí, ve kterém jsou vyvolány.</span><span class="sxs-lookup"><span data-stu-id="da86d-220">Adding routes using <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute%2A>, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute%2A>, and <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute%2A> automatically assign an order value to their endpoints based on the order they are invoked.</span></span> <span data-ttu-id="da86d-221">Shody z trasy, která se zobrazí dříve, mají vyšší prioritu.</span><span class="sxs-lookup"><span data-stu-id="da86d-221">Matches from a route that appears earlier have a higher priority.</span></span> <span data-ttu-id="da86d-222">Konvenční směrování je závislé na pořadí.</span><span class="sxs-lookup"><span data-stu-id="da86d-222">Conventional routing is order-dependent.</span></span> <span data-ttu-id="da86d-223">Obecně platí, že trasy s oblastmi by měly být umístěny dříve, protože jsou konkrétnější než trasy bez oblasti.</span><span class="sxs-lookup"><span data-stu-id="da86d-223">In general, routes with areas should be placed earlier as they're more specific than routes without an area.</span></span> <span data-ttu-id="da86d-224">[Vyhrazené konvenční trasy](#dcr) se zachycením všech parametrů tras, jako je například `{*article}` , mohou vytvořit trasu příliš [hladce](xref:fundamentals/routing#greedy), což znamená, že odpovídají adresám URL, které mají být porovnány s jinými trasami.</span><span class="sxs-lookup"><span data-stu-id="da86d-224">[Dedicated conventional routes](#dcr) with catch-all route parameters like `{*article}` can make a route too [greedy](xref:fundamentals/routing#greedy), meaning that it matches URLs that you intended to be matched by other routes.</span></span> <span data-ttu-id="da86d-225">Tyto hladové trasy umístěte později do směrovací tabulky, aby se zabránilo hladce porovnávání.</span><span class="sxs-lookup"><span data-stu-id="da86d-225">Put the greedy routes later in the route table to prevent greedy matches.</span></span>

[!INCLUDE[](~/includes/catchall.md)]

<a name="best"></a>

### <a name="resolving-ambiguous-actions"></a><span data-ttu-id="da86d-226">Řešení nejednoznačných akcí</span><span class="sxs-lookup"><span data-stu-id="da86d-226">Resolving ambiguous actions</span></span>

<span data-ttu-id="da86d-227">Pokud se dva koncové body shodují přes směrování, musí směrování provést jednu z následujících akcí:</span><span class="sxs-lookup"><span data-stu-id="da86d-227">When two endpoints match through routing, routing must do one of the following:</span></span>

* <span data-ttu-id="da86d-228">Vyberte nejlepší kandidáta.</span><span class="sxs-lookup"><span data-stu-id="da86d-228">Choose the best candidate.</span></span>
* <span data-ttu-id="da86d-229">Vyvolejte výjimku.</span><span class="sxs-lookup"><span data-stu-id="da86d-229">Throw an exception.</span></span>

<span data-ttu-id="da86d-230">Například:</span><span class="sxs-lookup"><span data-stu-id="da86d-230">For example:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet9)]

<span data-ttu-id="da86d-231">Předchozí kontroler definuje dvě akce, které se shodují:</span><span class="sxs-lookup"><span data-stu-id="da86d-231">The preceding controller defines two actions that match:</span></span>

* <span data-ttu-id="da86d-232">Cesta URL `/Products33/Edit/17`</span><span class="sxs-lookup"><span data-stu-id="da86d-232">The URL path `/Products33/Edit/17`</span></span>
* <span data-ttu-id="da86d-233">Směrování dat `{ controller = Products33, action = Edit, id = 17 }` .</span><span class="sxs-lookup"><span data-stu-id="da86d-233">Route data `{ controller = Products33, action = Edit, id = 17 }`.</span></span>

<span data-ttu-id="da86d-234">Toto je typický vzor pro řadiče MVC:</span><span class="sxs-lookup"><span data-stu-id="da86d-234">This is a typical pattern for MVC controllers:</span></span>

* <span data-ttu-id="da86d-235">`Edit(int)` zobrazí formulář pro úpravu produktu.</span><span class="sxs-lookup"><span data-stu-id="da86d-235">`Edit(int)` displays a form to edit a product.</span></span>
* <span data-ttu-id="da86d-236">`Edit(int, Product)` zpracuje publikovaný formulář.</span><span class="sxs-lookup"><span data-stu-id="da86d-236">`Edit(int, Product)` processes  the posted form.</span></span>

<span data-ttu-id="da86d-237">Postup při řešení správné trasy:</span><span class="sxs-lookup"><span data-stu-id="da86d-237">To resolve the correct route:</span></span>

* <span data-ttu-id="da86d-238">`Edit(int, Product)` je vybrána, pokud je požadavek HTTP `POST` .</span><span class="sxs-lookup"><span data-stu-id="da86d-238">`Edit(int, Product)` is selected when the request is an HTTP `POST`.</span></span>
* <span data-ttu-id="da86d-239">`Edit(int)` je vybrána, pokud je [příkaz HTTP](#verb) cokoliv jiného.</span><span class="sxs-lookup"><span data-stu-id="da86d-239">`Edit(int)` is selected when the [HTTP verb](#verb) is anything else.</span></span> <span data-ttu-id="da86d-240">`Edit(int)` obvykle se volá prostřednictvím `GET` .</span><span class="sxs-lookup"><span data-stu-id="da86d-240">`Edit(int)` is generally called via `GET`.</span></span>

<span data-ttu-id="da86d-241"><xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute> `[HttpPost]` Služba poskytuje směrování, aby se mohla zvolit na základě metody HTTP žádosti.</span><span class="sxs-lookup"><span data-stu-id="da86d-241">The <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute>, `[HttpPost]`, is provided to routing so that it can choose based on the HTTP method of the request.</span></span> <span data-ttu-id="da86d-242">Díky tomu je `HttpPostAttribute` `Edit(int, Product)` lepší shoda než `Edit(int)` .</span><span class="sxs-lookup"><span data-stu-id="da86d-242">The `HttpPostAttribute` makes `Edit(int, Product)` a better match than `Edit(int)`.</span></span>

<span data-ttu-id="da86d-243">Je důležité pochopit role, jako je například `HttpPostAttribute` .</span><span class="sxs-lookup"><span data-stu-id="da86d-243">It's important to understand the role of attributes like `HttpPostAttribute`.</span></span> <span data-ttu-id="da86d-244">Podobné atributy jsou definovány pro jiné [příkazy HTTP](#verb).</span><span class="sxs-lookup"><span data-stu-id="da86d-244">Similar attributes are defined for other [HTTP verbs](#verb).</span></span> <span data-ttu-id="da86d-245">V případě [konvenčního směrování](#cr)je běžné, že akce používají stejný název akce, když jsou součástí formuláře zobrazení, odeslání pracovního postupu formuláře.</span><span class="sxs-lookup"><span data-stu-id="da86d-245">In [conventional routing](#cr), it's common for actions to use the same action name when they're part of a show form, submit form workflow.</span></span> <span data-ttu-id="da86d-246">Příklad naleznete v tématu [prostudování dvou metod Edit Action](xref:tutorials/first-mvc-app/controller-methods-views#get-post).</span><span class="sxs-lookup"><span data-stu-id="da86d-246">For example, see [Examine the two Edit action methods](xref:tutorials/first-mvc-app/controller-methods-views#get-post).</span></span>

<span data-ttu-id="da86d-247">Pokud směrování nemůže zvolit nejlepší kandidáta, <xref:System.Reflection.AmbiguousMatchException> je vyvolána výjimka, která obsahuje seznam více odpovídajících koncových bodů.</span><span class="sxs-lookup"><span data-stu-id="da86d-247">If routing can't choose a best candidate, an <xref:System.Reflection.AmbiguousMatchException> is thrown, listing the multiple matched endpoints.</span></span>

<a name="routing-route-name-ref-label"></a>

### <a name="conventional-route-names"></a><span data-ttu-id="da86d-248">Názvy konvenčních tras</span><span class="sxs-lookup"><span data-stu-id="da86d-248">Conventional route names</span></span>

<span data-ttu-id="da86d-249">Řetězce  `"blog"` a `"default"` v následujících příkladech jsou konvenční názvy tras:</span><span class="sxs-lookup"><span data-stu-id="da86d-249">The strings  `"blog"` and `"default"` in the following examples are conventional route names:</span></span>

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

<span data-ttu-id="da86d-250">Názvy tras udávají logický název trasy.</span><span class="sxs-lookup"><span data-stu-id="da86d-250">The route names give the route a logical name.</span></span> <span data-ttu-id="da86d-251">Pojmenovanou trasu lze použít pro generování adresy URL.</span><span class="sxs-lookup"><span data-stu-id="da86d-251">The named route can be used for URL generation.</span></span> <span data-ttu-id="da86d-252">Použití pojmenované trasy zjednodušuje vytváření adresy URL, když řazení tras může způsobit komplikované generování adresy URL.</span><span class="sxs-lookup"><span data-stu-id="da86d-252">Using a named route simplifies URL creation when the ordering of routes could make URL generation complicated.</span></span> <span data-ttu-id="da86d-253">Názvy tras musí být jedinečné aplikace v širším rozsahu.</span><span class="sxs-lookup"><span data-stu-id="da86d-253">Route names must be unique application wide.</span></span>

<span data-ttu-id="da86d-254">Názvy tras:</span><span class="sxs-lookup"><span data-stu-id="da86d-254">Route names:</span></span>

* <span data-ttu-id="da86d-255">Nemusíte mít žádný vliv na adresu URL ani manipulaci s požadavky.</span><span class="sxs-lookup"><span data-stu-id="da86d-255">Have no impact on URL matching or handling of requests.</span></span>
* <span data-ttu-id="da86d-256">Jsou používány pouze pro generování adresy URL.</span><span class="sxs-lookup"><span data-stu-id="da86d-256">Are used only for URL generation.</span></span>

<span data-ttu-id="da86d-257">Koncept názvu trasy je reprezentován ve směrování jako [IEndpointNameMetadata](xref:Microsoft.AspNetCore.Routing.IEndpointNameMetadata).</span><span class="sxs-lookup"><span data-stu-id="da86d-257">The route name concept is represented in routing as [IEndpointNameMetadata](xref:Microsoft.AspNetCore.Routing.IEndpointNameMetadata).</span></span> <span data-ttu-id="da86d-258">Název **Směrování** a název **koncového bodu**:</span><span class="sxs-lookup"><span data-stu-id="da86d-258">The terms **route name** and **endpoint name**:</span></span>

* <span data-ttu-id="da86d-259">Jsou zaměnitelné.</span><span class="sxs-lookup"><span data-stu-id="da86d-259">Are interchangeable.</span></span>
* <span data-ttu-id="da86d-260">Který je používán v dokumentaci a kód závisí na popisovaném rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="da86d-260">Which one is used in documentation and code depends on the API being described.</span></span>

<a name="attribute-routing-ref-label"></a>
<a name="ar"></a>

## <a name="attribute-routing-for-rest-apis"></a><span data-ttu-id="da86d-261">Směrování atributů pro rozhraní REST API</span><span class="sxs-lookup"><span data-stu-id="da86d-261">Attribute routing for REST APIs</span></span>

<span data-ttu-id="da86d-262">Rozhraní REST API by měly používat směrování atributů k modelování funkčnosti aplikace jako sady prostředků, ve kterých jsou operace reprezentované [příkazy HTTP](#verb).</span><span class="sxs-lookup"><span data-stu-id="da86d-262">REST APIs should use attribute routing to model the app's functionality as a set of resources where operations are represented by [HTTP verbs](#verb).</span></span>

<span data-ttu-id="da86d-263">Směrování atributů používá sadu atributů k mapování akcí přímo na šablony směrování.</span><span class="sxs-lookup"><span data-stu-id="da86d-263">Attribute routing uses a set of attributes to map actions directly to route templates.</span></span> <span data-ttu-id="da86d-264">Následující `StartUp.Configure` kód je typický pro REST API a používá se v další ukázce:</span><span class="sxs-lookup"><span data-stu-id="da86d-264">The following `StartUp.Configure` code is typical for a REST API and is used in the next sample:</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupAPI.cs?name=snippet)]

<span data-ttu-id="da86d-265">V předchozím kódu <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers%2A> je volána uvnitř `UseEndpoints` pro mapování řadičů směrovaných na atribut.</span><span class="sxs-lookup"><span data-stu-id="da86d-265">In the preceding code, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers%2A> is called inside `UseEndpoints` to map attribute routed controllers.</span></span>

<span data-ttu-id="da86d-266">V následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="da86d-266">In the following example:</span></span>

* <span data-ttu-id="da86d-267">Předchozí `Configure` metoda se používá.</span><span class="sxs-lookup"><span data-stu-id="da86d-267">The preceding `Configure` method is used.</span></span>
* <span data-ttu-id="da86d-268">`HomeController` vyhledá shodu se sadou adres URL podobných tomu, co výchozí konvenční trasa `{controller=Home}/{action=Index}/{id?}` odpovídá.</span><span class="sxs-lookup"><span data-stu-id="da86d-268">`HomeController` matches a set of URLs similar to what the default conventional route `{controller=Home}/{action=Index}/{id?}` matches.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet2)]

<span data-ttu-id="da86d-269">`HomeController.Index`Akce se spustí pro všechny cesty URL `/` , `/Home` , `/Home/Index` nebo `/Home/Index/3` .</span><span class="sxs-lookup"><span data-stu-id="da86d-269">The `HomeController.Index` action is run for any of the URL paths `/`, `/Home`, `/Home/Index`, or `/Home/Index/3`.</span></span>

<span data-ttu-id="da86d-270">V tomto příkladu se zvýrazní klíčový rozdíl mezi směrováním atributů a [konvenčním směrováním](#cr).</span><span class="sxs-lookup"><span data-stu-id="da86d-270">This example highlights a key programming difference between attribute routing and [conventional routing](#cr).</span></span> <span data-ttu-id="da86d-271">Směrování atributů vyžaduje více vstupu pro určení trasy.</span><span class="sxs-lookup"><span data-stu-id="da86d-271">Attribute routing requires more input to specify a route.</span></span> <span data-ttu-id="da86d-272">Konvenční výchozí trasa zpracovává trasy více stručně.</span><span class="sxs-lookup"><span data-stu-id="da86d-272">The conventional default route handles routes more succinctly.</span></span> <span data-ttu-id="da86d-273">Směrování atributů ale umožňuje a vyžaduje přesnou kontrolu nad tím, které šablony směrování se vztahují na každou [akci](#action).</span><span class="sxs-lookup"><span data-stu-id="da86d-273">However, attribute routing allows and requires precise control of which route templates apply to each [action](#action).</span></span>

<span data-ttu-id="da86d-274">Při směrování atributů nehraje název kontroleru a akce žádnou část, ve které se akce shoduje, pokud se nepoužije [náhrada tokenu](#routing-token-replacement-templates-ref-label) .</span><span class="sxs-lookup"><span data-stu-id="da86d-274">With attribute routing, the controller and action names play no part in which action is matched, unless [token replacement](#routing-token-replacement-templates-ref-label) is used.</span></span> <span data-ttu-id="da86d-275">V následujícím příkladu se shodují stejné adresy URL jako v předchozím příkladu:</span><span class="sxs-lookup"><span data-stu-id="da86d-275">The following example matches the same URLs as the previous example:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemoController.cs?name=snippet)]

<span data-ttu-id="da86d-276">Následující kód používá nahrazení tokenu pro `action` a `controller` :</span><span class="sxs-lookup"><span data-stu-id="da86d-276">The following code uses token replacement for `action` and `controller`:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet22)]

<span data-ttu-id="da86d-277">Následující kód platí `[Route("[controller]/[action]")]` pro kontroler:</span><span class="sxs-lookup"><span data-stu-id="da86d-277">The following code applies `[Route("[controller]/[action]")]` to the controller:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet24)]

<span data-ttu-id="da86d-278">V předchozím kódu `Index` musí šablony metody předřadit `/` nebo `~/` do šablon směrování.</span><span class="sxs-lookup"><span data-stu-id="da86d-278">In the preceding code, the `Index` method templates must prepend `/` or `~/` to the route templates.</span></span> <span data-ttu-id="da86d-279">Šablony směrování použité pro akci, která začíná `/` nebo `~/` není kombinována s šablonami směrování použitými pro kontroler.</span><span class="sxs-lookup"><span data-stu-id="da86d-279">Route templates applied to an action that begin with `/` or `~/` don't get combined with route templates applied to the controller.</span></span>

<span data-ttu-id="da86d-280">Informace o výběru šablony směrování najdete v tématu [Priorita šablony směrování](xref:fundamentals/routing#rtp) .</span><span class="sxs-lookup"><span data-stu-id="da86d-280">See [Route template precedence](xref:fundamentals/routing#rtp) for information on route template selection.</span></span>

## <a name="reserved-routing-names"></a><span data-ttu-id="da86d-281">Názvy rezervovaných směrování</span><span class="sxs-lookup"><span data-stu-id="da86d-281">Reserved routing names</span></span>

<span data-ttu-id="da86d-282">Následující klíčová slova jsou rezervované názvy parametrů tras při použití řadičů nebo Razor stránek:</span><span class="sxs-lookup"><span data-stu-id="da86d-282">The following keywords are reserved route parameter names when using Controllers or Razor Pages:</span></span>

* `action`
* `area`
* `controller`
* `handler`
* `page`

<span data-ttu-id="da86d-283">Použití `page` jako parametr trasy se směrováním atributů je běžná chyba.</span><span class="sxs-lookup"><span data-stu-id="da86d-283">Using `page` as a route parameter with attribute routing is a common error.</span></span> <span data-ttu-id="da86d-284">Výsledkem je nekonzistentní a matoucí chování s generováním adresy URL.</span><span class="sxs-lookup"><span data-stu-id="da86d-284">Doing that results in inconsistent and confusing behavior with URL generation.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemo2Controller.cs?name=snippet)]

<span data-ttu-id="da86d-285">Speciální názvy parametrů jsou používány generováním adresy URL k určení, zda operace generování adresy URL odkazuje na Razor stránku nebo na kontroler.</span><span class="sxs-lookup"><span data-stu-id="da86d-285">The special parameter names are used by the URL generation to determine if a URL generation operation refers to a Razor Page or to a Controller.</span></span>

<a name="verb"></a>

## <a name="http-verb-templates"></a><span data-ttu-id="da86d-286">Šablony příkazů HTTP</span><span class="sxs-lookup"><span data-stu-id="da86d-286">HTTP verb templates</span></span>

<span data-ttu-id="da86d-287">ASP.NET Core má následující šablony příkazů HTTP:</span><span class="sxs-lookup"><span data-stu-id="da86d-287">ASP.NET Core has the following HTTP verb templates:</span></span>

* <span data-ttu-id="da86d-288">[HttpGet](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)</span><span class="sxs-lookup"><span data-stu-id="da86d-288">[[HttpGet]](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)</span></span>
* <span data-ttu-id="da86d-289">[HTTPPOST](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute)</span><span class="sxs-lookup"><span data-stu-id="da86d-289">[[HttpPost]](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute)</span></span>
* <span data-ttu-id="da86d-290">[[HttpPut]](xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute)</span><span class="sxs-lookup"><span data-stu-id="da86d-290">[[HttpPut]](xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute)</span></span>
* <span data-ttu-id="da86d-291">[[HttpDelete]](xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute)</span><span class="sxs-lookup"><span data-stu-id="da86d-291">[[HttpDelete]](xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute)</span></span>
* <span data-ttu-id="da86d-292">[[HttpHead]](xref:Microsoft.AspNetCore.Mvc.HttpHeadAttribute)</span><span class="sxs-lookup"><span data-stu-id="da86d-292">[[HttpHead]](xref:Microsoft.AspNetCore.Mvc.HttpHeadAttribute)</span></span>
* <span data-ttu-id="da86d-293">[[HttpPatch]](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)</span><span class="sxs-lookup"><span data-stu-id="da86d-293">[[HttpPatch]](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)</span></span>

<a name="rt"></a>

### <a name="route-templates"></a><span data-ttu-id="da86d-294">Šablony směrování</span><span class="sxs-lookup"><span data-stu-id="da86d-294">Route templates</span></span>

<span data-ttu-id="da86d-295">ASP.NET Core má následující šablony tras:</span><span class="sxs-lookup"><span data-stu-id="da86d-295">ASP.NET Core has the following route templates:</span></span>

* <span data-ttu-id="da86d-296">Všechny [šablony operací HTTP](#verb) jsou šablony směrování.</span><span class="sxs-lookup"><span data-stu-id="da86d-296">All the [HTTP verb templates](#verb) are route templates.</span></span>
* <span data-ttu-id="da86d-297">[Cestě](xref:Microsoft.AspNetCore.Mvc.RouteAttribute)</span><span class="sxs-lookup"><span data-stu-id="da86d-297">[[Route]](xref:Microsoft.AspNetCore.Mvc.RouteAttribute)</span></span>

<a name="arx"></a>

### <a name="attribute-routing-with-http-verb-attributes"></a><span data-ttu-id="da86d-298">Směrování atributů s atributy příkazu http</span><span class="sxs-lookup"><span data-stu-id="da86d-298">Attribute routing with Http verb attributes</span></span>

<span data-ttu-id="da86d-299">Vezměte v úvahu následující kontroler:</span><span class="sxs-lookup"><span data-stu-id="da86d-299">Consider the following controller:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet)]

<span data-ttu-id="da86d-300">V předchozím kódu:</span><span class="sxs-lookup"><span data-stu-id="da86d-300">In the preceding code:</span></span>

* <span data-ttu-id="da86d-301">Každá akce obsahuje `[HttpGet]` atribut, který omezuje porovnání pouze s požadavky HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="da86d-301">Each action contains the `[HttpGet]` attribute, which constrains matching to HTTP GET requests only.</span></span>
* <span data-ttu-id="da86d-302">Tato `GetProduct` akce zahrnuje `"{id}"` šablonu, proto `id` je připojená k `"api/[controller]"` šabloně na řadiči.</span><span class="sxs-lookup"><span data-stu-id="da86d-302">The `GetProduct` action includes the `"{id}"` template, therefore `id` is appended to the `"api/[controller]"` template on the controller.</span></span> <span data-ttu-id="da86d-303">Šablona metod je `"api/[controller]/"{id}""` .</span><span class="sxs-lookup"><span data-stu-id="da86d-303">The methods template is `"api/[controller]/"{id}""`.</span></span> <span data-ttu-id="da86d-304">Proto tato akce odpovídá pouze požadavkům get pro formulář `/api/test2/xyz` , `/api/test2/123` , `/api/test2/{any string}` atd.</span><span class="sxs-lookup"><span data-stu-id="da86d-304">Therefore this action only matches GET requests of for the form `/api/test2/xyz`,`/api/test2/123`,`/api/test2/{any string}`, etc.</span></span>
  [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet2)]
* <span data-ttu-id="da86d-305">`GetIntProduct`Akce obsahuje `"int/{id:int}")` šablonu.</span><span class="sxs-lookup"><span data-stu-id="da86d-305">The `GetIntProduct` action contains the `"int/{id:int}")` template.</span></span> <span data-ttu-id="da86d-306">`:int`Část šablony omezuje `id` hodnoty směrování na řetězce, které lze převést na celé číslo.</span><span class="sxs-lookup"><span data-stu-id="da86d-306">The `:int` portion of the template constrains the `id` route values to strings that can be converted to an integer.</span></span> <span data-ttu-id="da86d-307">Požadavek GET na `/api/test2/int/abc` :</span><span class="sxs-lookup"><span data-stu-id="da86d-307">A GET request to `/api/test2/int/abc`:</span></span>
  * <span data-ttu-id="da86d-308">Neodpovídá této akci.</span><span class="sxs-lookup"><span data-stu-id="da86d-308">Doesn't match this action.</span></span>
  * <span data-ttu-id="da86d-309">Vrátí chybu typu 404, která [nebyla nalezena](https://developer.mozilla.org/docs/Web/HTTP/Status/404) .</span><span class="sxs-lookup"><span data-stu-id="da86d-309">Returns a [404 Not Found](https://developer.mozilla.org/docs/Web/HTTP/Status/404) error.</span></span>
    [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet3)]
* <span data-ttu-id="da86d-310">`GetInt2Product`Akce obsahuje `{id}` v šabloně, ale neomezí `id` na hodnoty, které lze převést na celé číslo.</span><span class="sxs-lookup"><span data-stu-id="da86d-310">The `GetInt2Product` action contains `{id}` in the template, but doesn't constrain `id` to values that can be converted to an integer.</span></span> <span data-ttu-id="da86d-311">Požadavek GET na `/api/test2/int2/abc` :</span><span class="sxs-lookup"><span data-stu-id="da86d-311">A GET request to `/api/test2/int2/abc`:</span></span>
  * <span data-ttu-id="da86d-312">Odpovídá této trase.</span><span class="sxs-lookup"><span data-stu-id="da86d-312">Matches this route.</span></span>
  * <span data-ttu-id="da86d-313">Vazbu modelu se nepodařilo převést `abc` na celé číslo.</span><span class="sxs-lookup"><span data-stu-id="da86d-313">Model binding fails to convert `abc` to an integer.</span></span> <span data-ttu-id="da86d-314">`id`Parametr metody je celé číslo.</span><span class="sxs-lookup"><span data-stu-id="da86d-314">The `id` parameter of the method is integer.</span></span>
  * <span data-ttu-id="da86d-315">Vrátí [chybný požadavek 400](https://developer.mozilla.org/docs/Web/HTTP/Status/400) , protože vazba modelu selhala při převodu `abc` na celé číslo.</span><span class="sxs-lookup"><span data-stu-id="da86d-315">Returns a [400 Bad Request](https://developer.mozilla.org/docs/Web/HTTP/Status/400) because model binding failed to convert`abc` to an integer.</span></span>
      [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet4)]

<span data-ttu-id="da86d-316">Směrování atributů může používat <xref:Microsoft.AspNetCore.Mvc.Routing.HttpMethodAttribute> atributy jako <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute> , <xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute> a <xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute> .</span><span class="sxs-lookup"><span data-stu-id="da86d-316">Attribute routing can use <xref:Microsoft.AspNetCore.Mvc.Routing.HttpMethodAttribute> attributes such as <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute>, <xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute>, and <xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute>.</span></span> <span data-ttu-id="da86d-317">Všechny atributy [příkazu http](#verb) přijímají šablonu směrování.</span><span class="sxs-lookup"><span data-stu-id="da86d-317">All of the [HTTP verb](#verb) attributes accept a route template.</span></span> <span data-ttu-id="da86d-318">Následující příklad ukazuje dvě akce, které odpovídají stejné šabloně směrování:</span><span class="sxs-lookup"><span data-stu-id="da86d-318">The following example shows two actions that match the same route template:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyProductsController.cs?name=snippet1)]

<span data-ttu-id="da86d-319">Pomocí cesty URL `/products3` :</span><span class="sxs-lookup"><span data-stu-id="da86d-319">Using the URL path `/products3`:</span></span>

* <span data-ttu-id="da86d-320">`MyProductsController.ListProducts`Akce se spustí, když je [příkaz HTTP](#verb) `GET` .</span><span class="sxs-lookup"><span data-stu-id="da86d-320">The `MyProductsController.ListProducts` action runs when the [HTTP verb](#verb) is `GET`.</span></span>
* <span data-ttu-id="da86d-321">`MyProductsController.CreateProduct`Akce se spustí, když je [příkaz HTTP](#verb) `POST` .</span><span class="sxs-lookup"><span data-stu-id="da86d-321">The `MyProductsController.CreateProduct` action runs when the [HTTP verb](#verb) is `POST`.</span></span>

<span data-ttu-id="da86d-322">Při sestavování REST API je zřídka nutné použít `[Route(...)]` metodu akce, protože akce akceptuje všechny metody HTTP.</span><span class="sxs-lookup"><span data-stu-id="da86d-322">When building a REST API, it's rare that you'll need to use `[Route(...)]` on an action method because the action accepts all HTTP methods.</span></span> <span data-ttu-id="da86d-323">Je lepší použít konkrétnější [atribut příkazu http](#verb) , abyste mohli přesně zjistit, co vaše rozhraní API podporuje.</span><span class="sxs-lookup"><span data-stu-id="da86d-323">It's better to use the more specific [HTTP verb attribute](#verb) to be precise about what your API supports.</span></span> <span data-ttu-id="da86d-324">Očekává se, že klienti rozhraní REST API znají, které cesty a příkazy HTTP se mapují na konkrétní logické operace.</span><span class="sxs-lookup"><span data-stu-id="da86d-324">Clients of REST APIs are expected to know what paths and HTTP verbs map to specific logical operations.</span></span>

<span data-ttu-id="da86d-325">Rozhraní REST API by měly používat směrování atributů k modelování funkčnosti aplikace jako sady prostředků, ve kterých jsou operace reprezentované příkazy HTTP.</span><span class="sxs-lookup"><span data-stu-id="da86d-325">REST APIs should use attribute routing to model the app's functionality as a set of resources where operations are represented by HTTP verbs.</span></span> <span data-ttu-id="da86d-326">To znamená, že mnohé operace, například GET a POST u stejného logického prostředku, používají stejnou adresu URL.</span><span class="sxs-lookup"><span data-stu-id="da86d-326">This means that many operations, for example, GET and POST on the same logical resource use the same URL.</span></span> <span data-ttu-id="da86d-327">Směrování atributů poskytuje úroveň řízení, která je nutná k pečlivému návrhu rozložení veřejného koncového bodu rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="da86d-327">Attribute routing provides a level of control that's needed to carefully design an API's public endpoint layout.</span></span>

<span data-ttu-id="da86d-328">Vzhledem k tomu, že trasa atributu se vztahuje na konkrétní akci, je snadné vytvořit parametry požadované v rámci definice šablony trasy.</span><span class="sxs-lookup"><span data-stu-id="da86d-328">Since an attribute route applies to a specific action, it's easy to make parameters required as part of the route template definition.</span></span> <span data-ttu-id="da86d-329">V následujícím příkladu `id` je vyžadována jako součást cesty URL:</span><span class="sxs-lookup"><span data-stu-id="da86d-329">In the following example, `id` is required as part of the URL path:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet2)]

<span data-ttu-id="da86d-330">`Products2ApiController.GetProduct(int)`Akce:</span><span class="sxs-lookup"><span data-stu-id="da86d-330">The `Products2ApiController.GetProduct(int)` action:</span></span>

* <span data-ttu-id="da86d-331">Se spouští s cestou URL jako `/products2/3`</span><span class="sxs-lookup"><span data-stu-id="da86d-331">Is run with URL path like `/products2/3`</span></span>
* <span data-ttu-id="da86d-332">Není spuštěn s cestou URL `/products2` .</span><span class="sxs-lookup"><span data-stu-id="da86d-332">Isn't run with the URL path `/products2`.</span></span>

<span data-ttu-id="da86d-333">Atribut [[](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>) ] umožňuje akci omezit podporované typy obsahu požadavků.</span><span class="sxs-lookup"><span data-stu-id="da86d-333">The [[Consumes]](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>) attribute allows an action to limit the supported request content types.</span></span> <span data-ttu-id="da86d-334">Další informace najdete v tématu [Definování podporovaných typů obsahu požadavků pomocí atributu spotřebes](xref:web-api/index#consumes).</span><span class="sxs-lookup"><span data-stu-id="da86d-334">For more information, see [Define supported request content types with the Consumes attribute](xref:web-api/index#consumes).</span></span>

 <span data-ttu-id="da86d-335">Úplný popis šablon směrování a souvisejících možností najdete v tématu věnovaném [Směrování](xref:fundamentals/routing) .</span><span class="sxs-lookup"><span data-stu-id="da86d-335">See [Routing](xref:fundamentals/routing) for a full description of route templates and related options.</span></span>

<span data-ttu-id="da86d-336">Další informace o naleznete v `[ApiController]` tématu [atribut ApiController](xref:web-api/index##apicontroller-attribute).</span><span class="sxs-lookup"><span data-stu-id="da86d-336">For more information on `[ApiController]`, see [ApiController attribute](xref:web-api/index##apicontroller-attribute).</span></span>

## <a name="route-name"></a><span data-ttu-id="da86d-337">Název trasy</span><span class="sxs-lookup"><span data-stu-id="da86d-337">Route name</span></span>

<span data-ttu-id="da86d-338">Následující kód definuje název trasy `Products_List` :</span><span class="sxs-lookup"><span data-stu-id="da86d-338">The following code  defines a route name of `Products_List`:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet2)]

<span data-ttu-id="da86d-339">Názvy tras se dají použít k vygenerování adresy URL na základě konkrétní trasy.</span><span class="sxs-lookup"><span data-stu-id="da86d-339">Route names can be used to generate a URL based on a specific route.</span></span> <span data-ttu-id="da86d-340">Názvy tras:</span><span class="sxs-lookup"><span data-stu-id="da86d-340">Route names:</span></span>

* <span data-ttu-id="da86d-341">Nemá žádný vliv na chování směrování, které odpovídá adrese URL.</span><span class="sxs-lookup"><span data-stu-id="da86d-341">Have no impact on the URL matching behavior of routing.</span></span>
* <span data-ttu-id="da86d-342">Používá se pouze pro generování adresy URL.</span><span class="sxs-lookup"><span data-stu-id="da86d-342">Are only used for URL generation.</span></span>

<span data-ttu-id="da86d-343">Názvy tras musí být jedinečné aplikace v rozsahu.</span><span class="sxs-lookup"><span data-stu-id="da86d-343">Route names must be unique application-wide.</span></span>

<span data-ttu-id="da86d-344">Na rozdíl od předchozího kódu s konvenční výchozí trasou, která definuje `id` parametr jako volitelné ( `{id?}` ).</span><span class="sxs-lookup"><span data-stu-id="da86d-344">Contrast the preceding code with the conventional default route, which defines the `id` parameter as optional (`{id?}`).</span></span> <span data-ttu-id="da86d-345">Možnost přesně zadat rozhraní API má výhody, jako je povolení `/products` a `/products/5` odeslání do různých akcí.</span><span class="sxs-lookup"><span data-stu-id="da86d-345">The ability to precisely specify APIs has advantages, such as  allowing `/products` and `/products/5` to be dispatched to different actions.</span></span>

<a name="routing-combining-ref-label"></a>

## <a name="combining-attribute-routes"></a><span data-ttu-id="da86d-346">Kombinování tras atributů</span><span class="sxs-lookup"><span data-stu-id="da86d-346">Combining attribute routes</span></span>

<span data-ttu-id="da86d-347">Aby bylo směrování atributů méně opakované, jsou atributy směrování na kontroleru kombinovány s atributy směrování na jednotlivých akcích.</span><span class="sxs-lookup"><span data-stu-id="da86d-347">To make attribute routing less repetitive, route attributes on the controller are combined with route attributes on the individual actions.</span></span> <span data-ttu-id="da86d-348">Každá šablona trasy definovaná na řadiči je předá směrování šablon na akcích.</span><span class="sxs-lookup"><span data-stu-id="da86d-348">Any route templates defined on the controller are prepended to route templates on the actions.</span></span> <span data-ttu-id="da86d-349">Umístěním atributu směrování na řadiči se vytvoří **všechny** akce v řadiči použít směrování atributů.</span><span class="sxs-lookup"><span data-stu-id="da86d-349">Placing a route attribute on the controller makes **all** actions in the controller use attribute routing.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet)]

<span data-ttu-id="da86d-350">V předchozím příkladu:</span><span class="sxs-lookup"><span data-stu-id="da86d-350">In the preceding example:</span></span>

* <span data-ttu-id="da86d-351">Cesta URL se `/products` může shodovat. `ProductsApi.ListProducts`</span><span class="sxs-lookup"><span data-stu-id="da86d-351">The URL path `/products` can match `ProductsApi.ListProducts`</span></span>
* <span data-ttu-id="da86d-352">Cesta URL se `/products/5` může shodovat `ProductsApi.GetProduct(int)` .</span><span class="sxs-lookup"><span data-stu-id="da86d-352">The URL path `/products/5` can match `ProductsApi.GetProduct(int)`.</span></span>

<span data-ttu-id="da86d-353">Obě tyto akce odpovídají pouze HTTP, `GET` protože jsou označeny `[HttpGet]` atributem.</span><span class="sxs-lookup"><span data-stu-id="da86d-353">Both of these actions only match HTTP `GET` because they're marked with the `[HttpGet]` attribute.</span></span>

<span data-ttu-id="da86d-354">Šablony směrování použité pro akci, která začíná `/` nebo `~/` není kombinována s šablonami směrování použitými pro kontroler.</span><span class="sxs-lookup"><span data-stu-id="da86d-354">Route templates applied to an action that begin with `/` or `~/` don't get combined with route templates applied to the controller.</span></span> <span data-ttu-id="da86d-355">Následující příklad odpovídá sadě cest URL podobně jako výchozí trasa.</span><span class="sxs-lookup"><span data-stu-id="da86d-355">The following example matches a set of URL paths similar to the default route.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet)]

<span data-ttu-id="da86d-356">Následující tabulka popisuje `[Route]` atributy v předchozím kódu:</span><span class="sxs-lookup"><span data-stu-id="da86d-356">The following table explains the `[Route]` attributes in the preceding code:</span></span>

| <span data-ttu-id="da86d-357">Atribut</span><span class="sxs-lookup"><span data-stu-id="da86d-357">Attribute</span></span>               | <span data-ttu-id="da86d-358">Kombinuje s `[Route("Home")]`</span><span class="sxs-lookup"><span data-stu-id="da86d-358">Combines with `[Route("Home")]`</span></span> | <span data-ttu-id="da86d-359">Definuje šablonu směrování.</span><span class="sxs-lookup"><span data-stu-id="da86d-359">Defines route template</span></span> |
| ----------------- | ------------ | --------- |
| `[Route("")]` | <span data-ttu-id="da86d-360">Ano</span><span class="sxs-lookup"><span data-stu-id="da86d-360">Yes</span></span> | `"Home"` |
| `[Route("Index")]` | <span data-ttu-id="da86d-361">Ano</span><span class="sxs-lookup"><span data-stu-id="da86d-361">Yes</span></span> | `"Home/Index"` |
| `[Route("/")]` | <span data-ttu-id="da86d-362">**Ne**</span><span class="sxs-lookup"><span data-stu-id="da86d-362">**No**</span></span> | `""` |
| `[Route("About")]` | <span data-ttu-id="da86d-363">Ano</span><span class="sxs-lookup"><span data-stu-id="da86d-363">Yes</span></span> | `"Home/About"` |

<a name="routing-ordering-ref-label"></a>
<a name="oar"></a>

### <a name="attribute-route-order"></a><span data-ttu-id="da86d-364">Pořadí směrování atributu</span><span class="sxs-lookup"><span data-stu-id="da86d-364">Attribute route order</span></span>

<span data-ttu-id="da86d-365">Směrování sestaví strom a porovnává se všemi koncovými body současně:</span><span class="sxs-lookup"><span data-stu-id="da86d-365">Routing builds a tree and matches all endpoints simultaneously:</span></span>

* <span data-ttu-id="da86d-366">Položky směrování se chovají stejně, jako kdyby byly umístěny v ideálním pořadí.</span><span class="sxs-lookup"><span data-stu-id="da86d-366">The route entries behave as if placed in an ideal ordering.</span></span>
* <span data-ttu-id="da86d-367">Nejvíce konkrétní trasy mají možnost provést před obecnější trasy.</span><span class="sxs-lookup"><span data-stu-id="da86d-367">The most specific routes have a chance to execute before the more general routes.</span></span>

<span data-ttu-id="da86d-368">Například trasa atributu jako `blog/search/{topic}` je konkrétnější než trasa atributu jako `blog/{*article}` .</span><span class="sxs-lookup"><span data-stu-id="da86d-368">For example, an attribute route like `blog/search/{topic}` is more specific than an attribute route like `blog/{*article}`.</span></span> <span data-ttu-id="da86d-369">`blog/search/{topic}`Ve výchozím nastavení tras má vyšší prioritu, protože je konkrétnější.</span><span class="sxs-lookup"><span data-stu-id="da86d-369">The `blog/search/{topic}` route has higher priority, by default, because it's more specific.</span></span> <span data-ttu-id="da86d-370">Pomocí [konvenčního směrování](#cr)zodpovídá vývojář za umístění tras v požadovaném pořadí.</span><span class="sxs-lookup"><span data-stu-id="da86d-370">Using [conventional routing](#cr), the developer is responsible for placing routes in the desired order.</span></span>

<span data-ttu-id="da86d-371">Trasy atributů mohou konfigurovat objednávku pomocí <xref:Microsoft.AspNetCore.Mvc.RouteAttribute.Order> Vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="da86d-371">Attribute routes can configure an order using the <xref:Microsoft.AspNetCore.Mvc.RouteAttribute.Order> property.</span></span> <span data-ttu-id="da86d-372">Součástí jsou všechny [atributy tras](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) , které poskytuje rozhraní `Order` .</span><span class="sxs-lookup"><span data-stu-id="da86d-372">All of the framework provided [route attributes](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) include `Order` .</span></span> <span data-ttu-id="da86d-373">Trasy jsou zpracovávány podle vzestupného řazení `Order` Vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="da86d-373">Routes are processed according to an ascending sort of the `Order` property.</span></span> <span data-ttu-id="da86d-374">Výchozí pořadí je `0` .</span><span class="sxs-lookup"><span data-stu-id="da86d-374">The default order is `0`.</span></span> <span data-ttu-id="da86d-375">Nastavování trasy pomocí `Order = -1` spuštění před trasami, které nenastaví objednávku.</span><span class="sxs-lookup"><span data-stu-id="da86d-375">Setting a route using `Order = -1` runs before routes that don't set an order.</span></span> <span data-ttu-id="da86d-376">Nastavení trasy pomocí `Order = 1` spuštění po výchozím řazení směrování.</span><span class="sxs-lookup"><span data-stu-id="da86d-376">Setting a route using `Order = 1` runs after default route ordering.</span></span>

<span data-ttu-id="da86d-377">**Nepoužívejte** v závislosti `Order` .</span><span class="sxs-lookup"><span data-stu-id="da86d-377">**Avoid** depending on `Order`.</span></span> <span data-ttu-id="da86d-378">Pokud je místo na adrese URL aplikace nutné správně směrovat hodnoty pořadí, je pravděpodobné, že budou i u klientů matoucí.</span><span class="sxs-lookup"><span data-stu-id="da86d-378">If an app's URL-space requires explicit order values to route correctly, then it's likely confusing to clients as well.</span></span> <span data-ttu-id="da86d-379">Obecně platí, že směrování atributů vybere správnou trasu s odpovídající adresou URL.</span><span class="sxs-lookup"><span data-stu-id="da86d-379">In general, attribute routing selects the correct route with URL matching.</span></span> <span data-ttu-id="da86d-380">Pokud výchozí pořadí použité pro generování adresy URL nefunguje, je použití názvu trasy jako přepsání obvykle jednodušší než použití `Order` Vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="da86d-380">If the default order used for URL generation isn't working, using a route name as an override is usually simpler than applying the `Order` property.</span></span>

<span data-ttu-id="da86d-381">Vezměte v úvahu následující dva řadiče, které definují obě trasy `/home` :</span><span class="sxs-lookup"><span data-stu-id="da86d-381">Consider the following two controllers which both define the route matching `/home`:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet2)]

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemoController.cs?name=snippet)]

<span data-ttu-id="da86d-382">Požadavek `/home` s předchozím kódem vyvolá výjimku, která je podobná následující:</span><span class="sxs-lookup"><span data-stu-id="da86d-382">Requesting `/home` with the preceding code throws an exception similar to the following:</span></span>

```text
AmbiguousMatchException: The request matched multiple endpoints. Matches:

 WebMvcRouting.Controllers.HomeController.Index
 WebMvcRouting.Controllers.MyDemoController.MyIndex
```

<span data-ttu-id="da86d-383">Přidání `Order` do jednoho z atributů trasy řeší nejednoznačnost:</span><span class="sxs-lookup"><span data-stu-id="da86d-383">Adding `Order` to one of the route attributes resolves the ambiguity:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemo3Controller.cs?name=snippet3& highlight=2)]

<span data-ttu-id="da86d-384">S předchozím kódem `/home` spustí `HomeController.Index` koncový bod.</span><span class="sxs-lookup"><span data-stu-id="da86d-384">With the preceding code, `/home` runs the `HomeController.Index` endpoint.</span></span> <span data-ttu-id="da86d-385">Pro získání `MyDemoController.MyIndex` žádosti `/home/MyIndex` .</span><span class="sxs-lookup"><span data-stu-id="da86d-385">To get to the `MyDemoController.MyIndex`, request `/home/MyIndex`.</span></span> <span data-ttu-id="da86d-386">**Poznámka**:</span><span class="sxs-lookup"><span data-stu-id="da86d-386">**Note**:</span></span>

* <span data-ttu-id="da86d-387">Předchozí kód je příkladem nebo nekvalitní návrh směrování.</span><span class="sxs-lookup"><span data-stu-id="da86d-387">The preceding code is an example or poor routing design.</span></span> <span data-ttu-id="da86d-388">Byla použita k ilustraci `Order` Vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="da86d-388">It was used to illustrate the `Order` property.</span></span>
* <span data-ttu-id="da86d-389">`Order`Vlastnost pouze vyřeší nejednoznačnost, tuto šablonu nelze spárovat.</span><span class="sxs-lookup"><span data-stu-id="da86d-389">The `Order` property only resolves the ambiguity, that template cannot be matched.</span></span> <span data-ttu-id="da86d-390">Je lepší odebrat `[Route("Home")]` šablonu.</span><span class="sxs-lookup"><span data-stu-id="da86d-390">It would be better to remove the `[Route("Home")]` template.</span></span>

<span data-ttu-id="da86d-391">Podívejte se na [ Razor stránky směrování a konvence aplikace: pořadí tras](xref:razor-pages/razor-pages-conventions#route-order) pro informace o pořadí směrování se Razor stránkami.</span><span class="sxs-lookup"><span data-stu-id="da86d-391">See [Razor Pages route and app conventions: Route order](xref:razor-pages/razor-pages-conventions#route-order) for information on route order with Razor Pages.</span></span>

<span data-ttu-id="da86d-392">V některých případech se k chybě HTTP 500 vrátí nejednoznačné trasy.</span><span class="sxs-lookup"><span data-stu-id="da86d-392">In some cases, an HTTP 500 error is returned with ambiguous routes.</span></span> <span data-ttu-id="da86d-393">Pomocí [protokolování](xref:fundamentals/logging/index) zjistíte, které koncové body způsobily `AmbiguousMatchException` .</span><span class="sxs-lookup"><span data-stu-id="da86d-393">Use [logging](xref:fundamentals/logging/index) to see which endpoints caused the `AmbiguousMatchException`.</span></span>

<a name="routing-token-replacement-templates-ref-label"></a>

## <a name="token-replacement-in-route-templates-controller-action-area"></a><span data-ttu-id="da86d-394">Nahrazení tokenu v šablonách směrování [Controller], [akce], [oblast]</span><span class="sxs-lookup"><span data-stu-id="da86d-394">Token replacement in route templates [controller], [action], [area]</span></span>

<span data-ttu-id="da86d-395">Pro usnadnění práce, trasy atributů podporují nahrazení tokenů pro rezervované parametry tras uzavřením tokenu v jedné z následujících možností:</span><span class="sxs-lookup"><span data-stu-id="da86d-395">For convenience, attribute routes support token replacement for reserved route parameters by enclosing a token in one of the following:</span></span>

* <span data-ttu-id="da86d-396">Hranaté závorky: `[]`</span><span class="sxs-lookup"><span data-stu-id="da86d-396">Square brackets: `[]`</span></span>
* <span data-ttu-id="da86d-397">Složené závorky: `{}`</span><span class="sxs-lookup"><span data-stu-id="da86d-397">Curly braces: `{}`</span></span>

<span data-ttu-id="da86d-398">Tokeny `[action]` , `[area]` a `[controller]` jsou nahrazeny hodnotami názvu akce, názvu oblasti a názvu kontroleru z akce, kde je trasa definována:</span><span class="sxs-lookup"><span data-stu-id="da86d-398">The tokens `[action]`, `[area]`, and `[controller]` are replaced with the values of the action name, area name, and controller name from the action where the route is defined:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet)]

<span data-ttu-id="da86d-399">V předchozím kódu:</span><span class="sxs-lookup"><span data-stu-id="da86d-399">In the preceding code:</span></span>

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet10)]

  * <span data-ttu-id="da86d-400">Vyhovují `/Products0/List`</span><span class="sxs-lookup"><span data-stu-id="da86d-400">Matches `/Products0/List`</span></span>

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet11)]

  * <span data-ttu-id="da86d-401">Vyhovují `/Products0/Edit/{id}`</span><span class="sxs-lookup"><span data-stu-id="da86d-401">Matches `/Products0/Edit/{id}`</span></span>

<span data-ttu-id="da86d-402">Nahrazení tokenu probíhá jako poslední krok při vytváření tras atributů.</span><span class="sxs-lookup"><span data-stu-id="da86d-402">Token replacement occurs as the last step of building the attribute routes.</span></span> <span data-ttu-id="da86d-403">Předchozí příklad se chová stejně jako následující kód:</span><span class="sxs-lookup"><span data-stu-id="da86d-403">The preceding example behaves the same as the following code:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet20)]

[!INCLUDE[](~/includes/MTcomments.md)]

<span data-ttu-id="da86d-404">Trasy atributů je také možné kombinovat s dědičností.</span><span class="sxs-lookup"><span data-stu-id="da86d-404">Attribute routes can also be combined with inheritance.</span></span> <span data-ttu-id="da86d-405">Toto je výkonné v kombinaci s náhradou tokenu.</span><span class="sxs-lookup"><span data-stu-id="da86d-405">This is powerful combined with token replacement.</span></span> <span data-ttu-id="da86d-406">Nahrazení tokenu platí také pro názvy tras definované směrováními atributů.</span><span class="sxs-lookup"><span data-stu-id="da86d-406">Token replacement also applies to route names defined by attribute routes.</span></span>
<span data-ttu-id="da86d-407">`[Route("[controller]/[action]", Name="[controller]_[action]")]`vygeneruje jedinečný název trasy pro každou akci:</span><span class="sxs-lookup"><span data-stu-id="da86d-407">`[Route("[controller]/[action]", Name="[controller]_[action]")]`generates a unique route name for each action:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet5)]

<span data-ttu-id="da86d-408">Nahrazení tokenu platí také pro názvy tras definované směrováními atributů.</span><span class="sxs-lookup"><span data-stu-id="da86d-408">Token replacement also applies to route names defined by attribute routes.</span></span>
`[Route("[controller]/[action]", Name="[controller]_[action]")]`
<span data-ttu-id="da86d-409">vygeneruje jedinečný název trasy pro každou akci.</span><span class="sxs-lookup"><span data-stu-id="da86d-409">generates a unique route name for each action.</span></span>

<span data-ttu-id="da86d-410">Chcete-li spárovat oddělovač nahrazení tokenu literálu `[` nebo  `]` , zastavte ho opakováním znaku ( `[[` nebo `]]` ).</span><span class="sxs-lookup"><span data-stu-id="da86d-410">To match the literal token replacement delimiter `[` or  `]`, escape it by repeating the character (`[[` or `]]`).</span></span>

<a name="routing-token-replacement-transformers-ref-label"></a>

### <a name="use-a-parameter-transformer-to-customize-token-replacement"></a><span data-ttu-id="da86d-411">Použití transformátoru parametrů k přizpůsobení náhrady tokenu</span><span class="sxs-lookup"><span data-stu-id="da86d-411">Use a parameter transformer to customize token replacement</span></span>

<span data-ttu-id="da86d-412">Nahrazení tokenu se dá přizpůsobit pomocí transformátoru parametrů.</span><span class="sxs-lookup"><span data-stu-id="da86d-412">Token replacement can be customized using a parameter transformer.</span></span> <span data-ttu-id="da86d-413">Parametr Transformer implementuje <xref:Microsoft.AspNetCore.Routing.IOutboundParameterTransformer> a transformuje hodnotu parametrů.</span><span class="sxs-lookup"><span data-stu-id="da86d-413">A parameter transformer implements <xref:Microsoft.AspNetCore.Routing.IOutboundParameterTransformer> and transforms the value of parameters.</span></span> <span data-ttu-id="da86d-414">Například vlastní `SlugifyParameterTransformer` parametr Transformer změní `SubscriptionManagement` hodnotu trasy na `subscription-management` :</span><span class="sxs-lookup"><span data-stu-id="da86d-414">For example, a custom `SlugifyParameterTransformer` parameter transformer changes the `SubscriptionManagement` route value to `subscription-management`:</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupSlugifyParamTransformer.cs?name=snippet2)]

<span data-ttu-id="da86d-415"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention>Je konvence aplikačního modelu, která:</span><span class="sxs-lookup"><span data-stu-id="da86d-415">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention> is an application model convention that:</span></span>

* <span data-ttu-id="da86d-416">Aplikuje transformátor parametrů na všechny trasy atributů v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="da86d-416">Applies a parameter transformer to all attribute routes in an application.</span></span>
* <span data-ttu-id="da86d-417">Přizpůsobí hodnoty tokenu trasy při jejich nahrazení.</span><span class="sxs-lookup"><span data-stu-id="da86d-417">Customizes the attribute route token values as they are replaced.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/SubscriptionManagementController.cs?name=snippet)]

<span data-ttu-id="da86d-418">Předchozí `ListAll` Metoda odpovídá `/subscription-management/list-all` .</span><span class="sxs-lookup"><span data-stu-id="da86d-418">The preceding `ListAll` method matches `/subscription-management/list-all`.</span></span>

<span data-ttu-id="da86d-419">`RouteTokenTransformerConvention`Je zaregistrován jako možnost v `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="da86d-419">The `RouteTokenTransformerConvention` is registered as an option in `ConfigureServices`.</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupSlugifyParamTransformer.cs?name=snippet)]

<span data-ttu-id="da86d-420">Definice popisu najdete v tématu [MDN web docs on](https://developer.mozilla.org/docs/Glossary/Slug) .</span><span class="sxs-lookup"><span data-stu-id="da86d-420">See [MDN web docs on Slug](https://developer.mozilla.org/docs/Glossary/Slug) for the definition of Slug.</span></span>

[!INCLUDE[](~/includes/regex.md)]
<a name="routing-multiple-routes-ref-label"></a>

### <a name="multiple-attribute-routes"></a><span data-ttu-id="da86d-421">Trasy s více atributy</span><span class="sxs-lookup"><span data-stu-id="da86d-421">Multiple attribute routes</span></span>

<span data-ttu-id="da86d-422">Směrování atributů podporuje definování více tras, které dosáhnou stejné akce.</span><span class="sxs-lookup"><span data-stu-id="da86d-422">Attribute routing supports defining multiple routes that reach the same action.</span></span> <span data-ttu-id="da86d-423">Nejběžnějším využitím je napodobení chování výchozí konvenční trasy, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="da86d-423">The most common usage of this is to mimic the behavior of the default conventional route as shown in the following example:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet6x)]

<span data-ttu-id="da86d-424">Vložení více atributů trasy na řadič znamená, že každý z nich kombinuje s každým atributem směrování na metodách akcí:</span><span class="sxs-lookup"><span data-stu-id="da86d-424">Putting multiple route attributes on the controller means that each one combines with each of the route attributes on the action methods:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet6)]

<span data-ttu-id="da86d-425">Všechna omezení trasy [příkazů http](#verb) implementují `IActionConstraint` .</span><span class="sxs-lookup"><span data-stu-id="da86d-425">All the [HTTP verb](#verb) route constraints implement `IActionConstraint`.</span></span>

<span data-ttu-id="da86d-426">Pokud <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint> je pro akci umístěno více atributů tras, které implementují:</span><span class="sxs-lookup"><span data-stu-id="da86d-426">When multiple route attributes that implement <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint> are placed on an action:</span></span>

* <span data-ttu-id="da86d-427">Každé omezení akce kombinuje se šablonou směrování použitou pro kontroler.</span><span class="sxs-lookup"><span data-stu-id="da86d-427">Each action constraint combines with the route template applied to the controller.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet7)]

<span data-ttu-id="da86d-428">Použití více tras na akcích může vypadat jako užitečné a výkonné, takže je lepší zachovat místo na adrese URL vaší aplikace základní a dobře definovaný.</span><span class="sxs-lookup"><span data-stu-id="da86d-428">Using multiple routes on actions might seem useful and powerful, it's better to keep your app's URL space basic and well defined.</span></span> <span data-ttu-id="da86d-429">V případě potřeby použijte více tras **na akcích** , například pro podporu stávajících klientů.</span><span class="sxs-lookup"><span data-stu-id="da86d-429">Use multiple routes on actions **only** where needed, for example, to support existing clients.</span></span>

<a name="routing-attr-options"></a>

### <a name="specifying-attribute-route-optional-parameters-default-values-and-constraints"></a><span data-ttu-id="da86d-430">Určení volitelných parametrů trasy atributu, výchozích hodnot a omezení</span><span class="sxs-lookup"><span data-stu-id="da86d-430">Specifying attribute route optional parameters, default values, and constraints</span></span>

<span data-ttu-id="da86d-431">Trasy atributů podporují stejnou vloženou syntaxi jako konvenční trasy, aby bylo možné zadat volitelné parametry, výchozí hodnoty a omezení.</span><span class="sxs-lookup"><span data-stu-id="da86d-431">Attribute routes support the same inline syntax as conventional routes to specify optional parameters, default values, and constraints.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet8&highlight=3)]

<span data-ttu-id="da86d-432">V předchozím kódu `[HttpPost("product/{id:int}")]` platí omezení trasy.</span><span class="sxs-lookup"><span data-stu-id="da86d-432">In the preceding code, `[HttpPost("product/{id:int}")]` applies a route constraint.</span></span> <span data-ttu-id="da86d-433">Tato `ProductsController.ShowProduct` akce odpovídá pouze cestou URL jako `/product/3` .</span><span class="sxs-lookup"><span data-stu-id="da86d-433">The `ProductsController.ShowProduct` action is matched only by URL paths like `/product/3`.</span></span> <span data-ttu-id="da86d-434">Část šablony trasy `{id:int}` omezuje segment na pouze celá čísla.</span><span class="sxs-lookup"><span data-stu-id="da86d-434">The route template portion `{id:int}` constrains that segment to only integers.</span></span>

<span data-ttu-id="da86d-435">Podrobný popis syntaxe šablony směrování naleznete v tématu Referenční dokumentace k [šabloně směrování](xref:fundamentals/routing#route-template-reference) .</span><span class="sxs-lookup"><span data-stu-id="da86d-435">See [Route Template Reference](xref:fundamentals/routing#route-template-reference) for a detailed description of route template syntax.</span></span>

<a name="routing-cust-rt-attr-irt-ref-label"></a>

### <a name="custom-route-attributes-using-iroutetemplateprovider"></a><span data-ttu-id="da86d-436">Vlastní atributy směrování pomocí IRouteTemplateProvider</span><span class="sxs-lookup"><span data-stu-id="da86d-436">Custom route attributes using IRouteTemplateProvider</span></span>

<span data-ttu-id="da86d-437">Všechny [atributy směrování](#rt) implementují <xref:Microsoft.AspNetCore.Mvc.Routing.IRouteTemplateProvider> .</span><span class="sxs-lookup"><span data-stu-id="da86d-437">All of the [route attributes](#rt) implement <xref:Microsoft.AspNetCore.Mvc.Routing.IRouteTemplateProvider>.</span></span> <span data-ttu-id="da86d-438">Modul runtime ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="da86d-438">The ASP.NET Core runtime:</span></span>

* <span data-ttu-id="da86d-439">Vyhledá atributy tříd kontroleru a metody akcí při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="da86d-439">Looks for attributes on controller classes and action methods when the app starts.</span></span>
* <span data-ttu-id="da86d-440">Používá atributy, které implementují `IRouteTemplateProvider` k sestavení počáteční sady tras.</span><span class="sxs-lookup"><span data-stu-id="da86d-440">Uses the attributes that implement `IRouteTemplateProvider` to build the initial set of routes.</span></span>

<span data-ttu-id="da86d-441">Implementujte `IRouteTemplateProvider` pro definování vlastních atributů směrování.</span><span class="sxs-lookup"><span data-stu-id="da86d-441">Implement `IRouteTemplateProvider` to define custom route attributes.</span></span> <span data-ttu-id="da86d-442">Každá `IRouteTemplateProvider` z nich umožňuje definovat jednu trasu s vlastní šablonou směrování, objednávkou a názvem:</span><span class="sxs-lookup"><span data-stu-id="da86d-442">Each `IRouteTemplateProvider` allows you to define a single route with a custom route template, order, and name:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyTestApiController.cs?name=snippet&highlight=1-10)]

<span data-ttu-id="da86d-443">Předchozí `Get` Metoda vrátí `Order = 2, Template = api/MyTestApi` .</span><span class="sxs-lookup"><span data-stu-id="da86d-443">The preceding `Get` method returns `Order = 2, Template = api/MyTestApi`.</span></span>

<a name="routing-app-model-ref-label"></a>

### <a name="use-application-model-to-customize-attribute-routes"></a><span data-ttu-id="da86d-444">Použití aplikačního modelu k přizpůsobení tras atributů</span><span class="sxs-lookup"><span data-stu-id="da86d-444">Use application model to customize attribute routes</span></span>

<span data-ttu-id="da86d-445">Aplikační model:</span><span class="sxs-lookup"><span data-stu-id="da86d-445">The application model:</span></span>

* <span data-ttu-id="da86d-446">Je objektovým modelem vytvořeným při spuštění.</span><span class="sxs-lookup"><span data-stu-id="da86d-446">Is an object model created at startup.</span></span>
* <span data-ttu-id="da86d-447">Obsahuje všechna metadata používaná ASP.NET Core ke směrování a provádění akcí v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="da86d-447">Contains all of the metadata used by ASP.NET Core to route and execute the actions in an app.</span></span>

<span data-ttu-id="da86d-448">Aplikační model zahrnuje všechna data získaná z atributů směrování.</span><span class="sxs-lookup"><span data-stu-id="da86d-448">The application model includes all of the data gathered from route attributes.</span></span> <span data-ttu-id="da86d-449">Data z atributů trasy jsou k dispozici v `IRouteTemplateProvider` implementaci.</span><span class="sxs-lookup"><span data-stu-id="da86d-449">The data from route attributes is provided by the `IRouteTemplateProvider` implementation.</span></span> <span data-ttu-id="da86d-450">Úmluvy</span><span class="sxs-lookup"><span data-stu-id="da86d-450">Conventions:</span></span>

* <span data-ttu-id="da86d-451">Lze zapsat pro úpravu modelu aplikace, aby bylo možné přizpůsobit způsob, jakým se směrování chová.</span><span class="sxs-lookup"><span data-stu-id="da86d-451">Can be written to modify the application model to customize how routing behaves.</span></span>
* <span data-ttu-id="da86d-452">Jsou čteny při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="da86d-452">Are read at app startup.</span></span>

<span data-ttu-id="da86d-453">V této části najdete základní příklad přizpůsobení směrování pomocí aplikačního modelu.</span><span class="sxs-lookup"><span data-stu-id="da86d-453">This section shows a basic example of customizing routing using application model.</span></span> <span data-ttu-id="da86d-454">Následující kód vytvoří trasy přibližně v souladu se strukturou složek v projektu.</span><span class="sxs-lookup"><span data-stu-id="da86d-454">The following code makes routes roughly line up with the folder structure of the project.</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/NamespaceRoutingConvention.cs?name=snippet)]

<span data-ttu-id="da86d-455">Následující kód brání `namespace` použití úmluvy na řadičích, které jsou směrovány atributem:</span><span class="sxs-lookup"><span data-stu-id="da86d-455">The following code prevents the `namespace` convention from being applied to controllers that are attribute routed:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/NamespaceRoutingConvention.cs?name=snippet2)]

<span data-ttu-id="da86d-456">Například následující kontroler nepoužívá `NamespaceRoutingConvention` :</span><span class="sxs-lookup"><span data-stu-id="da86d-456">For example, the following controller doesn't use `NamespaceRoutingConvention`:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/ManagersController.cs?name=snippet&highlight=1)]

<span data-ttu-id="da86d-457">`NamespaceRoutingConvention.Apply`Metoda:</span><span class="sxs-lookup"><span data-stu-id="da86d-457">The `NamespaceRoutingConvention.Apply` method:</span></span>

* <span data-ttu-id="da86d-458">Neprovede žádnou akci, pokud je řadič směrován do atributu.</span><span class="sxs-lookup"><span data-stu-id="da86d-458">Does nothing if the controller is attribute routed.</span></span>
* <span data-ttu-id="da86d-459">Nastaví šablonu řadičů založenou na `namespace` , se základním `namespace` odebráním.</span><span class="sxs-lookup"><span data-stu-id="da86d-459">Sets the controllers template based on the `namespace`, with the base `namespace` removed.</span></span>

<span data-ttu-id="da86d-460">`NamespaceRoutingConvention`Lze použít v `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="da86d-460">The `NamespaceRoutingConvention` can be applied in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/Startup.cs?name=snippet&highlight=1,14-18)]

<span data-ttu-id="da86d-461">Zvažte například následující kontroler:</span><span class="sxs-lookup"><span data-stu-id="da86d-461">For example, consider the following controller:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/UsersController.cs)]

<span data-ttu-id="da86d-462">V předchozím kódu:</span><span class="sxs-lookup"><span data-stu-id="da86d-462">In the preceding code:</span></span>

* <span data-ttu-id="da86d-463">Základem `namespace` je `My.Application` .</span><span class="sxs-lookup"><span data-stu-id="da86d-463">The base `namespace` is `My.Application`.</span></span>
* <span data-ttu-id="da86d-464">Úplný název předchozího kontroleru je `My.Application.Admin.Controllers.UsersController` .</span><span class="sxs-lookup"><span data-stu-id="da86d-464">The full name of the preceding controller is `My.Application.Admin.Controllers.UsersController`.</span></span>
* <span data-ttu-id="da86d-465">`NamespaceRoutingConvention`Nastaví šablonu Controllers na `Admin/Controllers/Users/[action]/{id?` .</span><span class="sxs-lookup"><span data-stu-id="da86d-465">The `NamespaceRoutingConvention` sets the controllers template to `Admin/Controllers/Users/[action]/{id?`.</span></span>

<span data-ttu-id="da86d-466">`NamespaceRoutingConvention`Lze také použít jako atribut na řadiči:</span><span class="sxs-lookup"><span data-stu-id="da86d-466">The `NamespaceRoutingConvention` can also be applied as an attribute on a controller:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/TestController.cs?name=snippet&highlight=1)]

<a name="routing-mixed-ref-label"></a>

## <a name="mixed-routing-attribute-routing-vs-conventional-routing"></a><span data-ttu-id="da86d-467">Kombinované směrování: směrování atributů vs. konvenční směrování</span><span class="sxs-lookup"><span data-stu-id="da86d-467">Mixed routing: Attribute routing vs conventional routing</span></span>

<span data-ttu-id="da86d-468">Aplikace ASP.NET Core můžou kombinovat použití konvenčního směrování a směrování atributů.</span><span class="sxs-lookup"><span data-stu-id="da86d-468">ASP.NET Core apps can mix the use of conventional routing and attribute routing.</span></span> <span data-ttu-id="da86d-469">Pro řadiče, které obsluhují stránky HTML pro prohlížeče, a směrování atributů pro řadiče obsluhující rozhraní REST API se obvykle používá konvenční trasy.</span><span class="sxs-lookup"><span data-stu-id="da86d-469">It's typical to use conventional routes for controllers serving HTML pages for browsers, and attribute routing for controllers serving REST APIs.</span></span>

<span data-ttu-id="da86d-470">Akce jsou směrovány buď podle konvence nebo směrovaného atributu.</span><span class="sxs-lookup"><span data-stu-id="da86d-470">Actions are either conventionally routed or attribute routed.</span></span> <span data-ttu-id="da86d-471">Umístěním trasy do kontroleru nebo akce se nasměruje atribut IT.</span><span class="sxs-lookup"><span data-stu-id="da86d-471">Placing a route on the controller or the action makes it attribute routed.</span></span> <span data-ttu-id="da86d-472">Akce definující trasy atributů nemůžou být dostupné prostřednictvím konvenčních tras a naopak.</span><span class="sxs-lookup"><span data-stu-id="da86d-472">Actions that define attribute routes cannot be reached through the conventional routes and vice-versa.</span></span> <span data-ttu-id="da86d-473">**Libovolný** atribut směrování v řadiči provádí směrování **všech** akcí v atributu kontroleru.</span><span class="sxs-lookup"><span data-stu-id="da86d-473">**Any** route attribute on the controller makes **all** actions in the controller attribute routed.</span></span>

<span data-ttu-id="da86d-474">Směrování atributů a konvenční směrování používají stejný modul směrování.</span><span class="sxs-lookup"><span data-stu-id="da86d-474">Attribute routing and conventional routing use the same routing engine.</span></span>

<a name="routing-url-gen-ref-label"></a>
<a name="ambient"></a>

## <a name="url-generation-and-ambient-values"></a><span data-ttu-id="da86d-475">Generování adresy URL a okolní hodnoty</span><span class="sxs-lookup"><span data-stu-id="da86d-475">URL Generation and ambient values</span></span>

<span data-ttu-id="da86d-476">Aplikace můžou použít funkce generování adresy URL směrování k vygenerování odkazů na akce v adresách URL.</span><span class="sxs-lookup"><span data-stu-id="da86d-476">Apps can use routing URL generation features to generate URL links to actions.</span></span> <span data-ttu-id="da86d-477">Generování adres URL eliminuje adresy URL zakódujeme, takže je kód robustnější a udržovatelný.</span><span class="sxs-lookup"><span data-stu-id="da86d-477">Generating URLs eliminates hardcoding URLs, making code more robust and maintainable.</span></span> <span data-ttu-id="da86d-478">Tato část se zaměřuje na funkce generování adresy URL poskytované MVC a jenom základní informace o tom, jak generování adresy URL funguje.</span><span class="sxs-lookup"><span data-stu-id="da86d-478">This section focuses on the URL generation features provided by MVC and only cover basics of how URL generation works.</span></span> <span data-ttu-id="da86d-479">Podrobný popis generování adresy URL najdete v tématu věnovaném [Směrování](xref:fundamentals/routing) .</span><span class="sxs-lookup"><span data-stu-id="da86d-479">See [Routing](xref:fundamentals/routing) for a detailed description of URL generation.</span></span>

<span data-ttu-id="da86d-480"><xref:Microsoft.AspNetCore.Mvc.IUrlHelper>Rozhraní je základní prvek infrastruktury mezi MVC a směrováním pro generování adresy URL.</span><span class="sxs-lookup"><span data-stu-id="da86d-480">The <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> interface is the underlying element of infrastructure between MVC and routing for URL generation.</span></span> <span data-ttu-id="da86d-481">Instance `IUrlHelper` je k dispozici prostřednictvím `Url` vlastnosti v řadičích, zobrazeních a zobrazení komponent.</span><span class="sxs-lookup"><span data-stu-id="da86d-481">An instance of `IUrlHelper` is available through the `Url` property in controllers, views, and view components.</span></span>

<span data-ttu-id="da86d-482">V následujícím příkladu se `IUrlHelper` rozhraní používá přes `Controller.Url` vlastnost k VYGENEROVÁNÍ adresy URL jiné akci.</span><span class="sxs-lookup"><span data-stu-id="da86d-482">In the following example, the `IUrlHelper` interface is used through the `Controller.Url` property to generate a URL to another action.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGenerationController.cs?name=snippet_1)]

<span data-ttu-id="da86d-483">Pokud aplikace používá výchozí konvenční trasu, hodnota `url` proměnné je řetězec cesty adresy URL `/UrlGeneration/Destination` .</span><span class="sxs-lookup"><span data-stu-id="da86d-483">If the app is using the default conventional route, the value of the `url` variable is the URL path string `/UrlGeneration/Destination`.</span></span> <span data-ttu-id="da86d-484">Tato cesta URL se vytvoří směrováním kombinováním:</span><span class="sxs-lookup"><span data-stu-id="da86d-484">This URL path is created by routing by combining:</span></span>

* <span data-ttu-id="da86d-485">Hodnoty tras z aktuální žádosti, které se nazývají **okolní hodnoty**.</span><span class="sxs-lookup"><span data-stu-id="da86d-485">The route values from the current request, which are called **ambient values**.</span></span>
* <span data-ttu-id="da86d-486">Hodnoty předané do `Url.Action` a nahrazování těchto hodnot do šablony trasy:</span><span class="sxs-lookup"><span data-stu-id="da86d-486">The values passed to `Url.Action` and substituting those values into the route template:</span></span>

``` text
ambient values: { controller = "UrlGeneration", action = "Source" }
values passed to Url.Action: { controller = "UrlGeneration", action = "Destination" }
route template: {controller}/{action}/{id?}

result: /UrlGeneration/Destination
```

<span data-ttu-id="da86d-487">Každý parametr trasy v šabloně směrování má jeho hodnotu nahrazenou odpovídajícími názvy hodnotami a okolními hodnotami.</span><span class="sxs-lookup"><span data-stu-id="da86d-487">Each route parameter in the route template has its value substituted by matching names with the values and ambient values.</span></span> <span data-ttu-id="da86d-488">Parametr trasy, který nemá hodnotu, může:</span><span class="sxs-lookup"><span data-stu-id="da86d-488">A route parameter that doesn't have a value can:</span></span>

* <span data-ttu-id="da86d-489">Pokud má jednu z těchto hodnot, použijte výchozí hodnotu.</span><span class="sxs-lookup"><span data-stu-id="da86d-489">Use a default value if it has one.</span></span>
* <span data-ttu-id="da86d-490">Přeskočeno, pokud je volitelné.</span><span class="sxs-lookup"><span data-stu-id="da86d-490">Be skipped if it's optional.</span></span> <span data-ttu-id="da86d-491">Například `id` ze šablony trasy `{controller}/{action}/{id?}` .</span><span class="sxs-lookup"><span data-stu-id="da86d-491">For example, the `id` from the  route template `{controller}/{action}/{id?}`.</span></span>

<span data-ttu-id="da86d-492">Generování adresy URL se nepovede, pokud libovolný požadovaný parametr trasy nemá odpovídající hodnotu.</span><span class="sxs-lookup"><span data-stu-id="da86d-492">URL generation fails if any required route parameter doesn't have a corresponding value.</span></span> <span data-ttu-id="da86d-493">Pokud se generování adresy URL u trasy nezdařilo, bude se zkoušet další trasa, dokud nebudou všechny trasy vyzkoušeny nebo nalezena shoda.</span><span class="sxs-lookup"><span data-stu-id="da86d-493">If URL generation fails for a route, the next route is tried until all routes have been tried or a match is found.</span></span>

<span data-ttu-id="da86d-494">Předchozí příklad `Url.Action` předpokládá [konvenční směrování](#cr).</span><span class="sxs-lookup"><span data-stu-id="da86d-494">The preceding example of `Url.Action` assumes [conventional routing](#cr).</span></span> <span data-ttu-id="da86d-495">Generování adresy URL funguje podobně jako [Směrování atributů](#ar), i když se koncepty liší.</span><span class="sxs-lookup"><span data-stu-id="da86d-495">URL generation works similarly with [attribute routing](#ar), though the concepts are different.</span></span> <span data-ttu-id="da86d-496">S konvenčním směrováním:</span><span class="sxs-lookup"><span data-stu-id="da86d-496">With conventional routing:</span></span>

* <span data-ttu-id="da86d-497">Hodnoty tras slouží k rozbalení šablony.</span><span class="sxs-lookup"><span data-stu-id="da86d-497">The route values are used to expand a template.</span></span>
* <span data-ttu-id="da86d-498">Hodnoty směrování pro `controller` a `action` obvykle se zobrazí v této šabloně.</span><span class="sxs-lookup"><span data-stu-id="da86d-498">The route values for `controller` and `action` usually appear in that template.</span></span> <span data-ttu-id="da86d-499">To se hodí, protože adresy URL, které odpovídají směrování, dodržují konvenci.</span><span class="sxs-lookup"><span data-stu-id="da86d-499">This works because the URLs matched by routing adhere to a convention.</span></span>

<span data-ttu-id="da86d-500">Následující příklad používá směrování atributů:</span><span class="sxs-lookup"><span data-stu-id="da86d-500">The following example uses attribute routing:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGenerationAttrController.cs?name=snippet_1)]

<span data-ttu-id="da86d-501">`Source`Akce v předchozím kódu generuje `custom/url/to/destination` .</span><span class="sxs-lookup"><span data-stu-id="da86d-501">The `Source` action in the preceding code generates `custom/url/to/destination`.</span></span>

<span data-ttu-id="da86d-502"><xref:Microsoft.AspNetCore.Routing.LinkGenerator> byl přidán v ASP.NET Core 3,0 jako alternativa k `IUrlHelper` .</span><span class="sxs-lookup"><span data-stu-id="da86d-502"><xref:Microsoft.AspNetCore.Routing.LinkGenerator> was added in ASP.NET Core 3.0 as an alternative to `IUrlHelper`.</span></span> <span data-ttu-id="da86d-503">`LinkGenerator` nabízí podobné, ale flexibilnější funkce.</span><span class="sxs-lookup"><span data-stu-id="da86d-503">`LinkGenerator` offers similar but more flexible functionality.</span></span> <span data-ttu-id="da86d-504">Každá metoda on `IUrlHelper` má také odpovídající rodinu metod `LinkGenerator` .</span><span class="sxs-lookup"><span data-stu-id="da86d-504">Each method on `IUrlHelper` has a corresponding family of methods on `LinkGenerator` as well.</span></span>

### <a name="generating-urls-by-action-name"></a><span data-ttu-id="da86d-505">Generování adres URL podle názvu akce</span><span class="sxs-lookup"><span data-stu-id="da86d-505">Generating URLs by action name</span></span>

<span data-ttu-id="da86d-506">[Adresa URL. Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*), [LinkGenerator. GetPathByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetPathByAction*)a všechny související přetížení jsou navržené tak, aby vygenerovaly cílový koncový bod zadáním názvu kontroleru a názvu akce.</span><span class="sxs-lookup"><span data-stu-id="da86d-506">[Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*), [LinkGenerator.GetPathByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetPathByAction*), and all related overloads all are designed to generate the target endpoint by specifying a controller name and action name.</span></span>

<span data-ttu-id="da86d-507">Při použití nástroje `Url.Action` jsou aktuální hodnoty trasy pro `controller` a `action` poskytovány modulem runtime:</span><span class="sxs-lookup"><span data-stu-id="da86d-507">When using `Url.Action`, the current route values for `controller` and `action` are provided by the runtime:</span></span>

* <span data-ttu-id="da86d-508">Hodnota `controller` a `action` je součástí obou [okolních hodnot](#ambient) a hodnot.</span><span class="sxs-lookup"><span data-stu-id="da86d-508">The value of `controller` and `action` are part of both [ambient values](#ambient) and values.</span></span> <span data-ttu-id="da86d-509">Metoda `Url.Action` vždy používá aktuální hodnoty `action` a `controller` a generuje cestu adresy URL, která směruje na aktuální akci.</span><span class="sxs-lookup"><span data-stu-id="da86d-509">The method `Url.Action` always uses the current values of `action` and `controller` and generates a URL path that routes to the current action.</span></span>

<span data-ttu-id="da86d-510">Směrování se pokusí použít hodnoty v ambientních hodnotách k vyplnění informací, které nebyly poskytnuty při generování adresy URL.</span><span class="sxs-lookup"><span data-stu-id="da86d-510">Routing attempts to use the values in ambient values to fill in information that wasn't provided when generating a URL.</span></span> <span data-ttu-id="da86d-511">Vezměte v úvahu trasu jako `{a}/{b}/{c}/{d}` u okolních hodnot `{ a = Alice, b = Bob, c = Carol, d = David }` :</span><span class="sxs-lookup"><span data-stu-id="da86d-511">Consider a route like `{a}/{b}/{c}/{d}` with ambient values `{ a = Alice, b = Bob, c = Carol, d = David }`:</span></span>

* <span data-ttu-id="da86d-512">Směrování má dostatek informací pro vygenerování adresy URL bez dalších hodnot.</span><span class="sxs-lookup"><span data-stu-id="da86d-512">Routing has enough information to generate a URL without any additional values.</span></span>
* <span data-ttu-id="da86d-513">Směrování má dostatek informací, protože všechny parametry tras mají hodnotu.</span><span class="sxs-lookup"><span data-stu-id="da86d-513">Routing has enough information because all route parameters have a value.</span></span>

<span data-ttu-id="da86d-514">Pokud `{ d = Donovan }` se přidá hodnota:</span><span class="sxs-lookup"><span data-stu-id="da86d-514">If the value `{ d = Donovan }` is added:</span></span>

* <span data-ttu-id="da86d-515">Hodnota `{ d = David }` se ignoruje.</span><span class="sxs-lookup"><span data-stu-id="da86d-515">The value `{ d = David }` is ignored.</span></span>
* <span data-ttu-id="da86d-516">Vygenerovaná cesta URL je `Alice/Bob/Carol/Donovan` .</span><span class="sxs-lookup"><span data-stu-id="da86d-516">The generated URL path is `Alice/Bob/Carol/Donovan`.</span></span>

<span data-ttu-id="da86d-517">**Upozornění**: cesty URL jsou hierarchické.</span><span class="sxs-lookup"><span data-stu-id="da86d-517">**Warning**: URL paths are hierarchical.</span></span> <span data-ttu-id="da86d-518">V předchozím příkladu, pokud `{ c = Cheryl }` je přidána hodnota:</span><span class="sxs-lookup"><span data-stu-id="da86d-518">In the preceding example, if the value `{ c = Cheryl }` is added:</span></span>

* <span data-ttu-id="da86d-519">Obě hodnoty `{ c = Carol, d = David }` jsou ignorovány.</span><span class="sxs-lookup"><span data-stu-id="da86d-519">Both of the values `{ c = Carol, d = David }` are ignored.</span></span>
* <span data-ttu-id="da86d-520">Již není hodnota `d` a generování adresy URL se nezdařilo.</span><span class="sxs-lookup"><span data-stu-id="da86d-520">There is no longer a value for `d` and URL generation fails.</span></span>
* <span data-ttu-id="da86d-521">`c` `d` Aby bylo možné vygenerovat adresu URL, je nutné zadat požadované hodnoty a.</span><span class="sxs-lookup"><span data-stu-id="da86d-521">The desired values of `c` and `d` must be specified to generate a URL.</span></span>  

<span data-ttu-id="da86d-522">Můžete očekávat, že se tento problém bude narazit u výchozí trasy `{controller}/{action}/{id?}` .</span><span class="sxs-lookup"><span data-stu-id="da86d-522">You might expect to hit this problem with the default route `{controller}/{action}/{id?}`.</span></span> <span data-ttu-id="da86d-523">Tento problém je v praxi zřídka, protože `Url.Action` vždy explicitně určuje `controller` `action` hodnotu a.</span><span class="sxs-lookup"><span data-stu-id="da86d-523">This problem is rare in practice because `Url.Action` always explicitly specifies a `controller` and `action` value.</span></span>

<span data-ttu-id="da86d-524">Několik přetížení [URL. akce](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) přebírají objekt hodnot směrování, aby poskytovala hodnoty pro parametry směrování jiné než `controller` a `action` .</span><span class="sxs-lookup"><span data-stu-id="da86d-524">Several overloads of [Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) take a route values object to provide values for route parameters other than `controller` and `action`.</span></span> <span data-ttu-id="da86d-525">Objekt hodnoty trasy se často používá s `id` .</span><span class="sxs-lookup"><span data-stu-id="da86d-525">The route values object is frequently used with `id`.</span></span> <span data-ttu-id="da86d-526">Například, `Url.Action("Buy", "Products", new { id = 17 })`.</span><span class="sxs-lookup"><span data-stu-id="da86d-526">For example, `Url.Action("Buy", "Products", new { id = 17 })`.</span></span> <span data-ttu-id="da86d-527">Objekt hodnot směrování:</span><span class="sxs-lookup"><span data-stu-id="da86d-527">The route values object:</span></span>

* <span data-ttu-id="da86d-528">Podle konvence je obvykle objekt anonymního typu.</span><span class="sxs-lookup"><span data-stu-id="da86d-528">By convention is usually an object of anonymous type.</span></span>
* <span data-ttu-id="da86d-529">Může to být `IDictionary<>` nebo [POCO](https://wikipedia.org/wiki/Plain_old_CLR_object)).</span><span class="sxs-lookup"><span data-stu-id="da86d-529">Can be an `IDictionary<>` or a [POCO](https://wikipedia.org/wiki/Plain_old_CLR_object)).</span></span>

<span data-ttu-id="da86d-530">Všechny další hodnoty tras, které neodpovídají parametrům směrování, jsou vloženy do řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="da86d-530">Any additional route values that don't match route parameters are put in the query string.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/TestController.cs?name=snippet)]

<span data-ttu-id="da86d-531">Předchozí kód generuje `/Products/Buy/17?color=red` .</span><span class="sxs-lookup"><span data-stu-id="da86d-531">The preceding code generates `/Products/Buy/17?color=red`.</span></span>

<span data-ttu-id="da86d-532">Následující kód vygeneruje absolutní adresu URL:</span><span class="sxs-lookup"><span data-stu-id="da86d-532">The following code generates an absolute URL:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/TestController.cs?name=snippet2)]

<span data-ttu-id="da86d-533">Chcete-li vytvořit absolutní adresu URL, použijte jednu z následujících možností:</span><span class="sxs-lookup"><span data-stu-id="da86d-533">To create an absolute URL, use one of the following:</span></span>

* <span data-ttu-id="da86d-534">Přetížení, které přijímá `protocol` .</span><span class="sxs-lookup"><span data-stu-id="da86d-534">An overload that accepts a `protocol`.</span></span> <span data-ttu-id="da86d-535">Například předchozí kód.</span><span class="sxs-lookup"><span data-stu-id="da86d-535">For example, the preceding code.</span></span>
* <span data-ttu-id="da86d-536">[LinkGenerator. GetUriByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetUriByAction*), který ve výchozím nastavení generuje absolutní identifikátory URI.</span><span class="sxs-lookup"><span data-stu-id="da86d-536">[LinkGenerator.GetUriByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetUriByAction*), which generates absolute URIs by default.</span></span>

<a name="routing-gen-urls-route-ref-label"></a>

### <a name="generate-urls-by-route"></a><span data-ttu-id="da86d-537">Generovat adresy URL podle směrování</span><span class="sxs-lookup"><span data-stu-id="da86d-537">Generate URLs by route</span></span>

<span data-ttu-id="da86d-538">Předchozí kód ukázal generování adresy URL předáním do kontroleru a názvu akce.</span><span class="sxs-lookup"><span data-stu-id="da86d-538">The preceding code demonstrated generating a URL by passing in the controller and action name.</span></span> <span data-ttu-id="da86d-539">`IUrlHelper` také poskytuje [adresu URL. RouteUrl](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.RouteUrl*) řady metod.</span><span class="sxs-lookup"><span data-stu-id="da86d-539">`IUrlHelper` also provides the [Url.RouteUrl](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.RouteUrl*) family of methods.</span></span> <span data-ttu-id="da86d-540">Tyto metody jsou podobné jako [URL. Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*), ale nekopírují aktuální hodnoty `action` a `controller` do hodnot tras.</span><span class="sxs-lookup"><span data-stu-id="da86d-540">These methods are similar to [Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*), but they don't copy the current values of `action` and `controller` to the route values.</span></span> <span data-ttu-id="da86d-541">Nejběžnější využití `Url.RouteUrl` :</span><span class="sxs-lookup"><span data-stu-id="da86d-541">The most common usage of `Url.RouteUrl`:</span></span>

* <span data-ttu-id="da86d-542">Určuje název trasy pro vygenerování adresy URL.</span><span class="sxs-lookup"><span data-stu-id="da86d-542">Specifies a route name to generate the URL.</span></span>
* <span data-ttu-id="da86d-543">Obecně neurčuje kontrolér nebo název akce.</span><span class="sxs-lookup"><span data-stu-id="da86d-543">Generally doesn't specify a controller or action name.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGeneration2Controller.cs?name=snippet_1)]

<span data-ttu-id="da86d-544">Následující Razor soubor generuje odkaz HTML na `Destination_Route` :</span><span class="sxs-lookup"><span data-stu-id="da86d-544">The following Razor file generates an HTML link to the `Destination_Route`:</span></span>

[!code-cshtml[](routing/samples/3.x/main/Views/Shared/MyLink.cshtml)]

<a name="routing-gen-urls-html-ref-label"></a>

### <a name="generate-urls-in-html-and-no-locrazor"></a><span data-ttu-id="da86d-545">Generování adres URL ve formátu HTML a Razor</span><span class="sxs-lookup"><span data-stu-id="da86d-545">Generate URLs in HTML and Razor</span></span>

<span data-ttu-id="da86d-546"><xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper> poskytuje <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.HtmlHelper> metody [HTML. BeginForm](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.BeginForm*) a [HTML. ActionLink](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.ActionLink*) ke generování `<form>` a `<a>` prvkům v tomto pořadí.</span><span class="sxs-lookup"><span data-stu-id="da86d-546"><xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper> provides the <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.HtmlHelper> methods [Html.BeginForm](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.BeginForm*) and [Html.ActionLink](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.ActionLink*) to generate `<form>` and `<a>` elements respectively.</span></span> <span data-ttu-id="da86d-547">Tyto metody používají metodu [URL. Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) pro VYGENEROVÁNÍ adresy URL a přijímající podobné argumenty.</span><span class="sxs-lookup"><span data-stu-id="da86d-547">These methods use the [Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) method to generate a URL and they accept similar arguments.</span></span> <span data-ttu-id="da86d-548">`Url.RouteUrl`Doprovodníci pro `HtmlHelper` jsou `Html.BeginRouteForm` a `Html.RouteLink` mají podobné funkce.</span><span class="sxs-lookup"><span data-stu-id="da86d-548">The `Url.RouteUrl` companions for `HtmlHelper` are `Html.BeginRouteForm` and `Html.RouteLink` which have similar functionality.</span></span>

<span data-ttu-id="da86d-549">TagHelpers generuje adresy URL prostřednictvím `form` taghelperu a `<a>` taghelperu.</span><span class="sxs-lookup"><span data-stu-id="da86d-549">TagHelpers generate URLs through the `form` TagHelper and the `<a>` TagHelper.</span></span> <span data-ttu-id="da86d-550">Oba tyto použití `IUrlHelper` při jejich implementaci.</span><span class="sxs-lookup"><span data-stu-id="da86d-550">Both of these use `IUrlHelper` for their implementation.</span></span> <span data-ttu-id="da86d-551">Další informace najdete v tématu věnovaném [pomocníkům značek ve formulářích](xref:mvc/views/working-with-forms) .</span><span class="sxs-lookup"><span data-stu-id="da86d-551">See [Tag Helpers in forms](xref:mvc/views/working-with-forms) for more information.</span></span>

<span data-ttu-id="da86d-552">Uvnitř zobrazení `IUrlHelper` je k dispozici prostřednictvím `Url` vlastnosti pro jakoukoli generaci adres URL ad hoc, která není pokrytá výše.</span><span class="sxs-lookup"><span data-stu-id="da86d-552">Inside views, the `IUrlHelper` is available through the `Url` property for any ad-hoc URL generation not covered by the above.</span></span>

<a name="routing-gen-urls-action-ref-label"></a>

### <a name="url-generation-in-action-results"></a><span data-ttu-id="da86d-553">Generování adresy URL ve výsledcích akce</span><span class="sxs-lookup"><span data-stu-id="da86d-553">URL generation in Action Results</span></span>

<span data-ttu-id="da86d-554">Předchozí příklady ukázaly použití `IUrlHelper` v kontroleru.</span><span class="sxs-lookup"><span data-stu-id="da86d-554">The preceding examples showed using `IUrlHelper` in a controller.</span></span> <span data-ttu-id="da86d-555">Nejběžnějším využitím v kontroleru je vygenerování adresy URL jako součást výsledku akce.</span><span class="sxs-lookup"><span data-stu-id="da86d-555">The most common usage in a controller is to generate a URL as part of an action result.</span></span>

<span data-ttu-id="da86d-556"><xref:Microsoft.AspNetCore.Mvc.ControllerBase> <xref:Microsoft.AspNetCore.Mvc.Controller> Základní třídy a poskytují pohodlný způsob pro výsledky akcí, které odkazují na jinou akci.</span><span class="sxs-lookup"><span data-stu-id="da86d-556">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase> and <xref:Microsoft.AspNetCore.Mvc.Controller> base classes provide convenience methods for action results that reference another action.</span></span> <span data-ttu-id="da86d-557">Jedním z typických použití je přesměrování po přijetí vstupu uživatele:</span><span class="sxs-lookup"><span data-stu-id="da86d-557">One typical usage is to redirect after accepting user input:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/CustomerController.cs?name=snippet)]

<span data-ttu-id="da86d-558">Akce pro metody vytváření výsledků, jako je například <xref:Microsoft.AspNetCore.Mvc.ControllerBase.RedirectToAction%2A> a <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction%2A> následují podobně jako metody v `IUrlHelper` .</span><span class="sxs-lookup"><span data-stu-id="da86d-558">The action results factory methods such as <xref:Microsoft.AspNetCore.Mvc.ControllerBase.RedirectToAction%2A> and <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction%2A> follow a similar pattern to the methods on `IUrlHelper`.</span></span>

<a name="routing-dedicated-ref-label"></a>

### <a name="special-case-for-dedicated-conventional-routes"></a><span data-ttu-id="da86d-559">Zvláštní případ pro vyhrazené konvenční trasy</span><span class="sxs-lookup"><span data-stu-id="da86d-559">Special case for dedicated conventional routes</span></span>

<span data-ttu-id="da86d-560">[Konvenční směrování](#cr) může používat speciální druh definice trasy, která se označuje jako [vyhrazená konvenční trasa](#dcr).</span><span class="sxs-lookup"><span data-stu-id="da86d-560">[Conventional routing](#cr) can use a special kind of route definition called a [dedicated conventional route](#dcr).</span></span> <span data-ttu-id="da86d-561">V následujícím příkladu je trasa s názvem `blog` vyhrazená konvenční trasa:</span><span class="sxs-lookup"><span data-stu-id="da86d-561">In the following example, the route named `blog` is a dedicated conventional route:</span></span>

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

<span data-ttu-id="da86d-562">Když použijete předchozí definice tras, `Url.Action("Index", "Home")` vygeneruje cestu URL `/` pomocí `default` trasy, ale proč?</span><span class="sxs-lookup"><span data-stu-id="da86d-562">Using the preceding route definitions, `Url.Action("Index", "Home")` generates the URL path `/` using the `default` route, but why?</span></span> <span data-ttu-id="da86d-563">Můžete odhadnout, že hodnoty tras `{ controller = Home, action = Index }` by byly dostatečné pro vygenerování adresy URL pomocí `blog` a výsledek by byl `/blog?action=Index&controller=Home` .</span><span class="sxs-lookup"><span data-stu-id="da86d-563">You might guess the route values `{ controller = Home, action = Index }` would be enough to generate a URL using `blog`, and the result would be `/blog?action=Index&controller=Home`.</span></span>

<span data-ttu-id="da86d-564">[Vyhrazené konvenční trasy](#dcr) spoléhají na speciální chování výchozích hodnot, které nemají odpovídající parametr trasy, který brání tomu, aby byla trasa příliš [hladkou](xref:fundamentals/routing#greedy) při generování adresy URL.</span><span class="sxs-lookup"><span data-stu-id="da86d-564">[Dedicated conventional routes](#dcr) rely on a special behavior of default values that don't have a corresponding route parameter that prevents the route from being too [greedy](xref:fundamentals/routing#greedy) with URL generation.</span></span> <span data-ttu-id="da86d-565">V tomto případě výchozí hodnoty jsou `{ controller = Blog, action = Article }` a ani `controller` `action` se neobjeví jako parametr trasy.</span><span class="sxs-lookup"><span data-stu-id="da86d-565">In this case the default values are `{ controller = Blog, action = Article }`, and neither `controller` nor `action` appears as a route parameter.</span></span> <span data-ttu-id="da86d-566">Když směrování provádí generování adresy URL, zadané hodnoty musí odpovídat výchozím hodnotám.</span><span class="sxs-lookup"><span data-stu-id="da86d-566">When routing performs URL generation, the values provided must match the default values.</span></span> <span data-ttu-id="da86d-567">Generování adresy URL pomocí `blog` neproběhne úspěšně, protože hodnoty se `{ controller = Home, action = Index }` neshodují `{ controller = Blog, action = Article }` .</span><span class="sxs-lookup"><span data-stu-id="da86d-567">URL generation using `blog` fails because the values `{ controller = Home, action = Index }` don't match `{ controller = Blog, action = Article }`.</span></span> <span data-ttu-id="da86d-568">Směrování pak vrátí zpět k akci `default` , která bude úspěšná.</span><span class="sxs-lookup"><span data-stu-id="da86d-568">Routing then falls back to try `default`, which succeeds.</span></span>

<a name="routing-areas-ref-label"></a>

## <a name="areas"></a><span data-ttu-id="da86d-569">Oblasti</span><span class="sxs-lookup"><span data-stu-id="da86d-569">Areas</span></span>

<span data-ttu-id="da86d-570">[Oblasti](xref:mvc/controllers/areas) představují funkci MVC, která slouží k uspořádání souvisejících funkcí do skupiny jako samostatného:</span><span class="sxs-lookup"><span data-stu-id="da86d-570">[Areas](xref:mvc/controllers/areas) are an MVC feature used to organize related functionality into a group as a separate:</span></span>

* <span data-ttu-id="da86d-571">Obor názvů směrování pro akce kontroleru.</span><span class="sxs-lookup"><span data-stu-id="da86d-571">Routing namespace for controller actions.</span></span>
* <span data-ttu-id="da86d-572">Struktura složek pro zobrazení.</span><span class="sxs-lookup"><span data-stu-id="da86d-572">Folder structure for views.</span></span>

<span data-ttu-id="da86d-573">Použití oblastí umožňuje aplikaci mít více řadičů se stejným názvem, pokud mají různé oblasti.</span><span class="sxs-lookup"><span data-stu-id="da86d-573">Using areas allows an app to have multiple controllers with the same name, as long as they have different areas.</span></span> <span data-ttu-id="da86d-574">Použití oblastí vytvoří hierarchii pro účely směrování přidáním dalšího parametru směrování `area` do `controller` a `action` .</span><span class="sxs-lookup"><span data-stu-id="da86d-574">Using areas creates a hierarchy for the purpose of routing by adding another route parameter, `area` to `controller` and `action`.</span></span> <span data-ttu-id="da86d-575">Tato část popisuje, jak směrování komunikuje s oblastmi.</span><span class="sxs-lookup"><span data-stu-id="da86d-575">This section discusses how routing interacts with areas.</span></span> <span data-ttu-id="da86d-576">Podrobnosti o tom, jak se používají oblasti se zobrazeními, najdete v části [oblasti](xref:mvc/controllers/areas) .</span><span class="sxs-lookup"><span data-stu-id="da86d-576">See [Areas](xref:mvc/controllers/areas) for details about how areas are used with views.</span></span>

<span data-ttu-id="da86d-577">Následující příklad nakonfiguruje MVC tak, aby používala výchozí konvenční trasu a `area` trasu pro `area` název `Blog` :</span><span class="sxs-lookup"><span data-stu-id="da86d-577">The following example configures MVC to use the default conventional route and an `area` route for an `area` named `Blog`:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet1)]

<span data-ttu-id="da86d-578">V předchozím kódu <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute%2A> je volána k vytvoření `"blog_route"` .</span><span class="sxs-lookup"><span data-stu-id="da86d-578">In the preceding code, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute%2A> is called to create the `"blog_route"`.</span></span> <span data-ttu-id="da86d-579">Druhým parametrem `"Blog"` je název oblasti.</span><span class="sxs-lookup"><span data-stu-id="da86d-579">The second parameter, `"Blog"`, is the area name.</span></span>

<span data-ttu-id="da86d-580">Při porovnání cesty URL jako `/Manage/Users/AddUser` se v `"blog_route"` trase vygeneruje hodnoty trasy `{ area = Blog, controller = Users, action = AddUser }` .</span><span class="sxs-lookup"><span data-stu-id="da86d-580">When matching a URL path like `/Manage/Users/AddUser`, the `"blog_route"` route generates the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span> <span data-ttu-id="da86d-581">`area`Hodnota trasy je vytvořena výchozí hodnotou pro `area` .</span><span class="sxs-lookup"><span data-stu-id="da86d-581">The `area` route value is produced by a default value for `area`.</span></span> <span data-ttu-id="da86d-582">Trasa, kterou vytvořil, `MapAreaControllerRoute` je ekvivalentní následujícímu:</span><span class="sxs-lookup"><span data-stu-id="da86d-582">The route created by `MapAreaControllerRoute` is equivalent to the following:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup2.cs?name=snippet2)]

<span data-ttu-id="da86d-583">`MapAreaControllerRoute` Vytvoří trasu s použitím výchozí hodnoty a omezení pro `area` použití poskytnutého názvu oblasti v tomto případě `Blog` .</span><span class="sxs-lookup"><span data-stu-id="da86d-583">`MapAreaControllerRoute` creates a route using both a default value and constraint for `area` using the provided area name, in this case `Blog`.</span></span> <span data-ttu-id="da86d-584">Výchozí hodnota zajistí, že trasa vždy vytvoří `{ area = Blog, ... }` , omezení vyžaduje hodnotu `{ area = Blog, ... }` pro generování adresy URL.</span><span class="sxs-lookup"><span data-stu-id="da86d-584">The default value ensures that the route always produces `{ area = Blog, ... }`, the constraint requires the value `{ area = Blog, ... }` for URL generation.</span></span>

<span data-ttu-id="da86d-585">Konvenční směrování je závislé na pořadí.</span><span class="sxs-lookup"><span data-stu-id="da86d-585">Conventional routing is order-dependent.</span></span> <span data-ttu-id="da86d-586">Obecně platí, že trasy s oblastmi by měly být umístěny dříve, protože jsou konkrétnější než trasy bez oblasti.</span><span class="sxs-lookup"><span data-stu-id="da86d-586">In general, routes with areas should be placed earlier as they're more specific than routes without an area.</span></span>

<span data-ttu-id="da86d-587">Pomocí předchozího příkladu se hodnoty tras `{ area = Blog, controller = Users, action = AddUser }` shodují s touto akcí:</span><span class="sxs-lookup"><span data-stu-id="da86d-587">Using the preceding example, the route values `{ area = Blog, controller = Users, action = AddUser }` match the following action:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

<span data-ttu-id="da86d-588">Atribut [[Area]](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) je tím, že označuje kontroler jako součást oblasti.</span><span class="sxs-lookup"><span data-stu-id="da86d-588">The [[Area]](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) attribute is what denotes a controller as part of an area.</span></span> <span data-ttu-id="da86d-589">Tento kontroler je v `Blog` oblasti.</span><span class="sxs-lookup"><span data-stu-id="da86d-589">This controller is in the `Blog` area.</span></span> <span data-ttu-id="da86d-590">Řadiče bez `[Area]` atributu nejsou členy žádné oblasti a neodpovídají, pokud **not** `area` je hodnota směrování poskytována směrováním.</span><span class="sxs-lookup"><span data-stu-id="da86d-590">Controllers without an `[Area]` attribute are not members of any area, and do **not** match when the `area` route value is provided by routing.</span></span> <span data-ttu-id="da86d-591">V následujícím příkladu se může shodovat jenom první kontroler, který bude odpovídat hodnotám tras `{ area = Blog, controller = Users, action = AddUser }` .</span><span class="sxs-lookup"><span data-stu-id="da86d-591">In the following example, only the first controller listed can match the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Zebra/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/UsersController.cs)]

<span data-ttu-id="da86d-592">Pro úplnost se zobrazí obor názvů každého kontroleru.</span><span class="sxs-lookup"><span data-stu-id="da86d-592">The namespace of each controller is shown here for completeness.</span></span> <span data-ttu-id="da86d-593">Pokud předchozí řadiče používají stejný obor názvů, vygeneruje se chyba kompilátoru.</span><span class="sxs-lookup"><span data-stu-id="da86d-593">If the preceding controllers uses the same namespace, a compiler error would be generated.</span></span> <span data-ttu-id="da86d-594">Obory názvů třídy nemají žádný vliv na směrování MVC.</span><span class="sxs-lookup"><span data-stu-id="da86d-594">Class namespaces have no effect on MVC's routing.</span></span>

<span data-ttu-id="da86d-595">První dva řadiče jsou členy oblastí a odpovídají pouze v případě, že je jejich název příslušné oblasti poskytovaný `area` hodnotou trasy.</span><span class="sxs-lookup"><span data-stu-id="da86d-595">The first two controllers are members of areas, and only match when their respective area name is provided by the `area` route value.</span></span> <span data-ttu-id="da86d-596">Třetí kontroler není členem žádné oblasti a může odpovídat jenom v případě, že `area` směrováním neposkytuje žádnou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="da86d-596">The third controller isn't a member of any area, and can only match when no value for `area` is provided by routing.</span></span>

<a name="aa"></a>

<span data-ttu-id="da86d-597">V souvislosti s neshodnou *hodnotou* `area` je absence hodnoty stejná, jako kdyby hodnota `area` null nebo prázdný řetězec.</span><span class="sxs-lookup"><span data-stu-id="da86d-597">In terms of matching *no value*, the absence of the `area` value is the same as if the value for `area` were null or the empty string.</span></span>

<span data-ttu-id="da86d-598">Při provádění akce uvnitř oblasti je hodnota trasy `area` dostupná jako [ambientní hodnota](#ambient) pro směrování, která se má použít pro generování adresy URL.</span><span class="sxs-lookup"><span data-stu-id="da86d-598">When executing an action inside an area, the route value for `area` is available as an [ambient value](#ambient) for routing to use for URL generation.</span></span> <span data-ttu-id="da86d-599">To znamená, že ve výchozím nastavení jsou tyto oblasti pro generování adresy URL *rychlé* , jak je znázorněno v následující ukázce.</span><span class="sxs-lookup"><span data-stu-id="da86d-599">This means that by default areas act *sticky* for URL generation as demonstrated by the following sample.</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup3.cs?name=snippet3)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Duck/Controllers/UsersController.cs)]

<span data-ttu-id="da86d-600">Následující kód vygeneruje adresu URL pro `/Zebra/Users/AddUser` :</span><span class="sxs-lookup"><span data-stu-id="da86d-600">The following code generates a URL to `/Zebra/Users/AddUser`:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/HomeController.cs?name=snippet)]

<a name="action"></a>

## <a name="action-definition"></a><span data-ttu-id="da86d-601">Definice akce</span><span class="sxs-lookup"><span data-stu-id="da86d-601">Action definition</span></span>

<span data-ttu-id="da86d-602">Veřejné metody na řadiči, s výjimkou atributu [neaction](xref:Microsoft.AspNetCore.Mvc.NonActionAttribute) , jsou akce.</span><span class="sxs-lookup"><span data-stu-id="da86d-602">Public methods on a controller, except those with the [NonAction](xref:Microsoft.AspNetCore.Mvc.NonActionAttribute) attribute, are actions.</span></span>

## <a name="sample-code"></a><span data-ttu-id="da86d-603">Ukázka kódu</span><span class="sxs-lookup"><span data-stu-id="da86d-603">Sample code</span></span>

 * <span data-ttu-id="da86d-604">Metoda [MyDisplayRouteInfo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x/main/Extensions/ControllerContextExtensions.cs) je obsažena v [ukázce stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) a slouží k zobrazení informací o směrování.</span><span class="sxs-lookup"><span data-stu-id="da86d-604">The [MyDisplayRouteInfo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x/main/Extensions/ControllerContextExtensions.cs) method is included in the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) and is used to display routing information.</span></span>
* <span data-ttu-id="da86d-605">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="da86d-605">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) ([how to download](xref:index#how-to-download-a-sample))</span></span>

[!INCLUDE[](~/includes/dbg-route.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="da86d-606">ASP.NET Core MVC používá [middleware](xref:fundamentals/middleware/index) směrování ke spárování adres URL příchozích požadavků a jejich mapování na akce.</span><span class="sxs-lookup"><span data-stu-id="da86d-606">ASP.NET Core MVC uses the Routing [middleware](xref:fundamentals/middleware/index) to match the URLs of incoming requests and map them to actions.</span></span> <span data-ttu-id="da86d-607">Trasy jsou definovány ve spouštěcím kódu nebo v atributech.</span><span class="sxs-lookup"><span data-stu-id="da86d-607">Routes are defined in startup code or attributes.</span></span> <span data-ttu-id="da86d-608">Trasy popisují, jak by měly být cesty URL odpovídat akcím.</span><span class="sxs-lookup"><span data-stu-id="da86d-608">Routes describe how URL paths should be matched to actions.</span></span> <span data-ttu-id="da86d-609">Trasy se také používají ke generování adres URL (pro odkazy) odesílaných v odpovědích.</span><span class="sxs-lookup"><span data-stu-id="da86d-609">Routes are also used to generate URLs (for links) sent out in responses.</span></span>

<span data-ttu-id="da86d-610">Akce jsou směrovány buď podle konvence nebo směrovaného atributu.</span><span class="sxs-lookup"><span data-stu-id="da86d-610">Actions are either conventionally routed or attribute routed.</span></span> <span data-ttu-id="da86d-611">Umístěním trasy do kontroleru nebo akce se nasměruje atribut IT.</span><span class="sxs-lookup"><span data-stu-id="da86d-611">Placing a route on the controller or the action makes it attribute routed.</span></span> <span data-ttu-id="da86d-612">Další informace najdete v tématu [smíšená směrování](#routing-mixed-ref-label) .</span><span class="sxs-lookup"><span data-stu-id="da86d-612">See [Mixed routing](#routing-mixed-ref-label) for more information.</span></span>

<span data-ttu-id="da86d-613">Tento dokument vysvětluje interakce mezi MVC a směrováními a jak běžné aplikace MVC využívají funkce směrování.</span><span class="sxs-lookup"><span data-stu-id="da86d-613">This document will explain the interactions between MVC and routing, and how typical MVC apps make use of routing features.</span></span> <span data-ttu-id="da86d-614">Podrobnosti o pokročilém směrování najdete v tématu [Směrování](xref:fundamentals/routing) .</span><span class="sxs-lookup"><span data-stu-id="da86d-614">See [Routing](xref:fundamentals/routing) for details on advanced routing.</span></span>

## <a name="setting-up-routing-middleware"></a><span data-ttu-id="da86d-615">Nastavení middlewaru směrování</span><span class="sxs-lookup"><span data-stu-id="da86d-615">Setting up Routing Middleware</span></span>

<span data-ttu-id="da86d-616">V metodě *Konfigurace* se může zobrazit kód podobný tomuto:</span><span class="sxs-lookup"><span data-stu-id="da86d-616">In your *Configure* method you may see code similar to:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="da86d-617">Uvnitř volání metody `UseMvc` `MapRoute` se používá k vytvoření jedné trasy, na kterou odkazujeme jako na `default` trase.</span><span class="sxs-lookup"><span data-stu-id="da86d-617">Inside the call to `UseMvc`, `MapRoute` is used to create a single route, which we'll refer to as the `default` route.</span></span> <span data-ttu-id="da86d-618">Většina aplikací MVC bude používat trasu se šablonou podobnou této `default` trase.</span><span class="sxs-lookup"><span data-stu-id="da86d-618">Most MVC apps will use a route with a template similar to the `default` route.</span></span>

<span data-ttu-id="da86d-619">Šablona trasy se `"{controller=Home}/{action=Index}/{id?}"` může shodovat s cestou adresy URL `/Products/Details/5` , jako je, a extrahuje hodnoty tras tím, že `{ controller = Products, action = Details, id = 5 }` tokenizací cestu.</span><span class="sxs-lookup"><span data-stu-id="da86d-619">The route template `"{controller=Home}/{action=Index}/{id?}"` can match a URL path like `/Products/Details/5` and will extract the route values `{ controller = Products, action = Details, id = 5 }` by tokenizing the path.</span></span> <span data-ttu-id="da86d-620">MVC se pokusí vyhledat kontroler s názvem `ProductsController` a spustit akci `Details` :</span><span class="sxs-lookup"><span data-stu-id="da86d-620">MVC will attempt to locate a controller named `ProductsController` and run the action `Details`:</span></span>

```csharp
public class ProductsController : Controller
{
   public IActionResult Details(int id) { ... }
}
```

<span data-ttu-id="da86d-621">Všimněte si, že v tomto příkladu vazba modelu použije hodnotu `id = 5` pro nastavení `id` parametru `5` při vyvolání této akce.</span><span class="sxs-lookup"><span data-stu-id="da86d-621">Note that in this example, model binding would use the value of `id = 5` to set the `id` parameter to `5` when invoking this action.</span></span> <span data-ttu-id="da86d-622">Další podrobnosti najdete v tématu [vazba modelu](../models/model-binding.md) .</span><span class="sxs-lookup"><span data-stu-id="da86d-622">See the [Model Binding](../models/model-binding.md) for more details.</span></span>

<span data-ttu-id="da86d-623">Pomocí `default` trasy:</span><span class="sxs-lookup"><span data-stu-id="da86d-623">Using the `default` route:</span></span>

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="da86d-624">Šablona trasy:</span><span class="sxs-lookup"><span data-stu-id="da86d-624">The route template:</span></span>

* <span data-ttu-id="da86d-625">`{controller=Home}` definuje `Home` jako výchozí. `controller`</span><span class="sxs-lookup"><span data-stu-id="da86d-625">`{controller=Home}` defines `Home` as the default `controller`</span></span>

* <span data-ttu-id="da86d-626">`{action=Index}` definuje `Index` jako výchozí. `action`</span><span class="sxs-lookup"><span data-stu-id="da86d-626">`{action=Index}` defines `Index` as the default `action`</span></span>

* <span data-ttu-id="da86d-627">`{id?}` definuje `id` jako volitelné.</span><span class="sxs-lookup"><span data-stu-id="da86d-627">`{id?}` defines `id` as optional</span></span>

<span data-ttu-id="da86d-628">Výchozí a volitelné parametry směrování nemusejí být v cestě URL pro porovnávání k dispozici.</span><span class="sxs-lookup"><span data-stu-id="da86d-628">Default and optional route parameters don't need to be present in the URL path for a match.</span></span> <span data-ttu-id="da86d-629">Podrobný popis syntaxe šablony směrování naleznete v tématu Referenční dokumentace k [šabloně směrování](xref:fundamentals/routing#route-template-reference) .</span><span class="sxs-lookup"><span data-stu-id="da86d-629">See [Route Template Reference](xref:fundamentals/routing#route-template-reference) for a detailed description of route template syntax.</span></span>

<span data-ttu-id="da86d-630">`"{controller=Home}/{action=Index}/{id?}"` může odpovídat cestě URL `/` a vytvoří hodnoty tras `{ controller = Home, action = Index }` .</span><span class="sxs-lookup"><span data-stu-id="da86d-630">`"{controller=Home}/{action=Index}/{id?}"` can match the URL path `/` and will produce the route values `{ controller = Home, action = Index }`.</span></span> <span data-ttu-id="da86d-631">Hodnoty pro `controller` a `action` využívají výchozí hodnoty `id` nevytvářejí hodnotu, protože v cestě URL není žádný odpovídající segment.</span><span class="sxs-lookup"><span data-stu-id="da86d-631">The values for `controller` and `action` make use of the default values, `id` doesn't produce a value since there's no corresponding segment in the URL path.</span></span> <span data-ttu-id="da86d-632">MVC by tyto hodnoty trasy použily k výběru `HomeController` `Index` akce a:</span><span class="sxs-lookup"><span data-stu-id="da86d-632">MVC would use these route values to select the `HomeController` and `Index` action:</span></span>

```csharp
public class HomeController : Controller
{
  public IActionResult Index() { ... }
}
```

<span data-ttu-id="da86d-633">Pomocí této definice kontroleru a šablony trasy se `HomeController.Index` akce spustí pro libovolnou z následujících cest URL:</span><span class="sxs-lookup"><span data-stu-id="da86d-633">Using this controller definition and route template, the `HomeController.Index` action would be executed for any of the following URL paths:</span></span>

* `/Home/Index/17`

* `/Home/Index`

* `/Home`

* `/`

<span data-ttu-id="da86d-634">Způsob usnadnění `UseMvcWithDefaultRoute` :</span><span class="sxs-lookup"><span data-stu-id="da86d-634">The convenience method `UseMvcWithDefaultRoute`:</span></span>

```csharp
app.UseMvcWithDefaultRoute();
```

<span data-ttu-id="da86d-635">Dá se použít k nahrazení:</span><span class="sxs-lookup"><span data-stu-id="da86d-635">Can be used to replace:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="da86d-636">`UseMvc` a `UseMvcWithDefaultRoute` přidejte instanci `RouterMiddleware` do kanálu middlewaru.</span><span class="sxs-lookup"><span data-stu-id="da86d-636">`UseMvc` and `UseMvcWithDefaultRoute` add an instance of `RouterMiddleware` to the middleware pipeline.</span></span> <span data-ttu-id="da86d-637">MVC nekomunikuje přímo se middlewarem a používá směrování pro zpracování požadavků.</span><span class="sxs-lookup"><span data-stu-id="da86d-637">MVC doesn't interact directly with middleware, and uses routing to handle requests.</span></span> <span data-ttu-id="da86d-638">MVC je připojeno k trasám prostřednictvím instance `MvcRouteHandler` .</span><span class="sxs-lookup"><span data-stu-id="da86d-638">MVC is connected to the routes through an instance of `MvcRouteHandler`.</span></span> <span data-ttu-id="da86d-639">Kód uvnitř `UseMvc` je podobný následujícímu:</span><span class="sxs-lookup"><span data-stu-id="da86d-639">The code inside of `UseMvc` is similar to the following:</span></span>

```csharp
var routes = new RouteBuilder(app);

// Add connection to MVC, will be hooked up by calls to MapRoute.
routes.DefaultHandler = new MvcRouteHandler(...);

// Execute callback to register routes.
// routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");

// Create route collection and add the middleware.
app.UseRouter(routes.Build());
```

<span data-ttu-id="da86d-640">`UseMvc` přímo nedefinuje žádné trasy, přidá zástupný symbol do kolekce směrování pro `attribute` trasu.</span><span class="sxs-lookup"><span data-stu-id="da86d-640">`UseMvc` doesn't directly define any routes, it adds a placeholder to the route collection for the `attribute` route.</span></span> <span data-ttu-id="da86d-641">Přetížení `UseMvc(Action<IRouteBuilder>)` umožňuje přidat vlastní trasy a zároveň podporuje směrování atributů.</span><span class="sxs-lookup"><span data-stu-id="da86d-641">The overload `UseMvc(Action<IRouteBuilder>)` lets you add your own routes and also supports attribute routing.</span></span>  <span data-ttu-id="da86d-642">`UseMvc` a všechny jeho variace přidávají zástupný symbol pro atribut směrování atributu směrování je vždycky dostupný bez ohledu na to, jak nakonfigurujete `UseMvc` .</span><span class="sxs-lookup"><span data-stu-id="da86d-642">`UseMvc` and all of its variations add a placeholder for the attribute route - attribute routing is always available regardless of how you configure `UseMvc`.</span></span> <span data-ttu-id="da86d-643">`UseMvcWithDefaultRoute` definuje výchozí trasu a podporuje směrování atributů.</span><span class="sxs-lookup"><span data-stu-id="da86d-643">`UseMvcWithDefaultRoute` defines a default route and supports attribute routing.</span></span> <span data-ttu-id="da86d-644">Oddíl [Směrování atributů](#attribute-routing-ref-label) obsahuje další podrobnosti o směrování atributů.</span><span class="sxs-lookup"><span data-stu-id="da86d-644">The [Attribute Routing](#attribute-routing-ref-label) section includes more details on attribute routing.</span></span>

<a name="routing-conventional-ref-label"></a>

## <a name="conventional-routing"></a><span data-ttu-id="da86d-645">Konvenční směrování</span><span class="sxs-lookup"><span data-stu-id="da86d-645">Conventional routing</span></span>

<span data-ttu-id="da86d-646">`default`Trasa:</span><span class="sxs-lookup"><span data-stu-id="da86d-646">The `default` route:</span></span>

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="da86d-647">Předchozí kód je příkladem konvenčního směrování.</span><span class="sxs-lookup"><span data-stu-id="da86d-647">The preceding code is an example of a conventional routing.</span></span> <span data-ttu-id="da86d-648">Tento styl se nazývá konvenční směrování, protože vytváří *konvenci* pro cesty URL:</span><span class="sxs-lookup"><span data-stu-id="da86d-648">This style is called conventional routing because it establishes a *convention* for URL paths:</span></span>

* <span data-ttu-id="da86d-649">První segment cesty se mapuje na název kontroleru.</span><span class="sxs-lookup"><span data-stu-id="da86d-649">The first path segment maps to the controller name.</span></span>
* <span data-ttu-id="da86d-650">Druhý se mapuje na název akce.</span><span class="sxs-lookup"><span data-stu-id="da86d-650">The second maps to the action name.</span></span>
* <span data-ttu-id="da86d-651">Třetí segment se používá pro volitelné `id` .</span><span class="sxs-lookup"><span data-stu-id="da86d-651">The third segment is used for an optional `id`.</span></span> <span data-ttu-id="da86d-652">`id` provede mapování na entitu modelu.</span><span class="sxs-lookup"><span data-stu-id="da86d-652">`id` maps to a model entity.</span></span>

<span data-ttu-id="da86d-653">Pomocí této `default` trasy se cesta URL `/Products/List` mapuje na `ProductsController.List` akci a `/Blog/Article/17` mapuje se na `BlogController.Article` .</span><span class="sxs-lookup"><span data-stu-id="da86d-653">Using this `default` route, the URL path `/Products/List` maps to the `ProductsController.List` action, and `/Blog/Article/17` maps to `BlogController.Article`.</span></span> <span data-ttu-id="da86d-654">Toto mapování je založené **pouze** na názvech kontroléru a akce a není založené na oborech názvů, umístěních zdrojového souboru nebo parametrech metody.</span><span class="sxs-lookup"><span data-stu-id="da86d-654">This mapping is based on the controller and action names **only** and isn't based on namespaces, source file locations, or method parameters.</span></span>

> [!TIP]
> <span data-ttu-id="da86d-655">Použití konvenčního směrování s výchozí trasou vám umožní rychle vytvořit aplikaci, aniž by se musela používat nový vzor adresy URL pro každou akci, kterou definujete.</span><span class="sxs-lookup"><span data-stu-id="da86d-655">Using conventional routing with the default route allows you to build the application quickly without having to come up with a new URL pattern for each action you define.</span></span> <span data-ttu-id="da86d-656">Pro aplikace s akcemi stylu CRUD, které mají konzistenci pro adresy URL napříč řadiči, může zjednodušit váš kód a zajistit větší předvídatelné uživatelské rozhraní.</span><span class="sxs-lookup"><span data-stu-id="da86d-656">For an application with CRUD style actions, having consistency for the URLs across your controllers can help simplify your code and make your UI more predictable.</span></span>

> [!WARNING]
> <span data-ttu-id="da86d-657">`id`Šablona trasy je definována jako volitelná, což znamená, že vaše akce mohou být provedeny bez ID, které je součástí adresy URL.</span><span class="sxs-lookup"><span data-stu-id="da86d-657">The `id` is defined as optional by the route template, meaning that your actions can execute without the ID provided as part of the URL.</span></span> <span data-ttu-id="da86d-658">K tomu, co se stane `id` , když se z adresy URL vynechá, znamená to, že se nastaví na `0` základě vazby modelů, a v důsledku toho se v porovnání databáze nenajde žádná entita `id == 0` .</span><span class="sxs-lookup"><span data-stu-id="da86d-658">Usually what will happen if `id` is omitted from the URL is that it will be set to `0` by model binding, and as a result no entity will be found in the database matching `id == 0`.</span></span> <span data-ttu-id="da86d-659">Směrování atributů vám může poskytnout jemně odstupňovaný ovládací prvek, který umožňuje, aby se ID vyžadovalo u některých akcí, a ne pro ostatní.</span><span class="sxs-lookup"><span data-stu-id="da86d-659">Attribute routing can give you fine-grained control to make the ID required for some actions and not for others.</span></span> <span data-ttu-id="da86d-660">Podle konvence bude dokumentace obsahovat volitelné parametry, jako když se budou `id` pravděpodobně zobrazovat ve správném použití.</span><span class="sxs-lookup"><span data-stu-id="da86d-660">By convention the documentation will include optional parameters like `id` when they're likely to appear in correct usage.</span></span>

## <a name="multiple-routes"></a><span data-ttu-id="da86d-661">Více tras</span><span class="sxs-lookup"><span data-stu-id="da86d-661">Multiple routes</span></span>

<span data-ttu-id="da86d-662">Do můžete přidat více tras v rámci `UseMvc` přidáním dalších volání do `MapRoute` .</span><span class="sxs-lookup"><span data-stu-id="da86d-662">You can add multiple routes inside `UseMvc` by adding more calls to `MapRoute`.</span></span> <span data-ttu-id="da86d-663">V takovém případě můžete definovat více konvencí nebo přidat konvenční trasy, které jsou vyhrazeny určité akci, například:</span><span class="sxs-lookup"><span data-stu-id="da86d-663">Doing so allows you to define multiple conventions, or to add conventional routes that are dedicated to a specific action, such as:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("blog", "blog/{*article}",
            defaults: new { controller = "Blog", action = "Article" });
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="da86d-664">`blog`Trasa je *vyhrazená konvenční trasa*, což znamená, že používá konvenční směrovací systém, ale je vyhrazená pro konkrétní akci.</span><span class="sxs-lookup"><span data-stu-id="da86d-664">The `blog` route here is a *dedicated conventional route*, meaning that it uses the conventional routing system, but is dedicated to a specific action.</span></span> <span data-ttu-id="da86d-665">Vzhledem `controller` k tomu, že se `action` nezobrazuje v šabloně směrování jako parametry, můžou mít jenom výchozí hodnoty, takže tato trasa se vždycky namapuje na akci `BlogController.Article` .</span><span class="sxs-lookup"><span data-stu-id="da86d-665">Since `controller` and `action` don't appear in the route template as parameters, they can only have the default values, and thus this route will always map to the action `BlogController.Article`.</span></span>

<span data-ttu-id="da86d-666">Trasy v kolekci tras jsou seřazené a budou zpracovávány v pořadí, v jakém byly přidány.</span><span class="sxs-lookup"><span data-stu-id="da86d-666">Routes in the route collection are ordered, and will be processed in the order they're added.</span></span> <span data-ttu-id="da86d-667">V tomto příkladu `blog` bude trasa vyzkoušena před `default` trasou.</span><span class="sxs-lookup"><span data-stu-id="da86d-667">So in this example, the `blog` route will be tried before the `default` route.</span></span>

> [!NOTE]
> <span data-ttu-id="da86d-668">*Vyhrazené konvenční trasy* často používají **zachycení všech** parametrů tras, jako `{*article}` je zachycení zbývající části cesty URL.</span><span class="sxs-lookup"><span data-stu-id="da86d-668">*Dedicated conventional routes* often use **catch-all** route parameters like `{*article}` to capture the remaining portion of the URL path.</span></span> <span data-ttu-id="da86d-669">To může vést k tomu, že trasa "je příliš hlade", což znamená, že odpovídá adresám URL, které mají odpovídat jiným trasám.</span><span class="sxs-lookup"><span data-stu-id="da86d-669">This can make a route 'too greedy' meaning that it matches URLs that you intended to be matched by other routes.</span></span> <span data-ttu-id="da86d-670">Pokud to chcete vyřešit, umístěte trasy "hladce" do směrovací tabulky později.</span><span class="sxs-lookup"><span data-stu-id="da86d-670">Put the 'greedy' routes later in the route table to solve this.</span></span>

### <a name="fallback"></a><span data-ttu-id="da86d-671">Záložní volba</span><span class="sxs-lookup"><span data-stu-id="da86d-671">Fallback</span></span>

<span data-ttu-id="da86d-672">V rámci zpracování žádosti MVC ověří, že hodnoty trasy lze použít k vyhledání kontroleru a akce v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="da86d-672">As part of request processing, MVC will verify that the route values can be used to find a controller and action in your application.</span></span> <span data-ttu-id="da86d-673">Pokud se hodnoty trasy neshodují s akcí, trasa není považována za shodnou a bude se opakovat další trasa.</span><span class="sxs-lookup"><span data-stu-id="da86d-673">If the route values don't match an action then the route isn't considered a match, and the next route will be tried.</span></span> <span data-ttu-id="da86d-674">Označuje se jako *záložní*a je určený pro zjednodušení případů, kdy se konvenční trasy překrývají.</span><span class="sxs-lookup"><span data-stu-id="da86d-674">This is called *fallback*, and it's intended to simplify cases where conventional routes overlap.</span></span>

### <a name="disambiguating-actions"></a><span data-ttu-id="da86d-675">Nejednoznačnost akcí</span><span class="sxs-lookup"><span data-stu-id="da86d-675">Disambiguating actions</span></span>

<span data-ttu-id="da86d-676">Pokud se dvě akce shodují přes směrování, MVC musí nejednoznačně vybrat kandidáta na nejlepší nebo jinak vyvolat výjimku.</span><span class="sxs-lookup"><span data-stu-id="da86d-676">When two actions match through routing, MVC must disambiguate to choose the 'best' candidate or else throw an exception.</span></span> <span data-ttu-id="da86d-677">Například:</span><span class="sxs-lookup"><span data-stu-id="da86d-677">For example:</span></span>

```csharp
public class ProductsController : Controller
{
   public IActionResult Edit(int id) { ... }

   [HttpPost]
   public IActionResult Edit(int id, Product product) { ... }
}
```

<span data-ttu-id="da86d-678">Tento kontroler definuje dvě akce, které by odpovídaly cestě URL `/Products/Edit/17` a datům směrování `{ controller = Products, action = Edit, id = 17 }` .</span><span class="sxs-lookup"><span data-stu-id="da86d-678">This controller defines two actions that would match the URL path `/Products/Edit/17` and route data `{ controller = Products, action = Edit, id = 17 }`.</span></span> <span data-ttu-id="da86d-679">Toto je typický vzor pro řadiče MVC, kde se `Edit(int)` zobrazuje formulář pro úpravu produktu a `Edit(int, Product)` zpracovává se tam publikovaný formulář.</span><span class="sxs-lookup"><span data-stu-id="da86d-679">This is a typical pattern for MVC controllers where `Edit(int)` shows a form to edit a product, and `Edit(int, Product)` processes  the posted form.</span></span> <span data-ttu-id="da86d-680">Aby tato možnost MVC mohla být v případě, že je `Edit(int, Product)` požadavek HTTP `POST` , a `Edit(int)` Pokud je příkaz HTTP cokoli jiného, musí si vybrat, když je žádost http.</span><span class="sxs-lookup"><span data-stu-id="da86d-680">To make this possible MVC would need to choose `Edit(int, Product)` when the request is an HTTP `POST` and `Edit(int)` when the HTTP verb is anything else.</span></span>

<span data-ttu-id="da86d-681">Rozhraní `HttpPostAttribute` ( `[HttpPost]` ) je implementací `IActionConstraint` , která umožňuje, aby byla akce vybrána pouze v případě, že je příkaz http `POST` .</span><span class="sxs-lookup"><span data-stu-id="da86d-681">The `HttpPostAttribute` ( `[HttpPost]` ) is an implementation of `IActionConstraint` that will only allow the action to be selected when the HTTP verb is `POST`.</span></span> <span data-ttu-id="da86d-682">Přítomnost `IActionConstraint` `Edit(int, Product)` a "lepší" se shoduje s tím, že se `Edit(int)` bude zkoušet jako `Edit(int, Product)` první.</span><span class="sxs-lookup"><span data-stu-id="da86d-682">The presence of an `IActionConstraint` makes the `Edit(int, Product)` a 'better' match than `Edit(int)`, so `Edit(int, Product)` will be tried first.</span></span>

<span data-ttu-id="da86d-683">Budete muset psát pouze vlastní `IActionConstraint` implementace ve specializovaných scénářích, ale je důležité pochopit, že role `HttpPostAttribute`  podobných atributů je definována pro jiné příkazy HTTP.</span><span class="sxs-lookup"><span data-stu-id="da86d-683">You will only need to write custom `IActionConstraint` implementations in specialized scenarios, but it's important to understand the role of attributes like `HttpPostAttribute`  - similar attributes are defined for other HTTP verbs.</span></span> <span data-ttu-id="da86d-684">V případě konvenčního směrování je běžné, že akce budou používat stejný název akce, i když jsou součástí `show form -> submit form` pracovního postupu.</span><span class="sxs-lookup"><span data-stu-id="da86d-684">In conventional routing it's common for actions to use the same action name when they're part of a `show form -> submit form` workflow.</span></span> <span data-ttu-id="da86d-685">Pohodlí tohoto vzoru se po kontrole části [porozumění IActionConstraint](#understanding-iactionconstraint) projeví.</span><span class="sxs-lookup"><span data-stu-id="da86d-685">The convenience of this pattern will become more apparent after reviewing the [Understanding IActionConstraint](#understanding-iactionconstraint) section.</span></span>

<span data-ttu-id="da86d-686">Pokud se více tras shodují a MVC nemůže najít nejlepší trasu, vyvolá výjimku `AmbiguousActionException` .</span><span class="sxs-lookup"><span data-stu-id="da86d-686">If multiple routes match, and MVC can't find a 'best' route, it will throw an `AmbiguousActionException`.</span></span>

<a name="routing-route-name-ref-label"></a>

### <a name="route-names"></a><span data-ttu-id="da86d-687">Názvy tras</span><span class="sxs-lookup"><span data-stu-id="da86d-687">Route names</span></span>

<span data-ttu-id="da86d-688">Řetězce  `"blog"` a `"default"` v následujících příkladech jsou názvy tras:</span><span class="sxs-lookup"><span data-stu-id="da86d-688">The strings  `"blog"` and `"default"` in the following examples are route names:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("blog", "blog/{*article}",
               defaults: new { controller = "Blog", action = "Article" });
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="da86d-689">Názvy tras směrují logický název tak, aby se pojmenovaná trasa dala použít pro generování adresy URL.</span><span class="sxs-lookup"><span data-stu-id="da86d-689">The route names give the route a logical name so that the named route can be used for URL generation.</span></span> <span data-ttu-id="da86d-690">Tím se značně zjednodušuje vytváření adres URL, když řazení tras může způsobit komplikované generování adresy URL.</span><span class="sxs-lookup"><span data-stu-id="da86d-690">This greatly simplifies URL creation when the ordering of routes could make URL generation complicated.</span></span> <span data-ttu-id="da86d-691">Názvy tras musí být jedinečné aplikace v rozsahu.</span><span class="sxs-lookup"><span data-stu-id="da86d-691">Route names must be unique application-wide.</span></span>

<span data-ttu-id="da86d-692">Názvy tras nemají žádný vliv na adresy URL, které by odpovídaly ani manipulaci s požadavky. používají se pouze pro generování adresy URL.</span><span class="sxs-lookup"><span data-stu-id="da86d-692">Route names have no impact on URL matching or handling of requests; they're used only for URL generation.</span></span> <span data-ttu-id="da86d-693">[Směrování](xref:fundamentals/routing) obsahuje podrobnější informace o generování adresy URL včetně generování adresy URL v pomocníkech specifických pro MVC.</span><span class="sxs-lookup"><span data-stu-id="da86d-693">[Routing](xref:fundamentals/routing) has more detailed information on URL generation including URL generation in MVC-specific helpers.</span></span>

<a name="attribute-routing-ref-label"></a>

## <a name="attribute-routing"></a><span data-ttu-id="da86d-694">Směrování atributů</span><span class="sxs-lookup"><span data-stu-id="da86d-694">Attribute routing</span></span>

<span data-ttu-id="da86d-695">Směrování atributů používá sadu atributů k mapování akcí přímo na šablony směrování.</span><span class="sxs-lookup"><span data-stu-id="da86d-695">Attribute routing uses a set of attributes to map actions directly to route templates.</span></span> <span data-ttu-id="da86d-696">V následujícím příkladu `app.UseMvc();` se používá v `Configure` metodě a není předána žádná trasa.</span><span class="sxs-lookup"><span data-stu-id="da86d-696">In the following example, `app.UseMvc();` is used in the `Configure` method and no route is passed.</span></span> <span data-ttu-id="da86d-697">`HomeController`Bude odpovídat sadě adres URL podobně jako výchozí trasa, která odpovídá `{controller=Home}/{action=Index}/{id?}` :</span><span class="sxs-lookup"><span data-stu-id="da86d-697">The `HomeController` will match a set of URLs similar to what the default route `{controller=Home}/{action=Index}/{id?}` would match:</span></span>

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

<span data-ttu-id="da86d-698">`HomeController.Index()`Akce se spustí pro všechny cesty URL `/` , `/Home` nebo `/Home/Index` .</span><span class="sxs-lookup"><span data-stu-id="da86d-698">The `HomeController.Index()` action will be executed for any of the URL paths `/`, `/Home`, or `/Home/Index`.</span></span>

> [!NOTE]
> <span data-ttu-id="da86d-699">V tomto příkladu se zvýrazní klíčový rozdíl mezi směrováním atributů a konvenčním směrováním.</span><span class="sxs-lookup"><span data-stu-id="da86d-699">This example highlights a key programming difference between attribute routing and conventional routing.</span></span> <span data-ttu-id="da86d-700">Směrování atributů vyžaduje více vstupu pro určení trasy; konvenční výchozí trasa zpracovává trasy více stručně.</span><span class="sxs-lookup"><span data-stu-id="da86d-700">Attribute routing requires more input to specify a route; the conventional default route handles routes more succinctly.</span></span> <span data-ttu-id="da86d-701">Směrování atributů ale povoluje (a vyžaduje) přesnou kontrolu nad tím, které šablony směrování se vztahují na každou akci.</span><span class="sxs-lookup"><span data-stu-id="da86d-701">However, attribute routing allows (and requires) precise control of which route templates apply to each action.</span></span>

<span data-ttu-id="da86d-702">Při směrování atributů název kontroleru a názvy akcí nehraje **žádná** role, ve které je vybrána akce.</span><span class="sxs-lookup"><span data-stu-id="da86d-702">With attribute routing the controller name and action names play **no** role in which action is selected.</span></span> <span data-ttu-id="da86d-703">Tento příklad bude odpovídat stejným adresám URL jako v předchozím příkladu.</span><span class="sxs-lookup"><span data-stu-id="da86d-703">This example will match the same URLs as the previous example.</span></span>

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
> <span data-ttu-id="da86d-704">Výše uvedené šablony tras nedefinují parametry směrování pro `action` , `area` a `controller` .</span><span class="sxs-lookup"><span data-stu-id="da86d-704">The route templates above don't define route parameters for `action`, `area`, and `controller`.</span></span> <span data-ttu-id="da86d-705">Ve skutečnosti nejsou tyto parametry trasy v trasách atributů povoleny.</span><span class="sxs-lookup"><span data-stu-id="da86d-705">In fact, these route parameters are not allowed in attribute routes.</span></span> <span data-ttu-id="da86d-706">Vzhledem k tomu, že šablona trasy je již k akci přidružena, by nedošlo k tomu, že by bylo vhodné analyzovat název akce z adresy URL.</span><span class="sxs-lookup"><span data-stu-id="da86d-706">Since the route template is already associated with an action, it wouldn't make sense to parse the action name from the URL.</span></span>

## <a name="attribute-routing-with-httpverb-attributes"></a><span data-ttu-id="da86d-707">Směrování atributů pomocí atributů http [příkaz]</span><span class="sxs-lookup"><span data-stu-id="da86d-707">Attribute routing with Http[Verb] attributes</span></span>

<span data-ttu-id="da86d-708">Směrování atributů může také používat atributy, jako je například `Http[Verb]` `HttpPostAttribute` .</span><span class="sxs-lookup"><span data-stu-id="da86d-708">Attribute routing can also make use of the `Http[Verb]` attributes such as `HttpPostAttribute`.</span></span> <span data-ttu-id="da86d-709">Všechny tyto atributy mohou přijmout šablonu směrování.</span><span class="sxs-lookup"><span data-stu-id="da86d-709">All of these attributes can accept a route template.</span></span> <span data-ttu-id="da86d-710">Tento příklad ukazuje dvě akce, které odpovídají stejné šabloně směrování:</span><span class="sxs-lookup"><span data-stu-id="da86d-710">This example shows two actions that match the same route template:</span></span>

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

<span data-ttu-id="da86d-711">V případě cesty URL, jako je akce, se spustí `/products` `ProductsApi.ListProducts` , když je příkaz http `GET` a spustí se, `ProductsApi.CreateProduct` když je příkaz http `POST` .</span><span class="sxs-lookup"><span data-stu-id="da86d-711">For a URL path like `/products` the `ProductsApi.ListProducts` action will be executed when the HTTP verb is `GET` and `ProductsApi.CreateProduct` will be executed when the HTTP verb is `POST`.</span></span> <span data-ttu-id="da86d-712">Směrování atributů první odpovídá adrese URL oproti sadě šablon směrování definovaných atributy směrování.</span><span class="sxs-lookup"><span data-stu-id="da86d-712">Attribute routing first matches the URL against the set of route templates defined by route attributes.</span></span> <span data-ttu-id="da86d-713">Po porovnání šablony trasy `IActionConstraint` se použijí omezení pro určení akcí, které lze provést.</span><span class="sxs-lookup"><span data-stu-id="da86d-713">Once a route template matches, `IActionConstraint` constraints are applied to determine which actions can be executed.</span></span>

> [!TIP]
> <span data-ttu-id="da86d-714">Při sestavování REST API je zřídka pravděpodobné, že budete chtít použít `[Route(...)]` metodu akce, protože akce bude přijímat všechny metody HTTP.</span><span class="sxs-lookup"><span data-stu-id="da86d-714">When building a REST API, it's rare that you will want to use `[Route(...)]` on an action method as the action will accept all HTTP methods.</span></span> <span data-ttu-id="da86d-715">Je lepší použít konkrétnější a `Http*Verb*Attributes` přesnější informace o tom, co vaše rozhraní API podporuje.</span><span class="sxs-lookup"><span data-stu-id="da86d-715">It's better to use the more specific `Http*Verb*Attributes` to be precise about what your API supports.</span></span> <span data-ttu-id="da86d-716">Očekává se, že klienti rozhraní REST API znají, které cesty a příkazy HTTP se mapují na konkrétní logické operace.</span><span class="sxs-lookup"><span data-stu-id="da86d-716">Clients of REST APIs are expected to know what paths and HTTP verbs map to specific logical operations.</span></span>

<span data-ttu-id="da86d-717">Vzhledem k tomu, že trasa atributu se vztahuje na konkrétní akci, je snadné vytvořit parametry požadované v rámci definice šablony trasy.</span><span class="sxs-lookup"><span data-stu-id="da86d-717">Since an attribute route applies to a specific action, it's easy to make parameters required as part of the route template definition.</span></span> <span data-ttu-id="da86d-718">V tomto příkladu `id` je vyžadována jako součást cesty URL.</span><span class="sxs-lookup"><span data-stu-id="da86d-718">In this example, `id` is required as part of the URL path.</span></span>

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

<span data-ttu-id="da86d-719">`ProductsApi.GetProduct(int)`Akce se spustí pro cestu URL, například `/products/3` ne pro cestu URL `/products` .</span><span class="sxs-lookup"><span data-stu-id="da86d-719">The `ProductsApi.GetProduct(int)` action will be executed for a URL path like `/products/3` but not for a URL path like `/products`.</span></span> <span data-ttu-id="da86d-720">Úplný popis šablon směrování a souvisejících možností najdete v tématu věnovaném [Směrování](xref:fundamentals/routing) .</span><span class="sxs-lookup"><span data-stu-id="da86d-720">See [Routing](xref:fundamentals/routing) for a full description of route templates and related options.</span></span>

## <a name="route-name"></a><span data-ttu-id="da86d-721">Název trasy</span><span class="sxs-lookup"><span data-stu-id="da86d-721">Route Name</span></span>

<span data-ttu-id="da86d-722">Následující kód definuje *název trasy* `Products_List` :</span><span class="sxs-lookup"><span data-stu-id="da86d-722">The following code  defines a *route name* of `Products_List`:</span></span>

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

<span data-ttu-id="da86d-723">Názvy tras se dají použít k vygenerování adresy URL na základě konkrétní trasy.</span><span class="sxs-lookup"><span data-stu-id="da86d-723">Route names can be used to generate a URL based on a specific route.</span></span> <span data-ttu-id="da86d-724">Názvy tras nemají žádný vliv na adresu URL, která odpovídá chování směrování a používají se jenom pro generování adresy URL.</span><span class="sxs-lookup"><span data-stu-id="da86d-724">Route names have no impact on the URL matching behavior of routing and are only used for URL generation.</span></span> <span data-ttu-id="da86d-725">Názvy tras musí být jedinečné aplikace v rozsahu.</span><span class="sxs-lookup"><span data-stu-id="da86d-725">Route names must be unique application-wide.</span></span>

> [!NOTE]
> <span data-ttu-id="da86d-726">Na rozdíl od konvenční *výchozí trasy*definuje `id` parametr jako volitelné ( `{id?}` ).</span><span class="sxs-lookup"><span data-stu-id="da86d-726">Contrast this with the conventional *default route*, which defines the `id` parameter as optional (`{id?}`).</span></span> <span data-ttu-id="da86d-727">Tato možnost přesného určení rozhraní API má výhody, jako je povolení `/products` a `/products/5` odeslání do různých akcí.</span><span class="sxs-lookup"><span data-stu-id="da86d-727">This ability to precisely specify APIs has advantages, such as  allowing `/products` and `/products/5` to be dispatched to different actions.</span></span>

<a name="routing-combining-ref-label"></a>

### <a name="combining-routes"></a><span data-ttu-id="da86d-728">Kombinování tras</span><span class="sxs-lookup"><span data-stu-id="da86d-728">Combining routes</span></span>

<span data-ttu-id="da86d-729">Aby bylo směrování atributů méně opakované, jsou atributy směrování na kontroleru kombinovány s atributy směrování na jednotlivých akcích.</span><span class="sxs-lookup"><span data-stu-id="da86d-729">To make attribute routing less repetitive, route attributes on the controller are combined with route attributes on the individual actions.</span></span> <span data-ttu-id="da86d-730">Každá šablona trasy definovaná na řadiči je předá směrování šablon na akcích.</span><span class="sxs-lookup"><span data-stu-id="da86d-730">Any route templates defined on the controller are prepended to route templates on the actions.</span></span> <span data-ttu-id="da86d-731">Umístěním atributu směrování na řadiči se vytvoří **všechny** akce v řadiči použít směrování atributů.</span><span class="sxs-lookup"><span data-stu-id="da86d-731">Placing a route attribute on the controller makes **all** actions in the controller use attribute routing.</span></span>

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

<span data-ttu-id="da86d-732">V tomto příkladu se cesta URL `/products` může shodovat `ProductsApi.ListProducts` a cesta k adrese URL se `/products/5` může shodovat `ProductsApi.GetProduct(int)` .</span><span class="sxs-lookup"><span data-stu-id="da86d-732">In this example the URL path `/products` can match `ProductsApi.ListProducts`, and the URL path `/products/5` can match `ProductsApi.GetProduct(int)`.</span></span> <span data-ttu-id="da86d-733">Obě tyto akce odpovídají pouze HTTP, `GET` protože jsou označeny pomocí `HttpGetAttribute` .</span><span class="sxs-lookup"><span data-stu-id="da86d-733">Both of these actions only match HTTP `GET` because they're marked with the `HttpGetAttribute`.</span></span>

<span data-ttu-id="da86d-734">Šablony směrování použité pro akci, která začíná `/` nebo `~/` není kombinována s šablonami směrování použitými pro kontroler.</span><span class="sxs-lookup"><span data-stu-id="da86d-734">Route templates applied to an action that begin with `/` or `~/` don't get combined with route templates applied to the controller.</span></span> <span data-ttu-id="da86d-735">Tento příklad odpovídá sadě cest URL podobně jako *výchozí trasa*.</span><span class="sxs-lookup"><span data-stu-id="da86d-735">This example matches a set of URL paths similar to the *default route*.</span></span>

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

### <a name="ordering-attribute-routes"></a><span data-ttu-id="da86d-736">Směrování atributů řazení</span><span class="sxs-lookup"><span data-stu-id="da86d-736">Ordering attribute routes</span></span>

<span data-ttu-id="da86d-737">Na rozdíl od konvenčních tras, které se spouštějí v definovaném pořadí, směrování atributů vytvoří strom a vyhledá současně všechny trasy.</span><span class="sxs-lookup"><span data-stu-id="da86d-737">In contrast to conventional routes, which execute in a defined order, attribute routing builds a tree and matches all routes simultaneously.</span></span> <span data-ttu-id="da86d-738">To se chová jako – Pokud byly položky trasy umístěny v ideálním pořadí; nejvíce konkrétní trasy mají možnost provést před obecnější trasy.</span><span class="sxs-lookup"><span data-stu-id="da86d-738">This behaves as-if the route entries were placed in an ideal ordering; the most specific routes have a chance to execute before the more general routes.</span></span>

<span data-ttu-id="da86d-739">Například trasa, jako je konkrétnější, `blog/search/{topic}` než trasa, jako je třeba `blog/{*article}` .</span><span class="sxs-lookup"><span data-stu-id="da86d-739">For example, a route like `blog/search/{topic}` is more specific than a route like `blog/{*article}`.</span></span> <span data-ttu-id="da86d-740">Logicky mluví `blog/search/{topic}` nejprve Route "běh", protože se jedná o jediné řazení rozumné.</span><span class="sxs-lookup"><span data-stu-id="da86d-740">Logically speaking the `blog/search/{topic}` route 'runs' first, by default, because that's the only sensible ordering.</span></span> <span data-ttu-id="da86d-741">Pomocí konvenčního směrování zodpovídá vývojář za umístění tras v požadovaném pořadí.</span><span class="sxs-lookup"><span data-stu-id="da86d-741">Using conventional routing, the developer is  responsible for placing routes in the desired order.</span></span>

<span data-ttu-id="da86d-742">Trasy atributů mohou konfigurovat objednávku pomocí `Order` vlastnosti všech rozhraní, které poskytují atributy směrování.</span><span class="sxs-lookup"><span data-stu-id="da86d-742">Attribute routes can configure an order, using the `Order` property of all of the framework provided route attributes.</span></span> <span data-ttu-id="da86d-743">Trasy jsou zpracovávány podle vzestupného řazení `Order` Vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="da86d-743">Routes are processed according to an ascending sort of the `Order` property.</span></span> <span data-ttu-id="da86d-744">Výchozí pořadí je `0` .</span><span class="sxs-lookup"><span data-stu-id="da86d-744">The default order is `0`.</span></span> <span data-ttu-id="da86d-745">Nastavení trasy `Order = -1` , která se používá, se spustí před trasami, které nenastaví objednávku.</span><span class="sxs-lookup"><span data-stu-id="da86d-745">Setting a route using `Order = -1` will run before routes that don't set an order.</span></span> <span data-ttu-id="da86d-746">Nastavování trasy pomocí `Order = 1` se spustí po výchozím řazení směrování.</span><span class="sxs-lookup"><span data-stu-id="da86d-746">Setting a route using `Order = 1` will run after default route ordering.</span></span>

> [!TIP]
> <span data-ttu-id="da86d-747">Nepoužívejte v závislosti `Order` .</span><span class="sxs-lookup"><span data-stu-id="da86d-747">Avoid depending on `Order`.</span></span> <span data-ttu-id="da86d-748">Pokud vaše adresa URL vyžaduje explicitní směrování hodnot pořadí, je pravděpodobné, že budou i u klientů matoucí.</span><span class="sxs-lookup"><span data-stu-id="da86d-748">If your URL-space requires explicit order values to route correctly, then it's likely confusing to clients as well.</span></span> <span data-ttu-id="da86d-749">V části Obecné směrování atributů výběr správné trasy s odpovídající adresou URL.</span><span class="sxs-lookup"><span data-stu-id="da86d-749">In general attribute routing will select the correct route with URL matching.</span></span> <span data-ttu-id="da86d-750">Pokud výchozí pořadí použité pro generování adresy URL nefunguje, je použití názvu trasy jako přepsání obvykle jednodušší než použití `Order` Vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="da86d-750">If the default order used for URL generation isn't working, using route name as an override is usually simpler than applying the `Order` property.</span></span>

<span data-ttu-id="da86d-751">Razor Směrování stránek a směrování kontroléru MVC sdílí implementaci.</span><span class="sxs-lookup"><span data-stu-id="da86d-751">Razor Pages routing and MVC controller routing share an implementation.</span></span> <span data-ttu-id="da86d-752">Informace o pořadí směrování v Razor tématech stránky jsou k dispozici na [ Razor stránkách směrování a konvence aplikace: pořadí směrování](xref:razor-pages/razor-pages-conventions#route-order).</span><span class="sxs-lookup"><span data-stu-id="da86d-752">Information on route order in the Razor Pages topics is available at [Razor Pages route and app conventions: Route order](xref:razor-pages/razor-pages-conventions#route-order).</span></span>

<a name="routing-token-replacement-templates-ref-label"></a>

## <a name="token-replacement-in-route-templates-controller-action-area"></a><span data-ttu-id="da86d-753">Nahrazení tokenu v šablonách směrování ([Controller], [Action], [oblast])</span><span class="sxs-lookup"><span data-stu-id="da86d-753">Token replacement in route templates ([controller], [action], [area])</span></span>

<span data-ttu-id="da86d-754">Pro usnadnění pohodlí trasy atributů podporují *Nahrazení tokenu* uzavřením tokenu do hranatých závorek ( `[` , `]` ).</span><span class="sxs-lookup"><span data-stu-id="da86d-754">For convenience, attribute routes support *token replacement* by enclosing a token in square-brackets (`[`, `]`).</span></span> <span data-ttu-id="da86d-755">Tokeny `[action]` , `[area]` a `[controller]` jsou nahrazeny hodnotami názvu akce, názvu oblasti a názvu kontroleru z akce, kde je trasa definována.</span><span class="sxs-lookup"><span data-stu-id="da86d-755">The tokens `[action]`, `[area]`, and `[controller]` are replaced with the values of the action name, area name, and controller name from the action where the route is defined.</span></span> <span data-ttu-id="da86d-756">V následujícím příkladu akce odpovídají cestám adresy URL, jak je popsáno v komentářích:</span><span class="sxs-lookup"><span data-stu-id="da86d-756">In the following example, the actions match URL paths as described in the comments:</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/ProductsController.cs?range=7-11,13-17,20-22)]

<span data-ttu-id="da86d-757">Nahrazení tokenu probíhá jako poslední krok při vytváření tras atributů.</span><span class="sxs-lookup"><span data-stu-id="da86d-757">Token replacement occurs as the last step of building the attribute routes.</span></span> <span data-ttu-id="da86d-758">Výše uvedený příklad se chová stejně jako následující kód:</span><span class="sxs-lookup"><span data-stu-id="da86d-758">The above example will behave the same as the following code:</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/ProductsController2.cs?range=7-11,13-17,20-22)]

<span data-ttu-id="da86d-759">Trasy atributů je také možné kombinovat s dědičností.</span><span class="sxs-lookup"><span data-stu-id="da86d-759">Attribute routes can also be combined with inheritance.</span></span> <span data-ttu-id="da86d-760">To je zvlášť výkonné v kombinaci s náhradou tokenu.</span><span class="sxs-lookup"><span data-stu-id="da86d-760">This is particularly powerful combined with token replacement.</span></span>

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

<span data-ttu-id="da86d-761">Nahrazení tokenu platí také pro názvy tras definované směrováními atributů.</span><span class="sxs-lookup"><span data-stu-id="da86d-761">Token replacement also applies to route names defined by attribute routes.</span></span> <span data-ttu-id="da86d-762">`[Route("[controller]/[action]", Name="[controller]_[action]")]` vygeneruje jedinečný název trasy pro každou akci.</span><span class="sxs-lookup"><span data-stu-id="da86d-762">`[Route("[controller]/[action]", Name="[controller]_[action]")]` generates a unique route name for each action.</span></span>

<span data-ttu-id="da86d-763">Chcete-li spárovat oddělovač nahrazení tokenu literálu `[` nebo  `]` , zastavte ho opakováním znaku ( `[[` nebo `]]` ).</span><span class="sxs-lookup"><span data-stu-id="da86d-763">To match the literal token replacement delimiter `[` or  `]`, escape it by repeating the character (`[[` or `]]`).</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<a name="routing-token-replacement-transformers-ref-label"></a>

### <a name="use-a-parameter-transformer-to-customize-token-replacement"></a><span data-ttu-id="da86d-764">Použití transformátoru parametrů k přizpůsobení náhrady tokenu</span><span class="sxs-lookup"><span data-stu-id="da86d-764">Use a parameter transformer to customize token replacement</span></span>

<span data-ttu-id="da86d-765">Nahrazení tokenu se dá přizpůsobit pomocí transformátoru parametrů.</span><span class="sxs-lookup"><span data-stu-id="da86d-765">Token replacement can be customized using a parameter transformer.</span></span> <span data-ttu-id="da86d-766">Parametr Transformer implementuje `IOutboundParameterTransformer` a transformuje hodnotu parametrů.</span><span class="sxs-lookup"><span data-stu-id="da86d-766">A parameter transformer implements `IOutboundParameterTransformer` and transforms the value of parameters.</span></span> <span data-ttu-id="da86d-767">Například vlastní `SlugifyParameterTransformer` parametr Transformer změní `SubscriptionManagement` hodnotu Route na `subscription-management` .</span><span class="sxs-lookup"><span data-stu-id="da86d-767">For example, a custom `SlugifyParameterTransformer` parameter transformer changes the `SubscriptionManagement` route value to `subscription-management`.</span></span>

<span data-ttu-id="da86d-768">`RouteTokenTransformerConvention`Je konvence aplikačního modelu, která:</span><span class="sxs-lookup"><span data-stu-id="da86d-768">The `RouteTokenTransformerConvention` is an application model convention that:</span></span>

* <span data-ttu-id="da86d-769">Aplikuje transformátor parametrů na všechny trasy atributů v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="da86d-769">Applies a parameter transformer to all attribute routes in an application.</span></span>
* <span data-ttu-id="da86d-770">Přizpůsobí hodnoty tokenu trasy při jejich nahrazení.</span><span class="sxs-lookup"><span data-stu-id="da86d-770">Customizes the attribute route token values as they are replaced.</span></span>

```csharp
public class SubscriptionManagementController : Controller
{
    [HttpGet("[controller]/[action]")] // Matches '/subscription-management/list-all'
    public IActionResult ListAll() { ... }
}
```

<span data-ttu-id="da86d-771">`RouteTokenTransformerConvention`Je zaregistrován jako možnost v `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="da86d-771">The `RouteTokenTransformerConvention` is registered as an option in `ConfigureServices`.</span></span>

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

### <a name="multiple-routes"></a><span data-ttu-id="da86d-772">Více tras</span><span class="sxs-lookup"><span data-stu-id="da86d-772">Multiple Routes</span></span>

<span data-ttu-id="da86d-773">Směrování atributů podporuje definování více tras, které dosáhnou stejné akce.</span><span class="sxs-lookup"><span data-stu-id="da86d-773">Attribute routing supports defining multiple routes that reach the same action.</span></span> <span data-ttu-id="da86d-774">Nejběžnějším využitím je napodobení chování *výchozí konvenční trasy* , jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="da86d-774">The most common usage of this is to mimic the behavior of the *default conventional route* as shown in the following example:</span></span>

```csharp
[Route("[controller]")]
public class ProductsController : Controller
{
   [Route("")]     // Matches 'Products'
   [Route("Index")] // Matches 'Products/Index'
   public IActionResult Index()
}
```

<span data-ttu-id="da86d-775">Vložení více atributů trasy na řadič znamená, že každá z nich bude kombinována s každým z atributů tras na metodách akcí.</span><span class="sxs-lookup"><span data-stu-id="da86d-775">Putting multiple route attributes on the controller means that each one will combine with each of the route attributes on the action methods.</span></span>

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

<span data-ttu-id="da86d-776">Pokud jsou na akci umístěny různé atributy trasy (implementující `IActionConstraint` ), pak každé omezení akce kombinuje s šablonou směrování z atributu, který ji definoval.</span><span class="sxs-lookup"><span data-stu-id="da86d-776">When multiple route attributes (that implement `IActionConstraint`) are placed on an action, then each action constraint combines with the route template from the attribute that defined it.</span></span>

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
> <span data-ttu-id="da86d-777">Zatímco používání více tras na akcích může vypadat jako účinné, je lepší zachovat jednoduché a jasně definované místo URL vaší aplikace.</span><span class="sxs-lookup"><span data-stu-id="da86d-777">While using multiple routes on actions can seem powerful, it's better to keep your application's URL space simple and well-defined.</span></span> <span data-ttu-id="da86d-778">V případě potřeby použijte více tras na akcích, například pro podporu stávajících klientů.</span><span class="sxs-lookup"><span data-stu-id="da86d-778">Use multiple routes on actions only where needed, for example to support existing clients.</span></span>

<a name="routing-attr-options"></a>

### <a name="specifying-attribute-route-optional-parameters-default-values-and-constraints"></a><span data-ttu-id="da86d-779">Určení volitelných parametrů trasy atributu, výchozích hodnot a omezení</span><span class="sxs-lookup"><span data-stu-id="da86d-779">Specifying attribute route optional parameters, default values, and constraints</span></span>

<span data-ttu-id="da86d-780">Trasy atributů podporují stejnou vloženou syntaxi jako konvenční trasy, aby bylo možné zadat volitelné parametry, výchozí hodnoty a omezení.</span><span class="sxs-lookup"><span data-stu-id="da86d-780">Attribute routes support the same inline syntax as conventional routes to specify optional parameters, default values, and constraints.</span></span>

```csharp
[HttpPost("product/{id:int}")]
public IActionResult ShowProduct(int id)
{
   // ...
}
```

<span data-ttu-id="da86d-781">Podrobný popis syntaxe šablony směrování naleznete v tématu Referenční dokumentace k [šabloně směrování](xref:fundamentals/routing#route-template-reference) .</span><span class="sxs-lookup"><span data-stu-id="da86d-781">See [Route Template Reference](xref:fundamentals/routing#route-template-reference) for a detailed description of route template syntax.</span></span>

<a name="routing-cust-rt-attr-irt-ref-label"></a>

### <a name="custom-route-attributes-using-iroutetemplateprovider"></a><span data-ttu-id="da86d-782">Vlastní atributy směrování pomocí `IRouteTemplateProvider`</span><span class="sxs-lookup"><span data-stu-id="da86d-782">Custom route attributes using `IRouteTemplateProvider`</span></span>

<span data-ttu-id="da86d-783">Všechny atributy směrování, které jsou k dispozici v rozhraní ( `[Route(...)]` , `[HttpGet(...)]` atd.) implementují `IRouteTemplateProvider` rozhraní.</span><span class="sxs-lookup"><span data-stu-id="da86d-783">All of the route attributes provided in the framework ( `[Route(...)]`, `[HttpGet(...)]` , etc.) implement the `IRouteTemplateProvider` interface.</span></span> <span data-ttu-id="da86d-784">MVC vyhledává atributy tříd kontroleru a metody akcí při spuštění aplikace a používá ty, které implementují `IRouteTemplateProvider` k sestavení počáteční sady tras.</span><span class="sxs-lookup"><span data-stu-id="da86d-784">MVC looks for attributes on controller classes and action methods when the app starts and uses the ones that implement `IRouteTemplateProvider` to build the initial set of routes.</span></span>

<span data-ttu-id="da86d-785">Můžete implementovat `IRouteTemplateProvider` k definování vlastních atributů tras.</span><span class="sxs-lookup"><span data-stu-id="da86d-785">You can implement `IRouteTemplateProvider` to define your own route attributes.</span></span> <span data-ttu-id="da86d-786">Každá `IRouteTemplateProvider` z nich umožňuje definovat jednu trasu s vlastní šablonou směrování, objednávkou a názvem:</span><span class="sxs-lookup"><span data-stu-id="da86d-786">Each `IRouteTemplateProvider` allows you to define a single route with a custom route template, order, and name:</span></span>

```csharp
public class MyApiControllerAttribute : Attribute, IRouteTemplateProvider
{
   public string Template => "api/[controller]";

   public int? Order { get; set; }

   public string Name { get; set; }
}
```

<span data-ttu-id="da86d-787">Atribut z výše uvedeného příkladu automaticky nastaví na `Template` `"api/[controller]"` hodnotu when při `[MyApiController]` použití.</span><span class="sxs-lookup"><span data-stu-id="da86d-787">The attribute from the above example automatically sets the `Template` to `"api/[controller]"` when `[MyApiController]` is applied.</span></span>

<a name="routing-app-model-ref-label"></a>

### <a name="using-application-model-to-customize-attribute-routes"></a><span data-ttu-id="da86d-788">Přizpůsobení tras atributů pomocí aplikačního modelu</span><span class="sxs-lookup"><span data-stu-id="da86d-788">Using Application Model to customize attribute routes</span></span>

<span data-ttu-id="da86d-789">*Aplikační model* je objektový model vytvořený při spuštění se všemi metadaty, které MVC používá ke směrování a provádění akcí.</span><span class="sxs-lookup"><span data-stu-id="da86d-789">The *application model* is an object model created at startup with all of the metadata used by MVC to route and execute your actions.</span></span> <span data-ttu-id="da86d-790">*Aplikační model* zahrnuje všechna data získaná z atributů trasy (prostřednictvím `IRouteTemplateProvider` ).</span><span class="sxs-lookup"><span data-stu-id="da86d-790">The *application model* includes all of the data gathered from route attributes (through `IRouteTemplateProvider`).</span></span> <span data-ttu-id="da86d-791">Můžete napsat *konvence* pro úpravu aplikačního modelu v době spuštění, abyste mohli přizpůsobit způsob, jakým se směrování chová.</span><span class="sxs-lookup"><span data-stu-id="da86d-791">You can write *conventions* to modify the application model at startup time to customize how routing behaves.</span></span> <span data-ttu-id="da86d-792">V této části se dozvíte jednoduchý příklad přizpůsobení směrování pomocí aplikačního modelu.</span><span class="sxs-lookup"><span data-stu-id="da86d-792">This section shows a simple example of customizing routing using application model.</span></span>

[!code-csharp[](routing/samples/2.x/main/NamespaceRoutingConvention.cs)]

<a name="routing-mixed-ref-label"></a>

## <a name="mixed-routing-attribute-routing-vs-conventional-routing"></a><span data-ttu-id="da86d-793">Kombinované směrování: směrování atributů vs. konvenční směrování</span><span class="sxs-lookup"><span data-stu-id="da86d-793">Mixed routing: Attribute routing vs conventional routing</span></span>

<span data-ttu-id="da86d-794">Aplikace MVC můžou kombinovat použití konvenčního směrování a směrování atributů.</span><span class="sxs-lookup"><span data-stu-id="da86d-794">MVC applications can mix the use of conventional routing and attribute routing.</span></span> <span data-ttu-id="da86d-795">Pro řadiče, které obsluhují stránky HTML pro prohlížeče, a směrování atributů pro řadiče obsluhující rozhraní REST API se obvykle používá konvenční trasy.</span><span class="sxs-lookup"><span data-stu-id="da86d-795">It's typical to use conventional routes for controllers serving HTML pages for browsers, and attribute routing for controllers serving REST APIs.</span></span>

<span data-ttu-id="da86d-796">Akce jsou směrovány buď podle konvence nebo směrovaného atributu.</span><span class="sxs-lookup"><span data-stu-id="da86d-796">Actions are either conventionally routed or attribute routed.</span></span> <span data-ttu-id="da86d-797">Umístěním trasy do kontroleru nebo akce se nasměruje atribut IT.</span><span class="sxs-lookup"><span data-stu-id="da86d-797">Placing a route on the controller or the action makes it attribute routed.</span></span> <span data-ttu-id="da86d-798">Akce definující trasy atributů nemůžou být dostupné prostřednictvím konvenčních tras a naopak.</span><span class="sxs-lookup"><span data-stu-id="da86d-798">Actions that define attribute routes cannot be reached through the conventional routes and vice-versa.</span></span> <span data-ttu-id="da86d-799">**Libovolný** atribut směrování v řadiči provádí směrování všech akcí v atributu kontroleru.</span><span class="sxs-lookup"><span data-stu-id="da86d-799">**Any** route attribute on the controller makes all actions in the controller attribute routed.</span></span>

> [!NOTE]
> <span data-ttu-id="da86d-800">Způsob, jakým se rozlišují dva typy systémů směrování, je proces, který se použije po tom, co adresa URL odpovídá šabloně směrování.</span><span class="sxs-lookup"><span data-stu-id="da86d-800">What distinguishes the two types of routing systems is the process applied after a URL matches a route template.</span></span> <span data-ttu-id="da86d-801">V části konvenční směrování se při výběru akce a kontroleru z vyhledávací tabulky všech konvenčních akcí směrování používají hodnoty tras z shody.</span><span class="sxs-lookup"><span data-stu-id="da86d-801">In conventional routing, the route values from the match are used to choose the action and controller from a lookup table of all conventional routed actions.</span></span> <span data-ttu-id="da86d-802">V směrování atributů je Každá šablona již přidružena k akci a není nutné žádné další vyhledávání.</span><span class="sxs-lookup"><span data-stu-id="da86d-802">In attribute routing, each template is already associated with an action, and no further lookup is needed.</span></span>

## <a name="complex-segments"></a><span data-ttu-id="da86d-803">Komplexní segmenty</span><span class="sxs-lookup"><span data-stu-id="da86d-803">Complex segments</span></span>

<span data-ttu-id="da86d-804">Komplexní segmenty (například `[Route("/dog{token}cat")]` ) jsou zpracovávány porovnáním koncových literálů zprava doleva nehladým způsobem.</span><span class="sxs-lookup"><span data-stu-id="da86d-804">Complex segments (for example, `[Route("/dog{token}cat")]`), are processed by matching up literals from right to left in a non-greedy way.</span></span> <span data-ttu-id="da86d-805">Popis najdete [ve zdrojovém kódu](https://github.com/aspnet/Routing/blob/9cea167cfac36cf034dbb780e3f783114ef94780/src/Microsoft.AspNetCore.Routing/Patterns/RoutePatternMatcher.cs#L296) .</span><span class="sxs-lookup"><span data-stu-id="da86d-805">See [the source code](https://github.com/aspnet/Routing/blob/9cea167cfac36cf034dbb780e3f783114ef94780/src/Microsoft.AspNetCore.Routing/Patterns/RoutePatternMatcher.cs#L296) for a description.</span></span> <span data-ttu-id="da86d-806">Další informace najdete v [tomto problému](https://github.com/dotnet/AspNetCore.Docs/issues/8197).</span><span class="sxs-lookup"><span data-stu-id="da86d-806">For more information, see [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/8197).</span></span>

<a name="routing-url-gen-ref-label"></a>

## <a name="url-generation"></a><span data-ttu-id="da86d-807">Generování adresy URL</span><span class="sxs-lookup"><span data-stu-id="da86d-807">URL Generation</span></span>

<span data-ttu-id="da86d-808">Aplikace MVC můžou použít funkce generování adresy URL směrování k vygenerování odkazů na akce v adresách URL.</span><span class="sxs-lookup"><span data-stu-id="da86d-808">MVC applications can use routing's URL generation features to generate URL links to actions.</span></span> <span data-ttu-id="da86d-809">Generování adres URL eliminuje adresy URL zakódujeme, takže je váš kód robustnější a udržovatelný.</span><span class="sxs-lookup"><span data-stu-id="da86d-809">Generating URLs eliminates hardcoding URLs, making your code more robust and maintainable.</span></span> <span data-ttu-id="da86d-810">Tato část se zaměřuje na funkce generování adresy URL poskytované MVC a bude se týkat jenom základních informací o tom, jak generování adresy URL funguje.</span><span class="sxs-lookup"><span data-stu-id="da86d-810">This section focuses on the URL generation features provided by MVC and will only cover basics of how URL generation works.</span></span> <span data-ttu-id="da86d-811">Podrobný popis generování adresy URL najdete v tématu věnovaném [Směrování](xref:fundamentals/routing) .</span><span class="sxs-lookup"><span data-stu-id="da86d-811">See [Routing](xref:fundamentals/routing) for a detailed description of URL generation.</span></span>

<span data-ttu-id="da86d-812">`IUrlHelper`Rozhraní je základní částí infrastruktury mezi MVC a směrováním pro generování adresy URL.</span><span class="sxs-lookup"><span data-stu-id="da86d-812">The `IUrlHelper` interface is the underlying piece of infrastructure between MVC and routing for URL generation.</span></span> <span data-ttu-id="da86d-813">`IUrlHelper`K dispozici je instance dostupná prostřednictvím `Url` vlastnosti v části řadiče, zobrazení a zobrazení komponent.</span><span class="sxs-lookup"><span data-stu-id="da86d-813">You'll find an instance of `IUrlHelper` available through the `Url` property in controllers, views, and view components.</span></span>

<span data-ttu-id="da86d-814">V tomto příkladu se `IUrlHelper` rozhraní používá přes `Controller.Url` vlastnost k VYGENEROVÁNÍ adresy URL jiné akci.</span><span class="sxs-lookup"><span data-stu-id="da86d-814">In this example, the `IUrlHelper` interface is used through the `Controller.Url` property to generate a URL to another action.</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationController.cs?name=snippet_1)]

<span data-ttu-id="da86d-815">Pokud aplikace používá výchozí konvenční trasu, hodnota `url` proměnné bude řetězec cesty adresy URL `/UrlGeneration/Destination` .</span><span class="sxs-lookup"><span data-stu-id="da86d-815">If the application is using the default conventional route, the value of the `url` variable will be the URL path string `/UrlGeneration/Destination`.</span></span> <span data-ttu-id="da86d-816">Tato cesta URL se vytvoří směrováním kombinováním hodnot tras z aktuální žádosti (okolní hodnoty) s hodnotami předanými do `Url.Action` a nahrazením těchto hodnot do šablony trasy:</span><span class="sxs-lookup"><span data-stu-id="da86d-816">This URL path is created by routing by combining the route values from the current request (ambient values), with the values passed to `Url.Action` and substituting those values into the route template:</span></span>

```
ambient values: { controller = "UrlGeneration", action = "Source" }
values passed to Url.Action: { controller = "UrlGeneration", action = "Destination" }
route template: {controller}/{action}/{id?}

result: /UrlGeneration/Destination
```

<span data-ttu-id="da86d-817">Každý parametr trasy v šabloně směrování má jeho hodnotu nahrazenou odpovídajícími názvy hodnotami a okolními hodnotami.</span><span class="sxs-lookup"><span data-stu-id="da86d-817">Each route parameter in the route template has its value substituted by matching names with the values and ambient values.</span></span> <span data-ttu-id="da86d-818">Parametr trasy, který nemá hodnotu, může používat výchozí hodnotu, pokud má jednu, nebo může být vynechána, pokud je volitelná (jako v případě `id` v tomto příkladu).</span><span class="sxs-lookup"><span data-stu-id="da86d-818">A route parameter that doesn't have a value can use a default value if it has one, or be skipped if it's optional (as in the case of `id` in this example).</span></span> <span data-ttu-id="da86d-819">Generování adresy URL selže, pokud žádný požadovaný parametr trasy nemá odpovídající hodnotu.</span><span class="sxs-lookup"><span data-stu-id="da86d-819">URL generation will fail if any required route parameter doesn't have a corresponding value.</span></span> <span data-ttu-id="da86d-820">Pokud se generování adresy URL u trasy nezdařilo, bude se zkoušet další trasa, dokud nebudou všechny trasy vyzkoušeny nebo nalezena shoda.</span><span class="sxs-lookup"><span data-stu-id="da86d-820">If URL generation fails for a route, the next route is tried until all routes have been tried or a match is found.</span></span>

<span data-ttu-id="da86d-821">`Url.Action`Výše uvedený příklad předpokládá konvenční směrování, ale generování adresy URL funguje podobně jako při směrování atributů, i když se koncepty liší.</span><span class="sxs-lookup"><span data-stu-id="da86d-821">The example of `Url.Action` above assumes conventional routing, but URL generation works similarly with attribute routing, though the concepts are different.</span></span> <span data-ttu-id="da86d-822">Při použití konvenčního směrování se hodnoty tras používají k rozšíření šablony a hodnoty tras pro `controller` a `action` obvykle se v této šabloně zobrazují – to funguje, protože adresy URL, které odpovídají směrování, dodržují *konvence*.</span><span class="sxs-lookup"><span data-stu-id="da86d-822">With conventional routing, the route values are used to expand a template, and the route values for `controller` and `action` usually appear in that template - this works because the URLs matched by routing adhere to a *convention*.</span></span> <span data-ttu-id="da86d-823">V případě směrování atributů se v `controller` šabloně nemůžou zobrazovat hodnoty tras pro a, které se používají `action` k vyhledání šablony, která se má použít.</span><span class="sxs-lookup"><span data-stu-id="da86d-823">In attribute routing, the route values for `controller` and `action` are not allowed to appear in the template - they're instead used to look up which template to use.</span></span>

<span data-ttu-id="da86d-824">V tomto příkladu se používá směrování atributů:</span><span class="sxs-lookup"><span data-stu-id="da86d-824">This example uses attribute routing:</span></span>

[!code-csharp[](routing/samples/2.x/main/StartupUseMvc.cs?name=snippet_1)]

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationControllerAttr.cs?name=snippet_1)]

<span data-ttu-id="da86d-825">MVC sestaví vyhledávací tabulku všech akcí směrovaného atributu a bude odpovídat `controller` `action` hodnotám a vybrat šablonu směrování, která se má použít pro generování adresy URL.</span><span class="sxs-lookup"><span data-stu-id="da86d-825">MVC builds a lookup table of all attribute routed actions and will match the `controller` and `action` values to select the route template to use for URL generation.</span></span> <span data-ttu-id="da86d-826">V ukázce výše   `custom/url/to/destination` je vygenerována.</span><span class="sxs-lookup"><span data-stu-id="da86d-826">In the sample above,   `custom/url/to/destination` is generated.</span></span>

### <a name="generating-urls-by-action-name"></a><span data-ttu-id="da86d-827">Generování adres URL podle názvu akce</span><span class="sxs-lookup"><span data-stu-id="da86d-827">Generating URLs by action name</span></span>

<span data-ttu-id="da86d-828">`Url.Action` (`IUrlHelper` .</span><span class="sxs-lookup"><span data-stu-id="da86d-828">`Url.Action` (`IUrlHelper` .</span></span> <span data-ttu-id="da86d-829">`Action`) a všechna související přetížení jsou založena na této myšlence, se kterou chcete určit, k čemu se odkazujete zadáním názvu kontroleru a názvu akce.</span><span class="sxs-lookup"><span data-stu-id="da86d-829">`Action`) and all related overloads all are based on that idea that you want to specify what you're linking to by specifying a controller name and action name.</span></span>

> [!NOTE]
> <span data-ttu-id="da86d-830">Při použití `Url.Action` aplikace jsou aktuální hodnoty trasy pro `controller` a `action` určeny pro vás – hodnota `controller` a `action` jsou součástí obou *okolních hodnot* **a** *hodnot*.</span><span class="sxs-lookup"><span data-stu-id="da86d-830">When using `Url.Action`, the current route values for `controller` and `action` are specified for you - the value of `controller` and `action` are part of both *ambient values* **and** *values*.</span></span> <span data-ttu-id="da86d-831">Metoda `Url.Action` vždy používá aktuální hodnoty `action` a `controller` a generuje cestu URL, která směruje na aktuální akci.</span><span class="sxs-lookup"><span data-stu-id="da86d-831">The method `Url.Action`, always uses the current values of `action` and `controller` and will generate a URL path that routes to the current action.</span></span>

<span data-ttu-id="da86d-832">Směrování se pokusí použít hodnoty v ambientních hodnotách k vyplnění informací, které jste nezadali při generování adresy URL.</span><span class="sxs-lookup"><span data-stu-id="da86d-832">Routing attempts to use the values in ambient values to fill in information that you didn't provide when generating a URL.</span></span> <span data-ttu-id="da86d-833">`{a}/{b}/{c}/{d}` `{ a = Alice, b = Bob, c = Carol, d = David }` Směrování má dostatek informací, aby vygenerovala adresu URL bez dalších hodnot, a to s využitím trasy jako a okolních hodnot – protože všechny parametry tras mají hodnotu.</span><span class="sxs-lookup"><span data-stu-id="da86d-833">Using a route like `{a}/{b}/{c}/{d}` and ambient values `{ a = Alice, b = Bob, c = Carol, d = David }`, routing has enough information to generate a URL without any additional values - since all route parameters have a value.</span></span> <span data-ttu-id="da86d-834">Pokud jste přidali hodnotu `{ d = Donovan }` , hodnota by byla `{ d = David }` ignorována a vygenerovaná cesta URL bude `Alice/Bob/Carol/Donovan` .</span><span class="sxs-lookup"><span data-stu-id="da86d-834">If you added the value `{ d = Donovan }`, the value `{ d = David }` would be ignored, and the generated URL path would be `Alice/Bob/Carol/Donovan`.</span></span>

> [!WARNING]
> <span data-ttu-id="da86d-835">Cesty URL jsou hierarchické.</span><span class="sxs-lookup"><span data-stu-id="da86d-835">URL paths are hierarchical.</span></span> <span data-ttu-id="da86d-836">Pokud jste v předchozím příkladu přidali hodnotu `{ c = Cheryl }` , obě hodnoty `{ c = Carol, d = David }` budou ignorovány.</span><span class="sxs-lookup"><span data-stu-id="da86d-836">In the example above, if you added the value `{ c = Cheryl }`, both of the values `{ c = Carol, d = David }` would be ignored.</span></span> <span data-ttu-id="da86d-837">V tomto případě už nemusíte mít hodnotu `d` a generování adresy URL selže.</span><span class="sxs-lookup"><span data-stu-id="da86d-837">In this case we no longer have a value for `d` and URL generation will fail.</span></span> <span data-ttu-id="da86d-838">Je nutné zadat požadovanou hodnotu `c` a `d` .</span><span class="sxs-lookup"><span data-stu-id="da86d-838">You would need to specify the desired value of `c` and `d`.</span></span>  <span data-ttu-id="da86d-839">Můžete očekávat, že se tento problém bude nacházet s výchozí trasou ( `{controller}/{action}/{id?}` ), ale v praxi se toto chování bude chovat zřídka, jako `Url.Action` vždycky explicitně zadáte `controller` `action` hodnotu a.</span><span class="sxs-lookup"><span data-stu-id="da86d-839">You might expect to hit this problem with the default route (`{controller}/{action}/{id?}`) - but you will rarely encounter this behavior in practice as `Url.Action` will always explicitly specify a `controller` and `action` value.</span></span>

<span data-ttu-id="da86d-840">Delší přetížení `Url.Action` také přijímají další objekt *hodnoty trasy* , který poskytuje hodnoty pro parametry směrování jiné než `controller` a `action` .</span><span class="sxs-lookup"><span data-stu-id="da86d-840">Longer overloads of `Url.Action` also take an additional *route values* object to provide values for route parameters other than `controller` and `action`.</span></span> <span data-ttu-id="da86d-841">Nejčastěji se to zobrazí s `id` podobným způsobem `Url.Action("Buy", "Products", new { id = 17 })` .</span><span class="sxs-lookup"><span data-stu-id="da86d-841">You will most commonly see this used with `id` like `Url.Action("Buy", "Products", new { id = 17 })`.</span></span> <span data-ttu-id="da86d-842">Podle konvence objekt *hodnoty trasy* je obvykle objektem anonymního typu, ale může to být také objekt typu " `IDictionary<>` nebo" v *podobě prostého starého objektu .NET*.</span><span class="sxs-lookup"><span data-stu-id="da86d-842">By convention the *route values* object is usually an object of anonymous type, but it can also be an `IDictionary<>` or a *plain old .NET object*.</span></span> <span data-ttu-id="da86d-843">Všechny další hodnoty tras, které neodpovídají parametrům směrování, jsou vloženy do řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="da86d-843">Any additional route values that don't match route parameters are put in the query string.</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/TestController.cs)]

> [!TIP]
> <span data-ttu-id="da86d-844">Chcete-li vytvořit absolutní adresu URL, použijte přetížení, které přijímá `protocol` : `Url.Action("Buy", "Products", new { id = 17 }, protocol: Request.Scheme)`</span><span class="sxs-lookup"><span data-stu-id="da86d-844">To create an absolute URL, use an overload that accepts a `protocol`: `Url.Action("Buy", "Products", new { id = 17 }, protocol: Request.Scheme)`</span></span>

<a name="routing-gen-urls-route-ref-label"></a>

### <a name="generating-urls-by-route"></a><span data-ttu-id="da86d-845">Generování adres URL podle směrování</span><span class="sxs-lookup"><span data-stu-id="da86d-845">Generating URLs by route</span></span>

<span data-ttu-id="da86d-846">Výše uvedený kód ukázal generování adresy URL předáním do kontroleru a názvu akce.</span><span class="sxs-lookup"><span data-stu-id="da86d-846">The code above demonstrated generating a URL by passing in the controller and action name.</span></span> <span data-ttu-id="da86d-847">`IUrlHelper` poskytuje také `Url.RouteUrl` rodinu metod.</span><span class="sxs-lookup"><span data-stu-id="da86d-847">`IUrlHelper` also provides the `Url.RouteUrl` family of methods.</span></span> <span data-ttu-id="da86d-848">Tyto metody jsou podobné `Url.Action` , ale nekopírují aktuální hodnoty `action` a `controller` do hodnot tras.</span><span class="sxs-lookup"><span data-stu-id="da86d-848">These methods are similar to `Url.Action`, but they don't copy the current values of `action` and `controller` to the route values.</span></span> <span data-ttu-id="da86d-849">Nejběžnějším využitím je zadání názvu trasy pro použití konkrétní trasy pro vygenerování adresy URL, a to obecně *bez* zadání kontroleru nebo názvu akce.</span><span class="sxs-lookup"><span data-stu-id="da86d-849">The most common usage is to specify a route name to use a specific route to generate the URL, generally *without* specifying a controller or action name.</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationControllerRouting.cs?name=snippet_1)]

<a name="routing-gen-urls-html-ref-label"></a>

### <a name="generating-urls-in-html"></a><span data-ttu-id="da86d-850">Generování adres URL v HTML</span><span class="sxs-lookup"><span data-stu-id="da86d-850">Generating URLs in HTML</span></span>

<span data-ttu-id="da86d-851">`IHtmlHelper` poskytuje `HtmlHelper` metody `Html.BeginForm` a `Html.ActionLink` ke generování `<form>` a `<a>` prvkům v uvedeném pořadí.</span><span class="sxs-lookup"><span data-stu-id="da86d-851">`IHtmlHelper` provides the `HtmlHelper` methods `Html.BeginForm` and `Html.ActionLink` to generate `<form>` and `<a>` elements respectively.</span></span> <span data-ttu-id="da86d-852">Tyto metody používají `Url.Action` metodu pro vygenerování adresy URL a přijímající podobné argumenty.</span><span class="sxs-lookup"><span data-stu-id="da86d-852">These methods use the `Url.Action` method to generate a URL and they accept similar arguments.</span></span> <span data-ttu-id="da86d-853">`Url.RouteUrl`Doprovodníci pro `HtmlHelper` jsou `Html.BeginRouteForm` a `Html.RouteLink` mají podobné funkce.</span><span class="sxs-lookup"><span data-stu-id="da86d-853">The `Url.RouteUrl` companions for `HtmlHelper` are `Html.BeginRouteForm` and `Html.RouteLink` which have similar functionality.</span></span>

<span data-ttu-id="da86d-854">TagHelpers generuje adresy URL prostřednictvím `form` taghelperu a `<a>` taghelperu.</span><span class="sxs-lookup"><span data-stu-id="da86d-854">TagHelpers generate URLs through the `form` TagHelper and the `<a>` TagHelper.</span></span> <span data-ttu-id="da86d-855">Oba tyto použití `IUrlHelper` při jejich implementaci.</span><span class="sxs-lookup"><span data-stu-id="da86d-855">Both of these use `IUrlHelper` for their implementation.</span></span> <span data-ttu-id="da86d-856">Další informace najdete v tématu [práce s formuláři](../views/working-with-forms.md) .</span><span class="sxs-lookup"><span data-stu-id="da86d-856">See [Working with Forms](../views/working-with-forms.md) for more information.</span></span>

<span data-ttu-id="da86d-857">Uvnitř zobrazení `IUrlHelper` je k dispozici prostřednictvím `Url` vlastnosti pro jakoukoli generaci adres URL ad hoc, která není pokrytá výše.</span><span class="sxs-lookup"><span data-stu-id="da86d-857">Inside views, the `IUrlHelper` is available through the `Url` property for any ad-hoc URL generation not covered by the above.</span></span>

<a name="routing-gen-urls-action-ref-label"></a>

### <a name="generating-urls-in-action-results"></a><span data-ttu-id="da86d-858">Generování adres URL ve výsledcích akcí</span><span class="sxs-lookup"><span data-stu-id="da86d-858">Generating URLS in Action Results</span></span>

<span data-ttu-id="da86d-859">Výše uvedené příklady ukazují použití `IUrlHelper` v kontroleru, zatímco nejběžnější využití v řadiči je vygenerování adresy URL jako součást výsledku akce.</span><span class="sxs-lookup"><span data-stu-id="da86d-859">The examples above have shown using `IUrlHelper` in a controller, while the most common usage in a controller is to generate a URL as part of an action result.</span></span>

<span data-ttu-id="da86d-860">`ControllerBase` `Controller` Základní třídy a poskytují pohodlný způsob pro výsledky akcí, které odkazují na jinou akci.</span><span class="sxs-lookup"><span data-stu-id="da86d-860">The `ControllerBase` and `Controller` base classes provide convenience methods for action results that reference another action.</span></span> <span data-ttu-id="da86d-861">Jedním z typických použití je přesměrování po přijetí vstupu uživatele.</span><span class="sxs-lookup"><span data-stu-id="da86d-861">One typical usage is to redirect after accepting user input.</span></span>

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

<span data-ttu-id="da86d-862">Metody pro vytváření výsledků pro zpracování akcí dodrží podobný vzor pro metody na `IUrlHelper` .</span><span class="sxs-lookup"><span data-stu-id="da86d-862">The action results factory methods follow a similar pattern to the methods on `IUrlHelper`.</span></span>

<a name="routing-dedicated-ref-label"></a>

### <a name="special-case-for-dedicated-conventional-routes"></a><span data-ttu-id="da86d-863">Zvláštní případ pro vyhrazené konvenční trasy</span><span class="sxs-lookup"><span data-stu-id="da86d-863">Special case for dedicated conventional routes</span></span>

<span data-ttu-id="da86d-864">Konvenční směrování může používat speciální druh definice trasy, která se označuje jako *vyhrazená konvenční trasa*.</span><span class="sxs-lookup"><span data-stu-id="da86d-864">Conventional routing can use a special kind of route definition called a *dedicated conventional route*.</span></span> <span data-ttu-id="da86d-865">V následujícím příkladu je trasa s názvem `blog` vyhrazená konvenční trasa.</span><span class="sxs-lookup"><span data-stu-id="da86d-865">In the example below, the route named `blog` is a dedicated conventional route.</span></span>

```csharp
app.UseMvc(routes =>
{
    routes.MapRoute("blog", "blog/{*article}",
        defaults: new { controller = "Blog", action = "Article" });
    routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="da86d-866">Pomocí těchto definic tras se `Url.Action("Index", "Home")` vygeneruje cesta URL `/` s `default` trasou, ale proč?</span><span class="sxs-lookup"><span data-stu-id="da86d-866">Using these route definitions, `Url.Action("Index", "Home")` will generate the URL path `/` with the `default` route, but why?</span></span> <span data-ttu-id="da86d-867">Můžete odhadnout, že hodnoty tras `{ controller = Home, action = Index }` by byly dostatečné pro vygenerování adresy URL pomocí `blog` a výsledek by byl `/blog?action=Index&controller=Home` .</span><span class="sxs-lookup"><span data-stu-id="da86d-867">You might guess the route values `{ controller = Home, action = Index }` would be enough to generate a URL using `blog`, and the result would be `/blog?action=Index&controller=Home`.</span></span>

<span data-ttu-id="da86d-868">Vyhrazené konvenční trasy spoléhají na speciální chování výchozích hodnot, které nemají odpovídající parametr trasy, který brání tomu, aby trasa byla "příliš hlade" s generováním adresy URL.</span><span class="sxs-lookup"><span data-stu-id="da86d-868">Dedicated conventional routes rely on a special behavior of default values that don't have a corresponding route parameter that prevents the route from being "too greedy" with URL generation.</span></span> <span data-ttu-id="da86d-869">V tomto případě výchozí hodnoty jsou `{ controller = Blog, action = Article }` a ani `controller` `action` se neobjeví jako parametr trasy.</span><span class="sxs-lookup"><span data-stu-id="da86d-869">In this case the default values are `{ controller = Blog, action = Article }`, and neither `controller` nor `action` appears as a route parameter.</span></span> <span data-ttu-id="da86d-870">Když směrování provádí generování adresy URL, zadané hodnoty musí odpovídat výchozím hodnotám.</span><span class="sxs-lookup"><span data-stu-id="da86d-870">When routing performs URL generation, the values provided must match the default values.</span></span> <span data-ttu-id="da86d-871">Generování adresy URL pomocí `blog` se nezdaří, protože se hodnoty `{ controller = Home, action = Index }` neshodují `{ controller = Blog, action = Article }` .</span><span class="sxs-lookup"><span data-stu-id="da86d-871">URL generation using `blog` will fail because the values `{ controller = Home, action = Index }` don't match `{ controller = Blog, action = Article }`.</span></span> <span data-ttu-id="da86d-872">Směrování pak vrátí zpět k akci `default` , která bude úspěšná.</span><span class="sxs-lookup"><span data-stu-id="da86d-872">Routing then falls back to try `default`, which succeeds.</span></span>

<a name="routing-areas-ref-label"></a>

## <a name="areas"></a><span data-ttu-id="da86d-873">Oblasti</span><span class="sxs-lookup"><span data-stu-id="da86d-873">Areas</span></span>

<span data-ttu-id="da86d-874">[Oblasti](areas.md) jsou funkce MVC, která slouží k uspořádání souvisejících funkcí do skupiny jako samostatného oboru názvů směrování (pro akce kontroleru) a struktury složek (pro zobrazení).</span><span class="sxs-lookup"><span data-stu-id="da86d-874">[Areas](areas.md) are an MVC feature used to organize related functionality into a group as a separate routing-namespace (for controller actions) and folder structure (for views).</span></span> <span data-ttu-id="da86d-875">Použití oblastí umožňuje aplikaci mít více řadičů se stejným názvem, pokud mají různé *oblasti*.</span><span class="sxs-lookup"><span data-stu-id="da86d-875">Using areas allows an application to have multiple controllers with the same name - as long as they have different *areas*.</span></span> <span data-ttu-id="da86d-876">Použití oblastí vytvoří hierarchii pro účely směrování přidáním dalšího parametru směrování `area` do `controller` a `action` .</span><span class="sxs-lookup"><span data-stu-id="da86d-876">Using areas creates a hierarchy for the purpose of routing by adding another route parameter, `area` to `controller` and `action`.</span></span> <span data-ttu-id="da86d-877">V této části se dozvíte, jak směrování komunikuje s oblastmi – viz [oblasti](areas.md) pro podrobnosti o použití oblastí se zobrazeními.</span><span class="sxs-lookup"><span data-stu-id="da86d-877">This section will discuss how routing interacts with areas - see [Areas](areas.md) for details about how areas are used with views.</span></span>

<span data-ttu-id="da86d-878">V následujícím příkladu je nakonfiguruje MVC na použití výchozí konvenční trasy a *trasy oblasti* pro oblast s názvem `Blog` :</span><span class="sxs-lookup"><span data-stu-id="da86d-878">The following example configures MVC to use the default conventional route and an *area route* for an area named `Blog`:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet1)]

<span data-ttu-id="da86d-879">Při porovnání cesty URL jako `/Manage/Users/AddUser` se v první trase vytvoří hodnoty trasy `{ area = Blog, controller = Users, action = AddUser }` .</span><span class="sxs-lookup"><span data-stu-id="da86d-879">When matching a URL path like `/Manage/Users/AddUser`, the first route will produce the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span> <span data-ttu-id="da86d-880">`area`Hodnota trasy je vytvořena výchozí hodnotou pro `area` , ve skutečnosti je trasa vytvořená pomocí `MapAreaRoute` následujícího ekvivalentu:</span><span class="sxs-lookup"><span data-stu-id="da86d-880">The `area` route value is produced by a default value for `area`, in fact the route created by `MapAreaRoute` is equivalent to the following:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet2)]

<span data-ttu-id="da86d-881">`MapAreaRoute` Vytvoří trasu s použitím výchozí hodnoty a omezení pro `area` použití poskytnutého názvu oblasti v tomto případě `Blog` .</span><span class="sxs-lookup"><span data-stu-id="da86d-881">`MapAreaRoute` creates a route using both a default value and constraint for `area` using the provided area name, in this case `Blog`.</span></span> <span data-ttu-id="da86d-882">Výchozí hodnota zajistí, že trasa vždy vytvoří `{ area = Blog, ... }` , omezení vyžaduje hodnotu `{ area = Blog, ... }` pro generování adresy URL.</span><span class="sxs-lookup"><span data-stu-id="da86d-882">The default value ensures that the route always produces `{ area = Blog, ... }`, the constraint requires the value `{ area = Blog, ... }` for URL generation.</span></span>

> [!TIP]
> <span data-ttu-id="da86d-883">Konvenční směrování je závislé na pořadí.</span><span class="sxs-lookup"><span data-stu-id="da86d-883">Conventional routing is order-dependent.</span></span> <span data-ttu-id="da86d-884">Obecně platí, že trasy s oblastmi by měly být umístěny dříve v tabulce směrování, protože jsou konkrétnější než trasy bez oblasti.</span><span class="sxs-lookup"><span data-stu-id="da86d-884">In general, routes with areas should be placed earlier in the route table as they're more specific than routes without an area.</span></span>

<span data-ttu-id="da86d-885">Pomocí výše uvedeného příkladu budou hodnoty směrování odpovídat následující akci:</span><span class="sxs-lookup"><span data-stu-id="da86d-885">Using the above example, the route values would match the following action:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

<span data-ttu-id="da86d-886">V této `AreaAttribute` části je uvedeno, že kontroler je v rámci oblasti, říkáme, že tento kontroler je v `Blog` oblasti.</span><span class="sxs-lookup"><span data-stu-id="da86d-886">The `AreaAttribute` is what denotes a controller as part of an area, we say that this controller is in the `Blog` area.</span></span> <span data-ttu-id="da86d-887">Řadiče bez `[Area]` atributu nejsou členy žádné oblasti a nebudou se shodovat s tím **not** , kdy `area` je hodnota směrování poskytována směrováním.</span><span class="sxs-lookup"><span data-stu-id="da86d-887">Controllers without an `[Area]` attribute are not members of any area, and will **not** match when the `area` route value is provided by routing.</span></span> <span data-ttu-id="da86d-888">V následujícím příkladu se může shodovat jenom první kontroler, který bude odpovídat hodnotám tras `{ area = Blog, controller = Users, action = AddUser }` .</span><span class="sxs-lookup"><span data-stu-id="da86d-888">In the following example, only the first controller listed can match the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Zebra/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/UsersController.cs)]

> [!NOTE]
> <span data-ttu-id="da86d-889">Obor názvů každého kontroleru se tady zobrazuje pro úplnost – jinak by řadiče vyžadovaly konflikt názvů a generovaly chybu kompilátoru.</span><span class="sxs-lookup"><span data-stu-id="da86d-889">The namespace of each controller is shown here for completeness - otherwise the controllers would have a naming conflict and generate a compiler error.</span></span> <span data-ttu-id="da86d-890">Obory názvů třídy nemají žádný vliv na směrování MVC.</span><span class="sxs-lookup"><span data-stu-id="da86d-890">Class namespaces have no effect on MVC's routing.</span></span>

<span data-ttu-id="da86d-891">První dva řadiče jsou členy oblastí a odpovídají pouze v případě, že je jejich název příslušné oblasti poskytovaný `area` hodnotou trasy.</span><span class="sxs-lookup"><span data-stu-id="da86d-891">The first two controllers are members of areas, and only match when their respective area name is provided by the `area` route value.</span></span> <span data-ttu-id="da86d-892">Třetí kontroler není členem žádné oblasti a může odpovídat jenom v případě, že `area` směrováním neposkytuje žádnou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="da86d-892">The third controller isn't a member of any area, and can only match when no value for `area` is provided by routing.</span></span>

> [!NOTE]
> <span data-ttu-id="da86d-893">V souvislosti s neshodnou *hodnotou* `area` je absence hodnoty stejná, jako kdyby hodnota `area` null nebo prázdný řetězec.</span><span class="sxs-lookup"><span data-stu-id="da86d-893">In terms of matching *no value*, the absence of the `area` value is the same as if the value for `area` were null or the empty string.</span></span>

<span data-ttu-id="da86d-894">Při provádění akce uvnitř oblasti bude hodnota trasy pro `area` směrování k dispozici jako *ambientní hodnota* pro směrování, která se má použít pro generování adresy URL.</span><span class="sxs-lookup"><span data-stu-id="da86d-894">When executing an action inside an area, the route value for `area` will be available as an *ambient value* for routing to use for URL generation.</span></span> <span data-ttu-id="da86d-895">To znamená, že ve výchozím nastavení jsou tyto oblasti pro generování adresy URL *rychlé* , jak je znázorněno v následující ukázce.</span><span class="sxs-lookup"><span data-stu-id="da86d-895">This means that by default areas act *sticky* for URL generation as demonstrated by the following sample.</span></span>
[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet3)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Duck/Controllers/UsersController.cs)]

<a name="iactionconstraint-ref-label"></a>

## <a name="understanding-iactionconstraint"></a><span data-ttu-id="da86d-896">Principy IActionConstraint</span><span class="sxs-lookup"><span data-stu-id="da86d-896">Understanding IActionConstraint</span></span>

> [!NOTE]
> <span data-ttu-id="da86d-897">Tato část je hlubokým podrobněem interních platforem a způsobu, jakým MVC vybírá akci, která se má provést.</span><span class="sxs-lookup"><span data-stu-id="da86d-897">This section is a deep-dive on framework internals and how MVC chooses an action to execute.</span></span> <span data-ttu-id="da86d-898">Typická aplikace nebude potřebovat vlastní. `IActionConstraint`</span><span class="sxs-lookup"><span data-stu-id="da86d-898">A typical application won't need a custom `IActionConstraint`</span></span>

<span data-ttu-id="da86d-899">Pravděpodobně jste již použili, `IActionConstraint` i když nejste obeznámeni s rozhraním.</span><span class="sxs-lookup"><span data-stu-id="da86d-899">You have likely already used `IActionConstraint` even if you're not familiar with the interface.</span></span> <span data-ttu-id="da86d-900">`[HttpGet]`Atribut a podobné `[Http-VERB]` atributy implementují `IActionConstraint` , aby bylo možné omezit provádění metody akce.</span><span class="sxs-lookup"><span data-stu-id="da86d-900">The `[HttpGet]` Attribute and similar `[Http-VERB]` attributes implement `IActionConstraint` in order to limit the execution of an action method.</span></span>

```csharp
public class ProductsController : Controller
{
    [HttpGet]
    public IActionResult Edit() { }

    public IActionResult Edit(...) { }
}
```

<span data-ttu-id="da86d-901">Za předpokladu, že výchozí konvenční trasa, cesta URL `/Products/Edit` by vytvořila hodnoty `{ controller = Products, action = Edit }` , které by odpovídaly **oběma** akcím, které jsou zde uvedeny.</span><span class="sxs-lookup"><span data-stu-id="da86d-901">Assuming the default conventional route, the URL path `/Products/Edit` would produce the values `{ controller = Products, action = Edit }`, which would match **both** of the actions shown here.</span></span> <span data-ttu-id="da86d-902">V `IActionConstraint` terminologii bychom znamenali, že obě tyto akce se považují za kandidáty, protože obě odpovídají datům směrování.</span><span class="sxs-lookup"><span data-stu-id="da86d-902">In `IActionConstraint` terminology we would say that both of these actions are considered candidates - as they both match the route data.</span></span>

<span data-ttu-id="da86d-903">Když se `HttpGetAttribute` spustí, znamená to, že *Edit ()* je odpovídající pro *Get* a neshoduje se s žádným jiným příkazem http.</span><span class="sxs-lookup"><span data-stu-id="da86d-903">When the `HttpGetAttribute` executes, it will say that *Edit()* is a match for *GET* and isn't a match for any other HTTP verb.</span></span> <span data-ttu-id="da86d-904">`Edit(...)`Akce nemá definovány žádné omezení, a proto bude odpovídat jakémukoli příkazu http.</span><span class="sxs-lookup"><span data-stu-id="da86d-904">The `Edit(...)` action doesn't have any constraints defined, and so will match any HTTP verb.</span></span> <span data-ttu-id="da86d-905">Proto za předpokladu, že `POST` `Edit(...)` odpovídají pouze.</span><span class="sxs-lookup"><span data-stu-id="da86d-905">So assuming a `POST` - only `Edit(...)` matches.</span></span> <span data-ttu-id="da86d-906">V případě, že `GET` obě akce stále můžou souhlasit, ale akce s `IActionConstraint` je vždy považována za *lepší* , než je akce bez.</span><span class="sxs-lookup"><span data-stu-id="da86d-906">But, for a `GET` both actions can still match - however, an action with an `IActionConstraint` is always considered *better* than an action without.</span></span> <span data-ttu-id="da86d-907">Proto je to proto, že se `Edit()` `[HttpGet]` považuje za konkrétnější a vybere se, pokud se obě akce můžou shodovat.</span><span class="sxs-lookup"><span data-stu-id="da86d-907">So because `Edit()` has `[HttpGet]` it's considered more specific, and will be selected if both actions can match.</span></span>

<span data-ttu-id="da86d-908">Koncepčně `IActionConstraint` je forma *přetížení*, ale místo přetížení metod se stejným názvem, je přetížení mezi akcemi, které odpovídají stejné adrese URL.</span><span class="sxs-lookup"><span data-stu-id="da86d-908">Conceptually, `IActionConstraint` is a form of *overloading*, but instead of overloading methods with the same name, it's overloading between actions that match the same URL.</span></span> <span data-ttu-id="da86d-909">Směrování atributů používá také `IActionConstraint` a může mít za následek, že se z různých řadičů považují za kandidáty.</span><span class="sxs-lookup"><span data-stu-id="da86d-909">Attribute routing also uses `IActionConstraint` and can result in actions from different controllers both being considered candidates.</span></span>

<a name="iactionconstraint-impl-ref-label"></a>

### <a name="implementing-iactionconstraint"></a><span data-ttu-id="da86d-910">Implementace IActionConstraint</span><span class="sxs-lookup"><span data-stu-id="da86d-910">Implementing IActionConstraint</span></span>

<span data-ttu-id="da86d-911">Nejjednodušší způsob, jak implementovat, `IActionConstraint` je vytvořit třídu odvozenou z `System.Attribute` a umístit ji na vaše akce a řadiče.</span><span class="sxs-lookup"><span data-stu-id="da86d-911">The simplest way to implement an `IActionConstraint` is to create a class derived from `System.Attribute` and place it on your actions and controllers.</span></span> <span data-ttu-id="da86d-912">MVC bude automaticky zjišťovat všechny `IActionConstraint` , které jsou aplikovány jako atributy.</span><span class="sxs-lookup"><span data-stu-id="da86d-912">MVC will automatically discover any `IActionConstraint` that are applied as attributes.</span></span> <span data-ttu-id="da86d-913">Pomocí modelu aplikace můžete použít omezení, což je pravděpodobně nejpružnější přístup, protože umožňuje Metaprogram způsob jejich použití.</span><span class="sxs-lookup"><span data-stu-id="da86d-913">You can use the application model to apply constraints, and this is probably the most flexible approach as it allows you to metaprogram how they're applied.</span></span>

<span data-ttu-id="da86d-914">V následujícím příkladu omezení zvolí akci na základě *kódu země* z údajů o trasách.</span><span class="sxs-lookup"><span data-stu-id="da86d-914">In the following example, a constraint chooses an action based on a *country code* from the route data.</span></span> <span data-ttu-id="da86d-915">[Úplná ukázka na GitHubu](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.ActionConstraintSample.Web/CountrySpecificAttribute.cs).</span><span class="sxs-lookup"><span data-stu-id="da86d-915">The [full sample on GitHub](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.ActionConstraintSample.Web/CountrySpecificAttribute.cs).</span></span>

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

<span data-ttu-id="da86d-916">Zodpovídáte za implementaci `Accept` metody a výběru možnosti Order, aby bylo omezení spuštěno.</span><span class="sxs-lookup"><span data-stu-id="da86d-916">You are responsible for implementing the `Accept` method and choosing an 'Order' for the constraint to execute.</span></span> <span data-ttu-id="da86d-917">V tomto případě se `Accept` Metoda vrátí `true` k označení, že akce odpovídá `country` hodnotě trasy.</span><span class="sxs-lookup"><span data-stu-id="da86d-917">In this case, the `Accept` method returns `true` to denote the action is a match when the `country` route value matches.</span></span> <span data-ttu-id="da86d-918">To se liší od a `RouteValueAttribute` v tom, že umožňuje přechod na akci bez atributu.</span><span class="sxs-lookup"><span data-stu-id="da86d-918">This is different from a `RouteValueAttribute` in that it allows fallback to a non-attributed action.</span></span> <span data-ttu-id="da86d-919">Ukázka ukazuje, že pokud zadáte `en-US` akci, směrové číslo země jako `fr-FR` se vrátí k obecnější kontroleru, který se `[CountrySpecific(...)]` nepoužívá.</span><span class="sxs-lookup"><span data-stu-id="da86d-919">The sample shows that if you define an `en-US` action then a country code like `fr-FR` will fall back to a more generic controller that doesn't have `[CountrySpecific(...)]` applied.</span></span>

<span data-ttu-id="da86d-920">`Order`Vlastnost určuje, ve které *fázi* je omezení součástí.</span><span class="sxs-lookup"><span data-stu-id="da86d-920">The `Order` property decides which *stage* the constraint is part of.</span></span> <span data-ttu-id="da86d-921">Omezení akcí se spouští ve skupinách na základě `Order` .</span><span class="sxs-lookup"><span data-stu-id="da86d-921">Action constraints run in groups based on the `Order`.</span></span> <span data-ttu-id="da86d-922">Například všechny atributy, které poskytuje rozhraní HTTP, používají stejnou `Order` hodnotu tak, aby běžely ve stejné fázi.</span><span class="sxs-lookup"><span data-stu-id="da86d-922">For example, all of the framework provided HTTP method attributes use the same `Order` value so that they run in the same stage.</span></span> <span data-ttu-id="da86d-923">Můžete mít tolik fází, kolik potřebujete k implementaci požadovaných zásad.</span><span class="sxs-lookup"><span data-stu-id="da86d-923">You can have as many stages as you need to implement your desired policies.</span></span>

> [!TIP]
> <span data-ttu-id="da86d-924">Chcete-li se rozhodnout o hodnotě pro `Order` zamyslení nad tím, zda by mělo být vaše omezení použito před metodami http.</span><span class="sxs-lookup"><span data-stu-id="da86d-924">To decide on a value for `Order` think about whether or not your constraint should be applied before HTTP methods.</span></span> <span data-ttu-id="da86d-925">Nižší čísla se spouštějí jako první.</span><span class="sxs-lookup"><span data-stu-id="da86d-925">Lower numbers run first.</span></span>

::: moniker-end
