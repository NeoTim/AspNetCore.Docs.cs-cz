---
title: Razor Pages konvence směrování a aplikace v ASP.NET Core
author: guardrex
description: Seznamte se s tím, jak konvence poskytovatelů modelů směrování a aplikací umožňují řídit směrování, zjišťování a zpracování stránky.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/08/2019
uid: razor-pages/razor-pages-conventions
ms.openlocfilehash: c93f169c422d260f738faba4812861521f383e51
ms.sourcegitcommit: 776367717e990bdd600cb3c9148ffb905d56862d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/09/2019
ms.locfileid: "68914980"
---
# <a name="razor-pages-route-and-app-conventions-in-aspnet-core"></a><span data-ttu-id="6cdd1-103">Razor Pages konvence směrování a aplikace v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6cdd1-103">Razor Pages route and app conventions in ASP.NET Core</span></span>

<span data-ttu-id="6cdd1-104">Podle [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="6cdd1-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="6cdd1-105">Naučte se používat konvence směrování stránky [a poskytovatele modelů aplikací](xref:mvc/controllers/application-model#conventions) k řízení směrování, zjišťování a zpracování stránky v aplikacích Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="6cdd1-105">Learn how to use page [route and app model provider conventions](xref:mvc/controllers/application-model#conventions) to control page routing, discovery, and processing in Razor Pages apps.</span></span>

<span data-ttu-id="6cdd1-106">Pokud potřebujete nakonfigurovat vlastní trasy stránky pro jednotlivé stránky, nakonfigurujte směrování na stránky pomocí [AddPageRoute konvence](#configure-a-page-route) popsané dále v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="6cdd1-106">When you need to configure custom page routes for individual pages, configure routing to pages with the [AddPageRoute convention](#configure-a-page-route) described later in this topic.</span></span>

<span data-ttu-id="6cdd1-107">Chcete-li určit trasu stránky, přidat segmenty směrování nebo přidat parametry do trasy, použijte `@page` direktivu stránky.</span><span class="sxs-lookup"><span data-stu-id="6cdd1-107">To specify a page route, add route segments, or add parameters to a route, use the page's `@page` directive.</span></span> <span data-ttu-id="6cdd1-108">Další informace najdete v tématu [vlastní trasy](xref:razor-pages/index#custom-routes).</span><span class="sxs-lookup"><span data-stu-id="6cdd1-108">For more information, see [Custom routes](xref:razor-pages/index#custom-routes).</span></span>

<span data-ttu-id="6cdd1-109">Existují vyhrazená slova, která nelze použít jako segmenty směrování nebo názvy parametrů.</span><span class="sxs-lookup"><span data-stu-id="6cdd1-109">There are reserved words that can't be used as route segments or parameter names.</span></span> <span data-ttu-id="6cdd1-110">Další informace najdete v tématu [směrování: Názvy](xref:fundamentals/routing#reserved-routing-names)rezervovaných směrování.</span><span class="sxs-lookup"><span data-stu-id="6cdd1-110">For more information, see [Routing: Reserved routing names](xref:fundamentals/routing#reserved-routing-names).</span></span>

<span data-ttu-id="6cdd1-111">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="6cdd1-111">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

| <span data-ttu-id="6cdd1-112">Scénář</span><span class="sxs-lookup"><span data-stu-id="6cdd1-112">Scenario</span></span> | <span data-ttu-id="6cdd1-113">Ukázka znázorňuje...</span><span class="sxs-lookup"><span data-stu-id="6cdd1-113">The sample demonstrates ...</span></span> |
| -------- | --------------------------- |
| [<span data-ttu-id="6cdd1-114">Konvence modelu</span><span class="sxs-lookup"><span data-stu-id="6cdd1-114">Model conventions</span></span>](#model-conventions)<br><br><span data-ttu-id="6cdd1-115">Konvence. Add</span><span class="sxs-lookup"><span data-stu-id="6cdd1-115">Conventions.Add</span></span><ul><li><span data-ttu-id="6cdd1-116">IPageRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="6cdd1-116">IPageRouteModelConvention</span></span></li><li><span data-ttu-id="6cdd1-117">IPageApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="6cdd1-117">IPageApplicationModelConvention</span></span></li><li><span data-ttu-id="6cdd1-118">IPageHandlerModelConvention</span><span class="sxs-lookup"><span data-stu-id="6cdd1-118">IPageHandlerModelConvention</span></span></li></ul> | <span data-ttu-id="6cdd1-119">Přidejte šablonu a hlavičku směrování na stránky aplikace.</span><span class="sxs-lookup"><span data-stu-id="6cdd1-119">Add a route template and header to an app's pages.</span></span> |
| [<span data-ttu-id="6cdd1-120">Konvence akcí při směrování stránky</span><span class="sxs-lookup"><span data-stu-id="6cdd1-120">Page route action conventions</span></span>](#page-route-action-conventions)<ul><li><span data-ttu-id="6cdd1-121">AddFolderRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="6cdd1-121">AddFolderRouteModelConvention</span></span></li><li><span data-ttu-id="6cdd1-122">AddPageRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="6cdd1-122">AddPageRouteModelConvention</span></span></li><li><span data-ttu-id="6cdd1-123">AddPageRoute</span><span class="sxs-lookup"><span data-stu-id="6cdd1-123">AddPageRoute</span></span></li></ul> | <span data-ttu-id="6cdd1-124">Přidejte šablonu směrování do stránek ve složce a na jednu stránku.</span><span class="sxs-lookup"><span data-stu-id="6cdd1-124">Add a route template to pages in a folder and to a single page.</span></span> |
| [<span data-ttu-id="6cdd1-125">Konvence akcí modelu stránky</span><span class="sxs-lookup"><span data-stu-id="6cdd1-125">Page model action conventions</span></span>](#page-model-action-conventions)<ul><li><span data-ttu-id="6cdd1-126">AddFolderApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="6cdd1-126">AddFolderApplicationModelConvention</span></span></li><li><span data-ttu-id="6cdd1-127">AddPageApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="6cdd1-127">AddPageApplicationModelConvention</span></span></li><li><span data-ttu-id="6cdd1-128">ConfigureFilter (filtrovat třídu, lambda výraz nebo objekt pro vytváření filtru)</span><span class="sxs-lookup"><span data-stu-id="6cdd1-128">ConfigureFilter (filter class, lambda expression, or filter factory)</span></span></li></ul> | <span data-ttu-id="6cdd1-129">Umožňuje přidat záhlaví na stránky ve složce, přidat záhlaví na jednu stránku a nakonfigurovat [objekt pro vytváření filtru](xref:mvc/controllers/filters#ifilterfactory) tak, aby přidal hlavičku na stránky aplikace.</span><span class="sxs-lookup"><span data-stu-id="6cdd1-129">Add a header to pages in a folder, add a header to a single page, and configure a [filter factory](xref:mvc/controllers/filters#ifilterfactory) to add a header to an app's pages.</span></span> |

<span data-ttu-id="6cdd1-130">Konvence Razor Pages jsou přidány a konfigurovány <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> pomocí metody rozšíření <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> pro kolekci služeb ve `Startup` třídě.</span><span class="sxs-lookup"><span data-stu-id="6cdd1-130">Razor Pages conventions are added and configured using the <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> extension method to <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> on the service collection in the `Startup` class.</span></span> <span data-ttu-id="6cdd1-131">Následující příklady konvence jsou vysvětleny dále v tomto tématu:</span><span class="sxs-lookup"><span data-stu-id="6cdd1-131">The following convention examples are explained later in this topic:</span></span>

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

## <a name="route-order"></a><span data-ttu-id="6cdd1-132">Pořadí směrování</span><span class="sxs-lookup"><span data-stu-id="6cdd1-132">Route order</span></span>

<span data-ttu-id="6cdd1-133">Trasy určují <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> pro zpracování (odpovídání tras).</span><span class="sxs-lookup"><span data-stu-id="6cdd1-133">Routes specify an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> for processing (route matching).</span></span>

| <span data-ttu-id="6cdd1-134">Pořadí</span><span class="sxs-lookup"><span data-stu-id="6cdd1-134">Order</span></span>            | <span data-ttu-id="6cdd1-135">Chování</span><span class="sxs-lookup"><span data-stu-id="6cdd1-135">Behavior</span></span> |
| :--------------: | -------- |
| <span data-ttu-id="6cdd1-136">-1</span><span class="sxs-lookup"><span data-stu-id="6cdd1-136">-1</span></span>               | <span data-ttu-id="6cdd1-137">Trasa je zpracována před zpracováním jiných tras.</span><span class="sxs-lookup"><span data-stu-id="6cdd1-137">The route is processed before other routes are processed.</span></span> |
| <span data-ttu-id="6cdd1-138">0</span><span class="sxs-lookup"><span data-stu-id="6cdd1-138">0</span></span>                | <span data-ttu-id="6cdd1-139">Pořadí není zadáno (výchozí hodnota).</span><span class="sxs-lookup"><span data-stu-id="6cdd1-139">Order isn't specified (default value).</span></span> <span data-ttu-id="6cdd1-140">Nepřiřazuje `Order` se`Order = null`() výchozí hodnota `Order` trasy na 0 (nula) pro zpracování.</span><span class="sxs-lookup"><span data-stu-id="6cdd1-140">Not assigning `Order` (`Order = null`) defaults the route `Order` to 0 (zero) for processing.</span></span> |
| <span data-ttu-id="6cdd1-141">1, 2, &hellip; n</span><span class="sxs-lookup"><span data-stu-id="6cdd1-141">1, 2, &hellip; n</span></span> | <span data-ttu-id="6cdd1-142">Určuje pořadí zpracování směrování.</span><span class="sxs-lookup"><span data-stu-id="6cdd1-142">Specifies the route processing order.</span></span> |

<span data-ttu-id="6cdd1-143">Zpracování směrování je zřízené podle konvence:</span><span class="sxs-lookup"><span data-stu-id="6cdd1-143">Route processing is established by convention:</span></span>

* <span data-ttu-id="6cdd1-144">Trasy jsou zpracovávány v sekvenčním pořadí (-1, 0, 1, &hellip; 2, n).</span><span class="sxs-lookup"><span data-stu-id="6cdd1-144">Routes are processed in sequential order (-1, 0, 1, 2, &hellip; n).</span></span>
* <span data-ttu-id="6cdd1-145">V případě, že trasy `Order`mají stejný směr, je nejdříve porovnána konkrétní trasa, za kterou následuje méně specifických tras.</span><span class="sxs-lookup"><span data-stu-id="6cdd1-145">When routes have the same `Order`, the most specific route is matched first followed by less specific routes.</span></span>
* <span data-ttu-id="6cdd1-146">Když se trasy se stejným `Order` počtem parametrů shodují s adresou URL požadavku, trasy se zpracovávají v pořadí, v jakém jsou přidány <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>do.</span><span class="sxs-lookup"><span data-stu-id="6cdd1-146">When routes with the same `Order` and the same number of parameters match a request URL, routes are processed in the order that they're added to the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>.</span></span>

<span data-ttu-id="6cdd1-147">Pokud je to možné, vyhněte se v závislosti na zavedeném pořadí zpracování směrování.</span><span class="sxs-lookup"><span data-stu-id="6cdd1-147">If possible, avoid depending on an established route processing order.</span></span> <span data-ttu-id="6cdd1-148">Obecně směrování vybírá správnou trasu s odpovídající adresou URL.</span><span class="sxs-lookup"><span data-stu-id="6cdd1-148">Generally, routing selects the correct route with URL matching.</span></span> <span data-ttu-id="6cdd1-149">Pokud musíte nastavit vlastnosti směrování `Order` na správně směrované požadavky, schéma směrování aplikace je pravděpodobně matoucí pro klienty a křehké na údržbu.</span><span class="sxs-lookup"><span data-stu-id="6cdd1-149">If you must set route `Order` properties to route requests correctly, the app's routing scheme is probably confusing to clients and fragile to maintain.</span></span> <span data-ttu-id="6cdd1-150">Vyhledejte zjednodušené schéma směrování aplikace.</span><span class="sxs-lookup"><span data-stu-id="6cdd1-150">Seek to simplify the app's routing scheme.</span></span> <span data-ttu-id="6cdd1-151">Ukázková aplikace vyžaduje explicitní pořadí zpracování tras, které předvádí několik scénářů směrování pomocí jedné aplikace.</span><span class="sxs-lookup"><span data-stu-id="6cdd1-151">The sample app requires an explicit route processing order to demonstrate several routing scenarios using a single app.</span></span> <span data-ttu-id="6cdd1-152">Měli byste se však pokusit vyhnout postup nastavení trasy `Order` v produkčních aplikacích.</span><span class="sxs-lookup"><span data-stu-id="6cdd1-152">However, you should attempt to avoid the practice of setting route `Order` in production apps.</span></span>

<span data-ttu-id="6cdd1-153">Směrování Razor Pages směrování a řadiče MVC sdílí implementaci.</span><span class="sxs-lookup"><span data-stu-id="6cdd1-153">Razor Pages routing and MVC controller routing share an implementation.</span></span> <span data-ttu-id="6cdd1-154">Informace o pořadí směrování v tématech MVC jsou k dispozici [při směrování na akce kontroleru: Rozobjednávkuje](xref:mvc/controllers/routing#ordering-attribute-routes)trasy atributů.</span><span class="sxs-lookup"><span data-stu-id="6cdd1-154">Information on route order in the MVC topics is available at [Routing to controller actions: Ordering attribute routes](xref:mvc/controllers/routing#ordering-attribute-routes).</span></span>

## <a name="model-conventions"></a><span data-ttu-id="6cdd1-155">Konvence modelu</span><span class="sxs-lookup"><span data-stu-id="6cdd1-155">Model conventions</span></span>

<span data-ttu-id="6cdd1-156">Přidejte delegáta pro <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> přidání [modelových konvencí](xref:mvc/controllers/application-model#conventions) , které se vztahují na Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="6cdd1-156">Add a delegate for <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> to add [model conventions](xref:mvc/controllers/application-model#conventions) that apply to Razor Pages.</span></span>

### <a name="add-a-route-model-convention-to-all-pages"></a><span data-ttu-id="6cdd1-157">Přidat konvenci modelu směrování na všechny stránky</span><span class="sxs-lookup"><span data-stu-id="6cdd1-157">Add a route model convention to all pages</span></span>

<span data-ttu-id="6cdd1-158">Slouží <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> k vytvoření a <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> přidání kolekce <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instancí, které se použijí během vytváření modelu směrování stránky.</span><span class="sxs-lookup"><span data-stu-id="6cdd1-158">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page route model construction.</span></span>

<span data-ttu-id="6cdd1-159">Ukázková aplikace přidá `{globalTemplate?}` šablonu směrování na všechny stránky v aplikaci:</span><span class="sxs-lookup"><span data-stu-id="6cdd1-159">The sample app adds a `{globalTemplate?}` route template to all of the pages in the app:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="6cdd1-160">Vlastnost pro je nastavena na `1`. <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*></span><span class="sxs-lookup"><span data-stu-id="6cdd1-160">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `1`.</span></span> <span data-ttu-id="6cdd1-161">Tím se zajistí následující chování při shodě trasy v ukázkové aplikaci:</span><span class="sxs-lookup"><span data-stu-id="6cdd1-161">This ensures the following route matching behavior in the sample app:</span></span>

* <span data-ttu-id="6cdd1-162">Šablona směrování pro `TheContactPage/{text?}` je přidána později v tématu.</span><span class="sxs-lookup"><span data-stu-id="6cdd1-162">A route template for `TheContactPage/{text?}` is added later in the topic.</span></span> <span data-ttu-id="6cdd1-163">Trasa stránky kontaktu má výchozí pořadí `null` (`Order = 0`), `{globalTemplate?}` takže odpovídá před šablonou směrování.</span><span class="sxs-lookup"><span data-stu-id="6cdd1-163">The Contact Page route has a default order of `null` (`Order = 0`), so it matches before the `{globalTemplate?}` route template.</span></span>
* <span data-ttu-id="6cdd1-164">Šablona `{aboutTemplate?}` směrování se přidá později v tématu.</span><span class="sxs-lookup"><span data-stu-id="6cdd1-164">An `{aboutTemplate?}` route template is added later in the topic.</span></span> <span data-ttu-id="6cdd1-165">`{aboutTemplate?}` Šabloně je přidělena. `Order` `2`</span><span class="sxs-lookup"><span data-stu-id="6cdd1-165">The `{aboutTemplate?}` template is given an `Order` of `2`.</span></span> <span data-ttu-id="6cdd1-166">Když se na `/About/RouteDataValue`stránce o žádosti vyžádá, "RouteDataValue" se načte do`Order = 1` `RouteData.Values["globalTemplate"]` () `Order` a `RouteData.Values["aboutTemplate"]` ne`Order = 2`() kvůli nastavení vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="6cdd1-166">When the About page is requested at `/About/RouteDataValue`, "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["aboutTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>
* <span data-ttu-id="6cdd1-167">Šablona `{otherPagesTemplate?}` směrování se přidá později v tématu.</span><span class="sxs-lookup"><span data-stu-id="6cdd1-167">An `{otherPagesTemplate?}` route template is added later in the topic.</span></span> <span data-ttu-id="6cdd1-168">`{otherPagesTemplate?}` Šabloně je přidělena. `Order` `2`</span><span class="sxs-lookup"><span data-stu-id="6cdd1-168">The `{otherPagesTemplate?}` template is given an `Order` of `2`.</span></span> <span data-ttu-id="6cdd1-169">Pokud je u libovolné stránky ve složce *Pages/OtherPages* požadováno zadáním parametru `/OtherPages/Page1/RouteDataValue`trasy (například), "RouteDataValue" se načte do `RouteData.Values["otherPagesTemplate"]` `RouteData.Values["globalTemplate"]` (`Order = 1`) a ne (`Order = 2`) kvůli nastavení `Order` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="6cdd1-169">When any page in the *Pages/OtherPages* folder is requested with a route parameter (for example, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="6cdd1-170">Pokud je to možné, nenastavte `Order`, které `Order = 0`výsledky mají.</span><span class="sxs-lookup"><span data-stu-id="6cdd1-170">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="6cdd1-171">Pro výběr správné trasy se spoléhá na směrování.</span><span class="sxs-lookup"><span data-stu-id="6cdd1-171">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="6cdd1-172">Možnosti Razor Pages, jako je například <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>přidání, jsou přidány při přidání MVC do kolekce služeb v. `Startup.ConfigureServices`</span><span class="sxs-lookup"><span data-stu-id="6cdd1-172">Razor Pages options, such as adding <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>, are added when MVC is added to the service collection in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="6cdd1-173">Příklad najdete v [ukázkové aplikaci](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/).</span><span class="sxs-lookup"><span data-stu-id="6cdd1-173">For an example, see the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/).</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="6cdd1-174">Požádejte o stránku ukázek o stránce `localhost:5000/About/GlobalRouteValue` a Prohlédněte si výsledek:</span><span class="sxs-lookup"><span data-stu-id="6cdd1-174">Request the sample's About page at `localhost:5000/About/GlobalRouteValue` and inspect the result:</span></span>

![Stránka o aplikaci je požadována s segmentem směrování GlobalRouteValue.](razor-pages-conventions/_static/about-page-global-template.png)

### <a name="add-an-app-model-convention-to-all-pages"></a><span data-ttu-id="6cdd1-177">Přidání konvence modelu aplikace na všechny stránky</span><span class="sxs-lookup"><span data-stu-id="6cdd1-177">Add an app model convention to all pages</span></span>

<span data-ttu-id="6cdd1-178">Použijte <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> k vytvoření a <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> přidání do kolekce <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instancí, které se použijí během vytváření modelu aplikace stránky.</span><span class="sxs-lookup"><span data-stu-id="6cdd1-178">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page app model construction.</span></span>

<span data-ttu-id="6cdd1-179">Chcete-li předvést tuto a další konvenci později v tématu, ukázková `AddHeaderAttribute` aplikace obsahuje třídu.</span><span class="sxs-lookup"><span data-stu-id="6cdd1-179">To demonstrate this and other conventions later in the topic, the sample app includes an `AddHeaderAttribute` class.</span></span> <span data-ttu-id="6cdd1-180">Konstruktor třídy přijímá `name` řetězec `values` a pole řetězců.</span><span class="sxs-lookup"><span data-stu-id="6cdd1-180">The class constructor accepts a `name` string and a `values` string array.</span></span> <span data-ttu-id="6cdd1-181">Tyto hodnoty jsou použity v `OnResultExecuting` metodě pro nastavení hlavičky odpovědi.</span><span class="sxs-lookup"><span data-stu-id="6cdd1-181">These values are used in its `OnResultExecuting` method to set a response header.</span></span> <span data-ttu-id="6cdd1-182">Úplná třída je uvedena v části [konvence akcí modelu stránky](#page-model-action-conventions) dále v tématu.</span><span class="sxs-lookup"><span data-stu-id="6cdd1-182">The full class is shown in the [Page model action conventions](#page-model-action-conventions) section later in the topic.</span></span>

<span data-ttu-id="6cdd1-183">Ukázková aplikace používá `AddHeaderAttribute` třídu k přidání záhlaví, `GlobalHeader`na všechny stránky v aplikaci:</span><span class="sxs-lookup"><span data-stu-id="6cdd1-183">The sample app uses the `AddHeaderAttribute` class to add a header, `GlobalHeader`, to all of the pages in the app:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="6cdd1-184">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="6cdd1-184">*Startup.cs*:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet2)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

::: moniker-end

<span data-ttu-id="6cdd1-185">Požádejte o stránku ukázek o stránce `localhost:5000/About` a Prohlédněte si záhlaví a podívejte se na výsledek:</span><span class="sxs-lookup"><span data-stu-id="6cdd1-185">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![Hlavičky odpovědi stránky o produktu ukazují, že byl přidán GlobalHeader.](razor-pages-conventions/_static/about-page-global-header.png)

### <a name="add-a-handler-model-convention-to-all-pages"></a><span data-ttu-id="6cdd1-187">Přidat konvenci modelu obslužné rutiny na všechny stránky</span><span class="sxs-lookup"><span data-stu-id="6cdd1-187">Add a handler model convention to all pages</span></span>

<span data-ttu-id="6cdd1-188">Použijte <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> k vytvoření a <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> přidání do kolekce <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instancí, které jsou použity během vytváření modelu obslužné rutiny stránky.</span><span class="sxs-lookup"><span data-stu-id="6cdd1-188">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page handler model construction.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalPageHandlerModelConvention.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalPageHandlerModelConvention.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="6cdd1-189">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="6cdd1-189">*Startup.cs*:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet10)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet10)]

::: moniker-end

## <a name="page-route-action-conventions"></a><span data-ttu-id="6cdd1-190">Konvence akcí při směrování stránky</span><span class="sxs-lookup"><span data-stu-id="6cdd1-190">Page route action conventions</span></span>

<span data-ttu-id="6cdd1-191">Výchozí zprostředkovatel modelu směrování, který je odvozen od <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> konvencí vyvolání, které jsou navrženy tak, aby poskytovaly body rozšiřitelnosti pro konfiguraci tras stránky.</span><span class="sxs-lookup"><span data-stu-id="6cdd1-191">The default route model provider that derives from <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> invokes conventions which are designed to provide extensibility points for configuring page routes.</span></span>

### <a name="folder-route-model-convention"></a><span data-ttu-id="6cdd1-192">Konvence modelu směrování složky</span><span class="sxs-lookup"><span data-stu-id="6cdd1-192">Folder route model convention</span></span>

<span data-ttu-id="6cdd1-193">Slouží <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> k vytvoření a <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> Přidání akce, která vyvolá akci <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> pro všechny stránky v zadané složce.</span><span class="sxs-lookup"><span data-stu-id="6cdd1-193">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> for all of the pages under the specified folder.</span></span>

<span data-ttu-id="6cdd1-194">Ukázková aplikace používá <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> k `{otherPagesTemplate?}` Přidání šablony směrování na stránky ve složce *OtherPages* :</span><span class="sxs-lookup"><span data-stu-id="6cdd1-194">The sample app uses <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> to add an `{otherPagesTemplate?}` route template to the pages in the *OtherPages* folder:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet3)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

::: moniker-end

<span data-ttu-id="6cdd1-195">Vlastnost pro je nastavena na `2`. <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*></span><span class="sxs-lookup"><span data-stu-id="6cdd1-195">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `2`.</span></span> <span data-ttu-id="6cdd1-196">Tím se zajistí, že šablona `{globalTemplate?}` pro (nastavená výše v tématu `1`na) má prioritu první pozice hodnoty dat trasy při zadání jedné hodnoty trasy.</span><span class="sxs-lookup"><span data-stu-id="6cdd1-196">This ensures that the template for `{globalTemplate?}` (set earlier in the topic to `1`) is given priority for the first route data value position when a single route value is provided.</span></span> <span data-ttu-id="6cdd1-197">Pokud se stránka ve složce *Pages/OtherPages* `/OtherPages/Page1/RouteDataValue`požaduje s hodnotou parametru trasy (například), "RouteDataValue" se načte do`Order = 1` `RouteData.Values["globalTemplate"]` () a ne `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) kvůli nastavení `Order` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="6cdd1-197">If a page in the *Pages/OtherPages* folder is requested with a route parameter value (for example, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="6cdd1-198">Pokud je to možné, nenastavte `Order`, které `Order = 0`výsledky mají.</span><span class="sxs-lookup"><span data-stu-id="6cdd1-198">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="6cdd1-199">Pro výběr správné trasy se spoléhá na směrování.</span><span class="sxs-lookup"><span data-stu-id="6cdd1-199">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="6cdd1-200">Vyžádejte si Page1 stránku `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` ukázky a Prohlédněte si výsledek:</span><span class="sxs-lookup"><span data-stu-id="6cdd1-200">Request the sample's Page1 page at `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` and inspect the result:</span></span>

![Page1 ve složce OtherPages se vyžádá s segmentem směrování GlobalRouteValue a OtherPagesRouteValue.](razor-pages-conventions/_static/otherpages-page1-global-and-otherpages-templates.png)

### <a name="page-route-model-convention"></a><span data-ttu-id="6cdd1-203">Konvence modelu směrování stránky</span><span class="sxs-lookup"><span data-stu-id="6cdd1-203">Page route model convention</span></span>

<span data-ttu-id="6cdd1-204">Slouží <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> k vytvoření a <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> Přidání akce, která vyvolá akci na <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> stránce pro stránku se zadaným názvem.</span><span class="sxs-lookup"><span data-stu-id="6cdd1-204">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> for the page with the specified name.</span></span>

<span data-ttu-id="6cdd1-205">Ukázková aplikace používá `AddPageRouteModelConvention` k přidání šablony `{aboutTemplate?}` směrování na stránku o aplikaci:</span><span class="sxs-lookup"><span data-stu-id="6cdd1-205">The sample app uses `AddPageRouteModelConvention` to add an `{aboutTemplate?}` route template to the About page:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet4)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

::: moniker-end

<span data-ttu-id="6cdd1-206">Vlastnost pro je nastavena na `2`. <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*></span><span class="sxs-lookup"><span data-stu-id="6cdd1-206">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `2`.</span></span> <span data-ttu-id="6cdd1-207">Tím se zajistí, že šablona `{globalTemplate?}` pro (nastavená výše v tématu `1`na) má prioritu první pozice hodnoty dat trasy při zadání jedné hodnoty trasy.</span><span class="sxs-lookup"><span data-stu-id="6cdd1-207">This ensures that the template for `{globalTemplate?}` (set earlier in the topic to `1`) is given priority for the first route data value position when a single route value is provided.</span></span> <span data-ttu-id="6cdd1-208">Pokud se stránka o žádosti vyžaduje s hodnotou parametru Route na `/About/RouteDataValue`, je "RouteDataValue" načten do `RouteData.Values["globalTemplate"]` (`Order = 1`) `Order` a nikoli `RouteData.Values["aboutTemplate"]` (`Order = 2`) z důvodu nastavení vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="6cdd1-208">If the About page is requested with a route parameter value at `/About/RouteDataValue`, "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["aboutTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="6cdd1-209">Pokud je to možné, nenastavte `Order`, které `Order = 0`výsledky mají.</span><span class="sxs-lookup"><span data-stu-id="6cdd1-209">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="6cdd1-210">Pro výběr správné trasy se spoléhá na směrování.</span><span class="sxs-lookup"><span data-stu-id="6cdd1-210">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="6cdd1-211">Požádejte o stránku ukázek o stránce `localhost:5000/About/GlobalRouteValue/AboutRouteValue` a Prohlédněte si výsledek:</span><span class="sxs-lookup"><span data-stu-id="6cdd1-211">Request the sample's About page at `localhost:5000/About/GlobalRouteValue/AboutRouteValue` and inspect the result:</span></span>

![U segmentů směrování pro GlobalRouteValue a AboutRouteValue se žádá o stránku.](razor-pages-conventions/_static/about-page-global-and-about-templates.png)

::: moniker range=">= aspnetcore-2.2"

## <a name="use-a-parameter-transformer-to-customize-page-routes"></a><span data-ttu-id="6cdd1-214">Použití transformátoru parametrů k přizpůsobení cest stránky</span><span class="sxs-lookup"><span data-stu-id="6cdd1-214">Use a parameter transformer to customize page routes</span></span>

<span data-ttu-id="6cdd1-215">Trasy stránky generované ASP.NET Core lze přizpůsobit pomocí transformátoru parametrů.</span><span class="sxs-lookup"><span data-stu-id="6cdd1-215">Page routes generated by ASP.NET Core can be customized using a parameter transformer.</span></span> <span data-ttu-id="6cdd1-216">Parametr Transformer implementuje `IOutboundParameterTransformer` a transformuje hodnotu parametrů.</span><span class="sxs-lookup"><span data-stu-id="6cdd1-216">A parameter transformer implements `IOutboundParameterTransformer` and transforms the value of parameters.</span></span> <span data-ttu-id="6cdd1-217">Například vlastní `SlugifyParameterTransformer` parametr Transformer `SubscriptionManagement` změní hodnotu Route na `subscription-management`.</span><span class="sxs-lookup"><span data-stu-id="6cdd1-217">For example, a custom `SlugifyParameterTransformer` parameter transformer changes the `SubscriptionManagement` route value to `subscription-management`.</span></span>

<span data-ttu-id="6cdd1-218">Konvence modelu směrování stránky aplikuje transformátor parametrů na složku a název souboru segmentů automaticky generovaných tras stránky v aplikaci. `PageRouteTransformerConvention`</span><span class="sxs-lookup"><span data-stu-id="6cdd1-218">The `PageRouteTransformerConvention` page route model convention applies a parameter transformer to the folder and file name segments of automatically generated page routes in an app.</span></span> <span data-ttu-id="6cdd1-219">Například soubor Razor Pages v */Pages/SubscriptionManagement/ViewAll.cshtml* by měl přepsané směrování z `/SubscriptionManagement/ViewAll` do. `/subscription-management/view-all`</span><span class="sxs-lookup"><span data-stu-id="6cdd1-219">For example, the Razor Pages file at */Pages/SubscriptionManagement/ViewAll.cshtml* would have its route rewritten from `/SubscriptionManagement/ViewAll` to `/subscription-management/view-all`.</span></span>

<span data-ttu-id="6cdd1-220">`PageRouteTransformerConvention`transformuje automaticky generované segmenty stránky trasy, které pocházejí z Razor Pages složky a názvu souboru.</span><span class="sxs-lookup"><span data-stu-id="6cdd1-220">`PageRouteTransformerConvention` only transforms the automatically generated segments of a page route that come from the Razor Pages folder and file name.</span></span> <span data-ttu-id="6cdd1-221">Netransformuje segmenty směrování přidané s `@page` direktivou.</span><span class="sxs-lookup"><span data-stu-id="6cdd1-221">It doesn't transform route segments added with the `@page` directive.</span></span> <span data-ttu-id="6cdd1-222">Konvence také netransformuje trasy, <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>které přidal.</span><span class="sxs-lookup"><span data-stu-id="6cdd1-222">The convention also doesn't transform routes added by <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>.</span></span>

<span data-ttu-id="6cdd1-223">Je zaregistrován jako možnost v `Startup.ConfigureServices`: `PageRouteTransformerConvention`</span><span class="sxs-lookup"><span data-stu-id="6cdd1-223">The `PageRouteTransformerConvention` is registered as an option in `Startup.ConfigureServices`:</span></span>

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

## <a name="configure-a-page-route"></a><span data-ttu-id="6cdd1-224">Konfigurace trasy stránky</span><span class="sxs-lookup"><span data-stu-id="6cdd1-224">Configure a page route</span></span>

<span data-ttu-id="6cdd1-225">Slouží <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> ke konfiguraci směrování na stránku na zadané cestě stránky.</span><span class="sxs-lookup"><span data-stu-id="6cdd1-225">Use <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> to configure a route to a page at the specified page path.</span></span> <span data-ttu-id="6cdd1-226">Vygenerované odkazy na stránku používají zadanou trasu.</span><span class="sxs-lookup"><span data-stu-id="6cdd1-226">Generated links to the page use your specified route.</span></span> <span data-ttu-id="6cdd1-227">`AddPageRoute`slouží `AddPageRouteModelConvention` k vytvoření trasy.</span><span class="sxs-lookup"><span data-stu-id="6cdd1-227">`AddPageRoute` uses `AddPageRouteModelConvention` to establish the route.</span></span>

<span data-ttu-id="6cdd1-228">Ukázková aplikace vytvoří trasu `/TheContactPage` pro *kontakt. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="6cdd1-228">The sample app creates a route to `/TheContactPage` for *Contact.cshtml*:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet5)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet5)]

::: moniker-end

<span data-ttu-id="6cdd1-229">Stránku kontaktů lze také kontaktovat `/Contact` prostřednictvím výchozí trasy.</span><span class="sxs-lookup"><span data-stu-id="6cdd1-229">The Contact page can also be reached at `/Contact` via its default route.</span></span>

<span data-ttu-id="6cdd1-230">Vlastní trasa ukázkové aplikace na stránku kontaktů umožňuje volitelný `text` segment směrování (`{text?}`).</span><span class="sxs-lookup"><span data-stu-id="6cdd1-230">The sample app's custom route to the Contact page allows for an optional `text` route segment (`{text?}`).</span></span> <span data-ttu-id="6cdd1-231">Tato stránka obsahuje také volitelný segment v rámci své `@page` směrnice pro případ, že návštěvník přistupuje ke stránce `/Contact` v cestě:</span><span class="sxs-lookup"><span data-stu-id="6cdd1-231">The page also includes this optional segment in its `@page` directive in case the visitor accesses the page at its `/Contact` route:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-cshtml[](razor-pages-conventions/samples/3.x/SampleApp/Pages/Contact.cshtml?highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-cshtml[](razor-pages-conventions/samples/2.x/SampleApp/Pages/Contact.cshtml?highlight=1)]

::: moniker-end

<span data-ttu-id="6cdd1-232">Všimněte si, že adresa URL vygenerovaná pro odkaz **kontaktu** na vykreslené stránce odráží aktualizovanou trasu:</span><span class="sxs-lookup"><span data-stu-id="6cdd1-232">Note that the URL generated for the **Contact** link in the rendered page reflects the updated route:</span></span>

![Odkaz na ukázkový kontakt aplikace na navigačním panelu](razor-pages-conventions/_static/contact-link.png)

![Kontrola odkazu na kontakt ve vykresleném kódu HTML znamená, že odkaz href je nastaven na hodnotu "/TheContactPage".](razor-pages-conventions/_static/contact-link-source.png)

<span data-ttu-id="6cdd1-235">Navštivte stránku kontaktů buď na své běžné trase, `/Contact`nebo na vlastní `/TheContactPage`trasu.</span><span class="sxs-lookup"><span data-stu-id="6cdd1-235">Visit the Contact page at either its ordinary route, `/Contact`, or the custom route, `/TheContactPage`.</span></span> <span data-ttu-id="6cdd1-236">Pokud zadáte další `text` segment směrování, stránka zobrazuje segment, který zakódovaný ve formátu HTML, který zadáte:</span><span class="sxs-lookup"><span data-stu-id="6cdd1-236">If you supply an additional `text` route segment, the page shows the HTML-encoded segment that you provide:</span></span>

![Prohlížeč Edge – příklad poskytnutí volitelného segmentu "text" trasy "TextValue" v adrese URL](razor-pages-conventions/_static/route-segment-with-custom-route.png)

## <a name="page-model-action-conventions"></a><span data-ttu-id="6cdd1-239">Konvence akcí modelu stránky</span><span class="sxs-lookup"><span data-stu-id="6cdd1-239">Page model action conventions</span></span>

<span data-ttu-id="6cdd1-240">Výchozí zprostředkovatel stránky, který implementuje implementují <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> konvence, které jsou navrženy tak, aby poskytovaly body rozšiřitelnosti pro konfiguraci modelů stránek.</span><span class="sxs-lookup"><span data-stu-id="6cdd1-240">The default page model provider that implements <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> invokes conventions which are designed to provide extensibility points for configuring page models.</span></span> <span data-ttu-id="6cdd1-241">Tyto konvence jsou užitečné při sestavování a úpravách scénářů zjišťování a zpracování stránky.</span><span class="sxs-lookup"><span data-stu-id="6cdd1-241">These conventions are useful when building and modifying page discovery and processing scenarios.</span></span>

<span data-ttu-id="6cdd1-242">V příkladech v této části používá `AddHeaderAttribute` ukázková aplikace třídu, která <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>je, která používá hlavičku odpovědi:</span><span class="sxs-lookup"><span data-stu-id="6cdd1-242">For the examples in this section, the sample app uses an `AddHeaderAttribute` class, which is a <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>, that applies a response header:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="6cdd1-243">Pomocí konvence Ukázka ukazuje, jak použít atribut na všechny stránky ve složce a na jednu stránku.</span><span class="sxs-lookup"><span data-stu-id="6cdd1-243">Using conventions, the sample demonstrates how to apply the attribute to all of the pages in a folder and to a single page.</span></span>

<span data-ttu-id="6cdd1-244">**Konvence modelu aplikace složky**</span><span class="sxs-lookup"><span data-stu-id="6cdd1-244">**Folder app model convention**</span></span>

<span data-ttu-id="6cdd1-245">Slouží <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> k vytvoření a <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> Přidání akce, která vyvolá akci u <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> instancí pro všechny stránky v zadané složce.</span><span class="sxs-lookup"><span data-stu-id="6cdd1-245">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> that invokes an action on <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> instances for all pages under the specified folder.</span></span>

<span data-ttu-id="6cdd1-246">Ukázka demonstruje použití `AddFolderApplicationModelConvention` přidáním záhlaví, `OtherPagesHeader`na stránkách ve složce *OtherPages* této aplikace:</span><span class="sxs-lookup"><span data-stu-id="6cdd1-246">The sample demonstrates the use of `AddFolderApplicationModelConvention` by adding a header, `OtherPagesHeader`, to the pages inside the *OtherPages* folder of the app:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet6)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet6)]

::: moniker-end

<span data-ttu-id="6cdd1-247">Vyžádejte si ukázkovou stránku Page1 `localhost:5000/OtherPages/Page1` na stránce a Prohlédněte si záhlaví a podívejte se na výsledek:</span><span class="sxs-lookup"><span data-stu-id="6cdd1-247">Request the sample's Page1 page at `localhost:5000/OtherPages/Page1` and inspect the headers to view the result:</span></span>

![Hlavičky odpovědi stránky OtherPages/Page1 ukazují, že byla přidána OtherPagesHeader.](razor-pages-conventions/_static/page1-otherpages-header.png)

<span data-ttu-id="6cdd1-249">**Konvence modelu aplikace stránky**</span><span class="sxs-lookup"><span data-stu-id="6cdd1-249">**Page app model convention**</span></span>

<span data-ttu-id="6cdd1-250">Slouží <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> k vytvoření a <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> Přidání akce, která vyvolá akci na <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> stránce pro stránku se zadaným názvem.</span><span class="sxs-lookup"><span data-stu-id="6cdd1-250">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> for the page with the specified name.</span></span>

<span data-ttu-id="6cdd1-251">Ukázka demonstruje použití `AddPageApplicationModelConvention` přidáním záhlaví, `AboutHeader`na stránce o produktu:</span><span class="sxs-lookup"><span data-stu-id="6cdd1-251">The sample demonstrates the use of `AddPageApplicationModelConvention` by adding a header, `AboutHeader`, to the About page:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet7)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet7)]

::: moniker-end

<span data-ttu-id="6cdd1-252">Požádejte o stránku ukázek o stránce `localhost:5000/About` a Prohlédněte si záhlaví a podívejte se na výsledek:</span><span class="sxs-lookup"><span data-stu-id="6cdd1-252">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![Hlavičky odpovědi stránky o produktu ukazují, že byl přidán AboutHeader.](razor-pages-conventions/_static/about-page-about-header.png)

<span data-ttu-id="6cdd1-254">**Konfigurace filtru**</span><span class="sxs-lookup"><span data-stu-id="6cdd1-254">**Configure a filter**</span></span>

<span data-ttu-id="6cdd1-255"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*>nakonfiguruje zadaný filtr na použití.</span><span class="sxs-lookup"><span data-stu-id="6cdd1-255"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configures the specified filter to apply.</span></span> <span data-ttu-id="6cdd1-256">Můžete implementovat třídu filtru, ale ukázková aplikace ukazuje, jak implementovat filtr ve výrazu lambda, který je implementován na pozadí jako objekt pro vytváření, který vrací filtr:</span><span class="sxs-lookup"><span data-stu-id="6cdd1-256">You can implement a filter class, but the sample app shows how to implement a filter in a lambda expression, which is implemented behind-the-scenes as a factory that returns a filter:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet8)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet8)]

::: moniker-end

<span data-ttu-id="6cdd1-257">Model webové aplikace se používá ke kontrole relativní cesty pro segmenty, které vedou ke stránce Page2 ve složce *OtherPages* .</span><span class="sxs-lookup"><span data-stu-id="6cdd1-257">The page app model is used to check the relative path for segments that lead to the Page2 page in the *OtherPages* folder.</span></span> <span data-ttu-id="6cdd1-258">Pokud podmínka projde, přidá se hlavička.</span><span class="sxs-lookup"><span data-stu-id="6cdd1-258">If the condition passes, a header is added.</span></span> <span data-ttu-id="6cdd1-259">V `EmptyFilter` takovém případě se použije.</span><span class="sxs-lookup"><span data-stu-id="6cdd1-259">If not, the `EmptyFilter` is applied.</span></span>

<span data-ttu-id="6cdd1-260">`EmptyFilter`je [Filtr akcí](xref:mvc/controllers/filters#action-filters).</span><span class="sxs-lookup"><span data-stu-id="6cdd1-260">`EmptyFilter` is an [Action filter](xref:mvc/controllers/filters#action-filters).</span></span> <span data-ttu-id="6cdd1-261">Vzhledem k tomu, že filtry akcí jsou Razor Pages `EmptyFilter` ignorovány, nemá žádný vliv na zamýšlené, pokud `OtherPages/Page2`cesta neobsahuje.</span><span class="sxs-lookup"><span data-stu-id="6cdd1-261">Since Action filters are ignored by Razor Pages, the `EmptyFilter` has no effect as intended if the path doesn't contain `OtherPages/Page2`.</span></span>

<span data-ttu-id="6cdd1-262">Vyžádejte si ukázkovou stránku Page2 `localhost:5000/OtherPages/Page2` na stránce a Prohlédněte si záhlaví a podívejte se na výsledek:</span><span class="sxs-lookup"><span data-stu-id="6cdd1-262">Request the sample's Page2 page at `localhost:5000/OtherPages/Page2` and inspect the headers to view the result:</span></span>

![OtherPagesPage2Header se přidá do odpovědi pro Page2.](razor-pages-conventions/_static/page2-filter-header.png)

<span data-ttu-id="6cdd1-264">**Konfigurace objektu pro vytváření filtrů**</span><span class="sxs-lookup"><span data-stu-id="6cdd1-264">**Configure a filter factory**</span></span>

<span data-ttu-id="6cdd1-265"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*>nakonfiguruje určený objekt pro vytváření na použití [filtrů](xref:mvc/controllers/filters) na všechny Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="6cdd1-265"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configures the specified factory to apply [filters](xref:mvc/controllers/filters) to all Razor Pages.</span></span>

<span data-ttu-id="6cdd1-266">Ukázková aplikace poskytuje příklad použití [objektu pro vytváření filtru](xref:mvc/controllers/filters#ifilterfactory) přidáním hlavičky, `FilterFactoryHeader`se dvěma hodnotami na stránkách aplikace:</span><span class="sxs-lookup"><span data-stu-id="6cdd1-266">The sample app provides an example of using a [filter factory](xref:mvc/controllers/filters#ifilterfactory) by adding a header, `FilterFactoryHeader`, with two values to the app's pages:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet9)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet9)]

::: moniker-end

<span data-ttu-id="6cdd1-267">*AddHeaderWithFactory.cs*:</span><span class="sxs-lookup"><span data-stu-id="6cdd1-267">*AddHeaderWithFactory.cs*:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Factories/AddHeaderWithFactory.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Factories/AddHeaderWithFactory.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="6cdd1-268">Požádejte o stránku ukázek o stránce `localhost:5000/About` a Prohlédněte si záhlaví a podívejte se na výsledek:</span><span class="sxs-lookup"><span data-stu-id="6cdd1-268">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![Hlavičky odpovědi stránky o stránku zobrazují, že byly přidány dvě hlavičky FilterFactoryHeader.](razor-pages-conventions/_static/about-page-filter-factory-header.png)

## <a name="mvc-filters-and-the-page-filter-ipagefilter"></a><span data-ttu-id="6cdd1-270">Filtry MVC a filtr stránky (IPageFilter)</span><span class="sxs-lookup"><span data-stu-id="6cdd1-270">MVC Filters and the Page filter (IPageFilter)</span></span>

<span data-ttu-id="6cdd1-271">[Filtry akcí](xref:mvc/controllers/filters#action-filters) MVC se Razor Pages ignorují, protože Razor Pages použít obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="6cdd1-271">MVC [Action filters](xref:mvc/controllers/filters#action-filters) are ignored by Razor Pages, since Razor Pages use handler methods.</span></span> <span data-ttu-id="6cdd1-272">Další typy filtrů MVC jsou k dispozici pro použití: [Autorizace](xref:mvc/controllers/filters#authorization-filters), [výjimka](xref:mvc/controllers/filters#exception-filters), [prostředek](xref:mvc/controllers/filters#resource-filters)a [výsledek](xref:mvc/controllers/filters#result-filters).</span><span class="sxs-lookup"><span data-stu-id="6cdd1-272">Other types of MVC filters are available for you to use: [Authorization](xref:mvc/controllers/filters#authorization-filters), [Exception](xref:mvc/controllers/filters#exception-filters), [Resource](xref:mvc/controllers/filters#resource-filters), and [Result](xref:mvc/controllers/filters#result-filters).</span></span> <span data-ttu-id="6cdd1-273">Další informace najdete v tématu [filtry](xref:mvc/controllers/filters) .</span><span class="sxs-lookup"><span data-stu-id="6cdd1-273">For more information, see the [Filters](xref:mvc/controllers/filters) topic.</span></span>

<span data-ttu-id="6cdd1-274">Filtr stránky (<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>) je filtr, který se vztahuje na Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="6cdd1-274">The Page filter (<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>) is a filter that applies to Razor Pages.</span></span> <span data-ttu-id="6cdd1-275">Další informace naleznete v tématu [metody Filter pro Razor Pages](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="6cdd1-275">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6cdd1-276">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="6cdd1-276">Additional resources</span></span>

* <xref:security/authorization/razor-pages-authorization>
* <xref:mvc/controllers/areas#areas-with-razor-pages>
