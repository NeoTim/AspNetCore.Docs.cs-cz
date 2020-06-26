---
title: RazorSměrování stránek a konvence aplikací v ASP.NET Core
author: rick-anderson
description: Seznamte se s tím, jak konvence poskytovatelů modelů směrování a aplikací umožňují řídit směrování, zjišťování a zpracování stránky.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: razor-pages/razor-pages-conventions
ms.openlocfilehash: 308ca4401289a55e5dba8d61de50644cb2a53433
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2020
ms.locfileid: "85405247"
---
# <a name="razor-pages-route-and-app-conventions-in-aspnet-core"></a>Razor<span data-ttu-id="3bd7e-103">Směrování stránek a konvence aplikací v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3bd7e-103"> Pages route and app conventions in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="3bd7e-104">Naučte se používat [konvence směrování stránky a poskytovatele modelů aplikací](xref:mvc/controllers/application-model#conventions) k řízení směrování, zjišťování a zpracování stránky v Razor aplikacích stránky.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-104">Learn how to use page [route and app model provider conventions](xref:mvc/controllers/application-model#conventions) to control page routing, discovery, and processing in Razor Pages apps.</span></span>

<span data-ttu-id="3bd7e-105">Pokud potřebujete nakonfigurovat vlastní trasy stránky pro jednotlivé stránky, nakonfigurujte směrování na stránky pomocí [AddPageRoute konvence](#configure-a-page-route) popsané dále v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-105">When you need to configure custom page routes for individual pages, configure routing to pages with the [AddPageRoute convention](#configure-a-page-route) described later in this topic.</span></span>

<span data-ttu-id="3bd7e-106">Chcete-li určit trasu stránky, přidat segmenty směrování nebo přidat parametry do trasy, použijte `@page` direktivu stránky.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-106">To specify a page route, add route segments, or add parameters to a route, use the page's `@page` directive.</span></span> <span data-ttu-id="3bd7e-107">Další informace najdete v tématu [vlastní trasy](xref:razor-pages/index#custom-routes).</span><span class="sxs-lookup"><span data-stu-id="3bd7e-107">For more information, see [Custom routes](xref:razor-pages/index#custom-routes).</span></span>

<span data-ttu-id="3bd7e-108">Existují vyhrazená slova, která nelze použít jako segmenty směrování nebo názvy parametrů.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-108">There are reserved words that can't be used as route segments or parameter names.</span></span> <span data-ttu-id="3bd7e-109">Další informace najdete v tématu [směrování: názvy rezervovaných směrování](xref:mvc/controllers/routing#reserved-routing-names).</span><span class="sxs-lookup"><span data-stu-id="3bd7e-109">For more information, see [Routing: Reserved routing names](xref:mvc/controllers/routing#reserved-routing-names).</span></span>

<span data-ttu-id="3bd7e-110">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="3bd7e-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

| <span data-ttu-id="3bd7e-111">Scénář</span><span class="sxs-lookup"><span data-stu-id="3bd7e-111">Scenario</span></span> | <span data-ttu-id="3bd7e-112">Ukázka znázorňuje...</span><span class="sxs-lookup"><span data-stu-id="3bd7e-112">The sample demonstrates ...</span></span> |
| -------- | --------------------------- |
| [<span data-ttu-id="3bd7e-113">Modelové konvence</span><span class="sxs-lookup"><span data-stu-id="3bd7e-113">Model conventions</span></span>](#model-conventions)<br><br><span data-ttu-id="3bd7e-114">Konvence. Add</span><span class="sxs-lookup"><span data-stu-id="3bd7e-114">Conventions.Add</span></span><ul><li><span data-ttu-id="3bd7e-115">IPageRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="3bd7e-115">IPageRouteModelConvention</span></span></li><li><span data-ttu-id="3bd7e-116">IPageApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="3bd7e-116">IPageApplicationModelConvention</span></span></li><li><span data-ttu-id="3bd7e-117">IPageHandlerModelConvention</span><span class="sxs-lookup"><span data-stu-id="3bd7e-117">IPageHandlerModelConvention</span></span></li></ul> | <span data-ttu-id="3bd7e-118">Přidejte šablonu a hlavičku směrování na stránky aplikace.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-118">Add a route template and header to an app's pages.</span></span> |
| [<span data-ttu-id="3bd7e-119">Konvence akcí při směrování stránky</span><span class="sxs-lookup"><span data-stu-id="3bd7e-119">Page route action conventions</span></span>](#page-route-action-conventions)<ul><li><span data-ttu-id="3bd7e-120">AddFolderRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="3bd7e-120">AddFolderRouteModelConvention</span></span></li><li><span data-ttu-id="3bd7e-121">AddPageRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="3bd7e-121">AddPageRouteModelConvention</span></span></li><li><span data-ttu-id="3bd7e-122">AddPageRoute</span><span class="sxs-lookup"><span data-stu-id="3bd7e-122">AddPageRoute</span></span></li></ul> | <span data-ttu-id="3bd7e-123">Přidejte šablonu směrování do stránek ve složce a na jednu stránku.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-123">Add a route template to pages in a folder and to a single page.</span></span> |
| [<span data-ttu-id="3bd7e-124">Konvence akcí modelu stránky</span><span class="sxs-lookup"><span data-stu-id="3bd7e-124">Page model action conventions</span></span>](#page-model-action-conventions)<ul><li><span data-ttu-id="3bd7e-125">AddFolderApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="3bd7e-125">AddFolderApplicationModelConvention</span></span></li><li><span data-ttu-id="3bd7e-126">AddPageApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="3bd7e-126">AddPageApplicationModelConvention</span></span></li><li><span data-ttu-id="3bd7e-127">ConfigureFilter (filtrovat třídu, lambda výraz nebo objekt pro vytváření filtru)</span><span class="sxs-lookup"><span data-stu-id="3bd7e-127">ConfigureFilter (filter class, lambda expression, or filter factory)</span></span></li></ul> | <span data-ttu-id="3bd7e-128">Umožňuje přidat záhlaví na stránky ve složce, přidat záhlaví na jednu stránku a nakonfigurovat [objekt pro vytváření filtru](xref:mvc/controllers/filters#ifilterfactory) tak, aby přidal hlavičku na stránky aplikace.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-128">Add a header to pages in a folder, add a header to a single page, and configure a [filter factory](xref:mvc/controllers/filters#ifilterfactory) to add a header to an app's pages.</span></span> |

Razor<span data-ttu-id="3bd7e-129">Konvence stránek jsou přidány a nakonfigurovány pomocí <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> metody rozšíření pro <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> kolekci služeb ve `Startup` třídě.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-129"> Pages conventions are added and configured using the <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> extension method to <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> on the service collection in the `Startup` class.</span></span> <span data-ttu-id="3bd7e-130">Následující příklady konvence jsou vysvětleny dále v tomto tématu:</span><span class="sxs-lookup"><span data-stu-id="3bd7e-130">The following convention examples are explained later in this topic:</span></span>

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

## <a name="route-order"></a><span data-ttu-id="3bd7e-131">Pořadí směrování</span><span class="sxs-lookup"><span data-stu-id="3bd7e-131">Route order</span></span>

<span data-ttu-id="3bd7e-132">Trasy určují <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> pro zpracování (odpovídání tras).</span><span class="sxs-lookup"><span data-stu-id="3bd7e-132">Routes specify an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> for processing (route matching).</span></span>

| <span data-ttu-id="3bd7e-133">Objednání</span><span class="sxs-lookup"><span data-stu-id="3bd7e-133">Order</span></span>            | <span data-ttu-id="3bd7e-134">Chování</span><span class="sxs-lookup"><span data-stu-id="3bd7e-134">Behavior</span></span> |
| :--------------: | -------- |
| <span data-ttu-id="3bd7e-135">-1</span><span class="sxs-lookup"><span data-stu-id="3bd7e-135">-1</span></span>               | <span data-ttu-id="3bd7e-136">Trasa je zpracována před zpracováním jiných tras.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-136">The route is processed before other routes are processed.</span></span> |
| <span data-ttu-id="3bd7e-137">0</span><span class="sxs-lookup"><span data-stu-id="3bd7e-137">0</span></span>                | <span data-ttu-id="3bd7e-138">Pořadí není zadáno (výchozí hodnota).</span><span class="sxs-lookup"><span data-stu-id="3bd7e-138">Order isn't specified (default value).</span></span> <span data-ttu-id="3bd7e-139">Nepřiřazuje se `Order` ( `Order = null` ) výchozí hodnota trasy `Order` na 0 (nula) pro zpracování.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-139">Not assigning `Order` (`Order = null`) defaults the route `Order` to 0 (zero) for processing.</span></span> |
| <span data-ttu-id="3bd7e-140">1, 2, &hellip; n</span><span class="sxs-lookup"><span data-stu-id="3bd7e-140">1, 2, &hellip; n</span></span> | <span data-ttu-id="3bd7e-141">Určuje pořadí zpracování směrování.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-141">Specifies the route processing order.</span></span> |

<span data-ttu-id="3bd7e-142">Zpracování směrování je zřízené podle konvence:</span><span class="sxs-lookup"><span data-stu-id="3bd7e-142">Route processing is established by convention:</span></span>

* <span data-ttu-id="3bd7e-143">Trasy jsou zpracovávány v sekvenčním pořadí (-1, 0, 1, 2, &hellip; n).</span><span class="sxs-lookup"><span data-stu-id="3bd7e-143">Routes are processed in sequential order (-1, 0, 1, 2, &hellip; n).</span></span>
* <span data-ttu-id="3bd7e-144">V případě, že trasy mají stejný směr `Order` , je nejdříve porovnána konkrétní trasa, za kterou následuje méně specifických tras.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-144">When routes have the same `Order`, the most specific route is matched first followed by less specific routes.</span></span>
* <span data-ttu-id="3bd7e-145">Když se trasy se stejným `Order` počtem parametrů shodují s adresou URL požadavku, trasy se zpracovávají v pořadí, v jakém jsou přidány do <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection> .</span><span class="sxs-lookup"><span data-stu-id="3bd7e-145">When routes with the same `Order` and the same number of parameters match a request URL, routes are processed in the order that they're added to the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>.</span></span>

<span data-ttu-id="3bd7e-146">Pokud je to možné, vyhněte se v závislosti na zavedeném pořadí zpracování směrování.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-146">If possible, avoid depending on an established route processing order.</span></span> <span data-ttu-id="3bd7e-147">Obecně směrování vybírá správnou trasu s odpovídající adresou URL.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-147">Generally, routing selects the correct route with URL matching.</span></span> <span data-ttu-id="3bd7e-148">Pokud musíte nastavit vlastnosti směrování `Order` na správně směrované požadavky, schéma směrování aplikace je pravděpodobně matoucí pro klienty a křehké na údržbu.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-148">If you must set route `Order` properties to route requests correctly, the app's routing scheme is probably confusing to clients and fragile to maintain.</span></span> <span data-ttu-id="3bd7e-149">Vyhledejte zjednodušené schéma směrování aplikace.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-149">Seek to simplify the app's routing scheme.</span></span> <span data-ttu-id="3bd7e-150">Ukázková aplikace vyžaduje explicitní pořadí zpracování tras, které předvádí několik scénářů směrování pomocí jedné aplikace.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-150">The sample app requires an explicit route processing order to demonstrate several routing scenarios using a single app.</span></span> <span data-ttu-id="3bd7e-151">Měli byste se však pokusit vyhnout postup nastavení trasy `Order` v produkčních aplikacích.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-151">However, you should attempt to avoid the practice of setting route `Order` in production apps.</span></span>

Razor<span data-ttu-id="3bd7e-152">Směrování stránek a směrování kontroléru MVC sdílí implementaci.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-152"> Pages routing and MVC controller routing share an implementation.</span></span> <span data-ttu-id="3bd7e-153">Informace o pořadí směrování v tématech MVC jsou k dispozici při [směrování na akce kontroleru: trasy atributů řazení](xref:mvc/controllers/routing#ordering-attribute-routes).</span><span class="sxs-lookup"><span data-stu-id="3bd7e-153">Information on route order in the MVC topics is available at [Routing to controller actions: Ordering attribute routes](xref:mvc/controllers/routing#ordering-attribute-routes).</span></span>

## <a name="model-conventions"></a><span data-ttu-id="3bd7e-154">Modelové konvence</span><span class="sxs-lookup"><span data-stu-id="3bd7e-154">Model conventions</span></span>

<span data-ttu-id="3bd7e-155">Přidejte delegáta pro <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> Přidání [modelových konvencí](xref:mvc/controllers/application-model#conventions) , které se vztahují na Razor stránky.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-155">Add a delegate for <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> to add [model conventions](xref:mvc/controllers/application-model#conventions) that apply to Razor Pages.</span></span>

### <a name="add-a-route-model-convention-to-all-pages"></a><span data-ttu-id="3bd7e-156">Přidat konvenci modelu směrování na všechny stránky</span><span class="sxs-lookup"><span data-stu-id="3bd7e-156">Add a route model convention to all pages</span></span>

<span data-ttu-id="3bd7e-157">Slouží <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> k vytvoření a přidání <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> kolekce <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instancí, které se použijí během vytváření modelu směrování stránky.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-157">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page route model construction.</span></span>

<span data-ttu-id="3bd7e-158">Ukázková aplikace přidá `{globalTemplate?}` šablonu směrování na všechny stránky v aplikaci:</span><span class="sxs-lookup"><span data-stu-id="3bd7e-158">The sample app adds a `{globalTemplate?}` route template to all of the pages in the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

<span data-ttu-id="3bd7e-159"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*>Vlastnost pro <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> je nastavena na `1` .</span><span class="sxs-lookup"><span data-stu-id="3bd7e-159">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `1`.</span></span> <span data-ttu-id="3bd7e-160">Tím se zajistí následující chování při shodě trasy v ukázkové aplikaci:</span><span class="sxs-lookup"><span data-stu-id="3bd7e-160">This ensures the following route matching behavior in the sample app:</span></span>

* <span data-ttu-id="3bd7e-161">Šablona směrování pro `TheContactPage/{text?}` je přidána později v tématu.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-161">A route template for `TheContactPage/{text?}` is added later in the topic.</span></span> <span data-ttu-id="3bd7e-162">Trasa stránky kontaktu má výchozí pořadí `null` ( `Order = 0` ), takže odpovídá před `{globalTemplate?}` šablonou směrování.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-162">The Contact Page route has a default order of `null` (`Order = 0`), so it matches before the `{globalTemplate?}` route template.</span></span>
* <span data-ttu-id="3bd7e-163">`{aboutTemplate?}`Šablona směrování se přidá později v tématu.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-163">An `{aboutTemplate?}` route template is added later in the topic.</span></span> <span data-ttu-id="3bd7e-164">`{aboutTemplate?}`Šabloně je přidělena `Order` `2` .</span><span class="sxs-lookup"><span data-stu-id="3bd7e-164">The `{aboutTemplate?}` template is given an `Order` of `2`.</span></span> <span data-ttu-id="3bd7e-165">Když se na stránce o žádosti vyžádá `/About/RouteDataValue` , "RouteDataValue" se načte do `RouteData.Values["globalTemplate"]` ( `Order = 1` ) a `RouteData.Values["aboutTemplate"]` ne ( `Order = 2` ) kvůli nastavení `Order` Vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-165">When the About page is requested at `/About/RouteDataValue`, "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["aboutTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>
* <span data-ttu-id="3bd7e-166">`{otherPagesTemplate?}`Šablona směrování se přidá později v tématu.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-166">An `{otherPagesTemplate?}` route template is added later in the topic.</span></span> <span data-ttu-id="3bd7e-167">`{otherPagesTemplate?}`Šabloně je přidělena `Order` `2` .</span><span class="sxs-lookup"><span data-stu-id="3bd7e-167">The `{otherPagesTemplate?}` template is given an `Order` of `2`.</span></span> <span data-ttu-id="3bd7e-168">Pokud je pro libovolnou stránku ve složce *Pages/OtherPages* požadováno parametr trasy (například `/OtherPages/Page1/RouteDataValue` ), je hodnota "RouteDataValue" načtena do `RouteData.Values["globalTemplate"]` ( `Order = 1` ) a nikoli `RouteData.Values["otherPagesTemplate"]` ( `Order = 2` ) z důvodu nastavení `Order` Vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-168">When any page in the *Pages/OtherPages* folder is requested with a route parameter (for example, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="3bd7e-169">Pokud je to možné, nenastavte `Order` , které výsledky mají `Order = 0` .</span><span class="sxs-lookup"><span data-stu-id="3bd7e-169">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="3bd7e-170">Pro výběr správné trasy se spoléhá na směrování.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-170">Rely on routing to select the correct route.</span></span>

Razor<span data-ttu-id="3bd7e-171">Možnosti stránek, jako je například přidání <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> , jsou přidány při přidání MVC do kolekce služby v nástroji `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="3bd7e-171"> Pages options, such as adding <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>, are added when MVC is added to the service collection in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="3bd7e-172">Příklad najdete v [ukázkové aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/).</span><span class="sxs-lookup"><span data-stu-id="3bd7e-172">For an example, see the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/).</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="3bd7e-173">Požádejte o stránku ukázek o stránce `localhost:5000/About/GlobalRouteValue` a Prohlédněte si výsledek:</span><span class="sxs-lookup"><span data-stu-id="3bd7e-173">Request the sample's About page at `localhost:5000/About/GlobalRouteValue` and inspect the result:</span></span>

![Stránka o aplikaci je požadována s segmentem směrování GlobalRouteValue.](razor-pages-conventions/_static/about-page-global-template.png)

### <a name="add-an-app-model-convention-to-all-pages"></a><span data-ttu-id="3bd7e-176">Přidání konvence modelu aplikace na všechny stránky</span><span class="sxs-lookup"><span data-stu-id="3bd7e-176">Add an app model convention to all pages</span></span>

<span data-ttu-id="3bd7e-177">Použijte <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> k vytvoření a přidání <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> do kolekce <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instancí, které se použijí během vytváření modelu aplikace stránky.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-177">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page app model construction.</span></span>

<span data-ttu-id="3bd7e-178">Chcete-li předvést tuto a další konvenci později v tématu, ukázková aplikace obsahuje `AddHeaderAttribute` třídu.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-178">To demonstrate this and other conventions later in the topic, the sample app includes an `AddHeaderAttribute` class.</span></span> <span data-ttu-id="3bd7e-179">Konstruktor třídy přijímá `name` řetězec a `values` pole řetězců.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-179">The class constructor accepts a `name` string and a `values` string array.</span></span> <span data-ttu-id="3bd7e-180">Tyto hodnoty jsou použity v `OnResultExecuting` metodě pro nastavení hlavičky odpovědi.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-180">These values are used in its `OnResultExecuting` method to set a response header.</span></span> <span data-ttu-id="3bd7e-181">Úplná třída je uvedena v části [konvence akcí modelu stránky](#page-model-action-conventions) dále v tématu.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-181">The full class is shown in the [Page model action conventions](#page-model-action-conventions) section later in the topic.</span></span>

<span data-ttu-id="3bd7e-182">Ukázková aplikace používá `AddHeaderAttribute` třídu k přidání záhlaví, na `GlobalHeader` všechny stránky v aplikaci:</span><span class="sxs-lookup"><span data-stu-id="3bd7e-182">The sample app uses the `AddHeaderAttribute` class to add a header, `GlobalHeader`, to all of the pages in the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

<span data-ttu-id="3bd7e-183">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="3bd7e-183">*Startup.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet2)]

<span data-ttu-id="3bd7e-184">Požádejte o stránku ukázek o stránce `localhost:5000/About` a Prohlédněte si záhlaví a podívejte se na výsledek:</span><span class="sxs-lookup"><span data-stu-id="3bd7e-184">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![Hlavičky odpovědi stránky o produktu ukazují, že byl přidán GlobalHeader.](razor-pages-conventions/_static/about-page-global-header.png)

### <a name="add-a-handler-model-convention-to-all-pages"></a><span data-ttu-id="3bd7e-186">Přidat konvenci modelu obslužné rutiny na všechny stránky</span><span class="sxs-lookup"><span data-stu-id="3bd7e-186">Add a handler model convention to all pages</span></span>

<span data-ttu-id="3bd7e-187">Použijte <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> k vytvoření a přidání <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> do kolekce <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instancí, které jsou použity během vytváření modelu obslužné rutiny stránky.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-187">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page handler model construction.</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalPageHandlerModelConvention.cs?name=snippet1)]

<span data-ttu-id="3bd7e-188">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="3bd7e-188">*Startup.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet10)]

## <a name="page-route-action-conventions"></a><span data-ttu-id="3bd7e-189">Konvence akcí při směrování stránky</span><span class="sxs-lookup"><span data-stu-id="3bd7e-189">Page route action conventions</span></span>

<span data-ttu-id="3bd7e-190">Výchozí zprostředkovatel modelu směrování, který je odvozen od <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> konvencí vyvolání, které jsou navrženy tak, aby poskytovaly body rozšiřitelnosti pro konfiguraci tras stránky.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-190">The default route model provider that derives from <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> invokes conventions which are designed to provide extensibility points for configuring page routes.</span></span>

### <a name="folder-route-model-convention"></a><span data-ttu-id="3bd7e-191">Konvence modelu směrování složky</span><span class="sxs-lookup"><span data-stu-id="3bd7e-191">Folder route model convention</span></span>

<span data-ttu-id="3bd7e-192">Slouží <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> k vytvoření a přidání <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> akce, která vyvolá akci <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> pro všechny stránky v zadané složce.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-192">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> for all of the pages under the specified folder.</span></span>

<span data-ttu-id="3bd7e-193">Ukázková aplikace používá <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> k přidání `{otherPagesTemplate?}` šablony směrování na stránky ve složce *OtherPages* :</span><span class="sxs-lookup"><span data-stu-id="3bd7e-193">The sample app uses <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> to add an `{otherPagesTemplate?}` route template to the pages in the *OtherPages* folder:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet3)]

<span data-ttu-id="3bd7e-194"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*>Vlastnost pro <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> je nastavena na `2` .</span><span class="sxs-lookup"><span data-stu-id="3bd7e-194">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `2`.</span></span> <span data-ttu-id="3bd7e-195">Tím se zajistí, že šablona pro `{globalTemplate?}` (nastavená výše v tématu na `1` ) má prioritu první pozice hodnoty dat trasy při zadání jedné hodnoty trasy.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-195">This ensures that the template for `{globalTemplate?}` (set earlier in the topic to `1`) is given priority for the first route data value position when a single route value is provided.</span></span> <span data-ttu-id="3bd7e-196">Pokud se stránka ve složce *Pages/OtherPages* požaduje s hodnotou parametru trasy (například `/OtherPages/Page1/RouteDataValue` ), "RouteDataValue" se načte do `RouteData.Values["globalTemplate"]` ( `Order = 1` ) a ne `RouteData.Values["otherPagesTemplate"]` ( `Order = 2` ) kvůli nastavení `Order` Vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-196">If a page in the *Pages/OtherPages* folder is requested with a route parameter value (for example, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="3bd7e-197">Pokud je to možné, nenastavte `Order` , které výsledky mají `Order = 0` .</span><span class="sxs-lookup"><span data-stu-id="3bd7e-197">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="3bd7e-198">Pro výběr správné trasy se spoléhá na směrování.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-198">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="3bd7e-199">Vyžádejte si Page1 stránku ukázky `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` a Prohlédněte si výsledek:</span><span class="sxs-lookup"><span data-stu-id="3bd7e-199">Request the sample's Page1 page at `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` and inspect the result:</span></span>

![Page1 ve složce OtherPages se vyžádá s segmentem směrování GlobalRouteValue a OtherPagesRouteValue.](razor-pages-conventions/_static/otherpages-page1-global-and-otherpages-templates.png)

### <a name="page-route-model-convention"></a><span data-ttu-id="3bd7e-202">Konvence modelu směrování stránky</span><span class="sxs-lookup"><span data-stu-id="3bd7e-202">Page route model convention</span></span>

<span data-ttu-id="3bd7e-203">Slouží <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> k vytvoření a přidání <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> akce, která vyvolá akci na <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> stránce pro stránku se zadaným názvem.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-203">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> for the page with the specified name.</span></span>

<span data-ttu-id="3bd7e-204">Ukázková aplikace používá `AddPageRouteModelConvention` k přidání `{aboutTemplate?}` šablony směrování na stránku o aplikaci:</span><span class="sxs-lookup"><span data-stu-id="3bd7e-204">The sample app uses `AddPageRouteModelConvention` to add an `{aboutTemplate?}` route template to the About page:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet4)]

<span data-ttu-id="3bd7e-205"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*>Vlastnost pro <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> je nastavena na `2` .</span><span class="sxs-lookup"><span data-stu-id="3bd7e-205">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `2`.</span></span> <span data-ttu-id="3bd7e-206">Tím se zajistí, že šablona pro `{globalTemplate?}` (nastavená výše v tématu na `1` ) má prioritu první pozice hodnoty dat trasy při zadání jedné hodnoty trasy.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-206">This ensures that the template for `{globalTemplate?}` (set earlier in the topic to `1`) is given priority for the first route data value position when a single route value is provided.</span></span> <span data-ttu-id="3bd7e-207">Pokud se stránka o žádosti vyžaduje s hodnotou parametru Route na `/About/RouteDataValue` , je "RouteDataValue" načten do `RouteData.Values["globalTemplate"]` ( `Order = 1` ) a nikoli `RouteData.Values["aboutTemplate"]` ( `Order = 2` ) z důvodu nastavení `Order` Vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-207">If the About page is requested with a route parameter value at `/About/RouteDataValue`, "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["aboutTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="3bd7e-208">Pokud je to možné, nenastavte `Order` , které výsledky mají `Order = 0` .</span><span class="sxs-lookup"><span data-stu-id="3bd7e-208">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="3bd7e-209">Pro výběr správné trasy se spoléhá na směrování.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-209">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="3bd7e-210">Požádejte o stránku ukázek o stránce `localhost:5000/About/GlobalRouteValue/AboutRouteValue` a Prohlédněte si výsledek:</span><span class="sxs-lookup"><span data-stu-id="3bd7e-210">Request the sample's About page at `localhost:5000/About/GlobalRouteValue/AboutRouteValue` and inspect the result:</span></span>

![U segmentů směrování pro GlobalRouteValue a AboutRouteValue se žádá o stránku.](razor-pages-conventions/_static/about-page-global-and-about-templates.png)

## <a name="use-a-parameter-transformer-to-customize-page-routes"></a><span data-ttu-id="3bd7e-213">Použití transformátoru parametrů k přizpůsobení cest stránky</span><span class="sxs-lookup"><span data-stu-id="3bd7e-213">Use a parameter transformer to customize page routes</span></span>

<span data-ttu-id="3bd7e-214">Trasy stránky generované ASP.NET Core lze přizpůsobit pomocí transformátoru parametrů.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-214">Page routes generated by ASP.NET Core can be customized using a parameter transformer.</span></span> <span data-ttu-id="3bd7e-215">Parametr Transformer implementuje `IOutboundParameterTransformer` a transformuje hodnotu parametrů.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-215">A parameter transformer implements `IOutboundParameterTransformer` and transforms the value of parameters.</span></span> <span data-ttu-id="3bd7e-216">Například vlastní `SlugifyParameterTransformer` parametr Transformer změní `SubscriptionManagement` hodnotu Route na `subscription-management` .</span><span class="sxs-lookup"><span data-stu-id="3bd7e-216">For example, a custom `SlugifyParameterTransformer` parameter transformer changes the `SubscriptionManagement` route value to `subscription-management`.</span></span>

<span data-ttu-id="3bd7e-217">`PageRouteTransformerConvention`Konvence modelu směrování stránky aplikuje transformátor parametrů na složku a název souboru segmentů automaticky generovaných tras stránky v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-217">The `PageRouteTransformerConvention` page route model convention applies a parameter transformer to the folder and file name segments of automatically generated page routes in an app.</span></span> <span data-ttu-id="3bd7e-218">Například Razor soubor stránek na */Pages/SubscriptionManagement/ViewAll.cshtml* by měl svou trasu přepsanou z `/SubscriptionManagement/ViewAll` do `/subscription-management/view-all` .</span><span class="sxs-lookup"><span data-stu-id="3bd7e-218">For example, the Razor Pages file at */Pages/SubscriptionManagement/ViewAll.cshtml* would have its route rewritten from `/SubscriptionManagement/ViewAll` to `/subscription-management/view-all`.</span></span>

<span data-ttu-id="3bd7e-219">`PageRouteTransformerConvention`transformuje automaticky generované segmenty stránky trasy, které pocházejí ze Razor složky a názvu souboru stránky.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-219">`PageRouteTransformerConvention` only transforms the automatically generated segments of a page route that come from the Razor Pages folder and file name.</span></span> <span data-ttu-id="3bd7e-220">Netransformuje segmenty směrování přidané s `@page` direktivou.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-220">It doesn't transform route segments added with the `@page` directive.</span></span> <span data-ttu-id="3bd7e-221">Konvence také netransformuje trasy, které přidal <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> .</span><span class="sxs-lookup"><span data-stu-id="3bd7e-221">The convention also doesn't transform routes added by <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>.</span></span>

<span data-ttu-id="3bd7e-222">`PageRouteTransformerConvention`Je zaregistrován jako možnost v `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="3bd7e-222">The `PageRouteTransformerConvention` is registered as an option in `Startup.ConfigureServices`:</span></span>

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

## <a name="configure-a-page-route"></a><span data-ttu-id="3bd7e-223">Konfigurace trasy stránky</span><span class="sxs-lookup"><span data-stu-id="3bd7e-223">Configure a page route</span></span>

<span data-ttu-id="3bd7e-224">Slouží <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> ke konfiguraci směrování na stránku na zadané cestě stránky.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-224">Use <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> to configure a route to a page at the specified page path.</span></span> <span data-ttu-id="3bd7e-225">Vygenerované odkazy na stránku používají zadanou trasu.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-225">Generated links to the page use your specified route.</span></span> <span data-ttu-id="3bd7e-226">`AddPageRoute`slouží `AddPageRouteModelConvention` k vytvoření trasy.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-226">`AddPageRoute` uses `AddPageRouteModelConvention` to establish the route.</span></span>

<span data-ttu-id="3bd7e-227">Ukázková aplikace vytvoří trasu `/TheContactPage` pro *kontakt. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="3bd7e-227">The sample app creates a route to `/TheContactPage` for *Contact.cshtml*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet5)]

<span data-ttu-id="3bd7e-228">Stránku kontaktů lze také kontaktovat `/Contact` prostřednictvím výchozí trasy.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-228">The Contact page can also be reached at `/Contact` via its default route.</span></span>

<span data-ttu-id="3bd7e-229">Vlastní trasa ukázkové aplikace na stránku kontaktů umožňuje volitelný `text` segment směrování ( `{text?}` ).</span><span class="sxs-lookup"><span data-stu-id="3bd7e-229">The sample app's custom route to the Contact page allows for an optional `text` route segment (`{text?}`).</span></span> <span data-ttu-id="3bd7e-230">Tato stránka obsahuje také volitelný segment v rámci své `@page` směrnice pro případ, že návštěvník přistupuje ke stránce v `/Contact` cestě:</span><span class="sxs-lookup"><span data-stu-id="3bd7e-230">The page also includes this optional segment in its `@page` directive in case the visitor accesses the page at its `/Contact` route:</span></span>

[!code-cshtml[](razor-pages-conventions/samples/3.x/SampleApp/Pages/Contact.cshtml?highlight=1)]

<span data-ttu-id="3bd7e-231">Všimněte si, že adresa URL vygenerovaná pro odkaz **kontaktu** na vykreslené stránce odráží aktualizovanou trasu:</span><span class="sxs-lookup"><span data-stu-id="3bd7e-231">Note that the URL generated for the **Contact** link in the rendered page reflects the updated route:</span></span>

![Odkaz na ukázkový kontakt aplikace na navigačním panelu](razor-pages-conventions/_static/contact-link.png)

![Kontrola odkazu na kontakt ve vykresleném kódu HTML znamená, že odkaz href je nastaven na hodnotu "/TheContactPage".](razor-pages-conventions/_static/contact-link-source.png)

<span data-ttu-id="3bd7e-234">Navštivte stránku kontaktů buď na své běžné trase, `/Contact` nebo na vlastní trasu `/TheContactPage` .</span><span class="sxs-lookup"><span data-stu-id="3bd7e-234">Visit the Contact page at either its ordinary route, `/Contact`, or the custom route, `/TheContactPage`.</span></span> <span data-ttu-id="3bd7e-235">Pokud zadáte další `text` segment směrování, stránka zobrazuje segment, který zakódovaný ve formátu HTML, který zadáte:</span><span class="sxs-lookup"><span data-stu-id="3bd7e-235">If you supply an additional `text` route segment, the page shows the HTML-encoded segment that you provide:</span></span>

![Prohlížeč Edge – příklad poskytnutí volitelného segmentu "text" trasy "TextValue" v adrese URL](razor-pages-conventions/_static/route-segment-with-custom-route.png)

## <a name="page-model-action-conventions"></a><span data-ttu-id="3bd7e-238">Konvence akcí modelu stránky</span><span class="sxs-lookup"><span data-stu-id="3bd7e-238">Page model action conventions</span></span>

<span data-ttu-id="3bd7e-239">Výchozí zprostředkovatel stránky, který implementuje implementují <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> konvence, které jsou navrženy tak, aby poskytovaly body rozšiřitelnosti pro konfiguraci modelů stránek.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-239">The default page model provider that implements <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> invokes conventions which are designed to provide extensibility points for configuring page models.</span></span> <span data-ttu-id="3bd7e-240">Tyto konvence jsou užitečné při sestavování a úpravách scénářů zjišťování a zpracování stránky.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-240">These conventions are useful when building and modifying page discovery and processing scenarios.</span></span>

<span data-ttu-id="3bd7e-241">V příkladech v této části používá ukázková aplikace třídu, `AddHeaderAttribute` která je <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute> , která používá hlavičku odpovědi:</span><span class="sxs-lookup"><span data-stu-id="3bd7e-241">For the examples in this section, the sample app uses an `AddHeaderAttribute` class, which is a <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>, that applies a response header:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

<span data-ttu-id="3bd7e-242">Pomocí konvence Ukázka ukazuje, jak použít atribut na všechny stránky ve složce a na jednu stránku.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-242">Using conventions, the sample demonstrates how to apply the attribute to all of the pages in a folder and to a single page.</span></span>

<span data-ttu-id="3bd7e-243">**Konvence modelu aplikace složky**</span><span class="sxs-lookup"><span data-stu-id="3bd7e-243">**Folder app model convention**</span></span>

<span data-ttu-id="3bd7e-244">Slouží <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> k vytvoření a přidání <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> akce, která vyvolá akci u <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> instancí pro všechny stránky v zadané složce.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-244">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> that invokes an action on <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> instances for all pages under the specified folder.</span></span>

<span data-ttu-id="3bd7e-245">Ukázka demonstruje použití přidáním `AddFolderApplicationModelConvention` záhlaví, na `OtherPagesHeader` stránkách ve složce *OtherPages* této aplikace:</span><span class="sxs-lookup"><span data-stu-id="3bd7e-245">The sample demonstrates the use of `AddFolderApplicationModelConvention` by adding a header, `OtherPagesHeader`, to the pages inside the *OtherPages* folder of the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet6)]

<span data-ttu-id="3bd7e-246">Vyžádejte si ukázkovou stránku Page1 na stránce a prohlédněte `localhost:5000/OtherPages/Page1` si záhlaví a podívejte se na výsledek:</span><span class="sxs-lookup"><span data-stu-id="3bd7e-246">Request the sample's Page1 page at `localhost:5000/OtherPages/Page1` and inspect the headers to view the result:</span></span>

![Hlavičky odpovědi stránky OtherPages/Page1 ukazují, že byla přidána OtherPagesHeader.](razor-pages-conventions/_static/page1-otherpages-header.png)

<span data-ttu-id="3bd7e-248">**Konvence modelu aplikace stránky**</span><span class="sxs-lookup"><span data-stu-id="3bd7e-248">**Page app model convention**</span></span>

<span data-ttu-id="3bd7e-249">Slouží <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> k vytvoření a přidání <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> akce, která vyvolá akci na <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> stránce pro stránku se zadaným názvem.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-249">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> for the page with the specified name.</span></span>

<span data-ttu-id="3bd7e-250">Ukázka demonstruje použití přidáním `AddPageApplicationModelConvention` záhlaví, na `AboutHeader` stránce o produktu:</span><span class="sxs-lookup"><span data-stu-id="3bd7e-250">The sample demonstrates the use of `AddPageApplicationModelConvention` by adding a header, `AboutHeader`, to the About page:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet7)]

<span data-ttu-id="3bd7e-251">Požádejte o stránku ukázek o stránce `localhost:5000/About` a Prohlédněte si záhlaví a podívejte se na výsledek:</span><span class="sxs-lookup"><span data-stu-id="3bd7e-251">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![Hlavičky odpovědi stránky o produktu ukazují, že byl přidán AboutHeader.](razor-pages-conventions/_static/about-page-about-header.png)

<span data-ttu-id="3bd7e-253">**Konfigurace filtru**</span><span class="sxs-lookup"><span data-stu-id="3bd7e-253">**Configure a filter**</span></span>

<span data-ttu-id="3bd7e-254"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*>nakonfiguruje zadaný filtr na použití.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-254"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configures the specified filter to apply.</span></span> <span data-ttu-id="3bd7e-255">Můžete implementovat třídu filtru, ale ukázková aplikace ukazuje, jak implementovat filtr ve výrazu lambda, který je implementován na pozadí jako objekt pro vytváření, který vrací filtr:</span><span class="sxs-lookup"><span data-stu-id="3bd7e-255">You can implement a filter class, but the sample app shows how to implement a filter in a lambda expression, which is implemented behind-the-scenes as a factory that returns a filter:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet8)]

<span data-ttu-id="3bd7e-256">Model webové aplikace se používá ke kontrole relativní cesty pro segmenty, které vedou ke stránce Page2 ve složce *OtherPages* .</span><span class="sxs-lookup"><span data-stu-id="3bd7e-256">The page app model is used to check the relative path for segments that lead to the Page2 page in the *OtherPages* folder.</span></span> <span data-ttu-id="3bd7e-257">Pokud podmínka projde, přidá se hlavička.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-257">If the condition passes, a header is added.</span></span> <span data-ttu-id="3bd7e-258">V takovém případě se `EmptyFilter` použije.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-258">If not, the `EmptyFilter` is applied.</span></span>

<span data-ttu-id="3bd7e-259">`EmptyFilter`je [Filtr akcí](xref:mvc/controllers/filters#action-filters).</span><span class="sxs-lookup"><span data-stu-id="3bd7e-259">`EmptyFilter` is an [Action filter](xref:mvc/controllers/filters#action-filters).</span></span> <span data-ttu-id="3bd7e-260">Vzhledem k tomu, že filtry akcí jsou ignorovány Razor stránkami, `EmptyFilter` nemá žádný vliv na zamýšlenou cestu, pokud cesta neobsahuje `OtherPages/Page2` .</span><span class="sxs-lookup"><span data-stu-id="3bd7e-260">Since Action filters are ignored by Razor Pages, the `EmptyFilter` has no effect as intended if the path doesn't contain `OtherPages/Page2`.</span></span>

<span data-ttu-id="3bd7e-261">Vyžádejte si ukázkovou stránku Page2 na stránce a prohlédněte `localhost:5000/OtherPages/Page2` si záhlaví a podívejte se na výsledek:</span><span class="sxs-lookup"><span data-stu-id="3bd7e-261">Request the sample's Page2 page at `localhost:5000/OtherPages/Page2` and inspect the headers to view the result:</span></span>

![OtherPagesPage2Header se přidá do odpovědi pro Page2.](razor-pages-conventions/_static/page2-filter-header.png)

<span data-ttu-id="3bd7e-263">**Konfigurace objektu pro vytváření filtrů**</span><span class="sxs-lookup"><span data-stu-id="3bd7e-263">**Configure a filter factory**</span></span>

<span data-ttu-id="3bd7e-264"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*>nakonfiguruje určený objekt pro vytváření na použití [filtrů](xref:mvc/controllers/filters) na všechny Razor stránky.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-264"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configures the specified factory to apply [filters](xref:mvc/controllers/filters) to all Razor Pages.</span></span>

<span data-ttu-id="3bd7e-265">Ukázková aplikace poskytuje příklad použití [objektu pro vytváření filtru](xref:mvc/controllers/filters#ifilterfactory) přidáním hlavičky, se `FilterFactoryHeader` dvěma hodnotami na stránkách aplikace:</span><span class="sxs-lookup"><span data-stu-id="3bd7e-265">The sample app provides an example of using a [filter factory](xref:mvc/controllers/filters#ifilterfactory) by adding a header, `FilterFactoryHeader`, with two values to the app's pages:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet9)]

<span data-ttu-id="3bd7e-266">*AddHeaderWithFactory.cs*:</span><span class="sxs-lookup"><span data-stu-id="3bd7e-266">*AddHeaderWithFactory.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Factories/AddHeaderWithFactory.cs?name=snippet1)]

<span data-ttu-id="3bd7e-267">Požádejte o stránku ukázek o stránce `localhost:5000/About` a Prohlédněte si záhlaví a podívejte se na výsledek:</span><span class="sxs-lookup"><span data-stu-id="3bd7e-267">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![Hlavičky odpovědi stránky o stránku zobrazují, že byly přidány dvě hlavičky FilterFactoryHeader.](razor-pages-conventions/_static/about-page-filter-factory-header.png)

## <a name="mvc-filters-and-the-page-filter-ipagefilter"></a><span data-ttu-id="3bd7e-269">Filtry MVC a filtr stránky (IPageFilter)</span><span class="sxs-lookup"><span data-stu-id="3bd7e-269">MVC Filters and the Page filter (IPageFilter)</span></span>

<span data-ttu-id="3bd7e-270">[Filtry akcí](xref:mvc/controllers/filters#action-filters) MVC jsou stránky ignorovány Razor , protože Razor stránky používají metody obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-270">MVC [Action filters](xref:mvc/controllers/filters#action-filters) are ignored by Razor Pages, since Razor Pages use handler methods.</span></span> <span data-ttu-id="3bd7e-271">Další typy filtrů MVC jsou k dispozici pro použití: [autorizace](xref:mvc/controllers/filters#authorization-filters), [výjimka](xref:mvc/controllers/filters#exception-filters), [prostředek](xref:mvc/controllers/filters#resource-filters)a [výsledek](xref:mvc/controllers/filters#result-filters).</span><span class="sxs-lookup"><span data-stu-id="3bd7e-271">Other types of MVC filters are available for you to use: [Authorization](xref:mvc/controllers/filters#authorization-filters), [Exception](xref:mvc/controllers/filters#exception-filters), [Resource](xref:mvc/controllers/filters#resource-filters), and [Result](xref:mvc/controllers/filters#result-filters).</span></span> <span data-ttu-id="3bd7e-272">Další informace najdete v tématu [filtry](xref:mvc/controllers/filters) .</span><span class="sxs-lookup"><span data-stu-id="3bd7e-272">For more information, see the [Filters](xref:mvc/controllers/filters) topic.</span></span>

<span data-ttu-id="3bd7e-273">Filtr stránky ( <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> ) je filtr, který se vztahuje na Razor stránky.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-273">The Page filter (<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>) is a filter that applies to Razor Pages.</span></span> <span data-ttu-id="3bd7e-274">Další informace najdete v tématu [metody filtrování pro Razor stránky](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="3bd7e-274">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3bd7e-275">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="3bd7e-275">Additional resources</span></span>

* <xref:security/authorization/razor-pages-authorization>
* <xref:mvc/controllers/areas#areas-with-razor-pages>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="3bd7e-276">Naučte se používat [konvence směrování stránky a poskytovatele modelů aplikací](xref:mvc/controllers/application-model#conventions) k řízení směrování, zjišťování a zpracování stránky v Razor aplikacích stránky.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-276">Learn how to use page [route and app model provider conventions](xref:mvc/controllers/application-model#conventions) to control page routing, discovery, and processing in Razor Pages apps.</span></span>

<span data-ttu-id="3bd7e-277">Pokud potřebujete nakonfigurovat vlastní trasy stránky pro jednotlivé stránky, nakonfigurujte směrování na stránky pomocí [AddPageRoute konvence](#configure-a-page-route) popsané dále v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-277">When you need to configure custom page routes for individual pages, configure routing to pages with the [AddPageRoute convention](#configure-a-page-route) described later in this topic.</span></span>

<span data-ttu-id="3bd7e-278">Chcete-li určit trasu stránky, přidat segmenty směrování nebo přidat parametry do trasy, použijte `@page` direktivu stránky.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-278">To specify a page route, add route segments, or add parameters to a route, use the page's `@page` directive.</span></span> <span data-ttu-id="3bd7e-279">Další informace najdete v tématu [vlastní trasy](xref:razor-pages/index#custom-routes).</span><span class="sxs-lookup"><span data-stu-id="3bd7e-279">For more information, see [Custom routes](xref:razor-pages/index#custom-routes).</span></span>

<span data-ttu-id="3bd7e-280">Existují vyhrazená slova, která nelze použít jako segmenty směrování nebo názvy parametrů.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-280">There are reserved words that can't be used as route segments or parameter names.</span></span> <span data-ttu-id="3bd7e-281">Další informace najdete v tématu [směrování: názvy rezervovaných směrování](xref:fundamentals/routing#reserved-routing-names).</span><span class="sxs-lookup"><span data-stu-id="3bd7e-281">For more information, see [Routing: Reserved routing names](xref:fundamentals/routing#reserved-routing-names).</span></span>

<span data-ttu-id="3bd7e-282">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="3bd7e-282">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

| <span data-ttu-id="3bd7e-283">Scénář</span><span class="sxs-lookup"><span data-stu-id="3bd7e-283">Scenario</span></span> | <span data-ttu-id="3bd7e-284">Ukázka znázorňuje...</span><span class="sxs-lookup"><span data-stu-id="3bd7e-284">The sample demonstrates ...</span></span> |
| -------- | --------------------------- |
| [<span data-ttu-id="3bd7e-285">Modelové konvence</span><span class="sxs-lookup"><span data-stu-id="3bd7e-285">Model conventions</span></span>](#model-conventions)<br><br><span data-ttu-id="3bd7e-286">Konvence. Add</span><span class="sxs-lookup"><span data-stu-id="3bd7e-286">Conventions.Add</span></span><ul><li><span data-ttu-id="3bd7e-287">IPageRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="3bd7e-287">IPageRouteModelConvention</span></span></li><li><span data-ttu-id="3bd7e-288">IPageApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="3bd7e-288">IPageApplicationModelConvention</span></span></li><li><span data-ttu-id="3bd7e-289">IPageHandlerModelConvention</span><span class="sxs-lookup"><span data-stu-id="3bd7e-289">IPageHandlerModelConvention</span></span></li></ul> | <span data-ttu-id="3bd7e-290">Přidejte šablonu a hlavičku směrování na stránky aplikace.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-290">Add a route template and header to an app's pages.</span></span> |
| [<span data-ttu-id="3bd7e-291">Konvence akcí při směrování stránky</span><span class="sxs-lookup"><span data-stu-id="3bd7e-291">Page route action conventions</span></span>](#page-route-action-conventions)<ul><li><span data-ttu-id="3bd7e-292">AddFolderRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="3bd7e-292">AddFolderRouteModelConvention</span></span></li><li><span data-ttu-id="3bd7e-293">AddPageRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="3bd7e-293">AddPageRouteModelConvention</span></span></li><li><span data-ttu-id="3bd7e-294">AddPageRoute</span><span class="sxs-lookup"><span data-stu-id="3bd7e-294">AddPageRoute</span></span></li></ul> | <span data-ttu-id="3bd7e-295">Přidejte šablonu směrování do stránek ve složce a na jednu stránku.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-295">Add a route template to pages in a folder and to a single page.</span></span> |
| [<span data-ttu-id="3bd7e-296">Konvence akcí modelu stránky</span><span class="sxs-lookup"><span data-stu-id="3bd7e-296">Page model action conventions</span></span>](#page-model-action-conventions)<ul><li><span data-ttu-id="3bd7e-297">AddFolderApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="3bd7e-297">AddFolderApplicationModelConvention</span></span></li><li><span data-ttu-id="3bd7e-298">AddPageApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="3bd7e-298">AddPageApplicationModelConvention</span></span></li><li><span data-ttu-id="3bd7e-299">ConfigureFilter (filtrovat třídu, lambda výraz nebo objekt pro vytváření filtru)</span><span class="sxs-lookup"><span data-stu-id="3bd7e-299">ConfigureFilter (filter class, lambda expression, or filter factory)</span></span></li></ul> | <span data-ttu-id="3bd7e-300">Umožňuje přidat záhlaví na stránky ve složce, přidat záhlaví na jednu stránku a nakonfigurovat [objekt pro vytváření filtru](xref:mvc/controllers/filters#ifilterfactory) tak, aby přidal hlavičku na stránky aplikace.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-300">Add a header to pages in a folder, add a header to a single page, and configure a [filter factory](xref:mvc/controllers/filters#ifilterfactory) to add a header to an app's pages.</span></span> |

Razor<span data-ttu-id="3bd7e-301">Konvence stránek jsou přidány a nakonfigurovány pomocí <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> metody rozšíření pro <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> kolekci služeb ve `Startup` třídě.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-301"> Pages conventions are added and configured using the <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> extension method to <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> on the service collection in the `Startup` class.</span></span> <span data-ttu-id="3bd7e-302">Následující příklady konvence jsou vysvětleny dále v tomto tématu:</span><span class="sxs-lookup"><span data-stu-id="3bd7e-302">The following convention examples are explained later in this topic:</span></span>

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

## <a name="route-order"></a><span data-ttu-id="3bd7e-303">Pořadí směrování</span><span class="sxs-lookup"><span data-stu-id="3bd7e-303">Route order</span></span>

<span data-ttu-id="3bd7e-304">Trasy určují <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> pro zpracování (odpovídání tras).</span><span class="sxs-lookup"><span data-stu-id="3bd7e-304">Routes specify an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> for processing (route matching).</span></span>

| <span data-ttu-id="3bd7e-305">Objednání</span><span class="sxs-lookup"><span data-stu-id="3bd7e-305">Order</span></span>            | <span data-ttu-id="3bd7e-306">Chování</span><span class="sxs-lookup"><span data-stu-id="3bd7e-306">Behavior</span></span> |
| :--------------: | -------- |
| <span data-ttu-id="3bd7e-307">-1</span><span class="sxs-lookup"><span data-stu-id="3bd7e-307">-1</span></span>               | <span data-ttu-id="3bd7e-308">Trasa je zpracována před zpracováním jiných tras.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-308">The route is processed before other routes are processed.</span></span> |
| <span data-ttu-id="3bd7e-309">0</span><span class="sxs-lookup"><span data-stu-id="3bd7e-309">0</span></span>                | <span data-ttu-id="3bd7e-310">Pořadí není zadáno (výchozí hodnota).</span><span class="sxs-lookup"><span data-stu-id="3bd7e-310">Order isn't specified (default value).</span></span> <span data-ttu-id="3bd7e-311">Nepřiřazuje se `Order` ( `Order = null` ) výchozí hodnota trasy `Order` na 0 (nula) pro zpracování.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-311">Not assigning `Order` (`Order = null`) defaults the route `Order` to 0 (zero) for processing.</span></span> |
| <span data-ttu-id="3bd7e-312">1, 2, &hellip; n</span><span class="sxs-lookup"><span data-stu-id="3bd7e-312">1, 2, &hellip; n</span></span> | <span data-ttu-id="3bd7e-313">Určuje pořadí zpracování směrování.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-313">Specifies the route processing order.</span></span> |

<span data-ttu-id="3bd7e-314">Zpracování směrování je zřízené podle konvence:</span><span class="sxs-lookup"><span data-stu-id="3bd7e-314">Route processing is established by convention:</span></span>

* <span data-ttu-id="3bd7e-315">Trasy jsou zpracovávány v sekvenčním pořadí (-1, 0, 1, 2, &hellip; n).</span><span class="sxs-lookup"><span data-stu-id="3bd7e-315">Routes are processed in sequential order (-1, 0, 1, 2, &hellip; n).</span></span>
* <span data-ttu-id="3bd7e-316">V případě, že trasy mají stejný směr `Order` , je nejdříve porovnána konkrétní trasa, za kterou následuje méně specifických tras.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-316">When routes have the same `Order`, the most specific route is matched first followed by less specific routes.</span></span>
* <span data-ttu-id="3bd7e-317">Když se trasy se stejným `Order` počtem parametrů shodují s adresou URL požadavku, trasy se zpracovávají v pořadí, v jakém jsou přidány do <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection> .</span><span class="sxs-lookup"><span data-stu-id="3bd7e-317">When routes with the same `Order` and the same number of parameters match a request URL, routes are processed in the order that they're added to the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>.</span></span>

<span data-ttu-id="3bd7e-318">Pokud je to možné, vyhněte se v závislosti na zavedeném pořadí zpracování směrování.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-318">If possible, avoid depending on an established route processing order.</span></span> <span data-ttu-id="3bd7e-319">Obecně směrování vybírá správnou trasu s odpovídající adresou URL.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-319">Generally, routing selects the correct route with URL matching.</span></span> <span data-ttu-id="3bd7e-320">Pokud musíte nastavit vlastnosti směrování `Order` na správně směrované požadavky, schéma směrování aplikace je pravděpodobně matoucí pro klienty a křehké na údržbu.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-320">If you must set route `Order` properties to route requests correctly, the app's routing scheme is probably confusing to clients and fragile to maintain.</span></span> <span data-ttu-id="3bd7e-321">Vyhledejte zjednodušené schéma směrování aplikace.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-321">Seek to simplify the app's routing scheme.</span></span> <span data-ttu-id="3bd7e-322">Ukázková aplikace vyžaduje explicitní pořadí zpracování tras, které předvádí několik scénářů směrování pomocí jedné aplikace.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-322">The sample app requires an explicit route processing order to demonstrate several routing scenarios using a single app.</span></span> <span data-ttu-id="3bd7e-323">Měli byste se však pokusit vyhnout postup nastavení trasy `Order` v produkčních aplikacích.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-323">However, you should attempt to avoid the practice of setting route `Order` in production apps.</span></span>

Razor<span data-ttu-id="3bd7e-324">Směrování stránek a směrování kontroléru MVC sdílí implementaci.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-324"> Pages routing and MVC controller routing share an implementation.</span></span> <span data-ttu-id="3bd7e-325">Informace o pořadí směrování v tématech MVC jsou k dispozici při [směrování na akce kontroleru: trasy atributů řazení](xref:mvc/controllers/routing#ordering-attribute-routes).</span><span class="sxs-lookup"><span data-stu-id="3bd7e-325">Information on route order in the MVC topics is available at [Routing to controller actions: Ordering attribute routes](xref:mvc/controllers/routing#ordering-attribute-routes).</span></span>

## <a name="model-conventions"></a><span data-ttu-id="3bd7e-326">Modelové konvence</span><span class="sxs-lookup"><span data-stu-id="3bd7e-326">Model conventions</span></span>

<span data-ttu-id="3bd7e-327">Přidejte delegáta pro <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> Přidání [modelových konvencí](xref:mvc/controllers/application-model#conventions) , které se vztahují na Razor stránky.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-327">Add a delegate for <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> to add [model conventions](xref:mvc/controllers/application-model#conventions) that apply to Razor Pages.</span></span>

### <a name="add-a-route-model-convention-to-all-pages"></a><span data-ttu-id="3bd7e-328">Přidat konvenci modelu směrování na všechny stránky</span><span class="sxs-lookup"><span data-stu-id="3bd7e-328">Add a route model convention to all pages</span></span>

<span data-ttu-id="3bd7e-329">Slouží <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> k vytvoření a přidání <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> kolekce <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instancí, které se použijí během vytváření modelu směrování stránky.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-329">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page route model construction.</span></span>

<span data-ttu-id="3bd7e-330">Ukázková aplikace přidá `{globalTemplate?}` šablonu směrování na všechny stránky v aplikaci:</span><span class="sxs-lookup"><span data-stu-id="3bd7e-330">The sample app adds a `{globalTemplate?}` route template to all of the pages in the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

<span data-ttu-id="3bd7e-331"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*>Vlastnost pro <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> je nastavena na `1` .</span><span class="sxs-lookup"><span data-stu-id="3bd7e-331">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `1`.</span></span> <span data-ttu-id="3bd7e-332">Tím se zajistí následující chování při shodě trasy v ukázkové aplikaci:</span><span class="sxs-lookup"><span data-stu-id="3bd7e-332">This ensures the following route matching behavior in the sample app:</span></span>

* <span data-ttu-id="3bd7e-333">Šablona směrování pro `TheContactPage/{text?}` je přidána později v tématu.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-333">A route template for `TheContactPage/{text?}` is added later in the topic.</span></span> <span data-ttu-id="3bd7e-334">Trasa stránky kontaktu má výchozí pořadí `null` ( `Order = 0` ), takže odpovídá před `{globalTemplate?}` šablonou směrování.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-334">The Contact Page route has a default order of `null` (`Order = 0`), so it matches before the `{globalTemplate?}` route template.</span></span>
* <span data-ttu-id="3bd7e-335">`{aboutTemplate?}`Šablona směrování se přidá později v tématu.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-335">An `{aboutTemplate?}` route template is added later in the topic.</span></span> <span data-ttu-id="3bd7e-336">`{aboutTemplate?}`Šabloně je přidělena `Order` `2` .</span><span class="sxs-lookup"><span data-stu-id="3bd7e-336">The `{aboutTemplate?}` template is given an `Order` of `2`.</span></span> <span data-ttu-id="3bd7e-337">Když se na stránce o žádosti vyžádá `/About/RouteDataValue` , "RouteDataValue" se načte do `RouteData.Values["globalTemplate"]` ( `Order = 1` ) a `RouteData.Values["aboutTemplate"]` ne ( `Order = 2` ) kvůli nastavení `Order` Vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-337">When the About page is requested at `/About/RouteDataValue`, "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["aboutTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>
* <span data-ttu-id="3bd7e-338">`{otherPagesTemplate?}`Šablona směrování se přidá později v tématu.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-338">An `{otherPagesTemplate?}` route template is added later in the topic.</span></span> <span data-ttu-id="3bd7e-339">`{otherPagesTemplate?}`Šabloně je přidělena `Order` `2` .</span><span class="sxs-lookup"><span data-stu-id="3bd7e-339">The `{otherPagesTemplate?}` template is given an `Order` of `2`.</span></span> <span data-ttu-id="3bd7e-340">Pokud je pro libovolnou stránku ve složce *Pages/OtherPages* požadováno parametr trasy (například `/OtherPages/Page1/RouteDataValue` ), je hodnota "RouteDataValue" načtena do `RouteData.Values["globalTemplate"]` ( `Order = 1` ) a nikoli `RouteData.Values["otherPagesTemplate"]` ( `Order = 2` ) z důvodu nastavení `Order` Vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-340">When any page in the *Pages/OtherPages* folder is requested with a route parameter (for example, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="3bd7e-341">Pokud je to možné, nenastavte `Order` , které výsledky mají `Order = 0` .</span><span class="sxs-lookup"><span data-stu-id="3bd7e-341">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="3bd7e-342">Pro výběr správné trasy se spoléhá na směrování.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-342">Rely on routing to select the correct route.</span></span>

Razor<span data-ttu-id="3bd7e-343">Možnosti stránek, jako je například přidání <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> , jsou přidány při přidání MVC do kolekce služby v nástroji `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="3bd7e-343"> Pages options, such as adding <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>, are added when MVC is added to the service collection in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="3bd7e-344">Příklad najdete v [ukázkové aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/).</span><span class="sxs-lookup"><span data-stu-id="3bd7e-344">For an example, see the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/).</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="3bd7e-345">Požádejte o stránku ukázek o stránce `localhost:5000/About/GlobalRouteValue` a Prohlédněte si výsledek:</span><span class="sxs-lookup"><span data-stu-id="3bd7e-345">Request the sample's About page at `localhost:5000/About/GlobalRouteValue` and inspect the result:</span></span>

![Stránka o aplikaci je požadována s segmentem směrování GlobalRouteValue.](razor-pages-conventions/_static/about-page-global-template.png)

### <a name="add-an-app-model-convention-to-all-pages"></a><span data-ttu-id="3bd7e-348">Přidání konvence modelu aplikace na všechny stránky</span><span class="sxs-lookup"><span data-stu-id="3bd7e-348">Add an app model convention to all pages</span></span>

<span data-ttu-id="3bd7e-349">Použijte <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> k vytvoření a přidání <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> do kolekce <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instancí, které se použijí během vytváření modelu aplikace stránky.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-349">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page app model construction.</span></span>

<span data-ttu-id="3bd7e-350">Chcete-li předvést tuto a další konvenci později v tématu, ukázková aplikace obsahuje `AddHeaderAttribute` třídu.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-350">To demonstrate this and other conventions later in the topic, the sample app includes an `AddHeaderAttribute` class.</span></span> <span data-ttu-id="3bd7e-351">Konstruktor třídy přijímá `name` řetězec a `values` pole řetězců.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-351">The class constructor accepts a `name` string and a `values` string array.</span></span> <span data-ttu-id="3bd7e-352">Tyto hodnoty jsou použity v `OnResultExecuting` metodě pro nastavení hlavičky odpovědi.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-352">These values are used in its `OnResultExecuting` method to set a response header.</span></span> <span data-ttu-id="3bd7e-353">Úplná třída je uvedena v části [konvence akcí modelu stránky](#page-model-action-conventions) dále v tématu.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-353">The full class is shown in the [Page model action conventions](#page-model-action-conventions) section later in the topic.</span></span>

<span data-ttu-id="3bd7e-354">Ukázková aplikace používá `AddHeaderAttribute` třídu k přidání záhlaví, na `GlobalHeader` všechny stránky v aplikaci:</span><span class="sxs-lookup"><span data-stu-id="3bd7e-354">The sample app uses the `AddHeaderAttribute` class to add a header, `GlobalHeader`, to all of the pages in the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

<span data-ttu-id="3bd7e-355">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="3bd7e-355">*Startup.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

<span data-ttu-id="3bd7e-356">Požádejte o stránku ukázek o stránce `localhost:5000/About` a Prohlédněte si záhlaví a podívejte se na výsledek:</span><span class="sxs-lookup"><span data-stu-id="3bd7e-356">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![Hlavičky odpovědi stránky o produktu ukazují, že byl přidán GlobalHeader.](razor-pages-conventions/_static/about-page-global-header.png)

### <a name="add-a-handler-model-convention-to-all-pages"></a><span data-ttu-id="3bd7e-358">Přidat konvenci modelu obslužné rutiny na všechny stránky</span><span class="sxs-lookup"><span data-stu-id="3bd7e-358">Add a handler model convention to all pages</span></span>

<span data-ttu-id="3bd7e-359">Použijte <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> k vytvoření a přidání <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> do kolekce <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instancí, které jsou použity během vytváření modelu obslužné rutiny stránky.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-359">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page handler model construction.</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalPageHandlerModelConvention.cs?name=snippet1)]

<span data-ttu-id="3bd7e-360">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="3bd7e-360">*Startup.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet10)]

## <a name="page-route-action-conventions"></a><span data-ttu-id="3bd7e-361">Konvence akcí při směrování stránky</span><span class="sxs-lookup"><span data-stu-id="3bd7e-361">Page route action conventions</span></span>

<span data-ttu-id="3bd7e-362">Výchozí zprostředkovatel modelu směrování, který je odvozen od <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> konvencí vyvolání, které jsou navrženy tak, aby poskytovaly body rozšiřitelnosti pro konfiguraci tras stránky.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-362">The default route model provider that derives from <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> invokes conventions which are designed to provide extensibility points for configuring page routes.</span></span>

### <a name="folder-route-model-convention"></a><span data-ttu-id="3bd7e-363">Konvence modelu směrování složky</span><span class="sxs-lookup"><span data-stu-id="3bd7e-363">Folder route model convention</span></span>

<span data-ttu-id="3bd7e-364">Slouží <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> k vytvoření a přidání <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> akce, která vyvolá akci <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> pro všechny stránky v zadané složce.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-364">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> for all of the pages under the specified folder.</span></span>

<span data-ttu-id="3bd7e-365">Ukázková aplikace používá <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> k přidání `{otherPagesTemplate?}` šablony směrování na stránky ve složce *OtherPages* :</span><span class="sxs-lookup"><span data-stu-id="3bd7e-365">The sample app uses <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> to add an `{otherPagesTemplate?}` route template to the pages in the *OtherPages* folder:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

<span data-ttu-id="3bd7e-366"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*>Vlastnost pro <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> je nastavena na `2` .</span><span class="sxs-lookup"><span data-stu-id="3bd7e-366">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `2`.</span></span> <span data-ttu-id="3bd7e-367">Tím se zajistí, že šablona pro `{globalTemplate?}` (nastavená výše v tématu na `1` ) má prioritu první pozice hodnoty dat trasy při zadání jedné hodnoty trasy.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-367">This ensures that the template for `{globalTemplate?}` (set earlier in the topic to `1`) is given priority for the first route data value position when a single route value is provided.</span></span> <span data-ttu-id="3bd7e-368">Pokud se stránka ve složce *Pages/OtherPages* požaduje s hodnotou parametru trasy (například `/OtherPages/Page1/RouteDataValue` ), "RouteDataValue" se načte do `RouteData.Values["globalTemplate"]` ( `Order = 1` ) a ne `RouteData.Values["otherPagesTemplate"]` ( `Order = 2` ) kvůli nastavení `Order` Vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-368">If a page in the *Pages/OtherPages* folder is requested with a route parameter value (for example, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="3bd7e-369">Pokud je to možné, nenastavte `Order` , které výsledky mají `Order = 0` .</span><span class="sxs-lookup"><span data-stu-id="3bd7e-369">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="3bd7e-370">Pro výběr správné trasy se spoléhá na směrování.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-370">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="3bd7e-371">Vyžádejte si Page1 stránku ukázky `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` a Prohlédněte si výsledek:</span><span class="sxs-lookup"><span data-stu-id="3bd7e-371">Request the sample's Page1 page at `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` and inspect the result:</span></span>

![Page1 ve složce OtherPages se vyžádá s segmentem směrování GlobalRouteValue a OtherPagesRouteValue.](razor-pages-conventions/_static/otherpages-page1-global-and-otherpages-templates.png)

### <a name="page-route-model-convention"></a><span data-ttu-id="3bd7e-374">Konvence modelu směrování stránky</span><span class="sxs-lookup"><span data-stu-id="3bd7e-374">Page route model convention</span></span>

<span data-ttu-id="3bd7e-375">Slouží <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> k vytvoření a přidání <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> akce, která vyvolá akci na <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> stránce pro stránku se zadaným názvem.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-375">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> for the page with the specified name.</span></span>

<span data-ttu-id="3bd7e-376">Ukázková aplikace používá `AddPageRouteModelConvention` k přidání `{aboutTemplate?}` šablony směrování na stránku o aplikaci:</span><span class="sxs-lookup"><span data-stu-id="3bd7e-376">The sample app uses `AddPageRouteModelConvention` to add an `{aboutTemplate?}` route template to the About page:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

<span data-ttu-id="3bd7e-377"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*>Vlastnost pro <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> je nastavena na `2` .</span><span class="sxs-lookup"><span data-stu-id="3bd7e-377">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `2`.</span></span> <span data-ttu-id="3bd7e-378">Tím se zajistí, že šablona pro `{globalTemplate?}` (nastavená výše v tématu na `1` ) má prioritu první pozice hodnoty dat trasy při zadání jedné hodnoty trasy.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-378">This ensures that the template for `{globalTemplate?}` (set earlier in the topic to `1`) is given priority for the first route data value position when a single route value is provided.</span></span> <span data-ttu-id="3bd7e-379">Pokud se stránka o žádosti vyžaduje s hodnotou parametru Route na `/About/RouteDataValue` , je "RouteDataValue" načten do `RouteData.Values["globalTemplate"]` ( `Order = 1` ) a nikoli `RouteData.Values["aboutTemplate"]` ( `Order = 2` ) z důvodu nastavení `Order` Vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-379">If the About page is requested with a route parameter value at `/About/RouteDataValue`, "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["aboutTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="3bd7e-380">Pokud je to možné, nenastavte `Order` , které výsledky mají `Order = 0` .</span><span class="sxs-lookup"><span data-stu-id="3bd7e-380">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="3bd7e-381">Pro výběr správné trasy se spoléhá na směrování.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-381">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="3bd7e-382">Požádejte o stránku ukázek o stránce `localhost:5000/About/GlobalRouteValue/AboutRouteValue` a Prohlédněte si výsledek:</span><span class="sxs-lookup"><span data-stu-id="3bd7e-382">Request the sample's About page at `localhost:5000/About/GlobalRouteValue/AboutRouteValue` and inspect the result:</span></span>

![U segmentů směrování pro GlobalRouteValue a AboutRouteValue se žádá o stránku.](razor-pages-conventions/_static/about-page-global-and-about-templates.png)

## <a name="use-a-parameter-transformer-to-customize-page-routes"></a><span data-ttu-id="3bd7e-385">Použití transformátoru parametrů k přizpůsobení cest stránky</span><span class="sxs-lookup"><span data-stu-id="3bd7e-385">Use a parameter transformer to customize page routes</span></span>

<span data-ttu-id="3bd7e-386">Trasy stránky generované ASP.NET Core lze přizpůsobit pomocí transformátoru parametrů.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-386">Page routes generated by ASP.NET Core can be customized using a parameter transformer.</span></span> <span data-ttu-id="3bd7e-387">Parametr Transformer implementuje `IOutboundParameterTransformer` a transformuje hodnotu parametrů.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-387">A parameter transformer implements `IOutboundParameterTransformer` and transforms the value of parameters.</span></span> <span data-ttu-id="3bd7e-388">Například vlastní `SlugifyParameterTransformer` parametr Transformer změní `SubscriptionManagement` hodnotu Route na `subscription-management` .</span><span class="sxs-lookup"><span data-stu-id="3bd7e-388">For example, a custom `SlugifyParameterTransformer` parameter transformer changes the `SubscriptionManagement` route value to `subscription-management`.</span></span>

<span data-ttu-id="3bd7e-389">`PageRouteTransformerConvention`Konvence modelu směrování stránky aplikuje transformátor parametrů na složku a název souboru segmentů automaticky generovaných tras stránky v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-389">The `PageRouteTransformerConvention` page route model convention applies a parameter transformer to the folder and file name segments of automatically generated page routes in an app.</span></span> <span data-ttu-id="3bd7e-390">Například Razor soubor stránek na */Pages/SubscriptionManagement/ViewAll.cshtml* by měl svou trasu přepsanou z `/SubscriptionManagement/ViewAll` do `/subscription-management/view-all` .</span><span class="sxs-lookup"><span data-stu-id="3bd7e-390">For example, the Razor Pages file at */Pages/SubscriptionManagement/ViewAll.cshtml* would have its route rewritten from `/SubscriptionManagement/ViewAll` to `/subscription-management/view-all`.</span></span>

<span data-ttu-id="3bd7e-391">`PageRouteTransformerConvention`transformuje automaticky generované segmenty stránky trasy, které pocházejí ze Razor složky a názvu souboru stránky.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-391">`PageRouteTransformerConvention` only transforms the automatically generated segments of a page route that come from the Razor Pages folder and file name.</span></span> <span data-ttu-id="3bd7e-392">Netransformuje segmenty směrování přidané s `@page` direktivou.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-392">It doesn't transform route segments added with the `@page` directive.</span></span> <span data-ttu-id="3bd7e-393">Konvence také netransformuje trasy, které přidal <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> .</span><span class="sxs-lookup"><span data-stu-id="3bd7e-393">The convention also doesn't transform routes added by <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>.</span></span>

<span data-ttu-id="3bd7e-394">`PageRouteTransformerConvention`Je zaregistrován jako možnost v `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="3bd7e-394">The `PageRouteTransformerConvention` is registered as an option in `Startup.ConfigureServices`:</span></span>

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

## <a name="configure-a-page-route"></a><span data-ttu-id="3bd7e-395">Konfigurace trasy stránky</span><span class="sxs-lookup"><span data-stu-id="3bd7e-395">Configure a page route</span></span>

<span data-ttu-id="3bd7e-396">Slouží <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> ke konfiguraci směrování na stránku na zadané cestě stránky.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-396">Use <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> to configure a route to a page at the specified page path.</span></span> <span data-ttu-id="3bd7e-397">Vygenerované odkazy na stránku používají zadanou trasu.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-397">Generated links to the page use your specified route.</span></span> <span data-ttu-id="3bd7e-398">`AddPageRoute`slouží `AddPageRouteModelConvention` k vytvoření trasy.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-398">`AddPageRoute` uses `AddPageRouteModelConvention` to establish the route.</span></span>

<span data-ttu-id="3bd7e-399">Ukázková aplikace vytvoří trasu `/TheContactPage` pro *kontakt. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="3bd7e-399">The sample app creates a route to `/TheContactPage` for *Contact.cshtml*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet5)]

<span data-ttu-id="3bd7e-400">Stránku kontaktů lze také kontaktovat `/Contact` prostřednictvím výchozí trasy.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-400">The Contact page can also be reached at `/Contact` via its default route.</span></span>

<span data-ttu-id="3bd7e-401">Vlastní trasa ukázkové aplikace na stránku kontaktů umožňuje volitelný `text` segment směrování ( `{text?}` ).</span><span class="sxs-lookup"><span data-stu-id="3bd7e-401">The sample app's custom route to the Contact page allows for an optional `text` route segment (`{text?}`).</span></span> <span data-ttu-id="3bd7e-402">Tato stránka obsahuje také volitelný segment v rámci své `@page` směrnice pro případ, že návštěvník přistupuje ke stránce v `/Contact` cestě:</span><span class="sxs-lookup"><span data-stu-id="3bd7e-402">The page also includes this optional segment in its `@page` directive in case the visitor accesses the page at its `/Contact` route:</span></span>

[!code-cshtml[](razor-pages-conventions/samples/2.x/SampleApp/Pages/Contact.cshtml?highlight=1)]

<span data-ttu-id="3bd7e-403">Všimněte si, že adresa URL vygenerovaná pro odkaz **kontaktu** na vykreslené stránce odráží aktualizovanou trasu:</span><span class="sxs-lookup"><span data-stu-id="3bd7e-403">Note that the URL generated for the **Contact** link in the rendered page reflects the updated route:</span></span>

![Odkaz na ukázkový kontakt aplikace na navigačním panelu](razor-pages-conventions/_static/contact-link.png)

![Kontrola odkazu na kontakt ve vykresleném kódu HTML znamená, že odkaz href je nastaven na hodnotu "/TheContactPage".](razor-pages-conventions/_static/contact-link-source.png)

<span data-ttu-id="3bd7e-406">Navštivte stránku kontaktů buď na své běžné trase, `/Contact` nebo na vlastní trasu `/TheContactPage` .</span><span class="sxs-lookup"><span data-stu-id="3bd7e-406">Visit the Contact page at either its ordinary route, `/Contact`, or the custom route, `/TheContactPage`.</span></span> <span data-ttu-id="3bd7e-407">Pokud zadáte další `text` segment směrování, stránka zobrazuje segment, který zakódovaný ve formátu HTML, který zadáte:</span><span class="sxs-lookup"><span data-stu-id="3bd7e-407">If you supply an additional `text` route segment, the page shows the HTML-encoded segment that you provide:</span></span>

![Prohlížeč Edge – příklad poskytnutí volitelného segmentu "text" trasy "TextValue" v adrese URL](razor-pages-conventions/_static/route-segment-with-custom-route.png)

## <a name="page-model-action-conventions"></a><span data-ttu-id="3bd7e-410">Konvence akcí modelu stránky</span><span class="sxs-lookup"><span data-stu-id="3bd7e-410">Page model action conventions</span></span>

<span data-ttu-id="3bd7e-411">Výchozí zprostředkovatel stránky, který implementuje implementují <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> konvence, které jsou navrženy tak, aby poskytovaly body rozšiřitelnosti pro konfiguraci modelů stránek.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-411">The default page model provider that implements <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> invokes conventions which are designed to provide extensibility points for configuring page models.</span></span> <span data-ttu-id="3bd7e-412">Tyto konvence jsou užitečné při sestavování a úpravách scénářů zjišťování a zpracování stránky.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-412">These conventions are useful when building and modifying page discovery and processing scenarios.</span></span>

<span data-ttu-id="3bd7e-413">V příkladech v této části používá ukázková aplikace třídu, `AddHeaderAttribute` která je <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute> , která používá hlavičku odpovědi:</span><span class="sxs-lookup"><span data-stu-id="3bd7e-413">For the examples in this section, the sample app uses an `AddHeaderAttribute` class, which is a <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>, that applies a response header:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

<span data-ttu-id="3bd7e-414">Pomocí konvence Ukázka ukazuje, jak použít atribut na všechny stránky ve složce a na jednu stránku.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-414">Using conventions, the sample demonstrates how to apply the attribute to all of the pages in a folder and to a single page.</span></span>

<span data-ttu-id="3bd7e-415">**Konvence modelu aplikace složky**</span><span class="sxs-lookup"><span data-stu-id="3bd7e-415">**Folder app model convention**</span></span>

<span data-ttu-id="3bd7e-416">Slouží <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> k vytvoření a přidání <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> akce, která vyvolá akci u <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> instancí pro všechny stránky v zadané složce.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-416">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> that invokes an action on <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> instances for all pages under the specified folder.</span></span>

<span data-ttu-id="3bd7e-417">Ukázka demonstruje použití přidáním `AddFolderApplicationModelConvention` záhlaví, na `OtherPagesHeader` stránkách ve složce *OtherPages* této aplikace:</span><span class="sxs-lookup"><span data-stu-id="3bd7e-417">The sample demonstrates the use of `AddFolderApplicationModelConvention` by adding a header, `OtherPagesHeader`, to the pages inside the *OtherPages* folder of the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet6)]

<span data-ttu-id="3bd7e-418">Vyžádejte si ukázkovou stránku Page1 na stránce a prohlédněte `localhost:5000/OtherPages/Page1` si záhlaví a podívejte se na výsledek:</span><span class="sxs-lookup"><span data-stu-id="3bd7e-418">Request the sample's Page1 page at `localhost:5000/OtherPages/Page1` and inspect the headers to view the result:</span></span>

![Hlavičky odpovědi stránky OtherPages/Page1 ukazují, že byla přidána OtherPagesHeader.](razor-pages-conventions/_static/page1-otherpages-header.png)

<span data-ttu-id="3bd7e-420">**Konvence modelu aplikace stránky**</span><span class="sxs-lookup"><span data-stu-id="3bd7e-420">**Page app model convention**</span></span>

<span data-ttu-id="3bd7e-421">Slouží <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> k vytvoření a přidání <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> akce, která vyvolá akci na <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> stránce pro stránku se zadaným názvem.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-421">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> for the page with the specified name.</span></span>

<span data-ttu-id="3bd7e-422">Ukázka demonstruje použití přidáním `AddPageApplicationModelConvention` záhlaví, na `AboutHeader` stránce o produktu:</span><span class="sxs-lookup"><span data-stu-id="3bd7e-422">The sample demonstrates the use of `AddPageApplicationModelConvention` by adding a header, `AboutHeader`, to the About page:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet7)]

<span data-ttu-id="3bd7e-423">Požádejte o stránku ukázek o stránce `localhost:5000/About` a Prohlédněte si záhlaví a podívejte se na výsledek:</span><span class="sxs-lookup"><span data-stu-id="3bd7e-423">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![Hlavičky odpovědi stránky o produktu ukazují, že byl přidán AboutHeader.](razor-pages-conventions/_static/about-page-about-header.png)

<span data-ttu-id="3bd7e-425">**Konfigurace filtru**</span><span class="sxs-lookup"><span data-stu-id="3bd7e-425">**Configure a filter**</span></span>

<span data-ttu-id="3bd7e-426"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*>nakonfiguruje zadaný filtr na použití.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-426"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configures the specified filter to apply.</span></span> <span data-ttu-id="3bd7e-427">Můžete implementovat třídu filtru, ale ukázková aplikace ukazuje, jak implementovat filtr ve výrazu lambda, který je implementován na pozadí jako objekt pro vytváření, který vrací filtr:</span><span class="sxs-lookup"><span data-stu-id="3bd7e-427">You can implement a filter class, but the sample app shows how to implement a filter in a lambda expression, which is implemented behind-the-scenes as a factory that returns a filter:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet8)]

<span data-ttu-id="3bd7e-428">Model webové aplikace se používá ke kontrole relativní cesty pro segmenty, které vedou ke stránce Page2 ve složce *OtherPages* .</span><span class="sxs-lookup"><span data-stu-id="3bd7e-428">The page app model is used to check the relative path for segments that lead to the Page2 page in the *OtherPages* folder.</span></span> <span data-ttu-id="3bd7e-429">Pokud podmínka projde, přidá se hlavička.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-429">If the condition passes, a header is added.</span></span> <span data-ttu-id="3bd7e-430">V takovém případě se `EmptyFilter` použije.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-430">If not, the `EmptyFilter` is applied.</span></span>

<span data-ttu-id="3bd7e-431">`EmptyFilter`je [Filtr akcí](xref:mvc/controllers/filters#action-filters).</span><span class="sxs-lookup"><span data-stu-id="3bd7e-431">`EmptyFilter` is an [Action filter](xref:mvc/controllers/filters#action-filters).</span></span> <span data-ttu-id="3bd7e-432">Vzhledem k tomu, že filtry akcí jsou ignorovány Razor stránkami, `EmptyFilter` nemá žádný vliv na zamýšlenou cestu, pokud cesta neobsahuje `OtherPages/Page2` .</span><span class="sxs-lookup"><span data-stu-id="3bd7e-432">Since Action filters are ignored by Razor Pages, the `EmptyFilter` has no effect as intended if the path doesn't contain `OtherPages/Page2`.</span></span>

<span data-ttu-id="3bd7e-433">Vyžádejte si ukázkovou stránku Page2 na stránce a prohlédněte `localhost:5000/OtherPages/Page2` si záhlaví a podívejte se na výsledek:</span><span class="sxs-lookup"><span data-stu-id="3bd7e-433">Request the sample's Page2 page at `localhost:5000/OtherPages/Page2` and inspect the headers to view the result:</span></span>

![OtherPagesPage2Header se přidá do odpovědi pro Page2.](razor-pages-conventions/_static/page2-filter-header.png)

<span data-ttu-id="3bd7e-435">**Konfigurace objektu pro vytváření filtrů**</span><span class="sxs-lookup"><span data-stu-id="3bd7e-435">**Configure a filter factory**</span></span>

<span data-ttu-id="3bd7e-436"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*>nakonfiguruje určený objekt pro vytváření na použití [filtrů](xref:mvc/controllers/filters) na všechny Razor stránky.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-436"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configures the specified factory to apply [filters](xref:mvc/controllers/filters) to all Razor Pages.</span></span>

<span data-ttu-id="3bd7e-437">Ukázková aplikace poskytuje příklad použití [objektu pro vytváření filtru](xref:mvc/controllers/filters#ifilterfactory) přidáním hlavičky, se `FilterFactoryHeader` dvěma hodnotami na stránkách aplikace:</span><span class="sxs-lookup"><span data-stu-id="3bd7e-437">The sample app provides an example of using a [filter factory](xref:mvc/controllers/filters#ifilterfactory) by adding a header, `FilterFactoryHeader`, with two values to the app's pages:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet9)]

<span data-ttu-id="3bd7e-438">*AddHeaderWithFactory.cs*:</span><span class="sxs-lookup"><span data-stu-id="3bd7e-438">*AddHeaderWithFactory.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Factories/AddHeaderWithFactory.cs?name=snippet1)]

<span data-ttu-id="3bd7e-439">Požádejte o stránku ukázek o stránce `localhost:5000/About` a Prohlédněte si záhlaví a podívejte se na výsledek:</span><span class="sxs-lookup"><span data-stu-id="3bd7e-439">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![Hlavičky odpovědi stránky o stránku zobrazují, že byly přidány dvě hlavičky FilterFactoryHeader.](razor-pages-conventions/_static/about-page-filter-factory-header.png)

## <a name="mvc-filters-and-the-page-filter-ipagefilter"></a><span data-ttu-id="3bd7e-441">Filtry MVC a filtr stránky (IPageFilter)</span><span class="sxs-lookup"><span data-stu-id="3bd7e-441">MVC Filters and the Page filter (IPageFilter)</span></span>

<span data-ttu-id="3bd7e-442">[Filtry akcí](xref:mvc/controllers/filters#action-filters) MVC jsou stránky ignorovány Razor , protože Razor stránky používají metody obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-442">MVC [Action filters](xref:mvc/controllers/filters#action-filters) are ignored by Razor Pages, since Razor Pages use handler methods.</span></span> <span data-ttu-id="3bd7e-443">Další typy filtrů MVC jsou k dispozici pro použití: [autorizace](xref:mvc/controllers/filters#authorization-filters), [výjimka](xref:mvc/controllers/filters#exception-filters), [prostředek](xref:mvc/controllers/filters#resource-filters)a [výsledek](xref:mvc/controllers/filters#result-filters).</span><span class="sxs-lookup"><span data-stu-id="3bd7e-443">Other types of MVC filters are available for you to use: [Authorization](xref:mvc/controllers/filters#authorization-filters), [Exception](xref:mvc/controllers/filters#exception-filters), [Resource](xref:mvc/controllers/filters#resource-filters), and [Result](xref:mvc/controllers/filters#result-filters).</span></span> <span data-ttu-id="3bd7e-444">Další informace najdete v tématu [filtry](xref:mvc/controllers/filters) .</span><span class="sxs-lookup"><span data-stu-id="3bd7e-444">For more information, see the [Filters](xref:mvc/controllers/filters) topic.</span></span>

<span data-ttu-id="3bd7e-445">Filtr stránky ( <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> ) je filtr, který se vztahuje na Razor stránky.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-445">The Page filter (<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>) is a filter that applies to Razor Pages.</span></span> <span data-ttu-id="3bd7e-446">Další informace najdete v tématu [metody filtrování pro Razor stránky](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="3bd7e-446">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3bd7e-447">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="3bd7e-447">Additional resources</span></span>

* <xref:security/authorization/razor-pages-authorization>
* <xref:mvc/controllers/areas#areas-with-razor-pages>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="3bd7e-448">Naučte se používat [konvence směrování stránky a poskytovatele modelů aplikací](xref:mvc/controllers/application-model#conventions) k řízení směrování, zjišťování a zpracování stránky v Razor aplikacích stránky.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-448">Learn how to use page [route and app model provider conventions](xref:mvc/controllers/application-model#conventions) to control page routing, discovery, and processing in Razor Pages apps.</span></span>

<span data-ttu-id="3bd7e-449">Pokud potřebujete nakonfigurovat vlastní trasy stránky pro jednotlivé stránky, nakonfigurujte směrování na stránky pomocí [AddPageRoute konvence](#configure-a-page-route) popsané dále v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-449">When you need to configure custom page routes for individual pages, configure routing to pages with the [AddPageRoute convention](#configure-a-page-route) described later in this topic.</span></span>

<span data-ttu-id="3bd7e-450">Chcete-li určit trasu stránky, přidat segmenty směrování nebo přidat parametry do trasy, použijte `@page` direktivu stránky.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-450">To specify a page route, add route segments, or add parameters to a route, use the page's `@page` directive.</span></span> <span data-ttu-id="3bd7e-451">Další informace najdete v tématu [vlastní trasy](xref:razor-pages/index#custom-routes).</span><span class="sxs-lookup"><span data-stu-id="3bd7e-451">For more information, see [Custom routes](xref:razor-pages/index#custom-routes).</span></span>

<span data-ttu-id="3bd7e-452">Existují vyhrazená slova, která nelze použít jako segmenty směrování nebo názvy parametrů.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-452">There are reserved words that can't be used as route segments or parameter names.</span></span> <span data-ttu-id="3bd7e-453">Další informace najdete v tématu [směrování: názvy rezervovaných směrování](xref:fundamentals/routing#reserved-routing-names).</span><span class="sxs-lookup"><span data-stu-id="3bd7e-453">For more information, see [Routing: Reserved routing names](xref:fundamentals/routing#reserved-routing-names).</span></span>

<span data-ttu-id="3bd7e-454">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="3bd7e-454">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

| <span data-ttu-id="3bd7e-455">Scénář</span><span class="sxs-lookup"><span data-stu-id="3bd7e-455">Scenario</span></span> | <span data-ttu-id="3bd7e-456">Ukázka znázorňuje...</span><span class="sxs-lookup"><span data-stu-id="3bd7e-456">The sample demonstrates ...</span></span> |
| -------- | --------------------------- |
| [<span data-ttu-id="3bd7e-457">Modelové konvence</span><span class="sxs-lookup"><span data-stu-id="3bd7e-457">Model conventions</span></span>](#model-conventions)<br><br><span data-ttu-id="3bd7e-458">Konvence. Add</span><span class="sxs-lookup"><span data-stu-id="3bd7e-458">Conventions.Add</span></span><ul><li><span data-ttu-id="3bd7e-459">IPageRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="3bd7e-459">IPageRouteModelConvention</span></span></li><li><span data-ttu-id="3bd7e-460">IPageApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="3bd7e-460">IPageApplicationModelConvention</span></span></li><li><span data-ttu-id="3bd7e-461">IPageHandlerModelConvention</span><span class="sxs-lookup"><span data-stu-id="3bd7e-461">IPageHandlerModelConvention</span></span></li></ul> | <span data-ttu-id="3bd7e-462">Přidejte šablonu a hlavičku směrování na stránky aplikace.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-462">Add a route template and header to an app's pages.</span></span> |
| [<span data-ttu-id="3bd7e-463">Konvence akcí při směrování stránky</span><span class="sxs-lookup"><span data-stu-id="3bd7e-463">Page route action conventions</span></span>](#page-route-action-conventions)<ul><li><span data-ttu-id="3bd7e-464">AddFolderRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="3bd7e-464">AddFolderRouteModelConvention</span></span></li><li><span data-ttu-id="3bd7e-465">AddPageRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="3bd7e-465">AddPageRouteModelConvention</span></span></li><li><span data-ttu-id="3bd7e-466">AddPageRoute</span><span class="sxs-lookup"><span data-stu-id="3bd7e-466">AddPageRoute</span></span></li></ul> | <span data-ttu-id="3bd7e-467">Přidejte šablonu směrování do stránek ve složce a na jednu stránku.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-467">Add a route template to pages in a folder and to a single page.</span></span> |
| [<span data-ttu-id="3bd7e-468">Konvence akcí modelu stránky</span><span class="sxs-lookup"><span data-stu-id="3bd7e-468">Page model action conventions</span></span>](#page-model-action-conventions)<ul><li><span data-ttu-id="3bd7e-469">AddFolderApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="3bd7e-469">AddFolderApplicationModelConvention</span></span></li><li><span data-ttu-id="3bd7e-470">AddPageApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="3bd7e-470">AddPageApplicationModelConvention</span></span></li><li><span data-ttu-id="3bd7e-471">ConfigureFilter (filtrovat třídu, lambda výraz nebo objekt pro vytváření filtru)</span><span class="sxs-lookup"><span data-stu-id="3bd7e-471">ConfigureFilter (filter class, lambda expression, or filter factory)</span></span></li></ul> | <span data-ttu-id="3bd7e-472">Umožňuje přidat záhlaví na stránky ve složce, přidat záhlaví na jednu stránku a nakonfigurovat [objekt pro vytváření filtru](xref:mvc/controllers/filters#ifilterfactory) tak, aby přidal hlavičku na stránky aplikace.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-472">Add a header to pages in a folder, add a header to a single page, and configure a [filter factory](xref:mvc/controllers/filters#ifilterfactory) to add a header to an app's pages.</span></span> |

Razor<span data-ttu-id="3bd7e-473">Konvence stránek jsou přidány a nakonfigurovány pomocí <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> metody rozšíření pro <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> kolekci služeb ve `Startup` třídě.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-473"> Pages conventions are added and configured using the <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> extension method to <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> on the service collection in the `Startup` class.</span></span> <span data-ttu-id="3bd7e-474">Následující příklady konvence jsou vysvětleny dále v tomto tématu:</span><span class="sxs-lookup"><span data-stu-id="3bd7e-474">The following convention examples are explained later in this topic:</span></span>

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

## <a name="route-order"></a><span data-ttu-id="3bd7e-475">Pořadí směrování</span><span class="sxs-lookup"><span data-stu-id="3bd7e-475">Route order</span></span>

<span data-ttu-id="3bd7e-476">Trasy určují <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> pro zpracování (odpovídání tras).</span><span class="sxs-lookup"><span data-stu-id="3bd7e-476">Routes specify an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> for processing (route matching).</span></span>

| <span data-ttu-id="3bd7e-477">Objednání</span><span class="sxs-lookup"><span data-stu-id="3bd7e-477">Order</span></span>            | <span data-ttu-id="3bd7e-478">Chování</span><span class="sxs-lookup"><span data-stu-id="3bd7e-478">Behavior</span></span> |
| :--------------: | -------- |
| <span data-ttu-id="3bd7e-479">-1</span><span class="sxs-lookup"><span data-stu-id="3bd7e-479">-1</span></span>               | <span data-ttu-id="3bd7e-480">Trasa je zpracována před zpracováním jiných tras.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-480">The route is processed before other routes are processed.</span></span> |
| <span data-ttu-id="3bd7e-481">0</span><span class="sxs-lookup"><span data-stu-id="3bd7e-481">0</span></span>                | <span data-ttu-id="3bd7e-482">Pořadí není zadáno (výchozí hodnota).</span><span class="sxs-lookup"><span data-stu-id="3bd7e-482">Order isn't specified (default value).</span></span> <span data-ttu-id="3bd7e-483">Nepřiřazuje se `Order` ( `Order = null` ) výchozí hodnota trasy `Order` na 0 (nula) pro zpracování.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-483">Not assigning `Order` (`Order = null`) defaults the route `Order` to 0 (zero) for processing.</span></span> |
| <span data-ttu-id="3bd7e-484">1, 2, &hellip; n</span><span class="sxs-lookup"><span data-stu-id="3bd7e-484">1, 2, &hellip; n</span></span> | <span data-ttu-id="3bd7e-485">Určuje pořadí zpracování směrování.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-485">Specifies the route processing order.</span></span> |

<span data-ttu-id="3bd7e-486">Zpracování směrování je zřízené podle konvence:</span><span class="sxs-lookup"><span data-stu-id="3bd7e-486">Route processing is established by convention:</span></span>

* <span data-ttu-id="3bd7e-487">Trasy jsou zpracovávány v sekvenčním pořadí (-1, 0, 1, 2, &hellip; n).</span><span class="sxs-lookup"><span data-stu-id="3bd7e-487">Routes are processed in sequential order (-1, 0, 1, 2, &hellip; n).</span></span>
* <span data-ttu-id="3bd7e-488">V případě, že trasy mají stejný směr `Order` , je nejdříve porovnána konkrétní trasa, za kterou následuje méně specifických tras.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-488">When routes have the same `Order`, the most specific route is matched first followed by less specific routes.</span></span>
* <span data-ttu-id="3bd7e-489">Když se trasy se stejným `Order` počtem parametrů shodují s adresou URL požadavku, trasy se zpracovávají v pořadí, v jakém jsou přidány do <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection> .</span><span class="sxs-lookup"><span data-stu-id="3bd7e-489">When routes with the same `Order` and the same number of parameters match a request URL, routes are processed in the order that they're added to the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>.</span></span>

<span data-ttu-id="3bd7e-490">Pokud je to možné, vyhněte se v závislosti na zavedeném pořadí zpracování směrování.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-490">If possible, avoid depending on an established route processing order.</span></span> <span data-ttu-id="3bd7e-491">Obecně směrování vybírá správnou trasu s odpovídající adresou URL.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-491">Generally, routing selects the correct route with URL matching.</span></span> <span data-ttu-id="3bd7e-492">Pokud musíte nastavit vlastnosti směrování `Order` na správně směrované požadavky, schéma směrování aplikace je pravděpodobně matoucí pro klienty a křehké na údržbu.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-492">If you must set route `Order` properties to route requests correctly, the app's routing scheme is probably confusing to clients and fragile to maintain.</span></span> <span data-ttu-id="3bd7e-493">Vyhledejte zjednodušené schéma směrování aplikace.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-493">Seek to simplify the app's routing scheme.</span></span> <span data-ttu-id="3bd7e-494">Ukázková aplikace vyžaduje explicitní pořadí zpracování tras, které předvádí několik scénářů směrování pomocí jedné aplikace.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-494">The sample app requires an explicit route processing order to demonstrate several routing scenarios using a single app.</span></span> <span data-ttu-id="3bd7e-495">Měli byste se však pokusit vyhnout postup nastavení trasy `Order` v produkčních aplikacích.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-495">However, you should attempt to avoid the practice of setting route `Order` in production apps.</span></span>

Razor<span data-ttu-id="3bd7e-496">Směrování stránek a směrování kontroléru MVC sdílí implementaci.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-496"> Pages routing and MVC controller routing share an implementation.</span></span> <span data-ttu-id="3bd7e-497">Informace o pořadí směrování v tématech MVC jsou k dispozici při [směrování na akce kontroleru: trasy atributů řazení](xref:mvc/controllers/routing#ordering-attribute-routes).</span><span class="sxs-lookup"><span data-stu-id="3bd7e-497">Information on route order in the MVC topics is available at [Routing to controller actions: Ordering attribute routes](xref:mvc/controllers/routing#ordering-attribute-routes).</span></span>

## <a name="model-conventions"></a><span data-ttu-id="3bd7e-498">Modelové konvence</span><span class="sxs-lookup"><span data-stu-id="3bd7e-498">Model conventions</span></span>

<span data-ttu-id="3bd7e-499">Přidejte delegáta pro <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> Přidání [modelových konvencí](xref:mvc/controllers/application-model#conventions) , které se vztahují na Razor stránky.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-499">Add a delegate for <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> to add [model conventions](xref:mvc/controllers/application-model#conventions) that apply to Razor Pages.</span></span>

### <a name="add-a-route-model-convention-to-all-pages"></a><span data-ttu-id="3bd7e-500">Přidat konvenci modelu směrování na všechny stránky</span><span class="sxs-lookup"><span data-stu-id="3bd7e-500">Add a route model convention to all pages</span></span>

<span data-ttu-id="3bd7e-501">Slouží <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> k vytvoření a přidání <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> kolekce <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instancí, které se použijí během vytváření modelu směrování stránky.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-501">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page route model construction.</span></span>

<span data-ttu-id="3bd7e-502">Ukázková aplikace přidá `{globalTemplate?}` šablonu směrování na všechny stránky v aplikaci:</span><span class="sxs-lookup"><span data-stu-id="3bd7e-502">The sample app adds a `{globalTemplate?}` route template to all of the pages in the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

<span data-ttu-id="3bd7e-503"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*>Vlastnost pro <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> je nastavena na `1` .</span><span class="sxs-lookup"><span data-stu-id="3bd7e-503">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `1`.</span></span> <span data-ttu-id="3bd7e-504">Tím se zajistí následující chování při shodě trasy v ukázkové aplikaci:</span><span class="sxs-lookup"><span data-stu-id="3bd7e-504">This ensures the following route matching behavior in the sample app:</span></span>

* <span data-ttu-id="3bd7e-505">Šablona směrování pro `TheContactPage/{text?}` je přidána později v tématu.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-505">A route template for `TheContactPage/{text?}` is added later in the topic.</span></span> <span data-ttu-id="3bd7e-506">Trasa stránky kontaktu má výchozí pořadí `null` ( `Order = 0` ), takže odpovídá před `{globalTemplate?}` šablonou směrování.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-506">The Contact Page route has a default order of `null` (`Order = 0`), so it matches before the `{globalTemplate?}` route template.</span></span>
* <span data-ttu-id="3bd7e-507">`{aboutTemplate?}`Šablona směrování se přidá později v tématu.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-507">An `{aboutTemplate?}` route template is added later in the topic.</span></span> <span data-ttu-id="3bd7e-508">`{aboutTemplate?}`Šabloně je přidělena `Order` `2` .</span><span class="sxs-lookup"><span data-stu-id="3bd7e-508">The `{aboutTemplate?}` template is given an `Order` of `2`.</span></span> <span data-ttu-id="3bd7e-509">Když se na stránce o žádosti vyžádá `/About/RouteDataValue` , "RouteDataValue" se načte do `RouteData.Values["globalTemplate"]` ( `Order = 1` ) a `RouteData.Values["aboutTemplate"]` ne ( `Order = 2` ) kvůli nastavení `Order` Vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-509">When the About page is requested at `/About/RouteDataValue`, "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["aboutTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>
* <span data-ttu-id="3bd7e-510">`{otherPagesTemplate?}`Šablona směrování se přidá později v tématu.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-510">An `{otherPagesTemplate?}` route template is added later in the topic.</span></span> <span data-ttu-id="3bd7e-511">`{otherPagesTemplate?}`Šabloně je přidělena `Order` `2` .</span><span class="sxs-lookup"><span data-stu-id="3bd7e-511">The `{otherPagesTemplate?}` template is given an `Order` of `2`.</span></span> <span data-ttu-id="3bd7e-512">Pokud je pro libovolnou stránku ve složce *Pages/OtherPages* požadováno parametr trasy (například `/OtherPages/Page1/RouteDataValue` ), je hodnota "RouteDataValue" načtena do `RouteData.Values["globalTemplate"]` ( `Order = 1` ) a nikoli `RouteData.Values["otherPagesTemplate"]` ( `Order = 2` ) z důvodu nastavení `Order` Vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-512">When any page in the *Pages/OtherPages* folder is requested with a route parameter (for example, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="3bd7e-513">Pokud je to možné, nenastavte `Order` , které výsledky mají `Order = 0` .</span><span class="sxs-lookup"><span data-stu-id="3bd7e-513">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="3bd7e-514">Pro výběr správné trasy se spoléhá na směrování.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-514">Rely on routing to select the correct route.</span></span>

Razor<span data-ttu-id="3bd7e-515">Možnosti stránek, jako je například přidání <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> , jsou přidány při přidání MVC do kolekce služby v nástroji `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="3bd7e-515"> Pages options, such as adding <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>, are added when MVC is added to the service collection in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="3bd7e-516">Příklad najdete v [ukázkové aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/).</span><span class="sxs-lookup"><span data-stu-id="3bd7e-516">For an example, see the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/).</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="3bd7e-517">Požádejte o stránku ukázek o stránce `localhost:5000/About/GlobalRouteValue` a Prohlédněte si výsledek:</span><span class="sxs-lookup"><span data-stu-id="3bd7e-517">Request the sample's About page at `localhost:5000/About/GlobalRouteValue` and inspect the result:</span></span>

![Stránka o aplikaci je požadována s segmentem směrování GlobalRouteValue.](razor-pages-conventions/_static/about-page-global-template.png)

### <a name="add-an-app-model-convention-to-all-pages"></a><span data-ttu-id="3bd7e-520">Přidání konvence modelu aplikace na všechny stránky</span><span class="sxs-lookup"><span data-stu-id="3bd7e-520">Add an app model convention to all pages</span></span>

<span data-ttu-id="3bd7e-521">Použijte <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> k vytvoření a přidání <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> do kolekce <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instancí, které se použijí během vytváření modelu aplikace stránky.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-521">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page app model construction.</span></span>

<span data-ttu-id="3bd7e-522">Chcete-li předvést tuto a další konvenci později v tématu, ukázková aplikace obsahuje `AddHeaderAttribute` třídu.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-522">To demonstrate this and other conventions later in the topic, the sample app includes an `AddHeaderAttribute` class.</span></span> <span data-ttu-id="3bd7e-523">Konstruktor třídy přijímá `name` řetězec a `values` pole řetězců.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-523">The class constructor accepts a `name` string and a `values` string array.</span></span> <span data-ttu-id="3bd7e-524">Tyto hodnoty jsou použity v `OnResultExecuting` metodě pro nastavení hlavičky odpovědi.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-524">These values are used in its `OnResultExecuting` method to set a response header.</span></span> <span data-ttu-id="3bd7e-525">Úplná třída je uvedena v části [konvence akcí modelu stránky](#page-model-action-conventions) dále v tématu.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-525">The full class is shown in the [Page model action conventions](#page-model-action-conventions) section later in the topic.</span></span>

<span data-ttu-id="3bd7e-526">Ukázková aplikace používá `AddHeaderAttribute` třídu k přidání záhlaví, na `GlobalHeader` všechny stránky v aplikaci:</span><span class="sxs-lookup"><span data-stu-id="3bd7e-526">The sample app uses the `AddHeaderAttribute` class to add a header, `GlobalHeader`, to all of the pages in the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

<span data-ttu-id="3bd7e-527">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="3bd7e-527">*Startup.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

<span data-ttu-id="3bd7e-528">Požádejte o stránku ukázek o stránce `localhost:5000/About` a Prohlédněte si záhlaví a podívejte se na výsledek:</span><span class="sxs-lookup"><span data-stu-id="3bd7e-528">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![Hlavičky odpovědi stránky o produktu ukazují, že byl přidán GlobalHeader.](razor-pages-conventions/_static/about-page-global-header.png)

### <a name="add-a-handler-model-convention-to-all-pages"></a><span data-ttu-id="3bd7e-530">Přidat konvenci modelu obslužné rutiny na všechny stránky</span><span class="sxs-lookup"><span data-stu-id="3bd7e-530">Add a handler model convention to all pages</span></span>

<span data-ttu-id="3bd7e-531">Použijte <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> k vytvoření a přidání <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> do kolekce <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instancí, které jsou použity během vytváření modelu obslužné rutiny stránky.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-531">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page handler model construction.</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalPageHandlerModelConvention.cs?name=snippet1)]

<span data-ttu-id="3bd7e-532">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="3bd7e-532">*Startup.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet10)]

## <a name="page-route-action-conventions"></a><span data-ttu-id="3bd7e-533">Konvence akcí při směrování stránky</span><span class="sxs-lookup"><span data-stu-id="3bd7e-533">Page route action conventions</span></span>

<span data-ttu-id="3bd7e-534">Výchozí zprostředkovatel modelu směrování, který je odvozen od <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> konvencí vyvolání, které jsou navrženy tak, aby poskytovaly body rozšiřitelnosti pro konfiguraci tras stránky.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-534">The default route model provider that derives from <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> invokes conventions which are designed to provide extensibility points for configuring page routes.</span></span>

### <a name="folder-route-model-convention"></a><span data-ttu-id="3bd7e-535">Konvence modelu směrování složky</span><span class="sxs-lookup"><span data-stu-id="3bd7e-535">Folder route model convention</span></span>

<span data-ttu-id="3bd7e-536">Slouží <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> k vytvoření a přidání <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> akce, která vyvolá akci <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> pro všechny stránky v zadané složce.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-536">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> for all of the pages under the specified folder.</span></span>

<span data-ttu-id="3bd7e-537">Ukázková aplikace používá <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> k přidání `{otherPagesTemplate?}` šablony směrování na stránky ve složce *OtherPages* :</span><span class="sxs-lookup"><span data-stu-id="3bd7e-537">The sample app uses <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> to add an `{otherPagesTemplate?}` route template to the pages in the *OtherPages* folder:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

<span data-ttu-id="3bd7e-538"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*>Vlastnost pro <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> je nastavena na `2` .</span><span class="sxs-lookup"><span data-stu-id="3bd7e-538">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `2`.</span></span> <span data-ttu-id="3bd7e-539">Tím se zajistí, že šablona pro `{globalTemplate?}` (nastavená výše v tématu na `1` ) má prioritu první pozice hodnoty dat trasy při zadání jedné hodnoty trasy.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-539">This ensures that the template for `{globalTemplate?}` (set earlier in the topic to `1`) is given priority for the first route data value position when a single route value is provided.</span></span> <span data-ttu-id="3bd7e-540">Pokud se stránka ve složce *Pages/OtherPages* požaduje s hodnotou parametru trasy (například `/OtherPages/Page1/RouteDataValue` ), "RouteDataValue" se načte do `RouteData.Values["globalTemplate"]` ( `Order = 1` ) a ne `RouteData.Values["otherPagesTemplate"]` ( `Order = 2` ) kvůli nastavení `Order` Vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-540">If a page in the *Pages/OtherPages* folder is requested with a route parameter value (for example, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="3bd7e-541">Pokud je to možné, nenastavte `Order` , které výsledky mají `Order = 0` .</span><span class="sxs-lookup"><span data-stu-id="3bd7e-541">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="3bd7e-542">Pro výběr správné trasy se spoléhá na směrování.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-542">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="3bd7e-543">Vyžádejte si Page1 stránku ukázky `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` a Prohlédněte si výsledek:</span><span class="sxs-lookup"><span data-stu-id="3bd7e-543">Request the sample's Page1 page at `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` and inspect the result:</span></span>

![Page1 ve složce OtherPages se vyžádá s segmentem směrování GlobalRouteValue a OtherPagesRouteValue.](razor-pages-conventions/_static/otherpages-page1-global-and-otherpages-templates.png)

### <a name="page-route-model-convention"></a><span data-ttu-id="3bd7e-546">Konvence modelu směrování stránky</span><span class="sxs-lookup"><span data-stu-id="3bd7e-546">Page route model convention</span></span>

<span data-ttu-id="3bd7e-547">Slouží <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> k vytvoření a přidání <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> akce, která vyvolá akci na <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> stránce pro stránku se zadaným názvem.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-547">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> for the page with the specified name.</span></span>

<span data-ttu-id="3bd7e-548">Ukázková aplikace používá `AddPageRouteModelConvention` k přidání `{aboutTemplate?}` šablony směrování na stránku o aplikaci:</span><span class="sxs-lookup"><span data-stu-id="3bd7e-548">The sample app uses `AddPageRouteModelConvention` to add an `{aboutTemplate?}` route template to the About page:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

<span data-ttu-id="3bd7e-549"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*>Vlastnost pro <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> je nastavena na `2` .</span><span class="sxs-lookup"><span data-stu-id="3bd7e-549">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `2`.</span></span> <span data-ttu-id="3bd7e-550">Tím se zajistí, že šablona pro `{globalTemplate?}` (nastavená výše v tématu na `1` ) má prioritu první pozice hodnoty dat trasy při zadání jedné hodnoty trasy.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-550">This ensures that the template for `{globalTemplate?}` (set earlier in the topic to `1`) is given priority for the first route data value position when a single route value is provided.</span></span> <span data-ttu-id="3bd7e-551">Pokud se stránka o žádosti vyžaduje s hodnotou parametru Route na `/About/RouteDataValue` , je "RouteDataValue" načten do `RouteData.Values["globalTemplate"]` ( `Order = 1` ) a nikoli `RouteData.Values["aboutTemplate"]` ( `Order = 2` ) z důvodu nastavení `Order` Vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-551">If the About page is requested with a route parameter value at `/About/RouteDataValue`, "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["aboutTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="3bd7e-552">Pokud je to možné, nenastavte `Order` , které výsledky mají `Order = 0` .</span><span class="sxs-lookup"><span data-stu-id="3bd7e-552">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="3bd7e-553">Pro výběr správné trasy se spoléhá na směrování.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-553">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="3bd7e-554">Požádejte o stránku ukázek o stránce `localhost:5000/About/GlobalRouteValue/AboutRouteValue` a Prohlédněte si výsledek:</span><span class="sxs-lookup"><span data-stu-id="3bd7e-554">Request the sample's About page at `localhost:5000/About/GlobalRouteValue/AboutRouteValue` and inspect the result:</span></span>

![U segmentů směrování pro GlobalRouteValue a AboutRouteValue se žádá o stránku.](razor-pages-conventions/_static/about-page-global-and-about-templates.png)

## <a name="configure-a-page-route"></a><span data-ttu-id="3bd7e-557">Konfigurace trasy stránky</span><span class="sxs-lookup"><span data-stu-id="3bd7e-557">Configure a page route</span></span>

<span data-ttu-id="3bd7e-558">Slouží <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> ke konfiguraci směrování na stránku na zadané cestě stránky.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-558">Use <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> to configure a route to a page at the specified page path.</span></span> <span data-ttu-id="3bd7e-559">Vygenerované odkazy na stránku používají zadanou trasu.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-559">Generated links to the page use your specified route.</span></span> <span data-ttu-id="3bd7e-560">`AddPageRoute`slouží `AddPageRouteModelConvention` k vytvoření trasy.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-560">`AddPageRoute` uses `AddPageRouteModelConvention` to establish the route.</span></span>

<span data-ttu-id="3bd7e-561">Ukázková aplikace vytvoří trasu `/TheContactPage` pro *kontakt. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="3bd7e-561">The sample app creates a route to `/TheContactPage` for *Contact.cshtml*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet5)]

<span data-ttu-id="3bd7e-562">Stránku kontaktů lze také kontaktovat `/Contact` prostřednictvím výchozí trasy.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-562">The Contact page can also be reached at `/Contact` via its default route.</span></span>

<span data-ttu-id="3bd7e-563">Vlastní trasa ukázkové aplikace na stránku kontaktů umožňuje volitelný `text` segment směrování ( `{text?}` ).</span><span class="sxs-lookup"><span data-stu-id="3bd7e-563">The sample app's custom route to the Contact page allows for an optional `text` route segment (`{text?}`).</span></span> <span data-ttu-id="3bd7e-564">Tato stránka obsahuje také volitelný segment v rámci své `@page` směrnice pro případ, že návštěvník přistupuje ke stránce v `/Contact` cestě:</span><span class="sxs-lookup"><span data-stu-id="3bd7e-564">The page also includes this optional segment in its `@page` directive in case the visitor accesses the page at its `/Contact` route:</span></span>

[!code-cshtml[](razor-pages-conventions/samples/2.x/SampleApp/Pages/Contact.cshtml?highlight=1)]

<span data-ttu-id="3bd7e-565">Všimněte si, že adresa URL vygenerovaná pro odkaz **kontaktu** na vykreslené stránce odráží aktualizovanou trasu:</span><span class="sxs-lookup"><span data-stu-id="3bd7e-565">Note that the URL generated for the **Contact** link in the rendered page reflects the updated route:</span></span>

![Odkaz na ukázkový kontakt aplikace na navigačním panelu](razor-pages-conventions/_static/contact-link.png)

![Kontrola odkazu na kontakt ve vykresleném kódu HTML znamená, že odkaz href je nastaven na hodnotu "/TheContactPage".](razor-pages-conventions/_static/contact-link-source.png)

<span data-ttu-id="3bd7e-568">Navštivte stránku kontaktů buď na své běžné trase, `/Contact` nebo na vlastní trasu `/TheContactPage` .</span><span class="sxs-lookup"><span data-stu-id="3bd7e-568">Visit the Contact page at either its ordinary route, `/Contact`, or the custom route, `/TheContactPage`.</span></span> <span data-ttu-id="3bd7e-569">Pokud zadáte další `text` segment směrování, stránka zobrazuje segment, který zakódovaný ve formátu HTML, který zadáte:</span><span class="sxs-lookup"><span data-stu-id="3bd7e-569">If you supply an additional `text` route segment, the page shows the HTML-encoded segment that you provide:</span></span>

![Prohlížeč Edge – příklad poskytnutí volitelného segmentu "text" trasy "TextValue" v adrese URL](razor-pages-conventions/_static/route-segment-with-custom-route.png)

## <a name="page-model-action-conventions"></a><span data-ttu-id="3bd7e-572">Konvence akcí modelu stránky</span><span class="sxs-lookup"><span data-stu-id="3bd7e-572">Page model action conventions</span></span>

<span data-ttu-id="3bd7e-573">Výchozí zprostředkovatel stránky, který implementuje implementují <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> konvence, které jsou navrženy tak, aby poskytovaly body rozšiřitelnosti pro konfiguraci modelů stránek.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-573">The default page model provider that implements <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> invokes conventions which are designed to provide extensibility points for configuring page models.</span></span> <span data-ttu-id="3bd7e-574">Tyto konvence jsou užitečné při sestavování a úpravách scénářů zjišťování a zpracování stránky.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-574">These conventions are useful when building and modifying page discovery and processing scenarios.</span></span>

<span data-ttu-id="3bd7e-575">V příkladech v této části používá ukázková aplikace třídu, `AddHeaderAttribute` která je <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute> , která používá hlavičku odpovědi:</span><span class="sxs-lookup"><span data-stu-id="3bd7e-575">For the examples in this section, the sample app uses an `AddHeaderAttribute` class, which is a <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>, that applies a response header:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

<span data-ttu-id="3bd7e-576">Pomocí konvence Ukázka ukazuje, jak použít atribut na všechny stránky ve složce a na jednu stránku.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-576">Using conventions, the sample demonstrates how to apply the attribute to all of the pages in a folder and to a single page.</span></span>

<span data-ttu-id="3bd7e-577">**Konvence modelu aplikace složky**</span><span class="sxs-lookup"><span data-stu-id="3bd7e-577">**Folder app model convention**</span></span>

<span data-ttu-id="3bd7e-578">Slouží <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> k vytvoření a přidání <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> akce, která vyvolá akci u <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> instancí pro všechny stránky v zadané složce.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-578">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> that invokes an action on <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> instances for all pages under the specified folder.</span></span>

<span data-ttu-id="3bd7e-579">Ukázka demonstruje použití přidáním `AddFolderApplicationModelConvention` záhlaví, na `OtherPagesHeader` stránkách ve složce *OtherPages* této aplikace:</span><span class="sxs-lookup"><span data-stu-id="3bd7e-579">The sample demonstrates the use of `AddFolderApplicationModelConvention` by adding a header, `OtherPagesHeader`, to the pages inside the *OtherPages* folder of the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet6)]

<span data-ttu-id="3bd7e-580">Vyžádejte si ukázkovou stránku Page1 na stránce a prohlédněte `localhost:5000/OtherPages/Page1` si záhlaví a podívejte se na výsledek:</span><span class="sxs-lookup"><span data-stu-id="3bd7e-580">Request the sample's Page1 page at `localhost:5000/OtherPages/Page1` and inspect the headers to view the result:</span></span>

![Hlavičky odpovědi stránky OtherPages/Page1 ukazují, že byla přidána OtherPagesHeader.](razor-pages-conventions/_static/page1-otherpages-header.png)

<span data-ttu-id="3bd7e-582">**Konvence modelu aplikace stránky**</span><span class="sxs-lookup"><span data-stu-id="3bd7e-582">**Page app model convention**</span></span>

<span data-ttu-id="3bd7e-583">Slouží <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> k vytvoření a přidání <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> akce, která vyvolá akci na <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> stránce pro stránku se zadaným názvem.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-583">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> for the page with the specified name.</span></span>

<span data-ttu-id="3bd7e-584">Ukázka demonstruje použití přidáním `AddPageApplicationModelConvention` záhlaví, na `AboutHeader` stránce o produktu:</span><span class="sxs-lookup"><span data-stu-id="3bd7e-584">The sample demonstrates the use of `AddPageApplicationModelConvention` by adding a header, `AboutHeader`, to the About page:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet7)]

<span data-ttu-id="3bd7e-585">Požádejte o stránku ukázek o stránce `localhost:5000/About` a Prohlédněte si záhlaví a podívejte se na výsledek:</span><span class="sxs-lookup"><span data-stu-id="3bd7e-585">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![Hlavičky odpovědi stránky o produktu ukazují, že byl přidán AboutHeader.](razor-pages-conventions/_static/about-page-about-header.png)

<span data-ttu-id="3bd7e-587">**Konfigurace filtru**</span><span class="sxs-lookup"><span data-stu-id="3bd7e-587">**Configure a filter**</span></span>

<span data-ttu-id="3bd7e-588"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*>nakonfiguruje zadaný filtr na použití.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-588"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configures the specified filter to apply.</span></span> <span data-ttu-id="3bd7e-589">Můžete implementovat třídu filtru, ale ukázková aplikace ukazuje, jak implementovat filtr ve výrazu lambda, který je implementován na pozadí jako objekt pro vytváření, který vrací filtr:</span><span class="sxs-lookup"><span data-stu-id="3bd7e-589">You can implement a filter class, but the sample app shows how to implement a filter in a lambda expression, which is implemented behind-the-scenes as a factory that returns a filter:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet8)]

<span data-ttu-id="3bd7e-590">Model webové aplikace se používá ke kontrole relativní cesty pro segmenty, které vedou ke stránce Page2 ve složce *OtherPages* .</span><span class="sxs-lookup"><span data-stu-id="3bd7e-590">The page app model is used to check the relative path for segments that lead to the Page2 page in the *OtherPages* folder.</span></span> <span data-ttu-id="3bd7e-591">Pokud podmínka projde, přidá se hlavička.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-591">If the condition passes, a header is added.</span></span> <span data-ttu-id="3bd7e-592">V takovém případě se `EmptyFilter` použije.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-592">If not, the `EmptyFilter` is applied.</span></span>

<span data-ttu-id="3bd7e-593">`EmptyFilter`je [Filtr akcí](xref:mvc/controllers/filters#action-filters).</span><span class="sxs-lookup"><span data-stu-id="3bd7e-593">`EmptyFilter` is an [Action filter](xref:mvc/controllers/filters#action-filters).</span></span> <span data-ttu-id="3bd7e-594">Vzhledem k tomu, že filtry akcí jsou ignorovány Razor stránkami, `EmptyFilter` nemá žádný vliv na zamýšlenou cestu, pokud cesta neobsahuje `OtherPages/Page2` .</span><span class="sxs-lookup"><span data-stu-id="3bd7e-594">Since Action filters are ignored by Razor Pages, the `EmptyFilter` has no effect as intended if the path doesn't contain `OtherPages/Page2`.</span></span>

<span data-ttu-id="3bd7e-595">Vyžádejte si ukázkovou stránku Page2 na stránce a prohlédněte `localhost:5000/OtherPages/Page2` si záhlaví a podívejte se na výsledek:</span><span class="sxs-lookup"><span data-stu-id="3bd7e-595">Request the sample's Page2 page at `localhost:5000/OtherPages/Page2` and inspect the headers to view the result:</span></span>

![OtherPagesPage2Header se přidá do odpovědi pro Page2.](razor-pages-conventions/_static/page2-filter-header.png)

<span data-ttu-id="3bd7e-597">**Konfigurace objektu pro vytváření filtrů**</span><span class="sxs-lookup"><span data-stu-id="3bd7e-597">**Configure a filter factory**</span></span>

<span data-ttu-id="3bd7e-598"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*>nakonfiguruje určený objekt pro vytváření na použití [filtrů](xref:mvc/controllers/filters) na všechny Razor stránky.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-598"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configures the specified factory to apply [filters](xref:mvc/controllers/filters) to all Razor Pages.</span></span>

<span data-ttu-id="3bd7e-599">Ukázková aplikace poskytuje příklad použití [objektu pro vytváření filtru](xref:mvc/controllers/filters#ifilterfactory) přidáním hlavičky, se `FilterFactoryHeader` dvěma hodnotami na stránkách aplikace:</span><span class="sxs-lookup"><span data-stu-id="3bd7e-599">The sample app provides an example of using a [filter factory](xref:mvc/controllers/filters#ifilterfactory) by adding a header, `FilterFactoryHeader`, with two values to the app's pages:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet9)]

<span data-ttu-id="3bd7e-600">*AddHeaderWithFactory.cs*:</span><span class="sxs-lookup"><span data-stu-id="3bd7e-600">*AddHeaderWithFactory.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Factories/AddHeaderWithFactory.cs?name=snippet1)]

<span data-ttu-id="3bd7e-601">Požádejte o stránku ukázek o stránce `localhost:5000/About` a Prohlédněte si záhlaví a podívejte se na výsledek:</span><span class="sxs-lookup"><span data-stu-id="3bd7e-601">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![Hlavičky odpovědi stránky o stránku zobrazují, že byly přidány dvě hlavičky FilterFactoryHeader.](razor-pages-conventions/_static/about-page-filter-factory-header.png)

## <a name="mvc-filters-and-the-page-filter-ipagefilter"></a><span data-ttu-id="3bd7e-603">Filtry MVC a filtr stránky (IPageFilter)</span><span class="sxs-lookup"><span data-stu-id="3bd7e-603">MVC Filters and the Page filter (IPageFilter)</span></span>

<span data-ttu-id="3bd7e-604">[Filtry akcí](xref:mvc/controllers/filters#action-filters) MVC jsou stránky ignorovány Razor , protože Razor stránky používají metody obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-604">MVC [Action filters](xref:mvc/controllers/filters#action-filters) are ignored by Razor Pages, since Razor Pages use handler methods.</span></span> <span data-ttu-id="3bd7e-605">Další typy filtrů MVC jsou k dispozici pro použití: [autorizace](xref:mvc/controllers/filters#authorization-filters), [výjimka](xref:mvc/controllers/filters#exception-filters), [prostředek](xref:mvc/controllers/filters#resource-filters)a [výsledek](xref:mvc/controllers/filters#result-filters).</span><span class="sxs-lookup"><span data-stu-id="3bd7e-605">Other types of MVC filters are available for you to use: [Authorization](xref:mvc/controllers/filters#authorization-filters), [Exception](xref:mvc/controllers/filters#exception-filters), [Resource](xref:mvc/controllers/filters#resource-filters), and [Result](xref:mvc/controllers/filters#result-filters).</span></span> <span data-ttu-id="3bd7e-606">Další informace najdete v tématu [filtry](xref:mvc/controllers/filters) .</span><span class="sxs-lookup"><span data-stu-id="3bd7e-606">For more information, see the [Filters](xref:mvc/controllers/filters) topic.</span></span>

<span data-ttu-id="3bd7e-607">Filtr stránky ( <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> ) je filtr, který se vztahuje na Razor stránky.</span><span class="sxs-lookup"><span data-stu-id="3bd7e-607">The Page filter (<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>) is a filter that applies to Razor Pages.</span></span> <span data-ttu-id="3bd7e-608">Další informace najdete v tématu [metody filtrování pro Razor stránky](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="3bd7e-608">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3bd7e-609">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="3bd7e-609">Additional resources</span></span>

* <xref:security/authorization/razor-pages-authorization>
* <xref:mvc/controllers/areas#areas-with-razor-pages>

::: moniker-end
