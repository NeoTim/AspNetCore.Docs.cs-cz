---
title: Razor Pages konvence směrování a aplikace v ASP.NET Core
author: rick-anderson
description: Seznamte se s tím, jak konvence poskytovatelů modelů směrování a aplikací umožňují řídit směrování, zjišťování a zpracování stránky.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
uid: razor-pages/razor-pages-conventions
ms.openlocfilehash: f45e327051aba54d1cab67148eb540fb1a5cc149
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78667858"
---
# <a name="razor-pages-route-and-app-conventions-in-aspnet-core"></a><span data-ttu-id="030f6-103">Razor Pages konvence směrování a aplikace v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="030f6-103">Razor Pages route and app conventions in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="030f6-104">Naučte se používat [konvence směrování stránky a poskytovatele modelů aplikací](xref:mvc/controllers/application-model#conventions) k řízení směrování, zjišťování a zpracování stránky v aplikacích Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="030f6-104">Learn how to use page [route and app model provider conventions](xref:mvc/controllers/application-model#conventions) to control page routing, discovery, and processing in Razor Pages apps.</span></span>

<span data-ttu-id="030f6-105">Pokud potřebujete nakonfigurovat vlastní trasy stránky pro jednotlivé stránky, nakonfigurujte směrování na stránky pomocí [AddPageRoute konvence](#configure-a-page-route) popsané dále v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="030f6-105">When you need to configure custom page routes for individual pages, configure routing to pages with the [AddPageRoute convention](#configure-a-page-route) described later in this topic.</span></span>

<span data-ttu-id="030f6-106">Chcete-li určit trasu stránky, přidat segmenty směrování nebo přidat parametry do trasy, použijte direktivu `@page` stránky.</span><span class="sxs-lookup"><span data-stu-id="030f6-106">To specify a page route, add route segments, or add parameters to a route, use the page's `@page` directive.</span></span> <span data-ttu-id="030f6-107">Další informace najdete v tématu [vlastní trasy](xref:razor-pages/index#custom-routes).</span><span class="sxs-lookup"><span data-stu-id="030f6-107">For more information, see [Custom routes](xref:razor-pages/index#custom-routes).</span></span>

<span data-ttu-id="030f6-108">Existují vyhrazená slova, která nelze použít jako segmenty směrování nebo názvy parametrů.</span><span class="sxs-lookup"><span data-stu-id="030f6-108">There are reserved words that can't be used as route segments or parameter names.</span></span> <span data-ttu-id="030f6-109">Další informace najdete v tématu [směrování: názvy rezervovaných směrování](xref:fundamentals/routing#reserved-routing-names).</span><span class="sxs-lookup"><span data-stu-id="030f6-109">For more information, see [Routing: Reserved routing names](xref:fundamentals/routing#reserved-routing-names).</span></span>

<span data-ttu-id="030f6-110">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="030f6-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

| <span data-ttu-id="030f6-111">Scénář</span><span class="sxs-lookup"><span data-stu-id="030f6-111">Scenario</span></span> | <span data-ttu-id="030f6-112">Ukázka znázorňuje...</span><span class="sxs-lookup"><span data-stu-id="030f6-112">The sample demonstrates ...</span></span> |
| -------- | --------------------------- |
| [<span data-ttu-id="030f6-113">Konvence modelu</span><span class="sxs-lookup"><span data-stu-id="030f6-113">Model conventions</span></span>](#model-conventions)<br><br><span data-ttu-id="030f6-114">Konvence. Add</span><span class="sxs-lookup"><span data-stu-id="030f6-114">Conventions.Add</span></span><ul><li><span data-ttu-id="030f6-115">IPageRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="030f6-115">IPageRouteModelConvention</span></span></li><li><span data-ttu-id="030f6-116">IPageApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="030f6-116">IPageApplicationModelConvention</span></span></li><li><span data-ttu-id="030f6-117">IPageHandlerModelConvention</span><span class="sxs-lookup"><span data-stu-id="030f6-117">IPageHandlerModelConvention</span></span></li></ul> | <span data-ttu-id="030f6-118">Přidejte šablonu a hlavičku směrování na stránky aplikace.</span><span class="sxs-lookup"><span data-stu-id="030f6-118">Add a route template and header to an app's pages.</span></span> |
| [<span data-ttu-id="030f6-119">Konvence akcí při směrování stránky</span><span class="sxs-lookup"><span data-stu-id="030f6-119">Page route action conventions</span></span>](#page-route-action-conventions)<ul><li><span data-ttu-id="030f6-120">AddFolderRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="030f6-120">AddFolderRouteModelConvention</span></span></li><li><span data-ttu-id="030f6-121">AddPageRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="030f6-121">AddPageRouteModelConvention</span></span></li><li><span data-ttu-id="030f6-122">AddPageRoute</span><span class="sxs-lookup"><span data-stu-id="030f6-122">AddPageRoute</span></span></li></ul> | <span data-ttu-id="030f6-123">Přidejte šablonu směrování do stránek ve složce a na jednu stránku.</span><span class="sxs-lookup"><span data-stu-id="030f6-123">Add a route template to pages in a folder and to a single page.</span></span> |
| [<span data-ttu-id="030f6-124">Konvence akcí modelu stránky</span><span class="sxs-lookup"><span data-stu-id="030f6-124">Page model action conventions</span></span>](#page-model-action-conventions)<ul><li><span data-ttu-id="030f6-125">AddFolderApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="030f6-125">AddFolderApplicationModelConvention</span></span></li><li><span data-ttu-id="030f6-126">AddPageApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="030f6-126">AddPageApplicationModelConvention</span></span></li><li><span data-ttu-id="030f6-127">ConfigureFilter (filtrovat třídu, lambda výraz nebo objekt pro vytváření filtru)</span><span class="sxs-lookup"><span data-stu-id="030f6-127">ConfigureFilter (filter class, lambda expression, or filter factory)</span></span></li></ul> | <span data-ttu-id="030f6-128">Umožňuje přidat záhlaví na stránky ve složce, přidat záhlaví na jednu stránku a nakonfigurovat [objekt pro vytváření filtru](xref:mvc/controllers/filters#ifilterfactory) tak, aby přidal hlavičku na stránky aplikace.</span><span class="sxs-lookup"><span data-stu-id="030f6-128">Add a header to pages in a folder, add a header to a single page, and configure a [filter factory](xref:mvc/controllers/filters#ifilterfactory) to add a header to an app's pages.</span></span> |

<span data-ttu-id="030f6-129">Razor Pages konvence jsou přidány a nakonfigurovány pomocí metody rozšíření <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> pro <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> v kolekci služeb ve třídě `Startup`.</span><span class="sxs-lookup"><span data-stu-id="030f6-129">Razor Pages conventions are added and configured using the <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> extension method to <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> on the service collection in the `Startup` class.</span></span> <span data-ttu-id="030f6-130">Následující příklady konvence jsou vysvětleny dále v tomto tématu:</span><span class="sxs-lookup"><span data-stu-id="030f6-130">The following convention examples are explained later in this topic:</span></span>

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

## <a name="route-order"></a><span data-ttu-id="030f6-131">Pořadí směrování</span><span class="sxs-lookup"><span data-stu-id="030f6-131">Route order</span></span>

<span data-ttu-id="030f6-132">Trasy určují <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> pro zpracování (odpovídá směrování).</span><span class="sxs-lookup"><span data-stu-id="030f6-132">Routes specify an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> for processing (route matching).</span></span>

| <span data-ttu-id="030f6-133">Objednání</span><span class="sxs-lookup"><span data-stu-id="030f6-133">Order</span></span>            | <span data-ttu-id="030f6-134">Chování</span><span class="sxs-lookup"><span data-stu-id="030f6-134">Behavior</span></span> |
| :--------------: | -------- |
| <span data-ttu-id="030f6-135">-1</span><span class="sxs-lookup"><span data-stu-id="030f6-135">-1</span></span>               | <span data-ttu-id="030f6-136">Trasa je zpracována před zpracováním jiných tras.</span><span class="sxs-lookup"><span data-stu-id="030f6-136">The route is processed before other routes are processed.</span></span> |
| <span data-ttu-id="030f6-137">0</span><span class="sxs-lookup"><span data-stu-id="030f6-137">0</span></span>                | <span data-ttu-id="030f6-138">Pořadí není zadáno (výchozí hodnota).</span><span class="sxs-lookup"><span data-stu-id="030f6-138">Order isn't specified (default value).</span></span> <span data-ttu-id="030f6-139">Při nepřiřazování `Order` (`Order = null`) jsou výchozí hodnoty `Order` trasy na 0 (nula) pro zpracování.</span><span class="sxs-lookup"><span data-stu-id="030f6-139">Not assigning `Order` (`Order = null`) defaults the route `Order` to 0 (zero) for processing.</span></span> |
| <span data-ttu-id="030f6-140">1, 2, &hellip; n</span><span class="sxs-lookup"><span data-stu-id="030f6-140">1, 2, &hellip; n</span></span> | <span data-ttu-id="030f6-141">Určuje pořadí zpracování směrování.</span><span class="sxs-lookup"><span data-stu-id="030f6-141">Specifies the route processing order.</span></span> |

<span data-ttu-id="030f6-142">Zpracování směrování je zřízené podle konvence:</span><span class="sxs-lookup"><span data-stu-id="030f6-142">Route processing is established by convention:</span></span>

* <span data-ttu-id="030f6-143">Trasy jsou zpracovávány v sekvenčním pořadí (-1, 0, 1, 2, &hellip; n).</span><span class="sxs-lookup"><span data-stu-id="030f6-143">Routes are processed in sequential order (-1, 0, 1, 2, &hellip; n).</span></span>
* <span data-ttu-id="030f6-144">Když mají stejné trasy stejné `Order`, bude se nejprve shodovat konkrétní trasa, za kterou následuje méně specifických tras.</span><span class="sxs-lookup"><span data-stu-id="030f6-144">When routes have the same `Order`, the most specific route is matched first followed by less specific routes.</span></span>
* <span data-ttu-id="030f6-145">Když se trasy se stejnou `Order` a stejný počet parametrů shodují s adresou URL požadavku, trasy se zpracovávají v pořadí, v jakém jsou přidány do <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>.</span><span class="sxs-lookup"><span data-stu-id="030f6-145">When routes with the same `Order` and the same number of parameters match a request URL, routes are processed in the order that they're added to the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>.</span></span>

<span data-ttu-id="030f6-146">Pokud je to možné, vyhněte se v závislosti na zavedeném pořadí zpracování směrování.</span><span class="sxs-lookup"><span data-stu-id="030f6-146">If possible, avoid depending on an established route processing order.</span></span> <span data-ttu-id="030f6-147">Obecně směrování vybírá správnou trasu s odpovídající adresou URL.</span><span class="sxs-lookup"><span data-stu-id="030f6-147">Generally, routing selects the correct route with URL matching.</span></span> <span data-ttu-id="030f6-148">Pokud musíte nastavit vlastnosti směrovacích `Order` tak, aby správně směrovaly požadavky, schéma směrování aplikace je pravděpodobně matoucí pro klienty a křehké k údržbě.</span><span class="sxs-lookup"><span data-stu-id="030f6-148">If you must set route `Order` properties to route requests correctly, the app's routing scheme is probably confusing to clients and fragile to maintain.</span></span> <span data-ttu-id="030f6-149">Vyhledejte zjednodušené schéma směrování aplikace.</span><span class="sxs-lookup"><span data-stu-id="030f6-149">Seek to simplify the app's routing scheme.</span></span> <span data-ttu-id="030f6-150">Ukázková aplikace vyžaduje explicitní pořadí zpracování tras, které předvádí několik scénářů směrování pomocí jedné aplikace.</span><span class="sxs-lookup"><span data-stu-id="030f6-150">The sample app requires an explicit route processing order to demonstrate several routing scenarios using a single app.</span></span> <span data-ttu-id="030f6-151">Měli byste se však pokusit vyhnout se postupu nastavení `Order` trasy v produkčních aplikacích.</span><span class="sxs-lookup"><span data-stu-id="030f6-151">However, you should attempt to avoid the practice of setting route `Order` in production apps.</span></span>

<span data-ttu-id="030f6-152">Směrování Razor Pages směrování a řadiče MVC sdílí implementaci.</span><span class="sxs-lookup"><span data-stu-id="030f6-152">Razor Pages routing and MVC controller routing share an implementation.</span></span> <span data-ttu-id="030f6-153">Informace o pořadí směrování v tématech MVC jsou k dispozici při [směrování na akce kontroleru: trasy atributů řazení](xref:mvc/controllers/routing#ordering-attribute-routes).</span><span class="sxs-lookup"><span data-stu-id="030f6-153">Information on route order in the MVC topics is available at [Routing to controller actions: Ordering attribute routes](xref:mvc/controllers/routing#ordering-attribute-routes).</span></span>

## <a name="model-conventions"></a><span data-ttu-id="030f6-154">Konvence modelu</span><span class="sxs-lookup"><span data-stu-id="030f6-154">Model conventions</span></span>

<span data-ttu-id="030f6-155">Přidejte delegáta pro <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> pro přidání [konvencí modelu](xref:mvc/controllers/application-model#conventions) , které se vztahují na Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="030f6-155">Add a delegate for <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> to add [model conventions](xref:mvc/controllers/application-model#conventions) that apply to Razor Pages.</span></span>

### <a name="add-a-route-model-convention-to-all-pages"></a><span data-ttu-id="030f6-156">Přidat konvenci modelu směrování na všechny stránky</span><span class="sxs-lookup"><span data-stu-id="030f6-156">Add a route model convention to all pages</span></span>

<span data-ttu-id="030f6-157">Pomocí <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> můžete vytvořit a přidat <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> do kolekce instancí <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention>, které se použijí během vytváření modelu směrování stránky.</span><span class="sxs-lookup"><span data-stu-id="030f6-157">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page route model construction.</span></span>

<span data-ttu-id="030f6-158">Ukázková aplikace přidá šablonu směrování `{globalTemplate?}` na všechny stránky v aplikaci:</span><span class="sxs-lookup"><span data-stu-id="030f6-158">The sample app adds a `{globalTemplate?}` route template to all of the pages in the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

<span data-ttu-id="030f6-159">Vlastnost <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> pro <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> je nastavena na `1`.</span><span class="sxs-lookup"><span data-stu-id="030f6-159">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `1`.</span></span> <span data-ttu-id="030f6-160">Tím se zajistí následující chování při shodě trasy v ukázkové aplikaci:</span><span class="sxs-lookup"><span data-stu-id="030f6-160">This ensures the following route matching behavior in the sample app:</span></span>

* <span data-ttu-id="030f6-161">Šablona směrování pro `TheContactPage/{text?}` se přidá později v tématu.</span><span class="sxs-lookup"><span data-stu-id="030f6-161">A route template for `TheContactPage/{text?}` is added later in the topic.</span></span> <span data-ttu-id="030f6-162">Trasa stránky kontaktu má výchozí pořadí `null` (`Order = 0`), takže odpovídá před šablonou směrování `{globalTemplate?}`.</span><span class="sxs-lookup"><span data-stu-id="030f6-162">The Contact Page route has a default order of `null` (`Order = 0`), so it matches before the `{globalTemplate?}` route template.</span></span>
* <span data-ttu-id="030f6-163">Šablona směrování `{aboutTemplate?}` je přidána později v tématu.</span><span class="sxs-lookup"><span data-stu-id="030f6-163">An `{aboutTemplate?}` route template is added later in the topic.</span></span> <span data-ttu-id="030f6-164">Šabloně `{aboutTemplate?}` je předána `Order` `2`.</span><span class="sxs-lookup"><span data-stu-id="030f6-164">The `{aboutTemplate?}` template is given an `Order` of `2`.</span></span> <span data-ttu-id="030f6-165">Když se na stránce o aplikaci žádá `/About/RouteDataValue`, načtou se "RouteDataValue" do `RouteData.Values["globalTemplate"]` (`Order = 1`) a není `RouteData.Values["aboutTemplate"]` (`Order = 2`) z důvodu nastavení vlastnosti `Order`.</span><span class="sxs-lookup"><span data-stu-id="030f6-165">When the About page is requested at `/About/RouteDataValue`, "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["aboutTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>
* <span data-ttu-id="030f6-166">Šablona směrování `{otherPagesTemplate?}` je přidána později v tématu.</span><span class="sxs-lookup"><span data-stu-id="030f6-166">An `{otherPagesTemplate?}` route template is added later in the topic.</span></span> <span data-ttu-id="030f6-167">Šabloně `{otherPagesTemplate?}` je předána `Order` `2`.</span><span class="sxs-lookup"><span data-stu-id="030f6-167">The `{otherPagesTemplate?}` template is given an `Order` of `2`.</span></span> <span data-ttu-id="030f6-168">Pokud se u libovolné stránky ve složce *Pages/OtherPages* požaduje parametr trasy (například `/OtherPages/Page1/RouteDataValue`), RouteDataValue se načte do `RouteData.Values["globalTemplate"]` (`Order = 1`) a ne`RouteData.Values["otherPagesTemplate"]` (`Order = 2`) z důvodu nastavení vlastnosti `Order`.</span><span class="sxs-lookup"><span data-stu-id="030f6-168">When any page in the *Pages/OtherPages* folder is requested with a route parameter (for example, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="030f6-169">Pokud je to možné, nenastavte `Order`, což má za následek `Order = 0`.</span><span class="sxs-lookup"><span data-stu-id="030f6-169">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="030f6-170">Pro výběr správné trasy se spoléhá na směrování.</span><span class="sxs-lookup"><span data-stu-id="030f6-170">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="030f6-171">Razor Pages možnosti, jako je přidání <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>, jsou přidány při přidání MVC do kolekce služby v `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="030f6-171">Razor Pages options, such as adding <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>, are added when MVC is added to the service collection in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="030f6-172">Příklad najdete v [ukázkové aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/).</span><span class="sxs-lookup"><span data-stu-id="030f6-172">For an example, see the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/).</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="030f6-173">Požádejte o stránku ukázky o `localhost:5000/About/GlobalRouteValue` a Prohlédněte si výsledek:</span><span class="sxs-lookup"><span data-stu-id="030f6-173">Request the sample's About page at `localhost:5000/About/GlobalRouteValue` and inspect the result:</span></span>

![Stránka o aplikaci je požadována s segmentem směrování GlobalRouteValue.](razor-pages-conventions/_static/about-page-global-template.png)

### <a name="add-an-app-model-convention-to-all-pages"></a><span data-ttu-id="030f6-176">Přidání konvence modelu aplikace na všechny stránky</span><span class="sxs-lookup"><span data-stu-id="030f6-176">Add an app model convention to all pages</span></span>

<span data-ttu-id="030f6-177">Pomocí <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> můžete vytvořit a přidat <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> do kolekce instancí <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention>, které se použijí během vytváření modelu aplikace stránky.</span><span class="sxs-lookup"><span data-stu-id="030f6-177">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page app model construction.</span></span>

<span data-ttu-id="030f6-178">Chcete-li předvést tuto a další konvenci dále v tématu, ukázková aplikace obsahuje třídu `AddHeaderAttribute`.</span><span class="sxs-lookup"><span data-stu-id="030f6-178">To demonstrate this and other conventions later in the topic, the sample app includes an `AddHeaderAttribute` class.</span></span> <span data-ttu-id="030f6-179">Konstruktor třídy přijímá `name` řetězec a pole `values` řetězců.</span><span class="sxs-lookup"><span data-stu-id="030f6-179">The class constructor accepts a `name` string and a `values` string array.</span></span> <span data-ttu-id="030f6-180">Tyto hodnoty se používají ve své `OnResultExecuting` metodě pro nastavení hlavičky odpovědi.</span><span class="sxs-lookup"><span data-stu-id="030f6-180">These values are used in its `OnResultExecuting` method to set a response header.</span></span> <span data-ttu-id="030f6-181">Úplná třída je uvedena v části [konvence akcí modelu stránky](#page-model-action-conventions) dále v tématu.</span><span class="sxs-lookup"><span data-stu-id="030f6-181">The full class is shown in the [Page model action conventions](#page-model-action-conventions) section later in the topic.</span></span>

<span data-ttu-id="030f6-182">Ukázková aplikace používá třídu `AddHeaderAttribute` k přidání záhlaví, `GlobalHeader`, na všechny stránky v aplikaci:</span><span class="sxs-lookup"><span data-stu-id="030f6-182">The sample app uses the `AddHeaderAttribute` class to add a header, `GlobalHeader`, to all of the pages in the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

<span data-ttu-id="030f6-183">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="030f6-183">*Startup.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet2)]

<span data-ttu-id="030f6-184">Požádejte o stránku ukázek o `localhost:5000/About` a Prohlédněte si záhlaví a podívejte se na výsledek:</span><span class="sxs-lookup"><span data-stu-id="030f6-184">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![Hlavičky odpovědi stránky o produktu ukazují, že byl přidán GlobalHeader.](razor-pages-conventions/_static/about-page-global-header.png)

### <a name="add-a-handler-model-convention-to-all-pages"></a><span data-ttu-id="030f6-186">Přidat konvenci modelu obslužné rutiny na všechny stránky</span><span class="sxs-lookup"><span data-stu-id="030f6-186">Add a handler model convention to all pages</span></span>

<span data-ttu-id="030f6-187">Pomocí <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> můžete vytvořit a přidat <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> do kolekce instancí <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention>, které se použijí během vytváření modelu obslužné rutiny stránky.</span><span class="sxs-lookup"><span data-stu-id="030f6-187">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page handler model construction.</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalPageHandlerModelConvention.cs?name=snippet1)]

<span data-ttu-id="030f6-188">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="030f6-188">*Startup.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet10)]

## <a name="page-route-action-conventions"></a><span data-ttu-id="030f6-189">Konvence akcí při směrování stránky</span><span class="sxs-lookup"><span data-stu-id="030f6-189">Page route action conventions</span></span>

<span data-ttu-id="030f6-190">Výchozí zprostředkovatel modelu směrování odvozený od <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> vyvolá konvence, které jsou navržené tak, aby poskytovaly body rozšiřitelnosti pro konfiguraci tras stránky.</span><span class="sxs-lookup"><span data-stu-id="030f6-190">The default route model provider that derives from <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> invokes conventions which are designed to provide extensibility points for configuring page routes.</span></span>

### <a name="folder-route-model-convention"></a><span data-ttu-id="030f6-191">Konvence modelu směrování složky</span><span class="sxs-lookup"><span data-stu-id="030f6-191">Folder route model convention</span></span>

<span data-ttu-id="030f6-192">Pomocí <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> můžete vytvořit a přidat <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention>, který vyvolá akci na <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> pro všechny stránky v zadané složce.</span><span class="sxs-lookup"><span data-stu-id="030f6-192">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> for all of the pages under the specified folder.</span></span>

<span data-ttu-id="030f6-193">Ukázková aplikace používá <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> k přidání šablony trasy `{otherPagesTemplate?}` na stránky ve složce *OtherPages* :</span><span class="sxs-lookup"><span data-stu-id="030f6-193">The sample app uses <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> to add an `{otherPagesTemplate?}` route template to the pages in the *OtherPages* folder:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet3)]

<span data-ttu-id="030f6-194">Vlastnost <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> pro <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> je nastavena na `2`.</span><span class="sxs-lookup"><span data-stu-id="030f6-194">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `2`.</span></span> <span data-ttu-id="030f6-195">Tím je zajištěno, že šablona pro `{globalTemplate?}` (nastavená dříve v tématu `1`) má prioritu první pozice hodnoty dat trasy, když je zadána hodnota jedné trasy.</span><span class="sxs-lookup"><span data-stu-id="030f6-195">This ensures that the template for `{globalTemplate?}` (set earlier in the topic to `1`) is given priority for the first route data value position when a single route value is provided.</span></span> <span data-ttu-id="030f6-196">Pokud je stránka ve složce *Pages/OtherPages* požadována s hodnotou parametru trasy (například `/OtherPages/Page1/RouteDataValue`), je "RouteDataValue" načteno do `RouteData.Values["globalTemplate"]` (`Order = 1`) a není `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) z důvodu nastavení vlastnosti `Order`.</span><span class="sxs-lookup"><span data-stu-id="030f6-196">If a page in the *Pages/OtherPages* folder is requested with a route parameter value (for example, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="030f6-197">Pokud je to možné, nenastavte `Order`, což má za následek `Order = 0`.</span><span class="sxs-lookup"><span data-stu-id="030f6-197">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="030f6-198">Pro výběr správné trasy se spoléhá na směrování.</span><span class="sxs-lookup"><span data-stu-id="030f6-198">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="030f6-199">Vyžádejte si Page1 stránku ukázky na `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` a Prohlédněte si výsledek:</span><span class="sxs-lookup"><span data-stu-id="030f6-199">Request the sample's Page1 page at `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` and inspect the result:</span></span>

![Page1 ve složce OtherPages se vyžádá s segmentem směrování GlobalRouteValue a OtherPagesRouteValue.](razor-pages-conventions/_static/otherpages-page1-global-and-otherpages-templates.png)

### <a name="page-route-model-convention"></a><span data-ttu-id="030f6-202">Konvence modelu směrování stránky</span><span class="sxs-lookup"><span data-stu-id="030f6-202">Page route model convention</span></span>

<span data-ttu-id="030f6-203">Pomocí <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> vytvořit a přidat <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention>, který vyvolá akci na <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> pro stránku se zadaným názvem.</span><span class="sxs-lookup"><span data-stu-id="030f6-203">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> for the page with the specified name.</span></span>

<span data-ttu-id="030f6-204">Ukázková aplikace používá `AddPageRouteModelConvention` k přidání šablony trasy `{aboutTemplate?}` na stránku o aplikaci:</span><span class="sxs-lookup"><span data-stu-id="030f6-204">The sample app uses `AddPageRouteModelConvention` to add an `{aboutTemplate?}` route template to the About page:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet4)]

<span data-ttu-id="030f6-205">Vlastnost <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> pro <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> je nastavena na `2`.</span><span class="sxs-lookup"><span data-stu-id="030f6-205">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `2`.</span></span> <span data-ttu-id="030f6-206">Tím je zajištěno, že šablona pro `{globalTemplate?}` (nastavená dříve v tématu `1`) má prioritu první pozice hodnoty dat trasy, když je zadána hodnota jedné trasy.</span><span class="sxs-lookup"><span data-stu-id="030f6-206">This ensures that the template for `{globalTemplate?}` (set earlier in the topic to `1`) is given priority for the first route data value position when a single route value is provided.</span></span> <span data-ttu-id="030f6-207">Pokud se stránka o žádosti vyžaduje s hodnotou parametru Route na `/About/RouteDataValue`, načte se RouteDataValue do `RouteData.Values["globalTemplate"]` (`Order = 1`) a ne`RouteData.Values["aboutTemplate"]` (`Order = 2`) z důvodu nastavení vlastnosti `Order`.</span><span class="sxs-lookup"><span data-stu-id="030f6-207">If the About page is requested with a route parameter value at `/About/RouteDataValue`, "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["aboutTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="030f6-208">Pokud je to možné, nenastavte `Order`, což má za následek `Order = 0`.</span><span class="sxs-lookup"><span data-stu-id="030f6-208">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="030f6-209">Pro výběr správné trasy se spoléhá na směrování.</span><span class="sxs-lookup"><span data-stu-id="030f6-209">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="030f6-210">Požádejte o stránku ukázky o `localhost:5000/About/GlobalRouteValue/AboutRouteValue` a Prohlédněte si výsledek:</span><span class="sxs-lookup"><span data-stu-id="030f6-210">Request the sample's About page at `localhost:5000/About/GlobalRouteValue/AboutRouteValue` and inspect the result:</span></span>

![U segmentů směrování pro GlobalRouteValue a AboutRouteValue se žádá o stránku.](razor-pages-conventions/_static/about-page-global-and-about-templates.png)

## <a name="use-a-parameter-transformer-to-customize-page-routes"></a><span data-ttu-id="030f6-213">Použití transformátoru parametrů k přizpůsobení cest stránky</span><span class="sxs-lookup"><span data-stu-id="030f6-213">Use a parameter transformer to customize page routes</span></span>

<span data-ttu-id="030f6-214">Trasy stránky generované ASP.NET Core lze přizpůsobit pomocí transformátoru parametrů.</span><span class="sxs-lookup"><span data-stu-id="030f6-214">Page routes generated by ASP.NET Core can be customized using a parameter transformer.</span></span> <span data-ttu-id="030f6-215">Transformátor parametru implementuje `IOutboundParameterTransformer` a transformuje hodnotu parametrů.</span><span class="sxs-lookup"><span data-stu-id="030f6-215">A parameter transformer implements `IOutboundParameterTransformer` and transforms the value of parameters.</span></span> <span data-ttu-id="030f6-216">Například vlastní parametr `SlugifyParameterTransformer` Transformer změní hodnotu trasy `SubscriptionManagement` na `subscription-management`.</span><span class="sxs-lookup"><span data-stu-id="030f6-216">For example, a custom `SlugifyParameterTransformer` parameter transformer changes the `SubscriptionManagement` route value to `subscription-management`.</span></span>

<span data-ttu-id="030f6-217">Konvence modelu směrování na stránce `PageRouteTransformerConvention` aplikuje transformátor parametrů na složku a název souboru segmentů automaticky generovaných tras stránky v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="030f6-217">The `PageRouteTransformerConvention` page route model convention applies a parameter transformer to the folder and file name segments of automatically generated page routes in an app.</span></span> <span data-ttu-id="030f6-218">Například soubor Razor Pages v */Pages/SubscriptionManagement/ViewAll.cshtml* by měl přepsané směrování z `/SubscriptionManagement/ViewAll` na `/subscription-management/view-all`.</span><span class="sxs-lookup"><span data-stu-id="030f6-218">For example, the Razor Pages file at */Pages/SubscriptionManagement/ViewAll.cshtml* would have its route rewritten from `/SubscriptionManagement/ViewAll` to `/subscription-management/view-all`.</span></span>

<span data-ttu-id="030f6-219">`PageRouteTransformerConvention` pouze transformuje automaticky generované segmenty cesty stránky, které pocházejí ze složky Razor Pages a názvu souboru.</span><span class="sxs-lookup"><span data-stu-id="030f6-219">`PageRouteTransformerConvention` only transforms the automatically generated segments of a page route that come from the Razor Pages folder and file name.</span></span> <span data-ttu-id="030f6-220">Netransformuje segmenty směrování přidané pomocí direktivy `@page`.</span><span class="sxs-lookup"><span data-stu-id="030f6-220">It doesn't transform route segments added with the `@page` directive.</span></span> <span data-ttu-id="030f6-221">Konvence také netransformuje trasy přidané pomocí <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>.</span><span class="sxs-lookup"><span data-stu-id="030f6-221">The convention also doesn't transform routes added by <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>.</span></span>

<span data-ttu-id="030f6-222">`PageRouteTransformerConvention` je v `Startup.ConfigureServices`zaregistrován jako možnost:</span><span class="sxs-lookup"><span data-stu-id="030f6-222">The `PageRouteTransformerConvention` is registered as an option in `Startup.ConfigureServices`:</span></span>

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

## <a name="configure-a-page-route"></a><span data-ttu-id="030f6-223">Konfigurace trasy stránky</span><span class="sxs-lookup"><span data-stu-id="030f6-223">Configure a page route</span></span>

<span data-ttu-id="030f6-224">Pomocí <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> můžete nakonfigurovat směrování na stránku na zadané cestě stránky.</span><span class="sxs-lookup"><span data-stu-id="030f6-224">Use <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> to configure a route to a page at the specified page path.</span></span> <span data-ttu-id="030f6-225">Vygenerované odkazy na stránku používají zadanou trasu.</span><span class="sxs-lookup"><span data-stu-id="030f6-225">Generated links to the page use your specified route.</span></span> <span data-ttu-id="030f6-226">`AddPageRoute` používá k vytvoření trasy `AddPageRouteModelConvention`.</span><span class="sxs-lookup"><span data-stu-id="030f6-226">`AddPageRoute` uses `AddPageRouteModelConvention` to establish the route.</span></span>

<span data-ttu-id="030f6-227">Ukázková aplikace vytvoří trasu, která `/TheContactPage` pro *kontakt. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="030f6-227">The sample app creates a route to `/TheContactPage` for *Contact.cshtml*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet5)]

<span data-ttu-id="030f6-228">Stránku kontaktů lze také získat na `/Contact` prostřednictvím výchozí trasy.</span><span class="sxs-lookup"><span data-stu-id="030f6-228">The Contact page can also be reached at `/Contact` via its default route.</span></span>

<span data-ttu-id="030f6-229">Vlastní trasa ukázkové aplikace na stránce kontaktů umožňuje, aby byl `text` segment trasy (`{text?}`).</span><span class="sxs-lookup"><span data-stu-id="030f6-229">The sample app's custom route to the Contact page allows for an optional `text` route segment (`{text?}`).</span></span> <span data-ttu-id="030f6-230">Tato stránka zahrnuje také tento volitelný segment v direktivě `@page` pro případ, že návštěvník přistupuje k této stránce `/Contact` trase:</span><span class="sxs-lookup"><span data-stu-id="030f6-230">The page also includes this optional segment in its `@page` directive in case the visitor accesses the page at its `/Contact` route:</span></span>

[!code-cshtml[](razor-pages-conventions/samples/3.x/SampleApp/Pages/Contact.cshtml?highlight=1)]

<span data-ttu-id="030f6-231">Všimněte si, že adresa URL vygenerovaná pro odkaz **kontaktu** na vykreslené stránce odráží aktualizovanou trasu:</span><span class="sxs-lookup"><span data-stu-id="030f6-231">Note that the URL generated for the **Contact** link in the rendered page reflects the updated route:</span></span>

![Odkaz na ukázkový kontakt aplikace na navigačním panelu](razor-pages-conventions/_static/contact-link.png)

![Kontrola odkazu na kontakt ve vykresleném kódu HTML znamená, že odkaz href je nastaven na hodnotu "/TheContactPage".](razor-pages-conventions/_static/contact-link-source.png)

<span data-ttu-id="030f6-234">Navštivte stránku kontaktů buď na své běžné trase, `/Contact`, nebo na vlastní trasu `/TheContactPage`.</span><span class="sxs-lookup"><span data-stu-id="030f6-234">Visit the Contact page at either its ordinary route, `/Contact`, or the custom route, `/TheContactPage`.</span></span> <span data-ttu-id="030f6-235">Pokud zadáte další segment `text` trasy, stránka zobrazuje segment zakódovaný ve formátu HTML, který zadáte:</span><span class="sxs-lookup"><span data-stu-id="030f6-235">If you supply an additional `text` route segment, the page shows the HTML-encoded segment that you provide:</span></span>

![Prohlížeč Edge – příklad poskytnutí volitelného segmentu "text" trasy "TextValue" v adrese URL](razor-pages-conventions/_static/route-segment-with-custom-route.png)

## <a name="page-model-action-conventions"></a><span data-ttu-id="030f6-238">Konvence akcí modelu stránky</span><span class="sxs-lookup"><span data-stu-id="030f6-238">Page model action conventions</span></span>

<span data-ttu-id="030f6-239">Výchozí poskytovatel modelu stránky, který implementuje <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider>, vyvolá konvence, které jsou navrženy tak, aby poskytovaly body rozšiřitelnosti pro konfiguraci modelů stránek.</span><span class="sxs-lookup"><span data-stu-id="030f6-239">The default page model provider that implements <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> invokes conventions which are designed to provide extensibility points for configuring page models.</span></span> <span data-ttu-id="030f6-240">Tyto konvence jsou užitečné při sestavování a úpravách scénářů zjišťování a zpracování stránky.</span><span class="sxs-lookup"><span data-stu-id="030f6-240">These conventions are useful when building and modifying page discovery and processing scenarios.</span></span>

<span data-ttu-id="030f6-241">V příkladech v této části používá ukázková aplikace třídu `AddHeaderAttribute`, což je <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>, která používá hlavičku odpovědi:</span><span class="sxs-lookup"><span data-stu-id="030f6-241">For the examples in this section, the sample app uses an `AddHeaderAttribute` class, which is a <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>, that applies a response header:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

<span data-ttu-id="030f6-242">Pomocí konvence Ukázka ukazuje, jak použít atribut na všechny stránky ve složce a na jednu stránku.</span><span class="sxs-lookup"><span data-stu-id="030f6-242">Using conventions, the sample demonstrates how to apply the attribute to all of the pages in a folder and to a single page.</span></span>

<span data-ttu-id="030f6-243">**Konvence modelu aplikace složky**</span><span class="sxs-lookup"><span data-stu-id="030f6-243">**Folder app model convention**</span></span>

<span data-ttu-id="030f6-244">Pomocí <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> můžete vytvořit a přidat <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention>, který vyvolá akci na instancích <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> pro všechny stránky v zadané složce.</span><span class="sxs-lookup"><span data-stu-id="030f6-244">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> that invokes an action on <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> instances for all pages under the specified folder.</span></span>

<span data-ttu-id="030f6-245">Ukázka demonstruje použití `AddFolderApplicationModelConvention` přidáním hlavičky, `OtherPagesHeader`na stránky ve složce *OtherPages* v aplikaci:</span><span class="sxs-lookup"><span data-stu-id="030f6-245">The sample demonstrates the use of `AddFolderApplicationModelConvention` by adding a header, `OtherPagesHeader`, to the pages inside the *OtherPages* folder of the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet6)]

<span data-ttu-id="030f6-246">Vyžádejte si Page1 stránku ukázky na `localhost:5000/OtherPages/Page1` a Prohlédněte si hlavičky a podívejte se na výsledek:</span><span class="sxs-lookup"><span data-stu-id="030f6-246">Request the sample's Page1 page at `localhost:5000/OtherPages/Page1` and inspect the headers to view the result:</span></span>

![Hlavičky odpovědi stránky OtherPages/Page1 ukazují, že byla přidána OtherPagesHeader.](razor-pages-conventions/_static/page1-otherpages-header.png)

<span data-ttu-id="030f6-248">**Konvence modelu aplikace stránky**</span><span class="sxs-lookup"><span data-stu-id="030f6-248">**Page app model convention**</span></span>

<span data-ttu-id="030f6-249">Pomocí <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> vytvořit a přidat <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention>, který vyvolá akci na <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> pro stránku se zadaným názvem.</span><span class="sxs-lookup"><span data-stu-id="030f6-249">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> for the page with the specified name.</span></span>

<span data-ttu-id="030f6-250">Ukázka demonstruje použití `AddPageApplicationModelConvention` přidáním hlavičky, `AboutHeader`na stránku o aplikaci:</span><span class="sxs-lookup"><span data-stu-id="030f6-250">The sample demonstrates the use of `AddPageApplicationModelConvention` by adding a header, `AboutHeader`, to the About page:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet7)]

<span data-ttu-id="030f6-251">Požádejte o stránku ukázek o `localhost:5000/About` a Prohlédněte si záhlaví a podívejte se na výsledek:</span><span class="sxs-lookup"><span data-stu-id="030f6-251">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![Hlavičky odpovědi stránky o produktu ukazují, že byl přidán AboutHeader.](razor-pages-conventions/_static/about-page-about-header.png)

<span data-ttu-id="030f6-253">**Konfigurace filtru**</span><span class="sxs-lookup"><span data-stu-id="030f6-253">**Configure a filter**</span></span>

<span data-ttu-id="030f6-254"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> nakonfiguruje zadaný filtr, který se má použít.</span><span class="sxs-lookup"><span data-stu-id="030f6-254"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configures the specified filter to apply.</span></span> <span data-ttu-id="030f6-255">Můžete implementovat třídu filtru, ale ukázková aplikace ukazuje, jak implementovat filtr ve výrazu lambda, který je implementován na pozadí jako objekt pro vytváření, který vrací filtr:</span><span class="sxs-lookup"><span data-stu-id="030f6-255">You can implement a filter class, but the sample app shows how to implement a filter in a lambda expression, which is implemented behind-the-scenes as a factory that returns a filter:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet8)]

<span data-ttu-id="030f6-256">Model webové aplikace se používá ke kontrole relativní cesty pro segmenty, které vedou ke stránce Page2 ve složce *OtherPages* .</span><span class="sxs-lookup"><span data-stu-id="030f6-256">The page app model is used to check the relative path for segments that lead to the Page2 page in the *OtherPages* folder.</span></span> <span data-ttu-id="030f6-257">Pokud podmínka projde, přidá se hlavička.</span><span class="sxs-lookup"><span data-stu-id="030f6-257">If the condition passes, a header is added.</span></span> <span data-ttu-id="030f6-258">V takovém případě je `EmptyFilter` použito.</span><span class="sxs-lookup"><span data-stu-id="030f6-258">If not, the `EmptyFilter` is applied.</span></span>

<span data-ttu-id="030f6-259">`EmptyFilter` je [Filtr akcí](xref:mvc/controllers/filters#action-filters).</span><span class="sxs-lookup"><span data-stu-id="030f6-259">`EmptyFilter` is an [Action filter](xref:mvc/controllers/filters#action-filters).</span></span> <span data-ttu-id="030f6-260">Vzhledem k tomu, že jsou filtry akcí ignorovány Razor Pages, `EmptyFilter` nemá žádný vliv, pokud cesta neobsahuje `OtherPages/Page2`.</span><span class="sxs-lookup"><span data-stu-id="030f6-260">Since Action filters are ignored by Razor Pages, the `EmptyFilter` has no effect as intended if the path doesn't contain `OtherPages/Page2`.</span></span>

<span data-ttu-id="030f6-261">Vyžádejte si Page2 stránku ukázky na `localhost:5000/OtherPages/Page2` a Prohlédněte si hlavičky a podívejte se na výsledek:</span><span class="sxs-lookup"><span data-stu-id="030f6-261">Request the sample's Page2 page at `localhost:5000/OtherPages/Page2` and inspect the headers to view the result:</span></span>

![OtherPagesPage2Header se přidá do odpovědi pro Page2.](razor-pages-conventions/_static/page2-filter-header.png)

<span data-ttu-id="030f6-263">**Konfigurace objektu pro vytváření filtrů**</span><span class="sxs-lookup"><span data-stu-id="030f6-263">**Configure a filter factory**</span></span>

<span data-ttu-id="030f6-264"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> nakonfiguruje určený objekt pro vytváření a použije [filtry](xref:mvc/controllers/filters) na všechny Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="030f6-264"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configures the specified factory to apply [filters](xref:mvc/controllers/filters) to all Razor Pages.</span></span>

<span data-ttu-id="030f6-265">Ukázková aplikace poskytuje příklad použití [objektu pro vytváření filtru](xref:mvc/controllers/filters#ifilterfactory) přidáním hlavičky, `FilterFactoryHeader`se dvěma hodnotami na stránky aplikace:</span><span class="sxs-lookup"><span data-stu-id="030f6-265">The sample app provides an example of using a [filter factory](xref:mvc/controllers/filters#ifilterfactory) by adding a header, `FilterFactoryHeader`, with two values to the app's pages:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet9)]

<span data-ttu-id="030f6-266">*AddHeaderWithFactory.cs*:</span><span class="sxs-lookup"><span data-stu-id="030f6-266">*AddHeaderWithFactory.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Factories/AddHeaderWithFactory.cs?name=snippet1)]

<span data-ttu-id="030f6-267">Požádejte o stránku ukázek o `localhost:5000/About` a Prohlédněte si záhlaví a podívejte se na výsledek:</span><span class="sxs-lookup"><span data-stu-id="030f6-267">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![Hlavičky odpovědi stránky o stránku zobrazují, že byly přidány dvě hlavičky FilterFactoryHeader.](razor-pages-conventions/_static/about-page-filter-factory-header.png)

## <a name="mvc-filters-and-the-page-filter-ipagefilter"></a><span data-ttu-id="030f6-269">Filtry MVC a filtr stránky (IPageFilter)</span><span class="sxs-lookup"><span data-stu-id="030f6-269">MVC Filters and the Page filter (IPageFilter)</span></span>

<span data-ttu-id="030f6-270">[Filtry akcí](xref:mvc/controllers/filters#action-filters) MVC se Razor Pages ignorují, protože Razor Pages použít obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="030f6-270">MVC [Action filters](xref:mvc/controllers/filters#action-filters) are ignored by Razor Pages, since Razor Pages use handler methods.</span></span> <span data-ttu-id="030f6-271">Další typy filtrů MVC jsou k dispozici pro použití: [autorizace](xref:mvc/controllers/filters#authorization-filters), [výjimka](xref:mvc/controllers/filters#exception-filters), [prostředek](xref:mvc/controllers/filters#resource-filters)a [výsledek](xref:mvc/controllers/filters#result-filters).</span><span class="sxs-lookup"><span data-stu-id="030f6-271">Other types of MVC filters are available for you to use: [Authorization](xref:mvc/controllers/filters#authorization-filters), [Exception](xref:mvc/controllers/filters#exception-filters), [Resource](xref:mvc/controllers/filters#resource-filters), and [Result](xref:mvc/controllers/filters#result-filters).</span></span> <span data-ttu-id="030f6-272">Další informace najdete v tématu [filtry](xref:mvc/controllers/filters) .</span><span class="sxs-lookup"><span data-stu-id="030f6-272">For more information, see the [Filters](xref:mvc/controllers/filters) topic.</span></span>

<span data-ttu-id="030f6-273">Filtr stránky (<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>) je filtr, který se vztahuje na Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="030f6-273">The Page filter (<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>) is a filter that applies to Razor Pages.</span></span> <span data-ttu-id="030f6-274">Další informace naleznete v tématu [metody Filter pro Razor Pages](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="030f6-274">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="030f6-275">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="030f6-275">Additional resources</span></span>

* <xref:security/authorization/razor-pages-authorization>
* <xref:mvc/controllers/areas#areas-with-razor-pages>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="030f6-276">Naučte se používat [konvence směrování stránky a poskytovatele modelů aplikací](xref:mvc/controllers/application-model#conventions) k řízení směrování, zjišťování a zpracování stránky v aplikacích Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="030f6-276">Learn how to use page [route and app model provider conventions](xref:mvc/controllers/application-model#conventions) to control page routing, discovery, and processing in Razor Pages apps.</span></span>

<span data-ttu-id="030f6-277">Pokud potřebujete nakonfigurovat vlastní trasy stránky pro jednotlivé stránky, nakonfigurujte směrování na stránky pomocí [AddPageRoute konvence](#configure-a-page-route) popsané dále v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="030f6-277">When you need to configure custom page routes for individual pages, configure routing to pages with the [AddPageRoute convention](#configure-a-page-route) described later in this topic.</span></span>

<span data-ttu-id="030f6-278">Chcete-li určit trasu stránky, přidat segmenty směrování nebo přidat parametry do trasy, použijte direktivu `@page` stránky.</span><span class="sxs-lookup"><span data-stu-id="030f6-278">To specify a page route, add route segments, or add parameters to a route, use the page's `@page` directive.</span></span> <span data-ttu-id="030f6-279">Další informace najdete v tématu [vlastní trasy](xref:razor-pages/index#custom-routes).</span><span class="sxs-lookup"><span data-stu-id="030f6-279">For more information, see [Custom routes](xref:razor-pages/index#custom-routes).</span></span>

<span data-ttu-id="030f6-280">Existují vyhrazená slova, která nelze použít jako segmenty směrování nebo názvy parametrů.</span><span class="sxs-lookup"><span data-stu-id="030f6-280">There are reserved words that can't be used as route segments or parameter names.</span></span> <span data-ttu-id="030f6-281">Další informace najdete v tématu [směrování: názvy rezervovaných směrování](xref:fundamentals/routing#reserved-routing-names).</span><span class="sxs-lookup"><span data-stu-id="030f6-281">For more information, see [Routing: Reserved routing names](xref:fundamentals/routing#reserved-routing-names).</span></span>

<span data-ttu-id="030f6-282">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="030f6-282">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

| <span data-ttu-id="030f6-283">Scénář</span><span class="sxs-lookup"><span data-stu-id="030f6-283">Scenario</span></span> | <span data-ttu-id="030f6-284">Ukázka znázorňuje...</span><span class="sxs-lookup"><span data-stu-id="030f6-284">The sample demonstrates ...</span></span> |
| -------- | --------------------------- |
| [<span data-ttu-id="030f6-285">Konvence modelu</span><span class="sxs-lookup"><span data-stu-id="030f6-285">Model conventions</span></span>](#model-conventions)<br><br><span data-ttu-id="030f6-286">Konvence. Add</span><span class="sxs-lookup"><span data-stu-id="030f6-286">Conventions.Add</span></span><ul><li><span data-ttu-id="030f6-287">IPageRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="030f6-287">IPageRouteModelConvention</span></span></li><li><span data-ttu-id="030f6-288">IPageApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="030f6-288">IPageApplicationModelConvention</span></span></li><li><span data-ttu-id="030f6-289">IPageHandlerModelConvention</span><span class="sxs-lookup"><span data-stu-id="030f6-289">IPageHandlerModelConvention</span></span></li></ul> | <span data-ttu-id="030f6-290">Přidejte šablonu a hlavičku směrování na stránky aplikace.</span><span class="sxs-lookup"><span data-stu-id="030f6-290">Add a route template and header to an app's pages.</span></span> |
| [<span data-ttu-id="030f6-291">Konvence akcí při směrování stránky</span><span class="sxs-lookup"><span data-stu-id="030f6-291">Page route action conventions</span></span>](#page-route-action-conventions)<ul><li><span data-ttu-id="030f6-292">AddFolderRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="030f6-292">AddFolderRouteModelConvention</span></span></li><li><span data-ttu-id="030f6-293">AddPageRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="030f6-293">AddPageRouteModelConvention</span></span></li><li><span data-ttu-id="030f6-294">AddPageRoute</span><span class="sxs-lookup"><span data-stu-id="030f6-294">AddPageRoute</span></span></li></ul> | <span data-ttu-id="030f6-295">Přidejte šablonu směrování do stránek ve složce a na jednu stránku.</span><span class="sxs-lookup"><span data-stu-id="030f6-295">Add a route template to pages in a folder and to a single page.</span></span> |
| [<span data-ttu-id="030f6-296">Konvence akcí modelu stránky</span><span class="sxs-lookup"><span data-stu-id="030f6-296">Page model action conventions</span></span>](#page-model-action-conventions)<ul><li><span data-ttu-id="030f6-297">AddFolderApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="030f6-297">AddFolderApplicationModelConvention</span></span></li><li><span data-ttu-id="030f6-298">AddPageApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="030f6-298">AddPageApplicationModelConvention</span></span></li><li><span data-ttu-id="030f6-299">ConfigureFilter (filtrovat třídu, lambda výraz nebo objekt pro vytváření filtru)</span><span class="sxs-lookup"><span data-stu-id="030f6-299">ConfigureFilter (filter class, lambda expression, or filter factory)</span></span></li></ul> | <span data-ttu-id="030f6-300">Umožňuje přidat záhlaví na stránky ve složce, přidat záhlaví na jednu stránku a nakonfigurovat [objekt pro vytváření filtru](xref:mvc/controllers/filters#ifilterfactory) tak, aby přidal hlavičku na stránky aplikace.</span><span class="sxs-lookup"><span data-stu-id="030f6-300">Add a header to pages in a folder, add a header to a single page, and configure a [filter factory](xref:mvc/controllers/filters#ifilterfactory) to add a header to an app's pages.</span></span> |

<span data-ttu-id="030f6-301">Razor Pages konvence jsou přidány a nakonfigurovány pomocí metody rozšíření <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> pro <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> v kolekci služeb ve třídě `Startup`.</span><span class="sxs-lookup"><span data-stu-id="030f6-301">Razor Pages conventions are added and configured using the <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> extension method to <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> on the service collection in the `Startup` class.</span></span> <span data-ttu-id="030f6-302">Následující příklady konvence jsou vysvětleny dále v tomto tématu:</span><span class="sxs-lookup"><span data-stu-id="030f6-302">The following convention examples are explained later in this topic:</span></span>

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

## <a name="route-order"></a><span data-ttu-id="030f6-303">Pořadí směrování</span><span class="sxs-lookup"><span data-stu-id="030f6-303">Route order</span></span>

<span data-ttu-id="030f6-304">Trasy určují <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> pro zpracování (odpovídá směrování).</span><span class="sxs-lookup"><span data-stu-id="030f6-304">Routes specify an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> for processing (route matching).</span></span>

| <span data-ttu-id="030f6-305">Objednání</span><span class="sxs-lookup"><span data-stu-id="030f6-305">Order</span></span>            | <span data-ttu-id="030f6-306">Chování</span><span class="sxs-lookup"><span data-stu-id="030f6-306">Behavior</span></span> |
| :--------------: | -------- |
| <span data-ttu-id="030f6-307">-1</span><span class="sxs-lookup"><span data-stu-id="030f6-307">-1</span></span>               | <span data-ttu-id="030f6-308">Trasa je zpracována před zpracováním jiných tras.</span><span class="sxs-lookup"><span data-stu-id="030f6-308">The route is processed before other routes are processed.</span></span> |
| <span data-ttu-id="030f6-309">0</span><span class="sxs-lookup"><span data-stu-id="030f6-309">0</span></span>                | <span data-ttu-id="030f6-310">Pořadí není zadáno (výchozí hodnota).</span><span class="sxs-lookup"><span data-stu-id="030f6-310">Order isn't specified (default value).</span></span> <span data-ttu-id="030f6-311">Při nepřiřazování `Order` (`Order = null`) jsou výchozí hodnoty `Order` trasy na 0 (nula) pro zpracování.</span><span class="sxs-lookup"><span data-stu-id="030f6-311">Not assigning `Order` (`Order = null`) defaults the route `Order` to 0 (zero) for processing.</span></span> |
| <span data-ttu-id="030f6-312">1, 2, &hellip; n</span><span class="sxs-lookup"><span data-stu-id="030f6-312">1, 2, &hellip; n</span></span> | <span data-ttu-id="030f6-313">Určuje pořadí zpracování směrování.</span><span class="sxs-lookup"><span data-stu-id="030f6-313">Specifies the route processing order.</span></span> |

<span data-ttu-id="030f6-314">Zpracování směrování je zřízené podle konvence:</span><span class="sxs-lookup"><span data-stu-id="030f6-314">Route processing is established by convention:</span></span>

* <span data-ttu-id="030f6-315">Trasy jsou zpracovávány v sekvenčním pořadí (-1, 0, 1, 2, &hellip; n).</span><span class="sxs-lookup"><span data-stu-id="030f6-315">Routes are processed in sequential order (-1, 0, 1, 2, &hellip; n).</span></span>
* <span data-ttu-id="030f6-316">Když mají stejné trasy stejné `Order`, bude se nejprve shodovat konkrétní trasa, za kterou následuje méně specifických tras.</span><span class="sxs-lookup"><span data-stu-id="030f6-316">When routes have the same `Order`, the most specific route is matched first followed by less specific routes.</span></span>
* <span data-ttu-id="030f6-317">Když se trasy se stejnou `Order` a stejný počet parametrů shodují s adresou URL požadavku, trasy se zpracovávají v pořadí, v jakém jsou přidány do <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>.</span><span class="sxs-lookup"><span data-stu-id="030f6-317">When routes with the same `Order` and the same number of parameters match a request URL, routes are processed in the order that they're added to the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>.</span></span>

<span data-ttu-id="030f6-318">Pokud je to možné, vyhněte se v závislosti na zavedeném pořadí zpracování směrování.</span><span class="sxs-lookup"><span data-stu-id="030f6-318">If possible, avoid depending on an established route processing order.</span></span> <span data-ttu-id="030f6-319">Obecně směrování vybírá správnou trasu s odpovídající adresou URL.</span><span class="sxs-lookup"><span data-stu-id="030f6-319">Generally, routing selects the correct route with URL matching.</span></span> <span data-ttu-id="030f6-320">Pokud musíte nastavit vlastnosti směrovacích `Order` tak, aby správně směrovaly požadavky, schéma směrování aplikace je pravděpodobně matoucí pro klienty a křehké k údržbě.</span><span class="sxs-lookup"><span data-stu-id="030f6-320">If you must set route `Order` properties to route requests correctly, the app's routing scheme is probably confusing to clients and fragile to maintain.</span></span> <span data-ttu-id="030f6-321">Vyhledejte zjednodušené schéma směrování aplikace.</span><span class="sxs-lookup"><span data-stu-id="030f6-321">Seek to simplify the app's routing scheme.</span></span> <span data-ttu-id="030f6-322">Ukázková aplikace vyžaduje explicitní pořadí zpracování tras, které předvádí několik scénářů směrování pomocí jedné aplikace.</span><span class="sxs-lookup"><span data-stu-id="030f6-322">The sample app requires an explicit route processing order to demonstrate several routing scenarios using a single app.</span></span> <span data-ttu-id="030f6-323">Měli byste se však pokusit vyhnout se postupu nastavení `Order` trasy v produkčních aplikacích.</span><span class="sxs-lookup"><span data-stu-id="030f6-323">However, you should attempt to avoid the practice of setting route `Order` in production apps.</span></span>

<span data-ttu-id="030f6-324">Směrování Razor Pages směrování a řadiče MVC sdílí implementaci.</span><span class="sxs-lookup"><span data-stu-id="030f6-324">Razor Pages routing and MVC controller routing share an implementation.</span></span> <span data-ttu-id="030f6-325">Informace o pořadí směrování v tématech MVC jsou k dispozici při [směrování na akce kontroleru: trasy atributů řazení](xref:mvc/controllers/routing#ordering-attribute-routes).</span><span class="sxs-lookup"><span data-stu-id="030f6-325">Information on route order in the MVC topics is available at [Routing to controller actions: Ordering attribute routes](xref:mvc/controllers/routing#ordering-attribute-routes).</span></span>

## <a name="model-conventions"></a><span data-ttu-id="030f6-326">Konvence modelu</span><span class="sxs-lookup"><span data-stu-id="030f6-326">Model conventions</span></span>

<span data-ttu-id="030f6-327">Přidejte delegáta pro <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> pro přidání [konvencí modelu](xref:mvc/controllers/application-model#conventions) , které se vztahují na Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="030f6-327">Add a delegate for <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> to add [model conventions](xref:mvc/controllers/application-model#conventions) that apply to Razor Pages.</span></span>

### <a name="add-a-route-model-convention-to-all-pages"></a><span data-ttu-id="030f6-328">Přidat konvenci modelu směrování na všechny stránky</span><span class="sxs-lookup"><span data-stu-id="030f6-328">Add a route model convention to all pages</span></span>

<span data-ttu-id="030f6-329">Pomocí <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> můžete vytvořit a přidat <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> do kolekce instancí <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention>, které se použijí během vytváření modelu směrování stránky.</span><span class="sxs-lookup"><span data-stu-id="030f6-329">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page route model construction.</span></span>

<span data-ttu-id="030f6-330">Ukázková aplikace přidá šablonu směrování `{globalTemplate?}` na všechny stránky v aplikaci:</span><span class="sxs-lookup"><span data-stu-id="030f6-330">The sample app adds a `{globalTemplate?}` route template to all of the pages in the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

<span data-ttu-id="030f6-331">Vlastnost <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> pro <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> je nastavena na `1`.</span><span class="sxs-lookup"><span data-stu-id="030f6-331">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `1`.</span></span> <span data-ttu-id="030f6-332">Tím se zajistí následující chování při shodě trasy v ukázkové aplikaci:</span><span class="sxs-lookup"><span data-stu-id="030f6-332">This ensures the following route matching behavior in the sample app:</span></span>

* <span data-ttu-id="030f6-333">Šablona směrování pro `TheContactPage/{text?}` se přidá později v tématu.</span><span class="sxs-lookup"><span data-stu-id="030f6-333">A route template for `TheContactPage/{text?}` is added later in the topic.</span></span> <span data-ttu-id="030f6-334">Trasa stránky kontaktu má výchozí pořadí `null` (`Order = 0`), takže odpovídá před šablonou směrování `{globalTemplate?}`.</span><span class="sxs-lookup"><span data-stu-id="030f6-334">The Contact Page route has a default order of `null` (`Order = 0`), so it matches before the `{globalTemplate?}` route template.</span></span>
* <span data-ttu-id="030f6-335">Šablona směrování `{aboutTemplate?}` je přidána později v tématu.</span><span class="sxs-lookup"><span data-stu-id="030f6-335">An `{aboutTemplate?}` route template is added later in the topic.</span></span> <span data-ttu-id="030f6-336">Šabloně `{aboutTemplate?}` je předána `Order` `2`.</span><span class="sxs-lookup"><span data-stu-id="030f6-336">The `{aboutTemplate?}` template is given an `Order` of `2`.</span></span> <span data-ttu-id="030f6-337">Když se na stránce o aplikaci žádá `/About/RouteDataValue`, načtou se "RouteDataValue" do `RouteData.Values["globalTemplate"]` (`Order = 1`) a není `RouteData.Values["aboutTemplate"]` (`Order = 2`) z důvodu nastavení vlastnosti `Order`.</span><span class="sxs-lookup"><span data-stu-id="030f6-337">When the About page is requested at `/About/RouteDataValue`, "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["aboutTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>
* <span data-ttu-id="030f6-338">Šablona směrování `{otherPagesTemplate?}` je přidána později v tématu.</span><span class="sxs-lookup"><span data-stu-id="030f6-338">An `{otherPagesTemplate?}` route template is added later in the topic.</span></span> <span data-ttu-id="030f6-339">Šabloně `{otherPagesTemplate?}` je předána `Order` `2`.</span><span class="sxs-lookup"><span data-stu-id="030f6-339">The `{otherPagesTemplate?}` template is given an `Order` of `2`.</span></span> <span data-ttu-id="030f6-340">Pokud se u libovolné stránky ve složce *Pages/OtherPages* požaduje parametr trasy (například `/OtherPages/Page1/RouteDataValue`), RouteDataValue se načte do `RouteData.Values["globalTemplate"]` (`Order = 1`) a ne`RouteData.Values["otherPagesTemplate"]` (`Order = 2`) z důvodu nastavení vlastnosti `Order`.</span><span class="sxs-lookup"><span data-stu-id="030f6-340">When any page in the *Pages/OtherPages* folder is requested with a route parameter (for example, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="030f6-341">Pokud je to možné, nenastavte `Order`, což má za následek `Order = 0`.</span><span class="sxs-lookup"><span data-stu-id="030f6-341">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="030f6-342">Pro výběr správné trasy se spoléhá na směrování.</span><span class="sxs-lookup"><span data-stu-id="030f6-342">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="030f6-343">Razor Pages možnosti, jako je přidání <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>, jsou přidány při přidání MVC do kolekce služby v `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="030f6-343">Razor Pages options, such as adding <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>, are added when MVC is added to the service collection in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="030f6-344">Příklad najdete v [ukázkové aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/).</span><span class="sxs-lookup"><span data-stu-id="030f6-344">For an example, see the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/).</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="030f6-345">Požádejte o stránku ukázky o `localhost:5000/About/GlobalRouteValue` a Prohlédněte si výsledek:</span><span class="sxs-lookup"><span data-stu-id="030f6-345">Request the sample's About page at `localhost:5000/About/GlobalRouteValue` and inspect the result:</span></span>

![Stránka o aplikaci je požadována s segmentem směrování GlobalRouteValue.](razor-pages-conventions/_static/about-page-global-template.png)

### <a name="add-an-app-model-convention-to-all-pages"></a><span data-ttu-id="030f6-348">Přidání konvence modelu aplikace na všechny stránky</span><span class="sxs-lookup"><span data-stu-id="030f6-348">Add an app model convention to all pages</span></span>

<span data-ttu-id="030f6-349">Pomocí <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> můžete vytvořit a přidat <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> do kolekce instancí <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention>, které se použijí během vytváření modelu aplikace stránky.</span><span class="sxs-lookup"><span data-stu-id="030f6-349">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page app model construction.</span></span>

<span data-ttu-id="030f6-350">Chcete-li předvést tuto a další konvenci dále v tématu, ukázková aplikace obsahuje třídu `AddHeaderAttribute`.</span><span class="sxs-lookup"><span data-stu-id="030f6-350">To demonstrate this and other conventions later in the topic, the sample app includes an `AddHeaderAttribute` class.</span></span> <span data-ttu-id="030f6-351">Konstruktor třídy přijímá `name` řetězec a pole `values` řetězců.</span><span class="sxs-lookup"><span data-stu-id="030f6-351">The class constructor accepts a `name` string and a `values` string array.</span></span> <span data-ttu-id="030f6-352">Tyto hodnoty se používají ve své `OnResultExecuting` metodě pro nastavení hlavičky odpovědi.</span><span class="sxs-lookup"><span data-stu-id="030f6-352">These values are used in its `OnResultExecuting` method to set a response header.</span></span> <span data-ttu-id="030f6-353">Úplná třída je uvedena v části [konvence akcí modelu stránky](#page-model-action-conventions) dále v tématu.</span><span class="sxs-lookup"><span data-stu-id="030f6-353">The full class is shown in the [Page model action conventions](#page-model-action-conventions) section later in the topic.</span></span>

<span data-ttu-id="030f6-354">Ukázková aplikace používá třídu `AddHeaderAttribute` k přidání záhlaví, `GlobalHeader`, na všechny stránky v aplikaci:</span><span class="sxs-lookup"><span data-stu-id="030f6-354">The sample app uses the `AddHeaderAttribute` class to add a header, `GlobalHeader`, to all of the pages in the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

<span data-ttu-id="030f6-355">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="030f6-355">*Startup.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

<span data-ttu-id="030f6-356">Požádejte o stránku ukázek o `localhost:5000/About` a Prohlédněte si záhlaví a podívejte se na výsledek:</span><span class="sxs-lookup"><span data-stu-id="030f6-356">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![Hlavičky odpovědi stránky o produktu ukazují, že byl přidán GlobalHeader.](razor-pages-conventions/_static/about-page-global-header.png)

### <a name="add-a-handler-model-convention-to-all-pages"></a><span data-ttu-id="030f6-358">Přidat konvenci modelu obslužné rutiny na všechny stránky</span><span class="sxs-lookup"><span data-stu-id="030f6-358">Add a handler model convention to all pages</span></span>

<span data-ttu-id="030f6-359">Pomocí <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> můžete vytvořit a přidat <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> do kolekce instancí <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention>, které se použijí během vytváření modelu obslužné rutiny stránky.</span><span class="sxs-lookup"><span data-stu-id="030f6-359">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page handler model construction.</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalPageHandlerModelConvention.cs?name=snippet1)]

<span data-ttu-id="030f6-360">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="030f6-360">*Startup.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet10)]

## <a name="page-route-action-conventions"></a><span data-ttu-id="030f6-361">Konvence akcí při směrování stránky</span><span class="sxs-lookup"><span data-stu-id="030f6-361">Page route action conventions</span></span>

<span data-ttu-id="030f6-362">Výchozí zprostředkovatel modelu směrování odvozený od <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> vyvolá konvence, které jsou navržené tak, aby poskytovaly body rozšiřitelnosti pro konfiguraci tras stránky.</span><span class="sxs-lookup"><span data-stu-id="030f6-362">The default route model provider that derives from <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> invokes conventions which are designed to provide extensibility points for configuring page routes.</span></span>

### <a name="folder-route-model-convention"></a><span data-ttu-id="030f6-363">Konvence modelu směrování složky</span><span class="sxs-lookup"><span data-stu-id="030f6-363">Folder route model convention</span></span>

<span data-ttu-id="030f6-364">Pomocí <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> můžete vytvořit a přidat <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention>, který vyvolá akci na <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> pro všechny stránky v zadané složce.</span><span class="sxs-lookup"><span data-stu-id="030f6-364">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> for all of the pages under the specified folder.</span></span>

<span data-ttu-id="030f6-365">Ukázková aplikace používá <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> k přidání šablony trasy `{otherPagesTemplate?}` na stránky ve složce *OtherPages* :</span><span class="sxs-lookup"><span data-stu-id="030f6-365">The sample app uses <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> to add an `{otherPagesTemplate?}` route template to the pages in the *OtherPages* folder:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

<span data-ttu-id="030f6-366">Vlastnost <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> pro <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> je nastavena na `2`.</span><span class="sxs-lookup"><span data-stu-id="030f6-366">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `2`.</span></span> <span data-ttu-id="030f6-367">Tím je zajištěno, že šablona pro `{globalTemplate?}` (nastavená dříve v tématu `1`) má prioritu první pozice hodnoty dat trasy, když je zadána hodnota jedné trasy.</span><span class="sxs-lookup"><span data-stu-id="030f6-367">This ensures that the template for `{globalTemplate?}` (set earlier in the topic to `1`) is given priority for the first route data value position when a single route value is provided.</span></span> <span data-ttu-id="030f6-368">Pokud je stránka ve složce *Pages/OtherPages* požadována s hodnotou parametru trasy (například `/OtherPages/Page1/RouteDataValue`), je "RouteDataValue" načteno do `RouteData.Values["globalTemplate"]` (`Order = 1`) a není `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) z důvodu nastavení vlastnosti `Order`.</span><span class="sxs-lookup"><span data-stu-id="030f6-368">If a page in the *Pages/OtherPages* folder is requested with a route parameter value (for example, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="030f6-369">Pokud je to možné, nenastavte `Order`, což má za následek `Order = 0`.</span><span class="sxs-lookup"><span data-stu-id="030f6-369">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="030f6-370">Pro výběr správné trasy se spoléhá na směrování.</span><span class="sxs-lookup"><span data-stu-id="030f6-370">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="030f6-371">Vyžádejte si Page1 stránku ukázky na `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` a Prohlédněte si výsledek:</span><span class="sxs-lookup"><span data-stu-id="030f6-371">Request the sample's Page1 page at `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` and inspect the result:</span></span>

![Page1 ve složce OtherPages se vyžádá s segmentem směrování GlobalRouteValue a OtherPagesRouteValue.](razor-pages-conventions/_static/otherpages-page1-global-and-otherpages-templates.png)

### <a name="page-route-model-convention"></a><span data-ttu-id="030f6-374">Konvence modelu směrování stránky</span><span class="sxs-lookup"><span data-stu-id="030f6-374">Page route model convention</span></span>

<span data-ttu-id="030f6-375">Pomocí <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> vytvořit a přidat <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention>, který vyvolá akci na <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> pro stránku se zadaným názvem.</span><span class="sxs-lookup"><span data-stu-id="030f6-375">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> for the page with the specified name.</span></span>

<span data-ttu-id="030f6-376">Ukázková aplikace používá `AddPageRouteModelConvention` k přidání šablony trasy `{aboutTemplate?}` na stránku o aplikaci:</span><span class="sxs-lookup"><span data-stu-id="030f6-376">The sample app uses `AddPageRouteModelConvention` to add an `{aboutTemplate?}` route template to the About page:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

<span data-ttu-id="030f6-377">Vlastnost <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> pro <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> je nastavena na `2`.</span><span class="sxs-lookup"><span data-stu-id="030f6-377">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `2`.</span></span> <span data-ttu-id="030f6-378">Tím je zajištěno, že šablona pro `{globalTemplate?}` (nastavená dříve v tématu `1`) má prioritu první pozice hodnoty dat trasy, když je zadána hodnota jedné trasy.</span><span class="sxs-lookup"><span data-stu-id="030f6-378">This ensures that the template for `{globalTemplate?}` (set earlier in the topic to `1`) is given priority for the first route data value position when a single route value is provided.</span></span> <span data-ttu-id="030f6-379">Pokud se stránka o žádosti vyžaduje s hodnotou parametru Route na `/About/RouteDataValue`, načte se RouteDataValue do `RouteData.Values["globalTemplate"]` (`Order = 1`) a ne`RouteData.Values["aboutTemplate"]` (`Order = 2`) z důvodu nastavení vlastnosti `Order`.</span><span class="sxs-lookup"><span data-stu-id="030f6-379">If the About page is requested with a route parameter value at `/About/RouteDataValue`, "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["aboutTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="030f6-380">Pokud je to možné, nenastavte `Order`, což má za následek `Order = 0`.</span><span class="sxs-lookup"><span data-stu-id="030f6-380">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="030f6-381">Pro výběr správné trasy se spoléhá na směrování.</span><span class="sxs-lookup"><span data-stu-id="030f6-381">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="030f6-382">Požádejte o stránku ukázky o `localhost:5000/About/GlobalRouteValue/AboutRouteValue` a Prohlédněte si výsledek:</span><span class="sxs-lookup"><span data-stu-id="030f6-382">Request the sample's About page at `localhost:5000/About/GlobalRouteValue/AboutRouteValue` and inspect the result:</span></span>

![U segmentů směrování pro GlobalRouteValue a AboutRouteValue se žádá o stránku.](razor-pages-conventions/_static/about-page-global-and-about-templates.png)

## <a name="use-a-parameter-transformer-to-customize-page-routes"></a><span data-ttu-id="030f6-385">Použití transformátoru parametrů k přizpůsobení cest stránky</span><span class="sxs-lookup"><span data-stu-id="030f6-385">Use a parameter transformer to customize page routes</span></span>

<span data-ttu-id="030f6-386">Trasy stránky generované ASP.NET Core lze přizpůsobit pomocí transformátoru parametrů.</span><span class="sxs-lookup"><span data-stu-id="030f6-386">Page routes generated by ASP.NET Core can be customized using a parameter transformer.</span></span> <span data-ttu-id="030f6-387">Transformátor parametru implementuje `IOutboundParameterTransformer` a transformuje hodnotu parametrů.</span><span class="sxs-lookup"><span data-stu-id="030f6-387">A parameter transformer implements `IOutboundParameterTransformer` and transforms the value of parameters.</span></span> <span data-ttu-id="030f6-388">Například vlastní parametr `SlugifyParameterTransformer` Transformer změní hodnotu trasy `SubscriptionManagement` na `subscription-management`.</span><span class="sxs-lookup"><span data-stu-id="030f6-388">For example, a custom `SlugifyParameterTransformer` parameter transformer changes the `SubscriptionManagement` route value to `subscription-management`.</span></span>

<span data-ttu-id="030f6-389">Konvence modelu směrování na stránce `PageRouteTransformerConvention` aplikuje transformátor parametrů na složku a název souboru segmentů automaticky generovaných tras stránky v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="030f6-389">The `PageRouteTransformerConvention` page route model convention applies a parameter transformer to the folder and file name segments of automatically generated page routes in an app.</span></span> <span data-ttu-id="030f6-390">Například soubor Razor Pages v */Pages/SubscriptionManagement/ViewAll.cshtml* by měl přepsané směrování z `/SubscriptionManagement/ViewAll` na `/subscription-management/view-all`.</span><span class="sxs-lookup"><span data-stu-id="030f6-390">For example, the Razor Pages file at */Pages/SubscriptionManagement/ViewAll.cshtml* would have its route rewritten from `/SubscriptionManagement/ViewAll` to `/subscription-management/view-all`.</span></span>

<span data-ttu-id="030f6-391">`PageRouteTransformerConvention` pouze transformuje automaticky generované segmenty cesty stránky, které pocházejí ze složky Razor Pages a názvu souboru.</span><span class="sxs-lookup"><span data-stu-id="030f6-391">`PageRouteTransformerConvention` only transforms the automatically generated segments of a page route that come from the Razor Pages folder and file name.</span></span> <span data-ttu-id="030f6-392">Netransformuje segmenty směrování přidané pomocí direktivy `@page`.</span><span class="sxs-lookup"><span data-stu-id="030f6-392">It doesn't transform route segments added with the `@page` directive.</span></span> <span data-ttu-id="030f6-393">Konvence také netransformuje trasy přidané pomocí <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>.</span><span class="sxs-lookup"><span data-stu-id="030f6-393">The convention also doesn't transform routes added by <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>.</span></span>

<span data-ttu-id="030f6-394">`PageRouteTransformerConvention` je v `Startup.ConfigureServices`zaregistrován jako možnost:</span><span class="sxs-lookup"><span data-stu-id="030f6-394">The `PageRouteTransformerConvention` is registered as an option in `Startup.ConfigureServices`:</span></span>

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

## <a name="configure-a-page-route"></a><span data-ttu-id="030f6-395">Konfigurace trasy stránky</span><span class="sxs-lookup"><span data-stu-id="030f6-395">Configure a page route</span></span>

<span data-ttu-id="030f6-396">Pomocí <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> můžete nakonfigurovat směrování na stránku na zadané cestě stránky.</span><span class="sxs-lookup"><span data-stu-id="030f6-396">Use <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> to configure a route to a page at the specified page path.</span></span> <span data-ttu-id="030f6-397">Vygenerované odkazy na stránku používají zadanou trasu.</span><span class="sxs-lookup"><span data-stu-id="030f6-397">Generated links to the page use your specified route.</span></span> <span data-ttu-id="030f6-398">`AddPageRoute` používá k vytvoření trasy `AddPageRouteModelConvention`.</span><span class="sxs-lookup"><span data-stu-id="030f6-398">`AddPageRoute` uses `AddPageRouteModelConvention` to establish the route.</span></span>

<span data-ttu-id="030f6-399">Ukázková aplikace vytvoří trasu, která `/TheContactPage` pro *kontakt. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="030f6-399">The sample app creates a route to `/TheContactPage` for *Contact.cshtml*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet5)]

<span data-ttu-id="030f6-400">Stránku kontaktů lze také získat na `/Contact` prostřednictvím výchozí trasy.</span><span class="sxs-lookup"><span data-stu-id="030f6-400">The Contact page can also be reached at `/Contact` via its default route.</span></span>

<span data-ttu-id="030f6-401">Vlastní trasa ukázkové aplikace na stránce kontaktů umožňuje, aby byl `text` segment trasy (`{text?}`).</span><span class="sxs-lookup"><span data-stu-id="030f6-401">The sample app's custom route to the Contact page allows for an optional `text` route segment (`{text?}`).</span></span> <span data-ttu-id="030f6-402">Tato stránka zahrnuje také tento volitelný segment v direktivě `@page` pro případ, že návštěvník přistupuje k této stránce `/Contact` trase:</span><span class="sxs-lookup"><span data-stu-id="030f6-402">The page also includes this optional segment in its `@page` directive in case the visitor accesses the page at its `/Contact` route:</span></span>

[!code-cshtml[](razor-pages-conventions/samples/2.x/SampleApp/Pages/Contact.cshtml?highlight=1)]

<span data-ttu-id="030f6-403">Všimněte si, že adresa URL vygenerovaná pro odkaz **kontaktu** na vykreslené stránce odráží aktualizovanou trasu:</span><span class="sxs-lookup"><span data-stu-id="030f6-403">Note that the URL generated for the **Contact** link in the rendered page reflects the updated route:</span></span>

![Odkaz na ukázkový kontakt aplikace na navigačním panelu](razor-pages-conventions/_static/contact-link.png)

![Kontrola odkazu na kontakt ve vykresleném kódu HTML znamená, že odkaz href je nastaven na hodnotu "/TheContactPage".](razor-pages-conventions/_static/contact-link-source.png)

<span data-ttu-id="030f6-406">Navštivte stránku kontaktů buď na své běžné trase, `/Contact`, nebo na vlastní trasu `/TheContactPage`.</span><span class="sxs-lookup"><span data-stu-id="030f6-406">Visit the Contact page at either its ordinary route, `/Contact`, or the custom route, `/TheContactPage`.</span></span> <span data-ttu-id="030f6-407">Pokud zadáte další segment `text` trasy, stránka zobrazuje segment zakódovaný ve formátu HTML, který zadáte:</span><span class="sxs-lookup"><span data-stu-id="030f6-407">If you supply an additional `text` route segment, the page shows the HTML-encoded segment that you provide:</span></span>

![Prohlížeč Edge – příklad poskytnutí volitelného segmentu "text" trasy "TextValue" v adrese URL](razor-pages-conventions/_static/route-segment-with-custom-route.png)

## <a name="page-model-action-conventions"></a><span data-ttu-id="030f6-410">Konvence akcí modelu stránky</span><span class="sxs-lookup"><span data-stu-id="030f6-410">Page model action conventions</span></span>

<span data-ttu-id="030f6-411">Výchozí poskytovatel modelu stránky, který implementuje <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider>, vyvolá konvence, které jsou navrženy tak, aby poskytovaly body rozšiřitelnosti pro konfiguraci modelů stránek.</span><span class="sxs-lookup"><span data-stu-id="030f6-411">The default page model provider that implements <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> invokes conventions which are designed to provide extensibility points for configuring page models.</span></span> <span data-ttu-id="030f6-412">Tyto konvence jsou užitečné při sestavování a úpravách scénářů zjišťování a zpracování stránky.</span><span class="sxs-lookup"><span data-stu-id="030f6-412">These conventions are useful when building and modifying page discovery and processing scenarios.</span></span>

<span data-ttu-id="030f6-413">V příkladech v této části používá ukázková aplikace třídu `AddHeaderAttribute`, což je <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>, která používá hlavičku odpovědi:</span><span class="sxs-lookup"><span data-stu-id="030f6-413">For the examples in this section, the sample app uses an `AddHeaderAttribute` class, which is a <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>, that applies a response header:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

<span data-ttu-id="030f6-414">Pomocí konvence Ukázka ukazuje, jak použít atribut na všechny stránky ve složce a na jednu stránku.</span><span class="sxs-lookup"><span data-stu-id="030f6-414">Using conventions, the sample demonstrates how to apply the attribute to all of the pages in a folder and to a single page.</span></span>

<span data-ttu-id="030f6-415">**Konvence modelu aplikace složky**</span><span class="sxs-lookup"><span data-stu-id="030f6-415">**Folder app model convention**</span></span>

<span data-ttu-id="030f6-416">Pomocí <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> můžete vytvořit a přidat <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention>, který vyvolá akci na instancích <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> pro všechny stránky v zadané složce.</span><span class="sxs-lookup"><span data-stu-id="030f6-416">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> that invokes an action on <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> instances for all pages under the specified folder.</span></span>

<span data-ttu-id="030f6-417">Ukázka demonstruje použití `AddFolderApplicationModelConvention` přidáním hlavičky, `OtherPagesHeader`na stránky ve složce *OtherPages* v aplikaci:</span><span class="sxs-lookup"><span data-stu-id="030f6-417">The sample demonstrates the use of `AddFolderApplicationModelConvention` by adding a header, `OtherPagesHeader`, to the pages inside the *OtherPages* folder of the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet6)]

<span data-ttu-id="030f6-418">Vyžádejte si Page1 stránku ukázky na `localhost:5000/OtherPages/Page1` a Prohlédněte si hlavičky a podívejte se na výsledek:</span><span class="sxs-lookup"><span data-stu-id="030f6-418">Request the sample's Page1 page at `localhost:5000/OtherPages/Page1` and inspect the headers to view the result:</span></span>

![Hlavičky odpovědi stránky OtherPages/Page1 ukazují, že byla přidána OtherPagesHeader.](razor-pages-conventions/_static/page1-otherpages-header.png)

<span data-ttu-id="030f6-420">**Konvence modelu aplikace stránky**</span><span class="sxs-lookup"><span data-stu-id="030f6-420">**Page app model convention**</span></span>

<span data-ttu-id="030f6-421">Pomocí <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> vytvořit a přidat <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention>, který vyvolá akci na <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> pro stránku se zadaným názvem.</span><span class="sxs-lookup"><span data-stu-id="030f6-421">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> for the page with the specified name.</span></span>

<span data-ttu-id="030f6-422">Ukázka demonstruje použití `AddPageApplicationModelConvention` přidáním hlavičky, `AboutHeader`na stránku o aplikaci:</span><span class="sxs-lookup"><span data-stu-id="030f6-422">The sample demonstrates the use of `AddPageApplicationModelConvention` by adding a header, `AboutHeader`, to the About page:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet7)]

<span data-ttu-id="030f6-423">Požádejte o stránku ukázek o `localhost:5000/About` a Prohlédněte si záhlaví a podívejte se na výsledek:</span><span class="sxs-lookup"><span data-stu-id="030f6-423">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![Hlavičky odpovědi stránky o produktu ukazují, že byl přidán AboutHeader.](razor-pages-conventions/_static/about-page-about-header.png)

<span data-ttu-id="030f6-425">**Konfigurace filtru**</span><span class="sxs-lookup"><span data-stu-id="030f6-425">**Configure a filter**</span></span>

<span data-ttu-id="030f6-426"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> nakonfiguruje zadaný filtr, který se má použít.</span><span class="sxs-lookup"><span data-stu-id="030f6-426"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configures the specified filter to apply.</span></span> <span data-ttu-id="030f6-427">Můžete implementovat třídu filtru, ale ukázková aplikace ukazuje, jak implementovat filtr ve výrazu lambda, který je implementován na pozadí jako objekt pro vytváření, který vrací filtr:</span><span class="sxs-lookup"><span data-stu-id="030f6-427">You can implement a filter class, but the sample app shows how to implement a filter in a lambda expression, which is implemented behind-the-scenes as a factory that returns a filter:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet8)]

<span data-ttu-id="030f6-428">Model webové aplikace se používá ke kontrole relativní cesty pro segmenty, které vedou ke stránce Page2 ve složce *OtherPages* .</span><span class="sxs-lookup"><span data-stu-id="030f6-428">The page app model is used to check the relative path for segments that lead to the Page2 page in the *OtherPages* folder.</span></span> <span data-ttu-id="030f6-429">Pokud podmínka projde, přidá se hlavička.</span><span class="sxs-lookup"><span data-stu-id="030f6-429">If the condition passes, a header is added.</span></span> <span data-ttu-id="030f6-430">V takovém případě je `EmptyFilter` použito.</span><span class="sxs-lookup"><span data-stu-id="030f6-430">If not, the `EmptyFilter` is applied.</span></span>

<span data-ttu-id="030f6-431">`EmptyFilter` je [Filtr akcí](xref:mvc/controllers/filters#action-filters).</span><span class="sxs-lookup"><span data-stu-id="030f6-431">`EmptyFilter` is an [Action filter](xref:mvc/controllers/filters#action-filters).</span></span> <span data-ttu-id="030f6-432">Vzhledem k tomu, že jsou filtry akcí ignorovány Razor Pages, `EmptyFilter` nemá žádný vliv, pokud cesta neobsahuje `OtherPages/Page2`.</span><span class="sxs-lookup"><span data-stu-id="030f6-432">Since Action filters are ignored by Razor Pages, the `EmptyFilter` has no effect as intended if the path doesn't contain `OtherPages/Page2`.</span></span>

<span data-ttu-id="030f6-433">Vyžádejte si Page2 stránku ukázky na `localhost:5000/OtherPages/Page2` a Prohlédněte si hlavičky a podívejte se na výsledek:</span><span class="sxs-lookup"><span data-stu-id="030f6-433">Request the sample's Page2 page at `localhost:5000/OtherPages/Page2` and inspect the headers to view the result:</span></span>

![OtherPagesPage2Header se přidá do odpovědi pro Page2.](razor-pages-conventions/_static/page2-filter-header.png)

<span data-ttu-id="030f6-435">**Konfigurace objektu pro vytváření filtrů**</span><span class="sxs-lookup"><span data-stu-id="030f6-435">**Configure a filter factory**</span></span>

<span data-ttu-id="030f6-436"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> nakonfiguruje určený objekt pro vytváření a použije [filtry](xref:mvc/controllers/filters) na všechny Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="030f6-436"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configures the specified factory to apply [filters](xref:mvc/controllers/filters) to all Razor Pages.</span></span>

<span data-ttu-id="030f6-437">Ukázková aplikace poskytuje příklad použití [objektu pro vytváření filtru](xref:mvc/controllers/filters#ifilterfactory) přidáním hlavičky, `FilterFactoryHeader`se dvěma hodnotami na stránky aplikace:</span><span class="sxs-lookup"><span data-stu-id="030f6-437">The sample app provides an example of using a [filter factory](xref:mvc/controllers/filters#ifilterfactory) by adding a header, `FilterFactoryHeader`, with two values to the app's pages:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet9)]

<span data-ttu-id="030f6-438">*AddHeaderWithFactory.cs*:</span><span class="sxs-lookup"><span data-stu-id="030f6-438">*AddHeaderWithFactory.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Factories/AddHeaderWithFactory.cs?name=snippet1)]

<span data-ttu-id="030f6-439">Požádejte o stránku ukázek o `localhost:5000/About` a Prohlédněte si záhlaví a podívejte se na výsledek:</span><span class="sxs-lookup"><span data-stu-id="030f6-439">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![Hlavičky odpovědi stránky o stránku zobrazují, že byly přidány dvě hlavičky FilterFactoryHeader.](razor-pages-conventions/_static/about-page-filter-factory-header.png)

## <a name="mvc-filters-and-the-page-filter-ipagefilter"></a><span data-ttu-id="030f6-441">Filtry MVC a filtr stránky (IPageFilter)</span><span class="sxs-lookup"><span data-stu-id="030f6-441">MVC Filters and the Page filter (IPageFilter)</span></span>

<span data-ttu-id="030f6-442">[Filtry akcí](xref:mvc/controllers/filters#action-filters) MVC se Razor Pages ignorují, protože Razor Pages použít obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="030f6-442">MVC [Action filters](xref:mvc/controllers/filters#action-filters) are ignored by Razor Pages, since Razor Pages use handler methods.</span></span> <span data-ttu-id="030f6-443">Další typy filtrů MVC jsou k dispozici pro použití: [autorizace](xref:mvc/controllers/filters#authorization-filters), [výjimka](xref:mvc/controllers/filters#exception-filters), [prostředek](xref:mvc/controllers/filters#resource-filters)a [výsledek](xref:mvc/controllers/filters#result-filters).</span><span class="sxs-lookup"><span data-stu-id="030f6-443">Other types of MVC filters are available for you to use: [Authorization](xref:mvc/controllers/filters#authorization-filters), [Exception](xref:mvc/controllers/filters#exception-filters), [Resource](xref:mvc/controllers/filters#resource-filters), and [Result](xref:mvc/controllers/filters#result-filters).</span></span> <span data-ttu-id="030f6-444">Další informace najdete v tématu [filtry](xref:mvc/controllers/filters) .</span><span class="sxs-lookup"><span data-stu-id="030f6-444">For more information, see the [Filters](xref:mvc/controllers/filters) topic.</span></span>

<span data-ttu-id="030f6-445">Filtr stránky (<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>) je filtr, který se vztahuje na Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="030f6-445">The Page filter (<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>) is a filter that applies to Razor Pages.</span></span> <span data-ttu-id="030f6-446">Další informace naleznete v tématu [metody Filter pro Razor Pages](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="030f6-446">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="030f6-447">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="030f6-447">Additional resources</span></span>

* <xref:security/authorization/razor-pages-authorization>
* <xref:mvc/controllers/areas#areas-with-razor-pages>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="030f6-448">Naučte se používat [konvence směrování stránky a poskytovatele modelů aplikací](xref:mvc/controllers/application-model#conventions) k řízení směrování, zjišťování a zpracování stránky v aplikacích Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="030f6-448">Learn how to use page [route and app model provider conventions](xref:mvc/controllers/application-model#conventions) to control page routing, discovery, and processing in Razor Pages apps.</span></span>

<span data-ttu-id="030f6-449">Pokud potřebujete nakonfigurovat vlastní trasy stránky pro jednotlivé stránky, nakonfigurujte směrování na stránky pomocí [AddPageRoute konvence](#configure-a-page-route) popsané dále v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="030f6-449">When you need to configure custom page routes for individual pages, configure routing to pages with the [AddPageRoute convention](#configure-a-page-route) described later in this topic.</span></span>

<span data-ttu-id="030f6-450">Chcete-li určit trasu stránky, přidat segmenty směrování nebo přidat parametry do trasy, použijte direktivu `@page` stránky.</span><span class="sxs-lookup"><span data-stu-id="030f6-450">To specify a page route, add route segments, or add parameters to a route, use the page's `@page` directive.</span></span> <span data-ttu-id="030f6-451">Další informace najdete v tématu [vlastní trasy](xref:razor-pages/index#custom-routes).</span><span class="sxs-lookup"><span data-stu-id="030f6-451">For more information, see [Custom routes](xref:razor-pages/index#custom-routes).</span></span>

<span data-ttu-id="030f6-452">Existují vyhrazená slova, která nelze použít jako segmenty směrování nebo názvy parametrů.</span><span class="sxs-lookup"><span data-stu-id="030f6-452">There are reserved words that can't be used as route segments or parameter names.</span></span> <span data-ttu-id="030f6-453">Další informace najdete v tématu [směrování: názvy rezervovaných směrování](xref:fundamentals/routing#reserved-routing-names).</span><span class="sxs-lookup"><span data-stu-id="030f6-453">For more information, see [Routing: Reserved routing names](xref:fundamentals/routing#reserved-routing-names).</span></span>

<span data-ttu-id="030f6-454">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="030f6-454">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

| <span data-ttu-id="030f6-455">Scénář</span><span class="sxs-lookup"><span data-stu-id="030f6-455">Scenario</span></span> | <span data-ttu-id="030f6-456">Ukázka znázorňuje...</span><span class="sxs-lookup"><span data-stu-id="030f6-456">The sample demonstrates ...</span></span> |
| -------- | --------------------------- |
| [<span data-ttu-id="030f6-457">Konvence modelu</span><span class="sxs-lookup"><span data-stu-id="030f6-457">Model conventions</span></span>](#model-conventions)<br><br><span data-ttu-id="030f6-458">Konvence. Add</span><span class="sxs-lookup"><span data-stu-id="030f6-458">Conventions.Add</span></span><ul><li><span data-ttu-id="030f6-459">IPageRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="030f6-459">IPageRouteModelConvention</span></span></li><li><span data-ttu-id="030f6-460">IPageApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="030f6-460">IPageApplicationModelConvention</span></span></li><li><span data-ttu-id="030f6-461">IPageHandlerModelConvention</span><span class="sxs-lookup"><span data-stu-id="030f6-461">IPageHandlerModelConvention</span></span></li></ul> | <span data-ttu-id="030f6-462">Přidejte šablonu a hlavičku směrování na stránky aplikace.</span><span class="sxs-lookup"><span data-stu-id="030f6-462">Add a route template and header to an app's pages.</span></span> |
| [<span data-ttu-id="030f6-463">Konvence akcí při směrování stránky</span><span class="sxs-lookup"><span data-stu-id="030f6-463">Page route action conventions</span></span>](#page-route-action-conventions)<ul><li><span data-ttu-id="030f6-464">AddFolderRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="030f6-464">AddFolderRouteModelConvention</span></span></li><li><span data-ttu-id="030f6-465">AddPageRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="030f6-465">AddPageRouteModelConvention</span></span></li><li><span data-ttu-id="030f6-466">AddPageRoute</span><span class="sxs-lookup"><span data-stu-id="030f6-466">AddPageRoute</span></span></li></ul> | <span data-ttu-id="030f6-467">Přidejte šablonu směrování do stránek ve složce a na jednu stránku.</span><span class="sxs-lookup"><span data-stu-id="030f6-467">Add a route template to pages in a folder and to a single page.</span></span> |
| [<span data-ttu-id="030f6-468">Konvence akcí modelu stránky</span><span class="sxs-lookup"><span data-stu-id="030f6-468">Page model action conventions</span></span>](#page-model-action-conventions)<ul><li><span data-ttu-id="030f6-469">AddFolderApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="030f6-469">AddFolderApplicationModelConvention</span></span></li><li><span data-ttu-id="030f6-470">AddPageApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="030f6-470">AddPageApplicationModelConvention</span></span></li><li><span data-ttu-id="030f6-471">ConfigureFilter (filtrovat třídu, lambda výraz nebo objekt pro vytváření filtru)</span><span class="sxs-lookup"><span data-stu-id="030f6-471">ConfigureFilter (filter class, lambda expression, or filter factory)</span></span></li></ul> | <span data-ttu-id="030f6-472">Umožňuje přidat záhlaví na stránky ve složce, přidat záhlaví na jednu stránku a nakonfigurovat [objekt pro vytváření filtru](xref:mvc/controllers/filters#ifilterfactory) tak, aby přidal hlavičku na stránky aplikace.</span><span class="sxs-lookup"><span data-stu-id="030f6-472">Add a header to pages in a folder, add a header to a single page, and configure a [filter factory](xref:mvc/controllers/filters#ifilterfactory) to add a header to an app's pages.</span></span> |

<span data-ttu-id="030f6-473">Razor Pages konvence jsou přidány a nakonfigurovány pomocí metody rozšíření <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> pro <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> v kolekci služeb ve třídě `Startup`.</span><span class="sxs-lookup"><span data-stu-id="030f6-473">Razor Pages conventions are added and configured using the <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> extension method to <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> on the service collection in the `Startup` class.</span></span> <span data-ttu-id="030f6-474">Následující příklady konvence jsou vysvětleny dále v tomto tématu:</span><span class="sxs-lookup"><span data-stu-id="030f6-474">The following convention examples are explained later in this topic:</span></span>

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

## <a name="route-order"></a><span data-ttu-id="030f6-475">Pořadí směrování</span><span class="sxs-lookup"><span data-stu-id="030f6-475">Route order</span></span>

<span data-ttu-id="030f6-476">Trasy určují <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> pro zpracování (odpovídá směrování).</span><span class="sxs-lookup"><span data-stu-id="030f6-476">Routes specify an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> for processing (route matching).</span></span>

| <span data-ttu-id="030f6-477">Objednání</span><span class="sxs-lookup"><span data-stu-id="030f6-477">Order</span></span>            | <span data-ttu-id="030f6-478">Chování</span><span class="sxs-lookup"><span data-stu-id="030f6-478">Behavior</span></span> |
| :--------------: | -------- |
| <span data-ttu-id="030f6-479">-1</span><span class="sxs-lookup"><span data-stu-id="030f6-479">-1</span></span>               | <span data-ttu-id="030f6-480">Trasa je zpracována před zpracováním jiných tras.</span><span class="sxs-lookup"><span data-stu-id="030f6-480">The route is processed before other routes are processed.</span></span> |
| <span data-ttu-id="030f6-481">0</span><span class="sxs-lookup"><span data-stu-id="030f6-481">0</span></span>                | <span data-ttu-id="030f6-482">Pořadí není zadáno (výchozí hodnota).</span><span class="sxs-lookup"><span data-stu-id="030f6-482">Order isn't specified (default value).</span></span> <span data-ttu-id="030f6-483">Při nepřiřazování `Order` (`Order = null`) jsou výchozí hodnoty `Order` trasy na 0 (nula) pro zpracování.</span><span class="sxs-lookup"><span data-stu-id="030f6-483">Not assigning `Order` (`Order = null`) defaults the route `Order` to 0 (zero) for processing.</span></span> |
| <span data-ttu-id="030f6-484">1, 2, &hellip; n</span><span class="sxs-lookup"><span data-stu-id="030f6-484">1, 2, &hellip; n</span></span> | <span data-ttu-id="030f6-485">Určuje pořadí zpracování směrování.</span><span class="sxs-lookup"><span data-stu-id="030f6-485">Specifies the route processing order.</span></span> |

<span data-ttu-id="030f6-486">Zpracování směrování je zřízené podle konvence:</span><span class="sxs-lookup"><span data-stu-id="030f6-486">Route processing is established by convention:</span></span>

* <span data-ttu-id="030f6-487">Trasy jsou zpracovávány v sekvenčním pořadí (-1, 0, 1, 2, &hellip; n).</span><span class="sxs-lookup"><span data-stu-id="030f6-487">Routes are processed in sequential order (-1, 0, 1, 2, &hellip; n).</span></span>
* <span data-ttu-id="030f6-488">Když mají stejné trasy stejné `Order`, bude se nejprve shodovat konkrétní trasa, za kterou následuje méně specifických tras.</span><span class="sxs-lookup"><span data-stu-id="030f6-488">When routes have the same `Order`, the most specific route is matched first followed by less specific routes.</span></span>
* <span data-ttu-id="030f6-489">Když se trasy se stejnou `Order` a stejný počet parametrů shodují s adresou URL požadavku, trasy se zpracovávají v pořadí, v jakém jsou přidány do <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>.</span><span class="sxs-lookup"><span data-stu-id="030f6-489">When routes with the same `Order` and the same number of parameters match a request URL, routes are processed in the order that they're added to the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>.</span></span>

<span data-ttu-id="030f6-490">Pokud je to možné, vyhněte se v závislosti na zavedeném pořadí zpracování směrování.</span><span class="sxs-lookup"><span data-stu-id="030f6-490">If possible, avoid depending on an established route processing order.</span></span> <span data-ttu-id="030f6-491">Obecně směrování vybírá správnou trasu s odpovídající adresou URL.</span><span class="sxs-lookup"><span data-stu-id="030f6-491">Generally, routing selects the correct route with URL matching.</span></span> <span data-ttu-id="030f6-492">Pokud musíte nastavit vlastnosti směrovacích `Order` tak, aby správně směrovaly požadavky, schéma směrování aplikace je pravděpodobně matoucí pro klienty a křehké k údržbě.</span><span class="sxs-lookup"><span data-stu-id="030f6-492">If you must set route `Order` properties to route requests correctly, the app's routing scheme is probably confusing to clients and fragile to maintain.</span></span> <span data-ttu-id="030f6-493">Vyhledejte zjednodušené schéma směrování aplikace.</span><span class="sxs-lookup"><span data-stu-id="030f6-493">Seek to simplify the app's routing scheme.</span></span> <span data-ttu-id="030f6-494">Ukázková aplikace vyžaduje explicitní pořadí zpracování tras, které předvádí několik scénářů směrování pomocí jedné aplikace.</span><span class="sxs-lookup"><span data-stu-id="030f6-494">The sample app requires an explicit route processing order to demonstrate several routing scenarios using a single app.</span></span> <span data-ttu-id="030f6-495">Měli byste se však pokusit vyhnout se postupu nastavení `Order` trasy v produkčních aplikacích.</span><span class="sxs-lookup"><span data-stu-id="030f6-495">However, you should attempt to avoid the practice of setting route `Order` in production apps.</span></span>

<span data-ttu-id="030f6-496">Směrování Razor Pages směrování a řadiče MVC sdílí implementaci.</span><span class="sxs-lookup"><span data-stu-id="030f6-496">Razor Pages routing and MVC controller routing share an implementation.</span></span> <span data-ttu-id="030f6-497">Informace o pořadí směrování v tématech MVC jsou k dispozici při [směrování na akce kontroleru: trasy atributů řazení](xref:mvc/controllers/routing#ordering-attribute-routes).</span><span class="sxs-lookup"><span data-stu-id="030f6-497">Information on route order in the MVC topics is available at [Routing to controller actions: Ordering attribute routes](xref:mvc/controllers/routing#ordering-attribute-routes).</span></span>

## <a name="model-conventions"></a><span data-ttu-id="030f6-498">Konvence modelu</span><span class="sxs-lookup"><span data-stu-id="030f6-498">Model conventions</span></span>

<span data-ttu-id="030f6-499">Přidejte delegáta pro <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> pro přidání [konvencí modelu](xref:mvc/controllers/application-model#conventions) , které se vztahují na Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="030f6-499">Add a delegate for <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> to add [model conventions](xref:mvc/controllers/application-model#conventions) that apply to Razor Pages.</span></span>

### <a name="add-a-route-model-convention-to-all-pages"></a><span data-ttu-id="030f6-500">Přidat konvenci modelu směrování na všechny stránky</span><span class="sxs-lookup"><span data-stu-id="030f6-500">Add a route model convention to all pages</span></span>

<span data-ttu-id="030f6-501">Pomocí <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> můžete vytvořit a přidat <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> do kolekce instancí <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention>, které se použijí během vytváření modelu směrování stránky.</span><span class="sxs-lookup"><span data-stu-id="030f6-501">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page route model construction.</span></span>

<span data-ttu-id="030f6-502">Ukázková aplikace přidá šablonu směrování `{globalTemplate?}` na všechny stránky v aplikaci:</span><span class="sxs-lookup"><span data-stu-id="030f6-502">The sample app adds a `{globalTemplate?}` route template to all of the pages in the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

<span data-ttu-id="030f6-503">Vlastnost <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> pro <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> je nastavena na `1`.</span><span class="sxs-lookup"><span data-stu-id="030f6-503">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `1`.</span></span> <span data-ttu-id="030f6-504">Tím se zajistí následující chování při shodě trasy v ukázkové aplikaci:</span><span class="sxs-lookup"><span data-stu-id="030f6-504">This ensures the following route matching behavior in the sample app:</span></span>

* <span data-ttu-id="030f6-505">Šablona směrování pro `TheContactPage/{text?}` se přidá později v tématu.</span><span class="sxs-lookup"><span data-stu-id="030f6-505">A route template for `TheContactPage/{text?}` is added later in the topic.</span></span> <span data-ttu-id="030f6-506">Trasa stránky kontaktu má výchozí pořadí `null` (`Order = 0`), takže odpovídá před šablonou směrování `{globalTemplate?}`.</span><span class="sxs-lookup"><span data-stu-id="030f6-506">The Contact Page route has a default order of `null` (`Order = 0`), so it matches before the `{globalTemplate?}` route template.</span></span>
* <span data-ttu-id="030f6-507">Šablona směrování `{aboutTemplate?}` je přidána později v tématu.</span><span class="sxs-lookup"><span data-stu-id="030f6-507">An `{aboutTemplate?}` route template is added later in the topic.</span></span> <span data-ttu-id="030f6-508">Šabloně `{aboutTemplate?}` je předána `Order` `2`.</span><span class="sxs-lookup"><span data-stu-id="030f6-508">The `{aboutTemplate?}` template is given an `Order` of `2`.</span></span> <span data-ttu-id="030f6-509">Když se na stránce o aplikaci žádá `/About/RouteDataValue`, načtou se "RouteDataValue" do `RouteData.Values["globalTemplate"]` (`Order = 1`) a není `RouteData.Values["aboutTemplate"]` (`Order = 2`) z důvodu nastavení vlastnosti `Order`.</span><span class="sxs-lookup"><span data-stu-id="030f6-509">When the About page is requested at `/About/RouteDataValue`, "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["aboutTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>
* <span data-ttu-id="030f6-510">Šablona směrování `{otherPagesTemplate?}` je přidána později v tématu.</span><span class="sxs-lookup"><span data-stu-id="030f6-510">An `{otherPagesTemplate?}` route template is added later in the topic.</span></span> <span data-ttu-id="030f6-511">Šabloně `{otherPagesTemplate?}` je předána `Order` `2`.</span><span class="sxs-lookup"><span data-stu-id="030f6-511">The `{otherPagesTemplate?}` template is given an `Order` of `2`.</span></span> <span data-ttu-id="030f6-512">Pokud se u libovolné stránky ve složce *Pages/OtherPages* požaduje parametr trasy (například `/OtherPages/Page1/RouteDataValue`), RouteDataValue se načte do `RouteData.Values["globalTemplate"]` (`Order = 1`) a ne`RouteData.Values["otherPagesTemplate"]` (`Order = 2`) z důvodu nastavení vlastnosti `Order`.</span><span class="sxs-lookup"><span data-stu-id="030f6-512">When any page in the *Pages/OtherPages* folder is requested with a route parameter (for example, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="030f6-513">Pokud je to možné, nenastavte `Order`, což má za následek `Order = 0`.</span><span class="sxs-lookup"><span data-stu-id="030f6-513">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="030f6-514">Pro výběr správné trasy se spoléhá na směrování.</span><span class="sxs-lookup"><span data-stu-id="030f6-514">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="030f6-515">Razor Pages možnosti, jako je přidání <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>, jsou přidány při přidání MVC do kolekce služby v `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="030f6-515">Razor Pages options, such as adding <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>, are added when MVC is added to the service collection in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="030f6-516">Příklad najdete v [ukázkové aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/).</span><span class="sxs-lookup"><span data-stu-id="030f6-516">For an example, see the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/).</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="030f6-517">Požádejte o stránku ukázky o `localhost:5000/About/GlobalRouteValue` a Prohlédněte si výsledek:</span><span class="sxs-lookup"><span data-stu-id="030f6-517">Request the sample's About page at `localhost:5000/About/GlobalRouteValue` and inspect the result:</span></span>

![Stránka o aplikaci je požadována s segmentem směrování GlobalRouteValue.](razor-pages-conventions/_static/about-page-global-template.png)

### <a name="add-an-app-model-convention-to-all-pages"></a><span data-ttu-id="030f6-520">Přidání konvence modelu aplikace na všechny stránky</span><span class="sxs-lookup"><span data-stu-id="030f6-520">Add an app model convention to all pages</span></span>

<span data-ttu-id="030f6-521">Pomocí <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> můžete vytvořit a přidat <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> do kolekce instancí <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention>, které se použijí během vytváření modelu aplikace stránky.</span><span class="sxs-lookup"><span data-stu-id="030f6-521">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page app model construction.</span></span>

<span data-ttu-id="030f6-522">Chcete-li předvést tuto a další konvenci dále v tématu, ukázková aplikace obsahuje třídu `AddHeaderAttribute`.</span><span class="sxs-lookup"><span data-stu-id="030f6-522">To demonstrate this and other conventions later in the topic, the sample app includes an `AddHeaderAttribute` class.</span></span> <span data-ttu-id="030f6-523">Konstruktor třídy přijímá `name` řetězec a pole `values` řetězců.</span><span class="sxs-lookup"><span data-stu-id="030f6-523">The class constructor accepts a `name` string and a `values` string array.</span></span> <span data-ttu-id="030f6-524">Tyto hodnoty se používají ve své `OnResultExecuting` metodě pro nastavení hlavičky odpovědi.</span><span class="sxs-lookup"><span data-stu-id="030f6-524">These values are used in its `OnResultExecuting` method to set a response header.</span></span> <span data-ttu-id="030f6-525">Úplná třída je uvedena v části [konvence akcí modelu stránky](#page-model-action-conventions) dále v tématu.</span><span class="sxs-lookup"><span data-stu-id="030f6-525">The full class is shown in the [Page model action conventions](#page-model-action-conventions) section later in the topic.</span></span>

<span data-ttu-id="030f6-526">Ukázková aplikace používá třídu `AddHeaderAttribute` k přidání záhlaví, `GlobalHeader`, na všechny stránky v aplikaci:</span><span class="sxs-lookup"><span data-stu-id="030f6-526">The sample app uses the `AddHeaderAttribute` class to add a header, `GlobalHeader`, to all of the pages in the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

<span data-ttu-id="030f6-527">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="030f6-527">*Startup.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

<span data-ttu-id="030f6-528">Požádejte o stránku ukázek o `localhost:5000/About` a Prohlédněte si záhlaví a podívejte se na výsledek:</span><span class="sxs-lookup"><span data-stu-id="030f6-528">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![Hlavičky odpovědi stránky o produktu ukazují, že byl přidán GlobalHeader.](razor-pages-conventions/_static/about-page-global-header.png)

### <a name="add-a-handler-model-convention-to-all-pages"></a><span data-ttu-id="030f6-530">Přidat konvenci modelu obslužné rutiny na všechny stránky</span><span class="sxs-lookup"><span data-stu-id="030f6-530">Add a handler model convention to all pages</span></span>

<span data-ttu-id="030f6-531">Pomocí <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> můžete vytvořit a přidat <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> do kolekce instancí <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention>, které se použijí během vytváření modelu obslužné rutiny stránky.</span><span class="sxs-lookup"><span data-stu-id="030f6-531">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page handler model construction.</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalPageHandlerModelConvention.cs?name=snippet1)]

<span data-ttu-id="030f6-532">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="030f6-532">*Startup.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet10)]

## <a name="page-route-action-conventions"></a><span data-ttu-id="030f6-533">Konvence akcí při směrování stránky</span><span class="sxs-lookup"><span data-stu-id="030f6-533">Page route action conventions</span></span>

<span data-ttu-id="030f6-534">Výchozí zprostředkovatel modelu směrování odvozený od <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> vyvolá konvence, které jsou navržené tak, aby poskytovaly body rozšiřitelnosti pro konfiguraci tras stránky.</span><span class="sxs-lookup"><span data-stu-id="030f6-534">The default route model provider that derives from <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> invokes conventions which are designed to provide extensibility points for configuring page routes.</span></span>

### <a name="folder-route-model-convention"></a><span data-ttu-id="030f6-535">Konvence modelu směrování složky</span><span class="sxs-lookup"><span data-stu-id="030f6-535">Folder route model convention</span></span>

<span data-ttu-id="030f6-536">Pomocí <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> můžete vytvořit a přidat <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention>, který vyvolá akci na <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> pro všechny stránky v zadané složce.</span><span class="sxs-lookup"><span data-stu-id="030f6-536">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> for all of the pages under the specified folder.</span></span>

<span data-ttu-id="030f6-537">Ukázková aplikace používá <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> k přidání šablony trasy `{otherPagesTemplate?}` na stránky ve složce *OtherPages* :</span><span class="sxs-lookup"><span data-stu-id="030f6-537">The sample app uses <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> to add an `{otherPagesTemplate?}` route template to the pages in the *OtherPages* folder:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

<span data-ttu-id="030f6-538">Vlastnost <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> pro <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> je nastavena na `2`.</span><span class="sxs-lookup"><span data-stu-id="030f6-538">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `2`.</span></span> <span data-ttu-id="030f6-539">Tím je zajištěno, že šablona pro `{globalTemplate?}` (nastavená dříve v tématu `1`) má prioritu první pozice hodnoty dat trasy, když je zadána hodnota jedné trasy.</span><span class="sxs-lookup"><span data-stu-id="030f6-539">This ensures that the template for `{globalTemplate?}` (set earlier in the topic to `1`) is given priority for the first route data value position when a single route value is provided.</span></span> <span data-ttu-id="030f6-540">Pokud je stránka ve složce *Pages/OtherPages* požadována s hodnotou parametru trasy (například `/OtherPages/Page1/RouteDataValue`), je "RouteDataValue" načteno do `RouteData.Values["globalTemplate"]` (`Order = 1`) a není `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) z důvodu nastavení vlastnosti `Order`.</span><span class="sxs-lookup"><span data-stu-id="030f6-540">If a page in the *Pages/OtherPages* folder is requested with a route parameter value (for example, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="030f6-541">Pokud je to možné, nenastavte `Order`, což má za následek `Order = 0`.</span><span class="sxs-lookup"><span data-stu-id="030f6-541">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="030f6-542">Pro výběr správné trasy se spoléhá na směrování.</span><span class="sxs-lookup"><span data-stu-id="030f6-542">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="030f6-543">Vyžádejte si Page1 stránku ukázky na `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` a Prohlédněte si výsledek:</span><span class="sxs-lookup"><span data-stu-id="030f6-543">Request the sample's Page1 page at `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` and inspect the result:</span></span>

![Page1 ve složce OtherPages se vyžádá s segmentem směrování GlobalRouteValue a OtherPagesRouteValue.](razor-pages-conventions/_static/otherpages-page1-global-and-otherpages-templates.png)

### <a name="page-route-model-convention"></a><span data-ttu-id="030f6-546">Konvence modelu směrování stránky</span><span class="sxs-lookup"><span data-stu-id="030f6-546">Page route model convention</span></span>

<span data-ttu-id="030f6-547">Pomocí <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> vytvořit a přidat <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention>, který vyvolá akci na <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> pro stránku se zadaným názvem.</span><span class="sxs-lookup"><span data-stu-id="030f6-547">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> for the page with the specified name.</span></span>

<span data-ttu-id="030f6-548">Ukázková aplikace používá `AddPageRouteModelConvention` k přidání šablony trasy `{aboutTemplate?}` na stránku o aplikaci:</span><span class="sxs-lookup"><span data-stu-id="030f6-548">The sample app uses `AddPageRouteModelConvention` to add an `{aboutTemplate?}` route template to the About page:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

<span data-ttu-id="030f6-549">Vlastnost <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> pro <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> je nastavena na `2`.</span><span class="sxs-lookup"><span data-stu-id="030f6-549">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `2`.</span></span> <span data-ttu-id="030f6-550">Tím je zajištěno, že šablona pro `{globalTemplate?}` (nastavená dříve v tématu `1`) má prioritu první pozice hodnoty dat trasy, když je zadána hodnota jedné trasy.</span><span class="sxs-lookup"><span data-stu-id="030f6-550">This ensures that the template for `{globalTemplate?}` (set earlier in the topic to `1`) is given priority for the first route data value position when a single route value is provided.</span></span> <span data-ttu-id="030f6-551">Pokud se stránka o žádosti vyžaduje s hodnotou parametru Route na `/About/RouteDataValue`, načte se RouteDataValue do `RouteData.Values["globalTemplate"]` (`Order = 1`) a ne`RouteData.Values["aboutTemplate"]` (`Order = 2`) z důvodu nastavení vlastnosti `Order`.</span><span class="sxs-lookup"><span data-stu-id="030f6-551">If the About page is requested with a route parameter value at `/About/RouteDataValue`, "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["aboutTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="030f6-552">Pokud je to možné, nenastavte `Order`, což má za následek `Order = 0`.</span><span class="sxs-lookup"><span data-stu-id="030f6-552">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="030f6-553">Pro výběr správné trasy se spoléhá na směrování.</span><span class="sxs-lookup"><span data-stu-id="030f6-553">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="030f6-554">Požádejte o stránku ukázky o `localhost:5000/About/GlobalRouteValue/AboutRouteValue` a Prohlédněte si výsledek:</span><span class="sxs-lookup"><span data-stu-id="030f6-554">Request the sample's About page at `localhost:5000/About/GlobalRouteValue/AboutRouteValue` and inspect the result:</span></span>

![U segmentů směrování pro GlobalRouteValue a AboutRouteValue se žádá o stránku.](razor-pages-conventions/_static/about-page-global-and-about-templates.png)

## <a name="configure-a-page-route"></a><span data-ttu-id="030f6-557">Konfigurace trasy stránky</span><span class="sxs-lookup"><span data-stu-id="030f6-557">Configure a page route</span></span>

<span data-ttu-id="030f6-558">Pomocí <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> můžete nakonfigurovat směrování na stránku na zadané cestě stránky.</span><span class="sxs-lookup"><span data-stu-id="030f6-558">Use <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> to configure a route to a page at the specified page path.</span></span> <span data-ttu-id="030f6-559">Vygenerované odkazy na stránku používají zadanou trasu.</span><span class="sxs-lookup"><span data-stu-id="030f6-559">Generated links to the page use your specified route.</span></span> <span data-ttu-id="030f6-560">`AddPageRoute` používá k vytvoření trasy `AddPageRouteModelConvention`.</span><span class="sxs-lookup"><span data-stu-id="030f6-560">`AddPageRoute` uses `AddPageRouteModelConvention` to establish the route.</span></span>

<span data-ttu-id="030f6-561">Ukázková aplikace vytvoří trasu, která `/TheContactPage` pro *kontakt. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="030f6-561">The sample app creates a route to `/TheContactPage` for *Contact.cshtml*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet5)]

<span data-ttu-id="030f6-562">Stránku kontaktů lze také získat na `/Contact` prostřednictvím výchozí trasy.</span><span class="sxs-lookup"><span data-stu-id="030f6-562">The Contact page can also be reached at `/Contact` via its default route.</span></span>

<span data-ttu-id="030f6-563">Vlastní trasa ukázkové aplikace na stránce kontaktů umožňuje, aby byl `text` segment trasy (`{text?}`).</span><span class="sxs-lookup"><span data-stu-id="030f6-563">The sample app's custom route to the Contact page allows for an optional `text` route segment (`{text?}`).</span></span> <span data-ttu-id="030f6-564">Tato stránka zahrnuje také tento volitelný segment v direktivě `@page` pro případ, že návštěvník přistupuje k této stránce `/Contact` trase:</span><span class="sxs-lookup"><span data-stu-id="030f6-564">The page also includes this optional segment in its `@page` directive in case the visitor accesses the page at its `/Contact` route:</span></span>

[!code-cshtml[](razor-pages-conventions/samples/2.x/SampleApp/Pages/Contact.cshtml?highlight=1)]

<span data-ttu-id="030f6-565">Všimněte si, že adresa URL vygenerovaná pro odkaz **kontaktu** na vykreslené stránce odráží aktualizovanou trasu:</span><span class="sxs-lookup"><span data-stu-id="030f6-565">Note that the URL generated for the **Contact** link in the rendered page reflects the updated route:</span></span>

![Odkaz na ukázkový kontakt aplikace na navigačním panelu](razor-pages-conventions/_static/contact-link.png)

![Kontrola odkazu na kontakt ve vykresleném kódu HTML znamená, že odkaz href je nastaven na hodnotu "/TheContactPage".](razor-pages-conventions/_static/contact-link-source.png)

<span data-ttu-id="030f6-568">Navštivte stránku kontaktů buď na své běžné trase, `/Contact`, nebo na vlastní trasu `/TheContactPage`.</span><span class="sxs-lookup"><span data-stu-id="030f6-568">Visit the Contact page at either its ordinary route, `/Contact`, or the custom route, `/TheContactPage`.</span></span> <span data-ttu-id="030f6-569">Pokud zadáte další segment `text` trasy, stránka zobrazuje segment zakódovaný ve formátu HTML, který zadáte:</span><span class="sxs-lookup"><span data-stu-id="030f6-569">If you supply an additional `text` route segment, the page shows the HTML-encoded segment that you provide:</span></span>

![Prohlížeč Edge – příklad poskytnutí volitelného segmentu "text" trasy "TextValue" v adrese URL](razor-pages-conventions/_static/route-segment-with-custom-route.png)

## <a name="page-model-action-conventions"></a><span data-ttu-id="030f6-572">Konvence akcí modelu stránky</span><span class="sxs-lookup"><span data-stu-id="030f6-572">Page model action conventions</span></span>

<span data-ttu-id="030f6-573">Výchozí poskytovatel modelu stránky, který implementuje <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider>, vyvolá konvence, které jsou navrženy tak, aby poskytovaly body rozšiřitelnosti pro konfiguraci modelů stránek.</span><span class="sxs-lookup"><span data-stu-id="030f6-573">The default page model provider that implements <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> invokes conventions which are designed to provide extensibility points for configuring page models.</span></span> <span data-ttu-id="030f6-574">Tyto konvence jsou užitečné při sestavování a úpravách scénářů zjišťování a zpracování stránky.</span><span class="sxs-lookup"><span data-stu-id="030f6-574">These conventions are useful when building and modifying page discovery and processing scenarios.</span></span>

<span data-ttu-id="030f6-575">V příkladech v této části používá ukázková aplikace třídu `AddHeaderAttribute`, což je <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>, která používá hlavičku odpovědi:</span><span class="sxs-lookup"><span data-stu-id="030f6-575">For the examples in this section, the sample app uses an `AddHeaderAttribute` class, which is a <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>, that applies a response header:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

<span data-ttu-id="030f6-576">Pomocí konvence Ukázka ukazuje, jak použít atribut na všechny stránky ve složce a na jednu stránku.</span><span class="sxs-lookup"><span data-stu-id="030f6-576">Using conventions, the sample demonstrates how to apply the attribute to all of the pages in a folder and to a single page.</span></span>

<span data-ttu-id="030f6-577">**Konvence modelu aplikace složky**</span><span class="sxs-lookup"><span data-stu-id="030f6-577">**Folder app model convention**</span></span>

<span data-ttu-id="030f6-578">Pomocí <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> můžete vytvořit a přidat <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention>, který vyvolá akci na instancích <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> pro všechny stránky v zadané složce.</span><span class="sxs-lookup"><span data-stu-id="030f6-578">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> that invokes an action on <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> instances for all pages under the specified folder.</span></span>

<span data-ttu-id="030f6-579">Ukázka demonstruje použití `AddFolderApplicationModelConvention` přidáním hlavičky, `OtherPagesHeader`na stránky ve složce *OtherPages* v aplikaci:</span><span class="sxs-lookup"><span data-stu-id="030f6-579">The sample demonstrates the use of `AddFolderApplicationModelConvention` by adding a header, `OtherPagesHeader`, to the pages inside the *OtherPages* folder of the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet6)]

<span data-ttu-id="030f6-580">Vyžádejte si Page1 stránku ukázky na `localhost:5000/OtherPages/Page1` a Prohlédněte si hlavičky a podívejte se na výsledek:</span><span class="sxs-lookup"><span data-stu-id="030f6-580">Request the sample's Page1 page at `localhost:5000/OtherPages/Page1` and inspect the headers to view the result:</span></span>

![Hlavičky odpovědi stránky OtherPages/Page1 ukazují, že byla přidána OtherPagesHeader.](razor-pages-conventions/_static/page1-otherpages-header.png)

<span data-ttu-id="030f6-582">**Konvence modelu aplikace stránky**</span><span class="sxs-lookup"><span data-stu-id="030f6-582">**Page app model convention**</span></span>

<span data-ttu-id="030f6-583">Pomocí <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> vytvořit a přidat <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention>, který vyvolá akci na <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> pro stránku se zadaným názvem.</span><span class="sxs-lookup"><span data-stu-id="030f6-583">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> for the page with the specified name.</span></span>

<span data-ttu-id="030f6-584">Ukázka demonstruje použití `AddPageApplicationModelConvention` přidáním hlavičky, `AboutHeader`na stránku o aplikaci:</span><span class="sxs-lookup"><span data-stu-id="030f6-584">The sample demonstrates the use of `AddPageApplicationModelConvention` by adding a header, `AboutHeader`, to the About page:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet7)]

<span data-ttu-id="030f6-585">Požádejte o stránku ukázek o `localhost:5000/About` a Prohlédněte si záhlaví a podívejte se na výsledek:</span><span class="sxs-lookup"><span data-stu-id="030f6-585">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![Hlavičky odpovědi stránky o produktu ukazují, že byl přidán AboutHeader.](razor-pages-conventions/_static/about-page-about-header.png)

<span data-ttu-id="030f6-587">**Konfigurace filtru**</span><span class="sxs-lookup"><span data-stu-id="030f6-587">**Configure a filter**</span></span>

<span data-ttu-id="030f6-588"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> nakonfiguruje zadaný filtr, který se má použít.</span><span class="sxs-lookup"><span data-stu-id="030f6-588"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configures the specified filter to apply.</span></span> <span data-ttu-id="030f6-589">Můžete implementovat třídu filtru, ale ukázková aplikace ukazuje, jak implementovat filtr ve výrazu lambda, který je implementován na pozadí jako objekt pro vytváření, který vrací filtr:</span><span class="sxs-lookup"><span data-stu-id="030f6-589">You can implement a filter class, but the sample app shows how to implement a filter in a lambda expression, which is implemented behind-the-scenes as a factory that returns a filter:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet8)]

<span data-ttu-id="030f6-590">Model webové aplikace se používá ke kontrole relativní cesty pro segmenty, které vedou ke stránce Page2 ve složce *OtherPages* .</span><span class="sxs-lookup"><span data-stu-id="030f6-590">The page app model is used to check the relative path for segments that lead to the Page2 page in the *OtherPages* folder.</span></span> <span data-ttu-id="030f6-591">Pokud podmínka projde, přidá se hlavička.</span><span class="sxs-lookup"><span data-stu-id="030f6-591">If the condition passes, a header is added.</span></span> <span data-ttu-id="030f6-592">V takovém případě je `EmptyFilter` použito.</span><span class="sxs-lookup"><span data-stu-id="030f6-592">If not, the `EmptyFilter` is applied.</span></span>

<span data-ttu-id="030f6-593">`EmptyFilter` je [Filtr akcí](xref:mvc/controllers/filters#action-filters).</span><span class="sxs-lookup"><span data-stu-id="030f6-593">`EmptyFilter` is an [Action filter](xref:mvc/controllers/filters#action-filters).</span></span> <span data-ttu-id="030f6-594">Vzhledem k tomu, že jsou filtry akcí ignorovány Razor Pages, `EmptyFilter` nemá žádný vliv, pokud cesta neobsahuje `OtherPages/Page2`.</span><span class="sxs-lookup"><span data-stu-id="030f6-594">Since Action filters are ignored by Razor Pages, the `EmptyFilter` has no effect as intended if the path doesn't contain `OtherPages/Page2`.</span></span>

<span data-ttu-id="030f6-595">Vyžádejte si Page2 stránku ukázky na `localhost:5000/OtherPages/Page2` a Prohlédněte si hlavičky a podívejte se na výsledek:</span><span class="sxs-lookup"><span data-stu-id="030f6-595">Request the sample's Page2 page at `localhost:5000/OtherPages/Page2` and inspect the headers to view the result:</span></span>

![OtherPagesPage2Header se přidá do odpovědi pro Page2.](razor-pages-conventions/_static/page2-filter-header.png)

<span data-ttu-id="030f6-597">**Konfigurace objektu pro vytváření filtrů**</span><span class="sxs-lookup"><span data-stu-id="030f6-597">**Configure a filter factory**</span></span>

<span data-ttu-id="030f6-598"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> nakonfiguruje určený objekt pro vytváření a použije [filtry](xref:mvc/controllers/filters) na všechny Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="030f6-598"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configures the specified factory to apply [filters](xref:mvc/controllers/filters) to all Razor Pages.</span></span>

<span data-ttu-id="030f6-599">Ukázková aplikace poskytuje příklad použití [objektu pro vytváření filtru](xref:mvc/controllers/filters#ifilterfactory) přidáním hlavičky, `FilterFactoryHeader`se dvěma hodnotami na stránky aplikace:</span><span class="sxs-lookup"><span data-stu-id="030f6-599">The sample app provides an example of using a [filter factory](xref:mvc/controllers/filters#ifilterfactory) by adding a header, `FilterFactoryHeader`, with two values to the app's pages:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet9)]

<span data-ttu-id="030f6-600">*AddHeaderWithFactory.cs*:</span><span class="sxs-lookup"><span data-stu-id="030f6-600">*AddHeaderWithFactory.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Factories/AddHeaderWithFactory.cs?name=snippet1)]

<span data-ttu-id="030f6-601">Požádejte o stránku ukázek o `localhost:5000/About` a Prohlédněte si záhlaví a podívejte se na výsledek:</span><span class="sxs-lookup"><span data-stu-id="030f6-601">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![Hlavičky odpovědi stránky o stránku zobrazují, že byly přidány dvě hlavičky FilterFactoryHeader.](razor-pages-conventions/_static/about-page-filter-factory-header.png)

## <a name="mvc-filters-and-the-page-filter-ipagefilter"></a><span data-ttu-id="030f6-603">Filtry MVC a filtr stránky (IPageFilter)</span><span class="sxs-lookup"><span data-stu-id="030f6-603">MVC Filters and the Page filter (IPageFilter)</span></span>

<span data-ttu-id="030f6-604">[Filtry akcí](xref:mvc/controllers/filters#action-filters) MVC se Razor Pages ignorují, protože Razor Pages použít obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="030f6-604">MVC [Action filters](xref:mvc/controllers/filters#action-filters) are ignored by Razor Pages, since Razor Pages use handler methods.</span></span> <span data-ttu-id="030f6-605">Další typy filtrů MVC jsou k dispozici pro použití: [autorizace](xref:mvc/controllers/filters#authorization-filters), [výjimka](xref:mvc/controllers/filters#exception-filters), [prostředek](xref:mvc/controllers/filters#resource-filters)a [výsledek](xref:mvc/controllers/filters#result-filters).</span><span class="sxs-lookup"><span data-stu-id="030f6-605">Other types of MVC filters are available for you to use: [Authorization](xref:mvc/controllers/filters#authorization-filters), [Exception](xref:mvc/controllers/filters#exception-filters), [Resource](xref:mvc/controllers/filters#resource-filters), and [Result](xref:mvc/controllers/filters#result-filters).</span></span> <span data-ttu-id="030f6-606">Další informace najdete v tématu [filtry](xref:mvc/controllers/filters) .</span><span class="sxs-lookup"><span data-stu-id="030f6-606">For more information, see the [Filters](xref:mvc/controllers/filters) topic.</span></span>

<span data-ttu-id="030f6-607">Filtr stránky (<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>) je filtr, který se vztahuje na Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="030f6-607">The Page filter (<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>) is a filter that applies to Razor Pages.</span></span> <span data-ttu-id="030f6-608">Další informace naleznete v tématu [metody Filter pro Razor Pages](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="030f6-608">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="030f6-609">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="030f6-609">Additional resources</span></span>

* <xref:security/authorization/razor-pages-authorization>
* <xref:mvc/controllers/areas#areas-with-razor-pages>

::: moniker-end
