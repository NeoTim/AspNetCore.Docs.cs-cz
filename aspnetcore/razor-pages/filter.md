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
ms.openlocfilehash: 436d640130b378e2c770322186020c6e252872ef
ms.sourcegitcommit: 24106b7ffffc9fff410a679863e28aeb2bbe5b7e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/17/2020
ms.locfileid: "90722511"
---
# <a name="filter-methods-for-no-locrazor-pages-in-aspnet-core"></a>Filtrovat metody pro Razor stránky v ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

Razor Filtry stránky [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) a [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) umožňují, Razor aby stránky spouštěly kód před a po Razor spuštění obslužné rutiny stránky. Razor Filtry stránky jsou podobné [filtrům akcí ASP.NET Core MVC](xref:mvc/controllers/filters#action-filters), s výjimkou toho, že je nelze použít na jednotlivé metody obslužné rutiny stránky.

Razor Filtry stránky:

* Spusťte kód po výběru metody obslužné rutiny, ale před tím, než dojde k vazbě modelu.
* Po dokončení vazby modelu spusťte kód před spuštěním obslužné rutiny.
* Spusťte kód poté, co se spustí metoda obslužné rutiny.
* Může být implementováno na stránce nebo globálně.
* Nelze použít na konkrétní metody obslužné rutiny stránky.
* Může mít závislosti konstruktoru vyplněné [vstřikem závislosti](xref:fundamentals/dependency-injection) (di). Další informace najdete v tématu [ServiceFilterAttribute](../mvc/controllers/filters.md#servicefilterattribute) a [TypeFilterAttribute](../mvc/controllers/filters.md#typefilterattribute).

I když konstruktory stránky a middleware umožňují spouštění vlastního kódu před spuštěním metody obslužné rutiny, pouze Razor filtry stránky umožňují přístup <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.HttpContext> a stránku. Middleware má přístup k `HttpContext` , ale ne k "kontextu stránky". Filtry mají <xref:Microsoft.AspNetCore.Mvc.Filters.FilterContext> odvozený parametr, který poskytuje přístup k `HttpContext` . Zde je ukázka pro filtr stránky: [implementujte atribut filtru](#ifa) , který do odpovědi přidá hlavičku, něco, co nelze provést s konstruktory nebo middleware. Přístup k kontextu stránky, který zahrnuje přístup k instancím stránky a jejímu modelu, je k dispozici pouze při provádění filtrů, obslužných rutin nebo textu Razor stránky.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/filter/3.1sample) ([Jak stáhnout](xref:index#how-to-download-a-sample))

Razor Filtry stránek poskytují následující metody, které lze použít globálně nebo na úrovni stránky:

* Synchronní metody:

  * [OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0) : volá se po výběru metody obslužné rutiny, ale před tím, než dojde k vazbě modelu.
  * [OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0) : volá se před spuštěním metody obslužné rutiny po dokončení vazby modelu.
  * [OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0) : volá se, když se spustí obslužná rutina, před výsledek akce.

* Asynchronní metody:

  * [OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0) : používá se asynchronně po výběru obslužné rutiny, ale před tím, než dojde k vazbě modelu.
  * [OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0) : používá se asynchronně před vyvoláním metody obslužné rutiny po dokončení vazby modelu.

Implementujte **buď** synchronní, nebo asynchronní verzi rozhraní filtru, **nikoli** obojí. Rozhraní nejprve kontroluje, zda filtr implementuje asynchronní rozhraní, a pokud ano, zavolá tuto metodu. V takovém případě volá metody synchronního rozhraní. Pokud jsou implementována obě rozhraní, jsou zavolány pouze asynchronní metody. Stejné pravidlo se vztahuje na přepsání na stránkách, implementuje synchronní nebo asynchronní verzi přepsání, nikoli obojí.

## <a name="implement-no-locrazor-page-filters-globally"></a>RazorGlobálně implementovat filtry stránek

Následující kód implementuje `IAsyncPageFilter` :

[!code-csharp[Main](filter/3.1sample/PageFilter/Filters/SampleAsyncPageFilter.cs?name=snippet1)]

V předchozím kódu `ProcessUserAgent.Write` je uživatelem zadaný kód, který pracuje s řetězcem uživatelského agenta.

Následující kód povoluje `SampleAsyncPageFilter` ve `Startup` třídě:

[!code-csharp[Main](filter/3.1sample/PageFilter/Startup.cs?name=snippet2)]

Následující kód volá <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> pro použití `SampleAsyncPageFilter` pouze na stránky v */Movies*:

[!code-csharp[Main](filter/3.1sample/PageFilter/Startup2.cs?name=snippet2)]

Následující kód implementuje synchronní `IPageFilter` :

[!code-csharp[Main](filter/3.1sample/PageFilter/Filters/SamplePageFilter.cs?name=snippet1)]

Následující kód umožňuje `SamplePageFilter` :

[!code-csharp[Main](filter/3.1sample/PageFilter/StartupSync.cs?name=snippet2)]

## <a name="implement-no-locrazor-page-filters-by-overriding-filter-methods"></a>Implementace Razor filtrů stránek přepsáním metod filtrování

Následující kód Přepisuje Razor filtry asynchronní stránky:

[!code-csharp[Main](filter/3.1sample/PageFilter/Pages/Index.cshtml.cs?name=snippet)]

<a name="ifa"></a>

## <a name="implement-a-filter-attribute"></a>Implementace atributu filtru

Předdefinovaný filtr filtru založený na atributech <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter.OnResultExecutionAsync*> může být podtříd. Následující filtr Přidá hlavičku k odpovědi:

[!code-csharp[Main](filter/3.1sample/PageFilter/Filters/AddHeaderAttribute.cs)]

Následující kód použije `AddHeader` atribut:

[!code-csharp[Main](filter/3.1sample/PageFilter/Pages/Movies/Test.cshtml.cs)]

K prohlédnutí hlaviček použijte nástroj, jako je například nástroj pro vývojáře v prohlížeči. V části **hlavičky odpovědi** `author: Rick` se zobrazí.

Pokyny k přepsání objednávky najdete v tématu [přepisování výchozího pořadí](xref:mvc/controllers/filters#overriding-the-default-order) .

Pokyny k krátkodobému zaokruhu kanálu filtrování z filtru najdete v tématu [zrušení a krátké vytvoření okruhu](xref:mvc/controllers/filters#cancellation-and-short-circuiting) .

<a name="auth"></a>

## <a name="authorize-filter-attribute"></a>Autorizovat atribut filtru

Atribut [autorizace](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) lze použít pro `PageModel` :

[!code-csharp[Main](filter/sample/PageFilter/Pages/ModelWithAuthFilter.cshtml.cs?highlight=7)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

Razor Filtry stránky [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) a [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) umožňují, Razor aby stránky spouštěly kód před a po Razor spuštění obslužné rutiny stránky. Razor Filtry stránky jsou podobné [filtrům akcí ASP.NET Core MVC](xref:mvc/controllers/filters#action-filters), s výjimkou toho, že je nelze použít na jednotlivé metody obslužné rutiny stránky.

Razor Filtry stránky:

* Spusťte kód po výběru metody obslužné rutiny, ale před tím, než dojde k vazbě modelu.
* Po dokončení vazby modelu spusťte kód před spuštěním obslužné rutiny.
* Spusťte kód poté, co se spustí metoda obslužné rutiny.
* Může být implementováno na stránce nebo globálně.
* Nelze použít na konkrétní metody obslužné rutiny stránky.

Kód lze spustit před spuštěním obslužné rutiny pomocí konstruktoru stránky nebo middlewaru, ale pouze Razor filtry stránky mají přístup k [HttpContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.httpcontext?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_HttpContext). Filtry mají [FilterContext](/dotnet/api/microsoft.aspnetcore.mvc.filters.filtercontext?view=aspnetcore-2.0) odvozený parametr, který poskytuje přístup k `HttpContext` . Například Implementace ukázkového [atributu Filter](#ifa) přidá hlavičku do odpovědi, něco, co nelze provést s konstruktory nebo middlewaru.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/filter/sample/PageFilter) ([Jak stáhnout](xref:index#how-to-download-a-sample))

Razor Filtry stránek poskytují následující metody, které lze použít globálně nebo na úrovni stránky:

* Synchronní metody:

  * [OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0) : volá se po výběru metody obslužné rutiny, ale před tím, než dojde k vazbě modelu.
  * [OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0) : volá se před spuštěním metody obslužné rutiny po dokončení vazby modelu.
  * [OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0) : volá se, když se spustí obslužná rutina, před výsledek akce.

* Asynchronní metody:

  * [OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0) : používá se asynchronně po výběru obslužné rutiny, ale před tím, než dojde k vazbě modelu.
  * [OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0) : používá se asynchronně před vyvoláním metody obslužné rutiny po dokončení vazby modelu.

> [!NOTE]
> Implementujte **buď** synchronní, nebo asynchronní verzi rozhraní filtru, nikoli obojí. Rozhraní nejprve kontroluje, zda filtr implementuje asynchronní rozhraní, a pokud ano, zavolá tuto metodu. V takovém případě volá metody synchronního rozhraní. Pokud jsou implementována obě rozhraní, jsou zavolány pouze asynchronní metody. Stejné pravidlo se vztahuje na přepsání na stránkách, implementuje synchronní nebo asynchronní verzi přepsání, nikoli obojí.

## <a name="implement-no-locrazor-page-filters-globally"></a>RazorGlobálně implementovat filtry stránek

Následující kód implementuje `IAsyncPageFilter` :

[!code-csharp[Main](filter/sample/PageFilter/Filters/SampleAsyncPageFilter.cs?name=snippet1)]

V předchozím kódu není [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger?view=aspnetcore-2.0) požadováno. Používá se v ukázce k poskytnutí informací o trasování pro aplikaci.

Následující kód povoluje `SampleAsyncPageFilter` ve `Startup` třídě:

[!code-csharp[Main](filter/sample/PageFilter/Startup.cs?name=snippet2&highlight=11)]

Následující kód ukazuje úplnou `Startup` třídu:

[!code-csharp[Main](filter/sample/PageFilter/Startup.cs?name=snippet1)]

Následující kód volá `AddFolderApplicationModelConvention` pro použití `SampleAsyncPageFilter` pouze na stránky v */subFolder*:

[!code-csharp[Main](filter/sample/PageFilter/Startup2.cs?name=snippet2)]

Následující kód implementuje synchronní `IPageFilter` :

[!code-csharp[Main](filter/sample/PageFilter/Filters/SamplePageFilter.cs?name=snippet1)]

Následující kód umožňuje `SamplePageFilter` :

[!code-csharp[Main](filter/sample/PageFilter/StartupSync.cs?name=snippet2&highlight=11)]

## <a name="implement-no-locrazor-page-filters-by-overriding-filter-methods"></a>Implementace Razor filtrů stránek přepsáním metod filtrování

Následující kód Přepisuje Razor filtry synchronní stránky:

[!code-csharp[Main](filter/sample/PageFilter/Pages/Index.cshtml.cs)]

<a name="ifa"></a>

## <a name="implement-a-filter-attribute"></a>Implementace atributu filtru

Vestavěný filtr [OnResultExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncresultfilter.onresultexecutionasync?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Filters_IAsyncResultFilter_OnResultExecutionAsync_Microsoft_AspNetCore_Mvc_Filters_ResultExecutingContext_Microsoft_AspNetCore_Mvc_Filters_ResultExecutionDelegate_) filtru založený na atributech může být podtříd. Následující filtr Přidá hlavičku k odpovědi:

[!code-csharp[Main](filter/sample/PageFilter/Filters/AddHeaderAttribute.cs)]

Následující kód použije `AddHeader` atribut:

[!code-csharp[Main](filter/sample/PageFilter/Pages/Contact.cshtml.cs?name=snippet1)]

Pokyny k přepsání objednávky najdete v tématu [přepisování výchozího pořadí](xref:mvc/controllers/filters#overriding-the-default-order) .

Pokyny k krátkodobému zaokruhu kanálu filtrování z filtru najdete v tématu [zrušení a krátké vytvoření okruhu](xref:mvc/controllers/filters#cancellation-and-short-circuiting) . 

<a name="auth"></a>

## <a name="authorize-filter-attribute"></a>Autorizovat atribut filtru

Atribut [autorizace](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) lze použít pro `PageModel` :

[!code-csharp[Main](filter/sample/PageFilter/Pages/ModelWithAuthFilter.cshtml.cs?highlight=7)]

::: moniker-end