---
title: Směrování na akce kontroleru v ASP.NET Core
author: rick-anderson
description: Přečtěte si, jak ASP.NET Core MVC používá middleware směrování k párování adres URL příchozích požadavků a jejich mapování na akce.
ms.author: riande
ms.date: 01/24/2019
uid: mvc/controllers/routing
ms.openlocfilehash: a0dbfbe60c151990581b494f81e500fe0b315f55
ms.sourcegitcommit: a166291c6708f5949c417874108332856b53b6a9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/18/2019
ms.locfileid: "72589858"
---
# <a name="routing-to-controller-actions-in-aspnet-core"></a><span data-ttu-id="d1af9-103">Směrování na akce kontroleru v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d1af9-103">Routing to controller actions in ASP.NET Core</span></span>

<span data-ttu-id="d1af9-104">Služba [Ryan Nowak](https://github.com/rynowak) a [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="d1af9-104">By [Ryan Nowak](https://github.com/rynowak) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="d1af9-105">ASP.NET Core MVC používá [middleware](xref:fundamentals/middleware/index) směrování ke spárování adres URL příchozích požadavků a jejich mapování na akce.</span><span class="sxs-lookup"><span data-stu-id="d1af9-105">ASP.NET Core MVC uses the Routing [middleware](xref:fundamentals/middleware/index) to match the URLs of incoming requests and map them to actions.</span></span> <span data-ttu-id="d1af9-106">Trasy jsou definovány ve spouštěcím kódu nebo v atributech.</span><span class="sxs-lookup"><span data-stu-id="d1af9-106">Routes are defined in startup code or attributes.</span></span> <span data-ttu-id="d1af9-107">Trasy popisují, jak by měly být cesty URL odpovídat akcím.</span><span class="sxs-lookup"><span data-stu-id="d1af9-107">Routes describe how URL paths should be matched to actions.</span></span> <span data-ttu-id="d1af9-108">Trasy se také používají ke generování adres URL (pro odkazy) odesílaných v odpovědích.</span><span class="sxs-lookup"><span data-stu-id="d1af9-108">Routes are also used to generate URLs (for links) sent out in responses.</span></span>

<span data-ttu-id="d1af9-109">Akce jsou směrovány buď podle konvence nebo směrovaného atributu.</span><span class="sxs-lookup"><span data-stu-id="d1af9-109">Actions are either conventionally routed or attribute routed.</span></span> <span data-ttu-id="d1af9-110">Umístěním trasy do kontroleru nebo akce se nasměruje atribut IT.</span><span class="sxs-lookup"><span data-stu-id="d1af9-110">Placing a route on the controller or the action makes it attribute routed.</span></span> <span data-ttu-id="d1af9-111">Další informace najdete v tématu [smíšená směrování](#routing-mixed-ref-label) .</span><span class="sxs-lookup"><span data-stu-id="d1af9-111">See [Mixed routing](#routing-mixed-ref-label) for more information.</span></span>

<span data-ttu-id="d1af9-112">Tento dokument vysvětluje interakce mezi MVC a směrováními a jak běžné aplikace MVC využívají funkce směrování.</span><span class="sxs-lookup"><span data-stu-id="d1af9-112">This document will explain the interactions between MVC and routing, and how typical MVC apps make use of routing features.</span></span> <span data-ttu-id="d1af9-113">Podrobnosti o pokročilém směrování najdete v tématu [Směrování](xref:fundamentals/routing) .</span><span class="sxs-lookup"><span data-stu-id="d1af9-113">See [Routing](xref:fundamentals/routing) for details on advanced routing.</span></span>

## <a name="setting-up-routing-middleware"></a><span data-ttu-id="d1af9-114">Nastavení middlewaru směrování</span><span class="sxs-lookup"><span data-stu-id="d1af9-114">Setting up Routing Middleware</span></span>

<span data-ttu-id="d1af9-115">V metodě *Konfigurace* se může zobrazit kód podobný tomuto:</span><span class="sxs-lookup"><span data-stu-id="d1af9-115">In your *Configure* method you may see code similar to:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="d1af9-116">Uvnitř volání `UseMvc` se `MapRoute` používá k vytvoření jedné trasy, na kterou odkazujeme jako na `default` trase.</span><span class="sxs-lookup"><span data-stu-id="d1af9-116">Inside the call to `UseMvc`, `MapRoute` is used to create a single route, which we'll refer to as the `default` route.</span></span> <span data-ttu-id="d1af9-117">Většina aplikací MVC bude používat trasu se šablonou podobnou `default` trase.</span><span class="sxs-lookup"><span data-stu-id="d1af9-117">Most MVC apps will use a route with a template similar to the `default` route.</span></span>

<span data-ttu-id="d1af9-118">@No__t_0 šablony trasy se může shodovat s cestou URL jako `/Products/Details/5` a extrahuje hodnoty trasy `{ controller = Products, action = Details, id = 5 }` cestou tokenizací.</span><span class="sxs-lookup"><span data-stu-id="d1af9-118">The route template `"{controller=Home}/{action=Index}/{id?}"` can match a URL path like `/Products/Details/5` and will extract the route values `{ controller = Products, action = Details, id = 5 }` by tokenizing the path.</span></span> <span data-ttu-id="d1af9-119">MVC se pokusí vyhledat kontroler s názvem `ProductsController` a spustit akci `Details`:</span><span class="sxs-lookup"><span data-stu-id="d1af9-119">MVC will attempt to locate a controller named `ProductsController` and run the action `Details`:</span></span>

```csharp
public class ProductsController : Controller
{
   public IActionResult Details(int id) { ... }
}
```

<span data-ttu-id="d1af9-120">Všimněte si, že v tomto příkladu vazba modelu používá hodnotu `id = 5` k nastavení parametru `id` na `5` při vyvolání této akce.</span><span class="sxs-lookup"><span data-stu-id="d1af9-120">Note that in this example, model binding would use the value of `id = 5` to set the `id` parameter to `5` when invoking this action.</span></span> <span data-ttu-id="d1af9-121">Další podrobnosti najdete v tématu [vazba modelu](../models/model-binding.md) .</span><span class="sxs-lookup"><span data-stu-id="d1af9-121">See the [Model Binding](../models/model-binding.md) for more details.</span></span>

<span data-ttu-id="d1af9-122">Použití trasy `default`:</span><span class="sxs-lookup"><span data-stu-id="d1af9-122">Using the `default` route:</span></span>

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="d1af9-123">Šablona trasy:</span><span class="sxs-lookup"><span data-stu-id="d1af9-123">The route template:</span></span>

* <span data-ttu-id="d1af9-124">`{controller=Home}` definuje jako výchozí `controller` `Home`.</span><span class="sxs-lookup"><span data-stu-id="d1af9-124">`{controller=Home}` defines `Home` as the default `controller`</span></span>

* <span data-ttu-id="d1af9-125">`{action=Index}` definuje jako výchozí `action` `Index`.</span><span class="sxs-lookup"><span data-stu-id="d1af9-125">`{action=Index}` defines `Index` as the default `action`</span></span>

* <span data-ttu-id="d1af9-126">`{id?}` definuje `id` jako volitelné.</span><span class="sxs-lookup"><span data-stu-id="d1af9-126">`{id?}` defines `id` as optional</span></span>

<span data-ttu-id="d1af9-127">Výchozí a volitelné parametry směrování nemusejí být v cestě URL pro porovnávání k dispozici.</span><span class="sxs-lookup"><span data-stu-id="d1af9-127">Default and optional route parameters don't need to be present in the URL path for a match.</span></span> <span data-ttu-id="d1af9-128">Podrobný popis syntaxe šablony směrování naleznete v tématu Referenční dokumentace k [šabloně směrování](../../fundamentals/routing.md#route-template-reference) .</span><span class="sxs-lookup"><span data-stu-id="d1af9-128">See [Route Template Reference](../../fundamentals/routing.md#route-template-reference) for a detailed description of route template syntax.</span></span>

<span data-ttu-id="d1af9-129">`"{controller=Home}/{action=Index}/{id?}"` se může shodovat s cestou adresy URL `/` a vytvoří hodnoty trasy `{ controller = Home, action = Index }`.</span><span class="sxs-lookup"><span data-stu-id="d1af9-129">`"{controller=Home}/{action=Index}/{id?}"` can match the URL path `/` and will produce the route values `{ controller = Home, action = Index }`.</span></span> <span data-ttu-id="d1af9-130">Hodnoty pro `controller` a `action` využívají výchozí hodnoty, `id` nevytvoří hodnotu, protože v cestě URL není žádný odpovídající segment.</span><span class="sxs-lookup"><span data-stu-id="d1af9-130">The values for `controller` and `action` make use of the default values, `id` doesn't produce a value since there's no corresponding segment in the URL path.</span></span> <span data-ttu-id="d1af9-131">MVC použije tyto hodnoty tras k výběru `HomeController` a `Index` akci:</span><span class="sxs-lookup"><span data-stu-id="d1af9-131">MVC would use these route values to select the `HomeController` and `Index` action:</span></span>

```csharp
public class HomeController : Controller
{
  public IActionResult Index() { ... }
}
```

<span data-ttu-id="d1af9-132">Pomocí této definice kontroleru a šablony trasy se spustí akce `HomeController.Index` pro některou z následujících cest URL:</span><span class="sxs-lookup"><span data-stu-id="d1af9-132">Using this controller definition and route template, the `HomeController.Index` action would be executed for any of the following URL paths:</span></span>

* `/Home/Index/17`

* `/Home/Index`

* `/Home`

* `/`

<span data-ttu-id="d1af9-133">Metoda usnadnění `UseMvcWithDefaultRoute`:</span><span class="sxs-lookup"><span data-stu-id="d1af9-133">The convenience method `UseMvcWithDefaultRoute`:</span></span>

```csharp
app.UseMvcWithDefaultRoute();
```

<span data-ttu-id="d1af9-134">Dá se použít k nahrazení:</span><span class="sxs-lookup"><span data-stu-id="d1af9-134">Can be used to replace:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="d1af9-135">`UseMvc` a `UseMvcWithDefaultRoute` přidat instanci `RouterMiddleware` do kanálu middlewaru.</span><span class="sxs-lookup"><span data-stu-id="d1af9-135">`UseMvc` and `UseMvcWithDefaultRoute` add an instance of `RouterMiddleware` to the middleware pipeline.</span></span> <span data-ttu-id="d1af9-136">MVC nekomunikuje přímo se middlewarem a používá směrování pro zpracování požadavků.</span><span class="sxs-lookup"><span data-stu-id="d1af9-136">MVC doesn't interact directly with middleware, and uses routing to handle requests.</span></span> <span data-ttu-id="d1af9-137">MVC je připojeno k trasám prostřednictvím instance `MvcRouteHandler`.</span><span class="sxs-lookup"><span data-stu-id="d1af9-137">MVC is connected to the routes through an instance of `MvcRouteHandler`.</span></span> <span data-ttu-id="d1af9-138">Kód uvnitř `UseMvc` je podobný následujícímu:</span><span class="sxs-lookup"><span data-stu-id="d1af9-138">The code inside of `UseMvc` is similar to the following:</span></span>

```csharp
var routes = new RouteBuilder(app);

// Add connection to MVC, will be hooked up by calls to MapRoute.
routes.DefaultHandler = new MvcRouteHandler(...);

// Execute callback to register routes.
// routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");

// Create route collection and add the middleware.
app.UseRouter(routes.Build());
```

<span data-ttu-id="d1af9-139">`UseMvc` přímo nedefinuje žádné trasy, přidá zástupný symbol do kolekce směrování pro trasu `attribute`.</span><span class="sxs-lookup"><span data-stu-id="d1af9-139">`UseMvc` doesn't directly define any routes, it adds a placeholder to the route collection for the `attribute` route.</span></span> <span data-ttu-id="d1af9-140">Přetížení `UseMvc(Action<IRouteBuilder>)` umožňuje přidat vlastní trasy a zároveň podporuje směrování atributů.</span><span class="sxs-lookup"><span data-stu-id="d1af9-140">The overload `UseMvc(Action<IRouteBuilder>)` lets you add your own routes and also supports attribute routing.</span></span>  <span data-ttu-id="d1af9-141">`UseMvc` a všechny jeho variace přidávají zástupný symbol pro atribut směrování atributu směrování je vždycky dostupný bez ohledu na to, jak nakonfigurujete `UseMvc`.</span><span class="sxs-lookup"><span data-stu-id="d1af9-141">`UseMvc` and all of its variations adds a placeholder for the attribute route - attribute routing is always available regardless of how you configure `UseMvc`.</span></span> <span data-ttu-id="d1af9-142">`UseMvcWithDefaultRoute` definuje výchozí trasu a podporuje směrování atributů.</span><span class="sxs-lookup"><span data-stu-id="d1af9-142">`UseMvcWithDefaultRoute` defines a default route and supports attribute routing.</span></span> <span data-ttu-id="d1af9-143">Oddíl [Směrování atributů](#attribute-routing-ref-label) obsahuje další podrobnosti o směrování atributů.</span><span class="sxs-lookup"><span data-stu-id="d1af9-143">The [Attribute Routing](#attribute-routing-ref-label) section includes more details on attribute routing.</span></span>

<a name="routing-conventional-ref-label"></a>

## <a name="conventional-routing"></a><span data-ttu-id="d1af9-144">Konvenční směrování</span><span class="sxs-lookup"><span data-stu-id="d1af9-144">Conventional routing</span></span>

<span data-ttu-id="d1af9-145">Trasa `default`:</span><span class="sxs-lookup"><span data-stu-id="d1af9-145">The `default` route:</span></span>

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="d1af9-146">je příkladem *konvenčního směrování*.</span><span class="sxs-lookup"><span data-stu-id="d1af9-146">is an example of a *conventional routing*.</span></span> <span data-ttu-id="d1af9-147">Tento styl se nazývá *konvenční směrování* , protože vytváří *konvenci* pro cesty URL:</span><span class="sxs-lookup"><span data-stu-id="d1af9-147">We call this style *conventional routing* because it establishes a *convention* for URL paths:</span></span>

* <span data-ttu-id="d1af9-148">první segment cesty se mapuje na název kontroleru.</span><span class="sxs-lookup"><span data-stu-id="d1af9-148">the first path segment maps to the controller name</span></span>

* <span data-ttu-id="d1af9-149">druhý se mapuje na název akce.</span><span class="sxs-lookup"><span data-stu-id="d1af9-149">the second maps to the action name.</span></span>

* <span data-ttu-id="d1af9-150">Třetí segment se používá pro volitelný `id`, který se používá k mapování na entitu modelu.</span><span class="sxs-lookup"><span data-stu-id="d1af9-150">the third segment is used for an optional `id` used to map to a model entity</span></span>

<span data-ttu-id="d1af9-151">Pomocí této `default` trasy adresa URL `/Products/List` mapuje na `ProductsController.List` akci a `/Blog/Article/17` mapuje na `BlogController.Article`.</span><span class="sxs-lookup"><span data-stu-id="d1af9-151">Using this `default` route, the URL path `/Products/List` maps to the `ProductsController.List` action, and `/Blog/Article/17` maps to `BlogController.Article`.</span></span> <span data-ttu-id="d1af9-152">Toto mapování je založené **pouze** na názvech kontroléru a akce a není založené na oborech názvů, umístěních zdrojového souboru nebo parametrech metody.</span><span class="sxs-lookup"><span data-stu-id="d1af9-152">This mapping is based on the controller and action names **only** and isn't based on namespaces, source file locations, or method parameters.</span></span>

> [!TIP]
> <span data-ttu-id="d1af9-153">Použití konvenčního směrování s výchozí trasou vám umožní rychle vytvořit aplikaci, aniž by se musela používat nový vzor adresy URL pro každou akci, kterou definujete.</span><span class="sxs-lookup"><span data-stu-id="d1af9-153">Using conventional routing with the default route allows you to build the application quickly without having to come up with a new URL pattern for each action you define.</span></span> <span data-ttu-id="d1af9-154">Pro aplikace s akcemi stylu CRUD, které mají konzistenci pro adresy URL napříč řadiči, může zjednodušit váš kód a zajistit větší předvídatelné uživatelské rozhraní.</span><span class="sxs-lookup"><span data-stu-id="d1af9-154">For an application with CRUD style actions, having consistency for the URLs across your controllers can help simplify your code and make your UI more predictable.</span></span>

> [!WARNING]
> <span data-ttu-id="d1af9-155">@No__t_0 je definována jako volitelná šablonou směrování, což znamená, že se akce můžou provádět bez ID, které je součástí adresy URL.</span><span class="sxs-lookup"><span data-stu-id="d1af9-155">The `id` is defined as optional by the route template, meaning that your actions can execute without the ID provided as part of the URL.</span></span> <span data-ttu-id="d1af9-156">Obvykle k tomu, co se stane, když se `id` z adresy URL vynechá, znamená to, že bude nastavená na `0` podle vazby modelu a v důsledku toho se v databázi odpovídající `id == 0` nenajde žádná entita.</span><span class="sxs-lookup"><span data-stu-id="d1af9-156">Usually what will happen if `id` is omitted from the URL is that it will be set to `0` by model binding, and as a result no entity will be found in the database matching `id == 0`.</span></span> <span data-ttu-id="d1af9-157">Směrování atributů vám může poskytnout jemně odstupňovaný ovládací prvek, který umožňuje, aby se ID vyžadovalo u některých akcí, a ne pro ostatní.</span><span class="sxs-lookup"><span data-stu-id="d1af9-157">Attribute routing can give you fine-grained control to make the ID required for some actions and not for others.</span></span> <span data-ttu-id="d1af9-158">Podle konvence bude dokumentace obsahovat volitelné parametry, jako `id`, když se budou pravděpodobně zobrazovat ve správném použití.</span><span class="sxs-lookup"><span data-stu-id="d1af9-158">By convention the documentation will include optional parameters like `id` when they're likely to appear in correct usage.</span></span>

## <a name="multiple-routes"></a><span data-ttu-id="d1af9-159">Více tras</span><span class="sxs-lookup"><span data-stu-id="d1af9-159">Multiple routes</span></span>

<span data-ttu-id="d1af9-160">Přidáním dalších volání do `MapRoute` můžete do `UseMvc` přidat více tras.</span><span class="sxs-lookup"><span data-stu-id="d1af9-160">You can add multiple routes inside `UseMvc` by adding more calls to `MapRoute`.</span></span> <span data-ttu-id="d1af9-161">V takovém případě můžete definovat více konvencí nebo přidat konvenční trasy, které jsou vyhrazeny určité akci, například:</span><span class="sxs-lookup"><span data-stu-id="d1af9-161">Doing so allows you to define multiple conventions, or to add conventional routes that are dedicated to a specific action, such as:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("blog", "blog/{*article}",
            defaults: new { controller = "Blog", action = "Article" });
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="d1af9-162">@No__t_0 trasa je to *vyhrazená konvenční trasa*, což znamená, že používá konvenční směrovací systém, ale je vyhrazená konkrétní akci.</span><span class="sxs-lookup"><span data-stu-id="d1af9-162">The `blog` route here is a *dedicated conventional route*, meaning that it uses the conventional routing system, but is dedicated to a specific action.</span></span> <span data-ttu-id="d1af9-163">Vzhledem k tomu, že `controller` a `action` se v šabloně směrování nezobrazují jako parametry, můžou mít jenom výchozí hodnoty, takže tato trasa se vždycky namapuje na `BlogController.Article` akcí.</span><span class="sxs-lookup"><span data-stu-id="d1af9-163">Since `controller` and `action` don't appear in the route template as parameters, they can only have the default values, and thus this route will always map to the action `BlogController.Article`.</span></span>

<span data-ttu-id="d1af9-164">Trasy v kolekci tras jsou seřazené a budou zpracovávány v pořadí, v jakém byly přidány.</span><span class="sxs-lookup"><span data-stu-id="d1af9-164">Routes in the route collection are ordered, and will be processed in the order they're added.</span></span> <span data-ttu-id="d1af9-165">Takže v tomto příkladu bude `blog` trasa vyzkoušena před trasou `default`.</span><span class="sxs-lookup"><span data-stu-id="d1af9-165">So in this example, the `blog` route will be tried before the `default` route.</span></span>

> [!NOTE]
> <span data-ttu-id="d1af9-166">*Vyhrazené konvenční trasy* často používají zachycení všech parametrů tras, jako je `{*article}`, k zachycení zbývající části cesty URL.</span><span class="sxs-lookup"><span data-stu-id="d1af9-166">*Dedicated conventional routes* often use catch-all route parameters like `{*article}` to capture the remaining portion of the URL path.</span></span> <span data-ttu-id="d1af9-167">To může vést k tomu, že trasa "je příliš hlade", což znamená, že odpovídá adresám URL, které mají odpovídat jiným trasám.</span><span class="sxs-lookup"><span data-stu-id="d1af9-167">This can make a route 'too greedy' meaning that it matches URLs that you intended to be matched by other routes.</span></span> <span data-ttu-id="d1af9-168">Pokud to chcete vyřešit, umístěte trasy "hladce" do směrovací tabulky později.</span><span class="sxs-lookup"><span data-stu-id="d1af9-168">Put the 'greedy' routes later in the route table to solve this.</span></span>

### <a name="fallback"></a><span data-ttu-id="d1af9-169">Nalezení</span><span class="sxs-lookup"><span data-stu-id="d1af9-169">Fallback</span></span>

<span data-ttu-id="d1af9-170">V rámci zpracování žádosti MVC ověří, že hodnoty trasy lze použít k vyhledání kontroleru a akce v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="d1af9-170">As part of request processing, MVC will verify that the route values can be used to find a controller and action in your application.</span></span> <span data-ttu-id="d1af9-171">Pokud se hodnoty trasy neshodují s akcí, trasa není považována za shodnou a bude se opakovat další trasa.</span><span class="sxs-lookup"><span data-stu-id="d1af9-171">If the route values don't match an action then the route isn't considered a match, and the next route will be tried.</span></span> <span data-ttu-id="d1af9-172">Označuje se jako *záložní*a je určený pro zjednodušení případů, kdy se konvenční trasy překrývají.</span><span class="sxs-lookup"><span data-stu-id="d1af9-172">This is called *fallback*, and it's intended to simplify cases where conventional routes overlap.</span></span>

### <a name="disambiguating-actions"></a><span data-ttu-id="d1af9-173">Nejednoznačnost akcí</span><span class="sxs-lookup"><span data-stu-id="d1af9-173">Disambiguating actions</span></span>

<span data-ttu-id="d1af9-174">Pokud se dvě akce shodují přes směrování, MVC musí nejednoznačně vybrat kandidáta na nejlepší nebo jinak vyvolat výjimku.</span><span class="sxs-lookup"><span data-stu-id="d1af9-174">When two actions match through routing, MVC must disambiguate to choose the 'best' candidate or else throw an exception.</span></span> <span data-ttu-id="d1af9-175">Příklad:</span><span class="sxs-lookup"><span data-stu-id="d1af9-175">For example:</span></span>

```csharp
public class ProductsController : Controller
{
   public IActionResult Edit(int id) { ... }

   [HttpPost]
   public IActionResult Edit(int id, Product product) { ... }
}
```

<span data-ttu-id="d1af9-176">Tento kontroler definuje dvě akce, které by odpovídaly cestě URL `/Products/Edit/17` a `{ controller = Products, action = Edit, id = 17 }` dat směrování.</span><span class="sxs-lookup"><span data-stu-id="d1af9-176">This controller defines two actions that would match the URL path `/Products/Edit/17` and route data `{ controller = Products, action = Edit, id = 17 }`.</span></span> <span data-ttu-id="d1af9-177">Toto je typický vzor pro řadiče MVC, kde `Edit(int)` zobrazuje formulář pro úpravu produktu a `Edit(int, Product)` zpracovává vystavený formulář.</span><span class="sxs-lookup"><span data-stu-id="d1af9-177">This is a typical pattern for MVC controllers where `Edit(int)` shows a form to edit a product, and `Edit(int, Product)` processes  the posted form.</span></span> <span data-ttu-id="d1af9-178">Aby tato možnost MVC mohla být v případě, že se jedná o jinou akci než příkaz HTTP, musela zvolit `Edit(int, Product)`, když je žádost `POST` HTTP a `Edit(int)`.</span><span class="sxs-lookup"><span data-stu-id="d1af9-178">To make this possible MVC would need to choose `Edit(int, Product)` when the request is an HTTP `POST` and `Edit(int)` when the HTTP verb is anything else.</span></span>

<span data-ttu-id="d1af9-179">@No__t_0 (`[HttpPost]`) je implementace `IActionConstraint`, která umožňuje, aby byla akce vybrána pouze v případě, že je `POST` příkaz HTTP.</span><span class="sxs-lookup"><span data-stu-id="d1af9-179">The `HttpPostAttribute` ( `[HttpPost]` ) is an implementation of `IActionConstraint` that will only allow the action to be selected when the HTTP verb is `POST`.</span></span> <span data-ttu-id="d1af9-180">Přítomnost `IActionConstraint` nastaví `Edit(int, Product)` "lepší", než `Edit(int)`, takže se `Edit(int, Product)` vyzkouší jako první.</span><span class="sxs-lookup"><span data-stu-id="d1af9-180">The presence of an `IActionConstraint` makes the `Edit(int, Product)` a 'better' match than `Edit(int)`, so `Edit(int, Product)` will be tried first.</span></span>

<span data-ttu-id="d1af9-181">Stačí napsat vlastní implementace `IActionConstraint` ve specializovaných scénářích, ale je důležité pochopit, že role atributů, jako jsou `HttpPostAttribute` podobné atributy, jsou definovány pro jiné příkazy HTTP.</span><span class="sxs-lookup"><span data-stu-id="d1af9-181">You will only need to write custom `IActionConstraint` implementations in specialized scenarios, but it's important to understand the role of attributes like `HttpPostAttribute`  - similar attributes are defined for other HTTP verbs.</span></span> <span data-ttu-id="d1af9-182">V konvenčním směrování je běžné, že pokud jsou součástí pracovního postupu `show form -> submit form`, je běžné použít stejný název akce.</span><span class="sxs-lookup"><span data-stu-id="d1af9-182">In conventional routing it's common for actions to use the same action name when they're part of a `show form -> submit form` workflow.</span></span> <span data-ttu-id="d1af9-183">Pohodlí tohoto vzoru se po kontrole části [porozumění IActionConstraint](#understanding-iactionconstraint) projeví.</span><span class="sxs-lookup"><span data-stu-id="d1af9-183">The convenience of this pattern will become more apparent after reviewing the [Understanding IActionConstraint](#understanding-iactionconstraint) section.</span></span>

<span data-ttu-id="d1af9-184">Pokud se více tras shodují a MVC nemůže najít nejlepší trasu, vyvolá `AmbiguousActionException`.</span><span class="sxs-lookup"><span data-stu-id="d1af9-184">If multiple routes match, and MVC can't find a 'best' route, it will throw an `AmbiguousActionException`.</span></span>

<a name="routing-route-name-ref-label"></a>

### <a name="route-names"></a><span data-ttu-id="d1af9-185">Názvy tras</span><span class="sxs-lookup"><span data-stu-id="d1af9-185">Route names</span></span>

<span data-ttu-id="d1af9-186">Řetězce `"blog"` a `"default"` v následujících příkladech jsou názvy tras:</span><span class="sxs-lookup"><span data-stu-id="d1af9-186">The strings  `"blog"` and `"default"` in the following examples are route names:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("blog", "blog/{*article}",
               defaults: new { controller = "Blog", action = "Article" });
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="d1af9-187">Názvy tras směrují logický název tak, aby se pojmenovaná trasa dala použít pro generování adresy URL.</span><span class="sxs-lookup"><span data-stu-id="d1af9-187">The route names give the route a logical name so that the named route can be used for URL generation.</span></span> <span data-ttu-id="d1af9-188">Tím se značně zjednodušuje vytváření adres URL, když řazení tras může způsobit komplikované generování adresy URL.</span><span class="sxs-lookup"><span data-stu-id="d1af9-188">This greatly simplifies URL creation when the ordering of routes could make URL generation complicated.</span></span> <span data-ttu-id="d1af9-189">Názvy tras musí být jedinečné aplikace v rozsahu.</span><span class="sxs-lookup"><span data-stu-id="d1af9-189">Route names must be unique application-wide.</span></span>

<span data-ttu-id="d1af9-190">Názvy tras nemají žádný vliv na adresy URL, které by odpovídaly ani manipulaci s požadavky. používají se pouze pro generování adresy URL.</span><span class="sxs-lookup"><span data-stu-id="d1af9-190">Route names have no impact on URL matching or handling of requests; they're used only for URL generation.</span></span> <span data-ttu-id="d1af9-191">[Směrování](xref:fundamentals/routing) obsahuje podrobnější informace o generování adresy URL včetně generování adresy URL v pomocníkech specifických pro MVC.</span><span class="sxs-lookup"><span data-stu-id="d1af9-191">[Routing](xref:fundamentals/routing) has more detailed information on URL generation including URL generation in MVC-specific helpers.</span></span>

<a name="attribute-routing-ref-label"></a>

## <a name="attribute-routing"></a><span data-ttu-id="d1af9-192">Směrování atributů</span><span class="sxs-lookup"><span data-stu-id="d1af9-192">Attribute routing</span></span>

<span data-ttu-id="d1af9-193">Směrování atributů používá sadu atributů k mapování akcí přímo na šablony směrování.</span><span class="sxs-lookup"><span data-stu-id="d1af9-193">Attribute routing uses a set of attributes to map actions directly to route templates.</span></span> <span data-ttu-id="d1af9-194">V následujícím příkladu se `app.UseMvc();` používá v metodě `Configure` a není předána žádná trasa.</span><span class="sxs-lookup"><span data-stu-id="d1af9-194">In the following example, `app.UseMvc();` is used in the `Configure` method and no route is passed.</span></span> <span data-ttu-id="d1af9-195">@No__t_0 se bude shodovat s množinou adres URL podobných tomu, co by se výchozí trasa `{controller=Home}/{action=Index}/{id?}` shodovala:</span><span class="sxs-lookup"><span data-stu-id="d1af9-195">The `HomeController` will match a set of URLs similar to what the default route `{controller=Home}/{action=Index}/{id?}` would match:</span></span>

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

<span data-ttu-id="d1af9-196">Akce `HomeController.Index()` se spustí pro všechny cesty URL `/`, `/Home` nebo `/Home/Index`.</span><span class="sxs-lookup"><span data-stu-id="d1af9-196">The `HomeController.Index()` action will be executed for any of the URL paths `/`, `/Home`, or `/Home/Index`.</span></span>

> [!NOTE]
> <span data-ttu-id="d1af9-197">V tomto příkladu se zvýrazní klíčový rozdíl mezi směrováním atributů a konvenčním směrováním.</span><span class="sxs-lookup"><span data-stu-id="d1af9-197">This example highlights a key programming difference between attribute routing and conventional routing.</span></span> <span data-ttu-id="d1af9-198">Směrování atributů vyžaduje více vstupu pro určení trasy; konvenční výchozí trasa zpracovává trasy více stručně.</span><span class="sxs-lookup"><span data-stu-id="d1af9-198">Attribute routing requires more input to specify a route; the conventional default route handles routes more succinctly.</span></span> <span data-ttu-id="d1af9-199">Směrování atributů ale povoluje (a vyžaduje) přesnou kontrolu nad tím, které šablony směrování se vztahují na každou akci.</span><span class="sxs-lookup"><span data-stu-id="d1af9-199">However, attribute routing allows (and requires) precise control of which route templates apply to each action.</span></span>

<span data-ttu-id="d1af9-200">Při směrování atributů název kontroleru a názvy akcí nehraje **žádná** role, ve které je vybrána akce.</span><span class="sxs-lookup"><span data-stu-id="d1af9-200">With attribute routing the controller name and action names play **no** role in which action is selected.</span></span> <span data-ttu-id="d1af9-201">Tento příklad bude odpovídat stejným adresám URL jako v předchozím příkladu.</span><span class="sxs-lookup"><span data-stu-id="d1af9-201">This example will match the same URLs as the previous example.</span></span>

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
> <span data-ttu-id="d1af9-202">Výše uvedené šablony tras nedefinují parametry směrování pro `action`, `area` a `controller`.</span><span class="sxs-lookup"><span data-stu-id="d1af9-202">The route templates above don't define route parameters for `action`, `area`, and `controller`.</span></span> <span data-ttu-id="d1af9-203">Ve skutečnosti nejsou tyto parametry trasy v trasách atributů povoleny.</span><span class="sxs-lookup"><span data-stu-id="d1af9-203">In fact, these route parameters are not allowed in attribute routes.</span></span> <span data-ttu-id="d1af9-204">Vzhledem k tomu, že šablona trasy je již k akci přidružena, by nedošlo k tomu, že by bylo vhodné analyzovat název akce z adresy URL.</span><span class="sxs-lookup"><span data-stu-id="d1af9-204">Since the route template is already associated with an action, it wouldn't make sense to parse the action name from the URL.</span></span>

## <a name="attribute-routing-with-httpverb-attributes"></a><span data-ttu-id="d1af9-205">Směrování atributů pomocí atributů http [příkaz]</span><span class="sxs-lookup"><span data-stu-id="d1af9-205">Attribute routing with Http[Verb] attributes</span></span>

<span data-ttu-id="d1af9-206">Směrování atributů může také používat atributy `Http[Verb]`, jako je například `HttpPostAttribute`.</span><span class="sxs-lookup"><span data-stu-id="d1af9-206">Attribute routing can also make use of the `Http[Verb]` attributes such as `HttpPostAttribute`.</span></span> <span data-ttu-id="d1af9-207">Všechny tyto atributy mohou přijmout šablonu směrování.</span><span class="sxs-lookup"><span data-stu-id="d1af9-207">All of these attributes can accept a route template.</span></span> <span data-ttu-id="d1af9-208">Tento příklad ukazuje dvě akce, které odpovídají stejné šabloně směrování:</span><span class="sxs-lookup"><span data-stu-id="d1af9-208">This example shows two actions that match the same route template:</span></span>

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

<span data-ttu-id="d1af9-209">V případě cesty URL, jako je `/products` akce `ProductsApi.ListProducts` se spustí, když je příkaz HTTP `GET` a `ProductsApi.CreateProduct` se spustí, když se příkaz HTTP `POST`.</span><span class="sxs-lookup"><span data-stu-id="d1af9-209">For a URL path like `/products` the `ProductsApi.ListProducts` action will be executed when the HTTP verb is `GET` and `ProductsApi.CreateProduct` will be executed when the HTTP verb is `POST`.</span></span> <span data-ttu-id="d1af9-210">Směrování atributů první odpovídá adrese URL oproti sadě šablon směrování definovaných atributy směrování.</span><span class="sxs-lookup"><span data-stu-id="d1af9-210">Attribute routing first matches the URL against the set of route templates defined by route attributes.</span></span> <span data-ttu-id="d1af9-211">Po porovnání šablony trasy se použijí omezení `IActionConstraint` pro určení akcí, které lze provést.</span><span class="sxs-lookup"><span data-stu-id="d1af9-211">Once a route template matches, `IActionConstraint` constraints are applied to determine which actions can be executed.</span></span>

> [!TIP]
> <span data-ttu-id="d1af9-212">Při sestavování REST API je zřídka pravděpodobné, že budete chtít použít `[Route(...)]` na metodě akce, protože akce přijme všechny metody HTTP.</span><span class="sxs-lookup"><span data-stu-id="d1af9-212">When building a REST API, it's rare that you will want to use `[Route(...)]` on an action method as the action will accept all HTTP methods.</span></span> <span data-ttu-id="d1af9-213">Je lepší používat přesnější `Http*Verb*Attributes`, abyste měli přesně informace o tom, co vaše rozhraní API podporuje.</span><span class="sxs-lookup"><span data-stu-id="d1af9-213">It's better to use the more specific `Http*Verb*Attributes` to be precise about what your API supports.</span></span> <span data-ttu-id="d1af9-214">Očekává se, že klienti rozhraní REST API znají, které cesty a příkazy HTTP se mapují na konkrétní logické operace.</span><span class="sxs-lookup"><span data-stu-id="d1af9-214">Clients of REST APIs are expected to know what paths and HTTP verbs map to specific logical operations.</span></span>

<span data-ttu-id="d1af9-215">Vzhledem k tomu, že trasa atributu se vztahuje na konkrétní akci, je snadné vytvořit parametry požadované v rámci definice šablony trasy.</span><span class="sxs-lookup"><span data-stu-id="d1af9-215">Since an attribute route applies to a specific action, it's easy to make parameters required as part of the route template definition.</span></span> <span data-ttu-id="d1af9-216">V tomto příkladu je `id` nutné jako součást cesty URL.</span><span class="sxs-lookup"><span data-stu-id="d1af9-216">In this example, `id` is required as part of the URL path.</span></span>

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

<span data-ttu-id="d1af9-217">Akce `ProductsApi.GetProduct(int)` se spustí pro cestu URL, jako je `/products/3`, ale ne pro cestu URL, jako je `/products`.</span><span class="sxs-lookup"><span data-stu-id="d1af9-217">The `ProductsApi.GetProduct(int)` action will be executed for a URL path like `/products/3` but not for a URL path like `/products`.</span></span> <span data-ttu-id="d1af9-218">Úplný popis šablon směrování a souvisejících možností najdete v tématu věnovaném [Směrování](../../fundamentals/routing.md) .</span><span class="sxs-lookup"><span data-stu-id="d1af9-218">See [Routing](../../fundamentals/routing.md) for a full description of route templates and related options.</span></span>

## <a name="route-name"></a><span data-ttu-id="d1af9-219">Název trasy</span><span class="sxs-lookup"><span data-stu-id="d1af9-219">Route Name</span></span>

<span data-ttu-id="d1af9-220">Následující kód definuje *název trasy* `Products_List`:</span><span class="sxs-lookup"><span data-stu-id="d1af9-220">The following code  defines a *route name* of `Products_List`:</span></span>

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

<span data-ttu-id="d1af9-221">Názvy tras se dají použít k vygenerování adresy URL na základě konkrétní trasy.</span><span class="sxs-lookup"><span data-stu-id="d1af9-221">Route names can be used to generate a URL based on a specific route.</span></span> <span data-ttu-id="d1af9-222">Názvy tras nemají žádný vliv na adresu URL, která odpovídá chování směrování a používají se jenom pro generování adresy URL.</span><span class="sxs-lookup"><span data-stu-id="d1af9-222">Route names have no impact on the URL matching behavior of routing and are only used for URL generation.</span></span> <span data-ttu-id="d1af9-223">Názvy tras musí být jedinečné aplikace v rozsahu.</span><span class="sxs-lookup"><span data-stu-id="d1af9-223">Route names must be unique application-wide.</span></span>

> [!NOTE]
> <span data-ttu-id="d1af9-224">Na rozdíl od konvenční *výchozí trasy*definuje parametr `id` jako volitelné (`{id?}`).</span><span class="sxs-lookup"><span data-stu-id="d1af9-224">Contrast this with the conventional *default route*, which defines the `id` parameter as optional (`{id?}`).</span></span> <span data-ttu-id="d1af9-225">Tato možnost přesného určení rozhraní API má výhody, jako je například povolení `/products` a `/products/5` odeslání do různých akcí.</span><span class="sxs-lookup"><span data-stu-id="d1af9-225">This ability to precisely specify APIs has advantages, such as  allowing `/products` and `/products/5` to be dispatched to different actions.</span></span>

<a name="routing-combining-ref-label"></a>

### <a name="combining-routes"></a><span data-ttu-id="d1af9-226">Kombinování tras</span><span class="sxs-lookup"><span data-stu-id="d1af9-226">Combining routes</span></span>

<span data-ttu-id="d1af9-227">Aby bylo směrování atributů méně opakované, jsou atributy směrování na kontroleru kombinovány s atributy směrování na jednotlivých akcích.</span><span class="sxs-lookup"><span data-stu-id="d1af9-227">To make attribute routing less repetitive, route attributes on the controller are combined with route attributes on the individual actions.</span></span> <span data-ttu-id="d1af9-228">Každá šablona trasy definovaná na řadiči je předá směrování šablon na akcích.</span><span class="sxs-lookup"><span data-stu-id="d1af9-228">Any route templates defined on the controller are prepended to route templates on the actions.</span></span> <span data-ttu-id="d1af9-229">Umístěním atributu směrování na řadiči se vytvoří **všechny** akce v řadiči použít směrování atributů.</span><span class="sxs-lookup"><span data-stu-id="d1af9-229">Placing a route attribute on the controller makes **all** actions in the controller use attribute routing.</span></span>

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

<span data-ttu-id="d1af9-230">V tomto příkladu se cesta URL `/products` může shodovat `ProductsApi.ListProducts` a cesta URL `/products/5` může odpovídat `ProductsApi.GetProduct(int)`.</span><span class="sxs-lookup"><span data-stu-id="d1af9-230">In this example the URL path `/products` can match `ProductsApi.ListProducts`, and the URL path `/products/5` can match `ProductsApi.GetProduct(int)`.</span></span> <span data-ttu-id="d1af9-231">Obě tyto akce odpovídají pouze `GET` HTTP, protože jsou upraveny pomocí `HttpGetAttribute`.</span><span class="sxs-lookup"><span data-stu-id="d1af9-231">Both of these actions only match HTTP `GET` because they're decorated with the `HttpGetAttribute`.</span></span>

<span data-ttu-id="d1af9-232">Šablony směrování použité pro akci, která začíná na `/` nebo `~/` se nekombinují se šablonami směrování použitými pro kontroler.</span><span class="sxs-lookup"><span data-stu-id="d1af9-232">Route templates applied to an action that begin with `/` or `~/` don't get combined with route templates applied to the controller.</span></span> <span data-ttu-id="d1af9-233">Tento příklad odpovídá sadě cest URL podobně jako *výchozí trasa*.</span><span class="sxs-lookup"><span data-stu-id="d1af9-233">This example matches a set of URL paths similar to the *default route*.</span></span>

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

### <a name="ordering-attribute-routes"></a><span data-ttu-id="d1af9-234">Směrování atributů řazení</span><span class="sxs-lookup"><span data-stu-id="d1af9-234">Ordering attribute routes</span></span>

<span data-ttu-id="d1af9-235">Na rozdíl od konvenčních tras, které provádějí v definovaném pořadí, směrování atributů vytvoří strom a porovnává všechny trasy současně.</span><span class="sxs-lookup"><span data-stu-id="d1af9-235">In contrast to conventional routes which execute in a defined order, attribute routing builds a tree and matches all routes simultaneously.</span></span> <span data-ttu-id="d1af9-236">To se chová jako – Pokud byly položky trasy umístěny v ideálním pořadí; nejvíce konkrétní trasy mají možnost provést před obecnější trasy.</span><span class="sxs-lookup"><span data-stu-id="d1af9-236">This behaves as-if the route entries were placed in an ideal ordering; the most specific routes have a chance to execute before the more general routes.</span></span>

<span data-ttu-id="d1af9-237">Například trasa jako `blog/search/{topic}` je konkrétnější než trasa, jako je `blog/{*article}`.</span><span class="sxs-lookup"><span data-stu-id="d1af9-237">For example, a route like `blog/search/{topic}` is more specific than a route like `blog/{*article}`.</span></span> <span data-ttu-id="d1af9-238">Logicky mluví nejprve `blog/search/{topic}` Route "běžel", ve výchozím nastavení, protože se jedná o jediné řazení rozumné.</span><span class="sxs-lookup"><span data-stu-id="d1af9-238">Logically speaking the `blog/search/{topic}` route 'runs' first, by default, because that's the only sensible ordering.</span></span> <span data-ttu-id="d1af9-239">Pomocí konvenčního směrování zodpovídá vývojář za umístění tras v požadovaném pořadí.</span><span class="sxs-lookup"><span data-stu-id="d1af9-239">Using conventional routing, the developer is  responsible for placing routes in the desired order.</span></span>

<span data-ttu-id="d1af9-240">Trasy atributů mohou konfigurovat objednávku pomocí vlastnosti `Order` všech rozhraní, které poskytují atributy směrování.</span><span class="sxs-lookup"><span data-stu-id="d1af9-240">Attribute routes can configure an order, using the `Order` property of all of the framework provided route attributes.</span></span> <span data-ttu-id="d1af9-241">Trasy jsou zpracovávány podle vzestupného řazení vlastnosti `Order`.</span><span class="sxs-lookup"><span data-stu-id="d1af9-241">Routes are processed according to an ascending sort of the `Order` property.</span></span> <span data-ttu-id="d1af9-242">Výchozí pořadí je `0`.</span><span class="sxs-lookup"><span data-stu-id="d1af9-242">The default order is `0`.</span></span> <span data-ttu-id="d1af9-243">Nastavení trasy pomocí `Order = -1` spustí před trasami, které nenastaví objednávku.</span><span class="sxs-lookup"><span data-stu-id="d1af9-243">Setting a route using `Order = -1` will run before routes that don't set an order.</span></span> <span data-ttu-id="d1af9-244">Nastavení trasy pomocí `Order = 1` spustí se po výchozím řazení trasy.</span><span class="sxs-lookup"><span data-stu-id="d1af9-244">Setting a route using `Order = 1` will run after default route ordering.</span></span>

> [!TIP]
> <span data-ttu-id="d1af9-245">Nepoužívejte v závislosti na `Order`.</span><span class="sxs-lookup"><span data-stu-id="d1af9-245">Avoid depending on `Order`.</span></span> <span data-ttu-id="d1af9-246">Pokud vaše adresa URL vyžaduje explicitní směrování hodnot pořadí, je pravděpodobné, že budou i u klientů matoucí.</span><span class="sxs-lookup"><span data-stu-id="d1af9-246">If your URL-space requires explicit order values to route correctly, then it's likely confusing to clients as well.</span></span> <span data-ttu-id="d1af9-247">V části Obecné směrování atributů výběr správné trasy s odpovídající adresou URL.</span><span class="sxs-lookup"><span data-stu-id="d1af9-247">In general attribute routing will select the correct route with URL matching.</span></span> <span data-ttu-id="d1af9-248">Pokud výchozí pořadí použité pro generování adresy URL nefunguje, je použití názvu trasy jako přepsání obvykle jednodušší než použití vlastnosti `Order`.</span><span class="sxs-lookup"><span data-stu-id="d1af9-248">If the default order used for URL generation isn't working, using route name as an override is usually simpler than applying the `Order` property.</span></span>

<span data-ttu-id="d1af9-249">Směrování Razor Pages směrování a řadiče MVC sdílí implementaci.</span><span class="sxs-lookup"><span data-stu-id="d1af9-249">Razor Pages routing and MVC controller routing share an implementation.</span></span> <span data-ttu-id="d1af9-250">Informace o pořadí směrování v Razor Pages tématech jsou k dispozici v tématu [Razor Pages konvence směrování a aplikace: pořadí směrování](xref:razor-pages/razor-pages-conventions#route-order).</span><span class="sxs-lookup"><span data-stu-id="d1af9-250">Information on route order in the Razor Pages topics is available at [Razor Pages route and app conventions: Route order](xref:razor-pages/razor-pages-conventions#route-order).</span></span>

<a name="routing-token-replacement-templates-ref-label"></a>

## <a name="token-replacement-in-route-templates-controller-action-area"></a><span data-ttu-id="d1af9-251">Nahrazení tokenu v šablonách směrování ([Controller], [Action], [oblast])</span><span class="sxs-lookup"><span data-stu-id="d1af9-251">Token replacement in route templates ([controller], [action], [area])</span></span>

<span data-ttu-id="d1af9-252">Pro usnadnění pohodlí trasy atributů podporují *Nahrazení tokenu* uzavřením tokenu do hranatých závorek (`[`, `]`).</span><span class="sxs-lookup"><span data-stu-id="d1af9-252">For convenience, attribute routes support *token replacement* by enclosing a token in square-braces (`[`, `]`).</span></span> <span data-ttu-id="d1af9-253">Tokeny `[action]`, `[area]` a `[controller]` jsou nahrazeny hodnotami názvu akce, názvu oblasti a názvu kontroleru z akce, kde je trasa definována.</span><span class="sxs-lookup"><span data-stu-id="d1af9-253">The tokens `[action]`, `[area]`, and `[controller]` are replaced with the values of the action name, area name, and controller name from the action where the route is defined.</span></span> <span data-ttu-id="d1af9-254">V následujícím příkladu akce odpovídají cestám adresy URL, jak je popsáno v komentářích:</span><span class="sxs-lookup"><span data-stu-id="d1af9-254">In the following example, the actions match URL paths as described in the comments:</span></span>

[!code-csharp[](routing/sample/main/Controllers/ProductsController.cs?range=7-11,13-17,20-22)]

<span data-ttu-id="d1af9-255">Nahrazení tokenu probíhá jako poslední krok při vytváření tras atributů.</span><span class="sxs-lookup"><span data-stu-id="d1af9-255">Token replacement occurs as the last step of building the attribute routes.</span></span> <span data-ttu-id="d1af9-256">Výše uvedený příklad se chová stejně jako následující kód:</span><span class="sxs-lookup"><span data-stu-id="d1af9-256">The above example will behave the same as the following code:</span></span>

[!code-csharp[](routing/sample/main/Controllers/ProductsController2.cs?range=7-11,13-17,20-22)]

<span data-ttu-id="d1af9-257">Trasy atributů je také možné kombinovat s dědičností.</span><span class="sxs-lookup"><span data-stu-id="d1af9-257">Attribute routes can also be combined with inheritance.</span></span> <span data-ttu-id="d1af9-258">To je zvlášť výkonné v kombinaci s náhradou tokenu.</span><span class="sxs-lookup"><span data-stu-id="d1af9-258">This is particularly powerful combined with token replacement.</span></span>

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

<span data-ttu-id="d1af9-259">Nahrazení tokenu platí také pro názvy tras definované směrováními atributů.</span><span class="sxs-lookup"><span data-stu-id="d1af9-259">Token replacement also applies to route names defined by attribute routes.</span></span> <span data-ttu-id="d1af9-260">`[Route("[controller]/[action]", Name="[controller]_[action]")]` vygeneruje jedinečný název trasy pro každou akci.</span><span class="sxs-lookup"><span data-stu-id="d1af9-260">`[Route("[controller]/[action]", Name="[controller]_[action]")]` generates a unique route name for each action.</span></span>

<span data-ttu-id="d1af9-261">Chcete-li porovnat oddělovač nahrazení tokenu literálu `[` nebo `]`, vydejte ho opakováním znaku (`[[` nebo `]]`).</span><span class="sxs-lookup"><span data-stu-id="d1af9-261">To match the literal token replacement delimiter `[` or  `]`, escape it by repeating the character (`[[` or `]]`).</span></span>

::: moniker range=">= aspnetcore-2.2"

<a name="routing-token-replacement-transformers-ref-label"></a>

### <a name="use-a-parameter-transformer-to-customize-token-replacement"></a><span data-ttu-id="d1af9-262">Použití transformátoru parametrů k přizpůsobení náhrady tokenu</span><span class="sxs-lookup"><span data-stu-id="d1af9-262">Use a parameter transformer to customize token replacement</span></span>

<span data-ttu-id="d1af9-263">Nahrazení tokenu se dá přizpůsobit pomocí transformátoru parametrů.</span><span class="sxs-lookup"><span data-stu-id="d1af9-263">Token replacement can be customized using a parameter transformer.</span></span> <span data-ttu-id="d1af9-264">Transformátor parametru implementuje `IOutboundParameterTransformer` a transformuje hodnotu parametrů.</span><span class="sxs-lookup"><span data-stu-id="d1af9-264">A parameter transformer implements `IOutboundParameterTransformer` and transforms the value of parameters.</span></span> <span data-ttu-id="d1af9-265">Například vlastní parametr `SlugifyParameterTransformer` Transformer změní hodnotu trasy `SubscriptionManagement` na `subscription-management`.</span><span class="sxs-lookup"><span data-stu-id="d1af9-265">For example, a custom `SlugifyParameterTransformer` parameter transformer changes the `SubscriptionManagement` route value to `subscription-management`.</span></span>

<span data-ttu-id="d1af9-266">@No__t_0 je konvence aplikačního modelu, kterou:</span><span class="sxs-lookup"><span data-stu-id="d1af9-266">The `RouteTokenTransformerConvention` is an application model convention that:</span></span>

* <span data-ttu-id="d1af9-267">Aplikuje transformátor parametrů na všechny trasy atributů v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="d1af9-267">Applies a parameter transformer to all attribute routes in an application.</span></span>
* <span data-ttu-id="d1af9-268">Přizpůsobí hodnoty tokenu trasy při jejich nahrazení.</span><span class="sxs-lookup"><span data-stu-id="d1af9-268">Customizes the attribute route token values as they are replaced.</span></span>

```csharp
public class SubscriptionManagementController : Controller
{
    [HttpGet("[controller]/[action]")] // Matches '/subscription-management/list-all'
    public IActionResult ListAll() { ... }
}
```

<span data-ttu-id="d1af9-269">@No__t_0 je v `ConfigureServices` zaregistrován jako možnost.</span><span class="sxs-lookup"><span data-stu-id="d1af9-269">The `RouteTokenTransformerConvention` is registered as an option in `ConfigureServices`.</span></span>

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

<a name="routing-multiple-routes-ref-label"></a>

### <a name="multiple-routes"></a><span data-ttu-id="d1af9-270">Více tras</span><span class="sxs-lookup"><span data-stu-id="d1af9-270">Multiple Routes</span></span>

<span data-ttu-id="d1af9-271">Směrování atributů podporuje definování více tras, které dosáhnou stejné akce.</span><span class="sxs-lookup"><span data-stu-id="d1af9-271">Attribute routing supports defining multiple routes that reach the same action.</span></span> <span data-ttu-id="d1af9-272">Nejběžnějším využitím je napodobení chování *výchozí konvenční trasy* , jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="d1af9-272">The most common usage of this is to mimic the behavior of the *default conventional route* as shown in the following example:</span></span>

```csharp
[Route("[controller]")]
public class ProductsController : Controller
{
   [Route("")]     // Matches 'Products'
   [Route("Index")] // Matches 'Products/Index'
   public IActionResult Index()
}
```

<span data-ttu-id="d1af9-273">Vložení více atributů trasy na řadič znamená, že každá z nich bude kombinována s každým z atributů tras na metodách akcí.</span><span class="sxs-lookup"><span data-stu-id="d1af9-273">Putting multiple route attributes on the controller means that each one will combine with each of the route attributes on the action methods.</span></span>

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

<span data-ttu-id="d1af9-274">Pokud jsou na akci umístěny více atributů trasy (které implementují `IActionConstraint`), pak každé omezení akce kombinuje s šablonou směrování z atributu, který ji definoval.</span><span class="sxs-lookup"><span data-stu-id="d1af9-274">When multiple route attributes (that implement `IActionConstraint`) are placed on an action, then each action constraint combines with the route template from the attribute that defined it.</span></span>

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
> <span data-ttu-id="d1af9-275">Zatímco používání více tras na akcích může vypadat jako účinné, je lepší zachovat jednoduché a jasně definované místo URL vaší aplikace.</span><span class="sxs-lookup"><span data-stu-id="d1af9-275">While using multiple routes on actions can seem powerful, it's better to keep your application's URL space simple and well-defined.</span></span> <span data-ttu-id="d1af9-276">V případě potřeby použijte více tras na akcích, například pro podporu stávajících klientů.</span><span class="sxs-lookup"><span data-stu-id="d1af9-276">Use multiple routes on actions only where needed, for example to support existing clients.</span></span>

<a name="routing-attr-options"></a>

### <a name="specifying-attribute-route-optional-parameters-default-values-and-constraints"></a><span data-ttu-id="d1af9-277">Určení volitelných parametrů trasy atributu, výchozích hodnot a omezení</span><span class="sxs-lookup"><span data-stu-id="d1af9-277">Specifying attribute route optional parameters, default values, and constraints</span></span>

<span data-ttu-id="d1af9-278">Trasy atributů podporují stejnou vloženou syntaxi jako konvenční trasy, aby bylo možné zadat volitelné parametry, výchozí hodnoty a omezení.</span><span class="sxs-lookup"><span data-stu-id="d1af9-278">Attribute routes support the same inline syntax as conventional routes to specify optional parameters, default values, and constraints.</span></span>

```csharp
[HttpPost("product/{id:int}")]
public IActionResult ShowProduct(int id)
{
   // ...
}
```

<span data-ttu-id="d1af9-279">Podrobný popis syntaxe šablony směrování naleznete v tématu Referenční dokumentace k [šabloně směrování](../../fundamentals/routing.md#route-template-reference) .</span><span class="sxs-lookup"><span data-stu-id="d1af9-279">See [Route Template Reference](../../fundamentals/routing.md#route-template-reference) for a detailed description of route template syntax.</span></span>

<a name="routing-cust-rt-attr-irt-ref-label"></a>

### <a name="custom-route-attributes-using-iroutetemplateprovider"></a><span data-ttu-id="d1af9-280">Vlastní atributy směrování pomocí `IRouteTemplateProvider`</span><span class="sxs-lookup"><span data-stu-id="d1af9-280">Custom route attributes using `IRouteTemplateProvider`</span></span>

<span data-ttu-id="d1af9-281">Všechny atributy trasy, které jsou k dispozici v rozhraní (`[Route(...)]`, `[HttpGet(...)]` atd.) implementují rozhraní `IRouteTemplateProvider`.</span><span class="sxs-lookup"><span data-stu-id="d1af9-281">All of the route attributes provided in the framework ( `[Route(...)]`, `[HttpGet(...)]` , etc.) implement the `IRouteTemplateProvider` interface.</span></span> <span data-ttu-id="d1af9-282">MVC vyhledává atributy tříd kontroleru a metody akcí při spuštění aplikace a používá ty, které implementují `IRouteTemplateProvider` k sestavení počáteční sady tras.</span><span class="sxs-lookup"><span data-stu-id="d1af9-282">MVC looks for attributes on controller classes and action methods when the app starts and uses the ones that implement `IRouteTemplateProvider` to build the initial set of routes.</span></span>

<span data-ttu-id="d1af9-283">Můžete implementovat `IRouteTemplateProvider` k definování vlastních atributů tras.</span><span class="sxs-lookup"><span data-stu-id="d1af9-283">You can implement `IRouteTemplateProvider` to define your own route attributes.</span></span> <span data-ttu-id="d1af9-284">Každý `IRouteTemplateProvider` umožňuje definovat jednu trasu s vlastní šablonou směrování, objednávkou a názvem:</span><span class="sxs-lookup"><span data-stu-id="d1af9-284">Each `IRouteTemplateProvider` allows you to define a single route with a custom route template, order, and name:</span></span>

```csharp
public class MyApiControllerAttribute : Attribute, IRouteTemplateProvider
{
   public string Template => "api/[controller]";

   public int? Order { get; set; }

   public string Name { get; set; }
}
```

<span data-ttu-id="d1af9-285">Atribut z výše uvedeného příkladu automaticky nastaví `Template` na `"api/[controller]"` při použití `[MyApiController]`.</span><span class="sxs-lookup"><span data-stu-id="d1af9-285">The attribute from the above example automatically sets the `Template` to `"api/[controller]"` when `[MyApiController]` is applied.</span></span>

<a name="routing-app-model-ref-label"></a>

### <a name="using-application-model-to-customize-attribute-routes"></a><span data-ttu-id="d1af9-286">Přizpůsobení tras atributů pomocí aplikačního modelu</span><span class="sxs-lookup"><span data-stu-id="d1af9-286">Using Application Model to customize attribute routes</span></span>

<span data-ttu-id="d1af9-287">*Aplikační model* je objektový model vytvořený při spuštění se všemi metadaty, které MVC používá ke směrování a provádění akcí.</span><span class="sxs-lookup"><span data-stu-id="d1af9-287">The *application model* is an object model created at startup with all of the metadata used by MVC to route and execute your actions.</span></span> <span data-ttu-id="d1af9-288">*Aplikační model* zahrnuje všechna data získaná z atributů směrování (prostřednictvím `IRouteTemplateProvider`).</span><span class="sxs-lookup"><span data-stu-id="d1af9-288">The *application model* includes all of the data gathered from route attributes (through `IRouteTemplateProvider`).</span></span> <span data-ttu-id="d1af9-289">Můžete napsat *konvence* pro úpravu aplikačního modelu v době spuštění, abyste mohli přizpůsobit způsob, jakým se směrování chová.</span><span class="sxs-lookup"><span data-stu-id="d1af9-289">You can write *conventions* to modify the application model at startup time to customize how routing behaves.</span></span> <span data-ttu-id="d1af9-290">V této části se dozvíte jednoduchý příklad přizpůsobení směrování pomocí aplikačního modelu.</span><span class="sxs-lookup"><span data-stu-id="d1af9-290">This section shows a simple example of customizing routing using application model.</span></span>

[!code-csharp[](routing/sample/main/NamespaceRoutingConvention.cs)]

<a name="routing-mixed-ref-label"></a>

## <a name="mixed-routing-attribute-routing-vs-conventional-routing"></a><span data-ttu-id="d1af9-291">Kombinované směrování: směrování atributů vs. konvenční směrování</span><span class="sxs-lookup"><span data-stu-id="d1af9-291">Mixed routing: Attribute routing vs conventional routing</span></span>

<span data-ttu-id="d1af9-292">Aplikace MVC můžou kombinovat použití konvenčního směrování a směrování atributů.</span><span class="sxs-lookup"><span data-stu-id="d1af9-292">MVC applications can mix the use of conventional routing and attribute routing.</span></span> <span data-ttu-id="d1af9-293">Pro řadiče, které obsluhují stránky HTML pro prohlížeče, a směrování atributů pro řadiče obsluhující rozhraní REST API se obvykle používá konvenční trasy.</span><span class="sxs-lookup"><span data-stu-id="d1af9-293">It's typical to use conventional routes for controllers serving HTML pages for browsers, and attribute routing for controllers serving REST APIs.</span></span>

<span data-ttu-id="d1af9-294">Akce jsou směrovány buď podle konvence nebo směrovaného atributu.</span><span class="sxs-lookup"><span data-stu-id="d1af9-294">Actions are either conventionally routed or attribute routed.</span></span> <span data-ttu-id="d1af9-295">Umístěním trasy do kontroleru nebo akce se nasměruje atribut IT.</span><span class="sxs-lookup"><span data-stu-id="d1af9-295">Placing a route on the controller or the action makes it attribute routed.</span></span> <span data-ttu-id="d1af9-296">Akce definující trasy atributů nemůžou být dostupné prostřednictvím konvenčních tras a naopak.</span><span class="sxs-lookup"><span data-stu-id="d1af9-296">Actions that define attribute routes cannot be reached through the conventional routes and vice-versa.</span></span> <span data-ttu-id="d1af9-297">**Libovolný** atribut směrování v řadiči provádí směrování všech akcí v atributu kontroleru.</span><span class="sxs-lookup"><span data-stu-id="d1af9-297">**Any** route attribute on the controller makes all actions in the controller attribute routed.</span></span>

> [!NOTE]
> <span data-ttu-id="d1af9-298">Způsob, jakým se rozlišují dva typy systémů směrování, je proces, který se použije po tom, co adresa URL odpovídá šabloně směrování.</span><span class="sxs-lookup"><span data-stu-id="d1af9-298">What distinguishes the two types of routing systems is the process applied after a URL matches a route template.</span></span> <span data-ttu-id="d1af9-299">V části konvenční směrování se při výběru akce a kontroleru z vyhledávací tabulky všech konvenčních akcí směrování používají hodnoty tras z shody.</span><span class="sxs-lookup"><span data-stu-id="d1af9-299">In conventional routing, the route values from the match are used to choose the action and controller from a lookup table of all conventional routed actions.</span></span> <span data-ttu-id="d1af9-300">V směrování atributů je Každá šablona již přidružena k akci a není nutné žádné další vyhledávání.</span><span class="sxs-lookup"><span data-stu-id="d1af9-300">In attribute routing, each template is already associated with an action, and no further lookup is needed.</span></span>

## <a name="complex-segments"></a><span data-ttu-id="d1af9-301">Komplexní segmenty</span><span class="sxs-lookup"><span data-stu-id="d1af9-301">Complex segments</span></span>

<span data-ttu-id="d1af9-302">Komplexní segmenty (například `[Route("/dog{token}cat")]`) jsou zpracovávány porovnáním koncových literálů zprava doleva nehladým způsobem.</span><span class="sxs-lookup"><span data-stu-id="d1af9-302">Complex segments (for example, `[Route("/dog{token}cat")]`), are processed by matching up literals from right to left in a non-greedy way.</span></span> <span data-ttu-id="d1af9-303">Popis najdete [ve zdrojovém kódu](https://github.com/aspnet/Routing/blob/9cea167cfac36cf034dbb780e3f783114ef94780/src/Microsoft.AspNetCore.Routing/Patterns/RoutePatternMatcher.cs#L296) .</span><span class="sxs-lookup"><span data-stu-id="d1af9-303">See [the source code](https://github.com/aspnet/Routing/blob/9cea167cfac36cf034dbb780e3f783114ef94780/src/Microsoft.AspNetCore.Routing/Patterns/RoutePatternMatcher.cs#L296) for a description.</span></span> <span data-ttu-id="d1af9-304">Další informace najdete v [tomto problému](https://github.com/aspnet/AspNetCore.Docs/issues/8197).</span><span class="sxs-lookup"><span data-stu-id="d1af9-304">For more information, see [this issue](https://github.com/aspnet/AspNetCore.Docs/issues/8197).</span></span>

<a name="routing-url-gen-ref-label"></a>

## <a name="url-generation"></a><span data-ttu-id="d1af9-305">Generování adresy URL</span><span class="sxs-lookup"><span data-stu-id="d1af9-305">URL Generation</span></span>

<span data-ttu-id="d1af9-306">Aplikace MVC můžou použít funkce generování adresy URL směrování k vygenerování odkazů na akce v adresách URL.</span><span class="sxs-lookup"><span data-stu-id="d1af9-306">MVC applications can use routing's URL generation features to generate URL links to actions.</span></span> <span data-ttu-id="d1af9-307">Generování adres URL eliminuje adresy URL zakódujeme, takže je váš kód robustnější a udržovatelný.</span><span class="sxs-lookup"><span data-stu-id="d1af9-307">Generating URLs eliminates hardcoding URLs, making your code more robust and maintainable.</span></span> <span data-ttu-id="d1af9-308">Tato část se zaměřuje na funkce generování adresy URL poskytované MVC a bude se týkat jenom základních informací o tom, jak generování adresy URL funguje.</span><span class="sxs-lookup"><span data-stu-id="d1af9-308">This section focuses on the URL generation features provided by MVC and will only cover basics of how URL generation works.</span></span> <span data-ttu-id="d1af9-309">Podrobný popis generování adresy URL najdete v tématu věnovaném [Směrování](../../fundamentals/routing.md) .</span><span class="sxs-lookup"><span data-stu-id="d1af9-309">See [Routing](../../fundamentals/routing.md) for a detailed description of URL generation.</span></span>

<span data-ttu-id="d1af9-310">Rozhraní `IUrlHelper` je základní částí infrastruktury mezi MVC a směrováním pro generování adresy URL.</span><span class="sxs-lookup"><span data-stu-id="d1af9-310">The `IUrlHelper` interface is the underlying piece of infrastructure between MVC and routing for URL generation.</span></span> <span data-ttu-id="d1af9-311">V části řadiče, zobrazení a zobrazení komponent najdete instanci `IUrlHelper` k dispozici prostřednictvím vlastnosti `Url`.</span><span class="sxs-lookup"><span data-stu-id="d1af9-311">You'll find an instance of `IUrlHelper` available through the `Url` property in controllers, views, and view components.</span></span>

<span data-ttu-id="d1af9-312">V tomto příkladu je `IUrlHelper` rozhraní použito prostřednictvím vlastnosti `Controller.Url` pro vygenerování adresy URL jiné akci.</span><span class="sxs-lookup"><span data-stu-id="d1af9-312">In this example, the `IUrlHelper` interface is used through the `Controller.Url` property to generate a URL to another action.</span></span>

[!code-csharp[](routing/sample/main/Controllers/UrlGenerationController.cs?name=snippet_1)]

<span data-ttu-id="d1af9-313">Pokud aplikace používá výchozí konvenční trasu, hodnota proměnné `url` bude řetězec cesty adresy URL `/UrlGeneration/Destination`.</span><span class="sxs-lookup"><span data-stu-id="d1af9-313">If the application is using the default conventional route, the value of the `url` variable will be the URL path string `/UrlGeneration/Destination`.</span></span> <span data-ttu-id="d1af9-314">Tato cesta URL se vytvoří směrováním kombinováním hodnot tras z aktuální žádosti (okolní hodnoty) s hodnotami předanými do `Url.Action` a nahrazením těchto hodnot do šablony trasy:</span><span class="sxs-lookup"><span data-stu-id="d1af9-314">This URL path is created by routing by combining the route values from the current request (ambient values), with the values passed to `Url.Action` and substituting those values into the route template:</span></span>

```
ambient values: { controller = "UrlGeneration", action = "Source" }
values passed to Url.Action: { controller = "UrlGeneration", action = "Destination" }
route template: {controller}/{action}/{id?}

result: /UrlGeneration/Destination
```

<span data-ttu-id="d1af9-315">Každý parametr trasy v šabloně směrování má jeho hodnotu nahrazenou odpovídajícími názvy hodnotami a okolními hodnotami.</span><span class="sxs-lookup"><span data-stu-id="d1af9-315">Each route parameter in the route template has its value substituted by matching names with the values and ambient values.</span></span> <span data-ttu-id="d1af9-316">Parametr trasy, který nemá hodnotu, může používat výchozí hodnotu, pokud má jednu, nebo může být vynechána, pokud je volitelná (jako v případě `id` v tomto příkladu).</span><span class="sxs-lookup"><span data-stu-id="d1af9-316">A route parameter that doesn't have a value can use a default value if it has one, or be skipped if it's optional (as in the case of `id` in this example).</span></span> <span data-ttu-id="d1af9-317">Generování adresy URL selže, pokud žádný požadovaný parametr trasy nemá odpovídající hodnotu.</span><span class="sxs-lookup"><span data-stu-id="d1af9-317">URL generation will fail if any required route parameter doesn't have a corresponding value.</span></span> <span data-ttu-id="d1af9-318">Pokud se generování adresy URL u trasy nezdařilo, bude se zkoušet další trasa, dokud nebudou všechny trasy vyzkoušeny nebo nalezena shoda.</span><span class="sxs-lookup"><span data-stu-id="d1af9-318">If URL generation fails for a route, the next route is tried until all routes have been tried or a match is found.</span></span>

<span data-ttu-id="d1af9-319">Příklad `Url.Action` výše předpokládá konvenční směrování, ale generování adresy URL funguje podobně jako při směrování atributů, i když se koncepty liší.</span><span class="sxs-lookup"><span data-stu-id="d1af9-319">The example of `Url.Action` above assumes conventional routing, but URL generation works similarly with attribute routing, though the concepts are different.</span></span> <span data-ttu-id="d1af9-320">Při použití konvenčního směrování se hodnoty tras používají k rozbalení šablony a hodnoty tras pro `controller` a `action` obvykle se v této šabloně zobrazují – to funguje, protože adresy URL, které odpovídají směrování, dodržují *konvence*.</span><span class="sxs-lookup"><span data-stu-id="d1af9-320">With conventional routing, the route values are used to expand a template, and the route values for `controller` and `action` usually appear in that template - this works because the URLs matched by routing adhere to a *convention*.</span></span> <span data-ttu-id="d1af9-321">V případě směrování atributů se v šabloně nemůžou zobrazovat hodnoty tras pro `controller` a `action`, které se místo toho používají k vyhledání šablony, která se má použít.</span><span class="sxs-lookup"><span data-stu-id="d1af9-321">In attribute routing, the route values for `controller` and `action` are not allowed to appear in the template - they're instead used to look up which template to use.</span></span>

<span data-ttu-id="d1af9-322">V tomto příkladu se používá směrování atributů:</span><span class="sxs-lookup"><span data-stu-id="d1af9-322">This example uses attribute routing:</span></span>

[!code-csharp[](routing/sample/main/StartupUseMvc.cs?name=snippet_1)]

[!code-csharp[](routing/sample/main/Controllers/UrlGenerationControllerAttr.cs?name=snippet_1)]

<span data-ttu-id="d1af9-323">MVC vytvoří vyhledávací tabulku všech akcí směrovaného atributu a bude odpovídat hodnotám `controller` a `action` a vybrat šablonu směrování, která se má použít pro generování adresy URL.</span><span class="sxs-lookup"><span data-stu-id="d1af9-323">MVC builds a lookup table of all attribute routed actions and will match the `controller` and `action` values to select the route template to use for URL generation.</span></span> <span data-ttu-id="d1af9-324">V ukázce výše se vygeneruje `custom/url/to/destination`.</span><span class="sxs-lookup"><span data-stu-id="d1af9-324">In the sample above,   `custom/url/to/destination` is generated.</span></span>

### <a name="generating-urls-by-action-name"></a><span data-ttu-id="d1af9-325">Generování adres URL podle názvu akce</span><span class="sxs-lookup"><span data-stu-id="d1af9-325">Generating URLs by action name</span></span>

<span data-ttu-id="d1af9-326">`Url.Action` (`IUrlHelper`.</span><span class="sxs-lookup"><span data-stu-id="d1af9-326">`Url.Action` (`IUrlHelper` .</span></span> <span data-ttu-id="d1af9-327">`Action`) a všechna související přetížení jsou založena na této myšlence, se kterou chcete určit, k čemu se odkazuje, zadáním názvu kontroleru a názvu akce.</span><span class="sxs-lookup"><span data-stu-id="d1af9-327">`Action`) and all related overloads all are based on that idea that you want to specify what you're linking to by specifying a controller name and action name.</span></span>

> [!NOTE]
> <span data-ttu-id="d1af9-328">Při použití `Url.Action` jsou aktuální hodnoty trasy pro `controller` a `action` určené pro vás – hodnota `controller` a `action` jsou součástí obou *okolních hodnot* **a** *hodnot*.</span><span class="sxs-lookup"><span data-stu-id="d1af9-328">When using `Url.Action`, the current route values for `controller` and `action` are specified for you - the value of `controller` and `action` are part of both *ambient values* **and** *values*.</span></span> <span data-ttu-id="d1af9-329">Metoda `Url.Action`, vždy používá aktuální hodnoty `action` a `controller` a vygeneruje cestu URL, která bude směrovat na aktuální akci.</span><span class="sxs-lookup"><span data-stu-id="d1af9-329">The method `Url.Action`, always uses the current values of `action` and `controller` and will generate a URL path that routes to the current action.</span></span>

<span data-ttu-id="d1af9-330">Směrování se pokusí použít hodnoty v ambientních hodnotách k vyplnění informací, které jste nezadali při generování adresy URL.</span><span class="sxs-lookup"><span data-stu-id="d1af9-330">Routing attempts to use the values in ambient values to fill in information that you didn't provide when generating a URL.</span></span> <span data-ttu-id="d1af9-331">Při použití trasy, jako je `{a}/{b}/{c}/{d}` a ambientních hodnot `{ a = Alice, b = Bob, c = Carol, d = David }`, směrování obsahuje dostatek informací pro vygenerování adresy URL bez dalších hodnot – protože všechny parametry tras mají hodnotu.</span><span class="sxs-lookup"><span data-stu-id="d1af9-331">Using a route like `{a}/{b}/{c}/{d}` and ambient values `{ a = Alice, b = Bob, c = Carol, d = David }`, routing has enough information to generate a URL without any additional values - since all route parameters have a value.</span></span> <span data-ttu-id="d1af9-332">Pokud jste přidali hodnotu `{ d = Donovan }`, hodnota `{ d = David }` by byla ignorována a vygenerovaná cesta URL bude `Alice/Bob/Carol/Donovan`.</span><span class="sxs-lookup"><span data-stu-id="d1af9-332">If you added the value `{ d = Donovan }`, the value `{ d = David }` would be ignored, and the generated URL path would be `Alice/Bob/Carol/Donovan`.</span></span>

> [!WARNING]
> <span data-ttu-id="d1af9-333">Cesty URL jsou hierarchické.</span><span class="sxs-lookup"><span data-stu-id="d1af9-333">URL paths are hierarchical.</span></span> <span data-ttu-id="d1af9-334">Pokud jste v předchozím příkladu přidali hodnotu `{ c = Cheryl }`, budou obě hodnoty `{ c = Carol, d = David }` ignorovány.</span><span class="sxs-lookup"><span data-stu-id="d1af9-334">In the example above, if you added the value `{ c = Cheryl }`, both of the values `{ c = Carol, d = David }` would be ignored.</span></span> <span data-ttu-id="d1af9-335">V tomto případě již nemusíte mít hodnotu pro `d` a generování adresy URL selže.</span><span class="sxs-lookup"><span data-stu-id="d1af9-335">In this case we no longer have a value for `d` and URL generation will fail.</span></span> <span data-ttu-id="d1af9-336">Je nutné zadat požadovanou hodnotu `c` a `d`.</span><span class="sxs-lookup"><span data-stu-id="d1af9-336">You would need to specify the desired value of `c` and `d`.</span></span>  <span data-ttu-id="d1af9-337">Můžete očekávat, že se tento problém bude nacházet s výchozí trasou (`{controller}/{action}/{id?}`), ale toto chování se bude v praxi chovat zřídka, protože `Url.Action` vždy explicitně specifikuje `controller` a `action` hodnotu.</span><span class="sxs-lookup"><span data-stu-id="d1af9-337">You might expect to hit this problem with the default route (`{controller}/{action}/{id?}`) - but you will rarely encounter this behavior in practice as `Url.Action` will always explicitly specify a `controller` and `action` value.</span></span>

<span data-ttu-id="d1af9-338">Delší přetížení `Url.Action` také přijímají další objekt *hodnoty trasy* , který poskytuje hodnoty pro parametry směrování jiné než `controller` a `action`.</span><span class="sxs-lookup"><span data-stu-id="d1af9-338">Longer overloads of `Url.Action` also take an additional *route values* object to provide values for route parameters other than `controller` and `action`.</span></span> <span data-ttu-id="d1af9-339">Nejčastěji to vidíte, že se používá `id` jako `Url.Action("Buy", "Products", new { id = 17 })`.</span><span class="sxs-lookup"><span data-stu-id="d1af9-339">You will most commonly see this used with `id` like `Url.Action("Buy", "Products", new { id = 17 })`.</span></span> <span data-ttu-id="d1af9-340">Podle konvence objekt *hodnoty trasy* je obvykle objektem anonymního typu, ale může se také jednat o `IDictionary<>` nebo o *prostý starý objekt .NET*.</span><span class="sxs-lookup"><span data-stu-id="d1af9-340">By convention the *route values* object is usually an object of anonymous type, but it can also be an `IDictionary<>` or a *plain old .NET object*.</span></span> <span data-ttu-id="d1af9-341">Všechny další hodnoty tras, které neodpovídají parametrům směrování, jsou vloženy do řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="d1af9-341">Any additional route values that don't match route parameters are put in the query string.</span></span>

[!code-csharp[](routing/sample/main/Controllers/TestController.cs)]

> [!TIP]
> <span data-ttu-id="d1af9-342">Chcete-li vytvořit absolutní adresu URL, použijte přetížení, které přijímá `protocol`: `Url.Action("Buy", "Products", new { id = 17 }, protocol: Request.Scheme)`</span><span class="sxs-lookup"><span data-stu-id="d1af9-342">To create an absolute URL, use an overload that accepts a `protocol`: `Url.Action("Buy", "Products", new { id = 17 }, protocol: Request.Scheme)`</span></span>

<a name="routing-gen-urls-route-ref-label"></a>

### <a name="generating-urls-by-route"></a><span data-ttu-id="d1af9-343">Generování adres URL podle směrování</span><span class="sxs-lookup"><span data-stu-id="d1af9-343">Generating URLs by route</span></span>

<span data-ttu-id="d1af9-344">Výše uvedený kód ukázal generování adresy URL předáním do kontroleru a názvu akce.</span><span class="sxs-lookup"><span data-stu-id="d1af9-344">The code above demonstrated generating a URL by passing in the controller and action name.</span></span> <span data-ttu-id="d1af9-345">`IUrlHelper` také poskytuje `Url.RouteUrl` rodinu metod.</span><span class="sxs-lookup"><span data-stu-id="d1af9-345">`IUrlHelper` also provides the `Url.RouteUrl` family of methods.</span></span> <span data-ttu-id="d1af9-346">Tyto metody jsou podobné `Url.Action`, ale nekopírují aktuální hodnoty `action` a `controller` na hodnoty tras.</span><span class="sxs-lookup"><span data-stu-id="d1af9-346">These methods are similar to `Url.Action`, but they don't copy the current values of `action` and `controller` to the route values.</span></span> <span data-ttu-id="d1af9-347">Nejběžnějším využitím je zadání názvu trasy pro použití konkrétní trasy pro vygenerování adresy URL, a to obecně *bez* zadání kontroleru nebo názvu akce.</span><span class="sxs-lookup"><span data-stu-id="d1af9-347">The most common usage is to specify a route name to use a specific route to generate the URL, generally *without* specifying a controller or action name.</span></span>

[!code-csharp[](routing/sample/main/Controllers/UrlGenerationControllerRouting.cs?name=snippet_1)]

<a name="routing-gen-urls-html-ref-label"></a>

### <a name="generating-urls-in-html"></a><span data-ttu-id="d1af9-348">Generování adres URL v HTML</span><span class="sxs-lookup"><span data-stu-id="d1af9-348">Generating URLs in HTML</span></span>

<span data-ttu-id="d1af9-349">`IHtmlHelper` poskytuje `HtmlHelper` metody `Html.BeginForm` a `Html.ActionLink` k vygenerování `<form>` a `<a>` prvků v uvedeném pořadí.</span><span class="sxs-lookup"><span data-stu-id="d1af9-349">`IHtmlHelper` provides the `HtmlHelper` methods `Html.BeginForm` and `Html.ActionLink` to generate `<form>` and `<a>` elements respectively.</span></span> <span data-ttu-id="d1af9-350">Tyto metody používají metodu `Url.Action` k vygenerování adresy URL a akceptuje podobné argumenty.</span><span class="sxs-lookup"><span data-stu-id="d1af9-350">These methods use the `Url.Action` method to generate a URL and they accept similar arguments.</span></span> <span data-ttu-id="d1af9-351">@No__t_0 doprovodné funkce pro `HtmlHelper` jsou `Html.BeginRouteForm` a `Html.RouteLink` s podobnými funkcemi.</span><span class="sxs-lookup"><span data-stu-id="d1af9-351">The `Url.RouteUrl` companions for `HtmlHelper` are `Html.BeginRouteForm` and `Html.RouteLink` which have similar functionality.</span></span>

<span data-ttu-id="d1af9-352">TagHelpers vygeneruje adresy URL prostřednictvím `form` Taghelperu a `<a>` Taghelperu.</span><span class="sxs-lookup"><span data-stu-id="d1af9-352">TagHelpers generate URLs through the `form` TagHelper and the `<a>` TagHelper.</span></span> <span data-ttu-id="d1af9-353">Obě tato použití `IUrlHelper` pro jejich implementaci.</span><span class="sxs-lookup"><span data-stu-id="d1af9-353">Both of these use `IUrlHelper` for their implementation.</span></span> <span data-ttu-id="d1af9-354">Další informace najdete v tématu [práce s formuláři](../views/working-with-forms.md) .</span><span class="sxs-lookup"><span data-stu-id="d1af9-354">See [Working with Forms](../views/working-with-forms.md) for more information.</span></span>

<span data-ttu-id="d1af9-355">V zobrazeních jsou `IUrlHelper` k dispozici prostřednictvím vlastnosti `Url` pro všechny generace adres URL ad-hoc, které nejsou pokryté výše.</span><span class="sxs-lookup"><span data-stu-id="d1af9-355">Inside views, the `IUrlHelper` is available through the `Url` property for any ad-hoc URL generation not covered by the above.</span></span>

<a name="routing-gen-urls-action-ref-label"></a>

### <a name="generating-urls-in-action-results"></a><span data-ttu-id="d1af9-356">Generování adres URL ve výsledcích akcí</span><span class="sxs-lookup"><span data-stu-id="d1af9-356">Generating URLS in Action Results</span></span>

<span data-ttu-id="d1af9-357">Výše uvedené příklady ukazují použití `IUrlHelper` v kontroleru, zatímco nejběžnější využití v kontroleru vygeneruje adresu URL jako součást výsledku akce.</span><span class="sxs-lookup"><span data-stu-id="d1af9-357">The examples above have shown using `IUrlHelper` in a controller, while the most common usage in a controller is to generate a URL as part of an action result.</span></span>

<span data-ttu-id="d1af9-358">Základní třídy `ControllerBase` a `Controller` poskytují pohodlný způsob pro výsledky akcí, které odkazují na jinou akci.</span><span class="sxs-lookup"><span data-stu-id="d1af9-358">The `ControllerBase` and `Controller` base classes provide convenience methods for action results that reference another action.</span></span> <span data-ttu-id="d1af9-359">Jedním z typických použití je přesměrování po přijetí vstupu uživatele.</span><span class="sxs-lookup"><span data-stu-id="d1af9-359">One typical usage is to redirect after accepting user input.</span></span>

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

<span data-ttu-id="d1af9-360">Metody pro vytváření výsledků pro zpracování akcí dodrží podobný vzor pro metody `IUrlHelper`.</span><span class="sxs-lookup"><span data-stu-id="d1af9-360">The action results factory methods follow a similar pattern to the methods on `IUrlHelper`.</span></span>

<a name="routing-dedicated-ref-label"></a>

### <a name="special-case-for-dedicated-conventional-routes"></a><span data-ttu-id="d1af9-361">Zvláštní případ pro vyhrazené konvenční trasy</span><span class="sxs-lookup"><span data-stu-id="d1af9-361">Special case for dedicated conventional routes</span></span>

<span data-ttu-id="d1af9-362">Konvenční směrování může používat speciální druh definice trasy, která se označuje jako *vyhrazená konvenční trasa*.</span><span class="sxs-lookup"><span data-stu-id="d1af9-362">Conventional routing can use a special kind of route definition called a *dedicated conventional route*.</span></span> <span data-ttu-id="d1af9-363">V následujícím příkladu je trasa s názvem `blog` vyhrazenou konvenční trasou.</span><span class="sxs-lookup"><span data-stu-id="d1af9-363">In the example below, the route named `blog` is a dedicated conventional route.</span></span>

```csharp
app.UseMvc(routes =>
{
    routes.MapRoute("blog", "blog/{*article}",
        defaults: new { controller = "Blog", action = "Article" });
    routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="d1af9-364">Pomocí těchto definic tras `Url.Action("Index", "Home")` vygeneruje cestu URL `/` s `default` trasou, ale proč?</span><span class="sxs-lookup"><span data-stu-id="d1af9-364">Using these route definitions, `Url.Action("Index", "Home")` will generate the URL path `/` with the `default` route, but why?</span></span> <span data-ttu-id="d1af9-365">Můžete odhadnout hodnoty tras, `{ controller = Home, action = Index }` by bylo dostatečné pro vygenerování adresy URL pomocí `blog` a výsledek by byl `/blog?action=Index&controller=Home`.</span><span class="sxs-lookup"><span data-stu-id="d1af9-365">You might guess the route values `{ controller = Home, action = Index }` would be enough to generate a URL using `blog`, and the result would be `/blog?action=Index&controller=Home`.</span></span>

<span data-ttu-id="d1af9-366">Vyhrazené konvenční trasy spoléhají na speciální chování výchozích hodnot, které nemají odpovídající parametr trasy, který brání tomu, aby trasa byla "příliš hlade" s generováním adresy URL.</span><span class="sxs-lookup"><span data-stu-id="d1af9-366">Dedicated conventional routes rely on a special behavior of default values that don't have a corresponding route parameter that prevents the route from being "too greedy" with URL generation.</span></span> <span data-ttu-id="d1af9-367">V tomto případě jsou `{ controller = Blog, action = Article }` výchozí hodnoty a ani `controller` ani `action` se nezobrazí jako parametr trasy.</span><span class="sxs-lookup"><span data-stu-id="d1af9-367">In this case the default values are `{ controller = Blog, action = Article }`, and neither `controller` nor `action` appears as a route parameter.</span></span> <span data-ttu-id="d1af9-368">Když směrování provádí generování adresy URL, zadané hodnoty musí odpovídat výchozím hodnotám.</span><span class="sxs-lookup"><span data-stu-id="d1af9-368">When routing performs URL generation, the values provided must match the default values.</span></span> <span data-ttu-id="d1af9-369">Generování adresy URL pomocí `blog` se nezdaří, protože hodnoty `{ controller = Home, action = Index }` neodpovídají `{ controller = Blog, action = Article }`.</span><span class="sxs-lookup"><span data-stu-id="d1af9-369">URL generation using `blog` will fail because the values `{ controller = Home, action = Index }` don't match `{ controller = Blog, action = Article }`.</span></span> <span data-ttu-id="d1af9-370">Směrování se pak vrátí k pokusu o `default`, který je úspěšný.</span><span class="sxs-lookup"><span data-stu-id="d1af9-370">Routing then falls back to try `default`, which succeeds.</span></span>

<a name="routing-areas-ref-label"></a>

## <a name="areas"></a><span data-ttu-id="d1af9-371">Oblasti</span><span class="sxs-lookup"><span data-stu-id="d1af9-371">Areas</span></span>

<span data-ttu-id="d1af9-372">[Oblasti](areas.md) jsou funkce MVC, která slouží k uspořádání souvisejících funkcí do skupiny jako samostatného oboru názvů směrování (pro akce kontroleru) a struktury složek (pro zobrazení).</span><span class="sxs-lookup"><span data-stu-id="d1af9-372">[Areas](areas.md) are an MVC feature used to organize related functionality into a group as a separate routing-namespace (for controller actions) and folder structure (for views).</span></span> <span data-ttu-id="d1af9-373">Použití oblastí umožňuje aplikaci mít více řadičů se stejným názvem, pokud mají různé *oblasti*.</span><span class="sxs-lookup"><span data-stu-id="d1af9-373">Using areas allows an application to have multiple controllers with the same name - as long as they have different *areas*.</span></span> <span data-ttu-id="d1af9-374">Použití oblastí vytvoří hierarchii pro účely směrování přidáním dalšího parametru trasy `area` `controller` a `action`.</span><span class="sxs-lookup"><span data-stu-id="d1af9-374">Using areas creates a hierarchy for the purpose of routing by adding another route parameter, `area` to `controller` and `action`.</span></span> <span data-ttu-id="d1af9-375">V této části se dozvíte, jak směrování komunikuje s oblastmi – viz [oblasti](areas.md) pro podrobnosti o použití oblastí se zobrazeními.</span><span class="sxs-lookup"><span data-stu-id="d1af9-375">This section will discuss how routing interacts with areas - see [Areas](areas.md) for details about how areas are used with views.</span></span>

<span data-ttu-id="d1af9-376">V následujícím příkladu je nakonfiguruje MVC pro použití výchozí konvenční trasy a *trasy oblasti* pro oblast s názvem `Blog`:</span><span class="sxs-lookup"><span data-stu-id="d1af9-376">The following example configures MVC to use the default conventional route and an *area route* for an area named `Blog`:</span></span>

[!code-csharp[](routing/sample/AreasRouting/Startup.cs?name=snippet1)]

<span data-ttu-id="d1af9-377">Když se porovná cesta URL jako `/Manage/Users/AddUser`, první trasa vytvoří hodnoty trasy `{ area = Blog, controller = Users, action = AddUser }`.</span><span class="sxs-lookup"><span data-stu-id="d1af9-377">When matching a URL path like `/Manage/Users/AddUser`, the first route will produce the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span> <span data-ttu-id="d1af9-378">Hodnota `area` trasy je vytvořena výchozí hodnotou pro `area`, ve skutečnosti je trasa vytvořená v `MapAreaRoute` shodná s následujícím:</span><span class="sxs-lookup"><span data-stu-id="d1af9-378">The `area` route value is produced by a default value for `area`, in fact the route created by `MapAreaRoute` is equivalent to the following:</span></span>

[!code-csharp[](routing/sample/AreasRouting/Startup.cs?name=snippet2)]

<span data-ttu-id="d1af9-379">`MapAreaRoute` vytvoří trasu pomocí výchozí hodnoty i omezení pro `area` pomocí zadaného názvu oblasti v tomto případě `Blog`.</span><span class="sxs-lookup"><span data-stu-id="d1af9-379">`MapAreaRoute` creates a route using both a default value and constraint for `area` using the provided area name, in this case `Blog`.</span></span> <span data-ttu-id="d1af9-380">Výchozí hodnota zajistí, že trasa vždy vytvoří `{ area = Blog, ... }`, omezení vyžaduje `{ area = Blog, ... }` hodnoty pro generování adresy URL.</span><span class="sxs-lookup"><span data-stu-id="d1af9-380">The default value ensures that the route always produces `{ area = Blog, ... }`, the constraint requires the value `{ area = Blog, ... }` for URL generation.</span></span>

> [!TIP]
> <span data-ttu-id="d1af9-381">Konvenční směrování je závislé na pořadí.</span><span class="sxs-lookup"><span data-stu-id="d1af9-381">Conventional routing is order-dependent.</span></span> <span data-ttu-id="d1af9-382">Obecně platí, že trasy s oblastmi by měly být umístěny dříve v tabulce směrování, protože jsou konkrétnější než trasy bez oblasti.</span><span class="sxs-lookup"><span data-stu-id="d1af9-382">In general, routes with areas should be placed earlier in the route table as they're more specific than routes without an area.</span></span>

<span data-ttu-id="d1af9-383">Pomocí výše uvedeného příkladu budou hodnoty směrování odpovídat následující akci:</span><span class="sxs-lookup"><span data-stu-id="d1af9-383">Using the above example, the route values would match the following action:</span></span>

[!code-csharp[](routing/sample/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

<span data-ttu-id="d1af9-384">@No__t_0 je to, že jako součást oblasti označuje kontroler, říkáme, že tento kontroler je v oblasti `Blog`.</span><span class="sxs-lookup"><span data-stu-id="d1af9-384">The `AreaAttribute` is what denotes a controller as part of an area, we say that this controller is in the `Blog` area.</span></span> <span data-ttu-id="d1af9-385">Řadiče bez atributu `[Area]` nejsou členy žádné oblasti **a nebudou se shodovat,** Pokud je hodnota `area` trasy poskytnuta směrováním.</span><span class="sxs-lookup"><span data-stu-id="d1af9-385">Controllers without an `[Area]` attribute are not members of any area, and will **not** match when the `area` route value is provided by routing.</span></span> <span data-ttu-id="d1af9-386">V následujícím příkladu se může shodovat jenom první kontroler, který bude odpovídat hodnotám tras `{ area = Blog, controller = Users, action = AddUser }`.</span><span class="sxs-lookup"><span data-stu-id="d1af9-386">In the following example, only the first controller listed can match the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span>

[!code-csharp[](routing/sample/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

[!code-csharp[](routing/sample/AreasRouting/Areas/Zebra/Controllers/UsersController.cs)]

[!code-csharp[](routing/sample/AreasRouting/Controllers/UsersController.cs)]

> [!NOTE]
> <span data-ttu-id="d1af9-387">Obor názvů každého kontroleru se tady zobrazuje pro úplnost – jinak by řadiče vyžadovaly konflikt názvů a generovaly chybu kompilátoru.</span><span class="sxs-lookup"><span data-stu-id="d1af9-387">The namespace of each controller is shown here for completeness - otherwise the controllers would have a naming conflict and generate a compiler error.</span></span> <span data-ttu-id="d1af9-388">Obory názvů třídy nemají žádný vliv na směrování MVC.</span><span class="sxs-lookup"><span data-stu-id="d1af9-388">Class namespaces have no effect on MVC's routing.</span></span>

<span data-ttu-id="d1af9-389">První dva řadiče jsou členy oblastí a shodují se pouze v případě, že je jejich název příslušné oblasti poskytovaný hodnotou `area` trasy.</span><span class="sxs-lookup"><span data-stu-id="d1af9-389">The first two controllers are members of areas, and only match when their respective area name is provided by the `area` route value.</span></span> <span data-ttu-id="d1af9-390">Třetí kontroler není členem žádné oblasti a může odpovídat jenom v případě, že směrováním neposkytuje žádnou hodnotu pro `area`.</span><span class="sxs-lookup"><span data-stu-id="d1af9-390">The third controller isn't a member of any area, and can only match when no value for `area` is provided by routing.</span></span>

> [!NOTE]
> <span data-ttu-id="d1af9-391">V souvislosti s neshodnou *hodnotou*je absence hodnoty `area` stejná, jako kdyby hodnota `area` měla hodnotu null nebo prázdný řetězec.</span><span class="sxs-lookup"><span data-stu-id="d1af9-391">In terms of matching *no value*, the absence of the `area` value is the same as if the value for `area` were null or the empty string.</span></span>

<span data-ttu-id="d1af9-392">Při provádění akce uvnitř oblasti bude hodnota trasy pro `area` k dispozici jako *ambientní hodnota* pro směrování, která se má použít pro generování adresy URL.</span><span class="sxs-lookup"><span data-stu-id="d1af9-392">When executing an action inside an area, the route value for `area` will be available as an *ambient value* for routing to use for URL generation.</span></span> <span data-ttu-id="d1af9-393">To znamená, že ve výchozím nastavení jsou tyto oblasti pro generování adresy URL *rychlé* , jak je znázorněno v následující ukázce.</span><span class="sxs-lookup"><span data-stu-id="d1af9-393">This means that by default areas act *sticky* for URL generation as demonstrated by the following sample.</span></span>

[!code-csharp[](routing/sample/AreasRouting/Startup.cs?name=snippet3)]

[!code-csharp[](routing/sample/AreasRouting/Areas/Duck/Controllers/UsersController.cs)]

<a name="iactionconstraint-ref-label"></a>

## <a name="understanding-iactionconstraint"></a><span data-ttu-id="d1af9-394">Principy IActionConstraint</span><span class="sxs-lookup"><span data-stu-id="d1af9-394">Understanding IActionConstraint</span></span>

> [!NOTE]
> <span data-ttu-id="d1af9-395">Tato část je hlubokým podrobněem interních platforem a způsobu, jakým MVC vybírá akci, která se má provést.</span><span class="sxs-lookup"><span data-stu-id="d1af9-395">This section is a deep-dive on framework internals and how MVC chooses an action to execute.</span></span> <span data-ttu-id="d1af9-396">Typická aplikace nebude potřebovat vlastní `IActionConstraint`</span><span class="sxs-lookup"><span data-stu-id="d1af9-396">A typical application won't need a custom `IActionConstraint`</span></span>

<span data-ttu-id="d1af9-397">Pravděpodobně jste již použili `IActionConstraint`, i když nejste obeznámeni s rozhraním.</span><span class="sxs-lookup"><span data-stu-id="d1af9-397">You have likely already used `IActionConstraint` even if you're not familiar with the interface.</span></span> <span data-ttu-id="d1af9-398">Atribut `[HttpGet]` a podobné atributy `[Http-VERB]` implementují `IActionConstraint`, aby bylo možné omezit provádění metody akce.</span><span class="sxs-lookup"><span data-stu-id="d1af9-398">The `[HttpGet]` Attribute and similar `[Http-VERB]` attributes implement `IActionConstraint` in order to limit the execution of an action method.</span></span>

```csharp
public class ProductsController : Controller
{
    [HttpGet]
    public IActionResult Edit() { }

    public IActionResult Edit(...) { }
}
```

<span data-ttu-id="d1af9-399">Za předpokladu, že výchozí konvenční trasa, adresa URL `/Products/Edit` by vytvořila `{ controller = Products, action = Edit }` hodnoty, které odpovídají **oběma** akcím, které jsou zde uvedeny.</span><span class="sxs-lookup"><span data-stu-id="d1af9-399">Assuming the default conventional route, the URL path `/Products/Edit` would produce the values `{ controller = Products, action = Edit }`, which would match **both** of the actions shown here.</span></span> <span data-ttu-id="d1af9-400">V `IActionConstraint` terminologii bychom znamenali, že obě tyto akce se považují za kandidáty, protože obě odpovídají datům směrování.</span><span class="sxs-lookup"><span data-stu-id="d1af9-400">In `IActionConstraint` terminology we would say that both of these actions are considered candidates - as they both match the route data.</span></span>

<span data-ttu-id="d1af9-401">Pokud se `HttpGetAttribute` spustí, znamená to, že *Edit ()* je odpovídající pro *Get* a neshoduje se s žádným jiným příkazem http.</span><span class="sxs-lookup"><span data-stu-id="d1af9-401">When the `HttpGetAttribute` executes, it will say that *Edit()* is a match for *GET* and isn't a match for any other HTTP verb.</span></span> <span data-ttu-id="d1af9-402">Akce `Edit(...)` nemá definovány žádné omezení, a proto bude odpovídat jakémukoli příkazu HTTP.</span><span class="sxs-lookup"><span data-stu-id="d1af9-402">The `Edit(...)` action doesn't have any constraints defined, and so will match any HTTP verb.</span></span> <span data-ttu-id="d1af9-403">Proto předpokládáme, že `Edit(...)` odpovídá pouze `POST`.</span><span class="sxs-lookup"><span data-stu-id="d1af9-403">So assuming a `POST` - only `Edit(...)` matches.</span></span> <span data-ttu-id="d1af9-404">Ale u `GET` se obě akce stále shodují – ale akce s `IActionConstraint` je vždy považována za *lepší* , než je akce bez.</span><span class="sxs-lookup"><span data-stu-id="d1af9-404">But, for a `GET` both actions can still match - however, an action with an `IActionConstraint` is always considered *better* than an action without.</span></span> <span data-ttu-id="d1af9-405">Proto vzhledem k tomu, že `Edit()` má `[HttpGet]` považuje za konkrétnější, a vybere se, pokud se obě akce mohou shodovat.</span><span class="sxs-lookup"><span data-stu-id="d1af9-405">So because `Edit()` has `[HttpGet]` it's considered more specific, and will be selected if both actions can match.</span></span>

<span data-ttu-id="d1af9-406">Koncepční, `IActionConstraint` je forma *přetížení*, ale místo přetížení metod se stejným názvem, je přetížení mezi akcemi, které odpovídají stejné adrese URL.</span><span class="sxs-lookup"><span data-stu-id="d1af9-406">Conceptually, `IActionConstraint` is a form of *overloading*, but instead of overloading methods with the same name, it's overloading between actions that match the same URL.</span></span> <span data-ttu-id="d1af9-407">Směrování atributů používá taky `IActionConstraint` a může mít za následek, že se z různých řadičů považují i kandidáti.</span><span class="sxs-lookup"><span data-stu-id="d1af9-407">Attribute routing also uses `IActionConstraint` and can result in actions from different controllers both being considered candidates.</span></span>

<a name="iactionconstraint-impl-ref-label"></a>

### <a name="implementing-iactionconstraint"></a><span data-ttu-id="d1af9-408">Implementace IActionConstraint</span><span class="sxs-lookup"><span data-stu-id="d1af9-408">Implementing IActionConstraint</span></span>

<span data-ttu-id="d1af9-409">Nejjednodušší způsob, jak implementovat `IActionConstraint`, je vytvořit třídu odvozenou z `System.Attribute` a umístit ji na vaše akce a řadiče.</span><span class="sxs-lookup"><span data-stu-id="d1af9-409">The simplest way to implement an `IActionConstraint` is to create a class derived from `System.Attribute` and place it on your actions and controllers.</span></span> <span data-ttu-id="d1af9-410">MVC automaticky zjistí všechny `IActionConstraint`, které se použijí jako atributy.</span><span class="sxs-lookup"><span data-stu-id="d1af9-410">MVC will automatically discover any `IActionConstraint` that are applied as attributes.</span></span> <span data-ttu-id="d1af9-411">Pomocí modelu aplikace můžete použít omezení, což je pravděpodobně nejpružnější přístup, protože umožňuje Metaprogram způsob jejich použití.</span><span class="sxs-lookup"><span data-stu-id="d1af9-411">You can use the application model to apply constraints, and this is probably the most flexible approach as it allows you to metaprogram how they're applied.</span></span>

<span data-ttu-id="d1af9-412">V následujícím příkladu omezení zvolí akci založenou na *kódu země* z údajů o trasách.</span><span class="sxs-lookup"><span data-stu-id="d1af9-412">In the following example a constraint chooses an action based on a *country code* from the route data.</span></span> <span data-ttu-id="d1af9-413">[Úplná ukázka na GitHubu](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.ActionConstraintSample.Web/CountrySpecificAttribute.cs).</span><span class="sxs-lookup"><span data-stu-id="d1af9-413">The [full sample on GitHub](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.ActionConstraintSample.Web/CountrySpecificAttribute.cs).</span></span>

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

<span data-ttu-id="d1af9-414">Zodpovídáte za implementaci `Accept` metody a výběrem příkazu Order, aby se omezení spustilo.</span><span class="sxs-lookup"><span data-stu-id="d1af9-414">You are responsible for implementing the `Accept` method and choosing an 'Order' for the constraint to execute.</span></span> <span data-ttu-id="d1af9-415">V tomto případě metoda `Accept` vrátí `true` k tomu, že se jedná o shodu, když hodnota `country` trasy odpovídá hodnotě.</span><span class="sxs-lookup"><span data-stu-id="d1af9-415">In this case, the `Accept` method returns `true` to denote the action is a match when the `country` route value matches.</span></span> <span data-ttu-id="d1af9-416">To se liší od `RouteValueAttribute` v tom, že umožňuje přechod na akci bez atributu.</span><span class="sxs-lookup"><span data-stu-id="d1af9-416">This is different from a `RouteValueAttribute` in that it allows fallback to a non-attributed action.</span></span> <span data-ttu-id="d1af9-417">Ukázka ukazuje, že pokud definujete `en-US` akci, směrové číslo země, jako je `fr-FR`, se vrátí do obecnější kontroler, který nemá `[CountrySpecific(...)]` použití.</span><span class="sxs-lookup"><span data-stu-id="d1af9-417">The sample shows that if you define an `en-US` action then a country code like `fr-FR` will fall back to a more generic controller that doesn't have `[CountrySpecific(...)]` applied.</span></span>

<span data-ttu-id="d1af9-418">Vlastnost `Order` určuje, ve které *fázi* je omezení součástí.</span><span class="sxs-lookup"><span data-stu-id="d1af9-418">The `Order` property decides which *stage* the constraint is part of.</span></span> <span data-ttu-id="d1af9-419">Omezení akcí se spouští ve skupinách na základě `Order`.</span><span class="sxs-lookup"><span data-stu-id="d1af9-419">Action constraints run in groups based on the `Order`.</span></span> <span data-ttu-id="d1af9-420">Například všechny atributy, které poskytuje rozhraní HTTP, používají stejnou `Order` hodnotu tak, aby běžely ve stejné fázi.</span><span class="sxs-lookup"><span data-stu-id="d1af9-420">For example, all of the framework provided HTTP method attributes use the same `Order` value so that they run in the same stage.</span></span> <span data-ttu-id="d1af9-421">Můžete mít tolik fází, kolik potřebujete k implementaci požadovaných zásad.</span><span class="sxs-lookup"><span data-stu-id="d1af9-421">You can have as many stages as you need to implement your desired policies.</span></span>

> [!TIP]
> <span data-ttu-id="d1af9-422">Chcete-li se rozhodnout o hodnotě pro `Order` zamyslete nad tím, zda by mělo být vaše omezení použito před metodami HTTP.</span><span class="sxs-lookup"><span data-stu-id="d1af9-422">To decide on a value for `Order` think about whether or not your constraint should be applied before HTTP methods.</span></span> <span data-ttu-id="d1af9-423">Nižší čísla se spouštějí jako první.</span><span class="sxs-lookup"><span data-stu-id="d1af9-423">Lower numbers run first.</span></span>
