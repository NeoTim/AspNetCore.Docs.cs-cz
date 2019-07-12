---
title: Filtry v ASP.NET Core
author: ardalis
description: Zjistěte, jak filtry fungují a jak je používat v ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 05/08/2019
uid: mvc/controllers/filters
ms.openlocfilehash: 50b199744f32ad19335080da406db69665ec1ae9
ms.sourcegitcommit: 7a40c56bf6a6aaa63a7ee83a2cac9b3a1d77555e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/12/2019
ms.locfileid: "67856162"
---
# <a name="filters-in-aspnet-core"></a><span data-ttu-id="45d24-103">Filtry v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="45d24-103">Filters in ASP.NET Core</span></span>

<span data-ttu-id="45d24-104">Podle [Kirka Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Petr Dykstra](https://github.com/tdykstra/), a [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="45d24-104">By [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="45d24-105">*Filtry* v ASP.NET Core umožňují kód ke spuštění před nebo po určitým fázím v kanálu zpracování požadavků.</span><span class="sxs-lookup"><span data-stu-id="45d24-105">*Filters* in ASP.NET Core allow code to be run before or after specific stages in the request processing pipeline.</span></span>

<span data-ttu-id="45d24-106">Integrované filtry naložit s úkoly, jako:</span><span class="sxs-lookup"><span data-stu-id="45d24-106">Built-in filters handle tasks such as:</span></span>

* <span data-ttu-id="45d24-107">Autorizace (zabránění přístupu k prostředkům, které uživatel nemá oprávnění pro).</span><span class="sxs-lookup"><span data-stu-id="45d24-107">Authorization (preventing access to resources a user isn't authorized for).</span></span>
* <span data-ttu-id="45d24-108">Odpověď do mezipaměti (krátký cyklus kanál požadavku na vrátí odpověď uložená v mezipaměti).</span><span class="sxs-lookup"><span data-stu-id="45d24-108">Response caching (short-circuiting the request pipeline to return a cached response).</span></span>

<span data-ttu-id="45d24-109">Vlastní filtry lze vytvořit pro zpracování vyskytující aspekty.</span><span class="sxs-lookup"><span data-stu-id="45d24-109">Custom filters can be created to handle cross-cutting concerns.</span></span> <span data-ttu-id="45d24-110">Příklady související aspekty: zpracování chyb, ukládání do mezipaměti, konfiguraci, ověření a protokolování.</span><span class="sxs-lookup"><span data-stu-id="45d24-110">Examples of cross-cutting concerns include error handling, caching, configuration, authorization, and logging.</span></span>  <span data-ttu-id="45d24-111">Filtry předejít duplikování kódu.</span><span class="sxs-lookup"><span data-stu-id="45d24-111">Filters avoid duplicating code.</span></span> <span data-ttu-id="45d24-112">Zpracování filtru výjimek chyby může například konsolidovat zpracování chyb.</span><span class="sxs-lookup"><span data-stu-id="45d24-112">For example, an error handling exception filter could consolidate error handling.</span></span>

<span data-ttu-id="45d24-113">Tento dokument se vztahuje na stránky Razor, kontrolery rozhraní API a řadiče se zobrazeními.</span><span class="sxs-lookup"><span data-stu-id="45d24-113">This document applies to Razor Pages, API controllers, and controllers with views.</span></span>

<span data-ttu-id="45d24-114">[Zobrazení nebo stažení ukázky](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample) ([stažení](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="45d24-114">[View or download sample](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="how-filters-work"></a><span data-ttu-id="45d24-115">Jak fungují filtry</span><span class="sxs-lookup"><span data-stu-id="45d24-115">How filters work</span></span>

<span data-ttu-id="45d24-116">Filtry se spouští v rámci *kanálu vyvolání akce ASP.NET Core*, která se někdy označují jako *filtrovat*.</span><span class="sxs-lookup"><span data-stu-id="45d24-116">Filters run within the *ASP.NET Core action invocation pipeline*, sometimes referred to as the *filter pipeline*.</span></span>  <span data-ttu-id="45d24-117">Spuštění kanálu filtru po ASP.NET Core vybere akci pro spuštění.</span><span class="sxs-lookup"><span data-stu-id="45d24-117">The filter pipeline runs after ASP.NET Core selects the action to execute.</span></span>

![Další Middleware, směrování Middleware, výběr akce a vyvolání kanálu ASP.NET Core akce zpracování žádosti.](filters/_static/filter-pipeline-1.png)

### <a name="filter-types"></a><span data-ttu-id="45d24-120">Typy filtrů</span><span class="sxs-lookup"><span data-stu-id="45d24-120">Filter types</span></span>

<span data-ttu-id="45d24-121">Různé fáze v kanálu filtr spuštění jednotlivých typů filtrů:</span><span class="sxs-lookup"><span data-stu-id="45d24-121">Each filter type is executed at a different stage in the filter pipeline:</span></span>

* <span data-ttu-id="45d24-122">[Filtry autorizace](#authorization-filters) spouští jako první a slouží k určení, zda je uživatel autorizován pro daný požadavek.</span><span class="sxs-lookup"><span data-stu-id="45d24-122">[Authorization filters](#authorization-filters) run first and are used to determine whether the user is authorized for the request.</span></span> <span data-ttu-id="45d24-123">Filtry autorizace zkrácenou kanálu, pokud žádost neoprávněná.</span><span class="sxs-lookup"><span data-stu-id="45d24-123">Authorization filters short-circuit the pipeline if the request is unauthorized.</span></span>

* <span data-ttu-id="45d24-124">[Filtry prostředků](#resource-filters):</span><span class="sxs-lookup"><span data-stu-id="45d24-124">[Resource filters](#resource-filters):</span></span>

  * <span data-ttu-id="45d24-125">Po povolení spusťte.</span><span class="sxs-lookup"><span data-stu-id="45d24-125">Run after authorization.</span></span>  
  * <span data-ttu-id="45d24-126"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> můžete spustit kód před filtr zbytku.</span><span class="sxs-lookup"><span data-stu-id="45d24-126"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> can run code before the rest of the filter pipeline.</span></span> <span data-ttu-id="45d24-127">Například `OnResourceExecuting` může spustit kód před navázáním modelu.</span><span class="sxs-lookup"><span data-stu-id="45d24-127">For example, `OnResourceExecuting` can run code before model binding.</span></span>
  * <span data-ttu-id="45d24-128"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> můžete spustit kód po dokončení rest z kanálu.</span><span class="sxs-lookup"><span data-stu-id="45d24-128"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> can run code after the rest of the pipeline has completed.</span></span>

* <span data-ttu-id="45d24-129">[Filtry akcí](#action-filters) může spustit kód bezprostředně před a po volání metody jednotlivé akce.</span><span class="sxs-lookup"><span data-stu-id="45d24-129">[Action filters](#action-filters) can run code immediately before and after an individual action method is called.</span></span> <span data-ttu-id="45d24-130">Můžete být použít k manipulaci s argumenty předané do akce a výsledek vrácený z akce.</span><span class="sxs-lookup"><span data-stu-id="45d24-130">They can be used to manipulate the arguments passed into an action and the result returned from the action.</span></span> <span data-ttu-id="45d24-131">Filtry akce jsou **není** podporované v stránky Razor.</span><span class="sxs-lookup"><span data-stu-id="45d24-131">Action filters are **not** supported in Razor Pages.</span></span>

* <span data-ttu-id="45d24-132">[Filtry výjimek](#exception-filters) umožňují použití globálních zásad pro neošetřených výjimek, ke kterým dojde před nic se zapsala do datové části odpovědi.</span><span class="sxs-lookup"><span data-stu-id="45d24-132">[Exception filters](#exception-filters) are used to apply global policies to unhandled exceptions that occur before anything has been written to the response body.</span></span>

* <span data-ttu-id="45d24-133">[Výsledek filtry](#result-filters) může spustit kód bezprostředně před a po spuštění výsledky jednotlivých akcí.</span><span class="sxs-lookup"><span data-stu-id="45d24-133">[Result filters](#result-filters) can run code immediately before and after the execution of individual action results.</span></span> <span data-ttu-id="45d24-134">Spouštějí se pouze v případě, že byl úspěšně proveden metody akce.</span><span class="sxs-lookup"><span data-stu-id="45d24-134">They run only when the action method has executed successfully.</span></span> <span data-ttu-id="45d24-135">Jsou užitečné pro logiku, která se musí před a za spuštění zobrazení nebo formátovacího modulu.</span><span class="sxs-lookup"><span data-stu-id="45d24-135">They are useful for logic that must surround view or formatter execution.</span></span>

<span data-ttu-id="45d24-136">Následující diagram znázorňuje interakci typy filtr v kanálu filtru.</span><span class="sxs-lookup"><span data-stu-id="45d24-136">The following diagram shows how filter types interact in the filter pipeline.</span></span>

![Žádost se zpracovává prostřednictvím filtry autorizace, prostředků filtry, vazby modelu, filtry akce, provedení akce a převod výsledek akce, filtry výjimek, filtry výsledků a výsledek spuštění.](filters/_static/filter-pipeline-2.png)

## <a name="implementation"></a><span data-ttu-id="45d24-139">Implementace</span><span class="sxs-lookup"><span data-stu-id="45d24-139">Implementation</span></span>

<span data-ttu-id="45d24-140">Filtrů podporuje synchronní a asynchronní implementace prostřednictvím různých rozhraní definice.</span><span class="sxs-lookup"><span data-stu-id="45d24-140">Filters support both synchronous and asynchronous implementations through different interface definitions.</span></span>

<span data-ttu-id="45d24-141">Synchronní filtry můžete spuštění kódu před (`On-Stage-Executing`) a po provedení (`On-Stage-Executed`) jejich fázi zřetězení.</span><span class="sxs-lookup"><span data-stu-id="45d24-141">Synchronous filters can run code before (`On-Stage-Executing`) and after (`On-Stage-Executed`) their pipeline stage.</span></span> <span data-ttu-id="45d24-142">Například `OnActionExecuting` je volána před voláním metody akce.</span><span class="sxs-lookup"><span data-stu-id="45d24-142">For example, `OnActionExecuting` is called before the action method is called.</span></span> <span data-ttu-id="45d24-143">`OnActionExecuted` je volána po vrácení metody akce.</span><span class="sxs-lookup"><span data-stu-id="45d24-143">`OnActionExecuted` is called after the action method returns.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="45d24-144">Definování asynchronní filtry `On-Stage-ExecutionAsync` metody:</span><span class="sxs-lookup"><span data-stu-id="45d24-144">Asynchronous filters define an `On-Stage-ExecutionAsync` method:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/SampleAsyncActionFilter.cs?name=snippet)]

<span data-ttu-id="45d24-145">V předchozím kódu `SampleAsyncActionFilter` má <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`), který provede metodu akce.</span><span class="sxs-lookup"><span data-stu-id="45d24-145">In the preceding code, the `SampleAsyncActionFilter` has an <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`)  that executes the action method.</span></span>  <span data-ttu-id="45d24-146">Každá z `On-Stage-ExecutionAsync` metody přijímají `FilterType-ExecutionDelegate` fázi zřetězení tohoto filtru, který se spustí.</span><span class="sxs-lookup"><span data-stu-id="45d24-146">Each of the `On-Stage-ExecutionAsync` methods take a `FilterType-ExecutionDelegate` that executes the filter's pipeline stage.</span></span>

### <a name="multiple-filter-stages"></a><span data-ttu-id="45d24-147">Více fázích filtru</span><span class="sxs-lookup"><span data-stu-id="45d24-147">Multiple filter stages</span></span>

<span data-ttu-id="45d24-148">Rozhraní pro více fázích filtru je možné implementovat do jedné třídy.</span><span class="sxs-lookup"><span data-stu-id="45d24-148">Interfaces for multiple filter stages can be implemented in a single class.</span></span> <span data-ttu-id="45d24-149">Například <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> implementuje třída `IActionFilter`, `IResultFilter`a jejich ekvivalenty asynchronní.</span><span class="sxs-lookup"><span data-stu-id="45d24-149">For example, the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> class implements `IActionFilter`, `IResultFilter`, and their async equivalents.</span></span>

<span data-ttu-id="45d24-150">Implementace **buď** synchronní nebo asynchronní verze rozhraní filtru, **není** obojí.</span><span class="sxs-lookup"><span data-stu-id="45d24-150">Implement **either** the synchronous or the async version of a filter interface, **not** both.</span></span> <span data-ttu-id="45d24-151">Modul runtime nejprve zkontroluje zobrazit, pokud filtr, implementuje rozhraní asynchronní, a pokud ano, který volá.</span><span class="sxs-lookup"><span data-stu-id="45d24-151">The runtime checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="45d24-152">Pokud tomu tak není, volá rozhraní synchronní metody.</span><span class="sxs-lookup"><span data-stu-id="45d24-152">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="45d24-153">Pokud se synchronní a asynchronní rozhraní jsou implementovány v jedné třídy, se nazývá asynchronní metody.</span><span class="sxs-lookup"><span data-stu-id="45d24-153">If both asynchronous and synchronous interfaces are implemented in one class, only the async method is called.</span></span> <span data-ttu-id="45d24-154">Při používání abstraktních tříd jako <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> přepsání pouze metody synchronní nebo asynchronní metody pro každý typ filtru.</span><span class="sxs-lookup"><span data-stu-id="45d24-154">When using abstract classes like <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> override only the synchronous methods or the async method for each filter type.</span></span>

### <a name="built-in-filter-attributes"></a><span data-ttu-id="45d24-155">Integrovaný filtr atributy</span><span class="sxs-lookup"><span data-stu-id="45d24-155">Built-in filter attributes</span></span>

<span data-ttu-id="45d24-156">ASP.NET Core zahrnuje integrované filtry založených na atributech, které mohou být rozčlenění a přizpůsobit.</span><span class="sxs-lookup"><span data-stu-id="45d24-156">ASP.NET Core includes built-in attribute-based filters that can be subclassed and customized.</span></span> <span data-ttu-id="45d24-157">Například následující filtr výsledků přidá do odpovědi hlavičku:</span><span class="sxs-lookup"><span data-stu-id="45d24-157">For example, the following result filter adds a header to the response:</span></span>

<a name="add-header-attribute"></a>

[!code-csharp[](./filters/sample/FiltersSample/Filters/AddHeaderAttribute.cs?name=snippet)]

<span data-ttu-id="45d24-158">Atributy Povolit filtry, které přijímají argumenty, jak je znázorněno v předchozím příkladu.</span><span class="sxs-lookup"><span data-stu-id="45d24-158">Attributes allow filters to accept arguments, as shown in the preceding example.</span></span> <span data-ttu-id="45d24-159">Použít `AddHeaderAttribute` metodě kontroler nebo akce a zadejte název a hodnotu hlavičky protokolu HTTP:</span><span class="sxs-lookup"><span data-stu-id="45d24-159">Apply the `AddHeaderAttribute` to a controller or action method and specify the name and value of the HTTP header:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

<!-- `https://localhost:5001/Sample` -->

<span data-ttu-id="45d24-160">Některé z rozhraní filtru mít odpovídající atributy, které lze použít jako základní třídy pro vlastní implementace.</span><span class="sxs-lookup"><span data-stu-id="45d24-160">Several of the filter interfaces have corresponding attributes that can be used as base classes for custom implementations.</span></span>

<span data-ttu-id="45d24-161">Atributy filtru:</span><span class="sxs-lookup"><span data-stu-id="45d24-161">Filter attributes:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ExceptionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>

## <a name="filter-scopes-and-order-of-execution"></a><span data-ttu-id="45d24-162">Filtr oborů a pořadí provádění</span><span class="sxs-lookup"><span data-stu-id="45d24-162">Filter scopes and order of execution</span></span>

<span data-ttu-id="45d24-163">Filtr lze přidat do kanálu na jeden ze tří *obory*:</span><span class="sxs-lookup"><span data-stu-id="45d24-163">A filter can be added to the pipeline at one of three *scopes*:</span></span>

* <span data-ttu-id="45d24-164">Pomocí atributu na akci.</span><span class="sxs-lookup"><span data-stu-id="45d24-164">Using an attribute on an action.</span></span>
* <span data-ttu-id="45d24-165">Pomocí atributu na kontroleru.</span><span class="sxs-lookup"><span data-stu-id="45d24-165">Using an attribute on a controller.</span></span>
* <span data-ttu-id="45d24-166">Globálně pro všechny kontrolery a akce, jak je znázorněno v následujícím kódu:</span><span class="sxs-lookup"><span data-stu-id="45d24-166">Globally for all controllers and actions as shown in the following code:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/StartupGF.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="45d24-167">Předchozí kód přidá globálně pomocí tří filtry [MvcOptions.Filters](xref:Microsoft.AspNetCore.Mvc.MvcOptions.Filters) kolekce.</span><span class="sxs-lookup"><span data-stu-id="45d24-167">The preceding code adds three filters globally using the [MvcOptions.Filters](xref:Microsoft.AspNetCore.Mvc.MvcOptions.Filters) collection.</span></span>

### <a name="default-order-of-execution"></a><span data-ttu-id="45d24-168">Výchozí pořadí provádění</span><span class="sxs-lookup"><span data-stu-id="45d24-168">Default order of execution</span></span>

<span data-ttu-id="45d24-169">Pokud jsou použity více filtry pro konkrétní fázi kanálu, obor Určuje výchozí pořadí spuštění filtrů.</span><span class="sxs-lookup"><span data-stu-id="45d24-169">When there are multiple filters for a particular stage of the pipeline, scope determines the default order of filter execution.</span></span>  <span data-ttu-id="45d24-170">Globální filtry před a za třídy filtrů, které pak před a za metoda filtry.</span><span class="sxs-lookup"><span data-stu-id="45d24-170">Global filters surround class filters, which in turn surround method filters.</span></span>

<span data-ttu-id="45d24-171">V důsledku filtru vnoření *po* spuštění kódu filtrů v obráceném pořadí *před* kódu.</span><span class="sxs-lookup"><span data-stu-id="45d24-171">As a result of filter nesting, the *after* code of filters runs in the reverse order of the *before* code.</span></span> <span data-ttu-id="45d24-172">Pořadí filtru:</span><span class="sxs-lookup"><span data-stu-id="45d24-172">The filter sequence:</span></span>

* <span data-ttu-id="45d24-173">*Před* kód globálních filtrů.</span><span class="sxs-lookup"><span data-stu-id="45d24-173">The *before* code of global filters.</span></span>
  * <span data-ttu-id="45d24-174">*Před* kód filtrů kontroleru.</span><span class="sxs-lookup"><span data-stu-id="45d24-174">The *before* code of controller filters.</span></span>
    * <span data-ttu-id="45d24-175">*Před* kód filtrů metody akce.</span><span class="sxs-lookup"><span data-stu-id="45d24-175">The *before* code of action method filters.</span></span>
    * <span data-ttu-id="45d24-176">*Po* kód filtrů metody akce.</span><span class="sxs-lookup"><span data-stu-id="45d24-176">The *after* code of action method filters.</span></span>
  * <span data-ttu-id="45d24-177">*Po* kód filtrů kontroleru.</span><span class="sxs-lookup"><span data-stu-id="45d24-177">The *after* code of controller filters.</span></span>
* <span data-ttu-id="45d24-178">*Po* kód globálních filtrů.</span><span class="sxs-lookup"><span data-stu-id="45d24-178">The *after* code of global filters.</span></span>
  
<span data-ttu-id="45d24-179">Následující příklad, který znázorňuje pořadí, ve které filtru jsou volány metody pro synchronní akce filtry.</span><span class="sxs-lookup"><span data-stu-id="45d24-179">The following example that illustrates the order in which filter methods are called for synchronous action filters.</span></span>

| <span data-ttu-id="45d24-180">Sequence</span><span class="sxs-lookup"><span data-stu-id="45d24-180">Sequence</span></span> | <span data-ttu-id="45d24-181">Obor filtru</span><span class="sxs-lookup"><span data-stu-id="45d24-181">Filter scope</span></span> | <span data-ttu-id="45d24-182">Filter – metoda</span><span class="sxs-lookup"><span data-stu-id="45d24-182">Filter method</span></span> |
|:--------:|:------------:|:-------------:|
| <span data-ttu-id="45d24-183">1</span><span class="sxs-lookup"><span data-stu-id="45d24-183">1</span></span> | <span data-ttu-id="45d24-184">Globální</span><span class="sxs-lookup"><span data-stu-id="45d24-184">Global</span></span> | `OnActionExecuting` |
| <span data-ttu-id="45d24-185">2</span><span class="sxs-lookup"><span data-stu-id="45d24-185">2</span></span> | <span data-ttu-id="45d24-186">Kontroler</span><span class="sxs-lookup"><span data-stu-id="45d24-186">Controller</span></span> | `OnActionExecuting` |
| <span data-ttu-id="45d24-187">3</span><span class="sxs-lookup"><span data-stu-id="45d24-187">3</span></span> | <span data-ttu-id="45d24-188">Metoda</span><span class="sxs-lookup"><span data-stu-id="45d24-188">Method</span></span> | `OnActionExecuting` |
| <span data-ttu-id="45d24-189">4</span><span class="sxs-lookup"><span data-stu-id="45d24-189">4</span></span> | <span data-ttu-id="45d24-190">Metoda</span><span class="sxs-lookup"><span data-stu-id="45d24-190">Method</span></span> | `OnActionExecuted` |
| <span data-ttu-id="45d24-191">5</span><span class="sxs-lookup"><span data-stu-id="45d24-191">5</span></span> | <span data-ttu-id="45d24-192">Kontroler</span><span class="sxs-lookup"><span data-stu-id="45d24-192">Controller</span></span> | `OnActionExecuted` |
| <span data-ttu-id="45d24-193">6</span><span class="sxs-lookup"><span data-stu-id="45d24-193">6</span></span> | <span data-ttu-id="45d24-194">Globální</span><span class="sxs-lookup"><span data-stu-id="45d24-194">Global</span></span> | `OnActionExecuted` |

<span data-ttu-id="45d24-195">Zobrazí se toto pořadí:</span><span class="sxs-lookup"><span data-stu-id="45d24-195">This sequence shows:</span></span>

* <span data-ttu-id="45d24-196">Metoda filtr je vnořená v rámci kontroleru filtru.</span><span class="sxs-lookup"><span data-stu-id="45d24-196">The method filter is nested within the controller filter.</span></span>
* <span data-ttu-id="45d24-197">Filtr kontroleru je vnořená v rámci globálních filtrů.</span><span class="sxs-lookup"><span data-stu-id="45d24-197">The controller filter is nested within the global filter.</span></span>

### <a name="controller-and-razor-page-level-filters"></a><span data-ttu-id="45d24-198">Filtry na úrovni kontroleru a stránky Razor</span><span class="sxs-lookup"><span data-stu-id="45d24-198">Controller and Razor Page level filters</span></span>

<span data-ttu-id="45d24-199">Každý kontroler, který dědí z <xref:Microsoft.AspNetCore.Mvc.Controller> základní třída zahrnuje [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*), [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*), a [ Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*) 
 `OnActionExecuted` metody.</span><span class="sxs-lookup"><span data-stu-id="45d24-199">Every controller that inherits from the <xref:Microsoft.AspNetCore.Mvc.Controller> base class includes [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*),  [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*), and [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted` methods.</span></span> <span data-ttu-id="45d24-200">Tyto metody:</span><span class="sxs-lookup"><span data-stu-id="45d24-200">These methods:</span></span>

* <span data-ttu-id="45d24-201">Zabalení, na kterých běží filtry pro danou akci.</span><span class="sxs-lookup"><span data-stu-id="45d24-201">Wrap the filters that run for a given action.</span></span>
* <span data-ttu-id="45d24-202">`OnActionExecuting` je volána před provedením akce filtry.</span><span class="sxs-lookup"><span data-stu-id="45d24-202">`OnActionExecuting` is called before any of the action's filters.</span></span>
* <span data-ttu-id="45d24-203">`OnActionExecuted` je volána po všech filtrů akce.</span><span class="sxs-lookup"><span data-stu-id="45d24-203">`OnActionExecuted` is called after all of the action filters.</span></span>
* <span data-ttu-id="45d24-204">`OnActionExecutionAsync` je volána před provedením akce filtry.</span><span class="sxs-lookup"><span data-stu-id="45d24-204">`OnActionExecutionAsync` is called before any of the action's filters.</span></span> <span data-ttu-id="45d24-205">Kód ve filtru po `next` běží po metodě akce.</span><span class="sxs-lookup"><span data-stu-id="45d24-205">Code in the filter after `next` runs after the action method.</span></span>

<span data-ttu-id="45d24-206">Například v ukázce stahování `MySampleActionFilter` platí globálně ve spuštění.</span><span class="sxs-lookup"><span data-stu-id="45d24-206">For example, in the download sample, `MySampleActionFilter` is applied globally in startup.</span></span>

<span data-ttu-id="45d24-207">`TestController`:</span><span class="sxs-lookup"><span data-stu-id="45d24-207">The `TestController`:</span></span>

* <span data-ttu-id="45d24-208">Se vztahuje `SampleActionFilterAttribute` (`[SampleActionFilter]`) k `FilterTest2` akce:</span><span class="sxs-lookup"><span data-stu-id="45d24-208">Applies the `SampleActionFilterAttribute` (`[SampleActionFilter]`) to the `FilterTest2` action:</span></span>
* <span data-ttu-id="45d24-209">Přepíše `OnActionExecuting` a `OnActionExecuted`.</span><span class="sxs-lookup"><span data-stu-id="45d24-209">Overrides `OnActionExecuting` and `OnActionExecuted`.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/TestController.cs?name=snippet)]

<span data-ttu-id="45d24-210">Přejděte na `https://localhost:5001/Test/FilterTest2` spustí následující kód:</span><span class="sxs-lookup"><span data-stu-id="45d24-210">Navigating to `https://localhost:5001/Test/FilterTest2` runs the following code:</span></span>

* `TestController.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `SampleActionFilterAttribute.OnActionExecuting`
      * `TestController.FilterTest2`
    * `SampleActionFilterAttribute.OnActionExecuted`
  * `MySampleActionFilter.OnActionExecuted`
* `TestController.OnActionExecuted`

<span data-ttu-id="45d24-211">Stránky Razor, naleznete v tématu [filtry stránky Razor implementace přepsáním metody filtr](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span><span class="sxs-lookup"><span data-stu-id="45d24-211">For Razor Pages, see [Implement Razor Page filters by overriding filter methods](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span></span>

### <a name="overriding-the-default-order"></a><span data-ttu-id="45d24-212">Přepsání výchozího pořadí</span><span class="sxs-lookup"><span data-stu-id="45d24-212">Overriding the default order</span></span>

<span data-ttu-id="45d24-213">Výchozí pořadí provádění lze přepsat pomocí implementace <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span><span class="sxs-lookup"><span data-stu-id="45d24-213">The default sequence of execution can be overridden by implementing <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span></span> <span data-ttu-id="45d24-214">`IOrderedFilter` Zpřístupňuje <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> vlastnost, která má přednost před obor pro určení pořadí spouštění.</span><span class="sxs-lookup"><span data-stu-id="45d24-214">`IOrderedFilter` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> property that takes precedence over scope to determine the order of execution.</span></span> <span data-ttu-id="45d24-215">Filtr s nižší `Order` hodnotu:</span><span class="sxs-lookup"><span data-stu-id="45d24-215">A filter with a lower `Order` value:</span></span>

* <span data-ttu-id="45d24-216">Spuštění *před* kódu před inicializací filtr s vyšší hodnotou `Order`.</span><span class="sxs-lookup"><span data-stu-id="45d24-216">Runs the *before* code before that of a filter with a higher value of `Order`.</span></span>
* <span data-ttu-id="45d24-217">Spuštění *po* kódu po tomto filtr s vyšší `Order` hodnotu.</span><span class="sxs-lookup"><span data-stu-id="45d24-217">Runs the *after* code after that of a filter with a higher `Order` value.</span></span>

<span data-ttu-id="45d24-218">`Order` Vlastnost lze nastavit pomocí parametru konstruktoru:</span><span class="sxs-lookup"><span data-stu-id="45d24-218">The `Order` property can be set with a constructor parameter:</span></span>

```csharp
[MyFilter(Name = "Controller Level Attribute", Order=1)]
```

<span data-ttu-id="45d24-219">Vezměte v úvahu stejné filtry akce 3 v předchozím příkladu.</span><span class="sxs-lookup"><span data-stu-id="45d24-219">Consider the same 3 action filters shown in the preceding example.</span></span> <span data-ttu-id="45d24-220">Pokud `Order` řadiče a globální filtry je nastavena na 1 a 2 v uvedeném pořadí, je obrácený pořadí provádění.</span><span class="sxs-lookup"><span data-stu-id="45d24-220">If the `Order` property of the controller and global filters is set to 1 and 2 respectively, the order of execution is reversed.</span></span>

| <span data-ttu-id="45d24-221">Sequence</span><span class="sxs-lookup"><span data-stu-id="45d24-221">Sequence</span></span> | <span data-ttu-id="45d24-222">Obor filtru</span><span class="sxs-lookup"><span data-stu-id="45d24-222">Filter scope</span></span> | <span data-ttu-id="45d24-223">`Order` Vlastnost</span><span class="sxs-lookup"><span data-stu-id="45d24-223">`Order` property</span></span> | <span data-ttu-id="45d24-224">Filter – metoda</span><span class="sxs-lookup"><span data-stu-id="45d24-224">Filter method</span></span> |
|:--------:|:------------:|:-----------------:|:-------------:|
| <span data-ttu-id="45d24-225">1</span><span class="sxs-lookup"><span data-stu-id="45d24-225">1</span></span> | <span data-ttu-id="45d24-226">Metoda</span><span class="sxs-lookup"><span data-stu-id="45d24-226">Method</span></span> | <span data-ttu-id="45d24-227">0</span><span class="sxs-lookup"><span data-stu-id="45d24-227">0</span></span> | `OnActionExecuting` |
| <span data-ttu-id="45d24-228">2</span><span class="sxs-lookup"><span data-stu-id="45d24-228">2</span></span> | <span data-ttu-id="45d24-229">Kontroler</span><span class="sxs-lookup"><span data-stu-id="45d24-229">Controller</span></span> | <span data-ttu-id="45d24-230">1</span><span class="sxs-lookup"><span data-stu-id="45d24-230">1</span></span>  | `OnActionExecuting` |
| <span data-ttu-id="45d24-231">3</span><span class="sxs-lookup"><span data-stu-id="45d24-231">3</span></span> | <span data-ttu-id="45d24-232">Globální</span><span class="sxs-lookup"><span data-stu-id="45d24-232">Global</span></span> | <span data-ttu-id="45d24-233">2</span><span class="sxs-lookup"><span data-stu-id="45d24-233">2</span></span>  | `OnActionExecuting` |
| <span data-ttu-id="45d24-234">4</span><span class="sxs-lookup"><span data-stu-id="45d24-234">4</span></span> | <span data-ttu-id="45d24-235">Globální</span><span class="sxs-lookup"><span data-stu-id="45d24-235">Global</span></span> | <span data-ttu-id="45d24-236">2</span><span class="sxs-lookup"><span data-stu-id="45d24-236">2</span></span>  | `OnActionExecuted` |
| <span data-ttu-id="45d24-237">5</span><span class="sxs-lookup"><span data-stu-id="45d24-237">5</span></span> | <span data-ttu-id="45d24-238">Kontroler</span><span class="sxs-lookup"><span data-stu-id="45d24-238">Controller</span></span> | <span data-ttu-id="45d24-239">1</span><span class="sxs-lookup"><span data-stu-id="45d24-239">1</span></span>  | `OnActionExecuted` |
| <span data-ttu-id="45d24-240">6</span><span class="sxs-lookup"><span data-stu-id="45d24-240">6</span></span> | <span data-ttu-id="45d24-241">Metoda</span><span class="sxs-lookup"><span data-stu-id="45d24-241">Method</span></span> | <span data-ttu-id="45d24-242">0</span><span class="sxs-lookup"><span data-stu-id="45d24-242">0</span></span>  | `OnActionExecuted` |

<span data-ttu-id="45d24-243">`Order` Vlastnost přepíše obor při určování pořadí, ve které filtry spustit.</span><span class="sxs-lookup"><span data-stu-id="45d24-243">The `Order` property overrides scope when determining the order in which filters run.</span></span> <span data-ttu-id="45d24-244">Filtry jsou řazeny nejdříve podle pořadí a oboru se používá k rozdělení vazby.</span><span class="sxs-lookup"><span data-stu-id="45d24-244">Filters are sorted first by order, then scope is used to break ties.</span></span> <span data-ttu-id="45d24-245">Všechny integrované filtry implementovat `IOrderedFilter` a nastaví výchozí `Order` hodnotu na 0.</span><span class="sxs-lookup"><span data-stu-id="45d24-245">All of the built-in filters implement `IOrderedFilter` and set the default `Order` value to 0.</span></span> <span data-ttu-id="45d24-246">Obor pro integrované filtry, určuje pořadí, není-li `Order` nastaven na nenulovou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="45d24-246">For built-in filters, scope determines order unless `Order` is set to a non-zero value.</span></span>

## <a name="cancellation-and-short-circuiting"></a><span data-ttu-id="45d24-247">Zrušení a krátký cyklus</span><span class="sxs-lookup"><span data-stu-id="45d24-247">Cancellation and short-circuiting</span></span>

<span data-ttu-id="45d24-248">Filtr kanálu můžete short-circuited nastavením <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> vlastnost na <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> parametr předán metodě filtru.</span><span class="sxs-lookup"><span data-stu-id="45d24-248">The filter pipeline can be short-circuited by setting the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> property on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> parameter provided to the filter method.</span></span> <span data-ttu-id="45d24-249">Například následující filtr prostředků zabraňuje rest z kanálu provádění:</span><span class="sxs-lookup"><span data-stu-id="45d24-249">For instance, the following Resource filter prevents the rest of the pipeline from executing:</span></span>

<a name="short-circuiting-resource-filter"></a>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ShortCircuitingResourceFilterAttribute.cs?name=snippet)]

<span data-ttu-id="45d24-250">V následujícím kódu jak `ShortCircuitingResourceFilter` a `AddHeader` cílový filtr `SomeResource` metody akce.</span><span class="sxs-lookup"><span data-stu-id="45d24-250">In the following code, both the `ShortCircuitingResourceFilter` and the `AddHeader` filter target the `SomeResource` action method.</span></span> <span data-ttu-id="45d24-251">`ShortCircuitingResourceFilter`:</span><span class="sxs-lookup"><span data-stu-id="45d24-251">The `ShortCircuitingResourceFilter`:</span></span>

* <span data-ttu-id="45d24-252">Spustí nejdříve, protože je filtr prostředků a `AddHeader` je filtr akce.</span><span class="sxs-lookup"><span data-stu-id="45d24-252">Runs first, because it's a Resource Filter and `AddHeader` is an Action Filter.</span></span>
* <span data-ttu-id="45d24-253">Zkratům rest z kanálu.</span><span class="sxs-lookup"><span data-stu-id="45d24-253">Short-circuits the rest of the pipeline.</span></span>

<span data-ttu-id="45d24-254">Proto `AddHeader` filtr nikdy spuštěn `SomeResource` akce.</span><span class="sxs-lookup"><span data-stu-id="45d24-254">Therefore the `AddHeader` filter never runs for the `SomeResource` action.</span></span> <span data-ttu-id="45d24-255">Toto chování by být stejný Pokud byly použity oba filtry na úrovni metody akce k dispozici `ShortCircuitingResourceFilter` spustil první.</span><span class="sxs-lookup"><span data-stu-id="45d24-255">This behavior would be the same if both filters were applied at the action method level, provided the `ShortCircuitingResourceFilter` ran first.</span></span> <span data-ttu-id="45d24-256">`ShortCircuitingResourceFilter` Spustí první z důvodu jeho typ filtru nebo explicitní použití `Order` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="45d24-256">The `ShortCircuitingResourceFilter` runs first because of its filter type, or by explicit use of `Order` property.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1,9)]

## <a name="dependency-injection"></a><span data-ttu-id="45d24-257">Injektáž závislostí</span><span class="sxs-lookup"><span data-stu-id="45d24-257">Dependency injection</span></span>

<span data-ttu-id="45d24-258">Filtry je možné přidat podle typu nebo instance.</span><span class="sxs-lookup"><span data-stu-id="45d24-258">Filters can be added by type or by instance.</span></span> <span data-ttu-id="45d24-259">Pokud je přidána instance, se pro každý požadavek používá tuto instanci.</span><span class="sxs-lookup"><span data-stu-id="45d24-259">If an instance is added, that instance is used for every request.</span></span> <span data-ttu-id="45d24-260">Pokud se přidá typ, je aktivovat typu.</span><span class="sxs-lookup"><span data-stu-id="45d24-260">If a type is added, it's type-activated.</span></span> <span data-ttu-id="45d24-261">Aktivovat typ filtru znamená, že:</span><span class="sxs-lookup"><span data-stu-id="45d24-261">A type-activated filter means:</span></span>

* <span data-ttu-id="45d24-262">Pro každý požadavek je vytvořena instance.</span><span class="sxs-lookup"><span data-stu-id="45d24-262">An instance is created for each request.</span></span>
* <span data-ttu-id="45d24-263">Všechny závislosti konstruktoru se vyplní podle [injektáž závislostí](xref:fundamentals/dependency-injection) (DI).</span><span class="sxs-lookup"><span data-stu-id="45d24-263">Any constructor dependencies are populated by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span>

<span data-ttu-id="45d24-264">Filtry, které jsou implementovány jako atributy a přidat přímo do třídy kontroleru nebo metody akce nemůže mít konstruktor závislosti poskytované [injektáž závislostí](xref:fundamentals/dependency-injection) (DI).</span><span class="sxs-lookup"><span data-stu-id="45d24-264">Filters that are implemented as attributes and added directly to controller classes or action methods cannot have constructor dependencies provided by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span> <span data-ttu-id="45d24-265">Závislosti konstruktoru nemůže poskytovat DI, protože:</span><span class="sxs-lookup"><span data-stu-id="45d24-265">Constructor dependencies cannot be provided by DI because:</span></span>

* <span data-ttu-id="45d24-266">Atributy musí mít zadané, kde uplatňují jejich parametry konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="45d24-266">Attributes must have their constructor parameters supplied where they're applied.</span></span> 
* <span data-ttu-id="45d24-267">Jedná se omezení fungování atributy.</span><span class="sxs-lookup"><span data-stu-id="45d24-267">This is a limitation of how attributes work.</span></span>

<span data-ttu-id="45d24-268">Následující filtry podpory poskytované DI závislosti konstruktoru:</span><span class="sxs-lookup"><span data-stu-id="45d24-268">The following filters support constructor dependencies provided from DI:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>
* <span data-ttu-id="45d24-269"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implementováno v atributu.</span><span class="sxs-lookup"><span data-stu-id="45d24-269"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implemented on the attribute.</span></span>

<span data-ttu-id="45d24-270">Předchozí filtry můžete použít pro metodu kontroler nebo akce:</span><span class="sxs-lookup"><span data-stu-id="45d24-270">The preceding filters can be applied to a controller or action method:</span></span>

<span data-ttu-id="45d24-271">Protokolovací nástroje jsou k dispozici DI.</span><span class="sxs-lookup"><span data-stu-id="45d24-271">Loggers are available from DI.</span></span> <span data-ttu-id="45d24-272">Ale Vyhněte se vytváření a používání filtrů výhradně pro účely protokolování.</span><span class="sxs-lookup"><span data-stu-id="45d24-272">However, avoid creating and using filters purely for logging purposes.</span></span> <span data-ttu-id="45d24-273">[Vestavěnou architekturou protokolování](xref:fundamentals/logging/index) obvykle poskytuje toho, co je potřeba pro protokolování.</span><span class="sxs-lookup"><span data-stu-id="45d24-273">The [built-in framework logging](xref:fundamentals/logging/index) typically provides what's needed for logging.</span></span> <span data-ttu-id="45d24-274">Přidat do filtrů protokolování:</span><span class="sxs-lookup"><span data-stu-id="45d24-274">Logging added to filters:</span></span>

* <span data-ttu-id="45d24-275">Byste se zaměřit na aspekty obchodní domény nebo chování specifické pro filtr.</span><span class="sxs-lookup"><span data-stu-id="45d24-275">Should focus on business domain concerns or behavior specific to the filter.</span></span>
* <span data-ttu-id="45d24-276">By měl **není** protokolu akcí nebo jiné rozhraní události.</span><span class="sxs-lookup"><span data-stu-id="45d24-276">Should **not** log actions or other framework events.</span></span> <span data-ttu-id="45d24-277">Integrované filtry akce a framework události protokolu.</span><span class="sxs-lookup"><span data-stu-id="45d24-277">The built in filters log actions and framework events.</span></span>

### <a name="servicefilterattribute"></a><span data-ttu-id="45d24-278">ServiceFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="45d24-278">ServiceFilterAttribute</span></span>

<span data-ttu-id="45d24-279">Tyto typy implementace filtr služby jsou registrované ve `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="45d24-279">Service filter implementation types are registered in `ConfigureServices`.</span></span> <span data-ttu-id="45d24-280">A <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> načte z DI instance filtru.</span><span class="sxs-lookup"><span data-stu-id="45d24-280">A <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> retrieves an instance of the filter from DI.</span></span>

<span data-ttu-id="45d24-281">Následující kód ukazuje `AddHeaderResultServiceFilter`:</span><span class="sxs-lookup"><span data-stu-id="45d24-281">The following code shows the `AddHeaderResultServiceFilter`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="45d24-282">V následujícím kódu `AddHeaderResultServiceFilter` se přidá do kontejnerů DI:</span><span class="sxs-lookup"><span data-stu-id="45d24-282">In the following code, `AddHeaderResultServiceFilter` is added to the DI container:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

<span data-ttu-id="45d24-283">V následujícím kódu `ServiceFilter` atribut načte instanci `AddHeaderResultServiceFilter` filtr ze sloupce DI:</span><span class="sxs-lookup"><span data-stu-id="45d24-283">In the following code, the `ServiceFilter` attribute retrieves an instance of the `AddHeaderResultServiceFilter` filter from DI:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_ServiceFilter&highlight=1)]

<span data-ttu-id="45d24-284">Při použití `ServiceFilterAttribute`a nastavte [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span><span class="sxs-lookup"><span data-stu-id="45d24-284">When using `ServiceFilterAttribute`, setting [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span></span>

* <span data-ttu-id="45d24-285">Poskytuje nápovědu, která instance filtru *může* znovu použít mimo byl vytvořen v rámci oboru požadavku.</span><span class="sxs-lookup"><span data-stu-id="45d24-285">Provides a hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="45d24-286">Modul runtime ASP.NET Core nezaručuje:</span><span class="sxs-lookup"><span data-stu-id="45d24-286">The ASP.NET Core runtime doesn't guarantee:</span></span>

  * <span data-ttu-id="45d24-287">Vytvoří se jedna instance filtru.</span><span class="sxs-lookup"><span data-stu-id="45d24-287">That a single instance of the filter will be created.</span></span>
  * <span data-ttu-id="45d24-288">Filtr nebudou vyžádána znovu z kontejnerů DI někdy později.</span><span class="sxs-lookup"><span data-stu-id="45d24-288">The filter will not be re-requested from the DI container at some later point.</span></span>

* <span data-ttu-id="45d24-289">Se nemá používat s filtr, který závisí na službách s životností jiného než typu singleton.</span><span class="sxs-lookup"><span data-stu-id="45d24-289">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

 <span data-ttu-id="45d24-290"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implementuje <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span><span class="sxs-lookup"><span data-stu-id="45d24-290"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="45d24-291">`IFilterFactory` Zpřístupňuje <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> metodu pro vytvoření <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span><span class="sxs-lookup"><span data-stu-id="45d24-291">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="45d24-292">`CreateInstance` načte zadaného typu z DI.</span><span class="sxs-lookup"><span data-stu-id="45d24-292">`CreateInstance` loads the specified type from DI.</span></span>

### <a name="typefilterattribute"></a><span data-ttu-id="45d24-293">TypeFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="45d24-293">TypeFilterAttribute</span></span>

<span data-ttu-id="45d24-294"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> je podobný <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, ale jeho typ není přímo z kontejnerů DI přeložit.</span><span class="sxs-lookup"><span data-stu-id="45d24-294"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> is similar to <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, but its type isn't resolved directly from the DI container.</span></span> <span data-ttu-id="45d24-295">Vytvoření instance typu pomocí <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="45d24-295">It instantiates the type by using <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span></span>

<span data-ttu-id="45d24-296">Protože `TypeFilterAttribute` nejsou přímo z kontejnerů DI přeložit typy:</span><span class="sxs-lookup"><span data-stu-id="45d24-296">Because `TypeFilterAttribute` types aren't resolved directly from the DI container:</span></span>

* <span data-ttu-id="45d24-297">Typy, které jsou odkazovány pomocí `TypeFilterAttribute` nemusíte být registrována pomocí kontejnerů DI.</span><span class="sxs-lookup"><span data-stu-id="45d24-297">Types that are referenced using the `TypeFilterAttribute` don't need to be registered with the DI container.</span></span>  <span data-ttu-id="45d24-298">Mají jejich závislosti podle kontejnerů DI splněny.</span><span class="sxs-lookup"><span data-stu-id="45d24-298">They do have their dependencies fulfilled by the DI container.</span></span>
* <span data-ttu-id="45d24-299">`TypeFilterAttribute` Volitelně může přijímat argumenty konstruktoru typu.</span><span class="sxs-lookup"><span data-stu-id="45d24-299">`TypeFilterAttribute` can optionally accept constructor arguments for the type.</span></span>

<span data-ttu-id="45d24-300">Při použití `TypeFilterAttribute`a nastavte [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span><span class="sxs-lookup"><span data-stu-id="45d24-300">When using `TypeFilterAttribute`, setting [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span></span>
* <span data-ttu-id="45d24-301">Poskytuje nápovědu, která instance filtru *může* znovu použít mimo byl vytvořen v rámci oboru požadavku.</span><span class="sxs-lookup"><span data-stu-id="45d24-301">Provides hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="45d24-302">Modul runtime ASP.NET Core poskytuje žádnou záruku, že jedna instance filtru bude vytvořen.</span><span class="sxs-lookup"><span data-stu-id="45d24-302">The ASP.NET Core runtime provides no guarantees that a single instance of the filter will be created.</span></span>

* <span data-ttu-id="45d24-303">Se nemá používat s filtr, který závisí na službách s životností jiného než typu singleton.</span><span class="sxs-lookup"><span data-stu-id="45d24-303">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

<span data-ttu-id="45d24-304">Následující příklad ukazuje, jak předat argumenty typu pomocí `TypeFilterAttribute`:</span><span class="sxs-lookup"><span data-stu-id="45d24-304">The following example shows how to pass arguments to a type using `TypeFilterAttribute`:</span></span>

[!code-csharp[](../../mvc/controllers/filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_TypeFilter&highlight=1,2)]
[!code-csharp[](../../mvc/controllers/filters/sample/FiltersSample/Filters/LogConstantFilter.cs?name=snippet_TypeFilter_Implementation&highlight=6)]

<!-- 
https://localhost:5001/home/hi?name=joe
VS debug window shows 
FiltersSample.Filters.LogConstantFilter:Information: Method 'Hi' called
-->

## <a name="authorization-filters"></a><span data-ttu-id="45d24-305">Filtry autorizace</span><span class="sxs-lookup"><span data-stu-id="45d24-305">Authorization filters</span></span>

<span data-ttu-id="45d24-306">Filtry autorizace:</span><span class="sxs-lookup"><span data-stu-id="45d24-306">Authorization filters:</span></span>

* <span data-ttu-id="45d24-307">Běží první filtry v kanálu filtru.</span><span class="sxs-lookup"><span data-stu-id="45d24-307">Are the first filters run in the filter pipeline.</span></span>
* <span data-ttu-id="45d24-308">Řízení přístupu na metody akce.</span><span class="sxs-lookup"><span data-stu-id="45d24-308">Control access to action methods.</span></span>
* <span data-ttu-id="45d24-309">Máte před metodou, ale ne po metodě.</span><span class="sxs-lookup"><span data-stu-id="45d24-309">Have a before method, but no after method.</span></span>

<span data-ttu-id="45d24-310">Filtry autorizace vyžadují framework vlastní autorizace.</span><span class="sxs-lookup"><span data-stu-id="45d24-310">Custom authorization filters require a custom authorization framework.</span></span> <span data-ttu-id="45d24-311">Preferovat konfigurace zásad autorizace nebo zápis vlastní zásady autorizace přes psaní vlastního filtru.</span><span class="sxs-lookup"><span data-stu-id="45d24-311">Prefer configuring the authorization policies or writing a custom authorization policy over writing a custom filter.</span></span> <span data-ttu-id="45d24-312">Filtr autorizace integrovaná:</span><span class="sxs-lookup"><span data-stu-id="45d24-312">The built-in authorization filter:</span></span>

* <span data-ttu-id="45d24-313">Volání autorizace systému.</span><span class="sxs-lookup"><span data-stu-id="45d24-313">Calls the authorization system.</span></span>
* <span data-ttu-id="45d24-314">Nepovolí požadavky.</span><span class="sxs-lookup"><span data-stu-id="45d24-314">Does not authorize requests.</span></span>

<span data-ttu-id="45d24-315">Proveďte **není** vyvolat výjimky v rámci filtry autorizace:</span><span class="sxs-lookup"><span data-stu-id="45d24-315">Do **not** throw exceptions within authorization filters:</span></span>

* <span data-ttu-id="45d24-316">Tato výjimka není ošetřena.</span><span class="sxs-lookup"><span data-stu-id="45d24-316">The exception will not be handled.</span></span>
* <span data-ttu-id="45d24-317">Filtry výjimek nebude zpracování výjimky.</span><span class="sxs-lookup"><span data-stu-id="45d24-317">Exception filters will not handle the exception.</span></span>

<span data-ttu-id="45d24-318">Zvažte výzvu, když dojde k výjimce v filtr autorizace.</span><span class="sxs-lookup"><span data-stu-id="45d24-318">Consider issuing a challenge when an exception occurs in an authorization filter.</span></span>

<span data-ttu-id="45d24-319">Další informace o [autorizace](xref:security/authorization/introduction).</span><span class="sxs-lookup"><span data-stu-id="45d24-319">Learn more about [Authorization](xref:security/authorization/introduction).</span></span>

## <a name="resource-filters"></a><span data-ttu-id="45d24-320">Filtry prostředků</span><span class="sxs-lookup"><span data-stu-id="45d24-320">Resource filters</span></span>

<span data-ttu-id="45d24-321">Filtry prostředků:</span><span class="sxs-lookup"><span data-stu-id="45d24-321">Resource filters:</span></span>

* <span data-ttu-id="45d24-322">Implementovat buď <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> nebo <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> rozhraní.</span><span class="sxs-lookup"><span data-stu-id="45d24-322">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> interface.</span></span>
* <span data-ttu-id="45d24-323">Spuštění zabalí většinu kanálu filtru.</span><span class="sxs-lookup"><span data-stu-id="45d24-323">Execution wraps most of the filter pipeline.</span></span>
* <span data-ttu-id="45d24-324">Pouze [filtry autorizace](#authorization-filters) spustit před filtry prostředků.</span><span class="sxs-lookup"><span data-stu-id="45d24-324">Only [Authorization filters](#authorization-filters) run before resource filters.</span></span>

<span data-ttu-id="45d24-325">Filtry prostředků jsou užitečné pro zkrácenou většinu kanálu.</span><span class="sxs-lookup"><span data-stu-id="45d24-325">Resource filters are useful to short-circuit most of the pipeline.</span></span> <span data-ttu-id="45d24-326">Například filtr ukládání do mezipaměti se můžete vyhnout zbytku na přístup do mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="45d24-326">For example, a caching filter can avoid the rest of the pipeline on a cache hit.</span></span>

<span data-ttu-id="45d24-327">Příklady prostředků filtru:</span><span class="sxs-lookup"><span data-stu-id="45d24-327">Resource filter examples:</span></span>

* <span data-ttu-id="45d24-328">[Short-circuiting filtr prostředků](#short-circuiting-resource-filter) uvedenému výše.</span><span class="sxs-lookup"><span data-stu-id="45d24-328">[The short-circuiting resource filter](#short-circuiting-resource-filter) shown previously.</span></span>
* <span data-ttu-id="45d24-329">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span><span class="sxs-lookup"><span data-stu-id="45d24-329">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span></span>

  * <span data-ttu-id="45d24-330">Zabraňuje vazby modelu v přístupu k data formuláře.</span><span class="sxs-lookup"><span data-stu-id="45d24-330">Prevents model binding from accessing the form data.</span></span>
  * <span data-ttu-id="45d24-331">Používá se pro nahrávání velkých souborů zabránit načítána do paměti data formuláře.</span><span class="sxs-lookup"><span data-stu-id="45d24-331">Used for large file uploads to prevent the form data from being read into memory.</span></span>

## <a name="action-filters"></a><span data-ttu-id="45d24-332">Filtry akcí</span><span class="sxs-lookup"><span data-stu-id="45d24-332">Action filters</span></span>

> [!IMPORTANT]
> <span data-ttu-id="45d24-333">Filtry akce provést **není** platí pro stránky Razor.</span><span class="sxs-lookup"><span data-stu-id="45d24-333">Action filters do **not** apply to Razor Pages.</span></span> <span data-ttu-id="45d24-334">Stránky Razor podporuje <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> a <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span><span class="sxs-lookup"><span data-stu-id="45d24-334">Razor Pages supports <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span></span> <span data-ttu-id="45d24-335">Další informace najdete v tématu [metody filtrování pro Razor Pages](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="45d24-335">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

<span data-ttu-id="45d24-336">Filtry akcí:</span><span class="sxs-lookup"><span data-stu-id="45d24-336">Action filters:</span></span>

* <span data-ttu-id="45d24-337">Implementovat buď <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> nebo <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> rozhraní.</span><span class="sxs-lookup"><span data-stu-id="45d24-337">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> interface.</span></span>
* <span data-ttu-id="45d24-338">Jejich spuštění obklopuje provádění metody akce.</span><span class="sxs-lookup"><span data-stu-id="45d24-338">Their execution surrounds the execution of action methods.</span></span>

<span data-ttu-id="45d24-339">Následující kód ukazuje ukázkový filtr akce:</span><span class="sxs-lookup"><span data-stu-id="45d24-339">The following code shows a sample action filter:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="45d24-340"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> Poskytuje následující vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="45d24-340">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> provides the following properties:</span></span>

* <span data-ttu-id="45d24-341"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> – umožňuje načíst vstupní hodnoty pro metodu akce.</span><span class="sxs-lookup"><span data-stu-id="45d24-341"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> - enables the inputs to an action method be read.</span></span>
* <span data-ttu-id="45d24-342"><xref:Microsoft.AspNetCore.Mvc.Controller> – umožňuje manipulaci s instance kontroleru.</span><span class="sxs-lookup"><span data-stu-id="45d24-342"><xref:Microsoft.AspNetCore.Mvc.Controller> - enables manipulating the controller instance.</span></span>
* <span data-ttu-id="45d24-343"><xref:System.Web.Mvc.ActionExecutingContext.Result> -nastavení `Result` zkratům provádění metody akce a filtry následné akce.</span><span class="sxs-lookup"><span data-stu-id="45d24-343"><xref:System.Web.Mvc.ActionExecutingContext.Result> - setting `Result` short-circuits execution of the action method and subsequent action filters.</span></span>

<span data-ttu-id="45d24-344">Došlo k výjimce v metodě akce:</span><span class="sxs-lookup"><span data-stu-id="45d24-344">Throwing an exception in an action method:</span></span>

* <span data-ttu-id="45d24-345">Zabraňuje spuštění následujícími filtry.</span><span class="sxs-lookup"><span data-stu-id="45d24-345">Prevents running of subsequent filters.</span></span>
* <span data-ttu-id="45d24-346">Na rozdíl od nastavení `Result`, je považováno za selhání místo úspěšný výsledek.</span><span class="sxs-lookup"><span data-stu-id="45d24-346">Unlike setting `Result`, is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="45d24-347"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> Poskytuje `Controller` a `Result` plus následující vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="45d24-347">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> provides `Controller` and `Result` plus the following properties:</span></span>

* <span data-ttu-id="45d24-348"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> -True, pokud spuštění akce byla zkratována jiný filtr.</span><span class="sxs-lookup"><span data-stu-id="45d24-348"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> - True if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="45d24-349"><xref:System.Web.Mvc.ActionExecutedContext.Exception> Bez-hodnota null, pokud akci nebo filtr dříve spuštění akce došlo k výjimce.</span><span class="sxs-lookup"><span data-stu-id="45d24-349"><xref:System.Web.Mvc.ActionExecutedContext.Exception> - Non-null if the action or a previously run action filter threw an exception.</span></span> <span data-ttu-id="45d24-350">Nastavení této vlastnosti na hodnotu null:</span><span class="sxs-lookup"><span data-stu-id="45d24-350">Setting this property to null:</span></span>

  * <span data-ttu-id="45d24-351">Efektivně ošetří výjimku.</span><span class="sxs-lookup"><span data-stu-id="45d24-351">Effectively handles the exception.</span></span>
  * <span data-ttu-id="45d24-352">`Result` je provedeno, jako kdyby byla vrácena z metody akce.</span><span class="sxs-lookup"><span data-stu-id="45d24-352">`Result` is executed as if it was returned from the action method.</span></span>

<span data-ttu-id="45d24-353">Pro `IAsyncActionFilter`, volání <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span><span class="sxs-lookup"><span data-stu-id="45d24-353">For an `IAsyncActionFilter`, a call to the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span></span>

* <span data-ttu-id="45d24-354">Spustí všechny filtry následné akce a metody akce.</span><span class="sxs-lookup"><span data-stu-id="45d24-354">Executes any subsequent action filters and the action method.</span></span>
* <span data-ttu-id="45d24-355">Vrátí `ActionExecutedContext`.</span><span class="sxs-lookup"><span data-stu-id="45d24-355">Returns `ActionExecutedContext`.</span></span>

<span data-ttu-id="45d24-356">Chcete-li zkrácenou, přiřaďte <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> do výsledku instance a volat `next` ( `ActionExecutionDelegate`).</span><span class="sxs-lookup"><span data-stu-id="45d24-356">To short-circuit, assign <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> to a result instance and don't call `next` (the `ActionExecutionDelegate`).</span></span>

<span data-ttu-id="45d24-357">Rozhraní poskytuje abstraktní <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> , který může být rozčlenit do podtříd.</span><span class="sxs-lookup"><span data-stu-id="45d24-357">The framework provides an abstract <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> that can be subclassed.</span></span>

<span data-ttu-id="45d24-358">`OnActionExecuting` Filtr akce lze provádět:</span><span class="sxs-lookup"><span data-stu-id="45d24-358">The `OnActionExecuting` action filter can be used to:</span></span>

* <span data-ttu-id="45d24-359">Ověření stavu modelu.</span><span class="sxs-lookup"><span data-stu-id="45d24-359">Validate model state.</span></span>
* <span data-ttu-id="45d24-360">Vrátí chybu, pokud stav není platný.</span><span class="sxs-lookup"><span data-stu-id="45d24-360">Return an error if the state is invalid.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet)]

<span data-ttu-id="45d24-361">`OnActionExecuted` Metoda spustí po metodě akce:</span><span class="sxs-lookup"><span data-stu-id="45d24-361">The `OnActionExecuted` method runs after the action method:</span></span>

* <span data-ttu-id="45d24-362">A můžete zobrazit a pracovat s výsledky akce prostřednictvím <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> vlastnost.</span><span class="sxs-lookup"><span data-stu-id="45d24-362">And can see and manipulate the results of the action through the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> property.</span></span>
* <span data-ttu-id="45d24-363"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> je nastavena na hodnotu true, pokud spuštění akce byla zkratována jiný filtr.</span><span class="sxs-lookup"><span data-stu-id="45d24-363"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> is set to true if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="45d24-364"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> je nastavena na hodnotu než null, pokud akci nebo filtr následné akce došlo k výjimce.</span><span class="sxs-lookup"><span data-stu-id="45d24-364"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> is set to a non-null value if the action or a subsequent action filter threw an exception.</span></span> <span data-ttu-id="45d24-365">Nastavení `Exception` na hodnotu null:</span><span class="sxs-lookup"><span data-stu-id="45d24-365">Setting `Exception` to null:</span></span>

  * <span data-ttu-id="45d24-366">Efektivně zpracovává výjimku.</span><span class="sxs-lookup"><span data-stu-id="45d24-366">Effectively handles an exception.</span></span>
  * <span data-ttu-id="45d24-367">`ActionExecutedContext.Result` je provedeno, jako kdyby byly obvykle vrátil z metody akce.</span><span class="sxs-lookup"><span data-stu-id="45d24-367">`ActionExecutedContext.Result` is executed as if it were returned normally from the action method.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet2&higlight=12-99)]

## <a name="exception-filters"></a><span data-ttu-id="45d24-368">Filtry výjimek</span><span class="sxs-lookup"><span data-stu-id="45d24-368">Exception filters</span></span>

<span data-ttu-id="45d24-369">Filtry výjimek:</span><span class="sxs-lookup"><span data-stu-id="45d24-369">Exception filters:</span></span>

* <span data-ttu-id="45d24-370">Implementace <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> nebo <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span><span class="sxs-lookup"><span data-stu-id="45d24-370">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span></span> 
* <span data-ttu-id="45d24-371">Je možné implementovat zásady pro zpracování běžných chyb.</span><span class="sxs-lookup"><span data-stu-id="45d24-371">Can be used to implement common error handling policies.</span></span>

<span data-ttu-id="45d24-372">Následující vzorek filtru výjimek zobrazení vlastní chybové používá k zobrazení podrobností o výjimkách, ke kterým dochází, když je aplikace ve vývoji:</span><span class="sxs-lookup"><span data-stu-id="45d24-372">The following sample exception filter uses a custom error view to display details about exceptions that occur when the app is in development:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/CustomExceptionFilterAttribute.cs?name=snippet_ExceptionFilter&highlight=16-19)]

<span data-ttu-id="45d24-373">Filtry výjimek:</span><span class="sxs-lookup"><span data-stu-id="45d24-373">Exception filters:</span></span>

* <span data-ttu-id="45d24-374">Není k dispozici před a po události.</span><span class="sxs-lookup"><span data-stu-id="45d24-374">Don't have before and after events.</span></span>
* <span data-ttu-id="45d24-375">Implementace <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> nebo <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span><span class="sxs-lookup"><span data-stu-id="45d24-375">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span></span>
* <span data-ttu-id="45d24-376">Zpracování neošetřených výjimek, ke kterým dochází v stránky Razor nebo vytváření řadiče [vazby modelu](xref:mvc/models/model-binding), filtrů Akce nebo metody akce.</span><span class="sxs-lookup"><span data-stu-id="45d24-376">Handle unhandled exceptions that occur in Razor Page or controller creation, [model binding](xref:mvc/models/model-binding), action filters, or action methods.</span></span>
* <span data-ttu-id="45d24-377">Proveďte **není** zachytit výjimky, ke kterým dochází v prostředku filtry, filtry výsledků nebo provedení výsledku MVC.</span><span class="sxs-lookup"><span data-stu-id="45d24-377">Do **not** catch exceptions that occur in resource filters, result filters, or MVC result execution.</span></span>

<span data-ttu-id="45d24-378">Ke zpracování výjimky, nastavte <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> vlastnost `true` nebo zápisu odpovědi.</span><span class="sxs-lookup"><span data-stu-id="45d24-378">To handle an exception, set the <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> property to `true` or write a response.</span></span> <span data-ttu-id="45d24-379">Tím se zastaví šíření výjimky.</span><span class="sxs-lookup"><span data-stu-id="45d24-379">This stops propagation of the exception.</span></span> <span data-ttu-id="45d24-380">Filtr výjimek nelze zapnout výjimku do "ÚSPĚCH".</span><span class="sxs-lookup"><span data-stu-id="45d24-380">An exception filter can't turn an exception into a "success".</span></span> <span data-ttu-id="45d24-381">Pouze filtru akce můžete to udělat.</span><span class="sxs-lookup"><span data-stu-id="45d24-381">Only an action filter can do that.</span></span>

<span data-ttu-id="45d24-382">Filtry výjimek:</span><span class="sxs-lookup"><span data-stu-id="45d24-382">Exception filters:</span></span>

* <span data-ttu-id="45d24-383">Jsou vhodné pro zachytávání výjimek, které se vyskytují v rámci akce.</span><span class="sxs-lookup"><span data-stu-id="45d24-383">Are good for trapping exceptions that occur within actions.</span></span>
* <span data-ttu-id="45d24-384">Nejsou tak flexibilní jako middleware pro zpracování chyb.</span><span class="sxs-lookup"><span data-stu-id="45d24-384">Are not as flexible as error handling middleware.</span></span>

<span data-ttu-id="45d24-385">Preferovat middleware pro zpracování výjimek.</span><span class="sxs-lookup"><span data-stu-id="45d24-385">Prefer middleware for exception handling.</span></span> <span data-ttu-id="45d24-386">Výjimky použijte filtry pouze tehdy, pokud zpracování chyb *se liší* v závislosti na způsobu akce je volána.</span><span class="sxs-lookup"><span data-stu-id="45d24-386">Use exception filters only where error handling *differs* based on which action method is called.</span></span> <span data-ttu-id="45d24-387">Aplikace může mít například metody akci pro oba koncové body rozhraní API a pro zobrazení a HTML.</span><span class="sxs-lookup"><span data-stu-id="45d24-387">For example, an app might have action methods for both API endpoints and for views/HTML.</span></span> <span data-ttu-id="45d24-388">Koncové body rozhraní API můžou se zobrazovat informace o chybě jako dokumenty JSON, zatímco akce na základě zobrazení může vrátit chybovou stránku ve formátu HTML.</span><span class="sxs-lookup"><span data-stu-id="45d24-388">The API endpoints could return error information as JSON, while the view-based actions could return an error page as HTML.</span></span>

## <a name="result-filters"></a><span data-ttu-id="45d24-389">Filtry výsledků</span><span class="sxs-lookup"><span data-stu-id="45d24-389">Result filters</span></span>

<span data-ttu-id="45d24-390">Filtry výsledků:</span><span class="sxs-lookup"><span data-stu-id="45d24-390">Result filters:</span></span>

* <span data-ttu-id="45d24-391">Implementujte rozhraní:</span><span class="sxs-lookup"><span data-stu-id="45d24-391">Implement an interface:</span></span>
  * <span data-ttu-id="45d24-392"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> Nebo <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="45d24-392"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
  * <span data-ttu-id="45d24-393"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> Nebo <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span><span class="sxs-lookup"><span data-stu-id="45d24-393"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span></span>
* <span data-ttu-id="45d24-394">Jejich spuštění obklopuje provádění výsledky akce.</span><span class="sxs-lookup"><span data-stu-id="45d24-394">Their execution surrounds the execution of action results.</span></span>

### <a name="iresultfilter-and-iasyncresultfilter"></a><span data-ttu-id="45d24-395">IResultFilter a IAsyncResultFilter</span><span class="sxs-lookup"><span data-stu-id="45d24-395">IResultFilter and IAsyncResultFilter</span></span>

<span data-ttu-id="45d24-396">Následující kód ukazuje výsledek filtr, který přidá hlavičku protokolu HTTP:</span><span class="sxs-lookup"><span data-stu-id="45d24-396">The following code shows a result filter that adds an HTTP header:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="45d24-397">Typ výsledku prováděný závisí na akci.</span><span class="sxs-lookup"><span data-stu-id="45d24-397">The kind of result being executed depends on the action.</span></span> <span data-ttu-id="45d24-398">Akce vrácení zobrazení bude zahrnovat všechny razor zpracování v rámci <xref:Microsoft.AspNetCore.Mvc.ViewResult> spouštěna.</span><span class="sxs-lookup"><span data-stu-id="45d24-398">An action returning a view would include all razor processing as part of the <xref:Microsoft.AspNetCore.Mvc.ViewResult> being executed.</span></span> <span data-ttu-id="45d24-399">Metodu rozhraní API může provést některé serializace jako součást provedení výsledku.</span><span class="sxs-lookup"><span data-stu-id="45d24-399">An API method might perform some serialization as part of the execution of the result.</span></span> <span data-ttu-id="45d24-400">Další informace o [výsledky akcí](xref:mvc/controllers/actions)</span><span class="sxs-lookup"><span data-stu-id="45d24-400">Learn more about [action results](xref:mvc/controllers/actions)</span></span>

<span data-ttu-id="45d24-401">Filtry výsledků jsou vykonány pouze pro úspěšné výsledky – při akci nebo filtrů akce vytvoří výsledek akce.</span><span class="sxs-lookup"><span data-stu-id="45d24-401">Result filters are only executed for successful results - when the action or action filters produce an action result.</span></span> <span data-ttu-id="45d24-402">Filtry výsledků nejsou provedeny, když filtry výjimek zpracování výjimky.</span><span class="sxs-lookup"><span data-stu-id="45d24-402">Result filters are not executed when exception filters handle an exception.</span></span>

<span data-ttu-id="45d24-403"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> Metoda můžete zkrácenou provedení výsledku akce a filtry následujících výsledků nastavením <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> k `true`.</span><span class="sxs-lookup"><span data-stu-id="45d24-403">The <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> method can short-circuit execution of the action result and subsequent result filters by setting <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> to `true`.</span></span> <span data-ttu-id="45d24-404">Zápis do objektu odpovědi při zkrácení pro zabránění generování odpověď je prázdná.</span><span class="sxs-lookup"><span data-stu-id="45d24-404">Write to the response object when short-circuiting to avoid generating an empty response.</span></span> <span data-ttu-id="45d24-405">Došlo k výjimce `IResultFilter.OnResultExecuting` bude:</span><span class="sxs-lookup"><span data-stu-id="45d24-405">Throwing an exception in `IResultFilter.OnResultExecuting` will:</span></span>

* <span data-ttu-id="45d24-406">Zabraňte spuštění výsledku akce a následné filtry.</span><span class="sxs-lookup"><span data-stu-id="45d24-406">Prevent execution of the action result and subsequent filters.</span></span>
* <span data-ttu-id="45d24-407">Být považován za spadne, místo aby úspěšný výsledek.</span><span class="sxs-lookup"><span data-stu-id="45d24-407">Be treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="45d24-408">Když <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> metoda spuštění:</span><span class="sxs-lookup"><span data-stu-id="45d24-408">When the <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> method runs:</span></span>

* <span data-ttu-id="45d24-409">Odpověď pravděpodobně byl odeslán do klienta a nedá se změnit.</span><span class="sxs-lookup"><span data-stu-id="45d24-409">The response has likely been sent to the client and cannot be changed.</span></span>
* <span data-ttu-id="45d24-410">Pokud došlo k výjimce, nejsou zasílány text odpovědi.</span><span class="sxs-lookup"><span data-stu-id="45d24-410">If an exception was thrown, the response body is not sent.</span></span>

<!-- Review preceding "If an exception was thrown: Original 
When the OnResultExecuted method runs, the response has likely been sent to the client and cannot be changed further (unless an exception was thrown).

SHould that be , 
If an exception was thrown **IN THE RESULT FILTER**, the response body is not sent.

 -->

<span data-ttu-id="45d24-411">`ResultExecutedContext.Canceled` je nastavena na `true` Pokud provedení výsledku akce byla zkratována jiný filtr.</span><span class="sxs-lookup"><span data-stu-id="45d24-411">`ResultExecutedContext.Canceled` is set to `true` if the action result execution was short-circuited by another filter.</span></span>

<span data-ttu-id="45d24-412">`ResultExecutedContext.Exception` je nastavena na hodnotu než null, pokud výsledek akce nebo filtr následné výsledků došlo k výjimce.</span><span class="sxs-lookup"><span data-stu-id="45d24-412">`ResultExecutedContext.Exception` is set to a non-null value if the action result or a subsequent result filter threw an exception.</span></span> <span data-ttu-id="45d24-413">Nastavení `Exception` na null efektivně zpracovává výjimku a brání výjimka z právě znovu ASP.NET Core později v kanálu.</span><span class="sxs-lookup"><span data-stu-id="45d24-413">Setting `Exception` to null effectively handles an exception and prevents the exception from being rethrown by ASP.NET Core later in the pipeline.</span></span> <span data-ttu-id="45d24-414">Neexistuje žádný spolehlivý způsob při zpracování výjimky v filtr výsledků zapsat data do odpovědi.</span><span class="sxs-lookup"><span data-stu-id="45d24-414">There is no reliable way to write data to a response when handling an exception in a result filter.</span></span> <span data-ttu-id="45d24-415">Pokud hlavičky mají klientovi vyprazdňuje, pokud výsledek akce vyvolá výjimku, neexistuje žádný spolehlivý mechanismus poslat kód selhání.</span><span class="sxs-lookup"><span data-stu-id="45d24-415">If the headers have been flushed to the client when an action result throws an exception, there's no reliable mechanism to send a failure code.</span></span>

<span data-ttu-id="45d24-416">Pro <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, volání `await next` na <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> spustí všechny následné výsledek filtry a výsledku akce.</span><span class="sxs-lookup"><span data-stu-id="45d24-416">For an <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, a call to `await next` on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> executes any subsequent result filters and the action result.</span></span> <span data-ttu-id="45d24-417">Zkrácenou, nastavte [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) k `true` a nemůžete volat `ResultExecutionDelegate`:</span><span class="sxs-lookup"><span data-stu-id="45d24-417">To short-circuit, set [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) to `true` and don't call the `ResultExecutionDelegate`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MyAsyncResponseFilter.cs?name=snippet)]

<span data-ttu-id="45d24-418">Rozhraní poskytuje abstraktní `ResultFilterAttribute` , který může být rozčlenit do podtříd.</span><span class="sxs-lookup"><span data-stu-id="45d24-418">The framework provides an abstract `ResultFilterAttribute` that can be subclassed.</span></span> <span data-ttu-id="45d24-419">[AddHeaderAttribute](#add-header-attribute) uvedená třída dříve je příkladem atribut filtru výsledků.</span><span class="sxs-lookup"><span data-stu-id="45d24-419">The [AddHeaderAttribute](#add-header-attribute) class shown previously is an example of a result filter attribute.</span></span>

### <a name="ialwaysrunresultfilter-and-iasyncalwaysrunresultfilter"></a><span data-ttu-id="45d24-420">IAlwaysRunResultFilter a IAsyncAlwaysRunResultFilter</span><span class="sxs-lookup"><span data-stu-id="45d24-420">IAlwaysRunResultFilter and IAsyncAlwaysRunResultFilter</span></span>

<span data-ttu-id="45d24-421"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> a <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> rozhraní deklarovat <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> implementace, která spustí pro všechny výsledky akce.</span><span class="sxs-lookup"><span data-stu-id="45d24-421">The <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> interfaces declare an <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> implementation that runs for all action results.</span></span> <span data-ttu-id="45d24-422">Filtr platí pro všechny výsledky akce, pokud:</span><span class="sxs-lookup"><span data-stu-id="45d24-422">The filter is applied to all action results unless:</span></span>

* <span data-ttu-id="45d24-423"><xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> Nebo <xref:Microsoft.AspNetCore.Mvc.Filters.IAuthorizationFilter> platí a zkratům odpovědi.</span><span class="sxs-lookup"><span data-stu-id="45d24-423">An <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAuthorizationFilter> applies and short-circuits the response.</span></span>
* <span data-ttu-id="45d24-424">Filtr výjimek zpracovává výjimku vytvářením výsledek akce.</span><span class="sxs-lookup"><span data-stu-id="45d24-424">An exception filter handles an exception by producing an action result.</span></span>

<span data-ttu-id="45d24-425">Filtry pro jiné než `IExceptionFilter` a `IAuthorizationFilter` není zkrácenou `IAlwaysRunResultFilter` a `IAsyncAlwaysRunResultFilter`.</span><span class="sxs-lookup"><span data-stu-id="45d24-425">Filters other than `IExceptionFilter` and `IAuthorizationFilter` don't short-circuit `IAlwaysRunResultFilter` and `IAsyncAlwaysRunResultFilter`.</span></span>

<span data-ttu-id="45d24-426">Například následující filtr vždy spustí a nastaví výsledek akce (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) s *422 nezpracované Entity* stavový kód, pokud selže vyjednávání obsahu:</span><span class="sxs-lookup"><span data-stu-id="45d24-426">For example, the following filter always runs and sets an action result (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) with a *422 Unprocessable Entity* status code when content negotiation fails:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/UnprocessableResultFilter.cs?name=snippet)]

### <a name="ifilterfactory"></a><span data-ttu-id="45d24-427">IFilterFactory</span><span class="sxs-lookup"><span data-stu-id="45d24-427">IFilterFactory</span></span>

<span data-ttu-id="45d24-428"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implementuje <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span><span class="sxs-lookup"><span data-stu-id="45d24-428"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span></span> <span data-ttu-id="45d24-429">Proto `IFilterFactory` instance může sloužit jako `IFilterMetadata` instance kdekoli v kanálu filtru.</span><span class="sxs-lookup"><span data-stu-id="45d24-429">Therefore, an `IFilterFactory` instance can be used as an `IFilterMetadata` instance anywhere in the filter pipeline.</span></span> <span data-ttu-id="45d24-430">Když modul runtime se připravuje k vyvolání filtr, pokusí se vysílat na `IFilterFactory`.</span><span class="sxs-lookup"><span data-stu-id="45d24-430">When the runtime prepares to invoke the filter, it attempts to cast it to an `IFilterFactory`.</span></span> <span data-ttu-id="45d24-431">Pokud je úspěšná tohoto přetypování <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> metoda je volána k vytvoření `IFilterMetadata` instanci, která je vyvolána.</span><span class="sxs-lookup"><span data-stu-id="45d24-431">If that cast succeeds, the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method is called to create the `IFilterMetadata` instance that is invoked.</span></span> <span data-ttu-id="45d24-432">To poskytuje flexibilní návrhu, protože kanál přesné filtru nemusí být explicitně nastaveno při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="45d24-432">This provides a flexible design, since the precise filter pipeline doesn't need to be set explicitly when the app starts.</span></span>

<span data-ttu-id="45d24-433">`IFilterFactory` je možné implementovat jako jiný přístup k vytváření filtrů pomocí implementace vlastního atributu:</span><span class="sxs-lookup"><span data-stu-id="45d24-433">`IFilterFactory` can be implemented using custom attribute implementations as another approach to creating filters:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/AddHeaderWithFactoryAttribute.cs?name=snippet_IFilterFactory&highlight=1,4,5,6,7)]

<span data-ttu-id="45d24-434">Předchozí kód můžete otestovat spuštěním [stáhněte si ukázky](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample):</span><span class="sxs-lookup"><span data-stu-id="45d24-434">The preceding code can be tested by running the [download sample](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample):</span></span>

* <span data-ttu-id="45d24-435">Vyvolání vývojářských nástrojů F12.</span><span class="sxs-lookup"><span data-stu-id="45d24-435">Invoke the F12 developer tools.</span></span>
* <span data-ttu-id="45d24-436">Přejděte na `https://localhost:5001/Sample/HeaderWithFactory`</span><span class="sxs-lookup"><span data-stu-id="45d24-436">Navigate to `https://localhost:5001/Sample/HeaderWithFactory`</span></span>

<span data-ttu-id="45d24-437">Vývojářské nástroje F12 pomáhají zobrazit následující hlavičky odpovědi přidal ukázkový kód:</span><span class="sxs-lookup"><span data-stu-id="45d24-437">The F12 developer tools display the following response headers added by the sample code:</span></span>

* <span data-ttu-id="45d24-438">**Autor:** `Joe Smith`</span><span class="sxs-lookup"><span data-stu-id="45d24-438">**author:** `Joe Smith`</span></span>
* <span data-ttu-id="45d24-439">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span><span class="sxs-lookup"><span data-stu-id="45d24-439">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span></span>
* <span data-ttu-id="45d24-440">**Interní:** `My header`</span><span class="sxs-lookup"><span data-stu-id="45d24-440">**internal:** `My header`</span></span>

<span data-ttu-id="45d24-441">Předchozí kód vytvoří **interní:** `My header` hlavičky odpovědi.</span><span class="sxs-lookup"><span data-stu-id="45d24-441">The preceding code creates the **internal:** `My header` response header.</span></span>

### <a name="ifilterfactory-implemented-on-an-attribute"></a><span data-ttu-id="45d24-442">IFilterFactory implementovaná v atributu</span><span class="sxs-lookup"><span data-stu-id="45d24-442">IFilterFactory implemented on an attribute</span></span>

<!-- Review 
This section needs to be rewritten.
What's a non-named attribute?
-->

<span data-ttu-id="45d24-443">Filtry, které implementují `IFilterFactory` jsou užitečné pro filtry, které:</span><span class="sxs-lookup"><span data-stu-id="45d24-443">Filters that implement `IFilterFactory` are useful for filters that:</span></span>

* <span data-ttu-id="45d24-444">Předávání parametrů nevyžadují.</span><span class="sxs-lookup"><span data-stu-id="45d24-444">Don't require passing parameters.</span></span>
* <span data-ttu-id="45d24-445">Mají konstruktor závislosti, které potřebujete pro vyplnění podle DI.</span><span class="sxs-lookup"><span data-stu-id="45d24-445">Have constructor dependencies that need to be filled by DI.</span></span>

<span data-ttu-id="45d24-446"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implementuje <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span><span class="sxs-lookup"><span data-stu-id="45d24-446"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="45d24-447">`IFilterFactory` Zpřístupňuje <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> metodu pro vytvoření <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span><span class="sxs-lookup"><span data-stu-id="45d24-447">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="45d24-448">`CreateInstance` načte zadaného typu ze služby kontejnerů (DI).</span><span class="sxs-lookup"><span data-stu-id="45d24-448">`CreateInstance` loads the specified type from the services container (DI).</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/SampleActionFilterAttribute.cs?name=snippet_TypeFilterAttribute&highlight=1,3,7)]

<span data-ttu-id="45d24-449">Následující kód ukazuje tři přístupy k použití `[SampleActionFilter]`:</span><span class="sxs-lookup"><span data-stu-id="45d24-449">The following code shows three approaches to applying the `[SampleActionFilter]`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet&highlight=1)]

<span data-ttu-id="45d24-450">V předchozím kódu, upravení metody `[SampleActionFilter]` je upřednostňovaný způsob použití `SampleActionFilter`.</span><span class="sxs-lookup"><span data-stu-id="45d24-450">In the preceding code, decorating the method with `[SampleActionFilter]` is the preferred approach to applying the `SampleActionFilter`.</span></span>

## <a name="using-middleware-in-the-filter-pipeline"></a><span data-ttu-id="45d24-451">Pomocí middleware v kanálu filtru</span><span class="sxs-lookup"><span data-stu-id="45d24-451">Using middleware in the filter pipeline</span></span>

<span data-ttu-id="45d24-452">Filtry prostředků fungují jako [middleware](xref:fundamentals/middleware/index) v tom, že jsou před a za provádění všeho, která se dodává později v kanálu.</span><span class="sxs-lookup"><span data-stu-id="45d24-452">Resource filters work like [middleware](xref:fundamentals/middleware/index) in that they surround the execution of everything that comes later in the pipeline.</span></span> <span data-ttu-id="45d24-453">Ale filtry liší od middleware, že jsou součástí modulu runtime ASP.NET Core, což znamená, že mají přístup ke kontextu ASP.NET Core a konstrukce.</span><span class="sxs-lookup"><span data-stu-id="45d24-453">But filters differ from middleware in that they're part of the ASP.NET Core runtime, which means that they have access to ASP.NET Core context and constructs.</span></span>

<span data-ttu-id="45d24-454">Použití middlewaru jako filtr, vytvoření typu s `Configure` metodu, která určuje middlewaru, který má být vložení do kanálu filtru.</span><span class="sxs-lookup"><span data-stu-id="45d24-454">To use middleware as a filter, create a type with a `Configure` method that specifies the middleware to inject into the filter pipeline.</span></span> <span data-ttu-id="45d24-455">Lokalizace middleware pro aktuální jazykovou verzi pro žádost o navázání v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="45d24-455">The following example uses the localization middleware to establish the current culture for a request:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LocalizationPipeline.cs?name=snippet_MiddlewareFilter&highlight=3,21)]

<span data-ttu-id="45d24-456">Použití <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> pro spuštění middlewaru:</span><span class="sxs-lookup"><span data-stu-id="45d24-456">Use the <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> to run the middleware:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_MiddlewareFilter&highlight=2)]

<span data-ttu-id="45d24-457">Middleware filtry třídí ve stejné fázi kanálu filtr jako prostředek filtry, před navázáním modelu a po zbytek kanálu.</span><span class="sxs-lookup"><span data-stu-id="45d24-457">Middleware filters run at the same stage of the filter pipeline as Resource filters, before model binding and after the rest of the pipeline.</span></span>

## <a name="next-actions"></a><span data-ttu-id="45d24-458">Další akce</span><span class="sxs-lookup"><span data-stu-id="45d24-458">Next actions</span></span>

* <span data-ttu-id="45d24-459">Zobrazit [metody filtrování pro Razor Pages](xref:razor-pages/filter)</span><span class="sxs-lookup"><span data-stu-id="45d24-459">See [Filter methods for Razor Pages](xref:razor-pages/filter)</span></span>
* <span data-ttu-id="45d24-460">Můžete experimentovat s filtry, [stažení, otestovat a upravit na Githubu ukázky](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample).</span><span class="sxs-lookup"><span data-stu-id="45d24-460">To experiment with filters, [download, test, and modify the GitHub sample](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample).</span></span>
