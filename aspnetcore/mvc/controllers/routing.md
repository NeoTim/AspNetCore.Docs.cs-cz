---
title: Směrování na akce kontroleru v ASP.NET Core
author: rick-anderson
description: Zjistěte, jak ASP.NET Core MVC používá směrování Middleware podle adresy URL příchozích událostí požadavků a jejich namapování na akce.
ms.author: riande
ms.date: 01/24/2019
uid: mvc/controllers/routing
ms.openlocfilehash: f5104bc53581a41fa8c25d8c67e08e038c275391
ms.sourcegitcommit: c6db8b14521814f1f7e528d7aa06e474e4c04a1f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/25/2019
ms.locfileid: "55065006"
---
# <a name="routing-to-controller-actions-in-aspnet-core"></a><span data-ttu-id="6580f-103">Směrování na akce kontroleru v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6580f-103">Routing to controller actions in ASP.NET Core</span></span>

<span data-ttu-id="6580f-104">Podle [Ryanem Nowak](https://github.com/rynowak) a [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="6580f-104">By [Ryan Nowak](https://github.com/rynowak) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="6580f-105">ASP.NET Core MVC používá směrování [middleware](xref:fundamentals/middleware/index) podle adresy URL příchozích událostí požadavků a jejich namapování na akce.</span><span class="sxs-lookup"><span data-stu-id="6580f-105">ASP.NET Core MVC uses the Routing [middleware](xref:fundamentals/middleware/index) to match the URLs of incoming requests and map them to actions.</span></span> <span data-ttu-id="6580f-106">Směrování je definováno v kódu při spuštění nebo atributy.</span><span class="sxs-lookup"><span data-stu-id="6580f-106">Routes are defined in startup code or attributes.</span></span> <span data-ttu-id="6580f-107">Postupy popisují, jak cesty adresy URL by měla odpovídat akce.</span><span class="sxs-lookup"><span data-stu-id="6580f-107">Routes describe how URL paths should be matched to actions.</span></span> <span data-ttu-id="6580f-108">Trasy se také používají k vygenerování adres URL (pro odkazy), odeslání odpovědi.</span><span class="sxs-lookup"><span data-stu-id="6580f-108">Routes are also used to generate URLs (for links) sent out in responses.</span></span>

<span data-ttu-id="6580f-109">Akce jsou buď konvenčně směrovat nebo atribut směrovat.</span><span class="sxs-lookup"><span data-stu-id="6580f-109">Actions are either conventionally routed or attribute routed.</span></span> <span data-ttu-id="6580f-110">Umístění trasy na kontroler nebo akce umožňuje směrovat atribut.</span><span class="sxs-lookup"><span data-stu-id="6580f-110">Placing a route on the controller or the action makes it attribute routed.</span></span> <span data-ttu-id="6580f-111">Zobrazit [smíšené směrování](#routing-mixed-ref-label) Další informace.</span><span class="sxs-lookup"><span data-stu-id="6580f-111">See [Mixed routing](#routing-mixed-ref-label) for more information.</span></span>

<span data-ttu-id="6580f-112">Tento dokument vysvětluje interakce mezi MVC a směrování a jak typické zpřístupnění aplikace MVC pomocí funkce směrování.</span><span class="sxs-lookup"><span data-stu-id="6580f-112">This document will explain the interactions between MVC and routing, and how typical MVC apps make use of routing features.</span></span> <span data-ttu-id="6580f-113">Zobrazit [směrování](xref:fundamentals/routing) podrobnosti o pokročilé směrování.</span><span class="sxs-lookup"><span data-stu-id="6580f-113">See [Routing](xref:fundamentals/routing) for details on advanced routing.</span></span>

## <a name="setting-up-routing-middleware"></a><span data-ttu-id="6580f-114">Nastavení směrování middlewaru</span><span class="sxs-lookup"><span data-stu-id="6580f-114">Setting up Routing Middleware</span></span>

<span data-ttu-id="6580f-115">Ve vaší *konfigurovat* metoda může se zobrazit podobný kód:</span><span class="sxs-lookup"><span data-stu-id="6580f-115">In your *Configure* method you may see code similar to:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="6580f-116">Uvnitř volání `UseMvc`, `MapRoute` slouží k vytvoření jedné trasy, které budete označujeme jako `default` trasy.</span><span class="sxs-lookup"><span data-stu-id="6580f-116">Inside the call to `UseMvc`, `MapRoute` is used to create a single route, which we'll refer to as the `default` route.</span></span> <span data-ttu-id="6580f-117">Většina aplikací MVC použije trasu pomocí šablony podobně jako `default` trasy.</span><span class="sxs-lookup"><span data-stu-id="6580f-117">Most MVC apps will use a route with a template similar to the `default` route.</span></span>

<span data-ttu-id="6580f-118">Šablona trasy `"{controller=Home}/{action=Index}/{id?}"` odpovídá cesty adresy URL jako `/Products/Details/5` extrahuje hodnoty trasy `{ controller = Products, action = Details, id = 5 }` podle tokenizaci cestu.</span><span class="sxs-lookup"><span data-stu-id="6580f-118">The route template `"{controller=Home}/{action=Index}/{id?}"` can match a URL path like `/Products/Details/5` and will extract the route values `{ controller = Products, action = Details, id = 5 }` by tokenizing the path.</span></span> <span data-ttu-id="6580f-119">MVC se pokusí najít kontroler s názvem `ProductsController` a spustit akci `Details`:</span><span class="sxs-lookup"><span data-stu-id="6580f-119">MVC will attempt to locate a controller named `ProductsController` and run the action `Details`:</span></span>

```csharp
public class ProductsController : Controller
{
   public IActionResult Details(int id) { ... }
}
```

<span data-ttu-id="6580f-120">Všimněte si, že v tomto příkladu by vazby modelu použijte hodnotu `id = 5` nastavit `id` parametr `5` při vyvolání této akce.</span><span class="sxs-lookup"><span data-stu-id="6580f-120">Note that in this example, model binding would use the value of `id = 5` to set the `id` parameter to `5` when invoking this action.</span></span> <span data-ttu-id="6580f-121">Zobrazit [vazby modelu](../models/model-binding.md) další podrobnosti.</span><span class="sxs-lookup"><span data-stu-id="6580f-121">See the [Model Binding](../models/model-binding.md) for more details.</span></span>

<span data-ttu-id="6580f-122">Použití `default` trasy:</span><span class="sxs-lookup"><span data-stu-id="6580f-122">Using the `default` route:</span></span>

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="6580f-123">Šablona trasy:</span><span class="sxs-lookup"><span data-stu-id="6580f-123">The route template:</span></span>

* <span data-ttu-id="6580f-124">`{controller=Home}` definuje `Home` jako výchozí `controller`</span><span class="sxs-lookup"><span data-stu-id="6580f-124">`{controller=Home}` defines `Home` as the default `controller`</span></span>

* <span data-ttu-id="6580f-125">`{action=Index}` definuje `Index` jako výchozí `action`</span><span class="sxs-lookup"><span data-stu-id="6580f-125">`{action=Index}` defines `Index` as the default `action`</span></span>

* <span data-ttu-id="6580f-126">`{id?}` definuje `id` jako volitelné</span><span class="sxs-lookup"><span data-stu-id="6580f-126">`{id?}` defines `id` as optional</span></span>

<span data-ttu-id="6580f-127">Výchozí a volitelné trasy parametry nemusí být k dispozici v cestě adresy URL pro shodu.</span><span class="sxs-lookup"><span data-stu-id="6580f-127">Default and optional route parameters don't need to be present in the URL path for a match.</span></span> <span data-ttu-id="6580f-128">V tématu [trasy referenčními informacemi k šablonám](../../fundamentals/routing.md#route-template-reference) podrobný popis syntaxe šablon trasy.</span><span class="sxs-lookup"><span data-stu-id="6580f-128">See [Route Template Reference](../../fundamentals/routing.md#route-template-reference) for a detailed description of route template syntax.</span></span>

<span data-ttu-id="6580f-129">`"{controller=Home}/{action=Index}/{id?}"` odpovídá cestě adresy URL `/` a hodnoty trasy `{ controller = Home, action = Index }`.</span><span class="sxs-lookup"><span data-stu-id="6580f-129">`"{controller=Home}/{action=Index}/{id?}"` can match the URL path `/` and will produce the route values `{ controller = Home, action = Index }`.</span></span> <span data-ttu-id="6580f-130">Hodnoty pro `controller` a `action` provést pomocí výchozích hodnot `id` nevytvoří hodnotu, protože neexistuje žádný odpovídající segment v cestě adresy URL.</span><span class="sxs-lookup"><span data-stu-id="6580f-130">The values for `controller` and `action` make use of the default values, `id` doesn't produce a value since there's no corresponding segment in the URL path.</span></span> <span data-ttu-id="6580f-131">MVC využije tyto hodnoty trasy k výběru `HomeController` a `Index` akce:</span><span class="sxs-lookup"><span data-stu-id="6580f-131">MVC would use these route values to select the `HomeController` and `Index` action:</span></span>

```csharp
public class HomeController : Controller
{
  public IActionResult Index() { ... }
}
```

<span data-ttu-id="6580f-132">Pomocí tohoto kontroleru definice a šablonu trasy `HomeController.Index` akce by byl proveden pro některý z následujících cest URL:</span><span class="sxs-lookup"><span data-stu-id="6580f-132">Using this controller definition and route template, the `HomeController.Index` action would be executed for any of the following URL paths:</span></span>

* `/Home/Index/17`

* `/Home/Index`

* `/Home`

* `/`

<span data-ttu-id="6580f-133">Metoda pohodlí `UseMvcWithDefaultRoute`:</span><span class="sxs-lookup"><span data-stu-id="6580f-133">The convenience method `UseMvcWithDefaultRoute`:</span></span>

```csharp
app.UseMvcWithDefaultRoute();
```

<span data-ttu-id="6580f-134">Je možné nahradit:</span><span class="sxs-lookup"><span data-stu-id="6580f-134">Can be used to replace:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="6580f-135">`UseMvc` a `UseMvcWithDefaultRoute` přidat instanci `RouterMiddleware` do kanálu middlewaru.</span><span class="sxs-lookup"><span data-stu-id="6580f-135">`UseMvc` and `UseMvcWithDefaultRoute` add an instance of `RouterMiddleware` to the middleware pipeline.</span></span> <span data-ttu-id="6580f-136">MVC nebude pracovat přímo s middlewarem a používá směrování pro zpracování požadavků.</span><span class="sxs-lookup"><span data-stu-id="6580f-136">MVC doesn't interact directly with middleware, and uses routing to handle requests.</span></span> <span data-ttu-id="6580f-137">MVC je připojené k trasám prostřednictvím instance `MvcRouteHandler`.</span><span class="sxs-lookup"><span data-stu-id="6580f-137">MVC is connected to the routes through an instance of `MvcRouteHandler`.</span></span> <span data-ttu-id="6580f-138">Kód uvnitř `UseMvc` je podobný následujícímu:</span><span class="sxs-lookup"><span data-stu-id="6580f-138">The code inside of `UseMvc` is similar to the following:</span></span>

```csharp
var routes = new RouteBuilder(app);

// Add connection to MVC, will be hooked up by calls to MapRoute.
routes.DefaultHandler = new MvcRouteHandler(...);

// Execute callback to register routes.
// routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");

// Create route collection and add the middleware.
app.UseRouter(routes.Build());
```

<span data-ttu-id="6580f-139">`UseMvc` přímo nedefinuje všechny trasy, se přidá do kolekce tras pro zástupný symbol `attribute` trasy.</span><span class="sxs-lookup"><span data-stu-id="6580f-139">`UseMvc` doesn't directly define any routes, it adds a placeholder to the route collection for the `attribute` route.</span></span> <span data-ttu-id="6580f-140">Přetížení `UseMvc(Action<IRouteBuilder>)` umožňuje přidat vlastní trasy a také podporuje směrováním atributů.</span><span class="sxs-lookup"><span data-stu-id="6580f-140">The overload `UseMvc(Action<IRouteBuilder>)` lets you add your own routes and also supports attribute routing.</span></span>  <span data-ttu-id="6580f-141">`UseMvc` a všechny jeho variant přidá zástupný symbol pro atribut trasy – směrování atributů je vždy k dispozici bez ohledu na to, jak nakonfigurovat `UseMvc`.</span><span class="sxs-lookup"><span data-stu-id="6580f-141">`UseMvc` and all of its variations adds a placeholder for the attribute route - attribute routing is always available regardless of how you configure `UseMvc`.</span></span> <span data-ttu-id="6580f-142">`UseMvcWithDefaultRoute` Definuje výchozí trasu a podporuje směrováním atributů.</span><span class="sxs-lookup"><span data-stu-id="6580f-142">`UseMvcWithDefaultRoute` defines a default route and supports attribute routing.</span></span> <span data-ttu-id="6580f-143">[Směrováním atributů](#attribute-routing-ref-label) část obsahuje další podrobnosti o směrování atributů.</span><span class="sxs-lookup"><span data-stu-id="6580f-143">The [Attribute Routing](#attribute-routing-ref-label) section includes more details on attribute routing.</span></span>

<a name="routing-conventional-ref-label"></a>

## <a name="conventional-routing"></a><span data-ttu-id="6580f-144">Konvenční směrování</span><span class="sxs-lookup"><span data-stu-id="6580f-144">Conventional routing</span></span>

<span data-ttu-id="6580f-145">`default` Trasy:</span><span class="sxs-lookup"><span data-stu-id="6580f-145">The `default` route:</span></span>

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="6580f-146">Příkladem je *konvenční směrování*.</span><span class="sxs-lookup"><span data-stu-id="6580f-146">is an example of a *conventional routing*.</span></span> <span data-ttu-id="6580f-147">Označujeme je jako tento styl *konvenční směrování* protože navazuje *konvence* pro cesty adresy URL:</span><span class="sxs-lookup"><span data-stu-id="6580f-147">We call this style *conventional routing* because it establishes a *convention* for URL paths:</span></span>

* <span data-ttu-id="6580f-148">první segment cesty se mapuje na název řadiče</span><span class="sxs-lookup"><span data-stu-id="6580f-148">the first path segment maps to the controller name</span></span>

* <span data-ttu-id="6580f-149">Druhá mapuje na název akce.</span><span class="sxs-lookup"><span data-stu-id="6580f-149">the second maps to the action name.</span></span>

* <span data-ttu-id="6580f-150">třetí segmentu se používá pro volitelný `id` slouží k mapování na modelu entity</span><span class="sxs-lookup"><span data-stu-id="6580f-150">the third segment is used for an optional `id` used to map to a model entity</span></span>

<span data-ttu-id="6580f-151">Použití této funkce `default` trasy, URL path `/Products/List` mapuje `ProductsController.List` akce, a `/Blog/Article/17` mapuje na `BlogController.Article`.</span><span class="sxs-lookup"><span data-stu-id="6580f-151">Using this `default` route, the URL path `/Products/List` maps to the `ProductsController.List` action, and `/Blog/Article/17` maps to `BlogController.Article`.</span></span> <span data-ttu-id="6580f-152">Toto mapování je podle názvu kontroleru a akce **pouze** a není založena na obory názvů, umístění zdrojových souborů nebo parametry metody.</span><span class="sxs-lookup"><span data-stu-id="6580f-152">This mapping is based on the controller and action names **only** and isn't based on namespaces, source file locations, or method parameters.</span></span>

> [!TIP]
> <span data-ttu-id="6580f-153">Použití konvenční směrování s výchozí trasa umožňuje rychle vytvářet aplikace bez nutnosti a Navrhněte nové vzor adresy URL pro každou akci, kterou definujete.</span><span class="sxs-lookup"><span data-stu-id="6580f-153">Using conventional routing with the default route allows you to build the application quickly without having to come up with a new URL pattern for each action you define.</span></span> <span data-ttu-id="6580f-154">Aplikace s akcemi CRUD styl s konzistence adres URL ve vašich řadičů může pomoct zjednodušit kód a ujistěte se, uživatelské rozhraní předvídatelnější.</span><span class="sxs-lookup"><span data-stu-id="6580f-154">For an application with CRUD style actions, having consistency for the URLs across your controllers can help simplify your code and make your UI more predictable.</span></span>

> [!WARNING]
> <span data-ttu-id="6580f-155">`id` Je definován jako volitelné šablonu trasy, což znamená, že vaše akce můžete provést bez ID zadané jako část adresy URL.</span><span class="sxs-lookup"><span data-stu-id="6580f-155">The `id` is defined as optional by the route template, meaning that your actions can execute without the ID provided as part of the URL.</span></span> <span data-ttu-id="6580f-156">Obvykle co se stane, pokud `id` vynecháte z adresy URL je, že bude nastavena `0` navázáním modelu a jako výsledek žádná entita se nenašel v odpovídající databázi `id == 0`.</span><span class="sxs-lookup"><span data-stu-id="6580f-156">Usually what will happen if `id` is omitted from the URL is that it will be set to `0` by model binding, and as a result no entity will be found in the database matching `id == 0`.</span></span> <span data-ttu-id="6580f-157">Směrování atributů vám může velice přesně kontrolovat, aby je vyžadováno pro některé akce a nikoli pro jiné ID.</span><span class="sxs-lookup"><span data-stu-id="6580f-157">Attribute routing can give you fine-grained control to make the ID required for some actions and not for others.</span></span> <span data-ttu-id="6580f-158">Podle konvence zahrne do dokumentace, jako jsou volitelné parametry `id` když že se pravděpodobně se zobrazí v správné použití.</span><span class="sxs-lookup"><span data-stu-id="6580f-158">By convention the documentation will include optional parameters like `id` when they're likely to appear in correct usage.</span></span>

## <a name="multiple-routes"></a><span data-ttu-id="6580f-159">Několik tras</span><span class="sxs-lookup"><span data-stu-id="6580f-159">Multiple routes</span></span>

<span data-ttu-id="6580f-160">Můžete přidat několik tras uvnitř `UseMvc` tak, že přidáte další volání `MapRoute`.</span><span class="sxs-lookup"><span data-stu-id="6580f-160">You can add multiple routes inside `UseMvc` by adding more calls to `MapRoute`.</span></span> <span data-ttu-id="6580f-161">To umožňuje definovat více konvence, nebo přidat konvenční trasy, které jsou vyhrazeny pro určité akce, jako například:</span><span class="sxs-lookup"><span data-stu-id="6580f-161">Doing so allows you to define multiple conventions, or to add conventional routes that are dedicated to a specific action, such as:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("blog", "blog/{*article}",
            defaults: new { controller = "Blog", action = "Article" });
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="6580f-162">`blog` Trasy tady *vyhrazené konvenční trasy*, to znamená, že používá konvenční směrování systému, ale je vyhrazen pro určité akce.</span><span class="sxs-lookup"><span data-stu-id="6580f-162">The `blog` route here is a *dedicated conventional route*, meaning that it uses the conventional routing system, but is dedicated to a specific action.</span></span> <span data-ttu-id="6580f-163">Protože `controller` a `action` nejsou zobrazeny v šabloně trasy jako parametry, můžou mít jenom výchozí hodnoty a proto se tato trasa vždy mapují na akci `BlogController.Article`.</span><span class="sxs-lookup"><span data-stu-id="6580f-163">Since `controller` and `action` don't appear in the route template as parameters, they can only have the default values, and thus this route will always map to the action `BlogController.Article`.</span></span>

<span data-ttu-id="6580f-164">Trasy v kolekci tras jsou seřazené a budou zpracovány v pořadí, ve kterém se přidají.</span><span class="sxs-lookup"><span data-stu-id="6580f-164">Routes in the route collection are ordered, and will be processed in the order they're added.</span></span> <span data-ttu-id="6580f-165">Ano, v tomto příkladu `blog` trasy, vyzkouší se před `default` trasy.</span><span class="sxs-lookup"><span data-stu-id="6580f-165">So in this example, the `blog` route will be tried before the `default` route.</span></span>

> [!NOTE]
> <span data-ttu-id="6580f-166">*Vyhrazené konvenční trasy* často používají parametry trasy pokrývající vše jako `{*article}` zachycení zbývající část cesty URL.</span><span class="sxs-lookup"><span data-stu-id="6580f-166">*Dedicated conventional routes* often use catch-all route parameters like `{*article}` to capture the remaining portion of the URL path.</span></span> <span data-ttu-id="6580f-167">Díky tomu se trasy 'příliš greedy' to znamená, že se shoduje adresy URL, které je určený k porovnání s jiným trasám.</span><span class="sxs-lookup"><span data-stu-id="6580f-167">This can make a route 'too greedy' meaning that it matches URLs that you intended to be matched by other routes.</span></span> <span data-ttu-id="6580f-168">Umístěte "greedy" trasy později ve směrovací tabulce tento problém vyřešit.</span><span class="sxs-lookup"><span data-stu-id="6580f-168">Put the 'greedy' routes later in the route table to solve this.</span></span>

### <a name="fallback"></a><span data-ttu-id="6580f-169">Použití náhradní lokality</span><span class="sxs-lookup"><span data-stu-id="6580f-169">Fallback</span></span>

<span data-ttu-id="6580f-170">V rámci zpracování žádosti se bude ověřovat MVC, hodnoty trasy je možné najít kontroleru a akce ve vaší aplikaci.</span><span class="sxs-lookup"><span data-stu-id="6580f-170">As part of request processing, MVC will verify that the route values can be used to find a controller and action in your application.</span></span> <span data-ttu-id="6580f-171">Pokud se hodnoty trasy neshodují akci pak není trasy považovány za shodné a vyzkouší se další trasy.</span><span class="sxs-lookup"><span data-stu-id="6580f-171">If the route values don't match an action then the route isn't considered a match, and the next route will be tried.</span></span> <span data-ttu-id="6580f-172">Tento postup se nazývá *záložní*, a je určena ke zjednodušení případy, kdy konvenční trasy překrývat.</span><span class="sxs-lookup"><span data-stu-id="6580f-172">This is called *fallback*, and it's intended to simplify cases where conventional routes overlap.</span></span>

### <a name="disambiguating-actions"></a><span data-ttu-id="6580f-173">Odstraňování akce</span><span class="sxs-lookup"><span data-stu-id="6580f-173">Disambiguating actions</span></span>

<span data-ttu-id="6580f-174">Když dvě akce odpovídají prostřednictvím směrování, musí na tlačítko "nejlepší" Release candidate, jinak se vyvolat výjimku rozlišení MVC.</span><span class="sxs-lookup"><span data-stu-id="6580f-174">When two actions match through routing, MVC must disambiguate to choose the 'best' candidate or else throw an exception.</span></span> <span data-ttu-id="6580f-175">Příklad:</span><span class="sxs-lookup"><span data-stu-id="6580f-175">For example:</span></span>

```csharp
public class ProductsController : Controller
{
   public IActionResult Edit(int id) { ... }

   [HttpPost]
   public IActionResult Edit(int id, Product product) { ... }
}
```

<span data-ttu-id="6580f-176">Tento kontroler definuje dvě akce, která by odpovídala cestě adresy URL `/Products/Edit/17` a směrovat data `{ controller = Products, action = Edit, id = 17 }`.</span><span class="sxs-lookup"><span data-stu-id="6580f-176">This controller defines two actions that would match the URL path `/Products/Edit/17` and route data `{ controller = Products, action = Edit, id = 17 }`.</span></span> <span data-ttu-id="6580f-177">Toto je typický vzor pro kontrolery MVC kde `Edit(int)` ukazuje formulář pro úpravy produktu, a `Edit(int, Product)` zpracovává odeslaného formuláře.</span><span class="sxs-lookup"><span data-stu-id="6580f-177">This is a typical pattern for MVC controllers where `Edit(int)` shows a form to edit a product, and `Edit(int, Product)` processes  the posted form.</span></span> <span data-ttu-id="6580f-178">Aby to bylo možné vytvořit zvolte MVC třeba `Edit(int, Product)` při požadavku HTTP `POST` a `Edit(int)` po cokoli, je příkazem HTTP příkaz.</span><span class="sxs-lookup"><span data-stu-id="6580f-178">To make this possible MVC would need to choose `Edit(int, Product)` when the request is an HTTP `POST` and `Edit(int)` when the HTTP verb is anything else.</span></span>

<span data-ttu-id="6580f-179">`HttpPostAttribute` ( `[HttpPost]` ) Je implementace `IActionConstraint` akce k volbě, když je příkazem HTTP příkaz, který vám umožní pouze `POST`.</span><span class="sxs-lookup"><span data-stu-id="6580f-179">The `HttpPostAttribute` ( `[HttpPost]` ) is an implementation of `IActionConstraint` that will only allow the action to be selected when the HTTP verb is `POST`.</span></span> <span data-ttu-id="6580f-180">Přítomnost `IActionConstraint` díky `Edit(int, Product)` shodovat lepší než `Edit(int)`, takže `Edit(int, Product)` vyzkouší se napřed.</span><span class="sxs-lookup"><span data-stu-id="6580f-180">The presence of an `IActionConstraint` makes the `Edit(int, Product)` a 'better' match than `Edit(int)`, so `Edit(int, Product)` will be tried first.</span></span>

<span data-ttu-id="6580f-181">Je potřeba pouze napsat vlastní `IActionConstraint` implementace specializované scénáře, ale je důležité pochopit role atributů, jako je `HttpPostAttribute` – podobně jako atributy jsou definovány pro jiné příkazy HTTP.</span><span class="sxs-lookup"><span data-stu-id="6580f-181">You will only need to write custom `IActionConstraint` implementations in specialized scenarios, but it's important to understand the role of attributes like `HttpPostAttribute`  - similar attributes are defined for other HTTP verbs.</span></span> <span data-ttu-id="6580f-182">V konvenční směrování je běžné akce, které používají stejný název akce v případě, že se součást `show form -> submit form` pracovního postupu.</span><span class="sxs-lookup"><span data-stu-id="6580f-182">In conventional routing it's common for actions to use the same action name when they're part of a `show form -> submit form` workflow.</span></span> <span data-ttu-id="6580f-183">Výhodou tohoto modelu se stane po kontrole zřetelnější [Principy IActionConstraint](#understanding-iactionconstraint) oddílu.</span><span class="sxs-lookup"><span data-stu-id="6580f-183">The convenience of this pattern will become more apparent after reviewing the [Understanding IActionConstraint](#understanding-iactionconstraint) section.</span></span>

<span data-ttu-id="6580f-184">Pokud několik tras odpovídá a MVC nelze najít "nejlepší" směrování, vyvolá výjimku `AmbiguousActionException`.</span><span class="sxs-lookup"><span data-stu-id="6580f-184">If multiple routes match, and MVC can't find a 'best' route, it will throw an `AmbiguousActionException`.</span></span>

<a name="routing-route-name-ref-label"></a>

### <a name="route-names"></a><span data-ttu-id="6580f-185">Názvy tras</span><span class="sxs-lookup"><span data-stu-id="6580f-185">Route names</span></span>

<span data-ttu-id="6580f-186">Řetězce `"blog"` a `"default"` v následujících příkladech jsou názvy tras:</span><span class="sxs-lookup"><span data-stu-id="6580f-186">The strings  `"blog"` and `"default"` in the following examples are route names:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("blog", "blog/{*article}",
               defaults: new { controller = "Blog", action = "Article" });
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="6580f-187">Názvy tras poskytují trasy logický název tak, aby pojmenovanou trasu lze použít pro generování adresy URL.</span><span class="sxs-lookup"><span data-stu-id="6580f-187">The route names give the route a logical name so that the named route can be used for URL generation.</span></span> <span data-ttu-id="6580f-188">To výrazně zjednodušuje vytvoření adresy URL při řazení trasy dokonce vytvářet složité generování adresy URL.</span><span class="sxs-lookup"><span data-stu-id="6580f-188">This greatly simplifies URL creation when the ordering of routes could make URL generation complicated.</span></span> <span data-ttu-id="6580f-189">Názvy tras musí být jedinečný pro celou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="6580f-189">Route names must be unique application-wide.</span></span>

<span data-ttu-id="6580f-190">Názvy tras mít žádný vliv na adrese URL odpovídající nebo zpracování požadavků. slouží pouze pro generování adresy URL.</span><span class="sxs-lookup"><span data-stu-id="6580f-190">Route names have no impact on URL matching or handling of requests; they're used only for URL generation.</span></span> <span data-ttu-id="6580f-191">[Směrování](xref:fundamentals/routing) obsahuje podrobnější informace o generování adresy URL včetně generování adresy URL v pomocné rutiny specifické pro MVC.</span><span class="sxs-lookup"><span data-stu-id="6580f-191">[Routing](xref:fundamentals/routing) has more detailed information on URL generation including URL generation in MVC-specific helpers.</span></span>

<a name="attribute-routing-ref-label"></a>

## <a name="attribute-routing"></a><span data-ttu-id="6580f-192">Směrování atributů</span><span class="sxs-lookup"><span data-stu-id="6580f-192">Attribute routing</span></span>

<span data-ttu-id="6580f-193">Směrování atributů používá sadu atributů mapování akcí přímo do šablon trasy.</span><span class="sxs-lookup"><span data-stu-id="6580f-193">Attribute routing uses a set of attributes to map actions directly to route templates.</span></span> <span data-ttu-id="6580f-194">V následujícím příkladu `app.UseMvc();` je používán `Configure` je předán metodě a žádná trasa.</span><span class="sxs-lookup"><span data-stu-id="6580f-194">In the following example, `app.UseMvc();` is used in the `Configure` method and no route is passed.</span></span> <span data-ttu-id="6580f-195">`HomeController` Bude odpovídat sadu podobný postupu výchozí adresy URL `{controller=Home}/{action=Index}/{id?}` odpovídají:</span><span class="sxs-lookup"><span data-stu-id="6580f-195">The `HomeController` will match a set of URLs similar to what the default route `{controller=Home}/{action=Index}/{id?}` would match:</span></span>

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

<span data-ttu-id="6580f-196">`HomeController.Index()` Akce se provede pro všechny cesty adresy URL `/`, `/Home`, nebo `/Home/Index`.</span><span class="sxs-lookup"><span data-stu-id="6580f-196">The `HomeController.Index()` action will be executed for any of the URL paths `/`, `/Home`, or `/Home/Index`.</span></span>

> [!NOTE]
> <span data-ttu-id="6580f-197">Tento příklad ukazuje klíčové programovací rozdíl mezi konvenční směrování a směrování atributů.</span><span class="sxs-lookup"><span data-stu-id="6580f-197">This example highlights a key programming difference between attribute routing and conventional routing.</span></span> <span data-ttu-id="6580f-198">Směrování atributů vyžaduje další vstup k určení postupu; konvenční výchozí trasu zpracovává více stručně trasy.</span><span class="sxs-lookup"><span data-stu-id="6580f-198">Attribute routing requires more input to specify a route; the conventional default route handles routes more succinctly.</span></span> <span data-ttu-id="6580f-199">Ale směrováním atributů umožňuje (a vyžaduje) přesné řízení se vztahuje na každou akci šablon trasy.</span><span class="sxs-lookup"><span data-stu-id="6580f-199">However, attribute routing allows (and requires) precise control of which route templates apply to each action.</span></span>

<span data-ttu-id="6580f-200">Se směrováním, názvu kontroleru a akce názvy atributů Přehrát **žádné** role, ve které je vybrané akce.</span><span class="sxs-lookup"><span data-stu-id="6580f-200">With attribute routing the controller name and action names play **no** role in which action is selected.</span></span> <span data-ttu-id="6580f-201">V tomto příkladu bude odpovídat stejné adresy URL jako v předchozím příkladu.</span><span class="sxs-lookup"><span data-stu-id="6580f-201">This example will match the same URLs as the previous example.</span></span>

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
> <span data-ttu-id="6580f-202">Výše uvedené šablony trasy nebudete definovat parametry trasy pro `action`, `area`, a `controller`.</span><span class="sxs-lookup"><span data-stu-id="6580f-202">The route templates above don't define route parameters for `action`, `area`, and `controller`.</span></span> <span data-ttu-id="6580f-203">Ve skutečnosti nejsou povolené tyto parametry trasy v atribut trasy.</span><span class="sxs-lookup"><span data-stu-id="6580f-203">In fact, these route parameters are not allowed in attribute routes.</span></span> <span data-ttu-id="6580f-204">Vzhledem k tomu, že šablona trasy je již spojen s akcí, to by nedávalo smysl parsovat název akce z adresy URL.</span><span class="sxs-lookup"><span data-stu-id="6580f-204">Since the route template is already associated with an action, it wouldn't make sense to parse the action name from the URL.</span></span>

## <a name="attribute-routing-with-httpverb-attributes"></a><span data-ttu-id="6580f-205">Atribut, směrování pomocí protokolu Http [příkaz] atributy</span><span class="sxs-lookup"><span data-stu-id="6580f-205">Attribute routing with Http[Verb] attributes</span></span>

<span data-ttu-id="6580f-206">Směrování atributů lze také nastavit využívání `Http[Verb]` atributy, jako `HttpPostAttribute`.</span><span class="sxs-lookup"><span data-stu-id="6580f-206">Attribute routing can also make use of the `Http[Verb]` attributes such as `HttpPostAttribute`.</span></span> <span data-ttu-id="6580f-207">Všechny tyto atributy můžete přijmout šablonu trasy.</span><span class="sxs-lookup"><span data-stu-id="6580f-207">All of these attributes can accept a route template.</span></span> <span data-ttu-id="6580f-208">Tento příklad ukazuje dvě akce, které odpovídají stejnou šablonu trasy:</span><span class="sxs-lookup"><span data-stu-id="6580f-208">This example shows two actions that match the same route template:</span></span>

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

<span data-ttu-id="6580f-209">Pro cestu adresy URL jako `/products` `ProductsApi.ListProducts` akce se provede, když je příkazem HTTP příkaz `GET` a `ProductsApi.CreateProduct` se provede, když je příkazem HTTP příkaz `POST`.</span><span class="sxs-lookup"><span data-stu-id="6580f-209">For a URL path like `/products` the `ProductsApi.ListProducts` action will be executed when the HTTP verb is `GET` and `ProductsApi.CreateProduct` will be executed when the HTTP verb is `POST`.</span></span> <span data-ttu-id="6580f-210">Nejprve směrováním atributů odpovídá adrese URL vůči sadu šablon trasy definované atributy trasy.</span><span class="sxs-lookup"><span data-stu-id="6580f-210">Attribute routing first matches the URL against the set of route templates defined by route attributes.</span></span> <span data-ttu-id="6580f-211">Jakmile odpovídá šablonu trasy `IActionConstraint` omezení se použijí k určení akce, které mohou být provedeny.</span><span class="sxs-lookup"><span data-stu-id="6580f-211">Once a route template matches, `IActionConstraint` constraints are applied to determine which actions can be executed.</span></span>

> [!TIP]
> <span data-ttu-id="6580f-212">Při sestavování rozhraní REST API, není obvyklé, že budete chtít použít `[Route(...)]` na metodu akce.</span><span class="sxs-lookup"><span data-stu-id="6580f-212">When building a REST API, it's rare that you will want to use `[Route(...)]` on an action method.</span></span> <span data-ttu-id="6580f-213">Je vhodnější použít další konkrétní `Http*Verb*Attributes` abychom byli přesní o tom, co vaše rozhraní API podporuje.</span><span class="sxs-lookup"><span data-stu-id="6580f-213">It's better to use the more specific `Http*Verb*Attributes` to be precise about what your API supports.</span></span> <span data-ttu-id="6580f-214">Očekává se, že klienti rozhraní REST API vědět, co cesty a příkazy HTTP se mapují na konkrétní logické operace.</span><span class="sxs-lookup"><span data-stu-id="6580f-214">Clients of REST APIs are expected to know what paths and HTTP verbs map to specific logical operations.</span></span>

<span data-ttu-id="6580f-215">Protože trasa atributu, platí pro konkrétní akci, je snadné vytvořit parametry požadované jako součást definice šablony trasy.</span><span class="sxs-lookup"><span data-stu-id="6580f-215">Since an attribute route applies to a specific action, it's easy to make parameters required as part of the route template definition.</span></span> <span data-ttu-id="6580f-216">V tomto příkladu `id` je vyžadován jako součást cesty URL.</span><span class="sxs-lookup"><span data-stu-id="6580f-216">In this example, `id` is required as part of the URL path.</span></span>

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

<span data-ttu-id="6580f-217">`ProductsApi.GetProduct(int)` Akce se provede pro cestu adresy URL jako `/products/3` , ale ne pro cestu adresy URL jako `/products`.</span><span class="sxs-lookup"><span data-stu-id="6580f-217">The `ProductsApi.GetProduct(int)` action will be executed for a URL path like `/products/3` but not for a URL path like `/products`.</span></span> <span data-ttu-id="6580f-218">Zobrazit [směrování](../../fundamentals/routing.md) úplný popis šablony trasy a související možnosti.</span><span class="sxs-lookup"><span data-stu-id="6580f-218">See [Routing](../../fundamentals/routing.md) for a full description of route templates and related options.</span></span>

## <a name="route-name"></a><span data-ttu-id="6580f-219">Název trasy</span><span class="sxs-lookup"><span data-stu-id="6580f-219">Route Name</span></span>

<span data-ttu-id="6580f-220">Následující kód definuje *trasy název* z `Products_List`:</span><span class="sxs-lookup"><span data-stu-id="6580f-220">The following code  defines a *route name* of `Products_List`:</span></span>

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

<span data-ttu-id="6580f-221">Názvy tras lze použít ke generování adresy URL na základě konkrétní trasy.</span><span class="sxs-lookup"><span data-stu-id="6580f-221">Route names can be used to generate a URL based on a specific route.</span></span> <span data-ttu-id="6580f-222">Názvy tras mít vliv na porovnávání chování směrování adres URL a používají pouze pro generování adresy URL.</span><span class="sxs-lookup"><span data-stu-id="6580f-222">Route names have no impact on the URL matching behavior of routing and are only used for URL generation.</span></span> <span data-ttu-id="6580f-223">Názvy tras musí být jedinečný pro celou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="6580f-223">Route names must be unique application-wide.</span></span>

> [!NOTE]
> <span data-ttu-id="6580f-224">Rozdíl oproti to běžné *výchozí trasa*, která definuje `id` jako volitelný parametr (`{id?}`).</span><span class="sxs-lookup"><span data-stu-id="6580f-224">Contrast this with the conventional *default route*, which defines the `id` parameter as optional (`{id?}`).</span></span> <span data-ttu-id="6580f-225">Tato možnost přesně určit rozhraní API má výhody, jako je například povolení `/products` a `/products/5` odeslat na různé akce.</span><span class="sxs-lookup"><span data-stu-id="6580f-225">This ability to precisely specify APIs has advantages, such as  allowing `/products` and `/products/5` to be dispatched to different actions.</span></span>

<a name="routing-combining-ref-label"></a>

### <a name="combining-routes"></a><span data-ttu-id="6580f-226">Kombinování trasy</span><span class="sxs-lookup"><span data-stu-id="6580f-226">Combining routes</span></span>

<span data-ttu-id="6580f-227">Chcete-li směrováním atributů méně opakované, atributů tras na řadiči spolu se atributy trasy na jednotlivé akce.</span><span class="sxs-lookup"><span data-stu-id="6580f-227">To make attribute routing less repetitive, route attributes on the controller are combined with route attributes on the individual actions.</span></span> <span data-ttu-id="6580f-228">Šablony směrování na akce, které jsou před žádné šablony trasy definované v kontroleru.</span><span class="sxs-lookup"><span data-stu-id="6580f-228">Any route templates defined on the controller are prepended to route templates on the actions.</span></span> <span data-ttu-id="6580f-229">Atribut trasy umístění na kontroleru díky **všechny** akce v kontroleru pomocí směrování atributů.</span><span class="sxs-lookup"><span data-stu-id="6580f-229">Placing a route attribute on the controller makes **all** actions in the controller use attribute routing.</span></span>

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

<span data-ttu-id="6580f-230">V tomto příkladu cesty URL `/products` odpovídá `ProductsApi.ListProducts`a cesta URL `/products/5` odpovídá `ProductsApi.GetProduct(int)`.</span><span class="sxs-lookup"><span data-stu-id="6580f-230">In this example the URL path `/products` can match `ProductsApi.ListProducts`, and the URL path `/products/5` can match `ProductsApi.GetProduct(int)`.</span></span> <span data-ttu-id="6580f-231">Obě tyto akce HTTP odpovídá pouze `GET` vzhledem k tomu, že upravené pomocí `HttpGetAttribute`.</span><span class="sxs-lookup"><span data-stu-id="6580f-231">Both of these actions only match HTTP `GET` because they're decorated with the `HttpGetAttribute`.</span></span>

<span data-ttu-id="6580f-232">Směrovat šablony u akce, která začínají `/` nebo `~/` není spojit se použijí pro kontroler, šablon trasy.</span><span class="sxs-lookup"><span data-stu-id="6580f-232">Route templates applied to an action that begin with `/` or `~/` don't get combined with route templates applied to the controller.</span></span> <span data-ttu-id="6580f-233">Tento příklad porovná sadu cest URL podobně jako *trasy výchozí*.</span><span class="sxs-lookup"><span data-stu-id="6580f-233">This example matches a set of URL paths similar to the *default route*.</span></span>

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

### <a name="ordering-attribute-routes"></a><span data-ttu-id="6580f-234">Pořadí trasy atributů</span><span class="sxs-lookup"><span data-stu-id="6580f-234">Ordering attribute routes</span></span>

<span data-ttu-id="6580f-235">Na rozdíl od běžných trasy, které se spustí v zadaném pořadí směrování atributů sestavení stromu a současně odpovídá všechny trasy.</span><span class="sxs-lookup"><span data-stu-id="6580f-235">In contrast to conventional routes which execute in a defined order, attribute routing builds a tree and matches all routes simultaneously.</span></span> <span data-ttu-id="6580f-236">To se chová jako-li položky trasy byly umístěny do ideální má za výsledek řazení; nejspecifičtější trasy mít možnost spustit před obecnější trasy.</span><span class="sxs-lookup"><span data-stu-id="6580f-236">This behaves as-if the route entries were placed in an ideal ordering; the most specific routes have a chance to execute before the more general routes.</span></span>

<span data-ttu-id="6580f-237">Třeba jako trasu `blog/search/{topic}` je konkrétnější než trasa jako `blog/{*article}`.</span><span class="sxs-lookup"><span data-stu-id="6580f-237">For example, a route like `blog/search/{topic}` is more specific than a route like `blog/{*article}`.</span></span> <span data-ttu-id="6580f-238">Logicky mluvený `blog/search/{topic}` trasy "", nejprve ve výchozím nastavení spustí, protože se jedná pouze rozumné řazení.</span><span class="sxs-lookup"><span data-stu-id="6580f-238">Logically speaking the `blog/search/{topic}` route 'runs' first, by default, because that's the only sensible ordering.</span></span> <span data-ttu-id="6580f-239">Použití konvenční směrování, vývojář je odpovědná za umístění trasy do požadovaného pořadí.</span><span class="sxs-lookup"><span data-stu-id="6580f-239">Using conventional routing, the developer is  responsible for placing routes in the desired order.</span></span>

<span data-ttu-id="6580f-240">Atribut trasy můžete nakonfigurovat pořadí, pomocí `Order` vlastnost všechny atributy rámec poskytovaný trasy.</span><span class="sxs-lookup"><span data-stu-id="6580f-240">Attribute routes can configure an order, using the `Order` property of all of the framework provided route attributes.</span></span> <span data-ttu-id="6580f-241">Trasy se zpracovávají podle vzestupném ze na `Order` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="6580f-241">Routes are processed according to an ascending sort of the `Order` property.</span></span> <span data-ttu-id="6580f-242">Výchozí pořadí je `0`.</span><span class="sxs-lookup"><span data-stu-id="6580f-242">The default order is `0`.</span></span> <span data-ttu-id="6580f-243">Nastavení směrování pomocí `Order = -1` bude spuštěn před trasy, které nemají nastavený objednávky.</span><span class="sxs-lookup"><span data-stu-id="6580f-243">Setting a route using `Order = -1` will run before routes that don't set an order.</span></span> <span data-ttu-id="6580f-244">Nastavení směrování pomocí `Order = 1` se spustí po změně pořadí výchozí trasy.</span><span class="sxs-lookup"><span data-stu-id="6580f-244">Setting a route using `Order = 1` will run after default route ordering.</span></span>

> [!TIP]
> <span data-ttu-id="6580f-245">Vyhněte se v závislosti na `Order`.</span><span class="sxs-lookup"><span data-stu-id="6580f-245">Avoid depending on `Order`.</span></span> <span data-ttu-id="6580f-246">Pokud váš prostor adresy URL vyžaduje explicitní seřazení hodnot pro směrování správně, je pravděpodobně matoucí také klientům.</span><span class="sxs-lookup"><span data-stu-id="6580f-246">If your URL-space requires explicit order values to route correctly, then it's likely confusing to clients as well.</span></span> <span data-ttu-id="6580f-247">Směrování atributů obecně bude vyberte správné směrování s odpovídajícími adresy URL.</span><span class="sxs-lookup"><span data-stu-id="6580f-247">In general attribute routing will select the correct route with URL matching.</span></span> <span data-ttu-id="6580f-248">Pokud nefunguje výchozí pořadí použili pro generování adresy URL, pomocí názvu trasy, je obvykle jednodušší než použití přepsání `Order` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="6580f-248">If the default order used for URL generation isn't working, using route name as an override is usually simpler than applying the `Order` property.</span></span>

<span data-ttu-id="6580f-249">Stránky Razor směrování a směrování sdílení řadiče MVC implementace.</span><span class="sxs-lookup"><span data-stu-id="6580f-249">Razor Pages routing and MVC controller routing share an implementation.</span></span> <span data-ttu-id="6580f-250">Informace o pořadí trasy v tématech pro stránky Razor je k dispozici na [trasy a aplikační konvence pro stránky Razor: Pořadí trasy](xref:razor-pages/razor-pages-conventions#route-order).</span><span class="sxs-lookup"><span data-stu-id="6580f-250">Information on route order in the Razor Pages topics is available at [Razor Pages route and app conventions: Route order](xref:razor-pages/razor-pages-conventions#route-order).</span></span>

<a name="routing-token-replacement-templates-ref-label"></a>

## <a name="token-replacement-in-route-templates-controller-action-area"></a><span data-ttu-id="6580f-251">Token nahrazení v šablonách tras ([kontroler], [action] [Oblast])</span><span class="sxs-lookup"><span data-stu-id="6580f-251">Token replacement in route templates ([controller], [action], [area])</span></span>

<span data-ttu-id="6580f-252">Pro usnadnění práce trasy atributů podporují *token nahrazení* uzavřením token do hranatých závorek (`[`, `]`).</span><span class="sxs-lookup"><span data-stu-id="6580f-252">For convenience, attribute routes support *token replacement* by enclosing a token in square-braces (`[`, `]`).</span></span> <span data-ttu-id="6580f-253">Tokeny `[action]`, `[area]`, a `[controller]` se nahradí hodnotami názvu akce, názvu oblasti a názvu kontroleru z akce, kde je definován trasy.</span><span class="sxs-lookup"><span data-stu-id="6580f-253">The tokens `[action]`, `[area]`, and `[controller]` are replaced with the values of the action name, area name, and controller name from the action where the route is defined.</span></span> <span data-ttu-id="6580f-254">V následujícím příkladu akce odpovídat cesty adresy URL, jak je popsáno v komentářích:</span><span class="sxs-lookup"><span data-stu-id="6580f-254">In the following example, the actions match URL paths as described in the comments:</span></span>

[!code-csharp[](routing/sample/main/Controllers/ProductsController.cs?range=7-11,13-17,20-22)]

<span data-ttu-id="6580f-255">Vyvolá se v posledním kroku vytváření trasy atributů náhradních tokenů.</span><span class="sxs-lookup"><span data-stu-id="6580f-255">Token replacement occurs as the last step of building the attribute routes.</span></span> <span data-ttu-id="6580f-256">Výše uvedeném příkladu se bude chovat stejně jako následující kód:</span><span class="sxs-lookup"><span data-stu-id="6580f-256">The above example will behave the same as the following code:</span></span>

[!code-csharp[](routing/sample/main/Controllers/ProductsController2.cs?range=7-11,13-17,20-22)]

<span data-ttu-id="6580f-257">Atribut trasy můžete také kombinovat s dědičnosti.</span><span class="sxs-lookup"><span data-stu-id="6580f-257">Attribute routes can also be combined with inheritance.</span></span> <span data-ttu-id="6580f-258">To je zvláště efektivní, v kombinaci s náhradních tokenů.</span><span class="sxs-lookup"><span data-stu-id="6580f-258">This is particularly powerful combined with token replacement.</span></span>

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

<span data-ttu-id="6580f-259">Nahrazování tokenů platí také pro názvy tras definován atribut trasy.</span><span class="sxs-lookup"><span data-stu-id="6580f-259">Token replacement also applies to route names defined by attribute routes.</span></span> <span data-ttu-id="6580f-260">`[Route("[controller]/[action]", Name="[controller]_[action]")]` vygeneruje název jedinečný trasa pro každou akci.</span><span class="sxs-lookup"><span data-stu-id="6580f-260">`[Route("[controller]/[action]", Name="[controller]_[action]")]` generates a unique route name for each action.</span></span>

<span data-ttu-id="6580f-261">Tak, aby odpovídaly oddělovač literální nahrazení tokenu `[` nebo `]`, řídicí znak opakováním (`[[` nebo `]]`).</span><span class="sxs-lookup"><span data-stu-id="6580f-261">To match the literal token replacement delimiter `[` or  `]`, escape it by repeating the character (`[[` or `]]`).</span></span>

::: moniker range=">= aspnetcore-2.2"

<a name="routing-token-replacement-transformers-ref-label"></a>

### <a name="use-a-parameter-transformer-to-customize-token-replacement"></a><span data-ttu-id="6580f-262">Použití transformeru parametr k přizpůsobení náhradních tokenů</span><span class="sxs-lookup"><span data-stu-id="6580f-262">Use a parameter transformer to customize token replacement</span></span>

<span data-ttu-id="6580f-263">Použití transformeru parametr, je možné přizpůsobit náhradních tokenů.</span><span class="sxs-lookup"><span data-stu-id="6580f-263">Token replacement can be customized using a parameter transformer.</span></span> <span data-ttu-id="6580f-264">Parametr transformer implementuje `IOutboundParameterTransformer` a transformuje hodnoty parametrů.</span><span class="sxs-lookup"><span data-stu-id="6580f-264">A parameter transformer implements `IOutboundParameterTransformer` and transforms the value of parameters.</span></span> <span data-ttu-id="6580f-265">Například vlastní `SlugifyParameterTransformer` parametr transformer změny `SubscriptionManagement` trasy hodnota, která má `subscription-management`.</span><span class="sxs-lookup"><span data-stu-id="6580f-265">For example, a custom `SlugifyParameterTransformer` parameter transformer changes the `SubscriptionManagement` route value to `subscription-management`.</span></span>

<span data-ttu-id="6580f-266">`RouteTokenTransformerConvention` Je vytváření modelu aplikace, které:</span><span class="sxs-lookup"><span data-stu-id="6580f-266">The `RouteTokenTransformerConvention` is an application model convention that:</span></span>

* <span data-ttu-id="6580f-267">Parametr transformer platí pro všechny trasy atributů v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="6580f-267">Applies a parameter transformer to all attribute routes in an application.</span></span>
* <span data-ttu-id="6580f-268">Přizpůsobí atribut token hodnoty trasy jako se nahradí.</span><span class="sxs-lookup"><span data-stu-id="6580f-268">Customizes the attribute route token values as they are replaced.</span></span>

```csharp
public class SubscriptionManagementController : Controller
{
    [HttpGet("[controller]/[action]")] // Matches '/subscription-management/list-all'
    public IActionResult ListAll() { ... }
}
```

<span data-ttu-id="6580f-269">`RouteTokenTransformerConvention` Je zaregistrovaný jako možnost v `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="6580f-269">The `RouteTokenTransformerConvention` is registered as an option in `ConfigureServices`.</span></span>

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

### <a name="multiple-routes"></a><span data-ttu-id="6580f-270">Několik tras</span><span class="sxs-lookup"><span data-stu-id="6580f-270">Multiple Routes</span></span>

<span data-ttu-id="6580f-271">Atribut směrování podporuje určení několik tras, které jsou poskytovány stejnou akci.</span><span class="sxs-lookup"><span data-stu-id="6580f-271">Attribute routing supports defining multiple routes that reach the same action.</span></span> <span data-ttu-id="6580f-272">Nejběžnější použití tohoto objektu je tak, aby napodoboval chování *výchozí trasa konvenční* jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="6580f-272">The most common usage of this is to mimic the behavior of the *default conventional route* as shown in the following example:</span></span>

```csharp
[Route("[controller]")]
public class ProductsController : Controller
{
   [Route("")]     // Matches 'Products'
   [Route("Index")] // Matches 'Products/Index'
   public IActionResult Index()
}
```

<span data-ttu-id="6580f-273">Uvedení několika atributů tras na řadiči znamená, že každé z nich bude kombinovat s každého z atributů trasy na metody akce.</span><span class="sxs-lookup"><span data-stu-id="6580f-273">Putting multiple route attributes on the controller means that each one will combine with each of the route attributes on the action methods.</span></span>

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

<span data-ttu-id="6580f-274">Po několika atributů tras (, které implementují `IActionConstraint`) jsou umístěné na akci, pak každá akce omezení spojuje se šablona trasy z atributu, který je definován.</span><span class="sxs-lookup"><span data-stu-id="6580f-274">When multiple route attributes (that implement `IActionConstraint`) are placed on an action, then each action constraint combines with the route template from the attribute that defined it.</span></span>

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
> <span data-ttu-id="6580f-275">Při použití více tras na akce se může zdát výkonné, je lepší byly místo adresy URL vaší aplikace, jednoduché a dobře definovaný.</span><span class="sxs-lookup"><span data-stu-id="6580f-275">While using multiple routes on actions can seem powerful, it's better to keep your application's URL space simple and well-defined.</span></span> <span data-ttu-id="6580f-276">Použijte několik tras na akce, pouze v případě potřeby, například k podpoře existujících klientů.</span><span class="sxs-lookup"><span data-stu-id="6580f-276">Use multiple routes on actions only where needed, for example to support existing clients.</span></span>

<a name="routing-attr-options"></a>

### <a name="specifying-attribute-route-optional-parameters-default-values-and-constraints"></a><span data-ttu-id="6580f-277">Určení atribut trasy volitelné parametry, výchozí hodnoty a omezení</span><span class="sxs-lookup"><span data-stu-id="6580f-277">Specifying attribute route optional parameters, default values, and constraints</span></span>

<span data-ttu-id="6580f-278">Atribut trasy podporují stejné vložená syntaxe jako konvenční trasy a určit volitelné parametry, výchozí hodnoty a omezení.</span><span class="sxs-lookup"><span data-stu-id="6580f-278">Attribute routes support the same inline syntax as conventional routes to specify optional parameters, default values, and constraints.</span></span>

```csharp
[HttpPost("product/{id:int}")]
public IActionResult ShowProduct(int id)
{
   // ...
}
```

<span data-ttu-id="6580f-279">V tématu [trasy referenčními informacemi k šablonám](../../fundamentals/routing.md#route-template-reference) podrobný popis syntaxe šablon trasy.</span><span class="sxs-lookup"><span data-stu-id="6580f-279">See [Route Template Reference](../../fundamentals/routing.md#route-template-reference) for a detailed description of route template syntax.</span></span>

<a name="routing-cust-rt-attr-irt-ref-label"></a>

### <a name="custom-route-attributes-using-iroutetemplateprovider"></a><span data-ttu-id="6580f-280">Vlastní trasy atributů s použitím `IRouteTemplateProvider`</span><span class="sxs-lookup"><span data-stu-id="6580f-280">Custom route attributes using `IRouteTemplateProvider`</span></span>

<span data-ttu-id="6580f-281">Všechny atributy trasy k dispozici v rámci ( `[Route(...)]`, `[HttpGet(...)]` atd) implementace `IRouteTemplateProvider` rozhraní.</span><span class="sxs-lookup"><span data-stu-id="6580f-281">All of the route attributes provided in the framework ( `[Route(...)]`, `[HttpGet(...)]` , etc.) implement the `IRouteTemplateProvider` interface.</span></span> <span data-ttu-id="6580f-282">MVC hledá atributy u třídy kontroleru a metody akce při spuštění aplikace a používá ty, které implementují `IRouteTemplateProvider` vytvářet počáteční sadu trasy.</span><span class="sxs-lookup"><span data-stu-id="6580f-282">MVC looks for attributes on controller classes and action methods when the app starts and uses the ones that implement `IRouteTemplateProvider` to build the initial set of routes.</span></span>

<span data-ttu-id="6580f-283">Můžete implementovat `IRouteTemplateProvider` definovat vlastní atributy trasy.</span><span class="sxs-lookup"><span data-stu-id="6580f-283">You can implement `IRouteTemplateProvider` to define your own route attributes.</span></span> <span data-ttu-id="6580f-284">Každý `IRouteTemplateProvider` vám umožní definovat jednu trasu šablonou vlastní trasy, pořadí a název:</span><span class="sxs-lookup"><span data-stu-id="6580f-284">Each `IRouteTemplateProvider` allows you to define a single route with a custom route template, order, and name:</span></span>

```csharp
public class MyApiControllerAttribute : Attribute, IRouteTemplateProvider
{
   public string Template => "api/[controller]";

   public int? Order { get; set; }

   public string Name { get; set; }
}
```

<span data-ttu-id="6580f-285">Atribut z výše uvedeném příkladu se automaticky nastaví `Template` k `"api/[controller]"` při `[MyApiController]` platí.</span><span class="sxs-lookup"><span data-stu-id="6580f-285">The attribute from the above example automatically sets the `Template` to `"api/[controller]"` when `[MyApiController]` is applied.</span></span>

<a name="routing-app-model-ref-label"></a>

### <a name="using-application-model-to-customize-attribute-routes"></a><span data-ttu-id="6580f-286">Přizpůsobení trasy atributů pomocí aplikačního modelu</span><span class="sxs-lookup"><span data-stu-id="6580f-286">Using Application Model to customize attribute routes</span></span>

<span data-ttu-id="6580f-287">*Aplikační model* je vytvořen při spuštění se všemi metadat používané MVC pro směrování a provádění akcí objektový model.</span><span class="sxs-lookup"><span data-stu-id="6580f-287">The *application model* is an object model created at startup with all of the metadata used by MVC to route and execute your actions.</span></span> <span data-ttu-id="6580f-288">*Aplikační model* zahrnuje všechna data shromážděná z atributů tras (prostřednictvím `IRouteTemplateProvider`).</span><span class="sxs-lookup"><span data-stu-id="6580f-288">The *application model* includes all of the data gathered from route attributes (through `IRouteTemplateProvider`).</span></span> <span data-ttu-id="6580f-289">Můžete napsat *konvence* upravit aplikační model v době spuštění k přizpůsobení chování směrování.</span><span class="sxs-lookup"><span data-stu-id="6580f-289">You can write *conventions* to modify the application model at startup time to customize how routing behaves.</span></span> <span data-ttu-id="6580f-290">Tato část ukazuje jednoduchý příklad přizpůsobení, směrování pomocí aplikačního modelu.</span><span class="sxs-lookup"><span data-stu-id="6580f-290">This section shows a simple example of customizing routing using application model.</span></span>

[!code-csharp[](routing/sample/main/NamespaceRoutingConvention.cs)]

<a name="routing-mixed-ref-label"></a>

## <a name="mixed-routing-attribute-routing-vs-conventional-routing"></a><span data-ttu-id="6580f-291">Smíšené směrování: Atribut směrování vs konvenční směrování</span><span class="sxs-lookup"><span data-stu-id="6580f-291">Mixed routing: Attribute routing vs conventional routing</span></span>

<span data-ttu-id="6580f-292">Aplikace MVC můžete kombinovat použití konvenční směrování a směrování atributů.</span><span class="sxs-lookup"><span data-stu-id="6580f-292">MVC applications can mix the use of conventional routing and attribute routing.</span></span> <span data-ttu-id="6580f-293">Je typické použití konvenční trasy pro kontrolery obsluhující stránky HTML pro prohlížeče, a atribut směrování pro kontrolery slouží rozhraní REST API.</span><span class="sxs-lookup"><span data-stu-id="6580f-293">It's typical to use conventional routes for controllers serving HTML pages for browsers, and attribute routing for controllers serving REST APIs.</span></span>

<span data-ttu-id="6580f-294">Akce jsou buď konvenčně směrovat nebo atribut směrovat.</span><span class="sxs-lookup"><span data-stu-id="6580f-294">Actions are either conventionally routed or attribute routed.</span></span> <span data-ttu-id="6580f-295">Umístění trasy na kontroler nebo akce umožňuje směrovat atribut.</span><span class="sxs-lookup"><span data-stu-id="6580f-295">Placing a route on the controller or the action makes it attribute routed.</span></span> <span data-ttu-id="6580f-296">Akce, které definují trasy atributů není dostupný prostřednictvím konvenční trasy a naopak.</span><span class="sxs-lookup"><span data-stu-id="6580f-296">Actions that define attribute routes cannot be reached through the conventional routes and vice-versa.</span></span> <span data-ttu-id="6580f-297">**Žádné** atribut trasy na řadiči provede všechny akce v kontroleru atribut směrovat.</span><span class="sxs-lookup"><span data-stu-id="6580f-297">**Any** route attribute on the controller makes all actions in the controller attribute routed.</span></span>

> [!NOTE]
> <span data-ttu-id="6580f-298">Co rozlišuje dva typy směrování systémů je proces použijí po adresa URL odpovídá šablonu trasy.</span><span class="sxs-lookup"><span data-stu-id="6580f-298">What distinguishes the two types of routing systems is the process applied after a URL matches a route template.</span></span> <span data-ttu-id="6580f-299">V konvenční směrování, se používají hodnoty trasy ze shody lze vybírat vyhledávací tabulky všech akcí konvenční směrované akce a kontroler.</span><span class="sxs-lookup"><span data-stu-id="6580f-299">In conventional routing, the route values from the match are used to choose the action and controller from a lookup table of all conventional routed actions.</span></span> <span data-ttu-id="6580f-300">V směrování atributů, každá šablona je již přidružen akce a je potřeba žádné další vyhledávání.</span><span class="sxs-lookup"><span data-stu-id="6580f-300">In attribute routing, each template is already associated with an action, and no further lookup is needed.</span></span>

## <a name="complex-segments"></a><span data-ttu-id="6580f-301">Komplexní segmenty</span><span class="sxs-lookup"><span data-stu-id="6580f-301">Complex segments</span></span>

<span data-ttu-id="6580f-302">Komplexní segmentech (například `[Route("/dog{token}cat")]`), se zpracovávají to provede spárováním odpovídajících nahoru literály zprava doleva bez metody greedy způsobem.</span><span class="sxs-lookup"><span data-stu-id="6580f-302">Complex segments (for example, `[Route("/dog{token}cat")]`), are processed by matching up literals from right to left in a non-greedy way.</span></span> <span data-ttu-id="6580f-303">Zobrazit [zdrojový kód](https://github.com/aspnet/Routing/blob/9cea167cfac36cf034dbb780e3f783114ef94780/src/Microsoft.AspNetCore.Routing/Patterns/RoutePatternMatcher.cs#L296) popis.</span><span class="sxs-lookup"><span data-stu-id="6580f-303">See [the source code](https://github.com/aspnet/Routing/blob/9cea167cfac36cf034dbb780e3f783114ef94780/src/Microsoft.AspNetCore.Routing/Patterns/RoutePatternMatcher.cs#L296) for a description.</span></span> <span data-ttu-id="6580f-304">Další informace najdete v tématu [tento problém](https://github.com/aspnet/Docs/issues/8197).</span><span class="sxs-lookup"><span data-stu-id="6580f-304">For more information, see [this issue](https://github.com/aspnet/Docs/issues/8197).</span></span>

<a name="routing-url-gen-ref-label"></a>

## <a name="url-generation"></a><span data-ttu-id="6580f-305">Generování adresy URL</span><span class="sxs-lookup"><span data-stu-id="6580f-305">URL Generation</span></span>

<span data-ttu-id="6580f-306">Aplikace MVC slouží ke generování adresy URL odkazů na akce směrování pro funkce generování adresy URL.</span><span class="sxs-lookup"><span data-stu-id="6580f-306">MVC applications can use routing's URL generation features to generate URL links to actions.</span></span> <span data-ttu-id="6580f-307">Generování adresy URL eliminuje hardcoding adresy URL, provádění kódu, robustní a udržovatelný.</span><span class="sxs-lookup"><span data-stu-id="6580f-307">Generating URLs eliminates hardcoding URLs, making your code more robust and maintainable.</span></span> <span data-ttu-id="6580f-308">Tato část se zaměřuje na Funkce generování adresy URL poskytnuté MVC a bude pouze zabývat základy fungování generování adresy URL.</span><span class="sxs-lookup"><span data-stu-id="6580f-308">This section focuses on the URL generation features provided by MVC and will only cover basics of how URL generation works.</span></span> <span data-ttu-id="6580f-309">Zobrazit [směrování](../../fundamentals/routing.md) podrobný popis generování adresy URL.</span><span class="sxs-lookup"><span data-stu-id="6580f-309">See [Routing](../../fundamentals/routing.md) for a detailed description of URL generation.</span></span>

<span data-ttu-id="6580f-310">`IUrlHelper` Rozhraní je základní část infrastruktury mezi MVC a směrování pro generování adresy URL.</span><span class="sxs-lookup"><span data-stu-id="6580f-310">The `IUrlHelper` interface is the underlying piece of infrastructure between MVC and routing for URL generation.</span></span> <span data-ttu-id="6580f-311">Zjistíte instance `IUrlHelper` k dispozici prostřednictvím `Url` vlastnost v řadiči, zobrazení a zobrazení komponenty.</span><span class="sxs-lookup"><span data-stu-id="6580f-311">You'll find an instance of `IUrlHelper` available through the `Url` property in controllers, views, and view components.</span></span>

<span data-ttu-id="6580f-312">V tomto příkladu `IUrlHelper` rozhraní se používá prostřednictvím `Controller.Url` vlastnost ke generování adresy URL pro jiná akce.</span><span class="sxs-lookup"><span data-stu-id="6580f-312">In this example, the `IUrlHelper` interface is used through the `Controller.Url` property to generate a URL to another action.</span></span>

[!code-csharp[](routing/sample/main/Controllers/UrlGenerationController.cs?name=snippet_1)]

<span data-ttu-id="6580f-313">Pokud aplikace používá výchozí konvenční směrovat, hodnota `url` proměnná bude řetězec cesty adresy URL `/UrlGeneration/Destination`.</span><span class="sxs-lookup"><span data-stu-id="6580f-313">If the application is using the default conventional route, the value of the `url` variable will be the URL path string `/UrlGeneration/Destination`.</span></span> <span data-ttu-id="6580f-314">Tato cesta URL je vytvořené směrování kombinací hodnot trasy z aktuální žádosti (okolí hodnoty), obsahuje hodnotu předanou do `Url.Action` a nahraďte hodnoty v šabloně trasy:</span><span class="sxs-lookup"><span data-stu-id="6580f-314">This URL path is created by routing by combining the route values from the current request (ambient values), with the values passed to `Url.Action` and substituting those values into the route template:</span></span>

```
ambient values: { controller = "UrlGeneration", action = "Source" }
values passed to Url.Action: { controller = "UrlGeneration", action = "Destination" }
route template: {controller}/{action}/{id?}

result: /UrlGeneration/Destination
```

<span data-ttu-id="6580f-315">Každý parametr trasa v šabloně trasy je jeho hodnota nahradí odpovídající názvy s hodnotami a okolí hodnoty.</span><span class="sxs-lookup"><span data-stu-id="6580f-315">Each route parameter in the route template has its value substituted by matching names with the values and ambient values.</span></span> <span data-ttu-id="6580f-316">Parametr trasy, která nemá hodnotu můžete použít výchozí hodnotu, pokud má jeden, nebo přeskočit, pokud je volitelný (stejně jako v případě třídy `id` v tomto příkladu).</span><span class="sxs-lookup"><span data-stu-id="6580f-316">A route parameter that doesn't have a value can use a default value if it has one, or be skipped if it's optional (as in the case of `id` in this example).</span></span> <span data-ttu-id="6580f-317">Generování adresy URL se nezdaří, pokud všechny požadované trasy parametr nemá odpovídající hodnotu.</span><span class="sxs-lookup"><span data-stu-id="6580f-317">URL generation will fail if any required route parameter doesn't have a corresponding value.</span></span> <span data-ttu-id="6580f-318">Pokud selže generování adresy URL pro trasu, zkusí se další směrování, dokud vyzkoušeny všechny trasy nebo se najde shoda.</span><span class="sxs-lookup"><span data-stu-id="6580f-318">If URL generation fails for a route, the next route is tried until all routes have been tried or a match is found.</span></span>

<span data-ttu-id="6580f-319">Příklad `Url.Action` uvedená výše předpokládá konvenční směrování, ale adresa URL generování funguje podobně se směrováním atributů, i když popsané koncepty se jiný.</span><span class="sxs-lookup"><span data-stu-id="6580f-319">The example of `Url.Action` above assumes conventional routing, but URL generation works similarly with attribute routing, though the concepts are different.</span></span> <span data-ttu-id="6580f-320">S konvenčním směrování, hodnoty trasy umožňují rozšířit šablonu a hodnot trasy pro `controller` a `action` obvykle zobrazují v šabloně – to funguje, protože odpovídající směrování adres URL dodržovat *konvence*.</span><span class="sxs-lookup"><span data-stu-id="6580f-320">With conventional routing, the route values are used to expand a template, and the route values for `controller` and `action` usually appear in that template - this works because the URLs matched by routing adhere to a *convention*.</span></span> <span data-ttu-id="6580f-321">V atributu směrování, hodnoty trasy `controller` a `action` nepovolují se zobrazí v šabloně – místo toho slouží k vyhledání, která šablona se má použít.</span><span class="sxs-lookup"><span data-stu-id="6580f-321">In attribute routing, the route values for `controller` and `action` are not allowed to appear in the template - they're instead used to look up which template to use.</span></span>

<span data-ttu-id="6580f-322">Tento příklad používá směrování atributů:</span><span class="sxs-lookup"><span data-stu-id="6580f-322">This example uses attribute routing:</span></span>

[!code-csharp[](routing/sample/main/StartupUseMvc.cs?name=snippet_1)]

[!code-csharp[](routing/sample/main/Controllers/UrlGenerationControllerAttr.cs?name=snippet_1)]

<span data-ttu-id="6580f-323">Vytvoří vyhledávací tabulku všech akcí atribut směrovat MVC a bude odpovídat `controller` a `action` hodnoty a vyberte šablonu trasy pro generování adresy URL.</span><span class="sxs-lookup"><span data-stu-id="6580f-323">MVC builds a lookup table of all attribute routed actions and will match the `controller` and `action` values to select the route template to use for URL generation.</span></span> <span data-ttu-id="6580f-324">V příkladu výše `custom/url/to/destination` je generován.</span><span class="sxs-lookup"><span data-stu-id="6580f-324">In the sample above,   `custom/url/to/destination` is generated.</span></span>

### <a name="generating-urls-by-action-name"></a><span data-ttu-id="6580f-325">Generování adresy URL pomocí názvu akce</span><span class="sxs-lookup"><span data-stu-id="6580f-325">Generating URLs by action name</span></span>

<span data-ttu-id="6580f-326">`Url.Action` (`IUrlHelper` .</span><span class="sxs-lookup"><span data-stu-id="6580f-326">`Url.Action` (`IUrlHelper` .</span></span> <span data-ttu-id="6580f-327">`Action`) a všech souvisejících přetížení všechny jsou založeny na tento nápad, že chcete určit, co při připojování ke zadáním názvu kontroleru a názvu akce.</span><span class="sxs-lookup"><span data-stu-id="6580f-327">`Action`) and all related overloads all are based on that idea that you want to specify what you're linking to by specifying a controller name and action name.</span></span>

> [!NOTE]
> <span data-ttu-id="6580f-328">Při použití `Url.Action`, aktuální trasy hodnoty `controller` a `action` jsou určené pro vás – hodnota `controller` a `action` jsou součástí obou *okolí hodnoty* **a** *hodnoty*.</span><span class="sxs-lookup"><span data-stu-id="6580f-328">When using `Url.Action`, the current route values for `controller` and `action` are specified for you - the value of `controller` and `action` are part of both *ambient values* **and** *values*.</span></span> <span data-ttu-id="6580f-329">Metoda `Url.Action`, vždy používá aktuální hodnoty `action` a `controller` a budou vytvářet cesty adresy URL, která směruje na aktuální akci.</span><span class="sxs-lookup"><span data-stu-id="6580f-329">The method `Url.Action`, always uses the current values of `action` and `controller` and will generate a URL path that routes to the current action.</span></span>

<span data-ttu-id="6580f-330">Směrování pokusy, aby použil hodnoty v okolí hodnoty k vyplnění informací, které nebyly poskytují při generování adresy URL.</span><span class="sxs-lookup"><span data-stu-id="6580f-330">Routing attempts to use the values in ambient values to fill in information that you didn't provide when generating a URL.</span></span> <span data-ttu-id="6580f-331">Nepřidávejte jako `{a}/{b}/{c}/{d}` a okolí hodnoty `{ a = Alice, b = Bob, c = Carol, d = David }`, směrování má dostatek informací ke generování adresy URL bez jakékoli další hodnoty – protože směrovat všechny parametry mají hodnotu.</span><span class="sxs-lookup"><span data-stu-id="6580f-331">Using a route like `{a}/{b}/{c}/{d}` and ambient values `{ a = Alice, b = Bob, c = Carol, d = David }`, routing has enough information to generate a URL without any additional values - since all route parameters have a value.</span></span> <span data-ttu-id="6580f-332">Pokud jste přidali hodnota `{ d = Donovan }`, hodnota `{ d = David }` by být ignorovány, a vygenerovanou cestu adresy URL by `Alice/Bob/Carol/Donovan`.</span><span class="sxs-lookup"><span data-stu-id="6580f-332">If you added the value `{ d = Donovan }`, the value `{ d = David }` would be ignored, and the generated URL path would be `Alice/Bob/Carol/Donovan`.</span></span>

> [!WARNING]
> <span data-ttu-id="6580f-333">Cesty adresy URL jsou hierarchická.</span><span class="sxs-lookup"><span data-stu-id="6580f-333">URL paths are hierarchical.</span></span> <span data-ttu-id="6580f-334">V příkladu výše, pokud jste přidali hodnota `{ c = Cheryl }`, obě hodnoty `{ c = Carol, d = David }` bude ignorován.</span><span class="sxs-lookup"><span data-stu-id="6580f-334">In the example above, if you added the value `{ c = Cheryl }`, both of the values `{ c = Carol, d = David }` would be ignored.</span></span> <span data-ttu-id="6580f-335">V tomto případě jsme už nebude mít hodnotu `d` a generování adresy URL se nezdaří.</span><span class="sxs-lookup"><span data-stu-id="6580f-335">In this case we no longer have a value for `d` and URL generation will fail.</span></span> <span data-ttu-id="6580f-336">Je třeba zadat hodnotu požadovaného `c` a `d`.</span><span class="sxs-lookup"><span data-stu-id="6580f-336">You would need to specify the desired value of `c` and `d`.</span></span>  <span data-ttu-id="6580f-337">Můžete očekávat, že přístupů tento problém se výchozí trasa (`{controller}/{action}/{id?}`) – tomuto chování v praxi jako bude docházet zřídka, ale `Url.Action` bude vždy explicitně určete `controller` a `action` hodnotu.</span><span class="sxs-lookup"><span data-stu-id="6580f-337">You might expect to hit this problem with the default route (`{controller}/{action}/{id?}`) - but you will rarely encounter this behavior in practice as `Url.Action` will always explicitly specify a `controller` and `action` value.</span></span>

<span data-ttu-id="6580f-338">Delší přetížení `Url.Action` taky využít další *hodnot trasy* objektu k poskytnutí hodnot pro parametry trasy jiné než `controller` a `action`.</span><span class="sxs-lookup"><span data-stu-id="6580f-338">Longer overloads of `Url.Action` also take an additional *route values* object to provide values for route parameters other than `controller` and `action`.</span></span> <span data-ttu-id="6580f-339">Nejčastěji to použít s se zobrazí `id` jako `Url.Action("Buy", "Products", new { id = 17 })`.</span><span class="sxs-lookup"><span data-stu-id="6580f-339">You will most commonly see this used with `id` like `Url.Action("Buy", "Products", new { id = 17 })`.</span></span> <span data-ttu-id="6580f-340">Podle konvence *hodnot trasy* objekt je obvykle objekt anonymního typu, ale může být `IDictionary<>` nebo *prostý původní objekt .NET*.</span><span class="sxs-lookup"><span data-stu-id="6580f-340">By convention the *route values* object is usually an object of anonymous type, but it can also be an `IDictionary<>` or a *plain old .NET object*.</span></span> <span data-ttu-id="6580f-341">Žádné další trasy hodnoty, které neodpovídají parametry trasy jsou umístěny v řetězci dotazu.</span><span class="sxs-lookup"><span data-stu-id="6580f-341">Any additional route values that don't match route parameters are put in the query string.</span></span>

[!code-csharp[](routing/sample/main/Controllers/TestController.cs)]

> [!TIP]
> <span data-ttu-id="6580f-342">Absolutní adresu URL vytvoříte pomocí přetížení přijímající `protocol`: `Url.Action("Buy", "Products", new { id = 17 }, protocol: Request.Scheme)`</span><span class="sxs-lookup"><span data-stu-id="6580f-342">To create an absolute URL, use an overload that accepts a `protocol`: `Url.Action("Buy", "Products", new { id = 17 }, protocol: Request.Scheme)`</span></span>

<a name="routing-gen-urls-route-ref-label"></a>

### <a name="generating-urls-by-route"></a><span data-ttu-id="6580f-343">Generuje adresy URL trasy</span><span class="sxs-lookup"><span data-stu-id="6580f-343">Generating URLs by route</span></span>

<span data-ttu-id="6580f-344">Výše uvedený kód jsme vám ukázali, generování adresy URL předáním názvu kontroleru a akce.</span><span class="sxs-lookup"><span data-stu-id="6580f-344">The code above demonstrated generating a URL by passing in the controller and action name.</span></span> <span data-ttu-id="6580f-345">`IUrlHelper` poskytuje také `Url.RouteUrl` řady metod.</span><span class="sxs-lookup"><span data-stu-id="6580f-345">`IUrlHelper` also provides the `Url.RouteUrl` family of methods.</span></span> <span data-ttu-id="6580f-346">Tyto metody jsou podobné `Url.Action`, ale jejich aktuálními hodnotami nekopírujte `action` a `controller` hodnoty trasy.</span><span class="sxs-lookup"><span data-stu-id="6580f-346">These methods are similar to `Url.Action`, but they don't copy the current values of `action` and `controller` to the route values.</span></span> <span data-ttu-id="6580f-347">Nejběžnější použití je zadání názvu trasy pro konkrétní trasu použít ke generování adresy URL, obecně *bez* zadáním názvu kontroler nebo akce.</span><span class="sxs-lookup"><span data-stu-id="6580f-347">The most common usage is to specify a route name to use a specific route to generate the URL, generally *without* specifying a controller or action name.</span></span>

[!code-csharp[](routing/sample/main/Controllers/UrlGenerationControllerRouting.cs?name=snippet_1)]

<a name="routing-gen-urls-html-ref-label"></a>

### <a name="generating-urls-in-html"></a><span data-ttu-id="6580f-348">Generování adresy URL ve formátu HTML</span><span class="sxs-lookup"><span data-stu-id="6580f-348">Generating URLs in HTML</span></span>

<span data-ttu-id="6580f-349">`IHtmlHelper` poskytuje `HtmlHelper` metody `Html.BeginForm` a `Html.ActionLink` ke generování `<form>` a `<a>` prvky v uvedeném pořadí.</span><span class="sxs-lookup"><span data-stu-id="6580f-349">`IHtmlHelper` provides the `HtmlHelper` methods `Html.BeginForm` and `Html.ActionLink` to generate `<form>` and `<a>` elements respectively.</span></span> <span data-ttu-id="6580f-350">Tyto metody používají `Url.Action` metoda ke generování adresy URL a přijetí podobně jako argumenty.</span><span class="sxs-lookup"><span data-stu-id="6580f-350">These methods use the `Url.Action` method to generate a URL and they accept similar arguments.</span></span> <span data-ttu-id="6580f-351">`Url.RouteUrl` Companions pro `HtmlHelper` jsou `Html.BeginRouteForm` a `Html.RouteLink` které mají podobné funkce.</span><span class="sxs-lookup"><span data-stu-id="6580f-351">The `Url.RouteUrl` companions for `HtmlHelper` are `Html.BeginRouteForm` and `Html.RouteLink` which have similar functionality.</span></span>

<span data-ttu-id="6580f-352">TagHelpers generování adres URL prostřednictvím `form` Taghelperu a `<a>` Taghelperu.</span><span class="sxs-lookup"><span data-stu-id="6580f-352">TagHelpers generate URLs through the `form` TagHelper and the `<a>` TagHelper.</span></span> <span data-ttu-id="6580f-353">Obě tyto použít `IUrlHelper` pro jejich implementaci.</span><span class="sxs-lookup"><span data-stu-id="6580f-353">Both of these use `IUrlHelper` for their implementation.</span></span> <span data-ttu-id="6580f-354">Zobrazit [práce s formuláři](../views/working-with-forms.md) Další informace.</span><span class="sxs-lookup"><span data-stu-id="6580f-354">See [Working with Forms](../views/working-with-forms.md) for more information.</span></span>

<span data-ttu-id="6580f-355">Uvnitř zobrazení `IUrlHelper` je k dispozici prostřednictvím `Url` vlastnost pro všechny ad-hoc generování adresy URL není pokrytá výše.</span><span class="sxs-lookup"><span data-stu-id="6580f-355">Inside views, the `IUrlHelper` is available through the `Url` property for any ad-hoc URL generation not covered by the above.</span></span>

<a name="routing-gen-urls-action-ref-label"></a>

### <a name="generating-urls-in-action-results"></a><span data-ttu-id="6580f-356">Generování adresy URL v výsledky akcí</span><span class="sxs-lookup"><span data-stu-id="6580f-356">Generating URLS in Action Results</span></span>

<span data-ttu-id="6580f-357">Výše uvedené příklady mají zobrazit díky `IUrlHelper` v kontroleru, zatímco nejběžnější využití v kontroleru je ke generování adresy URL jako součást výsledku akce.</span><span class="sxs-lookup"><span data-stu-id="6580f-357">The examples above have shown using `IUrlHelper` in a controller, while the most common usage in a controller is to generate a URL as part of an action result.</span></span>

<span data-ttu-id="6580f-358">`ControllerBase` a `Controller` základní třídy poskytují vhodné metody pro výsledky akce, které odkazují na jiná akce.</span><span class="sxs-lookup"><span data-stu-id="6580f-358">The `ControllerBase` and `Controller` base classes provide convenience methods for action results that reference another action.</span></span> <span data-ttu-id="6580f-359">Jeden typickému využití je provést přesměrování po přijetí vstupu uživatele.</span><span class="sxs-lookup"><span data-stu-id="6580f-359">One typical usage is to redirect after accepting user input.</span></span>

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

<span data-ttu-id="6580f-360">Metody pro vytváření objektů výsledků akce použijte podobný vzorec na metody na `IUrlHelper`.</span><span class="sxs-lookup"><span data-stu-id="6580f-360">The action results factory methods follow a similar pattern to the methods on `IUrlHelper`.</span></span>

<a name="routing-dedicated-ref-label"></a>

### <a name="special-case-for-dedicated-conventional-routes"></a><span data-ttu-id="6580f-361">Zvláštní případ pro vyhrazené konvenční trasy</span><span class="sxs-lookup"><span data-stu-id="6580f-361">Special case for dedicated conventional routes</span></span>

<span data-ttu-id="6580f-362">Konvenční směrování můžete použít zvláštní druh definice trasy volána *vyhrazené konvenční trasy*.</span><span class="sxs-lookup"><span data-stu-id="6580f-362">Conventional routing can use a special kind of route definition called a *dedicated conventional route*.</span></span> <span data-ttu-id="6580f-363">V následujícím příkladu s názvem trasy `blog` vyhrazené konvenční tras.</span><span class="sxs-lookup"><span data-stu-id="6580f-363">In the example below, the route named `blog` is a dedicated conventional route.</span></span>

```csharp
app.UseMvc(routes =>
{
    routes.MapRoute("blog", "blog/{*article}",
        defaults: new { controller = "Blog", action = "Article" });
    routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="6580f-364">Pomocí těchto definicí cesty `Url.Action("Index", "Home")` vygeneruje cesty URL `/` s `default` trasy, ale proč?</span><span class="sxs-lookup"><span data-stu-id="6580f-364">Using these route definitions, `Url.Action("Index", "Home")` will generate the URL path `/` with the `default` route, but why?</span></span> <span data-ttu-id="6580f-365">Může být uhodnout hodnoty trasy `{ controller = Home, action = Index }` dost informací k vygenerování adresy URL použije `blog`, a výsledkem bude `/blog?action=Index&controller=Home`.</span><span class="sxs-lookup"><span data-stu-id="6580f-365">You might guess the route values `{ controller = Home, action = Index }` would be enough to generate a URL using `blog`, and the result would be `/blog?action=Index&controller=Home`.</span></span>

<span data-ttu-id="6580f-366">Konvenční trasy vyhrazené Spolehněte se na zvláštní chování výchozí hodnoty, které nemají odpovídající parametr trasy, trasy brání "příliš chamtivého" s generování adresy URL.</span><span class="sxs-lookup"><span data-stu-id="6580f-366">Dedicated conventional routes rely on a special behavior of default values that don't have a corresponding route parameter that prevents the route from being "too greedy" with URL generation.</span></span> <span data-ttu-id="6580f-367">V tomto případě výchozí hodnoty jsou `{ controller = Blog, action = Article }`a ani `controller` ani `action` se zobrazí jako parametr trasa.</span><span class="sxs-lookup"><span data-stu-id="6580f-367">In this case the default values are `{ controller = Blog, action = Article }`, and neither `controller` nor `action` appears as a route parameter.</span></span> <span data-ttu-id="6580f-368">Při generování adresy URL směrování provádí, hodnoty poskytnuté musí odpovídat výchozím hodnotám.</span><span class="sxs-lookup"><span data-stu-id="6580f-368">When routing performs URL generation, the values provided must match the default values.</span></span> <span data-ttu-id="6580f-369">Pomocí generování adresy URL `blog` selže, protože hodnoty `{ controller = Home, action = Index }` neodpovídají `{ controller = Blog, action = Article }`.</span><span class="sxs-lookup"><span data-stu-id="6580f-369">URL generation using `blog` will fail because the values `{ controller = Home, action = Index }` don't match `{ controller = Blog, action = Article }`.</span></span> <span data-ttu-id="6580f-370">Směrování pak přejde k akci `default`, která proběhne úspěšně.</span><span class="sxs-lookup"><span data-stu-id="6580f-370">Routing then falls back to try `default`, which succeeds.</span></span>

<a name="routing-areas-ref-label"></a>

## <a name="areas"></a><span data-ttu-id="6580f-371">Oblasti</span><span class="sxs-lookup"><span data-stu-id="6580f-371">Areas</span></span>

<span data-ttu-id="6580f-372">[Oblasti](areas.md) jsou funkce služby MVC používány pro organizaci související funkce do skupiny jako samostatné směrování – obor názvů (pro akce kontroleru) a strukturu složek (pro zobrazení).</span><span class="sxs-lookup"><span data-stu-id="6580f-372">[Areas](areas.md) are an MVC feature used to organize related functionality into a group as a separate routing-namespace (for controller actions) and folder structure (for views).</span></span> <span data-ttu-id="6580f-373">Pomocí oblastí umožňuje aplikaci mít víc řadičích se stejným názvem – za předpokladu, že mají různé *oblasti*.</span><span class="sxs-lookup"><span data-stu-id="6580f-373">Using areas allows an application to have multiple controllers with the same name - as long as they have different *areas*.</span></span> <span data-ttu-id="6580f-374">Pomocí oblastí vytvoří hierarchii pro účely směrování tak, že přidáte další parametr trasy, `area` k `controller` a `action`.</span><span class="sxs-lookup"><span data-stu-id="6580f-374">Using areas creates a hierarchy for the purpose of routing by adding another route parameter, `area` to `controller` and `action`.</span></span> <span data-ttu-id="6580f-375">Tato část se bude zabývat směrování interakci s oblastmi - naleznete v tématu [oblasti](areas.md) podrobnosti o použití oblasti se zobrazeními.</span><span class="sxs-lookup"><span data-stu-id="6580f-375">This section will discuss how routing interacts with areas - see [Areas](areas.md) for details about how areas are used with views.</span></span>

<span data-ttu-id="6580f-376">Následující příklad nastaví MVC použití konvenční výchozí trasu a *trasy oblasti* pro danou oblast s názvem `Blog`:</span><span class="sxs-lookup"><span data-stu-id="6580f-376">The following example configures MVC to use the default conventional route and an *area route* for an area named `Blog`:</span></span>

[!code-csharp[](routing/sample/AreasRouting/Startup.cs?name=snippet1)]

<span data-ttu-id="6580f-377">Při porovnávání cesty adresy URL jako `/Manage/Users/AddUser`, první trasa vytvoří hodnoty trasy `{ area = Blog, controller = Users, action = AddUser }`.</span><span class="sxs-lookup"><span data-stu-id="6580f-377">When matching a URL path like `/Manage/Users/AddUser`, the first route will produce the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span> <span data-ttu-id="6580f-378">`area` Trasy hodnota je vytvořen výchozí hodnotu pro `area`, ve skutečnosti vytvořené trasy `MapAreaRoute` je ekvivalentní následujícímu:</span><span class="sxs-lookup"><span data-stu-id="6580f-378">The `area` route value is produced by a default value for `area`, in fact the route created by `MapAreaRoute` is equivalent to the following:</span></span>

[!code-csharp[](routing/sample/AreasRouting/Startup.cs?name=snippet2)]

<span data-ttu-id="6580f-379">`MapAreaRoute` Vytvoří trasu pomocí výchozí hodnoty a omezení pro `area` pomocí názvu zadané oblasti, v tomto případě `Blog`.</span><span class="sxs-lookup"><span data-stu-id="6580f-379">`MapAreaRoute` creates a route using both a default value and constraint for `area` using the provided area name, in this case `Blog`.</span></span> <span data-ttu-id="6580f-380">Výchozí hodnota zajistí, že vždy vytváří trasy `{ area = Blog, ... }`, omezení vyžaduje hodnotu `{ area = Blog, ... }` pro generování adresy URL.</span><span class="sxs-lookup"><span data-stu-id="6580f-380">The default value ensures that the route always produces `{ area = Blog, ... }`, the constraint requires the value `{ area = Blog, ... }` for URL generation.</span></span>

> [!TIP]
> <span data-ttu-id="6580f-381">Konvenční směrování je závislé na pořadí.</span><span class="sxs-lookup"><span data-stu-id="6580f-381">Conventional routing is order-dependent.</span></span> <span data-ttu-id="6580f-382">Obecně platí trasy s oblastmi by měl umístit výše ve směrovací tabulce, jako jsou podrobnější než trasy bez oblasti.</span><span class="sxs-lookup"><span data-stu-id="6580f-382">In general, routes with areas should be placed earlier in the route table as they're more specific than routes without an area.</span></span>

<span data-ttu-id="6580f-383">Pomocí výše uvedený příklad, hodnoty trasy odpovídají následující akce:</span><span class="sxs-lookup"><span data-stu-id="6580f-383">Using the above example, the route values would match the following action:</span></span>

[!code-csharp[](routing/sample/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

<span data-ttu-id="6580f-384">`AreaAttribute` Je co označuje kontroleru v rámci oblasti, říkáme, že tento kontroler je v `Blog` oblasti.</span><span class="sxs-lookup"><span data-stu-id="6580f-384">The `AreaAttribute` is what denotes a controller as part of an area, we say that this controller is in the `Blog` area.</span></span> <span data-ttu-id="6580f-385">Řadiče bez `[Area]` atributu nejsou členy libovolné oblasti a bude **není** odpovídat, kdy `area` trasy hodnota poskytuje směrování.</span><span class="sxs-lookup"><span data-stu-id="6580f-385">Controllers without an `[Area]` attribute are not members of any area, and will **not** match when the `area` route value is provided by routing.</span></span> <span data-ttu-id="6580f-386">V následujícím příkladu může odpovídat pouze první řadič uvedené hodnoty trasy `{ area = Blog, controller = Users, action = AddUser }`.</span><span class="sxs-lookup"><span data-stu-id="6580f-386">In the following example, only the first controller listed can match the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span>

[!code-csharp[](routing/sample/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

[!code-csharp[](routing/sample/AreasRouting/Areas/Zebra/Controllers/UsersController.cs)]

[!code-csharp[](routing/sample/AreasRouting/Controllers/UsersController.cs)]

> [!NOTE]
> <span data-ttu-id="6580f-387">Obor názvů každý kontroler je znázorněna zde pro úplnost – kontrolery jinak bude mít názvy v konfliktu a generovat chybu kompilátoru.</span><span class="sxs-lookup"><span data-stu-id="6580f-387">The namespace of each controller is shown here for completeness - otherwise the controllers would have a naming conflict and generate a compiler error.</span></span> <span data-ttu-id="6580f-388">Obory názvů třídy nemají žádný vliv na směrování MVC.</span><span class="sxs-lookup"><span data-stu-id="6580f-388">Class namespaces have no effect on MVC's routing.</span></span>

<span data-ttu-id="6580f-389">První dva řadiče jsou členy oblastí a porovnávat pouze při jejich název příslušné oblasti poskytuje `area` trasy hodnotu.</span><span class="sxs-lookup"><span data-stu-id="6580f-389">The first two controllers are members of areas, and only match when their respective area name is provided by the `area` route value.</span></span> <span data-ttu-id="6580f-390">Třetí kontroler není členem žádné oblasti a pouze shody může při žádná hodnota pro `area` poskytuje směrování.</span><span class="sxs-lookup"><span data-stu-id="6580f-390">The third controller isn't a member of any area, and can only match when no value for `area` is provided by routing.</span></span>

> [!NOTE]
> <span data-ttu-id="6580f-391">Z hlediska odpovídající *žádná hodnota*, neexistence `area` hodnota je stejná jako hodnota `area` byla null nebo prázdný řetězec.</span><span class="sxs-lookup"><span data-stu-id="6580f-391">In terms of matching *no value*, the absence of the `area` value is the same as if the value for `area` were null or the empty string.</span></span>

<span data-ttu-id="6580f-392">Při provádění akce v oblasti, hodnoty trasy pro `area` bude k dispozici jako *okolí hodnotu* pro směrování pro generování adresy URL.</span><span class="sxs-lookup"><span data-stu-id="6580f-392">When executing an action inside an area, the route value for `area` will be available as an *ambient value* for routing to use for URL generation.</span></span> <span data-ttu-id="6580f-393">To znamená, že ve výchozím nastavení oblasti fungovat *vždy navrchu* pro generování adresy URL, jak je ukázáno v následujícím příkladu.</span><span class="sxs-lookup"><span data-stu-id="6580f-393">This means that by default areas act *sticky* for URL generation as demonstrated by the following sample.</span></span>

[!code-csharp[](routing/sample/AreasRouting/Startup.cs?name=snippet3)]

[!code-csharp[](routing/sample/AreasRouting/Areas/Duck/Controllers/UsersController.cs)]

<a name="iactionconstraint-ref-label"></a>

## <a name="understanding-iactionconstraint"></a><span data-ttu-id="6580f-394">Principy IActionConstraint</span><span class="sxs-lookup"><span data-stu-id="6580f-394">Understanding IActionConstraint</span></span>

> [!NOTE]
> <span data-ttu-id="6580f-395">Tato část se podrobně na interní informace o rozhraní framework a způsobu, jakým MVC vybírá akci, kterou chcete spustit.</span><span class="sxs-lookup"><span data-stu-id="6580f-395">This section is a deep-dive on framework internals and how MVC chooses an action to execute.</span></span> <span data-ttu-id="6580f-396">Typická aplikace nebudete potřebovat vlastní `IActionConstraint`</span><span class="sxs-lookup"><span data-stu-id="6580f-396">A typical application won't need a custom `IActionConstraint`</span></span>

<span data-ttu-id="6580f-397">Pravděpodobně už používáte `IActionConstraint` i v případě, že nejste obeznámeni s rozhraním.</span><span class="sxs-lookup"><span data-stu-id="6580f-397">You have likely already used `IActionConstraint` even if you're not familiar with the interface.</span></span> <span data-ttu-id="6580f-398">`[HttpGet]` Atribut a podobné `[Http-VERB]` implementaci atributy `IActionConstraint` aby bylo možné omezit provádění metody akce.</span><span class="sxs-lookup"><span data-stu-id="6580f-398">The `[HttpGet]` Attribute and similar `[Http-VERB]` attributes implement `IActionConstraint` in order to limit the execution of an action method.</span></span>

```csharp
public class ProductsController : Controller
{
    [HttpGet]
    public IActionResult Edit() { }

    public IActionResult Edit(...) { }
}
```

<span data-ttu-id="6580f-399">Za předpokladu, že výchozí konvenční trasu cesty URL `/Products/Edit` vyprodukuje hodnoty `{ controller = Products, action = Edit }`, která by odpovídala **obě** akcí je vidět tady.</span><span class="sxs-lookup"><span data-stu-id="6580f-399">Assuming the default conventional route, the URL path `/Products/Edit` would produce the values `{ controller = Products, action = Edit }`, which would match **both** of the actions shown here.</span></span> <span data-ttu-id="6580f-400">V `IActionConstraint` terminologie by říkáme, že obě tyto akce jsou považovány za kandidáty – jako obě shodovat data trasy.</span><span class="sxs-lookup"><span data-stu-id="6580f-400">In `IActionConstraint` terminology we would say that both of these actions are considered candidates - as they both match the route data.</span></span>

<span data-ttu-id="6580f-401">Když `HttpGetAttribute` spustí, bude říct, že *Edit()* odpovídá *získat* a není nalezena shoda s další příkaz protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="6580f-401">When the `HttpGetAttribute` executes, it will say that *Edit()* is a match for *GET* and isn't a match for any other HTTP verb.</span></span> <span data-ttu-id="6580f-402">`Edit(...)` Akce nemá žádné omezení definovaná a proto bude odpovídat libovolný příkaz protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="6580f-402">The `Edit(...)` action doesn't have any constraints defined, and so will match any HTTP verb.</span></span> <span data-ttu-id="6580f-403">To za předpokladu, že `POST` – pouze `Edit(...)` odpovídá.</span><span class="sxs-lookup"><span data-stu-id="6580f-403">So assuming a `POST` - only `Edit(...)` matches.</span></span> <span data-ttu-id="6580f-404">Ale pro `GET` obě akce může i nadále odpovídat - však akce s `IActionConstraint` je vždy považován za *lepší* než akce bez.</span><span class="sxs-lookup"><span data-stu-id="6580f-404">But, for a `GET` both actions can still match - however, an action with an `IActionConstraint` is always considered *better* than an action without.</span></span> <span data-ttu-id="6580f-405">Takže protože `Edit()` má `[HttpGet]` se považuje za konkrétnější a bude vybrána, pokud obě akce můžou odpovídat.</span><span class="sxs-lookup"><span data-stu-id="6580f-405">So because `Edit()` has `[HttpGet]` it's considered more specific, and will be selected if both actions can match.</span></span>

<span data-ttu-id="6580f-406">Koncepčně `IActionConstraint` je forma *přetížení*, ale ne přetížení metod se stejným názvem, je přetížení mezi akcemi, které odpovídají stejnou adresu URL.</span><span class="sxs-lookup"><span data-stu-id="6580f-406">Conceptually, `IActionConstraint` is a form of *overloading*, but instead of overloading methods with the same name, it's overloading between actions that match the same URL.</span></span> <span data-ttu-id="6580f-407">Směrování atributů, zabírá `IActionConstraint` a může docházet k provedení akcí z různých řadičů obě jsou považovány za kandidáty.</span><span class="sxs-lookup"><span data-stu-id="6580f-407">Attribute routing also uses `IActionConstraint` and can result in actions from different controllers both being considered candidates.</span></span>

<a name="iactionconstraint-impl-ref-label"></a>

### <a name="implementing-iactionconstraint"></a><span data-ttu-id="6580f-408">Implementace IActionConstraint</span><span class="sxs-lookup"><span data-stu-id="6580f-408">Implementing IActionConstraint</span></span>

<span data-ttu-id="6580f-409">Nejjednodušší způsob, jak implementovat `IActionConstraint` je vytvoření třídy odvozené od `System.Attribute` a umístěte ho na akce a kontrolery.</span><span class="sxs-lookup"><span data-stu-id="6580f-409">The simplest way to implement an `IActionConstraint` is to create a class derived from `System.Attribute` and place it on your actions and controllers.</span></span> <span data-ttu-id="6580f-410">MVC automaticky zjistí všechny `IActionConstraint` , která jsou použita jako atributy.</span><span class="sxs-lookup"><span data-stu-id="6580f-410">MVC will automatically discover any `IActionConstraint` that are applied as attributes.</span></span> <span data-ttu-id="6580f-411">Můžete použít model aplikace použít omezení, a to je pravděpodobně nejflexibilnějším přístupem, protože umožňuje metaprogram jak uplatňují.</span><span class="sxs-lookup"><span data-stu-id="6580f-411">You can use the application model to apply constraints, and this is probably the most flexible approach as it allows you to metaprogram how they're applied.</span></span>

<span data-ttu-id="6580f-412">V následujícím příkladu vybere omezení akce na základě *směrové číslo země* z dat trasy.</span><span class="sxs-lookup"><span data-stu-id="6580f-412">In the following example a constraint chooses an action based on a *country code* from the route data.</span></span> <span data-ttu-id="6580f-413">[Úplná ukázka na Githubu](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.ActionConstraintSample.Web/CountrySpecificAttribute.cs).</span><span class="sxs-lookup"><span data-stu-id="6580f-413">The [full sample on GitHub](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.ActionConstraintSample.Web/CountrySpecificAttribute.cs).</span></span>

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

<span data-ttu-id="6580f-414">Zodpovídáte za implementaci `Accept` metoda a zvolením "Objednávku" omezení ke spuštění.</span><span class="sxs-lookup"><span data-stu-id="6580f-414">You are responsible for implementing the `Accept` method and choosing an 'Order' for the constraint to execute.</span></span> <span data-ttu-id="6580f-415">V tomto případě `Accept` vrátí metoda `true` k označení "action" je shoda při `country` trasy hodnotu shody.</span><span class="sxs-lookup"><span data-stu-id="6580f-415">In this case, the `Accept` method returns `true` to denote the action is a match when the `country` route value matches.</span></span> <span data-ttu-id="6580f-416">Tím se liší od `RouteValueAttribute` v tom, že umožňuje použití náhradní lokality bez atributů akce.</span><span class="sxs-lookup"><span data-stu-id="6580f-416">This is different from a `RouteValueAttribute` in that it allows fallback to a non-attributed action.</span></span> <span data-ttu-id="6580f-417">Vzorek ukazuje, že pokud definujete `en-US` akce potom takový kód země `fr-FR` použije místo toho obecnější kontroler, který nemá `[CountrySpecific(...)]` použít.</span><span class="sxs-lookup"><span data-stu-id="6580f-417">The sample shows that if you define an `en-US` action then a country code like `fr-FR` will fall back to a more generic controller that doesn't have `[CountrySpecific(...)]` applied.</span></span>

<span data-ttu-id="6580f-418">`Order` Určuje vlastnost, na které *fáze* je součástí omezení.</span><span class="sxs-lookup"><span data-stu-id="6580f-418">The `Order` property decides which *stage* the constraint is part of.</span></span> <span data-ttu-id="6580f-419">Akce omezení spuštění ve skupinách na základě `Order`.</span><span class="sxs-lookup"><span data-stu-id="6580f-419">Action constraints run in groups based on the `Order`.</span></span> <span data-ttu-id="6580f-420">Například všechny rozhraní zadané atributy metody HTTP použít stejné `Order` hodnotu tak, aby spouštět ve stejné fázi.</span><span class="sxs-lookup"><span data-stu-id="6580f-420">For example, all of the framework provided HTTP method attributes use the same `Order` value so that they run in the same stage.</span></span> <span data-ttu-id="6580f-421">Můžete mít libovolný počet fáze, protože je potřeba implementovat požadované zásady.</span><span class="sxs-lookup"><span data-stu-id="6580f-421">You can have as many stages as you need to implement your desired policies.</span></span>

> [!TIP]
> <span data-ttu-id="6580f-422">Při rozhodování o hodnotu `Order` přemýšlení o tom, zda by měla být vaše omezení používají před metody HTTP.</span><span class="sxs-lookup"><span data-stu-id="6580f-422">To decide on a value for `Order` think about whether or not your constraint should be applied before HTTP methods.</span></span> <span data-ttu-id="6580f-423">Nižší hodnoty se spouští jako první.</span><span class="sxs-lookup"><span data-stu-id="6580f-423">Lower numbers run first.</span></span>
