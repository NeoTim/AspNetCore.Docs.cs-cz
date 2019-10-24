---
title: Razor Pages konvence směrování a aplikace v ASP.NET Core
author: guardrex
description: Seznamte se s tím, jak konvence poskytovatelů modelů směrování a aplikací umožňují řídit směrování, zjišťování a zpracování stránky.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/22/2019
uid: razor-pages/razor-pages-conventions
ms.openlocfilehash: a0a1eda69da34d1865fd11ef464c3697bcd01eff
ms.sourcegitcommit: 810d5831169770ee240d03207d6671dabea2486e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/22/2019
ms.locfileid: "72779220"
---
# <a name="razor-pages-route-and-app-conventions-in-aspnet-core"></a><span data-ttu-id="328dd-103">Razor Pages konvence směrování a aplikace v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="328dd-103">Razor Pages route and app conventions in ASP.NET Core</span></span>

<span data-ttu-id="328dd-104">Od [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="328dd-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="328dd-105">Naučte se používat [konvence směrování stránky a poskytovatele modelů aplikací](xref:mvc/controllers/application-model#conventions) k řízení směrování, zjišťování a zpracování stránky v aplikacích Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="328dd-105">Learn how to use page [route and app model provider conventions](xref:mvc/controllers/application-model#conventions) to control page routing, discovery, and processing in Razor Pages apps.</span></span>

<span data-ttu-id="328dd-106">Pokud potřebujete nakonfigurovat vlastní trasy stránky pro jednotlivé stránky, nakonfigurujte směrování na stránky pomocí [AddPageRoute konvence](#configure-a-page-route) popsané dále v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="328dd-106">When you need to configure custom page routes for individual pages, configure routing to pages with the [AddPageRoute convention](#configure-a-page-route) described later in this topic.</span></span>

<span data-ttu-id="328dd-107">Chcete-li určit trasu stránky, přidat segmenty směrování nebo přidat parametry do trasy, použijte direktivu `@page` stránky.</span><span class="sxs-lookup"><span data-stu-id="328dd-107">To specify a page route, add route segments, or add parameters to a route, use the page's `@page` directive.</span></span> <span data-ttu-id="328dd-108">Další informace najdete v tématu [vlastní trasy](xref:razor-pages/index#custom-routes).</span><span class="sxs-lookup"><span data-stu-id="328dd-108">For more information, see [Custom routes](xref:razor-pages/index#custom-routes).</span></span>

<span data-ttu-id="328dd-109">Existují vyhrazená slova, která nelze použít jako segmenty směrování nebo názvy parametrů.</span><span class="sxs-lookup"><span data-stu-id="328dd-109">There are reserved words that can't be used as route segments or parameter names.</span></span> <span data-ttu-id="328dd-110">Další informace najdete v tématu [směrování: názvy rezervovaných směrování](xref:fundamentals/routing#reserved-routing-names).</span><span class="sxs-lookup"><span data-stu-id="328dd-110">For more information, see [Routing: Reserved routing names](xref:fundamentals/routing#reserved-routing-names).</span></span>

<span data-ttu-id="328dd-111">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="328dd-111">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

| <span data-ttu-id="328dd-112">Scénář</span><span class="sxs-lookup"><span data-stu-id="328dd-112">Scenario</span></span> | <span data-ttu-id="328dd-113">Ukázka znázorňuje...</span><span class="sxs-lookup"><span data-stu-id="328dd-113">The sample demonstrates ...</span></span> |
| -------- | --------------------------- |
| [<span data-ttu-id="328dd-114">Konvence modelu</span><span class="sxs-lookup"><span data-stu-id="328dd-114">Model conventions</span></span>](#model-conventions)<br><br><span data-ttu-id="328dd-115">Konvence. Add</span><span class="sxs-lookup"><span data-stu-id="328dd-115">Conventions.Add</span></span><ul><li><span data-ttu-id="328dd-116">IPageRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="328dd-116">IPageRouteModelConvention</span></span></li><li><span data-ttu-id="328dd-117">IPageApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="328dd-117">IPageApplicationModelConvention</span></span></li><li><span data-ttu-id="328dd-118">IPageHandlerModelConvention</span><span class="sxs-lookup"><span data-stu-id="328dd-118">IPageHandlerModelConvention</span></span></li></ul> | <span data-ttu-id="328dd-119">Přidejte šablonu a hlavičku směrování na stránky aplikace.</span><span class="sxs-lookup"><span data-stu-id="328dd-119">Add a route template and header to an app's pages.</span></span> |
| [<span data-ttu-id="328dd-120">Konvence akcí při směrování stránky</span><span class="sxs-lookup"><span data-stu-id="328dd-120">Page route action conventions</span></span>](#page-route-action-conventions)<ul><li><span data-ttu-id="328dd-121">AddFolderRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="328dd-121">AddFolderRouteModelConvention</span></span></li><li><span data-ttu-id="328dd-122">AddPageRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="328dd-122">AddPageRouteModelConvention</span></span></li><li><span data-ttu-id="328dd-123">AddPageRoute</span><span class="sxs-lookup"><span data-stu-id="328dd-123">AddPageRoute</span></span></li></ul> | <span data-ttu-id="328dd-124">Přidejte šablonu směrování do stránek ve složce a na jednu stránku.</span><span class="sxs-lookup"><span data-stu-id="328dd-124">Add a route template to pages in a folder and to a single page.</span></span> |
| [<span data-ttu-id="328dd-125">Konvence akcí modelu stránky</span><span class="sxs-lookup"><span data-stu-id="328dd-125">Page model action conventions</span></span>](#page-model-action-conventions)<ul><li><span data-ttu-id="328dd-126">AddFolderApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="328dd-126">AddFolderApplicationModelConvention</span></span></li><li><span data-ttu-id="328dd-127">AddPageApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="328dd-127">AddPageApplicationModelConvention</span></span></li><li><span data-ttu-id="328dd-128">ConfigureFilter (filtrovat třídu, lambda výraz nebo objekt pro vytváření filtru)</span><span class="sxs-lookup"><span data-stu-id="328dd-128">ConfigureFilter (filter class, lambda expression, or filter factory)</span></span></li></ul> | <span data-ttu-id="328dd-129">Umožňuje přidat záhlaví na stránky ve složce, přidat záhlaví na jednu stránku a nakonfigurovat [objekt pro vytváření filtru](xref:mvc/controllers/filters#ifilterfactory) tak, aby přidal hlavičku na stránky aplikace.</span><span class="sxs-lookup"><span data-stu-id="328dd-129">Add a header to pages in a folder, add a header to a single page, and configure a [filter factory](xref:mvc/controllers/filters#ifilterfactory) to add a header to an app's pages.</span></span> |

<span data-ttu-id="328dd-130">Razor Pages konvence jsou přidány a nakonfigurovány pomocí metody rozšíření <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> pro <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> v kolekci služeb ve třídě `Startup`.</span><span class="sxs-lookup"><span data-stu-id="328dd-130">Razor Pages conventions are added and configured using the <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> extension method to <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> on the service collection in the `Startup` class.</span></span> <span data-ttu-id="328dd-131">Následující příklady konvence jsou vysvětleny dále v tomto tématu:</span><span class="sxs-lookup"><span data-stu-id="328dd-131">The following convention examples are explained later in this topic:</span></span>

::: moniker range=">= aspnetcore-3.0"

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

::: moniker-end

::: moniker range="< aspnetcore-3.0"

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

::: moniker-end

## <a name="route-order"></a><span data-ttu-id="328dd-132">Pořadí směrování</span><span class="sxs-lookup"><span data-stu-id="328dd-132">Route order</span></span>

<span data-ttu-id="328dd-133">Trasy určují <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> pro zpracování (odpovídá směrování).</span><span class="sxs-lookup"><span data-stu-id="328dd-133">Routes specify an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> for processing (route matching).</span></span>

| <span data-ttu-id="328dd-134">Za</span><span class="sxs-lookup"><span data-stu-id="328dd-134">Order</span></span>            | <span data-ttu-id="328dd-135">Předvídatelně</span><span class="sxs-lookup"><span data-stu-id="328dd-135">Behavior</span></span> |
| :--------------: | -------- |
| <span data-ttu-id="328dd-136">– 1</span><span class="sxs-lookup"><span data-stu-id="328dd-136">-1</span></span>               | <span data-ttu-id="328dd-137">Trasa je zpracována před zpracováním jiných tras.</span><span class="sxs-lookup"><span data-stu-id="328dd-137">The route is processed before other routes are processed.</span></span> |
| <span data-ttu-id="328dd-138">0,8</span><span class="sxs-lookup"><span data-stu-id="328dd-138">0</span></span>                | <span data-ttu-id="328dd-139">Pořadí není zadáno (výchozí hodnota).</span><span class="sxs-lookup"><span data-stu-id="328dd-139">Order isn't specified (default value).</span></span> <span data-ttu-id="328dd-140">Při nepřiřazování `Order` (`Order = null`) jsou výchozí hodnoty `Order` trasy na 0 (nula) pro zpracování.</span><span class="sxs-lookup"><span data-stu-id="328dd-140">Not assigning `Order` (`Order = null`) defaults the route `Order` to 0 (zero) for processing.</span></span> |
| <span data-ttu-id="328dd-141">1, 2, &hellip; n</span><span class="sxs-lookup"><span data-stu-id="328dd-141">1, 2, &hellip; n</span></span> | <span data-ttu-id="328dd-142">Určuje pořadí zpracování směrování.</span><span class="sxs-lookup"><span data-stu-id="328dd-142">Specifies the route processing order.</span></span> |

<span data-ttu-id="328dd-143">Zpracování směrování je zřízené podle konvence:</span><span class="sxs-lookup"><span data-stu-id="328dd-143">Route processing is established by convention:</span></span>

* <span data-ttu-id="328dd-144">Trasy jsou zpracovávány v sekvenčním pořadí (-1, 0, 1, 2, &hellip; n).</span><span class="sxs-lookup"><span data-stu-id="328dd-144">Routes are processed in sequential order (-1, 0, 1, 2, &hellip; n).</span></span>
* <span data-ttu-id="328dd-145">Když mají stejné trasy stejné `Order`, bude se nejprve shodovat konkrétní trasa, za kterou následuje méně specifických tras.</span><span class="sxs-lookup"><span data-stu-id="328dd-145">When routes have the same `Order`, the most specific route is matched first followed by less specific routes.</span></span>
* <span data-ttu-id="328dd-146">Když se trasy se stejnou `Order` a stejný počet parametrů shodují s adresou URL požadavku, trasy se zpracovávají v pořadí, v jakém jsou přidány do <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>.</span><span class="sxs-lookup"><span data-stu-id="328dd-146">When routes with the same `Order` and the same number of parameters match a request URL, routes are processed in the order that they're added to the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>.</span></span>

<span data-ttu-id="328dd-147">Pokud je to možné, vyhněte se v závislosti na zavedeném pořadí zpracování směrování.</span><span class="sxs-lookup"><span data-stu-id="328dd-147">If possible, avoid depending on an established route processing order.</span></span> <span data-ttu-id="328dd-148">Obecně směrování vybírá správnou trasu s odpovídající adresou URL.</span><span class="sxs-lookup"><span data-stu-id="328dd-148">Generally, routing selects the correct route with URL matching.</span></span> <span data-ttu-id="328dd-149">Pokud musíte nastavit vlastnosti směrovacích `Order` tak, aby správně směrovaly požadavky, schéma směrování aplikace je pravděpodobně matoucí pro klienty a křehké k údržbě.</span><span class="sxs-lookup"><span data-stu-id="328dd-149">If you must set route `Order` properties to route requests correctly, the app's routing scheme is probably confusing to clients and fragile to maintain.</span></span> <span data-ttu-id="328dd-150">Vyhledejte zjednodušené schéma směrování aplikace.</span><span class="sxs-lookup"><span data-stu-id="328dd-150">Seek to simplify the app's routing scheme.</span></span> <span data-ttu-id="328dd-151">Ukázková aplikace vyžaduje explicitní pořadí zpracování tras, které předvádí několik scénářů směrování pomocí jedné aplikace.</span><span class="sxs-lookup"><span data-stu-id="328dd-151">The sample app requires an explicit route processing order to demonstrate several routing scenarios using a single app.</span></span> <span data-ttu-id="328dd-152">Měli byste se však pokusit vyhnout se postupu nastavení `Order` trasy v produkčních aplikacích.</span><span class="sxs-lookup"><span data-stu-id="328dd-152">However, you should attempt to avoid the practice of setting route `Order` in production apps.</span></span>

<span data-ttu-id="328dd-153">Směrování Razor Pages směrování a řadiče MVC sdílí implementaci.</span><span class="sxs-lookup"><span data-stu-id="328dd-153">Razor Pages routing and MVC controller routing share an implementation.</span></span> <span data-ttu-id="328dd-154">Informace o pořadí směrování v tématech MVC jsou k dispozici při [směrování na akce kontroleru: trasy atributů řazení](xref:mvc/controllers/routing#ordering-attribute-routes).</span><span class="sxs-lookup"><span data-stu-id="328dd-154">Information on route order in the MVC topics is available at [Routing to controller actions: Ordering attribute routes](xref:mvc/controllers/routing#ordering-attribute-routes).</span></span>

## <a name="model-conventions"></a><span data-ttu-id="328dd-155">Konvence modelu</span><span class="sxs-lookup"><span data-stu-id="328dd-155">Model conventions</span></span>

<span data-ttu-id="328dd-156">Přidejte delegáta pro <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> pro přidání [konvencí modelu](xref:mvc/controllers/application-model#conventions) , které se vztahují na Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="328dd-156">Add a delegate for <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> to add [model conventions](xref:mvc/controllers/application-model#conventions) that apply to Razor Pages.</span></span>

### <a name="add-a-route-model-convention-to-all-pages"></a><span data-ttu-id="328dd-157">Přidat konvenci modelu směrování na všechny stránky</span><span class="sxs-lookup"><span data-stu-id="328dd-157">Add a route model convention to all pages</span></span>

<span data-ttu-id="328dd-158">Pomocí <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> můžete vytvořit a přidat <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> do kolekce instancí <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention>, které se použijí během vytváření modelu směrování stránky.</span><span class="sxs-lookup"><span data-stu-id="328dd-158">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page route model construction.</span></span>

<span data-ttu-id="328dd-159">Ukázková aplikace přidá šablonu směrování `{globalTemplate?}` na všechny stránky v aplikaci:</span><span class="sxs-lookup"><span data-stu-id="328dd-159">The sample app adds a `{globalTemplate?}` route template to all of the pages in the app:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="328dd-160">Vlastnost <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> pro <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> je nastavena na `1`.</span><span class="sxs-lookup"><span data-stu-id="328dd-160">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `1`.</span></span> <span data-ttu-id="328dd-161">Tím se zajistí následující chování při shodě trasy v ukázkové aplikaci:</span><span class="sxs-lookup"><span data-stu-id="328dd-161">This ensures the following route matching behavior in the sample app:</span></span>

* <span data-ttu-id="328dd-162">Šablona směrování pro `TheContactPage/{text?}` se přidá později v tématu.</span><span class="sxs-lookup"><span data-stu-id="328dd-162">A route template for `TheContactPage/{text?}` is added later in the topic.</span></span> <span data-ttu-id="328dd-163">Trasa stránky kontaktu má výchozí pořadí `null` (`Order = 0`), takže odpovídá před šablonou směrování `{globalTemplate?}`.</span><span class="sxs-lookup"><span data-stu-id="328dd-163">The Contact Page route has a default order of `null` (`Order = 0`), so it matches before the `{globalTemplate?}` route template.</span></span>
* <span data-ttu-id="328dd-164">Šablona směrování `{aboutTemplate?}` je přidána později v tématu.</span><span class="sxs-lookup"><span data-stu-id="328dd-164">An `{aboutTemplate?}` route template is added later in the topic.</span></span> <span data-ttu-id="328dd-165">Šabloně `{aboutTemplate?}` je předána `Order` `2`.</span><span class="sxs-lookup"><span data-stu-id="328dd-165">The `{aboutTemplate?}` template is given an `Order` of `2`.</span></span> <span data-ttu-id="328dd-166">Když se na stránce o aplikaci žádá `/About/RouteDataValue`, načtou se "RouteDataValue" do `RouteData.Values["globalTemplate"]` (`Order = 1`) a není `RouteData.Values["aboutTemplate"]` (`Order = 2`) z důvodu nastavení vlastnosti `Order`.</span><span class="sxs-lookup"><span data-stu-id="328dd-166">When the About page is requested at `/About/RouteDataValue`, "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["aboutTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>
* <span data-ttu-id="328dd-167">Šablona směrování `{otherPagesTemplate?}` je přidána později v tématu.</span><span class="sxs-lookup"><span data-stu-id="328dd-167">An `{otherPagesTemplate?}` route template is added later in the topic.</span></span> <span data-ttu-id="328dd-168">Šabloně `{otherPagesTemplate?}` je předána `Order` `2`.</span><span class="sxs-lookup"><span data-stu-id="328dd-168">The `{otherPagesTemplate?}` template is given an `Order` of `2`.</span></span> <span data-ttu-id="328dd-169">Pokud se u libovolné stránky ve složce *Pages/OtherPages* požaduje parametr trasy (například `/OtherPages/Page1/RouteDataValue`), RouteDataValue se načte do `RouteData.Values["globalTemplate"]` (`Order = 1`) a ne`RouteData.Values["otherPagesTemplate"]` (`Order = 2`) z důvodu nastavení vlastnosti `Order`.</span><span class="sxs-lookup"><span data-stu-id="328dd-169">When any page in the *Pages/OtherPages* folder is requested with a route parameter (for example, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="328dd-170">Pokud je to možné, nenastavte `Order`, což má za následek `Order = 0`.</span><span class="sxs-lookup"><span data-stu-id="328dd-170">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="328dd-171">Pro výběr správné trasy se spoléhá na směrování.</span><span class="sxs-lookup"><span data-stu-id="328dd-171">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="328dd-172">Razor Pages možnosti, jako je přidání <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>, jsou přidány při přidání MVC do kolekce služby v `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="328dd-172">Razor Pages options, such as adding <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>, are added when MVC is added to the service collection in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="328dd-173">Příklad najdete v [ukázkové aplikaci](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/).</span><span class="sxs-lookup"><span data-stu-id="328dd-173">For an example, see the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/).</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="328dd-174">Požádejte o stránku ukázky o `localhost:5000/About/GlobalRouteValue` a Prohlédněte si výsledek:</span><span class="sxs-lookup"><span data-stu-id="328dd-174">Request the sample's About page at `localhost:5000/About/GlobalRouteValue` and inspect the result:</span></span>

![Stránka o aplikaci je požadována s segmentem směrování GlobalRouteValue.](razor-pages-conventions/_static/about-page-global-template.png)

### <a name="add-an-app-model-convention-to-all-pages"></a><span data-ttu-id="328dd-177">Přidání konvence modelu aplikace na všechny stránky</span><span class="sxs-lookup"><span data-stu-id="328dd-177">Add an app model convention to all pages</span></span>

<span data-ttu-id="328dd-178">Pomocí <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> můžete vytvořit a přidat <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> do kolekce instancí <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention>, které se použijí během vytváření modelu aplikace stránky.</span><span class="sxs-lookup"><span data-stu-id="328dd-178">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page app model construction.</span></span>

<span data-ttu-id="328dd-179">Chcete-li předvést tuto a další konvenci dále v tématu, ukázková aplikace obsahuje třídu `AddHeaderAttribute`.</span><span class="sxs-lookup"><span data-stu-id="328dd-179">To demonstrate this and other conventions later in the topic, the sample app includes an `AddHeaderAttribute` class.</span></span> <span data-ttu-id="328dd-180">Konstruktor třídy přijímá `name` řetězec a pole `values` řetězců.</span><span class="sxs-lookup"><span data-stu-id="328dd-180">The class constructor accepts a `name` string and a `values` string array.</span></span> <span data-ttu-id="328dd-181">Tyto hodnoty se používají ve své `OnResultExecuting` metodě pro nastavení hlavičky odpovědi.</span><span class="sxs-lookup"><span data-stu-id="328dd-181">These values are used in its `OnResultExecuting` method to set a response header.</span></span> <span data-ttu-id="328dd-182">Úplná třída je uvedena v části [konvence akcí modelu stránky](#page-model-action-conventions) dále v tématu.</span><span class="sxs-lookup"><span data-stu-id="328dd-182">The full class is shown in the [Page model action conventions](#page-model-action-conventions) section later in the topic.</span></span>

<span data-ttu-id="328dd-183">Ukázková aplikace používá třídu `AddHeaderAttribute` k přidání záhlaví, `GlobalHeader`, na všechny stránky v aplikaci:</span><span class="sxs-lookup"><span data-stu-id="328dd-183">The sample app uses the `AddHeaderAttribute` class to add a header, `GlobalHeader`, to all of the pages in the app:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="328dd-184">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="328dd-184">*Startup.cs*:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet2)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

::: moniker-end

<span data-ttu-id="328dd-185">Požádejte o stránku ukázek o `localhost:5000/About` a Prohlédněte si záhlaví a podívejte se na výsledek:</span><span class="sxs-lookup"><span data-stu-id="328dd-185">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![Hlavičky odpovědi stránky o produktu ukazují, že byl přidán GlobalHeader.](razor-pages-conventions/_static/about-page-global-header.png)

### <a name="add-a-handler-model-convention-to-all-pages"></a><span data-ttu-id="328dd-187">Přidat konvenci modelu obslužné rutiny na všechny stránky</span><span class="sxs-lookup"><span data-stu-id="328dd-187">Add a handler model convention to all pages</span></span>

<span data-ttu-id="328dd-188">Pomocí <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> můžete vytvořit a přidat <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> do kolekce instancí <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention>, které se použijí během vytváření modelu obslužné rutiny stránky.</span><span class="sxs-lookup"><span data-stu-id="328dd-188">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page handler model construction.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalPageHandlerModelConvention.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalPageHandlerModelConvention.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="328dd-189">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="328dd-189">*Startup.cs*:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet10)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet10)]

::: moniker-end

## <a name="page-route-action-conventions"></a><span data-ttu-id="328dd-190">Konvence akcí při směrování stránky</span><span class="sxs-lookup"><span data-stu-id="328dd-190">Page route action conventions</span></span>

<span data-ttu-id="328dd-191">Výchozí zprostředkovatel modelu směrování odvozený od <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> vyvolá konvence, které jsou navržené tak, aby poskytovaly body rozšiřitelnosti pro konfiguraci tras stránky.</span><span class="sxs-lookup"><span data-stu-id="328dd-191">The default route model provider that derives from <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> invokes conventions which are designed to provide extensibility points for configuring page routes.</span></span>

### <a name="folder-route-model-convention"></a><span data-ttu-id="328dd-192">Konvence modelu směrování složky</span><span class="sxs-lookup"><span data-stu-id="328dd-192">Folder route model convention</span></span>

<span data-ttu-id="328dd-193">Pomocí <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> můžete vytvořit a přidat <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention>, který vyvolá akci na <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> pro všechny stránky v zadané složce.</span><span class="sxs-lookup"><span data-stu-id="328dd-193">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> for all of the pages under the specified folder.</span></span>

<span data-ttu-id="328dd-194">Ukázková aplikace používá <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> k přidání šablony trasy `{otherPagesTemplate?}` na stránky ve složce *OtherPages* :</span><span class="sxs-lookup"><span data-stu-id="328dd-194">The sample app uses <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> to add an `{otherPagesTemplate?}` route template to the pages in the *OtherPages* folder:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet3)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

::: moniker-end

<span data-ttu-id="328dd-195">Vlastnost <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> pro <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> je nastavena na `2`.</span><span class="sxs-lookup"><span data-stu-id="328dd-195">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `2`.</span></span> <span data-ttu-id="328dd-196">Tím je zajištěno, že šablona pro `{globalTemplate?}` (nastavená dříve v tématu `1`) má prioritu první pozice hodnoty dat trasy, když je zadána hodnota jedné trasy.</span><span class="sxs-lookup"><span data-stu-id="328dd-196">This ensures that the template for `{globalTemplate?}` (set earlier in the topic to `1`) is given priority for the first route data value position when a single route value is provided.</span></span> <span data-ttu-id="328dd-197">Pokud je stránka ve složce *Pages/OtherPages* požadována s hodnotou parametru trasy (například `/OtherPages/Page1/RouteDataValue`), je "RouteDataValue" načteno do `RouteData.Values["globalTemplate"]` (`Order = 1`) a není `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) z důvodu nastavení vlastnosti `Order`.</span><span class="sxs-lookup"><span data-stu-id="328dd-197">If a page in the *Pages/OtherPages* folder is requested with a route parameter value (for example, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="328dd-198">Pokud je to možné, nenastavte `Order`, což má za následek `Order = 0`.</span><span class="sxs-lookup"><span data-stu-id="328dd-198">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="328dd-199">Pro výběr správné trasy se spoléhá na směrování.</span><span class="sxs-lookup"><span data-stu-id="328dd-199">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="328dd-200">Vyžádejte si Page1 stránku ukázky na `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` a Prohlédněte si výsledek:</span><span class="sxs-lookup"><span data-stu-id="328dd-200">Request the sample's Page1 page at `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` and inspect the result:</span></span>

![Page1 ve složce OtherPages se vyžádá s segmentem směrování GlobalRouteValue a OtherPagesRouteValue.](razor-pages-conventions/_static/otherpages-page1-global-and-otherpages-templates.png)

### <a name="page-route-model-convention"></a><span data-ttu-id="328dd-203">Konvence modelu směrování stránky</span><span class="sxs-lookup"><span data-stu-id="328dd-203">Page route model convention</span></span>

<span data-ttu-id="328dd-204">Pomocí <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> vytvořit a přidat <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention>, který vyvolá akci na <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> pro stránku se zadaným názvem.</span><span class="sxs-lookup"><span data-stu-id="328dd-204">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> for the page with the specified name.</span></span>

<span data-ttu-id="328dd-205">Ukázková aplikace používá `AddPageRouteModelConvention` k přidání šablony trasy `{aboutTemplate?}` na stránku o aplikaci:</span><span class="sxs-lookup"><span data-stu-id="328dd-205">The sample app uses `AddPageRouteModelConvention` to add an `{aboutTemplate?}` route template to the About page:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet4)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

::: moniker-end

<span data-ttu-id="328dd-206">Vlastnost <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> pro <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> je nastavena na `2`.</span><span class="sxs-lookup"><span data-stu-id="328dd-206">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `2`.</span></span> <span data-ttu-id="328dd-207">Tím je zajištěno, že šablona pro `{globalTemplate?}` (nastavená dříve v tématu `1`) má prioritu první pozice hodnoty dat trasy, když je zadána hodnota jedné trasy.</span><span class="sxs-lookup"><span data-stu-id="328dd-207">This ensures that the template for `{globalTemplate?}` (set earlier in the topic to `1`) is given priority for the first route data value position when a single route value is provided.</span></span> <span data-ttu-id="328dd-208">Pokud se stránka o žádosti vyžaduje s hodnotou parametru Route na `/About/RouteDataValue`, načte se RouteDataValue do `RouteData.Values["globalTemplate"]` (`Order = 1`) a ne`RouteData.Values["aboutTemplate"]` (`Order = 2`) z důvodu nastavení vlastnosti `Order`.</span><span class="sxs-lookup"><span data-stu-id="328dd-208">If the About page is requested with a route parameter value at `/About/RouteDataValue`, "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["aboutTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="328dd-209">Pokud je to možné, nenastavte `Order`, což má za následek `Order = 0`.</span><span class="sxs-lookup"><span data-stu-id="328dd-209">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="328dd-210">Pro výběr správné trasy se spoléhá na směrování.</span><span class="sxs-lookup"><span data-stu-id="328dd-210">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="328dd-211">Požádejte o stránku ukázky o `localhost:5000/About/GlobalRouteValue/AboutRouteValue` a Prohlédněte si výsledek:</span><span class="sxs-lookup"><span data-stu-id="328dd-211">Request the sample's About page at `localhost:5000/About/GlobalRouteValue/AboutRouteValue` and inspect the result:</span></span>

![U segmentů směrování pro GlobalRouteValue a AboutRouteValue se žádá o stránku.](razor-pages-conventions/_static/about-page-global-and-about-templates.png)

::: moniker range=">= aspnetcore-2.2"

## <a name="use-a-parameter-transformer-to-customize-page-routes"></a><span data-ttu-id="328dd-214">Použití transformátoru parametrů k přizpůsobení cest stránky</span><span class="sxs-lookup"><span data-stu-id="328dd-214">Use a parameter transformer to customize page routes</span></span>

<span data-ttu-id="328dd-215">Trasy stránky generované ASP.NET Core lze přizpůsobit pomocí transformátoru parametrů.</span><span class="sxs-lookup"><span data-stu-id="328dd-215">Page routes generated by ASP.NET Core can be customized using a parameter transformer.</span></span> <span data-ttu-id="328dd-216">Transformátor parametru implementuje `IOutboundParameterTransformer` a transformuje hodnotu parametrů.</span><span class="sxs-lookup"><span data-stu-id="328dd-216">A parameter transformer implements `IOutboundParameterTransformer` and transforms the value of parameters.</span></span> <span data-ttu-id="328dd-217">Například vlastní parametr `SlugifyParameterTransformer` Transformer změní hodnotu trasy `SubscriptionManagement` na `subscription-management`.</span><span class="sxs-lookup"><span data-stu-id="328dd-217">For example, a custom `SlugifyParameterTransformer` parameter transformer changes the `SubscriptionManagement` route value to `subscription-management`.</span></span>

<span data-ttu-id="328dd-218">Konvence modelu směrování na stránce `PageRouteTransformerConvention` aplikuje transformátor parametrů na složku a název souboru segmentů automaticky generovaných tras stránky v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="328dd-218">The `PageRouteTransformerConvention` page route model convention applies a parameter transformer to the folder and file name segments of automatically generated page routes in an app.</span></span> <span data-ttu-id="328dd-219">Například soubor Razor Pages v */Pages/SubscriptionManagement/ViewAll.cshtml* by měl přepsané směrování z `/SubscriptionManagement/ViewAll` na `/subscription-management/view-all`.</span><span class="sxs-lookup"><span data-stu-id="328dd-219">For example, the Razor Pages file at */Pages/SubscriptionManagement/ViewAll.cshtml* would have its route rewritten from `/SubscriptionManagement/ViewAll` to `/subscription-management/view-all`.</span></span>

<span data-ttu-id="328dd-220">`PageRouteTransformerConvention` pouze transformuje automaticky generované segmenty cesty stránky, které pocházejí ze složky Razor Pages a názvu souboru.</span><span class="sxs-lookup"><span data-stu-id="328dd-220">`PageRouteTransformerConvention` only transforms the automatically generated segments of a page route that come from the Razor Pages folder and file name.</span></span> <span data-ttu-id="328dd-221">Netransformuje segmenty směrování přidané pomocí direktivy `@page`.</span><span class="sxs-lookup"><span data-stu-id="328dd-221">It doesn't transform route segments added with the `@page` directive.</span></span> <span data-ttu-id="328dd-222">Konvence také netransformuje trasy přidané pomocí <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>.</span><span class="sxs-lookup"><span data-stu-id="328dd-222">The convention also doesn't transform routes added by <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>.</span></span>

<span data-ttu-id="328dd-223">@No__t_0 je v `Startup.ConfigureServices` zaregistrován jako možnost:</span><span class="sxs-lookup"><span data-stu-id="328dd-223">The `PageRouteTransformerConvention` is registered as an option in `Startup.ConfigureServices`:</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

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

::: moniker range="= aspnetcore-2.2"

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

## <a name="configure-a-page-route"></a><span data-ttu-id="328dd-224">Konfigurace trasy stránky</span><span class="sxs-lookup"><span data-stu-id="328dd-224">Configure a page route</span></span>

<span data-ttu-id="328dd-225">Pomocí <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> můžete nakonfigurovat směrování na stránku na zadané cestě stránky.</span><span class="sxs-lookup"><span data-stu-id="328dd-225">Use <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> to configure a route to a page at the specified page path.</span></span> <span data-ttu-id="328dd-226">Vygenerované odkazy na stránku používají zadanou trasu.</span><span class="sxs-lookup"><span data-stu-id="328dd-226">Generated links to the page use your specified route.</span></span> <span data-ttu-id="328dd-227">`AddPageRoute` používá k vytvoření trasy `AddPageRouteModelConvention`.</span><span class="sxs-lookup"><span data-stu-id="328dd-227">`AddPageRoute` uses `AddPageRouteModelConvention` to establish the route.</span></span>

<span data-ttu-id="328dd-228">Ukázková aplikace vytvoří trasu, která `/TheContactPage` pro *kontakt. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="328dd-228">The sample app creates a route to `/TheContactPage` for *Contact.cshtml*:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet5)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet5)]

::: moniker-end

<span data-ttu-id="328dd-229">Stránku kontaktů lze také získat na `/Contact` prostřednictvím výchozí trasy.</span><span class="sxs-lookup"><span data-stu-id="328dd-229">The Contact page can also be reached at `/Contact` via its default route.</span></span>

<span data-ttu-id="328dd-230">Vlastní trasa ukázkové aplikace na stránce kontaktů umožňuje, aby byl `text` segment trasy (`{text?}`).</span><span class="sxs-lookup"><span data-stu-id="328dd-230">The sample app's custom route to the Contact page allows for an optional `text` route segment (`{text?}`).</span></span> <span data-ttu-id="328dd-231">Tato stránka zahrnuje také tento volitelný segment v direktivě `@page` pro případ, že návštěvník přistupuje k této stránce `/Contact` trase:</span><span class="sxs-lookup"><span data-stu-id="328dd-231">The page also includes this optional segment in its `@page` directive in case the visitor accesses the page at its `/Contact` route:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-cshtml[](razor-pages-conventions/samples/3.x/SampleApp/Pages/Contact.cshtml?highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-cshtml[](razor-pages-conventions/samples/2.x/SampleApp/Pages/Contact.cshtml?highlight=1)]

::: moniker-end

<span data-ttu-id="328dd-232">Všimněte si, že adresa URL vygenerovaná pro odkaz **kontaktu** na vykreslené stránce odráží aktualizovanou trasu:</span><span class="sxs-lookup"><span data-stu-id="328dd-232">Note that the URL generated for the **Contact** link in the rendered page reflects the updated route:</span></span>

![Odkaz na ukázkový kontakt aplikace na navigačním panelu](razor-pages-conventions/_static/contact-link.png)

![Kontrola odkazu na kontakt ve vykresleném kódu HTML znamená, že odkaz href je nastaven na hodnotu "/TheContactPage".](razor-pages-conventions/_static/contact-link-source.png)

<span data-ttu-id="328dd-235">Navštivte stránku kontaktů buď na své běžné trase, `/Contact`, nebo na vlastní trasu `/TheContactPage`.</span><span class="sxs-lookup"><span data-stu-id="328dd-235">Visit the Contact page at either its ordinary route, `/Contact`, or the custom route, `/TheContactPage`.</span></span> <span data-ttu-id="328dd-236">Pokud zadáte další segment `text` trasy, stránka zobrazuje segment zakódovaný ve formátu HTML, který zadáte:</span><span class="sxs-lookup"><span data-stu-id="328dd-236">If you supply an additional `text` route segment, the page shows the HTML-encoded segment that you provide:</span></span>

![Prohlížeč Edge – příklad poskytnutí volitelného segmentu "text" trasy "TextValue" v adrese URL](razor-pages-conventions/_static/route-segment-with-custom-route.png)

## <a name="page-model-action-conventions"></a><span data-ttu-id="328dd-239">Konvence akcí modelu stránky</span><span class="sxs-lookup"><span data-stu-id="328dd-239">Page model action conventions</span></span>

<span data-ttu-id="328dd-240">Výchozí poskytovatel modelu stránky, který implementuje <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider>, vyvolá konvence, které jsou navrženy tak, aby poskytovaly body rozšiřitelnosti pro konfiguraci modelů stránek.</span><span class="sxs-lookup"><span data-stu-id="328dd-240">The default page model provider that implements <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> invokes conventions which are designed to provide extensibility points for configuring page models.</span></span> <span data-ttu-id="328dd-241">Tyto konvence jsou užitečné při sestavování a úpravách scénářů zjišťování a zpracování stránky.</span><span class="sxs-lookup"><span data-stu-id="328dd-241">These conventions are useful when building and modifying page discovery and processing scenarios.</span></span>

<span data-ttu-id="328dd-242">V příkladech v této části používá ukázková aplikace třídu `AddHeaderAttribute`, což je <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>, která používá hlavičku odpovědi:</span><span class="sxs-lookup"><span data-stu-id="328dd-242">For the examples in this section, the sample app uses an `AddHeaderAttribute` class, which is a <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>, that applies a response header:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="328dd-243">Pomocí konvence Ukázka ukazuje, jak použít atribut na všechny stránky ve složce a na jednu stránku.</span><span class="sxs-lookup"><span data-stu-id="328dd-243">Using conventions, the sample demonstrates how to apply the attribute to all of the pages in a folder and to a single page.</span></span>

<span data-ttu-id="328dd-244">**Konvence modelu aplikace složky**</span><span class="sxs-lookup"><span data-stu-id="328dd-244">**Folder app model convention**</span></span>

<span data-ttu-id="328dd-245">Pomocí <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> můžete vytvořit a přidat <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention>, který vyvolá akci na instancích <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> pro všechny stránky v zadané složce.</span><span class="sxs-lookup"><span data-stu-id="328dd-245">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> that invokes an action on <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> instances for all pages under the specified folder.</span></span>

<span data-ttu-id="328dd-246">Ukázka demonstruje použití `AddFolderApplicationModelConvention` přidáním hlavičky, `OtherPagesHeader` na stránky ve složce *OtherPages* v aplikaci:</span><span class="sxs-lookup"><span data-stu-id="328dd-246">The sample demonstrates the use of `AddFolderApplicationModelConvention` by adding a header, `OtherPagesHeader`, to the pages inside the *OtherPages* folder of the app:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet6)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet6)]

::: moniker-end

<span data-ttu-id="328dd-247">Vyžádejte si Page1 stránku ukázky na `localhost:5000/OtherPages/Page1` a Prohlédněte si hlavičky a podívejte se na výsledek:</span><span class="sxs-lookup"><span data-stu-id="328dd-247">Request the sample's Page1 page at `localhost:5000/OtherPages/Page1` and inspect the headers to view the result:</span></span>

![Hlavičky odpovědi stránky OtherPages/Page1 ukazují, že byla přidána OtherPagesHeader.](razor-pages-conventions/_static/page1-otherpages-header.png)

<span data-ttu-id="328dd-249">**Konvence modelu aplikace stránky**</span><span class="sxs-lookup"><span data-stu-id="328dd-249">**Page app model convention**</span></span>

<span data-ttu-id="328dd-250">Pomocí <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> vytvořit a přidat <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention>, který vyvolá akci na <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> pro stránku se zadaným názvem.</span><span class="sxs-lookup"><span data-stu-id="328dd-250">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> for the page with the specified name.</span></span>

<span data-ttu-id="328dd-251">Ukázka demonstruje použití `AddPageApplicationModelConvention` přidáním hlavičky, `AboutHeader` na stránku o aplikaci:</span><span class="sxs-lookup"><span data-stu-id="328dd-251">The sample demonstrates the use of `AddPageApplicationModelConvention` by adding a header, `AboutHeader`, to the About page:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet7)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet7)]

::: moniker-end

<span data-ttu-id="328dd-252">Požádejte o stránku ukázek o `localhost:5000/About` a Prohlédněte si záhlaví a podívejte se na výsledek:</span><span class="sxs-lookup"><span data-stu-id="328dd-252">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![Hlavičky odpovědi stránky o produktu ukazují, že byl přidán AboutHeader.](razor-pages-conventions/_static/about-page-about-header.png)

<span data-ttu-id="328dd-254">**Konfigurace filtru**</span><span class="sxs-lookup"><span data-stu-id="328dd-254">**Configure a filter**</span></span>

<span data-ttu-id="328dd-255"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> nakonfiguruje zadaný filtr, který se má použít.</span><span class="sxs-lookup"><span data-stu-id="328dd-255"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configures the specified filter to apply.</span></span> <span data-ttu-id="328dd-256">Můžete implementovat třídu filtru, ale ukázková aplikace ukazuje, jak implementovat filtr ve výrazu lambda, který je implementován na pozadí jako objekt pro vytváření, který vrací filtr:</span><span class="sxs-lookup"><span data-stu-id="328dd-256">You can implement a filter class, but the sample app shows how to implement a filter in a lambda expression, which is implemented behind-the-scenes as a factory that returns a filter:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet8)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet8)]

::: moniker-end

<span data-ttu-id="328dd-257">Model webové aplikace se používá ke kontrole relativní cesty pro segmenty, které vedou ke stránce Page2 ve složce *OtherPages* .</span><span class="sxs-lookup"><span data-stu-id="328dd-257">The page app model is used to check the relative path for segments that lead to the Page2 page in the *OtherPages* folder.</span></span> <span data-ttu-id="328dd-258">Pokud podmínka projde, přidá se hlavička.</span><span class="sxs-lookup"><span data-stu-id="328dd-258">If the condition passes, a header is added.</span></span> <span data-ttu-id="328dd-259">V takovém případě je `EmptyFilter` použito.</span><span class="sxs-lookup"><span data-stu-id="328dd-259">If not, the `EmptyFilter` is applied.</span></span>

<span data-ttu-id="328dd-260">`EmptyFilter` je [Filtr akcí](xref:mvc/controllers/filters#action-filters).</span><span class="sxs-lookup"><span data-stu-id="328dd-260">`EmptyFilter` is an [Action filter](xref:mvc/controllers/filters#action-filters).</span></span> <span data-ttu-id="328dd-261">Vzhledem k tomu, že jsou filtry akcí ignorovány Razor Pages, `EmptyFilter` nemá žádný vliv, pokud cesta neobsahuje `OtherPages/Page2`.</span><span class="sxs-lookup"><span data-stu-id="328dd-261">Since Action filters are ignored by Razor Pages, the `EmptyFilter` has no effect as intended if the path doesn't contain `OtherPages/Page2`.</span></span>

<span data-ttu-id="328dd-262">Vyžádejte si Page2 stránku ukázky na `localhost:5000/OtherPages/Page2` a Prohlédněte si hlavičky a podívejte se na výsledek:</span><span class="sxs-lookup"><span data-stu-id="328dd-262">Request the sample's Page2 page at `localhost:5000/OtherPages/Page2` and inspect the headers to view the result:</span></span>

![OtherPagesPage2Header se přidá do odpovědi pro Page2.](razor-pages-conventions/_static/page2-filter-header.png)

<span data-ttu-id="328dd-264">**Konfigurace objektu pro vytváření filtrů**</span><span class="sxs-lookup"><span data-stu-id="328dd-264">**Configure a filter factory**</span></span>

<span data-ttu-id="328dd-265"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> nakonfiguruje určený objekt pro vytváření a použije [filtry](xref:mvc/controllers/filters) na všechny Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="328dd-265"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configures the specified factory to apply [filters](xref:mvc/controllers/filters) to all Razor Pages.</span></span>

<span data-ttu-id="328dd-266">Ukázková aplikace poskytuje příklad použití [objektu pro vytváření filtru](xref:mvc/controllers/filters#ifilterfactory) přidáním hlavičky, `FilterFactoryHeader` se dvěma hodnotami na stránky aplikace:</span><span class="sxs-lookup"><span data-stu-id="328dd-266">The sample app provides an example of using a [filter factory](xref:mvc/controllers/filters#ifilterfactory) by adding a header, `FilterFactoryHeader`, with two values to the app's pages:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet9)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet9)]

::: moniker-end

<span data-ttu-id="328dd-267">*AddHeaderWithFactory.cs*:</span><span class="sxs-lookup"><span data-stu-id="328dd-267">*AddHeaderWithFactory.cs*:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Factories/AddHeaderWithFactory.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Factories/AddHeaderWithFactory.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="328dd-268">Požádejte o stránku ukázek o `localhost:5000/About` a Prohlédněte si záhlaví a podívejte se na výsledek:</span><span class="sxs-lookup"><span data-stu-id="328dd-268">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![Hlavičky odpovědi stránky o stránku zobrazují, že byly přidány dvě hlavičky FilterFactoryHeader.](razor-pages-conventions/_static/about-page-filter-factory-header.png)

## <a name="mvc-filters-and-the-page-filter-ipagefilter"></a><span data-ttu-id="328dd-270">Filtry MVC a filtr stránky (IPageFilter)</span><span class="sxs-lookup"><span data-stu-id="328dd-270">MVC Filters and the Page filter (IPageFilter)</span></span>

<span data-ttu-id="328dd-271">[Filtry akcí](xref:mvc/controllers/filters#action-filters) MVC se Razor Pages ignorují, protože Razor Pages použít obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="328dd-271">MVC [Action filters](xref:mvc/controllers/filters#action-filters) are ignored by Razor Pages, since Razor Pages use handler methods.</span></span> <span data-ttu-id="328dd-272">Další typy filtrů MVC jsou k dispozici pro použití: [autorizace](xref:mvc/controllers/filters#authorization-filters), [výjimka](xref:mvc/controllers/filters#exception-filters), [prostředek](xref:mvc/controllers/filters#resource-filters)a [výsledek](xref:mvc/controllers/filters#result-filters).</span><span class="sxs-lookup"><span data-stu-id="328dd-272">Other types of MVC filters are available for you to use: [Authorization](xref:mvc/controllers/filters#authorization-filters), [Exception](xref:mvc/controllers/filters#exception-filters), [Resource](xref:mvc/controllers/filters#resource-filters), and [Result](xref:mvc/controllers/filters#result-filters).</span></span> <span data-ttu-id="328dd-273">Další informace najdete v tématu [filtry](xref:mvc/controllers/filters) .</span><span class="sxs-lookup"><span data-stu-id="328dd-273">For more information, see the [Filters](xref:mvc/controllers/filters) topic.</span></span>

<span data-ttu-id="328dd-274">Filtr stránky (<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>) je filtr, který se vztahuje na Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="328dd-274">The Page filter (<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>) is a filter that applies to Razor Pages.</span></span> <span data-ttu-id="328dd-275">Další informace naleznete v tématu [metody Filter pro Razor Pages](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="328dd-275">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="328dd-276">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="328dd-276">Additional resources</span></span>

* <xref:security/authorization/razor-pages-authorization>
* <xref:mvc/controllers/areas#areas-with-razor-pages>
