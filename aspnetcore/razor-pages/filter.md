---
title: Metody filtrování pro Razor Pages v ASP.NET Core
author: Rick-Anderson
description: Zjistěte, jak vytvořit filtr metody pro stránky Razor v ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 04/05/2018
uid: razor-pages/filter
ms.openlocfilehash: 2480e67d251de8f8aecb6c484999c90d0220dd19
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/27/2019
ms.locfileid: "64900891"
---
# <a name="filter-methods-for-razor-pages-in-aspnet-core"></a>Metody filtrování pro Razor Pages v ASP.NET Core

Podle [Rick Anderson](https://twitter.com/RickAndMSFT)

Stránka Razor filtruje [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) a [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) povolit stránky Razor pro spuštění kódu před a po spuštění rutiny stránky Razor. Filtry stránek Razor jsou podobné [filtrů Akce ASP.NET Core MVC](xref:mvc/controllers/filters#action-filters)s výjimkou případů, nejde použít u metody obslužné rutiny jednotlivých stránek. 

Stránka Razor filtry:

* Spuštění kódu po výběru metody obslužné rutiny, ale před provedením vázání modelu.
* Spuštění kódu před spuštěním metody obslužné rutiny, po dokončení vazby modelu.
* Spuštění kódu po provedení metody obslužné rutiny.
* Je možné implementovat na stránce nebo globálně.
* Nejde použít u metody obslužné rutiny konkrétní stránky.

Kód můžete spustit před metodu obslužné rutiny provede pomocí konstruktoru stránky nebo middleware, ale pouze filtry stránky Razor nemají přístup k [HttpContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.httpcontext?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_HttpContext). Filtry nemají [FilterContext](/dotnet/api/microsoft.aspnetcore.mvc.filters.filtercontext?view=aspnetcore-2.0) odvozené parametr, který poskytuje přístup k `HttpContext`. Například [implementovat atribut filtru](#ifa) ukázka přidá hlavičku odpovědi, něco, co nelze provést s konstruktory nebo middlewaru.

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/filter/sample/PageFilter) ([stažení](xref:index#how-to-download-a-sample))

Filtry stránek Razor poskytují následující metody, které se dají aplikovat globálně nebo na úrovni stránky:

* Synchronní metody:

  * [OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0) : Volá se po vybrali metodu obslužné rutiny, ale před modelu dojde k vazby.
  * [OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0) : Voláno před provedením metody obslužné rutiny, po dokončení vazby modelu.
  * [OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0) : Voláno po spuštění metody obslužné rutiny, před výsledku akce.

* Asynchronní metody:

  * [OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0) : Asynchronně volá po výběru metody obslužné rutiny, ale před provedením vázání modelu.
  * [OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0) : Asynchronně volat před vyvoláním metody obslužné rutiny, po dokončení vazby modelu.

> [!NOTE]
> Implementace **buď** synchronní nebo asynchronní verzi rozhraní filtru, ne obojí. Rozhraní framework nejprve zkontroluje a zjistěte, jestli implementuje rozhraní asynchronní filtr, a pokud ano, který volá. Pokud tomu tak není, volá rozhraní synchronní metody. Pokud jsou obě rozhraní implementované, se nazývají asynchronní metody. Stejné pravidlo platí pro přepsání hodnot v stránky, implementovat synchronní nebo asynchronní verze přepsání, ne obojí.

## <a name="implement-razor-page-filters-globally"></a>Globálně implementovat filtry stránek Razor

Následující kód implementuje `IAsyncPageFilter`:

[!code-csharp[Main](filter/sample/PageFilter/Filters/SampleAsyncPageFilter.cs?name=snippet1)]

V předchozím kódu [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger?view=aspnetcore-2.0) se nevyžaduje. V ukázce slouží k poskytnutí informací o trasování pro aplikaci.

V následujícím kódu umožňuje `SampleAsyncPageFilter` v `Startup` třídy:

[!code-csharp[Main](filter/sample/PageFilter/Startup.cs?name=snippet2&highlight=11)]

Následující kód ukazuje kompletní `Startup` třídy:

[!code-csharp[Main](filter/sample/PageFilter/Startup.cs?name=snippet1)]

Následující kód volá `AddFolderApplicationModelConvention` použít `SampleAsyncPageFilter` na pouze stránky */subFolder*:

[!code-csharp[Main](filter/sample/PageFilter/Startup2.cs?name=snippet2)]

Následující kód implementuje synchronní `IPageFilter`:

[!code-csharp[Main](filter/sample/PageFilter/Filters/SamplePageFilter.cs?name=snippet1)]

V následujícím kódu umožňuje `SamplePageFilter`:

[!code-csharp[Main](filter/sample/PageFilter/StartupSync.cs?name=snippet2&highlight=11)]

::: moniker range=">= aspnetcore-2.1"

## <a name="implement-razor-page-filters-by-overriding-filter-methods"></a>Stránka Razor filtry implementace přepsáním metody filtru

Následující kód přepíše synchronní filtry stránky Razor:

[!code-csharp[Main](filter/sample/PageFilter/Pages/Index.cshtml.cs)]

::: moniker-end

<a name="ifa"></a>

## <a name="implement-a-filter-attribute"></a>Implementace atribut filtru

Integrovaný filtr založený na atributu [OnResultExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncresultfilter.onresultexecutionasync?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Filters_IAsyncResultFilter_OnResultExecutionAsync_Microsoft_AspNetCore_Mvc_Filters_ResultExecutingContext_Microsoft_AspNetCore_Mvc_Filters_ResultExecutionDelegate_) filtru můžete má rozčlenit do podtříd. Filtr přidá do odpovědi hlavičku:

[!code-csharp[Main](filter/sample/PageFilter/Filters/AddHeaderAttribute.cs)]

Následující kód se vztahuje `AddHeader` atribut:

[!code-csharp[Main](filter/sample/PageFilter/Pages/Contact.cshtml.cs?name=snippet1)]

Zobrazit [přepisuje výchozí pořadí](xref:mvc/controllers/filters#overriding-the-default-order) pokyny k přepsání pořadí.

Zobrazit [zrušení a krátký circuiting](xref:mvc/controllers/filters#cancellation-and-short-circuiting) pokyny týkající se zkrácenou filtr kanálu z filtru. 

<a name="auth"></a>

## <a name="authorize-filter-attribute"></a>Povolit atribut filtru

[Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) atribut lze použít `PageModel`:

[!code-csharp[Main](filter/sample/PageFilter/Pages/ModelWithAuthFilter.cshtml.cs?highlight=7)]
