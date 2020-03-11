---
title: Filtrovat metody Razor Pages v ASP.NET Core
author: Rick-Anderson
description: Naučte se vytvářet metody filtru pro Razor Pages v ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 2/18/2020
uid: razor-pages/filter
ms.openlocfilehash: cd772da8ed565bc779d8c6bcc7c9949a0c1c7c60
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78660753"
---
# <a name="filter-methods-for-razor-pages-in-aspnet-core"></a>Filtrovat metody Razor Pages v ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

Filtry stránky Razor [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) a [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) umožňují, aby Razor Pages spustit kód před a po spuštění obslužné rutiny stránky Razor. Filtry stránky Razor jsou podobné [filtrům akcí ASP.NET Core MVC](xref:mvc/controllers/filters#action-filters), s výjimkou toho, že je nelze použít na jednotlivé metody obslužné rutiny stránky.

Filtry stránky Razor:

* Spusťte kód po výběru metody obslužné rutiny, ale před tím, než dojde k vazbě modelu.
* Po dokončení vazby modelu spusťte kód před spuštěním obslužné rutiny.
* Spusťte kód poté, co se spustí metoda obslužné rutiny.
* Může být implementováno na stránce nebo globálně.
* Nelze použít na konkrétní metody obslužné rutiny stránky.
* Může mít závislosti konstruktoru vyplněné [vstřikem závislosti](xref:fundamentals/dependency-injection) (di). Další informace najdete v tématu [ServiceFilterAttribute](/aspnet/core/mvc/controllers/filters#servicefilterattribute) a [TypeFilterAttribute](/aspnet/core/mvc/controllers/filters#typefilterattribute).

I když konstruktory stránky a middleware umožňují spouštění vlastního kódu před spuštěním metody obslužné rutiny, umožňují přístup k <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.HttpContext> a stránce pouze filtry stránky Razor. Middleware má přístup k `HttpContext`, ale ne k "kontextu stránky". Filtry mají <xref:Microsoft.AspNetCore.Mvc.Filters.FilterContext> odvozeného parametru, který poskytuje přístup k `HttpContext`. Například Implementace ukázkového [atributu Filter](#ifa) přidá hlavičku do odpovědi, něco, co nelze provést s konstruktory nebo middlewaru.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/filter/3.1sample) ([Jak stáhnout](xref:index#how-to-download-a-sample))

Filtry stránky Razor poskytují následující metody, které lze použít globálně nebo na úrovni stránky:

* Synchronní metody:

  * [OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0) : volá se po výběru metody obslužné rutiny, ale před tím, než dojde k vazbě modelu.
  * [OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0) : volá se před spuštěním metody obslužné rutiny po dokončení vazby modelu.
  * [OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0) : volá se, když se spustí obslužná rutina, před výsledek akce.

* Asynchronní metody:

  * [OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0) : používá se asynchronně po výběru obslužné rutiny, ale před tím, než dojde k vazbě modelu.
  * [OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0) : používá se asynchronně před vyvoláním metody obslužné rutiny po dokončení vazby modelu.

Implementujte **buď** synchronní, nebo asynchronní verzi rozhraní filtru, **nikoli** obojí. Rozhraní nejprve kontroluje, zda filtr implementuje asynchronní rozhraní, a pokud ano, zavolá tuto metodu. V takovém případě volá metody synchronního rozhraní. Pokud jsou implementována obě rozhraní, jsou zavolány pouze asynchronní metody. Stejné pravidlo se vztahuje na přepsání na stránkách, implementuje synchronní nebo asynchronní verzi přepsání, nikoli obojí.

## <a name="implement-razor-page-filters-globally"></a>Globálně implementovat filtry stránek Razor

Následující kód implementuje `IAsyncPageFilter`:

[!code-csharp[Main](filter/3.1sample/PageFilter/Filters/SampleAsyncPageFilter.cs?name=snippet1)]

V předchozím kódu `ProcessUserAgent.Write` je uživatelem zadaný kód, který pracuje s řetězcem uživatelského agenta.

Následující kód povoluje `SampleAsyncPageFilter` v `Startup` třídě:

[!code-csharp[Main](filter/3.1sample/PageFilter/Startup.cs?name=snippet2)]

Následující kód volá <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> pro použití `SampleAsyncPageFilter` pouze na stránky v */Movies*:

[!code-csharp[Main](filter/3.1sample/PageFilter/Startup2.cs?name=snippet2)]

Následující kód implementuje synchronní `IPageFilter`:

[!code-csharp[Main](filter/3.1sample/PageFilter/Filters/SamplePageFilter.cs?name=snippet1)]

Následující kód povoluje `SamplePageFilter`:

[!code-csharp[Main](filter/3.1sample/PageFilter/StartupSync.cs?name=snippet2)]

## <a name="implement-razor-page-filters-by-overriding-filter-methods"></a>Implementace filtrů stránek Razor pomocí přepsání metod filtrování

Následující kód Přepisuje filtry stránky asynchronního Razor:

[!code-csharp[Main](filter/3.1sample/PageFilter/Pages/Index.cshtml.cs?name=snippet)]

<a name="ifa"></a>

## <a name="implement-a-filter-attribute"></a>Implementace atributu filtru

Vestavěný filtr založený na atributech <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter.OnResultExecutionAsync*> filtr může být podtříd. Následující filtr Přidá hlavičku k odpovědi:

[!code-csharp[Main](filter/3.1sample/PageFilter/Filters/AddHeaderAttribute.cs)]

Následující kód používá atribut `AddHeader`:

[!code-csharp[Main](filter/3.1sample/PageFilter/Pages/Movies/Test.cshtml.cs)]

K prohlédnutí hlaviček použijte nástroj, jako je například nástroj pro vývojáře v prohlížeči. V části **hlavičky odpovědi**se zobrazí `author: Rick`.

Pokyny k přepsání objednávky najdete v tématu [přepisování výchozího pořadí](xref:mvc/controllers/filters#overriding-the-default-order) .

Pokyny k krátkodobému zaokruhu kanálu filtrování z filtru najdete v tématu [zrušení a krátké vytvoření okruhu](xref:mvc/controllers/filters#cancellation-and-short-circuiting) .

<a name="auth"></a>

## <a name="authorize-filter-attribute"></a>Autorizovat atribut filtru

Atribut [autorizace](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) lze použít pro `PageModel`:

[!code-csharp[Main](filter/sample/PageFilter/Pages/ModelWithAuthFilter.cshtml.cs?highlight=7)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

Filtry stránky Razor [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) a [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) umožňují, aby Razor Pages spustit kód před a po spuštění obslužné rutiny stránky Razor. Filtry stránky Razor jsou podobné [filtrům akcí ASP.NET Core MVC](xref:mvc/controllers/filters#action-filters), s výjimkou toho, že je nelze použít na jednotlivé metody obslužné rutiny stránky.

Filtry stránky Razor:

* Spusťte kód po výběru metody obslužné rutiny, ale před tím, než dojde k vazbě modelu.
* Po dokončení vazby modelu spusťte kód před spuštěním obslužné rutiny.
* Spusťte kód poté, co se spustí metoda obslužné rutiny.
* Může být implementováno na stránce nebo globálně.
* Nelze použít na konkrétní metody obslužné rutiny stránky.

Kód lze spustit před spuštěním obslužné rutiny pomocí konstruktoru stránky nebo middlewaru, ale pouze filtry stránky Razor mají přístup k [HttpContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.httpcontext?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_HttpContext). Filtry mají [FilterContext](/dotnet/api/microsoft.aspnetcore.mvc.filters.filtercontext?view=aspnetcore-2.0) odvozený parametr, který poskytuje přístup k `HttpContext`. Například Implementace ukázkového [atributu Filter](#ifa) přidá hlavičku do odpovědi, něco, co nelze provést s konstruktory nebo middlewaru.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/filter/sample/PageFilter) ([Jak stáhnout](xref:index#how-to-download-a-sample))

Filtry stránky Razor poskytují následující metody, které lze použít globálně nebo na úrovni stránky:

* Synchronní metody:

  * [OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0) : volá se po výběru metody obslužné rutiny, ale před tím, než dojde k vazbě modelu.
  * [OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0) : volá se před spuštěním metody obslužné rutiny po dokončení vazby modelu.
  * [OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0) : volá se, když se spustí obslužná rutina, před výsledek akce.

* Asynchronní metody:

  * [OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0) : používá se asynchronně po výběru obslužné rutiny, ale před tím, než dojde k vazbě modelu.
  * [OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0) : používá se asynchronně před vyvoláním metody obslužné rutiny po dokončení vazby modelu.

> [!NOTE]
> Implementujte **buď** synchronní, nebo asynchronní verzi rozhraní filtru, nikoli obojí. Rozhraní nejprve kontroluje, zda filtr implementuje asynchronní rozhraní, a pokud ano, zavolá tuto metodu. V takovém případě volá metody synchronního rozhraní. Pokud jsou implementována obě rozhraní, jsou zavolány pouze asynchronní metody. Stejné pravidlo se vztahuje na přepsání na stránkách, implementuje synchronní nebo asynchronní verzi přepsání, nikoli obojí.

## <a name="implement-razor-page-filters-globally"></a>Globálně implementovat filtry stránek Razor

Následující kód implementuje `IAsyncPageFilter`:

[!code-csharp[Main](filter/sample/PageFilter/Filters/SampleAsyncPageFilter.cs?name=snippet1)]

V předchozím kódu není [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger?view=aspnetcore-2.0) požadováno. Používá se v ukázce k poskytnutí informací o trasování pro aplikaci.

Následující kód povoluje `SampleAsyncPageFilter` v `Startup` třídě:

[!code-csharp[Main](filter/sample/PageFilter/Startup.cs?name=snippet2&highlight=11)]

Následující kód ukazuje kompletní třídu `Startup`:

[!code-csharp[Main](filter/sample/PageFilter/Startup.cs?name=snippet1)]

Následující kód volá `AddFolderApplicationModelConvention` pro použití `SampleAsyncPageFilter` pouze na stránky v */subFolder*:

[!code-csharp[Main](filter/sample/PageFilter/Startup2.cs?name=snippet2)]

Následující kód implementuje synchronní `IPageFilter`:

[!code-csharp[Main](filter/sample/PageFilter/Filters/SamplePageFilter.cs?name=snippet1)]

Následující kód povoluje `SamplePageFilter`:

[!code-csharp[Main](filter/sample/PageFilter/StartupSync.cs?name=snippet2&highlight=11)]

## <a name="implement-razor-page-filters-by-overriding-filter-methods"></a>Implementace filtrů stránek Razor pomocí přepsání metod filtrování

Následující kód přepíše synchronní filtry stránky Razor:

[!code-csharp[Main](filter/sample/PageFilter/Pages/Index.cshtml.cs)]

<a name="ifa"></a>

## <a name="implement-a-filter-attribute"></a>Implementace atributu filtru

Vestavěný filtr [OnResultExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncresultfilter.onresultexecutionasync?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Filters_IAsyncResultFilter_OnResultExecutionAsync_Microsoft_AspNetCore_Mvc_Filters_ResultExecutingContext_Microsoft_AspNetCore_Mvc_Filters_ResultExecutionDelegate_) filtru založený na atributech může být podtříd. Následující filtr Přidá hlavičku k odpovědi:

[!code-csharp[Main](filter/sample/PageFilter/Filters/AddHeaderAttribute.cs)]

Následující kód používá atribut `AddHeader`:

[!code-csharp[Main](filter/sample/PageFilter/Pages/Contact.cshtml.cs?name=snippet1)]

Pokyny k přepsání objednávky najdete v tématu [přepisování výchozího pořadí](xref:mvc/controllers/filters#overriding-the-default-order) .

Pokyny k krátkodobému zaokruhu kanálu filtrování z filtru najdete v tématu [zrušení a krátké vytvoření okruhu](xref:mvc/controllers/filters#cancellation-and-short-circuiting) . 

<a name="auth"></a>

## <a name="authorize-filter-attribute"></a>Autorizovat atribut filtru

Atribut [autorizace](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) lze použít pro `PageModel`:

[!code-csharp[Main](filter/sample/PageFilter/Pages/ModelWithAuthFilter.cshtml.cs?highlight=7)]

::: moniker-end
