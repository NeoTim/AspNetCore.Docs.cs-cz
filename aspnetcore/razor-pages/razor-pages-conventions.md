---
title: Konvence trasy a aplikací Razor Pages v ASP.NET Core
author: rick-anderson
description: Zjistěte, jak konvence poskytovatele trasy a modelu aplikace pomáhají řídit směrování, zjišťování a zpracování stránek.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
uid: razor-pages/razor-pages-conventions
ms.openlocfilehash: 6124554d5f9859179edfb5c545cf0b082369c0c9
ms.sourcegitcommit: 3d07e21868dafc503530ecae2cfa18a7490b58a6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2020
ms.locfileid: "81642736"
---
# <a name="razor-pages-route-and-app-conventions-in-aspnet-core"></a><span data-ttu-id="0e2b2-103">Konvence trasy a aplikací Razor Pages v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0e2b2-103">Razor Pages route and app conventions in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="0e2b2-104">Přečtěte si, jak používat konvence trasy stránky [a modelu aplikace](xref:mvc/controllers/application-model#conventions) k řízení směrování, zjišťování a zpracování stránek v aplikacích Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-104">Learn how to use page [route and app model provider conventions](xref:mvc/controllers/application-model#conventions) to control page routing, discovery, and processing in Razor Pages apps.</span></span>

<span data-ttu-id="0e2b2-105">Pokud potřebujete nakonfigurovat vlastní trasy stránek pro jednotlivé stránky, nakonfigurujte směrování na stránky pomocí [konvence AddPageRoute](#configure-a-page-route) popsané dále v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-105">When you need to configure custom page routes for individual pages, configure routing to pages with the [AddPageRoute convention](#configure-a-page-route) described later in this topic.</span></span>

<span data-ttu-id="0e2b2-106">Chcete-li určit trasu stránky, přidat segmenty trasy nebo přidat parametry do trasy, použijte direktivu `@page` stránky.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-106">To specify a page route, add route segments, or add parameters to a route, use the page's `@page` directive.</span></span> <span data-ttu-id="0e2b2-107">Další informace naleznete [v tématu Vlastní trasy](xref:razor-pages/index#custom-routes).</span><span class="sxs-lookup"><span data-stu-id="0e2b2-107">For more information, see [Custom routes](xref:razor-pages/index#custom-routes).</span></span>

<span data-ttu-id="0e2b2-108">Existují vyhrazená slova, která nelze použít jako segmenty trasy nebo názvy parametrů.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-108">There are reserved words that can't be used as route segments or parameter names.</span></span> <span data-ttu-id="0e2b2-109">Další informace naleznete v [tématu Směrování: Vyhrazené názvy směrování](xref:mvc/controllers/routing#reserved-routing-names).</span><span class="sxs-lookup"><span data-stu-id="0e2b2-109">For more information, see [Routing: Reserved routing names](xref:mvc/controllers/routing#reserved-routing-names).</span></span>

<span data-ttu-id="0e2b2-110">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) [(jak stáhnout)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="0e2b2-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

| <span data-ttu-id="0e2b2-111">Scénář</span><span class="sxs-lookup"><span data-stu-id="0e2b2-111">Scenario</span></span> | <span data-ttu-id="0e2b2-112">Vzorek ukazuje ...</span><span class="sxs-lookup"><span data-stu-id="0e2b2-112">The sample demonstrates ...</span></span> |
| -------- | --------------------------- |
| [<span data-ttu-id="0e2b2-113">Modelové konvence</span><span class="sxs-lookup"><span data-stu-id="0e2b2-113">Model conventions</span></span>](#model-conventions)<br><br><span data-ttu-id="0e2b2-114">Conventions.Add</span><span class="sxs-lookup"><span data-stu-id="0e2b2-114">Conventions.Add</span></span><ul><li><span data-ttu-id="0e2b2-115">IPageRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="0e2b2-115">IPageRouteModelConvention</span></span></li><li><span data-ttu-id="0e2b2-116">IPageApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="0e2b2-116">IPageApplicationModelConvention</span></span></li><li><span data-ttu-id="0e2b2-117">IPageHandlerModelConvention</span><span class="sxs-lookup"><span data-stu-id="0e2b2-117">IPageHandlerModelConvention</span></span></li></ul> | <span data-ttu-id="0e2b2-118">Přidejte šablonu trasy a záhlaví na stránky aplikace.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-118">Add a route template and header to an app's pages.</span></span> |
| [<span data-ttu-id="0e2b2-119">Konvence akcí trasy stránky</span><span class="sxs-lookup"><span data-stu-id="0e2b2-119">Page route action conventions</span></span>](#page-route-action-conventions)<ul><li><span data-ttu-id="0e2b2-120">AddFolderRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="0e2b2-120">AddFolderRouteModelConvention</span></span></li><li><span data-ttu-id="0e2b2-121">AddPageRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="0e2b2-121">AddPageRouteModelConvention</span></span></li><li><span data-ttu-id="0e2b2-122">AddPageRoute</span><span class="sxs-lookup"><span data-stu-id="0e2b2-122">AddPageRoute</span></span></li></ul> | <span data-ttu-id="0e2b2-123">Přidejte šablonu trasy na stránky ve složce a na jednu stránku.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-123">Add a route template to pages in a folder and to a single page.</span></span> |
| [<span data-ttu-id="0e2b2-124">Konvence akcí modelu stránky</span><span class="sxs-lookup"><span data-stu-id="0e2b2-124">Page model action conventions</span></span>](#page-model-action-conventions)<ul><li><span data-ttu-id="0e2b2-125">AddFolderApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="0e2b2-125">AddFolderApplicationModelConvention</span></span></li><li><span data-ttu-id="0e2b2-126">AddPageApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="0e2b2-126">AddPageApplicationModelConvention</span></span></li><li><span data-ttu-id="0e2b2-127">ConfigureFilter (třída filtru, výraz lambda nebo továrna filtrů)</span><span class="sxs-lookup"><span data-stu-id="0e2b2-127">ConfigureFilter (filter class, lambda expression, or filter factory)</span></span></li></ul> | <span data-ttu-id="0e2b2-128">Přidejte záhlaví na stránky ve složce, přidejte záhlaví na jednu stránku a nakonfigurujte [továrnu filtrů](xref:mvc/controllers/filters#ifilterfactory) tak, aby přidala záhlaví na stránky aplikace.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-128">Add a header to pages in a folder, add a header to a single page, and configure a [filter factory](xref:mvc/controllers/filters#ifilterfactory) to add a header to an app's pages.</span></span> |

<span data-ttu-id="0e2b2-129">Razor Pages konvence jsou přidány <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> a konfigurovány pomocí metody rozšíření na <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> kolekci služeb ve `Startup` třídě.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-129">Razor Pages conventions are added and configured using the <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> extension method to <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> on the service collection in the `Startup` class.</span></span> <span data-ttu-id="0e2b2-130">Následující příklady konvence jsou vysvětleny dále v tomto tématu:</span><span class="sxs-lookup"><span data-stu-id="0e2b2-130">The following convention examples are explained later in this topic:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddRazorPages()
        .AddRazorPagesOptions(options =>
        {
            options.Conventions.Add( ... );
            options.Conventions.AddFolderRouteModelConvention(
                "/OtherPages", model => { ... });
            options.Conventions.AddPageRouteModelConvention(
                "/About", model => { ... });
            options.Conventions.AddPageRoute(
                "/Contact", "TheContactPage/{text?}");
            options.Conventions.AddFolderApplicationModelConvention(
                "/OtherPages", model => { ... });
            options.Conventions.AddPageApplicationModelConvention(
                "/About", model => { ... });
            options.Conventions.ConfigureFilter(model => { ... });
            options.Conventions.ConfigureFilter( ... );
        });
}
```

## <a name="route-order"></a><span data-ttu-id="0e2b2-131">Pořadí postupu</span><span class="sxs-lookup"><span data-stu-id="0e2b2-131">Route order</span></span>

<span data-ttu-id="0e2b2-132">Trasy určují <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> pro zpracování (párování tras).</span><span class="sxs-lookup"><span data-stu-id="0e2b2-132">Routes specify an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> for processing (route matching).</span></span>

| <span data-ttu-id="0e2b2-133">Objednání</span><span class="sxs-lookup"><span data-stu-id="0e2b2-133">Order</span></span>            | <span data-ttu-id="0e2b2-134">Chování</span><span class="sxs-lookup"><span data-stu-id="0e2b2-134">Behavior</span></span> |
| :--------------: | -------- |
| <span data-ttu-id="0e2b2-135">-1</span><span class="sxs-lookup"><span data-stu-id="0e2b2-135">-1</span></span>               | <span data-ttu-id="0e2b2-136">Trasa je zpracována před zpracováním jiných tras.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-136">The route is processed before other routes are processed.</span></span> |
| <span data-ttu-id="0e2b2-137">0</span><span class="sxs-lookup"><span data-stu-id="0e2b2-137">0</span></span>                | <span data-ttu-id="0e2b2-138">Objednávka není zadána (výchozí hodnota).</span><span class="sxs-lookup"><span data-stu-id="0e2b2-138">Order isn't specified (default value).</span></span> <span data-ttu-id="0e2b2-139">Nepřiřazovat `Order` (`Order = null` `Order` ) výchozí trasu na 0 (nula) pro zpracování.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-139">Not assigning `Order` (`Order = null`) defaults the route `Order` to 0 (zero) for processing.</span></span> |
| <span data-ttu-id="0e2b2-140">1, 2, &hellip; n</span><span class="sxs-lookup"><span data-stu-id="0e2b2-140">1, 2, &hellip; n</span></span> | <span data-ttu-id="0e2b2-141">Určuje pořadí zpracování postupu.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-141">Specifies the route processing order.</span></span> |

<span data-ttu-id="0e2b2-142">Zpracování postupu je stanoveno úmluvou:</span><span class="sxs-lookup"><span data-stu-id="0e2b2-142">Route processing is established by convention:</span></span>

* <span data-ttu-id="0e2b2-143">Trasy jsou zpracovávány v sekvenčním pořadí (-1, 0, 1, 2, &hellip; n).</span><span class="sxs-lookup"><span data-stu-id="0e2b2-143">Routes are processed in sequential order (-1, 0, 1, 2, &hellip; n).</span></span>
* <span data-ttu-id="0e2b2-144">Pokud mají trasy `Order`stejné , nejkonkrétnější trasa se nejprve shoduje s méně specifickými trasami.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-144">When routes have the same `Order`, the most specific route is matched first followed by less specific routes.</span></span>
* <span data-ttu-id="0e2b2-145">Pokud trasy se `Order` stejným a stejným počtem parametrů odpovídají adrese URL požadavku, jsou trasy zpracovány v <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>pořadí, v jakém jsou přidány do .</span><span class="sxs-lookup"><span data-stu-id="0e2b2-145">When routes with the same `Order` and the same number of parameters match a request URL, routes are processed in the order that they're added to the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>.</span></span>

<span data-ttu-id="0e2b2-146">Pokud je to možné, vyhněte se v závislosti na zavedeném pořadí zpracování postupu.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-146">If possible, avoid depending on an established route processing order.</span></span> <span data-ttu-id="0e2b2-147">Obecně platí, že směrování vybere správnou trasu s párováním adres URL.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-147">Generally, routing selects the correct route with URL matching.</span></span> <span data-ttu-id="0e2b2-148">Pokud je nutné `Order` nastavit vlastnosti trasy pro směrování požadavků správně, schéma směrování aplikace je pravděpodobně matoucí pro klienty a křehké udržovat.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-148">If you must set route `Order` properties to route requests correctly, the app's routing scheme is probably confusing to clients and fragile to maintain.</span></span> <span data-ttu-id="0e2b2-149">Vyhledejte zjednodušení schématu směrování aplikace.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-149">Seek to simplify the app's routing scheme.</span></span> <span data-ttu-id="0e2b2-150">Ukázková aplikace vyžaduje explicitní pořadí zpracování trasy k předvedení několika scénářů směrování pomocí jedné aplikace.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-150">The sample app requires an explicit route processing order to demonstrate several routing scenarios using a single app.</span></span> <span data-ttu-id="0e2b2-151">Měli byste se však pokusit vyhnout `Order` se praxi nastavení postupu v produkčních aplikacích.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-151">However, you should attempt to avoid the practice of setting route `Order` in production apps.</span></span>

<span data-ttu-id="0e2b2-152">Razor Pages směrování a MVC řadič směrování sdílet implementaci.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-152">Razor Pages routing and MVC controller routing share an implementation.</span></span> <span data-ttu-id="0e2b2-153">Informace o pořadí tras v tématech MVC jsou k dispozici na [adrese Akce Směrování k kontrolorovi: Řazení tras atributů](xref:mvc/controllers/routing#ordering-attribute-routes).</span><span class="sxs-lookup"><span data-stu-id="0e2b2-153">Information on route order in the MVC topics is available at [Routing to controller actions: Ordering attribute routes](xref:mvc/controllers/routing#ordering-attribute-routes).</span></span>

## <a name="model-conventions"></a><span data-ttu-id="0e2b2-154">Modelové konvence</span><span class="sxs-lookup"><span data-stu-id="0e2b2-154">Model conventions</span></span>

<span data-ttu-id="0e2b2-155">Přidejte delegáta pro <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> přidání [konvencí modelu,](xref:mvc/controllers/application-model#conventions) které se vztahují k razor pages.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-155">Add a delegate for <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> to add [model conventions](xref:mvc/controllers/application-model#conventions) that apply to Razor Pages.</span></span>

### <a name="add-a-route-model-convention-to-all-pages"></a><span data-ttu-id="0e2b2-156">Přidání konvence modelu trasy na všechny stránky</span><span class="sxs-lookup"><span data-stu-id="0e2b2-156">Add a route model convention to all pages</span></span>

<span data-ttu-id="0e2b2-157">Slouží <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> k vytvoření <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> a přidání <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> a do kolekce instancí, které jsou použity během konstrukce modelu trasy stránky.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-157">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page route model construction.</span></span>

<span data-ttu-id="0e2b2-158">Ukázková aplikace `{globalTemplate?}` přidá šablonu trasy na všechny stránky v aplikaci:</span><span class="sxs-lookup"><span data-stu-id="0e2b2-158">The sample app adds a `{globalTemplate?}` route template to all of the pages in the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

<span data-ttu-id="0e2b2-159">Vlastnost <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> pro <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> je nastavena na `1`.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-159">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `1`.</span></span> <span data-ttu-id="0e2b2-160">Tím zajistíte následující chování při porovnávání tras v ukázkové aplikaci:</span><span class="sxs-lookup"><span data-stu-id="0e2b2-160">This ensures the following route matching behavior in the sample app:</span></span>

* <span data-ttu-id="0e2b2-161">Šablona trasy `TheContactPage/{text?}` pro je přidána později v tématu.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-161">A route template for `TheContactPage/{text?}` is added later in the topic.</span></span> <span data-ttu-id="0e2b2-162">Trasa Stránka kontaktu má `null` výchozí`Order = 0`pořadí ( ), `{globalTemplate?}` takže odpovídá před šablonou trasy.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-162">The Contact Page route has a default order of `null` (`Order = 0`), so it matches before the `{globalTemplate?}` route template.</span></span>
* <span data-ttu-id="0e2b2-163">Šablona `{aboutTemplate?}` trasy je přidána později v tématu.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-163">An `{aboutTemplate?}` route template is added later in the topic.</span></span> <span data-ttu-id="0e2b2-164">Šablona `{aboutTemplate?}` je `Order` dána . `2`</span><span class="sxs-lookup"><span data-stu-id="0e2b2-164">The `{aboutTemplate?}` template is given an `Order` of `2`.</span></span> <span data-ttu-id="0e2b2-165">Je-li požadována `/About/RouteDataValue`stránka O , je z `RouteData.Values["globalTemplate"]` `Order = 1`důvodu nastavení `RouteData.Values["aboutTemplate"]` `Order = 2`vlastnosti načtena `Order` hodnota RouteDataValue do položky ( ) a nikoli ( ).</span><span class="sxs-lookup"><span data-stu-id="0e2b2-165">When the About page is requested at `/About/RouteDataValue`, "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["aboutTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>
* <span data-ttu-id="0e2b2-166">Šablona `{otherPagesTemplate?}` trasy je přidána později v tématu.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-166">An `{otherPagesTemplate?}` route template is added later in the topic.</span></span> <span data-ttu-id="0e2b2-167">Šablona `{otherPagesTemplate?}` je `Order` dána . `2`</span><span class="sxs-lookup"><span data-stu-id="0e2b2-167">The `{otherPagesTemplate?}` template is given an `Order` of `2`.</span></span> <span data-ttu-id="0e2b2-168">Pokud je požadována libovolná stránka ve složce *Pages/OtherPages* s parametrem `/OtherPages/Page1/RouteDataValue`trasy `RouteData.Values["globalTemplate"]` (například), "RouteDataValue" je načtena do (`Order = 1`) a ne `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) z důvodu nastavení vlastnosti. `Order`</span><span class="sxs-lookup"><span data-stu-id="0e2b2-168">When any page in the *Pages/OtherPages* folder is requested with a route parameter (for example, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="0e2b2-169">Pokud je to možné, `Order`nenastavujte , což má za následek `Order = 0`.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-169">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="0e2b2-170">Při výběru správné trasy se můžete spolehnout na směrování.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-170">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="0e2b2-171">Razor Pages možnosti, <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>jako je například přidání , jsou `Startup.ConfigureServices`přidány při MVC je přidán do kolekce služeb v .</span><span class="sxs-lookup"><span data-stu-id="0e2b2-171">Razor Pages options, such as adding <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>, are added when MVC is added to the service collection in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="0e2b2-172">Například najdete v [ukázkové aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/).</span><span class="sxs-lookup"><span data-stu-id="0e2b2-172">For an example, see the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/).</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="0e2b2-173">Vyžádejte si `localhost:5000/About/GlobalRouteValue` stránku O vzorku a zkontrolujte výsledek:</span><span class="sxs-lookup"><span data-stu-id="0e2b2-173">Request the sample's About page at `localhost:5000/About/GlobalRouteValue` and inspect the result:</span></span>

![Stránka Informace je požadována s segmentem trasy GlobalRouteValue.](razor-pages-conventions/_static/about-page-global-template.png)

### <a name="add-an-app-model-convention-to-all-pages"></a><span data-ttu-id="0e2b2-176">Přidání konvence modelu aplikace na všechny stránky</span><span class="sxs-lookup"><span data-stu-id="0e2b2-176">Add an app model convention to all pages</span></span>

<span data-ttu-id="0e2b2-177">Slouží <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> k vytvoření <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> a přidání <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> a do kolekce instancí, které se použijí během konstrukce modelu aplikace stránky.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-177">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page app model construction.</span></span>

<span data-ttu-id="0e2b2-178">Chcete-li demonstrovat toto a další konvence dále `AddHeaderAttribute` v tématu, ukázková aplikace obsahuje třídu.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-178">To demonstrate this and other conventions later in the topic, the sample app includes an `AddHeaderAttribute` class.</span></span> <span data-ttu-id="0e2b2-179">Konstruktor třídy přijímá `name` řetězec `values` a pole řetězce.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-179">The class constructor accepts a `name` string and a `values` string array.</span></span> <span data-ttu-id="0e2b2-180">Tyto hodnoty se `OnResultExecuting` používají v jeho metodě nastavit hlavičku odpovědi.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-180">These values are used in its `OnResultExecuting` method to set a response header.</span></span> <span data-ttu-id="0e2b2-181">Celá třída je zobrazena v části [Akce modelu stránky](#page-model-action-conventions) dále v tématu.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-181">The full class is shown in the [Page model action conventions](#page-model-action-conventions) section later in the topic.</span></span>

<span data-ttu-id="0e2b2-182">Ukázková aplikace `AddHeaderAttribute` používá třídu k `GlobalHeader`přidání záhlaví , na všechny stránky v aplikaci:</span><span class="sxs-lookup"><span data-stu-id="0e2b2-182">The sample app uses the `AddHeaderAttribute` class to add a header, `GlobalHeader`, to all of the pages in the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

<span data-ttu-id="0e2b2-183">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="0e2b2-183">*Startup.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet2)]

<span data-ttu-id="0e2b2-184">Požádejte o stránku `localhost:5000/About` Informace v ukázce a zkontrolujte záhlaví, abyste zobrazili výsledek:</span><span class="sxs-lookup"><span data-stu-id="0e2b2-184">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![Záhlaví odpovědí na stránce O stránce ukazují, že byla přidána globalheader.](razor-pages-conventions/_static/about-page-global-header.png)

### <a name="add-a-handler-model-convention-to-all-pages"></a><span data-ttu-id="0e2b2-186">Přidání konvence modelu obslužné rutiny na všechny stránky</span><span class="sxs-lookup"><span data-stu-id="0e2b2-186">Add a handler model convention to all pages</span></span>

<span data-ttu-id="0e2b2-187">Slouží <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> k vytvoření <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> a přidání <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> a do kolekce instancí, které jsou použity během konstrukce modelu obslužné rutiny stránky.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-187">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page handler model construction.</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalPageHandlerModelConvention.cs?name=snippet1)]

<span data-ttu-id="0e2b2-188">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="0e2b2-188">*Startup.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet10)]

## <a name="page-route-action-conventions"></a><span data-ttu-id="0e2b2-189">Konvence akcí trasy stránky</span><span class="sxs-lookup"><span data-stu-id="0e2b2-189">Page route action conventions</span></span>

<span data-ttu-id="0e2b2-190">Výchozí zprostředkovatel modelu trasy, <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> který je odvozen od vyvolá ní konvence, které jsou navrženy tak, aby poskytovaly body rozšiřitelnosti pro konfiguraci tras stránek.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-190">The default route model provider that derives from <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> invokes conventions which are designed to provide extensibility points for configuring page routes.</span></span>

### <a name="folder-route-model-convention"></a><span data-ttu-id="0e2b2-191">Konvence modelu postupu složky</span><span class="sxs-lookup"><span data-stu-id="0e2b2-191">Folder route model convention</span></span>

<span data-ttu-id="0e2b2-192">Slouží <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> k vytvoření <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> a přidání, který <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> vyvolá akci pro všechny stránky pod zadanou složkou.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-192">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> for all of the pages under the specified folder.</span></span>

<span data-ttu-id="0e2b2-193">Ukázková aplikace <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> používá `{otherPagesTemplate?}` k přidání šablony trasy na stránky ve složce *OtherPages:*</span><span class="sxs-lookup"><span data-stu-id="0e2b2-193">The sample app uses <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> to add an `{otherPagesTemplate?}` route template to the pages in the *OtherPages* folder:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet3)]

<span data-ttu-id="0e2b2-194">Vlastnost <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> pro <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> je nastavena na `2`.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-194">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `2`.</span></span> <span data-ttu-id="0e2b2-195">Tím je zajištěno, `{globalTemplate?}` že šablona pro `1`(nastavená dříve v tématu na ) má prioritu pro první pozici hodnoty dat trasy, pokud je poskytnuta jedna hodnota trasy.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-195">This ensures that the template for `{globalTemplate?}` (set earlier in the topic to `1`) is given priority for the first route data value position when a single route value is provided.</span></span> <span data-ttu-id="0e2b2-196">Pokud je požadována stránka ve složce *Pages/OtherPages* s `/OtherPages/Page1/RouteDataValue`hodnotou parametru trasy (například), "RouteDataValue" je načtena `RouteData.Values["globalTemplate"]` do (`Order = 1`) a `RouteData.Values["otherPagesTemplate"]` ne (`Order = 2`) z důvodu nastavení vlastnosti. `Order`</span><span class="sxs-lookup"><span data-stu-id="0e2b2-196">If a page in the *Pages/OtherPages* folder is requested with a route parameter value (for example, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="0e2b2-197">Pokud je to možné, `Order`nenastavujte , což má za následek `Order = 0`.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-197">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="0e2b2-198">Při výběru správné trasy se můžete spolehnout na směrování.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-198">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="0e2b2-199">Vyžádejte si stránku Stránky 1 ukázky a `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` zkontrolujte výsledek:</span><span class="sxs-lookup"><span data-stu-id="0e2b2-199">Request the sample's Page1 page at `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` and inspect the result:</span></span>

![Stránka1 ve složce OtherPages je požadována s segmentem trasy GlobalRouteValue a OtherPagesRouteValue.](razor-pages-conventions/_static/otherpages-page1-global-and-otherpages-templates.png)

### <a name="page-route-model-convention"></a><span data-ttu-id="0e2b2-202">Konvence modelu trasy stránky</span><span class="sxs-lookup"><span data-stu-id="0e2b2-202">Page route model convention</span></span>

<span data-ttu-id="0e2b2-203">Slouží <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> k vytvoření <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> a přidání, která <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> vyvolá akci na stránce se zadaným názvem.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-203">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> for the page with the specified name.</span></span>

<span data-ttu-id="0e2b2-204">Ukázková aplikace `AddPageRouteModelConvention` používá `{aboutTemplate?}` k přidání šablony trasy na stránku Informace:</span><span class="sxs-lookup"><span data-stu-id="0e2b2-204">The sample app uses `AddPageRouteModelConvention` to add an `{aboutTemplate?}` route template to the About page:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet4)]

<span data-ttu-id="0e2b2-205">Vlastnost <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> pro <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> je nastavena na `2`.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-205">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `2`.</span></span> <span data-ttu-id="0e2b2-206">Tím je zajištěno, `{globalTemplate?}` že šablona pro `1`(nastavená dříve v tématu na ) má prioritu pro první pozici hodnoty dat trasy, pokud je poskytnuta jedna hodnota trasy.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-206">This ensures that the template for `{globalTemplate?}` (set earlier in the topic to `1`) is given priority for the first route data value position when a single route value is provided.</span></span> <span data-ttu-id="0e2b2-207">Pokud je požadována stránka O s `/About/RouteDataValue`hodnotou parametru trasy `RouteData.Values["globalTemplate"]` na`Order = 1`, `RouteData.Values["aboutTemplate"]` "RouteDataValue" je načten do ( ) a ne (`Order = 2`) z důvodu nastavení vlastnosti. `Order`</span><span class="sxs-lookup"><span data-stu-id="0e2b2-207">If the About page is requested with a route parameter value at `/About/RouteDataValue`, "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["aboutTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="0e2b2-208">Pokud je to možné, `Order`nenastavujte , což má za následek `Order = 0`.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-208">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="0e2b2-209">Při výběru správné trasy se můžete spolehnout na směrování.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-209">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="0e2b2-210">Vyžádejte si `localhost:5000/About/GlobalRouteValue/AboutRouteValue` stránku O vzorku a zkontrolujte výsledek:</span><span class="sxs-lookup"><span data-stu-id="0e2b2-210">Request the sample's About page at `localhost:5000/About/GlobalRouteValue/AboutRouteValue` and inspect the result:</span></span>

![O stránce je požadováno s segmenty postupu pro GlobalRouteValue a AboutRouteValue.](razor-pages-conventions/_static/about-page-global-and-about-templates.png)

## <a name="use-a-parameter-transformer-to-customize-page-routes"></a><span data-ttu-id="0e2b2-213">Přizpůsobení tras stránek pomocí transformátoru parametrů</span><span class="sxs-lookup"><span data-stu-id="0e2b2-213">Use a parameter transformer to customize page routes</span></span>

<span data-ttu-id="0e2b2-214">Trasy stránek generované ASP.NET Core lze přizpůsobit pomocí transformátoru parametrů.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-214">Page routes generated by ASP.NET Core can be customized using a parameter transformer.</span></span> <span data-ttu-id="0e2b2-215">Parametr transformátor `IOutboundParameterTransformer` implementuje a transformuje hodnotu parametrů.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-215">A parameter transformer implements `IOutboundParameterTransformer` and transforms the value of parameters.</span></span> <span data-ttu-id="0e2b2-216">Například vlastní `SlugifyParameterTransformer` parametr transformátor `SubscriptionManagement` změní `subscription-management`hodnotu trasy na .</span><span class="sxs-lookup"><span data-stu-id="0e2b2-216">For example, a custom `SlugifyParameterTransformer` parameter transformer changes the `SubscriptionManagement` route value to `subscription-management`.</span></span>

<span data-ttu-id="0e2b2-217">Konvence `PageRouteTransformerConvention` modelu trasy stránky aplikuje transformátor parametrů na segmenty složek a názvů souborů automaticky generovaných tras stránek v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-217">The `PageRouteTransformerConvention` page route model convention applies a parameter transformer to the folder and file name segments of automatically generated page routes in an app.</span></span> <span data-ttu-id="0e2b2-218">Například soubor Razor Pages na *adrese /Pages/SubscriptionManagement/ViewAll.cshtml* by `/SubscriptionManagement/ViewAll` `/subscription-management/view-all`měl svou trasu přepsánz do .</span><span class="sxs-lookup"><span data-stu-id="0e2b2-218">For example, the Razor Pages file at */Pages/SubscriptionManagement/ViewAll.cshtml* would have its route rewritten from `/SubscriptionManagement/ViewAll` to `/subscription-management/view-all`.</span></span>

<span data-ttu-id="0e2b2-219">`PageRouteTransformerConvention`transformuje pouze automaticky generované segmenty trasy stránky, které pocházejí ze složky Razor Pages a názvu souboru.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-219">`PageRouteTransformerConvention` only transforms the automatically generated segments of a page route that come from the Razor Pages folder and file name.</span></span> <span data-ttu-id="0e2b2-220">Netransformuje segmenty trasy přidané `@page` se směrnicí.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-220">It doesn't transform route segments added with the `@page` directive.</span></span> <span data-ttu-id="0e2b2-221">Konvence také netransformuje trasy <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>přidané .</span><span class="sxs-lookup"><span data-stu-id="0e2b2-221">The convention also doesn't transform routes added by <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>.</span></span>

<span data-ttu-id="0e2b2-222">Je `PageRouteTransformerConvention` registrován jako možnost `Startup.ConfigureServices`v :</span><span class="sxs-lookup"><span data-stu-id="0e2b2-222">The `PageRouteTransformerConvention` is registered as an option in `Startup.ConfigureServices`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddRazorPages()
        .AddRazorPagesOptions(options =>
        {
            options.Conventions.Add(
                new PageRouteTransformerConvention(
                    new SlugifyParameterTransformer()));
        });
}
```

[!code-csharp[](~/mvc/controllers/routing/samples/3.x/main/StartupSlugifyParamTransformer.cs?name=snippet2)]

[!INCLUDE[](~/includes/regex.md)]

## <a name="configure-a-page-route"></a><span data-ttu-id="0e2b2-223">Konfigurace trasy stránky</span><span class="sxs-lookup"><span data-stu-id="0e2b2-223">Configure a page route</span></span>

<span data-ttu-id="0e2b2-224">Slouží <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> ke konfiguraci trasy ke stránce na zadané cestě stránky.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-224">Use <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> to configure a route to a page at the specified page path.</span></span> <span data-ttu-id="0e2b2-225">Generované odkazy na stránku používají zadanou trasu.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-225">Generated links to the page use your specified route.</span></span> <span data-ttu-id="0e2b2-226">`AddPageRoute`používá `AddPageRouteModelConvention` k určení trasy.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-226">`AddPageRoute` uses `AddPageRouteModelConvention` to establish the route.</span></span>

<span data-ttu-id="0e2b2-227">Ukázková aplikace vytvoří `/TheContactPage` trasu pro *Contact.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="0e2b2-227">The sample app creates a route to `/TheContactPage` for *Contact.cshtml*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet5)]

<span data-ttu-id="0e2b2-228">Na stránku Kontakt lze `/Contact` také dosáhnout prostřednictvím výchozí trasy.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-228">The Contact page can also be reached at `/Contact` via its default route.</span></span>

<span data-ttu-id="0e2b2-229">Vlastní trasa ukázkové aplikace na stránku `text` Kontakt umožňuje`{text?}`volitelný segment trasy ( ).</span><span class="sxs-lookup"><span data-stu-id="0e2b2-229">The sample app's custom route to the Contact page allows for an optional `text` route segment (`{text?}`).</span></span> <span data-ttu-id="0e2b2-230">Stránka také obsahuje tento volitelný `@page` segment ve své směrnici v `/Contact` případě, že návštěvník přistupuje ke stránce na své trase:</span><span class="sxs-lookup"><span data-stu-id="0e2b2-230">The page also includes this optional segment in its `@page` directive in case the visitor accesses the page at its `/Contact` route:</span></span>

[!code-cshtml[](razor-pages-conventions/samples/3.x/SampleApp/Pages/Contact.cshtml?highlight=1)]

<span data-ttu-id="0e2b2-231">Všimněte si, že adresa URL vygenerovaná pro odkaz **Kontakt** na vykreslené stránce odráží aktualizovanou trasu:</span><span class="sxs-lookup"><span data-stu-id="0e2b2-231">Note that the URL generated for the **Contact** link in the rendered page reflects the updated route:</span></span>

![Ukázkový odkaz Kontakt aplikace na navigačním panelu](razor-pages-conventions/_static/contact-link.png)

![Kontrola odkazu Kontakt v vykresleném KÓDU HTML znamená, že href je nastaven na '/TheContactPage'](razor-pages-conventions/_static/contact-link-source.png)

<span data-ttu-id="0e2b2-234">Navštivte stránku Kontakt na běžné `/Contact`trase , `/TheContactPage`nebo na vlastní trase .</span><span class="sxs-lookup"><span data-stu-id="0e2b2-234">Visit the Contact page at either its ordinary route, `/Contact`, or the custom route, `/TheContactPage`.</span></span> <span data-ttu-id="0e2b2-235">Pokud zadáte další `text` segment trasy, stránka zobrazí segment kódovaný HTML, který zadáte:</span><span class="sxs-lookup"><span data-stu-id="0e2b2-235">If you supply an additional `text` route segment, the page shows the HTML-encoded segment that you provide:</span></span>

![Okraj ový příklad poskytnutí volitelného segmentu trasy "text" textvalue v adrese URL.](razor-pages-conventions/_static/route-segment-with-custom-route.png)

## <a name="page-model-action-conventions"></a><span data-ttu-id="0e2b2-238">Konvence akcí modelu stránky</span><span class="sxs-lookup"><span data-stu-id="0e2b2-238">Page model action conventions</span></span>

<span data-ttu-id="0e2b2-239">Výchozí zprostředkovatel modelu stránky, <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> který implementuje vyvolá konvence, které jsou navrženy tak, aby poskytovaly body rozšiřitelnosti pro konfiguraci modelů stránek.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-239">The default page model provider that implements <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> invokes conventions which are designed to provide extensibility points for configuring page models.</span></span> <span data-ttu-id="0e2b2-240">Tyto konvence jsou užitečné při vytváření a úpravách scénáře zjišťování a zpracování stránky.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-240">These conventions are useful when building and modifying page discovery and processing scenarios.</span></span>

<span data-ttu-id="0e2b2-241">Příklady v této části ukázkové aplikace `AddHeaderAttribute` používá třídu, což je <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>, která používá hlavičku odpovědi:</span><span class="sxs-lookup"><span data-stu-id="0e2b2-241">For the examples in this section, the sample app uses an `AddHeaderAttribute` class, which is a <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>, that applies a response header:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

<span data-ttu-id="0e2b2-242">Pomocí konvencí ukázka ukazuje, jak použít atribut pro všechny stránky ve složce a na jednu stránku.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-242">Using conventions, the sample demonstrates how to apply the attribute to all of the pages in a folder and to a single page.</span></span>

<span data-ttu-id="0e2b2-243">**Konvence modelu aplikace složky**</span><span class="sxs-lookup"><span data-stu-id="0e2b2-243">**Folder app model convention**</span></span>

<span data-ttu-id="0e2b2-244">Slouží <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> k vytvoření <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> a přidání akce, <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> která vyvolá akci na instancích pro všechny stránky v zadané složce.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-244">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> that invokes an action on <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> instances for all pages under the specified folder.</span></span>

<span data-ttu-id="0e2b2-245">Ukázka ukazuje použití `AddFolderApplicationModelConvention` přidáním záhlaví `OtherPagesHeader`, na stránky uvnitř složky *OtherPages* aplikace:</span><span class="sxs-lookup"><span data-stu-id="0e2b2-245">The sample demonstrates the use of `AddFolderApplicationModelConvention` by adding a header, `OtherPagesHeader`, to the pages inside the *OtherPages* folder of the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet6)]

<span data-ttu-id="0e2b2-246">Požádejte ukázkovou stránku `localhost:5000/OtherPages/Page1` Page1 a zkontrolujte záhlaví, abyste zobrazili výsledek:</span><span class="sxs-lookup"><span data-stu-id="0e2b2-246">Request the sample's Page1 page at `localhost:5000/OtherPages/Page1` and inspect the headers to view the result:</span></span>

![Záhlaví odpovědí na stránce OtherPages/Page1 ukazují, že byla přidána hlavička OtherPagesHeader.](razor-pages-conventions/_static/page1-otherpages-header.png)

<span data-ttu-id="0e2b2-248">**Konvence modelu aplikace stránky**</span><span class="sxs-lookup"><span data-stu-id="0e2b2-248">**Page app model convention**</span></span>

<span data-ttu-id="0e2b2-249">Slouží <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> k vytvoření <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> a přidání, která <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> vyvolá akci na stránce se zadaným názvem.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-249">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> for the page with the specified name.</span></span>

<span data-ttu-id="0e2b2-250">Ukázka ukazuje použití `AddPageApplicationModelConvention` přidáním záhlaví `AboutHeader`, na stránku O:</span><span class="sxs-lookup"><span data-stu-id="0e2b2-250">The sample demonstrates the use of `AddPageApplicationModelConvention` by adding a header, `AboutHeader`, to the About page:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet7)]

<span data-ttu-id="0e2b2-251">Požádejte o stránku `localhost:5000/About` Informace v ukázce a zkontrolujte záhlaví, abyste zobrazili výsledek:</span><span class="sxs-lookup"><span data-stu-id="0e2b2-251">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![Záhlaví odpovědí na stránce O stránce ukazují, že byla přidána hlavička AboutHeader.](razor-pages-conventions/_static/about-page-about-header.png)

<span data-ttu-id="0e2b2-253">**Konfigurace filtru**</span><span class="sxs-lookup"><span data-stu-id="0e2b2-253">**Configure a filter**</span></span>

<span data-ttu-id="0e2b2-254"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*>nakonfiguruje použití zadaného filtru.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-254"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configures the specified filter to apply.</span></span> <span data-ttu-id="0e2b2-255">Můžete implementovat třídu filtru, ale ukázková aplikace ukazuje, jak implementovat filtr ve výrazu lambda, který je implementován na pozadí jako továrna, která vrací filtr:</span><span class="sxs-lookup"><span data-stu-id="0e2b2-255">You can implement a filter class, but the sample app shows how to implement a filter in a lambda expression, which is implemented behind-the-scenes as a factory that returns a filter:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet8)]

<span data-ttu-id="0e2b2-256">Model aplikace stránky se používá ke kontrole relativní cesty pro segmenty, které vedou na stránku Stránka2 ve složce *OtherPages.*</span><span class="sxs-lookup"><span data-stu-id="0e2b2-256">The page app model is used to check the relative path for segments that lead to the Page2 page in the *OtherPages* folder.</span></span> <span data-ttu-id="0e2b2-257">Pokud podmínka projde, je přidána hlavička.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-257">If the condition passes, a header is added.</span></span> <span data-ttu-id="0e2b2-258">Pokud ne, `EmptyFilter` použije se.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-258">If not, the `EmptyFilter` is applied.</span></span>

<span data-ttu-id="0e2b2-259">`EmptyFilter`je [filtr akce](xref:mvc/controllers/filters#action-filters).</span><span class="sxs-lookup"><span data-stu-id="0e2b2-259">`EmptyFilter` is an [Action filter](xref:mvc/controllers/filters#action-filters).</span></span> <span data-ttu-id="0e2b2-260">Vzhledem k tomu, že filtry akce jsou ignorovány Razor Pages, `EmptyFilter` nemá žádný účinek, jak bylo zamýšleno, pokud cesta neobsahuje `OtherPages/Page2`.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-260">Since Action filters are ignored by Razor Pages, the `EmptyFilter` has no effect as intended if the path doesn't contain `OtherPages/Page2`.</span></span>

<span data-ttu-id="0e2b2-261">Požádejte stránku Page2 `localhost:5000/OtherPages/Page2` ukázky a zkontrolujte záhlaví, abyste zobrazili výsledek:</span><span class="sxs-lookup"><span data-stu-id="0e2b2-261">Request the sample's Page2 page at `localhost:5000/OtherPages/Page2` and inspect the headers to view the result:</span></span>

![OtherPagesPage2Header je přidán do odpovědi pro Page2.](razor-pages-conventions/_static/page2-filter-header.png)

<span data-ttu-id="0e2b2-263">**Konfigurace výroby filtrů**</span><span class="sxs-lookup"><span data-stu-id="0e2b2-263">**Configure a filter factory**</span></span>

<span data-ttu-id="0e2b2-264"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*>nakonfiguruje zadanou továrnu tak, aby na všechny stránky Razor [použila filtry.](xref:mvc/controllers/filters)</span><span class="sxs-lookup"><span data-stu-id="0e2b2-264"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configures the specified factory to apply [filters](xref:mvc/controllers/filters) to all Razor Pages.</span></span>

<span data-ttu-id="0e2b2-265">Ukázková aplikace poskytuje příklad použití [továrny filtrů](xref:mvc/controllers/filters#ifilterfactory) přidáním záhlaví `FilterFactoryHeader`, se dvěma hodnotami na stránky aplikace:</span><span class="sxs-lookup"><span data-stu-id="0e2b2-265">The sample app provides an example of using a [filter factory](xref:mvc/controllers/filters#ifilterfactory) by adding a header, `FilterFactoryHeader`, with two values to the app's pages:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet9)]

<span data-ttu-id="0e2b2-266">*AddHeaderWithFactory.cs*:</span><span class="sxs-lookup"><span data-stu-id="0e2b2-266">*AddHeaderWithFactory.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Factories/AddHeaderWithFactory.cs?name=snippet1)]

<span data-ttu-id="0e2b2-267">Požádejte o stránku `localhost:5000/About` Informace v ukázce a zkontrolujte záhlaví, abyste zobrazili výsledek:</span><span class="sxs-lookup"><span data-stu-id="0e2b2-267">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![Záhlaví odpovědí na stránce O stránce ukazují, že byly přidány dvě záhlaví FilterFactoryHeader.](razor-pages-conventions/_static/about-page-filter-factory-header.png)

## <a name="mvc-filters-and-the-page-filter-ipagefilter"></a><span data-ttu-id="0e2b2-269">Filtry MVC a filtr Stránky (IPageFilter)</span><span class="sxs-lookup"><span data-stu-id="0e2b2-269">MVC Filters and the Page filter (IPageFilter)</span></span>

<span data-ttu-id="0e2b2-270">[Filtry akce](xref:mvc/controllers/filters#action-filters) MVC jsou razor pages ignorovány, protože Razor Pages používají metody obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-270">MVC [Action filters](xref:mvc/controllers/filters#action-filters) are ignored by Razor Pages, since Razor Pages use handler methods.</span></span> <span data-ttu-id="0e2b2-271">K dispozici jsou další typy filtrů MVC: [Autorizace](xref:mvc/controllers/filters#authorization-filters), [Výjimka](xref:mvc/controllers/filters#exception-filters), [Prostředek](xref:mvc/controllers/filters#resource-filters)a [Výsledek](xref:mvc/controllers/filters#result-filters).</span><span class="sxs-lookup"><span data-stu-id="0e2b2-271">Other types of MVC filters are available for you to use: [Authorization](xref:mvc/controllers/filters#authorization-filters), [Exception](xref:mvc/controllers/filters#exception-filters), [Resource](xref:mvc/controllers/filters#resource-filters), and [Result](xref:mvc/controllers/filters#result-filters).</span></span> <span data-ttu-id="0e2b2-272">Další informace naleznete v tématu [Filtry.](xref:mvc/controllers/filters)</span><span class="sxs-lookup"><span data-stu-id="0e2b2-272">For more information, see the [Filters](xref:mvc/controllers/filters) topic.</span></span>

<span data-ttu-id="0e2b2-273">Filtr Stránka<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>( ) je filtr, který se vztahuje na žiletky stránky.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-273">The Page filter (<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>) is a filter that applies to Razor Pages.</span></span> <span data-ttu-id="0e2b2-274">Další informace naleznete v [tématu Filter methods for Razor Pages](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="0e2b2-274">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="0e2b2-275">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="0e2b2-275">Additional resources</span></span>

* <xref:security/authorization/razor-pages-authorization>
* <xref:mvc/controllers/areas#areas-with-razor-pages>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="0e2b2-276">Přečtěte si, jak používat konvence trasy stránky [a modelu aplikace](xref:mvc/controllers/application-model#conventions) k řízení směrování, zjišťování a zpracování stránek v aplikacích Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-276">Learn how to use page [route and app model provider conventions](xref:mvc/controllers/application-model#conventions) to control page routing, discovery, and processing in Razor Pages apps.</span></span>

<span data-ttu-id="0e2b2-277">Pokud potřebujete nakonfigurovat vlastní trasy stránek pro jednotlivé stránky, nakonfigurujte směrování na stránky pomocí [konvence AddPageRoute](#configure-a-page-route) popsané dále v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-277">When you need to configure custom page routes for individual pages, configure routing to pages with the [AddPageRoute convention](#configure-a-page-route) described later in this topic.</span></span>

<span data-ttu-id="0e2b2-278">Chcete-li určit trasu stránky, přidat segmenty trasy nebo přidat parametry do trasy, použijte direktivu `@page` stránky.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-278">To specify a page route, add route segments, or add parameters to a route, use the page's `@page` directive.</span></span> <span data-ttu-id="0e2b2-279">Další informace naleznete [v tématu Vlastní trasy](xref:razor-pages/index#custom-routes).</span><span class="sxs-lookup"><span data-stu-id="0e2b2-279">For more information, see [Custom routes](xref:razor-pages/index#custom-routes).</span></span>

<span data-ttu-id="0e2b2-280">Existují vyhrazená slova, která nelze použít jako segmenty trasy nebo názvy parametrů.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-280">There are reserved words that can't be used as route segments or parameter names.</span></span> <span data-ttu-id="0e2b2-281">Další informace naleznete v [tématu Směrování: Vyhrazené názvy směrování](xref:fundamentals/routing#reserved-routing-names).</span><span class="sxs-lookup"><span data-stu-id="0e2b2-281">For more information, see [Routing: Reserved routing names](xref:fundamentals/routing#reserved-routing-names).</span></span>

<span data-ttu-id="0e2b2-282">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) [(jak stáhnout)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="0e2b2-282">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

| <span data-ttu-id="0e2b2-283">Scénář</span><span class="sxs-lookup"><span data-stu-id="0e2b2-283">Scenario</span></span> | <span data-ttu-id="0e2b2-284">Vzorek ukazuje ...</span><span class="sxs-lookup"><span data-stu-id="0e2b2-284">The sample demonstrates ...</span></span> |
| -------- | --------------------------- |
| [<span data-ttu-id="0e2b2-285">Modelové konvence</span><span class="sxs-lookup"><span data-stu-id="0e2b2-285">Model conventions</span></span>](#model-conventions)<br><br><span data-ttu-id="0e2b2-286">Conventions.Add</span><span class="sxs-lookup"><span data-stu-id="0e2b2-286">Conventions.Add</span></span><ul><li><span data-ttu-id="0e2b2-287">IPageRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="0e2b2-287">IPageRouteModelConvention</span></span></li><li><span data-ttu-id="0e2b2-288">IPageApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="0e2b2-288">IPageApplicationModelConvention</span></span></li><li><span data-ttu-id="0e2b2-289">IPageHandlerModelConvention</span><span class="sxs-lookup"><span data-stu-id="0e2b2-289">IPageHandlerModelConvention</span></span></li></ul> | <span data-ttu-id="0e2b2-290">Přidejte šablonu trasy a záhlaví na stránky aplikace.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-290">Add a route template and header to an app's pages.</span></span> |
| [<span data-ttu-id="0e2b2-291">Konvence akcí trasy stránky</span><span class="sxs-lookup"><span data-stu-id="0e2b2-291">Page route action conventions</span></span>](#page-route-action-conventions)<ul><li><span data-ttu-id="0e2b2-292">AddFolderRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="0e2b2-292">AddFolderRouteModelConvention</span></span></li><li><span data-ttu-id="0e2b2-293">AddPageRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="0e2b2-293">AddPageRouteModelConvention</span></span></li><li><span data-ttu-id="0e2b2-294">AddPageRoute</span><span class="sxs-lookup"><span data-stu-id="0e2b2-294">AddPageRoute</span></span></li></ul> | <span data-ttu-id="0e2b2-295">Přidejte šablonu trasy na stránky ve složce a na jednu stránku.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-295">Add a route template to pages in a folder and to a single page.</span></span> |
| [<span data-ttu-id="0e2b2-296">Konvence akcí modelu stránky</span><span class="sxs-lookup"><span data-stu-id="0e2b2-296">Page model action conventions</span></span>](#page-model-action-conventions)<ul><li><span data-ttu-id="0e2b2-297">AddFolderApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="0e2b2-297">AddFolderApplicationModelConvention</span></span></li><li><span data-ttu-id="0e2b2-298">AddPageApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="0e2b2-298">AddPageApplicationModelConvention</span></span></li><li><span data-ttu-id="0e2b2-299">ConfigureFilter (třída filtru, výraz lambda nebo továrna filtrů)</span><span class="sxs-lookup"><span data-stu-id="0e2b2-299">ConfigureFilter (filter class, lambda expression, or filter factory)</span></span></li></ul> | <span data-ttu-id="0e2b2-300">Přidejte záhlaví na stránky ve složce, přidejte záhlaví na jednu stránku a nakonfigurujte [továrnu filtrů](xref:mvc/controllers/filters#ifilterfactory) tak, aby přidala záhlaví na stránky aplikace.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-300">Add a header to pages in a folder, add a header to a single page, and configure a [filter factory](xref:mvc/controllers/filters#ifilterfactory) to add a header to an app's pages.</span></span> |

<span data-ttu-id="0e2b2-301">Razor Pages konvence jsou přidány <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> a konfigurovány pomocí metody rozšíření na <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> kolekci služeb ve `Startup` třídě.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-301">Razor Pages conventions are added and configured using the <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> extension method to <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> on the service collection in the `Startup` class.</span></span> <span data-ttu-id="0e2b2-302">Následující příklady konvence jsou vysvětleny dále v tomto tématu:</span><span class="sxs-lookup"><span data-stu-id="0e2b2-302">The following convention examples are explained later in this topic:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddRazorPagesOptions(options =>
        {
            options.Conventions.Add( ... );
            options.Conventions.AddFolderRouteModelConvention(
                "/OtherPages", model => { ... });
            options.Conventions.AddPageRouteModelConvention(
                "/About", model => { ... });
            options.Conventions.AddPageRoute(
                "/Contact", "TheContactPage/{text?}");
            options.Conventions.AddFolderApplicationModelConvention(
                "/OtherPages", model => { ... });
            options.Conventions.AddPageApplicationModelConvention(
                "/About", model => { ... });
            options.Conventions.ConfigureFilter(model => { ... });
            options.Conventions.ConfigureFilter( ... );
        });
}
```

## <a name="route-order"></a><span data-ttu-id="0e2b2-303">Pořadí postupu</span><span class="sxs-lookup"><span data-stu-id="0e2b2-303">Route order</span></span>

<span data-ttu-id="0e2b2-304">Trasy určují <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> pro zpracování (párování tras).</span><span class="sxs-lookup"><span data-stu-id="0e2b2-304">Routes specify an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> for processing (route matching).</span></span>

| <span data-ttu-id="0e2b2-305">Objednání</span><span class="sxs-lookup"><span data-stu-id="0e2b2-305">Order</span></span>            | <span data-ttu-id="0e2b2-306">Chování</span><span class="sxs-lookup"><span data-stu-id="0e2b2-306">Behavior</span></span> |
| :--------------: | -------- |
| <span data-ttu-id="0e2b2-307">-1</span><span class="sxs-lookup"><span data-stu-id="0e2b2-307">-1</span></span>               | <span data-ttu-id="0e2b2-308">Trasa je zpracována před zpracováním jiných tras.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-308">The route is processed before other routes are processed.</span></span> |
| <span data-ttu-id="0e2b2-309">0</span><span class="sxs-lookup"><span data-stu-id="0e2b2-309">0</span></span>                | <span data-ttu-id="0e2b2-310">Objednávka není zadána (výchozí hodnota).</span><span class="sxs-lookup"><span data-stu-id="0e2b2-310">Order isn't specified (default value).</span></span> <span data-ttu-id="0e2b2-311">Nepřiřazovat `Order` (`Order = null` `Order` ) výchozí trasu na 0 (nula) pro zpracování.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-311">Not assigning `Order` (`Order = null`) defaults the route `Order` to 0 (zero) for processing.</span></span> |
| <span data-ttu-id="0e2b2-312">1, 2, &hellip; n</span><span class="sxs-lookup"><span data-stu-id="0e2b2-312">1, 2, &hellip; n</span></span> | <span data-ttu-id="0e2b2-313">Určuje pořadí zpracování postupu.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-313">Specifies the route processing order.</span></span> |

<span data-ttu-id="0e2b2-314">Zpracování postupu je stanoveno úmluvou:</span><span class="sxs-lookup"><span data-stu-id="0e2b2-314">Route processing is established by convention:</span></span>

* <span data-ttu-id="0e2b2-315">Trasy jsou zpracovávány v sekvenčním pořadí (-1, 0, 1, 2, &hellip; n).</span><span class="sxs-lookup"><span data-stu-id="0e2b2-315">Routes are processed in sequential order (-1, 0, 1, 2, &hellip; n).</span></span>
* <span data-ttu-id="0e2b2-316">Pokud mají trasy `Order`stejné , nejkonkrétnější trasa se nejprve shoduje s méně specifickými trasami.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-316">When routes have the same `Order`, the most specific route is matched first followed by less specific routes.</span></span>
* <span data-ttu-id="0e2b2-317">Pokud trasy se `Order` stejným a stejným počtem parametrů odpovídají adrese URL požadavku, jsou trasy zpracovány v <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>pořadí, v jakém jsou přidány do .</span><span class="sxs-lookup"><span data-stu-id="0e2b2-317">When routes with the same `Order` and the same number of parameters match a request URL, routes are processed in the order that they're added to the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>.</span></span>

<span data-ttu-id="0e2b2-318">Pokud je to možné, vyhněte se v závislosti na zavedeném pořadí zpracování postupu.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-318">If possible, avoid depending on an established route processing order.</span></span> <span data-ttu-id="0e2b2-319">Obecně platí, že směrování vybere správnou trasu s párováním adres URL.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-319">Generally, routing selects the correct route with URL matching.</span></span> <span data-ttu-id="0e2b2-320">Pokud je nutné `Order` nastavit vlastnosti trasy pro směrování požadavků správně, schéma směrování aplikace je pravděpodobně matoucí pro klienty a křehké udržovat.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-320">If you must set route `Order` properties to route requests correctly, the app's routing scheme is probably confusing to clients and fragile to maintain.</span></span> <span data-ttu-id="0e2b2-321">Vyhledejte zjednodušení schématu směrování aplikace.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-321">Seek to simplify the app's routing scheme.</span></span> <span data-ttu-id="0e2b2-322">Ukázková aplikace vyžaduje explicitní pořadí zpracování trasy k předvedení několika scénářů směrování pomocí jedné aplikace.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-322">The sample app requires an explicit route processing order to demonstrate several routing scenarios using a single app.</span></span> <span data-ttu-id="0e2b2-323">Měli byste se však pokusit vyhnout `Order` se praxi nastavení postupu v produkčních aplikacích.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-323">However, you should attempt to avoid the practice of setting route `Order` in production apps.</span></span>

<span data-ttu-id="0e2b2-324">Razor Pages směrování a MVC řadič směrování sdílet implementaci.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-324">Razor Pages routing and MVC controller routing share an implementation.</span></span> <span data-ttu-id="0e2b2-325">Informace o pořadí tras v tématech MVC jsou k dispozici na [adrese Akce Směrování k kontrolorovi: Řazení tras atributů](xref:mvc/controllers/routing#ordering-attribute-routes).</span><span class="sxs-lookup"><span data-stu-id="0e2b2-325">Information on route order in the MVC topics is available at [Routing to controller actions: Ordering attribute routes](xref:mvc/controllers/routing#ordering-attribute-routes).</span></span>

## <a name="model-conventions"></a><span data-ttu-id="0e2b2-326">Modelové konvence</span><span class="sxs-lookup"><span data-stu-id="0e2b2-326">Model conventions</span></span>

<span data-ttu-id="0e2b2-327">Přidejte delegáta pro <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> přidání [konvencí modelu,](xref:mvc/controllers/application-model#conventions) které se vztahují k razor pages.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-327">Add a delegate for <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> to add [model conventions](xref:mvc/controllers/application-model#conventions) that apply to Razor Pages.</span></span>

### <a name="add-a-route-model-convention-to-all-pages"></a><span data-ttu-id="0e2b2-328">Přidání konvence modelu trasy na všechny stránky</span><span class="sxs-lookup"><span data-stu-id="0e2b2-328">Add a route model convention to all pages</span></span>

<span data-ttu-id="0e2b2-329">Slouží <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> k vytvoření <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> a přidání <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> a do kolekce instancí, které jsou použity během konstrukce modelu trasy stránky.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-329">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page route model construction.</span></span>

<span data-ttu-id="0e2b2-330">Ukázková aplikace `{globalTemplate?}` přidá šablonu trasy na všechny stránky v aplikaci:</span><span class="sxs-lookup"><span data-stu-id="0e2b2-330">The sample app adds a `{globalTemplate?}` route template to all of the pages in the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

<span data-ttu-id="0e2b2-331">Vlastnost <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> pro <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> je nastavena na `1`.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-331">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `1`.</span></span> <span data-ttu-id="0e2b2-332">Tím zajistíte následující chování při porovnávání tras v ukázkové aplikaci:</span><span class="sxs-lookup"><span data-stu-id="0e2b2-332">This ensures the following route matching behavior in the sample app:</span></span>

* <span data-ttu-id="0e2b2-333">Šablona trasy `TheContactPage/{text?}` pro je přidána později v tématu.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-333">A route template for `TheContactPage/{text?}` is added later in the topic.</span></span> <span data-ttu-id="0e2b2-334">Trasa Stránka kontaktu má `null` výchozí`Order = 0`pořadí ( ), `{globalTemplate?}` takže odpovídá před šablonou trasy.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-334">The Contact Page route has a default order of `null` (`Order = 0`), so it matches before the `{globalTemplate?}` route template.</span></span>
* <span data-ttu-id="0e2b2-335">Šablona `{aboutTemplate?}` trasy je přidána později v tématu.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-335">An `{aboutTemplate?}` route template is added later in the topic.</span></span> <span data-ttu-id="0e2b2-336">Šablona `{aboutTemplate?}` je `Order` dána . `2`</span><span class="sxs-lookup"><span data-stu-id="0e2b2-336">The `{aboutTemplate?}` template is given an `Order` of `2`.</span></span> <span data-ttu-id="0e2b2-337">Je-li požadována `/About/RouteDataValue`stránka O , je z `RouteData.Values["globalTemplate"]` `Order = 1`důvodu nastavení `RouteData.Values["aboutTemplate"]` `Order = 2`vlastnosti načtena `Order` hodnota RouteDataValue do položky ( ) a nikoli ( ).</span><span class="sxs-lookup"><span data-stu-id="0e2b2-337">When the About page is requested at `/About/RouteDataValue`, "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["aboutTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>
* <span data-ttu-id="0e2b2-338">Šablona `{otherPagesTemplate?}` trasy je přidána později v tématu.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-338">An `{otherPagesTemplate?}` route template is added later in the topic.</span></span> <span data-ttu-id="0e2b2-339">Šablona `{otherPagesTemplate?}` je `Order` dána . `2`</span><span class="sxs-lookup"><span data-stu-id="0e2b2-339">The `{otherPagesTemplate?}` template is given an `Order` of `2`.</span></span> <span data-ttu-id="0e2b2-340">Pokud je požadována libovolná stránka ve složce *Pages/OtherPages* s parametrem `/OtherPages/Page1/RouteDataValue`trasy `RouteData.Values["globalTemplate"]` (například), "RouteDataValue" je načtena do (`Order = 1`) a ne `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) z důvodu nastavení vlastnosti. `Order`</span><span class="sxs-lookup"><span data-stu-id="0e2b2-340">When any page in the *Pages/OtherPages* folder is requested with a route parameter (for example, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="0e2b2-341">Pokud je to možné, `Order`nenastavujte , což má za následek `Order = 0`.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-341">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="0e2b2-342">Při výběru správné trasy se můžete spolehnout na směrování.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-342">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="0e2b2-343">Razor Pages možnosti, <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>jako je například přidání , jsou `Startup.ConfigureServices`přidány při MVC je přidán do kolekce služeb v .</span><span class="sxs-lookup"><span data-stu-id="0e2b2-343">Razor Pages options, such as adding <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>, are added when MVC is added to the service collection in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="0e2b2-344">Například najdete v [ukázkové aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/).</span><span class="sxs-lookup"><span data-stu-id="0e2b2-344">For an example, see the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/).</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="0e2b2-345">Vyžádejte si `localhost:5000/About/GlobalRouteValue` stránku O vzorku a zkontrolujte výsledek:</span><span class="sxs-lookup"><span data-stu-id="0e2b2-345">Request the sample's About page at `localhost:5000/About/GlobalRouteValue` and inspect the result:</span></span>

![Stránka Informace je požadována s segmentem trasy GlobalRouteValue.](razor-pages-conventions/_static/about-page-global-template.png)

### <a name="add-an-app-model-convention-to-all-pages"></a><span data-ttu-id="0e2b2-348">Přidání konvence modelu aplikace na všechny stránky</span><span class="sxs-lookup"><span data-stu-id="0e2b2-348">Add an app model convention to all pages</span></span>

<span data-ttu-id="0e2b2-349">Slouží <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> k vytvoření <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> a přidání <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> a do kolekce instancí, které se použijí během konstrukce modelu aplikace stránky.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-349">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page app model construction.</span></span>

<span data-ttu-id="0e2b2-350">Chcete-li demonstrovat toto a další konvence dále `AddHeaderAttribute` v tématu, ukázková aplikace obsahuje třídu.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-350">To demonstrate this and other conventions later in the topic, the sample app includes an `AddHeaderAttribute` class.</span></span> <span data-ttu-id="0e2b2-351">Konstruktor třídy přijímá `name` řetězec `values` a pole řetězce.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-351">The class constructor accepts a `name` string and a `values` string array.</span></span> <span data-ttu-id="0e2b2-352">Tyto hodnoty se `OnResultExecuting` používají v jeho metodě nastavit hlavičku odpovědi.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-352">These values are used in its `OnResultExecuting` method to set a response header.</span></span> <span data-ttu-id="0e2b2-353">Celá třída je zobrazena v části [Akce modelu stránky](#page-model-action-conventions) dále v tématu.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-353">The full class is shown in the [Page model action conventions](#page-model-action-conventions) section later in the topic.</span></span>

<span data-ttu-id="0e2b2-354">Ukázková aplikace `AddHeaderAttribute` používá třídu k `GlobalHeader`přidání záhlaví , na všechny stránky v aplikaci:</span><span class="sxs-lookup"><span data-stu-id="0e2b2-354">The sample app uses the `AddHeaderAttribute` class to add a header, `GlobalHeader`, to all of the pages in the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

<span data-ttu-id="0e2b2-355">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="0e2b2-355">*Startup.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

<span data-ttu-id="0e2b2-356">Požádejte o stránku `localhost:5000/About` Informace v ukázce a zkontrolujte záhlaví, abyste zobrazili výsledek:</span><span class="sxs-lookup"><span data-stu-id="0e2b2-356">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![Záhlaví odpovědí na stránce O stránce ukazují, že byla přidána globalheader.](razor-pages-conventions/_static/about-page-global-header.png)

### <a name="add-a-handler-model-convention-to-all-pages"></a><span data-ttu-id="0e2b2-358">Přidání konvence modelu obslužné rutiny na všechny stránky</span><span class="sxs-lookup"><span data-stu-id="0e2b2-358">Add a handler model convention to all pages</span></span>

<span data-ttu-id="0e2b2-359">Slouží <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> k vytvoření <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> a přidání <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> a do kolekce instancí, které jsou použity během konstrukce modelu obslužné rutiny stránky.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-359">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page handler model construction.</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalPageHandlerModelConvention.cs?name=snippet1)]

<span data-ttu-id="0e2b2-360">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="0e2b2-360">*Startup.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet10)]

## <a name="page-route-action-conventions"></a><span data-ttu-id="0e2b2-361">Konvence akcí trasy stránky</span><span class="sxs-lookup"><span data-stu-id="0e2b2-361">Page route action conventions</span></span>

<span data-ttu-id="0e2b2-362">Výchozí zprostředkovatel modelu trasy, <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> který je odvozen od vyvolá ní konvence, které jsou navrženy tak, aby poskytovaly body rozšiřitelnosti pro konfiguraci tras stránek.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-362">The default route model provider that derives from <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> invokes conventions which are designed to provide extensibility points for configuring page routes.</span></span>

### <a name="folder-route-model-convention"></a><span data-ttu-id="0e2b2-363">Konvence modelu postupu složky</span><span class="sxs-lookup"><span data-stu-id="0e2b2-363">Folder route model convention</span></span>

<span data-ttu-id="0e2b2-364">Slouží <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> k vytvoření <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> a přidání, který <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> vyvolá akci pro všechny stránky pod zadanou složkou.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-364">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> for all of the pages under the specified folder.</span></span>

<span data-ttu-id="0e2b2-365">Ukázková aplikace <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> používá `{otherPagesTemplate?}` k přidání šablony trasy na stránky ve složce *OtherPages:*</span><span class="sxs-lookup"><span data-stu-id="0e2b2-365">The sample app uses <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> to add an `{otherPagesTemplate?}` route template to the pages in the *OtherPages* folder:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

<span data-ttu-id="0e2b2-366">Vlastnost <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> pro <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> je nastavena na `2`.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-366">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `2`.</span></span> <span data-ttu-id="0e2b2-367">Tím je zajištěno, `{globalTemplate?}` že šablona pro `1`(nastavená dříve v tématu na ) má prioritu pro první pozici hodnoty dat trasy, pokud je poskytnuta jedna hodnota trasy.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-367">This ensures that the template for `{globalTemplate?}` (set earlier in the topic to `1`) is given priority for the first route data value position when a single route value is provided.</span></span> <span data-ttu-id="0e2b2-368">Pokud je požadována stránka ve složce *Pages/OtherPages* s `/OtherPages/Page1/RouteDataValue`hodnotou parametru trasy (například), "RouteDataValue" je načtena `RouteData.Values["globalTemplate"]` do (`Order = 1`) a `RouteData.Values["otherPagesTemplate"]` ne (`Order = 2`) z důvodu nastavení vlastnosti. `Order`</span><span class="sxs-lookup"><span data-stu-id="0e2b2-368">If a page in the *Pages/OtherPages* folder is requested with a route parameter value (for example, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="0e2b2-369">Pokud je to možné, `Order`nenastavujte , což má za následek `Order = 0`.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-369">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="0e2b2-370">Při výběru správné trasy se můžete spolehnout na směrování.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-370">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="0e2b2-371">Vyžádejte si stránku Stránky 1 ukázky a `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` zkontrolujte výsledek:</span><span class="sxs-lookup"><span data-stu-id="0e2b2-371">Request the sample's Page1 page at `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` and inspect the result:</span></span>

![Stránka1 ve složce OtherPages je požadována s segmentem trasy GlobalRouteValue a OtherPagesRouteValue.](razor-pages-conventions/_static/otherpages-page1-global-and-otherpages-templates.png)

### <a name="page-route-model-convention"></a><span data-ttu-id="0e2b2-374">Konvence modelu trasy stránky</span><span class="sxs-lookup"><span data-stu-id="0e2b2-374">Page route model convention</span></span>

<span data-ttu-id="0e2b2-375">Slouží <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> k vytvoření <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> a přidání, která <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> vyvolá akci na stránce se zadaným názvem.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-375">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> for the page with the specified name.</span></span>

<span data-ttu-id="0e2b2-376">Ukázková aplikace `AddPageRouteModelConvention` používá `{aboutTemplate?}` k přidání šablony trasy na stránku Informace:</span><span class="sxs-lookup"><span data-stu-id="0e2b2-376">The sample app uses `AddPageRouteModelConvention` to add an `{aboutTemplate?}` route template to the About page:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

<span data-ttu-id="0e2b2-377">Vlastnost <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> pro <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> je nastavena na `2`.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-377">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `2`.</span></span> <span data-ttu-id="0e2b2-378">Tím je zajištěno, `{globalTemplate?}` že šablona pro `1`(nastavená dříve v tématu na ) má prioritu pro první pozici hodnoty dat trasy, pokud je poskytnuta jedna hodnota trasy.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-378">This ensures that the template for `{globalTemplate?}` (set earlier in the topic to `1`) is given priority for the first route data value position when a single route value is provided.</span></span> <span data-ttu-id="0e2b2-379">Pokud je požadována stránka O s `/About/RouteDataValue`hodnotou parametru trasy `RouteData.Values["globalTemplate"]` na`Order = 1`, `RouteData.Values["aboutTemplate"]` "RouteDataValue" je načten do ( ) a ne (`Order = 2`) z důvodu nastavení vlastnosti. `Order`</span><span class="sxs-lookup"><span data-stu-id="0e2b2-379">If the About page is requested with a route parameter value at `/About/RouteDataValue`, "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["aboutTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="0e2b2-380">Pokud je to možné, `Order`nenastavujte , což má za následek `Order = 0`.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-380">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="0e2b2-381">Při výběru správné trasy se můžete spolehnout na směrování.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-381">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="0e2b2-382">Vyžádejte si `localhost:5000/About/GlobalRouteValue/AboutRouteValue` stránku O vzorku a zkontrolujte výsledek:</span><span class="sxs-lookup"><span data-stu-id="0e2b2-382">Request the sample's About page at `localhost:5000/About/GlobalRouteValue/AboutRouteValue` and inspect the result:</span></span>

![O stránce je požadováno s segmenty postupu pro GlobalRouteValue a AboutRouteValue.](razor-pages-conventions/_static/about-page-global-and-about-templates.png)

## <a name="use-a-parameter-transformer-to-customize-page-routes"></a><span data-ttu-id="0e2b2-385">Přizpůsobení tras stránek pomocí transformátoru parametrů</span><span class="sxs-lookup"><span data-stu-id="0e2b2-385">Use a parameter transformer to customize page routes</span></span>

<span data-ttu-id="0e2b2-386">Trasy stránek generované ASP.NET Core lze přizpůsobit pomocí transformátoru parametrů.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-386">Page routes generated by ASP.NET Core can be customized using a parameter transformer.</span></span> <span data-ttu-id="0e2b2-387">Parametr transformátor `IOutboundParameterTransformer` implementuje a transformuje hodnotu parametrů.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-387">A parameter transformer implements `IOutboundParameterTransformer` and transforms the value of parameters.</span></span> <span data-ttu-id="0e2b2-388">Například vlastní `SlugifyParameterTransformer` parametr transformátor `SubscriptionManagement` změní `subscription-management`hodnotu trasy na .</span><span class="sxs-lookup"><span data-stu-id="0e2b2-388">For example, a custom `SlugifyParameterTransformer` parameter transformer changes the `SubscriptionManagement` route value to `subscription-management`.</span></span>

<span data-ttu-id="0e2b2-389">Konvence `PageRouteTransformerConvention` modelu trasy stránky aplikuje transformátor parametrů na segmenty složek a názvů souborů automaticky generovaných tras stránek v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-389">The `PageRouteTransformerConvention` page route model convention applies a parameter transformer to the folder and file name segments of automatically generated page routes in an app.</span></span> <span data-ttu-id="0e2b2-390">Například soubor Razor Pages na *adrese /Pages/SubscriptionManagement/ViewAll.cshtml* by `/SubscriptionManagement/ViewAll` `/subscription-management/view-all`měl svou trasu přepsánz do .</span><span class="sxs-lookup"><span data-stu-id="0e2b2-390">For example, the Razor Pages file at */Pages/SubscriptionManagement/ViewAll.cshtml* would have its route rewritten from `/SubscriptionManagement/ViewAll` to `/subscription-management/view-all`.</span></span>

<span data-ttu-id="0e2b2-391">`PageRouteTransformerConvention`transformuje pouze automaticky generované segmenty trasy stránky, které pocházejí ze složky Razor Pages a názvu souboru.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-391">`PageRouteTransformerConvention` only transforms the automatically generated segments of a page route that come from the Razor Pages folder and file name.</span></span> <span data-ttu-id="0e2b2-392">Netransformuje segmenty trasy přidané `@page` se směrnicí.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-392">It doesn't transform route segments added with the `@page` directive.</span></span> <span data-ttu-id="0e2b2-393">Konvence také netransformuje trasy <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>přidané .</span><span class="sxs-lookup"><span data-stu-id="0e2b2-393">The convention also doesn't transform routes added by <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>.</span></span>

<span data-ttu-id="0e2b2-394">Je `PageRouteTransformerConvention` registrován jako možnost `Startup.ConfigureServices`v :</span><span class="sxs-lookup"><span data-stu-id="0e2b2-394">The `PageRouteTransformerConvention` is registered as an option in `Startup.ConfigureServices`:</span></span>

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

## <a name="configure-a-page-route"></a><span data-ttu-id="0e2b2-395">Konfigurace trasy stránky</span><span class="sxs-lookup"><span data-stu-id="0e2b2-395">Configure a page route</span></span>

<span data-ttu-id="0e2b2-396">Slouží <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> ke konfiguraci trasy ke stránce na zadané cestě stránky.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-396">Use <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> to configure a route to a page at the specified page path.</span></span> <span data-ttu-id="0e2b2-397">Generované odkazy na stránku používají zadanou trasu.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-397">Generated links to the page use your specified route.</span></span> <span data-ttu-id="0e2b2-398">`AddPageRoute`používá `AddPageRouteModelConvention` k určení trasy.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-398">`AddPageRoute` uses `AddPageRouteModelConvention` to establish the route.</span></span>

<span data-ttu-id="0e2b2-399">Ukázková aplikace vytvoří `/TheContactPage` trasu pro *Contact.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="0e2b2-399">The sample app creates a route to `/TheContactPage` for *Contact.cshtml*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet5)]

<span data-ttu-id="0e2b2-400">Na stránku Kontakt lze `/Contact` také dosáhnout prostřednictvím výchozí trasy.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-400">The Contact page can also be reached at `/Contact` via its default route.</span></span>

<span data-ttu-id="0e2b2-401">Vlastní trasa ukázkové aplikace na stránku `text` Kontakt umožňuje`{text?}`volitelný segment trasy ( ).</span><span class="sxs-lookup"><span data-stu-id="0e2b2-401">The sample app's custom route to the Contact page allows for an optional `text` route segment (`{text?}`).</span></span> <span data-ttu-id="0e2b2-402">Stránka také obsahuje tento volitelný `@page` segment ve své směrnici v `/Contact` případě, že návštěvník přistupuje ke stránce na své trase:</span><span class="sxs-lookup"><span data-stu-id="0e2b2-402">The page also includes this optional segment in its `@page` directive in case the visitor accesses the page at its `/Contact` route:</span></span>

[!code-cshtml[](razor-pages-conventions/samples/2.x/SampleApp/Pages/Contact.cshtml?highlight=1)]

<span data-ttu-id="0e2b2-403">Všimněte si, že adresa URL vygenerovaná pro odkaz **Kontakt** na vykreslené stránce odráží aktualizovanou trasu:</span><span class="sxs-lookup"><span data-stu-id="0e2b2-403">Note that the URL generated for the **Contact** link in the rendered page reflects the updated route:</span></span>

![Ukázkový odkaz Kontakt aplikace na navigačním panelu](razor-pages-conventions/_static/contact-link.png)

![Kontrola odkazu Kontakt v vykresleném KÓDU HTML znamená, že href je nastaven na '/TheContactPage'](razor-pages-conventions/_static/contact-link-source.png)

<span data-ttu-id="0e2b2-406">Navštivte stránku Kontakt na běžné `/Contact`trase , `/TheContactPage`nebo na vlastní trase .</span><span class="sxs-lookup"><span data-stu-id="0e2b2-406">Visit the Contact page at either its ordinary route, `/Contact`, or the custom route, `/TheContactPage`.</span></span> <span data-ttu-id="0e2b2-407">Pokud zadáte další `text` segment trasy, stránka zobrazí segment kódovaný HTML, který zadáte:</span><span class="sxs-lookup"><span data-stu-id="0e2b2-407">If you supply an additional `text` route segment, the page shows the HTML-encoded segment that you provide:</span></span>

![Okraj ový příklad poskytnutí volitelného segmentu trasy "text" textvalue v adrese URL.](razor-pages-conventions/_static/route-segment-with-custom-route.png)

## <a name="page-model-action-conventions"></a><span data-ttu-id="0e2b2-410">Konvence akcí modelu stránky</span><span class="sxs-lookup"><span data-stu-id="0e2b2-410">Page model action conventions</span></span>

<span data-ttu-id="0e2b2-411">Výchozí zprostředkovatel modelu stránky, <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> který implementuje vyvolá konvence, které jsou navrženy tak, aby poskytovaly body rozšiřitelnosti pro konfiguraci modelů stránek.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-411">The default page model provider that implements <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> invokes conventions which are designed to provide extensibility points for configuring page models.</span></span> <span data-ttu-id="0e2b2-412">Tyto konvence jsou užitečné při vytváření a úpravách scénáře zjišťování a zpracování stránky.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-412">These conventions are useful when building and modifying page discovery and processing scenarios.</span></span>

<span data-ttu-id="0e2b2-413">Příklady v této části ukázkové aplikace `AddHeaderAttribute` používá třídu, což je <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>, která používá hlavičku odpovědi:</span><span class="sxs-lookup"><span data-stu-id="0e2b2-413">For the examples in this section, the sample app uses an `AddHeaderAttribute` class, which is a <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>, that applies a response header:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

<span data-ttu-id="0e2b2-414">Pomocí konvencí ukázka ukazuje, jak použít atribut pro všechny stránky ve složce a na jednu stránku.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-414">Using conventions, the sample demonstrates how to apply the attribute to all of the pages in a folder and to a single page.</span></span>

<span data-ttu-id="0e2b2-415">**Konvence modelu aplikace složky**</span><span class="sxs-lookup"><span data-stu-id="0e2b2-415">**Folder app model convention**</span></span>

<span data-ttu-id="0e2b2-416">Slouží <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> k vytvoření <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> a přidání akce, <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> která vyvolá akci na instancích pro všechny stránky v zadané složce.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-416">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> that invokes an action on <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> instances for all pages under the specified folder.</span></span>

<span data-ttu-id="0e2b2-417">Ukázka ukazuje použití `AddFolderApplicationModelConvention` přidáním záhlaví `OtherPagesHeader`, na stránky uvnitř složky *OtherPages* aplikace:</span><span class="sxs-lookup"><span data-stu-id="0e2b2-417">The sample demonstrates the use of `AddFolderApplicationModelConvention` by adding a header, `OtherPagesHeader`, to the pages inside the *OtherPages* folder of the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet6)]

<span data-ttu-id="0e2b2-418">Požádejte ukázkovou stránku `localhost:5000/OtherPages/Page1` Page1 a zkontrolujte záhlaví, abyste zobrazili výsledek:</span><span class="sxs-lookup"><span data-stu-id="0e2b2-418">Request the sample's Page1 page at `localhost:5000/OtherPages/Page1` and inspect the headers to view the result:</span></span>

![Záhlaví odpovědí na stránce OtherPages/Page1 ukazují, že byla přidána hlavička OtherPagesHeader.](razor-pages-conventions/_static/page1-otherpages-header.png)

<span data-ttu-id="0e2b2-420">**Konvence modelu aplikace stránky**</span><span class="sxs-lookup"><span data-stu-id="0e2b2-420">**Page app model convention**</span></span>

<span data-ttu-id="0e2b2-421">Slouží <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> k vytvoření <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> a přidání, která <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> vyvolá akci na stránce se zadaným názvem.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-421">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> for the page with the specified name.</span></span>

<span data-ttu-id="0e2b2-422">Ukázka ukazuje použití `AddPageApplicationModelConvention` přidáním záhlaví `AboutHeader`, na stránku O:</span><span class="sxs-lookup"><span data-stu-id="0e2b2-422">The sample demonstrates the use of `AddPageApplicationModelConvention` by adding a header, `AboutHeader`, to the About page:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet7)]

<span data-ttu-id="0e2b2-423">Požádejte o stránku `localhost:5000/About` Informace v ukázce a zkontrolujte záhlaví, abyste zobrazili výsledek:</span><span class="sxs-lookup"><span data-stu-id="0e2b2-423">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![Záhlaví odpovědí na stránce O stránce ukazují, že byla přidána hlavička AboutHeader.](razor-pages-conventions/_static/about-page-about-header.png)

<span data-ttu-id="0e2b2-425">**Konfigurace filtru**</span><span class="sxs-lookup"><span data-stu-id="0e2b2-425">**Configure a filter**</span></span>

<span data-ttu-id="0e2b2-426"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*>nakonfiguruje použití zadaného filtru.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-426"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configures the specified filter to apply.</span></span> <span data-ttu-id="0e2b2-427">Můžete implementovat třídu filtru, ale ukázková aplikace ukazuje, jak implementovat filtr ve výrazu lambda, který je implementován na pozadí jako továrna, která vrací filtr:</span><span class="sxs-lookup"><span data-stu-id="0e2b2-427">You can implement a filter class, but the sample app shows how to implement a filter in a lambda expression, which is implemented behind-the-scenes as a factory that returns a filter:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet8)]

<span data-ttu-id="0e2b2-428">Model aplikace stránky se používá ke kontrole relativní cesty pro segmenty, které vedou na stránku Stránka2 ve složce *OtherPages.*</span><span class="sxs-lookup"><span data-stu-id="0e2b2-428">The page app model is used to check the relative path for segments that lead to the Page2 page in the *OtherPages* folder.</span></span> <span data-ttu-id="0e2b2-429">Pokud podmínka projde, je přidána hlavička.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-429">If the condition passes, a header is added.</span></span> <span data-ttu-id="0e2b2-430">Pokud ne, `EmptyFilter` použije se.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-430">If not, the `EmptyFilter` is applied.</span></span>

<span data-ttu-id="0e2b2-431">`EmptyFilter`je [filtr akce](xref:mvc/controllers/filters#action-filters).</span><span class="sxs-lookup"><span data-stu-id="0e2b2-431">`EmptyFilter` is an [Action filter](xref:mvc/controllers/filters#action-filters).</span></span> <span data-ttu-id="0e2b2-432">Vzhledem k tomu, že filtry akce jsou ignorovány Razor Pages, `EmptyFilter` nemá žádný účinek, jak bylo zamýšleno, pokud cesta neobsahuje `OtherPages/Page2`.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-432">Since Action filters are ignored by Razor Pages, the `EmptyFilter` has no effect as intended if the path doesn't contain `OtherPages/Page2`.</span></span>

<span data-ttu-id="0e2b2-433">Požádejte stránku Page2 `localhost:5000/OtherPages/Page2` ukázky a zkontrolujte záhlaví, abyste zobrazili výsledek:</span><span class="sxs-lookup"><span data-stu-id="0e2b2-433">Request the sample's Page2 page at `localhost:5000/OtherPages/Page2` and inspect the headers to view the result:</span></span>

![OtherPagesPage2Header je přidán do odpovědi pro Page2.](razor-pages-conventions/_static/page2-filter-header.png)

<span data-ttu-id="0e2b2-435">**Konfigurace výroby filtrů**</span><span class="sxs-lookup"><span data-stu-id="0e2b2-435">**Configure a filter factory**</span></span>

<span data-ttu-id="0e2b2-436"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*>nakonfiguruje zadanou továrnu tak, aby na všechny stránky Razor [použila filtry.](xref:mvc/controllers/filters)</span><span class="sxs-lookup"><span data-stu-id="0e2b2-436"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configures the specified factory to apply [filters](xref:mvc/controllers/filters) to all Razor Pages.</span></span>

<span data-ttu-id="0e2b2-437">Ukázková aplikace poskytuje příklad použití [továrny filtrů](xref:mvc/controllers/filters#ifilterfactory) přidáním záhlaví `FilterFactoryHeader`, se dvěma hodnotami na stránky aplikace:</span><span class="sxs-lookup"><span data-stu-id="0e2b2-437">The sample app provides an example of using a [filter factory](xref:mvc/controllers/filters#ifilterfactory) by adding a header, `FilterFactoryHeader`, with two values to the app's pages:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet9)]

<span data-ttu-id="0e2b2-438">*AddHeaderWithFactory.cs*:</span><span class="sxs-lookup"><span data-stu-id="0e2b2-438">*AddHeaderWithFactory.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Factories/AddHeaderWithFactory.cs?name=snippet1)]

<span data-ttu-id="0e2b2-439">Požádejte o stránku `localhost:5000/About` Informace v ukázce a zkontrolujte záhlaví, abyste zobrazili výsledek:</span><span class="sxs-lookup"><span data-stu-id="0e2b2-439">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![Záhlaví odpovědí na stránce O stránce ukazují, že byly přidány dvě záhlaví FilterFactoryHeader.](razor-pages-conventions/_static/about-page-filter-factory-header.png)

## <a name="mvc-filters-and-the-page-filter-ipagefilter"></a><span data-ttu-id="0e2b2-441">Filtry MVC a filtr Stránky (IPageFilter)</span><span class="sxs-lookup"><span data-stu-id="0e2b2-441">MVC Filters and the Page filter (IPageFilter)</span></span>

<span data-ttu-id="0e2b2-442">[Filtry akce](xref:mvc/controllers/filters#action-filters) MVC jsou razor pages ignorovány, protože Razor Pages používají metody obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-442">MVC [Action filters](xref:mvc/controllers/filters#action-filters) are ignored by Razor Pages, since Razor Pages use handler methods.</span></span> <span data-ttu-id="0e2b2-443">K dispozici jsou další typy filtrů MVC: [Autorizace](xref:mvc/controllers/filters#authorization-filters), [Výjimka](xref:mvc/controllers/filters#exception-filters), [Prostředek](xref:mvc/controllers/filters#resource-filters)a [Výsledek](xref:mvc/controllers/filters#result-filters).</span><span class="sxs-lookup"><span data-stu-id="0e2b2-443">Other types of MVC filters are available for you to use: [Authorization](xref:mvc/controllers/filters#authorization-filters), [Exception](xref:mvc/controllers/filters#exception-filters), [Resource](xref:mvc/controllers/filters#resource-filters), and [Result](xref:mvc/controllers/filters#result-filters).</span></span> <span data-ttu-id="0e2b2-444">Další informace naleznete v tématu [Filtry.](xref:mvc/controllers/filters)</span><span class="sxs-lookup"><span data-stu-id="0e2b2-444">For more information, see the [Filters](xref:mvc/controllers/filters) topic.</span></span>

<span data-ttu-id="0e2b2-445">Filtr Stránka<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>( ) je filtr, který se vztahuje na žiletky stránky.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-445">The Page filter (<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>) is a filter that applies to Razor Pages.</span></span> <span data-ttu-id="0e2b2-446">Další informace naleznete v [tématu Filter methods for Razor Pages](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="0e2b2-446">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="0e2b2-447">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="0e2b2-447">Additional resources</span></span>

* <xref:security/authorization/razor-pages-authorization>
* <xref:mvc/controllers/areas#areas-with-razor-pages>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="0e2b2-448">Přečtěte si, jak používat konvence trasy stránky [a modelu aplikace](xref:mvc/controllers/application-model#conventions) k řízení směrování, zjišťování a zpracování stránek v aplikacích Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-448">Learn how to use page [route and app model provider conventions](xref:mvc/controllers/application-model#conventions) to control page routing, discovery, and processing in Razor Pages apps.</span></span>

<span data-ttu-id="0e2b2-449">Pokud potřebujete nakonfigurovat vlastní trasy stránek pro jednotlivé stránky, nakonfigurujte směrování na stránky pomocí [konvence AddPageRoute](#configure-a-page-route) popsané dále v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-449">When you need to configure custom page routes for individual pages, configure routing to pages with the [AddPageRoute convention](#configure-a-page-route) described later in this topic.</span></span>

<span data-ttu-id="0e2b2-450">Chcete-li určit trasu stránky, přidat segmenty trasy nebo přidat parametry do trasy, použijte direktivu `@page` stránky.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-450">To specify a page route, add route segments, or add parameters to a route, use the page's `@page` directive.</span></span> <span data-ttu-id="0e2b2-451">Další informace naleznete [v tématu Vlastní trasy](xref:razor-pages/index#custom-routes).</span><span class="sxs-lookup"><span data-stu-id="0e2b2-451">For more information, see [Custom routes](xref:razor-pages/index#custom-routes).</span></span>

<span data-ttu-id="0e2b2-452">Existují vyhrazená slova, která nelze použít jako segmenty trasy nebo názvy parametrů.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-452">There are reserved words that can't be used as route segments or parameter names.</span></span> <span data-ttu-id="0e2b2-453">Další informace naleznete v [tématu Směrování: Vyhrazené názvy směrování](xref:fundamentals/routing#reserved-routing-names).</span><span class="sxs-lookup"><span data-stu-id="0e2b2-453">For more information, see [Routing: Reserved routing names](xref:fundamentals/routing#reserved-routing-names).</span></span>

<span data-ttu-id="0e2b2-454">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) [(jak stáhnout)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="0e2b2-454">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

| <span data-ttu-id="0e2b2-455">Scénář</span><span class="sxs-lookup"><span data-stu-id="0e2b2-455">Scenario</span></span> | <span data-ttu-id="0e2b2-456">Vzorek ukazuje ...</span><span class="sxs-lookup"><span data-stu-id="0e2b2-456">The sample demonstrates ...</span></span> |
| -------- | --------------------------- |
| [<span data-ttu-id="0e2b2-457">Modelové konvence</span><span class="sxs-lookup"><span data-stu-id="0e2b2-457">Model conventions</span></span>](#model-conventions)<br><br><span data-ttu-id="0e2b2-458">Conventions.Add</span><span class="sxs-lookup"><span data-stu-id="0e2b2-458">Conventions.Add</span></span><ul><li><span data-ttu-id="0e2b2-459">IPageRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="0e2b2-459">IPageRouteModelConvention</span></span></li><li><span data-ttu-id="0e2b2-460">IPageApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="0e2b2-460">IPageApplicationModelConvention</span></span></li><li><span data-ttu-id="0e2b2-461">IPageHandlerModelConvention</span><span class="sxs-lookup"><span data-stu-id="0e2b2-461">IPageHandlerModelConvention</span></span></li></ul> | <span data-ttu-id="0e2b2-462">Přidejte šablonu trasy a záhlaví na stránky aplikace.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-462">Add a route template and header to an app's pages.</span></span> |
| [<span data-ttu-id="0e2b2-463">Konvence akcí trasy stránky</span><span class="sxs-lookup"><span data-stu-id="0e2b2-463">Page route action conventions</span></span>](#page-route-action-conventions)<ul><li><span data-ttu-id="0e2b2-464">AddFolderRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="0e2b2-464">AddFolderRouteModelConvention</span></span></li><li><span data-ttu-id="0e2b2-465">AddPageRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="0e2b2-465">AddPageRouteModelConvention</span></span></li><li><span data-ttu-id="0e2b2-466">AddPageRoute</span><span class="sxs-lookup"><span data-stu-id="0e2b2-466">AddPageRoute</span></span></li></ul> | <span data-ttu-id="0e2b2-467">Přidejte šablonu trasy na stránky ve složce a na jednu stránku.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-467">Add a route template to pages in a folder and to a single page.</span></span> |
| [<span data-ttu-id="0e2b2-468">Konvence akcí modelu stránky</span><span class="sxs-lookup"><span data-stu-id="0e2b2-468">Page model action conventions</span></span>](#page-model-action-conventions)<ul><li><span data-ttu-id="0e2b2-469">AddFolderApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="0e2b2-469">AddFolderApplicationModelConvention</span></span></li><li><span data-ttu-id="0e2b2-470">AddPageApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="0e2b2-470">AddPageApplicationModelConvention</span></span></li><li><span data-ttu-id="0e2b2-471">ConfigureFilter (třída filtru, výraz lambda nebo továrna filtrů)</span><span class="sxs-lookup"><span data-stu-id="0e2b2-471">ConfigureFilter (filter class, lambda expression, or filter factory)</span></span></li></ul> | <span data-ttu-id="0e2b2-472">Přidejte záhlaví na stránky ve složce, přidejte záhlaví na jednu stránku a nakonfigurujte [továrnu filtrů](xref:mvc/controllers/filters#ifilterfactory) tak, aby přidala záhlaví na stránky aplikace.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-472">Add a header to pages in a folder, add a header to a single page, and configure a [filter factory](xref:mvc/controllers/filters#ifilterfactory) to add a header to an app's pages.</span></span> |

<span data-ttu-id="0e2b2-473">Razor Pages konvence jsou přidány <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> a konfigurovány pomocí metody rozšíření na <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> kolekci služeb ve `Startup` třídě.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-473">Razor Pages conventions are added and configured using the <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> extension method to <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> on the service collection in the `Startup` class.</span></span> <span data-ttu-id="0e2b2-474">Následující příklady konvence jsou vysvětleny dále v tomto tématu:</span><span class="sxs-lookup"><span data-stu-id="0e2b2-474">The following convention examples are explained later in this topic:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddRazorPagesOptions(options =>
        {
            options.Conventions.Add( ... );
            options.Conventions.AddFolderRouteModelConvention(
                "/OtherPages", model => { ... });
            options.Conventions.AddPageRouteModelConvention(
                "/About", model => { ... });
            options.Conventions.AddPageRoute(
                "/Contact", "TheContactPage/{text?}");
            options.Conventions.AddFolderApplicationModelConvention(
                "/OtherPages", model => { ... });
            options.Conventions.AddPageApplicationModelConvention(
                "/About", model => { ... });
            options.Conventions.ConfigureFilter(model => { ... });
            options.Conventions.ConfigureFilter( ... );
        });
}
```

## <a name="route-order"></a><span data-ttu-id="0e2b2-475">Pořadí postupu</span><span class="sxs-lookup"><span data-stu-id="0e2b2-475">Route order</span></span>

<span data-ttu-id="0e2b2-476">Trasy určují <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> pro zpracování (párování tras).</span><span class="sxs-lookup"><span data-stu-id="0e2b2-476">Routes specify an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> for processing (route matching).</span></span>

| <span data-ttu-id="0e2b2-477">Objednání</span><span class="sxs-lookup"><span data-stu-id="0e2b2-477">Order</span></span>            | <span data-ttu-id="0e2b2-478">Chování</span><span class="sxs-lookup"><span data-stu-id="0e2b2-478">Behavior</span></span> |
| :--------------: | -------- |
| <span data-ttu-id="0e2b2-479">-1</span><span class="sxs-lookup"><span data-stu-id="0e2b2-479">-1</span></span>               | <span data-ttu-id="0e2b2-480">Trasa je zpracována před zpracováním jiných tras.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-480">The route is processed before other routes are processed.</span></span> |
| <span data-ttu-id="0e2b2-481">0</span><span class="sxs-lookup"><span data-stu-id="0e2b2-481">0</span></span>                | <span data-ttu-id="0e2b2-482">Objednávka není zadána (výchozí hodnota).</span><span class="sxs-lookup"><span data-stu-id="0e2b2-482">Order isn't specified (default value).</span></span> <span data-ttu-id="0e2b2-483">Nepřiřazovat `Order` (`Order = null` `Order` ) výchozí trasu na 0 (nula) pro zpracování.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-483">Not assigning `Order` (`Order = null`) defaults the route `Order` to 0 (zero) for processing.</span></span> |
| <span data-ttu-id="0e2b2-484">1, 2, &hellip; n</span><span class="sxs-lookup"><span data-stu-id="0e2b2-484">1, 2, &hellip; n</span></span> | <span data-ttu-id="0e2b2-485">Určuje pořadí zpracování postupu.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-485">Specifies the route processing order.</span></span> |

<span data-ttu-id="0e2b2-486">Zpracování postupu je stanoveno úmluvou:</span><span class="sxs-lookup"><span data-stu-id="0e2b2-486">Route processing is established by convention:</span></span>

* <span data-ttu-id="0e2b2-487">Trasy jsou zpracovávány v sekvenčním pořadí (-1, 0, 1, 2, &hellip; n).</span><span class="sxs-lookup"><span data-stu-id="0e2b2-487">Routes are processed in sequential order (-1, 0, 1, 2, &hellip; n).</span></span>
* <span data-ttu-id="0e2b2-488">Pokud mají trasy `Order`stejné , nejkonkrétnější trasa se nejprve shoduje s méně specifickými trasami.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-488">When routes have the same `Order`, the most specific route is matched first followed by less specific routes.</span></span>
* <span data-ttu-id="0e2b2-489">Pokud trasy se `Order` stejným a stejným počtem parametrů odpovídají adrese URL požadavku, jsou trasy zpracovány v <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>pořadí, v jakém jsou přidány do .</span><span class="sxs-lookup"><span data-stu-id="0e2b2-489">When routes with the same `Order` and the same number of parameters match a request URL, routes are processed in the order that they're added to the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>.</span></span>

<span data-ttu-id="0e2b2-490">Pokud je to možné, vyhněte se v závislosti na zavedeném pořadí zpracování postupu.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-490">If possible, avoid depending on an established route processing order.</span></span> <span data-ttu-id="0e2b2-491">Obecně platí, že směrování vybere správnou trasu s párováním adres URL.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-491">Generally, routing selects the correct route with URL matching.</span></span> <span data-ttu-id="0e2b2-492">Pokud je nutné `Order` nastavit vlastnosti trasy pro směrování požadavků správně, schéma směrování aplikace je pravděpodobně matoucí pro klienty a křehké udržovat.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-492">If you must set route `Order` properties to route requests correctly, the app's routing scheme is probably confusing to clients and fragile to maintain.</span></span> <span data-ttu-id="0e2b2-493">Vyhledejte zjednodušení schématu směrování aplikace.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-493">Seek to simplify the app's routing scheme.</span></span> <span data-ttu-id="0e2b2-494">Ukázková aplikace vyžaduje explicitní pořadí zpracování trasy k předvedení několika scénářů směrování pomocí jedné aplikace.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-494">The sample app requires an explicit route processing order to demonstrate several routing scenarios using a single app.</span></span> <span data-ttu-id="0e2b2-495">Měli byste se však pokusit vyhnout `Order` se praxi nastavení postupu v produkčních aplikacích.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-495">However, you should attempt to avoid the practice of setting route `Order` in production apps.</span></span>

<span data-ttu-id="0e2b2-496">Razor Pages směrování a MVC řadič směrování sdílet implementaci.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-496">Razor Pages routing and MVC controller routing share an implementation.</span></span> <span data-ttu-id="0e2b2-497">Informace o pořadí tras v tématech MVC jsou k dispozici na [adrese Akce Směrování k kontrolorovi: Řazení tras atributů](xref:mvc/controllers/routing#ordering-attribute-routes).</span><span class="sxs-lookup"><span data-stu-id="0e2b2-497">Information on route order in the MVC topics is available at [Routing to controller actions: Ordering attribute routes](xref:mvc/controllers/routing#ordering-attribute-routes).</span></span>

## <a name="model-conventions"></a><span data-ttu-id="0e2b2-498">Modelové konvence</span><span class="sxs-lookup"><span data-stu-id="0e2b2-498">Model conventions</span></span>

<span data-ttu-id="0e2b2-499">Přidejte delegáta pro <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> přidání [konvencí modelu,](xref:mvc/controllers/application-model#conventions) které se vztahují k razor pages.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-499">Add a delegate for <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> to add [model conventions](xref:mvc/controllers/application-model#conventions) that apply to Razor Pages.</span></span>

### <a name="add-a-route-model-convention-to-all-pages"></a><span data-ttu-id="0e2b2-500">Přidání konvence modelu trasy na všechny stránky</span><span class="sxs-lookup"><span data-stu-id="0e2b2-500">Add a route model convention to all pages</span></span>

<span data-ttu-id="0e2b2-501">Slouží <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> k vytvoření <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> a přidání <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> a do kolekce instancí, které jsou použity během konstrukce modelu trasy stránky.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-501">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page route model construction.</span></span>

<span data-ttu-id="0e2b2-502">Ukázková aplikace `{globalTemplate?}` přidá šablonu trasy na všechny stránky v aplikaci:</span><span class="sxs-lookup"><span data-stu-id="0e2b2-502">The sample app adds a `{globalTemplate?}` route template to all of the pages in the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

<span data-ttu-id="0e2b2-503">Vlastnost <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> pro <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> je nastavena na `1`.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-503">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `1`.</span></span> <span data-ttu-id="0e2b2-504">Tím zajistíte následující chování při porovnávání tras v ukázkové aplikaci:</span><span class="sxs-lookup"><span data-stu-id="0e2b2-504">This ensures the following route matching behavior in the sample app:</span></span>

* <span data-ttu-id="0e2b2-505">Šablona trasy `TheContactPage/{text?}` pro je přidána později v tématu.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-505">A route template for `TheContactPage/{text?}` is added later in the topic.</span></span> <span data-ttu-id="0e2b2-506">Trasa Stránka kontaktu má `null` výchozí`Order = 0`pořadí ( ), `{globalTemplate?}` takže odpovídá před šablonou trasy.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-506">The Contact Page route has a default order of `null` (`Order = 0`), so it matches before the `{globalTemplate?}` route template.</span></span>
* <span data-ttu-id="0e2b2-507">Šablona `{aboutTemplate?}` trasy je přidána později v tématu.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-507">An `{aboutTemplate?}` route template is added later in the topic.</span></span> <span data-ttu-id="0e2b2-508">Šablona `{aboutTemplate?}` je `Order` dána . `2`</span><span class="sxs-lookup"><span data-stu-id="0e2b2-508">The `{aboutTemplate?}` template is given an `Order` of `2`.</span></span> <span data-ttu-id="0e2b2-509">Je-li požadována `/About/RouteDataValue`stránka O , je z `RouteData.Values["globalTemplate"]` `Order = 1`důvodu nastavení `RouteData.Values["aboutTemplate"]` `Order = 2`vlastnosti načtena `Order` hodnota RouteDataValue do položky ( ) a nikoli ( ).</span><span class="sxs-lookup"><span data-stu-id="0e2b2-509">When the About page is requested at `/About/RouteDataValue`, "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["aboutTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>
* <span data-ttu-id="0e2b2-510">Šablona `{otherPagesTemplate?}` trasy je přidána později v tématu.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-510">An `{otherPagesTemplate?}` route template is added later in the topic.</span></span> <span data-ttu-id="0e2b2-511">Šablona `{otherPagesTemplate?}` je `Order` dána . `2`</span><span class="sxs-lookup"><span data-stu-id="0e2b2-511">The `{otherPagesTemplate?}` template is given an `Order` of `2`.</span></span> <span data-ttu-id="0e2b2-512">Pokud je požadována libovolná stránka ve složce *Pages/OtherPages* s parametrem `/OtherPages/Page1/RouteDataValue`trasy `RouteData.Values["globalTemplate"]` (například), "RouteDataValue" je načtena do (`Order = 1`) a ne `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) z důvodu nastavení vlastnosti. `Order`</span><span class="sxs-lookup"><span data-stu-id="0e2b2-512">When any page in the *Pages/OtherPages* folder is requested with a route parameter (for example, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="0e2b2-513">Pokud je to možné, `Order`nenastavujte , což má za následek `Order = 0`.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-513">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="0e2b2-514">Při výběru správné trasy se můžete spolehnout na směrování.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-514">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="0e2b2-515">Razor Pages možnosti, <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>jako je například přidání , jsou `Startup.ConfigureServices`přidány při MVC je přidán do kolekce služeb v .</span><span class="sxs-lookup"><span data-stu-id="0e2b2-515">Razor Pages options, such as adding <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>, are added when MVC is added to the service collection in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="0e2b2-516">Například najdete v [ukázkové aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/).</span><span class="sxs-lookup"><span data-stu-id="0e2b2-516">For an example, see the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/).</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="0e2b2-517">Vyžádejte si `localhost:5000/About/GlobalRouteValue` stránku O vzorku a zkontrolujte výsledek:</span><span class="sxs-lookup"><span data-stu-id="0e2b2-517">Request the sample's About page at `localhost:5000/About/GlobalRouteValue` and inspect the result:</span></span>

![Stránka Informace je požadována s segmentem trasy GlobalRouteValue.](razor-pages-conventions/_static/about-page-global-template.png)

### <a name="add-an-app-model-convention-to-all-pages"></a><span data-ttu-id="0e2b2-520">Přidání konvence modelu aplikace na všechny stránky</span><span class="sxs-lookup"><span data-stu-id="0e2b2-520">Add an app model convention to all pages</span></span>

<span data-ttu-id="0e2b2-521">Slouží <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> k vytvoření <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> a přidání <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> a do kolekce instancí, které se použijí během konstrukce modelu aplikace stránky.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-521">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page app model construction.</span></span>

<span data-ttu-id="0e2b2-522">Chcete-li demonstrovat toto a další konvence dále `AddHeaderAttribute` v tématu, ukázková aplikace obsahuje třídu.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-522">To demonstrate this and other conventions later in the topic, the sample app includes an `AddHeaderAttribute` class.</span></span> <span data-ttu-id="0e2b2-523">Konstruktor třídy přijímá `name` řetězec `values` a pole řetězce.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-523">The class constructor accepts a `name` string and a `values` string array.</span></span> <span data-ttu-id="0e2b2-524">Tyto hodnoty se `OnResultExecuting` používají v jeho metodě nastavit hlavičku odpovědi.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-524">These values are used in its `OnResultExecuting` method to set a response header.</span></span> <span data-ttu-id="0e2b2-525">Celá třída je zobrazena v části [Akce modelu stránky](#page-model-action-conventions) dále v tématu.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-525">The full class is shown in the [Page model action conventions](#page-model-action-conventions) section later in the topic.</span></span>

<span data-ttu-id="0e2b2-526">Ukázková aplikace `AddHeaderAttribute` používá třídu k `GlobalHeader`přidání záhlaví , na všechny stránky v aplikaci:</span><span class="sxs-lookup"><span data-stu-id="0e2b2-526">The sample app uses the `AddHeaderAttribute` class to add a header, `GlobalHeader`, to all of the pages in the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

<span data-ttu-id="0e2b2-527">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="0e2b2-527">*Startup.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

<span data-ttu-id="0e2b2-528">Požádejte o stránku `localhost:5000/About` Informace v ukázce a zkontrolujte záhlaví, abyste zobrazili výsledek:</span><span class="sxs-lookup"><span data-stu-id="0e2b2-528">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![Záhlaví odpovědí na stránce O stránce ukazují, že byla přidána globalheader.](razor-pages-conventions/_static/about-page-global-header.png)

### <a name="add-a-handler-model-convention-to-all-pages"></a><span data-ttu-id="0e2b2-530">Přidání konvence modelu obslužné rutiny na všechny stránky</span><span class="sxs-lookup"><span data-stu-id="0e2b2-530">Add a handler model convention to all pages</span></span>

<span data-ttu-id="0e2b2-531">Slouží <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> k vytvoření <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> a přidání <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> a do kolekce instancí, které jsou použity během konstrukce modelu obslužné rutiny stránky.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-531">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page handler model construction.</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalPageHandlerModelConvention.cs?name=snippet1)]

<span data-ttu-id="0e2b2-532">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="0e2b2-532">*Startup.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet10)]

## <a name="page-route-action-conventions"></a><span data-ttu-id="0e2b2-533">Konvence akcí trasy stránky</span><span class="sxs-lookup"><span data-stu-id="0e2b2-533">Page route action conventions</span></span>

<span data-ttu-id="0e2b2-534">Výchozí zprostředkovatel modelu trasy, <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> který je odvozen od vyvolá ní konvence, které jsou navrženy tak, aby poskytovaly body rozšiřitelnosti pro konfiguraci tras stránek.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-534">The default route model provider that derives from <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> invokes conventions which are designed to provide extensibility points for configuring page routes.</span></span>

### <a name="folder-route-model-convention"></a><span data-ttu-id="0e2b2-535">Konvence modelu postupu složky</span><span class="sxs-lookup"><span data-stu-id="0e2b2-535">Folder route model convention</span></span>

<span data-ttu-id="0e2b2-536">Slouží <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> k vytvoření <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> a přidání, který <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> vyvolá akci pro všechny stránky pod zadanou složkou.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-536">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> for all of the pages under the specified folder.</span></span>

<span data-ttu-id="0e2b2-537">Ukázková aplikace <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> používá `{otherPagesTemplate?}` k přidání šablony trasy na stránky ve složce *OtherPages:*</span><span class="sxs-lookup"><span data-stu-id="0e2b2-537">The sample app uses <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> to add an `{otherPagesTemplate?}` route template to the pages in the *OtherPages* folder:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

<span data-ttu-id="0e2b2-538">Vlastnost <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> pro <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> je nastavena na `2`.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-538">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `2`.</span></span> <span data-ttu-id="0e2b2-539">Tím je zajištěno, `{globalTemplate?}` že šablona pro `1`(nastavená dříve v tématu na ) má prioritu pro první pozici hodnoty dat trasy, pokud je poskytnuta jedna hodnota trasy.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-539">This ensures that the template for `{globalTemplate?}` (set earlier in the topic to `1`) is given priority for the first route data value position when a single route value is provided.</span></span> <span data-ttu-id="0e2b2-540">Pokud je požadována stránka ve složce *Pages/OtherPages* s `/OtherPages/Page1/RouteDataValue`hodnotou parametru trasy (například), "RouteDataValue" je načtena `RouteData.Values["globalTemplate"]` do (`Order = 1`) a `RouteData.Values["otherPagesTemplate"]` ne (`Order = 2`) z důvodu nastavení vlastnosti. `Order`</span><span class="sxs-lookup"><span data-stu-id="0e2b2-540">If a page in the *Pages/OtherPages* folder is requested with a route parameter value (for example, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="0e2b2-541">Pokud je to možné, `Order`nenastavujte , což má za následek `Order = 0`.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-541">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="0e2b2-542">Při výběru správné trasy se můžete spolehnout na směrování.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-542">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="0e2b2-543">Vyžádejte si stránku Stránky 1 ukázky a `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` zkontrolujte výsledek:</span><span class="sxs-lookup"><span data-stu-id="0e2b2-543">Request the sample's Page1 page at `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` and inspect the result:</span></span>

![Stránka1 ve složce OtherPages je požadována s segmentem trasy GlobalRouteValue a OtherPagesRouteValue.](razor-pages-conventions/_static/otherpages-page1-global-and-otherpages-templates.png)

### <a name="page-route-model-convention"></a><span data-ttu-id="0e2b2-546">Konvence modelu trasy stránky</span><span class="sxs-lookup"><span data-stu-id="0e2b2-546">Page route model convention</span></span>

<span data-ttu-id="0e2b2-547">Slouží <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> k vytvoření <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> a přidání, která <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> vyvolá akci na stránce se zadaným názvem.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-547">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> for the page with the specified name.</span></span>

<span data-ttu-id="0e2b2-548">Ukázková aplikace `AddPageRouteModelConvention` používá `{aboutTemplate?}` k přidání šablony trasy na stránku Informace:</span><span class="sxs-lookup"><span data-stu-id="0e2b2-548">The sample app uses `AddPageRouteModelConvention` to add an `{aboutTemplate?}` route template to the About page:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

<span data-ttu-id="0e2b2-549">Vlastnost <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> pro <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> je nastavena na `2`.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-549">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `2`.</span></span> <span data-ttu-id="0e2b2-550">Tím je zajištěno, `{globalTemplate?}` že šablona pro `1`(nastavená dříve v tématu na ) má prioritu pro první pozici hodnoty dat trasy, pokud je poskytnuta jedna hodnota trasy.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-550">This ensures that the template for `{globalTemplate?}` (set earlier in the topic to `1`) is given priority for the first route data value position when a single route value is provided.</span></span> <span data-ttu-id="0e2b2-551">Pokud je požadována stránka O s `/About/RouteDataValue`hodnotou parametru trasy `RouteData.Values["globalTemplate"]` na`Order = 1`, `RouteData.Values["aboutTemplate"]` "RouteDataValue" je načten do ( ) a ne (`Order = 2`) z důvodu nastavení vlastnosti. `Order`</span><span class="sxs-lookup"><span data-stu-id="0e2b2-551">If the About page is requested with a route parameter value at `/About/RouteDataValue`, "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["aboutTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="0e2b2-552">Pokud je to možné, `Order`nenastavujte , což má za následek `Order = 0`.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-552">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="0e2b2-553">Při výběru správné trasy se můžete spolehnout na směrování.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-553">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="0e2b2-554">Vyžádejte si `localhost:5000/About/GlobalRouteValue/AboutRouteValue` stránku O vzorku a zkontrolujte výsledek:</span><span class="sxs-lookup"><span data-stu-id="0e2b2-554">Request the sample's About page at `localhost:5000/About/GlobalRouteValue/AboutRouteValue` and inspect the result:</span></span>

![O stránce je požadováno s segmenty postupu pro GlobalRouteValue a AboutRouteValue.](razor-pages-conventions/_static/about-page-global-and-about-templates.png)

## <a name="configure-a-page-route"></a><span data-ttu-id="0e2b2-557">Konfigurace trasy stránky</span><span class="sxs-lookup"><span data-stu-id="0e2b2-557">Configure a page route</span></span>

<span data-ttu-id="0e2b2-558">Slouží <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> ke konfiguraci trasy ke stránce na zadané cestě stránky.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-558">Use <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> to configure a route to a page at the specified page path.</span></span> <span data-ttu-id="0e2b2-559">Generované odkazy na stránku používají zadanou trasu.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-559">Generated links to the page use your specified route.</span></span> <span data-ttu-id="0e2b2-560">`AddPageRoute`používá `AddPageRouteModelConvention` k určení trasy.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-560">`AddPageRoute` uses `AddPageRouteModelConvention` to establish the route.</span></span>

<span data-ttu-id="0e2b2-561">Ukázková aplikace vytvoří `/TheContactPage` trasu pro *Contact.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="0e2b2-561">The sample app creates a route to `/TheContactPage` for *Contact.cshtml*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet5)]

<span data-ttu-id="0e2b2-562">Na stránku Kontakt lze `/Contact` také dosáhnout prostřednictvím výchozí trasy.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-562">The Contact page can also be reached at `/Contact` via its default route.</span></span>

<span data-ttu-id="0e2b2-563">Vlastní trasa ukázkové aplikace na stránku `text` Kontakt umožňuje`{text?}`volitelný segment trasy ( ).</span><span class="sxs-lookup"><span data-stu-id="0e2b2-563">The sample app's custom route to the Contact page allows for an optional `text` route segment (`{text?}`).</span></span> <span data-ttu-id="0e2b2-564">Stránka také obsahuje tento volitelný `@page` segment ve své směrnici v `/Contact` případě, že návštěvník přistupuje ke stránce na své trase:</span><span class="sxs-lookup"><span data-stu-id="0e2b2-564">The page also includes this optional segment in its `@page` directive in case the visitor accesses the page at its `/Contact` route:</span></span>

[!code-cshtml[](razor-pages-conventions/samples/2.x/SampleApp/Pages/Contact.cshtml?highlight=1)]

<span data-ttu-id="0e2b2-565">Všimněte si, že adresa URL vygenerovaná pro odkaz **Kontakt** na vykreslené stránce odráží aktualizovanou trasu:</span><span class="sxs-lookup"><span data-stu-id="0e2b2-565">Note that the URL generated for the **Contact** link in the rendered page reflects the updated route:</span></span>

![Ukázkový odkaz Kontakt aplikace na navigačním panelu](razor-pages-conventions/_static/contact-link.png)

![Kontrola odkazu Kontakt v vykresleném KÓDU HTML znamená, že href je nastaven na '/TheContactPage'](razor-pages-conventions/_static/contact-link-source.png)

<span data-ttu-id="0e2b2-568">Navštivte stránku Kontakt na běžné `/Contact`trase , `/TheContactPage`nebo na vlastní trase .</span><span class="sxs-lookup"><span data-stu-id="0e2b2-568">Visit the Contact page at either its ordinary route, `/Contact`, or the custom route, `/TheContactPage`.</span></span> <span data-ttu-id="0e2b2-569">Pokud zadáte další `text` segment trasy, stránka zobrazí segment kódovaný HTML, který zadáte:</span><span class="sxs-lookup"><span data-stu-id="0e2b2-569">If you supply an additional `text` route segment, the page shows the HTML-encoded segment that you provide:</span></span>

![Okraj ový příklad poskytnutí volitelného segmentu trasy "text" textvalue v adrese URL.](razor-pages-conventions/_static/route-segment-with-custom-route.png)

## <a name="page-model-action-conventions"></a><span data-ttu-id="0e2b2-572">Konvence akcí modelu stránky</span><span class="sxs-lookup"><span data-stu-id="0e2b2-572">Page model action conventions</span></span>

<span data-ttu-id="0e2b2-573">Výchozí zprostředkovatel modelu stránky, <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> který implementuje vyvolá konvence, které jsou navrženy tak, aby poskytovaly body rozšiřitelnosti pro konfiguraci modelů stránek.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-573">The default page model provider that implements <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> invokes conventions which are designed to provide extensibility points for configuring page models.</span></span> <span data-ttu-id="0e2b2-574">Tyto konvence jsou užitečné při vytváření a úpravách scénáře zjišťování a zpracování stránky.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-574">These conventions are useful when building and modifying page discovery and processing scenarios.</span></span>

<span data-ttu-id="0e2b2-575">Příklady v této části ukázkové aplikace `AddHeaderAttribute` používá třídu, což je <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>, která používá hlavičku odpovědi:</span><span class="sxs-lookup"><span data-stu-id="0e2b2-575">For the examples in this section, the sample app uses an `AddHeaderAttribute` class, which is a <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>, that applies a response header:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

<span data-ttu-id="0e2b2-576">Pomocí konvencí ukázka ukazuje, jak použít atribut pro všechny stránky ve složce a na jednu stránku.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-576">Using conventions, the sample demonstrates how to apply the attribute to all of the pages in a folder and to a single page.</span></span>

<span data-ttu-id="0e2b2-577">**Konvence modelu aplikace složky**</span><span class="sxs-lookup"><span data-stu-id="0e2b2-577">**Folder app model convention**</span></span>

<span data-ttu-id="0e2b2-578">Slouží <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> k vytvoření <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> a přidání akce, <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> která vyvolá akci na instancích pro všechny stránky v zadané složce.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-578">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> that invokes an action on <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> instances for all pages under the specified folder.</span></span>

<span data-ttu-id="0e2b2-579">Ukázka ukazuje použití `AddFolderApplicationModelConvention` přidáním záhlaví `OtherPagesHeader`, na stránky uvnitř složky *OtherPages* aplikace:</span><span class="sxs-lookup"><span data-stu-id="0e2b2-579">The sample demonstrates the use of `AddFolderApplicationModelConvention` by adding a header, `OtherPagesHeader`, to the pages inside the *OtherPages* folder of the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet6)]

<span data-ttu-id="0e2b2-580">Požádejte ukázkovou stránku `localhost:5000/OtherPages/Page1` Page1 a zkontrolujte záhlaví, abyste zobrazili výsledek:</span><span class="sxs-lookup"><span data-stu-id="0e2b2-580">Request the sample's Page1 page at `localhost:5000/OtherPages/Page1` and inspect the headers to view the result:</span></span>

![Záhlaví odpovědí na stránce OtherPages/Page1 ukazují, že byla přidána hlavička OtherPagesHeader.](razor-pages-conventions/_static/page1-otherpages-header.png)

<span data-ttu-id="0e2b2-582">**Konvence modelu aplikace stránky**</span><span class="sxs-lookup"><span data-stu-id="0e2b2-582">**Page app model convention**</span></span>

<span data-ttu-id="0e2b2-583">Slouží <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> k vytvoření <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> a přidání, která <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> vyvolá akci na stránce se zadaným názvem.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-583">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> for the page with the specified name.</span></span>

<span data-ttu-id="0e2b2-584">Ukázka ukazuje použití `AddPageApplicationModelConvention` přidáním záhlaví `AboutHeader`, na stránku O:</span><span class="sxs-lookup"><span data-stu-id="0e2b2-584">The sample demonstrates the use of `AddPageApplicationModelConvention` by adding a header, `AboutHeader`, to the About page:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet7)]

<span data-ttu-id="0e2b2-585">Požádejte o stránku `localhost:5000/About` Informace v ukázce a zkontrolujte záhlaví, abyste zobrazili výsledek:</span><span class="sxs-lookup"><span data-stu-id="0e2b2-585">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![Záhlaví odpovědí na stránce O stránce ukazují, že byla přidána hlavička AboutHeader.](razor-pages-conventions/_static/about-page-about-header.png)

<span data-ttu-id="0e2b2-587">**Konfigurace filtru**</span><span class="sxs-lookup"><span data-stu-id="0e2b2-587">**Configure a filter**</span></span>

<span data-ttu-id="0e2b2-588"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*>nakonfiguruje použití zadaného filtru.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-588"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configures the specified filter to apply.</span></span> <span data-ttu-id="0e2b2-589">Můžete implementovat třídu filtru, ale ukázková aplikace ukazuje, jak implementovat filtr ve výrazu lambda, který je implementován na pozadí jako továrna, která vrací filtr:</span><span class="sxs-lookup"><span data-stu-id="0e2b2-589">You can implement a filter class, but the sample app shows how to implement a filter in a lambda expression, which is implemented behind-the-scenes as a factory that returns a filter:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet8)]

<span data-ttu-id="0e2b2-590">Model aplikace stránky se používá ke kontrole relativní cesty pro segmenty, které vedou na stránku Stránka2 ve složce *OtherPages.*</span><span class="sxs-lookup"><span data-stu-id="0e2b2-590">The page app model is used to check the relative path for segments that lead to the Page2 page in the *OtherPages* folder.</span></span> <span data-ttu-id="0e2b2-591">Pokud podmínka projde, je přidána hlavička.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-591">If the condition passes, a header is added.</span></span> <span data-ttu-id="0e2b2-592">Pokud ne, `EmptyFilter` použije se.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-592">If not, the `EmptyFilter` is applied.</span></span>

<span data-ttu-id="0e2b2-593">`EmptyFilter`je [filtr akce](xref:mvc/controllers/filters#action-filters).</span><span class="sxs-lookup"><span data-stu-id="0e2b2-593">`EmptyFilter` is an [Action filter](xref:mvc/controllers/filters#action-filters).</span></span> <span data-ttu-id="0e2b2-594">Vzhledem k tomu, že filtry akce jsou ignorovány Razor Pages, `EmptyFilter` nemá žádný účinek, jak bylo zamýšleno, pokud cesta neobsahuje `OtherPages/Page2`.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-594">Since Action filters are ignored by Razor Pages, the `EmptyFilter` has no effect as intended if the path doesn't contain `OtherPages/Page2`.</span></span>

<span data-ttu-id="0e2b2-595">Požádejte stránku Page2 `localhost:5000/OtherPages/Page2` ukázky a zkontrolujte záhlaví, abyste zobrazili výsledek:</span><span class="sxs-lookup"><span data-stu-id="0e2b2-595">Request the sample's Page2 page at `localhost:5000/OtherPages/Page2` and inspect the headers to view the result:</span></span>

![OtherPagesPage2Header je přidán do odpovědi pro Page2.](razor-pages-conventions/_static/page2-filter-header.png)

<span data-ttu-id="0e2b2-597">**Konfigurace výroby filtrů**</span><span class="sxs-lookup"><span data-stu-id="0e2b2-597">**Configure a filter factory**</span></span>

<span data-ttu-id="0e2b2-598"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*>nakonfiguruje zadanou továrnu tak, aby na všechny stránky Razor [použila filtry.](xref:mvc/controllers/filters)</span><span class="sxs-lookup"><span data-stu-id="0e2b2-598"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configures the specified factory to apply [filters](xref:mvc/controllers/filters) to all Razor Pages.</span></span>

<span data-ttu-id="0e2b2-599">Ukázková aplikace poskytuje příklad použití [továrny filtrů](xref:mvc/controllers/filters#ifilterfactory) přidáním záhlaví `FilterFactoryHeader`, se dvěma hodnotami na stránky aplikace:</span><span class="sxs-lookup"><span data-stu-id="0e2b2-599">The sample app provides an example of using a [filter factory](xref:mvc/controllers/filters#ifilterfactory) by adding a header, `FilterFactoryHeader`, with two values to the app's pages:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet9)]

<span data-ttu-id="0e2b2-600">*AddHeaderWithFactory.cs*:</span><span class="sxs-lookup"><span data-stu-id="0e2b2-600">*AddHeaderWithFactory.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Factories/AddHeaderWithFactory.cs?name=snippet1)]

<span data-ttu-id="0e2b2-601">Požádejte o stránku `localhost:5000/About` Informace v ukázce a zkontrolujte záhlaví, abyste zobrazili výsledek:</span><span class="sxs-lookup"><span data-stu-id="0e2b2-601">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![Záhlaví odpovědí na stránce O stránce ukazují, že byly přidány dvě záhlaví FilterFactoryHeader.](razor-pages-conventions/_static/about-page-filter-factory-header.png)

## <a name="mvc-filters-and-the-page-filter-ipagefilter"></a><span data-ttu-id="0e2b2-603">Filtry MVC a filtr Stránky (IPageFilter)</span><span class="sxs-lookup"><span data-stu-id="0e2b2-603">MVC Filters and the Page filter (IPageFilter)</span></span>

<span data-ttu-id="0e2b2-604">[Filtry akce](xref:mvc/controllers/filters#action-filters) MVC jsou razor pages ignorovány, protože Razor Pages používají metody obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-604">MVC [Action filters](xref:mvc/controllers/filters#action-filters) are ignored by Razor Pages, since Razor Pages use handler methods.</span></span> <span data-ttu-id="0e2b2-605">K dispozici jsou další typy filtrů MVC: [Autorizace](xref:mvc/controllers/filters#authorization-filters), [Výjimka](xref:mvc/controllers/filters#exception-filters), [Prostředek](xref:mvc/controllers/filters#resource-filters)a [Výsledek](xref:mvc/controllers/filters#result-filters).</span><span class="sxs-lookup"><span data-stu-id="0e2b2-605">Other types of MVC filters are available for you to use: [Authorization](xref:mvc/controllers/filters#authorization-filters), [Exception](xref:mvc/controllers/filters#exception-filters), [Resource](xref:mvc/controllers/filters#resource-filters), and [Result](xref:mvc/controllers/filters#result-filters).</span></span> <span data-ttu-id="0e2b2-606">Další informace naleznete v tématu [Filtry.](xref:mvc/controllers/filters)</span><span class="sxs-lookup"><span data-stu-id="0e2b2-606">For more information, see the [Filters](xref:mvc/controllers/filters) topic.</span></span>

<span data-ttu-id="0e2b2-607">Filtr Stránka<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>( ) je filtr, který se vztahuje na žiletky stránky.</span><span class="sxs-lookup"><span data-stu-id="0e2b2-607">The Page filter (<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>) is a filter that applies to Razor Pages.</span></span> <span data-ttu-id="0e2b2-608">Další informace naleznete v [tématu Filter methods for Razor Pages](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="0e2b2-608">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="0e2b2-609">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="0e2b2-609">Additional resources</span></span>

* <xref:security/authorization/razor-pages-authorization>
* <xref:mvc/controllers/areas#areas-with-razor-pages>

::: moniker-end
