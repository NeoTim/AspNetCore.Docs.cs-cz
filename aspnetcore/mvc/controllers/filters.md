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
ms.openlocfilehash: 068b471c1f5fa5f0ca87dd7b028badf70f8c1b67
ms.sourcegitcommit: 77729ba225d5143c0e3954db005906f4a5c7da95
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2020
ms.locfileid: "85122162"
---
# <a name="filters-in-aspnet-core"></a><span data-ttu-id="7c5f7-103">Filtry v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7c5f7-103">Filters in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="7c5f7-104">[Kirka Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Dykstra](https://github.com/tdykstra/)a [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="7c5f7-104">By [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="7c5f7-105">*Filtry* v ASP.NET Core umožňují spuštění kódu před nebo po určitých fázích v kanálu zpracování požadavků.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-105">*Filters* in ASP.NET Core allow code to be run before or after specific stages in the request processing pipeline.</span></span>

<span data-ttu-id="7c5f7-106">Předdefinované filtry zpracovávají úlohy, jako například:</span><span class="sxs-lookup"><span data-stu-id="7c5f7-106">Built-in filters handle tasks such as:</span></span>

* <span data-ttu-id="7c5f7-107">Autorizace (zabránění přístupu k prostředkům, ke kterým uživatel nemá oprávnění).</span><span class="sxs-lookup"><span data-stu-id="7c5f7-107">Authorization (preventing access to resources a user isn't authorized for).</span></span>
* <span data-ttu-id="7c5f7-108">Ukládání odpovědí do mezipaměti (při krátkém okruhu kanálu požadavků, který vrátí odpověď uloženou v mezipaměti).</span><span class="sxs-lookup"><span data-stu-id="7c5f7-108">Response caching (short-circuiting the request pipeline to return a cached response).</span></span>

<span data-ttu-id="7c5f7-109">Vlastní filtry je možné vytvořit pro zpracování vzájemně se týkajících se otázek.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-109">Custom filters can be created to handle cross-cutting concerns.</span></span> <span data-ttu-id="7c5f7-110">Mezi obavy mezi průřezy patří zpracování chyb, ukládání do mezipaměti, konfigurace, autorizace a protokolování.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-110">Examples of cross-cutting concerns include error handling, caching, configuration, authorization, and logging.</span></span>  <span data-ttu-id="7c5f7-111">Filtry zabraňují duplikování kódu.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-111">Filters avoid duplicating code.</span></span> <span data-ttu-id="7c5f7-112">Například filtr výjimek zpracování chyb může konsolidovat zpracování chyb.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-112">For example, an error handling exception filter could consolidate error handling.</span></span>

<span data-ttu-id="7c5f7-113">Tento dokument se týká Razor stránek, řadičů rozhraní API a řadičů se zobrazeními.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-113">This document applies to Razor Pages, API controllers, and controllers with views.</span></span> <span data-ttu-id="7c5f7-114">Filtry nefungují přímo s [ Razor komponentami](xref:blazor/components/index).</span><span class="sxs-lookup"><span data-stu-id="7c5f7-114">Filters don't work directly with [Razor components](xref:blazor/components/index).</span></span> <span data-ttu-id="7c5f7-115">Filtr může nepřímo ovlivnit komponentu pouze v těchto případech:</span><span class="sxs-lookup"><span data-stu-id="7c5f7-115">A filter can only indirectly affect a component when:</span></span>

* <span data-ttu-id="7c5f7-116">Komponenta je vložena do stránky nebo zobrazení.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-116">The component is embedded in a page or view.</span></span>
* <span data-ttu-id="7c5f7-117">Stránka nebo kontroler nebo zobrazení používá filtr.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-117">The page or controller/view uses the filter.</span></span>

<span data-ttu-id="7c5f7-118">[Zobrazit nebo Stáhnout ukázku](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample) ([Jak stáhnout](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="7c5f7-118">[View or download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="how-filters-work"></a><span data-ttu-id="7c5f7-119">Jak fungují filtry</span><span class="sxs-lookup"><span data-stu-id="7c5f7-119">How filters work</span></span>

<span data-ttu-id="7c5f7-120">Filtry se spouštějí v *kanálu vyvolání akce ASP.NET Core*, někdy označované jako *kanál filtru*.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-120">Filters run within the *ASP.NET Core action invocation pipeline*, sometimes referred to as the *filter pipeline*.</span></span> <span data-ttu-id="7c5f7-121">Kanál filtru se spustí po ASP.NET Core vybere akci, která se má provést.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-121">The filter pipeline runs after ASP.NET Core selects the action to execute.</span></span>

![Požadavek se zpracovává prostřednictvím jiného middlewaru, middleware směrování, výběru akcí a kanálu vyvolání akce.](filters/_static/filter-pipeline-1.png)

### <a name="filter-types"></a><span data-ttu-id="7c5f7-124">Typy filtrů</span><span class="sxs-lookup"><span data-stu-id="7c5f7-124">Filter types</span></span>

<span data-ttu-id="7c5f7-125">Každý typ filtru se spustí v jiné fázi kanálu filtru:</span><span class="sxs-lookup"><span data-stu-id="7c5f7-125">Each filter type is executed at a different stage in the filter pipeline:</span></span>

* <span data-ttu-id="7c5f7-126">[Filtry autorizace](#authorization-filters) se spouštějí jako první a používají se k určení, jestli je uživatel autorizovaný pro požadavek.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-126">[Authorization filters](#authorization-filters) run first and are used to determine whether the user is authorized for the request.</span></span> <span data-ttu-id="7c5f7-127">Filtry autorizace pro krátkodobé vynechání kanálu, pokud žádost není autorizována.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-127">Authorization filters short-circuit the pipeline if the request is not authorized.</span></span>

* <span data-ttu-id="7c5f7-128">[Filtry prostředků](#resource-filters):</span><span class="sxs-lookup"><span data-stu-id="7c5f7-128">[Resource filters](#resource-filters):</span></span>

  * <span data-ttu-id="7c5f7-129">Spusťte po autorizaci.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-129">Run after authorization.</span></span>  
  * <span data-ttu-id="7c5f7-130"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*>spustí kód před zbytek kanálu filtru.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-130"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> runs code before the rest of the filter pipeline.</span></span> <span data-ttu-id="7c5f7-131">Například `OnResourceExecuting` spustí kód před vazbou modelu.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-131">For example, `OnResourceExecuting` runs code before model binding.</span></span>
  * <span data-ttu-id="7c5f7-132"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*>spustí kód po dokončení zbývající části kanálu.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-132"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> runs code after the rest of the pipeline has completed.</span></span>

* <span data-ttu-id="7c5f7-133">[Filtry akcí](#action-filters):</span><span class="sxs-lookup"><span data-stu-id="7c5f7-133">[Action filters](#action-filters):</span></span>

  * <span data-ttu-id="7c5f7-134">Spustit kód bezprostředně před a po volání metody Action.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-134">Run code immediately before and after an action method is called.</span></span>
  * <span data-ttu-id="7c5f7-135">Může změnit argumenty předané do akce.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-135">Can change the arguments passed into an action.</span></span>
  * <span data-ttu-id="7c5f7-136">Může změnit výsledek vrácený z akce.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-136">Can change the result returned from the action.</span></span>
  * <span data-ttu-id="7c5f7-137">Nejsou **na** stránkách podporovány Razor .</span><span class="sxs-lookup"><span data-stu-id="7c5f7-137">Are **not** supported in Razor Pages.</span></span>

* <span data-ttu-id="7c5f7-138">[Filtry výjimek](#exception-filters) aplikují globální zásady na neošetřené výjimky, ke kterým došlo před zápisem textu odpovědi do.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-138">[Exception filters](#exception-filters) apply global policies to unhandled exceptions that occur before the response body has been written to.</span></span>

* <span data-ttu-id="7c5f7-139">[Filtry výsledků](#result-filters) spouštějí kód bezprostředně před a po provedení výsledků akce.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-139">[Result filters](#result-filters) run code immediately before and after the execution of action results.</span></span> <span data-ttu-id="7c5f7-140">Spustí se pouze v případě, že metoda akce byla úspěšně provedena.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-140">They run only when the action method has executed successfully.</span></span> <span data-ttu-id="7c5f7-141">Jsou užitečné pro logiku, která musí obklopit zobrazení nebo formátovací modul.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-141">They are useful for logic that must surround view or formatter execution.</span></span>

<span data-ttu-id="7c5f7-142">Následující diagram znázorňuje způsob interakce typů filtrů v kanálu filtru.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-142">The following diagram shows how filter types interact in the filter pipeline.</span></span>

![Požadavek se zpracovává pomocí autorizačních filtrů, filtrů prostředků, vazeb modelů, filtrů akcí, provádění akcí a konverze výsledků akcí, filtrů výjimek, výsledných filtrů a provádění výsledků.](filters/_static/filter-pipeline-2.png)

## <a name="implementation"></a><span data-ttu-id="7c5f7-145">Implementace</span><span class="sxs-lookup"><span data-stu-id="7c5f7-145">Implementation</span></span>

<span data-ttu-id="7c5f7-146">Filtry podporují synchronní i asynchronní implementace prostřednictvím různých definic rozhraní.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-146">Filters support both synchronous and asynchronous implementations through different interface definitions.</span></span>

<span data-ttu-id="7c5f7-147">Synchronní filtry spouštějí kód před a po fázi zřetězení.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-147">Synchronous filters run code before and after their pipeline stage.</span></span> <span data-ttu-id="7c5f7-148">Například <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*> je volána před voláním metody Action.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-148">For example, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*> is called before the action method is called.</span></span> <span data-ttu-id="7c5f7-149"><xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*>je volána po návratu metody Action.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-149"><xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*> is called after the action method returns.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="7c5f7-150">Asynchronní filtry definují `On-Stage-ExecutionAsync` metodu.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-150">Asynchronous filters define an `On-Stage-ExecutionAsync` method.</span></span> <span data-ttu-id="7c5f7-151">Například <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*> :</span><span class="sxs-lookup"><span data-stu-id="7c5f7-151">For example, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*>:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/SampleAsyncActionFilter.cs?name=snippet)]

<span data-ttu-id="7c5f7-152">V předchozím kódu `SampleAsyncActionFilter` obsahuje znak <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> ( `next` ), který provádí metodu Action.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-152">In the preceding code, the `SampleAsyncActionFilter` has an <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`) that executes the action method.</span></span>

### <a name="multiple-filter-stages"></a><span data-ttu-id="7c5f7-153">Několik fází filtru</span><span class="sxs-lookup"><span data-stu-id="7c5f7-153">Multiple filter stages</span></span>

<span data-ttu-id="7c5f7-154">Rozhraní pro více fází filtru lze implementovat v rámci jedné třídy.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-154">Interfaces for multiple filter stages can be implemented in a single class.</span></span> <span data-ttu-id="7c5f7-155">Například <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> Třída implementuje:</span><span class="sxs-lookup"><span data-stu-id="7c5f7-155">For example, the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> class implements:</span></span>

* <span data-ttu-id="7c5f7-156">Synchronní: <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> a<xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter></span><span class="sxs-lookup"><span data-stu-id="7c5f7-156">Synchronous: <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> and  <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter></span></span>
* <span data-ttu-id="7c5f7-157">Asynchronní: <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> a<xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="7c5f7-157">Asynchronous: <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
* <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>

<span data-ttu-id="7c5f7-158">Implementujte **buď** synchronní, nebo asynchronní verzi rozhraní filtru, **nikoli** obojí.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-158">Implement **either** the synchronous or the async version of a filter interface, **not** both.</span></span> <span data-ttu-id="7c5f7-159">Modul runtime nejprve kontroluje, zda filtr implementuje asynchronní rozhraní a v takovém případě jej zavolá.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-159">The runtime checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="7c5f7-160">V takovém případě volá metody synchronního rozhraní.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-160">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="7c5f7-161">Pokud jsou asynchronní i synchronní rozhraní implementovány v jedné třídě, je volána pouze asynchronní metoda.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-161">If both asynchronous and synchronous interfaces are implemented in one class, only the async method is called.</span></span> <span data-ttu-id="7c5f7-162">Při použití abstraktních tříd <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> , jako je přepsání pouze synchronních metod nebo asynchronní metody pro každý typ filtru.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-162">When using abstract classes like <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>, override only the synchronous methods or the asynchronous method for each filter type.</span></span>

### <a name="built-in-filter-attributes"></a><span data-ttu-id="7c5f7-163">Předdefinované atributy filtru</span><span class="sxs-lookup"><span data-stu-id="7c5f7-163">Built-in filter attributes</span></span>

<span data-ttu-id="7c5f7-164">ASP.NET Core obsahuje integrované filtry založené na atributech, které mohou být roztříděné a přizpůsobené.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-164">ASP.NET Core includes built-in attribute-based filters that can be subclassed and customized.</span></span> <span data-ttu-id="7c5f7-165">Například následující filtr výsledků přidá hlavičku do odpovědi:</span><span class="sxs-lookup"><span data-stu-id="7c5f7-165">For example, the following result filter adds a header to the response:</span></span>

<a name="add-header-attribute"></a>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/AddHeaderAttribute.cs?name=snippet)]

<span data-ttu-id="7c5f7-166">Atributy umožňují filtrům přijímat argumenty, jak je znázorněno v předchozím příkladu.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-166">Attributes allow filters to accept arguments, as shown in the preceding example.</span></span> <span data-ttu-id="7c5f7-167">Použijte `AddHeaderAttribute` pro metodu Controller nebo Action a zadejte název a hodnotu HLAVIČKY http:</span><span class="sxs-lookup"><span data-stu-id="7c5f7-167">Apply the `AddHeaderAttribute` to a controller or action method and specify the name and value of the HTTP header:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

<span data-ttu-id="7c5f7-168">K prohlédnutí hlaviček použijte nástroj, jako je například nástroj pro [vývojáře v prohlížeči](https://developer.mozilla.org/docs/Learn/Common_questions/What_are_browser_developer_tools) .</span><span class="sxs-lookup"><span data-stu-id="7c5f7-168">Use a tool such as the [browser developer tools](https://developer.mozilla.org/docs/Learn/Common_questions/What_are_browser_developer_tools) to examine the headers.</span></span> <span data-ttu-id="7c5f7-169">V části **hlavičky odpovědi** `author: Rick Anderson` se zobrazí.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-169">Under **Response Headers**, `author: Rick Anderson` is displayed.</span></span>

<span data-ttu-id="7c5f7-170">Následující kód implementuje `ActionFilterAttribute` :</span><span class="sxs-lookup"><span data-stu-id="7c5f7-170">The following code implements an `ActionFilterAttribute` that:</span></span>

* <span data-ttu-id="7c5f7-171">Přečte název a název z konfiguračního systému.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-171">Reads the title and name from the configuration system.</span></span> <span data-ttu-id="7c5f7-172">Na rozdíl od předchozí ukázky, následující kód nevyžaduje přidání parametrů filtru do kódu.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-172">Unlike the previous sample, the following code doesn't require filter parameters to be added to the code.</span></span>
* <span data-ttu-id="7c5f7-173">Přidá název a název do hlavičky odpovědi.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-173">Adds the title and name to the response header.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MyActionFilterAttribute.cs?name=snippet)]

<span data-ttu-id="7c5f7-174">Možnosti konfigurace se poskytují z [konfiguračního systému](xref:fundamentals/configuration/index) pomocí [vzoru možností](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="7c5f7-174">The configuration options are provided from the [configuration system](xref:fundamentals/configuration/index) using the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="7c5f7-175">Například z *appsettings.js* souboru:</span><span class="sxs-lookup"><span data-stu-id="7c5f7-175">For example, from the *appsettings.json* file:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/appsettings.json)]

<span data-ttu-id="7c5f7-176">V `StartUp.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="7c5f7-176">In the `StartUp.ConfigureServices`:</span></span>

* <span data-ttu-id="7c5f7-177">`PositionOptions`Třída je přidána do kontejneru služby s `"Position"` oblastí konfigurace.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-177">The `PositionOptions` class is added to the service container with the `"Position"` configuration area.</span></span>
* <span data-ttu-id="7c5f7-178">`MyActionFilterAttribute`Přidá se do kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-178">The `MyActionFilterAttribute` is added to the service container.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupAF.cs?name=snippet)]

<span data-ttu-id="7c5f7-179">Následující kód ukazuje `PositionOptions` třídu:</span><span class="sxs-lookup"><span data-stu-id="7c5f7-179">The following code shows the `PositionOptions` class:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Helper/PositionOptions.cs?name=snippet)]

<span data-ttu-id="7c5f7-180">Následující kód platí `MyActionFilterAttribute` pro `Index2` metodu:</span><span class="sxs-lookup"><span data-stu-id="7c5f7-180">The following code applies the `MyActionFilterAttribute` to the `Index2` method:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet2&highlight=9)]

<span data-ttu-id="7c5f7-181">V části **hlavičky odpovědi**, `author: Rick Anderson` a `Editor: Joe Smith` se zobrazí při `Sample/Index2` volání koncového bodu.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-181">Under **Response Headers**, `author: Rick Anderson`, and `Editor: Joe Smith` is displayed when the `Sample/Index2` endpoint is called.</span></span>

<span data-ttu-id="7c5f7-182">Následující kód platí `MyActionFilterAttribute` pro a na `AddHeaderAttribute` Razor stránce:</span><span class="sxs-lookup"><span data-stu-id="7c5f7-182">The following code applies the `MyActionFilterAttribute` and the `AddHeaderAttribute` to the Razor Page:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Pages/Movies/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="7c5f7-183">Filtry nelze použít na Razor metody obslužné rutiny stránky.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-183">Filters cannot be applied to Razor Page handler methods.</span></span> <span data-ttu-id="7c5f7-184">Lze je použít buď na Razor model stránky, nebo globálně.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-184">They can be applied either to the Razor Page model or globally.</span></span>

<span data-ttu-id="7c5f7-185">Některé z rozhraní filtru mají odpovídající atributy, které lze použít jako základní třídy pro vlastní implementace.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-185">Several of the filter interfaces have corresponding attributes that can be used as base classes for custom implementations.</span></span>

<span data-ttu-id="7c5f7-186">Atributy filtru:</span><span class="sxs-lookup"><span data-stu-id="7c5f7-186">Filter attributes:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ExceptionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>

## <a name="filter-scopes-and-order-of-execution"></a><span data-ttu-id="7c5f7-187">Filtrovat obory a pořadí provádění</span><span class="sxs-lookup"><span data-stu-id="7c5f7-187">Filter scopes and order of execution</span></span>

<span data-ttu-id="7c5f7-188">Filtr lze přidat do kanálu v jednom ze tří *oborů*:</span><span class="sxs-lookup"><span data-stu-id="7c5f7-188">A filter can be added to the pipeline at one of three *scopes*:</span></span>

* <span data-ttu-id="7c5f7-189">Použití atributu u akce kontroleru.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-189">Using an attribute on a controller action.</span></span> <span data-ttu-id="7c5f7-190">Atributy filtru nelze použít na Razor stránky metod obslužných rutin.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-190">Filter attributes cannot be applied to Razor Pages handler methods.</span></span>
* <span data-ttu-id="7c5f7-191">Použití atributu na řadiči nebo na Razor stránce.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-191">Using an attribute on a controller or Razor Page.</span></span>
* <span data-ttu-id="7c5f7-192">Globálně pro všechny řadiče, akce a Razor stránky, jak je znázorněno v následujícím kódu:</span><span class="sxs-lookup"><span data-stu-id="7c5f7-192">Globally for all controllers, actions, and Razor Pages as shown in the following code:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder.cs?name=snippet)]

### <a name="default-order-of-execution"></a><span data-ttu-id="7c5f7-193">Výchozí pořadí provádění</span><span class="sxs-lookup"><span data-stu-id="7c5f7-193">Default order of execution</span></span>

<span data-ttu-id="7c5f7-194">Pokud existuje více filtrů pro určitou fázi kanálu, rozsah Určuje výchozí pořadí provádění filtru.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-194">When there are multiple filters for a particular stage of the pipeline, scope determines the default order of filter execution.</span></span>  <span data-ttu-id="7c5f7-195">Globální filtry obklopují filtry tříd, které jsou ve filtru metod Surround.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-195">Global filters surround class filters, which in turn surround method filters.</span></span>

<span data-ttu-id="7c5f7-196">V důsledku vnořování filtru je *po* kódu spuštěn v obráceném pořadí *před* kódem.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-196">As a result of filter nesting, the *after* code of filters runs in the reverse order of the *before* code.</span></span> <span data-ttu-id="7c5f7-197">Pořadí filtru:</span><span class="sxs-lookup"><span data-stu-id="7c5f7-197">The filter sequence:</span></span>

* <span data-ttu-id="7c5f7-198">*Před* kódem globálních filtrů.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-198">The *before* code of global filters.</span></span>
  * <span data-ttu-id="7c5f7-199">*Před* kódem kontroleru a Razor filtru stránky.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-199">The *before* code of controller and Razor Page filters.</span></span>
    * <span data-ttu-id="7c5f7-200">Kód *před* filtry metody Action.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-200">The *before* code of action method filters.</span></span>
    * <span data-ttu-id="7c5f7-201">*Po* kódu filtry metody Action.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-201">The *after* code of action method filters.</span></span>
  * <span data-ttu-id="7c5f7-202">*Po* kódu filtru řadiče a Razor stránky.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-202">The *after* code of controller and Razor Page filters.</span></span>
* <span data-ttu-id="7c5f7-203">*Po* kódu globálních filtrů.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-203">The *after* code of global filters.</span></span>
  
<span data-ttu-id="7c5f7-204">Následující příklad ilustruje pořadí, ve kterém jsou metody filtru volány pro filtry synchronních akcí.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-204">The following example that illustrates the order in which filter methods are called for synchronous action filters.</span></span>

| <span data-ttu-id="7c5f7-205">Sequence</span><span class="sxs-lookup"><span data-stu-id="7c5f7-205">Sequence</span></span> | <span data-ttu-id="7c5f7-206">Rozsah filtru</span><span class="sxs-lookup"><span data-stu-id="7c5f7-206">Filter scope</span></span> | <span data-ttu-id="7c5f7-207">Filter – metoda</span><span class="sxs-lookup"><span data-stu-id="7c5f7-207">Filter method</span></span> |
|:--------:|:------------:|:-------------:|
| <span data-ttu-id="7c5f7-208">1</span><span class="sxs-lookup"><span data-stu-id="7c5f7-208">1</span></span> | <span data-ttu-id="7c5f7-209">Globální</span><span class="sxs-lookup"><span data-stu-id="7c5f7-209">Global</span></span> | `OnActionExecuting` |
| <span data-ttu-id="7c5f7-210">2</span><span class="sxs-lookup"><span data-stu-id="7c5f7-210">2</span></span> | <span data-ttu-id="7c5f7-211">Kontroler nebo Razor Stránka</span><span class="sxs-lookup"><span data-stu-id="7c5f7-211">Controller or Razor Page</span></span>| `OnActionExecuting` |
| <span data-ttu-id="7c5f7-212">3</span><span class="sxs-lookup"><span data-stu-id="7c5f7-212">3</span></span> | <span data-ttu-id="7c5f7-213">Metoda</span><span class="sxs-lookup"><span data-stu-id="7c5f7-213">Method</span></span> | `OnActionExecuting` |
| <span data-ttu-id="7c5f7-214">4</span><span class="sxs-lookup"><span data-stu-id="7c5f7-214">4</span></span> | <span data-ttu-id="7c5f7-215">Metoda</span><span class="sxs-lookup"><span data-stu-id="7c5f7-215">Method</span></span> | `OnActionExecuted` |
| <span data-ttu-id="7c5f7-216">5</span><span class="sxs-lookup"><span data-stu-id="7c5f7-216">5</span></span> | <span data-ttu-id="7c5f7-217">Kontroler nebo Razor Stránka</span><span class="sxs-lookup"><span data-stu-id="7c5f7-217">Controller or Razor Page</span></span> | `OnActionExecuted` |
| <span data-ttu-id="7c5f7-218">6</span><span class="sxs-lookup"><span data-stu-id="7c5f7-218">6</span></span> | <span data-ttu-id="7c5f7-219">Globální</span><span class="sxs-lookup"><span data-stu-id="7c5f7-219">Global</span></span> | `OnActionExecuted` |

### <a name="controller-level-filters"></a><span data-ttu-id="7c5f7-220">Filtry na úrovni řadiče</span><span class="sxs-lookup"><span data-stu-id="7c5f7-220">Controller level filters</span></span>

<span data-ttu-id="7c5f7-221">Každý kontroler, který dědí ze <xref:Microsoft.AspNetCore.Mvc.Controller> základní třídy, zahrnuje metody [Controller. OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*), [Controller. OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*)a [Controller. OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*) 
 `OnActionExecuted` .</span><span class="sxs-lookup"><span data-stu-id="7c5f7-221">Every controller that inherits from the <xref:Microsoft.AspNetCore.Mvc.Controller> base class includes [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*),  [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*), and [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted` methods.</span></span> <span data-ttu-id="7c5f7-222">Tyto metody:</span><span class="sxs-lookup"><span data-stu-id="7c5f7-222">These methods:</span></span>

* <span data-ttu-id="7c5f7-223">Zabalte filtry, které se spouštějí pro danou akci.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-223">Wrap the filters that run for a given action.</span></span>
* <span data-ttu-id="7c5f7-224">`OnActionExecuting`je volána před jakýmkoli filtrem akce.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-224">`OnActionExecuting` is called before any of the action's filters.</span></span>
* <span data-ttu-id="7c5f7-225">`OnActionExecuted`se volá po všech filtrech akcí.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-225">`OnActionExecuted` is called after all of the action filters.</span></span>
* <span data-ttu-id="7c5f7-226">`OnActionExecutionAsync`je volána před jakýmkoli filtrem akce.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-226">`OnActionExecutionAsync` is called before any of the action's filters.</span></span> <span data-ttu-id="7c5f7-227">Kód v filtru po `next` spuštění po metodě Action.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-227">Code in the filter after `next` runs after the action method.</span></span>

<span data-ttu-id="7c5f7-228">Například v ukázce ke stažení `MySampleActionFilter` se použije globálně při spuštění.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-228">For example, in the download sample, `MySampleActionFilter` is applied globally in startup.</span></span>

<span data-ttu-id="7c5f7-229">`TestController`:</span><span class="sxs-lookup"><span data-stu-id="7c5f7-229">The `TestController`:</span></span>

* <span data-ttu-id="7c5f7-230">Aplikuje `SampleActionFilterAttribute` ( `[SampleActionFilter]` ) na `FilterTest2` akci.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-230">Applies the `SampleActionFilterAttribute` (`[SampleActionFilter]`) to the `FilterTest2` action.</span></span>
* <span data-ttu-id="7c5f7-231">Přepisuje `OnActionExecuting` a `OnActionExecuted` .</span><span class="sxs-lookup"><span data-stu-id="7c5f7-231">Overrides `OnActionExecuting` and `OnActionExecuted`.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/TestController.cs?name=snippet)]

<!-- test via  webBuilder.UseStartup<Startup>(); -->

<span data-ttu-id="7c5f7-232">Navigace pro `https://localhost:5001/Test2/FilterTest2` spustí následující kód:</span><span class="sxs-lookup"><span data-stu-id="7c5f7-232">Navigating to `https://localhost:5001/Test2/FilterTest2` runs the following code:</span></span>

* `TestController.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `SampleActionFilterAttribute.OnActionExecuting`
      * `TestController.FilterTest2`
    * `SampleActionFilterAttribute.OnActionExecuted`
  * `MySampleActionFilter.OnActionExecuted`
* `TestController.OnActionExecuted`

<span data-ttu-id="7c5f7-233">Filtry na úrovni řadiče nastaví vlastnost [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) na `int.MinValue` .</span><span class="sxs-lookup"><span data-stu-id="7c5f7-233">Controller level filters set the [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) property to `int.MinValue`.</span></span> <span data-ttu-id="7c5f7-234">Filtry na úrovni řadiče **nelze nastavit** tak, aby běžely po filtrech použitých na metody.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-234">Controller level filters can **not** be set to run after filters applied to methods.</span></span> <span data-ttu-id="7c5f7-235">Pořadí je vysvětleno v další části.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-235">Order is explained in the next section.</span></span>

<span data-ttu-id="7c5f7-236">RazorInformace o stránkách naleznete v tématu [implementace Razor filtrů stránek pomocí přepsání metod filtru](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span><span class="sxs-lookup"><span data-stu-id="7c5f7-236">For Razor Pages, see [Implement Razor Page filters by overriding filter methods](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span></span>

### <a name="overriding-the-default-order"></a><span data-ttu-id="7c5f7-237">Přepsání výchozího pořadí</span><span class="sxs-lookup"><span data-stu-id="7c5f7-237">Overriding the default order</span></span>

<span data-ttu-id="7c5f7-238">Výchozí sekvenci spuštění lze přepsat implementací <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter> .</span><span class="sxs-lookup"><span data-stu-id="7c5f7-238">The default sequence of execution can be overridden by implementing <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span></span> <span data-ttu-id="7c5f7-239">`IOrderedFilter`zpřístupňuje <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> vlastnost, která má přednost před rozsahem, aby určila pořadí provádění.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-239">`IOrderedFilter` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> property that takes precedence over scope to determine the order of execution.</span></span> <span data-ttu-id="7c5f7-240">Filtr s nižší `Order` hodnotou:</span><span class="sxs-lookup"><span data-stu-id="7c5f7-240">A filter with a lower `Order` value:</span></span>

* <span data-ttu-id="7c5f7-241">Spustí *před* kódem filtr s vyšší hodnotou `Order` .</span><span class="sxs-lookup"><span data-stu-id="7c5f7-241">Runs the *before* code before that of a filter with a higher value of `Order`.</span></span>
* <span data-ttu-id="7c5f7-242">Spustí *za* kódem za filtr s vyšší `Order` hodnotou.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-242">Runs the *after* code after that of a filter with a higher `Order` value.</span></span>

<span data-ttu-id="7c5f7-243">`Order`Vlastnost je nastavena s parametrem konstruktoru:</span><span class="sxs-lookup"><span data-stu-id="7c5f7-243">The `Order` property is set with a constructor parameter:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test3Controller.cs?name=snippet)]

<span data-ttu-id="7c5f7-244">Vezměte v úvahu dva filtry akcí v následujícím kontroleru:</span><span class="sxs-lookup"><span data-stu-id="7c5f7-244">Consider the two action filters in the following controller:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test2Controller.cs?name=snippet)]

<span data-ttu-id="7c5f7-245">Globální filtr je přidaný v `StartUp.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="7c5f7-245">A global filter is added in `StartUp.ConfigureServices`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder.cs?name=snippet)]

<span data-ttu-id="7c5f7-246">Tři filtry jsou spouštěny v následujícím pořadí:</span><span class="sxs-lookup"><span data-stu-id="7c5f7-246">The 3 filters run in the following order:</span></span>

* `Test2Controller.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `MyAction2FilterAttribute.OnActionExecuting`
      * `Test2Controller.FilterTest2`
    * `MySampleActionFilter.OnActionExecuted`
  * `MyAction2FilterAttribute.OnResultExecuting`
* `Test2Controller.OnActionExecuted`

<span data-ttu-id="7c5f7-247">`Order`Vlastnost Přepisuje obor při určování pořadí, ve kterém jsou filtry spouštěny.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-247">The `Order` property overrides scope when determining the order in which filters run.</span></span> <span data-ttu-id="7c5f7-248">Filtry jsou seřazené podle pořadí, pak se k přerušení vztahů používá obor.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-248">Filters are sorted first by order, then scope is used to break ties.</span></span> <span data-ttu-id="7c5f7-249">Všechny předdefinované filtry implementují `IOrderedFilter` a nastaví výchozí `Order` hodnotu na 0.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-249">All of the built-in filters implement `IOrderedFilter` and set the default `Order` value to 0.</span></span> <span data-ttu-id="7c5f7-250">Jak už bylo uvedeno výše, filtry na úrovni řadiče nastaví vlastnost [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) na `int.MinValue` pro předdefinované filtry. Scope určí pořadí, pokud `Order` není nastavená na nenulovou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-250">As mentioned previously, controller level filters set the [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) property to `int.MinValue` For built-in filters, scope determines order unless `Order` is set to a non-zero value.</span></span>

<span data-ttu-id="7c5f7-251">V předchozím kódu `MySampleActionFilter` má globální rozsah, takže se spustí před `MyAction2FilterAttribute` , který má obor kontroleru.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-251">In the preceding code, `MySampleActionFilter` has global scope so it runs before `MyAction2FilterAttribute`, which has controller scope.</span></span> <span data-ttu-id="7c5f7-252">Aby bylo možné `MyAction2FilterAttribute` Spustit jako první, nastavte pořadí na `int.MinValue` :</span><span class="sxs-lookup"><span data-stu-id="7c5f7-252">To make `MyAction2FilterAttribute` run first, set the order to `int.MinValue`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test2Controller.cs?name=snippet2)]

<span data-ttu-id="7c5f7-253">Aby se globální filtr `MySampleActionFilter` spouštěl jako první, nastavte `Order` na `int.MinValue` :</span><span class="sxs-lookup"><span data-stu-id="7c5f7-253">To make the global filter `MySampleActionFilter` run first, set `Order` to `int.MinValue`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder2.cs?name=snippet&highlight=6)]

## <a name="cancellation-and-short-circuiting"></a><span data-ttu-id="7c5f7-254">Zrušení a zkrácení okruhů</span><span class="sxs-lookup"><span data-stu-id="7c5f7-254">Cancellation and short-circuiting</span></span>

<span data-ttu-id="7c5f7-255">Kanál filtru může být v krátkém okruhu nastavením <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> vlastnosti pro <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> parametr poskytnutý metodě Filter.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-255">The filter pipeline can be short-circuited by setting the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> property on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> parameter provided to the filter method.</span></span> <span data-ttu-id="7c5f7-256">Například následující filtr prostředků zabrání ve spuštění zbývajícího kanálu:</span><span class="sxs-lookup"><span data-stu-id="7c5f7-256">For instance, the following Resource filter prevents the rest of the pipeline from executing:</span></span>

<a name="short-circuiting-resource-filter"></a>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ShortCircuitingResourceFilterAttribute.cs?name=snippet)]

<span data-ttu-id="7c5f7-257">V následujícím kódu, `ShortCircuitingResourceFilter` a jako `AddHeader` cíl filtru i cílovou `SomeResource` metodu Action.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-257">In the following code, both the `ShortCircuitingResourceFilter` and the `AddHeader` filter target the `SomeResource` action method.</span></span> <span data-ttu-id="7c5f7-258">`ShortCircuitingResourceFilter`:</span><span class="sxs-lookup"><span data-stu-id="7c5f7-258">The `ShortCircuitingResourceFilter`:</span></span>

* <span data-ttu-id="7c5f7-259">Nejprve se spustí, protože se jedná o filtr prostředků a `AddHeader` je filtr akcí.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-259">Runs first, because it's a Resource Filter and `AddHeader` is an Action Filter.</span></span>
* <span data-ttu-id="7c5f7-260">Krátké okruhy zbývajícího kanálu.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-260">Short-circuits the rest of the pipeline.</span></span>

<span data-ttu-id="7c5f7-261">Proto `AddHeader` Filtr pro akci nikdy neběží `SomeResource` .</span><span class="sxs-lookup"><span data-stu-id="7c5f7-261">Therefore the `AddHeader` filter never runs for the `SomeResource` action.</span></span> <span data-ttu-id="7c5f7-262">Toto chování by bylo stejné, pokud byly oba filtry aplikovány na úrovni metody akce, a to za předpokladu, že byla `ShortCircuitingResourceFilter` spuštěna jako první.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-262">This behavior would be the same if both filters were applied at the action method level, provided the `ShortCircuitingResourceFilter` ran first.</span></span> <span data-ttu-id="7c5f7-263">`ShortCircuitingResourceFilter`Nejprve se spustí z důvodu jeho typu filtru nebo explicitního použití `Order` Vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-263">The `ShortCircuitingResourceFilter` runs first because of its filter type, or by explicit use of `Order` property.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

## <a name="dependency-injection"></a><span data-ttu-id="7c5f7-264">Injektáž závislosti</span><span class="sxs-lookup"><span data-stu-id="7c5f7-264">Dependency injection</span></span>

<span data-ttu-id="7c5f7-265">Filtry lze přidat podle typu nebo podle instance.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-265">Filters can be added by type or by instance.</span></span> <span data-ttu-id="7c5f7-266">Pokud je přidána instance, bude tato instance použita pro každý požadavek.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-266">If an instance is added, that instance is used for every request.</span></span> <span data-ttu-id="7c5f7-267">Pokud je přidán typ, je aktivován typ.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-267">If a type is added, it's type-activated.</span></span> <span data-ttu-id="7c5f7-268">Filtr aktivovaný typu znamená:</span><span class="sxs-lookup"><span data-stu-id="7c5f7-268">A type-activated filter means:</span></span>

* <span data-ttu-id="7c5f7-269">Instance se vytvoří pro každý požadavek.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-269">An instance is created for each request.</span></span>
* <span data-ttu-id="7c5f7-270">Jakékoli závislosti konstruktoru jsou vyplněny pomocí [Injektáže závislosti](xref:fundamentals/dependency-injection) (di).</span><span class="sxs-lookup"><span data-stu-id="7c5f7-270">Any constructor dependencies are populated by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span>

<span data-ttu-id="7c5f7-271">Filtry, které jsou implementovány jako atributy a přidány přímo do tříd kontroleru nebo metody akcí, nemohou mít závislosti konstruktoru poskytované [vkládáním závislostí](xref:fundamentals/dependency-injection) (di).</span><span class="sxs-lookup"><span data-stu-id="7c5f7-271">Filters that are implemented as attributes and added directly to controller classes or action methods cannot have constructor dependencies provided by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span> <span data-ttu-id="7c5f7-272">Pomocí DI nelze poskytnout závislosti konstruktoru:</span><span class="sxs-lookup"><span data-stu-id="7c5f7-272">Constructor dependencies cannot be provided by DI because:</span></span>

* <span data-ttu-id="7c5f7-273">V atributech musí být zadány parametry konstruktoru, kde jsou aplikovány.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-273">Attributes must have their constructor parameters supplied where they're applied.</span></span> 
* <span data-ttu-id="7c5f7-274">Toto je omezení způsobu fungování atributů.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-274">This is a limitation of how attributes work.</span></span>

<span data-ttu-id="7c5f7-275">Následující filtry podporují závislosti konstruktoru poskytované od DI:</span><span class="sxs-lookup"><span data-stu-id="7c5f7-275">The following filters support constructor dependencies provided from DI:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>
* <span data-ttu-id="7c5f7-276"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>implementováno u atributu.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-276"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implemented on the attribute.</span></span>

<span data-ttu-id="7c5f7-277">Předchozí filtry lze použít pro metodu kontroleru nebo akce:</span><span class="sxs-lookup"><span data-stu-id="7c5f7-277">The preceding filters can be applied to a controller or action method:</span></span>

<span data-ttu-id="7c5f7-278">Protokolovací nástroje jsou k dispozici z DI.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-278">Loggers are available from DI.</span></span> <span data-ttu-id="7c5f7-279">Ale nevytvářejte a používejte filtry čistě pro účely protokolování.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-279">However, avoid creating and using filters purely for logging purposes.</span></span> <span data-ttu-id="7c5f7-280">[Integrované protokolování rozhraní](xref:fundamentals/logging/index) obvykle zajišťuje, co je potřeba pro protokolování.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-280">The [built-in framework logging](xref:fundamentals/logging/index) typically provides what's needed for logging.</span></span> <span data-ttu-id="7c5f7-281">Protokolování přidáno do filtrů:</span><span class="sxs-lookup"><span data-stu-id="7c5f7-281">Logging added to filters:</span></span>

* <span data-ttu-id="7c5f7-282">Měl by se soustředit na obavy a chování obchodních domén, které jsou specifické pro filtr.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-282">Should focus on business domain concerns or behavior specific to the filter.</span></span>
* <span data-ttu-id="7c5f7-283">Neměli **byste** protokolovat akce nebo jiné události rozhraní.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-283">Should **not** log actions or other framework events.</span></span> <span data-ttu-id="7c5f7-284">Vestavěné filtry protokolují akce a události rozhraní.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-284">The built-in filters log actions and framework events.</span></span>

### <a name="servicefilterattribute"></a><span data-ttu-id="7c5f7-285">ServiceFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="7c5f7-285">ServiceFilterAttribute</span></span>

<span data-ttu-id="7c5f7-286">Typy implementace filtru služby jsou zaregistrované v `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="7c5f7-286">Service filter implementation types are registered in `ConfigureServices`.</span></span> <span data-ttu-id="7c5f7-287"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>Načte instanci filtru z di.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-287">A <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> retrieves an instance of the filter from DI.</span></span>

<span data-ttu-id="7c5f7-288">Následující kód ukazuje `AddHeaderResultServiceFilter` :</span><span class="sxs-lookup"><span data-stu-id="7c5f7-288">The following code shows the `AddHeaderResultServiceFilter`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="7c5f7-289">V následujícím kódu `AddHeaderResultServiceFilter` je přidán do kontejneru di:</span><span class="sxs-lookup"><span data-stu-id="7c5f7-289">In the following code, `AddHeaderResultServiceFilter` is added to the DI container:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Startup.cs?name=snippet&highlight=4)]

<span data-ttu-id="7c5f7-290">V následujícím kódu `ServiceFilter` atribut načte instanci `AddHeaderResultServiceFilter` filtru z di:</span><span class="sxs-lookup"><span data-stu-id="7c5f7-290">In the following code, the `ServiceFilter` attribute retrieves an instance of the `AddHeaderResultServiceFilter` filter from DI:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_ServiceFilter&highlight=1)]

<span data-ttu-id="7c5f7-291">Při použití `ServiceFilterAttribute` nastavte [ServiceFilterAttribute. IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span><span class="sxs-lookup"><span data-stu-id="7c5f7-291">When using `ServiceFilterAttribute`, setting [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span></span>

* <span data-ttu-id="7c5f7-292">Poskytuje nápovědu, že se instance filtru *dá* znovu použít mimo rozsah požadavku, který byl vytvořen v rámci.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-292">Provides a hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="7c5f7-293">Modul runtime ASP.NET Core nezaručuje:</span><span class="sxs-lookup"><span data-stu-id="7c5f7-293">The ASP.NET Core runtime doesn't guarantee:</span></span>

  * <span data-ttu-id="7c5f7-294">Vytvoří se jedna instance filtru.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-294">That a single instance of the filter will be created.</span></span>
  * <span data-ttu-id="7c5f7-295">Filtr nebude znovu vyžádán z kontejneru DI v pozdějším bodě.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-295">The filter will not be re-requested from the DI container at some later point.</span></span>

* <span data-ttu-id="7c5f7-296">Neměl by se používat s filtrem, který závisí na službách s jinou životností než singleton.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-296">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

 <span data-ttu-id="7c5f7-297"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>implementuje <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> .</span><span class="sxs-lookup"><span data-stu-id="7c5f7-297"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="7c5f7-298">`IFilterFactory`zpřístupňuje <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> metodu pro vytvoření <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-298">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="7c5f7-299">`CreateInstance`Načte zadaný typ z DI.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-299">`CreateInstance` loads the specified type from DI.</span></span>

### <a name="typefilterattribute"></a><span data-ttu-id="7c5f7-300">TypeFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="7c5f7-300">TypeFilterAttribute</span></span>

<span data-ttu-id="7c5f7-301"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>je podobný <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> , ale jeho typ není vyřešen přímo z kontejneru di.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-301"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> is similar to <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, but its type isn't resolved directly from the DI container.</span></span> <span data-ttu-id="7c5f7-302">Vytvoří instanci typu pomocí <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName> .</span><span class="sxs-lookup"><span data-stu-id="7c5f7-302">It instantiates the type by using <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span></span>

<span data-ttu-id="7c5f7-303">Protože `TypeFilterAttribute` typy nejsou vyřešeny přímo z kontejneru di:</span><span class="sxs-lookup"><span data-stu-id="7c5f7-303">Because `TypeFilterAttribute` types aren't resolved directly from the DI container:</span></span>

* <span data-ttu-id="7c5f7-304">Typy, na které se odkazuje pomocí, `TypeFilterAttribute` není nutné registrovat v kontejneru di.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-304">Types that are referenced using the `TypeFilterAttribute` don't need to be registered with the DI container.</span></span>  <span data-ttu-id="7c5f7-305">Mají své závislosti splněné kontejnerem DI.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-305">They do have their dependencies fulfilled by the DI container.</span></span>
* <span data-ttu-id="7c5f7-306">`TypeFilterAttribute`může volitelně přijmout argumenty konstruktoru pro typ.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-306">`TypeFilterAttribute` can optionally accept constructor arguments for the type.</span></span>

<span data-ttu-id="7c5f7-307">Při použití `TypeFilterAttribute` nastavte [TypeFilterAttribute. IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span><span class="sxs-lookup"><span data-stu-id="7c5f7-307">When using `TypeFilterAttribute`, setting [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span></span>
* <span data-ttu-id="7c5f7-308">Poskytuje nápovědu, že se instance filtru *dá* znovu použít mimo rozsah požadavku, který byl vytvořen v rámci.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-308">Provides hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="7c5f7-309">Modul runtime ASP.NET Core neposkytuje žádné záruky, že se vytvoří jediná instance filtru.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-309">The ASP.NET Core runtime provides no guarantees that a single instance of the filter will be created.</span></span>

* <span data-ttu-id="7c5f7-310">Neměl by se používat s filtrem, který závisí na službách s jinou životností než singleton.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-310">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

<span data-ttu-id="7c5f7-311">Následující příklad ukazuje, jak předat argumenty typu pomocí `TypeFilterAttribute` :</span><span class="sxs-lookup"><span data-stu-id="7c5f7-311">The following example shows how to pass arguments to a type using `TypeFilterAttribute`:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_TypeFilter&highlight=1,2)]

<!-- 
https://localhost:5001/home/hi?name=joe
VS debug window shows 
FiltersSample.Filters.LogConstantFilter:Information: Method 'Hi' called
-->

## <a name="authorization-filters"></a><span data-ttu-id="7c5f7-312">Filtry autorizace</span><span class="sxs-lookup"><span data-stu-id="7c5f7-312">Authorization filters</span></span>

<span data-ttu-id="7c5f7-313">Filtry autorizace:</span><span class="sxs-lookup"><span data-stu-id="7c5f7-313">Authorization filters:</span></span>

* <span data-ttu-id="7c5f7-314">Jsou první filtry spouštěny v kanálu filtru.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-314">Are the first filters run in the filter pipeline.</span></span>
* <span data-ttu-id="7c5f7-315">Řízení přístupu k metodám akcí.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-315">Control access to action methods.</span></span>
* <span data-ttu-id="7c5f7-316">Před metodou, ale ne po metodě.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-316">Have a before method, but no after method.</span></span>

<span data-ttu-id="7c5f7-317">Vlastní autorizační filtry vyžadují vlastní autorizační rozhraní.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-317">Custom authorization filters require a custom authorization framework.</span></span> <span data-ttu-id="7c5f7-318">Preferovat konfiguraci autorizačních zásad nebo psaní vlastních zásad autorizace při psaní vlastního filtru.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-318">Prefer configuring the authorization policies or writing a custom authorization policy over writing a custom filter.</span></span> <span data-ttu-id="7c5f7-319">Vestavěný autorizační filtr:</span><span class="sxs-lookup"><span data-stu-id="7c5f7-319">The built-in authorization filter:</span></span>

* <span data-ttu-id="7c5f7-320">Volá autorizační systém.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-320">Calls the authorization system.</span></span>
* <span data-ttu-id="7c5f7-321">Neautorizuje požadavky.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-321">Does not authorize requests.</span></span>

<span data-ttu-id="7c5f7-322">Negenerovat **výjimky** v rámci autorizačních filtrů:</span><span class="sxs-lookup"><span data-stu-id="7c5f7-322">Do **not** throw exceptions within authorization filters:</span></span>

* <span data-ttu-id="7c5f7-323">Výjimka nebude zpracována.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-323">The exception will not be handled.</span></span>
* <span data-ttu-id="7c5f7-324">Filtry výjimek nebudou zpracovávat výjimku.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-324">Exception filters will not handle the exception.</span></span>

<span data-ttu-id="7c5f7-325">Zvažte vydání výzvy, pokud dojde k výjimce ve autorizačním filtru.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-325">Consider issuing a challenge when an exception occurs in an authorization filter.</span></span>

<span data-ttu-id="7c5f7-326">Přečtěte si další informace o [autorizaci](xref:security/authorization/introduction).</span><span class="sxs-lookup"><span data-stu-id="7c5f7-326">Learn more about [Authorization](xref:security/authorization/introduction).</span></span>

## <a name="resource-filters"></a><span data-ttu-id="7c5f7-327">Filtry prostředků</span><span class="sxs-lookup"><span data-stu-id="7c5f7-327">Resource filters</span></span>

<span data-ttu-id="7c5f7-328">Filtry prostředků:</span><span class="sxs-lookup"><span data-stu-id="7c5f7-328">Resource filters:</span></span>

* <span data-ttu-id="7c5f7-329">Implementujte <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> rozhraní nebo <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> .</span><span class="sxs-lookup"><span data-stu-id="7c5f7-329">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> interface.</span></span>
* <span data-ttu-id="7c5f7-330">Provádění zabalí většinu kanálu filtru.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-330">Execution wraps most of the filter pipeline.</span></span>
* <span data-ttu-id="7c5f7-331">Před filtry prostředků se spouštějí jenom [filtry autorizace](#authorization-filters) .</span><span class="sxs-lookup"><span data-stu-id="7c5f7-331">Only [Authorization filters](#authorization-filters) run before resource filters.</span></span>

<span data-ttu-id="7c5f7-332">Filtry prostředků jsou užitečné pro krátkodobé okruhy v kanálu.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-332">Resource filters are useful to short-circuit most of the pipeline.</span></span> <span data-ttu-id="7c5f7-333">Například filtr ukládání do mezipaměti může zabránit zbývajícímu kanálu v případě přístupů do mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-333">For example, a caching filter can avoid the rest of the pipeline on a cache hit.</span></span>

<span data-ttu-id="7c5f7-334">Příklady filtru prostředků:</span><span class="sxs-lookup"><span data-stu-id="7c5f7-334">Resource filter examples:</span></span>

* <span data-ttu-id="7c5f7-335">[Filtr prostředků se zkrácenými okruhy](#short-circuiting-resource-filter) zobrazenými dříve.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-335">[The short-circuiting resource filter](#short-circuiting-resource-filter) shown previously.</span></span>
* <span data-ttu-id="7c5f7-336">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span><span class="sxs-lookup"><span data-stu-id="7c5f7-336">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span></span>

  * <span data-ttu-id="7c5f7-337">Zabraňuje vazbě modelu v přístupu k datům formuláře.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-337">Prevents model binding from accessing the form data.</span></span>
  * <span data-ttu-id="7c5f7-338">Používá se pro nahrávání velkých souborů, aby se zabránilo čtení dat z formuláře do paměti.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-338">Used for large file uploads to prevent the form data from being read into memory.</span></span>

## <a name="action-filters"></a><span data-ttu-id="7c5f7-339">Filtry akcí</span><span class="sxs-lookup"><span data-stu-id="7c5f7-339">Action filters</span></span>

<span data-ttu-id="7c5f7-340">Filtry **akcí se nevztahují** na Razor stránky.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-340">Action filters do **not** apply to Razor Pages.</span></span> Razor<span data-ttu-id="7c5f7-341">Stránky podporují <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> a <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span><span class="sxs-lookup"><span data-stu-id="7c5f7-341"> Pages supports <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span></span> <span data-ttu-id="7c5f7-342">Další informace najdete v tématu [metody filtrování pro Razor stránky](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="7c5f7-342">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

<span data-ttu-id="7c5f7-343">Filtry akcí:</span><span class="sxs-lookup"><span data-stu-id="7c5f7-343">Action filters:</span></span>

* <span data-ttu-id="7c5f7-344">Implementujte <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> rozhraní nebo <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> .</span><span class="sxs-lookup"><span data-stu-id="7c5f7-344">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> interface.</span></span>
* <span data-ttu-id="7c5f7-345">Jejich spuštění obklopuje provádění metod akcí.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-345">Their execution surrounds the execution of action methods.</span></span>

<span data-ttu-id="7c5f7-346">Následující kód ukazuje filtr vzorové akce:</span><span class="sxs-lookup"><span data-stu-id="7c5f7-346">The following code shows a sample action filter:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="7c5f7-347"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext>Poskytuje následující vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="7c5f7-347">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> provides the following properties:</span></span>

* <span data-ttu-id="7c5f7-348"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments>– povolí čtení vstupů do metody Action.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-348"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> - enables reading the inputs to an action method.</span></span>
* <span data-ttu-id="7c5f7-349"><xref:Microsoft.AspNetCore.Mvc.Controller>– povolí manipulaci s instancí kontroleru.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-349"><xref:Microsoft.AspNetCore.Mvc.Controller> - enables manipulating the controller instance.</span></span>
* <span data-ttu-id="7c5f7-350"><xref:System.Web.Mvc.ActionExecutingContext.Result>– nastavení `Result` krátkodobého provádění metody akce a následných filtrů akcí.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-350"><xref:System.Web.Mvc.ActionExecutingContext.Result> - setting `Result` short-circuits execution of the action method and subsequent action filters.</span></span>

<span data-ttu-id="7c5f7-351">Vyvolání výjimky v metodě akce:</span><span class="sxs-lookup"><span data-stu-id="7c5f7-351">Throwing an exception in an action method:</span></span>

* <span data-ttu-id="7c5f7-352">Zabraňuje spuštění dalších filtrů.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-352">Prevents running of subsequent filters.</span></span>
* <span data-ttu-id="7c5f7-353">Na rozdíl od nastavení `Result` se místo úspěšného výsledku považuje za selhání.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-353">Unlike setting `Result`, is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="7c5f7-354"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext>Poskytuje `Controller` a `Result` plus následující vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="7c5f7-354">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> provides `Controller` and `Result` plus the following properties:</span></span>

* <span data-ttu-id="7c5f7-355"><xref:System.Web.Mvc.ActionExecutedContext.Canceled>-True, pokud bylo provedení akce zkráceno jiným filtrem.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-355"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> - True if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="7c5f7-356"><xref:System.Web.Mvc.ActionExecutedContext.Exception>-Hodnota není null, pokud akce nebo dříve vyvolala filtr akcí.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-356"><xref:System.Web.Mvc.ActionExecutedContext.Exception> - Non-null if the action or a previously run action filter threw an exception.</span></span> <span data-ttu-id="7c5f7-357">Nastavení této vlastnosti na hodnotu null:</span><span class="sxs-lookup"><span data-stu-id="7c5f7-357">Setting this property to null:</span></span>

  * <span data-ttu-id="7c5f7-358">Efektivně zpracovává výjimku.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-358">Effectively handles the exception.</span></span>
  * <span data-ttu-id="7c5f7-359">`Result`je spuštěn, jako kdyby byl vrácen z metody Action.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-359">`Result` is executed as if it was returned from the action method.</span></span>

<span data-ttu-id="7c5f7-360">V případě `IAsyncActionFilter` volání <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> :</span><span class="sxs-lookup"><span data-stu-id="7c5f7-360">For an `IAsyncActionFilter`, a call to the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span></span>

* <span data-ttu-id="7c5f7-361">Provede všechny následné filtry akcí a metodu Action.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-361">Executes any subsequent action filters and the action method.</span></span>
* <span data-ttu-id="7c5f7-362">Vrací objekt `ActionExecutedContext`.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-362">Returns `ActionExecutedContext`.</span></span>

<span data-ttu-id="7c5f7-363">Do krátkodobého okruhu, přiřaďte <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> k instanci výsledku a Nevolejte `next` (a `ActionExecutionDelegate` ).</span><span class="sxs-lookup"><span data-stu-id="7c5f7-363">To short-circuit, assign <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> to a result instance and don't call `next` (the `ActionExecutionDelegate`).</span></span>

<span data-ttu-id="7c5f7-364">Rozhraní poskytuje abstrakci <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> , která může být podtříd.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-364">The framework provides an abstract <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> that can be subclassed.</span></span>

<span data-ttu-id="7c5f7-365">`OnActionExecuting`Filtr akcí lze použít k těmto akcím:</span><span class="sxs-lookup"><span data-stu-id="7c5f7-365">The `OnActionExecuting` action filter can be used to:</span></span>

* <span data-ttu-id="7c5f7-366">Ověří stav modelu.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-366">Validate model state.</span></span>
* <span data-ttu-id="7c5f7-367">Pokud je stav neplatný, vrátí se chyba.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-367">Return an error if the state is invalid.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet)]

> [!NOTE]
> <span data-ttu-id="7c5f7-368">Řadiče s poznámkou s `[ApiController]` atributem automaticky ověří stav modelu a vrátí odpověď 400.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-368">Controllers annotated with the `[ApiController]` attribute automatically validate model state and return a 400 response.</span></span> <span data-ttu-id="7c5f7-369">Další informace najdete v tématu [Automatické odpovědi HTTP 400](xref:web-api/index#automatic-http-400-responses).</span><span class="sxs-lookup"><span data-stu-id="7c5f7-369">For more information, see [Automatic HTTP 400 responses](xref:web-api/index#automatic-http-400-responses).</span></span>

<span data-ttu-id="7c5f7-370">`OnActionExecuted`Metoda se spustí za metodou akce:</span><span class="sxs-lookup"><span data-stu-id="7c5f7-370">The `OnActionExecuted` method runs after the action method:</span></span>

* <span data-ttu-id="7c5f7-371">A mohou zobrazit výsledky akce a manipulovat s nimi prostřednictvím <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> Vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-371">And can see and manipulate the results of the action through the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> property.</span></span>
* <span data-ttu-id="7c5f7-372"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled>je nastaven na hodnotu true, pokud bylo provedení akce zkráceno jiným filtrem.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-372"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> is set to true if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="7c5f7-373"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception>je nastavena na hodnotu jinou než null, pokud akce nebo filtr následné akce vyvolaly výjimku.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-373"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> is set to a non-null value if the action or a subsequent action filter threw an exception.</span></span> <span data-ttu-id="7c5f7-374">Nastavení `Exception` na hodnotu null:</span><span class="sxs-lookup"><span data-stu-id="7c5f7-374">Setting `Exception` to null:</span></span>

  * <span data-ttu-id="7c5f7-375">Efektivně zpracovává výjimku.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-375">Effectively handles an exception.</span></span>
  * <span data-ttu-id="7c5f7-376">`ActionExecutedContext.Result`je spuštěn, jako kdyby byl vrácen normálně z metody Action.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-376">`ActionExecutedContext.Result` is executed as if it were returned normally from the action method.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet2&higlight=12-99)]

## <a name="exception-filters"></a><span data-ttu-id="7c5f7-377">Filtry výjimek</span><span class="sxs-lookup"><span data-stu-id="7c5f7-377">Exception filters</span></span>

<span data-ttu-id="7c5f7-378">Filtry výjimek:</span><span class="sxs-lookup"><span data-stu-id="7c5f7-378">Exception filters:</span></span>

* <span data-ttu-id="7c5f7-379">Implementujte <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> nebo <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter> .</span><span class="sxs-lookup"><span data-stu-id="7c5f7-379">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span></span>
* <span data-ttu-id="7c5f7-380">Dá se použít k implementaci běžných zásad zpracování chyb.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-380">Can be used to implement common error handling policies.</span></span>

<span data-ttu-id="7c5f7-381">Následující ukázkový filtr výjimek používá vlastní zobrazení chyb pro zobrazení podrobností o výjimkách, ke kterým dochází při vývoji aplikace:</span><span class="sxs-lookup"><span data-stu-id="7c5f7-381">The following sample exception filter uses a custom error view to display details about exceptions that occur when the app is in development:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/CustomExceptionFilter.cs?name=snippet_ExceptionFilter&highlight=16-19)]

<span data-ttu-id="7c5f7-382">Následující kód testuje filtr výjimky:</span><span class="sxs-lookup"><span data-stu-id="7c5f7-382">The following code tests the exception filter:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Controllers/FailingController.cs?name=snippet)]

<span data-ttu-id="7c5f7-383">Filtry výjimek:</span><span class="sxs-lookup"><span data-stu-id="7c5f7-383">Exception filters:</span></span>

* <span data-ttu-id="7c5f7-384">Nemusíte mít události před a po.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-384">Don't have before and after events.</span></span>
* <span data-ttu-id="7c5f7-385">Implementujte <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> nebo <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*> .</span><span class="sxs-lookup"><span data-stu-id="7c5f7-385">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span></span>
* <span data-ttu-id="7c5f7-386">Zpracování neošetřených výjimek, ke kterým dochází při Razor vytváření stránky nebo kontroleru, [vázání modelů](xref:mvc/models/model-binding), filtrů akcí nebo metod akcí.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-386">Handle unhandled exceptions that occur in Razor Page or controller creation, [model binding](xref:mvc/models/model-binding), action filters, or action methods.</span></span>
* <span data-ttu-id="7c5f7-387">**Nezachycujte** výjimky, ke kterým dochází v filtrech prostředků, výsledných filtrech nebo v důsledku provádění výsledků MVC.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-387">Do **not** catch exceptions that occur in resource filters, result filters, or MVC result execution.</span></span>

<span data-ttu-id="7c5f7-388">Chcete-li zpracovat výjimku, nastavte <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> vlastnost na `true` nebo zapište odpověď.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-388">To handle an exception, set the <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> property to `true` or write a response.</span></span> <span data-ttu-id="7c5f7-389">Tím se zastaví šíření výjimky.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-389">This stops propagation of the exception.</span></span> <span data-ttu-id="7c5f7-390">Filtr výjimek nemůže vypnout výjimku na "úspěch".</span><span class="sxs-lookup"><span data-stu-id="7c5f7-390">An exception filter can't turn an exception into a "success".</span></span> <span data-ttu-id="7c5f7-391">To může provést pouze filtr akcí.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-391">Only an action filter can do that.</span></span>

<span data-ttu-id="7c5f7-392">Filtry výjimek:</span><span class="sxs-lookup"><span data-stu-id="7c5f7-392">Exception filters:</span></span>

* <span data-ttu-id="7c5f7-393">Jsou vhodné pro vytváření přesahů výjimek, ke kterým dochází v rámci akcí.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-393">Are good for trapping exceptions that occur within actions.</span></span>
* <span data-ttu-id="7c5f7-394">Nejsou tak flexibilní jako middleware při zpracování chyb.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-394">Are not as flexible as error handling middleware.</span></span>

<span data-ttu-id="7c5f7-395">Preferovat middleware pro zpracování výjimek.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-395">Prefer middleware for exception handling.</span></span> <span data-ttu-id="7c5f7-396">Filtry výjimek použijte pouze v případě, že se zpracování chyb *liší* v závislosti na tom, která metoda Action je volána.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-396">Use exception filters only where error handling *differs* based on which action method is called.</span></span> <span data-ttu-id="7c5f7-397">Aplikace může například mít metody akcí pro koncové body rozhraní API i pro zobrazení/HTML.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-397">For example, an app might have action methods for both API endpoints and for views/HTML.</span></span> <span data-ttu-id="7c5f7-398">Koncové body rozhraní API mohou vracet informace o chybě jako JSON, zatímco akce na základě zobrazení by mohly vracet chybovou stránku jako HTML.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-398">The API endpoints could return error information as JSON, while the view-based actions could return an error page as HTML.</span></span>

## <a name="result-filters"></a><span data-ttu-id="7c5f7-399">Filtry výsledků</span><span class="sxs-lookup"><span data-stu-id="7c5f7-399">Result filters</span></span>

<span data-ttu-id="7c5f7-400">Filtry výsledků:</span><span class="sxs-lookup"><span data-stu-id="7c5f7-400">Result filters:</span></span>

* <span data-ttu-id="7c5f7-401">Implementace rozhraní:</span><span class="sxs-lookup"><span data-stu-id="7c5f7-401">Implement an interface:</span></span>
  * <span data-ttu-id="7c5f7-402"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> nebo <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="7c5f7-402"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
  * <span data-ttu-id="7c5f7-403"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> nebo <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span><span class="sxs-lookup"><span data-stu-id="7c5f7-403"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span></span>
* <span data-ttu-id="7c5f7-404">Jejich spuštění obklopuje provádění výsledků akcí.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-404">Their execution surrounds the execution of action results.</span></span>

### <a name="iresultfilter-and-iasyncresultfilter"></a><span data-ttu-id="7c5f7-405">IResultFilter a IAsyncResultFilter</span><span class="sxs-lookup"><span data-stu-id="7c5f7-405">IResultFilter and IAsyncResultFilter</span></span>

<span data-ttu-id="7c5f7-406">Následující kód ukazuje filtr výsledků, který přidá hlavičku protokolu HTTP:</span><span class="sxs-lookup"><span data-stu-id="7c5f7-406">The following code shows a result filter that adds an HTTP header:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="7c5f7-407">Typ výsledku, který se má provést, závisí na akci.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-407">The kind of result being executed depends on the action.</span></span> <span data-ttu-id="7c5f7-408">Akce vracející zobrazení zahrnuje všechny zpracování Razor jako součást prováděné <xref:Microsoft.AspNetCore.Mvc.ViewResult> .</span><span class="sxs-lookup"><span data-stu-id="7c5f7-408">An action returning a view includes all razor processing as part of the <xref:Microsoft.AspNetCore.Mvc.ViewResult> being executed.</span></span> <span data-ttu-id="7c5f7-409">Metoda rozhraní API může provést určitou serializaci v rámci provádění výsledku.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-409">An API method might perform some serialization as part of the execution of the result.</span></span> <span data-ttu-id="7c5f7-410">Přečtěte si další informace o [výsledcích akcí](xref:mvc/controllers/actions).</span><span class="sxs-lookup"><span data-stu-id="7c5f7-410">Learn more about [action results](xref:mvc/controllers/actions).</span></span>

<span data-ttu-id="7c5f7-411">Filtry výsledků se spustí pouze v případě, že akce nebo filtr akcí vytvoří výsledek akce.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-411">Result filters are only executed when an action or action filter produces an action result.</span></span> <span data-ttu-id="7c5f7-412">Filtry výsledků nejsou provedeny v těchto případech:</span><span class="sxs-lookup"><span data-stu-id="7c5f7-412">Result filters are not executed when:</span></span>

* <span data-ttu-id="7c5f7-413">Filtr autorizace nebo filtr prostředků pro krátké okruhy kanálu.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-413">An authorization filter or resource filter short-circuits the pipeline.</span></span>
* <span data-ttu-id="7c5f7-414">Filtr výjimek zpracovává výjimku tím, že vyprodukuje výsledek akce.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-414">An exception filter handles an exception by producing an action result.</span></span>

<span data-ttu-id="7c5f7-415"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName>Metoda může zkrátit provádění výsledků akce a následných filtrů výsledků nastavením <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> na `true` .</span><span class="sxs-lookup"><span data-stu-id="7c5f7-415">The <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> method can short-circuit execution of the action result and subsequent result filters by setting <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> to `true`.</span></span> <span data-ttu-id="7c5f7-416">Zapište do objektu Response v případě krátkého okruhu, abyste se vyhnuli vygenerování prázdné odpovědi.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-416">Write to the response object when short-circuiting to avoid generating an empty response.</span></span> <span data-ttu-id="7c5f7-417">Vyvolání výjimky v `IResultFilter.OnResultExecuting` :</span><span class="sxs-lookup"><span data-stu-id="7c5f7-417">Throwing an exception in `IResultFilter.OnResultExecuting`:</span></span>

* <span data-ttu-id="7c5f7-418">Zabraňuje spuštění výsledků akce a následných filtrů.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-418">Prevents execution of the action result and subsequent filters.</span></span>
* <span data-ttu-id="7c5f7-419">Se považuje za selhání namísto úspěšného výsledku.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-419">Is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="7c5f7-420">Při <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> spuštění metody je odpověď pravděpodobně již odeslána klientovi.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-420">When the <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> method runs, the response has probably already been sent to the client.</span></span> <span data-ttu-id="7c5f7-421">Pokud byla odpověď již odeslána klientovi, nelze ji změnit.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-421">If the response has already been sent to the client, it cannot be changed.</span></span>

<span data-ttu-id="7c5f7-422">`ResultExecutedContext.Canceled`je nastaven na hodnotu, `true` Pokud je spuštění výsledku akce zkráceno jiným filtrem.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-422">`ResultExecutedContext.Canceled` is set to `true` if the action result execution was short-circuited by another filter.</span></span>

<span data-ttu-id="7c5f7-423">`ResultExecutedContext.Exception`je nastavena na hodnotu jinou než null, pokud výsledek akce nebo následný filtr výsledků vyvolal výjimku.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-423">`ResultExecutedContext.Exception` is set to a non-null value if the action result or a subsequent result filter threw an exception.</span></span> <span data-ttu-id="7c5f7-424">Nastavení `Exception` na hodnotu null efektivně zpracuje výjimku a zabrání vyvolání výjimky později v kanálu.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-424">Setting `Exception` to null effectively handles an exception and prevents the exception from being thrown again later in the pipeline.</span></span> <span data-ttu-id="7c5f7-425">Neexistuje žádný spolehlivý způsob, jak zapisovat data do odpovědi při zpracování výjimky ve filtru výsledků.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-425">There is no reliable way to write data to a response when handling an exception in a result filter.</span></span> <span data-ttu-id="7c5f7-426">Pokud byly hlavičky vyprázdněny do klienta, pokud výsledek akce vyvolá výjimku, neexistuje žádný spolehlivý mechanismus pro odeslání kódu chyby.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-426">If the headers have been flushed to the client when an action result throws an exception, there's no reliable mechanism to send a failure code.</span></span>

<span data-ttu-id="7c5f7-427">V případě <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter> volání `await next` na se <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> spustí všechny následné filtry výsledků a výsledek akce.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-427">For an <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, a call to `await next` on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> executes any subsequent result filters and the action result.</span></span> <span data-ttu-id="7c5f7-428">Do krátkodobého okruhu nastavte [ResultExecutingContext. Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) na `true` a Nevolejte na `ResultExecutionDelegate` :</span><span class="sxs-lookup"><span data-stu-id="7c5f7-428">To short-circuit, set [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) to `true` and don't call the `ResultExecutionDelegate`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MyAsyncResponseFilter.cs?name=snippet)]

<span data-ttu-id="7c5f7-429">Rozhraní poskytuje abstrakci `ResultFilterAttribute` , která může být podtříd.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-429">The framework provides an abstract `ResultFilterAttribute` that can be subclassed.</span></span> <span data-ttu-id="7c5f7-430">Dříve uvedená třída [AddHeaderAttribute](#add-header-attribute) je příkladem atributu filtru výsledků.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-430">The [AddHeaderAttribute](#add-header-attribute) class shown previously is an example of a result filter attribute.</span></span>

### <a name="ialwaysrunresultfilter-and-iasyncalwaysrunresultfilter"></a><span data-ttu-id="7c5f7-431">IAlwaysRunResultFilter a IAsyncAlwaysRunResultFilter</span><span class="sxs-lookup"><span data-stu-id="7c5f7-431">IAlwaysRunResultFilter and IAsyncAlwaysRunResultFilter</span></span>

<span data-ttu-id="7c5f7-432"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter>Rozhraní a <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> deklaruje <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> implementaci, která je spuštěna pro všechny výsledky akce.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-432">The <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> interfaces declare an <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> implementation that runs for all action results.</span></span> <span data-ttu-id="7c5f7-433">To zahrnuje výsledky akcí, které vytvořil:</span><span class="sxs-lookup"><span data-stu-id="7c5f7-433">This includes action results produced by:</span></span>

* <span data-ttu-id="7c5f7-434">Filtry autorizace a filtry prostředků, které mají krátký okruh.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-434">Authorization filters and resource filters that short-circuit.</span></span>
* <span data-ttu-id="7c5f7-435">Filtry výjimek.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-435">Exception filters.</span></span>

<span data-ttu-id="7c5f7-436">Například následující filtr vždy spustí a nastaví výsledek akce ( <xref:Microsoft.AspNetCore.Mvc.ObjectResult> ) s *422 nezpracovaným* stavovým kódem entity, pokud se nezdaří vyjednávání obsahu:</span><span class="sxs-lookup"><span data-stu-id="7c5f7-436">For example, the following filter always runs and sets an action result (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) with a *422 Unprocessable Entity* status code when content negotiation fails:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/UnprocessableResultFilter.cs?name=snippet)]

### <a name="ifilterfactory"></a><span data-ttu-id="7c5f7-437">IFilterFactory</span><span class="sxs-lookup"><span data-stu-id="7c5f7-437">IFilterFactory</span></span>

<span data-ttu-id="7c5f7-438"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>implementuje <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> .</span><span class="sxs-lookup"><span data-stu-id="7c5f7-438"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span></span> <span data-ttu-id="7c5f7-439">Proto `IFilterFactory` může být instance použita jako `IFilterMetadata` instance kdekoli v kanálu filtru.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-439">Therefore, an `IFilterFactory` instance can be used as an `IFilterMetadata` instance anywhere in the filter pipeline.</span></span> <span data-ttu-id="7c5f7-440">Když se modul runtime připraví k vyvolání filtru, pokusí se ho přetypovat na `IFilterFactory` .</span><span class="sxs-lookup"><span data-stu-id="7c5f7-440">When the runtime prepares to invoke the filter, it attempts to cast it to an `IFilterFactory`.</span></span> <span data-ttu-id="7c5f7-441">Pokud je toto přetypování úspěšné, <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> je volána metoda pro vytvoření `IFilterMetadata` instance, která je vyvolána.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-441">If that cast succeeds, the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method is called to create the `IFilterMetadata` instance that is invoked.</span></span> <span data-ttu-id="7c5f7-442">To poskytuje flexibilní návrh, protože přesný kanál filtru není nutné nastavit explicitně při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-442">This provides a flexible design, since the precise filter pipeline doesn't need to be set explicitly when the app starts.</span></span>

<span data-ttu-id="7c5f7-443">`IFilterFactory`může být implementováno pomocí implementace vlastního atributu jako jiný přístup k vytváření filtrů:</span><span class="sxs-lookup"><span data-stu-id="7c5f7-443">`IFilterFactory` can be implemented using custom attribute implementations as another approach to creating filters:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/AddHeaderWithFactoryAttribute.cs?name=snippet_IFilterFactory&highlight=1,4,5,6,7)]

<span data-ttu-id="7c5f7-444">Filtr je použit v následujícím kódu:</span><span class="sxs-lookup"><span data-stu-id="7c5f7-444">The filter is applied in the following code:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet3&highlight=21)]

<span data-ttu-id="7c5f7-445">Otestujte předchozí kód spuštěním [ukázky stahování](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample):</span><span class="sxs-lookup"><span data-stu-id="7c5f7-445">Test the preceding code by running the [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample):</span></span>

* <span data-ttu-id="7c5f7-446">Vyvolejte vývojářské nástroje F12.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-446">Invoke the F12 developer tools.</span></span>
* <span data-ttu-id="7c5f7-447">Přejděte na adresu `https://localhost:5001/Sample/HeaderWithFactory`.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-447">Navigate to `https://localhost:5001/Sample/HeaderWithFactory`.</span></span>

<span data-ttu-id="7c5f7-448">Vývojářské nástroje F12 zobrazují následující hlavičky odpovědí přidané ukázkovým kódem:</span><span class="sxs-lookup"><span data-stu-id="7c5f7-448">The F12 developer tools display the following response headers added by the sample code:</span></span>

* <span data-ttu-id="7c5f7-449">**Autor:**`Rick Anderson`</span><span class="sxs-lookup"><span data-stu-id="7c5f7-449">**author:** `Rick Anderson`</span></span>
* <span data-ttu-id="7c5f7-450">**globaladdheader:**`Result filter added to MvcOptions.Filters`</span><span class="sxs-lookup"><span data-stu-id="7c5f7-450">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span></span>
* <span data-ttu-id="7c5f7-451">**interní:**`My header`</span><span class="sxs-lookup"><span data-stu-id="7c5f7-451">**internal:** `My header`</span></span>

<span data-ttu-id="7c5f7-452">Předchozí kód vytvoří hlavičku **interního:** `My header` Response.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-452">The preceding code creates the **internal:** `My header` response header.</span></span>

### <a name="ifilterfactory-implemented-on-an-attribute"></a><span data-ttu-id="7c5f7-453">IFilterFactory implementované u atributu</span><span class="sxs-lookup"><span data-stu-id="7c5f7-453">IFilterFactory implemented on an attribute</span></span>

<!-- Review 
This section needs to be rewritten.
What's a non-named attribute?
-->

<span data-ttu-id="7c5f7-454">Filtry, které implementují, `IFilterFactory` jsou užitečné pro filtry, které:</span><span class="sxs-lookup"><span data-stu-id="7c5f7-454">Filters that implement `IFilterFactory` are useful for filters that:</span></span>

* <span data-ttu-id="7c5f7-455">Nevyžadovat předávání parametrů</span><span class="sxs-lookup"><span data-stu-id="7c5f7-455">Don't require passing parameters.</span></span>
* <span data-ttu-id="7c5f7-456">Musí mít závislosti konstruktoru, které musí vyplnit DI.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-456">Have constructor dependencies that need to be filled by DI.</span></span>

<span data-ttu-id="7c5f7-457"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>implementuje <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> .</span><span class="sxs-lookup"><span data-stu-id="7c5f7-457"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="7c5f7-458">`IFilterFactory`zpřístupňuje <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> metodu pro vytvoření <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-458">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="7c5f7-459">`CreateInstance`Načte zadaný typ z kontejneru Services (DI).</span><span class="sxs-lookup"><span data-stu-id="7c5f7-459">`CreateInstance` loads the specified type from the services container (DI).</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/SampleActionFilterAttribute.cs?name=snippet_TypeFilterAttribute&highlight=1,3,7)]

<span data-ttu-id="7c5f7-460">Následující kód ukazuje tři přístupy k použití `[SampleActionFilter]` :</span><span class="sxs-lookup"><span data-stu-id="7c5f7-460">The following code shows three approaches to applying the `[SampleActionFilter]`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet&highlight=1)]

<span data-ttu-id="7c5f7-461">V předchozím kódu upravení metoda s jako `[SampleActionFilter]` upřednostňovaným přístupem k použití `SampleActionFilter` .</span><span class="sxs-lookup"><span data-stu-id="7c5f7-461">In the preceding code, decorating the method with `[SampleActionFilter]` is the preferred approach to applying the `SampleActionFilter`.</span></span>

## <a name="using-middleware-in-the-filter-pipeline"></a><span data-ttu-id="7c5f7-462">Použití middlewaru v kanálu filtru</span><span class="sxs-lookup"><span data-stu-id="7c5f7-462">Using middleware in the filter pipeline</span></span>

<span data-ttu-id="7c5f7-463">Filtry prostředků fungují jako [middleware](xref:fundamentals/middleware/index) v tom, že obklopují provádění všech položek, které jsou později v kanálu.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-463">Resource filters work like [middleware](xref:fundamentals/middleware/index) in that they surround the execution of everything that comes later in the pipeline.</span></span> <span data-ttu-id="7c5f7-464">Filtry se ale liší od middlewaru v tom, že jsou součástí modulu runtime, což znamená, že mají přístup k kontextu a sestavám.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-464">But filters differ from middleware in that they're part of the runtime, which means that they have access to context and constructs.</span></span>

<span data-ttu-id="7c5f7-465">Chcete-li použít middleware jako filtr, vytvořte typ s `Configure` metodou, která určuje middleware pro vložení do kanálu filtru.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-465">To use middleware as a filter, create a type with a `Configure` method that specifies the middleware to inject into the filter pipeline.</span></span> <span data-ttu-id="7c5f7-466">Následující příklad používá middleware Localization k vytvoření aktuální jazykové verze pro požadavek:</span><span class="sxs-lookup"><span data-stu-id="7c5f7-466">The following example uses the localization middleware to establish the current culture for a request:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LocalizationPipeline.cs?name=snippet_MiddlewareFilter&highlight=3,22)]

<span data-ttu-id="7c5f7-467">Pomocí <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> Spusťte middleware:</span><span class="sxs-lookup"><span data-stu-id="7c5f7-467">Use the <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> to run the middleware:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_MiddlewareFilter&highlight=2)]

<span data-ttu-id="7c5f7-468">Filtry middlewaru jsou spouštěny ve stejné fázi kanálu filtru jako filtry prostředků, před vazbami modelů a po zbytek kanálu.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-468">Middleware filters run at the same stage of the filter pipeline as Resource filters, before model binding and after the rest of the pipeline.</span></span>

## <a name="next-actions"></a><span data-ttu-id="7c5f7-469">Další akce</span><span class="sxs-lookup"><span data-stu-id="7c5f7-469">Next actions</span></span>

* <span data-ttu-id="7c5f7-470">Viz [metody filtru pro Razor stránky](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="7c5f7-470">See [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>
* <span data-ttu-id="7c5f7-471">Pro experimentování s filtry, [stažení, otestování a úpravy ukázky GitHubu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample).</span><span class="sxs-lookup"><span data-stu-id="7c5f7-471">To experiment with filters, [download, test, and modify the GitHub sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="7c5f7-472">[Kirka Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Dykstra](https://github.com/tdykstra/)a [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="7c5f7-472">By [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="7c5f7-473">*Filtry* v ASP.NET Core umožňují spuštění kódu před nebo po určitých fázích v kanálu zpracování požadavků.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-473">*Filters* in ASP.NET Core allow code to be run before or after specific stages in the request processing pipeline.</span></span>

<span data-ttu-id="7c5f7-474">Předdefinované filtry zpracovávají úlohy, jako například:</span><span class="sxs-lookup"><span data-stu-id="7c5f7-474">Built-in filters handle tasks such as:</span></span>

* <span data-ttu-id="7c5f7-475">Autorizace (zabránění přístupu k prostředkům, ke kterým uživatel nemá oprávnění).</span><span class="sxs-lookup"><span data-stu-id="7c5f7-475">Authorization (preventing access to resources a user isn't authorized for).</span></span>
* <span data-ttu-id="7c5f7-476">Ukládání odpovědí do mezipaměti (při krátkém okruhu kanálu požadavků, který vrátí odpověď uloženou v mezipaměti).</span><span class="sxs-lookup"><span data-stu-id="7c5f7-476">Response caching (short-circuiting the request pipeline to return a cached response).</span></span>

<span data-ttu-id="7c5f7-477">Vlastní filtry je možné vytvořit pro zpracování vzájemně se týkajících se otázek.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-477">Custom filters can be created to handle cross-cutting concerns.</span></span> <span data-ttu-id="7c5f7-478">Mezi obavy mezi průřezy patří zpracování chyb, ukládání do mezipaměti, konfigurace, autorizace a protokolování.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-478">Examples of cross-cutting concerns include error handling, caching, configuration, authorization, and logging.</span></span>  <span data-ttu-id="7c5f7-479">Filtry zabraňují duplikování kódu.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-479">Filters avoid duplicating code.</span></span> <span data-ttu-id="7c5f7-480">Například filtr výjimek zpracování chyb může konsolidovat zpracování chyb.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-480">For example, an error handling exception filter could consolidate error handling.</span></span>

<span data-ttu-id="7c5f7-481">Tento dokument se týká Razor stránek, řadičů rozhraní API a řadičů se zobrazeními.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-481">This document applies to Razor Pages, API controllers, and controllers with views.</span></span>

<span data-ttu-id="7c5f7-482">[Zobrazit nebo Stáhnout ukázku](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample) ([Jak stáhnout](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="7c5f7-482">[View or download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="how-filters-work"></a><span data-ttu-id="7c5f7-483">Jak fungují filtry</span><span class="sxs-lookup"><span data-stu-id="7c5f7-483">How filters work</span></span>

<span data-ttu-id="7c5f7-484">Filtry se spouštějí v *kanálu vyvolání akce ASP.NET Core*, někdy označované jako *kanál filtru*.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-484">Filters run within the *ASP.NET Core action invocation pipeline*, sometimes referred to as the *filter pipeline*.</span></span>  <span data-ttu-id="7c5f7-485">Kanál filtru se spustí po ASP.NET Core vybere akci, která se má provést.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-485">The filter pipeline runs after ASP.NET Core selects the action to execute.</span></span>

![Požadavek se zpracovává prostřednictvím jiného middlewaru, middleware směrování, výběru akcí a kanálu vyvolání akce ASP.NET Core.](filters/_static/filter-pipeline-1.png)

### <a name="filter-types"></a><span data-ttu-id="7c5f7-488">Typy filtrů</span><span class="sxs-lookup"><span data-stu-id="7c5f7-488">Filter types</span></span>

<span data-ttu-id="7c5f7-489">Každý typ filtru se spustí v jiné fázi kanálu filtru:</span><span class="sxs-lookup"><span data-stu-id="7c5f7-489">Each filter type is executed at a different stage in the filter pipeline:</span></span>

* <span data-ttu-id="7c5f7-490">[Filtry autorizace](#authorization-filters) se spouštějí jako první a používají se k určení, jestli je uživatel autorizovaný pro požadavek.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-490">[Authorization filters](#authorization-filters) run first and are used to determine whether the user is authorized for the request.</span></span> <span data-ttu-id="7c5f7-491">Filtry autorizace pro krátkodobé vynechání kanálu, pokud je požadavek neautorizovaný</span><span class="sxs-lookup"><span data-stu-id="7c5f7-491">Authorization filters short-circuit the pipeline if the request is unauthorized.</span></span>

* <span data-ttu-id="7c5f7-492">[Filtry prostředků](#resource-filters):</span><span class="sxs-lookup"><span data-stu-id="7c5f7-492">[Resource filters](#resource-filters):</span></span>

  * <span data-ttu-id="7c5f7-493">Spusťte po autorizaci.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-493">Run after authorization.</span></span>  
  * <span data-ttu-id="7c5f7-494"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*>může spustit kód před zbytek kanálu filtru.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-494"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> can run code before the rest of the filter pipeline.</span></span> <span data-ttu-id="7c5f7-495">Například `OnResourceExecuting` může spustit kód před vytvořením vazby modelu.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-495">For example, `OnResourceExecuting` can run code before model binding.</span></span>
  * <span data-ttu-id="7c5f7-496"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*>může spustit kód po dokončení zbývající části kanálu.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-496"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> can run code after the rest of the pipeline has completed.</span></span>

* <span data-ttu-id="7c5f7-497">[Filtry akcí](#action-filters) mohou spustit kód bezprostředně před a po volání jednotlivé metody akce.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-497">[Action filters](#action-filters) can run code immediately before and after an individual action method is called.</span></span> <span data-ttu-id="7c5f7-498">Lze je použít k manipulaci s argumenty předaných do akce a výsledek vráceného z akce.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-498">They can be used to manipulate the arguments passed into an action and the result returned from the action.</span></span> <span data-ttu-id="7c5f7-499">Na stránkách se **nepodporují filtry** akcí Razor .</span><span class="sxs-lookup"><span data-stu-id="7c5f7-499">Action filters are **not** supported in Razor Pages.</span></span>

* <span data-ttu-id="7c5f7-500">[Filtry výjimek](#exception-filters) slouží k aplikování globálních zásad na neošetřené výjimky, ke kterým dojde před zapsáním cokoli do těla odpovědi.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-500">[Exception filters](#exception-filters) are used to apply global policies to unhandled exceptions that occur before anything has been written to the response body.</span></span>

* <span data-ttu-id="7c5f7-501">[Filtry výsledků](#result-filters) můžou spustit kód hned před a po provedení jednotlivých výsledků akce.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-501">[Result filters](#result-filters) can run code immediately before and after the execution of individual action results.</span></span> <span data-ttu-id="7c5f7-502">Spustí se pouze v případě, že metoda akce byla úspěšně provedena.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-502">They run only when the action method has executed successfully.</span></span> <span data-ttu-id="7c5f7-503">Jsou užitečné pro logiku, která musí obklopit zobrazení nebo formátovací modul.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-503">They are useful for logic that must surround view or formatter execution.</span></span>

<span data-ttu-id="7c5f7-504">Následující diagram znázorňuje způsob interakce typů filtrů v kanálu filtru.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-504">The following diagram shows how filter types interact in the filter pipeline.</span></span>

![Požadavek se zpracovává pomocí autorizačních filtrů, filtrů prostředků, vazeb modelů, filtrů akcí, provádění akcí a konverze výsledků akcí, filtrů výjimek, výsledných filtrů a provádění výsledků.](filters/_static/filter-pipeline-2.png)

## <a name="implementation"></a><span data-ttu-id="7c5f7-507">Implementace</span><span class="sxs-lookup"><span data-stu-id="7c5f7-507">Implementation</span></span>

<span data-ttu-id="7c5f7-508">Filtry podporují synchronní i asynchronní implementace prostřednictvím různých definic rozhraní.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-508">Filters support both synchronous and asynchronous implementations through different interface definitions.</span></span>

<span data-ttu-id="7c5f7-509">Synchronní filtry mohou spustit kód před ( `On-Stage-Executing` ) a po ( `On-Stage-Executed` ) jejich fáze zřetězení.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-509">Synchronous filters can run code before (`On-Stage-Executing`) and after (`On-Stage-Executed`) their pipeline stage.</span></span> <span data-ttu-id="7c5f7-510">Například `OnActionExecuting` je volána před voláním metody Action.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-510">For example, `OnActionExecuting` is called before the action method is called.</span></span> <span data-ttu-id="7c5f7-511">`OnActionExecuted`je volána po návratu metody Action.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-511">`OnActionExecuted` is called after the action method returns.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="7c5f7-512">Asynchronní filtry definují `On-Stage-ExecutionAsync` metodu:</span><span class="sxs-lookup"><span data-stu-id="7c5f7-512">Asynchronous filters define an `On-Stage-ExecutionAsync` method:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/SampleAsyncActionFilter.cs?name=snippet)]

<span data-ttu-id="7c5f7-513">V předchozím kódu `SampleAsyncActionFilter` obsahuje znak <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> ( `next` ), který provádí metodu Action.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-513">In the preceding code, the `SampleAsyncActionFilter` has an <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`)  that executes the action method.</span></span>  <span data-ttu-id="7c5f7-514">Každá z těchto `On-Stage-ExecutionAsync` metod vezme `FilterType-ExecutionDelegate` fázi zřetězení filtru.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-514">Each of the `On-Stage-ExecutionAsync` methods take a `FilterType-ExecutionDelegate` that executes the filter's pipeline stage.</span></span>

### <a name="multiple-filter-stages"></a><span data-ttu-id="7c5f7-515">Několik fází filtru</span><span class="sxs-lookup"><span data-stu-id="7c5f7-515">Multiple filter stages</span></span>

<span data-ttu-id="7c5f7-516">Rozhraní pro více fází filtru lze implementovat v rámci jedné třídy.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-516">Interfaces for multiple filter stages can be implemented in a single class.</span></span> <span data-ttu-id="7c5f7-517">Například <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> Třída implementuje `IActionFilter` , `IResultFilter` a jejich asynchronní ekvivalenty.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-517">For example, the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> class implements `IActionFilter`, `IResultFilter`, and their async equivalents.</span></span>

<span data-ttu-id="7c5f7-518">Implementujte **buď** synchronní, nebo asynchronní verzi rozhraní filtru, **nikoli** obojí.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-518">Implement **either** the synchronous or the async version of a filter interface, **not** both.</span></span> <span data-ttu-id="7c5f7-519">Modul runtime nejprve kontroluje, zda filtr implementuje asynchronní rozhraní a v takovém případě jej zavolá.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-519">The runtime checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="7c5f7-520">V takovém případě volá metody synchronního rozhraní.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-520">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="7c5f7-521">Pokud jsou asynchronní i synchronní rozhraní implementovány v jedné třídě, je volána pouze asynchronní metoda.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-521">If both asynchronous and synchronous interfaces are implemented in one class, only the async method is called.</span></span> <span data-ttu-id="7c5f7-522">Při použití abstraktních tříd, jako <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> je například přepsání pouze synchronních metod nebo asynchronní metody pro každý typ filtru.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-522">When using abstract classes like <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> override only the synchronous methods or the async method for each filter type.</span></span>

### <a name="built-in-filter-attributes"></a><span data-ttu-id="7c5f7-523">Předdefinované atributy filtru</span><span class="sxs-lookup"><span data-stu-id="7c5f7-523">Built-in filter attributes</span></span>

<span data-ttu-id="7c5f7-524">ASP.NET Core obsahuje integrované filtry založené na atributech, které mohou být roztříděné a přizpůsobené.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-524">ASP.NET Core includes built-in attribute-based filters that can be subclassed and customized.</span></span> <span data-ttu-id="7c5f7-525">Například následující filtr výsledků přidá hlavičku do odpovědi:</span><span class="sxs-lookup"><span data-stu-id="7c5f7-525">For example, the following result filter adds a header to the response:</span></span>

<a name="add-header-attribute"></a>

[!code-csharp[](./filters/sample/FiltersSample/Filters/AddHeaderAttribute.cs?name=snippet)]

<span data-ttu-id="7c5f7-526">Atributy umožňují filtrům přijímat argumenty, jak je znázorněno v předchozím příkladu.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-526">Attributes allow filters to accept arguments, as shown in the preceding example.</span></span> <span data-ttu-id="7c5f7-527">Použijte `AddHeaderAttribute` pro metodu Controller nebo Action a zadejte název a hodnotu HLAVIČKY http:</span><span class="sxs-lookup"><span data-stu-id="7c5f7-527">Apply the `AddHeaderAttribute` to a controller or action method and specify the name and value of the HTTP header:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

<!-- `https://localhost:5001/Sample` -->

<span data-ttu-id="7c5f7-528">Některé z rozhraní filtru mají odpovídající atributy, které lze použít jako základní třídy pro vlastní implementace.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-528">Several of the filter interfaces have corresponding attributes that can be used as base classes for custom implementations.</span></span>

<span data-ttu-id="7c5f7-529">Atributy filtru:</span><span class="sxs-lookup"><span data-stu-id="7c5f7-529">Filter attributes:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ExceptionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>

## <a name="filter-scopes-and-order-of-execution"></a><span data-ttu-id="7c5f7-530">Filtrovat obory a pořadí provádění</span><span class="sxs-lookup"><span data-stu-id="7c5f7-530">Filter scopes and order of execution</span></span>

<span data-ttu-id="7c5f7-531">Filtr lze přidat do kanálu v jednom ze tří *oborů*:</span><span class="sxs-lookup"><span data-stu-id="7c5f7-531">A filter can be added to the pipeline at one of three *scopes*:</span></span>

* <span data-ttu-id="7c5f7-532">Použití atributu pro akci.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-532">Using an attribute on an action.</span></span>
* <span data-ttu-id="7c5f7-533">Použití atributu na řadiči.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-533">Using an attribute on a controller.</span></span>
* <span data-ttu-id="7c5f7-534">Globálně pro všechny řadiče a akce, jak je znázorněno v následujícím kódu:</span><span class="sxs-lookup"><span data-stu-id="7c5f7-534">Globally for all controllers and actions as shown in the following code:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/StartupGF.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="7c5f7-535">Předchozí kód přičítá tři filtry globálně pomocí kolekce [MvcOptions. filters](xref:Microsoft.AspNetCore.Mvc.MvcOptions.Filters) .</span><span class="sxs-lookup"><span data-stu-id="7c5f7-535">The preceding code adds three filters globally using the [MvcOptions.Filters](xref:Microsoft.AspNetCore.Mvc.MvcOptions.Filters) collection.</span></span>

### <a name="default-order-of-execution"></a><span data-ttu-id="7c5f7-536">Výchozí pořadí provádění</span><span class="sxs-lookup"><span data-stu-id="7c5f7-536">Default order of execution</span></span>

<span data-ttu-id="7c5f7-537">Pokud existuje více filtrů *stejného typu*, určuje obor výchozí pořadí provádění filtru.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-537">When there are multiple filters *of the same type*, scope determines the default order of filter execution.</span></span>  <span data-ttu-id="7c5f7-538">Globální filtry filtr tříd Surround.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-538">Global filters surround class filters.</span></span> <span data-ttu-id="7c5f7-539">Filtry tříd obklopují filtry metod Surround.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-539">Class filters surround method filters.</span></span>

<span data-ttu-id="7c5f7-540">V důsledku vnořování filtru je *po* kódu spuštěn v obráceném pořadí *před* kódem.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-540">As a result of filter nesting, the *after* code of filters runs in the reverse order of the *before* code.</span></span> <span data-ttu-id="7c5f7-541">Pořadí filtru:</span><span class="sxs-lookup"><span data-stu-id="7c5f7-541">The filter sequence:</span></span>

* <span data-ttu-id="7c5f7-542">*Před* kódem globálních filtrů.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-542">The *before* code of global filters.</span></span>
  * <span data-ttu-id="7c5f7-543">*Před* kódem filtru kontroleru.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-543">The *before* code of controller filters.</span></span>
    * <span data-ttu-id="7c5f7-544">Kód *před* filtry metody Action.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-544">The *before* code of action method filters.</span></span>
    * <span data-ttu-id="7c5f7-545">*Po* kódu filtry metody Action.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-545">The *after* code of action method filters.</span></span>
  * <span data-ttu-id="7c5f7-546">*Po* kódu filtry kontroleru.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-546">The *after* code of controller filters.</span></span>
* <span data-ttu-id="7c5f7-547">*Po* kódu globálních filtrů.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-547">The *after* code of global filters.</span></span>
  
<span data-ttu-id="7c5f7-548">Následující příklad ilustruje pořadí, ve kterém jsou metody filtru volány pro filtry synchronních akcí.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-548">The following example that illustrates the order in which filter methods are called for synchronous action filters.</span></span>

| <span data-ttu-id="7c5f7-549">Sequence</span><span class="sxs-lookup"><span data-stu-id="7c5f7-549">Sequence</span></span> | <span data-ttu-id="7c5f7-550">Rozsah filtru</span><span class="sxs-lookup"><span data-stu-id="7c5f7-550">Filter scope</span></span> | <span data-ttu-id="7c5f7-551">Filter – metoda</span><span class="sxs-lookup"><span data-stu-id="7c5f7-551">Filter method</span></span> |
|:--------:|:------------:|:-------------:|
| <span data-ttu-id="7c5f7-552">1</span><span class="sxs-lookup"><span data-stu-id="7c5f7-552">1</span></span> | <span data-ttu-id="7c5f7-553">Globální</span><span class="sxs-lookup"><span data-stu-id="7c5f7-553">Global</span></span> | `OnActionExecuting` |
| <span data-ttu-id="7c5f7-554">2</span><span class="sxs-lookup"><span data-stu-id="7c5f7-554">2</span></span> | <span data-ttu-id="7c5f7-555">Kontrolér</span><span class="sxs-lookup"><span data-stu-id="7c5f7-555">Controller</span></span> | `OnActionExecuting` |
| <span data-ttu-id="7c5f7-556">3</span><span class="sxs-lookup"><span data-stu-id="7c5f7-556">3</span></span> | <span data-ttu-id="7c5f7-557">Metoda</span><span class="sxs-lookup"><span data-stu-id="7c5f7-557">Method</span></span> | `OnActionExecuting` |
| <span data-ttu-id="7c5f7-558">4</span><span class="sxs-lookup"><span data-stu-id="7c5f7-558">4</span></span> | <span data-ttu-id="7c5f7-559">Metoda</span><span class="sxs-lookup"><span data-stu-id="7c5f7-559">Method</span></span> | `OnActionExecuted` |
| <span data-ttu-id="7c5f7-560">5</span><span class="sxs-lookup"><span data-stu-id="7c5f7-560">5</span></span> | <span data-ttu-id="7c5f7-561">Kontrolér</span><span class="sxs-lookup"><span data-stu-id="7c5f7-561">Controller</span></span> | `OnActionExecuted` |
| <span data-ttu-id="7c5f7-562">6</span><span class="sxs-lookup"><span data-stu-id="7c5f7-562">6</span></span> | <span data-ttu-id="7c5f7-563">Globální</span><span class="sxs-lookup"><span data-stu-id="7c5f7-563">Global</span></span> | `OnActionExecuted` |

<span data-ttu-id="7c5f7-564">Tato posloupnost zobrazuje:</span><span class="sxs-lookup"><span data-stu-id="7c5f7-564">This sequence shows:</span></span>

* <span data-ttu-id="7c5f7-565">Filtr metod je vnořený do filtru kontroleru.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-565">The method filter is nested within the controller filter.</span></span>
* <span data-ttu-id="7c5f7-566">Filtr kontroleru je vnořený do globálního filtru.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-566">The controller filter is nested within the global filter.</span></span>

### <a name="controller-and-razor-page-level-filters"></a><span data-ttu-id="7c5f7-567">Filtry na Razor úrovni řadiče a stránky</span><span class="sxs-lookup"><span data-stu-id="7c5f7-567">Controller and Razor Page level filters</span></span>

<span data-ttu-id="7c5f7-568">Každý kontroler, který dědí ze <xref:Microsoft.AspNetCore.Mvc.Controller> základní třídy, zahrnuje metody [Controller. OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*), [Controller. OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*)a [Controller. OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*) 
 `OnActionExecuted` .</span><span class="sxs-lookup"><span data-stu-id="7c5f7-568">Every controller that inherits from the <xref:Microsoft.AspNetCore.Mvc.Controller> base class includes [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*),  [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*), and [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted` methods.</span></span> <span data-ttu-id="7c5f7-569">Tyto metody:</span><span class="sxs-lookup"><span data-stu-id="7c5f7-569">These methods:</span></span>

* <span data-ttu-id="7c5f7-570">Zabalte filtry, které se spouštějí pro danou akci.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-570">Wrap the filters that run for a given action.</span></span>
* <span data-ttu-id="7c5f7-571">`OnActionExecuting`je volána před jakýmkoli filtrem akce.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-571">`OnActionExecuting` is called before any of the action's filters.</span></span>
* <span data-ttu-id="7c5f7-572">`OnActionExecuted`se volá po všech filtrech akcí.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-572">`OnActionExecuted` is called after all of the action filters.</span></span>
* <span data-ttu-id="7c5f7-573">`OnActionExecutionAsync`je volána před jakýmkoli filtrem akce.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-573">`OnActionExecutionAsync` is called before any of the action's filters.</span></span> <span data-ttu-id="7c5f7-574">Kód v filtru po `next` spuštění po metodě Action.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-574">Code in the filter after `next` runs after the action method.</span></span>

<span data-ttu-id="7c5f7-575">Například v ukázce ke stažení `MySampleActionFilter` se použije globálně při spuštění.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-575">For example, in the download sample, `MySampleActionFilter` is applied globally in startup.</span></span>

<span data-ttu-id="7c5f7-576">`TestController`:</span><span class="sxs-lookup"><span data-stu-id="7c5f7-576">The `TestController`:</span></span>

* <span data-ttu-id="7c5f7-577">Aplikuje `SampleActionFilterAttribute` ( `[SampleActionFilter]` ) na `FilterTest2` akci.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-577">Applies the `SampleActionFilterAttribute` (`[SampleActionFilter]`) to the `FilterTest2` action.</span></span>
* <span data-ttu-id="7c5f7-578">Přepisuje `OnActionExecuting` a `OnActionExecuted` .</span><span class="sxs-lookup"><span data-stu-id="7c5f7-578">Overrides `OnActionExecuting` and `OnActionExecuted`.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/TestController.cs?name=snippet)]

<span data-ttu-id="7c5f7-579">Navigace pro `https://localhost:5001/Test/FilterTest2` spustí následující kód:</span><span class="sxs-lookup"><span data-stu-id="7c5f7-579">Navigating to `https://localhost:5001/Test/FilterTest2` runs the following code:</span></span>

* `TestController.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `SampleActionFilterAttribute.OnActionExecuting`
      * `TestController.FilterTest2`
    * `SampleActionFilterAttribute.OnActionExecuted`
  * `MySampleActionFilter.OnActionExecuted`
* `TestController.OnActionExecuted`

<span data-ttu-id="7c5f7-580">RazorInformace o stránkách naleznete v tématu [implementace Razor filtrů stránek pomocí přepsání metod filtru](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span><span class="sxs-lookup"><span data-stu-id="7c5f7-580">For Razor Pages, see [Implement Razor Page filters by overriding filter methods](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span></span>

### <a name="overriding-the-default-order"></a><span data-ttu-id="7c5f7-581">Přepsání výchozího pořadí</span><span class="sxs-lookup"><span data-stu-id="7c5f7-581">Overriding the default order</span></span>

<span data-ttu-id="7c5f7-582">Výchozí sekvenci spuštění lze přepsat implementací <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter> .</span><span class="sxs-lookup"><span data-stu-id="7c5f7-582">The default sequence of execution can be overridden by implementing <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span></span> <span data-ttu-id="7c5f7-583">`IOrderedFilter`zpřístupňuje <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> vlastnost, která má přednost před rozsahem, aby určila pořadí provádění.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-583">`IOrderedFilter` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> property that takes precedence over scope to determine the order of execution.</span></span> <span data-ttu-id="7c5f7-584">Filtr s nižší `Order` hodnotou:</span><span class="sxs-lookup"><span data-stu-id="7c5f7-584">A filter with a lower `Order` value:</span></span>

* <span data-ttu-id="7c5f7-585">Spustí *před* kódem filtr s vyšší hodnotou `Order` .</span><span class="sxs-lookup"><span data-stu-id="7c5f7-585">Runs the *before* code before that of a filter with a higher value of `Order`.</span></span>
* <span data-ttu-id="7c5f7-586">Spustí *za* kódem za filtr s vyšší `Order` hodnotou.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-586">Runs the *after* code after that of a filter with a higher `Order` value.</span></span>

<span data-ttu-id="7c5f7-587">`Order`Vlastnost lze nastavit s parametrem konstruktoru:</span><span class="sxs-lookup"><span data-stu-id="7c5f7-587">The `Order` property can be set with a constructor parameter:</span></span>

```csharp
[MyFilter(Name = "Controller Level Attribute", Order=1)]
```

<span data-ttu-id="7c5f7-588">Vezměte v úvahu stejné 3 filtry akcí, které jsou uvedené v předchozím příkladu.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-588">Consider the same 3 action filters shown in the preceding example.</span></span> <span data-ttu-id="7c5f7-589">Pokud `Order` je vlastnost Controller a globální filtry nastavená na 1 a 2 v uvedeném pořadí, pořadí spouštění se vrátí zpět.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-589">If the `Order` property of the controller and global filters is set to 1 and 2 respectively, the order of execution is reversed.</span></span>

| <span data-ttu-id="7c5f7-590">Sequence</span><span class="sxs-lookup"><span data-stu-id="7c5f7-590">Sequence</span></span> | <span data-ttu-id="7c5f7-591">Rozsah filtru</span><span class="sxs-lookup"><span data-stu-id="7c5f7-591">Filter scope</span></span> | <span data-ttu-id="7c5f7-592">`Order`majetek</span><span class="sxs-lookup"><span data-stu-id="7c5f7-592">`Order` property</span></span> | <span data-ttu-id="7c5f7-593">Filter – metoda</span><span class="sxs-lookup"><span data-stu-id="7c5f7-593">Filter method</span></span> |
|:--------:|:------------:|:-----------------:|:-------------:|
| <span data-ttu-id="7c5f7-594">1</span><span class="sxs-lookup"><span data-stu-id="7c5f7-594">1</span></span> | <span data-ttu-id="7c5f7-595">Metoda</span><span class="sxs-lookup"><span data-stu-id="7c5f7-595">Method</span></span> | <span data-ttu-id="7c5f7-596">0</span><span class="sxs-lookup"><span data-stu-id="7c5f7-596">0</span></span> | `OnActionExecuting` |
| <span data-ttu-id="7c5f7-597">2</span><span class="sxs-lookup"><span data-stu-id="7c5f7-597">2</span></span> | <span data-ttu-id="7c5f7-598">Kontrolér</span><span class="sxs-lookup"><span data-stu-id="7c5f7-598">Controller</span></span> | <span data-ttu-id="7c5f7-599">1</span><span class="sxs-lookup"><span data-stu-id="7c5f7-599">1</span></span>  | `OnActionExecuting` |
| <span data-ttu-id="7c5f7-600">3</span><span class="sxs-lookup"><span data-stu-id="7c5f7-600">3</span></span> | <span data-ttu-id="7c5f7-601">Globální</span><span class="sxs-lookup"><span data-stu-id="7c5f7-601">Global</span></span> | <span data-ttu-id="7c5f7-602">2</span><span class="sxs-lookup"><span data-stu-id="7c5f7-602">2</span></span>  | `OnActionExecuting` |
| <span data-ttu-id="7c5f7-603">4</span><span class="sxs-lookup"><span data-stu-id="7c5f7-603">4</span></span> | <span data-ttu-id="7c5f7-604">Globální</span><span class="sxs-lookup"><span data-stu-id="7c5f7-604">Global</span></span> | <span data-ttu-id="7c5f7-605">2</span><span class="sxs-lookup"><span data-stu-id="7c5f7-605">2</span></span>  | `OnActionExecuted` |
| <span data-ttu-id="7c5f7-606">5</span><span class="sxs-lookup"><span data-stu-id="7c5f7-606">5</span></span> | <span data-ttu-id="7c5f7-607">Kontrolér</span><span class="sxs-lookup"><span data-stu-id="7c5f7-607">Controller</span></span> | <span data-ttu-id="7c5f7-608">1</span><span class="sxs-lookup"><span data-stu-id="7c5f7-608">1</span></span>  | `OnActionExecuted` |
| <span data-ttu-id="7c5f7-609">6</span><span class="sxs-lookup"><span data-stu-id="7c5f7-609">6</span></span> | <span data-ttu-id="7c5f7-610">Metoda</span><span class="sxs-lookup"><span data-stu-id="7c5f7-610">Method</span></span> | <span data-ttu-id="7c5f7-611">0</span><span class="sxs-lookup"><span data-stu-id="7c5f7-611">0</span></span>  | `OnActionExecuted` |

<span data-ttu-id="7c5f7-612">`Order`Vlastnost Přepisuje obor při určování pořadí, ve kterém jsou filtry spouštěny.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-612">The `Order` property overrides scope when determining the order in which filters run.</span></span> <span data-ttu-id="7c5f7-613">Filtry jsou seřazené podle pořadí, pak se k přerušení vztahů používá obor.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-613">Filters are sorted first by order, then scope is used to break ties.</span></span> <span data-ttu-id="7c5f7-614">Všechny předdefinované filtry implementují `IOrderedFilter` a nastaví výchozí `Order` hodnotu na 0.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-614">All of the built-in filters implement `IOrderedFilter` and set the default `Order` value to 0.</span></span> <span data-ttu-id="7c5f7-615">Pro předdefinované filtry rozsah určuje pořadí, pokud `Order` není nastaven na nenulovou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-615">For built-in filters, scope determines order unless `Order` is set to a non-zero value.</span></span>

## <a name="cancellation-and-short-circuiting"></a><span data-ttu-id="7c5f7-616">Zrušení a zkrácení okruhů</span><span class="sxs-lookup"><span data-stu-id="7c5f7-616">Cancellation and short-circuiting</span></span>

<span data-ttu-id="7c5f7-617">Kanál filtru může být v krátkém okruhu nastavením <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> vlastnosti pro <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> parametr poskytnutý metodě Filter.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-617">The filter pipeline can be short-circuited by setting the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> property on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> parameter provided to the filter method.</span></span> <span data-ttu-id="7c5f7-618">Například následující filtr prostředků zabrání ve spuštění zbývajícího kanálu:</span><span class="sxs-lookup"><span data-stu-id="7c5f7-618">For instance, the following Resource filter prevents the rest of the pipeline from executing:</span></span>

<a name="short-circuiting-resource-filter"></a>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ShortCircuitingResourceFilterAttribute.cs?name=snippet)]

<span data-ttu-id="7c5f7-619">V následujícím kódu, `ShortCircuitingResourceFilter` a jako `AddHeader` cíl filtru i cílovou `SomeResource` metodu Action.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-619">In the following code, both the `ShortCircuitingResourceFilter` and the `AddHeader` filter target the `SomeResource` action method.</span></span> <span data-ttu-id="7c5f7-620">`ShortCircuitingResourceFilter`:</span><span class="sxs-lookup"><span data-stu-id="7c5f7-620">The `ShortCircuitingResourceFilter`:</span></span>

* <span data-ttu-id="7c5f7-621">Nejprve se spustí, protože se jedná o filtr prostředků a `AddHeader` je filtr akcí.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-621">Runs first, because it's a Resource Filter and `AddHeader` is an Action Filter.</span></span>
* <span data-ttu-id="7c5f7-622">Krátké okruhy zbývajícího kanálu.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-622">Short-circuits the rest of the pipeline.</span></span>

<span data-ttu-id="7c5f7-623">Proto `AddHeader` Filtr pro akci nikdy neběží `SomeResource` .</span><span class="sxs-lookup"><span data-stu-id="7c5f7-623">Therefore the `AddHeader` filter never runs for the `SomeResource` action.</span></span> <span data-ttu-id="7c5f7-624">Toto chování by bylo stejné, pokud byly oba filtry aplikovány na úrovni metody akce, a to za předpokladu, že byla `ShortCircuitingResourceFilter` spuštěna jako první.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-624">This behavior would be the same if both filters were applied at the action method level, provided the `ShortCircuitingResourceFilter` ran first.</span></span> <span data-ttu-id="7c5f7-625">`ShortCircuitingResourceFilter`Nejprve se spustí z důvodu jeho typu filtru nebo explicitního použití `Order` Vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-625">The `ShortCircuitingResourceFilter` runs first because of its filter type, or by explicit use of `Order` property.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1,9)]

## <a name="dependency-injection"></a><span data-ttu-id="7c5f7-626">Injektáž závislosti</span><span class="sxs-lookup"><span data-stu-id="7c5f7-626">Dependency injection</span></span>

<span data-ttu-id="7c5f7-627">Filtry lze přidat podle typu nebo podle instance.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-627">Filters can be added by type or by instance.</span></span> <span data-ttu-id="7c5f7-628">Pokud je přidána instance, bude tato instance použita pro každý požadavek.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-628">If an instance is added, that instance is used for every request.</span></span> <span data-ttu-id="7c5f7-629">Pokud je přidán typ, je aktivován typ.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-629">If a type is added, it's type-activated.</span></span> <span data-ttu-id="7c5f7-630">Filtr aktivovaný typu znamená:</span><span class="sxs-lookup"><span data-stu-id="7c5f7-630">A type-activated filter means:</span></span>

* <span data-ttu-id="7c5f7-631">Instance se vytvoří pro každý požadavek.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-631">An instance is created for each request.</span></span>
* <span data-ttu-id="7c5f7-632">Jakékoli závislosti konstruktoru jsou vyplněny pomocí [Injektáže závislosti](xref:fundamentals/dependency-injection) (di).</span><span class="sxs-lookup"><span data-stu-id="7c5f7-632">Any constructor dependencies are populated by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span>

<span data-ttu-id="7c5f7-633">Filtry, které jsou implementovány jako atributy a přidány přímo do tříd kontroleru nebo metody akcí, nemohou mít závislosti konstruktoru poskytované [vkládáním závislostí](xref:fundamentals/dependency-injection) (di).</span><span class="sxs-lookup"><span data-stu-id="7c5f7-633">Filters that are implemented as attributes and added directly to controller classes or action methods cannot have constructor dependencies provided by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span> <span data-ttu-id="7c5f7-634">Pomocí DI nelze poskytnout závislosti konstruktoru:</span><span class="sxs-lookup"><span data-stu-id="7c5f7-634">Constructor dependencies cannot be provided by DI because:</span></span>

* <span data-ttu-id="7c5f7-635">V atributech musí být zadány parametry konstruktoru, kde jsou aplikovány.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-635">Attributes must have their constructor parameters supplied where they're applied.</span></span> 
* <span data-ttu-id="7c5f7-636">Toto je omezení způsobu fungování atributů.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-636">This is a limitation of how attributes work.</span></span>

<span data-ttu-id="7c5f7-637">Následující filtry podporují závislosti konstruktoru poskytované od DI:</span><span class="sxs-lookup"><span data-stu-id="7c5f7-637">The following filters support constructor dependencies provided from DI:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>
* <span data-ttu-id="7c5f7-638"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>implementováno u atributu.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-638"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implemented on the attribute.</span></span>

<span data-ttu-id="7c5f7-639">Předchozí filtry lze použít pro metodu kontroleru nebo akce:</span><span class="sxs-lookup"><span data-stu-id="7c5f7-639">The preceding filters can be applied to a controller or action method:</span></span>

<span data-ttu-id="7c5f7-640">Protokolovací nástroje jsou k dispozici z DI.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-640">Loggers are available from DI.</span></span> <span data-ttu-id="7c5f7-641">Ale nevytvářejte a používejte filtry čistě pro účely protokolování.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-641">However, avoid creating and using filters purely for logging purposes.</span></span> <span data-ttu-id="7c5f7-642">[Integrované protokolování rozhraní](xref:fundamentals/logging/index) obvykle zajišťuje, co je potřeba pro protokolování.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-642">The [built-in framework logging](xref:fundamentals/logging/index) typically provides what's needed for logging.</span></span> <span data-ttu-id="7c5f7-643">Protokolování přidáno do filtrů:</span><span class="sxs-lookup"><span data-stu-id="7c5f7-643">Logging added to filters:</span></span>

* <span data-ttu-id="7c5f7-644">Měl by se soustředit na obavy a chování obchodních domén, které jsou specifické pro filtr.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-644">Should focus on business domain concerns or behavior specific to the filter.</span></span>
* <span data-ttu-id="7c5f7-645">Neměli **byste** protokolovat akce nebo jiné události rozhraní.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-645">Should **not** log actions or other framework events.</span></span> <span data-ttu-id="7c5f7-646">Vestavěné filtry protokolují akce a události rozhraní.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-646">The built in filters log actions and framework events.</span></span>

### <a name="servicefilterattribute"></a><span data-ttu-id="7c5f7-647">ServiceFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="7c5f7-647">ServiceFilterAttribute</span></span>

<span data-ttu-id="7c5f7-648">Typy implementace filtru služby jsou zaregistrované v `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="7c5f7-648">Service filter implementation types are registered in `ConfigureServices`.</span></span> <span data-ttu-id="7c5f7-649"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>Načte instanci filtru z di.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-649">A <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> retrieves an instance of the filter from DI.</span></span>

<span data-ttu-id="7c5f7-650">Následující kód ukazuje `AddHeaderResultServiceFilter` :</span><span class="sxs-lookup"><span data-stu-id="7c5f7-650">The following code shows the `AddHeaderResultServiceFilter`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="7c5f7-651">V následujícím kódu `AddHeaderResultServiceFilter` je přidán do kontejneru di:</span><span class="sxs-lookup"><span data-stu-id="7c5f7-651">In the following code, `AddHeaderResultServiceFilter` is added to the DI container:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

<span data-ttu-id="7c5f7-652">V následujícím kódu `ServiceFilter` atribut načte instanci `AddHeaderResultServiceFilter` filtru z di:</span><span class="sxs-lookup"><span data-stu-id="7c5f7-652">In the following code, the `ServiceFilter` attribute retrieves an instance of the `AddHeaderResultServiceFilter` filter from DI:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_ServiceFilter&highlight=1)]

<span data-ttu-id="7c5f7-653">Při použití `ServiceFilterAttribute` nastavte [ServiceFilterAttribute. IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span><span class="sxs-lookup"><span data-stu-id="7c5f7-653">When using `ServiceFilterAttribute`, setting [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span></span>

* <span data-ttu-id="7c5f7-654">Poskytuje nápovědu, že se instance filtru *dá* znovu použít mimo rozsah požadavku, který byl vytvořen v rámci.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-654">Provides a hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="7c5f7-655">Modul runtime ASP.NET Core nezaručuje:</span><span class="sxs-lookup"><span data-stu-id="7c5f7-655">The ASP.NET Core runtime doesn't guarantee:</span></span>

  * <span data-ttu-id="7c5f7-656">Vytvoří se jedna instance filtru.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-656">That a single instance of the filter will be created.</span></span>
  * <span data-ttu-id="7c5f7-657">Filtr nebude znovu vyžádán z kontejneru DI v pozdějším bodě.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-657">The filter will not be re-requested from the DI container at some later point.</span></span>

* <span data-ttu-id="7c5f7-658">Neměl by se používat s filtrem, který závisí na službách s jinou životností než singleton.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-658">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

 <span data-ttu-id="7c5f7-659"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>implementuje <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> .</span><span class="sxs-lookup"><span data-stu-id="7c5f7-659"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="7c5f7-660">`IFilterFactory`zpřístupňuje <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> metodu pro vytvoření <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-660">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="7c5f7-661">`CreateInstance`Načte zadaný typ z DI.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-661">`CreateInstance` loads the specified type from DI.</span></span>

### <a name="typefilterattribute"></a><span data-ttu-id="7c5f7-662">TypeFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="7c5f7-662">TypeFilterAttribute</span></span>

<span data-ttu-id="7c5f7-663"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>je podobný <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> , ale jeho typ není vyřešen přímo z kontejneru di.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-663"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> is similar to <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, but its type isn't resolved directly from the DI container.</span></span> <span data-ttu-id="7c5f7-664">Vytvoří instanci typu pomocí <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName> .</span><span class="sxs-lookup"><span data-stu-id="7c5f7-664">It instantiates the type by using <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span></span>

<span data-ttu-id="7c5f7-665">Protože `TypeFilterAttribute` typy nejsou vyřešeny přímo z kontejneru di:</span><span class="sxs-lookup"><span data-stu-id="7c5f7-665">Because `TypeFilterAttribute` types aren't resolved directly from the DI container:</span></span>

* <span data-ttu-id="7c5f7-666">Typy, na které se odkazuje pomocí, `TypeFilterAttribute` není nutné registrovat v kontejneru di.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-666">Types that are referenced using the `TypeFilterAttribute` don't need to be registered with the DI container.</span></span>  <span data-ttu-id="7c5f7-667">Mají své závislosti splněné kontejnerem DI.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-667">They do have their dependencies fulfilled by the DI container.</span></span>
* <span data-ttu-id="7c5f7-668">`TypeFilterAttribute`může volitelně přijmout argumenty konstruktoru pro typ.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-668">`TypeFilterAttribute` can optionally accept constructor arguments for the type.</span></span>

<span data-ttu-id="7c5f7-669">Při použití `TypeFilterAttribute` nastavte [TypeFilterAttribute. IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span><span class="sxs-lookup"><span data-stu-id="7c5f7-669">When using `TypeFilterAttribute`, setting [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span></span>
* <span data-ttu-id="7c5f7-670">Poskytuje nápovědu, že se instance filtru *dá* znovu použít mimo rozsah požadavku, který byl vytvořen v rámci.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-670">Provides hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="7c5f7-671">Modul runtime ASP.NET Core neposkytuje žádné záruky, že se vytvoří jediná instance filtru.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-671">The ASP.NET Core runtime provides no guarantees that a single instance of the filter will be created.</span></span>

* <span data-ttu-id="7c5f7-672">Neměl by se používat s filtrem, který závisí na službách s jinou životností než singleton.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-672">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

<span data-ttu-id="7c5f7-673">Následující příklad ukazuje, jak předat argumenty typu pomocí `TypeFilterAttribute` :</span><span class="sxs-lookup"><span data-stu-id="7c5f7-673">The following example shows how to pass arguments to a type using `TypeFilterAttribute`:</span></span>

[!code-csharp[](../../mvc/controllers/filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_TypeFilter&highlight=1,2)]
[!code-csharp[](../../mvc/controllers/filters/sample/FiltersSample/Filters/LogConstantFilter.cs?name=snippet_TypeFilter_Implementation&highlight=6)]

<!-- 
https://localhost:5001/home/hi?name=joe
VS debug window shows 
FiltersSample.Filters.LogConstantFilter:Information: Method 'Hi' called
-->

## <a name="authorization-filters"></a><span data-ttu-id="7c5f7-674">Filtry autorizace</span><span class="sxs-lookup"><span data-stu-id="7c5f7-674">Authorization filters</span></span>

<span data-ttu-id="7c5f7-675">Filtry autorizace:</span><span class="sxs-lookup"><span data-stu-id="7c5f7-675">Authorization filters:</span></span>

* <span data-ttu-id="7c5f7-676">Jsou první filtry spouštěny v kanálu filtru.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-676">Are the first filters run in the filter pipeline.</span></span>
* <span data-ttu-id="7c5f7-677">Řízení přístupu k metodám akcí.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-677">Control access to action methods.</span></span>
* <span data-ttu-id="7c5f7-678">Před metodou, ale ne po metodě.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-678">Have a before method, but no after method.</span></span>

<span data-ttu-id="7c5f7-679">Vlastní autorizační filtry vyžadují vlastní autorizační rozhraní.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-679">Custom authorization filters require a custom authorization framework.</span></span> <span data-ttu-id="7c5f7-680">Preferovat konfiguraci autorizačních zásad nebo psaní vlastních zásad autorizace při psaní vlastního filtru.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-680">Prefer configuring the authorization policies or writing a custom authorization policy over writing a custom filter.</span></span> <span data-ttu-id="7c5f7-681">Vestavěný autorizační filtr:</span><span class="sxs-lookup"><span data-stu-id="7c5f7-681">The built-in authorization filter:</span></span>

* <span data-ttu-id="7c5f7-682">Volá autorizační systém.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-682">Calls the authorization system.</span></span>
* <span data-ttu-id="7c5f7-683">Neautorizuje požadavky.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-683">Does not authorize requests.</span></span>

<span data-ttu-id="7c5f7-684">Negenerovat **výjimky** v rámci autorizačních filtrů:</span><span class="sxs-lookup"><span data-stu-id="7c5f7-684">Do **not** throw exceptions within authorization filters:</span></span>

* <span data-ttu-id="7c5f7-685">Výjimka nebude zpracována.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-685">The exception will not be handled.</span></span>
* <span data-ttu-id="7c5f7-686">Filtry výjimek nebudou zpracovávat výjimku.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-686">Exception filters will not handle the exception.</span></span>

<span data-ttu-id="7c5f7-687">Zvažte vydání výzvy, pokud dojde k výjimce ve autorizačním filtru.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-687">Consider issuing a challenge when an exception occurs in an authorization filter.</span></span>

<span data-ttu-id="7c5f7-688">Přečtěte si další informace o [autorizaci](xref:security/authorization/introduction).</span><span class="sxs-lookup"><span data-stu-id="7c5f7-688">Learn more about [Authorization](xref:security/authorization/introduction).</span></span>

## <a name="resource-filters"></a><span data-ttu-id="7c5f7-689">Filtry prostředků</span><span class="sxs-lookup"><span data-stu-id="7c5f7-689">Resource filters</span></span>

<span data-ttu-id="7c5f7-690">Filtry prostředků:</span><span class="sxs-lookup"><span data-stu-id="7c5f7-690">Resource filters:</span></span>

* <span data-ttu-id="7c5f7-691">Implementujte <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> rozhraní nebo <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> .</span><span class="sxs-lookup"><span data-stu-id="7c5f7-691">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> interface.</span></span>
* <span data-ttu-id="7c5f7-692">Provádění zabalí většinu kanálu filtru.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-692">Execution wraps most of the filter pipeline.</span></span>
* <span data-ttu-id="7c5f7-693">Před filtry prostředků se spouštějí jenom [filtry autorizace](#authorization-filters) .</span><span class="sxs-lookup"><span data-stu-id="7c5f7-693">Only [Authorization filters](#authorization-filters) run before resource filters.</span></span>

<span data-ttu-id="7c5f7-694">Filtry prostředků jsou užitečné pro krátkodobé okruhy v kanálu.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-694">Resource filters are useful to short-circuit most of the pipeline.</span></span> <span data-ttu-id="7c5f7-695">Například filtr ukládání do mezipaměti může zabránit zbývajícímu kanálu v případě přístupů do mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-695">For example, a caching filter can avoid the rest of the pipeline on a cache hit.</span></span>

<span data-ttu-id="7c5f7-696">Příklady filtru prostředků:</span><span class="sxs-lookup"><span data-stu-id="7c5f7-696">Resource filter examples:</span></span>

* <span data-ttu-id="7c5f7-697">[Filtr prostředků se zkrácenými okruhy](#short-circuiting-resource-filter) zobrazenými dříve.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-697">[The short-circuiting resource filter](#short-circuiting-resource-filter) shown previously.</span></span>
* <span data-ttu-id="7c5f7-698">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span><span class="sxs-lookup"><span data-stu-id="7c5f7-698">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span></span>

  * <span data-ttu-id="7c5f7-699">Zabraňuje vazbě modelu v přístupu k datům formuláře.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-699">Prevents model binding from accessing the form data.</span></span>
  * <span data-ttu-id="7c5f7-700">Používá se pro nahrávání velkých souborů, aby se zabránilo čtení dat z formuláře do paměti.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-700">Used for large file uploads to prevent the form data from being read into memory.</span></span>

## <a name="action-filters"></a><span data-ttu-id="7c5f7-701">Filtry akcí</span><span class="sxs-lookup"><span data-stu-id="7c5f7-701">Action filters</span></span>

> [!IMPORTANT]
> <span data-ttu-id="7c5f7-702">Filtry **akcí se nevztahují** na Razor stránky.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-702">Action filters do **not** apply to Razor Pages.</span></span> Razor<span data-ttu-id="7c5f7-703">Stránky podporují <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> a <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span><span class="sxs-lookup"><span data-stu-id="7c5f7-703"> Pages supports <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span></span> <span data-ttu-id="7c5f7-704">Další informace najdete v tématu [metody filtrování pro Razor stránky](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="7c5f7-704">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

<span data-ttu-id="7c5f7-705">Filtry akcí:</span><span class="sxs-lookup"><span data-stu-id="7c5f7-705">Action filters:</span></span>

* <span data-ttu-id="7c5f7-706">Implementujte <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> rozhraní nebo <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> .</span><span class="sxs-lookup"><span data-stu-id="7c5f7-706">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> interface.</span></span>
* <span data-ttu-id="7c5f7-707">Jejich spuštění obklopuje provádění metod akcí.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-707">Their execution surrounds the execution of action methods.</span></span>

<span data-ttu-id="7c5f7-708">Následující kód ukazuje filtr vzorové akce:</span><span class="sxs-lookup"><span data-stu-id="7c5f7-708">The following code shows a sample action filter:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="7c5f7-709"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext>Poskytuje následující vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="7c5f7-709">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> provides the following properties:</span></span>

* <span data-ttu-id="7c5f7-710"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments>– povolí čtení vstupů do metody akce.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-710"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> - enables the inputs to an action method be read.</span></span>
* <span data-ttu-id="7c5f7-711"><xref:Microsoft.AspNetCore.Mvc.Controller>– povolí manipulaci s instancí kontroleru.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-711"><xref:Microsoft.AspNetCore.Mvc.Controller> - enables manipulating the controller instance.</span></span>
* <span data-ttu-id="7c5f7-712"><xref:System.Web.Mvc.ActionExecutingContext.Result>– nastavení `Result` krátkodobého provádění metody akce a následných filtrů akcí.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-712"><xref:System.Web.Mvc.ActionExecutingContext.Result> - setting `Result` short-circuits execution of the action method and subsequent action filters.</span></span>

<span data-ttu-id="7c5f7-713">Vyvolání výjimky v metodě akce:</span><span class="sxs-lookup"><span data-stu-id="7c5f7-713">Throwing an exception in an action method:</span></span>

* <span data-ttu-id="7c5f7-714">Zabraňuje spuštění dalších filtrů.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-714">Prevents running of subsequent filters.</span></span>
* <span data-ttu-id="7c5f7-715">Na rozdíl od nastavení `Result` se místo úspěšného výsledku považuje za selhání.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-715">Unlike setting `Result`, is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="7c5f7-716"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext>Poskytuje `Controller` a `Result` plus následující vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="7c5f7-716">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> provides `Controller` and `Result` plus the following properties:</span></span>

* <span data-ttu-id="7c5f7-717"><xref:System.Web.Mvc.ActionExecutedContext.Canceled>-True, pokud bylo provedení akce zkráceno jiným filtrem.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-717"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> - True if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="7c5f7-718"><xref:System.Web.Mvc.ActionExecutedContext.Exception>-Hodnota není null, pokud akce nebo dříve vyvolala filtr akcí.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-718"><xref:System.Web.Mvc.ActionExecutedContext.Exception> - Non-null if the action or a previously run action filter threw an exception.</span></span> <span data-ttu-id="7c5f7-719">Nastavení této vlastnosti na hodnotu null:</span><span class="sxs-lookup"><span data-stu-id="7c5f7-719">Setting this property to null:</span></span>

  * <span data-ttu-id="7c5f7-720">Efektivně zpracovává výjimku.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-720">Effectively handles the exception.</span></span>
  * <span data-ttu-id="7c5f7-721">`Result`je spuštěn, jako kdyby byl vrácen z metody Action.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-721">`Result` is executed as if it was returned from the action method.</span></span>

<span data-ttu-id="7c5f7-722">V případě `IAsyncActionFilter` volání <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> :</span><span class="sxs-lookup"><span data-stu-id="7c5f7-722">For an `IAsyncActionFilter`, a call to the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span></span>

* <span data-ttu-id="7c5f7-723">Provede všechny následné filtry akcí a metodu Action.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-723">Executes any subsequent action filters and the action method.</span></span>
* <span data-ttu-id="7c5f7-724">Vrací objekt `ActionExecutedContext`.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-724">Returns `ActionExecutedContext`.</span></span>

<span data-ttu-id="7c5f7-725">Do krátkodobého okruhu, přiřaďte <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> k instanci výsledku a Nevolejte `next` (a `ActionExecutionDelegate` ).</span><span class="sxs-lookup"><span data-stu-id="7c5f7-725">To short-circuit, assign <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> to a result instance and don't call `next` (the `ActionExecutionDelegate`).</span></span>

<span data-ttu-id="7c5f7-726">Rozhraní poskytuje abstrakci <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> , která může být podtříd.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-726">The framework provides an abstract <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> that can be subclassed.</span></span>

<span data-ttu-id="7c5f7-727">`OnActionExecuting`Filtr akcí lze použít k těmto akcím:</span><span class="sxs-lookup"><span data-stu-id="7c5f7-727">The `OnActionExecuting` action filter can be used to:</span></span>

* <span data-ttu-id="7c5f7-728">Ověří stav modelu.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-728">Validate model state.</span></span>
* <span data-ttu-id="7c5f7-729">Pokud je stav neplatný, vrátí se chyba.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-729">Return an error if the state is invalid.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet)]

<span data-ttu-id="7c5f7-730">`OnActionExecuted`Metoda se spustí za metodou akce:</span><span class="sxs-lookup"><span data-stu-id="7c5f7-730">The `OnActionExecuted` method runs after the action method:</span></span>

* <span data-ttu-id="7c5f7-731">A mohou zobrazit výsledky akce a manipulovat s nimi prostřednictvím <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> Vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-731">And can see and manipulate the results of the action through the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> property.</span></span>
* <span data-ttu-id="7c5f7-732"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled>je nastaven na hodnotu true, pokud bylo provedení akce zkráceno jiným filtrem.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-732"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> is set to true if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="7c5f7-733"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception>je nastavena na hodnotu jinou než null, pokud akce nebo filtr následné akce vyvolaly výjimku.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-733"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> is set to a non-null value if the action or a subsequent action filter threw an exception.</span></span> <span data-ttu-id="7c5f7-734">Nastavení `Exception` na hodnotu null:</span><span class="sxs-lookup"><span data-stu-id="7c5f7-734">Setting `Exception` to null:</span></span>

  * <span data-ttu-id="7c5f7-735">Efektivně zpracovává výjimku.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-735">Effectively handles an exception.</span></span>
  * <span data-ttu-id="7c5f7-736">`ActionExecutedContext.Result`je spuštěn, jako kdyby byl vrácen normálně z metody Action.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-736">`ActionExecutedContext.Result` is executed as if it were returned normally from the action method.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet2&higlight=12-99)]

## <a name="exception-filters"></a><span data-ttu-id="7c5f7-737">Filtry výjimek</span><span class="sxs-lookup"><span data-stu-id="7c5f7-737">Exception filters</span></span>

<span data-ttu-id="7c5f7-738">Filtry výjimek:</span><span class="sxs-lookup"><span data-stu-id="7c5f7-738">Exception filters:</span></span>

* <span data-ttu-id="7c5f7-739">Implementujte <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> nebo <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter> .</span><span class="sxs-lookup"><span data-stu-id="7c5f7-739">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span></span> 
* <span data-ttu-id="7c5f7-740">Dá se použít k implementaci běžných zásad zpracování chyb.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-740">Can be used to implement common error handling policies.</span></span>

<span data-ttu-id="7c5f7-741">Následující ukázkový filtr výjimek používá vlastní zobrazení chyb pro zobrazení podrobností o výjimkách, ke kterým dochází při vývoji aplikace:</span><span class="sxs-lookup"><span data-stu-id="7c5f7-741">The following sample exception filter uses a custom error view to display details about exceptions that occur when the app is in development:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/CustomExceptionFilter.cs?name=snippet_ExceptionFilter&highlight=16-19)]

<span data-ttu-id="7c5f7-742">Filtry výjimek:</span><span class="sxs-lookup"><span data-stu-id="7c5f7-742">Exception filters:</span></span>

* <span data-ttu-id="7c5f7-743">Nemusíte mít události před a po.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-743">Don't have before and after events.</span></span>
* <span data-ttu-id="7c5f7-744">Implementujte <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> nebo <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*> .</span><span class="sxs-lookup"><span data-stu-id="7c5f7-744">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span></span>
* <span data-ttu-id="7c5f7-745">Zpracování neošetřených výjimek, ke kterým dochází při Razor vytváření stránky nebo kontroleru, [vázání modelů](xref:mvc/models/model-binding), filtrů akcí nebo metod akcí.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-745">Handle unhandled exceptions that occur in Razor Page or controller creation, [model binding](xref:mvc/models/model-binding), action filters, or action methods.</span></span>
* <span data-ttu-id="7c5f7-746">**Nezachycujte** výjimky, ke kterým dochází v filtrech prostředků, výsledných filtrech nebo v důsledku provádění výsledků MVC.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-746">Do **not** catch exceptions that occur in resource filters, result filters, or MVC result execution.</span></span>

<span data-ttu-id="7c5f7-747">Chcete-li zpracovat výjimku, nastavte <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> vlastnost na `true` nebo zapište odpověď.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-747">To handle an exception, set the <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> property to `true` or write a response.</span></span> <span data-ttu-id="7c5f7-748">Tím se zastaví šíření výjimky.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-748">This stops propagation of the exception.</span></span> <span data-ttu-id="7c5f7-749">Filtr výjimek nemůže vypnout výjimku na "úspěch".</span><span class="sxs-lookup"><span data-stu-id="7c5f7-749">An exception filter can't turn an exception into a "success".</span></span> <span data-ttu-id="7c5f7-750">To může provést pouze filtr akcí.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-750">Only an action filter can do that.</span></span>

<span data-ttu-id="7c5f7-751">Filtry výjimek:</span><span class="sxs-lookup"><span data-stu-id="7c5f7-751">Exception filters:</span></span>

* <span data-ttu-id="7c5f7-752">Jsou vhodné pro vytváření přesahů výjimek, ke kterým dochází v rámci akcí.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-752">Are good for trapping exceptions that occur within actions.</span></span>
* <span data-ttu-id="7c5f7-753">Nejsou tak flexibilní jako middleware při zpracování chyb.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-753">Are not as flexible as error handling middleware.</span></span>

<span data-ttu-id="7c5f7-754">Preferovat middleware pro zpracování výjimek.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-754">Prefer middleware for exception handling.</span></span> <span data-ttu-id="7c5f7-755">Filtry výjimek použijte pouze v případě, že se zpracování chyb *liší* v závislosti na tom, která metoda Action je volána.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-755">Use exception filters only where error handling *differs* based on which action method is called.</span></span> <span data-ttu-id="7c5f7-756">Aplikace může například mít metody akcí pro koncové body rozhraní API i pro zobrazení/HTML.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-756">For example, an app might have action methods for both API endpoints and for views/HTML.</span></span> <span data-ttu-id="7c5f7-757">Koncové body rozhraní API mohou vracet informace o chybě jako JSON, zatímco akce na základě zobrazení by mohly vracet chybovou stránku jako HTML.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-757">The API endpoints could return error information as JSON, while the view-based actions could return an error page as HTML.</span></span>

## <a name="result-filters"></a><span data-ttu-id="7c5f7-758">Filtry výsledků</span><span class="sxs-lookup"><span data-stu-id="7c5f7-758">Result filters</span></span>

<span data-ttu-id="7c5f7-759">Filtry výsledků:</span><span class="sxs-lookup"><span data-stu-id="7c5f7-759">Result filters:</span></span>

* <span data-ttu-id="7c5f7-760">Implementace rozhraní:</span><span class="sxs-lookup"><span data-stu-id="7c5f7-760">Implement an interface:</span></span>
  * <span data-ttu-id="7c5f7-761"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> nebo <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="7c5f7-761"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
  * <span data-ttu-id="7c5f7-762"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> nebo <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span><span class="sxs-lookup"><span data-stu-id="7c5f7-762"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span></span>
* <span data-ttu-id="7c5f7-763">Jejich spuštění obklopuje provádění výsledků akcí.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-763">Their execution surrounds the execution of action results.</span></span>

### <a name="iresultfilter-and-iasyncresultfilter"></a><span data-ttu-id="7c5f7-764">IResultFilter a IAsyncResultFilter</span><span class="sxs-lookup"><span data-stu-id="7c5f7-764">IResultFilter and IAsyncResultFilter</span></span>

<span data-ttu-id="7c5f7-765">Následující kód ukazuje filtr výsledků, který přidá hlavičku protokolu HTTP:</span><span class="sxs-lookup"><span data-stu-id="7c5f7-765">The following code shows a result filter that adds an HTTP header:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="7c5f7-766">Typ výsledku, který se má provést, závisí na akci.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-766">The kind of result being executed depends on the action.</span></span> <span data-ttu-id="7c5f7-767">Akce vracející zobrazení zahrnuje všechny zpracování Razor jako součást prováděné <xref:Microsoft.AspNetCore.Mvc.ViewResult> .</span><span class="sxs-lookup"><span data-stu-id="7c5f7-767">An action returning a view would include all razor processing as part of the <xref:Microsoft.AspNetCore.Mvc.ViewResult> being executed.</span></span> <span data-ttu-id="7c5f7-768">Metoda rozhraní API může provést určitou serializaci v rámci provádění výsledku.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-768">An API method might perform some serialization as part of the execution of the result.</span></span> <span data-ttu-id="7c5f7-769">Přečtěte si další informace o [výsledcích akcí](xref:mvc/controllers/actions).</span><span class="sxs-lookup"><span data-stu-id="7c5f7-769">Learn more about [action results](xref:mvc/controllers/actions).</span></span>

<span data-ttu-id="7c5f7-770">Filtry výsledků se spustí pouze v případě, že akce nebo filtr akcí vytvoří výsledek akce.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-770">Result filters are only executed when an action or action filter produces an action result.</span></span> <span data-ttu-id="7c5f7-771">Filtry výsledků nejsou provedeny v těchto případech:</span><span class="sxs-lookup"><span data-stu-id="7c5f7-771">Result filters are not executed when:</span></span>

* <span data-ttu-id="7c5f7-772">Filtr autorizace nebo filtr prostředků pro krátké okruhy kanálu.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-772">An authorization filter or resource filter short-circuits the pipeline.</span></span>
* <span data-ttu-id="7c5f7-773">Filtr výjimek zpracovává výjimku tím, že vyprodukuje výsledek akce.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-773">An exception filter handles an exception by producing an action result.</span></span>

<span data-ttu-id="7c5f7-774"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName>Metoda může zkrátit provádění výsledků akce a následných filtrů výsledků nastavením <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> na `true` .</span><span class="sxs-lookup"><span data-stu-id="7c5f7-774">The <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> method can short-circuit execution of the action result and subsequent result filters by setting <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> to `true`.</span></span> <span data-ttu-id="7c5f7-775">Zapište do objektu Response v případě krátkého okruhu, abyste se vyhnuli vygenerování prázdné odpovědi.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-775">Write to the response object when short-circuiting to avoid generating an empty response.</span></span> <span data-ttu-id="7c5f7-776">Vyvolání výjimky v `IResultFilter.OnResultExecuting` bude:</span><span class="sxs-lookup"><span data-stu-id="7c5f7-776">Throwing an exception in `IResultFilter.OnResultExecuting` will:</span></span>

* <span data-ttu-id="7c5f7-777">Zabraňte provádění výsledků akce a dalších filtrů.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-777">Prevent execution of the action result and subsequent filters.</span></span>
* <span data-ttu-id="7c5f7-778">Být považována za selhání namísto úspěšného výsledku.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-778">Be treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="7c5f7-779">Při <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> spuštění metody je odpověď pravděpodobně již odeslána klientovi.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-779">When the <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> method runs, the response has likely already been sent to the client.</span></span> <span data-ttu-id="7c5f7-780">Pokud byla odpověď již odeslána klientovi, nelze ji dále změnit.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-780">If the response has already been sent to the client, it cannot be changed further.</span></span>

<span data-ttu-id="7c5f7-781">`ResultExecutedContext.Canceled`je nastaven na hodnotu, `true` Pokud je spuštění výsledku akce zkráceno jiným filtrem.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-781">`ResultExecutedContext.Canceled` is set to `true` if the action result execution was short-circuited by another filter.</span></span>

<span data-ttu-id="7c5f7-782">`ResultExecutedContext.Exception`je nastavena na hodnotu jinou než null, pokud výsledek akce nebo následný filtr výsledků vyvolal výjimku.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-782">`ResultExecutedContext.Exception` is set to a non-null value if the action result or a subsequent result filter threw an exception.</span></span> <span data-ttu-id="7c5f7-783">Nastavení `Exception` na hodnotu null efektivně zpracovává výjimku a brání výjimce znovu vyvolat ASP.NET Core později v kanálu.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-783">Setting `Exception` to null effectively handles an exception and prevents the exception from being rethrown by ASP.NET Core later in the pipeline.</span></span> <span data-ttu-id="7c5f7-784">Neexistuje žádný spolehlivý způsob, jak zapisovat data do odpovědi při zpracování výjimky ve filtru výsledků.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-784">There is no reliable way to write data to a response when handling an exception in a result filter.</span></span> <span data-ttu-id="7c5f7-785">Pokud byly hlavičky vyprázdněny do klienta, pokud výsledek akce vyvolá výjimku, neexistuje žádný spolehlivý mechanismus pro odeslání kódu chyby.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-785">If the headers have been flushed to the client when an action result throws an exception, there's no reliable mechanism to send a failure code.</span></span>

<span data-ttu-id="7c5f7-786">V případě <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter> volání `await next` na se <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> spustí všechny následné filtry výsledků a výsledek akce.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-786">For an <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, a call to `await next` on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> executes any subsequent result filters and the action result.</span></span> <span data-ttu-id="7c5f7-787">Do krátkodobého okruhu nastavte [ResultExecutingContext. Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) na `true` a Nevolejte na `ResultExecutionDelegate` :</span><span class="sxs-lookup"><span data-stu-id="7c5f7-787">To short-circuit, set [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) to `true` and don't call the `ResultExecutionDelegate`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MyAsyncResponseFilter.cs?name=snippet)]

<span data-ttu-id="7c5f7-788">Rozhraní poskytuje abstrakci `ResultFilterAttribute` , která může být podtříd.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-788">The framework provides an abstract `ResultFilterAttribute` that can be subclassed.</span></span> <span data-ttu-id="7c5f7-789">Dříve uvedená třída [AddHeaderAttribute](#add-header-attribute) je příkladem atributu filtru výsledků.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-789">The [AddHeaderAttribute](#add-header-attribute) class shown previously is an example of a result filter attribute.</span></span>

### <a name="ialwaysrunresultfilter-and-iasyncalwaysrunresultfilter"></a><span data-ttu-id="7c5f7-790">IAlwaysRunResultFilter a IAsyncAlwaysRunResultFilter</span><span class="sxs-lookup"><span data-stu-id="7c5f7-790">IAlwaysRunResultFilter and IAsyncAlwaysRunResultFilter</span></span>

<span data-ttu-id="7c5f7-791"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter>Rozhraní a <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> deklaruje <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> implementaci, která je spuštěna pro všechny výsledky akce.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-791">The <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> interfaces declare an <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> implementation that runs for all action results.</span></span> <span data-ttu-id="7c5f7-792">To zahrnuje výsledky akcí, které vytvořil:</span><span class="sxs-lookup"><span data-stu-id="7c5f7-792">This includes action results produced by:</span></span>

* <span data-ttu-id="7c5f7-793">Filtry autorizace a filtry prostředků, které mají krátký okruh.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-793">Authorization filters and resource filters that short-circuit.</span></span>
* <span data-ttu-id="7c5f7-794">Filtry výjimek.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-794">Exception filters.</span></span>

<span data-ttu-id="7c5f7-795">Například následující filtr vždy spustí a nastaví výsledek akce ( <xref:Microsoft.AspNetCore.Mvc.ObjectResult> ) s *422 nezpracovaným* stavovým kódem entity, pokud se nezdaří vyjednávání obsahu:</span><span class="sxs-lookup"><span data-stu-id="7c5f7-795">For example, the following filter always runs and sets an action result (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) with a *422 Unprocessable Entity* status code when content negotiation fails:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/UnprocessableResultFilter.cs?name=snippet)]

### <a name="ifilterfactory"></a><span data-ttu-id="7c5f7-796">IFilterFactory</span><span class="sxs-lookup"><span data-stu-id="7c5f7-796">IFilterFactory</span></span>

<span data-ttu-id="7c5f7-797"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>implementuje <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> .</span><span class="sxs-lookup"><span data-stu-id="7c5f7-797"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span></span> <span data-ttu-id="7c5f7-798">Proto `IFilterFactory` může být instance použita jako `IFilterMetadata` instance kdekoli v kanálu filtru.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-798">Therefore, an `IFilterFactory` instance can be used as an `IFilterMetadata` instance anywhere in the filter pipeline.</span></span> <span data-ttu-id="7c5f7-799">Když se modul runtime připraví k vyvolání filtru, pokusí se ho přetypovat na `IFilterFactory` .</span><span class="sxs-lookup"><span data-stu-id="7c5f7-799">When the runtime prepares to invoke the filter, it attempts to cast it to an `IFilterFactory`.</span></span> <span data-ttu-id="7c5f7-800">Pokud je toto přetypování úspěšné, <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> je volána metoda pro vytvoření `IFilterMetadata` instance, která je vyvolána.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-800">If that cast succeeds, the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method is called to create the `IFilterMetadata` instance that is invoked.</span></span> <span data-ttu-id="7c5f7-801">To poskytuje flexibilní návrh, protože přesný kanál filtru není nutné nastavit explicitně při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-801">This provides a flexible design, since the precise filter pipeline doesn't need to be set explicitly when the app starts.</span></span>

<span data-ttu-id="7c5f7-802">`IFilterFactory`může být implementováno pomocí implementace vlastního atributu jako jiný přístup k vytváření filtrů:</span><span class="sxs-lookup"><span data-stu-id="7c5f7-802">`IFilterFactory` can be implemented using custom attribute implementations as another approach to creating filters:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/AddHeaderWithFactoryAttribute.cs?name=snippet_IFilterFactory&highlight=1,4,5,6,7)]

<span data-ttu-id="7c5f7-803">Předchozí kód lze otestovat spuštěním [ukázky stahování](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample):</span><span class="sxs-lookup"><span data-stu-id="7c5f7-803">The preceding code can be tested by running the [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample):</span></span>

* <span data-ttu-id="7c5f7-804">Vyvolejte vývojářské nástroje F12.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-804">Invoke the F12 developer tools.</span></span>
* <span data-ttu-id="7c5f7-805">Přejděte na adresu `https://localhost:5001/Sample/HeaderWithFactory`.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-805">Navigate to `https://localhost:5001/Sample/HeaderWithFactory`.</span></span>

<span data-ttu-id="7c5f7-806">Vývojářské nástroje F12 zobrazují následující hlavičky odpovědí přidané ukázkovým kódem:</span><span class="sxs-lookup"><span data-stu-id="7c5f7-806">The F12 developer tools display the following response headers added by the sample code:</span></span>

* <span data-ttu-id="7c5f7-807">**Autor:**`Joe Smith`</span><span class="sxs-lookup"><span data-stu-id="7c5f7-807">**author:** `Joe Smith`</span></span>
* <span data-ttu-id="7c5f7-808">**globaladdheader:**`Result filter added to MvcOptions.Filters`</span><span class="sxs-lookup"><span data-stu-id="7c5f7-808">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span></span>
* <span data-ttu-id="7c5f7-809">**interní:**`My header`</span><span class="sxs-lookup"><span data-stu-id="7c5f7-809">**internal:** `My header`</span></span>

<span data-ttu-id="7c5f7-810">Předchozí kód vytvoří hlavičku **interního:** `My header` Response.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-810">The preceding code creates the **internal:** `My header` response header.</span></span>

### <a name="ifilterfactory-implemented-on-an-attribute"></a><span data-ttu-id="7c5f7-811">IFilterFactory implementované u atributu</span><span class="sxs-lookup"><span data-stu-id="7c5f7-811">IFilterFactory implemented on an attribute</span></span>

<!-- Review 
This section needs to be rewritten.
What's a non-named attribute?
-->

<span data-ttu-id="7c5f7-812">Filtry, které implementují, `IFilterFactory` jsou užitečné pro filtry, které:</span><span class="sxs-lookup"><span data-stu-id="7c5f7-812">Filters that implement `IFilterFactory` are useful for filters that:</span></span>

* <span data-ttu-id="7c5f7-813">Nevyžadovat předávání parametrů</span><span class="sxs-lookup"><span data-stu-id="7c5f7-813">Don't require passing parameters.</span></span>
* <span data-ttu-id="7c5f7-814">Musí mít závislosti konstruktoru, které musí vyplnit DI.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-814">Have constructor dependencies that need to be filled by DI.</span></span>

<span data-ttu-id="7c5f7-815"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>implementuje <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> .</span><span class="sxs-lookup"><span data-stu-id="7c5f7-815"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="7c5f7-816">`IFilterFactory`zpřístupňuje <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> metodu pro vytvoření <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-816">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="7c5f7-817">`CreateInstance`Načte zadaný typ z kontejneru Services (DI).</span><span class="sxs-lookup"><span data-stu-id="7c5f7-817">`CreateInstance` loads the specified type from the services container (DI).</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/SampleActionFilterAttribute.cs?name=snippet_TypeFilterAttribute&highlight=1,3,7)]

<span data-ttu-id="7c5f7-818">Následující kód ukazuje tři přístupy k použití `[SampleActionFilter]` :</span><span class="sxs-lookup"><span data-stu-id="7c5f7-818">The following code shows three approaches to applying the `[SampleActionFilter]`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet&highlight=1)]

<span data-ttu-id="7c5f7-819">V předchozím kódu upravení metoda s jako `[SampleActionFilter]` upřednostňovaným přístupem k použití `SampleActionFilter` .</span><span class="sxs-lookup"><span data-stu-id="7c5f7-819">In the preceding code, decorating the method with `[SampleActionFilter]` is the preferred approach to applying the `SampleActionFilter`.</span></span>

## <a name="using-middleware-in-the-filter-pipeline"></a><span data-ttu-id="7c5f7-820">Použití middlewaru v kanálu filtru</span><span class="sxs-lookup"><span data-stu-id="7c5f7-820">Using middleware in the filter pipeline</span></span>

<span data-ttu-id="7c5f7-821">Filtry prostředků fungují jako [middleware](xref:fundamentals/middleware/index) v tom, že obklopují provádění všech položek, které jsou později v kanálu.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-821">Resource filters work like [middleware](xref:fundamentals/middleware/index) in that they surround the execution of everything that comes later in the pipeline.</span></span> <span data-ttu-id="7c5f7-822">Filtry se ale liší od middlewaru v tom, že jsou součástí modulu ASP.NET Core Runtime, což znamená, že mají přístup k ASP.NET Core kontextu a konstrukcí.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-822">But filters differ from middleware in that they're part of the ASP.NET Core runtime, which means that they have access to ASP.NET Core context and constructs.</span></span>

<span data-ttu-id="7c5f7-823">Chcete-li použít middleware jako filtr, vytvořte typ s `Configure` metodou, která určuje middleware pro vložení do kanálu filtru.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-823">To use middleware as a filter, create a type with a `Configure` method that specifies the middleware to inject into the filter pipeline.</span></span> <span data-ttu-id="7c5f7-824">Následující příklad používá middleware Localization k vytvoření aktuální jazykové verze pro požadavek:</span><span class="sxs-lookup"><span data-stu-id="7c5f7-824">The following example uses the localization middleware to establish the current culture for a request:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LocalizationPipeline.cs?name=snippet_MiddlewareFilter&highlight=3,22)]

<span data-ttu-id="7c5f7-825">Pomocí <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> Spusťte middleware:</span><span class="sxs-lookup"><span data-stu-id="7c5f7-825">Use the <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> to run the middleware:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_MiddlewareFilter&highlight=2)]

<span data-ttu-id="7c5f7-826">Filtry middlewaru jsou spouštěny ve stejné fázi kanálu filtru jako filtry prostředků, před vazbami modelů a po zbytek kanálu.</span><span class="sxs-lookup"><span data-stu-id="7c5f7-826">Middleware filters run at the same stage of the filter pipeline as Resource filters, before model binding and after the rest of the pipeline.</span></span>

## <a name="next-actions"></a><span data-ttu-id="7c5f7-827">Další akce</span><span class="sxs-lookup"><span data-stu-id="7c5f7-827">Next actions</span></span>

* <span data-ttu-id="7c5f7-828">Viz [metody filtru pro Razor stránky](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="7c5f7-828">See [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>
* <span data-ttu-id="7c5f7-829">Pro experimentování s filtry, [stažení, otestování a úpravy ukázky GitHubu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample).</span><span class="sxs-lookup"><span data-stu-id="7c5f7-829">To experiment with filters, [download, test, and modify the GitHub sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample).</span></span>

::: moniker-end
