---
title: Filtrovat metody pro Razor stránky v ASP.NET Core
author: Rick-Anderson
description: Naučte se vytvářet metody filtru pro Razor stránky v ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 2/18/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: razor-pages/filter
ms.openlocfilehash: b6d6585c0cbd52715c4192d4ab3bee756dbb41b3
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2020
ms.locfileid: "88635044"
---
# <a name="filter-methods-for-no-locrazor-pages-in-aspnet-core"></a><span data-ttu-id="53618-103">Filtrovat metody pro Razor stránky v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="53618-103">Filter methods for Razor Pages in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="53618-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="53618-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="53618-105">Razor Filtry stránky [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) a [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) umožňují, Razor aby stránky spouštěly kód před a po Razor spuštění obslužné rutiny stránky.</span><span class="sxs-lookup"><span data-stu-id="53618-105">Razor Page filters [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) and [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) allow Razor Pages to run code before and after a Razor Page handler is run.</span></span> <span data-ttu-id="53618-106">Razor Filtry stránky jsou podobné [filtrům akcí ASP.NET Core MVC](xref:mvc/controllers/filters#action-filters), s výjimkou toho, že je nelze použít na jednotlivé metody obslužné rutiny stránky.</span><span class="sxs-lookup"><span data-stu-id="53618-106">Razor Page filters are similar to [ASP.NET Core MVC action filters](xref:mvc/controllers/filters#action-filters), except they can't be applied to individual page handler methods.</span></span>

<span data-ttu-id="53618-107">Razor Filtry stránky:</span><span class="sxs-lookup"><span data-stu-id="53618-107">Razor Page filters:</span></span>

* <span data-ttu-id="53618-108">Spusťte kód po výběru metody obslužné rutiny, ale před tím, než dojde k vazbě modelu.</span><span class="sxs-lookup"><span data-stu-id="53618-108">Run code after a handler method has been selected, but before model binding occurs.</span></span>
* <span data-ttu-id="53618-109">Po dokončení vazby modelu spusťte kód před spuštěním obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="53618-109">Run code before the handler method executes, after model binding is complete.</span></span>
* <span data-ttu-id="53618-110">Spusťte kód poté, co se spustí metoda obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="53618-110">Run code after the handler method executes.</span></span>
* <span data-ttu-id="53618-111">Může být implementováno na stránce nebo globálně.</span><span class="sxs-lookup"><span data-stu-id="53618-111">Can be implemented on a page or globally.</span></span>
* <span data-ttu-id="53618-112">Nelze použít na konkrétní metody obslužné rutiny stránky.</span><span class="sxs-lookup"><span data-stu-id="53618-112">Cannot be applied to specific page handler methods.</span></span>
* <span data-ttu-id="53618-113">Může mít závislosti konstruktoru vyplněné [vstřikem závislosti](xref:fundamentals/dependency-injection) (di).</span><span class="sxs-lookup"><span data-stu-id="53618-113">Can have constructor dependencies populated by [Dependency Injection](xref:fundamentals/dependency-injection) (DI).</span></span> <span data-ttu-id="53618-114">Další informace najdete v tématu [ServiceFilterAttribute](/aspnet/core/mvc/controllers/filters#servicefilterattribute) a [TypeFilterAttribute](/aspnet/core/mvc/controllers/filters#typefilterattribute).</span><span class="sxs-lookup"><span data-stu-id="53618-114">For more information, see [ServiceFilterAttribute](/aspnet/core/mvc/controllers/filters#servicefilterattribute) and [TypeFilterAttribute](/aspnet/core/mvc/controllers/filters#typefilterattribute).</span></span>

<span data-ttu-id="53618-115">I když konstruktory stránky a middleware umožňují spouštění vlastního kódu před spuštěním metody obslužné rutiny, pouze Razor filtry stránky umožňují přístup <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.HttpContext> a stránku.</span><span class="sxs-lookup"><span data-stu-id="53618-115">While page constructors and middleware enable executing custom code before a handler method executes, only Razor Page filters enable access to <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.HttpContext> and the page.</span></span> <span data-ttu-id="53618-116">Middleware má přístup k `HttpContext` , ale ne k "kontextu stránky".</span><span class="sxs-lookup"><span data-stu-id="53618-116">Middleware has access to the `HttpContext`, but not to the "page context".</span></span> <span data-ttu-id="53618-117">Filtry mají <xref:Microsoft.AspNetCore.Mvc.Filters.FilterContext> odvozený parametr, který poskytuje přístup k `HttpContext` .</span><span class="sxs-lookup"><span data-stu-id="53618-117">Filters have a <xref:Microsoft.AspNetCore.Mvc.Filters.FilterContext> derived parameter, which provides access to `HttpContext`.</span></span> <span data-ttu-id="53618-118">Zde je ukázka pro filtr stránky: [implementujte atribut filtru](#ifa) , který do odpovědi přidá hlavičku, něco, co nelze provést s konstruktory nebo middleware.</span><span class="sxs-lookup"><span data-stu-id="53618-118">Here's a sample for a page filter: [Implement a filter attribute](#ifa) that adds a header to the response, something that can't be done with constructors or middleware.</span></span> <span data-ttu-id="53618-119">Přístup k kontextu stránky, který zahrnuje přístup k instancím stránky a jejímu modelu, je k dispozici pouze při provádění filtrů, obslužných rutin nebo textu Razor stránky.</span><span class="sxs-lookup"><span data-stu-id="53618-119">Access to the page context, which includes access to the instances of the page and it's model, are only available when executing filters, handlers, or the body of a Razor Page.</span></span>

<span data-ttu-id="53618-120">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/filter/3.1sample) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="53618-120">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/filter/3.1sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="53618-121">Razor Filtry stránek poskytují následující metody, které lze použít globálně nebo na úrovni stránky:</span><span class="sxs-lookup"><span data-stu-id="53618-121">Razor Page filters provide the following methods, which can be applied globally or at the page level:</span></span>

* <span data-ttu-id="53618-122">Synchronní metody:</span><span class="sxs-lookup"><span data-stu-id="53618-122">Synchronous methods:</span></span>

  * <span data-ttu-id="53618-123">[OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0) : volá se po výběru metody obslužné rutiny, ale před tím, než dojde k vazbě modelu.</span><span class="sxs-lookup"><span data-stu-id="53618-123">[OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0) : Called after a handler method has been selected, but before model binding occurs.</span></span>
  * <span data-ttu-id="53618-124">[OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0) : volá se před spuštěním metody obslužné rutiny po dokončení vazby modelu.</span><span class="sxs-lookup"><span data-stu-id="53618-124">[OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0) : Called before the handler method executes, after model binding is complete.</span></span>
  * <span data-ttu-id="53618-125">[OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0) : volá se, když se spustí obslužná rutina, před výsledek akce.</span><span class="sxs-lookup"><span data-stu-id="53618-125">[OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0) : Called after the handler method executes, before the action result.</span></span>

* <span data-ttu-id="53618-126">Asynchronní metody:</span><span class="sxs-lookup"><span data-stu-id="53618-126">Asynchronous methods:</span></span>

  * <span data-ttu-id="53618-127">[OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0) : používá se asynchronně po výběru obslužné rutiny, ale před tím, než dojde k vazbě modelu.</span><span class="sxs-lookup"><span data-stu-id="53618-127">[OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0) : Called asynchronously after the handler method has been selected, but before model binding occurs.</span></span>
  * <span data-ttu-id="53618-128">[OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0) : používá se asynchronně před vyvoláním metody obslužné rutiny po dokončení vazby modelu.</span><span class="sxs-lookup"><span data-stu-id="53618-128">[OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0) : Called asynchronously before the handler method is invoked, after model binding is complete.</span></span>

<span data-ttu-id="53618-129">Implementujte **buď** synchronní, nebo asynchronní verzi rozhraní filtru, **nikoli** obojí.</span><span class="sxs-lookup"><span data-stu-id="53618-129">Implement **either** the synchronous or the async version of a filter interface, **not** both.</span></span> <span data-ttu-id="53618-130">Rozhraní nejprve kontroluje, zda filtr implementuje asynchronní rozhraní, a pokud ano, zavolá tuto metodu.</span><span class="sxs-lookup"><span data-stu-id="53618-130">The framework checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="53618-131">V takovém případě volá metody synchronního rozhraní.</span><span class="sxs-lookup"><span data-stu-id="53618-131">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="53618-132">Pokud jsou implementována obě rozhraní, jsou zavolány pouze asynchronní metody.</span><span class="sxs-lookup"><span data-stu-id="53618-132">If both interfaces are implemented, only the async methods are called.</span></span> <span data-ttu-id="53618-133">Stejné pravidlo se vztahuje na přepsání na stránkách, implementuje synchronní nebo asynchronní verzi přepsání, nikoli obojí.</span><span class="sxs-lookup"><span data-stu-id="53618-133">The same rule applies to overrides in pages, implement the synchronous or the async version of the override, not both.</span></span>

## <a name="implement-no-locrazor-page-filters-globally"></a><span data-ttu-id="53618-134">RazorGlobálně implementovat filtry stránek</span><span class="sxs-lookup"><span data-stu-id="53618-134">Implement Razor Page filters globally</span></span>

<span data-ttu-id="53618-135">Následující kód implementuje `IAsyncPageFilter` :</span><span class="sxs-lookup"><span data-stu-id="53618-135">The following code implements `IAsyncPageFilter`:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Filters/SampleAsyncPageFilter.cs?name=snippet1)]

<span data-ttu-id="53618-136">V předchozím kódu `ProcessUserAgent.Write` je uživatelem zadaný kód, který pracuje s řetězcem uživatelského agenta.</span><span class="sxs-lookup"><span data-stu-id="53618-136">In the preceding code, `ProcessUserAgent.Write` is user supplied code that works with the user agent string.</span></span>

<span data-ttu-id="53618-137">Následující kód povoluje `SampleAsyncPageFilter` ve `Startup` třídě:</span><span class="sxs-lookup"><span data-stu-id="53618-137">The following code enables the `SampleAsyncPageFilter` in the `Startup` class:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Startup.cs?name=snippet2)]

<span data-ttu-id="53618-138">Následující kód volá <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> pro použití `SampleAsyncPageFilter` pouze na stránky v */Movies*:</span><span class="sxs-lookup"><span data-stu-id="53618-138">The following code calls <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> to apply the `SampleAsyncPageFilter` to only pages in */Movies*:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Startup2.cs?name=snippet2)]

<span data-ttu-id="53618-139">Následující kód implementuje synchronní `IPageFilter` :</span><span class="sxs-lookup"><span data-stu-id="53618-139">The following code implements the synchronous `IPageFilter`:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Filters/SamplePageFilter.cs?name=snippet1)]

<span data-ttu-id="53618-140">Následující kód umožňuje `SamplePageFilter` :</span><span class="sxs-lookup"><span data-stu-id="53618-140">The following code enables the `SamplePageFilter`:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/StartupSync.cs?name=snippet2)]

## <a name="implement-no-locrazor-page-filters-by-overriding-filter-methods"></a><span data-ttu-id="53618-141">Implementace Razor filtrů stránek přepsáním metod filtrování</span><span class="sxs-lookup"><span data-stu-id="53618-141">Implement Razor Page filters by overriding filter methods</span></span>

<span data-ttu-id="53618-142">Následující kód Přepisuje Razor filtry asynchronní stránky:</span><span class="sxs-lookup"><span data-stu-id="53618-142">The following code overrides the asynchronous Razor Page filters:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Pages/Index.cshtml.cs?name=snippet)]

<a name="ifa"></a>

## <a name="implement-a-filter-attribute"></a><span data-ttu-id="53618-143">Implementace atributu filtru</span><span class="sxs-lookup"><span data-stu-id="53618-143">Implement a filter attribute</span></span>

<span data-ttu-id="53618-144">Předdefinovaný filtr filtru založený na atributech <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter.OnResultExecutionAsync*> může být podtříd.</span><span class="sxs-lookup"><span data-stu-id="53618-144">The built-in attribute-based filter <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter.OnResultExecutionAsync*> filter can be subclassed.</span></span> <span data-ttu-id="53618-145">Následující filtr Přidá hlavičku k odpovědi:</span><span class="sxs-lookup"><span data-stu-id="53618-145">The following filter adds a header to the response:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Filters/AddHeaderAttribute.cs)]

<span data-ttu-id="53618-146">Následující kód použije `AddHeader` atribut:</span><span class="sxs-lookup"><span data-stu-id="53618-146">The following code applies the `AddHeader` attribute:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Pages/Movies/Test.cshtml.cs)]

<span data-ttu-id="53618-147">K prohlédnutí hlaviček použijte nástroj, jako je například nástroj pro vývojáře v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="53618-147">Use a tool such as the browser developer tools to examine the headers.</span></span> <span data-ttu-id="53618-148">V části **hlavičky odpovědi** `author: Rick` se zobrazí.</span><span class="sxs-lookup"><span data-stu-id="53618-148">Under **Response Headers**, `author: Rick` is displayed.</span></span>

<span data-ttu-id="53618-149">Pokyny k přepsání objednávky najdete v tématu [přepisování výchozího pořadí](xref:mvc/controllers/filters#overriding-the-default-order) .</span><span class="sxs-lookup"><span data-stu-id="53618-149">See [Overriding the default order](xref:mvc/controllers/filters#overriding-the-default-order) for instructions on overriding the order.</span></span>

<span data-ttu-id="53618-150">Pokyny k krátkodobému zaokruhu kanálu filtrování z filtru najdete v tématu [zrušení a krátké vytvoření okruhu](xref:mvc/controllers/filters#cancellation-and-short-circuiting) .</span><span class="sxs-lookup"><span data-stu-id="53618-150">See [Cancellation and short circuiting](xref:mvc/controllers/filters#cancellation-and-short-circuiting) for instructions to short-circuit the filter pipeline from a filter.</span></span>

<a name="auth"></a>

## <a name="authorize-filter-attribute"></a><span data-ttu-id="53618-151">Autorizovat atribut filtru</span><span class="sxs-lookup"><span data-stu-id="53618-151">Authorize filter attribute</span></span>

<span data-ttu-id="53618-152">Atribut [autorizace](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) lze použít pro `PageModel` :</span><span class="sxs-lookup"><span data-stu-id="53618-152">The [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) attribute can be applied to a `PageModel`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Pages/ModelWithAuthFilter.cshtml.cs?highlight=7)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="53618-153">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="53618-153">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="53618-154">Razor Filtry stránky [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) a [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) umožňují, Razor aby stránky spouštěly kód před a po Razor spuštění obslužné rutiny stránky.</span><span class="sxs-lookup"><span data-stu-id="53618-154">Razor Page filters [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) and [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) allow Razor Pages to run code before and after a Razor Page handler is run.</span></span> <span data-ttu-id="53618-155">Razor Filtry stránky jsou podobné [filtrům akcí ASP.NET Core MVC](xref:mvc/controllers/filters#action-filters), s výjimkou toho, že je nelze použít na jednotlivé metody obslužné rutiny stránky.</span><span class="sxs-lookup"><span data-stu-id="53618-155">Razor Page filters are similar to [ASP.NET Core MVC action filters](xref:mvc/controllers/filters#action-filters), except they can't be applied to individual page handler methods.</span></span>

<span data-ttu-id="53618-156">Razor Filtry stránky:</span><span class="sxs-lookup"><span data-stu-id="53618-156">Razor Page filters:</span></span>

* <span data-ttu-id="53618-157">Spusťte kód po výběru metody obslužné rutiny, ale před tím, než dojde k vazbě modelu.</span><span class="sxs-lookup"><span data-stu-id="53618-157">Run code after a handler method has been selected, but before model binding occurs.</span></span>
* <span data-ttu-id="53618-158">Po dokončení vazby modelu spusťte kód před spuštěním obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="53618-158">Run code before the handler method executes, after model binding is complete.</span></span>
* <span data-ttu-id="53618-159">Spusťte kód poté, co se spustí metoda obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="53618-159">Run code after the handler method executes.</span></span>
* <span data-ttu-id="53618-160">Může být implementováno na stránce nebo globálně.</span><span class="sxs-lookup"><span data-stu-id="53618-160">Can be implemented on a page or globally.</span></span>
* <span data-ttu-id="53618-161">Nelze použít na konkrétní metody obslužné rutiny stránky.</span><span class="sxs-lookup"><span data-stu-id="53618-161">Cannot be applied to specific page handler methods.</span></span>

<span data-ttu-id="53618-162">Kód lze spustit před spuštěním obslužné rutiny pomocí konstruktoru stránky nebo middlewaru, ale pouze Razor filtry stránky mají přístup k [HttpContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.httpcontext?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_HttpContext).</span><span class="sxs-lookup"><span data-stu-id="53618-162">Code can be run before a handler method executes using the page constructor or middleware, but only Razor Page filters have access to [HttpContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.httpcontext?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_HttpContext).</span></span> <span data-ttu-id="53618-163">Filtry mají [FilterContext](/dotnet/api/microsoft.aspnetcore.mvc.filters.filtercontext?view=aspnetcore-2.0) odvozený parametr, který poskytuje přístup k `HttpContext` .</span><span class="sxs-lookup"><span data-stu-id="53618-163">Filters have a [FilterContext](/dotnet/api/microsoft.aspnetcore.mvc.filters.filtercontext?view=aspnetcore-2.0) derived parameter, which provides access to `HttpContext`.</span></span> <span data-ttu-id="53618-164">Například Implementace ukázkového [atributu Filter](#ifa) přidá hlavičku do odpovědi, něco, co nelze provést s konstruktory nebo middlewaru.</span><span class="sxs-lookup"><span data-stu-id="53618-164">For example, the [Implement a filter attribute](#ifa) sample adds a header to the response, something that can't be done with constructors or middleware.</span></span>

<span data-ttu-id="53618-165">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/filter/sample/PageFilter) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="53618-165">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/filter/sample/PageFilter) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="53618-166">Razor Filtry stránek poskytují následující metody, které lze použít globálně nebo na úrovni stránky:</span><span class="sxs-lookup"><span data-stu-id="53618-166">Razor Page filters provide the following methods, which can be applied globally or at the page level:</span></span>

* <span data-ttu-id="53618-167">Synchronní metody:</span><span class="sxs-lookup"><span data-stu-id="53618-167">Synchronous methods:</span></span>

  * <span data-ttu-id="53618-168">[OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0) : volá se po výběru metody obslužné rutiny, ale před tím, než dojde k vazbě modelu.</span><span class="sxs-lookup"><span data-stu-id="53618-168">[OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0) : Called after a handler method has been selected, but before model binding occurs.</span></span>
  * <span data-ttu-id="53618-169">[OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0) : volá se před spuštěním metody obslužné rutiny po dokončení vazby modelu.</span><span class="sxs-lookup"><span data-stu-id="53618-169">[OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0) : Called before the handler method executes, after model binding is complete.</span></span>
  * <span data-ttu-id="53618-170">[OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0) : volá se, když se spustí obslužná rutina, před výsledek akce.</span><span class="sxs-lookup"><span data-stu-id="53618-170">[OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0) : Called after the handler method executes, before the action result.</span></span>

* <span data-ttu-id="53618-171">Asynchronní metody:</span><span class="sxs-lookup"><span data-stu-id="53618-171">Asynchronous methods:</span></span>

  * <span data-ttu-id="53618-172">[OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0) : používá se asynchronně po výběru obslužné rutiny, ale před tím, než dojde k vazbě modelu.</span><span class="sxs-lookup"><span data-stu-id="53618-172">[OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0) : Called asynchronously after the handler method has been selected, but before model binding occurs.</span></span>
  * <span data-ttu-id="53618-173">[OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0) : používá se asynchronně před vyvoláním metody obslužné rutiny po dokončení vazby modelu.</span><span class="sxs-lookup"><span data-stu-id="53618-173">[OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0) : Called asynchronously before the handler method is invoked, after model binding is complete.</span></span>

> [!NOTE]
> <span data-ttu-id="53618-174">Implementujte **buď** synchronní, nebo asynchronní verzi rozhraní filtru, nikoli obojí.</span><span class="sxs-lookup"><span data-stu-id="53618-174">Implement **either** the synchronous or the async version of a filter interface, not both.</span></span> <span data-ttu-id="53618-175">Rozhraní nejprve kontroluje, zda filtr implementuje asynchronní rozhraní, a pokud ano, zavolá tuto metodu.</span><span class="sxs-lookup"><span data-stu-id="53618-175">The framework checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="53618-176">V takovém případě volá metody synchronního rozhraní.</span><span class="sxs-lookup"><span data-stu-id="53618-176">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="53618-177">Pokud jsou implementována obě rozhraní, jsou zavolány pouze asynchronní metody.</span><span class="sxs-lookup"><span data-stu-id="53618-177">If both interfaces are implemented, only the async methods are called.</span></span> <span data-ttu-id="53618-178">Stejné pravidlo se vztahuje na přepsání na stránkách, implementuje synchronní nebo asynchronní verzi přepsání, nikoli obojí.</span><span class="sxs-lookup"><span data-stu-id="53618-178">The same rule applies to overrides in pages, implement the synchronous or the async version of the override, not both.</span></span>

## <a name="implement-no-locrazor-page-filters-globally"></a><span data-ttu-id="53618-179">RazorGlobálně implementovat filtry stránek</span><span class="sxs-lookup"><span data-stu-id="53618-179">Implement Razor Page filters globally</span></span>

<span data-ttu-id="53618-180">Následující kód implementuje `IAsyncPageFilter` :</span><span class="sxs-lookup"><span data-stu-id="53618-180">The following code implements `IAsyncPageFilter`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Filters/SampleAsyncPageFilter.cs?name=snippet1)]

<span data-ttu-id="53618-181">V předchozím kódu není [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger?view=aspnetcore-2.0) požadováno.</span><span class="sxs-lookup"><span data-stu-id="53618-181">In the preceding code, [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger?view=aspnetcore-2.0) is not required.</span></span> <span data-ttu-id="53618-182">Používá se v ukázce k poskytnutí informací o trasování pro aplikaci.</span><span class="sxs-lookup"><span data-stu-id="53618-182">It's used in the sample to provide trace information for the application.</span></span>

<span data-ttu-id="53618-183">Následující kód povoluje `SampleAsyncPageFilter` ve `Startup` třídě:</span><span class="sxs-lookup"><span data-stu-id="53618-183">The following code enables the `SampleAsyncPageFilter` in the `Startup` class:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Startup.cs?name=snippet2&highlight=11)]

<span data-ttu-id="53618-184">Následující kód ukazuje úplnou `Startup` třídu:</span><span class="sxs-lookup"><span data-stu-id="53618-184">The following code shows the complete `Startup` class:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Startup.cs?name=snippet1)]

<span data-ttu-id="53618-185">Následující kód volá `AddFolderApplicationModelConvention` pro použití `SampleAsyncPageFilter` pouze na stránky v */subFolder*:</span><span class="sxs-lookup"><span data-stu-id="53618-185">The following code calls `AddFolderApplicationModelConvention` to apply the `SampleAsyncPageFilter` to only pages in */subFolder*:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Startup2.cs?name=snippet2)]

<span data-ttu-id="53618-186">Následující kód implementuje synchronní `IPageFilter` :</span><span class="sxs-lookup"><span data-stu-id="53618-186">The following code implements the synchronous `IPageFilter`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Filters/SamplePageFilter.cs?name=snippet1)]

<span data-ttu-id="53618-187">Následující kód umožňuje `SamplePageFilter` :</span><span class="sxs-lookup"><span data-stu-id="53618-187">The following code enables the `SamplePageFilter`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/StartupSync.cs?name=snippet2&highlight=11)]

## <a name="implement-no-locrazor-page-filters-by-overriding-filter-methods"></a><span data-ttu-id="53618-188">Implementace Razor filtrů stránek přepsáním metod filtrování</span><span class="sxs-lookup"><span data-stu-id="53618-188">Implement Razor Page filters by overriding filter methods</span></span>

<span data-ttu-id="53618-189">Následující kód Přepisuje Razor filtry synchronní stránky:</span><span class="sxs-lookup"><span data-stu-id="53618-189">The following code overrides the synchronous Razor Page filters:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Pages/Index.cshtml.cs)]

<a name="ifa"></a>

## <a name="implement-a-filter-attribute"></a><span data-ttu-id="53618-190">Implementace atributu filtru</span><span class="sxs-lookup"><span data-stu-id="53618-190">Implement a filter attribute</span></span>

<span data-ttu-id="53618-191">Vestavěný filtr [OnResultExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncresultfilter.onresultexecutionasync?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Filters_IAsyncResultFilter_OnResultExecutionAsync_Microsoft_AspNetCore_Mvc_Filters_ResultExecutingContext_Microsoft_AspNetCore_Mvc_Filters_ResultExecutionDelegate_) filtru založený na atributech může být podtříd.</span><span class="sxs-lookup"><span data-stu-id="53618-191">The built-in attribute-based filter [OnResultExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncresultfilter.onresultexecutionasync?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Filters_IAsyncResultFilter_OnResultExecutionAsync_Microsoft_AspNetCore_Mvc_Filters_ResultExecutingContext_Microsoft_AspNetCore_Mvc_Filters_ResultExecutionDelegate_) filter can be subclassed.</span></span> <span data-ttu-id="53618-192">Následující filtr Přidá hlavičku k odpovědi:</span><span class="sxs-lookup"><span data-stu-id="53618-192">The following filter adds a header to the response:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Filters/AddHeaderAttribute.cs)]

<span data-ttu-id="53618-193">Následující kód použije `AddHeader` atribut:</span><span class="sxs-lookup"><span data-stu-id="53618-193">The following code applies the `AddHeader` attribute:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Pages/Contact.cshtml.cs?name=snippet1)]

<span data-ttu-id="53618-194">Pokyny k přepsání objednávky najdete v tématu [přepisování výchozího pořadí](xref:mvc/controllers/filters#overriding-the-default-order) .</span><span class="sxs-lookup"><span data-stu-id="53618-194">See [Overriding the default order](xref:mvc/controllers/filters#overriding-the-default-order) for instructions on overriding the order.</span></span>

<span data-ttu-id="53618-195">Pokyny k krátkodobému zaokruhu kanálu filtrování z filtru najdete v tématu [zrušení a krátké vytvoření okruhu](xref:mvc/controllers/filters#cancellation-and-short-circuiting) .</span><span class="sxs-lookup"><span data-stu-id="53618-195">See [Cancellation and short circuiting](xref:mvc/controllers/filters#cancellation-and-short-circuiting) for instructions to short-circuit the filter pipeline from a filter.</span></span> 

<a name="auth"></a>

## <a name="authorize-filter-attribute"></a><span data-ttu-id="53618-196">Autorizovat atribut filtru</span><span class="sxs-lookup"><span data-stu-id="53618-196">Authorize filter attribute</span></span>

<span data-ttu-id="53618-197">Atribut [autorizace](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) lze použít pro `PageModel` :</span><span class="sxs-lookup"><span data-stu-id="53618-197">The [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) attribute can be applied to a `PageModel`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Pages/ModelWithAuthFilter.cshtml.cs?highlight=7)]

::: moniker-end
