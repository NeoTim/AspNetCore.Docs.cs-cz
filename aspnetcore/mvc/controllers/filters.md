---
title: Filtry v ASP.NET Core
author: Rick-Anderson
description: Přečtěte si, jak filtry fungují a jak je používat v ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 1/1/2020
uid: mvc/controllers/filters
ms.openlocfilehash: 759c150e7f35f3f6a52947edc5ef41448dc227fe
ms.sourcegitcommit: 7dfe6cc8408ac6a4549c29ca57b0c67ec4baa8de
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2020
ms.locfileid: "75828968"
---
# <a name="filters-in-aspnet-core"></a><span data-ttu-id="c774c-103">Filtry v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c774c-103">Filters in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c774c-104">[Kirka Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Dykstra](https://github.com/tdykstra/)a [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="c774c-104">By [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="c774c-105">*Filtry* v ASP.NET Core umožňují spuštění kódu před nebo po určitých fázích v kanálu zpracování požadavků.</span><span class="sxs-lookup"><span data-stu-id="c774c-105">*Filters* in ASP.NET Core allow code to be run before or after specific stages in the request processing pipeline.</span></span>

<span data-ttu-id="c774c-106">Předdefinované filtry zpracovávají úlohy, jako například:</span><span class="sxs-lookup"><span data-stu-id="c774c-106">Built-in filters handle tasks such as:</span></span>

* <span data-ttu-id="c774c-107">Autorizace (zabránění přístupu k prostředkům, ke kterým uživatel nemá oprávnění).</span><span class="sxs-lookup"><span data-stu-id="c774c-107">Authorization (preventing access to resources a user isn't authorized for).</span></span>
* <span data-ttu-id="c774c-108">Ukládání odpovědí do mezipaměti (při krátkém okruhu kanálu požadavků, který vrátí odpověď uloženou v mezipaměti).</span><span class="sxs-lookup"><span data-stu-id="c774c-108">Response caching (short-circuiting the request pipeline to return a cached response).</span></span>

<span data-ttu-id="c774c-109">Vlastní filtry je možné vytvořit pro zpracování vzájemně se týkajících se otázek.</span><span class="sxs-lookup"><span data-stu-id="c774c-109">Custom filters can be created to handle cross-cutting concerns.</span></span> <span data-ttu-id="c774c-110">Mezi obavy mezi průřezy patří zpracování chyb, ukládání do mezipaměti, konfigurace, autorizace a protokolování.</span><span class="sxs-lookup"><span data-stu-id="c774c-110">Examples of cross-cutting concerns include error handling, caching, configuration, authorization, and logging.</span></span>  <span data-ttu-id="c774c-111">Filtry zabraňují duplikování kódu.</span><span class="sxs-lookup"><span data-stu-id="c774c-111">Filters avoid duplicating code.</span></span> <span data-ttu-id="c774c-112">Například filtr výjimek zpracování chyb může konsolidovat zpracování chyb.</span><span class="sxs-lookup"><span data-stu-id="c774c-112">For example, an error handling exception filter could consolidate error handling.</span></span>

<span data-ttu-id="c774c-113">Tento dokument se týká Razor Pages, řadičů rozhraní API a řadičů se zobrazeními.</span><span class="sxs-lookup"><span data-stu-id="c774c-113">This document applies to Razor Pages, API controllers, and controllers with views.</span></span>

<span data-ttu-id="c774c-114">[Zobrazit nebo Stáhnout ukázku](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample) ([Jak stáhnout](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="c774c-114">[View or download sample](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="how-filters-work"></a><span data-ttu-id="c774c-115">Jak fungují filtry</span><span class="sxs-lookup"><span data-stu-id="c774c-115">How filters work</span></span>

<span data-ttu-id="c774c-116">Filtry se spouštějí v *kanálu vyvolání akce ASP.NET Core*, někdy označované jako *kanál filtru*.</span><span class="sxs-lookup"><span data-stu-id="c774c-116">Filters run within the *ASP.NET Core action invocation pipeline*, sometimes referred to as the *filter pipeline*.</span></span>  <span data-ttu-id="c774c-117">Kanál filtru se spustí po ASP.NET Core vybere akci, která se má provést.</span><span class="sxs-lookup"><span data-stu-id="c774c-117">The filter pipeline runs after ASP.NET Core selects the action to execute.</span></span>

![Požadavek se zpracovává prostřednictvím jiného middlewaru, middleware směrování, výběru akcí a kanálu vyvolání akce.](filters/_static/filter-pipeline-1.png)

### <a name="filter-types"></a><span data-ttu-id="c774c-120">Typy filtrů</span><span class="sxs-lookup"><span data-stu-id="c774c-120">Filter types</span></span>

<span data-ttu-id="c774c-121">Každý typ filtru se spustí v jiné fázi kanálu filtru:</span><span class="sxs-lookup"><span data-stu-id="c774c-121">Each filter type is executed at a different stage in the filter pipeline:</span></span>

* <span data-ttu-id="c774c-122">[Filtry autorizace](#authorization-filters) se spouštějí jako první a používají se k určení, jestli je uživatel autorizovaný pro požadavek.</span><span class="sxs-lookup"><span data-stu-id="c774c-122">[Authorization filters](#authorization-filters) run first and are used to determine whether the user is authorized for the request.</span></span> <span data-ttu-id="c774c-123">Filtry autorizace pro krátkodobé vynechání kanálu, pokud žádost není autorizována.</span><span class="sxs-lookup"><span data-stu-id="c774c-123">Authorization filters short-circuit the pipeline if the request is not authorized.</span></span>

* <span data-ttu-id="c774c-124">[Filtry prostředků](#resource-filters):</span><span class="sxs-lookup"><span data-stu-id="c774c-124">[Resource filters](#resource-filters):</span></span>

  * <span data-ttu-id="c774c-125">Spusťte po autorizaci.</span><span class="sxs-lookup"><span data-stu-id="c774c-125">Run after authorization.</span></span>  
  * <span data-ttu-id="c774c-126"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> spustí kód před ostatními kanály filtru.</span><span class="sxs-lookup"><span data-stu-id="c774c-126"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> runs code before the rest of the filter pipeline.</span></span> <span data-ttu-id="c774c-127">Například `OnResourceExecuting` spouští kód před vytvořením vazby modelu.</span><span class="sxs-lookup"><span data-stu-id="c774c-127">For example, `OnResourceExecuting` runs code before model binding.</span></span>
  * <span data-ttu-id="c774c-128"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> spustí kód po dokončení zbývající části kanálu.</span><span class="sxs-lookup"><span data-stu-id="c774c-128"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> runs code after the rest of the pipeline has completed.</span></span>

* <span data-ttu-id="c774c-129">[Filtry akcí](#action-filters):</span><span class="sxs-lookup"><span data-stu-id="c774c-129">[Action filters](#action-filters):</span></span>

  * <span data-ttu-id="c774c-130">Spustit kód bezprostředně před a po volání metody Action.</span><span class="sxs-lookup"><span data-stu-id="c774c-130">Run code immediately before and after an action method is called.</span></span>
  * <span data-ttu-id="c774c-131">Může změnit argumenty předané do akce.</span><span class="sxs-lookup"><span data-stu-id="c774c-131">Can change the arguments passed into an action.</span></span>
  * <span data-ttu-id="c774c-132">Může změnit výsledek vrácený z akce.</span><span class="sxs-lookup"><span data-stu-id="c774c-132">Can change the result returned from the action.</span></span>
  * <span data-ttu-id="c774c-133">Nejsou **podporovány v** Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="c774c-133">Are **not** supported in Razor Pages.</span></span>

* <span data-ttu-id="c774c-134">[Filtry výjimek](#exception-filters) aplikují globální zásady na neošetřené výjimky, ke kterým došlo před zápisem textu odpovědi do.</span><span class="sxs-lookup"><span data-stu-id="c774c-134">[Exception filters](#exception-filters) apply global policies to unhandled exceptions that occur before the response body has been written to.</span></span>

* <span data-ttu-id="c774c-135">[Filtry výsledků](#result-filters) spouštějí kód bezprostředně před a po provedení výsledků akce.</span><span class="sxs-lookup"><span data-stu-id="c774c-135">[Result filters](#result-filters) run code immediately before and after the execution of action results.</span></span> <span data-ttu-id="c774c-136">Spustí se pouze v případě, že metoda akce byla úspěšně provedena.</span><span class="sxs-lookup"><span data-stu-id="c774c-136">They run only when the action method has executed successfully.</span></span> <span data-ttu-id="c774c-137">Jsou užitečné pro logiku, která musí obklopit zobrazení nebo formátovací modul.</span><span class="sxs-lookup"><span data-stu-id="c774c-137">They are useful for logic that must surround view or formatter execution.</span></span>

<span data-ttu-id="c774c-138">Následující diagram znázorňuje způsob interakce typů filtrů v kanálu filtru.</span><span class="sxs-lookup"><span data-stu-id="c774c-138">The following diagram shows how filter types interact in the filter pipeline.</span></span>

![Požadavek se zpracovává pomocí autorizačních filtrů, filtrů prostředků, vazeb modelů, filtrů akcí, provádění akcí a konverze výsledků akcí, filtrů výjimek, výsledných filtrů a provádění výsledků.](filters/_static/filter-pipeline-2.png)

## <a name="implementation"></a><span data-ttu-id="c774c-141">Implementace</span><span class="sxs-lookup"><span data-stu-id="c774c-141">Implementation</span></span>

<span data-ttu-id="c774c-142">Filtry podporují synchronní i asynchronní implementace prostřednictvím různých definic rozhraní.</span><span class="sxs-lookup"><span data-stu-id="c774c-142">Filters support both synchronous and asynchronous implementations through different interface definitions.</span></span>

<span data-ttu-id="c774c-143">Synchronní filtry spouštějí kód před a po fázi zřetězení.</span><span class="sxs-lookup"><span data-stu-id="c774c-143">Synchronous filters run code before and after their pipeline stage.</span></span> <span data-ttu-id="c774c-144">Například <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*> je volána před voláním metody Action.</span><span class="sxs-lookup"><span data-stu-id="c774c-144">For example, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*> is called before the action method is called.</span></span> <span data-ttu-id="c774c-145"><xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*> se volá po návratu metody Action.</span><span class="sxs-lookup"><span data-stu-id="c774c-145"><xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*> is called after the action method returns.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="c774c-146">Asynchronní filtry definují metodu `On-Stage-ExecutionAsync`.</span><span class="sxs-lookup"><span data-stu-id="c774c-146">Asynchronous filters define an `On-Stage-ExecutionAsync` method.</span></span> <span data-ttu-id="c774c-147">Například <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*>:</span><span class="sxs-lookup"><span data-stu-id="c774c-147">For example, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*>:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/SampleAsyncActionFilter.cs?name=snippet)]

<span data-ttu-id="c774c-148">V předchozím kódu `SampleAsyncActionFilter` má <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`), který provádí metodu Action.</span><span class="sxs-lookup"><span data-stu-id="c774c-148">In the preceding code, the `SampleAsyncActionFilter` has an <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`) that executes the action method.</span></span>

### <a name="multiple-filter-stages"></a><span data-ttu-id="c774c-149">Několik fází filtru</span><span class="sxs-lookup"><span data-stu-id="c774c-149">Multiple filter stages</span></span>

<span data-ttu-id="c774c-150">Rozhraní pro více fází filtru lze implementovat v rámci jedné třídy.</span><span class="sxs-lookup"><span data-stu-id="c774c-150">Interfaces for multiple filter stages can be implemented in a single class.</span></span> <span data-ttu-id="c774c-151">Například třída <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> implementuje:</span><span class="sxs-lookup"><span data-stu-id="c774c-151">For example, the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> class implements:</span></span>

* <span data-ttu-id="c774c-152">Synchronní: <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> a <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter></span><span class="sxs-lookup"><span data-stu-id="c774c-152">Synchronous: <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> and  <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter></span></span>
* <span data-ttu-id="c774c-153">Asynchronní: <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> a <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="c774c-153">Asynchronous: <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
* <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>

<span data-ttu-id="c774c-154">Implementujte **buď** synchronní, nebo asynchronní verzi rozhraní filtru, **nikoli** obojí.</span><span class="sxs-lookup"><span data-stu-id="c774c-154">Implement **either** the synchronous or the async version of a filter interface, **not** both.</span></span> <span data-ttu-id="c774c-155">Modul runtime nejprve kontroluje, zda filtr implementuje asynchronní rozhraní a v takovém případě jej zavolá.</span><span class="sxs-lookup"><span data-stu-id="c774c-155">The runtime checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="c774c-156">V takovém případě volá metody synchronního rozhraní.</span><span class="sxs-lookup"><span data-stu-id="c774c-156">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="c774c-157">Pokud jsou asynchronní i synchronní rozhraní implementovány v jedné třídě, je volána pouze asynchronní metoda.</span><span class="sxs-lookup"><span data-stu-id="c774c-157">If both asynchronous and synchronous interfaces are implemented in one class, only the async method is called.</span></span> <span data-ttu-id="c774c-158">Při použití abstraktních tříd, jako je <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>, přepište pouze synchronní metody nebo asynchronní metodu pro každý typ filtru.</span><span class="sxs-lookup"><span data-stu-id="c774c-158">When using abstract classes like <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>, override only the synchronous methods or the asynchronous method for each filter type.</span></span>

### <a name="built-in-filter-attributes"></a><span data-ttu-id="c774c-159">Předdefinované atributy filtru</span><span class="sxs-lookup"><span data-stu-id="c774c-159">Built-in filter attributes</span></span>

<span data-ttu-id="c774c-160">ASP.NET Core obsahuje integrované filtry založené na atributech, které mohou být roztříděné a přizpůsobené.</span><span class="sxs-lookup"><span data-stu-id="c774c-160">ASP.NET Core includes built-in attribute-based filters that can be subclassed and customized.</span></span> <span data-ttu-id="c774c-161">Například následující filtr výsledků přidá hlavičku do odpovědi:</span><span class="sxs-lookup"><span data-stu-id="c774c-161">For example, the following result filter adds a header to the response:</span></span>

<a name="add-header-attribute"></a>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/AddHeaderAttribute.cs?name=snippet)]

<span data-ttu-id="c774c-162">Atributy umožňují filtrům přijímat argumenty, jak je znázorněno v předchozím příkladu.</span><span class="sxs-lookup"><span data-stu-id="c774c-162">Attributes allow filters to accept arguments, as shown in the preceding example.</span></span> <span data-ttu-id="c774c-163">Použijte `AddHeaderAttribute` pro metodu Controller nebo Action a zadejte název a hodnotu hlavičky HTTP:</span><span class="sxs-lookup"><span data-stu-id="c774c-163">Apply the `AddHeaderAttribute` to a controller or action method and specify the name and value of the HTTP header:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

<span data-ttu-id="c774c-164">K prohlédnutí hlaviček použijte nástroj, jako je například nástroj pro [vývojáře v prohlížeči](https://developer.mozilla.org/docs/Learn/Common_questions/What_are_browser_developer_tools) .</span><span class="sxs-lookup"><span data-stu-id="c774c-164">Use a tool such as the [browser developer tools](https://developer.mozilla.org/docs/Learn/Common_questions/What_are_browser_developer_tools) to examine the headers.</span></span> <span data-ttu-id="c774c-165">V části **hlavičky odpovědi**se zobrazí `author: Rick Anderson`.</span><span class="sxs-lookup"><span data-stu-id="c774c-165">Under **Response Headers**, `author: Rick Anderson` is displayed.</span></span>

<span data-ttu-id="c774c-166">Následující kód implementuje `ActionFilterAttribute`, který:</span><span class="sxs-lookup"><span data-stu-id="c774c-166">The following code implements an `ActionFilterAttribute` that:</span></span>

* <span data-ttu-id="c774c-167">Přečte název a název z konfiguračního systému.</span><span class="sxs-lookup"><span data-stu-id="c774c-167">Reads the title and name from the configuration system.</span></span> <span data-ttu-id="c774c-168">Na rozdíl od předchozí ukázky, následující kód nevyžaduje přidání parametrů filtru do kódu.</span><span class="sxs-lookup"><span data-stu-id="c774c-168">Unlike the previous sample, the following code doesn't require filter parameters to be added to the code.</span></span>
* <span data-ttu-id="c774c-169">Přidá název a název do hlavičky odpovědi.</span><span class="sxs-lookup"><span data-stu-id="c774c-169">Adds the title and name to the response header.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MyActionFilterAttribute.cs?name=snippet)]

<span data-ttu-id="c774c-170">Možnosti konfigurace se poskytují z [konfiguračního systému](xref:fundamentals/configuration/index) pomocí [vzoru možností](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="c774c-170">The configuration options are provided from the [configuration system](xref:fundamentals/configuration/index) using the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="c774c-171">Například ze souboru *appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="c774c-171">For example, from the *appsettings.json* file:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/appsettings.json)]

<span data-ttu-id="c774c-172">V `StartUp.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="c774c-172">In the `StartUp.ConfigureServices`:</span></span>

* <span data-ttu-id="c774c-173">Třída `PositionOptions` je přidána do kontejneru služby s oblastí konfigurace `"Position"`.</span><span class="sxs-lookup"><span data-stu-id="c774c-173">The `PositionOptions` class is added to the service container with the `"Position"` configuration area.</span></span>
* <span data-ttu-id="c774c-174">`MyActionFilterAttribute` se přidá do kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="c774c-174">The `MyActionFilterAttribute` is added to the service container.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupAF.cs?name=snippet)]

<span data-ttu-id="c774c-175">Následující kód ukazuje třídu `PositionOptions`:</span><span class="sxs-lookup"><span data-stu-id="c774c-175">The following code shows the `PositionOptions` class:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Helper/PositionOptions.cs?name=snippet)]

<span data-ttu-id="c774c-176">Následující kód používá `MyActionFilterAttribute` k metodě `Index2`:</span><span class="sxs-lookup"><span data-stu-id="c774c-176">The following code applies the `MyActionFilterAttribute` to the `Index2` method:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet2&highlight=9)]

<span data-ttu-id="c774c-177">V části **hlavičky odpovědi**`author: Rick Anderson`a `Editor: Joe Smith` se zobrazí při volání `Sample/Index2`ho koncového bodu.</span><span class="sxs-lookup"><span data-stu-id="c774c-177">Under **Response Headers**, `author: Rick Anderson`, and `Editor: Joe Smith` is displayed when the `Sample/Index2` endpoint is called.</span></span>

<span data-ttu-id="c774c-178">Následující kód použije `MyActionFilterAttribute` a `AddHeaderAttribute` na stránku Razor:</span><span class="sxs-lookup"><span data-stu-id="c774c-178">The following code applies the `MyActionFilterAttribute` and the `AddHeaderAttribute` to the Razor Page:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Pages/Movies/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="c774c-179">Filtry nelze použít pro metody obslužné rutiny stránky Razor.</span><span class="sxs-lookup"><span data-stu-id="c774c-179">Filters cannot be applied to Razor Page handler methods.</span></span> <span data-ttu-id="c774c-180">Mohou být aplikovány buď na model stránky Razor, nebo globálně.</span><span class="sxs-lookup"><span data-stu-id="c774c-180">They can be applied either to the Razor Page model or globally.</span></span>

<span data-ttu-id="c774c-181">Některé z rozhraní filtru mají odpovídající atributy, které lze použít jako základní třídy pro vlastní implementace.</span><span class="sxs-lookup"><span data-stu-id="c774c-181">Several of the filter interfaces have corresponding attributes that can be used as base classes for custom implementations.</span></span>

<span data-ttu-id="c774c-182">Atributy filtru:</span><span class="sxs-lookup"><span data-stu-id="c774c-182">Filter attributes:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ExceptionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>

## <a name="filter-scopes-and-order-of-execution"></a><span data-ttu-id="c774c-183">Filtrovat obory a pořadí provádění</span><span class="sxs-lookup"><span data-stu-id="c774c-183">Filter scopes and order of execution</span></span>

<span data-ttu-id="c774c-184">Filtr lze přidat do kanálu v jednom ze tří *oborů*:</span><span class="sxs-lookup"><span data-stu-id="c774c-184">A filter can be added to the pipeline at one of three *scopes*:</span></span>

* <span data-ttu-id="c774c-185">Použití atributu u akce kontroleru.</span><span class="sxs-lookup"><span data-stu-id="c774c-185">Using an attribute on a controller action.</span></span> <span data-ttu-id="c774c-186">Atributy filtru nelze použít pro Razor Pages metody obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="c774c-186">Filter attributes cannot be applied to Razor Pages handler methods.</span></span>
* <span data-ttu-id="c774c-187">Použití atributu na stránce kontroleru nebo Razor.</span><span class="sxs-lookup"><span data-stu-id="c774c-187">Using an attribute on a controller or Razor Page.</span></span>
* <span data-ttu-id="c774c-188">Globálně pro všechny řadiče, akce a Razor Pages, jak je znázorněno v následujícím kódu:</span><span class="sxs-lookup"><span data-stu-id="c774c-188">Globally for all controllers, actions, and Razor Pages as shown in the following code:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder.cs?name=snippet)]

### <a name="default-order-of-execution"></a><span data-ttu-id="c774c-189">Výchozí pořadí provádění</span><span class="sxs-lookup"><span data-stu-id="c774c-189">Default order of execution</span></span>

<span data-ttu-id="c774c-190">Pokud existuje více filtrů pro určitou fázi kanálu, rozsah Určuje výchozí pořadí provádění filtru.</span><span class="sxs-lookup"><span data-stu-id="c774c-190">When there are multiple filters for a particular stage of the pipeline, scope determines the default order of filter execution.</span></span>  <span data-ttu-id="c774c-191">Globální filtry obklopují filtry tříd, které jsou ve filtru metod Surround.</span><span class="sxs-lookup"><span data-stu-id="c774c-191">Global filters surround class filters, which in turn surround method filters.</span></span>

<span data-ttu-id="c774c-192">V důsledku vnořování filtru je *po* kódu spuštěn v obráceném pořadí *před* kódem.</span><span class="sxs-lookup"><span data-stu-id="c774c-192">As a result of filter nesting, the *after* code of filters runs in the reverse order of the *before* code.</span></span> <span data-ttu-id="c774c-193">Pořadí filtru:</span><span class="sxs-lookup"><span data-stu-id="c774c-193">The filter sequence:</span></span>

* <span data-ttu-id="c774c-194">*Před* kódem globálních filtrů.</span><span class="sxs-lookup"><span data-stu-id="c774c-194">The *before* code of global filters.</span></span>
  * <span data-ttu-id="c774c-195">Kód *před* filtrem stránky kontroleru a Razor.</span><span class="sxs-lookup"><span data-stu-id="c774c-195">The *before* code of controller and Razor Page filters.</span></span>
    * <span data-ttu-id="c774c-196">Kód *před* filtry metody Action.</span><span class="sxs-lookup"><span data-stu-id="c774c-196">The *before* code of action method filters.</span></span>
    * <span data-ttu-id="c774c-197">*Po* kódu filtry metody Action.</span><span class="sxs-lookup"><span data-stu-id="c774c-197">The *after* code of action method filters.</span></span>
  * <span data-ttu-id="c774c-198">Filtr *po* kódu řadiče a stránky Razor.</span><span class="sxs-lookup"><span data-stu-id="c774c-198">The *after* code of controller and Razor Page filters.</span></span>
* <span data-ttu-id="c774c-199">*Po* kódu globálních filtrů.</span><span class="sxs-lookup"><span data-stu-id="c774c-199">The *after* code of global filters.</span></span>
  
<span data-ttu-id="c774c-200">Následující příklad ilustruje pořadí, ve kterém jsou metody filtru volány pro filtry synchronních akcí.</span><span class="sxs-lookup"><span data-stu-id="c774c-200">The following example that illustrates the order in which filter methods are called for synchronous action filters.</span></span>

| <span data-ttu-id="c774c-201">Sequence</span><span class="sxs-lookup"><span data-stu-id="c774c-201">Sequence</span></span> | <span data-ttu-id="c774c-202">Rozsah filtru</span><span class="sxs-lookup"><span data-stu-id="c774c-202">Filter scope</span></span> | <span data-ttu-id="c774c-203">Filter – metoda</span><span class="sxs-lookup"><span data-stu-id="c774c-203">Filter method</span></span> |
|:--------:|:------------:|:-------------:|
| <span data-ttu-id="c774c-204">1</span><span class="sxs-lookup"><span data-stu-id="c774c-204">1</span></span> | <span data-ttu-id="c774c-205">Globální</span><span class="sxs-lookup"><span data-stu-id="c774c-205">Global</span></span> | `OnActionExecuting` |
| <span data-ttu-id="c774c-206">2</span><span class="sxs-lookup"><span data-stu-id="c774c-206">2</span></span> | <span data-ttu-id="c774c-207">Kontroler nebo stránka Razor</span><span class="sxs-lookup"><span data-stu-id="c774c-207">Controller or Razor Page</span></span>| `OnActionExecuting` |
| <span data-ttu-id="c774c-208">3</span><span class="sxs-lookup"><span data-stu-id="c774c-208">3</span></span> | <span data-ttu-id="c774c-209">Metoda</span><span class="sxs-lookup"><span data-stu-id="c774c-209">Method</span></span> | `OnActionExecuting` |
| <span data-ttu-id="c774c-210">4</span><span class="sxs-lookup"><span data-stu-id="c774c-210">4</span></span> | <span data-ttu-id="c774c-211">Metoda</span><span class="sxs-lookup"><span data-stu-id="c774c-211">Method</span></span> | `OnActionExecuted` |
| <span data-ttu-id="c774c-212">5</span><span class="sxs-lookup"><span data-stu-id="c774c-212">5</span></span> | <span data-ttu-id="c774c-213">Kontroler nebo stránka Razor</span><span class="sxs-lookup"><span data-stu-id="c774c-213">Controller or Razor Page</span></span> | `OnActionExecuted` |
| <span data-ttu-id="c774c-214">6</span><span class="sxs-lookup"><span data-stu-id="c774c-214">6</span></span> | <span data-ttu-id="c774c-215">Globální</span><span class="sxs-lookup"><span data-stu-id="c774c-215">Global</span></span> | `OnActionExecuted` |

### <a name="controller-level-filters"></a><span data-ttu-id="c774c-216">Filtry na úrovni řadiče</span><span class="sxs-lookup"><span data-stu-id="c774c-216">Controller level filters</span></span>

<span data-ttu-id="c774c-217">Každý kontroler, který dědí z <xref:Microsoft.AspNetCore.Mvc.Controller> základní třídy, zahrnuje metody [Controller. OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*), [Controller. OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*)a [Controller. OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted`.</span><span class="sxs-lookup"><span data-stu-id="c774c-217">Every controller that inherits from the <xref:Microsoft.AspNetCore.Mvc.Controller> base class includes [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*),  [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*), and [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted` methods.</span></span> <span data-ttu-id="c774c-218">Tyto metody:</span><span class="sxs-lookup"><span data-stu-id="c774c-218">These methods:</span></span>

* <span data-ttu-id="c774c-219">Zabalte filtry, které se spouštějí pro danou akci.</span><span class="sxs-lookup"><span data-stu-id="c774c-219">Wrap the filters that run for a given action.</span></span>
* <span data-ttu-id="c774c-220">`OnActionExecuting` se volá před všemi filtry akce.</span><span class="sxs-lookup"><span data-stu-id="c774c-220">`OnActionExecuting` is called before any of the action's filters.</span></span>
* <span data-ttu-id="c774c-221">`OnActionExecuted` se volá po všech filtrech akcí.</span><span class="sxs-lookup"><span data-stu-id="c774c-221">`OnActionExecuted` is called after all of the action filters.</span></span>
* <span data-ttu-id="c774c-222">`OnActionExecutionAsync` se volá před všemi filtry akce.</span><span class="sxs-lookup"><span data-stu-id="c774c-222">`OnActionExecutionAsync` is called before any of the action's filters.</span></span> <span data-ttu-id="c774c-223">Kód v filtru po `next` spustí po metodě Action.</span><span class="sxs-lookup"><span data-stu-id="c774c-223">Code in the filter after `next` runs after the action method.</span></span>

<span data-ttu-id="c774c-224">Například v ukázce stahování se `MySampleActionFilter` aplikuje globálně při spuštění.</span><span class="sxs-lookup"><span data-stu-id="c774c-224">For example, in the download sample, `MySampleActionFilter` is applied globally in startup.</span></span>

<span data-ttu-id="c774c-225">`TestController`:</span><span class="sxs-lookup"><span data-stu-id="c774c-225">The `TestController`:</span></span>

* <span data-ttu-id="c774c-226">Aplikuje `SampleActionFilterAttribute` (`[SampleActionFilter]`) na akci `FilterTest2`.</span><span class="sxs-lookup"><span data-stu-id="c774c-226">Applies the `SampleActionFilterAttribute` (`[SampleActionFilter]`) to the `FilterTest2` action.</span></span>
* <span data-ttu-id="c774c-227">Přepisuje `OnActionExecuting` a `OnActionExecuted`.</span><span class="sxs-lookup"><span data-stu-id="c774c-227">Overrides `OnActionExecuting` and `OnActionExecuted`.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/TestController.cs?name=snippet)]

<!-- test via  webBuilder.UseStartup<Startup>(); -->

<span data-ttu-id="c774c-228">Při přechodu na `https://localhost:5001/Test2/FilterTest2` se spustí následující kód:</span><span class="sxs-lookup"><span data-stu-id="c774c-228">Navigating to `https://localhost:5001/Test2/FilterTest2` runs the following code:</span></span>

* `TestController.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `SampleActionFilterAttribute.OnActionExecuting`
      * `TestController.FilterTest2`
    * `SampleActionFilterAttribute.OnActionExecuted`
  * `MySampleActionFilter.OnActionExecuted`
* `TestController.OnActionExecuted`

<span data-ttu-id="c774c-229">Filtry na úrovni řadiče nastaví vlastnost [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) na `int.MinValue`.</span><span class="sxs-lookup"><span data-stu-id="c774c-229">Controller level filters set the [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) property to `int.MinValue`.</span></span> <span data-ttu-id="c774c-230">Filtry na úrovni řadiče **nelze nastavit** tak, aby běžely po filtrech použitých na metody.</span><span class="sxs-lookup"><span data-stu-id="c774c-230">Controller level filters can **not** be set to run after filters applied to methods.</span></span> <span data-ttu-id="c774c-231">Pořadí je vysvětleno v další části.</span><span class="sxs-lookup"><span data-stu-id="c774c-231">Order is explained in the next section.</span></span>

<span data-ttu-id="c774c-232">Razor Pages najdete v tématu [implementace filtrů stránek Razor pomocí přepsání metod filtru](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span><span class="sxs-lookup"><span data-stu-id="c774c-232">For Razor Pages, see [Implement Razor Page filters by overriding filter methods](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span></span>

### <a name="overriding-the-default-order"></a><span data-ttu-id="c774c-233">Přepsání výchozího pořadí</span><span class="sxs-lookup"><span data-stu-id="c774c-233">Overriding the default order</span></span>

<span data-ttu-id="c774c-234">Výchozí sekvenci spuštění lze přepsat implementací <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span><span class="sxs-lookup"><span data-stu-id="c774c-234">The default sequence of execution can be overridden by implementing <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span></span> <span data-ttu-id="c774c-235">`IOrderedFilter` zpřístupňuje vlastnost <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order>, která má přednost před rozsahem, aby bylo možné určit pořadí spouštění.</span><span class="sxs-lookup"><span data-stu-id="c774c-235">`IOrderedFilter` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> property that takes precedence over scope to determine the order of execution.</span></span> <span data-ttu-id="c774c-236">Filtr s nižší hodnotou `Order`:</span><span class="sxs-lookup"><span data-stu-id="c774c-236">A filter with a lower `Order` value:</span></span>

* <span data-ttu-id="c774c-237">Spustí *před* kódem filtr s vyšší hodnotou `Order`.</span><span class="sxs-lookup"><span data-stu-id="c774c-237">Runs the *before* code before that of a filter with a higher value of `Order`.</span></span>
* <span data-ttu-id="c774c-238">Spustí *za* kódem filtru s vyšší `Order` hodnotou.</span><span class="sxs-lookup"><span data-stu-id="c774c-238">Runs the *after* code after that of a filter with a higher `Order` value.</span></span>

<span data-ttu-id="c774c-239">Vlastnost `Order` je nastavena s parametrem konstruktoru:</span><span class="sxs-lookup"><span data-stu-id="c774c-239">The `Order` property is set with a constructor parameter:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test3Controller.cs?name=snippet)]

<span data-ttu-id="c774c-240">Vezměte v úvahu dva filtry akcí v následujícím kontroleru:</span><span class="sxs-lookup"><span data-stu-id="c774c-240">Consider the two action filters in the following controller:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test2Controller.cs?name=snippet)]

<span data-ttu-id="c774c-241">Do `StartUp.ConfigureServices`se přidá globální filtr:</span><span class="sxs-lookup"><span data-stu-id="c774c-241">A global filter is added in `StartUp.ConfigureServices`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder.cs?name=snippet)]

<span data-ttu-id="c774c-242">Tři filtry jsou spouštěny v následujícím pořadí:</span><span class="sxs-lookup"><span data-stu-id="c774c-242">The 3 filters run in the following order:</span></span>

* `Test2Controller.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `MyAction2FilterAttribute.OnActionExecuting`
      * `Test2Controller.FilterTest2`
    * `MySampleActionFilter.OnActionExecuted`
  * `MyAction2FilterAttribute.OnResultExecuting`
* `Test2Controller.OnActionExecuted`

<span data-ttu-id="c774c-243">Vlastnost `Order` Přepisuje obor při určování pořadí, ve kterém jsou filtry spouštěny.</span><span class="sxs-lookup"><span data-stu-id="c774c-243">The `Order` property overrides scope when determining the order in which filters run.</span></span> <span data-ttu-id="c774c-244">Filtry jsou seřazené podle pořadí, pak se k přerušení vztahů používá obor.</span><span class="sxs-lookup"><span data-stu-id="c774c-244">Filters are sorted first by order, then scope is used to break ties.</span></span> <span data-ttu-id="c774c-245">Všechny předdefinované filtry implementují `IOrderedFilter` a nastaví výchozí hodnotu `Order` na 0.</span><span class="sxs-lookup"><span data-stu-id="c774c-245">All of the built-in filters implement `IOrderedFilter` and set the default `Order` value to 0.</span></span> <span data-ttu-id="c774c-246">Jak již bylo zmíněno dříve, filtry na úrovni řadiče nastaví vlastnost [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) na `int.MinValue` pro předdefinované filtry, pokud je `Order` nastavena na nenulovou hodnotu, určuje rozsah.</span><span class="sxs-lookup"><span data-stu-id="c774c-246">As mentioned previously, controller level filters set the [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) property to `int.MinValue` For built-in filters, scope determines order unless `Order` is set to a non-zero value.</span></span>

<span data-ttu-id="c774c-247">V předchozím kódu `MySampleActionFilter` má globální rozsah, takže se spustí před `MyAction2FilterAttribute`a s rozsahem kontroleru.</span><span class="sxs-lookup"><span data-stu-id="c774c-247">In the preceding code, `MySampleActionFilter` has global scope so it runs before `MyAction2FilterAttribute`, which has controller scope.</span></span> <span data-ttu-id="c774c-248">Pokud chcete `MyAction2FilterAttribute` spustit jako první, nastavte pořadí na `int.MinValue`:</span><span class="sxs-lookup"><span data-stu-id="c774c-248">To make `MyAction2FilterAttribute` run first, set the order to `int.MinValue`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test2Controller.cs?name=snippet2)]

<span data-ttu-id="c774c-249">Chcete-li nastavit globální filtr `MySampleActionFilter` nejprve spustit, nastavte `Order` na `int.MinValue`:</span><span class="sxs-lookup"><span data-stu-id="c774c-249">To make the global filter `MySampleActionFilter` run first, set `Order` to `int.MinValue`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder2.cs?name=snippet&highlight=6)]

## <a name="cancellation-and-short-circuiting"></a><span data-ttu-id="c774c-250">Zrušení a zkrácení okruhů</span><span class="sxs-lookup"><span data-stu-id="c774c-250">Cancellation and short-circuiting</span></span>

<span data-ttu-id="c774c-251">Kanál filtru může být v krátkém okruhu nastavením vlastnosti <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> u parametru <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> poskytnutého metodě Filter.</span><span class="sxs-lookup"><span data-stu-id="c774c-251">The filter pipeline can be short-circuited by setting the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> property on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> parameter provided to the filter method.</span></span> <span data-ttu-id="c774c-252">Například následující filtr prostředků zabrání ve spuštění zbývajícího kanálu:</span><span class="sxs-lookup"><span data-stu-id="c774c-252">For instance, the following Resource filter prevents the rest of the pipeline from executing:</span></span>

<a name="short-circuiting-resource-filter"></a>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ShortCircuitingResourceFilterAttribute.cs?name=snippet)]

<span data-ttu-id="c774c-253">V následujícím kódu, jak `ShortCircuitingResourceFilter`, tak filtr `AddHeader` cílovou metodu `SomeResource` akce.</span><span class="sxs-lookup"><span data-stu-id="c774c-253">In the following code, both the `ShortCircuitingResourceFilter` and the `AddHeader` filter target the `SomeResource` action method.</span></span> <span data-ttu-id="c774c-254">`ShortCircuitingResourceFilter`:</span><span class="sxs-lookup"><span data-stu-id="c774c-254">The `ShortCircuitingResourceFilter`:</span></span>

* <span data-ttu-id="c774c-255">Nejprve se spustí, protože se jedná o filtr prostředku a `AddHeader` je filtr akcí.</span><span class="sxs-lookup"><span data-stu-id="c774c-255">Runs first, because it's a Resource Filter and `AddHeader` is an Action Filter.</span></span>
* <span data-ttu-id="c774c-256">Krátké okruhy zbývajícího kanálu.</span><span class="sxs-lookup"><span data-stu-id="c774c-256">Short-circuits the rest of the pipeline.</span></span>

<span data-ttu-id="c774c-257">Proto filtr `AddHeader` pro akci `SomeResource` nikdy neběží.</span><span class="sxs-lookup"><span data-stu-id="c774c-257">Therefore the `AddHeader` filter never runs for the `SomeResource` action.</span></span> <span data-ttu-id="c774c-258">Toto chování by bylo stejné, pokud byly oba filtry aplikovány na úrovni metody akce, a to za předpokladu, že `ShortCircuitingResourceFilter` běžela jako první.</span><span class="sxs-lookup"><span data-stu-id="c774c-258">This behavior would be the same if both filters were applied at the action method level, provided the `ShortCircuitingResourceFilter` ran first.</span></span> <span data-ttu-id="c774c-259">`ShortCircuitingResourceFilter` se nejprve spustí z důvodu jeho typu filtru nebo explicitním použitím vlastnosti `Order`.</span><span class="sxs-lookup"><span data-stu-id="c774c-259">The `ShortCircuitingResourceFilter` runs first because of its filter type, or by explicit use of `Order` property.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

## <a name="dependency-injection"></a><span data-ttu-id="c774c-260">Injektáž závislostí</span><span class="sxs-lookup"><span data-stu-id="c774c-260">Dependency injection</span></span>

<span data-ttu-id="c774c-261">Filtry lze přidat podle typu nebo podle instance.</span><span class="sxs-lookup"><span data-stu-id="c774c-261">Filters can be added by type or by instance.</span></span> <span data-ttu-id="c774c-262">Pokud je přidána instance, bude tato instance použita pro každý požadavek.</span><span class="sxs-lookup"><span data-stu-id="c774c-262">If an instance is added, that instance is used for every request.</span></span> <span data-ttu-id="c774c-263">Pokud je přidán typ, je aktivován typ.</span><span class="sxs-lookup"><span data-stu-id="c774c-263">If a type is added, it's type-activated.</span></span> <span data-ttu-id="c774c-264">Filtr aktivovaný typu znamená:</span><span class="sxs-lookup"><span data-stu-id="c774c-264">A type-activated filter means:</span></span>

* <span data-ttu-id="c774c-265">Instance se vytvoří pro každý požadavek.</span><span class="sxs-lookup"><span data-stu-id="c774c-265">An instance is created for each request.</span></span>
* <span data-ttu-id="c774c-266">Jakékoli závislosti konstruktoru jsou vyplněny pomocí [Injektáže závislosti](xref:fundamentals/dependency-injection) (di).</span><span class="sxs-lookup"><span data-stu-id="c774c-266">Any constructor dependencies are populated by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span>

<span data-ttu-id="c774c-267">Filtry, které jsou implementovány jako atributy a přidány přímo do tříd kontroleru nebo metody akcí, nemohou mít závislosti konstruktoru poskytované [vkládáním závislostí](xref:fundamentals/dependency-injection) (di).</span><span class="sxs-lookup"><span data-stu-id="c774c-267">Filters that are implemented as attributes and added directly to controller classes or action methods cannot have constructor dependencies provided by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span> <span data-ttu-id="c774c-268">Pomocí DI nelze poskytnout závislosti konstruktoru:</span><span class="sxs-lookup"><span data-stu-id="c774c-268">Constructor dependencies cannot be provided by DI because:</span></span>

* <span data-ttu-id="c774c-269">V atributech musí být zadány parametry konstruktoru, kde jsou aplikovány.</span><span class="sxs-lookup"><span data-stu-id="c774c-269">Attributes must have their constructor parameters supplied where they're applied.</span></span> 
* <span data-ttu-id="c774c-270">Toto je omezení způsobu fungování atributů.</span><span class="sxs-lookup"><span data-stu-id="c774c-270">This is a limitation of how attributes work.</span></span>

<span data-ttu-id="c774c-271">Následující filtry podporují závislosti konstruktoru poskytované od DI:</span><span class="sxs-lookup"><span data-stu-id="c774c-271">The following filters support constructor dependencies provided from DI:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>
* <span data-ttu-id="c774c-272"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implementované na atribut.</span><span class="sxs-lookup"><span data-stu-id="c774c-272"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implemented on the attribute.</span></span>

<span data-ttu-id="c774c-273">Předchozí filtry lze použít pro metodu kontroleru nebo akce:</span><span class="sxs-lookup"><span data-stu-id="c774c-273">The preceding filters can be applied to a controller or action method:</span></span>

<span data-ttu-id="c774c-274">Protokolovací nástroje jsou k dispozici z DI.</span><span class="sxs-lookup"><span data-stu-id="c774c-274">Loggers are available from DI.</span></span> <span data-ttu-id="c774c-275">Ale nevytvářejte a používejte filtry čistě pro účely protokolování.</span><span class="sxs-lookup"><span data-stu-id="c774c-275">However, avoid creating and using filters purely for logging purposes.</span></span> <span data-ttu-id="c774c-276">[Integrované protokolování rozhraní](xref:fundamentals/logging/index) obvykle zajišťuje, co je potřeba pro protokolování.</span><span class="sxs-lookup"><span data-stu-id="c774c-276">The [built-in framework logging](xref:fundamentals/logging/index) typically provides what's needed for logging.</span></span> <span data-ttu-id="c774c-277">Protokolování přidáno do filtrů:</span><span class="sxs-lookup"><span data-stu-id="c774c-277">Logging added to filters:</span></span>

* <span data-ttu-id="c774c-278">Měl by se soustředit na obavy a chování obchodních domén, které jsou specifické pro filtr.</span><span class="sxs-lookup"><span data-stu-id="c774c-278">Should focus on business domain concerns or behavior specific to the filter.</span></span>
* <span data-ttu-id="c774c-279">Neměli **byste** protokolovat akce nebo jiné události rozhraní.</span><span class="sxs-lookup"><span data-stu-id="c774c-279">Should **not** log actions or other framework events.</span></span> <span data-ttu-id="c774c-280">Vestavěné filtry protokolují akce a události rozhraní.</span><span class="sxs-lookup"><span data-stu-id="c774c-280">The built-in filters log actions and framework events.</span></span>

### <a name="servicefilterattribute"></a><span data-ttu-id="c774c-281">ServiceFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="c774c-281">ServiceFilterAttribute</span></span>

<span data-ttu-id="c774c-282">Typy implementace filtru služby jsou registrovány v `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="c774c-282">Service filter implementation types are registered in `ConfigureServices`.</span></span> <span data-ttu-id="c774c-283"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> načte instanci filtru z DI.</span><span class="sxs-lookup"><span data-stu-id="c774c-283">A <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> retrieves an instance of the filter from DI.</span></span>

<span data-ttu-id="c774c-284">Následující kód ukazuje `AddHeaderResultServiceFilter`:</span><span class="sxs-lookup"><span data-stu-id="c774c-284">The following code shows the `AddHeaderResultServiceFilter`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="c774c-285">V následujícím kódu je `AddHeaderResultServiceFilter` přidána do kontejneru DI:</span><span class="sxs-lookup"><span data-stu-id="c774c-285">In the following code, `AddHeaderResultServiceFilter` is added to the DI container:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Startup.cs?name=snippet&highlight=4)]

<span data-ttu-id="c774c-286">V následujícím kódu atribut `ServiceFilter` načte instanci `AddHeaderResultServiceFilter` filtru z DI:</span><span class="sxs-lookup"><span data-stu-id="c774c-286">In the following code, the `ServiceFilter` attribute retrieves an instance of the `AddHeaderResultServiceFilter` filter from DI:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_ServiceFilter&highlight=1)]

<span data-ttu-id="c774c-287">Při použití `ServiceFilterAttribute`se nastavuje [ServiceFilterAttribute. IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span><span class="sxs-lookup"><span data-stu-id="c774c-287">When using `ServiceFilterAttribute`, setting [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span></span>

* <span data-ttu-id="c774c-288">Poskytuje nápovědu, že se instance filtru *dá* znovu použít mimo rozsah požadavku, který byl vytvořen v rámci.</span><span class="sxs-lookup"><span data-stu-id="c774c-288">Provides a hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="c774c-289">Modul runtime ASP.NET Core nezaručuje:</span><span class="sxs-lookup"><span data-stu-id="c774c-289">The ASP.NET Core runtime doesn't guarantee:</span></span>

  * <span data-ttu-id="c774c-290">Vytvoří se jedna instance filtru.</span><span class="sxs-lookup"><span data-stu-id="c774c-290">That a single instance of the filter will be created.</span></span>
  * <span data-ttu-id="c774c-291">Filtr nebude znovu vyžádán z kontejneru DI v pozdějším bodě.</span><span class="sxs-lookup"><span data-stu-id="c774c-291">The filter will not be re-requested from the DI container at some later point.</span></span>

* <span data-ttu-id="c774c-292">Neměl by se používat s filtrem, který závisí na službách s jinou životností než singleton.</span><span class="sxs-lookup"><span data-stu-id="c774c-292">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

 <span data-ttu-id="c774c-293"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implementuje <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span><span class="sxs-lookup"><span data-stu-id="c774c-293"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="c774c-294">`IFilterFactory` zpřístupňuje <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> metodu pro vytvoření instance <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span><span class="sxs-lookup"><span data-stu-id="c774c-294">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="c774c-295">`CreateInstance` načte zadaný typ z DI.</span><span class="sxs-lookup"><span data-stu-id="c774c-295">`CreateInstance` loads the specified type from DI.</span></span>

### <a name="typefilterattribute"></a><span data-ttu-id="c774c-296">TypeFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="c774c-296">TypeFilterAttribute</span></span>

<span data-ttu-id="c774c-297"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> je podobná <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, ale její typ není vyřešen přímo z kontejneru DI.</span><span class="sxs-lookup"><span data-stu-id="c774c-297"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> is similar to <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, but its type isn't resolved directly from the DI container.</span></span> <span data-ttu-id="c774c-298">Vytvoří instanci typu pomocí <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="c774c-298">It instantiates the type by using <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span></span>

<span data-ttu-id="c774c-299">Vzhledem k tomu, že typy `TypeFilterAttribute` nejsou vyřešeny přímo z kontejneru DI:</span><span class="sxs-lookup"><span data-stu-id="c774c-299">Because `TypeFilterAttribute` types aren't resolved directly from the DI container:</span></span>

* <span data-ttu-id="c774c-300">Typy, na které se odkazuje pomocí `TypeFilterAttribute`, nemusejí být registrovány v kontejneru DI.</span><span class="sxs-lookup"><span data-stu-id="c774c-300">Types that are referenced using the `TypeFilterAttribute` don't need to be registered with the DI container.</span></span>  <span data-ttu-id="c774c-301">Mají své závislosti splněné kontejnerem DI.</span><span class="sxs-lookup"><span data-stu-id="c774c-301">They do have their dependencies fulfilled by the DI container.</span></span>
* <span data-ttu-id="c774c-302">`TypeFilterAttribute` může volitelně přijímat argumenty konstruktoru pro daný typ.</span><span class="sxs-lookup"><span data-stu-id="c774c-302">`TypeFilterAttribute` can optionally accept constructor arguments for the type.</span></span>

<span data-ttu-id="c774c-303">Při použití `TypeFilterAttribute`se nastavuje [TypeFilterAttribute. IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span><span class="sxs-lookup"><span data-stu-id="c774c-303">When using `TypeFilterAttribute`, setting [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span></span>
* <span data-ttu-id="c774c-304">Poskytuje nápovědu, že se instance filtru *dá* znovu použít mimo rozsah požadavku, který byl vytvořen v rámci.</span><span class="sxs-lookup"><span data-stu-id="c774c-304">Provides hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="c774c-305">Modul runtime ASP.NET Core neposkytuje žádné záruky, že se vytvoří jediná instance filtru.</span><span class="sxs-lookup"><span data-stu-id="c774c-305">The ASP.NET Core runtime provides no guarantees that a single instance of the filter will be created.</span></span>

* <span data-ttu-id="c774c-306">Neměl by se používat s filtrem, který závisí na službách s jinou životností než singleton.</span><span class="sxs-lookup"><span data-stu-id="c774c-306">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

<span data-ttu-id="c774c-307">Následující příklad ukazuje, jak předat argumenty typu pomocí `TypeFilterAttribute`:</span><span class="sxs-lookup"><span data-stu-id="c774c-307">The following example shows how to pass arguments to a type using `TypeFilterAttribute`:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_TypeFilter&highlight=1,2)]

<!-- 
https://localhost:5001/home/hi?name=joe
VS debug window shows 
FiltersSample.Filters.LogConstantFilter:Information: Method 'Hi' called
-->

## <a name="authorization-filters"></a><span data-ttu-id="c774c-308">Filtry autorizace</span><span class="sxs-lookup"><span data-stu-id="c774c-308">Authorization filters</span></span>

<span data-ttu-id="c774c-309">Filtry autorizace:</span><span class="sxs-lookup"><span data-stu-id="c774c-309">Authorization filters:</span></span>

* <span data-ttu-id="c774c-310">Jsou první filtry spouštěny v kanálu filtru.</span><span class="sxs-lookup"><span data-stu-id="c774c-310">Are the first filters run in the filter pipeline.</span></span>
* <span data-ttu-id="c774c-311">Řízení přístupu k metodám akcí.</span><span class="sxs-lookup"><span data-stu-id="c774c-311">Control access to action methods.</span></span>
* <span data-ttu-id="c774c-312">Před metodou, ale ne po metodě.</span><span class="sxs-lookup"><span data-stu-id="c774c-312">Have a before method, but no after method.</span></span>

<span data-ttu-id="c774c-313">Vlastní autorizační filtry vyžadují vlastní autorizační rozhraní.</span><span class="sxs-lookup"><span data-stu-id="c774c-313">Custom authorization filters require a custom authorization framework.</span></span> <span data-ttu-id="c774c-314">Preferovat konfiguraci autorizačních zásad nebo psaní vlastních zásad autorizace při psaní vlastního filtru.</span><span class="sxs-lookup"><span data-stu-id="c774c-314">Prefer configuring the authorization policies or writing a custom authorization policy over writing a custom filter.</span></span> <span data-ttu-id="c774c-315">Vestavěný autorizační filtr:</span><span class="sxs-lookup"><span data-stu-id="c774c-315">The built-in authorization filter:</span></span>

* <span data-ttu-id="c774c-316">Volá autorizační systém.</span><span class="sxs-lookup"><span data-stu-id="c774c-316">Calls the authorization system.</span></span>
* <span data-ttu-id="c774c-317">Neautorizuje požadavky.</span><span class="sxs-lookup"><span data-stu-id="c774c-317">Does not authorize requests.</span></span>

<span data-ttu-id="c774c-318">Negenerovat **výjimky** v rámci autorizačních filtrů:</span><span class="sxs-lookup"><span data-stu-id="c774c-318">Do **not** throw exceptions within authorization filters:</span></span>

* <span data-ttu-id="c774c-319">Výjimka nebude zpracována.</span><span class="sxs-lookup"><span data-stu-id="c774c-319">The exception will not be handled.</span></span>
* <span data-ttu-id="c774c-320">Filtry výjimek nebudou zpracovávat výjimku.</span><span class="sxs-lookup"><span data-stu-id="c774c-320">Exception filters will not handle the exception.</span></span>

<span data-ttu-id="c774c-321">Zvažte vydání výzvy, pokud dojde k výjimce ve autorizačním filtru.</span><span class="sxs-lookup"><span data-stu-id="c774c-321">Consider issuing a challenge when an exception occurs in an authorization filter.</span></span>

<span data-ttu-id="c774c-322">Přečtěte si další informace o [autorizaci](xref:security/authorization/introduction).</span><span class="sxs-lookup"><span data-stu-id="c774c-322">Learn more about [Authorization](xref:security/authorization/introduction).</span></span>

## <a name="resource-filters"></a><span data-ttu-id="c774c-323">Filtry prostředků</span><span class="sxs-lookup"><span data-stu-id="c774c-323">Resource filters</span></span>

<span data-ttu-id="c774c-324">Filtry prostředků:</span><span class="sxs-lookup"><span data-stu-id="c774c-324">Resource filters:</span></span>

* <span data-ttu-id="c774c-325">Implementujte rozhraní <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> nebo <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter>.</span><span class="sxs-lookup"><span data-stu-id="c774c-325">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> interface.</span></span>
* <span data-ttu-id="c774c-326">Provádění zabalí většinu kanálu filtru.</span><span class="sxs-lookup"><span data-stu-id="c774c-326">Execution wraps most of the filter pipeline.</span></span>
* <span data-ttu-id="c774c-327">Před filtry prostředků se spouštějí jenom [filtry autorizace](#authorization-filters) .</span><span class="sxs-lookup"><span data-stu-id="c774c-327">Only [Authorization filters](#authorization-filters) run before resource filters.</span></span>

<span data-ttu-id="c774c-328">Filtry prostředků jsou užitečné pro krátkodobé okruhy v kanálu.</span><span class="sxs-lookup"><span data-stu-id="c774c-328">Resource filters are useful to short-circuit most of the pipeline.</span></span> <span data-ttu-id="c774c-329">Například filtr ukládání do mezipaměti může zabránit zbývajícímu kanálu v případě přístupů do mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="c774c-329">For example, a caching filter can avoid the rest of the pipeline on a cache hit.</span></span>

<span data-ttu-id="c774c-330">Příklady filtru prostředků:</span><span class="sxs-lookup"><span data-stu-id="c774c-330">Resource filter examples:</span></span>

* <span data-ttu-id="c774c-331">[Filtr prostředků se zkrácenými okruhy](#short-circuiting-resource-filter) zobrazenými dříve.</span><span class="sxs-lookup"><span data-stu-id="c774c-331">[The short-circuiting resource filter](#short-circuiting-resource-filter) shown previously.</span></span>
* <span data-ttu-id="c774c-332">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span><span class="sxs-lookup"><span data-stu-id="c774c-332">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span></span>

  * <span data-ttu-id="c774c-333">Zabraňuje vazbě modelu v přístupu k datům formuláře.</span><span class="sxs-lookup"><span data-stu-id="c774c-333">Prevents model binding from accessing the form data.</span></span>
  * <span data-ttu-id="c774c-334">Používá se pro nahrávání velkých souborů, aby se zabránilo čtení dat z formuláře do paměti.</span><span class="sxs-lookup"><span data-stu-id="c774c-334">Used for large file uploads to prevent the form data from being read into memory.</span></span>

## <a name="action-filters"></a><span data-ttu-id="c774c-335">Filtry akcí</span><span class="sxs-lookup"><span data-stu-id="c774c-335">Action filters</span></span>

<span data-ttu-id="c774c-336">Filtry akcí **se** nevztahují na Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="c774c-336">Action filters do **not** apply to Razor Pages.</span></span> <span data-ttu-id="c774c-337">Razor Pages podporuje <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> a <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter>.</span><span class="sxs-lookup"><span data-stu-id="c774c-337">Razor Pages supports <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span></span> <span data-ttu-id="c774c-338">Další informace naleznete v tématu [metody Filter pro Razor Pages](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="c774c-338">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

<span data-ttu-id="c774c-339">Filtry akcí:</span><span class="sxs-lookup"><span data-stu-id="c774c-339">Action filters:</span></span>

* <span data-ttu-id="c774c-340">Implementujte rozhraní <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> nebo <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter>.</span><span class="sxs-lookup"><span data-stu-id="c774c-340">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> interface.</span></span>
* <span data-ttu-id="c774c-341">Jejich spuštění obklopuje provádění metod akcí.</span><span class="sxs-lookup"><span data-stu-id="c774c-341">Their execution surrounds the execution of action methods.</span></span>

<span data-ttu-id="c774c-342">Následující kód ukazuje filtr vzorové akce:</span><span class="sxs-lookup"><span data-stu-id="c774c-342">The following code shows a sample action filter:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="c774c-343"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> poskytuje následující vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="c774c-343">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> provides the following properties:</span></span>

* <span data-ttu-id="c774c-344"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> – povolí čtení vstupů do metody Action.</span><span class="sxs-lookup"><span data-stu-id="c774c-344"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> - enables reading the inputs to an action method.</span></span>
* <span data-ttu-id="c774c-345"><xref:Microsoft.AspNetCore.Mvc.Controller> – povolí manipulaci s instancí kontroleru.</span><span class="sxs-lookup"><span data-stu-id="c774c-345"><xref:Microsoft.AspNetCore.Mvc.Controller> - enables manipulating the controller instance.</span></span>
* <span data-ttu-id="c774c-346"><xref:System.Web.Mvc.ActionExecutingContext.Result> – nastavení `Result` provádění krátkých okruhů metodou Action a následnými filtry akcí.</span><span class="sxs-lookup"><span data-stu-id="c774c-346"><xref:System.Web.Mvc.ActionExecutingContext.Result> - setting `Result` short-circuits execution of the action method and subsequent action filters.</span></span>

<span data-ttu-id="c774c-347">Vyvolání výjimky v metodě akce:</span><span class="sxs-lookup"><span data-stu-id="c774c-347">Throwing an exception in an action method:</span></span>

* <span data-ttu-id="c774c-348">Zabraňuje spuštění dalších filtrů.</span><span class="sxs-lookup"><span data-stu-id="c774c-348">Prevents running of subsequent filters.</span></span>
* <span data-ttu-id="c774c-349">Na rozdíl od nastavení `Result`se místo úspěšného výsledku považuje za selhání.</span><span class="sxs-lookup"><span data-stu-id="c774c-349">Unlike setting `Result`, is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="c774c-350"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> poskytuje `Controller` a `Result` a následující vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="c774c-350">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> provides `Controller` and `Result` plus the following properties:</span></span>

* <span data-ttu-id="c774c-351"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> – hodnota true, pokud provádění akce bylo zkráceno jiným filtrem.</span><span class="sxs-lookup"><span data-stu-id="c774c-351"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> - True if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="c774c-352"><xref:System.Web.Mvc.ActionExecutedContext.Exception> – nenulová, pokud akce nebo filtr dříve spuštěných akcí vyvolaly výjimku.</span><span class="sxs-lookup"><span data-stu-id="c774c-352"><xref:System.Web.Mvc.ActionExecutedContext.Exception> - Non-null if the action or a previously run action filter threw an exception.</span></span> <span data-ttu-id="c774c-353">Nastavení této vlastnosti na hodnotu null:</span><span class="sxs-lookup"><span data-stu-id="c774c-353">Setting this property to null:</span></span>

  * <span data-ttu-id="c774c-354">Efektivně zpracovává výjimku.</span><span class="sxs-lookup"><span data-stu-id="c774c-354">Effectively handles the exception.</span></span>
  * <span data-ttu-id="c774c-355">`Result` se spustí, jako kdyby byla vrácena z metody Action.</span><span class="sxs-lookup"><span data-stu-id="c774c-355">`Result` is executed as if it was returned from the action method.</span></span>

<span data-ttu-id="c774c-356">Pro `IAsyncActionFilter`volání <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span><span class="sxs-lookup"><span data-stu-id="c774c-356">For an `IAsyncActionFilter`, a call to the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span></span>

* <span data-ttu-id="c774c-357">Provede všechny následné filtry akcí a metodu Action.</span><span class="sxs-lookup"><span data-stu-id="c774c-357">Executes any subsequent action filters and the action method.</span></span>
* <span data-ttu-id="c774c-358">Vrací objekt `ActionExecutedContext`.</span><span class="sxs-lookup"><span data-stu-id="c774c-358">Returns `ActionExecutedContext`.</span></span>

<span data-ttu-id="c774c-359">Do krátkodobého okruhu, přiřaďte <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> k instanci výsledku a Nevolejte `next` (`ActionExecutionDelegate`).</span><span class="sxs-lookup"><span data-stu-id="c774c-359">To short-circuit, assign <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> to a result instance and don't call `next` (the `ActionExecutionDelegate`).</span></span>

<span data-ttu-id="c774c-360">Rozhraní poskytuje abstraktní <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>, které mohou být roztříděné.</span><span class="sxs-lookup"><span data-stu-id="c774c-360">The framework provides an abstract <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> that can be subclassed.</span></span>

<span data-ttu-id="c774c-361">Filtr akce `OnActionExecuting` lze použít k těmto akcím:</span><span class="sxs-lookup"><span data-stu-id="c774c-361">The `OnActionExecuting` action filter can be used to:</span></span>

* <span data-ttu-id="c774c-362">Ověří stav modelu.</span><span class="sxs-lookup"><span data-stu-id="c774c-362">Validate model state.</span></span>
* <span data-ttu-id="c774c-363">Pokud je stav neplatný, vrátí se chyba.</span><span class="sxs-lookup"><span data-stu-id="c774c-363">Return an error if the state is invalid.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet)]

<span data-ttu-id="c774c-364">Metoda `OnActionExecuted` se spouští po metodě Action:</span><span class="sxs-lookup"><span data-stu-id="c774c-364">The `OnActionExecuted` method runs after the action method:</span></span>

* <span data-ttu-id="c774c-365">A mohou zobrazit výsledky akce a manipulovat s nimi prostřednictvím vlastnosti <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result>.</span><span class="sxs-lookup"><span data-stu-id="c774c-365">And can see and manipulate the results of the action through the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> property.</span></span>
* <span data-ttu-id="c774c-366"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> je nastavena na hodnotu true, pokud bylo provedení akce zkráceno jiným filtrem.</span><span class="sxs-lookup"><span data-stu-id="c774c-366"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> is set to true if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="c774c-367"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> je nastavena na hodnotu jinou než null, pokud akce nebo filtr následné akce vyvolaly výjimku.</span><span class="sxs-lookup"><span data-stu-id="c774c-367"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> is set to a non-null value if the action or a subsequent action filter threw an exception.</span></span> <span data-ttu-id="c774c-368">Nastavení `Exception` na hodnotu null:</span><span class="sxs-lookup"><span data-stu-id="c774c-368">Setting `Exception` to null:</span></span>

  * <span data-ttu-id="c774c-369">Efektivně zpracovává výjimku.</span><span class="sxs-lookup"><span data-stu-id="c774c-369">Effectively handles an exception.</span></span>
  * <span data-ttu-id="c774c-370">`ActionExecutedContext.Result` se spustí, jako kdyby byl vrácen normálně z metody Action.</span><span class="sxs-lookup"><span data-stu-id="c774c-370">`ActionExecutedContext.Result` is executed as if it were returned normally from the action method.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet2&higlight=12-99)]

## <a name="exception-filters"></a><span data-ttu-id="c774c-371">Filtry výjimek</span><span class="sxs-lookup"><span data-stu-id="c774c-371">Exception filters</span></span>

<span data-ttu-id="c774c-372">Filtry výjimek:</span><span class="sxs-lookup"><span data-stu-id="c774c-372">Exception filters:</span></span>

* <span data-ttu-id="c774c-373">Implementujte <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> nebo <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span><span class="sxs-lookup"><span data-stu-id="c774c-373">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span></span>
* <span data-ttu-id="c774c-374">Dá se použít k implementaci běžných zásad zpracování chyb.</span><span class="sxs-lookup"><span data-stu-id="c774c-374">Can be used to implement common error handling policies.</span></span>

<span data-ttu-id="c774c-375">Následující ukázkový filtr výjimek používá vlastní zobrazení chyb pro zobrazení podrobností o výjimkách, ke kterým dochází při vývoji aplikace:</span><span class="sxs-lookup"><span data-stu-id="c774c-375">The following sample exception filter uses a custom error view to display details about exceptions that occur when the app is in development:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/CustomExceptionFilter.cs?name=snippet_ExceptionFilter&highlight=16-19)]

<span data-ttu-id="c774c-376">Následující kód testuje filtr výjimky:</span><span class="sxs-lookup"><span data-stu-id="c774c-376">The following code tests the exception filter:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Controllers/FailingController.cs?name=snippet)]

<span data-ttu-id="c774c-377">Filtry výjimek:</span><span class="sxs-lookup"><span data-stu-id="c774c-377">Exception filters:</span></span>

* <span data-ttu-id="c774c-378">Nemusíte mít události před a po.</span><span class="sxs-lookup"><span data-stu-id="c774c-378">Don't have before and after events.</span></span>
* <span data-ttu-id="c774c-379">Implementujte <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> nebo <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span><span class="sxs-lookup"><span data-stu-id="c774c-379">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span></span>
* <span data-ttu-id="c774c-380">Zpracování neošetřených výjimek, ke kterým dochází při vytváření stránky nebo kontroléru Razor, [vázání modelů](xref:mvc/models/model-binding), filtrů akcí nebo metod akcí.</span><span class="sxs-lookup"><span data-stu-id="c774c-380">Handle unhandled exceptions that occur in Razor Page or controller creation, [model binding](xref:mvc/models/model-binding), action filters, or action methods.</span></span>
* <span data-ttu-id="c774c-381">**Nezachycujte** výjimky, ke kterým dochází v filtrech prostředků, výsledných filtrech nebo v důsledku provádění výsledků MVC.</span><span class="sxs-lookup"><span data-stu-id="c774c-381">Do **not** catch exceptions that occur in resource filters, result filters, or MVC result execution.</span></span>

<span data-ttu-id="c774c-382">Chcete-li zpracovat výjimku, nastavte vlastnost <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> na `true` nebo zapište odpověď.</span><span class="sxs-lookup"><span data-stu-id="c774c-382">To handle an exception, set the <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> property to `true` or write a response.</span></span> <span data-ttu-id="c774c-383">Tím se zastaví šíření výjimky.</span><span class="sxs-lookup"><span data-stu-id="c774c-383">This stops propagation of the exception.</span></span> <span data-ttu-id="c774c-384">Filtr výjimek nemůže vypnout výjimku na "úspěch".</span><span class="sxs-lookup"><span data-stu-id="c774c-384">An exception filter can't turn an exception into a "success".</span></span> <span data-ttu-id="c774c-385">To může provést pouze filtr akcí.</span><span class="sxs-lookup"><span data-stu-id="c774c-385">Only an action filter can do that.</span></span>

<span data-ttu-id="c774c-386">Filtry výjimek:</span><span class="sxs-lookup"><span data-stu-id="c774c-386">Exception filters:</span></span>

* <span data-ttu-id="c774c-387">Jsou vhodné pro vytváření přesahů výjimek, ke kterým dochází v rámci akcí.</span><span class="sxs-lookup"><span data-stu-id="c774c-387">Are good for trapping exceptions that occur within actions.</span></span>
* <span data-ttu-id="c774c-388">Nejsou tak flexibilní jako middleware při zpracování chyb.</span><span class="sxs-lookup"><span data-stu-id="c774c-388">Are not as flexible as error handling middleware.</span></span>

<span data-ttu-id="c774c-389">Preferovat middleware pro zpracování výjimek.</span><span class="sxs-lookup"><span data-stu-id="c774c-389">Prefer middleware for exception handling.</span></span> <span data-ttu-id="c774c-390">Filtry výjimek použijte pouze v případě, že se zpracování chyb *liší* v závislosti na tom, která metoda Action je volána.</span><span class="sxs-lookup"><span data-stu-id="c774c-390">Use exception filters only where error handling *differs* based on which action method is called.</span></span> <span data-ttu-id="c774c-391">Aplikace může například mít metody akcí pro koncové body rozhraní API i pro zobrazení/HTML.</span><span class="sxs-lookup"><span data-stu-id="c774c-391">For example, an app might have action methods for both API endpoints and for views/HTML.</span></span> <span data-ttu-id="c774c-392">Koncové body rozhraní API mohou vracet informace o chybě jako JSON, zatímco akce na základě zobrazení by mohly vracet chybovou stránku jako HTML.</span><span class="sxs-lookup"><span data-stu-id="c774c-392">The API endpoints could return error information as JSON, while the view-based actions could return an error page as HTML.</span></span>

## <a name="result-filters"></a><span data-ttu-id="c774c-393">Filtry výsledků</span><span class="sxs-lookup"><span data-stu-id="c774c-393">Result filters</span></span>

<span data-ttu-id="c774c-394">Filtry výsledků:</span><span class="sxs-lookup"><span data-stu-id="c774c-394">Result filters:</span></span>

* <span data-ttu-id="c774c-395">Implementace rozhraní:</span><span class="sxs-lookup"><span data-stu-id="c774c-395">Implement an interface:</span></span>
  * <span data-ttu-id="c774c-396"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> Nebo <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="c774c-396"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
  * <span data-ttu-id="c774c-397"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> Nebo <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span><span class="sxs-lookup"><span data-stu-id="c774c-397"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span></span>
* <span data-ttu-id="c774c-398">Jejich spuštění obklopuje provádění výsledků akcí.</span><span class="sxs-lookup"><span data-stu-id="c774c-398">Their execution surrounds the execution of action results.</span></span>

### <a name="iresultfilter-and-iasyncresultfilter"></a><span data-ttu-id="c774c-399">IResultFilter a IAsyncResultFilter</span><span class="sxs-lookup"><span data-stu-id="c774c-399">IResultFilter and IAsyncResultFilter</span></span>

<span data-ttu-id="c774c-400">Následující kód ukazuje filtr výsledků, který přidá hlavičku protokolu HTTP:</span><span class="sxs-lookup"><span data-stu-id="c774c-400">The following code shows a result filter that adds an HTTP header:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="c774c-401">Typ výsledku, který se má provést, závisí na akci.</span><span class="sxs-lookup"><span data-stu-id="c774c-401">The kind of result being executed depends on the action.</span></span> <span data-ttu-id="c774c-402">Akce vracející zobrazení zahrnuje všechny zpracování Razor jako součást spouštěného <xref:Microsoft.AspNetCore.Mvc.ViewResult>.</span><span class="sxs-lookup"><span data-stu-id="c774c-402">An action returning a view includes all razor processing as part of the <xref:Microsoft.AspNetCore.Mvc.ViewResult> being executed.</span></span> <span data-ttu-id="c774c-403">Metoda rozhraní API může provést určitou serializaci v rámci provádění výsledku.</span><span class="sxs-lookup"><span data-stu-id="c774c-403">An API method might perform some serialization as part of the execution of the result.</span></span> <span data-ttu-id="c774c-404">Přečtěte si další informace o [výsledcích akcí](xref:mvc/controllers/actions).</span><span class="sxs-lookup"><span data-stu-id="c774c-404">Learn more about [action results](xref:mvc/controllers/actions).</span></span>

<span data-ttu-id="c774c-405">Filtry výsledků se spustí pouze v případě, že akce nebo filtr akcí vytvoří výsledek akce.</span><span class="sxs-lookup"><span data-stu-id="c774c-405">Result filters are only executed when an action or action filter produces an action result.</span></span> <span data-ttu-id="c774c-406">Filtry výsledků nejsou provedeny v těchto případech:</span><span class="sxs-lookup"><span data-stu-id="c774c-406">Result filters are not executed when:</span></span>

* <span data-ttu-id="c774c-407">Filtr autorizace nebo filtr prostředků pro krátké okruhy kanálu.</span><span class="sxs-lookup"><span data-stu-id="c774c-407">An authorization filter or resource filter short-circuits the pipeline.</span></span>
* <span data-ttu-id="c774c-408">Filtr výjimek zpracovává výjimku tím, že vyprodukuje výsledek akce.</span><span class="sxs-lookup"><span data-stu-id="c774c-408">An exception filter handles an exception by producing an action result.</span></span>

<span data-ttu-id="c774c-409">Metoda <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> může krátkodobé vykonání výsledku akce a následných filtrů výsledků nastavením <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> na `true`.</span><span class="sxs-lookup"><span data-stu-id="c774c-409">The <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> method can short-circuit execution of the action result and subsequent result filters by setting <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> to `true`.</span></span> <span data-ttu-id="c774c-410">Zapište do objektu Response v případě krátkého okruhu, abyste se vyhnuli vygenerování prázdné odpovědi.</span><span class="sxs-lookup"><span data-stu-id="c774c-410">Write to the response object when short-circuiting to avoid generating an empty response.</span></span> <span data-ttu-id="c774c-411">Vyvolání výjimky v `IResultFilter.OnResultExecuting`:</span><span class="sxs-lookup"><span data-stu-id="c774c-411">Throwing an exception in `IResultFilter.OnResultExecuting`:</span></span>

* <span data-ttu-id="c774c-412">Zabraňuje spuštění výsledků akce a následných filtrů.</span><span class="sxs-lookup"><span data-stu-id="c774c-412">Prevents execution of the action result and subsequent filters.</span></span>
* <span data-ttu-id="c774c-413">Se považuje za selhání namísto úspěšného výsledku.</span><span class="sxs-lookup"><span data-stu-id="c774c-413">Is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="c774c-414">Při spuštění metody <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> je odpověď pravděpodobně již odeslána klientovi.</span><span class="sxs-lookup"><span data-stu-id="c774c-414">When the <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> method runs, the response has probably already been sent to the client.</span></span> <span data-ttu-id="c774c-415">Pokud byla odpověď již odeslána klientovi, nelze ji změnit.</span><span class="sxs-lookup"><span data-stu-id="c774c-415">If the response has already been sent to the client, it cannot be changed.</span></span>

<span data-ttu-id="c774c-416">`ResultExecutedContext.Canceled` je nastavená na `true`, pokud je spuštění výsledku akce od sebe krátkým jiným filtrem.</span><span class="sxs-lookup"><span data-stu-id="c774c-416">`ResultExecutedContext.Canceled` is set to `true` if the action result execution was short-circuited by another filter.</span></span>

<span data-ttu-id="c774c-417">`ResultExecutedContext.Exception` je nastavena na hodnotu jinou než null, pokud výsledek akce nebo následný filtr výsledků vyvolal výjimku.</span><span class="sxs-lookup"><span data-stu-id="c774c-417">`ResultExecutedContext.Exception` is set to a non-null value if the action result or a subsequent result filter threw an exception.</span></span> <span data-ttu-id="c774c-418">Nastavení `Exception` na hodnotu null efektivně zpracovává výjimku a zabraňuje vyvolání výjimky později v kanálu.</span><span class="sxs-lookup"><span data-stu-id="c774c-418">Setting `Exception` to null effectively handles an exception and prevents the exception from being thrown again later in the pipeline.</span></span> <span data-ttu-id="c774c-419">Neexistuje žádný spolehlivý způsob, jak zapisovat data do odpovědi při zpracování výjimky ve filtru výsledků.</span><span class="sxs-lookup"><span data-stu-id="c774c-419">There is no reliable way to write data to a response when handling an exception in a result filter.</span></span> <span data-ttu-id="c774c-420">Pokud byly hlavičky vyprázdněny do klienta, pokud výsledek akce vyvolá výjimku, neexistuje žádný spolehlivý mechanismus pro odeslání kódu chyby.</span><span class="sxs-lookup"><span data-stu-id="c774c-420">If the headers have been flushed to the client when an action result throws an exception, there's no reliable mechanism to send a failure code.</span></span>

<span data-ttu-id="c774c-421">U <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>volání `await next` na <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> provede všechny následné filtry výsledků a výsledek akce.</span><span class="sxs-lookup"><span data-stu-id="c774c-421">For an <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, a call to `await next` on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> executes any subsequent result filters and the action result.</span></span> <span data-ttu-id="c774c-422">Do krátkodobého okruhu nastavte [ResultExecutingContext. Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) na `true` a nevolejte `ResultExecutionDelegate`:</span><span class="sxs-lookup"><span data-stu-id="c774c-422">To short-circuit, set [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) to `true` and don't call the `ResultExecutionDelegate`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MyAsyncResponseFilter.cs?name=snippet)]

<span data-ttu-id="c774c-423">Rozhraní poskytuje abstraktní `ResultFilterAttribute`, které mohou být roztříděné.</span><span class="sxs-lookup"><span data-stu-id="c774c-423">The framework provides an abstract `ResultFilterAttribute` that can be subclassed.</span></span> <span data-ttu-id="c774c-424">Dříve uvedená třída [AddHeaderAttribute](#add-header-attribute) je příkladem atributu filtru výsledků.</span><span class="sxs-lookup"><span data-stu-id="c774c-424">The [AddHeaderAttribute](#add-header-attribute) class shown previously is an example of a result filter attribute.</span></span>

### <a name="ialwaysrunresultfilter-and-iasyncalwaysrunresultfilter"></a><span data-ttu-id="c774c-425">IAlwaysRunResultFilter a IAsyncAlwaysRunResultFilter</span><span class="sxs-lookup"><span data-stu-id="c774c-425">IAlwaysRunResultFilter and IAsyncAlwaysRunResultFilter</span></span>

<span data-ttu-id="c774c-426">Rozhraní <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> a <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> deklarují <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> implementaci, která se spouští pro všechny výsledky akcí.</span><span class="sxs-lookup"><span data-stu-id="c774c-426">The <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> interfaces declare an <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> implementation that runs for all action results.</span></span> <span data-ttu-id="c774c-427">To zahrnuje výsledky akcí, které vytvořil:</span><span class="sxs-lookup"><span data-stu-id="c774c-427">This includes action results produced by:</span></span>

* <span data-ttu-id="c774c-428">Filtry autorizace a filtry prostředků, které mají krátký okruh.</span><span class="sxs-lookup"><span data-stu-id="c774c-428">Authorization filters and resource filters that short-circuit.</span></span>
* <span data-ttu-id="c774c-429">Filtry výjimek.</span><span class="sxs-lookup"><span data-stu-id="c774c-429">Exception filters.</span></span>

<span data-ttu-id="c774c-430">Například následující filtr vždy spustí a nastaví výsledek akce (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) s *422 nezpracovaným* stavovým kódem entity, pokud se nezdaří vyjednávání obsahu:</span><span class="sxs-lookup"><span data-stu-id="c774c-430">For example, the following filter always runs and sets an action result (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) with a *422 Unprocessable Entity* status code when content negotiation fails:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/UnprocessableResultFilter.cs?name=snippet)]

### <a name="ifilterfactory"></a><span data-ttu-id="c774c-431">IFilterFactory</span><span class="sxs-lookup"><span data-stu-id="c774c-431">IFilterFactory</span></span>

<span data-ttu-id="c774c-432"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implementuje <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span><span class="sxs-lookup"><span data-stu-id="c774c-432"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span></span> <span data-ttu-id="c774c-433">Proto může být instance `IFilterFactory` použita jako instance `IFilterMetadata` kdekoli v kanálu filtru.</span><span class="sxs-lookup"><span data-stu-id="c774c-433">Therefore, an `IFilterFactory` instance can be used as an `IFilterMetadata` instance anywhere in the filter pipeline.</span></span> <span data-ttu-id="c774c-434">Když se modul runtime připraví k vyvolání filtru, pokusí se ho přetypovat na `IFilterFactory`.</span><span class="sxs-lookup"><span data-stu-id="c774c-434">When the runtime prepares to invoke the filter, it attempts to cast it to an `IFilterFactory`.</span></span> <span data-ttu-id="c774c-435">Pokud je toto přetypování úspěšné, je volána metoda <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> pro vytvoření instance `IFilterMetadata`, která je vyvolána.</span><span class="sxs-lookup"><span data-stu-id="c774c-435">If that cast succeeds, the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method is called to create the `IFilterMetadata` instance that is invoked.</span></span> <span data-ttu-id="c774c-436">To poskytuje flexibilní návrh, protože přesný kanál filtru není nutné nastavit explicitně při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="c774c-436">This provides a flexible design, since the precise filter pipeline doesn't need to be set explicitly when the app starts.</span></span>

<span data-ttu-id="c774c-437">`IFilterFactory` lze implementovat pomocí implementace vlastních atributů jako jiný přístup k vytváření filtrů:</span><span class="sxs-lookup"><span data-stu-id="c774c-437">`IFilterFactory` can be implemented using custom attribute implementations as another approach to creating filters:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/AddHeaderWithFactoryAttribute.cs?name=snippet_IFilterFactory&highlight=1,4,5,6,7)]

<span data-ttu-id="c774c-438">Filtr je použit v následujícím kódu:</span><span class="sxs-lookup"><span data-stu-id="c774c-438">The filter is applied in the following code:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet3&highlight=21)]

<span data-ttu-id="c774c-439">Otestujte předchozí kód spuštěním [ukázky stahování](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample):</span><span class="sxs-lookup"><span data-stu-id="c774c-439">Test the preceding code by running the [download sample](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample):</span></span>

* <span data-ttu-id="c774c-440">Vyvolejte vývojářské nástroje F12.</span><span class="sxs-lookup"><span data-stu-id="c774c-440">Invoke the F12 developer tools.</span></span>
* <span data-ttu-id="c774c-441">Přejděte na adresu `https://localhost:5001/Sample/HeaderWithFactory`.</span><span class="sxs-lookup"><span data-stu-id="c774c-441">Navigate to `https://localhost:5001/Sample/HeaderWithFactory`.</span></span>

<span data-ttu-id="c774c-442">Vývojářské nástroje F12 zobrazují následující hlavičky odpovědí přidané ukázkovým kódem:</span><span class="sxs-lookup"><span data-stu-id="c774c-442">The F12 developer tools display the following response headers added by the sample code:</span></span>

* <span data-ttu-id="c774c-443">**Autor:** `Rick Anderson`</span><span class="sxs-lookup"><span data-stu-id="c774c-443">**author:** `Rick Anderson`</span></span>
* <span data-ttu-id="c774c-444">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span><span class="sxs-lookup"><span data-stu-id="c774c-444">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span></span>
* <span data-ttu-id="c774c-445">**interní:** `My header`</span><span class="sxs-lookup"><span data-stu-id="c774c-445">**internal:** `My header`</span></span>

<span data-ttu-id="c774c-446">Předchozí kód vytvoří hlavičku **interního:** `My header` Response.</span><span class="sxs-lookup"><span data-stu-id="c774c-446">The preceding code creates the **internal:** `My header` response header.</span></span>

### <a name="ifilterfactory-implemented-on-an-attribute"></a><span data-ttu-id="c774c-447">IFilterFactory implementované u atributu</span><span class="sxs-lookup"><span data-stu-id="c774c-447">IFilterFactory implemented on an attribute</span></span>

<!-- Review 
This section needs to be rewritten.
What's a non-named attribute?
-->

<span data-ttu-id="c774c-448">Filtry, které implementují `IFilterFactory`, jsou užitečné pro filtry, které:</span><span class="sxs-lookup"><span data-stu-id="c774c-448">Filters that implement `IFilterFactory` are useful for filters that:</span></span>

* <span data-ttu-id="c774c-449">Nevyžadovat předávání parametrů</span><span class="sxs-lookup"><span data-stu-id="c774c-449">Don't require passing parameters.</span></span>
* <span data-ttu-id="c774c-450">Musí mít závislosti konstruktoru, které musí vyplnit DI.</span><span class="sxs-lookup"><span data-stu-id="c774c-450">Have constructor dependencies that need to be filled by DI.</span></span>

<span data-ttu-id="c774c-451"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implementuje <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span><span class="sxs-lookup"><span data-stu-id="c774c-451"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="c774c-452">`IFilterFactory` zpřístupňuje <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> metodu pro vytvoření instance <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span><span class="sxs-lookup"><span data-stu-id="c774c-452">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="c774c-453">`CreateInstance` načte zadaný typ z kontejneru Services (DI).</span><span class="sxs-lookup"><span data-stu-id="c774c-453">`CreateInstance` loads the specified type from the services container (DI).</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/SampleActionFilterAttribute.cs?name=snippet_TypeFilterAttribute&highlight=1,3,7)]

<span data-ttu-id="c774c-454">Následující kód ukazuje tři přístupy k použití `[SampleActionFilter]`:</span><span class="sxs-lookup"><span data-stu-id="c774c-454">The following code shows three approaches to applying the `[SampleActionFilter]`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet&highlight=1)]

<span data-ttu-id="c774c-455">V předchozím kódu upravení metoda s `[SampleActionFilter]` je upřednostňovaným přístupem k použití `SampleActionFilter`.</span><span class="sxs-lookup"><span data-stu-id="c774c-455">In the preceding code, decorating the method with `[SampleActionFilter]` is the preferred approach to applying the `SampleActionFilter`.</span></span>

## <a name="using-middleware-in-the-filter-pipeline"></a><span data-ttu-id="c774c-456">Použití middlewaru v kanálu filtru</span><span class="sxs-lookup"><span data-stu-id="c774c-456">Using middleware in the filter pipeline</span></span>

<span data-ttu-id="c774c-457">Filtry prostředků fungují jako [middleware](xref:fundamentals/middleware/index) v tom, že obklopují provádění všech položek, které jsou později v kanálu.</span><span class="sxs-lookup"><span data-stu-id="c774c-457">Resource filters work like [middleware](xref:fundamentals/middleware/index) in that they surround the execution of everything that comes later in the pipeline.</span></span> <span data-ttu-id="c774c-458">Filtry se ale liší od middlewaru v tom, že jsou součástí modulu runtime, což znamená, že mají přístup k kontextu a sestavám.</span><span class="sxs-lookup"><span data-stu-id="c774c-458">But filters differ from middleware in that they're part of the runtime, which means that they have access to context and constructs.</span></span>

<span data-ttu-id="c774c-459">Chcete-li použít middleware jako filtr, vytvořte typ s `Configure` metodou, která určuje middleware pro vložení do kanálu filtru.</span><span class="sxs-lookup"><span data-stu-id="c774c-459">To use middleware as a filter, create a type with a `Configure` method that specifies the middleware to inject into the filter pipeline.</span></span> <span data-ttu-id="c774c-460">Následující příklad používá middleware Localization k vytvoření aktuální jazykové verze pro požadavek:</span><span class="sxs-lookup"><span data-stu-id="c774c-460">The following example uses the localization middleware to establish the current culture for a request:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LocalizationPipeline.cs?name=snippet_MiddlewareFilter&highlight=3,22)]

<span data-ttu-id="c774c-461">Pomocí <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> spusťte middleware:</span><span class="sxs-lookup"><span data-stu-id="c774c-461">Use the <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> to run the middleware:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_MiddlewareFilter&highlight=2)]

<span data-ttu-id="c774c-462">Filtry middlewaru jsou spouštěny ve stejné fázi kanálu filtru jako filtry prostředků, před vazbami modelů a po zbytek kanálu.</span><span class="sxs-lookup"><span data-stu-id="c774c-462">Middleware filters run at the same stage of the filter pipeline as Resource filters, before model binding and after the rest of the pipeline.</span></span>

## <a name="next-actions"></a><span data-ttu-id="c774c-463">Další akce</span><span class="sxs-lookup"><span data-stu-id="c774c-463">Next actions</span></span>

* <span data-ttu-id="c774c-464">Viz [metody filtru pro Razor Pages](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="c774c-464">See [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>
* <span data-ttu-id="c774c-465">Pro experimentování s filtry, [stažení, otestování a úpravy ukázky GitHubu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample).</span><span class="sxs-lookup"><span data-stu-id="c774c-465">To experiment with filters, [download, test, and modify the GitHub sample](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="c774c-466">[Kirka Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Dykstra](https://github.com/tdykstra/)a [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="c774c-466">By [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="c774c-467">*Filtry* v ASP.NET Core umožňují spuštění kódu před nebo po určitých fázích v kanálu zpracování požadavků.</span><span class="sxs-lookup"><span data-stu-id="c774c-467">*Filters* in ASP.NET Core allow code to be run before or after specific stages in the request processing pipeline.</span></span>

<span data-ttu-id="c774c-468">Předdefinované filtry zpracovávají úlohy, jako například:</span><span class="sxs-lookup"><span data-stu-id="c774c-468">Built-in filters handle tasks such as:</span></span>

* <span data-ttu-id="c774c-469">Autorizace (zabránění přístupu k prostředkům, ke kterým uživatel nemá oprávnění).</span><span class="sxs-lookup"><span data-stu-id="c774c-469">Authorization (preventing access to resources a user isn't authorized for).</span></span>
* <span data-ttu-id="c774c-470">Ukládání odpovědí do mezipaměti (při krátkém okruhu kanálu požadavků, který vrátí odpověď uloženou v mezipaměti).</span><span class="sxs-lookup"><span data-stu-id="c774c-470">Response caching (short-circuiting the request pipeline to return a cached response).</span></span>

<span data-ttu-id="c774c-471">Vlastní filtry je možné vytvořit pro zpracování vzájemně se týkajících se otázek.</span><span class="sxs-lookup"><span data-stu-id="c774c-471">Custom filters can be created to handle cross-cutting concerns.</span></span> <span data-ttu-id="c774c-472">Mezi obavy mezi průřezy patří zpracování chyb, ukládání do mezipaměti, konfigurace, autorizace a protokolování.</span><span class="sxs-lookup"><span data-stu-id="c774c-472">Examples of cross-cutting concerns include error handling, caching, configuration, authorization, and logging.</span></span>  <span data-ttu-id="c774c-473">Filtry zabraňují duplikování kódu.</span><span class="sxs-lookup"><span data-stu-id="c774c-473">Filters avoid duplicating code.</span></span> <span data-ttu-id="c774c-474">Například filtr výjimek zpracování chyb může konsolidovat zpracování chyb.</span><span class="sxs-lookup"><span data-stu-id="c774c-474">For example, an error handling exception filter could consolidate error handling.</span></span>

<span data-ttu-id="c774c-475">Tento dokument se týká Razor Pages, řadičů rozhraní API a řadičů se zobrazeními.</span><span class="sxs-lookup"><span data-stu-id="c774c-475">This document applies to Razor Pages, API controllers, and controllers with views.</span></span>

<span data-ttu-id="c774c-476">[Zobrazit nebo Stáhnout ukázku](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample) ([Jak stáhnout](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="c774c-476">[View or download sample](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="how-filters-work"></a><span data-ttu-id="c774c-477">Jak fungují filtry</span><span class="sxs-lookup"><span data-stu-id="c774c-477">How filters work</span></span>

<span data-ttu-id="c774c-478">Filtry se spouštějí v *kanálu vyvolání akce ASP.NET Core*, někdy označované jako *kanál filtru*.</span><span class="sxs-lookup"><span data-stu-id="c774c-478">Filters run within the *ASP.NET Core action invocation pipeline*, sometimes referred to as the *filter pipeline*.</span></span>  <span data-ttu-id="c774c-479">Kanál filtru se spustí po ASP.NET Core vybere akci, která se má provést.</span><span class="sxs-lookup"><span data-stu-id="c774c-479">The filter pipeline runs after ASP.NET Core selects the action to execute.</span></span>

![Požadavek se zpracovává prostřednictvím jiného middlewaru, middleware směrování, výběru akcí a kanálu vyvolání akce ASP.NET Core.](filters/_static/filter-pipeline-1.png)

### <a name="filter-types"></a><span data-ttu-id="c774c-482">Typy filtrů</span><span class="sxs-lookup"><span data-stu-id="c774c-482">Filter types</span></span>

<span data-ttu-id="c774c-483">Každý typ filtru se spustí v jiné fázi kanálu filtru:</span><span class="sxs-lookup"><span data-stu-id="c774c-483">Each filter type is executed at a different stage in the filter pipeline:</span></span>

* <span data-ttu-id="c774c-484">[Filtry autorizace](#authorization-filters) se spouštějí jako první a používají se k určení, jestli je uživatel autorizovaný pro požadavek.</span><span class="sxs-lookup"><span data-stu-id="c774c-484">[Authorization filters](#authorization-filters) run first and are used to determine whether the user is authorized for the request.</span></span> <span data-ttu-id="c774c-485">Filtry autorizace pro krátkodobé vynechání kanálu, pokud je požadavek neautorizovaný</span><span class="sxs-lookup"><span data-stu-id="c774c-485">Authorization filters short-circuit the pipeline if the request is unauthorized.</span></span>

* <span data-ttu-id="c774c-486">[Filtry prostředků](#resource-filters):</span><span class="sxs-lookup"><span data-stu-id="c774c-486">[Resource filters](#resource-filters):</span></span>

  * <span data-ttu-id="c774c-487">Spusťte po autorizaci.</span><span class="sxs-lookup"><span data-stu-id="c774c-487">Run after authorization.</span></span>  
  * <span data-ttu-id="c774c-488"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> může spustit kód před zbytek kanálu filtru.</span><span class="sxs-lookup"><span data-stu-id="c774c-488"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> can run code before the rest of the filter pipeline.</span></span> <span data-ttu-id="c774c-489">`OnResourceExecuting` například může spustit kód před vytvořením vazby modelu.</span><span class="sxs-lookup"><span data-stu-id="c774c-489">For example, `OnResourceExecuting` can run code before model binding.</span></span>
  * <span data-ttu-id="c774c-490"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> může po dokončení zbývající části kanálu spustit kód.</span><span class="sxs-lookup"><span data-stu-id="c774c-490"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> can run code after the rest of the pipeline has completed.</span></span>

* <span data-ttu-id="c774c-491">[Filtry akcí](#action-filters) mohou spustit kód bezprostředně před a po volání jednotlivé metody akce.</span><span class="sxs-lookup"><span data-stu-id="c774c-491">[Action filters](#action-filters) can run code immediately before and after an individual action method is called.</span></span> <span data-ttu-id="c774c-492">Lze je použít k manipulaci s argumenty předaných do akce a výsledek vráceného z akce.</span><span class="sxs-lookup"><span data-stu-id="c774c-492">They can be used to manipulate the arguments passed into an action and the result returned from the action.</span></span> <span data-ttu-id="c774c-493">Filtry **akcí nejsou v Razor Pages podporovány.**</span><span class="sxs-lookup"><span data-stu-id="c774c-493">Action filters are **not** supported in Razor Pages.</span></span>

* <span data-ttu-id="c774c-494">[Filtry výjimek](#exception-filters) slouží k aplikování globálních zásad na neošetřené výjimky, ke kterým dojde před zapsáním cokoli do těla odpovědi.</span><span class="sxs-lookup"><span data-stu-id="c774c-494">[Exception filters](#exception-filters) are used to apply global policies to unhandled exceptions that occur before anything has been written to the response body.</span></span>

* <span data-ttu-id="c774c-495">[Filtry výsledků](#result-filters) můžou spustit kód hned před a po provedení jednotlivých výsledků akce.</span><span class="sxs-lookup"><span data-stu-id="c774c-495">[Result filters](#result-filters) can run code immediately before and after the execution of individual action results.</span></span> <span data-ttu-id="c774c-496">Spustí se pouze v případě, že metoda akce byla úspěšně provedena.</span><span class="sxs-lookup"><span data-stu-id="c774c-496">They run only when the action method has executed successfully.</span></span> <span data-ttu-id="c774c-497">Jsou užitečné pro logiku, která musí obklopit zobrazení nebo formátovací modul.</span><span class="sxs-lookup"><span data-stu-id="c774c-497">They are useful for logic that must surround view or formatter execution.</span></span>

<span data-ttu-id="c774c-498">Následující diagram znázorňuje způsob interakce typů filtrů v kanálu filtru.</span><span class="sxs-lookup"><span data-stu-id="c774c-498">The following diagram shows how filter types interact in the filter pipeline.</span></span>

![Požadavek se zpracovává pomocí autorizačních filtrů, filtrů prostředků, vazeb modelů, filtrů akcí, provádění akcí a konverze výsledků akcí, filtrů výjimek, výsledných filtrů a provádění výsledků.](filters/_static/filter-pipeline-2.png)

## <a name="implementation"></a><span data-ttu-id="c774c-501">Implementace</span><span class="sxs-lookup"><span data-stu-id="c774c-501">Implementation</span></span>

<span data-ttu-id="c774c-502">Filtry podporují synchronní i asynchronní implementace prostřednictvím různých definic rozhraní.</span><span class="sxs-lookup"><span data-stu-id="c774c-502">Filters support both synchronous and asynchronous implementations through different interface definitions.</span></span>

<span data-ttu-id="c774c-503">Synchronní filtry mohou spustit kód před (`On-Stage-Executing`) a po (`On-Stage-Executed`) jejich fáze zřetězení.</span><span class="sxs-lookup"><span data-stu-id="c774c-503">Synchronous filters can run code before (`On-Stage-Executing`) and after (`On-Stage-Executed`) their pipeline stage.</span></span> <span data-ttu-id="c774c-504">Například `OnActionExecuting` je volána před voláním metody Action.</span><span class="sxs-lookup"><span data-stu-id="c774c-504">For example, `OnActionExecuting` is called before the action method is called.</span></span> <span data-ttu-id="c774c-505">`OnActionExecuted` se volá po návratu metody Action.</span><span class="sxs-lookup"><span data-stu-id="c774c-505">`OnActionExecuted` is called after the action method returns.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="c774c-506">Asynchronní filtry definují metodu `On-Stage-ExecutionAsync`:</span><span class="sxs-lookup"><span data-stu-id="c774c-506">Asynchronous filters define an `On-Stage-ExecutionAsync` method:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/SampleAsyncActionFilter.cs?name=snippet)]

<span data-ttu-id="c774c-507">V předchozím kódu `SampleAsyncActionFilter` má <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`), který provádí metodu Action.</span><span class="sxs-lookup"><span data-stu-id="c774c-507">In the preceding code, the `SampleAsyncActionFilter` has an <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`)  that executes the action method.</span></span>  <span data-ttu-id="c774c-508">Každá z metod `On-Stage-ExecutionAsync` převezme `FilterType-ExecutionDelegate`, který provádí fázi zřetězení filtru.</span><span class="sxs-lookup"><span data-stu-id="c774c-508">Each of the `On-Stage-ExecutionAsync` methods take a `FilterType-ExecutionDelegate` that executes the filter's pipeline stage.</span></span>

### <a name="multiple-filter-stages"></a><span data-ttu-id="c774c-509">Několik fází filtru</span><span class="sxs-lookup"><span data-stu-id="c774c-509">Multiple filter stages</span></span>

<span data-ttu-id="c774c-510">Rozhraní pro více fází filtru lze implementovat v rámci jedné třídy.</span><span class="sxs-lookup"><span data-stu-id="c774c-510">Interfaces for multiple filter stages can be implemented in a single class.</span></span> <span data-ttu-id="c774c-511">Například třída <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> implementuje `IActionFilter`, `IResultFilter`a jejich asynchronní ekvivalenty.</span><span class="sxs-lookup"><span data-stu-id="c774c-511">For example, the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> class implements `IActionFilter`, `IResultFilter`, and their async equivalents.</span></span>

<span data-ttu-id="c774c-512">Implementujte **buď** synchronní, nebo asynchronní verzi rozhraní filtru, **nikoli** obojí.</span><span class="sxs-lookup"><span data-stu-id="c774c-512">Implement **either** the synchronous or the async version of a filter interface, **not** both.</span></span> <span data-ttu-id="c774c-513">Modul runtime nejprve kontroluje, zda filtr implementuje asynchronní rozhraní a v takovém případě jej zavolá.</span><span class="sxs-lookup"><span data-stu-id="c774c-513">The runtime checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="c774c-514">V takovém případě volá metody synchronního rozhraní.</span><span class="sxs-lookup"><span data-stu-id="c774c-514">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="c774c-515">Pokud jsou asynchronní i synchronní rozhraní implementovány v jedné třídě, je volána pouze asynchronní metoda.</span><span class="sxs-lookup"><span data-stu-id="c774c-515">If both asynchronous and synchronous interfaces are implemented in one class, only the async method is called.</span></span> <span data-ttu-id="c774c-516">Při použití abstraktních tříd, jako <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>, přepište pouze synchronní metody nebo asynchronní metodu pro každý typ filtru.</span><span class="sxs-lookup"><span data-stu-id="c774c-516">When using abstract classes like <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> override only the synchronous methods or the async method for each filter type.</span></span>

### <a name="built-in-filter-attributes"></a><span data-ttu-id="c774c-517">Předdefinované atributy filtru</span><span class="sxs-lookup"><span data-stu-id="c774c-517">Built-in filter attributes</span></span>

<span data-ttu-id="c774c-518">ASP.NET Core obsahuje integrované filtry založené na atributech, které mohou být roztříděné a přizpůsobené.</span><span class="sxs-lookup"><span data-stu-id="c774c-518">ASP.NET Core includes built-in attribute-based filters that can be subclassed and customized.</span></span> <span data-ttu-id="c774c-519">Například následující filtr výsledků přidá hlavičku do odpovědi:</span><span class="sxs-lookup"><span data-stu-id="c774c-519">For example, the following result filter adds a header to the response:</span></span>

<a name="add-header-attribute"></a>

[!code-csharp[](./filters/sample/FiltersSample/Filters/AddHeaderAttribute.cs?name=snippet)]

<span data-ttu-id="c774c-520">Atributy umožňují filtrům přijímat argumenty, jak je znázorněno v předchozím příkladu.</span><span class="sxs-lookup"><span data-stu-id="c774c-520">Attributes allow filters to accept arguments, as shown in the preceding example.</span></span> <span data-ttu-id="c774c-521">Použijte `AddHeaderAttribute` pro metodu Controller nebo Action a zadejte název a hodnotu hlavičky HTTP:</span><span class="sxs-lookup"><span data-stu-id="c774c-521">Apply the `AddHeaderAttribute` to a controller or action method and specify the name and value of the HTTP header:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

<!-- `https://localhost:5001/Sample` -->

<span data-ttu-id="c774c-522">Některé z rozhraní filtru mají odpovídající atributy, které lze použít jako základní třídy pro vlastní implementace.</span><span class="sxs-lookup"><span data-stu-id="c774c-522">Several of the filter interfaces have corresponding attributes that can be used as base classes for custom implementations.</span></span>

<span data-ttu-id="c774c-523">Atributy filtru:</span><span class="sxs-lookup"><span data-stu-id="c774c-523">Filter attributes:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ExceptionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>

## <a name="filter-scopes-and-order-of-execution"></a><span data-ttu-id="c774c-524">Filtrovat obory a pořadí provádění</span><span class="sxs-lookup"><span data-stu-id="c774c-524">Filter scopes and order of execution</span></span>

<span data-ttu-id="c774c-525">Filtr lze přidat do kanálu v jednom ze tří *oborů*:</span><span class="sxs-lookup"><span data-stu-id="c774c-525">A filter can be added to the pipeline at one of three *scopes*:</span></span>

* <span data-ttu-id="c774c-526">Použití atributu pro akci.</span><span class="sxs-lookup"><span data-stu-id="c774c-526">Using an attribute on an action.</span></span>
* <span data-ttu-id="c774c-527">Použití atributu na řadiči.</span><span class="sxs-lookup"><span data-stu-id="c774c-527">Using an attribute on a controller.</span></span>
* <span data-ttu-id="c774c-528">Globálně pro všechny řadiče a akce, jak je znázorněno v následujícím kódu:</span><span class="sxs-lookup"><span data-stu-id="c774c-528">Globally for all controllers and actions as shown in the following code:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/StartupGF.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="c774c-529">Předchozí kód přičítá tři filtry globálně pomocí kolekce [MvcOptions. filters](xref:Microsoft.AspNetCore.Mvc.MvcOptions.Filters) .</span><span class="sxs-lookup"><span data-stu-id="c774c-529">The preceding code adds three filters globally using the [MvcOptions.Filters](xref:Microsoft.AspNetCore.Mvc.MvcOptions.Filters) collection.</span></span>

### <a name="default-order-of-execution"></a><span data-ttu-id="c774c-530">Výchozí pořadí provádění</span><span class="sxs-lookup"><span data-stu-id="c774c-530">Default order of execution</span></span>

<span data-ttu-id="c774c-531">Pokud existuje více filtrů *stejného typu*, určuje obor výchozí pořadí provádění filtru.</span><span class="sxs-lookup"><span data-stu-id="c774c-531">When there are multiple filters *of the same type*, scope determines the default order of filter execution.</span></span>  <span data-ttu-id="c774c-532">Globální filtry filtr tříd Surround.</span><span class="sxs-lookup"><span data-stu-id="c774c-532">Global filters surround class filters.</span></span> <span data-ttu-id="c774c-533">Filtry tříd obklopují filtry metod Surround.</span><span class="sxs-lookup"><span data-stu-id="c774c-533">Class filters surround method filters.</span></span>

<span data-ttu-id="c774c-534">V důsledku vnořování filtru je *po* kódu spuštěn v obráceném pořadí *před* kódem.</span><span class="sxs-lookup"><span data-stu-id="c774c-534">As a result of filter nesting, the *after* code of filters runs in the reverse order of the *before* code.</span></span> <span data-ttu-id="c774c-535">Pořadí filtru:</span><span class="sxs-lookup"><span data-stu-id="c774c-535">The filter sequence:</span></span>

* <span data-ttu-id="c774c-536">*Před* kódem globálních filtrů.</span><span class="sxs-lookup"><span data-stu-id="c774c-536">The *before* code of global filters.</span></span>
  * <span data-ttu-id="c774c-537">*Před* kódem filtru kontroleru.</span><span class="sxs-lookup"><span data-stu-id="c774c-537">The *before* code of controller filters.</span></span>
    * <span data-ttu-id="c774c-538">Kód *před* filtry metody Action.</span><span class="sxs-lookup"><span data-stu-id="c774c-538">The *before* code of action method filters.</span></span>
    * <span data-ttu-id="c774c-539">*Po* kódu filtry metody Action.</span><span class="sxs-lookup"><span data-stu-id="c774c-539">The *after* code of action method filters.</span></span>
  * <span data-ttu-id="c774c-540">*Po* kódu filtry kontroleru.</span><span class="sxs-lookup"><span data-stu-id="c774c-540">The *after* code of controller filters.</span></span>
* <span data-ttu-id="c774c-541">*Po* kódu globálních filtrů.</span><span class="sxs-lookup"><span data-stu-id="c774c-541">The *after* code of global filters.</span></span>
  
<span data-ttu-id="c774c-542">Následující příklad ilustruje pořadí, ve kterém jsou metody filtru volány pro filtry synchronních akcí.</span><span class="sxs-lookup"><span data-stu-id="c774c-542">The following example that illustrates the order in which filter methods are called for synchronous action filters.</span></span>

| <span data-ttu-id="c774c-543">Sequence</span><span class="sxs-lookup"><span data-stu-id="c774c-543">Sequence</span></span> | <span data-ttu-id="c774c-544">Rozsah filtru</span><span class="sxs-lookup"><span data-stu-id="c774c-544">Filter scope</span></span> | <span data-ttu-id="c774c-545">Filter – metoda</span><span class="sxs-lookup"><span data-stu-id="c774c-545">Filter method</span></span> |
|:--------:|:------------:|:-------------:|
| <span data-ttu-id="c774c-546">1</span><span class="sxs-lookup"><span data-stu-id="c774c-546">1</span></span> | <span data-ttu-id="c774c-547">Globální</span><span class="sxs-lookup"><span data-stu-id="c774c-547">Global</span></span> | `OnActionExecuting` |
| <span data-ttu-id="c774c-548">2</span><span class="sxs-lookup"><span data-stu-id="c774c-548">2</span></span> | <span data-ttu-id="c774c-549">Správce</span><span class="sxs-lookup"><span data-stu-id="c774c-549">Controller</span></span> | `OnActionExecuting` |
| <span data-ttu-id="c774c-550">3</span><span class="sxs-lookup"><span data-stu-id="c774c-550">3</span></span> | <span data-ttu-id="c774c-551">Metoda</span><span class="sxs-lookup"><span data-stu-id="c774c-551">Method</span></span> | `OnActionExecuting` |
| <span data-ttu-id="c774c-552">4</span><span class="sxs-lookup"><span data-stu-id="c774c-552">4</span></span> | <span data-ttu-id="c774c-553">Metoda</span><span class="sxs-lookup"><span data-stu-id="c774c-553">Method</span></span> | `OnActionExecuted` |
| <span data-ttu-id="c774c-554">5</span><span class="sxs-lookup"><span data-stu-id="c774c-554">5</span></span> | <span data-ttu-id="c774c-555">Správce</span><span class="sxs-lookup"><span data-stu-id="c774c-555">Controller</span></span> | `OnActionExecuted` |
| <span data-ttu-id="c774c-556">6</span><span class="sxs-lookup"><span data-stu-id="c774c-556">6</span></span> | <span data-ttu-id="c774c-557">Globální</span><span class="sxs-lookup"><span data-stu-id="c774c-557">Global</span></span> | `OnActionExecuted` |

<span data-ttu-id="c774c-558">Tato posloupnost zobrazuje:</span><span class="sxs-lookup"><span data-stu-id="c774c-558">This sequence shows:</span></span>

* <span data-ttu-id="c774c-559">Filtr metod je vnořený do filtru kontroleru.</span><span class="sxs-lookup"><span data-stu-id="c774c-559">The method filter is nested within the controller filter.</span></span>
* <span data-ttu-id="c774c-560">Filtr kontroleru je vnořený do globálního filtru.</span><span class="sxs-lookup"><span data-stu-id="c774c-560">The controller filter is nested within the global filter.</span></span>

### <a name="controller-and-razor-page-level-filters"></a><span data-ttu-id="c774c-561">Filtry na úrovni ovladače a stránky Razor</span><span class="sxs-lookup"><span data-stu-id="c774c-561">Controller and Razor Page level filters</span></span>

<span data-ttu-id="c774c-562">Každý kontroler, který dědí z <xref:Microsoft.AspNetCore.Mvc.Controller> základní třídy, zahrnuje metody [Controller. OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*), [Controller. OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*)a [Controller. OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted`.</span><span class="sxs-lookup"><span data-stu-id="c774c-562">Every controller that inherits from the <xref:Microsoft.AspNetCore.Mvc.Controller> base class includes [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*),  [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*), and [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted` methods.</span></span> <span data-ttu-id="c774c-563">Tyto metody:</span><span class="sxs-lookup"><span data-stu-id="c774c-563">These methods:</span></span>

* <span data-ttu-id="c774c-564">Zabalte filtry, které se spouštějí pro danou akci.</span><span class="sxs-lookup"><span data-stu-id="c774c-564">Wrap the filters that run for a given action.</span></span>
* <span data-ttu-id="c774c-565">`OnActionExecuting` se volá před všemi filtry akce.</span><span class="sxs-lookup"><span data-stu-id="c774c-565">`OnActionExecuting` is called before any of the action's filters.</span></span>
* <span data-ttu-id="c774c-566">`OnActionExecuted` se volá po všech filtrech akcí.</span><span class="sxs-lookup"><span data-stu-id="c774c-566">`OnActionExecuted` is called after all of the action filters.</span></span>
* <span data-ttu-id="c774c-567">`OnActionExecutionAsync` se volá před všemi filtry akce.</span><span class="sxs-lookup"><span data-stu-id="c774c-567">`OnActionExecutionAsync` is called before any of the action's filters.</span></span> <span data-ttu-id="c774c-568">Kód v filtru po `next` spustí po metodě Action.</span><span class="sxs-lookup"><span data-stu-id="c774c-568">Code in the filter after `next` runs after the action method.</span></span>

<span data-ttu-id="c774c-569">Například v ukázce stahování se `MySampleActionFilter` aplikuje globálně při spuštění.</span><span class="sxs-lookup"><span data-stu-id="c774c-569">For example, in the download sample, `MySampleActionFilter` is applied globally in startup.</span></span>

<span data-ttu-id="c774c-570">`TestController`:</span><span class="sxs-lookup"><span data-stu-id="c774c-570">The `TestController`:</span></span>

* <span data-ttu-id="c774c-571">Aplikuje `SampleActionFilterAttribute` (`[SampleActionFilter]`) na akci `FilterTest2`.</span><span class="sxs-lookup"><span data-stu-id="c774c-571">Applies the `SampleActionFilterAttribute` (`[SampleActionFilter]`) to the `FilterTest2` action.</span></span>
* <span data-ttu-id="c774c-572">Přepisuje `OnActionExecuting` a `OnActionExecuted`.</span><span class="sxs-lookup"><span data-stu-id="c774c-572">Overrides `OnActionExecuting` and `OnActionExecuted`.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/TestController.cs?name=snippet)]

<span data-ttu-id="c774c-573">Při přechodu na `https://localhost:5001/Test/FilterTest2` se spustí následující kód:</span><span class="sxs-lookup"><span data-stu-id="c774c-573">Navigating to `https://localhost:5001/Test/FilterTest2` runs the following code:</span></span>

* `TestController.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `SampleActionFilterAttribute.OnActionExecuting`
      * `TestController.FilterTest2`
    * `SampleActionFilterAttribute.OnActionExecuted`
  * `MySampleActionFilter.OnActionExecuted`
* `TestController.OnActionExecuted`

<span data-ttu-id="c774c-574">Razor Pages najdete v tématu [implementace filtrů stránek Razor pomocí přepsání metod filtru](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span><span class="sxs-lookup"><span data-stu-id="c774c-574">For Razor Pages, see [Implement Razor Page filters by overriding filter methods](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span></span>

### <a name="overriding-the-default-order"></a><span data-ttu-id="c774c-575">Přepsání výchozího pořadí</span><span class="sxs-lookup"><span data-stu-id="c774c-575">Overriding the default order</span></span>

<span data-ttu-id="c774c-576">Výchozí sekvenci spuštění lze přepsat implementací <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span><span class="sxs-lookup"><span data-stu-id="c774c-576">The default sequence of execution can be overridden by implementing <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span></span> <span data-ttu-id="c774c-577">`IOrderedFilter` zpřístupňuje vlastnost <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order>, která má přednost před rozsahem, aby bylo možné určit pořadí spouštění.</span><span class="sxs-lookup"><span data-stu-id="c774c-577">`IOrderedFilter` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> property that takes precedence over scope to determine the order of execution.</span></span> <span data-ttu-id="c774c-578">Filtr s nižší hodnotou `Order`:</span><span class="sxs-lookup"><span data-stu-id="c774c-578">A filter with a lower `Order` value:</span></span>

* <span data-ttu-id="c774c-579">Spustí *před* kódem filtr s vyšší hodnotou `Order`.</span><span class="sxs-lookup"><span data-stu-id="c774c-579">Runs the *before* code before that of a filter with a higher value of `Order`.</span></span>
* <span data-ttu-id="c774c-580">Spustí *za* kódem filtru s vyšší `Order` hodnotou.</span><span class="sxs-lookup"><span data-stu-id="c774c-580">Runs the *after* code after that of a filter with a higher `Order` value.</span></span>

<span data-ttu-id="c774c-581">Vlastnost `Order` lze nastavit pomocí parametru konstruktoru:</span><span class="sxs-lookup"><span data-stu-id="c774c-581">The `Order` property can be set with a constructor parameter:</span></span>

```csharp
[MyFilter(Name = "Controller Level Attribute", Order=1)]
```

<span data-ttu-id="c774c-582">Vezměte v úvahu stejné 3 filtry akcí, které jsou uvedené v předchozím příkladu.</span><span class="sxs-lookup"><span data-stu-id="c774c-582">Consider the same 3 action filters shown in the preceding example.</span></span> <span data-ttu-id="c774c-583">Je-li vlastnost `Order` řadiče a globálních filtrů nastavena na hodnotu 1 a 2 v uvedeném pořadí, je pořadí spuštění obrácené.</span><span class="sxs-lookup"><span data-stu-id="c774c-583">If the `Order` property of the controller and global filters is set to 1 and 2 respectively, the order of execution is reversed.</span></span>

| <span data-ttu-id="c774c-584">Sequence</span><span class="sxs-lookup"><span data-stu-id="c774c-584">Sequence</span></span> | <span data-ttu-id="c774c-585">Rozsah filtru</span><span class="sxs-lookup"><span data-stu-id="c774c-585">Filter scope</span></span> | <span data-ttu-id="c774c-586">`Order` – vlastnost</span><span class="sxs-lookup"><span data-stu-id="c774c-586">`Order` property</span></span> | <span data-ttu-id="c774c-587">Filter – metoda</span><span class="sxs-lookup"><span data-stu-id="c774c-587">Filter method</span></span> |
|:--------:|:------------:|:-----------------:|:-------------:|
| <span data-ttu-id="c774c-588">1</span><span class="sxs-lookup"><span data-stu-id="c774c-588">1</span></span> | <span data-ttu-id="c774c-589">Metoda</span><span class="sxs-lookup"><span data-stu-id="c774c-589">Method</span></span> | <span data-ttu-id="c774c-590">0</span><span class="sxs-lookup"><span data-stu-id="c774c-590">0</span></span> | `OnActionExecuting` |
| <span data-ttu-id="c774c-591">2</span><span class="sxs-lookup"><span data-stu-id="c774c-591">2</span></span> | <span data-ttu-id="c774c-592">Správce</span><span class="sxs-lookup"><span data-stu-id="c774c-592">Controller</span></span> | <span data-ttu-id="c774c-593">1</span><span class="sxs-lookup"><span data-stu-id="c774c-593">1</span></span>  | `OnActionExecuting` |
| <span data-ttu-id="c774c-594">3</span><span class="sxs-lookup"><span data-stu-id="c774c-594">3</span></span> | <span data-ttu-id="c774c-595">Globální</span><span class="sxs-lookup"><span data-stu-id="c774c-595">Global</span></span> | <span data-ttu-id="c774c-596">2</span><span class="sxs-lookup"><span data-stu-id="c774c-596">2</span></span>  | `OnActionExecuting` |
| <span data-ttu-id="c774c-597">4</span><span class="sxs-lookup"><span data-stu-id="c774c-597">4</span></span> | <span data-ttu-id="c774c-598">Globální</span><span class="sxs-lookup"><span data-stu-id="c774c-598">Global</span></span> | <span data-ttu-id="c774c-599">2</span><span class="sxs-lookup"><span data-stu-id="c774c-599">2</span></span>  | `OnActionExecuted` |
| <span data-ttu-id="c774c-600">5</span><span class="sxs-lookup"><span data-stu-id="c774c-600">5</span></span> | <span data-ttu-id="c774c-601">Správce</span><span class="sxs-lookup"><span data-stu-id="c774c-601">Controller</span></span> | <span data-ttu-id="c774c-602">1</span><span class="sxs-lookup"><span data-stu-id="c774c-602">1</span></span>  | `OnActionExecuted` |
| <span data-ttu-id="c774c-603">6</span><span class="sxs-lookup"><span data-stu-id="c774c-603">6</span></span> | <span data-ttu-id="c774c-604">Metoda</span><span class="sxs-lookup"><span data-stu-id="c774c-604">Method</span></span> | <span data-ttu-id="c774c-605">0</span><span class="sxs-lookup"><span data-stu-id="c774c-605">0</span></span>  | `OnActionExecuted` |

<span data-ttu-id="c774c-606">Vlastnost `Order` Přepisuje obor při určování pořadí, ve kterém jsou filtry spouštěny.</span><span class="sxs-lookup"><span data-stu-id="c774c-606">The `Order` property overrides scope when determining the order in which filters run.</span></span> <span data-ttu-id="c774c-607">Filtry jsou seřazené podle pořadí, pak se k přerušení vztahů používá obor.</span><span class="sxs-lookup"><span data-stu-id="c774c-607">Filters are sorted first by order, then scope is used to break ties.</span></span> <span data-ttu-id="c774c-608">Všechny předdefinované filtry implementují `IOrderedFilter` a nastaví výchozí hodnotu `Order` na 0.</span><span class="sxs-lookup"><span data-stu-id="c774c-608">All of the built-in filters implement `IOrderedFilter` and set the default `Order` value to 0.</span></span> <span data-ttu-id="c774c-609">Pro předdefinované filtry rozsah určuje pořadí, pokud není `Order` nastaveno na nenulovou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="c774c-609">For built-in filters, scope determines order unless `Order` is set to a non-zero value.</span></span>

## <a name="cancellation-and-short-circuiting"></a><span data-ttu-id="c774c-610">Zrušení a zkrácení okruhů</span><span class="sxs-lookup"><span data-stu-id="c774c-610">Cancellation and short-circuiting</span></span>

<span data-ttu-id="c774c-611">Kanál filtru může být v krátkém okruhu nastavením vlastnosti <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> u parametru <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> poskytnutého metodě Filter.</span><span class="sxs-lookup"><span data-stu-id="c774c-611">The filter pipeline can be short-circuited by setting the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> property on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> parameter provided to the filter method.</span></span> <span data-ttu-id="c774c-612">Například následující filtr prostředků zabrání ve spuštění zbývajícího kanálu:</span><span class="sxs-lookup"><span data-stu-id="c774c-612">For instance, the following Resource filter prevents the rest of the pipeline from executing:</span></span>

<a name="short-circuiting-resource-filter"></a>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ShortCircuitingResourceFilterAttribute.cs?name=snippet)]

<span data-ttu-id="c774c-613">V následujícím kódu, jak `ShortCircuitingResourceFilter`, tak filtr `AddHeader` cílovou metodu `SomeResource` akce.</span><span class="sxs-lookup"><span data-stu-id="c774c-613">In the following code, both the `ShortCircuitingResourceFilter` and the `AddHeader` filter target the `SomeResource` action method.</span></span> <span data-ttu-id="c774c-614">`ShortCircuitingResourceFilter`:</span><span class="sxs-lookup"><span data-stu-id="c774c-614">The `ShortCircuitingResourceFilter`:</span></span>

* <span data-ttu-id="c774c-615">Nejprve se spustí, protože se jedná o filtr prostředku a `AddHeader` je filtr akcí.</span><span class="sxs-lookup"><span data-stu-id="c774c-615">Runs first, because it's a Resource Filter and `AddHeader` is an Action Filter.</span></span>
* <span data-ttu-id="c774c-616">Krátké okruhy zbývajícího kanálu.</span><span class="sxs-lookup"><span data-stu-id="c774c-616">Short-circuits the rest of the pipeline.</span></span>

<span data-ttu-id="c774c-617">Proto filtr `AddHeader` pro akci `SomeResource` nikdy neběží.</span><span class="sxs-lookup"><span data-stu-id="c774c-617">Therefore the `AddHeader` filter never runs for the `SomeResource` action.</span></span> <span data-ttu-id="c774c-618">Toto chování by bylo stejné, pokud byly oba filtry aplikovány na úrovni metody akce, a to za předpokladu, že `ShortCircuitingResourceFilter` běžela jako první.</span><span class="sxs-lookup"><span data-stu-id="c774c-618">This behavior would be the same if both filters were applied at the action method level, provided the `ShortCircuitingResourceFilter` ran first.</span></span> <span data-ttu-id="c774c-619">`ShortCircuitingResourceFilter` se nejprve spustí z důvodu jeho typu filtru nebo explicitním použitím vlastnosti `Order`.</span><span class="sxs-lookup"><span data-stu-id="c774c-619">The `ShortCircuitingResourceFilter` runs first because of its filter type, or by explicit use of `Order` property.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1,9)]

## <a name="dependency-injection"></a><span data-ttu-id="c774c-620">Injektáž závislostí</span><span class="sxs-lookup"><span data-stu-id="c774c-620">Dependency injection</span></span>

<span data-ttu-id="c774c-621">Filtry lze přidat podle typu nebo podle instance.</span><span class="sxs-lookup"><span data-stu-id="c774c-621">Filters can be added by type or by instance.</span></span> <span data-ttu-id="c774c-622">Pokud je přidána instance, bude tato instance použita pro každý požadavek.</span><span class="sxs-lookup"><span data-stu-id="c774c-622">If an instance is added, that instance is used for every request.</span></span> <span data-ttu-id="c774c-623">Pokud je přidán typ, je aktivován typ.</span><span class="sxs-lookup"><span data-stu-id="c774c-623">If a type is added, it's type-activated.</span></span> <span data-ttu-id="c774c-624">Filtr aktivovaný typu znamená:</span><span class="sxs-lookup"><span data-stu-id="c774c-624">A type-activated filter means:</span></span>

* <span data-ttu-id="c774c-625">Instance se vytvoří pro každý požadavek.</span><span class="sxs-lookup"><span data-stu-id="c774c-625">An instance is created for each request.</span></span>
* <span data-ttu-id="c774c-626">Jakékoli závislosti konstruktoru jsou vyplněny pomocí [Injektáže závislosti](xref:fundamentals/dependency-injection) (di).</span><span class="sxs-lookup"><span data-stu-id="c774c-626">Any constructor dependencies are populated by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span>

<span data-ttu-id="c774c-627">Filtry, které jsou implementovány jako atributy a přidány přímo do tříd kontroleru nebo metody akcí, nemohou mít závislosti konstruktoru poskytované [vkládáním závislostí](xref:fundamentals/dependency-injection) (di).</span><span class="sxs-lookup"><span data-stu-id="c774c-627">Filters that are implemented as attributes and added directly to controller classes or action methods cannot have constructor dependencies provided by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span> <span data-ttu-id="c774c-628">Pomocí DI nelze poskytnout závislosti konstruktoru:</span><span class="sxs-lookup"><span data-stu-id="c774c-628">Constructor dependencies cannot be provided by DI because:</span></span>

* <span data-ttu-id="c774c-629">V atributech musí být zadány parametry konstruktoru, kde jsou aplikovány.</span><span class="sxs-lookup"><span data-stu-id="c774c-629">Attributes must have their constructor parameters supplied where they're applied.</span></span> 
* <span data-ttu-id="c774c-630">Toto je omezení způsobu fungování atributů.</span><span class="sxs-lookup"><span data-stu-id="c774c-630">This is a limitation of how attributes work.</span></span>

<span data-ttu-id="c774c-631">Následující filtry podporují závislosti konstruktoru poskytované od DI:</span><span class="sxs-lookup"><span data-stu-id="c774c-631">The following filters support constructor dependencies provided from DI:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>
* <span data-ttu-id="c774c-632"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implementované na atribut.</span><span class="sxs-lookup"><span data-stu-id="c774c-632"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implemented on the attribute.</span></span>

<span data-ttu-id="c774c-633">Předchozí filtry lze použít pro metodu kontroleru nebo akce:</span><span class="sxs-lookup"><span data-stu-id="c774c-633">The preceding filters can be applied to a controller or action method:</span></span>

<span data-ttu-id="c774c-634">Protokolovací nástroje jsou k dispozici z DI.</span><span class="sxs-lookup"><span data-stu-id="c774c-634">Loggers are available from DI.</span></span> <span data-ttu-id="c774c-635">Ale nevytvářejte a používejte filtry čistě pro účely protokolování.</span><span class="sxs-lookup"><span data-stu-id="c774c-635">However, avoid creating and using filters purely for logging purposes.</span></span> <span data-ttu-id="c774c-636">[Integrované protokolování rozhraní](xref:fundamentals/logging/index) obvykle zajišťuje, co je potřeba pro protokolování.</span><span class="sxs-lookup"><span data-stu-id="c774c-636">The [built-in framework logging](xref:fundamentals/logging/index) typically provides what's needed for logging.</span></span> <span data-ttu-id="c774c-637">Protokolování přidáno do filtrů:</span><span class="sxs-lookup"><span data-stu-id="c774c-637">Logging added to filters:</span></span>

* <span data-ttu-id="c774c-638">Měl by se soustředit na obavy a chování obchodních domén, které jsou specifické pro filtr.</span><span class="sxs-lookup"><span data-stu-id="c774c-638">Should focus on business domain concerns or behavior specific to the filter.</span></span>
* <span data-ttu-id="c774c-639">Neměli **byste** protokolovat akce nebo jiné události rozhraní.</span><span class="sxs-lookup"><span data-stu-id="c774c-639">Should **not** log actions or other framework events.</span></span> <span data-ttu-id="c774c-640">Vestavěné filtry protokolují akce a události rozhraní.</span><span class="sxs-lookup"><span data-stu-id="c774c-640">The built in filters log actions and framework events.</span></span>

### <a name="servicefilterattribute"></a><span data-ttu-id="c774c-641">ServiceFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="c774c-641">ServiceFilterAttribute</span></span>

<span data-ttu-id="c774c-642">Typy implementace filtru služby jsou registrovány v `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="c774c-642">Service filter implementation types are registered in `ConfigureServices`.</span></span> <span data-ttu-id="c774c-643"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> načte instanci filtru z DI.</span><span class="sxs-lookup"><span data-stu-id="c774c-643">A <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> retrieves an instance of the filter from DI.</span></span>

<span data-ttu-id="c774c-644">Následující kód ukazuje `AddHeaderResultServiceFilter`:</span><span class="sxs-lookup"><span data-stu-id="c774c-644">The following code shows the `AddHeaderResultServiceFilter`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="c774c-645">V následujícím kódu je `AddHeaderResultServiceFilter` přidána do kontejneru DI:</span><span class="sxs-lookup"><span data-stu-id="c774c-645">In the following code, `AddHeaderResultServiceFilter` is added to the DI container:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

<span data-ttu-id="c774c-646">V následujícím kódu atribut `ServiceFilter` načte instanci `AddHeaderResultServiceFilter` filtru z DI:</span><span class="sxs-lookup"><span data-stu-id="c774c-646">In the following code, the `ServiceFilter` attribute retrieves an instance of the `AddHeaderResultServiceFilter` filter from DI:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_ServiceFilter&highlight=1)]

<span data-ttu-id="c774c-647">Při použití `ServiceFilterAttribute`se nastavuje [ServiceFilterAttribute. IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span><span class="sxs-lookup"><span data-stu-id="c774c-647">When using `ServiceFilterAttribute`, setting [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span></span>

* <span data-ttu-id="c774c-648">Poskytuje nápovědu, že se instance filtru *dá* znovu použít mimo rozsah požadavku, který byl vytvořen v rámci.</span><span class="sxs-lookup"><span data-stu-id="c774c-648">Provides a hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="c774c-649">Modul runtime ASP.NET Core nezaručuje:</span><span class="sxs-lookup"><span data-stu-id="c774c-649">The ASP.NET Core runtime doesn't guarantee:</span></span>

  * <span data-ttu-id="c774c-650">Vytvoří se jedna instance filtru.</span><span class="sxs-lookup"><span data-stu-id="c774c-650">That a single instance of the filter will be created.</span></span>
  * <span data-ttu-id="c774c-651">Filtr nebude znovu vyžádán z kontejneru DI v pozdějším bodě.</span><span class="sxs-lookup"><span data-stu-id="c774c-651">The filter will not be re-requested from the DI container at some later point.</span></span>

* <span data-ttu-id="c774c-652">Neměl by se používat s filtrem, který závisí na službách s jinou životností než singleton.</span><span class="sxs-lookup"><span data-stu-id="c774c-652">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

 <span data-ttu-id="c774c-653"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implementuje <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span><span class="sxs-lookup"><span data-stu-id="c774c-653"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="c774c-654">`IFilterFactory` zpřístupňuje <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> metodu pro vytvoření instance <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span><span class="sxs-lookup"><span data-stu-id="c774c-654">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="c774c-655">`CreateInstance` načte zadaný typ z DI.</span><span class="sxs-lookup"><span data-stu-id="c774c-655">`CreateInstance` loads the specified type from DI.</span></span>

### <a name="typefilterattribute"></a><span data-ttu-id="c774c-656">TypeFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="c774c-656">TypeFilterAttribute</span></span>

<span data-ttu-id="c774c-657"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> je podobná <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, ale její typ není vyřešen přímo z kontejneru DI.</span><span class="sxs-lookup"><span data-stu-id="c774c-657"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> is similar to <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, but its type isn't resolved directly from the DI container.</span></span> <span data-ttu-id="c774c-658">Vytvoří instanci typu pomocí <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="c774c-658">It instantiates the type by using <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span></span>

<span data-ttu-id="c774c-659">Vzhledem k tomu, že typy `TypeFilterAttribute` nejsou vyřešeny přímo z kontejneru DI:</span><span class="sxs-lookup"><span data-stu-id="c774c-659">Because `TypeFilterAttribute` types aren't resolved directly from the DI container:</span></span>

* <span data-ttu-id="c774c-660">Typy, na které se odkazuje pomocí `TypeFilterAttribute`, nemusejí být registrovány v kontejneru DI.</span><span class="sxs-lookup"><span data-stu-id="c774c-660">Types that are referenced using the `TypeFilterAttribute` don't need to be registered with the DI container.</span></span>  <span data-ttu-id="c774c-661">Mají své závislosti splněné kontejnerem DI.</span><span class="sxs-lookup"><span data-stu-id="c774c-661">They do have their dependencies fulfilled by the DI container.</span></span>
* <span data-ttu-id="c774c-662">`TypeFilterAttribute` může volitelně přijímat argumenty konstruktoru pro daný typ.</span><span class="sxs-lookup"><span data-stu-id="c774c-662">`TypeFilterAttribute` can optionally accept constructor arguments for the type.</span></span>

<span data-ttu-id="c774c-663">Při použití `TypeFilterAttribute`se nastavuje [TypeFilterAttribute. IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span><span class="sxs-lookup"><span data-stu-id="c774c-663">When using `TypeFilterAttribute`, setting [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span></span>
* <span data-ttu-id="c774c-664">Poskytuje nápovědu, že se instance filtru *dá* znovu použít mimo rozsah požadavku, který byl vytvořen v rámci.</span><span class="sxs-lookup"><span data-stu-id="c774c-664">Provides hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="c774c-665">Modul runtime ASP.NET Core neposkytuje žádné záruky, že se vytvoří jediná instance filtru.</span><span class="sxs-lookup"><span data-stu-id="c774c-665">The ASP.NET Core runtime provides no guarantees that a single instance of the filter will be created.</span></span>

* <span data-ttu-id="c774c-666">Neměl by se používat s filtrem, který závisí na službách s jinou životností než singleton.</span><span class="sxs-lookup"><span data-stu-id="c774c-666">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

<span data-ttu-id="c774c-667">Následující příklad ukazuje, jak předat argumenty typu pomocí `TypeFilterAttribute`:</span><span class="sxs-lookup"><span data-stu-id="c774c-667">The following example shows how to pass arguments to a type using `TypeFilterAttribute`:</span></span>

[!code-csharp[](../../mvc/controllers/filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_TypeFilter&highlight=1,2)]
[!code-csharp[](../../mvc/controllers/filters/sample/FiltersSample/Filters/LogConstantFilter.cs?name=snippet_TypeFilter_Implementation&highlight=6)]

<!-- 
https://localhost:5001/home/hi?name=joe
VS debug window shows 
FiltersSample.Filters.LogConstantFilter:Information: Method 'Hi' called
-->

## <a name="authorization-filters"></a><span data-ttu-id="c774c-668">Filtry autorizace</span><span class="sxs-lookup"><span data-stu-id="c774c-668">Authorization filters</span></span>

<span data-ttu-id="c774c-669">Filtry autorizace:</span><span class="sxs-lookup"><span data-stu-id="c774c-669">Authorization filters:</span></span>

* <span data-ttu-id="c774c-670">Jsou první filtry spouštěny v kanálu filtru.</span><span class="sxs-lookup"><span data-stu-id="c774c-670">Are the first filters run in the filter pipeline.</span></span>
* <span data-ttu-id="c774c-671">Řízení přístupu k metodám akcí.</span><span class="sxs-lookup"><span data-stu-id="c774c-671">Control access to action methods.</span></span>
* <span data-ttu-id="c774c-672">Před metodou, ale ne po metodě.</span><span class="sxs-lookup"><span data-stu-id="c774c-672">Have a before method, but no after method.</span></span>

<span data-ttu-id="c774c-673">Vlastní autorizační filtry vyžadují vlastní autorizační rozhraní.</span><span class="sxs-lookup"><span data-stu-id="c774c-673">Custom authorization filters require a custom authorization framework.</span></span> <span data-ttu-id="c774c-674">Preferovat konfiguraci autorizačních zásad nebo psaní vlastních zásad autorizace při psaní vlastního filtru.</span><span class="sxs-lookup"><span data-stu-id="c774c-674">Prefer configuring the authorization policies or writing a custom authorization policy over writing a custom filter.</span></span> <span data-ttu-id="c774c-675">Vestavěný autorizační filtr:</span><span class="sxs-lookup"><span data-stu-id="c774c-675">The built-in authorization filter:</span></span>

* <span data-ttu-id="c774c-676">Volá autorizační systém.</span><span class="sxs-lookup"><span data-stu-id="c774c-676">Calls the authorization system.</span></span>
* <span data-ttu-id="c774c-677">Neautorizuje požadavky.</span><span class="sxs-lookup"><span data-stu-id="c774c-677">Does not authorize requests.</span></span>

<span data-ttu-id="c774c-678">Negenerovat **výjimky** v rámci autorizačních filtrů:</span><span class="sxs-lookup"><span data-stu-id="c774c-678">Do **not** throw exceptions within authorization filters:</span></span>

* <span data-ttu-id="c774c-679">Výjimka nebude zpracována.</span><span class="sxs-lookup"><span data-stu-id="c774c-679">The exception will not be handled.</span></span>
* <span data-ttu-id="c774c-680">Filtry výjimek nebudou zpracovávat výjimku.</span><span class="sxs-lookup"><span data-stu-id="c774c-680">Exception filters will not handle the exception.</span></span>

<span data-ttu-id="c774c-681">Zvažte vydání výzvy, pokud dojde k výjimce ve autorizačním filtru.</span><span class="sxs-lookup"><span data-stu-id="c774c-681">Consider issuing a challenge when an exception occurs in an authorization filter.</span></span>

<span data-ttu-id="c774c-682">Přečtěte si další informace o [autorizaci](xref:security/authorization/introduction).</span><span class="sxs-lookup"><span data-stu-id="c774c-682">Learn more about [Authorization](xref:security/authorization/introduction).</span></span>

## <a name="resource-filters"></a><span data-ttu-id="c774c-683">Filtry prostředků</span><span class="sxs-lookup"><span data-stu-id="c774c-683">Resource filters</span></span>

<span data-ttu-id="c774c-684">Filtry prostředků:</span><span class="sxs-lookup"><span data-stu-id="c774c-684">Resource filters:</span></span>

* <span data-ttu-id="c774c-685">Implementujte rozhraní <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> nebo <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter>.</span><span class="sxs-lookup"><span data-stu-id="c774c-685">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> interface.</span></span>
* <span data-ttu-id="c774c-686">Provádění zabalí většinu kanálu filtru.</span><span class="sxs-lookup"><span data-stu-id="c774c-686">Execution wraps most of the filter pipeline.</span></span>
* <span data-ttu-id="c774c-687">Před filtry prostředků se spouštějí jenom [filtry autorizace](#authorization-filters) .</span><span class="sxs-lookup"><span data-stu-id="c774c-687">Only [Authorization filters](#authorization-filters) run before resource filters.</span></span>

<span data-ttu-id="c774c-688">Filtry prostředků jsou užitečné pro krátkodobé okruhy v kanálu.</span><span class="sxs-lookup"><span data-stu-id="c774c-688">Resource filters are useful to short-circuit most of the pipeline.</span></span> <span data-ttu-id="c774c-689">Například filtr ukládání do mezipaměti může zabránit zbývajícímu kanálu v případě přístupů do mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="c774c-689">For example, a caching filter can avoid the rest of the pipeline on a cache hit.</span></span>

<span data-ttu-id="c774c-690">Příklady filtru prostředků:</span><span class="sxs-lookup"><span data-stu-id="c774c-690">Resource filter examples:</span></span>

* <span data-ttu-id="c774c-691">[Filtr prostředků se zkrácenými okruhy](#short-circuiting-resource-filter) zobrazenými dříve.</span><span class="sxs-lookup"><span data-stu-id="c774c-691">[The short-circuiting resource filter](#short-circuiting-resource-filter) shown previously.</span></span>
* <span data-ttu-id="c774c-692">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span><span class="sxs-lookup"><span data-stu-id="c774c-692">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span></span>

  * <span data-ttu-id="c774c-693">Zabraňuje vazbě modelu v přístupu k datům formuláře.</span><span class="sxs-lookup"><span data-stu-id="c774c-693">Prevents model binding from accessing the form data.</span></span>
  * <span data-ttu-id="c774c-694">Používá se pro nahrávání velkých souborů, aby se zabránilo čtení dat z formuláře do paměti.</span><span class="sxs-lookup"><span data-stu-id="c774c-694">Used for large file uploads to prevent the form data from being read into memory.</span></span>

## <a name="action-filters"></a><span data-ttu-id="c774c-695">Filtry akcí</span><span class="sxs-lookup"><span data-stu-id="c774c-695">Action filters</span></span>

> [!IMPORTANT]
> <span data-ttu-id="c774c-696">Filtry akcí **se** nevztahují na Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="c774c-696">Action filters do **not** apply to Razor Pages.</span></span> <span data-ttu-id="c774c-697">Razor Pages podporuje <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> a <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter>.</span><span class="sxs-lookup"><span data-stu-id="c774c-697">Razor Pages supports <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span></span> <span data-ttu-id="c774c-698">Další informace naleznete v tématu [metody Filter pro Razor Pages](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="c774c-698">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

<span data-ttu-id="c774c-699">Filtry akcí:</span><span class="sxs-lookup"><span data-stu-id="c774c-699">Action filters:</span></span>

* <span data-ttu-id="c774c-700">Implementujte rozhraní <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> nebo <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter>.</span><span class="sxs-lookup"><span data-stu-id="c774c-700">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> interface.</span></span>
* <span data-ttu-id="c774c-701">Jejich spuštění obklopuje provádění metod akcí.</span><span class="sxs-lookup"><span data-stu-id="c774c-701">Their execution surrounds the execution of action methods.</span></span>

<span data-ttu-id="c774c-702">Následující kód ukazuje filtr vzorové akce:</span><span class="sxs-lookup"><span data-stu-id="c774c-702">The following code shows a sample action filter:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="c774c-703"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> poskytuje následující vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="c774c-703">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> provides the following properties:</span></span>

* <span data-ttu-id="c774c-704"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> – povolí čtení vstupů do metody akce.</span><span class="sxs-lookup"><span data-stu-id="c774c-704"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> - enables the inputs to an action method be read.</span></span>
* <span data-ttu-id="c774c-705"><xref:Microsoft.AspNetCore.Mvc.Controller> – povolí manipulaci s instancí kontroleru.</span><span class="sxs-lookup"><span data-stu-id="c774c-705"><xref:Microsoft.AspNetCore.Mvc.Controller> - enables manipulating the controller instance.</span></span>
* <span data-ttu-id="c774c-706"><xref:System.Web.Mvc.ActionExecutingContext.Result> – nastavení `Result` provádění krátkých okruhů metodou Action a následnými filtry akcí.</span><span class="sxs-lookup"><span data-stu-id="c774c-706"><xref:System.Web.Mvc.ActionExecutingContext.Result> - setting `Result` short-circuits execution of the action method and subsequent action filters.</span></span>

<span data-ttu-id="c774c-707">Vyvolání výjimky v metodě akce:</span><span class="sxs-lookup"><span data-stu-id="c774c-707">Throwing an exception in an action method:</span></span>

* <span data-ttu-id="c774c-708">Zabraňuje spuštění dalších filtrů.</span><span class="sxs-lookup"><span data-stu-id="c774c-708">Prevents running of subsequent filters.</span></span>
* <span data-ttu-id="c774c-709">Na rozdíl od nastavení `Result`se místo úspěšného výsledku považuje za selhání.</span><span class="sxs-lookup"><span data-stu-id="c774c-709">Unlike setting `Result`, is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="c774c-710"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> poskytuje `Controller` a `Result` a následující vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="c774c-710">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> provides `Controller` and `Result` plus the following properties:</span></span>

* <span data-ttu-id="c774c-711"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> – hodnota true, pokud provádění akce bylo zkráceno jiným filtrem.</span><span class="sxs-lookup"><span data-stu-id="c774c-711"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> - True if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="c774c-712"><xref:System.Web.Mvc.ActionExecutedContext.Exception> – nenulová, pokud akce nebo filtr dříve spuštěných akcí vyvolaly výjimku.</span><span class="sxs-lookup"><span data-stu-id="c774c-712"><xref:System.Web.Mvc.ActionExecutedContext.Exception> - Non-null if the action or a previously run action filter threw an exception.</span></span> <span data-ttu-id="c774c-713">Nastavení této vlastnosti na hodnotu null:</span><span class="sxs-lookup"><span data-stu-id="c774c-713">Setting this property to null:</span></span>

  * <span data-ttu-id="c774c-714">Efektivně zpracovává výjimku.</span><span class="sxs-lookup"><span data-stu-id="c774c-714">Effectively handles the exception.</span></span>
  * <span data-ttu-id="c774c-715">`Result` se spustí, jako kdyby byla vrácena z metody Action.</span><span class="sxs-lookup"><span data-stu-id="c774c-715">`Result` is executed as if it was returned from the action method.</span></span>

<span data-ttu-id="c774c-716">Pro `IAsyncActionFilter`volání <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span><span class="sxs-lookup"><span data-stu-id="c774c-716">For an `IAsyncActionFilter`, a call to the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span></span>

* <span data-ttu-id="c774c-717">Provede všechny následné filtry akcí a metodu Action.</span><span class="sxs-lookup"><span data-stu-id="c774c-717">Executes any subsequent action filters and the action method.</span></span>
* <span data-ttu-id="c774c-718">Vrací objekt `ActionExecutedContext`.</span><span class="sxs-lookup"><span data-stu-id="c774c-718">Returns `ActionExecutedContext`.</span></span>

<span data-ttu-id="c774c-719">Do krátkodobého okruhu, přiřaďte <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> k instanci výsledku a Nevolejte `next` (`ActionExecutionDelegate`).</span><span class="sxs-lookup"><span data-stu-id="c774c-719">To short-circuit, assign <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> to a result instance and don't call `next` (the `ActionExecutionDelegate`).</span></span>

<span data-ttu-id="c774c-720">Rozhraní poskytuje abstraktní <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>, které mohou být roztříděné.</span><span class="sxs-lookup"><span data-stu-id="c774c-720">The framework provides an abstract <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> that can be subclassed.</span></span>

<span data-ttu-id="c774c-721">Filtr akce `OnActionExecuting` lze použít k těmto akcím:</span><span class="sxs-lookup"><span data-stu-id="c774c-721">The `OnActionExecuting` action filter can be used to:</span></span>

* <span data-ttu-id="c774c-722">Ověří stav modelu.</span><span class="sxs-lookup"><span data-stu-id="c774c-722">Validate model state.</span></span>
* <span data-ttu-id="c774c-723">Pokud je stav neplatný, vrátí se chyba.</span><span class="sxs-lookup"><span data-stu-id="c774c-723">Return an error if the state is invalid.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet)]

<span data-ttu-id="c774c-724">Metoda `OnActionExecuted` se spouští po metodě Action:</span><span class="sxs-lookup"><span data-stu-id="c774c-724">The `OnActionExecuted` method runs after the action method:</span></span>

* <span data-ttu-id="c774c-725">A mohou zobrazit výsledky akce a manipulovat s nimi prostřednictvím vlastnosti <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result>.</span><span class="sxs-lookup"><span data-stu-id="c774c-725">And can see and manipulate the results of the action through the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> property.</span></span>
* <span data-ttu-id="c774c-726"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> je nastavena na hodnotu true, pokud bylo provedení akce zkráceno jiným filtrem.</span><span class="sxs-lookup"><span data-stu-id="c774c-726"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> is set to true if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="c774c-727"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> je nastavena na hodnotu jinou než null, pokud akce nebo filtr následné akce vyvolaly výjimku.</span><span class="sxs-lookup"><span data-stu-id="c774c-727"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> is set to a non-null value if the action or a subsequent action filter threw an exception.</span></span> <span data-ttu-id="c774c-728">Nastavení `Exception` na hodnotu null:</span><span class="sxs-lookup"><span data-stu-id="c774c-728">Setting `Exception` to null:</span></span>

  * <span data-ttu-id="c774c-729">Efektivně zpracovává výjimku.</span><span class="sxs-lookup"><span data-stu-id="c774c-729">Effectively handles an exception.</span></span>
  * <span data-ttu-id="c774c-730">`ActionExecutedContext.Result` se spustí, jako kdyby byl vrácen normálně z metody Action.</span><span class="sxs-lookup"><span data-stu-id="c774c-730">`ActionExecutedContext.Result` is executed as if it were returned normally from the action method.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet2&higlight=12-99)]

## <a name="exception-filters"></a><span data-ttu-id="c774c-731">Filtry výjimek</span><span class="sxs-lookup"><span data-stu-id="c774c-731">Exception filters</span></span>

<span data-ttu-id="c774c-732">Filtry výjimek:</span><span class="sxs-lookup"><span data-stu-id="c774c-732">Exception filters:</span></span>

* <span data-ttu-id="c774c-733">Implementujte <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> nebo <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span><span class="sxs-lookup"><span data-stu-id="c774c-733">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span></span> 
* <span data-ttu-id="c774c-734">Dá se použít k implementaci běžných zásad zpracování chyb.</span><span class="sxs-lookup"><span data-stu-id="c774c-734">Can be used to implement common error handling policies.</span></span>

<span data-ttu-id="c774c-735">Následující ukázkový filtr výjimek používá vlastní zobrazení chyb pro zobrazení podrobností o výjimkách, ke kterým dochází při vývoji aplikace:</span><span class="sxs-lookup"><span data-stu-id="c774c-735">The following sample exception filter uses a custom error view to display details about exceptions that occur when the app is in development:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/CustomExceptionFilter.cs?name=snippet_ExceptionFilter&highlight=16-19)]

<span data-ttu-id="c774c-736">Filtry výjimek:</span><span class="sxs-lookup"><span data-stu-id="c774c-736">Exception filters:</span></span>

* <span data-ttu-id="c774c-737">Nemusíte mít události před a po.</span><span class="sxs-lookup"><span data-stu-id="c774c-737">Don't have before and after events.</span></span>
* <span data-ttu-id="c774c-738">Implementujte <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> nebo <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span><span class="sxs-lookup"><span data-stu-id="c774c-738">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span></span>
* <span data-ttu-id="c774c-739">Zpracování neošetřených výjimek, ke kterým dochází při vytváření stránky nebo kontroléru Razor, [vázání modelů](xref:mvc/models/model-binding), filtrů akcí nebo metod akcí.</span><span class="sxs-lookup"><span data-stu-id="c774c-739">Handle unhandled exceptions that occur in Razor Page or controller creation, [model binding](xref:mvc/models/model-binding), action filters, or action methods.</span></span>
* <span data-ttu-id="c774c-740">**Nezachycujte** výjimky, ke kterým dochází v filtrech prostředků, výsledných filtrech nebo v důsledku provádění výsledků MVC.</span><span class="sxs-lookup"><span data-stu-id="c774c-740">Do **not** catch exceptions that occur in resource filters, result filters, or MVC result execution.</span></span>

<span data-ttu-id="c774c-741">Chcete-li zpracovat výjimku, nastavte vlastnost <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> na `true` nebo zapište odpověď.</span><span class="sxs-lookup"><span data-stu-id="c774c-741">To handle an exception, set the <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> property to `true` or write a response.</span></span> <span data-ttu-id="c774c-742">Tím se zastaví šíření výjimky.</span><span class="sxs-lookup"><span data-stu-id="c774c-742">This stops propagation of the exception.</span></span> <span data-ttu-id="c774c-743">Filtr výjimek nemůže vypnout výjimku na "úspěch".</span><span class="sxs-lookup"><span data-stu-id="c774c-743">An exception filter can't turn an exception into a "success".</span></span> <span data-ttu-id="c774c-744">To může provést pouze filtr akcí.</span><span class="sxs-lookup"><span data-stu-id="c774c-744">Only an action filter can do that.</span></span>

<span data-ttu-id="c774c-745">Filtry výjimek:</span><span class="sxs-lookup"><span data-stu-id="c774c-745">Exception filters:</span></span>

* <span data-ttu-id="c774c-746">Jsou vhodné pro vytváření přesahů výjimek, ke kterým dochází v rámci akcí.</span><span class="sxs-lookup"><span data-stu-id="c774c-746">Are good for trapping exceptions that occur within actions.</span></span>
* <span data-ttu-id="c774c-747">Nejsou tak flexibilní jako middleware při zpracování chyb.</span><span class="sxs-lookup"><span data-stu-id="c774c-747">Are not as flexible as error handling middleware.</span></span>

<span data-ttu-id="c774c-748">Preferovat middleware pro zpracování výjimek.</span><span class="sxs-lookup"><span data-stu-id="c774c-748">Prefer middleware for exception handling.</span></span> <span data-ttu-id="c774c-749">Filtry výjimek použijte pouze v případě, že se zpracování chyb *liší* v závislosti na tom, která metoda Action je volána.</span><span class="sxs-lookup"><span data-stu-id="c774c-749">Use exception filters only where error handling *differs* based on which action method is called.</span></span> <span data-ttu-id="c774c-750">Aplikace může například mít metody akcí pro koncové body rozhraní API i pro zobrazení/HTML.</span><span class="sxs-lookup"><span data-stu-id="c774c-750">For example, an app might have action methods for both API endpoints and for views/HTML.</span></span> <span data-ttu-id="c774c-751">Koncové body rozhraní API mohou vracet informace o chybě jako JSON, zatímco akce na základě zobrazení by mohly vracet chybovou stránku jako HTML.</span><span class="sxs-lookup"><span data-stu-id="c774c-751">The API endpoints could return error information as JSON, while the view-based actions could return an error page as HTML.</span></span>

## <a name="result-filters"></a><span data-ttu-id="c774c-752">Filtry výsledků</span><span class="sxs-lookup"><span data-stu-id="c774c-752">Result filters</span></span>

<span data-ttu-id="c774c-753">Filtry výsledků:</span><span class="sxs-lookup"><span data-stu-id="c774c-753">Result filters:</span></span>

* <span data-ttu-id="c774c-754">Implementace rozhraní:</span><span class="sxs-lookup"><span data-stu-id="c774c-754">Implement an interface:</span></span>
  * <span data-ttu-id="c774c-755"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> Nebo <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="c774c-755"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
  * <span data-ttu-id="c774c-756"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> Nebo <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span><span class="sxs-lookup"><span data-stu-id="c774c-756"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span></span>
* <span data-ttu-id="c774c-757">Jejich spuštění obklopuje provádění výsledků akcí.</span><span class="sxs-lookup"><span data-stu-id="c774c-757">Their execution surrounds the execution of action results.</span></span>

### <a name="iresultfilter-and-iasyncresultfilter"></a><span data-ttu-id="c774c-758">IResultFilter a IAsyncResultFilter</span><span class="sxs-lookup"><span data-stu-id="c774c-758">IResultFilter and IAsyncResultFilter</span></span>

<span data-ttu-id="c774c-759">Následující kód ukazuje filtr výsledků, který přidá hlavičku protokolu HTTP:</span><span class="sxs-lookup"><span data-stu-id="c774c-759">The following code shows a result filter that adds an HTTP header:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="c774c-760">Typ výsledku, který se má provést, závisí na akci.</span><span class="sxs-lookup"><span data-stu-id="c774c-760">The kind of result being executed depends on the action.</span></span> <span data-ttu-id="c774c-761">Akce vracející zobrazení zahrnuje všechny zpracování Razor jako součást prováděné <xref:Microsoft.AspNetCore.Mvc.ViewResult>.</span><span class="sxs-lookup"><span data-stu-id="c774c-761">An action returning a view would include all razor processing as part of the <xref:Microsoft.AspNetCore.Mvc.ViewResult> being executed.</span></span> <span data-ttu-id="c774c-762">Metoda rozhraní API může provést určitou serializaci v rámci provádění výsledku.</span><span class="sxs-lookup"><span data-stu-id="c774c-762">An API method might perform some serialization as part of the execution of the result.</span></span> <span data-ttu-id="c774c-763">Přečtěte si další informace o [výsledcích akcí](xref:mvc/controllers/actions).</span><span class="sxs-lookup"><span data-stu-id="c774c-763">Learn more about [action results](xref:mvc/controllers/actions).</span></span>

<span data-ttu-id="c774c-764">Filtry výsledků se spustí pouze v případě, že akce nebo filtr akcí vytvoří výsledek akce.</span><span class="sxs-lookup"><span data-stu-id="c774c-764">Result filters are only executed when an action or action filter produces an action result.</span></span> <span data-ttu-id="c774c-765">Filtry výsledků nejsou provedeny v těchto případech:</span><span class="sxs-lookup"><span data-stu-id="c774c-765">Result filters are not executed when:</span></span>

* <span data-ttu-id="c774c-766">Filtr autorizace nebo filtr prostředků pro krátké okruhy kanálu.</span><span class="sxs-lookup"><span data-stu-id="c774c-766">An authorization filter or resource filter short-circuits the pipeline.</span></span>
* <span data-ttu-id="c774c-767">Filtr výjimek zpracovává výjimku tím, že vyprodukuje výsledek akce.</span><span class="sxs-lookup"><span data-stu-id="c774c-767">An exception filter handles an exception by producing an action result.</span></span>

<span data-ttu-id="c774c-768">Metoda <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> může krátkodobé vykonání výsledku akce a následných filtrů výsledků nastavením <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> na `true`.</span><span class="sxs-lookup"><span data-stu-id="c774c-768">The <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> method can short-circuit execution of the action result and subsequent result filters by setting <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> to `true`.</span></span> <span data-ttu-id="c774c-769">Zapište do objektu Response v případě krátkého okruhu, abyste se vyhnuli vygenerování prázdné odpovědi.</span><span class="sxs-lookup"><span data-stu-id="c774c-769">Write to the response object when short-circuiting to avoid generating an empty response.</span></span> <span data-ttu-id="c774c-770">Vyvolání výjimky v `IResultFilter.OnResultExecuting` bude:</span><span class="sxs-lookup"><span data-stu-id="c774c-770">Throwing an exception in `IResultFilter.OnResultExecuting` will:</span></span>

* <span data-ttu-id="c774c-771">Zabraňte provádění výsledků akce a dalších filtrů.</span><span class="sxs-lookup"><span data-stu-id="c774c-771">Prevent execution of the action result and subsequent filters.</span></span>
* <span data-ttu-id="c774c-772">Být považována za selhání namísto úspěšného výsledku.</span><span class="sxs-lookup"><span data-stu-id="c774c-772">Be treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="c774c-773">Při spuštění metody <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> je odpověď pravděpodobně již odeslána klientovi.</span><span class="sxs-lookup"><span data-stu-id="c774c-773">When the <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> method runs, the response has likely already been sent to the client.</span></span> <span data-ttu-id="c774c-774">Pokud byla odpověď již odeslána klientovi, nelze ji dále změnit.</span><span class="sxs-lookup"><span data-stu-id="c774c-774">If the response has already been sent to the client, it cannot be changed further.</span></span>

<span data-ttu-id="c774c-775">`ResultExecutedContext.Canceled` je nastavená na `true`, pokud je spuštění výsledku akce od sebe krátkým jiným filtrem.</span><span class="sxs-lookup"><span data-stu-id="c774c-775">`ResultExecutedContext.Canceled` is set to `true` if the action result execution was short-circuited by another filter.</span></span>

<span data-ttu-id="c774c-776">`ResultExecutedContext.Exception` je nastavena na hodnotu jinou než null, pokud výsledek akce nebo následný filtr výsledků vyvolal výjimku.</span><span class="sxs-lookup"><span data-stu-id="c774c-776">`ResultExecutedContext.Exception` is set to a non-null value if the action result or a subsequent result filter threw an exception.</span></span> <span data-ttu-id="c774c-777">Nastavení `Exception` na hodnotu null efektivně zpracovává výjimku a brání výjimce znovu vyvolat pomocí ASP.NET Core později v kanálu.</span><span class="sxs-lookup"><span data-stu-id="c774c-777">Setting `Exception` to null effectively handles an exception and prevents the exception from being rethrown by ASP.NET Core later in the pipeline.</span></span> <span data-ttu-id="c774c-778">Neexistuje žádný spolehlivý způsob, jak zapisovat data do odpovědi při zpracování výjimky ve filtru výsledků.</span><span class="sxs-lookup"><span data-stu-id="c774c-778">There is no reliable way to write data to a response when handling an exception in a result filter.</span></span> <span data-ttu-id="c774c-779">Pokud byly hlavičky vyprázdněny do klienta, pokud výsledek akce vyvolá výjimku, neexistuje žádný spolehlivý mechanismus pro odeslání kódu chyby.</span><span class="sxs-lookup"><span data-stu-id="c774c-779">If the headers have been flushed to the client when an action result throws an exception, there's no reliable mechanism to send a failure code.</span></span>

<span data-ttu-id="c774c-780">U <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>volání `await next` na <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> provede všechny následné filtry výsledků a výsledek akce.</span><span class="sxs-lookup"><span data-stu-id="c774c-780">For an <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, a call to `await next` on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> executes any subsequent result filters and the action result.</span></span> <span data-ttu-id="c774c-781">Do krátkodobého okruhu nastavte [ResultExecutingContext. Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) na `true` a nevolejte `ResultExecutionDelegate`:</span><span class="sxs-lookup"><span data-stu-id="c774c-781">To short-circuit, set [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) to `true` and don't call the `ResultExecutionDelegate`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MyAsyncResponseFilter.cs?name=snippet)]

<span data-ttu-id="c774c-782">Rozhraní poskytuje abstraktní `ResultFilterAttribute`, které mohou být roztříděné.</span><span class="sxs-lookup"><span data-stu-id="c774c-782">The framework provides an abstract `ResultFilterAttribute` that can be subclassed.</span></span> <span data-ttu-id="c774c-783">Dříve uvedená třída [AddHeaderAttribute](#add-header-attribute) je příkladem atributu filtru výsledků.</span><span class="sxs-lookup"><span data-stu-id="c774c-783">The [AddHeaderAttribute](#add-header-attribute) class shown previously is an example of a result filter attribute.</span></span>

### <a name="ialwaysrunresultfilter-and-iasyncalwaysrunresultfilter"></a><span data-ttu-id="c774c-784">IAlwaysRunResultFilter a IAsyncAlwaysRunResultFilter</span><span class="sxs-lookup"><span data-stu-id="c774c-784">IAlwaysRunResultFilter and IAsyncAlwaysRunResultFilter</span></span>

<span data-ttu-id="c774c-785">Rozhraní <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> a <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> deklarují <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> implementaci, která se spouští pro všechny výsledky akcí.</span><span class="sxs-lookup"><span data-stu-id="c774c-785">The <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> interfaces declare an <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> implementation that runs for all action results.</span></span> <span data-ttu-id="c774c-786">To zahrnuje výsledky akcí, které vytvořil:</span><span class="sxs-lookup"><span data-stu-id="c774c-786">This includes action results produced by:</span></span>

* <span data-ttu-id="c774c-787">Filtry autorizace a filtry prostředků, které mají krátký okruh.</span><span class="sxs-lookup"><span data-stu-id="c774c-787">Authorization filters and resource filters that short-circuit.</span></span>
* <span data-ttu-id="c774c-788">Filtry výjimek.</span><span class="sxs-lookup"><span data-stu-id="c774c-788">Exception filters.</span></span>

<span data-ttu-id="c774c-789">Například následující filtr vždy spustí a nastaví výsledek akce (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) s *422 nezpracovaným* stavovým kódem entity, pokud se nezdaří vyjednávání obsahu:</span><span class="sxs-lookup"><span data-stu-id="c774c-789">For example, the following filter always runs and sets an action result (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) with a *422 Unprocessable Entity* status code when content negotiation fails:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/UnprocessableResultFilter.cs?name=snippet)]

### <a name="ifilterfactory"></a><span data-ttu-id="c774c-790">IFilterFactory</span><span class="sxs-lookup"><span data-stu-id="c774c-790">IFilterFactory</span></span>

<span data-ttu-id="c774c-791"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implementuje <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span><span class="sxs-lookup"><span data-stu-id="c774c-791"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span></span> <span data-ttu-id="c774c-792">Proto může být instance `IFilterFactory` použita jako instance `IFilterMetadata` kdekoli v kanálu filtru.</span><span class="sxs-lookup"><span data-stu-id="c774c-792">Therefore, an `IFilterFactory` instance can be used as an `IFilterMetadata` instance anywhere in the filter pipeline.</span></span> <span data-ttu-id="c774c-793">Když se modul runtime připraví k vyvolání filtru, pokusí se ho přetypovat na `IFilterFactory`.</span><span class="sxs-lookup"><span data-stu-id="c774c-793">When the runtime prepares to invoke the filter, it attempts to cast it to an `IFilterFactory`.</span></span> <span data-ttu-id="c774c-794">Pokud je toto přetypování úspěšné, je volána metoda <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> pro vytvoření instance `IFilterMetadata`, která je vyvolána.</span><span class="sxs-lookup"><span data-stu-id="c774c-794">If that cast succeeds, the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method is called to create the `IFilterMetadata` instance that is invoked.</span></span> <span data-ttu-id="c774c-795">To poskytuje flexibilní návrh, protože přesný kanál filtru není nutné nastavit explicitně při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="c774c-795">This provides a flexible design, since the precise filter pipeline doesn't need to be set explicitly when the app starts.</span></span>

<span data-ttu-id="c774c-796">`IFilterFactory` lze implementovat pomocí implementace vlastních atributů jako jiný přístup k vytváření filtrů:</span><span class="sxs-lookup"><span data-stu-id="c774c-796">`IFilterFactory` can be implemented using custom attribute implementations as another approach to creating filters:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/AddHeaderWithFactoryAttribute.cs?name=snippet_IFilterFactory&highlight=1,4,5,6,7)]

<span data-ttu-id="c774c-797">Předchozí kód lze otestovat spuštěním [ukázky stahování](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample):</span><span class="sxs-lookup"><span data-stu-id="c774c-797">The preceding code can be tested by running the [download sample](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample):</span></span>

* <span data-ttu-id="c774c-798">Vyvolejte vývojářské nástroje F12.</span><span class="sxs-lookup"><span data-stu-id="c774c-798">Invoke the F12 developer tools.</span></span>
* <span data-ttu-id="c774c-799">Přejděte na adresu `https://localhost:5001/Sample/HeaderWithFactory`.</span><span class="sxs-lookup"><span data-stu-id="c774c-799">Navigate to `https://localhost:5001/Sample/HeaderWithFactory`.</span></span>

<span data-ttu-id="c774c-800">Vývojářské nástroje F12 zobrazují následující hlavičky odpovědí přidané ukázkovým kódem:</span><span class="sxs-lookup"><span data-stu-id="c774c-800">The F12 developer tools display the following response headers added by the sample code:</span></span>

* <span data-ttu-id="c774c-801">**Autor:** `Joe Smith`</span><span class="sxs-lookup"><span data-stu-id="c774c-801">**author:** `Joe Smith`</span></span>
* <span data-ttu-id="c774c-802">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span><span class="sxs-lookup"><span data-stu-id="c774c-802">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span></span>
* <span data-ttu-id="c774c-803">**interní:** `My header`</span><span class="sxs-lookup"><span data-stu-id="c774c-803">**internal:** `My header`</span></span>

<span data-ttu-id="c774c-804">Předchozí kód vytvoří hlavičku **interního:** `My header` Response.</span><span class="sxs-lookup"><span data-stu-id="c774c-804">The preceding code creates the **internal:** `My header` response header.</span></span>

### <a name="ifilterfactory-implemented-on-an-attribute"></a><span data-ttu-id="c774c-805">IFilterFactory implementované u atributu</span><span class="sxs-lookup"><span data-stu-id="c774c-805">IFilterFactory implemented on an attribute</span></span>

<!-- Review 
This section needs to be rewritten.
What's a non-named attribute?
-->

<span data-ttu-id="c774c-806">Filtry, které implementují `IFilterFactory`, jsou užitečné pro filtry, které:</span><span class="sxs-lookup"><span data-stu-id="c774c-806">Filters that implement `IFilterFactory` are useful for filters that:</span></span>

* <span data-ttu-id="c774c-807">Nevyžadovat předávání parametrů</span><span class="sxs-lookup"><span data-stu-id="c774c-807">Don't require passing parameters.</span></span>
* <span data-ttu-id="c774c-808">Musí mít závislosti konstruktoru, které musí vyplnit DI.</span><span class="sxs-lookup"><span data-stu-id="c774c-808">Have constructor dependencies that need to be filled by DI.</span></span>

<span data-ttu-id="c774c-809"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implementuje <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span><span class="sxs-lookup"><span data-stu-id="c774c-809"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="c774c-810">`IFilterFactory` zpřístupňuje <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> metodu pro vytvoření instance <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span><span class="sxs-lookup"><span data-stu-id="c774c-810">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="c774c-811">`CreateInstance` načte zadaný typ z kontejneru Services (DI).</span><span class="sxs-lookup"><span data-stu-id="c774c-811">`CreateInstance` loads the specified type from the services container (DI).</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/SampleActionFilterAttribute.cs?name=snippet_TypeFilterAttribute&highlight=1,3,7)]

<span data-ttu-id="c774c-812">Následující kód ukazuje tři přístupy k použití `[SampleActionFilter]`:</span><span class="sxs-lookup"><span data-stu-id="c774c-812">The following code shows three approaches to applying the `[SampleActionFilter]`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet&highlight=1)]

<span data-ttu-id="c774c-813">V předchozím kódu upravení metoda s `[SampleActionFilter]` je upřednostňovaným přístupem k použití `SampleActionFilter`.</span><span class="sxs-lookup"><span data-stu-id="c774c-813">In the preceding code, decorating the method with `[SampleActionFilter]` is the preferred approach to applying the `SampleActionFilter`.</span></span>

## <a name="using-middleware-in-the-filter-pipeline"></a><span data-ttu-id="c774c-814">Použití middlewaru v kanálu filtru</span><span class="sxs-lookup"><span data-stu-id="c774c-814">Using middleware in the filter pipeline</span></span>

<span data-ttu-id="c774c-815">Filtry prostředků fungují jako [middleware](xref:fundamentals/middleware/index) v tom, že obklopují provádění všech položek, které jsou později v kanálu.</span><span class="sxs-lookup"><span data-stu-id="c774c-815">Resource filters work like [middleware](xref:fundamentals/middleware/index) in that they surround the execution of everything that comes later in the pipeline.</span></span> <span data-ttu-id="c774c-816">Filtry se ale liší od middlewaru v tom, že jsou součástí modulu ASP.NET Core Runtime, což znamená, že mají přístup k ASP.NET Core kontextu a konstrukcí.</span><span class="sxs-lookup"><span data-stu-id="c774c-816">But filters differ from middleware in that they're part of the ASP.NET Core runtime, which means that they have access to ASP.NET Core context and constructs.</span></span>

<span data-ttu-id="c774c-817">Chcete-li použít middleware jako filtr, vytvořte typ s `Configure` metodou, která určuje middleware pro vložení do kanálu filtru.</span><span class="sxs-lookup"><span data-stu-id="c774c-817">To use middleware as a filter, create a type with a `Configure` method that specifies the middleware to inject into the filter pipeline.</span></span> <span data-ttu-id="c774c-818">Následující příklad používá middleware Localization k vytvoření aktuální jazykové verze pro požadavek:</span><span class="sxs-lookup"><span data-stu-id="c774c-818">The following example uses the localization middleware to establish the current culture for a request:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LocalizationPipeline.cs?name=snippet_MiddlewareFilter&highlight=3,22)]

<span data-ttu-id="c774c-819">Pomocí <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> spusťte middleware:</span><span class="sxs-lookup"><span data-stu-id="c774c-819">Use the <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> to run the middleware:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_MiddlewareFilter&highlight=2)]

<span data-ttu-id="c774c-820">Filtry middlewaru jsou spouštěny ve stejné fázi kanálu filtru jako filtry prostředků, před vazbami modelů a po zbytek kanálu.</span><span class="sxs-lookup"><span data-stu-id="c774c-820">Middleware filters run at the same stage of the filter pipeline as Resource filters, before model binding and after the rest of the pipeline.</span></span>

## <a name="next-actions"></a><span data-ttu-id="c774c-821">Další akce</span><span class="sxs-lookup"><span data-stu-id="c774c-821">Next actions</span></span>

* <span data-ttu-id="c774c-822">Viz [metody filtru pro Razor Pages](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="c774c-822">See [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>
* <span data-ttu-id="c774c-823">Pro experimentování s filtry, [stažení, otestování a úpravy ukázky GitHubu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample).</span><span class="sxs-lookup"><span data-stu-id="c774c-823">To experiment with filters, [download, test, and modify the GitHub sample](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample).</span></span>

::: moniker-end
