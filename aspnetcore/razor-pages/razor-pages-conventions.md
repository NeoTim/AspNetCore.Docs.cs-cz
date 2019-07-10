---
title: Konvence směrování a aplikačních stránky Razor v ASP.NET Core
author: guardrex
description: Zjistěte, jak směrování a aplikační konvence zprostředkovatele modelu můžete ovládací prvek stránky směrování, zjišťování a zpracování.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/07/2019
uid: razor-pages/razor-pages-conventions
ms.openlocfilehash: 59c8af648b50deb51f3762c14348d08acd48886e
ms.sourcegitcommit: bee530454ae2b3c25dc7ffebf93536f479a14460
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2019
ms.locfileid: "67724447"
---
# <a name="razor-pages-route-and-app-conventions-in-aspnet-core"></a><span data-ttu-id="00094-103">Konvence směrování a aplikačních stránky Razor v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="00094-103">Razor Pages route and app conventions in ASP.NET Core</span></span>

<span data-ttu-id="00094-104">Podle [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="00094-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="00094-105">Další informace o použití stránky [směrování a aplikační model poskytovatele konvence](xref:mvc/controllers/application-model#conventions) řídit směrování stránky, zjišťování a zpracování v aplikacích pro stránky Razor.</span><span class="sxs-lookup"><span data-stu-id="00094-105">Learn how to use page [route and app model provider conventions](xref:mvc/controllers/application-model#conventions) to control page routing, discovery, and processing in Razor Pages apps.</span></span>

<span data-ttu-id="00094-106">Pokud potřebujete nakonfigurovat vlastní stránku trasy pro jednotlivé stránky, konfigurace směrování pro ně [AddPageRoute konvence](#configure-a-page-route) je popsáno dále v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="00094-106">When you need to configure custom page routes for individual pages, configure routing to pages with the [AddPageRoute convention](#configure-a-page-route) described later in this topic.</span></span>

<span data-ttu-id="00094-107">Zadejte trasy stránku, přidat segmenty směrování nebo parametry trasu, můžete na stránce `@page` směrnice.</span><span class="sxs-lookup"><span data-stu-id="00094-107">To specify a page route, add route segments, or add parameters to a route, use the page's `@page` directive.</span></span> <span data-ttu-id="00094-108">Další informace najdete v tématu [trasy vlastní](xref:razor-pages/index#custom-routes).</span><span class="sxs-lookup"><span data-stu-id="00094-108">For more information, see [Custom routes](xref:razor-pages/index#custom-routes).</span></span>

<span data-ttu-id="00094-109">Existují vyhrazených slov, která nejde použít jako segmenty směrování nebo názvy parametrů.</span><span class="sxs-lookup"><span data-stu-id="00094-109">There are reserved words that can't be used as route segments or parameter names.</span></span> <span data-ttu-id="00094-110">Další informace najdete v tématu [směrování: Vyhrazené názvy směrování](xref:fundamentals/routing#reserved-routing-names).</span><span class="sxs-lookup"><span data-stu-id="00094-110">For more information, see [Routing: Reserved routing names](xref:fundamentals/routing#reserved-routing-names).</span></span>

<span data-ttu-id="00094-111">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="00094-111">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

| <span data-ttu-id="00094-112">Scénář</span><span class="sxs-lookup"><span data-stu-id="00094-112">Scenario</span></span> | <span data-ttu-id="00094-113">Ukázce...</span><span class="sxs-lookup"><span data-stu-id="00094-113">The sample demonstrates ...</span></span> |
| -------- | --------------------------- |
| [<span data-ttu-id="00094-114">Vytváření modelu</span><span class="sxs-lookup"><span data-stu-id="00094-114">Model conventions</span></span>](#model-conventions)<br><br><span data-ttu-id="00094-115">Conventions.Add</span><span class="sxs-lookup"><span data-stu-id="00094-115">Conventions.Add</span></span><ul><li><span data-ttu-id="00094-116">IPageRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="00094-116">IPageRouteModelConvention</span></span></li><li><span data-ttu-id="00094-117">IPageApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="00094-117">IPageApplicationModelConvention</span></span></li><li><span data-ttu-id="00094-118">IPageHandlerModelConvention</span><span class="sxs-lookup"><span data-stu-id="00094-118">IPageHandlerModelConvention</span></span></li></ul> | <span data-ttu-id="00094-119">Přidáte šablonu trasy a záhlaví stránek vaší aplikace.</span><span class="sxs-lookup"><span data-stu-id="00094-119">Add a route template and header to an app's pages.</span></span> |
| [<span data-ttu-id="00094-120">Stránka trasy akce konvence</span><span class="sxs-lookup"><span data-stu-id="00094-120">Page route action conventions</span></span>](#page-route-action-conventions)<ul><li><span data-ttu-id="00094-121">AddFolderRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="00094-121">AddFolderRouteModelConvention</span></span></li><li><span data-ttu-id="00094-122">AddPageRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="00094-122">AddPageRouteModelConvention</span></span></li><li><span data-ttu-id="00094-123">AddPageRoute</span><span class="sxs-lookup"><span data-stu-id="00094-123">AddPageRoute</span></span></li></ul> | <span data-ttu-id="00094-124">Přidáte šablonu trasy na stránky ve složce a jednu stránku.</span><span class="sxs-lookup"><span data-stu-id="00094-124">Add a route template to pages in a folder and to a single page.</span></span> |
| [<span data-ttu-id="00094-125">Konvence akce modelu stránky</span><span class="sxs-lookup"><span data-stu-id="00094-125">Page model action conventions</span></span>](#page-model-action-conventions)<ul><li><span data-ttu-id="00094-126">AddFolderApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="00094-126">AddFolderApplicationModelConvention</span></span></li><li><span data-ttu-id="00094-127">AddPageApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="00094-127">AddPageApplicationModelConvention</span></span></li><li><span data-ttu-id="00094-128">ConfigureFilter (třídy filtru, výraz lambda nebo objekt pro vytváření filtru)</span><span class="sxs-lookup"><span data-stu-id="00094-128">ConfigureFilter (filter class, lambda expression, or filter factory)</span></span></li></ul> | <span data-ttu-id="00094-129">Přidat záhlaví stránky ve složce, přidat hlavičku do jediné stránce a nakonfigurovat [filtr factory](xref:mvc/controllers/filters#ifilterfactory) přidat záhlaví stránek vaší aplikace.</span><span class="sxs-lookup"><span data-stu-id="00094-129">Add a header to pages in a folder, add a header to a single page, and configure a [filter factory](xref:mvc/controllers/filters#ifilterfactory) to add a header to an app's pages.</span></span> |

<span data-ttu-id="00094-130">Vytváření stránek Razor přidávají a konfigurovat pomocí <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> metodu rozšíření k <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> na kolekci služby v `Startup` třídy.</span><span class="sxs-lookup"><span data-stu-id="00094-130">Razor Pages conventions are added and configured using the <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> extension method to <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> on the service collection in the `Startup` class.</span></span> <span data-ttu-id="00094-131">Následující příklady konvence jsou vysvětleny dále v tomto tématu:</span><span class="sxs-lookup"><span data-stu-id="00094-131">The following convention examples are explained later in this topic:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddRazorPagesOptions(options =>
            {
                options.Conventions.Add( ... );
                options.Conventions.AddFolderRouteModelConvention("/OtherPages", model => { ... });
                options.Conventions.AddPageRouteModelConvention("/About", model => { ... });
                options.Conventions.AddPageRoute("/Contact", "TheContactPage/{text?}");
                options.Conventions.AddFolderApplicationModelConvention("/OtherPages", model => { ... });
                options.Conventions.AddPageApplicationModelConvention("/About", model => { ... });
                options.Conventions.ConfigureFilter(model => { ... });
                options.Conventions.ConfigureFilter( ... );
            });
}
```

## <a name="route-order"></a><span data-ttu-id="00094-132">Pořadí trasy</span><span class="sxs-lookup"><span data-stu-id="00094-132">Route order</span></span>

<span data-ttu-id="00094-133">Zadejte trasy <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> pro zpracování (trasy párování).</span><span class="sxs-lookup"><span data-stu-id="00094-133">Routes specify an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> for processing (route matching).</span></span>

| <span data-ttu-id="00094-134">Objednání</span><span class="sxs-lookup"><span data-stu-id="00094-134">Order</span></span>            | <span data-ttu-id="00094-135">Chování</span><span class="sxs-lookup"><span data-stu-id="00094-135">Behavior</span></span> |
| :--------------: | -------- |
| <span data-ttu-id="00094-136">-1</span><span class="sxs-lookup"><span data-stu-id="00094-136">-1</span></span>               | <span data-ttu-id="00094-137">Trasy se zpracuje dříve, než jsou zpracovány jiným trasám.</span><span class="sxs-lookup"><span data-stu-id="00094-137">The route is processed before other routes are processed.</span></span> |
| <span data-ttu-id="00094-138">0</span><span class="sxs-lookup"><span data-stu-id="00094-138">0</span></span>                | <span data-ttu-id="00094-139">Není zadáno pořadí (výchozí hodnota).</span><span class="sxs-lookup"><span data-stu-id="00094-139">Order isn't specified (default value).</span></span> <span data-ttu-id="00094-140">Není přiřazení `Order` (`Order = null`) výchozí hodnoty trasy `Order` na hodnotu 0 (nula) pro zpracování.</span><span class="sxs-lookup"><span data-stu-id="00094-140">Not assigning `Order` (`Order = null`) defaults the route `Order` to 0 (zero) for processing.</span></span> |
| <span data-ttu-id="00094-141">1, 2, &hellip; n</span><span class="sxs-lookup"><span data-stu-id="00094-141">1, 2, &hellip; n</span></span> | <span data-ttu-id="00094-142">Určuje pořadí zpracování trasy.</span><span class="sxs-lookup"><span data-stu-id="00094-142">Specifies the route processing order.</span></span> |

<span data-ttu-id="00094-143">Podle konvence pokládáme stav zpracování trasy:</span><span class="sxs-lookup"><span data-stu-id="00094-143">Route processing is established by convention:</span></span>

* <span data-ttu-id="00094-144">Trasy se zpracovávají v pořadí (hodnota -1, 0, 1, 2, &hellip; n).</span><span class="sxs-lookup"><span data-stu-id="00094-144">Routes are processed in sequential order (-1, 0, 1, 2, &hellip; n).</span></span>
* <span data-ttu-id="00094-145">Když trasy mají stejné `Order`, nejvíce určené směrování je nalezena shoda, nejprve následovaný specifické pro less trasy.</span><span class="sxs-lookup"><span data-stu-id="00094-145">When routes have the same `Order`, the most specific route is matched first followed by less specific routes.</span></span>
* <span data-ttu-id="00094-146">Když tras se stejnou `Order` a stejný počet parametrů odpovídat adrese URL žádosti, trasy se zpracovávají v pořadí, ve kterém se přidají do <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>.</span><span class="sxs-lookup"><span data-stu-id="00094-146">When routes with the same `Order` and the same number of parameters match a request URL, routes are processed in the order that they're added to the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>.</span></span>

<span data-ttu-id="00094-147">Pokud je to možné Vyhněte se v závislosti na zavedených postupu zpracování objednávky.</span><span class="sxs-lookup"><span data-stu-id="00094-147">If possible, avoid depending on an established route processing order.</span></span> <span data-ttu-id="00094-148">Obecně platí směrování vybere správné směrování s odpovídajícími adresy URL.</span><span class="sxs-lookup"><span data-stu-id="00094-148">Generally, routing selects the correct route with URL matching.</span></span> <span data-ttu-id="00094-149">Pokud je nutné nastavit trasy `Order` vlastnosti pro směrování požadavků správně, směrování schéma aplikace je pravděpodobně matoucí pro klienty a křehkými udržovat.</span><span class="sxs-lookup"><span data-stu-id="00094-149">If you must set route `Order` properties to route requests correctly, the app's routing scheme is probably confusing to clients and fragile to maintain.</span></span> <span data-ttu-id="00094-150">Které se snaží zjednodušit směrování schéma aplikace.</span><span class="sxs-lookup"><span data-stu-id="00094-150">Seek to simplify the app's routing scheme.</span></span> <span data-ttu-id="00094-151">Ukázková aplikace vyžaduje explicitní trasy zpracování objednávky k předvedení několik scénáře použití jedné aplikace.</span><span class="sxs-lookup"><span data-stu-id="00094-151">The sample app requires an explicit route processing order to demonstrate several routing scenarios using a single app.</span></span> <span data-ttu-id="00094-152">Nicméně, měli byste se pokusit postup nastavení trasy, aby `Order` v produkčních aplikacích.</span><span class="sxs-lookup"><span data-stu-id="00094-152">However, you should attempt to avoid the practice of setting route `Order` in production apps.</span></span>

<span data-ttu-id="00094-153">Stránky Razor směrování a směrování sdílení řadiče MVC implementace.</span><span class="sxs-lookup"><span data-stu-id="00094-153">Razor Pages routing and MVC controller routing share an implementation.</span></span> <span data-ttu-id="00094-154">Informace o pořadí trasy v tématech MVC je k dispozici na [směrování na akce kontroleru: Pořadí trasy atributů](xref:mvc/controllers/routing#ordering-attribute-routes).</span><span class="sxs-lookup"><span data-stu-id="00094-154">Information on route order in the MVC topics is available at [Routing to controller actions: Ordering attribute routes](xref:mvc/controllers/routing#ordering-attribute-routes).</span></span>

## <a name="model-conventions"></a><span data-ttu-id="00094-155">Vytváření modelu</span><span class="sxs-lookup"><span data-stu-id="00094-155">Model conventions</span></span>

<span data-ttu-id="00094-156">Přidat delegáta pro <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> přidat [modelu konvencí](xref:mvc/controllers/application-model#conventions) , která platí pro stránky Razor.</span><span class="sxs-lookup"><span data-stu-id="00094-156">Add a delegate for <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> to add [model conventions](xref:mvc/controllers/application-model#conventions) that apply to Razor Pages.</span></span>

### <a name="add-a-route-model-convention-to-all-pages"></a><span data-ttu-id="00094-157">Přidání vytváření modelu trasu pro všechny stránky</span><span class="sxs-lookup"><span data-stu-id="00094-157">Add a route model convention to all pages</span></span>

<span data-ttu-id="00094-158">Použití <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> vytvořit a přidat <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> ke kolekci <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instancí, které se použijí během postupu stránky model konstrukce.</span><span class="sxs-lookup"><span data-stu-id="00094-158">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page route model construction.</span></span>

<span data-ttu-id="00094-159">Ukázková aplikace přidá `{globalTemplate?}` šablona trasy pro všechny stránky v aplikaci:</span><span class="sxs-lookup"><span data-stu-id="00094-159">The sample app adds a `{globalTemplate?}` route template to all of the pages in the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

<span data-ttu-id="00094-160"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> Vlastnost <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> je nastavena na `1`.</span><span class="sxs-lookup"><span data-stu-id="00094-160">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `1`.</span></span> <span data-ttu-id="00094-161">Tím se zajistí následující trasu odpovídající chování v ukázkové aplikaci:</span><span class="sxs-lookup"><span data-stu-id="00094-161">This ensures the following route matching behavior in the sample app:</span></span>

* <span data-ttu-id="00094-162">Šablona trasy pro `TheContactPage/{text?}` přidat později v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="00094-162">A route template for `TheContactPage/{text?}` is added later in the topic.</span></span> <span data-ttu-id="00094-163">Trasa stránku kontaktu má výchozí pořadí `null` (`Order = 0`), tak, aby odpovídala před `{globalTemplate?}` šablonu trasy.</span><span class="sxs-lookup"><span data-stu-id="00094-163">The Contact Page route has a default order of `null` (`Order = 0`), so it matches before the `{globalTemplate?}` route template.</span></span>
* <span data-ttu-id="00094-164">`{aboutTemplate?}` Trasy šablona se přidá později v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="00094-164">An `{aboutTemplate?}` route template is added later in the topic.</span></span> <span data-ttu-id="00094-165">`{aboutTemplate?}` Šablony je uveden `Order` z `2`.</span><span class="sxs-lookup"><span data-stu-id="00094-165">The `{aboutTemplate?}` template is given an `Order` of `2`.</span></span> <span data-ttu-id="00094-166">Když se na stránce o požaduje za `/About/RouteDataValue`, "RouteDataValue" je načten do `RouteData.Values["globalTemplate"]` (`Order = 1`) a není `RouteData.Values["aboutTemplate"]` (`Order = 2`) z důvodu nastavení `Order` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="00094-166">When the About page is requested at `/About/RouteDataValue`, "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["aboutTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>
* <span data-ttu-id="00094-167">`{otherPagesTemplate?}` Trasy šablona se přidá později v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="00094-167">An `{otherPagesTemplate?}` route template is added later in the topic.</span></span> <span data-ttu-id="00094-168">`{otherPagesTemplate?}` Šablony je uveden `Order` z `2`.</span><span class="sxs-lookup"><span data-stu-id="00094-168">The `{otherPagesTemplate?}` template is given an `Order` of `2`.</span></span> <span data-ttu-id="00094-169">Když všechny stránky v *stránek/OtherPages* složky je požadováno se parametr trasy (například `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" je načten do `RouteData.Values["globalTemplate"]` (`Order = 1`) a ne `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) z důvodu nastavení `Order` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="00094-169">When any page in the *Pages/OtherPages* folder is requested with a route parameter (for example, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="00094-170">Kdykoli je to možné, nemají nastavený `Order`, což má za následek `Order = 0`.</span><span class="sxs-lookup"><span data-stu-id="00094-170">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="00094-171">Spolehněte se na směrování k výběru správné směrování.</span><span class="sxs-lookup"><span data-stu-id="00094-171">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="00094-172">Možnosti stránky Razor, jako je například přidávání <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>, se přidají, když MVC se přidá do kolekce služby `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="00094-172">Razor Pages options, such as adding <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>, are added when MVC is added to the service collection in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="00094-173">Příklad najdete v tématu [ukázkovou aplikaci](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/).</span><span class="sxs-lookup"><span data-stu-id="00094-173">For an example, see the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/).</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="00094-174">Žádosti o stránku ukázky na `localhost:5000/About/GlobalRouteValue` a zkontrolujte výsledek:</span><span class="sxs-lookup"><span data-stu-id="00094-174">Request the sample's About page at `localhost:5000/About/GlobalRouteValue` and inspect the result:</span></span>

![Na stránce o žádá s segment GlobalRouteValue trasy.](razor-pages-conventions/_static/about-page-global-template.png)

### <a name="add-an-app-model-convention-to-all-pages"></a><span data-ttu-id="00094-177">Přidejte aplikaci modelu konvence pro všechny stránky</span><span class="sxs-lookup"><span data-stu-id="00094-177">Add an app model convention to all pages</span></span>

<span data-ttu-id="00094-178">Použití <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> vytvořit a přidat <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> ke kolekci <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> stránce instance, které se použijí při vytváření modelu aplikace.</span><span class="sxs-lookup"><span data-stu-id="00094-178">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page app model construction.</span></span>

<span data-ttu-id="00094-179">Abychom si předvedli to a jiné konvence později v tomto tématu, obsahuje ukázkovou aplikaci `AddHeaderAttribute` třídy.</span><span class="sxs-lookup"><span data-stu-id="00094-179">To demonstrate this and other conventions later in the topic, the sample app includes an `AddHeaderAttribute` class.</span></span> <span data-ttu-id="00094-180">Konstruktor třídy přijímá `name` řetězec a `values` pole řetězců.</span><span class="sxs-lookup"><span data-stu-id="00094-180">The class constructor accepts a `name` string and a `values` string array.</span></span> <span data-ttu-id="00094-181">Tyto hodnoty jsou použity v jeho `OnResultExecuting` metoda nastavit hlavičku odpovědi.</span><span class="sxs-lookup"><span data-stu-id="00094-181">These values are used in its `OnResultExecuting` method to set a response header.</span></span> <span data-ttu-id="00094-182">Úplné třídy je zobrazena ve [stránce modelu akce konvence](#page-model-action-conventions) dále v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="00094-182">The full class is shown in the [Page model action conventions](#page-model-action-conventions) section later in the topic.</span></span>

<span data-ttu-id="00094-183">Tato ukázková aplikace používá `AddHeaderAttribute` třídy přidat záhlaví `GlobalHeader`, na všechny stránky v aplikaci:</span><span class="sxs-lookup"><span data-stu-id="00094-183">The sample app uses the `AddHeaderAttribute` class to add a header, `GlobalHeader`, to all of the pages in the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

<span data-ttu-id="00094-184">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="00094-184">*Startup.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

<span data-ttu-id="00094-185">Žádosti o stránku ukázky na `localhost:5000/About` a zkontrolujte záhlaví, abyste viděli výsledek:</span><span class="sxs-lookup"><span data-stu-id="00094-185">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![Hlavičky odpovědi na stránce o ukazují, že byly přidány GlobalHeader.](razor-pages-conventions/_static/about-page-global-header.png)

### <a name="add-a-handler-model-convention-to-all-pages"></a><span data-ttu-id="00094-187">Přidat konvence model obslužné rutiny pro všechny stránky</span><span class="sxs-lookup"><span data-stu-id="00094-187">Add a handler model convention to all pages</span></span>

<span data-ttu-id="00094-188">Použití <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> vytvořit a přidat <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> ke kolekci <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> stránce instance, které se použijí během konstrukce model obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="00094-188">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page handler model construction.</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalPageHandlerModelConvention.cs?name=snippet1)]

<span data-ttu-id="00094-189">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="00094-189">*Startup.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet10)]

## <a name="page-route-action-conventions"></a><span data-ttu-id="00094-190">Stránka trasy akce konvence</span><span class="sxs-lookup"><span data-stu-id="00094-190">Page route action conventions</span></span>

<span data-ttu-id="00094-191">Výchozího zprostředkovatele modelu trasy, která je odvozena z <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> vyvolá smluv, které poskytují body rozšiřitelnosti pro konfiguraci tras stránky.</span><span class="sxs-lookup"><span data-stu-id="00094-191">The default route model provider that derives from <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> invokes conventions which are designed to provide extensibility points for configuring page routes.</span></span>

### <a name="folder-route-model-convention"></a><span data-ttu-id="00094-192">Složka trasy modelu konvence</span><span class="sxs-lookup"><span data-stu-id="00094-192">Folder route model convention</span></span>

<span data-ttu-id="00094-193">Použití <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> vytvořit a přidat <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> , která vyvolá akci na <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> pro všechny stránky v zadané složce.</span><span class="sxs-lookup"><span data-stu-id="00094-193">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> for all of the pages under the specified folder.</span></span>

<span data-ttu-id="00094-194">Tato ukázková aplikace používá <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> přidat `{otherPagesTemplate?}` šablonu trasy na stránky *OtherPages* složky:</span><span class="sxs-lookup"><span data-stu-id="00094-194">The sample app uses <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> to add an `{otherPagesTemplate?}` route template to the pages in the *OtherPages* folder:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

<span data-ttu-id="00094-195"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> Vlastnost <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> je nastavena na `2`.</span><span class="sxs-lookup"><span data-stu-id="00094-195">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `2`.</span></span> <span data-ttu-id="00094-196">To zajistí, že šablona pro `{globalTemplate?}` (dříve v tématu, které chcete nastavit `1`) je prioritu pro první data trasy hodnotu pozice, pokud je zadaná hodnota jednu trasu.</span><span class="sxs-lookup"><span data-stu-id="00094-196">This ensures that the template for `{globalTemplate?}` (set earlier in the topic to `1`) is given priority for the first route data value position when a single route value is provided.</span></span> <span data-ttu-id="00094-197">Pokud stránku *stránek/OtherPages* složky je požadováno s hodnotu parametru trasy (například `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" je načten do `RouteData.Values["globalTemplate"]` (`Order = 1`) a není `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) z důvodu nastavení `Order` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="00094-197">If a page in the *Pages/OtherPages* folder is requested with a route parameter value (for example, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="00094-198">Kdykoli je to možné, nemají nastavený `Order`, což má za následek `Order = 0`.</span><span class="sxs-lookup"><span data-stu-id="00094-198">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="00094-199">Spolehněte se na směrování k výběru správné směrování.</span><span class="sxs-lookup"><span data-stu-id="00094-199">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="00094-200">Požadavek ukázky Page1 stránku na `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` a zkontrolujte výsledek:</span><span class="sxs-lookup"><span data-stu-id="00094-200">Request the sample's Page1 page at `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` and inspect the result:</span></span>

![Page1 ve složce OtherPages žádá segmentu směrování GlobalRouteValue a OtherPagesRouteValue.](razor-pages-conventions/_static/otherpages-page1-global-and-otherpages-templates.png)

### <a name="page-route-model-convention"></a><span data-ttu-id="00094-203">Stránka trasy modelu konvence</span><span class="sxs-lookup"><span data-stu-id="00094-203">Page route model convention</span></span>

<span data-ttu-id="00094-204">Použití <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> vytvořit a přidat <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> , která vyvolá akci na <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> stránky se zadaným názvem.</span><span class="sxs-lookup"><span data-stu-id="00094-204">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> for the page with the specified name.</span></span>

<span data-ttu-id="00094-205">Tato ukázková aplikace používá `AddPageRouteModelConvention` přidat `{aboutTemplate?}` šablonu trasy ke stránce About:</span><span class="sxs-lookup"><span data-stu-id="00094-205">The sample app uses `AddPageRouteModelConvention` to add an `{aboutTemplate?}` route template to the About page:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

<span data-ttu-id="00094-206"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> Vlastnost <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> je nastavena na `2`.</span><span class="sxs-lookup"><span data-stu-id="00094-206">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `2`.</span></span> <span data-ttu-id="00094-207">To zajistí, že šablona pro `{globalTemplate?}` (dříve v tématu, které chcete nastavit `1`) je prioritu pro první data trasy hodnotu pozice, pokud je zadaná hodnota jednu trasu.</span><span class="sxs-lookup"><span data-stu-id="00094-207">This ensures that the template for `{globalTemplate?}` (set earlier in the topic to `1`) is given priority for the first route data value position when a single route value is provided.</span></span> <span data-ttu-id="00094-208">Pokud je hodnotou parametr trasy na požadované stránku o `/About/RouteDataValue`, "RouteDataValue" je načten do `RouteData.Values["globalTemplate"]` (`Order = 1`) a ne `RouteData.Values["aboutTemplate"]` (`Order = 2`) z důvodu nastavení `Order` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="00094-208">If the About page is requested with a route parameter value at `/About/RouteDataValue`, "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["aboutTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="00094-209">Kdykoli je to možné, nemají nastavený `Order`, což má za následek `Order = 0`.</span><span class="sxs-lookup"><span data-stu-id="00094-209">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="00094-210">Spolehněte se na směrování k výběru správné směrování.</span><span class="sxs-lookup"><span data-stu-id="00094-210">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="00094-211">Žádosti o stránku ukázky na `localhost:5000/About/GlobalRouteValue/AboutRouteValue` a zkontrolujte výsledek:</span><span class="sxs-lookup"><span data-stu-id="00094-211">Request the sample's About page at `localhost:5000/About/GlobalRouteValue/AboutRouteValue` and inspect the result:</span></span>

![Stránka je požadováno se segmenty směrování pro GlobalRouteValue a AboutRouteValue.](razor-pages-conventions/_static/about-page-global-and-about-templates.png)

::: moniker range=">= aspnetcore-2.2"

## <a name="use-a-parameter-transformer-to-customize-page-routes"></a><span data-ttu-id="00094-214">Použití transformátoru parametr k přizpůsobení stránky trasy</span><span class="sxs-lookup"><span data-stu-id="00094-214">Use a parameter transformer to customize page routes</span></span>

<span data-ttu-id="00094-215">Použití transformeru parametr lze přizpůsobit stránky trasy generovaných ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="00094-215">Page routes generated by ASP.NET Core can be customized using a parameter transformer.</span></span> <span data-ttu-id="00094-216">Parametr transformer implementuje `IOutboundParameterTransformer` a transformuje hodnoty parametrů.</span><span class="sxs-lookup"><span data-stu-id="00094-216">A parameter transformer implements `IOutboundParameterTransformer` and transforms the value of parameters.</span></span> <span data-ttu-id="00094-217">Například vlastní `SlugifyParameterTransformer` parametr transformer změny `SubscriptionManagement` trasy hodnota, která má `subscription-management`.</span><span class="sxs-lookup"><span data-stu-id="00094-217">For example, a custom `SlugifyParameterTransformer` parameter transformer changes the `SubscriptionManagement` route value to `subscription-management`.</span></span>

<span data-ttu-id="00094-218">`PageRouteTransformerConvention` Stránky trasy modelu úmluvy transformátoru parametr pro složku a soubor segmenty název trasy pro automaticky generované stránky v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="00094-218">The `PageRouteTransformerConvention` page route model convention applies a parameter transformer to the folder and file name segments of automatically generated page routes in an app.</span></span> <span data-ttu-id="00094-219">Například soubor Razor Pages na */Pages/SubscriptionManagement/ViewAll.cshtml* bude mít jeho trasu přepsán z `/SubscriptionManagement/ViewAll` k `/subscription-management/view-all`.</span><span class="sxs-lookup"><span data-stu-id="00094-219">For example, the Razor Pages file at */Pages/SubscriptionManagement/ViewAll.cshtml* would have its route rewritten from `/SubscriptionManagement/ViewAll` to `/subscription-management/view-all`.</span></span>

<span data-ttu-id="00094-220">`PageRouteTransformerConvention` pouze transformuje automaticky generované segmentů směrování stránky, které pocházejí ze stránky Razor složku a název souboru.</span><span class="sxs-lookup"><span data-stu-id="00094-220">`PageRouteTransformerConvention` only transforms the automatically generated segments of a page route that come from the Razor Pages folder and file name.</span></span> <span data-ttu-id="00094-221">To nebude transformace segmenty směrování přidána s `@page` směrnice.</span><span class="sxs-lookup"><span data-stu-id="00094-221">It doesn't transform route segments added with the `@page` directive.</span></span> <span data-ttu-id="00094-222">Úmluvy nebude transformovat trasy přidal <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>.</span><span class="sxs-lookup"><span data-stu-id="00094-222">The convention also doesn't transform routes added by <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>.</span></span>

<span data-ttu-id="00094-223">`PageRouteTransformerConvention` Je zaregistrovaný jako možnost v `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="00094-223">The `PageRouteTransformerConvention` is registered as an option in `Startup.ConfigureServices`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddRazorPagesOptions(options =>
            {
                options.Conventions.Add(
                    new PageRouteTransformerConvention(
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

## <a name="configure-a-page-route"></a><span data-ttu-id="00094-224">Konfigurace stránky trasy</span><span class="sxs-lookup"><span data-stu-id="00094-224">Configure a page route</span></span>

<span data-ttu-id="00094-225">Použití <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> trasy na stránku konfigurace v cestě zadané stránky.</span><span class="sxs-lookup"><span data-stu-id="00094-225">Use <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> to configure a route to a page at the specified page path.</span></span> <span data-ttu-id="00094-226">Vygenerovaný odkazy na stránce použít zadanou trasu.</span><span class="sxs-lookup"><span data-stu-id="00094-226">Generated links to the page use your specified route.</span></span> <span data-ttu-id="00094-227">`AddPageRoute` používá `AddPageRouteModelConvention` k vytvoření trasy.</span><span class="sxs-lookup"><span data-stu-id="00094-227">`AddPageRoute` uses `AddPageRouteModelConvention` to establish the route.</span></span>

<span data-ttu-id="00094-228">Ukázková aplikace vytvoří trasu k `/TheContactPage` pro *Contact.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="00094-228">The sample app creates a route to `/TheContactPage` for *Contact.cshtml*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet5)]

<span data-ttu-id="00094-229">Na stránce kontaktu lze dosáhnout také za `/Contact` prostřednictvím jeho výchozí trasy.</span><span class="sxs-lookup"><span data-stu-id="00094-229">The Contact page can also be reached at `/Contact` via its default route.</span></span>

<span data-ttu-id="00094-230">Vlastní trasy ukázkovou aplikaci na stránku kontaktujte umožňuje volitelně `text` segment směrování (`{text?}`).</span><span class="sxs-lookup"><span data-stu-id="00094-230">The sample app's custom route to the Contact page allows for an optional `text` route segment (`{text?}`).</span></span> <span data-ttu-id="00094-231">Na stránce také zahrnuje tato volitelná segment v jeho `@page` direktiv v případě, že na stránce návštěvníka má přístup k jeho `/Contact` trasy:</span><span class="sxs-lookup"><span data-stu-id="00094-231">The page also includes this optional segment in its `@page` directive in case the visitor accesses the page at its `/Contact` route:</span></span>

[!code-cshtml[](razor-pages-conventions/samples/2.x/SampleApp/Pages/Contact.cshtml?highlight=1)]

<span data-ttu-id="00094-232">Všimněte si, že adresa URL se vygeneruje pro **kontakt** odkaz na vykreslené stránce odráží aktualizovaný trasy:</span><span class="sxs-lookup"><span data-stu-id="00094-232">Note that the URL generated for the **Contact** link in the rendered page reflects the updated route:</span></span>

![Ukázkový odkaz na aplikaci obraťte se na navigačním panelu](razor-pages-conventions/_static/contact-link.png)

![Kontrola odkazu kontakt v zobrazený HTML označuje, že je odkaz nastavený na "/ TheContactPage.](razor-pages-conventions/_static/contact-link-source.png)

<span data-ttu-id="00094-235">Navštivte stránku nástroje kontakt na buď jeho běžný trasu `/Contact`, nebo vlastní trasy `/TheContactPage`.</span><span class="sxs-lookup"><span data-stu-id="00094-235">Visit the Contact page at either its ordinary route, `/Contact`, or the custom route, `/TheContactPage`.</span></span> <span data-ttu-id="00094-236">Pokud zadáte další `text` trasy segmentu, na stránce se zobrazí segment kódovaný jazykem HTML, že zadáte:</span><span class="sxs-lookup"><span data-stu-id="00094-236">If you supply an additional `text` route segment, the page shows the HTML-encoded segment that you provide:</span></span>

![Příklad prohlížeče Edge poskytnutí segment trasy volitelné 'text' 'TextValue"v adrese URL.](razor-pages-conventions/_static/route-segment-with-custom-route.png)

## <a name="page-model-action-conventions"></a><span data-ttu-id="00094-239">Konvence akce modelu stránky</span><span class="sxs-lookup"><span data-stu-id="00094-239">Page model action conventions</span></span>

<span data-ttu-id="00094-240">Výchozího zprostředkovatele modelu stránky, která implementuje <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> vyvolá smluv, které poskytují body rozšiřitelnosti pro konfiguraci stránky modely.</span><span class="sxs-lookup"><span data-stu-id="00094-240">The default page model provider that implements <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> invokes conventions which are designed to provide extensibility points for configuring page models.</span></span> <span data-ttu-id="00094-241">Tato konvence jsou užitečné při vytváření a úprava stránky zjišťování a scénáře zpracování.</span><span class="sxs-lookup"><span data-stu-id="00094-241">These conventions are useful when building and modifying page discovery and processing scenarios.</span></span>

<span data-ttu-id="00094-242">Příklady v této části, se ukázková aplikace používá `AddHeaderAttribute` třídy, která je <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>, aplikovaná hlavičky odpovědi:</span><span class="sxs-lookup"><span data-stu-id="00094-242">For the examples in this section, the sample app uses an `AddHeaderAttribute` class, which is a <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>, that applies a response header:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

<span data-ttu-id="00094-243">Pomocí konvencí vzorek ukazuje, jak použít atribut na všechny stránky ve složce a jednu stránku.</span><span class="sxs-lookup"><span data-stu-id="00094-243">Using conventions, the sample demonstrates how to apply the attribute to all of the pages in a folder and to a single page.</span></span>

<span data-ttu-id="00094-244">**Složky aplikace modelu konvence**</span><span class="sxs-lookup"><span data-stu-id="00094-244">**Folder app model convention**</span></span>

<span data-ttu-id="00094-245">Použití <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> vytvořit a přidat <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> , která vyvolá akci na <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> instance pro všechny stránky v zadané složce.</span><span class="sxs-lookup"><span data-stu-id="00094-245">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> that invokes an action on <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> instances for all pages under the specified folder.</span></span>

<span data-ttu-id="00094-246">Ukázka demonstruje použití `AddFolderApplicationModelConvention` přidáním záhlaví `OtherPagesHeader`, na stránky uvnitř *OtherPages* složku aplikace:</span><span class="sxs-lookup"><span data-stu-id="00094-246">The sample demonstrates the use of `AddFolderApplicationModelConvention` by adding a header, `OtherPagesHeader`, to the pages inside the *OtherPages* folder of the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet6)]

<span data-ttu-id="00094-247">Požadavek ukázky Page1 stránku na `localhost:5000/OtherPages/Page1` a zkontrolujte záhlaví, abyste viděli výsledek:</span><span class="sxs-lookup"><span data-stu-id="00094-247">Request the sample's Page1 page at `localhost:5000/OtherPages/Page1` and inspect the headers to view the result:</span></span>

![Hlavičky odpovědi stránky OtherPages/Page1 ukazují, že byla přidána OtherPagesHeader.](razor-pages-conventions/_static/page1-otherpages-header.png)

<span data-ttu-id="00094-249">**Stránka aplikace modelu konvence**</span><span class="sxs-lookup"><span data-stu-id="00094-249">**Page app model convention**</span></span>

<span data-ttu-id="00094-250">Použití <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> vytvořit a přidat <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> , která vyvolá akci na <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> stránky se zadaným názvem.</span><span class="sxs-lookup"><span data-stu-id="00094-250">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> for the page with the specified name.</span></span>

<span data-ttu-id="00094-251">Ukázka demonstruje použití `AddPageApplicationModelConvention` přidáním záhlaví `AboutHeader`, ke stránce About:</span><span class="sxs-lookup"><span data-stu-id="00094-251">The sample demonstrates the use of `AddPageApplicationModelConvention` by adding a header, `AboutHeader`, to the About page:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet7)]

<span data-ttu-id="00094-252">Žádosti o stránku ukázky na `localhost:5000/About` a zkontrolujte záhlaví, abyste viděli výsledek:</span><span class="sxs-lookup"><span data-stu-id="00094-252">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![Hlavičky odpovědi na stránce o ukazují, že byly přidány AboutHeader.](razor-pages-conventions/_static/about-page-about-header.png)

<span data-ttu-id="00094-254">**Konfigurace filtru**</span><span class="sxs-lookup"><span data-stu-id="00094-254">**Configure a filter**</span></span>

<span data-ttu-id="00094-255"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> Nakonfiguruje zadaný filtr použít.</span><span class="sxs-lookup"><span data-stu-id="00094-255"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configures the specified filter to apply.</span></span> <span data-ttu-id="00094-256">Můžete implementovat filtr třídy, ale ukázková aplikace ukazuje, jak implementovat filtr ve výrazu lambda, který je implementován jako objekt factory, který vrátí filtr pozadí:</span><span class="sxs-lookup"><span data-stu-id="00094-256">You can implement a filter class, but the sample app shows how to implement a filter in a lambda expression, which is implemented behind-the-scenes as a factory that returns a filter:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet8)]

<span data-ttu-id="00094-257">Model stránky aplikace se používá ke kontrole relativní cestu pro segmenty, které vedou na stránku strany Page2 *OtherPages* složky.</span><span class="sxs-lookup"><span data-stu-id="00094-257">The page app model is used to check the relative path for segments that lead to the Page2 page in the *OtherPages* folder.</span></span> <span data-ttu-id="00094-258">Pokud bude podmínka splněna, se přidá hlavičku.</span><span class="sxs-lookup"><span data-stu-id="00094-258">If the condition passes, a header is added.</span></span> <span data-ttu-id="00094-259">Pokud ne, `EmptyFilter` platí.</span><span class="sxs-lookup"><span data-stu-id="00094-259">If not, the `EmptyFilter` is applied.</span></span>

<span data-ttu-id="00094-260">`EmptyFilter` je [filtr akce](xref:mvc/controllers/filters#action-filters).</span><span class="sxs-lookup"><span data-stu-id="00094-260">`EmptyFilter` is an [Action filter](xref:mvc/controllers/filters#action-filters).</span></span> <span data-ttu-id="00094-261">Protože filtrů Akce ignorovány pomocí Razor Pages `EmptyFilter` nemá žádný vliv, tak, jak má, pokud cesta neobsahuje `OtherPages/Page2`.</span><span class="sxs-lookup"><span data-stu-id="00094-261">Since Action filters are ignored by Razor Pages, the `EmptyFilter` has no effect as intended if the path doesn't contain `OtherPages/Page2`.</span></span>

<span data-ttu-id="00094-262">Požadavek ukázky strany Page2 stránku na `localhost:5000/OtherPages/Page2` a zkontrolujte záhlaví, abyste viděli výsledek:</span><span class="sxs-lookup"><span data-stu-id="00094-262">Request the sample's Page2 page at `localhost:5000/OtherPages/Page2` and inspect the headers to view the result:</span></span>

![OtherPagesPage2Header se přidá do odpovědi pro strany Page2.](razor-pages-conventions/_static/page2-filter-header.png)

<span data-ttu-id="00094-264">**Konfigurace továrny filtru**</span><span class="sxs-lookup"><span data-stu-id="00094-264">**Configure a filter factory**</span></span>

<span data-ttu-id="00094-265"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> Nastaví zadaný objekt factory použít [filtry](xref:mvc/controllers/filters) na všechny stránky Razor.</span><span class="sxs-lookup"><span data-stu-id="00094-265"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configures the specified factory to apply [filters](xref:mvc/controllers/filters) to all Razor Pages.</span></span>

<span data-ttu-id="00094-266">Ukázkovou aplikaci poskytuje příklad použití [filtr factory](xref:mvc/controllers/filters#ifilterfactory) přidáním záhlaví `FilterFactoryHeader`, se dvě hodnoty a stránek vaší aplikace:</span><span class="sxs-lookup"><span data-stu-id="00094-266">The sample app provides an example of using a [filter factory](xref:mvc/controllers/filters#ifilterfactory) by adding a header, `FilterFactoryHeader`, with two values to the app's pages:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet9)]

<span data-ttu-id="00094-267">*AddHeaderWithFactory.cs*:</span><span class="sxs-lookup"><span data-stu-id="00094-267">*AddHeaderWithFactory.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Factories/AddHeaderWithFactory.cs?name=snippet1)]

<span data-ttu-id="00094-268">Žádosti o stránku ukázky na `localhost:5000/About` a zkontrolujte záhlaví, abyste viděli výsledek:</span><span class="sxs-lookup"><span data-stu-id="00094-268">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![Hlavičky odpovědi na stránce o ukazují, že byly přidány dva FilterFactoryHeader záhlaví.](razor-pages-conventions/_static/about-page-filter-factory-header.png)

## <a name="mvc-filters-and-the-page-filter-ipagefilter"></a><span data-ttu-id="00094-270">Filtry MVC a filtr stránky (IPageFilter)</span><span class="sxs-lookup"><span data-stu-id="00094-270">MVC Filters and the Page filter (IPageFilter)</span></span>

<span data-ttu-id="00094-271">MVC [filtrů Akce](xref:mvc/controllers/filters#action-filters) ignorují podle stránky Razor, protože stránky Razor pomocí metody obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="00094-271">MVC [Action filters](xref:mvc/controllers/filters#action-filters) are ignored by Razor Pages, since Razor Pages use handler methods.</span></span> <span data-ttu-id="00094-272">Jiné typy filtrů MVC jsou k dispozici pro použití: [Autorizace](xref:mvc/controllers/filters#authorization-filters), [výjimka](xref:mvc/controllers/filters#exception-filters), [prostředků](xref:mvc/controllers/filters#resource-filters), a [výsledek](xref:mvc/controllers/filters#result-filters).</span><span class="sxs-lookup"><span data-stu-id="00094-272">Other types of MVC filters are available for you to use: [Authorization](xref:mvc/controllers/filters#authorization-filters), [Exception](xref:mvc/controllers/filters#exception-filters), [Resource](xref:mvc/controllers/filters#resource-filters), and [Result](xref:mvc/controllers/filters#result-filters).</span></span> <span data-ttu-id="00094-273">Další informace najdete v tématu [filtry](xref:mvc/controllers/filters) tématu.</span><span class="sxs-lookup"><span data-stu-id="00094-273">For more information, see the [Filters](xref:mvc/controllers/filters) topic.</span></span>

<span data-ttu-id="00094-274">Filtr stránky (<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>) je filtr, který platí pro stránky Razor.</span><span class="sxs-lookup"><span data-stu-id="00094-274">The Page filter (<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>) is a filter that applies to Razor Pages.</span></span> <span data-ttu-id="00094-275">Další informace najdete v tématu [metody filtrování pro Razor Pages](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="00094-275">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="00094-276">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="00094-276">Additional resources</span></span>

* <xref:security/authorization/razor-pages-authorization>
* <xref:mvc/controllers/areas#areas-with-razor-pages>
