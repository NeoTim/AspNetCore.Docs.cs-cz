---
title: Filtrovat metody Razor Pages v ASP.NET Core
author: Rick-Anderson
description: Naučte se vytvářet metody filtru pro Razor Pages v ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 04/05/2018
uid: razor-pages/filter
ms.openlocfilehash: 1da46c61617a01698e3c4b1fe6bf9825db6643fd
ms.sourcegitcommit: a166291c6708f5949c417874108332856b53b6a9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/18/2019
ms.locfileid: "72589954"
---
# <a name="filter-methods-for-razor-pages-in-aspnet-core"></a><span data-ttu-id="736c7-103">Filtrovat metody Razor Pages v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="736c7-103">Filter methods for Razor Pages in ASP.NET Core</span></span>

<span data-ttu-id="736c7-104">Od [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="736c7-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="736c7-105">Filtry stránky Razor [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) a [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) umožňují, aby Razor Pages spustit kód před a po spuštění obslužné rutiny stránky Razor.</span><span class="sxs-lookup"><span data-stu-id="736c7-105">Razor Page filters [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) and [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) allow Razor Pages to run code before and after a Razor Page handler is run.</span></span> <span data-ttu-id="736c7-106">Filtry stránky Razor jsou podobné [filtrům akcí ASP.NET Core MVC](xref:mvc/controllers/filters#action-filters), s výjimkou toho, že je nelze použít na jednotlivé metody obslužné rutiny stránky.</span><span class="sxs-lookup"><span data-stu-id="736c7-106">Razor Page filters are similar to [ASP.NET Core MVC action filters](xref:mvc/controllers/filters#action-filters), except they can't be applied to individual page handler methods.</span></span> 

<span data-ttu-id="736c7-107">Filtry stránky Razor:</span><span class="sxs-lookup"><span data-stu-id="736c7-107">Razor Page filters:</span></span>

* <span data-ttu-id="736c7-108">Spusťte kód po výběru metody obslužné rutiny, ale před tím, než dojde k vazbě modelu.</span><span class="sxs-lookup"><span data-stu-id="736c7-108">Run code after a handler method has been selected, but before model binding occurs.</span></span>
* <span data-ttu-id="736c7-109">Po dokončení vazby modelu spusťte kód před spuštěním obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="736c7-109">Run code before the handler method executes, after model binding is complete.</span></span>
* <span data-ttu-id="736c7-110">Spusťte kód poté, co se spustí metoda obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="736c7-110">Run code after the handler method executes.</span></span>
* <span data-ttu-id="736c7-111">Může být implementováno na stránce nebo globálně.</span><span class="sxs-lookup"><span data-stu-id="736c7-111">Can be implemented on a page or globally.</span></span>
* <span data-ttu-id="736c7-112">Nelze použít na konkrétní metody obslužné rutiny stránky.</span><span class="sxs-lookup"><span data-stu-id="736c7-112">Cannot be applied to specific page handler methods.</span></span>

<span data-ttu-id="736c7-113">Kód lze spustit před spuštěním obslužné rutiny pomocí konstruktoru stránky nebo middlewaru, ale pouze filtry stránky Razor mají přístup k [HttpContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.httpcontext?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_HttpContext).</span><span class="sxs-lookup"><span data-stu-id="736c7-113">Code can be run before a handler method executes using the page constructor or middleware, but only Razor Page filters have access to [HttpContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.httpcontext?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_HttpContext).</span></span> <span data-ttu-id="736c7-114">Filtry mají [FilterContext](/dotnet/api/microsoft.aspnetcore.mvc.filters.filtercontext?view=aspnetcore-2.0) odvozený parametr, který poskytuje přístup k `HttpContext`.</span><span class="sxs-lookup"><span data-stu-id="736c7-114">Filters have a [FilterContext](/dotnet/api/microsoft.aspnetcore.mvc.filters.filtercontext?view=aspnetcore-2.0) derived parameter, which provides access to `HttpContext`.</span></span> <span data-ttu-id="736c7-115">Například Implementace ukázkového [atributu Filter](#ifa) přidá hlavičku do odpovědi, něco, co nelze provést s konstruktory nebo middlewaru.</span><span class="sxs-lookup"><span data-stu-id="736c7-115">For example, the [Implement a filter attribute](#ifa) sample adds a header to the response, something that can't be done with constructors or middleware.</span></span>

<span data-ttu-id="736c7-116">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/filter/sample/PageFilter) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="736c7-116">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/filter/sample/PageFilter) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="736c7-117">Filtry stránky Razor poskytují následující metody, které lze použít globálně nebo na úrovni stránky:</span><span class="sxs-lookup"><span data-stu-id="736c7-117">Razor Page filters provide the following methods, which can be applied globally or at the page level:</span></span>

* <span data-ttu-id="736c7-118">Synchronní metody:</span><span class="sxs-lookup"><span data-stu-id="736c7-118">Synchronous methods:</span></span>

  * <span data-ttu-id="736c7-119">[OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0) : volá se po výběru metody obslužné rutiny, ale před tím, než dojde k vazbě modelu.</span><span class="sxs-lookup"><span data-stu-id="736c7-119">[OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0) : Called after a handler method has been selected, but before model binding occurs.</span></span>
  * <span data-ttu-id="736c7-120">[OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0) : volá se před spuštěním metody obslužné rutiny po dokončení vazby modelu.</span><span class="sxs-lookup"><span data-stu-id="736c7-120">[OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0) : Called before the handler method executes, after model binding is complete.</span></span>
  * <span data-ttu-id="736c7-121">[OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0) : volá se, když se spustí obslužná rutina, před výsledek akce.</span><span class="sxs-lookup"><span data-stu-id="736c7-121">[OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0) : Called after the handler method executes, before the action result.</span></span>

* <span data-ttu-id="736c7-122">Asynchronní metody:</span><span class="sxs-lookup"><span data-stu-id="736c7-122">Asynchronous methods:</span></span>

  * <span data-ttu-id="736c7-123">[OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0) : používá se asynchronně po výběru obslužné rutiny, ale před tím, než dojde k vazbě modelu.</span><span class="sxs-lookup"><span data-stu-id="736c7-123">[OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0) : Called asynchronously after the handler method has been selected, but before model binding occurs.</span></span>
  * <span data-ttu-id="736c7-124">[OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0) : používá se asynchronně před vyvoláním metody obslužné rutiny po dokončení vazby modelu.</span><span class="sxs-lookup"><span data-stu-id="736c7-124">[OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0) : Called asynchronously before the handler method is invoked, after model binding is complete.</span></span>

> [!NOTE]
> <span data-ttu-id="736c7-125">Implementujte **buď** synchronní, nebo asynchronní verzi rozhraní filtru, nikoli obojí.</span><span class="sxs-lookup"><span data-stu-id="736c7-125">Implement **either** the synchronous or the async version of a filter interface, not both.</span></span> <span data-ttu-id="736c7-126">Rozhraní nejprve kontroluje, zda filtr implementuje asynchronní rozhraní, a pokud ano, zavolá tuto metodu.</span><span class="sxs-lookup"><span data-stu-id="736c7-126">The framework checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="736c7-127">V takovém případě volá metody synchronního rozhraní.</span><span class="sxs-lookup"><span data-stu-id="736c7-127">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="736c7-128">Pokud jsou implementována obě rozhraní, jsou zavolány pouze asynchronní metody.</span><span class="sxs-lookup"><span data-stu-id="736c7-128">If both interfaces are implemented, only the async methods are called.</span></span> <span data-ttu-id="736c7-129">Stejné pravidlo se vztahuje na přepsání na stránkách, implementuje synchronní nebo asynchronní verzi přepsání, nikoli obojí.</span><span class="sxs-lookup"><span data-stu-id="736c7-129">The same rule applies to overrides in pages, implement the synchronous or the async version of the override, not both.</span></span>

## <a name="implement-razor-page-filters-globally"></a><span data-ttu-id="736c7-130">Globálně implementovat filtry stránek Razor</span><span class="sxs-lookup"><span data-stu-id="736c7-130">Implement Razor Page filters globally</span></span>

<span data-ttu-id="736c7-131">Následující kód implementuje `IAsyncPageFilter`:</span><span class="sxs-lookup"><span data-stu-id="736c7-131">The following code implements `IAsyncPageFilter`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Filters/SampleAsyncPageFilter.cs?name=snippet1)]

<span data-ttu-id="736c7-132">V předchozím kódu není [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger?view=aspnetcore-2.0) požadováno.</span><span class="sxs-lookup"><span data-stu-id="736c7-132">In the preceding code, [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger?view=aspnetcore-2.0) is not required.</span></span> <span data-ttu-id="736c7-133">Používá se v ukázce k poskytnutí informací o trasování pro aplikaci.</span><span class="sxs-lookup"><span data-stu-id="736c7-133">It's used in the sample to provide trace information for the application.</span></span>

<span data-ttu-id="736c7-134">Následující kód povoluje `SampleAsyncPageFilter` v `Startup` třídě:</span><span class="sxs-lookup"><span data-stu-id="736c7-134">The following code enables the `SampleAsyncPageFilter` in the `Startup` class:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Startup.cs?name=snippet2&highlight=11)]

<span data-ttu-id="736c7-135">Následující kód ukazuje kompletní třídu `Startup`:</span><span class="sxs-lookup"><span data-stu-id="736c7-135">The following code shows the complete `Startup` class:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Startup.cs?name=snippet1)]

<span data-ttu-id="736c7-136">Následující kód volá `AddFolderApplicationModelConvention` pro použití `SampleAsyncPageFilter` pouze na stránky v */subFolder*:</span><span class="sxs-lookup"><span data-stu-id="736c7-136">The following code calls `AddFolderApplicationModelConvention` to apply the `SampleAsyncPageFilter` to only pages in */subFolder*:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Startup2.cs?name=snippet2)]

<span data-ttu-id="736c7-137">Následující kód implementuje synchronní `IPageFilter`:</span><span class="sxs-lookup"><span data-stu-id="736c7-137">The following code implements the synchronous `IPageFilter`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Filters/SamplePageFilter.cs?name=snippet1)]

<span data-ttu-id="736c7-138">Následující kód povoluje `SamplePageFilter`:</span><span class="sxs-lookup"><span data-stu-id="736c7-138">The following code enables the `SamplePageFilter`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/StartupSync.cs?name=snippet2&highlight=11)]

::: moniker range=">= aspnetcore-2.1"

## <a name="implement-razor-page-filters-by-overriding-filter-methods"></a><span data-ttu-id="736c7-139">Implementace filtrů stránek Razor pomocí přepsání metod filtrování</span><span class="sxs-lookup"><span data-stu-id="736c7-139">Implement Razor Page filters by overriding filter methods</span></span>

<span data-ttu-id="736c7-140">Následující kód přepíše synchronní filtry stránky Razor:</span><span class="sxs-lookup"><span data-stu-id="736c7-140">The following code overrides the synchronous Razor Page filters:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Pages/Index.cshtml.cs)]

::: moniker-end

<a name="ifa"></a>

## <a name="implement-a-filter-attribute"></a><span data-ttu-id="736c7-141">Implementace atributu filtru</span><span class="sxs-lookup"><span data-stu-id="736c7-141">Implement a filter attribute</span></span>

<span data-ttu-id="736c7-142">Vestavěný filtr [OnResultExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncresultfilter.onresultexecutionasync?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Filters_IAsyncResultFilter_OnResultExecutionAsync_Microsoft_AspNetCore_Mvc_Filters_ResultExecutingContext_Microsoft_AspNetCore_Mvc_Filters_ResultExecutionDelegate_) filtru založený na atributech může být podtříd.</span><span class="sxs-lookup"><span data-stu-id="736c7-142">The built-in attribute-based filter [OnResultExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncresultfilter.onresultexecutionasync?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Filters_IAsyncResultFilter_OnResultExecutionAsync_Microsoft_AspNetCore_Mvc_Filters_ResultExecutingContext_Microsoft_AspNetCore_Mvc_Filters_ResultExecutionDelegate_) filter can be subclassed.</span></span> <span data-ttu-id="736c7-143">Následující filtr Přidá hlavičku k odpovědi:</span><span class="sxs-lookup"><span data-stu-id="736c7-143">The following filter adds a header to the response:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Filters/AddHeaderAttribute.cs)]

<span data-ttu-id="736c7-144">Následující kód používá atribut `AddHeader`:</span><span class="sxs-lookup"><span data-stu-id="736c7-144">The following code applies the `AddHeader` attribute:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Pages/Contact.cshtml.cs?name=snippet1)]

<span data-ttu-id="736c7-145">Pokyny k přepsání objednávky najdete v tématu [přepisování výchozího pořadí](xref:mvc/controllers/filters#overriding-the-default-order) .</span><span class="sxs-lookup"><span data-stu-id="736c7-145">See [Overriding the default order](xref:mvc/controllers/filters#overriding-the-default-order) for instructions on overriding the order.</span></span>

<span data-ttu-id="736c7-146">Pokyny k krátkodobému zaokruhu kanálu filtrování z filtru najdete v tématu [zrušení a krátké vytvoření okruhu](xref:mvc/controllers/filters#cancellation-and-short-circuiting) .</span><span class="sxs-lookup"><span data-stu-id="736c7-146">See [Cancellation and short circuiting](xref:mvc/controllers/filters#cancellation-and-short-circuiting) for instructions to short-circuit the filter pipeline from a filter.</span></span> 

<a name="auth"></a>

## <a name="authorize-filter-attribute"></a><span data-ttu-id="736c7-147">Autorizovat atribut filtru</span><span class="sxs-lookup"><span data-stu-id="736c7-147">Authorize filter attribute</span></span>

<span data-ttu-id="736c7-148">Atribut [autorizace](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) lze použít pro `PageModel`:</span><span class="sxs-lookup"><span data-stu-id="736c7-148">The [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) attribute can be applied to a `PageModel`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Pages/ModelWithAuthFilter.cshtml.cs?highlight=7)]
