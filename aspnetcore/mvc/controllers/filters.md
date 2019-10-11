---
title: Filtry v ASP.NET Core
author: ardalis
description: Přečtěte si, jak filtry fungují a jak je používat v ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 09/28/2019
uid: mvc/controllers/filters
ms.openlocfilehash: ed48c2074360768b8d8c5af7057b353b00592394
ms.sourcegitcommit: 73a451e9a58ac7102f90b608d661d8c23dd9bbaf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2019
ms.locfileid: "72037691"
---
# <a name="filters-in-aspnet-core"></a><span data-ttu-id="ddde3-103">Filtry v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ddde3-103">Filters in ASP.NET Core</span></span>

<span data-ttu-id="ddde3-104">[Kirka Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Dykstra](https://github.com/tdykstra/)a [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="ddde3-104">By [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="ddde3-105">*Filtry* v ASP.NET Core umožňují spuštění kódu před nebo po určitých fázích v kanálu zpracování požadavků.</span><span class="sxs-lookup"><span data-stu-id="ddde3-105">*Filters* in ASP.NET Core allow code to be run before or after specific stages in the request processing pipeline.</span></span>

<span data-ttu-id="ddde3-106">Předdefinované filtry zpracovávají úlohy, jako například:</span><span class="sxs-lookup"><span data-stu-id="ddde3-106">Built-in filters handle tasks such as:</span></span>

* <span data-ttu-id="ddde3-107">Autorizace (zabránění přístupu k prostředkům, ke kterým uživatel nemá oprávnění).</span><span class="sxs-lookup"><span data-stu-id="ddde3-107">Authorization (preventing access to resources a user isn't authorized for).</span></span>
* <span data-ttu-id="ddde3-108">Ukládání odpovědí do mezipaměti (při krátkém okruhu kanálu požadavků, který vrátí odpověď uloženou v mezipaměti).</span><span class="sxs-lookup"><span data-stu-id="ddde3-108">Response caching (short-circuiting the request pipeline to return a cached response).</span></span>

<span data-ttu-id="ddde3-109">Vlastní filtry je možné vytvořit pro zpracování vzájemně se týkajících se otázek.</span><span class="sxs-lookup"><span data-stu-id="ddde3-109">Custom filters can be created to handle cross-cutting concerns.</span></span> <span data-ttu-id="ddde3-110">Mezi obavy mezi průřezy patří zpracování chyb, ukládání do mezipaměti, konfigurace, autorizace a protokolování.</span><span class="sxs-lookup"><span data-stu-id="ddde3-110">Examples of cross-cutting concerns include error handling, caching, configuration, authorization, and logging.</span></span>  <span data-ttu-id="ddde3-111">Filtry zabraňují duplikování kódu.</span><span class="sxs-lookup"><span data-stu-id="ddde3-111">Filters avoid duplicating code.</span></span> <span data-ttu-id="ddde3-112">Například filtr výjimek zpracování chyb může konsolidovat zpracování chyb.</span><span class="sxs-lookup"><span data-stu-id="ddde3-112">For example, an error handling exception filter could consolidate error handling.</span></span>

<span data-ttu-id="ddde3-113">Tento dokument se týká Razor Pages, řadičů rozhraní API a řadičů se zobrazeními.</span><span class="sxs-lookup"><span data-stu-id="ddde3-113">This document applies to Razor Pages, API controllers, and controllers with views.</span></span>

<span data-ttu-id="ddde3-114">[Zobrazit nebo Stáhnout ukázku](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample) ([Jak stáhnout](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="ddde3-114">[View or download sample](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="how-filters-work"></a><span data-ttu-id="ddde3-115">Jak fungují filtry</span><span class="sxs-lookup"><span data-stu-id="ddde3-115">How filters work</span></span>

<span data-ttu-id="ddde3-116">Filtry se spouštějí v *kanálu vyvolání akce ASP.NET Core*, někdy označované jako *kanál filtru*.</span><span class="sxs-lookup"><span data-stu-id="ddde3-116">Filters run within the *ASP.NET Core action invocation pipeline*, sometimes referred to as the *filter pipeline*.</span></span>  <span data-ttu-id="ddde3-117">Kanál filtru se spustí po ASP.NET Core vybere akci, která se má provést.</span><span class="sxs-lookup"><span data-stu-id="ddde3-117">The filter pipeline runs after ASP.NET Core selects the action to execute.</span></span>

![Požadavek se zpracovává prostřednictvím jiného middlewaru, middleware směrování, výběru akcí a kanálu vyvolání akce ASP.NET Core.](filters/_static/filter-pipeline-1.png)

### <a name="filter-types"></a><span data-ttu-id="ddde3-120">Typy filtrů</span><span class="sxs-lookup"><span data-stu-id="ddde3-120">Filter types</span></span>

<span data-ttu-id="ddde3-121">Každý typ filtru se spustí v jiné fázi kanálu filtru:</span><span class="sxs-lookup"><span data-stu-id="ddde3-121">Each filter type is executed at a different stage in the filter pipeline:</span></span>

* <span data-ttu-id="ddde3-122">[Filtry autorizace](#authorization-filters) se spouštějí jako první a používají se k určení, jestli je uživatel autorizovaný pro požadavek.</span><span class="sxs-lookup"><span data-stu-id="ddde3-122">[Authorization filters](#authorization-filters) run first and are used to determine whether the user is authorized for the request.</span></span> <span data-ttu-id="ddde3-123">Filtry autorizace pro krátkodobé vynechání kanálu, pokud je požadavek neautorizovaný</span><span class="sxs-lookup"><span data-stu-id="ddde3-123">Authorization filters short-circuit the pipeline if the request is unauthorized.</span></span>

* <span data-ttu-id="ddde3-124">[Filtry prostředků](#resource-filters):</span><span class="sxs-lookup"><span data-stu-id="ddde3-124">[Resource filters](#resource-filters):</span></span>

  * <span data-ttu-id="ddde3-125">Spusťte po autorizaci.</span><span class="sxs-lookup"><span data-stu-id="ddde3-125">Run after authorization.</span></span>  
  * <span data-ttu-id="ddde3-126"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> může spustit kód před ostatními kanály filtru.</span><span class="sxs-lookup"><span data-stu-id="ddde3-126"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> can run code before the rest of the filter pipeline.</span></span> <span data-ttu-id="ddde3-127">Například `OnResourceExecuting` může spustit kód před vytvořením vazby modelu.</span><span class="sxs-lookup"><span data-stu-id="ddde3-127">For example, `OnResourceExecuting` can run code before model binding.</span></span>
  * <span data-ttu-id="ddde3-128"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> může spustit kód po dokončení zbývající části kanálu.</span><span class="sxs-lookup"><span data-stu-id="ddde3-128"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> can run code after the rest of the pipeline has completed.</span></span>

* <span data-ttu-id="ddde3-129">[Filtry akcí](#action-filters) mohou spustit kód bezprostředně před a po volání jednotlivé metody akce.</span><span class="sxs-lookup"><span data-stu-id="ddde3-129">[Action filters](#action-filters) can run code immediately before and after an individual action method is called.</span></span> <span data-ttu-id="ddde3-130">Lze je použít k manipulaci s argumenty předaných do akce a výsledek vráceného z akce.</span><span class="sxs-lookup"><span data-stu-id="ddde3-130">They can be used to manipulate the arguments passed into an action and the result returned from the action.</span></span> <span data-ttu-id="ddde3-131">Filtry **akcí nejsou v Razor Pages podporovány.**</span><span class="sxs-lookup"><span data-stu-id="ddde3-131">Action filters are **not** supported in Razor Pages.</span></span>

* <span data-ttu-id="ddde3-132">[Filtry výjimek](#exception-filters) slouží k aplikování globálních zásad na neošetřené výjimky, ke kterým dojde před zapsáním cokoli do těla odpovědi.</span><span class="sxs-lookup"><span data-stu-id="ddde3-132">[Exception filters](#exception-filters) are used to apply global policies to unhandled exceptions that occur before anything has been written to the response body.</span></span>

* <span data-ttu-id="ddde3-133">[Filtry výsledků](#result-filters) můžou spustit kód hned před a po provedení jednotlivých výsledků akce.</span><span class="sxs-lookup"><span data-stu-id="ddde3-133">[Result filters](#result-filters) can run code immediately before and after the execution of individual action results.</span></span> <span data-ttu-id="ddde3-134">Spustí se pouze v případě, že metoda akce byla úspěšně provedena.</span><span class="sxs-lookup"><span data-stu-id="ddde3-134">They run only when the action method has executed successfully.</span></span> <span data-ttu-id="ddde3-135">Jsou užitečné pro logiku, která musí obklopit zobrazení nebo formátovací modul.</span><span class="sxs-lookup"><span data-stu-id="ddde3-135">They are useful for logic that must surround view or formatter execution.</span></span>

<span data-ttu-id="ddde3-136">Následující diagram znázorňuje způsob interakce typů filtrů v kanálu filtru.</span><span class="sxs-lookup"><span data-stu-id="ddde3-136">The following diagram shows how filter types interact in the filter pipeline.</span></span>

![Požadavek se zpracovává pomocí autorizačních filtrů, filtrů prostředků, vazeb modelů, filtrů akcí, provádění akcí a konverze výsledků akcí, filtrů výjimek, výsledných filtrů a provádění výsledků.](filters/_static/filter-pipeline-2.png)

## <a name="implementation"></a><span data-ttu-id="ddde3-139">Implementace</span><span class="sxs-lookup"><span data-stu-id="ddde3-139">Implementation</span></span>

<span data-ttu-id="ddde3-140">Filtry podporují synchronní i asynchronní implementace prostřednictvím různých definic rozhraní.</span><span class="sxs-lookup"><span data-stu-id="ddde3-140">Filters support both synchronous and asynchronous implementations through different interface definitions.</span></span>

<span data-ttu-id="ddde3-141">Synchronní filtry mohou spustit kód před (`On-Stage-Executing`) a po (`On-Stage-Executed`) jejich fáze zřetězení.</span><span class="sxs-lookup"><span data-stu-id="ddde3-141">Synchronous filters can run code before (`On-Stage-Executing`) and after (`On-Stage-Executed`) their pipeline stage.</span></span> <span data-ttu-id="ddde3-142">Například `OnActionExecuting` je volána před voláním metody Action.</span><span class="sxs-lookup"><span data-stu-id="ddde3-142">For example, `OnActionExecuting` is called before the action method is called.</span></span> <span data-ttu-id="ddde3-143">`OnActionExecuted` se volá po návratu metody Action.</span><span class="sxs-lookup"><span data-stu-id="ddde3-143">`OnActionExecuted` is called after the action method returns.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="ddde3-144">Asynchronní filtry definují metodu `On-Stage-ExecutionAsync`:</span><span class="sxs-lookup"><span data-stu-id="ddde3-144">Asynchronous filters define an `On-Stage-ExecutionAsync` method:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/SampleAsyncActionFilter.cs?name=snippet)]

<span data-ttu-id="ddde3-145">V předchozím kódu `SampleAsyncActionFilter` má <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`), který provádí metodu Action.</span><span class="sxs-lookup"><span data-stu-id="ddde3-145">In the preceding code, the `SampleAsyncActionFilter` has an <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`)  that executes the action method.</span></span>  <span data-ttu-id="ddde3-146">Každá z metod `On-Stage-ExecutionAsync` vezme `FilterType-ExecutionDelegate`, který provádí fázi zřetězení filtru.</span><span class="sxs-lookup"><span data-stu-id="ddde3-146">Each of the `On-Stage-ExecutionAsync` methods take a `FilterType-ExecutionDelegate` that executes the filter's pipeline stage.</span></span>

### <a name="multiple-filter-stages"></a><span data-ttu-id="ddde3-147">Několik fází filtru</span><span class="sxs-lookup"><span data-stu-id="ddde3-147">Multiple filter stages</span></span>

<span data-ttu-id="ddde3-148">Rozhraní pro více fází filtru lze implementovat v rámci jedné třídy.</span><span class="sxs-lookup"><span data-stu-id="ddde3-148">Interfaces for multiple filter stages can be implemented in a single class.</span></span> <span data-ttu-id="ddde3-149">Například třída <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> implementuje `IActionFilter`, `IResultFilter` a jejich asynchronní ekvivalenty.</span><span class="sxs-lookup"><span data-stu-id="ddde3-149">For example, the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> class implements `IActionFilter`, `IResultFilter`, and their async equivalents.</span></span>

<span data-ttu-id="ddde3-150">Implementujte **buď** synchronní, nebo asynchronní verzi rozhraní filtru, **nikoli** obojí.</span><span class="sxs-lookup"><span data-stu-id="ddde3-150">Implement **either** the synchronous or the async version of a filter interface, **not** both.</span></span> <span data-ttu-id="ddde3-151">Modul runtime nejprve kontroluje, zda filtr implementuje asynchronní rozhraní a v takovém případě jej zavolá.</span><span class="sxs-lookup"><span data-stu-id="ddde3-151">The runtime checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="ddde3-152">V takovém případě volá metody synchronního rozhraní.</span><span class="sxs-lookup"><span data-stu-id="ddde3-152">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="ddde3-153">Pokud jsou asynchronní i synchronní rozhraní implementovány v jedné třídě, je volána pouze asynchronní metoda.</span><span class="sxs-lookup"><span data-stu-id="ddde3-153">If both asynchronous and synchronous interfaces are implemented in one class, only the async method is called.</span></span> <span data-ttu-id="ddde3-154">Při použití abstraktních tříd, jako je <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>, přepište pouze synchronní metody nebo asynchronní metodu pro každý typ filtru.</span><span class="sxs-lookup"><span data-stu-id="ddde3-154">When using abstract classes like <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> override only the synchronous methods or the async method for each filter type.</span></span>

### <a name="built-in-filter-attributes"></a><span data-ttu-id="ddde3-155">Předdefinované atributy filtru</span><span class="sxs-lookup"><span data-stu-id="ddde3-155">Built-in filter attributes</span></span>

<span data-ttu-id="ddde3-156">ASP.NET Core obsahuje integrované filtry založené na atributech, které mohou být roztříděné a přizpůsobené.</span><span class="sxs-lookup"><span data-stu-id="ddde3-156">ASP.NET Core includes built-in attribute-based filters that can be subclassed and customized.</span></span> <span data-ttu-id="ddde3-157">Například následující filtr výsledků přidá hlavičku do odpovědi:</span><span class="sxs-lookup"><span data-stu-id="ddde3-157">For example, the following result filter adds a header to the response:</span></span>

<a name="add-header-attribute"></a>

[!code-csharp[](./filters/sample/FiltersSample/Filters/AddHeaderAttribute.cs?name=snippet)]

<span data-ttu-id="ddde3-158">Atributy umožňují filtrům přijímat argumenty, jak je znázorněno v předchozím příkladu.</span><span class="sxs-lookup"><span data-stu-id="ddde3-158">Attributes allow filters to accept arguments, as shown in the preceding example.</span></span> <span data-ttu-id="ddde3-159">Použijte `AddHeaderAttribute` na metodu Controller nebo Action a zadejte název a hodnotu hlavičky HTTP:</span><span class="sxs-lookup"><span data-stu-id="ddde3-159">Apply the `AddHeaderAttribute` to a controller or action method and specify the name and value of the HTTP header:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

<!-- `https://localhost:5001/Sample` -->

<span data-ttu-id="ddde3-160">Některé z rozhraní filtru mají odpovídající atributy, které lze použít jako základní třídy pro vlastní implementace.</span><span class="sxs-lookup"><span data-stu-id="ddde3-160">Several of the filter interfaces have corresponding attributes that can be used as base classes for custom implementations.</span></span>

<span data-ttu-id="ddde3-161">Atributy filtru:</span><span class="sxs-lookup"><span data-stu-id="ddde3-161">Filter attributes:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ExceptionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>

## <a name="filter-scopes-and-order-of-execution"></a><span data-ttu-id="ddde3-162">Filtrovat obory a pořadí provádění</span><span class="sxs-lookup"><span data-stu-id="ddde3-162">Filter scopes and order of execution</span></span>

<span data-ttu-id="ddde3-163">Filtr lze přidat do kanálu v jednom ze tří *oborů*:</span><span class="sxs-lookup"><span data-stu-id="ddde3-163">A filter can be added to the pipeline at one of three *scopes*:</span></span>

* <span data-ttu-id="ddde3-164">Použití atributu pro akci.</span><span class="sxs-lookup"><span data-stu-id="ddde3-164">Using an attribute on an action.</span></span>
* <span data-ttu-id="ddde3-165">Použití atributu na řadiči.</span><span class="sxs-lookup"><span data-stu-id="ddde3-165">Using an attribute on a controller.</span></span>
* <span data-ttu-id="ddde3-166">Globálně pro všechny řadiče a akce, jak je znázorněno v následujícím kódu:</span><span class="sxs-lookup"><span data-stu-id="ddde3-166">Globally for all controllers and actions as shown in the following code:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/StartupGF.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="ddde3-167">Předchozí kód přičítá tři filtry globálně pomocí kolekce [MvcOptions. filters](xref:Microsoft.AspNetCore.Mvc.MvcOptions.Filters) .</span><span class="sxs-lookup"><span data-stu-id="ddde3-167">The preceding code adds three filters globally using the [MvcOptions.Filters](xref:Microsoft.AspNetCore.Mvc.MvcOptions.Filters) collection.</span></span>

### <a name="default-order-of-execution"></a><span data-ttu-id="ddde3-168">Výchozí pořadí provádění</span><span class="sxs-lookup"><span data-stu-id="ddde3-168">Default order of execution</span></span>

<span data-ttu-id="ddde3-169">Pokud existuje více filtrů pro určitou fázi kanálu, rozsah Určuje výchozí pořadí provádění filtru.</span><span class="sxs-lookup"><span data-stu-id="ddde3-169">When there are multiple filters for a particular stage of the pipeline, scope determines the default order of filter execution.</span></span>  <span data-ttu-id="ddde3-170">Globální filtry obklopují filtry tříd, které jsou ve filtru metod Surround.</span><span class="sxs-lookup"><span data-stu-id="ddde3-170">Global filters surround class filters, which in turn surround method filters.</span></span>

<span data-ttu-id="ddde3-171">V důsledku vnořování filtru je *po* kódu spuštěn v obráceném pořadí *před* kódem.</span><span class="sxs-lookup"><span data-stu-id="ddde3-171">As a result of filter nesting, the *after* code of filters runs in the reverse order of the *before* code.</span></span> <span data-ttu-id="ddde3-172">Pořadí filtru:</span><span class="sxs-lookup"><span data-stu-id="ddde3-172">The filter sequence:</span></span>

* <span data-ttu-id="ddde3-173">*Před* kódem globálních filtrů.</span><span class="sxs-lookup"><span data-stu-id="ddde3-173">The *before* code of global filters.</span></span>
  * <span data-ttu-id="ddde3-174">*Před* kódem filtru kontroleru.</span><span class="sxs-lookup"><span data-stu-id="ddde3-174">The *before* code of controller filters.</span></span>
    * <span data-ttu-id="ddde3-175">Kód *před* filtry metody Action.</span><span class="sxs-lookup"><span data-stu-id="ddde3-175">The *before* code of action method filters.</span></span>
    * <span data-ttu-id="ddde3-176">*Po* kódu filtry metody Action.</span><span class="sxs-lookup"><span data-stu-id="ddde3-176">The *after* code of action method filters.</span></span>
  * <span data-ttu-id="ddde3-177">*Po* kódu filtry kontroleru.</span><span class="sxs-lookup"><span data-stu-id="ddde3-177">The *after* code of controller filters.</span></span>
* <span data-ttu-id="ddde3-178">*Po* kódu globálních filtrů.</span><span class="sxs-lookup"><span data-stu-id="ddde3-178">The *after* code of global filters.</span></span>
  
<span data-ttu-id="ddde3-179">Následující příklad ilustruje pořadí, ve kterém jsou metody filtru volány pro filtry synchronních akcí.</span><span class="sxs-lookup"><span data-stu-id="ddde3-179">The following example that illustrates the order in which filter methods are called for synchronous action filters.</span></span>

| <span data-ttu-id="ddde3-180">Pořadí</span><span class="sxs-lookup"><span data-stu-id="ddde3-180">Sequence</span></span> | <span data-ttu-id="ddde3-181">Rozsah filtru</span><span class="sxs-lookup"><span data-stu-id="ddde3-181">Filter scope</span></span> | <span data-ttu-id="ddde3-182">Filter – metoda</span><span class="sxs-lookup"><span data-stu-id="ddde3-182">Filter method</span></span> |
|:--------:|:------------:|:-------------:|
| <span data-ttu-id="ddde3-183">1</span><span class="sxs-lookup"><span data-stu-id="ddde3-183">1</span></span> | <span data-ttu-id="ddde3-184">Globální</span><span class="sxs-lookup"><span data-stu-id="ddde3-184">Global</span></span> | `OnActionExecuting` |
| <span data-ttu-id="ddde3-185">2</span><span class="sxs-lookup"><span data-stu-id="ddde3-185">2</span></span> | <span data-ttu-id="ddde3-186">Kontrolér</span><span class="sxs-lookup"><span data-stu-id="ddde3-186">Controller</span></span> | `OnActionExecuting` |
| <span data-ttu-id="ddde3-187">3</span><span class="sxs-lookup"><span data-stu-id="ddde3-187">3</span></span> | <span data-ttu-id="ddde3-188">Metoda</span><span class="sxs-lookup"><span data-stu-id="ddde3-188">Method</span></span> | `OnActionExecuting` |
| <span data-ttu-id="ddde3-189">4</span><span class="sxs-lookup"><span data-stu-id="ddde3-189">4</span></span> | <span data-ttu-id="ddde3-190">Metoda</span><span class="sxs-lookup"><span data-stu-id="ddde3-190">Method</span></span> | `OnActionExecuted` |
| <span data-ttu-id="ddde3-191">5</span><span class="sxs-lookup"><span data-stu-id="ddde3-191">5</span></span> | <span data-ttu-id="ddde3-192">Kontrolér</span><span class="sxs-lookup"><span data-stu-id="ddde3-192">Controller</span></span> | `OnActionExecuted` |
| <span data-ttu-id="ddde3-193">6</span><span class="sxs-lookup"><span data-stu-id="ddde3-193">6</span></span> | <span data-ttu-id="ddde3-194">Globální</span><span class="sxs-lookup"><span data-stu-id="ddde3-194">Global</span></span> | `OnActionExecuted` |

<span data-ttu-id="ddde3-195">Tato posloupnost zobrazuje:</span><span class="sxs-lookup"><span data-stu-id="ddde3-195">This sequence shows:</span></span>

* <span data-ttu-id="ddde3-196">Filtr metod je vnořený do filtru kontroleru.</span><span class="sxs-lookup"><span data-stu-id="ddde3-196">The method filter is nested within the controller filter.</span></span>
* <span data-ttu-id="ddde3-197">Filtr kontroleru je vnořený do globálního filtru.</span><span class="sxs-lookup"><span data-stu-id="ddde3-197">The controller filter is nested within the global filter.</span></span>

### <a name="controller-and-razor-page-level-filters"></a><span data-ttu-id="ddde3-198">Filtry na úrovni ovladače a stránky Razor</span><span class="sxs-lookup"><span data-stu-id="ddde3-198">Controller and Razor Page level filters</span></span>

<span data-ttu-id="ddde3-199">Každý kontroler, který dědí ze základní třídy <xref:Microsoft.AspNetCore.Mvc.Controller>, zahrnuje metody [Controller. OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*), [Controller. OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*)a [Controller. OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
 @ no__t-5.</span><span class="sxs-lookup"><span data-stu-id="ddde3-199">Every controller that inherits from the <xref:Microsoft.AspNetCore.Mvc.Controller> base class includes [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*),  [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*), and [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted` methods.</span></span> <span data-ttu-id="ddde3-200">Tyto metody:</span><span class="sxs-lookup"><span data-stu-id="ddde3-200">These methods:</span></span>

* <span data-ttu-id="ddde3-201">Zabalte filtry, které se spouštějí pro danou akci.</span><span class="sxs-lookup"><span data-stu-id="ddde3-201">Wrap the filters that run for a given action.</span></span>
* <span data-ttu-id="ddde3-202">`OnActionExecuting` se volá před jakýmikoli filtry akce.</span><span class="sxs-lookup"><span data-stu-id="ddde3-202">`OnActionExecuting` is called before any of the action's filters.</span></span>
* <span data-ttu-id="ddde3-203">`OnActionExecuted` se volá po všech filtrech akcí.</span><span class="sxs-lookup"><span data-stu-id="ddde3-203">`OnActionExecuted` is called after all of the action filters.</span></span>
* <span data-ttu-id="ddde3-204">`OnActionExecutionAsync` se volá před jakýmikoli filtry akce.</span><span class="sxs-lookup"><span data-stu-id="ddde3-204">`OnActionExecutionAsync` is called before any of the action's filters.</span></span> <span data-ttu-id="ddde3-205">Kód v filtru po `next` se spustí po metodě Action.</span><span class="sxs-lookup"><span data-stu-id="ddde3-205">Code in the filter after `next` runs after the action method.</span></span>

<span data-ttu-id="ddde3-206">Například v ukázce stahování se při spuštění použije globálně `MySampleActionFilter`.</span><span class="sxs-lookup"><span data-stu-id="ddde3-206">For example, in the download sample, `MySampleActionFilter` is applied globally in startup.</span></span>

<span data-ttu-id="ddde3-207">@No__t-0:</span><span class="sxs-lookup"><span data-stu-id="ddde3-207">The `TestController`:</span></span>

* <span data-ttu-id="ddde3-208">Aplikuje na akci `FilterTest2` `SampleActionFilterAttribute` (`[SampleActionFilter]`):</span><span class="sxs-lookup"><span data-stu-id="ddde3-208">Applies the `SampleActionFilterAttribute` (`[SampleActionFilter]`) to the `FilterTest2` action:</span></span>
* <span data-ttu-id="ddde3-209">Přepisuje `OnActionExecuting` a `OnActionExecuted`.</span><span class="sxs-lookup"><span data-stu-id="ddde3-209">Overrides `OnActionExecuting` and `OnActionExecuted`.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/TestController.cs?name=snippet)]

<span data-ttu-id="ddde3-210">Přechod na @no__t – 0 spustí následující kód:</span><span class="sxs-lookup"><span data-stu-id="ddde3-210">Navigating to `https://localhost:5001/Test/FilterTest2` runs the following code:</span></span>

* `TestController.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `SampleActionFilterAttribute.OnActionExecuting`
      * `TestController.FilterTest2`
    * `SampleActionFilterAttribute.OnActionExecuted`
  * `MySampleActionFilter.OnActionExecuted`
* `TestController.OnActionExecuted`

<span data-ttu-id="ddde3-211">Razor Pages najdete v tématu [implementace filtrů stránek Razor pomocí přepsání metod filtru](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span><span class="sxs-lookup"><span data-stu-id="ddde3-211">For Razor Pages, see [Implement Razor Page filters by overriding filter methods](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span></span>

### <a name="overriding-the-default-order"></a><span data-ttu-id="ddde3-212">Přepsání výchozího pořadí</span><span class="sxs-lookup"><span data-stu-id="ddde3-212">Overriding the default order</span></span>

<span data-ttu-id="ddde3-213">Výchozí sekvenci spuštění lze přepsat implementací <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span><span class="sxs-lookup"><span data-stu-id="ddde3-213">The default sequence of execution can be overridden by implementing <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span></span> <span data-ttu-id="ddde3-214">`IOrderedFilter` zpřístupňuje vlastnost <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order>, která má přednost před rozsahem, aby bylo možné určit pořadí spouštění.</span><span class="sxs-lookup"><span data-stu-id="ddde3-214">`IOrderedFilter` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> property that takes precedence over scope to determine the order of execution.</span></span> <span data-ttu-id="ddde3-215">Filtr s nižší hodnotou @no__t 0:</span><span class="sxs-lookup"><span data-stu-id="ddde3-215">A filter with a lower `Order` value:</span></span>

* <span data-ttu-id="ddde3-216">Spustí *před* kódem filtr s vyšší hodnotou `Order`.</span><span class="sxs-lookup"><span data-stu-id="ddde3-216">Runs the *before* code before that of a filter with a higher value of `Order`.</span></span>
* <span data-ttu-id="ddde3-217">Spustí *za* kódem filtru s vyšší hodnotou `Order`.</span><span class="sxs-lookup"><span data-stu-id="ddde3-217">Runs the *after* code after that of a filter with a higher `Order` value.</span></span>

<span data-ttu-id="ddde3-218">Vlastnost `Order` lze nastavit pomocí parametru konstruktoru:</span><span class="sxs-lookup"><span data-stu-id="ddde3-218">The `Order` property can be set with a constructor parameter:</span></span>

```csharp
[MyFilter(Name = "Controller Level Attribute", Order=1)]
```

<span data-ttu-id="ddde3-219">Vezměte v úvahu stejné 3 filtry akcí, které jsou uvedené v předchozím příkladu.</span><span class="sxs-lookup"><span data-stu-id="ddde3-219">Consider the same 3 action filters shown in the preceding example.</span></span> <span data-ttu-id="ddde3-220">Pokud se vlastnost `Order` řadiče a globální filtry nastaví na 1 a 2 v uvedeném pořadí, pořadí spouštění se vrátí zpět.</span><span class="sxs-lookup"><span data-stu-id="ddde3-220">If the `Order` property of the controller and global filters is set to 1 and 2 respectively, the order of execution is reversed.</span></span>

| <span data-ttu-id="ddde3-221">Pořadí</span><span class="sxs-lookup"><span data-stu-id="ddde3-221">Sequence</span></span> | <span data-ttu-id="ddde3-222">Rozsah filtru</span><span class="sxs-lookup"><span data-stu-id="ddde3-222">Filter scope</span></span> | <span data-ttu-id="ddde3-223">vlastnost `Order`</span><span class="sxs-lookup"><span data-stu-id="ddde3-223">`Order` property</span></span> | <span data-ttu-id="ddde3-224">Filter – metoda</span><span class="sxs-lookup"><span data-stu-id="ddde3-224">Filter method</span></span> |
|:--------:|:------------:|:-----------------:|:-------------:|
| <span data-ttu-id="ddde3-225">1</span><span class="sxs-lookup"><span data-stu-id="ddde3-225">1</span></span> | <span data-ttu-id="ddde3-226">Metoda</span><span class="sxs-lookup"><span data-stu-id="ddde3-226">Method</span></span> | <span data-ttu-id="ddde3-227">0</span><span class="sxs-lookup"><span data-stu-id="ddde3-227">0</span></span> | `OnActionExecuting` |
| <span data-ttu-id="ddde3-228">2</span><span class="sxs-lookup"><span data-stu-id="ddde3-228">2</span></span> | <span data-ttu-id="ddde3-229">Kontrolér</span><span class="sxs-lookup"><span data-stu-id="ddde3-229">Controller</span></span> | <span data-ttu-id="ddde3-230">1</span><span class="sxs-lookup"><span data-stu-id="ddde3-230">1</span></span>  | `OnActionExecuting` |
| <span data-ttu-id="ddde3-231">3</span><span class="sxs-lookup"><span data-stu-id="ddde3-231">3</span></span> | <span data-ttu-id="ddde3-232">Globální</span><span class="sxs-lookup"><span data-stu-id="ddde3-232">Global</span></span> | <span data-ttu-id="ddde3-233">2</span><span class="sxs-lookup"><span data-stu-id="ddde3-233">2</span></span>  | `OnActionExecuting` |
| <span data-ttu-id="ddde3-234">4</span><span class="sxs-lookup"><span data-stu-id="ddde3-234">4</span></span> | <span data-ttu-id="ddde3-235">Globální</span><span class="sxs-lookup"><span data-stu-id="ddde3-235">Global</span></span> | <span data-ttu-id="ddde3-236">2</span><span class="sxs-lookup"><span data-stu-id="ddde3-236">2</span></span>  | `OnActionExecuted` |
| <span data-ttu-id="ddde3-237">5</span><span class="sxs-lookup"><span data-stu-id="ddde3-237">5</span></span> | <span data-ttu-id="ddde3-238">Kontrolér</span><span class="sxs-lookup"><span data-stu-id="ddde3-238">Controller</span></span> | <span data-ttu-id="ddde3-239">1</span><span class="sxs-lookup"><span data-stu-id="ddde3-239">1</span></span>  | `OnActionExecuted` |
| <span data-ttu-id="ddde3-240">6</span><span class="sxs-lookup"><span data-stu-id="ddde3-240">6</span></span> | <span data-ttu-id="ddde3-241">Metoda</span><span class="sxs-lookup"><span data-stu-id="ddde3-241">Method</span></span> | <span data-ttu-id="ddde3-242">0</span><span class="sxs-lookup"><span data-stu-id="ddde3-242">0</span></span>  | `OnActionExecuted` |

<span data-ttu-id="ddde3-243">Vlastnost `Order` Přepisuje obor při určování pořadí, ve kterém jsou filtry spouštěny.</span><span class="sxs-lookup"><span data-stu-id="ddde3-243">The `Order` property overrides scope when determining the order in which filters run.</span></span> <span data-ttu-id="ddde3-244">Filtry jsou seřazené podle pořadí, pak se k přerušení vztahů používá obor.</span><span class="sxs-lookup"><span data-stu-id="ddde3-244">Filters are sorted first by order, then scope is used to break ties.</span></span> <span data-ttu-id="ddde3-245">Všechny předdefinované filtry implementují `IOrderedFilter` a nastaví výchozí hodnotu `Order` na 0.</span><span class="sxs-lookup"><span data-stu-id="ddde3-245">All of the built-in filters implement `IOrderedFilter` and set the default `Order` value to 0.</span></span> <span data-ttu-id="ddde3-246">Pro předdefinované filtry rozsah určuje pořadí, pokud `Order` není nastaveno na nenulovou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="ddde3-246">For built-in filters, scope determines order unless `Order` is set to a non-zero value.</span></span>

## <a name="cancellation-and-short-circuiting"></a><span data-ttu-id="ddde3-247">Zrušení a zkrácení okruhů</span><span class="sxs-lookup"><span data-stu-id="ddde3-247">Cancellation and short-circuiting</span></span>

<span data-ttu-id="ddde3-248">Kanál filtru může být v krátkém okruhu nastavením vlastnosti <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> u parametru <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> poskytnutého metodě Filter.</span><span class="sxs-lookup"><span data-stu-id="ddde3-248">The filter pipeline can be short-circuited by setting the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> property on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> parameter provided to the filter method.</span></span> <span data-ttu-id="ddde3-249">Například následující filtr prostředků zabrání ve spuštění zbývajícího kanálu:</span><span class="sxs-lookup"><span data-stu-id="ddde3-249">For instance, the following Resource filter prevents the rest of the pipeline from executing:</span></span>

<a name="short-circuiting-resource-filter"></a>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ShortCircuitingResourceFilterAttribute.cs?name=snippet)]

<span data-ttu-id="ddde3-250">V následujícím kódu, jak `ShortCircuitingResourceFilter` a filtr `AddHeader` cílí na metodu akce `SomeResource`.</span><span class="sxs-lookup"><span data-stu-id="ddde3-250">In the following code, both the `ShortCircuitingResourceFilter` and the `AddHeader` filter target the `SomeResource` action method.</span></span> <span data-ttu-id="ddde3-251">@No__t-0:</span><span class="sxs-lookup"><span data-stu-id="ddde3-251">The `ShortCircuitingResourceFilter`:</span></span>

* <span data-ttu-id="ddde3-252">Nejprve se spustí, protože se jedná o filtr prostředku a `AddHeader` je filtr akcí.</span><span class="sxs-lookup"><span data-stu-id="ddde3-252">Runs first, because it's a Resource Filter and `AddHeader` is an Action Filter.</span></span>
* <span data-ttu-id="ddde3-253">Krátké okruhy zbývajícího kanálu.</span><span class="sxs-lookup"><span data-stu-id="ddde3-253">Short-circuits the rest of the pipeline.</span></span>

<span data-ttu-id="ddde3-254">Proto se filtr `AddHeader` nikdy nespustí pro akci `SomeResource`.</span><span class="sxs-lookup"><span data-stu-id="ddde3-254">Therefore the `AddHeader` filter never runs for the `SomeResource` action.</span></span> <span data-ttu-id="ddde3-255">Toto chování by bylo stejné, pokud byly oba filtry aplikovány na úrovni metody akce za předpokladu, `ShortCircuitingResourceFilter` běželo jako první.</span><span class="sxs-lookup"><span data-stu-id="ddde3-255">This behavior would be the same if both filters were applied at the action method level, provided the `ShortCircuitingResourceFilter` ran first.</span></span> <span data-ttu-id="ddde3-256">@No__t-0 se nejprve spustí z důvodu jeho typu filtru nebo explicitním použitím vlastnosti `Order`.</span><span class="sxs-lookup"><span data-stu-id="ddde3-256">The `ShortCircuitingResourceFilter` runs first because of its filter type, or by explicit use of `Order` property.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1,9)]

## <a name="dependency-injection"></a><span data-ttu-id="ddde3-257">Injektáž závislostí</span><span class="sxs-lookup"><span data-stu-id="ddde3-257">Dependency injection</span></span>

<span data-ttu-id="ddde3-258">Filtry lze přidat podle typu nebo podle instance.</span><span class="sxs-lookup"><span data-stu-id="ddde3-258">Filters can be added by type or by instance.</span></span> <span data-ttu-id="ddde3-259">Pokud je přidána instance, bude tato instance použita pro každý požadavek.</span><span class="sxs-lookup"><span data-stu-id="ddde3-259">If an instance is added, that instance is used for every request.</span></span> <span data-ttu-id="ddde3-260">Pokud je přidán typ, je aktivován typ.</span><span class="sxs-lookup"><span data-stu-id="ddde3-260">If a type is added, it's type-activated.</span></span> <span data-ttu-id="ddde3-261">Filtr aktivovaný typu znamená:</span><span class="sxs-lookup"><span data-stu-id="ddde3-261">A type-activated filter means:</span></span>

* <span data-ttu-id="ddde3-262">Instance se vytvoří pro každý požadavek.</span><span class="sxs-lookup"><span data-stu-id="ddde3-262">An instance is created for each request.</span></span>
* <span data-ttu-id="ddde3-263">Jakékoli závislosti konstruktoru jsou vyplněny pomocí [Injektáže závislosti](xref:fundamentals/dependency-injection) (di).</span><span class="sxs-lookup"><span data-stu-id="ddde3-263">Any constructor dependencies are populated by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span>

<span data-ttu-id="ddde3-264">Filtry, které jsou implementovány jako atributy a přidány přímo do tříd kontroleru nebo metody akcí, nemohou mít závislosti konstruktoru poskytované [vkládáním závislostí](xref:fundamentals/dependency-injection) (di).</span><span class="sxs-lookup"><span data-stu-id="ddde3-264">Filters that are implemented as attributes and added directly to controller classes or action methods cannot have constructor dependencies provided by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span> <span data-ttu-id="ddde3-265">Pomocí DI nelze poskytnout závislosti konstruktoru:</span><span class="sxs-lookup"><span data-stu-id="ddde3-265">Constructor dependencies cannot be provided by DI because:</span></span>

* <span data-ttu-id="ddde3-266">V atributech musí být zadány parametry konstruktoru, kde jsou aplikovány.</span><span class="sxs-lookup"><span data-stu-id="ddde3-266">Attributes must have their constructor parameters supplied where they're applied.</span></span> 
* <span data-ttu-id="ddde3-267">Toto je omezení způsobu fungování atributů.</span><span class="sxs-lookup"><span data-stu-id="ddde3-267">This is a limitation of how attributes work.</span></span>

<span data-ttu-id="ddde3-268">Následující filtry podporují závislosti konstruktoru poskytované od DI:</span><span class="sxs-lookup"><span data-stu-id="ddde3-268">The following filters support constructor dependencies provided from DI:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>
* <span data-ttu-id="ddde3-269"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implementováno u atributu.</span><span class="sxs-lookup"><span data-stu-id="ddde3-269"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implemented on the attribute.</span></span>

<span data-ttu-id="ddde3-270">Předchozí filtry lze použít pro metodu kontroleru nebo akce:</span><span class="sxs-lookup"><span data-stu-id="ddde3-270">The preceding filters can be applied to a controller or action method:</span></span>

<span data-ttu-id="ddde3-271">Protokolovací nástroje jsou k dispozici z DI.</span><span class="sxs-lookup"><span data-stu-id="ddde3-271">Loggers are available from DI.</span></span> <span data-ttu-id="ddde3-272">Ale nevytvářejte a používejte filtry čistě pro účely protokolování.</span><span class="sxs-lookup"><span data-stu-id="ddde3-272">However, avoid creating and using filters purely for logging purposes.</span></span> <span data-ttu-id="ddde3-273">[Integrované protokolování rozhraní](xref:fundamentals/logging/index) obvykle zajišťuje, co je potřeba pro protokolování.</span><span class="sxs-lookup"><span data-stu-id="ddde3-273">The [built-in framework logging](xref:fundamentals/logging/index) typically provides what's needed for logging.</span></span> <span data-ttu-id="ddde3-274">Protokolování přidáno do filtrů:</span><span class="sxs-lookup"><span data-stu-id="ddde3-274">Logging added to filters:</span></span>

* <span data-ttu-id="ddde3-275">Měl by se soustředit na obavy a chování obchodních domén, které jsou specifické pro filtr.</span><span class="sxs-lookup"><span data-stu-id="ddde3-275">Should focus on business domain concerns or behavior specific to the filter.</span></span>
* <span data-ttu-id="ddde3-276">Neměli **byste** protokolovat akce nebo jiné události rozhraní.</span><span class="sxs-lookup"><span data-stu-id="ddde3-276">Should **not** log actions or other framework events.</span></span> <span data-ttu-id="ddde3-277">Vestavěné filtry protokolují akce a události rozhraní.</span><span class="sxs-lookup"><span data-stu-id="ddde3-277">The built in filters log actions and framework events.</span></span>

### <a name="servicefilterattribute"></a><span data-ttu-id="ddde3-278">ServiceFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="ddde3-278">ServiceFilterAttribute</span></span>

<span data-ttu-id="ddde3-279">Typy implementace filtru služby jsou registrovány v `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="ddde3-279">Service filter implementation types are registered in `ConfigureServices`.</span></span> <span data-ttu-id="ddde3-280">@No__t-0 načte instanci filtru z DI.</span><span class="sxs-lookup"><span data-stu-id="ddde3-280">A <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> retrieves an instance of the filter from DI.</span></span>

<span data-ttu-id="ddde3-281">Následující kód ukazuje `AddHeaderResultServiceFilter`:</span><span class="sxs-lookup"><span data-stu-id="ddde3-281">The following code shows the `AddHeaderResultServiceFilter`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="ddde3-282">V následujícím kódu je `AddHeaderResultServiceFilter` přidáno do kontejneru DI:</span><span class="sxs-lookup"><span data-stu-id="ddde3-282">In the following code, `AddHeaderResultServiceFilter` is added to the DI container:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

<span data-ttu-id="ddde3-283">V následujícím kódu atribut `ServiceFilter` načte instanci filtru `AddHeaderResultServiceFilter` z DI:</span><span class="sxs-lookup"><span data-stu-id="ddde3-283">In the following code, the `ServiceFilter` attribute retrieves an instance of the `AddHeaderResultServiceFilter` filter from DI:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_ServiceFilter&highlight=1)]

<span data-ttu-id="ddde3-284">Při použití `ServiceFilterAttribute` se nastavuje [ServiceFilterAttribute. IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span><span class="sxs-lookup"><span data-stu-id="ddde3-284">When using `ServiceFilterAttribute`, setting [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span></span>

* <span data-ttu-id="ddde3-285">Poskytuje nápovědu, že se instance filtru *dá* znovu použít mimo rozsah požadavku, který byl vytvořen v rámci.</span><span class="sxs-lookup"><span data-stu-id="ddde3-285">Provides a hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="ddde3-286">Modul runtime ASP.NET Core nezaručuje:</span><span class="sxs-lookup"><span data-stu-id="ddde3-286">The ASP.NET Core runtime doesn't guarantee:</span></span>

  * <span data-ttu-id="ddde3-287">Vytvoří se jedna instance filtru.</span><span class="sxs-lookup"><span data-stu-id="ddde3-287">That a single instance of the filter will be created.</span></span>
  * <span data-ttu-id="ddde3-288">Filtr nebude znovu vyžádán z kontejneru DI v pozdějším bodě.</span><span class="sxs-lookup"><span data-stu-id="ddde3-288">The filter will not be re-requested from the DI container at some later point.</span></span>

* <span data-ttu-id="ddde3-289">Neměl by se používat s filtrem, který závisí na službách s jinou životností než singleton.</span><span class="sxs-lookup"><span data-stu-id="ddde3-289">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

 <span data-ttu-id="ddde3-290"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implementuje <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span><span class="sxs-lookup"><span data-stu-id="ddde3-290"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="ddde3-291">`IFilterFactory` zpřístupňuje metodu <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> pro vytvoření instance <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span><span class="sxs-lookup"><span data-stu-id="ddde3-291">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="ddde3-292">`CreateInstance` načte zadaný typ z DI.</span><span class="sxs-lookup"><span data-stu-id="ddde3-292">`CreateInstance` loads the specified type from DI.</span></span>

### <a name="typefilterattribute"></a><span data-ttu-id="ddde3-293">TypeFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="ddde3-293">TypeFilterAttribute</span></span>

<span data-ttu-id="ddde3-294"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> se podobá <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, ale jeho typ není vyřešen přímo z kontejneru DI.</span><span class="sxs-lookup"><span data-stu-id="ddde3-294"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> is similar to <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, but its type isn't resolved directly from the DI container.</span></span> <span data-ttu-id="ddde3-295">Vytvoří instanci typu pomocí <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="ddde3-295">It instantiates the type by using <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span></span>

<span data-ttu-id="ddde3-296">Protože typy `TypeFilterAttribute` nejsou vyřešeny přímo z kontejneru DI:</span><span class="sxs-lookup"><span data-stu-id="ddde3-296">Because `TypeFilterAttribute` types aren't resolved directly from the DI container:</span></span>

* <span data-ttu-id="ddde3-297">Typy, na které se odkazuje pomocí `TypeFilterAttribute`, není nutné registrovat v kontejneru DI.</span><span class="sxs-lookup"><span data-stu-id="ddde3-297">Types that are referenced using the `TypeFilterAttribute` don't need to be registered with the DI container.</span></span>  <span data-ttu-id="ddde3-298">Mají své závislosti splněné kontejnerem DI.</span><span class="sxs-lookup"><span data-stu-id="ddde3-298">They do have their dependencies fulfilled by the DI container.</span></span>
* <span data-ttu-id="ddde3-299">`TypeFilterAttribute` může volitelně přijmout argumenty konstruktoru pro daný typ.</span><span class="sxs-lookup"><span data-stu-id="ddde3-299">`TypeFilterAttribute` can optionally accept constructor arguments for the type.</span></span>

<span data-ttu-id="ddde3-300">Při použití `TypeFilterAttribute` se nastavuje [TypeFilterAttribute. IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span><span class="sxs-lookup"><span data-stu-id="ddde3-300">When using `TypeFilterAttribute`, setting [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span></span>
* <span data-ttu-id="ddde3-301">Poskytuje nápovědu, že se instance filtru *dá* znovu použít mimo rozsah požadavku, který byl vytvořen v rámci.</span><span class="sxs-lookup"><span data-stu-id="ddde3-301">Provides hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="ddde3-302">Modul runtime ASP.NET Core neposkytuje žádné záruky, že se vytvoří jediná instance filtru.</span><span class="sxs-lookup"><span data-stu-id="ddde3-302">The ASP.NET Core runtime provides no guarantees that a single instance of the filter will be created.</span></span>

* <span data-ttu-id="ddde3-303">Neměl by se používat s filtrem, který závisí na službách s jinou životností než singleton.</span><span class="sxs-lookup"><span data-stu-id="ddde3-303">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

<span data-ttu-id="ddde3-304">Následující příklad ukazuje, jak předat argumenty typu pomocí `TypeFilterAttribute`:</span><span class="sxs-lookup"><span data-stu-id="ddde3-304">The following example shows how to pass arguments to a type using `TypeFilterAttribute`:</span></span>

[!code-csharp[](../../mvc/controllers/filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_TypeFilter&highlight=1,2)]
[!code-csharp[](../../mvc/controllers/filters/sample/FiltersSample/Filters/LogConstantFilter.cs?name=snippet_TypeFilter_Implementation&highlight=6)]

<!-- 
https://localhost:5001/home/hi?name=joe
VS debug window shows 
FiltersSample.Filters.LogConstantFilter:Information: Method 'Hi' called
-->

## <a name="authorization-filters"></a><span data-ttu-id="ddde3-305">Filtry autorizace</span><span class="sxs-lookup"><span data-stu-id="ddde3-305">Authorization filters</span></span>

<span data-ttu-id="ddde3-306">Filtry autorizace:</span><span class="sxs-lookup"><span data-stu-id="ddde3-306">Authorization filters:</span></span>

* <span data-ttu-id="ddde3-307">Jsou první filtry spouštěny v kanálu filtru.</span><span class="sxs-lookup"><span data-stu-id="ddde3-307">Are the first filters run in the filter pipeline.</span></span>
* <span data-ttu-id="ddde3-308">Řízení přístupu k metodám akcí.</span><span class="sxs-lookup"><span data-stu-id="ddde3-308">Control access to action methods.</span></span>
* <span data-ttu-id="ddde3-309">Před metodou, ale ne po metodě.</span><span class="sxs-lookup"><span data-stu-id="ddde3-309">Have a before method, but no after method.</span></span>

<span data-ttu-id="ddde3-310">Vlastní autorizační filtry vyžadují vlastní autorizační rozhraní.</span><span class="sxs-lookup"><span data-stu-id="ddde3-310">Custom authorization filters require a custom authorization framework.</span></span> <span data-ttu-id="ddde3-311">Preferovat konfiguraci autorizačních zásad nebo psaní vlastních zásad autorizace při psaní vlastního filtru.</span><span class="sxs-lookup"><span data-stu-id="ddde3-311">Prefer configuring the authorization policies or writing a custom authorization policy over writing a custom filter.</span></span> <span data-ttu-id="ddde3-312">Vestavěný autorizační filtr:</span><span class="sxs-lookup"><span data-stu-id="ddde3-312">The built-in authorization filter:</span></span>

* <span data-ttu-id="ddde3-313">Volá autorizační systém.</span><span class="sxs-lookup"><span data-stu-id="ddde3-313">Calls the authorization system.</span></span>
* <span data-ttu-id="ddde3-314">Neautorizuje požadavky.</span><span class="sxs-lookup"><span data-stu-id="ddde3-314">Does not authorize requests.</span></span>

<span data-ttu-id="ddde3-315">Negenerovat **výjimky** v rámci autorizačních filtrů:</span><span class="sxs-lookup"><span data-stu-id="ddde3-315">Do **not** throw exceptions within authorization filters:</span></span>

* <span data-ttu-id="ddde3-316">Výjimka nebude zpracována.</span><span class="sxs-lookup"><span data-stu-id="ddde3-316">The exception will not be handled.</span></span>
* <span data-ttu-id="ddde3-317">Filtry výjimek nebudou zpracovávat výjimku.</span><span class="sxs-lookup"><span data-stu-id="ddde3-317">Exception filters will not handle the exception.</span></span>

<span data-ttu-id="ddde3-318">Zvažte vydání výzvy, pokud dojde k výjimce ve autorizačním filtru.</span><span class="sxs-lookup"><span data-stu-id="ddde3-318">Consider issuing a challenge when an exception occurs in an authorization filter.</span></span>

<span data-ttu-id="ddde3-319">Přečtěte si další informace o [autorizaci](xref:security/authorization/introduction).</span><span class="sxs-lookup"><span data-stu-id="ddde3-319">Learn more about [Authorization](xref:security/authorization/introduction).</span></span>

## <a name="resource-filters"></a><span data-ttu-id="ddde3-320">Filtry prostředků</span><span class="sxs-lookup"><span data-stu-id="ddde3-320">Resource filters</span></span>

<span data-ttu-id="ddde3-321">Filtry prostředků:</span><span class="sxs-lookup"><span data-stu-id="ddde3-321">Resource filters:</span></span>

* <span data-ttu-id="ddde3-322">Implementujte rozhraní <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> nebo <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter>.</span><span class="sxs-lookup"><span data-stu-id="ddde3-322">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> interface.</span></span>
* <span data-ttu-id="ddde3-323">Provádění zabalí většinu kanálu filtru.</span><span class="sxs-lookup"><span data-stu-id="ddde3-323">Execution wraps most of the filter pipeline.</span></span>
* <span data-ttu-id="ddde3-324">Před filtry prostředků se spouštějí jenom [filtry autorizace](#authorization-filters) .</span><span class="sxs-lookup"><span data-stu-id="ddde3-324">Only [Authorization filters](#authorization-filters) run before resource filters.</span></span>

<span data-ttu-id="ddde3-325">Filtry prostředků jsou užitečné pro krátkodobé okruhy v kanálu.</span><span class="sxs-lookup"><span data-stu-id="ddde3-325">Resource filters are useful to short-circuit most of the pipeline.</span></span> <span data-ttu-id="ddde3-326">Například filtr ukládání do mezipaměti může zabránit zbývajícímu kanálu v případě přístupů do mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="ddde3-326">For example, a caching filter can avoid the rest of the pipeline on a cache hit.</span></span>

<span data-ttu-id="ddde3-327">Příklady filtru prostředků:</span><span class="sxs-lookup"><span data-stu-id="ddde3-327">Resource filter examples:</span></span>

* <span data-ttu-id="ddde3-328">[Filtr prostředků se zkrácenými okruhy](#short-circuiting-resource-filter) zobrazenými dříve.</span><span class="sxs-lookup"><span data-stu-id="ddde3-328">[The short-circuiting resource filter](#short-circuiting-resource-filter) shown previously.</span></span>
* <span data-ttu-id="ddde3-329">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span><span class="sxs-lookup"><span data-stu-id="ddde3-329">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span></span>

  * <span data-ttu-id="ddde3-330">Zabraňuje vazbě modelu v přístupu k datům formuláře.</span><span class="sxs-lookup"><span data-stu-id="ddde3-330">Prevents model binding from accessing the form data.</span></span>
  * <span data-ttu-id="ddde3-331">Používá se pro nahrávání velkých souborů, aby se zabránilo čtení dat z formuláře do paměti.</span><span class="sxs-lookup"><span data-stu-id="ddde3-331">Used for large file uploads to prevent the form data from being read into memory.</span></span>

## <a name="action-filters"></a><span data-ttu-id="ddde3-332">Filtry akcí</span><span class="sxs-lookup"><span data-stu-id="ddde3-332">Action filters</span></span>

> [!IMPORTANT]
> <span data-ttu-id="ddde3-333">Filtry akcí **se** nevztahují na Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="ddde3-333">Action filters do **not** apply to Razor Pages.</span></span> <span data-ttu-id="ddde3-334">Razor Pages podporuje <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> a <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter>.</span><span class="sxs-lookup"><span data-stu-id="ddde3-334">Razor Pages supports <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span></span> <span data-ttu-id="ddde3-335">Další informace naleznete v tématu [metody Filter pro Razor Pages](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="ddde3-335">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

<span data-ttu-id="ddde3-336">Filtry akcí:</span><span class="sxs-lookup"><span data-stu-id="ddde3-336">Action filters:</span></span>

* <span data-ttu-id="ddde3-337">Implementujte rozhraní <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> nebo <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter>.</span><span class="sxs-lookup"><span data-stu-id="ddde3-337">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> interface.</span></span>
* <span data-ttu-id="ddde3-338">Jejich spuštění obklopuje provádění metod akcí.</span><span class="sxs-lookup"><span data-stu-id="ddde3-338">Their execution surrounds the execution of action methods.</span></span>

<span data-ttu-id="ddde3-339">Následující kód ukazuje filtr vzorové akce:</span><span class="sxs-lookup"><span data-stu-id="ddde3-339">The following code shows a sample action filter:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="ddde3-340">@No__t-0 poskytuje následující vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="ddde3-340">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> provides the following properties:</span></span>

* <span data-ttu-id="ddde3-341"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> – povolí čtení vstupů do metody akce.</span><span class="sxs-lookup"><span data-stu-id="ddde3-341"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> - enables the inputs to an action method be read.</span></span>
* <span data-ttu-id="ddde3-342"><xref:Microsoft.AspNetCore.Mvc.Controller> – povoluje manipulaci s instancí kontroleru.</span><span class="sxs-lookup"><span data-stu-id="ddde3-342"><xref:Microsoft.AspNetCore.Mvc.Controller> - enables manipulating the controller instance.</span></span>
* <span data-ttu-id="ddde3-343"><xref:System.Web.Mvc.ActionExecutingContext.Result>-Nastavuje se krátkodobé pookruhy `Result` metody akce a následné filtry akcí.</span><span class="sxs-lookup"><span data-stu-id="ddde3-343"><xref:System.Web.Mvc.ActionExecutingContext.Result> - setting `Result` short-circuits execution of the action method and subsequent action filters.</span></span>

<span data-ttu-id="ddde3-344">Vyvolání výjimky v metodě akce:</span><span class="sxs-lookup"><span data-stu-id="ddde3-344">Throwing an exception in an action method:</span></span>

* <span data-ttu-id="ddde3-345">Zabraňuje spuštění dalších filtrů.</span><span class="sxs-lookup"><span data-stu-id="ddde3-345">Prevents running of subsequent filters.</span></span>
* <span data-ttu-id="ddde3-346">Na rozdíl od nastavení `Result` se místo úspěšného výsledku považuje za selhání.</span><span class="sxs-lookup"><span data-stu-id="ddde3-346">Unlike setting `Result`, is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="ddde3-347">@No__t-0 poskytuje `Controller` a `Result` a následující vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="ddde3-347">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> provides `Controller` and `Result` plus the following properties:</span></span>

* <span data-ttu-id="ddde3-348"><xref:System.Web.Mvc.ActionExecutedContext.Canceled>-true, pokud provádění akce bylo zkráceno jiným filtrem.</span><span class="sxs-lookup"><span data-stu-id="ddde3-348"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> - True if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="ddde3-349"><xref:System.Web.Mvc.ActionExecutedContext.Exception>-nenulová, pokud akce nebo filtr dříve spuštěných akcí vyvolaly výjimku.</span><span class="sxs-lookup"><span data-stu-id="ddde3-349"><xref:System.Web.Mvc.ActionExecutedContext.Exception> - Non-null if the action or a previously run action filter threw an exception.</span></span> <span data-ttu-id="ddde3-350">Nastavení této vlastnosti na hodnotu null:</span><span class="sxs-lookup"><span data-stu-id="ddde3-350">Setting this property to null:</span></span>

  * <span data-ttu-id="ddde3-351">Efektivně zpracovává výjimku.</span><span class="sxs-lookup"><span data-stu-id="ddde3-351">Effectively handles the exception.</span></span>
  * <span data-ttu-id="ddde3-352">`Result` se spustí, jako kdyby byla vrácena z metody Action.</span><span class="sxs-lookup"><span data-stu-id="ddde3-352">`Result` is executed as if it was returned from the action method.</span></span>

<span data-ttu-id="ddde3-353">Pro `IAsyncActionFilter` volání <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span><span class="sxs-lookup"><span data-stu-id="ddde3-353">For an `IAsyncActionFilter`, a call to the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span></span>

* <span data-ttu-id="ddde3-354">Provede všechny následné filtry akcí a metodu Action.</span><span class="sxs-lookup"><span data-stu-id="ddde3-354">Executes any subsequent action filters and the action method.</span></span>
* <span data-ttu-id="ddde3-355">Vrátí `ActionExecutedContext`.</span><span class="sxs-lookup"><span data-stu-id="ddde3-355">Returns `ActionExecutedContext`.</span></span>

<span data-ttu-id="ddde3-356">Do krátkodobého okruhu, přiřaďte <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> do instance výsledku a Nevolejte `next` (`ActionExecutionDelegate`).</span><span class="sxs-lookup"><span data-stu-id="ddde3-356">To short-circuit, assign <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> to a result instance and don't call `next` (the `ActionExecutionDelegate`).</span></span>

<span data-ttu-id="ddde3-357">Rozhraní poskytuje abstraktní <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>, které lze roztřídit.</span><span class="sxs-lookup"><span data-stu-id="ddde3-357">The framework provides an abstract <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> that can be subclassed.</span></span>

<span data-ttu-id="ddde3-358">Filtr akcí `OnActionExecuting` lze použít k těmto akcím:</span><span class="sxs-lookup"><span data-stu-id="ddde3-358">The `OnActionExecuting` action filter can be used to:</span></span>

* <span data-ttu-id="ddde3-359">Ověří stav modelu.</span><span class="sxs-lookup"><span data-stu-id="ddde3-359">Validate model state.</span></span>
* <span data-ttu-id="ddde3-360">Pokud je stav neplatný, vrátí se chyba.</span><span class="sxs-lookup"><span data-stu-id="ddde3-360">Return an error if the state is invalid.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet)]

<span data-ttu-id="ddde3-361">Metoda `OnActionExecuted` se spouští po metodě Action:</span><span class="sxs-lookup"><span data-stu-id="ddde3-361">The `OnActionExecuted` method runs after the action method:</span></span>

* <span data-ttu-id="ddde3-362">A mohou zobrazit výsledky akce pomocí vlastnosti <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> a manipulovat s nimi.</span><span class="sxs-lookup"><span data-stu-id="ddde3-362">And can see and manipulate the results of the action through the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> property.</span></span>
* <span data-ttu-id="ddde3-363"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> je nastavena na hodnotu true, pokud bylo provedení akce zkráceno jiným filtrem.</span><span class="sxs-lookup"><span data-stu-id="ddde3-363"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> is set to true if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="ddde3-364"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> je nastavena na hodnotu, která není null, pokud akce nebo filtr následné akce vyvolaly výjimku.</span><span class="sxs-lookup"><span data-stu-id="ddde3-364"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> is set to a non-null value if the action or a subsequent action filter threw an exception.</span></span> <span data-ttu-id="ddde3-365">Nastavení `Exception` na hodnotu null:</span><span class="sxs-lookup"><span data-stu-id="ddde3-365">Setting `Exception` to null:</span></span>

  * <span data-ttu-id="ddde3-366">Efektivně zpracovává výjimku.</span><span class="sxs-lookup"><span data-stu-id="ddde3-366">Effectively handles an exception.</span></span>
  * <span data-ttu-id="ddde3-367">`ActionExecutedContext.Result` se spustí, jako kdyby byl normálně vrácen z metody Action.</span><span class="sxs-lookup"><span data-stu-id="ddde3-367">`ActionExecutedContext.Result` is executed as if it were returned normally from the action method.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet2&higlight=12-99)]

## <a name="exception-filters"></a><span data-ttu-id="ddde3-368">Filtry výjimek</span><span class="sxs-lookup"><span data-stu-id="ddde3-368">Exception filters</span></span>

<span data-ttu-id="ddde3-369">Filtry výjimek:</span><span class="sxs-lookup"><span data-stu-id="ddde3-369">Exception filters:</span></span>

* <span data-ttu-id="ddde3-370">Implementujte <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> nebo <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span><span class="sxs-lookup"><span data-stu-id="ddde3-370">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span></span> 
* <span data-ttu-id="ddde3-371">Dá se použít k implementaci běžných zásad zpracování chyb.</span><span class="sxs-lookup"><span data-stu-id="ddde3-371">Can be used to implement common error handling policies.</span></span>

<span data-ttu-id="ddde3-372">Následující ukázkový filtr výjimek používá vlastní zobrazení chyb pro zobrazení podrobností o výjimkách, ke kterým dochází při vývoji aplikace:</span><span class="sxs-lookup"><span data-stu-id="ddde3-372">The following sample exception filter uses a custom error view to display details about exceptions that occur when the app is in development:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/CustomExceptionFilterAttribute.cs?name=snippet_ExceptionFilter&highlight=16-19)]

<span data-ttu-id="ddde3-373">Filtry výjimek:</span><span class="sxs-lookup"><span data-stu-id="ddde3-373">Exception filters:</span></span>

* <span data-ttu-id="ddde3-374">Nemusíte mít události před a po.</span><span class="sxs-lookup"><span data-stu-id="ddde3-374">Don't have before and after events.</span></span>
* <span data-ttu-id="ddde3-375">Implementujte <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> nebo <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span><span class="sxs-lookup"><span data-stu-id="ddde3-375">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span></span>
* <span data-ttu-id="ddde3-376">Zpracování neošetřených výjimek, ke kterým dochází při vytváření stránky nebo kontroléru Razor, [vázání modelů](xref:mvc/models/model-binding), filtrů akcí nebo metod akcí.</span><span class="sxs-lookup"><span data-stu-id="ddde3-376">Handle unhandled exceptions that occur in Razor Page or controller creation, [model binding](xref:mvc/models/model-binding), action filters, or action methods.</span></span>
* <span data-ttu-id="ddde3-377">**Nezachycujte** výjimky, ke kterým dochází v filtrech prostředků, výsledných filtrech nebo v důsledku provádění výsledků MVC.</span><span class="sxs-lookup"><span data-stu-id="ddde3-377">Do **not** catch exceptions that occur in resource filters, result filters, or MVC result execution.</span></span>

<span data-ttu-id="ddde3-378">Chcete-li zpracovat výjimku, nastavte vlastnost <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> na hodnotu `true` nebo zapište odpověď.</span><span class="sxs-lookup"><span data-stu-id="ddde3-378">To handle an exception, set the <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> property to `true` or write a response.</span></span> <span data-ttu-id="ddde3-379">Tím se zastaví šíření výjimky.</span><span class="sxs-lookup"><span data-stu-id="ddde3-379">This stops propagation of the exception.</span></span> <span data-ttu-id="ddde3-380">Filtr výjimek nemůže vypnout výjimku na "úspěch".</span><span class="sxs-lookup"><span data-stu-id="ddde3-380">An exception filter can't turn an exception into a "success".</span></span> <span data-ttu-id="ddde3-381">To může provést pouze filtr akcí.</span><span class="sxs-lookup"><span data-stu-id="ddde3-381">Only an action filter can do that.</span></span>

<span data-ttu-id="ddde3-382">Filtry výjimek:</span><span class="sxs-lookup"><span data-stu-id="ddde3-382">Exception filters:</span></span>

* <span data-ttu-id="ddde3-383">Jsou vhodné pro vytváření přesahů výjimek, ke kterým dochází v rámci akcí.</span><span class="sxs-lookup"><span data-stu-id="ddde3-383">Are good for trapping exceptions that occur within actions.</span></span>
* <span data-ttu-id="ddde3-384">Nejsou tak flexibilní jako middleware při zpracování chyb.</span><span class="sxs-lookup"><span data-stu-id="ddde3-384">Are not as flexible as error handling middleware.</span></span>

<span data-ttu-id="ddde3-385">Preferovat middleware pro zpracování výjimek.</span><span class="sxs-lookup"><span data-stu-id="ddde3-385">Prefer middleware for exception handling.</span></span> <span data-ttu-id="ddde3-386">Filtry výjimek použijte pouze v případě, že se zpracování chyb *liší* v závislosti na tom, která metoda Action je volána.</span><span class="sxs-lookup"><span data-stu-id="ddde3-386">Use exception filters only where error handling *differs* based on which action method is called.</span></span> <span data-ttu-id="ddde3-387">Aplikace může například mít metody akcí pro koncové body rozhraní API i pro zobrazení/HTML.</span><span class="sxs-lookup"><span data-stu-id="ddde3-387">For example, an app might have action methods for both API endpoints and for views/HTML.</span></span> <span data-ttu-id="ddde3-388">Koncové body rozhraní API mohou vracet informace o chybě jako JSON, zatímco akce na základě zobrazení by mohly vracet chybovou stránku jako HTML.</span><span class="sxs-lookup"><span data-stu-id="ddde3-388">The API endpoints could return error information as JSON, while the view-based actions could return an error page as HTML.</span></span>

## <a name="result-filters"></a><span data-ttu-id="ddde3-389">Filtry výsledků</span><span class="sxs-lookup"><span data-stu-id="ddde3-389">Result filters</span></span>

<span data-ttu-id="ddde3-390">Filtry výsledků:</span><span class="sxs-lookup"><span data-stu-id="ddde3-390">Result filters:</span></span>

* <span data-ttu-id="ddde3-391">Implementace rozhraní:</span><span class="sxs-lookup"><span data-stu-id="ddde3-391">Implement an interface:</span></span>
  * <span data-ttu-id="ddde3-392"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> Nebo <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="ddde3-392"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
  * <span data-ttu-id="ddde3-393"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> Nebo <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span><span class="sxs-lookup"><span data-stu-id="ddde3-393"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span></span>
* <span data-ttu-id="ddde3-394">Jejich spuštění obklopuje provádění výsledků akcí.</span><span class="sxs-lookup"><span data-stu-id="ddde3-394">Their execution surrounds the execution of action results.</span></span>

### <a name="iresultfilter-and-iasyncresultfilter"></a><span data-ttu-id="ddde3-395">IResultFilter a IAsyncResultFilter</span><span class="sxs-lookup"><span data-stu-id="ddde3-395">IResultFilter and IAsyncResultFilter</span></span>

<span data-ttu-id="ddde3-396">Následující kód ukazuje filtr výsledků, který přidá hlavičku protokolu HTTP:</span><span class="sxs-lookup"><span data-stu-id="ddde3-396">The following code shows a result filter that adds an HTTP header:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="ddde3-397">Typ výsledku, který se má provést, závisí na akci.</span><span class="sxs-lookup"><span data-stu-id="ddde3-397">The kind of result being executed depends on the action.</span></span> <span data-ttu-id="ddde3-398">Akce, která vrací zobrazení, by zahrnovala všechny zpracování Razor jako součást <xref:Microsoft.AspNetCore.Mvc.ViewResult> prováděných.</span><span class="sxs-lookup"><span data-stu-id="ddde3-398">An action returning a view would include all razor processing as part of the <xref:Microsoft.AspNetCore.Mvc.ViewResult> being executed.</span></span> <span data-ttu-id="ddde3-399">Metoda rozhraní API může provést určitou serializaci v rámci provádění výsledku.</span><span class="sxs-lookup"><span data-stu-id="ddde3-399">An API method might perform some serialization as part of the execution of the result.</span></span> <span data-ttu-id="ddde3-400">Přečtěte si další informace o [výsledcích akcí](xref:mvc/controllers/actions).</span><span class="sxs-lookup"><span data-stu-id="ddde3-400">Learn more about [action results](xref:mvc/controllers/actions).</span></span>

<span data-ttu-id="ddde3-401">Filtry výsledků se spustí pouze v případě, že akce nebo filtr akcí vytvoří výsledek akce.</span><span class="sxs-lookup"><span data-stu-id="ddde3-401">Result filters are only executed when an action or action filter produces an action result.</span></span> <span data-ttu-id="ddde3-402">Filtry výsledků nejsou provedeny v těchto případech:</span><span class="sxs-lookup"><span data-stu-id="ddde3-402">Result filters are not executed when:</span></span>

* <span data-ttu-id="ddde3-403">Filtr autorizace nebo filtr prostředků pro krátké okruhy kanálu.</span><span class="sxs-lookup"><span data-stu-id="ddde3-403">An authorization filter or resource filter short-circuits the pipeline.</span></span>
* <span data-ttu-id="ddde3-404">Filtr výjimek zpracovává výjimku tím, že vyprodukuje výsledek akce.</span><span class="sxs-lookup"><span data-stu-id="ddde3-404">An exception filter handles an exception by producing an action result.</span></span>

<span data-ttu-id="ddde3-405">Metoda <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> umožňuje krátkodobé vykonání výsledku akce a následných filtrů výsledků nastavením <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> na `true`.</span><span class="sxs-lookup"><span data-stu-id="ddde3-405">The <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> method can short-circuit execution of the action result and subsequent result filters by setting <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> to `true`.</span></span> <span data-ttu-id="ddde3-406">Zapište do objektu Response v případě krátkého okruhu, abyste se vyhnuli vygenerování prázdné odpovědi.</span><span class="sxs-lookup"><span data-stu-id="ddde3-406">Write to the response object when short-circuiting to avoid generating an empty response.</span></span> <span data-ttu-id="ddde3-407">Vyvolání výjimky v `IResultFilter.OnResultExecuting` bude:</span><span class="sxs-lookup"><span data-stu-id="ddde3-407">Throwing an exception in `IResultFilter.OnResultExecuting` will:</span></span>

* <span data-ttu-id="ddde3-408">Zabraňte provádění výsledků akce a dalších filtrů.</span><span class="sxs-lookup"><span data-stu-id="ddde3-408">Prevent execution of the action result and subsequent filters.</span></span>
* <span data-ttu-id="ddde3-409">Být považována za selhání namísto úspěšného výsledku.</span><span class="sxs-lookup"><span data-stu-id="ddde3-409">Be treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="ddde3-410">Při spuštění metody <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName>:</span><span class="sxs-lookup"><span data-stu-id="ddde3-410">When the <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> method runs:</span></span>

* <span data-ttu-id="ddde3-411">Odpověď byla pravděpodobně odeslána klientovi a nelze ji změnit.</span><span class="sxs-lookup"><span data-stu-id="ddde3-411">The response has likely been sent to the client and cannot be changed.</span></span>
* <span data-ttu-id="ddde3-412">Pokud byla vyvolána výjimka, tělo odpovědi nebude odesláno.</span><span class="sxs-lookup"><span data-stu-id="ddde3-412">If an exception was thrown, the response body is not sent.</span></span>

<!-- Review preceding "If an exception was thrown: Original 
When the OnResultExecuted method runs, the response has likely been sent to the client and cannot be changed further (unless an exception was thrown).

SHould that be , 
If an exception was thrown **IN THE RESULT FILTER**, the response body is not sent.

 -->

<span data-ttu-id="ddde3-413">`ResultExecutedContext.Canceled` je nastavená na `true`, pokud je spuštění výsledku akce od sebe krátkým jiným filtrem.</span><span class="sxs-lookup"><span data-stu-id="ddde3-413">`ResultExecutedContext.Canceled` is set to `true` if the action result execution was short-circuited by another filter.</span></span>

<span data-ttu-id="ddde3-414">`ResultExecutedContext.Exception` je nastavena na hodnotu, která není null, pokud výsledek akce nebo následný filtr výsledků vyvolal výjimku.</span><span class="sxs-lookup"><span data-stu-id="ddde3-414">`ResultExecutedContext.Exception` is set to a non-null value if the action result or a subsequent result filter threw an exception.</span></span> <span data-ttu-id="ddde3-415">Nastavení `Exception` na hodnotu null efektivně zpracovává výjimku a zabraňuje výjimce v jejím opětovném vyvolání pomocí ASP.NET Core později v kanálu.</span><span class="sxs-lookup"><span data-stu-id="ddde3-415">Setting `Exception` to null effectively handles an exception and prevents the exception from being rethrown by ASP.NET Core later in the pipeline.</span></span> <span data-ttu-id="ddde3-416">Neexistuje žádný spolehlivý způsob, jak zapisovat data do odpovědi při zpracování výjimky ve filtru výsledků.</span><span class="sxs-lookup"><span data-stu-id="ddde3-416">There is no reliable way to write data to a response when handling an exception in a result filter.</span></span> <span data-ttu-id="ddde3-417">Pokud byly hlavičky vyprázdněny do klienta, pokud výsledek akce vyvolá výjimku, neexistuje žádný spolehlivý mechanismus pro odeslání kódu chyby.</span><span class="sxs-lookup"><span data-stu-id="ddde3-417">If the headers have been flushed to the client when an action result throws an exception, there's no reliable mechanism to send a failure code.</span></span>

<span data-ttu-id="ddde3-418">U <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter> volání `await next` na <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> provede všechny následné filtry výsledků a výsledek akce.</span><span class="sxs-lookup"><span data-stu-id="ddde3-418">For an <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, a call to `await next` on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> executes any subsequent result filters and the action result.</span></span> <span data-ttu-id="ddde3-419">Do krátkodobého okruhu nastavte [ResultExecutingContext. Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) na `true` a nevolejte `ResultExecutionDelegate`:</span><span class="sxs-lookup"><span data-stu-id="ddde3-419">To short-circuit, set [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) to `true` and don't call the `ResultExecutionDelegate`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MyAsyncResponseFilter.cs?name=snippet)]

<span data-ttu-id="ddde3-420">Rozhraní poskytuje abstraktní `ResultFilterAttribute`, které lze roztřídit.</span><span class="sxs-lookup"><span data-stu-id="ddde3-420">The framework provides an abstract `ResultFilterAttribute` that can be subclassed.</span></span> <span data-ttu-id="ddde3-421">Dříve uvedená třída [AddHeaderAttribute](#add-header-attribute) je příkladem atributu filtru výsledků.</span><span class="sxs-lookup"><span data-stu-id="ddde3-421">The [AddHeaderAttribute](#add-header-attribute) class shown previously is an example of a result filter attribute.</span></span>

### <a name="ialwaysrunresultfilter-and-iasyncalwaysrunresultfilter"></a><span data-ttu-id="ddde3-422">IAlwaysRunResultFilter a IAsyncAlwaysRunResultFilter</span><span class="sxs-lookup"><span data-stu-id="ddde3-422">IAlwaysRunResultFilter and IAsyncAlwaysRunResultFilter</span></span>

<span data-ttu-id="ddde3-423">Rozhraní <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> a <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> deklaruje implementaci <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter>, která je spuštěna pro všechny výsledky akce.</span><span class="sxs-lookup"><span data-stu-id="ddde3-423">The <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> interfaces declare an <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> implementation that runs for all action results.</span></span> <span data-ttu-id="ddde3-424">To zahrnuje výsledky akcí, které vytvořil:</span><span class="sxs-lookup"><span data-stu-id="ddde3-424">This includes action results produced by:</span></span>

* <span data-ttu-id="ddde3-425">Filtry autorizace a filtry prostředků, které mají krátký okruh.</span><span class="sxs-lookup"><span data-stu-id="ddde3-425">Authorization filters and resource filters that short-circuit.</span></span>
* <span data-ttu-id="ddde3-426">Filtry výjimek.</span><span class="sxs-lookup"><span data-stu-id="ddde3-426">Exception filters.</span></span>

<span data-ttu-id="ddde3-427">Například následující filtr vždy spustí a nastaví výsledek akce (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) s *422 nezpracovaným* kódem stavu entity, když se domlouvání obsahu nezdaří:</span><span class="sxs-lookup"><span data-stu-id="ddde3-427">For example, the following filter always runs and sets an action result (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) with a *422 Unprocessable Entity* status code when content negotiation fails:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/UnprocessableResultFilter.cs?name=snippet)]

### <a name="ifilterfactory"></a><span data-ttu-id="ddde3-428">IFilterFactory</span><span class="sxs-lookup"><span data-stu-id="ddde3-428">IFilterFactory</span></span>

<span data-ttu-id="ddde3-429"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implementuje <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span><span class="sxs-lookup"><span data-stu-id="ddde3-429"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span></span> <span data-ttu-id="ddde3-430">Proto může být instance `IFilterFactory` použita jako instance `IFilterMetadata` kdekoli v kanálu filtru.</span><span class="sxs-lookup"><span data-stu-id="ddde3-430">Therefore, an `IFilterFactory` instance can be used as an `IFilterMetadata` instance anywhere in the filter pipeline.</span></span> <span data-ttu-id="ddde3-431">Když se modul runtime připraví k vyvolání filtru, pokusí se ho přetypovat na `IFilterFactory`.</span><span class="sxs-lookup"><span data-stu-id="ddde3-431">When the runtime prepares to invoke the filter, it attempts to cast it to an `IFilterFactory`.</span></span> <span data-ttu-id="ddde3-432">Pokud je toto přetypování úspěšné, je volána metoda <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> k vytvoření instance `IFilterMetadata`, která je vyvolána.</span><span class="sxs-lookup"><span data-stu-id="ddde3-432">If that cast succeeds, the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method is called to create the `IFilterMetadata` instance that is invoked.</span></span> <span data-ttu-id="ddde3-433">To poskytuje flexibilní návrh, protože přesný kanál filtru není nutné nastavit explicitně při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="ddde3-433">This provides a flexible design, since the precise filter pipeline doesn't need to be set explicitly when the app starts.</span></span>

<span data-ttu-id="ddde3-434">`IFilterFactory` se dá implementovat pomocí implementace vlastního atributu jako jiný přístup k vytváření filtrů:</span><span class="sxs-lookup"><span data-stu-id="ddde3-434">`IFilterFactory` can be implemented using custom attribute implementations as another approach to creating filters:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/AddHeaderWithFactoryAttribute.cs?name=snippet_IFilterFactory&highlight=1,4,5,6,7)]

<span data-ttu-id="ddde3-435">Předchozí kód lze otestovat spuštěním [ukázky stahování](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample):</span><span class="sxs-lookup"><span data-stu-id="ddde3-435">The preceding code can be tested by running the [download sample](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample):</span></span>

* <span data-ttu-id="ddde3-436">Vyvolejte vývojářské nástroje F12.</span><span class="sxs-lookup"><span data-stu-id="ddde3-436">Invoke the F12 developer tools.</span></span>
* <span data-ttu-id="ddde3-437">Přejděte na `https://localhost:5001/Sample/HeaderWithFactory`</span><span class="sxs-lookup"><span data-stu-id="ddde3-437">Navigate to `https://localhost:5001/Sample/HeaderWithFactory`</span></span>

<span data-ttu-id="ddde3-438">Vývojářské nástroje F12 zobrazují následující hlavičky odpovědí přidané ukázkovým kódem:</span><span class="sxs-lookup"><span data-stu-id="ddde3-438">The F12 developer tools display the following response headers added by the sample code:</span></span>

* <span data-ttu-id="ddde3-439">**Autor:** `Joe Smith`</span><span class="sxs-lookup"><span data-stu-id="ddde3-439">**author:** `Joe Smith`</span></span>
* <span data-ttu-id="ddde3-440">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span><span class="sxs-lookup"><span data-stu-id="ddde3-440">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span></span>
* <span data-ttu-id="ddde3-441">**interní:** `My header`</span><span class="sxs-lookup"><span data-stu-id="ddde3-441">**internal:** `My header`</span></span>

<span data-ttu-id="ddde3-442">Předchozí kód vytvoří hlavičku odpovědi **internal:** `My header`.</span><span class="sxs-lookup"><span data-stu-id="ddde3-442">The preceding code creates the **internal:** `My header` response header.</span></span>

### <a name="ifilterfactory-implemented-on-an-attribute"></a><span data-ttu-id="ddde3-443">IFilterFactory implementované u atributu</span><span class="sxs-lookup"><span data-stu-id="ddde3-443">IFilterFactory implemented on an attribute</span></span>

<!-- Review 
This section needs to be rewritten.
What's a non-named attribute?
-->

<span data-ttu-id="ddde3-444">Filtry, které implementují `IFilterFactory`, jsou užitečné pro filtry, které:</span><span class="sxs-lookup"><span data-stu-id="ddde3-444">Filters that implement `IFilterFactory` are useful for filters that:</span></span>

* <span data-ttu-id="ddde3-445">Nevyžadovat předávání parametrů</span><span class="sxs-lookup"><span data-stu-id="ddde3-445">Don't require passing parameters.</span></span>
* <span data-ttu-id="ddde3-446">Musí mít závislosti konstruktoru, které musí vyplnit DI.</span><span class="sxs-lookup"><span data-stu-id="ddde3-446">Have constructor dependencies that need to be filled by DI.</span></span>

<span data-ttu-id="ddde3-447"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implementuje <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span><span class="sxs-lookup"><span data-stu-id="ddde3-447"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="ddde3-448">`IFilterFactory` zpřístupňuje metodu <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> pro vytvoření instance <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span><span class="sxs-lookup"><span data-stu-id="ddde3-448">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="ddde3-449">`CreateInstance` načte zadaný typ z kontejneru Services (DI).</span><span class="sxs-lookup"><span data-stu-id="ddde3-449">`CreateInstance` loads the specified type from the services container (DI).</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/SampleActionFilterAttribute.cs?name=snippet_TypeFilterAttribute&highlight=1,3,7)]

<span data-ttu-id="ddde3-450">Následující kód ukazuje tři přístupy k použití `[SampleActionFilter]`:</span><span class="sxs-lookup"><span data-stu-id="ddde3-450">The following code shows three approaches to applying the `[SampleActionFilter]`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet&highlight=1)]

<span data-ttu-id="ddde3-451">V předchozím kódu je upravení metoda s `[SampleActionFilter]` upřednostňovaným přístupem k použití `SampleActionFilter`.</span><span class="sxs-lookup"><span data-stu-id="ddde3-451">In the preceding code, decorating the method with `[SampleActionFilter]` is the preferred approach to applying the `SampleActionFilter`.</span></span>

## <a name="using-middleware-in-the-filter-pipeline"></a><span data-ttu-id="ddde3-452">Použití middlewaru v kanálu filtru</span><span class="sxs-lookup"><span data-stu-id="ddde3-452">Using middleware in the filter pipeline</span></span>

<span data-ttu-id="ddde3-453">Filtry prostředků fungují jako [middleware](xref:fundamentals/middleware/index) v tom, že obklopují provádění všech položek, které jsou později v kanálu.</span><span class="sxs-lookup"><span data-stu-id="ddde3-453">Resource filters work like [middleware](xref:fundamentals/middleware/index) in that they surround the execution of everything that comes later in the pipeline.</span></span> <span data-ttu-id="ddde3-454">Filtry se ale liší od middlewaru v tom, že jsou součástí modulu ASP.NET Core Runtime, což znamená, že mají přístup k ASP.NET Core kontextu a konstrukcí.</span><span class="sxs-lookup"><span data-stu-id="ddde3-454">But filters differ from middleware in that they're part of the ASP.NET Core runtime, which means that they have access to ASP.NET Core context and constructs.</span></span>

<span data-ttu-id="ddde3-455">Chcete-li použít middleware jako filtr, vytvořte typ s metodou `Configure`, která určuje middleware, který se má vložit do kanálu filtru.</span><span class="sxs-lookup"><span data-stu-id="ddde3-455">To use middleware as a filter, create a type with a `Configure` method that specifies the middleware to inject into the filter pipeline.</span></span> <span data-ttu-id="ddde3-456">Následující příklad používá middleware Localization k vytvoření aktuální jazykové verze pro požadavek:</span><span class="sxs-lookup"><span data-stu-id="ddde3-456">The following example uses the localization middleware to establish the current culture for a request:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LocalizationPipeline.cs?name=snippet_MiddlewareFilter&highlight=3,21)]

<span data-ttu-id="ddde3-457">Pro spuštění middlewaru použijte <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute>:</span><span class="sxs-lookup"><span data-stu-id="ddde3-457">Use the <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> to run the middleware:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_MiddlewareFilter&highlight=2)]

<span data-ttu-id="ddde3-458">Filtry middlewaru jsou spouštěny ve stejné fázi kanálu filtru jako filtry prostředků, před vazbami modelů a po zbytek kanálu.</span><span class="sxs-lookup"><span data-stu-id="ddde3-458">Middleware filters run at the same stage of the filter pipeline as Resource filters, before model binding and after the rest of the pipeline.</span></span>

## <a name="next-actions"></a><span data-ttu-id="ddde3-459">Další akce</span><span class="sxs-lookup"><span data-stu-id="ddde3-459">Next actions</span></span>

* <span data-ttu-id="ddde3-460">Viz [metody filtru pro Razor Pages](xref:razor-pages/filter)</span><span class="sxs-lookup"><span data-stu-id="ddde3-460">See [Filter methods for Razor Pages](xref:razor-pages/filter)</span></span>
* <span data-ttu-id="ddde3-461">Pro experimentování s filtry, [stažení, otestování a úpravy ukázky GitHubu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample).</span><span class="sxs-lookup"><span data-stu-id="ddde3-461">To experiment with filters, [download, test, and modify the GitHub sample](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample).</span></span>
