---
title: Filtry v ASP.NET Core
author: Rick-Anderson
description: Přečtěte si, jak filtry fungují a jak je používat v ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 02/04/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/controllers/filters
ms.openlocfilehash: 7fe33a620e43603388dd0cacb3ea42f5b5adc40f
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2020
ms.locfileid: "85408289"
---
# <a name="filters-in-aspnet-core"></a><span data-ttu-id="e5b8d-103">Filtry v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e5b8d-103">Filters in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="e5b8d-104">[Kirka Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Dykstra](https://github.com/tdykstra/)a [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="e5b8d-104">By [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="e5b8d-105">*Filtry* v ASP.NET Core umožňují spuštění kódu před nebo po určitých fázích v kanálu zpracování požadavků.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-105">*Filters* in ASP.NET Core allow code to be run before or after specific stages in the request processing pipeline.</span></span>

<span data-ttu-id="e5b8d-106">Předdefinované filtry zpracovávají úlohy, jako například:</span><span class="sxs-lookup"><span data-stu-id="e5b8d-106">Built-in filters handle tasks such as:</span></span>

* <span data-ttu-id="e5b8d-107">Autorizace (zabránění přístupu k prostředkům, ke kterým uživatel nemá oprávnění).</span><span class="sxs-lookup"><span data-stu-id="e5b8d-107">Authorization (preventing access to resources a user isn't authorized for).</span></span>
* <span data-ttu-id="e5b8d-108">Ukládání odpovědí do mezipaměti (při krátkém okruhu kanálu požadavků, který vrátí odpověď uloženou v mezipaměti).</span><span class="sxs-lookup"><span data-stu-id="e5b8d-108">Response caching (short-circuiting the request pipeline to return a cached response).</span></span>

<span data-ttu-id="e5b8d-109">Vlastní filtry je možné vytvořit pro zpracování vzájemně se týkajících se otázek.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-109">Custom filters can be created to handle cross-cutting concerns.</span></span> <span data-ttu-id="e5b8d-110">Mezi obavy mezi průřezy patří zpracování chyb, ukládání do mezipaměti, konfigurace, autorizace a protokolování.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-110">Examples of cross-cutting concerns include error handling, caching, configuration, authorization, and logging.</span></span>  <span data-ttu-id="e5b8d-111">Filtry zabraňují duplikování kódu.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-111">Filters avoid duplicating code.</span></span> <span data-ttu-id="e5b8d-112">Například filtr výjimek zpracování chyb může konsolidovat zpracování chyb.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-112">For example, an error handling exception filter could consolidate error handling.</span></span>

<span data-ttu-id="e5b8d-113">Tento dokument se týká Razor stránek, řadičů rozhraní API a řadičů se zobrazeními.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-113">This document applies to Razor Pages, API controllers, and controllers with views.</span></span> <span data-ttu-id="e5b8d-114">Filtry nefungují přímo s [ Razor komponentami](xref:blazor/components/index).</span><span class="sxs-lookup"><span data-stu-id="e5b8d-114">Filters don't work directly with [Razor components](xref:blazor/components/index).</span></span> <span data-ttu-id="e5b8d-115">Filtr může nepřímo ovlivnit komponentu pouze v těchto případech:</span><span class="sxs-lookup"><span data-stu-id="e5b8d-115">A filter can only indirectly affect a component when:</span></span>

* <span data-ttu-id="e5b8d-116">Komponenta je vložena do stránky nebo zobrazení.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-116">The component is embedded in a page or view.</span></span>
* <span data-ttu-id="e5b8d-117">Stránka nebo kontroler nebo zobrazení používá filtr.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-117">The page or controller/view uses the filter.</span></span>

<span data-ttu-id="e5b8d-118">[Zobrazit nebo Stáhnout ukázku](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample) ([Jak stáhnout](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="e5b8d-118">[View or download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="how-filters-work"></a><span data-ttu-id="e5b8d-119">Jak fungují filtry</span><span class="sxs-lookup"><span data-stu-id="e5b8d-119">How filters work</span></span>

<span data-ttu-id="e5b8d-120">Filtry se spouštějí v *kanálu vyvolání akce ASP.NET Core*, někdy označované jako *kanál filtru*.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-120">Filters run within the *ASP.NET Core action invocation pipeline*, sometimes referred to as the *filter pipeline*.</span></span> <span data-ttu-id="e5b8d-121">Kanál filtru se spustí po ASP.NET Core vybere akci, která se má provést.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-121">The filter pipeline runs after ASP.NET Core selects the action to execute.</span></span>

![Požadavek se zpracovává prostřednictvím jiného middlewaru, middleware směrování, výběru akcí a kanálu vyvolání akce.](filters/_static/filter-pipeline-1.png)

### <a name="filter-types"></a><span data-ttu-id="e5b8d-124">Typy filtrů</span><span class="sxs-lookup"><span data-stu-id="e5b8d-124">Filter types</span></span>

<span data-ttu-id="e5b8d-125">Každý typ filtru se spustí v jiné fázi kanálu filtru:</span><span class="sxs-lookup"><span data-stu-id="e5b8d-125">Each filter type is executed at a different stage in the filter pipeline:</span></span>

* <span data-ttu-id="e5b8d-126">[Filtry autorizace](#authorization-filters) se spouštějí jako první a používají se k určení, jestli je uživatel autorizovaný pro požadavek.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-126">[Authorization filters](#authorization-filters) run first and are used to determine whether the user is authorized for the request.</span></span> <span data-ttu-id="e5b8d-127">Filtry autorizace pro krátkodobé vynechání kanálu, pokud žádost není autorizována.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-127">Authorization filters short-circuit the pipeline if the request is not authorized.</span></span>

* <span data-ttu-id="e5b8d-128">[Filtry prostředků](#resource-filters):</span><span class="sxs-lookup"><span data-stu-id="e5b8d-128">[Resource filters](#resource-filters):</span></span>

  * <span data-ttu-id="e5b8d-129">Spusťte po autorizaci.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-129">Run after authorization.</span></span>  
  * <span data-ttu-id="e5b8d-130"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*>spustí kód před zbytek kanálu filtru.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-130"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> runs code before the rest of the filter pipeline.</span></span> <span data-ttu-id="e5b8d-131">Například `OnResourceExecuting` spustí kód před vazbou modelu.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-131">For example, `OnResourceExecuting` runs code before model binding.</span></span>
  * <span data-ttu-id="e5b8d-132"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*>spustí kód po dokončení zbývající části kanálu.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-132"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> runs code after the rest of the pipeline has completed.</span></span>

* <span data-ttu-id="e5b8d-133">[Filtry akcí](#action-filters):</span><span class="sxs-lookup"><span data-stu-id="e5b8d-133">[Action filters](#action-filters):</span></span>

  * <span data-ttu-id="e5b8d-134">Spustit kód bezprostředně před a po volání metody Action.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-134">Run code immediately before and after an action method is called.</span></span>
  * <span data-ttu-id="e5b8d-135">Může změnit argumenty předané do akce.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-135">Can change the arguments passed into an action.</span></span>
  * <span data-ttu-id="e5b8d-136">Může změnit výsledek vrácený z akce.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-136">Can change the result returned from the action.</span></span>
  * <span data-ttu-id="e5b8d-137">Nejsou **na** stránkách podporovány Razor .</span><span class="sxs-lookup"><span data-stu-id="e5b8d-137">Are **not** supported in Razor Pages.</span></span>

* <span data-ttu-id="e5b8d-138">[Filtry výjimek](#exception-filters) aplikují globální zásady na neošetřené výjimky, ke kterým došlo před zápisem textu odpovědi do.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-138">[Exception filters](#exception-filters) apply global policies to unhandled exceptions that occur before the response body has been written to.</span></span>

* <span data-ttu-id="e5b8d-139">[Filtry výsledků](#result-filters) spouštějí kód bezprostředně před a po provedení výsledků akce.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-139">[Result filters](#result-filters) run code immediately before and after the execution of action results.</span></span> <span data-ttu-id="e5b8d-140">Spustí se pouze v případě, že metoda akce byla úspěšně provedena.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-140">They run only when the action method has executed successfully.</span></span> <span data-ttu-id="e5b8d-141">Jsou užitečné pro logiku, která musí obklopit zobrazení nebo formátovací modul.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-141">They are useful for logic that must surround view or formatter execution.</span></span>

<span data-ttu-id="e5b8d-142">Následující diagram znázorňuje způsob interakce typů filtrů v kanálu filtru.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-142">The following diagram shows how filter types interact in the filter pipeline.</span></span>

![Požadavek se zpracovává pomocí autorizačních filtrů, filtrů prostředků, vazeb modelů, filtrů akcí, provádění akcí a konverze výsledků akcí, filtrů výjimek, výsledných filtrů a provádění výsledků.](filters/_static/filter-pipeline-2.png)

## <a name="implementation"></a><span data-ttu-id="e5b8d-145">Implementace</span><span class="sxs-lookup"><span data-stu-id="e5b8d-145">Implementation</span></span>

<span data-ttu-id="e5b8d-146">Filtry podporují synchronní i asynchronní implementace prostřednictvím různých definic rozhraní.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-146">Filters support both synchronous and asynchronous implementations through different interface definitions.</span></span>

<span data-ttu-id="e5b8d-147">Synchronní filtry spouštějí kód před a po fázi zřetězení.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-147">Synchronous filters run code before and after their pipeline stage.</span></span> <span data-ttu-id="e5b8d-148">Například <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*> je volána před voláním metody Action.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-148">For example, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*> is called before the action method is called.</span></span> <span data-ttu-id="e5b8d-149"><xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*>je volána po návratu metody Action.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-149"><xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*> is called after the action method returns.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="e5b8d-150">Asynchronní filtry definují `On-Stage-ExecutionAsync` metodu.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-150">Asynchronous filters define an `On-Stage-ExecutionAsync` method.</span></span> <span data-ttu-id="e5b8d-151">Například <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*> :</span><span class="sxs-lookup"><span data-stu-id="e5b8d-151">For example, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*>:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/SampleAsyncActionFilter.cs?name=snippet)]

<span data-ttu-id="e5b8d-152">V předchozím kódu `SampleAsyncActionFilter` obsahuje znak <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> ( `next` ), který provádí metodu Action.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-152">In the preceding code, the `SampleAsyncActionFilter` has an <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`) that executes the action method.</span></span>

### <a name="multiple-filter-stages"></a><span data-ttu-id="e5b8d-153">Několik fází filtru</span><span class="sxs-lookup"><span data-stu-id="e5b8d-153">Multiple filter stages</span></span>

<span data-ttu-id="e5b8d-154">Rozhraní pro více fází filtru lze implementovat v rámci jedné třídy.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-154">Interfaces for multiple filter stages can be implemented in a single class.</span></span> <span data-ttu-id="e5b8d-155">Například <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> Třída implementuje:</span><span class="sxs-lookup"><span data-stu-id="e5b8d-155">For example, the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> class implements:</span></span>

* <span data-ttu-id="e5b8d-156">Synchronní: <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> a<xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter></span><span class="sxs-lookup"><span data-stu-id="e5b8d-156">Synchronous: <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> and  <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter></span></span>
* <span data-ttu-id="e5b8d-157">Asynchronní: <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> a<xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="e5b8d-157">Asynchronous: <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
* <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>

<span data-ttu-id="e5b8d-158">Implementujte **buď** synchronní, nebo asynchronní verzi rozhraní filtru, **nikoli** obojí.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-158">Implement **either** the synchronous or the async version of a filter interface, **not** both.</span></span> <span data-ttu-id="e5b8d-159">Modul runtime nejprve kontroluje, zda filtr implementuje asynchronní rozhraní a v takovém případě jej zavolá.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-159">The runtime checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="e5b8d-160">V takovém případě volá metody synchronního rozhraní.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-160">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="e5b8d-161">Pokud jsou asynchronní i synchronní rozhraní implementovány v jedné třídě, je volána pouze asynchronní metoda.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-161">If both asynchronous and synchronous interfaces are implemented in one class, only the async method is called.</span></span> <span data-ttu-id="e5b8d-162">Při použití abstraktních tříd <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> , jako je přepsání pouze synchronních metod nebo asynchronní metody pro každý typ filtru.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-162">When using abstract classes like <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>, override only the synchronous methods or the asynchronous method for each filter type.</span></span>

### <a name="built-in-filter-attributes"></a><span data-ttu-id="e5b8d-163">Předdefinované atributy filtru</span><span class="sxs-lookup"><span data-stu-id="e5b8d-163">Built-in filter attributes</span></span>

<span data-ttu-id="e5b8d-164">ASP.NET Core obsahuje integrované filtry založené na atributech, které mohou být roztříděné a přizpůsobené.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-164">ASP.NET Core includes built-in attribute-based filters that can be subclassed and customized.</span></span> <span data-ttu-id="e5b8d-165">Například následující filtr výsledků přidá hlavičku do odpovědi:</span><span class="sxs-lookup"><span data-stu-id="e5b8d-165">For example, the following result filter adds a header to the response:</span></span>

<a name="add-header-attribute"></a>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/AddHeaderAttribute.cs?name=snippet)]

<span data-ttu-id="e5b8d-166">Atributy umožňují filtrům přijímat argumenty, jak je znázorněno v předchozím příkladu.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-166">Attributes allow filters to accept arguments, as shown in the preceding example.</span></span> <span data-ttu-id="e5b8d-167">Použijte `AddHeaderAttribute` pro metodu Controller nebo Action a zadejte název a hodnotu HLAVIČKY http:</span><span class="sxs-lookup"><span data-stu-id="e5b8d-167">Apply the `AddHeaderAttribute` to a controller or action method and specify the name and value of the HTTP header:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

<span data-ttu-id="e5b8d-168">K prohlédnutí hlaviček použijte nástroj, jako je například nástroj pro [vývojáře v prohlížeči](https://developer.mozilla.org/docs/Learn/Common_questions/What_are_browser_developer_tools) .</span><span class="sxs-lookup"><span data-stu-id="e5b8d-168">Use a tool such as the [browser developer tools](https://developer.mozilla.org/docs/Learn/Common_questions/What_are_browser_developer_tools) to examine the headers.</span></span> <span data-ttu-id="e5b8d-169">V části **hlavičky odpovědi** `author: Rick Anderson` se zobrazí.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-169">Under **Response Headers**, `author: Rick Anderson` is displayed.</span></span>

<span data-ttu-id="e5b8d-170">Následující kód implementuje `ActionFilterAttribute` :</span><span class="sxs-lookup"><span data-stu-id="e5b8d-170">The following code implements an `ActionFilterAttribute` that:</span></span>

* <span data-ttu-id="e5b8d-171">Přečte název a název z konfiguračního systému.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-171">Reads the title and name from the configuration system.</span></span> <span data-ttu-id="e5b8d-172">Na rozdíl od předchozí ukázky, následující kód nevyžaduje přidání parametrů filtru do kódu.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-172">Unlike the previous sample, the following code doesn't require filter parameters to be added to the code.</span></span>
* <span data-ttu-id="e5b8d-173">Přidá název a název do hlavičky odpovědi.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-173">Adds the title and name to the response header.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MyActionFilterAttribute.cs?name=snippet)]

<span data-ttu-id="e5b8d-174">Možnosti konfigurace se poskytují z [konfiguračního systému](xref:fundamentals/configuration/index) pomocí [vzoru možností](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="e5b8d-174">The configuration options are provided from the [configuration system](xref:fundamentals/configuration/index) using the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="e5b8d-175">Například z *appsettings.js* souboru:</span><span class="sxs-lookup"><span data-stu-id="e5b8d-175">For example, from the *appsettings.json* file:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/appsettings.json)]

<span data-ttu-id="e5b8d-176">V `StartUp.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="e5b8d-176">In the `StartUp.ConfigureServices`:</span></span>

* <span data-ttu-id="e5b8d-177">`PositionOptions`Třída je přidána do kontejneru služby s `"Position"` oblastí konfigurace.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-177">The `PositionOptions` class is added to the service container with the `"Position"` configuration area.</span></span>
* <span data-ttu-id="e5b8d-178">`MyActionFilterAttribute`Přidá se do kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-178">The `MyActionFilterAttribute` is added to the service container.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupAF.cs?name=snippet)]

<span data-ttu-id="e5b8d-179">Následující kód ukazuje `PositionOptions` třídu:</span><span class="sxs-lookup"><span data-stu-id="e5b8d-179">The following code shows the `PositionOptions` class:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Helper/PositionOptions.cs?name=snippet)]

<span data-ttu-id="e5b8d-180">Následující kód platí `MyActionFilterAttribute` pro `Index2` metodu:</span><span class="sxs-lookup"><span data-stu-id="e5b8d-180">The following code applies the `MyActionFilterAttribute` to the `Index2` method:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet2&highlight=9)]

<span data-ttu-id="e5b8d-181">V části **hlavičky odpovědi**, `author: Rick Anderson` a `Editor: Joe Smith` se zobrazí při `Sample/Index2` volání koncového bodu.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-181">Under **Response Headers**, `author: Rick Anderson`, and `Editor: Joe Smith` is displayed when the `Sample/Index2` endpoint is called.</span></span>

<span data-ttu-id="e5b8d-182">Následující kód platí `MyActionFilterAttribute` pro a na `AddHeaderAttribute` Razor stránce:</span><span class="sxs-lookup"><span data-stu-id="e5b8d-182">The following code applies the `MyActionFilterAttribute` and the `AddHeaderAttribute` to the Razor Page:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Pages/Movies/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="e5b8d-183">Filtry nelze použít na Razor metody obslužné rutiny stránky.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-183">Filters cannot be applied to Razor Page handler methods.</span></span> <span data-ttu-id="e5b8d-184">Lze je použít buď na Razor model stránky, nebo globálně.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-184">They can be applied either to the Razor Page model or globally.</span></span>

<span data-ttu-id="e5b8d-185">Některé z rozhraní filtru mají odpovídající atributy, které lze použít jako základní třídy pro vlastní implementace.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-185">Several of the filter interfaces have corresponding attributes that can be used as base classes for custom implementations.</span></span>

<span data-ttu-id="e5b8d-186">Atributy filtru:</span><span class="sxs-lookup"><span data-stu-id="e5b8d-186">Filter attributes:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ExceptionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>

## <a name="filter-scopes-and-order-of-execution"></a><span data-ttu-id="e5b8d-187">Filtrovat obory a pořadí provádění</span><span class="sxs-lookup"><span data-stu-id="e5b8d-187">Filter scopes and order of execution</span></span>

<span data-ttu-id="e5b8d-188">Filtr lze přidat do kanálu v jednom ze tří *oborů*:</span><span class="sxs-lookup"><span data-stu-id="e5b8d-188">A filter can be added to the pipeline at one of three *scopes*:</span></span>

* <span data-ttu-id="e5b8d-189">Použití atributu u akce kontroleru.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-189">Using an attribute on a controller action.</span></span> <span data-ttu-id="e5b8d-190">Atributy filtru nelze použít na Razor stránky metod obslužných rutin.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-190">Filter attributes cannot be applied to Razor Pages handler methods.</span></span>
* <span data-ttu-id="e5b8d-191">Použití atributu na řadiči nebo na Razor stránce.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-191">Using an attribute on a controller or Razor Page.</span></span>
* <span data-ttu-id="e5b8d-192">Globálně pro všechny řadiče, akce a Razor stránky, jak je znázorněno v následujícím kódu:</span><span class="sxs-lookup"><span data-stu-id="e5b8d-192">Globally for all controllers, actions, and Razor Pages as shown in the following code:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder.cs?name=snippet)]

### <a name="default-order-of-execution"></a><span data-ttu-id="e5b8d-193">Výchozí pořadí provádění</span><span class="sxs-lookup"><span data-stu-id="e5b8d-193">Default order of execution</span></span>

<span data-ttu-id="e5b8d-194">Pokud existuje více filtrů pro určitou fázi kanálu, rozsah Určuje výchozí pořadí provádění filtru.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-194">When there are multiple filters for a particular stage of the pipeline, scope determines the default order of filter execution.</span></span>  <span data-ttu-id="e5b8d-195">Globální filtry obklopují filtry tříd, které jsou ve filtru metod Surround.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-195">Global filters surround class filters, which in turn surround method filters.</span></span>

<span data-ttu-id="e5b8d-196">V důsledku vnořování filtru je *po* kódu spuštěn v obráceném pořadí *před* kódem.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-196">As a result of filter nesting, the *after* code of filters runs in the reverse order of the *before* code.</span></span> <span data-ttu-id="e5b8d-197">Pořadí filtru:</span><span class="sxs-lookup"><span data-stu-id="e5b8d-197">The filter sequence:</span></span>

* <span data-ttu-id="e5b8d-198">*Před* kódem globálních filtrů.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-198">The *before* code of global filters.</span></span>
  * <span data-ttu-id="e5b8d-199">*Před* kódem kontroleru a Razor filtru stránky.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-199">The *before* code of controller and Razor Page filters.</span></span>
    * <span data-ttu-id="e5b8d-200">Kód *před* filtry metody Action.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-200">The *before* code of action method filters.</span></span>
    * <span data-ttu-id="e5b8d-201">*Po* kódu filtry metody Action.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-201">The *after* code of action method filters.</span></span>
  * <span data-ttu-id="e5b8d-202">*Po* kódu filtru řadiče a Razor stránky.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-202">The *after* code of controller and Razor Page filters.</span></span>
* <span data-ttu-id="e5b8d-203">*Po* kódu globálních filtrů.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-203">The *after* code of global filters.</span></span>
  
<span data-ttu-id="e5b8d-204">Následující příklad ilustruje pořadí, ve kterém jsou metody filtru volány pro filtry synchronních akcí.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-204">The following example that illustrates the order in which filter methods are called for synchronous action filters.</span></span>

| <span data-ttu-id="e5b8d-205">Sequence</span><span class="sxs-lookup"><span data-stu-id="e5b8d-205">Sequence</span></span> | <span data-ttu-id="e5b8d-206">Rozsah filtru</span><span class="sxs-lookup"><span data-stu-id="e5b8d-206">Filter scope</span></span> | <span data-ttu-id="e5b8d-207">Filter – metoda</span><span class="sxs-lookup"><span data-stu-id="e5b8d-207">Filter method</span></span> |
|:--------:|:------------:|:-------------:|
| <span data-ttu-id="e5b8d-208">1</span><span class="sxs-lookup"><span data-stu-id="e5b8d-208">1</span></span> | <span data-ttu-id="e5b8d-209">Globální</span><span class="sxs-lookup"><span data-stu-id="e5b8d-209">Global</span></span> | `OnActionExecuting` |
| <span data-ttu-id="e5b8d-210">2</span><span class="sxs-lookup"><span data-stu-id="e5b8d-210">2</span></span> | <span data-ttu-id="e5b8d-211">Kontroler nebo Razor Stránka</span><span class="sxs-lookup"><span data-stu-id="e5b8d-211">Controller or Razor Page</span></span>| `OnActionExecuting` |
| <span data-ttu-id="e5b8d-212">3</span><span class="sxs-lookup"><span data-stu-id="e5b8d-212">3</span></span> | <span data-ttu-id="e5b8d-213">Metoda</span><span class="sxs-lookup"><span data-stu-id="e5b8d-213">Method</span></span> | `OnActionExecuting` |
| <span data-ttu-id="e5b8d-214">4</span><span class="sxs-lookup"><span data-stu-id="e5b8d-214">4</span></span> | <span data-ttu-id="e5b8d-215">Metoda</span><span class="sxs-lookup"><span data-stu-id="e5b8d-215">Method</span></span> | `OnActionExecuted` |
| <span data-ttu-id="e5b8d-216">5</span><span class="sxs-lookup"><span data-stu-id="e5b8d-216">5</span></span> | <span data-ttu-id="e5b8d-217">Kontroler nebo Razor Stránka</span><span class="sxs-lookup"><span data-stu-id="e5b8d-217">Controller or Razor Page</span></span> | `OnActionExecuted` |
| <span data-ttu-id="e5b8d-218">6</span><span class="sxs-lookup"><span data-stu-id="e5b8d-218">6</span></span> | <span data-ttu-id="e5b8d-219">Globální</span><span class="sxs-lookup"><span data-stu-id="e5b8d-219">Global</span></span> | `OnActionExecuted` |

### <a name="controller-level-filters"></a><span data-ttu-id="e5b8d-220">Filtry na úrovni řadiče</span><span class="sxs-lookup"><span data-stu-id="e5b8d-220">Controller level filters</span></span>

<span data-ttu-id="e5b8d-221">Každý kontroler, který dědí ze <xref:Microsoft.AspNetCore.Mvc.Controller> základní třídy, zahrnuje metody [Controller. OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*), [Controller. OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*)a [Controller. OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*) 
 `OnActionExecuted` .</span><span class="sxs-lookup"><span data-stu-id="e5b8d-221">Every controller that inherits from the <xref:Microsoft.AspNetCore.Mvc.Controller> base class includes [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*),  [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*), and [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted` methods.</span></span> <span data-ttu-id="e5b8d-222">Tyto metody:</span><span class="sxs-lookup"><span data-stu-id="e5b8d-222">These methods:</span></span>

* <span data-ttu-id="e5b8d-223">Zabalte filtry, které se spouštějí pro danou akci.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-223">Wrap the filters that run for a given action.</span></span>
* <span data-ttu-id="e5b8d-224">`OnActionExecuting`je volána před jakýmkoli filtrem akce.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-224">`OnActionExecuting` is called before any of the action's filters.</span></span>
* <span data-ttu-id="e5b8d-225">`OnActionExecuted`se volá po všech filtrech akcí.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-225">`OnActionExecuted` is called after all of the action filters.</span></span>
* <span data-ttu-id="e5b8d-226">`OnActionExecutionAsync`je volána před jakýmkoli filtrem akce.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-226">`OnActionExecutionAsync` is called before any of the action's filters.</span></span> <span data-ttu-id="e5b8d-227">Kód v filtru po `next` spuštění po metodě Action.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-227">Code in the filter after `next` runs after the action method.</span></span>

<span data-ttu-id="e5b8d-228">Například v ukázce ke stažení `MySampleActionFilter` se použije globálně při spuštění.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-228">For example, in the download sample, `MySampleActionFilter` is applied globally in startup.</span></span>

<span data-ttu-id="e5b8d-229">`TestController`:</span><span class="sxs-lookup"><span data-stu-id="e5b8d-229">The `TestController`:</span></span>

* <span data-ttu-id="e5b8d-230">Aplikuje `SampleActionFilterAttribute` ( `[SampleActionFilter]` ) na `FilterTest2` akci.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-230">Applies the `SampleActionFilterAttribute` (`[SampleActionFilter]`) to the `FilterTest2` action.</span></span>
* <span data-ttu-id="e5b8d-231">Přepisuje `OnActionExecuting` a `OnActionExecuted` .</span><span class="sxs-lookup"><span data-stu-id="e5b8d-231">Overrides `OnActionExecuting` and `OnActionExecuted`.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/TestController.cs?name=snippet)]

<!-- test via  webBuilder.UseStartup<Startup>(); -->

<span data-ttu-id="e5b8d-232">Navigace pro `https://localhost:5001/Test2/FilterTest2` spustí následující kód:</span><span class="sxs-lookup"><span data-stu-id="e5b8d-232">Navigating to `https://localhost:5001/Test2/FilterTest2` runs the following code:</span></span>

* `TestController.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `SampleActionFilterAttribute.OnActionExecuting`
      * `TestController.FilterTest2`
    * `SampleActionFilterAttribute.OnActionExecuted`
  * `MySampleActionFilter.OnActionExecuted`
* `TestController.OnActionExecuted`

<span data-ttu-id="e5b8d-233">Filtry na úrovni řadiče nastaví vlastnost [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) na `int.MinValue` .</span><span class="sxs-lookup"><span data-stu-id="e5b8d-233">Controller level filters set the [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) property to `int.MinValue`.</span></span> <span data-ttu-id="e5b8d-234">Filtry na úrovni řadiče **nelze nastavit** tak, aby běžely po filtrech použitých na metody.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-234">Controller level filters can **not** be set to run after filters applied to methods.</span></span> <span data-ttu-id="e5b8d-235">Pořadí je vysvětleno v další části.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-235">Order is explained in the next section.</span></span>

<span data-ttu-id="e5b8d-236">RazorInformace o stránkách naleznete v tématu [implementace Razor filtrů stránek pomocí přepsání metod filtru](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span><span class="sxs-lookup"><span data-stu-id="e5b8d-236">For Razor Pages, see [Implement Razor Page filters by overriding filter methods](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span></span>

### <a name="overriding-the-default-order"></a><span data-ttu-id="e5b8d-237">Přepsání výchozího pořadí</span><span class="sxs-lookup"><span data-stu-id="e5b8d-237">Overriding the default order</span></span>

<span data-ttu-id="e5b8d-238">Výchozí sekvenci spuštění lze přepsat implementací <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter> .</span><span class="sxs-lookup"><span data-stu-id="e5b8d-238">The default sequence of execution can be overridden by implementing <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span></span> <span data-ttu-id="e5b8d-239">`IOrderedFilter`zpřístupňuje <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> vlastnost, která má přednost před rozsahem, aby určila pořadí provádění.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-239">`IOrderedFilter` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> property that takes precedence over scope to determine the order of execution.</span></span> <span data-ttu-id="e5b8d-240">Filtr s nižší `Order` hodnotou:</span><span class="sxs-lookup"><span data-stu-id="e5b8d-240">A filter with a lower `Order` value:</span></span>

* <span data-ttu-id="e5b8d-241">Spustí *před* kódem filtr s vyšší hodnotou `Order` .</span><span class="sxs-lookup"><span data-stu-id="e5b8d-241">Runs the *before* code before that of a filter with a higher value of `Order`.</span></span>
* <span data-ttu-id="e5b8d-242">Spustí *za* kódem za filtr s vyšší `Order` hodnotou.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-242">Runs the *after* code after that of a filter with a higher `Order` value.</span></span>

<span data-ttu-id="e5b8d-243">`Order`Vlastnost je nastavena s parametrem konstruktoru:</span><span class="sxs-lookup"><span data-stu-id="e5b8d-243">The `Order` property is set with a constructor parameter:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test3Controller.cs?name=snippet)]

<span data-ttu-id="e5b8d-244">Vezměte v úvahu dva filtry akcí v následujícím kontroleru:</span><span class="sxs-lookup"><span data-stu-id="e5b8d-244">Consider the two action filters in the following controller:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test2Controller.cs?name=snippet)]

<span data-ttu-id="e5b8d-245">Globální filtr je přidaný v `StartUp.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="e5b8d-245">A global filter is added in `StartUp.ConfigureServices`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder.cs?name=snippet)]

<span data-ttu-id="e5b8d-246">Tři filtry jsou spouštěny v následujícím pořadí:</span><span class="sxs-lookup"><span data-stu-id="e5b8d-246">The 3 filters run in the following order:</span></span>

* `Test2Controller.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `MyAction2FilterAttribute.OnActionExecuting`
      * `Test2Controller.FilterTest2`
    * `MySampleActionFilter.OnActionExecuted`
  * `MyAction2FilterAttribute.OnResultExecuting`
* `Test2Controller.OnActionExecuted`

<span data-ttu-id="e5b8d-247">`Order`Vlastnost Přepisuje obor při určování pořadí, ve kterém jsou filtry spouštěny.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-247">The `Order` property overrides scope when determining the order in which filters run.</span></span> <span data-ttu-id="e5b8d-248">Filtry jsou seřazené podle pořadí, pak se k přerušení vztahů používá obor.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-248">Filters are sorted first by order, then scope is used to break ties.</span></span> <span data-ttu-id="e5b8d-249">Všechny předdefinované filtry implementují `IOrderedFilter` a nastaví výchozí `Order` hodnotu na 0.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-249">All of the built-in filters implement `IOrderedFilter` and set the default `Order` value to 0.</span></span> <span data-ttu-id="e5b8d-250">Jak už bylo uvedeno výše, filtry na úrovni řadiče nastaví vlastnost [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) na `int.MinValue` pro předdefinované filtry. Scope určí pořadí, pokud `Order` není nastavená na nenulovou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-250">As mentioned previously, controller level filters set the [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) property to `int.MinValue` For built-in filters, scope determines order unless `Order` is set to a non-zero value.</span></span>

<span data-ttu-id="e5b8d-251">V předchozím kódu `MySampleActionFilter` má globální rozsah, takže se spustí před `MyAction2FilterAttribute` , který má obor kontroleru.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-251">In the preceding code, `MySampleActionFilter` has global scope so it runs before `MyAction2FilterAttribute`, which has controller scope.</span></span> <span data-ttu-id="e5b8d-252">Aby bylo možné `MyAction2FilterAttribute` Spustit jako první, nastavte pořadí na `int.MinValue` :</span><span class="sxs-lookup"><span data-stu-id="e5b8d-252">To make `MyAction2FilterAttribute` run first, set the order to `int.MinValue`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test2Controller.cs?name=snippet2)]

<span data-ttu-id="e5b8d-253">Aby se globální filtr `MySampleActionFilter` spouštěl jako první, nastavte `Order` na `int.MinValue` :</span><span class="sxs-lookup"><span data-stu-id="e5b8d-253">To make the global filter `MySampleActionFilter` run first, set `Order` to `int.MinValue`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder2.cs?name=snippet&highlight=6)]

## <a name="cancellation-and-short-circuiting"></a><span data-ttu-id="e5b8d-254">Zrušení a zkrácení okruhů</span><span class="sxs-lookup"><span data-stu-id="e5b8d-254">Cancellation and short-circuiting</span></span>

<span data-ttu-id="e5b8d-255">Kanál filtru může být v krátkém okruhu nastavením <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> vlastnosti pro <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> parametr poskytnutý metodě Filter.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-255">The filter pipeline can be short-circuited by setting the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> property on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> parameter provided to the filter method.</span></span> <span data-ttu-id="e5b8d-256">Například následující filtr prostředků zabrání ve spuštění zbývajícího kanálu:</span><span class="sxs-lookup"><span data-stu-id="e5b8d-256">For instance, the following Resource filter prevents the rest of the pipeline from executing:</span></span>

<a name="short-circuiting-resource-filter"></a>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ShortCircuitingResourceFilterAttribute.cs?name=snippet)]

<span data-ttu-id="e5b8d-257">V následujícím kódu, `ShortCircuitingResourceFilter` a jako `AddHeader` cíl filtru i cílovou `SomeResource` metodu Action.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-257">In the following code, both the `ShortCircuitingResourceFilter` and the `AddHeader` filter target the `SomeResource` action method.</span></span> <span data-ttu-id="e5b8d-258">`ShortCircuitingResourceFilter`:</span><span class="sxs-lookup"><span data-stu-id="e5b8d-258">The `ShortCircuitingResourceFilter`:</span></span>

* <span data-ttu-id="e5b8d-259">Nejprve se spustí, protože se jedná o filtr prostředků a `AddHeader` je filtr akcí.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-259">Runs first, because it's a Resource Filter and `AddHeader` is an Action Filter.</span></span>
* <span data-ttu-id="e5b8d-260">Krátké okruhy zbývajícího kanálu.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-260">Short-circuits the rest of the pipeline.</span></span>

<span data-ttu-id="e5b8d-261">Proto `AddHeader` Filtr pro akci nikdy neběží `SomeResource` .</span><span class="sxs-lookup"><span data-stu-id="e5b8d-261">Therefore the `AddHeader` filter never runs for the `SomeResource` action.</span></span> <span data-ttu-id="e5b8d-262">Toto chování by bylo stejné, pokud byly oba filtry aplikovány na úrovni metody akce, a to za předpokladu, že byla `ShortCircuitingResourceFilter` spuštěna jako první.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-262">This behavior would be the same if both filters were applied at the action method level, provided the `ShortCircuitingResourceFilter` ran first.</span></span> <span data-ttu-id="e5b8d-263">`ShortCircuitingResourceFilter`Nejprve se spustí z důvodu jeho typu filtru nebo explicitního použití `Order` Vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-263">The `ShortCircuitingResourceFilter` runs first because of its filter type, or by explicit use of `Order` property.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

## <a name="dependency-injection"></a><span data-ttu-id="e5b8d-264">Injektáž závislostí</span><span class="sxs-lookup"><span data-stu-id="e5b8d-264">Dependency injection</span></span>

<span data-ttu-id="e5b8d-265">Filtry lze přidat podle typu nebo podle instance.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-265">Filters can be added by type or by instance.</span></span> <span data-ttu-id="e5b8d-266">Pokud je přidána instance, bude tato instance použita pro každý požadavek.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-266">If an instance is added, that instance is used for every request.</span></span> <span data-ttu-id="e5b8d-267">Pokud je přidán typ, je aktivován typ.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-267">If a type is added, it's type-activated.</span></span> <span data-ttu-id="e5b8d-268">Filtr aktivovaný typu znamená:</span><span class="sxs-lookup"><span data-stu-id="e5b8d-268">A type-activated filter means:</span></span>

* <span data-ttu-id="e5b8d-269">Instance se vytvoří pro každý požadavek.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-269">An instance is created for each request.</span></span>
* <span data-ttu-id="e5b8d-270">Jakékoli závislosti konstruktoru jsou vyplněny pomocí [Injektáže závislosti](xref:fundamentals/dependency-injection) (di).</span><span class="sxs-lookup"><span data-stu-id="e5b8d-270">Any constructor dependencies are populated by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span>

<span data-ttu-id="e5b8d-271">Filtry, které jsou implementovány jako atributy a přidány přímo do tříd kontroleru nebo metody akcí, nemohou mít závislosti konstruktoru poskytované [vkládáním závislostí](xref:fundamentals/dependency-injection) (di).</span><span class="sxs-lookup"><span data-stu-id="e5b8d-271">Filters that are implemented as attributes and added directly to controller classes or action methods cannot have constructor dependencies provided by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span> <span data-ttu-id="e5b8d-272">Pomocí DI nelze poskytnout závislosti konstruktoru:</span><span class="sxs-lookup"><span data-stu-id="e5b8d-272">Constructor dependencies cannot be provided by DI because:</span></span>

* <span data-ttu-id="e5b8d-273">V atributech musí být zadány parametry konstruktoru, kde jsou aplikovány.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-273">Attributes must have their constructor parameters supplied where they're applied.</span></span> 
* <span data-ttu-id="e5b8d-274">Toto je omezení způsobu fungování atributů.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-274">This is a limitation of how attributes work.</span></span>

<span data-ttu-id="e5b8d-275">Následující filtry podporují závislosti konstruktoru poskytované od DI:</span><span class="sxs-lookup"><span data-stu-id="e5b8d-275">The following filters support constructor dependencies provided from DI:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>
* <span data-ttu-id="e5b8d-276"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>implementováno u atributu.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-276"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implemented on the attribute.</span></span>

<span data-ttu-id="e5b8d-277">Předchozí filtry lze použít pro metodu kontroleru nebo akce:</span><span class="sxs-lookup"><span data-stu-id="e5b8d-277">The preceding filters can be applied to a controller or action method:</span></span>

<span data-ttu-id="e5b8d-278">Protokolovací nástroje jsou k dispozici z DI.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-278">Loggers are available from DI.</span></span> <span data-ttu-id="e5b8d-279">Ale nevytvářejte a používejte filtry čistě pro účely protokolování.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-279">However, avoid creating and using filters purely for logging purposes.</span></span> <span data-ttu-id="e5b8d-280">[Integrované protokolování rozhraní](xref:fundamentals/logging/index) obvykle zajišťuje, co je potřeba pro protokolování.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-280">The [built-in framework logging](xref:fundamentals/logging/index) typically provides what's needed for logging.</span></span> <span data-ttu-id="e5b8d-281">Protokolování přidáno do filtrů:</span><span class="sxs-lookup"><span data-stu-id="e5b8d-281">Logging added to filters:</span></span>

* <span data-ttu-id="e5b8d-282">Měl by se soustředit na obavy a chování obchodních domén, které jsou specifické pro filtr.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-282">Should focus on business domain concerns or behavior specific to the filter.</span></span>
* <span data-ttu-id="e5b8d-283">Neměli **byste** protokolovat akce nebo jiné události rozhraní.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-283">Should **not** log actions or other framework events.</span></span> <span data-ttu-id="e5b8d-284">Vestavěné filtry protokolují akce a události rozhraní.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-284">The built-in filters log actions and framework events.</span></span>

### <a name="servicefilterattribute"></a><span data-ttu-id="e5b8d-285">ServiceFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="e5b8d-285">ServiceFilterAttribute</span></span>

<span data-ttu-id="e5b8d-286">Typy implementace filtru služby jsou zaregistrované v `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="e5b8d-286">Service filter implementation types are registered in `ConfigureServices`.</span></span> <span data-ttu-id="e5b8d-287"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>Načte instanci filtru z di.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-287">A <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> retrieves an instance of the filter from DI.</span></span>

<span data-ttu-id="e5b8d-288">Následující kód ukazuje `AddHeaderResultServiceFilter` :</span><span class="sxs-lookup"><span data-stu-id="e5b8d-288">The following code shows the `AddHeaderResultServiceFilter`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="e5b8d-289">V následujícím kódu `AddHeaderResultServiceFilter` je přidán do kontejneru di:</span><span class="sxs-lookup"><span data-stu-id="e5b8d-289">In the following code, `AddHeaderResultServiceFilter` is added to the DI container:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Startup.cs?name=snippet&highlight=4)]

<span data-ttu-id="e5b8d-290">V následujícím kódu `ServiceFilter` atribut načte instanci `AddHeaderResultServiceFilter` filtru z di:</span><span class="sxs-lookup"><span data-stu-id="e5b8d-290">In the following code, the `ServiceFilter` attribute retrieves an instance of the `AddHeaderResultServiceFilter` filter from DI:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_ServiceFilter&highlight=1)]

<span data-ttu-id="e5b8d-291">Při použití `ServiceFilterAttribute` nastavte [ServiceFilterAttribute. IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span><span class="sxs-lookup"><span data-stu-id="e5b8d-291">When using `ServiceFilterAttribute`, setting [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span></span>

* <span data-ttu-id="e5b8d-292">Poskytuje nápovědu, že se instance filtru *dá* znovu použít mimo rozsah požadavku, který byl vytvořen v rámci.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-292">Provides a hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="e5b8d-293">Modul runtime ASP.NET Core nezaručuje:</span><span class="sxs-lookup"><span data-stu-id="e5b8d-293">The ASP.NET Core runtime doesn't guarantee:</span></span>

  * <span data-ttu-id="e5b8d-294">Vytvoří se jedna instance filtru.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-294">That a single instance of the filter will be created.</span></span>
  * <span data-ttu-id="e5b8d-295">Filtr nebude znovu vyžádán z kontejneru DI v pozdějším bodě.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-295">The filter will not be re-requested from the DI container at some later point.</span></span>

* <span data-ttu-id="e5b8d-296">Neměl by se používat s filtrem, který závisí na službách s jinou životností než singleton.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-296">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

 <span data-ttu-id="e5b8d-297"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>implementuje <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> .</span><span class="sxs-lookup"><span data-stu-id="e5b8d-297"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="e5b8d-298">`IFilterFactory`zpřístupňuje <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> metodu pro vytvoření <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-298">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="e5b8d-299">`CreateInstance`Načte zadaný typ z DI.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-299">`CreateInstance` loads the specified type from DI.</span></span>

### <a name="typefilterattribute"></a><span data-ttu-id="e5b8d-300">TypeFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="e5b8d-300">TypeFilterAttribute</span></span>

<span data-ttu-id="e5b8d-301"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>je podobný <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> , ale jeho typ není vyřešen přímo z kontejneru di.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-301"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> is similar to <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, but its type isn't resolved directly from the DI container.</span></span> <span data-ttu-id="e5b8d-302">Vytvoří instanci typu pomocí <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName> .</span><span class="sxs-lookup"><span data-stu-id="e5b8d-302">It instantiates the type by using <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span></span>

<span data-ttu-id="e5b8d-303">Protože `TypeFilterAttribute` typy nejsou vyřešeny přímo z kontejneru di:</span><span class="sxs-lookup"><span data-stu-id="e5b8d-303">Because `TypeFilterAttribute` types aren't resolved directly from the DI container:</span></span>

* <span data-ttu-id="e5b8d-304">Typy, na které se odkazuje pomocí, `TypeFilterAttribute` není nutné registrovat v kontejneru di.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-304">Types that are referenced using the `TypeFilterAttribute` don't need to be registered with the DI container.</span></span>  <span data-ttu-id="e5b8d-305">Mají své závislosti splněné kontejnerem DI.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-305">They do have their dependencies fulfilled by the DI container.</span></span>
* <span data-ttu-id="e5b8d-306">`TypeFilterAttribute`může volitelně přijmout argumenty konstruktoru pro typ.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-306">`TypeFilterAttribute` can optionally accept constructor arguments for the type.</span></span>

<span data-ttu-id="e5b8d-307">Při použití `TypeFilterAttribute` nastavte [TypeFilterAttribute. IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span><span class="sxs-lookup"><span data-stu-id="e5b8d-307">When using `TypeFilterAttribute`, setting [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span></span>
* <span data-ttu-id="e5b8d-308">Poskytuje nápovědu, že se instance filtru *dá* znovu použít mimo rozsah požadavku, který byl vytvořen v rámci.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-308">Provides hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="e5b8d-309">Modul runtime ASP.NET Core neposkytuje žádné záruky, že se vytvoří jediná instance filtru.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-309">The ASP.NET Core runtime provides no guarantees that a single instance of the filter will be created.</span></span>

* <span data-ttu-id="e5b8d-310">Neměl by se používat s filtrem, který závisí na službách s jinou životností než singleton.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-310">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

<span data-ttu-id="e5b8d-311">Následující příklad ukazuje, jak předat argumenty typu pomocí `TypeFilterAttribute` :</span><span class="sxs-lookup"><span data-stu-id="e5b8d-311">The following example shows how to pass arguments to a type using `TypeFilterAttribute`:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_TypeFilter&highlight=1,2)]

<!-- 
https://localhost:5001/home/hi?name=joe
VS debug window shows 
FiltersSample.Filters.LogConstantFilter:Information: Method 'Hi' called
-->

## <a name="authorization-filters"></a><span data-ttu-id="e5b8d-312">Filtry autorizace</span><span class="sxs-lookup"><span data-stu-id="e5b8d-312">Authorization filters</span></span>

<span data-ttu-id="e5b8d-313">Filtry autorizace:</span><span class="sxs-lookup"><span data-stu-id="e5b8d-313">Authorization filters:</span></span>

* <span data-ttu-id="e5b8d-314">Jsou první filtry spouštěny v kanálu filtru.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-314">Are the first filters run in the filter pipeline.</span></span>
* <span data-ttu-id="e5b8d-315">Řízení přístupu k metodám akcí.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-315">Control access to action methods.</span></span>
* <span data-ttu-id="e5b8d-316">Před metodou, ale ne po metodě.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-316">Have a before method, but no after method.</span></span>

<span data-ttu-id="e5b8d-317">Vlastní autorizační filtry vyžadují vlastní autorizační rozhraní.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-317">Custom authorization filters require a custom authorization framework.</span></span> <span data-ttu-id="e5b8d-318">Preferovat konfiguraci autorizačních zásad nebo psaní vlastních zásad autorizace při psaní vlastního filtru.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-318">Prefer configuring the authorization policies or writing a custom authorization policy over writing a custom filter.</span></span> <span data-ttu-id="e5b8d-319">Vestavěný autorizační filtr:</span><span class="sxs-lookup"><span data-stu-id="e5b8d-319">The built-in authorization filter:</span></span>

* <span data-ttu-id="e5b8d-320">Volá autorizační systém.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-320">Calls the authorization system.</span></span>
* <span data-ttu-id="e5b8d-321">Neautorizuje požadavky.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-321">Does not authorize requests.</span></span>

<span data-ttu-id="e5b8d-322">Negenerovat **výjimky** v rámci autorizačních filtrů:</span><span class="sxs-lookup"><span data-stu-id="e5b8d-322">Do **not** throw exceptions within authorization filters:</span></span>

* <span data-ttu-id="e5b8d-323">Výjimka nebude zpracována.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-323">The exception will not be handled.</span></span>
* <span data-ttu-id="e5b8d-324">Filtry výjimek nebudou zpracovávat výjimku.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-324">Exception filters will not handle the exception.</span></span>

<span data-ttu-id="e5b8d-325">Zvažte vydání výzvy, pokud dojde k výjimce ve autorizačním filtru.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-325">Consider issuing a challenge when an exception occurs in an authorization filter.</span></span>

<span data-ttu-id="e5b8d-326">Přečtěte si další informace o [autorizaci](xref:security/authorization/introduction).</span><span class="sxs-lookup"><span data-stu-id="e5b8d-326">Learn more about [Authorization](xref:security/authorization/introduction).</span></span>

## <a name="resource-filters"></a><span data-ttu-id="e5b8d-327">Filtry prostředků</span><span class="sxs-lookup"><span data-stu-id="e5b8d-327">Resource filters</span></span>

<span data-ttu-id="e5b8d-328">Filtry prostředků:</span><span class="sxs-lookup"><span data-stu-id="e5b8d-328">Resource filters:</span></span>

* <span data-ttu-id="e5b8d-329">Implementujte <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> rozhraní nebo <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> .</span><span class="sxs-lookup"><span data-stu-id="e5b8d-329">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> interface.</span></span>
* <span data-ttu-id="e5b8d-330">Provádění zabalí většinu kanálu filtru.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-330">Execution wraps most of the filter pipeline.</span></span>
* <span data-ttu-id="e5b8d-331">Před filtry prostředků se spouštějí jenom [filtry autorizace](#authorization-filters) .</span><span class="sxs-lookup"><span data-stu-id="e5b8d-331">Only [Authorization filters](#authorization-filters) run before resource filters.</span></span>

<span data-ttu-id="e5b8d-332">Filtry prostředků jsou užitečné pro krátkodobé okruhy v kanálu.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-332">Resource filters are useful to short-circuit most of the pipeline.</span></span> <span data-ttu-id="e5b8d-333">Například filtr ukládání do mezipaměti může zabránit zbývajícímu kanálu v případě přístupů do mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-333">For example, a caching filter can avoid the rest of the pipeline on a cache hit.</span></span>

<span data-ttu-id="e5b8d-334">Příklady filtru prostředků:</span><span class="sxs-lookup"><span data-stu-id="e5b8d-334">Resource filter examples:</span></span>

* <span data-ttu-id="e5b8d-335">[Filtr prostředků se zkrácenými okruhy](#short-circuiting-resource-filter) zobrazenými dříve.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-335">[The short-circuiting resource filter](#short-circuiting-resource-filter) shown previously.</span></span>
* <span data-ttu-id="e5b8d-336">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span><span class="sxs-lookup"><span data-stu-id="e5b8d-336">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span></span>

  * <span data-ttu-id="e5b8d-337">Zabraňuje vazbě modelu v přístupu k datům formuláře.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-337">Prevents model binding from accessing the form data.</span></span>
  * <span data-ttu-id="e5b8d-338">Používá se pro nahrávání velkých souborů, aby se zabránilo čtení dat z formuláře do paměti.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-338">Used for large file uploads to prevent the form data from being read into memory.</span></span>

## <a name="action-filters"></a><span data-ttu-id="e5b8d-339">Filtry akcí</span><span class="sxs-lookup"><span data-stu-id="e5b8d-339">Action filters</span></span>

<span data-ttu-id="e5b8d-340">Filtry **akcí se nevztahují** na Razor stránky.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-340">Action filters do **not** apply to Razor Pages.</span></span> Razor<span data-ttu-id="e5b8d-341">Stránky podporují <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> a <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span><span class="sxs-lookup"><span data-stu-id="e5b8d-341"> Pages supports <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span></span> <span data-ttu-id="e5b8d-342">Další informace najdete v tématu [metody filtrování pro Razor stránky](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="e5b8d-342">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

<span data-ttu-id="e5b8d-343">Filtry akcí:</span><span class="sxs-lookup"><span data-stu-id="e5b8d-343">Action filters:</span></span>

* <span data-ttu-id="e5b8d-344">Implementujte <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> rozhraní nebo <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> .</span><span class="sxs-lookup"><span data-stu-id="e5b8d-344">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> interface.</span></span>
* <span data-ttu-id="e5b8d-345">Jejich spuštění obklopuje provádění metod akcí.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-345">Their execution surrounds the execution of action methods.</span></span>

<span data-ttu-id="e5b8d-346">Následující kód ukazuje filtr vzorové akce:</span><span class="sxs-lookup"><span data-stu-id="e5b8d-346">The following code shows a sample action filter:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="e5b8d-347"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext>Poskytuje následující vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="e5b8d-347">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> provides the following properties:</span></span>

* <span data-ttu-id="e5b8d-348"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments>– povolí čtení vstupů do metody Action.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-348"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> - enables reading the inputs to an action method.</span></span>
* <span data-ttu-id="e5b8d-349"><xref:Microsoft.AspNetCore.Mvc.Controller>– povolí manipulaci s instancí kontroleru.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-349"><xref:Microsoft.AspNetCore.Mvc.Controller> - enables manipulating the controller instance.</span></span>
* <span data-ttu-id="e5b8d-350"><xref:System.Web.Mvc.ActionExecutingContext.Result>– nastavení `Result` krátkodobého provádění metody akce a následných filtrů akcí.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-350"><xref:System.Web.Mvc.ActionExecutingContext.Result> - setting `Result` short-circuits execution of the action method and subsequent action filters.</span></span>

<span data-ttu-id="e5b8d-351">Vyvolání výjimky v metodě akce:</span><span class="sxs-lookup"><span data-stu-id="e5b8d-351">Throwing an exception in an action method:</span></span>

* <span data-ttu-id="e5b8d-352">Zabraňuje spuštění dalších filtrů.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-352">Prevents running of subsequent filters.</span></span>
* <span data-ttu-id="e5b8d-353">Na rozdíl od nastavení `Result` se místo úspěšného výsledku považuje za selhání.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-353">Unlike setting `Result`, is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="e5b8d-354"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext>Poskytuje `Controller` a `Result` plus následující vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="e5b8d-354">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> provides `Controller` and `Result` plus the following properties:</span></span>

* <span data-ttu-id="e5b8d-355"><xref:System.Web.Mvc.ActionExecutedContext.Canceled>-True, pokud bylo provedení akce zkráceno jiným filtrem.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-355"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> - True if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="e5b8d-356"><xref:System.Web.Mvc.ActionExecutedContext.Exception>-Hodnota není null, pokud akce nebo dříve vyvolala filtr akcí.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-356"><xref:System.Web.Mvc.ActionExecutedContext.Exception> - Non-null if the action or a previously run action filter threw an exception.</span></span> <span data-ttu-id="e5b8d-357">Nastavení této vlastnosti na hodnotu null:</span><span class="sxs-lookup"><span data-stu-id="e5b8d-357">Setting this property to null:</span></span>

  * <span data-ttu-id="e5b8d-358">Efektivně zpracovává výjimku.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-358">Effectively handles the exception.</span></span>
  * <span data-ttu-id="e5b8d-359">`Result`je spuštěn, jako kdyby byl vrácen z metody Action.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-359">`Result` is executed as if it was returned from the action method.</span></span>

<span data-ttu-id="e5b8d-360">V případě `IAsyncActionFilter` volání <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> :</span><span class="sxs-lookup"><span data-stu-id="e5b8d-360">For an `IAsyncActionFilter`, a call to the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span></span>

* <span data-ttu-id="e5b8d-361">Provede všechny následné filtry akcí a metodu Action.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-361">Executes any subsequent action filters and the action method.</span></span>
* <span data-ttu-id="e5b8d-362">Vrací objekt `ActionExecutedContext`.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-362">Returns `ActionExecutedContext`.</span></span>

<span data-ttu-id="e5b8d-363">Do krátkodobého okruhu, přiřaďte <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> k instanci výsledku a Nevolejte `next` (a `ActionExecutionDelegate` ).</span><span class="sxs-lookup"><span data-stu-id="e5b8d-363">To short-circuit, assign <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> to a result instance and don't call `next` (the `ActionExecutionDelegate`).</span></span>

<span data-ttu-id="e5b8d-364">Rozhraní poskytuje abstrakci <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> , která může být podtříd.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-364">The framework provides an abstract <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> that can be subclassed.</span></span>

<span data-ttu-id="e5b8d-365">`OnActionExecuting`Filtr akcí lze použít k těmto akcím:</span><span class="sxs-lookup"><span data-stu-id="e5b8d-365">The `OnActionExecuting` action filter can be used to:</span></span>

* <span data-ttu-id="e5b8d-366">Ověří stav modelu.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-366">Validate model state.</span></span>
* <span data-ttu-id="e5b8d-367">Pokud je stav neplatný, vrátí se chyba.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-367">Return an error if the state is invalid.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet)]

> [!NOTE]
> <span data-ttu-id="e5b8d-368">Řadiče s poznámkou s `[ApiController]` atributem automaticky ověří stav modelu a vrátí odpověď 400.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-368">Controllers annotated with the `[ApiController]` attribute automatically validate model state and return a 400 response.</span></span> <span data-ttu-id="e5b8d-369">Další informace najdete v tématu [Automatické odpovědi HTTP 400](xref:web-api/index#automatic-http-400-responses).</span><span class="sxs-lookup"><span data-stu-id="e5b8d-369">For more information, see [Automatic HTTP 400 responses](xref:web-api/index#automatic-http-400-responses).</span></span>

<span data-ttu-id="e5b8d-370">`OnActionExecuted`Metoda se spustí za metodou akce:</span><span class="sxs-lookup"><span data-stu-id="e5b8d-370">The `OnActionExecuted` method runs after the action method:</span></span>

* <span data-ttu-id="e5b8d-371">A mohou zobrazit výsledky akce a manipulovat s nimi prostřednictvím <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> Vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-371">And can see and manipulate the results of the action through the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> property.</span></span>
* <span data-ttu-id="e5b8d-372"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled>je nastaven na hodnotu true, pokud bylo provedení akce zkráceno jiným filtrem.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-372"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> is set to true if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="e5b8d-373"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception>je nastavena na hodnotu jinou než null, pokud akce nebo filtr následné akce vyvolaly výjimku.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-373"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> is set to a non-null value if the action or a subsequent action filter threw an exception.</span></span> <span data-ttu-id="e5b8d-374">Nastavení `Exception` na hodnotu null:</span><span class="sxs-lookup"><span data-stu-id="e5b8d-374">Setting `Exception` to null:</span></span>

  * <span data-ttu-id="e5b8d-375">Efektivně zpracovává výjimku.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-375">Effectively handles an exception.</span></span>
  * <span data-ttu-id="e5b8d-376">`ActionExecutedContext.Result`je spuštěn, jako kdyby byl vrácen normálně z metody Action.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-376">`ActionExecutedContext.Result` is executed as if it were returned normally from the action method.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet2&higlight=12-99)]

## <a name="exception-filters"></a><span data-ttu-id="e5b8d-377">Filtry výjimek</span><span class="sxs-lookup"><span data-stu-id="e5b8d-377">Exception filters</span></span>

<span data-ttu-id="e5b8d-378">Filtry výjimek:</span><span class="sxs-lookup"><span data-stu-id="e5b8d-378">Exception filters:</span></span>

* <span data-ttu-id="e5b8d-379">Implementujte <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> nebo <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter> .</span><span class="sxs-lookup"><span data-stu-id="e5b8d-379">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span></span>
* <span data-ttu-id="e5b8d-380">Dá se použít k implementaci běžných zásad zpracování chyb.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-380">Can be used to implement common error handling policies.</span></span>

<span data-ttu-id="e5b8d-381">Následující ukázkový filtr výjimek používá vlastní zobrazení chyb pro zobrazení podrobností o výjimkách, ke kterým dochází při vývoji aplikace:</span><span class="sxs-lookup"><span data-stu-id="e5b8d-381">The following sample exception filter uses a custom error view to display details about exceptions that occur when the app is in development:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/CustomExceptionFilter.cs?name=snippet_ExceptionFilter&highlight=16-19)]

<span data-ttu-id="e5b8d-382">Následující kód testuje filtr výjimky:</span><span class="sxs-lookup"><span data-stu-id="e5b8d-382">The following code tests the exception filter:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Controllers/FailingController.cs?name=snippet)]

<span data-ttu-id="e5b8d-383">Filtry výjimek:</span><span class="sxs-lookup"><span data-stu-id="e5b8d-383">Exception filters:</span></span>

* <span data-ttu-id="e5b8d-384">Nemusíte mít události před a po.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-384">Don't have before and after events.</span></span>
* <span data-ttu-id="e5b8d-385">Implementujte <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> nebo <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*> .</span><span class="sxs-lookup"><span data-stu-id="e5b8d-385">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span></span>
* <span data-ttu-id="e5b8d-386">Zpracování neošetřených výjimek, ke kterým dochází při Razor vytváření stránky nebo kontroleru, [vázání modelů](xref:mvc/models/model-binding), filtrů akcí nebo metod akcí.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-386">Handle unhandled exceptions that occur in Razor Page or controller creation, [model binding](xref:mvc/models/model-binding), action filters, or action methods.</span></span>
* <span data-ttu-id="e5b8d-387">**Nezachycujte** výjimky, ke kterým dochází v filtrech prostředků, výsledných filtrech nebo v důsledku provádění výsledků MVC.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-387">Do **not** catch exceptions that occur in resource filters, result filters, or MVC result execution.</span></span>

<span data-ttu-id="e5b8d-388">Chcete-li zpracovat výjimku, nastavte <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> vlastnost na `true` nebo zapište odpověď.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-388">To handle an exception, set the <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> property to `true` or write a response.</span></span> <span data-ttu-id="e5b8d-389">Tím se zastaví šíření výjimky.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-389">This stops propagation of the exception.</span></span> <span data-ttu-id="e5b8d-390">Filtr výjimek nemůže vypnout výjimku na "úspěch".</span><span class="sxs-lookup"><span data-stu-id="e5b8d-390">An exception filter can't turn an exception into a "success".</span></span> <span data-ttu-id="e5b8d-391">To může provést pouze filtr akcí.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-391">Only an action filter can do that.</span></span>

<span data-ttu-id="e5b8d-392">Filtry výjimek:</span><span class="sxs-lookup"><span data-stu-id="e5b8d-392">Exception filters:</span></span>

* <span data-ttu-id="e5b8d-393">Jsou vhodné pro vytváření přesahů výjimek, ke kterým dochází v rámci akcí.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-393">Are good for trapping exceptions that occur within actions.</span></span>
* <span data-ttu-id="e5b8d-394">Nejsou tak flexibilní jako middleware při zpracování chyb.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-394">Are not as flexible as error handling middleware.</span></span>

<span data-ttu-id="e5b8d-395">Preferovat middleware pro zpracování výjimek.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-395">Prefer middleware for exception handling.</span></span> <span data-ttu-id="e5b8d-396">Filtry výjimek použijte pouze v případě, že se zpracování chyb *liší* v závislosti na tom, která metoda Action je volána.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-396">Use exception filters only where error handling *differs* based on which action method is called.</span></span> <span data-ttu-id="e5b8d-397">Aplikace může například mít metody akcí pro koncové body rozhraní API i pro zobrazení/HTML.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-397">For example, an app might have action methods for both API endpoints and for views/HTML.</span></span> <span data-ttu-id="e5b8d-398">Koncové body rozhraní API mohou vracet informace o chybě jako JSON, zatímco akce na základě zobrazení by mohly vracet chybovou stránku jako HTML.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-398">The API endpoints could return error information as JSON, while the view-based actions could return an error page as HTML.</span></span>

## <a name="result-filters"></a><span data-ttu-id="e5b8d-399">Filtry výsledků</span><span class="sxs-lookup"><span data-stu-id="e5b8d-399">Result filters</span></span>

<span data-ttu-id="e5b8d-400">Filtry výsledků:</span><span class="sxs-lookup"><span data-stu-id="e5b8d-400">Result filters:</span></span>

* <span data-ttu-id="e5b8d-401">Implementace rozhraní:</span><span class="sxs-lookup"><span data-stu-id="e5b8d-401">Implement an interface:</span></span>
  * <span data-ttu-id="e5b8d-402"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> nebo <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="e5b8d-402"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
  * <span data-ttu-id="e5b8d-403"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> nebo <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span><span class="sxs-lookup"><span data-stu-id="e5b8d-403"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span></span>
* <span data-ttu-id="e5b8d-404">Jejich spuštění obklopuje provádění výsledků akcí.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-404">Their execution surrounds the execution of action results.</span></span>

### <a name="iresultfilter-and-iasyncresultfilter"></a><span data-ttu-id="e5b8d-405">IResultFilter a IAsyncResultFilter</span><span class="sxs-lookup"><span data-stu-id="e5b8d-405">IResultFilter and IAsyncResultFilter</span></span>

<span data-ttu-id="e5b8d-406">Následující kód ukazuje filtr výsledků, který přidá hlavičku protokolu HTTP:</span><span class="sxs-lookup"><span data-stu-id="e5b8d-406">The following code shows a result filter that adds an HTTP header:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="e5b8d-407">Typ výsledku, který se má provést, závisí na akci.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-407">The kind of result being executed depends on the action.</span></span> <span data-ttu-id="e5b8d-408">Akce vracející zobrazení zahrnuje všechny zpracování Razor jako součást prováděné <xref:Microsoft.AspNetCore.Mvc.ViewResult> .</span><span class="sxs-lookup"><span data-stu-id="e5b8d-408">An action returning a view includes all razor processing as part of the <xref:Microsoft.AspNetCore.Mvc.ViewResult> being executed.</span></span> <span data-ttu-id="e5b8d-409">Metoda rozhraní API může provést určitou serializaci v rámci provádění výsledku.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-409">An API method might perform some serialization as part of the execution of the result.</span></span> <span data-ttu-id="e5b8d-410">Přečtěte si další informace o [výsledcích akcí](xref:mvc/controllers/actions).</span><span class="sxs-lookup"><span data-stu-id="e5b8d-410">Learn more about [action results](xref:mvc/controllers/actions).</span></span>

<span data-ttu-id="e5b8d-411">Filtry výsledků se spustí pouze v případě, že akce nebo filtr akcí vytvoří výsledek akce.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-411">Result filters are only executed when an action or action filter produces an action result.</span></span> <span data-ttu-id="e5b8d-412">Filtry výsledků nejsou provedeny v těchto případech:</span><span class="sxs-lookup"><span data-stu-id="e5b8d-412">Result filters are not executed when:</span></span>

* <span data-ttu-id="e5b8d-413">Filtr autorizace nebo filtr prostředků pro krátké okruhy kanálu.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-413">An authorization filter or resource filter short-circuits the pipeline.</span></span>
* <span data-ttu-id="e5b8d-414">Filtr výjimek zpracovává výjimku tím, že vyprodukuje výsledek akce.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-414">An exception filter handles an exception by producing an action result.</span></span>

<span data-ttu-id="e5b8d-415"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName>Metoda může zkrátit provádění výsledků akce a následných filtrů výsledků nastavením <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> na `true` .</span><span class="sxs-lookup"><span data-stu-id="e5b8d-415">The <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> method can short-circuit execution of the action result and subsequent result filters by setting <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> to `true`.</span></span> <span data-ttu-id="e5b8d-416">Zapište do objektu Response v případě krátkého okruhu, abyste se vyhnuli vygenerování prázdné odpovědi.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-416">Write to the response object when short-circuiting to avoid generating an empty response.</span></span> <span data-ttu-id="e5b8d-417">Vyvolání výjimky v `IResultFilter.OnResultExecuting` :</span><span class="sxs-lookup"><span data-stu-id="e5b8d-417">Throwing an exception in `IResultFilter.OnResultExecuting`:</span></span>

* <span data-ttu-id="e5b8d-418">Zabraňuje spuštění výsledků akce a následných filtrů.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-418">Prevents execution of the action result and subsequent filters.</span></span>
* <span data-ttu-id="e5b8d-419">Se považuje za selhání namísto úspěšného výsledku.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-419">Is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="e5b8d-420">Při <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> spuštění metody je odpověď pravděpodobně již odeslána klientovi.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-420">When the <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> method runs, the response has probably already been sent to the client.</span></span> <span data-ttu-id="e5b8d-421">Pokud byla odpověď již odeslána klientovi, nelze ji změnit.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-421">If the response has already been sent to the client, it cannot be changed.</span></span>

<span data-ttu-id="e5b8d-422">`ResultExecutedContext.Canceled`je nastaven na hodnotu, `true` Pokud je spuštění výsledku akce zkráceno jiným filtrem.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-422">`ResultExecutedContext.Canceled` is set to `true` if the action result execution was short-circuited by another filter.</span></span>

<span data-ttu-id="e5b8d-423">`ResultExecutedContext.Exception`je nastavena na hodnotu jinou než null, pokud výsledek akce nebo následný filtr výsledků vyvolal výjimku.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-423">`ResultExecutedContext.Exception` is set to a non-null value if the action result or a subsequent result filter threw an exception.</span></span> <span data-ttu-id="e5b8d-424">Nastavení `Exception` na hodnotu null efektivně zpracuje výjimku a zabrání vyvolání výjimky později v kanálu.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-424">Setting `Exception` to null effectively handles an exception and prevents the exception from being thrown again later in the pipeline.</span></span> <span data-ttu-id="e5b8d-425">Neexistuje žádný spolehlivý způsob, jak zapisovat data do odpovědi při zpracování výjimky ve filtru výsledků.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-425">There is no reliable way to write data to a response when handling an exception in a result filter.</span></span> <span data-ttu-id="e5b8d-426">Pokud byly hlavičky vyprázdněny do klienta, pokud výsledek akce vyvolá výjimku, neexistuje žádný spolehlivý mechanismus pro odeslání kódu chyby.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-426">If the headers have been flushed to the client when an action result throws an exception, there's no reliable mechanism to send a failure code.</span></span>

<span data-ttu-id="e5b8d-427">V případě <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter> volání `await next` na se <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> spustí všechny následné filtry výsledků a výsledek akce.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-427">For an <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, a call to `await next` on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> executes any subsequent result filters and the action result.</span></span> <span data-ttu-id="e5b8d-428">Do krátkodobého okruhu nastavte [ResultExecutingContext. Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) na `true` a Nevolejte na `ResultExecutionDelegate` :</span><span class="sxs-lookup"><span data-stu-id="e5b8d-428">To short-circuit, set [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) to `true` and don't call the `ResultExecutionDelegate`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MyAsyncResponseFilter.cs?name=snippet)]

<span data-ttu-id="e5b8d-429">Rozhraní poskytuje abstrakci `ResultFilterAttribute` , která může být podtříd.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-429">The framework provides an abstract `ResultFilterAttribute` that can be subclassed.</span></span> <span data-ttu-id="e5b8d-430">Dříve uvedená třída [AddHeaderAttribute](#add-header-attribute) je příkladem atributu filtru výsledků.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-430">The [AddHeaderAttribute](#add-header-attribute) class shown previously is an example of a result filter attribute.</span></span>

### <a name="ialwaysrunresultfilter-and-iasyncalwaysrunresultfilter"></a><span data-ttu-id="e5b8d-431">IAlwaysRunResultFilter a IAsyncAlwaysRunResultFilter</span><span class="sxs-lookup"><span data-stu-id="e5b8d-431">IAlwaysRunResultFilter and IAsyncAlwaysRunResultFilter</span></span>

<span data-ttu-id="e5b8d-432"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter>Rozhraní a <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> deklaruje <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> implementaci, která je spuštěna pro všechny výsledky akce.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-432">The <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> interfaces declare an <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> implementation that runs for all action results.</span></span> <span data-ttu-id="e5b8d-433">To zahrnuje výsledky akcí, které vytvořil:</span><span class="sxs-lookup"><span data-stu-id="e5b8d-433">This includes action results produced by:</span></span>

* <span data-ttu-id="e5b8d-434">Filtry autorizace a filtry prostředků, které mají krátký okruh.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-434">Authorization filters and resource filters that short-circuit.</span></span>
* <span data-ttu-id="e5b8d-435">Filtry výjimek.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-435">Exception filters.</span></span>

<span data-ttu-id="e5b8d-436">Například následující filtr vždy spustí a nastaví výsledek akce ( <xref:Microsoft.AspNetCore.Mvc.ObjectResult> ) s *422 nezpracovaným* stavovým kódem entity, pokud se nezdaří vyjednávání obsahu:</span><span class="sxs-lookup"><span data-stu-id="e5b8d-436">For example, the following filter always runs and sets an action result (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) with a *422 Unprocessable Entity* status code when content negotiation fails:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/UnprocessableResultFilter.cs?name=snippet)]

### <a name="ifilterfactory"></a><span data-ttu-id="e5b8d-437">IFilterFactory</span><span class="sxs-lookup"><span data-stu-id="e5b8d-437">IFilterFactory</span></span>

<span data-ttu-id="e5b8d-438"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>implementuje <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> .</span><span class="sxs-lookup"><span data-stu-id="e5b8d-438"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span></span> <span data-ttu-id="e5b8d-439">Proto `IFilterFactory` může být instance použita jako `IFilterMetadata` instance kdekoli v kanálu filtru.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-439">Therefore, an `IFilterFactory` instance can be used as an `IFilterMetadata` instance anywhere in the filter pipeline.</span></span> <span data-ttu-id="e5b8d-440">Když se modul runtime připraví k vyvolání filtru, pokusí se ho přetypovat na `IFilterFactory` .</span><span class="sxs-lookup"><span data-stu-id="e5b8d-440">When the runtime prepares to invoke the filter, it attempts to cast it to an `IFilterFactory`.</span></span> <span data-ttu-id="e5b8d-441">Pokud je toto přetypování úspěšné, <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> je volána metoda pro vytvoření `IFilterMetadata` instance, která je vyvolána.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-441">If that cast succeeds, the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method is called to create the `IFilterMetadata` instance that is invoked.</span></span> <span data-ttu-id="e5b8d-442">To poskytuje flexibilní návrh, protože přesný kanál filtru není nutné nastavit explicitně při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-442">This provides a flexible design, since the precise filter pipeline doesn't need to be set explicitly when the app starts.</span></span>

<span data-ttu-id="e5b8d-443">`IFilterFactory`může být implementováno pomocí implementace vlastního atributu jako jiný přístup k vytváření filtrů:</span><span class="sxs-lookup"><span data-stu-id="e5b8d-443">`IFilterFactory` can be implemented using custom attribute implementations as another approach to creating filters:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/AddHeaderWithFactoryAttribute.cs?name=snippet_IFilterFactory&highlight=1,4,5,6,7)]

<span data-ttu-id="e5b8d-444">Filtr je použit v následujícím kódu:</span><span class="sxs-lookup"><span data-stu-id="e5b8d-444">The filter is applied in the following code:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet3&highlight=21)]

<span data-ttu-id="e5b8d-445">Otestujte předchozí kód spuštěním [ukázky stahování](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample):</span><span class="sxs-lookup"><span data-stu-id="e5b8d-445">Test the preceding code by running the [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample):</span></span>

* <span data-ttu-id="e5b8d-446">Vyvolejte vývojářské nástroje F12.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-446">Invoke the F12 developer tools.</span></span>
* <span data-ttu-id="e5b8d-447">Přejděte na adresu `https://localhost:5001/Sample/HeaderWithFactory`.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-447">Navigate to `https://localhost:5001/Sample/HeaderWithFactory`.</span></span>

<span data-ttu-id="e5b8d-448">Vývojářské nástroje F12 zobrazují následující hlavičky odpovědí přidané ukázkovým kódem:</span><span class="sxs-lookup"><span data-stu-id="e5b8d-448">The F12 developer tools display the following response headers added by the sample code:</span></span>

* <span data-ttu-id="e5b8d-449">**Autor:**`Rick Anderson`</span><span class="sxs-lookup"><span data-stu-id="e5b8d-449">**author:** `Rick Anderson`</span></span>
* <span data-ttu-id="e5b8d-450">**globaladdheader:**`Result filter added to MvcOptions.Filters`</span><span class="sxs-lookup"><span data-stu-id="e5b8d-450">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span></span>
* <span data-ttu-id="e5b8d-451">**interní:**`My header`</span><span class="sxs-lookup"><span data-stu-id="e5b8d-451">**internal:** `My header`</span></span>

<span data-ttu-id="e5b8d-452">Předchozí kód vytvoří hlavičku **interního:** `My header` Response.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-452">The preceding code creates the **internal:** `My header` response header.</span></span>

### <a name="ifilterfactory-implemented-on-an-attribute"></a><span data-ttu-id="e5b8d-453">IFilterFactory implementované u atributu</span><span class="sxs-lookup"><span data-stu-id="e5b8d-453">IFilterFactory implemented on an attribute</span></span>

<!-- Review 
This section needs to be rewritten.
What's a non-named attribute?
-->

<span data-ttu-id="e5b8d-454">Filtry, které implementují, `IFilterFactory` jsou užitečné pro filtry, které:</span><span class="sxs-lookup"><span data-stu-id="e5b8d-454">Filters that implement `IFilterFactory` are useful for filters that:</span></span>

* <span data-ttu-id="e5b8d-455">Nevyžadovat předávání parametrů</span><span class="sxs-lookup"><span data-stu-id="e5b8d-455">Don't require passing parameters.</span></span>
* <span data-ttu-id="e5b8d-456">Musí mít závislosti konstruktoru, které musí vyplnit DI.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-456">Have constructor dependencies that need to be filled by DI.</span></span>

<span data-ttu-id="e5b8d-457"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>implementuje <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> .</span><span class="sxs-lookup"><span data-stu-id="e5b8d-457"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="e5b8d-458">`IFilterFactory`zpřístupňuje <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> metodu pro vytvoření <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-458">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="e5b8d-459">`CreateInstance`Načte zadaný typ z kontejneru Services (DI).</span><span class="sxs-lookup"><span data-stu-id="e5b8d-459">`CreateInstance` loads the specified type from the services container (DI).</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/SampleActionFilterAttribute.cs?name=snippet_TypeFilterAttribute&highlight=1,3,7)]

<span data-ttu-id="e5b8d-460">Následující kód ukazuje tři přístupy k použití `[SampleActionFilter]` :</span><span class="sxs-lookup"><span data-stu-id="e5b8d-460">The following code shows three approaches to applying the `[SampleActionFilter]`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet&highlight=1)]

<span data-ttu-id="e5b8d-461">V předchozím kódu upravení metoda s jako `[SampleActionFilter]` upřednostňovaným přístupem k použití `SampleActionFilter` .</span><span class="sxs-lookup"><span data-stu-id="e5b8d-461">In the preceding code, decorating the method with `[SampleActionFilter]` is the preferred approach to applying the `SampleActionFilter`.</span></span>

## <a name="using-middleware-in-the-filter-pipeline"></a><span data-ttu-id="e5b8d-462">Použití middlewaru v kanálu filtru</span><span class="sxs-lookup"><span data-stu-id="e5b8d-462">Using middleware in the filter pipeline</span></span>

<span data-ttu-id="e5b8d-463">Filtry prostředků fungují jako [middleware](xref:fundamentals/middleware/index) v tom, že obklopují provádění všech položek, které jsou později v kanálu.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-463">Resource filters work like [middleware](xref:fundamentals/middleware/index) in that they surround the execution of everything that comes later in the pipeline.</span></span> <span data-ttu-id="e5b8d-464">Filtry se ale liší od middlewaru v tom, že jsou součástí modulu runtime, což znamená, že mají přístup k kontextu a sestavám.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-464">But filters differ from middleware in that they're part of the runtime, which means that they have access to context and constructs.</span></span>

<span data-ttu-id="e5b8d-465">Chcete-li použít middleware jako filtr, vytvořte typ s `Configure` metodou, která určuje middleware pro vložení do kanálu filtru.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-465">To use middleware as a filter, create a type with a `Configure` method that specifies the middleware to inject into the filter pipeline.</span></span> <span data-ttu-id="e5b8d-466">Následující příklad používá middleware Localization k vytvoření aktuální jazykové verze pro požadavek:</span><span class="sxs-lookup"><span data-stu-id="e5b8d-466">The following example uses the localization middleware to establish the current culture for a request:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LocalizationPipeline.cs?name=snippet_MiddlewareFilter&highlight=3,22)]

<span data-ttu-id="e5b8d-467">Pomocí <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> Spusťte middleware:</span><span class="sxs-lookup"><span data-stu-id="e5b8d-467">Use the <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> to run the middleware:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_MiddlewareFilter&highlight=2)]

<span data-ttu-id="e5b8d-468">Filtry middlewaru jsou spouštěny ve stejné fázi kanálu filtru jako filtry prostředků, před vazbami modelů a po zbytek kanálu.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-468">Middleware filters run at the same stage of the filter pipeline as Resource filters, before model binding and after the rest of the pipeline.</span></span>

## <a name="next-actions"></a><span data-ttu-id="e5b8d-469">Další akce</span><span class="sxs-lookup"><span data-stu-id="e5b8d-469">Next actions</span></span>

* <span data-ttu-id="e5b8d-470">Viz [metody filtru pro Razor stránky](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="e5b8d-470">See [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>
* <span data-ttu-id="e5b8d-471">Pro experimentování s filtry, [stažení, otestování a úpravy ukázky GitHubu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample).</span><span class="sxs-lookup"><span data-stu-id="e5b8d-471">To experiment with filters, [download, test, and modify the GitHub sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="e5b8d-472">[Kirka Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Dykstra](https://github.com/tdykstra/)a [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="e5b8d-472">By [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="e5b8d-473">*Filtry* v ASP.NET Core umožňují spuštění kódu před nebo po určitých fázích v kanálu zpracování požadavků.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-473">*Filters* in ASP.NET Core allow code to be run before or after specific stages in the request processing pipeline.</span></span>

<span data-ttu-id="e5b8d-474">Předdefinované filtry zpracovávají úlohy, jako například:</span><span class="sxs-lookup"><span data-stu-id="e5b8d-474">Built-in filters handle tasks such as:</span></span>

* <span data-ttu-id="e5b8d-475">Autorizace (zabránění přístupu k prostředkům, ke kterým uživatel nemá oprávnění).</span><span class="sxs-lookup"><span data-stu-id="e5b8d-475">Authorization (preventing access to resources a user isn't authorized for).</span></span>
* <span data-ttu-id="e5b8d-476">Ukládání odpovědí do mezipaměti (při krátkém okruhu kanálu požadavků, který vrátí odpověď uloženou v mezipaměti).</span><span class="sxs-lookup"><span data-stu-id="e5b8d-476">Response caching (short-circuiting the request pipeline to return a cached response).</span></span>

<span data-ttu-id="e5b8d-477">Vlastní filtry je možné vytvořit pro zpracování vzájemně se týkajících se otázek.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-477">Custom filters can be created to handle cross-cutting concerns.</span></span> <span data-ttu-id="e5b8d-478">Mezi obavy mezi průřezy patří zpracování chyb, ukládání do mezipaměti, konfigurace, autorizace a protokolování.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-478">Examples of cross-cutting concerns include error handling, caching, configuration, authorization, and logging.</span></span>  <span data-ttu-id="e5b8d-479">Filtry zabraňují duplikování kódu.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-479">Filters avoid duplicating code.</span></span> <span data-ttu-id="e5b8d-480">Například filtr výjimek zpracování chyb může konsolidovat zpracování chyb.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-480">For example, an error handling exception filter could consolidate error handling.</span></span>

<span data-ttu-id="e5b8d-481">Tento dokument se týká Razor stránek, řadičů rozhraní API a řadičů se zobrazeními.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-481">This document applies to Razor Pages, API controllers, and controllers with views.</span></span>

<span data-ttu-id="e5b8d-482">[Zobrazit nebo Stáhnout ukázku](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample) ([Jak stáhnout](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="e5b8d-482">[View or download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="how-filters-work"></a><span data-ttu-id="e5b8d-483">Jak fungují filtry</span><span class="sxs-lookup"><span data-stu-id="e5b8d-483">How filters work</span></span>

<span data-ttu-id="e5b8d-484">Filtry se spouštějí v *kanálu vyvolání akce ASP.NET Core*, někdy označované jako *kanál filtru*.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-484">Filters run within the *ASP.NET Core action invocation pipeline*, sometimes referred to as the *filter pipeline*.</span></span>  <span data-ttu-id="e5b8d-485">Kanál filtru se spustí po ASP.NET Core vybere akci, která se má provést.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-485">The filter pipeline runs after ASP.NET Core selects the action to execute.</span></span>

![Požadavek se zpracovává prostřednictvím jiného middlewaru, middleware směrování, výběru akcí a kanálu vyvolání akce ASP.NET Core.](filters/_static/filter-pipeline-1.png)

### <a name="filter-types"></a><span data-ttu-id="e5b8d-488">Typy filtrů</span><span class="sxs-lookup"><span data-stu-id="e5b8d-488">Filter types</span></span>

<span data-ttu-id="e5b8d-489">Každý typ filtru se spustí v jiné fázi kanálu filtru:</span><span class="sxs-lookup"><span data-stu-id="e5b8d-489">Each filter type is executed at a different stage in the filter pipeline:</span></span>

* <span data-ttu-id="e5b8d-490">[Filtry autorizace](#authorization-filters) se spouštějí jako první a používají se k určení, jestli je uživatel autorizovaný pro požadavek.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-490">[Authorization filters](#authorization-filters) run first and are used to determine whether the user is authorized for the request.</span></span> <span data-ttu-id="e5b8d-491">Filtry autorizace pro krátkodobé vynechání kanálu, pokud je požadavek neautorizovaný</span><span class="sxs-lookup"><span data-stu-id="e5b8d-491">Authorization filters short-circuit the pipeline if the request is unauthorized.</span></span>

* <span data-ttu-id="e5b8d-492">[Filtry prostředků](#resource-filters):</span><span class="sxs-lookup"><span data-stu-id="e5b8d-492">[Resource filters](#resource-filters):</span></span>

  * <span data-ttu-id="e5b8d-493">Spusťte po autorizaci.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-493">Run after authorization.</span></span>  
  * <span data-ttu-id="e5b8d-494"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*>může spustit kód před zbytek kanálu filtru.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-494"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> can run code before the rest of the filter pipeline.</span></span> <span data-ttu-id="e5b8d-495">Například `OnResourceExecuting` může spustit kód před vytvořením vazby modelu.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-495">For example, `OnResourceExecuting` can run code before model binding.</span></span>
  * <span data-ttu-id="e5b8d-496"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*>může spustit kód po dokončení zbývající části kanálu.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-496"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> can run code after the rest of the pipeline has completed.</span></span>

* <span data-ttu-id="e5b8d-497">[Filtry akcí](#action-filters) mohou spustit kód bezprostředně před a po volání jednotlivé metody akce.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-497">[Action filters](#action-filters) can run code immediately before and after an individual action method is called.</span></span> <span data-ttu-id="e5b8d-498">Lze je použít k manipulaci s argumenty předaných do akce a výsledek vráceného z akce.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-498">They can be used to manipulate the arguments passed into an action and the result returned from the action.</span></span> <span data-ttu-id="e5b8d-499">Na stránkách se **nepodporují filtry** akcí Razor .</span><span class="sxs-lookup"><span data-stu-id="e5b8d-499">Action filters are **not** supported in Razor Pages.</span></span>

* <span data-ttu-id="e5b8d-500">[Filtry výjimek](#exception-filters) slouží k aplikování globálních zásad na neošetřené výjimky, ke kterým dojde před zapsáním cokoli do těla odpovědi.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-500">[Exception filters](#exception-filters) are used to apply global policies to unhandled exceptions that occur before anything has been written to the response body.</span></span>

* <span data-ttu-id="e5b8d-501">[Filtry výsledků](#result-filters) můžou spustit kód hned před a po provedení jednotlivých výsledků akce.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-501">[Result filters](#result-filters) can run code immediately before and after the execution of individual action results.</span></span> <span data-ttu-id="e5b8d-502">Spustí se pouze v případě, že metoda akce byla úspěšně provedena.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-502">They run only when the action method has executed successfully.</span></span> <span data-ttu-id="e5b8d-503">Jsou užitečné pro logiku, která musí obklopit zobrazení nebo formátovací modul.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-503">They are useful for logic that must surround view or formatter execution.</span></span>

<span data-ttu-id="e5b8d-504">Následující diagram znázorňuje způsob interakce typů filtrů v kanálu filtru.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-504">The following diagram shows how filter types interact in the filter pipeline.</span></span>

![Požadavek se zpracovává pomocí autorizačních filtrů, filtrů prostředků, vazeb modelů, filtrů akcí, provádění akcí a konverze výsledků akcí, filtrů výjimek, výsledných filtrů a provádění výsledků.](filters/_static/filter-pipeline-2.png)

## <a name="implementation"></a><span data-ttu-id="e5b8d-507">Implementace</span><span class="sxs-lookup"><span data-stu-id="e5b8d-507">Implementation</span></span>

<span data-ttu-id="e5b8d-508">Filtry podporují synchronní i asynchronní implementace prostřednictvím různých definic rozhraní.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-508">Filters support both synchronous and asynchronous implementations through different interface definitions.</span></span>

<span data-ttu-id="e5b8d-509">Synchronní filtry mohou spustit kód před ( `On-Stage-Executing` ) a po ( `On-Stage-Executed` ) jejich fáze zřetězení.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-509">Synchronous filters can run code before (`On-Stage-Executing`) and after (`On-Stage-Executed`) their pipeline stage.</span></span> <span data-ttu-id="e5b8d-510">Například `OnActionExecuting` je volána před voláním metody Action.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-510">For example, `OnActionExecuting` is called before the action method is called.</span></span> <span data-ttu-id="e5b8d-511">`OnActionExecuted`je volána po návratu metody Action.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-511">`OnActionExecuted` is called after the action method returns.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="e5b8d-512">Asynchronní filtry definují `On-Stage-ExecutionAsync` metodu:</span><span class="sxs-lookup"><span data-stu-id="e5b8d-512">Asynchronous filters define an `On-Stage-ExecutionAsync` method:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/SampleAsyncActionFilter.cs?name=snippet)]

<span data-ttu-id="e5b8d-513">V předchozím kódu `SampleAsyncActionFilter` obsahuje znak <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> ( `next` ), který provádí metodu Action.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-513">In the preceding code, the `SampleAsyncActionFilter` has an <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`)  that executes the action method.</span></span>  <span data-ttu-id="e5b8d-514">Každá z těchto `On-Stage-ExecutionAsync` metod vezme `FilterType-ExecutionDelegate` fázi zřetězení filtru.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-514">Each of the `On-Stage-ExecutionAsync` methods take a `FilterType-ExecutionDelegate` that executes the filter's pipeline stage.</span></span>

### <a name="multiple-filter-stages"></a><span data-ttu-id="e5b8d-515">Několik fází filtru</span><span class="sxs-lookup"><span data-stu-id="e5b8d-515">Multiple filter stages</span></span>

<span data-ttu-id="e5b8d-516">Rozhraní pro více fází filtru lze implementovat v rámci jedné třídy.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-516">Interfaces for multiple filter stages can be implemented in a single class.</span></span> <span data-ttu-id="e5b8d-517">Například <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> Třída implementuje `IActionFilter` , `IResultFilter` a jejich asynchronní ekvivalenty.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-517">For example, the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> class implements `IActionFilter`, `IResultFilter`, and their async equivalents.</span></span>

<span data-ttu-id="e5b8d-518">Implementujte **buď** synchronní, nebo asynchronní verzi rozhraní filtru, **nikoli** obojí.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-518">Implement **either** the synchronous or the async version of a filter interface, **not** both.</span></span> <span data-ttu-id="e5b8d-519">Modul runtime nejprve kontroluje, zda filtr implementuje asynchronní rozhraní a v takovém případě jej zavolá.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-519">The runtime checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="e5b8d-520">V takovém případě volá metody synchronního rozhraní.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-520">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="e5b8d-521">Pokud jsou asynchronní i synchronní rozhraní implementovány v jedné třídě, je volána pouze asynchronní metoda.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-521">If both asynchronous and synchronous interfaces are implemented in one class, only the async method is called.</span></span> <span data-ttu-id="e5b8d-522">Při použití abstraktních tříd, jako <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> je například přepsání pouze synchronních metod nebo asynchronní metody pro každý typ filtru.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-522">When using abstract classes like <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> override only the synchronous methods or the async method for each filter type.</span></span>

### <a name="built-in-filter-attributes"></a><span data-ttu-id="e5b8d-523">Předdefinované atributy filtru</span><span class="sxs-lookup"><span data-stu-id="e5b8d-523">Built-in filter attributes</span></span>

<span data-ttu-id="e5b8d-524">ASP.NET Core obsahuje integrované filtry založené na atributech, které mohou být roztříděné a přizpůsobené.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-524">ASP.NET Core includes built-in attribute-based filters that can be subclassed and customized.</span></span> <span data-ttu-id="e5b8d-525">Například následující filtr výsledků přidá hlavičku do odpovědi:</span><span class="sxs-lookup"><span data-stu-id="e5b8d-525">For example, the following result filter adds a header to the response:</span></span>

<a name="add-header-attribute"></a>

[!code-csharp[](./filters/sample/FiltersSample/Filters/AddHeaderAttribute.cs?name=snippet)]

<span data-ttu-id="e5b8d-526">Atributy umožňují filtrům přijímat argumenty, jak je znázorněno v předchozím příkladu.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-526">Attributes allow filters to accept arguments, as shown in the preceding example.</span></span> <span data-ttu-id="e5b8d-527">Použijte `AddHeaderAttribute` pro metodu Controller nebo Action a zadejte název a hodnotu HLAVIČKY http:</span><span class="sxs-lookup"><span data-stu-id="e5b8d-527">Apply the `AddHeaderAttribute` to a controller or action method and specify the name and value of the HTTP header:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

<!-- `https://localhost:5001/Sample` -->

<span data-ttu-id="e5b8d-528">Některé z rozhraní filtru mají odpovídající atributy, které lze použít jako základní třídy pro vlastní implementace.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-528">Several of the filter interfaces have corresponding attributes that can be used as base classes for custom implementations.</span></span>

<span data-ttu-id="e5b8d-529">Atributy filtru:</span><span class="sxs-lookup"><span data-stu-id="e5b8d-529">Filter attributes:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ExceptionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>

## <a name="filter-scopes-and-order-of-execution"></a><span data-ttu-id="e5b8d-530">Filtrovat obory a pořadí provádění</span><span class="sxs-lookup"><span data-stu-id="e5b8d-530">Filter scopes and order of execution</span></span>

<span data-ttu-id="e5b8d-531">Filtr lze přidat do kanálu v jednom ze tří *oborů*:</span><span class="sxs-lookup"><span data-stu-id="e5b8d-531">A filter can be added to the pipeline at one of three *scopes*:</span></span>

* <span data-ttu-id="e5b8d-532">Použití atributu pro akci.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-532">Using an attribute on an action.</span></span>
* <span data-ttu-id="e5b8d-533">Použití atributu na řadiči.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-533">Using an attribute on a controller.</span></span>
* <span data-ttu-id="e5b8d-534">Globálně pro všechny řadiče a akce, jak je znázorněno v následujícím kódu:</span><span class="sxs-lookup"><span data-stu-id="e5b8d-534">Globally for all controllers and actions as shown in the following code:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/StartupGF.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="e5b8d-535">Předchozí kód přičítá tři filtry globálně pomocí kolekce [MvcOptions. filters](xref:Microsoft.AspNetCore.Mvc.MvcOptions.Filters) .</span><span class="sxs-lookup"><span data-stu-id="e5b8d-535">The preceding code adds three filters globally using the [MvcOptions.Filters](xref:Microsoft.AspNetCore.Mvc.MvcOptions.Filters) collection.</span></span>

### <a name="default-order-of-execution"></a><span data-ttu-id="e5b8d-536">Výchozí pořadí provádění</span><span class="sxs-lookup"><span data-stu-id="e5b8d-536">Default order of execution</span></span>

<span data-ttu-id="e5b8d-537">Pokud existuje více filtrů *stejného typu*, určuje obor výchozí pořadí provádění filtru.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-537">When there are multiple filters *of the same type*, scope determines the default order of filter execution.</span></span>  <span data-ttu-id="e5b8d-538">Globální filtry filtr tříd Surround.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-538">Global filters surround class filters.</span></span> <span data-ttu-id="e5b8d-539">Filtry tříd obklopují filtry metod Surround.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-539">Class filters surround method filters.</span></span>

<span data-ttu-id="e5b8d-540">V důsledku vnořování filtru je *po* kódu spuštěn v obráceném pořadí *před* kódem.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-540">As a result of filter nesting, the *after* code of filters runs in the reverse order of the *before* code.</span></span> <span data-ttu-id="e5b8d-541">Pořadí filtru:</span><span class="sxs-lookup"><span data-stu-id="e5b8d-541">The filter sequence:</span></span>

* <span data-ttu-id="e5b8d-542">*Před* kódem globálních filtrů.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-542">The *before* code of global filters.</span></span>
  * <span data-ttu-id="e5b8d-543">*Před* kódem filtru kontroleru.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-543">The *before* code of controller filters.</span></span>
    * <span data-ttu-id="e5b8d-544">Kód *před* filtry metody Action.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-544">The *before* code of action method filters.</span></span>
    * <span data-ttu-id="e5b8d-545">*Po* kódu filtry metody Action.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-545">The *after* code of action method filters.</span></span>
  * <span data-ttu-id="e5b8d-546">*Po* kódu filtry kontroleru.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-546">The *after* code of controller filters.</span></span>
* <span data-ttu-id="e5b8d-547">*Po* kódu globálních filtrů.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-547">The *after* code of global filters.</span></span>
  
<span data-ttu-id="e5b8d-548">Následující příklad ilustruje pořadí, ve kterém jsou metody filtru volány pro filtry synchronních akcí.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-548">The following example that illustrates the order in which filter methods are called for synchronous action filters.</span></span>

| <span data-ttu-id="e5b8d-549">Sequence</span><span class="sxs-lookup"><span data-stu-id="e5b8d-549">Sequence</span></span> | <span data-ttu-id="e5b8d-550">Rozsah filtru</span><span class="sxs-lookup"><span data-stu-id="e5b8d-550">Filter scope</span></span> | <span data-ttu-id="e5b8d-551">Filter – metoda</span><span class="sxs-lookup"><span data-stu-id="e5b8d-551">Filter method</span></span> |
|:--------:|:------------:|:-------------:|
| <span data-ttu-id="e5b8d-552">1</span><span class="sxs-lookup"><span data-stu-id="e5b8d-552">1</span></span> | <span data-ttu-id="e5b8d-553">Globální</span><span class="sxs-lookup"><span data-stu-id="e5b8d-553">Global</span></span> | `OnActionExecuting` |
| <span data-ttu-id="e5b8d-554">2</span><span class="sxs-lookup"><span data-stu-id="e5b8d-554">2</span></span> | <span data-ttu-id="e5b8d-555">Kontrolér</span><span class="sxs-lookup"><span data-stu-id="e5b8d-555">Controller</span></span> | `OnActionExecuting` |
| <span data-ttu-id="e5b8d-556">3</span><span class="sxs-lookup"><span data-stu-id="e5b8d-556">3</span></span> | <span data-ttu-id="e5b8d-557">Metoda</span><span class="sxs-lookup"><span data-stu-id="e5b8d-557">Method</span></span> | `OnActionExecuting` |
| <span data-ttu-id="e5b8d-558">4</span><span class="sxs-lookup"><span data-stu-id="e5b8d-558">4</span></span> | <span data-ttu-id="e5b8d-559">Metoda</span><span class="sxs-lookup"><span data-stu-id="e5b8d-559">Method</span></span> | `OnActionExecuted` |
| <span data-ttu-id="e5b8d-560">5</span><span class="sxs-lookup"><span data-stu-id="e5b8d-560">5</span></span> | <span data-ttu-id="e5b8d-561">Kontrolér</span><span class="sxs-lookup"><span data-stu-id="e5b8d-561">Controller</span></span> | `OnActionExecuted` |
| <span data-ttu-id="e5b8d-562">6</span><span class="sxs-lookup"><span data-stu-id="e5b8d-562">6</span></span> | <span data-ttu-id="e5b8d-563">Globální</span><span class="sxs-lookup"><span data-stu-id="e5b8d-563">Global</span></span> | `OnActionExecuted` |

<span data-ttu-id="e5b8d-564">Tato posloupnost zobrazuje:</span><span class="sxs-lookup"><span data-stu-id="e5b8d-564">This sequence shows:</span></span>

* <span data-ttu-id="e5b8d-565">Filtr metod je vnořený do filtru kontroleru.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-565">The method filter is nested within the controller filter.</span></span>
* <span data-ttu-id="e5b8d-566">Filtr kontroleru je vnořený do globálního filtru.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-566">The controller filter is nested within the global filter.</span></span>

### <a name="controller-and-razor-page-level-filters"></a><span data-ttu-id="e5b8d-567">Filtry na Razor úrovni řadiče a stránky</span><span class="sxs-lookup"><span data-stu-id="e5b8d-567">Controller and Razor Page level filters</span></span>

<span data-ttu-id="e5b8d-568">Každý kontroler, který dědí ze <xref:Microsoft.AspNetCore.Mvc.Controller> základní třídy, zahrnuje metody [Controller. OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*), [Controller. OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*)a [Controller. OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*) 
 `OnActionExecuted` .</span><span class="sxs-lookup"><span data-stu-id="e5b8d-568">Every controller that inherits from the <xref:Microsoft.AspNetCore.Mvc.Controller> base class includes [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*),  [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*), and [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted` methods.</span></span> <span data-ttu-id="e5b8d-569">Tyto metody:</span><span class="sxs-lookup"><span data-stu-id="e5b8d-569">These methods:</span></span>

* <span data-ttu-id="e5b8d-570">Zabalte filtry, které se spouštějí pro danou akci.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-570">Wrap the filters that run for a given action.</span></span>
* <span data-ttu-id="e5b8d-571">`OnActionExecuting`je volána před jakýmkoli filtrem akce.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-571">`OnActionExecuting` is called before any of the action's filters.</span></span>
* <span data-ttu-id="e5b8d-572">`OnActionExecuted`se volá po všech filtrech akcí.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-572">`OnActionExecuted` is called after all of the action filters.</span></span>
* <span data-ttu-id="e5b8d-573">`OnActionExecutionAsync`je volána před jakýmkoli filtrem akce.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-573">`OnActionExecutionAsync` is called before any of the action's filters.</span></span> <span data-ttu-id="e5b8d-574">Kód v filtru po `next` spuštění po metodě Action.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-574">Code in the filter after `next` runs after the action method.</span></span>

<span data-ttu-id="e5b8d-575">Například v ukázce ke stažení `MySampleActionFilter` se použije globálně při spuštění.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-575">For example, in the download sample, `MySampleActionFilter` is applied globally in startup.</span></span>

<span data-ttu-id="e5b8d-576">`TestController`:</span><span class="sxs-lookup"><span data-stu-id="e5b8d-576">The `TestController`:</span></span>

* <span data-ttu-id="e5b8d-577">Aplikuje `SampleActionFilterAttribute` ( `[SampleActionFilter]` ) na `FilterTest2` akci.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-577">Applies the `SampleActionFilterAttribute` (`[SampleActionFilter]`) to the `FilterTest2` action.</span></span>
* <span data-ttu-id="e5b8d-578">Přepisuje `OnActionExecuting` a `OnActionExecuted` .</span><span class="sxs-lookup"><span data-stu-id="e5b8d-578">Overrides `OnActionExecuting` and `OnActionExecuted`.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/TestController.cs?name=snippet)]

<span data-ttu-id="e5b8d-579">Navigace pro `https://localhost:5001/Test/FilterTest2` spustí následující kód:</span><span class="sxs-lookup"><span data-stu-id="e5b8d-579">Navigating to `https://localhost:5001/Test/FilterTest2` runs the following code:</span></span>

* `TestController.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `SampleActionFilterAttribute.OnActionExecuting`
      * `TestController.FilterTest2`
    * `SampleActionFilterAttribute.OnActionExecuted`
  * `MySampleActionFilter.OnActionExecuted`
* `TestController.OnActionExecuted`

<span data-ttu-id="e5b8d-580">RazorInformace o stránkách naleznete v tématu [implementace Razor filtrů stránek pomocí přepsání metod filtru](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span><span class="sxs-lookup"><span data-stu-id="e5b8d-580">For Razor Pages, see [Implement Razor Page filters by overriding filter methods](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span></span>

### <a name="overriding-the-default-order"></a><span data-ttu-id="e5b8d-581">Přepsání výchozího pořadí</span><span class="sxs-lookup"><span data-stu-id="e5b8d-581">Overriding the default order</span></span>

<span data-ttu-id="e5b8d-582">Výchozí sekvenci spuštění lze přepsat implementací <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter> .</span><span class="sxs-lookup"><span data-stu-id="e5b8d-582">The default sequence of execution can be overridden by implementing <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span></span> <span data-ttu-id="e5b8d-583">`IOrderedFilter`zpřístupňuje <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> vlastnost, která má přednost před rozsahem, aby určila pořadí provádění.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-583">`IOrderedFilter` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> property that takes precedence over scope to determine the order of execution.</span></span> <span data-ttu-id="e5b8d-584">Filtr s nižší `Order` hodnotou:</span><span class="sxs-lookup"><span data-stu-id="e5b8d-584">A filter with a lower `Order` value:</span></span>

* <span data-ttu-id="e5b8d-585">Spustí *před* kódem filtr s vyšší hodnotou `Order` .</span><span class="sxs-lookup"><span data-stu-id="e5b8d-585">Runs the *before* code before that of a filter with a higher value of `Order`.</span></span>
* <span data-ttu-id="e5b8d-586">Spustí *za* kódem za filtr s vyšší `Order` hodnotou.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-586">Runs the *after* code after that of a filter with a higher `Order` value.</span></span>

<span data-ttu-id="e5b8d-587">`Order`Vlastnost lze nastavit s parametrem konstruktoru:</span><span class="sxs-lookup"><span data-stu-id="e5b8d-587">The `Order` property can be set with a constructor parameter:</span></span>

```csharp
[MyFilter(Name = "Controller Level Attribute", Order=1)]
```

<span data-ttu-id="e5b8d-588">Vezměte v úvahu stejné 3 filtry akcí, které jsou uvedené v předchozím příkladu.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-588">Consider the same 3 action filters shown in the preceding example.</span></span> <span data-ttu-id="e5b8d-589">Pokud `Order` je vlastnost Controller a globální filtry nastavená na 1 a 2 v uvedeném pořadí, pořadí spouštění se vrátí zpět.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-589">If the `Order` property of the controller and global filters is set to 1 and 2 respectively, the order of execution is reversed.</span></span>

| <span data-ttu-id="e5b8d-590">Sequence</span><span class="sxs-lookup"><span data-stu-id="e5b8d-590">Sequence</span></span> | <span data-ttu-id="e5b8d-591">Rozsah filtru</span><span class="sxs-lookup"><span data-stu-id="e5b8d-591">Filter scope</span></span> | <span data-ttu-id="e5b8d-592">`Order`majetek</span><span class="sxs-lookup"><span data-stu-id="e5b8d-592">`Order` property</span></span> | <span data-ttu-id="e5b8d-593">Filter – metoda</span><span class="sxs-lookup"><span data-stu-id="e5b8d-593">Filter method</span></span> |
|:--------:|:------------:|:-----------------:|:-------------:|
| <span data-ttu-id="e5b8d-594">1</span><span class="sxs-lookup"><span data-stu-id="e5b8d-594">1</span></span> | <span data-ttu-id="e5b8d-595">Metoda</span><span class="sxs-lookup"><span data-stu-id="e5b8d-595">Method</span></span> | <span data-ttu-id="e5b8d-596">0</span><span class="sxs-lookup"><span data-stu-id="e5b8d-596">0</span></span> | `OnActionExecuting` |
| <span data-ttu-id="e5b8d-597">2</span><span class="sxs-lookup"><span data-stu-id="e5b8d-597">2</span></span> | <span data-ttu-id="e5b8d-598">Kontrolér</span><span class="sxs-lookup"><span data-stu-id="e5b8d-598">Controller</span></span> | <span data-ttu-id="e5b8d-599">1</span><span class="sxs-lookup"><span data-stu-id="e5b8d-599">1</span></span>  | `OnActionExecuting` |
| <span data-ttu-id="e5b8d-600">3</span><span class="sxs-lookup"><span data-stu-id="e5b8d-600">3</span></span> | <span data-ttu-id="e5b8d-601">Globální</span><span class="sxs-lookup"><span data-stu-id="e5b8d-601">Global</span></span> | <span data-ttu-id="e5b8d-602">2</span><span class="sxs-lookup"><span data-stu-id="e5b8d-602">2</span></span>  | `OnActionExecuting` |
| <span data-ttu-id="e5b8d-603">4</span><span class="sxs-lookup"><span data-stu-id="e5b8d-603">4</span></span> | <span data-ttu-id="e5b8d-604">Globální</span><span class="sxs-lookup"><span data-stu-id="e5b8d-604">Global</span></span> | <span data-ttu-id="e5b8d-605">2</span><span class="sxs-lookup"><span data-stu-id="e5b8d-605">2</span></span>  | `OnActionExecuted` |
| <span data-ttu-id="e5b8d-606">5</span><span class="sxs-lookup"><span data-stu-id="e5b8d-606">5</span></span> | <span data-ttu-id="e5b8d-607">Kontrolér</span><span class="sxs-lookup"><span data-stu-id="e5b8d-607">Controller</span></span> | <span data-ttu-id="e5b8d-608">1</span><span class="sxs-lookup"><span data-stu-id="e5b8d-608">1</span></span>  | `OnActionExecuted` |
| <span data-ttu-id="e5b8d-609">6</span><span class="sxs-lookup"><span data-stu-id="e5b8d-609">6</span></span> | <span data-ttu-id="e5b8d-610">Metoda</span><span class="sxs-lookup"><span data-stu-id="e5b8d-610">Method</span></span> | <span data-ttu-id="e5b8d-611">0</span><span class="sxs-lookup"><span data-stu-id="e5b8d-611">0</span></span>  | `OnActionExecuted` |

<span data-ttu-id="e5b8d-612">`Order`Vlastnost Přepisuje obor při určování pořadí, ve kterém jsou filtry spouštěny.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-612">The `Order` property overrides scope when determining the order in which filters run.</span></span> <span data-ttu-id="e5b8d-613">Filtry jsou seřazené podle pořadí, pak se k přerušení vztahů používá obor.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-613">Filters are sorted first by order, then scope is used to break ties.</span></span> <span data-ttu-id="e5b8d-614">Všechny předdefinované filtry implementují `IOrderedFilter` a nastaví výchozí `Order` hodnotu na 0.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-614">All of the built-in filters implement `IOrderedFilter` and set the default `Order` value to 0.</span></span> <span data-ttu-id="e5b8d-615">Pro předdefinované filtry rozsah určuje pořadí, pokud `Order` není nastaven na nenulovou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-615">For built-in filters, scope determines order unless `Order` is set to a non-zero value.</span></span>

## <a name="cancellation-and-short-circuiting"></a><span data-ttu-id="e5b8d-616">Zrušení a zkrácení okruhů</span><span class="sxs-lookup"><span data-stu-id="e5b8d-616">Cancellation and short-circuiting</span></span>

<span data-ttu-id="e5b8d-617">Kanál filtru může být v krátkém okruhu nastavením <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> vlastnosti pro <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> parametr poskytnutý metodě Filter.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-617">The filter pipeline can be short-circuited by setting the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> property on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> parameter provided to the filter method.</span></span> <span data-ttu-id="e5b8d-618">Například následující filtr prostředků zabrání ve spuštění zbývajícího kanálu:</span><span class="sxs-lookup"><span data-stu-id="e5b8d-618">For instance, the following Resource filter prevents the rest of the pipeline from executing:</span></span>

<a name="short-circuiting-resource-filter"></a>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ShortCircuitingResourceFilterAttribute.cs?name=snippet)]

<span data-ttu-id="e5b8d-619">V následujícím kódu, `ShortCircuitingResourceFilter` a jako `AddHeader` cíl filtru i cílovou `SomeResource` metodu Action.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-619">In the following code, both the `ShortCircuitingResourceFilter` and the `AddHeader` filter target the `SomeResource` action method.</span></span> <span data-ttu-id="e5b8d-620">`ShortCircuitingResourceFilter`:</span><span class="sxs-lookup"><span data-stu-id="e5b8d-620">The `ShortCircuitingResourceFilter`:</span></span>

* <span data-ttu-id="e5b8d-621">Nejprve se spustí, protože se jedná o filtr prostředků a `AddHeader` je filtr akcí.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-621">Runs first, because it's a Resource Filter and `AddHeader` is an Action Filter.</span></span>
* <span data-ttu-id="e5b8d-622">Krátké okruhy zbývajícího kanálu.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-622">Short-circuits the rest of the pipeline.</span></span>

<span data-ttu-id="e5b8d-623">Proto `AddHeader` Filtr pro akci nikdy neběží `SomeResource` .</span><span class="sxs-lookup"><span data-stu-id="e5b8d-623">Therefore the `AddHeader` filter never runs for the `SomeResource` action.</span></span> <span data-ttu-id="e5b8d-624">Toto chování by bylo stejné, pokud byly oba filtry aplikovány na úrovni metody akce, a to za předpokladu, že byla `ShortCircuitingResourceFilter` spuštěna jako první.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-624">This behavior would be the same if both filters were applied at the action method level, provided the `ShortCircuitingResourceFilter` ran first.</span></span> <span data-ttu-id="e5b8d-625">`ShortCircuitingResourceFilter`Nejprve se spustí z důvodu jeho typu filtru nebo explicitního použití `Order` Vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-625">The `ShortCircuitingResourceFilter` runs first because of its filter type, or by explicit use of `Order` property.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1,9)]

## <a name="dependency-injection"></a><span data-ttu-id="e5b8d-626">Injektáž závislostí</span><span class="sxs-lookup"><span data-stu-id="e5b8d-626">Dependency injection</span></span>

<span data-ttu-id="e5b8d-627">Filtry lze přidat podle typu nebo podle instance.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-627">Filters can be added by type or by instance.</span></span> <span data-ttu-id="e5b8d-628">Pokud je přidána instance, bude tato instance použita pro každý požadavek.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-628">If an instance is added, that instance is used for every request.</span></span> <span data-ttu-id="e5b8d-629">Pokud je přidán typ, je aktivován typ.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-629">If a type is added, it's type-activated.</span></span> <span data-ttu-id="e5b8d-630">Filtr aktivovaný typu znamená:</span><span class="sxs-lookup"><span data-stu-id="e5b8d-630">A type-activated filter means:</span></span>

* <span data-ttu-id="e5b8d-631">Instance se vytvoří pro každý požadavek.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-631">An instance is created for each request.</span></span>
* <span data-ttu-id="e5b8d-632">Jakékoli závislosti konstruktoru jsou vyplněny pomocí [Injektáže závislosti](xref:fundamentals/dependency-injection) (di).</span><span class="sxs-lookup"><span data-stu-id="e5b8d-632">Any constructor dependencies are populated by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span>

<span data-ttu-id="e5b8d-633">Filtry, které jsou implementovány jako atributy a přidány přímo do tříd kontroleru nebo metody akcí, nemohou mít závislosti konstruktoru poskytované [vkládáním závislostí](xref:fundamentals/dependency-injection) (di).</span><span class="sxs-lookup"><span data-stu-id="e5b8d-633">Filters that are implemented as attributes and added directly to controller classes or action methods cannot have constructor dependencies provided by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span> <span data-ttu-id="e5b8d-634">Pomocí DI nelze poskytnout závislosti konstruktoru:</span><span class="sxs-lookup"><span data-stu-id="e5b8d-634">Constructor dependencies cannot be provided by DI because:</span></span>

* <span data-ttu-id="e5b8d-635">V atributech musí být zadány parametry konstruktoru, kde jsou aplikovány.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-635">Attributes must have their constructor parameters supplied where they're applied.</span></span> 
* <span data-ttu-id="e5b8d-636">Toto je omezení způsobu fungování atributů.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-636">This is a limitation of how attributes work.</span></span>

<span data-ttu-id="e5b8d-637">Následující filtry podporují závislosti konstruktoru poskytované od DI:</span><span class="sxs-lookup"><span data-stu-id="e5b8d-637">The following filters support constructor dependencies provided from DI:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>
* <span data-ttu-id="e5b8d-638"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>implementováno u atributu.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-638"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implemented on the attribute.</span></span>

<span data-ttu-id="e5b8d-639">Předchozí filtry lze použít pro metodu kontroleru nebo akce:</span><span class="sxs-lookup"><span data-stu-id="e5b8d-639">The preceding filters can be applied to a controller or action method:</span></span>

<span data-ttu-id="e5b8d-640">Protokolovací nástroje jsou k dispozici z DI.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-640">Loggers are available from DI.</span></span> <span data-ttu-id="e5b8d-641">Ale nevytvářejte a používejte filtry čistě pro účely protokolování.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-641">However, avoid creating and using filters purely for logging purposes.</span></span> <span data-ttu-id="e5b8d-642">[Integrované protokolování rozhraní](xref:fundamentals/logging/index) obvykle zajišťuje, co je potřeba pro protokolování.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-642">The [built-in framework logging](xref:fundamentals/logging/index) typically provides what's needed for logging.</span></span> <span data-ttu-id="e5b8d-643">Protokolování přidáno do filtrů:</span><span class="sxs-lookup"><span data-stu-id="e5b8d-643">Logging added to filters:</span></span>

* <span data-ttu-id="e5b8d-644">Měl by se soustředit na obavy a chování obchodních domén, které jsou specifické pro filtr.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-644">Should focus on business domain concerns or behavior specific to the filter.</span></span>
* <span data-ttu-id="e5b8d-645">Neměli **byste** protokolovat akce nebo jiné události rozhraní.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-645">Should **not** log actions or other framework events.</span></span> <span data-ttu-id="e5b8d-646">Vestavěné filtry protokolují akce a události rozhraní.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-646">The built in filters log actions and framework events.</span></span>

### <a name="servicefilterattribute"></a><span data-ttu-id="e5b8d-647">ServiceFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="e5b8d-647">ServiceFilterAttribute</span></span>

<span data-ttu-id="e5b8d-648">Typy implementace filtru služby jsou zaregistrované v `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="e5b8d-648">Service filter implementation types are registered in `ConfigureServices`.</span></span> <span data-ttu-id="e5b8d-649"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>Načte instanci filtru z di.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-649">A <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> retrieves an instance of the filter from DI.</span></span>

<span data-ttu-id="e5b8d-650">Následující kód ukazuje `AddHeaderResultServiceFilter` :</span><span class="sxs-lookup"><span data-stu-id="e5b8d-650">The following code shows the `AddHeaderResultServiceFilter`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="e5b8d-651">V následujícím kódu `AddHeaderResultServiceFilter` je přidán do kontejneru di:</span><span class="sxs-lookup"><span data-stu-id="e5b8d-651">In the following code, `AddHeaderResultServiceFilter` is added to the DI container:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

<span data-ttu-id="e5b8d-652">V následujícím kódu `ServiceFilter` atribut načte instanci `AddHeaderResultServiceFilter` filtru z di:</span><span class="sxs-lookup"><span data-stu-id="e5b8d-652">In the following code, the `ServiceFilter` attribute retrieves an instance of the `AddHeaderResultServiceFilter` filter from DI:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_ServiceFilter&highlight=1)]

<span data-ttu-id="e5b8d-653">Při použití `ServiceFilterAttribute` nastavte [ServiceFilterAttribute. IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span><span class="sxs-lookup"><span data-stu-id="e5b8d-653">When using `ServiceFilterAttribute`, setting [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span></span>

* <span data-ttu-id="e5b8d-654">Poskytuje nápovědu, že se instance filtru *dá* znovu použít mimo rozsah požadavku, který byl vytvořen v rámci.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-654">Provides a hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="e5b8d-655">Modul runtime ASP.NET Core nezaručuje:</span><span class="sxs-lookup"><span data-stu-id="e5b8d-655">The ASP.NET Core runtime doesn't guarantee:</span></span>

  * <span data-ttu-id="e5b8d-656">Vytvoří se jedna instance filtru.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-656">That a single instance of the filter will be created.</span></span>
  * <span data-ttu-id="e5b8d-657">Filtr nebude znovu vyžádán z kontejneru DI v pozdějším bodě.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-657">The filter will not be re-requested from the DI container at some later point.</span></span>

* <span data-ttu-id="e5b8d-658">Neměl by se používat s filtrem, který závisí na službách s jinou životností než singleton.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-658">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

 <span data-ttu-id="e5b8d-659"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>implementuje <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> .</span><span class="sxs-lookup"><span data-stu-id="e5b8d-659"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="e5b8d-660">`IFilterFactory`zpřístupňuje <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> metodu pro vytvoření <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-660">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="e5b8d-661">`CreateInstance`Načte zadaný typ z DI.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-661">`CreateInstance` loads the specified type from DI.</span></span>

### <a name="typefilterattribute"></a><span data-ttu-id="e5b8d-662">TypeFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="e5b8d-662">TypeFilterAttribute</span></span>

<span data-ttu-id="e5b8d-663"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>je podobný <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> , ale jeho typ není vyřešen přímo z kontejneru di.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-663"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> is similar to <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, but its type isn't resolved directly from the DI container.</span></span> <span data-ttu-id="e5b8d-664">Vytvoří instanci typu pomocí <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName> .</span><span class="sxs-lookup"><span data-stu-id="e5b8d-664">It instantiates the type by using <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span></span>

<span data-ttu-id="e5b8d-665">Protože `TypeFilterAttribute` typy nejsou vyřešeny přímo z kontejneru di:</span><span class="sxs-lookup"><span data-stu-id="e5b8d-665">Because `TypeFilterAttribute` types aren't resolved directly from the DI container:</span></span>

* <span data-ttu-id="e5b8d-666">Typy, na které se odkazuje pomocí, `TypeFilterAttribute` není nutné registrovat v kontejneru di.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-666">Types that are referenced using the `TypeFilterAttribute` don't need to be registered with the DI container.</span></span>  <span data-ttu-id="e5b8d-667">Mají své závislosti splněné kontejnerem DI.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-667">They do have their dependencies fulfilled by the DI container.</span></span>
* <span data-ttu-id="e5b8d-668">`TypeFilterAttribute`může volitelně přijmout argumenty konstruktoru pro typ.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-668">`TypeFilterAttribute` can optionally accept constructor arguments for the type.</span></span>

<span data-ttu-id="e5b8d-669">Při použití `TypeFilterAttribute` nastavte [TypeFilterAttribute. IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span><span class="sxs-lookup"><span data-stu-id="e5b8d-669">When using `TypeFilterAttribute`, setting [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span></span>
* <span data-ttu-id="e5b8d-670">Poskytuje nápovědu, že se instance filtru *dá* znovu použít mimo rozsah požadavku, který byl vytvořen v rámci.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-670">Provides hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="e5b8d-671">Modul runtime ASP.NET Core neposkytuje žádné záruky, že se vytvoří jediná instance filtru.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-671">The ASP.NET Core runtime provides no guarantees that a single instance of the filter will be created.</span></span>

* <span data-ttu-id="e5b8d-672">Neměl by se používat s filtrem, který závisí na službách s jinou životností než singleton.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-672">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

<span data-ttu-id="e5b8d-673">Následující příklad ukazuje, jak předat argumenty typu pomocí `TypeFilterAttribute` :</span><span class="sxs-lookup"><span data-stu-id="e5b8d-673">The following example shows how to pass arguments to a type using `TypeFilterAttribute`:</span></span>

[!code-csharp[](../../mvc/controllers/filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_TypeFilter&highlight=1,2)]
[!code-csharp[](../../mvc/controllers/filters/sample/FiltersSample/Filters/LogConstantFilter.cs?name=snippet_TypeFilter_Implementation&highlight=6)]

<!-- 
https://localhost:5001/home/hi?name=joe
VS debug window shows 
FiltersSample.Filters.LogConstantFilter:Information: Method 'Hi' called
-->

## <a name="authorization-filters"></a><span data-ttu-id="e5b8d-674">Filtry autorizace</span><span class="sxs-lookup"><span data-stu-id="e5b8d-674">Authorization filters</span></span>

<span data-ttu-id="e5b8d-675">Filtry autorizace:</span><span class="sxs-lookup"><span data-stu-id="e5b8d-675">Authorization filters:</span></span>

* <span data-ttu-id="e5b8d-676">Jsou první filtry spouštěny v kanálu filtru.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-676">Are the first filters run in the filter pipeline.</span></span>
* <span data-ttu-id="e5b8d-677">Řízení přístupu k metodám akcí.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-677">Control access to action methods.</span></span>
* <span data-ttu-id="e5b8d-678">Před metodou, ale ne po metodě.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-678">Have a before method, but no after method.</span></span>

<span data-ttu-id="e5b8d-679">Vlastní autorizační filtry vyžadují vlastní autorizační rozhraní.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-679">Custom authorization filters require a custom authorization framework.</span></span> <span data-ttu-id="e5b8d-680">Preferovat konfiguraci autorizačních zásad nebo psaní vlastních zásad autorizace při psaní vlastního filtru.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-680">Prefer configuring the authorization policies or writing a custom authorization policy over writing a custom filter.</span></span> <span data-ttu-id="e5b8d-681">Vestavěný autorizační filtr:</span><span class="sxs-lookup"><span data-stu-id="e5b8d-681">The built-in authorization filter:</span></span>

* <span data-ttu-id="e5b8d-682">Volá autorizační systém.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-682">Calls the authorization system.</span></span>
* <span data-ttu-id="e5b8d-683">Neautorizuje požadavky.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-683">Does not authorize requests.</span></span>

<span data-ttu-id="e5b8d-684">Negenerovat **výjimky** v rámci autorizačních filtrů:</span><span class="sxs-lookup"><span data-stu-id="e5b8d-684">Do **not** throw exceptions within authorization filters:</span></span>

* <span data-ttu-id="e5b8d-685">Výjimka nebude zpracována.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-685">The exception will not be handled.</span></span>
* <span data-ttu-id="e5b8d-686">Filtry výjimek nebudou zpracovávat výjimku.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-686">Exception filters will not handle the exception.</span></span>

<span data-ttu-id="e5b8d-687">Zvažte vydání výzvy, pokud dojde k výjimce ve autorizačním filtru.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-687">Consider issuing a challenge when an exception occurs in an authorization filter.</span></span>

<span data-ttu-id="e5b8d-688">Přečtěte si další informace o [autorizaci](xref:security/authorization/introduction).</span><span class="sxs-lookup"><span data-stu-id="e5b8d-688">Learn more about [Authorization](xref:security/authorization/introduction).</span></span>

## <a name="resource-filters"></a><span data-ttu-id="e5b8d-689">Filtry prostředků</span><span class="sxs-lookup"><span data-stu-id="e5b8d-689">Resource filters</span></span>

<span data-ttu-id="e5b8d-690">Filtry prostředků:</span><span class="sxs-lookup"><span data-stu-id="e5b8d-690">Resource filters:</span></span>

* <span data-ttu-id="e5b8d-691">Implementujte <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> rozhraní nebo <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> .</span><span class="sxs-lookup"><span data-stu-id="e5b8d-691">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> interface.</span></span>
* <span data-ttu-id="e5b8d-692">Provádění zabalí většinu kanálu filtru.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-692">Execution wraps most of the filter pipeline.</span></span>
* <span data-ttu-id="e5b8d-693">Před filtry prostředků se spouštějí jenom [filtry autorizace](#authorization-filters) .</span><span class="sxs-lookup"><span data-stu-id="e5b8d-693">Only [Authorization filters](#authorization-filters) run before resource filters.</span></span>

<span data-ttu-id="e5b8d-694">Filtry prostředků jsou užitečné pro krátkodobé okruhy v kanálu.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-694">Resource filters are useful to short-circuit most of the pipeline.</span></span> <span data-ttu-id="e5b8d-695">Například filtr ukládání do mezipaměti může zabránit zbývajícímu kanálu v případě přístupů do mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-695">For example, a caching filter can avoid the rest of the pipeline on a cache hit.</span></span>

<span data-ttu-id="e5b8d-696">Příklady filtru prostředků:</span><span class="sxs-lookup"><span data-stu-id="e5b8d-696">Resource filter examples:</span></span>

* <span data-ttu-id="e5b8d-697">[Filtr prostředků se zkrácenými okruhy](#short-circuiting-resource-filter) zobrazenými dříve.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-697">[The short-circuiting resource filter](#short-circuiting-resource-filter) shown previously.</span></span>
* <span data-ttu-id="e5b8d-698">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span><span class="sxs-lookup"><span data-stu-id="e5b8d-698">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span></span>

  * <span data-ttu-id="e5b8d-699">Zabraňuje vazbě modelu v přístupu k datům formuláře.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-699">Prevents model binding from accessing the form data.</span></span>
  * <span data-ttu-id="e5b8d-700">Používá se pro nahrávání velkých souborů, aby se zabránilo čtení dat z formuláře do paměti.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-700">Used for large file uploads to prevent the form data from being read into memory.</span></span>

## <a name="action-filters"></a><span data-ttu-id="e5b8d-701">Filtry akcí</span><span class="sxs-lookup"><span data-stu-id="e5b8d-701">Action filters</span></span>

> [!IMPORTANT]
> <span data-ttu-id="e5b8d-702">Filtry **akcí se nevztahují** na Razor stránky.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-702">Action filters do **not** apply to Razor Pages.</span></span> Razor<span data-ttu-id="e5b8d-703">Stránky podporují <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> a <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span><span class="sxs-lookup"><span data-stu-id="e5b8d-703"> Pages supports <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span></span> <span data-ttu-id="e5b8d-704">Další informace najdete v tématu [metody filtrování pro Razor stránky](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="e5b8d-704">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

<span data-ttu-id="e5b8d-705">Filtry akcí:</span><span class="sxs-lookup"><span data-stu-id="e5b8d-705">Action filters:</span></span>

* <span data-ttu-id="e5b8d-706">Implementujte <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> rozhraní nebo <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> .</span><span class="sxs-lookup"><span data-stu-id="e5b8d-706">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> interface.</span></span>
* <span data-ttu-id="e5b8d-707">Jejich spuštění obklopuje provádění metod akcí.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-707">Their execution surrounds the execution of action methods.</span></span>

<span data-ttu-id="e5b8d-708">Následující kód ukazuje filtr vzorové akce:</span><span class="sxs-lookup"><span data-stu-id="e5b8d-708">The following code shows a sample action filter:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="e5b8d-709"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext>Poskytuje následující vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="e5b8d-709">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> provides the following properties:</span></span>

* <span data-ttu-id="e5b8d-710"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments>– povolí čtení vstupů do metody akce.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-710"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> - enables the inputs to an action method be read.</span></span>
* <span data-ttu-id="e5b8d-711"><xref:Microsoft.AspNetCore.Mvc.Controller>– povolí manipulaci s instancí kontroleru.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-711"><xref:Microsoft.AspNetCore.Mvc.Controller> - enables manipulating the controller instance.</span></span>
* <span data-ttu-id="e5b8d-712"><xref:System.Web.Mvc.ActionExecutingContext.Result>– nastavení `Result` krátkodobého provádění metody akce a následných filtrů akcí.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-712"><xref:System.Web.Mvc.ActionExecutingContext.Result> - setting `Result` short-circuits execution of the action method and subsequent action filters.</span></span>

<span data-ttu-id="e5b8d-713">Vyvolání výjimky v metodě akce:</span><span class="sxs-lookup"><span data-stu-id="e5b8d-713">Throwing an exception in an action method:</span></span>

* <span data-ttu-id="e5b8d-714">Zabraňuje spuštění dalších filtrů.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-714">Prevents running of subsequent filters.</span></span>
* <span data-ttu-id="e5b8d-715">Na rozdíl od nastavení `Result` se místo úspěšného výsledku považuje za selhání.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-715">Unlike setting `Result`, is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="e5b8d-716"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext>Poskytuje `Controller` a `Result` plus následující vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="e5b8d-716">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> provides `Controller` and `Result` plus the following properties:</span></span>

* <span data-ttu-id="e5b8d-717"><xref:System.Web.Mvc.ActionExecutedContext.Canceled>-True, pokud bylo provedení akce zkráceno jiným filtrem.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-717"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> - True if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="e5b8d-718"><xref:System.Web.Mvc.ActionExecutedContext.Exception>-Hodnota není null, pokud akce nebo dříve vyvolala filtr akcí.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-718"><xref:System.Web.Mvc.ActionExecutedContext.Exception> - Non-null if the action or a previously run action filter threw an exception.</span></span> <span data-ttu-id="e5b8d-719">Nastavení této vlastnosti na hodnotu null:</span><span class="sxs-lookup"><span data-stu-id="e5b8d-719">Setting this property to null:</span></span>

  * <span data-ttu-id="e5b8d-720">Efektivně zpracovává výjimku.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-720">Effectively handles the exception.</span></span>
  * <span data-ttu-id="e5b8d-721">`Result`je spuštěn, jako kdyby byl vrácen z metody Action.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-721">`Result` is executed as if it was returned from the action method.</span></span>

<span data-ttu-id="e5b8d-722">V případě `IAsyncActionFilter` volání <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> :</span><span class="sxs-lookup"><span data-stu-id="e5b8d-722">For an `IAsyncActionFilter`, a call to the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span></span>

* <span data-ttu-id="e5b8d-723">Provede všechny následné filtry akcí a metodu Action.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-723">Executes any subsequent action filters and the action method.</span></span>
* <span data-ttu-id="e5b8d-724">Vrací objekt `ActionExecutedContext`.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-724">Returns `ActionExecutedContext`.</span></span>

<span data-ttu-id="e5b8d-725">Do krátkodobého okruhu, přiřaďte <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> k instanci výsledku a Nevolejte `next` (a `ActionExecutionDelegate` ).</span><span class="sxs-lookup"><span data-stu-id="e5b8d-725">To short-circuit, assign <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> to a result instance and don't call `next` (the `ActionExecutionDelegate`).</span></span>

<span data-ttu-id="e5b8d-726">Rozhraní poskytuje abstrakci <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> , která může být podtříd.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-726">The framework provides an abstract <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> that can be subclassed.</span></span>

<span data-ttu-id="e5b8d-727">`OnActionExecuting`Filtr akcí lze použít k těmto akcím:</span><span class="sxs-lookup"><span data-stu-id="e5b8d-727">The `OnActionExecuting` action filter can be used to:</span></span>

* <span data-ttu-id="e5b8d-728">Ověří stav modelu.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-728">Validate model state.</span></span>
* <span data-ttu-id="e5b8d-729">Pokud je stav neplatný, vrátí se chyba.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-729">Return an error if the state is invalid.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet)]

<span data-ttu-id="e5b8d-730">`OnActionExecuted`Metoda se spustí za metodou akce:</span><span class="sxs-lookup"><span data-stu-id="e5b8d-730">The `OnActionExecuted` method runs after the action method:</span></span>

* <span data-ttu-id="e5b8d-731">A mohou zobrazit výsledky akce a manipulovat s nimi prostřednictvím <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> Vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-731">And can see and manipulate the results of the action through the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> property.</span></span>
* <span data-ttu-id="e5b8d-732"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled>je nastaven na hodnotu true, pokud bylo provedení akce zkráceno jiným filtrem.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-732"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> is set to true if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="e5b8d-733"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception>je nastavena na hodnotu jinou než null, pokud akce nebo filtr následné akce vyvolaly výjimku.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-733"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> is set to a non-null value if the action or a subsequent action filter threw an exception.</span></span> <span data-ttu-id="e5b8d-734">Nastavení `Exception` na hodnotu null:</span><span class="sxs-lookup"><span data-stu-id="e5b8d-734">Setting `Exception` to null:</span></span>

  * <span data-ttu-id="e5b8d-735">Efektivně zpracovává výjimku.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-735">Effectively handles an exception.</span></span>
  * <span data-ttu-id="e5b8d-736">`ActionExecutedContext.Result`je spuštěn, jako kdyby byl vrácen normálně z metody Action.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-736">`ActionExecutedContext.Result` is executed as if it were returned normally from the action method.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet2&higlight=12-99)]

## <a name="exception-filters"></a><span data-ttu-id="e5b8d-737">Filtry výjimek</span><span class="sxs-lookup"><span data-stu-id="e5b8d-737">Exception filters</span></span>

<span data-ttu-id="e5b8d-738">Filtry výjimek:</span><span class="sxs-lookup"><span data-stu-id="e5b8d-738">Exception filters:</span></span>

* <span data-ttu-id="e5b8d-739">Implementujte <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> nebo <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter> .</span><span class="sxs-lookup"><span data-stu-id="e5b8d-739">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span></span> 
* <span data-ttu-id="e5b8d-740">Dá se použít k implementaci běžných zásad zpracování chyb.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-740">Can be used to implement common error handling policies.</span></span>

<span data-ttu-id="e5b8d-741">Následující ukázkový filtr výjimek používá vlastní zobrazení chyb pro zobrazení podrobností o výjimkách, ke kterým dochází při vývoji aplikace:</span><span class="sxs-lookup"><span data-stu-id="e5b8d-741">The following sample exception filter uses a custom error view to display details about exceptions that occur when the app is in development:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/CustomExceptionFilter.cs?name=snippet_ExceptionFilter&highlight=16-19)]

<span data-ttu-id="e5b8d-742">Filtry výjimek:</span><span class="sxs-lookup"><span data-stu-id="e5b8d-742">Exception filters:</span></span>

* <span data-ttu-id="e5b8d-743">Nemusíte mít události před a po.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-743">Don't have before and after events.</span></span>
* <span data-ttu-id="e5b8d-744">Implementujte <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> nebo <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*> .</span><span class="sxs-lookup"><span data-stu-id="e5b8d-744">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span></span>
* <span data-ttu-id="e5b8d-745">Zpracování neošetřených výjimek, ke kterým dochází při Razor vytváření stránky nebo kontroleru, [vázání modelů](xref:mvc/models/model-binding), filtrů akcí nebo metod akcí.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-745">Handle unhandled exceptions that occur in Razor Page or controller creation, [model binding](xref:mvc/models/model-binding), action filters, or action methods.</span></span>
* <span data-ttu-id="e5b8d-746">**Nezachycujte** výjimky, ke kterým dochází v filtrech prostředků, výsledných filtrech nebo v důsledku provádění výsledků MVC.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-746">Do **not** catch exceptions that occur in resource filters, result filters, or MVC result execution.</span></span>

<span data-ttu-id="e5b8d-747">Chcete-li zpracovat výjimku, nastavte <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> vlastnost na `true` nebo zapište odpověď.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-747">To handle an exception, set the <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> property to `true` or write a response.</span></span> <span data-ttu-id="e5b8d-748">Tím se zastaví šíření výjimky.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-748">This stops propagation of the exception.</span></span> <span data-ttu-id="e5b8d-749">Filtr výjimek nemůže vypnout výjimku na "úspěch".</span><span class="sxs-lookup"><span data-stu-id="e5b8d-749">An exception filter can't turn an exception into a "success".</span></span> <span data-ttu-id="e5b8d-750">To může provést pouze filtr akcí.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-750">Only an action filter can do that.</span></span>

<span data-ttu-id="e5b8d-751">Filtry výjimek:</span><span class="sxs-lookup"><span data-stu-id="e5b8d-751">Exception filters:</span></span>

* <span data-ttu-id="e5b8d-752">Jsou vhodné pro vytváření přesahů výjimek, ke kterým dochází v rámci akcí.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-752">Are good for trapping exceptions that occur within actions.</span></span>
* <span data-ttu-id="e5b8d-753">Nejsou tak flexibilní jako middleware při zpracování chyb.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-753">Are not as flexible as error handling middleware.</span></span>

<span data-ttu-id="e5b8d-754">Preferovat middleware pro zpracování výjimek.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-754">Prefer middleware for exception handling.</span></span> <span data-ttu-id="e5b8d-755">Filtry výjimek použijte pouze v případě, že se zpracování chyb *liší* v závislosti na tom, která metoda Action je volána.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-755">Use exception filters only where error handling *differs* based on which action method is called.</span></span> <span data-ttu-id="e5b8d-756">Aplikace může například mít metody akcí pro koncové body rozhraní API i pro zobrazení/HTML.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-756">For example, an app might have action methods for both API endpoints and for views/HTML.</span></span> <span data-ttu-id="e5b8d-757">Koncové body rozhraní API mohou vracet informace o chybě jako JSON, zatímco akce na základě zobrazení by mohly vracet chybovou stránku jako HTML.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-757">The API endpoints could return error information as JSON, while the view-based actions could return an error page as HTML.</span></span>

## <a name="result-filters"></a><span data-ttu-id="e5b8d-758">Filtry výsledků</span><span class="sxs-lookup"><span data-stu-id="e5b8d-758">Result filters</span></span>

<span data-ttu-id="e5b8d-759">Filtry výsledků:</span><span class="sxs-lookup"><span data-stu-id="e5b8d-759">Result filters:</span></span>

* <span data-ttu-id="e5b8d-760">Implementace rozhraní:</span><span class="sxs-lookup"><span data-stu-id="e5b8d-760">Implement an interface:</span></span>
  * <span data-ttu-id="e5b8d-761"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> nebo <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="e5b8d-761"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
  * <span data-ttu-id="e5b8d-762"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> nebo <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span><span class="sxs-lookup"><span data-stu-id="e5b8d-762"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span></span>
* <span data-ttu-id="e5b8d-763">Jejich spuštění obklopuje provádění výsledků akcí.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-763">Their execution surrounds the execution of action results.</span></span>

### <a name="iresultfilter-and-iasyncresultfilter"></a><span data-ttu-id="e5b8d-764">IResultFilter a IAsyncResultFilter</span><span class="sxs-lookup"><span data-stu-id="e5b8d-764">IResultFilter and IAsyncResultFilter</span></span>

<span data-ttu-id="e5b8d-765">Následující kód ukazuje filtr výsledků, který přidá hlavičku protokolu HTTP:</span><span class="sxs-lookup"><span data-stu-id="e5b8d-765">The following code shows a result filter that adds an HTTP header:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="e5b8d-766">Typ výsledku, který se má provést, závisí na akci.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-766">The kind of result being executed depends on the action.</span></span> <span data-ttu-id="e5b8d-767">Akce vracející zobrazení zahrnuje všechny zpracování Razor jako součást prováděné <xref:Microsoft.AspNetCore.Mvc.ViewResult> .</span><span class="sxs-lookup"><span data-stu-id="e5b8d-767">An action returning a view would include all razor processing as part of the <xref:Microsoft.AspNetCore.Mvc.ViewResult> being executed.</span></span> <span data-ttu-id="e5b8d-768">Metoda rozhraní API může provést určitou serializaci v rámci provádění výsledku.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-768">An API method might perform some serialization as part of the execution of the result.</span></span> <span data-ttu-id="e5b8d-769">Přečtěte si další informace o [výsledcích akcí](xref:mvc/controllers/actions).</span><span class="sxs-lookup"><span data-stu-id="e5b8d-769">Learn more about [action results](xref:mvc/controllers/actions).</span></span>

<span data-ttu-id="e5b8d-770">Filtry výsledků se spustí pouze v případě, že akce nebo filtr akcí vytvoří výsledek akce.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-770">Result filters are only executed when an action or action filter produces an action result.</span></span> <span data-ttu-id="e5b8d-771">Filtry výsledků nejsou provedeny v těchto případech:</span><span class="sxs-lookup"><span data-stu-id="e5b8d-771">Result filters are not executed when:</span></span>

* <span data-ttu-id="e5b8d-772">Filtr autorizace nebo filtr prostředků pro krátké okruhy kanálu.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-772">An authorization filter or resource filter short-circuits the pipeline.</span></span>
* <span data-ttu-id="e5b8d-773">Filtr výjimek zpracovává výjimku tím, že vyprodukuje výsledek akce.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-773">An exception filter handles an exception by producing an action result.</span></span>

<span data-ttu-id="e5b8d-774"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName>Metoda může zkrátit provádění výsledků akce a následných filtrů výsledků nastavením <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> na `true` .</span><span class="sxs-lookup"><span data-stu-id="e5b8d-774">The <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> method can short-circuit execution of the action result and subsequent result filters by setting <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> to `true`.</span></span> <span data-ttu-id="e5b8d-775">Zapište do objektu Response v případě krátkého okruhu, abyste se vyhnuli vygenerování prázdné odpovědi.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-775">Write to the response object when short-circuiting to avoid generating an empty response.</span></span> <span data-ttu-id="e5b8d-776">Vyvolání výjimky v `IResultFilter.OnResultExecuting` bude:</span><span class="sxs-lookup"><span data-stu-id="e5b8d-776">Throwing an exception in `IResultFilter.OnResultExecuting` will:</span></span>

* <span data-ttu-id="e5b8d-777">Zabraňte provádění výsledků akce a dalších filtrů.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-777">Prevent execution of the action result and subsequent filters.</span></span>
* <span data-ttu-id="e5b8d-778">Být považována za selhání namísto úspěšného výsledku.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-778">Be treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="e5b8d-779">Při <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> spuštění metody je odpověď pravděpodobně již odeslána klientovi.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-779">When the <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> method runs, the response has likely already been sent to the client.</span></span> <span data-ttu-id="e5b8d-780">Pokud byla odpověď již odeslána klientovi, nelze ji dále změnit.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-780">If the response has already been sent to the client, it cannot be changed further.</span></span>

<span data-ttu-id="e5b8d-781">`ResultExecutedContext.Canceled`je nastaven na hodnotu, `true` Pokud je spuštění výsledku akce zkráceno jiným filtrem.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-781">`ResultExecutedContext.Canceled` is set to `true` if the action result execution was short-circuited by another filter.</span></span>

<span data-ttu-id="e5b8d-782">`ResultExecutedContext.Exception`je nastavena na hodnotu jinou než null, pokud výsledek akce nebo následný filtr výsledků vyvolal výjimku.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-782">`ResultExecutedContext.Exception` is set to a non-null value if the action result or a subsequent result filter threw an exception.</span></span> <span data-ttu-id="e5b8d-783">Nastavení `Exception` na hodnotu null efektivně zpracovává výjimku a brání výjimce znovu vyvolat ASP.NET Core později v kanálu.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-783">Setting `Exception` to null effectively handles an exception and prevents the exception from being rethrown by ASP.NET Core later in the pipeline.</span></span> <span data-ttu-id="e5b8d-784">Neexistuje žádný spolehlivý způsob, jak zapisovat data do odpovědi při zpracování výjimky ve filtru výsledků.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-784">There is no reliable way to write data to a response when handling an exception in a result filter.</span></span> <span data-ttu-id="e5b8d-785">Pokud byly hlavičky vyprázdněny do klienta, pokud výsledek akce vyvolá výjimku, neexistuje žádný spolehlivý mechanismus pro odeslání kódu chyby.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-785">If the headers have been flushed to the client when an action result throws an exception, there's no reliable mechanism to send a failure code.</span></span>

<span data-ttu-id="e5b8d-786">V případě <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter> volání `await next` na se <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> spustí všechny následné filtry výsledků a výsledek akce.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-786">For an <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, a call to `await next` on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> executes any subsequent result filters and the action result.</span></span> <span data-ttu-id="e5b8d-787">Do krátkodobého okruhu nastavte [ResultExecutingContext. Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) na `true` a Nevolejte na `ResultExecutionDelegate` :</span><span class="sxs-lookup"><span data-stu-id="e5b8d-787">To short-circuit, set [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) to `true` and don't call the `ResultExecutionDelegate`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MyAsyncResponseFilter.cs?name=snippet)]

<span data-ttu-id="e5b8d-788">Rozhraní poskytuje abstrakci `ResultFilterAttribute` , která může být podtříd.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-788">The framework provides an abstract `ResultFilterAttribute` that can be subclassed.</span></span> <span data-ttu-id="e5b8d-789">Dříve uvedená třída [AddHeaderAttribute](#add-header-attribute) je příkladem atributu filtru výsledků.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-789">The [AddHeaderAttribute](#add-header-attribute) class shown previously is an example of a result filter attribute.</span></span>

### <a name="ialwaysrunresultfilter-and-iasyncalwaysrunresultfilter"></a><span data-ttu-id="e5b8d-790">IAlwaysRunResultFilter a IAsyncAlwaysRunResultFilter</span><span class="sxs-lookup"><span data-stu-id="e5b8d-790">IAlwaysRunResultFilter and IAsyncAlwaysRunResultFilter</span></span>

<span data-ttu-id="e5b8d-791"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter>Rozhraní a <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> deklaruje <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> implementaci, která je spuštěna pro všechny výsledky akce.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-791">The <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> interfaces declare an <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> implementation that runs for all action results.</span></span> <span data-ttu-id="e5b8d-792">To zahrnuje výsledky akcí, které vytvořil:</span><span class="sxs-lookup"><span data-stu-id="e5b8d-792">This includes action results produced by:</span></span>

* <span data-ttu-id="e5b8d-793">Filtry autorizace a filtry prostředků, které mají krátký okruh.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-793">Authorization filters and resource filters that short-circuit.</span></span>
* <span data-ttu-id="e5b8d-794">Filtry výjimek.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-794">Exception filters.</span></span>

<span data-ttu-id="e5b8d-795">Například následující filtr vždy spustí a nastaví výsledek akce ( <xref:Microsoft.AspNetCore.Mvc.ObjectResult> ) s *422 nezpracovaným* stavovým kódem entity, pokud se nezdaří vyjednávání obsahu:</span><span class="sxs-lookup"><span data-stu-id="e5b8d-795">For example, the following filter always runs and sets an action result (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) with a *422 Unprocessable Entity* status code when content negotiation fails:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/UnprocessableResultFilter.cs?name=snippet)]

### <a name="ifilterfactory"></a><span data-ttu-id="e5b8d-796">IFilterFactory</span><span class="sxs-lookup"><span data-stu-id="e5b8d-796">IFilterFactory</span></span>

<span data-ttu-id="e5b8d-797"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>implementuje <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> .</span><span class="sxs-lookup"><span data-stu-id="e5b8d-797"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span></span> <span data-ttu-id="e5b8d-798">Proto `IFilterFactory` může být instance použita jako `IFilterMetadata` instance kdekoli v kanálu filtru.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-798">Therefore, an `IFilterFactory` instance can be used as an `IFilterMetadata` instance anywhere in the filter pipeline.</span></span> <span data-ttu-id="e5b8d-799">Když se modul runtime připraví k vyvolání filtru, pokusí se ho přetypovat na `IFilterFactory` .</span><span class="sxs-lookup"><span data-stu-id="e5b8d-799">When the runtime prepares to invoke the filter, it attempts to cast it to an `IFilterFactory`.</span></span> <span data-ttu-id="e5b8d-800">Pokud je toto přetypování úspěšné, <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> je volána metoda pro vytvoření `IFilterMetadata` instance, která je vyvolána.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-800">If that cast succeeds, the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method is called to create the `IFilterMetadata` instance that is invoked.</span></span> <span data-ttu-id="e5b8d-801">To poskytuje flexibilní návrh, protože přesný kanál filtru není nutné nastavit explicitně při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-801">This provides a flexible design, since the precise filter pipeline doesn't need to be set explicitly when the app starts.</span></span>

<span data-ttu-id="e5b8d-802">`IFilterFactory`může být implementováno pomocí implementace vlastního atributu jako jiný přístup k vytváření filtrů:</span><span class="sxs-lookup"><span data-stu-id="e5b8d-802">`IFilterFactory` can be implemented using custom attribute implementations as another approach to creating filters:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/AddHeaderWithFactoryAttribute.cs?name=snippet_IFilterFactory&highlight=1,4,5,6,7)]

<span data-ttu-id="e5b8d-803">Předchozí kód lze otestovat spuštěním [ukázky stahování](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample):</span><span class="sxs-lookup"><span data-stu-id="e5b8d-803">The preceding code can be tested by running the [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample):</span></span>

* <span data-ttu-id="e5b8d-804">Vyvolejte vývojářské nástroje F12.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-804">Invoke the F12 developer tools.</span></span>
* <span data-ttu-id="e5b8d-805">Přejděte na adresu `https://localhost:5001/Sample/HeaderWithFactory`.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-805">Navigate to `https://localhost:5001/Sample/HeaderWithFactory`.</span></span>

<span data-ttu-id="e5b8d-806">Vývojářské nástroje F12 zobrazují následující hlavičky odpovědí přidané ukázkovým kódem:</span><span class="sxs-lookup"><span data-stu-id="e5b8d-806">The F12 developer tools display the following response headers added by the sample code:</span></span>

* <span data-ttu-id="e5b8d-807">**Autor:**`Joe Smith`</span><span class="sxs-lookup"><span data-stu-id="e5b8d-807">**author:** `Joe Smith`</span></span>
* <span data-ttu-id="e5b8d-808">**globaladdheader:**`Result filter added to MvcOptions.Filters`</span><span class="sxs-lookup"><span data-stu-id="e5b8d-808">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span></span>
* <span data-ttu-id="e5b8d-809">**interní:**`My header`</span><span class="sxs-lookup"><span data-stu-id="e5b8d-809">**internal:** `My header`</span></span>

<span data-ttu-id="e5b8d-810">Předchozí kód vytvoří hlavičku **interního:** `My header` Response.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-810">The preceding code creates the **internal:** `My header` response header.</span></span>

### <a name="ifilterfactory-implemented-on-an-attribute"></a><span data-ttu-id="e5b8d-811">IFilterFactory implementované u atributu</span><span class="sxs-lookup"><span data-stu-id="e5b8d-811">IFilterFactory implemented on an attribute</span></span>

<!-- Review 
This section needs to be rewritten.
What's a non-named attribute?
-->

<span data-ttu-id="e5b8d-812">Filtry, které implementují, `IFilterFactory` jsou užitečné pro filtry, které:</span><span class="sxs-lookup"><span data-stu-id="e5b8d-812">Filters that implement `IFilterFactory` are useful for filters that:</span></span>

* <span data-ttu-id="e5b8d-813">Nevyžadovat předávání parametrů</span><span class="sxs-lookup"><span data-stu-id="e5b8d-813">Don't require passing parameters.</span></span>
* <span data-ttu-id="e5b8d-814">Musí mít závislosti konstruktoru, které musí vyplnit DI.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-814">Have constructor dependencies that need to be filled by DI.</span></span>

<span data-ttu-id="e5b8d-815"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>implementuje <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> .</span><span class="sxs-lookup"><span data-stu-id="e5b8d-815"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="e5b8d-816">`IFilterFactory`zpřístupňuje <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> metodu pro vytvoření <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-816">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="e5b8d-817">`CreateInstance`Načte zadaný typ z kontejneru Services (DI).</span><span class="sxs-lookup"><span data-stu-id="e5b8d-817">`CreateInstance` loads the specified type from the services container (DI).</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/SampleActionFilterAttribute.cs?name=snippet_TypeFilterAttribute&highlight=1,3,7)]

<span data-ttu-id="e5b8d-818">Následující kód ukazuje tři přístupy k použití `[SampleActionFilter]` :</span><span class="sxs-lookup"><span data-stu-id="e5b8d-818">The following code shows three approaches to applying the `[SampleActionFilter]`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet&highlight=1)]

<span data-ttu-id="e5b8d-819">V předchozím kódu upravení metoda s jako `[SampleActionFilter]` upřednostňovaným přístupem k použití `SampleActionFilter` .</span><span class="sxs-lookup"><span data-stu-id="e5b8d-819">In the preceding code, decorating the method with `[SampleActionFilter]` is the preferred approach to applying the `SampleActionFilter`.</span></span>

## <a name="using-middleware-in-the-filter-pipeline"></a><span data-ttu-id="e5b8d-820">Použití middlewaru v kanálu filtru</span><span class="sxs-lookup"><span data-stu-id="e5b8d-820">Using middleware in the filter pipeline</span></span>

<span data-ttu-id="e5b8d-821">Filtry prostředků fungují jako [middleware](xref:fundamentals/middleware/index) v tom, že obklopují provádění všech položek, které jsou později v kanálu.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-821">Resource filters work like [middleware](xref:fundamentals/middleware/index) in that they surround the execution of everything that comes later in the pipeline.</span></span> <span data-ttu-id="e5b8d-822">Filtry se ale liší od middlewaru v tom, že jsou součástí modulu ASP.NET Core Runtime, což znamená, že mají přístup k ASP.NET Core kontextu a konstrukcí.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-822">But filters differ from middleware in that they're part of the ASP.NET Core runtime, which means that they have access to ASP.NET Core context and constructs.</span></span>

<span data-ttu-id="e5b8d-823">Chcete-li použít middleware jako filtr, vytvořte typ s `Configure` metodou, která určuje middleware pro vložení do kanálu filtru.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-823">To use middleware as a filter, create a type with a `Configure` method that specifies the middleware to inject into the filter pipeline.</span></span> <span data-ttu-id="e5b8d-824">Následující příklad používá middleware Localization k vytvoření aktuální jazykové verze pro požadavek:</span><span class="sxs-lookup"><span data-stu-id="e5b8d-824">The following example uses the localization middleware to establish the current culture for a request:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LocalizationPipeline.cs?name=snippet_MiddlewareFilter&highlight=3,22)]

<span data-ttu-id="e5b8d-825">Pomocí <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> Spusťte middleware:</span><span class="sxs-lookup"><span data-stu-id="e5b8d-825">Use the <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> to run the middleware:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_MiddlewareFilter&highlight=2)]

<span data-ttu-id="e5b8d-826">Filtry middlewaru jsou spouštěny ve stejné fázi kanálu filtru jako filtry prostředků, před vazbami modelů a po zbytek kanálu.</span><span class="sxs-lookup"><span data-stu-id="e5b8d-826">Middleware filters run at the same stage of the filter pipeline as Resource filters, before model binding and after the rest of the pipeline.</span></span>

## <a name="next-actions"></a><span data-ttu-id="e5b8d-827">Další akce</span><span class="sxs-lookup"><span data-stu-id="e5b8d-827">Next actions</span></span>

* <span data-ttu-id="e5b8d-828">Viz [metody filtru pro Razor stránky](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="e5b8d-828">See [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>
* <span data-ttu-id="e5b8d-829">Pro experimentování s filtry, [stažení, otestování a úpravy ukázky GitHubu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample).</span><span class="sxs-lookup"><span data-stu-id="e5b8d-829">To experiment with filters, [download, test, and modify the GitHub sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample).</span></span>

::: moniker-end
