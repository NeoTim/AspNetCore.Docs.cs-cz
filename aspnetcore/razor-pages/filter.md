---
title: Filtrovat metody pro Razor stránky v ASP.NET Core
author: Rick-Anderson
description: Naučte se vytvářet metody filtru pro Razor stránky v ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 2/18/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: razor-pages/filter
ms.openlocfilehash: 68962d5a3a49e52510d72899e7dead2c1983d8b6
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775515"
---
# <a name="filter-methods-for-razor-pages-in-aspnet-core"></a><span data-ttu-id="8537b-103">Filtrovat metody pro Razor stránky v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8537b-103">Filter methods for Razor Pages in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="8537b-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="8537b-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

Razor<span data-ttu-id="8537b-105">Filtry stránky [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) a [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) umožňují Razor , aby stránky spouštěly kód před a Razor po spuštění obslužné rutiny stránky.</span><span class="sxs-lookup"><span data-stu-id="8537b-105"> Page filters [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) and [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) allow Razor Pages to run code before and after a Razor Page handler is run.</span></span> Razor<span data-ttu-id="8537b-106">Filtry stránky jsou podobné [filtrům akcí ASP.NET Core MVC](xref:mvc/controllers/filters#action-filters), s výjimkou toho, že je nelze použít na jednotlivé metody obslužné rutiny stránky.</span><span class="sxs-lookup"><span data-stu-id="8537b-106"> Page filters are similar to [ASP.NET Core MVC action filters](xref:mvc/controllers/filters#action-filters), except they can't be applied to individual page handler methods.</span></span>

Razor<span data-ttu-id="8537b-107">Filtry stránky:</span><span class="sxs-lookup"><span data-stu-id="8537b-107"> Page filters:</span></span>

* <span data-ttu-id="8537b-108">Spusťte kód po výběru metody obslužné rutiny, ale před tím, než dojde k vazbě modelu.</span><span class="sxs-lookup"><span data-stu-id="8537b-108">Run code after a handler method has been selected, but before model binding occurs.</span></span>
* <span data-ttu-id="8537b-109">Po dokončení vazby modelu spusťte kód před spuštěním obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="8537b-109">Run code before the handler method executes, after model binding is complete.</span></span>
* <span data-ttu-id="8537b-110">Spusťte kód poté, co se spustí metoda obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="8537b-110">Run code after the handler method executes.</span></span>
* <span data-ttu-id="8537b-111">Může být implementováno na stránce nebo globálně.</span><span class="sxs-lookup"><span data-stu-id="8537b-111">Can be implemented on a page or globally.</span></span>
* <span data-ttu-id="8537b-112">Nelze použít na konkrétní metody obslužné rutiny stránky.</span><span class="sxs-lookup"><span data-stu-id="8537b-112">Cannot be applied to specific page handler methods.</span></span>
* <span data-ttu-id="8537b-113">Může mít závislosti konstruktoru vyplněné [vstřikem závislosti](xref:fundamentals/dependency-injection) (di).</span><span class="sxs-lookup"><span data-stu-id="8537b-113">Can have constructor dependencies populated by [Dependency Injection](xref:fundamentals/dependency-injection) (DI).</span></span> <span data-ttu-id="8537b-114">Další informace najdete v tématu [ServiceFilterAttribute](/aspnet/core/mvc/controllers/filters#servicefilterattribute) a [TypeFilterAttribute](/aspnet/core/mvc/controllers/filters#typefilterattribute).</span><span class="sxs-lookup"><span data-stu-id="8537b-114">For more information, see [ServiceFilterAttribute](/aspnet/core/mvc/controllers/filters#servicefilterattribute) and [TypeFilterAttribute](/aspnet/core/mvc/controllers/filters#typefilterattribute).</span></span>

<span data-ttu-id="8537b-115">I když konstruktory stránky a middleware umožňují spouštění vlastního kódu před spuštěním metody obslužné rutiny, pouze Razor filtry stránky <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.HttpContext> umožňují přístup a stránku.</span><span class="sxs-lookup"><span data-stu-id="8537b-115">While page constructors and middleware enable executing custom code before a handler method executes, only Razor Page filters enable access to <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.HttpContext> and the page.</span></span> <span data-ttu-id="8537b-116">Middleware má přístup k `HttpContext`, ale ne k "kontextu stránky".</span><span class="sxs-lookup"><span data-stu-id="8537b-116">Middleware has access to the `HttpContext`, but not to the "page context".</span></span> <span data-ttu-id="8537b-117">Filtry mají <xref:Microsoft.AspNetCore.Mvc.Filters.FilterContext> odvozený parametr, který poskytuje přístup k `HttpContext`.</span><span class="sxs-lookup"><span data-stu-id="8537b-117">Filters have a <xref:Microsoft.AspNetCore.Mvc.Filters.FilterContext> derived parameter, which provides access to `HttpContext`.</span></span> <span data-ttu-id="8537b-118">Například Implementace ukázkového [atributu Filter](#ifa) přidá hlavičku do odpovědi, něco, co nelze provést s konstruktory nebo middlewaru.</span><span class="sxs-lookup"><span data-stu-id="8537b-118">For example, the [Implement a filter attribute](#ifa) sample adds a header to the response, something that can't be done with constructors or middleware.</span></span>

<span data-ttu-id="8537b-119">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/filter/3.1sample) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="8537b-119">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/filter/3.1sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

Razor<span data-ttu-id="8537b-120">Filtry stránek poskytují následující metody, které lze použít globálně nebo na úrovni stránky:</span><span class="sxs-lookup"><span data-stu-id="8537b-120"> Page filters provide the following methods, which can be applied globally or at the page level:</span></span>

* <span data-ttu-id="8537b-121">Synchronní metody:</span><span class="sxs-lookup"><span data-stu-id="8537b-121">Synchronous methods:</span></span>

  * <span data-ttu-id="8537b-122">[OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0) : volá se po výběru metody obslužné rutiny, ale před tím, než dojde k vazbě modelu.</span><span class="sxs-lookup"><span data-stu-id="8537b-122">[OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0) : Called after a handler method has been selected, but before model binding occurs.</span></span>
  * <span data-ttu-id="8537b-123">[OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0) : volá se před spuštěním metody obslužné rutiny po dokončení vazby modelu.</span><span class="sxs-lookup"><span data-stu-id="8537b-123">[OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0) : Called before the handler method executes, after model binding is complete.</span></span>
  * <span data-ttu-id="8537b-124">[OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0) : volá se, když se spustí obslužná rutina, před výsledek akce.</span><span class="sxs-lookup"><span data-stu-id="8537b-124">[OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0) : Called after the handler method executes, before the action result.</span></span>

* <span data-ttu-id="8537b-125">Asynchronní metody:</span><span class="sxs-lookup"><span data-stu-id="8537b-125">Asynchronous methods:</span></span>

  * <span data-ttu-id="8537b-126">[OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0) : používá se asynchronně po výběru obslužné rutiny, ale před tím, než dojde k vazbě modelu.</span><span class="sxs-lookup"><span data-stu-id="8537b-126">[OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0) : Called asynchronously after the handler method has been selected, but before model binding occurs.</span></span>
  * <span data-ttu-id="8537b-127">[OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0) : používá se asynchronně před vyvoláním metody obslužné rutiny po dokončení vazby modelu.</span><span class="sxs-lookup"><span data-stu-id="8537b-127">[OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0) : Called asynchronously before the handler method is invoked, after model binding is complete.</span></span>

<span data-ttu-id="8537b-128">Implementujte **buď** synchronní, nebo asynchronní verzi rozhraní filtru, **nikoli** obojí.</span><span class="sxs-lookup"><span data-stu-id="8537b-128">Implement **either** the synchronous or the async version of a filter interface, **not** both.</span></span> <span data-ttu-id="8537b-129">Rozhraní nejprve kontroluje, zda filtr implementuje asynchronní rozhraní, a pokud ano, zavolá tuto metodu.</span><span class="sxs-lookup"><span data-stu-id="8537b-129">The framework checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="8537b-130">V takovém případě volá metody synchronního rozhraní.</span><span class="sxs-lookup"><span data-stu-id="8537b-130">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="8537b-131">Pokud jsou implementována obě rozhraní, jsou zavolány pouze asynchronní metody.</span><span class="sxs-lookup"><span data-stu-id="8537b-131">If both interfaces are implemented, only the async methods are called.</span></span> <span data-ttu-id="8537b-132">Stejné pravidlo se vztahuje na přepsání na stránkách, implementuje synchronní nebo asynchronní verzi přepsání, nikoli obojí.</span><span class="sxs-lookup"><span data-stu-id="8537b-132">The same rule applies to overrides in pages, implement the synchronous or the async version of the override, not both.</span></span>

## <a name="implement-razor-page-filters-globally"></a><span data-ttu-id="8537b-133">Globálně Razor implementovat filtry stránek</span><span class="sxs-lookup"><span data-stu-id="8537b-133">Implement Razor Page filters globally</span></span>

<span data-ttu-id="8537b-134">Následující kód implementuje `IAsyncPageFilter`:</span><span class="sxs-lookup"><span data-stu-id="8537b-134">The following code implements `IAsyncPageFilter`:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Filters/SampleAsyncPageFilter.cs?name=snippet1)]

<span data-ttu-id="8537b-135">V předchozím kódu `ProcessUserAgent.Write` je uživatelem zadaný kód, který pracuje s řetězcem uživatelského agenta.</span><span class="sxs-lookup"><span data-stu-id="8537b-135">In the preceding code, `ProcessUserAgent.Write` is user supplied code that works with the user agent string.</span></span>

<span data-ttu-id="8537b-136">Následující kód povoluje `SampleAsyncPageFilter` ve `Startup` třídě:</span><span class="sxs-lookup"><span data-stu-id="8537b-136">The following code enables the `SampleAsyncPageFilter` in the `Startup` class:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Startup.cs?name=snippet2)]

<span data-ttu-id="8537b-137">Následující kód volá <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> pro použití pouze `SampleAsyncPageFilter` na stránky v */Movies*:</span><span class="sxs-lookup"><span data-stu-id="8537b-137">The following code calls <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> to apply the `SampleAsyncPageFilter` to only pages in */Movies*:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Startup2.cs?name=snippet2)]

<span data-ttu-id="8537b-138">Následující kód implementuje synchronní `IPageFilter`:</span><span class="sxs-lookup"><span data-stu-id="8537b-138">The following code implements the synchronous `IPageFilter`:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Filters/SamplePageFilter.cs?name=snippet1)]

<span data-ttu-id="8537b-139">Následující kód umožňuje `SamplePageFilter`:</span><span class="sxs-lookup"><span data-stu-id="8537b-139">The following code enables the `SamplePageFilter`:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/StartupSync.cs?name=snippet2)]

## <a name="implement-razor-page-filters-by-overriding-filter-methods"></a><span data-ttu-id="8537b-140">Implementace Razor filtrů stránek přepsáním metod filtrování</span><span class="sxs-lookup"><span data-stu-id="8537b-140">Implement Razor Page filters by overriding filter methods</span></span>

<span data-ttu-id="8537b-141">Následující kód Přepisuje filtry asynchronní Razor stránky:</span><span class="sxs-lookup"><span data-stu-id="8537b-141">The following code overrides the asynchronous Razor Page filters:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Pages/Index.cshtml.cs?name=snippet)]

<a name="ifa"></a>

## <a name="implement-a-filter-attribute"></a><span data-ttu-id="8537b-142">Implementace atributu filtru</span><span class="sxs-lookup"><span data-stu-id="8537b-142">Implement a filter attribute</span></span>

<span data-ttu-id="8537b-143">Předdefinovaný filtr filtru <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter.OnResultExecutionAsync*> založený na atributech může být podtříd.</span><span class="sxs-lookup"><span data-stu-id="8537b-143">The built-in attribute-based filter <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter.OnResultExecutionAsync*> filter can be subclassed.</span></span> <span data-ttu-id="8537b-144">Následující filtr Přidá hlavičku k odpovědi:</span><span class="sxs-lookup"><span data-stu-id="8537b-144">The following filter adds a header to the response:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Filters/AddHeaderAttribute.cs)]

<span data-ttu-id="8537b-145">Následující kód použije `AddHeader` atribut:</span><span class="sxs-lookup"><span data-stu-id="8537b-145">The following code applies the `AddHeader` attribute:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Pages/Movies/Test.cshtml.cs)]

<span data-ttu-id="8537b-146">K prohlédnutí hlaviček použijte nástroj, jako je například nástroj pro vývojáře v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="8537b-146">Use a tool such as the browser developer tools to examine the headers.</span></span> <span data-ttu-id="8537b-147">V části **hlavičky odpovědi** `author: Rick` se zobrazí.</span><span class="sxs-lookup"><span data-stu-id="8537b-147">Under **Response Headers**, `author: Rick` is displayed.</span></span>

<span data-ttu-id="8537b-148">Pokyny k přepsání objednávky najdete v tématu [přepisování výchozího pořadí](xref:mvc/controllers/filters#overriding-the-default-order) .</span><span class="sxs-lookup"><span data-stu-id="8537b-148">See [Overriding the default order](xref:mvc/controllers/filters#overriding-the-default-order) for instructions on overriding the order.</span></span>

<span data-ttu-id="8537b-149">Pokyny k krátkodobému zaokruhu kanálu filtrování z filtru najdete v tématu [zrušení a krátké vytvoření okruhu](xref:mvc/controllers/filters#cancellation-and-short-circuiting) .</span><span class="sxs-lookup"><span data-stu-id="8537b-149">See [Cancellation and short circuiting](xref:mvc/controllers/filters#cancellation-and-short-circuiting) for instructions to short-circuit the filter pipeline from a filter.</span></span>

<a name="auth"></a>

## <a name="authorize-filter-attribute"></a><span data-ttu-id="8537b-150">Autorizovat atribut filtru</span><span class="sxs-lookup"><span data-stu-id="8537b-150">Authorize filter attribute</span></span>

<span data-ttu-id="8537b-151">Atribut [autorizace](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) lze použít pro `PageModel`:</span><span class="sxs-lookup"><span data-stu-id="8537b-151">The [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) attribute can be applied to a `PageModel`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Pages/ModelWithAuthFilter.cshtml.cs?highlight=7)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="8537b-152">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="8537b-152">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

Razor<span data-ttu-id="8537b-153">Filtry stránky [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) a [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) umožňují Razor , aby stránky spouštěly kód před a Razor po spuštění obslužné rutiny stránky.</span><span class="sxs-lookup"><span data-stu-id="8537b-153"> Page filters [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) and [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) allow Razor Pages to run code before and after a Razor Page handler is run.</span></span> Razor<span data-ttu-id="8537b-154">Filtry stránky jsou podobné [filtrům akcí ASP.NET Core MVC](xref:mvc/controllers/filters#action-filters), s výjimkou toho, že je nelze použít na jednotlivé metody obslužné rutiny stránky.</span><span class="sxs-lookup"><span data-stu-id="8537b-154"> Page filters are similar to [ASP.NET Core MVC action filters](xref:mvc/controllers/filters#action-filters), except they can't be applied to individual page handler methods.</span></span>

Razor<span data-ttu-id="8537b-155">Filtry stránky:</span><span class="sxs-lookup"><span data-stu-id="8537b-155"> Page filters:</span></span>

* <span data-ttu-id="8537b-156">Spusťte kód po výběru metody obslužné rutiny, ale před tím, než dojde k vazbě modelu.</span><span class="sxs-lookup"><span data-stu-id="8537b-156">Run code after a handler method has been selected, but before model binding occurs.</span></span>
* <span data-ttu-id="8537b-157">Po dokončení vazby modelu spusťte kód před spuštěním obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="8537b-157">Run code before the handler method executes, after model binding is complete.</span></span>
* <span data-ttu-id="8537b-158">Spusťte kód poté, co se spustí metoda obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="8537b-158">Run code after the handler method executes.</span></span>
* <span data-ttu-id="8537b-159">Může být implementováno na stránce nebo globálně.</span><span class="sxs-lookup"><span data-stu-id="8537b-159">Can be implemented on a page or globally.</span></span>
* <span data-ttu-id="8537b-160">Nelze použít na konkrétní metody obslužné rutiny stránky.</span><span class="sxs-lookup"><span data-stu-id="8537b-160">Cannot be applied to specific page handler methods.</span></span>

<span data-ttu-id="8537b-161">Kód lze spustit před spuštěním obslužné rutiny pomocí konstruktoru stránky nebo middlewaru, ale pouze Razor filtry stránky mají přístup k [HttpContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.httpcontext?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_HttpContext).</span><span class="sxs-lookup"><span data-stu-id="8537b-161">Code can be run before a handler method executes using the page constructor or middleware, but only Razor Page filters have access to [HttpContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.httpcontext?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_HttpContext).</span></span> <span data-ttu-id="8537b-162">Filtry mají [FilterContext](/dotnet/api/microsoft.aspnetcore.mvc.filters.filtercontext?view=aspnetcore-2.0) odvozený parametr, který poskytuje přístup k `HttpContext`.</span><span class="sxs-lookup"><span data-stu-id="8537b-162">Filters have a [FilterContext](/dotnet/api/microsoft.aspnetcore.mvc.filters.filtercontext?view=aspnetcore-2.0) derived parameter, which provides access to `HttpContext`.</span></span> <span data-ttu-id="8537b-163">Například Implementace ukázkového [atributu Filter](#ifa) přidá hlavičku do odpovědi, něco, co nelze provést s konstruktory nebo middlewaru.</span><span class="sxs-lookup"><span data-stu-id="8537b-163">For example, the [Implement a filter attribute](#ifa) sample adds a header to the response, something that can't be done with constructors or middleware.</span></span>

<span data-ttu-id="8537b-164">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/filter/sample/PageFilter) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="8537b-164">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/filter/sample/PageFilter) ([how to download](xref:index#how-to-download-a-sample))</span></span>

Razor<span data-ttu-id="8537b-165">Filtry stránek poskytují následující metody, které lze použít globálně nebo na úrovni stránky:</span><span class="sxs-lookup"><span data-stu-id="8537b-165"> Page filters provide the following methods, which can be applied globally or at the page level:</span></span>

* <span data-ttu-id="8537b-166">Synchronní metody:</span><span class="sxs-lookup"><span data-stu-id="8537b-166">Synchronous methods:</span></span>

  * <span data-ttu-id="8537b-167">[OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0) : volá se po výběru metody obslužné rutiny, ale před tím, než dojde k vazbě modelu.</span><span class="sxs-lookup"><span data-stu-id="8537b-167">[OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0) : Called after a handler method has been selected, but before model binding occurs.</span></span>
  * <span data-ttu-id="8537b-168">[OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0) : volá se před spuštěním metody obslužné rutiny po dokončení vazby modelu.</span><span class="sxs-lookup"><span data-stu-id="8537b-168">[OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0) : Called before the handler method executes, after model binding is complete.</span></span>
  * <span data-ttu-id="8537b-169">[OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0) : volá se, když se spustí obslužná rutina, před výsledek akce.</span><span class="sxs-lookup"><span data-stu-id="8537b-169">[OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0) : Called after the handler method executes, before the action result.</span></span>

* <span data-ttu-id="8537b-170">Asynchronní metody:</span><span class="sxs-lookup"><span data-stu-id="8537b-170">Asynchronous methods:</span></span>

  * <span data-ttu-id="8537b-171">[OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0) : používá se asynchronně po výběru obslužné rutiny, ale před tím, než dojde k vazbě modelu.</span><span class="sxs-lookup"><span data-stu-id="8537b-171">[OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0) : Called asynchronously after the handler method has been selected, but before model binding occurs.</span></span>
  * <span data-ttu-id="8537b-172">[OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0) : používá se asynchronně před vyvoláním metody obslužné rutiny po dokončení vazby modelu.</span><span class="sxs-lookup"><span data-stu-id="8537b-172">[OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0) : Called asynchronously before the handler method is invoked, after model binding is complete.</span></span>

> [!NOTE]
> <span data-ttu-id="8537b-173">Implementujte **buď** synchronní, nebo asynchronní verzi rozhraní filtru, nikoli obojí.</span><span class="sxs-lookup"><span data-stu-id="8537b-173">Implement **either** the synchronous or the async version of a filter interface, not both.</span></span> <span data-ttu-id="8537b-174">Rozhraní nejprve kontroluje, zda filtr implementuje asynchronní rozhraní, a pokud ano, zavolá tuto metodu.</span><span class="sxs-lookup"><span data-stu-id="8537b-174">The framework checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="8537b-175">V takovém případě volá metody synchronního rozhraní.</span><span class="sxs-lookup"><span data-stu-id="8537b-175">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="8537b-176">Pokud jsou implementována obě rozhraní, jsou zavolány pouze asynchronní metody.</span><span class="sxs-lookup"><span data-stu-id="8537b-176">If both interfaces are implemented, only the async methods are called.</span></span> <span data-ttu-id="8537b-177">Stejné pravidlo se vztahuje na přepsání na stránkách, implementuje synchronní nebo asynchronní verzi přepsání, nikoli obojí.</span><span class="sxs-lookup"><span data-stu-id="8537b-177">The same rule applies to overrides in pages, implement the synchronous or the async version of the override, not both.</span></span>

## <a name="implement-razor-page-filters-globally"></a><span data-ttu-id="8537b-178">Globálně Razor implementovat filtry stránek</span><span class="sxs-lookup"><span data-stu-id="8537b-178">Implement Razor Page filters globally</span></span>

<span data-ttu-id="8537b-179">Následující kód implementuje `IAsyncPageFilter`:</span><span class="sxs-lookup"><span data-stu-id="8537b-179">The following code implements `IAsyncPageFilter`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Filters/SampleAsyncPageFilter.cs?name=snippet1)]

<span data-ttu-id="8537b-180">V předchozím kódu není [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger?view=aspnetcore-2.0) požadováno.</span><span class="sxs-lookup"><span data-stu-id="8537b-180">In the preceding code, [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger?view=aspnetcore-2.0) is not required.</span></span> <span data-ttu-id="8537b-181">Používá se v ukázce k poskytnutí informací o trasování pro aplikaci.</span><span class="sxs-lookup"><span data-stu-id="8537b-181">It's used in the sample to provide trace information for the application.</span></span>

<span data-ttu-id="8537b-182">Následující kód povoluje `SampleAsyncPageFilter` ve `Startup` třídě:</span><span class="sxs-lookup"><span data-stu-id="8537b-182">The following code enables the `SampleAsyncPageFilter` in the `Startup` class:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Startup.cs?name=snippet2&highlight=11)]

<span data-ttu-id="8537b-183">Následující kód ukazuje úplnou `Startup` třídu:</span><span class="sxs-lookup"><span data-stu-id="8537b-183">The following code shows the complete `Startup` class:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Startup.cs?name=snippet1)]

<span data-ttu-id="8537b-184">Následující kód volá `AddFolderApplicationModelConvention` pro použití pouze `SampleAsyncPageFilter` na stránky v */subFolder*:</span><span class="sxs-lookup"><span data-stu-id="8537b-184">The following code calls `AddFolderApplicationModelConvention` to apply the `SampleAsyncPageFilter` to only pages in */subFolder*:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Startup2.cs?name=snippet2)]

<span data-ttu-id="8537b-185">Následující kód implementuje synchronní `IPageFilter`:</span><span class="sxs-lookup"><span data-stu-id="8537b-185">The following code implements the synchronous `IPageFilter`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Filters/SamplePageFilter.cs?name=snippet1)]

<span data-ttu-id="8537b-186">Následující kód umožňuje `SamplePageFilter`:</span><span class="sxs-lookup"><span data-stu-id="8537b-186">The following code enables the `SamplePageFilter`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/StartupSync.cs?name=snippet2&highlight=11)]

## <a name="implement-razor-page-filters-by-overriding-filter-methods"></a><span data-ttu-id="8537b-187">Implementace Razor filtrů stránek přepsáním metod filtrování</span><span class="sxs-lookup"><span data-stu-id="8537b-187">Implement Razor Page filters by overriding filter methods</span></span>

<span data-ttu-id="8537b-188">Následující kód Přepisuje filtry synchronní Razor stránky:</span><span class="sxs-lookup"><span data-stu-id="8537b-188">The following code overrides the synchronous Razor Page filters:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Pages/Index.cshtml.cs)]

<a name="ifa"></a>

## <a name="implement-a-filter-attribute"></a><span data-ttu-id="8537b-189">Implementace atributu filtru</span><span class="sxs-lookup"><span data-stu-id="8537b-189">Implement a filter attribute</span></span>

<span data-ttu-id="8537b-190">Vestavěný filtr [OnResultExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncresultfilter.onresultexecutionasync?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Filters_IAsyncResultFilter_OnResultExecutionAsync_Microsoft_AspNetCore_Mvc_Filters_ResultExecutingContext_Microsoft_AspNetCore_Mvc_Filters_ResultExecutionDelegate_) filtru založený na atributech může být podtříd.</span><span class="sxs-lookup"><span data-stu-id="8537b-190">The built-in attribute-based filter [OnResultExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncresultfilter.onresultexecutionasync?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Filters_IAsyncResultFilter_OnResultExecutionAsync_Microsoft_AspNetCore_Mvc_Filters_ResultExecutingContext_Microsoft_AspNetCore_Mvc_Filters_ResultExecutionDelegate_) filter can be subclassed.</span></span> <span data-ttu-id="8537b-191">Následující filtr Přidá hlavičku k odpovědi:</span><span class="sxs-lookup"><span data-stu-id="8537b-191">The following filter adds a header to the response:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Filters/AddHeaderAttribute.cs)]

<span data-ttu-id="8537b-192">Následující kód použije `AddHeader` atribut:</span><span class="sxs-lookup"><span data-stu-id="8537b-192">The following code applies the `AddHeader` attribute:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Pages/Contact.cshtml.cs?name=snippet1)]

<span data-ttu-id="8537b-193">Pokyny k přepsání objednávky najdete v tématu [přepisování výchozího pořadí](xref:mvc/controllers/filters#overriding-the-default-order) .</span><span class="sxs-lookup"><span data-stu-id="8537b-193">See [Overriding the default order](xref:mvc/controllers/filters#overriding-the-default-order) for instructions on overriding the order.</span></span>

<span data-ttu-id="8537b-194">Pokyny k krátkodobému zaokruhu kanálu filtrování z filtru najdete v tématu [zrušení a krátké vytvoření okruhu](xref:mvc/controllers/filters#cancellation-and-short-circuiting) .</span><span class="sxs-lookup"><span data-stu-id="8537b-194">See [Cancellation and short circuiting](xref:mvc/controllers/filters#cancellation-and-short-circuiting) for instructions to short-circuit the filter pipeline from a filter.</span></span> 

<a name="auth"></a>

## <a name="authorize-filter-attribute"></a><span data-ttu-id="8537b-195">Autorizovat atribut filtru</span><span class="sxs-lookup"><span data-stu-id="8537b-195">Authorize filter attribute</span></span>

<span data-ttu-id="8537b-196">Atribut [autorizace](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) lze použít pro `PageModel`:</span><span class="sxs-lookup"><span data-stu-id="8537b-196">The [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) attribute can be applied to a `PageModel`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Pages/ModelWithAuthFilter.cshtml.cs?highlight=7)]

::: moniker-end
