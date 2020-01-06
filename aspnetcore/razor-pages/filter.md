---
title: Filtrovat metody Razor Pages v ASP.NET Core
author: Rick-Anderson
description: Naučte se vytvářet metody filtru pro Razor Pages v ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 12/28/2019
uid: razor-pages/filter
ms.openlocfilehash: 02771219454556b236080c2668243f788693b2c1
ms.sourcegitcommit: 077b45eceae044475f04c1d7ef2d153d7c0515a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/29/2019
ms.locfileid: "75542715"
---
# <a name="filter-methods-for-razor-pages-in-aspnet-core"></a><span data-ttu-id="6aee3-103">Filtrovat metody Razor Pages v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6aee3-103">Filter methods for Razor Pages in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="6aee3-104">Podle [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="6aee3-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="6aee3-105">Filtry stránky Razor [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) a [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) umožňují, aby Razor Pages spustit kód před a po spuštění obslužné rutiny stránky Razor.</span><span class="sxs-lookup"><span data-stu-id="6aee3-105">Razor Page filters [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) and [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) allow Razor Pages to run code before and after a Razor Page handler is run.</span></span> <span data-ttu-id="6aee3-106">Filtry stránky Razor jsou podobné [filtrům akcí ASP.NET Core MVC](xref:mvc/controllers/filters#action-filters), s výjimkou toho, že je nelze použít na jednotlivé metody obslužné rutiny stránky.</span><span class="sxs-lookup"><span data-stu-id="6aee3-106">Razor Page filters are similar to [ASP.NET Core MVC action filters](xref:mvc/controllers/filters#action-filters), except they can't be applied to individual page handler methods.</span></span>

<span data-ttu-id="6aee3-107">Filtry stránky Razor:</span><span class="sxs-lookup"><span data-stu-id="6aee3-107">Razor Page filters:</span></span>

* <span data-ttu-id="6aee3-108">Spusťte kód po výběru metody obslužné rutiny, ale před tím, než dojde k vazbě modelu.</span><span class="sxs-lookup"><span data-stu-id="6aee3-108">Run code after a handler method has been selected, but before model binding occurs.</span></span>
* <span data-ttu-id="6aee3-109">Po dokončení vazby modelu spusťte kód před spuštěním obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="6aee3-109">Run code before the handler method executes, after model binding is complete.</span></span>
* <span data-ttu-id="6aee3-110">Spusťte kód poté, co se spustí metoda obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="6aee3-110">Run code after the handler method executes.</span></span>
* <span data-ttu-id="6aee3-111">Může být implementováno na stránce nebo globálně.</span><span class="sxs-lookup"><span data-stu-id="6aee3-111">Can be implemented on a page or globally.</span></span>
* <span data-ttu-id="6aee3-112">Nelze použít na konkrétní metody obslužné rutiny stránky.</span><span class="sxs-lookup"><span data-stu-id="6aee3-112">Cannot be applied to specific page handler methods.</span></span>
* <span data-ttu-id="6aee3-113">Může mít závislosti konstruktoru vyplněné [vstřikem závislosti](xref:fundamentals/dependency-injection) (di).</span><span class="sxs-lookup"><span data-stu-id="6aee3-113">Can have constructor dependencies populated by [Dependency Injection](xref:fundamentals/dependency-injection) (DI).</span></span> <span data-ttu-id="6aee3-114">Další informace najdete v tématu [ServiceFilterAttribute](/aspnet/core/mvc/controllers/filters#servicefilterattribute) a [TypeFilterAttribute](/aspnet/core/mvc/controllers/filters#typefilterattribute).</span><span class="sxs-lookup"><span data-stu-id="6aee3-114">For more information, see [ServiceFilterAttribute](/aspnet/core/mvc/controllers/filters#servicefilterattribute) and [TypeFilterAttribute](/aspnet/core/mvc/controllers/filters#typefilterattribute).</span></span>

<span data-ttu-id="6aee3-115">Kód lze spustit před spuštěním obslužné rutiny pomocí konstruktoru stránky nebo middlewaru, ale pouze filtry stránky Razor mají přístup k <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.HttpContext>.</span><span class="sxs-lookup"><span data-stu-id="6aee3-115">Code can be run before a handler method executes using the page constructor or middleware, but only Razor Page filters have access to <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.HttpContext>.</span></span> <span data-ttu-id="6aee3-116">Filtry mají <xref:Microsoft.AspNetCore.Mvc.Filters.FilterContext> odvozeného parametru, který poskytuje přístup k `HttpContext`.</span><span class="sxs-lookup"><span data-stu-id="6aee3-116">Filters have a <xref:Microsoft.AspNetCore.Mvc.Filters.FilterContext> derived parameter, which provides access to `HttpContext`.</span></span> <span data-ttu-id="6aee3-117">Například Implementace ukázkového [atributu Filter](#ifa) přidá hlavičku do odpovědi, něco, co nelze provést s konstruktory nebo middlewaru.</span><span class="sxs-lookup"><span data-stu-id="6aee3-117">For example, the [Implement a filter attribute](#ifa) sample adds a header to the response, something that can't be done with constructors or middleware.</span></span>

<span data-ttu-id="6aee3-118">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/filter/3.1sample) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="6aee3-118">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/filter/3.1sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="6aee3-119">Filtry stránky Razor poskytují následující metody, které lze použít globálně nebo na úrovni stránky:</span><span class="sxs-lookup"><span data-stu-id="6aee3-119">Razor Page filters provide the following methods, which can be applied globally or at the page level:</span></span>

* <span data-ttu-id="6aee3-120">Synchronní metody:</span><span class="sxs-lookup"><span data-stu-id="6aee3-120">Synchronous methods:</span></span>

  * <span data-ttu-id="6aee3-121">[OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0) : volá se po výběru metody obslužné rutiny, ale před tím, než dojde k vazbě modelu.</span><span class="sxs-lookup"><span data-stu-id="6aee3-121">[OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0) : Called after a handler method has been selected, but before model binding occurs.</span></span>
  * <span data-ttu-id="6aee3-122">[OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0) : volá se před spuštěním metody obslužné rutiny po dokončení vazby modelu.</span><span class="sxs-lookup"><span data-stu-id="6aee3-122">[OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0) : Called before the handler method executes, after model binding is complete.</span></span>
  * <span data-ttu-id="6aee3-123">[OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0) : volá se, když se spustí obslužná rutina, před výsledek akce.</span><span class="sxs-lookup"><span data-stu-id="6aee3-123">[OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0) : Called after the handler method executes, before the action result.</span></span>

* <span data-ttu-id="6aee3-124">Asynchronní metody:</span><span class="sxs-lookup"><span data-stu-id="6aee3-124">Asynchronous methods:</span></span>

  * <span data-ttu-id="6aee3-125">[OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0) : používá se asynchronně po výběru obslužné rutiny, ale před tím, než dojde k vazbě modelu.</span><span class="sxs-lookup"><span data-stu-id="6aee3-125">[OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0) : Called asynchronously after the handler method has been selected, but before model binding occurs.</span></span>
  * <span data-ttu-id="6aee3-126">[OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0) : používá se asynchronně před vyvoláním metody obslužné rutiny po dokončení vazby modelu.</span><span class="sxs-lookup"><span data-stu-id="6aee3-126">[OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0) : Called asynchronously before the handler method is invoked, after model binding is complete.</span></span>

<span data-ttu-id="6aee3-127">Implementujte **buď** synchronní, nebo asynchronní verzi rozhraní filtru, **nikoli** obojí.</span><span class="sxs-lookup"><span data-stu-id="6aee3-127">Implement **either** the synchronous or the async version of a filter interface, **not** both.</span></span> <span data-ttu-id="6aee3-128">Rozhraní nejprve kontroluje, zda filtr implementuje asynchronní rozhraní, a pokud ano, zavolá tuto metodu.</span><span class="sxs-lookup"><span data-stu-id="6aee3-128">The framework checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="6aee3-129">V takovém případě volá metody synchronního rozhraní.</span><span class="sxs-lookup"><span data-stu-id="6aee3-129">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="6aee3-130">Pokud jsou implementována obě rozhraní, jsou zavolány pouze asynchronní metody.</span><span class="sxs-lookup"><span data-stu-id="6aee3-130">If both interfaces are implemented, only the async methods are called.</span></span> <span data-ttu-id="6aee3-131">Stejné pravidlo se vztahuje na přepsání na stránkách, implementuje synchronní nebo asynchronní verzi přepsání, nikoli obojí.</span><span class="sxs-lookup"><span data-stu-id="6aee3-131">The same rule applies to overrides in pages, implement the synchronous or the async version of the override, not both.</span></span>

## <a name="implement-razor-page-filters-globally"></a><span data-ttu-id="6aee3-132">Globálně implementovat filtry stránek Razor</span><span class="sxs-lookup"><span data-stu-id="6aee3-132">Implement Razor Page filters globally</span></span>

<span data-ttu-id="6aee3-133">Následující kód implementuje `IAsyncPageFilter`:</span><span class="sxs-lookup"><span data-stu-id="6aee3-133">The following code implements `IAsyncPageFilter`:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Filters/SampleAsyncPageFilter.cs?name=snippet1)]

<span data-ttu-id="6aee3-134">V předchozím kódu `ProcessUserAgent.Write` je uživatelem zadaný kód, který pracuje s řetězcem uživatelského agenta.</span><span class="sxs-lookup"><span data-stu-id="6aee3-134">In the preceding code, `ProcessUserAgent.Write` is user supplied code that works with the user agent string.</span></span>

<span data-ttu-id="6aee3-135">Následující kód povoluje `SampleAsyncPageFilter` v `Startup` třídě:</span><span class="sxs-lookup"><span data-stu-id="6aee3-135">The following code enables the `SampleAsyncPageFilter` in the `Startup` class:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Startup.cs?name=snippet2)]

<span data-ttu-id="6aee3-136">Následující kód volá <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> pro použití `SampleAsyncPageFilter` pouze na stránky v */Movies*:</span><span class="sxs-lookup"><span data-stu-id="6aee3-136">The following code calls <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> to apply the `SampleAsyncPageFilter` to only pages in */Movies*:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Startup2.cs?name=snippet2)]

<span data-ttu-id="6aee3-137">Následující kód implementuje synchronní `IPageFilter`:</span><span class="sxs-lookup"><span data-stu-id="6aee3-137">The following code implements the synchronous `IPageFilter`:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Filters/SamplePageFilter.cs?name=snippet1)]

<span data-ttu-id="6aee3-138">Následující kód povoluje `SamplePageFilter`:</span><span class="sxs-lookup"><span data-stu-id="6aee3-138">The following code enables the `SamplePageFilter`:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/StartupSync.cs?name=snippet2)]

## <a name="implement-razor-page-filters-by-overriding-filter-methods"></a><span data-ttu-id="6aee3-139">Implementace filtrů stránek Razor pomocí přepsání metod filtrování</span><span class="sxs-lookup"><span data-stu-id="6aee3-139">Implement Razor Page filters by overriding filter methods</span></span>

<span data-ttu-id="6aee3-140">Následující kód Přepisuje filtry stránky asynchronního Razor:</span><span class="sxs-lookup"><span data-stu-id="6aee3-140">The following code overrides the asynchronous Razor Page filters:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Pages/Index.cshtml.cs?name=snippet)]

<a name="ifa"></a>

## <a name="implement-a-filter-attribute"></a><span data-ttu-id="6aee3-141">Implementace atributu filtru</span><span class="sxs-lookup"><span data-stu-id="6aee3-141">Implement a filter attribute</span></span>

<span data-ttu-id="6aee3-142">Vestavěný filtr založený na atributech <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter.OnResultExecutionAsync*> filtr může být podtříd.</span><span class="sxs-lookup"><span data-stu-id="6aee3-142">The built-in attribute-based filter <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter.OnResultExecutionAsync*> filter can be subclassed.</span></span> <span data-ttu-id="6aee3-143">Následující filtr Přidá hlavičku k odpovědi:</span><span class="sxs-lookup"><span data-stu-id="6aee3-143">The following filter adds a header to the response:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Filters/AddHeaderAttribute.cs)]

<span data-ttu-id="6aee3-144">Následující kód používá atribut `AddHeader`:</span><span class="sxs-lookup"><span data-stu-id="6aee3-144">The following code applies the `AddHeader` attribute:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Pages/Movies/Test.cshtml.cs)]

<span data-ttu-id="6aee3-145">K prohlédnutí hlaviček použijte nástroj, jako je například nástroj pro vývojáře v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="6aee3-145">Use a tool such as the browser developer tools to examine the headers.</span></span> <span data-ttu-id="6aee3-146">V části **hlavičky odpovědi**se zobrazí `author: Rick`.</span><span class="sxs-lookup"><span data-stu-id="6aee3-146">Under **Response Headers**, `author: Rick` is displayed.</span></span>

<span data-ttu-id="6aee3-147">Pokyny k přepsání objednávky najdete v tématu [přepisování výchozího pořadí](xref:mvc/controllers/filters#overriding-the-default-order) .</span><span class="sxs-lookup"><span data-stu-id="6aee3-147">See [Overriding the default order](xref:mvc/controllers/filters#overriding-the-default-order) for instructions on overriding the order.</span></span>

<span data-ttu-id="6aee3-148">Pokyny k krátkodobému zaokruhu kanálu filtrování z filtru najdete v tématu [zrušení a krátké vytvoření okruhu](xref:mvc/controllers/filters#cancellation-and-short-circuiting) .</span><span class="sxs-lookup"><span data-stu-id="6aee3-148">See [Cancellation and short circuiting](xref:mvc/controllers/filters#cancellation-and-short-circuiting) for instructions to short-circuit the filter pipeline from a filter.</span></span>

<a name="auth"></a>

## <a name="authorize-filter-attribute"></a><span data-ttu-id="6aee3-149">Autorizovat atribut filtru</span><span class="sxs-lookup"><span data-stu-id="6aee3-149">Authorize filter attribute</span></span>

<span data-ttu-id="6aee3-150">Atribut [autorizace](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) lze použít pro `PageModel`:</span><span class="sxs-lookup"><span data-stu-id="6aee3-150">The [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) attribute can be applied to a `PageModel`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Pages/ModelWithAuthFilter.cshtml.cs?highlight=7)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="6aee3-151">Podle [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="6aee3-151">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="6aee3-152">Filtry stránky Razor [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) a [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) umožňují, aby Razor Pages spustit kód před a po spuštění obslužné rutiny stránky Razor.</span><span class="sxs-lookup"><span data-stu-id="6aee3-152">Razor Page filters [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) and [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) allow Razor Pages to run code before and after a Razor Page handler is run.</span></span> <span data-ttu-id="6aee3-153">Filtry stránky Razor jsou podobné [filtrům akcí ASP.NET Core MVC](xref:mvc/controllers/filters#action-filters), s výjimkou toho, že je nelze použít na jednotlivé metody obslužné rutiny stránky.</span><span class="sxs-lookup"><span data-stu-id="6aee3-153">Razor Page filters are similar to [ASP.NET Core MVC action filters](xref:mvc/controllers/filters#action-filters), except they can't be applied to individual page handler methods.</span></span>

<span data-ttu-id="6aee3-154">Filtry stránky Razor:</span><span class="sxs-lookup"><span data-stu-id="6aee3-154">Razor Page filters:</span></span>

* <span data-ttu-id="6aee3-155">Spusťte kód po výběru metody obslužné rutiny, ale před tím, než dojde k vazbě modelu.</span><span class="sxs-lookup"><span data-stu-id="6aee3-155">Run code after a handler method has been selected, but before model binding occurs.</span></span>
* <span data-ttu-id="6aee3-156">Po dokončení vazby modelu spusťte kód před spuštěním obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="6aee3-156">Run code before the handler method executes, after model binding is complete.</span></span>
* <span data-ttu-id="6aee3-157">Spusťte kód poté, co se spustí metoda obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="6aee3-157">Run code after the handler method executes.</span></span>
* <span data-ttu-id="6aee3-158">Může být implementováno na stránce nebo globálně.</span><span class="sxs-lookup"><span data-stu-id="6aee3-158">Can be implemented on a page or globally.</span></span>
* <span data-ttu-id="6aee3-159">Nelze použít na konkrétní metody obslužné rutiny stránky.</span><span class="sxs-lookup"><span data-stu-id="6aee3-159">Cannot be applied to specific page handler methods.</span></span>

<span data-ttu-id="6aee3-160">Kód lze spustit před spuštěním obslužné rutiny pomocí konstruktoru stránky nebo middlewaru, ale pouze filtry stránky Razor mají přístup k [HttpContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.httpcontext?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_HttpContext).</span><span class="sxs-lookup"><span data-stu-id="6aee3-160">Code can be run before a handler method executes using the page constructor or middleware, but only Razor Page filters have access to [HttpContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.httpcontext?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_HttpContext).</span></span> <span data-ttu-id="6aee3-161">Filtry mají [FilterContext](/dotnet/api/microsoft.aspnetcore.mvc.filters.filtercontext?view=aspnetcore-2.0) odvozený parametr, který poskytuje přístup k `HttpContext`.</span><span class="sxs-lookup"><span data-stu-id="6aee3-161">Filters have a [FilterContext](/dotnet/api/microsoft.aspnetcore.mvc.filters.filtercontext?view=aspnetcore-2.0) derived parameter, which provides access to `HttpContext`.</span></span> <span data-ttu-id="6aee3-162">Například Implementace ukázkového [atributu Filter](#ifa) přidá hlavičku do odpovědi, něco, co nelze provést s konstruktory nebo middlewaru.</span><span class="sxs-lookup"><span data-stu-id="6aee3-162">For example, the [Implement a filter attribute](#ifa) sample adds a header to the response, something that can't be done with constructors or middleware.</span></span>

<span data-ttu-id="6aee3-163">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/filter/sample/PageFilter) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="6aee3-163">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/filter/sample/PageFilter) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="6aee3-164">Filtry stránky Razor poskytují následující metody, které lze použít globálně nebo na úrovni stránky:</span><span class="sxs-lookup"><span data-stu-id="6aee3-164">Razor Page filters provide the following methods, which can be applied globally or at the page level:</span></span>

* <span data-ttu-id="6aee3-165">Synchronní metody:</span><span class="sxs-lookup"><span data-stu-id="6aee3-165">Synchronous methods:</span></span>

  * <span data-ttu-id="6aee3-166">[OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0) : volá se po výběru metody obslužné rutiny, ale před tím, než dojde k vazbě modelu.</span><span class="sxs-lookup"><span data-stu-id="6aee3-166">[OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0) : Called after a handler method has been selected, but before model binding occurs.</span></span>
  * <span data-ttu-id="6aee3-167">[OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0) : volá se před spuštěním metody obslužné rutiny po dokončení vazby modelu.</span><span class="sxs-lookup"><span data-stu-id="6aee3-167">[OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0) : Called before the handler method executes, after model binding is complete.</span></span>
  * <span data-ttu-id="6aee3-168">[OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0) : volá se, když se spustí obslužná rutina, před výsledek akce.</span><span class="sxs-lookup"><span data-stu-id="6aee3-168">[OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0) : Called after the handler method executes, before the action result.</span></span>

* <span data-ttu-id="6aee3-169">Asynchronní metody:</span><span class="sxs-lookup"><span data-stu-id="6aee3-169">Asynchronous methods:</span></span>

  * <span data-ttu-id="6aee3-170">[OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0) : používá se asynchronně po výběru obslužné rutiny, ale před tím, než dojde k vazbě modelu.</span><span class="sxs-lookup"><span data-stu-id="6aee3-170">[OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0) : Called asynchronously after the handler method has been selected, but before model binding occurs.</span></span>
  * <span data-ttu-id="6aee3-171">[OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0) : používá se asynchronně před vyvoláním metody obslužné rutiny po dokončení vazby modelu.</span><span class="sxs-lookup"><span data-stu-id="6aee3-171">[OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0) : Called asynchronously before the handler method is invoked, after model binding is complete.</span></span>

> [!NOTE]
> <span data-ttu-id="6aee3-172">Implementujte **buď** synchronní, nebo asynchronní verzi rozhraní filtru, nikoli obojí.</span><span class="sxs-lookup"><span data-stu-id="6aee3-172">Implement **either** the synchronous or the async version of a filter interface, not both.</span></span> <span data-ttu-id="6aee3-173">Rozhraní nejprve kontroluje, zda filtr implementuje asynchronní rozhraní, a pokud ano, zavolá tuto metodu.</span><span class="sxs-lookup"><span data-stu-id="6aee3-173">The framework checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="6aee3-174">V takovém případě volá metody synchronního rozhraní.</span><span class="sxs-lookup"><span data-stu-id="6aee3-174">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="6aee3-175">Pokud jsou implementována obě rozhraní, jsou zavolány pouze asynchronní metody.</span><span class="sxs-lookup"><span data-stu-id="6aee3-175">If both interfaces are implemented, only the async methods are called.</span></span> <span data-ttu-id="6aee3-176">Stejné pravidlo se vztahuje na přepsání na stránkách, implementuje synchronní nebo asynchronní verzi přepsání, nikoli obojí.</span><span class="sxs-lookup"><span data-stu-id="6aee3-176">The same rule applies to overrides in pages, implement the synchronous or the async version of the override, not both.</span></span>

## <a name="implement-razor-page-filters-globally"></a><span data-ttu-id="6aee3-177">Globálně implementovat filtry stránek Razor</span><span class="sxs-lookup"><span data-stu-id="6aee3-177">Implement Razor Page filters globally</span></span>

<span data-ttu-id="6aee3-178">Následující kód implementuje `IAsyncPageFilter`:</span><span class="sxs-lookup"><span data-stu-id="6aee3-178">The following code implements `IAsyncPageFilter`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Filters/SampleAsyncPageFilter.cs?name=snippet1)]

<span data-ttu-id="6aee3-179">V předchozím kódu není [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger?view=aspnetcore-2.0) požadováno.</span><span class="sxs-lookup"><span data-stu-id="6aee3-179">In the preceding code, [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger?view=aspnetcore-2.0) is not required.</span></span> <span data-ttu-id="6aee3-180">Používá se v ukázce k poskytnutí informací o trasování pro aplikaci.</span><span class="sxs-lookup"><span data-stu-id="6aee3-180">It's used in the sample to provide trace information for the application.</span></span>

<span data-ttu-id="6aee3-181">Následující kód povoluje `SampleAsyncPageFilter` v `Startup` třídě:</span><span class="sxs-lookup"><span data-stu-id="6aee3-181">The following code enables the `SampleAsyncPageFilter` in the `Startup` class:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Startup.cs?name=snippet2&highlight=11)]

<span data-ttu-id="6aee3-182">Následující kód ukazuje kompletní třídu `Startup`:</span><span class="sxs-lookup"><span data-stu-id="6aee3-182">The following code shows the complete `Startup` class:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Startup.cs?name=snippet1)]

<span data-ttu-id="6aee3-183">Následující kód volá `AddFolderApplicationModelConvention` pro použití `SampleAsyncPageFilter` pouze na stránky v */subFolder*:</span><span class="sxs-lookup"><span data-stu-id="6aee3-183">The following code calls `AddFolderApplicationModelConvention` to apply the `SampleAsyncPageFilter` to only pages in */subFolder*:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Startup2.cs?name=snippet2)]

<span data-ttu-id="6aee3-184">Následující kód implementuje synchronní `IPageFilter`:</span><span class="sxs-lookup"><span data-stu-id="6aee3-184">The following code implements the synchronous `IPageFilter`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Filters/SamplePageFilter.cs?name=snippet1)]

<span data-ttu-id="6aee3-185">Následující kód povoluje `SamplePageFilter`:</span><span class="sxs-lookup"><span data-stu-id="6aee3-185">The following code enables the `SamplePageFilter`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/StartupSync.cs?name=snippet2&highlight=11)]

## <a name="implement-razor-page-filters-by-overriding-filter-methods"></a><span data-ttu-id="6aee3-186">Implementace filtrů stránek Razor pomocí přepsání metod filtrování</span><span class="sxs-lookup"><span data-stu-id="6aee3-186">Implement Razor Page filters by overriding filter methods</span></span>

<span data-ttu-id="6aee3-187">Následující kód přepíše synchronní filtry stránky Razor:</span><span class="sxs-lookup"><span data-stu-id="6aee3-187">The following code overrides the synchronous Razor Page filters:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Pages/Index.cshtml.cs)]

<a name="ifa"></a>

## <a name="implement-a-filter-attribute"></a><span data-ttu-id="6aee3-188">Implementace atributu filtru</span><span class="sxs-lookup"><span data-stu-id="6aee3-188">Implement a filter attribute</span></span>

<span data-ttu-id="6aee3-189">Vestavěný filtr [OnResultExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncresultfilter.onresultexecutionasync?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Filters_IAsyncResultFilter_OnResultExecutionAsync_Microsoft_AspNetCore_Mvc_Filters_ResultExecutingContext_Microsoft_AspNetCore_Mvc_Filters_ResultExecutionDelegate_) filtru založený na atributech může být podtříd.</span><span class="sxs-lookup"><span data-stu-id="6aee3-189">The built-in attribute-based filter [OnResultExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncresultfilter.onresultexecutionasync?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Filters_IAsyncResultFilter_OnResultExecutionAsync_Microsoft_AspNetCore_Mvc_Filters_ResultExecutingContext_Microsoft_AspNetCore_Mvc_Filters_ResultExecutionDelegate_) filter can be subclassed.</span></span> <span data-ttu-id="6aee3-190">Následující filtr Přidá hlavičku k odpovědi:</span><span class="sxs-lookup"><span data-stu-id="6aee3-190">The following filter adds a header to the response:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Filters/AddHeaderAttribute.cs)]

<span data-ttu-id="6aee3-191">Následující kód používá atribut `AddHeader`:</span><span class="sxs-lookup"><span data-stu-id="6aee3-191">The following code applies the `AddHeader` attribute:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Pages/Contact.cshtml.cs?name=snippet1)]

<span data-ttu-id="6aee3-192">Pokyny k přepsání objednávky najdete v tématu [přepisování výchozího pořadí](xref:mvc/controllers/filters#overriding-the-default-order) .</span><span class="sxs-lookup"><span data-stu-id="6aee3-192">See [Overriding the default order](xref:mvc/controllers/filters#overriding-the-default-order) for instructions on overriding the order.</span></span>

<span data-ttu-id="6aee3-193">Pokyny k krátkodobému zaokruhu kanálu filtrování z filtru najdete v tématu [zrušení a krátké vytvoření okruhu](xref:mvc/controllers/filters#cancellation-and-short-circuiting) .</span><span class="sxs-lookup"><span data-stu-id="6aee3-193">See [Cancellation and short circuiting](xref:mvc/controllers/filters#cancellation-and-short-circuiting) for instructions to short-circuit the filter pipeline from a filter.</span></span> 

<a name="auth"></a>

## <a name="authorize-filter-attribute"></a><span data-ttu-id="6aee3-194">Autorizovat atribut filtru</span><span class="sxs-lookup"><span data-stu-id="6aee3-194">Authorize filter attribute</span></span>

<span data-ttu-id="6aee3-195">Atribut [autorizace](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) lze použít pro `PageModel`:</span><span class="sxs-lookup"><span data-stu-id="6aee3-195">The [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) attribute can be applied to a `PageModel`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Pages/ModelWithAuthFilter.cshtml.cs?highlight=7)]

::: moniker-end
