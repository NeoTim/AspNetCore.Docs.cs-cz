---
title: Metody filtrování pro Razor Pages v ASP.NET Core
author: Rick-Anderson
description: Zjistěte, jak vytvořit filtr metody pro stránky Razor v ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 04/05/2018
uid: razor-pages/filter
ms.openlocfilehash: 32613d75d966a698c6478234f3f5f9d5fc0628bc
ms.sourcegitcommit: 57792e5f594db1574742588017c708350958bdf0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2019
ms.locfileid: "58264788"
---
# <a name="filter-methods-for-razor-pages-in-aspnet-core"></a><span data-ttu-id="44f8a-103">Metody filtrování pro Razor Pages v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="44f8a-103">Filter methods for Razor Pages in ASP.NET Core</span></span>

<span data-ttu-id="44f8a-104">Podle [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="44f8a-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="44f8a-105">Stránka Razor filtruje [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) a [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) povolit stránky Razor pro spuštění kódu před a po spuštění rutiny stránky Razor.</span><span class="sxs-lookup"><span data-stu-id="44f8a-105">Razor Page filters [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) and [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) allow Razor Pages to run code before and after a Razor Page handler is run.</span></span> <span data-ttu-id="44f8a-106">Filtry stránek Razor jsou podobné [filtrů Akce ASP.NET Core MVC](xref:mvc/controllers/filters#action-filters)s výjimkou případů, nejde použít u metody obslužné rutiny jednotlivých stránek.</span><span class="sxs-lookup"><span data-stu-id="44f8a-106">Razor Page filters are similar to [ASP.NET Core MVC action filters](xref:mvc/controllers/filters#action-filters), except they can't be applied to individual page handler methods.</span></span> 

<span data-ttu-id="44f8a-107">Stránka Razor filtry:</span><span class="sxs-lookup"><span data-stu-id="44f8a-107">Razor Page filters:</span></span>

* <span data-ttu-id="44f8a-108">Spuštění kódu po výběru metody obslužné rutiny, ale před provedením vázání modelu.</span><span class="sxs-lookup"><span data-stu-id="44f8a-108">Run code after a handler method has been selected, but before model binding occurs.</span></span>
* <span data-ttu-id="44f8a-109">Spuštění kódu před spuštěním metody obslužné rutiny, po dokončení vazby modelu.</span><span class="sxs-lookup"><span data-stu-id="44f8a-109">Run code before the handler method executes, after model binding is complete.</span></span>
* <span data-ttu-id="44f8a-110">Spuštění kódu po provedení metody obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="44f8a-110">Run code after the handler method executes.</span></span>
* <span data-ttu-id="44f8a-111">Je možné implementovat na stránce nebo globálně.</span><span class="sxs-lookup"><span data-stu-id="44f8a-111">Can be implemented on a page or globally.</span></span>
* <span data-ttu-id="44f8a-112">Nejde použít u metody obslužné rutiny konkrétní stránky.</span><span class="sxs-lookup"><span data-stu-id="44f8a-112">Cannot be applied to specific page handler methods.</span></span>

<span data-ttu-id="44f8a-113">Kód můžete spustit před metodu obslužné rutiny provede pomocí konstruktoru stránky nebo middleware, ale pouze filtry stránky Razor nemají přístup k [HttpContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.httpcontext?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_HttpContext).</span><span class="sxs-lookup"><span data-stu-id="44f8a-113">Code can be run before a handler method executes using the page constructor or middleware, but only Razor Page filters have access to [HttpContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.httpcontext?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_HttpContext).</span></span> <span data-ttu-id="44f8a-114">Filtry nemají [FilterContext](/dotnet/api/microsoft.aspnetcore.mvc.filters.filtercontext?view=aspnetcore-2.0) odvozené parametr, který poskytuje přístup k `HttpContext`.</span><span class="sxs-lookup"><span data-stu-id="44f8a-114">Filters have a [FilterContext](/dotnet/api/microsoft.aspnetcore.mvc.filters.filtercontext?view=aspnetcore-2.0) derived parameter, which provides access to `HttpContext`.</span></span> <span data-ttu-id="44f8a-115">Například [implementovat atribut filtru](#ifa) ukázka přidá hlavičku odpovědi, něco, co nelze provést s konstruktory nebo middlewaru.</span><span class="sxs-lookup"><span data-stu-id="44f8a-115">For example, the [Implement a filter attribute](#ifa) sample adds a header to the response, something that can't be done with constructors or middleware.</span></span>

<span data-ttu-id="44f8a-116">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/razor-pages/filter/sample/PageFilter) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="44f8a-116">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/razor-pages/filter/sample/PageFilter) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="44f8a-117">Filtry stránek Razor poskytují následující metody, které se dají aplikovat globálně nebo na úrovni stránky:</span><span class="sxs-lookup"><span data-stu-id="44f8a-117">Razor Page filters provide the following methods, which can be applied globally or at the page level:</span></span>

* <span data-ttu-id="44f8a-118">Synchronní metody:</span><span class="sxs-lookup"><span data-stu-id="44f8a-118">Synchronous methods:</span></span>

  * <span data-ttu-id="44f8a-119">[OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0) : Volá se po vybrali metodu obslužné rutiny, ale před modelu dojde k vazby.</span><span class="sxs-lookup"><span data-stu-id="44f8a-119">[OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0) : Called after a handler method has been selected, but before model binding occurs.</span></span>
  * <span data-ttu-id="44f8a-120">[OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0) : Voláno před provedením metody obslužné rutiny, po dokončení vazby modelu.</span><span class="sxs-lookup"><span data-stu-id="44f8a-120">[OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0) : Called before the handler method executes, after model binding is complete.</span></span>
  * <span data-ttu-id="44f8a-121">[OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0) : Voláno po spuštění metody obslužné rutiny, před výsledku akce.</span><span class="sxs-lookup"><span data-stu-id="44f8a-121">[OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0) : Called after the handler method executes, before the action result.</span></span>

* <span data-ttu-id="44f8a-122">Asynchronní metody:</span><span class="sxs-lookup"><span data-stu-id="44f8a-122">Asynchronous methods:</span></span>

  * <span data-ttu-id="44f8a-123">[OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0) : Asynchronně volá po výběru metody obslužné rutiny, ale před provedením vázání modelu.</span><span class="sxs-lookup"><span data-stu-id="44f8a-123">[OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0) : Called asynchronously after the handler method has been selected, but before model binding occurs.</span></span>
  * <span data-ttu-id="44f8a-124">[OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0) : Asynchronně volat před vyvoláním metody obslužné rutiny, po dokončení vazby modelu.</span><span class="sxs-lookup"><span data-stu-id="44f8a-124">[OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0) : Called asynchronously before the handler method is invoked, after model binding is complete.</span></span>

> [!NOTE]
> <span data-ttu-id="44f8a-125">Implementace **buď** synchronní nebo asynchronní verzi rozhraní filtru, ne obojí.</span><span class="sxs-lookup"><span data-stu-id="44f8a-125">Implement **either** the synchronous or the async version of a filter interface, not both.</span></span> <span data-ttu-id="44f8a-126">Rozhraní framework nejprve zkontroluje a zjistěte, jestli implementuje rozhraní asynchronní filtr, a pokud ano, který volá.</span><span class="sxs-lookup"><span data-stu-id="44f8a-126">The framework checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="44f8a-127">Pokud tomu tak není, volá rozhraní synchronní metody.</span><span class="sxs-lookup"><span data-stu-id="44f8a-127">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="44f8a-128">Pokud jsou obě rozhraní implementované, se nazývají asynchronní metody.</span><span class="sxs-lookup"><span data-stu-id="44f8a-128">If both interfaces are implemented, only the async methods are be called.</span></span> <span data-ttu-id="44f8a-129">Stejné pravidlo platí pro přepsání hodnot v stránky, implementovat synchronní nebo asynchronní verze přepsání, ne obojí.</span><span class="sxs-lookup"><span data-stu-id="44f8a-129">The same rule applies to overrides in pages, implement the synchronous or the async version of the override, not both.</span></span>

## <a name="implement-razor-page-filters-globally"></a><span data-ttu-id="44f8a-130">Globálně implementovat filtry stránek Razor</span><span class="sxs-lookup"><span data-stu-id="44f8a-130">Implement Razor Page filters globally</span></span>

<span data-ttu-id="44f8a-131">Následující kód implementuje `IAsyncPageFilter`:</span><span class="sxs-lookup"><span data-stu-id="44f8a-131">The following code implements `IAsyncPageFilter`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Filters/SampleAsyncPageFilter.cs?name=snippet1)]

<span data-ttu-id="44f8a-132">V předchozím kódu [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger?view=aspnetcore-2.0) se nevyžaduje.</span><span class="sxs-lookup"><span data-stu-id="44f8a-132">In the preceding code, [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger?view=aspnetcore-2.0) is not required.</span></span> <span data-ttu-id="44f8a-133">V ukázce slouží k poskytnutí informací o trasování pro aplikaci.</span><span class="sxs-lookup"><span data-stu-id="44f8a-133">It's used in the sample to provide trace information for the application.</span></span>

<span data-ttu-id="44f8a-134">V následujícím kódu umožňuje `SampleAsyncPageFilter` v `Startup` třídy:</span><span class="sxs-lookup"><span data-stu-id="44f8a-134">The following code enables the `SampleAsyncPageFilter` in the `Startup` class:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Startup.cs?name=snippet2&highlight=11)]

<span data-ttu-id="44f8a-135">Následující kód ukazuje kompletní `Startup` třídy:</span><span class="sxs-lookup"><span data-stu-id="44f8a-135">The following code shows the complete `Startup` class:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Startup.cs?name=snippet1)]

<span data-ttu-id="44f8a-136">Následující kód volá `AddFolderApplicationModelConvention` použít `SampleAsyncPageFilter` na pouze stránky */subFolder*:</span><span class="sxs-lookup"><span data-stu-id="44f8a-136">The following code calls `AddFolderApplicationModelConvention` to apply the `SampleAsyncPageFilter` to only pages in */subFolder*:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Startup2.cs?name=snippet2)]

<span data-ttu-id="44f8a-137">Následující kód implementuje synchronní `IPageFilter`:</span><span class="sxs-lookup"><span data-stu-id="44f8a-137">The following code implements the synchronous `IPageFilter`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Filters/SamplePageFilter.cs?name=snippet1)]

<span data-ttu-id="44f8a-138">V následujícím kódu umožňuje `SamplePageFilter`:</span><span class="sxs-lookup"><span data-stu-id="44f8a-138">The following code enables the `SamplePageFilter`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/StartupSync.cs?name=snippet2&highlight=11)]

::: moniker range=">= aspnetcore-2.1"

## <a name="implement-razor-page-filters-by-overriding-filter-methods"></a><span data-ttu-id="44f8a-139">Stránka Razor filtry implementace přepsáním metody filtru</span><span class="sxs-lookup"><span data-stu-id="44f8a-139">Implement Razor Page filters by overriding filter methods</span></span>

<span data-ttu-id="44f8a-140">Následující kód přepíše synchronní filtry stránky Razor:</span><span class="sxs-lookup"><span data-stu-id="44f8a-140">The following code overrides the synchronous Razor Page filters:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Pages/Index.cshtml.cs)]

::: moniker-end

<a name="ifa"></a>

## <a name="implement-a-filter-attribute"></a><span data-ttu-id="44f8a-141">Implementace atribut filtru</span><span class="sxs-lookup"><span data-stu-id="44f8a-141">Implement a filter attribute</span></span>

<span data-ttu-id="44f8a-142">Integrovaný filtr založený na atributu [OnResultExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncresultfilter.onresultexecutionasync?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Filters_IAsyncResultFilter_OnResultExecutionAsync_Microsoft_AspNetCore_Mvc_Filters_ResultExecutingContext_Microsoft_AspNetCore_Mvc_Filters_ResultExecutionDelegate_) filtru můžete má rozčlenit do podtříd.</span><span class="sxs-lookup"><span data-stu-id="44f8a-142">The built-in attribute-based filter [OnResultExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncresultfilter.onresultexecutionasync?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Filters_IAsyncResultFilter_OnResultExecutionAsync_Microsoft_AspNetCore_Mvc_Filters_ResultExecutingContext_Microsoft_AspNetCore_Mvc_Filters_ResultExecutionDelegate_) filter can be subclassed.</span></span> <span data-ttu-id="44f8a-143">Filtr přidá do odpovědi hlavičku:</span><span class="sxs-lookup"><span data-stu-id="44f8a-143">The following filter adds a header to the response:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Filters/AddHeaderAttribute.cs)]

<span data-ttu-id="44f8a-144">Následující kód se vztahuje `AddHeader` atribut:</span><span class="sxs-lookup"><span data-stu-id="44f8a-144">The following code applies the `AddHeader` attribute:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Pages/Contact.cshtml.cs?name=snippet1)]

<span data-ttu-id="44f8a-145">Zobrazit [přepisuje výchozí pořadí](xref:mvc/controllers/filters#overriding-the-default-order) pokyny k přepsání pořadí.</span><span class="sxs-lookup"><span data-stu-id="44f8a-145">See [Overriding the default order](xref:mvc/controllers/filters#overriding-the-default-order) for instructions on overriding the order.</span></span>

<span data-ttu-id="44f8a-146">Zobrazit [zrušení a krátký circuiting](xref:mvc/controllers/filters#cancellation-and-short-circuiting) pokyny týkající se zkrácenou filtr kanálu z filtru.</span><span class="sxs-lookup"><span data-stu-id="44f8a-146">See [Cancellation and short circuiting](xref:mvc/controllers/filters#cancellation-and-short-circuiting) for instructions to short-circuit the filter pipeline from a filter.</span></span> 

<a name="auth"></a>

## <a name="authorize-filter-attribute"></a><span data-ttu-id="44f8a-147">Povolit atribut filtru</span><span class="sxs-lookup"><span data-stu-id="44f8a-147">Authorize filter attribute</span></span>

<span data-ttu-id="44f8a-148">[Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) atribut lze použít `PageModel`:</span><span class="sxs-lookup"><span data-stu-id="44f8a-148">The [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) attribute can be applied to a `PageModel`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Pages/ModelWithAuthFilter.cshtml.cs?highlight=7)]
