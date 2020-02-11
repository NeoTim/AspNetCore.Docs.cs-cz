---
title: Razor Pages konvence směrování a aplikace v ASP.NET Core
author: guardrex
description: Seznamte se s tím, jak konvence poskytovatelů modelů směrování a aplikací umožňují řídit směrování, zjišťování a zpracování stránky.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
uid: razor-pages/razor-pages-conventions
ms.openlocfilehash: d8377c0a0b8a29fe4b6a7fa67beeff84927c8b74
ms.sourcegitcommit: 235623b6e5a5d1841139c82a11ac2b4b3f31a7a9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/10/2020
ms.locfileid: "77114767"
---
# <a name="razor-pages-route-and-app-conventions-in-aspnet-core"></a><span data-ttu-id="42914-103">Razor Pages konvence směrování a aplikace v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="42914-103">Razor Pages route and app conventions in ASP.NET Core</span></span>

<span data-ttu-id="42914-104">Od [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="42914-104">By [Luke Latham](https://github.com/guardrex)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="42914-105">Naučte se používat [konvence směrování stránky a poskytovatele modelů aplikací](xref:mvc/controllers/application-model#conventions) k řízení směrování, zjišťování a zpracování stránky v aplikacích Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="42914-105">Learn how to use page [route and app model provider conventions](xref:mvc/controllers/application-model#conventions) to control page routing, discovery, and processing in Razor Pages apps.</span></span>

<span data-ttu-id="42914-106">Pokud potřebujete nakonfigurovat vlastní trasy stránky pro jednotlivé stránky, nakonfigurujte směrování na stránky pomocí [AddPageRoute konvence](#configure-a-page-route) popsané dále v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="42914-106">When you need to configure custom page routes for individual pages, configure routing to pages with the [AddPageRoute convention](#configure-a-page-route) described later in this topic.</span></span>

<span data-ttu-id="42914-107">Chcete-li určit trasu stránky, přidat segmenty směrování nebo přidat parametry do trasy, použijte direktivu `@page` stránky.</span><span class="sxs-lookup"><span data-stu-id="42914-107">To specify a page route, add route segments, or add parameters to a route, use the page's `@page` directive.</span></span> <span data-ttu-id="42914-108">Další informace najdete v tématu [vlastní trasy](xref:razor-pages/index#custom-routes).</span><span class="sxs-lookup"><span data-stu-id="42914-108">For more information, see [Custom routes](xref:razor-pages/index#custom-routes).</span></span>

<span data-ttu-id="42914-109">Existují vyhrazená slova, která nelze použít jako segmenty směrování nebo názvy parametrů.</span><span class="sxs-lookup"><span data-stu-id="42914-109">There are reserved words that can't be used as route segments or parameter names.</span></span> <span data-ttu-id="42914-110">Další informace najdete v tématu [směrování: názvy rezervovaných směrování](xref:fundamentals/routing#reserved-routing-names).</span><span class="sxs-lookup"><span data-stu-id="42914-110">For more information, see [Routing: Reserved routing names](xref:fundamentals/routing#reserved-routing-names).</span></span>

<span data-ttu-id="42914-111">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="42914-111">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

| <span data-ttu-id="42914-112">Scénář</span><span class="sxs-lookup"><span data-stu-id="42914-112">Scenario</span></span> | <span data-ttu-id="42914-113">Ukázka znázorňuje...</span><span class="sxs-lookup"><span data-stu-id="42914-113">The sample demonstrates ...</span></span> |
| -------- | --------------------------- |
| [<span data-ttu-id="42914-114">Konvence modelu</span><span class="sxs-lookup"><span data-stu-id="42914-114">Model conventions</span></span>](#model-conventions)<br><br><span data-ttu-id="42914-115">Konvence. Add</span><span class="sxs-lookup"><span data-stu-id="42914-115">Conventions.Add</span></span><ul><li><span data-ttu-id="42914-116">IPageRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="42914-116">IPageRouteModelConvention</span></span></li><li><span data-ttu-id="42914-117">IPageApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="42914-117">IPageApplicationModelConvention</span></span></li><li><span data-ttu-id="42914-118">IPageHandlerModelConvention</span><span class="sxs-lookup"><span data-stu-id="42914-118">IPageHandlerModelConvention</span></span></li></ul> | <span data-ttu-id="42914-119">Přidejte šablonu a hlavičku směrování na stránky aplikace.</span><span class="sxs-lookup"><span data-stu-id="42914-119">Add a route template and header to an app's pages.</span></span> |
| [<span data-ttu-id="42914-120">Konvence akcí při směrování stránky</span><span class="sxs-lookup"><span data-stu-id="42914-120">Page route action conventions</span></span>](#page-route-action-conventions)<ul><li><span data-ttu-id="42914-121">AddFolderRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="42914-121">AddFolderRouteModelConvention</span></span></li><li><span data-ttu-id="42914-122">AddPageRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="42914-122">AddPageRouteModelConvention</span></span></li><li><span data-ttu-id="42914-123">AddPageRoute</span><span class="sxs-lookup"><span data-stu-id="42914-123">AddPageRoute</span></span></li></ul> | <span data-ttu-id="42914-124">Přidejte šablonu směrování do stránek ve složce a na jednu stránku.</span><span class="sxs-lookup"><span data-stu-id="42914-124">Add a route template to pages in a folder and to a single page.</span></span> |
| [<span data-ttu-id="42914-125">Konvence akcí modelu stránky</span><span class="sxs-lookup"><span data-stu-id="42914-125">Page model action conventions</span></span>](#page-model-action-conventions)<ul><li><span data-ttu-id="42914-126">AddFolderApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="42914-126">AddFolderApplicationModelConvention</span></span></li><li><span data-ttu-id="42914-127">AddPageApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="42914-127">AddPageApplicationModelConvention</span></span></li><li><span data-ttu-id="42914-128">ConfigureFilter (filtrovat třídu, lambda výraz nebo objekt pro vytváření filtru)</span><span class="sxs-lookup"><span data-stu-id="42914-128">ConfigureFilter (filter class, lambda expression, or filter factory)</span></span></li></ul> | <span data-ttu-id="42914-129">Umožňuje přidat záhlaví na stránky ve složce, přidat záhlaví na jednu stránku a nakonfigurovat [objekt pro vytváření filtru](xref:mvc/controllers/filters#ifilterfactory) tak, aby přidal hlavičku na stránky aplikace.</span><span class="sxs-lookup"><span data-stu-id="42914-129">Add a header to pages in a folder, add a header to a single page, and configure a [filter factory](xref:mvc/controllers/filters#ifilterfactory) to add a header to an app's pages.</span></span> |

<span data-ttu-id="42914-130">Razor Pages konvence jsou přidány a nakonfigurovány pomocí metody rozšíření <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> pro <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> v kolekci služeb ve třídě `Startup`.</span><span class="sxs-lookup"><span data-stu-id="42914-130">Razor Pages conventions are added and configured using the <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> extension method to <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> on the service collection in the `Startup` class.</span></span> <span data-ttu-id="42914-131">Následující příklady konvence jsou vysvětleny dále v tomto tématu:</span><span class="sxs-lookup"><span data-stu-id="42914-131">The following convention examples are explained later in this topic:</span></span>

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

## <a name="route-order"></a><span data-ttu-id="42914-132">Pořadí směrování</span><span class="sxs-lookup"><span data-stu-id="42914-132">Route order</span></span>

<span data-ttu-id="42914-133">Trasy určují <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> pro zpracování (odpovídá směrování).</span><span class="sxs-lookup"><span data-stu-id="42914-133">Routes specify an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> for processing (route matching).</span></span>

| <span data-ttu-id="42914-134">Objednání</span><span class="sxs-lookup"><span data-stu-id="42914-134">Order</span></span>            | <span data-ttu-id="42914-135">Chování</span><span class="sxs-lookup"><span data-stu-id="42914-135">Behavior</span></span> |
| :--------------: | -------- |
| <span data-ttu-id="42914-136">-1</span><span class="sxs-lookup"><span data-stu-id="42914-136">-1</span></span>               | <span data-ttu-id="42914-137">Trasa je zpracována před zpracováním jiných tras.</span><span class="sxs-lookup"><span data-stu-id="42914-137">The route is processed before other routes are processed.</span></span> |
| <span data-ttu-id="42914-138">0</span><span class="sxs-lookup"><span data-stu-id="42914-138">0</span></span>                | <span data-ttu-id="42914-139">Pořadí není zadáno (výchozí hodnota).</span><span class="sxs-lookup"><span data-stu-id="42914-139">Order isn't specified (default value).</span></span> <span data-ttu-id="42914-140">Při nepřiřazování `Order` (`Order = null`) jsou výchozí hodnoty `Order` trasy na 0 (nula) pro zpracování.</span><span class="sxs-lookup"><span data-stu-id="42914-140">Not assigning `Order` (`Order = null`) defaults the route `Order` to 0 (zero) for processing.</span></span> |
| <span data-ttu-id="42914-141">1, 2, &hellip; n</span><span class="sxs-lookup"><span data-stu-id="42914-141">1, 2, &hellip; n</span></span> | <span data-ttu-id="42914-142">Určuje pořadí zpracování směrování.</span><span class="sxs-lookup"><span data-stu-id="42914-142">Specifies the route processing order.</span></span> |

<span data-ttu-id="42914-143">Zpracování směrování je zřízené podle konvence:</span><span class="sxs-lookup"><span data-stu-id="42914-143">Route processing is established by convention:</span></span>

* <span data-ttu-id="42914-144">Trasy jsou zpracovávány v sekvenčním pořadí (-1, 0, 1, 2, &hellip; n).</span><span class="sxs-lookup"><span data-stu-id="42914-144">Routes are processed in sequential order (-1, 0, 1, 2, &hellip; n).</span></span>
* <span data-ttu-id="42914-145">Když mají stejné trasy stejné `Order`, bude se nejprve shodovat konkrétní trasa, za kterou následuje méně specifických tras.</span><span class="sxs-lookup"><span data-stu-id="42914-145">When routes have the same `Order`, the most specific route is matched first followed by less specific routes.</span></span>
* <span data-ttu-id="42914-146">Když se trasy se stejnou `Order` a stejný počet parametrů shodují s adresou URL požadavku, trasy se zpracovávají v pořadí, v jakém jsou přidány do <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>.</span><span class="sxs-lookup"><span data-stu-id="42914-146">When routes with the same `Order` and the same number of parameters match a request URL, routes are processed in the order that they're added to the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>.</span></span>

<span data-ttu-id="42914-147">Pokud je to možné, vyhněte se v závislosti na zavedeném pořadí zpracování směrování.</span><span class="sxs-lookup"><span data-stu-id="42914-147">If possible, avoid depending on an established route processing order.</span></span> <span data-ttu-id="42914-148">Obecně směrování vybírá správnou trasu s odpovídající adresou URL.</span><span class="sxs-lookup"><span data-stu-id="42914-148">Generally, routing selects the correct route with URL matching.</span></span> <span data-ttu-id="42914-149">Pokud musíte nastavit vlastnosti směrovacích `Order` tak, aby správně směrovaly požadavky, schéma směrování aplikace je pravděpodobně matoucí pro klienty a křehké k údržbě.</span><span class="sxs-lookup"><span data-stu-id="42914-149">If you must set route `Order` properties to route requests correctly, the app's routing scheme is probably confusing to clients and fragile to maintain.</span></span> <span data-ttu-id="42914-150">Vyhledejte zjednodušené schéma směrování aplikace.</span><span class="sxs-lookup"><span data-stu-id="42914-150">Seek to simplify the app's routing scheme.</span></span> <span data-ttu-id="42914-151">Ukázková aplikace vyžaduje explicitní pořadí zpracování tras, které předvádí několik scénářů směrování pomocí jedné aplikace.</span><span class="sxs-lookup"><span data-stu-id="42914-151">The sample app requires an explicit route processing order to demonstrate several routing scenarios using a single app.</span></span> <span data-ttu-id="42914-152">Měli byste se však pokusit vyhnout se postupu nastavení `Order` trasy v produkčních aplikacích.</span><span class="sxs-lookup"><span data-stu-id="42914-152">However, you should attempt to avoid the practice of setting route `Order` in production apps.</span></span>

<span data-ttu-id="42914-153">Směrování Razor Pages směrování a řadiče MVC sdílí implementaci.</span><span class="sxs-lookup"><span data-stu-id="42914-153">Razor Pages routing and MVC controller routing share an implementation.</span></span> <span data-ttu-id="42914-154">Informace o pořadí směrování v tématech MVC jsou k dispozici při [směrování na akce kontroleru: trasy atributů řazení](xref:mvc/controllers/routing#ordering-attribute-routes).</span><span class="sxs-lookup"><span data-stu-id="42914-154">Information on route order in the MVC topics is available at [Routing to controller actions: Ordering attribute routes](xref:mvc/controllers/routing#ordering-attribute-routes).</span></span>

## <a name="model-conventions"></a><span data-ttu-id="42914-155">Konvence modelu</span><span class="sxs-lookup"><span data-stu-id="42914-155">Model conventions</span></span>

<span data-ttu-id="42914-156">Přidejte delegáta pro <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> pro přidání [konvencí modelu](xref:mvc/controllers/application-model#conventions) , které se vztahují na Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="42914-156">Add a delegate for <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> to add [model conventions](xref:mvc/controllers/application-model#conventions) that apply to Razor Pages.</span></span>

### <a name="add-a-route-model-convention-to-all-pages"></a><span data-ttu-id="42914-157">Přidat konvenci modelu směrování na všechny stránky</span><span class="sxs-lookup"><span data-stu-id="42914-157">Add a route model convention to all pages</span></span>

<span data-ttu-id="42914-158">Pomocí <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> můžete vytvořit a přidat <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> do kolekce instancí <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention>, které se použijí během vytváření modelu směrování stránky.</span><span class="sxs-lookup"><span data-stu-id="42914-158">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page route model construction.</span></span>

<span data-ttu-id="42914-159">Ukázková aplikace přidá šablonu směrování `{globalTemplate?}` na všechny stránky v aplikaci:</span><span class="sxs-lookup"><span data-stu-id="42914-159">The sample app adds a `{globalTemplate?}` route template to all of the pages in the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

<span data-ttu-id="42914-160">Vlastnost <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> pro <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> je nastavena na `1`.</span><span class="sxs-lookup"><span data-stu-id="42914-160">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `1`.</span></span> <span data-ttu-id="42914-161">Tím se zajistí následující chování při shodě trasy v ukázkové aplikaci:</span><span class="sxs-lookup"><span data-stu-id="42914-161">This ensures the following route matching behavior in the sample app:</span></span>

* <span data-ttu-id="42914-162">Šablona směrování pro `TheContactPage/{text?}` se přidá později v tématu.</span><span class="sxs-lookup"><span data-stu-id="42914-162">A route template for `TheContactPage/{text?}` is added later in the topic.</span></span> <span data-ttu-id="42914-163">Trasa stránky kontaktu má výchozí pořadí `null` (`Order = 0`), takže odpovídá před šablonou směrování `{globalTemplate?}`.</span><span class="sxs-lookup"><span data-stu-id="42914-163">The Contact Page route has a default order of `null` (`Order = 0`), so it matches before the `{globalTemplate?}` route template.</span></span>
* <span data-ttu-id="42914-164">Šablona směrování `{aboutTemplate?}` je přidána později v tématu.</span><span class="sxs-lookup"><span data-stu-id="42914-164">An `{aboutTemplate?}` route template is added later in the topic.</span></span> <span data-ttu-id="42914-165">Šabloně `{aboutTemplate?}` je předána `Order` `2`.</span><span class="sxs-lookup"><span data-stu-id="42914-165">The `{aboutTemplate?}` template is given an `Order` of `2`.</span></span> <span data-ttu-id="42914-166">Když se na stránce o aplikaci žádá `/About/RouteDataValue`, načtou se "RouteDataValue" do `RouteData.Values["globalTemplate"]` (`Order = 1`) a není `RouteData.Values["aboutTemplate"]` (`Order = 2`) z důvodu nastavení vlastnosti `Order`.</span><span class="sxs-lookup"><span data-stu-id="42914-166">When the About page is requested at `/About/RouteDataValue`, "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["aboutTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>
* <span data-ttu-id="42914-167">Šablona směrování `{otherPagesTemplate?}` je přidána později v tématu.</span><span class="sxs-lookup"><span data-stu-id="42914-167">An `{otherPagesTemplate?}` route template is added later in the topic.</span></span> <span data-ttu-id="42914-168">Šabloně `{otherPagesTemplate?}` je předána `Order` `2`.</span><span class="sxs-lookup"><span data-stu-id="42914-168">The `{otherPagesTemplate?}` template is given an `Order` of `2`.</span></span> <span data-ttu-id="42914-169">Pokud se u libovolné stránky ve složce *Pages/OtherPages* požaduje parametr trasy (například `/OtherPages/Page1/RouteDataValue`), RouteDataValue se načte do `RouteData.Values["globalTemplate"]` (`Order = 1`) a ne`RouteData.Values["otherPagesTemplate"]` (`Order = 2`) z důvodu nastavení vlastnosti `Order`.</span><span class="sxs-lookup"><span data-stu-id="42914-169">When any page in the *Pages/OtherPages* folder is requested with a route parameter (for example, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="42914-170">Pokud je to možné, nenastavte `Order`, což má za následek `Order = 0`.</span><span class="sxs-lookup"><span data-stu-id="42914-170">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="42914-171">Pro výběr správné trasy se spoléhá na směrování.</span><span class="sxs-lookup"><span data-stu-id="42914-171">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="42914-172">Razor Pages možnosti, jako je přidání <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>, jsou přidány při přidání MVC do kolekce služby v `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="42914-172">Razor Pages options, such as adding <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>, are added when MVC is added to the service collection in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="42914-173">Příklad najdete v [ukázkové aplikaci](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/).</span><span class="sxs-lookup"><span data-stu-id="42914-173">For an example, see the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/).</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="42914-174">Požádejte o stránku ukázky o `localhost:5000/About/GlobalRouteValue` a Prohlédněte si výsledek:</span><span class="sxs-lookup"><span data-stu-id="42914-174">Request the sample's About page at `localhost:5000/About/GlobalRouteValue` and inspect the result:</span></span>

![Stránka o aplikaci je požadována s segmentem směrování GlobalRouteValue.](razor-pages-conventions/_static/about-page-global-template.png)

### <a name="add-an-app-model-convention-to-all-pages"></a><span data-ttu-id="42914-177">Přidání konvence modelu aplikace na všechny stránky</span><span class="sxs-lookup"><span data-stu-id="42914-177">Add an app model convention to all pages</span></span>

<span data-ttu-id="42914-178">Pomocí <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> můžete vytvořit a přidat <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> do kolekce instancí <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention>, které se použijí během vytváření modelu aplikace stránky.</span><span class="sxs-lookup"><span data-stu-id="42914-178">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page app model construction.</span></span>

<span data-ttu-id="42914-179">Chcete-li předvést tuto a další konvenci dále v tématu, ukázková aplikace obsahuje třídu `AddHeaderAttribute`.</span><span class="sxs-lookup"><span data-stu-id="42914-179">To demonstrate this and other conventions later in the topic, the sample app includes an `AddHeaderAttribute` class.</span></span> <span data-ttu-id="42914-180">Konstruktor třídy přijímá `name` řetězec a pole `values` řetězců.</span><span class="sxs-lookup"><span data-stu-id="42914-180">The class constructor accepts a `name` string and a `values` string array.</span></span> <span data-ttu-id="42914-181">Tyto hodnoty se používají ve své `OnResultExecuting` metodě pro nastavení hlavičky odpovědi.</span><span class="sxs-lookup"><span data-stu-id="42914-181">These values are used in its `OnResultExecuting` method to set a response header.</span></span> <span data-ttu-id="42914-182">Úplná třída je uvedena v části [konvence akcí modelu stránky](#page-model-action-conventions) dále v tématu.</span><span class="sxs-lookup"><span data-stu-id="42914-182">The full class is shown in the [Page model action conventions](#page-model-action-conventions) section later in the topic.</span></span>

<span data-ttu-id="42914-183">Ukázková aplikace používá třídu `AddHeaderAttribute` k přidání záhlaví, `GlobalHeader`, na všechny stránky v aplikaci:</span><span class="sxs-lookup"><span data-stu-id="42914-183">The sample app uses the `AddHeaderAttribute` class to add a header, `GlobalHeader`, to all of the pages in the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

<span data-ttu-id="42914-184">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="42914-184">*Startup.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet2)]

<span data-ttu-id="42914-185">Požádejte o stránku ukázek o `localhost:5000/About` a Prohlédněte si záhlaví a podívejte se na výsledek:</span><span class="sxs-lookup"><span data-stu-id="42914-185">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![Hlavičky odpovědi stránky o produktu ukazují, že byl přidán GlobalHeader.](razor-pages-conventions/_static/about-page-global-header.png)

### <a name="add-a-handler-model-convention-to-all-pages"></a><span data-ttu-id="42914-187">Přidat konvenci modelu obslužné rutiny na všechny stránky</span><span class="sxs-lookup"><span data-stu-id="42914-187">Add a handler model convention to all pages</span></span>

<span data-ttu-id="42914-188">Pomocí <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> můžete vytvořit a přidat <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> do kolekce instancí <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention>, které se použijí během vytváření modelu obslužné rutiny stránky.</span><span class="sxs-lookup"><span data-stu-id="42914-188">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page handler model construction.</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalPageHandlerModelConvention.cs?name=snippet1)]

<span data-ttu-id="42914-189">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="42914-189">*Startup.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet10)]

## <a name="page-route-action-conventions"></a><span data-ttu-id="42914-190">Konvence akcí při směrování stránky</span><span class="sxs-lookup"><span data-stu-id="42914-190">Page route action conventions</span></span>

<span data-ttu-id="42914-191">Výchozí zprostředkovatel modelu směrování odvozený od <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> vyvolá konvence, které jsou navržené tak, aby poskytovaly body rozšiřitelnosti pro konfiguraci tras stránky.</span><span class="sxs-lookup"><span data-stu-id="42914-191">The default route model provider that derives from <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> invokes conventions which are designed to provide extensibility points for configuring page routes.</span></span>

### <a name="folder-route-model-convention"></a><span data-ttu-id="42914-192">Konvence modelu směrování složky</span><span class="sxs-lookup"><span data-stu-id="42914-192">Folder route model convention</span></span>

<span data-ttu-id="42914-193">Pomocí <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> můžete vytvořit a přidat <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention>, který vyvolá akci na <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> pro všechny stránky v zadané složce.</span><span class="sxs-lookup"><span data-stu-id="42914-193">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> for all of the pages under the specified folder.</span></span>

<span data-ttu-id="42914-194">Ukázková aplikace používá <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> k přidání šablony trasy `{otherPagesTemplate?}` na stránky ve složce *OtherPages* :</span><span class="sxs-lookup"><span data-stu-id="42914-194">The sample app uses <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> to add an `{otherPagesTemplate?}` route template to the pages in the *OtherPages* folder:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet3)]

<span data-ttu-id="42914-195">Vlastnost <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> pro <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> je nastavena na `2`.</span><span class="sxs-lookup"><span data-stu-id="42914-195">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `2`.</span></span> <span data-ttu-id="42914-196">Tím je zajištěno, že šablona pro `{globalTemplate?}` (nastavená dříve v tématu `1`) má prioritu první pozice hodnoty dat trasy, když je zadána hodnota jedné trasy.</span><span class="sxs-lookup"><span data-stu-id="42914-196">This ensures that the template for `{globalTemplate?}` (set earlier in the topic to `1`) is given priority for the first route data value position when a single route value is provided.</span></span> <span data-ttu-id="42914-197">Pokud je stránka ve složce *Pages/OtherPages* požadována s hodnotou parametru trasy (například `/OtherPages/Page1/RouteDataValue`), je "RouteDataValue" načteno do `RouteData.Values["globalTemplate"]` (`Order = 1`) a není `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) z důvodu nastavení vlastnosti `Order`.</span><span class="sxs-lookup"><span data-stu-id="42914-197">If a page in the *Pages/OtherPages* folder is requested with a route parameter value (for example, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="42914-198">Pokud je to možné, nenastavte `Order`, což má za následek `Order = 0`.</span><span class="sxs-lookup"><span data-stu-id="42914-198">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="42914-199">Pro výběr správné trasy se spoléhá na směrování.</span><span class="sxs-lookup"><span data-stu-id="42914-199">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="42914-200">Vyžádejte si Page1 stránku ukázky na `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` a Prohlédněte si výsledek:</span><span class="sxs-lookup"><span data-stu-id="42914-200">Request the sample's Page1 page at `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` and inspect the result:</span></span>

![Page1 ve složce OtherPages se vyžádá s segmentem směrování GlobalRouteValue a OtherPagesRouteValue.](razor-pages-conventions/_static/otherpages-page1-global-and-otherpages-templates.png)

### <a name="page-route-model-convention"></a><span data-ttu-id="42914-203">Konvence modelu směrování stránky</span><span class="sxs-lookup"><span data-stu-id="42914-203">Page route model convention</span></span>

<span data-ttu-id="42914-204">Pomocí <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> vytvořit a přidat <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention>, který vyvolá akci na <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> pro stránku se zadaným názvem.</span><span class="sxs-lookup"><span data-stu-id="42914-204">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> for the page with the specified name.</span></span>

<span data-ttu-id="42914-205">Ukázková aplikace používá `AddPageRouteModelConvention` k přidání šablony trasy `{aboutTemplate?}` na stránku o aplikaci:</span><span class="sxs-lookup"><span data-stu-id="42914-205">The sample app uses `AddPageRouteModelConvention` to add an `{aboutTemplate?}` route template to the About page:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet4)]

<span data-ttu-id="42914-206">Vlastnost <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> pro <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> je nastavena na `2`.</span><span class="sxs-lookup"><span data-stu-id="42914-206">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `2`.</span></span> <span data-ttu-id="42914-207">Tím je zajištěno, že šablona pro `{globalTemplate?}` (nastavená dříve v tématu `1`) má prioritu první pozice hodnoty dat trasy, když je zadána hodnota jedné trasy.</span><span class="sxs-lookup"><span data-stu-id="42914-207">This ensures that the template for `{globalTemplate?}` (set earlier in the topic to `1`) is given priority for the first route data value position when a single route value is provided.</span></span> <span data-ttu-id="42914-208">Pokud se stránka o žádosti vyžaduje s hodnotou parametru Route na `/About/RouteDataValue`, načte se RouteDataValue do `RouteData.Values["globalTemplate"]` (`Order = 1`) a ne`RouteData.Values["aboutTemplate"]` (`Order = 2`) z důvodu nastavení vlastnosti `Order`.</span><span class="sxs-lookup"><span data-stu-id="42914-208">If the About page is requested with a route parameter value at `/About/RouteDataValue`, "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["aboutTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="42914-209">Pokud je to možné, nenastavte `Order`, což má za následek `Order = 0`.</span><span class="sxs-lookup"><span data-stu-id="42914-209">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="42914-210">Pro výběr správné trasy se spoléhá na směrování.</span><span class="sxs-lookup"><span data-stu-id="42914-210">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="42914-211">Požádejte o stránku ukázky o `localhost:5000/About/GlobalRouteValue/AboutRouteValue` a Prohlédněte si výsledek:</span><span class="sxs-lookup"><span data-stu-id="42914-211">Request the sample's About page at `localhost:5000/About/GlobalRouteValue/AboutRouteValue` and inspect the result:</span></span>

![U segmentů směrování pro GlobalRouteValue a AboutRouteValue se žádá o stránku.](razor-pages-conventions/_static/about-page-global-and-about-templates.png)

## <a name="use-a-parameter-transformer-to-customize-page-routes"></a><span data-ttu-id="42914-214">Použití transformátoru parametrů k přizpůsobení cest stránky</span><span class="sxs-lookup"><span data-stu-id="42914-214">Use a parameter transformer to customize page routes</span></span>

<span data-ttu-id="42914-215">Trasy stránky generované ASP.NET Core lze přizpůsobit pomocí transformátoru parametrů.</span><span class="sxs-lookup"><span data-stu-id="42914-215">Page routes generated by ASP.NET Core can be customized using a parameter transformer.</span></span> <span data-ttu-id="42914-216">Transformátor parametru implementuje `IOutboundParameterTransformer` a transformuje hodnotu parametrů.</span><span class="sxs-lookup"><span data-stu-id="42914-216">A parameter transformer implements `IOutboundParameterTransformer` and transforms the value of parameters.</span></span> <span data-ttu-id="42914-217">Například vlastní parametr `SlugifyParameterTransformer` Transformer změní hodnotu trasy `SubscriptionManagement` na `subscription-management`.</span><span class="sxs-lookup"><span data-stu-id="42914-217">For example, a custom `SlugifyParameterTransformer` parameter transformer changes the `SubscriptionManagement` route value to `subscription-management`.</span></span>

<span data-ttu-id="42914-218">Konvence modelu směrování na stránce `PageRouteTransformerConvention` aplikuje transformátor parametrů na složku a název souboru segmentů automaticky generovaných tras stránky v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="42914-218">The `PageRouteTransformerConvention` page route model convention applies a parameter transformer to the folder and file name segments of automatically generated page routes in an app.</span></span> <span data-ttu-id="42914-219">Například soubor Razor Pages v */Pages/SubscriptionManagement/ViewAll.cshtml* by měl přepsané směrování z `/SubscriptionManagement/ViewAll` na `/subscription-management/view-all`.</span><span class="sxs-lookup"><span data-stu-id="42914-219">For example, the Razor Pages file at */Pages/SubscriptionManagement/ViewAll.cshtml* would have its route rewritten from `/SubscriptionManagement/ViewAll` to `/subscription-management/view-all`.</span></span>

<span data-ttu-id="42914-220">`PageRouteTransformerConvention` pouze transformuje automaticky generované segmenty cesty stránky, které pocházejí ze složky Razor Pages a názvu souboru.</span><span class="sxs-lookup"><span data-stu-id="42914-220">`PageRouteTransformerConvention` only transforms the automatically generated segments of a page route that come from the Razor Pages folder and file name.</span></span> <span data-ttu-id="42914-221">Netransformuje segmenty směrování přidané pomocí direktivy `@page`.</span><span class="sxs-lookup"><span data-stu-id="42914-221">It doesn't transform route segments added with the `@page` directive.</span></span> <span data-ttu-id="42914-222">Konvence také netransformuje trasy přidané pomocí <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>.</span><span class="sxs-lookup"><span data-stu-id="42914-222">The convention also doesn't transform routes added by <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>.</span></span>

<span data-ttu-id="42914-223">`PageRouteTransformerConvention` je v `Startup.ConfigureServices`zaregistrován jako možnost:</span><span class="sxs-lookup"><span data-stu-id="42914-223">The `PageRouteTransformerConvention` is registered as an option in `Startup.ConfigureServices`:</span></span>

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

## <a name="configure-a-page-route"></a><span data-ttu-id="42914-224">Konfigurace trasy stránky</span><span class="sxs-lookup"><span data-stu-id="42914-224">Configure a page route</span></span>

<span data-ttu-id="42914-225">Pomocí <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> můžete nakonfigurovat směrování na stránku na zadané cestě stránky.</span><span class="sxs-lookup"><span data-stu-id="42914-225">Use <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> to configure a route to a page at the specified page path.</span></span> <span data-ttu-id="42914-226">Vygenerované odkazy na stránku používají zadanou trasu.</span><span class="sxs-lookup"><span data-stu-id="42914-226">Generated links to the page use your specified route.</span></span> <span data-ttu-id="42914-227">`AddPageRoute` používá k vytvoření trasy `AddPageRouteModelConvention`.</span><span class="sxs-lookup"><span data-stu-id="42914-227">`AddPageRoute` uses `AddPageRouteModelConvention` to establish the route.</span></span>

<span data-ttu-id="42914-228">Ukázková aplikace vytvoří trasu, která `/TheContactPage` pro *kontakt. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="42914-228">The sample app creates a route to `/TheContactPage` for *Contact.cshtml*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet5)]

<span data-ttu-id="42914-229">Stránku kontaktů lze také získat na `/Contact` prostřednictvím výchozí trasy.</span><span class="sxs-lookup"><span data-stu-id="42914-229">The Contact page can also be reached at `/Contact` via its default route.</span></span>

<span data-ttu-id="42914-230">Vlastní trasa ukázkové aplikace na stránce kontaktů umožňuje, aby byl `text` segment trasy (`{text?}`).</span><span class="sxs-lookup"><span data-stu-id="42914-230">The sample app's custom route to the Contact page allows for an optional `text` route segment (`{text?}`).</span></span> <span data-ttu-id="42914-231">Tato stránka zahrnuje také tento volitelný segment v direktivě `@page` pro případ, že návštěvník přistupuje k této stránce `/Contact` trase:</span><span class="sxs-lookup"><span data-stu-id="42914-231">The page also includes this optional segment in its `@page` directive in case the visitor accesses the page at its `/Contact` route:</span></span>

[!code-cshtml[](razor-pages-conventions/samples/3.x/SampleApp/Pages/Contact.cshtml?highlight=1)]

<span data-ttu-id="42914-232">Všimněte si, že adresa URL vygenerovaná pro odkaz **kontaktu** na vykreslené stránce odráží aktualizovanou trasu:</span><span class="sxs-lookup"><span data-stu-id="42914-232">Note that the URL generated for the **Contact** link in the rendered page reflects the updated route:</span></span>

![Odkaz na ukázkový kontakt aplikace na navigačním panelu](razor-pages-conventions/_static/contact-link.png)

![Kontrola odkazu na kontakt ve vykresleném kódu HTML znamená, že odkaz href je nastaven na hodnotu "/TheContactPage".](razor-pages-conventions/_static/contact-link-source.png)

<span data-ttu-id="42914-235">Navštivte stránku kontaktů buď na své běžné trase, `/Contact`, nebo na vlastní trasu `/TheContactPage`.</span><span class="sxs-lookup"><span data-stu-id="42914-235">Visit the Contact page at either its ordinary route, `/Contact`, or the custom route, `/TheContactPage`.</span></span> <span data-ttu-id="42914-236">Pokud zadáte další segment `text` trasy, stránka zobrazuje segment zakódovaný ve formátu HTML, který zadáte:</span><span class="sxs-lookup"><span data-stu-id="42914-236">If you supply an additional `text` route segment, the page shows the HTML-encoded segment that you provide:</span></span>

![Prohlížeč Edge – příklad poskytnutí volitelného segmentu "text" trasy "TextValue" v adrese URL](razor-pages-conventions/_static/route-segment-with-custom-route.png)

## <a name="page-model-action-conventions"></a><span data-ttu-id="42914-239">Konvence akcí modelu stránky</span><span class="sxs-lookup"><span data-stu-id="42914-239">Page model action conventions</span></span>

<span data-ttu-id="42914-240">Výchozí poskytovatel modelu stránky, který implementuje <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider>, vyvolá konvence, které jsou navrženy tak, aby poskytovaly body rozšiřitelnosti pro konfiguraci modelů stránek.</span><span class="sxs-lookup"><span data-stu-id="42914-240">The default page model provider that implements <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> invokes conventions which are designed to provide extensibility points for configuring page models.</span></span> <span data-ttu-id="42914-241">Tyto konvence jsou užitečné při sestavování a úpravách scénářů zjišťování a zpracování stránky.</span><span class="sxs-lookup"><span data-stu-id="42914-241">These conventions are useful when building and modifying page discovery and processing scenarios.</span></span>

<span data-ttu-id="42914-242">V příkladech v této části používá ukázková aplikace třídu `AddHeaderAttribute`, což je <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>, která používá hlavičku odpovědi:</span><span class="sxs-lookup"><span data-stu-id="42914-242">For the examples in this section, the sample app uses an `AddHeaderAttribute` class, which is a <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>, that applies a response header:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

<span data-ttu-id="42914-243">Pomocí konvence Ukázka ukazuje, jak použít atribut na všechny stránky ve složce a na jednu stránku.</span><span class="sxs-lookup"><span data-stu-id="42914-243">Using conventions, the sample demonstrates how to apply the attribute to all of the pages in a folder and to a single page.</span></span>

<span data-ttu-id="42914-244">**Konvence modelu aplikace složky**</span><span class="sxs-lookup"><span data-stu-id="42914-244">**Folder app model convention**</span></span>

<span data-ttu-id="42914-245">Pomocí <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> můžete vytvořit a přidat <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention>, který vyvolá akci na instancích <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> pro všechny stránky v zadané složce.</span><span class="sxs-lookup"><span data-stu-id="42914-245">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> that invokes an action on <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> instances for all pages under the specified folder.</span></span>

<span data-ttu-id="42914-246">Ukázka demonstruje použití `AddFolderApplicationModelConvention` přidáním hlavičky, `OtherPagesHeader`na stránky ve složce *OtherPages* v aplikaci:</span><span class="sxs-lookup"><span data-stu-id="42914-246">The sample demonstrates the use of `AddFolderApplicationModelConvention` by adding a header, `OtherPagesHeader`, to the pages inside the *OtherPages* folder of the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet6)]

<span data-ttu-id="42914-247">Vyžádejte si Page1 stránku ukázky na `localhost:5000/OtherPages/Page1` a Prohlédněte si hlavičky a podívejte se na výsledek:</span><span class="sxs-lookup"><span data-stu-id="42914-247">Request the sample's Page1 page at `localhost:5000/OtherPages/Page1` and inspect the headers to view the result:</span></span>

![Hlavičky odpovědi stránky OtherPages/Page1 ukazují, že byla přidána OtherPagesHeader.](razor-pages-conventions/_static/page1-otherpages-header.png)

<span data-ttu-id="42914-249">**Konvence modelu aplikace stránky**</span><span class="sxs-lookup"><span data-stu-id="42914-249">**Page app model convention**</span></span>

<span data-ttu-id="42914-250">Pomocí <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> vytvořit a přidat <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention>, který vyvolá akci na <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> pro stránku se zadaným názvem.</span><span class="sxs-lookup"><span data-stu-id="42914-250">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> for the page with the specified name.</span></span>

<span data-ttu-id="42914-251">Ukázka demonstruje použití `AddPageApplicationModelConvention` přidáním hlavičky, `AboutHeader`na stránku o aplikaci:</span><span class="sxs-lookup"><span data-stu-id="42914-251">The sample demonstrates the use of `AddPageApplicationModelConvention` by adding a header, `AboutHeader`, to the About page:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet7)]

<span data-ttu-id="42914-252">Požádejte o stránku ukázek o `localhost:5000/About` a Prohlédněte si záhlaví a podívejte se na výsledek:</span><span class="sxs-lookup"><span data-stu-id="42914-252">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![Hlavičky odpovědi stránky o produktu ukazují, že byl přidán AboutHeader.](razor-pages-conventions/_static/about-page-about-header.png)

<span data-ttu-id="42914-254">**Konfigurace filtru**</span><span class="sxs-lookup"><span data-stu-id="42914-254">**Configure a filter**</span></span>

<span data-ttu-id="42914-255"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> nakonfiguruje zadaný filtr, který se má použít.</span><span class="sxs-lookup"><span data-stu-id="42914-255"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configures the specified filter to apply.</span></span> <span data-ttu-id="42914-256">Můžete implementovat třídu filtru, ale ukázková aplikace ukazuje, jak implementovat filtr ve výrazu lambda, který je implementován na pozadí jako objekt pro vytváření, který vrací filtr:</span><span class="sxs-lookup"><span data-stu-id="42914-256">You can implement a filter class, but the sample app shows how to implement a filter in a lambda expression, which is implemented behind-the-scenes as a factory that returns a filter:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet8)]

<span data-ttu-id="42914-257">Model webové aplikace se používá ke kontrole relativní cesty pro segmenty, které vedou ke stránce Page2 ve složce *OtherPages* .</span><span class="sxs-lookup"><span data-stu-id="42914-257">The page app model is used to check the relative path for segments that lead to the Page2 page in the *OtherPages* folder.</span></span> <span data-ttu-id="42914-258">Pokud podmínka projde, přidá se hlavička.</span><span class="sxs-lookup"><span data-stu-id="42914-258">If the condition passes, a header is added.</span></span> <span data-ttu-id="42914-259">V takovém případě je `EmptyFilter` použito.</span><span class="sxs-lookup"><span data-stu-id="42914-259">If not, the `EmptyFilter` is applied.</span></span>

<span data-ttu-id="42914-260">`EmptyFilter` je [Filtr akcí](xref:mvc/controllers/filters#action-filters).</span><span class="sxs-lookup"><span data-stu-id="42914-260">`EmptyFilter` is an [Action filter](xref:mvc/controllers/filters#action-filters).</span></span> <span data-ttu-id="42914-261">Vzhledem k tomu, že jsou filtry akcí ignorovány Razor Pages, `EmptyFilter` nemá žádný vliv, pokud cesta neobsahuje `OtherPages/Page2`.</span><span class="sxs-lookup"><span data-stu-id="42914-261">Since Action filters are ignored by Razor Pages, the `EmptyFilter` has no effect as intended if the path doesn't contain `OtherPages/Page2`.</span></span>

<span data-ttu-id="42914-262">Vyžádejte si Page2 stránku ukázky na `localhost:5000/OtherPages/Page2` a Prohlédněte si hlavičky a podívejte se na výsledek:</span><span class="sxs-lookup"><span data-stu-id="42914-262">Request the sample's Page2 page at `localhost:5000/OtherPages/Page2` and inspect the headers to view the result:</span></span>

![OtherPagesPage2Header se přidá do odpovědi pro Page2.](razor-pages-conventions/_static/page2-filter-header.png)

<span data-ttu-id="42914-264">**Konfigurace objektu pro vytváření filtrů**</span><span class="sxs-lookup"><span data-stu-id="42914-264">**Configure a filter factory**</span></span>

<span data-ttu-id="42914-265"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> nakonfiguruje určený objekt pro vytváření a použije [filtry](xref:mvc/controllers/filters) na všechny Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="42914-265"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configures the specified factory to apply [filters](xref:mvc/controllers/filters) to all Razor Pages.</span></span>

<span data-ttu-id="42914-266">Ukázková aplikace poskytuje příklad použití [objektu pro vytváření filtru](xref:mvc/controllers/filters#ifilterfactory) přidáním hlavičky, `FilterFactoryHeader`se dvěma hodnotami na stránky aplikace:</span><span class="sxs-lookup"><span data-stu-id="42914-266">The sample app provides an example of using a [filter factory](xref:mvc/controllers/filters#ifilterfactory) by adding a header, `FilterFactoryHeader`, with two values to the app's pages:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet9)]

<span data-ttu-id="42914-267">*AddHeaderWithFactory.cs*:</span><span class="sxs-lookup"><span data-stu-id="42914-267">*AddHeaderWithFactory.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Factories/AddHeaderWithFactory.cs?name=snippet1)]

<span data-ttu-id="42914-268">Požádejte o stránku ukázek o `localhost:5000/About` a Prohlédněte si záhlaví a podívejte se na výsledek:</span><span class="sxs-lookup"><span data-stu-id="42914-268">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![Hlavičky odpovědi stránky o stránku zobrazují, že byly přidány dvě hlavičky FilterFactoryHeader.](razor-pages-conventions/_static/about-page-filter-factory-header.png)

## <a name="mvc-filters-and-the-page-filter-ipagefilter"></a><span data-ttu-id="42914-270">Filtry MVC a filtr stránky (IPageFilter)</span><span class="sxs-lookup"><span data-stu-id="42914-270">MVC Filters and the Page filter (IPageFilter)</span></span>

<span data-ttu-id="42914-271">[Filtry akcí](xref:mvc/controllers/filters#action-filters) MVC se Razor Pages ignorují, protože Razor Pages použít obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="42914-271">MVC [Action filters](xref:mvc/controllers/filters#action-filters) are ignored by Razor Pages, since Razor Pages use handler methods.</span></span> <span data-ttu-id="42914-272">Další typy filtrů MVC jsou k dispozici pro použití: [autorizace](xref:mvc/controllers/filters#authorization-filters), [výjimka](xref:mvc/controllers/filters#exception-filters), [prostředek](xref:mvc/controllers/filters#resource-filters)a [výsledek](xref:mvc/controllers/filters#result-filters).</span><span class="sxs-lookup"><span data-stu-id="42914-272">Other types of MVC filters are available for you to use: [Authorization](xref:mvc/controllers/filters#authorization-filters), [Exception](xref:mvc/controllers/filters#exception-filters), [Resource](xref:mvc/controllers/filters#resource-filters), and [Result](xref:mvc/controllers/filters#result-filters).</span></span> <span data-ttu-id="42914-273">Další informace najdete v tématu [filtry](xref:mvc/controllers/filters) .</span><span class="sxs-lookup"><span data-stu-id="42914-273">For more information, see the [Filters](xref:mvc/controllers/filters) topic.</span></span>

<span data-ttu-id="42914-274">Filtr stránky (<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>) je filtr, který se vztahuje na Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="42914-274">The Page filter (<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>) is a filter that applies to Razor Pages.</span></span> <span data-ttu-id="42914-275">Další informace naleznete v tématu [metody Filter pro Razor Pages](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="42914-275">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="42914-276">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="42914-276">Additional resources</span></span>

* <xref:security/authorization/razor-pages-authorization>
* <xref:mvc/controllers/areas#areas-with-razor-pages>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="42914-277">Naučte se používat [konvence směrování stránky a poskytovatele modelů aplikací](xref:mvc/controllers/application-model#conventions) k řízení směrování, zjišťování a zpracování stránky v aplikacích Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="42914-277">Learn how to use page [route and app model provider conventions](xref:mvc/controllers/application-model#conventions) to control page routing, discovery, and processing in Razor Pages apps.</span></span>

<span data-ttu-id="42914-278">Pokud potřebujete nakonfigurovat vlastní trasy stránky pro jednotlivé stránky, nakonfigurujte směrování na stránky pomocí [AddPageRoute konvence](#configure-a-page-route) popsané dále v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="42914-278">When you need to configure custom page routes for individual pages, configure routing to pages with the [AddPageRoute convention](#configure-a-page-route) described later in this topic.</span></span>

<span data-ttu-id="42914-279">Chcete-li určit trasu stránky, přidat segmenty směrování nebo přidat parametry do trasy, použijte direktivu `@page` stránky.</span><span class="sxs-lookup"><span data-stu-id="42914-279">To specify a page route, add route segments, or add parameters to a route, use the page's `@page` directive.</span></span> <span data-ttu-id="42914-280">Další informace najdete v tématu [vlastní trasy](xref:razor-pages/index#custom-routes).</span><span class="sxs-lookup"><span data-stu-id="42914-280">For more information, see [Custom routes](xref:razor-pages/index#custom-routes).</span></span>

<span data-ttu-id="42914-281">Existují vyhrazená slova, která nelze použít jako segmenty směrování nebo názvy parametrů.</span><span class="sxs-lookup"><span data-stu-id="42914-281">There are reserved words that can't be used as route segments or parameter names.</span></span> <span data-ttu-id="42914-282">Další informace najdete v tématu [směrování: názvy rezervovaných směrování](xref:fundamentals/routing#reserved-routing-names).</span><span class="sxs-lookup"><span data-stu-id="42914-282">For more information, see [Routing: Reserved routing names](xref:fundamentals/routing#reserved-routing-names).</span></span>

<span data-ttu-id="42914-283">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="42914-283">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

| <span data-ttu-id="42914-284">Scénář</span><span class="sxs-lookup"><span data-stu-id="42914-284">Scenario</span></span> | <span data-ttu-id="42914-285">Ukázka znázorňuje...</span><span class="sxs-lookup"><span data-stu-id="42914-285">The sample demonstrates ...</span></span> |
| -------- | --------------------------- |
| [<span data-ttu-id="42914-286">Konvence modelu</span><span class="sxs-lookup"><span data-stu-id="42914-286">Model conventions</span></span>](#model-conventions)<br><br><span data-ttu-id="42914-287">Konvence. Add</span><span class="sxs-lookup"><span data-stu-id="42914-287">Conventions.Add</span></span><ul><li><span data-ttu-id="42914-288">IPageRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="42914-288">IPageRouteModelConvention</span></span></li><li><span data-ttu-id="42914-289">IPageApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="42914-289">IPageApplicationModelConvention</span></span></li><li><span data-ttu-id="42914-290">IPageHandlerModelConvention</span><span class="sxs-lookup"><span data-stu-id="42914-290">IPageHandlerModelConvention</span></span></li></ul> | <span data-ttu-id="42914-291">Přidejte šablonu a hlavičku směrování na stránky aplikace.</span><span class="sxs-lookup"><span data-stu-id="42914-291">Add a route template and header to an app's pages.</span></span> |
| [<span data-ttu-id="42914-292">Konvence akcí při směrování stránky</span><span class="sxs-lookup"><span data-stu-id="42914-292">Page route action conventions</span></span>](#page-route-action-conventions)<ul><li><span data-ttu-id="42914-293">AddFolderRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="42914-293">AddFolderRouteModelConvention</span></span></li><li><span data-ttu-id="42914-294">AddPageRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="42914-294">AddPageRouteModelConvention</span></span></li><li><span data-ttu-id="42914-295">AddPageRoute</span><span class="sxs-lookup"><span data-stu-id="42914-295">AddPageRoute</span></span></li></ul> | <span data-ttu-id="42914-296">Přidejte šablonu směrování do stránek ve složce a na jednu stránku.</span><span class="sxs-lookup"><span data-stu-id="42914-296">Add a route template to pages in a folder and to a single page.</span></span> |
| [<span data-ttu-id="42914-297">Konvence akcí modelu stránky</span><span class="sxs-lookup"><span data-stu-id="42914-297">Page model action conventions</span></span>](#page-model-action-conventions)<ul><li><span data-ttu-id="42914-298">AddFolderApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="42914-298">AddFolderApplicationModelConvention</span></span></li><li><span data-ttu-id="42914-299">AddPageApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="42914-299">AddPageApplicationModelConvention</span></span></li><li><span data-ttu-id="42914-300">ConfigureFilter (filtrovat třídu, lambda výraz nebo objekt pro vytváření filtru)</span><span class="sxs-lookup"><span data-stu-id="42914-300">ConfigureFilter (filter class, lambda expression, or filter factory)</span></span></li></ul> | <span data-ttu-id="42914-301">Umožňuje přidat záhlaví na stránky ve složce, přidat záhlaví na jednu stránku a nakonfigurovat [objekt pro vytváření filtru](xref:mvc/controllers/filters#ifilterfactory) tak, aby přidal hlavičku na stránky aplikace.</span><span class="sxs-lookup"><span data-stu-id="42914-301">Add a header to pages in a folder, add a header to a single page, and configure a [filter factory](xref:mvc/controllers/filters#ifilterfactory) to add a header to an app's pages.</span></span> |

<span data-ttu-id="42914-302">Razor Pages konvence jsou přidány a nakonfigurovány pomocí metody rozšíření <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> pro <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> v kolekci služeb ve třídě `Startup`.</span><span class="sxs-lookup"><span data-stu-id="42914-302">Razor Pages conventions are added and configured using the <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> extension method to <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> on the service collection in the `Startup` class.</span></span> <span data-ttu-id="42914-303">Následující příklady konvence jsou vysvětleny dále v tomto tématu:</span><span class="sxs-lookup"><span data-stu-id="42914-303">The following convention examples are explained later in this topic:</span></span>

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

## <a name="route-order"></a><span data-ttu-id="42914-304">Pořadí směrování</span><span class="sxs-lookup"><span data-stu-id="42914-304">Route order</span></span>

<span data-ttu-id="42914-305">Trasy určují <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> pro zpracování (odpovídá směrování).</span><span class="sxs-lookup"><span data-stu-id="42914-305">Routes specify an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> for processing (route matching).</span></span>

| <span data-ttu-id="42914-306">Objednání</span><span class="sxs-lookup"><span data-stu-id="42914-306">Order</span></span>            | <span data-ttu-id="42914-307">Chování</span><span class="sxs-lookup"><span data-stu-id="42914-307">Behavior</span></span> |
| :--------------: | -------- |
| <span data-ttu-id="42914-308">-1</span><span class="sxs-lookup"><span data-stu-id="42914-308">-1</span></span>               | <span data-ttu-id="42914-309">Trasa je zpracována před zpracováním jiných tras.</span><span class="sxs-lookup"><span data-stu-id="42914-309">The route is processed before other routes are processed.</span></span> |
| <span data-ttu-id="42914-310">0</span><span class="sxs-lookup"><span data-stu-id="42914-310">0</span></span>                | <span data-ttu-id="42914-311">Pořadí není zadáno (výchozí hodnota).</span><span class="sxs-lookup"><span data-stu-id="42914-311">Order isn't specified (default value).</span></span> <span data-ttu-id="42914-312">Při nepřiřazování `Order` (`Order = null`) jsou výchozí hodnoty `Order` trasy na 0 (nula) pro zpracování.</span><span class="sxs-lookup"><span data-stu-id="42914-312">Not assigning `Order` (`Order = null`) defaults the route `Order` to 0 (zero) for processing.</span></span> |
| <span data-ttu-id="42914-313">1, 2, &hellip; n</span><span class="sxs-lookup"><span data-stu-id="42914-313">1, 2, &hellip; n</span></span> | <span data-ttu-id="42914-314">Určuje pořadí zpracování směrování.</span><span class="sxs-lookup"><span data-stu-id="42914-314">Specifies the route processing order.</span></span> |

<span data-ttu-id="42914-315">Zpracování směrování je zřízené podle konvence:</span><span class="sxs-lookup"><span data-stu-id="42914-315">Route processing is established by convention:</span></span>

* <span data-ttu-id="42914-316">Trasy jsou zpracovávány v sekvenčním pořadí (-1, 0, 1, 2, &hellip; n).</span><span class="sxs-lookup"><span data-stu-id="42914-316">Routes are processed in sequential order (-1, 0, 1, 2, &hellip; n).</span></span>
* <span data-ttu-id="42914-317">Když mají stejné trasy stejné `Order`, bude se nejprve shodovat konkrétní trasa, za kterou následuje méně specifických tras.</span><span class="sxs-lookup"><span data-stu-id="42914-317">When routes have the same `Order`, the most specific route is matched first followed by less specific routes.</span></span>
* <span data-ttu-id="42914-318">Když se trasy se stejnou `Order` a stejný počet parametrů shodují s adresou URL požadavku, trasy se zpracovávají v pořadí, v jakém jsou přidány do <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>.</span><span class="sxs-lookup"><span data-stu-id="42914-318">When routes with the same `Order` and the same number of parameters match a request URL, routes are processed in the order that they're added to the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>.</span></span>

<span data-ttu-id="42914-319">Pokud je to možné, vyhněte se v závislosti na zavedeném pořadí zpracování směrování.</span><span class="sxs-lookup"><span data-stu-id="42914-319">If possible, avoid depending on an established route processing order.</span></span> <span data-ttu-id="42914-320">Obecně směrování vybírá správnou trasu s odpovídající adresou URL.</span><span class="sxs-lookup"><span data-stu-id="42914-320">Generally, routing selects the correct route with URL matching.</span></span> <span data-ttu-id="42914-321">Pokud musíte nastavit vlastnosti směrovacích `Order` tak, aby správně směrovaly požadavky, schéma směrování aplikace je pravděpodobně matoucí pro klienty a křehké k údržbě.</span><span class="sxs-lookup"><span data-stu-id="42914-321">If you must set route `Order` properties to route requests correctly, the app's routing scheme is probably confusing to clients and fragile to maintain.</span></span> <span data-ttu-id="42914-322">Vyhledejte zjednodušené schéma směrování aplikace.</span><span class="sxs-lookup"><span data-stu-id="42914-322">Seek to simplify the app's routing scheme.</span></span> <span data-ttu-id="42914-323">Ukázková aplikace vyžaduje explicitní pořadí zpracování tras, které předvádí několik scénářů směrování pomocí jedné aplikace.</span><span class="sxs-lookup"><span data-stu-id="42914-323">The sample app requires an explicit route processing order to demonstrate several routing scenarios using a single app.</span></span> <span data-ttu-id="42914-324">Měli byste se však pokusit vyhnout se postupu nastavení `Order` trasy v produkčních aplikacích.</span><span class="sxs-lookup"><span data-stu-id="42914-324">However, you should attempt to avoid the practice of setting route `Order` in production apps.</span></span>

<span data-ttu-id="42914-325">Směrování Razor Pages směrování a řadiče MVC sdílí implementaci.</span><span class="sxs-lookup"><span data-stu-id="42914-325">Razor Pages routing and MVC controller routing share an implementation.</span></span> <span data-ttu-id="42914-326">Informace o pořadí směrování v tématech MVC jsou k dispozici při [směrování na akce kontroleru: trasy atributů řazení](xref:mvc/controllers/routing#ordering-attribute-routes).</span><span class="sxs-lookup"><span data-stu-id="42914-326">Information on route order in the MVC topics is available at [Routing to controller actions: Ordering attribute routes](xref:mvc/controllers/routing#ordering-attribute-routes).</span></span>

## <a name="model-conventions"></a><span data-ttu-id="42914-327">Konvence modelu</span><span class="sxs-lookup"><span data-stu-id="42914-327">Model conventions</span></span>

<span data-ttu-id="42914-328">Přidejte delegáta pro <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> pro přidání [konvencí modelu](xref:mvc/controllers/application-model#conventions) , které se vztahují na Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="42914-328">Add a delegate for <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> to add [model conventions](xref:mvc/controllers/application-model#conventions) that apply to Razor Pages.</span></span>

### <a name="add-a-route-model-convention-to-all-pages"></a><span data-ttu-id="42914-329">Přidat konvenci modelu směrování na všechny stránky</span><span class="sxs-lookup"><span data-stu-id="42914-329">Add a route model convention to all pages</span></span>

<span data-ttu-id="42914-330">Pomocí <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> můžete vytvořit a přidat <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> do kolekce instancí <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention>, které se použijí během vytváření modelu směrování stránky.</span><span class="sxs-lookup"><span data-stu-id="42914-330">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page route model construction.</span></span>

<span data-ttu-id="42914-331">Ukázková aplikace přidá šablonu směrování `{globalTemplate?}` na všechny stránky v aplikaci:</span><span class="sxs-lookup"><span data-stu-id="42914-331">The sample app adds a `{globalTemplate?}` route template to all of the pages in the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

<span data-ttu-id="42914-332">Vlastnost <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> pro <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> je nastavena na `1`.</span><span class="sxs-lookup"><span data-stu-id="42914-332">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `1`.</span></span> <span data-ttu-id="42914-333">Tím se zajistí následující chování při shodě trasy v ukázkové aplikaci:</span><span class="sxs-lookup"><span data-stu-id="42914-333">This ensures the following route matching behavior in the sample app:</span></span>

* <span data-ttu-id="42914-334">Šablona směrování pro `TheContactPage/{text?}` se přidá později v tématu.</span><span class="sxs-lookup"><span data-stu-id="42914-334">A route template for `TheContactPage/{text?}` is added later in the topic.</span></span> <span data-ttu-id="42914-335">Trasa stránky kontaktu má výchozí pořadí `null` (`Order = 0`), takže odpovídá před šablonou směrování `{globalTemplate?}`.</span><span class="sxs-lookup"><span data-stu-id="42914-335">The Contact Page route has a default order of `null` (`Order = 0`), so it matches before the `{globalTemplate?}` route template.</span></span>
* <span data-ttu-id="42914-336">Šablona směrování `{aboutTemplate?}` je přidána později v tématu.</span><span class="sxs-lookup"><span data-stu-id="42914-336">An `{aboutTemplate?}` route template is added later in the topic.</span></span> <span data-ttu-id="42914-337">Šabloně `{aboutTemplate?}` je předána `Order` `2`.</span><span class="sxs-lookup"><span data-stu-id="42914-337">The `{aboutTemplate?}` template is given an `Order` of `2`.</span></span> <span data-ttu-id="42914-338">Když se na stránce o aplikaci žádá `/About/RouteDataValue`, načtou se "RouteDataValue" do `RouteData.Values["globalTemplate"]` (`Order = 1`) a není `RouteData.Values["aboutTemplate"]` (`Order = 2`) z důvodu nastavení vlastnosti `Order`.</span><span class="sxs-lookup"><span data-stu-id="42914-338">When the About page is requested at `/About/RouteDataValue`, "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["aboutTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>
* <span data-ttu-id="42914-339">Šablona směrování `{otherPagesTemplate?}` je přidána později v tématu.</span><span class="sxs-lookup"><span data-stu-id="42914-339">An `{otherPagesTemplate?}` route template is added later in the topic.</span></span> <span data-ttu-id="42914-340">Šabloně `{otherPagesTemplate?}` je předána `Order` `2`.</span><span class="sxs-lookup"><span data-stu-id="42914-340">The `{otherPagesTemplate?}` template is given an `Order` of `2`.</span></span> <span data-ttu-id="42914-341">Pokud se u libovolné stránky ve složce *Pages/OtherPages* požaduje parametr trasy (například `/OtherPages/Page1/RouteDataValue`), RouteDataValue se načte do `RouteData.Values["globalTemplate"]` (`Order = 1`) a ne`RouteData.Values["otherPagesTemplate"]` (`Order = 2`) z důvodu nastavení vlastnosti `Order`.</span><span class="sxs-lookup"><span data-stu-id="42914-341">When any page in the *Pages/OtherPages* folder is requested with a route parameter (for example, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="42914-342">Pokud je to možné, nenastavte `Order`, což má za následek `Order = 0`.</span><span class="sxs-lookup"><span data-stu-id="42914-342">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="42914-343">Pro výběr správné trasy se spoléhá na směrování.</span><span class="sxs-lookup"><span data-stu-id="42914-343">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="42914-344">Razor Pages možnosti, jako je přidání <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>, jsou přidány při přidání MVC do kolekce služby v `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="42914-344">Razor Pages options, such as adding <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>, are added when MVC is added to the service collection in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="42914-345">Příklad najdete v [ukázkové aplikaci](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/).</span><span class="sxs-lookup"><span data-stu-id="42914-345">For an example, see the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/).</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="42914-346">Požádejte o stránku ukázky o `localhost:5000/About/GlobalRouteValue` a Prohlédněte si výsledek:</span><span class="sxs-lookup"><span data-stu-id="42914-346">Request the sample's About page at `localhost:5000/About/GlobalRouteValue` and inspect the result:</span></span>

![Stránka o aplikaci je požadována s segmentem směrování GlobalRouteValue.](razor-pages-conventions/_static/about-page-global-template.png)

### <a name="add-an-app-model-convention-to-all-pages"></a><span data-ttu-id="42914-349">Přidání konvence modelu aplikace na všechny stránky</span><span class="sxs-lookup"><span data-stu-id="42914-349">Add an app model convention to all pages</span></span>

<span data-ttu-id="42914-350">Pomocí <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> můžete vytvořit a přidat <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> do kolekce instancí <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention>, které se použijí během vytváření modelu aplikace stránky.</span><span class="sxs-lookup"><span data-stu-id="42914-350">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page app model construction.</span></span>

<span data-ttu-id="42914-351">Chcete-li předvést tuto a další konvenci dále v tématu, ukázková aplikace obsahuje třídu `AddHeaderAttribute`.</span><span class="sxs-lookup"><span data-stu-id="42914-351">To demonstrate this and other conventions later in the topic, the sample app includes an `AddHeaderAttribute` class.</span></span> <span data-ttu-id="42914-352">Konstruktor třídy přijímá `name` řetězec a pole `values` řetězců.</span><span class="sxs-lookup"><span data-stu-id="42914-352">The class constructor accepts a `name` string and a `values` string array.</span></span> <span data-ttu-id="42914-353">Tyto hodnoty se používají ve své `OnResultExecuting` metodě pro nastavení hlavičky odpovědi.</span><span class="sxs-lookup"><span data-stu-id="42914-353">These values are used in its `OnResultExecuting` method to set a response header.</span></span> <span data-ttu-id="42914-354">Úplná třída je uvedena v části [konvence akcí modelu stránky](#page-model-action-conventions) dále v tématu.</span><span class="sxs-lookup"><span data-stu-id="42914-354">The full class is shown in the [Page model action conventions](#page-model-action-conventions) section later in the topic.</span></span>

<span data-ttu-id="42914-355">Ukázková aplikace používá třídu `AddHeaderAttribute` k přidání záhlaví, `GlobalHeader`, na všechny stránky v aplikaci:</span><span class="sxs-lookup"><span data-stu-id="42914-355">The sample app uses the `AddHeaderAttribute` class to add a header, `GlobalHeader`, to all of the pages in the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

<span data-ttu-id="42914-356">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="42914-356">*Startup.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

<span data-ttu-id="42914-357">Požádejte o stránku ukázek o `localhost:5000/About` a Prohlédněte si záhlaví a podívejte se na výsledek:</span><span class="sxs-lookup"><span data-stu-id="42914-357">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![Hlavičky odpovědi stránky o produktu ukazují, že byl přidán GlobalHeader.](razor-pages-conventions/_static/about-page-global-header.png)

### <a name="add-a-handler-model-convention-to-all-pages"></a><span data-ttu-id="42914-359">Přidat konvenci modelu obslužné rutiny na všechny stránky</span><span class="sxs-lookup"><span data-stu-id="42914-359">Add a handler model convention to all pages</span></span>

<span data-ttu-id="42914-360">Pomocí <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> můžete vytvořit a přidat <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> do kolekce instancí <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention>, které se použijí během vytváření modelu obslužné rutiny stránky.</span><span class="sxs-lookup"><span data-stu-id="42914-360">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page handler model construction.</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalPageHandlerModelConvention.cs?name=snippet1)]

<span data-ttu-id="42914-361">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="42914-361">*Startup.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet10)]

## <a name="page-route-action-conventions"></a><span data-ttu-id="42914-362">Konvence akcí při směrování stránky</span><span class="sxs-lookup"><span data-stu-id="42914-362">Page route action conventions</span></span>

<span data-ttu-id="42914-363">Výchozí zprostředkovatel modelu směrování odvozený od <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> vyvolá konvence, které jsou navržené tak, aby poskytovaly body rozšiřitelnosti pro konfiguraci tras stránky.</span><span class="sxs-lookup"><span data-stu-id="42914-363">The default route model provider that derives from <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> invokes conventions which are designed to provide extensibility points for configuring page routes.</span></span>

### <a name="folder-route-model-convention"></a><span data-ttu-id="42914-364">Konvence modelu směrování složky</span><span class="sxs-lookup"><span data-stu-id="42914-364">Folder route model convention</span></span>

<span data-ttu-id="42914-365">Pomocí <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> můžete vytvořit a přidat <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention>, který vyvolá akci na <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> pro všechny stránky v zadané složce.</span><span class="sxs-lookup"><span data-stu-id="42914-365">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> for all of the pages under the specified folder.</span></span>

<span data-ttu-id="42914-366">Ukázková aplikace používá <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> k přidání šablony trasy `{otherPagesTemplate?}` na stránky ve složce *OtherPages* :</span><span class="sxs-lookup"><span data-stu-id="42914-366">The sample app uses <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> to add an `{otherPagesTemplate?}` route template to the pages in the *OtherPages* folder:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

<span data-ttu-id="42914-367">Vlastnost <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> pro <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> je nastavena na `2`.</span><span class="sxs-lookup"><span data-stu-id="42914-367">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `2`.</span></span> <span data-ttu-id="42914-368">Tím je zajištěno, že šablona pro `{globalTemplate?}` (nastavená dříve v tématu `1`) má prioritu první pozice hodnoty dat trasy, když je zadána hodnota jedné trasy.</span><span class="sxs-lookup"><span data-stu-id="42914-368">This ensures that the template for `{globalTemplate?}` (set earlier in the topic to `1`) is given priority for the first route data value position when a single route value is provided.</span></span> <span data-ttu-id="42914-369">Pokud je stránka ve složce *Pages/OtherPages* požadována s hodnotou parametru trasy (například `/OtherPages/Page1/RouteDataValue`), je "RouteDataValue" načteno do `RouteData.Values["globalTemplate"]` (`Order = 1`) a není `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) z důvodu nastavení vlastnosti `Order`.</span><span class="sxs-lookup"><span data-stu-id="42914-369">If a page in the *Pages/OtherPages* folder is requested with a route parameter value (for example, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="42914-370">Pokud je to možné, nenastavte `Order`, což má za následek `Order = 0`.</span><span class="sxs-lookup"><span data-stu-id="42914-370">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="42914-371">Pro výběr správné trasy se spoléhá na směrování.</span><span class="sxs-lookup"><span data-stu-id="42914-371">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="42914-372">Vyžádejte si Page1 stránku ukázky na `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` a Prohlédněte si výsledek:</span><span class="sxs-lookup"><span data-stu-id="42914-372">Request the sample's Page1 page at `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` and inspect the result:</span></span>

![Page1 ve složce OtherPages se vyžádá s segmentem směrování GlobalRouteValue a OtherPagesRouteValue.](razor-pages-conventions/_static/otherpages-page1-global-and-otherpages-templates.png)

### <a name="page-route-model-convention"></a><span data-ttu-id="42914-375">Konvence modelu směrování stránky</span><span class="sxs-lookup"><span data-stu-id="42914-375">Page route model convention</span></span>

<span data-ttu-id="42914-376">Pomocí <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> vytvořit a přidat <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention>, který vyvolá akci na <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> pro stránku se zadaným názvem.</span><span class="sxs-lookup"><span data-stu-id="42914-376">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> for the page with the specified name.</span></span>

<span data-ttu-id="42914-377">Ukázková aplikace používá `AddPageRouteModelConvention` k přidání šablony trasy `{aboutTemplate?}` na stránku o aplikaci:</span><span class="sxs-lookup"><span data-stu-id="42914-377">The sample app uses `AddPageRouteModelConvention` to add an `{aboutTemplate?}` route template to the About page:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

<span data-ttu-id="42914-378">Vlastnost <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> pro <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> je nastavena na `2`.</span><span class="sxs-lookup"><span data-stu-id="42914-378">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `2`.</span></span> <span data-ttu-id="42914-379">Tím je zajištěno, že šablona pro `{globalTemplate?}` (nastavená dříve v tématu `1`) má prioritu první pozice hodnoty dat trasy, když je zadána hodnota jedné trasy.</span><span class="sxs-lookup"><span data-stu-id="42914-379">This ensures that the template for `{globalTemplate?}` (set earlier in the topic to `1`) is given priority for the first route data value position when a single route value is provided.</span></span> <span data-ttu-id="42914-380">Pokud se stránka o žádosti vyžaduje s hodnotou parametru Route na `/About/RouteDataValue`, načte se RouteDataValue do `RouteData.Values["globalTemplate"]` (`Order = 1`) a ne`RouteData.Values["aboutTemplate"]` (`Order = 2`) z důvodu nastavení vlastnosti `Order`.</span><span class="sxs-lookup"><span data-stu-id="42914-380">If the About page is requested with a route parameter value at `/About/RouteDataValue`, "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["aboutTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="42914-381">Pokud je to možné, nenastavte `Order`, což má za následek `Order = 0`.</span><span class="sxs-lookup"><span data-stu-id="42914-381">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="42914-382">Pro výběr správné trasy se spoléhá na směrování.</span><span class="sxs-lookup"><span data-stu-id="42914-382">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="42914-383">Požádejte o stránku ukázky o `localhost:5000/About/GlobalRouteValue/AboutRouteValue` a Prohlédněte si výsledek:</span><span class="sxs-lookup"><span data-stu-id="42914-383">Request the sample's About page at `localhost:5000/About/GlobalRouteValue/AboutRouteValue` and inspect the result:</span></span>

![U segmentů směrování pro GlobalRouteValue a AboutRouteValue se žádá o stránku.](razor-pages-conventions/_static/about-page-global-and-about-templates.png)

## <a name="use-a-parameter-transformer-to-customize-page-routes"></a><span data-ttu-id="42914-386">Použití transformátoru parametrů k přizpůsobení cest stránky</span><span class="sxs-lookup"><span data-stu-id="42914-386">Use a parameter transformer to customize page routes</span></span>

<span data-ttu-id="42914-387">Trasy stránky generované ASP.NET Core lze přizpůsobit pomocí transformátoru parametrů.</span><span class="sxs-lookup"><span data-stu-id="42914-387">Page routes generated by ASP.NET Core can be customized using a parameter transformer.</span></span> <span data-ttu-id="42914-388">Transformátor parametru implementuje `IOutboundParameterTransformer` a transformuje hodnotu parametrů.</span><span class="sxs-lookup"><span data-stu-id="42914-388">A parameter transformer implements `IOutboundParameterTransformer` and transforms the value of parameters.</span></span> <span data-ttu-id="42914-389">Například vlastní parametr `SlugifyParameterTransformer` Transformer změní hodnotu trasy `SubscriptionManagement` na `subscription-management`.</span><span class="sxs-lookup"><span data-stu-id="42914-389">For example, a custom `SlugifyParameterTransformer` parameter transformer changes the `SubscriptionManagement` route value to `subscription-management`.</span></span>

<span data-ttu-id="42914-390">Konvence modelu směrování na stránce `PageRouteTransformerConvention` aplikuje transformátor parametrů na složku a název souboru segmentů automaticky generovaných tras stránky v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="42914-390">The `PageRouteTransformerConvention` page route model convention applies a parameter transformer to the folder and file name segments of automatically generated page routes in an app.</span></span> <span data-ttu-id="42914-391">Například soubor Razor Pages v */Pages/SubscriptionManagement/ViewAll.cshtml* by měl přepsané směrování z `/SubscriptionManagement/ViewAll` na `/subscription-management/view-all`.</span><span class="sxs-lookup"><span data-stu-id="42914-391">For example, the Razor Pages file at */Pages/SubscriptionManagement/ViewAll.cshtml* would have its route rewritten from `/SubscriptionManagement/ViewAll` to `/subscription-management/view-all`.</span></span>

<span data-ttu-id="42914-392">`PageRouteTransformerConvention` pouze transformuje automaticky generované segmenty cesty stránky, které pocházejí ze složky Razor Pages a názvu souboru.</span><span class="sxs-lookup"><span data-stu-id="42914-392">`PageRouteTransformerConvention` only transforms the automatically generated segments of a page route that come from the Razor Pages folder and file name.</span></span> <span data-ttu-id="42914-393">Netransformuje segmenty směrování přidané pomocí direktivy `@page`.</span><span class="sxs-lookup"><span data-stu-id="42914-393">It doesn't transform route segments added with the `@page` directive.</span></span> <span data-ttu-id="42914-394">Konvence také netransformuje trasy přidané pomocí <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>.</span><span class="sxs-lookup"><span data-stu-id="42914-394">The convention also doesn't transform routes added by <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>.</span></span>

<span data-ttu-id="42914-395">`PageRouteTransformerConvention` je v `Startup.ConfigureServices`zaregistrován jako možnost:</span><span class="sxs-lookup"><span data-stu-id="42914-395">The `PageRouteTransformerConvention` is registered as an option in `Startup.ConfigureServices`:</span></span>

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

## <a name="configure-a-page-route"></a><span data-ttu-id="42914-396">Konfigurace trasy stránky</span><span class="sxs-lookup"><span data-stu-id="42914-396">Configure a page route</span></span>

<span data-ttu-id="42914-397">Pomocí <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> můžete nakonfigurovat směrování na stránku na zadané cestě stránky.</span><span class="sxs-lookup"><span data-stu-id="42914-397">Use <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> to configure a route to a page at the specified page path.</span></span> <span data-ttu-id="42914-398">Vygenerované odkazy na stránku používají zadanou trasu.</span><span class="sxs-lookup"><span data-stu-id="42914-398">Generated links to the page use your specified route.</span></span> <span data-ttu-id="42914-399">`AddPageRoute` používá k vytvoření trasy `AddPageRouteModelConvention`.</span><span class="sxs-lookup"><span data-stu-id="42914-399">`AddPageRoute` uses `AddPageRouteModelConvention` to establish the route.</span></span>

<span data-ttu-id="42914-400">Ukázková aplikace vytvoří trasu, která `/TheContactPage` pro *kontakt. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="42914-400">The sample app creates a route to `/TheContactPage` for *Contact.cshtml*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet5)]

<span data-ttu-id="42914-401">Stránku kontaktů lze také získat na `/Contact` prostřednictvím výchozí trasy.</span><span class="sxs-lookup"><span data-stu-id="42914-401">The Contact page can also be reached at `/Contact` via its default route.</span></span>

<span data-ttu-id="42914-402">Vlastní trasa ukázkové aplikace na stránce kontaktů umožňuje, aby byl `text` segment trasy (`{text?}`).</span><span class="sxs-lookup"><span data-stu-id="42914-402">The sample app's custom route to the Contact page allows for an optional `text` route segment (`{text?}`).</span></span> <span data-ttu-id="42914-403">Tato stránka zahrnuje také tento volitelný segment v direktivě `@page` pro případ, že návštěvník přistupuje k této stránce `/Contact` trase:</span><span class="sxs-lookup"><span data-stu-id="42914-403">The page also includes this optional segment in its `@page` directive in case the visitor accesses the page at its `/Contact` route:</span></span>

[!code-cshtml[](razor-pages-conventions/samples/2.x/SampleApp/Pages/Contact.cshtml?highlight=1)]

<span data-ttu-id="42914-404">Všimněte si, že adresa URL vygenerovaná pro odkaz **kontaktu** na vykreslené stránce odráží aktualizovanou trasu:</span><span class="sxs-lookup"><span data-stu-id="42914-404">Note that the URL generated for the **Contact** link in the rendered page reflects the updated route:</span></span>

![Odkaz na ukázkový kontakt aplikace na navigačním panelu](razor-pages-conventions/_static/contact-link.png)

![Kontrola odkazu na kontakt ve vykresleném kódu HTML znamená, že odkaz href je nastaven na hodnotu "/TheContactPage".](razor-pages-conventions/_static/contact-link-source.png)

<span data-ttu-id="42914-407">Navštivte stránku kontaktů buď na své běžné trase, `/Contact`, nebo na vlastní trasu `/TheContactPage`.</span><span class="sxs-lookup"><span data-stu-id="42914-407">Visit the Contact page at either its ordinary route, `/Contact`, or the custom route, `/TheContactPage`.</span></span> <span data-ttu-id="42914-408">Pokud zadáte další segment `text` trasy, stránka zobrazuje segment zakódovaný ve formátu HTML, který zadáte:</span><span class="sxs-lookup"><span data-stu-id="42914-408">If you supply an additional `text` route segment, the page shows the HTML-encoded segment that you provide:</span></span>

![Prohlížeč Edge – příklad poskytnutí volitelného segmentu "text" trasy "TextValue" v adrese URL](razor-pages-conventions/_static/route-segment-with-custom-route.png)

## <a name="page-model-action-conventions"></a><span data-ttu-id="42914-411">Konvence akcí modelu stránky</span><span class="sxs-lookup"><span data-stu-id="42914-411">Page model action conventions</span></span>

<span data-ttu-id="42914-412">Výchozí poskytovatel modelu stránky, který implementuje <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider>, vyvolá konvence, které jsou navrženy tak, aby poskytovaly body rozšiřitelnosti pro konfiguraci modelů stránek.</span><span class="sxs-lookup"><span data-stu-id="42914-412">The default page model provider that implements <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> invokes conventions which are designed to provide extensibility points for configuring page models.</span></span> <span data-ttu-id="42914-413">Tyto konvence jsou užitečné při sestavování a úpravách scénářů zjišťování a zpracování stránky.</span><span class="sxs-lookup"><span data-stu-id="42914-413">These conventions are useful when building and modifying page discovery and processing scenarios.</span></span>

<span data-ttu-id="42914-414">V příkladech v této části používá ukázková aplikace třídu `AddHeaderAttribute`, což je <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>, která používá hlavičku odpovědi:</span><span class="sxs-lookup"><span data-stu-id="42914-414">For the examples in this section, the sample app uses an `AddHeaderAttribute` class, which is a <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>, that applies a response header:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

<span data-ttu-id="42914-415">Pomocí konvence Ukázka ukazuje, jak použít atribut na všechny stránky ve složce a na jednu stránku.</span><span class="sxs-lookup"><span data-stu-id="42914-415">Using conventions, the sample demonstrates how to apply the attribute to all of the pages in a folder and to a single page.</span></span>

<span data-ttu-id="42914-416">**Konvence modelu aplikace složky**</span><span class="sxs-lookup"><span data-stu-id="42914-416">**Folder app model convention**</span></span>

<span data-ttu-id="42914-417">Pomocí <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> můžete vytvořit a přidat <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention>, který vyvolá akci na instancích <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> pro všechny stránky v zadané složce.</span><span class="sxs-lookup"><span data-stu-id="42914-417">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> that invokes an action on <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> instances for all pages under the specified folder.</span></span>

<span data-ttu-id="42914-418">Ukázka demonstruje použití `AddFolderApplicationModelConvention` přidáním hlavičky, `OtherPagesHeader`na stránky ve složce *OtherPages* v aplikaci:</span><span class="sxs-lookup"><span data-stu-id="42914-418">The sample demonstrates the use of `AddFolderApplicationModelConvention` by adding a header, `OtherPagesHeader`, to the pages inside the *OtherPages* folder of the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet6)]

<span data-ttu-id="42914-419">Vyžádejte si Page1 stránku ukázky na `localhost:5000/OtherPages/Page1` a Prohlédněte si hlavičky a podívejte se na výsledek:</span><span class="sxs-lookup"><span data-stu-id="42914-419">Request the sample's Page1 page at `localhost:5000/OtherPages/Page1` and inspect the headers to view the result:</span></span>

![Hlavičky odpovědi stránky OtherPages/Page1 ukazují, že byla přidána OtherPagesHeader.](razor-pages-conventions/_static/page1-otherpages-header.png)

<span data-ttu-id="42914-421">**Konvence modelu aplikace stránky**</span><span class="sxs-lookup"><span data-stu-id="42914-421">**Page app model convention**</span></span>

<span data-ttu-id="42914-422">Pomocí <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> vytvořit a přidat <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention>, který vyvolá akci na <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> pro stránku se zadaným názvem.</span><span class="sxs-lookup"><span data-stu-id="42914-422">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> for the page with the specified name.</span></span>

<span data-ttu-id="42914-423">Ukázka demonstruje použití `AddPageApplicationModelConvention` přidáním hlavičky, `AboutHeader`na stránku o aplikaci:</span><span class="sxs-lookup"><span data-stu-id="42914-423">The sample demonstrates the use of `AddPageApplicationModelConvention` by adding a header, `AboutHeader`, to the About page:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet7)]

<span data-ttu-id="42914-424">Požádejte o stránku ukázek o `localhost:5000/About` a Prohlédněte si záhlaví a podívejte se na výsledek:</span><span class="sxs-lookup"><span data-stu-id="42914-424">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![Hlavičky odpovědi stránky o produktu ukazují, že byl přidán AboutHeader.](razor-pages-conventions/_static/about-page-about-header.png)

<span data-ttu-id="42914-426">**Konfigurace filtru**</span><span class="sxs-lookup"><span data-stu-id="42914-426">**Configure a filter**</span></span>

<span data-ttu-id="42914-427"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> nakonfiguruje zadaný filtr, který se má použít.</span><span class="sxs-lookup"><span data-stu-id="42914-427"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configures the specified filter to apply.</span></span> <span data-ttu-id="42914-428">Můžete implementovat třídu filtru, ale ukázková aplikace ukazuje, jak implementovat filtr ve výrazu lambda, který je implementován na pozadí jako objekt pro vytváření, který vrací filtr:</span><span class="sxs-lookup"><span data-stu-id="42914-428">You can implement a filter class, but the sample app shows how to implement a filter in a lambda expression, which is implemented behind-the-scenes as a factory that returns a filter:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet8)]

<span data-ttu-id="42914-429">Model webové aplikace se používá ke kontrole relativní cesty pro segmenty, které vedou ke stránce Page2 ve složce *OtherPages* .</span><span class="sxs-lookup"><span data-stu-id="42914-429">The page app model is used to check the relative path for segments that lead to the Page2 page in the *OtherPages* folder.</span></span> <span data-ttu-id="42914-430">Pokud podmínka projde, přidá se hlavička.</span><span class="sxs-lookup"><span data-stu-id="42914-430">If the condition passes, a header is added.</span></span> <span data-ttu-id="42914-431">V takovém případě je `EmptyFilter` použito.</span><span class="sxs-lookup"><span data-stu-id="42914-431">If not, the `EmptyFilter` is applied.</span></span>

<span data-ttu-id="42914-432">`EmptyFilter` je [Filtr akcí](xref:mvc/controllers/filters#action-filters).</span><span class="sxs-lookup"><span data-stu-id="42914-432">`EmptyFilter` is an [Action filter](xref:mvc/controllers/filters#action-filters).</span></span> <span data-ttu-id="42914-433">Vzhledem k tomu, že jsou filtry akcí ignorovány Razor Pages, `EmptyFilter` nemá žádný vliv, pokud cesta neobsahuje `OtherPages/Page2`.</span><span class="sxs-lookup"><span data-stu-id="42914-433">Since Action filters are ignored by Razor Pages, the `EmptyFilter` has no effect as intended if the path doesn't contain `OtherPages/Page2`.</span></span>

<span data-ttu-id="42914-434">Vyžádejte si Page2 stránku ukázky na `localhost:5000/OtherPages/Page2` a Prohlédněte si hlavičky a podívejte se na výsledek:</span><span class="sxs-lookup"><span data-stu-id="42914-434">Request the sample's Page2 page at `localhost:5000/OtherPages/Page2` and inspect the headers to view the result:</span></span>

![OtherPagesPage2Header se přidá do odpovědi pro Page2.](razor-pages-conventions/_static/page2-filter-header.png)

<span data-ttu-id="42914-436">**Konfigurace objektu pro vytváření filtrů**</span><span class="sxs-lookup"><span data-stu-id="42914-436">**Configure a filter factory**</span></span>

<span data-ttu-id="42914-437"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> nakonfiguruje určený objekt pro vytváření a použije [filtry](xref:mvc/controllers/filters) na všechny Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="42914-437"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configures the specified factory to apply [filters](xref:mvc/controllers/filters) to all Razor Pages.</span></span>

<span data-ttu-id="42914-438">Ukázková aplikace poskytuje příklad použití [objektu pro vytváření filtru](xref:mvc/controllers/filters#ifilterfactory) přidáním hlavičky, `FilterFactoryHeader`se dvěma hodnotami na stránky aplikace:</span><span class="sxs-lookup"><span data-stu-id="42914-438">The sample app provides an example of using a [filter factory](xref:mvc/controllers/filters#ifilterfactory) by adding a header, `FilterFactoryHeader`, with two values to the app's pages:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet9)]

<span data-ttu-id="42914-439">*AddHeaderWithFactory.cs*:</span><span class="sxs-lookup"><span data-stu-id="42914-439">*AddHeaderWithFactory.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Factories/AddHeaderWithFactory.cs?name=snippet1)]

<span data-ttu-id="42914-440">Požádejte o stránku ukázek o `localhost:5000/About` a Prohlédněte si záhlaví a podívejte se na výsledek:</span><span class="sxs-lookup"><span data-stu-id="42914-440">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![Hlavičky odpovědi stránky o stránku zobrazují, že byly přidány dvě hlavičky FilterFactoryHeader.](razor-pages-conventions/_static/about-page-filter-factory-header.png)

## <a name="mvc-filters-and-the-page-filter-ipagefilter"></a><span data-ttu-id="42914-442">Filtry MVC a filtr stránky (IPageFilter)</span><span class="sxs-lookup"><span data-stu-id="42914-442">MVC Filters and the Page filter (IPageFilter)</span></span>

<span data-ttu-id="42914-443">[Filtry akcí](xref:mvc/controllers/filters#action-filters) MVC se Razor Pages ignorují, protože Razor Pages použít obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="42914-443">MVC [Action filters](xref:mvc/controllers/filters#action-filters) are ignored by Razor Pages, since Razor Pages use handler methods.</span></span> <span data-ttu-id="42914-444">Další typy filtrů MVC jsou k dispozici pro použití: [autorizace](xref:mvc/controllers/filters#authorization-filters), [výjimka](xref:mvc/controllers/filters#exception-filters), [prostředek](xref:mvc/controllers/filters#resource-filters)a [výsledek](xref:mvc/controllers/filters#result-filters).</span><span class="sxs-lookup"><span data-stu-id="42914-444">Other types of MVC filters are available for you to use: [Authorization](xref:mvc/controllers/filters#authorization-filters), [Exception](xref:mvc/controllers/filters#exception-filters), [Resource](xref:mvc/controllers/filters#resource-filters), and [Result](xref:mvc/controllers/filters#result-filters).</span></span> <span data-ttu-id="42914-445">Další informace najdete v tématu [filtry](xref:mvc/controllers/filters) .</span><span class="sxs-lookup"><span data-stu-id="42914-445">For more information, see the [Filters](xref:mvc/controllers/filters) topic.</span></span>

<span data-ttu-id="42914-446">Filtr stránky (<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>) je filtr, který se vztahuje na Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="42914-446">The Page filter (<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>) is a filter that applies to Razor Pages.</span></span> <span data-ttu-id="42914-447">Další informace naleznete v tématu [metody Filter pro Razor Pages](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="42914-447">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="42914-448">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="42914-448">Additional resources</span></span>

* <xref:security/authorization/razor-pages-authorization>
* <xref:mvc/controllers/areas#areas-with-razor-pages>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="42914-449">Naučte se používat [konvence směrování stránky a poskytovatele modelů aplikací](xref:mvc/controllers/application-model#conventions) k řízení směrování, zjišťování a zpracování stránky v aplikacích Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="42914-449">Learn how to use page [route and app model provider conventions](xref:mvc/controllers/application-model#conventions) to control page routing, discovery, and processing in Razor Pages apps.</span></span>

<span data-ttu-id="42914-450">Pokud potřebujete nakonfigurovat vlastní trasy stránky pro jednotlivé stránky, nakonfigurujte směrování na stránky pomocí [AddPageRoute konvence](#configure-a-page-route) popsané dále v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="42914-450">When you need to configure custom page routes for individual pages, configure routing to pages with the [AddPageRoute convention](#configure-a-page-route) described later in this topic.</span></span>

<span data-ttu-id="42914-451">Chcete-li určit trasu stránky, přidat segmenty směrování nebo přidat parametry do trasy, použijte direktivu `@page` stránky.</span><span class="sxs-lookup"><span data-stu-id="42914-451">To specify a page route, add route segments, or add parameters to a route, use the page's `@page` directive.</span></span> <span data-ttu-id="42914-452">Další informace najdete v tématu [vlastní trasy](xref:razor-pages/index#custom-routes).</span><span class="sxs-lookup"><span data-stu-id="42914-452">For more information, see [Custom routes](xref:razor-pages/index#custom-routes).</span></span>

<span data-ttu-id="42914-453">Existují vyhrazená slova, která nelze použít jako segmenty směrování nebo názvy parametrů.</span><span class="sxs-lookup"><span data-stu-id="42914-453">There are reserved words that can't be used as route segments or parameter names.</span></span> <span data-ttu-id="42914-454">Další informace najdete v tématu [směrování: názvy rezervovaných směrování](xref:fundamentals/routing#reserved-routing-names).</span><span class="sxs-lookup"><span data-stu-id="42914-454">For more information, see [Routing: Reserved routing names](xref:fundamentals/routing#reserved-routing-names).</span></span>

<span data-ttu-id="42914-455">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="42914-455">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

| <span data-ttu-id="42914-456">Scénář</span><span class="sxs-lookup"><span data-stu-id="42914-456">Scenario</span></span> | <span data-ttu-id="42914-457">Ukázka znázorňuje...</span><span class="sxs-lookup"><span data-stu-id="42914-457">The sample demonstrates ...</span></span> |
| -------- | --------------------------- |
| [<span data-ttu-id="42914-458">Konvence modelu</span><span class="sxs-lookup"><span data-stu-id="42914-458">Model conventions</span></span>](#model-conventions)<br><br><span data-ttu-id="42914-459">Konvence. Add</span><span class="sxs-lookup"><span data-stu-id="42914-459">Conventions.Add</span></span><ul><li><span data-ttu-id="42914-460">IPageRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="42914-460">IPageRouteModelConvention</span></span></li><li><span data-ttu-id="42914-461">IPageApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="42914-461">IPageApplicationModelConvention</span></span></li><li><span data-ttu-id="42914-462">IPageHandlerModelConvention</span><span class="sxs-lookup"><span data-stu-id="42914-462">IPageHandlerModelConvention</span></span></li></ul> | <span data-ttu-id="42914-463">Přidejte šablonu a hlavičku směrování na stránky aplikace.</span><span class="sxs-lookup"><span data-stu-id="42914-463">Add a route template and header to an app's pages.</span></span> |
| [<span data-ttu-id="42914-464">Konvence akcí při směrování stránky</span><span class="sxs-lookup"><span data-stu-id="42914-464">Page route action conventions</span></span>](#page-route-action-conventions)<ul><li><span data-ttu-id="42914-465">AddFolderRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="42914-465">AddFolderRouteModelConvention</span></span></li><li><span data-ttu-id="42914-466">AddPageRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="42914-466">AddPageRouteModelConvention</span></span></li><li><span data-ttu-id="42914-467">AddPageRoute</span><span class="sxs-lookup"><span data-stu-id="42914-467">AddPageRoute</span></span></li></ul> | <span data-ttu-id="42914-468">Přidejte šablonu směrování do stránek ve složce a na jednu stránku.</span><span class="sxs-lookup"><span data-stu-id="42914-468">Add a route template to pages in a folder and to a single page.</span></span> |
| [<span data-ttu-id="42914-469">Konvence akcí modelu stránky</span><span class="sxs-lookup"><span data-stu-id="42914-469">Page model action conventions</span></span>](#page-model-action-conventions)<ul><li><span data-ttu-id="42914-470">AddFolderApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="42914-470">AddFolderApplicationModelConvention</span></span></li><li><span data-ttu-id="42914-471">AddPageApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="42914-471">AddPageApplicationModelConvention</span></span></li><li><span data-ttu-id="42914-472">ConfigureFilter (filtrovat třídu, lambda výraz nebo objekt pro vytváření filtru)</span><span class="sxs-lookup"><span data-stu-id="42914-472">ConfigureFilter (filter class, lambda expression, or filter factory)</span></span></li></ul> | <span data-ttu-id="42914-473">Umožňuje přidat záhlaví na stránky ve složce, přidat záhlaví na jednu stránku a nakonfigurovat [objekt pro vytváření filtru](xref:mvc/controllers/filters#ifilterfactory) tak, aby přidal hlavičku na stránky aplikace.</span><span class="sxs-lookup"><span data-stu-id="42914-473">Add a header to pages in a folder, add a header to a single page, and configure a [filter factory](xref:mvc/controllers/filters#ifilterfactory) to add a header to an app's pages.</span></span> |

<span data-ttu-id="42914-474">Razor Pages konvence jsou přidány a nakonfigurovány pomocí metody rozšíření <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> pro <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> v kolekci služeb ve třídě `Startup`.</span><span class="sxs-lookup"><span data-stu-id="42914-474">Razor Pages conventions are added and configured using the <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> extension method to <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> on the service collection in the `Startup` class.</span></span> <span data-ttu-id="42914-475">Následující příklady konvence jsou vysvětleny dále v tomto tématu:</span><span class="sxs-lookup"><span data-stu-id="42914-475">The following convention examples are explained later in this topic:</span></span>

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

## <a name="route-order"></a><span data-ttu-id="42914-476">Pořadí směrování</span><span class="sxs-lookup"><span data-stu-id="42914-476">Route order</span></span>

<span data-ttu-id="42914-477">Trasy určují <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> pro zpracování (odpovídá směrování).</span><span class="sxs-lookup"><span data-stu-id="42914-477">Routes specify an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> for processing (route matching).</span></span>

| <span data-ttu-id="42914-478">Objednání</span><span class="sxs-lookup"><span data-stu-id="42914-478">Order</span></span>            | <span data-ttu-id="42914-479">Chování</span><span class="sxs-lookup"><span data-stu-id="42914-479">Behavior</span></span> |
| :--------------: | -------- |
| <span data-ttu-id="42914-480">-1</span><span class="sxs-lookup"><span data-stu-id="42914-480">-1</span></span>               | <span data-ttu-id="42914-481">Trasa je zpracována před zpracováním jiných tras.</span><span class="sxs-lookup"><span data-stu-id="42914-481">The route is processed before other routes are processed.</span></span> |
| <span data-ttu-id="42914-482">0</span><span class="sxs-lookup"><span data-stu-id="42914-482">0</span></span>                | <span data-ttu-id="42914-483">Pořadí není zadáno (výchozí hodnota).</span><span class="sxs-lookup"><span data-stu-id="42914-483">Order isn't specified (default value).</span></span> <span data-ttu-id="42914-484">Při nepřiřazování `Order` (`Order = null`) jsou výchozí hodnoty `Order` trasy na 0 (nula) pro zpracování.</span><span class="sxs-lookup"><span data-stu-id="42914-484">Not assigning `Order` (`Order = null`) defaults the route `Order` to 0 (zero) for processing.</span></span> |
| <span data-ttu-id="42914-485">1, 2, &hellip; n</span><span class="sxs-lookup"><span data-stu-id="42914-485">1, 2, &hellip; n</span></span> | <span data-ttu-id="42914-486">Určuje pořadí zpracování směrování.</span><span class="sxs-lookup"><span data-stu-id="42914-486">Specifies the route processing order.</span></span> |

<span data-ttu-id="42914-487">Zpracování směrování je zřízené podle konvence:</span><span class="sxs-lookup"><span data-stu-id="42914-487">Route processing is established by convention:</span></span>

* <span data-ttu-id="42914-488">Trasy jsou zpracovávány v sekvenčním pořadí (-1, 0, 1, 2, &hellip; n).</span><span class="sxs-lookup"><span data-stu-id="42914-488">Routes are processed in sequential order (-1, 0, 1, 2, &hellip; n).</span></span>
* <span data-ttu-id="42914-489">Když mají stejné trasy stejné `Order`, bude se nejprve shodovat konkrétní trasa, za kterou následuje méně specifických tras.</span><span class="sxs-lookup"><span data-stu-id="42914-489">When routes have the same `Order`, the most specific route is matched first followed by less specific routes.</span></span>
* <span data-ttu-id="42914-490">Když se trasy se stejnou `Order` a stejný počet parametrů shodují s adresou URL požadavku, trasy se zpracovávají v pořadí, v jakém jsou přidány do <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>.</span><span class="sxs-lookup"><span data-stu-id="42914-490">When routes with the same `Order` and the same number of parameters match a request URL, routes are processed in the order that they're added to the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>.</span></span>

<span data-ttu-id="42914-491">Pokud je to možné, vyhněte se v závislosti na zavedeném pořadí zpracování směrování.</span><span class="sxs-lookup"><span data-stu-id="42914-491">If possible, avoid depending on an established route processing order.</span></span> <span data-ttu-id="42914-492">Obecně směrování vybírá správnou trasu s odpovídající adresou URL.</span><span class="sxs-lookup"><span data-stu-id="42914-492">Generally, routing selects the correct route with URL matching.</span></span> <span data-ttu-id="42914-493">Pokud musíte nastavit vlastnosti směrovacích `Order` tak, aby správně směrovaly požadavky, schéma směrování aplikace je pravděpodobně matoucí pro klienty a křehké k údržbě.</span><span class="sxs-lookup"><span data-stu-id="42914-493">If you must set route `Order` properties to route requests correctly, the app's routing scheme is probably confusing to clients and fragile to maintain.</span></span> <span data-ttu-id="42914-494">Vyhledejte zjednodušené schéma směrování aplikace.</span><span class="sxs-lookup"><span data-stu-id="42914-494">Seek to simplify the app's routing scheme.</span></span> <span data-ttu-id="42914-495">Ukázková aplikace vyžaduje explicitní pořadí zpracování tras, které předvádí několik scénářů směrování pomocí jedné aplikace.</span><span class="sxs-lookup"><span data-stu-id="42914-495">The sample app requires an explicit route processing order to demonstrate several routing scenarios using a single app.</span></span> <span data-ttu-id="42914-496">Měli byste se však pokusit vyhnout se postupu nastavení `Order` trasy v produkčních aplikacích.</span><span class="sxs-lookup"><span data-stu-id="42914-496">However, you should attempt to avoid the practice of setting route `Order` in production apps.</span></span>

<span data-ttu-id="42914-497">Směrování Razor Pages směrování a řadiče MVC sdílí implementaci.</span><span class="sxs-lookup"><span data-stu-id="42914-497">Razor Pages routing and MVC controller routing share an implementation.</span></span> <span data-ttu-id="42914-498">Informace o pořadí směrování v tématech MVC jsou k dispozici při [směrování na akce kontroleru: trasy atributů řazení](xref:mvc/controllers/routing#ordering-attribute-routes).</span><span class="sxs-lookup"><span data-stu-id="42914-498">Information on route order in the MVC topics is available at [Routing to controller actions: Ordering attribute routes](xref:mvc/controllers/routing#ordering-attribute-routes).</span></span>

## <a name="model-conventions"></a><span data-ttu-id="42914-499">Konvence modelu</span><span class="sxs-lookup"><span data-stu-id="42914-499">Model conventions</span></span>

<span data-ttu-id="42914-500">Přidejte delegáta pro <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> pro přidání [konvencí modelu](xref:mvc/controllers/application-model#conventions) , které se vztahují na Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="42914-500">Add a delegate for <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> to add [model conventions](xref:mvc/controllers/application-model#conventions) that apply to Razor Pages.</span></span>

### <a name="add-a-route-model-convention-to-all-pages"></a><span data-ttu-id="42914-501">Přidat konvenci modelu směrování na všechny stránky</span><span class="sxs-lookup"><span data-stu-id="42914-501">Add a route model convention to all pages</span></span>

<span data-ttu-id="42914-502">Pomocí <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> můžete vytvořit a přidat <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> do kolekce instancí <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention>, které se použijí během vytváření modelu směrování stránky.</span><span class="sxs-lookup"><span data-stu-id="42914-502">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page route model construction.</span></span>

<span data-ttu-id="42914-503">Ukázková aplikace přidá šablonu směrování `{globalTemplate?}` na všechny stránky v aplikaci:</span><span class="sxs-lookup"><span data-stu-id="42914-503">The sample app adds a `{globalTemplate?}` route template to all of the pages in the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

<span data-ttu-id="42914-504">Vlastnost <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> pro <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> je nastavena na `1`.</span><span class="sxs-lookup"><span data-stu-id="42914-504">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `1`.</span></span> <span data-ttu-id="42914-505">Tím se zajistí následující chování při shodě trasy v ukázkové aplikaci:</span><span class="sxs-lookup"><span data-stu-id="42914-505">This ensures the following route matching behavior in the sample app:</span></span>

* <span data-ttu-id="42914-506">Šablona směrování pro `TheContactPage/{text?}` se přidá později v tématu.</span><span class="sxs-lookup"><span data-stu-id="42914-506">A route template for `TheContactPage/{text?}` is added later in the topic.</span></span> <span data-ttu-id="42914-507">Trasa stránky kontaktu má výchozí pořadí `null` (`Order = 0`), takže odpovídá před šablonou směrování `{globalTemplate?}`.</span><span class="sxs-lookup"><span data-stu-id="42914-507">The Contact Page route has a default order of `null` (`Order = 0`), so it matches before the `{globalTemplate?}` route template.</span></span>
* <span data-ttu-id="42914-508">Šablona směrování `{aboutTemplate?}` je přidána později v tématu.</span><span class="sxs-lookup"><span data-stu-id="42914-508">An `{aboutTemplate?}` route template is added later in the topic.</span></span> <span data-ttu-id="42914-509">Šabloně `{aboutTemplate?}` je předána `Order` `2`.</span><span class="sxs-lookup"><span data-stu-id="42914-509">The `{aboutTemplate?}` template is given an `Order` of `2`.</span></span> <span data-ttu-id="42914-510">Když se na stránce o aplikaci žádá `/About/RouteDataValue`, načtou se "RouteDataValue" do `RouteData.Values["globalTemplate"]` (`Order = 1`) a není `RouteData.Values["aboutTemplate"]` (`Order = 2`) z důvodu nastavení vlastnosti `Order`.</span><span class="sxs-lookup"><span data-stu-id="42914-510">When the About page is requested at `/About/RouteDataValue`, "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["aboutTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>
* <span data-ttu-id="42914-511">Šablona směrování `{otherPagesTemplate?}` je přidána později v tématu.</span><span class="sxs-lookup"><span data-stu-id="42914-511">An `{otherPagesTemplate?}` route template is added later in the topic.</span></span> <span data-ttu-id="42914-512">Šabloně `{otherPagesTemplate?}` je předána `Order` `2`.</span><span class="sxs-lookup"><span data-stu-id="42914-512">The `{otherPagesTemplate?}` template is given an `Order` of `2`.</span></span> <span data-ttu-id="42914-513">Pokud se u libovolné stránky ve složce *Pages/OtherPages* požaduje parametr trasy (například `/OtherPages/Page1/RouteDataValue`), RouteDataValue se načte do `RouteData.Values["globalTemplate"]` (`Order = 1`) a ne`RouteData.Values["otherPagesTemplate"]` (`Order = 2`) z důvodu nastavení vlastnosti `Order`.</span><span class="sxs-lookup"><span data-stu-id="42914-513">When any page in the *Pages/OtherPages* folder is requested with a route parameter (for example, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="42914-514">Pokud je to možné, nenastavte `Order`, což má za následek `Order = 0`.</span><span class="sxs-lookup"><span data-stu-id="42914-514">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="42914-515">Pro výběr správné trasy se spoléhá na směrování.</span><span class="sxs-lookup"><span data-stu-id="42914-515">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="42914-516">Razor Pages možnosti, jako je přidání <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>, jsou přidány při přidání MVC do kolekce služby v `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="42914-516">Razor Pages options, such as adding <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>, are added when MVC is added to the service collection in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="42914-517">Příklad najdete v [ukázkové aplikaci](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/).</span><span class="sxs-lookup"><span data-stu-id="42914-517">For an example, see the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/).</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="42914-518">Požádejte o stránku ukázky o `localhost:5000/About/GlobalRouteValue` a Prohlédněte si výsledek:</span><span class="sxs-lookup"><span data-stu-id="42914-518">Request the sample's About page at `localhost:5000/About/GlobalRouteValue` and inspect the result:</span></span>

![Stránka o aplikaci je požadována s segmentem směrování GlobalRouteValue.](razor-pages-conventions/_static/about-page-global-template.png)

### <a name="add-an-app-model-convention-to-all-pages"></a><span data-ttu-id="42914-521">Přidání konvence modelu aplikace na všechny stránky</span><span class="sxs-lookup"><span data-stu-id="42914-521">Add an app model convention to all pages</span></span>

<span data-ttu-id="42914-522">Pomocí <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> můžete vytvořit a přidat <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> do kolekce instancí <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention>, které se použijí během vytváření modelu aplikace stránky.</span><span class="sxs-lookup"><span data-stu-id="42914-522">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page app model construction.</span></span>

<span data-ttu-id="42914-523">Chcete-li předvést tuto a další konvenci dále v tématu, ukázková aplikace obsahuje třídu `AddHeaderAttribute`.</span><span class="sxs-lookup"><span data-stu-id="42914-523">To demonstrate this and other conventions later in the topic, the sample app includes an `AddHeaderAttribute` class.</span></span> <span data-ttu-id="42914-524">Konstruktor třídy přijímá `name` řetězec a pole `values` řetězců.</span><span class="sxs-lookup"><span data-stu-id="42914-524">The class constructor accepts a `name` string and a `values` string array.</span></span> <span data-ttu-id="42914-525">Tyto hodnoty se používají ve své `OnResultExecuting` metodě pro nastavení hlavičky odpovědi.</span><span class="sxs-lookup"><span data-stu-id="42914-525">These values are used in its `OnResultExecuting` method to set a response header.</span></span> <span data-ttu-id="42914-526">Úplná třída je uvedena v části [konvence akcí modelu stránky](#page-model-action-conventions) dále v tématu.</span><span class="sxs-lookup"><span data-stu-id="42914-526">The full class is shown in the [Page model action conventions](#page-model-action-conventions) section later in the topic.</span></span>

<span data-ttu-id="42914-527">Ukázková aplikace používá třídu `AddHeaderAttribute` k přidání záhlaví, `GlobalHeader`, na všechny stránky v aplikaci:</span><span class="sxs-lookup"><span data-stu-id="42914-527">The sample app uses the `AddHeaderAttribute` class to add a header, `GlobalHeader`, to all of the pages in the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

<span data-ttu-id="42914-528">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="42914-528">*Startup.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

<span data-ttu-id="42914-529">Požádejte o stránku ukázek o `localhost:5000/About` a Prohlédněte si záhlaví a podívejte se na výsledek:</span><span class="sxs-lookup"><span data-stu-id="42914-529">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![Hlavičky odpovědi stránky o produktu ukazují, že byl přidán GlobalHeader.](razor-pages-conventions/_static/about-page-global-header.png)

### <a name="add-a-handler-model-convention-to-all-pages"></a><span data-ttu-id="42914-531">Přidat konvenci modelu obslužné rutiny na všechny stránky</span><span class="sxs-lookup"><span data-stu-id="42914-531">Add a handler model convention to all pages</span></span>

<span data-ttu-id="42914-532">Pomocí <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> můžete vytvořit a přidat <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> do kolekce instancí <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention>, které se použijí během vytváření modelu obslužné rutiny stránky.</span><span class="sxs-lookup"><span data-stu-id="42914-532">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page handler model construction.</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalPageHandlerModelConvention.cs?name=snippet1)]

<span data-ttu-id="42914-533">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="42914-533">*Startup.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet10)]

## <a name="page-route-action-conventions"></a><span data-ttu-id="42914-534">Konvence akcí při směrování stránky</span><span class="sxs-lookup"><span data-stu-id="42914-534">Page route action conventions</span></span>

<span data-ttu-id="42914-535">Výchozí zprostředkovatel modelu směrování odvozený od <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> vyvolá konvence, které jsou navržené tak, aby poskytovaly body rozšiřitelnosti pro konfiguraci tras stránky.</span><span class="sxs-lookup"><span data-stu-id="42914-535">The default route model provider that derives from <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> invokes conventions which are designed to provide extensibility points for configuring page routes.</span></span>

### <a name="folder-route-model-convention"></a><span data-ttu-id="42914-536">Konvence modelu směrování složky</span><span class="sxs-lookup"><span data-stu-id="42914-536">Folder route model convention</span></span>

<span data-ttu-id="42914-537">Pomocí <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> můžete vytvořit a přidat <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention>, který vyvolá akci na <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> pro všechny stránky v zadané složce.</span><span class="sxs-lookup"><span data-stu-id="42914-537">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> for all of the pages under the specified folder.</span></span>

<span data-ttu-id="42914-538">Ukázková aplikace používá <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> k přidání šablony trasy `{otherPagesTemplate?}` na stránky ve složce *OtherPages* :</span><span class="sxs-lookup"><span data-stu-id="42914-538">The sample app uses <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> to add an `{otherPagesTemplate?}` route template to the pages in the *OtherPages* folder:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

<span data-ttu-id="42914-539">Vlastnost <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> pro <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> je nastavena na `2`.</span><span class="sxs-lookup"><span data-stu-id="42914-539">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `2`.</span></span> <span data-ttu-id="42914-540">Tím je zajištěno, že šablona pro `{globalTemplate?}` (nastavená dříve v tématu `1`) má prioritu první pozice hodnoty dat trasy, když je zadána hodnota jedné trasy.</span><span class="sxs-lookup"><span data-stu-id="42914-540">This ensures that the template for `{globalTemplate?}` (set earlier in the topic to `1`) is given priority for the first route data value position when a single route value is provided.</span></span> <span data-ttu-id="42914-541">Pokud je stránka ve složce *Pages/OtherPages* požadována s hodnotou parametru trasy (například `/OtherPages/Page1/RouteDataValue`), je "RouteDataValue" načteno do `RouteData.Values["globalTemplate"]` (`Order = 1`) a není `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) z důvodu nastavení vlastnosti `Order`.</span><span class="sxs-lookup"><span data-stu-id="42914-541">If a page in the *Pages/OtherPages* folder is requested with a route parameter value (for example, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="42914-542">Pokud je to možné, nenastavte `Order`, což má za následek `Order = 0`.</span><span class="sxs-lookup"><span data-stu-id="42914-542">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="42914-543">Pro výběr správné trasy se spoléhá na směrování.</span><span class="sxs-lookup"><span data-stu-id="42914-543">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="42914-544">Vyžádejte si Page1 stránku ukázky na `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` a Prohlédněte si výsledek:</span><span class="sxs-lookup"><span data-stu-id="42914-544">Request the sample's Page1 page at `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` and inspect the result:</span></span>

![Page1 ve složce OtherPages se vyžádá s segmentem směrování GlobalRouteValue a OtherPagesRouteValue.](razor-pages-conventions/_static/otherpages-page1-global-and-otherpages-templates.png)

### <a name="page-route-model-convention"></a><span data-ttu-id="42914-547">Konvence modelu směrování stránky</span><span class="sxs-lookup"><span data-stu-id="42914-547">Page route model convention</span></span>

<span data-ttu-id="42914-548">Pomocí <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> vytvořit a přidat <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention>, který vyvolá akci na <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> pro stránku se zadaným názvem.</span><span class="sxs-lookup"><span data-stu-id="42914-548">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> for the page with the specified name.</span></span>

<span data-ttu-id="42914-549">Ukázková aplikace používá `AddPageRouteModelConvention` k přidání šablony trasy `{aboutTemplate?}` na stránku o aplikaci:</span><span class="sxs-lookup"><span data-stu-id="42914-549">The sample app uses `AddPageRouteModelConvention` to add an `{aboutTemplate?}` route template to the About page:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

<span data-ttu-id="42914-550">Vlastnost <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> pro <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> je nastavena na `2`.</span><span class="sxs-lookup"><span data-stu-id="42914-550">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `2`.</span></span> <span data-ttu-id="42914-551">Tím je zajištěno, že šablona pro `{globalTemplate?}` (nastavená dříve v tématu `1`) má prioritu první pozice hodnoty dat trasy, když je zadána hodnota jedné trasy.</span><span class="sxs-lookup"><span data-stu-id="42914-551">This ensures that the template for `{globalTemplate?}` (set earlier in the topic to `1`) is given priority for the first route data value position when a single route value is provided.</span></span> <span data-ttu-id="42914-552">Pokud se stránka o žádosti vyžaduje s hodnotou parametru Route na `/About/RouteDataValue`, načte se RouteDataValue do `RouteData.Values["globalTemplate"]` (`Order = 1`) a ne`RouteData.Values["aboutTemplate"]` (`Order = 2`) z důvodu nastavení vlastnosti `Order`.</span><span class="sxs-lookup"><span data-stu-id="42914-552">If the About page is requested with a route parameter value at `/About/RouteDataValue`, "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["aboutTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="42914-553">Pokud je to možné, nenastavte `Order`, což má za následek `Order = 0`.</span><span class="sxs-lookup"><span data-stu-id="42914-553">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="42914-554">Pro výběr správné trasy se spoléhá na směrování.</span><span class="sxs-lookup"><span data-stu-id="42914-554">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="42914-555">Požádejte o stránku ukázky o `localhost:5000/About/GlobalRouteValue/AboutRouteValue` a Prohlédněte si výsledek:</span><span class="sxs-lookup"><span data-stu-id="42914-555">Request the sample's About page at `localhost:5000/About/GlobalRouteValue/AboutRouteValue` and inspect the result:</span></span>

![U segmentů směrování pro GlobalRouteValue a AboutRouteValue se žádá o stránku.](razor-pages-conventions/_static/about-page-global-and-about-templates.png)

## <a name="configure-a-page-route"></a><span data-ttu-id="42914-558">Konfigurace trasy stránky</span><span class="sxs-lookup"><span data-stu-id="42914-558">Configure a page route</span></span>

<span data-ttu-id="42914-559">Pomocí <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> můžete nakonfigurovat směrování na stránku na zadané cestě stránky.</span><span class="sxs-lookup"><span data-stu-id="42914-559">Use <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> to configure a route to a page at the specified page path.</span></span> <span data-ttu-id="42914-560">Vygenerované odkazy na stránku používají zadanou trasu.</span><span class="sxs-lookup"><span data-stu-id="42914-560">Generated links to the page use your specified route.</span></span> <span data-ttu-id="42914-561">`AddPageRoute` používá k vytvoření trasy `AddPageRouteModelConvention`.</span><span class="sxs-lookup"><span data-stu-id="42914-561">`AddPageRoute` uses `AddPageRouteModelConvention` to establish the route.</span></span>

<span data-ttu-id="42914-562">Ukázková aplikace vytvoří trasu, která `/TheContactPage` pro *kontakt. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="42914-562">The sample app creates a route to `/TheContactPage` for *Contact.cshtml*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet5)]

<span data-ttu-id="42914-563">Stránku kontaktů lze také získat na `/Contact` prostřednictvím výchozí trasy.</span><span class="sxs-lookup"><span data-stu-id="42914-563">The Contact page can also be reached at `/Contact` via its default route.</span></span>

<span data-ttu-id="42914-564">Vlastní trasa ukázkové aplikace na stránce kontaktů umožňuje, aby byl `text` segment trasy (`{text?}`).</span><span class="sxs-lookup"><span data-stu-id="42914-564">The sample app's custom route to the Contact page allows for an optional `text` route segment (`{text?}`).</span></span> <span data-ttu-id="42914-565">Tato stránka zahrnuje také tento volitelný segment v direktivě `@page` pro případ, že návštěvník přistupuje k této stránce `/Contact` trase:</span><span class="sxs-lookup"><span data-stu-id="42914-565">The page also includes this optional segment in its `@page` directive in case the visitor accesses the page at its `/Contact` route:</span></span>

[!code-cshtml[](razor-pages-conventions/samples/2.x/SampleApp/Pages/Contact.cshtml?highlight=1)]

<span data-ttu-id="42914-566">Všimněte si, že adresa URL vygenerovaná pro odkaz **kontaktu** na vykreslené stránce odráží aktualizovanou trasu:</span><span class="sxs-lookup"><span data-stu-id="42914-566">Note that the URL generated for the **Contact** link in the rendered page reflects the updated route:</span></span>

![Odkaz na ukázkový kontakt aplikace na navigačním panelu](razor-pages-conventions/_static/contact-link.png)

![Kontrola odkazu na kontakt ve vykresleném kódu HTML znamená, že odkaz href je nastaven na hodnotu "/TheContactPage".](razor-pages-conventions/_static/contact-link-source.png)

<span data-ttu-id="42914-569">Navštivte stránku kontaktů buď na své běžné trase, `/Contact`, nebo na vlastní trasu `/TheContactPage`.</span><span class="sxs-lookup"><span data-stu-id="42914-569">Visit the Contact page at either its ordinary route, `/Contact`, or the custom route, `/TheContactPage`.</span></span> <span data-ttu-id="42914-570">Pokud zadáte další segment `text` trasy, stránka zobrazuje segment zakódovaný ve formátu HTML, který zadáte:</span><span class="sxs-lookup"><span data-stu-id="42914-570">If you supply an additional `text` route segment, the page shows the HTML-encoded segment that you provide:</span></span>

![Prohlížeč Edge – příklad poskytnutí volitelného segmentu "text" trasy "TextValue" v adrese URL](razor-pages-conventions/_static/route-segment-with-custom-route.png)

## <a name="page-model-action-conventions"></a><span data-ttu-id="42914-573">Konvence akcí modelu stránky</span><span class="sxs-lookup"><span data-stu-id="42914-573">Page model action conventions</span></span>

<span data-ttu-id="42914-574">Výchozí poskytovatel modelu stránky, který implementuje <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider>, vyvolá konvence, které jsou navrženy tak, aby poskytovaly body rozšiřitelnosti pro konfiguraci modelů stránek.</span><span class="sxs-lookup"><span data-stu-id="42914-574">The default page model provider that implements <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> invokes conventions which are designed to provide extensibility points for configuring page models.</span></span> <span data-ttu-id="42914-575">Tyto konvence jsou užitečné při sestavování a úpravách scénářů zjišťování a zpracování stránky.</span><span class="sxs-lookup"><span data-stu-id="42914-575">These conventions are useful when building and modifying page discovery and processing scenarios.</span></span>

<span data-ttu-id="42914-576">V příkladech v této části používá ukázková aplikace třídu `AddHeaderAttribute`, což je <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>, která používá hlavičku odpovědi:</span><span class="sxs-lookup"><span data-stu-id="42914-576">For the examples in this section, the sample app uses an `AddHeaderAttribute` class, which is a <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>, that applies a response header:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

<span data-ttu-id="42914-577">Pomocí konvence Ukázka ukazuje, jak použít atribut na všechny stránky ve složce a na jednu stránku.</span><span class="sxs-lookup"><span data-stu-id="42914-577">Using conventions, the sample demonstrates how to apply the attribute to all of the pages in a folder and to a single page.</span></span>

<span data-ttu-id="42914-578">**Konvence modelu aplikace složky**</span><span class="sxs-lookup"><span data-stu-id="42914-578">**Folder app model convention**</span></span>

<span data-ttu-id="42914-579">Pomocí <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> můžete vytvořit a přidat <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention>, který vyvolá akci na instancích <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> pro všechny stránky v zadané složce.</span><span class="sxs-lookup"><span data-stu-id="42914-579">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> that invokes an action on <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> instances for all pages under the specified folder.</span></span>

<span data-ttu-id="42914-580">Ukázka demonstruje použití `AddFolderApplicationModelConvention` přidáním hlavičky, `OtherPagesHeader`na stránky ve složce *OtherPages* v aplikaci:</span><span class="sxs-lookup"><span data-stu-id="42914-580">The sample demonstrates the use of `AddFolderApplicationModelConvention` by adding a header, `OtherPagesHeader`, to the pages inside the *OtherPages* folder of the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet6)]

<span data-ttu-id="42914-581">Vyžádejte si Page1 stránku ukázky na `localhost:5000/OtherPages/Page1` a Prohlédněte si hlavičky a podívejte se na výsledek:</span><span class="sxs-lookup"><span data-stu-id="42914-581">Request the sample's Page1 page at `localhost:5000/OtherPages/Page1` and inspect the headers to view the result:</span></span>

![Hlavičky odpovědi stránky OtherPages/Page1 ukazují, že byla přidána OtherPagesHeader.](razor-pages-conventions/_static/page1-otherpages-header.png)

<span data-ttu-id="42914-583">**Konvence modelu aplikace stránky**</span><span class="sxs-lookup"><span data-stu-id="42914-583">**Page app model convention**</span></span>

<span data-ttu-id="42914-584">Pomocí <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> vytvořit a přidat <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention>, který vyvolá akci na <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> pro stránku se zadaným názvem.</span><span class="sxs-lookup"><span data-stu-id="42914-584">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> for the page with the specified name.</span></span>

<span data-ttu-id="42914-585">Ukázka demonstruje použití `AddPageApplicationModelConvention` přidáním hlavičky, `AboutHeader`na stránku o aplikaci:</span><span class="sxs-lookup"><span data-stu-id="42914-585">The sample demonstrates the use of `AddPageApplicationModelConvention` by adding a header, `AboutHeader`, to the About page:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet7)]

<span data-ttu-id="42914-586">Požádejte o stránku ukázek o `localhost:5000/About` a Prohlédněte si záhlaví a podívejte se na výsledek:</span><span class="sxs-lookup"><span data-stu-id="42914-586">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![Hlavičky odpovědi stránky o produktu ukazují, že byl přidán AboutHeader.](razor-pages-conventions/_static/about-page-about-header.png)

<span data-ttu-id="42914-588">**Konfigurace filtru**</span><span class="sxs-lookup"><span data-stu-id="42914-588">**Configure a filter**</span></span>

<span data-ttu-id="42914-589"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> nakonfiguruje zadaný filtr, který se má použít.</span><span class="sxs-lookup"><span data-stu-id="42914-589"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configures the specified filter to apply.</span></span> <span data-ttu-id="42914-590">Můžete implementovat třídu filtru, ale ukázková aplikace ukazuje, jak implementovat filtr ve výrazu lambda, který je implementován na pozadí jako objekt pro vytváření, který vrací filtr:</span><span class="sxs-lookup"><span data-stu-id="42914-590">You can implement a filter class, but the sample app shows how to implement a filter in a lambda expression, which is implemented behind-the-scenes as a factory that returns a filter:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet8)]

<span data-ttu-id="42914-591">Model webové aplikace se používá ke kontrole relativní cesty pro segmenty, které vedou ke stránce Page2 ve složce *OtherPages* .</span><span class="sxs-lookup"><span data-stu-id="42914-591">The page app model is used to check the relative path for segments that lead to the Page2 page in the *OtherPages* folder.</span></span> <span data-ttu-id="42914-592">Pokud podmínka projde, přidá se hlavička.</span><span class="sxs-lookup"><span data-stu-id="42914-592">If the condition passes, a header is added.</span></span> <span data-ttu-id="42914-593">V takovém případě je `EmptyFilter` použito.</span><span class="sxs-lookup"><span data-stu-id="42914-593">If not, the `EmptyFilter` is applied.</span></span>

<span data-ttu-id="42914-594">`EmptyFilter` je [Filtr akcí](xref:mvc/controllers/filters#action-filters).</span><span class="sxs-lookup"><span data-stu-id="42914-594">`EmptyFilter` is an [Action filter](xref:mvc/controllers/filters#action-filters).</span></span> <span data-ttu-id="42914-595">Vzhledem k tomu, že jsou filtry akcí ignorovány Razor Pages, `EmptyFilter` nemá žádný vliv, pokud cesta neobsahuje `OtherPages/Page2`.</span><span class="sxs-lookup"><span data-stu-id="42914-595">Since Action filters are ignored by Razor Pages, the `EmptyFilter` has no effect as intended if the path doesn't contain `OtherPages/Page2`.</span></span>

<span data-ttu-id="42914-596">Vyžádejte si Page2 stránku ukázky na `localhost:5000/OtherPages/Page2` a Prohlédněte si hlavičky a podívejte se na výsledek:</span><span class="sxs-lookup"><span data-stu-id="42914-596">Request the sample's Page2 page at `localhost:5000/OtherPages/Page2` and inspect the headers to view the result:</span></span>

![OtherPagesPage2Header se přidá do odpovědi pro Page2.](razor-pages-conventions/_static/page2-filter-header.png)

<span data-ttu-id="42914-598">**Konfigurace objektu pro vytváření filtrů**</span><span class="sxs-lookup"><span data-stu-id="42914-598">**Configure a filter factory**</span></span>

<span data-ttu-id="42914-599"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> nakonfiguruje určený objekt pro vytváření a použije [filtry](xref:mvc/controllers/filters) na všechny Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="42914-599"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configures the specified factory to apply [filters](xref:mvc/controllers/filters) to all Razor Pages.</span></span>

<span data-ttu-id="42914-600">Ukázková aplikace poskytuje příklad použití [objektu pro vytváření filtru](xref:mvc/controllers/filters#ifilterfactory) přidáním hlavičky, `FilterFactoryHeader`se dvěma hodnotami na stránky aplikace:</span><span class="sxs-lookup"><span data-stu-id="42914-600">The sample app provides an example of using a [filter factory](xref:mvc/controllers/filters#ifilterfactory) by adding a header, `FilterFactoryHeader`, with two values to the app's pages:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet9)]

<span data-ttu-id="42914-601">*AddHeaderWithFactory.cs*:</span><span class="sxs-lookup"><span data-stu-id="42914-601">*AddHeaderWithFactory.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Factories/AddHeaderWithFactory.cs?name=snippet1)]

<span data-ttu-id="42914-602">Požádejte o stránku ukázek o `localhost:5000/About` a Prohlédněte si záhlaví a podívejte se na výsledek:</span><span class="sxs-lookup"><span data-stu-id="42914-602">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![Hlavičky odpovědi stránky o stránku zobrazují, že byly přidány dvě hlavičky FilterFactoryHeader.](razor-pages-conventions/_static/about-page-filter-factory-header.png)

## <a name="mvc-filters-and-the-page-filter-ipagefilter"></a><span data-ttu-id="42914-604">Filtry MVC a filtr stránky (IPageFilter)</span><span class="sxs-lookup"><span data-stu-id="42914-604">MVC Filters and the Page filter (IPageFilter)</span></span>

<span data-ttu-id="42914-605">[Filtry akcí](xref:mvc/controllers/filters#action-filters) MVC se Razor Pages ignorují, protože Razor Pages použít obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="42914-605">MVC [Action filters](xref:mvc/controllers/filters#action-filters) are ignored by Razor Pages, since Razor Pages use handler methods.</span></span> <span data-ttu-id="42914-606">Další typy filtrů MVC jsou k dispozici pro použití: [autorizace](xref:mvc/controllers/filters#authorization-filters), [výjimka](xref:mvc/controllers/filters#exception-filters), [prostředek](xref:mvc/controllers/filters#resource-filters)a [výsledek](xref:mvc/controllers/filters#result-filters).</span><span class="sxs-lookup"><span data-stu-id="42914-606">Other types of MVC filters are available for you to use: [Authorization](xref:mvc/controllers/filters#authorization-filters), [Exception](xref:mvc/controllers/filters#exception-filters), [Resource](xref:mvc/controllers/filters#resource-filters), and [Result](xref:mvc/controllers/filters#result-filters).</span></span> <span data-ttu-id="42914-607">Další informace najdete v tématu [filtry](xref:mvc/controllers/filters) .</span><span class="sxs-lookup"><span data-stu-id="42914-607">For more information, see the [Filters](xref:mvc/controllers/filters) topic.</span></span>

<span data-ttu-id="42914-608">Filtr stránky (<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>) je filtr, který se vztahuje na Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="42914-608">The Page filter (<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>) is a filter that applies to Razor Pages.</span></span> <span data-ttu-id="42914-609">Další informace naleznete v tématu [metody Filter pro Razor Pages](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="42914-609">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="42914-610">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="42914-610">Additional resources</span></span>

* <xref:security/authorization/razor-pages-authorization>
* <xref:mvc/controllers/areas#areas-with-razor-pages>

::: moniker-end
