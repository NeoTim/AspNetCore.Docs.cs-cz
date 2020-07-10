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
ms.openlocfilehash: 0141ad2df5216183424980a6ca50bf6bcd64ade5
ms.sourcegitcommit: 50e7c970f327dbe92d45eaf4c21caa001c9106d0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2020
ms.locfileid: "86213061"
---
# <a name="filters-in-aspnet-core"></a><span data-ttu-id="3e19b-103">Filtry v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3e19b-103">Filters in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="3e19b-104">[Kirka Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Dykstra](https://github.com/tdykstra/)a [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="3e19b-104">By [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="3e19b-105">*Filtry* v ASP.NET Core umožňují spuštění kódu před nebo po určitých fázích v kanálu zpracování požadavků.</span><span class="sxs-lookup"><span data-stu-id="3e19b-105">*Filters* in ASP.NET Core allow code to be run before or after specific stages in the request processing pipeline.</span></span>

<span data-ttu-id="3e19b-106">Předdefinované filtry zpracovávají úlohy, jako například:</span><span class="sxs-lookup"><span data-stu-id="3e19b-106">Built-in filters handle tasks such as:</span></span>

* <span data-ttu-id="3e19b-107">Autorizace (zabránění přístupu k prostředkům, ke kterým uživatel nemá oprávnění).</span><span class="sxs-lookup"><span data-stu-id="3e19b-107">Authorization (preventing access to resources a user isn't authorized for).</span></span>
* <span data-ttu-id="3e19b-108">Ukládání odpovědí do mezipaměti (při krátkém okruhu kanálu požadavků, který vrátí odpověď uloženou v mezipaměti).</span><span class="sxs-lookup"><span data-stu-id="3e19b-108">Response caching (short-circuiting the request pipeline to return a cached response).</span></span>

<span data-ttu-id="3e19b-109">Vlastní filtry je možné vytvořit pro zpracování vzájemně se týkajících se otázek.</span><span class="sxs-lookup"><span data-stu-id="3e19b-109">Custom filters can be created to handle cross-cutting concerns.</span></span> <span data-ttu-id="3e19b-110">Mezi obavy mezi průřezy patří zpracování chyb, ukládání do mezipaměti, konfigurace, autorizace a protokolování.</span><span class="sxs-lookup"><span data-stu-id="3e19b-110">Examples of cross-cutting concerns include error handling, caching, configuration, authorization, and logging.</span></span>  <span data-ttu-id="3e19b-111">Filtry zabraňují duplikování kódu.</span><span class="sxs-lookup"><span data-stu-id="3e19b-111">Filters avoid duplicating code.</span></span> <span data-ttu-id="3e19b-112">Například filtr výjimek zpracování chyb může konsolidovat zpracování chyb.</span><span class="sxs-lookup"><span data-stu-id="3e19b-112">For example, an error handling exception filter could consolidate error handling.</span></span>

<span data-ttu-id="3e19b-113">Tento dokument se týká Razor stránek, řadičů rozhraní API a řadičů se zobrazeními.</span><span class="sxs-lookup"><span data-stu-id="3e19b-113">This document applies to Razor Pages, API controllers, and controllers with views.</span></span> <span data-ttu-id="3e19b-114">Filtry nefungují přímo s [ Razor komponentami](xref:blazor/components/index).</span><span class="sxs-lookup"><span data-stu-id="3e19b-114">Filters don't work directly with [Razor components](xref:blazor/components/index).</span></span> <span data-ttu-id="3e19b-115">Filtr může nepřímo ovlivnit komponentu pouze v těchto případech:</span><span class="sxs-lookup"><span data-stu-id="3e19b-115">A filter can only indirectly affect a component when:</span></span>

* <span data-ttu-id="3e19b-116">Komponenta je vložena do stránky nebo zobrazení.</span><span class="sxs-lookup"><span data-stu-id="3e19b-116">The component is embedded in a page or view.</span></span>
* <span data-ttu-id="3e19b-117">Stránka nebo kontroler nebo zobrazení používá filtr.</span><span class="sxs-lookup"><span data-stu-id="3e19b-117">The page or controller/view uses the filter.</span></span>

<span data-ttu-id="3e19b-118">[Zobrazit nebo Stáhnout ukázku](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample) ([Jak stáhnout](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="3e19b-118">[View or download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="how-filters-work"></a><span data-ttu-id="3e19b-119">Jak fungují filtry</span><span class="sxs-lookup"><span data-stu-id="3e19b-119">How filters work</span></span>

<span data-ttu-id="3e19b-120">Filtry se spouštějí v *kanálu vyvolání akce ASP.NET Core*, někdy označované jako *kanál filtru*.</span><span class="sxs-lookup"><span data-stu-id="3e19b-120">Filters run within the *ASP.NET Core action invocation pipeline*, sometimes referred to as the *filter pipeline*.</span></span> <span data-ttu-id="3e19b-121">Kanál filtru se spustí po ASP.NET Core vybere akci, která se má provést.</span><span class="sxs-lookup"><span data-stu-id="3e19b-121">The filter pipeline runs after ASP.NET Core selects the action to execute.</span></span>

![Požadavek se zpracovává prostřednictvím jiného middlewaru, middleware směrování, výběru akcí a kanálu vyvolání akce.](filters/_static/filter-pipeline-1.png)

### <a name="filter-types"></a><span data-ttu-id="3e19b-124">Typy filtrů</span><span class="sxs-lookup"><span data-stu-id="3e19b-124">Filter types</span></span>

<span data-ttu-id="3e19b-125">Každý typ filtru se spustí v jiné fázi kanálu filtru:</span><span class="sxs-lookup"><span data-stu-id="3e19b-125">Each filter type is executed at a different stage in the filter pipeline:</span></span>

* <span data-ttu-id="3e19b-126">[Filtry autorizace](#authorization-filters) se spouštějí jako první a používají se k určení, jestli je uživatel autorizovaný pro požadavek.</span><span class="sxs-lookup"><span data-stu-id="3e19b-126">[Authorization filters](#authorization-filters) run first and are used to determine whether the user is authorized for the request.</span></span> <span data-ttu-id="3e19b-127">Filtry autorizace pro krátkodobé vynechání kanálu, pokud žádost není autorizována.</span><span class="sxs-lookup"><span data-stu-id="3e19b-127">Authorization filters short-circuit the pipeline if the request is not authorized.</span></span>

* <span data-ttu-id="3e19b-128">[Filtry prostředků](#resource-filters):</span><span class="sxs-lookup"><span data-stu-id="3e19b-128">[Resource filters](#resource-filters):</span></span>

  * <span data-ttu-id="3e19b-129">Spusťte po autorizaci.</span><span class="sxs-lookup"><span data-stu-id="3e19b-129">Run after authorization.</span></span>  
  * <span data-ttu-id="3e19b-130"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*>spustí kód před zbytek kanálu filtru.</span><span class="sxs-lookup"><span data-stu-id="3e19b-130"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> runs code before the rest of the filter pipeline.</span></span> <span data-ttu-id="3e19b-131">Například `OnResourceExecuting` spustí kód před vazbou modelu.</span><span class="sxs-lookup"><span data-stu-id="3e19b-131">For example, `OnResourceExecuting` runs code before model binding.</span></span>
  * <span data-ttu-id="3e19b-132"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*>spustí kód po dokončení zbývající části kanálu.</span><span class="sxs-lookup"><span data-stu-id="3e19b-132"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> runs code after the rest of the pipeline has completed.</span></span>

* <span data-ttu-id="3e19b-133">[Filtry akcí](#action-filters):</span><span class="sxs-lookup"><span data-stu-id="3e19b-133">[Action filters](#action-filters):</span></span>

  * <span data-ttu-id="3e19b-134">Spustit kód bezprostředně před a po volání metody Action.</span><span class="sxs-lookup"><span data-stu-id="3e19b-134">Run code immediately before and after an action method is called.</span></span>
  * <span data-ttu-id="3e19b-135">Může změnit argumenty předané do akce.</span><span class="sxs-lookup"><span data-stu-id="3e19b-135">Can change the arguments passed into an action.</span></span>
  * <span data-ttu-id="3e19b-136">Může změnit výsledek vrácený z akce.</span><span class="sxs-lookup"><span data-stu-id="3e19b-136">Can change the result returned from the action.</span></span>
  * <span data-ttu-id="3e19b-137">Nejsou **na** stránkách podporovány Razor .</span><span class="sxs-lookup"><span data-stu-id="3e19b-137">Are **not** supported in Razor Pages.</span></span>

* <span data-ttu-id="3e19b-138">[Filtry výjimek](#exception-filters) aplikují globální zásady na neošetřené výjimky, ke kterým došlo před zápisem textu odpovědi do.</span><span class="sxs-lookup"><span data-stu-id="3e19b-138">[Exception filters](#exception-filters) apply global policies to unhandled exceptions that occur before the response body has been written to.</span></span>

* <span data-ttu-id="3e19b-139">[Filtry výsledků](#result-filters) spouštějí kód bezprostředně před a po provedení výsledků akce.</span><span class="sxs-lookup"><span data-stu-id="3e19b-139">[Result filters](#result-filters) run code immediately before and after the execution of action results.</span></span> <span data-ttu-id="3e19b-140">Spustí se pouze v případě, že metoda akce byla úspěšně provedena.</span><span class="sxs-lookup"><span data-stu-id="3e19b-140">They run only when the action method has executed successfully.</span></span> <span data-ttu-id="3e19b-141">Jsou užitečné pro logiku, která musí obklopit zobrazení nebo formátovací modul.</span><span class="sxs-lookup"><span data-stu-id="3e19b-141">They are useful for logic that must surround view or formatter execution.</span></span>

<span data-ttu-id="3e19b-142">Následující diagram znázorňuje způsob interakce typů filtrů v kanálu filtru.</span><span class="sxs-lookup"><span data-stu-id="3e19b-142">The following diagram shows how filter types interact in the filter pipeline.</span></span>

![Požadavek se zpracovává pomocí autorizačních filtrů, filtrů prostředků, vazeb modelů, filtrů akcí, provádění akcí a konverze výsledků akcí, filtrů výjimek, výsledných filtrů a provádění výsledků.](filters/_static/filter-pipeline-2.png)

## <a name="implementation"></a><span data-ttu-id="3e19b-145">Implementace</span><span class="sxs-lookup"><span data-stu-id="3e19b-145">Implementation</span></span>

<span data-ttu-id="3e19b-146">Filtry podporují synchronní i asynchronní implementace prostřednictvím různých definic rozhraní.</span><span class="sxs-lookup"><span data-stu-id="3e19b-146">Filters support both synchronous and asynchronous implementations through different interface definitions.</span></span>

<span data-ttu-id="3e19b-147">Synchronní filtry spouštějí kód před a po fázi zřetězení.</span><span class="sxs-lookup"><span data-stu-id="3e19b-147">Synchronous filters run code before and after their pipeline stage.</span></span> <span data-ttu-id="3e19b-148">Například <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*> je volána před voláním metody Action.</span><span class="sxs-lookup"><span data-stu-id="3e19b-148">For example, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*> is called before the action method is called.</span></span> <span data-ttu-id="3e19b-149"><xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*>je volána po návratu metody Action.</span><span class="sxs-lookup"><span data-stu-id="3e19b-149"><xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*> is called after the action method returns.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="3e19b-150">Asynchronní filtry definují `On-Stage-ExecutionAsync` metodu.</span><span class="sxs-lookup"><span data-stu-id="3e19b-150">Asynchronous filters define an `On-Stage-ExecutionAsync` method.</span></span> <span data-ttu-id="3e19b-151">Například <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*> :</span><span class="sxs-lookup"><span data-stu-id="3e19b-151">For example, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*>:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/SampleAsyncActionFilter.cs?name=snippet)]

<span data-ttu-id="3e19b-152">V předchozím kódu `SampleAsyncActionFilter` obsahuje znak <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> ( `next` ), který provádí metodu Action.</span><span class="sxs-lookup"><span data-stu-id="3e19b-152">In the preceding code, the `SampleAsyncActionFilter` has an <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`) that executes the action method.</span></span>

### <a name="multiple-filter-stages"></a><span data-ttu-id="3e19b-153">Několik fází filtru</span><span class="sxs-lookup"><span data-stu-id="3e19b-153">Multiple filter stages</span></span>

<span data-ttu-id="3e19b-154">Rozhraní pro více fází filtru lze implementovat v rámci jedné třídy.</span><span class="sxs-lookup"><span data-stu-id="3e19b-154">Interfaces for multiple filter stages can be implemented in a single class.</span></span> <span data-ttu-id="3e19b-155">Například <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> Třída implementuje:</span><span class="sxs-lookup"><span data-stu-id="3e19b-155">For example, the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> class implements:</span></span>

* <span data-ttu-id="3e19b-156">Synchronní: <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> a<xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter></span><span class="sxs-lookup"><span data-stu-id="3e19b-156">Synchronous: <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> and  <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter></span></span>
* <span data-ttu-id="3e19b-157">Asynchronní: <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> a<xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="3e19b-157">Asynchronous: <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
* <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>

<span data-ttu-id="3e19b-158">Implementujte **buď** synchronní, nebo asynchronní verzi rozhraní filtru, **nikoli** obojí.</span><span class="sxs-lookup"><span data-stu-id="3e19b-158">Implement **either** the synchronous or the async version of a filter interface, **not** both.</span></span> <span data-ttu-id="3e19b-159">Modul runtime nejprve kontroluje, zda filtr implementuje asynchronní rozhraní a v takovém případě jej zavolá.</span><span class="sxs-lookup"><span data-stu-id="3e19b-159">The runtime checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="3e19b-160">V takovém případě volá metody synchronního rozhraní.</span><span class="sxs-lookup"><span data-stu-id="3e19b-160">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="3e19b-161">Pokud jsou asynchronní i synchronní rozhraní implementovány v jedné třídě, je volána pouze asynchronní metoda.</span><span class="sxs-lookup"><span data-stu-id="3e19b-161">If both asynchronous and synchronous interfaces are implemented in one class, only the async method is called.</span></span> <span data-ttu-id="3e19b-162">Při použití abstraktních tříd <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> , jako je přepsání pouze synchronních metod nebo asynchronní metody pro každý typ filtru.</span><span class="sxs-lookup"><span data-stu-id="3e19b-162">When using abstract classes like <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>, override only the synchronous methods or the asynchronous method for each filter type.</span></span>

### <a name="built-in-filter-attributes"></a><span data-ttu-id="3e19b-163">Předdefinované atributy filtru</span><span class="sxs-lookup"><span data-stu-id="3e19b-163">Built-in filter attributes</span></span>

<span data-ttu-id="3e19b-164">ASP.NET Core obsahuje integrované filtry založené na atributech, které mohou být roztříděné a přizpůsobené.</span><span class="sxs-lookup"><span data-stu-id="3e19b-164">ASP.NET Core includes built-in attribute-based filters that can be subclassed and customized.</span></span> <span data-ttu-id="3e19b-165">Například následující filtr výsledků přidá hlavičku do odpovědi:</span><span class="sxs-lookup"><span data-stu-id="3e19b-165">For example, the following result filter adds a header to the response:</span></span>

<a name="add-header-attribute"></a>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/AddHeaderAttribute.cs?name=snippet)]

<span data-ttu-id="3e19b-166">Atributy umožňují filtrům přijímat argumenty, jak je znázorněno v předchozím příkladu.</span><span class="sxs-lookup"><span data-stu-id="3e19b-166">Attributes allow filters to accept arguments, as shown in the preceding example.</span></span> <span data-ttu-id="3e19b-167">Použijte `AddHeaderAttribute` pro metodu Controller nebo Action a zadejte název a hodnotu HLAVIČKY http:</span><span class="sxs-lookup"><span data-stu-id="3e19b-167">Apply the `AddHeaderAttribute` to a controller or action method and specify the name and value of the HTTP header:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

<span data-ttu-id="3e19b-168">K prohlédnutí hlaviček použijte nástroj, jako je například nástroj pro [vývojáře v prohlížeči](https://developer.mozilla.org/docs/Learn/Common_questions/What_are_browser_developer_tools) .</span><span class="sxs-lookup"><span data-stu-id="3e19b-168">Use a tool such as the [browser developer tools](https://developer.mozilla.org/docs/Learn/Common_questions/What_are_browser_developer_tools) to examine the headers.</span></span> <span data-ttu-id="3e19b-169">V části **hlavičky odpovědi** `author: Rick Anderson` se zobrazí.</span><span class="sxs-lookup"><span data-stu-id="3e19b-169">Under **Response Headers**, `author: Rick Anderson` is displayed.</span></span>

<span data-ttu-id="3e19b-170">Následující kód implementuje `ActionFilterAttribute` :</span><span class="sxs-lookup"><span data-stu-id="3e19b-170">The following code implements an `ActionFilterAttribute` that:</span></span>

* <span data-ttu-id="3e19b-171">Přečte název a název z konfiguračního systému.</span><span class="sxs-lookup"><span data-stu-id="3e19b-171">Reads the title and name from the configuration system.</span></span> <span data-ttu-id="3e19b-172">Na rozdíl od předchozí ukázky, následující kód nevyžaduje přidání parametrů filtru do kódu.</span><span class="sxs-lookup"><span data-stu-id="3e19b-172">Unlike the previous sample, the following code doesn't require filter parameters to be added to the code.</span></span>
* <span data-ttu-id="3e19b-173">Přidá název a název do hlavičky odpovědi.</span><span class="sxs-lookup"><span data-stu-id="3e19b-173">Adds the title and name to the response header.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MyActionFilterAttribute.cs?name=snippet)]

<span data-ttu-id="3e19b-174">Možnosti konfigurace se poskytují z [konfiguračního systému](xref:fundamentals/configuration/index) pomocí [vzoru možností](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="3e19b-174">The configuration options are provided from the [configuration system](xref:fundamentals/configuration/index) using the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="3e19b-175">Například z *appsettings.js* souboru:</span><span class="sxs-lookup"><span data-stu-id="3e19b-175">For example, from the *appsettings.json* file:</span></span>

[!code-json[](filters/3.1sample/FiltersSample/appsettings.json)]

<span data-ttu-id="3e19b-176">V `StartUp.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="3e19b-176">In the `StartUp.ConfigureServices`:</span></span>

* <span data-ttu-id="3e19b-177">`PositionOptions`Třída je přidána do kontejneru služby s `"Position"` oblastí konfigurace.</span><span class="sxs-lookup"><span data-stu-id="3e19b-177">The `PositionOptions` class is added to the service container with the `"Position"` configuration area.</span></span>
* <span data-ttu-id="3e19b-178">`MyActionFilterAttribute`Přidá se do kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="3e19b-178">The `MyActionFilterAttribute` is added to the service container.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupAF.cs?name=snippet)]

<span data-ttu-id="3e19b-179">Následující kód ukazuje `PositionOptions` třídu:</span><span class="sxs-lookup"><span data-stu-id="3e19b-179">The following code shows the `PositionOptions` class:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Helper/PositionOptions.cs?name=snippet)]

<span data-ttu-id="3e19b-180">Následující kód platí `MyActionFilterAttribute` pro `Index2` metodu:</span><span class="sxs-lookup"><span data-stu-id="3e19b-180">The following code applies the `MyActionFilterAttribute` to the `Index2` method:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet2&highlight=9)]

<span data-ttu-id="3e19b-181">V části **hlavičky odpovědi**, `author: Rick Anderson` a `Editor: Joe Smith` se zobrazí při `Sample/Index2` volání koncového bodu.</span><span class="sxs-lookup"><span data-stu-id="3e19b-181">Under **Response Headers**, `author: Rick Anderson`, and `Editor: Joe Smith` is displayed when the `Sample/Index2` endpoint is called.</span></span>

<span data-ttu-id="3e19b-182">Následující kód platí `MyActionFilterAttribute` pro a na `AddHeaderAttribute` Razor stránce:</span><span class="sxs-lookup"><span data-stu-id="3e19b-182">The following code applies the `MyActionFilterAttribute` and the `AddHeaderAttribute` to the Razor Page:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Pages/Movies/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="3e19b-183">Filtry nelze použít na Razor metody obslužné rutiny stránky.</span><span class="sxs-lookup"><span data-stu-id="3e19b-183">Filters cannot be applied to Razor Page handler methods.</span></span> <span data-ttu-id="3e19b-184">Lze je použít buď na Razor model stránky, nebo globálně.</span><span class="sxs-lookup"><span data-stu-id="3e19b-184">They can be applied either to the Razor Page model or globally.</span></span>

<span data-ttu-id="3e19b-185">Některé z rozhraní filtru mají odpovídající atributy, které lze použít jako základní třídy pro vlastní implementace.</span><span class="sxs-lookup"><span data-stu-id="3e19b-185">Several of the filter interfaces have corresponding attributes that can be used as base classes for custom implementations.</span></span>

<span data-ttu-id="3e19b-186">Atributy filtru:</span><span class="sxs-lookup"><span data-stu-id="3e19b-186">Filter attributes:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ExceptionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>

## <a name="filter-scopes-and-order-of-execution"></a><span data-ttu-id="3e19b-187">Filtrovat obory a pořadí provádění</span><span class="sxs-lookup"><span data-stu-id="3e19b-187">Filter scopes and order of execution</span></span>

<span data-ttu-id="3e19b-188">Filtr lze přidat do kanálu v jednom ze tří *oborů*:</span><span class="sxs-lookup"><span data-stu-id="3e19b-188">A filter can be added to the pipeline at one of three *scopes*:</span></span>

* <span data-ttu-id="3e19b-189">Použití atributu u akce kontroleru.</span><span class="sxs-lookup"><span data-stu-id="3e19b-189">Using an attribute on a controller action.</span></span> <span data-ttu-id="3e19b-190">Atributy filtru nelze použít na Razor stránky metod obslužných rutin.</span><span class="sxs-lookup"><span data-stu-id="3e19b-190">Filter attributes cannot be applied to Razor Pages handler methods.</span></span>
* <span data-ttu-id="3e19b-191">Použití atributu na řadiči nebo na Razor stránce.</span><span class="sxs-lookup"><span data-stu-id="3e19b-191">Using an attribute on a controller or Razor Page.</span></span>
* <span data-ttu-id="3e19b-192">Globálně pro všechny řadiče, akce a Razor stránky, jak je znázorněno v následujícím kódu:</span><span class="sxs-lookup"><span data-stu-id="3e19b-192">Globally for all controllers, actions, and Razor Pages as shown in the following code:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder.cs?name=snippet)]

### <a name="default-order-of-execution"></a><span data-ttu-id="3e19b-193">Výchozí pořadí provádění</span><span class="sxs-lookup"><span data-stu-id="3e19b-193">Default order of execution</span></span>

<span data-ttu-id="3e19b-194">Pokud existuje více filtrů pro určitou fázi kanálu, rozsah Určuje výchozí pořadí provádění filtru.</span><span class="sxs-lookup"><span data-stu-id="3e19b-194">When there are multiple filters for a particular stage of the pipeline, scope determines the default order of filter execution.</span></span>  <span data-ttu-id="3e19b-195">Globální filtry obklopují filtry tříd, které jsou ve filtru metod Surround.</span><span class="sxs-lookup"><span data-stu-id="3e19b-195">Global filters surround class filters, which in turn surround method filters.</span></span>

<span data-ttu-id="3e19b-196">V důsledku vnořování filtru je *po* kódu spuštěn v obráceném pořadí *před* kódem.</span><span class="sxs-lookup"><span data-stu-id="3e19b-196">As a result of filter nesting, the *after* code of filters runs in the reverse order of the *before* code.</span></span> <span data-ttu-id="3e19b-197">Pořadí filtru:</span><span class="sxs-lookup"><span data-stu-id="3e19b-197">The filter sequence:</span></span>

* <span data-ttu-id="3e19b-198">*Před* kódem globálních filtrů.</span><span class="sxs-lookup"><span data-stu-id="3e19b-198">The *before* code of global filters.</span></span>
  * <span data-ttu-id="3e19b-199">*Před* kódem kontroleru a Razor filtru stránky.</span><span class="sxs-lookup"><span data-stu-id="3e19b-199">The *before* code of controller and Razor Page filters.</span></span>
    * <span data-ttu-id="3e19b-200">Kód *před* filtry metody Action.</span><span class="sxs-lookup"><span data-stu-id="3e19b-200">The *before* code of action method filters.</span></span>
    * <span data-ttu-id="3e19b-201">*Po* kódu filtry metody Action.</span><span class="sxs-lookup"><span data-stu-id="3e19b-201">The *after* code of action method filters.</span></span>
  * <span data-ttu-id="3e19b-202">*Po* kódu filtru řadiče a Razor stránky.</span><span class="sxs-lookup"><span data-stu-id="3e19b-202">The *after* code of controller and Razor Page filters.</span></span>
* <span data-ttu-id="3e19b-203">*Po* kódu globálních filtrů.</span><span class="sxs-lookup"><span data-stu-id="3e19b-203">The *after* code of global filters.</span></span>
  
<span data-ttu-id="3e19b-204">Následující příklad ilustruje pořadí, ve kterém jsou metody filtru volány pro filtry synchronních akcí.</span><span class="sxs-lookup"><span data-stu-id="3e19b-204">The following example that illustrates the order in which filter methods are called for synchronous action filters.</span></span>

| <span data-ttu-id="3e19b-205">Sequence</span><span class="sxs-lookup"><span data-stu-id="3e19b-205">Sequence</span></span> | <span data-ttu-id="3e19b-206">Rozsah filtru</span><span class="sxs-lookup"><span data-stu-id="3e19b-206">Filter scope</span></span> | <span data-ttu-id="3e19b-207">Filter – metoda</span><span class="sxs-lookup"><span data-stu-id="3e19b-207">Filter method</span></span> |
|:--------:|:------------:|:-------------:|
| <span data-ttu-id="3e19b-208">1</span><span class="sxs-lookup"><span data-stu-id="3e19b-208">1</span></span> | <span data-ttu-id="3e19b-209">Globální</span><span class="sxs-lookup"><span data-stu-id="3e19b-209">Global</span></span> | `OnActionExecuting` |
| <span data-ttu-id="3e19b-210">2</span><span class="sxs-lookup"><span data-stu-id="3e19b-210">2</span></span> | <span data-ttu-id="3e19b-211">Kontroler nebo Razor Stránka</span><span class="sxs-lookup"><span data-stu-id="3e19b-211">Controller or Razor Page</span></span>| `OnActionExecuting` |
| <span data-ttu-id="3e19b-212">3</span><span class="sxs-lookup"><span data-stu-id="3e19b-212">3</span></span> | <span data-ttu-id="3e19b-213">Metoda</span><span class="sxs-lookup"><span data-stu-id="3e19b-213">Method</span></span> | `OnActionExecuting` |
| <span data-ttu-id="3e19b-214">4</span><span class="sxs-lookup"><span data-stu-id="3e19b-214">4</span></span> | <span data-ttu-id="3e19b-215">Metoda</span><span class="sxs-lookup"><span data-stu-id="3e19b-215">Method</span></span> | `OnActionExecuted` |
| <span data-ttu-id="3e19b-216">5</span><span class="sxs-lookup"><span data-stu-id="3e19b-216">5</span></span> | <span data-ttu-id="3e19b-217">Kontroler nebo Razor Stránka</span><span class="sxs-lookup"><span data-stu-id="3e19b-217">Controller or Razor Page</span></span> | `OnActionExecuted` |
| <span data-ttu-id="3e19b-218">6</span><span class="sxs-lookup"><span data-stu-id="3e19b-218">6</span></span> | <span data-ttu-id="3e19b-219">Globální</span><span class="sxs-lookup"><span data-stu-id="3e19b-219">Global</span></span> | `OnActionExecuted` |

### <a name="controller-level-filters"></a><span data-ttu-id="3e19b-220">Filtry na úrovni řadiče</span><span class="sxs-lookup"><span data-stu-id="3e19b-220">Controller level filters</span></span>

<span data-ttu-id="3e19b-221">Každý kontroler, který dědí ze <xref:Microsoft.AspNetCore.Mvc.Controller> základní třídy, zahrnuje metody [Controller. OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*), [Controller. OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*)a [Controller. OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*) 
 `OnActionExecuted` .</span><span class="sxs-lookup"><span data-stu-id="3e19b-221">Every controller that inherits from the <xref:Microsoft.AspNetCore.Mvc.Controller> base class includes [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*),  [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*), and [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted` methods.</span></span> <span data-ttu-id="3e19b-222">Tyto metody:</span><span class="sxs-lookup"><span data-stu-id="3e19b-222">These methods:</span></span>

* <span data-ttu-id="3e19b-223">Zabalte filtry, které se spouštějí pro danou akci.</span><span class="sxs-lookup"><span data-stu-id="3e19b-223">Wrap the filters that run for a given action.</span></span>
* <span data-ttu-id="3e19b-224">`OnActionExecuting`je volána před jakýmkoli filtrem akce.</span><span class="sxs-lookup"><span data-stu-id="3e19b-224">`OnActionExecuting` is called before any of the action's filters.</span></span>
* <span data-ttu-id="3e19b-225">`OnActionExecuted`se volá po všech filtrech akcí.</span><span class="sxs-lookup"><span data-stu-id="3e19b-225">`OnActionExecuted` is called after all of the action filters.</span></span>
* <span data-ttu-id="3e19b-226">`OnActionExecutionAsync`je volána před jakýmkoli filtrem akce.</span><span class="sxs-lookup"><span data-stu-id="3e19b-226">`OnActionExecutionAsync` is called before any of the action's filters.</span></span> <span data-ttu-id="3e19b-227">Kód v filtru po `next` spuštění po metodě Action.</span><span class="sxs-lookup"><span data-stu-id="3e19b-227">Code in the filter after `next` runs after the action method.</span></span>

<span data-ttu-id="3e19b-228">Například v ukázce ke stažení `MySampleActionFilter` se použije globálně při spuštění.</span><span class="sxs-lookup"><span data-stu-id="3e19b-228">For example, in the download sample, `MySampleActionFilter` is applied globally in startup.</span></span>

<span data-ttu-id="3e19b-229">`TestController`:</span><span class="sxs-lookup"><span data-stu-id="3e19b-229">The `TestController`:</span></span>

* <span data-ttu-id="3e19b-230">Aplikuje `SampleActionFilterAttribute` ( `[SampleActionFilter]` ) na `FilterTest2` akci.</span><span class="sxs-lookup"><span data-stu-id="3e19b-230">Applies the `SampleActionFilterAttribute` (`[SampleActionFilter]`) to the `FilterTest2` action.</span></span>
* <span data-ttu-id="3e19b-231">Přepisuje `OnActionExecuting` a `OnActionExecuted` .</span><span class="sxs-lookup"><span data-stu-id="3e19b-231">Overrides `OnActionExecuting` and `OnActionExecuted`.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/TestController.cs?name=snippet)]

<!-- test via  webBuilder.UseStartup<Startup>(); -->

<span data-ttu-id="3e19b-232">Navigace pro `https://localhost:5001/Test2/FilterTest2` spustí následující kód:</span><span class="sxs-lookup"><span data-stu-id="3e19b-232">Navigating to `https://localhost:5001/Test2/FilterTest2` runs the following code:</span></span>

* `TestController.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `SampleActionFilterAttribute.OnActionExecuting`
      * `TestController.FilterTest2`
    * `SampleActionFilterAttribute.OnActionExecuted`
  * `MySampleActionFilter.OnActionExecuted`
* `TestController.OnActionExecuted`

<span data-ttu-id="3e19b-233">Filtry na úrovni řadiče nastaví vlastnost [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) na `int.MinValue` .</span><span class="sxs-lookup"><span data-stu-id="3e19b-233">Controller level filters set the [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) property to `int.MinValue`.</span></span> <span data-ttu-id="3e19b-234">Filtry na úrovni řadiče **nelze nastavit** tak, aby běžely po filtrech použitých na metody.</span><span class="sxs-lookup"><span data-stu-id="3e19b-234">Controller level filters can **not** be set to run after filters applied to methods.</span></span> <span data-ttu-id="3e19b-235">Pořadí je vysvětleno v další části.</span><span class="sxs-lookup"><span data-stu-id="3e19b-235">Order is explained in the next section.</span></span>

<span data-ttu-id="3e19b-236">RazorInformace o stránkách naleznete v tématu [implementace Razor filtrů stránek pomocí přepsání metod filtru](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span><span class="sxs-lookup"><span data-stu-id="3e19b-236">For Razor Pages, see [Implement Razor Page filters by overriding filter methods](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span></span>

### <a name="overriding-the-default-order"></a><span data-ttu-id="3e19b-237">Přepsání výchozího pořadí</span><span class="sxs-lookup"><span data-stu-id="3e19b-237">Overriding the default order</span></span>

<span data-ttu-id="3e19b-238">Výchozí sekvenci spuštění lze přepsat implementací <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter> .</span><span class="sxs-lookup"><span data-stu-id="3e19b-238">The default sequence of execution can be overridden by implementing <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span></span> <span data-ttu-id="3e19b-239">`IOrderedFilter`zpřístupňuje <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> vlastnost, která má přednost před rozsahem, aby určila pořadí provádění.</span><span class="sxs-lookup"><span data-stu-id="3e19b-239">`IOrderedFilter` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> property that takes precedence over scope to determine the order of execution.</span></span> <span data-ttu-id="3e19b-240">Filtr s nižší `Order` hodnotou:</span><span class="sxs-lookup"><span data-stu-id="3e19b-240">A filter with a lower `Order` value:</span></span>

* <span data-ttu-id="3e19b-241">Spustí *před* kódem filtr s vyšší hodnotou `Order` .</span><span class="sxs-lookup"><span data-stu-id="3e19b-241">Runs the *before* code before that of a filter with a higher value of `Order`.</span></span>
* <span data-ttu-id="3e19b-242">Spustí *za* kódem za filtr s vyšší `Order` hodnotou.</span><span class="sxs-lookup"><span data-stu-id="3e19b-242">Runs the *after* code after that of a filter with a higher `Order` value.</span></span>

<span data-ttu-id="3e19b-243">`Order`Vlastnost je nastavena s parametrem konstruktoru:</span><span class="sxs-lookup"><span data-stu-id="3e19b-243">The `Order` property is set with a constructor parameter:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test3Controller.cs?name=snippet)]

<span data-ttu-id="3e19b-244">Vezměte v úvahu dva filtry akcí v následujícím kontroleru:</span><span class="sxs-lookup"><span data-stu-id="3e19b-244">Consider the two action filters in the following controller:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test2Controller.cs?name=snippet)]

<span data-ttu-id="3e19b-245">Globální filtr je přidaný v `StartUp.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="3e19b-245">A global filter is added in `StartUp.ConfigureServices`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder.cs?name=snippet)]

<span data-ttu-id="3e19b-246">Tři filtry jsou spouštěny v následujícím pořadí:</span><span class="sxs-lookup"><span data-stu-id="3e19b-246">The 3 filters run in the following order:</span></span>

* `Test2Controller.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `MyAction2FilterAttribute.OnActionExecuting`
      * `Test2Controller.FilterTest2`
    * `MySampleActionFilter.OnActionExecuted`
  * `MyAction2FilterAttribute.OnResultExecuting`
* `Test2Controller.OnActionExecuted`

<span data-ttu-id="3e19b-247">`Order`Vlastnost Přepisuje obor při určování pořadí, ve kterém jsou filtry spouštěny.</span><span class="sxs-lookup"><span data-stu-id="3e19b-247">The `Order` property overrides scope when determining the order in which filters run.</span></span> <span data-ttu-id="3e19b-248">Filtry jsou seřazené podle pořadí, pak se k přerušení vztahů používá obor.</span><span class="sxs-lookup"><span data-stu-id="3e19b-248">Filters are sorted first by order, then scope is used to break ties.</span></span> <span data-ttu-id="3e19b-249">Všechny předdefinované filtry implementují `IOrderedFilter` a nastaví výchozí `Order` hodnotu na 0.</span><span class="sxs-lookup"><span data-stu-id="3e19b-249">All of the built-in filters implement `IOrderedFilter` and set the default `Order` value to 0.</span></span> <span data-ttu-id="3e19b-250">Jak už bylo uvedeno výše, filtry na úrovni řadiče nastaví vlastnost [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) na `int.MinValue` pro předdefinované filtry. Scope určí pořadí, pokud `Order` není nastavená na nenulovou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="3e19b-250">As mentioned previously, controller level filters set the [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) property to `int.MinValue` For built-in filters, scope determines order unless `Order` is set to a non-zero value.</span></span>

<span data-ttu-id="3e19b-251">V předchozím kódu `MySampleActionFilter` má globální rozsah, takže se spustí před `MyAction2FilterAttribute` , který má obor kontroleru.</span><span class="sxs-lookup"><span data-stu-id="3e19b-251">In the preceding code, `MySampleActionFilter` has global scope so it runs before `MyAction2FilterAttribute`, which has controller scope.</span></span> <span data-ttu-id="3e19b-252">Aby bylo možné `MyAction2FilterAttribute` Spustit jako první, nastavte pořadí na `int.MinValue` :</span><span class="sxs-lookup"><span data-stu-id="3e19b-252">To make `MyAction2FilterAttribute` run first, set the order to `int.MinValue`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test2Controller.cs?name=snippet2)]

<span data-ttu-id="3e19b-253">Aby se globální filtr `MySampleActionFilter` spouštěl jako první, nastavte `Order` na `int.MinValue` :</span><span class="sxs-lookup"><span data-stu-id="3e19b-253">To make the global filter `MySampleActionFilter` run first, set `Order` to `int.MinValue`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder2.cs?name=snippet&highlight=6)]

## <a name="cancellation-and-short-circuiting"></a><span data-ttu-id="3e19b-254">Zrušení a zkrácení okruhů</span><span class="sxs-lookup"><span data-stu-id="3e19b-254">Cancellation and short-circuiting</span></span>

<span data-ttu-id="3e19b-255">Kanál filtru může být v krátkém okruhu nastavením <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> vlastnosti pro <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> parametr poskytnutý metodě Filter.</span><span class="sxs-lookup"><span data-stu-id="3e19b-255">The filter pipeline can be short-circuited by setting the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> property on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> parameter provided to the filter method.</span></span> <span data-ttu-id="3e19b-256">Například následující filtr prostředků zabrání ve spuštění zbývajícího kanálu:</span><span class="sxs-lookup"><span data-stu-id="3e19b-256">For instance, the following Resource filter prevents the rest of the pipeline from executing:</span></span>

<a name="short-circuiting-resource-filter"></a>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ShortCircuitingResourceFilterAttribute.cs?name=snippet)]

<span data-ttu-id="3e19b-257">V následujícím kódu, `ShortCircuitingResourceFilter` a jako `AddHeader` cíl filtru i cílovou `SomeResource` metodu Action.</span><span class="sxs-lookup"><span data-stu-id="3e19b-257">In the following code, both the `ShortCircuitingResourceFilter` and the `AddHeader` filter target the `SomeResource` action method.</span></span> <span data-ttu-id="3e19b-258">`ShortCircuitingResourceFilter`:</span><span class="sxs-lookup"><span data-stu-id="3e19b-258">The `ShortCircuitingResourceFilter`:</span></span>

* <span data-ttu-id="3e19b-259">Nejprve se spustí, protože se jedná o filtr prostředků a `AddHeader` je filtr akcí.</span><span class="sxs-lookup"><span data-stu-id="3e19b-259">Runs first, because it's a Resource Filter and `AddHeader` is an Action Filter.</span></span>
* <span data-ttu-id="3e19b-260">Krátké okruhy zbývajícího kanálu.</span><span class="sxs-lookup"><span data-stu-id="3e19b-260">Short-circuits the rest of the pipeline.</span></span>

<span data-ttu-id="3e19b-261">Proto `AddHeader` Filtr pro akci nikdy neběží `SomeResource` .</span><span class="sxs-lookup"><span data-stu-id="3e19b-261">Therefore the `AddHeader` filter never runs for the `SomeResource` action.</span></span> <span data-ttu-id="3e19b-262">Toto chování by bylo stejné, pokud byly oba filtry aplikovány na úrovni metody akce, a to za předpokladu, že byla `ShortCircuitingResourceFilter` spuštěna jako první.</span><span class="sxs-lookup"><span data-stu-id="3e19b-262">This behavior would be the same if both filters were applied at the action method level, provided the `ShortCircuitingResourceFilter` ran first.</span></span> <span data-ttu-id="3e19b-263">`ShortCircuitingResourceFilter`Nejprve se spustí z důvodu jeho typu filtru nebo explicitního použití `Order` Vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="3e19b-263">The `ShortCircuitingResourceFilter` runs first because of its filter type, or by explicit use of `Order` property.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

## <a name="dependency-injection"></a><span data-ttu-id="3e19b-264">Injektáž závislostí</span><span class="sxs-lookup"><span data-stu-id="3e19b-264">Dependency injection</span></span>

<span data-ttu-id="3e19b-265">Filtry lze přidat podle typu nebo podle instance.</span><span class="sxs-lookup"><span data-stu-id="3e19b-265">Filters can be added by type or by instance.</span></span> <span data-ttu-id="3e19b-266">Pokud je přidána instance, bude tato instance použita pro každý požadavek.</span><span class="sxs-lookup"><span data-stu-id="3e19b-266">If an instance is added, that instance is used for every request.</span></span> <span data-ttu-id="3e19b-267">Pokud je přidán typ, je aktivován typ.</span><span class="sxs-lookup"><span data-stu-id="3e19b-267">If a type is added, it's type-activated.</span></span> <span data-ttu-id="3e19b-268">Filtr aktivovaný typu znamená:</span><span class="sxs-lookup"><span data-stu-id="3e19b-268">A type-activated filter means:</span></span>

* <span data-ttu-id="3e19b-269">Instance se vytvoří pro každý požadavek.</span><span class="sxs-lookup"><span data-stu-id="3e19b-269">An instance is created for each request.</span></span>
* <span data-ttu-id="3e19b-270">Jakékoli závislosti konstruktoru jsou vyplněny pomocí [Injektáže závislosti](xref:fundamentals/dependency-injection) (di).</span><span class="sxs-lookup"><span data-stu-id="3e19b-270">Any constructor dependencies are populated by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span>

<span data-ttu-id="3e19b-271">Filtry, které jsou implementovány jako atributy a přidány přímo do tříd kontroleru nebo metody akcí, nemohou mít závislosti konstruktoru poskytované [vkládáním závislostí](xref:fundamentals/dependency-injection) (di).</span><span class="sxs-lookup"><span data-stu-id="3e19b-271">Filters that are implemented as attributes and added directly to controller classes or action methods cannot have constructor dependencies provided by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span> <span data-ttu-id="3e19b-272">Pomocí DI nelze poskytnout závislosti konstruktoru:</span><span class="sxs-lookup"><span data-stu-id="3e19b-272">Constructor dependencies cannot be provided by DI because:</span></span>

* <span data-ttu-id="3e19b-273">V atributech musí být zadány parametry konstruktoru, kde jsou aplikovány.</span><span class="sxs-lookup"><span data-stu-id="3e19b-273">Attributes must have their constructor parameters supplied where they're applied.</span></span> 
* <span data-ttu-id="3e19b-274">Toto je omezení způsobu fungování atributů.</span><span class="sxs-lookup"><span data-stu-id="3e19b-274">This is a limitation of how attributes work.</span></span>

<span data-ttu-id="3e19b-275">Následující filtry podporují závislosti konstruktoru poskytované od DI:</span><span class="sxs-lookup"><span data-stu-id="3e19b-275">The following filters support constructor dependencies provided from DI:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>
* <span data-ttu-id="3e19b-276"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>implementováno u atributu.</span><span class="sxs-lookup"><span data-stu-id="3e19b-276"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implemented on the attribute.</span></span>

<span data-ttu-id="3e19b-277">Předchozí filtry lze použít pro metodu kontroleru nebo akce:</span><span class="sxs-lookup"><span data-stu-id="3e19b-277">The preceding filters can be applied to a controller or action method:</span></span>

<span data-ttu-id="3e19b-278">Protokolovací nástroje jsou k dispozici z DI.</span><span class="sxs-lookup"><span data-stu-id="3e19b-278">Loggers are available from DI.</span></span> <span data-ttu-id="3e19b-279">Ale nevytvářejte a používejte filtry čistě pro účely protokolování.</span><span class="sxs-lookup"><span data-stu-id="3e19b-279">However, avoid creating and using filters purely for logging purposes.</span></span> <span data-ttu-id="3e19b-280">[Integrované protokolování rozhraní](xref:fundamentals/logging/index) obvykle zajišťuje, co je potřeba pro protokolování.</span><span class="sxs-lookup"><span data-stu-id="3e19b-280">The [built-in framework logging](xref:fundamentals/logging/index) typically provides what's needed for logging.</span></span> <span data-ttu-id="3e19b-281">Protokolování přidáno do filtrů:</span><span class="sxs-lookup"><span data-stu-id="3e19b-281">Logging added to filters:</span></span>

* <span data-ttu-id="3e19b-282">Měl by se soustředit na obavy a chování obchodních domén, které jsou specifické pro filtr.</span><span class="sxs-lookup"><span data-stu-id="3e19b-282">Should focus on business domain concerns or behavior specific to the filter.</span></span>
* <span data-ttu-id="3e19b-283">Neměli **byste** protokolovat akce nebo jiné události rozhraní.</span><span class="sxs-lookup"><span data-stu-id="3e19b-283">Should **not** log actions or other framework events.</span></span> <span data-ttu-id="3e19b-284">Vestavěné filtry protokolují akce a události rozhraní.</span><span class="sxs-lookup"><span data-stu-id="3e19b-284">The built-in filters log actions and framework events.</span></span>

### <a name="servicefilterattribute"></a><span data-ttu-id="3e19b-285">ServiceFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="3e19b-285">ServiceFilterAttribute</span></span>

<span data-ttu-id="3e19b-286">Typy implementace filtru služby jsou zaregistrované v `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="3e19b-286">Service filter implementation types are registered in `ConfigureServices`.</span></span> <span data-ttu-id="3e19b-287"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>Načte instanci filtru z di.</span><span class="sxs-lookup"><span data-stu-id="3e19b-287">A <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> retrieves an instance of the filter from DI.</span></span>

<span data-ttu-id="3e19b-288">Následující kód ukazuje `AddHeaderResultServiceFilter` :</span><span class="sxs-lookup"><span data-stu-id="3e19b-288">The following code shows the `AddHeaderResultServiceFilter`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="3e19b-289">V následujícím kódu `AddHeaderResultServiceFilter` je přidán do kontejneru di:</span><span class="sxs-lookup"><span data-stu-id="3e19b-289">In the following code, `AddHeaderResultServiceFilter` is added to the DI container:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Startup.cs?name=snippet&highlight=4)]

<span data-ttu-id="3e19b-290">V následujícím kódu `ServiceFilter` atribut načte instanci `AddHeaderResultServiceFilter` filtru z di:</span><span class="sxs-lookup"><span data-stu-id="3e19b-290">In the following code, the `ServiceFilter` attribute retrieves an instance of the `AddHeaderResultServiceFilter` filter from DI:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_ServiceFilter&highlight=1)]

<span data-ttu-id="3e19b-291">Při použití `ServiceFilterAttribute` nastavte [ServiceFilterAttribute. IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span><span class="sxs-lookup"><span data-stu-id="3e19b-291">When using `ServiceFilterAttribute`, setting [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span></span>

* <span data-ttu-id="3e19b-292">Poskytuje nápovědu, že se instance filtru *dá* znovu použít mimo rozsah požadavku, který byl vytvořen v rámci.</span><span class="sxs-lookup"><span data-stu-id="3e19b-292">Provides a hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="3e19b-293">Modul runtime ASP.NET Core nezaručuje:</span><span class="sxs-lookup"><span data-stu-id="3e19b-293">The ASP.NET Core runtime doesn't guarantee:</span></span>

  * <span data-ttu-id="3e19b-294">Vytvoří se jedna instance filtru.</span><span class="sxs-lookup"><span data-stu-id="3e19b-294">That a single instance of the filter will be created.</span></span>
  * <span data-ttu-id="3e19b-295">Filtr nebude znovu vyžádán z kontejneru DI v pozdějším bodě.</span><span class="sxs-lookup"><span data-stu-id="3e19b-295">The filter will not be re-requested from the DI container at some later point.</span></span>

* <span data-ttu-id="3e19b-296">Neměl by se používat s filtrem, který závisí na službách s jinou životností než singleton.</span><span class="sxs-lookup"><span data-stu-id="3e19b-296">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

 <span data-ttu-id="3e19b-297"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>implementuje <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> .</span><span class="sxs-lookup"><span data-stu-id="3e19b-297"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="3e19b-298">`IFilterFactory`zpřístupňuje <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> metodu pro vytvoření <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span><span class="sxs-lookup"><span data-stu-id="3e19b-298">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="3e19b-299">`CreateInstance`Načte zadaný typ z DI.</span><span class="sxs-lookup"><span data-stu-id="3e19b-299">`CreateInstance` loads the specified type from DI.</span></span>

### <a name="typefilterattribute"></a><span data-ttu-id="3e19b-300">TypeFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="3e19b-300">TypeFilterAttribute</span></span>

<span data-ttu-id="3e19b-301"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>je podobný <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> , ale jeho typ není vyřešen přímo z kontejneru di.</span><span class="sxs-lookup"><span data-stu-id="3e19b-301"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> is similar to <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, but its type isn't resolved directly from the DI container.</span></span> <span data-ttu-id="3e19b-302">Vytvoří instanci typu pomocí <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName> .</span><span class="sxs-lookup"><span data-stu-id="3e19b-302">It instantiates the type by using <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span></span>

<span data-ttu-id="3e19b-303">Protože `TypeFilterAttribute` typy nejsou vyřešeny přímo z kontejneru di:</span><span class="sxs-lookup"><span data-stu-id="3e19b-303">Because `TypeFilterAttribute` types aren't resolved directly from the DI container:</span></span>

* <span data-ttu-id="3e19b-304">Typy, na které se odkazuje pomocí, `TypeFilterAttribute` není nutné registrovat v kontejneru di.</span><span class="sxs-lookup"><span data-stu-id="3e19b-304">Types that are referenced using the `TypeFilterAttribute` don't need to be registered with the DI container.</span></span>  <span data-ttu-id="3e19b-305">Mají své závislosti splněné kontejnerem DI.</span><span class="sxs-lookup"><span data-stu-id="3e19b-305">They do have their dependencies fulfilled by the DI container.</span></span>
* <span data-ttu-id="3e19b-306">`TypeFilterAttribute`může volitelně přijmout argumenty konstruktoru pro typ.</span><span class="sxs-lookup"><span data-stu-id="3e19b-306">`TypeFilterAttribute` can optionally accept constructor arguments for the type.</span></span>

<span data-ttu-id="3e19b-307">Při použití `TypeFilterAttribute` nastavte [TypeFilterAttribute. IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span><span class="sxs-lookup"><span data-stu-id="3e19b-307">When using `TypeFilterAttribute`, setting [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span></span>
* <span data-ttu-id="3e19b-308">Poskytuje nápovědu, že se instance filtru *dá* znovu použít mimo rozsah požadavku, který byl vytvořen v rámci.</span><span class="sxs-lookup"><span data-stu-id="3e19b-308">Provides hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="3e19b-309">Modul runtime ASP.NET Core neposkytuje žádné záruky, že se vytvoří jediná instance filtru.</span><span class="sxs-lookup"><span data-stu-id="3e19b-309">The ASP.NET Core runtime provides no guarantees that a single instance of the filter will be created.</span></span>

* <span data-ttu-id="3e19b-310">Neměl by se používat s filtrem, který závisí na službách s jinou životností než singleton.</span><span class="sxs-lookup"><span data-stu-id="3e19b-310">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

<span data-ttu-id="3e19b-311">Následující příklad ukazuje, jak předat argumenty typu pomocí `TypeFilterAttribute` :</span><span class="sxs-lookup"><span data-stu-id="3e19b-311">The following example shows how to pass arguments to a type using `TypeFilterAttribute`:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_TypeFilter&highlight=1,2)]

<!-- 
https://localhost:5001/home/hi?name=joe
VS debug window shows 
FiltersSample.Filters.LogConstantFilter:Information: Method 'Hi' called
-->

## <a name="authorization-filters"></a><span data-ttu-id="3e19b-312">Filtry autorizace</span><span class="sxs-lookup"><span data-stu-id="3e19b-312">Authorization filters</span></span>

<span data-ttu-id="3e19b-313">Filtry autorizace:</span><span class="sxs-lookup"><span data-stu-id="3e19b-313">Authorization filters:</span></span>

* <span data-ttu-id="3e19b-314">Jsou první filtry spouštěny v kanálu filtru.</span><span class="sxs-lookup"><span data-stu-id="3e19b-314">Are the first filters run in the filter pipeline.</span></span>
* <span data-ttu-id="3e19b-315">Řízení přístupu k metodám akcí.</span><span class="sxs-lookup"><span data-stu-id="3e19b-315">Control access to action methods.</span></span>
* <span data-ttu-id="3e19b-316">Před metodou, ale ne po metodě.</span><span class="sxs-lookup"><span data-stu-id="3e19b-316">Have a before method, but no after method.</span></span>

<span data-ttu-id="3e19b-317">Vlastní autorizační filtry vyžadují vlastní autorizační rozhraní.</span><span class="sxs-lookup"><span data-stu-id="3e19b-317">Custom authorization filters require a custom authorization framework.</span></span> <span data-ttu-id="3e19b-318">Preferovat konfiguraci autorizačních zásad nebo psaní vlastních zásad autorizace při psaní vlastního filtru.</span><span class="sxs-lookup"><span data-stu-id="3e19b-318">Prefer configuring the authorization policies or writing a custom authorization policy over writing a custom filter.</span></span> <span data-ttu-id="3e19b-319">Vestavěný autorizační filtr:</span><span class="sxs-lookup"><span data-stu-id="3e19b-319">The built-in authorization filter:</span></span>

* <span data-ttu-id="3e19b-320">Volá autorizační systém.</span><span class="sxs-lookup"><span data-stu-id="3e19b-320">Calls the authorization system.</span></span>
* <span data-ttu-id="3e19b-321">Neautorizuje požadavky.</span><span class="sxs-lookup"><span data-stu-id="3e19b-321">Does not authorize requests.</span></span>

<span data-ttu-id="3e19b-322">Negenerovat **výjimky** v rámci autorizačních filtrů:</span><span class="sxs-lookup"><span data-stu-id="3e19b-322">Do **not** throw exceptions within authorization filters:</span></span>

* <span data-ttu-id="3e19b-323">Výjimka nebude zpracována.</span><span class="sxs-lookup"><span data-stu-id="3e19b-323">The exception will not be handled.</span></span>
* <span data-ttu-id="3e19b-324">Filtry výjimek nebudou zpracovávat výjimku.</span><span class="sxs-lookup"><span data-stu-id="3e19b-324">Exception filters will not handle the exception.</span></span>

<span data-ttu-id="3e19b-325">Zvažte vydání výzvy, pokud dojde k výjimce ve autorizačním filtru.</span><span class="sxs-lookup"><span data-stu-id="3e19b-325">Consider issuing a challenge when an exception occurs in an authorization filter.</span></span>

<span data-ttu-id="3e19b-326">Přečtěte si další informace o [autorizaci](xref:security/authorization/introduction).</span><span class="sxs-lookup"><span data-stu-id="3e19b-326">Learn more about [Authorization](xref:security/authorization/introduction).</span></span>

## <a name="resource-filters"></a><span data-ttu-id="3e19b-327">Filtry prostředků</span><span class="sxs-lookup"><span data-stu-id="3e19b-327">Resource filters</span></span>

<span data-ttu-id="3e19b-328">Filtry prostředků:</span><span class="sxs-lookup"><span data-stu-id="3e19b-328">Resource filters:</span></span>

* <span data-ttu-id="3e19b-329">Implementujte <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> rozhraní nebo <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> .</span><span class="sxs-lookup"><span data-stu-id="3e19b-329">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> interface.</span></span>
* <span data-ttu-id="3e19b-330">Provádění zabalí většinu kanálu filtru.</span><span class="sxs-lookup"><span data-stu-id="3e19b-330">Execution wraps most of the filter pipeline.</span></span>
* <span data-ttu-id="3e19b-331">Před filtry prostředků se spouštějí jenom [filtry autorizace](#authorization-filters) .</span><span class="sxs-lookup"><span data-stu-id="3e19b-331">Only [Authorization filters](#authorization-filters) run before resource filters.</span></span>

<span data-ttu-id="3e19b-332">Filtry prostředků jsou užitečné pro krátkodobé okruhy v kanálu.</span><span class="sxs-lookup"><span data-stu-id="3e19b-332">Resource filters are useful to short-circuit most of the pipeline.</span></span> <span data-ttu-id="3e19b-333">Například filtr ukládání do mezipaměti může zabránit zbývajícímu kanálu v případě přístupů do mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="3e19b-333">For example, a caching filter can avoid the rest of the pipeline on a cache hit.</span></span>

<span data-ttu-id="3e19b-334">Příklady filtru prostředků:</span><span class="sxs-lookup"><span data-stu-id="3e19b-334">Resource filter examples:</span></span>

* <span data-ttu-id="3e19b-335">[Filtr prostředků se zkrácenými okruhy](#short-circuiting-resource-filter) zobrazenými dříve.</span><span class="sxs-lookup"><span data-stu-id="3e19b-335">[The short-circuiting resource filter](#short-circuiting-resource-filter) shown previously.</span></span>
* <span data-ttu-id="3e19b-336">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span><span class="sxs-lookup"><span data-stu-id="3e19b-336">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span></span>

  * <span data-ttu-id="3e19b-337">Zabraňuje vazbě modelu v přístupu k datům formuláře.</span><span class="sxs-lookup"><span data-stu-id="3e19b-337">Prevents model binding from accessing the form data.</span></span>
  * <span data-ttu-id="3e19b-338">Používá se pro nahrávání velkých souborů, aby se zabránilo čtení dat z formuláře do paměti.</span><span class="sxs-lookup"><span data-stu-id="3e19b-338">Used for large file uploads to prevent the form data from being read into memory.</span></span>

## <a name="action-filters"></a><span data-ttu-id="3e19b-339">Filtry akcí</span><span class="sxs-lookup"><span data-stu-id="3e19b-339">Action filters</span></span>

<span data-ttu-id="3e19b-340">Filtry **akcí se nevztahují** na Razor stránky.</span><span class="sxs-lookup"><span data-stu-id="3e19b-340">Action filters do **not** apply to Razor Pages.</span></span> Razor<span data-ttu-id="3e19b-341">Stránky podporují <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> a <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span><span class="sxs-lookup"><span data-stu-id="3e19b-341"> Pages supports <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span></span> <span data-ttu-id="3e19b-342">Další informace najdete v tématu [metody filtrování pro Razor stránky](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="3e19b-342">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

<span data-ttu-id="3e19b-343">Filtry akcí:</span><span class="sxs-lookup"><span data-stu-id="3e19b-343">Action filters:</span></span>

* <span data-ttu-id="3e19b-344">Implementujte <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> rozhraní nebo <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> .</span><span class="sxs-lookup"><span data-stu-id="3e19b-344">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> interface.</span></span>
* <span data-ttu-id="3e19b-345">Jejich spuštění obklopuje provádění metod akcí.</span><span class="sxs-lookup"><span data-stu-id="3e19b-345">Their execution surrounds the execution of action methods.</span></span>

<span data-ttu-id="3e19b-346">Následující kód ukazuje filtr vzorové akce:</span><span class="sxs-lookup"><span data-stu-id="3e19b-346">The following code shows a sample action filter:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="3e19b-347"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext>Poskytuje následující vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="3e19b-347">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> provides the following properties:</span></span>

* <span data-ttu-id="3e19b-348"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments>– povolí čtení vstupů do metody Action.</span><span class="sxs-lookup"><span data-stu-id="3e19b-348"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> - enables reading the inputs to an action method.</span></span>
* <span data-ttu-id="3e19b-349"><xref:Microsoft.AspNetCore.Mvc.Controller>– povolí manipulaci s instancí kontroleru.</span><span class="sxs-lookup"><span data-stu-id="3e19b-349"><xref:Microsoft.AspNetCore.Mvc.Controller> - enables manipulating the controller instance.</span></span>
* <span data-ttu-id="3e19b-350"><xref:System.Web.Mvc.ActionExecutingContext.Result>– nastavení `Result` krátkodobého provádění metody akce a následných filtrů akcí.</span><span class="sxs-lookup"><span data-stu-id="3e19b-350"><xref:System.Web.Mvc.ActionExecutingContext.Result> - setting `Result` short-circuits execution of the action method and subsequent action filters.</span></span>

<span data-ttu-id="3e19b-351">Vyvolání výjimky v metodě akce:</span><span class="sxs-lookup"><span data-stu-id="3e19b-351">Throwing an exception in an action method:</span></span>

* <span data-ttu-id="3e19b-352">Zabraňuje spuštění dalších filtrů.</span><span class="sxs-lookup"><span data-stu-id="3e19b-352">Prevents running of subsequent filters.</span></span>
* <span data-ttu-id="3e19b-353">Na rozdíl od nastavení `Result` se místo úspěšného výsledku považuje za selhání.</span><span class="sxs-lookup"><span data-stu-id="3e19b-353">Unlike setting `Result`, is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="3e19b-354"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext>Poskytuje `Controller` a `Result` plus následující vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="3e19b-354">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> provides `Controller` and `Result` plus the following properties:</span></span>

* <span data-ttu-id="3e19b-355"><xref:System.Web.Mvc.ActionExecutedContext.Canceled>-True, pokud bylo provedení akce zkráceno jiným filtrem.</span><span class="sxs-lookup"><span data-stu-id="3e19b-355"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> - True if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="3e19b-356"><xref:System.Web.Mvc.ActionExecutedContext.Exception>-Hodnota není null, pokud akce nebo dříve vyvolala filtr akcí.</span><span class="sxs-lookup"><span data-stu-id="3e19b-356"><xref:System.Web.Mvc.ActionExecutedContext.Exception> - Non-null if the action or a previously run action filter threw an exception.</span></span> <span data-ttu-id="3e19b-357">Nastavení této vlastnosti na hodnotu null:</span><span class="sxs-lookup"><span data-stu-id="3e19b-357">Setting this property to null:</span></span>

  * <span data-ttu-id="3e19b-358">Efektivně zpracovává výjimku.</span><span class="sxs-lookup"><span data-stu-id="3e19b-358">Effectively handles the exception.</span></span>
  * <span data-ttu-id="3e19b-359">`Result`je spuštěn, jako kdyby byl vrácen z metody Action.</span><span class="sxs-lookup"><span data-stu-id="3e19b-359">`Result` is executed as if it was returned from the action method.</span></span>

<span data-ttu-id="3e19b-360">V případě `IAsyncActionFilter` volání <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> :</span><span class="sxs-lookup"><span data-stu-id="3e19b-360">For an `IAsyncActionFilter`, a call to the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span></span>

* <span data-ttu-id="3e19b-361">Provede všechny následné filtry akcí a metodu Action.</span><span class="sxs-lookup"><span data-stu-id="3e19b-361">Executes any subsequent action filters and the action method.</span></span>
* <span data-ttu-id="3e19b-362">Vrací objekt `ActionExecutedContext`.</span><span class="sxs-lookup"><span data-stu-id="3e19b-362">Returns `ActionExecutedContext`.</span></span>

<span data-ttu-id="3e19b-363">Do krátkodobého okruhu, přiřaďte <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> k instanci výsledku a Nevolejte `next` (a `ActionExecutionDelegate` ).</span><span class="sxs-lookup"><span data-stu-id="3e19b-363">To short-circuit, assign <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> to a result instance and don't call `next` (the `ActionExecutionDelegate`).</span></span>

<span data-ttu-id="3e19b-364">Rozhraní poskytuje abstrakci <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> , která může být podtříd.</span><span class="sxs-lookup"><span data-stu-id="3e19b-364">The framework provides an abstract <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> that can be subclassed.</span></span>

<span data-ttu-id="3e19b-365">`OnActionExecuting`Filtr akcí lze použít k těmto akcím:</span><span class="sxs-lookup"><span data-stu-id="3e19b-365">The `OnActionExecuting` action filter can be used to:</span></span>

* <span data-ttu-id="3e19b-366">Ověří stav modelu.</span><span class="sxs-lookup"><span data-stu-id="3e19b-366">Validate model state.</span></span>
* <span data-ttu-id="3e19b-367">Pokud je stav neplatný, vrátí se chyba.</span><span class="sxs-lookup"><span data-stu-id="3e19b-367">Return an error if the state is invalid.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet)]

> [!NOTE]
> <span data-ttu-id="3e19b-368">Řadiče s poznámkou s `[ApiController]` atributem automaticky ověří stav modelu a vrátí odpověď 400.</span><span class="sxs-lookup"><span data-stu-id="3e19b-368">Controllers annotated with the `[ApiController]` attribute automatically validate model state and return a 400 response.</span></span> <span data-ttu-id="3e19b-369">Další informace najdete v tématu [Automatické odpovědi HTTP 400](xref:web-api/index#automatic-http-400-responses).</span><span class="sxs-lookup"><span data-stu-id="3e19b-369">For more information, see [Automatic HTTP 400 responses](xref:web-api/index#automatic-http-400-responses).</span></span>

<span data-ttu-id="3e19b-370">`OnActionExecuted`Metoda se spustí za metodou akce:</span><span class="sxs-lookup"><span data-stu-id="3e19b-370">The `OnActionExecuted` method runs after the action method:</span></span>

* <span data-ttu-id="3e19b-371">A mohou zobrazit výsledky akce a manipulovat s nimi prostřednictvím <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> Vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="3e19b-371">And can see and manipulate the results of the action through the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> property.</span></span>
* <span data-ttu-id="3e19b-372"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled>je nastaven na hodnotu true, pokud bylo provedení akce zkráceno jiným filtrem.</span><span class="sxs-lookup"><span data-stu-id="3e19b-372"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> is set to true if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="3e19b-373"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception>je nastavena na hodnotu jinou než null, pokud akce nebo filtr následné akce vyvolaly výjimku.</span><span class="sxs-lookup"><span data-stu-id="3e19b-373"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> is set to a non-null value if the action or a subsequent action filter threw an exception.</span></span> <span data-ttu-id="3e19b-374">Nastavení `Exception` na hodnotu null:</span><span class="sxs-lookup"><span data-stu-id="3e19b-374">Setting `Exception` to null:</span></span>

  * <span data-ttu-id="3e19b-375">Efektivně zpracovává výjimku.</span><span class="sxs-lookup"><span data-stu-id="3e19b-375">Effectively handles an exception.</span></span>
  * <span data-ttu-id="3e19b-376">`ActionExecutedContext.Result`je spuštěn, jako kdyby byl vrácen normálně z metody Action.</span><span class="sxs-lookup"><span data-stu-id="3e19b-376">`ActionExecutedContext.Result` is executed as if it were returned normally from the action method.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet2&higlight=12-99)]

## <a name="exception-filters"></a><span data-ttu-id="3e19b-377">Filtry výjimek</span><span class="sxs-lookup"><span data-stu-id="3e19b-377">Exception filters</span></span>

<span data-ttu-id="3e19b-378">Filtry výjimek:</span><span class="sxs-lookup"><span data-stu-id="3e19b-378">Exception filters:</span></span>

* <span data-ttu-id="3e19b-379">Implementujte <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> nebo <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter> .</span><span class="sxs-lookup"><span data-stu-id="3e19b-379">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span></span>
* <span data-ttu-id="3e19b-380">Dá se použít k implementaci běžných zásad zpracování chyb.</span><span class="sxs-lookup"><span data-stu-id="3e19b-380">Can be used to implement common error handling policies.</span></span>

<span data-ttu-id="3e19b-381">Následující ukázkový filtr výjimek používá vlastní zobrazení chyb pro zobrazení podrobností o výjimkách, ke kterým dochází při vývoji aplikace:</span><span class="sxs-lookup"><span data-stu-id="3e19b-381">The following sample exception filter uses a custom error view to display details about exceptions that occur when the app is in development:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/CustomExceptionFilter.cs?name=snippet_ExceptionFilter&highlight=16-19)]

<span data-ttu-id="3e19b-382">Následující kód testuje filtr výjimky:</span><span class="sxs-lookup"><span data-stu-id="3e19b-382">The following code tests the exception filter:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Controllers/FailingController.cs?name=snippet)]

<span data-ttu-id="3e19b-383">Filtry výjimek:</span><span class="sxs-lookup"><span data-stu-id="3e19b-383">Exception filters:</span></span>

* <span data-ttu-id="3e19b-384">Nemusíte mít události před a po.</span><span class="sxs-lookup"><span data-stu-id="3e19b-384">Don't have before and after events.</span></span>
* <span data-ttu-id="3e19b-385">Implementujte <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> nebo <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*> .</span><span class="sxs-lookup"><span data-stu-id="3e19b-385">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span></span>
* <span data-ttu-id="3e19b-386">Zpracování neošetřených výjimek, ke kterým dochází při Razor vytváření stránky nebo kontroleru, [vázání modelů](xref:mvc/models/model-binding), filtrů akcí nebo metod akcí.</span><span class="sxs-lookup"><span data-stu-id="3e19b-386">Handle unhandled exceptions that occur in Razor Page or controller creation, [model binding](xref:mvc/models/model-binding), action filters, or action methods.</span></span>
* <span data-ttu-id="3e19b-387">**Nezachycujte** výjimky, ke kterým dochází v filtrech prostředků, výsledných filtrech nebo v důsledku provádění výsledků MVC.</span><span class="sxs-lookup"><span data-stu-id="3e19b-387">Do **not** catch exceptions that occur in resource filters, result filters, or MVC result execution.</span></span>

<span data-ttu-id="3e19b-388">Chcete-li zpracovat výjimku, nastavte <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> vlastnost na `true` nebo zapište odpověď.</span><span class="sxs-lookup"><span data-stu-id="3e19b-388">To handle an exception, set the <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> property to `true` or write a response.</span></span> <span data-ttu-id="3e19b-389">Tím se zastaví šíření výjimky.</span><span class="sxs-lookup"><span data-stu-id="3e19b-389">This stops propagation of the exception.</span></span> <span data-ttu-id="3e19b-390">Filtr výjimek nemůže vypnout výjimku na "úspěch".</span><span class="sxs-lookup"><span data-stu-id="3e19b-390">An exception filter can't turn an exception into a "success".</span></span> <span data-ttu-id="3e19b-391">To může provést pouze filtr akcí.</span><span class="sxs-lookup"><span data-stu-id="3e19b-391">Only an action filter can do that.</span></span>

<span data-ttu-id="3e19b-392">Filtry výjimek:</span><span class="sxs-lookup"><span data-stu-id="3e19b-392">Exception filters:</span></span>

* <span data-ttu-id="3e19b-393">Jsou vhodné pro vytváření přesahů výjimek, ke kterým dochází v rámci akcí.</span><span class="sxs-lookup"><span data-stu-id="3e19b-393">Are good for trapping exceptions that occur within actions.</span></span>
* <span data-ttu-id="3e19b-394">Nejsou tak flexibilní jako middleware při zpracování chyb.</span><span class="sxs-lookup"><span data-stu-id="3e19b-394">Are not as flexible as error handling middleware.</span></span>

<span data-ttu-id="3e19b-395">Preferovat middleware pro zpracování výjimek.</span><span class="sxs-lookup"><span data-stu-id="3e19b-395">Prefer middleware for exception handling.</span></span> <span data-ttu-id="3e19b-396">Filtry výjimek použijte pouze v případě, že se zpracování chyb *liší* v závislosti na tom, která metoda Action je volána.</span><span class="sxs-lookup"><span data-stu-id="3e19b-396">Use exception filters only where error handling *differs* based on which action method is called.</span></span> <span data-ttu-id="3e19b-397">Aplikace může například mít metody akcí pro koncové body rozhraní API i pro zobrazení/HTML.</span><span class="sxs-lookup"><span data-stu-id="3e19b-397">For example, an app might have action methods for both API endpoints and for views/HTML.</span></span> <span data-ttu-id="3e19b-398">Koncové body rozhraní API mohou vracet informace o chybě jako JSON, zatímco akce na základě zobrazení by mohly vracet chybovou stránku jako HTML.</span><span class="sxs-lookup"><span data-stu-id="3e19b-398">The API endpoints could return error information as JSON, while the view-based actions could return an error page as HTML.</span></span>

## <a name="result-filters"></a><span data-ttu-id="3e19b-399">Filtry výsledků</span><span class="sxs-lookup"><span data-stu-id="3e19b-399">Result filters</span></span>

<span data-ttu-id="3e19b-400">Filtry výsledků:</span><span class="sxs-lookup"><span data-stu-id="3e19b-400">Result filters:</span></span>

* <span data-ttu-id="3e19b-401">Implementace rozhraní:</span><span class="sxs-lookup"><span data-stu-id="3e19b-401">Implement an interface:</span></span>
  * <span data-ttu-id="3e19b-402"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> nebo <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="3e19b-402"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
  * <span data-ttu-id="3e19b-403"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> nebo <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span><span class="sxs-lookup"><span data-stu-id="3e19b-403"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span></span>
* <span data-ttu-id="3e19b-404">Jejich spuštění obklopuje provádění výsledků akcí.</span><span class="sxs-lookup"><span data-stu-id="3e19b-404">Their execution surrounds the execution of action results.</span></span>

### <a name="iresultfilter-and-iasyncresultfilter"></a><span data-ttu-id="3e19b-405">IResultFilter a IAsyncResultFilter</span><span class="sxs-lookup"><span data-stu-id="3e19b-405">IResultFilter and IAsyncResultFilter</span></span>

<span data-ttu-id="3e19b-406">Následující kód ukazuje filtr výsledků, který přidá hlavičku protokolu HTTP:</span><span class="sxs-lookup"><span data-stu-id="3e19b-406">The following code shows a result filter that adds an HTTP header:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="3e19b-407">Typ výsledku, který se má provést, závisí na akci.</span><span class="sxs-lookup"><span data-stu-id="3e19b-407">The kind of result being executed depends on the action.</span></span> <span data-ttu-id="3e19b-408">Akce vracející zobrazení zahrnuje všechny zpracování Razor jako součást prováděné <xref:Microsoft.AspNetCore.Mvc.ViewResult> .</span><span class="sxs-lookup"><span data-stu-id="3e19b-408">An action returning a view includes all razor processing as part of the <xref:Microsoft.AspNetCore.Mvc.ViewResult> being executed.</span></span> <span data-ttu-id="3e19b-409">Metoda rozhraní API může provést určitou serializaci v rámci provádění výsledku.</span><span class="sxs-lookup"><span data-stu-id="3e19b-409">An API method might perform some serialization as part of the execution of the result.</span></span> <span data-ttu-id="3e19b-410">Přečtěte si další informace o [výsledcích akcí](xref:mvc/controllers/actions).</span><span class="sxs-lookup"><span data-stu-id="3e19b-410">Learn more about [action results](xref:mvc/controllers/actions).</span></span>

<span data-ttu-id="3e19b-411">Filtry výsledků se spustí pouze v případě, že akce nebo filtr akcí vytvoří výsledek akce.</span><span class="sxs-lookup"><span data-stu-id="3e19b-411">Result filters are only executed when an action or action filter produces an action result.</span></span> <span data-ttu-id="3e19b-412">Filtry výsledků nejsou provedeny v těchto případech:</span><span class="sxs-lookup"><span data-stu-id="3e19b-412">Result filters are not executed when:</span></span>

* <span data-ttu-id="3e19b-413">Filtr autorizace nebo filtr prostředků pro krátké okruhy kanálu.</span><span class="sxs-lookup"><span data-stu-id="3e19b-413">An authorization filter or resource filter short-circuits the pipeline.</span></span>
* <span data-ttu-id="3e19b-414">Filtr výjimek zpracovává výjimku tím, že vyprodukuje výsledek akce.</span><span class="sxs-lookup"><span data-stu-id="3e19b-414">An exception filter handles an exception by producing an action result.</span></span>

<span data-ttu-id="3e19b-415"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName>Metoda může zkrátit provádění výsledků akce a následných filtrů výsledků nastavením <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> na `true` .</span><span class="sxs-lookup"><span data-stu-id="3e19b-415">The <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> method can short-circuit execution of the action result and subsequent result filters by setting <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> to `true`.</span></span> <span data-ttu-id="3e19b-416">Zapište do objektu Response v případě krátkého okruhu, abyste se vyhnuli vygenerování prázdné odpovědi.</span><span class="sxs-lookup"><span data-stu-id="3e19b-416">Write to the response object when short-circuiting to avoid generating an empty response.</span></span> <span data-ttu-id="3e19b-417">Vyvolání výjimky v `IResultFilter.OnResultExecuting` :</span><span class="sxs-lookup"><span data-stu-id="3e19b-417">Throwing an exception in `IResultFilter.OnResultExecuting`:</span></span>

* <span data-ttu-id="3e19b-418">Zabraňuje spuštění výsledků akce a následných filtrů.</span><span class="sxs-lookup"><span data-stu-id="3e19b-418">Prevents execution of the action result and subsequent filters.</span></span>
* <span data-ttu-id="3e19b-419">Se považuje za selhání namísto úspěšného výsledku.</span><span class="sxs-lookup"><span data-stu-id="3e19b-419">Is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="3e19b-420">Při <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> spuštění metody je odpověď pravděpodobně již odeslána klientovi.</span><span class="sxs-lookup"><span data-stu-id="3e19b-420">When the <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> method runs, the response has probably already been sent to the client.</span></span> <span data-ttu-id="3e19b-421">Pokud byla odpověď již odeslána klientovi, nelze ji změnit.</span><span class="sxs-lookup"><span data-stu-id="3e19b-421">If the response has already been sent to the client, it cannot be changed.</span></span>

<span data-ttu-id="3e19b-422">`ResultExecutedContext.Canceled`je nastaven na hodnotu, `true` Pokud je spuštění výsledku akce zkráceno jiným filtrem.</span><span class="sxs-lookup"><span data-stu-id="3e19b-422">`ResultExecutedContext.Canceled` is set to `true` if the action result execution was short-circuited by another filter.</span></span>

<span data-ttu-id="3e19b-423">`ResultExecutedContext.Exception`je nastavena na hodnotu jinou než null, pokud výsledek akce nebo následný filtr výsledků vyvolal výjimku.</span><span class="sxs-lookup"><span data-stu-id="3e19b-423">`ResultExecutedContext.Exception` is set to a non-null value if the action result or a subsequent result filter threw an exception.</span></span> <span data-ttu-id="3e19b-424">Nastavení `Exception` na hodnotu null efektivně zpracuje výjimku a zabrání vyvolání výjimky později v kanálu.</span><span class="sxs-lookup"><span data-stu-id="3e19b-424">Setting `Exception` to null effectively handles an exception and prevents the exception from being thrown again later in the pipeline.</span></span> <span data-ttu-id="3e19b-425">Neexistuje žádný spolehlivý způsob, jak zapisovat data do odpovědi při zpracování výjimky ve filtru výsledků.</span><span class="sxs-lookup"><span data-stu-id="3e19b-425">There is no reliable way to write data to a response when handling an exception in a result filter.</span></span> <span data-ttu-id="3e19b-426">Pokud byly hlavičky vyprázdněny do klienta, pokud výsledek akce vyvolá výjimku, neexistuje žádný spolehlivý mechanismus pro odeslání kódu chyby.</span><span class="sxs-lookup"><span data-stu-id="3e19b-426">If the headers have been flushed to the client when an action result throws an exception, there's no reliable mechanism to send a failure code.</span></span>

<span data-ttu-id="3e19b-427">V případě <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter> volání `await next` na se <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> spustí všechny následné filtry výsledků a výsledek akce.</span><span class="sxs-lookup"><span data-stu-id="3e19b-427">For an <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, a call to `await next` on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> executes any subsequent result filters and the action result.</span></span> <span data-ttu-id="3e19b-428">Do krátkodobého okruhu nastavte [ResultExecutingContext. Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) na `true` a Nevolejte na `ResultExecutionDelegate` :</span><span class="sxs-lookup"><span data-stu-id="3e19b-428">To short-circuit, set [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) to `true` and don't call the `ResultExecutionDelegate`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MyAsyncResponseFilter.cs?name=snippet)]

<span data-ttu-id="3e19b-429">Rozhraní poskytuje abstrakci `ResultFilterAttribute` , která může být podtříd.</span><span class="sxs-lookup"><span data-stu-id="3e19b-429">The framework provides an abstract `ResultFilterAttribute` that can be subclassed.</span></span> <span data-ttu-id="3e19b-430">Dříve uvedená třída [AddHeaderAttribute](#add-header-attribute) je příkladem atributu filtru výsledků.</span><span class="sxs-lookup"><span data-stu-id="3e19b-430">The [AddHeaderAttribute](#add-header-attribute) class shown previously is an example of a result filter attribute.</span></span>

### <a name="ialwaysrunresultfilter-and-iasyncalwaysrunresultfilter"></a><span data-ttu-id="3e19b-431">IAlwaysRunResultFilter a IAsyncAlwaysRunResultFilter</span><span class="sxs-lookup"><span data-stu-id="3e19b-431">IAlwaysRunResultFilter and IAsyncAlwaysRunResultFilter</span></span>

<span data-ttu-id="3e19b-432"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter>Rozhraní a <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> deklaruje <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> implementaci, která je spuštěna pro všechny výsledky akce.</span><span class="sxs-lookup"><span data-stu-id="3e19b-432">The <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> interfaces declare an <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> implementation that runs for all action results.</span></span> <span data-ttu-id="3e19b-433">To zahrnuje výsledky akcí, které vytvořil:</span><span class="sxs-lookup"><span data-stu-id="3e19b-433">This includes action results produced by:</span></span>

* <span data-ttu-id="3e19b-434">Filtry autorizace a filtry prostředků, které mají krátký okruh.</span><span class="sxs-lookup"><span data-stu-id="3e19b-434">Authorization filters and resource filters that short-circuit.</span></span>
* <span data-ttu-id="3e19b-435">Filtry výjimek.</span><span class="sxs-lookup"><span data-stu-id="3e19b-435">Exception filters.</span></span>

<span data-ttu-id="3e19b-436">Například následující filtr vždy spustí a nastaví výsledek akce ( <xref:Microsoft.AspNetCore.Mvc.ObjectResult> ) s *422 nezpracovaným* stavovým kódem entity, pokud se nezdaří vyjednávání obsahu:</span><span class="sxs-lookup"><span data-stu-id="3e19b-436">For example, the following filter always runs and sets an action result (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) with a *422 Unprocessable Entity* status code when content negotiation fails:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/UnprocessableResultFilter.cs?name=snippet)]

### <a name="ifilterfactory"></a><span data-ttu-id="3e19b-437">IFilterFactory</span><span class="sxs-lookup"><span data-stu-id="3e19b-437">IFilterFactory</span></span>

<span data-ttu-id="3e19b-438"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>implementuje <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> .</span><span class="sxs-lookup"><span data-stu-id="3e19b-438"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span></span> <span data-ttu-id="3e19b-439">Proto `IFilterFactory` může být instance použita jako `IFilterMetadata` instance kdekoli v kanálu filtru.</span><span class="sxs-lookup"><span data-stu-id="3e19b-439">Therefore, an `IFilterFactory` instance can be used as an `IFilterMetadata` instance anywhere in the filter pipeline.</span></span> <span data-ttu-id="3e19b-440">Když se modul runtime připraví k vyvolání filtru, pokusí se ho přetypovat na `IFilterFactory` .</span><span class="sxs-lookup"><span data-stu-id="3e19b-440">When the runtime prepares to invoke the filter, it attempts to cast it to an `IFilterFactory`.</span></span> <span data-ttu-id="3e19b-441">Pokud je toto přetypování úspěšné, <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> je volána metoda pro vytvoření `IFilterMetadata` instance, která je vyvolána.</span><span class="sxs-lookup"><span data-stu-id="3e19b-441">If that cast succeeds, the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method is called to create the `IFilterMetadata` instance that is invoked.</span></span> <span data-ttu-id="3e19b-442">To poskytuje flexibilní návrh, protože přesný kanál filtru není nutné nastavit explicitně při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="3e19b-442">This provides a flexible design, since the precise filter pipeline doesn't need to be set explicitly when the app starts.</span></span>

<span data-ttu-id="3e19b-443">`IFilterFactory`může být implementováno pomocí implementace vlastního atributu jako jiný přístup k vytváření filtrů:</span><span class="sxs-lookup"><span data-stu-id="3e19b-443">`IFilterFactory` can be implemented using custom attribute implementations as another approach to creating filters:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/AddHeaderWithFactoryAttribute.cs?name=snippet_IFilterFactory&highlight=1,4,5,6,7)]

<span data-ttu-id="3e19b-444">Filtr je použit v následujícím kódu:</span><span class="sxs-lookup"><span data-stu-id="3e19b-444">The filter is applied in the following code:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet3&highlight=21)]

<span data-ttu-id="3e19b-445">Otestujte předchozí kód spuštěním [ukázky stahování](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample):</span><span class="sxs-lookup"><span data-stu-id="3e19b-445">Test the preceding code by running the [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample):</span></span>

* <span data-ttu-id="3e19b-446">Vyvolejte vývojářské nástroje F12.</span><span class="sxs-lookup"><span data-stu-id="3e19b-446">Invoke the F12 developer tools.</span></span>
* <span data-ttu-id="3e19b-447">Přejděte na adresu `https://localhost:5001/Sample/HeaderWithFactory`.</span><span class="sxs-lookup"><span data-stu-id="3e19b-447">Navigate to `https://localhost:5001/Sample/HeaderWithFactory`.</span></span>

<span data-ttu-id="3e19b-448">Vývojářské nástroje F12 zobrazují následující hlavičky odpovědí přidané ukázkovým kódem:</span><span class="sxs-lookup"><span data-stu-id="3e19b-448">The F12 developer tools display the following response headers added by the sample code:</span></span>

* <span data-ttu-id="3e19b-449">**Autor:**`Rick Anderson`</span><span class="sxs-lookup"><span data-stu-id="3e19b-449">**author:** `Rick Anderson`</span></span>
* <span data-ttu-id="3e19b-450">**globaladdheader:**`Result filter added to MvcOptions.Filters`</span><span class="sxs-lookup"><span data-stu-id="3e19b-450">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span></span>
* <span data-ttu-id="3e19b-451">**interní:**`My header`</span><span class="sxs-lookup"><span data-stu-id="3e19b-451">**internal:** `My header`</span></span>

<span data-ttu-id="3e19b-452">Předchozí kód vytvoří hlavičku **interního:** `My header` Response.</span><span class="sxs-lookup"><span data-stu-id="3e19b-452">The preceding code creates the **internal:** `My header` response header.</span></span>

### <a name="ifilterfactory-implemented-on-an-attribute"></a><span data-ttu-id="3e19b-453">IFilterFactory implementované u atributu</span><span class="sxs-lookup"><span data-stu-id="3e19b-453">IFilterFactory implemented on an attribute</span></span>

<!-- Review 
This section needs to be rewritten.
What's a non-named attribute?
-->

<span data-ttu-id="3e19b-454">Filtry, které implementují, `IFilterFactory` jsou užitečné pro filtry, které:</span><span class="sxs-lookup"><span data-stu-id="3e19b-454">Filters that implement `IFilterFactory` are useful for filters that:</span></span>

* <span data-ttu-id="3e19b-455">Nevyžadovat předávání parametrů</span><span class="sxs-lookup"><span data-stu-id="3e19b-455">Don't require passing parameters.</span></span>
* <span data-ttu-id="3e19b-456">Musí mít závislosti konstruktoru, které musí vyplnit DI.</span><span class="sxs-lookup"><span data-stu-id="3e19b-456">Have constructor dependencies that need to be filled by DI.</span></span>

<span data-ttu-id="3e19b-457"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>implementuje <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> .</span><span class="sxs-lookup"><span data-stu-id="3e19b-457"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="3e19b-458">`IFilterFactory`zpřístupňuje <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> metodu pro vytvoření <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span><span class="sxs-lookup"><span data-stu-id="3e19b-458">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="3e19b-459">`CreateInstance`Načte zadaný typ z kontejneru Services (DI).</span><span class="sxs-lookup"><span data-stu-id="3e19b-459">`CreateInstance` loads the specified type from the services container (DI).</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/SampleActionFilterAttribute.cs?name=snippet_TypeFilterAttribute&highlight=1,3,7)]

<span data-ttu-id="3e19b-460">Následující kód ukazuje tři přístupy k použití `[SampleActionFilter]` :</span><span class="sxs-lookup"><span data-stu-id="3e19b-460">The following code shows three approaches to applying the `[SampleActionFilter]`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet&highlight=1)]

<span data-ttu-id="3e19b-461">V předchozím kódu upravení metoda s jako `[SampleActionFilter]` upřednostňovaným přístupem k použití `SampleActionFilter` .</span><span class="sxs-lookup"><span data-stu-id="3e19b-461">In the preceding code, decorating the method with `[SampleActionFilter]` is the preferred approach to applying the `SampleActionFilter`.</span></span>

## <a name="using-middleware-in-the-filter-pipeline"></a><span data-ttu-id="3e19b-462">Použití middlewaru v kanálu filtru</span><span class="sxs-lookup"><span data-stu-id="3e19b-462">Using middleware in the filter pipeline</span></span>

<span data-ttu-id="3e19b-463">Filtry prostředků fungují jako [middleware](xref:fundamentals/middleware/index) v tom, že obklopují provádění všech položek, které jsou později v kanálu.</span><span class="sxs-lookup"><span data-stu-id="3e19b-463">Resource filters work like [middleware](xref:fundamentals/middleware/index) in that they surround the execution of everything that comes later in the pipeline.</span></span> <span data-ttu-id="3e19b-464">Filtry se ale liší od middlewaru v tom, že jsou součástí modulu runtime, což znamená, že mají přístup k kontextu a sestavám.</span><span class="sxs-lookup"><span data-stu-id="3e19b-464">But filters differ from middleware in that they're part of the runtime, which means that they have access to context and constructs.</span></span>

<span data-ttu-id="3e19b-465">Chcete-li použít middleware jako filtr, vytvořte typ s `Configure` metodou, která určuje middleware pro vložení do kanálu filtru.</span><span class="sxs-lookup"><span data-stu-id="3e19b-465">To use middleware as a filter, create a type with a `Configure` method that specifies the middleware to inject into the filter pipeline.</span></span> <span data-ttu-id="3e19b-466">Následující příklad používá middleware Localization k vytvoření aktuální jazykové verze pro požadavek:</span><span class="sxs-lookup"><span data-stu-id="3e19b-466">The following example uses the localization middleware to establish the current culture for a request:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LocalizationPipeline.cs?name=snippet_MiddlewareFilter&highlight=3,22)]

<span data-ttu-id="3e19b-467">Pomocí <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> Spusťte middleware:</span><span class="sxs-lookup"><span data-stu-id="3e19b-467">Use the <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> to run the middleware:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_MiddlewareFilter&highlight=2)]

<span data-ttu-id="3e19b-468">Filtry middlewaru jsou spouštěny ve stejné fázi kanálu filtru jako filtry prostředků, před vazbami modelů a po zbytek kanálu.</span><span class="sxs-lookup"><span data-stu-id="3e19b-468">Middleware filters run at the same stage of the filter pipeline as Resource filters, before model binding and after the rest of the pipeline.</span></span>

## <a name="next-actions"></a><span data-ttu-id="3e19b-469">Další akce</span><span class="sxs-lookup"><span data-stu-id="3e19b-469">Next actions</span></span>

* <span data-ttu-id="3e19b-470">Viz [metody filtru pro Razor stránky](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="3e19b-470">See [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>
* <span data-ttu-id="3e19b-471">Pro experimentování s filtry, [stažení, otestování a úpravy ukázky GitHubu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample).</span><span class="sxs-lookup"><span data-stu-id="3e19b-471">To experiment with filters, [download, test, and modify the GitHub sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="3e19b-472">[Kirka Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Dykstra](https://github.com/tdykstra/)a [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="3e19b-472">By [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="3e19b-473">*Filtry* v ASP.NET Core umožňují spuštění kódu před nebo po určitých fázích v kanálu zpracování požadavků.</span><span class="sxs-lookup"><span data-stu-id="3e19b-473">*Filters* in ASP.NET Core allow code to be run before or after specific stages in the request processing pipeline.</span></span>

<span data-ttu-id="3e19b-474">Předdefinované filtry zpracovávají úlohy, jako například:</span><span class="sxs-lookup"><span data-stu-id="3e19b-474">Built-in filters handle tasks such as:</span></span>

* <span data-ttu-id="3e19b-475">Autorizace (zabránění přístupu k prostředkům, ke kterým uživatel nemá oprávnění).</span><span class="sxs-lookup"><span data-stu-id="3e19b-475">Authorization (preventing access to resources a user isn't authorized for).</span></span>
* <span data-ttu-id="3e19b-476">Ukládání odpovědí do mezipaměti (při krátkém okruhu kanálu požadavků, který vrátí odpověď uloženou v mezipaměti).</span><span class="sxs-lookup"><span data-stu-id="3e19b-476">Response caching (short-circuiting the request pipeline to return a cached response).</span></span>

<span data-ttu-id="3e19b-477">Vlastní filtry je možné vytvořit pro zpracování vzájemně se týkajících se otázek.</span><span class="sxs-lookup"><span data-stu-id="3e19b-477">Custom filters can be created to handle cross-cutting concerns.</span></span> <span data-ttu-id="3e19b-478">Mezi obavy mezi průřezy patří zpracování chyb, ukládání do mezipaměti, konfigurace, autorizace a protokolování.</span><span class="sxs-lookup"><span data-stu-id="3e19b-478">Examples of cross-cutting concerns include error handling, caching, configuration, authorization, and logging.</span></span>  <span data-ttu-id="3e19b-479">Filtry zabraňují duplikování kódu.</span><span class="sxs-lookup"><span data-stu-id="3e19b-479">Filters avoid duplicating code.</span></span> <span data-ttu-id="3e19b-480">Například filtr výjimek zpracování chyb může konsolidovat zpracování chyb.</span><span class="sxs-lookup"><span data-stu-id="3e19b-480">For example, an error handling exception filter could consolidate error handling.</span></span>

<span data-ttu-id="3e19b-481">Tento dokument se týká Razor stránek, řadičů rozhraní API a řadičů se zobrazeními.</span><span class="sxs-lookup"><span data-stu-id="3e19b-481">This document applies to Razor Pages, API controllers, and controllers with views.</span></span>

<span data-ttu-id="3e19b-482">[Zobrazit nebo Stáhnout ukázku](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample) ([Jak stáhnout](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="3e19b-482">[View or download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="how-filters-work"></a><span data-ttu-id="3e19b-483">Jak fungují filtry</span><span class="sxs-lookup"><span data-stu-id="3e19b-483">How filters work</span></span>

<span data-ttu-id="3e19b-484">Filtry se spouštějí v *kanálu vyvolání akce ASP.NET Core*, někdy označované jako *kanál filtru*.</span><span class="sxs-lookup"><span data-stu-id="3e19b-484">Filters run within the *ASP.NET Core action invocation pipeline*, sometimes referred to as the *filter pipeline*.</span></span>  <span data-ttu-id="3e19b-485">Kanál filtru se spustí po ASP.NET Core vybere akci, která se má provést.</span><span class="sxs-lookup"><span data-stu-id="3e19b-485">The filter pipeline runs after ASP.NET Core selects the action to execute.</span></span>

![Požadavek se zpracovává prostřednictvím jiného middlewaru, middleware směrování, výběru akcí a kanálu vyvolání akce ASP.NET Core.](filters/_static/filter-pipeline-1.png)

### <a name="filter-types"></a><span data-ttu-id="3e19b-488">Typy filtrů</span><span class="sxs-lookup"><span data-stu-id="3e19b-488">Filter types</span></span>

<span data-ttu-id="3e19b-489">Každý typ filtru se spustí v jiné fázi kanálu filtru:</span><span class="sxs-lookup"><span data-stu-id="3e19b-489">Each filter type is executed at a different stage in the filter pipeline:</span></span>

* <span data-ttu-id="3e19b-490">[Filtry autorizace](#authorization-filters) se spouštějí jako první a používají se k určení, jestli je uživatel autorizovaný pro požadavek.</span><span class="sxs-lookup"><span data-stu-id="3e19b-490">[Authorization filters](#authorization-filters) run first and are used to determine whether the user is authorized for the request.</span></span> <span data-ttu-id="3e19b-491">Filtry autorizace pro krátkodobé vynechání kanálu, pokud je požadavek neautorizovaný</span><span class="sxs-lookup"><span data-stu-id="3e19b-491">Authorization filters short-circuit the pipeline if the request is unauthorized.</span></span>

* <span data-ttu-id="3e19b-492">[Filtry prostředků](#resource-filters):</span><span class="sxs-lookup"><span data-stu-id="3e19b-492">[Resource filters](#resource-filters):</span></span>

  * <span data-ttu-id="3e19b-493">Spusťte po autorizaci.</span><span class="sxs-lookup"><span data-stu-id="3e19b-493">Run after authorization.</span></span>  
  * <span data-ttu-id="3e19b-494"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*>může spustit kód před zbytek kanálu filtru.</span><span class="sxs-lookup"><span data-stu-id="3e19b-494"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> can run code before the rest of the filter pipeline.</span></span> <span data-ttu-id="3e19b-495">Například `OnResourceExecuting` může spustit kód před vytvořením vazby modelu.</span><span class="sxs-lookup"><span data-stu-id="3e19b-495">For example, `OnResourceExecuting` can run code before model binding.</span></span>
  * <span data-ttu-id="3e19b-496"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*>může spustit kód po dokončení zbývající části kanálu.</span><span class="sxs-lookup"><span data-stu-id="3e19b-496"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> can run code after the rest of the pipeline has completed.</span></span>

* <span data-ttu-id="3e19b-497">[Filtry akcí](#action-filters) mohou spustit kód bezprostředně před a po volání jednotlivé metody akce.</span><span class="sxs-lookup"><span data-stu-id="3e19b-497">[Action filters](#action-filters) can run code immediately before and after an individual action method is called.</span></span> <span data-ttu-id="3e19b-498">Lze je použít k manipulaci s argumenty předaných do akce a výsledek vráceného z akce.</span><span class="sxs-lookup"><span data-stu-id="3e19b-498">They can be used to manipulate the arguments passed into an action and the result returned from the action.</span></span> <span data-ttu-id="3e19b-499">Na stránkách se **nepodporují filtry** akcí Razor .</span><span class="sxs-lookup"><span data-stu-id="3e19b-499">Action filters are **not** supported in Razor Pages.</span></span>

* <span data-ttu-id="3e19b-500">[Filtry výjimek](#exception-filters) slouží k aplikování globálních zásad na neošetřené výjimky, ke kterým dojde před zapsáním cokoli do těla odpovědi.</span><span class="sxs-lookup"><span data-stu-id="3e19b-500">[Exception filters](#exception-filters) are used to apply global policies to unhandled exceptions that occur before anything has been written to the response body.</span></span>

* <span data-ttu-id="3e19b-501">[Filtry výsledků](#result-filters) můžou spustit kód hned před a po provedení jednotlivých výsledků akce.</span><span class="sxs-lookup"><span data-stu-id="3e19b-501">[Result filters](#result-filters) can run code immediately before and after the execution of individual action results.</span></span> <span data-ttu-id="3e19b-502">Spustí se pouze v případě, že metoda akce byla úspěšně provedena.</span><span class="sxs-lookup"><span data-stu-id="3e19b-502">They run only when the action method has executed successfully.</span></span> <span data-ttu-id="3e19b-503">Jsou užitečné pro logiku, která musí obklopit zobrazení nebo formátovací modul.</span><span class="sxs-lookup"><span data-stu-id="3e19b-503">They are useful for logic that must surround view or formatter execution.</span></span>

<span data-ttu-id="3e19b-504">Následující diagram znázorňuje způsob interakce typů filtrů v kanálu filtru.</span><span class="sxs-lookup"><span data-stu-id="3e19b-504">The following diagram shows how filter types interact in the filter pipeline.</span></span>

![Požadavek se zpracovává pomocí autorizačních filtrů, filtrů prostředků, vazeb modelů, filtrů akcí, provádění akcí a konverze výsledků akcí, filtrů výjimek, výsledných filtrů a provádění výsledků.](filters/_static/filter-pipeline-2.png)

## <a name="implementation"></a><span data-ttu-id="3e19b-507">Implementace</span><span class="sxs-lookup"><span data-stu-id="3e19b-507">Implementation</span></span>

<span data-ttu-id="3e19b-508">Filtry podporují synchronní i asynchronní implementace prostřednictvím různých definic rozhraní.</span><span class="sxs-lookup"><span data-stu-id="3e19b-508">Filters support both synchronous and asynchronous implementations through different interface definitions.</span></span>

<span data-ttu-id="3e19b-509">Synchronní filtry mohou spustit kód před ( `On-Stage-Executing` ) a po ( `On-Stage-Executed` ) jejich fáze zřetězení.</span><span class="sxs-lookup"><span data-stu-id="3e19b-509">Synchronous filters can run code before (`On-Stage-Executing`) and after (`On-Stage-Executed`) their pipeline stage.</span></span> <span data-ttu-id="3e19b-510">Například `OnActionExecuting` je volána před voláním metody Action.</span><span class="sxs-lookup"><span data-stu-id="3e19b-510">For example, `OnActionExecuting` is called before the action method is called.</span></span> <span data-ttu-id="3e19b-511">`OnActionExecuted`je volána po návratu metody Action.</span><span class="sxs-lookup"><span data-stu-id="3e19b-511">`OnActionExecuted` is called after the action method returns.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="3e19b-512">Asynchronní filtry definují `On-Stage-ExecutionAsync` metodu:</span><span class="sxs-lookup"><span data-stu-id="3e19b-512">Asynchronous filters define an `On-Stage-ExecutionAsync` method:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/SampleAsyncActionFilter.cs?name=snippet)]

<span data-ttu-id="3e19b-513">V předchozím kódu `SampleAsyncActionFilter` obsahuje znak <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> ( `next` ), který provádí metodu Action.</span><span class="sxs-lookup"><span data-stu-id="3e19b-513">In the preceding code, the `SampleAsyncActionFilter` has an <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`)  that executes the action method.</span></span>  <span data-ttu-id="3e19b-514">Každá z těchto `On-Stage-ExecutionAsync` metod vezme `FilterType-ExecutionDelegate` fázi zřetězení filtru.</span><span class="sxs-lookup"><span data-stu-id="3e19b-514">Each of the `On-Stage-ExecutionAsync` methods take a `FilterType-ExecutionDelegate` that executes the filter's pipeline stage.</span></span>

### <a name="multiple-filter-stages"></a><span data-ttu-id="3e19b-515">Několik fází filtru</span><span class="sxs-lookup"><span data-stu-id="3e19b-515">Multiple filter stages</span></span>

<span data-ttu-id="3e19b-516">Rozhraní pro více fází filtru lze implementovat v rámci jedné třídy.</span><span class="sxs-lookup"><span data-stu-id="3e19b-516">Interfaces for multiple filter stages can be implemented in a single class.</span></span> <span data-ttu-id="3e19b-517">Například <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> Třída implementuje `IActionFilter` , `IResultFilter` a jejich asynchronní ekvivalenty.</span><span class="sxs-lookup"><span data-stu-id="3e19b-517">For example, the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> class implements `IActionFilter`, `IResultFilter`, and their async equivalents.</span></span>

<span data-ttu-id="3e19b-518">Implementujte **buď** synchronní, nebo asynchronní verzi rozhraní filtru, **nikoli** obojí.</span><span class="sxs-lookup"><span data-stu-id="3e19b-518">Implement **either** the synchronous or the async version of a filter interface, **not** both.</span></span> <span data-ttu-id="3e19b-519">Modul runtime nejprve kontroluje, zda filtr implementuje asynchronní rozhraní a v takovém případě jej zavolá.</span><span class="sxs-lookup"><span data-stu-id="3e19b-519">The runtime checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="3e19b-520">V takovém případě volá metody synchronního rozhraní.</span><span class="sxs-lookup"><span data-stu-id="3e19b-520">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="3e19b-521">Pokud jsou asynchronní i synchronní rozhraní implementovány v jedné třídě, je volána pouze asynchronní metoda.</span><span class="sxs-lookup"><span data-stu-id="3e19b-521">If both asynchronous and synchronous interfaces are implemented in one class, only the async method is called.</span></span> <span data-ttu-id="3e19b-522">Při použití abstraktních tříd, jako <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> je například přepsání pouze synchronních metod nebo asynchronní metody pro každý typ filtru.</span><span class="sxs-lookup"><span data-stu-id="3e19b-522">When using abstract classes like <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> override only the synchronous methods or the async method for each filter type.</span></span>

### <a name="built-in-filter-attributes"></a><span data-ttu-id="3e19b-523">Předdefinované atributy filtru</span><span class="sxs-lookup"><span data-stu-id="3e19b-523">Built-in filter attributes</span></span>

<span data-ttu-id="3e19b-524">ASP.NET Core obsahuje integrované filtry založené na atributech, které mohou být roztříděné a přizpůsobené.</span><span class="sxs-lookup"><span data-stu-id="3e19b-524">ASP.NET Core includes built-in attribute-based filters that can be subclassed and customized.</span></span> <span data-ttu-id="3e19b-525">Například následující filtr výsledků přidá hlavičku do odpovědi:</span><span class="sxs-lookup"><span data-stu-id="3e19b-525">For example, the following result filter adds a header to the response:</span></span>

<a name="add-header-attribute"></a>

[!code-csharp[](./filters/sample/FiltersSample/Filters/AddHeaderAttribute.cs?name=snippet)]

<span data-ttu-id="3e19b-526">Atributy umožňují filtrům přijímat argumenty, jak je znázorněno v předchozím příkladu.</span><span class="sxs-lookup"><span data-stu-id="3e19b-526">Attributes allow filters to accept arguments, as shown in the preceding example.</span></span> <span data-ttu-id="3e19b-527">Použijte `AddHeaderAttribute` pro metodu Controller nebo Action a zadejte název a hodnotu HLAVIČKY http:</span><span class="sxs-lookup"><span data-stu-id="3e19b-527">Apply the `AddHeaderAttribute` to a controller or action method and specify the name and value of the HTTP header:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

<!-- `https://localhost:5001/Sample` -->

<span data-ttu-id="3e19b-528">Některé z rozhraní filtru mají odpovídající atributy, které lze použít jako základní třídy pro vlastní implementace.</span><span class="sxs-lookup"><span data-stu-id="3e19b-528">Several of the filter interfaces have corresponding attributes that can be used as base classes for custom implementations.</span></span>

<span data-ttu-id="3e19b-529">Atributy filtru:</span><span class="sxs-lookup"><span data-stu-id="3e19b-529">Filter attributes:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ExceptionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>

## <a name="filter-scopes-and-order-of-execution"></a><span data-ttu-id="3e19b-530">Filtrovat obory a pořadí provádění</span><span class="sxs-lookup"><span data-stu-id="3e19b-530">Filter scopes and order of execution</span></span>

<span data-ttu-id="3e19b-531">Filtr lze přidat do kanálu v jednom ze tří *oborů*:</span><span class="sxs-lookup"><span data-stu-id="3e19b-531">A filter can be added to the pipeline at one of three *scopes*:</span></span>

* <span data-ttu-id="3e19b-532">Použití atributu pro akci.</span><span class="sxs-lookup"><span data-stu-id="3e19b-532">Using an attribute on an action.</span></span>
* <span data-ttu-id="3e19b-533">Použití atributu na řadiči.</span><span class="sxs-lookup"><span data-stu-id="3e19b-533">Using an attribute on a controller.</span></span>
* <span data-ttu-id="3e19b-534">Globálně pro všechny řadiče a akce, jak je znázorněno v následujícím kódu:</span><span class="sxs-lookup"><span data-stu-id="3e19b-534">Globally for all controllers and actions as shown in the following code:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/StartupGF.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="3e19b-535">Předchozí kód přičítá tři filtry globálně pomocí kolekce [MvcOptions. filters](xref:Microsoft.AspNetCore.Mvc.MvcOptions.Filters) .</span><span class="sxs-lookup"><span data-stu-id="3e19b-535">The preceding code adds three filters globally using the [MvcOptions.Filters](xref:Microsoft.AspNetCore.Mvc.MvcOptions.Filters) collection.</span></span>

### <a name="default-order-of-execution"></a><span data-ttu-id="3e19b-536">Výchozí pořadí provádění</span><span class="sxs-lookup"><span data-stu-id="3e19b-536">Default order of execution</span></span>

<span data-ttu-id="3e19b-537">Pokud existuje více filtrů *stejného typu*, určuje obor výchozí pořadí provádění filtru.</span><span class="sxs-lookup"><span data-stu-id="3e19b-537">When there are multiple filters *of the same type*, scope determines the default order of filter execution.</span></span>  <span data-ttu-id="3e19b-538">Globální filtry filtr tříd Surround.</span><span class="sxs-lookup"><span data-stu-id="3e19b-538">Global filters surround class filters.</span></span> <span data-ttu-id="3e19b-539">Filtry tříd obklopují filtry metod Surround.</span><span class="sxs-lookup"><span data-stu-id="3e19b-539">Class filters surround method filters.</span></span>

<span data-ttu-id="3e19b-540">V důsledku vnořování filtru je *po* kódu spuštěn v obráceném pořadí *před* kódem.</span><span class="sxs-lookup"><span data-stu-id="3e19b-540">As a result of filter nesting, the *after* code of filters runs in the reverse order of the *before* code.</span></span> <span data-ttu-id="3e19b-541">Pořadí filtru:</span><span class="sxs-lookup"><span data-stu-id="3e19b-541">The filter sequence:</span></span>

* <span data-ttu-id="3e19b-542">*Před* kódem globálních filtrů.</span><span class="sxs-lookup"><span data-stu-id="3e19b-542">The *before* code of global filters.</span></span>
  * <span data-ttu-id="3e19b-543">*Před* kódem filtru kontroleru.</span><span class="sxs-lookup"><span data-stu-id="3e19b-543">The *before* code of controller filters.</span></span>
    * <span data-ttu-id="3e19b-544">Kód *před* filtry metody Action.</span><span class="sxs-lookup"><span data-stu-id="3e19b-544">The *before* code of action method filters.</span></span>
    * <span data-ttu-id="3e19b-545">*Po* kódu filtry metody Action.</span><span class="sxs-lookup"><span data-stu-id="3e19b-545">The *after* code of action method filters.</span></span>
  * <span data-ttu-id="3e19b-546">*Po* kódu filtry kontroleru.</span><span class="sxs-lookup"><span data-stu-id="3e19b-546">The *after* code of controller filters.</span></span>
* <span data-ttu-id="3e19b-547">*Po* kódu globálních filtrů.</span><span class="sxs-lookup"><span data-stu-id="3e19b-547">The *after* code of global filters.</span></span>
  
<span data-ttu-id="3e19b-548">Následující příklad ilustruje pořadí, ve kterém jsou metody filtru volány pro filtry synchronních akcí.</span><span class="sxs-lookup"><span data-stu-id="3e19b-548">The following example that illustrates the order in which filter methods are called for synchronous action filters.</span></span>

| <span data-ttu-id="3e19b-549">Sequence</span><span class="sxs-lookup"><span data-stu-id="3e19b-549">Sequence</span></span> | <span data-ttu-id="3e19b-550">Rozsah filtru</span><span class="sxs-lookup"><span data-stu-id="3e19b-550">Filter scope</span></span> | <span data-ttu-id="3e19b-551">Filter – metoda</span><span class="sxs-lookup"><span data-stu-id="3e19b-551">Filter method</span></span> |
|:--------:|:------------:|:-------------:|
| <span data-ttu-id="3e19b-552">1</span><span class="sxs-lookup"><span data-stu-id="3e19b-552">1</span></span> | <span data-ttu-id="3e19b-553">Globální</span><span class="sxs-lookup"><span data-stu-id="3e19b-553">Global</span></span> | `OnActionExecuting` |
| <span data-ttu-id="3e19b-554">2</span><span class="sxs-lookup"><span data-stu-id="3e19b-554">2</span></span> | <span data-ttu-id="3e19b-555">Kontrolér</span><span class="sxs-lookup"><span data-stu-id="3e19b-555">Controller</span></span> | `OnActionExecuting` |
| <span data-ttu-id="3e19b-556">3</span><span class="sxs-lookup"><span data-stu-id="3e19b-556">3</span></span> | <span data-ttu-id="3e19b-557">Metoda</span><span class="sxs-lookup"><span data-stu-id="3e19b-557">Method</span></span> | `OnActionExecuting` |
| <span data-ttu-id="3e19b-558">4</span><span class="sxs-lookup"><span data-stu-id="3e19b-558">4</span></span> | <span data-ttu-id="3e19b-559">Metoda</span><span class="sxs-lookup"><span data-stu-id="3e19b-559">Method</span></span> | `OnActionExecuted` |
| <span data-ttu-id="3e19b-560">5</span><span class="sxs-lookup"><span data-stu-id="3e19b-560">5</span></span> | <span data-ttu-id="3e19b-561">Kontrolér</span><span class="sxs-lookup"><span data-stu-id="3e19b-561">Controller</span></span> | `OnActionExecuted` |
| <span data-ttu-id="3e19b-562">6</span><span class="sxs-lookup"><span data-stu-id="3e19b-562">6</span></span> | <span data-ttu-id="3e19b-563">Globální</span><span class="sxs-lookup"><span data-stu-id="3e19b-563">Global</span></span> | `OnActionExecuted` |

<span data-ttu-id="3e19b-564">Tato posloupnost zobrazuje:</span><span class="sxs-lookup"><span data-stu-id="3e19b-564">This sequence shows:</span></span>

* <span data-ttu-id="3e19b-565">Filtr metod je vnořený do filtru kontroleru.</span><span class="sxs-lookup"><span data-stu-id="3e19b-565">The method filter is nested within the controller filter.</span></span>
* <span data-ttu-id="3e19b-566">Filtr kontroleru je vnořený do globálního filtru.</span><span class="sxs-lookup"><span data-stu-id="3e19b-566">The controller filter is nested within the global filter.</span></span>

### <a name="controller-and-razor-page-level-filters"></a><span data-ttu-id="3e19b-567">Filtry na Razor úrovni řadiče a stránky</span><span class="sxs-lookup"><span data-stu-id="3e19b-567">Controller and Razor Page level filters</span></span>

<span data-ttu-id="3e19b-568">Každý kontroler, který dědí ze <xref:Microsoft.AspNetCore.Mvc.Controller> základní třídy, zahrnuje metody [Controller. OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*), [Controller. OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*)a [Controller. OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*) 
 `OnActionExecuted` .</span><span class="sxs-lookup"><span data-stu-id="3e19b-568">Every controller that inherits from the <xref:Microsoft.AspNetCore.Mvc.Controller> base class includes [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*),  [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*), and [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted` methods.</span></span> <span data-ttu-id="3e19b-569">Tyto metody:</span><span class="sxs-lookup"><span data-stu-id="3e19b-569">These methods:</span></span>

* <span data-ttu-id="3e19b-570">Zabalte filtry, které se spouštějí pro danou akci.</span><span class="sxs-lookup"><span data-stu-id="3e19b-570">Wrap the filters that run for a given action.</span></span>
* <span data-ttu-id="3e19b-571">`OnActionExecuting`je volána před jakýmkoli filtrem akce.</span><span class="sxs-lookup"><span data-stu-id="3e19b-571">`OnActionExecuting` is called before any of the action's filters.</span></span>
* <span data-ttu-id="3e19b-572">`OnActionExecuted`se volá po všech filtrech akcí.</span><span class="sxs-lookup"><span data-stu-id="3e19b-572">`OnActionExecuted` is called after all of the action filters.</span></span>
* <span data-ttu-id="3e19b-573">`OnActionExecutionAsync`je volána před jakýmkoli filtrem akce.</span><span class="sxs-lookup"><span data-stu-id="3e19b-573">`OnActionExecutionAsync` is called before any of the action's filters.</span></span> <span data-ttu-id="3e19b-574">Kód v filtru po `next` spuštění po metodě Action.</span><span class="sxs-lookup"><span data-stu-id="3e19b-574">Code in the filter after `next` runs after the action method.</span></span>

<span data-ttu-id="3e19b-575">Například v ukázce ke stažení `MySampleActionFilter` se použije globálně při spuštění.</span><span class="sxs-lookup"><span data-stu-id="3e19b-575">For example, in the download sample, `MySampleActionFilter` is applied globally in startup.</span></span>

<span data-ttu-id="3e19b-576">`TestController`:</span><span class="sxs-lookup"><span data-stu-id="3e19b-576">The `TestController`:</span></span>

* <span data-ttu-id="3e19b-577">Aplikuje `SampleActionFilterAttribute` ( `[SampleActionFilter]` ) na `FilterTest2` akci.</span><span class="sxs-lookup"><span data-stu-id="3e19b-577">Applies the `SampleActionFilterAttribute` (`[SampleActionFilter]`) to the `FilterTest2` action.</span></span>
* <span data-ttu-id="3e19b-578">Přepisuje `OnActionExecuting` a `OnActionExecuted` .</span><span class="sxs-lookup"><span data-stu-id="3e19b-578">Overrides `OnActionExecuting` and `OnActionExecuted`.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/TestController.cs?name=snippet)]

<span data-ttu-id="3e19b-579">Navigace pro `https://localhost:5001/Test/FilterTest2` spustí následující kód:</span><span class="sxs-lookup"><span data-stu-id="3e19b-579">Navigating to `https://localhost:5001/Test/FilterTest2` runs the following code:</span></span>

* `TestController.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `SampleActionFilterAttribute.OnActionExecuting`
      * `TestController.FilterTest2`
    * `SampleActionFilterAttribute.OnActionExecuted`
  * `MySampleActionFilter.OnActionExecuted`
* `TestController.OnActionExecuted`

<span data-ttu-id="3e19b-580">RazorInformace o stránkách naleznete v tématu [implementace Razor filtrů stránek pomocí přepsání metod filtru](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span><span class="sxs-lookup"><span data-stu-id="3e19b-580">For Razor Pages, see [Implement Razor Page filters by overriding filter methods](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span></span>

### <a name="overriding-the-default-order"></a><span data-ttu-id="3e19b-581">Přepsání výchozího pořadí</span><span class="sxs-lookup"><span data-stu-id="3e19b-581">Overriding the default order</span></span>

<span data-ttu-id="3e19b-582">Výchozí sekvenci spuštění lze přepsat implementací <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter> .</span><span class="sxs-lookup"><span data-stu-id="3e19b-582">The default sequence of execution can be overridden by implementing <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span></span> <span data-ttu-id="3e19b-583">`IOrderedFilter`zpřístupňuje <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> vlastnost, která má přednost před rozsahem, aby určila pořadí provádění.</span><span class="sxs-lookup"><span data-stu-id="3e19b-583">`IOrderedFilter` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> property that takes precedence over scope to determine the order of execution.</span></span> <span data-ttu-id="3e19b-584">Filtr s nižší `Order` hodnotou:</span><span class="sxs-lookup"><span data-stu-id="3e19b-584">A filter with a lower `Order` value:</span></span>

* <span data-ttu-id="3e19b-585">Spustí *před* kódem filtr s vyšší hodnotou `Order` .</span><span class="sxs-lookup"><span data-stu-id="3e19b-585">Runs the *before* code before that of a filter with a higher value of `Order`.</span></span>
* <span data-ttu-id="3e19b-586">Spustí *za* kódem za filtr s vyšší `Order` hodnotou.</span><span class="sxs-lookup"><span data-stu-id="3e19b-586">Runs the *after* code after that of a filter with a higher `Order` value.</span></span>

<span data-ttu-id="3e19b-587">`Order`Vlastnost lze nastavit s parametrem konstruktoru:</span><span class="sxs-lookup"><span data-stu-id="3e19b-587">The `Order` property can be set with a constructor parameter:</span></span>

```csharp
[MyFilter(Name = "Controller Level Attribute", Order=1)]
```

<span data-ttu-id="3e19b-588">Vezměte v úvahu stejné 3 filtry akcí, které jsou uvedené v předchozím příkladu.</span><span class="sxs-lookup"><span data-stu-id="3e19b-588">Consider the same 3 action filters shown in the preceding example.</span></span> <span data-ttu-id="3e19b-589">Pokud `Order` je vlastnost Controller a globální filtry nastavená na 1 a 2 v uvedeném pořadí, pořadí spouštění se vrátí zpět.</span><span class="sxs-lookup"><span data-stu-id="3e19b-589">If the `Order` property of the controller and global filters is set to 1 and 2 respectively, the order of execution is reversed.</span></span>

| <span data-ttu-id="3e19b-590">Sequence</span><span class="sxs-lookup"><span data-stu-id="3e19b-590">Sequence</span></span> | <span data-ttu-id="3e19b-591">Rozsah filtru</span><span class="sxs-lookup"><span data-stu-id="3e19b-591">Filter scope</span></span> | <span data-ttu-id="3e19b-592">`Order`majetek</span><span class="sxs-lookup"><span data-stu-id="3e19b-592">`Order` property</span></span> | <span data-ttu-id="3e19b-593">Filter – metoda</span><span class="sxs-lookup"><span data-stu-id="3e19b-593">Filter method</span></span> |
|:--------:|:------------:|:-----------------:|:-------------:|
| <span data-ttu-id="3e19b-594">1</span><span class="sxs-lookup"><span data-stu-id="3e19b-594">1</span></span> | <span data-ttu-id="3e19b-595">Metoda</span><span class="sxs-lookup"><span data-stu-id="3e19b-595">Method</span></span> | <span data-ttu-id="3e19b-596">0</span><span class="sxs-lookup"><span data-stu-id="3e19b-596">0</span></span> | `OnActionExecuting` |
| <span data-ttu-id="3e19b-597">2</span><span class="sxs-lookup"><span data-stu-id="3e19b-597">2</span></span> | <span data-ttu-id="3e19b-598">Kontrolér</span><span class="sxs-lookup"><span data-stu-id="3e19b-598">Controller</span></span> | <span data-ttu-id="3e19b-599">1</span><span class="sxs-lookup"><span data-stu-id="3e19b-599">1</span></span>  | `OnActionExecuting` |
| <span data-ttu-id="3e19b-600">3</span><span class="sxs-lookup"><span data-stu-id="3e19b-600">3</span></span> | <span data-ttu-id="3e19b-601">Globální</span><span class="sxs-lookup"><span data-stu-id="3e19b-601">Global</span></span> | <span data-ttu-id="3e19b-602">2</span><span class="sxs-lookup"><span data-stu-id="3e19b-602">2</span></span>  | `OnActionExecuting` |
| <span data-ttu-id="3e19b-603">4</span><span class="sxs-lookup"><span data-stu-id="3e19b-603">4</span></span> | <span data-ttu-id="3e19b-604">Globální</span><span class="sxs-lookup"><span data-stu-id="3e19b-604">Global</span></span> | <span data-ttu-id="3e19b-605">2</span><span class="sxs-lookup"><span data-stu-id="3e19b-605">2</span></span>  | `OnActionExecuted` |
| <span data-ttu-id="3e19b-606">5</span><span class="sxs-lookup"><span data-stu-id="3e19b-606">5</span></span> | <span data-ttu-id="3e19b-607">Kontrolér</span><span class="sxs-lookup"><span data-stu-id="3e19b-607">Controller</span></span> | <span data-ttu-id="3e19b-608">1</span><span class="sxs-lookup"><span data-stu-id="3e19b-608">1</span></span>  | `OnActionExecuted` |
| <span data-ttu-id="3e19b-609">6</span><span class="sxs-lookup"><span data-stu-id="3e19b-609">6</span></span> | <span data-ttu-id="3e19b-610">Metoda</span><span class="sxs-lookup"><span data-stu-id="3e19b-610">Method</span></span> | <span data-ttu-id="3e19b-611">0</span><span class="sxs-lookup"><span data-stu-id="3e19b-611">0</span></span>  | `OnActionExecuted` |

<span data-ttu-id="3e19b-612">`Order`Vlastnost Přepisuje obor při určování pořadí, ve kterém jsou filtry spouštěny.</span><span class="sxs-lookup"><span data-stu-id="3e19b-612">The `Order` property overrides scope when determining the order in which filters run.</span></span> <span data-ttu-id="3e19b-613">Filtry jsou seřazené podle pořadí, pak se k přerušení vztahů používá obor.</span><span class="sxs-lookup"><span data-stu-id="3e19b-613">Filters are sorted first by order, then scope is used to break ties.</span></span> <span data-ttu-id="3e19b-614">Všechny předdefinované filtry implementují `IOrderedFilter` a nastaví výchozí `Order` hodnotu na 0.</span><span class="sxs-lookup"><span data-stu-id="3e19b-614">All of the built-in filters implement `IOrderedFilter` and set the default `Order` value to 0.</span></span> <span data-ttu-id="3e19b-615">Pro předdefinované filtry rozsah určuje pořadí, pokud `Order` není nastaven na nenulovou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="3e19b-615">For built-in filters, scope determines order unless `Order` is set to a non-zero value.</span></span>

## <a name="cancellation-and-short-circuiting"></a><span data-ttu-id="3e19b-616">Zrušení a zkrácení okruhů</span><span class="sxs-lookup"><span data-stu-id="3e19b-616">Cancellation and short-circuiting</span></span>

<span data-ttu-id="3e19b-617">Kanál filtru může být v krátkém okruhu nastavením <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> vlastnosti pro <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> parametr poskytnutý metodě Filter.</span><span class="sxs-lookup"><span data-stu-id="3e19b-617">The filter pipeline can be short-circuited by setting the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> property on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> parameter provided to the filter method.</span></span> <span data-ttu-id="3e19b-618">Například následující filtr prostředků zabrání ve spuštění zbývajícího kanálu:</span><span class="sxs-lookup"><span data-stu-id="3e19b-618">For instance, the following Resource filter prevents the rest of the pipeline from executing:</span></span>

<a name="short-circuiting-resource-filter"></a>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ShortCircuitingResourceFilterAttribute.cs?name=snippet)]

<span data-ttu-id="3e19b-619">V následujícím kódu, `ShortCircuitingResourceFilter` a jako `AddHeader` cíl filtru i cílovou `SomeResource` metodu Action.</span><span class="sxs-lookup"><span data-stu-id="3e19b-619">In the following code, both the `ShortCircuitingResourceFilter` and the `AddHeader` filter target the `SomeResource` action method.</span></span> <span data-ttu-id="3e19b-620">`ShortCircuitingResourceFilter`:</span><span class="sxs-lookup"><span data-stu-id="3e19b-620">The `ShortCircuitingResourceFilter`:</span></span>

* <span data-ttu-id="3e19b-621">Nejprve se spustí, protože se jedná o filtr prostředků a `AddHeader` je filtr akcí.</span><span class="sxs-lookup"><span data-stu-id="3e19b-621">Runs first, because it's a Resource Filter and `AddHeader` is an Action Filter.</span></span>
* <span data-ttu-id="3e19b-622">Krátké okruhy zbývajícího kanálu.</span><span class="sxs-lookup"><span data-stu-id="3e19b-622">Short-circuits the rest of the pipeline.</span></span>

<span data-ttu-id="3e19b-623">Proto `AddHeader` Filtr pro akci nikdy neběží `SomeResource` .</span><span class="sxs-lookup"><span data-stu-id="3e19b-623">Therefore the `AddHeader` filter never runs for the `SomeResource` action.</span></span> <span data-ttu-id="3e19b-624">Toto chování by bylo stejné, pokud byly oba filtry aplikovány na úrovni metody akce, a to za předpokladu, že byla `ShortCircuitingResourceFilter` spuštěna jako první.</span><span class="sxs-lookup"><span data-stu-id="3e19b-624">This behavior would be the same if both filters were applied at the action method level, provided the `ShortCircuitingResourceFilter` ran first.</span></span> <span data-ttu-id="3e19b-625">`ShortCircuitingResourceFilter`Nejprve se spustí z důvodu jeho typu filtru nebo explicitního použití `Order` Vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="3e19b-625">The `ShortCircuitingResourceFilter` runs first because of its filter type, or by explicit use of `Order` property.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1,9)]

## <a name="dependency-injection"></a><span data-ttu-id="3e19b-626">Injektáž závislostí</span><span class="sxs-lookup"><span data-stu-id="3e19b-626">Dependency injection</span></span>

<span data-ttu-id="3e19b-627">Filtry lze přidat podle typu nebo podle instance.</span><span class="sxs-lookup"><span data-stu-id="3e19b-627">Filters can be added by type or by instance.</span></span> <span data-ttu-id="3e19b-628">Pokud je přidána instance, bude tato instance použita pro každý požadavek.</span><span class="sxs-lookup"><span data-stu-id="3e19b-628">If an instance is added, that instance is used for every request.</span></span> <span data-ttu-id="3e19b-629">Pokud je přidán typ, je aktivován typ.</span><span class="sxs-lookup"><span data-stu-id="3e19b-629">If a type is added, it's type-activated.</span></span> <span data-ttu-id="3e19b-630">Filtr aktivovaný typu znamená:</span><span class="sxs-lookup"><span data-stu-id="3e19b-630">A type-activated filter means:</span></span>

* <span data-ttu-id="3e19b-631">Instance se vytvoří pro každý požadavek.</span><span class="sxs-lookup"><span data-stu-id="3e19b-631">An instance is created for each request.</span></span>
* <span data-ttu-id="3e19b-632">Jakékoli závislosti konstruktoru jsou vyplněny pomocí [Injektáže závislosti](xref:fundamentals/dependency-injection) (di).</span><span class="sxs-lookup"><span data-stu-id="3e19b-632">Any constructor dependencies are populated by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span>

<span data-ttu-id="3e19b-633">Filtry, které jsou implementovány jako atributy a přidány přímo do tříd kontroleru nebo metody akcí, nemohou mít závislosti konstruktoru poskytované [vkládáním závislostí](xref:fundamentals/dependency-injection) (di).</span><span class="sxs-lookup"><span data-stu-id="3e19b-633">Filters that are implemented as attributes and added directly to controller classes or action methods cannot have constructor dependencies provided by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span> <span data-ttu-id="3e19b-634">Pomocí DI nelze poskytnout závislosti konstruktoru:</span><span class="sxs-lookup"><span data-stu-id="3e19b-634">Constructor dependencies cannot be provided by DI because:</span></span>

* <span data-ttu-id="3e19b-635">V atributech musí být zadány parametry konstruktoru, kde jsou aplikovány.</span><span class="sxs-lookup"><span data-stu-id="3e19b-635">Attributes must have their constructor parameters supplied where they're applied.</span></span> 
* <span data-ttu-id="3e19b-636">Toto je omezení způsobu fungování atributů.</span><span class="sxs-lookup"><span data-stu-id="3e19b-636">This is a limitation of how attributes work.</span></span>

<span data-ttu-id="3e19b-637">Následující filtry podporují závislosti konstruktoru poskytované od DI:</span><span class="sxs-lookup"><span data-stu-id="3e19b-637">The following filters support constructor dependencies provided from DI:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>
* <span data-ttu-id="3e19b-638"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>implementováno u atributu.</span><span class="sxs-lookup"><span data-stu-id="3e19b-638"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implemented on the attribute.</span></span>

<span data-ttu-id="3e19b-639">Předchozí filtry lze použít pro metodu kontroleru nebo akce:</span><span class="sxs-lookup"><span data-stu-id="3e19b-639">The preceding filters can be applied to a controller or action method:</span></span>

<span data-ttu-id="3e19b-640">Protokolovací nástroje jsou k dispozici z DI.</span><span class="sxs-lookup"><span data-stu-id="3e19b-640">Loggers are available from DI.</span></span> <span data-ttu-id="3e19b-641">Ale nevytvářejte a používejte filtry čistě pro účely protokolování.</span><span class="sxs-lookup"><span data-stu-id="3e19b-641">However, avoid creating and using filters purely for logging purposes.</span></span> <span data-ttu-id="3e19b-642">[Integrované protokolování rozhraní](xref:fundamentals/logging/index) obvykle zajišťuje, co je potřeba pro protokolování.</span><span class="sxs-lookup"><span data-stu-id="3e19b-642">The [built-in framework logging](xref:fundamentals/logging/index) typically provides what's needed for logging.</span></span> <span data-ttu-id="3e19b-643">Protokolování přidáno do filtrů:</span><span class="sxs-lookup"><span data-stu-id="3e19b-643">Logging added to filters:</span></span>

* <span data-ttu-id="3e19b-644">Měl by se soustředit na obavy a chování obchodních domén, které jsou specifické pro filtr.</span><span class="sxs-lookup"><span data-stu-id="3e19b-644">Should focus on business domain concerns or behavior specific to the filter.</span></span>
* <span data-ttu-id="3e19b-645">Neměli **byste** protokolovat akce nebo jiné události rozhraní.</span><span class="sxs-lookup"><span data-stu-id="3e19b-645">Should **not** log actions or other framework events.</span></span> <span data-ttu-id="3e19b-646">Vestavěné filtry protokolují akce a události rozhraní.</span><span class="sxs-lookup"><span data-stu-id="3e19b-646">The built in filters log actions and framework events.</span></span>

### <a name="servicefilterattribute"></a><span data-ttu-id="3e19b-647">ServiceFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="3e19b-647">ServiceFilterAttribute</span></span>

<span data-ttu-id="3e19b-648">Typy implementace filtru služby jsou zaregistrované v `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="3e19b-648">Service filter implementation types are registered in `ConfigureServices`.</span></span> <span data-ttu-id="3e19b-649"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>Načte instanci filtru z di.</span><span class="sxs-lookup"><span data-stu-id="3e19b-649">A <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> retrieves an instance of the filter from DI.</span></span>

<span data-ttu-id="3e19b-650">Následující kód ukazuje `AddHeaderResultServiceFilter` :</span><span class="sxs-lookup"><span data-stu-id="3e19b-650">The following code shows the `AddHeaderResultServiceFilter`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="3e19b-651">V následujícím kódu `AddHeaderResultServiceFilter` je přidán do kontejneru di:</span><span class="sxs-lookup"><span data-stu-id="3e19b-651">In the following code, `AddHeaderResultServiceFilter` is added to the DI container:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

<span data-ttu-id="3e19b-652">V následujícím kódu `ServiceFilter` atribut načte instanci `AddHeaderResultServiceFilter` filtru z di:</span><span class="sxs-lookup"><span data-stu-id="3e19b-652">In the following code, the `ServiceFilter` attribute retrieves an instance of the `AddHeaderResultServiceFilter` filter from DI:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_ServiceFilter&highlight=1)]

<span data-ttu-id="3e19b-653">Při použití `ServiceFilterAttribute` nastavte [ServiceFilterAttribute. IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span><span class="sxs-lookup"><span data-stu-id="3e19b-653">When using `ServiceFilterAttribute`, setting [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span></span>

* <span data-ttu-id="3e19b-654">Poskytuje nápovědu, že se instance filtru *dá* znovu použít mimo rozsah požadavku, který byl vytvořen v rámci.</span><span class="sxs-lookup"><span data-stu-id="3e19b-654">Provides a hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="3e19b-655">Modul runtime ASP.NET Core nezaručuje:</span><span class="sxs-lookup"><span data-stu-id="3e19b-655">The ASP.NET Core runtime doesn't guarantee:</span></span>

  * <span data-ttu-id="3e19b-656">Vytvoří se jedna instance filtru.</span><span class="sxs-lookup"><span data-stu-id="3e19b-656">That a single instance of the filter will be created.</span></span>
  * <span data-ttu-id="3e19b-657">Filtr nebude znovu vyžádán z kontejneru DI v pozdějším bodě.</span><span class="sxs-lookup"><span data-stu-id="3e19b-657">The filter will not be re-requested from the DI container at some later point.</span></span>

* <span data-ttu-id="3e19b-658">Neměl by se používat s filtrem, který závisí na službách s jinou životností než singleton.</span><span class="sxs-lookup"><span data-stu-id="3e19b-658">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

 <span data-ttu-id="3e19b-659"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>implementuje <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> .</span><span class="sxs-lookup"><span data-stu-id="3e19b-659"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="3e19b-660">`IFilterFactory`zpřístupňuje <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> metodu pro vytvoření <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span><span class="sxs-lookup"><span data-stu-id="3e19b-660">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="3e19b-661">`CreateInstance`Načte zadaný typ z DI.</span><span class="sxs-lookup"><span data-stu-id="3e19b-661">`CreateInstance` loads the specified type from DI.</span></span>

### <a name="typefilterattribute"></a><span data-ttu-id="3e19b-662">TypeFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="3e19b-662">TypeFilterAttribute</span></span>

<span data-ttu-id="3e19b-663"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>je podobný <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> , ale jeho typ není vyřešen přímo z kontejneru di.</span><span class="sxs-lookup"><span data-stu-id="3e19b-663"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> is similar to <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, but its type isn't resolved directly from the DI container.</span></span> <span data-ttu-id="3e19b-664">Vytvoří instanci typu pomocí <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName> .</span><span class="sxs-lookup"><span data-stu-id="3e19b-664">It instantiates the type by using <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span></span>

<span data-ttu-id="3e19b-665">Protože `TypeFilterAttribute` typy nejsou vyřešeny přímo z kontejneru di:</span><span class="sxs-lookup"><span data-stu-id="3e19b-665">Because `TypeFilterAttribute` types aren't resolved directly from the DI container:</span></span>

* <span data-ttu-id="3e19b-666">Typy, na které se odkazuje pomocí, `TypeFilterAttribute` není nutné registrovat v kontejneru di.</span><span class="sxs-lookup"><span data-stu-id="3e19b-666">Types that are referenced using the `TypeFilterAttribute` don't need to be registered with the DI container.</span></span>  <span data-ttu-id="3e19b-667">Mají své závislosti splněné kontejnerem DI.</span><span class="sxs-lookup"><span data-stu-id="3e19b-667">They do have their dependencies fulfilled by the DI container.</span></span>
* <span data-ttu-id="3e19b-668">`TypeFilterAttribute`může volitelně přijmout argumenty konstruktoru pro typ.</span><span class="sxs-lookup"><span data-stu-id="3e19b-668">`TypeFilterAttribute` can optionally accept constructor arguments for the type.</span></span>

<span data-ttu-id="3e19b-669">Při použití `TypeFilterAttribute` nastavte [TypeFilterAttribute. IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span><span class="sxs-lookup"><span data-stu-id="3e19b-669">When using `TypeFilterAttribute`, setting [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span></span>
* <span data-ttu-id="3e19b-670">Poskytuje nápovědu, že se instance filtru *dá* znovu použít mimo rozsah požadavku, který byl vytvořen v rámci.</span><span class="sxs-lookup"><span data-stu-id="3e19b-670">Provides hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="3e19b-671">Modul runtime ASP.NET Core neposkytuje žádné záruky, že se vytvoří jediná instance filtru.</span><span class="sxs-lookup"><span data-stu-id="3e19b-671">The ASP.NET Core runtime provides no guarantees that a single instance of the filter will be created.</span></span>

* <span data-ttu-id="3e19b-672">Neměl by se používat s filtrem, který závisí na službách s jinou životností než singleton.</span><span class="sxs-lookup"><span data-stu-id="3e19b-672">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

<span data-ttu-id="3e19b-673">Následující příklad ukazuje, jak předat argumenty typu pomocí `TypeFilterAttribute` :</span><span class="sxs-lookup"><span data-stu-id="3e19b-673">The following example shows how to pass arguments to a type using `TypeFilterAttribute`:</span></span>

[!code-csharp[](../../mvc/controllers/filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_TypeFilter&highlight=1,2)]
[!code-csharp[](../../mvc/controllers/filters/sample/FiltersSample/Filters/LogConstantFilter.cs?name=snippet_TypeFilter_Implementation&highlight=6)]

<!-- 
https://localhost:5001/home/hi?name=joe
VS debug window shows 
FiltersSample.Filters.LogConstantFilter:Information: Method 'Hi' called
-->

## <a name="authorization-filters"></a><span data-ttu-id="3e19b-674">Filtry autorizace</span><span class="sxs-lookup"><span data-stu-id="3e19b-674">Authorization filters</span></span>

<span data-ttu-id="3e19b-675">Filtry autorizace:</span><span class="sxs-lookup"><span data-stu-id="3e19b-675">Authorization filters:</span></span>

* <span data-ttu-id="3e19b-676">Jsou první filtry spouštěny v kanálu filtru.</span><span class="sxs-lookup"><span data-stu-id="3e19b-676">Are the first filters run in the filter pipeline.</span></span>
* <span data-ttu-id="3e19b-677">Řízení přístupu k metodám akcí.</span><span class="sxs-lookup"><span data-stu-id="3e19b-677">Control access to action methods.</span></span>
* <span data-ttu-id="3e19b-678">Před metodou, ale ne po metodě.</span><span class="sxs-lookup"><span data-stu-id="3e19b-678">Have a before method, but no after method.</span></span>

<span data-ttu-id="3e19b-679">Vlastní autorizační filtry vyžadují vlastní autorizační rozhraní.</span><span class="sxs-lookup"><span data-stu-id="3e19b-679">Custom authorization filters require a custom authorization framework.</span></span> <span data-ttu-id="3e19b-680">Preferovat konfiguraci autorizačních zásad nebo psaní vlastních zásad autorizace při psaní vlastního filtru.</span><span class="sxs-lookup"><span data-stu-id="3e19b-680">Prefer configuring the authorization policies or writing a custom authorization policy over writing a custom filter.</span></span> <span data-ttu-id="3e19b-681">Vestavěný autorizační filtr:</span><span class="sxs-lookup"><span data-stu-id="3e19b-681">The built-in authorization filter:</span></span>

* <span data-ttu-id="3e19b-682">Volá autorizační systém.</span><span class="sxs-lookup"><span data-stu-id="3e19b-682">Calls the authorization system.</span></span>
* <span data-ttu-id="3e19b-683">Neautorizuje požadavky.</span><span class="sxs-lookup"><span data-stu-id="3e19b-683">Does not authorize requests.</span></span>

<span data-ttu-id="3e19b-684">Negenerovat **výjimky** v rámci autorizačních filtrů:</span><span class="sxs-lookup"><span data-stu-id="3e19b-684">Do **not** throw exceptions within authorization filters:</span></span>

* <span data-ttu-id="3e19b-685">Výjimka nebude zpracována.</span><span class="sxs-lookup"><span data-stu-id="3e19b-685">The exception will not be handled.</span></span>
* <span data-ttu-id="3e19b-686">Filtry výjimek nebudou zpracovávat výjimku.</span><span class="sxs-lookup"><span data-stu-id="3e19b-686">Exception filters will not handle the exception.</span></span>

<span data-ttu-id="3e19b-687">Zvažte vydání výzvy, pokud dojde k výjimce ve autorizačním filtru.</span><span class="sxs-lookup"><span data-stu-id="3e19b-687">Consider issuing a challenge when an exception occurs in an authorization filter.</span></span>

<span data-ttu-id="3e19b-688">Přečtěte si další informace o [autorizaci](xref:security/authorization/introduction).</span><span class="sxs-lookup"><span data-stu-id="3e19b-688">Learn more about [Authorization](xref:security/authorization/introduction).</span></span>

## <a name="resource-filters"></a><span data-ttu-id="3e19b-689">Filtry prostředků</span><span class="sxs-lookup"><span data-stu-id="3e19b-689">Resource filters</span></span>

<span data-ttu-id="3e19b-690">Filtry prostředků:</span><span class="sxs-lookup"><span data-stu-id="3e19b-690">Resource filters:</span></span>

* <span data-ttu-id="3e19b-691">Implementujte <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> rozhraní nebo <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> .</span><span class="sxs-lookup"><span data-stu-id="3e19b-691">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> interface.</span></span>
* <span data-ttu-id="3e19b-692">Provádění zabalí většinu kanálu filtru.</span><span class="sxs-lookup"><span data-stu-id="3e19b-692">Execution wraps most of the filter pipeline.</span></span>
* <span data-ttu-id="3e19b-693">Před filtry prostředků se spouštějí jenom [filtry autorizace](#authorization-filters) .</span><span class="sxs-lookup"><span data-stu-id="3e19b-693">Only [Authorization filters](#authorization-filters) run before resource filters.</span></span>

<span data-ttu-id="3e19b-694">Filtry prostředků jsou užitečné pro krátkodobé okruhy v kanálu.</span><span class="sxs-lookup"><span data-stu-id="3e19b-694">Resource filters are useful to short-circuit most of the pipeline.</span></span> <span data-ttu-id="3e19b-695">Například filtr ukládání do mezipaměti může zabránit zbývajícímu kanálu v případě přístupů do mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="3e19b-695">For example, a caching filter can avoid the rest of the pipeline on a cache hit.</span></span>

<span data-ttu-id="3e19b-696">Příklady filtru prostředků:</span><span class="sxs-lookup"><span data-stu-id="3e19b-696">Resource filter examples:</span></span>

* <span data-ttu-id="3e19b-697">[Filtr prostředků se zkrácenými okruhy](#short-circuiting-resource-filter) zobrazenými dříve.</span><span class="sxs-lookup"><span data-stu-id="3e19b-697">[The short-circuiting resource filter](#short-circuiting-resource-filter) shown previously.</span></span>
* <span data-ttu-id="3e19b-698">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span><span class="sxs-lookup"><span data-stu-id="3e19b-698">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span></span>

  * <span data-ttu-id="3e19b-699">Zabraňuje vazbě modelu v přístupu k datům formuláře.</span><span class="sxs-lookup"><span data-stu-id="3e19b-699">Prevents model binding from accessing the form data.</span></span>
  * <span data-ttu-id="3e19b-700">Používá se pro nahrávání velkých souborů, aby se zabránilo čtení dat z formuláře do paměti.</span><span class="sxs-lookup"><span data-stu-id="3e19b-700">Used for large file uploads to prevent the form data from being read into memory.</span></span>

## <a name="action-filters"></a><span data-ttu-id="3e19b-701">Filtry akcí</span><span class="sxs-lookup"><span data-stu-id="3e19b-701">Action filters</span></span>

> [!IMPORTANT]
> <span data-ttu-id="3e19b-702">Filtry **akcí se nevztahují** na Razor stránky.</span><span class="sxs-lookup"><span data-stu-id="3e19b-702">Action filters do **not** apply to Razor Pages.</span></span> Razor<span data-ttu-id="3e19b-703">Stránky podporují <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> a <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span><span class="sxs-lookup"><span data-stu-id="3e19b-703"> Pages supports <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span></span> <span data-ttu-id="3e19b-704">Další informace najdete v tématu [metody filtrování pro Razor stránky](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="3e19b-704">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

<span data-ttu-id="3e19b-705">Filtry akcí:</span><span class="sxs-lookup"><span data-stu-id="3e19b-705">Action filters:</span></span>

* <span data-ttu-id="3e19b-706">Implementujte <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> rozhraní nebo <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> .</span><span class="sxs-lookup"><span data-stu-id="3e19b-706">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> interface.</span></span>
* <span data-ttu-id="3e19b-707">Jejich spuštění obklopuje provádění metod akcí.</span><span class="sxs-lookup"><span data-stu-id="3e19b-707">Their execution surrounds the execution of action methods.</span></span>

<span data-ttu-id="3e19b-708">Následující kód ukazuje filtr vzorové akce:</span><span class="sxs-lookup"><span data-stu-id="3e19b-708">The following code shows a sample action filter:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="3e19b-709"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext>Poskytuje následující vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="3e19b-709">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> provides the following properties:</span></span>

* <span data-ttu-id="3e19b-710"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments>– povolí čtení vstupů do metody akce.</span><span class="sxs-lookup"><span data-stu-id="3e19b-710"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> - enables the inputs to an action method be read.</span></span>
* <span data-ttu-id="3e19b-711"><xref:Microsoft.AspNetCore.Mvc.Controller>– povolí manipulaci s instancí kontroleru.</span><span class="sxs-lookup"><span data-stu-id="3e19b-711"><xref:Microsoft.AspNetCore.Mvc.Controller> - enables manipulating the controller instance.</span></span>
* <span data-ttu-id="3e19b-712"><xref:System.Web.Mvc.ActionExecutingContext.Result>– nastavení `Result` krátkodobého provádění metody akce a následných filtrů akcí.</span><span class="sxs-lookup"><span data-stu-id="3e19b-712"><xref:System.Web.Mvc.ActionExecutingContext.Result> - setting `Result` short-circuits execution of the action method and subsequent action filters.</span></span>

<span data-ttu-id="3e19b-713">Vyvolání výjimky v metodě akce:</span><span class="sxs-lookup"><span data-stu-id="3e19b-713">Throwing an exception in an action method:</span></span>

* <span data-ttu-id="3e19b-714">Zabraňuje spuštění dalších filtrů.</span><span class="sxs-lookup"><span data-stu-id="3e19b-714">Prevents running of subsequent filters.</span></span>
* <span data-ttu-id="3e19b-715">Na rozdíl od nastavení `Result` se místo úspěšného výsledku považuje za selhání.</span><span class="sxs-lookup"><span data-stu-id="3e19b-715">Unlike setting `Result`, is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="3e19b-716"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext>Poskytuje `Controller` a `Result` plus následující vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="3e19b-716">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> provides `Controller` and `Result` plus the following properties:</span></span>

* <span data-ttu-id="3e19b-717"><xref:System.Web.Mvc.ActionExecutedContext.Canceled>-True, pokud bylo provedení akce zkráceno jiným filtrem.</span><span class="sxs-lookup"><span data-stu-id="3e19b-717"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> - True if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="3e19b-718"><xref:System.Web.Mvc.ActionExecutedContext.Exception>-Hodnota není null, pokud akce nebo dříve vyvolala filtr akcí.</span><span class="sxs-lookup"><span data-stu-id="3e19b-718"><xref:System.Web.Mvc.ActionExecutedContext.Exception> - Non-null if the action or a previously run action filter threw an exception.</span></span> <span data-ttu-id="3e19b-719">Nastavení této vlastnosti na hodnotu null:</span><span class="sxs-lookup"><span data-stu-id="3e19b-719">Setting this property to null:</span></span>

  * <span data-ttu-id="3e19b-720">Efektivně zpracovává výjimku.</span><span class="sxs-lookup"><span data-stu-id="3e19b-720">Effectively handles the exception.</span></span>
  * <span data-ttu-id="3e19b-721">`Result`je spuštěn, jako kdyby byl vrácen z metody Action.</span><span class="sxs-lookup"><span data-stu-id="3e19b-721">`Result` is executed as if it was returned from the action method.</span></span>

<span data-ttu-id="3e19b-722">V případě `IAsyncActionFilter` volání <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> :</span><span class="sxs-lookup"><span data-stu-id="3e19b-722">For an `IAsyncActionFilter`, a call to the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span></span>

* <span data-ttu-id="3e19b-723">Provede všechny následné filtry akcí a metodu Action.</span><span class="sxs-lookup"><span data-stu-id="3e19b-723">Executes any subsequent action filters and the action method.</span></span>
* <span data-ttu-id="3e19b-724">Vrací objekt `ActionExecutedContext`.</span><span class="sxs-lookup"><span data-stu-id="3e19b-724">Returns `ActionExecutedContext`.</span></span>

<span data-ttu-id="3e19b-725">Do krátkodobého okruhu, přiřaďte <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> k instanci výsledku a Nevolejte `next` (a `ActionExecutionDelegate` ).</span><span class="sxs-lookup"><span data-stu-id="3e19b-725">To short-circuit, assign <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> to a result instance and don't call `next` (the `ActionExecutionDelegate`).</span></span>

<span data-ttu-id="3e19b-726">Rozhraní poskytuje abstrakci <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> , která může být podtříd.</span><span class="sxs-lookup"><span data-stu-id="3e19b-726">The framework provides an abstract <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> that can be subclassed.</span></span>

<span data-ttu-id="3e19b-727">`OnActionExecuting`Filtr akcí lze použít k těmto akcím:</span><span class="sxs-lookup"><span data-stu-id="3e19b-727">The `OnActionExecuting` action filter can be used to:</span></span>

* <span data-ttu-id="3e19b-728">Ověří stav modelu.</span><span class="sxs-lookup"><span data-stu-id="3e19b-728">Validate model state.</span></span>
* <span data-ttu-id="3e19b-729">Pokud je stav neplatný, vrátí se chyba.</span><span class="sxs-lookup"><span data-stu-id="3e19b-729">Return an error if the state is invalid.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet)]

<span data-ttu-id="3e19b-730">`OnActionExecuted`Metoda se spustí za metodou akce:</span><span class="sxs-lookup"><span data-stu-id="3e19b-730">The `OnActionExecuted` method runs after the action method:</span></span>

* <span data-ttu-id="3e19b-731">A mohou zobrazit výsledky akce a manipulovat s nimi prostřednictvím <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> Vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="3e19b-731">And can see and manipulate the results of the action through the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> property.</span></span>
* <span data-ttu-id="3e19b-732"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled>je nastaven na hodnotu true, pokud bylo provedení akce zkráceno jiným filtrem.</span><span class="sxs-lookup"><span data-stu-id="3e19b-732"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> is set to true if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="3e19b-733"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception>je nastavena na hodnotu jinou než null, pokud akce nebo filtr následné akce vyvolaly výjimku.</span><span class="sxs-lookup"><span data-stu-id="3e19b-733"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> is set to a non-null value if the action or a subsequent action filter threw an exception.</span></span> <span data-ttu-id="3e19b-734">Nastavení `Exception` na hodnotu null:</span><span class="sxs-lookup"><span data-stu-id="3e19b-734">Setting `Exception` to null:</span></span>

  * <span data-ttu-id="3e19b-735">Efektivně zpracovává výjimku.</span><span class="sxs-lookup"><span data-stu-id="3e19b-735">Effectively handles an exception.</span></span>
  * <span data-ttu-id="3e19b-736">`ActionExecutedContext.Result`je spuštěn, jako kdyby byl vrácen normálně z metody Action.</span><span class="sxs-lookup"><span data-stu-id="3e19b-736">`ActionExecutedContext.Result` is executed as if it were returned normally from the action method.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet2&higlight=12-99)]

## <a name="exception-filters"></a><span data-ttu-id="3e19b-737">Filtry výjimek</span><span class="sxs-lookup"><span data-stu-id="3e19b-737">Exception filters</span></span>

<span data-ttu-id="3e19b-738">Filtry výjimek:</span><span class="sxs-lookup"><span data-stu-id="3e19b-738">Exception filters:</span></span>

* <span data-ttu-id="3e19b-739">Implementujte <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> nebo <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter> .</span><span class="sxs-lookup"><span data-stu-id="3e19b-739">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span></span> 
* <span data-ttu-id="3e19b-740">Dá se použít k implementaci běžných zásad zpracování chyb.</span><span class="sxs-lookup"><span data-stu-id="3e19b-740">Can be used to implement common error handling policies.</span></span>

<span data-ttu-id="3e19b-741">Následující ukázkový filtr výjimek používá vlastní zobrazení chyb pro zobrazení podrobností o výjimkách, ke kterým dochází při vývoji aplikace:</span><span class="sxs-lookup"><span data-stu-id="3e19b-741">The following sample exception filter uses a custom error view to display details about exceptions that occur when the app is in development:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/CustomExceptionFilter.cs?name=snippet_ExceptionFilter&highlight=16-19)]

<span data-ttu-id="3e19b-742">Filtry výjimek:</span><span class="sxs-lookup"><span data-stu-id="3e19b-742">Exception filters:</span></span>

* <span data-ttu-id="3e19b-743">Nemusíte mít události před a po.</span><span class="sxs-lookup"><span data-stu-id="3e19b-743">Don't have before and after events.</span></span>
* <span data-ttu-id="3e19b-744">Implementujte <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> nebo <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*> .</span><span class="sxs-lookup"><span data-stu-id="3e19b-744">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span></span>
* <span data-ttu-id="3e19b-745">Zpracování neošetřených výjimek, ke kterým dochází při Razor vytváření stránky nebo kontroleru, [vázání modelů](xref:mvc/models/model-binding), filtrů akcí nebo metod akcí.</span><span class="sxs-lookup"><span data-stu-id="3e19b-745">Handle unhandled exceptions that occur in Razor Page or controller creation, [model binding](xref:mvc/models/model-binding), action filters, or action methods.</span></span>
* <span data-ttu-id="3e19b-746">**Nezachycujte** výjimky, ke kterým dochází v filtrech prostředků, výsledných filtrech nebo v důsledku provádění výsledků MVC.</span><span class="sxs-lookup"><span data-stu-id="3e19b-746">Do **not** catch exceptions that occur in resource filters, result filters, or MVC result execution.</span></span>

<span data-ttu-id="3e19b-747">Chcete-li zpracovat výjimku, nastavte <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> vlastnost na `true` nebo zapište odpověď.</span><span class="sxs-lookup"><span data-stu-id="3e19b-747">To handle an exception, set the <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> property to `true` or write a response.</span></span> <span data-ttu-id="3e19b-748">Tím se zastaví šíření výjimky.</span><span class="sxs-lookup"><span data-stu-id="3e19b-748">This stops propagation of the exception.</span></span> <span data-ttu-id="3e19b-749">Filtr výjimek nemůže vypnout výjimku na "úspěch".</span><span class="sxs-lookup"><span data-stu-id="3e19b-749">An exception filter can't turn an exception into a "success".</span></span> <span data-ttu-id="3e19b-750">To může provést pouze filtr akcí.</span><span class="sxs-lookup"><span data-stu-id="3e19b-750">Only an action filter can do that.</span></span>

<span data-ttu-id="3e19b-751">Filtry výjimek:</span><span class="sxs-lookup"><span data-stu-id="3e19b-751">Exception filters:</span></span>

* <span data-ttu-id="3e19b-752">Jsou vhodné pro vytváření přesahů výjimek, ke kterým dochází v rámci akcí.</span><span class="sxs-lookup"><span data-stu-id="3e19b-752">Are good for trapping exceptions that occur within actions.</span></span>
* <span data-ttu-id="3e19b-753">Nejsou tak flexibilní jako middleware při zpracování chyb.</span><span class="sxs-lookup"><span data-stu-id="3e19b-753">Are not as flexible as error handling middleware.</span></span>

<span data-ttu-id="3e19b-754">Preferovat middleware pro zpracování výjimek.</span><span class="sxs-lookup"><span data-stu-id="3e19b-754">Prefer middleware for exception handling.</span></span> <span data-ttu-id="3e19b-755">Filtry výjimek použijte pouze v případě, že se zpracování chyb *liší* v závislosti na tom, která metoda Action je volána.</span><span class="sxs-lookup"><span data-stu-id="3e19b-755">Use exception filters only where error handling *differs* based on which action method is called.</span></span> <span data-ttu-id="3e19b-756">Aplikace může například mít metody akcí pro koncové body rozhraní API i pro zobrazení/HTML.</span><span class="sxs-lookup"><span data-stu-id="3e19b-756">For example, an app might have action methods for both API endpoints and for views/HTML.</span></span> <span data-ttu-id="3e19b-757">Koncové body rozhraní API mohou vracet informace o chybě jako JSON, zatímco akce na základě zobrazení by mohly vracet chybovou stránku jako HTML.</span><span class="sxs-lookup"><span data-stu-id="3e19b-757">The API endpoints could return error information as JSON, while the view-based actions could return an error page as HTML.</span></span>

## <a name="result-filters"></a><span data-ttu-id="3e19b-758">Filtry výsledků</span><span class="sxs-lookup"><span data-stu-id="3e19b-758">Result filters</span></span>

<span data-ttu-id="3e19b-759">Filtry výsledků:</span><span class="sxs-lookup"><span data-stu-id="3e19b-759">Result filters:</span></span>

* <span data-ttu-id="3e19b-760">Implementace rozhraní:</span><span class="sxs-lookup"><span data-stu-id="3e19b-760">Implement an interface:</span></span>
  * <span data-ttu-id="3e19b-761"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> nebo <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="3e19b-761"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
  * <span data-ttu-id="3e19b-762"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> nebo <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span><span class="sxs-lookup"><span data-stu-id="3e19b-762"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span></span>
* <span data-ttu-id="3e19b-763">Jejich spuštění obklopuje provádění výsledků akcí.</span><span class="sxs-lookup"><span data-stu-id="3e19b-763">Their execution surrounds the execution of action results.</span></span>

### <a name="iresultfilter-and-iasyncresultfilter"></a><span data-ttu-id="3e19b-764">IResultFilter a IAsyncResultFilter</span><span class="sxs-lookup"><span data-stu-id="3e19b-764">IResultFilter and IAsyncResultFilter</span></span>

<span data-ttu-id="3e19b-765">Následující kód ukazuje filtr výsledků, který přidá hlavičku protokolu HTTP:</span><span class="sxs-lookup"><span data-stu-id="3e19b-765">The following code shows a result filter that adds an HTTP header:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="3e19b-766">Typ výsledku, který se má provést, závisí na akci.</span><span class="sxs-lookup"><span data-stu-id="3e19b-766">The kind of result being executed depends on the action.</span></span> <span data-ttu-id="3e19b-767">Akce vracející zobrazení zahrnuje všechny zpracování Razor jako součást prováděné <xref:Microsoft.AspNetCore.Mvc.ViewResult> .</span><span class="sxs-lookup"><span data-stu-id="3e19b-767">An action returning a view would include all razor processing as part of the <xref:Microsoft.AspNetCore.Mvc.ViewResult> being executed.</span></span> <span data-ttu-id="3e19b-768">Metoda rozhraní API může provést určitou serializaci v rámci provádění výsledku.</span><span class="sxs-lookup"><span data-stu-id="3e19b-768">An API method might perform some serialization as part of the execution of the result.</span></span> <span data-ttu-id="3e19b-769">Přečtěte si další informace o [výsledcích akcí](xref:mvc/controllers/actions).</span><span class="sxs-lookup"><span data-stu-id="3e19b-769">Learn more about [action results](xref:mvc/controllers/actions).</span></span>

<span data-ttu-id="3e19b-770">Filtry výsledků se spustí pouze v případě, že akce nebo filtr akcí vytvoří výsledek akce.</span><span class="sxs-lookup"><span data-stu-id="3e19b-770">Result filters are only executed when an action or action filter produces an action result.</span></span> <span data-ttu-id="3e19b-771">Filtry výsledků nejsou provedeny v těchto případech:</span><span class="sxs-lookup"><span data-stu-id="3e19b-771">Result filters are not executed when:</span></span>

* <span data-ttu-id="3e19b-772">Filtr autorizace nebo filtr prostředků pro krátké okruhy kanálu.</span><span class="sxs-lookup"><span data-stu-id="3e19b-772">An authorization filter or resource filter short-circuits the pipeline.</span></span>
* <span data-ttu-id="3e19b-773">Filtr výjimek zpracovává výjimku tím, že vyprodukuje výsledek akce.</span><span class="sxs-lookup"><span data-stu-id="3e19b-773">An exception filter handles an exception by producing an action result.</span></span>

<span data-ttu-id="3e19b-774"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName>Metoda může zkrátit provádění výsledků akce a následných filtrů výsledků nastavením <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> na `true` .</span><span class="sxs-lookup"><span data-stu-id="3e19b-774">The <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> method can short-circuit execution of the action result and subsequent result filters by setting <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> to `true`.</span></span> <span data-ttu-id="3e19b-775">Zapište do objektu Response v případě krátkého okruhu, abyste se vyhnuli vygenerování prázdné odpovědi.</span><span class="sxs-lookup"><span data-stu-id="3e19b-775">Write to the response object when short-circuiting to avoid generating an empty response.</span></span> <span data-ttu-id="3e19b-776">Vyvolání výjimky v `IResultFilter.OnResultExecuting` bude:</span><span class="sxs-lookup"><span data-stu-id="3e19b-776">Throwing an exception in `IResultFilter.OnResultExecuting` will:</span></span>

* <span data-ttu-id="3e19b-777">Zabraňte provádění výsledků akce a dalších filtrů.</span><span class="sxs-lookup"><span data-stu-id="3e19b-777">Prevent execution of the action result and subsequent filters.</span></span>
* <span data-ttu-id="3e19b-778">Být považována za selhání namísto úspěšného výsledku.</span><span class="sxs-lookup"><span data-stu-id="3e19b-778">Be treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="3e19b-779">Při <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> spuštění metody je odpověď pravděpodobně již odeslána klientovi.</span><span class="sxs-lookup"><span data-stu-id="3e19b-779">When the <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> method runs, the response has likely already been sent to the client.</span></span> <span data-ttu-id="3e19b-780">Pokud byla odpověď již odeslána klientovi, nelze ji dále změnit.</span><span class="sxs-lookup"><span data-stu-id="3e19b-780">If the response has already been sent to the client, it cannot be changed further.</span></span>

<span data-ttu-id="3e19b-781">`ResultExecutedContext.Canceled`je nastaven na hodnotu, `true` Pokud je spuštění výsledku akce zkráceno jiným filtrem.</span><span class="sxs-lookup"><span data-stu-id="3e19b-781">`ResultExecutedContext.Canceled` is set to `true` if the action result execution was short-circuited by another filter.</span></span>

<span data-ttu-id="3e19b-782">`ResultExecutedContext.Exception`je nastavena na hodnotu jinou než null, pokud výsledek akce nebo následný filtr výsledků vyvolal výjimku.</span><span class="sxs-lookup"><span data-stu-id="3e19b-782">`ResultExecutedContext.Exception` is set to a non-null value if the action result or a subsequent result filter threw an exception.</span></span> <span data-ttu-id="3e19b-783">Nastavení `Exception` na hodnotu null efektivně zpracovává výjimku a brání výjimce znovu vyvolat ASP.NET Core později v kanálu.</span><span class="sxs-lookup"><span data-stu-id="3e19b-783">Setting `Exception` to null effectively handles an exception and prevents the exception from being rethrown by ASP.NET Core later in the pipeline.</span></span> <span data-ttu-id="3e19b-784">Neexistuje žádný spolehlivý způsob, jak zapisovat data do odpovědi při zpracování výjimky ve filtru výsledků.</span><span class="sxs-lookup"><span data-stu-id="3e19b-784">There is no reliable way to write data to a response when handling an exception in a result filter.</span></span> <span data-ttu-id="3e19b-785">Pokud byly hlavičky vyprázdněny do klienta, pokud výsledek akce vyvolá výjimku, neexistuje žádný spolehlivý mechanismus pro odeslání kódu chyby.</span><span class="sxs-lookup"><span data-stu-id="3e19b-785">If the headers have been flushed to the client when an action result throws an exception, there's no reliable mechanism to send a failure code.</span></span>

<span data-ttu-id="3e19b-786">V případě <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter> volání `await next` na se <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> spustí všechny následné filtry výsledků a výsledek akce.</span><span class="sxs-lookup"><span data-stu-id="3e19b-786">For an <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, a call to `await next` on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> executes any subsequent result filters and the action result.</span></span> <span data-ttu-id="3e19b-787">Do krátkodobého okruhu nastavte [ResultExecutingContext. Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) na `true` a Nevolejte na `ResultExecutionDelegate` :</span><span class="sxs-lookup"><span data-stu-id="3e19b-787">To short-circuit, set [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) to `true` and don't call the `ResultExecutionDelegate`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MyAsyncResponseFilter.cs?name=snippet)]

<span data-ttu-id="3e19b-788">Rozhraní poskytuje abstrakci `ResultFilterAttribute` , která může být podtříd.</span><span class="sxs-lookup"><span data-stu-id="3e19b-788">The framework provides an abstract `ResultFilterAttribute` that can be subclassed.</span></span> <span data-ttu-id="3e19b-789">Dříve uvedená třída [AddHeaderAttribute](#add-header-attribute) je příkladem atributu filtru výsledků.</span><span class="sxs-lookup"><span data-stu-id="3e19b-789">The [AddHeaderAttribute](#add-header-attribute) class shown previously is an example of a result filter attribute.</span></span>

### <a name="ialwaysrunresultfilter-and-iasyncalwaysrunresultfilter"></a><span data-ttu-id="3e19b-790">IAlwaysRunResultFilter a IAsyncAlwaysRunResultFilter</span><span class="sxs-lookup"><span data-stu-id="3e19b-790">IAlwaysRunResultFilter and IAsyncAlwaysRunResultFilter</span></span>

<span data-ttu-id="3e19b-791"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter>Rozhraní a <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> deklaruje <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> implementaci, která je spuštěna pro všechny výsledky akce.</span><span class="sxs-lookup"><span data-stu-id="3e19b-791">The <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> interfaces declare an <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> implementation that runs for all action results.</span></span> <span data-ttu-id="3e19b-792">To zahrnuje výsledky akcí, které vytvořil:</span><span class="sxs-lookup"><span data-stu-id="3e19b-792">This includes action results produced by:</span></span>

* <span data-ttu-id="3e19b-793">Filtry autorizace a filtry prostředků, které mají krátký okruh.</span><span class="sxs-lookup"><span data-stu-id="3e19b-793">Authorization filters and resource filters that short-circuit.</span></span>
* <span data-ttu-id="3e19b-794">Filtry výjimek.</span><span class="sxs-lookup"><span data-stu-id="3e19b-794">Exception filters.</span></span>

<span data-ttu-id="3e19b-795">Například následující filtr vždy spustí a nastaví výsledek akce ( <xref:Microsoft.AspNetCore.Mvc.ObjectResult> ) s *422 nezpracovaným* stavovým kódem entity, pokud se nezdaří vyjednávání obsahu:</span><span class="sxs-lookup"><span data-stu-id="3e19b-795">For example, the following filter always runs and sets an action result (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) with a *422 Unprocessable Entity* status code when content negotiation fails:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/UnprocessableResultFilter.cs?name=snippet)]

### <a name="ifilterfactory"></a><span data-ttu-id="3e19b-796">IFilterFactory</span><span class="sxs-lookup"><span data-stu-id="3e19b-796">IFilterFactory</span></span>

<span data-ttu-id="3e19b-797"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>implementuje <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> .</span><span class="sxs-lookup"><span data-stu-id="3e19b-797"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span></span> <span data-ttu-id="3e19b-798">Proto `IFilterFactory` může být instance použita jako `IFilterMetadata` instance kdekoli v kanálu filtru.</span><span class="sxs-lookup"><span data-stu-id="3e19b-798">Therefore, an `IFilterFactory` instance can be used as an `IFilterMetadata` instance anywhere in the filter pipeline.</span></span> <span data-ttu-id="3e19b-799">Když se modul runtime připraví k vyvolání filtru, pokusí se ho přetypovat na `IFilterFactory` .</span><span class="sxs-lookup"><span data-stu-id="3e19b-799">When the runtime prepares to invoke the filter, it attempts to cast it to an `IFilterFactory`.</span></span> <span data-ttu-id="3e19b-800">Pokud je toto přetypování úspěšné, <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> je volána metoda pro vytvoření `IFilterMetadata` instance, která je vyvolána.</span><span class="sxs-lookup"><span data-stu-id="3e19b-800">If that cast succeeds, the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method is called to create the `IFilterMetadata` instance that is invoked.</span></span> <span data-ttu-id="3e19b-801">To poskytuje flexibilní návrh, protože přesný kanál filtru není nutné nastavit explicitně při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="3e19b-801">This provides a flexible design, since the precise filter pipeline doesn't need to be set explicitly when the app starts.</span></span>

<span data-ttu-id="3e19b-802">`IFilterFactory`může být implementováno pomocí implementace vlastního atributu jako jiný přístup k vytváření filtrů:</span><span class="sxs-lookup"><span data-stu-id="3e19b-802">`IFilterFactory` can be implemented using custom attribute implementations as another approach to creating filters:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/AddHeaderWithFactoryAttribute.cs?name=snippet_IFilterFactory&highlight=1,4,5,6,7)]

<span data-ttu-id="3e19b-803">Předchozí kód lze otestovat spuštěním [ukázky stahování](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample):</span><span class="sxs-lookup"><span data-stu-id="3e19b-803">The preceding code can be tested by running the [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample):</span></span>

* <span data-ttu-id="3e19b-804">Vyvolejte vývojářské nástroje F12.</span><span class="sxs-lookup"><span data-stu-id="3e19b-804">Invoke the F12 developer tools.</span></span>
* <span data-ttu-id="3e19b-805">Přejděte na adresu `https://localhost:5001/Sample/HeaderWithFactory`.</span><span class="sxs-lookup"><span data-stu-id="3e19b-805">Navigate to `https://localhost:5001/Sample/HeaderWithFactory`.</span></span>

<span data-ttu-id="3e19b-806">Vývojářské nástroje F12 zobrazují následující hlavičky odpovědí přidané ukázkovým kódem:</span><span class="sxs-lookup"><span data-stu-id="3e19b-806">The F12 developer tools display the following response headers added by the sample code:</span></span>

* <span data-ttu-id="3e19b-807">**Autor:**`Joe Smith`</span><span class="sxs-lookup"><span data-stu-id="3e19b-807">**author:** `Joe Smith`</span></span>
* <span data-ttu-id="3e19b-808">**globaladdheader:**`Result filter added to MvcOptions.Filters`</span><span class="sxs-lookup"><span data-stu-id="3e19b-808">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span></span>
* <span data-ttu-id="3e19b-809">**interní:**`My header`</span><span class="sxs-lookup"><span data-stu-id="3e19b-809">**internal:** `My header`</span></span>

<span data-ttu-id="3e19b-810">Předchozí kód vytvoří hlavičku **interního:** `My header` Response.</span><span class="sxs-lookup"><span data-stu-id="3e19b-810">The preceding code creates the **internal:** `My header` response header.</span></span>

### <a name="ifilterfactory-implemented-on-an-attribute"></a><span data-ttu-id="3e19b-811">IFilterFactory implementované u atributu</span><span class="sxs-lookup"><span data-stu-id="3e19b-811">IFilterFactory implemented on an attribute</span></span>

<!-- Review 
This section needs to be rewritten.
What's a non-named attribute?
-->

<span data-ttu-id="3e19b-812">Filtry, které implementují, `IFilterFactory` jsou užitečné pro filtry, které:</span><span class="sxs-lookup"><span data-stu-id="3e19b-812">Filters that implement `IFilterFactory` are useful for filters that:</span></span>

* <span data-ttu-id="3e19b-813">Nevyžadovat předávání parametrů</span><span class="sxs-lookup"><span data-stu-id="3e19b-813">Don't require passing parameters.</span></span>
* <span data-ttu-id="3e19b-814">Musí mít závislosti konstruktoru, které musí vyplnit DI.</span><span class="sxs-lookup"><span data-stu-id="3e19b-814">Have constructor dependencies that need to be filled by DI.</span></span>

<span data-ttu-id="3e19b-815"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>implementuje <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> .</span><span class="sxs-lookup"><span data-stu-id="3e19b-815"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="3e19b-816">`IFilterFactory`zpřístupňuje <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> metodu pro vytvoření <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span><span class="sxs-lookup"><span data-stu-id="3e19b-816">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="3e19b-817">`CreateInstance`Načte zadaný typ z kontejneru Services (DI).</span><span class="sxs-lookup"><span data-stu-id="3e19b-817">`CreateInstance` loads the specified type from the services container (DI).</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/SampleActionFilterAttribute.cs?name=snippet_TypeFilterAttribute&highlight=1,3,7)]

<span data-ttu-id="3e19b-818">Následující kód ukazuje tři přístupy k použití `[SampleActionFilter]` :</span><span class="sxs-lookup"><span data-stu-id="3e19b-818">The following code shows three approaches to applying the `[SampleActionFilter]`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet&highlight=1)]

<span data-ttu-id="3e19b-819">V předchozím kódu upravení metoda s jako `[SampleActionFilter]` upřednostňovaným přístupem k použití `SampleActionFilter` .</span><span class="sxs-lookup"><span data-stu-id="3e19b-819">In the preceding code, decorating the method with `[SampleActionFilter]` is the preferred approach to applying the `SampleActionFilter`.</span></span>

## <a name="using-middleware-in-the-filter-pipeline"></a><span data-ttu-id="3e19b-820">Použití middlewaru v kanálu filtru</span><span class="sxs-lookup"><span data-stu-id="3e19b-820">Using middleware in the filter pipeline</span></span>

<span data-ttu-id="3e19b-821">Filtry prostředků fungují jako [middleware](xref:fundamentals/middleware/index) v tom, že obklopují provádění všech položek, které jsou později v kanálu.</span><span class="sxs-lookup"><span data-stu-id="3e19b-821">Resource filters work like [middleware](xref:fundamentals/middleware/index) in that they surround the execution of everything that comes later in the pipeline.</span></span> <span data-ttu-id="3e19b-822">Filtry se ale liší od middlewaru v tom, že jsou součástí modulu ASP.NET Core Runtime, což znamená, že mají přístup k ASP.NET Core kontextu a konstrukcí.</span><span class="sxs-lookup"><span data-stu-id="3e19b-822">But filters differ from middleware in that they're part of the ASP.NET Core runtime, which means that they have access to ASP.NET Core context and constructs.</span></span>

<span data-ttu-id="3e19b-823">Chcete-li použít middleware jako filtr, vytvořte typ s `Configure` metodou, která určuje middleware pro vložení do kanálu filtru.</span><span class="sxs-lookup"><span data-stu-id="3e19b-823">To use middleware as a filter, create a type with a `Configure` method that specifies the middleware to inject into the filter pipeline.</span></span> <span data-ttu-id="3e19b-824">Následující příklad používá middleware Localization k vytvoření aktuální jazykové verze pro požadavek:</span><span class="sxs-lookup"><span data-stu-id="3e19b-824">The following example uses the localization middleware to establish the current culture for a request:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LocalizationPipeline.cs?name=snippet_MiddlewareFilter&highlight=3,22)]

<span data-ttu-id="3e19b-825">Pomocí <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> Spusťte middleware:</span><span class="sxs-lookup"><span data-stu-id="3e19b-825">Use the <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> to run the middleware:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_MiddlewareFilter&highlight=2)]

<span data-ttu-id="3e19b-826">Filtry middlewaru jsou spouštěny ve stejné fázi kanálu filtru jako filtry prostředků, před vazbami modelů a po zbytek kanálu.</span><span class="sxs-lookup"><span data-stu-id="3e19b-826">Middleware filters run at the same stage of the filter pipeline as Resource filters, before model binding and after the rest of the pipeline.</span></span>

## <a name="next-actions"></a><span data-ttu-id="3e19b-827">Další akce</span><span class="sxs-lookup"><span data-stu-id="3e19b-827">Next actions</span></span>

* <span data-ttu-id="3e19b-828">Viz [metody filtru pro Razor stránky](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="3e19b-828">See [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>
* <span data-ttu-id="3e19b-829">Pro experimentování s filtry, [stažení, otestování a úpravy ukázky GitHubu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample).</span><span class="sxs-lookup"><span data-stu-id="3e19b-829">To experiment with filters, [download, test, and modify the GitHub sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample).</span></span>

::: moniker-end
