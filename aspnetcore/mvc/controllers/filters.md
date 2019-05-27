---
title: Filtry v ASP.NET Core
author: ardalis
description: Zjistěte, jak filtry fungují a jak je používat v ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 5/08/2019
uid: mvc/controllers/filters
ms.openlocfilehash: cdf121b97396cb23103d49cd141b9ef19b8c0cc6
ms.sourcegitcommit: e1623d8279b27ff83d8ad67a1e7ef439259decdf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/25/2019
ms.locfileid: "66223026"
---
# <a name="filters-in-aspnet-core"></a><span data-ttu-id="6c471-103">Filtry v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6c471-103">Filters in ASP.NET Core</span></span>

<span data-ttu-id="6c471-104">Podle [Kirka Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Petr Dykstra](https://github.com/tdykstra/), a [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="6c471-104">By [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="6c471-105">*Filtry* v ASP.NET Core umožňují kód ke spuštění před nebo po určitým fázím v kanálu zpracování požadavků.</span><span class="sxs-lookup"><span data-stu-id="6c471-105">*Filters* in ASP.NET Core allow code to be run before or after specific stages in the request processing pipeline.</span></span>

<span data-ttu-id="6c471-106">Integrované filtry naložit s úkoly, jako:</span><span class="sxs-lookup"><span data-stu-id="6c471-106">Built-in filters handle tasks such as:</span></span>

* <span data-ttu-id="6c471-107">Autorizace (zabránění přístupu k prostředkům, které uživatel nemá oprávnění pro).</span><span class="sxs-lookup"><span data-stu-id="6c471-107">Authorization (preventing access to resources a user isn't authorized for).</span></span>
* <span data-ttu-id="6c471-108">Odpověď do mezipaměti (krátký cyklus kanál požadavku na vrátí odpověď uložená v mezipaměti).</span><span class="sxs-lookup"><span data-stu-id="6c471-108">Response caching (short-circuiting the request pipeline to return a cached response).</span></span>

<span data-ttu-id="6c471-109">Vlastní filtry lze vytvořit pro zpracování vyskytující aspekty.</span><span class="sxs-lookup"><span data-stu-id="6c471-109">Custom filters can be created to handle cross-cutting concerns.</span></span> <span data-ttu-id="6c471-110">Příklady související aspekty: zpracování chyb, ukládání do mezipaměti, konfiguraci, ověření a protokolování.</span><span class="sxs-lookup"><span data-stu-id="6c471-110">Examples of cross-cutting concerns include error handling, caching, configuration, authorization, and logging.</span></span>  <span data-ttu-id="6c471-111">Filtry předejít duplikování kódu.</span><span class="sxs-lookup"><span data-stu-id="6c471-111">Filters avoid duplicating code.</span></span> <span data-ttu-id="6c471-112">Zpracování filtru výjimek chyby může například konsolidovat zpracování chyb.</span><span class="sxs-lookup"><span data-stu-id="6c471-112">For example, an error handling exception filter could consolidate error handling.</span></span>

<span data-ttu-id="6c471-113">Tento dokument se vztahuje na stránky Razor, kontrolery rozhraní API a řadiče se zobrazeními.</span><span class="sxs-lookup"><span data-stu-id="6c471-113">This document applies to Razor Pages, API controllers, and controllers with views.</span></span>

<span data-ttu-id="6c471-114">[Zobrazení nebo stažení ukázky](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample) ([stažení](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="6c471-114">[View or download sample](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="how-filters-work"></a><span data-ttu-id="6c471-115">Jak fungují filtry</span><span class="sxs-lookup"><span data-stu-id="6c471-115">How filters work</span></span>

<span data-ttu-id="6c471-116">Filtry se spouští v rámci *kanálu vyvolání akce ASP.NET Core*, která se někdy označují jako *filtrovat*.</span><span class="sxs-lookup"><span data-stu-id="6c471-116">Filters run within the *ASP.NET Core action invocation pipeline*, sometimes referred to as the *filter pipeline*.</span></span>  <span data-ttu-id="6c471-117">Spuštění kanálu filtru po ASP.NET Core vybere akci pro spuštění.</span><span class="sxs-lookup"><span data-stu-id="6c471-117">The filter pipeline runs after ASP.NET Core selects the action to execute.</span></span>

![Další Middleware, směrování Middleware, výběr akce a vyvolání kanálu ASP.NET Core akce zpracování žádosti.](filters/_static/filter-pipeline-1.png)

### <a name="filter-types"></a><span data-ttu-id="6c471-120">Typy filtrů</span><span class="sxs-lookup"><span data-stu-id="6c471-120">Filter types</span></span>

<span data-ttu-id="6c471-121">Různé fáze v kanálu filtr spuštění jednotlivých typů filtrů:</span><span class="sxs-lookup"><span data-stu-id="6c471-121">Each filter type is executed at a different stage in the filter pipeline:</span></span>

* <span data-ttu-id="6c471-122">[Filtry autorizace](#authorization-filters) spouští jako první a slouží k určení, zda je uživatel autorizován pro daný požadavek.</span><span class="sxs-lookup"><span data-stu-id="6c471-122">[Authorization filters](#authorization-filters) run first and are used to determine whether the user is authorized for the request.</span></span> <span data-ttu-id="6c471-123">Filtry autorizace zkrácenou kanálu, pokud žádost neoprávněná.</span><span class="sxs-lookup"><span data-stu-id="6c471-123">Authorization filters short-circuit the pipeline if the request is unauthorized.</span></span>

* <span data-ttu-id="6c471-124">[Filtry prostředků](#resource-filters):</span><span class="sxs-lookup"><span data-stu-id="6c471-124">[Resource filters](#resource-filters):</span></span>

  * <span data-ttu-id="6c471-125">Po povolení spusťte.</span><span class="sxs-lookup"><span data-stu-id="6c471-125">Run after authorization.</span></span>  
  * <span data-ttu-id="6c471-126"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> můžete spustit kód před filtr zbytku.</span><span class="sxs-lookup"><span data-stu-id="6c471-126"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> can run code before the rest of the filter pipeline.</span></span> <span data-ttu-id="6c471-127">Například `OnResourceExecuting` může spustit kód před navázáním modelu.</span><span class="sxs-lookup"><span data-stu-id="6c471-127">For example, `OnResourceExecuting` can run code before model binding.</span></span>
  * <span data-ttu-id="6c471-128"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> můžete spustit kód po dokončení rest z kanálu.</span><span class="sxs-lookup"><span data-stu-id="6c471-128"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> can run code after the rest of the pipeline has completed.</span></span>

* <span data-ttu-id="6c471-129">[Filtry akcí](#action-filters) může spustit kód bezprostředně před a po volání metody jednotlivé akce.</span><span class="sxs-lookup"><span data-stu-id="6c471-129">[Action filters](#action-filters) can run code immediately before and after an individual action method is called.</span></span> <span data-ttu-id="6c471-130">Můžete být použít k manipulaci s argumenty předané do akce a výsledek vrácený z akce.</span><span class="sxs-lookup"><span data-stu-id="6c471-130">They can be used to manipulate the arguments passed into an action and the result returned from the action.</span></span> <span data-ttu-id="6c471-131">Filtry akce jsou **není** podporované v stránky Razor.</span><span class="sxs-lookup"><span data-stu-id="6c471-131">Action filters are **not** supported in Razor Pages.</span></span>

* <span data-ttu-id="6c471-132">[Filtry výjimek](#exception-filters) umožňují použití globálních zásad pro neošetřených výjimek, ke kterým dojde před nic se zapsala do datové části odpovědi.</span><span class="sxs-lookup"><span data-stu-id="6c471-132">[Exception filters](#exception-filters) are used to apply global policies to unhandled exceptions that occur before anything has been written to the response body.</span></span>

* <span data-ttu-id="6c471-133">[Výsledek filtry](#result-filters) může spustit kód bezprostředně před a po spuštění výsledky jednotlivých akcí.</span><span class="sxs-lookup"><span data-stu-id="6c471-133">[Result filters](#result-filters) can run code immediately before and after the execution of individual action results.</span></span> <span data-ttu-id="6c471-134">Spouštějí se pouze v případě, že byl úspěšně proveden metody akce.</span><span class="sxs-lookup"><span data-stu-id="6c471-134">They run only when the action method has executed successfully.</span></span> <span data-ttu-id="6c471-135">Jsou užitečné pro logiku, která se musí před a za spuštění zobrazení nebo formátovacího modulu.</span><span class="sxs-lookup"><span data-stu-id="6c471-135">They are useful for logic that must surround view or formatter execution.</span></span>

<span data-ttu-id="6c471-136">Následující diagram znázorňuje interakci typy filtr v kanálu filtru.</span><span class="sxs-lookup"><span data-stu-id="6c471-136">The following diagram shows how filter types interact in the filter pipeline.</span></span>

![Žádost se zpracovává prostřednictvím filtry autorizace, prostředků filtry, vazby modelu, filtry akce, provedení akce a převod výsledek akce, filtry výjimek, filtry výsledků a výsledek spuštění.](filters/_static/filter-pipeline-2.png)

## <a name="implementation"></a><span data-ttu-id="6c471-139">Implementace</span><span class="sxs-lookup"><span data-stu-id="6c471-139">Implementation</span></span>

<span data-ttu-id="6c471-140">Filtrů podporuje synchronní a asynchronní implementace prostřednictvím různých rozhraní definice.</span><span class="sxs-lookup"><span data-stu-id="6c471-140">Filters support both synchronous and asynchronous implementations through different interface definitions.</span></span>

<span data-ttu-id="6c471-141">Synchronní filtry můžete spuštění kódu před (`On-Stage-Executing`) a po provedení (`On-Stage-Executed`) jejich fázi zřetězení.</span><span class="sxs-lookup"><span data-stu-id="6c471-141">Synchronous filters can run code before (`On-Stage-Executing`) and after (`On-Stage-Executed`) their pipeline stage.</span></span> <span data-ttu-id="6c471-142">Například `OnActionExecuting` je volána před voláním metody akce.</span><span class="sxs-lookup"><span data-stu-id="6c471-142">For example, `OnActionExecuting` is called before the action method is called.</span></span> <span data-ttu-id="6c471-143">`OnActionExecuted` je volána po vrácení metody akce.</span><span class="sxs-lookup"><span data-stu-id="6c471-143">`OnActionExecuted` is called after the action method returns.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="6c471-144">Definování asynchronní filtry `On-Stage-ExecutionAsync` metody:</span><span class="sxs-lookup"><span data-stu-id="6c471-144">Asynchronous filters define an `On-Stage-ExecutionAsync` method:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/SampleAsyncActionFilter.cs?name=snippet)]

<span data-ttu-id="6c471-145">V předchozím kódu `SampleAsyncActionFilter` má <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`), který provede metodu akce.</span><span class="sxs-lookup"><span data-stu-id="6c471-145">In the preceding code, the `SampleAsyncActionFilter` has an <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`)  that executes the action method.</span></span>  <span data-ttu-id="6c471-146">Každá z `On-Stage-ExecutionAsync` metody přijímají `FilterType-ExecutionDelegate` fázi zřetězení tohoto filtru, který se spustí.</span><span class="sxs-lookup"><span data-stu-id="6c471-146">Each of the `On-Stage-ExecutionAsync` methods take a `FilterType-ExecutionDelegate` that executes the filter's pipeline stage.</span></span>

### <a name="multiple-filter-stages"></a><span data-ttu-id="6c471-147">Více fázích filtru</span><span class="sxs-lookup"><span data-stu-id="6c471-147">Multiple filter stages</span></span>

<span data-ttu-id="6c471-148">Rozhraní pro více fázích filtru je možné implementovat do jedné třídy.</span><span class="sxs-lookup"><span data-stu-id="6c471-148">Interfaces for multiple filter stages can be implemented in a single class.</span></span> <span data-ttu-id="6c471-149">Například <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> implementuje třída `IActionFilter`, `IResultFilter`a jejich ekvivalenty asynchronní.</span><span class="sxs-lookup"><span data-stu-id="6c471-149">For example, the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> class implements `IActionFilter`, `IResultFilter`, and their async equivalents.</span></span>

<span data-ttu-id="6c471-150">Implementace **buď** synchronní nebo asynchronní verze rozhraní filtru, **není** obojí.</span><span class="sxs-lookup"><span data-stu-id="6c471-150">Implement **either** the synchronous or the async version of a filter interface, **not** both.</span></span> <span data-ttu-id="6c471-151">Modul runtime nejprve zkontroluje zobrazit, pokud filtr, implementuje rozhraní asynchronní, a pokud ano, který volá.</span><span class="sxs-lookup"><span data-stu-id="6c471-151">The runtime checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="6c471-152">Pokud tomu tak není, volá rozhraní synchronní metody.</span><span class="sxs-lookup"><span data-stu-id="6c471-152">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="6c471-153">Pokud se synchronní a asynchronní rozhraní jsou implementovány v jedné třídy, se nazývá asynchronní metody.</span><span class="sxs-lookup"><span data-stu-id="6c471-153">If both asynchronous and synchronous interfaces are implemented in one class, only the async method is called.</span></span> <span data-ttu-id="6c471-154">Při používání abstraktních tříd jako <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> přepsání pouze metody synchronní nebo asynchronní metody pro každý typ filtru.</span><span class="sxs-lookup"><span data-stu-id="6c471-154">When using abstract classes like <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> override only the synchronous methods or the async method for each filter type.</span></span>

### <a name="built-in-filter-attributes"></a><span data-ttu-id="6c471-155">Integrovaný filtr atributy</span><span class="sxs-lookup"><span data-stu-id="6c471-155">Built-in filter attributes</span></span>

<span data-ttu-id="6c471-156">ASP.NET Core zahrnuje integrované filtry založených na atributech, které mohou být rozčlenění a přizpůsobit.</span><span class="sxs-lookup"><span data-stu-id="6c471-156">ASP.NET Core includes built-in attribute-based filters that can be subclassed and customized.</span></span> <span data-ttu-id="6c471-157">Například následující filtr výsledků přidá do odpovědi hlavičku:</span><span class="sxs-lookup"><span data-stu-id="6c471-157">For example, the following result filter adds a header to the response:</span></span>

<a name="add-header-attribute"></a>

[!code-csharp[](./filters/sample/FiltersSample/Filters/AddHeaderAttribute.cs?name=snippet)]

<span data-ttu-id="6c471-158">Atributy Povolit filtry, které přijímají argumenty, jak je znázorněno v předchozím příkladu.</span><span class="sxs-lookup"><span data-stu-id="6c471-158">Attributes allow filters to accept arguments, as shown in the preceding example.</span></span> <span data-ttu-id="6c471-159">Použít `AddHeaderAttribute` metodě kontroler nebo akce a zadejte název a hodnotu hlavičky protokolu HTTP:</span><span class="sxs-lookup"><span data-stu-id="6c471-159">Apply the `AddHeaderAttribute` to a controller or action method and specify the name and value of the HTTP header:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

<!-- `https://localhost:5001/Sample` -->

<span data-ttu-id="6c471-160">Některé z rozhraní filtru mít odpovídající atributy, které lze použít jako základní třídy pro vlastní implementace.</span><span class="sxs-lookup"><span data-stu-id="6c471-160">Several of the filter interfaces have corresponding attributes that can be used as base classes for custom implementations.</span></span>

<span data-ttu-id="6c471-161">Atributy filtru:</span><span class="sxs-lookup"><span data-stu-id="6c471-161">Filter attributes:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ExceptionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>

## <a name="filter-scopes-and-order-of-execution"></a><span data-ttu-id="6c471-162">Filtr oborů a pořadí provádění</span><span class="sxs-lookup"><span data-stu-id="6c471-162">Filter scopes and order of execution</span></span>

<span data-ttu-id="6c471-163">Filtr lze přidat do kanálu na jeden ze tří *obory*:</span><span class="sxs-lookup"><span data-stu-id="6c471-163">A filter can be added to the pipeline at one of three *scopes*:</span></span>

* <span data-ttu-id="6c471-164">Pomocí atributu na akci.</span><span class="sxs-lookup"><span data-stu-id="6c471-164">Using an attribute on an action.</span></span>
* <span data-ttu-id="6c471-165">Pomocí atributu na kontroleru.</span><span class="sxs-lookup"><span data-stu-id="6c471-165">Using an attribute on a controller.</span></span>
* <span data-ttu-id="6c471-166">Globálně pro všechny kontrolery a akce, jak je znázorněno v následujícím kódu:</span><span class="sxs-lookup"><span data-stu-id="6c471-166">Globally for all controllers and actions as shown in the following code:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/StartupGF.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="6c471-167">Předchozí kód přidá globálně pomocí tří filtry [MvcOptions.Filters](xref:Microsoft.AspNetCore.Mvc.MvcOptions.Filters) kolekce.</span><span class="sxs-lookup"><span data-stu-id="6c471-167">The preceding code adds three filters globally using the [MvcOptions.Filters](xref:Microsoft.AspNetCore.Mvc.MvcOptions.Filters) collection.</span></span>

### <a name="default-order-of-execution"></a><span data-ttu-id="6c471-168">Výchozí pořadí provádění</span><span class="sxs-lookup"><span data-stu-id="6c471-168">Default order of execution</span></span>

<span data-ttu-id="6c471-169">Pokud jsou použity více filtry pro konkrétní fázi kanálu, obor Určuje výchozí pořadí spuštění filtrů.</span><span class="sxs-lookup"><span data-stu-id="6c471-169">When there are multiple filters for a particular stage of the pipeline, scope determines the default order of filter execution.</span></span>  <span data-ttu-id="6c471-170">Globální filtry před a za třídy filtrů, které pak před a za metoda filtry.</span><span class="sxs-lookup"><span data-stu-id="6c471-170">Global filters surround class filters, which in turn surround method filters.</span></span>

<span data-ttu-id="6c471-171">V důsledku filtru vnoření *po* spuštění kódu filtrů v obráceném pořadí *před* kódu.</span><span class="sxs-lookup"><span data-stu-id="6c471-171">As a result of filter nesting, the *after* code of filters runs in the reverse order of the *before* code.</span></span> <span data-ttu-id="6c471-172">Pořadí filtru:</span><span class="sxs-lookup"><span data-stu-id="6c471-172">The filter sequence:</span></span>

* <span data-ttu-id="6c471-173">*Před* kód globálních filtrů.</span><span class="sxs-lookup"><span data-stu-id="6c471-173">The *before* code of global filters.</span></span>
  * <span data-ttu-id="6c471-174">*Před* kód filtrů kontroleru.</span><span class="sxs-lookup"><span data-stu-id="6c471-174">The *before* code of controller filters.</span></span>
    * <span data-ttu-id="6c471-175">*Před* kód filtrů metody akce.</span><span class="sxs-lookup"><span data-stu-id="6c471-175">The *before* code of action method filters.</span></span>
    * <span data-ttu-id="6c471-176">*Po* kód filtrů metody akce.</span><span class="sxs-lookup"><span data-stu-id="6c471-176">The *after* code of action method filters.</span></span>
  * <span data-ttu-id="6c471-177">*Po* kód filtrů kontroleru.</span><span class="sxs-lookup"><span data-stu-id="6c471-177">The *after* code of controller filters.</span></span>
* <span data-ttu-id="6c471-178">*Po* kód globálních filtrů.</span><span class="sxs-lookup"><span data-stu-id="6c471-178">The *after* code of global filters.</span></span>
  
<span data-ttu-id="6c471-179">Následující příklad, který znázorňuje pořadí, ve které filtru jsou volány metody pro synchronní akce filtry.</span><span class="sxs-lookup"><span data-stu-id="6c471-179">The following example that illustrates the order in which filter methods are called for synchronous action filters.</span></span>

| <span data-ttu-id="6c471-180">Sequence</span><span class="sxs-lookup"><span data-stu-id="6c471-180">Sequence</span></span> | <span data-ttu-id="6c471-181">Obor filtru</span><span class="sxs-lookup"><span data-stu-id="6c471-181">Filter scope</span></span> | <span data-ttu-id="6c471-182">Filter – metoda</span><span class="sxs-lookup"><span data-stu-id="6c471-182">Filter method</span></span> |
|:--------:|:------------:|:-------------:|
| <span data-ttu-id="6c471-183">1</span><span class="sxs-lookup"><span data-stu-id="6c471-183">1</span></span> | <span data-ttu-id="6c471-184">Globální</span><span class="sxs-lookup"><span data-stu-id="6c471-184">Global</span></span> | `OnActionExecuting` |
| <span data-ttu-id="6c471-185">2</span><span class="sxs-lookup"><span data-stu-id="6c471-185">2</span></span> | <span data-ttu-id="6c471-186">Kontroler</span><span class="sxs-lookup"><span data-stu-id="6c471-186">Controller</span></span> | `OnActionExecuting` |
| <span data-ttu-id="6c471-187">3</span><span class="sxs-lookup"><span data-stu-id="6c471-187">3</span></span> | <span data-ttu-id="6c471-188">Metoda</span><span class="sxs-lookup"><span data-stu-id="6c471-188">Method</span></span> | `OnActionExecuting` |
| <span data-ttu-id="6c471-189">4</span><span class="sxs-lookup"><span data-stu-id="6c471-189">4</span></span> | <span data-ttu-id="6c471-190">Metoda</span><span class="sxs-lookup"><span data-stu-id="6c471-190">Method</span></span> | `OnActionExecuted` |
| <span data-ttu-id="6c471-191">5</span><span class="sxs-lookup"><span data-stu-id="6c471-191">5</span></span> | <span data-ttu-id="6c471-192">Kontroler</span><span class="sxs-lookup"><span data-stu-id="6c471-192">Controller</span></span> | `OnActionExecuted` |
| <span data-ttu-id="6c471-193">6</span><span class="sxs-lookup"><span data-stu-id="6c471-193">6</span></span> | <span data-ttu-id="6c471-194">Globální</span><span class="sxs-lookup"><span data-stu-id="6c471-194">Global</span></span> | `OnActionExecuted` |

<span data-ttu-id="6c471-195">Zobrazí se toto pořadí:</span><span class="sxs-lookup"><span data-stu-id="6c471-195">This sequence shows:</span></span>

* <span data-ttu-id="6c471-196">Metoda filtr je vnořená v rámci kontroleru filtru.</span><span class="sxs-lookup"><span data-stu-id="6c471-196">The method filter is nested within the controller filter.</span></span>
* <span data-ttu-id="6c471-197">Filtr kontroleru je vnořená v rámci globálních filtrů.</span><span class="sxs-lookup"><span data-stu-id="6c471-197">The controller filter is nested within the global filter.</span></span>

### <a name="controller-and-razor-page-level-filters"></a><span data-ttu-id="6c471-198">Filtry na úrovni kontroleru a stránky Razor</span><span class="sxs-lookup"><span data-stu-id="6c471-198">Controller and Razor Page level filters</span></span>

<span data-ttu-id="6c471-199">Každý kontroler, který dědí z <xref:Microsoft.AspNetCore.Mvc.Controller> základní třída zahrnuje [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*), [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*), a [ Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*) 
 `OnActionExecuted` metody.</span><span class="sxs-lookup"><span data-stu-id="6c471-199">Every controller that inherits from the <xref:Microsoft.AspNetCore.Mvc.Controller> base class includes [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*),  [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*), and [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted` methods.</span></span> <span data-ttu-id="6c471-200">Tyto metody:</span><span class="sxs-lookup"><span data-stu-id="6c471-200">These methods:</span></span>

* <span data-ttu-id="6c471-201">Zabalení, na kterých běží filtry pro danou akci.</span><span class="sxs-lookup"><span data-stu-id="6c471-201">Wrap the filters that run for a given action.</span></span>
* <span data-ttu-id="6c471-202">`OnActionExecuting` je volána před provedením akce filtry.</span><span class="sxs-lookup"><span data-stu-id="6c471-202">`OnActionExecuting` is called before any of the action's filters.</span></span>
* <span data-ttu-id="6c471-203">`OnActionExecuted` je volána po všech filtrů akce.</span><span class="sxs-lookup"><span data-stu-id="6c471-203">`OnActionExecuted` is called after all of the action filters.</span></span>
* <span data-ttu-id="6c471-204">`OnActionExecutionAsync` je volána před provedením akce filtry.</span><span class="sxs-lookup"><span data-stu-id="6c471-204">`OnActionExecutionAsync` is called before any of the action's filters.</span></span> <span data-ttu-id="6c471-205">Kód ve filtru po `next` běží po metodě akce.</span><span class="sxs-lookup"><span data-stu-id="6c471-205">Code in the filter after `next` runs after the action method.</span></span>

<span data-ttu-id="6c471-206">Například v ukázce stahování `MySampleActionFilter` platí globálně ve spuštění.</span><span class="sxs-lookup"><span data-stu-id="6c471-206">For example, in the download sample, `MySampleActionFilter` is applied globally in startup.</span></span>

<span data-ttu-id="6c471-207">`TestController`:</span><span class="sxs-lookup"><span data-stu-id="6c471-207">The `TestController`:</span></span>

* <span data-ttu-id="6c471-208">Se vztahuje `SampleActionFilterAttribute` (`[SampleActionFilter]`) k `FilterTest2` akce:</span><span class="sxs-lookup"><span data-stu-id="6c471-208">Applies the `SampleActionFilterAttribute` (`[SampleActionFilter]`) to the `FilterTest2` action:</span></span>
* <span data-ttu-id="6c471-209">Přepíše `OnActionExecuting` a `OnActionExecuted`.</span><span class="sxs-lookup"><span data-stu-id="6c471-209">Overrides `OnActionExecuting` and `OnActionExecuted`.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/TestController.cs?name=snippet)]

<span data-ttu-id="6c471-210">Přejděte na `https://localhost:5001/Test/FilterTest2` spustí následující kód:</span><span class="sxs-lookup"><span data-stu-id="6c471-210">Navigating to `https://localhost:5001/Test/FilterTest2` runs the following code:</span></span>

* `TestController.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `SampleActionFilterAttribute.OnActionExecuting`
      * `TestController.FilterTest2`
    * `SampleActionFilterAttribute.OnActionExecuted`
  * `MySampleActionFilter.OnActionExecuted`
* `TestController.OnActionExecuted`

<span data-ttu-id="6c471-211">Stránky Razor, naleznete v tématu [filtry stránky Razor implementace přepsáním metody filtr](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span><span class="sxs-lookup"><span data-stu-id="6c471-211">For Razor Pages, see [Implement Razor Page filters by overriding filter methods](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span></span>

### <a name="overriding-the-default-order"></a><span data-ttu-id="6c471-212">Přepsání výchozího pořadí</span><span class="sxs-lookup"><span data-stu-id="6c471-212">Overriding the default order</span></span>

<span data-ttu-id="6c471-213">Výchozí pořadí provádění lze přepsat pomocí implementace <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span><span class="sxs-lookup"><span data-stu-id="6c471-213">The default sequence of execution can be overridden by implementing <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span></span> <span data-ttu-id="6c471-214">`IOrderedFilter` Zpřístupňuje <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> vlastnost, která má přednost před obor pro určení pořadí spouštění.</span><span class="sxs-lookup"><span data-stu-id="6c471-214">`IOrderedFilter` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> property that takes precedence over scope to determine the order of execution.</span></span> <span data-ttu-id="6c471-215">Filtr s nižší `Order` hodnotu:</span><span class="sxs-lookup"><span data-stu-id="6c471-215">A filter with a lower `Order` value:</span></span>

* <span data-ttu-id="6c471-216">Spuštění *před* kódu před inicializací filtr s vyšší hodnotou `Order`.</span><span class="sxs-lookup"><span data-stu-id="6c471-216">Runs the *before* code before that of a filter with a higher value of `Order`.</span></span>
* <span data-ttu-id="6c471-217">Spuštění *po* kódu po tomto filtr s vyšší `Order` hodnotu.</span><span class="sxs-lookup"><span data-stu-id="6c471-217">Runs the *after* code after that of a filter with a higher `Order` value.</span></span>

<span data-ttu-id="6c471-218">`Order` Vlastnost lze nastavit pomocí parametru konstruktoru:</span><span class="sxs-lookup"><span data-stu-id="6c471-218">The `Order` property can be set with a constructor parameter:</span></span>

```csharp
[MyFilter(Name = "Controller Level Attribute", Order=1)]
```

<span data-ttu-id="6c471-219">Vezměte v úvahu stejné filtry akce 3 v předchozím příkladu.</span><span class="sxs-lookup"><span data-stu-id="6c471-219">Consider the same 3 action filters shown in the preceding example.</span></span> <span data-ttu-id="6c471-220">Pokud `Order` řadiče a globální filtry je nastavena na 1 a 2 v uvedeném pořadí, je obrácený pořadí provádění.</span><span class="sxs-lookup"><span data-stu-id="6c471-220">If the `Order` property of the controller and global filters is set to 1 and 2 respectively, the order of execution is reversed.</span></span>

| <span data-ttu-id="6c471-221">Sequence</span><span class="sxs-lookup"><span data-stu-id="6c471-221">Sequence</span></span> | <span data-ttu-id="6c471-222">Obor filtru</span><span class="sxs-lookup"><span data-stu-id="6c471-222">Filter scope</span></span> | <span data-ttu-id="6c471-223">`Order` Vlastnost</span><span class="sxs-lookup"><span data-stu-id="6c471-223">`Order` property</span></span> | <span data-ttu-id="6c471-224">Filter – metoda</span><span class="sxs-lookup"><span data-stu-id="6c471-224">Filter method</span></span> |
|:--------:|:------------:|:-----------------:|:-------------:|
| <span data-ttu-id="6c471-225">1</span><span class="sxs-lookup"><span data-stu-id="6c471-225">1</span></span> | <span data-ttu-id="6c471-226">Metoda</span><span class="sxs-lookup"><span data-stu-id="6c471-226">Method</span></span> | <span data-ttu-id="6c471-227">0</span><span class="sxs-lookup"><span data-stu-id="6c471-227">0</span></span> | `OnActionExecuting` |
| <span data-ttu-id="6c471-228">2</span><span class="sxs-lookup"><span data-stu-id="6c471-228">2</span></span> | <span data-ttu-id="6c471-229">Kontroler</span><span class="sxs-lookup"><span data-stu-id="6c471-229">Controller</span></span> | <span data-ttu-id="6c471-230">1</span><span class="sxs-lookup"><span data-stu-id="6c471-230">1</span></span>  | `OnActionExecuting` |
| <span data-ttu-id="6c471-231">3</span><span class="sxs-lookup"><span data-stu-id="6c471-231">3</span></span> | <span data-ttu-id="6c471-232">Globální</span><span class="sxs-lookup"><span data-stu-id="6c471-232">Global</span></span> | <span data-ttu-id="6c471-233">2</span><span class="sxs-lookup"><span data-stu-id="6c471-233">2</span></span>  | `OnActionExecuting` |
| <span data-ttu-id="6c471-234">4</span><span class="sxs-lookup"><span data-stu-id="6c471-234">4</span></span> | <span data-ttu-id="6c471-235">Globální</span><span class="sxs-lookup"><span data-stu-id="6c471-235">Global</span></span> | <span data-ttu-id="6c471-236">2</span><span class="sxs-lookup"><span data-stu-id="6c471-236">2</span></span>  | `OnActionExecuted` |
| <span data-ttu-id="6c471-237">5</span><span class="sxs-lookup"><span data-stu-id="6c471-237">5</span></span> | <span data-ttu-id="6c471-238">Kontroler</span><span class="sxs-lookup"><span data-stu-id="6c471-238">Controller</span></span> | <span data-ttu-id="6c471-239">1</span><span class="sxs-lookup"><span data-stu-id="6c471-239">1</span></span>  | `OnActionExecuted` |
| <span data-ttu-id="6c471-240">6</span><span class="sxs-lookup"><span data-stu-id="6c471-240">6</span></span> | <span data-ttu-id="6c471-241">Metoda</span><span class="sxs-lookup"><span data-stu-id="6c471-241">Method</span></span> | <span data-ttu-id="6c471-242">0</span><span class="sxs-lookup"><span data-stu-id="6c471-242">0</span></span>  | `OnActionExecuted` |

<span data-ttu-id="6c471-243">`Order` Vlastnost přepíše obor při určování pořadí, ve které filtry spustit.</span><span class="sxs-lookup"><span data-stu-id="6c471-243">The `Order` property overrides scope when determining the order in which filters run.</span></span> <span data-ttu-id="6c471-244">Filtry jsou řazeny nejdříve podle pořadí a oboru se používá k rozdělení vazby.</span><span class="sxs-lookup"><span data-stu-id="6c471-244">Filters are sorted first by order, then scope is used to break ties.</span></span> <span data-ttu-id="6c471-245">Všechny integrované filtry implementovat `IOrderedFilter` a nastaví výchozí `Order` hodnotu na 0.</span><span class="sxs-lookup"><span data-stu-id="6c471-245">All of the built-in filters implement `IOrderedFilter` and set the default `Order` value to 0.</span></span> <span data-ttu-id="6c471-246">Obor pro integrované filtry, určuje pořadí, není-li `Order` nastaven na nenulovou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="6c471-246">For built-in filters, scope determines order unless `Order` is set to a non-zero value.</span></span>

## <a name="cancellation-and-short-circuiting"></a><span data-ttu-id="6c471-247">Zrušení a krátký cyklus</span><span class="sxs-lookup"><span data-stu-id="6c471-247">Cancellation and short-circuiting</span></span>

<span data-ttu-id="6c471-248">Filtr kanálu můžete short-circuited nastavením <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> vlastnost na <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> parametr předán metodě filtru.</span><span class="sxs-lookup"><span data-stu-id="6c471-248">The filter pipeline can be short-circuited by setting the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> property on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> parameter provided to the filter method.</span></span> <span data-ttu-id="6c471-249">Například následující filtr prostředků zabraňuje rest z kanálu provádění:</span><span class="sxs-lookup"><span data-stu-id="6c471-249">For instance, the following Resource filter prevents the rest of the pipeline from executing:</span></span>

<a name="short-circuiting-resource-filter"></a>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ShortCircuitingResourceFilterAttribute.cs?name=snippet)]

<span data-ttu-id="6c471-250">V následujícím kódu jak `ShortCircuitingResourceFilter` a `AddHeader` cílový filtr `SomeResource` metody akce.</span><span class="sxs-lookup"><span data-stu-id="6c471-250">In the following code, both the `ShortCircuitingResourceFilter` and the `AddHeader` filter target the `SomeResource` action method.</span></span> <span data-ttu-id="6c471-251">`ShortCircuitingResourceFilter`:</span><span class="sxs-lookup"><span data-stu-id="6c471-251">The `ShortCircuitingResourceFilter`:</span></span>

* <span data-ttu-id="6c471-252">Spustí nejdříve, protože je filtr prostředků a `AddHeader` je filtr akce.</span><span class="sxs-lookup"><span data-stu-id="6c471-252">Runs first, because it's a Resource Filter and `AddHeader` is an Action Filter.</span></span>
* <span data-ttu-id="6c471-253">Zkratům rest z kanálu.</span><span class="sxs-lookup"><span data-stu-id="6c471-253">Short-circuits the rest of the pipeline.</span></span>

<span data-ttu-id="6c471-254">Proto `AddHeader` filtr nikdy spuštěn `SomeResource` akce.</span><span class="sxs-lookup"><span data-stu-id="6c471-254">Therefore the `AddHeader` filter never runs for the `SomeResource` action.</span></span> <span data-ttu-id="6c471-255">Toto chování by být stejný Pokud byly použity oba filtry na úrovni metody akce k dispozici `ShortCircuitingResourceFilter` spustil první.</span><span class="sxs-lookup"><span data-stu-id="6c471-255">This behavior would be the same if both filters were applied at the action method level, provided the `ShortCircuitingResourceFilter` ran first.</span></span> <span data-ttu-id="6c471-256">`ShortCircuitingResourceFilter` Spustí první z důvodu jeho typ filtru nebo explicitní použití `Order` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="6c471-256">The `ShortCircuitingResourceFilter` runs first because of its filter type, or by explicit use of `Order` property.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1,9)]

## <a name="dependency-injection"></a><span data-ttu-id="6c471-257">Injektáž závislostí</span><span class="sxs-lookup"><span data-stu-id="6c471-257">Dependency injection</span></span>

<span data-ttu-id="6c471-258">Filtry je možné přidat podle typu nebo instance.</span><span class="sxs-lookup"><span data-stu-id="6c471-258">Filters can be added by type or by instance.</span></span> <span data-ttu-id="6c471-259">Pokud je přidána instance, se pro každý požadavek používá tuto instanci.</span><span class="sxs-lookup"><span data-stu-id="6c471-259">If an instance is added, that instance is used for every request.</span></span> <span data-ttu-id="6c471-260">Pokud se přidá typ, je aktivovat typu.</span><span class="sxs-lookup"><span data-stu-id="6c471-260">If a type is added, it's type-activated.</span></span> <span data-ttu-id="6c471-261">Aktivovat typ filtru znamená, že:</span><span class="sxs-lookup"><span data-stu-id="6c471-261">A type-activated filter means:</span></span>

* <span data-ttu-id="6c471-262">Pro každý požadavek je vytvořena instance.</span><span class="sxs-lookup"><span data-stu-id="6c471-262">An instance is created for each request.</span></span>
* <span data-ttu-id="6c471-263">Všechny závislosti konstruktoru se vyplní podle [injektáž závislostí](xref:fundamentals/dependency-injection) (DI).</span><span class="sxs-lookup"><span data-stu-id="6c471-263">Any constructor dependencies are populated by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span>

<span data-ttu-id="6c471-264">Filtry, které jsou implementovány jako atributy a přidat přímo do třídy kontroleru nebo metody akce nemůže mít konstruktor závislosti poskytované [injektáž závislostí](xref:fundamentals/dependency-injection) (DI).</span><span class="sxs-lookup"><span data-stu-id="6c471-264">Filters that are implemented as attributes and added directly to controller classes or action methods cannot have constructor dependencies provided by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span> <span data-ttu-id="6c471-265">Závislosti konstruktoru nemůže poskytovat DI, protože:</span><span class="sxs-lookup"><span data-stu-id="6c471-265">Constructor dependencies cannot be provided by DI because:</span></span>

* <span data-ttu-id="6c471-266">Atributy musí mít zadané, kde uplatňují jejich parametry konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="6c471-266">Attributes must have their constructor parameters supplied where they're applied.</span></span> 
* <span data-ttu-id="6c471-267">Jedná se omezení fungování atributy.</span><span class="sxs-lookup"><span data-stu-id="6c471-267">This is a limitation of how attributes work.</span></span>

<span data-ttu-id="6c471-268">Následující filtry podpory poskytované DI závislosti konstruktoru:</span><span class="sxs-lookup"><span data-stu-id="6c471-268">The following filters support constructor dependencies provided from DI:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>
* <span data-ttu-id="6c471-269"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implementováno v atributu.</span><span class="sxs-lookup"><span data-stu-id="6c471-269"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implemented on the attribute.</span></span>

<span data-ttu-id="6c471-270">Předchozí filtry můžete použít pro metodu kontroler nebo akce:</span><span class="sxs-lookup"><span data-stu-id="6c471-270">The preceding filters can be applied to a controller or action method:</span></span>

<span data-ttu-id="6c471-271">Protokolovací nástroje jsou k dispozici DI.</span><span class="sxs-lookup"><span data-stu-id="6c471-271">Loggers are available from DI.</span></span> <span data-ttu-id="6c471-272">Ale Vyhněte se vytváření a používání filtrů výhradně pro účely protokolování.</span><span class="sxs-lookup"><span data-stu-id="6c471-272">However, avoid creating and using filters purely for logging purposes.</span></span> <span data-ttu-id="6c471-273">[Vestavěnou architekturou protokolování](xref:fundamentals/logging/index) obvykle poskytuje toho, co je potřeba pro protokolování.</span><span class="sxs-lookup"><span data-stu-id="6c471-273">The [built-in framework logging](xref:fundamentals/logging/index) typically provides what's needed for logging.</span></span> <span data-ttu-id="6c471-274">Přidat do filtrů protokolování:</span><span class="sxs-lookup"><span data-stu-id="6c471-274">Logging added to filters:</span></span>

* <span data-ttu-id="6c471-275">Byste se zaměřit na aspekty obchodní domény nebo chování specifické pro filtr.</span><span class="sxs-lookup"><span data-stu-id="6c471-275">Should focus on business domain concerns or behavior specific to the filter.</span></span>
* <span data-ttu-id="6c471-276">By měl **není** protokolu akcí nebo jiné rozhraní události.</span><span class="sxs-lookup"><span data-stu-id="6c471-276">Should **not** log actions or other framework events.</span></span> <span data-ttu-id="6c471-277">Integrované filtry akce a framework události protokolu.</span><span class="sxs-lookup"><span data-stu-id="6c471-277">The built in filters log actions and framework events.</span></span>

### <a name="servicefilterattribute"></a><span data-ttu-id="6c471-278">ServiceFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="6c471-278">ServiceFilterAttribute</span></span>

<span data-ttu-id="6c471-279">Tyto typy implementace filtr služby jsou registrované ve `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="6c471-279">Service filter implementation types are registered in `ConfigureServices`.</span></span> <span data-ttu-id="6c471-280">A <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> načte z DI instance filtru.</span><span class="sxs-lookup"><span data-stu-id="6c471-280">A <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> retrieves an instance of the filter from DI.</span></span>

<span data-ttu-id="6c471-281">Následující kód ukazuje `AddHeaderResultServiceFilter`:</span><span class="sxs-lookup"><span data-stu-id="6c471-281">The following code shows the `AddHeaderResultServiceFilter`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="6c471-282">V následujícím kódu `AddHeaderResultServiceFilter` se přidá do kontejnerů DI:</span><span class="sxs-lookup"><span data-stu-id="6c471-282">In the following code, `AddHeaderResultServiceFilter` is added to the DI container:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

<span data-ttu-id="6c471-283">V následujícím kódu `ServiceFilter` atribut načte instanci `AddHeaderResultServiceFilter` filtr ze sloupce DI:</span><span class="sxs-lookup"><span data-stu-id="6c471-283">In the following code, the `ServiceFilter` attribute retrieves an instance of the `AddHeaderResultServiceFilter` filter from DI:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_ServiceFilter&highlight=1)]

<span data-ttu-id="6c471-284">[ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span><span class="sxs-lookup"><span data-stu-id="6c471-284">[ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span></span>

* <span data-ttu-id="6c471-285">Poskytuje nápovědu, která instance filtru *může* znovu použít mimo byl vytvořen v rámci oboru požadavku.</span><span class="sxs-lookup"><span data-stu-id="6c471-285">Provides a hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="6c471-286">Modul runtime ASP.NET Core nezaručuje:</span><span class="sxs-lookup"><span data-stu-id="6c471-286">The ASP.NET Core runtime doesn't guarantee:</span></span>

  * <span data-ttu-id="6c471-287">Vytvoří se jedna instance filtru.</span><span class="sxs-lookup"><span data-stu-id="6c471-287">That a single instance of the filter will be created.</span></span>
  * <span data-ttu-id="6c471-288">Filtr nebudou vyžádána znovu z kontejnerů DI někdy později.</span><span class="sxs-lookup"><span data-stu-id="6c471-288">The filter will not be re-requested from the DI container at some later point.</span></span>

* <span data-ttu-id="6c471-289">Se nemá používat s filtr, který závisí na službách s životností jiného než typu singleton.</span><span class="sxs-lookup"><span data-stu-id="6c471-289">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

 <span data-ttu-id="6c471-290"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implementuje <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span><span class="sxs-lookup"><span data-stu-id="6c471-290"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="6c471-291">`IFilterFactory` Zpřístupňuje <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> metodu pro vytvoření <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span><span class="sxs-lookup"><span data-stu-id="6c471-291">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="6c471-292">`CreateInstance` načte zadaného typu z DI.</span><span class="sxs-lookup"><span data-stu-id="6c471-292">`CreateInstance` loads the specified type from DI.</span></span>

### <a name="typefilterattribute"></a><span data-ttu-id="6c471-293">TypeFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="6c471-293">TypeFilterAttribute</span></span>

<span data-ttu-id="6c471-294"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> je podobný <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, ale jeho typ není přímo z kontejnerů DI přeložit.</span><span class="sxs-lookup"><span data-stu-id="6c471-294"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> is similar to <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, but its type isn't resolved directly from the DI container.</span></span> <span data-ttu-id="6c471-295">Vytvoření instance typu pomocí <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="6c471-295">It instantiates the type by using <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span></span>

<span data-ttu-id="6c471-296">Protože `TypeFilterAttribute` nejsou přímo z kontejnerů DI přeložit typy:</span><span class="sxs-lookup"><span data-stu-id="6c471-296">Because `TypeFilterAttribute` types aren't resolved directly from the DI container:</span></span>

* <span data-ttu-id="6c471-297">Typy, které jsou odkazovány pomocí `TypeFilterAttribute` nemusíte být registrována pomocí kontejnerů DI.</span><span class="sxs-lookup"><span data-stu-id="6c471-297">Types that are referenced using the `TypeFilterAttribute` don't need to be registered with the DI container.</span></span>  <span data-ttu-id="6c471-298">Mají jejich závislosti podle kontejnerů DI splněny.</span><span class="sxs-lookup"><span data-stu-id="6c471-298">They do have their dependencies fulfilled by the DI container.</span></span>
* <span data-ttu-id="6c471-299">`TypeFilterAttribute` Volitelně může přijímat argumenty konstruktoru typu.</span><span class="sxs-lookup"><span data-stu-id="6c471-299">`TypeFilterAttribute` can optionally accept constructor arguments for the type.</span></span>

<span data-ttu-id="6c471-300">Při použití `TypeFilterAttribute`a nastavte `IsReusable` se o nápovědu, která instance filtru *může* znovu použít mimo byl vytvořen v rámci oboru požadavku.</span><span class="sxs-lookup"><span data-stu-id="6c471-300">When using `TypeFilterAttribute`, setting `IsReusable` is a hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="6c471-301">Modul runtime ASP.NET Core poskytuje žádnou záruku, že jedna instance filtru bude vytvořen.</span><span class="sxs-lookup"><span data-stu-id="6c471-301">The ASP.NET Core runtime provides no guarantees that a single instance of the filter will be created.</span></span> <span data-ttu-id="6c471-302">`IsReusable` se nemá používat s filtr, který závisí na službách s životností jiného než typu singleton.</span><span class="sxs-lookup"><span data-stu-id="6c471-302">`IsReusable` should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

<span data-ttu-id="6c471-303">Následující příklad ukazuje, jak předat argumenty typu pomocí `TypeFilterAttribute`:</span><span class="sxs-lookup"><span data-stu-id="6c471-303">The following example shows how to pass arguments to a type using `TypeFilterAttribute`:</span></span>

[!code-csharp[](../../mvc/controllers/filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_TypeFilter&highlight=1,2)]
[!code-csharp[](../../mvc/controllers/filters/sample/FiltersSample/Filters/LogConstantFilter.cs?name=snippet_TypeFilter_Implementation&highlight=6)]

<!-- 
https://localhost:5001/home/hi?name=joe
VS debug window shows 
FiltersSample.Filters.LogConstantFilter:Information: Method 'Hi' called
-->

## <a name="authorization-filters"></a><span data-ttu-id="6c471-304">Filtry autorizace</span><span class="sxs-lookup"><span data-stu-id="6c471-304">Authorization filters</span></span>

<span data-ttu-id="6c471-305">Filtry autorizace:</span><span class="sxs-lookup"><span data-stu-id="6c471-305">Authorization filters:</span></span>

* <span data-ttu-id="6c471-306">Běží první filtry v kanálu filtru.</span><span class="sxs-lookup"><span data-stu-id="6c471-306">Are the first filters run in the filter pipeline.</span></span>
* <span data-ttu-id="6c471-307">Řízení přístupu na metody akce.</span><span class="sxs-lookup"><span data-stu-id="6c471-307">Control access to action methods.</span></span>
* <span data-ttu-id="6c471-308">Máte před metodou, ale ne po metodě.</span><span class="sxs-lookup"><span data-stu-id="6c471-308">Have a before method, but no after method.</span></span>

<span data-ttu-id="6c471-309">Filtry autorizace vyžadují framework vlastní autorizace.</span><span class="sxs-lookup"><span data-stu-id="6c471-309">Custom authorization filters require a custom authorization framework.</span></span> <span data-ttu-id="6c471-310">Preferovat konfigurace zásad autorizace nebo zápis vlastní zásady autorizace přes psaní vlastního filtru.</span><span class="sxs-lookup"><span data-stu-id="6c471-310">Prefer configuring the authorization policies or writing a custom authorization policy over writing a custom filter.</span></span> <span data-ttu-id="6c471-311">Filtr autorizace integrovaná:</span><span class="sxs-lookup"><span data-stu-id="6c471-311">The built-in authorization filter:</span></span>

* <span data-ttu-id="6c471-312">Volání autorizace systému.</span><span class="sxs-lookup"><span data-stu-id="6c471-312">Calls the authorization system.</span></span>
* <span data-ttu-id="6c471-313">Nepovolí požadavky.</span><span class="sxs-lookup"><span data-stu-id="6c471-313">Does not authorize requests.</span></span>

<span data-ttu-id="6c471-314">Proveďte **není** vyvolat výjimky v rámci filtry autorizace:</span><span class="sxs-lookup"><span data-stu-id="6c471-314">Do **not** throw exceptions within authorization filters:</span></span>

* <span data-ttu-id="6c471-315">Tato výjimka není ošetřena.</span><span class="sxs-lookup"><span data-stu-id="6c471-315">The exception will not be handled.</span></span>
* <span data-ttu-id="6c471-316">Filtry výjimek nebude zpracování výjimky.</span><span class="sxs-lookup"><span data-stu-id="6c471-316">Exception filters will not handle the exception.</span></span>

<span data-ttu-id="6c471-317">Zvažte výzvu, když dojde k výjimce v filtr autorizace.</span><span class="sxs-lookup"><span data-stu-id="6c471-317">Consider issuing a challenge when an exception occurs in an authorization filter.</span></span>

<span data-ttu-id="6c471-318">Další informace o [autorizace](xref:security/authorization/introduction).</span><span class="sxs-lookup"><span data-stu-id="6c471-318">Learn more about [Authorization](xref:security/authorization/introduction).</span></span>

## <a name="resource-filters"></a><span data-ttu-id="6c471-319">Filtry prostředků</span><span class="sxs-lookup"><span data-stu-id="6c471-319">Resource filters</span></span>

<span data-ttu-id="6c471-320">Filtry prostředků:</span><span class="sxs-lookup"><span data-stu-id="6c471-320">Resource filters:</span></span>

* <span data-ttu-id="6c471-321">Implementovat buď <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> nebo <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> rozhraní.</span><span class="sxs-lookup"><span data-stu-id="6c471-321">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> interface.</span></span>
* <span data-ttu-id="6c471-322">Spuštění zabalí většinu kanálu filtru.</span><span class="sxs-lookup"><span data-stu-id="6c471-322">Execution wraps most of the filter pipeline.</span></span>
* <span data-ttu-id="6c471-323">Pouze [filtry autorizace](#authorization-filters) spustit před filtry prostředků.</span><span class="sxs-lookup"><span data-stu-id="6c471-323">Only [Authorization filters](#authorization-filters) run before resource filters.</span></span>

<span data-ttu-id="6c471-324">Filtry prostředků jsou užitečné pro zkrácenou většinu kanálu.</span><span class="sxs-lookup"><span data-stu-id="6c471-324">Resource filters are useful to short-circuit most of the pipeline.</span></span> <span data-ttu-id="6c471-325">Například filtr ukládání do mezipaměti se můžete vyhnout zbytku na přístup do mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="6c471-325">For example, a caching filter can avoid the rest of the pipeline on a cache hit.</span></span>

<span data-ttu-id="6c471-326">Příklady prostředků filtru:</span><span class="sxs-lookup"><span data-stu-id="6c471-326">Resource filter examples:</span></span>

* <span data-ttu-id="6c471-327">[Short-circuiting filtr prostředků](#short-circuiting-resource-filter) uvedenému výše.</span><span class="sxs-lookup"><span data-stu-id="6c471-327">[The short-circuiting resource filter](#short-circuiting-resource-filter) shown previously.</span></span>
* <span data-ttu-id="6c471-328">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span><span class="sxs-lookup"><span data-stu-id="6c471-328">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span></span>

  * <span data-ttu-id="6c471-329">Zabraňuje vazby modelu v přístupu k data formuláře.</span><span class="sxs-lookup"><span data-stu-id="6c471-329">Prevents model binding from accessing the form data.</span></span>
  * <span data-ttu-id="6c471-330">Používá se pro nahrávání velkých souborů zabránit načítána do paměti data formuláře.</span><span class="sxs-lookup"><span data-stu-id="6c471-330">Used for large file uploads to prevent the form data from being read into memory.</span></span>

## <a name="action-filters"></a><span data-ttu-id="6c471-331">Filtry akcí</span><span class="sxs-lookup"><span data-stu-id="6c471-331">Action filters</span></span>

> [!IMPORTANT]
> <span data-ttu-id="6c471-332">Filtry akce provést **není** platí pro stránky Razor.</span><span class="sxs-lookup"><span data-stu-id="6c471-332">Action filters do **not** apply to Razor Pages.</span></span> <span data-ttu-id="6c471-333">Stránky Razor podporuje <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> a <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span><span class="sxs-lookup"><span data-stu-id="6c471-333">Razor Pages supports <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span></span> <span data-ttu-id="6c471-334">Další informace najdete v tématu [metody filtrování pro Razor Pages](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="6c471-334">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

<span data-ttu-id="6c471-335">Filtry akcí:</span><span class="sxs-lookup"><span data-stu-id="6c471-335">Action filters:</span></span>

* <span data-ttu-id="6c471-336">Implementovat buď <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> nebo <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> rozhraní.</span><span class="sxs-lookup"><span data-stu-id="6c471-336">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> interface.</span></span>
* <span data-ttu-id="6c471-337">Jejich spuštění obklopuje provádění metody akce.</span><span class="sxs-lookup"><span data-stu-id="6c471-337">Their execution surrounds the execution of action methods.</span></span>

<span data-ttu-id="6c471-338">Následující kód ukazuje ukázkový filtr akce:</span><span class="sxs-lookup"><span data-stu-id="6c471-338">The following code shows a sample action filter:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="6c471-339"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> Poskytuje následující vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="6c471-339">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> provides the following properties:</span></span>

* <span data-ttu-id="6c471-340"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> – umožňuje načíst vstupní hodnoty pro metodu akce.</span><span class="sxs-lookup"><span data-stu-id="6c471-340"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> - enables the inputs to an action method be read.</span></span>
* <span data-ttu-id="6c471-341"><xref:Microsoft.AspNetCore.Mvc.Controller> – umožňuje manipulaci s instance kontroleru.</span><span class="sxs-lookup"><span data-stu-id="6c471-341"><xref:Microsoft.AspNetCore.Mvc.Controller> - enables manipulating the controller instance.</span></span>
* <span data-ttu-id="6c471-342"><xref:System.Web.Mvc.ActionExecutingContext.Result> -nastavení `Result` zkratům provádění metody akce a filtry následné akce.</span><span class="sxs-lookup"><span data-stu-id="6c471-342"><xref:System.Web.Mvc.ActionExecutingContext.Result> - setting `Result` short-circuits execution of the action method and subsequent action filters.</span></span>

<span data-ttu-id="6c471-343">Došlo k výjimce v metodě akce:</span><span class="sxs-lookup"><span data-stu-id="6c471-343">Throwing an exception in an action method:</span></span>

* <span data-ttu-id="6c471-344">Zabraňuje spuštění následujícími filtry.</span><span class="sxs-lookup"><span data-stu-id="6c471-344">Prevents running of subsequent filters.</span></span>
* <span data-ttu-id="6c471-345">Na rozdíl od nastavení `Result`, je považováno za selhání místo úspěšný výsledek.</span><span class="sxs-lookup"><span data-stu-id="6c471-345">Unlike setting `Result`, is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="6c471-346"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> Poskytuje `Controller` a `Result` plus následující vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="6c471-346">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> provides `Controller` and `Result` plus the following properties:</span></span>

* <span data-ttu-id="6c471-347"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> -True, pokud spuštění akce byla zkratována jiný filtr.</span><span class="sxs-lookup"><span data-stu-id="6c471-347"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> - True if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="6c471-348"><xref:System.Web.Mvc.ActionExecutedContext.Exception> Bez-hodnota null, pokud akci nebo filtr dříve spuštění akce došlo k výjimce.</span><span class="sxs-lookup"><span data-stu-id="6c471-348"><xref:System.Web.Mvc.ActionExecutedContext.Exception> - Non-null if the action or a previously run action filter threw an exception.</span></span> <span data-ttu-id="6c471-349">Nastavení této vlastnosti na hodnotu null:</span><span class="sxs-lookup"><span data-stu-id="6c471-349">Setting this property to null:</span></span>

  * <span data-ttu-id="6c471-350">Efektivně ošetří výjimku.</span><span class="sxs-lookup"><span data-stu-id="6c471-350">Effectively handles the exception.</span></span>
  * <span data-ttu-id="6c471-351">`Result` je provedeno, jako kdyby byla vrácena z metody akce.</span><span class="sxs-lookup"><span data-stu-id="6c471-351">`Result` is executed as if it was returned from the action method.</span></span>

<span data-ttu-id="6c471-352">Pro `IAsyncActionFilter`, volání <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span><span class="sxs-lookup"><span data-stu-id="6c471-352">For an `IAsyncActionFilter`, a call to the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span></span>

* <span data-ttu-id="6c471-353">Spustí všechny filtry následné akce a metody akce.</span><span class="sxs-lookup"><span data-stu-id="6c471-353">Executes any subsequent action filters and the action method.</span></span>
* <span data-ttu-id="6c471-354">Vrátí `ActionExecutedContext`.</span><span class="sxs-lookup"><span data-stu-id="6c471-354">Returns `ActionExecutedContext`.</span></span>

<span data-ttu-id="6c471-355">Chcete-li zkrácenou, přiřaďte <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> do výsledku instance a volat `next` ( `ActionExecutionDelegate`).</span><span class="sxs-lookup"><span data-stu-id="6c471-355">To short-circuit, assign <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> to a result instance and don't call `next` (the `ActionExecutionDelegate`).</span></span>

<span data-ttu-id="6c471-356">Rozhraní poskytuje abstraktní <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> , který může být rozčlenit do podtříd.</span><span class="sxs-lookup"><span data-stu-id="6c471-356">The framework provides an abstract <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> that can be subclassed.</span></span>

<span data-ttu-id="6c471-357">`OnActionExecuting` Filtr akce lze provádět:</span><span class="sxs-lookup"><span data-stu-id="6c471-357">The `OnActionExecuting` action filter can be used to:</span></span>

* <span data-ttu-id="6c471-358">Ověření stavu modelu.</span><span class="sxs-lookup"><span data-stu-id="6c471-358">Validate model state.</span></span>
* <span data-ttu-id="6c471-359">Vrátí chybu, pokud stav není platný.</span><span class="sxs-lookup"><span data-stu-id="6c471-359">Return an error if the state is invalid.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet)]

<span data-ttu-id="6c471-360">`OnActionExecuted` Metoda spustí po metodě akce:</span><span class="sxs-lookup"><span data-stu-id="6c471-360">The `OnActionExecuted` method runs after the action method:</span></span>

* <span data-ttu-id="6c471-361">A můžete zobrazit a pracovat s výsledky akce prostřednictvím <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> vlastnost.</span><span class="sxs-lookup"><span data-stu-id="6c471-361">And can see and manipulate the results of the action through the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> property.</span></span>
* <span data-ttu-id="6c471-362"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> je nastavena na hodnotu true, pokud spuštění akce byla zkratována jiný filtr.</span><span class="sxs-lookup"><span data-stu-id="6c471-362"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> is set to true if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="6c471-363"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> je nastavena na hodnotu než null, pokud akci nebo filtr následné akce došlo k výjimce.</span><span class="sxs-lookup"><span data-stu-id="6c471-363"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> is set to a non-null value if the action or a subsequent action filter threw an exception.</span></span> <span data-ttu-id="6c471-364">Nastavení `Exception` na hodnotu null:</span><span class="sxs-lookup"><span data-stu-id="6c471-364">Setting `Exception` to null:</span></span>

  * <span data-ttu-id="6c471-365">Efektivně zpracovává výjimku.</span><span class="sxs-lookup"><span data-stu-id="6c471-365">Effectively handles an exception.</span></span>
  * <span data-ttu-id="6c471-366">`ActionExecutedContext.Result` je provedeno, jako kdyby byly obvykle vrátil z metody akce.</span><span class="sxs-lookup"><span data-stu-id="6c471-366">`ActionExecutedContext.Result` is executed as if it were returned normally from the action method.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet2&higlight=12-99)]

## <a name="exception-filters"></a><span data-ttu-id="6c471-367">Filtry výjimek</span><span class="sxs-lookup"><span data-stu-id="6c471-367">Exception filters</span></span>

<span data-ttu-id="6c471-368">Filtry výjimek:</span><span class="sxs-lookup"><span data-stu-id="6c471-368">Exception filters:</span></span>

* <span data-ttu-id="6c471-369">Implementace <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> nebo <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span><span class="sxs-lookup"><span data-stu-id="6c471-369">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span></span> 
* <span data-ttu-id="6c471-370">Je možné implementovat zásady pro zpracování běžných chyb.</span><span class="sxs-lookup"><span data-stu-id="6c471-370">Can be used to implement common error handling policies.</span></span>

<span data-ttu-id="6c471-371">Následující vzorek filtru výjimek zobrazení vlastní chybové používá k zobrazení podrobností o výjimkách, ke kterým dochází, když je aplikace ve vývoji:</span><span class="sxs-lookup"><span data-stu-id="6c471-371">The following sample exception filter uses a custom error view to display details about exceptions that occur when the app is in development:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/CustomExceptionFilterAttribute.cs?name=snippet_ExceptionFilter&highlight=16-19)]

<span data-ttu-id="6c471-372">Filtry výjimek:</span><span class="sxs-lookup"><span data-stu-id="6c471-372">Exception filters:</span></span>

* <span data-ttu-id="6c471-373">Není k dispozici před a po události.</span><span class="sxs-lookup"><span data-stu-id="6c471-373">Don't have before and after events.</span></span>
* <span data-ttu-id="6c471-374">Implementace <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> nebo <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span><span class="sxs-lookup"><span data-stu-id="6c471-374">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span></span>
* <span data-ttu-id="6c471-375">Zpracování neošetřených výjimek, ke kterým dochází v stránky Razor nebo vytváření řadiče [vazby modelu](xref:mvc/models/model-binding), filtrů Akce nebo metody akce.</span><span class="sxs-lookup"><span data-stu-id="6c471-375">Handle unhandled exceptions that occur in Razor Page or controller creation, [model binding](xref:mvc/models/model-binding), action filters, or action methods.</span></span>
* <span data-ttu-id="6c471-376">Proveďte **není** zachytit výjimky, ke kterým dochází v prostředku filtry, filtry výsledků nebo provedení výsledku MVC.</span><span class="sxs-lookup"><span data-stu-id="6c471-376">Do **not** catch exceptions that occur in resource filters, result filters, or MVC result execution.</span></span>

<span data-ttu-id="6c471-377">Ke zpracování výjimky, nastavte <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> vlastnost `true` nebo zápisu odpovědi.</span><span class="sxs-lookup"><span data-stu-id="6c471-377">To handle an exception, set the <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> property to `true` or write a response.</span></span> <span data-ttu-id="6c471-378">Tím se zastaví šíření výjimky.</span><span class="sxs-lookup"><span data-stu-id="6c471-378">This stops propagation of the exception.</span></span> <span data-ttu-id="6c471-379">Filtr výjimek nelze zapnout výjimku do "ÚSPĚCH".</span><span class="sxs-lookup"><span data-stu-id="6c471-379">An exception filter can't turn an exception into a "success".</span></span> <span data-ttu-id="6c471-380">Pouze filtru akce můžete to udělat.</span><span class="sxs-lookup"><span data-stu-id="6c471-380">Only an action filter can do that.</span></span>

<span data-ttu-id="6c471-381">Filtry výjimek:</span><span class="sxs-lookup"><span data-stu-id="6c471-381">Exception filters:</span></span>

* <span data-ttu-id="6c471-382">Jsou vhodné pro zachytávání výjimek, které se vyskytují v rámci akce.</span><span class="sxs-lookup"><span data-stu-id="6c471-382">Are good for trapping exceptions that occur within actions.</span></span>
* <span data-ttu-id="6c471-383">Nejsou tak flexibilní jako middleware pro zpracování chyb.</span><span class="sxs-lookup"><span data-stu-id="6c471-383">Are not as flexible as error handling middleware.</span></span>

<span data-ttu-id="6c471-384">Preferovat middleware pro zpracování výjimek.</span><span class="sxs-lookup"><span data-stu-id="6c471-384">Prefer middleware for exception handling.</span></span> <span data-ttu-id="6c471-385">Výjimky použijte filtry pouze tehdy, pokud zpracování chyb *se liší* v závislosti na způsobu akce je volána.</span><span class="sxs-lookup"><span data-stu-id="6c471-385">Use exception filters only where error handling *differs* based on which action method is called.</span></span> <span data-ttu-id="6c471-386">Aplikace může mít například metody akci pro oba koncové body rozhraní API a pro zobrazení a HTML.</span><span class="sxs-lookup"><span data-stu-id="6c471-386">For example, an app might have action methods for both API endpoints and for views/HTML.</span></span> <span data-ttu-id="6c471-387">Koncové body rozhraní API můžou se zobrazovat informace o chybě jako dokumenty JSON, zatímco akce na základě zobrazení může vrátit chybovou stránku ve formátu HTML.</span><span class="sxs-lookup"><span data-stu-id="6c471-387">The API endpoints could return error information as JSON, while the view-based actions could return an error page as HTML.</span></span>

## <a name="result-filters"></a><span data-ttu-id="6c471-388">Filtry výsledků</span><span class="sxs-lookup"><span data-stu-id="6c471-388">Result filters</span></span>

<span data-ttu-id="6c471-389">Filtry výsledků:</span><span class="sxs-lookup"><span data-stu-id="6c471-389">Result filters:</span></span>

* <span data-ttu-id="6c471-390">Implementujte rozhraní:</span><span class="sxs-lookup"><span data-stu-id="6c471-390">Implement an interface:</span></span>
  * <span data-ttu-id="6c471-391"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> Nebo <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="6c471-391"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
  * <span data-ttu-id="6c471-392"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> Nebo <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span><span class="sxs-lookup"><span data-stu-id="6c471-392"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span></span>
* <span data-ttu-id="6c471-393">Jejich spuštění obklopuje provádění výsledky akce.</span><span class="sxs-lookup"><span data-stu-id="6c471-393">Their execution surrounds the execution of action results.</span></span>

### <a name="iresultfilter-and-iasyncresultfilter"></a><span data-ttu-id="6c471-394">IResultFilter a IAsyncResultFilter</span><span class="sxs-lookup"><span data-stu-id="6c471-394">IResultFilter and IAsyncResultFilter</span></span>

<span data-ttu-id="6c471-395">Následující kód ukazuje výsledek filtr, který přidá hlavičku protokolu HTTP:</span><span class="sxs-lookup"><span data-stu-id="6c471-395">The following code shows a result filter that adds an HTTP header:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="6c471-396">Typ výsledku prováděný závisí na akci.</span><span class="sxs-lookup"><span data-stu-id="6c471-396">The kind of result being executed depends on the action.</span></span> <span data-ttu-id="6c471-397">Akce vrácení zobrazení bude zahrnovat všechny razor zpracování v rámci <xref:Microsoft.AspNetCore.Mvc.ViewResult> spouštěna.</span><span class="sxs-lookup"><span data-stu-id="6c471-397">An action returning a view would include all razor processing as part of the <xref:Microsoft.AspNetCore.Mvc.ViewResult> being executed.</span></span> <span data-ttu-id="6c471-398">Metodu rozhraní API může provést některé serializace jako součást provedení výsledku.</span><span class="sxs-lookup"><span data-stu-id="6c471-398">An API method might perform some serialization as part of the execution of the result.</span></span> <span data-ttu-id="6c471-399">Další informace o [výsledky akcí](xref:mvc/controllers/actions)</span><span class="sxs-lookup"><span data-stu-id="6c471-399">Learn more about [action results](xref:mvc/controllers/actions)</span></span>

<span data-ttu-id="6c471-400">Filtry výsledků jsou vykonány pouze pro úspěšné výsledky – při akci nebo filtrů akce vytvoří výsledek akce.</span><span class="sxs-lookup"><span data-stu-id="6c471-400">Result filters are only executed for successful results - when the action or action filters produce an action result.</span></span> <span data-ttu-id="6c471-401">Filtry výsledků nejsou provedeny, když filtry výjimek zpracování výjimky.</span><span class="sxs-lookup"><span data-stu-id="6c471-401">Result filters are not executed when exception filters handle an exception.</span></span>

<span data-ttu-id="6c471-402"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> Metoda můžete zkrácenou provedení výsledku akce a filtry následujících výsledků nastavením <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> k `true`.</span><span class="sxs-lookup"><span data-stu-id="6c471-402">The <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> method can short-circuit execution of the action result and subsequent result filters by setting <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> to `true`.</span></span> <span data-ttu-id="6c471-403">Zápis do objektu odpovědi při zkrácení pro zabránění generování odpověď je prázdná.</span><span class="sxs-lookup"><span data-stu-id="6c471-403">Write to the response object when short-circuiting to avoid generating an empty response.</span></span> <span data-ttu-id="6c471-404">Došlo k výjimce `IResultFilter.OnResultExecuting` bude:</span><span class="sxs-lookup"><span data-stu-id="6c471-404">Throwing an exception in `IResultFilter.OnResultExecuting` will:</span></span>

* <span data-ttu-id="6c471-405">Zabraňte spuštění výsledku akce a následné filtry.</span><span class="sxs-lookup"><span data-stu-id="6c471-405">Prevent execution of the action result and subsequent filters.</span></span>
* <span data-ttu-id="6c471-406">Být považován za spadne, místo aby úspěšný výsledek.</span><span class="sxs-lookup"><span data-stu-id="6c471-406">Be treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="6c471-407">Když <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> metoda spuštění:</span><span class="sxs-lookup"><span data-stu-id="6c471-407">When the <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> method runs:</span></span>

* <span data-ttu-id="6c471-408">Odpověď pravděpodobně byl odeslán do klienta a nedá se změnit.</span><span class="sxs-lookup"><span data-stu-id="6c471-408">The response has likely been sent to the client and cannot be changed.</span></span>
* <span data-ttu-id="6c471-409">Pokud došlo k výjimce, nejsou zasílány text odpovědi.</span><span class="sxs-lookup"><span data-stu-id="6c471-409">If an exception was thrown, the response body is not sent.</span></span>

<!-- Review preceding "If an exception was thrown: Original 
When the OnResultExecuted method runs, the response has likely been sent to the client and cannot be changed further (unless an exception was thrown).

SHould that be , 
If an exception was thrown **IN THE RESULT FILTER**, the response body is not sent.

 -->

<span data-ttu-id="6c471-410">`ResultExecutedContext.Canceled` je nastavena na `true` Pokud provedení výsledku akce byla zkratována jiný filtr.</span><span class="sxs-lookup"><span data-stu-id="6c471-410">`ResultExecutedContext.Canceled` is set to `true` if the action result execution was short-circuited by another filter.</span></span>

<span data-ttu-id="6c471-411">`ResultExecutedContext.Exception` je nastavena na hodnotu než null, pokud výsledek akce nebo filtr následné výsledků došlo k výjimce.</span><span class="sxs-lookup"><span data-stu-id="6c471-411">`ResultExecutedContext.Exception` is set to a non-null value if the action result or a subsequent result filter threw an exception.</span></span> <span data-ttu-id="6c471-412">Nastavení `Exception` na null efektivně zpracovává výjimku a brání výjimka z právě znovu ASP.NET Core později v kanálu.</span><span class="sxs-lookup"><span data-stu-id="6c471-412">Setting `Exception` to null effectively handles an exception and prevents the exception from being rethrown by ASP.NET Core later in the pipeline.</span></span> <span data-ttu-id="6c471-413">Neexistuje žádný spolehlivý způsob při zpracování výjimky v filtr výsledků zapsat data do odpovědi.</span><span class="sxs-lookup"><span data-stu-id="6c471-413">There is no reliable way to write data to a response when handling an exception in a result filter.</span></span> <span data-ttu-id="6c471-414">Pokud hlavičky mají klientovi vyprazdňuje, pokud výsledek akce vyvolá výjimku, neexistuje žádný spolehlivý mechanismus poslat kód selhání.</span><span class="sxs-lookup"><span data-stu-id="6c471-414">If the headers have been flushed to the client when an action result throws an exception, there's no reliable mechanism to send a failure code.</span></span>

<span data-ttu-id="6c471-415">Pro <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, volání `await next` na <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> spustí všechny následné výsledek filtry a výsledku akce.</span><span class="sxs-lookup"><span data-stu-id="6c471-415">For an <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, a call to `await next` on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> executes any subsequent result filters and the action result.</span></span> <span data-ttu-id="6c471-416">Zkrácenou, nastavte [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) k `true` a nemůžete volat `ResultExecutionDelegate`:</span><span class="sxs-lookup"><span data-stu-id="6c471-416">To short-circuit, set [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) to `true` and don't call the `ResultExecutionDelegate`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MyAsyncResponseFilter.cs?name=snippet)]

<span data-ttu-id="6c471-417">Rozhraní poskytuje abstraktní `ResultFilterAttribute` , který může být rozčlenit do podtříd.</span><span class="sxs-lookup"><span data-stu-id="6c471-417">The framework provides an abstract `ResultFilterAttribute` that can be subclassed.</span></span> <span data-ttu-id="6c471-418">[AddHeaderAttribute](#add-header-attribute) uvedená třída dříve je příkladem atribut filtru výsledků.</span><span class="sxs-lookup"><span data-stu-id="6c471-418">The [AddHeaderAttribute](#add-header-attribute) class shown previously is an example of a result filter attribute.</span></span>

### <a name="ialwaysrunresultfilter-and-iasyncalwaysrunresultfilter"></a><span data-ttu-id="6c471-419">IAlwaysRunResultFilter a IAsyncAlwaysRunResultFilter</span><span class="sxs-lookup"><span data-stu-id="6c471-419">IAlwaysRunResultFilter and IAsyncAlwaysRunResultFilter</span></span>

<span data-ttu-id="6c471-420"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> a <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> rozhraní deklarovat <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> implementace, která spustí pro všechny výsledky akce.</span><span class="sxs-lookup"><span data-stu-id="6c471-420">The <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> interfaces declare an <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> implementation that runs for all action results.</span></span> <span data-ttu-id="6c471-421">Filtr platí pro všechny výsledky akce, pokud:</span><span class="sxs-lookup"><span data-stu-id="6c471-421">The filter is applied to all action results unless:</span></span>

* <span data-ttu-id="6c471-422"><xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> Nebo <xref:Microsoft.AspNetCore.Mvc.Filters.IAuthorizationFilter> platí a zkratům odpovědi.</span><span class="sxs-lookup"><span data-stu-id="6c471-422">An <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAuthorizationFilter> applies and short-circuits the response.</span></span>
* <span data-ttu-id="6c471-423">Filtr výjimek zpracovává výjimku vytvářením výsledek akce.</span><span class="sxs-lookup"><span data-stu-id="6c471-423">An exception filter handles an exception by producing an action result.</span></span>

<span data-ttu-id="6c471-424">Filtry pro jiné než `IExceptionFilter` a `IAuthorizationFilter` není zkrácenou `IAlwaysRunResultFilter` a `IAsyncAlwaysRunResultFilter`.</span><span class="sxs-lookup"><span data-stu-id="6c471-424">Filters other than `IExceptionFilter` and `IAuthorizationFilter` don't short-circuit `IAlwaysRunResultFilter` and `IAsyncAlwaysRunResultFilter`.</span></span>

<span data-ttu-id="6c471-425">Například následující filtr vždy spustí a nastaví výsledek akce (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) s *422 nezpracované Entity* stavový kód, pokud selže vyjednávání obsahu:</span><span class="sxs-lookup"><span data-stu-id="6c471-425">For example, the following filter always runs and sets an action result (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) with a *422 Unprocessable Entity* status code when content negotiation fails:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/UnprocessableResultFilter.cs?name=snippet)]

### <a name="ifilterfactory"></a><span data-ttu-id="6c471-426">IFilterFactory</span><span class="sxs-lookup"><span data-stu-id="6c471-426">IFilterFactory</span></span>

<span data-ttu-id="6c471-427"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implementuje <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span><span class="sxs-lookup"><span data-stu-id="6c471-427"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span></span> <span data-ttu-id="6c471-428">Proto `IFilterFactory` instance může sloužit jako `IFilterMetadata` instance kdekoli v kanálu filtru.</span><span class="sxs-lookup"><span data-stu-id="6c471-428">Therefore, an `IFilterFactory` instance can be used as an `IFilterMetadata` instance anywhere in the filter pipeline.</span></span> <span data-ttu-id="6c471-429">Když modul runtime se připravuje k vyvolání filtr, pokusí se vysílat na `IFilterFactory`.</span><span class="sxs-lookup"><span data-stu-id="6c471-429">When the runtime prepares to invoke the filter, it attempts to cast it to an `IFilterFactory`.</span></span> <span data-ttu-id="6c471-430">Pokud je úspěšná tohoto přetypování <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> metoda je volána k vytvoření `IFilterMetadata` instanci, která je vyvolána.</span><span class="sxs-lookup"><span data-stu-id="6c471-430">If that cast succeeds, the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method is called to create the `IFilterMetadata` instance that is invoked.</span></span> <span data-ttu-id="6c471-431">To poskytuje flexibilní návrhu, protože kanál přesné filtru nemusí být explicitně nastaveno při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="6c471-431">This provides a flexible design, since the precise filter pipeline doesn't need to be set explicitly when the app starts.</span></span>

<span data-ttu-id="6c471-432">`IFilterFactory` je možné implementovat jako jiný přístup k vytváření filtrů pomocí implementace vlastního atributu:</span><span class="sxs-lookup"><span data-stu-id="6c471-432">`IFilterFactory` can be implemented using custom attribute implementations as another approach to creating filters:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/AddHeaderWithFactoryAttribute.cs?name=snippet_IFilterFactory&highlight=1,4,5,6,7)]

<span data-ttu-id="6c471-433">Předchozí kód můžete otestovat spuštěním [stáhněte si ukázky](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample):</span><span class="sxs-lookup"><span data-stu-id="6c471-433">The preceding code can be tested by running the [download sample](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample):</span></span>

* <span data-ttu-id="6c471-434">Vyvolání vývojářských nástrojů F12.</span><span class="sxs-lookup"><span data-stu-id="6c471-434">Invoke the F12 developer tools.</span></span>
* <span data-ttu-id="6c471-435">Přejděte na `https://localhost:5001/Sample/HeaderWithFactory`</span><span class="sxs-lookup"><span data-stu-id="6c471-435">Navigate to `https://localhost:5001/Sample/HeaderWithFactory`</span></span>

<span data-ttu-id="6c471-436">Vývojářské nástroje F12 pomáhají zobrazit následující hlavičky odpovědi přidal ukázkový kód:</span><span class="sxs-lookup"><span data-stu-id="6c471-436">The F12 developer tools display the following response headers added by the sample code:</span></span>

* <span data-ttu-id="6c471-437">**Autor:** `Joe Smith`</span><span class="sxs-lookup"><span data-stu-id="6c471-437">**author:** `Joe Smith`</span></span>
* <span data-ttu-id="6c471-438">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span><span class="sxs-lookup"><span data-stu-id="6c471-438">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span></span>
* <span data-ttu-id="6c471-439">**Interní:** `My header`</span><span class="sxs-lookup"><span data-stu-id="6c471-439">**internal:** `My header`</span></span>

<span data-ttu-id="6c471-440">Předchozí kód vytvoří **interní:** `My header` hlavičky odpovědi.</span><span class="sxs-lookup"><span data-stu-id="6c471-440">The preceding code creates the **internal:** `My header` response header.</span></span>

### <a name="ifilterfactory-implemented-on-an-attribute"></a><span data-ttu-id="6c471-441">IFilterFactory implementovaná v atributu</span><span class="sxs-lookup"><span data-stu-id="6c471-441">IFilterFactory implemented on an attribute</span></span>

<!-- Review 
This section needs to be rewritten.
What's a non-named attribute?
-->

<span data-ttu-id="6c471-442">Filtry, které implementují `IFilterFactory` jsou užitečné pro filtry, které:</span><span class="sxs-lookup"><span data-stu-id="6c471-442">Filters that implement `IFilterFactory` are useful for filters that:</span></span>

* <span data-ttu-id="6c471-443">Předávání parametrů nevyžadují.</span><span class="sxs-lookup"><span data-stu-id="6c471-443">Don't require passing parameters.</span></span>
* <span data-ttu-id="6c471-444">Mají konstruktor závislosti, které potřebujete pro vyplnění podle DI.</span><span class="sxs-lookup"><span data-stu-id="6c471-444">Have constructor dependencies that need to be filled by DI.</span></span>

<span data-ttu-id="6c471-445"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implementuje <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span><span class="sxs-lookup"><span data-stu-id="6c471-445"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="6c471-446">`IFilterFactory` Zpřístupňuje <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> metodu pro vytvoření <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span><span class="sxs-lookup"><span data-stu-id="6c471-446">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="6c471-447">`CreateInstance` načte zadaného typu ze služby kontejnerů (DI).</span><span class="sxs-lookup"><span data-stu-id="6c471-447">`CreateInstance` loads the specified type from the services container (DI).</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/SampleActionFilterAttribute.cs?name=snippet_TypeFilterAttribute&highlight=1,3,7)]

<span data-ttu-id="6c471-448">Následující kód ukazuje tři přístupy k použití `[SampleActionFilter]`:</span><span class="sxs-lookup"><span data-stu-id="6c471-448">The following code shows three approaches to applying the `[SampleActionFilter]`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet&highlight=1)]

<span data-ttu-id="6c471-449">V předchozím kódu, upravení metody `[SampleActionFilter]` je upřednostňovaný způsob použití `SampleActionFilter`.</span><span class="sxs-lookup"><span data-stu-id="6c471-449">In the preceding code, decorating the method with `[SampleActionFilter]` is the preferred approach to applying the `SampleActionFilter`.</span></span>

## <a name="using-middleware-in-the-filter-pipeline"></a><span data-ttu-id="6c471-450">Pomocí middleware v kanálu filtru</span><span class="sxs-lookup"><span data-stu-id="6c471-450">Using middleware in the filter pipeline</span></span>

<span data-ttu-id="6c471-451">Filtry prostředků fungují jako [middleware](xref:fundamentals/middleware/index) v tom, že jsou před a za provádění všeho, která se dodává později v kanálu.</span><span class="sxs-lookup"><span data-stu-id="6c471-451">Resource filters work like [middleware](xref:fundamentals/middleware/index) in that they surround the execution of everything that comes later in the pipeline.</span></span> <span data-ttu-id="6c471-452">Ale filtry liší od middleware, že jsou součástí modulu runtime ASP.NET Core, což znamená, že mají přístup ke kontextu ASP.NET Core a konstrukce.</span><span class="sxs-lookup"><span data-stu-id="6c471-452">But filters differ from middleware in that they're part of the ASP.NET Core runtime, which means that they have access to ASP.NET Core context and constructs.</span></span>

<span data-ttu-id="6c471-453">Použití middlewaru jako filtr, vytvoření typu s `Configure` metodu, která určuje middlewaru, který má být vložení do kanálu filtru.</span><span class="sxs-lookup"><span data-stu-id="6c471-453">To use middleware as a filter, create a type with a `Configure` method that specifies the middleware to inject into the filter pipeline.</span></span> <span data-ttu-id="6c471-454">Lokalizace middleware pro aktuální jazykovou verzi pro žádost o navázání v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="6c471-454">The following example uses the localization middleware to establish the current culture for a request:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LocalizationPipeline.cs?name=snippet_MiddlewareFilter&highlight=3,21)]

<span data-ttu-id="6c471-455">Použití <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> pro spuštění middlewaru:</span><span class="sxs-lookup"><span data-stu-id="6c471-455">Use the <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> to run the middleware:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_MiddlewareFilter&highlight=2)]

<span data-ttu-id="6c471-456">Middleware filtry třídí ve stejné fázi kanálu filtr jako prostředek filtry, před navázáním modelu a po zbytek kanálu.</span><span class="sxs-lookup"><span data-stu-id="6c471-456">Middleware filters run at the same stage of the filter pipeline as Resource filters, before model binding and after the rest of the pipeline.</span></span>

## <a name="next-actions"></a><span data-ttu-id="6c471-457">Další akce</span><span class="sxs-lookup"><span data-stu-id="6c471-457">Next actions</span></span>

* <span data-ttu-id="6c471-458">Zobrazit [metody filtrování pro Razor Pages](xref:razor-pages/filter)</span><span class="sxs-lookup"><span data-stu-id="6c471-458">See [Filter methods for Razor Pages](xref:razor-pages/filter)</span></span>
* <span data-ttu-id="6c471-459">Můžete experimentovat s filtry, [stažení, otestovat a upravit na Githubu ukázky](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample).</span><span class="sxs-lookup"><span data-stu-id="6c471-459">To experiment with filters, [download, test, and modify the GitHub sample](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample).</span></span>
