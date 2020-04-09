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
# <a name="filter-methods-for-razor-pages-in-aspnet-core"></a>Metody filtrování pro žiletkové stránky v ASP.NET jádru

::: moniker range=">= aspnetcore-3.0"

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

Filtry Razor Page [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) a [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) umožňují razor pages spouštět kód před a po spuštění obslužné rutiny Razor Page. Filtry holicí strojky jsou podobné [ASP.NET filtry akcí Core MVC](xref:mvc/controllers/filters#action-filters), s výjimkou, že je nelze použít na jednotlivé metody obslužné rutiny stránky.

Filtry stránky holicí strojek:

* Spusťte kód po metodě obslužné rutiny, ale dříve, než dojde k vazbě modelu.
* Spusťte kód před spuštěním metody obslužné rutiny po dokončení vazby modelu.
* Spusťte kód po spuštění metody obslužné rutiny.
* Lze implementovat na stránce nebo globálně.
* Nelze použít pro konkrétní metody obslužné rutiny stránky.
* Může mít konstruktor uvyplněny [vkládání závislostí](xref:fundamentals/dependency-injection) (DI). Další informace naleznete v [tématech ServiceFilterAttribute](/aspnet/core/mvc/controllers/filters#servicefilterattribute) a [TypeFilterAttribute](/aspnet/core/mvc/controllers/filters#typefilterattribute).

Zatímco konstruktory stránek a middleware umožňují provádění vlastního kódu před spuštěním metody <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.HttpContext> obslužné rutiny, přístup a stránku umožňují pouze filtry Razor Page. Middleware má přístup `HttpContext`k , ale ne k "kontextu stránky". Filtry <xref:Microsoft.AspNetCore.Mvc.Filters.FilterContext> mají odvozený parametr, `HttpContext`který poskytuje přístup k aplikaci . Například [ukázka atributu Implement a filter](#ifa) přidá k odpovědi záhlaví, což nelze provést s konstruktory nebo middleware.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/filter/3.1sample) [(jak stáhnout)](xref:index#how-to-download-a-sample)

Filtry Razor Page poskytují následující metody, které lze použít globálně nebo na úrovni stránky:

* Synchronní metody:

  * [OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0) : Voláno po obslužné rutiny metoda byla vybrána, ale před dojde k vazbě modelu.
  * [OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0) : Voláno před spuštěním metody obslužné rutiny po dokončení vazby modelu.
  * [OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0) : Voláno po spuštění metody obslužné rutiny před výsledkem akce.

* Asynchronní metody:

  * [OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0) : Voláno asynchronně po metodě obslužné rutiny, ale dříve, než dojde k vazbě modelu.
  * [OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0) : Voláno asynchronně před vyvoláním metody obslužné rutiny po dokončení vazby modelu.

Implementujte **synchronní** nebo asynchronní verzi rozhraní filtru, **nikoli** obojí. Rozhraní framework nejprve zkontroluje, pokud filtr implementuje asynchronní rozhraní a pokud ano, volá to. Pokud ne, volá metody synchronního rozhraní. Pokud jsou implementovány obě rozhraní, jsou volány pouze asynchronní metody. Stejné pravidlo platí pro lokální změny na stránkách, implementovat synchronní nebo asynchronní verzi přepsání, ne obojí.

## <a name="implement-razor-page-filters-globally"></a>Implementovat filtry Razor Page globálně

Následující kód implementuje `IAsyncPageFilter`:

[!code-csharp[Main](filter/3.1sample/PageFilter/Filters/SampleAsyncPageFilter.cs?name=snippet1)]

V předchozím kódu `ProcessUserAgent.Write` je uživatel zadaný kód, který pracuje s řetězcem uživatelského agenta.

Následující kód umožňuje `SampleAsyncPageFilter` ve `Startup` třídě:

[!code-csharp[Main](filter/3.1sample/PageFilter/Startup.cs?name=snippet2)]

Následující kód <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> volá použít `SampleAsyncPageFilter` pouze stránky v */Movies*:

[!code-csharp[Main](filter/3.1sample/PageFilter/Startup2.cs?name=snippet2)]

Následující kód implementuje synchronní `IPageFilter`:

[!code-csharp[Main](filter/3.1sample/PageFilter/Filters/SamplePageFilter.cs?name=snippet1)]

Následující kód umožňuje `SamplePageFilter`:

[!code-csharp[Main](filter/3.1sample/PageFilter/StartupSync.cs?name=snippet2)]

## <a name="implement-razor-page-filters-by-overriding-filter-methods"></a>Implementovat filtry Razor Page přepsáním metod filtru

Následující kód přepíše asynchronní filtry Razor Page:

[!code-csharp[Main](filter/3.1sample/PageFilter/Pages/Index.cshtml.cs?name=snippet)]

<a name="ifa"></a>

## <a name="implement-a-filter-attribute"></a>Implementace atributu filtru

Vestavěný filtr filtr <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter.OnResultExecutionAsync*> založený na atributech může být podtřídou. Následující filtr přidá k odpovědi záhlaví:

[!code-csharp[Main](filter/3.1sample/PageFilter/Filters/AddHeaderAttribute.cs)]

Následující kód použije `AddHeader` atribut:

[!code-csharp[Main](filter/3.1sample/PageFilter/Pages/Movies/Test.cshtml.cs)]

Záhlaví můžete zkontrolovat pomocí nástroje, například nástrojů pro vývojáře prohlížeče. V části Záhlaví `author: Rick` **odpovědí**se zobrazí.

Pokyny k přepsání pořadí v [tématu Přepsání výchozího pořadí](xref:mvc/controllers/filters#overriding-the-default-order) naleznete v tématu Přepsání pořadí.

Pokyny ke zkratování potrubí filtru z filtru naleznete v tématu [Zrušení a zkratování.](xref:mvc/controllers/filters#cancellation-and-short-circuiting)

<a name="auth"></a>

## <a name="authorize-filter-attribute"></a>Autorizovat atribut filtru

Atribut [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) lze použít `PageModel`na :

[!code-csharp[Main](filter/sample/PageFilter/Pages/ModelWithAuthFilter.cshtml.cs?highlight=7)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

Filtry Razor Page [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) a [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) umožňují razor pages spouštět kód před a po spuštění obslužné rutiny Razor Page. Filtry holicí strojky jsou podobné [ASP.NET filtry akcí Core MVC](xref:mvc/controllers/filters#action-filters), s výjimkou, že je nelze použít na jednotlivé metody obslužné rutiny stránky.

Filtry stránky holicí strojek:

* Spusťte kód po metodě obslužné rutiny, ale dříve, než dojde k vazbě modelu.
* Spusťte kód před spuštěním metody obslužné rutiny po dokončení vazby modelu.
* Spusťte kód po spuštění metody obslužné rutiny.
* Lze implementovat na stránce nebo globálně.
* Nelze použít pro konkrétní metody obslužné rutiny stránky.

Kód lze spustit před spuštěním metody obslužné rutiny pomocí konstruktoru stránky nebo middlewaru, ale pouze filtry Razor Page mají přístup k [httpcontext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.httpcontext?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_HttpContext). Filtry mají [filterContext](/dotnet/api/microsoft.aspnetcore.mvc.filters.filtercontext?view=aspnetcore-2.0) odvozený parametr, `HttpContext`který poskytuje přístup k . Například [ukázka atributu Implement a filter](#ifa) přidá k odpovědi záhlaví, což nelze provést s konstruktory nebo middleware.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/filter/sample/PageFilter) [(jak stáhnout)](xref:index#how-to-download-a-sample)

Filtry Razor Page poskytují následující metody, které lze použít globálně nebo na úrovni stránky:

* Synchronní metody:

  * [OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0) : Voláno po obslužné rutiny metoda byla vybrána, ale před dojde k vazbě modelu.
  * [OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0) : Voláno před spuštěním metody obslužné rutiny po dokončení vazby modelu.
  * [OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0) : Voláno po spuštění metody obslužné rutiny před výsledkem akce.

* Asynchronní metody:

  * [OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0) : Voláno asynchronně po metodě obslužné rutiny, ale dříve, než dojde k vazbě modelu.
  * [OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0) : Voláno asynchronně před vyvoláním metody obslužné rutiny po dokončení vazby modelu.

> [!NOTE]
> Implementujte **synchronní** nebo asynchronní verzi rozhraní filtru, nikoli obojí. Rozhraní framework nejprve zkontroluje, pokud filtr implementuje asynchronní rozhraní a pokud ano, volá to. Pokud ne, volá metody synchronního rozhraní. Pokud jsou implementovány obě rozhraní, jsou volány pouze asynchronní metody. Stejné pravidlo platí pro lokální změny na stránkách, implementovat synchronní nebo asynchronní verzi přepsání, ne obojí.

## <a name="implement-razor-page-filters-globally"></a>Implementovat filtry Razor Page globálně

Následující kód implementuje `IAsyncPageFilter`:

[!code-csharp[Main](filter/sample/PageFilter/Filters/SampleAsyncPageFilter.cs?name=snippet1)]

V předchozím kódu [iLogger](/dotnet/api/microsoft.extensions.logging.ilogger?view=aspnetcore-2.0) není vyžadován. Používá se v ukázce poskytnout informace o trasování pro aplikaci.

Následující kód umožňuje `SampleAsyncPageFilter` ve `Startup` třídě:

[!code-csharp[Main](filter/sample/PageFilter/Startup.cs?name=snippet2&highlight=11)]

Následující kód ukazuje `Startup` kompletní třídu:

[!code-csharp[Main](filter/sample/PageFilter/Startup.cs?name=snippet1)]

Následující kód `AddFolderApplicationModelConvention` volá použít `SampleAsyncPageFilter` pouze stránky v */subFolder*:

[!code-csharp[Main](filter/sample/PageFilter/Startup2.cs?name=snippet2)]

Následující kód implementuje synchronní `IPageFilter`:

[!code-csharp[Main](filter/sample/PageFilter/Filters/SamplePageFilter.cs?name=snippet1)]

Následující kód umožňuje `SamplePageFilter`:

[!code-csharp[Main](filter/sample/PageFilter/StartupSync.cs?name=snippet2&highlight=11)]

## <a name="implement-razor-page-filters-by-overriding-filter-methods"></a>Implementovat filtry Razor Page přepsáním metod filtru

Následující kód přepíše synchronní filtry Razor Page:

[!code-csharp[Main](filter/sample/PageFilter/Pages/Index.cshtml.cs)]

<a name="ifa"></a>

## <a name="implement-a-filter-attribute"></a>Implementace atributu filtru

Vestavěný filtr založený na atributech [OnResultExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncresultfilter.onresultexecutionasync?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Filters_IAsyncResultFilter_OnResultExecutionAsync_Microsoft_AspNetCore_Mvc_Filters_ResultExecutingContext_Microsoft_AspNetCore_Mvc_Filters_ResultExecutionDelegate_) filtr může být podtřídy. Následující filtr přidá k odpovědi záhlaví:

[!code-csharp[Main](filter/sample/PageFilter/Filters/AddHeaderAttribute.cs)]

Následující kód použije `AddHeader` atribut:

[!code-csharp[Main](filter/sample/PageFilter/Pages/Contact.cshtml.cs?name=snippet1)]

Pokyny k přepsání pořadí v [tématu Přepsání výchozího pořadí](xref:mvc/controllers/filters#overriding-the-default-order) naleznete v tématu Přepsání pořadí.

Pokyny ke zkratování potrubí filtru z filtru naleznete v tématu [Zrušení a zkratování.](xref:mvc/controllers/filters#cancellation-and-short-circuiting) 

<a name="auth"></a>

## <a name="authorize-filter-attribute"></a>Autorizovat atribut filtru

Atribut [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) lze použít `PageModel`na :

[!code-csharp[Main](filter/sample/PageFilter/Pages/ModelWithAuthFilter.cshtml.cs?highlight=7)]

::: moniker-end
