---
title: Metody filtrování pro žiletkové stránky v ASP.NET jádru
author: Rick-Anderson
description: Přečtěte si, jak vytvořit metody filtrování pro Razor Pages v ASP.NET core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 2/18/2020
uid: razor-pages/filter
ms.openlocfilehash: cd772da8ed565bc779d8c6bcc7c9949a0c1c7c60
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78660753"
---
# <a name="filter-methods-for-razor-pages-in-aspnet-core"></a><span data-ttu-id="531d4-103">Metody filtrování pro žiletkové stránky v ASP.NET jádru</span><span class="sxs-lookup"><span data-stu-id="531d4-103">Filter methods for Razor Pages in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="531d4-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="531d4-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="531d4-105">Filtry Razor Page [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) a [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) umožňují razor pages spouštět kód před a po spuštění obslužné rutiny Razor Page.</span><span class="sxs-lookup"><span data-stu-id="531d4-105">Razor Page filters [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) and [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) allow Razor Pages to run code before and after a Razor Page handler is run.</span></span> <span data-ttu-id="531d4-106">Filtry holicí strojky jsou podobné [ASP.NET filtry akcí Core MVC](xref:mvc/controllers/filters#action-filters), s výjimkou, že je nelze použít na jednotlivé metody obslužné rutiny stránky.</span><span class="sxs-lookup"><span data-stu-id="531d4-106">Razor Page filters are similar to [ASP.NET Core MVC action filters](xref:mvc/controllers/filters#action-filters), except they can't be applied to individual page handler methods.</span></span>

<span data-ttu-id="531d4-107">Filtry stránky holicí strojek:</span><span class="sxs-lookup"><span data-stu-id="531d4-107">Razor Page filters:</span></span>

* <span data-ttu-id="531d4-108">Spusťte kód po metodě obslužné rutiny, ale dříve, než dojde k vazbě modelu.</span><span class="sxs-lookup"><span data-stu-id="531d4-108">Run code after a handler method has been selected, but before model binding occurs.</span></span>
* <span data-ttu-id="531d4-109">Spusťte kód před spuštěním metody obslužné rutiny po dokončení vazby modelu.</span><span class="sxs-lookup"><span data-stu-id="531d4-109">Run code before the handler method executes, after model binding is complete.</span></span>
* <span data-ttu-id="531d4-110">Spusťte kód po spuštění metody obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="531d4-110">Run code after the handler method executes.</span></span>
* <span data-ttu-id="531d4-111">Lze implementovat na stránce nebo globálně.</span><span class="sxs-lookup"><span data-stu-id="531d4-111">Can be implemented on a page or globally.</span></span>
* <span data-ttu-id="531d4-112">Nelze použít pro konkrétní metody obslužné rutiny stránky.</span><span class="sxs-lookup"><span data-stu-id="531d4-112">Cannot be applied to specific page handler methods.</span></span>
* <span data-ttu-id="531d4-113">Může mít konstruktor uvyplněny [vkládání závislostí](xref:fundamentals/dependency-injection) (DI).</span><span class="sxs-lookup"><span data-stu-id="531d4-113">Can have constructor dependencies populated by [Dependency Injection](xref:fundamentals/dependency-injection) (DI).</span></span> <span data-ttu-id="531d4-114">Další informace naleznete v [tématech ServiceFilterAttribute](/aspnet/core/mvc/controllers/filters#servicefilterattribute) a [TypeFilterAttribute](/aspnet/core/mvc/controllers/filters#typefilterattribute).</span><span class="sxs-lookup"><span data-stu-id="531d4-114">For more information, see [ServiceFilterAttribute](/aspnet/core/mvc/controllers/filters#servicefilterattribute) and [TypeFilterAttribute](/aspnet/core/mvc/controllers/filters#typefilterattribute).</span></span>

<span data-ttu-id="531d4-115">Zatímco konstruktory stránek a middleware umožňují provádění vlastního kódu před spuštěním metody <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.HttpContext> obslužné rutiny, přístup a stránku umožňují pouze filtry Razor Page.</span><span class="sxs-lookup"><span data-stu-id="531d4-115">While page constructors and middleware enable executing custom code before a handler method executes, only Razor Page filters enable access to <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.HttpContext> and the page.</span></span> <span data-ttu-id="531d4-116">Middleware má přístup `HttpContext`k , ale ne k "kontextu stránky".</span><span class="sxs-lookup"><span data-stu-id="531d4-116">Middleware has access to the `HttpContext`, but not to the "page context".</span></span> <span data-ttu-id="531d4-117">Filtry <xref:Microsoft.AspNetCore.Mvc.Filters.FilterContext> mají odvozený parametr, `HttpContext`který poskytuje přístup k aplikaci .</span><span class="sxs-lookup"><span data-stu-id="531d4-117">Filters have a <xref:Microsoft.AspNetCore.Mvc.Filters.FilterContext> derived parameter, which provides access to `HttpContext`.</span></span> <span data-ttu-id="531d4-118">Například [ukázka atributu Implement a filter](#ifa) přidá k odpovědi záhlaví, což nelze provést s konstruktory nebo middleware.</span><span class="sxs-lookup"><span data-stu-id="531d4-118">For example, the [Implement a filter attribute](#ifa) sample adds a header to the response, something that can't be done with constructors or middleware.</span></span>

<span data-ttu-id="531d4-119">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/filter/3.1sample) [(jak stáhnout)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="531d4-119">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/filter/3.1sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="531d4-120">Filtry Razor Page poskytují následující metody, které lze použít globálně nebo na úrovni stránky:</span><span class="sxs-lookup"><span data-stu-id="531d4-120">Razor Page filters provide the following methods, which can be applied globally or at the page level:</span></span>

* <span data-ttu-id="531d4-121">Synchronní metody:</span><span class="sxs-lookup"><span data-stu-id="531d4-121">Synchronous methods:</span></span>

  * <span data-ttu-id="531d4-122">[OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0) : Voláno po obslužné rutiny metoda byla vybrána, ale před dojde k vazbě modelu.</span><span class="sxs-lookup"><span data-stu-id="531d4-122">[OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0) : Called after a handler method has been selected, but before model binding occurs.</span></span>
  * <span data-ttu-id="531d4-123">[OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0) : Voláno před spuštěním metody obslužné rutiny po dokončení vazby modelu.</span><span class="sxs-lookup"><span data-stu-id="531d4-123">[OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0) : Called before the handler method executes, after model binding is complete.</span></span>
  * <span data-ttu-id="531d4-124">[OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0) : Voláno po spuštění metody obslužné rutiny před výsledkem akce.</span><span class="sxs-lookup"><span data-stu-id="531d4-124">[OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0) : Called after the handler method executes, before the action result.</span></span>

* <span data-ttu-id="531d4-125">Asynchronní metody:</span><span class="sxs-lookup"><span data-stu-id="531d4-125">Asynchronous methods:</span></span>

  * <span data-ttu-id="531d4-126">[OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0) : Voláno asynchronně po metodě obslužné rutiny, ale dříve, než dojde k vazbě modelu.</span><span class="sxs-lookup"><span data-stu-id="531d4-126">[OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0) : Called asynchronously after the handler method has been selected, but before model binding occurs.</span></span>
  * <span data-ttu-id="531d4-127">[OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0) : Voláno asynchronně před vyvoláním metody obslužné rutiny po dokončení vazby modelu.</span><span class="sxs-lookup"><span data-stu-id="531d4-127">[OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0) : Called asynchronously before the handler method is invoked, after model binding is complete.</span></span>

<span data-ttu-id="531d4-128">Implementujte **synchronní** nebo asynchronní verzi rozhraní filtru, **nikoli** obojí.</span><span class="sxs-lookup"><span data-stu-id="531d4-128">Implement **either** the synchronous or the async version of a filter interface, **not** both.</span></span> <span data-ttu-id="531d4-129">Rozhraní framework nejprve zkontroluje, pokud filtr implementuje asynchronní rozhraní a pokud ano, volá to.</span><span class="sxs-lookup"><span data-stu-id="531d4-129">The framework checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="531d4-130">Pokud ne, volá metody synchronního rozhraní.</span><span class="sxs-lookup"><span data-stu-id="531d4-130">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="531d4-131">Pokud jsou implementovány obě rozhraní, jsou volány pouze asynchronní metody.</span><span class="sxs-lookup"><span data-stu-id="531d4-131">If both interfaces are implemented, only the async methods are called.</span></span> <span data-ttu-id="531d4-132">Stejné pravidlo platí pro lokální změny na stránkách, implementovat synchronní nebo asynchronní verzi přepsání, ne obojí.</span><span class="sxs-lookup"><span data-stu-id="531d4-132">The same rule applies to overrides in pages, implement the synchronous or the async version of the override, not both.</span></span>

## <a name="implement-razor-page-filters-globally"></a><span data-ttu-id="531d4-133">Implementovat filtry Razor Page globálně</span><span class="sxs-lookup"><span data-stu-id="531d4-133">Implement Razor Page filters globally</span></span>

<span data-ttu-id="531d4-134">Následující kód implementuje `IAsyncPageFilter`:</span><span class="sxs-lookup"><span data-stu-id="531d4-134">The following code implements `IAsyncPageFilter`:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Filters/SampleAsyncPageFilter.cs?name=snippet1)]

<span data-ttu-id="531d4-135">V předchozím kódu `ProcessUserAgent.Write` je uživatel zadaný kód, který pracuje s řetězcem uživatelského agenta.</span><span class="sxs-lookup"><span data-stu-id="531d4-135">In the preceding code, `ProcessUserAgent.Write` is user supplied code that works with the user agent string.</span></span>

<span data-ttu-id="531d4-136">Následující kód umožňuje `SampleAsyncPageFilter` ve `Startup` třídě:</span><span class="sxs-lookup"><span data-stu-id="531d4-136">The following code enables the `SampleAsyncPageFilter` in the `Startup` class:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Startup.cs?name=snippet2)]

<span data-ttu-id="531d4-137">Následující kód <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> volá použít `SampleAsyncPageFilter` pouze stránky v */Movies*:</span><span class="sxs-lookup"><span data-stu-id="531d4-137">The following code calls <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> to apply the `SampleAsyncPageFilter` to only pages in */Movies*:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Startup2.cs?name=snippet2)]

<span data-ttu-id="531d4-138">Následující kód implementuje synchronní `IPageFilter`:</span><span class="sxs-lookup"><span data-stu-id="531d4-138">The following code implements the synchronous `IPageFilter`:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Filters/SamplePageFilter.cs?name=snippet1)]

<span data-ttu-id="531d4-139">Následující kód umožňuje `SamplePageFilter`:</span><span class="sxs-lookup"><span data-stu-id="531d4-139">The following code enables the `SamplePageFilter`:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/StartupSync.cs?name=snippet2)]

## <a name="implement-razor-page-filters-by-overriding-filter-methods"></a><span data-ttu-id="531d4-140">Implementovat filtry Razor Page přepsáním metod filtru</span><span class="sxs-lookup"><span data-stu-id="531d4-140">Implement Razor Page filters by overriding filter methods</span></span>

<span data-ttu-id="531d4-141">Následující kód přepíše asynchronní filtry Razor Page:</span><span class="sxs-lookup"><span data-stu-id="531d4-141">The following code overrides the asynchronous Razor Page filters:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Pages/Index.cshtml.cs?name=snippet)]

<a name="ifa"></a>

## <a name="implement-a-filter-attribute"></a><span data-ttu-id="531d4-142">Implementace atributu filtru</span><span class="sxs-lookup"><span data-stu-id="531d4-142">Implement a filter attribute</span></span>

<span data-ttu-id="531d4-143">Vestavěný filtr filtr <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter.OnResultExecutionAsync*> založený na atributech může být podtřídou.</span><span class="sxs-lookup"><span data-stu-id="531d4-143">The built-in attribute-based filter <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter.OnResultExecutionAsync*> filter can be subclassed.</span></span> <span data-ttu-id="531d4-144">Následující filtr přidá k odpovědi záhlaví:</span><span class="sxs-lookup"><span data-stu-id="531d4-144">The following filter adds a header to the response:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Filters/AddHeaderAttribute.cs)]

<span data-ttu-id="531d4-145">Následující kód použije `AddHeader` atribut:</span><span class="sxs-lookup"><span data-stu-id="531d4-145">The following code applies the `AddHeader` attribute:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Pages/Movies/Test.cshtml.cs)]

<span data-ttu-id="531d4-146">Záhlaví můžete zkontrolovat pomocí nástroje, například nástrojů pro vývojáře prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="531d4-146">Use a tool such as the browser developer tools to examine the headers.</span></span> <span data-ttu-id="531d4-147">V části Záhlaví `author: Rick` **odpovědí**se zobrazí.</span><span class="sxs-lookup"><span data-stu-id="531d4-147">Under **Response Headers**, `author: Rick` is displayed.</span></span>

<span data-ttu-id="531d4-148">Pokyny k přepsání pořadí v [tématu Přepsání výchozího pořadí](xref:mvc/controllers/filters#overriding-the-default-order) naleznete v tématu Přepsání pořadí.</span><span class="sxs-lookup"><span data-stu-id="531d4-148">See [Overriding the default order](xref:mvc/controllers/filters#overriding-the-default-order) for instructions on overriding the order.</span></span>

<span data-ttu-id="531d4-149">Pokyny ke zkratování potrubí filtru z filtru naleznete v tématu [Zrušení a zkratování.](xref:mvc/controllers/filters#cancellation-and-short-circuiting)</span><span class="sxs-lookup"><span data-stu-id="531d4-149">See [Cancellation and short circuiting](xref:mvc/controllers/filters#cancellation-and-short-circuiting) for instructions to short-circuit the filter pipeline from a filter.</span></span>

<a name="auth"></a>

## <a name="authorize-filter-attribute"></a><span data-ttu-id="531d4-150">Autorizovat atribut filtru</span><span class="sxs-lookup"><span data-stu-id="531d4-150">Authorize filter attribute</span></span>

<span data-ttu-id="531d4-151">Atribut [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) lze použít `PageModel`na :</span><span class="sxs-lookup"><span data-stu-id="531d4-151">The [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) attribute can be applied to a `PageModel`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Pages/ModelWithAuthFilter.cshtml.cs?highlight=7)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="531d4-152">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="531d4-152">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="531d4-153">Filtry Razor Page [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) a [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) umožňují razor pages spouštět kód před a po spuštění obslužné rutiny Razor Page.</span><span class="sxs-lookup"><span data-stu-id="531d4-153">Razor Page filters [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) and [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) allow Razor Pages to run code before and after a Razor Page handler is run.</span></span> <span data-ttu-id="531d4-154">Filtry holicí strojky jsou podobné [ASP.NET filtry akcí Core MVC](xref:mvc/controllers/filters#action-filters), s výjimkou, že je nelze použít na jednotlivé metody obslužné rutiny stránky.</span><span class="sxs-lookup"><span data-stu-id="531d4-154">Razor Page filters are similar to [ASP.NET Core MVC action filters](xref:mvc/controllers/filters#action-filters), except they can't be applied to individual page handler methods.</span></span>

<span data-ttu-id="531d4-155">Filtry stránky holicí strojek:</span><span class="sxs-lookup"><span data-stu-id="531d4-155">Razor Page filters:</span></span>

* <span data-ttu-id="531d4-156">Spusťte kód po metodě obslužné rutiny, ale dříve, než dojde k vazbě modelu.</span><span class="sxs-lookup"><span data-stu-id="531d4-156">Run code after a handler method has been selected, but before model binding occurs.</span></span>
* <span data-ttu-id="531d4-157">Spusťte kód před spuštěním metody obslužné rutiny po dokončení vazby modelu.</span><span class="sxs-lookup"><span data-stu-id="531d4-157">Run code before the handler method executes, after model binding is complete.</span></span>
* <span data-ttu-id="531d4-158">Spusťte kód po spuštění metody obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="531d4-158">Run code after the handler method executes.</span></span>
* <span data-ttu-id="531d4-159">Lze implementovat na stránce nebo globálně.</span><span class="sxs-lookup"><span data-stu-id="531d4-159">Can be implemented on a page or globally.</span></span>
* <span data-ttu-id="531d4-160">Nelze použít pro konkrétní metody obslužné rutiny stránky.</span><span class="sxs-lookup"><span data-stu-id="531d4-160">Cannot be applied to specific page handler methods.</span></span>

<span data-ttu-id="531d4-161">Kód lze spustit před spuštěním metody obslužné rutiny pomocí konstruktoru stránky nebo middlewaru, ale pouze filtry Razor Page mají přístup k [httpcontext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.httpcontext?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_HttpContext).</span><span class="sxs-lookup"><span data-stu-id="531d4-161">Code can be run before a handler method executes using the page constructor or middleware, but only Razor Page filters have access to [HttpContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.httpcontext?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_HttpContext).</span></span> <span data-ttu-id="531d4-162">Filtry mají [filterContext](/dotnet/api/microsoft.aspnetcore.mvc.filters.filtercontext?view=aspnetcore-2.0) odvozený parametr, `HttpContext`který poskytuje přístup k .</span><span class="sxs-lookup"><span data-stu-id="531d4-162">Filters have a [FilterContext](/dotnet/api/microsoft.aspnetcore.mvc.filters.filtercontext?view=aspnetcore-2.0) derived parameter, which provides access to `HttpContext`.</span></span> <span data-ttu-id="531d4-163">Například [ukázka atributu Implement a filter](#ifa) přidá k odpovědi záhlaví, což nelze provést s konstruktory nebo middleware.</span><span class="sxs-lookup"><span data-stu-id="531d4-163">For example, the [Implement a filter attribute](#ifa) sample adds a header to the response, something that can't be done with constructors or middleware.</span></span>

<span data-ttu-id="531d4-164">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/filter/sample/PageFilter) [(jak stáhnout)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="531d4-164">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/filter/sample/PageFilter) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="531d4-165">Filtry Razor Page poskytují následující metody, které lze použít globálně nebo na úrovni stránky:</span><span class="sxs-lookup"><span data-stu-id="531d4-165">Razor Page filters provide the following methods, which can be applied globally or at the page level:</span></span>

* <span data-ttu-id="531d4-166">Synchronní metody:</span><span class="sxs-lookup"><span data-stu-id="531d4-166">Synchronous methods:</span></span>

  * <span data-ttu-id="531d4-167">[OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0) : Voláno po obslužné rutiny metoda byla vybrána, ale před dojde k vazbě modelu.</span><span class="sxs-lookup"><span data-stu-id="531d4-167">[OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0) : Called after a handler method has been selected, but before model binding occurs.</span></span>
  * <span data-ttu-id="531d4-168">[OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0) : Voláno před spuštěním metody obslužné rutiny po dokončení vazby modelu.</span><span class="sxs-lookup"><span data-stu-id="531d4-168">[OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0) : Called before the handler method executes, after model binding is complete.</span></span>
  * <span data-ttu-id="531d4-169">[OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0) : Voláno po spuštění metody obslužné rutiny před výsledkem akce.</span><span class="sxs-lookup"><span data-stu-id="531d4-169">[OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0) : Called after the handler method executes, before the action result.</span></span>

* <span data-ttu-id="531d4-170">Asynchronní metody:</span><span class="sxs-lookup"><span data-stu-id="531d4-170">Asynchronous methods:</span></span>

  * <span data-ttu-id="531d4-171">[OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0) : Voláno asynchronně po metodě obslužné rutiny, ale dříve, než dojde k vazbě modelu.</span><span class="sxs-lookup"><span data-stu-id="531d4-171">[OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0) : Called asynchronously after the handler method has been selected, but before model binding occurs.</span></span>
  * <span data-ttu-id="531d4-172">[OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0) : Voláno asynchronně před vyvoláním metody obslužné rutiny po dokončení vazby modelu.</span><span class="sxs-lookup"><span data-stu-id="531d4-172">[OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0) : Called asynchronously before the handler method is invoked, after model binding is complete.</span></span>

> [!NOTE]
> <span data-ttu-id="531d4-173">Implementujte **synchronní** nebo asynchronní verzi rozhraní filtru, nikoli obojí.</span><span class="sxs-lookup"><span data-stu-id="531d4-173">Implement **either** the synchronous or the async version of a filter interface, not both.</span></span> <span data-ttu-id="531d4-174">Rozhraní framework nejprve zkontroluje, pokud filtr implementuje asynchronní rozhraní a pokud ano, volá to.</span><span class="sxs-lookup"><span data-stu-id="531d4-174">The framework checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="531d4-175">Pokud ne, volá metody synchronního rozhraní.</span><span class="sxs-lookup"><span data-stu-id="531d4-175">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="531d4-176">Pokud jsou implementovány obě rozhraní, jsou volány pouze asynchronní metody.</span><span class="sxs-lookup"><span data-stu-id="531d4-176">If both interfaces are implemented, only the async methods are called.</span></span> <span data-ttu-id="531d4-177">Stejné pravidlo platí pro lokální změny na stránkách, implementovat synchronní nebo asynchronní verzi přepsání, ne obojí.</span><span class="sxs-lookup"><span data-stu-id="531d4-177">The same rule applies to overrides in pages, implement the synchronous or the async version of the override, not both.</span></span>

## <a name="implement-razor-page-filters-globally"></a><span data-ttu-id="531d4-178">Implementovat filtry Razor Page globálně</span><span class="sxs-lookup"><span data-stu-id="531d4-178">Implement Razor Page filters globally</span></span>

<span data-ttu-id="531d4-179">Následující kód implementuje `IAsyncPageFilter`:</span><span class="sxs-lookup"><span data-stu-id="531d4-179">The following code implements `IAsyncPageFilter`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Filters/SampleAsyncPageFilter.cs?name=snippet1)]

<span data-ttu-id="531d4-180">V předchozím kódu [iLogger](/dotnet/api/microsoft.extensions.logging.ilogger?view=aspnetcore-2.0) není vyžadován.</span><span class="sxs-lookup"><span data-stu-id="531d4-180">In the preceding code, [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger?view=aspnetcore-2.0) is not required.</span></span> <span data-ttu-id="531d4-181">Používá se v ukázce poskytnout informace o trasování pro aplikaci.</span><span class="sxs-lookup"><span data-stu-id="531d4-181">It's used in the sample to provide trace information for the application.</span></span>

<span data-ttu-id="531d4-182">Následující kód umožňuje `SampleAsyncPageFilter` ve `Startup` třídě:</span><span class="sxs-lookup"><span data-stu-id="531d4-182">The following code enables the `SampleAsyncPageFilter` in the `Startup` class:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Startup.cs?name=snippet2&highlight=11)]

<span data-ttu-id="531d4-183">Následující kód ukazuje `Startup` kompletní třídu:</span><span class="sxs-lookup"><span data-stu-id="531d4-183">The following code shows the complete `Startup` class:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Startup.cs?name=snippet1)]

<span data-ttu-id="531d4-184">Následující kód `AddFolderApplicationModelConvention` volá použít `SampleAsyncPageFilter` pouze stránky v */subFolder*:</span><span class="sxs-lookup"><span data-stu-id="531d4-184">The following code calls `AddFolderApplicationModelConvention` to apply the `SampleAsyncPageFilter` to only pages in */subFolder*:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Startup2.cs?name=snippet2)]

<span data-ttu-id="531d4-185">Následující kód implementuje synchronní `IPageFilter`:</span><span class="sxs-lookup"><span data-stu-id="531d4-185">The following code implements the synchronous `IPageFilter`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Filters/SamplePageFilter.cs?name=snippet1)]

<span data-ttu-id="531d4-186">Následující kód umožňuje `SamplePageFilter`:</span><span class="sxs-lookup"><span data-stu-id="531d4-186">The following code enables the `SamplePageFilter`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/StartupSync.cs?name=snippet2&highlight=11)]

## <a name="implement-razor-page-filters-by-overriding-filter-methods"></a><span data-ttu-id="531d4-187">Implementovat filtry Razor Page přepsáním metod filtru</span><span class="sxs-lookup"><span data-stu-id="531d4-187">Implement Razor Page filters by overriding filter methods</span></span>

<span data-ttu-id="531d4-188">Následující kód přepíše synchronní filtry Razor Page:</span><span class="sxs-lookup"><span data-stu-id="531d4-188">The following code overrides the synchronous Razor Page filters:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Pages/Index.cshtml.cs)]

<a name="ifa"></a>

## <a name="implement-a-filter-attribute"></a><span data-ttu-id="531d4-189">Implementace atributu filtru</span><span class="sxs-lookup"><span data-stu-id="531d4-189">Implement a filter attribute</span></span>

<span data-ttu-id="531d4-190">Vestavěný filtr založený na atributech [OnResultExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncresultfilter.onresultexecutionasync?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Filters_IAsyncResultFilter_OnResultExecutionAsync_Microsoft_AspNetCore_Mvc_Filters_ResultExecutingContext_Microsoft_AspNetCore_Mvc_Filters_ResultExecutionDelegate_) filtr může být podtřídy.</span><span class="sxs-lookup"><span data-stu-id="531d4-190">The built-in attribute-based filter [OnResultExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncresultfilter.onresultexecutionasync?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Filters_IAsyncResultFilter_OnResultExecutionAsync_Microsoft_AspNetCore_Mvc_Filters_ResultExecutingContext_Microsoft_AspNetCore_Mvc_Filters_ResultExecutionDelegate_) filter can be subclassed.</span></span> <span data-ttu-id="531d4-191">Následující filtr přidá k odpovědi záhlaví:</span><span class="sxs-lookup"><span data-stu-id="531d4-191">The following filter adds a header to the response:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Filters/AddHeaderAttribute.cs)]

<span data-ttu-id="531d4-192">Následující kód použije `AddHeader` atribut:</span><span class="sxs-lookup"><span data-stu-id="531d4-192">The following code applies the `AddHeader` attribute:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Pages/Contact.cshtml.cs?name=snippet1)]

<span data-ttu-id="531d4-193">Pokyny k přepsání pořadí v [tématu Přepsání výchozího pořadí](xref:mvc/controllers/filters#overriding-the-default-order) naleznete v tématu Přepsání pořadí.</span><span class="sxs-lookup"><span data-stu-id="531d4-193">See [Overriding the default order](xref:mvc/controllers/filters#overriding-the-default-order) for instructions on overriding the order.</span></span>

<span data-ttu-id="531d4-194">Pokyny ke zkratování potrubí filtru z filtru naleznete v tématu [Zrušení a zkratování.](xref:mvc/controllers/filters#cancellation-and-short-circuiting)</span><span class="sxs-lookup"><span data-stu-id="531d4-194">See [Cancellation and short circuiting](xref:mvc/controllers/filters#cancellation-and-short-circuiting) for instructions to short-circuit the filter pipeline from a filter.</span></span> 

<a name="auth"></a>

## <a name="authorize-filter-attribute"></a><span data-ttu-id="531d4-195">Autorizovat atribut filtru</span><span class="sxs-lookup"><span data-stu-id="531d4-195">Authorize filter attribute</span></span>

<span data-ttu-id="531d4-196">Atribut [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) lze použít `PageModel`na :</span><span class="sxs-lookup"><span data-stu-id="531d4-196">The [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) attribute can be applied to a `PageModel`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Pages/ModelWithAuthFilter.cshtml.cs?highlight=7)]

::: moniker-end
