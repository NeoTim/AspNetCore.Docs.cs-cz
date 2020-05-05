---
title: Filtry v ASP.NET Core
author: Rick-Anderson
description: Přečtěte si, jak filtry fungují a jak je používat v ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 02/04/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/controllers/filters
ms.openlocfilehash: 7272e05b408ac6f8daeda586c6f40fcc5bd1f6eb
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776783"
---
# <a name="filters-in-aspnet-core"></a><span data-ttu-id="18c47-103">Filtry v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="18c47-103">Filters in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="18c47-104">[Kirka Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Dykstra](https://github.com/tdykstra/)a [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="18c47-104">By [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="18c47-105">*Filtry* v ASP.NET Core umožňují spuštění kódu před nebo po určitých fázích v kanálu zpracování požadavků.</span><span class="sxs-lookup"><span data-stu-id="18c47-105">*Filters* in ASP.NET Core allow code to be run before or after specific stages in the request processing pipeline.</span></span>

<span data-ttu-id="18c47-106">Předdefinované filtry zpracovávají úlohy, jako například:</span><span class="sxs-lookup"><span data-stu-id="18c47-106">Built-in filters handle tasks such as:</span></span>

* <span data-ttu-id="18c47-107">Autorizace (zabránění přístupu k prostředkům, ke kterým uživatel nemá oprávnění).</span><span class="sxs-lookup"><span data-stu-id="18c47-107">Authorization (preventing access to resources a user isn't authorized for).</span></span>
* <span data-ttu-id="18c47-108">Ukládání odpovědí do mezipaměti (při krátkém okruhu kanálu požadavků, který vrátí odpověď uloženou v mezipaměti).</span><span class="sxs-lookup"><span data-stu-id="18c47-108">Response caching (short-circuiting the request pipeline to return a cached response).</span></span>

<span data-ttu-id="18c47-109">Vlastní filtry je možné vytvořit pro zpracování vzájemně se týkajících se otázek.</span><span class="sxs-lookup"><span data-stu-id="18c47-109">Custom filters can be created to handle cross-cutting concerns.</span></span> <span data-ttu-id="18c47-110">Mezi obavy mezi průřezy patří zpracování chyb, ukládání do mezipaměti, konfigurace, autorizace a protokolování.</span><span class="sxs-lookup"><span data-stu-id="18c47-110">Examples of cross-cutting concerns include error handling, caching, configuration, authorization, and logging.</span></span>  <span data-ttu-id="18c47-111">Filtry zabraňují duplikování kódu.</span><span class="sxs-lookup"><span data-stu-id="18c47-111">Filters avoid duplicating code.</span></span> <span data-ttu-id="18c47-112">Například filtr výjimek zpracování chyb může konsolidovat zpracování chyb.</span><span class="sxs-lookup"><span data-stu-id="18c47-112">For example, an error handling exception filter could consolidate error handling.</span></span>

<span data-ttu-id="18c47-113">Tento dokument se týká Razor Pages, řadičů rozhraní API a řadičů se zobrazeními.</span><span class="sxs-lookup"><span data-stu-id="18c47-113">This document applies to Razor Pages, API controllers, and controllers with views.</span></span> <span data-ttu-id="18c47-114">Filtry nefungují přímo se [součástmi Razor](xref:blazor/components).</span><span class="sxs-lookup"><span data-stu-id="18c47-114">Filters don't work directly with [Razor components](xref:blazor/components).</span></span> <span data-ttu-id="18c47-115">Filtr může nepřímo ovlivnit komponentu pouze v těchto případech:</span><span class="sxs-lookup"><span data-stu-id="18c47-115">A filter can only indirectly affect a component when:</span></span>

* <span data-ttu-id="18c47-116">Komponenta je vložena do stránky nebo zobrazení.</span><span class="sxs-lookup"><span data-stu-id="18c47-116">The component is embedded in a page or view.</span></span>
* <span data-ttu-id="18c47-117">Stránka nebo kontroler nebo zobrazení používá filtr.</span><span class="sxs-lookup"><span data-stu-id="18c47-117">The page or controller/view uses the filter.</span></span>

<span data-ttu-id="18c47-118">[Zobrazit nebo Stáhnout ukázku](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample) ([Jak stáhnout](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="18c47-118">[View or download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="how-filters-work"></a><span data-ttu-id="18c47-119">Jak fungují filtry</span><span class="sxs-lookup"><span data-stu-id="18c47-119">How filters work</span></span>

<span data-ttu-id="18c47-120">Filtry se spouštějí v *kanálu vyvolání akce ASP.NET Core*, někdy označované jako *kanál filtru*.</span><span class="sxs-lookup"><span data-stu-id="18c47-120">Filters run within the *ASP.NET Core action invocation pipeline*, sometimes referred to as the *filter pipeline*.</span></span> <span data-ttu-id="18c47-121">Kanál filtru se spustí po ASP.NET Core vybere akci, která se má provést.</span><span class="sxs-lookup"><span data-stu-id="18c47-121">The filter pipeline runs after ASP.NET Core selects the action to execute.</span></span>

![Požadavek se zpracovává prostřednictvím jiného middlewaru, middleware směrování, výběru akcí a kanálu vyvolání akce.](filters/_static/filter-pipeline-1.png)

### <a name="filter-types"></a><span data-ttu-id="18c47-124">Typy filtrů</span><span class="sxs-lookup"><span data-stu-id="18c47-124">Filter types</span></span>

<span data-ttu-id="18c47-125">Každý typ filtru se spustí v jiné fázi kanálu filtru:</span><span class="sxs-lookup"><span data-stu-id="18c47-125">Each filter type is executed at a different stage in the filter pipeline:</span></span>

* <span data-ttu-id="18c47-126">[Filtry autorizace](#authorization-filters) se spouštějí jako první a používají se k určení, jestli je uživatel autorizovaný pro požadavek.</span><span class="sxs-lookup"><span data-stu-id="18c47-126">[Authorization filters](#authorization-filters) run first and are used to determine whether the user is authorized for the request.</span></span> <span data-ttu-id="18c47-127">Filtry autorizace pro krátkodobé vynechání kanálu, pokud žádost není autorizována.</span><span class="sxs-lookup"><span data-stu-id="18c47-127">Authorization filters short-circuit the pipeline if the request is not authorized.</span></span>

* <span data-ttu-id="18c47-128">[Filtry prostředků](#resource-filters):</span><span class="sxs-lookup"><span data-stu-id="18c47-128">[Resource filters](#resource-filters):</span></span>

  * <span data-ttu-id="18c47-129">Spusťte po autorizaci.</span><span class="sxs-lookup"><span data-stu-id="18c47-129">Run after authorization.</span></span>  
  * <span data-ttu-id="18c47-130"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*>spustí kód před zbytek kanálu filtru.</span><span class="sxs-lookup"><span data-stu-id="18c47-130"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> runs code before the rest of the filter pipeline.</span></span> <span data-ttu-id="18c47-131">Například `OnResourceExecuting` spustí kód před vazbou modelu.</span><span class="sxs-lookup"><span data-stu-id="18c47-131">For example, `OnResourceExecuting` runs code before model binding.</span></span>
  * <span data-ttu-id="18c47-132"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*>spustí kód po dokončení zbývající části kanálu.</span><span class="sxs-lookup"><span data-stu-id="18c47-132"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> runs code after the rest of the pipeline has completed.</span></span>

* <span data-ttu-id="18c47-133">[Filtry akcí](#action-filters):</span><span class="sxs-lookup"><span data-stu-id="18c47-133">[Action filters](#action-filters):</span></span>

  * <span data-ttu-id="18c47-134">Spustit kód bezprostředně před a po volání metody Action.</span><span class="sxs-lookup"><span data-stu-id="18c47-134">Run code immediately before and after an action method is called.</span></span>
  * <span data-ttu-id="18c47-135">Může změnit argumenty předané do akce.</span><span class="sxs-lookup"><span data-stu-id="18c47-135">Can change the arguments passed into an action.</span></span>
  * <span data-ttu-id="18c47-136">Může změnit výsledek vrácený z akce.</span><span class="sxs-lookup"><span data-stu-id="18c47-136">Can change the result returned from the action.</span></span>
  * <span data-ttu-id="18c47-137">Nejsou **podporovány v** Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="18c47-137">Are **not** supported in Razor Pages.</span></span>

* <span data-ttu-id="18c47-138">[Filtry výjimek](#exception-filters) aplikují globální zásady na neošetřené výjimky, ke kterým došlo před zápisem textu odpovědi do.</span><span class="sxs-lookup"><span data-stu-id="18c47-138">[Exception filters](#exception-filters) apply global policies to unhandled exceptions that occur before the response body has been written to.</span></span>

* <span data-ttu-id="18c47-139">[Filtry výsledků](#result-filters) spouštějí kód bezprostředně před a po provedení výsledků akce.</span><span class="sxs-lookup"><span data-stu-id="18c47-139">[Result filters](#result-filters) run code immediately before and after the execution of action results.</span></span> <span data-ttu-id="18c47-140">Spustí se pouze v případě, že metoda akce byla úspěšně provedena.</span><span class="sxs-lookup"><span data-stu-id="18c47-140">They run only when the action method has executed successfully.</span></span> <span data-ttu-id="18c47-141">Jsou užitečné pro logiku, která musí obklopit zobrazení nebo formátovací modul.</span><span class="sxs-lookup"><span data-stu-id="18c47-141">They are useful for logic that must surround view or formatter execution.</span></span>

<span data-ttu-id="18c47-142">Následující diagram znázorňuje způsob interakce typů filtrů v kanálu filtru.</span><span class="sxs-lookup"><span data-stu-id="18c47-142">The following diagram shows how filter types interact in the filter pipeline.</span></span>

![Požadavek se zpracovává pomocí autorizačních filtrů, filtrů prostředků, vazeb modelů, filtrů akcí, provádění akcí a konverze výsledků akcí, filtrů výjimek, výsledných filtrů a provádění výsledků.](filters/_static/filter-pipeline-2.png)

## <a name="implementation"></a><span data-ttu-id="18c47-145">Implementace</span><span class="sxs-lookup"><span data-stu-id="18c47-145">Implementation</span></span>

<span data-ttu-id="18c47-146">Filtry podporují synchronní i asynchronní implementace prostřednictvím různých definic rozhraní.</span><span class="sxs-lookup"><span data-stu-id="18c47-146">Filters support both synchronous and asynchronous implementations through different interface definitions.</span></span>

<span data-ttu-id="18c47-147">Synchronní filtry spouštějí kód před a po fázi zřetězení.</span><span class="sxs-lookup"><span data-stu-id="18c47-147">Synchronous filters run code before and after their pipeline stage.</span></span> <span data-ttu-id="18c47-148">Například <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*> je volána před voláním metody Action.</span><span class="sxs-lookup"><span data-stu-id="18c47-148">For example, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*> is called before the action method is called.</span></span> <span data-ttu-id="18c47-149"><xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*>je volána po návratu metody Action.</span><span class="sxs-lookup"><span data-stu-id="18c47-149"><xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*> is called after the action method returns.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="18c47-150">Asynchronní filtry definují `On-Stage-ExecutionAsync` metodu.</span><span class="sxs-lookup"><span data-stu-id="18c47-150">Asynchronous filters define an `On-Stage-ExecutionAsync` method.</span></span> <span data-ttu-id="18c47-151">Například <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*>:</span><span class="sxs-lookup"><span data-stu-id="18c47-151">For example, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*>:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/SampleAsyncActionFilter.cs?name=snippet)]

<span data-ttu-id="18c47-152">V předchozím kódu `SampleAsyncActionFilter` obsahuje znak <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`), který provádí metodu Action.</span><span class="sxs-lookup"><span data-stu-id="18c47-152">In the preceding code, the `SampleAsyncActionFilter` has an <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`) that executes the action method.</span></span>

### <a name="multiple-filter-stages"></a><span data-ttu-id="18c47-153">Několik fází filtru</span><span class="sxs-lookup"><span data-stu-id="18c47-153">Multiple filter stages</span></span>

<span data-ttu-id="18c47-154">Rozhraní pro více fází filtru lze implementovat v rámci jedné třídy.</span><span class="sxs-lookup"><span data-stu-id="18c47-154">Interfaces for multiple filter stages can be implemented in a single class.</span></span> <span data-ttu-id="18c47-155">Například <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> třída implementuje:</span><span class="sxs-lookup"><span data-stu-id="18c47-155">For example, the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> class implements:</span></span>

* <span data-ttu-id="18c47-156">Synchronní: <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> a<xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter></span><span class="sxs-lookup"><span data-stu-id="18c47-156">Synchronous: <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> and  <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter></span></span>
* <span data-ttu-id="18c47-157">Asynchronní: <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> a<xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="18c47-157">Asynchronous: <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
* <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>

<span data-ttu-id="18c47-158">Implementujte **buď** synchronní, nebo asynchronní verzi rozhraní filtru, **nikoli** obojí.</span><span class="sxs-lookup"><span data-stu-id="18c47-158">Implement **either** the synchronous or the async version of a filter interface, **not** both.</span></span> <span data-ttu-id="18c47-159">Modul runtime nejprve kontroluje, zda filtr implementuje asynchronní rozhraní a v takovém případě jej zavolá.</span><span class="sxs-lookup"><span data-stu-id="18c47-159">The runtime checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="18c47-160">V takovém případě volá metody synchronního rozhraní.</span><span class="sxs-lookup"><span data-stu-id="18c47-160">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="18c47-161">Pokud jsou asynchronní i synchronní rozhraní implementovány v jedné třídě, je volána pouze asynchronní metoda.</span><span class="sxs-lookup"><span data-stu-id="18c47-161">If both asynchronous and synchronous interfaces are implemented in one class, only the async method is called.</span></span> <span data-ttu-id="18c47-162">Při použití abstraktních tříd <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>, jako je přepsání pouze synchronních metod nebo asynchronní metody pro každý typ filtru.</span><span class="sxs-lookup"><span data-stu-id="18c47-162">When using abstract classes like <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>, override only the synchronous methods or the asynchronous method for each filter type.</span></span>

### <a name="built-in-filter-attributes"></a><span data-ttu-id="18c47-163">Předdefinované atributy filtru</span><span class="sxs-lookup"><span data-stu-id="18c47-163">Built-in filter attributes</span></span>

<span data-ttu-id="18c47-164">ASP.NET Core obsahuje integrované filtry založené na atributech, které mohou být roztříděné a přizpůsobené.</span><span class="sxs-lookup"><span data-stu-id="18c47-164">ASP.NET Core includes built-in attribute-based filters that can be subclassed and customized.</span></span> <span data-ttu-id="18c47-165">Například následující filtr výsledků přidá hlavičku do odpovědi:</span><span class="sxs-lookup"><span data-stu-id="18c47-165">For example, the following result filter adds a header to the response:</span></span>

<a name="add-header-attribute"></a>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/AddHeaderAttribute.cs?name=snippet)]

<span data-ttu-id="18c47-166">Atributy umožňují filtrům přijímat argumenty, jak je znázorněno v předchozím příkladu.</span><span class="sxs-lookup"><span data-stu-id="18c47-166">Attributes allow filters to accept arguments, as shown in the preceding example.</span></span> <span data-ttu-id="18c47-167">Použijte `AddHeaderAttribute` pro metodu Controller nebo Action a zadejte název a hodnotu hlavičky http:</span><span class="sxs-lookup"><span data-stu-id="18c47-167">Apply the `AddHeaderAttribute` to a controller or action method and specify the name and value of the HTTP header:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

<span data-ttu-id="18c47-168">K prohlédnutí hlaviček použijte nástroj, jako je například nástroj pro [vývojáře v prohlížeči](https://developer.mozilla.org/docs/Learn/Common_questions/What_are_browser_developer_tools) .</span><span class="sxs-lookup"><span data-stu-id="18c47-168">Use a tool such as the [browser developer tools](https://developer.mozilla.org/docs/Learn/Common_questions/What_are_browser_developer_tools) to examine the headers.</span></span> <span data-ttu-id="18c47-169">V části **hlavičky odpovědi** `author: Rick Anderson` se zobrazí.</span><span class="sxs-lookup"><span data-stu-id="18c47-169">Under **Response Headers**, `author: Rick Anderson` is displayed.</span></span>

<span data-ttu-id="18c47-170">Následující kód implementuje `ActionFilterAttribute` :</span><span class="sxs-lookup"><span data-stu-id="18c47-170">The following code implements an `ActionFilterAttribute` that:</span></span>

* <span data-ttu-id="18c47-171">Přečte název a název z konfiguračního systému.</span><span class="sxs-lookup"><span data-stu-id="18c47-171">Reads the title and name from the configuration system.</span></span> <span data-ttu-id="18c47-172">Na rozdíl od předchozí ukázky, následující kód nevyžaduje přidání parametrů filtru do kódu.</span><span class="sxs-lookup"><span data-stu-id="18c47-172">Unlike the previous sample, the following code doesn't require filter parameters to be added to the code.</span></span>
* <span data-ttu-id="18c47-173">Přidá název a název do hlavičky odpovědi.</span><span class="sxs-lookup"><span data-stu-id="18c47-173">Adds the title and name to the response header.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MyActionFilterAttribute.cs?name=snippet)]

<span data-ttu-id="18c47-174">Možnosti konfigurace se poskytují z [konfiguračního systému](xref:fundamentals/configuration/index) pomocí [vzoru možností](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="18c47-174">The configuration options are provided from the [configuration system](xref:fundamentals/configuration/index) using the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="18c47-175">Například ze souboru *appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="18c47-175">For example, from the *appsettings.json* file:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/appsettings.json)]

<span data-ttu-id="18c47-176">V `StartUp.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="18c47-176">In the `StartUp.ConfigureServices`:</span></span>

* <span data-ttu-id="18c47-177">`PositionOptions` Třída je přidána do kontejneru služby s oblastí `"Position"` konfigurace.</span><span class="sxs-lookup"><span data-stu-id="18c47-177">The `PositionOptions` class is added to the service container with the `"Position"` configuration area.</span></span>
* <span data-ttu-id="18c47-178">`MyActionFilterAttribute` Přidá se do kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="18c47-178">The `MyActionFilterAttribute` is added to the service container.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupAF.cs?name=snippet)]

<span data-ttu-id="18c47-179">Následující kód ukazuje `PositionOptions` třídu:</span><span class="sxs-lookup"><span data-stu-id="18c47-179">The following code shows the `PositionOptions` class:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Helper/PositionOptions.cs?name=snippet)]

<span data-ttu-id="18c47-180">Následující kód platí `MyActionFilterAttribute` pro `Index2` metodu:</span><span class="sxs-lookup"><span data-stu-id="18c47-180">The following code applies the `MyActionFilterAttribute` to the `Index2` method:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet2&highlight=9)]

<span data-ttu-id="18c47-181">V části `author: Rick Anderson` **hlavičky odpovědi**, a `Editor: Joe Smith` se zobrazí při volání `Sample/Index2` koncového bodu.</span><span class="sxs-lookup"><span data-stu-id="18c47-181">Under **Response Headers**, `author: Rick Anderson`, and `Editor: Joe Smith` is displayed when the `Sample/Index2` endpoint is called.</span></span>

<span data-ttu-id="18c47-182">Následující kód platí `MyActionFilterAttribute` `AddHeaderAttribute` pro a na stránku Razor:</span><span class="sxs-lookup"><span data-stu-id="18c47-182">The following code applies the `MyActionFilterAttribute` and the `AddHeaderAttribute` to the Razor Page:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Pages/Movies/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="18c47-183">Filtry nelze použít pro metody obslužné rutiny stránky Razor.</span><span class="sxs-lookup"><span data-stu-id="18c47-183">Filters cannot be applied to Razor Page handler methods.</span></span> <span data-ttu-id="18c47-184">Mohou být aplikovány buď na model stránky Razor, nebo globálně.</span><span class="sxs-lookup"><span data-stu-id="18c47-184">They can be applied either to the Razor Page model or globally.</span></span>

<span data-ttu-id="18c47-185">Některé z rozhraní filtru mají odpovídající atributy, které lze použít jako základní třídy pro vlastní implementace.</span><span class="sxs-lookup"><span data-stu-id="18c47-185">Several of the filter interfaces have corresponding attributes that can be used as base classes for custom implementations.</span></span>

<span data-ttu-id="18c47-186">Atributy filtru:</span><span class="sxs-lookup"><span data-stu-id="18c47-186">Filter attributes:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ExceptionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>

## <a name="filter-scopes-and-order-of-execution"></a><span data-ttu-id="18c47-187">Filtrovat obory a pořadí provádění</span><span class="sxs-lookup"><span data-stu-id="18c47-187">Filter scopes and order of execution</span></span>

<span data-ttu-id="18c47-188">Filtr lze přidat do kanálu v jednom ze tří *oborů*:</span><span class="sxs-lookup"><span data-stu-id="18c47-188">A filter can be added to the pipeline at one of three *scopes*:</span></span>

* <span data-ttu-id="18c47-189">Použití atributu u akce kontroleru.</span><span class="sxs-lookup"><span data-stu-id="18c47-189">Using an attribute on a controller action.</span></span> <span data-ttu-id="18c47-190">Atributy filtru nelze použít pro Razor Pages metody obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="18c47-190">Filter attributes cannot be applied to Razor Pages handler methods.</span></span>
* <span data-ttu-id="18c47-191">Použití atributu na stránce kontroleru nebo Razor.</span><span class="sxs-lookup"><span data-stu-id="18c47-191">Using an attribute on a controller or Razor Page.</span></span>
* <span data-ttu-id="18c47-192">Globálně pro všechny řadiče, akce a Razor Pages, jak je znázorněno v následujícím kódu:</span><span class="sxs-lookup"><span data-stu-id="18c47-192">Globally for all controllers, actions, and Razor Pages as shown in the following code:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder.cs?name=snippet)]

### <a name="default-order-of-execution"></a><span data-ttu-id="18c47-193">Výchozí pořadí provádění</span><span class="sxs-lookup"><span data-stu-id="18c47-193">Default order of execution</span></span>

<span data-ttu-id="18c47-194">Pokud existuje více filtrů pro určitou fázi kanálu, rozsah Určuje výchozí pořadí provádění filtru.</span><span class="sxs-lookup"><span data-stu-id="18c47-194">When there are multiple filters for a particular stage of the pipeline, scope determines the default order of filter execution.</span></span>  <span data-ttu-id="18c47-195">Globální filtry obklopují filtry tříd, které jsou ve filtru metod Surround.</span><span class="sxs-lookup"><span data-stu-id="18c47-195">Global filters surround class filters, which in turn surround method filters.</span></span>

<span data-ttu-id="18c47-196">V důsledku vnořování filtru je *po* kódu spuštěn v obráceném pořadí *před* kódem.</span><span class="sxs-lookup"><span data-stu-id="18c47-196">As a result of filter nesting, the *after* code of filters runs in the reverse order of the *before* code.</span></span> <span data-ttu-id="18c47-197">Pořadí filtru:</span><span class="sxs-lookup"><span data-stu-id="18c47-197">The filter sequence:</span></span>

* <span data-ttu-id="18c47-198">*Před* kódem globálních filtrů.</span><span class="sxs-lookup"><span data-stu-id="18c47-198">The *before* code of global filters.</span></span>
  * <span data-ttu-id="18c47-199">Kód *před* filtrem stránky kontroleru a Razor.</span><span class="sxs-lookup"><span data-stu-id="18c47-199">The *before* code of controller and Razor Page filters.</span></span>
    * <span data-ttu-id="18c47-200">Kód *před* filtry metody Action.</span><span class="sxs-lookup"><span data-stu-id="18c47-200">The *before* code of action method filters.</span></span>
    * <span data-ttu-id="18c47-201">*Po* kódu filtry metody Action.</span><span class="sxs-lookup"><span data-stu-id="18c47-201">The *after* code of action method filters.</span></span>
  * <span data-ttu-id="18c47-202">Filtr *po* kódu řadiče a stránky Razor.</span><span class="sxs-lookup"><span data-stu-id="18c47-202">The *after* code of controller and Razor Page filters.</span></span>
* <span data-ttu-id="18c47-203">*Po* kódu globálních filtrů.</span><span class="sxs-lookup"><span data-stu-id="18c47-203">The *after* code of global filters.</span></span>
  
<span data-ttu-id="18c47-204">Následující příklad ilustruje pořadí, ve kterém jsou metody filtru volány pro filtry synchronních akcí.</span><span class="sxs-lookup"><span data-stu-id="18c47-204">The following example that illustrates the order in which filter methods are called for synchronous action filters.</span></span>

| <span data-ttu-id="18c47-205">Sequence</span><span class="sxs-lookup"><span data-stu-id="18c47-205">Sequence</span></span> | <span data-ttu-id="18c47-206">Rozsah filtru</span><span class="sxs-lookup"><span data-stu-id="18c47-206">Filter scope</span></span> | <span data-ttu-id="18c47-207">Filter – metoda</span><span class="sxs-lookup"><span data-stu-id="18c47-207">Filter method</span></span> |
|:--------:|:------------:|:-------------:|
| <span data-ttu-id="18c47-208">1</span><span class="sxs-lookup"><span data-stu-id="18c47-208">1</span></span> | <span data-ttu-id="18c47-209">Globální</span><span class="sxs-lookup"><span data-stu-id="18c47-209">Global</span></span> | `OnActionExecuting` |
| <span data-ttu-id="18c47-210">2</span><span class="sxs-lookup"><span data-stu-id="18c47-210">2</span></span> | <span data-ttu-id="18c47-211">Kontroler nebo stránka Razor</span><span class="sxs-lookup"><span data-stu-id="18c47-211">Controller or Razor Page</span></span>| `OnActionExecuting` |
| <span data-ttu-id="18c47-212">3</span><span class="sxs-lookup"><span data-stu-id="18c47-212">3</span></span> | <span data-ttu-id="18c47-213">Metoda</span><span class="sxs-lookup"><span data-stu-id="18c47-213">Method</span></span> | `OnActionExecuting` |
| <span data-ttu-id="18c47-214">4</span><span class="sxs-lookup"><span data-stu-id="18c47-214">4</span></span> | <span data-ttu-id="18c47-215">Metoda</span><span class="sxs-lookup"><span data-stu-id="18c47-215">Method</span></span> | `OnActionExecuted` |
| <span data-ttu-id="18c47-216">5</span><span class="sxs-lookup"><span data-stu-id="18c47-216">5</span></span> | <span data-ttu-id="18c47-217">Kontroler nebo stránka Razor</span><span class="sxs-lookup"><span data-stu-id="18c47-217">Controller or Razor Page</span></span> | `OnActionExecuted` |
| <span data-ttu-id="18c47-218">6</span><span class="sxs-lookup"><span data-stu-id="18c47-218">6</span></span> | <span data-ttu-id="18c47-219">Globální</span><span class="sxs-lookup"><span data-stu-id="18c47-219">Global</span></span> | `OnActionExecuted` |

### <a name="controller-level-filters"></a><span data-ttu-id="18c47-220">Filtry na úrovni řadiče</span><span class="sxs-lookup"><span data-stu-id="18c47-220">Controller level filters</span></span>

<span data-ttu-id="18c47-221">Každý kontroler, který dědí <xref:Microsoft.AspNetCore.Mvc.Controller> ze základní třídy, zahrnuje metody [Controller. OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*), [Controller. OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*)a [Controller. OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*) 
 `OnActionExecuted` .</span><span class="sxs-lookup"><span data-stu-id="18c47-221">Every controller that inherits from the <xref:Microsoft.AspNetCore.Mvc.Controller> base class includes [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*),  [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*), and [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted` methods.</span></span> <span data-ttu-id="18c47-222">Tyto metody:</span><span class="sxs-lookup"><span data-stu-id="18c47-222">These methods:</span></span>

* <span data-ttu-id="18c47-223">Zabalte filtry, které se spouštějí pro danou akci.</span><span class="sxs-lookup"><span data-stu-id="18c47-223">Wrap the filters that run for a given action.</span></span>
* <span data-ttu-id="18c47-224">`OnActionExecuting`je volána před jakýmkoli filtrem akce.</span><span class="sxs-lookup"><span data-stu-id="18c47-224">`OnActionExecuting` is called before any of the action's filters.</span></span>
* <span data-ttu-id="18c47-225">`OnActionExecuted`se volá po všech filtrech akcí.</span><span class="sxs-lookup"><span data-stu-id="18c47-225">`OnActionExecuted` is called after all of the action filters.</span></span>
* <span data-ttu-id="18c47-226">`OnActionExecutionAsync`je volána před jakýmkoli filtrem akce.</span><span class="sxs-lookup"><span data-stu-id="18c47-226">`OnActionExecutionAsync` is called before any of the action's filters.</span></span> <span data-ttu-id="18c47-227">Kód v filtru po `next` spuštění po metodě Action.</span><span class="sxs-lookup"><span data-stu-id="18c47-227">Code in the filter after `next` runs after the action method.</span></span>

<span data-ttu-id="18c47-228">Například v ukázce ke stažení `MySampleActionFilter` se použije globálně při spuštění.</span><span class="sxs-lookup"><span data-stu-id="18c47-228">For example, in the download sample, `MySampleActionFilter` is applied globally in startup.</span></span>

<span data-ttu-id="18c47-229">`TestController`:</span><span class="sxs-lookup"><span data-stu-id="18c47-229">The `TestController`:</span></span>

* <span data-ttu-id="18c47-230">Aplikuje `SampleActionFilterAttribute` (`[SampleActionFilter]`) na `FilterTest2` akci.</span><span class="sxs-lookup"><span data-stu-id="18c47-230">Applies the `SampleActionFilterAttribute` (`[SampleActionFilter]`) to the `FilterTest2` action.</span></span>
* <span data-ttu-id="18c47-231">Přepisuje `OnActionExecuting` a `OnActionExecuted`.</span><span class="sxs-lookup"><span data-stu-id="18c47-231">Overrides `OnActionExecuting` and `OnActionExecuted`.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/TestController.cs?name=snippet)]

<!-- test via  webBuilder.UseStartup<Startup>(); -->

<span data-ttu-id="18c47-232">Navigace pro `https://localhost:5001/Test2/FilterTest2` spustí následující kód:</span><span class="sxs-lookup"><span data-stu-id="18c47-232">Navigating to `https://localhost:5001/Test2/FilterTest2` runs the following code:</span></span>

* `TestController.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `SampleActionFilterAttribute.OnActionExecuting`
      * `TestController.FilterTest2`
    * `SampleActionFilterAttribute.OnActionExecuted`
  * `MySampleActionFilter.OnActionExecuted`
* `TestController.OnActionExecuted`

<span data-ttu-id="18c47-233">Filtry na úrovni řadiče nastaví vlastnost [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) na `int.MinValue`.</span><span class="sxs-lookup"><span data-stu-id="18c47-233">Controller level filters set the [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) property to `int.MinValue`.</span></span> <span data-ttu-id="18c47-234">Filtry na úrovni řadiče **nelze nastavit** tak, aby běžely po filtrech použitých na metody.</span><span class="sxs-lookup"><span data-stu-id="18c47-234">Controller level filters can **not** be set to run after filters applied to methods.</span></span> <span data-ttu-id="18c47-235">Pořadí je vysvětleno v další části.</span><span class="sxs-lookup"><span data-stu-id="18c47-235">Order is explained in the next section.</span></span>

<span data-ttu-id="18c47-236">Razor Pages najdete v tématu [implementace filtrů stránek Razor pomocí přepsání metod filtru](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span><span class="sxs-lookup"><span data-stu-id="18c47-236">For Razor Pages, see [Implement Razor Page filters by overriding filter methods](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span></span>

### <a name="overriding-the-default-order"></a><span data-ttu-id="18c47-237">Přepsání výchozího pořadí</span><span class="sxs-lookup"><span data-stu-id="18c47-237">Overriding the default order</span></span>

<span data-ttu-id="18c47-238">Výchozí sekvenci spuštění lze přepsat implementací <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span><span class="sxs-lookup"><span data-stu-id="18c47-238">The default sequence of execution can be overridden by implementing <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span></span> <span data-ttu-id="18c47-239">`IOrderedFilter`zpřístupňuje <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> vlastnost, která má přednost před rozsahem, aby určila pořadí provádění.</span><span class="sxs-lookup"><span data-stu-id="18c47-239">`IOrderedFilter` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> property that takes precedence over scope to determine the order of execution.</span></span> <span data-ttu-id="18c47-240">Filtr s nižší `Order` hodnotou:</span><span class="sxs-lookup"><span data-stu-id="18c47-240">A filter with a lower `Order` value:</span></span>

* <span data-ttu-id="18c47-241">Spustí *před* kódem filtr s vyšší hodnotou `Order`.</span><span class="sxs-lookup"><span data-stu-id="18c47-241">Runs the *before* code before that of a filter with a higher value of `Order`.</span></span>
* <span data-ttu-id="18c47-242">Spustí *za* kódem za filtr s vyšší `Order` hodnotou.</span><span class="sxs-lookup"><span data-stu-id="18c47-242">Runs the *after* code after that of a filter with a higher `Order` value.</span></span>

<span data-ttu-id="18c47-243">`Order` Vlastnost je nastavena s parametrem konstruktoru:</span><span class="sxs-lookup"><span data-stu-id="18c47-243">The `Order` property is set with a constructor parameter:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test3Controller.cs?name=snippet)]

<span data-ttu-id="18c47-244">Vezměte v úvahu dva filtry akcí v následujícím kontroleru:</span><span class="sxs-lookup"><span data-stu-id="18c47-244">Consider the two action filters in the following controller:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test2Controller.cs?name=snippet)]

<span data-ttu-id="18c47-245">Globální filtr je přidaný v `StartUp.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="18c47-245">A global filter is added in `StartUp.ConfigureServices`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder.cs?name=snippet)]

<span data-ttu-id="18c47-246">Tři filtry jsou spouštěny v následujícím pořadí:</span><span class="sxs-lookup"><span data-stu-id="18c47-246">The 3 filters run in the following order:</span></span>

* `Test2Controller.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `MyAction2FilterAttribute.OnActionExecuting`
      * `Test2Controller.FilterTest2`
    * `MySampleActionFilter.OnActionExecuted`
  * `MyAction2FilterAttribute.OnResultExecuting`
* `Test2Controller.OnActionExecuted`

<span data-ttu-id="18c47-247">`Order` Vlastnost Přepisuje obor při určování pořadí, ve kterém jsou filtry spouštěny.</span><span class="sxs-lookup"><span data-stu-id="18c47-247">The `Order` property overrides scope when determining the order in which filters run.</span></span> <span data-ttu-id="18c47-248">Filtry jsou seřazené podle pořadí, pak se k přerušení vztahů používá obor.</span><span class="sxs-lookup"><span data-stu-id="18c47-248">Filters are sorted first by order, then scope is used to break ties.</span></span> <span data-ttu-id="18c47-249">Všechny předdefinované filtry implementují `IOrderedFilter` a nastaví výchozí `Order` hodnotu na 0.</span><span class="sxs-lookup"><span data-stu-id="18c47-249">All of the built-in filters implement `IOrderedFilter` and set the default `Order` value to 0.</span></span> <span data-ttu-id="18c47-250">Jak už bylo uvedeno výše, filtry na úrovni [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) řadiče nastaví vlastnost `int.MinValue` Order na pro předdefinované filtry. Scope určí pořadí, `Order` Pokud není nastavená na nenulovou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="18c47-250">As mentioned previously, controller level filters set the [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) property to `int.MinValue` For built-in filters, scope determines order unless `Order` is set to a non-zero value.</span></span>

<span data-ttu-id="18c47-251">V předchozím kódu `MySampleActionFilter` má globální rozsah, takže se spustí před `MyAction2FilterAttribute`, který má obor kontroleru.</span><span class="sxs-lookup"><span data-stu-id="18c47-251">In the preceding code, `MySampleActionFilter` has global scope so it runs before `MyAction2FilterAttribute`, which has controller scope.</span></span> <span data-ttu-id="18c47-252">Aby bylo `MyAction2FilterAttribute` možné spustit jako první, nastavte pořadí `int.MinValue`na:</span><span class="sxs-lookup"><span data-stu-id="18c47-252">To make `MyAction2FilterAttribute` run first, set the order to `int.MinValue`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test2Controller.cs?name=snippet2)]

<span data-ttu-id="18c47-253">Aby se globální filtr `MySampleActionFilter` spouštěl jako první, nastavte `Order` na `int.MinValue`:</span><span class="sxs-lookup"><span data-stu-id="18c47-253">To make the global filter `MySampleActionFilter` run first, set `Order` to `int.MinValue`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder2.cs?name=snippet&highlight=6)]

## <a name="cancellation-and-short-circuiting"></a><span data-ttu-id="18c47-254">Zrušení a zkrácení okruhů</span><span class="sxs-lookup"><span data-stu-id="18c47-254">Cancellation and short-circuiting</span></span>

<span data-ttu-id="18c47-255">Kanál filtru může být v krátkém okruhu nastavením <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> vlastnosti pro <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> parametr poskytnutý metodě Filter.</span><span class="sxs-lookup"><span data-stu-id="18c47-255">The filter pipeline can be short-circuited by setting the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> property on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> parameter provided to the filter method.</span></span> <span data-ttu-id="18c47-256">Například následující filtr prostředků zabrání ve spuštění zbývajícího kanálu:</span><span class="sxs-lookup"><span data-stu-id="18c47-256">For instance, the following Resource filter prevents the rest of the pipeline from executing:</span></span>

<a name="short-circuiting-resource-filter"></a>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ShortCircuitingResourceFilterAttribute.cs?name=snippet)]

<span data-ttu-id="18c47-257">V následujícím kódu, `ShortCircuitingResourceFilter` a jako `AddHeader` cíl filtru i cílovou metodu `SomeResource` Action.</span><span class="sxs-lookup"><span data-stu-id="18c47-257">In the following code, both the `ShortCircuitingResourceFilter` and the `AddHeader` filter target the `SomeResource` action method.</span></span> <span data-ttu-id="18c47-258">`ShortCircuitingResourceFilter`:</span><span class="sxs-lookup"><span data-stu-id="18c47-258">The `ShortCircuitingResourceFilter`:</span></span>

* <span data-ttu-id="18c47-259">Nejprve se spustí, protože se jedná o filtr prostředků `AddHeader` a je filtr akcí.</span><span class="sxs-lookup"><span data-stu-id="18c47-259">Runs first, because it's a Resource Filter and `AddHeader` is an Action Filter.</span></span>
* <span data-ttu-id="18c47-260">Krátké okruhy zbývajícího kanálu.</span><span class="sxs-lookup"><span data-stu-id="18c47-260">Short-circuits the rest of the pipeline.</span></span>

<span data-ttu-id="18c47-261">Proto `AddHeader` filtr pro `SomeResource` akci nikdy neběží.</span><span class="sxs-lookup"><span data-stu-id="18c47-261">Therefore the `AddHeader` filter never runs for the `SomeResource` action.</span></span> <span data-ttu-id="18c47-262">Toto chování by bylo stejné, pokud byly oba filtry aplikovány na úrovni metody akce, a to `ShortCircuitingResourceFilter` za předpokladu, že byla spuštěna jako první.</span><span class="sxs-lookup"><span data-stu-id="18c47-262">This behavior would be the same if both filters were applied at the action method level, provided the `ShortCircuitingResourceFilter` ran first.</span></span> <span data-ttu-id="18c47-263">Nejprve `ShortCircuitingResourceFilter` se spustí z důvodu jeho typu filtru nebo explicitního použití `Order` vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="18c47-263">The `ShortCircuitingResourceFilter` runs first because of its filter type, or by explicit use of `Order` property.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

## <a name="dependency-injection"></a><span data-ttu-id="18c47-264">Injektáž závislostí</span><span class="sxs-lookup"><span data-stu-id="18c47-264">Dependency injection</span></span>

<span data-ttu-id="18c47-265">Filtry lze přidat podle typu nebo podle instance.</span><span class="sxs-lookup"><span data-stu-id="18c47-265">Filters can be added by type or by instance.</span></span> <span data-ttu-id="18c47-266">Pokud je přidána instance, bude tato instance použita pro každý požadavek.</span><span class="sxs-lookup"><span data-stu-id="18c47-266">If an instance is added, that instance is used for every request.</span></span> <span data-ttu-id="18c47-267">Pokud je přidán typ, je aktivován typ.</span><span class="sxs-lookup"><span data-stu-id="18c47-267">If a type is added, it's type-activated.</span></span> <span data-ttu-id="18c47-268">Filtr aktivovaný typu znamená:</span><span class="sxs-lookup"><span data-stu-id="18c47-268">A type-activated filter means:</span></span>

* <span data-ttu-id="18c47-269">Instance se vytvoří pro každý požadavek.</span><span class="sxs-lookup"><span data-stu-id="18c47-269">An instance is created for each request.</span></span>
* <span data-ttu-id="18c47-270">Jakékoli závislosti konstruktoru jsou vyplněny pomocí [Injektáže závislosti](xref:fundamentals/dependency-injection) (di).</span><span class="sxs-lookup"><span data-stu-id="18c47-270">Any constructor dependencies are populated by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span>

<span data-ttu-id="18c47-271">Filtry, které jsou implementovány jako atributy a přidány přímo do tříd kontroleru nebo metody akcí, nemohou mít závislosti konstruktoru poskytované [vkládáním závislostí](xref:fundamentals/dependency-injection) (di).</span><span class="sxs-lookup"><span data-stu-id="18c47-271">Filters that are implemented as attributes and added directly to controller classes or action methods cannot have constructor dependencies provided by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span> <span data-ttu-id="18c47-272">Pomocí DI nelze poskytnout závislosti konstruktoru:</span><span class="sxs-lookup"><span data-stu-id="18c47-272">Constructor dependencies cannot be provided by DI because:</span></span>

* <span data-ttu-id="18c47-273">V atributech musí být zadány parametry konstruktoru, kde jsou aplikovány.</span><span class="sxs-lookup"><span data-stu-id="18c47-273">Attributes must have their constructor parameters supplied where they're applied.</span></span> 
* <span data-ttu-id="18c47-274">Toto je omezení způsobu fungování atributů.</span><span class="sxs-lookup"><span data-stu-id="18c47-274">This is a limitation of how attributes work.</span></span>

<span data-ttu-id="18c47-275">Následující filtry podporují závislosti konstruktoru poskytované od DI:</span><span class="sxs-lookup"><span data-stu-id="18c47-275">The following filters support constructor dependencies provided from DI:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>
* <span data-ttu-id="18c47-276"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>implementováno u atributu.</span><span class="sxs-lookup"><span data-stu-id="18c47-276"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implemented on the attribute.</span></span>

<span data-ttu-id="18c47-277">Předchozí filtry lze použít pro metodu kontroleru nebo akce:</span><span class="sxs-lookup"><span data-stu-id="18c47-277">The preceding filters can be applied to a controller or action method:</span></span>

<span data-ttu-id="18c47-278">Protokolovací nástroje jsou k dispozici z DI.</span><span class="sxs-lookup"><span data-stu-id="18c47-278">Loggers are available from DI.</span></span> <span data-ttu-id="18c47-279">Ale nevytvářejte a používejte filtry čistě pro účely protokolování.</span><span class="sxs-lookup"><span data-stu-id="18c47-279">However, avoid creating and using filters purely for logging purposes.</span></span> <span data-ttu-id="18c47-280">[Integrované protokolování rozhraní](xref:fundamentals/logging/index) obvykle zajišťuje, co je potřeba pro protokolování.</span><span class="sxs-lookup"><span data-stu-id="18c47-280">The [built-in framework logging](xref:fundamentals/logging/index) typically provides what's needed for logging.</span></span> <span data-ttu-id="18c47-281">Protokolování přidáno do filtrů:</span><span class="sxs-lookup"><span data-stu-id="18c47-281">Logging added to filters:</span></span>

* <span data-ttu-id="18c47-282">Měl by se soustředit na obavy a chování obchodních domén, které jsou specifické pro filtr.</span><span class="sxs-lookup"><span data-stu-id="18c47-282">Should focus on business domain concerns or behavior specific to the filter.</span></span>
* <span data-ttu-id="18c47-283">Neměli **byste** protokolovat akce nebo jiné události rozhraní.</span><span class="sxs-lookup"><span data-stu-id="18c47-283">Should **not** log actions or other framework events.</span></span> <span data-ttu-id="18c47-284">Vestavěné filtry protokolují akce a události rozhraní.</span><span class="sxs-lookup"><span data-stu-id="18c47-284">The built-in filters log actions and framework events.</span></span>

### <a name="servicefilterattribute"></a><span data-ttu-id="18c47-285">ServiceFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="18c47-285">ServiceFilterAttribute</span></span>

<span data-ttu-id="18c47-286">Typy implementace filtru služby jsou zaregistrované v `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="18c47-286">Service filter implementation types are registered in `ConfigureServices`.</span></span> <span data-ttu-id="18c47-287"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> Načte instanci filtru z di.</span><span class="sxs-lookup"><span data-stu-id="18c47-287">A <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> retrieves an instance of the filter from DI.</span></span>

<span data-ttu-id="18c47-288">Následující kód ukazuje `AddHeaderResultServiceFilter`:</span><span class="sxs-lookup"><span data-stu-id="18c47-288">The following code shows the `AddHeaderResultServiceFilter`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="18c47-289">V následujícím kódu `AddHeaderResultServiceFilter` je přidán do kontejneru di:</span><span class="sxs-lookup"><span data-stu-id="18c47-289">In the following code, `AddHeaderResultServiceFilter` is added to the DI container:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Startup.cs?name=snippet&highlight=4)]

<span data-ttu-id="18c47-290">V následujícím kódu `ServiceFilter` atribut načte instanci `AddHeaderResultServiceFilter` filtru z di:</span><span class="sxs-lookup"><span data-stu-id="18c47-290">In the following code, the `ServiceFilter` attribute retrieves an instance of the `AddHeaderResultServiceFilter` filter from DI:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_ServiceFilter&highlight=1)]

<span data-ttu-id="18c47-291">Při použití `ServiceFilterAttribute`nastavte [ServiceFilterAttribute. IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span><span class="sxs-lookup"><span data-stu-id="18c47-291">When using `ServiceFilterAttribute`, setting [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span></span>

* <span data-ttu-id="18c47-292">Poskytuje nápovědu, že se instance filtru *dá* znovu použít mimo rozsah požadavku, který byl vytvořen v rámci.</span><span class="sxs-lookup"><span data-stu-id="18c47-292">Provides a hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="18c47-293">Modul runtime ASP.NET Core nezaručuje:</span><span class="sxs-lookup"><span data-stu-id="18c47-293">The ASP.NET Core runtime doesn't guarantee:</span></span>

  * <span data-ttu-id="18c47-294">Vytvoří se jedna instance filtru.</span><span class="sxs-lookup"><span data-stu-id="18c47-294">That a single instance of the filter will be created.</span></span>
  * <span data-ttu-id="18c47-295">Filtr nebude znovu vyžádán z kontejneru DI v pozdějším bodě.</span><span class="sxs-lookup"><span data-stu-id="18c47-295">The filter will not be re-requested from the DI container at some later point.</span></span>

* <span data-ttu-id="18c47-296">Neměl by se používat s filtrem, který závisí na službách s jinou životností než singleton.</span><span class="sxs-lookup"><span data-stu-id="18c47-296">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

 <span data-ttu-id="18c47-297"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>implementuje <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span><span class="sxs-lookup"><span data-stu-id="18c47-297"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="18c47-298">`IFilterFactory`zpřístupňuje <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> metodu pro vytvoření <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span><span class="sxs-lookup"><span data-stu-id="18c47-298">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="18c47-299">`CreateInstance`Načte zadaný typ z DI.</span><span class="sxs-lookup"><span data-stu-id="18c47-299">`CreateInstance` loads the specified type from DI.</span></span>

### <a name="typefilterattribute"></a><span data-ttu-id="18c47-300">TypeFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="18c47-300">TypeFilterAttribute</span></span>

<span data-ttu-id="18c47-301"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>je podobný <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, ale jeho typ není vyřešen přímo z kontejneru di.</span><span class="sxs-lookup"><span data-stu-id="18c47-301"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> is similar to <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, but its type isn't resolved directly from the DI container.</span></span> <span data-ttu-id="18c47-302">Vytvoří instanci typu pomocí <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="18c47-302">It instantiates the type by using <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span></span>

<span data-ttu-id="18c47-303">Protože `TypeFilterAttribute` typy nejsou vyřešeny přímo z kontejneru di:</span><span class="sxs-lookup"><span data-stu-id="18c47-303">Because `TypeFilterAttribute` types aren't resolved directly from the DI container:</span></span>

* <span data-ttu-id="18c47-304">Typy, na které se odkazuje `TypeFilterAttribute` pomocí, není nutné registrovat v kontejneru di.</span><span class="sxs-lookup"><span data-stu-id="18c47-304">Types that are referenced using the `TypeFilterAttribute` don't need to be registered with the DI container.</span></span>  <span data-ttu-id="18c47-305">Mají své závislosti splněné kontejnerem DI.</span><span class="sxs-lookup"><span data-stu-id="18c47-305">They do have their dependencies fulfilled by the DI container.</span></span>
* <span data-ttu-id="18c47-306">`TypeFilterAttribute`může volitelně přijmout argumenty konstruktoru pro typ.</span><span class="sxs-lookup"><span data-stu-id="18c47-306">`TypeFilterAttribute` can optionally accept constructor arguments for the type.</span></span>

<span data-ttu-id="18c47-307">Při použití `TypeFilterAttribute`nastavte [TypeFilterAttribute. IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span><span class="sxs-lookup"><span data-stu-id="18c47-307">When using `TypeFilterAttribute`, setting [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span></span>
* <span data-ttu-id="18c47-308">Poskytuje nápovědu, že se instance filtru *dá* znovu použít mimo rozsah požadavku, který byl vytvořen v rámci.</span><span class="sxs-lookup"><span data-stu-id="18c47-308">Provides hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="18c47-309">Modul runtime ASP.NET Core neposkytuje žádné záruky, že se vytvoří jediná instance filtru.</span><span class="sxs-lookup"><span data-stu-id="18c47-309">The ASP.NET Core runtime provides no guarantees that a single instance of the filter will be created.</span></span>

* <span data-ttu-id="18c47-310">Neměl by se používat s filtrem, který závisí na službách s jinou životností než singleton.</span><span class="sxs-lookup"><span data-stu-id="18c47-310">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

<span data-ttu-id="18c47-311">Následující příklad ukazuje, jak předat argumenty typu pomocí `TypeFilterAttribute`:</span><span class="sxs-lookup"><span data-stu-id="18c47-311">The following example shows how to pass arguments to a type using `TypeFilterAttribute`:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_TypeFilter&highlight=1,2)]

<!-- 
https://localhost:5001/home/hi?name=joe
VS debug window shows 
FiltersSample.Filters.LogConstantFilter:Information: Method 'Hi' called
-->

## <a name="authorization-filters"></a><span data-ttu-id="18c47-312">Filtry autorizace</span><span class="sxs-lookup"><span data-stu-id="18c47-312">Authorization filters</span></span>

<span data-ttu-id="18c47-313">Filtry autorizace:</span><span class="sxs-lookup"><span data-stu-id="18c47-313">Authorization filters:</span></span>

* <span data-ttu-id="18c47-314">Jsou první filtry spouštěny v kanálu filtru.</span><span class="sxs-lookup"><span data-stu-id="18c47-314">Are the first filters run in the filter pipeline.</span></span>
* <span data-ttu-id="18c47-315">Řízení přístupu k metodám akcí.</span><span class="sxs-lookup"><span data-stu-id="18c47-315">Control access to action methods.</span></span>
* <span data-ttu-id="18c47-316">Před metodou, ale ne po metodě.</span><span class="sxs-lookup"><span data-stu-id="18c47-316">Have a before method, but no after method.</span></span>

<span data-ttu-id="18c47-317">Vlastní autorizační filtry vyžadují vlastní autorizační rozhraní.</span><span class="sxs-lookup"><span data-stu-id="18c47-317">Custom authorization filters require a custom authorization framework.</span></span> <span data-ttu-id="18c47-318">Preferovat konfiguraci autorizačních zásad nebo psaní vlastních zásad autorizace při psaní vlastního filtru.</span><span class="sxs-lookup"><span data-stu-id="18c47-318">Prefer configuring the authorization policies or writing a custom authorization policy over writing a custom filter.</span></span> <span data-ttu-id="18c47-319">Vestavěný autorizační filtr:</span><span class="sxs-lookup"><span data-stu-id="18c47-319">The built-in authorization filter:</span></span>

* <span data-ttu-id="18c47-320">Volá autorizační systém.</span><span class="sxs-lookup"><span data-stu-id="18c47-320">Calls the authorization system.</span></span>
* <span data-ttu-id="18c47-321">Neautorizuje požadavky.</span><span class="sxs-lookup"><span data-stu-id="18c47-321">Does not authorize requests.</span></span>

<span data-ttu-id="18c47-322">Negenerovat **výjimky** v rámci autorizačních filtrů:</span><span class="sxs-lookup"><span data-stu-id="18c47-322">Do **not** throw exceptions within authorization filters:</span></span>

* <span data-ttu-id="18c47-323">Výjimka nebude zpracována.</span><span class="sxs-lookup"><span data-stu-id="18c47-323">The exception will not be handled.</span></span>
* <span data-ttu-id="18c47-324">Filtry výjimek nebudou zpracovávat výjimku.</span><span class="sxs-lookup"><span data-stu-id="18c47-324">Exception filters will not handle the exception.</span></span>

<span data-ttu-id="18c47-325">Zvažte vydání výzvy, pokud dojde k výjimce ve autorizačním filtru.</span><span class="sxs-lookup"><span data-stu-id="18c47-325">Consider issuing a challenge when an exception occurs in an authorization filter.</span></span>

<span data-ttu-id="18c47-326">Přečtěte si další informace o [autorizaci](xref:security/authorization/introduction).</span><span class="sxs-lookup"><span data-stu-id="18c47-326">Learn more about [Authorization](xref:security/authorization/introduction).</span></span>

## <a name="resource-filters"></a><span data-ttu-id="18c47-327">Filtry prostředků</span><span class="sxs-lookup"><span data-stu-id="18c47-327">Resource filters</span></span>

<span data-ttu-id="18c47-328">Filtry prostředků:</span><span class="sxs-lookup"><span data-stu-id="18c47-328">Resource filters:</span></span>

* <span data-ttu-id="18c47-329">Implementujte rozhraní <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> nebo <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> .</span><span class="sxs-lookup"><span data-stu-id="18c47-329">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> interface.</span></span>
* <span data-ttu-id="18c47-330">Provádění zabalí většinu kanálu filtru.</span><span class="sxs-lookup"><span data-stu-id="18c47-330">Execution wraps most of the filter pipeline.</span></span>
* <span data-ttu-id="18c47-331">Před filtry prostředků se spouštějí jenom [filtry autorizace](#authorization-filters) .</span><span class="sxs-lookup"><span data-stu-id="18c47-331">Only [Authorization filters](#authorization-filters) run before resource filters.</span></span>

<span data-ttu-id="18c47-332">Filtry prostředků jsou užitečné pro krátkodobé okruhy v kanálu.</span><span class="sxs-lookup"><span data-stu-id="18c47-332">Resource filters are useful to short-circuit most of the pipeline.</span></span> <span data-ttu-id="18c47-333">Například filtr ukládání do mezipaměti může zabránit zbývajícímu kanálu v případě přístupů do mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="18c47-333">For example, a caching filter can avoid the rest of the pipeline on a cache hit.</span></span>

<span data-ttu-id="18c47-334">Příklady filtru prostředků:</span><span class="sxs-lookup"><span data-stu-id="18c47-334">Resource filter examples:</span></span>

* <span data-ttu-id="18c47-335">[Filtr prostředků se zkrácenými okruhy](#short-circuiting-resource-filter) zobrazenými dříve.</span><span class="sxs-lookup"><span data-stu-id="18c47-335">[The short-circuiting resource filter](#short-circuiting-resource-filter) shown previously.</span></span>
* <span data-ttu-id="18c47-336">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span><span class="sxs-lookup"><span data-stu-id="18c47-336">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span></span>

  * <span data-ttu-id="18c47-337">Zabraňuje vazbě modelu v přístupu k datům formuláře.</span><span class="sxs-lookup"><span data-stu-id="18c47-337">Prevents model binding from accessing the form data.</span></span>
  * <span data-ttu-id="18c47-338">Používá se pro nahrávání velkých souborů, aby se zabránilo čtení dat z formuláře do paměti.</span><span class="sxs-lookup"><span data-stu-id="18c47-338">Used for large file uploads to prevent the form data from being read into memory.</span></span>

## <a name="action-filters"></a><span data-ttu-id="18c47-339">Filtry akcí</span><span class="sxs-lookup"><span data-stu-id="18c47-339">Action filters</span></span>

<span data-ttu-id="18c47-340">Filtry akcí **se** nevztahují na Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="18c47-340">Action filters do **not** apply to Razor Pages.</span></span> <span data-ttu-id="18c47-341">Razor Pages podporuje <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> a <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span><span class="sxs-lookup"><span data-stu-id="18c47-341">Razor Pages supports <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span></span> <span data-ttu-id="18c47-342">Další informace naleznete v tématu [metody Filter pro Razor Pages](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="18c47-342">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

<span data-ttu-id="18c47-343">Filtry akcí:</span><span class="sxs-lookup"><span data-stu-id="18c47-343">Action filters:</span></span>

* <span data-ttu-id="18c47-344">Implementujte rozhraní <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> nebo <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> .</span><span class="sxs-lookup"><span data-stu-id="18c47-344">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> interface.</span></span>
* <span data-ttu-id="18c47-345">Jejich spuštění obklopuje provádění metod akcí.</span><span class="sxs-lookup"><span data-stu-id="18c47-345">Their execution surrounds the execution of action methods.</span></span>

<span data-ttu-id="18c47-346">Následující kód ukazuje filtr vzorové akce:</span><span class="sxs-lookup"><span data-stu-id="18c47-346">The following code shows a sample action filter:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="18c47-347"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> Poskytuje následující vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="18c47-347">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> provides the following properties:</span></span>

* <span data-ttu-id="18c47-348"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments>– povolí čtení vstupů do metody Action.</span><span class="sxs-lookup"><span data-stu-id="18c47-348"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> - enables reading the inputs to an action method.</span></span>
* <span data-ttu-id="18c47-349"><xref:Microsoft.AspNetCore.Mvc.Controller>– povolí manipulaci s instancí kontroleru.</span><span class="sxs-lookup"><span data-stu-id="18c47-349"><xref:Microsoft.AspNetCore.Mvc.Controller> - enables manipulating the controller instance.</span></span>
* <span data-ttu-id="18c47-350"><xref:System.Web.Mvc.ActionExecutingContext.Result>– nastavení `Result` krátkodobého provádění metody akce a následných filtrů akcí.</span><span class="sxs-lookup"><span data-stu-id="18c47-350"><xref:System.Web.Mvc.ActionExecutingContext.Result> - setting `Result` short-circuits execution of the action method and subsequent action filters.</span></span>

<span data-ttu-id="18c47-351">Vyvolání výjimky v metodě akce:</span><span class="sxs-lookup"><span data-stu-id="18c47-351">Throwing an exception in an action method:</span></span>

* <span data-ttu-id="18c47-352">Zabraňuje spuštění dalších filtrů.</span><span class="sxs-lookup"><span data-stu-id="18c47-352">Prevents running of subsequent filters.</span></span>
* <span data-ttu-id="18c47-353">Na rozdíl od `Result`nastavení se místo úspěšného výsledku považuje za selhání.</span><span class="sxs-lookup"><span data-stu-id="18c47-353">Unlike setting `Result`, is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="18c47-354"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> Poskytuje `Controller` a `Result` plus následující vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="18c47-354">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> provides `Controller` and `Result` plus the following properties:</span></span>

* <span data-ttu-id="18c47-355"><xref:System.Web.Mvc.ActionExecutedContext.Canceled>-True, pokud bylo provedení akce zkráceno jiným filtrem.</span><span class="sxs-lookup"><span data-stu-id="18c47-355"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> - True if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="18c47-356"><xref:System.Web.Mvc.ActionExecutedContext.Exception>-Hodnota není null, pokud akce nebo dříve vyvolala filtr akcí.</span><span class="sxs-lookup"><span data-stu-id="18c47-356"><xref:System.Web.Mvc.ActionExecutedContext.Exception> - Non-null if the action or a previously run action filter threw an exception.</span></span> <span data-ttu-id="18c47-357">Nastavení této vlastnosti na hodnotu null:</span><span class="sxs-lookup"><span data-stu-id="18c47-357">Setting this property to null:</span></span>

  * <span data-ttu-id="18c47-358">Efektivně zpracovává výjimku.</span><span class="sxs-lookup"><span data-stu-id="18c47-358">Effectively handles the exception.</span></span>
  * <span data-ttu-id="18c47-359">`Result`je spuštěn, jako kdyby byl vrácen z metody Action.</span><span class="sxs-lookup"><span data-stu-id="18c47-359">`Result` is executed as if it was returned from the action method.</span></span>

<span data-ttu-id="18c47-360">`IAsyncActionFilter`V případě volání <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span><span class="sxs-lookup"><span data-stu-id="18c47-360">For an `IAsyncActionFilter`, a call to the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span></span>

* <span data-ttu-id="18c47-361">Provede všechny následné filtry akcí a metodu Action.</span><span class="sxs-lookup"><span data-stu-id="18c47-361">Executes any subsequent action filters and the action method.</span></span>
* <span data-ttu-id="18c47-362">Vrací objekt `ActionExecutedContext`.</span><span class="sxs-lookup"><span data-stu-id="18c47-362">Returns `ActionExecutedContext`.</span></span>

<span data-ttu-id="18c47-363">Do krátkodobého okruhu, <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> přiřaďte k instanci výsledku a Nevolejte `next` ( `ActionExecutionDelegate`a).</span><span class="sxs-lookup"><span data-stu-id="18c47-363">To short-circuit, assign <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> to a result instance and don't call `next` (the `ActionExecutionDelegate`).</span></span>

<span data-ttu-id="18c47-364">Rozhraní poskytuje abstrakci <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> , která může být podtříd.</span><span class="sxs-lookup"><span data-stu-id="18c47-364">The framework provides an abstract <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> that can be subclassed.</span></span>

<span data-ttu-id="18c47-365">Filtr `OnActionExecuting` akcí lze použít k těmto akcím:</span><span class="sxs-lookup"><span data-stu-id="18c47-365">The `OnActionExecuting` action filter can be used to:</span></span>

* <span data-ttu-id="18c47-366">Ověří stav modelu.</span><span class="sxs-lookup"><span data-stu-id="18c47-366">Validate model state.</span></span>
* <span data-ttu-id="18c47-367">Pokud je stav neplatný, vrátí se chyba.</span><span class="sxs-lookup"><span data-stu-id="18c47-367">Return an error if the state is invalid.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet)]

<span data-ttu-id="18c47-368">Metoda `OnActionExecuted` se spustí za metodou akce:</span><span class="sxs-lookup"><span data-stu-id="18c47-368">The `OnActionExecuted` method runs after the action method:</span></span>

* <span data-ttu-id="18c47-369">A mohou zobrazit výsledky akce a manipulovat s nimi prostřednictvím <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="18c47-369">And can see and manipulate the results of the action through the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> property.</span></span>
* <span data-ttu-id="18c47-370"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled>je nastaven na hodnotu true, pokud bylo provedení akce zkráceno jiným filtrem.</span><span class="sxs-lookup"><span data-stu-id="18c47-370"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> is set to true if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="18c47-371"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception>je nastavena na hodnotu jinou než null, pokud akce nebo filtr následné akce vyvolaly výjimku.</span><span class="sxs-lookup"><span data-stu-id="18c47-371"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> is set to a non-null value if the action or a subsequent action filter threw an exception.</span></span> <span data-ttu-id="18c47-372">Nastavení `Exception` na hodnotu null:</span><span class="sxs-lookup"><span data-stu-id="18c47-372">Setting `Exception` to null:</span></span>

  * <span data-ttu-id="18c47-373">Efektivně zpracovává výjimku.</span><span class="sxs-lookup"><span data-stu-id="18c47-373">Effectively handles an exception.</span></span>
  * <span data-ttu-id="18c47-374">`ActionExecutedContext.Result`je spuštěn, jako kdyby byl vrácen normálně z metody Action.</span><span class="sxs-lookup"><span data-stu-id="18c47-374">`ActionExecutedContext.Result` is executed as if it were returned normally from the action method.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet2&higlight=12-99)]

## <a name="exception-filters"></a><span data-ttu-id="18c47-375">Filtry výjimek</span><span class="sxs-lookup"><span data-stu-id="18c47-375">Exception filters</span></span>

<span data-ttu-id="18c47-376">Filtry výjimek:</span><span class="sxs-lookup"><span data-stu-id="18c47-376">Exception filters:</span></span>

* <span data-ttu-id="18c47-377"><xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> Implementujte <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>nebo.</span><span class="sxs-lookup"><span data-stu-id="18c47-377">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span></span>
* <span data-ttu-id="18c47-378">Dá se použít k implementaci běžných zásad zpracování chyb.</span><span class="sxs-lookup"><span data-stu-id="18c47-378">Can be used to implement common error handling policies.</span></span>

<span data-ttu-id="18c47-379">Následující ukázkový filtr výjimek používá vlastní zobrazení chyb pro zobrazení podrobností o výjimkách, ke kterým dochází při vývoji aplikace:</span><span class="sxs-lookup"><span data-stu-id="18c47-379">The following sample exception filter uses a custom error view to display details about exceptions that occur when the app is in development:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/CustomExceptionFilter.cs?name=snippet_ExceptionFilter&highlight=16-19)]

<span data-ttu-id="18c47-380">Následující kód testuje filtr výjimky:</span><span class="sxs-lookup"><span data-stu-id="18c47-380">The following code tests the exception filter:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Controllers/FailingController.cs?name=snippet)]

<span data-ttu-id="18c47-381">Filtry výjimek:</span><span class="sxs-lookup"><span data-stu-id="18c47-381">Exception filters:</span></span>

* <span data-ttu-id="18c47-382">Nemusíte mít události před a po.</span><span class="sxs-lookup"><span data-stu-id="18c47-382">Don't have before and after events.</span></span>
* <span data-ttu-id="18c47-383"><xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> Implementujte <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>nebo.</span><span class="sxs-lookup"><span data-stu-id="18c47-383">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span></span>
* <span data-ttu-id="18c47-384">Zpracování neošetřených výjimek, ke kterým dochází při vytváření stránky nebo kontroléru Razor, [vázání modelů](xref:mvc/models/model-binding), filtrů akcí nebo metod akcí.</span><span class="sxs-lookup"><span data-stu-id="18c47-384">Handle unhandled exceptions that occur in Razor Page or controller creation, [model binding](xref:mvc/models/model-binding), action filters, or action methods.</span></span>
* <span data-ttu-id="18c47-385">**Nezachycujte** výjimky, ke kterým dochází v filtrech prostředků, výsledných filtrech nebo v důsledku provádění výsledků MVC.</span><span class="sxs-lookup"><span data-stu-id="18c47-385">Do **not** catch exceptions that occur in resource filters, result filters, or MVC result execution.</span></span>

<span data-ttu-id="18c47-386">Chcete-li zpracovat výjimku, nastavte <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> vlastnost na `true` nebo zapište odpověď.</span><span class="sxs-lookup"><span data-stu-id="18c47-386">To handle an exception, set the <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> property to `true` or write a response.</span></span> <span data-ttu-id="18c47-387">Tím se zastaví šíření výjimky.</span><span class="sxs-lookup"><span data-stu-id="18c47-387">This stops propagation of the exception.</span></span> <span data-ttu-id="18c47-388">Filtr výjimek nemůže vypnout výjimku na "úspěch".</span><span class="sxs-lookup"><span data-stu-id="18c47-388">An exception filter can't turn an exception into a "success".</span></span> <span data-ttu-id="18c47-389">To může provést pouze filtr akcí.</span><span class="sxs-lookup"><span data-stu-id="18c47-389">Only an action filter can do that.</span></span>

<span data-ttu-id="18c47-390">Filtry výjimek:</span><span class="sxs-lookup"><span data-stu-id="18c47-390">Exception filters:</span></span>

* <span data-ttu-id="18c47-391">Jsou vhodné pro vytváření přesahů výjimek, ke kterým dochází v rámci akcí.</span><span class="sxs-lookup"><span data-stu-id="18c47-391">Are good for trapping exceptions that occur within actions.</span></span>
* <span data-ttu-id="18c47-392">Nejsou tak flexibilní jako middleware při zpracování chyb.</span><span class="sxs-lookup"><span data-stu-id="18c47-392">Are not as flexible as error handling middleware.</span></span>

<span data-ttu-id="18c47-393">Preferovat middleware pro zpracování výjimek.</span><span class="sxs-lookup"><span data-stu-id="18c47-393">Prefer middleware for exception handling.</span></span> <span data-ttu-id="18c47-394">Filtry výjimek použijte pouze v případě, že se zpracování chyb *liší* v závislosti na tom, která metoda Action je volána.</span><span class="sxs-lookup"><span data-stu-id="18c47-394">Use exception filters only where error handling *differs* based on which action method is called.</span></span> <span data-ttu-id="18c47-395">Aplikace může například mít metody akcí pro koncové body rozhraní API i pro zobrazení/HTML.</span><span class="sxs-lookup"><span data-stu-id="18c47-395">For example, an app might have action methods for both API endpoints and for views/HTML.</span></span> <span data-ttu-id="18c47-396">Koncové body rozhraní API mohou vracet informace o chybě jako JSON, zatímco akce na základě zobrazení by mohly vracet chybovou stránku jako HTML.</span><span class="sxs-lookup"><span data-stu-id="18c47-396">The API endpoints could return error information as JSON, while the view-based actions could return an error page as HTML.</span></span>

## <a name="result-filters"></a><span data-ttu-id="18c47-397">Filtry výsledků</span><span class="sxs-lookup"><span data-stu-id="18c47-397">Result filters</span></span>

<span data-ttu-id="18c47-398">Filtry výsledků:</span><span class="sxs-lookup"><span data-stu-id="18c47-398">Result filters:</span></span>

* <span data-ttu-id="18c47-399">Implementace rozhraní:</span><span class="sxs-lookup"><span data-stu-id="18c47-399">Implement an interface:</span></span>
  * <span data-ttu-id="18c47-400"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> nebo <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="18c47-400"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
  * <span data-ttu-id="18c47-401"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> nebo <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span><span class="sxs-lookup"><span data-stu-id="18c47-401"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span></span>
* <span data-ttu-id="18c47-402">Jejich spuštění obklopuje provádění výsledků akcí.</span><span class="sxs-lookup"><span data-stu-id="18c47-402">Their execution surrounds the execution of action results.</span></span>

### <a name="iresultfilter-and-iasyncresultfilter"></a><span data-ttu-id="18c47-403">IResultFilter a IAsyncResultFilter</span><span class="sxs-lookup"><span data-stu-id="18c47-403">IResultFilter and IAsyncResultFilter</span></span>

<span data-ttu-id="18c47-404">Následující kód ukazuje filtr výsledků, který přidá hlavičku protokolu HTTP:</span><span class="sxs-lookup"><span data-stu-id="18c47-404">The following code shows a result filter that adds an HTTP header:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="18c47-405">Typ výsledku, který se má provést, závisí na akci.</span><span class="sxs-lookup"><span data-stu-id="18c47-405">The kind of result being executed depends on the action.</span></span> <span data-ttu-id="18c47-406">Akce vracející zobrazení zahrnuje všechny zpracování Razor jako součást <xref:Microsoft.AspNetCore.Mvc.ViewResult> prováděné.</span><span class="sxs-lookup"><span data-stu-id="18c47-406">An action returning a view includes all razor processing as part of the <xref:Microsoft.AspNetCore.Mvc.ViewResult> being executed.</span></span> <span data-ttu-id="18c47-407">Metoda rozhraní API může provést určitou serializaci v rámci provádění výsledku.</span><span class="sxs-lookup"><span data-stu-id="18c47-407">An API method might perform some serialization as part of the execution of the result.</span></span> <span data-ttu-id="18c47-408">Přečtěte si další informace o [výsledcích akcí](xref:mvc/controllers/actions).</span><span class="sxs-lookup"><span data-stu-id="18c47-408">Learn more about [action results](xref:mvc/controllers/actions).</span></span>

<span data-ttu-id="18c47-409">Filtry výsledků se spustí pouze v případě, že akce nebo filtr akcí vytvoří výsledek akce.</span><span class="sxs-lookup"><span data-stu-id="18c47-409">Result filters are only executed when an action or action filter produces an action result.</span></span> <span data-ttu-id="18c47-410">Filtry výsledků nejsou provedeny v těchto případech:</span><span class="sxs-lookup"><span data-stu-id="18c47-410">Result filters are not executed when:</span></span>

* <span data-ttu-id="18c47-411">Filtr autorizace nebo filtr prostředků pro krátké okruhy kanálu.</span><span class="sxs-lookup"><span data-stu-id="18c47-411">An authorization filter or resource filter short-circuits the pipeline.</span></span>
* <span data-ttu-id="18c47-412">Filtr výjimek zpracovává výjimku tím, že vyprodukuje výsledek akce.</span><span class="sxs-lookup"><span data-stu-id="18c47-412">An exception filter handles an exception by producing an action result.</span></span>

<span data-ttu-id="18c47-413"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> Metoda může zkrátit provádění výsledků akce a následných filtrů výsledků nastavením <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> na `true`.</span><span class="sxs-lookup"><span data-stu-id="18c47-413">The <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> method can short-circuit execution of the action result and subsequent result filters by setting <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> to `true`.</span></span> <span data-ttu-id="18c47-414">Zapište do objektu Response v případě krátkého okruhu, abyste se vyhnuli vygenerování prázdné odpovědi.</span><span class="sxs-lookup"><span data-stu-id="18c47-414">Write to the response object when short-circuiting to avoid generating an empty response.</span></span> <span data-ttu-id="18c47-415">Vyvolání výjimky v `IResultFilter.OnResultExecuting`:</span><span class="sxs-lookup"><span data-stu-id="18c47-415">Throwing an exception in `IResultFilter.OnResultExecuting`:</span></span>

* <span data-ttu-id="18c47-416">Zabraňuje spuštění výsledků akce a následných filtrů.</span><span class="sxs-lookup"><span data-stu-id="18c47-416">Prevents execution of the action result and subsequent filters.</span></span>
* <span data-ttu-id="18c47-417">Se považuje za selhání namísto úspěšného výsledku.</span><span class="sxs-lookup"><span data-stu-id="18c47-417">Is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="18c47-418">Při spuštění <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> metody je odpověď pravděpodobně již odeslána klientovi.</span><span class="sxs-lookup"><span data-stu-id="18c47-418">When the <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> method runs, the response has probably already been sent to the client.</span></span> <span data-ttu-id="18c47-419">Pokud byla odpověď již odeslána klientovi, nelze ji změnit.</span><span class="sxs-lookup"><span data-stu-id="18c47-419">If the response has already been sent to the client, it cannot be changed.</span></span>

<span data-ttu-id="18c47-420">`ResultExecutedContext.Canceled`je nastaven na `true` hodnotu, pokud je spuštění výsledku akce zkráceno jiným filtrem.</span><span class="sxs-lookup"><span data-stu-id="18c47-420">`ResultExecutedContext.Canceled` is set to `true` if the action result execution was short-circuited by another filter.</span></span>

<span data-ttu-id="18c47-421">`ResultExecutedContext.Exception`je nastavena na hodnotu jinou než null, pokud výsledek akce nebo následný filtr výsledků vyvolal výjimku.</span><span class="sxs-lookup"><span data-stu-id="18c47-421">`ResultExecutedContext.Exception` is set to a non-null value if the action result or a subsequent result filter threw an exception.</span></span> <span data-ttu-id="18c47-422">Nastavení `Exception` na hodnotu null efektivně zpracuje výjimku a zabrání vyvolání výjimky později v kanálu.</span><span class="sxs-lookup"><span data-stu-id="18c47-422">Setting `Exception` to null effectively handles an exception and prevents the exception from being thrown again later in the pipeline.</span></span> <span data-ttu-id="18c47-423">Neexistuje žádný spolehlivý způsob, jak zapisovat data do odpovědi při zpracování výjimky ve filtru výsledků.</span><span class="sxs-lookup"><span data-stu-id="18c47-423">There is no reliable way to write data to a response when handling an exception in a result filter.</span></span> <span data-ttu-id="18c47-424">Pokud byly hlavičky vyprázdněny do klienta, pokud výsledek akce vyvolá výjimku, neexistuje žádný spolehlivý mechanismus pro odeslání kódu chyby.</span><span class="sxs-lookup"><span data-stu-id="18c47-424">If the headers have been flushed to the client when an action result throws an exception, there's no reliable mechanism to send a failure code.</span></span>

<span data-ttu-id="18c47-425">V případě volání na se <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> spustí všechny následné filtry výsledků a výsledek akce. <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter> `await next`</span><span class="sxs-lookup"><span data-stu-id="18c47-425">For an <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, a call to `await next` on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> executes any subsequent result filters and the action result.</span></span> <span data-ttu-id="18c47-426">Do krátkodobého okruhu nastavte [ResultExecutingContext. Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) na `true` a Nevolejte na `ResultExecutionDelegate`:</span><span class="sxs-lookup"><span data-stu-id="18c47-426">To short-circuit, set [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) to `true` and don't call the `ResultExecutionDelegate`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MyAsyncResponseFilter.cs?name=snippet)]

<span data-ttu-id="18c47-427">Rozhraní poskytuje abstrakci `ResultFilterAttribute` , která může být podtříd.</span><span class="sxs-lookup"><span data-stu-id="18c47-427">The framework provides an abstract `ResultFilterAttribute` that can be subclassed.</span></span> <span data-ttu-id="18c47-428">Dříve uvedená třída [AddHeaderAttribute](#add-header-attribute) je příkladem atributu filtru výsledků.</span><span class="sxs-lookup"><span data-stu-id="18c47-428">The [AddHeaderAttribute](#add-header-attribute) class shown previously is an example of a result filter attribute.</span></span>

### <a name="ialwaysrunresultfilter-and-iasyncalwaysrunresultfilter"></a><span data-ttu-id="18c47-429">IAlwaysRunResultFilter a IAsyncAlwaysRunResultFilter</span><span class="sxs-lookup"><span data-stu-id="18c47-429">IAlwaysRunResultFilter and IAsyncAlwaysRunResultFilter</span></span>

<span data-ttu-id="18c47-430">Rozhraní <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> a <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> deklaruje <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> implementaci, která je spuštěna pro všechny výsledky akce.</span><span class="sxs-lookup"><span data-stu-id="18c47-430">The <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> interfaces declare an <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> implementation that runs for all action results.</span></span> <span data-ttu-id="18c47-431">To zahrnuje výsledky akcí, které vytvořil:</span><span class="sxs-lookup"><span data-stu-id="18c47-431">This includes action results produced by:</span></span>

* <span data-ttu-id="18c47-432">Filtry autorizace a filtry prostředků, které mají krátký okruh.</span><span class="sxs-lookup"><span data-stu-id="18c47-432">Authorization filters and resource filters that short-circuit.</span></span>
* <span data-ttu-id="18c47-433">Filtry výjimek.</span><span class="sxs-lookup"><span data-stu-id="18c47-433">Exception filters.</span></span>

<span data-ttu-id="18c47-434">Například následující filtr vždy spustí a nastaví výsledek akce (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) s *422 nezpracovaným* stavovým kódem entity, pokud se nezdaří vyjednávání obsahu:</span><span class="sxs-lookup"><span data-stu-id="18c47-434">For example, the following filter always runs and sets an action result (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) with a *422 Unprocessable Entity* status code when content negotiation fails:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/UnprocessableResultFilter.cs?name=snippet)]

### <a name="ifilterfactory"></a><span data-ttu-id="18c47-435">IFilterFactory</span><span class="sxs-lookup"><span data-stu-id="18c47-435">IFilterFactory</span></span>

<span data-ttu-id="18c47-436"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>implementuje <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span><span class="sxs-lookup"><span data-stu-id="18c47-436"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span></span> <span data-ttu-id="18c47-437">Proto může být `IFilterFactory` instance použita jako `IFilterMetadata` instance kdekoli v kanálu filtru.</span><span class="sxs-lookup"><span data-stu-id="18c47-437">Therefore, an `IFilterFactory` instance can be used as an `IFilterMetadata` instance anywhere in the filter pipeline.</span></span> <span data-ttu-id="18c47-438">Když se modul runtime připraví k vyvolání filtru, pokusí se ho přetypovat na `IFilterFactory`.</span><span class="sxs-lookup"><span data-stu-id="18c47-438">When the runtime prepares to invoke the filter, it attempts to cast it to an `IFilterFactory`.</span></span> <span data-ttu-id="18c47-439">Pokud je toto přetypování úspěšné, <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> je volána metoda pro vytvoření `IFilterMetadata` instance, která je vyvolána.</span><span class="sxs-lookup"><span data-stu-id="18c47-439">If that cast succeeds, the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method is called to create the `IFilterMetadata` instance that is invoked.</span></span> <span data-ttu-id="18c47-440">To poskytuje flexibilní návrh, protože přesný kanál filtru není nutné nastavit explicitně při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="18c47-440">This provides a flexible design, since the precise filter pipeline doesn't need to be set explicitly when the app starts.</span></span>

<span data-ttu-id="18c47-441">`IFilterFactory`může být implementováno pomocí implementace vlastního atributu jako jiný přístup k vytváření filtrů:</span><span class="sxs-lookup"><span data-stu-id="18c47-441">`IFilterFactory` can be implemented using custom attribute implementations as another approach to creating filters:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/AddHeaderWithFactoryAttribute.cs?name=snippet_IFilterFactory&highlight=1,4,5,6,7)]

<span data-ttu-id="18c47-442">Filtr je použit v následujícím kódu:</span><span class="sxs-lookup"><span data-stu-id="18c47-442">The filter is applied in the following code:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet3&highlight=21)]

<span data-ttu-id="18c47-443">Otestujte předchozí kód spuštěním [ukázky stahování](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample):</span><span class="sxs-lookup"><span data-stu-id="18c47-443">Test the preceding code by running the [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample):</span></span>

* <span data-ttu-id="18c47-444">Vyvolejte vývojářské nástroje F12.</span><span class="sxs-lookup"><span data-stu-id="18c47-444">Invoke the F12 developer tools.</span></span>
* <span data-ttu-id="18c47-445">Přejděte na adresu `https://localhost:5001/Sample/HeaderWithFactory`.</span><span class="sxs-lookup"><span data-stu-id="18c47-445">Navigate to `https://localhost:5001/Sample/HeaderWithFactory`.</span></span>

<span data-ttu-id="18c47-446">Vývojářské nástroje F12 zobrazují následující hlavičky odpovědí přidané ukázkovým kódem:</span><span class="sxs-lookup"><span data-stu-id="18c47-446">The F12 developer tools display the following response headers added by the sample code:</span></span>

* <span data-ttu-id="18c47-447">**Autor:**`Rick Anderson`</span><span class="sxs-lookup"><span data-stu-id="18c47-447">**author:** `Rick Anderson`</span></span>
* <span data-ttu-id="18c47-448">**globaladdheader:**`Result filter added to MvcOptions.Filters`</span><span class="sxs-lookup"><span data-stu-id="18c47-448">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span></span>
* <span data-ttu-id="18c47-449">**interní:**`My header`</span><span class="sxs-lookup"><span data-stu-id="18c47-449">**internal:** `My header`</span></span>

<span data-ttu-id="18c47-450">Předchozí kód vytvoří hlavičku **interního:** `My header` Response.</span><span class="sxs-lookup"><span data-stu-id="18c47-450">The preceding code creates the **internal:** `My header` response header.</span></span>

### <a name="ifilterfactory-implemented-on-an-attribute"></a><span data-ttu-id="18c47-451">IFilterFactory implementované u atributu</span><span class="sxs-lookup"><span data-stu-id="18c47-451">IFilterFactory implemented on an attribute</span></span>

<!-- Review 
This section needs to be rewritten.
What's a non-named attribute?
-->

<span data-ttu-id="18c47-452">Filtry, které `IFilterFactory` implementují, jsou užitečné pro filtry, které:</span><span class="sxs-lookup"><span data-stu-id="18c47-452">Filters that implement `IFilterFactory` are useful for filters that:</span></span>

* <span data-ttu-id="18c47-453">Nevyžadovat předávání parametrů</span><span class="sxs-lookup"><span data-stu-id="18c47-453">Don't require passing parameters.</span></span>
* <span data-ttu-id="18c47-454">Musí mít závislosti konstruktoru, které musí vyplnit DI.</span><span class="sxs-lookup"><span data-stu-id="18c47-454">Have constructor dependencies that need to be filled by DI.</span></span>

<span data-ttu-id="18c47-455"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>implementuje <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span><span class="sxs-lookup"><span data-stu-id="18c47-455"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="18c47-456">`IFilterFactory`zpřístupňuje <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> metodu pro vytvoření <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span><span class="sxs-lookup"><span data-stu-id="18c47-456">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="18c47-457">`CreateInstance`Načte zadaný typ z kontejneru Services (DI).</span><span class="sxs-lookup"><span data-stu-id="18c47-457">`CreateInstance` loads the specified type from the services container (DI).</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/SampleActionFilterAttribute.cs?name=snippet_TypeFilterAttribute&highlight=1,3,7)]

<span data-ttu-id="18c47-458">Následující kód ukazuje tři přístupy k použití `[SampleActionFilter]`:</span><span class="sxs-lookup"><span data-stu-id="18c47-458">The following code shows three approaches to applying the `[SampleActionFilter]`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet&highlight=1)]

<span data-ttu-id="18c47-459">V předchozím kódu upravení metoda s `[SampleActionFilter]` jako upřednostňovaným přístupem k použití. `SampleActionFilter`</span><span class="sxs-lookup"><span data-stu-id="18c47-459">In the preceding code, decorating the method with `[SampleActionFilter]` is the preferred approach to applying the `SampleActionFilter`.</span></span>

## <a name="using-middleware-in-the-filter-pipeline"></a><span data-ttu-id="18c47-460">Použití middlewaru v kanálu filtru</span><span class="sxs-lookup"><span data-stu-id="18c47-460">Using middleware in the filter pipeline</span></span>

<span data-ttu-id="18c47-461">Filtry prostředků fungují jako [middleware](xref:fundamentals/middleware/index) v tom, že obklopují provádění všech položek, které jsou později v kanálu.</span><span class="sxs-lookup"><span data-stu-id="18c47-461">Resource filters work like [middleware](xref:fundamentals/middleware/index) in that they surround the execution of everything that comes later in the pipeline.</span></span> <span data-ttu-id="18c47-462">Filtry se ale liší od middlewaru v tom, že jsou součástí modulu runtime, což znamená, že mají přístup k kontextu a sestavám.</span><span class="sxs-lookup"><span data-stu-id="18c47-462">But filters differ from middleware in that they're part of the runtime, which means that they have access to context and constructs.</span></span>

<span data-ttu-id="18c47-463">Chcete-li použít middleware jako filtr, vytvořte typ s `Configure` metodou, která určuje middleware pro vložení do kanálu filtru.</span><span class="sxs-lookup"><span data-stu-id="18c47-463">To use middleware as a filter, create a type with a `Configure` method that specifies the middleware to inject into the filter pipeline.</span></span> <span data-ttu-id="18c47-464">Následující příklad používá middleware Localization k vytvoření aktuální jazykové verze pro požadavek:</span><span class="sxs-lookup"><span data-stu-id="18c47-464">The following example uses the localization middleware to establish the current culture for a request:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LocalizationPipeline.cs?name=snippet_MiddlewareFilter&highlight=3,22)]

<span data-ttu-id="18c47-465"><xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> Pomocí spusťte middleware:</span><span class="sxs-lookup"><span data-stu-id="18c47-465">Use the <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> to run the middleware:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_MiddlewareFilter&highlight=2)]

<span data-ttu-id="18c47-466">Filtry middlewaru jsou spouštěny ve stejné fázi kanálu filtru jako filtry prostředků, před vazbami modelů a po zbytek kanálu.</span><span class="sxs-lookup"><span data-stu-id="18c47-466">Middleware filters run at the same stage of the filter pipeline as Resource filters, before model binding and after the rest of the pipeline.</span></span>

## <a name="next-actions"></a><span data-ttu-id="18c47-467">Další akce</span><span class="sxs-lookup"><span data-stu-id="18c47-467">Next actions</span></span>

* <span data-ttu-id="18c47-468">Viz [metody filtru pro Razor Pages](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="18c47-468">See [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>
* <span data-ttu-id="18c47-469">Pro experimentování s filtry, [stažení, otestování a úpravy ukázky GitHubu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample).</span><span class="sxs-lookup"><span data-stu-id="18c47-469">To experiment with filters, [download, test, and modify the GitHub sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="18c47-470">[Kirka Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Dykstra](https://github.com/tdykstra/)a [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="18c47-470">By [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="18c47-471">*Filtry* v ASP.NET Core umožňují spuštění kódu před nebo po určitých fázích v kanálu zpracování požadavků.</span><span class="sxs-lookup"><span data-stu-id="18c47-471">*Filters* in ASP.NET Core allow code to be run before or after specific stages in the request processing pipeline.</span></span>

<span data-ttu-id="18c47-472">Předdefinované filtry zpracovávají úlohy, jako například:</span><span class="sxs-lookup"><span data-stu-id="18c47-472">Built-in filters handle tasks such as:</span></span>

* <span data-ttu-id="18c47-473">Autorizace (zabránění přístupu k prostředkům, ke kterým uživatel nemá oprávnění).</span><span class="sxs-lookup"><span data-stu-id="18c47-473">Authorization (preventing access to resources a user isn't authorized for).</span></span>
* <span data-ttu-id="18c47-474">Ukládání odpovědí do mezipaměti (při krátkém okruhu kanálu požadavků, který vrátí odpověď uloženou v mezipaměti).</span><span class="sxs-lookup"><span data-stu-id="18c47-474">Response caching (short-circuiting the request pipeline to return a cached response).</span></span>

<span data-ttu-id="18c47-475">Vlastní filtry je možné vytvořit pro zpracování vzájemně se týkajících se otázek.</span><span class="sxs-lookup"><span data-stu-id="18c47-475">Custom filters can be created to handle cross-cutting concerns.</span></span> <span data-ttu-id="18c47-476">Mezi obavy mezi průřezy patří zpracování chyb, ukládání do mezipaměti, konfigurace, autorizace a protokolování.</span><span class="sxs-lookup"><span data-stu-id="18c47-476">Examples of cross-cutting concerns include error handling, caching, configuration, authorization, and logging.</span></span>  <span data-ttu-id="18c47-477">Filtry zabraňují duplikování kódu.</span><span class="sxs-lookup"><span data-stu-id="18c47-477">Filters avoid duplicating code.</span></span> <span data-ttu-id="18c47-478">Například filtr výjimek zpracování chyb může konsolidovat zpracování chyb.</span><span class="sxs-lookup"><span data-stu-id="18c47-478">For example, an error handling exception filter could consolidate error handling.</span></span>

<span data-ttu-id="18c47-479">Tento dokument se týká Razor Pages, řadičů rozhraní API a řadičů se zobrazeními.</span><span class="sxs-lookup"><span data-stu-id="18c47-479">This document applies to Razor Pages, API controllers, and controllers with views.</span></span>

<span data-ttu-id="18c47-480">[Zobrazit nebo Stáhnout ukázku](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample) ([Jak stáhnout](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="18c47-480">[View or download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="how-filters-work"></a><span data-ttu-id="18c47-481">Jak fungují filtry</span><span class="sxs-lookup"><span data-stu-id="18c47-481">How filters work</span></span>

<span data-ttu-id="18c47-482">Filtry se spouštějí v *kanálu vyvolání akce ASP.NET Core*, někdy označované jako *kanál filtru*.</span><span class="sxs-lookup"><span data-stu-id="18c47-482">Filters run within the *ASP.NET Core action invocation pipeline*, sometimes referred to as the *filter pipeline*.</span></span>  <span data-ttu-id="18c47-483">Kanál filtru se spustí po ASP.NET Core vybere akci, která se má provést.</span><span class="sxs-lookup"><span data-stu-id="18c47-483">The filter pipeline runs after ASP.NET Core selects the action to execute.</span></span>

![Požadavek se zpracovává prostřednictvím jiného middlewaru, middleware směrování, výběru akcí a kanálu vyvolání akce ASP.NET Core.](filters/_static/filter-pipeline-1.png)

### <a name="filter-types"></a><span data-ttu-id="18c47-486">Typy filtrů</span><span class="sxs-lookup"><span data-stu-id="18c47-486">Filter types</span></span>

<span data-ttu-id="18c47-487">Každý typ filtru se spustí v jiné fázi kanálu filtru:</span><span class="sxs-lookup"><span data-stu-id="18c47-487">Each filter type is executed at a different stage in the filter pipeline:</span></span>

* <span data-ttu-id="18c47-488">[Filtry autorizace](#authorization-filters) se spouštějí jako první a používají se k určení, jestli je uživatel autorizovaný pro požadavek.</span><span class="sxs-lookup"><span data-stu-id="18c47-488">[Authorization filters](#authorization-filters) run first and are used to determine whether the user is authorized for the request.</span></span> <span data-ttu-id="18c47-489">Filtry autorizace pro krátkodobé vynechání kanálu, pokud je požadavek neautorizovaný</span><span class="sxs-lookup"><span data-stu-id="18c47-489">Authorization filters short-circuit the pipeline if the request is unauthorized.</span></span>

* <span data-ttu-id="18c47-490">[Filtry prostředků](#resource-filters):</span><span class="sxs-lookup"><span data-stu-id="18c47-490">[Resource filters](#resource-filters):</span></span>

  * <span data-ttu-id="18c47-491">Spusťte po autorizaci.</span><span class="sxs-lookup"><span data-stu-id="18c47-491">Run after authorization.</span></span>  
  * <span data-ttu-id="18c47-492"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*>může spustit kód před zbytek kanálu filtru.</span><span class="sxs-lookup"><span data-stu-id="18c47-492"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> can run code before the rest of the filter pipeline.</span></span> <span data-ttu-id="18c47-493">Například `OnResourceExecuting` může spustit kód před vytvořením vazby modelu.</span><span class="sxs-lookup"><span data-stu-id="18c47-493">For example, `OnResourceExecuting` can run code before model binding.</span></span>
  * <span data-ttu-id="18c47-494"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*>může spustit kód po dokončení zbývající části kanálu.</span><span class="sxs-lookup"><span data-stu-id="18c47-494"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> can run code after the rest of the pipeline has completed.</span></span>

* <span data-ttu-id="18c47-495">[Filtry akcí](#action-filters) mohou spustit kód bezprostředně před a po volání jednotlivé metody akce.</span><span class="sxs-lookup"><span data-stu-id="18c47-495">[Action filters](#action-filters) can run code immediately before and after an individual action method is called.</span></span> <span data-ttu-id="18c47-496">Lze je použít k manipulaci s argumenty předaných do akce a výsledek vráceného z akce.</span><span class="sxs-lookup"><span data-stu-id="18c47-496">They can be used to manipulate the arguments passed into an action and the result returned from the action.</span></span> <span data-ttu-id="18c47-497">Filtry **akcí nejsou v Razor Pages podporovány.**</span><span class="sxs-lookup"><span data-stu-id="18c47-497">Action filters are **not** supported in Razor Pages.</span></span>

* <span data-ttu-id="18c47-498">[Filtry výjimek](#exception-filters) slouží k aplikování globálních zásad na neošetřené výjimky, ke kterým dojde před zapsáním cokoli do těla odpovědi.</span><span class="sxs-lookup"><span data-stu-id="18c47-498">[Exception filters](#exception-filters) are used to apply global policies to unhandled exceptions that occur before anything has been written to the response body.</span></span>

* <span data-ttu-id="18c47-499">[Filtry výsledků](#result-filters) můžou spustit kód hned před a po provedení jednotlivých výsledků akce.</span><span class="sxs-lookup"><span data-stu-id="18c47-499">[Result filters](#result-filters) can run code immediately before and after the execution of individual action results.</span></span> <span data-ttu-id="18c47-500">Spustí se pouze v případě, že metoda akce byla úspěšně provedena.</span><span class="sxs-lookup"><span data-stu-id="18c47-500">They run only when the action method has executed successfully.</span></span> <span data-ttu-id="18c47-501">Jsou užitečné pro logiku, která musí obklopit zobrazení nebo formátovací modul.</span><span class="sxs-lookup"><span data-stu-id="18c47-501">They are useful for logic that must surround view or formatter execution.</span></span>

<span data-ttu-id="18c47-502">Následující diagram znázorňuje způsob interakce typů filtrů v kanálu filtru.</span><span class="sxs-lookup"><span data-stu-id="18c47-502">The following diagram shows how filter types interact in the filter pipeline.</span></span>

![Požadavek se zpracovává pomocí autorizačních filtrů, filtrů prostředků, vazeb modelů, filtrů akcí, provádění akcí a konverze výsledků akcí, filtrů výjimek, výsledných filtrů a provádění výsledků.](filters/_static/filter-pipeline-2.png)

## <a name="implementation"></a><span data-ttu-id="18c47-505">Implementace</span><span class="sxs-lookup"><span data-stu-id="18c47-505">Implementation</span></span>

<span data-ttu-id="18c47-506">Filtry podporují synchronní i asynchronní implementace prostřednictvím různých definic rozhraní.</span><span class="sxs-lookup"><span data-stu-id="18c47-506">Filters support both synchronous and asynchronous implementations through different interface definitions.</span></span>

<span data-ttu-id="18c47-507">Synchronní filtry mohou spustit kód před (`On-Stage-Executing`) a po (`On-Stage-Executed`) jejich fáze zřetězení.</span><span class="sxs-lookup"><span data-stu-id="18c47-507">Synchronous filters can run code before (`On-Stage-Executing`) and after (`On-Stage-Executed`) their pipeline stage.</span></span> <span data-ttu-id="18c47-508">Například `OnActionExecuting` je volána před voláním metody Action.</span><span class="sxs-lookup"><span data-stu-id="18c47-508">For example, `OnActionExecuting` is called before the action method is called.</span></span> <span data-ttu-id="18c47-509">`OnActionExecuted`je volána po návratu metody Action.</span><span class="sxs-lookup"><span data-stu-id="18c47-509">`OnActionExecuted` is called after the action method returns.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="18c47-510">Asynchronní filtry definují `On-Stage-ExecutionAsync` metodu:</span><span class="sxs-lookup"><span data-stu-id="18c47-510">Asynchronous filters define an `On-Stage-ExecutionAsync` method:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/SampleAsyncActionFilter.cs?name=snippet)]

<span data-ttu-id="18c47-511">V předchozím kódu `SampleAsyncActionFilter` obsahuje znak <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`), který provádí metodu Action.</span><span class="sxs-lookup"><span data-stu-id="18c47-511">In the preceding code, the `SampleAsyncActionFilter` has an <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`)  that executes the action method.</span></span>  <span data-ttu-id="18c47-512">Každá z těchto `On-Stage-ExecutionAsync` metod vezme `FilterType-ExecutionDelegate` fázi zřetězení filtru.</span><span class="sxs-lookup"><span data-stu-id="18c47-512">Each of the `On-Stage-ExecutionAsync` methods take a `FilterType-ExecutionDelegate` that executes the filter's pipeline stage.</span></span>

### <a name="multiple-filter-stages"></a><span data-ttu-id="18c47-513">Několik fází filtru</span><span class="sxs-lookup"><span data-stu-id="18c47-513">Multiple filter stages</span></span>

<span data-ttu-id="18c47-514">Rozhraní pro více fází filtru lze implementovat v rámci jedné třídy.</span><span class="sxs-lookup"><span data-stu-id="18c47-514">Interfaces for multiple filter stages can be implemented in a single class.</span></span> <span data-ttu-id="18c47-515">Například <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> třída implementuje `IActionFilter`, `IResultFilter`a jejich asynchronní ekvivalenty.</span><span class="sxs-lookup"><span data-stu-id="18c47-515">For example, the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> class implements `IActionFilter`, `IResultFilter`, and their async equivalents.</span></span>

<span data-ttu-id="18c47-516">Implementujte **buď** synchronní, nebo asynchronní verzi rozhraní filtru, **nikoli** obojí.</span><span class="sxs-lookup"><span data-stu-id="18c47-516">Implement **either** the synchronous or the async version of a filter interface, **not** both.</span></span> <span data-ttu-id="18c47-517">Modul runtime nejprve kontroluje, zda filtr implementuje asynchronní rozhraní a v takovém případě jej zavolá.</span><span class="sxs-lookup"><span data-stu-id="18c47-517">The runtime checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="18c47-518">V takovém případě volá metody synchronního rozhraní.</span><span class="sxs-lookup"><span data-stu-id="18c47-518">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="18c47-519">Pokud jsou asynchronní i synchronní rozhraní implementovány v jedné třídě, je volána pouze asynchronní metoda.</span><span class="sxs-lookup"><span data-stu-id="18c47-519">If both asynchronous and synchronous interfaces are implemented in one class, only the async method is called.</span></span> <span data-ttu-id="18c47-520">Při použití abstraktních tříd <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> , jako je například přepsání pouze synchronních metod nebo asynchronní metody pro každý typ filtru.</span><span class="sxs-lookup"><span data-stu-id="18c47-520">When using abstract classes like <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> override only the synchronous methods or the async method for each filter type.</span></span>

### <a name="built-in-filter-attributes"></a><span data-ttu-id="18c47-521">Předdefinované atributy filtru</span><span class="sxs-lookup"><span data-stu-id="18c47-521">Built-in filter attributes</span></span>

<span data-ttu-id="18c47-522">ASP.NET Core obsahuje integrované filtry založené na atributech, které mohou být roztříděné a přizpůsobené.</span><span class="sxs-lookup"><span data-stu-id="18c47-522">ASP.NET Core includes built-in attribute-based filters that can be subclassed and customized.</span></span> <span data-ttu-id="18c47-523">Například následující filtr výsledků přidá hlavičku do odpovědi:</span><span class="sxs-lookup"><span data-stu-id="18c47-523">For example, the following result filter adds a header to the response:</span></span>

<a name="add-header-attribute"></a>

[!code-csharp[](./filters/sample/FiltersSample/Filters/AddHeaderAttribute.cs?name=snippet)]

<span data-ttu-id="18c47-524">Atributy umožňují filtrům přijímat argumenty, jak je znázorněno v předchozím příkladu.</span><span class="sxs-lookup"><span data-stu-id="18c47-524">Attributes allow filters to accept arguments, as shown in the preceding example.</span></span> <span data-ttu-id="18c47-525">Použijte `AddHeaderAttribute` pro metodu Controller nebo Action a zadejte název a hodnotu hlavičky http:</span><span class="sxs-lookup"><span data-stu-id="18c47-525">Apply the `AddHeaderAttribute` to a controller or action method and specify the name and value of the HTTP header:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

<!-- `https://localhost:5001/Sample` -->

<span data-ttu-id="18c47-526">Některé z rozhraní filtru mají odpovídající atributy, které lze použít jako základní třídy pro vlastní implementace.</span><span class="sxs-lookup"><span data-stu-id="18c47-526">Several of the filter interfaces have corresponding attributes that can be used as base classes for custom implementations.</span></span>

<span data-ttu-id="18c47-527">Atributy filtru:</span><span class="sxs-lookup"><span data-stu-id="18c47-527">Filter attributes:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ExceptionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>

## <a name="filter-scopes-and-order-of-execution"></a><span data-ttu-id="18c47-528">Filtrovat obory a pořadí provádění</span><span class="sxs-lookup"><span data-stu-id="18c47-528">Filter scopes and order of execution</span></span>

<span data-ttu-id="18c47-529">Filtr lze přidat do kanálu v jednom ze tří *oborů*:</span><span class="sxs-lookup"><span data-stu-id="18c47-529">A filter can be added to the pipeline at one of three *scopes*:</span></span>

* <span data-ttu-id="18c47-530">Použití atributu pro akci.</span><span class="sxs-lookup"><span data-stu-id="18c47-530">Using an attribute on an action.</span></span>
* <span data-ttu-id="18c47-531">Použití atributu na řadiči.</span><span class="sxs-lookup"><span data-stu-id="18c47-531">Using an attribute on a controller.</span></span>
* <span data-ttu-id="18c47-532">Globálně pro všechny řadiče a akce, jak je znázorněno v následujícím kódu:</span><span class="sxs-lookup"><span data-stu-id="18c47-532">Globally for all controllers and actions as shown in the following code:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/StartupGF.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="18c47-533">Předchozí kód přičítá tři filtry globálně pomocí kolekce [MvcOptions. filters](xref:Microsoft.AspNetCore.Mvc.MvcOptions.Filters) .</span><span class="sxs-lookup"><span data-stu-id="18c47-533">The preceding code adds three filters globally using the [MvcOptions.Filters](xref:Microsoft.AspNetCore.Mvc.MvcOptions.Filters) collection.</span></span>

### <a name="default-order-of-execution"></a><span data-ttu-id="18c47-534">Výchozí pořadí provádění</span><span class="sxs-lookup"><span data-stu-id="18c47-534">Default order of execution</span></span>

<span data-ttu-id="18c47-535">Pokud existuje více filtrů *stejného typu*, určuje obor výchozí pořadí provádění filtru.</span><span class="sxs-lookup"><span data-stu-id="18c47-535">When there are multiple filters *of the same type*, scope determines the default order of filter execution.</span></span>  <span data-ttu-id="18c47-536">Globální filtry filtr tříd Surround.</span><span class="sxs-lookup"><span data-stu-id="18c47-536">Global filters surround class filters.</span></span> <span data-ttu-id="18c47-537">Filtry tříd obklopují filtry metod Surround.</span><span class="sxs-lookup"><span data-stu-id="18c47-537">Class filters surround method filters.</span></span>

<span data-ttu-id="18c47-538">V důsledku vnořování filtru je *po* kódu spuštěn v obráceném pořadí *před* kódem.</span><span class="sxs-lookup"><span data-stu-id="18c47-538">As a result of filter nesting, the *after* code of filters runs in the reverse order of the *before* code.</span></span> <span data-ttu-id="18c47-539">Pořadí filtru:</span><span class="sxs-lookup"><span data-stu-id="18c47-539">The filter sequence:</span></span>

* <span data-ttu-id="18c47-540">*Před* kódem globálních filtrů.</span><span class="sxs-lookup"><span data-stu-id="18c47-540">The *before* code of global filters.</span></span>
  * <span data-ttu-id="18c47-541">*Před* kódem filtru kontroleru.</span><span class="sxs-lookup"><span data-stu-id="18c47-541">The *before* code of controller filters.</span></span>
    * <span data-ttu-id="18c47-542">Kód *před* filtry metody Action.</span><span class="sxs-lookup"><span data-stu-id="18c47-542">The *before* code of action method filters.</span></span>
    * <span data-ttu-id="18c47-543">*Po* kódu filtry metody Action.</span><span class="sxs-lookup"><span data-stu-id="18c47-543">The *after* code of action method filters.</span></span>
  * <span data-ttu-id="18c47-544">*Po* kódu filtry kontroleru.</span><span class="sxs-lookup"><span data-stu-id="18c47-544">The *after* code of controller filters.</span></span>
* <span data-ttu-id="18c47-545">*Po* kódu globálních filtrů.</span><span class="sxs-lookup"><span data-stu-id="18c47-545">The *after* code of global filters.</span></span>
  
<span data-ttu-id="18c47-546">Následující příklad ilustruje pořadí, ve kterém jsou metody filtru volány pro filtry synchronních akcí.</span><span class="sxs-lookup"><span data-stu-id="18c47-546">The following example that illustrates the order in which filter methods are called for synchronous action filters.</span></span>

| <span data-ttu-id="18c47-547">Sequence</span><span class="sxs-lookup"><span data-stu-id="18c47-547">Sequence</span></span> | <span data-ttu-id="18c47-548">Rozsah filtru</span><span class="sxs-lookup"><span data-stu-id="18c47-548">Filter scope</span></span> | <span data-ttu-id="18c47-549">Filter – metoda</span><span class="sxs-lookup"><span data-stu-id="18c47-549">Filter method</span></span> |
|:--------:|:------------:|:-------------:|
| <span data-ttu-id="18c47-550">1</span><span class="sxs-lookup"><span data-stu-id="18c47-550">1</span></span> | <span data-ttu-id="18c47-551">Globální</span><span class="sxs-lookup"><span data-stu-id="18c47-551">Global</span></span> | `OnActionExecuting` |
| <span data-ttu-id="18c47-552">2</span><span class="sxs-lookup"><span data-stu-id="18c47-552">2</span></span> | <span data-ttu-id="18c47-553">Kontrolér</span><span class="sxs-lookup"><span data-stu-id="18c47-553">Controller</span></span> | `OnActionExecuting` |
| <span data-ttu-id="18c47-554">3</span><span class="sxs-lookup"><span data-stu-id="18c47-554">3</span></span> | <span data-ttu-id="18c47-555">Metoda</span><span class="sxs-lookup"><span data-stu-id="18c47-555">Method</span></span> | `OnActionExecuting` |
| <span data-ttu-id="18c47-556">4</span><span class="sxs-lookup"><span data-stu-id="18c47-556">4</span></span> | <span data-ttu-id="18c47-557">Metoda</span><span class="sxs-lookup"><span data-stu-id="18c47-557">Method</span></span> | `OnActionExecuted` |
| <span data-ttu-id="18c47-558">5</span><span class="sxs-lookup"><span data-stu-id="18c47-558">5</span></span> | <span data-ttu-id="18c47-559">Kontrolér</span><span class="sxs-lookup"><span data-stu-id="18c47-559">Controller</span></span> | `OnActionExecuted` |
| <span data-ttu-id="18c47-560">6</span><span class="sxs-lookup"><span data-stu-id="18c47-560">6</span></span> | <span data-ttu-id="18c47-561">Globální</span><span class="sxs-lookup"><span data-stu-id="18c47-561">Global</span></span> | `OnActionExecuted` |

<span data-ttu-id="18c47-562">Tato posloupnost zobrazuje:</span><span class="sxs-lookup"><span data-stu-id="18c47-562">This sequence shows:</span></span>

* <span data-ttu-id="18c47-563">Filtr metod je vnořený do filtru kontroleru.</span><span class="sxs-lookup"><span data-stu-id="18c47-563">The method filter is nested within the controller filter.</span></span>
* <span data-ttu-id="18c47-564">Filtr kontroleru je vnořený do globálního filtru.</span><span class="sxs-lookup"><span data-stu-id="18c47-564">The controller filter is nested within the global filter.</span></span>

### <a name="controller-and-razor-page-level-filters"></a><span data-ttu-id="18c47-565">Filtry na úrovni ovladače a stránky Razor</span><span class="sxs-lookup"><span data-stu-id="18c47-565">Controller and Razor Page level filters</span></span>

<span data-ttu-id="18c47-566">Každý kontroler, který dědí <xref:Microsoft.AspNetCore.Mvc.Controller> ze základní třídy, zahrnuje metody [Controller. OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*), [Controller. OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*)a [Controller. OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*) 
 `OnActionExecuted` .</span><span class="sxs-lookup"><span data-stu-id="18c47-566">Every controller that inherits from the <xref:Microsoft.AspNetCore.Mvc.Controller> base class includes [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*),  [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*), and [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted` methods.</span></span> <span data-ttu-id="18c47-567">Tyto metody:</span><span class="sxs-lookup"><span data-stu-id="18c47-567">These methods:</span></span>

* <span data-ttu-id="18c47-568">Zabalte filtry, které se spouštějí pro danou akci.</span><span class="sxs-lookup"><span data-stu-id="18c47-568">Wrap the filters that run for a given action.</span></span>
* <span data-ttu-id="18c47-569">`OnActionExecuting`je volána před jakýmkoli filtrem akce.</span><span class="sxs-lookup"><span data-stu-id="18c47-569">`OnActionExecuting` is called before any of the action's filters.</span></span>
* <span data-ttu-id="18c47-570">`OnActionExecuted`se volá po všech filtrech akcí.</span><span class="sxs-lookup"><span data-stu-id="18c47-570">`OnActionExecuted` is called after all of the action filters.</span></span>
* <span data-ttu-id="18c47-571">`OnActionExecutionAsync`je volána před jakýmkoli filtrem akce.</span><span class="sxs-lookup"><span data-stu-id="18c47-571">`OnActionExecutionAsync` is called before any of the action's filters.</span></span> <span data-ttu-id="18c47-572">Kód v filtru po `next` spuštění po metodě Action.</span><span class="sxs-lookup"><span data-stu-id="18c47-572">Code in the filter after `next` runs after the action method.</span></span>

<span data-ttu-id="18c47-573">Například v ukázce ke stažení `MySampleActionFilter` se použije globálně při spuštění.</span><span class="sxs-lookup"><span data-stu-id="18c47-573">For example, in the download sample, `MySampleActionFilter` is applied globally in startup.</span></span>

<span data-ttu-id="18c47-574">`TestController`:</span><span class="sxs-lookup"><span data-stu-id="18c47-574">The `TestController`:</span></span>

* <span data-ttu-id="18c47-575">Aplikuje `SampleActionFilterAttribute` (`[SampleActionFilter]`) na `FilterTest2` akci.</span><span class="sxs-lookup"><span data-stu-id="18c47-575">Applies the `SampleActionFilterAttribute` (`[SampleActionFilter]`) to the `FilterTest2` action.</span></span>
* <span data-ttu-id="18c47-576">Přepisuje `OnActionExecuting` a `OnActionExecuted`.</span><span class="sxs-lookup"><span data-stu-id="18c47-576">Overrides `OnActionExecuting` and `OnActionExecuted`.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/TestController.cs?name=snippet)]

<span data-ttu-id="18c47-577">Navigace pro `https://localhost:5001/Test/FilterTest2` spustí následující kód:</span><span class="sxs-lookup"><span data-stu-id="18c47-577">Navigating to `https://localhost:5001/Test/FilterTest2` runs the following code:</span></span>

* `TestController.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `SampleActionFilterAttribute.OnActionExecuting`
      * `TestController.FilterTest2`
    * `SampleActionFilterAttribute.OnActionExecuted`
  * `MySampleActionFilter.OnActionExecuted`
* `TestController.OnActionExecuted`

<span data-ttu-id="18c47-578">Razor Pages najdete v tématu [implementace filtrů stránek Razor pomocí přepsání metod filtru](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span><span class="sxs-lookup"><span data-stu-id="18c47-578">For Razor Pages, see [Implement Razor Page filters by overriding filter methods](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span></span>

### <a name="overriding-the-default-order"></a><span data-ttu-id="18c47-579">Přepsání výchozího pořadí</span><span class="sxs-lookup"><span data-stu-id="18c47-579">Overriding the default order</span></span>

<span data-ttu-id="18c47-580">Výchozí sekvenci spuštění lze přepsat implementací <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span><span class="sxs-lookup"><span data-stu-id="18c47-580">The default sequence of execution can be overridden by implementing <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span></span> <span data-ttu-id="18c47-581">`IOrderedFilter`zpřístupňuje <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> vlastnost, která má přednost před rozsahem, aby určila pořadí provádění.</span><span class="sxs-lookup"><span data-stu-id="18c47-581">`IOrderedFilter` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> property that takes precedence over scope to determine the order of execution.</span></span> <span data-ttu-id="18c47-582">Filtr s nižší `Order` hodnotou:</span><span class="sxs-lookup"><span data-stu-id="18c47-582">A filter with a lower `Order` value:</span></span>

* <span data-ttu-id="18c47-583">Spustí *před* kódem filtr s vyšší hodnotou `Order`.</span><span class="sxs-lookup"><span data-stu-id="18c47-583">Runs the *before* code before that of a filter with a higher value of `Order`.</span></span>
* <span data-ttu-id="18c47-584">Spustí *za* kódem za filtr s vyšší `Order` hodnotou.</span><span class="sxs-lookup"><span data-stu-id="18c47-584">Runs the *after* code after that of a filter with a higher `Order` value.</span></span>

<span data-ttu-id="18c47-585">`Order` Vlastnost lze nastavit s parametrem konstruktoru:</span><span class="sxs-lookup"><span data-stu-id="18c47-585">The `Order` property can be set with a constructor parameter:</span></span>

```csharp
[MyFilter(Name = "Controller Level Attribute", Order=1)]
```

<span data-ttu-id="18c47-586">Vezměte v úvahu stejné 3 filtry akcí, které jsou uvedené v předchozím příkladu.</span><span class="sxs-lookup"><span data-stu-id="18c47-586">Consider the same 3 action filters shown in the preceding example.</span></span> <span data-ttu-id="18c47-587">Pokud je `Order` vlastnost Controller a globální filtry nastavená na 1 a 2 v uvedeném pořadí, pořadí spouštění se vrátí zpět.</span><span class="sxs-lookup"><span data-stu-id="18c47-587">If the `Order` property of the controller and global filters is set to 1 and 2 respectively, the order of execution is reversed.</span></span>

| <span data-ttu-id="18c47-588">Sequence</span><span class="sxs-lookup"><span data-stu-id="18c47-588">Sequence</span></span> | <span data-ttu-id="18c47-589">Rozsah filtru</span><span class="sxs-lookup"><span data-stu-id="18c47-589">Filter scope</span></span> | <span data-ttu-id="18c47-590">`Order`majetek</span><span class="sxs-lookup"><span data-stu-id="18c47-590">`Order` property</span></span> | <span data-ttu-id="18c47-591">Filter – metoda</span><span class="sxs-lookup"><span data-stu-id="18c47-591">Filter method</span></span> |
|:--------:|:------------:|:-----------------:|:-------------:|
| <span data-ttu-id="18c47-592">1</span><span class="sxs-lookup"><span data-stu-id="18c47-592">1</span></span> | <span data-ttu-id="18c47-593">Metoda</span><span class="sxs-lookup"><span data-stu-id="18c47-593">Method</span></span> | <span data-ttu-id="18c47-594">0</span><span class="sxs-lookup"><span data-stu-id="18c47-594">0</span></span> | `OnActionExecuting` |
| <span data-ttu-id="18c47-595">2</span><span class="sxs-lookup"><span data-stu-id="18c47-595">2</span></span> | <span data-ttu-id="18c47-596">Kontrolér</span><span class="sxs-lookup"><span data-stu-id="18c47-596">Controller</span></span> | <span data-ttu-id="18c47-597">1</span><span class="sxs-lookup"><span data-stu-id="18c47-597">1</span></span>  | `OnActionExecuting` |
| <span data-ttu-id="18c47-598">3</span><span class="sxs-lookup"><span data-stu-id="18c47-598">3</span></span> | <span data-ttu-id="18c47-599">Globální</span><span class="sxs-lookup"><span data-stu-id="18c47-599">Global</span></span> | <span data-ttu-id="18c47-600">2</span><span class="sxs-lookup"><span data-stu-id="18c47-600">2</span></span>  | `OnActionExecuting` |
| <span data-ttu-id="18c47-601">4</span><span class="sxs-lookup"><span data-stu-id="18c47-601">4</span></span> | <span data-ttu-id="18c47-602">Globální</span><span class="sxs-lookup"><span data-stu-id="18c47-602">Global</span></span> | <span data-ttu-id="18c47-603">2</span><span class="sxs-lookup"><span data-stu-id="18c47-603">2</span></span>  | `OnActionExecuted` |
| <span data-ttu-id="18c47-604">5</span><span class="sxs-lookup"><span data-stu-id="18c47-604">5</span></span> | <span data-ttu-id="18c47-605">Kontrolér</span><span class="sxs-lookup"><span data-stu-id="18c47-605">Controller</span></span> | <span data-ttu-id="18c47-606">1</span><span class="sxs-lookup"><span data-stu-id="18c47-606">1</span></span>  | `OnActionExecuted` |
| <span data-ttu-id="18c47-607">6</span><span class="sxs-lookup"><span data-stu-id="18c47-607">6</span></span> | <span data-ttu-id="18c47-608">Metoda</span><span class="sxs-lookup"><span data-stu-id="18c47-608">Method</span></span> | <span data-ttu-id="18c47-609">0</span><span class="sxs-lookup"><span data-stu-id="18c47-609">0</span></span>  | `OnActionExecuted` |

<span data-ttu-id="18c47-610">`Order` Vlastnost Přepisuje obor při určování pořadí, ve kterém jsou filtry spouštěny.</span><span class="sxs-lookup"><span data-stu-id="18c47-610">The `Order` property overrides scope when determining the order in which filters run.</span></span> <span data-ttu-id="18c47-611">Filtry jsou seřazené podle pořadí, pak se k přerušení vztahů používá obor.</span><span class="sxs-lookup"><span data-stu-id="18c47-611">Filters are sorted first by order, then scope is used to break ties.</span></span> <span data-ttu-id="18c47-612">Všechny předdefinované filtry implementují `IOrderedFilter` a nastaví výchozí `Order` hodnotu na 0.</span><span class="sxs-lookup"><span data-stu-id="18c47-612">All of the built-in filters implement `IOrderedFilter` and set the default `Order` value to 0.</span></span> <span data-ttu-id="18c47-613">Pro předdefinované filtry rozsah určuje pořadí, pokud `Order` není nastaven na nenulovou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="18c47-613">For built-in filters, scope determines order unless `Order` is set to a non-zero value.</span></span>

## <a name="cancellation-and-short-circuiting"></a><span data-ttu-id="18c47-614">Zrušení a zkrácení okruhů</span><span class="sxs-lookup"><span data-stu-id="18c47-614">Cancellation and short-circuiting</span></span>

<span data-ttu-id="18c47-615">Kanál filtru může být v krátkém okruhu nastavením <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> vlastnosti pro <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> parametr poskytnutý metodě Filter.</span><span class="sxs-lookup"><span data-stu-id="18c47-615">The filter pipeline can be short-circuited by setting the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> property on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> parameter provided to the filter method.</span></span> <span data-ttu-id="18c47-616">Například následující filtr prostředků zabrání ve spuštění zbývajícího kanálu:</span><span class="sxs-lookup"><span data-stu-id="18c47-616">For instance, the following Resource filter prevents the rest of the pipeline from executing:</span></span>

<a name="short-circuiting-resource-filter"></a>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ShortCircuitingResourceFilterAttribute.cs?name=snippet)]

<span data-ttu-id="18c47-617">V následujícím kódu, `ShortCircuitingResourceFilter` a jako `AddHeader` cíl filtru i cílovou metodu `SomeResource` Action.</span><span class="sxs-lookup"><span data-stu-id="18c47-617">In the following code, both the `ShortCircuitingResourceFilter` and the `AddHeader` filter target the `SomeResource` action method.</span></span> <span data-ttu-id="18c47-618">`ShortCircuitingResourceFilter`:</span><span class="sxs-lookup"><span data-stu-id="18c47-618">The `ShortCircuitingResourceFilter`:</span></span>

* <span data-ttu-id="18c47-619">Nejprve se spustí, protože se jedná o filtr prostředků `AddHeader` a je filtr akcí.</span><span class="sxs-lookup"><span data-stu-id="18c47-619">Runs first, because it's a Resource Filter and `AddHeader` is an Action Filter.</span></span>
* <span data-ttu-id="18c47-620">Krátké okruhy zbývajícího kanálu.</span><span class="sxs-lookup"><span data-stu-id="18c47-620">Short-circuits the rest of the pipeline.</span></span>

<span data-ttu-id="18c47-621">Proto `AddHeader` filtr pro `SomeResource` akci nikdy neběží.</span><span class="sxs-lookup"><span data-stu-id="18c47-621">Therefore the `AddHeader` filter never runs for the `SomeResource` action.</span></span> <span data-ttu-id="18c47-622">Toto chování by bylo stejné, pokud byly oba filtry aplikovány na úrovni metody akce, a to `ShortCircuitingResourceFilter` za předpokladu, že byla spuštěna jako první.</span><span class="sxs-lookup"><span data-stu-id="18c47-622">This behavior would be the same if both filters were applied at the action method level, provided the `ShortCircuitingResourceFilter` ran first.</span></span> <span data-ttu-id="18c47-623">Nejprve `ShortCircuitingResourceFilter` se spustí z důvodu jeho typu filtru nebo explicitního použití `Order` vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="18c47-623">The `ShortCircuitingResourceFilter` runs first because of its filter type, or by explicit use of `Order` property.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1,9)]

## <a name="dependency-injection"></a><span data-ttu-id="18c47-624">Injektáž závislostí</span><span class="sxs-lookup"><span data-stu-id="18c47-624">Dependency injection</span></span>

<span data-ttu-id="18c47-625">Filtry lze přidat podle typu nebo podle instance.</span><span class="sxs-lookup"><span data-stu-id="18c47-625">Filters can be added by type or by instance.</span></span> <span data-ttu-id="18c47-626">Pokud je přidána instance, bude tato instance použita pro každý požadavek.</span><span class="sxs-lookup"><span data-stu-id="18c47-626">If an instance is added, that instance is used for every request.</span></span> <span data-ttu-id="18c47-627">Pokud je přidán typ, je aktivován typ.</span><span class="sxs-lookup"><span data-stu-id="18c47-627">If a type is added, it's type-activated.</span></span> <span data-ttu-id="18c47-628">Filtr aktivovaný typu znamená:</span><span class="sxs-lookup"><span data-stu-id="18c47-628">A type-activated filter means:</span></span>

* <span data-ttu-id="18c47-629">Instance se vytvoří pro každý požadavek.</span><span class="sxs-lookup"><span data-stu-id="18c47-629">An instance is created for each request.</span></span>
* <span data-ttu-id="18c47-630">Jakékoli závislosti konstruktoru jsou vyplněny pomocí [Injektáže závislosti](xref:fundamentals/dependency-injection) (di).</span><span class="sxs-lookup"><span data-stu-id="18c47-630">Any constructor dependencies are populated by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span>

<span data-ttu-id="18c47-631">Filtry, které jsou implementovány jako atributy a přidány přímo do tříd kontroleru nebo metody akcí, nemohou mít závislosti konstruktoru poskytované [vkládáním závislostí](xref:fundamentals/dependency-injection) (di).</span><span class="sxs-lookup"><span data-stu-id="18c47-631">Filters that are implemented as attributes and added directly to controller classes or action methods cannot have constructor dependencies provided by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span> <span data-ttu-id="18c47-632">Pomocí DI nelze poskytnout závislosti konstruktoru:</span><span class="sxs-lookup"><span data-stu-id="18c47-632">Constructor dependencies cannot be provided by DI because:</span></span>

* <span data-ttu-id="18c47-633">V atributech musí být zadány parametry konstruktoru, kde jsou aplikovány.</span><span class="sxs-lookup"><span data-stu-id="18c47-633">Attributes must have their constructor parameters supplied where they're applied.</span></span> 
* <span data-ttu-id="18c47-634">Toto je omezení způsobu fungování atributů.</span><span class="sxs-lookup"><span data-stu-id="18c47-634">This is a limitation of how attributes work.</span></span>

<span data-ttu-id="18c47-635">Následující filtry podporují závislosti konstruktoru poskytované od DI:</span><span class="sxs-lookup"><span data-stu-id="18c47-635">The following filters support constructor dependencies provided from DI:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>
* <span data-ttu-id="18c47-636"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>implementováno u atributu.</span><span class="sxs-lookup"><span data-stu-id="18c47-636"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implemented on the attribute.</span></span>

<span data-ttu-id="18c47-637">Předchozí filtry lze použít pro metodu kontroleru nebo akce:</span><span class="sxs-lookup"><span data-stu-id="18c47-637">The preceding filters can be applied to a controller or action method:</span></span>

<span data-ttu-id="18c47-638">Protokolovací nástroje jsou k dispozici z DI.</span><span class="sxs-lookup"><span data-stu-id="18c47-638">Loggers are available from DI.</span></span> <span data-ttu-id="18c47-639">Ale nevytvářejte a používejte filtry čistě pro účely protokolování.</span><span class="sxs-lookup"><span data-stu-id="18c47-639">However, avoid creating and using filters purely for logging purposes.</span></span> <span data-ttu-id="18c47-640">[Integrované protokolování rozhraní](xref:fundamentals/logging/index) obvykle zajišťuje, co je potřeba pro protokolování.</span><span class="sxs-lookup"><span data-stu-id="18c47-640">The [built-in framework logging](xref:fundamentals/logging/index) typically provides what's needed for logging.</span></span> <span data-ttu-id="18c47-641">Protokolování přidáno do filtrů:</span><span class="sxs-lookup"><span data-stu-id="18c47-641">Logging added to filters:</span></span>

* <span data-ttu-id="18c47-642">Měl by se soustředit na obavy a chování obchodních domén, které jsou specifické pro filtr.</span><span class="sxs-lookup"><span data-stu-id="18c47-642">Should focus on business domain concerns or behavior specific to the filter.</span></span>
* <span data-ttu-id="18c47-643">Neměli **byste** protokolovat akce nebo jiné události rozhraní.</span><span class="sxs-lookup"><span data-stu-id="18c47-643">Should **not** log actions or other framework events.</span></span> <span data-ttu-id="18c47-644">Vestavěné filtry protokolují akce a události rozhraní.</span><span class="sxs-lookup"><span data-stu-id="18c47-644">The built in filters log actions and framework events.</span></span>

### <a name="servicefilterattribute"></a><span data-ttu-id="18c47-645">ServiceFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="18c47-645">ServiceFilterAttribute</span></span>

<span data-ttu-id="18c47-646">Typy implementace filtru služby jsou zaregistrované v `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="18c47-646">Service filter implementation types are registered in `ConfigureServices`.</span></span> <span data-ttu-id="18c47-647"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> Načte instanci filtru z di.</span><span class="sxs-lookup"><span data-stu-id="18c47-647">A <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> retrieves an instance of the filter from DI.</span></span>

<span data-ttu-id="18c47-648">Následující kód ukazuje `AddHeaderResultServiceFilter`:</span><span class="sxs-lookup"><span data-stu-id="18c47-648">The following code shows the `AddHeaderResultServiceFilter`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="18c47-649">V následujícím kódu `AddHeaderResultServiceFilter` je přidán do kontejneru di:</span><span class="sxs-lookup"><span data-stu-id="18c47-649">In the following code, `AddHeaderResultServiceFilter` is added to the DI container:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

<span data-ttu-id="18c47-650">V následujícím kódu `ServiceFilter` atribut načte instanci `AddHeaderResultServiceFilter` filtru z di:</span><span class="sxs-lookup"><span data-stu-id="18c47-650">In the following code, the `ServiceFilter` attribute retrieves an instance of the `AddHeaderResultServiceFilter` filter from DI:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_ServiceFilter&highlight=1)]

<span data-ttu-id="18c47-651">Při použití `ServiceFilterAttribute`nastavte [ServiceFilterAttribute. IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span><span class="sxs-lookup"><span data-stu-id="18c47-651">When using `ServiceFilterAttribute`, setting [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span></span>

* <span data-ttu-id="18c47-652">Poskytuje nápovědu, že se instance filtru *dá* znovu použít mimo rozsah požadavku, který byl vytvořen v rámci.</span><span class="sxs-lookup"><span data-stu-id="18c47-652">Provides a hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="18c47-653">Modul runtime ASP.NET Core nezaručuje:</span><span class="sxs-lookup"><span data-stu-id="18c47-653">The ASP.NET Core runtime doesn't guarantee:</span></span>

  * <span data-ttu-id="18c47-654">Vytvoří se jedna instance filtru.</span><span class="sxs-lookup"><span data-stu-id="18c47-654">That a single instance of the filter will be created.</span></span>
  * <span data-ttu-id="18c47-655">Filtr nebude znovu vyžádán z kontejneru DI v pozdějším bodě.</span><span class="sxs-lookup"><span data-stu-id="18c47-655">The filter will not be re-requested from the DI container at some later point.</span></span>

* <span data-ttu-id="18c47-656">Neměl by se používat s filtrem, který závisí na službách s jinou životností než singleton.</span><span class="sxs-lookup"><span data-stu-id="18c47-656">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

 <span data-ttu-id="18c47-657"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>implementuje <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span><span class="sxs-lookup"><span data-stu-id="18c47-657"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="18c47-658">`IFilterFactory`zpřístupňuje <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> metodu pro vytvoření <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span><span class="sxs-lookup"><span data-stu-id="18c47-658">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="18c47-659">`CreateInstance`Načte zadaný typ z DI.</span><span class="sxs-lookup"><span data-stu-id="18c47-659">`CreateInstance` loads the specified type from DI.</span></span>

### <a name="typefilterattribute"></a><span data-ttu-id="18c47-660">TypeFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="18c47-660">TypeFilterAttribute</span></span>

<span data-ttu-id="18c47-661"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>je podobný <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, ale jeho typ není vyřešen přímo z kontejneru di.</span><span class="sxs-lookup"><span data-stu-id="18c47-661"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> is similar to <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, but its type isn't resolved directly from the DI container.</span></span> <span data-ttu-id="18c47-662">Vytvoří instanci typu pomocí <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="18c47-662">It instantiates the type by using <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span></span>

<span data-ttu-id="18c47-663">Protože `TypeFilterAttribute` typy nejsou vyřešeny přímo z kontejneru di:</span><span class="sxs-lookup"><span data-stu-id="18c47-663">Because `TypeFilterAttribute` types aren't resolved directly from the DI container:</span></span>

* <span data-ttu-id="18c47-664">Typy, na které se odkazuje `TypeFilterAttribute` pomocí, není nutné registrovat v kontejneru di.</span><span class="sxs-lookup"><span data-stu-id="18c47-664">Types that are referenced using the `TypeFilterAttribute` don't need to be registered with the DI container.</span></span>  <span data-ttu-id="18c47-665">Mají své závislosti splněné kontejnerem DI.</span><span class="sxs-lookup"><span data-stu-id="18c47-665">They do have their dependencies fulfilled by the DI container.</span></span>
* <span data-ttu-id="18c47-666">`TypeFilterAttribute`může volitelně přijmout argumenty konstruktoru pro typ.</span><span class="sxs-lookup"><span data-stu-id="18c47-666">`TypeFilterAttribute` can optionally accept constructor arguments for the type.</span></span>

<span data-ttu-id="18c47-667">Při použití `TypeFilterAttribute`nastavte [TypeFilterAttribute. IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span><span class="sxs-lookup"><span data-stu-id="18c47-667">When using `TypeFilterAttribute`, setting [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span></span>
* <span data-ttu-id="18c47-668">Poskytuje nápovědu, že se instance filtru *dá* znovu použít mimo rozsah požadavku, který byl vytvořen v rámci.</span><span class="sxs-lookup"><span data-stu-id="18c47-668">Provides hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="18c47-669">Modul runtime ASP.NET Core neposkytuje žádné záruky, že se vytvoří jediná instance filtru.</span><span class="sxs-lookup"><span data-stu-id="18c47-669">The ASP.NET Core runtime provides no guarantees that a single instance of the filter will be created.</span></span>

* <span data-ttu-id="18c47-670">Neměl by se používat s filtrem, který závisí na službách s jinou životností než singleton.</span><span class="sxs-lookup"><span data-stu-id="18c47-670">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

<span data-ttu-id="18c47-671">Následující příklad ukazuje, jak předat argumenty typu pomocí `TypeFilterAttribute`:</span><span class="sxs-lookup"><span data-stu-id="18c47-671">The following example shows how to pass arguments to a type using `TypeFilterAttribute`:</span></span>

[!code-csharp[](../../mvc/controllers/filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_TypeFilter&highlight=1,2)]
[!code-csharp[](../../mvc/controllers/filters/sample/FiltersSample/Filters/LogConstantFilter.cs?name=snippet_TypeFilter_Implementation&highlight=6)]

<!-- 
https://localhost:5001/home/hi?name=joe
VS debug window shows 
FiltersSample.Filters.LogConstantFilter:Information: Method 'Hi' called
-->

## <a name="authorization-filters"></a><span data-ttu-id="18c47-672">Filtry autorizace</span><span class="sxs-lookup"><span data-stu-id="18c47-672">Authorization filters</span></span>

<span data-ttu-id="18c47-673">Filtry autorizace:</span><span class="sxs-lookup"><span data-stu-id="18c47-673">Authorization filters:</span></span>

* <span data-ttu-id="18c47-674">Jsou první filtry spouštěny v kanálu filtru.</span><span class="sxs-lookup"><span data-stu-id="18c47-674">Are the first filters run in the filter pipeline.</span></span>
* <span data-ttu-id="18c47-675">Řízení přístupu k metodám akcí.</span><span class="sxs-lookup"><span data-stu-id="18c47-675">Control access to action methods.</span></span>
* <span data-ttu-id="18c47-676">Před metodou, ale ne po metodě.</span><span class="sxs-lookup"><span data-stu-id="18c47-676">Have a before method, but no after method.</span></span>

<span data-ttu-id="18c47-677">Vlastní autorizační filtry vyžadují vlastní autorizační rozhraní.</span><span class="sxs-lookup"><span data-stu-id="18c47-677">Custom authorization filters require a custom authorization framework.</span></span> <span data-ttu-id="18c47-678">Preferovat konfiguraci autorizačních zásad nebo psaní vlastních zásad autorizace při psaní vlastního filtru.</span><span class="sxs-lookup"><span data-stu-id="18c47-678">Prefer configuring the authorization policies or writing a custom authorization policy over writing a custom filter.</span></span> <span data-ttu-id="18c47-679">Vestavěný autorizační filtr:</span><span class="sxs-lookup"><span data-stu-id="18c47-679">The built-in authorization filter:</span></span>

* <span data-ttu-id="18c47-680">Volá autorizační systém.</span><span class="sxs-lookup"><span data-stu-id="18c47-680">Calls the authorization system.</span></span>
* <span data-ttu-id="18c47-681">Neautorizuje požadavky.</span><span class="sxs-lookup"><span data-stu-id="18c47-681">Does not authorize requests.</span></span>

<span data-ttu-id="18c47-682">Negenerovat **výjimky** v rámci autorizačních filtrů:</span><span class="sxs-lookup"><span data-stu-id="18c47-682">Do **not** throw exceptions within authorization filters:</span></span>

* <span data-ttu-id="18c47-683">Výjimka nebude zpracována.</span><span class="sxs-lookup"><span data-stu-id="18c47-683">The exception will not be handled.</span></span>
* <span data-ttu-id="18c47-684">Filtry výjimek nebudou zpracovávat výjimku.</span><span class="sxs-lookup"><span data-stu-id="18c47-684">Exception filters will not handle the exception.</span></span>

<span data-ttu-id="18c47-685">Zvažte vydání výzvy, pokud dojde k výjimce ve autorizačním filtru.</span><span class="sxs-lookup"><span data-stu-id="18c47-685">Consider issuing a challenge when an exception occurs in an authorization filter.</span></span>

<span data-ttu-id="18c47-686">Přečtěte si další informace o [autorizaci](xref:security/authorization/introduction).</span><span class="sxs-lookup"><span data-stu-id="18c47-686">Learn more about [Authorization](xref:security/authorization/introduction).</span></span>

## <a name="resource-filters"></a><span data-ttu-id="18c47-687">Filtry prostředků</span><span class="sxs-lookup"><span data-stu-id="18c47-687">Resource filters</span></span>

<span data-ttu-id="18c47-688">Filtry prostředků:</span><span class="sxs-lookup"><span data-stu-id="18c47-688">Resource filters:</span></span>

* <span data-ttu-id="18c47-689">Implementujte rozhraní <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> nebo <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> .</span><span class="sxs-lookup"><span data-stu-id="18c47-689">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> interface.</span></span>
* <span data-ttu-id="18c47-690">Provádění zabalí většinu kanálu filtru.</span><span class="sxs-lookup"><span data-stu-id="18c47-690">Execution wraps most of the filter pipeline.</span></span>
* <span data-ttu-id="18c47-691">Před filtry prostředků se spouštějí jenom [filtry autorizace](#authorization-filters) .</span><span class="sxs-lookup"><span data-stu-id="18c47-691">Only [Authorization filters](#authorization-filters) run before resource filters.</span></span>

<span data-ttu-id="18c47-692">Filtry prostředků jsou užitečné pro krátkodobé okruhy v kanálu.</span><span class="sxs-lookup"><span data-stu-id="18c47-692">Resource filters are useful to short-circuit most of the pipeline.</span></span> <span data-ttu-id="18c47-693">Například filtr ukládání do mezipaměti může zabránit zbývajícímu kanálu v případě přístupů do mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="18c47-693">For example, a caching filter can avoid the rest of the pipeline on a cache hit.</span></span>

<span data-ttu-id="18c47-694">Příklady filtru prostředků:</span><span class="sxs-lookup"><span data-stu-id="18c47-694">Resource filter examples:</span></span>

* <span data-ttu-id="18c47-695">[Filtr prostředků se zkrácenými okruhy](#short-circuiting-resource-filter) zobrazenými dříve.</span><span class="sxs-lookup"><span data-stu-id="18c47-695">[The short-circuiting resource filter](#short-circuiting-resource-filter) shown previously.</span></span>
* <span data-ttu-id="18c47-696">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span><span class="sxs-lookup"><span data-stu-id="18c47-696">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span></span>

  * <span data-ttu-id="18c47-697">Zabraňuje vazbě modelu v přístupu k datům formuláře.</span><span class="sxs-lookup"><span data-stu-id="18c47-697">Prevents model binding from accessing the form data.</span></span>
  * <span data-ttu-id="18c47-698">Používá se pro nahrávání velkých souborů, aby se zabránilo čtení dat z formuláře do paměti.</span><span class="sxs-lookup"><span data-stu-id="18c47-698">Used for large file uploads to prevent the form data from being read into memory.</span></span>

## <a name="action-filters"></a><span data-ttu-id="18c47-699">Filtry akcí</span><span class="sxs-lookup"><span data-stu-id="18c47-699">Action filters</span></span>

> [!IMPORTANT]
> <span data-ttu-id="18c47-700">Filtry **akcí se nevztahují** na Razor stránky.</span><span class="sxs-lookup"><span data-stu-id="18c47-700">Action filters do **not** apply to Razor Pages.</span></span> Razor<span data-ttu-id="18c47-701">Stránky podporují <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> a <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span><span class="sxs-lookup"><span data-stu-id="18c47-701"> Pages supports <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span></span> <span data-ttu-id="18c47-702">Další informace najdete v tématu [metody filtrování pro Razor stránky](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="18c47-702">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

<span data-ttu-id="18c47-703">Filtry akcí:</span><span class="sxs-lookup"><span data-stu-id="18c47-703">Action filters:</span></span>

* <span data-ttu-id="18c47-704">Implementujte rozhraní <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> nebo <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> .</span><span class="sxs-lookup"><span data-stu-id="18c47-704">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> interface.</span></span>
* <span data-ttu-id="18c47-705">Jejich spuštění obklopuje provádění metod akcí.</span><span class="sxs-lookup"><span data-stu-id="18c47-705">Their execution surrounds the execution of action methods.</span></span>

<span data-ttu-id="18c47-706">Následující kód ukazuje filtr vzorové akce:</span><span class="sxs-lookup"><span data-stu-id="18c47-706">The following code shows a sample action filter:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="18c47-707"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> Poskytuje následující vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="18c47-707">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> provides the following properties:</span></span>

* <span data-ttu-id="18c47-708"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments>– povolí čtení vstupů do metody akce.</span><span class="sxs-lookup"><span data-stu-id="18c47-708"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> - enables the inputs to an action method be read.</span></span>
* <span data-ttu-id="18c47-709"><xref:Microsoft.AspNetCore.Mvc.Controller>– povolí manipulaci s instancí kontroleru.</span><span class="sxs-lookup"><span data-stu-id="18c47-709"><xref:Microsoft.AspNetCore.Mvc.Controller> - enables manipulating the controller instance.</span></span>
* <span data-ttu-id="18c47-710"><xref:System.Web.Mvc.ActionExecutingContext.Result>– nastavení `Result` krátkodobého provádění metody akce a následných filtrů akcí.</span><span class="sxs-lookup"><span data-stu-id="18c47-710"><xref:System.Web.Mvc.ActionExecutingContext.Result> - setting `Result` short-circuits execution of the action method and subsequent action filters.</span></span>

<span data-ttu-id="18c47-711">Vyvolání výjimky v metodě akce:</span><span class="sxs-lookup"><span data-stu-id="18c47-711">Throwing an exception in an action method:</span></span>

* <span data-ttu-id="18c47-712">Zabraňuje spuštění dalších filtrů.</span><span class="sxs-lookup"><span data-stu-id="18c47-712">Prevents running of subsequent filters.</span></span>
* <span data-ttu-id="18c47-713">Na rozdíl od `Result`nastavení se místo úspěšného výsledku považuje za selhání.</span><span class="sxs-lookup"><span data-stu-id="18c47-713">Unlike setting `Result`, is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="18c47-714"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> Poskytuje `Controller` a `Result` plus následující vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="18c47-714">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> provides `Controller` and `Result` plus the following properties:</span></span>

* <span data-ttu-id="18c47-715"><xref:System.Web.Mvc.ActionExecutedContext.Canceled>-True, pokud bylo provedení akce zkráceno jiným filtrem.</span><span class="sxs-lookup"><span data-stu-id="18c47-715"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> - True if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="18c47-716"><xref:System.Web.Mvc.ActionExecutedContext.Exception>-Hodnota není null, pokud akce nebo dříve vyvolala filtr akcí.</span><span class="sxs-lookup"><span data-stu-id="18c47-716"><xref:System.Web.Mvc.ActionExecutedContext.Exception> - Non-null if the action or a previously run action filter threw an exception.</span></span> <span data-ttu-id="18c47-717">Nastavení této vlastnosti na hodnotu null:</span><span class="sxs-lookup"><span data-stu-id="18c47-717">Setting this property to null:</span></span>

  * <span data-ttu-id="18c47-718">Efektivně zpracovává výjimku.</span><span class="sxs-lookup"><span data-stu-id="18c47-718">Effectively handles the exception.</span></span>
  * <span data-ttu-id="18c47-719">`Result`je spuštěn, jako kdyby byl vrácen z metody Action.</span><span class="sxs-lookup"><span data-stu-id="18c47-719">`Result` is executed as if it was returned from the action method.</span></span>

<span data-ttu-id="18c47-720">`IAsyncActionFilter`V případě volání <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span><span class="sxs-lookup"><span data-stu-id="18c47-720">For an `IAsyncActionFilter`, a call to the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span></span>

* <span data-ttu-id="18c47-721">Provede všechny následné filtry akcí a metodu Action.</span><span class="sxs-lookup"><span data-stu-id="18c47-721">Executes any subsequent action filters and the action method.</span></span>
* <span data-ttu-id="18c47-722">Vrací objekt `ActionExecutedContext`.</span><span class="sxs-lookup"><span data-stu-id="18c47-722">Returns `ActionExecutedContext`.</span></span>

<span data-ttu-id="18c47-723">Do krátkodobého okruhu, <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> přiřaďte k instanci výsledku a Nevolejte `next` ( `ActionExecutionDelegate`a).</span><span class="sxs-lookup"><span data-stu-id="18c47-723">To short-circuit, assign <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> to a result instance and don't call `next` (the `ActionExecutionDelegate`).</span></span>

<span data-ttu-id="18c47-724">Rozhraní poskytuje abstrakci <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> , která může být podtříd.</span><span class="sxs-lookup"><span data-stu-id="18c47-724">The framework provides an abstract <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> that can be subclassed.</span></span>

<span data-ttu-id="18c47-725">Filtr `OnActionExecuting` akcí lze použít k těmto akcím:</span><span class="sxs-lookup"><span data-stu-id="18c47-725">The `OnActionExecuting` action filter can be used to:</span></span>

* <span data-ttu-id="18c47-726">Ověří stav modelu.</span><span class="sxs-lookup"><span data-stu-id="18c47-726">Validate model state.</span></span>
* <span data-ttu-id="18c47-727">Pokud je stav neplatný, vrátí se chyba.</span><span class="sxs-lookup"><span data-stu-id="18c47-727">Return an error if the state is invalid.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet)]

<span data-ttu-id="18c47-728">Metoda `OnActionExecuted` se spustí za metodou akce:</span><span class="sxs-lookup"><span data-stu-id="18c47-728">The `OnActionExecuted` method runs after the action method:</span></span>

* <span data-ttu-id="18c47-729">A mohou zobrazit výsledky akce a manipulovat s nimi prostřednictvím <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="18c47-729">And can see and manipulate the results of the action through the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> property.</span></span>
* <span data-ttu-id="18c47-730"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled>je nastaven na hodnotu true, pokud bylo provedení akce zkráceno jiným filtrem.</span><span class="sxs-lookup"><span data-stu-id="18c47-730"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> is set to true if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="18c47-731"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception>je nastavena na hodnotu jinou než null, pokud akce nebo filtr následné akce vyvolaly výjimku.</span><span class="sxs-lookup"><span data-stu-id="18c47-731"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> is set to a non-null value if the action or a subsequent action filter threw an exception.</span></span> <span data-ttu-id="18c47-732">Nastavení `Exception` na hodnotu null:</span><span class="sxs-lookup"><span data-stu-id="18c47-732">Setting `Exception` to null:</span></span>

  * <span data-ttu-id="18c47-733">Efektivně zpracovává výjimku.</span><span class="sxs-lookup"><span data-stu-id="18c47-733">Effectively handles an exception.</span></span>
  * <span data-ttu-id="18c47-734">`ActionExecutedContext.Result`je spuštěn, jako kdyby byl vrácen normálně z metody Action.</span><span class="sxs-lookup"><span data-stu-id="18c47-734">`ActionExecutedContext.Result` is executed as if it were returned normally from the action method.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet2&higlight=12-99)]

## <a name="exception-filters"></a><span data-ttu-id="18c47-735">Filtry výjimek</span><span class="sxs-lookup"><span data-stu-id="18c47-735">Exception filters</span></span>

<span data-ttu-id="18c47-736">Filtry výjimek:</span><span class="sxs-lookup"><span data-stu-id="18c47-736">Exception filters:</span></span>

* <span data-ttu-id="18c47-737"><xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> Implementujte <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>nebo.</span><span class="sxs-lookup"><span data-stu-id="18c47-737">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span></span> 
* <span data-ttu-id="18c47-738">Dá se použít k implementaci běžných zásad zpracování chyb.</span><span class="sxs-lookup"><span data-stu-id="18c47-738">Can be used to implement common error handling policies.</span></span>

<span data-ttu-id="18c47-739">Následující ukázkový filtr výjimek používá vlastní zobrazení chyb pro zobrazení podrobností o výjimkách, ke kterým dochází při vývoji aplikace:</span><span class="sxs-lookup"><span data-stu-id="18c47-739">The following sample exception filter uses a custom error view to display details about exceptions that occur when the app is in development:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/CustomExceptionFilter.cs?name=snippet_ExceptionFilter&highlight=16-19)]

<span data-ttu-id="18c47-740">Filtry výjimek:</span><span class="sxs-lookup"><span data-stu-id="18c47-740">Exception filters:</span></span>

* <span data-ttu-id="18c47-741">Nemusíte mít události před a po.</span><span class="sxs-lookup"><span data-stu-id="18c47-741">Don't have before and after events.</span></span>
* <span data-ttu-id="18c47-742"><xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> Implementujte <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>nebo.</span><span class="sxs-lookup"><span data-stu-id="18c47-742">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span></span>
* <span data-ttu-id="18c47-743">Zpracování neošetřených výjimek, ke kterým Razor dochází při vytváření stránky nebo kontroleru, [vázání modelů](xref:mvc/models/model-binding), filtrů akcí nebo metod akcí.</span><span class="sxs-lookup"><span data-stu-id="18c47-743">Handle unhandled exceptions that occur in Razor Page or controller creation, [model binding](xref:mvc/models/model-binding), action filters, or action methods.</span></span>
* <span data-ttu-id="18c47-744">**Nezachycujte** výjimky, ke kterým dochází v filtrech prostředků, výsledných filtrech nebo v důsledku provádění výsledků MVC.</span><span class="sxs-lookup"><span data-stu-id="18c47-744">Do **not** catch exceptions that occur in resource filters, result filters, or MVC result execution.</span></span>

<span data-ttu-id="18c47-745">Chcete-li zpracovat výjimku, nastavte <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> vlastnost na `true` nebo zapište odpověď.</span><span class="sxs-lookup"><span data-stu-id="18c47-745">To handle an exception, set the <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> property to `true` or write a response.</span></span> <span data-ttu-id="18c47-746">Tím se zastaví šíření výjimky.</span><span class="sxs-lookup"><span data-stu-id="18c47-746">This stops propagation of the exception.</span></span> <span data-ttu-id="18c47-747">Filtr výjimek nemůže vypnout výjimku na "úspěch".</span><span class="sxs-lookup"><span data-stu-id="18c47-747">An exception filter can't turn an exception into a "success".</span></span> <span data-ttu-id="18c47-748">To může provést pouze filtr akcí.</span><span class="sxs-lookup"><span data-stu-id="18c47-748">Only an action filter can do that.</span></span>

<span data-ttu-id="18c47-749">Filtry výjimek:</span><span class="sxs-lookup"><span data-stu-id="18c47-749">Exception filters:</span></span>

* <span data-ttu-id="18c47-750">Jsou vhodné pro vytváření přesahů výjimek, ke kterým dochází v rámci akcí.</span><span class="sxs-lookup"><span data-stu-id="18c47-750">Are good for trapping exceptions that occur within actions.</span></span>
* <span data-ttu-id="18c47-751">Nejsou tak flexibilní jako middleware při zpracování chyb.</span><span class="sxs-lookup"><span data-stu-id="18c47-751">Are not as flexible as error handling middleware.</span></span>

<span data-ttu-id="18c47-752">Preferovat middleware pro zpracování výjimek.</span><span class="sxs-lookup"><span data-stu-id="18c47-752">Prefer middleware for exception handling.</span></span> <span data-ttu-id="18c47-753">Filtry výjimek použijte pouze v případě, že se zpracování chyb *liší* v závislosti na tom, která metoda Action je volána.</span><span class="sxs-lookup"><span data-stu-id="18c47-753">Use exception filters only where error handling *differs* based on which action method is called.</span></span> <span data-ttu-id="18c47-754">Aplikace může například mít metody akcí pro koncové body rozhraní API i pro zobrazení/HTML.</span><span class="sxs-lookup"><span data-stu-id="18c47-754">For example, an app might have action methods for both API endpoints and for views/HTML.</span></span> <span data-ttu-id="18c47-755">Koncové body rozhraní API mohou vracet informace o chybě jako JSON, zatímco akce na základě zobrazení by mohly vracet chybovou stránku jako HTML.</span><span class="sxs-lookup"><span data-stu-id="18c47-755">The API endpoints could return error information as JSON, while the view-based actions could return an error page as HTML.</span></span>

## <a name="result-filters"></a><span data-ttu-id="18c47-756">Filtry výsledků</span><span class="sxs-lookup"><span data-stu-id="18c47-756">Result filters</span></span>

<span data-ttu-id="18c47-757">Filtry výsledků:</span><span class="sxs-lookup"><span data-stu-id="18c47-757">Result filters:</span></span>

* <span data-ttu-id="18c47-758">Implementace rozhraní:</span><span class="sxs-lookup"><span data-stu-id="18c47-758">Implement an interface:</span></span>
  * <span data-ttu-id="18c47-759"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> nebo <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="18c47-759"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
  * <span data-ttu-id="18c47-760"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> nebo <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span><span class="sxs-lookup"><span data-stu-id="18c47-760"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span></span>
* <span data-ttu-id="18c47-761">Jejich spuštění obklopuje provádění výsledků akcí.</span><span class="sxs-lookup"><span data-stu-id="18c47-761">Their execution surrounds the execution of action results.</span></span>

### <a name="iresultfilter-and-iasyncresultfilter"></a><span data-ttu-id="18c47-762">IResultFilter a IAsyncResultFilter</span><span class="sxs-lookup"><span data-stu-id="18c47-762">IResultFilter and IAsyncResultFilter</span></span>

<span data-ttu-id="18c47-763">Následující kód ukazuje filtr výsledků, který přidá hlavičku protokolu HTTP:</span><span class="sxs-lookup"><span data-stu-id="18c47-763">The following code shows a result filter that adds an HTTP header:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="18c47-764">Typ výsledku, který se má provést, závisí na akci.</span><span class="sxs-lookup"><span data-stu-id="18c47-764">The kind of result being executed depends on the action.</span></span> <span data-ttu-id="18c47-765">Akce vracející zobrazení zahrnuje všechny zpracování Razor jako součást <xref:Microsoft.AspNetCore.Mvc.ViewResult> prováděné.</span><span class="sxs-lookup"><span data-stu-id="18c47-765">An action returning a view would include all razor processing as part of the <xref:Microsoft.AspNetCore.Mvc.ViewResult> being executed.</span></span> <span data-ttu-id="18c47-766">Metoda rozhraní API může provést určitou serializaci v rámci provádění výsledku.</span><span class="sxs-lookup"><span data-stu-id="18c47-766">An API method might perform some serialization as part of the execution of the result.</span></span> <span data-ttu-id="18c47-767">Přečtěte si další informace o [výsledcích akcí](xref:mvc/controllers/actions).</span><span class="sxs-lookup"><span data-stu-id="18c47-767">Learn more about [action results](xref:mvc/controllers/actions).</span></span>

<span data-ttu-id="18c47-768">Filtry výsledků se spustí pouze v případě, že akce nebo filtr akcí vytvoří výsledek akce.</span><span class="sxs-lookup"><span data-stu-id="18c47-768">Result filters are only executed when an action or action filter produces an action result.</span></span> <span data-ttu-id="18c47-769">Filtry výsledků nejsou provedeny v těchto případech:</span><span class="sxs-lookup"><span data-stu-id="18c47-769">Result filters are not executed when:</span></span>

* <span data-ttu-id="18c47-770">Filtr autorizace nebo filtr prostředků pro krátké okruhy kanálu.</span><span class="sxs-lookup"><span data-stu-id="18c47-770">An authorization filter or resource filter short-circuits the pipeline.</span></span>
* <span data-ttu-id="18c47-771">Filtr výjimek zpracovává výjimku tím, že vyprodukuje výsledek akce.</span><span class="sxs-lookup"><span data-stu-id="18c47-771">An exception filter handles an exception by producing an action result.</span></span>

<span data-ttu-id="18c47-772"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> Metoda může zkrátit provádění výsledků akce a následných filtrů výsledků nastavením <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> na `true`.</span><span class="sxs-lookup"><span data-stu-id="18c47-772">The <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> method can short-circuit execution of the action result and subsequent result filters by setting <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> to `true`.</span></span> <span data-ttu-id="18c47-773">Zapište do objektu Response v případě krátkého okruhu, abyste se vyhnuli vygenerování prázdné odpovědi.</span><span class="sxs-lookup"><span data-stu-id="18c47-773">Write to the response object when short-circuiting to avoid generating an empty response.</span></span> <span data-ttu-id="18c47-774">Vyvolání výjimky v `IResultFilter.OnResultExecuting` bude:</span><span class="sxs-lookup"><span data-stu-id="18c47-774">Throwing an exception in `IResultFilter.OnResultExecuting` will:</span></span>

* <span data-ttu-id="18c47-775">Zabraňte provádění výsledků akce a dalších filtrů.</span><span class="sxs-lookup"><span data-stu-id="18c47-775">Prevent execution of the action result and subsequent filters.</span></span>
* <span data-ttu-id="18c47-776">Být považována za selhání namísto úspěšného výsledku.</span><span class="sxs-lookup"><span data-stu-id="18c47-776">Be treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="18c47-777">Při spuštění <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> metody je odpověď pravděpodobně již odeslána klientovi.</span><span class="sxs-lookup"><span data-stu-id="18c47-777">When the <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> method runs, the response has likely already been sent to the client.</span></span> <span data-ttu-id="18c47-778">Pokud byla odpověď již odeslána klientovi, nelze ji dále změnit.</span><span class="sxs-lookup"><span data-stu-id="18c47-778">If the response has already been sent to the client, it cannot be changed further.</span></span>

<span data-ttu-id="18c47-779">`ResultExecutedContext.Canceled`je nastaven na `true` hodnotu, pokud je spuštění výsledku akce zkráceno jiným filtrem.</span><span class="sxs-lookup"><span data-stu-id="18c47-779">`ResultExecutedContext.Canceled` is set to `true` if the action result execution was short-circuited by another filter.</span></span>

<span data-ttu-id="18c47-780">`ResultExecutedContext.Exception`je nastavena na hodnotu jinou než null, pokud výsledek akce nebo následný filtr výsledků vyvolal výjimku.</span><span class="sxs-lookup"><span data-stu-id="18c47-780">`ResultExecutedContext.Exception` is set to a non-null value if the action result or a subsequent result filter threw an exception.</span></span> <span data-ttu-id="18c47-781">Nastavení `Exception` na hodnotu null efektivně zpracovává výjimku a brání výjimce znovu vyvolat ASP.NET Core později v kanálu.</span><span class="sxs-lookup"><span data-stu-id="18c47-781">Setting `Exception` to null effectively handles an exception and prevents the exception from being rethrown by ASP.NET Core later in the pipeline.</span></span> <span data-ttu-id="18c47-782">Neexistuje žádný spolehlivý způsob, jak zapisovat data do odpovědi při zpracování výjimky ve filtru výsledků.</span><span class="sxs-lookup"><span data-stu-id="18c47-782">There is no reliable way to write data to a response when handling an exception in a result filter.</span></span> <span data-ttu-id="18c47-783">Pokud byly hlavičky vyprázdněny do klienta, pokud výsledek akce vyvolá výjimku, neexistuje žádný spolehlivý mechanismus pro odeslání kódu chyby.</span><span class="sxs-lookup"><span data-stu-id="18c47-783">If the headers have been flushed to the client when an action result throws an exception, there's no reliable mechanism to send a failure code.</span></span>

<span data-ttu-id="18c47-784">V případě volání na se <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> spustí všechny následné filtry výsledků a výsledek akce. <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter> `await next`</span><span class="sxs-lookup"><span data-stu-id="18c47-784">For an <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, a call to `await next` on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> executes any subsequent result filters and the action result.</span></span> <span data-ttu-id="18c47-785">Do krátkodobého okruhu nastavte [ResultExecutingContext. Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) na `true` a Nevolejte na `ResultExecutionDelegate`:</span><span class="sxs-lookup"><span data-stu-id="18c47-785">To short-circuit, set [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) to `true` and don't call the `ResultExecutionDelegate`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MyAsyncResponseFilter.cs?name=snippet)]

<span data-ttu-id="18c47-786">Rozhraní poskytuje abstrakci `ResultFilterAttribute` , která může být podtříd.</span><span class="sxs-lookup"><span data-stu-id="18c47-786">The framework provides an abstract `ResultFilterAttribute` that can be subclassed.</span></span> <span data-ttu-id="18c47-787">Dříve uvedená třída [AddHeaderAttribute](#add-header-attribute) je příkladem atributu filtru výsledků.</span><span class="sxs-lookup"><span data-stu-id="18c47-787">The [AddHeaderAttribute](#add-header-attribute) class shown previously is an example of a result filter attribute.</span></span>

### <a name="ialwaysrunresultfilter-and-iasyncalwaysrunresultfilter"></a><span data-ttu-id="18c47-788">IAlwaysRunResultFilter a IAsyncAlwaysRunResultFilter</span><span class="sxs-lookup"><span data-stu-id="18c47-788">IAlwaysRunResultFilter and IAsyncAlwaysRunResultFilter</span></span>

<span data-ttu-id="18c47-789">Rozhraní <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> a <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> deklaruje <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> implementaci, která je spuštěna pro všechny výsledky akce.</span><span class="sxs-lookup"><span data-stu-id="18c47-789">The <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> interfaces declare an <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> implementation that runs for all action results.</span></span> <span data-ttu-id="18c47-790">To zahrnuje výsledky akcí, které vytvořil:</span><span class="sxs-lookup"><span data-stu-id="18c47-790">This includes action results produced by:</span></span>

* <span data-ttu-id="18c47-791">Filtry autorizace a filtry prostředků, které mají krátký okruh.</span><span class="sxs-lookup"><span data-stu-id="18c47-791">Authorization filters and resource filters that short-circuit.</span></span>
* <span data-ttu-id="18c47-792">Filtry výjimek.</span><span class="sxs-lookup"><span data-stu-id="18c47-792">Exception filters.</span></span>

<span data-ttu-id="18c47-793">Například následující filtr vždy spustí a nastaví výsledek akce (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) s *422 nezpracovaným* stavovým kódem entity, pokud se nezdaří vyjednávání obsahu:</span><span class="sxs-lookup"><span data-stu-id="18c47-793">For example, the following filter always runs and sets an action result (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) with a *422 Unprocessable Entity* status code when content negotiation fails:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/UnprocessableResultFilter.cs?name=snippet)]

### <a name="ifilterfactory"></a><span data-ttu-id="18c47-794">IFilterFactory</span><span class="sxs-lookup"><span data-stu-id="18c47-794">IFilterFactory</span></span>

<span data-ttu-id="18c47-795"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>implementuje <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span><span class="sxs-lookup"><span data-stu-id="18c47-795"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span></span> <span data-ttu-id="18c47-796">Proto může být `IFilterFactory` instance použita jako `IFilterMetadata` instance kdekoli v kanálu filtru.</span><span class="sxs-lookup"><span data-stu-id="18c47-796">Therefore, an `IFilterFactory` instance can be used as an `IFilterMetadata` instance anywhere in the filter pipeline.</span></span> <span data-ttu-id="18c47-797">Když se modul runtime připraví k vyvolání filtru, pokusí se ho přetypovat na `IFilterFactory`.</span><span class="sxs-lookup"><span data-stu-id="18c47-797">When the runtime prepares to invoke the filter, it attempts to cast it to an `IFilterFactory`.</span></span> <span data-ttu-id="18c47-798">Pokud je toto přetypování úspěšné, <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> je volána metoda pro vytvoření `IFilterMetadata` instance, která je vyvolána.</span><span class="sxs-lookup"><span data-stu-id="18c47-798">If that cast succeeds, the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method is called to create the `IFilterMetadata` instance that is invoked.</span></span> <span data-ttu-id="18c47-799">To poskytuje flexibilní návrh, protože přesný kanál filtru není nutné nastavit explicitně při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="18c47-799">This provides a flexible design, since the precise filter pipeline doesn't need to be set explicitly when the app starts.</span></span>

<span data-ttu-id="18c47-800">`IFilterFactory`může být implementováno pomocí implementace vlastního atributu jako jiný přístup k vytváření filtrů:</span><span class="sxs-lookup"><span data-stu-id="18c47-800">`IFilterFactory` can be implemented using custom attribute implementations as another approach to creating filters:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/AddHeaderWithFactoryAttribute.cs?name=snippet_IFilterFactory&highlight=1,4,5,6,7)]

<span data-ttu-id="18c47-801">Předchozí kód lze otestovat spuštěním [ukázky stahování](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample):</span><span class="sxs-lookup"><span data-stu-id="18c47-801">The preceding code can be tested by running the [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample):</span></span>

* <span data-ttu-id="18c47-802">Vyvolejte vývojářské nástroje F12.</span><span class="sxs-lookup"><span data-stu-id="18c47-802">Invoke the F12 developer tools.</span></span>
* <span data-ttu-id="18c47-803">Přejděte na adresu `https://localhost:5001/Sample/HeaderWithFactory`.</span><span class="sxs-lookup"><span data-stu-id="18c47-803">Navigate to `https://localhost:5001/Sample/HeaderWithFactory`.</span></span>

<span data-ttu-id="18c47-804">Vývojářské nástroje F12 zobrazují následující hlavičky odpovědí přidané ukázkovým kódem:</span><span class="sxs-lookup"><span data-stu-id="18c47-804">The F12 developer tools display the following response headers added by the sample code:</span></span>

* <span data-ttu-id="18c47-805">**Autor:**`Joe Smith`</span><span class="sxs-lookup"><span data-stu-id="18c47-805">**author:** `Joe Smith`</span></span>
* <span data-ttu-id="18c47-806">**globaladdheader:**`Result filter added to MvcOptions.Filters`</span><span class="sxs-lookup"><span data-stu-id="18c47-806">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span></span>
* <span data-ttu-id="18c47-807">**interní:**`My header`</span><span class="sxs-lookup"><span data-stu-id="18c47-807">**internal:** `My header`</span></span>

<span data-ttu-id="18c47-808">Předchozí kód vytvoří hlavičku **interního:** `My header` Response.</span><span class="sxs-lookup"><span data-stu-id="18c47-808">The preceding code creates the **internal:** `My header` response header.</span></span>

### <a name="ifilterfactory-implemented-on-an-attribute"></a><span data-ttu-id="18c47-809">IFilterFactory implementované u atributu</span><span class="sxs-lookup"><span data-stu-id="18c47-809">IFilterFactory implemented on an attribute</span></span>

<!-- Review 
This section needs to be rewritten.
What's a non-named attribute?
-->

<span data-ttu-id="18c47-810">Filtry, které `IFilterFactory` implementují, jsou užitečné pro filtry, které:</span><span class="sxs-lookup"><span data-stu-id="18c47-810">Filters that implement `IFilterFactory` are useful for filters that:</span></span>

* <span data-ttu-id="18c47-811">Nevyžadovat předávání parametrů</span><span class="sxs-lookup"><span data-stu-id="18c47-811">Don't require passing parameters.</span></span>
* <span data-ttu-id="18c47-812">Musí mít závislosti konstruktoru, které musí vyplnit DI.</span><span class="sxs-lookup"><span data-stu-id="18c47-812">Have constructor dependencies that need to be filled by DI.</span></span>

<span data-ttu-id="18c47-813"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>implementuje <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span><span class="sxs-lookup"><span data-stu-id="18c47-813"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="18c47-814">`IFilterFactory`zpřístupňuje <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> metodu pro vytvoření <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span><span class="sxs-lookup"><span data-stu-id="18c47-814">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="18c47-815">`CreateInstance`Načte zadaný typ z kontejneru Services (DI).</span><span class="sxs-lookup"><span data-stu-id="18c47-815">`CreateInstance` loads the specified type from the services container (DI).</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/SampleActionFilterAttribute.cs?name=snippet_TypeFilterAttribute&highlight=1,3,7)]

<span data-ttu-id="18c47-816">Následující kód ukazuje tři přístupy k použití `[SampleActionFilter]`:</span><span class="sxs-lookup"><span data-stu-id="18c47-816">The following code shows three approaches to applying the `[SampleActionFilter]`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet&highlight=1)]

<span data-ttu-id="18c47-817">V předchozím kódu upravení metoda s `[SampleActionFilter]` jako upřednostňovaným přístupem k použití. `SampleActionFilter`</span><span class="sxs-lookup"><span data-stu-id="18c47-817">In the preceding code, decorating the method with `[SampleActionFilter]` is the preferred approach to applying the `SampleActionFilter`.</span></span>

## <a name="using-middleware-in-the-filter-pipeline"></a><span data-ttu-id="18c47-818">Použití middlewaru v kanálu filtru</span><span class="sxs-lookup"><span data-stu-id="18c47-818">Using middleware in the filter pipeline</span></span>

<span data-ttu-id="18c47-819">Filtry prostředků fungují jako [middleware](xref:fundamentals/middleware/index) v tom, že obklopují provádění všech položek, které jsou později v kanálu.</span><span class="sxs-lookup"><span data-stu-id="18c47-819">Resource filters work like [middleware](xref:fundamentals/middleware/index) in that they surround the execution of everything that comes later in the pipeline.</span></span> <span data-ttu-id="18c47-820">Filtry se ale liší od middlewaru v tom, že jsou součástí modulu ASP.NET Core Runtime, což znamená, že mají přístup k ASP.NET Core kontextu a konstrukcí.</span><span class="sxs-lookup"><span data-stu-id="18c47-820">But filters differ from middleware in that they're part of the ASP.NET Core runtime, which means that they have access to ASP.NET Core context and constructs.</span></span>

<span data-ttu-id="18c47-821">Chcete-li použít middleware jako filtr, vytvořte typ s `Configure` metodou, která určuje middleware pro vložení do kanálu filtru.</span><span class="sxs-lookup"><span data-stu-id="18c47-821">To use middleware as a filter, create a type with a `Configure` method that specifies the middleware to inject into the filter pipeline.</span></span> <span data-ttu-id="18c47-822">Následující příklad používá middleware Localization k vytvoření aktuální jazykové verze pro požadavek:</span><span class="sxs-lookup"><span data-stu-id="18c47-822">The following example uses the localization middleware to establish the current culture for a request:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LocalizationPipeline.cs?name=snippet_MiddlewareFilter&highlight=3,22)]

<span data-ttu-id="18c47-823"><xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> Pomocí spusťte middleware:</span><span class="sxs-lookup"><span data-stu-id="18c47-823">Use the <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> to run the middleware:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_MiddlewareFilter&highlight=2)]

<span data-ttu-id="18c47-824">Filtry middlewaru jsou spouštěny ve stejné fázi kanálu filtru jako filtry prostředků, před vazbami modelů a po zbytek kanálu.</span><span class="sxs-lookup"><span data-stu-id="18c47-824">Middleware filters run at the same stage of the filter pipeline as Resource filters, before model binding and after the rest of the pipeline.</span></span>

## <a name="next-actions"></a><span data-ttu-id="18c47-825">Další akce</span><span class="sxs-lookup"><span data-stu-id="18c47-825">Next actions</span></span>

* <span data-ttu-id="18c47-826">Viz [metody filtru pro Razor stránky](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="18c47-826">See [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>
* <span data-ttu-id="18c47-827">Pro experimentování s filtry, [stažení, otestování a úpravy ukázky GitHubu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample).</span><span class="sxs-lookup"><span data-stu-id="18c47-827">To experiment with filters, [download, test, and modify the GitHub sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample).</span></span>

::: moniker-end
